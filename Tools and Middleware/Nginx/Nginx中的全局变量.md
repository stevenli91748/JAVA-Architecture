

我们已经发现Nginx是支持变量的，Nginx还内置了一些全局变量，这里列举一些比较重要的全局变量：

$content_length: 获取request中header部分的“Content_Length”值。
$content_type: 获取request中header部分的“Content_type”值。
$request_method: 请求方式，常用的有两种请求方式：POST、GET
$remote_addr: 发送请求的客户端ip
$remote_port: 发送请求的客户端端口
$request_uri: 含有参数的完整的初始URI
$server_addr: request到达的server的ip。
$server_port: 请求到达的服务器的端口号。
