使用 yml 配置名空间应用程序
========================

## 知识点

* 使用 yml 配置名空间应用程序

## 实战演习/说明讲解

+ 建立部署文件
  - 名空间
  - 部署文件
  - 服务文件
+ 动作确认

## 操作步骤

### Minikube

https://kubernetes.io/zh/docs/tutorials/hello-minikube/

### 建立部署文件

*/k8sweb/10-nextweb-v8-namespaces.yml*

```yml
apiVersion: v1
kind: Namespace
metadata: 
  name: koma-dev
---
apiVersion: v1
kind: Namespace
metadata: 
  name: koma-staging
---
apiVersion: v1
kind: Namespace
metadata: 
  name: koma-prod
```

*/k8sweb/20-nextweb-v8-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextweb-deployment
  namespace: koma-dev
spec:
  replicas: 1
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
          image: komavideo/deeplearnaws-nextweb:v8
          ports:
            - containerPort: 3000
```

*/k8sweb/30-nextweb-v8-nodeport-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-nodeport-service
  namespace: koma-dev
spec:
  type: NodePort
  selector:
    # 从标签中选取操作目标
    app: nextweb-deployment
  ports: 
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 动作确认

```bash
# 建立配置文件
mkdir k8sweb
nano k8sweb/10-nextweb-v8-namespaces.yml
nano k8sweb/20-nextweb-v8-deployment.yml
nano k8sweb/30-nextweb-v8-nodeport-service.yml

# 按名空间部署服务
kubectl apply -f k8sweb/
kubectl get namespaces
kubectl get all -n koma-dev
minikube service --url nextweb-nodeport-service -n koma-dev
>curl http://public-ip:port/api/hello

# 确认各个名空间的部署内容
kubectl get all --all-namespaces

# 清理战场
kubectl delete -f k8sweb/
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
