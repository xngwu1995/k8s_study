YAML 配置 - 配置服务, 集成系统
============================

## 知识点

* 使用 YAML 配置 - 配置服务, 集成系统

## 实战演习/说明讲解

>看图说话

+ 编写后端 API 配置 YAML 文件
+ 编写后端 API 内部部署 YAML 文件
+ 编写前端 Web 配置 YAML 文件
+ 编写前端 Web 公开部署 YAML 文件
+ 动作确认

## 操作步骤

### 编写后端 API 配置 YAML 文件

*nextapi-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-nextapi
  labels: 
    app: backend-nextapi
    tier: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend-nextapi
  template: 
    metadata:
      labels:
        app: backend-nextapi
        tier: backend
    spec:
      containers:
        - name: backend-nextapi
          image: komavideo/deeplearnaws-nextapi:v1
          ports:
            - containerPort: 8080
```

### 编写后端 API 内部部署 YAML 文件

*nextapi-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextapi-service # 重要: 前端 Web 应用通过该名称访问 API 服务
  labels: 
    app: backend-nextapi
    tier: backend
spec:
  selector:
    app: backend-nextapi
  ports: 
    - name: http
      port: 8080
      targetPort: 8080
```

### 编写前端 Web 配置 YAML 文件

*nextweb-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-nextweb
  labels: 
    app: frontend-nextweb
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend-nextweb
  template: 
    metadata:
      labels:
        app: frontend-nextweb
        tier: frontend
    spec:
      containers:
        - name: frontend-nextweb
          image: komavideo/deeplearnaws-nextweb:v7
          ports:
            - containerPort: 3000
```

### 编写前端 Web 公开部署 YAML 文件

*nextweb-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-service
  labels: 
    app: frontend-nextweb
    tier: frontend     
spec:
  type: NodePort 
  selector:
    app: frontend-nextweb
  ports: 
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 动作确认

```bash
# 部署 nextapi 单元
$ kubectl apply -f nextapi-deployment.yml
$ kubectl get deployments -o wide
$ kubectl get pods -o wide
# 部署 nextapi NodePort 服务
$ kubectl apply -f nextapi-service.yml
$ kubectl get service
# 部署 nextweb 单元
$ kubectl apply -f nextweb-deployment.yml
$ kubectl get deployments -o wide
$ kubectl get pods -o wide
# 部署 nextweb NodePort 服务
$ kubectl apply -f nextweb-service.yml
$ kubectl get service
$ kubectl get nodes -o wide
$ kubectl get pods -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://52.193.156.249:31876
# 删除一波
$ kubectl delete -f nextapi-deployment.yml
$ kubectl delete -f nextapi-service.yml
$ kubectl delete -f nextweb-deployment.yml
$ kubectl delete -f nextweb-service.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
