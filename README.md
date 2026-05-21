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
- Tiến hành tạo Cloudflare Tunnel: ```cloudflared tunnel create bt4_wordpress```
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

### 
