# 🛡️ Brute-Force Defender Lab

Một hệ thống thực hành (Lab) tự động hóa quy trình giám sát, phát hiện và ngăn chặn các cuộc tấn công Brute Force vào Web Server thông qua phân tích Log thời gian thực (Real-time Log Analysis).

---

## 📑 Mục lục
- [Giới thiệu](#-giới-thiệu)
- [Kiến trúc Hệ thống](#-kiến-trúc-hệ-thống)
- [Cấu trúc dự án](#-cấu-trúc-dự-án)
- [Hướng dẫn Triển khai](#-hướng-dẫn-triển-khai)
- [Hình ảnh Demo](#-hình-ảnh-demo)

---

## 💡 Giới thiệu

Dự án này là một mô hình giả lập thu nhỏ của hệ thống IPS (Intrusion Prevention System) được đóng gói hoàn toàn bằng Docker. Hệ thống bao gồm 3 thành phần cốt lõi:

1. **Nginx Server (Mục tiêu):** Đóng vai trò là Web Server đang vận hành, ghi nhận mọi truy cập vào file `access.log` và được cấu hình sẵn các "bẫy" (Honeypot) tại các đường dẫn nhạy cảm.
2. **Attacker Bot (Kẻ tấn công):** Một script tự động hóa liên tục bắn phá (spam requests) vào Web Server để giả lập hành vi rà quét thư mục và Brute Force.
3. **Security Watcher (Hệ thống phòng thủ):** "Bộ não" bằng Python hoạt động độc lập, liên tục đọc log của Nginx. Khi phát hiện IP có hành vi khả nghi vượt ngưỡng cho phép, nó tự động cập nhật danh sách đen, ép Nginx chặn IP đó và ngay lập tức gửi cảnh báo về điện thoại qua Telegram.

---

## 🏗️ Kiến trúc Hệ thống

### 1. Vấn đề giải quyết là gì?
Các hệ thống Web luôn phải đối mặt với các luồng traffic rác hoặc bot tự động quét lỗ hổng liên tục. Việc chặn thủ công là bất khả thi. Dự án này giải quyết bài toán tự động hóa quy trình **Detect & Respond** (Phát hiện & Phản hồi) với độ trễ chỉ tính bằng mili-giây, giúp bảo vệ tài nguyên máy chủ.

### 2. Tại sao chọn Docker, Nginx và Python?
* **Docker:** Đảm bảo tính cô lập, dễ dàng tái tạo môi trường (reproducible) và triển khai nhanh chóng (Deploy anywhere) mà không lo xung đột thư viện trên máy host.
* **Nginx:** Nhẹ, hiệu năng cao, hỗ trợ cấu hình Access Control List (ACL) linh hoạt thông qua lệnh `deny`.
* **Python:** Cung cấp các cấu trúc dữ liệu tối ưu (như `collections.deque` cho Sliding Window) giúp xử lý file log dung lượng lớn theo thời gian thực mà không bị quá tải RAM, đồng thời hệ sinh thái thư viện phong phú giúp gọi API Telegram và giao tiếp với Docker Socket cực kỳ dễ dàng.

### 3. Luồng hoạt động chính (Workflow)
1. **Attacker Bot** gửi request lỗi (HTTP 403/404) liên tục vào **Nginx Server**.
2. **Nginx** ghi lại lịch sử truy cập (kèm IP) vào file `access.log`.
3. **Security Watcher** đọc luồng dữ liệu mới từ log (Tail -f).
4. Nếu 1 IP vượt ngưỡng (VD: 10 lỗi trong 60 giây), **Security Watcher** ghi IP đó vào file `block_ips.conf`.
5. **Security Watcher** gọi API qua Docker Socket để ra lệnh Nginx reload lại cấu hình. Nginx chính thức "cấm cửa" IP.
6. **Security Watcher** gửi tín hiệu cảnh báo chứa IP bị chặn đến Telegram của quản trị viên.

---

## 📂 Cấu trúc dự án

Dự án được phân chia rõ ràng theo nguyên tắc Microservices:

```text
brute-force-defender/
├── docker-compose.yml              # File điều phối hạ tầng trung tâm
├── README.md                       # Tài liệu dự án
├── .env                            # Chứa biến môi trường (Telegram Token)
│
├── nginx-server/                   # Component Web Server
│   ├── Dockerfile
│   └── nginx.conf                  # Cấu hình Nginx gốc
│
├── security-watcher/               # Component Hệ thống phòng thủ
│   ├── Dockerfile
│   ├── requirements.txt
│   └── watcher.py                  # Core logic xử lý log và chặn IP
│
├── attacker-bot/                   # Component Giả lập tấn công
│   ├── Dockerfile
│   └── attack.sh                   # Script bắn request liên tục
│
├── shared_logs/                    # Volume chia sẻ file access.log
└── shared_config/
    └── block_ips.conf              # Volume chia sẻ danh sách đen (ACL)
```



## 🚀 Hướng dẫn Triển khai
Yêu cầu hệ thống
Hệ điều hành: Ubuntu / Linux Distribution.

Đã cài đặt Docker và Docker Compose.

Bước 1: Clone mã nguồn
Tải toàn bộ dự án về máy trạm của bạn:

Bash
git clone <đường-dẫn-repo-của-bạn>
cd brute-force-defender
Bước 2: Cấu hình Telegram
Hệ thống cần thông tin Bot Telegram để gửi cảnh báo. Mở file .env bằng trình chỉnh sửa nano:

Bash
nano .env
Thay thế các giá trị mặc định bằng thông tin thật của bạn:

Code snippet
TELEGRAM_TOKEN=123456789:ABCDefghIJKLmnopQRSTuvwxyz
TELEGRAM_CHAT_ID=-1001234567890
(Nhấn Ctrl+O -> Enter để lưu, sau đó Ctrl+X để thoát nano).

Bước 3: Khởi chạy hệ thống
Sử dụng Docker Compose để build và chạy toàn bộ 3 dịch vụ cùng lúc. Quá trình này sẽ tự động tải các gói phụ thuộc và thiết lập mạng:

Bash
sudo docker-compose up --build
Lưu ý: Giữ nguyên Terminal để theo dõi log trực tiếp. Hệ thống phòng thủ sẽ bắt đầu đếm lỗi, khi đạt ngưỡng, bạn sẽ thấy log báo chặn IP và tin nhắn Telegram sẽ lập tức báo về điện thoại.

Bước 4: Dọn dẹp & Reset môi trường
Sau khi test xong, mở một Terminal mới (hoặc nhấn Ctrl+C ở Terminal cũ) và gõ lệnh sau để tắt các container một cách an toàn:

Bash
sudo docker-compose down
Để dọn dẹp danh sách đen (xóa các IP đã bị block để lần sau test lại từ đầu), chạy lệnh sau để làm rỗng file cấu hình:

Bash
sudo sh -c 'echo -n > shared_config/block_ips.conf'

## 📸 Hình ảnh Demo
(Chèn hình ảnh Terminal lúc Bot đang tấn công và Nginx đang ghi log)

(Chèn hình ảnh Terminal hiển thị Security Watcher phát hiện và ra lệnh Block)

(Chèn hình ảnh chụp màn hình tin nhắn cảnh báo gửi về điện thoại qua Telegram)
