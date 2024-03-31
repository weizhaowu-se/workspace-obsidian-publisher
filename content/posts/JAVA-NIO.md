---
share: "true"
title: JAVA NIO
date: 2017-08-23 20:31:21
tags:
  - java
---

* 参考资料:https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html


# 主要概念:
* 通道(channel)
* 缓冲区(buffer)

# 主要思想:
* 在原来的IO的思想上封装,提高IO效率

# 方法:
* 通过缓冲区实现基于块的读写

# 具体:
>先获得文件的输入输出流-->从文件的输入输出流获得通道(channel)
-->分配缓冲区(buffer)-->从缓冲区中读取内容/将内容写入缓冲


# 缓冲区内部细节:
缓冲区的底层实现可以看成是一个**字节数组**,
### 三个变量
* position:当前索引,也可以理解为指针所指的元素,初始值为0,指向第一个元素
* limit:初始值为capacity,通过与position以clear函数和flip函数结合来确定写入写出的元素.
* capacity:总容量,可以理解为数组长度,limit<=capacity

---
* flip函数
1.将limit设置为position的值
2.将position设置为0
将buffer写入到输出通道时,调用此函数,注意在写入到输出通道时(foutchanel.write(buffer)),
position会步进,而limit不变,所以想要循环读取时,需要调用clear函数重置缓冲区的状态.


* clear函数
1.将limit设置为capacity的值
2.将position设置为0

* 文件输入输出流会记住输入输出的位置,每个字节仅能被读取一次.

