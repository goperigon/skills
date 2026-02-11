# Authentication

## API Key

All Perigon API requests require authentication via an API key. Sign up at [perigon.io](https://www.perigon.io/) to get your key.

## Passing the API Key

### Option 1: HTTP Header (Recommended)

```bash
curl -H "x-api-key: YOUR_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=AI"
```

### Option 2: Query Parameter

```bash
curl "https://api.goperigon.com/v1/articles/all?apiKey=YOUR_API_KEY&q=AI"
```

**Prefer the header method** — it keeps your API key out of URLs, server logs, and browser history.

## Environment Variable Setup

Store your key in an environment variable rather than hardcoding it:

```bash
# .env or shell profile
export PERIGON_API_KEY="your-api-key-here"
```

### Python

```python
import os
import requests

api_key = os.environ["PERIGON_API_KEY"]

response = requests.get(
    "https://api.goperigon.com/v1/articles/all",
    headers={"x-api-key": api_key},
    params={"q": "artificial intelligence", "size": 10}
)
data = response.json()
```

### JavaScript / Node.js

```javascript
const apiKey = process.env.PERIGON_API_KEY;

const response = await fetch(
  "https://api.goperigon.com/v1/articles/all?q=artificial+intelligence&size=10",
  { headers: { "x-api-key": apiKey } }
);
const data = await response.json();
```

### curl

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=AI&size=10"
```

## Security Best Practices

1. **Never commit API keys to version control.** Add `.env` to `.gitignore`.
2. **Use environment variables** or a secrets manager in production.
3. **Rotate keys** if they are accidentally exposed.
4. **Use server-side requests only.** Never embed API keys in client-side JavaScript shipped to browsers.
5. **Restrict key scope** if Perigon provides scoped keys for your plan.
