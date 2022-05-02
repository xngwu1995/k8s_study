使用 Init 容器检测数据库服务
=========================

## 知识点

* 使用 Init 容器检测后端的数据库服务

## 官网

https://kubernetes.io/zh/docs/concepts/workloads/pods/init-containers/

## 实战演习/说明讲解

>看图说话

+ 沿用上期的 MySql 服务器
+ 编写 Web 应用程序，使用 MySql 服务器
+ 动作确认

## 操作步骤

### 沿用上期的 MySql 服务器

_nextweb/*_

### 编写 Web 应用程序，使用 MySql 服务器

*nextweb/50-nextweb.yml*

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nextweb-pod
  labels: 
    app: nextweb
spec:
  initContainers:
    - name: init-mysqldb
      image: busybox:1.35
      command: ['sh', '-c', 'echo -e "Checking for the availability of MySQL Server"; while ! nc -z mysql 3306; do sleep 1; printf "-"; done; echo -e "  >> MySQL DB Server has started";']
  containers:
    - name: nextweb
      image: komavideo/deeplearnaws-nextweb:v8
      env:
        - name: DBPWD
          valueFrom:
            secretKeyRef:
              name: mysql-secrets
              key: dbpwd
      ports:
        - containerPort: 3000
```

*nextweb/60-nextweb-nodeport-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-nodeport-service
spec:
  type: NodePort
  selector:
    # 从标签中选取操作目标
    app: nextweb
  ports: 
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 动作确认

```bash
# 部署服务
$ kubectl apply -f nextweb/
# 确认部署
$ kubectl get all
# 进入容器 Shell(Docker 内部)
$ kubectl exec -it nextweb-pod -- sh
# 详细描述Pod
$ kubectl describe pod nextweb-pod
# 查看Pod日志
$ kubectl logs -f nextweb-pod
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://54.238.6.76:31873
# Minikube
# $ minikube service nextweb-nodeport-service --url
# $ curl http://worknode-external-ip:Node-Port/api/users
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
