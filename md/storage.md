# 数据存储 #


----------

**Cookie**

HTTP是一个不保存状态的协议，也就说同个客户端即使多次向同个服务器发出请求，服务器也做出响应了，但服务器却不知道是否是同一个客户端发出的请求。而cookie可以保存一段信息，在请求时发给服务器让无状态的HTTP协议也可以记录状态信息。

![cookie原理]()

第一次访问网站的时候，浏览器发出请求，服务器响应请求后，会将cookie放入到响应请求中，这样会在客户端设置一个cookie。当浏览器第二次发请求的时候，会把这个cookie带给服务器，服务端会解析cookie辨别用户身份，当然服务器也可以修改cookie内容。

- 客户端设置cookie时，cookie为一个字符串，cookie各个属性之间以`;`分开，cookie属性：

	- `name`，唯一一个cookie名称，cookie不区分大小写，必须经过URL编码；
	- `value`，存储在cookie中的字符串值，必须被URL编码；
	- `domain`，cookie绑定的域名，如果没有设置，就会自动绑定到执行语句的当前域；
	- `path`，cookie绑定域名的路由，只有满足路由条件才会发送cookie，path默认值为设置该cookie的网页所在的目录；
	- `expires`，cookie有效期，格式满足GMT时间格式，失效的cookie会被删除，当设置的失效日期是一个当前时间以前的时间或为设置，此cookie会在浏览器关闭之后删除；
	
		> expires是http/1.0协议中的选项，在新的http/1.1协议中expires已经由max-age选项代替，两者的作用都是限制cookie的有效时间。expires的值是一个时间点（cookie失效时刻 = expires），而max-age的值是一个以秒为单位时间段（cookie失效时刻 = 创建时刻 + max-age）。另外，max-age的默认值是 -1（即有效期为session）；
		> 
		> max-age有三种可能值：负数、0、正数。负数：有效期session；0：删除cookie；正数：有效期为创建时刻 + max-age。
		>
	- `secure`，属性设置为true时，此cookie只会在https和ssl等安全协议下才会发送给服务器；
	- `HttpOnly`，属性设置为true时，就不能通过js脚本来获取cookie的值，能有效的防止xss击；

			document.cookie = "name=value; expires=GMT time; domain=domain.com; path=/; secure; HttpOnly"; // secure、HttpOnly设置即为true，不设置就是false

- cookie特点：

	- cookie不可跨域；
	- 每个域的cookie总数是有限的；
	- 单个cookie长度限制在4K（4095B）以内；
	- 当name/domain/path 这3个字段都相同的时候设置cookie会覆盖已设置cookie；
	- cookie不能直接删除，需要使用相同的属性再次设置此cookie，并将其失效时间设置为过去时间，便会在浏览器关闭时删除cookie；

- JS操作cookie，`document.cookie`（这个方法只能获取非HttpOnly类型的cookie）：[封装的cookie操作]()

在服务器端，还有一个与cookie相似的在服务器端使用的一种记录客户端状态的机制session，cookie与session有以下区别：

- cookie数据存放在客户的浏览器上，session数据放在服务器上；
- cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗考虑到安全应当使用 session；
- session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用cookie；
- 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie；

鉴于上述区别我们建议：将登陆信息等重要信息存放为session，其他信息如果需要保留，可以放在cookie中。

**Storage**

Web Storage提供客户端的信息存储，storage类型的属性：

- storage类型的属性：

	- `getItem(name)`，获取name对应的值；
	- `key(index)`，获取index位置的键名；
	- `removeItem(name)`，删除name指定的名值对；
	- `setItem(name, value)`，为那么设置对应的值；
	- `length`，名值对长度；
	- `clear()`，删除所有值；

- storage类型特点：

	- localStorage每个来源有5M的大小限制，sessionStorage也有大小限制；
	- storage类型只能存储字符串，如果想存储对象，可以将数据先调用`JSON.stringify()`将对象转换为字符串，使用时再使用`JSON.parse()`将数据解析为对象；

- `sessionStorage`，会话存储，该数据只保持到浏览器关闭，sessionStorage比localStorage更严苛一点，除了协议、主机名、端口外，还要求在同一窗口（也就是浏览器的标签页）下才可访问；
- `localStorage`，IE8+，浏览器持久保存数据的存储对象，localStorage的访问域默认设定为设置localStorage的当前域，需满足同源策略才能访问同一个localStorage；

**IndexDB**

浏览器保存结构化数据的数据库。indexedDB的操作都是异步的。

**HTML标签data属性**

`data-*` 属性用于存储私有页面后应用的自定义数据。data-* 属性可以在所有的 HTML 元素中嵌入数据。自定义的数据可以让页面拥有更好的交互体验（不需要使用 Ajax 或去服务端查询数据）。data-* 属性由以下两部分组成：属性名不要包含大写字母，在 data- 后必须至少有一个字符。该属性可以是任何字符串。

	<!-- data-*有两种设置方式，可以直接在HTML元素标签上书写 -->
    <p id="p1" data-mydata="mydata">Hello World</p>
	<!-- 使用JS设置data属性 -->
    <p id="p2">Hello World</p>

	// HTML5中元素都会有一个dataset的属性，这是一个DOMStringMap类型的键值对集合，可以设置读取data属性
    var p2 = document.getElementById('p2');
    p2.dataset.yourdata = 'yourdata';
    // 删掉一个 data-属性 ，可以这么做： delete el.dataset.id ;  或者 el.dataset.id = null









