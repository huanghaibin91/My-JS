# Ajax #


----------

Ajax（异步的JavaScript和XML），核心是`XMLHttpRequest`对象，在用户和服务器之间加了一个中间层(Ajax引擎)，使用户操作与服务器响应异步化，通过`XMLHttpRequest`对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。

**Ajax特点**

- 优点：

	 - 最大的一点是页面无刷新，在页面内与服务器通信，给用户的体验非常好；
     - 使用异步方式与服务器通信，不需要打断用户的操作，具有更加迅速的响应能力；
     - 可以把以前一些服务器负担的工作转嫁到客户端，利用客户端闲置的能力来处理，减轻服务器和带宽的负担，节约空间和宽带租用成本。并且减轻服务器的负担，ajax的原则是“按需取数据”，可以最大程度的减少冗余请求，和响应对服务器造成的负担；
     - 基于标准化的并被广泛支持的技术，不需要下载插件或者小程序；

- 缺点：

	- ajax干掉了back按钮，即对浏览器后退机制的破坏，因为Ajax是页面无刷新更新的内容，所以使用后退按钮并不是数据更新之前的页面；
    - ajax也难以避免一些已知的安全弱点，诸如跨站点脚步攻击、SQL注入攻击和基于credentials的安全漏洞等；
    - 对搜索引擎的支持比较弱；
    - 破坏了程序的异常机制，异步不好获取错误；
    - 违背了url和资源定位的初衷；

**Ajax过程**

Ajax过程：创建AJAX对象，发出HTTP请求，接收服务器传回的数据，更新网页数据，一旦拿到服务器返回的数据，AJAX不会刷新整个网页，而是只更新相关部分，从而不打断用户正在做的事情。

1. 创建Ajax：

		// 跨浏览器创建Ajax，IE7+就支持XMLHttpRequest对象
		// 使用惰性载入的方式，利用IFEE和return让变量声明时进行一次分支检测，以后使用便不再执行if分支检测，固定createXHR的值
		var createXHR = (function createXHR() {
            if (typeof XMLHttpRequest !== 'undefined') {
                return function () {
					return new XMLHttpRequest();
				}
            } else if (typeof ActiveXObject !== 'undefined') {
                return function () {
					if (typeof arguments.callee.activeXString !== 'string') {
                    	var versions = ['MSXML2.XMLHttp.6.0', 'MSXML2.XMLHttp.6.0', 'MSXML2.XMLHttp'];
                    	for (var i = 0, len = versions.length; i < len; i++) {
                        	try {
	                            new ActiveXObject(versions[i]);
	                            arguments.callee.activeXString = versions[i];
	                            break;
                        	} catch (ex) {
                            // 跳过
                        	}
                    	}
                	}
                	return new ActiveXObject(arguments.callee.activeXString);
				};
            } else {
				return function () {
					throw new Error('No XHR object available.');
				};
            }
        })();

2. 使用Ajax：

		// 创建Ajax对象		
		var xhr = createXHR();

		// 同步请求
        // 使用XHR对象时，调用的第一个方法是open()，接收三个参数，要发起请求的类型（'get','post'等），请求的URL和表示是否异步发送请求的布尔值，false表示同步
        xhr.open('get', 'example.php', false);
        // send()方法，接收一个参数，即要作为请求主体发送的数据，如果不需要通过请求主体发送数据，则必须传入null
        xhr.send(null);
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            console.log(xhr.responseText);
        } else {
            console.log('request was unsuccessful' + xhr.status);
        }

		// 异步请求
		xhr.open('get', 'example.php', true);
        xhr.send(null);
        // 异步有readyState属性，表示当前响应的活动阶段，4表示完成，readyState每次变化会触发readyStateChange事件
        // response属性为只读，返回接收到的数据体（即body部分）。它的类型可以是ArrayBuffer、Blob、Document、JSON对象、或者一个字符串，这由XMLHttpRequest.responseType属性的值决定。本次请求没有成功或者数据不完整，该属性就会等于null
        xhr.onreadyStateChange = function () {
            if (xhr.readyState == 4) {
                if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
                    // 处理返回值
                } else {
					// 返回数据失败
                    console.log('Request was unsuccessful' + xhr.status);
                }
            }
        }

**Ajax请求类型**

- `GET`，常用于向服务器查询某些信息，get请求HTML的responseText返回的是HTML的字符串，查询字符串中每个参数的名称和值都必须使用`encodeURIComponent()`进行编码向URL的末尾添加查询字符串参数；

		// 向URL的末尾添加查询字符串参数
        function addURLParam(url, name, value) {
            url += (url.index('?') == -1 ? '?' : '&');
            url += encodeURIComponent(name) + '=' + encodeURIComponent(value);
            return url;
        }

- `POST`，post请求，通常用于向服务器发送应该被保存的数据，通常用于表单提交；

**Ajax方法和属性**

- Ajax方法：

	- `.open(method, url, false/true)`，准备启动一个AJAX请求；
	- `.setRequestHeader()`，设置请求头部信息，必须在open()方法之后send()之前；
	- `.send()`，发送AJAX请求，send方法参数不能为空，即时不需要发送任何数据的GET请求，也需要在调用.send()方法时，向其传入null值；
	- `.getResponseHeader()`，获得响应头部信息；
	- `.getAllResponseHeader()`，获得一个包含所有头部信息的长字符串；
	- `.abort()`，取消异步请求；
	- `.overrideMineType()`，重写xhr响应返回数据的MIME类型，必须在send()方法之前调用；

- Ajax属性：

	- `.responseText`，包含响应主体返回文本；
	- `.responseType`，用来指定服务器返回数据（xhr.response）的类型：“”：字符串（默认值）；“aybuffer”：ArrayBuffer对象；“blob”：Blob对象；“document”：Document对象；“json”：JSON对象；“text”：字符串；
	- `.responseXML`，如果响应的内容类型时text/xml或application/xml，该属性将保存包含着相应数据的XML DOM文档；
	- `.status`，响应的HTTP状态；
		- 200, OK，访问正常，基本上，只有2xx和304的状态码，表示服务器返回是正常状态；
        - 301, Moved Permanently，永久移动；
        - 302, Move temporarily，暂时移动；
        - 304, Not Modified，未修改；
        - 307, Temporary Redirect，暂时重定向；
        - 401, Unauthorized，未授权；
        - 403, Forbidden，禁止访问；
        - 404, Not Found，未发现指定网址；
        - 500, Internal Server Error，服务器发生错误；
	- `.statusText`，HTTP状态的说明；
	- `.readyState`，表示“请求”/“响应”过程的当前活动阶段；
		- 0，未初始化，未调用open()方法；
		- 1，启动，调用open()方法，未调用send()方法；
		- 2，发送，调用send()方法，未收到响应；
		- 3，接收，已经收到部分响应数据；
		- 4，完成，已收到全部响应数据；
	- `.timeout`，请求等待响应多少毫秒之后终止请求；

- Ajax事件，事件绑定`xhr.onreadystatechange = fn`、`xhr.addEventListener('readystatechange', fn)`两种方式：

	- `readystatechange`，异步请求readyState属性改变触发；
	- `timeout`，在timeout属性规定的时间没有接收到响应触发；
	- 进度事件，进度事件是在Ajax响应各个阶段触发：
		- `loadStart`，接收响应数据第一个字节时触发；
		- `progress`，接收数据时不断触发，progress事件必须在open()方法之前绑定progress的事件event对象，event拥有几个属性：
			- `event.target`，指向XHR对象；
			- `event.lengthComputable`，布尔值，表示进度信息是否可用；
			- `position`，表示已经接收的字节数；
			- `totalSize`，表示根据Content-Length响应头部确定的预期字节数；
		- `error`，请求发生错误时触发；
		- `abort`，在因为调用abort()方法终止请求时调用；
		- `load`，接收完整响应数据时触发，类似readyState属性为4阶段；
		- `loadend`，通信完成或者触发error、abort和load事件后触发；

**Ajax上传文件**

Ajax除了能从服务器获取数据，还可以像服务器传递数据。

	// 利用formData模拟file控件。进行文件上传，IE9+
    function uploadFiles(url, files) {
        var formData = new FormData(); // 创建FormData实例，FormData对象，序列化表单，使用FormData不必明确设置头部，XHR对象能够识别数据类型是FormData的实例并配置适当的头部信息。FormData可以将表单字段的名称name和值value进行URL编码，使用和号（&）分割。可以直接将form元素传入FormData构造函数，或者使用append方法传入键值对，对应的键值对就已经添加到表单里面了，你在控制台看到的是FormData原型，存储的数据没有以对象属性的方式体现，可以理解为类的私有字段，外界访问不到，所以显示的是空对象，但是你可以通过formData.get(key)的方式获取到对应的表单数据
        for (var i = 0, file; file = files[i]; ++i) {
			// new FormData(formElement)
			// formData.append(key, value)
			// formData.get(key)
            formData.append(file.name, file); // 可加入第三个参数，表示文件名 
        }
        var xhr = new XMLHttpRequest();
        xhr.open('POST', url, true);
        xhr.onload = function (e) {
            // 文件创建成功后操作
        };
        xhr.send(formData); // multipart/form-data，直接将formData对象利用send方法传给服务器
    }

	
	// 直接使用file API上传文件
	function uploadFiles(url, files) {
		for (var i = 0, file; file = files[i]; ++i) {
			var xhr = new XMLHttpRequest();
    		xhr.open('POST', 'myserver/uploads');
    		xhr.setRequestHeader('Content-Type', file.type);
   			xhr.send(file);
		}
	}
	
	// 元素绑定事件触发上传
    document.querySelector('input[type="file"]').addEventListener('change', function (e) {
        uploadFiles('/server', this.files);
    }, false);

