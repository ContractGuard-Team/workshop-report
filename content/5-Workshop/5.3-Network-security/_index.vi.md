---
title: "Mạng và bảo mật"
date: 2026-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Mạng và bảo mật

Phần này dựng nền cho EC2: một VPC với một subnet đi ra Internet qua Internet Gateway, Security Group **không mở cổng
inbound nào**, hai VPC Gateway Endpoint để lưu lượng S3/DynamoDB không đi qua Internet, và IAM role cấp đúng quyền mà
code thực sự gọi.

#### Nội dung

- [Tạo VPC, subnet và Security Group](5.3.1-create-vpc/)
- [Tạo VPC Gateway Endpoint cho S3 và DynamoDB](5.3.2-gateway-endpoints/)
- [Tạo IAM role theo quyền tối thiểu](5.3.3-iam-role/)
