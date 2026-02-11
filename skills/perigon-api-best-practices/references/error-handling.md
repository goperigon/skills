# Error Handling

## HTTP Status Codes

| Status | Meaning | Action |
|---|---|---|
| `200` | Success | Process the response. |
| `400` | Bad Request | Invalid parameters. Check your query syntax, parameter names, and values. |
| `401` | Unauthorized | Invalid or missing API key. Verify your `apiKey` parameter or `x-api-key` header. |
| `403` | Forbidden | Your API key doesn't have access to this endpoint or feature. Check your plan. |
| `404` | Not Found | The endpoint doesn't exist or the resource ID is invalid. |
| `429` | Too Many Requests | Rate limit exceeded. Implement backoff and retry (see `rate-limits.md`). |
| `500` | Internal Server Error | Server-side issue. Retry with exponential backoff. |
| `502` | Bad Gateway | Temporary infrastructure issue. Retry after a short delay. |
| `503` | Service Unavailable | The service is temporarily down. Retry with exponential backoff. |

## Error Response Format

```json
{
  "status": 400,
  "error": "Bad Request",
  "message": "Invalid value for parameter 'from': expected ISO 8601 date format"
}
```

## Retry Strategy

### Exponential Backoff

For `429`, `500`, `502`, and `503` errors, implement exponential backoff:

```python
import time
import requests

def call_perigon(url, headers, params, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers, params=params)
        
        if response.status_code == 200:
            return response.json()
        
        if response.status_code in (429, 500, 502, 503):
            wait_time = min(2 ** attempt, 60)  # 1, 2, 4, 8, 16... max 60s
            print(f"Retry {attempt + 1}/{max_retries} after {wait_time}s (HTTP {response.status_code})")
            time.sleep(wait_time)
            continue
        
        # Non-retryable error
        response.raise_for_status()
    
    raise Exception(f"Max retries ({max_retries}) exceeded")
```

### JavaScript

```javascript
async function callPerigon(url, headers, params, maxRetries = 5) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    const queryString = new URLSearchParams(params).toString();
    const response = await fetch(`${url}?${queryString}`, { headers });
    
    if (response.ok) {
      return await response.json();
    }
    
    if ([429, 500, 502, 503].includes(response.status)) {
      const waitTime = Math.min(2 ** attempt * 1000, 60000);
      console.log(`Retry ${attempt + 1}/${maxRetries} after ${waitTime}ms (HTTP ${response.status})`);
      await new Promise(resolve => setTimeout(resolve, waitTime));
      continue;
    }
    
    throw new Error(`HTTP ${response.status}: ${await response.text()}`);
  }
  
  throw new Error(`Max retries (${maxRetries}) exceeded`);
}
```

## Common Errors and Fixes

### Invalid Date Format

```
❌ from=02-10-2026
✅ from=2026-02-10
✅ from=2026-02-10T00:00:00
```

### Invalid Boolean Values

```
❌ showReprints=yes
✅ showReprints=true
✅ showReprints=false
```

### Unrecognized Parameters

Parameters are case-sensitive:

```
❌ Sort_By=date
✅ sortBy=date
```

### Missing API Key

```
❌ (no key)
✅ Header: x-api-key: YOUR_KEY
✅ Query: ?apiKey=YOUR_KEY
```

### Invalid Sort Values

```
❌ sortBy=newest
✅ sortBy=date
```

### Empty Results

If you get zero results:
1. Broaden your date range (`from`/`to`).
2. Remove or relax filters one at a time.
3. Check spelling of entity names.
4. Try broader categories or topics.
5. Use `showNumResults=true` to confirm the total count.

## Validation Checklist

Before making a request, verify:

- [ ] API key is set (env var or header)
- [ ] Dates are in ISO 8601 or `yyyy-mm-dd` format
- [ ] Parameter names are camelCase and spelled correctly
- [ ] Boolean values are `true`/`false`
- [ ] Array values are comma-separated (query params) or JSON arrays (POST body)
- [ ] `page` starts at 0 (not 1)
- [ ] `sortBy` value is one of the documented options
