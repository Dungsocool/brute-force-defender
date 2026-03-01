# Nhật ký Vibe Coding: Dự án Brute-Force Defender

## Phần 1: Prompt thiết kế cấu trúc ban đầu
**Tình huống:** Thay vì chỉ yêu cầu code đơn giản, tôi đóng vai trò Kiến trúc sư hệ thống để yêu cầu AI thiết kế một hạ tầng Microservices hoàn chỉnh, đảm bảo tính cô lập và khả năng quan sát .

<img width="1377" height="692" alt="image" src="https://github.com/user-attachments/assets/44ee34c3-2c09-417f-a6e0-90dce06c0442" />
<img width="1602" height="879" alt="image" src="https://github.com/user-attachments/assets/385394d3-9b28-4803-b866-ce76b33843ca" />
<img width="885" height="632" alt="image" src="https://github.com/user-attachments/assets/f8ab30bd-f938-4eea-bf82-5939d903e6a8" />
<img width="1137" height="697" alt="image" src="https://github.com/user-attachments/assets/59fb73bd-d1af-4696-8f5b-44e20064ed59" />
<img width="901" height="473" alt="image" src="https://github.com/user-attachments/assets/3911e0b1-6901-4234-93ad-81589316ae97" />
<img width="1008" height="541" alt="image" src="https://github.com/user-attachments/assets/611da009-add6-486d-a288-bddc2a1b56ad" />
<img width="921" height="470" alt="image" src="https://github.com/user-attachments/assets/5c329f01-f533-4fce-97bc-3f15aaf5409f" />




**Đánh giá tính khả quan đối với môi trường thực tế :** Mặc dù trong bài vẫn còn nhiều nhược điểm , nếu tích hợp thêm nhiều thứ hệ thống sẽ quá tải . Do mục đích là muốn quan tâm đến tư duy logic và luồng hệ thống cốt lõi (Đọc log -> Phân tích -> Chặn). Nên tôi sẽ tránh phức tạp hóa vấn đề .



<img width="1004" height="618" alt="image" src="https://github.com/user-attachments/assets/1d714ec2-9a03-4ed1-bc9e-abd8d259597c" />
<img width="752" height="696" alt="image" src="https://github.com/user-attachments/assets/f2739b6c-cbd8-44c1-8e4b-d903d11f005e" />

---

## Phần 2: Quá trình Debug khi AI viết code sai 1.
**🐛 Lỗi 1: Quyền truy cập Docker Socket (Permission Denied)** 
Tình huống: Khi khởi chạy lần đầu, hệ thống báo lỗi không có quyền truy cập vào /var/run/docker.sock.

<img width="886" height="508" alt="image" src="https://github.com/user-attachments/assets/d90b952c-633d-4053-ae36-fbd796e8e2ec" />

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
<img width="957" height="527" alt="image" src="https://github.com/user-attachments/assets/7c410ff0-8546-4768-8f83-630a220ced08" />


**🐛 Lỗi 3: Xung đột thư viện ngầm (http+docker)**
Tình huống: Container sập ngay khi khởi động vì lỗi Not supported URL scheme http+docker.

<img width="1307" height="577" alt="image" src="https://github.com/user-attachments/assets/dc364fe7-d9d7-4a4e-b748-92e039f0b55f" />


Hãy cung cấp cho tôi nội dung mới của requirements.txt, watcher.py và attack.sh."
Prompt tôi đã dùng:

"Đã được nhưng có thêm vấn đề mới : Container security-watcher bị sập ngay lúc khởi động với lỗi:Error while fetching server API version: Not supported URL scheme http+dockerTôi chẩn đoán đây là lỗi xung đột phiên bản giữa thư viện docker và thư viện requests (hoặc urllib3) mà chúng ta vừa thêm vào. Bạn hãy thiết lập lại các phiên bản trong file requirements.txt sao cho tương thích nhé.
Terminal của tôi đang bị lỗi font hiển thị Tiếng Việt có dấu (sinh ra các ký tự đặc biệt). Bạn hãy viết lại toàn bộ các dòng in ra màn hình (logger.info, echo...) trong file watcher.py và attack.sh sang TIẾNG VIỆT KHÔNG DẤU giúp tôi (Ví dụ: 'Phat hien tan cong').
Hãy cung cấp cho tôi nội dung mới của requirements.txt, watcher.py và attack.sh."

Cách xử lý: Tôi bắt AI ghim cứng phiên bản urllib3<2.0.0. Điều này giúp tôi hiểu sâu về tầm quan trọng của việc quản lý Dependency.
<img width="804" height="679" alt="image" src="https://github.com/user-attachments/assets/e070e488-7d0e-4d23-b9f2-6a29ac46cfe6" />

**🐛 Lỗi 4: Cảnh báo Telegram bị im lặng (Lỗi mạng Runtime)**
Tình huống: Chặn IP thành công nhưng không có tin nhắn Telegram nào gửi về điện thoại.
Prompt tôi đã dùng:

"Chặn được IP rồi nhưng Telegram vẫn im lặng. Hãy thêm debug log in ra Status Code và Response Body của Telegram API, đồng thời cấu hình network_mode: host để container thoát khỏi mạng ảo Docker."
Cách xử lý: Sau khi đổi sang network_mode: host, hệ thống đã thông mạng hoàn toàn. Bài học: Mạng ảo Bridge của Docker đôi khi là rào cản cho các ứng dụng cần gọi API bên ngoài.
** Lỗi 1: Từ chối quyền truy cập Docker Socket (Permission Denied)
Tình huống bạn gặp: Khi vừa gõ lệnh docker-compose up --build lần đầu tiên, hệ thống văng lỗi permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock.

Cách Debug & Xử lý: Nhận diện được nguyên nhân do user Linux hiện tại không có quyền gọi tiến trình nền của Docker. Xử lý tận gốc bằng lệnh sudo usermod -aG docker $USER và newgrp docker thay vì lạm dụng quyền root (sudo).

📸 Hướng dẫn chụp ảnh: * Ảnh 1: Chụp đoạn Terminal hiện dòng chữ lỗi permission denied....

Ảnh 2: Chụp lúc bạn gõ lệnh sudo usermod -aG docker $USER thành công.

2. Lỗi 2: Mạng ảo bị cô lập khi Build (Lỗi TLS Alpine)
Tình huống bạn gặp: Khi build image attacker-bot, lệnh apk add curl bị thất bại kèm thông báo TLS: unspecified error. Dù đổi DNS hệ thống vẫn không được.

Cách Debug & Xử lý: Phát hiện ra 2 nguyên nhân sâu xa: (1) Do Docker được cài bằng bản Snap có cơ chế sandbox mạng quá khắt khe, gây đứt gãy kết nối khi đi qua VPN. (2) Phải bổ sung tham số network: host vào khối build trong docker-compose.yml để ép tiến trình build đi trực tiếp bằng card mạng vật lý của máy ảo.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Lấy ngay cái ảnh chụp màn hình log màu đỏ/vàng WARNING: fetching... TLS: unspecified error mà bạn đã gửi cho mình lúc nãy.

Ảnh 2: Chụp đoạn code trong file docker-compose.yml có highlight/bôi đen dòng chữ network: host nằm dưới chữ context.

3. Lỗi 3: Xung đột thư viện ngầm (http+docker)
Tình huống bạn gặp: Khi thêm thư viện requests để gọi Telegram, container security-watcher chết đứng ngay lúc khởi động với lỗi: Not supported URL scheme http+docker.

Cách Debug & Xử lý: Phân tích ra đây là lỗi xung đột "kinh điển". Thư viện requests kéo theo urllib3 bản 2.x, bản này đã cắt bỏ hỗ trợ giao thức socket tùy chỉnh, làm gãy thư viện docker. Khắc phục bằng cách ghim cứng (pin) phiên bản trong requirements.txt thành urllib3==1.26.18.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Chụp file requirements.txt có bôi đen dòng urllib3==1.26.18. (Có thể ghi chú thêm mũi tên trỏ vào: "Ghim phiên bản để fix lỗi").

4. Lỗi 4: Container không gọi được API Telegram (Lỗi mạng Runtime)
Tình huống bạn gặp: Hệ thống đã chặn IP thành công, log Nginx báo 403, nhưng điện thoại tuyệt nhiên không có thông báo Telegram.

Cách Debug & Xử lý: Phát hiện ra rằng khi chạy thực tế (runtime), Docker ném container vào mạng Bridge ảo. Mạng ảo này không có đường ra Internet (bị Firewall pfSense hoặc VPN chặn). Quyết định cấu trúc lại file compose, thêm network_mode: "host" vào service security-watcher để nó dùng chung IP và DNS với máy chủ vật lý, từ đó gọi API trót lọt.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Chụp dòng code network_mode: "host" trong file docker-compose.yml.

Ảnh 2 (Cực kỳ quan trọng để chốt hạ): Chụp Terminal đang chạy luồng log có chữ: STATUS CODE: 200 và Gui Telegram THANH CONG!.

Ảnh 3: Chụp màn hình điện thoại/app Telegram hiển thị dòng tin nhắn "🚨 [CANH BAO] Da chan IP..."

.**Lỗi 1 Lỗi 1: Từ chối quyền truy cập Docker Socket (Permission Denied)
Tình huống bạn gặp: Khi vừa gõ lệnh docker-compose up --build lần đầu tiên, hệ thống văng lỗi permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock.

Cách Debug & Xử lý: Nhận diện được nguyên nhân do user Linux hiện tại không có quyền gọi tiến trình nền của Docker. Xử lý tận gốc bằng lệnh sudo usermod -aG docker $USER và newgrp docker thay vì lạm dụng quyền root (sudo).

📸 Hướng dẫn chụp ảnh: * Ảnh 1: Chụp đoạn Terminal hiện dòng chữ lỗi permission denied....

Ảnh 2: Chụp lúc bạn gõ lệnh sudo usermod -aG docker $USER thành công.

2. Lỗi 2: Mạng ảo bị cô lập khi Build (Lỗi TLS Alpine)
Tình huống bạn gặp: Khi build image attacker-bot, lệnh apk add curl bị thất bại kèm thông báo TLS: unspecified error. Dù đổi DNS hệ thống vẫn không được.

Cách Debug & Xử lý: Phát hiện ra 2 nguyên nhân sâu xa: (1) Do Docker được cài bằng bản Snap có cơ chế sandbox mạng quá khắt khe, gây đứt gãy kết nối khi đi qua VPN. (2) Phải bổ sung tham số network: host vào khối build trong docker-compose.yml để ép tiến trình build đi trực tiếp bằng card mạng vật lý của máy ảo.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Lấy ngay cái ảnh chụp màn hình log màu đỏ/vàng WARNING: fetching... TLS: unspecified error mà bạn đã gửi cho mình lúc nãy.

Ảnh 2: Chụp đoạn code trong file docker-compose.yml có highlight/bôi đen dòng chữ network: host nằm dưới chữ context.

3. Lỗi 3: Xung đột thư viện ngầm (http+docker)
Tình huống bạn gặp: Khi thêm thư viện requests để gọi Telegram, container security-watcher chết đứng ngay lúc khởi động với lỗi: Not supported URL scheme http+docker.

Cách Debug & Xử lý: Phân tích ra đây là lỗi xung đột "kinh điển". Thư viện requests kéo theo urllib3 bản 2.x, bản này đã cắt bỏ hỗ trợ giao thức socket tùy chỉnh, làm gãy thư viện docker. Khắc phục bằng cách ghim cứng (pin) phiên bản trong requirements.txt thành urllib3==1.26.18.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Chụp file requirements.txt có bôi đen dòng urllib3==1.26.18. (Có thể ghi chú thêm mũi tên trỏ vào: "Ghim phiên bản để fix lỗi").

4. Lỗi 4: Container không gọi được API Telegram (Lỗi mạng Runtime)
Tình huống bạn gặp: Hệ thống đã chặn IP thành công, log Nginx báo 403, nhưng điện thoại tuyệt nhiên không có thông báo Telegram.

Cách Debug & Xử lý: Phát hiện ra rằng khi chạy thực tế (runtime), Docker ném container vào mạng Bridge ảo. Mạng ảo này không có đường ra Internet (bị Firewall pfSense hoặc VPN chặn). Quyết định cấu trúc lại file compose, thêm network_mode: "host" vào service security-watcher để nó dùng chung IP và DNS với máy chủ vật lý, từ đó gọi API trót lọt.

📸 Hướng dẫn chụp ảnh:

Ảnh 1: Chụp dòng code network_mode: "host" trong file docker-compose.yml.

Ảnh 2 (Cực kỳ quan trọng để chốt hạ): Chụp Terminal đang chạy luồng log có chữ: STATUS CODE: 200 và Gui Telegram THANH CONG!.

Ảnh 3: Chụp màn hình điện thoại/app Telegram hiển thị dòng tin nhắn "🚨 [CANH BAO] Da chan IP..."

<img width="1150" height="648" alt="image" src="https://github.com/user-attachments/assets/76af2c54-bc7c-4768-a174-863b39f7d57a" />

<img width="1119" height="611" alt="image" src="https://github.com/user-attachments/assets/710f6ffc-b117-4cc0-a14a-2974ea82469b" />


**Lỗi 2: AI viết code ghi IP vào file block nhưng quên cấu hình Nginx.**

> Script Python đã ghi IP 172.18.0.1 vào file block_ips.conf, nhưng Nginx vẫn không chặn (truy cập vẫn trả về mã 200 OK). Hình như bạn quên hướng dẫn tôi thêm lệnh include vào file nginx.conf để Nginx nạp danh sách block này đúng không? Hãy viết lại file nginx.conf hoàn chỉnh cho tôi.

![Ảnh minh chứng](dán_ảnh_vào_đây_bằng_Ctrl_V)

---

## Phần 3: Debug lỗi hệ thống khác

**Lỗi 3: Khác biệt phiên bản Docker Compose.**

> Khi tôi mang code sang máy Ubuntu mới và chạy, terminal báo lỗi unknown command: docker compose. Sửa nhanh như thế nào mà không cần cài lại Docker?

![Ảnh minh chứng](dán_ảnh_vào_đây_bằng_Ctrl_V)


