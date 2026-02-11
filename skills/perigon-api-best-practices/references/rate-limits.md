# Rate Limits

## Overview

The Perigon API applies rate limits per API key based on your subscription plan. When you exceed the rate limit, the API returns a `429 Too Many Requests` response.

## Handling Rate Limits

### Detecting Rate Limits

```
HTTP/1.1 429 Too Many Requests
```

Check for HTTP status `429` in your code and implement retry logic.

### Retry with Exponential Backoff

```python
import time
import requests

def make_request(url, headers, params, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers, params=params)
        
        if response.status_code == 200:
            return response.json()
        
        if response.status_code == 429:
            # Check Retry-After header if present
            retry_after = response.headers.get("Retry-After")
            if retry_after:
                wait_time = int(retry_after)
            else:
                wait_time = min(2 ** attempt, 60)
            
            print(f"Rate limited. Waiting {wait_time}s...")
            time.sleep(wait_time)
            continue
        
        response.raise_for_status()
    
    raise Exception("Max retries exceeded due to rate limiting")
```

## Best Practices

### 1. Batch Requests Efficiently

Instead of making many small requests, use larger `size` values:

```bash
# ❌ 10 requests for 10 articles each
page=0&size=10
page=1&size=10
...

# ✅ 1 request for 100 articles
page=0&size=100
```

### 2. Cache Responses

Entity data (people, companies, sources, topics) changes infrequently. Cache these responses:

```python
import functools
import time

@functools.lru_cache(maxsize=1000)
def get_company(symbol):
    """Cache company lookups for the session."""
    return call_perigon(
        "https://api.perigon.io/v1/companies/all",
        headers={"x-api-key": api_key},
        params={"symbol": symbol, "size": 1}
    )
```

### 3. Use Targeted Filters

Narrow your queries to reduce unnecessary requests:

```bash
# ❌ Broad search, then filter client-side
q=*&size=1000

# ✅ Targeted search with server-side filters
q=AI&category=Tech&sourceGroup=top100&from=2026-02-01&size=50
```

### 4. Use Webhooks or Polling Intervals

For monitoring use cases, avoid polling too frequently:

- **Breaking news:** Poll every 1–5 minutes.
- **Daily digests:** Poll once per day.
- **Trend tracking:** Poll every 15–60 minutes.

### 5. Parallelize Carefully

When making concurrent requests, add a small delay between them:

```python
import asyncio
import aiohttp

async def fetch_with_rate_limit(session, url, headers, params, semaphore):
    async with semaphore:
        async with session.get(url, headers=headers, params=params) as response:
            if response.status == 429:
                await asyncio.sleep(2)
                return await fetch_with_rate_limit(session, url, headers, params, semaphore)
            return await response.json()

async def main():
    semaphore = asyncio.Semaphore(5)  # Max 5 concurrent requests
    async with aiohttp.ClientSession() as session:
        tasks = [
            fetch_with_rate_limit(session, url, headers, params, semaphore)
            for params in param_list
        ]
        results = await asyncio.gather(*tasks)
```

### 6. Monitor Your Usage

Keep track of your API call volume to stay within limits. Log request counts and implement alerts before hitting thresholds.

## Quick Reference

| Strategy | When to Use |
|---|---|
| Exponential backoff | Always — default retry strategy for 429 |
| Response caching | Entity lookups (people, companies, sources, topics) |
| Larger page sizes | Data collection and bulk operations |
| Targeted filters | Always — reduces response size and server load |
| Request throttling | High-volume concurrent operations |
| Polling intervals | Monitoring and alerting use cases |
