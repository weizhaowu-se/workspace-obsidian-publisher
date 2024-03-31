---
share: "true"
title: Java正则表达式SomeTips
date: 2016-10-16 20:29:12
tags:
  - java
---
* 默认的写法均为贪婪的
* 加?之后修改为最短匹配

		Pattern pattern = Pattern.compile("\\d{1,3}");
        String s = "123-456-789";
        Matcher matcher = pattern.matcher(s);
<!--more-->
* Matcher类
	* matches() 方法返回boolean，判断整个字符串是否符合pattern。
	* find() 方法返回boolean， **判断字符串中是否存在子串符合pattern**,通过group()方法获得匹配的子串。
	
			while (matcher.find()) {
	            System.out.println(matcher.group());
	        }

