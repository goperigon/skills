# Topics & Categories — `/v1/topics/all`

Browse and search the topics taxonomy used by Perigon to classify news articles. Topics are more granular than categories and are used as filters across all article-related endpoints.

## Endpoint

```
GET https://api.goperigon.com/v1/topics/all
```

## Parameters

| Parameter | Type | Description |
|---|---|---|
| `name` | string | Search for topics by name (exact or partial match, no wildcards). |
| `category` | string | Filter topics by parent category (e.g., `Tech`, `Politics`, `Sports`). |
| `subcategory` | string | Filter topics by subcategory (more granular than category). |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |

## Response Structure

```json
{
  "total": 350,
  "data": [
    {
      "id": 42,
      "name": "Artificial Intelligence",
      "labels": {
        "category": "Tech",
        "subcategory": "Innovation"
      },
      "createdAt": "2023-01-15T10:00:00Z",
      "updatedAt": "2026-01-20T08:00:00Z"
    },
    {
      "id": 15,
      "name": "Cryptocurrency",
      "labels": {
        "category": "Finance",
        "subcategory": "Digital Assets"
      },
      "createdAt": "2023-01-15T10:00:00Z",
      "updatedAt": "2026-01-20T08:00:00Z"
    }
  ]
}
```

Note: The topics endpoint uses `total` and `data` instead of the `numResults` and `results`/`articles` pattern used by other endpoints.

## Example Requests

### Browse all topics

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/topics/all?size=100"
```

### Search for topics by name

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/topics/all?name=Climate&size=20"
```

### Filter topics by category

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/topics/all?category=Sports&size=50"
```

---

## Categories vs. Topics

| Concept | Description | Examples |
|---|---|---|
| **Category** | Broad thematic classification | Tech, Politics, Sports, Business, Finance, Entertainment, Health, Science, World, Lifestyle |
| **Topic** | Granular subject within a category | Artificial Intelligence, Elections, Cryptocurrency, Climate Change, College Sports, Markets |
| **Taxonomy** | Google Content Categories (hierarchical) | `/Finance/Banking/Other`, `/Technology/AI` |

### How They Relate

- Articles have **1+ categories** and **1+ topics**.
- Topics belong to categories (e.g., "Cryptocurrency" → "Finance").
- Use `category` for broad filtering, `topic` for targeted filtering.
- Use `taxonomy` for Google Content Categories (hierarchical, more specific).
- Use `prefixTaxonomy` to match all taxonomy paths starting with a prefix.

### Common Categories

| Category | Example Topics |
|---|---|
| Tech | Artificial Intelligence, Cybersecurity, Social Media, Startups, Semiconductors |
| Politics | Elections, Congress, Foreign Policy, Supreme Court |
| Business | Markets, IPOs, Mergers & Acquisitions, Earnings |
| Finance | Cryptocurrency, Banking, Investing, Personal Finance |
| Sports | NFL, NBA, MLB, Soccer, College Sports, Olympics |
| Health | COVID-19, Mental Health, Pharmaceuticals, Public Health |
| Science | Space, Climate Change, Biology, Physics |
| Entertainment | Movies, Music, Streaming, Celebrity, Gaming |
| World | Geopolitics, Humanitarian, Trade, Diplomacy |
| Lifestyle | Travel, Food, Real Estate, Fashion |

### Usage in Article Filters

```bash
# Filter articles by category
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?category=Tech&size=20"

# Filter articles by topic
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?topic=Artificial+Intelligence&size=20"

# Filter by Google Content Taxonomy
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?taxonomy=/Finance/Banking&size=20"

# Filter by taxonomy prefix
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?prefixTaxonomy=/Finance&size=20"

# Exclude specific categories
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?q=Apple&excludeCategory=Tech&size=20"

# Search uncategorized articles
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/articles/all?category=none&size=20"
```

## When to Use This Endpoint

- **Discovery** — Browse available topics before using them as filters.
- **Autocomplete** — Power topic suggestion UI in search interfaces.
- **Taxonomy mapping** — Map your application's categories to Perigon topics.
- **Monitoring setup** — Identify the right topics for news monitoring workflows.
