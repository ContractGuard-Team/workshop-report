---
title: "Data services and secrets"
date: 2026-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Data services and secrets

Create the resources used by the worker and the data pipeline, with exactly the names granted in step 5.3.3.

| Type | Name |
| --- | --- |
| S3 | `contract-risk-docs-<ACCOUNT_ID>`, `contract-risk-legal-<ACCOUNT_ID>` |
| SQS | `contract-risk-analysis-requests`, `contract-risk-analysis-results`, `contract-risk-chat-requests`, `contract-risk-data-update` (each with a `-dlq`) |
| DynamoDB | `contract-risk-jobs`, `contract-risk-job-events` |
| Secrets Manager | `contract-risk/prod/api-keys` |

#### Contents

- [Create S3 buckets](5.4.1-s3/)
- [Create SQS queues with a Dead-Letter Queue](5.4.2-sqs/)
- [Create DynamoDB tables](5.4.3-dynamodb/)
- [Store API keys in Secrets Manager](5.4.4-secrets/)
