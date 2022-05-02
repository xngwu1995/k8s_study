NodePort - 暴露服务端口给外部使用
==============================

## 知识点

* 使用 NodePort 服务, 分配服务端口给外部使用

## 官网

https://kubernetes.io/zh/docs/concepts/services-networking/service/#type-nodeport

## 实战演习/说明讲解

>看图说话

+ 建立 Pod 单元
+ 使用 NodePort 服务公开 Pod 单元端口
+ 动作确认

## 操作步骤

```bash
# 建立 Pod 单元
$ kubectl run nextweb-pod --image komavideo/deeplearnaws-nextweb:v1
# 列出 Pod 一览(确认该 Pod 部署在哪台工作节点上)
$ kubectl get pods -o wide
# 暴露 Pod 服务端口给外部使用
$ kubectl expose pod nextweb-pod --type=NodePort --port=3000 --name=nextweb-service
# 获取服务信息
$ kubectl get service -o wide
# 确认工作节点 IP
$ kubectl get nodes -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序
#  (*)注意修改安全组开放端口 Node-Port => 自定义TCP:30000-32767
#     sg-1234567890-eksctl-deeplearnaws-nextweb-cluster-nodegroup-deeplearnaws-nextweb-ng-remoteAccess
$ curl http://18.177.144.196:31630
# 确认容器内日志
$ kubectl logs -f nextweb-pod
# 进入容器 Shell(Docker 内部)
$ kubectl exec -it nextweb-pod -- sh
>ls
>ls pages
>ls pages/api
>cat pages/api/hello.js
>exit
# 直接执行容器命令
$ kubectl exec -it nextweb-pod -- ls
$ kubectl exec -it nextweb-pod -- cat pages/api/hello.js
# 获取当前运行的 Pod YAML 配置
$ kubectl get pod nextweb-pod -o yaml > nextweb-pod.yml
# 获取当前运行的服务的 YAML 配置
$ kubectl get service nextweb-service -o yaml > nextweb-service.yml
# 获取默认名空间的所有对象内容
$ kubectl get all
...
NAME              READY   STATUS    RESTARTS   AGE
pod/nextweb-pod   1/1     Running   0          25m

NAME                      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP   10.100.0.1     <none>        443/TCP          45m
service/nextweb-service   NodePort    10.100.50.15   <none>        3000:31449/TCP   24m
...
# 删除服务
$ kubectl delete svc nextweb-service
# 删除 Pod
$ kubectl delete pod nextweb-pod
# 再次确认集群内容
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
