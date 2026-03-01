# Nhật ký Vibe Coding - Dự án Brute-Force Defender

Dưới đây là lịch sử và các prompt thực tế tôi đã sử dụng để điều khiển AI hoàn thiện dự án này:

## 1. Prompt thiết kế cấu trúc ban đầu

<img width="1377" height="692" alt="image" src="https://github.com/user-attachments/assets/44ee34c3-2c09-417f-a6e0-90dce06c0442" />
<img width="1602" height="879" alt="image" src="https://github.com/user-attachments/assets/385394d3-9b28-4803-b866-ce76b33843ca" />


## 2. Các Prompt dùng để Debug (Xử lý lỗi)
Trong quá trình triển khai, tôi đã gặp một số lỗi thực tế và đây là cách tôi dùng AI để giải quyết:

* **Debug lỗi Docker trên máy mới:** * Prompt tôi dùng: "Lỗi unknown command: docker compose khi chạy lệnh sudo docker compose down..."
  * Bằng chứng: (Chụp lại đoạn chat AI chỉ bạn thêm dấu gạch nối `docker-compose`).

* **Debug lỗi xung đột Git (fetch first):**
  * Prompt tôi dùng: "Lỗi ! [rejected] main -> main (fetch first) khi git push..."
  * Bằng chứng: (Chụp lại đoạn chat AI chỉ bạn dùng lệnh `git pull origin main --rebase`).

* **Debug lỗi hệ thống lưu cache IP bị chặn:**
  * Prompt tôi dùng: "Làm sao để xóa dữ liệu cũ trong block_ips.conf để test lại từ đầu?"
  * Bằng chứng: (Chụp lại đoạn chat AI chỉ bạn dùng lệnh `echo -n > shared_config/block_ips.conf`).

* **Điều khiển AI viết tài liệu chuẩn hóa:**
  * Prompt tôi dùng: "Hãy đóng vai một chuyên gia An toàn thông tin và viết lại file README.md cho dự án..."
  * Bằng chứng: (Chụp lại đoạn chat bạn vừa bắt mình tối ưu file README với Telegram).
