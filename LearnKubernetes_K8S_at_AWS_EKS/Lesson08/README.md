ReplicaSet - Pod 计算负载的集合
=============================

## 知识点

* 使用 ReplicaSet 完成 Pod 计算负载的集合

## 官网

https://kubernetes.io/zh/docs/concepts/workloads/controllers/replicaset/

## 实战演习/说明讲解

>看图说话

+ 建立 nextweb 应用程序的 ReplicaSet 描述文件
+ 部署 ReplicaSet 服务
+ 暴露 ReplicaSet 服务
+ 动作确认

## 操作步骤

### 建立 nextweb 应用程序的 ReplicaSet 描述文件

*nextweb-replicaset.yml*

```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nextweb-rs
  labels:
    app: nextweb
    tier: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nextweb
        image: komavideo/deeplearnaws-nextweb:v1
```

### 部署 ReplicaSet 服务

```bash
# 部署 ReplicaSet 服务
$ kubectl apply -f nextweb-replicaset.yml
# 查看 ReplicaSet 部署状态
$ kubectl get replicaset
$ kubectl get replicaset -o wide
# 描述 ReplicaSet 服务
$ kubectl describe rs/nextweb-rs
# 确认生成的 Pod 单元
$ kubectl get pods
$ kubectl get pods -o wide
# 描述单个 Pod 单元
$ kubectl describe pod [podname]
$ kubectl get pods [podname] -o yaml
# 暴露 ReplicaSet 服务(外部可存取)
#   1. NodePort 外部存取端口
#   2. port 内部存取端口
#   3. target-port 容器本身暴露端口
$ kubectl expose rs nextweb-rs --type=NodePort --port=80 --target-port=3000 --name=nextweb-rs-service
# 确认服务信息
$ kubectl get service
# 确认工作节点 IP
$ kubectl get nodes -o wide
$ kubectl get pods -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(修改EC2安全组)
$ curl http://54.238.183.236:31550
$ curl http://54.248.158.27:31550
# 默认名空间服务确认
$ kubectl get all
# 删除一波
$ kubectl delete svc/nextweb-rs-service
$ kubectl delete rs/nextweb-rs
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
