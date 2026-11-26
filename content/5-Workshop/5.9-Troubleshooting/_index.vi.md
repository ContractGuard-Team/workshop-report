---
title: "Sự cố và bài học kinh nghiệm"
date: 2026-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Các sự cố đã gặp và cách xử lý

| Sự cố | Nguyên nhân | Cách xử lý |
| --- | --- | --- |
| Container báo `NoCredentialsError`, nhưng `aws` trên host vẫn chạy | IMDS hop limit = 1, request từ container qua bridge network bị chặn | Đặt `HttpPutResponseHopLimit=2`; `preflight.sh` kiểm tra STS từ bên trong container |
| GitHub Push Protection từ chối push | Key Gemini bị ghi thẳng trong `common/settings.py` | Xóa key khỏi code, chỉ đọc `GEMINI_API_KEY` từ `.env` (gitignored), sửa lại commit chưa push; đổi key mới |
| Gemini trả lỗi 400 ở vòng gọi tool thứ hai | Gemini 3 yêu cầu gửi lại `thoughtSignature` của mỗi `functionCall` | Client giữ chữ ký trong tool call và agent đưa lại vào transcript |
| `MALFORMED_FUNCTION_CALL` | Prompt bảo model gọi tool trong lượt không cung cấp tool | Chỉ yêu cầu dùng tool khi agent có tool (Agent 2/3) |
| Tiến độ trên DynamoDB báo lùi từ `agent_3` về `agent_1` | Span `reflect` (long-term memory) chạy sau Agent 3 bị tính là một stage | Monitoring bỏ qua span reflect khi tính tiến độ |
| Request bị 429 dù câu trả lời ngắn | Nhà cung cấp tính `max_output_tokens` vào hạn mức token/phút ngay khi nhận request | Đặt `max_completion_tokens` vừa đủ cho từng agent (32k/64k/32k) |
| Systemd không đọc được source | `ProtectHome=true` chặn thư mục `/home` | Clone vào `/opt/contract-risk-management` |
| Agent LLM có thể đọc bảng nghiệp vụ của web qua Supabase | Bảng app nằm chung schema `public`, secret key bỏ qua RLS | Bảng app chuyển sang schema `app`; MCP chỉ mở các bảng trong `SUPABASE_TABLES` |
| Đổi model embedding làm hỏng tìm kiếm | Số chiều vector khác collection Qdrant cũ | Tạo lại collection 1024 chiều + sparse vector, index lại; ghi rõ "đổi tokenizer = index lại" |

#### Bài học

* Kiểm tra quyền **từ bên trong container**, không chỉ trên host.
* Không bao giờ ghi secret vào code, kể cả khi "chỉ để thử"; bật secret scanning từ đầu.
* Kết quả của LLM là dữ liệu không tin cậy: luôn kiểm bằng code (schema, trích dẫn nguyên văn, điểm do code tính).
* Chỉ xóa message SQS ở bước cuối cùng, sau khi kết quả đã được lưu bền vững.

#### Định hướng phát triển

* Chuyển worker sang Amazon ECS và tự scale theo độ dài hàng đợi.
* Đưa EC2 vào private subnet với Interface Endpoint và NAT Gateway khi ngân sách cho phép.
* Dựng hạ tầng bằng IaC (AWS CDK hoặc Terraform) và CI/CD bằng GitHub Actions với OIDC.
* Bộ eval chất lượng (recall/precision của finding) trên tập hợp đồng có nhãn.
