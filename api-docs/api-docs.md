# 📧 Notification Service API Documentation

## Overview

The **Notification Service** handles all customer communications including emails, SMS, and push notifications for the e-commerce platform.

* **Base URL:** `https://api.ecommerce.example.com/notifications/v1`
* **Protocol:** HTTPS
* **Data Format:** JSON
* **Authentication:** JWT Bearer Token + API Key
* **Rate Limit:** 1000 requests/minute per API key

---

## Authentication

All endpoints require JWT authentication.

### Header Format

| Header Name     | Value Example          |
| --------------- | ---------------------- |
| `Authorization` | `Bearer <JWT_TOKEN>`   |
| `X-API-Key`     | `<API_KEY>`            |

---

## Common HTTP Status Codes

| Status Code | Meaning                      |
| ----------- | ---------------------------- |
| `200`       | Success                      |
| `201`       | Notification created         |
| `400`       | Bad request                  |
| `401`       | Unauthorized                 |
| `404`       | Notification not found       |
| `429`       | Rate limit exceeded          |
| `500`       | Internal server error        |

---

# 📨 Email APIs

## 1. Send Email

### Endpoint

```http
POST /emails/send
```

### Description

Sends a transactional email to a customer.

### Request Body

| Field        | Type   | Required | Description                    |
| ------------ | ------ | -------- | ------------------------------ |
| `to`         | string | Yes      | Recipient email address        |
| `templateId` | string | Yes      | Email template ID              |
| `subject`    | string | No       | Email subject (overrides template) |
| `variables`  | object | No       | Template variables             |
| `attachments`| array  | No       | Email attachments              |

### Example Request (cURL)

```bash
curl -X POST https://api.ecommerce.example.com/notifications/v1/emails/send \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "to": "customer@example.com",
    "templateId": "order_confirmation",
    "variables": {
      "customerName": "John Doe",
      "orderNumber": "ORD-2024-001234",
      "orderTotal": "$1,765.78",
      "trackingNumber": "1Z999AA10123456784"
    }
  }'
```

### Response Body

```json
{
  "emailId": "email_abc123xyz789",
  "to": "customer@example.com",
  "templateId": "order_confirmation",
  "status": "queued",
  "queuedAt": "2024-01-16T10:00:00Z",
  "estimatedDelivery": "2024-01-16T10:00:30Z"
}
```

---

## 2. Get Email Status

### Endpoint

```http
GET /emails/{emailId}
```

### Description

Retrieves the delivery status of an email.

### Example Request (cURL)

```bash
curl -X GET https://api.ecommerce.example.com/notifications/v1/emails/email_abc123xyz789 \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "emailId": "email_abc123xyz789",
  "to": "customer@example.com",
  "templateId": "order_confirmation",
  "status": "delivered",
  "sentAt": "2024-01-16T10:00:15Z",
  "deliveredAt": "2024-01-16T10:00:45Z",
  "opens": 1,
  "clicks": 2,
  "events": [
    {
      "type": "sent",
      "timestamp": "2024-01-16T10:00:15Z"
    },
    {
      "type": "delivered",
      "timestamp": "2024-01-16T10:00:45Z"
    },
    {
      "type": "opened",
      "timestamp": "2024-01-16T10:05:20Z"
    }
  ]
}
```

---

## 3. Send Bulk Emails

### Endpoint

```http
POST /emails/bulk
```

### Description

Sends emails to multiple recipients. Maximum 1000 recipients per request.

### Request Body

| Field        | Type   | Required | Description                    |
| ------------ | ------ | -------- | ------------------------------ |
| `templateId` | string | Yes      | Email template ID              |
| `recipients` | array  | Yes      | Array of recipient objects     |

### Example Request (cURL)

```bash
curl -X POST https://api.ecommerce.example.com/notifications/v1/emails/bulk \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "templateId": "promotional_sale",
    "recipients": [
      {
        "email": "customer1@example.com",
        "variables": {
          "firstName": "John",
          "discountCode": "SAVE20"
        }
      },
      {
        "email": "customer2@example.com",
        "variables": {
          "firstName": "Jane",
          "discountCode": "SAVE20"
        }
      }
    ]
  }'
```

### Response Body

```json
{
  "batchId": "batch_xyz789abc",
  "totalRecipients": 2,
  "status": "processing",
  "createdAt": "2024-01-16T10:00:00Z",
  "estimatedCompletion": "2024-01-16T10:05:00Z"
}
```

---

# 📱 SMS APIs

## 4. Send SMS

### Endpoint

```http
POST /sms/send
```

### Description

Sends an SMS message to a customer.

### Request Body

| Field        | Type   | Required | Description                    |
| ------------ | ------ | -------- | ------------------------------ |
| `to`         | string | Yes      | Recipient phone number (E.164) |
| `message`    | string | Yes      | SMS message text (max 160 chars) |
| `templateId` | string | No       | SMS template ID                |
| `variables`  | object | No       | Template variables             |

### Example Request (cURL)

```bash
curl -X POST https://api.ecommerce.example.com/notifications/v1/sms/send \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "to": "+14155551234",
    "templateId": "order_shipped",
    "variables": {
      "orderNumber": "ORD-2024-001234",
      "trackingNumber": "1Z999AA10123456784"
    }
  }'
```

### Response Body

```json
{
  "smsId": "sms_abc123xyz789",
  "to": "+14155551234",
  "message": "Your order ORD-2024-001234 has shipped! Track: 1Z999AA10123456784",
  "status": "queued",
  "segments": 1,
  "queuedAt": "2024-01-16T10:00:00Z"
}
```

---

## 5. Get SMS Status

### Endpoint

```http
GET /sms/{smsId}
```

### Description

Retrieves the delivery status of an SMS message.

### Example Request (cURL)

```bash
curl -X GET https://api.ecommerce.example.com/notifications/v1/sms/sms_abc123xyz789 \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "smsId": "sms_abc123xyz789",
  "to": "+14155551234",
  "status": "delivered",
  "sentAt": "2024-01-16T10:00:05Z",
  "deliveredAt": "2024-01-16T10:00:08Z",
  "cost": 0.0075,
  "currency": "usd"
}
```

---

# 🔔 Push Notification APIs

## 6. Send Push Notification

### Endpoint

```http
POST /push/send
```

### Description

Sends a push notification to a user's device.

### Request Body

| Field        | Type   | Required | Description                    |
| ------------ | ------ | -------- | ------------------------------ |
| `userId`     | string | Yes      | User ID                        |
| `title`      | string | Yes      | Notification title             |
| `body`       | string | Yes      | Notification body              |
| `data`       | object | No       | Custom data payload            |
| `imageUrl`   | string | No       | Notification image URL         |
| `actionUrl`  | string | No       | Deep link URL                  |

### Example Request (cURL)

```bash
curl -X POST https://api.ecommerce.example.com/notifications/v1/push/send \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "userId": "user_abc123",
    "title": "Order Delivered!",
    "body": "Your order ORD-2024-001234 has been delivered.",
    "imageUrl": "https://cdn.ecommerce.com/notifications/delivery.png",
    "actionUrl": "ecommerce://orders/ord_xyz789abc",
    "data": {
      "orderId": "ord_xyz789abc",
      "type": "order_delivered"
    }
  }'
```

### Response Body

```json
{
  "pushId": "push_abc123xyz789",
  "userId": "user_abc123",
  "title": "Order Delivered!",
  "status": "sent",
  "devicesSent": 2,
  "sentAt": "2024-01-16T10:00:00Z"
}
```

---

## 7. Get Push Notification Status

### Endpoint

```http
GET /push/{pushId}
```

### Description

Retrieves the delivery status of a push notification.

### Example Request (cURL)

```bash
curl -X GET https://api.ecommerce.example.com/notifications/v1/push/push_abc123xyz789 \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "pushId": "push_abc123xyz789",
  "userId": "user_abc123",
  "status": "delivered",
  "devicesSent": 2,
  "devicesDelivered": 2,
  "devicesFailed": 0,
  "sentAt": "2024-01-16T10:00:00Z",
  "deliveryRate": 100.0
}
```

---

# 📋 Template APIs

## 8. Create Email Template

### Endpoint

```http
POST /templates/email
```

### Description

Creates a new email template.

### Request Body

| Field        | Type   | Required | Description                    |
| ------------ | ------ | -------- | ------------------------------ |
| `name`       | string | Yes      | Template name                  |
| `subject`    | string | Yes      | Email subject                  |
| `htmlBody`   | string | Yes      | HTML email body                |
| `textBody`   | string | No       | Plain text email body          |
| `variables`  | array  | No       | List of template variables     |

### Example Request (cURL)

```bash
curl -X POST https://api.ecommerce.example.com/notifications/v1/templates/email \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "name": "order_confirmation",
    "subject": "Order Confirmation - {{orderNumber}}",
    "htmlBody": "<h1>Thank you {{customerName}}!</h1><p>Your order {{orderNumber}} has been confirmed.</p>",
    "textBody": "Thank you {{customerName}}! Your order {{orderNumber}} has been confirmed.",
    "variables": ["customerName", "orderNumber", "orderTotal"]
  }'
```

### Response Body

```json
{
  "templateId": "tmpl_email_abc123",
  "name": "order_confirmation",
  "type": "email",
  "createdAt": "2024-01-16T10:00:00Z"
}
```

---

## 9. List Templates

### Endpoint

```http
GET /templates
```

### Description

Lists all notification templates.

### Query Parameters

| Parameter | Type   | Required | Description                    |
| --------- | ------ | -------- | ------------------------------ |
| `type`    | string | No       | Filter by type: email, sms, push |
| `page`    | integer| No       | Page number (default: 1)       |
| `limit`   | integer| No       | Results per page (default: 20) |

### Example Request (cURL)

```bash
curl -X GET "https://api.ecommerce.example.com/notifications/v1/templates?type=email&page=1&limit=20" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "templates": [
    {
      "templateId": "tmpl_email_abc123",
      "name": "order_confirmation",
      "type": "email",
      "createdAt": "2024-01-16T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "totalTemplates": 45
  }
}
```

---

# 🔔 Preference APIs

## 10. Get User Preferences

### Endpoint

```http
GET /users/{userId}/preferences
```

### Description

Retrieves notification preferences for a user.

### Example Request (cURL)

```bash
curl -X GET https://api.ecommerce.example.com/notifications/v1/users/user_abc123/preferences \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "userId": "user_abc123",
  "email": {
    "orderUpdates": true,
    "promotions": false,
    "newsletters": true
  },
  "sms": {
    "orderUpdates": true,
    "promotions": false
  },
  "push": {
    "orderUpdates": true,
    "promotions": true,
    "recommendations": false
  }
}
```

---

## 11. Update User Preferences

### Endpoint

```http
PATCH /users/{userId}/preferences
```

### Description

Updates notification preferences for a user.

### Request Body

| Field   | Type   | Required | Description                    |
| ------- | ------ | -------- | ------------------------------ |
| `email` | object | No       | Email preferences              |
| `sms`   | object | No       | SMS preferences                |
| `push`  | object | No       | Push notification preferences  |

### Example Request (cURL)

```bash
curl -X PATCH https://api.ecommerce.example.com/notifications/v1/users/user_abc123/preferences \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789" \
  -d '{
    "email": {
      "promotions": true
    },
    "sms": {
      "orderUpdates": false
    }
  }'
```

### Response Body

```json
{
  "userId": "user_abc123",
  "email": {
    "orderUpdates": true,
    "promotions": true,
    "newsletters": true
  },
  "sms": {
    "orderUpdates": false,
    "promotions": false
  },
  "push": {
    "orderUpdates": true,
    "promotions": true,
    "recommendations": false
  },
  "updatedAt": "2024-01-16T10:00:00Z"
}
```

---

# 📊 Analytics APIs

## 12. Get Notification Analytics

### Endpoint

```http
GET /analytics/notifications
```

### Description

Retrieves analytics for sent notifications.

### Query Parameters

| Parameter   | Type   | Required | Description                    |
| ----------- | ------ | -------- | ------------------------------ |
| `type`      | string | No       | Filter by type: email, sms, push |
| `startDate` | string | Yes      | Start date (ISO 8601)          |
| `endDate`   | string | Yes      | End date (ISO 8601)            |

### Example Request (cURL)

```bash
curl -X GET "https://api.ecommerce.example.com/notifications/v1/analytics/notifications?type=email&startDate=2024-01-01&endDate=2024-01-31" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." \
  -H "X-API-Key: nk_live_abc123xyz789"
```

### Response Body

```json
{
  "period": {
    "startDate": "2024-01-01",
    "endDate": "2024-01-31"
  },
  "email": {
    "sent": 125000,
    "delivered": 123500,
    "bounced": 1200,
    "opened": 85000,
    "clicked": 35000,
    "deliveryRate": 98.8,
    "openRate": 68.8,
    "clickRate": 28.3
  }
}
```

---

## Error Response Format

All error responses follow this structure:

```json
{
  "errorCode": "INVALID_EMAIL",
  "message": "Email address is invalid",
  "timestamp": "2024-01-16T15:00:00Z",
  "requestId": "req_xyz123abc"
}
```

### Common Error Codes

| Error Code              | Description                          |
| ----------------------- | ------------------------------------ |
| `INVALID_EMAIL`         | Email address format is invalid      |
| `INVALID_PHONE`         | Phone number format is invalid       |
| `TEMPLATE_NOT_FOUND`    | Template does not exist              |
| `USER_UNSUBSCRIBED`     | User has unsubscribed                |
| `RATE_LIMIT_EXCEEDED`   | Too many requests                    |
| `DELIVERY_FAILED`       | Notification delivery failed         |
| `INVALID_TEMPLATE`      | Template syntax error                |

---
