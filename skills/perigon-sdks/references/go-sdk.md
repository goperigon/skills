# Go SDK (`perigon-go-sdk`)

Strongly-typed Go client for the Perigon API. Generated with Stainless, featuring built-in retries, auto-pagination, and functional options.

## Installation

```bash
go get -u github.com/goperigon/perigon-go-sdk/v2
```

**Requirements:** Go 1.22+

**Import:**

```go
import (
    "github.com/goperigon/perigon-go-sdk/v2" // imported as perigon
    "github.com/goperigon/perigon-go-sdk/v2/option"
)
```

## Client Setup

```go
// Reads PERIGON_API_KEY environment variable by default
client := perigon.NewClient()

// Explicit API key
client := perigon.NewClient(
    option.WithAPIKey("your-api-key"),
)

// With additional options
client := perigon.NewClient(
    option.WithAPIKey("your-api-key"),
    option.WithMaxRetries(3),
    option.WithHeader("X-Custom-Header", "value"),
)
```

## Service Pattern

The Go SDK uses a service-based pattern: `client.{Service}.{Method}(ctx, params)`.

All methods require a `context.Context` as the first argument.

## Method Reference

| Service Call | HTTP | Endpoint | Description |
|---|---|---|---|
| `client.All.List(ctx, params)` | GET | `/v1/articles/all` | Search news articles |
| `client.Stories.List(ctx, params)` | GET | `/v1/stories/all` | Search clustered stories |
| `client.Summarize.New(ctx, params)` | POST | `/v1/summarize` | AI-powered summarization |
| `client.Vector.News.Search(ctx, params)` | POST | `/v1/vector/news/all` | Semantic news search |
| `client.Wikipedia.Search(ctx, params)` | GET | `/v1/wikipedia/all` | Search Wikipedia pages |
| `client.Wikipedia.VectorSearch(ctx, params)` | POST | `/v1/vector/wikipedia/all` | Semantic Wikipedia search |
| `client.Companies.List(ctx, params)` | GET | `/v1/companies/all` | Search companies |
| `client.People.List(ctx, params)` | GET | `/v1/people/all` | Search people |
| `client.Journalists.List(ctx, params)` | GET | `/v1/journalists/all` | Search journalists |
| `client.Journalists.Get(ctx, id)` | GET | `/v1/journalists/{id}` | Get journalist by ID |
| `client.Sources.List(ctx, params)` | GET | `/v1/sources/all` | Search media sources |
| `client.Topics.List(ctx, params)` | GET | `/v1/topics/all` | Browse topic taxonomy |

## Optional Field Constructors

The Go SDK uses `omitzero` semantics. Optional primitive fields must be set using helper constructors:

| Constructor | Type | Example |
|---|---|---|
| `perigon.String("value")` | `param.Opt[string]` | `Q: perigon.String("AI")` |
| `perigon.Int(10)` | `param.Opt[int64]` | `Size: perigon.Int(10)` |
| `perigon.Float(0.7)` | `param.Opt[float64]` | `Temperature: perigon.Float(0.7)` |
| `perigon.Time(t)` | `param.Opt[time.Time]` | `From: perigon.Time(time.Now())` |
| `perigon.Bool(true)` | `param.Opt[bool]` | `ShowNumResults: perigon.Bool(true)` |

**Slices and enums** use zero-value omission (no constructor needed):

```go
params := perigon.AllListParams{
    Q:        perigon.String("AI"),          // optional string
    Size:     perigon.Int(10),               // optional int
    SortBy:   perigon.AllEndpointSortByDate, // enum value
    Source:   []string{"reuters.com"},        // slice
    Category: []string{"Tech", "Business"},  // slice
}
```

To send `null` explicitly: `param.Null[string]()`.

## Endpoint Examples

### Articles Search

```go
ctx := context.Background()

articles, err := client.All.List(ctx, perigon.AllListParams{
    Q:      perigon.String("artificial intelligence"),
    Source: []string{"techcrunch.com", "wired.com"},
    From:   perigon.Time(time.Now().AddDate(0, 0, -7)),
    Size:   perigon.Int(10),
    SortBy: perigon.AllEndpointSortByDate,
})
if err != nil {
    log.Fatal(err)
}

fmt.Printf("Found %d articles\n", len(articles.Articles))
for _, article := range articles.Articles {
    fmt.Printf("%s — %s\n", article.Title, article.Source.Domain)
}
```

**Key parameters (`AllListParams`):**
- `Q` — Search query (Boolean operators, exact phrases, wildcards)
- `Source` — Slice of source domains
- `From` / `To` — Date range (`perigon.Time(...)`)
- `Category` / `Topic` — Content filters (string slices)
- `SortBy` — `perigon.AllEndpointSortByDate` or `perigon.AllEndpointSortByRelevance`
- `Size` / `Page` — Pagination
- `PositiveSentimentFrom` / `NegativeSentimentFrom` — Sentiment range
- `CompanyName` / `PersonName` — Entity filters
- `SourceGroup` — Predefined groups (string slice)
- `ShowNumResults` — `perigon.Bool(true)` to include total count

### Stories (Clustered Articles)

```go
stories, err := client.Stories.List(ctx, perigon.StoryListParams{
    Q:                perigon.String("climate change"),
    MinUniqueSources: perigon.Int(3),
    SortBy:           perigon.SortByUpdatedAt,
    Size:             perigon.Int(5),
})
if err != nil {
    log.Fatal(err)
}

for _, story := range stories.Results {
    fmt.Printf("%s — %d articles\n", story.Name, story.UniqueCount)
    fmt.Printf("Summary: %s\n", story.Summary)
}
```

### Story History

The `/v1/stories/history` endpoint is not yet available in the Go SDK. Use the TypeScript or Python SDK, or call the REST API directly.

### AI Summarization

```go
summary, err := client.Summarize.New(ctx, perigon.SummarizeNewParams{
    Q:                    perigon.String("renewable energy"),
    Prompt:               perigon.String("Summarize the key developments"),
    MaxArticleCount:      perigon.Int(10),
    ReturnedArticleCount: perigon.Int(5),
    MaxTokens:            perigon.Int(300),
    Temperature:          perigon.Float(0.7),
    From:                 perigon.Time(time.Now().AddDate(0, 0, -7)),
})
if err != nil {
    log.Fatal(err)
}

fmt.Println(summary.Summary)
```

### Vector Search (Semantic)

```go
// News vector search
newsResults, err := client.Vector.News.Search(ctx, perigon.VectorNewsSearchParams{
    Prompt: "impact of AI on healthcare workers",
    Size:   perigon.Int(5),
})
if err != nil {
    log.Fatal(err)
}

for _, item := range newsResults.Results {
    fmt.Printf("%s (score: %.4f)\n", item.Data.Title, item.Score)
}

// Wikipedia vector search
wikiResults, err := client.Wikipedia.VectorSearch(ctx, perigon.WikipediaVectorSearchParams{
    Prompt: "quantum computing applications",
    Size:   perigon.Int(5),
})
if err != nil {
    log.Fatal(err)
}
```

### Companies

```go
companies, err := client.Companies.List(ctx, perigon.CompanyListParams{
    Name: perigon.String("Tesla"),
    Size: perigon.Int(5),
})
if err != nil {
    log.Fatal(err)
}

for _, company := range companies.Results {
    fmt.Printf("%s — %s (%s)\n", company.Name, company.Industry, company.Country)
}
```

### People

```go
people, err := client.People.List(ctx, perigon.PersonListParams{
    Name: perigon.String("Elon Musk"),
    Size: perigon.Int(5),
})
```

### Journalists

```go
// Search
journalists, err := client.Journalists.List(ctx, perigon.JournalistListParams{
    Name: perigon.String("Kevin"),
    Size: perigon.Int(5),
})

// Get by ID
journalist, err := client.Journalists.Get(ctx, journalists.Results[0].ID)
```

### Sources

```go
sources, err := client.Sources.List(ctx, perigon.SourceListParams{
    Domain: perigon.String("reuters.com"),
    Size:   perigon.Int(5),
})
```

### Topics

```go
topics, err := client.Topics.List(ctx, perigon.TopicListParams{
    Size: perigon.Int(20),
})
```

### Wikipedia

```go
wikiResult, err := client.Wikipedia.Search(ctx, perigon.WikipediaSearchParams{
    Q:      perigon.String("machine learning"),
    Size:   perigon.Int(5),
    SortBy: perigon.String("relevance"),
})
if err != nil {
    log.Fatal(err)
}

for _, page := range wikiResult.Results {
    fmt.Printf("%s — %s\n", page.WikiTitle, page.URL)
    fmt.Printf("Views/day: %d\n", page.Pageviews)
}
```

## Error Handling

API errors are returned as `*perigon.Error`, which includes `StatusCode`, HTTP request, and response data:

```go
import "errors"

result, err := client.All.List(ctx, perigon.AllListParams{
    Q: perigon.String("test"),
})
if err != nil {
    var apierr *perigon.Error
    if errors.As(err, &apierr) {
        fmt.Printf("HTTP %d\n", apierr.StatusCode)
        fmt.Println(string(apierr.DumpRequest(true)))  // full request
        fmt.Println(string(apierr.DumpResponse(true))) // full response
    } else {
        // Network error, timeout, etc.
        fmt.Printf("Error: %v\n", err)
    }
}
```

## Built-in Retries

The SDK automatically retries on connection errors, 408, 409, 429, and 5xx responses. Default: 2 retries with exponential backoff.

```go
// Disable retries
client := perigon.NewClient(
    option.WithMaxRetries(0),
)

// Override per-request
result, err := client.All.List(ctx, perigon.AllListParams{
    Q: perigon.String("test"),
}, option.WithMaxRetries(5))
```

## Timeouts

Use Go contexts for request timeouts:

```go
// Timeout for the entire request lifecycle (including retries)
ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
defer cancel()

result, err := client.All.List(ctx, perigon.AllListParams{
    Q: perigon.String("test"),
})

// Per-retry timeout
result, err := client.All.List(ctx, perigon.AllListParams{
    Q: perigon.String("test"),
}, option.WithRequestTimeout(10*time.Second))
```

## Pagination

### Manual Pagination

```go
page := int64(0)
var allArticles []perigon.Article

for {
    result, err := client.All.List(ctx, perigon.AllListParams{
        Q:              perigon.String("technology"),
        Size:           perigon.Int(100),
        Page:           perigon.Int(page),
        ShowNumResults: perigon.Bool(true),
    })
    if err != nil {
        log.Fatal(err)
    }

    allArticles = append(allArticles, result.Articles...)

    if len(allArticles) >= int(result.NumResults) || len(result.Articles) == 0 {
        break
    }
    page++
}
```

## RequestOptions

Functional options can be applied at the client level or per-request:

```go
// Client-level option
client := perigon.NewClient(
    option.WithHeader("X-Custom", "value"),
    option.WithMaxRetries(3),
)

// Per-request override
result, err := client.All.List(ctx, perigon.AllListParams{},
    option.WithHeader("X-Custom", "different-value"),
    option.WithMaxRetries(5),
    option.WithRequestTimeout(10*time.Second),
)
```

### Available Options

| Option | Description |
|---|---|
| `option.WithAPIKey(key)` | Set API key |
| `option.WithMaxRetries(n)` | Max retry attempts (default 2) |
| `option.WithRequestTimeout(d)` | Per-retry timeout |
| `option.WithHeader(k, v)` | Add custom header |
| `option.WithResponseInto(&resp)` | Capture raw `*http.Response` |
| `option.WithMiddleware(fn)` | Add request middleware |
| `option.WithJSONSet(path, val)` | Add undocumented body field |
| `option.WithQuerySet(k, v)` | Add undocumented query param |
| `option.WithDebugLog(nil)` | Enable debug logging |

## Middleware

```go
func Logger(req *http.Request, next option.MiddlewareNext) (res *http.Response, err error) {
    start := time.Now()
    log.Printf("Request: %s %s", req.Method, req.URL)

    res, err = next(req)

    log.Printf("Response: %d (%s)", res.StatusCode, time.Since(start))
    return res, err
}

client := perigon.NewClient(
    option.WithMiddleware(Logger),
)
```

## Accessing Raw Response

```go
var response *http.Response
result, err := client.All.List(ctx, perigon.AllListParams{
    Q: perigon.String("test"),
}, option.WithResponseInto(&response))

if err == nil {
    fmt.Printf("Status: %d\n", response.StatusCode)
    fmt.Printf("Headers: %v\n", response.Header)
}
```

## Response JSON Metadata

Response structs include a `JSON` field with metadata about each property:

```go
result, _ := client.All.List(ctx, perigon.AllListParams{Q: perigon.String("test")})

for _, article := range result.Articles {
    // Check if a field was present in the response
    if article.JSON.Title.Valid() {
        fmt.Println(article.Title)
    }

    // Access raw JSON
    raw := article.JSON.Title.Raw()

    // Access extra fields not in the struct
    for key, field := range article.JSON.ExtraFields {
        fmt.Printf("Extra: %s = %s\n", key, field.Raw())
    }
}
```

## External Links

- [Go package docs](https://pkg.go.dev/github.com/goperigon/perigon-go-sdk/v2)
- [GitHub repository](https://github.com/goperigon/perigon-go-sdk)
- [API reference (api.md)](https://github.com/goperigon/perigon-go-sdk/blob/main/api.md)
- [API documentation](https://docs.perigon.io)
