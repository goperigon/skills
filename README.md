# Perigon Skills

[AI Agent Skills](https://skills.sh) for the [Perigon News Intelligence API](https://www.perigon.io/). Install these skills to give your AI agent expert knowledge of the Perigon API and official SDKs — covering news search, story tracking, AI summarization, semantic vector search, Wikipedia knowledge, entity intelligence, and idiomatic SDK usage in TypeScript, Python, and Go.

## Installation

Install with the skills CLI:

```bash
npx skills add goperigon/skills
```

This works with **20+ AI agent platforms**, including:

| Platform | Notes |
|---|---|
| [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) | Loads into `.claude/skills/` |
| [Cursor](https://www.cursor.com/) | Loads into `.cursor/skills/` |
| [Cline](https://cline.bot/) | Loads into `.cline/skills/` |
| [GitHub Copilot](https://github.com/features/copilot) | Loads into `.github/skills/` |
| [Windsurf](https://windsurf.com/) | Loads into `.windsurf/skills/` |

See [agentskills.io](https://agentskills.io) for the full list of supported platforms.

## What's Included

### `perigon-api-best-practices`

A comprehensive skill that teaches your AI agent how to use every Perigon API endpoint effectively. It includes:

- **Endpoint selection guide** — decision tree for choosing the right API endpoint
- **Query construction** — Boolean operators, exact phrases, wildcards, and advanced filtering
- **12 detailed reference files** covering every aspect of the API:

| Reference | Covers |
|---|---|
| `authentication.md` | API key setup, header vs query param, security best practices |
| `articles-search.md` | `/v1/articles/all` — full-text article search with 40+ filter parameters |
| `stories-clustering.md` | `/v1/stories/all` — clustered news stories with summaries |
| `smart-summaries.md` | `/v1/summarize` — AI-powered summarization of news coverage |
| `vector-search.md` | `/v1/vector/news/all` and `/v1/vector/wikipedia/all` — semantic search |
| `wikipedia-knowledge.md` | `/v1/wikipedia/all` — structured Wikipedia page data |
| `people-companies.md` | `/v1/people/all` and `/v1/companies/all` — entity intelligence |
| `journalists-sources.md` | `/v1/journalists/all` and `/v1/sources/all` — media intelligence |
| `topics-categories.md` | `/v1/topics/all` — topic taxonomy and category system |
| `pagination-filtering.md` | Pagination patterns, array filters, sorting, and common recipes |
| `error-handling.md` | HTTP status codes, retry strategies, validation checklist |
| `rate-limits.md` | Rate limit management, caching, and optimization |

### `perigon-sdks`

A skill that teaches your AI agent how to use the official Perigon SDKs for TypeScript, Python, and Go. Includes:

- **SDK selection guide** — choose the right SDK for your runtime
- **Quick comparison table** — installation, auth, method style, async patterns, and error types across all three SDKs
- **Common patterns** — side-by-side examples for articles search, vector search, and error handling
- **3 detailed SDK reference files**:

| Reference | Covers |
|---|---|
| `typescript-sdk.md` | `@goperigon/perigon-ts` — full method reference, middleware, runtime compatibility |
| `python-sdk.md` | `perigon` (PyPI) — sync/async methods, `var_from` gotcha, Pydantic model objects |
| `go-sdk.md` | `perigon-go-sdk/v2` — service pattern, optional field constructors, built-in retries, middleware |

## Quick Start

1. **Get a Perigon API key** at [perigon.io](https://www.perigon.io/dev).

2. **Set the environment variable**:

   ```bash
   export PERIGON_API_KEY="your-api-key"
   ```

3. **Install the skill** into your project:

   ```bash
   npx skills add goperigon/skills
   ```

4. **Ask your AI agent** to use the Perigon API:

   > "Search for the latest news about renewable energy from the last week, filtered to top-tier sources only."

   > "Find all stories about the 2026 elections and summarize the key themes."

   > "Use vector search to find news articles semantically similar to 'impact of AI on healthcare workers'."

   > "Look up Wikipedia information about quantum computing and find related recent news."

## Perigon API Overview

The [Perigon API](https://www.perigon.io/) is a News Intelligence API that provides:

- **Real-time news search** across 140,000+ global sources
- **Story clustering** — articles grouped into evolving storylines
- **AI-powered summaries** of news coverage on any topic
- **Semantic vector search** over news and Wikipedia content
- **Entity intelligence** — 650k+ people, companies, 230k+ journalists, 200k+ sources
- **Wikipedia knowledge base** with structured page data
- **Rich filtering** — sentiment, categories, topics, locations, languages, and more

## Documentation

- [Perigon Documentation](https://perigon.io/docs)
- [Perigon API Reference](https://www.perigon.io/reference)
- [TypeScript SDK](https://github.com/goperigon/perigon-ts)
- [Python SDK](https://github.com/goperigon/perigon-python)
- [Go SDK](https://github.com/goperigon/perigon-go-sdk)
- [Skills Standard](https://agentskills.io)

## License

Apache 2.0 — see [LICENSE](LICENSE).
