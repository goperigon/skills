# Stories & Clustering — `/v1/stories/all`

Track evolving narratives and top news headlines. Returns structured clusters of related articles with AI-generated summaries, key points, sentiment scores, and metadata on people, companies, topics, countries, and categories.

## Endpoint

```
GET https://api.perigon.io/v1/stories/all
```

## How Stories Work

- **Articles are grouped into clusters** during processing. Each article belongs to a single story, identified by the `clusterId` field.
- **Story titles are auto-generated** once a cluster reaches 5+ articles.
- **Stories update continuously** as new coverage is published and key details evolve.
- To fetch all articles within a story, use `/v1/articles/all` with the `clusterId` parameter.

## Key Parameters

### Search & Filtering

The stories endpoint accepts **all the same filtering parameters** as the articles endpoint (see `articles-search.md`), including:

- `q`, `title`, `desc`, `content`, `summary` — Text search
- `from`, `to`, `addDateFrom`, `addDateTo` — Date filters
- `category`, `topic`, `source`, `sourceGroup`, `language`, `country` — Entity filters
- `personName`, `companyDomain`, `companySymbol` — People & companies
- `positiveSentimentFrom/To`, `negativeSentimentFrom/To` — Sentiment
- All `exclude*` variants

### Stories-Specific Parameters

| Parameter | Type | Description |
|---|---|---|
| `companyName` | string | Filter stories by names of top mentioned companies. |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `sortBy` | string | Sort order (same options as articles endpoint). |
| `showNumResults` | boolean | Return total count. |

## Response Structure

```json
{
  "status": 200,
  "numResults": 250,
  "stories": [
    {
      "id": "cluster-abc123",
      "name": "OpenAI Announces GPT-5 Launch",
      "slug": "openai-announces-gpt-5-launch",
      "createdAt": "2026-02-09T10:00:00Z",
      "initializedAt": "2026-02-09T08:30:00Z",
      "updatedAt": "2026-02-10T14:00:00Z",
      "summary": "OpenAI has officially announced...",
      "shortSummary": "OpenAI launches GPT-5 with...",
      "summaryReferences": ["https://example.com/article1", "..."],
      "keyPoints": [
        { "point": "GPT-5 outperforms previous models by 40%", "source": "..." }
      ],
      "questions": [
        { "question": "When will GPT-5 be available to the public?" }
      ],
      "sentiment": {
        "positive": 0.72,
        "negative": 0.08,
        "neutral": 0.20
      },
      "uniqueCount": 85,
      "totalCount": 120,
      "reprintCount": 35,
      "uniqueSources": ["nytimes.com", "bbc.com", "techcrunch.com"],
      "imageUrl": "https://...",
      "imageSource": { "domain": "techcrunch.com", "url": "https://..." },
      "topCategories": [{ "name": "Tech" }],
      "topTopics": [{ "name": "Artificial Intelligence", "count": 85 }],
      "topPeople": [{ "name": "Sam Altman", "wikidataId": "Q...", "count": 60 }],
      "topCompanies": [{ "name": "OpenAI", "id": "...", "count": 85 }],
      "topCountries": ["us", "gb"],
      "topLocations": [{ "country": "us", "state": "CA", "city": "San Francisco" }],
      "topTaxonomies": [{ "name": "/Technology/AI" }],
      "categories": [{ "name": "Tech", "count": 85 }],
      "topics": [{ "name": "Artificial Intelligence", "count": 85 }],
      "countries": [{ "name": "us", "count": 60 }],
      "selectedArticles": [
        {
          "articleId": "...",
          "title": "...",
          "url": "...",
          "source": { "domain": "nytimes.com" },
          "pubDate": "2026-02-10T12:00:00Z"
        }
      ]
    }
  ]
}
```

## Example Requests

### Top stories right now

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/all?sourceGroup=top100&size=10&sortBy=date"
```

### Stories about a specific company

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/all?companyName=Tesla&size=10&sortBy=date"
```

### Stories in a topic with date range

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/all?topic=Cryptocurrency&from=2026-01-01&to=2026-02-01&size=20"
```

### Then get all articles in a story

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/articles/all?clusterId=CLUSTER_ID_HERE&size=100&sortBy=date"
```

## When to Use This Endpoint

- **Headline monitoring** — Get the top stories for a topic, category, or source group.
- **Story tracking** — Monitor how a narrative evolves over time.
- **Briefings & digests** — Build daily/weekly news summaries from clustered stories.
- **Competitive intelligence** — Track stories about specific companies.
- **Trend analysis** — See which stories are growing in coverage.

**Use `/v1/articles/all` instead** when you need individual articles, precise filtering, or data pipelines. **Use `/v1/summarize` instead** when you need a single AI-generated summary paragraph rather than structured story metadata.
