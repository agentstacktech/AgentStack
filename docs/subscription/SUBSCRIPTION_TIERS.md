# Subscription Tiers Documentation

## Overview

AgentStack offers multiple subscription tiers to meet different user needs, from anonymous users trying the platform to enterprise customers requiring unlimited resources.

**See also:** Paid add-ons (overage, expansion packages) are described below and in the in-app **Billing / Pricing** area on [agentstack.tech](https://agentstack.tech). For machine-readable contracts, use [Swagger](https://agentstack.tech/swagger) (billing-related tags).

## Available Tiers

1. **ANONYMOUS** - Restricted tier for anonymous users (no registration required)
2. **FREE** - Free tier for registered users
3. **STARTER** - Entry-level paid tier
4. **BASIC** - Mid-level paid tier
5. **PRO** - Professional tier
6. **PREMIUM** - Corporate tier
7. **ENTERPRISE** - Enterprise tier (unlimited resources)

## Tier Comparison

| Feature | Anonymous | FREE | STARTER | BASIC | PRO | PREMIUM | ENTERPRISE |
|---------|-----------|------|---------|-------|-----|---------|------------|
| **Price** | $0 | $0 | $29/mo | $69/mo | $199/mo | $999/mo | Custom |
| **Projects** | 1 | 1 | 3 | 5 | 25 | Unlimited | Unlimited |
| **Active Projects** | 1 | 1 | 3 | 3 | 15 | Unlimited | Unlimited |
| **Total Members (Aggregated)** | 10 | 1,000 | 5,000 | 25,000 | Unlimited | Unlimited | Unlimited |
| **Note** | ✅ Aggregated across all projects | ✅ Aggregated across all projects | ✅ Aggregated across all projects | ✅ Aggregated across all projects | - | - | - |
| **API Keys** | 1 | 2 | 10 | 10 | 50 | 200 | Unlimited |
| **User API Keys** | 1 | 1 | 3 | 5 | 10 | Unlimited | Unlimited |
| **Logic Engine Calls/Month** | 1,000 | 10,000 | 50,000 | 50,000 | 200,000 | 1,000,000 | Unlimited |
| **API Calls/Month** | 10,000 | 50,000 | 200,000 | 500,000 | 2,000,000 | 10,000,000 | Unlimited |
| **Triggers** | 20 | 50 | 100 | 100 | 250 | 1,000 | Unlimited |
| **Logic Pages** | 3 | 3 | 5 | 5 | 10 | 50 | Unlimited |
| **JSON Storage (Per User)** | 20 MB | 100 MB | 500 MB | 500 MB | 2 GB | 10 GB | Unlimited |
| **Project Storage (Data Only)** | 0.02 GB | 0.1 GB | 0.5 GB | 1 GB | 5 GB | 20 GB | Unlimited |
| **Note** | Files in separate service | Files in separate service | Files in separate service | Files in separate service | Files in separate service | Files in separate service | Files in separate service |
| **Payments** | ❌ Disabled | ✅ Unlimited | ✅ Unlimited | ✅ Unlimited | ✅ Unlimited | ✅ Unlimited | ✅ Unlimited |
| **Analytics Retention** | 7 days | 7 days | 30 days | 30 days | 90 days | 365 days | Unlimited |
| **Analytics Exports** | 1 | 1 | 5 | 5 | 20 | 100 | Unlimited |
| **Support** | Community | Community | Email | Email | Priority | Priority | Dedicated |
| **Uptime SLA** | 99.0% | 99.0% | 99.5% | 99.5% | 99.9% | 99.95% | 99.99% |

## Overage Pricing (Pay-as-you-go)

AgentStack offers flexible Pay-as-you-go pricing for exceeding subscription limits. This allows you to pay only for what you use beyond your plan's included limits, without needing to upgrade your tier.

### Logic Engine Calls Overage

- **Price:** $1.00 per 1,000 calls beyond your limit
- **Minimum charge:** $0.01 for any overage
- **Example:** If you have 50K calls included and use 60K, you pay $10.00 for the 10K overage

### API Calls Overage

- **Price:** $0.50 per 1,000 calls beyond your limit (50% cheaper than Logic Engine)
- **Minimum charge:** $0.01 for any overage
- **Example:** If you have 500K calls included and use 600K, you pay $50.00 for the 100K overage

### Storage Packages

Storage packages allow you to expand your project's total storage beyond the included limit:

- **5 GB package:** $0.50 ($0.10 per GB)
- **10 GB package:** $0.90 ($0.09 per GB) - Best value
- **25 GB package:** $2.00 ($0.08 per GB) - Most economical

**Important:** 
- JSON Storage limit per user (data container) cannot be expanded via packages - it only increases with tier upgrades
- Storage packages apply to general project storage only (data, not files - files will be in separate storage service)
- Logic Engine and API calls can be expanded via expansion packages (see below)

### Overage Configuration

You can enable or disable overage billing for your project:
- Enable/disable globally for the project
- Enable/disable specifically for Logic Engine calls
- Enable/disable specifically for API calls
- Enable/disable specifically for Storage

When overage is disabled, the system will block operations when limits are exceeded (HTTP 429).

### Expansion Packages for Gaming Projects

For gaming projects that need more Logic Engine calls and API calls without upgrading their tier:

**Logic Engine Packages:**
- Small: +10,000 calls/month - $5.00
- Medium: +50,000 calls/month - $20.00
- Large: +100,000 calls/month - $35.00

**API Calls Packages:**
- Small: +100,000 calls/month - $5.00
- Medium: +500,000 calls/month - $20.00
- Large: +1,000,000 calls/month - $35.00

**Purchase:** 
- Logic Engine: `POST /api/billing/logic-engine-package?package_size=small|medium|large`
- API Calls: `POST /api/billing/api-calls-package?package_size=small|medium|large`

Packages are one-time purchases that add permanent capacity. They are stored in `user.data.config.packages` and apply to the user's account (aggregated across all projects).

**See:** Overage and package behaviour in this document (sections above). For live API parameters, use [Swagger](https://agentstack.tech/swagger) and [OPENAPI.md](../OPENAPI.md).

## Detailed Tier Information

### ANONYMOUS Tier

**Best for:** Users who want to try AgentStack without registration

- No registration required
- Automatic account creation
- Can convert to FREE tier at any time
- Strict limits to encourage conversion

**See:** [Anonymous Tier Documentation](./ANONYMOUS_TIER.md) for complete details

### FREE Tier

**Best for:** Individual developers and small projects

- Full registration required
- Basic features and limits
- Community support
- Good starting point for learning AgentStack

**Key Features:**
- 1 project
- 1,000 total members (aggregated across all projects)
- 10,000 Logic Engine calls/month
- 50,000 API calls/month
- 50 triggers
- 100 MB JSON storage per user
- 0.1 GB project storage (data only, files in separate service)
- Payments enabled

### STARTER Tier

**Best for:** Small teams and growing projects

- 3 projects (3 active)
- 5,000 total members (aggregated across projects)
- 200,000 API calls/month
- 50,000 Logic Engine calls/month
- 100 triggers, 5 logic pages
- 500 MB JSON storage per user
- Webhooks and user management
- Email support

### BASIC Tier

**Best for:** Medium-sized teams

- 5 projects
- 25,000 total members (aggregated across all projects)
- 50,000 Logic Engine calls/month
- 500,000 API calls/month
- 100 triggers
- 500 MB JSON storage per user
- 1 GB project storage (data only, files in separate service)
- Email support

### PRO Tier

**Best for:** Professional teams and businesses

- 25 projects
- Unlimited total members (aggregated across all projects)
- 200,000 Logic Engine calls/month
- 2,000,000 API calls/month
- 250 triggers
- 2 GB JSON storage per user
- 5 GB project storage (data only, files in separate service)
- Priority support
- Custom analytics
- Audit logs

### PREMIUM Tier

**Best for:** Large organizations

- Unlimited projects
- Unlimited members
- 1,000,000 Logic Engine calls/month
- 1,000 triggers
- 10 GB JSON storage
- Priority support
- White label
- Custom domain
- System admin access

### ENTERPRISE Tier

**Best for:** Enterprise customers with custom requirements

- Unlimited everything
- Dedicated support
- Custom integrations
- On-premise deployment options
- Custom SLA
- Advanced security features

## Feature Comparison

### User Management
- **FREE/ANONYMOUS:** Basic user management
- **STARTER/BASIC:** User management enabled
- **PRO/PREMIUM/ENTERPRISE:** Advanced user management

### API Access
- **All tiers:** Basic API access
- **STARTER+:** Advanced API access

### Analytics
- **FREE/ANONYMOUS:** Basic analytics (7 days retention)
- **STARTER/BASIC:** Advanced analytics (30 days retention)
- **PRO:** Custom analytics (90 days retention)
- **PREMIUM:** Custom analytics (365 days retention)
- **ENTERPRISE:** Custom analytics (unlimited retention)

### Webhooks
- **FREE/ANONYMOUS:** Not available
- **STARTER/BASIC:** Basic webhooks
- **PRO+:** Advanced webhooks

### Security
- **All tiers:** Basic security
- **PRO+:** Advanced security
- **PRO+:** Audit logs

### Field Access Policy (FAP) and Service Caps

- **FAP (Field Access Policy):** Rules (including trigger-driven rules) control which fields appear in API responses per role. See [Field Access Policy](../FIELD_ACCESS_POLICY.md) for configuration and behavior.
- **Service Caps (L1) on project API keys:** From **STARTER** upward, keys may include optional caps that limit use of sensitive modules (e.g. payments, scheduler, analytics, project wallets, data access) per key. Exact caps and enforcement follow the subscription tier and backend policy.
- **Admin workspace:** **PRO** includes `admin_functions` (admin tooling); **PREMIUM** and **ENTERPRISE** add `system_admin` where applicable.

### Customization
- **PRO+:** Custom domain
- **PREMIUM+:** White label

## Upgrading Tiers

### From ANONYMOUS to FREE

Use the conversion endpoint:

```bash
POST /api/auth/convert-anonymous
{
  "user_api_key": "anon_ask_...",
  "name": "John Doe",
  "email": "john@example.com",
  "password": "secure_password"
}
```

### From FREE to Paid Tiers

Contact support or use the billing interface to upgrade to a paid tier.

## Limit Enforcement

**Important:** All limits are applied **aggregated across all user's projects**, not per-project. For example, if you have 50,000 Logic Engine calls/month included, this limit applies to the total usage across all your projects combined.

All limits are enforced at the API level:

1. **Logic Engine** - Limits checked before execution (aggregated across all projects)
2. **Project Creation** - Limits checked before creation
3. **API Key Creation** - Limits checked before creation
4. **Storage** - Limits checked during storage operations (aggregated across all projects)
5. **User Count** - Limits checked when adding users (aggregated across all projects - `max_total_members`)
6. **JSON Storage** - Limits checked during storage operations (aggregated across all projects)
7. **Payments** - Limits checked before payment processing

### Metrics Storage

Usage metrics are automatically tracked by the ecosystem:

- **Project local metrics:** Stored in `project.data.ecosystem.metrics` (protected, not visible to project owners)
- **User aggregated metrics:** Stored in `user.data.metrics` (project_id=1, ecosystem) - aggregated across all projects
- **Automatic updates:** Metrics are updated automatically when resources are used (no explicit calls needed)
- **Fast access:** Metrics are cached for O(1) performance when checking limits

## Error Responses

When limits are exceeded, the API returns appropriate error codes:

- **429 Too Many Requests** - Rate limit exceeded
- **403 Forbidden** - Operation not allowed for current tier
- **402 Payment Required** - Upgrade required for this feature

## Related Documentation

- [Anonymous Tier](./ANONYMOUS_TIER.md) — Anonymous tier details
- [API topic index](../api/README.md) — REST topic index · [OPENAPI.md](../OPENAPI.md)
- [OPENAPI.md](../OPENAPI.md) — Swagger UI and `openapi.json`
- [USER_FEATURES_GUIDE.md](../USER_FEATURES_GUIDE.md) — Using the dashboard and new platform features

