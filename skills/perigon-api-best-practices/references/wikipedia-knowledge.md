# Wikipedia Knowledge Base — `/v1/wikipedia/all`

Search and filter Wikipedia pages available via the Perigon API. Returns structured page data including sections, categories, summaries, pageview statistics, and Wikidata metadata.

## Endpoint

```
GET https://api.perigon.io/v1/wikipedia/all
```

## Key Parameters

### Text Search

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Primary search across title, summary, and content. Supports Boolean operators (`AND`, `OR`, `NOT`), exact phrases, and wildcards (`*`, `?`). |
| `title` | string | Search within page titles only. |
| `summary` | string | Search within page summaries. |
| `text` | string | Search within page content (across all sections). |
| `reference` | string | Search across page references. |

### Date Filters

| Parameter | Type | Description |
|---|---|---|
| `scrapedAtFrom` | date | Pages scraped after this date (ISO 8601 or `yyyy-mm-dd`). |
| `scrapedAtTo` | date | Pages scraped before this date. |
| `wikiRevisionFrom` | date | Pages modified (on Wikipedia) after this date. |
| `wikiRevisionTo` | date | Pages modified before this date. |

### Pagination & Sorting

| Parameter | Type | Description |
|---|---|---|
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `sortBy` | string | Sort order: `relevance` (default), `revisionTsDesc`, `revisionTsAsc`, `pageViewsDesc`, `pageViewsAsc`, `scrapedAtDesc`, `scrapedAtAsc`. |
| `showNumResults` | boolean | Return total count (default `false`). |

### Pageviews Filters

| Parameter | Type | Description |
|---|---|---|
| `pageviewsFrom` | integer | Minimum average daily page views. |
| `pageviewsTo` | integer | Maximum average daily page views. |
| `withPageviews` | boolean | Only return pages with viewership data available. |

### Identity Filters

| Parameter | Type | Description |
|---|---|---|
| `id` | string[] | Perigon page identifiers. |
| `sectionId` | string[] | Specific section IDs (each is unique). |
| `category` | string[] | Wikipedia categories. |
| `wikiCode` | string[] | Wiki project codes (currently `enwiki`). |
| `wikidataId` | string[] | Wikidata entity IDs (e.g., `Q42` for Douglas Adams). |
| `wikidataInstanceOfId` | string[] | Filter by Wikidata "instance of" IDs. |
| `wikidataInstanceOfLabel` | string[] | Filter by Wikidata "instance of" labels (e.g., `human`, `city`). |
| `wikiNamespace` | integer[] | Wiki namespaces (currently only main namespace `0`). |
| `wikiPageId` | integer[] | Wikipedia page IDs (unique per `wikiCode`). |
| `wikiRevisionId` | integer[] | Wikipedia revision IDs (changes on each edit). |

## Response Structure

```json
{
  "status": 200,
  "numResults": 150,
  "results": [
    {
      "id": "perigon-page-id",
      "wikiTitle": "Artificial_intelligence",
      "wikidataId": "Q11660",
      "wikiCode": "enwiki",
      "wikiPageId": 233488,
      "wikiRevisionId": 1234567,
      "wikiRevisionTs": "2026-01-20T08:00:00Z",
      "wikiNamespace": 0,
      "url": "https://en.wikipedia.org/wiki/Artificial_intelligence",
      "summary": "Artificial intelligence (AI) is intelligence demonstrated by machines...",
      "topImage": "https://upload.wikimedia.org/...",
      "pageviews": 5200,
      "scrapedAt": "2026-02-01T12:00:00Z",
      "categories": ["Artificial intelligence", "Computer science", "Emerging technologies"],
      "redirectTitles": ["AI", "Machine intelligence"],
      "externalLinks": ["https://..."],
      "references": ["https://..."],
      "wikidataInstanceOf": [
        { "label": "academic discipline", "wikidataId": "Q11862829" },
        { "label": "technology", "wikidataId": "Q11016" }
      ],
      "sections": [
        {
          "id": "section-abc",
          "title": "History",
          "styleLevel": 2,
          "textRaw": "The field of AI research was founded...",
          "textRich": "<p>The field of AI research was founded...</p>",
          "loc": [1200, 3400]
        },
        {
          "id": "section-def",
          "title": "Applications",
          "styleLevel": 2,
          "textRaw": "AI is used in healthcare, finance...",
          "textRich": "<p>AI is used in healthcare, finance...</p>",
          "loc": [3400, 5600]
        }
      ]
    }
  ]
}
```

## Example Requests

### Search Wikipedia by keyword

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/wikipedia/all?q=quantum+computing&size=10"
```

### Find high-traffic Wikipedia pages about a topic

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/wikipedia/all?q=machine+learning&pageviewsFrom=1000&sortBy=pageViewsDesc&size=10"
```

### Look up a specific Wikidata entity

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/wikipedia/all?wikidataId=Q42&size=1"
```

### Find all Wikipedia pages about people (using instance-of)

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/wikipedia/all?wikidataInstanceOfLabel=human&q=physicist&sortBy=pageViewsDesc&size=20"
```

### Recently edited pages on a topic

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/wikipedia/all?q=climate+change&wikiRevisionFrom=2026-01-01&sortBy=revisionTsDesc&size=10"
```

## When to Use This Endpoint

- **Factual background** — Enrich news coverage with encyclopedic context.
- **Entity resolution** — Look up Wikidata IDs for people, companies, places.
- **Knowledge graphs** — Build structured knowledge from Wikipedia's categories and Wikidata links.
- **Content enrichment** — Add Wikipedia summaries and section content to applications.
- **Trend analysis** — Monitor high-traffic pages and recent edits.

**Use `/v1/vector/wikipedia/all` instead** for semantic/natural language queries where you want conceptually related content rather than keyword matches.
