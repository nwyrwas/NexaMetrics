Title: ADR-004: Analytics Schema Shape
Date: 04/28/2026
Status: Accepted

Context:
NexaMetrics has a read-heavy dashboard that polls RDS every 60 seconds across multiple chart components and date range queries. Two schema philosophies exist: normalized (separate tables for sources, articles, topics, and sentiment joined at query time) and denormalized (one wide table with all fields pre-joined for fast reads). The polling frequency makes read speed the priority over storage efficiency.

Decision:
We will use a denormalized wide table with all fields pre-joined so the dashboard can read from a single table without performing JOINs at query time.

Consequences:
The denormalized approach enables the dashboard to read from a single 
table and retrieve everything it needs instantly, eliminating JOIN 
overhead on every 60-second poll. The tradeoff is redundant data — 
source names and metadata appear in thousands of rows rather than once 
in a normalized table. At our scale, thousands of rows rather than 
billions, this storage cost is unmeasurable and acceptable.