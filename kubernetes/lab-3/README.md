### Đề bài
LEVEL 3 (ADVANCE): triển khai nginx proxy cho nhiều ứng dụng

- Từ level 2, triển khai thêm 1 trang web static thứ hai, khác với static web đã triển khai
- Service cho trang web tĩnh mới được lấy tên là web2
- Triển khai thêm 1 deployment nginx-proxy đóng vai trò proxy cho cả 2 ứng dụng trên và tạo nodePort service có tên “nginx-proxy”
- Thiết lập cấu hình config của nginx-proxy sao cho:
  - Khi gọi tới nginx-proxy với path /web1 > nginx-proxy filter path và forward tới service web 1 > service web1
  - Khi gọi tới nginx-proxy với path /web2 > nginx-proxy filter path và forward tới service web 2 > service web2

### Output:
curl http://<node-ip>:<node-port>/web1 > trả về static web 1
curl http://<node-ip>:<node-port>/web2 > trả về static web 2

---

### Các bước thực hiện

#### Bước 0: tạo cluster với KinD

```bash
$ kind create cluster --name lab --config kind.conf      
```

### Evident