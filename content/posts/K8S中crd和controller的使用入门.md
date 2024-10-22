---
title: K8S中crd和controller的使用入门
share: "true"
date: 2024-10-18 20:21:03
---
# 定义自定义资源定义
crd.yaml
```
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: foos.samplecontroller.k8s.io
spec:
  group: samplecontroller.k8s.io
  versions:
    - name: v1alpha1
      served: true
      storage: true
      schema:
        # schema used for validation
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                deploymentName:
                  type: string
                replicas:
                  type: integer
                  minimum: 1
                  maximum: 10
            status:
              type: object
              properties:
                availableReplicas:
                  type: integer
      subresources:
        status: {}
  names:
    kind: Foo
    plural: foos
  scope: Namespaced
```

* 具体内容请从代码中的src/main/resources/crd.yaml 进行获取
* 关于name、group、plural命名有一定的要求，可以参考官方文档
* 执行 如下命令即为定义好了自定义资源，自定义资源类型为Foo，同时该自定义资源存在一些自定义的属性（spec和status）
```
# create a CustomResourceDefinition
kubectl create -f src/main/resources/crd.yaml
```
# 启动控制器
```
# Run Controller
mvn exec:java -Dexec.mainClass=io.fabric8.samplecontroller.SampleControllerMain
```
* 这里是我们在本地启动的一个Java应用，其实可以理解为一个监听程序，会监听到定义在类‘Foo’上的注解@Version、@Group、@Plural对应类型的自定义资源的增删改查
* ‘FooSpec’ 和 ‘FooStatus’ 类需要按照自定义资源类型中的spec和status进行定义，若字段不一致会导致监听时序列化失败（可以通过在这两个类上加 @JsonIgnoreProperties(ignoreUnknown = true)） 进行处理
# 创建自定义资源
```
apiVersion: samplecontroller.k8s.io/v1alpha1
kind: Foo
metadata:
  name: example-bar
spec:
  deploymentName: example-bar-deploy
  replicas: 1
```
* 尤其注意这里的kind不同于平时使用service等，是自定义资源Foo
* 可以使用 kubectl get Foo 进行自定义资源的查询，同pod、service相同的语法、restapi都可以使用
* 在第二步的控制器日志中就可以查看到创建自定义资源的相关信息，控制器中可以根据接收到的信息做进一步的处理
# 参考

* [GitHub - rohanKanojia/sample-controller-java: Java...](https://github.com/rohanKanojia/sample-controller-java)