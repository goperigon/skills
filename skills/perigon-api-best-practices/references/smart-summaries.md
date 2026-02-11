# Smart Summaries — `/v1/summarize`

Produce a single, concise AI-generated summary over the full corpus of articles matching your filters. Use a custom prompt to guide which insights to highlight.

## Endpoint

```
POST https://api.perigon.io/v1/summarize
```

The request body is JSON. Article filters are passed as query parameters on the URL.

## Body Parameters (JSON)

| Parameter | Type | Required | Description |
|---|---|---|---|
| `prompt` | string | No | Instructions guiding the summary (max 2,048 chars). |
| `maxArticleCount` | integer | No | Maximum articles to factor into the summary. |
| `returnedArticleCount` | integer | No | Maximum articles to include in the response (can be less than `maxArticleCount`). |
| `maxTokens` | integer | No | Maximum tokens to generate in the summary. |
| `temperature` | float | No | LLM sampling temperature (0.0 = deterministic, up to 2.0). |
| `topP` | float | No | Nucleus sampling (0.0–1.0). |
| `model` | string | No | LLM model: `gpt-4o`, `gpt-4o-mini`, `gpt-4.1`, `gpt-4.1-mini`, `gpt-4.1-nano`, `llama-3.3-70b-versatile`, `openai/gpt-oss-120b`. |
| `method` | string | No | Article selection: `ARTICLES` (include all matches) or `CLUSTERS` (one per cluster for diversity). |
| `summarizeFields` | string[] | No | Which article fields to include: `TITLE`, `CONTENT`, `SUMMARY` (up to 3). |

## Query Parameters (URL)

The summarize endpoint accepts **all the same filtering query parameters** as the articles endpoint (see `articles-search.md`), including:

- `q`, `title`, `desc`, `content`, `summary` — Text search
- `from`, `to`, `addDateFrom`, `addDateTo` — Date filters
- `category`, `topic`, `source`, `sourceGroup`, `language`, `country` — Entity filters
- `personName`, `companyDomain`, `companySymbol` — People & companies
- `positiveSentimentFrom/To`, `negativeSentimentFrom/To` — Sentiment
- `page`, `size`, `sortBy` — Pagination and sorting
- All `exclude*` variants

## Response Structure

```json
{
  "status": 200,
  "numResults": 45,
  "summary": "Over the past week, artificial intelligence has dominated...",
  "results": [
    {
      "articleId": "...",
      "title": "...",
      "url": "...",
      "pubDate": "...",
      "source": { "domain": "..." },
      "summary": "..."
    }
  ]
}
```

## Example Requests

### Summarize recent AI news

```bash
curl -X POST "https://api.perigon.io/v1/summarize?q=artificial+intelligence&from=2026-02-01&sourceGroup=top100" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Provide a concise briefing of the most important AI developments this week. Focus on product launches, research breakthroughs, and regulatory news.",
    "maxArticleCount": 50,
    "returnedArticleCount": 5,
    "model": "gpt-4.1"
  }'
```

### Summarize company coverage with sentiment focus

```bash
curl -X POST "https://api.perigon.io/v1/summarize?companySymbol=AAPL&from=2026-01-15&to=2026-02-15" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Summarize the key themes in Apple news coverage. Highlight any negative sentiment trends and their causes.",
    "maxArticleCount": 100,
    "method": "CLUSTERS",
    "summarizeFields": ["TITLE", "SUMMARY"]
  }'
```

### Quick lightweight summary

```bash
curl -X POST "https://api.perigon.io/v1/summarize?topic=Cryptocurrency&from=2026-02-09" \
  -H "x-api-key: $PERIGON_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "One paragraph summary of crypto market developments today.",
    "maxArticleCount": 20,
    "maxTokens": 200,
    "model": "gpt-4.1-nano"
  }'
```

## Tips

- **Use `method: "CLUSTERS"`** for diverse summaries that don't repeat the same story from different sources.
- **Use `summarizeFields: ["TITLE", "SUMMARY"]`** for faster, cheaper summaries when full content isn't needed.
- **Keep `maxArticleCount` reasonable** (20–100) for focused summaries; higher counts increase latency and cost.
- **Use `gpt-4.1-nano`** for quick, low-cost summaries; `gpt-4.1` for highest quality.
- **Combine with filters** — the better you filter, the more relevant the summary.

## When to Use This Endpoint

- **Daily briefings** — Generate morning news summaries for specific topics or companies.
- **Report generation** — Create executive summaries of coverage trends.
- **Alert summaries** — Summarize breaking news as it develops.
- **Chatbot responses** — Generate contextual news summaries in AI agent conversations.

**Use `/v1/stories/all` instead** when you need structured cluster metadata (key points, top entities) rather than a prose summary. **Use `/v1/articles/all` instead** when you need the raw articles for your own processing.
