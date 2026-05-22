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

### Bước 2: Truy cập các sub-domain
#### Truy cập sub-domain2 để quan sát xem cơ sở dữ liệu chưa có bảng nào?
- Truy cập: pma.khanh123.id.vn
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/1a4309e8-4733-45cc-90d6-72f7aa0b8408" />

#### Truy cập sub-domain1 để cài đặt wordpress (làm theo hướng dẫn của wordpress)
- Truy cập: wp.khanh123.id.vn
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

### Truy cập sub-domain3 để cấu hình n8n:
