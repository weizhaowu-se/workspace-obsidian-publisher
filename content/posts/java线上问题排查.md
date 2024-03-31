---
share: "true"
title: java线上问题排查
date: 2020-05-26 21:46:52
tags: 
---

# java线上问题排查

系统线上运行时，跑着跑着总是可能会发生一些意料之外的事情，这里我们大致可以分为两类为问题，一类是CPU的问题，一类是内存的问题；而其中内存又可以分为内存泄漏与频繁GC，以下就从这几个方面展开探讨。

<!--more-->

## CPU占用率高

* 前置：此处我们模拟一个会导致CPU飚高的场景：死循环

```
@RestController
public class TestController {
	@RequestMapping(value = "/cpu", method = RequestMethod.GET)
	public void test() {
		while (true) {

		}
	}
}
```

* 请求接口几次之外，查看本机的CPU占用率，执行`top`命令可以看到

```
 PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
15165 wilbert   20   0 3481140 236596  13196 S 197.7  6.3   0:56.27 java 
```

### 分析

从上面我们可以看到进程的ID为15165，我们都知道java是单进程多线程，接下来我们看下PID为1312进程里面线程的CPU占用情况，执行命令`top -Hp 15165`

```
  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                                                                               
15283 wilbert   20   0 3481140 236596  13196 R 86.7  6.3   1:04.36 http-nio-8080-e                                                                                                      
15284 wilbert   20   0 3481140 236596  13196 R 60.0  6.3   0:38.89 http-nio-8080-e                                                                                                      
15287 wilbert   20   0 3481140 236596  13196 R 53.3  6.3   0:17.27 http-nio-8080-e
```

这个时候我们可以看下具体线程的CPU占用率，我们此处以查看PID为15283的线程为例，将其转为十六进制，执行命令`printf %x 15283`，得到十六进制为

```
3bb3
```

接下来通过jstack命令查看指定线程的堆栈信息`jstack 15165 |grep -A 200 3bb3`，可以看到如下内容

```
"http-nio-8080-exec-1" #115 daemon prio=5 os_prio=0 tid=0x00007fbb5877f800 nid=0x3bb3 runnable [0x00007fbb0f9fa000]
   java.lang.Thread.State: RUNNABLE
        at com.wilbert.java.check.demo.controller.TestController.test(TestController.java:15)
```

看到了内存的堆栈信息之后，我们就可以查看具体的代码`TestController.java:15`，然后再根据代码就可以分析出具体的原因了。

## 内存问题

### OOM

* 工具 jmap导出vm信息，visualvm分析vm信息
* 前置：我们先准备一个导致OOM的场景（通过不断在堆上创建对象实现）

```
private static  class TestOOM {
		private String string = "";
	}

	public static void main(String[] args) {
		List<TestOOM> list = new ArrayList<TestOOM>();
		//在堆中无限创建对象
		while (true) {
			list.add(new TestOOM());
		}
	}
```

修改程序启动参数 

```
-Xms20m -Xmx20m 
-XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=/Users/wilbert/Documents/project/java-check/my.dump
```

Xms 和Xmx分别代表初始和最大分配内存，后面两个代表在发生OOM的时候将内存信息dump到指定到文件上。main方法执行后，如愿以偿的看到

```
java.lang.OutOfMemoryError: Java heap space
Dumping heap to /Users/wilbert/Documents/project/java-check/my.dump ...
Heap dump file created [34385753 bytes in 0.189 secs]
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at java.util.Arrays.copyOf(Arrays.java:3210)
	at java.util.Arrays.copyOf(Arrays.java:3181)
	at java.util.ArrayList.grow(ArrayList.java:265)
	at java.util.ArrayList.ensureExplicitCapacity(ArrayList.java:239)
	at java.util.ArrayList.ensureCapacityInternal(ArrayList.java:231)
	at java.util.ArrayList.add(ArrayList.java:462)
	at com.wilbert.java.check.demo.controller.TestController.main(TestController.java:41)
```

接下来我们借助visualvm对my.dump文件进行分析

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf68oa983nj31ys0m6dnf.jpg)

通过这个，我们可以很简单的看出是哪些对象占用了最多的堆内存，为我们的问题排查提供方向。

* 一些内存泄漏导致无法GC的变量我们也可以通过对dump文件进行分析查看到。

### 频繁GC

* 工具：jstack
* 频繁GC与前面CPU占用率高和OOM这几者其实是互为因果关系的

#### 查看GC情况

执行命令` jstat  -gc 15165 1000`，其中15165是进程号，1000是每隔1s打印一次

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf68zmzjtoj31gc06sq58.jpg)

当出现频繁的FGC的时候就需要关注了，可能是你的代码中存在内存泄漏，可以结合上面OOM问题中的jmap和visualvm对内存进行分析。
