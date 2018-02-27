# Web即时通信 #


----------

Web端与服务器即时通信的实现方式有短轮询、Comet、SSE和Websocket。

**短轮询**

短轮询即浏览器定时向服务器发送请求，询问是否有消息更新。最简单的实现就是使用定时器每个一段时间像服务器发送请求获取数据。

**Comet**

Comet的实现主要有两种方式，基于Ajax的长轮询（long-polling）方式和基于Iframe及htmlfile 的流（http streaming）方式。

- Ajax长轮询，需要客户端和服务端配合。客户端发起请求，如果有新数据就返回。如果没有，服务器就将请求挂起，当有新数据时再返回。基本过程是客户端发起请求后服务器阻塞请求直到有数据或者超时才返回，浏览器JS在处理请求返回信息（超时或有效数据）后再次发出请求，重新建立连接；

- iframe标记，通过在HTML页面里嵌入一个隐蔵帧，然后将这个隐蔵帧的SRC属性设为对一个长连接的请求，服务器端就能源源不断地往客户端输入数据；

		// iframe实现长轮询
        var iframeCon = docuemnt.querySelector('#container'), // iframe包裹元素
            data; //传递的信息
        var iframe = document.createElement('iframe');
            iframe.id = "frame";
            iframe.style = "display:none;"; // 隐藏iframe
            iframe.name = "polling";
            iframe.src = "target.html"; // iframe的链接URL
        iframeCon.appendChild(iframe);
        iframe.onload = function () { // iframe加载后触发回调函数
            var iloc = iframe.contentWindow.location, // iframe的window对象的location对象
                idoc = iframe.contentDocument; // iframe的document对象
            setTimeout(function () { // 定时器函数
                data = idoc.getElementsByTagName('body')[0].textContent; // 获取iframe文本数据
                console.log(text);
                iloc.reload(); //刷新页面,再次获取信息，并且会触发onload函数
            }, 2000);
        }

**SSE（服务器发送时间）**

SSE（Server-Sent Event，服务端推送事件）是一种允许服务端向客户端推送新数据的HTML5技术。SSE只能接受type/event-stream类型。
	
	// SSE前端代码
	// 创建EventSource对象
	var source = new EventSource('/dates');  //指定路由发送
	// 
	source.onmessage = function(e) {  //监听信息的传输
	    var data = JSON.parse(e.data),
	        origin = e.origin;
	};
	source.onerror = function(e) { //当连接发生error时触发
	    console.log(e);
	};
	source.onopen = function(e) { //当连接正式建立时触发
	    console.log(e);
	};

	// EventSource会保持与服务器的活动连接如果连接断开，还会重新连接。
	source.close() // close方法强制断开连接并且不再重新连接

**Websocket**

Websocket是一个全新的、独立的协议，基于TCP协议，与http协议兼容、却不会融入http协议，仅仅作为html5的一部分。目标是在一个单独的持久连接上提供双工、双向通信。

