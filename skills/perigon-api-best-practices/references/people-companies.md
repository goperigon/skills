# People & Companies — `/v1/people/all` & `/v1/companies/all`

Search and retrieve entity data for people and companies tracked by Perigon. Use these endpoints to look up metadata, resolve identities, and enrich article data.

---

## People — `/v1/people/all`

Search Perigon's database of **650,000+ people** from around the world. People data is derived from Wikidata and refreshed frequently.

### Endpoint

```
GET https://api.goperigon.com/v1/people/all
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Search by name, description, or aliases. Supports Boolean operators and wildcards. |
| `name` | string | Search within person names specifically. |
| `wikidataId` | string[] | Filter by Wikidata IDs (e.g., `Q76` for Barack Obama). |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `showNumResults` | boolean | Return total count. |

### Response

```json
{
  "status": 200,
  "numResults": 5,
  "results": [
    {
      "name": "Barack Obama",
      "wikidataId": "Q76",
      "description": "44th president of the United States",
      "abstract": "Barack Hussein Obama II is an American politician...",
      "aliases": ["Barack Hussein Obama", "Obama"],
      "image": { "url": "https://..." },
      "gender": { "label": "male", "wikidataId": "Q6581097" },
      "dateOfBirth": { "time": "+1961-08-04T00:00:00Z", "precision": "day" },
      "dateOfDeath": null,
      "occupation": [
        { "label": "politician", "wikidataId": "Q82955" },
        { "label": "lawyer", "wikidataId": "Q40348" }
      ],
      "position": [
        {
          "label": "President of the United States",
          "wikidataId": "Q11696",
          "startTime": { "time": "+2009-01-20T00:00:00Z", "precision": "day" },
          "endTime": { "time": "+2017-01-20T00:00:00Z", "precision": "day" },
          "employer": { "label": "United States of America", "wikidataId": "Q30" }
        }
      ],
      "politicalParty": [
        { "label": "Democratic Party", "wikidataId": "Q29552" }
      ],
      "createdAt": "2024-01-15T10:00:00Z",
      "updatedAt": "2026-01-30T08:00:00Z"
    }
  ]
}
```

### Example Requests

```bash
# Search for a person by name
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/people/all?q=Elon+Musk&size=5"

# Look up by Wikidata ID
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/people/all?wikidataId=Q317521&size=1"
```

---

## Companies — `/v1/companies/all`

Browse and search companies tracked by Perigon. Supports filtering by name, domain, ticker symbol, industry, country, exchange, employee count, and more.

### Endpoint

```
GET https://api.goperigon.com/v1/companies/all
```

### Parameters

| Parameter | Type | Description |
|---|---|---|
| `q` | string | Search by name, description, or alternative names. Supports Boolean operators and wildcards. |
| `name` | string | Search by company name. |
| `domain` | string[] | Filter by company domains (e.g., `apple.com`). |
| `symbol` | string[] | Filter by stock ticker symbols (e.g., `AAPL`). |
| `id` | string[] | Filter by Perigon company IDs. |
| `industry` | string | Filter by industry. |
| `sector` | string | Filter by sector. |
| `country` | string[] | Filter by country code. |
| `exchange` | string | Filter by stock exchange. |
| `page` | integer | Page number (starts at 0). |
| `size` | integer | Results per page. |
| `showNumResults` | boolean | Return total count. |

### Response

```json
{
  "status": 200,
  "numResults": 3,
  "results": [
    {
      "id": "company-abc123",
      "name": "Apple Inc.",
      "description": "Apple Inc. is an American multinational technology company...",
      "domains": ["apple.com"],
      "altNames": ["Apple", "Apple Computer"],
      "symbols": [
        {
          "symbol": "AAPL",
          "exchange": "NASDAQ",
          "exchangeShortName": "NASDAQ",
          "ipoDate": "1980-12-12"
        }
      ],
      "industry": "Consumer Electronics",
      "sector": "Technology",
      "country": "US",
      "state": "CA",
      "city": "Cupertino",
      "address": "One Apple Park Way",
      "zip": "95014",
      "ceo": "Tim Cook",
      "fullTimeEmployees": 164000,
      "yearFounded": 1976,
      "revenue": "$394.3B",
      "logo": "https://...",
      "favicon": "https://...",
      "globalRank": 5,
      "monthlyVisits": 2500000000,
      "isActivelyTrading": true,
      "isEtf": false,
      "isFund": false,
      "isAdr": false,
      "cik": "0000320193",
      "isin": "US0378331005",
      "cusip": "037833100",
      "naics": "334220",
      "sic": "3571",
      "webResources": {
        "about": "https://www.apple.com/about/",
        "careers": "https://www.apple.com/careers/",
        "linkedin": "https://www.linkedin.com/company/apple/",
        "x": "https://x.com/Apple",
        "wikipedia": "https://en.wikipedia.org/wiki/Apple_Inc.",
        "youtube": "https://www.youtube.com/apple"
      },
      "updatedAt": "2026-02-01T10:00:00Z"
    }
  ]
}
```

### Example Requests

```bash
# Search for a company by name
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/companies/all?q=Tesla&size=5"

# Look up by stock symbol
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/companies/all?symbol=AAPL&size=1"

# Look up by domain
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/companies/all?domain=openai.com&size=1"

# Filter by industry and country
curl -H "x-api-key: $PERIGON_API_KEY" \
  "https://api.goperigon.com/v1/companies/all?industry=Semiconductors&country=us&size=20"
```

---

## When to Use These Endpoints

- **Entity resolution** — Resolve person names to Wikidata IDs, company names to domains/symbols.
- **Enrichment** — Add biographical or corporate metadata to article data.
- **Filtering** — Get IDs to use with the articles/stories endpoints (`personWikidataId`, `companyId`, `companyDomain`).
- **Company intelligence** — Look up financial metadata, employee counts, web resources.
- **People research** — Find occupations, positions, political affiliations.

**Use article filters** (`personName`, `companySymbol`, etc.) to search articles about people/companies. Use these entity endpoints to look up the entities themselves.
