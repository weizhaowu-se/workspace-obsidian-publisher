---
share: "true"
title: ThreadLocal及在多线程中使用
date: 2020-05-26 23:07:30
tags: 
---

本文主要讨论ThreadLocal，InheritableThreadLocal以及Transmittablethreadlocal的使用和这三者之间的一些异同。

<!--more-->

## ThreadLocal

ThreadLocal，翻译为线程局部变量，支持泛型的get和set方法，当我们的代码是在同个线程里面执行的时候，那么通过ThreadLocal变量取得的是值传递的同一个对象，以下代码做一个简单的示例

```
public class ThreadLocalTest {
	private static ThreadLocal<String> threadLocal = new ThreadLocal<>();

	public static void main(String[] args) {
		ThreadLocalTest threadLocalTest = new ThreadLocalTest();
		threadLocal.set("testThreadLocal");
		System.out.println("step1:" + threadLocalTest.step1());
		System.out.println("step2:" + threadLocalTest.step2());
	}

	public String step1() {
		return threadLocal.get();
	}

	public String step2() {
		return threadLocal.get();
	}
}
```

执行后控制台输出

```
step1:testThreadLocal
step2:testThreadLocal
```

可能你会觉得说这种场景下和我使用一个普通的static的静态变量（比如说Set）不是一样的效果吗？非也非也，我们使用一个多线程的例子来看看

```
public class MultipleThreadLocalTest implements Runnable{
	private static ThreadLocal<String> threadLocal = new ThreadLocal<>();

	public static void main(String[] args) {
		MultipleThreadLocalTest threadLocalTest = new MultipleThreadLocalTest();
		threadLocal.set("testThreadLocal");
		System.out.println(Thread.currentThread().getName() + " step1:" + threadLocalTest.step1());
		System.out.println(Thread.currentThread().getName() + " step2:" + threadLocalTest.step2());

		Thread thread = new Thread(threadLocalTest);
		thread.start();
	}

	public String step1() {
		return threadLocal.get();
	}

	public String step2() {
		return threadLocal.get();
	}

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
	}
}
```

在上面这个例子里面，除了`step1`和`step2`之外，我们还创建了一个子线程，打印`threadLocal`的值，控制台输出结果为

```
main step1:testThreadLocal
main step2:testThreadLocal
Thread-0 run:null
```

可以看到，在主线程当中是能够打印出set的值得，但是子线程是无法获得的。我们可以和单纯的静态变量做一个对比：修改ThreadLocal变量为Set变量，同时修改相关的代码

```
main step1:[testThreadLocal]
main step2:[testThreadLocal]
Thread-0 run:[testThreadLocal]
```

可以看到这种场景下的static的变量是父子线程都能够读取到的，而这个其实会导致多线程同步的问题（父子线程同时对变量进行修改），可以通过加锁的方式来解决。

## InheritableThreadLocal

通过上面的ThreadLocal了解了使用的一些场景，那么当我们想要在创建子线程的时候能够将变量和子线程一起共享的时候，InheritableThreadLocal就应运而生了。

InheritableThreadLocal直接翻译可以理解为可继承的线程局部变量，接下来我们简单修改下ThreadLocal里面的示例代码

```
public class MultipleThreadLocalTest implements Runnable{
	private static InheritableThreadLocal<String> threadLocal = new InheritableThreadLocal<>();
	public static void main(String[] args) {
		MultipleThreadLocalTest threadLocalTest = new MultipleThreadLocalTest();
		threadLocal.set("testThreadLocal");
		Thread thread = new Thread(threadLocalTest);
		thread.start();
	}

	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
	}
}
```

运行代码后，打印输出

```
Thread-0 run:testThreadLocal
```

可以看到，子线程此时也已经拿到了父线程的线程变量，这就是`可继承`。

这种继承的过程是在初始化线程的时候完成的，其实可以简单理解为一个变量的复制，既然涉及到了变量的复制，那么就需要考虑下子线程对泛型变量的修改是否生效的问题，其实这个问题也相当简单，就和java的方法调用是值传递还是引用传递的问题的解答是一样的，上代码

```
public class InheritableThreadLocalTest implements Runnable{
	private static InheritableThreadLocal<List<String>> threadLocal = new InheritableThreadLocal<>();

	public static void main(String[] args) throws InterruptedException {
		InheritableThreadLocalTest threadLocalTest = new InheritableThreadLocalTest();
		List<String> list = new ArrayList<>();
		list.add("a");
		threadLocal.set(list);

		Thread thread = new Thread(threadLocalTest);
		thread.start();
		Thread.sleep(1000);
		System.out.println(threadLocal.get().toString());
	}



	@Override
	public void run() {
		System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
		threadLocal.get().add("b");
	}
}
```

这段代码我们做了什么事情呢，我们使用了InheritableThreadLocal，泛型为List，然后我们在子线程里执行了如下操作

```
		threadLocal.get().add("b");
```

在父线程进行sleep之后，打印InheritableThreadLocal，可以看到

```
Thread-0 run:[a]
[a, b]
```

证实了这种修改是生效的，但是，如果我们在子线程里面执行如下代码

```
		threadLocal.set(new ArrayList<>());
```

可以看到这个赋值对于父线程的线程变量是不起作用的，其实就和方法调用时的入参传递道理是一样的，这一点是需要格外注意的。

## Transmittablethreadlocal

既然上面有了可继承的变量，那么Transmittablethreadlocal又有什么作用呢？

其实上面提到了很重要的一点，InheritableThreadLocal的线程变量的继承是在创建线程的时候传递的，但是大部分时候我们都是通过线程池的方式来执行多线程的操作，这么一来如果采用InheritableThreadLocal不就乱套了吗，

```
public class MultiplePoolThreadLocalTest {
	private static InheritableThreadLocal<String> threadLocal = new InheritableThreadLocal<>();

	public static void main(String[] args) throws InterruptedException {
		threadLocal.set("testThreadLocal");
		ExecutorService fixedThreadPool = Executors.newFixedThreadPool(1);

		fixedThreadPool.execute(new Runnable() {
			@Override
			public void run() {
				System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
			}
		});
		Thread.sleep(1000);
		threadLocal.set("testThreadLocal2");
		fixedThreadPool.execute(new Runnable() {
			@Override
			public void run() {
				System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
			}
		});
	}
}
```

在这段代码里面，我们初始化了一个定长的线程池，size为1，然后先execute一次之后，更新InheritableThreadLocal的值，然后再execute，控制台输出为

```
pool-1-thread-1 run:testThreadLocal
pool-1-thread-1 run:testThreadLocal
```

我们可以看到，两次的打印的线程名是一样的，因为是线程池，所以重用了这个线程，而正因为是重用，所以并没有`创建线程`这个过程，所以第二次的打印的InheritableThreadLocal的值还是初始的值。

而Transmittablethreadlocal就是为了解决这个问题，核心思想是在执行的时候对线程局部变量进行继承而非创建线程的时候，我们对代码进行修改（需要先引入相关依赖）

```
public class MultiplePoolThreadLocalTest {
	private static TransmittableThreadLocal<String> threadLocal = new TransmittableThreadLocal<>();

	public static void main(String[] args) throws InterruptedException {
		threadLocal.set("testThreadLocal");
		ExecutorService fixedThreadPool = Executors.newFixedThreadPool(1);

		fixedThreadPool.execute(TtlRunnable.get(new Runnable() {
			@Override
			public void run() {
				System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
			}
		}));
		Thread.sleep(1000);
		threadLocal.set("testThreadLocal2");
		fixedThreadPool.execute(TtlRunnable.get(new Runnable() {
			@Override
			public void run() {
				System.out.println(Thread.currentThread().getName() + " run:" + threadLocal.get());
			}
		}));
	}
}
```

修改的内容主要有

* InheritableThreadLocal 修改为TransmittableThreadLocal

* 使用`TtlRunnable.get()`对Runnable做一层包装

执行后控制台输出

```
pool-1-thread-1 run:testThreadLocal
pool-1-thread-1 run:testThreadLocal2
```

两次的打印，线程名称是一样的，但是打印出来的变量值是不同的，符合我们的预期。

## 与Springboot中Async配合使用

在Springboot开发web接口时，我们经常会使用Async注解来实现异步多线程的操作，那么怎么样在这种场景下面和ThreadLocal配合使用呢？

和上一小节我们需要使用TtlRunnable对Runnable进行包装一样，在使用线程池时我们同样需要修改下线程池配置

```
@Configuration
public class AsyncThreadPoolConfiguration implements AsyncConfigurer {
	@Override
	public Executor getAsyncExecutor() {
		ExecutorService fixedThreadPool = Executors.newFixedThreadPool(2);

		// 重点：使用 TTL 提供的 TtlExecutors
		return TtlExecutors.getTtlExecutor(fixedThreadPool);
	}
}
```

我们使用一个简单的接口验证下

```
//TestController.java
	public static TransmittableThreadLocal threadLocal = new TransmittableThreadLocal();
	@Autowired
	TestService testService;
	@RequestMapping(value = "/async", method = RequestMethod.GET)
	public void testAsync(@PathParam("value") String value) throws InterruptedException {
		System.out.println("value is " + value);
		threadLocal.set("set in main " + value);
		testService.test();
	}
	
//TestService.java
@Service
public class TestService {

	@Async
	public void test() throws InterruptedException {
		System.out.println(Thread.currentThread().getName() + ":" + TestController.threadLocal.get());
		Thread.sleep(3000);
	}
}
```

我们分别调用如下三次接口

```
http://localhost:8080/async?value=1
http://localhost:8080/async?value=2
http://localhost:8080/async?value=3
```

可以看到打印内容为

```
value is 1
pool-1-thread-1:set in main 1
value is 2
pool-1-thread-2:set in main 2
value is 3
pool-1-thread-1:set in main 3
```

确认这个情况下是正常set和get的，但是如果我们将线程池配置里面最关键的一行去掉呢，如下

```
@Configuration
public class AsyncThreadPoolConfiguration implements AsyncConfigurer {
	@Override
	public Executor getAsyncExecutor() {
		ExecutorService fixedThreadPool = Executors.newFixedThreadPool(2);
		return fixedThreadPool;
		// 重点：使用 TTL 提供的 TtlExecutors
		//return TtlExecutors.getTtlExecutor(fixedThreadPool);
	}
}
```

同样的还是调用三次接口，可以看到控制台打印内容为

```
value is 1
pool-1-thread-1:set in main 1
value is 2
pool-1-thread-2:set in main 2
value is 3
pool-1-thread-1:set in main 1
```

可以发现，第三次请求的时候，由于线程池中线程的复用，并且没有对线程池中的线程做委托处理（TtlExecutors.getTtlExecutor），所以导致我们对ThreadLocal的赋值并没有生效。

## 参考

* [transmittable-thread-local-GitHub](https://github.com/alibaba/transmittable-thread-local/)
* [通过transmittable-thread-local源码理解线程池线程本地变量传递的原理](http://throwable.club/2020/05/02/ttl/)
* [Spring @Async异步调用（异步线程池）](https://blog.csdn.net/wudiyong22/article/details/80747084)

* [如何在子线程和线程池中使用 ThreadLocal 传输上下文](https://www.jianshu.com/p/4093add7f2cd)

* [TransmittableThreadLocal 解决 线程池线程复用 无法复制 InheritableThreadLocal 的问题.](https://www.cnblogs.com/sweetchildomine/p/8807059.html)

