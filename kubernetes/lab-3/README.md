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

#### Bước 1: tạo namespace

```bash
$ kubectl create namespace lab --dry-run=client -o yaml > templates/namespace.yaml
```

#### Bước 2: Tạo deployment và service cho 2 web app

```bash
$ kubectl create deployment webapp --image duylinh158/my-website:1.0.0 --namespace lab --replicas=2 --dry-run=client -o yaml > templates/webapp-deployment.yaml

$ kubectl create deployment webapp2 --image duylinh158/my-website2:1.0.0 --namespace lab --replicas=2 --dry-run=client -o yaml > templates/webapp2-deployment.yaml

$ kubectl create service nodeport webapp --tcp=32002:80 --node-port=32002 --namespace lab --dry-run=client -o yaml > templates/webapp-service.yaml

$ kubectl create service nodeport webapp2 --tcp=32003:80 --node-port=32003 --namespace lab --dry-run=client -o yaml > templates/webapp2-service.yaml
```

#### Bước 3: Tạo file `default.conf` cho nginx

```bash
server {
  listen 80;
  server_name localhost;

  location /web1 {
    proxy_pass http://webapp:32002/;
  }

  location /web2 {
    proxy_pass http://webapp2:32003/;
  }
}
```

#### Bước 4: tạo configmap cho nginx-proxy

```bash
$ kubectl create configmap nginx-proxy-config --namespace lab --from-file default.conf --dry-run=client -o yaml > templates/nginx-proxy-config.yaml
```

#### Bước 5: Tạo deployment và service cho nginx proxy

```bash
$ kubectl create deployment nginx-proxy --image nginx:latest --namespace lab --replicas=1 --dry-run=client -o yaml > templates/nginx-proxy-deployment.yaml

$ kubectl create service nodeport nginx-proxy --tcp=80:80 --node-port=32004 --namespace lab --dry-run=client -o yaml > templates/nginx-proxy-service.yaml
```

#### Bước 6: Thêm mount volume vào tempate `nginx-proxy-deployment.yaml` đã tạo ở trên

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-proxy
  namespace: lab
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-proxy
  template:
    metadata:
      labels:
        app: nginx-proxy
    spec:
      containers:
      - name: nginx-proxy
        image: nginx
        resources: {}
        volumeMounts:
            - name: nginx-proxy-volume
              mountPath: /etc/nginx/conf.d/default.conf
              subPath: default.conf
      volumes:
        - name: nginx-proxy-volume
          configMap:
            name: nginx-proxy-config
```

#### Bước 7: Apply toàn bộ templates

```bash
$ kubectl create -f templates/
```

#### Truy cập tới ứng dụng thông qua service

từ máy host truy cập:

- `localhost:32004/web1` -> proxy qua web1

- `localhost:32004/web2` -> proxy qua web2
