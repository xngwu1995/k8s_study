准备几个 Docker 镜像
===================

## 知识点

* 准备几个 Docker 镜像

## 官网

https://kubernetes.io/zh/docs/concepts/containers/

+ Docker(推荐)
+ containerd
+ CRI-O

## 实战演习/说明讲解

>看图说话

+ 提前准备几个测试用的 Docker 镜像
+ 测试 Docker 镜像的动作

## 操作步骤

### 提前准备几个测试用的 Docker 镜像

https://hub.docker.com/u/komavideo

>komavideo/deeplearnaws-nextweb

+ v1~v7

### 测试 Docker 镜像的动作

```bash
$ sudo docker pull komavideo/deeplearnaws-nextweb:v1
$ sudo docker image ls
$ sudo docker run --rm --name deeplearnaws-web -p 80:3000 komavideo/deeplearnaws-nextweb:v1
>http://ec2-ip

# 下载其他的镜像
$ sudo docker pull komavideo/deeplearnaws-nextweb:v2
$ sudo docker pull komavideo/deeplearnaws-nextweb:v3
$ sudo docker pull komavideo/deeplearnaws-nextweb:v4
$ sudo docker pull komavideo/deeplearnaws-nextweb:v5
$ sudo docker pull komavideo/deeplearnaws-nextweb:v6
$ sudo docker pull komavideo/deeplearnaws-nextweb:v7
```

Done.

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
