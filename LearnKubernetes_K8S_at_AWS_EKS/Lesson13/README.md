YAML 配置 - 建立 Deployment 配置部署
==================================

## 知识点

* 使用 YAML 配置 - 建立 Deployment 配置部署

## 官网

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#deployment-v1-apps

## 实战演习/说明讲解

>看图说话

+ 建立 Deployment 配置 YAML 文件
+ 建立 NodePort 服务配置 YAML 文件
+ 部署 Pod 单元
+ 部署 Pod 服务
+ 动作确认

## 操作步骤

### 建立 Deployment 配置 YAML 文件

*nextweb-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextweb-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nextweb-deployment
  template:
    metadata:
      name: nextweb-pod
      labels:
        app: nextweb-deployment
    spec:
      containers:
        - name: nextweb
          image: komavideo/deeplearnaws-nextweb:v1
          ports:
            - containerPort: 3000
```

### 建立 NodePort 服务配置 YAML 文件

*nextweb-deployment-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-deployment-service
spec:
  type: NodePort 
  selector: 
    app: nextweb-deployment
  ports: 
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 动作确认

```bash
# 部署 Deployment 单元
$ kubectl apply -f nextweb-deployment.yml
$ kubectl get deployments -o wide
$ kubectl get pods -o wide
# 部署 NodePort 服务
$ kubectl apply -f nextweb-deployment-service.yml
$ kubectl get service
$ kubectl get nodes -o wide
$ kubectl get pods -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://52.193.156.249:30462
$ curl http://18.183.15.85:30462
# 删除一波
$ kubectl delete -f nextweb-deployment.yml
$ kubectl delete -f nextweb-deployment-service.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
