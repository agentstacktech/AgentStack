# Anonymous Tier Documentation

## Overview

The **Anonymous** tier is a restricted subscription tier designed for anonymous users who want to try AgentStack without full registration. This tier provides limited functionality to allow users to explore the platform before committing to a full account.

## Key Features

- **No registration required** - Users can start using AgentStack immediately
- **Automatic account creation** - Anonymous users are created automatically when using `/api/projects/anonymous`
- **Conversion to full account** - Users can convert to the FREE tier at any time by providing name, email, and password
- **Strict limits** - Significantly lower limits than the FREE tier to encourage conversion

## Limits

### Projects
- **1 project maximum** - Only one project can be created via `/api/projects/anonymous`
- **Cannot create additional projects** - Creating projects via `/api/projects` is blocked for anonymous users
- **1 active project** - Only one project can be active at a time

### Users & Members
- **10 members per project** - Maximum of 10 users per project
- **10 total members** - Maximum of 10 total members across all projects

### API Keys
- **1 API key** - Only one API key is provided at account creation (with prefix `anon_ask_`)
- **Cannot create additional API keys** - Creating additional API keys is blocked for anonymous users
- **1 user API key** - Only one user API key is allowed

### Logic Engine
- **1,000 calls per month** - Significantly lower than FREE tier (10,000 calls/month)
- **20 triggers maximum** - Limited to 20 triggers per project (vs 50 for FREE tier)
- **3 logic pages** - Same as FREE tier

### Storage
- **20 MB JSON storage** - Limited to 20 MB (vs 100 MB for FREE tier)
- **0.02 GB total storage** - Limited to 0.02 GB (vs 0.1 GB for FREE tier)

### Payments
- **Payments disabled** - Anonymous users cannot process payments or work with real money
- **Payment amount: $0.00** - No payment processing allowed

### Analytics
- **7 days retention** - Same as FREE tier
- **1 export** - Same as FREE tier

### Support
- **Community support** - Same as FREE tier
- **99.0% uptime SLA** - Same as FREE tier

## Creating an Anonymous Account

Anonymous accounts are created automatically when using the `/api/projects/anonymous` endpoint:

```bash
POST /api/projects/anonymous
{
  "name": "My Project",
  "description": "Project description"
}
```

**Response:**
```json
{
  "success": true,
  "project_id": 1234,
  "user_id": 5678,
  "user_api_key": "anon_ask_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "project_api_key": "ask_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "session_token": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "project": {
    "id": 1234,
    "name": "My Project"
  }
}
```

## Conversion to Full Account

When anonymous users exceed their limits or want to upgrade, they can convert to a full FREE tier account:

### Conversion Endpoint

```bash
POST /api/auth/convert-anonymous
{
  "user_api_key": "anon_ask_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "name": "John Doe",
  "email": "john@example.com",
  "password": "secure_password"
}
```

### What Happens During Conversion

1. **User data updated** - `is_anonymous` flag is set to `false`
2. **Email and password added** - User can now log in with email/password
3. **Subscription upgraded** - User is automatically moved to FREE tier
4. **Limits increased** - All limits are increased to FREE tier levels:
   - Logic Engine calls: 1,000 → 10,000/month
   - JSON storage: 20 MB → 100 MB
   - Triggers: 20 → 50
   - Storage: 0.02 GB → 0.1 GB
   - Payments: Enabled
   - Can create additional projects
   - Can create additional API keys

### After Conversion

- User can log in with email/password
- User can create additional projects via `/api/projects`
- User can create additional API keys
- User can process payments
- All FREE tier limits apply

## Error Responses

### Limit Exceeded

When an anonymous user exceeds their limits, the API returns a 429 error with conversion information:

```json
{
  "error": "Limit exceeded",
  "reason": "Anonymous user limit exceeded",
  "limit": 1000,
  "current": 1001,
  "requires_conversion": true,
  "conversion_url": "/api/auth/convert-anonymous",
  "conversion_message": "Please convert to full account to increase limits. Anonymous users are limited to 1,000 Logic Engine calls per month."
}
```

### Blocked Operations

When an anonymous user attempts a blocked operation, the API returns a 403 error:

**Creating additional projects:**
```json
{
  "error": "Anonymous users cannot create additional projects",
  "reason": "Anonymous users are limited to 1 project. Please convert to full account to create more projects.",
  "requires_conversion": true,
  "conversion_url": "/api/auth/convert-anonymous",
  "conversion_message": "Please convert to full account to create additional projects."
}
```

**Creating API keys:**
```json
{
  "error": "Anonymous users cannot create API keys",
  "reason": "Anonymous users are limited to 1 API key (provided at account creation). Please convert to full account to create additional API keys.",
  "requires_conversion": true,
  "conversion_url": "/api/auth/convert-anonymous",
  "conversion_message": "Please convert to full account to create additional API keys."
}
```

**Processing payments:**
```json
{
  "error": "Anonymous users cannot process payments",
  "reason": "Payment processing is not available for anonymous users. Please convert to full account to use payment features.",
  "requires_conversion": true,
  "conversion_url": "/api/auth/convert-anonymous",
  "conversion_message": "Please convert to full account to process payments."
}
```

## Comparison with FREE Tier

| Feature | Anonymous | FREE |
|---------|-----------|------|
| Projects | 1 | 1 |
| Members per project | 10 | 1,000 |
| Total members | 10 | 1,000 |
| API keys | 1 | 2 |
| User API keys | 1 | 1 |
| Logic Engine calls/month | 1,000 | 10,000 |
| Triggers | 20 | 50 |
| Logic pages | 3 | 3 |
| JSON storage | 20 MB | 100 MB |
| Total storage | 0.02 GB | 0.1 GB |
| Payments | ❌ Disabled | ✅ Enabled |
| Analytics retention | 7 days | 7 days |
| Analytics exports | 1 | 1 |
| Support | Community | Community |
| Uptime SLA | 99.0% | 99.0% |

## Best Practices

1. **Save API keys immediately** - API keys are shown only once during account creation
2. **Monitor usage** - Keep track of Logic Engine calls and storage usage
3. **Convert early** - Convert to FREE tier before hitting limits to avoid service interruption
4. **Use conversion endpoint** - Use `/api/auth/convert-anonymous` when ready to upgrade

## Technical Details

### Subscription Tier Detection

The system automatically detects anonymous users by checking the `is_anonymous` flag in user data:

```python
# In subscription_service.get_user_subscription()
user_data = user.data if isinstance(user.data, dict) else {}
is_anonymous = user_data.get('is_anonymous', False)

if is_anonymous:
    return {"plan_type": "anonymous", ...}
```

### Limit Enforcement

Limits are enforced at multiple levels:

1. **Logic Engine execution** - Checks limits before executing logic
2. **Project creation** - Blocks additional project creation
3. **API key creation** - Blocks additional API key creation
4. **Payment processing** - Blocks payment operations
5. **Storage operations** - Enforces JSON storage limits

### Conversion Process

1. User provides `user_api_key`, `name`, `email`, and `password`
2. System validates the anonymous API key
3. System checks if email is already registered
4. System updates user data: sets `is_anonymous: False`, adds email and password hash
5. System creates/updates subscription record with `plan_type: "free"`
6. System creates session for converted user
7. User can now use all FREE tier features

## Related Documentation

- [Subscription Tiers](./SUBSCRIPTION_TIERS.md) - Complete list of all subscription tiers
- [API Documentation](../api/README.md) - API endpoint documentation
- [Authentication Guide](../architecture/AUTHENTICATION.md) - Authentication and authorization

