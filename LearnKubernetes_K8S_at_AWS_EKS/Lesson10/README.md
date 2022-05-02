Service - 使用服务完成系统的集成
==============================

## 知识点

* 使用 Service 服务完成系统的集成

## 官网

https://kubernetes.io/zh/docs/concepts/services-networking/service/

## 实战演习/说明讲解

>看图说话

+ 部署后端 API 程序，内部发布并不暴露给外部
+ 通过前端 Web 程序连接后端 API 程序
+ 暴露前端 Web 程序给最终用户
+ 用户从前端访问 Web 应用程序，使用后端 API 的计算服务

## 操作步骤

```bash
# 部署后端 API 程序
# ->https://hub.docker.com/u/komavideo
$ kubectl create deployment nextapi-deployment --image=komavideo/deeplearnaws-nextapi:v1
$ kubectl get deployments
# 内部发布后端 API 程序
$ kubectl expose deployment nextapi-deployment --port=8080 --target-port=8080 --name=nextapi-service
$ kubectl get service

# 部署前端 Web 应用程序(nextweb-v7 版本开始支持读取后端 API)
$ kubectl create deployment nextweb-deployment --image=komavideo/deeplearnaws-nextweb:v7
$ kubectl get deployments
# 发布前端 Web 应用给最终用户
$ kubectl expose deployment nextweb-deployment --type=NodePort --port=3000 --target-port=3000 --name=nextweb-service
$ kubectl get service
# 确认默认名空间资源
$ kubectl get all
# 确认工作节点 IP
$ kubectl get pods -o wide
$ kubectl get nodes -o wide
# 通过工作节点的 外部IP 和 NodePort 访问应用程序(TCP:30000-32767)
$ curl http://13.113.207.77:30546
```

## 应用程序代码概要

### nextapi

*/pages/api/math/[...slug].js*

```js
export default function handler(req, res) {
    const { slug } = req.query
    res.status(200).json({ result: `${slug.join('-')}` })
}
```

### nextweb

*/pages/api/add.js*

```js
export default async function handler(req, res) {
    // 本地调试
    let api_server = "127.0.0.1";
    if (process.env.NODE_ENV == 'production') {
        // 产品环境
        api_server = "nextapi-service"
    }

    const api_response = await fetch(`http://${api_server}:8080/api/math/100/200/300`)
    const result = await api_response.json()

    res.status(200).json({ result })
}
```

## 小马部落

https://discord.gg/VSKw72P

## 课程文件

+ 小马部落Discord专区共享(三级会员)

## 小马视频频道

https://komavideo.com

## 深学AWS

https://deeplearnaws.com
