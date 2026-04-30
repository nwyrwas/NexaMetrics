Title: ADR-001: S3 Partition Structure
Date: 04/20/2026
Status: Accepted

Context:
NexaMetrics stores raw ingested data in S3 as JSON files. The folder structure determines how easily engineers can navigate, isolate, and reprocess data by source. Without a documented decision, the structure would become inconsistent across contributors and reprocessing would become painful.

Decision:
We will use s3://bucket/raw/{source}/{year}/{month}/{day}/{timestamp}.json rather than {year}/{month}/{day}/{source}/ because isolating a single source for reprocessing requires touching only one top-level directory.

Consequences:
Source-first partitioning enables fast, isolated reprocessing — to reprocess all NewsAPI data, an engineer navigates directly to /newsapi/ without touching other sources. The tradeoff is that cross-source date queries become more complex. Retrieving everything ingested on a specific date requires visiting each source directory separately (e.g. /newsapi/2026/04/20/, /hackernews/2026/04/20/, /alphavantage/2026/04/20/). We accepted this tradeoff because per-source reprocessing is our most common operation, while cross-date queries across all sources are rare.