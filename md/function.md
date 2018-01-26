# 函数 #


----------

函数是由事件驱动的或者当它被调用时执行的可重复使用的代码块，在JS中定义函数的方式有两种：函数声明和函数表达式。

区分函数声明和表达式最简单的方法是看function关键字出现在声明中的位置（不仅仅是一行代码， 而是整个声明中的位置）。如果function是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。
	
	// 函数声明
	function f1() {
		//
	}
	
	// 函数表达式
	var f2 = function () {
		//
	}
	
	// 函数表达式
	(function () {
		//
	})()

函数声明会提升，但函数表达式不会。声明提升在[执行上下文](https://github.com/huanghaibin91/My-JS/blob/master/md/execution-context.md)。

	function f() {
		console.log(1);
	}
	f(); // 1
	var f = function () {
		console.log(2);
	}
	f(); // 2

因为函数声明会提升，所以不能非函数的代码块中声明函数，比如if。

	// 下面代码的原始意图是不声明函数f，但是由于f的提升，导致if语句无效，所以上面的代码不会报错
    if (false) {
		// 这样声明因为if不是块级作用域，那么函数会提升，不论if如何判断f()都会生效
        function f1() {
			console.log(1);
		} 
    }
    f1(); // 1
        
	
    // 要达到在条件语句中定义函数的目的，只有使用函数表达式
    if (false) {
        var f2 = function () {
			console.log(2);
		}
    }
    f2(); // TypeError: f2 is not a function

但我在测试时发现新版火狐、Chrome、IE11都不会在if中提升函数声明，但IE11以下版本会，所以为避免出错，尽量在if等非函数代码块中使用函数表达式。

**JS中函数是第一等公民**

在JS中，函数也是一种对象，函数也拥有对象的特征，所以同对象一样为第一等公民：

- 可以在程序执行时动态创建函数；
- 可以将函数赋值给变量，可以将函数引用拷贝至另一个变量，可以扩充函数，除了某些特殊场景外均可以被删除；
- 可以将函数作为参数传入另一个函数，也可以被当做返回值返回；
- 函数可以包含自己的属性和方法；

**函数传递参数**

JS中所有函数的参数都是按值传递的。

	var a = 1;
	function f1(arg) {
        arg = 2;
        console.log(arg); //2，参数如果是基本类型是按值传递，传递的是参数的副本，操作不影响原变量
    }
    f1(a);
    console.log(a); // 1

    var obj = {
        a: 1
    };
    function f2(arg) {
        arg.value = 2;
        console.log(arg.value); //2，传递对象的时候，传递对象的引用的副本，改变参数会改变原参数
    }
    f2(obj);
    console.log(obj.a) // 2
        
    var obj2 = {
        value: 1
    };
    function f3(arg) {
        arg = { // 函数内部重写参数，这下变量引用的就是一个局部变量
			a: 2
		};
        console.log(arg.a); //2，传递引用参数副本，但是又重新给参数赋值，切断原来的引用，所以原有引用为改变
    }
    f3(obj2);
    console.log(obj2.a) // 1

**函数属性**

函数属性包括：length和prototype

length，函数的length属性是只读属性，函数定义时的形参个数即通常也是函数调用时期望传入函数的参数个数。

	function add(a, b) {
		return a + b;
	}
	add.length // 2，函数声明时形参长度

prototype，每个函数都包含一个prototype属性，这个属性是指向一个对象的引用，这个对象称为“原型对象”。每个函数都包含不同的原型对象。当函数用做构造函数时，新创建的对象会从原型对象上继承属性。

函数内部属性：arguments和this

arguments，arguments是一个类数组对象，包含传入函数的所有参数，arguments的主要用途是保存函数参数。注意：不要修改arguments对象，如果需要修改，使用`[].slice.call(arguments)`将参数对象复制到一个真实数组中再在数组中进行修改。
	
	function add(a, b) {
		return arguments[0] + arguments[1];
	}
	add(1, 2); // 3

this，this引用的是函数据以执行的环境对象，关于this请点这里[this](https://github.com/huanghaibin91/My-JS/blob/master/md/this.md)

	var a = {
		name: 'a',
		sayName: function () {
			console.log(this.name);
		}
	};
	a.sayName(); // 'a'

**函数方法**

每个函数都包含两个非继承而来的方法：apply()和call()。这两个方法的用途都是在特定的域中调用函数，其真正强大之处在于能够扩充函数赖以运行的作用域，ES5又新增bind()方法。

关于apply、call和bind请点这里[this](https://github.com/huanghaibin91/My-JS/blob/master/md/this.md)

因为函数也是对象，所以函数也有toString()方法，返回函数的字符串形式。

**匿名函数**

函数声明必须要有标识符名称，但函数表达式可以不写标识符，这样的函数称为匿名函数。
	
	// 匿名函数赋值给变量
	var f = function () {
		//
	}
	
	// 匿名函数自执行，自执行后内部声明的局部变量和函数会被销毁
	(function () {
		//
	})()	
	
	// 匿名函数不会自执行，会报错，这是因为这是一个函数声明，函数声明后边不能跟圆括号，函数表达式后边可以跟圆括号
	function () {
		// 
	}()

**函数递归**

递归函数是当一个函数调用自身，并且该调用做了同样的事情，这个循环持续到基本条件满足时，调用循环返回。递归函数必须要有一个结束循环的条件，不然会一直循环下去。

最经典的递归例子就是斐波那契函数：

	function fib(n) {
    	if (n <= 1) { // 结束递归的基本条件
			return n;
		}
    	return fib( n - 2 ) + fib( n - 1 ); // 通过函数名在函数内部调用函数本身
	}


	




        

