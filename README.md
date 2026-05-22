# Môn: Phát triển ứng dụng với mã nguồn mở-TEE0421
# Họ tên: Đậu Văn Khánh
# MSSV: K225480106099
# Lớp: K58KTP
# KHAI THÁC N8N ĐỂ TỰ ĐỘNG ĐĂNG BÀI LÊN WORDPRESS
# SỬ DỤNG KẾT QUẢ ĐÃ LÀM Ở BÀI TẬP 3, BỔ SUNG VÀO DOCKER COMPOSE ĐỂ CÓ THÊM SERVICE 8N8
## 1. Sử dụng docker trên ubuntu để tạo 1 file docker-compose.yml chứa:
### MariaDB:
<img width="592" height="437" alt="image" src="https://github.com/user-attachments/assets/cec749aa-094e-4c12-9d7d-e2769922df78" />

### Phpmyadmin:
<img width="496" height="357" alt="image" src="https://github.com/user-attachments/assets/06c3c213-f633-4349-819b-b895af35a1da" />

### WordPress:
<img width="577" height="448" alt="image" src="https://github.com/user-attachments/assets/fd5d044e-b1f7-4d1a-845e-1d54bb7188e5" />

### Public service bằng Cloudflare Tunnel
- Tiến hành tạo Cloudflare Tunnel: ```cloudflared tunnel create bt4_wordpress```(Cấu hình Cloudflare Tunnel nhằm công khai các dịch vụ WordPress, phpMyAdmin và n8n thông qua các subdomain riêng biệt. Toàn bộ quá trình được thực hiện bằng giao diện dòng lệnh (CLI) của Cloudflare Tunnel, giúp thao tác nhanh chóng, dễ quản lý và thuận tiện trong việc triển khai trên môi trường máy chủ Ubuntu.)
<img width="1462" height="175" alt="image" src="https://github.com/user-attachments/assets/7b73be44-c5fd-4d46-b6ab-dfd36687dee2" />

- Sau khi Tunnel được khởi tạo, tiến hành cấu hình DNS Routing để ánh xạ các subdomain về Tunnel vừa tạo:
<img width="1467" height="240" alt="image" src="https://github.com/user-attachments/assets/3f66cc34-2054-469d-97c9-9615e7e3f490" />

- Trong đó:
  + wp.khanh123.id.vn được sử dụng để truy cập website WordPress.
  + pma.khanh123.id.vn dùng để truy cập phpMyAdmin quản trị cơ sở dữ liệu.
  + n8n.khanh123.id.vn dùng để truy cập nền tảng tự động hóa n8n.
 
- Tạo và cấu hình file config.yml cho Cloudflare Tunnel
  + Sau khi tạo thành công Cloudflare Tunnel, tiến hành xây dựng file cấu hình config.yml nhằm định tuyến các yêu cầu truy cập từ Internet đến đúng các service Docker bên trong hệ thống.
  + Chạy lệnh: ```nano /home/khanh/.cloudflared/config.yml```
  + Tiếp theo, khai báo nội dung cấu hình cho Tunnel:
```
tunnel: 96bae3bc-d9cc-427f-aa52-c7a3f60e4af9
credentials-file: /etc/cloudflared/96bae3bc-d9cc-427f-aa52-c7a3f60e4af9.json

ingress:
  - hostname: wp.khanh123.id.vn
    service: http://wordpress:80
  - hostname: pma.khanh123.id.vn
    service: http://phpmyadmin:80
  - hostname: n8n.khanh123.id.vn
    service: http://n8n:5678
  - service: http_status:404
```
<img width="1473" height="750" alt="image" src="https://github.com/user-attachments/assets/b4f82b20-b646-4d96-9d6b-b158d7c2faad" />

- Ý nghĩa của từng thành phần trong file cấu hình:
  + tunnel: khai báo Tunnel ID đã tạo trước đó.
  + credentials-file: đường dẫn tới file xác thực Tunnel.
  + ingress: danh sách các quy tắc định tuyến truy cập.

- Cập nhật file docker-compose.yml
  + Sau khi hoàn tất cấu hình Cloudflare Tunnel, tiến hành cập nhật file docker-compose.yml nhằm triển khai đồng thời toàn bộ các service của hệ thống bằng Docker Compose.

### Cấu hình Cloudflare trong file docker-compose.yml
<img width="1313" height="348" alt="image" src="https://github.com/user-attachments/assets/1fcd1125-08c4-4ba9-9e99-904527bf3e6d" />

### Cấu hình n8n trong file docker-compose.yml
<img width="666" height="352" alt="image" src="https://github.com/user-attachments/assets/cc82d1f8-21da-4942-97b8-496448c9e101" />

### Triển khai hệ thống
- Sử dụng lệnh ```docker compose up -d``` để triển khai và khởi động toàn bộ các service trong hệ thống dưới dạng container.
<img width="1486" height="270" alt="image" src="https://github.com/user-attachments/assets/ab3c6939-85db-4ce5-979c-3aa3f9ccf1dc" />

- Kiểm tra trạng thái hoạt động của các container trong hệ thống bằng lệnh: ```docker compose ps```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/903e8ad8-1345-4fab-80b2-904be836fc40" />

## 2. Cấu hình Cloudflare Tunnel và định tuyến Subdomain
### Bước 1: Cấu hình Cloudflare Tunnel 
- Kết quả sau khi cấu hình Cloudflare Tunnel (em đã cấu hình ở ý 1)
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/854147d1-7c97-4a00-a5c6-75474039a813" />

### Bước 2: Cài đặt Wordpress và kiểm tra database
#### Truy cập sub-domain2 để quan sát xem cơ sở dữ liệu chưa có bảng nào?
- Truy cập: https://pma.khanh123.id.vn/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/1a4309e8-4733-45cc-90d6-72f7aa0b8408" />

#### Truy cập sub-domain1 để cài đặt wordpress (làm theo hướng dẫn của wordpress)
- Truy cập: https://wp.khanh123.id.vn/
- Chọn ngôn ngữ
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2ab28a3b-767d-46b4-9a75-82067f9536d3" />

- Nhập thông tin
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b5894deb-b843-4d57-b64d-9674a7be87cf" />

- Thông báo thành công sau khi cài đặt Wordpress
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/246085fb-d64e-4185-b7b7-f9241b15e49a" />

- Đăng nhập tài khoản
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ce178113-6241-4b1f-bdf2-ddf0dc7b16f0" />

#### Truy cập sub-domain2 để quan sát xem cơ sở dữ liệu có những bảng dữ liệu nào sau khi cài wp
<img width="1848" height="1433" alt="6" src="https://github.com/user-attachments/assets/cb574e38-1a69-4d08-a254-d42b3af4acfc" />

#### Tạo 1 bài viết trong wordpress giới thiệu về bản thân sinh viên: thông tin cá nhân, sở thích, ... bài viết có thể chứa hình ảnh, âm thanh, video, ...
- Trong giao diện trang chủ Wordpress: chọn bài viết -> thêm bài viết
<img width="1848" height="1433" alt="2" src="https://github.com/user-attachments/assets/98ef3ac0-cd3d-4412-9805-63556a7c85a0" />

- Thêm bài viết giới thiệu bản thân
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ec819533-a05c-42fc-b2af-1994d6f46049" />

- Truy cập: https://wp.khanh123.id.vn/2026/05/21/gioi-thieu-ban-than/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5448374b-d711-4b9f-82c9-70a0769a4c4f" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/6cac5524-59a1-499a-b22b-4fc8bfb0118a" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f99c052d-250f-4cb9-8a6e-59c51a0f07e8" />

#### Tạo 1 bài viết trong wordpress giới thiệu về nhữn kiến thức mà em đã học được ở môn Phát triển ứng dụng với mã nguồn mở
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4108f1c4-bda6-452d-a00a-d2229e4f07b0" />

- Truy cập: https://wp.khanh123.id.vn/2026/05/21/ptudvmnm/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/60426fa4-7b2a-460b-9335-07b38b4cd5f1" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b1aa2358-278f-466f-905e-35d900848e2f" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/013ace45-be36-4a10-a412-cbc85ce49a77" />

### Bước 3: Kích hoạt License và chuẩn bị API cho n8n
#### 1. Kích hoạt n8n Community
- Truy cập https://n8n.khanh123.id.vn/ để đăng ký tài khoản Admin n8n
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/e24bb673-6a28-44ff-b613-b8a4d62c8ba6" />

- Khi hệ thống hỏi License, điền thông tin để n8n gửi Key về Email.
<img width="1920" height="1205" alt="5" src="https://github.com/user-attachments/assets/3602e4f7-dace-43af-8c7b-5fba2121366f" />

- Vào Email -> Copy Key
<img width="1920" height="1205" alt="5" src="https://github.com/user-attachments/assets/3b5012bc-37fb-4eaa-ac77-b4036afe4716" />

- Vào Settings (Bánh răng góc dưới trái) > Usage and plan > Enter activation key -> Paste key vào để kích hoạt bản Registered Community Edition.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d8481668-d3c2-4efa-b4a7-2610bd83906b" />

#### 2. Lấy Telegram Bot Token
- Mở Telegram, chát với @BotFather.
- Gửi lệnh /newbot, đặt tên cho Bot và Username cho Bot.
- @BotFather sẽ trả về một chuỗi API Token (Ví dụ: 7123456789:ABCdefGhI...). Hãy copy lại.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/f26fe59c-9826-4886-b360-a91e11d0b8b6" />

#### 3. Lấy Google Gemini API Key
- Truy cập Google AI Studio.
- Bấm Create API Key -> Tạo một Project mới và copy chuỗi API Key được cấp.
<img width="1920" height="1205" alt="5" src="https://github.com/user-attachments/assets/a1680d43-2058-4e33-9893-63ba02c2f870" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/3344a185-708b-452b-820d-67c4c2d2e046" />

<img width="1920" height="1205" alt="5" src="https://github.com/user-attachments/assets/44dfdf05-70d0-4b66-a70f-e3d6ed2fd531" />

#### 4. Lấy Application Password từ WordPress
- Vào trang quản trị Wordpress (sub-domain1/wp-admin) -> Tài khoản -> Hồ sơ.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/53de76ab-a1d7-4c86-b1a0-208281e6d458" />

- Kéo xuống dưới cùng tìm mục Mật khẩu ứng dụng (Application Passwords).
- Nhập tên ứng dụng là n8n -> Bấm Thêm mật khẩu ứng dụng mới.
- Copy chuỗi 24 ký tự hiển thị trên màn hình (Lưu ý: Nó chỉ hiển thị 1 lần).
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a3c24be2-122c-4611-bbf0-256a4a669770" />

### Bước 4: Xây dựng Workflow trên n8n
- Trong n8n, tạo một Workflow mới và thêm 4 node nối tiếp
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c7e624b3-ecaa-4646-8d9f-7c975fbc3af3" />

#### Node 1: Telegram Trigger (On Message)
- Bấm "+" -> Trong thanh tìm kiếm gõ Telegram -> chọn Trigger On: Message
<img width="1920" height="1205" alt="5" src="https://github.com/user-attachments/assets/3f7da924-3ae0-4496-951b-0beab130aa8a" />

- Credential for Telegram API: Chọn Create New Credential, dán Access Token của Bot Telegram vào đây -> nhấn Save.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/82a079fa-ee51-4022-9a81-453197104052" />

- Bấm nút Listen for test event, sau đó mở Telegram chát với Bot chữ "hello" để node này bắt được dữ liệu mẫu.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/720cca0f-d648-40c0-91c9-d581a1a60e67" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4f97286a-96cf-4a6f-a7d9-dc8fb7bca9fd" />

#### Node 2: Google Gemini (Message a model)
- Trong thanh tìm kiếm gõ Gemini -> Chọn Message a model
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4e171aa0-76e8-4e17-bbe7-6af3e9d76942" />

- Credential for Google Gemini (API Key): Chọn Create New Credential, dán API Key lấy từ Google AI Studio vào.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a2e8259c-2a1c-4358-a8e6-b2ef9e8adc9a" />

- Prompt: Kéo trường Text từ node Telegram sang và bổ sung yêu cầu định dạng JSON. Cú pháp chuẩn sẽ như sau:
```
{{ $json.message.text }}. 

Hãy viết bài viết hoàn chỉnh dựa trên chủ đề trên. 
BẮT BUỘC trả về kết quả dưới định dạng JSON thuần túy (không bọc trong tag ```json), cấu trúc như sau:
{
  "post_title": "Tiêu đề bài viết hay và thu hút",
  "post_content": "Toàn bộ nội dung bài viết dài khoảng 600 chữ, được định dạng đẹp bằng các thẻ HTML và CSS (ví dụ h2, p, strong, em, h3) để tôi đăng trực tiếp lên WordPress."
}
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/08c8d8c6-b9da-4866-b57b-8a50a6fc9a83" />

#### Node 3: Code in JavaScript
- Trong thanh tìm kiếm gõ Code -> chọn Code in JavaScript
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/1d6f0f65-e363-4a78-89d6-b44741d4b1ca" />

- Nối tiếp sau node Gemini. Đoạn code này dùng để xử lý chuỗi văn bản thuần (Text) mà Gemini trả về thành một Object JSON mà n8n có thể đọc được cấu trúc:
```
// 1. lấy dữ liệu gốc từ node Gemini
const rawText = $input.first().json.content.parts[0].text;

// Xóa bỏ các ký tự bọc markdown nếu AI lỡ thêm vào
const cleanText = rawText.replace(/```json/g, '').replace(/```/g, '').trim();

// 2. Chuyển đổi chuỗi thành Object trong JavaScript
const cleanData = JSON.parse(cleanText);

// 3. Trả về kết quả định dạng lại gọn gàng cho node WordPress sử dụng
return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/561163dc-7e7e-4adb-8459-d4d05b201e67" />

#### Node 4: WordPress (Create a Post)
- Nối tiếp sau node Code, Trong thanh tìm kiếm gõ WordPress -> Chọn Actions: Create a Post.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/937427f7-abc1-49be-ab91-c0d18424efbc" />

- Credential for WordPress REST API: * Resource: Post | Operation: Create
  + URL: https://wp.khanh123.id.vn/
  + Authentication: Chọn Basic Auth.
  + User: Tên đăng nhập Admin WordPress.
  + Password: Dán mật khẩu ứng dụng 24 ký tự (không dùng mật khẩu đăng nhập chính).
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ee418941-037c-474a-9271-d9562e5bb204" />

- Kiểm tra nâng cao: Bật Ignore SSL Issues (Insecure) sang ON.
- Cấu hình Fields:
  + Bấm nút Execute previous nodes để lấy data mẫu từ node Code JS.
  + Title: Kéo thả biến title từ phần kết quả node trước vào.
  + Content: Kéo thả biến content từ phần kết quả node trước vào.
  + Bấm Add Field -> Chọn Status -> Chuyển thành Publish.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/dd99dd35-1686-4fef-9312-4cab50ca3778" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/83668432-c6bd-4c92-aaa9-0b164fef5142" />

### Bước 5: Bật Workflow và thử nghiệm thành quả
- Nhấp vào nút Màu cam (Publish) ở góc trên bên phải màn hình n8n để kích hoạt chế độ tự động chạy ngầm.
- Mở điện thoại lên, vào Telegram chat với Bot yêu cầu: "Viết bài 600 chữ về chủ đề: Giới thiệu ngành Kỹ thuật máy tính trường đại học Kỹ thuật Công nghiệp Thái Nguyên."
- Đợi khoảng 10-15 giây để n8n kích hoạt -> Gemini xử lý -> Đăng lên WordPress.
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/bc396d32-8c37-4e34-8fc9-86110aea72b5" />

- Truy cập trang chủ WordPress: https://wp.khanh123.id.vn/
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a0f48929-272a-420f-a293-f52f9da0baca" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/e5af6d89-f2f4-4864-9eb7-7cebdf6f1050" />

### Nhận xét kết quả đạt được
- Tính thực tiễn cao: Hệ thống giúp tự động hóa quy trình sáng tạo nội dung (Content Creation) từ bước lên ý tưởng (Telegram) qua xử lý thông minh (AI Gemini) đến xuất bản (WordPress) mà không cần can thiệp thủ công.
- Tối ưu hạ tầng với Docker: Việc đóng gói toàn bộ dịch vụ (Cơ sở dữ liệu, Web, Tool tự động hóa, Mạng mã hóa) vào một file docker-compose.yml giúp hệ thống cực kỳ dễ triển khai, bảo trì và mở rộng sau này.
- Bảo mật và tiện lợi nhờ Cloudflare Tunnel: Không cần mở port nguy hiểm trên Router/VPS (No port forwarding), bảo mật được IP gốc của máy chủ trước các cuộc tấn công mạng, đồng thời hỗ trợ cấp SSL tự động (HTTPS).
