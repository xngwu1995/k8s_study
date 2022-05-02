部署 phpmyadmin 连接 MySql 服务器
===============================

## 知识点

* 部署 phpmyadmin 连接 MySql 服务器

## 官网

https://hub.docker.com/r/phpmyadmin/phpmyadmin/

## 实战演习/说明讲解

>看图说话

+ 建立 phpmyadmin 部署设置
+ 暴露 phpmyadmin 服务
+ 动作确认

## 操作步骤

### 建立 phpmyadmin 部署设置

*nextweb/60-phpmyadmin-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phpmyadmin-deployment
  labels:
    app: phpmyadmin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: phpmyadmin
  template:
    metadata:
      labels:
        app: phpmyadmin
    spec:
      containers:
        - name: phpmyadmin
          image: phpmyadmin/phpmyadmin
          ports:
            - containerPort: 80
          env:
            - name: PMA_HOST
              value: mysql
            - name: PMA_PORT
              value: "3306"
            - name: MYSQL_ROOT_PASSWORD
              value: "12345678"
```

### 暴露 phpmyadmin 服务

*nextweb/70-phpmyadmin-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: phpmyadmin-service
spec:
  type: NodePort
  selector:
    app: phpmyadmin
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
      #nodePort: 30011
```

### 动作确认

```bash
# 部署 MySql 服务
$ kubectl apply -f nextweb/
# 部署 phpmyadmin
$ kubectl apply -f nextweb/60-phpmyadmin-deployment.yml
$ kubectl apply -f nextweb/70-phpmyadmin-service.yml
$ kubectl get all
$ kubectl get nodes -o wide
$ kubectl get pods -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://54.238.6.76:30043

# 清理项目
$ kubectl delete -f nextweb/
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
