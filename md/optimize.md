# JS 优化 #


----------

- 尽可能减少首屏元素数量：用异步加载和本地缓存加载数据

- CDN加载慢时候，减少页面空白概率：页面CSS样式文件内联在页面上

- 减少页面请求数量：CSS/JS combo，CSS sprite

- 减少静态文件体积：CSS/JS/Images压缩

- DNS预解析：头部增加比如<link rel="dns-prefetch" href="//d.jd.com"/>

- 减小Cookie体积等等