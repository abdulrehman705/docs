# Source: https://docs.bluumfinance.com/guides/operations/webhook-setup

This guide walks through setting up webhooks from scratch — registration, local testing, event handling, and production best practices.

## 

[​](https://docs.bluumfinance.com/#step-1-create-a-webhook-endpoint)

Step 1: Create a webhook endpoint

Build an HTTP endpoint that accepts POST requests:

```
// Express.js example
app.post('/webhooks/bluum', (req, res) => {
  const event = req.body;

  console.log(`Received event: ${event.event}`, event.data);

  // Process the event asynchronously
  processEvent(event).catch(console.error);

  // Respond immediately with 200
  res.status(200).json({ received: true });
});
```

```
# Flask example
@app.route('/webhooks/bluum', methods=['POST'])
def handle_webhook():
    event = request.get_json()

    print(f"Received event: {event['event']}", event['data'])

    # Process asynchronously
    process_event.delay(event)

    return jsonify(received=True), 200
```

## 

[​](https://docs.bluumfinance.com/#step-2-test-locally)

Step 2: Test locally

Use ngrok to expose your local server:

```
ngrok http 3000
# Forwarding: https://abc123.ngrok.io → http://localhost:3000
```

Or use webhook.site for quick inspection without a server.

## 

[​](https://docs.bluumfinance.com/#step-3-register-with-bluum)

Step 3: Register with Bluum

```
curl -X POST "$BASE_URL/webhooks" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Development webhook",
    "url": "https://abc123.ngrok.io/webhooks/bluum",
    "eventTypeNames": ["order.*", "transfer.*"]
  }'
```

## 

[​](https://docs.bluumfinance.com/#step-4-trigger-test-events)

Step 4: Trigger test events

Create an order or deposit in sandbox to generate events:

```
# This will trigger order.accepted and eventually order.filled
curl -X POST "$BASE_URL/investors/$INVESTOR_ID/orders" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "AAPL",
    "side": "buy",
    "type": "market",
    "time_in_force": "day",
    "quantity": "1"
  }'
```

Check your endpoint logs to verify events are being received.

## 

[​](https://docs.bluumfinance.com/#event-handling-patterns)

Event handling patterns

### 

[​](https://docs.bluumfinance.com/#idempotent-processing)

Idempotent processing

The same event may be delivered more than once. Use the event ID for deduplication:

```
const processedEvents = new Set(); // Use a database in production

app.post('/webhooks/bluum', (req, res) => {
  const { event, data } = req.body;
  const eventId = data.id;

  if (processedEvents.has(eventId)) {
    return res.status(200).json({ received: true }); // Already processed
  }

  processedEvents.add(eventId);
  handleEvent(event, data);

  res.status(200).json({ received: true });
});
```

### 

[​](https://docs.bluumfinance.com/#event-routing)

Event routing

```
function handleEvent(eventName, data) {
  switch (eventName) {
    case 'order.filled':
      notifyUser(data.account_id, `Order filled: ${data.symbol}`);
      break;
    case 'transfer.deposit.completed':
      enableTrading(data.account_id);
      break;
    case 'transfer.deposit.failed':
      alertUser(data.account_id, 'Deposit failed');
      break;
    case 'document.approved':
      proceedWithOnboarding(data.account_id);
      break;
  }
}
```

## 

[​](https://docs.bluumfinance.com/#production-checklist)

Production checklist

- [ ] Endpoint uses HTTPS
- [ ] Responds with `2xx` within 30 seconds
- [ ] Processes events asynchronously (don’t block the response)
- [ ] Handles duplicate events idempotently
- [ ] Verifies webhook signatures
- [ ] Monitors for delivery failures
- [ ] Logs all received events for debugging
- [ ] Update the webhook URL from ngrok/dev to your production domain

## 

[​](https://docs.bluumfinance.com/#updating-for-production)

Updating for production

```
curl -X PATCH "$BASE_URL/webhooks/$WEBHOOK_ID" \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://your-app.com/webhooks/bluum",
    "description": "Production webhook"
  }'
```

[Tracking Positions](https://docs.bluumfinance.com/guides/self-directed/positions-portfolio) [Working with Market Data](https://docs.bluumfinance.com/guides/operations/market-data)

⌘I