---
share: "true"
title: 关于java 中的equals的一些记录
date: 2018-10-21 21:39:15
tags:
  - java
---

### override equals方法的几个原则

1. 自反性。对于任何非null的引用值x，x.equals(x)应返回true。

2. 对称性。对于任何非null的引用值x与y，当且仅当：y.equals(x)返回true时，x.equals(y)才返回true。

3. 传递性。对于任何非null的引用值x、y与z，如果y.equals(x)返回true，y.equals(z)返回true，那么x.equals(z)也应返回true。

4. 一致性。对于任何非null的引用值x与y，假设对象上equals比较中的信息没有被修改，则多次调用x.equals(y)始终返回true或者始终返回false。

   <!--more-->

   ### 示例代码(String类的equals方法):

```
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

* list中的indexOf,contains方法根据equals方法来判断对象是否相等



### override hashCode方法

从Object类中的源码可以看到重载equals之后需要重载hashCode的提示

```
If two objects are equal according to the {@code equals(Object)}
*     method, then calling the {@code hashCode} method on each of
*     the two objects must produce the same integer result.
```

* String 类的hashCode代码:

```
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

* Map接口会使用,可以简单理解为通过hashCode方法算出该变量的地址,那么当两个变量相等,那么这个算出来的hashCode也势必需要相等.

### 编写equals的建议

下面给出编写一个完美的equals方法的建议（出自Java核心技术 第一卷：基础知识）：

1. 显式参数命名为otherObject,稍后需要将它转换成另一个叫做other的变量（参数名命名，强制转换请参考建议5）

2. 检测this与otherObject是否引用同一个对象 ：if(this == otherObject) return true;（存储地址相同，肯定是同个对象，直接返回true）

3. 检测otherObject是否为null ，如果为null,返回false.if(otherObject == null) return false;

4. 比较this与otherObject是否属于同一个类 （视需求而选择）

   * 如果equals的语义在每个子类中有所改变，就使用getClass检测 ：if(getClass()!=otherObject.getClass()) return false; (参考前面分析的第6点)

   * 如果所有的子类都拥有统一的语义，就使用instanceof检测 ：if(!(otherObject instanceof ClassName)) return false;

5. 将otherObject转换为相应的类类型变量：ClassName other = (ClassName) otherObject;

6. 现在开始对所有需要比较的域进行比较 。使用==比较基本类型域，使用equals比较对象域。如果所有的域都匹配，就返回true，否则就返回flase。

7. 如果在子类中重新定义equals，就要在其中包含调用super.equals(other)

8. 当此方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的常规协定，该协定声明 相等对象必须具有相等的哈希码 。

### 参考

[equals解读](https://blog.csdn.net/javazejian/article/details/51348320)


