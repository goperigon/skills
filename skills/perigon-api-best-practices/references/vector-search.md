# Vector Search — `/v1/vector/news/all` & `/v1/vector/wikipedia/all`

Perform natural language searches using semantic relevance. Unlike keyword search, vector search understands intent and meaning, returning results that are conceptually related to your query even without exact keyword matches.

## Endpoints

| Endpoint | Method | Corpus | Coverage |
|---|---|---|---|
| `/v1/vector/news/all` | POST | News articles | Last 6 months |
| `/v1/vector/wikipedia/all` | POST | Wikipedia pages | Full Wikipedia (English) |

---

## Vector News Search

### Endpoint

```
POST https://api.goperigon.com/v1/vector/news/all
```

### Body Parameters (JSON)

| Parameter | Type | Required | Description |
|---|---|---|---|
| `prompt` | string | **Yes** | Natural language query to search the news database. |
| `page` | integer | No | Page number to retrieve. |
| `size` | integer | No | Number of results per page. |
| `pubDateFrom` | date | No | Articles published after this date (ISO 8601 or `yyyy-mm-dd`). Default: last 30 days. |
| `pubDateTo` | date | No | Articles published before this date. |
| `showReprints` | boolean | No | Include wire-service reprints (default `true`). |
| `filter` | object | No | Structured filter with nested `AND`/`OR`/`NOT` logic (see below). |

### Filter Object

The `filter` parameter supports complex Boolean logic for structured filtering:

```json
{
  "filter": {
    "AND": [
      { "category": ["Tech"] },
      { "source": ["nytimes.com", "bbc.com"] }
    ],
    "NOT": [
      { "label": ["Opinion"] }
    ]
  }
}
```

**Available filter fields:** `articleId`, `category`, `city`, `clusterId`, `companyDomain`, `companyId`, `companyName`, `companySymbol`, `country`, `county`, `label`, `language`, `locationsCountry`, `personName`, `personWikidataId`, `source`, `sourceCity`, `sourceCountry`, `sourceCounty`, `sourceGroup`, `sourceState`, `state`, `taxonomy`, `topic`

Each filter field also has an `exclude` variant: `excludeCity`, `excludeCompanyDomain`, `excludeCompanyId`, `excludeCompanySymbol`, `excludeCountry`, `excludeCounty`, `excludeLabel`, `excludeLanguage`, `excludeLocationsCountry`, `excludePersonName`, `excludePersonWikidataId`, `excludeSource`, `excludeState`, `excludeTopic`

**Coordinate filters:** `coordinates` and `sourceCoordinates` objects with `lat`, `lon`, `radius` for geo-based filtering.

### Response

```json
{
  "status": 200,
  "results": [
    {
      "score": 0.92,
      "data": {
        "articleId": "...",
        "title": "...",
        "description": "...",
        "content": "...",
        "summary": "...",
        "url": "...",
        "pubDate": "...",
        "source": { "domain": "..." },
        "sentiment": { "positive": 0.7, "negative": 0.1, "neutral": 0.2 },
        "categories": [...],
        "topics": [...],
        "people": [...],
        "companies": [...]
      }
    }
  ]
}
```

Each result includes a `score` (0.0–1.0) indicating semantic similarity, and a `data` object with the full article.

### Example: News Vector Search

```bash
curl -X POST "https://api.goperigon.com/v1/vector/news/all" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "What are the latest developments in nuclear fusion energy?",
    "size": 10,
    "pubDateFrom": "2026-01-01",
    "filter": {
      "sourceGroup": ["top100"],
      "language": ["en"]
    }
  }'
```

### Example: News Vector Search with Complex Filter

```bash
curl -X POST "https://api.goperigon.com/v1/vector/news/all" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "How is AI being used in healthcare to improve patient outcomes?",
    "size": 20,
    "filter": {
      "AND": [
        { "category": ["Tech", "Health"] },
        { "sourceGroup": ["top100"] }
      ],
      "NOT": [
        { "label": ["Opinion", "Press Release"] }
      ]
    }
  }'
```

---

## Vector Wikipedia Search

### Endpoint

```
POST https://api.goperigon.com/v1/vector/wikipedia/all
```

### Body Parameters (JSON)

| Parameter | Type | Required | Description |
|---|---|---|---|
| `prompt` | string | **Yes** | Natural language query to search Wikipedia pages. |
| `page` | integer | No | Page number to retrieve. |
| `size` | integer | No | Number of results per page. |
| `pageviewsFrom` | integer | No | Minimum average daily page views. |
| `pageviewsTo` | integer | No | Maximum average daily page views. |
| `wikiRevisionFrom` | date | No | Pages modified after this date (ISO 8601 or `yyyy-mm-dd`). |
| `wikiRevisionTo` | date | No | Pages modified before this date. |
| `filter` | object | No | Structured filter with nested `AND`/`OR`/`NOT` logic (see below). |

### Wikipedia Filter Object

```json
{
  "filter": {
    "wikidataInstanceOfLabel": ["human"],
    "wikiCode": ["enwiki"]
  }
}
```

**Available filter fields:** `pageId`, `sectionId`, `wikiCode`, `wikidataId`, `wikidataInstanceOfId`, `wikidataInstanceOfLabel`, `wikiNamespace`, `wikiPageId`, `wikiRevisionId`

### Response

```json
{
  "status": 200,
  "results": [
    {
      "score": 0.89,
      "data": {
        "pageId": "...",
        "sectionId": "...",
        "title": "Nuclear fusion",
        "content": "Nuclear fusion is a reaction in which...",
        "wikiTitle": "Nuclear_fusion",
        "wikidataId": "Q...",
        "wikidataInstanceOf": [{ "label": "type of nuclear reaction", "wikidataId": "Q..." }],
        "wikiCode": "enwiki",
        "wikiPageId": 12345,
        "wikiRevisionId": 67890,
        "wikiRevisionTs": "2026-01-15T10:00:00Z",
        "wikiNamespace": 0,
        "pageviews": 1500,
        "redirectTitles": ["Fusion energy", "Thermonuclear fusion"],
        "styleLevel": 2
      }
    }
  ]
}
```

Each result returns a **section** of a Wikipedia page, not the entire page. The `score` indicates semantic similarity. Use `pageId` to fetch the full page via `/v1/wikipedia/all`.

### Example: Wikipedia Vector Search

```bash
curl -X POST "https://api.goperigon.com/v1/vector/wikipedia/all" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "How does CRISPR gene editing work and what are its applications?",
    "size": 10
  }'
```

### Example: Wikipedia Vector Search with Filters

```bash
curl -X POST "https://api.goperigon.com/v1/vector/wikipedia/all" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Major breakthroughs in quantum computing",
    "size": 15,
    "pageviewsFrom": 100,
    "filter": {
      "wikiCode": ["enwiki"]
    }
  }'
```

---

## When to Use Vector Search vs. Keyword Search

| Use Case | Recommended Endpoint |
|---|---|
| Exact keyword match, specific filters | `/v1/articles/all` (keyword) |
| Conversational, intent-based queries | `/v1/vector/news/all` (semantic) |
| "What's happening with..." style questions | `/v1/vector/news/all` (semantic) |
| Complex Boolean queries with many filters | `/v1/articles/all` (keyword) |
| Factual background/encyclopedic context | `/v1/vector/wikipedia/all` (semantic) |
| Understanding a concept or topic in depth | `/v1/vector/wikipedia/all` (semantic) |
| Combining news + knowledge | Use both vector endpoints |

## Tips

- **Vector search understands intent** — "companies going bankrupt in retail" works better as a vector prompt than as a keyword query.
- **Use filters to narrow scope** — Combine semantic search with structured filters for precision.
- **News vector covers 6 months** — For older articles, use keyword search with `/v1/articles/all`.
- **Wikipedia vector returns sections** — Each result is a page section, not a full page. Follow up with `/v1/wikipedia/all` using `id` or `sectionId` for full content.
- **Combine both corpora** — Search news for current events and Wikipedia for background context to build comprehensive answers.
