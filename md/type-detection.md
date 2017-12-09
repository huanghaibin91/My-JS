# 类型检测 #


----------

在JS中数据有七种内置类型：空值（ null）、未定义（ undefined）、布尔值（ boolean）、数字（ number）、字符串（ string）、对象（ object）、符号（ symbol， ES6 中新增）

有的时候我们对数据的类型进行判定，在JS中可以通过typeof运算符、instanceof运算符、Object.prototype.toString方法来判断数据的类型

**typeof运算符**

typeof运算符返回类型的字符串值
		
		typeof undefined // 'undefined'
		typeof null // 'object'，特殊情况
		typeof 1 // 'number'
		typeof '1' // 'string'
		typeof true  // 'boolean'
		typeof function () {} // 'function'
		typeof { name: 'a' } // 'object'
		typeof [1] // 'object'
		typeof Symbol() // "symbol"

**instanceof运算符**

在上面使用typeof检测数组和对象时，返回的都是'object'，这种情况下不能准确获取对象的类型。这时候可以使用instanceof运算符, instanceof运算符检测是什么类型的对象，返回一个布尔值。  

instanceof只能用来判断对象和函数，不能用来判断字符串和数字等，除非字符串和数字是构造函数生成的，instanceof的原理是检测对象的原型链是否指向构造函数的prototype对象。

		var a = [1];
		a instanceof Array // true
		a instanceof Object // true

		function A(){} 
		function B(){} 
		B.prototype = new A();//JavaScript 原型继承
		 
		var b = new B(); 
		console.log(b instanceof B) //true 
		console.log(b instanceof A) //true

		Object instanceof Object // true

		var c = /a/g;
		c instanceof RegExp; // true

前面说`typeof null === 'object'`，但是`null instanceof Object`返回`false`，正确检测null可以使用。

	var a = null;
	(!a && typeof a === "object"); // true

**Object.prototype.toString方法**

Object.prototype.toString方法可以准确区分数据的类型，Object.prototype.toString方法被调用时会执行以下步骤：

1. 如果this的值为undefined,则返回"[object Undefined]"
2. 如果this的值为null,则返回"[object Null]"
3. 让O成为调用ToObject(this)的结果.
4. 让class成为O的内部属性[[Class]]的值.
5. 返回三个字符串"[object ", class, 以及 "]"连接后的新字符串
		
		Object.prototype.toString.call(1) // [object Number] 
        Object.prototype.toString.call('1') // [object String] 
        Object.prototype.toString.call(true) // [object Boolean] 
        Object.prototype.toString.call(undefined) // [object Undefined] 
        Object.prototype.toString.call(null) // [object Null] 
        Object.prototype.toString.call({}) // [object Object] 
        Object.prototype.toString.call([]) // [object Array] 
        Object.prototype.toString.call(new Date) // [object Date] 
        Object.prototype.toString.call(new Error()) // [object Error] 
        Object.prototype.toString.call(/a/) // [object RegExp] 
        Object.prototype.toString.call(function () {}) // [object Function]

**其他检测方法**

- 


