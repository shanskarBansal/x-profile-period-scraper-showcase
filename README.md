# X Profile Period Scraper Showcase

[![Showcase](https://img.shields.io/badge/Repository-Showcase-blue)](https://github.com/shanskarBansal/x-profile-period-scraper-showcase)
[![Status](https://img.shields.io/badge/Status-Active-success)](#status)
[![Apify](https://img.shields.io/badge/Built%20For-Apify-black)](#what-this-project-demonstrates)

A public, documentation-only showcase of a production Apify actor that scrapes X (Twitter) profile posts for a user-defined date range with strict ownership checks.

## Jump To

- [What this project demonstrates](#what-this-project-demonstrates)
- [Choose your path](#choose-your-path)
- [How the pipeline works](#how-the-pipeline-works)
- [Try a sample run](#try-a-sample-run)
- [Example output](#example-output)
- [FAQ](#faq)
- [Status](#status)

## What this project demonstrates

- Multi-profile scraping (`profileUrl` or `profileUrls[]`)
- UTC date-window filtering (`fromDate` to `toDate`)
- Profile ownership-safe filtering (prevents cross-profile leakage)
- Canonical redirect-safe links: `https://x.com/i/status/<id>`
- Structured JSON output suitable for analytics pipelines
- Concurrent scraping strategy for higher throughput

## Choose your path

<details>
<summary><b>I am reviewing this as a recruiter / hiring manager</b></summary>

- Problem solved: social-profile scraping reliability and data correctness
- Key quality signal: ownership validation by profile identity, not URL text only
- Engineering signal: output normalization + defensive input validation + stop-condition tuning

</details>

<details>
<summary><b>I am an engineer evaluating implementation quality</b></summary>

- Collection strategy: GraphQL timeline response parsing over DOM-only extraction
- Filtering strategy: strict profile identity checks before persistence
- Output strategy: normalized post object (`author`, `metrics`, `media`, `thumbnail`, redirect-safe URL)
- Scalability strategy: profile concurrency + adaptive scrolling stop logic

</details>

<details>
<summary><b>I want this customized for my use case</b></summary>

Possible variants:
- queue-based multi-account orchestration
- CSV-first export workflow
- webhook notifications per completed profile
- cloud scheduling and periodic snapshots

Open an issue in this repo with your use case.

</details>

## How the pipeline works

```mermaid
flowchart LR
  A[Input: profile URL(s) + date range] --> B[Validate URL + date window]
  B --> C[Open profile timeline]
  C --> D[Parse GraphQL timeline responses]
  D --> E[Ownership check per post]
  E --> F[Date filter in UTC]
  F --> G[Normalize output + thumbnail + redirectLink]
  G --> H[Dataset + OUTPUT summary]
```

## Try a sample run

<details>
<summary><b>Sample input JSON</b></summary>

```json
{
  "profileUrls": ["https://x.com/narendramodi", "https://x.com/jack"],
  "fromDate": "2026-01-01",
  "toDate": "2026-02-01",
  "profileConcurrency": 2,
  "maxPosts": 0,
  "maxScrolls": 300,
  "scrollDelayMs": 1000
}
```

</details>

<details>
<summary><b>What you should expect in results</b></summary>

- `requestedProfileUsername` and `requestedProfileUserId` attached per row
- `redirectLink` points to `x.com/i/status/<id>`
- `thumbnail` present when media exists
- out-of-range and foreign-owner posts excluded

</details>

## Example output

```json
{
  "id": "2017989651190165690",
  "postUrl": "https://x.com/narendramodi/status/2017989651190165690",
  "redirectLink": "https://x.com/i/status/2017989651190165690",
  "createdAt": "2026-02-01T15:53:19.000Z",
  "text": "...",
  "thumbnail": "https://pbs.twimg.com/...jpg",
  "author": {
    "id": "18839785",
    "username": "narendramodi"
  },
  "metrics": {
    "likes": 20718,
    "reposts": 4105,
    "replies": 1660,
    "quotes": 141,
    "views": 5175985
  },
  "requestedProfileUsername": "narendramodi",
  "requestedProfileUserId": "18839785",
  "requestedProfileUrl": "https://x.com/narendramodi"
}
```

## FAQ

<details>
<summary><b>Why use redirectLink if postUrl already exists?</b></summary>

`postUrl` is human-readable. `redirectLink` (`x.com/i/status/<id>`) is canonical and safer for ownership-consistent resolution.

</details>

<details>
<summary><b>Can this run for multiple profiles in one execution?</b></summary>

Yes. Use `profileUrls[]` and tune `profileConcurrency`.

</details>

<details>
<summary><b>Does it support private/protected profiles?</b></summary>

No, public scraping cannot access private/protected profile content.

</details>

## Status

- Production actor exists in a separate repository (private)
- This repository is intentionally README-only for public showcase/portfolio
