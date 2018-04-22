# 类 #


----------

JS是没有类的，但JS可以利用构造函数实现近似类的功能。

**构造函数**

构造函数，是用来创建对象的函数。与普通的函数声明的区别是，约定函数名首字母大写，还有就是调用时要使用`new`关键字，如果不使用`new`调用就跟普通函数一样。

new关键字创建对象过程：

- 创建一个新的空对象；
- 设置新对象的`constructor`属性为构造函数的名称，设置新对象的`[[Prototype]]`属性指向构造函数的`prototype`对象；
- 将构造函数的作用域赋给新对象，`this`将指向这个新对象；
- 执行构造函数中的代码（为新对象添加属性）并返回新对象；

JS一般使用构造函数和原型配合实现类的功能：

		function F(val) { // 构造函数也是函数，所以可以传递参数
			'use strict'; // 1.使用严格模式，普通调用会报错
			// 2.检测函数的调用方式，确保构造函数以new操作符形式调用	
			if (!(this instanceof F)) {
                return new F(val);
            }
			// 3.ES6中函数内部可以使用new.target属性。如果当前函数是new命令调用，new.target指向当前函数，否则为undefined
			if (!new.target) {
                throw new Error('请使用 new 命令调用！');
            }
			// 以上是三种保证构造函数是new调用的方式
	
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
		
		// 创建实例对象
		var f1 = new F(1);
		var f2 = new F(2);

		f1.arr.push(1); // 实例属性被更改后，实例对象不会共享
		f1.sayArr(); // [1]
		f2.sayArr(); // []

		f1.array.push(1); // 原型属性被更改后，实例对象会共享
		f1.sayArray(); // [1]
		f2.sayArray(); // [1]

**类的继承**

类有一个很重要的特征，就是类可以继承，在JS中通过原型链实现继承。

在JS中，是通过 **[原型链](https://github.com/huanghaibin91/My-JS/blob/master/md/prototype.md)** 来实现继承。

- 原型链继承，缺点是继承的属性和方法会被所有实例共享；

		function SuperColor() {
            this.colors = ['red', 'blue', 'green'];
        }
        SuperColor.prototype.sayColor = function () {
            console.log(this.colors);
        }
        function SubColor() {

        }
		SubColor.prototype = new SuperColor(); // SuperColor生成的实例作为SubColor的原型属性，这样SuperColor的属性会被共享
		var color1 = new SubColor();
        var color2 = new SubColor();
        color1.colors.push('black');
		color1.sayColor(); // ['red', 'blue', 'green', 'black']
		color2.sayColor(); // ['red', 'blue', 'green', 'black']

- 原型式继承，跟原型链继承特点一样，但它是创建一个在原型上继承了传入对象的空对象；
		
		// object会创建一个空对象，空对象的原型会继承传入参数o的属性和方法
		function object(o) {
			function F() {};
			F.prototype = o;
			return new F();
		}
		
		var obj  = {
			a: 1
		};
		var a = object(obj);
		a.a // 1
		// 然后可以给新建的对象添加实例属性或方法
		a.b = 2; 
		a.b // 2

	ES5通过Object.create()方法规范了原型式继承，Object.create()，接收两个参数一个用作新对象原型的对象（必须）和一个为新对象定义额外属性的对象（可选，作为实例属性）（写法同Object.defineProperties()方法一致）。

		// Object.create()方法传入一个参数时与上面的object()方法一样
		var obj  = {
			a: 1
		};
		var a = Object.create(obj);
		a.a // 1
		// 然后可以给新建的对象添加实例属性或方法
		a.b = 2; 
		a.b // 2

		var b = Object.create(obj, { // 第二个参数的会作为实例属性
			a: {
				value: 3
			}
		});
		b.a // 3，第二个参数定义实例属性的会屏蔽掉第一个参数原型继承的

	Object.create()方法可以创建一个不继承原型属性的空对象，`Object.create(null)`

- 寄生式继承，在原型式继承的基础再上封装一个函数，并在函数内部增强对象，缺点是不能做到函数复用；

		// 模板类似下面：
		function parasiticObject(o) {
			var obj = object(o);
			// 下面写要给对象添加的实例方法和属性
			
			return obj;
		}
		
		var obj  = {
			a: 1
		};
		var a = function (obj) {
			var o = object(obj);
			o.b = 2; // 这个会成为实例属性
		}

		a.a // 1
		a.b // 2

- 借用构造函数继承，利用call、apply继承，优点是能传递参数，缺点是不能继承父类原型中的属性和方法；

		function SuperColor() {
            this.colors = ['red', 'blue', 'green'];
        }
		SuperColor.prototype.sayColor = function () {
            console.log(this.colors);
        }
        function SubColor() {
            // 利用call、apply将父类的属性和方法传给子类，这样缺点是不会继承父类的原型属性和方法
            SuperColor.call(this); // 实例之间不会共享数据，之间都是独立的，还有个好处是可以传递参数
        }
        var color1 = new SuperColor();
        var color2 = new SubColor();
		var color3 = new SubColor();
        color2.colors.push('black');

		color1.sayColor(); // ['red', 'blue', 'green']
		color2.sayColor(); // TypeError: color2.sayColor is not a function，父类原型上的方法没有继承
		
		// 子类继承父类的实例属性且不会共享
		color2.colors // ['red', 'blue', 'green', 'black']
		color3.colors // ['red', 'blue', 'green']
		
- 组合式继承，利用原型链继承和借用构造函数函数继承，让两者相互补充，既能继承实例也能共享原型，缺点是无论什么情况都会调用两次父类的构造函数，父类的实例属性和方法会被创建两次，子类的实例和原型都会有父类的实例属性和方法，只是实例中的属性和方法会屏蔽掉原型中同名的属性和方法；
		
		function SuperColor() {
            this.colors = ['red', 'blue', 'green'];
        }
        SuperColor.prototype.sayColor = function () {
            console.log(this.colors);
        }
        function SubColor() {
			SuperColor.call(this); // 第二次调用父类作为子类的实例对象，并屏蔽掉原型中的同名属性或方法
        }
		SubColor.prototype = new SuperColor(); // 第一次调用父类创建实例对象作为子类的原型     
	
        var color1 = new SubColor();
		var color2 = new SubColor();
        color1.colors.push('black');

		color1.sayColor(); // ['red', 'blue', 'green', 'black']
		color2.sayColor(); // ['red', 'blue', 'green']

- 寄生组合式继承，通过借用构造函数来继承属性，通过原型链的混成形式来继承方法；

		function SuperColor() {
            this.colors = ['red', 'blue', 'green'];
        }
        SuperColor.prototype.sayColor = function () {
            console.log(this.colors);
        }
        function SubColor() {
			SuperColor.call(this); // 只调用一次父类的构造函数
        }
		SubColor.prototype = Object.create(SuperColor.prototype); // 通过原型式继承将要继承的对象赋值给子类原型

		var color1 = new SubColor();
		var color2 = new SubColor();
        color1.colors.push('black');

		color1.sayColor(); // ['red', 'blue', 'green', 'black']
		color2.sayColor(); // ['red', 'blue', 'green']


		