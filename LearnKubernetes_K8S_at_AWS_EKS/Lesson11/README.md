YAML 配置 - 建立 Pod 并暴露服务给客户端
===================================

## 知识点

* 使用 YAML 配置 - 建立 Pod 并暴露服务给客户端

## 官网

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#pod-v1-core

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#service-v1-core

## 实战演习/说明讲解

>看图说话

+ 建立 Pod 配置 YAML 文件
+ 建立 NodePort 服务配置 YAML 文件
+ 部署 Pod 单元
+ 部署 Pod 服务
+ 动作确认

## 操作步骤

### 建立 Pod 配置 YAML 文件

*nextweb-pod.yml*

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nextweb-pod
  labels: 
    app: nextweb
spec:
  containers:
    - name: nextweb
      image: komavideo/deeplearnaws-nextweb:v1
      ports:
        - containerPort: 3000
```

*nextweb-nodeport-service.yml*

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
# 部署 Pod 单元
$ kubectl apply -f nextweb-pod.yml
$ kubectl get pods -o wide
# 部署 NodePort 服务
$ kubectl apply -f nextweb-nodeport-service.yml
$ kubectl get all
$ kubectl get nodes -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://13.113.207.77:31069
# 删除一波
$ kubectl delete -f nextweb-nodeport-service.yml
$ kubectl delete -f nextweb-pod.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
