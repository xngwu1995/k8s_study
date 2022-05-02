YAML 配置 - 快速建立 ReplicaSet Pod 集合
======================================

## 知识点

* 使用 YAML 配置 - 快速建立 ReplicaSet Pod 集合

## 官网

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#replicaset-v1-apps

## 实战演习/说明讲解

>看图说话

+ 建立 ReplicaSet 配置 YAML 文件
+ 建立 NodePort 服务配置 YAML 文件
+ 部署 Pod 单元
+ 部署 Pod 服务
+ 动作确认

## 操作步骤

### 建立 ReplicaSet 配置 YAML 文件

*nextweb-rs.yml*

```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nextweb-rs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nextweb-rs
  template:
    metadata:
      name: nextweb-pod
      labels:
        app: nextweb-rs
    spec:
      containers:
      - name: nextweb
        image: komavideo/deeplearnaws-nextweb:v1
        ports:
          - containerPort: 3000
```

### 建立 NodePort 服务配置 YAML 文件

*nextweb-rs-nodeport-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-rs-nodeport-service
spec:
  type: NodePort
  selector:
    app: nextweb-rs
  ports:
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 动作确认

```bash
# 部署 ReplicaSet 单元
$ kubectl apply -f nextweb-rs.yml
$ kubectl get replicaset -o wide
$ kubectl get pods -o wide
# 部署 NodePort 服务
$ kubectl apply -f nextweb-rs-nodeport-service.yml
$ kubectl get service
$ kubectl get nodes -o wide
$ kubectl get pods -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://13.113.207.77:31876
$ curl http://3.112.196.36:31876
# 删除一波
$ kubectl delete -f nextweb-rs-nodeport-service.yml
$ kubectl delete -f nextweb-rs.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
