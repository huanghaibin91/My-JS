# 高阶函数 #


----------

函数是可以接受并且返回任何类型的值。一个函数如果可以接受或返回一个甚至多个函数，它被叫做高阶函数。

**函数返回函数**

函数返回函数的高阶函数最常见的用法就是闭包，[闭包笔记](https://github.com/huanghaibin91/My-JS/blob/master/md/closure.md)。

**偏函数**

偏函数则是固定一个函数的一个或者多个参数，也就是将一个n元函数转换成一个n - x元函数。

	// underscore的偏函数实现
	function partial(func) {
	    var boundArgs = slice.call(arguments, 1);
	    var bound = function () {
	      	var position = 0,
	        length = boundArgs.length;
	      	var args = Array(length);
	      	for (var i = 0; i < length; i++) {
	        	args[i] = boundArgs[i] === _ ? arguments[position++] : boundArgs[i];
	      	}
	      	while (position < arguments.length) args.push(arguments[position++]);
	      	return executeBound(func, bound, this, this, args);
	    };
	    return bound;
  	};

**函数柯里化**

柯里化是将一个多参数函数转换成多个单参数函数，也就是将一个n元函数转换成n个一元函数。

	// 柯里化函数
	var curry = function (fn, args) {
	    var length = fn.length;
	    args = args || [];
	    return function () {
	        var _args = args.slice(0),
	            arg,
	            i;
	        for (i = 0; i < arguments.length; i++) {
	            arg = arguments[i];
	            _args.push(arg);
	        }
	        if (_args.length < length) {
	            return curry.call(this, fn, _args);
	        } else {
	            return fn.apply(this, _args);
	        }
	    }
	}