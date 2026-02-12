# Agent Skills

Give your AI coding agent expert knowledge of the Perigon API. Agent Skills let you install pre-built instructions and references directly into your development environment so your AI agent can build using Perigon APIs and SDKs with accurate knowledge and properly utilize all the features the Perigon API provides. This will also allow your agents to search news, track stories, summarize content, run vector searches, and more.

## What Are Agent Skills?

Agent Skills are reusable packages of knowledge that AI coding agents (like Claude Code, Cursor, OpenCode, GitHub Copilot, and others) can automatically discover and use. Each skill is a collection of markdown files containing instructions, API references, code examples, and best practices.

When you install a skill into your project, your AI agent smartly reads it and gains the ability to use that knowledge when helping you code. No configuration needed — just install and start asking your agent to build with the Perigon API.

Skills are part of the open [Agent Skills Standard](https://agentskills.io) and are distributed through the [skills.sh](https://skills.sh) CLI.

## Installation

1. Make sure you have Node.js installed.
2. Run the following command in your project directory:

```
$ npx skills add goperigon/skills
```

3. That's it! The skills are now installed in your project and your AI agent can use them immediately.

>NOTE: The `skills` CLI is not required to use the Perigon skills, but it is highly recommended since it will take care of cloning the repository, downloading the skills you're looking for, and placing them in the right directories for your AI agents to access them.

This works across **20+ AI agent platforms** — the CLI automatically detects which agent you're using and installs the skills in the right place.

## Supported Platforms

| Platform | Skills Directory |
|---|---|
| [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) | `.claude/skills/` |
| [Cursor](https://www.cursor.com/) | `.cursor/skills/` |
| [GitHub Copilot](https://github.com/features/copilot) | `.github/skills/` |
| [Cline](https://cline.bot/) | `.cline/skills/` |
| [Windsurf](https://windsurf.com/) | `.windsurf/skills/` |

For the full list of supported platforms, see [agentskills.io](https://agentskills.io).

## Available Skills

| Skill | Description |
|---|---|
| `perigon-api-best-practices` | Comprehensive guide for using every Perigon API endpoint — search, stories, summarization, vector search, Wikipedia, entities, and more. |
| `perigon-sdks` | Best practices for using the official Perigon SDKs for TypeScript, Python, and Go — installation, client setup, all methods, error handling, and idiomatic patterns. |

## What's Inside

### `perigon-api-best-practices`

Teaches your AI agent how to use every Perigon API endpoint effectively. Includes 12 detailed reference files:

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

Teaches your AI agent how to use the official Perigon SDKs. Includes side-by-side comparisons and 3 detailed SDK reference files:

| Reference | Covers |
|---|---|
| `typescript-sdk.md` | `@goperigon/perigon-ts` — full method reference, middleware, runtime compatibility |
| `python-sdk.md` | `perigon` (PyPI) — sync/async methods, Pydantic model objects, error handling |
| `go-sdk.md` | `perigon-go-sdk/v2` — service pattern, optional field constructors, built-in retries, middleware |

## Usage Examples

Once the skills are installed, just ask your AI agent to use the Perigon API. Here are some example prompts you can try:

> "Build real-time news tracking for new medical advancements in the field of nuclear medicine."

> "Integrate the Perigon API using the Typescript SDK into my news monitoring backend pipeline using the vector endpoint."

> "Search for the latest news about renewable energy from the last week, filtered to top-tier sources only."

> "Use vector search to find news articles semantically similar to 'impact of AI on healthcare workers'."

> "Look up Wikipedia information about quantum computing and find related recent news."

> "Set up the Perigon TypeScript SDK in my project and search for articles about Tesla."

Your agent will automatically reference the installed skills to construct the right API calls, use the correct SDK methods, and follow best practices to build fully running pipelines inside your project.

## Prerequisites

Before using Perigon Agent Skills, you need a Perigon API key:

1. [Create a Perigon account](https://www.perigon.io/) and verify your email.
2. Go to the [developer section](https://www.perigon.io/dev) to create an API Key.
3. Set the environment variable in your environment (e.g., shell environment):

```
$ export PERIGON_API_KEY="your-api-key"
```

For more details about authentication and getting started with the API, see the [Getting Started](/docs/getting-started) guide.

## Links

- [Perigon Skills Repository](https://github.com/goperigon/skills) — source code and all skill files
- [skills.sh](https://skills.sh) — the CLI tool for installing agent skills
- [Agent Skills Standard](https://agentskills.io) — the open standard and full list of supported platforms
- [Perigon SDKs](/docs/sdks) — official SDK documentation
- [API Reference](https://www.perigon.io/reference) — full Perigon API reference
