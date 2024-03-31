---
share: "true"
title: JavaScript中的面向对象程序设计
date: 2017-08-28 22:39:49
tags:
  - JavaScript
  - 面向对象
---

# JavaScript中的面向对象程序设计
## 1.最简单的模式
### 1.1 新建实例添加属性和方法
		var person = new Object();
		person.name = "name";
		person.sayHi = function() {
		    console.log("hi");
		}
		
		console.log(person.name);
		person.sayHi();
<!--more-->
### 1.2 对象字面量
* 与上面的方法其实是相同的,仅仅是书写上简化了一些而已.

		var person = {
		    name : "name",
		    sayHi : function() {
		        console.log("hi");
		    }
		}

---
如果你要构造多个对象的话,那么以上的模式的问题就显而易见了:产生大量的重复代码,而函数就是解决重复代码的利器.
## 2.工厂模式
	function createPerson() {
	    var o = {
	        name : "name",
	        sayHi : function() {
	            console.log("hi");
	        }
	    }
	    return o;
	}
## 3. 构造函数模式
	function Person(name) {
	    this.name = name;
	    this.sayHi = function() {
	        console.log("hi");
	    }
	}
	person = new Person("name");
**使用构造函数模式新建对象时,经历了以下的四个步骤:**  
1. 创建一个新对象  
2. 将构造函数的作用域赋给新对象(this关键字指向创建的新对象)  
3. 执行构造函数中的代码  
4. 返回新对象  

---
* 关于this 的具体含义,可以参考[阮一峰的网络日志-Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)

---
现在我们解决了构造多个对象时的重复代码问题,但是其实还有另外一个问题:  
**每一个方法都需要在每个实例上重新创建一遍**,而原型模式可以解决这个问题.
## 4. 原型模式
### 4.1 原型
* 每个函数都有prototype(原型)属性,这个属性指向一个原型对象,而这个对象为所有实例共享(包括对象里面的属性以及方法).
* ![](http://7xkzud.com1.z0.glb.clouddn.com/17-8-28/39105696.jpg)
* 值得注意的是,在实例中的变量会屏蔽掉原型模式中的同名变量.
### 4.2 使用
	function Person(name) {
	    this.name = name;
	    this.sayHi = function() {
	        console.log("hi");
	    }
	}
	Person.prototype.sayName = function() {
    	console.log(this.name)
	};

或者我们也可以通过对象字面量的方式来实现:

	Person.prototype = {
	    constructor: Person,
	    a: "a"
	}
### 4.3 问题
随着共享随之而来的问题是:对共享变量的修改可能会影响到你的预期输出结果,所以使用时还请慎重.
## 5. 混合使用原型模式和构造函数模式
	function Person(name) {
	    this.name = name;
	    this.sayHi = function() {
	        console.log("hi");
	    }
	}
	Person.prototype = {
	    constructor: Person,
	    a: "a"
	}

---
---
## 继承
	function SuperType() {
	    this.property = true;
	}
	
	SuperType.prototype.getSuperValue = function() {
	    return this.property;
	}
	
	function SubType() {
	    this.subproperty = false;
	}
	
	SubType.prototype  = new SuperType();
	
	SubType.prototype.getSubValue = function() {
	    return this.subproperty;
	}
	
	var instance = new SubType();
	alert(instance.getSuperValue());

![](http://7xkzud.com1.z0.glb.clouddn.com/17-8-30/19766157.jpg)


