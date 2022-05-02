使用 ExternalName Service 暴露 RDS 服务
======================================

## 知识点

* 在 EKS 集群中使用 ExternalName Service 暴露 RDS 服务

## 实战演习/说明讲解

>看图说话

+ 在相同的 VPC 中建立 RDS 数据库服务
+ 编写部署文件，使用 ExternalName Service 添加 DNS 映射
+ 部署确认

## 操作步骤

### 官网

https://kubernetes.io/zh/docs/concepts/services-networking/service/#externalname

### 在相同的 VPC 中建立 RDS 数据库服务

+ 确认 RDS 数据库服务
+ 确认终端节点

### 编写部署文件，使用 ExternalName Service 添加 DNS 映射

*10-rds-externalname-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: rds-mysql
spec:
  type: ExternalName
  externalName: database-1.cbcbjcjywvwn.ap-northeast-1.rds.amazonaws.com
```

### 部署确认

```bash
# 部署 RDS ExternalName Service 服务
kubectl apply -f 10-rds-externalname-service.yml
# 确认生成的 ExternalName 服务
kubectl get all
# 启动 MySql Pod 连接 RDS-MySql 服务
kubectl run -it --rm --image=mysql:5.7.36 --restart=Never mysql-client -- sh
# @MySql-Pod 内部
$ mysql -h database-1.cbcbjcjywvwn.ap-northeast-1.rds.amazonaws.com -u root -p
mysql> show databases;
mysql> exit
# 清理现场
kubectl delete -f 10-rds-externalname-service.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
