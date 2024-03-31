---
share: "true"
title: docker搭建minio及永久有效链接配置
date: 2020-09-27 22:41:09
tags: 
---

### minio部署

1. 拉取镜像

```
docker pull minio/minio
```

2. 创建本地数据目录及配置文件目录

```
/home/minio-data/data
/home/minio-data/config
```

3. 启动minio

```
docker run -p 9000:9000 --name minio \
-d --restart=always \
-e "MINIO_ACCESS_KEY=aaaaa" \
-e "MINIO_SECRET_KEY=bbbbb" \
-v /home/minio-data/data:/data \
-v /home/minio-data/config:/root/.minio \
minio/minio server /data
```

<!--more-->

注意：

1. 此处我们将容器命名成了minio
2. 启动脚本中指定了ak信息
3. -v 的参数需要注意修改成第二步新建的目录
4. 此处我们将服务器9000端口映射到了docker容器的9000端口

---

访问页面 http://localhost:9000，输出启动时配置的ak信息即可访问minio的web页面，可以新建bucket或者上传文件，获得下载链接等

### 关于下载链接

​	在生成文件签名链接的时候，可以看到，最长时间长度为7天，但是有时候我们又想要获得一个永久有效的，可访问的链接，那么怎么办呢？

​	可以通过将某个bucket设置为public或者将某个bucket下的某个目录设置为public，那么我们就可以直接进行访问了，此处建议单独建一个bucket或者单独设置某个目录为public，避免出现安全问题

​	这个配置是通过mc （minio client）客户端实现的，安装方式见参考二，配置步骤大概如下

1.  添加云存储服务（此处添加了一个名为minio的云存储服务）

```
mc config host add minio http://localhost:9000 aaaaa bbbb
```

2. 添加成功之后，可以通过命令查看某个云存储服务下的文件/bucket等

```
mc ls minio    //展示minio服务下的文件
mc mb minio/test-bucket    //在minio里新建桶
更多命令可以查看   https://docs.min.io/cn/minio-client-complete-guide.html
```

3. 设置桶或者目录的访问权限为public

```
mc policy set public  minio/test-bucket/public      //将minio中，test-bucket下的public目录设置为公开可访问
mc policy set public  minio/test-bucket2  //将minio中，test-bucket2设置为公开可访问
```

4. 将其设置为公开可访问后，我们可以直接通过拼装地址的方式直接读取到该文件而不需要签名

* 带签名的链接：http://localhost:9000/test-bucket/%E5%B7%A5%E4%BD%9C%E7%B0%BF2.xlsx?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=2F20200927%2F%2Fs3%2Faws4_request&X-Amz-Date=20200927T145659Z&X-Amz-Expires=432000&X-Amz-SignedHeaders=host&X-Amz-Signature=522cca58c133891da6fea0bc73463394bf10aef29d1949c5191de3a16f271fb2

* 公开永久可访问的链接：http://localhost:9000/test-bucket/%E5%B7%A5%E4%BD%9C%E7%B0%BF2.xlsx

### 参考

1. [MinIO Docker 快速入门](https://docs.min.io/cn/minio-docker-quickstart-guide.html)
2. [MinIO客户端快速入门指南](https://docs.min.io/cn/minio-client-quickstart-guide.html)
