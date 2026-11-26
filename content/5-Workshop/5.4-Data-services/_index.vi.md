---
title: "Tài nguyên dữ liệu và bí mật"
date: 2026-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tài nguyên dữ liệu và bí mật

Tạo các tài nguyên mà worker và data pipeline dùng, đúng tên đã cấp quyền ở bước 5.3.3.

| Loại | Tên |
| --- | --- |
| S3 | `contract-risk-docs-<ACCOUNT_ID>`, `contract-risk-legal-<ACCOUNT_ID>` |
| SQS | `contract-risk-analysis-requests`, `contract-risk-analysis-results`, `contract-risk-chat-requests`, `contract-risk-data-update` (mỗi queue có DLQ `-dlq`) |
| DynamoDB | `contract-risk-jobs`, `contract-risk-job-events` |
| Secrets Manager | `contract-risk/prod/api-keys` |

#### Nội dung

- [Tạo S3 bucket](5.4.1-s3/)
- [Tạo hàng đợi SQS kèm Dead-Letter Queue](5.4.2-sqs/)
- [Tạo bảng DynamoDB](5.4.3-dynamodb/)
- [Lưu API key trong Secrets Manager](5.4.4-secrets/)
