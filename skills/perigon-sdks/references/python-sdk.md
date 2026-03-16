# Python SDK (`perigon`)

Fully-typed Python client for the Perigon API. Built on httpx and Pydantic with sync and async support.

## Installation

```bash
pip install perigon
# poetry add perigon
```

**Requirements:** Python 3.9+

**Dependencies:** httpx, pydantic, python-dateutil, typing-extensions

## Client Setup

```python
import os
from perigon import V1Api, ApiClient

# Using environment variable (recommended)
api = V1Api(ApiClient(api_key=os.environ["PERIGON_API_KEY"]))

# Using a string directly
api = V1Api(ApiClient(api_key="your-api-key"))

# Using a callable (e.g., from a vault)
api = V1Api(ApiClient(api_key=lambda: get_key_from_vault()))
```

### Client Options

```python
api_client = ApiClient(
    api_key="your-key",
    base_url="https://api.perigon.io",  # default
    timeout=30.0,                        # request timeout in seconds
)
api = V1Api(api_client)
```

The client uses `httpx.Client` (sync) and `httpx.AsyncClient` (async) under the hood for connection pooling.

## Method Reference

Every method has a sync version and an `_async` variant:

| Method | Async Variant | Endpoint | Description |
|---|---|---|---|
| `search_articles(...)` | `search_articles_async(...)` | `GET /v1/articles/all` | Search news articles |
| `search_stories(...)` | `search_stories_async(...)` | `GET /v1/stories/all` | Search clustered stories |
| `get_story_history(...)` | `get_story_history_async(...)` | `GET /v1/stories/history` | Get story evolution history |
| `search_summarizer(...)` | `search_summarizer_async(...)` | `POST /v1/summarize` | AI-powered summarization |
| `vector_search_articles(...)` | `vector_search_articles_async(...)` | `POST /v1/vector/news/all` | Semantic news search |
| `search_wikipedia(...)` | `search_wikipedia_async(...)` | `GET /v1/wikipedia/all` | Search Wikipedia pages |
| `vector_search_wikipedia(...)` | `vector_search_wikipedia_async(...)` | `POST /v1/vector/wikipedia/all` | Semantic Wikipedia search |
| `search_companies(...)` | `search_companies_async(...)` | `GET /v1/companies/all` | Search companies |
| `search_people(...)` | `search_people_async(...)` | `GET /v1/people/all` | Search people |
| `search_journalists(...)` | `search_journalists_async(...)` | `GET /v1/journalists/all` | Search journalists |
| `get_journalist_by_id(...)` | `get_journalist_by_id_async(...)` | `GET /v1/journalists/{id}` | Get journalist by ID |
| `search_sources(...)` | `search_sources_async(...)` | `GET /v1/sources/all` | Search media sources |
| `search_topics(...)` | `search_topics_async(...)` | `GET /v1/topics/all` | Browse topic taxonomy |

## Important: `var_from` Keyword

`from` is a reserved keyword in Python. The SDK uses `var_from` instead:

```python
# CORRECT
articles = api.search_articles(q="AI", var_from="2026-01-01", to="2026-02-01")

# WRONG — SyntaxError
# articles = api.search_articles(q="AI", from="2026-01-01")
```

## Endpoint Examples

### Articles Search

```python
result = api.search_articles(
    q="artificial intelligence",
    source=["techcrunch.com", "wired.com"],
    var_from="2026-01-01",
    to="2026-02-11",
    size=10,
    sort_by="date",
)

print(f"Found {result.num_results} articles")
for article in result.articles:
    print(f"{article.title} — {article.source.domain}")
```

**Key parameters:**
- `q` — Search query (Boolean operators: `AND`, `OR`, `NOT`; exact phrases: `"..."`; wildcards: `*`)
- `source` — List of source domains
- `var_from` / `to` — Date range (ISO strings or datetime objects)
- `category` / `topic` — Content filters
- `sort_by` — `"date"` or `"relevance"`
- `size` / `page` — Pagination (page starts at 0)
- `positive_sentiment_from` / `negative_sentiment_from` — Sentiment range (0.0–1.0)
- `company_name` / `person_name` — Entity filters
- `source_group` — Predefined groups (`"top100"`, `"top25tech"`, etc.)
- `show_reprints` — Boolean, set `False` to deduplicate

### Stories (Clustered Articles)

```python
result = api.search_stories(
    q="climate change",
    size=5,
    sort_by="updatedAt",
)

for story in result.results:
    print(f"{story.name} — {story.article_count} articles")
    print(f"Summary: {story.summary}")
```

### Story History

```python
result = api.get_story_history(
    cluster_id=["911860d569ca464698c0beec0697f694"],
    var_from="2026-01-01",
    changelog_exists=True,
    sort_by="createdAt",
    size=10,
)

print(f"Found {result.num_results} history records")
for record in result.results:
    print(f"{record.cluster_id} — {record.created_at}")
    print(f"Summary: {record.short_summary}")
    if record.changelog:
        print(f"Changelog: {record.changelog}")
```

**Key parameters:**
- `cluster_id` — List of cluster IDs to track specific stories
- `var_from` / `to` — Date range (ISO strings or datetime objects). Note: `var_from` instead of `from` (Python reserved word).
- `sort_by` — `"createdAt"` or `"triggeredAt"`
- `changelog_exists` — Filter by presence of changelog
- `size` / `page` — Pagination

### AI Summarization

```python
from perigon.models import SummaryBody

result = api.search_summarizer(
    summary_body=SummaryBody(
        prompt="Summarize the key developments and their impact"
    ),
    q="renewable energy",
    size=10,
)

print(result.summary)
```

**Note:** The `summary_body` parameter requires a `SummaryBody` model object for the POST body.

### Vector Search (Semantic)

```python
from perigon.models import ArticleSearchParams, WikipediaSearchParams

# News vector search
news_results = api.vector_search_articles(
    article_search_params=ArticleSearchParams(
        prompt="impact of AI on healthcare workers",
        size=5,
    )
)

for item in news_results.results:
    print(f"{item.data.title} (score: {item.score})")

# Wikipedia vector search
wiki_results = api.vector_search_wikipedia(
    wikipedia_search_params=WikipediaSearchParams(
        prompt="quantum computing applications",
        size=5,
        pageviews_from=100,  # filter to popular pages
    )
)
```

**Note:** Vector search and Wikipedia vector search require model objects for the POST body.

### Companies

```python
result = api.search_companies(name="Apple", size=5)

for company in result.results:
    print(f"{company.name} — {company.description[:100] if company.description else 'N/A'}")
```

### People

```python
result = api.search_people(name="Elon Musk", size=5)

for person in result.results:
    print(f"{person.name}")
```

### Journalists

```python
# Search
result = api.search_journalists(name="Kevin", size=5)

for journalist in result.results:
    print(f"{journalist.name} — {journalist.id}")

# Get by ID
journalist = api.get_journalist_by_id(id=result.results[0].id)
print(f"{journalist.name}")
```

### Sources

```python
result = api.search_sources(domain="reuters.com", size=5)
```

### Topics

```python
result = api.search_topics(size=20)

for topic in result.results:
    print(f"{topic.name}")
```

### Wikipedia

```python
result = api.search_wikipedia(
    q="machine learning",
    size=5,
    sort_by="relevance",
)

for page in result.results:
    print(f"{page.wiki_title} — {page.url}")
    print(f"Views/day: {page.pageviews}")
```

## Async Support

All methods have async counterparts with the `_async` suffix:

```python
import asyncio
from perigon import V1Api, ApiClient

async def main():
    api = V1Api(ApiClient(api_key="your-key"))

    # Single async call
    articles = await api.search_articles_async(q="technology", size=5)

    # Concurrent calls with asyncio.gather
    articles_task = api.search_articles_async(q="AI", size=5)
    companies_task = api.search_companies_async(name="Google", size=3)
    stories_task = api.search_stories_async(q="AI", size=3)

    articles, companies, stories = await asyncio.gather(
        articles_task, companies_task, stories_task
    )

    print(f"Articles: {articles.num_results}")
    print(f"Companies: {len(companies.results)}")
    print(f"Stories: {len(stories.results)}")

asyncio.run(main())
```

## Error Handling

```python
from perigon.exceptions import ApiException, ApiKeyError, ApiValueError

try:
    result = api.search_articles(q="test")
except ApiException as e:
    print(f"HTTP {e.status}: {e.body}")

    if e.status == 401:
        print("Invalid API key")
    elif e.status == 429:
        print("Rate limited — wait and retry")
    elif e.status >= 500:
        print("Server error — retry with backoff")
except ApiKeyError as e:
    print(f"Missing or invalid key in response: {e}")
except ApiValueError as e:
    print(f"Invalid parameter value: {e}")
```

## Model Objects

POST-based endpoints require typed model objects for request bodies:

| Model | Used By | Fields |
|---|---|---|
| `ArticleSearchParams` | `vector_search_articles()` | `prompt`, `size`, `page`, `filter` |
| `SummaryBody` | `search_summarizer()` | `prompt`, `summary_type`, `language`, `max_tokens`, `temperature` |
| `WikipediaSearchParams` | `vector_search_wikipedia()` | `prompt`, `size`, `page`, `pageviews_from`, `filter` |

Import them from `perigon.models`:

```python
from perigon.models import ArticleSearchParams, SummaryBody, WikipediaSearchParams
```

## Pagination Pattern

```python
all_articles = []
page = 0

while True:
    result = api.search_articles(
        q="technology",
        size=100,
        page=page,
        show_num_results=True,
    )

    all_articles.extend(result.articles)

    if len(all_articles) >= result.num_results or len(result.articles) == 0:
        break
    page += 1
```

## Date Filtering

The SDK accepts both string dates and datetime objects:

```python
from datetime import datetime, timedelta, timezone

# Using strings
result = api.search_articles(
    q="business",
    var_from="2026-01-01",
    to="2026-02-11",
)

# Using datetime objects
end = datetime.now(timezone.utc)
start = end - timedelta(days=7)

result = api.search_articles(
    q="business",
    var_from=start,
    to=end,
)
```

## Clean Up

Close the HTTP clients when done (optional but recommended for long-running applications):

```python
# Sync
api_client.close()

# Async
await api_client.aclose()
```

## External Links

- [PyPI package](https://pypi.org/project/perigon)
- [GitHub repository](https://github.com/goperigon/perigon-python)
- [API documentation](https://docs.perigon.io)
