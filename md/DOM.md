# DOM #


----------

DOM（文档对象模型），是针对HTML和XML文档的API。主要由元素节点，属性节点，文本节点组成。

**DOM节点共有属性和方法**

节点属性：

- `nodeType`, 元素节点1，属性节点2，文本节点3；
- `nodeName`，元素节点保存元素的标签名与原始节点的`tagName`相同，属性节点为属性名，文本节点为`#text`；
- `nodeValue`，元素节点为null，文本节点为文本内容，属性节点为属性值；
- `textContent`，回当前节点和它的所有后代节点的文本内容，会把其中的标签解释为文本；
- `childElementCount`，返回子元素的个数；

节点方法：

- `.contains()`, 接受一个节点作为参数，返回一个布尔值，表示参数节点是否为当前节点的后代节点；
- `.isEqualNode()`, 返回一个布尔值，用于检查两个节点是否相等。所谓相等的节点，指的是两个节点的类型相同、属性相同、子节点相同；
- `.isSameNode()`，返回一个布尔值，检查两个节点是否为同一个节点；
- `.cloneNode()`，接收一个布尔值参数，表示是否进行深复制，深复制会复制其子节点，不会复制节点事件；

**节点数结构**

- `parentNode`,父节点，对于一个节点来说，它的父节点只可能是三种类型：element节点、document节点和documentFragment节点；
- `childNodes`，所有子节点，基于DOM结构动态查询的结果，因此DOM结构的变化能够自动反映在Nodelist对象中；
- `children`，所有子元素节点，`childElementCount`子元素个数；
- `firstChild`，第一个子节点，`firstElementChild`第一个元素子节点,如果当前节点没有子节点，则返回null；
- `lastChild`，最后一个子节点, `lastElementChild`最后一个元素子节点；
- `nextSibling`，下一个兄弟节点，`nextElementSibling`下一个元素兄弟节点；
- `previousSibing`，上一个兄弟节点，`previousElementSibing`上一个元素兄弟节点；


**选取节点**

- `document.querySelector()`、`document.querySelectorAll()`，接收一个CSS选择符，并获取匹配元素,返回的是一个静态集合。DOM内部的变化，并不会实时反映在该方法的返回结果之中。
- `document.getElementsByName()`，返回有指定name的元素，常用情况是取得单选按钮，name相同的单选按钮只有选中那个会被获取；
- `document.getElementById()`；
- `document.getElementByClassName()`；
- `document.getElementByTagName()`；
- `document.activeElement()`，DOM中当前获得焦点的元素；
- `document.documentElement，`取得html；
- `document.body`，取得body；

**操作节点**

- `parentNode.appendChild(newNode)`，在父节点末尾添加一个节点，如果目标节点已经是文档的一部分了，那结果就是将该节点从原来的的位置移动到新位置；
- `parentNode.insertBefore(newNode, targetNode)`，在父节点的某个子节点之前添加一个节点，目标位置参数null则跟appendChild()一样，如果所要插入的节点是当前DOM现有的节点，则该节点将从原有的位置移除，插入新的位置；
- `parentNode.replaceChild(newNode, oldNode)`，替换一个子节点；
- `parentNode.removeChild(node)`，移除一个子节点；
- `document.createElement()`，创建元素节点；
- `document.createTextNode()`，创建文本节点；
- `document.creatDocumentFragment()`，利用DocumentFragment作为仓库先储存元素，最后再一起插入目标元素，避免浏览器多次渲染；

**Document文档节点**

Document，是整个文档树的顶层节点，也是window对象的一个属性。

document节点有不同的办法可以获取：

- 对于正常的网页，直接使用`document`或`window.document`；
- 对于iframe载入的网页，使用iframe节点的`contentDocument`属性；
- 对Ajax操作返回的文档，使用XMLHttpRequest对象的`responseXML`属性；
- 对于包含某个节点的文档，使用该节点的`ownerDocument`属性；

document节点属性：

- `document.doctype`，doctype标签（比如`<!DOCTYPE html>`）；
- `document.documentElement`，返回HTML元素；
- `document.defaultView`属性，在浏览器中返回document对象所在的window对象，否则返回null；
- `document.documentURI`（所有文档），`document.URL`（HTML文档）表示当前文档的网址；
- `document.location`，属性返回location对象，提供了当前文档的URL信息；
- `document.anchors`，集合返回了当前页面带有`name`的锚链接数组，不建议使用；
- `document.links`，取得所有带href特性的a元素；
- `document.title`，获取文档标题，也可以修改文档标题；
- `document.URL`，获取页面完整URL，只读；
- `document.referrer`，保存着链接到当前页面的那个页面的URL，只读，如果当前文档不是通过超级链接访问的，则为 null；
- `document.domain`，返回当前页面域名，可写，当页面存在在其它域的子域，设置domain可以使父子间通信；
- `document.forms`，取得所有表单元素；
- `document.images`，取得所有图像元素；
- `document.embeds`，属性返回网页中所有嵌入对象，即embed标签；
- `document.readyState`，有两个值loading和complete，用来实现一个指示文档已经加载完成的指示器浏览器开始解析HTML文档，`document.readyState`属性等于loading。浏览器遇到HTML文档中的<script>元素，并且没有async或defer属性，就暂停解析，开始执行脚本，这时`document.readyState`属性还是等于loading。HTML文档解析完成，`document.readyState`属性变成interactive。浏览器等待图片、样式表、字体文件等外部资源加载完成，一旦全部加载完成，`document.readyState`属性变成complete；

document节点方法

- `document.write()`、`document.writeIn()`，将内容写入网页，writeIn会在每次调用换行，输入内容可以是文字或HTML；
- `document.open()`、`document.close()`，打开或关闭网页；
- `document.hasFocus()`，确定文档是否获取了焦点；

**元素节点**

Element元素节点。

- 元素节点属性：
	- `.id`；
	- `.tagName`；
	- `.className`；
	- `.title`；
	- `.classList`类名集合，以下是它的方法，IE10+：
		- `.add()`，添加类名；
	    - `.contains()`，是否有类名；
	    - `.remove()`，移除类名；
	    - `.toggle()`，检查是否有类名，若类名列表中有此类名，移除之，并返回false; 如果没有，则添加该类名，并返回true；
	 - 元素尺寸,以下属性都是只读属性，不可赋值，offset的定位依据最近的一个具有大小的祖先元素，包含元素保存在offsetParent，不一定与parentNode相同，因为offsetParent是要能够设置大小的元素：
        - `.offsetHeight`，元素垂直空间高度，包括边框与滚动条；
        - `.offsetWidth`，元素水平空间高度，包括边框与滚动条；
        - `.offsetTop`，元素上外边框与与包含元素上内边框的距离；
        - `.offsetLeft`，元素左外边框与与包含元素左内边框的距离；
        - `.clientHeight`，元素内容及其内边距所占高度，不包含滚动条与边框；
        - `.clientWidth`，元素内容及其内边距所占宽度，不包含滚动条与边框滚动大小；
        - `.scrollHeight`，在没有滚动条的情况下，元素内容的总高度；
        - `.scrollWidth`，在没有滚动条的情况下，元素内容的总高度；
        - `.scrollTop`，内容区域上方滚动的像素，可以设置改变滚动位置；
        - `.scrollLeft`，内容区域左侧滚动的像素，可以设置改变滚动位置；

	- 元素的属性对象，`ele.attributes`包含元素所有属性的对象，这个对象包含的是一个属性节点集合，每一个属性都是一个属性节点，操作得按节点操作，适合遍历属性节点时使用：
		- `.getNamedItem(name)`，返回name的属性节点；
		- `.removeNamedItem(name)`，移除name的属性节点；
		- `.setNamedItem(node)`，添加属性节点；
		- `.item(pos)`，返回pos位置的属性节点；

	- `.dataset`属性访问元素自定义属性：

			// HTML使用data-*自定义属性，注意HTML不区分大小写，所以属性名使用小写
			<div id="div" data-a="a" data-b="b"></div>

			// JS
			var odiv = document.getElementById("div");
			odiv.dataset.a; // a
			odiv.dataset.a = 'A'; // 设置自定义属性的值
			delete odiv.dataset.b; // 删除自定义属性

- 元素节点方法：
	- `.hasChildNodes()`，方法检测元素是否有子节点；
	- `.innerHTML()`，读模式属性返回与调用元素的所有子节点对应的HTML标记，写模式会被解析为DOM子树，替换调用元素所有的子节点，但是通过innerHTML插入script无效；
	- `.outerHTML()`，读模式属性返回与调用元素及其所有子节点对应的HTML标记，写模式会被解析为DOM子树，替换调用元素及其所有的子节点；
	- `.innerText`,读模式可以操作元素中包含的所有文本，包括子文档树中的文本，写模式下会替换掉元素的所有子节点，使元素只有一个子文本节点；
	- `.outerText()`，读模式下与innerText一致，写模式下会用文本节点替换掉元素及其子节点；
	- `.getBoundingClientRect()`，获取元素相对于视口的位置，返回一个矩形对象，有top、right、bottom和left是个属性；
	- `.scrollIntoView()`，参数默认true，页面（或容器）发生滚动，使element的顶部与视图（容器）顶部对齐；参数为false，使element的底部与视图（容器）底部对齐。应用场景，聊天新信息到了直接滚动到底部显示；
	- `ele1.contains(ele2)`，ele1是否是ele2的祖先节点；
   
**文本节点**

- `ele.remove()`，移除文本节点；
- `ele.splitText(target)`，从target位置分割文本节点，一个文本节点分成两个，参数是文本位置；
- `ele.normalize()`，合并文本节点normailize方法用于清理当前节点内部的所有Text节点。它会去除空的文本节点，并且将毗邻的文本节点合并成一个；

- XML操作文本节点方法，JS也可以用，需在文本节点上操作：
	- `appendData(str)`，把str放置在文本节点末尾；
	- `deleteData(target, length)`，从target位置删除长度length的文本；
	- `insertData(target, str)`，将str从target位置插入
	- `replaceData(target, length, str)`，用str替换target位置之后length长度的文本；
	- `subStringData(target, length)`，从target位置截取length长度的文本；

**属性节点**

操作元素节点：`ele.getAttribute()`，`ele.setAttribute()`，`ele.hasAttribute()`，`ele.removeAttribute()`，`ele.hasAttributes()`如果元素有任何属性返回true，否则返回false。

属性节点node属性：nodeName特性的名称，nodeValue特性的值，parentNode值为null。

`ele.attributes`，返回一个类似数组的动态对象，成员是该元素标签的所有属性节点对象，属性节点对象有name和value属性。

**DOM操作CSS**

- `ele.style`，元素的style特性，包含元素HTML标签中的style样式，不包含外部链接样式，所以通过style设置样式，设置的是内联样式。设置外部样式可以通过设置类名或id连接外部样式表。 style对象的以下三个方法，用来读写行内CSS规则：`setProperty(propertyName, propertyValue, priority)`，`getPropertyValue(propertyName)`，`removeProperty(propertyName)`，三个方法的第一个参数，都是CSS属性名，且不用改写连词线。style对象的`CSSText`属性可以读取或重写整个style特性的值；

- 计算样式，元素所有样式，`getComputedStyle`，`currentStyle`(IE中)，计算样式都是只读的。返回的CSS值都是绝对单位，比如，长度都是像素单位（返回值包括px后缀），颜色是rgb(#, #, #)或rgba(#, #, #, #)格式。CSS规则的简写形式无效，比如，想读取margin属性的值，不能直接读，只能读marginLeft、marginTop等属性。如果一个元素不是绝对定位，top和left属性总是返回auto。该方法返回的样式对象的cssText属性无效，返回undefined。该方法返回的样式对象是只读的，如果想设置样式，应该使用元素节点的style属性：
	
		// 两个参数是计算样式的元素和一个伪元素字符串，如":after"，不需要伪元素则写null，会返回一个包含所有计算属性的对象
		// 有可能不会显示综合属性，就是几个CSS属性简写在一起的，所以查询的时候写详细的属性
		var computedStyle = document.defaultView.getComputedStyle(node, null); 
		var computedStyle = node.currentStyle;
        
