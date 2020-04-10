## 1.什么是Cookie？
HTTP 协议（即超文本传输协议）是一个无状态的协议，浏览器和web 服务器会通过HTTP协议进行数据的发送和接收。这就造成一个问题：服务器无法分辨多次请求是否来自于同一个浏览器，无法在服务器端确认用户的状态。为了解决这个问题，我们需要使用Cookie，Cookie是服务器发送请求响应到浏览器并保存在本地的一块小的数据，当浏览器再次对服务器发送请求的时候，请求就会携带Cookie的值被发送给服务器，服务器根据cookie的值来判断两次请求是否来自于同一个浏览器。

Cookie的主要用途：  <br>
管理会话状态（如用户的登录状态、购物车、游戏分数、历史消息等其他需要保存的信息）<br>
个性化设置<br>
浏览器行为跟踪<br>

## 2.什么是 Session？

Session是服务器和浏览器进行一次会话的过程，Session存储着会话所需的某些属性和配置信息。这样，用户在应用程序的web前端页面之间跳转的时候，保存在Session中的变量不会丢失，而是在整个会话过程中一直保存下去，直到会话结束或Session过期。

Session机制原理：<br>
浏览器向服务器发送创建一个Session的请求，服务器会首先检查请求中是否包含一个用于标识Session的字段SessionID。<br>
如果请求中存在SessionID，说明服务器已经为浏览器创建了一个Session会话，服务器会根据SessionID的值来检索Session并响应给浏览器。<br>
如果请求中不存在SessionID，说明服务器没有为浏览器创建一个Session会话，服务器会创建一个Session会话并生成一个SessionID与之关联。<br>

Session的生命周期：<br>
Session的生命周期是指Servlet容器创建Session对象到销毁的过程，Servlet容器会根据Session对象设置的有效时间来管理Session，当达到Session有效时间就销毁Session对象。Session对象生成以后，浏览器每访问一次服务器，都会更新一次Session对象的最后访问时间，便于服务器维护Session。

## 3.Cookie和Session区别<br>
1、保存位置不同：Cookie保存在浏览器中，Session保存在服务器中。<br>
2、存取方式不同：Cookie只能保存ASCII码，Session可以保存任何类型的数据信息。<br>
3、有效期不同：Cookie可以设置为长时间保存（例如网站保存用户名密码等），Session一般有效时间短，一次会话结束，Session失效。<br>
4、隐私策略不同：Cookie存储在浏览器中，容易被窃取数据信息。Session存储在服务器中，较Cookie安全。<br>
5、存储大小不同：单个Cookie保存的数据大小不能超过4KB，Session可以存储的数据量要远远超过Cookie。<br>
## 4.Cookie和Session的关联
![图片](https://github.com/zhuqianqian1996/CS-Master-Note/blob/master/%E5%9B%BE%E7%89%87/CookieAndSession.bmp)

浏览器第一次请求服务器的时候，服务器会根据所提交的请求信息来创建对应的Session对象，请求返回的时候由服务器创建一个Cookie将Session对象的SessionID返回给浏览器。浏览器接收到服务器返回的SessionID信息之后，会将该信息保存在Cookie中，并记录该SessionID信息属于哪个域名。<br>

当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。<br>

## 5.浏览器禁用Cookie的解决方法

我们知道服务器根据Cookie中的信息来判断用户的状态，如果浏览器禁用了Cookie又该如何处理呢？

第一种方法：每次请求都携带一个SessionID的信息，可以通过POST请求发送，也可以在GET请求的请求地址后面拼接参数（例如：www.baidu.com?SessionID=123456789)。

第二种方法：token机制，当浏览器第一次对服务器发送请求的时候，服务器根据请求所提交的信息创建一个Token作为标识浏览器的标志返回给浏览器，以后每次浏览器再向服务器发送请求的时候都携带Token，服务器通过Token的值来判断浏览器是否发送过请求。

## 6.分布式系统中Session共享解决策略

分布式系统中的Session共享一般有三种策略：

第一种：粘性Session（我也将它称为ip_hash策略）：使用Nginx代理，每个请求按照服务器的ip地址进行分配，这样同一个ip地址的服务器创建的Session对象会被再次发送到该服务器。

第二种：复制Session：将Session对象复制到每一台服务器上，任何服务器的Session对象发生变化，内容都会被序列化广播给其他的服务器（确保每次Session发生变化的时候所有服务器的Session都同步更新）。

第三种：共享Session：服务器使用缓存中间件来将Session集中统一管理，保障分发到每一个服务器的响应结果都一致。
