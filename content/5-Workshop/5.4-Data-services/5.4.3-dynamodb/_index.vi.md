---
title: "Tạo bảng DynamoDB"
date: 2026-01-01
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

`contract-risk-jobs` giữ trạng thái hiện tại của mỗi hợp đồng (khóa `contract:<contract_id>`): runtime, lease, stage,
progress. `contract-risk-job-events` giữ lịch sử, chỉ thêm mới. Worker kiểm đúng key schema lúc khởi động (`DescribeTable`)
và ghi bằng transaction có điều kiện để owner cũ không ghi đè owner mới khi SQS giao lại message.


```bash
source ~/cr.env
aws dynamodb create-table --table-name contract-risk-jobs \
  --attribute-definitions AttributeName=job_id,AttributeType=S \
  --key-schema AttributeName=job_id,KeyType=HASH --billing-mode PAY_PER_REQUEST
aws dynamodb create-table --table-name contract-risk-job-events \
  --attribute-definitions AttributeName=job_id,AttributeType=S AttributeName=event_id,AttributeType=S \
  --key-schema AttributeName=job_id,KeyType=HASH AttributeName=event_id,KeyType=RANGE \
  --billing-mode PAY_PER_REQUEST
aws dynamodb wait table-exists --table-name contract-risk-jobs
aws dynamodb wait table-exists --table-name contract-risk-job-events
aws dynamodb list-tables --query 'TableNames[?starts_with(@, `contract-risk`)]'
```

{{% notice tip %}}
📸 **Ảnh cần chụp:** DynamoDB console → **Tables**: `contract-risk-jobs` (partition key `job_id`) và `contract-risk-job-events` (partition key `job_id`, sort key `event_id`), *Capacity mode* = On-demand, trạng thái **Active**.
{{% /notice %}}

![Hai bảng DynamoDB](/images/5-Workshop/5.4-Data-services/dynamodb-tables.png)
