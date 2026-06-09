# Source: https://docs.bluumfinance.com/platform/webhooks

Webhooks deliver real-time notifications to your server when events occur in Bluum — orders filling, deposits completing, KYC decisions, and more. Use webhooks instead of polling to build responsive integrations.

## 

[​](https://docs.bluumfinance.com/#how-webhooks-work)

How webhooks work

```
Event occurs in Bluum → Bluum sends POST to your endpoint → Your server responds 2xx
                                                                    │
                                                              (retry on failure)
```

1. Register a webhook endpoint URL and subscribe to event types
2. When an event occurs, Bluum sends a `POST` request with the event payload
3. Your server processes the event and responds with a `2xx` status code
4. If delivery fails, Bluum retries with exponential backoff

## 

[​](https://docs.bluumfinance.com/#event-types)

Event types

Events follow the naming convention `{domain}.{action}`:

| Event | Description |
| --- | --- |
| `account.created` | New account created |
| `account.updated` | Account details changed |
| `order.accepted` | Order accepted for execution |
| `order.filled` | Order fully filled |
| `order.partially_filled` | Order partially filled |
| `order.canceled` | Order canceled |
| `order.rejected` | Order rejected |
| `transfer.deposit.initiated` | Deposit initiated |
| `transfer.deposit.completed` | Deposit completed, funds available |
| `transfer.deposit.failed` | Deposit failed |
| `transfer.withdrawal.initiated` | Withdrawal initiated |
| `transfer.withdrawal.completed` | Withdrawal completed |
| `transfer.withdrawal.failed` | Withdrawal failed |
| `document.approved` | KYC document approved |
| `document.rejected` | KYC document rejected |

### 

[​](https://docs.bluumfinance.com/#wildcards)

Wildcards

Subscribe to all events in a domain with `*`:

- `order.*` — All order events
- `transfer.*` — All transfer events

### 

[​](https://docs.bluumfinance.com/#list-available-event-types)

List available event types

```
curl -X GET "$BASE_URL/webhooks/event-types" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#registering-a-webhook)

Registering a webhook

```
curl -X POST "$BASE_URL/webhooks" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Production webhook",
    "url": "https://your-app.com/webhooks/bluum",
    "eventTypeNames": ["order.*", "transfer.*"]
  }'
```

## 

[​](https://docs.bluumfinance.com/#managing-webhooks)

Managing webhooks

```
# List webhooks
curl -X GET "$BASE_URL/webhooks" \
  -H "Authorization: Basic $AUTH"

# Update a webhook
curl -X PATCH "$BASE_URL/webhooks/$WEBHOOK_ID" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "eventTypeNames": ["order.*", "transfer.*", "account.*"]
  }'

# Delete a webhook
curl -X DELETE "$BASE_URL/webhooks/$WEBHOOK_ID" \
  -H "Authorization: Basic $AUTH"
```

## 

[​](https://docs.bluumfinance.com/#webhook-payload-format)

Webhook payload format

```
{
  "event": "order.filled",
  "timestamp": "2025-06-15T14:30:15Z",
  "data": {
    "id": "ord_x9y8z7a6b5c4d3e2",
    "investor_id": "inv_01j9x8m2k7qpzwv3t5r6y8n0ab",
    "symbol": "AAPL",
    "side": "buy",
    "status": "filled",
    "filled_quantity": "10",
    "average_price": "178.50"
  }
}
```

## 

[​](https://docs.bluumfinance.com/#retry-behavior)

Retry behavior

If your endpoint doesn’t respond with `2xx` within 30 seconds, Bluum retries with exponential backoff:

| Attempt | Delay |
| --- | --- |
| 1st retry | ~1 minute |
| 2nd retry | ~5 minutes |
| 3rd retry | ~30 minutes |
| 4th retry | ~2 hours |
| 5th retry | ~8 hours |

After all retries are exhausted, the event is sent to a dead letter queue. Contact support to replay failed events.

## 

[​](https://docs.bluumfinance.com/#best-practices)

Best practices

- **Respond quickly** — Return `2xx` within 30 seconds. Process the event asynchronously if your logic takes longer.
- **Handle duplicates** — The same event may be delivered more than once. Use the event ID for deduplication.
- **Verify signatures** — Validate the webhook signature header to ensure the request came from Bluum.
- **Use HTTPS** — Webhook endpoints must use HTTPS in production.
- **Monitor failures** — Set up alerts if your endpoint starts returning errors.

## 

[​](https://docs.bluumfinance.com/#testing-webhooks)

Testing webhooks

In sandbox, webhooks fire exactly like production. Use these tools for local development:

- [webhook.site](https://webhook.site) — Inspect payloads without a server
- [ngrok](https://ngrok.com) — Expose your local server to the internet

## 

[​](https://docs.bluumfinance.com/#key-endpoints)

Key endpoints

| Method | Path | Description |
| --- | --- | --- |
| `GET` | `/webhooks/event-types` | List available event types |
| `POST` | `/webhooks` | Register a webhook |
| `GET` | `/webhooks` | List webhooks |
| `PATCH` | `/webhooks/{webhook_id}` | Update a webhook |
| `DELETE` | `/webhooks/{webhook_id}` | Delete a webhook |

[Positions](https://docs.bluumfinance.com/platform/positions) [Error Handling](https://docs.bluumfinance.com/platform/errors)

⌘I