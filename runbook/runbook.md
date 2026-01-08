# 📌 Notification Service - Operations Runbook

## Service Overview

* **Service Name:** notification-service
* **Owner Team:** Notifications Platform Team
* **Criticality:** High
* **Environments:** prod, stage, dev
* **Primary On-call:** notifications-oncall@ecommerce.com
* **Slack Channel:** #notifications-alerts
* **Repository:** https://github.com/ecommerce/notification-service
* **Monitoring Dashboard:** https://grafana.ecommerce.com/d/notification-service

---

## 1. Email Delivery Failures

**Error Code(s):**
`EMAIL_DELIVERY_FAILED`, `SMTP_ERROR`, `BOUNCE_RATE_HIGH`

**Severity:** High

---

### Symptoms

* High email bounce rate (>5%)
* Emails stuck in queue
* SMTP connection errors in logs
* Customer complaints about missing emails
* Email delivery rate <95%

---

### Likely Causes

* Email provider (SendGrid/SES) API issues
* SMTP server unavailable
* Domain reputation issues (blacklisted)
* Invalid recipient email addresses
* Email content triggering spam filters

---

### How to Confirm

1. Check email delivery metrics
2. Verify email provider status
3. Test SMTP connectivity
4. Check domain reputation/blacklist status
5. Review bounce reasons

```bash
# Check email delivery rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/metrics | grep email_delivery_rate

# View failed email deliveries
kubectl logs -l app=notification-service | grep EMAIL_DELIVERY_FAILED

# Test SMTP connection
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/test-smtp

# Check bounce rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/bounce-rate

# Check domain reputation
# Use external tools: mxtoolbox.com, senderscore.org
```

---

### Recommended Fix (Safe Actions)

1. **Check provider status**: Verify email provider operational
2. **Test connectivity**: Ensure SMTP connection working
3. **Review bounces**: Analyze bounce reasons
4. **Check reputation**: Verify domain not blacklisted
5. **Retry failed**: Trigger retry for soft bounces

```bash
# Retry failed emails (soft bounces only)
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/emails/retry-failed \
  -d '{"bounceType": "soft", "since": "24h"}'

# Test email delivery
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/test-email \
  -d '{"to": "test@example.com"}'

# Check email queue
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/queue-status

# Restart service if SMTP connection stuck
kubectl rollout restart deployment/notification-service
```

---

### Rollback / Recovery

* Failed emails stored in dead letter queue for 7 days
* Soft bounces retried automatically up to 3 times
* Hard bounces require email address correction
* Monitor delivery rate after fixes

---

### Notes

* Normal bounce rate is 2-3%
* Bounce rate >5% indicates systemic issue
* Hard bounces (invalid email) should not be retried
* Domain reputation takes time to recover if blacklisted

---

## 2. SMS Delivery Failures

**Error Code(s):**
`SMS_DELIVERY_FAILED`, `INVALID_PHONE_NUMBER`, `CARRIER_REJECTED`

**Severity:** High

---

### Symptoms

* High SMS failure rate (>5%)
* SMS stuck in pending status
* Carrier rejection errors
* Customer complaints about missing SMS
* Increased SMS costs without delivery

---

### Likely Causes

* SMS provider (Twilio) API issues
* Invalid phone number format
* Carrier blocking messages
* SMS content triggering spam filters
* Insufficient SMS credits/quota

---

### How to Confirm

1. Check SMS delivery metrics
2. Verify SMS provider status
3. Validate phone number format
4. Review carrier rejection reasons
5. Check SMS credit balance

```bash
# Check SMS delivery rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/metrics | grep sms_delivery_rate

# View failed SMS deliveries
kubectl logs -l app=notification-service | grep SMS_DELIVERY_FAILED

# Test SMS provider connectivity
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/test-sms

# Check SMS credits
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/sms/credits

# View carrier rejection reasons
kubectl exec -it deployment/notification-service -- \
  curl "localhost:8080/admin/sms/failures?reason=carrier_rejected"
```

---

### Recommended Fix (Safe Actions)

1. **Check provider status**: Verify SMS provider operational
2. **Validate numbers**: Ensure phone numbers in E.164 format
3. **Review content**: Check for spam trigger words
4. **Check credits**: Verify sufficient SMS credits
5. **Retry failed**: Trigger retry for failed SMS

```bash
# Retry failed SMS
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/sms/retry-failed \
  -d '{"since": "24h"}'

# Test SMS delivery
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/test-sms \
  -d '{"to": "+14155551234", "message": "Test message"}'

# Check SMS queue
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/sms/queue-status
```

---

### Rollback / Recovery

* Failed SMS stored in dead letter queue for 7 days
* SMS can be retried after fixing issues
* Monitor delivery rate and costs after fixes
* Contact SMS provider support if widespread issue

---

### Notes

* Normal SMS failure rate is 1-2%
* Phone numbers must be in E.164 format (+country code)
* Carrier filtering varies by country
* SMS costs vary by destination country

---

## 3. Push Notification Delivery Failures

**Error Code(s):**
`PUSH_DELIVERY_FAILED`, `INVALID_TOKEN`, `DEVICE_UNREGISTERED`

**Severity:** Medium

---

### Symptoms

* High push notification failure rate
* Invalid device token errors
* Push notifications not appearing on devices
* Device unregistered errors
* Customer complaints about missing notifications

---

### Likely Causes

* FCM/APNS service issues
* Expired or invalid device tokens
* App uninstalled by user
* Device token not refreshed
* Push notification permissions revoked

---

### How to Confirm

1. Check push delivery metrics
2. Verify FCM/APNS status
3. Review device token validity
4. Check failure reasons
5. Test push notification delivery

```bash
# Check push delivery rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/metrics | grep push_delivery_rate

# View failed push notifications
kubectl logs -l app=notification-service | grep PUSH_DELIVERY_FAILED

# Test FCM/APNS connectivity
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/test-push

# Check invalid token count
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/push/invalid-tokens

# View failure reasons
kubectl exec -it deployment/notification-service -- \
  curl "localhost:8080/admin/push/failures?groupBy=reason"
```

---

### Recommended Fix (Safe Actions)

1. **Check provider status**: Verify FCM/APNS operational
2. **Clean tokens**: Remove invalid device tokens
3. **Refresh tokens**: Trigger token refresh for users
4. **Test delivery**: Send test push notification
5. **Monitor**: Watch delivery rate after cleanup

```bash
# Remove invalid device tokens
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/push/cleanup-tokens

# Test push delivery
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/test-push \
  -d '{"userId": "user_abc123", "title": "Test", "body": "Test message"}'

# Check push queue
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/push/queue-status
```

---

### Rollback / Recovery

* Invalid tokens automatically removed after 3 failures
* Users must re-register device tokens after app reinstall
* Monitor token refresh rate
* No manual recovery needed for invalid tokens

---

### Notes

* Normal push failure rate is 5-10% (due to app uninstalls)
* Device tokens expire and must be refreshed periodically
* iOS tokens more stable than Android tokens
* Push permissions can be revoked by user

---

## 4. Template Rendering Errors

**Error Code(s):**
`TEMPLATE_RENDER_ERROR`, `INVALID_TEMPLATE`, `MISSING_VARIABLE`

**Severity:** Medium

---

### Symptoms

* Notification sending failures
* Template syntax errors in logs
* Missing content in sent notifications
* Variable substitution failures
* Customer complaints about broken emails

---

### Likely Causes

* Invalid template syntax
* Missing required template variables
* Template variable type mismatch
* Corrupted template in database
* Template caching issues

---

### How to Confirm

1. Check template rendering errors
2. Validate template syntax
3. Review missing variables
4. Test template rendering
5. Check template cache

```bash
# View template rendering errors
kubectl logs -l app=notification-service | grep TEMPLATE_RENDER_ERROR

# Test template rendering
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/templates/test-render \
  -d '{"templateId": "order_confirmation", "variables": {"orderNumber": "123"}}'

# Validate template syntax
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/templates/validate \
  -d '{"templateId": "order_confirmation"}'

# Check template cache
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/templates/cache-status
```

---

### Recommended Fix (Safe Actions)

1. **Validate template**: Check template syntax
2. **Test rendering**: Render with sample data
3. **Fix syntax**: Correct template errors
4. **Clear cache**: Invalidate template cache
5. **Monitor**: Watch rendering success rate

```bash
# Clear template cache
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/templates/clear-cache

# Fix template (via admin UI or API)
kubectl exec -it deployment/notification-service -- \
  curl -X PATCH localhost:8080/admin/templates/order_confirmation \
  -d '{"htmlBody": "<h1>Fixed template</h1>"}'

# Restart to reload templates
kubectl rollout restart deployment/notification-service
```

---

### Rollback / Recovery

* Revert template to previous version
* Template changes take effect immediately
* Test templates in staging before production
* Monitor rendering errors after changes

---

### Notes

* Template syntax uses Handlebars/Mustache
* Required variables must be provided
* Template cache TTL is 5 minutes
* Always test templates before deploying

---

## 5. High Email Bounce Rate

**Error Code(s):**
`HIGH_BOUNCE_RATE`, `SPAM_COMPLAINT`, `DOMAIN_BLACKLISTED`

**Severity:** High

---

### Symptoms

* Bounce rate >5% (normal is 2-3%)
* Spam complaints increasing
* Domain appearing on blacklists
* Email deliverability declining
* Email provider warnings

---

### Likely Causes

* Sending to invalid/old email lists
* Email content triggering spam filters
* Domain reputation degraded
* Insufficient email authentication (SPF/DKIM)
* High spam complaint rate

---

### How to Confirm

1. Check bounce rate metrics
2. Review bounce reasons
3. Check domain blacklist status
4. Verify SPF/DKIM configuration
5. Analyze spam complaints

```bash
# Check bounce rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/bounce-rate

# View bounce reasons
kubectl exec -it deployment/notification-service -- \
  curl "localhost:8080/admin/emails/bounces?groupBy=reason"

# Check spam complaints
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/spam-complaints

# Verify SPF/DKIM
dig TXT ecommerce.com
dig TXT _dmarc.ecommerce.com
```

---

### Recommended Fix (Safe Actions)

1. **Clean list**: Remove invalid emails
2. **Check authentication**: Verify SPF/DKIM/DMARC
3. **Review content**: Check for spam triggers
4. **Request delisting**: Submit delisting requests
5. **Monitor**: Watch bounce rate closely

```bash
# Remove hard bounces from list
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/emails/cleanup-bounces \
  -d '{"bounceType": "hard"}'

# Check email authentication
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/auth-status

# Test spam score
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/emails/spam-check \
  -d '{"templateId": "promotional_sale"}'
```

---

### Rollback / Recovery

* Domain reputation recovery takes weeks
* Stop sending to old/invalid lists immediately
* Implement double opt-in for new subscribers
* Monitor bounce rate daily

---

### Notes

* Bounce rate >5% triggers email provider warnings
* Hard bounces should be removed immediately
* Soft bounces can be retried
* Domain blacklisting severely impacts deliverability

---

## 6. Database Connection Pool Exhaustion

**Error Code(s):**
`DB_POOL_EXHAUSTED`, `CONNECTION_TIMEOUT`

**Severity:** Critical

---

### Symptoms

* All notification operations failing
* Database connection errors in logs
* API requests timing out
* Connection pool at 100% utilization
* Service health check failing

---

### Likely Causes

* Connection leak in application code
* Long-running database queries
* Sudden traffic spike
* Database failover or maintenance
* Connection pool size too small

---

### How to Confirm

1. Check connection pool metrics
2. Review active database connections
3. Identify long-running queries
4. Check traffic patterns
5. Verify database health

```bash
# Check connection pool usage
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/metrics | grep db_pool

# View active connections
kubectl exec -it postgres-0 -- psql -U notifications -c \
  "SELECT count(*) FROM pg_stat_activity WHERE state = 'active';"

# Find long-running queries
kubectl exec -it postgres-0 -- psql -U notifications -c \
  "SELECT pid, now() - query_start as duration, query FROM pg_stat_activity WHERE state = 'active' ORDER BY duration DESC LIMIT 10;"
```

---

### Recommended Fix (Safe Actions)

1. **Restart service**: Release stale connections
2. **Kill long queries**: Terminate blocking queries
3. **Increase pool**: Temporarily increase connection limit
4. **Scale service**: Add more replicas
5. **Monitor**: Watch connection usage

```bash
# Restart notification service
kubectl rollout restart deployment/notification-service

# Kill long-running query
kubectl exec -it postgres-0 -- psql -U notifications -c \
  "SELECT pg_terminate_backend(12345);"

# Increase connection pool
kubectl set env deployment/notification-service DB_POOL_SIZE=30

# Scale up replicas
kubectl scale deployment/notification-service --replicas=6
```

---

### Rollback / Recovery

* Revert pool size after root cause fixed
* Scale down replicas after traffic normalizes
* Fix connection leaks in code
* Monitor connection usage trends

---

### Notes

* Default pool size is 20 connections per pod
* Connection leaks must be fixed in code
* Database failover can cause temporary connection issues
* Monitor connection pool usage proactively

---

## 7. Kafka Event Consumption Lag

**Error Code(s):**
`KAFKA_LAG_HIGH`, `CONSUMER_STUCK`, `EVENT_PROCESSING_DELAYED`

**Severity:** High

---

### Symptoms

* Notification delays (>5 minutes)
* Kafka consumer lag increasing
* Events not being processed
* Customer complaints about delayed notifications
* Consumer group stuck

---

### Likely Causes

* Kafka broker issues
* Consumer processing too slow
* Consumer group rebalancing
* Event processing errors
* Insufficient consumer instances

---

### How to Confirm

1. Check Kafka consumer lag
2. Review consumer group status
3. Check event processing rate
4. Verify Kafka broker health
5. Review consumer errors

```bash
# Check consumer lag
kafka-consumer-groups --bootstrap-server kafka:9092 \
  --group notification-consumer --describe

# View consumer errors
kubectl logs -l app=notification-service | grep "kafka:consumer:error"

# Check event processing rate
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/metrics | grep event_processing_rate

# Check Kafka broker health
kubectl get pods -l app=kafka
```

---

### Recommended Fix (Safe Actions)

1. **Scale consumers**: Add more consumer instances
2. **Restart consumers**: Reset stuck consumer group
3. **Increase throughput**: Optimize event processing
4. **Skip errors**: Move failed events to DLQ
5. **Monitor**: Watch consumer lag

```bash
# Scale up consumers
kubectl scale deployment/notification-service --replicas=8

# Restart to reset consumer group
kubectl rollout restart deployment/notification-service

# Check consumer group
kafka-consumer-groups --bootstrap-server kafka:9092 \
  --group notification-consumer --describe

# Move failed events to DLQ
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/events/move-to-dlq \
  -d '{"maxRetries": 3}'
```

---

### Rollback / Recovery

* Consumer lag will catch up after scaling
* Failed events in DLQ can be replayed
* Monitor lag metrics after changes
* Scale down after lag resolved

---

### Notes

* Normal consumer lag is <100 messages
* Lag >1000 indicates processing issues
* Consumer rebalancing causes temporary lag
* Event processing should be idempotent

---

## 8. High Memory Usage / OOM Kills

**Error Code(s):**
`OUT_OF_MEMORY`, `CONTAINER_OOM_KILLED`

**Severity:** High

---

### Symptoms

* Pods being killed and restarted
* OOMKilled status in pod events
* Memory usage at 95%+ of limit
* Service unavailability during restarts
* Notification processing failures

---

### Likely Causes

* Memory leak in application code
* Large bulk email job
* Template caching using too much memory
* Insufficient memory limits
* Image processing consuming memory

---

### How to Confirm

1. Check pod events for OOMKilled
2. Review memory usage metrics
3. Identify memory-intensive operations
4. Check heap dump if available
5. Review recent code changes

```bash
# Check pod events
kubectl describe pod <pod-name> | grep -A 10 Events

# Check memory usage
kubectl top pods -l app=notification-service

# Check restart count
kubectl get pods -l app=notification-service -o jsonpath='{.items[*].status.containerStatuses[*].restartCount}'

# Get heap dump (if JVM)
kubectl exec -it <pod-name> -- jmap -dump:live,format=b,file=/tmp/heap.hprof 1
```

---

### Recommended Fix (Safe Actions)

1. **Increase memory**: Temporarily increase memory limits
2. **Scale up**: Add more replicas
3. **Investigate**: Review heap dump
4. **Optimize**: Fix memory-intensive operations
5. **Monitor**: Watch memory usage

```bash
# Increase memory limit
kubectl set resources deployment/notification-service --limits=memory=4Gi

# Scale up replicas
kubectl scale deployment/notification-service --replicas=6

# Restart pods
kubectl rollout restart deployment/notification-service

# Monitor memory
watch -n 5 'kubectl top pods -l app=notification-service'
```

---

### Rollback / Recovery

* Revert memory increase after fix
* Scale down replicas after load normalizes
* Deploy memory leak fix
* Monitor memory trends

---

### Notes

* Memory increase is temporary solution
* Common causes: template caching, bulk operations
* Use streaming for large email batches
* Implement memory limits on bulk jobs

---

## 9. Unsubscribe Link Failures

**Error Code(s):**
`UNSUBSCRIBE_FAILED`, `INVALID_TOKEN`, `TOKEN_EXPIRED`

**Severity:** Medium

---

### Symptoms

* Customer complaints about unsubscribe not working
* Invalid unsubscribe token errors
* Unsubscribe page not loading
* Customers still receiving emails after unsubscribe
* Compliance violations

---

### Likely Causes

* Unsubscribe token expired
* Token validation failing
* Preference update not persisting
* Cache not invalidated after unsubscribe
* Database update failures

---

### How to Confirm

1. Test unsubscribe link
2. Check token validation
3. Verify preference updates
4. Review unsubscribe errors
5. Check cache invalidation

```bash
# Test unsubscribe link
curl "https://ecommerce.com/unsubscribe?token=abc123xyz"

# View unsubscribe errors
kubectl logs -l app=notification-service | grep UNSUBSCRIBE_FAILED

# Check preference update
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/users/user_abc123/preferences

# Verify cache invalidation
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/cache/preferences/user_abc123
```

---

### Recommended Fix (Safe Actions)

1. **Extend token TTL**: Increase token expiration
2. **Manual unsubscribe**: Unsubscribe user manually
3. **Clear cache**: Invalidate preference cache
4. **Test flow**: Verify unsubscribe working
5. **Monitor**: Watch unsubscribe success rate

```bash
# Manually unsubscribe user
kubectl exec -it deployment/notification-service -- \
  curl -X PATCH localhost:8080/admin/users/user_abc123/preferences \
  -d '{"email": {"promotions": false, "newsletters": false}}'

# Clear preference cache
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/cache/clear-preferences

# Test unsubscribe flow
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/test-unsubscribe \
  -d '{"userId": "user_abc123"}'
```

---

### Rollback / Recovery

* Preference updates are immediate
* Cache invalidation ensures consistency
* Monitor unsubscribe rate
* Compliance requires working unsubscribe

---

### Notes

* Unsubscribe token TTL is 30 days
* One-click unsubscribe required for compliance
* Preference cache TTL is 5 minutes
* Always honor unsubscribe requests immediately

---

## 10. Email Spam Filter Issues

**Error Code(s):**
`SPAM_FILTER_TRIGGERED`, `LOW_SENDER_SCORE`, `CONTENT_BLOCKED`

**Severity:** Medium

---

### Symptoms

* Emails landing in spam folder
* Low email open rates
* Sender score declining
* Content filter warnings
* Customer complaints about missing emails

---

### Likely Causes

* Email content triggering spam filters
* Poor sender reputation
* Missing email authentication
* High spam complaint rate
* Suspicious links in emails

---

### How to Confirm

1. Check sender score
2. Test email spam score
3. Verify email authentication
4. Review spam complaints
5. Analyze email content

```bash
# Check sender score (use external tools)
# senderscore.org, mxtoolbox.com

# Test spam score
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/emails/spam-check \
  -d '{"templateId": "promotional_sale"}'

# Check spam complaints
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/spam-complaints

# Verify email authentication
dig TXT ecommerce.com
dig TXT default._domainkey.ecommerce.com
```

---

### Recommended Fix (Safe Actions)

1. **Review content**: Remove spam trigger words
2. **Improve authentication**: Ensure SPF/DKIM/DMARC configured
3. **Warm up domain**: Gradually increase send volume
4. **Monitor complaints**: Track spam complaint rate
5. **Test emails**: Use spam testing tools

```bash
# Test email content
kubectl exec -it deployment/notification-service -- \
  curl -X POST localhost:8080/admin/emails/content-analysis \
  -d '{"templateId": "promotional_sale"}'

# Check authentication status
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/auth-check

# Review spam trigger words
kubectl exec -it deployment/notification-service -- \
  curl localhost:8080/admin/emails/spam-triggers
```

---

### Rollback / Recovery

* Content changes take effect immediately
* Sender reputation improves gradually
* Monitor spam complaint rate
* Use email testing tools before sending

---

### Notes

* Sender score should be >80
* Spam complaint rate should be <0.1%
* Email authentication is mandatory
* Avoid spam trigger words: free, urgent, click here

---

## Escalation Path

* **Primary Escalation:** Notifications Platform Team (#notifications-team)
* **Secondary Escalation:** Platform Engineering (#platform-eng)
* **Email Issues:** Email Provider Support (SendGrid, SES)
* **SMS Issues:** SMS Provider Support (Twilio)
* **When to Escalate:** Issue persists >30 minutes or affects >25% of notifications

---

## Emergency Contacts

* **On-call Engineer:** notifications-oncall@ecommerce.com (PagerDuty)
* **Team Lead:** notifications-lead@ecommerce.com
* **Engineering Manager:** notifications-manager@ecommerce.com

---

## Last Updated: 2024-03-01

---
