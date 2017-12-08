# this #


----------

this引用的是函数据以执行的环境对象。虽然函数的作用域是在声明时决定的，但是this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用，所以this的指向是可变的。

this是函数的内部属性，所以this的使用环境是在函数中。

this的指向是一个对象。

**this绑定规则**

判断函数中this绑定的对象规则：

1. 函数是否在new中调用（new 绑定）？如果是的话this绑定的是新创建的对象；
2. 函数是否通过call、apply（显式绑定）或者硬绑定调用？ 如果是的话，this绑定的是指定的对象；
3. 函数是否在某个上下文对象中调用（也就是函数作为某个对象的方法调用）（隐式绑定）？如果是的话，this绑定的是那个上下文对象；
4. 如果都不是的话， 使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象；

从上至下逐条判断，就能正确判断this的指向。

- new 绑定，构造函数生成实例中调用；

	function F() {
		this.a = 1;
		this.sayA = function () {
			console.log(this.a);
		}
	}
	var f = new F();
	f.sayA(); // 1

- call、apply调用（显式绑定）；

	- call、apply接收两个参数，第一个是参数是对象，函数this就会指向这个对象，如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作 this 的绑定对象，这个原始值会被转换成它的对象形式（也就是 new String(..)、new Boolean(..)或者new Number(..)）；第二个参数是函数调用的；

			var a = 1;
			var obj = {
				a: 2
			};
			function f() {
				console.log(this.a);
			}
			f(); // 1
			f.call(obj); // 2，硬性将f的this绑定到指定对象
			f.apply(obj); // 2

	- bind，bind的作用与call、apply一样，都是为函数指定执行环境对象，但是bind返回的是一个新函数，而call和apply会直接执行返回结果；

			var a = 1;
			var obj = {
				a: 2
			};
			function f() {
				console.log(this.a);
			}
			f(); // 1
			var f2 = f.bind(obj); // 返回的是一个新函数，所以可以将函数保存至变量，方便以后调用
			f2(); // 2
	
	- 如果你把null或者undefined作为 this 的绑定对象传入call、apply或者bind，这些值在调用时会被忽略，实际应用的是默认绑定规则；

- 作为对象方法调用（隐式绑定）；
		
		var a = 1;
		function f() {
			console.log(this.a);
		}
		var obj = {
			a: 2,
			f: f // 将对象的方法指向函数
		};
		obj.f(); // 2，当函数作为对象方法时，this就指向当前对象

		var f2 = obj.f;
		f2(); //1，相当于直接调用f，所以this指向全局对象

		var obj2 = {
			a: 3,
			f: obj.f
		};
		obj2.f(); // 3，作为对象方法调用

- 默认函数调用，函数独立调用；

		var a = 1; // 全局变量会成为全局对象的属性，所以当this指向全局对象时能够访问
		function f() {
			console.log(this.a);
		}
		f(); // 1，普通的函数调用，this指向全局对象



