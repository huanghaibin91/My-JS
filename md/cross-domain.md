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
	
		// 以上面两个网址为例
		// 设置网址的document.domain，注意：document.domain的设置是有限制的，我们只能把document.domain设置成自身或更高一级的父域，且主域必须相同
		document.domain = 'example.com';

		// http://a.example.com/a.html设置cookie
		document.cookie = "a=a";

		// http://b.example.com/b.html将可以读取这个cookie
		console.log(document.cookie);

- 通过document.domain + iframe父窗口跨域操作子iframe中的DOM；

		


		



