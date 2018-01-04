# 跨域 #


----------

浏览器为了安全，不允许从其他域调用对象，这被成为“同源政策”，同源是指：协议相同、域名相同和端口相同。

同源政策会在跨域时限制一些操作，其限制之一是不能通过ajax的方法去请求不同源中的文档，第二个限制是浏览器中不同域的框架之间是不能进行js的交互操作的，具体表现在三个方面：

- Cookie、LocalStorage和 IndexedDB无法读取；

- DOM无法获得；

- AJAX请求无效（可以发送，但浏览器会拒绝接受响应）。

但有的时候，我们仍是需要进行跨域操作。

**document.domain跨域**

通过document.domain跨域只适用于不同子域的跨域情景
	
	// 下面两个子域不同
	http://a.example.com/a.html
	http://b.example.com/b.html

- 通过document.domain跨域访问cookie；   
	
		// http://a.example.com/a.html 网页中嵌套iframe页面，地址为http://b.example.com/b.html
		// 设置网址的document.domain，注意：document.domain的设置是有限制的，我们只能把document.domain设置成自身或更高一级的父域，且主域必须相同
		// 设置父窗口和子窗口的domain相同
		document.domain = 'example.com';

		// 父窗口http://a.example.com/a.html设置cookie
		document.cookie = "a=a";

		// 子窗口http://b.example.com/b.html将可以读取这个cookie
		console.log(document.cookie);

- 通过document.domain + iframe可以跨域操作DOM；

		// http://a.example.com/a.html 网页中嵌套iframe页面，地址为http://b.example.com/b.html
		// 设置父窗口和子窗口的domain相同
		document.domain = 'example.com';
		
		// 父窗口
		document.getElementById('my-iframe').contentWindow.document; // 可以跨域获取iframe里的document对象

**window.name跨域**

window对象有个name属性，该属性有个特征：即在一个窗口(window)的生命周期内,窗口载入的所有的页面都是共享一个window.name的，每个页面对window.name都有读写的权限，window.name是持久存在一个窗口载入过的所有页面中的，并不会因新页面的载入而进行重置。window.name是string类型。**window.name属性的神奇之处在于name 值在不同的页面（甚至不同域名）加载后依旧存在（如果没修改则值不会变化），并且可以支持非常长的 name 值（2MB**）

	// 假设我们有3个页面
	// a.com/index.html
	// a.com/empty.html
	// b.com/index.html
	
	// 1. 在a.com/index.html页面中嵌入一个iframe，设置src为b.com/index.html
	// 2. b.com/index.html载入后，设置window.name，然后再使用location.href='a.com/empty.html'跳转到父窗口页面同域的页面中，必须设置这一步，借用同域向父窗口传递信息
	// 3. 在a.com/index.html页面中，就可以通过document.getElementById('my-iframe').contentWindow.name来获取iframe内页面a.com/empty.html的window.name值了，而这个值正是b.com/index.html设置的

	// 注意： 实际操作中，我们一般使用一个隐藏的iframe，然后监听它第二次onload事件，就知道该iframe已经跳到同域页面了

	

**location.hash跨域**

location.hash可以解决页面与页面中iframe间通信。

location.hash就是URL的#号后面的部分，比如`http://a.example.com/a.html#fragment`的`#fragment`。如果只是改变location.hash，这部分的修改不会产生HTTP请求所以页面不会重新刷新，但是会产生浏览器历史记录。

	// http://a.example.com/a.html 网页中嵌套iframe页面，地址为http://b.example.com/b.html

	// 父窗口向子窗口传递信息
	// 父窗口代码
	var data = 'hello world';
	var url = 'http://b.example.com/b.html';
	var src = url + '#' + data; // data便是要传递的数据
	document.getElementById('my-iframe').src = src;
	
	// 子窗口代码，子窗口通过监听hashchange事件得到通知
	window.onhashchange = function () {
		console.log(window.location.hash); // 'hello world'，数据到达子窗口
	}

	// 子窗口向父窗口传递消息
	// 在子窗口中再创建一个隐藏的子iframe，此iframe的链接与父窗口同域
	try {  
    	parent.location.hash = 'data';  
	} catch (e) {  
		var url = 'ttp://a.example.com/proxy.html';
		var data = 'hello world'; 
	    // ie、chrome的安全机制无法修改parent.location.hash，创建一个代理iframe
	    var ifrproxy = document.createElement('iframe');  
	    ifrproxy.style.display = 'none';  
	    ifrproxy.src = url + '#' + data
	    document.body.appendChild(ifrproxy);  
	}

	// 代理proxy.html监听到url发生变化，修改a.html的url（因为a.html和proxy.html同域，所以proxy.html可修改a.html的url hash）

	// a.html监听到URL发生变化，触发相应操作
	

**H5跨文档通信 postMessage**

window.postMessage()方法实现页面与页面中iframe或通过window.open()方法打开的页面之间的通信。（IE8+）

window.postMessage()方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin）即“协议 + 域名 + 端口”。也可以设为*，表示不限制域名，向所有窗口发送。

	// http://a.example.com/a.html 网页中嵌套iframe页面，地址为http://b.example.com/b.html
	// 那么父窗口想向子窗口传递信息，父窗口代码：
	var iframeWindow = document.getElementById('my-iframe').contentWindow;
	iframeWindow.postMessage('hello world', 'http://b.example.com/b.html');

	// 父窗口发送消息后，iframe窗口通过message事件接收，message事件为异步触发，子窗口代码：
	window.addEventListener('message', function(e) {
  		console.log(e.data); // 'hello world'，postMessage第一个参数的数据
		console.log(e.origin); // 'http://a.example.com/a.html'，发送消息的文档所在的域
		console.log(e.source); // 发送消息的文档的window对象的代理，这里指向父窗口的window，可以利用此属性向来源窗口发送回执
		e.source.postMessage('hello world, too', 'http://a.example.com/a.html');
	},false);

还可以通过postMessage读取其他窗口的LocalStorage。

	// http://a.example.com/a.html 网页中嵌套iframe页面，地址为http://b.example.com/b.html
	// 那么父窗口想向子窗口传递信息，并将消息存在子窗口的localStorage中，父窗口代码：
	var iframeWindow = document.getElementById('my-iframe').contentWindow;
	var obj = { message: 'hello world' };
	// 将要传递的信息对象转化为字符串方便传输
	iframeWindow.postMessage(JSON.stringify({key: 'storage', data: obj}), 'http://b.example.com/b.html');

	// 父窗口发送消息后，iframe窗口通过message事件接收并存储，子窗口代码：
	window.addEventListener('message', function(e) {
  		var message = JSON.parse(e.data);
		// 通过传递的消息可以修改添加删除子窗口的localStorage
		localStorage.setItem(messgae.key, message.data.message);
	},false);

**JSONP**

我们可以看到使用script标签加载外部文件是可以跨域的（不仅如此，我们还发现凡有”src”这个属性的标签都拥有跨域的能力，比如`<script>、<img>、<iframe>`）。

JSONP，便是利用script的这个特性，该协议的一个要点就是允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据。

注意，JSONP的回调函数必须在window下添加。

这里是我封装的一个元素JS JSONP函数，[JS jsonp函数](https://github.com/huanghaibin91/JS-CSS-Fragment/blob/master/JS/jsonp.js)。

JSONP主要是用于解决Ajax跨域请求无效的情景。JSONP只能支持GET操作。

**CORS**

CORS，跨域资源共享，允许浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。CORS通信与同源的AJAX通信没有差别，代码完全一样。**因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。**服务器端对于CORS的支持，主要就是通过设置Access-`Control-Allow-Origin`来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问。

CORS，主要是用于解决Ajax跨域请求无效的情景。

**WebSocket**

WebSocket是一种通信协议，使用ws://（非加密）和wss://（加密）作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。

**服务器跨域**

- 通过搭建中间转发层；
- Nginx反向代理；









	

		


		



