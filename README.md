# NexaMetrics

A high-volume data ingestion and analytics platform that collects from multiple API sources, processes events through an event-driven AWS pipeline, and surfaces trends through a real-time React dashboard.

## Context

### The Problem
Most junior portfolios show that a developer can implement features. 
What they don't show is architectural thinking before coding — the 
considerations, tradeoffs, and decisions that needed to happen before 
a single line of code is written.

### Why I Built This
Unlike my previous projects, NexaMetrics required me to consider 
tradeoffs and document decisions before writing a single line of code. 
The goal was to demonstrate that I can think like a senior engineer — 
not just build features, but understand what I'm building and why.

- **Architectural Decision Records** — documented every major decision 
before writing code, forcing tradeoff analysis and preventing costly 
reversals later.

- **AWS free-tier capacity planning** — calculated invocation limits 
and storage constraints before any infrastructure decisions, treating 
cost boundaries as architectural inputs rather than afterthoughts.

- **Event-driven architecture** — chose S3 ObjectCreated events over 
CloudWatch polling so Lambda only fires when real work exists, 
eliminating wasted invocations at scale.

- **Data pipeline design** — separating ingestion, processing, and 
serving into distinct layers so that failures are isolated, and changes 
to one layer don't cascade into the others.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 18, Tailwind CSS, Recharts |
| **Backend** | Java 17, Spring Boot 3 |
| **Database** | AWS RDS PostgreSQL |
| **Storage** | AWS S3 (raw data lake) |
| **Processing** | AWS Lambda (event-driven processor) |
| **Monitoring** | AWS CloudWatch |

---

## Project Roadmap

| Phase | Focus | Description | Status |
|-------|-------|-------------|:------:|
| 0 | Purpose & Premise | Defined project brief, success condition, and target user | ✅ |
| 1 | Constraints & Capacity | AWS free-tier limits calculated, invocation math validated | ✅ |
| 2 | Architecture Decision Records | Four ADRs written before any code: S3 partitioning, Lambda trigger model, deduplication strategy, analytics schema | ✅ |
| 3 | Architecture Overview | Full system diagram, data flow documentation | 🔜 |
| 4 | Java Ingestion Service | Spring Scheduler, DataConnector interface, S3Uploader and GitHub CI/CD | 🔜 |
| 5 | Lambda Processor | Event-driven processor, deduplication, RDS writes | 🔜 |
| 6 | RDS Schema | Analytics tables designed for read-heavy dashboard queries | 🔜 |
| 7 | Spring Boot REST API | Pre-aggregated endpoints, versioned API contract | 🔜 |
| 8 | React Dashboard | Recharts visualizations, DateRangePicker, 60s polling | 🔜 |
| 9 | Observability | Structured logging, CloudWatch metrics, operational runbook | 🔜 |
| 10 | Documentation & Finishing | README, architecture diagram, self-postmortem | 🔜 |

---

## Architecture

```
[ASCII diagram goes here in Phase 3]

Spring Scheduler → DataConnectors → S3 → Lambda → RDS → REST API → React Dashboard
```

---

## Architecture Decision Records

Architectural decisions are documented before code is written. Each ADR captures the context, decision, and tradeoffs accepted.

| ADR | Decision | Status |
|-----|----------|:------:|
| [ADR-001](adrs/001-s3-partition-structure.md) | S3 Partition Structure: `source/year/month/day/` | ✅ |
| [ADR-002](adrs/002-lambda-trigger-model.md) | Lambda Trigger: S3 ObjectCreated event-driven | ✅ |
| [ADR-003](adrs/003-deduplication-strategy.md) | Deduplication: Source-provided IDs | ✅ |
| [ADR-004](adrs/004-analytics-schema-shape.md) | Analytics Schema Shape | ✅ |

---

## Phase Progress

### Phase 0 — Purpose & Premise ✅
Defined who NexaMetrics is for, what it proves, and what done looks like measurably. Established the project brief before any architecture decisions were made.

> **Key outcome:** A Principal Software Engineer at a data-heavy firm can open this repo and within 90 seconds conclude that architectural thinking came before implementation.

---

### Phase 1 — Constraints & Capacity ✅
Validated NexaMetrics against AWS free-tier limits before any infrastructure decisions.

- **Lambda:** 4 sources × 48 runs/day = 5,760 invocations/month against a 1,000,000 limit
- **S3:** Estimated ~200MB/month against a 5GB limit
- **RDS:** Single db.t3.micro instance, 750 hours/month, 20GB storage

> **Key outcome:** Constraints treated as architectural inputs, not afterthoughts.

---

### Phase 2 — Architecture Decision Records 🔄
Phase 2 required writing four Architecture Decision Records before any code was written. Each ADR identified the available options, documented the decision made, and acknowledged the tradeoffs accepted when moving forward.

> **Challenge:** Choosing between Lambda processing or CloudWatch Scheduled Polling

> **Solution:** We chose S3 ObjectCreated events to trigger Lambda, ensuring zero wasted invocations. The tradeoff accepted was tight coupling — any change to the S3 path structure requires updating the Lambda trigger configuration simultaneously.

---

## Running Locally

### Prerequisites
- Java 17+
- Maven 3.8+
- Node.js 18+
- AWS CLI v2 (configured with `--profile nexametrics`)

### Backend
```bash
cd src/backend
./mvnw spring-boot:run
```

### Frontend
```bash
cd src/frontend
npm install
npm run dev
```

### Environment Variables
Copy `.env.example` to `.env` and fill in your values. Never commit `.env`.

---

## Production Considerations

[Fill in Phase 10 — this section is what separates this repo from a typical junior portfolio. Examples:]
- Dead Letter Queue for failed Lambda invocations
- SNS push alerts replacing CloudWatch polling
- Athena querying over S3 data lake for ad-hoc analysis
- RDS read replica for dashboard queries under load
- S3 lifecycle policies to archive data older than 90 days
