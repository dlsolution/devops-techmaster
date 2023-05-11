### Đề bài
LEVEL 1: triển khai deployment chạy nginx (default) lên kubernetes và cho phép truy cập từ bên ngoài thông qua nodePort

### Output:
- 1 deployment nginx (replicas=2 pod)
- 1 nodePort service trỏ tới deployment
- thực hiên curl tới nodePort và cho ra kết quả trang web mặc định của nginx

---

### Các bước thực hiện

#### Bước 0: tạo cluster với KinD

```bash
$ kind create cluster --name lab --config kind.conf      
```

#### Bước 1: tạo deployment với image nginx và replicas=2

```bash
$ kubectl create deployment nginx --image nginx:latest --replicas=2 --dry-run=client -o yaml > templates/nginx-deployment.yaml
```

Kiểm tra nội dung template đã được generate:

```bash
$ cat templates/nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:latest
        name: nginx
        resources: {}
status: {}
```

Create deployment:

```bash
$ kubectl create -f templates/nginx-deployment.yaml 
deployment.apps/nginx created
```

#### Bước 2: tạo nodeport service

```bash
$ kubectl create service nodeport nginx --tcp=80:80 --node-port=32002 --dry-run=client -o yaml > templates/nginx-service.yaml
```

Kiểm tra nội dung template đã được generate:

```bash
$ cat templates/nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  ports:
  - name: 80-80
    port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 32002
  selector:
    app: nginx
  type: NodePort
status:
  loadBalancer: {}
```

Create service:

```bash
$ kubectl create -f templates/nginx-service.yaml 
service/nginx created
```

Kiểm tra trạng thái của service sau khi tạo:

```bash
$ kubectl get service nginx
NAME    TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.96.102.0   <none>        80:32002/TCP   3m40s
```

Kiểm tra các thông tin trên service:

```bash
$ kubectl describe service nginx
Name:                     nginx
Namespace:                default
Labels:                   app=nginx
Annotations:              <none>
Selector:                 app=nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.96.102.0
IPs:                      10.96.102.0
Port:                     80-80  80/TCP
TargetPort:               80/TCP
NodePort:                 80-80  32002/TCP
Endpoints:                10.244.1.8:80,10.244.1.9:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

#### Truy cập tới ứng dụng thông qua service 

```bash
$ curl 127.0.0.1:32002
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

### Evident
<img src="/kubernetes/lab-1/images/lab-1.png" width="50%">