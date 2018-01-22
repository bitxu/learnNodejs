---
title: 2018-1-19 函数
tags: 函数,nodejs,JavaScript
grammar_cjkRuby: true
---

## 函数
### 定义和调用
> 如果函数内部没有return语句，函数执行完毕后将返回undefined
**两种定义方式**

``` javascript
	function abs(x) {
		if (x >= 0) {
			return x;
		} else {
			return -x;
		}
	}
```
``` javascript
	var abs = function (x) {
		if (x >= 0) {
			return x;
		} else {
			return -x;
		}
	};
```
*注意：第二种方式结尾有`;`，表示赋值语句*
**调用**

`abs(10, 'blablabla'); // 返回10 `
`abs(); //返回NaN`
>javascript允许传入任意参数而不影响调用
**arguments**
>免费赠送的关键字arguments，它只在函数内部起作用，并且永远指向当前函数的调用者传入的所有参数。
>arguments类似Array但它不是一个Array

``` javascript
	function foo(x) {
		console.log('x = ' + x); // 10
		for (var i=0; i<arguments.length; i++) {
			console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
		}
	}
	foo(10, 20, 30);
	enter code here
```
**rest参数**
> 由于函数可以传入任意个参数，我们不得不依靠arguments来获取所有的参数

``` javascript
function foo(a, b) {
    var i, rest = [];
    if (arguments.length > 2) {
        for (i = 2; i<arguments.length; i++) {
            rest.push(arguments[i]);
        }
    }
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}
```
>为了获取除了已定义参数a、b之外的参数，我们不得不用arguments，并且循环要从索引2开始以便排除前两个参数，这种写法很别扭，只是为了获得额外的rest参数，有没有更好的方法？
>ES6标准引入了rest参数，上面的函数可以改写为：

``` javascript
	function foo(a, b, ...rest) {
		console.log('a = ' + a);
		console.log('b = ' + b);
		console.log(rest);
	}

	foo(1, 2, 3, 4, 5);
	// 结果:
	// a = 1
	// b = 2
	// Array [ 3, 4, 5 ]

	foo(1);
	// 结果:
	// a = 1
	// b = undefined
	// Array []
```
>rest参数只能写在最后，前面用...标识，从运行结果可知，传入的参数先绑定a、b，多余的参数以数组形式交给变量rest，
>所以，不再需要arguments我们就获取了全部参数





### 变量作用域和结构赋值
#### 变量作用域
- 在函数体内申明的变量，函数体外不可以引用
- 如果两个不同的函数各自申明了一个变量，该变量只在各自的函数体内起作用。
- JavaScript的函数可以嵌套，内部函数可以访问外部函数的变量，反过来则不行

``` javascript
	function foo() {
		var x = 1;
		function bar() {
			var x = 'A';
			console.log('x in bar() = ' + x); // 'A'
		}
		bar();
		console.log('x in foo() = ' + x); // 1
		bar();
	}
	foo();
	//运行后输出
	x in bar() = A
	x in foo() = 1
	x in bar() = A
```
#### 变量提升
JavaScript有个特点，它会先扫描整个函数体语句，把所有声明的变量提升到函数体顶部。
*注意：声明和赋值不是一回事，这里是声明提升，但是并没有赋值*

``` javascript
	'use strict';
	function foo() {
		var x = 'Hello, ' + y;
		console.log(x);
		var y = 'Bob';
	}
	foo();
```
>虽然是strict模式，但语句var x = 'Hello, ' + y;并不报错，原因是变量y在稍后申明了。
>但是console.log显示Hello, undefined，说明变量y的值为undefined。这正是因为JavaScript引擎自动提升了变量y的声明，但不会提升变量y的赋值
#### 全局作用域
*不在任何函数内定义的变量就具有全局作用域。实际上，JavaScript默认有一个全局对象window，全局作用域的变量实际上被绑定到window的一个属性*

``` javascript
'use strict';
var course = 'Learn JavaScript';
alert(course); // 'Learn JavaScript'
alert(window.course); // 'Learn JavaScript'
因此，直接访问全局变量course和访问window.course是完全一样的
```
顶层函数的定义也被视为一个全局变量，并绑定到window对象

``` javascript
	'use strict';
	function foo() {
		alert('foo');
	}

	foo(); // 直接调用foo()
	window.foo(); // 通过window.foo()调用
```
*`alert()`其实也是window的变量*
#### 名字空间
*问题：全局变量会绑定到window上，不同的JavaScript文件如果使用了相同的全局变量，或者定义了相同名字的顶层函数，都会造成命名冲突，并且很难被发现。*
*解决办法：减少冲突的一个方法是把自己的所有变量和函数全部绑定到一个全局变量中*

``` javascript
	// 唯一的全局变量MYAPP:
	var MYAPP = {};
	// 其他变量:
	MYAPP.name = 'myapp';
	MYAPP.version = 1.0;
	// 其他函数:
	MYAPP.foo = function () {
		return 'foo';
	};
```
*许多著名的JavaScript就是这样干的，例如jQuery*
#### 局部作用域
由于JavaScript的变量作用域实际上是函数内部，我们在for循环等语句块中是无法定义具有局部作用域的变量的

``` javascript
`use strict`;
funtion foo(){
	for(var i=0,i<100,i++){
		//
	}
	i+=100;   //仍然都是这样
```
*为了解决块级作用域，ES6引入了新的关键字let，用let替代var可以申明一个块级作用域的变量*

``` javascript
'use strict';
function foo() {
    var sum = 0;
    for (let i=0; i<100; i++) {
        sum += i;
    }
    // SyntaxError:
    i += 1;
}
```
*ES6标准引入了新的关键字const来定义常量，const与let都具有块级作用域*
#### 解构赋值
把数组的元素分别赋值给几个变量
>原来的做法
``` javascript
	var array=['hello','javascript','es6'];
	var x=array[0];
	var y=array[1];
	var z=array[2];
```
>利用ES6的的解构赋值

``` javascript
	`use strict`;
	var [x,y,z]=['hello','javascript','ES6'];
```
>数据嵌套时

``` javascript
	let [x,[y,z]]=['hello',['javascript','ES6'];
```
>解构赋值可以忽略某些元素

``` javascript
		let [, , z] = ['hello', 'JavaScript', 'ES6']; 
```
>从对象中取出若干属性，也可以使用解构赋值，快速便捷

``` javascript
	'use strict';
	var person = {
		name: '小明',
		age: 20,
		gender: 'male',
		passport: 'G-12345678',
		school: 'No.4 middle school'
	};
	var {name, age, passport} = person;
```
>对 `对象嵌套的对象`进行解构赋值

``` javascript
		var person = {
			name: '小明',
			age: 20,
			gender: 'male',
			passport: 'G-12345678',
			school: 'No.4 middle school',
			address: {
				city: 'Beijing',
				street: 'No.1 Road',
				zipcode: '100001'
			}
		};
		var {name, address: {city, zip}} = person;
		name; // '小明'
		city; // 'Beijing'
		zip; // undefined, 因为属性名是zipcode而不是zip
		// 注意: address不是变量，而是为了让city和zip获得嵌套的address对象的属性:
		address; // Uncaught ReferenceError: address is not defined
```
>解构赋值使用默认值，避免返回undefined

``` javascript
		var person = {
			name: '小明',
			age: 20,
			gender: 'male',
			passport: 'G-12345678'
		};

		// 如果person对象没有single属性，默认赋值为true:
		var {name, single=true} = person;
		name; // '小明'
		single; // true
```








