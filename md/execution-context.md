# 执行上下文 #


----------

执行上下文，就是Js执行的时候的一个运行环境/作用域（scope）。执行上下文决定了Js执行过程中可以获取哪些变量、函数、数据，一段程序可能被分割成许多不同的上下文，每一个上下文都会绑定一个变量对象（variable object），它就像一个容器，用来存储当前上下文中所有已定义或可获取的变量、函数等。

**可执行代码**

既然执行上下文是在JS执行的时候创建的，那么JS中可执行代码的类型有：

- 全局代码，这个是默认的代码运行环境，一旦代码被载入，引擎最先进入的就是这个环境；
- 函数级别的代码，当执行一个函数时，运行函数体中的代码；
- Eval的代码，在Eval函数内运行的代码；

**执行上下文特点**

执行上下文有以下特点：

- 单线程；
- 同步执行，只有栈顶的上下文处于执行中，其他上下文需要等待；
- 唯一的一个全局上下文，它在浏览器关闭时出栈；
- 函数的执行上下文的个数没有限制；
- 每次某个函数被调用，就会有个新的执行上下文为其创建，即使是调用的自身函数，也是如此；

**执行上下文的创建和执行**

- 上下文的创建阶段：函数被调用，但尚未开始执行（代码分析预处理阶段），此时会为执行上下文创建作用域链，创建变量、函数和参数以及求this的值。这也是发生声明提升的阶段，同时也是产生声明提升的原因；
- 执行阶段：指派变量的值和函数的引用并解释执行代码，也就是变量赋值，函数引用，执行其它代码，在执行阶段，JS引擎会创建执行上下文栈来管理执行上下文；   

简单例子：

		var a = 1; // ①
		function f() { // ②
			var a = 2; // ③
			function sayA() { // ④
				console.log(a); // ⑤
			}
			sayA(); // ⑥
		} 
		f(); // ⑦
		
首先先将执行上下文和执行上下文栈具象化：

- 将每个执行上下文看作一个对象，它包含三个属性：

		ECObj = {
			variableObject: {}, // 变量对象，函数中的arguments对象, 参数, 内部的变量以及函数声明 
   			scopeChain: [], // 作用域链，包含执行上下文自身变量对象以及所有父执行上下文中的变量对象，也就是所有可访问的变量和函数
   			this: {} // this指向的对象
		};

- 将执行上下文栈看作一个数组；

		ECStack = []; // 初始执行上下文栈为空数组

上面代码具体执行情况：

1. 代码开始执行之前，首先创建全局执行上下文；

	    globalECObj = {
			variableObject: {
				f: pointer to function f(), // 函数声明会指向该函数在内存中的地址的一个引用，所以函数可以在声明之前调用
				a: undefined // 变量声明初始化会是undefined
			},
   			scopeChain: Object.assign({}, globalECObj.variableObject), // 作用域链是全局作用域
   			this: window
		};

		// 执行全局执行上下文，全局执行上下文入栈
		ECStack.push(globalECObj);

2. 全局执行上下文创建完毕，开始执行代码，从上至下，① 为赋值操作；

		// ① var a = 1; 给全局变量a赋值
		globalECObj = {
			variableObject: {
				f: pointer to function f(), 
				a: 1
			},
   			scopeChain: Object.assign({}, globalECObj.variableObject),
   			this: window
		};

3. 跳过函数声明直到遇到可执行到代码 ⑦ ；

		// ⑦ f(); 遇到函数调用，创建函数f的执行上下文
		fECObj = {
			variableObject: {
				sayA: pointer to function sayA(),
				a: undefined
			},
   			scopeChain: Object.assign({}, globalECObj.variableObject, fECObj.variableObject), // 作用域链是全局作用域和f函数作用域
   			this: window
		};

		// 函数f执行上下文入栈
		ECStack.push(fECObj);

		// 执行f执行上下文
		// ③ var a = 2; 给局部变量a赋值
		fECObj = {
			variableObject: {
				sayA: pointer to function sayA(),
				a: 2
			},
   			scopeChain: Object.assign({}, globalECObj.variableObject, fECObj.variableObject),
   			this: window
		}

4. 执行函数f执行上下文中又遇到可执行代码 ⑥ ；
		
		// ⑥ sayA(); 遇到函数调用，创建函数sayA执行上下文
		sayAECObj = {
			variableObject: {},
   			scopeChain: Object.assign({}, globalECObj.variableObject, fECObj.variableObject, sayAECObj.variableObject), // 作用域链是全局作用域和f函数作用域和sayA函数作用域
   			this: window
		};

		// 函数sayA执行上下文入栈
		ECStack.push(sayAECObj);

		// 执行sayA执行上下文
		2 // 弹出结果2
			
5. sayA函数执行结束，从执行上下文栈弹出

		ECStack.pop();

6. f函数执行结束，从执行上下文栈弹出

		ECStack.pop();

7. 现在只剩全局执行上下文了，它会在浏览器关闭从执行上下文栈中弹出

		
**执行上下文过程**

1. JS代码加载完成；
2. 创建全局执行上下文，为全局执行上下文创建作用域链，创建变量、函数和参数以及求this的值；
3. 全局执行上下文入执行上下文栈，开始执行代码；
4. JS为单线程，从上至下当遇到可执行代码，就新建一个执行上下文，为新建执行上下文创建作用域链，创建变量、函数和参数以及求this的值；
5. 新建执行上下文入执行上下文栈，开始执行代码；
7. 如果可执行代码中还有可执行代码就重复4和5；
8. 执行完成的执行上下文从执行上下文栈顶弹出；
9. 关闭浏览器后全局执行上下文从执行上下文栈中弹出，执行上下文栈清空；



