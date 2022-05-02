Pod - 基础的计算单元
==================

## 知识点

* 理解 K8S 基础的计算单元 - Pod

## 官网

https://kubernetes.io/zh/docs/concepts/workloads/pods/

## 实战演习/说明讲解

>看图说话

+ 建立第一个 Pod 单元
+ 操作 Pod 单元
+ 删除 Pod 单元

## 操作步骤

```bash
# 确定工作节点状态
$ kubectl get nodes
$ kubectl get nodes -o wide
# 建立第一个 Pod 单元
#  1. K8S 内部建立一个 Pod
#  2. 从 DockerHub 下载指定镜像
#  3. 在 Pod 中建立容器
#  4. 启动 Pod 中容器
$ kubectl run nextweb-pod --image komavideo/deeplearnaws-nextweb:v1
# 列出 Pod 一览
$ kubectl get pods
$ kubectl get pods -o wide
# 描述 Pod 详细信息
$ kubectl describe pod nextweb-pod
# 删除 Pod
$ kubectl delete pod nextweb-pod
```

### kubectl 命令备忘单

https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
