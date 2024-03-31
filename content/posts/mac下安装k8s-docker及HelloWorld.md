---
share: "true"
title: mac下安装k8s&docker及HelloWorld
date: 2020-04-18 17:05:04
tags:
  - k8s
  - docker
  - springboot
---

# mac下安装k8s&docker及HelloWorld

安装环境：macOS

## 安装docker

下载地址：https://hub.docker.com/editions/community/docker-ce-desktop-mac

无脑点击下一步即可

## 启用k8s

打开docker---preference---kurbernets，勾上Enable Kurbernets，注意，勾选上之后需要下载文件，这个过程比较耗时，需要耐心等待

<!--more-->

## 安装k8s Dashboard及生成token登录

1. 执行如下命令安装Dashboard

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta4/aio/deploy/recommended.yaml

####启用代理
kubectl proxy

####访问地址
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login
```

2. 访问dashboard地址，访问页面如下

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdtozml4wdj30sn0btq42.jpg)

3. 生成token

```
kubectl create serviceaccount dashboard-admin -n kube-system
kubectl create clusterrolebinding dashboard-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
kubectl describe secret -n kube-system dashboard-admin-token
```

4. 使用token进行登录，登录后页面

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdy1d8hujbj31gh0inq5v.jpg)

## 使用k8s部署第一个应用-nginx

1. 点开右上角的 ‘+’ 按钮
2. create from input里填入

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  # 指定 label，便于检索
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    # 指定镜像
    image: nginx:alpine
    # 指定暴露端口
    ports:
    - containerPort: 80
```

3. 部署完成后

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdy1gb14zij31gl0i9jtj.jpg)

4. 可以通过dashboard或者kubectl命令进入容器内部

```
kubectl exec -it nginx sh
```

或者

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdy1izucnsj31hb0j7aci.jpg)

综上我们已经成功通过k8s部署了第一个应用。

下面我们会通过另外两篇文章来说明如何将springboot的jar包打包成docker镜像，同时推送到镜像仓库中，并且通过k8s从镜像仓库拉取并且部署。
