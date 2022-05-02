使用 Secrets 加密数据库口令
=========================

## 知识点

* 使用 Secrets 加密 MySql 数据库口令

## 官网

https://kubernetes.io/zh/docs/concepts/configuration/secret/

## 实战演习/说明讲解

+ 使用 ConfigMap 设置数据库初始化脚本
+ 建立 Secrets 密钥存储配置
+ 建立 MySql 数据库服务器配置
+ 编写 MySql 公开部署文件
+ 动作确认

## 操作步骤

### 使用 ConfigMap 设置数据库初始化脚本

*nextweb/10-mysql-initdb-script.yml*

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-initdb-script
data: 
  mysql_initdb_script.sql: |-
    DROP DATABASE IF EXISTS blogdb;
    CREATE DATABASE blogdb;
    create table if not exists blogdb.user (id int, name varchar(255));
    insert into blogdb.user values (1, 'Koma');
    insert into blogdb.user values (2, 'Xiaoma');
    insert into blogdb.user values (3, 'MySql');
    insert into blogdb.user values (4, 'RDS For MySql');
    insert into blogdb.user values (5, 'Amazon Aurora');
```

### 建立 Secrets 密钥存储配置

```bash
# 获取口令base64编码
echo -n '12345678aa' | base64
>MTIzNDU2NzhhYQ==
# URL: https://www.base64encode.org
```

*nextweb/20-mysql-secrets.yml*

```yml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secrets
type: Opaque
data:
  dbpwd: MTIzNDU2NzhhYQ==
```

### 建立 MySql 数据库服务器配置

*nextweb/30-mysql-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec: 
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate 
  template: 
    metadata: 
      labels: 
        app: mysql
    spec: 
      containers:
        - name: mysql
          image: mysql:5.6.51
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secrets
                  key: dbpwd
          ports:
            - containerPort: 3306
              name: mysql    
          volumeMounts:
            - name: mysql-initdb-script
              mountPath: /docker-entrypoint-initdb.d
      volumes: 
        - name: mysql-initdb-script
          configMap:
            name: mysql-initdb-script
```

### 编写 MySql 公开部署文件

*nextweb/40-mysql-clusterip-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
      targetPort: 3306
  #clusterIP: None # 生产环境部署时，禁止暴露数据库给外部
```

### 动作确认

```bash
# 部署服务
$ kubectl apply -f nextweb/
# 确认部署
$ kubectl get all
# 确认加密内容
$ kubectl get secrets
$ kubectl describe secret/mysql-secrets
# 确认初始化脚本
$ kubectl get configmap
$ kubectl describe configmap mysql-initdb-script
# 确认部署
$ kubectl get deployment
$ kubectl get pods -o wide
$ kubectl logs -f pod/mysql-pod-1234567890
$ kubectl describe pod/mysql-pod-1234567890
$ kubectl get service
$ kubectl get nodes -o wide

# MySql 客户端确认
$ kubectl exec -it mysql-pod-1234567890 -- sh
# MySQL版本确认
$ mysql -V
# 数据库连接(dbpwd:12345678aa)
$ mysql -u root -p
mysql> show databases;
mysql> use blogdb;
mysql> show tables;
mysql> select * from blogdb.user;
mysql> exit;

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
