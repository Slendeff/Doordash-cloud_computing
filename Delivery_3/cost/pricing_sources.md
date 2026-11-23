# Pricing sources and cost assumptions

Access date for all prices below: **2026-11-22**.

## Sources

- AWS Pricing Calculator: https://calculator.aws/
- AWS Pricing Calculator export documentation: https://docs.aws.amazon.com/pricing-calculator/latest/userguide/export-estimate.html
- AWS Fargate pricing: https://aws.amazon.com/fargate/pricing/
- Amazon RDS pricing: https://aws.amazon.com/rds/pricing/
- Amazon DynamoDB pricing: https://aws.amazon.com/dynamodb/pricing/
- Amazon ElastiCache pricing: https://aws.amazon.com/elasticache/pricing/
- Amazon S3 pricing: https://aws.amazon.com/s3/pricing/
- Amazon MSK pricing: https://aws.amazon.com/msk/pricing/
- Amazon CloudWatch pricing: https://aws.amazon.com/cloudwatch/pricing/
- AWS WAF pricing: https://aws.amazon.com/waf/pricing/
- AWS Secrets Manager pricing: https://aws.amazon.com/secrets-manager/pricing/
- AWS KMS pricing: https://aws.amazon.com/kms/pricing/
- AWS Architecture Icons: https://aws.amazon.com/architecture/icons

## Method

`cost_model.csv` reproduces the public on-demand list prices published by the AWS Pricing Calculator. The calculator has no public API; a saved share link or PDF/CSV export can only be produced through its web UI. The figures here use the same entry parameters: service, Region baseline (`us-east-1`), instance/task size, quantity, and monthly hours (730).

All unit prices are normalized to **us-east-1 public on-demand rates**. Regional differences for `us-west-2`, `ca-central-1` and `sa-east-1`, taxes, Free Tier, Savings Plans and Reserved Instances are intentionally excluded. This makes the estimate reproducible and conservative for planning; the calculator should be used again before procurement.

## Key unit rates used

| Item | Unit rate |
|---|---:|
| Fargate vCPU | $0.04048 / vCPU-hour |
| Fargate memory | $0.004445 / GB-hour |
| RDS PostgreSQL db.m6g.large Multi-AZ | $0.352 / hour |
| RDS PostgreSQL db.m6g.2xlarge Multi-AZ | $1.408 / hour |
| RDS PostgreSQL db.m6g.large read replica | $0.176 / hour |
| RDS gp3 storage | $0.115 / GB-month |
| DynamoDB on-demand write | $1.25 / million WCU |
| DynamoDB on-demand read | $0.25 / million RCU |
| DynamoDB storage | $0.25 / GB-month |
| ElastiCache cache.r6g.large | $0.156 / node-hour |
| S3 Standard | $0.023 / GB-month |
| MSK kafka.m5.large | $0.21 / broker-hour |
| MSK storage | $0.10 / GB-month |
| NAT Gateway | $0.045 / hour + $0.045 / GB processed |
| ALB | $0.0225 / hour + $0.008 / LCU-hour |
| SQS standard requests | $0.40 / million |
| SNS publish | $0.50 / million |
| EventBridge events | $1.00 / million |
| CloudWatch Logs ingestion | $0.50 / GB |
| CloudWatch Logs storage | $0.03 / GB-month |
| CloudWatch custom metric / alarm | $0.30 / $0.10 per month |
| X-Ray traces recorded | $5.00 / million |
| WAF | $5 / web ACL + $1 / rule + $0.60 / million requests |
| Secrets Manager | $0.40 / secret-month |
| KMS | $1.00 / key-month |

## Usage assumptions

- Effective Fargate tasks billed per month = `average tasks + 0.125 × (peak tasks − average tasks)`; 12.5% represents the 3-hour dinner peak.
- RDS: 6 service-owned PostgreSQL clusters per Region, Multi-AZ. Initial class `db.m6g.large`; growth class `db.m6g.2xlarge` for sharded Order/Payment clusters.
- DynamoDB: on-demand capacity for Dasher state, assignments, latest positions, tracking views and channel registry.
- OpenSearch: 2 `m6g.large.search` nodes per Region initially, 8 at 20×.
- Timestream: 16.3M GPS writes/hour baseline, 90-day magnetic retention, 20× at growth.
- Redshift Serverless: 1 RPU baseline, 8 RPU at growth.
- ElastiCache: 3 Redis clusters per Region × 2 nodes initially, × 8 nodes at 20×.
- S3: 8.5 TB launch data plus 54 TB/year of operational archives; growth scales this by 20×.
- Networking: 12 NAT Gateways (3 AZs × 4 Regions); 80% of object/API egress served by CloudFront, 10 KB mean response size.
- Messaging: 21.7M events/hour baseline at $0.40/million SQS requests, with SNS notification and EventBridge operational events added separately. MSK uses 3 `kafka.m5.large` brokers per Region initially (12 total), 12 per Region at growth (48 total), plus 3 TB / 60 TB broker storage.
- Monitoring: 1 KB structured log per request, 5% X-Ray sampling, 150 metrics and 60 alarms.
- Security: 4 WAF web ACLs, 40 rules, HTTP API requests at 7.5M/hour, 40 secrets and 50 KMS keys.
