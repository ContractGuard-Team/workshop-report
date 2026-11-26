---
title: "Triển khai lên Amazon EC2"
date: 2026-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Triển khai lên Amazon EC2

Một EC2 chạy toàn bộ phần AI: 9 container Docker Compose dùng chung network `backend`, cộng 2 service systemd của
data pipeline (phần 5.6). Máy không mở cổng nào; bạn vào máy bằng Session Manager.

#### Nội dung

- [Khởi tạo EC2 với IMDSv2 và instance role](5.5.1-launch-ec2/)
- [Cài đặt và cấu hình](5.5.2-configure/)
- [Build và chạy 9 service](5.5.3-run-services/)
