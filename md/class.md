# 类 #


----------

JS是没有类的，但JS可以利用构造函数实现近似类的功能。

**构造函数**

构造函数，是用来创建对象的函数。与普通的函数声明的区别是，约定函数名首字母大写，还有就是调用时要使用`new`关键字，如果不使用`new`调用就跟普通函数一样。

new关键字创建对象过程：

- 创建一个新的空对象；
- 设置新对象的constructor属性为构造函数的名称，设置新对象的__proto__属性指向构造函数的prototype对象；
- 将构造函数的作用域赋给新对象，this将指向这个新对象；
- 执行构造函数中的代码（为新对象添加属性）并返回新对象；

		function F(val) { // 构造函数也是函数，所以可以传递参数
			// 构造函数中定义实例特有的属性和方法
			this.a = val; 
			this.arr = [];
		}
		// 原型中定义实例对象共享的属性和方法
		F.prototype.array = [];
		F.prototype.sayA = function () { 
			console.log(this.a);
		};
		F.prototype.sayArr = function () { 
			console.log(this.arr);
		};
		F.prototype.sayArray = function () { 
			console.log(this.array);
		};

		var f1 = new F(1);
		var f2 = new F(2);

		f1.arr.push(1); // 实例属性被更改后，实例对象不会共享
		f1.sayArr(); // [1]
		f2.sayArr(); // []

		f1.array.push(1); // 原型属性被更改后，实例对象会共享
		f1.sayArray(); // [1]
		f2.sayArray(); // [1]
