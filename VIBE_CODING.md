# Nhật ký Vibe Coding: Dự án Brute-Force Defender

## Phần 1: Prompt thiết kế cấu trúc ban đầu
**Tình huống:** Thay vì chỉ yêu cầu code đơn giản, tôi đóng vai trò Kiến trúc sư hệ thống để yêu cầu AI thiết kế một hạ tầng Microservices hoàn chỉnh, đảm bảo tính cô lập và khả năng quan sát .

<img width="1377" height="692" alt="image" src="https://github.com/user-attachments/assets/44ee34c3-2c09-417f-a6e0-90dce06c0442" />
<img width="1602" height="879" alt="image" src="https://github.com/user-attachments/assets/385394d3-9b28-4803-b866-ce76b33843ca" />
<img width="1279" height="618" alt="image" src="https://github.com/user-attachments/assets/16e9b9d4-f888-41ad-910b-9db1e7619a78" />
<img width="1137" height="697" alt="image" src="https://github.com/user-attachments/assets/59fb73bd-d1af-4696-8f5b-44e20064ed59" />
<img width="1283" height="660" alt="image" src="https://github.com/user-attachments/assets/df106ac1-d722-483c-949a-7c6882e52835" />
<img width="1008" height="541" alt="image" src="https://github.com/user-attachments/assets/611da009-add6-486d-a288-bddc2a1b56ad" />
<img width="1268" height="538" alt="image" src="https://github.com/user-attachments/assets/0202ace6-e961-40a0-a14c-7db583bba88c" />





**Đánh giá tính khả quan đối với môi trường thực tế :** Mặc dù trong bài vẫn còn nhiều nhược điểm , nếu tích hợp thêm nhiều thứ hệ thống sẽ quá tải . Do mục đích là muốn quan tâm đến tư duy logic và luồng hệ thống cốt lõi (Đọc log -> Phân tích -> Chặn). Nên tôi sẽ tránh phức tạp hóa vấn đề .



<img width="1004" height="618" alt="image" src="https://github.com/user-attachments/assets/1d714ec2-9a03-4ed1-bc9e-abd8d259597c" />
<img width="1110" height="599" alt="image" src="https://github.com/user-attachments/assets/333e56c4-99cb-4eec-a1ca-d38e6cf24832" />
<img width="1274" height="598" alt="image" src="https://github.com/user-attachments/assets/02f08183-1333-4bf0-bf2e-45364bf0b9ae" />
<img width="1163" height="648" alt="image" src="https://github.com/user-attachments/assets/71aeb45b-d763-4c7d-9921-3f8c9df9078b" />


---

## Phần 2: Quá trình Debug khi AI viết code sai 1.
**🐛 Lỗi 1: Quyền truy cập Docker Socket (Permission Denied)** 
Tình huống: Khi khởi chạy lần đầu, hệ thống báo lỗi không có quyền truy cập vào /var/run/docker.sock.

<img width="1265" height="604" alt="image" src="https://github.com/user-attachments/assets/e2c50127-5b79-4916-baa5-0500e7feea72" />

Prompt tôi đã dùng:

"Dựa vào log này, tôi hiểu là hệ thống đang từ chối quyền truy cập vào file Docker Socket. Có phải do tôi chạy lệnh thiếu quyền sudo, hay do user Linux của tôi chưa được thêm vào group docker? Bạn hãy đóng vai DevOps Engineer, giải thích ngắn gọn nguyên nhân gốc rễ của lỗi này và đưa ra câu lệnh khắc phục chuẩn xác nhất cho tôi.?"

Cách xử lý: AI hướng dẫn thêm user vào group docker. Hoặc chạy với quyền sudo.
<img width="1302" height="654" alt="image" src="https://github.com/user-attachments/assets/fa21eede-8c40-4778-b193-0e16ce139a24" />


**🐛 Lỗi 2: Lỗi mạng TLS khi Build Image (Image_7fd549.png)**
Tình huống: Khi build container attacker-bot, lệnh apk add bị lỗi TLS: unspecified error.

<img width="1023" height="639" alt="image" src="https://github.com/user-attachments/assets/56c1c7f5-8305-484c-9dbc-30842725fd06" />

Prompt tôi đã dùng:

"Mình dùng quyền sudo và đã chạy được , nhưng ra lỗi này , mình nghĩ là do chưa có mạng để tải gói apk hãy hướng dẫn tôi cách khắc phục lỗi mạng/DNS của Docker trên máy ảo Linux này.  có nên thêm cấu hình DNS vào file /etc/docker/daemon.json hay khởi động lại service mạng không"

Cách xử lý: Tôi ép AI bổ sung network: host vào khối build. Đây là bài học lớn về việc xử lý mạng trong môi trường Lab phức tạp.
<img width="1060" height="624" alt="image" src="https://github.com/user-attachments/assets/4c8bf090-f842-41a3-9409-ada191a800f9" />
<img width="1020" height="632" alt="image" src="https://github.com/user-attachments/assets/513d104c-2182-467e-9066-0eced26ae2b2" />


**🐛 Lỗi 3: Xung đột thư viện ngầm (http+docker)**
Tình huống: Container sập ngay khi khởi động vì lỗi Not supported URL scheme http+docker.

<img width="1307" height="577" alt="image" src="https://github.com/user-attachments/assets/dc364fe7-d9d7-4a4e-b748-92e039f0b55f" />


Hãy cung cấp cho tôi nội dung mới của requirements.txt, watcher.py và attack.sh."
Prompt tôi đã dùng:

"Đã được nhưng có thêm vấn đề mới : Container security-watcher bị sập ngay lúc khởi động với lỗi:Error while fetching server API version: Not supported URL scheme http+dockerTôi chẩn đoán đây là lỗi xung đột phiên bản giữa thư viện docker và thư viện requests (hoặc urllib3) mà chúng ta vừa thêm vào. Bạn hãy thiết lập lại các phiên bản trong file requirements.txt sao cho tương thích nhé.
Terminal của tôi đang bị lỗi font hiển thị Tiếng Việt có dấu (sinh ra các ký tự đặc biệt). Bạn hãy viết lại toàn bộ các dòng in ra màn hình (logger.info, echo...) trong file watcher.py và attack.sh sang TIẾNG VIỆT KHÔNG DẤU giúp tôi (Ví dụ: 'Phat hien tan cong').
Hãy cung cấp cho tôi nội dung mới của requirements.txt, watcher.py và attack.sh."

Cách xử lý: Tôi bắt AI ghim cứng phiên bản urllib3<2.0.0. Điều này giúp tôi hiểu sâu về tầm quan trọng của việc quản lý Dependency.
<img width="1045" height="615" alt="image" src="https://github.com/user-attachments/assets/34a3c299-e552-428d-822a-a8c4bb9425f9" />

<img width="973" height="655" alt="image" src="https://github.com/user-attachments/assets/2e290fa7-60a5-4b49-be41-e6370a36f4a6" />

**🐛 Lỗi 4: Cảnh báo Telegram bị im lặng (Lỗi mạng Runtime)**
Tình huống: Chặn IP thành công nhưng không có tin nhắn Telegram nào gửi về điện thoại.
<img width="1158" height="604" alt="image" src="https://github.com/user-attachments/assets/e75268f4-5b6a-472b-ae5d-87dbdc0a3828" />


Prompt tôi đã dùng:

"Chặn được IP rồi nhưng Telegram vẫn im lặng. Hãy thêm debug log in ra Status Code và Response Body của Telegram API, đồng thời cấu hình network_mode: host để container thoát khỏi mạng ảo Docker."
Cách xử lý: Sau khi đổi sang network_mode: host, hệ thống đã thông mạng hoàn toàn. Bài học: Mạng ảo Bridge của Docker đôi khi là rào cản cho các ứng dụng cần gọi API bên ngoài.


