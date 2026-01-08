# Notification Service - Changelog

---

# 2024-03-01, Version 2.8.0 (Feature Release), @notifications-team

---

## Notable changes:

* **channels**: Added WhatsApp Business API integration for international messaging. Supports 180+ countries with rich media messages. (Integration Team)
* **personalization**: AI-powered content personalization using customer behavior data. Increases email engagement by 35%. (ML Team)
* **scheduling**: Advanced scheduling with timezone detection and optimal send time prediction. Improves open rates by 20%. (Backend Team)
* **templates**: Visual template editor with drag-and-drop components. Reduces template creation time by 60%. (Frontend Team)
* **analytics**: Real-time notification analytics dashboard with A/B testing support. Enables data-driven optimization. (Analytics Team)
* **webhooks**: Delivery status webhooks for real-time notification tracking. Enables downstream integrations. (Platform Team)
* **compliance**: GDPR and CAN-SPAM compliance tools with automatic unsubscribe handling. Ensures regulatory compliance. (Compliance Team)

## Breaking changes:

* **api**: Email send API now requires explicit consent tracking. Must include `consentTimestamp` field.
* **templates**: Template variable syntax changed from `{var}` to `{{var}}` for consistency with industry standards.

## Commits:

* [a1b2c3d4e5] - channels: integrate whatsapp business (Integration Team) https://github.com/ecommerce/notification-service/pull/512
* [b2c3d4e5f6] - personalization: add ai content (ML Team) https://github.com/ecommerce/notification-service/pull/515
* [c3d4e5f6a7] - scheduling: add timezone detection (Backend Team) https://github.com/ecommerce/notification-service/pull/518
* [d4e5f6a7b8] - templates: build visual editor (Frontend Team) https://github.com/ecommerce/notification-service/pull/521
* [e5f6a7b8c9] - analytics: add realtime dashboard (Analytics Team) https://github.com/ecommerce/notification-service/pull/524
* [f6a7b8c9d1] - webhooks: add delivery webhooks (Platform Team) https://github.com/ecommerce/notification-service/pull/527
* [a7b8c9d1e2] - compliance: add gdpr tools (Compliance Team) https://github.com/ecommerce/notification-service/pull/530

---

# 2024-02-12, Version 2.7.5 (Security Patch), @security-team

---

This is a security release addressing critical vulnerabilities.

## Notable changes:

* **security**: CVE-2024-4567 - Fixed email template injection vulnerability. Attackers could inject malicious content through template variables. Implemented strict input sanitization. (Security Team)
* **security**: CVE-2024-4568 - Fixed SMS spoofing vulnerability allowing sender ID manipulation. Enhanced sender validation. (Security Team)
* **security**: Fixed unsubscribe token prediction vulnerability. Tokens now cryptographically secure. (Security Team)
* **privacy**: Enhanced PII redaction in logs. Sensitive data now properly masked. (Security Team)

## Commits:

* [b8c9d1e2f3] - security: fix template injection (Security Team) https://github.com/ecommerce/notification-service/pull/535
* [c9d1e2f3a4] - security: fix sms spoofing (Security Team) https://github.com/ecommerce/notification-service/pull/536
* [d1e2f3a4b5] - security: fix token prediction (Security Team) https://github.com/ecommerce/notification-service/pull/537
* [e2f3a4b5c6] - privacy: enhance pii redaction (Security Team) https://github.com/ecommerce/notification-service/pull/538

---

# 2024-01-28, Version 2.7.4 (Bug Fix), @notifications-team

---

## Notable changes:

* **email**: Fixed email delivery delays during high traffic. Queue processing now properly scaled. (Backend Team)
* **sms**: Fixed SMS character encoding issues for international messages. Unicode now properly handled. (Integration Team)
* **push**: Fixed push notification delivery to iOS devices after token refresh. Token management improved. (Mobile Team)
* **templates**: Fixed template rendering errors with null variables. Proper null handling implemented. (Backend Team)
* **analytics**: Fixed open rate calculation including bot opens. Bot detection now accurate. (Analytics Team)

## Commits:

* [f3a4b5c6d7] - email: fix delivery delays (Backend Team) https://github.com/ecommerce/notification-service/pull/541
* [a4b5c6d7e8] - sms: fix unicode encoding (Integration Team) https://github.com/ecommerce/notification-service/pull/542
* [b5c6d7e8f9] - push: fix ios token refresh (Mobile Team) https://github.com/ecommerce/notification-service/pull/543
* [c6d7e8f9a1] - templates: fix null handling (Backend Team) https://github.com/ecommerce/notification-service/pull/544
* [d7e8f9a1b2] - analytics: fix bot detection (Analytics Team) https://github.com/ecommerce/notification-service/pull/545

---

# 2024-01-15, Version 2.7.3 (Performance), @performance-team

---

## Notable changes:

* **performance**: Optimized email sending reducing latency from 2s to 500ms. Parallelized template rendering and SMTP connections. (Performance Team)
* **performance**: Implemented Redis caching for templates. Template loading 10x faster. (Backend Team)
* **database**: Optimized notification history queries with proper indexing. Query performance improved by 6x. (Database Team)
* **performance**: Batch processing for bulk email sends. Throughput increased from 100 to 1000 emails/second. (Backend Team)
* **monitoring**: Enhanced performance metrics and distributed tracing. Full visibility into notification pipeline. (DevOps Team)

## Commits:

* [e8f9a1b2c3] - perf: optimize email sending (Performance Team) https://github.com/ecommerce/notification-service/pull/551
* [f9a1b2c3d4] - cache: add template caching (Backend Team) https://github.com/ecommerce/notification-service/pull/552
* [a1b2c3d4e5] - db: optimize queries (Database Team) https://github.com/ecommerce/notification-service/pull/553
* [b2c3d4e5f6] - perf: add batch processing (Backend Team) https://github.com/ecommerce/notification-service/pull/554
* [c3d4e5f6a7] - monitoring: enhance metrics (DevOps Team) https://github.com/ecommerce/notification-service/pull/555

---

# 2023-12-20, Version 2.7.2 (Feature), @notifications-team

---

## Notable changes:

* **retry**: Intelligent retry logic with exponential backoff for failed deliveries. Improves delivery success rate by 15%. (Backend Team)
* **localization**: Multi-language template support with automatic language detection. Supports 25 languages. (i18n Team)
* **attachments**: Email attachment support with virus scanning. Enables invoice and receipt delivery. (Backend Team)
* **tracking**: Enhanced click tracking with UTM parameter support. Better attribution for marketing campaigns. (Analytics Team)

## Commits:

* [d4e5f6a7b8] - retry: add intelligent retry (Backend Team) https://github.com/ecommerce/notification-service/pull/561
* [e5f6a7b8c9] - i18n: add multi-language (i18n Team) https://github.com/ecommerce/notification-service/pull/562
* [f6a7b8c9d1] - attachments: add email attachments (Backend Team) https://github.com/ecommerce/notification-service/pull/563
* [a7b8c9d1e2] - tracking: enhance click tracking (Analytics Team) https://github.com/ecommerce/notification-service/pull/564

---

# 2023-12-05, Version 2.7.1 (Bug Fix), @notifications-team

---

## Notable changes:

* **email**: Fixed email bounces not being properly tracked. Bounce handling now working correctly. (Backend Team)
* **sms**: Fixed SMS delivery reports not updating status. Status sync now reliable. (Integration Team)
* **push**: Fixed push notification badge count not updating. Badge management improved. (Mobile Team)
* **preferences**: Fixed preference updates not taking effect immediately. Cache invalidation fixed. (Backend Team)

## Commits:

* [b8c9d1e2f3] - email: fix bounce tracking (Backend Team) https://github.com/ecommerce/notification-service/pull/571
* [c9d1e2f3a4] - sms: fix delivery reports (Integration Team) https://github.com/ecommerce/notification-service/pull/572
* [d1e2f3a4b5] - push: fix badge count (Mobile Team) https://github.com/ecommerce/notification-service/pull/573
* [e2f3a4b5c6] - preferences: fix cache invalidation (Backend Team) https://github.com/ecommerce/notification-service/pull/574

---

# 2023-11-20, Version 2.7.0 (Major Release), @notifications-team

---

This release includes significant architectural improvements.

## Notable changes:

* **architecture**: Migrated to event-driven architecture using Kafka. Improves scalability and reliability. (Architecture Team)
* **database**: Migrated from MySQL to PostgreSQL for better JSON support. Data migration completed successfully. (Database Team)
* **api**: GraphQL API added alongside REST for flexible data fetching. Reduces over-fetching. (API Team)
* **resilience**: Implemented circuit breakers for external service calls. Prevents cascade failures. (DevOps Team)
* **observability**: Integrated OpenTelemetry for distributed tracing. Full visibility across notification pipeline. (DevOps Team)

## Breaking changes:

* **events**: Notification events now published to Kafka instead of RabbitMQ. Event consumers must migrate.
* **api**: Some REST endpoints restructured for consistency. See migration guide.

## Commits:

* [f3a4b5c6d7] - arch: migrate to event-driven (Architecture Team) https://github.com/ecommerce/notification-service/pull/581
* [a4b5c6d7e8] - db: migrate to postgresql (Database Team) https://github.com/ecommerce/notification-service/pull/582
* [b5c6d7e8f9] - api: add graphql endpoint (API Team) https://github.com/ecommerce/notification-service/pull/583
* [c6d7e8f9a1] - resilience: add circuit breakers (DevOps Team) https://github.com/ecommerce/notification-service/pull/584
* [d7e8f9a1b2] - observability: integrate opentelemetry (DevOps Team) https://github.com/ecommerce/notification-service/pull/585

---

# 2023-10-30, Version 2.6.8 (Security Patch), @security-team

---

This is a security release addressing vulnerabilities.

## Notable changes:

* **security**: CVE-2023-8901 - Fixed XSS vulnerability in email preview. Proper output encoding implemented. (Security Team)
* **security**: CVE-2023-8902 - Fixed SSRF vulnerability in image proxy. URL validation enhanced. (Security Team)
* **dependencies**: Updated all dependencies to patch known vulnerabilities. 10 high-severity CVEs addressed. (DevOps Team)

## Commits:

* [e8f9a1b2c3] - security: fix xss in preview (Security Team) https://github.com/ecommerce/notification-service/pull/591
* [f9a1b2c3d4] - security: fix ssrf in proxy (Security Team) https://github.com/ecommerce/notification-service/pull/592
* [a1b2c3d4e5] - deps: update dependencies (DevOps Team) https://github.com/ecommerce/notification-service/pull/593

---

# 2023-10-10, Version 2.6.7 (Feature), @notifications-team

---

## Notable changes:

* **digest**: Email digest functionality for batching notifications. Reduces email fatigue by 40%. (Product Team)
* **quiet-hours**: Quiet hours support respecting user timezone. Improves user experience. (Backend Team)
* **preview**: Email preview with device simulation (desktop, mobile, tablet). Ensures responsive design. (Frontend Team)
* **spam-check**: Automated spam score checking before sending. Improves deliverability. (Backend Team)

## Commits:

* [b2c3d4e5f6] - digest: add email batching (Product Team) https://github.com/ecommerce/notification-service/pull/601
* [c3d4e5f6a7] - quiet: add quiet hours (Backend Team) https://github.com/ecommerce/notification-service/pull/602
* [d4e5f6a7b8] - preview: add device simulation (Frontend Team) https://github.com/ecommerce/notification-service/pull/603
* [e5f6a7b8c9] - spam: add score checking (Backend Team) https://github.com/ecommerce/notification-service/pull/604

---

# 2023-09-20, Version 2.6.6 (Bug Fix), @notifications-team

---

## Notable changes:

* **email**: Fixed email images not loading due to CSP issues. Content Security Policy updated. (Backend Team)
* **sms**: Fixed SMS cost calculation for multi-segment messages. Billing now accurate. (Integration Team)
* **push**: Fixed push notification sound not playing on Android. Sound configuration fixed. (Mobile Team)
* **templates**: Fixed template variable escaping breaking HTML. Proper escaping logic implemented. (Backend Team)

## Commits:

* [f6a7b8c9d1] - email: fix csp for images (Backend Team) https://github.com/ecommerce/notification-service/pull/611
* [a7b8c9d1e2] - sms: fix cost calculation (Integration Team) https://github.com/ecommerce/notification-service/pull/612
* [b8c9d1e2f3] - push: fix android sound (Mobile Team) https://github.com/ecommerce/notification-service/pull/613
* [c9d1e2f3a4] - templates: fix variable escaping (Backend Team) https://github.com/ecommerce/notification-service/pull/614

---

# 2023-08-30, Version 2.6.5 (Performance), @performance-team

---

## Notable changes:

* **performance**: Optimized bulk email processing reducing time by 70%. Parallel processing implemented. (Performance Team)
* **database**: Implemented read replicas for analytics queries. Read performance improved by 5x. (Database Team)
* **caching**: Added caching for user preferences. Preference lookup 20x faster. (Backend Team)
* **performance**: Optimized image processing for email attachments. Processing time reduced by 60%. (Backend Team)

## Commits:

* [d1e2f3a4b5] - perf: optimize bulk processing (Performance Team) https://github.com/ecommerce/notification-service/pull/621
* [e2f3a4b5c6] - db: add read replicas (Database Team) https://github.com/ecommerce/notification-service/pull/622
* [f3a4b5c6d7] - cache: add preference caching (Backend Team) https://github.com/ecommerce/notification-service/pull/623
* [a4b5c6d7e8] - perf: optimize image processing (Backend Team) https://github.com/ecommerce/notification-service/pull/624

---

# 2023-08-10, Version 2.6.4 (Feature Release), @notifications-team

---

## Notable changes:

* **segments**: Customer segmentation for targeted notifications. Enables personalized campaigns. (Marketing Team)
* **ab-testing**: Built-in A/B testing for email subject lines and content. Improves engagement rates. (Analytics Team)
* **unsubscribe**: One-click unsubscribe support per RFC 8058. Improves user experience and compliance. (Backend Team)
* **delivery-windows**: Delivery window support for time-sensitive notifications. Ensures timely delivery. (Backend Team)
* **fallback**: SMS fallback for failed email delivery. Improves notification reliability. (Integration Team)

## Commits:

* [b5c6d7e8f9] - segments: add customer segmentation (Marketing Team) https://github.com/ecommerce/notification-service/pull/631
* [c6d7e8f9a1] - ab-testing: add testing framework (Analytics Team) https://github.com/ecommerce/notification-service/pull/632
* [d7e8f9a1b2] - unsubscribe: add one-click (Backend Team) https://github.com/ecommerce/notification-service/pull/633
* [e8f9a1b2c3] - delivery: add time windows (Backend Team) https://github.com/ecommerce/notification-service/pull/634
* [f9a1b2c3d4] - fallback: add sms fallback (Integration Team) https://github.com/ecommerce/notification-service/pull/635

---
