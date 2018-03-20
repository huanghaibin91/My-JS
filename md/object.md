# 对象 #


----------

对象就是一组数据和功能的集合。

**对象声明**

- 构造函数声明，调用`new Object()`构造函数声明对象；
- 对象字面量声明，使用`var obj = {}；`声明对象，推荐；

**对象构造函数方法**

- 对象属性模型的相关方法：
	- `Object.getOwnPropertyDescriptor()`，获取某个属性的描述对象；
	- `Object.defineProperty()`，通过属性描述对象，定义某个属性；
	- `Object.defineProperties()`，通过属性描述对象，定义多个属性；
	- `Object.getOwnPropertyNames()`，返回直接定义在某个对象上面的全部属性的名称，返回一个数组，不包含继承和原型的属性键名，Object.getOwnPropertyNames方法还返回不可枚举的属性名，类似数组的长度属性或者对象constructor属性；
	- `Object.keys()`，遍历对象的属性，返回一个数组，该数组的成员都是对象自身的（而不是继承和原型的）所有属性名；

- 控制对象状态的方法：  
	- `Object.preventExtensions()`，防止对象扩展，保留已有属性，禁止添加新属性；
	- `Object.isExtensible()`，判断对象是否可扩展；
	- `Object.seal()`，禁止对象配置，这个方法实际上会在一个现有对象上调用`Object.preventExtensions()` 并把所有现有属性标为`configurable: false`密封之后不仅不能添加新属性， 也不能重新配置或者删除任何现有属性（ 虽然可以修改属性的值）；
	- `Object.isSealed()`，判断一个对象是否可配置；
	- `Object.freeze()`，冻结一个对象，这个方法实际上会在一个现有对象上调用`Object.seal()`并把所有“数据访问” 属性标记为 `writable:false`，这样就无法修改它们的值；
	- `Object.isFrozen()`，判断一个对象是否被冻结；

- 原型链相关方法：
	- `Object.create()`，该方法可以指定原型对象和属性，返回一个新的对象；
	- `Object.getPrototypeOf()`，获取对象的Prototype对象；
	- `obj1.prototype.isPrototypeOf(obj2)`，obj1.prototype是否是obj2的原型；

**对象实例属性和方法**

- `obj.constructor`，保存着用于创建当前对象的函数，即当前对象的构造函数；
- `obj.hasOwnProperty(propertyName)`，用于检验属性是否在实例中，而不是原型中，返回布尔值；
- `obj.isPrototypeOf(object)`，用于检查当前对象obj是否是传入的对象object的原型；
- `obj.propertyIsEnumerable(propertyName)`，检查属性properName是否能用for-in枚举；
- `obj.toLocalString()`，返回对象的字符串表示，该只付出爱与执行环境对应；
- `obj.toString()`，要求的是字符串那么就会调用toString，如果调用toString方法的是数字可以传入参数，以指定进制表示的字符串；
- `obj.valueOf()`，要求的是原始值那么就会调用valueOf；
- `delete obj.propertyName`，删除对象属性，只能删除自身的属性，不能删除继承和原型的属性；

