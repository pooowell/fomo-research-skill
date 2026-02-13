# Cope Capital API Reference

Full interactive docs: https://api.cope.capital/docs

## Base URL

```
https://api.cope.capital
```

## Authentication

All endpoints (except POST /v1/register) require:
```
Authorization: Bearer cope_YOUR_KEY
```

## Response Formats

### Activity Item
```json
{
  "fomo_handle": "frankdegods",
  "wallet": "A5SEXYJY4jTEi6sjMLfZs5KAP8SVFvLDPDV67GgSSZSk",
  "chain": "solana",
  "action": "buy",
  "token_mint": "DezX7iJ4W8VqRXPpWLNq6YYr5ky2nrSR1GvSa8L7pump",
  "token_symbol": "BONK",
  "usd_amount": 2400.50,
  "timestamp": 1707603400
}
```

### Leaderboard Entry
```json
{
  "handle": "frankdegods",
  "display_name": "[PN] frank",
  "solana_address": "A5SEXY...",
  "base_address": "0x542b6b...",
  "pnl": 295066.84,
  "num_trades": 1404,
  "swap_count": 3686,
  "total_volume": 28357527.58,
  "followers": 73288,
  "total_holdings": 14
}
```

### Watchlist Wallet
```json
{
  "handle": "frankdegods",
  "solana": "A5SEXY...",
  "base": "0x542b6b...",
  "status": "active",
  "tracked_since": "2026-01-15"
}
```

## Query Parameters

### GET /v1/activity
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| watchlist_id | string | — | Filter to specific watchlist |
| handle | string | — | Filter to specific Fomo handle |
| chain | string | all | `solana`, `base`, or `all` |
| action | string | all | `buy`, `sell`, or `all` |
| min_usd | number | — | Minimum trade size in USD |
| since | number | — | Unix ms timestamp |
| limit | number | 50 | Max results (1-100) |
| cursor | string | — | Pagination cursor |

### GET /v1/activity/poll
Same filters as /v1/activity. Returns `{ count, latest_at }` only.

### GET /v1/leaderboard
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| timeframe | string | 7d | `24h`, `7d`, `30d`, or `all` |
| limit | number | 50 | Max results (1-100) |

### GET /v1/trending/handles
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| limit | number | 20 | Max results (1-100) |

## Rate Limits

| Tier | Rate | Daily Activity Calls |
|------|------|---------------------|
| Free | 10/min | 250 |
| x402 | 300/min | Unlimited ($0.005/call) |

## Chains

Only `solana` and `base` are supported. Invalid chain values return 400.

## Actions

Only `buy` and `sell` are supported. Invalid action values return 400.
