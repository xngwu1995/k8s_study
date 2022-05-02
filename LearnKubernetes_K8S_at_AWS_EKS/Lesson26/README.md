为名空间分配配额 CPU & Memory
===========================

## 知识点

* 为名空间分配配额 CPU & Memory

## 实战演习/说明讲解

+ 沿用前期配置文件
+ 修改名空间配置文件, 增加资源限制
  - ResourceQuota
    - 设定名空间最大使用资源
  - LimitRange
    - 设定 Pod 最大使用资源
+ 动作确认

## 操作步骤

### 沿用前期配置文件

*/k8sweb/10-nextweb-v8-namespaces.yml*  
*/k8sweb/20-nextweb-v8-deployment.yml*  
*/k8sweb/30-nextweb-v8-nodeport-service.yml*  

### 修改名空间配置文件, 增加资源限制

*/k8sweb/10-nextweb-v8-namespaces.yml*

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: koma-dev
---
apiVersion: v1
kind: Namespace
metadata: 
  name: koma-staging
---
apiVersion: v1
kind: Namespace
metadata: 
  name: koma-prod
---
apiVersion: v1
# 设定 名空间 最大使用资源
kind: ResourceQuota
metadata:
  name: ns-resource-quota
  namespace: koma-dev
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
    pods: "5"
    configmaps: "5"
    persistentvolumeclaims: "5"
    replicationcontrollers: "5"
    secrets: "5"
    services: "5"
---
apiVersion: v1
# 设定 Pod 最大使用资源
kind: LimitRange
metadata:
  name: default-cpu-mem-limit-range
  namespace: koma-dev
spec:
  limits:
    - default:
        cpu: "500m"
        memory: "256Mi"
      defaultRequest:
        cpu: "250m"
        memory: "128Mi"
      type: Container
```

### 操作步骤

```bash
# 按名空间部署服务
kubectl apply -f k8sweb/
kubectl get namespaces
kubectl get all -n koma-dev
minikube service --url nextweb-nodeport-service -n koma-dev
>curl http://public-ip:port/api/hello
# 确认 Pod 资源限制
kubectl get limits -n koma-dev
kubectl describe limits default-cpu-mem-limit-range -n koma-dev
# 确认名空间资源限制
kubectl get quota -n koma-dev
kubectl describe quota ns-resource-quota -n koma-dev
# 获取节点信息
kubectl get nodes
kubectl describe node minikube
# 清理现场
kubectl delete -f k8sweb/
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
