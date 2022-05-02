在指定 VPC 和子网组中生成 EKS 集群
===============================

## 知识点

* 使用 eksctl 在指定 VPC 和子网组中生成 EKS 集群

## 实战演习/说明讲解

>看图说话

+ 建立 VPC 和各 AZ 区子网
+ 建立 cluster.yaml 集群描述文件
+ 部署确认

## 操作步骤

### 官网

https://eksctl.io/usage/creating-and-managing-clusters/

#### Config file schema

https://eksctl.io/usage/schema/

#### Examples

https://github.com/weaveworks/eksctl/tree/main/

### 建立 VPC 和各 AZ 区子网

+ deeplearnaws-vpc

### 建立 cluster.yaml 集群描述文件

*cluster.yaml*

```yml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: deeplearnaws-nextweb-cluster
  region: ap-northeast-1

iam:
  withOIDC: true

vpc:
  subnets:
    public:
      public-eks1:
        id: subnet-0b7f77d791582e6b3
      public-eks2:
        id: subnet-06e1cc40b41368ab3

managedNodeGroups:
  - name: nodegroup-1
    amiFamily: AmazonLinux2
    instanceType: t3.micro
    minSize: 1
    maxSize: 4
    desiredCapacity: 2
    volumeSize: 8
    volumeType: gp2
    labels:
      nodegroup-type: frontend-workloads
    ssh:
      allow: true
      publicKeyName: deeplearnaws-ssh-key
    securityGroups:
      attachIDs:
        - sg-0e30a29941920eb78
    iam:
      withAddonPolicies:
        imageBuilder: true
        autoScaler: true
        externalDNS: true
        albIngress: true
        ebs: true
        cloudWatch: true
#   - name: nodegroup-2
#     amiFamily: AmazonLinux2
#     instanceType: t2.micro
#     desiredCapacity: 1
#     volumeSize: 10
#     volumeType: gp2
#     labels:
#       nodegroup-type: backend-workloads
cloudWatch:
  clusterLogging:
    # all supported types: "api", "audit", "authenticator", "controllerManager", "scheduler"
    # supported special values: "*" and "all"
    enableTypes: ["audit", "authenticator", "controllerManager"]

    # Sets the number of days to retain the logs for (see [CloudWatch docs](https://docs.aws.amazon.com/AmazonCloudWatchLogs/latest/APIReference/API_PutRetentionPolicy.html#API_PutRetentionPolicy_RequestSyntax)).
    # By default, log data is stored in CloudWatch Logs indefinitely.
    logRetentionInDays: 60
```

### 部署确认

```bash
# 建立 EKS 集群
eksctl create cluster -f cluster.yaml --dry-run
# 得到集群列表
eksctl get cluster --region=ap-northeast-1
# 列出集群内节点组
eksctl get nodegroup --cluster=deeplearnaws-nextweb-cluster --region=ap-northeast-1
# 列出节点明细
kubectl get nodes -o wide
# 删除 EKS 集群
eksctl delete cluster -f cluster.yaml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
