---
title: "Tạo IAM role theo quyền tối thiểu"
date: 2026-01-01
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

EC2 không dùng access key. Container lấy credential tạm của **instance role** qua IMDSv2. Policy dưới đây chỉ cấp
đúng các thao tác code gọi: worker đọc `quarantine/`, `clean/` và `request.json`, chỉ ghi `clean/` và
`extraction.json`, chỉ xóa trong `quarantine/`; DynamoDB chỉ `GetItem`/`PutItem`/`UpdateItem`/`DescribeTable` trên hai
bảng; Secrets Manager chỉ đọc một secret.

1. Tạo file policy (tên bucket/queue/bảng là tên sẽ tạo ở phần 5.4):


```bash
source ~/cr.env
cat > /tmp/runtime-policy.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {"Sid": "ConsumeJobQueues", "Effect": "Allow",
     "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage", "sqs:ChangeMessageVisibility", "sqs:GetQueueAttributes"],
     "Resource": ["arn:aws:sqs:$AWS_REGION:$ACCOUNT_ID:contract-risk-analysis-requests",
                  "arn:aws:sqs:$AWS_REGION:$ACCOUNT_ID:contract-risk-chat-requests",
                  "arn:aws:sqs:$AWS_REGION:$ACCOUNT_ID:contract-risk-data-update"]},
    {"Sid": "PublishResults", "Effect": "Allow", "Action": "sqs:SendMessage",
     "Resource": "arn:aws:sqs:$AWS_REGION:$ACCOUNT_ID:contract-risk-analysis-results"},
    {"Sid": "ReadJobInputs", "Effect": "Allow", "Action": "s3:GetObject",
     "Resource": ["arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/quarantine/*",
                  "arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/clean/*",
                  "arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/results/*/request.json"]},
    {"Sid": "WriteJobOutputs", "Effect": "Allow", "Action": "s3:PutObject",
     "Resource": ["arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/clean/*",
                  "arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/results/*/extraction.json"]},
    {"Sid": "RemoveQuarantined", "Effect": "Allow", "Action": "s3:DeleteObject",
     "Resource": "arn:aws:s3:::contract-risk-docs-$ACCOUNT_ID/quarantine/*"},
    {"Sid": "LegalBucketList", "Effect": "Allow", "Action": "s3:ListBucket",
     "Resource": "arn:aws:s3:::contract-risk-legal-$ACCOUNT_ID"},
    {"Sid": "LegalBucketRead", "Effect": "Allow", "Action": "s3:GetObject",
     "Resource": "arn:aws:s3:::contract-risk-legal-$ACCOUNT_ID/*"},
    {"Sid": "LegalBucketWrite", "Effect": "Allow", "Action": "s3:PutObject",
     "Resource": "arn:aws:s3:::contract-risk-legal-$ACCOUNT_ID/legal/*"},
    {"Sid": "JobStateTables", "Effect": "Allow",
     "Action": ["dynamodb:GetItem", "dynamodb:PutItem", "dynamodb:UpdateItem", "dynamodb:DescribeTable"],
     "Resource": ["arn:aws:dynamodb:$AWS_REGION:$ACCOUNT_ID:table/contract-risk-jobs",
                  "arn:aws:dynamodb:$AWS_REGION:$ACCOUNT_ID:table/contract-risk-job-events"]},
    {"Sid": "ReadProjectApiKeys", "Effect": "Allow", "Action": "secretsmanager:GetSecretValue",
     "Resource": "arn:aws:secretsmanager:$AWS_REGION:$ACCOUNT_ID:secret:contract-risk/prod/api-keys-*"}
  ]
}
EOF
jq . /tmp/runtime-policy.json > /dev/null && echo "policy JSON OK"
```


2. Tạo role, gắn policy quản lý Session Manager và inline policy, tạo instance profile:


```bash
cat > /tmp/ec2-trust.json <<'EOF'
{"Version": "2012-10-17",
 "Statement": [{"Effect": "Allow", "Principal": {"Service": "ec2.amazonaws.com"}, "Action": "sts:AssumeRole"}]}
EOF
aws iam create-role --role-name ContractRisk-EC2-Role --assume-role-policy-document file:///tmp/ec2-trust.json
aws iam attach-role-policy --role-name ContractRisk-EC2-Role \
  --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore
aws iam put-role-policy --role-name ContractRisk-EC2-Role --policy-name contract-risk-runtime \
  --policy-document file:///tmp/runtime-policy.json
aws iam create-instance-profile --instance-profile-name ContractRisk-EC2-Profile
aws iam add-role-to-instance-profile --instance-profile-name ContractRisk-EC2-Profile \
  --role-name ContractRisk-EC2-Role
```

{{% notice tip %}}
📸 **Ảnh cần chụp:** IAM console → **Roles** → `ContractRisk-EC2-Role` → tab **Permissions**: thấy `AmazonSSMManagedInstanceCore` và inline policy `contract-risk-runtime`.
{{% /notice %}}

![IAM role của EC2](/images/5-Workshop/5.3-Network-security/iam-role.png)

{{% notice tip %}}
📸 **Ảnh cần chụp:** Mở inline policy `contract-risk-runtime` ở chế độ **JSON** (cuộn tới các Sid `ConsumeJobQueues`, `WriteJobOutputs`, `JobStateTables`).
{{% /notice %}}

![Nội dung inline policy](/images/5-Workshop/5.3-Network-security/iam-policy-json.png)


{{% notice tip %}}
Tham chiếu: các policy mẫu trong repo nằm ở `infrastructure/aws/` (`config-secrets-policy.json`,
`mcp-s3-readonly-policy.json`, `data-pipeline-policy.json`). Policy ở trên gộp chúng và thay account/tên tài nguyên
theo bài lab.
{{% /notice %}}
