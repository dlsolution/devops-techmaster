### Đề bài
LEVEL 2: triển khai deployment một ứng dụng web tĩnh lên kubernetes cho phép truy cập từ bên ngoài thông qua nodePort

### Output:
- Đóng gói thành công container chứa web tĩnh
  - download 1 template tại https://www.free-css.com/free-css-templates)
  - sử dụng base image nginx
  - lưu ý cấu hình nginx trỏ tới web tĩnh (tham khảo file cấu hình mẫu đơn giản tại https://gist.github.com/mockra/9062657)
- 1 deployment chạy ứng dụng web tĩnh (replicas=2)
- 1 nodePort service trỏ tới deployment (service web 1)
- Thực hiên curl tới nodePort và cho ra kết quả trang web tĩnh theo template

---

### Các bước thực hiện

#### Bước 0: tạo cluster với KinD

```bash
$ kind create cluster --name lab --config kind.conf      
```

#### Bước 1: Clone web template

[https://www.free-css.com/free-css-templates/page290/digimedia](https://www.free-css.com/free-css-templates/page290/digimedia)

Tạo thư mục `my-website` và copy template vào đấy

#### Bước 2: Add file default.conf cho nginx

```bash
server {
  listen 80;

  location / {
    root /app;
    index homepage_1.html;
  }
}
```

#### Bước 3: tạo Dockerfile

```bash
FROM nginx

WORKDIR /app

COPY my-website .

COPY default.conf /etc/nginx/conf.d/default.conf      
```

#### Bước 4: Build image và push image lên docker hub

```bash
$ docker build -t duylinh158/my-website:1.0.0 .
$ docker push duylinh158/my-website:1.0.0
```

#### Bước 5: Tạo deployment với image duylinh158/my-website:1.0.0 và replicas=2

```bash
$ kubectl create deployment webapp --image duylinh158/my-website:latest --replicas=2 --dry-run=client -o yaml > templates/webapp-deployment.yaml  

$ kubectl create -f templates/webapp-deployment.yaml  
```

#### Bước 6: Tạo nodeport service

```bash
$ kubectl create service nodeport webapp --tcp=80:80 --node-port=32002 --dry-run=client -o yaml > templates/webapp-service.yaml  
```

#### Truy cập tới ứng dụng thông qua service 

```bash
$ curl 127.0.0.1:32002
<!DOCTYPE html>
<html lang="en">

  <head>

    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@100;200;300;400;500;600;700;800;900&display=swap" rel="stylesheet">

    <title>DigiMedia - Creative SEO HTML5 Template</title>

    <!-- Bootstrap core CSS -->
    <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
```

### Evident
<img src="/kubernetes/lab-2/images/lab-2.1.png" width="50%">

<img src="/kubernetes/lab-2/images/lab-2.png" width="50%">
