---
share: "true"
title: JAVA中的引用
date: 2016-10-07 20:09:10
tags:
  - java
---
<font style="font-family:微软雅黑">
### 强引用
* 最普遍的情况
* 宁愿抛出OOM也不会将其回收
* 可以通过将引用设置为null来弱化引用，便于gc回收对象
### 软引用 SoftReference
* 内存空间足够时不会回收，如果内存空间不足了，就会将其回收。

		String str=new String("abc");                                     // 强引用  
		SoftReference<String> softRef=new SoftReference<String>(str);  

### 弱引用WeakReference
* 比软引用更加弱，当垃圾收集器回收时就会被回收。
### 虚引用

