创建删除 EKS 集群
================

## 知识点

* 使用 eksctl 创建删除 EKS 集群

## 官网

https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/create-cluster.html

## eksctl - The official CLI for Amazon EKS

https://eksctl.io/

## 实战演习/说明讲解

>看图说话

+ 创建 EKS 集群
+ 建立节点组 Node Group
+ 删除 EKS 集群

## 操作步骤

### 创建 EKS 集群

```bash
# 创建 EKS 集群
# -> with-oidc: 启用IAM
# -> without-nodegroup: 创建集群时不创建节点组
$ eksctl create cluster \
    --name=deeplearnaws-nextweb-cluster \
    --region=ap-northeast-1 \
    --zones=ap-northeast-1a,ap-northeast-1c \
    --with-oidc \
    --without-nodegroup
# 自动生成的本地环境目录
$ ll .kube
# 得到集群列表
$ eksctl get cluster --region=ap-northeast-1
# 描述集群(包含KEY信息)
$ eksctl utils describe-stacks --region=ap-northeast-1 --cluster=deeplearnaws-nextweb-cluster
```

### 建立节点组 Node Group

```bash
# 在 EKS 集群内建立节点组
$ eksctl create nodegroup \
    --cluster=deeplearnaws-nextweb-cluster \
    --region=ap-northeast-1 \
    --name=deeplearnaws-nextweb-ng \
    --node-type=t3.small \
    --nodes=2 \
    --nodes-min=2 \
    --nodes-max=4 \
    --node-volume-size=20 \
    --managed \
    --asg-access \
    --external-dns-access \
    --full-ecr-access \
    --alb-ingress-access

# 更改工作节点数
$ eksctl scale nodegroup \
    --cluster=deeplearnaws-nextweb-cluster \
    --region=ap-northeast-1 \
    --nodes=4 \
    --name=deeplearnaws-nextweb-ng

# 列出集群内节点组
$ eksctl get nodegroup --cluster=deeplearnaws-nextweb-cluster --region=ap-northeast-1

# 列出节点明细
$ kubectl get nodes -o wide

# 确认集群设置
$ kubectl config view
```

### 删除 EKS 集群

```bash
# 删除节点组
# kubectl delete pod/ebs-csi-controller-123456789-aaaaa -n kube-system
$ eksctl delete nodegroup --cluster=deeplearnaws-nextweb-cluster --name=deeplearnaws-nextweb-ng --region=ap-northeast-1
>确认工作节点组删除后，再删除集群
# 删除集群
$ eksctl delete cluster deeplearnaws-nextweb-cluster --region=ap-northeast-1
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
