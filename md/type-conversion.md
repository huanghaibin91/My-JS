# 类型转换 #


----------

在JS中数据有七种内置类型：空值（ null）、未定义（ undefined）、布尔值（ boolean）、数字（ number）、字符串（ string）、对象（ object）、符号（ symbol， ES6 中新增）

将值从一种类型转换成另一种类型便是类型转换，在JS中常常会自主或不经意间将值进行类型转换。类型转换通常有两种情况一种是显式强制类型转换，这种情况是使用JS提供的类型转换函数，将值转换成特定的类型；另一种情况是隐式强制类型转换，是在进行某些操作是JS隐式的将值的类型进行转换后再进行操作。

**显式强制类型转换**

JS中提供了Number()、String()、Boolean()等函数将值转换成特定的类型。这种使用函数方法显式的将值的类型进行转换就称为显式强制类型转换。

- Number()，将值的类型转换成数字。Number()函数转换规则：

	- 参数是布尔值，true和false分别转换成1和1；
	- 参数是数字，返回参数本身；
	- 参数为null，返回0；
	- 参数是undefined，返回NaN；
	- 参数是字符串，有下面情况：
	
		- 参数字符串只包含数字，转化成十进制数值；
		- 参数字符串包含有效浮点数，转换成对应的浮点数值；
		- 参数字符串包含有效的十六进制格式，转换成相应的十进制整数；
		- 参数字符串为空，转换为0；
		- 参数字符串包含上述格式之外的字符，转换为NaN；   
		
	- 参数是对象，调用对象自身的valueOf()方法。如果返回原始类型的值，则直接对该值使用Number()函数，不再进行后续步骤。如果valueOf()方法返回的还是对象，则改为调用对象自身的toString()方法。如果toString()方法返回原始类型的值，则对该值使用Number()函数，不再进行后续步骤。如果toString()方法返回的是对象，就报错。

			var a = {
				name: 'a',
 			};
			Number(a); // NaN
			a.valueOf = function () {
					return 1;
			};
			Number(a); // 1

			var b = [2];
			b.valueof(); // [2]
			b.toString(); // '2'
			Number(b); // 2

- JS中parseInt和parseFloat函数可以将字符串转化为整数和浮点数，parseInt()可以传递第二个参数，代表解析的进制，parseFloat只能解析十进制值。这两个方法允许字符串中含有非数字字符，解析按从左到右的顺序，如果遇到非数字字符就停止，而Number()不允许参数含有非数字字符，如包含返回NaN。

		parseInt('10a') // 10
		parseInt('AF', 16) // 175
		parseInt('AF') // NaN 
		
		parseFloat('10.01a') // 10.01

- toString()方法和String()函数，将值的类型转换成字符串。

	- toString()方法，除null和undefined之外的所有值都有toString()方法，此方法可以接受一个参数，表示输出数值的进制基数。
	 	
			var a = 10;		
			a.toString(); // '10'
			a.toString(2); // '1010'，10的二进制字符串

			var b = null;
			b.toString(); // 报错

			var c = {
				name: 'c'
			};
			c.toString(); // "[object Object]"，默认的toString()方法可以用来判断对象类型
			c.toString = function () {
				return 'c';
			};
			c.toString(); // 'c'，改写toString()方法，返回设定的值

	- String()函数，可以将任何类型的值转换为字符串，转换规则：

		- 参数有toString()方法，调用该方法返回结果；
		- 参数是null，返回'null'；
		- 参数是undefined，返回'undefined'；

- Boolean()函数，将值转换为布尔值，在JS中`false、''、0、NaN、null、undefined`为假，其余都为真值，所以使用Number()函数`false、''、0、NaN、null、undefined`会被转换为false，其余转换为true。

**隐式强制类型转换**

在JS的某些操作中，JS会隐式将值进行类型转换，例如使用'=='、'+'和'-'等操作，所以需要在不同情况下进行区分。

- `+`运算符

	- `+`运算符单独使用，类似于调用Number()函数；
			
			+'-1' // 1
			+{name: 'a'} // NaN
			+{name: 'b', toString: function () { return 2 }} // 2

	- `+`运算符作为加法运算符；

		- 如果两个操作数都是数字，进行数学上的加法运算
		- 如果两个操作数都是字符串，进行字符串拼接操作
		- 如果只有一个操作数是字符串，则将另一个操作数转化为字符串，然后将两个字符串拼接起来
				
				1 + 1 // 2
				'a' + 'b' // 'ab'
				1 + 'a' // '1a'
	
- `-`，`*`，`/`运算符，会将两边操作数转换为数字，转换规则类似调用Number()；
	
		1 - 'a' // NaN
		1 * 'a' // NaN
		1 / 'a' // NaN

		1 - '2' // -1
		1 * '2' // 2
		1 / '2' // 0.5

		1 - {name: 'b', toString: function () { return 2 }} // -1
		1 * {name: 'b', toString: function () { return 2 }} // 2
		1 / {name: 'b', toString: function () { return 2 }} // 0.5

- `==`运算符，在判定操作数是否相等时规则：
	
	- NaN不与任何数相等，包括NaN本身；
	- null与undefined宽松相等；
			
			null == undefined // true
			null === undefined // false

	- 操作数其中一个为数字，另一个为字符串，则将字符串转换为数字；
			
			1 == '1' // true
			'1' == 1 // true

	- 操作数其中一个为布尔值，则将布尔值转换为数字再进行比较；
			
			true == 1 // true
			true == '1' // true
			null == false // false
			true == { valueOf: function () {return 1;}} // true

	- 操作数一个为对象，另一个为数字或字符串，对象进行toPrimitive(obj)操作，等价于：先计算obj.valueOf()，如果结果为原始值，则返回此结果；否则，计算obj.toString()；
	
			'a' == { valueOf: function () {return 'a';}} // true
			'a' == { toString: function () {return 'a';}} // true
			'a' == { valueOf: function () {return 'a';}, toString: function () {return 'b';}} // true
			'a' == { valueOf: function () {return 'b';}, toString: function () {return 'a';}} // false

- 隐式转换为布尔类型，在下面的情况下，值会被转换为布尔类型，规则与调用Boolean()函数一致：

	- if (..) 语句中的条件判断表达式；   
	- for ( .. ; .. ; .. ) 语句中的条件判断表达式（第二个）；   
	- while (..) 和 do..while(..) 循环中的条件判断表达式；  
	- ? : 中的条件判断表达式；   
	- 逻辑运算符 ||（逻辑或）和 &&（逻辑与）左边的操作数（作为条件判断表达式）； 
	 
				