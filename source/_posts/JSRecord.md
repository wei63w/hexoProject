---
title: JSRecord
date: 2017-06-02 10:47:45
tags:
---


>##### 类型

JavaScript分为：
- 基本类型： Undefined,Null,Bollean,Numer,String, 
    值保存在栈，通过值类型传递，内存中占有固定大小空间,值类型保持独立
- 引用类型：Object
    在堆中分配控件，大小不固定，不保存在栈中，读取时从栈中读取内存地址找到堆中的值，为引用传递
- 基本包装类型：Boolean,Number,String
    基本包装类型看似是值类型，但是有内置的方法存在
    例如：var a = 'xxxsxxx'; a.substring(3);
- 内置对象：
    浏览器本身具有的对象为内置对象，相当于window一部分对象方法
    例如：encodeURI();,eval();Math，等



>#### 对象 Object

#### 创建对象
##### 第一种
```
		var testObj = new Object();
		testObj.name = 'xxx';
		testObj.six = 1;
		console.log(testObj.name);//xxx
		console.log(testObj.six);// 1
```
##### 第二种-- new 可以省略
```
        var testObj = Object();
		testObj.name = 'xxx';
		testObj.six = 1;
		console.log(testObj.name);//xxx
		console.log(testObj.six);// 1
```
##### 第三种 -- 字面量创建
```
		var testObj = {
			name : 'xxx',
			six : 1
		}
		console.log(testObj['name']);//xxx
		console.log( typeof testObj);// Object
		console.log(testObj.name); //xxx
		console.log(testObj.six); // 1
```
##### 删除属性
```
		var testObj = {
			name : 'xxx',
			six : 1
		}
		delete testObj.name;
		console.log(testObj['name']);// undefined
```
> ### 构造函数

```
	function abc(a,b){
	    this.a = a;
	    this.b = b;
	    this.say = function(parameters){
	        return parameters +'hehe';
	    };
	}
	var test = new abc(1,2);
	console.log(test.say('xxx'));//xxxhehe


```
>### 原型 prototype
创建的每一个函数都有prototype属性，包含所有实力共享的属性和方法，
prototype通过调用构造函数而创建对象的原型对象,
##### 与实例属性和方法区别
-   原型属性方法 地址共享，相当于 所有属性方法内存只存在一份
-   实例属性方法 地址不一样 不能共享，相当于每次new的实例是 新开辟了一份空间 来复制了一份属性方法来存储
-   当实例方法与原型方法同时具有时，先查找实例方法中后查找原型

```	
    function abc(){
	    abc.prototype.a = 123;
	    abc.prototype.b = 456;
	    abc.prototype.say = function(){
	        return this.a +'hehe';
	    };
	}
	abc.prototype.smail = function(){
		return this.b;
	}
	var test = new abc();
	console.log(test.say());//123hehe
	console.log(test.smail());//456
	//可以用数组方式添加原型属性,方法
    abc.prototype = {
		contructor:abc,//返回对创建此对象的数组函数的引用,可有可无不影响创建
		aa:123,
		bb:456
	}
	
	//this 虽然代表当前对象，但是不能直接用this复制prototype
	function abc(){//这样是错的
		this.prototype.aa = 123;
		this.prototype.bb = 345;
	}

```
-   原型属性只需要初始化一次后就可使用

```
    function abc(){
		this.cc = 111;
		if(typeof this.aa == 'undefined'){
			console.log('初始化开始');//值初始化一次
			abc.prototype.aa = 123;
			abc.prototype.bb = 345;	
		}
	}
	var test = new abc();
	var test2 = new abc();
	console.log(test.aa);
	console.log(test.bb);
```


> ### Javascript继承

-   通过原型链来实现继承
```
    function A(x){
		this.x = x;
	}
	A.prototype.a = 'a';
	
	function B(y){
		this.y = y;
	}
	//把构造函数中的原型信息赋值给父类
	B.prototype = new A();
	
	var obj = new B(1);
	//obj.a   -->  a
```

> ### constructor
> ##### constructor 属性返回对创建此对象的数组函数的引用

```
    var a,b;
	(function(){
		function A(a1,a2){
			this.a1 = 1;
			this.a2 = 2;
		}
		A.prototype.show = function(){
			console.log(this.a1);
		}
		a = new A();
		b = new A();
	})()
	
	a.show();// 1
	b.show();// 1
	
	//给闭包中的A 增加新方法
	a.constructor.prototype.showTwo = function(){
		console.log(this.a2);
	}
	a.showTwo();// 2
	b.showTwo();// 2
```

> ### ______proto______原型链

  每个对象都会在其内部初始化一个属性，就是______proto______，当我们访问一个对象的属性 时，如果这个对象内部不存在这个属性，那么他就会去______proto______里找这个属性，这个______proto______ 又会有自己的____proto____，于是就这样 一直找下去。
#####原型链可以用于继承

```
      var a = function(){
	  	this.aa = 'aa'
	  }
	  var b = function(){
	  	this.bb = 'bb';
	  	this.__proto__ = new a();
	  }
	  var c = function(){
	  	this.cc = 'cc';
	  	this.__proto__ = new b();
	  }
	  var result = new c();
	  //result.cc -->cc
	  //result.bb __>bb
	  //result.aa __>aa
```




> ### constructor 构造函数
  在对象创建或者实例化时候被调用的方法。通常使用该方法来初始化数据成员和所需资源。构造器Constructor在js不能被继承，因此不能重写，但可以被重载.
  
  除了创建对象,constructor还为创建的对象设置了原型对象prototype,原型对象存放在 Function.prototype中,
###### 例如
```
      //构造函数A 被创建新对象的时候都会带上a
	  //A.prototype 存放了该对象的原型引用 可用于定义继承,共享属性方法
	  function A(a){
	      this.a = a;
	  }
	  //继承属性 x
	  A.prototype.x = 666;
	  //继承方法 show -->
	  //当该方法满足时才可进行自定义:typeof A.prototype.show !== 'function'
	  A.prototype.show = function(parameter){}
	  var tempa = new A(121);
	  //调用继承方法
	  tempa.show('xxx');
```

>##### JS 上下文Context与作用域

###### 每个函数都有自己的上下文，每个执行上下文中都有自己的变量Object，用于存储上下文中的变量、函数等，并产生作用域。

对于全局变量而言，在局部作用域中可以更改其值，使得全局变量更加慎用
```
		var a = 11;
		ff();
		console.log(a);//66
		function ff(){
			a = 66;
		}
```
> ### Javascript-->call(),apply()

-   call()
    
调用一个对象的一个方法,以另一个对象替换当前对象
可以将一个函数的对象上下文从初始的上下文改变为指定的新对象

1. call 可以继承对象属性但不能实现继承原型属性
```
function a(num){
		a.prototype.axx = 'axx';
		this.shit = 'shit';
	}
	function b(num){
		b.prototype.xx = 'bxx';
		a.call(this,num);
	}
	var bb = new b(1);
	console.log(bb.axx);//undefined
	console.log(bb.shit);//shit
```

2. 使用其他对象的属性绑定到当前环境下,变成自己的属性

```
function a(a1,a2){
		this.a1 = a1;
		this.a2 = a2;
	}
	function b(b1,b2,b3){
		a.call(this,b1,b2);
		this.b3 = b3;
	}
	var test = new b(1,2,3);
	console.log(test.a1);//1
	console.log(test.a2);//2
	console.log(test.b3);//3
```


-   apply()

    劫持另外一个对象的方法,继承另外一个对象的属性
```
function a(a1,a2){
		this.a1 = a1;
		this.a2 = a2;
	}
	function b(b1,b2,b3){
		a.apply(this,arguments);
		this.b3 = b3;
	}
	var test = new b(1,2,3);
	console.log(test.a1);//1
	console.log(test.a2);//2
	console.log(test.b3);//3
```

>### 自我执行匿名函数

```
    (function (){
		console.log('xxx');
	})();
	//xxx
```


>### 闭包

- 闭包可以把局部变量驻留在内存中,可以避免污染全局变量

```

```
>### 冒泡

```

```