# Articles Search — `/v1/articles/all`

Search and filter all news articles available via the Perigon API. Returns a list of individual articles matched to your criteria.

## Endpoint

```
GET https://api.perigon.io/v1/articles/all
```

## Key Parameters

### Text Search

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Primary search query across title, description, and content. Supports Boolean operators (`AND`, `OR`, `NOT`), exact phrases (`"quoted"`), and wildcards (`*`, `?`). |
| `title` | string | Search within article titles only. Same syntax as `q`. |
| `desc` | string | Search within description fields only. |
| `content` | string | Search within full article body content. |
| `summary` | string | Search within article summary fields. |
| `url` | string | Search within article URLs (e.g., `source=cnn.com&url=travel`). |

### Date Filters

| Parameter | Type | Description |
|---|---|---|
| `from` | date | Articles published after this date. ISO 8601 or `yyyy-mm-dd`. |
| `to` | date | Articles published before this date. |
| `addDateFrom` | date | Articles added to Perigon after this date. |
| `addDateTo` | date | Articles added to Perigon before this date. |
| `refreshDateFrom` | date | Articles refreshed/updated after this date. |
| `refreshDateTo` | date | Articles refreshed/updated before this date. |

### Pagination & Sorting

| Parameter | Type | Description |
|---|---|---|
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `sortBy` | string | Sort order: `relevance` (default), `date`, `reverseDate`, `addDate`, `reverseAddDate`, `pubDate`, `refreshDate`. |
| `showNumResults` | boolean | Return total count (default `false`, slightly slower when `true`). |

### Entity Filters (OR logic with arrays)

| Parameter | Type | Description |
|---|---|---|
| `category` | string[] | Filter by categories (Tech, Politics, Sports, Business, etc.). Use `none` for uncategorized. |
| `topic` | string[] | Filter by topics (Markets, Crime, Cryptocurrency, etc.). More granular than categories. |
| `source` | string[] | Filter by publisher domains. Supports wildcards (`*.cnn.com`). |
| `sourceGroup` | string[] | Curated publisher bundles: `top10`, `top25`, `top50`, `top100`, `top25tech`, `top25crypto`, etc. |
| `language` | string[] | ISO-639 two-letter codes (`en`, `es`, `fr`). |
| `country` | string[] | Two-letter country codes (`us`, `gb`, `jp`). |
| `label` | string[] | Editorial labels: `Opinion`, `Paid-news`, `Non-news`, `Fact Check`, `Press Release`. |
| `medium` | string[] | `Article` or `Video`. |
| `personName` | string[] | Exact person name match. |
| `personWikidataId` | string[] | Person Wikidata IDs. |
| `companyName` | string | Company name (exact match). |
| `companyDomain` | string[] | Company domains (e.g., `apple.com`). |
| `companyId` | string[] | Company identifiers. |
| `companySymbol` | string[] | Stock ticker symbols (e.g., `AAPL`). |
| `journalistId` | string[] | Journalist identifiers. |
| `author` | string[] | Author names (exact match). |
| `byline` | string[] | Author bylines. |
| `articleId` | string[] | Specific article IDs. |
| `clusterId` | string[] | Story/cluster IDs (get all articles in a story). |
| `watchlist` | string[] | Watchlist names for people and companies. |

### Location Filters

| Parameter | Type | Description |
|---|---|---|
| `city` | string[] | City plays a central role in content. |
| `state` | string[] | State plays a central role in content. |
| `county` | string[] | County plays a central role in content. |
| `area` | string[] | Neighborhood/borough/district level. |
| `locationsCountry` | string[] | Country plays a central role in content. |
| `location` | string[] | Structured location: `city:New York::state:NY`. |
| `lat`, `lon`, `maxDistance` | float | Geo search by coordinates (km radius). |

### Source Location Filters

| Parameter | Type | Description |
|---|---|---|
| `sourceCity` | string[] | Articles from publishers in a given city. |
| `sourceState` | string[] | Articles from publishers in a given state. |
| `sourceCountry` | string[] | Articles from publishers in a given country (2-letter code). |
| `sourceCounty` | string[] | Articles from publishers in a given county. |
| `sourceLat`, `sourceLon`, `sourceMaxDistance` | float | Geo search for local publications. |

### Sentiment Filters

| Parameter | Type | Description |
|---|---|---|
| `positiveSentimentFrom` / `positiveSentimentTo` | float | Positive sentiment range (0.0–1.0). |
| `negativeSentimentFrom` / `negativeSentimentTo` | float | Negative sentiment range (0.0–1.0). |
| `neutralSentimentFrom` / `neutralSentimentTo` | float | Neutral sentiment range (0.0–1.0). |

### Other Filters

| Parameter | Type | Description |
|---|---|---|
| `paywall` | boolean | Filter by source paywall status. |
| `showReprints` | boolean | Include wire-service reprints (default `true`). |
| `reprintGroupId` | string | Get all versions of the same content. |
| `searchTranslation` | boolean | Search translated content fields for non-English articles. |
| `linkTo` | string | Articles containing links to specified URL pattern. |
| `taxonomy` | string[] | Google Content Categories (full path, e.g., `/Finance/Banking/Other`). |
| `prefixTaxonomy` | string | Category prefix filter (e.g., `/Finance`). |

### Highlighting

| Parameter | Type | Description |
|---|---|---|
| `showHighlighting` | boolean | Enable text highlighting in results. |
| `highlightQ` | string | Separate query for highlights. |
| `highlightPreTag` | string | HTML tag before highlight (default `<em>`). |
| `highlightPostTag` | string | HTML tag after highlight (default `</em>`). |
| `highlightFragmentSize` | integer | Characters per fragment (default 100). |
| `highlightNumFragments` | integer | Max fragments per field. |

### Exclude Filters

Every entity filter has an `exclude` counterpart that uses AND-exclude logic:
`excludeCategory`, `excludeTopic`, `excludeSource`, `excludeSourceGroup`, `excludeLanguage`, `excludeLabel`, `excludePersonName`, `excludePersonWikidataId`, `excludeCompanyDomain`, `excludeCompanyId`, `excludeCompanySymbol`, `excludeAuthor`, `excludeJournalistId`, `excludeCounty`, `excludeCity`, `excludeState`, `excludeLocationsCountry`, `excludeSourceCity`, `excludeSourceState`, `excludeSourceCountry`, `excludeSourceCounty`, `excludeWatchlist`

## Response Structure

```json
{
  "status": 200,
  "numResults": 1542,
  "articles": [
    {
      "articleId": "abc123",
      "title": "OpenAI Releases New Model",
      "description": "...",
      "content": "...",
      "summary": "...",
      "shortSummary": "...",
      "url": "https://example.com/article",
      "imageUrl": "https://...",
      "pubDate": "2026-02-10T14:30:00Z",
      "addDate": "2026-02-10T14:35:00Z",
      "refreshDate": "2026-02-10T14:35:00Z",
      "language": "en",
      "country": "us",
      "medium": "Article",
      "authorsByline": "Jane Smith",
      "reprint": false,
      "reprintGroupId": "...",
      "clusterId": "story-xyz",
      "score": 12.5,
      "source": {
        "domain": "example.com",
        "location": { "country": "us", "state": "CA", "city": "San Francisco" },
        "paywall": false
      },
      "sentiment": {
        "positive": 0.65,
        "negative": 0.10,
        "neutral": 0.25
      },
      "categories": [{ "name": "Tech" }],
      "topics": [{ "name": "Artificial Intelligence" }],
      "people": [{ "name": "Sam Altman", "wikidataId": "Q..." }],
      "companies": [{ "id": "...", "name": "OpenAI", "domains": ["openai.com"], "symbols": [] }],
      "journalists": [{ "id": "...", "name": "Jane Smith" }],
      "keywords": [{ "name": "AI", "weight": 0.95 }],
      "labels": [{ "name": "News" }],
      "entities": [{ "data": "GPT-5", "type": "PRODUCT", "mentions": 12 }],
      "locations": [{ "country": "us", "state": "CA", "city": "San Francisco" }],
      "places": [{ "city": "San Francisco", "state": "California", "country": "United States" }],
      "taxonomies": [{ "name": "/Technology/AI", "score": 0.92 }]
    }
  ]
}
```

## Example Requests

### Basic keyword search

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/articles/all?q=artificial+intelligence&size=10"
```

### Filtered by topic, source group, and date range

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/articles/all?q=climate+change&topic=Environment&sourceGroup=top100&from=2026-01-01&to=2026-02-01&size=20&sortBy=date"
```

### Negative sentiment articles about a company

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/articles/all?companySymbol=TSLA&negativeSentimentFrom=0.6&size=10&sortBy=date"
```

### Articles in a specific story cluster

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/articles/all?clusterId=CLUSTER_ID_HERE&size=50&sortBy=date"
```

## When to Use This Endpoint

- **Individual article retrieval** — when you need the full article objects
- **Building search interfaces** — keyword + filter combination
- **Data pipelines** — paginate through results for bulk collection
- **Story deep-dives** — use `clusterId` to get all articles in a story from `/v1/stories/all`

For grouped/clustered results, use `/v1/stories/all` instead. For natural language search, use `/v1/vector/news/all`.
