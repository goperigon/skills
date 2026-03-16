# Story History — `/v1/stories/history`

Track how a news story evolves over time. Returns historical snapshots of a story cluster including AI-generated summaries, changelogs, key points, and questions at each refresh point.

## Endpoint

```
GET https://api.perigon.io/v1/stories/history
```

## How Story History Works

- **Each story cluster is refreshed periodically** as new articles are published. Each refresh produces a new historical record with an updated summary, key points, and optional changelog.
- **Changelogs describe what changed** between refreshes — new developments, shifted sentiment, or additional sources. Not all records have changelogs.
- **Key points highlight the most important facts** in the story at that point in time, each with article references.
- **Questions capture common reader questions** with AI-generated answers and supporting references.
- Use `clusterId` to track a specific story's evolution, or omit it to browse all story history records.

## Key Parameters

| Parameter | Type | Description |
|---|---|---|
| `clusterId` | string[] | Filter to a specific story. Pass one or more cluster IDs to see only history for those stories. |
| `from` | date-time | Return records created after this date. ISO 8601 or `yyyy-mm-dd`. |
| `to` | date-time | Return records created before this date. ISO 8601 or `yyyy-mm-dd`. |
| `sortBy` | string | Sort by `createdAt` (creation date) or `triggeredAt` (refresh trigger date). |
| `page` | integer | Zero-based page number (0 to 10000). See `pagination-filtering.md`. |
| `size` | integer | Results per page (0 to 100). |
| `changelogExists` | boolean | Filter to only include records that have a changelog (`true`) or don't (`false`). |

## Response Structure

```json
{
  "status": 200,
  "numResults": 4530319,
  "results": [
    {
      "clusterId": "911860d569ca464698c0beec0697f694",
      "createdAt": "2026-02-12T10:18:48.493128+00:00",
      "name": null,
      "triggeredAt": "2026-02-12T10:17:41.249815+00:00",
      "summary": "The Indian stock market experienced notable volatility...",
      "shortSummary": "Indian equity markets showed volatility with the Sensex and Nifty reversing early gains...",
      "changelog": null,
      "keyPoints": [
        {
          "point": "Among the Sensex components, 17 shares settled in the red, with notable losers including Bajaj Finance, HUL, and HDFC Bank.",
          "references": ["574436fd177e4af5a166a439a5bc97a0"]
        },
        {
          "point": "Market breadth showed about 1,994 shares advanced, 1,889 declined, and 157 remained unchanged.",
          "references": ["184a5f45336e4129b4e220d901abef10"]
        }
      ],
      "questions": [
        {
          "question": "What drove the market reversal?",
          "answer": "Cautious investor sentiment ahead of key inflation data from India and the US...",
          "references": ["bed7d190e0b64f17aa1f77012bf634af"]
        }
      ]
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|---|---|---|
| `clusterId` | string | The story cluster identifier. |
| `createdAt` | string | Timestamp when this history record was created. |
| `name` | string \| null | Story name/title (null if not yet generated). |
| `triggeredAt` | string | Timestamp when the story refresh was triggered. |
| `summary` | string \| null | Full AI-generated summary at this point in time. |
| `shortSummary` | string \| null | Abbreviated summary. |
| `changelog` | string \| null | Description of what changed since the previous snapshot. |
| `keyPoints` | array \| null | Key facts with `point` (string) and `references` (article ID array). |
| `questions` | array \| null | Reader questions with `question`, `answer`, and `references`. |

## Example Requests

### Recent story history

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/history?size=10&sortBy=createdAt"
```

### History for a specific story cluster

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/history?clusterId=911860d569ca464698c0beec0697f694&size=20"
```

### Only records with changelogs

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/history?changelogExists=true&from=2026-01-01&size=10"
```

### History within a date range

```bash
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/stories/history?from=2026-02-01&to=2026-02-15&size=50&sortBy=triggeredAt"
```

## When to Use This Endpoint

- **Story evolution tracking** — See how a story's summary and key points change over time as new information emerges.
- **Changelog monitoring** — Detect when significant new developments occur in a story by filtering with `changelogExists=true`.
- **Historical analysis** — Reconstruct the timeline of how a news event unfolded.
- **Alerting on story updates** — Poll for new history records to trigger notifications when stories are refreshed.

**Use `/v1/stories/all` instead** when you need the current state of stories with full metadata (sentiment, entities, article counts, selected articles). **Use `/v1/articles/all` with `clusterId` instead** when you need the individual articles within a story.
