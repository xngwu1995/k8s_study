Deployment - 产品环境部署应用程序
===============================

## 知识点

* 使用 Deployment - 部署产品环境应用程序

## 官网

https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/

## 实战演习/说明讲解

>看图说话

+ 利用 Docker 镜像快速创建部署 - Deployment
+ 扩展部署, 实时调整 replicaset 部署数量
+ 暴露部署给客户端
+ 更新部署的应用程序
  - v1 → v2
+ 回滚部署版本
+ 动作确认

## 操作步骤


```bash
# 快速创建产品部署
$ kubectl create deployment nextweb-deployment --image=komavideo/deeplearnaws-nextweb:v1
# 确认默认名空间生成的内容(一个部署，一个ReplicaSet，一个Pod)
$ kubectl get all
# 确认部署
$ kubectl get deployments
$ kubectl get deployments -o wide
# 描述部署
$ kubectl describe deployment nextweb-deployment
# 确认部署生成的 ReplicaSet
$ kubectl get replicaset
$ kubectl get replicaset -o wide
# 确认部署生成的 Pod
$ kubectl get pods
# 扩展部署(可根据系统负载随时调整)
$ kubectl scale --replicas=2 deployment/nextweb-deployment
# $ kubectl scale --replicas=3 deployment/nextweb-deployment
# $ kubectl scale --replicas=5 deployment/nextweb-deployment
# 确认扩展
$ kubectl get all
$ kubectl get deployments
$ kubectl get replicaset
$ kubectl get pods
# 暴露部署给外界使用
$ kubectl expose deployment nextweb-deployment --type=NodePort --port=80 --target-port=3000 --name=nextweb-deployment-service
# 确认服务信息
$ kubectl get service
# 确认节点信息
$ kubectl get nodes -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序
$ curl http://54.238.183.236:30257
$ curl http://54.248.158.27:30257
# 编辑部署
#   由于 EC2 资源不足，可先把 replicas 改为 1 后，再进行版本更新!!!否则会出现 Pending...
#   $ kubectl scale --replicas=1 deployment/nextweb-deployment
$ kubectl edit deployment/nextweb-deployment --record=true
...
# 更新容器版本号
komavideo/deeplearnaws-nextweb:v1
↓
komavideo/deeplearnaws-nextweb:v2
...
# 确认滚动更新状态
$ kubectl rollout status deployment/nextweb-deployment
...
Waiting for deployment "nextweb-deployment" rollout to finish: 1 out of 2 new replicas have been updated...
...
# 确认新版本 v2
$ curl http://worknode-external-ip:Node-Port
# 确认滚动更新历史
$ kubectl rollout history deployment/nextweb-deployment
...
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl edit deployment/nextweb-deployment --record=true
...
# 显示单个回滚历史详细
$ kubectl rollout history deployment/nextweb-deployment --revision=1
$ kubectl rollout history deployment/nextweb-deployment --revision=2
# 回滚历史(上一版本)
$ kubectl rollout undo deployment/nextweb-deployment
# 回滚历史(指定版本)
# $ kubectl rollout undo deployment/nextweb-deployment --to-revision=2
# 动作确认
$ curl http://worknode-external-ip:Node-Port
# 确认滚动更新状态
$ kubectl rollout status deployment/nextweb-deployment
# 重新部署
$ kubectl rollout restart deployment/nextweb-deployment
# 最后删除一波
$ kubectl delete svc nextweb-deployment-service
$ kubectl delete deployment nextweb-deployment
$ kubectl get all
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
