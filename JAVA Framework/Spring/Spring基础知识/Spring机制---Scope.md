


bean的scope是指bean的作用域，默认情况下是单例模式，这也是使用最多的一种方式；多例模式，即每次从beanFactory中获取bean都会创建一个新的bean。

request、session、global-session是在web服务中使用的scope，request每次请求都创建一个实例，session是在一个会话周期内保证只有一个实例。

global-session在5.x版本中已经不在使用，同时增加了Application和Websocket两种scope，分别保证在一个ServletContext与一个WebSocket中只创建一个实例。


Singleton|Prototype|Request|Session|Global-session|Application|Websocket|
---|---|---|---|---|---|---|
