---
share: "true"
title: docker私有仓库及k8s部署本地镜像
date: 2020-04-19 16:26:10
tags:
  - docker
  - k8s
---

# docker私有仓库及k8s部署本地镜像

1. 执行命令启动私有仓库

```
docker run -p 5000:5000 registry:2.0
```

执行命令后，会从DockerHub上拉取registry镜像并在本地启动Registry服务，并监听5000端口。

<!--more-->

2. 将本地镜像推送到本地仓库，此处以本地仓库的镜像`springio/docker-domer:latest`为例

```
docker tag springio/docker-domer:latest localhost:5000/springio/docker-domer:latest
docker push localhost:5000/springio/docker-domer:latest
```

3. K8s部署

yaml文件如下

```
apiVersion: v1
kind: Pod
metadata:
  name: springboot-demo
  # 指定 label，便于检索
  labels:
    app: springboot-demo
spec:
  containers:
  - name: springboot-demo
    # 指定镜像
    image: localhost:5000/springio/docker-domer
```

4. 启动完成后进入容器查看

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdz5xuhs83j30k605kjrs.jpg)

证明已经启动成功

### 参考

* [[docker创建私有仓库](https://www.cnblogs.com/fengzheng/p/5168951.html)](https://www.cnblogs.com/fengzheng/p/5168951.html)


