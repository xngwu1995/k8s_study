实施资源限制 CPU & Memory
========================

## 知识点

* 使用 Resources 实施资源限制 CPU & Memory

## 官网

https://kubernetes.io/zh/docs/concepts/configuration/manage-resources-containers/

## 实战演习/说明讲解

>看图说话

+ 编写部署文件，添加资源限制 CPU & Memory
+ 实施服务文件部署
+ 确认工作节点状态

## 操作步骤

### Minikube

https://kubernetes.io/zh/docs/tutorials/hello-minikube/

### 编写部署文件，添加资源限制 CPU & Memory

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
          livenessProbe:
            httpGet:
              path: /api/hello
              port: 3000
            initialDelaySeconds: 5
            periodSeconds: 10
          resources:
            requests:
              cpu: "500m"
              memory: "128Mi"
            limits:
              cpu: "1000m"
              memory: "256Mi"
```

*nextweb-v8-nodeport-service.yml*

```yml
apiVersion: v1
kind: Service
metadata:
  name: nextweb-nodeport-service
spec:
  type: NodePort
  selector:
    # 从标签中选取操作目标
    app: nextweb-deployment
  ports: 
    - name: http
      port: 3000
      targetPort: 3000
      #nodePort: 32020
```

### 实施服务文件部署, 确认工作节点状态

```bash
# 部署 Deployment 单元
$ kubectl apply -f nextweb-v8-deployment.yml
$ kubectl apply -f nextweb-v8-nodeport-service.yml
# 确认 NodePort 端口
$ kubectl get all
>service/nextweb-nodeport-service NodePort 10.107.106.64 <none> 3000:30052/TCP 5s
# 获取运行 pod 的其中一个节点的公共 IP 地址
$ kubectl cluster-info
>Kubernetes control plane is running at https://172.17.0.41:8443
# 确认服务(公共 IP + NodePort 端口)
$ curl http://172.17.0.36:30507/api/hello
# 显示 Pods 详细信息
$ kubectl get pods -o wide
# 确认工作节点状态
$ kubectl get nodes
$ kubectl describe node minikube
# 删除一波
$ kubectl delete -f nextweb-v8-deployment.yml
$ kubectl delete -f nextweb-v8-nodeport-service.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
