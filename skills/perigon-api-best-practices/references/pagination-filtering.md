# Pagination & Filtering Patterns

Common patterns for querying the Perigon API efficiently.

## Pagination

### Basic Pagination

All list endpoints support `page` and `size` parameters:

```bash
# Page 0, 10 results (default)
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=AI&page=0&size=10"

# Page 1, 20 results
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=AI&page=1&size=20"
```

- `page` starts at **0** (not 1).
- `size` controls results per page.
- Default size varies by endpoint; explicitly set it for consistency.

### Getting Total Counts

By default, `numResults` counts up to 10,000 for performance. Set `showNumResults=true` for exact counts:

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=climate+change&showNumResults=true&size=1"
```

This is slightly slower but gives the true total. Use it when you need to display total counts or calculate total pages.

### Iterating All Pages

```python
import os
import requests

api_key = os.environ["PERIGON_API_KEY"]
base_url = "https://api.goperigon.com/v1/articles/all"

all_articles = []
page = 0
size = 100

while True:
    response = requests.get(base_url, headers={"x-api-key": api_key}, params={
        "q": "climate change",
        "from": "2026-01-01",
        "page": page,
        "size": size,
        "sortBy": "date"
    })
    data = response.json()
    articles = data.get("articles", [])
    
    if not articles:
        break
    
    all_articles.extend(articles)
    page += 1

print(f"Collected {len(all_articles)} articles")
```

## Sorting

### Articles & Stories

| Value | Description |
|---|---|
| `relevance` | Default. Best match to query. |
| `date` / `pubDate` | Newest publication date first. |
| `reverseDate` | Oldest publication date first. |
| `addDate` | Newest ingestion date first. |
| `reverseAddDate` | Oldest ingestion date first. |
| `refreshDate` | Most recently updated first. |

### Wikipedia

| Value | Description |
|---|---|
| `relevance` | Default. Best match to query. |
| `revisionTsDesc` | Recently edited first. |
| `revisionTsAsc` | Recently edited last. |
| `pageViewsDesc` | Highest viewership first. |
| `pageViewsAsc` | Highest viewership last. |
| `scrapedAtDesc` | Recently scraped first. |
| `scrapedAtAsc` | Recently scraped last. |

### Sources

| Value | Description |
|---|---|
| `relevance` | Default. Best match to query. |
| `createdAt` | Most recently created first. |
| `updatedAt` | Most recently updated first. |
| `count` | By article count. |
| `totalCount` | By total article count. |

## Common Filter Patterns

### Boolean Search Syntax

The `q`, `title`, `desc`, `content`, and `summary` parameters support:

```
# AND (both terms required)
q=artificial AND intelligence

# OR (either term)
q=Tesla OR Rivian

# NOT (exclude term)
q=Apple NOT fruit

# Exact phrase
q="electric vehicles"

# Wildcards
q=crypto*        # matches cryptocurrency, cryptography, etc.
q=trad?          # matches trade, trader, etc.

# Complex combinations
q="climate change" AND (policy OR legislation) NOT opinion
```

### Date Range Patterns

```bash
# Last 24 hours
from=2026-02-10T00:00:00

# Last 7 days
from=2026-02-04&to=2026-02-11

# Specific month
from=2026-01-01&to=2026-01-31

# ISO 8601 with time
from=2026-02-10T08:00:00&to=2026-02-10T20:00:00
```

### Combining Filters

Filters combine with AND logic (narrowing results):

```bash
# Tech articles from top sources in English about AI, excluding opinions
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?\
q=artificial+intelligence&\
category=Tech&\
sourceGroup=top100&\
language=en&\
excludeLabel=Opinion&\
from=2026-02-01&\
sortBy=date&\
size=20"
```

### Array Filters (OR within, AND across)

Multiple values in the same filter use OR logic:

```bash
# Articles in Tech OR Business category
category=Tech,Business

# Articles from nytimes.com OR washingtonpost.com
source=nytimes.com,washingtonpost.com
```

Different filter types combine with AND:

```bash
# Tech articles from top100 sources (both must match)
category=Tech&sourceGroup=top100
```

### Reprint Handling

Wire-service articles (AP, Reuters) are reprinted on many sites:

```bash
# Exclude reprints for unique content
showReprints=false

# Get all versions of a specific article
reprintGroupId=REPRINT_GROUP_ID
```

### Geo-Based Filtering

```bash
# Articles about places near coordinates (article content mentions)
lat=37.7749&lon=-122.4194&maxDistance=50

# Articles from local publications near coordinates
sourceLat=37.7749&sourceLon=-122.4194&sourceMaxDistance=50
```

## Performance Tips

1. **Use `size` judiciously** — smaller pages are faster. Use 10-20 for interactive UIs, up to 100 for data collection.
2. **Avoid `showNumResults=true`** unless you need exact counts — it adds latency.
3. **Use `sortBy=date`** with date filters for chronological browsing.
4. **Use `sortBy=relevance`** (default) when using `q` for search.
5. **Add `from` date filters** to narrow the search window and improve speed.
6. **Use `sourceGroup`** filters to restrict to quality sources when precision matters.
7. **Paginate with consistent `sortBy`** to avoid duplicates across pages.
