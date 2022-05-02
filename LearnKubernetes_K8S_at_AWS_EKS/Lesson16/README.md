安装 Amazon EBS CSI 驱动程序
===========================

## 知识点

* 安装 Amazon EBS CSI 驱动程序

## 官网

https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/ebs-csi.html

## 实战演习/说明讲解

>看图说话

+ 建立集群 IAM 角色
+ 赋予集群 IAM 角色
+ 安装 Amazon EBS CSI 驱动程序
+ 验证 Amazon EBS CSI 驱动程序

## 操作步骤

```bash
$ cd /tmp
# 建立集群 IAM 角色
$ curl -o eks-iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-ebs-csi-driver/release-1.3/docs/example-iam-policy.json
$ aws iam create-policy \
    --policy-name AmazonEKS_EBS_CSI_Driver_Policy \
    --policy-document file://eks-iam-policy.json
# 赋予集群 IAM 角色
$ eksctl create iamserviceaccount \
    --name ebs-csi-controller-sa \
    --region=ap-northeast-1 \
    --namespace kube-system \
    --cluster deeplearnaws-nextweb-cluster \
    --attach-policy-arn arn:aws:iam::976591345876:policy/AmazonEKS_EBS_CSI_Driver_Policy \
    --approve \
    --override-existing-serviceaccounts
# 验证角色 ARN
$ aws cloudformation describe-stacks \
    --stack-name eksctl-deeplearnaws-nextweb-cluster-addon-iamserviceaccount-kube-system-ebs-csi-controller-sa \
    --query='Stacks[].Outputs[?OutputKey==`Role1`].OutputValue' \
    --output text
# 安装 Amazon EBS CSI 驱动程序
$ helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver
$ helm repo update
# 从镜像区域安装驱动
#   https://docs.aws.amazon.com/zh_cn/eks/latest/userguide/add-ons-images.html
$ helm upgrade -install aws-ebs-csi-driver aws-ebs-csi-driver/aws-ebs-csi-driver \
    --namespace kube-system \
    --set image.repository=602401143452.dkr.ecr.ap-northeast-1.amazonaws.com/eks/aws-ebs-csi-driver \
    --set controller.serviceAccount.create=false \
    --set controller.serviceAccount.name=ebs-csi-controller-sa
# To verify that aws-ebs-csi-driver has started, run:
$ kubectl get pod -n kube-system -l "app.kubernetes.io/name=aws-ebs-csi-driver,app.kubernetes.io/instance=aws-ebs-csi-driver"
# 验证 Amazon EBS CSI 驱动程序
$ kubectl get storageclass
# Verify driver is running:
$ kubectl get pods -n kube-system
# To deploy the CSI driver:(另一个部署确认例子)
# $ sudo yum install -y git
# $ kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=release-1.5"

#####################################################################
# 下面是 EKS 官方网站测试用例，相对比较花时间，割爱！！！
$ sudo yum install -y git
$ git clone https://github.com/kubernetes-sigs/aws-ebs-csi-driver.git
$ cd aws-ebs-csi-driver/examples/kubernetes/dynamic-provisioning/
# 从 ebs-sc 目录部署 ebs-claim 存储类、app 持久性卷声明和 specs 示例应用程序
$ kubectl apply -f specs/
# 描述 ebs-sc 存储类
$ kubectl describe storageclass ebs-sc
# 查看默认命名空间中的 Pod 并等待 app Pod 的状态变为 Running
$ kubectl get pods --watch
$ kubectl get pods -n kube-system --watch
# 列出默认命名空间中的持久性卷。查找具有 default/ebs-claim 声明的持久性卷。
$ kubectl get pv
# 描述持久性卷
$ kubectl describe pv pvc-1234567890
# 验证 Pod 是否成功将数据写入卷
$ kubectl exec -it app -- cat /data/out.txt
# 完成试验时，请删除此示例应用程序的资源以进行清理。
$ kubectl delete -f specs/
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
