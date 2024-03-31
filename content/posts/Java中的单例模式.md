---
share: "true"
title: Java中的单例模式
date: 2016-10-26 13:35:40
tags:
  - java
  - 设计模式
---
# Java中的单例模式
* 多线程中可能会导致对象的多次初始化.
## 懒汉式的实现
* 之所以成为懒汉,个人认为应该是类的初始化只在需要的时候进行,所以很"懒".
<!--more-->
#### 线程不安全


	/*懒汉 非线程安全*/
	public class SingleTon_01 {
	    private static SingleTon_01 singleTon_01 = null;
	    private SingleTon_01() {
	
	    }
	    public static SingleTon_01 getSingleTon_01() {
	        if (singleTon_01 == null) {
	            singleTon_01 = new SingleTon_01();
	        }
	        return singleTon_01;
	    }
	    public void print() {
	        System.out.println("singleton_01");
	    }
	}
#### 线程安全


	/*懒汉 线程安全*/
	public class SingleTon_02 {
	    private static SingleTon_02 singleTon_02 = null;
	    private SingleTon_02() {}
	    private static synchronized SingleTon_02 getSingleTon_02() {
	        if (singleTon_02 == null) {
	            singleTon_02 = new SingleTon_02();
	        }
	        return singleTon_02;
	    }
	}
## 饿汉实现
* 在类加载时就进行初始化,可能会产生垃圾对象,但是不会有多线程的问题出现.


		/*饿汉式*/ 
		public class SingleTon_03 {
		    private static SingleTon_03 singleTon_03 = new SingleTon_03();
		    private SingleTon_03() {
		        
		    }
		    public static SingleTon_03 getSingleTon_03() {
		        return singleTon_03;
		    }
		}

## 双重校验锁模式
* 要注意变量需要添加volatile关键字.
	* 假设有两个进程分别调用了getInstance方法,进程A首先将变量复制到CPU0缓存当中,进行初始化操作,如果没有volatile关键字,那么进程B在调用此方法时复制到CPU1缓存中的可能是未被更新的null,此时问题就出现了.


			/*双重校验锁模式*/
			public class SingleTon_04 {
			    private static volatile SingleTon_04 singleTon_04 = null;
			    private SingleTon_04() {
			
			    }
			    public static SingleTon_04 getSingleTon_04() {
			        if (singleTon_04 == null) {
			            synchronized (SingleTon_04.class) {
			                singleTon_04 = new SingleTon_04();
			            }
			        }
			        return singleTon_04;
			    }
			}
## 静态内部类实现
* 通过classloader机制确保初始化时只有一个线程


		/*静态内部类*/
		public class SingleTon_05 {
		    private static class InstanceHolder{
		        private static SingleTon_05 singleTon_05 = new SingleTon_05();
		    }
		    private SingleTon_05() {
		        
		    }
		    public SingleTon_05 getSingleTon_05() {
		        return InstanceHolder.singleTon_05;
		    }
		}
## enum实现


	public enum SingleTon_06 {
	    INSTANCE;
	    public void print() {
	        System.out.println("singleTon_06");
	    }
	}
