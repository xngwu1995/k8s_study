部署 ALB 负载均衡器
==================

## 知识点

* 在 EKS 集群中部署 ALB 负载均衡器 + nextweb-v8

## 实战演习/说明讲解

>看图说话

+ 配置部署 nextweb-v8 服务
+ 编写部署文件，通过 Ingress 部署配置负载均衡器
+ 部署确认

## 操作步骤

### 配置部署 nextweb-v8 服务

*10-nextweb.yml*

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
---
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
```

```bash
# 部署 nextweb-v8
kubectl apply -f 10-nextweb.yml
# 确认部署
kubectl get all
# 清理部署
kubectl delete -f 10-nextweb.yml
```

### 编写部署文件，通过 Ingress 部署配置负载均衡器

*20-alb-ingress-nextweb.yml*

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nextweb
  labels:
    app: nextweb
  annotations:
    # ALB名称
    alb.ingress.kubernetes.io/load-balancer-name: alb-nextweb
    # ALB类型
    alb.ingress.kubernetes.io/scheme: internet-facing
    # 使用子网设置
    alb.ingress.kubernetes.io/subnets: subnet-0b7f77d791582e6b3, subnet-06e1cc40b41368ab3
    # ALB健康设置
    alb.ingress.kubernetes.io/healthcheck-protocol: HTTP 
    alb.ingress.kubernetes.io/healthcheck-port: traffic-port
    alb.ingress.kubernetes.io/healthcheck-path: /api/hello
    alb.ingress.kubernetes.io/healthcheck-interval-seconds: '15'
    alb.ingress.kubernetes.io/healthcheck-timeout-seconds: '5'
    alb.ingress.kubernetes.io/success-codes: '200'
    alb.ingress.kubernetes.io/healthy-threshold-count: '2'
    alb.ingress.kubernetes.io/unhealthy-threshold-count: '2'
spec:
  ingressClassName: deeplearnaws-ingress-class # Ingress Class
  defaultBackend:
    service:
      name: nextweb-nodeport-service # NodePort 服务名
      port:
        number: 3000 # 容器 Port 端口
```

### 部署确认

```bash
# 部署 ALB
kubectl apply -f 20-alb-ingress-nextweb.yml
# 确认生成的 Ingress
kubectl get ingress
kubectl describe ingress ingress-nextweb
# 确认 ALB 控制器日志
kubectl get pods -n kube-system | grep aws-load-balancer-controller
kubectl -n kube-system logs -f aws-load-balancer-controller-000000000-11111
# 清理部署
kubectl delete -f 20-alb-ingress-nextweb.yml
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
