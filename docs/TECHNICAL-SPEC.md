# Jobber Integration Specification

**Service:** Jobber (Field Service Management)
**API Type:** GraphQL
**Endpoint:** `https://api.getjobber.com/api/graphql`
**n8n Node:** `@arisegroup/n8n-nodes-jobber` v0.1.1
**Status:** Production Ready

---

## Overview

Jobber is a field service management platform providing:
- Client/customer management
- Job scheduling and tracking
- Quoting and invoicing
- Team scheduling and dispatch

---

## Authentication

**Method:** OAuth 2.0

| Endpoint | URL |
|----------|-----|
| Authorization | `https://api.getjobber.com/api/oauth/authorize` |
| Token | `https://api.getjobber.com/api/oauth/token` |

**Flow:**
1. Redirect user to authorization URL with `client_id`, `redirect_uri`, `state`
2. User approves access in Jobber
3. Jobber redirects with authorization code
4. Exchange code for access token (expires in 60 minutes)
5. Use refresh token to obtain new access tokens

**Request Header:**
```
Authorization: Bearer <ACCESS_TOKEN>
X-JOBBER-GRAPHQL-VERSION: 2023-11-15
```

---

## API Resources & Operations

| Resource | Operations | Description |
|----------|-----------|-------------|
| **Clients** | create, read, update, delete | Contact details, billing address, company info, custom fields, balance, notes, tags |
| **Jobs** | create, read, update, delete | Scheduling, assignments, line items, status tracking |
| **Quotes** | create, read, update, delete, convertToJob | Estimates with line items, approval workflow |
| **Invoices** | create, read, update, delete | Billing, payments, line items |
| **Requests** | create, read, update, delete | Work requests from customers |
| **Properties** | create, read, update, delete | Service locations linked to clients |
| **Assessments** | create, read, update, delete | Site assessments with scheduling |
| **Custom Fields** | create, manage | Configurable for clients, properties, quotes, jobs, invoices |
| **Users** | read | Team member information and assignments |
| **Notes** | create, read, update, delete | Client-associated notes |
| **Tags** | create, read, update, delete | Client categorization |
| **Account** | read | Company name, phone, industry, features |

---

## Rate Limits

**Two-tier protection system:**

| Limit Type | Details |
|------------|---------|
| **DDoS Protection** | 2,500 requests per 5 minutes per app/account |
| **Query Cost** | Points-based using leaky bucket algorithm |

**When Exceeded:** Returns HTTP 429 Too Many Requests

**Optimization Best Practices:**
- Use pagination with `first`, `after`, `last`, `before`
- Prefer cursor-based pagination for large datasets
- Avoid deeply nested queries
- Implement delays between bulk calls
- Cache frequently accessed data

---

## Webhook Events

Webhooks send real-time POST requests when events occur in Jobber.

| Topic | Description |
|-------|-------------|
| `CLIENT_CREATE` | New client created |
| `CLIENT_UPDATE` | Client modified |
| `CLIENT_DELETE` | Client removed |
| `JOB_CREATE` | New job created |
| `JOB_UPDATE` | Job modified |
| `JOB_COMPLETE` | Job marked complete |
| `JOB_CLOSE` | Job closed |
| `QUOTE_CREATE` | New quote created |
| `QUOTE_UPDATE` | Quote modified |
| `QUOTE_APPROVAL` | Quote approved by customer |
| `INVOICE_CREATE` | New invoice created |
| `INVOICE_UPDATE` | Invoice modified |
| `REQUEST_CREATE` | New work request |
| `REQUEST_UPDATE` | Request modified |
| `VISIT_COMPLETE` | Visit completed |
| `TIMESHEET_ENTRY_CREATE` | Timesheet entry added |
| `APP_CONNECT` | App connected to account |

**Payload Example:**
```json
{
  "data": {
    "webHookEvent": {
      "topic": "CLIENT_CREATE",
      "appId": "3ef22a50-072d-430c-a78f-b7646657560b",
      "accountId": "MQ==",
      "itemId": "MQ==",
      "occurredAt": "2021-08-12T16:31:36-06:00"
    }
  }
}
```

**Security:**
- HMAC-SHA256 signature in `X-Jobber-Hmac-SHA256` header
- Verify using your OAuth client secret
- **Must respond within 1 second** (process asynchronously)
- At-least-once delivery (handle duplicates idempotently)

---

## GraphQL Examples

**Query Clients:**
```graphql
query {
  clients(first: 10) {
    nodes {
      id
      name
      emails { address }
      phones { number }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

**Query Jobs:**
```graphql
query {
  jobs(first: 10) {
    nodes {
      id
      jobNumber
      title
      client { name }
      jobStatus
    }
  }
}
```

**Create Job:**
```graphql
mutation {
  jobCreate(input: {
    clientId: "Q2xpZW50OjEyMzQ1",
    title: "Printer Repair",
    description: "Customer reports paper jam"
  }) {
    job { id jobNumber }
    userErrors { message }
  }
}
```

---

## n8n Node Implementation

### Node Types
| Node | Description |
|------|-------------|
| **Jobber** | Main action node (CRUD operations) |
| **JobberTrigger** | Webhook trigger node |

### Features
- Full CRUD for Clients, Jobs, Quotes, Invoices
- Raw GraphQL query execution for advanced use cases
- Dynamic dropdown loading for client/job/quote selection
- Cursor-based pagination for large datasets
- HMAC-SHA256 webhook signature verification
- Event deduplication (tracks last 100 event IDs)

### File Structure
```
n8n-nodes-jobber/
├── credentials/
│   └── JobberOAuth2Api.credentials.ts
├── nodes/Jobber/
│   ├── Jobber.node.ts           # Versioned wrapper
│   ├── JobberTrigger.node.ts    # Webhook trigger
│   └── v1/
│       ├── JobberV1.node.ts     # Main implementation
│       ├── actions/
│       │   ├── client/          # Client operations
│       │   ├── job/             # Job operations
│       │   ├── quote/           # Quote operations
│       │   ├── invoice/         # Invoice operations
│       │   └── graphql/         # Raw GraphQL
│       ├── helpers/             # Utilities
│       ├── methods/             # Load options
│       └── transport/           # API layer
└── dist/                        # Compiled output
```

---

## API Documentation

- [Jobber Developer Docs](https://developer.getjobber.com/docs)
- [GraphQL API Reference](https://developer.getjobber.com/docs/using_jobbers_api/api_queries_and_mutations/)
- [Webhook Setup](https://developer.getjobber.com/docs/using_jobbers_api/setting_up_webhooks/)
- [App Authorization](https://developer.getjobber.com/docs/building_your_app/app_authorization/)

---

## Development Status

### Completed
- Full CRUD for all 4 main resources
- OAuth2 authentication flow
- Webhook trigger with signature verification
- Cursor-based pagination
- Dynamic dropdown loading
- TypeScript with strict mode
- ESLint + Prettier configured
- Published to npm

### No Enhancements Planned
The Jobber node is feature-complete. The GraphQL escape hatch covers any edge cases not addressed by standard operations.

---

*Last Updated: December 26, 2025*
