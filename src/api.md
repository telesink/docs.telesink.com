# The API

Telesink's API is deliberately **dead simple** — exactly what you'd expect from
a lightweight, no-bloat product.

You don't need a heavy SDK or complicated setup. Just send one `POST` request
with a tiny JSON payload and your event appears instantly in the live dashboard.

## Endpoint

```
POST /api/v1/sinks/{your_sink_token}/events
```

- **Hosted version**: `https://app.telesink.com/api/v1/sinks/{your_sink_token}/events`
- **Self-hosted**: `https://your-telesink-domain.com/api/v1/sinks/{your_sink_token}/events`

You can find your sink token in the Telesink dashboard (when creating a new sink
or editing).

No API key required. The sink token in the URL is your only credential.

## Request

**Headers**

```
Content-Type: application/json
```

**Body** (JSON)

Only two fields are required:

| Field   | Type   | Required | Description                                                  |
| ------- | ------ | -------- | ------------------------------------------------------------ |
| `event` | string | Yes      | Event name/type (e.g. `User signed up`, `Payment succeeded`) |
| `text`  | string | Yes      | Human-readable text shown in the dashboard                   |

Optional fields (all very useful):

| Field             | Type   | Description                                                        |
| ----------------- | ------ | ------------------------------------------------------------------ |
| `emoji`           | string | Emoji to display with the event (e.g. `🚀`, `💰`, `✅`)            |
| `properties`      | object | Any additional structured data                                     |
| `occurred_at`     | string | ISO 8601 timestamp (e.g. `2024-01-15T10:30:00Z`). Defaults to now. |
| `idempotency_key` | string | Unique key to prevent duplicate events                             |

## Example Requests

### cURL (works from anywhere)

```bash
curl -X POST https://app.telesink.com/api/v1/sinks/abc123xyz/events \
  -H "Content-Type: application/json" \
  -d '{
    "event": "User signed up",
    "text": "kyrylo@telesink.com",
    "emoji": "✨",
    "properties": {
      "user_id": 452,
      "plan": "pro"
    }
  }'
```

### Ruby SDK (recommended for Ruby/Rails)

```ruby
Telesink.track(
  event: "User signed up",
  text: "kyrylo@telesink.com",
  emoji: "✨",
  properties: { user_id: 452, plan: "pro" }
)
```

## Response

- **Success**: `201 Created` (empty body)
- **Validation error** (missing `event` or `text`): `422 Unprocessable Entity`

```json
{
  "errors": ["Event can't be blank", "Text can't be blank"]
}
```

- **Invalid sink token**: `401 Unauthorized`

That's literally it.

The official SDKs (Ruby, JavaScript, PHP) wrap this exact API and add
conveniences like configuration via environment variables and silent error
handling — but the underlying API is always this simple.

You can send events from anywhere: your backend, frontend, cron jobs, webhooks,
background workers, or even a one-liner script.
