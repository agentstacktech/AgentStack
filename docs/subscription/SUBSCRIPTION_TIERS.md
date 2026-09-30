# Subscription Tiers Documentation

## Overview

AgentStack offers multiple subscription tiers to meet different user needs, from anonymous users trying the platform to enterprise customers requiring unlimited resources.

**See also:** Paid add-ons (overage, expansion packages) are described below and in the in-app **Billing / Pricing** area on [agentstack.tech](https://agentstack.tech). For machine-readable contracts, use [Swagger](https://agentstack.tech/swagger) (billing-related tags).

## Available Tiers

Two planes. Current prices and limits: [agentstack.tech/pricing](https://agentstack.tech/pricing).

**Personal** (the user, not a project): Free, Premium $15, VIP $45. They grant personal AI energy, personal files, and personal project slots (5 / 25 / 99). Business projects do not use those slots.

**Business** (the business and the projects inside it): Free, Launch $29 (`starter`), Business $69 (`basic`), Scale $199 (`pro`), Enterprise custom. There is no cap on how many projects you add. Active projects are how many run at once (Free 1, Launch 3, Business 3, Scale 15).

The stored string `business` still means enterprise until the legacy rewrite. Do not treat Premium as a $999 corporate plan.

## Tier Comparison

| Feature | Anonymous | FREE | LAUNCH | BUSINESS | SCALE | PREMIUM | VIP | ENTERPRISE |
|---------|-----------|------|--------|----------|-------|---------|-----|------------|
| **Price** | $0 | $0 | $29/mo | $69/mo | $199/mo | $15/mo | $45/mo | Custom |
| **Plane** | — | both | business | business | business | personal | personal | business |
| **Personal projects** | 0 | 5 | 5 | 5 | 5 | 25 | 99 | 5 |
| **Active projects** | 1 | 1 | 3 | 3 | 15 | — | — | unlimited |

Included API, Logic Engine, members, and project storage stay on the business plane. Read them from the catalog above, not from a second table. Sandbox quotas: [SANDBOX_AND_ENVIRONMENTS.md](../SANDBOX_AND_ENVIRONMENTS.md).

## Overage Pricing (Pay-as-you-go)

AgentStack offers flexible Pay-as-you-go pricing for exceeding subscription limits. This allows you to pay only for what you use beyond your plan's included limits, without needing to upgrade your tier.

### Logic Engine Calls Overage

- **Price:** $1.00 per 1,000 calls beyond your limit
- **Minimum charge:** $0.01 for any overage
- **Example:** If you have 50K calls included and use 60K, you pay $10.00 for the 10K overage

### API Calls Overage

- **Price:** $0.50 per 1,000 calls beyond your limit
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

Packages are one-time purchases that add capacity to the business plan. Projects inside that business share the allowance.

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

**Key Features (business plane — shared inside one business):**
- 1 active business project on free business tier
- 1,000 total members on that business
- 10,000 Logic Engine calls/month
- 50,000 API calls/month
- 50 triggers
- 100 MB JSON storage per user
- 0.1 GB project storage (data only, files in separate service)
- Payments enabled

### STARTER Tier

**Best for:** Small teams and growing projects

- 3 active business projects (Launch)
- 5,000 total members on that business
- 200,000 API calls/month
- 50,000 Logic Engine calls/month
- 100 triggers, 5 logic pages
- 500 MB JSON storage per user
- Webhooks and user management
- Email support

### BASIC Tier

**Best for:** Medium-sized teams

- 3 active business projects (Business / `basic`)
- 25,000 total members on that business
- 50,000 Logic Engine calls/month
- 500,000 API calls/month
- 100 triggers
- 500 MB JSON storage per user
- 1 GB project storage (data only, files in separate service)
- Email support

### PRO Tier

**Best for:** Professional teams and businesses

- 15 active business projects (Scale / `pro`)
- Unlimited total members on that business
- 200,000 Logic Engine calls/month
- 2,000,000 API calls/month
- 250 triggers
- 2 GB JSON storage per user
- 5 GB project storage (data only, files in separate service)
- Priority support
- Custom analytics
- Audit logs

### PREMIUM / VIP (personal plane)

**Best for:** Power users — personal AI energy, personal files, and personal project slots (5 / 25 / 99). Does **not** raise Logic Engine, API, members, or project storage on business projects.

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

**Important:** Logic Engine, API calls, members, and project storage on a **business** plan are shared by the projects inside that business. Personal Premium and VIP do not raise those project quotas.

All limits are enforced at the API level:

1. **Logic Engine** - Checked per business project (tier from project billing resolver)
2. **Project Creation** - Personal slots vs active business projects (separate gates)
3. **API Key Creation** - Per project / business limits
4. **Storage** - Project storage per business tier; JSON storage per user (personal plane)
5. **User Count** - `max_total_members` on the business plan
6. **JSON Storage** - Per-user cap from personal subscription (Premium/VIP raise only this)
7. **Payments** - Limits checked before payment processing

### Metrics Storage

Usage metrics are automatically tracked by the ecosystem:

- **Project local metrics:** Stored in `project.data.ecosystem.metrics` (protected, not visible to project owners)
- **Usage counters:** Stored in `user.data.metrics` (ecosystem slice); business limits are enforced per project billing tier while metrics may still roll up for billing UI
- **Automatic updates:** Metrics are updated automatically when resources are used (no explicit calls needed)
- **Fast access:** Metrics are cached for O(1) performance when checking limits

## Error Responses

When limits are exceeded, the API returns appropriate error codes:

- **429 Too Many Requests** - Rate limit exceeded
- **403 Forbidden** - Operation not allowed for current tier
- **402 Payment Required** - Upgrade required for this feature

## Related Documentation

- [Anonymous Tier](./ANONYMOUS_TIER.md) — Anonymous tier details
- [API Documentation](../api/README.md) — REST topic index
- [OPENAPI.md](../OPENAPI.md) — Swagger UI and `openapi.json`
- [USER_FEATURES_GUIDE.md](../USER_FEATURES_GUIDE.md) — Using the dashboard and new platform features (public English)
- Billing and subscription limits — contact support or see [USER_FEATURES_GUIDE.md](../USER_FEATURES_GUIDE.md) subscription section

