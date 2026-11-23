# Delivery 3 — Cloud Architecture & Scalability

This folder contains Team 2's AWS cloud architecture and capacity plan for the DoorDash-style food delivery platform.

## Contents

| Path | Description |
|---|---|
| `Delivery_3.pdf` | Assignment statement |
| `group2_delivery3.pdf` | Final Cloud Architecture & Capacity Plan (generated) |
| `src/group2_delivery3.md` | Report source |
| `diagrams/aws_architecture.svg` | Main AWS diagram with official AWS icons |
| `diagrams/aws-icons/` | Official AWS icon SVGs used by the main diagram |
| `diagrams/*.dot` / `*.svg` | Graphviz sources and rendered diagrams |
| `cost/capacity_model.csv` | Per-service capacity arithmetic for initial and 20× workloads |
| `cost/cost_model.csv` | Monthly cost categories for initial and 20× workloads |
| `cost/pricing_sources.md` | Public AWS pricing URLs, unit rates and usage assumptions |

## Key decisions

- Four AWS Regions: `us-east-1`, `us-west-2`, `ca-central-1`, `sa-east-1`, three AZs each.
- Every Delivery 2 service (S-01 – S-15) runs as an independent ECS on Fargate task family.
- Amazon MSK is the durable event backbone; SQS, SNS and EventBridge provide work queues, fan-out and scheduling.
- Managed polyglot data: RDS PostgreSQL, DynamoDB, ElastiCache, OpenSearch, Timestream, S3 and Redshift Serverless.
- 20× growth baseline: 21.7 M req/h → 434 M req/h (120,556 req/s).

## Building

From the repository root:

```sh
cd tools
npm install
npm run build -- delivery_3
```

Set `CHROME_PATH` if a Chromium-based browser is not in a standard location. The build renders every `diagrams/*.dot` file to SVG and prints `src/group2_delivery3.md` to `group2_delivery3.pdf`.
