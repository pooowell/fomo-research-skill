---
name: cope-account
description: Manage your Cope Capital account settings, view usage statistics, configure x402 payments, and handle API key lifecycle. Essential for account management and payment setup.
metadata:
  author: cope-capital
  version: "1.0"
---

# Instructions

Manage your Cope Capital account settings including x402 payment configuration, usage tracking, and API key management. Use this skill for account administration and payment setup.

## When to use this skill
- User wants to check their API usage or remaining limits
- User needs to configure x402 payments for unlimited access
- User asks about account settings or payment history
- User wants to view or update their payment address
- User needs to rotate or revoke their API key

## Get account information

View your account details, usage stats, and current limits:

```bash
curl -X GET https://api.cope.capital/v1/account \
  -H "Authorization: Bearer cope_<your_api_key>"
```

### Response
```json
{
  "agent_name": "my-trading-bot",
  "x402_enabled": false,
  "payment_address": null,
  "usage_today": 127,
  "usage_month": 2340,
  "limits": {
    "calls_per_day": 250,
    "watchlists": 1,
    "handles_per_watchlist": 10,
    "rate_limit": "10/min"
  }
}
```

With x402 enabled:
```json
{
  "agent_name": "my-trading-bot",
  "x402_enabled": true,
  "payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD18",
  "usage_today": 324,
  "usage_month": 5680,
  "limits": {
    "watchlists": 10,
    "handles_per_watchlist": 100,
    "rate_limit": "300/min"
  },
  "total_paid_usd": 12.50
}
```

## Configure x402 payments

Enable unlimited API access by setting your payment wallet address:

```bash
curl -X PATCH https://api.cope.capital/v1/account \
  -H "Authorization: Bearer cope_<your_api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD18"
  }'
```

### Supported payment addresses
- **Base USDC**: Ethereum-compatible address (0x...)
- **Solana USDC**: Solana wallet address

### Response
```json
{
  "agent_name": "my-trading-bot",
  "x402_enabled": true,
  "payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD18",
  "updated": true
}
```

## Disable x402 payments

Return to free tier limits by removing your payment address:

```bash
curl -X PATCH https://api.cope.capital/v1/account \
  -H "Authorization: Bearer cope_<your_api_key>" \
  -H "Content-Type: application/json" \
  -d '{
    "payment_address": null
  }'
```

## View detailed usage statistics

Get breakdown of API usage by endpoint and time period:

```bash
curl -X GET "https://api.cope.capital/v1/account/usage?since=1707600000&until=1707686400" \
  -H "Authorization: Bearer cope_<your_api_key>"
```

### Parameters
- `since`: Start timestamp (Unix ms)
- `until`: End timestamp (Unix ms)
- `endpoint`: Filter by specific endpoint (optional)

### Response
```json
{
  "calls": 234,
  "by_endpoint": {
    "/v1/activity": 150,
    "/v1/leaderboard": 45,
    "/v1/tokens": 39
  }
}
```

## View payment history

See your x402 payment transactions:

```bash
curl -X GET "https://api.cope.capital/v1/account/payments?limit=50" \
  -H "Authorization: Bearer cope_<your_api_key>"
```

### Response
```json
{
  "payments": [
    {
      "endpoint": "/v1/activity",
      "amount_usd": 0.005,
      "token_mint": "USDC",
      "tx_hash": "0xabc123...",
      "chain": "base",
      "timestamp": 1707600000
    }
  ],
  "total_usd": 1.25,
  "total_count": 250
}
```

## Revoke API key

Permanently revoke your current API key:

```bash
curl -X DELETE https://api.cope.capital/v1/account/key \
  -H "Authorization: Bearer cope_<your_api_key>"
```

### Response
```json
{
  "revoked": true,
  "api_key": "cope_abc123def456ghi789"
}
```

**⚠️ Warning**: This action cannot be undone. You'll need to register for a new API key.

## Understanding limits

### Free tier (x402_enabled: false)
- **Daily calls**: 250 (resets at midnight UTC)
- **Watchlists**: 1 maximum
- **Handles per watchlist**: 10 maximum
- **Rate limit**: 10 requests per minute

### Upgraded (x402_enabled: true)
- **Daily calls**: First 250 free, then $0.005 per call
- **Watchlists**: 10 maximum
- **Handles per watchlist**: 100 maximum
- **Rate limit**: 300 requests per minute

## Account lifecycle

1. **Register** → Get API key with free tier limits
2. **Optional**: Set payment_address → Upgrade to unlimited access
3. **Use APIs** → Track usage and payments
4. **Optional**: Update payment settings anytime
5. **Optional**: Revoke key when done

## Example: Complete account setup

```bash
#!/bin/bash
API_KEY="cope_your_api_key"

echo "1. Checking current account status..."
curl -X GET https://api.cope.capital/v1/account \
  -H "Authorization: Bearer $API_KEY"

echo "2. Enabling x402 payments..."
curl -X PATCH https://api.cope.capital/v1/account \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"payment_address": "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD18"}'

echo "3. Verifying upgraded limits..."
curl -X GET https://api.cope.capital/v1/account \
  -H "Authorization: Bearer $API_KEY"
```

## Notes

- All account management endpoints are **FREE** and don't count toward daily limits
- Payment address changes take effect immediately
- Usage statistics are updated in real-time
- Payment history includes all x402 transactions with full blockchain details
- API key revocation is permanent and immediate