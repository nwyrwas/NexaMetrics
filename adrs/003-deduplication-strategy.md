Title: ADR-003: Deduplication Strategy
Date: 04/20/2026
Status: Accepted

Context:
NexaMetrics ingests from multiple API sources every 30 minutes. The same article will appear in multiple ingestion runs.

Decision:
We will use source-provided IDs as the deduplication key. If an ID has already been processed in RDS, the record is skipped.

Consequences:
Source-provided IDs make the Lambda processor idempotent — processing the same file twice produces the same database state as processing it once. This is critical because Lambda retries on failure. The tradeoff is brittleness: if a source does not provide a stable ID, the deduplication check has nothing to compare against, causing the same article to be inserted into RDS on every ingestion run. Analytics numbers inflate and the dashboard produces inaccurate data.