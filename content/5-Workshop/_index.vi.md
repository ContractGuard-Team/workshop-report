---
title: "Workshop"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Triển khai hệ thống AI phân tích rủi ro hợp đồng đa agent trên AWS

#### Tổng quan

Workshop hướng dẫn triển khai **ContractGuard AI** từ đầu trên một tài khoản AWS: dựng mạng VPC an toàn, tạo IAM role
theo nguyên tắc quyền tối thiểu, tạo hàng đợi SQS, bucket S3, bảng DynamoDB, secret trong Secrets Manager, rồi chạy
9 container Docker và data pipeline pháp luật trên Amazon EC2. Cuối cùng bạn gửi một hợp đồng thử, theo dõi ba agent
xử lý trên Langfuse và dọn dẹp toàn bộ tài nguyên.

Bạn sẽ thực hành:
+ **Mạng và bảo mật:** VPC, subnet, Security Group không mở cổng inbound, VPC Gateway Endpoint cho S3 và DynamoDB,
  IAM instance role với IMDSv2.
+ **Kiến trúc hướng sự kiện:** SQS kèm Dead-Letter Queue, xử lý bất đồng bộ, lease trên DynamoDB chống xử lý trùng.
+ **Quản lý bí mật:** API key nằm trong Secrets Manager và `.env` trên máy, không hard-code trong code hay GitHub.
+ **Giám sát:** trace và token của từng stage trên Langfuse, metrics/alerts của service monitoring.

#### Nội dung

1. [Giới thiệu](5.1-workshop-overview/)
2. [Các bước chuẩn bị](5.2-prerequisite/)
3. [Mạng và bảo mật](5.3-network-security/)
4. [Tài nguyên dữ liệu và bí mật](5.4-data-services/)
5. [Triển khai lên Amazon EC2](5.5-deploy-ec2/)
6. [Data pipeline pháp luật](5.6-data-pipeline/)
7. [Kiểm thử và giám sát](5.7-testing/)
8. [Minh chứng sản phẩm](5.8-demo/)
9. [Sự cố và bài học kinh nghiệm](5.9-troubleshooting/)
10. [Dọn dẹp tài nguyên](5.10-cleanup/)
