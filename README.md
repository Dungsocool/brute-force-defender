# 🛡️ Brute-Force Defender Lab

Một hệ thống lab giả lập hoàn chỉnh dựa trên Docker, cho phép mô phỏng các cuộc tấn công Brute-Force/DDoS quy mô nhỏ và tự động phát hiện, ngăn chặn IP của kẻ tấn công theo thời gian thực.

---

## 📄 Mục lục

* [1. Giới thiệu Dự án](#-1-giới-thiệu-dự-án)
* [2. Kiến trúc Hệ thống (Phần 2)](#-2-kiến-trúc-hệ-thống-phần-2)
* [3. Cơ chế hoạt động](#-3-cơ-chế-hoạt-động)
* [4. Cấu trúc dự án](#-4-cấu-trúc-dự-án)
* [5. Hướng dẫn Triển khai (Phần 1)](#-5-hướng-dẫn-triển-khai-phần-1)
* [6. Hình ảnh Demo](#-6-hình-ảnh-demo)

---

## 💡 1. Giới thiệu Dự án

**Brute-Force Defender** là một bài lab an toàn thông tin được thiết kế để giúp bạn hiểu rõ cách thức một hệ thống phát hiện và phản hồi lại các lưu lượng truy cập bất thường. Dự án bao gồm 3 thành phần chính:

1. **Nginx Web Server**: Mục tiêu bị tấn công.
2. **Attacker Bot**: Kịch bản giả lập tấn công liên tục.
3. **Security Watcher**: Hệ thống phòng ngự chủ động (Blue Team) bằng Python.

---

## 🏗️ 2. Kiến trúc Hệ thống (Phần 2)

**Vấn đề chọn giải quyết là gì?**
Các máy chủ web thường xuyên phải đối mặt với các cuộc tấn công dò mật khẩu (Brute-force) hoặc rải request liên tục (DDoS Layer 7). Thay vì phụ thuộc vào các giải pháp Tường lửa (WAF) đắt tiền, dự án này xây dựng cơ chế phòng vệ chủ động ngay tại Web Server.

**Tại sao chọn tech stack này?**
* **Docker & Docker Compose:** Đóng gói hoàn hảo môi trường, giúp triển khai cực kỳ nhanh chóng và không bị xung đột.
* **Nginx:** Thao tác chặn IP trực tiếp ở tầng Nginx mang lại hiệu năng cao, giảm tải cho hệ thống phía sau.
* **Python:** Ngôn ngữ tối ưu để đọc, phân tích file log liên tục (real-time) và ra lệnh tự động cho hệ điều hành.

---

## ⚙️ 3. Cơ chế hoạt động

Luồng hoạt động chính (Workflow) diễn ra theo vòng lặp tự động khép kín:

1. `attacker-bot` liên tục gửi các request (`curl`) đến `nginx-server`.
2. `nginx-server` ghi nhận lại toàn bộ lịch sử truy cập vào file `access.log`.
3. `security-watcher` liên tục theo dõi (tail) file log này. Nếu phát hiện một IP gửi quá số lượng request cho phép trong một khoảng thời gian ngắn:
   * Ghi IP đó vào file `block_ips.conf`.
   * Ra lệnh reload lại cấu hình Nginx.
4. Kể từ lúc đó, Nginx sẽ trả về lỗi `403 Forbidden` cho mọi request từ IP của kẻ tấn công.

---

## 📁 4. Cấu trúc dự án

```text
brute-force-defender/
├── docker-compose.yml       # File điều phối trung tâm
├── README.md                # Tài liệu báo cáo dự án
├── VIBE_CODING.md           # Nhật ký phát triển cùng AI
├── nginx-server/            # Web Server Target
│   ├── Dockerfile           
│   └── nginx.conf           
├── security-watcher/        # Hệ thống phòng ngự (Python)
│   ├── watcher.py           
│   └── requirements.txt     
├── shared_config/           # Cấu hình dùng chung
│   └── block_ips.conf       # Danh sách đen (Blacklist IP)
├── shared_logs/             # Log dùng chung
│   ├── access.log           
│   └── error.log            
└── attacker-bot/            # Module giả lập tấn công
    ├── Dockerfile
    └── attack.sh

## **🚀 5. Hướng dẫn Triển khai (Phần 1)**
Yêu cầu hệ thống

Hệ điều hành: Ubuntu / Linux

Công cụ: Docker, Docker Compose

Bước 1: Tải mã nguồn về máy (Clone)

Bash
git clone [https://github.com/Dungsocool/brute-force-defender.git](https://github.com/Dungsocool/brute-force-defender.git)
cd brute-force-defender
Bước 2: Khởi chạy hệ thống phòng ngự (Run/Deploy)

Bash
sudo docker-compose up --build
(Hệ thống sẽ tự động chạy Nginx, kích hoạt Python Watcher và khởi động Bot tấn công)

Bước 3: Dọn dẹp môi trường sau khi test (Reset)
Để đưa hệ thống về trạng thái ban đầu (xóa danh sách IP đã chặn) nhằm test lại từ đầu:

Bash
sudo docker-compose down
sudo sh -c 'echo -n > shared_config/block_ips.conf'
## **📸 6. Hình ảnh Demo**
Dưới đây là minh chứng hệ thống hoạt động thực tế:

1. Log cho thấy Bot bắt đầu tấn công và bị phát hiện:
(Chèn ảnh 1 vào đây)

2. Kẻ tấn công nhận mã lỗi 403 Forbidden (DA BI BLOCK):
(Chèn ảnh 2 vào đây)


---

### Hướng dẫn cách đưa ảnh vào phần "Hình ảnh Demo" (Mục số 6):

Để ảnh hiện lên README, bạn làm theo 3 bước siêu đơn giản sau ngay trên trình duyệt web GitHub của bạn:

**Bước 1: Tải ảnh lên GitHub**
1. Ở trang chủ Repo của bạn, bấm nút **"Add file"** (ngay cạnh nút màu xanh Code) -> Chọn **"Upload files"**.
2. Kéo thả các ảnh bạn đã chụp màn hình terminal (ảnh lúc IP bị báo 403) vào đó.
3. Bấm **"Commit changes"** màu xanh lá ở dưới cùng.

**Bước 2: Lấy Link ảnh**
1. Click vào file ảnh bạn vừa up lên (ví dụ: `loi_403.png`).
2. Nhấp chuột phải vào chính tấm ảnh đó -> Chọn **"Copy image address"** (Sao chép địa chỉ hình ảnh).

**Bước 3: Dán vào README**
Mở lại file `README.md` lên để Edit, tìm đến Mục số 6 ở dưới cùng và thay thế dòng chữ `*(Chèn ảnh...)*` bằng cú pháp sau:
`![Mô tả ảnh](Dán_cái_link_vừa_copy_vào_đây)`

**Ví dụ thực tế:**
`![Hệ thống báo lỗi 403](https://github.com/Dungsocool/brute-force-defender/raw/main/loi_403.png)`

Bạn dán thử nội dung mới này lên GitHub xem cấu trúc nhìn đã "đã mắt" chưa nhé? Nếu cần
