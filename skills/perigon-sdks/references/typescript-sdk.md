# TypeScript SDK (`@goperigon/perigon-ts`)

Fully-typed TypeScript client for the Perigon API. Works in Node.js, browsers, Deno, and edge runtimes.

## Installation

```bash
npm install @goperigon/perigon-ts
# yarn add @goperigon/perigon-ts
# pnpm add @goperigon/perigon-ts
# bun add @goperigon/perigon-ts
```

**Requirements:** Node.js 18+ (or any runtime with `fetch` support)

## Client Setup

```ts
import { Configuration, V1Api } from "@goperigon/perigon-ts";

const perigon = new V1Api(
  new Configuration({
    apiKey: process.env.PERIGON_API_KEY,
  }),
);
```

The `apiKey` can be a string or an async function that returns a string:

```ts
const perigon = new V1Api(
  new Configuration({
    apiKey: () => Promise.resolve(getKeyFromVault()),
  }),
);
```

## Method Reference

| Method | HTTP | Endpoint | Description |
|---|---|---|---|
| `searchArticles(params)` | GET | `/v1/articles/all` | Search and filter news articles |
| `searchStories(params)` | GET | `/v1/stories/all` | Search clustered news stories |
| `searchSummarizer(params)` | POST | `/v1/summarize` | AI-powered article summarization |
| `vectorSearchArticles(params)` | POST | `/v1/vector/news/all` | Semantic vector search over news |
| `searchWikipedia(params)` | GET | `/v1/wikipedia/all` | Search Wikipedia pages |
| `vectorSearchWikipedia(params)` | POST | `/v1/vector/wikipedia/all` | Semantic search over Wikipedia |
| `searchCompanies(params)` | GET | `/v1/companies/all` | Search company database |
| `searchPeople(params)` | GET | `/v1/people/all` | Search people mentioned in news |
| `searchJournalists(params)` | GET | `/v1/journalists/all` | Search journalist profiles |
| `getJournalistById(params)` | GET | `/v1/journalists/{id}` | Get journalist by ID |
| `searchSources(params)` | GET | `/v1/sources/all` | Search media sources |
| `searchTopics(params)` | GET | `/v1/topics/all` | Browse topic taxonomy |

All methods return a Promise and are fully typed with IntelliSense.

## Endpoint Examples

### Articles Search

```ts
const { articles, numResults } = await perigon.searchArticles({
  q: "artificial intelligence",
  source: ["techcrunch.com", "wired.com"],
  from: new Date("2026-01-01"),
  size: 10,
  sortBy: "date",
});

console.log(`Found ${numResults} articles`);
articles.forEach((article) => {
  console.log(`${article.title} — ${article.source?.domain}`);
});
```

**Key parameters:**
- `q` — Search query (supports `AND`, `OR`, `NOT`, exact phrases `"..."`, wildcards `*`)
- `source` — Array of source domains
- `from` / `to` — Date range (Date objects or ISO strings)
- `category` / `topic` — Content filters
- `sortBy` — `"date"` or `"relevance"`
- `size` / `page` — Pagination
- `positiveSentimentFrom` / `negativeSentimentFrom` — Sentiment filters (0.0–1.0)
- `companyName` / `personName` — Entity filters
- `sourceGroup` — Predefined source groups (e.g., `"top100"`)

### Stories (Clustered Articles)

```ts
const { results, numResults } = await perigon.searchStories({
  q: "climate change",
  size: 5,
  sortBy: "updatedAt",
});

results.forEach((story) => {
  console.log(`${story.name} — ${story.articleCount} articles`);
  console.log(`Summary: ${story.summary}`);
});
```

### AI Summarization

```ts
const result = await perigon.searchSummarizer({
  summaryBody: {
    summaryType: "keyPoints", // or "paragraph"
    language: "en",
  },
  q: "renewable energy",
  size: 10,
  sortBy: "date",
});

console.log(result.summary);
```

### Vector Search (Semantic)

```ts
// News vector search
const newsResults = await perigon.vectorSearchArticles({
  articleSearchParams: {
    prompt: "impact of AI on healthcare workers",
    size: 5,
  },
});

newsResults.results.forEach((item) => {
  console.log(`${item.data.title} (score: ${item.score})`);
});

// Wikipedia vector search
const wikiResults = await perigon.vectorSearchWikipedia({
  wikipediaSearchParams: {
    prompt: "quantum computing applications",
    size: 5,
  },
});
```

### Companies

```ts
const { results } = await perigon.searchCompanies({
  name: "Microsoft",
  size: 5,
});

results.forEach((company) => {
  console.log(`${company.name} — ${company.symbol} (${company.industry})`);
});
```

### People

```ts
const { results } = await perigon.searchPeople({
  name: "Elon Musk",
  size: 5,
});
```

### Journalists

```ts
// Search
const { results } = await perigon.searchJournalists({
  name: "Kevin",
  size: 5,
});

// Get by ID
const journalist = await perigon.getJournalistById({
  id: results[0].id,
});
```

### Sources

```ts
const { results } = await perigon.searchSources({
  domain: "reuters.com",
  size: 5,
});
```

### Topics

```ts
const { results } = await perigon.searchTopics({
  size: 20,
});
```

### Wikipedia

```ts
const { results, numResults } = await perigon.searchWikipedia({
  q: "machine learning",
  size: 5,
  sortBy: "relevance",
});

results.forEach((page) => {
  console.log(`${page.wikiTitle} — ${page.url}`);
  console.log(`Views/day: ${page.pageviews}`);
});
```

## Error Handling

All API errors extend `ResponseError` with `status` and `body` properties:

```ts
import { ResponseError } from "@goperigon/perigon-ts";

try {
  const result = await perigon.searchArticles({ q: "test" });
} catch (error) {
  if (error instanceof ResponseError) {
    console.error(`HTTP ${error.response.status}: ${error.response.statusText}`);

    if (error.response.status === 401) {
      console.error("Invalid API key");
    } else if (error.response.status === 429) {
      console.error("Rate limit exceeded — retry after backoff");
    }
  } else {
    console.error("Network or unexpected error:", error.message);
  }
}
```

## Middleware

Add request/response hooks via the `middleware` option:

```ts
const configuration = new Configuration({
  apiKey: process.env.PERIGON_API_KEY,
  middleware: [
    {
      pre: async (context) => {
        console.log(`Request: ${context.init.method} ${context.url}`);
        return undefined;
      },
      post: async (context) => {
        console.log(`Response: ${context.response.status}`);
        return undefined;
      },
      onError: async (context) => {
        console.error(`Error: ${context.error.message}`);
        return undefined;
      },
    },
  ],
});
```

## Pagination Pattern

```ts
let page = 0;
const allArticles = [];

while (true) {
  const result = await perigon.searchArticles({
    q: "technology",
    size: 100,
    page,
    showNumResults: true,
  });

  allArticles.push(...result.articles);

  if (allArticles.length >= result.numResults || result.articles.length === 0) {
    break;
  }
  page++;
}
```

## Date Filtering

The SDK accepts both `Date` objects and ISO 8601 strings:

```ts
// Using Date objects
const result = await perigon.searchArticles({
  from: new Date("2026-01-01"),
  to: new Date(),
});

// Using strings
const result2 = await perigon.searchArticles({
  from: "2026-01-01",
  to: "2026-02-11",
});
```

## Runtime Support

The SDK uses the Fetch API and works in:
- **Node.js** 18+ (built-in fetch)
- **Browsers** (all modern browsers)
- **Deno** (built-in fetch)
- **Edge runtimes** (Cloudflare Workers, Vercel Edge Functions)
- **Bun** (built-in fetch)

## External Links

- [npm package](https://www.npmjs.com/package/@goperigon/perigon-ts)
- [GitHub repository](https://github.com/goperigon/perigon-ts)
- [API documentation](https://docs.perigon.io)
