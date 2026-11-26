# Hướng dẫn hoàn thiện báo cáo

Site Hugo dựng từ template FCAJ (`hugo-theme-learn`). Mỗi trang có hai bản: `_index.vi.md` (tiếng Việt) và
`_index.md` (tiếng Anh). Sửa cả hai bản.

## 1. Những phần bạn cần điền

Tìm mọi chỗ cần điền bằng lệnh sau (mọi giá trị chờ điền nằm trong `[...]`, kèm hộp "✏️ Cần điền"):

```powershell
Select-String -Path content\**\*.md -Pattern "\[(Họ và tên|Tiêu đề|Tên sự kiện|Công việc|điền|fill in|Dán|Paste|Mục tiêu|Objective)" | Select-Object -First 50
```

| Phần | File | Việc cần làm |
| --- | --- | --- |
| Thông tin sinh viên | `content/_index*.md`, `static/images/avatar.png` | Điền thông tin, thay ảnh đại diện |
| Worklog | `content/1-Worklog/1.x-WeekX/` | 12 tuần: mục tiêu, bảng công việc theo ngày, kết quả, tài liệu tham khảo |
| Proposal | `content/2-Proposal/` | Đã xong: sơ đồ kiến trúc và bảng chi phí (từ estimate "OJT" của AWS Pricing Calculator) |
| Blogs | `content/3-BlogsPosted/` | 3 bài: link bài đăng, tóm tắt, nội dung, ảnh |
| Events | `content/4-EventParticipated/` | 3 sự kiện: thông tin, nội dung, ảnh check-in rõ mặt, bài học |
| Workshop | `content/5-Workshop/` | Nội dung đã viết đủ. Chụp các ảnh theo hộp 📸 trong từng trang; điền bảng "Kết quả thực tế" ở 5.7.2 và link video ở 5.8 |
| Tự đánh giá | `content/6-Self-evaluation/` | Thay `☐` thành `✅` cho mỗi tiêu chí, viết nhận xét |
| Feedback | `content/7-Feedback/` | Cảm nhận, mức độ hài lòng, góp ý, có giới thiệu không |

Danh sách đầy đủ các ảnh cần chụp (đường dẫn file + nội dung): [SCREENSHOTS.md](SCREENSHOTS.md). Hiện mỗi ảnh là
ảnh giữ chỗ; lưu ảnh thật đè lên với **đúng tên file**.

## 2. Xem trước trên máy

Cài Hugo **extended 0.134.3** (cùng phiên bản với workflow GitHub Actions), rồi tại thư mục này:

```powershell
winget install Hugo.Hugo.Extended
```

```powershell
hugo server -D
```

Mở `http://localhost:1313/` (tiếng Anh) và `http://localhost:1313/vi/` (tiếng Việt).

## 3. Đưa lên GitHub Pages

1. Tạo repository mới trên GitHub của bạn, đổi `origin` (repo này đang trỏ về template gốc):

```powershell
git remote set-url origin https://github.com/<github-user>/<repo>.git
```

2. Sửa `baseURL` trong `config.toml` thành `https://<github-user>.github.io/<repo>/` và đặt `canonifyURLs = true`
   (nội dung dùng đường dẫn ảnh dạng `/images/...`; với project page có thêm `/<repo>/` thì Hugo cần chuyển chúng
   thành URL đầy đủ). Nếu dùng repo `<github-user>.github.io` hoặc domain riêng thì chỉ cần đổi `baseURL`.
3. Commit và push lên nhánh `main`. Workflow `.github/workflows/hugo.yml` build và đẩy site sang nhánh `gh-pages`.
4. GitHub → **Settings → Pages**: *Source* = Deploy from a branch, *Branch* = `gh-pages`.

## 4. Lưu ý bảo mật

* Không đưa API key, token, Account ID thật vào nội dung hay ảnh chụp.
* Sơ đồ kiến trúc phải do bạn tự vẽ (quy định FCAJ không chấp nhận sơ đồ GenAI tạo 100%). Có thể tham khảo
  `docs/architecture/` trong repository dự án.
