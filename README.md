# 🛡️ Brute-Force Defender & Telegram Alert System

Một hệ thống lab giả lập bằng Docker chuyên dụng để giám sát, phát hiện và tự động ngăn chặn các cuộc tấn công Brute Force, đồng thời **gửi cảnh báo tức thời qua Telegram**.

## 💡 Giới thiệu

Hệ thống được thiết kế tối giản với 3 thành phần cốt lõi:
* **Nginx Server (Mục tiêu):** Web Server chạy honeypot, ghi nhận mọi truy cập rác vào file log.
* **Attacker Bot (Kẻ tấn công):** Script tự động liên tục "bắn" request lỗi vào server để giả lập tấn công.
* **Security Watcher (Phòng thủ & Cảnh báo):** Trái tim của hệ thống. Script Python quét log Nginx liên tục (real-time). Khi phát hiện IP có dấu hiệu tấn công, nó sẽ tự động khóa IP đó và **đặc biệt: bắn ngay một cảnh báo chi tiết về điện thoại của bạn qua Telegram**.

## ⚙️ Kiến trúc & Luồng hoạt động (Workflow)

1. **Ghi Log:** Attacker Bot gửi request -> Nginx Server trả lỗi 403/404 và ghi trực tiếp vào `access.log`.
2. **Đọc Log:** Security Watcher sử dụng kỹ thuật "tail -f" để quét file log theo thời gian thực.
3. **Block IP:** Nếu phát hiện 1 IP vi phạm vượt ngưỡng (ví dụ: 10 lỗi/phút), Watcher tự động ghi IP đó vào danh sách đen (`block_ips.conf`) và ép Nginx reload để cắt đứt kết nối.
4. **Báo động (Telegram):** Ngay khoảnh khắc IP bị block, Watcher sẽ gọi API để gửi tin nhắn thông báo khẩn cấp đến Telegram của quản trị viên.

## 📂 Cấu trúc dự án

```text
brute-force-defender/
├── docker-compose.yml
├── README.md
├── .env                            # Lưu TELEGRAM_TOKEN và TELEGRAM_CHAT_ID
├── nginx-server/
│   ├── Dockerfile
│   └── nginx.conf
├── security-watcher/
│   ├── Dockerfile
│   ├── requirements.txt
│   └── watcher.py                  # Script phân tích log & gửi Telegram
├── attacker-bot/
│   ├── Dockerfile
│   └── attack.sh
├── shared_logs/                    # Chứa access.log của Nginx
└── shared_config/
    └── block_ips.conf              # Danh sách IP bị chặn (ACL)

```

## 🚀 Hướng dẫn Cài đặt & Sử dụng

Dưới đây là các lệnh triển khai nhanh gọn nhất. Bạn chỉ cần chạy lần lượt:

1) git clone [https://github.com/Dungsocool/brute-force-defender.git](https://github.com/Dungsocool/brute-force-defender.git)
2) cd brute-force-defender/
3) nano .env  # (Ghi chú: Điền TELEGRAM_TOKEN và TELEGRAM_CHAT_ID của bạn vào đây)
4) sudo docker-compose up --build

🧹 Dọn dẹp hệ thống (Reset)
Để tắt hệ thống và xóa sạch danh sách IP đã bị chặn (chuẩn bị cho lần test tiếp theo), hãy chạy 2 lệnh sau:

sudo docker-compose down
sudo sh -c 'echo -n > shared_config/block_ips.conf'

## 📸 Hình ảnh Demo

---
