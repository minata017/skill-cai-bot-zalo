---
name: cai-bot-zalo
description: Hướng dẫn Trợ lý AI tự động khởi tạo, cài đặt thư viện, nạp kịch bản bán hàng, cấu hình xoay vòng API Gemini, hẹn giờ nhắc lịch và xử lý lỗi cho Bot Zalo AI trên máy tính cá nhân.
---

# Skill: Tự Động Hóa Cài Đặt & Vận Hành Bot Zalo AI

Skill này trang bị cho Trợ lý AI (Google Antigravity, Claude Code, OpenAI Codex) quy trình chuẩn mực để khởi tạo, nạp tri thức và vận hành một Bot Zalo AI trên máy tính cá nhân hoạt động bền bỉ, mượt mà và không bị kẹt lỗi.

---

## 1. Khi nào dùng Skill này?
Kích hoạt skill này khi người dùng yêu cầu:
- "Tạo cho tôi một con bot Zalo tự động trả lời tin nhắn"
- "Cài đặt bot Zalo AI bằng Gemini / Claude / OpenAI"
- "Thêm tính năng nhắc lịch / hẹn giờ cho bot Zalo"
- "Cấu hình xoay vòng nhiều API key để nhân hạn mức miễn phí"
- "Sửa lỗi bot Zalo bị đơ (QuickEdit) / lỗi 429 quota / lỗi mã QR hết hạn"
- "Nạp thêm kịch bản bán hàng hoặc kết nối bảng giá Google Sheets cho bot"

---

## 2. Quy trình 6 bước thực thi chuẩn từ A - Z

### Bước 1: Kiểm tra môi trường hệ thống
Chạy lệnh kiểm tra Node.js trên máy người dùng:
```bash
node -v
npm -v
```
- Nếu máy chưa cài Node.js: Hướng dẫn người dùng tải và cài đặt bản LTS tại `nodejs.org` trước khi tiếp tục.

### Bước 2: Khởi tạo thư mục và cấu trúc dự án
Tạo thư mục dự án `zalo-bot` và tạo file `package.json` chuẩn ES Module:
```json
{
  "name": "zalo-bot",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.mjs"
  }
}
```

Cài đặt các thư viện lõi:
```bash
npm install zca-js dotenv @google/genai qrcode-terminal node-cron
```

### Bước 3: Thiết lập cấu hình môi trường (.env)
Tạo file `.env` hỗ trợ cả 1 key hoặc nhiều key xoay vòng:
```env
# API Key của Google Gemini (ngăn cách bằng dấu phẩy nếu có nhiều key để nhân hạn mức)
GEMINI_API_KEYS=your_gemini_api_key_here

# Tuỳ chọn nếu dùng Claude hoặc OpenAI
ANTHROPIC_API_KEY=
OPENAI_API_KEY=
AI_ENGINE=gemini
```

### Bước 4: Nạp kịch bản tri thức (kien-thuc.txt)
Tạo file `kien-thuc.txt` đóng vai trò là não bộ thông tin của Bot:
- Định hình nhân viên tư vấn: Xưng "Em", gọi khách là "Anh/Chị", dạ thưa lễ phép.
- Thông tin sản phẩm, bảng giá niêm yết, chính sách bảo hành và đổi trả.
- Kịch bản xử lý từ chối giá và chốt đơn khéo léo.

### Bước 5: Viết mã nguồn lõi index.mjs
Mã nguồn phải đáp ứng các tiêu chuẩn kỹ thuật sau:
1. Đăng nhập Zalo bằng mã QR in ra màn hình console bằng `qrcode-terminal`.
2. Lắng nghe tin nhắn mới: Hỗ trợ cả chat riêng 1-1 và chat trong nhóm (khi được tag @tên hoặc trả lời trích dẫn).
3. Đọc dữ liệu từ `kien-thuc.txt` làm bối cảnh hệ thống (System Instruction).
4. Cơ chế Round-Robin xoay vòng danh sách `GEMINI_API_KEYS` khi gặp mã lỗi `429 (Too Many Requests / Quota Exceeded)`.
5. Tích hợp tính năng nhắc lịch: Nhận diện tin nhắn hẹn giờ, lưu vào `lich-nhac.json`, hẹn giờ gửi tin nhắn Zalo đúng hạn.

### Bước 6: Khởi động và hướng dẫn người dùng
Chạy lệnh:
```bash
npm start
```
Hướng dẫn người dùng mở ứng dụng Zalo trên điện thoại, bấm Quét mã QR để đăng nhập.

---

## 3. Bác sĩ bắt bệnh: Xử lý 4 lỗi hay gặp nhất

1. **Lỗi Terminal bị đơ (QuickEdit Mode):**
   - *Hiện tượng:* Tiêu đề terminal có chữ `Select`, bot ngừng phản hồi tin nhắn.
   - *Khắc phục:* Bấm phím `ESC` hoặc `Enter`. Hướng dẫn tắt vĩnh viễn: Chuột phải tiêu đề PowerShell -> Properties -> Bỏ chọn QuickEdit Mode -> OK.
2. **Lỗi 429 Quota Exceeded:**
   - *Hiện tượng:* Hết 5 tin nhắn/phút hoặc 500 tin/ngày của gói Gemini miễn phí.
   - *Khắc phục:* Tự động kích hoạt đổi key trong danh sách `GEMINI_API_KEYS` hoặc hướng dẫn thêm thẻ Visa kích hoạt gói Pay-As-You-Go.
3. **Lỗi mã QR bị vỡ nét hoặc hết hạn:**
   - *Khắc phục:* Bấm `Ctrl + C` để dừng tiến trình, sau đó gõ lại `npm start` để sinh mã QR mới và quét ngay trong 60 giây.
4. **Lỗi thiếu quyền Administrator trên Windows:**
   - *Khắc phục:* Mở PowerShell bằng quyền Quản trị viên (Run as administrator) hoặc bật Developer Mode trong Windows Settings.
