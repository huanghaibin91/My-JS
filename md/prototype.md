# 原型 #


----------

每个**函数对象**都有一个prototype属性，这个属性这个属性是一个指针，指向一个对象。当函数作为构造函数使用时这个对象会成为调用该构造函数而创建的实例的原型，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

就是说，构造函数生成的实例会有一组共享的属性和方法，这些属性和方法在构造函数的原型对象里。

	function F(val) {
		this.a = val;
	}
	F.prototype.sayA = function () {
		console.log(this.a);
	}

	var f1 = new F(1);
	var f2 = new F(2);
	// 由F函数生成的实例都会包含sayA方法
	f1.sayA(); // 1
	f2.sayA(); // 2

**prototype**

只要创建一个新函数，就会创建一个prototype属性，这个属性指向原型对象。原型对象初始只包含`constructor`属性，指向构造函数。

	function F(val) {
		this.a = val;
	}

	typeof F; // 'function'，构造函数也是函数类型

	// 上面的构造函数的默认prototype类似下面
	F.prototype = {
		constructor: F
	};

	// 当需要在默认原型上添加属性和方法时|，就像在一个已经创建好的对象上添加新属性一样
	F.prototype.b = 2;
	F.prototype.sayA = function () {
		console.log(this.a);
	}

	var f1 = new F(1); // 重写原型之前创建的实例，拥有原来的属性和方法
	f1.sayA(); // 1
	f1.sayB(); // f1.sayB is not a function

	// 但如果像下面这样：
	// 这种写法相当于将原型对象的引用指向一个新的对象，这样的话原来的原型对象就丢失了，用这个新对象替代。添加在原来原型对象上的属性和方法也就无效了。
	F.prototype = {
		// 重写之后如果没有添加constructor属性，constructor属性也会丢失
		b: 3,
		sayB: function () {
			console.log(this.b);
		}
	}
	// 重写原型之后constructor属性丢失，如果需要，可以添加一个
	Object.defineProperty(F.prototype, 'constructor', {
        enmumerable: false, // constructor属性不可枚举
        value: F // 设定需要指向的构造函数
    }); 

	var f2 = new F(1); // 在重写原型之后创建的实例，拥有新的属性和方法
	f2.sayA(); // f1.sayA is not a function
	f2.sayB(); // 3

	// 产生这两种情况的原因其实就是执行上下文。
	
- 原型方法：

	- proObj.isPrototypeOf(obj)，判断proObj是否是obj的原型对象，是返回true，反之false
			
			Object.prototype.isPrototypeOf({}); // true

	- Object.getPrototypeOf(obj)，获取obj的原型对象

			Object.getPrototypeOf({}); // Object.prototype

- 原型规则：

	- 对象实例只能读取原型对象中的属性和方法，但不能改写、删除或添加；
	- 对象实例搜索属性和方法，会先搜索实例再搜索原型链，如果实例中存在，就会屏蔽原型中的同名属性或方法；

**原型链**

原型链，是JS实现继承的主要方法，基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。

我们知道构造函数、原型和实例之间有如下关系：**每个构造函数都有一个原型对象（prototype），原型对象都有一个指向构造函数的指针（constructor），而实例对象都包含一个指向原型对象的内部指针（ [[Prototype]] ）**

`[[Prototype]]`是一个JS内置属性，理解原型链，就得先理解`[[Prototype]]`;每个**对象（除null）**都有`[[Prototype]]`属性，指向其构造函数的原型对象，是不可见的。

在浏览器中 `[[Prototype]]`属性就是 `__proto__`。

正因为`[[Prototype]]`指向构造函数的原型对象，对象才能获取构造函数原型上的属性和方法。然后原型对象也是一个对象，它也有`[[Prototype]]`属性指向它的构造函数的原型对象，这样就构成了原型链。

**所以`[[Prototype]]`是实现原型链的基础**

`[[Prototype]]`有个特殊值，就是Object.prototype对象也有`[[Prototype]]`属性，值为null，处于原型链的最顶层。
	
	// 浏览器环境下例子，[[Prototype]]就是__proto__：
	
	var f = { // 字面量创建一个对象，相当于调用Object构造函数创建对象
		a: 1
	}; 
	// 原型链： f --__proto__--> Object.prototype --__proto__--> null

	function A() {
		this.a = 1;
	}
	A.prototype.sayA = function () {
		console.log(this.a);
	};

	var a = new A();
	// 原型链： a --__proto__--> a.prototype --__proto__ --> Object.prototype --__proto__ --> null

	function B() {
		this.b = 2;
	} 
	B.prototype = new A();

	var b = new B();
	// 原型链：b --__proto__--> B.prototype --__proto__ --> A.prototype --__proto__ --> Object.prototype --__proto__ --> null

实例对象会按照原型链的顺序搜索属性或方法。



