按名空间部署应用
==============

## 知识点

* 按名空间类别(dev, prod)部署应用

## 实战演习/说明讲解

>看图说话

+ 建立服务部署文件
+ 建立名空间分类
+ 按名空间部署服务
+ 动作确认

## 操作步骤

### Minikube

https://kubernetes.io/zh/docs/tutorials/hello-minikube/

### 建立服务部署文件

*/k8sweb/nextweb-v8-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextweb-deployment
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

*/k8sweb/nextweb-v8-nodeport-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-nodeport-service
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
mkdir k8sweb
nano k8sweb/nextweb-v8-deployment.yml
nano k8sweb/nextweb-v8-nodeport-service.yml

# 建立名空间分类
kubectl get namespace
kubectl create namespace koma-dev
kubectl create namespace koma-staging
kubectl create namespace koma-prod
kubectl get namespace

# 按名空间部署服务
kubectl apply -f k8sweb/ -n koma-dev
kubectl get all -n koma-dev
kubectl get all -n default
minikube service --url nextweb-nodeport-service -n koma-dev
>curl http://public-ip:port/api/hello

kubectl apply -f k8sweb/ -n koma-prod
kubectl get all -n koma-prod
minikube service --url nextweb-nodeport-service -n koma-prod
>curl http://public-ip:port/api/hello

# 确认各个名空间的部署内容
kubectl get all --all-namespaces

# 清理战场
kubectl delete -f k8sweb/ -n koma-dev
kubectl delete -f k8sweb/ -n koma-prod
kubectl get all -n koma-dev
kubectl get all -n koma-prod

# 删除名空间
kubectl get namespace
kubectl delete namespace koma-dev
kubectl delete namespace koma-staging
kubectl delete namespace koma-prod
kubectl get namespace
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
