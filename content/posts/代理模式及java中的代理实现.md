---
share: "true"
title: 代理模式及java中的代理实现
date: 2020-09-04 19:50:54
tags: 
---

## 什么是代理模式

```
Use of the proxy can simply be forwarding to the real object, or can provide additional logic.
```

​	简单来讲，火车票代售点就是一个代理模式的最好体现，我们通过代售点购买火车票，同时代售点会对我们的购买行为附加上额外的逻辑（比如说代购费）。

代理对象的作用有：

- 代理对象存在的价值主要用于拦截对真实业务对象的访问；
- 代理对象具有和目标对象(真实业务对象)实现共同的接口或继承于同一个类；
- 代理对象是对目标对象的增强，以便对消息进行预处理和后处理。

<!--more-->

## java中的代理

### 静态代理

​	代理类和目标对象实现同样的接口--->代理类持有目标对象的引用-->代理类通过调用目标对象对应的方法来实现代理调用--->代理类可以在代理调用前后插入自定义代码。

​	简单的例子如下

1. 接口类`HelloInterface.java`

```
public interface HelloInterface {
	void hello();
}
```

2. 目标对象`HelloInterfaceImpl`

```
public class HelloInterfaceImpl implements HelloInterface{
	@Override
	public void hello() {
		System.out.println("hello");
	}
}
```

3. 代理类`HelloProxy`

可以看到，此处我们在调用前后做了额外的输出

```
public class HelloProxy implements HelloInterface{
	private HelloInterface helloInterface;
	public HelloProxy(HelloInterface helloInterface) {
		this.helloInterface = helloInterface;
	}

	@Override
	public void hello() {
		System.out.println("before hello");
		helloInterface.hello();
		System.out.println("after hello");
	}
}
```

4. 通过代理类调用目标对象`TestMain`

```
public class TestMain {
	public static void main(String[] args) {
		HelloInterface helloInterface = new HelloInterfaceImpl();
		HelloProxy helloProxy = new HelloProxy(helloInterface);
		helloProxy.hello();
	}
}

```

可以看到，输出内容

```
before hello
hello
after hello
```

### 动态代理

调用处理类`MyInvocationHandler`，其中持有了目标对象的引用，在invoke方法中调用了目标对象的相应方法。

```
public class MyInvocationHandler implements InvocationHandler {
	private Object object;

	public MyInvocationHandler(Object object) {
		this.object = object;
	}
	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("before invoke");
		Object result = method.invoke(object, args);
		System.out.println("after invoke");
		return result;
	}
}
```

代理类调用目标测试`TestProxyMain`

* 通过Proxy.newProxyInstance获取代理类的实例，之后通过代理类调用目标对象的方法时，都会调用中介类的invoke方法，而此处我们可以添加自定义的逻辑

```
public class TestProxyMain {
	public static void main(String[] args) {
		MyInvocationHandler handler = new MyInvocationHandler(new HelloInterfaceImpl());

		HelloInterface helloInterface = (HelloInterface) Proxy.newProxyInstance(
				HelloInterface.class.getClassLoader(),
				new Class[]{HelloInterface.class},
				handler);

		helloInterface.hello();
	}
}
```

控制台输出内容

```
before invoke
hello
after invoke
```

### cglib代理

CGLIB(Code Generation Library)是一个基于[ASM](http://www.baeldung.com/java-asm)的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成

和java原生的动态代理相比较，cglib存在着以下不同之处

* JDK动态代理只能对实现了接口的类生成代理，而不能针对类
* CGLIB是针对类实现代理，主要是对指定的类生成一个**子类**，覆盖其中的方法

demo代码如下

```
public class CglibProxy implements MethodInterceptor {
	private Object target;

	//相当于JDK动态代理中的绑定
	public Object getInstance(Object target) {
		//给业务对象赋值
		this.target = target;
		//创建加强器，用来创建动态代理类
		Enhancer enhancer = new Enhancer();
		//为加强器指定要代理的业务类（即：为下面生成的代理类指定父类）
		enhancer.setSuperclass(this.target.getClass());
		//设置回调：对于代理类上所有方法的调用，都会调用CallBack，而Callback则需要实现intercept()方法进行拦
		enhancer.setCallback(this);
		// 创建动态代理类对象并返回
		return enhancer.create();
	}

	@Override
	public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
		System.out.println("before intercept");
		Object object = methodProxy.invokeSuper(o, objects);
		System.out.println("after intercept");
		return object;
	}
}
```

​	因为cglib是对指定的类生成一个子类，所以代码里面的 `setSuperclass` 和`invokeSuper`很好理解，同时由于是生成子类，那么像是final的方法就没有办法通过cglib的方式来实现动态代理了（因为子类不能覆盖父类的final方法）。

​	main函数调用如下

```
	public static void main(String[] args) {
		HelloInterfaceImpl helloInterface = new HelloInterfaceImpl();
		CglibProxy cglibProxy = new CglibProxy();
		HelloInterfaceImpl impl = (HelloInterfaceImpl) cglibProxy.getInstance(helloInterface);
		impl.hello();
	}
```

​	注意，这里的HelloInterfaceImpl 可以不需要是接口实现类，可以是普通类，执行输出为

```
before intercept
hello
after intercept
```

## spring中的动态代理

​	spring的重要特性AOP面向切面编程，其实底层就是通过动态代理来进行实现的，通过切面，增强某些方法的功能，在方法运行前后执行特定的方法与操作（例如说日志记录等）。

​	那么spring aop采用的是哪种方式进行动态代理的呢，有以下几个结论

* 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP
* 如果目标对象实现了接口，也可以强制使用CGLIB实现AOP
* 如果目标对象没有实现接口，必须采用CGLIB的动态代理，spring会自动在两种模式之间转换

---

​	但是，对于springboot来说，

* Spring 5.x 中 AOP 默认依旧使用 JDK 动态代理。
* SpringBoot 2.x 开始，为了解决使用 JDK 动态代理可能导致的类型转化异常而默认使用 CGLIB。
* 在 SpringBoot 2.x 中，如果需要默认使用 JDK 动态代理可以通过配置项`spring.aop.proxy-target-class=false`来进行修改，`proxyTargetClass`配置已无效。

## 参考

* [深入理解代理模式：静态代理与JDK动态代理](https://blog.csdn.net/justloveyou_/article/details/79407248)
* [Java三种代理模式：静态代理、动态代理和cglib代理](https://www.cnblogs.com/jxldjsn/p/10399607.html)
* [Java动态代理](https://juejin.im/post/6844903591501627405#heading-5)
* [动态代理](https://www.liaoxuefeng.com/wiki/1252599548343744/1264804593397984)
* [Spring的两种代理JDK和CGLIB的区别浅谈](https://blog.csdn.net/yamaxifeng_132/article/details/60868945?utm_medium=distribute.pc_relevant.none-task-blog-title-3&spm=1001.2101.3001.4242)
* [CGLIB动态代理demo实践](https://www.cnblogs.com/liyunfeng17/p/10896371.html)
* [惊人！Spring5 AOP 默认使用Cglib ？从现象到源码深度分析](https://juejin.im/post/6844903982721138696)

* [Spring AOP动态代理的实现方式](https://blog.csdn.net/weixin_38362455/article/details/91055939)


