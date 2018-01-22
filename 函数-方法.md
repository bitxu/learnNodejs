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









### 方法
#### 定义
*在一个对象中绑定的函数，就叫做这个对象的方法*

``` javascript
	var xiaoming={
		name:"xiaoming",
		birth:1992,
		age:function(){
			var y= new Date().getFullYear();
			return y-tis.birth;
		}
	}
	//注意调用方式
	xiaoming.age    //function xiaoming.age()
	xiaoming.age() //return 26
```
#### 关键字this

``` javascript
	function getAge(){
		var y=new Date().getFullyear();
		return 	y-this.birth;
	}
	var xiaoming={
		birth:1992,
		name:"xiaoming",
		age:getAge
	};
	xiaoming.age()   //26  正常结果
	getAge()          //NaN
```
*问题：*
>在函数内部调用this，这个this到底指向谁？
>1、以对象的方法调用，this指向调用的对象
>2、单独调用，this指向全局变量，也就是window
*下面这种写法也是不可以的*

``` javascript
	var fn = xiaoming.age; // 先拿到xiaoming的age函数
	fn(); // NaN
```
*解决：*
ECMA决定在strict模式下，让`函数`的this指向undefined，就会出现以下错误

``` javascript
	'use strict';

	var xiaoming = {
		name: '小明',
		birth: 1990,
		age: function () {
			var y = new Date().getFullYear();
			return y - this.birth;
		}
	};
	var fn=xiaoming.age;
	fn();  //Uncaught TypeError: Cannot read property 'birth' of undefined
```
但这个决定只是把错误暴露出来，并没有解决`this`的正确指向问题
*自行解决-重构方法*

``` javascript
'use strict';
var xiaoming = {
    name: '小明',
    birth: 1990,
  	age:function(){
		function getAgeFromBirth(){
			var y=new Date().getFullYear();
			return y-this.bitrh;
		}
		return getAgeFromBirth();
	}
};
xiaoming.age() // Uncaught TypeError: Cannot read property 'birth' of undefined
```
>原因：在函数内部定义的函数，this又指向undefined了。
*解决办法*

``` javascript
		'use strict';

		var xiaoming = {
			name: '小明',
			birth: 1990,
			age: function () {
				var that = this; // 在方法内部一开始就捕获this
				function getAgeFromBirth() {
					var y = new Date().getFullYear();
					return y - that.birth; // 用that而不是this
				}
				return getAgeFromBirth();
			}
		};

		xiaoming.age(); // 25
```
#### apply
**目的：指定函数的this指向哪个对象**
*要指定函数的this指向哪个对象，可以用函数本身的apply方法，它接收两个参数，第一个参数就是需要绑定的this变量，第二个参数是Array，表示函数本身的参数*
修复上面的方法

``` javascript
		function getAge(){
			var y=new Date().getFullYear();
			return y-this.birth;
		}
		var xiaoming={
			name:"xiaoming",
			birth:1992,
			age:getAge
		}
		xiaoming.age(); 	// 25
		getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
```
**apply() 和 call()**
>调用Math.max(3, 5, 4)，分别用apply()和call()实现如下

``` javascript
Math.max.apply(null, [3, 5, 4]); // 5
Math.max.call(null, 3, 5, 4); // 5
```





#### 装饰器
利用`apply()`,我们可以动态改变函数的行为，JavaScript的所有对象都是动态的，即使是内置的函数，我们也可以重新指向新的函数
*假设我们想统计一下一段代码一共执行了多少次`parseInt()`，我们可以把所有的调用都找出来，然后手动加上`count +=1`,但是这样太傻了*
*最佳的方案是用自己的`parseInt()`代替默认的*

``` javascript
	'use strict';
	var count=0;
	var oldParseInt = parseInt; //保留原函数
	window.parseInt = function(){
		count +=1;
		return oldParseInt.apply(null,arguments); //调用原函数
	}
	//测试
	parseInt('10');
	parseInt('20');
	parseInt('30');
	console.log(count);  // 3 
```



### 高阶函数
#### 背景
JavaScript的函数其实都指向一个变量。现在既然变量可以指向函数，函数的参数又可以是变量，那么函数就可以接受另外一个函数作为参数，这就是高阶函数

``` javascript
	function add(x,y,f){
		return f(x)+f(y);
	}
```
当我们调用add(-5,6,Math.abs),`Math.abs(-5)+Math.abs(6)`  // 11

### 高阶函数-map 和reduce
**map**
现在我们有一个方法`f(x)=x^2`,现在要把这个函数作用在数组`[1,2,3,4,5,6,7,8,9]`,就可以用map实现

``` javascript
	`use strict`
	function pow(x){
		return x * x;
	}
	var arr=[1,2,3,4,5,6,7,8,9];
	var result=arr.map(pow);
	console.log(result)  //[1, 4, 9, 16, 25, 36, 49, 64, 81]
	console.log(result[2])   // 9
```
>把f(x)作用在Array的每一个元素并把结果生成一个新的Array
**reduce**
*Array的reduce()把一个函数作用在这个Array的[x1, x2, x3...]上，这个函数必须接收两个参数，reduce()把结果继续和序列的下一个元素做累积计算，其效果就是：*

``` javascript
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
```
>array 求和

``` javascript
	var arr=[1,3,5,7,9];
	arr.reduce(function(x,y){
		return x+y;
	});   //25
```
找错误
>利用map将数组字符串变成整数

``` javascript
	`use strict`
	var arr=['1','2','3'];
	var r;
	r=arr.map(parseInt)
	console.log(r)   // 	1,NaN,NaN
```
>原因分析
>由于map()接收的回调函数可以有3个参数：callback(currentValue, index, array)
>currentValue:当前执行的数组元素；
>index：currentValue 在数组中对应的索引
>array：就是当前的调用的数组
>通常我们仅需要第一个参数，而忽略了传入的后面两个参数。
>不幸的是，parseInt(string, radix)没有忽略第二个参数，导致实际执行的函数分别是：

``` lsl
parseInt('0', 0); // 0, 按十进制转换
parseInt('1', 1); // NaN, 没有一进制
parseInt('2', 2); // NaN, 按二进制转换不允许出现2
```
可以改为r = arr.map(Number);，因为Number(value)函数仅接收一个参数


### 高阶函数-filter
#### 用法
*把array中某些元素过滤掉，返回剩下的元素*
>和map的区别
>都是把传入的函数一次作用于每一个元素，但是filter是根据返回值是true还是false，来判断是否丢弃此元素
例如一个数组中，现在只保留奇数

``` javascript
	var arr = [1, 2, 4, 5, 6, 9, 10, 15];
	var r=arr.filter(function(x){
		return x%2!==0;
	});
```
filter()接收的回调函数，其实可以有多个参数。通常我们仅使用第一个参数，表示Array的某个元素。回调函数还可以接收另外两个参数，表示元素的位置和数组本身
>取出数组中的重复元素

``` javascript
	var arr = ['apple', 'strawberry', 'banana', 'pear', 'apple', 'orange', 'orange', 'strawberry'];
	var r =arr.filter(function(element,index,self){
		return self.indexOf(element)===index;
	});
	console.log(r.toString());   //apple,strawberry,banana,pear,orange
	//特别说明：依靠的是indexOf总是返回第一个元素的位置，后续的重复元素位置与indexOf返回的位置不相等，因此被filter滤掉了
```
### 高阶函数sort
>如果使用默认的sort函数，它会按照它内置的排序算法来排序

``` javascript
	[10, 20, 1, 2].sort(); // [1, 10, 2, 20]  这并不是我们想要的结果
```
**sort的高阶函数定义**
*sort可以接受一个自定义的函数来实现自定义的排序*

``` javascript
		'use strict'
		var arr=[10,20,1,2];
		arr.sort(function(x,y){
			if(x<y){
				return -1;
			}
			if(x>y){
				return 1;
			}
			return 0;
		});
		console.log(arr)	//[1, 2, 10, 20]
```
**特别注意**
sort()方法会直接对当前调用的数组进行修改

``` javascript
		var a1 = ['B', 'A', 'C'];
		var a2 = a1.sort();
		a1; // ['A', 'B', 'C']
		a2; // ['A', 'B', 'C']
		a1 === a2; // true, a1和a2是同一对象
```






### 闭包
