# 正则表达式 #


----------

正则表达式用于对字符串模式匹配及检索替换。

**修饰符**

- `g`，全局模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即停止；
- `i`，不区分大小写模式，即在确定匹配项时忽略模式与字符串的大小写；
- `m`，多行模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模式匹配的项；

**元字符**

元字符是拥有特殊含义的字符。

- `.`，查找单个字符，除了换行和行结束符；
- `\w`，查找单词字符；
- `\W`，查找非单词字符；
- `\d`，查找数字；
- `\D`，查找非数字字符；
- `\s`，查找空白字符；
- `\S`，查找非空白字符；
- `\b`，匹配单词边界；
- `\B`，匹配非单词边界；
- `\0`，查找NULL字符；
- `\n`，查找换行符；
- `\f`，查找换页符；
- `\r`，查找回车符；
- `\t`，查找制表符；
- `\v`，查找垂直制表符；
- `\xxx`，查找以八进制数 xxx 规定的字符；
- `\xdd`，查找以十六进制数 dd 规定的字符；
- `\uxxxx`，查找以十六进制数 xxxx 规定的 Unicode 字符；


**字符组（方括号）**

在正则表达式中，包裹于方括号中的为字符组，字符组的匹配结果是其中的一个字符。

	[abc]  // 表示匹配一个字符，它可以是a、b、c之一

字符组可以使用范围表示法处理字符组中字符较多的情况：

	[abcdef] // 当字符组字符较多时
	[a-f] // 使用 - 作为连字符简写上面代码
	[ab-] // 当连字符不处于两个字符之间时或前面有专业符号时被当做字符匹配，这个正则会匹配a、b、-三个字符之一

排除字符组，在正则表达式字符组中使用`^`表示排除字符组，查找任何不再方括号之间的字符：

	[^abc] // 表示匹配一个不是a、b、c的任意字符

**多选分支（管道符）**

在正则表达式中，管道符`|`用于表示多选分支，支持多个模式任选其一进行匹配。

	/aaa|bbb/ // 表示匹配字符串aaa、bbb其中之一，与字符组的区别是匹配其中之一模式，而字符组是匹配其中之一字符

	/[ab]|[12]/ // 表示匹配[ab]、[12]字符组的其中之一，然后再匹配字符组中的其中一个字符

	/(aaa|bbb)ccc/ // 可以使用括号与分支匹配配合使用，表示部分使用分支匹配规则，其它使用另外的规则

分支匹配是惰性的，当前的匹配上了，后边的就不再尝试了。

	var reg = /good|goodbye/g;
	var reg2 = /goodbye|good/g; 
	var str = 'goodbye';

	str.match(reg); // ['good']
	str.match(reg2); // ['goodbye']


**量词**

量词就是重复，表示匹配字符重复的次数。

- `n{X}`，匹配包含X个n的序列的字符串；
- `n{X,}`，X是一个正整数。前面的模式n连续出现至少X次时匹配；
- `n{X,Y}`，X和Y为正整数。前面的模式n连续出现至少X次，至多Y次时匹配；
- `n+`，匹配任何包含至少一个n的字符串；
- `n*`，匹配任何包含零个或多个n的字符串；
- `n?`，匹配任何包含零个或一个n的字符串；


使用量词匹配时又有贪婪匹配和惰性匹配两种情况：

	var reg = /\d{2,5}/g;
	var str = '1234';

	str.match(reg); // ['1234']，默认贪婪模式，正则表达式会尽可能多的匹配

	var reg2 = /\d{2,5}/g; // ['12', '34']，在量词后边加上一个？符号，就是惰性匹配，即刚好满足条件的时候就不再向下继续匹配了

**锚**

位置（锚）是**相邻字符之间的位置**，用于匹配位置。

- `^`，匹配开头，在多行匹配中匹配行开头；
- `$`，匹配结尾，在多行匹配中匹配行结尾；
- `\b`，匹配单词边界；单词边界，具体就是\w与\W之间的位置，也包括\w与^之间的位置，和\w与$之间的位置；
- `\B`，匹配非单词边界；
- `(?=p)`，其中p是一个子模式（子模式可以是正则表达式），即p前面的位置，或者说，该位置后面的字符要匹配p；
- `(?!p)`，仍是p前面的位置，但位置后面的字符满足不匹配p；

可以将位置理解为空字符，而位置匹配匹配便是这些空字符（位置）。

	'hello' => '' + 'h' + '' + 'e' + '' + 'l' + '' + 'l' + '' + 'o' + ''

	var str = 'hello';
	str.replace(/^/g, '#'); // '#hello'

**分组（括号）**

在正则表达式中，括号提供了分组，便于我们引用它。括号捕获的分组引用有两种情形：在JavaScript 里引用它，在正则表达式里引用它。

- 分组，括号可以将正则的一部分分割成一组作为单独一个整体；

		var str = 'abbbab';
		var reg = /ab+/g; // 量词仅作用于单个字符
		var reg2 = /(ab)+/g; // 量词作用于括号中整体

		str.match(reg); // ['abbb', 'ab']
		str.match(reg2); // ['ab', 'ab']
	
- 分支结构，括号与管道符配合使用；

		var str = 'hello world, hi world';
		var reg = /(hello|hi) world/g;
		var reg2 = /hello|hi world/g;
		
		str.match(reg); // ['hello world', 'hi world']
		str.match(reg2); // ['hello', 'hi world']

在正则括号分组中，会给每一个分组都开辟一个空间，用来储存每一个分组匹配到的数据。当我们需要时。可以提取这些数据：

- `str.match(reg)`，字符串的match方法，返回一个数组，第一个元素是整体匹配结果，然后是各个分组（括号里）匹配的内容，然后此数组还有两个额外属性是匹配下标index，输入的文本input；

- `reg.exec(str)`，正则的exec方法，与字符串的match返回值一致；

- RegExp构造函数全局属性`$1-$9`，且基于最近一次正则表达式操作而改变；

		var reg = /(\d{4})-(\d{2})-(\d{2})/;
		var str = '2018-01-20';

		var arr = str.match(reg);
		arr.index; // 0
		arr.input; // '2018-01-20'
		
		// 下面两种方法都返回同样的结果，只是方法所属的对象不一样，分组获取的数据会保存在数组第一个元素之后
		str.match(reg); // ['2018-01-20, '2018', '01', '20']
		reg.exec(str); // ['2018-01-20, '2018', '01', '20']
		
		// 基于最近一次正则操作，例如调用了正则的方法或者字符串的正则方法之后，RegExp才会有保存了分组的全局属性
		RegExp.$1; // '2018'
		RegExp.$2; // '01'
		RegExp.$3; // '20'

		// 正则构造函数全局属性场用于替换，下面三种操作结果一致，皆为'01/20/2018'
		str.replace(reg, "$2/$3/$1");
		str.replace(reg, function () {
			return RegExp.$2 + "/" + RegExp.$3 + "/" + RegExp.$1;
		});
		str.replace(reg, function (match, year, month, day) {
			return month + "/" + day + "/" + year;
		});

- 正则中使用`\1`结构，`\ + 数字`可以在正则本身里引用分组。但只能引用之前出现的分组，即**反向引用**；

		var reg = /\d{4}(-|\/|\.)\d{2}\1\d{2}/; // 正则中使用了\1引用前面分组的结果，所以这有前后一致的情况下才会匹配成功
		var str = '2018-01-20';
		var str2 = '2018-01/20';

		reg.test(str); // true
		reg.test(str2); // false
		
		// 使用\ + 数字和$ + 数字引用分组，数组代表的分组，是以左括号为准，第一个左括号中内容就是1，依次向后推
		var reg = /^((\d)(\d(\d)))\1\2\3\4$/;
		var str = "1231231233";
		reg.test(str) ); // true
		RegExp.$1; // 123
		RegExp.$2; // 1
		RegExp.$3; // 23
		RegExp.$4; // 3

分组特殊情况：

- 引用了不存在的分组时，此时正则不会报错，只是匹配反向引用的字符本身；

- 分组后面有量词的话，分组最终捕获到的数据是最后一次的匹配；

		var reg = /(\d)+/;
		var str = '12345';

		str.match(reg); // ['12345', '5']数组中保存的分组是最后一次匹配
		
		var reg2 = /(\d)+ \1/;
		reg2.test('12345 1'); // false
		reg2.test('12345 5'); // true

括号还可以只分组不引用`(?:)`，这种结构的分组将不会出现在引用中。适用于后边不需要引用捕获组的情况，可以节约内存。

**上面所有的操作符优先级从高到低为：转义符（\） => 字符组和分组（括号和方括号） => 量词 => 位置（锚）、元字符、一般字符 => 管道符**
		
**回溯**

回溯法是正则表达式匹配字符串的原理，回溯法原理：回溯法也称试探法，它的基本思想是从问题的某一种状态（初始状态）出发，搜索从这种状态出发所能达到的所有“状态”，当一条路走到“尽头”的时候（不能再前进），再后退一步或若干步，从另一种可能“状态”出发，继续搜索，直到所有的“路径”（状态）都试探过。这种不断“前进”、不断“回溯”寻找解的方法，就称作“回溯法”。

**正则表达式操作方法**

- 正则表达式方法：

	- test，检索字符串中指定的值。返回 true 或 false；
	- exec，检索字符串中指定的值。返回找到的值的数组，并确定其位置；

- 字符串的正则表达式方法：

	- match，找到一个或多个正则表达式的匹配，同正则的exec方法结果一致；
	- search，检索与正则表达式相匹配的值；
	- replace，替换与正则表达式匹配的子串；
	- split，把字符串分割为字符串数组；


这篇笔记主要是基于老姚的《JavaScript 正则表达式迷你书》整理笔记，十分感谢老姚的分享，[原书地址](https://zhuanlan.zhihu.com/p/29707385)。


		

