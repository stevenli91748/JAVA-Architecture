
Nginx 的模块化设计特点如下：

* 高度抽象的模块接口

* 模块接口非常简单，具有很高的灵活性

* 配置模块的设计

* 核心模块接口的简单化

* 多层次、多类别的模块设计

---

## 1. 内部核心模块
<a href="https://ibb.co/319H789"><img src="https://i.ibb.co/Q93Gfq3/nginx15.webp" alt="nginx15" border="0"></a>
<a href="https://ibb.co/DCrT5xd"><img src="https://i.ibb.co/J73g5fZ/nginx14.webp" alt="nginx14" border="0"></a>

## 2. Handler 模块：接受来自客户端的请求并构建响应头和响应体。

<a href="https://ibb.co/74Lg0nh"><img src="https://i.ibb.co/GTNsrWm/nginx16.webp" alt="nginx16" border="0"></a>

## 3. Filter 模块：过滤（filter）模块是过滤响应头和内容的模块，可以对回复的头和内容进行处理。它的处理时间在获取回复内容之后，向用户发送响应之前

<a href="https://ibb.co/xHwsCKY"><img src="https://i.ibb.co/PQJYmsM/nginx17.webp" alt="nginx17" border="0"></a>

## 4. Upstream 模块：使 Nginx 跨越单机的限制，完成网络数据的接收、处理和转发，纯异步的访问后端服务

<a href="https://ibb.co/PjrQCWn"><img src="https://i.ibb.co/02ntJ9S/nginx18.webp" alt="nginx18" border="0"></a>

## 5. Load_Balance：负载均衡模块，实现特定的算法，在众多的后端服务器中，选择一个服务器出来作为某个请求的转发服务器

<a href="https://ibb.co/X5F9BFQ"><img src="https://i.ibb.co/xgzyvzk/nginx119.webp" alt="nginx119" border="0"></a>

