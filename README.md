# My-JS
JS知识笔记

----------
* HTML文本创建DOM树时，文本中的缩进或空格，可能被浏览器解释为包含空白符的文本节点。               element.firstChild可能就不是第一个子元素，而是一个空白的文本节点。
* 利用JS创建节点时，不会产生额外的空白文本节点。element.firstChild能准确获得元素的子节点。
* 使用删除节点时，提供给这个方法的的节点会立即从相应父节点删除。这也说明，所删除节点的所有引用，比如说在一个包含元素子节点的数组中，都必须更新。由于所指向的子节点不在存在，被删除的这个节点之后的所有子节点就必须在数组中上移。此时就得从后向前迭代，处理childNodes数组。
* 在JavaScript中，“==”为进行转换的等于符号，“===”为不转换的全等符号，“=”是赋值符号
* 如果要动态的取得输入的属性值，就不能在获取元素时直接取得当前的属性值，这会导致只有在刷新页面之后才会更新新的属性值，所以之前只能先取得元素
* 页面设计中，先不要设置页面高度，这样可以留足内容扩展后所需要的高度
* 4.取消a链接的跳转方法，三种JavaScript伪协议方法:
  * href="javascript:;"
  * href="javascript:void(0);"
  * href="javascript:void(null);"利用JavaScript伪协议来阻止链接窗口的弹出，但是不建议使用     
  * 伪协议，HTML中有href=”###”方法

* XMLHttpRequest
* 在Ajax中，可以向服务器发出请求而无须用户等待响应，称为异步请求。
* 同步请求，浏览器会等待服务器完成工作后，在服务器完成一个响应之前不会运行其他代码。利用XMLHttpRequest创建同步请求是不需要检查readyState和status值，因为同步请求只有在服务器成功完成响应后才会继续。
* 创建XMLHttpRequest
		
		function createRequest() {
    		try {
        		request = new XMLHttpRequest();
    		} catch (tryMS) {
        		try {
            		request = new ActiveXObject('Msxml2.XMLHTTP');
        		} catch (otherMS) {
            		try {
                		request = new ActiveXObject('Microsoft.XMLHTTP');
            		} catch (failed) {
                		request = null;
            		}
        		}
    		}
    		return request;
		}
*	创建请求
	
		function getDetails(itemName) {
    		request = createRequest();//创建请求对象
    		if (request == null) {//如果创建不成功，弹出一个错误并返回
        		alert('error');
        		return;
    		}
			//escape()函数会清理用户输入的内容，以防文本中有空格或问号等符号
    		var url = 'getDetails.php?ImageID=' + escape(itemName);
			//request.open()方法初始化链接
			//'GET'指示如何发送数据，另还有'POST'
			//url,这是对请求作出响应的服务器端脚本的URL
			//true表明请求是异步的，false则是同步
    		request.open('GET', url, true);
    		request.onreadystatechange = displayDetails;//这是函数引用不是调用，displayDetails函数后面不要加括号
			//send方法发送请求，null表示没有随请求发送额外的数据，null不可以省略
    		request.send(null);
		}
* 回调函数
		
		function displayDetails() {
    		if (request.readyState == 4) {//表示服务器处理完请求，数据可以使用
        		if (request.status == 200) {//服务器当前状态，200表示成功
            		detailDiv = document.getElementById('description');
            		detailDiv.innerHTML = request.responseText;
        		}
    		}
		}	
* 回调函数是另外某件事结束时执行的一个函数。在ajax中，回调函数就是服务器对一个请求对象作出响应时调用的函数。浏览器会在某个时刻“回调”这个函数。
* 请求对象的属性：
	* onreadystatechange，用于告诉浏览器当服务器响应一个请求时调用哪个函数。
	* readyState（表示请求对象当前状态的一个数，初始为0，服务器处理完一个请求是，这个属性的值将是4），这个属性指示请求已经完成，现在可以处理服务器返回的结果了。 
	* status（服务器返回的一个状态码，指示当前状态，例如指示成功或所请资源未找到，404代表禁止访问，200代表成功）。
	* responseXML（包含服务器发回的XML格式信息），除非服务器发回XML格式的信息，否则这个属性为空。
	* statusText（服务器返回的状态码消息），例如，“OK”对应状态202。
	* responseText（包含服务器发回的文本信息），除非服务器发回文本信息否则这个属性为空。
* 创建request时不用var关键字，创建的是全局变量是因为回调函数要求request为全局变量，这样回调函数才能访问这个变量和它的属性。
* Ajax引用XML,响应返回的XML用responseXML获得，操作取得的XML可以用DOM方法。
* Ajax引用JSON，响应返回的JSON文本用responseText获得，再利用eval解析文本成一个对象，eval（'(' + 获取的JSON文本 + ')'）;用括号将文本包裹有利于eval将文本当作一个整体解析，但是eval函数一般不建议使用。使用JSON.parse(获取的JSON文本);
* Ajax中的GET方法将数据作为请求URL的一部分发送给服务器。POST请求发送的数据与请求URL分离，使用POST时，request。send(要发送的数据)。
* 
* 
        window.onload = function(){
            var container = document.getElementById('container');
            var list = document.getElementById('list');
            var buttons = document.getElementById('buttons').getElementsByTagName('span');
            var prev = document.getElementById('prev');
            var next = document.getElementById('next');
            var index = 1;
            var timer;
            var animated = false;
 
            function animate(offset){
                animated = true;
                var time = 300;
                var interval = 10;
                var speed = offset/(time/interval);
                var newLeft = parseInt(list.style.left)+offset;
 
                function go(){
                    if((speed < 0 && parseInt(list.style.left) > newLeft) || (speed > 0 && parseInt(list.style.left) < newLeft)){
                        list.style.left = parseInt(list.style.left) + speed + "px";
                        setTimeout(go,interval);
                    }  
                    else{
                        animated = false;
                        list.style.left = newLeft + 'px';
                        if (newLeft >-600){
                            list.style.left = -3000 + 'px';
                        }
                        if (newLeft <-3000){
                            list.style.left = -600 + 'px';
                        }
                    }  
                }
                go();
            }
 
            function play(){
                timer = setInterval(function(){
                    next.onclick();
                },2000);
            }
 
            function stop(){
                clearInterval(timer);
            }
 
            function buttonsShow(){
                for(var i=0; i<buttons.length; i++){
                    if (buttons[i].className == "on"){
                        buttons[i].className = "";
                    }
                }
                buttons[index-1].className = "on";
            }
 
            prev.onclick = function(){
                index -=1;
                if (index < 1){
                    index = 5;
                }
                buttonsShow();
                if(!animated){
                    animate(600);
                }
            }
                 
 
            next.onclick = function(){
                index +=1;
                if(index > 5){
                    index =1;
                }
                 
                buttonsShow();
                if(!animated){
                    animate(-600);
                }
            }
 
            for(var i=0; i<buttons.length; i++){
                buttons[i].onclick = function(){
                    if(this.className == "on"){
                        return;
                    }
                    var clickIndex = parseInt(this.getAttribute('index'));
                    var offset = 600*(clickIndex - index);
                    animate(offset);
                    index = clickIndex;
                    buttonsShow();
                }
            }
            container.onmouseover = stop;
            container.onmouseout = play;
            play();
 
             
        }
    </script>
	> 之前写了一个轮播图，做完发现当我去浏览别的页面再返回时，轮播图会一直自动轮播，轮播的时间是我离开的时间，比如我离开了20s，内置2秒轮播一次，它就会快读轮播10次。而且我在他轮播时如果快速点击2次下一张图，他可能就会出现卡图的现象，就是两张图片各占了轮播图界面的一半。
	> 针对这个问题，优化步骤是在全局变量中声明一个变量。我用的是animated=false。在animate函数中设置animated=true。如果当轮播图到达最后的位置时，设置animated为false。并且在在切换图片时，在animate(600)和animate(-600)前加一句if(!animated)。
	> 解释：首先设置一个animated变量，当轮播图达到最后位置时，animated为false。所以当我快速点击2次下一张的时候，由于轮播图还没达到最后位置，所以animated为true。所以就算点击下一张，由于在animate(-600)前加一句if(!animated)，if(!animated)为false，所以并不会执行。
	> 而且当我去浏览别的页面时，由于我在它内置时间2s前离开，animated始终为true，它会数到2s，但是不会轮播，但是当你返回轮播图页面的时候，会迅速直接轮播下一张图片，但是这样的话就不会差很多。
* 窗口的位置和尺寸
	> 窗口位置尺寸
	> 
	> * 窗口相对于屏幕的位置，火狐`screenX`、`screenY`，其他浏览器`screenLeft`、`screenTop`
	> * 页面视图区的大小，不包括工具栏`innerWidth`、`innerHeigth`，浏览器窗口的大小`outerWidth`、`outerHeight`，DOM提供获取页面可区域的方法`document.documentElement.clientWidth`、`document.documentElement.clientheight`
	> * 滚动条位置`scrollLeft`、`scrollTop`
	> * 内容区宽高，包括被滚动条遮盖的部分`scrollWidth`、`scrollHeight`
	> 
	> 鼠标位置
	> 
	> * 鼠标在页面的位置，不随滚动条移动而变化`pageX`、`pageY`，鼠标在可视区域的位置，随滚动条移动而变化`clientX`、`clientY`
	> 
	> 元素位置尺寸
	> 
	> * 元素边框以内尺寸，除去外边距`offsetWidth`、`offsetHeight`
	> * 元素可见尺寸，除去外边距、变宽，`clientWidth`、`clientHeight`，如果有滚动条，还会再减去滚动条的宽度，内联元素的这两个属性为0
	> * 元素相对于父元素的位置`offsetLeft`、`offsetTop`
	> * 元素左、上边框大小`clientLeft`、`clientTop`