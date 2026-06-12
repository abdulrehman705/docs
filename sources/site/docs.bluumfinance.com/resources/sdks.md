# Source: https://docs.bluumfinance.com/resources/sdks

Official Bluum SDKs are in development. In the meantime, the API is accessible via standard HTTP from any language.

## 

[​](https://docs.bluumfinance.com/#using-the-api-directly)

Using the API directly

The Bluum API uses REST conventions with JSON payloads. Any HTTP client works:

| Language | Recommended client |
| --- | --- |
| JavaScript/Node.js | `fetch` (built-in), `axios` |
| Python | `requests`, `httpx` |
| Ruby | `net/http`, `faraday` |
| Go | `net/http` |
| Java | `HttpClient` (Java 11+), `OkHttp` |
| PHP | `Guzzle`, `cURL` |

## 

[​](https://docs.bluumfinance.com/#authentication-helper)

Authentication helper

All requests use HTTP Basic Auth. Here’s a minimal helper:

Node.js

Python

```
class BluumClient {
  constructor(apiKey, apiSecret, baseUrl = 'https://dev-tapp-api.tappengine.com/v1') {
    this.baseUrl = baseUrl;
    this.auth = 'Basic ' + Buffer.from(`${apiKey}:${apiSecret}`).toString('base64');
  }

  async request(method, path, body) {
    const response = await fetch(`${this.baseUrl}${path}`, {
      method,
      headers: {
        'Authorization': this.auth,
        'Content-Type': 'application/json'
      },
      body: body ? JSON.stringify(body) : undefined
    });
    if (!response.ok) throw new Error(`${response.status}: ${await response.text()}`);
    return response.json();
  }

  getAccounts() { return this.request('GET', '/accounts'); }
  createAccount(data) { return this.request('POST', '/accounts', data); }
  getAssets(params) { return this.request('GET', `/assets?${new URLSearchParams(params)}`); }
  placeOrder(accountId, order) { return this.request('POST', `/investors/${investorId}/orders`, order); }
}
```

## 

[​](https://docs.bluumfinance.com/#openapi-specification)

OpenAPI specification

The full OpenAPI 3.0 spec is available for code generation:

- **Bundled spec:** Available at `/api-reference/openapi.yaml` in the docs
- **Tools:** Use [openapi-generator](https://openapi-generator.tech/) or [swagger-codegen](https://swagger.io/tools/swagger-codegen/) to generate typed clients in any language

## 

[​](https://docs.bluumfinance.com/#coming-soon)

Coming soon

- Official Node.js SDK (`@bluum-finance/sdk`)
- Official Python SDK (`bluum-finance`)
- Webhook signature verification libraries

Contact [developer support](mailto:ope@bluumfinance.com) if you need SDK assistance.

[Postman Collection](https://docs.bluumfinance.com/resources/postman)

⌘I