title: 新技能get，用javascript快速写出html5端的多人聊天室应用（一）
date: 2015-04-26 20:31:14
tags: [javascript,html5,nodejs]
categories: code
---
##前言：##
在这篇文章以及，接下来的一篇文章中，我将给大家介绍一下神奇的javascript和nodejs，都说javascript是互联网时代的c语言，真的有这么强大？都说node的单线程同步很牛X，除了代码，所有的一切都是同步运行的，是逗我们玩的么？介绍完基础后，我将带着大家实现一个简单的多人聊天网页的编写，后端nodejs+前端bootstrap实现，非常简单。 

##好玩的javascript：##
说起javascript，大家的第一反应是浏览器。传统意义上,JavaScript 是由 ECMAScript，文档对象模型(DOM)和浏览器对象模型(BOM)组成的。具体的javascript诞生的历史就不细说了，[这篇文章](http://javascript.ruanyifeng.com/introduction/history.html )中有很详细的介绍。但是说了这么多，javascript到底有多强大，总得有一个例子来说明吧。给大家一个例子，一个叫Fabrice Bellard的程序员写了一段Javascript在Web浏览器中启动Linux，[网址](http://bellard.org/jslinux/)，目前，你只能使用Firefox 4和Chrome 11运行这个Linux。这不是什么恶作剧，这是实实在在的运行一个Linux。   
这本质上是用纯的javascrip些出了一个模拟器。在这位大神的[技术文档](http://bellard.org/jslinux/tech.html)中指出，CPU仿真器使用的是qemu，为了装上Linux，其做了一些改动。Linux  使用了2.6.20内核，还有Javascript的终端本来可以使用termlib，但他还是自己写了一个，因为OS的按键和Web浏览器不一样，具体的[说明文档](http://unixpapa.com/js/key.html)。从这件事中，可以看出，1.这才是真大神。2.linux优化真好。3.javascript好厉害，请问它在web上还有什么不能做么？还有谁～～    
 javascript绝对是现阶段最火的语言，证据就是这个月github上star增长最快的前20个项目中，javascript相关的项目占了7个，[链接在这](https://github.com/trending?since=monthly)。 
##改变世界的nodejs:##
从原理上说，Node.js 是一个让 JavaScript 运行在浏览器之外的平台。它实现了诸如文件系统、模块、包、操作系统 API、网络通信等 Core JavaScript 没有或者不完善的功能。历史上将 JavaScript移植到浏览器外的计划不止一个,但Node.js 是最出色的一个。顺便提一下，它的JavaScript解释器，采用了Google公司的V8引擎。具体的nodejs安装和入门可以看[这里](http://javascript.ruanyifeng.com/nodejs/basic.html)，没错，我是教程的搬运工～～  
现在我们来解决两个问题，1.为什么nodejs这么受关注，他和其他传统的多线程异步的方法比较，优势在哪？2.它能拿来做什么，或者说它适合做什么，不适合做什么？   
第一个问题，要知道nodejs最大的特点是异步式I/O与事件驱动，这有一张单线程同步+非阻塞io和多线程异步+阻塞io的对比图： 
![](http://203club.com/wp-content/uploads/2015/04/nodejs1.png)
传统的方式是多线程，为每个业务逻辑提供一个系统线程，通过系统线程切换来弥补同步时I/O调用时的时间开销。NodeJS使用的是单线程模型，对于所有I/O都采用异步式请求的方式，避免了上下文切换。在执行过程中维护着一个事件队列，程序在执行时进入事件循环等待下一个事件到来，每个异步式I/O请求完成后会被推送到事件队列，等待程序进入进行处理。看一段很简单的代码：
```
http.listen(3000, function(){
        console.log("server on 3000 start!");
});
```
首先http服务器监听端口3000，然后在后台打印出server on 3000 start！的字样。因为打印字符串是一个比较费时的i/o操作，不能等到i/o执行完成再往下，于是将采用异步i/o，在完成启动服务器的操作后，才异步唤起打印操作。NodeJS进程在同一时刻只会处理一个事件，完成后立即进入事件循环检查并处理后面的事件。CPU和内存在同一时间集中处理一件事，同时尽可能让耗时的I/O操作并行执行。只会在事件队列中添加请求等待操作系统的回应，因而不会有任何多线程开销，很大程度上可以提高Web应用的健壮性，防止恶意攻击。 
![](http://203club.com/wp-content/uploads/2015/04/nodejs2-300x204.png)
第二个问题，nodejs的优势在哪？第一，大家以前在做web后端的时候肯定装过各种apache，tomcat，ngix等，但node是很任性滴，它自带了服务器模块，纯c++实现，效率极高，代码只要这么几行： 
```
var http = require('http');
http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write('<h1>hello node</h1>');
    res.end();
}).listen(8080);
```
 第二，能支持数万个并发连接，Node.js 用异步式 I/O 和事件驱动代替多线程，带来了可观的性能提升。v8引擎加上高效的 libev 和 libeio 库，非常高效.    
nodejs缺点也比较明显，它不适合做高CPU消耗的应用，它的长处在高i/o，如果有频繁访问数据库的需求，那就使用它。还有一个缺点就是，太年轻～～不够成熟。这里有一篇介绍nodejs成也异步，败也异步的[文章](http://www.jiangmiao.org/blog/2491.html)，非常好的比较。 
##应用原理分析：##
让我们回归正题，对将要实现的web应用做一个简要的分析。在线聊天室应用，实际上是Realtime技术。这种技术要达到的目的是让用户不需要刷新浏览器就可以获得实时更新。它有着广泛的应用场景，比如在线聊天室、在线客服系统、评论系统、WebIM等。这是我实现的webIM网页的一个截图： 
![](http://203club.com/wp-content/uploads/2015/04/nodejs3-300x142.png)
首先第一个要介绍的是WebSocket，谈到Web实时推送，就不得不说WebSocket。在WebSocket出现之前，很多网站为了实现实时推送技术，通常采用的方案是轮询(Polling)和Comet技术，Comet又可细分为两种实现方式，一种是长轮询机制，一种称为流技术，这两种方式实际上是对轮询技术的改进，这些方案带来很明显的缺点，需要由浏览器对服务器发出HTTPrequest，大量消耗服务器带宽和资源。面对这种状况，HTML5定义了WebSocket协议，能更好的节省服务器资源和带宽并实现真正意义上的实时推送。      
WebSocket协议本质上是一个基于TCP的协议，它由通信协议和编程API组成，WebSocket能够在浏览器和服务器之间建立双向连接，以基于事件的方式，赋予浏览器实时通信能力。既然是双向通信，就意味着服务器端和客户端可以同时发送并响应请求，而不再像HTTP的请求和响应。但是由于浏览器兼容性的问题，特别是一些有害的浏览器如IE6（为什么有害？[请看](http://coolshell.cn/articles/1835.html)）等，不支持WebSocket技术，在它们这就只能用Comet来接收发送消息。 
##开源库：##
这里要使用Socket.IO来帮助我们。Socket.IO是一个开源的WebSocket库，它通过Node.js实现WebSocket服务端，同时也提供客户端JS库。Socket.IO支持以事件为基础的实时双向通讯，它可以工作在任何平台、浏览器或移动设备。Socket.IO支持4种协议：WebSocket、htmlfile、xhr-polling、jsonp-polling，它会自动根据浏览器选择适合的通讯方式，从而让开发者可以聚焦到功能的实现而不是平台的兼容性，同时Socket.IO具有不错的稳定性和性能。    
这里是socket.io的[文档手册页面](http://socket.io/docs/server-api/#)，在下一篇文章中会具体介绍本web应用将会甬道的api。 
##后记：##
在下一篇文章中将具体介绍技术实现，并附上代码。有人问我为什么不一块写完，还要写这么开头的废话。那是因为我高中的时候有一健身狂舍友，他给我的建议是：少量多组。。所以我就吸取教训，分开写。。