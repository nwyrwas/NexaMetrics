Title: ADR-002: Lambda Trigger Model
Date: 04/20/2026
Status: Accepted

Context:
NexaMetrics needs a mechanism to trigger Lambda processing when new data arrives in S3. Two options exist: S3 ObjectCreated events (event-driven) or CloudWatch scheduled polling.

Decision:
We will use S3 ObjectCreated events to trigger Lambda immediately when a new file lands in S3.

Consequences:
Lambda only fires when real work exists, resulting in zero wasted invocations. The tradeoff is that S3 and Lambda are tightly coupled — changing the S3 path structure requires updating the Lambda trigger configuration simultaneously.