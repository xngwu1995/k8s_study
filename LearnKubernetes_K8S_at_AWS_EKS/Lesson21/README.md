使用 livenessProbe 检测容器存活
==============================

## 知识点

* 使用 livenessProbe 检测容器存活

## 实战演习/说明讲解

>看图说话

+ 编写部署文件，添加容器存活检测配置
+ 实施服务文件部署
+ 确认服务启动状态

## 操作步骤

### 官网

https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

### Minikube

https://kubernetes.io/zh/docs/tutorials/hello-minikube/

### 编写部署文件，添加容器存活检测配置

*nextweb-v8-deployment.yml*

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextweb-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nextweb-deployment
  template:
    metadata:
      name: nextweb-pod
      labels:
        app: nextweb-deployment
    spec:
      containers:
        - name: nextweb
          image: komavideo/deeplearnaws-nextweb:v8
          ports:
            - containerPort: 3000
          # 存活探测: 一旦容器启动失败，则会重新启动容器 "RESTARTS"
          # livenessProbe:
          # 就绪探测: 一旦容器启动失败，则会暂停容器提供服务，不会重新启动容器
          readinessProbe:
            httpGet:
              path: /api/hello
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 10
```

### 实施服务文件部署, 确认服务启动状态

```bash
# 部署 Deployment 单元
$ kubectl apply -f nextweb-v8-deployment.yml
$ kubectl get all
# 显示 Pods 详细信息
$ kubectl get pods -o wide
# 描述 Pod 信息
$ kubectl describe pod nextweb-deployment-7ff6999477-v7nlg
# 进入容器 Shell(Docker 内部)
$ kubectl exec -it nextweb-deployment-7ff6999477-v7nlg -- sh
> wget -S http://127.0.0.1:3000/api/hello
> wget -S http://127.0.0.1:3000/api/helloworld
#######################################
# 修改存活检测路径，再次部署确认
$ nano nextweb-v8-deployment.yml
...
path: /api/hello
↓
path: /api/helloworld
...
# 部署 Deployment 单元
$ kubectl apply -f nextweb-v8-deployment.yml
# 观察 "RESTARTS" 次数和 "READY" 状态
$ kubectl get all
# 确认容器启动日志
$ kubectl describe pod nextweb-deployment-1234567890-abcde
>  Warning  Unhealthy  4s (x13 over 2m4s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 404

# 删除一波
$ kubectl delete -f nextweb-v8-deployment.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
