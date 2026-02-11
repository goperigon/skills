# Journalists & Sources — `/v1/journalists` & `/v1/sources/all`

Search journalist profiles and media source databases tracked by Perigon.

---

## Journalists — `/v1/journalists/all` & `/v1/journalists/{id}`

Search Perigon's database of **230,000+ journalists** from around the world, refreshed frequently.

### List Endpoint

```
GET https://api.perigon.io/v1/journalists/all
```

### Get by ID Endpoint

```
GET https://api.perigon.io/v1/journalists/{id}
```

### List Parameters

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Search by name, title, and Twitter bio. Supports Boolean operators and wildcards. |
| `name` | string | Search within journalist names specifically. |
| `twitter` | string | Filter by exact Twitter handle (without `@`). |
| `minMonthlyPosts` | integer | Minimum articles per month. |
| `maxMonthlyPosts` | integer | Maximum articles per month. |
| `updatedAtFrom` | date | Profiles updated after this date. |
| `updatedAtTo` | date | Profiles updated before this date. |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `showNumResults` | boolean | Return total count. |

### Journalist Response

```json
{
  "status": 200,
  "numResults": 12,
  "results": [
    {
      "id": "journalist-abc123",
      "name": "Jane Smith",
      "fullName": "Jane Marie Smith",
      "title": "Senior Technology Reporter",
      "headline": "Covering AI, semiconductors, and big tech",
      "description": "Jane Smith covers technology...",
      "imageUrl": "https://...",
      "avgMonthlyPosts": 25,
      "twitterHandle": "janesmith",
      "twitterBio": "Tech reporter @nytimes. AI, chips, big tech.",
      "linkedinUrl": "https://linkedin.com/in/janesmith",
      "linkedinConnections": 5000,
      "linkedinFollowers": 12000,
      "websiteUrl": "https://janesmith.com",
      "facebookUrl": null,
      "instagramUrl": null,
      "youtubeUrl": null,
      "blogUrl": null,
      "tumblrUrl": null,
      "locations": [{ "country": "us", "state": "NY", "city": "New York" }],
      "topSources": [
        { "name": "nytimes.com", "count": 150 },
        { "name": "wirecutter.com", "count": 20 }
      ],
      "topCategories": [
        { "name": "Tech", "count": 140 },
        { "name": "Business", "count": 30 }
      ],
      "topTopics": [
        { "name": "Artificial Intelligence", "count": 60 },
        { "name": "Semiconductors", "count": 35 }
      ],
      "topCountries": [
        { "name": "us", "count": 160 }
      ],
      "topLabels": [
        { "name": "News", "count": 145 }
      ],
      "updatedAt": "2026-02-01T10:00:00Z"
    }
  ]
}
```

### Example Requests

```bash
# Search journalists by name
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/journalists/all?q=AI+reporter&size=10"

# Find prolific journalists
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/journalists/all?minMonthlyPosts=30&size=10"

# Look up by Twitter handle
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/journalists/all?twitter=kaborczyk&size=1"

# Get journalist by ID
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/journalists/JOURNALIST_ID"
```

---

## Sources — `/v1/sources/all`

Search and filter **200,000+ media sources** available via Perigon. Includes publication metadata, bias ratings, traffic data, and geographic information.

### Endpoint

```
GET https://api.perigon.io/v1/sources/all
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Search by source name or domain. |
| `name` | string | Search by source name or alternative names. Supports Boolean operators and wildcards. |
| `sourceGroup` | string | Filter by curated bundles (`top10`, `top100`, `top50tech`, etc.). |
| `domain` | string[] | Filter by exact domain. |
| `paywall` | boolean | Filter by paywall status. |
| `minMonthlyPosts` | integer | Minimum articles published per month. |
| `maxMonthlyPosts` | integer | Maximum articles published per month. |
| `minMonthlyVisits` | integer | Minimum monthly visitors. |
| `maxMonthlyVisits` | integer | Maximum monthly visitors. |
| `showSubdomains` | boolean | Include subdomains as separate results (default `true`). |
| `sourceLat`, `sourceLon`, `sourceMaxDistance` | float | Geo search for local publications. |
| `sortBy` | string | Sort: `createdAt`, `updatedAt`, `relevance`, `count`, `totalCount`. |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `showNumResults` | boolean | Return total count. |

### Source Response

```json
{
  "status": 200,
  "numResults": 50,
  "results": [
    {
      "id": "source-abc123",
      "name": "The New York Times",
      "domain": "nytimes.com",
      "altNames": ["NYT", "New York Times"],
      "description": "The New York Times is an American daily newspaper...",
      "paywall": true,
      "globalRank": 80,
      "monthlyVisits": 500000000,
      "avgMonthlyPosts": 3000,
      "location": {
        "country": "us",
        "state": "NY",
        "city": "New York"
      },
      "logoFavIcon": { "url": "https://..." },
      "logoSquare": { "url": "https://..." },
      "logoLarge": { "url": "https://..." },
      "adFontesBiasRating": "Middle",
      "allSidesBiasRating": "Lean Left",
      "mbfcBiasRating": "Left-Center",
      "avgBiasRating": "Left-Center",
      "topCategories": [
        { "name": "Politics", "count": 800 },
        { "name": "World", "count": 600 }
      ],
      "topTopics": [
        { "name": "Elections", "count": 200 },
        { "name": "Economy", "count": 180 }
      ],
      "topCountries": [
        { "name": "us", "count": 2500 }
      ],
      "topLabels": [
        { "name": "News", "count": 2800 },
        { "name": "Opinion", "count": 200 }
      ],
      "updatedAt": "2026-02-01T10:00:00Z"
    }
  ]
}
```

### Example Requests

```bash
# Search sources by name
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/sources/all?name=New+York+Times&size=5"

# Browse top 100 sources
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/sources/all?sourceGroup=top100&size=100"

# Find high-traffic tech sources
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/sources/all?sourceGroup=top25tech&minMonthlyVisits=1000000&size=25"

# Local news sources near coordinates
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/sources/all?sourceLat=37.7749&sourceLon=-122.4194&sourceMaxDistance=50&size=20"

# Non-paywalled sources only
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.perigon.io/v1/sources/all?paywall=false&sourceGroup=top100&size=50"
```

---

## When to Use These Endpoints

- **Journalist lookup** — Find journalist IDs to use as filters in article searches (`journalistId`).
- **Source discovery** — Discover and evaluate media sources by traffic, bias, geography.
- **Media monitoring** — Track which journalists and sources cover specific topics.
- **Bias analysis** — Compare bias ratings across sources (Ad Fontes, AllSides, MBFC).
- **Local news** — Find publications near a geographic location.
- **Building source lists** — Curate custom source lists for filtering articles.

**Use journalist IDs and source domains** as filters in `/v1/articles/all` and `/v1/stories/all` for targeted content retrieval.
