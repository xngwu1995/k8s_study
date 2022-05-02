集成 AWS 负载均衡器 - AWS Load Balancer Controller
================================================

## 知识点

* 在 EKS 集群中集成 AWS 负载均衡器

## K8S

https://kubernetes.io/zh/docs/concepts/services-networking/ingress/

## 实战演习/说明讲解

>看图说话

+ 为 EKS 集群安装 AWS 负载均衡器控制器
+ 设置 IngressClass 入口类
+ 部署确认

## 操作步骤

### AWS Load Balancer Controller

https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.4/

```bash
# 确认 EKS 集群中的 IAM 服务账号
eksctl get iamserviceaccount --cluster=deeplearnaws-nextweb-cluster --region=ap-northeast-1

# 下载 EKS 服务账号 IAM 策略文件
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.0/docs/install/iam_policy.json

# 建立 IAM 策略文件
aws iam delete-policy --policy-arn arn:aws:iam::976591345876:policy/AWSLoadBalancerControllerIAMPolicy
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam-policy.json

>确认建立的策略
>记录下策略的 ARN
`arn:aws:iam::976591345876:policy/AWSLoadBalancerControllerIAMPolicy`

# 建立 IAM 服务账号, 使用上述步骤中的 ARN
#   1)建立 IAM 角色
#   2)建立 EKS 服务账号
eksctl create iamserviceaccount \
  --cluster=deeplearnaws-nextweb-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --attach-policy-arn=arn:aws:iam::976591345876:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --region=ap-northeast-1 \
  --approve

# 确认 EKS 集群中的 IAM 服务账号
eksctl get iamserviceaccount --cluster=deeplearnaws-nextweb-cluster --region=ap-northeast-1

# 确认 EKS 服务账号
# kubectl get serviceaccounts -n kube-system
kubectl get serviceaccounts aws-load-balancer-controller -n kube-system
kubectl describe serviceaccounts aws-load-balancer-controller -n kube-system

# 注册 EKS 信息库 For ALB
helm repo add eks https://aws.github.io/eks-charts

# 更新 EKS 信息库
helm repo update

# 安装 ALB 控制器
#   Amazon 容器镜像注册表
#   -> https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/add-ons-images.html
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=deeplearnaws-nextweb-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-northeast-1 \
  --set vpcId=vpc-08bcb57b3d4471b56 \
  --set image.repository=602401143452.dkr.ecr.ap-northeast-1.amazonaws.com/amazon/aws-load-balancer-controller

# 确认部署
kubectl -n kube-system get all

# 确认 ALB 控制器部署
kubectl -n kube-system describe deployment aws-load-balancer-controller

# 确认 ALB 控制器服务
kubectl -n kube-system describe svc aws-load-balancer-webhook-service

## 验证部署是否成功并且控制器已启动
# kubectl logs -n kube-system $(kubectl get po -n kube-system | egrep -o 'aws-load-balancer-controller[a-zA-Z0-9-]+')
kubectl get pods -n kube-system | grep aws-load-balancer-controller
kubectl -n kube-system logs -f aws-load-balancer-controller-000000000-11111

# 卸载 ALB 控制器部署
helm uninstall aws-load-balancer-controller -n kube-system 

# 建立 IngressClass
nano 00-ingressclass-resource.yaml
...

# 建立 IngressClass 资源
kubectl apply -f 00-ingressclass-resource.yaml
kubectl delete -f 00-ingressclass-resource.yaml

# 校验 IngressClass 资源
kubectl get ingressclass

# 描述 deeplearnaws-ingress-class 资源
kubectl describe ingressclass deeplearnaws-ingress-class
```

*00-ingressclass-resource.yaml*

```yml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: deeplearnaws-ingress-class
  annotations:
    ingressclass.kubernetes.io/is-default-class: "true"
spec:
  controller: ingress.k8s.aws/alb
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
