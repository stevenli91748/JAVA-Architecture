⼀⽂读懂 session 和 cookie
学算法，认准 labuladong 就够了！
cookie ⼤家应该都熟悉，⽐如说登录某些⽹站⼀段时间后，就要求你重新登
录；再⽐如有的同学很喜欢玩爬⾍技术，有时候⽹站就是可以拦截住你的爬
⾍，这些都和 cookie 有关。如果你明⽩了服务器后端对于 cookie 和 session
的处理逻辑，就可以解释这些现象，甚⾄钻⼀些空⼦⽆限⽩嫖，待我慢慢道
来。

⼀、session 和 cookie 简介
cookie 的出现是因为 HTTP 是⽆状态的⼀种协议，换句话说，服务器记不住
你，可能你每刷新⼀次⽹⻚，就要重新输⼊⼀次账号密码进⾏登录。这显然
是让⼈⽆法接受的，cookie 的作⽤就好⽐服务器给你贴个标签，然后你每次
向服务器再发请求时，服务器就能够 cookie 认出你。
抽象地概括⼀下：⼀个 cookie 可以认为是⼀个「变量」，形如
name=value ，存储在浏览器；⼀个 session 可以理解为⼀种数据结构，多数
情况是「映射」（键值对），存储在服务器上。
注意，我说的是「⼀个」cookie 可以认为是⼀个变量，但是服务器可以⼀次
设置多个 cookie，所以有时候说 cookie 是「⼀组」键值对⼉，这也可以说
得通。
cookie 可以在服务器端通过 HTTP 的 SetCookie 字段设置 cookie，⽐如我⽤
Go 语⾔写的⼀个简单服务：
func cookie(w http.ResponseWriter, r *http.Request) {
// 设置了两个 cookie
http.SetCookie(w, &http.Cookie{
Name: "name1",
Value: "value1",
})
⼀⽂看懂 session 和 cookie
535
http.SetCookie(w, &http.Cookie{
Name: "name2",
Value: "value2",
})
// 将字符串写⼊⽹⻚
fmt.Fprintln(w, "⻚⾯内容")
}
当浏览器访问对应⽹址时，通过浏览器的开发者⼯具查看此次 HTTP 通信的
细节，可以看⻅服务器的回应发出了两次 SetCookie 命令：
在这之后，浏览器的请求中的 Cookie 字段就带上了这两个 cookie：
cookie 的作⽤其实就是这么简单，⽆⾮就是服务器给每个客户端（浏览器）
打的标签，⽅便服务器辨认⽽已。当然，HTTP 还有很多参数可以设置
cookie，⽐如过期时间，或者让某个 cookie 只有某个特定路径才能使⽤等
等。
⼀⽂看懂 session 和 cookie
536
但问题是，我们也知道现在的很多⽹站功能很复杂，⽽且涉及很多的数据交
互，⽐如说电商⽹站的购物⻋功能，信息量⼤，⽽且结构也⽐较复杂，⽆法
通过简单的 cookie 机制传递这么多信息，⽽且要知道 cookie 字段是存储在
HTTP header 中的，就算能够承载这些信息，也会消耗很多的带宽，⽐较消
耗⽹络资源。
session 就可以配合 cookie 解决这⼀问题，⽐如说⼀个 cookie 存储这样⼀个
变量 sessionID=xxxx ，仅仅把这⼀个 cookie 传给服务器，然后服务器通过
这个 ID 找到对应的 session，这个 session 是⼀个数据结构，⾥⾯存储着该
⽤户的购物⻋等详细信息，服务器可以通过这些信息返回该⽤户的定制化⽹
⻚，有效解决了追踪⽤户的问题。
session 是⼀个数据结构，由⽹站的开发者设计，所以可以承载各种数据，
只要客户端的 cookie 传来⼀个唯⼀的 session ID，服务器就可以找到对应的
session，认出这个客户。
当然，由于 session 存储在服务器中，肯定会消耗服务器的资源，所以
session ⼀般都会有⼀个过期时间，服务器⼀般会定期检查并删除过期的
session，如果后来该⽤户再次访问服务器，可能就会⾯临重新登录等等措
施，然后服务器新建⼀个 session，将 session ID 通过 cookie 的形式传送给客
户端。
那么，我们知道 cookie 和 session 的原理，有什么切实的好处呢？除了应对
⾯试，我给你说⼀个鸡贼的⽤处，就是可以⽩嫖某些服务。
有些⽹站，你第⼀次使⽤它的服务，它直接免费让你试⽤，但是⽤⼀次之
后，就让你登录然后付费继续使⽤该服务。⽽且你发现⽹站似乎通过某些⼿
段记住了你的电脑，除⾮你换个电脑或者换个浏览器才能再⽩嫖⼀次。
那么问题来了，你试⽤的时候没有登录，⽹站服务器是怎么记住你的呢？这
就很显然了，服务器⼀定是给你的浏览器打了 cookie，后台建⽴了对应的
session 记录你的状态。你的浏览器在每次访问该⽹站的时候都会听话地带
着 cookie，服务器⼀查 session 就知道这个浏览器已经免费使⽤过了，得让
它登录付费，不能让它继续⽩嫖了。
⼀⽂看懂 session 和 cookie
537
那如果我不让浏览器发送 cookie，每次都伪装成⼀个第⼀次来试⽤的⼩萌
新，不就可以不断⽩嫖了么？浏览器会把⽹站的 cookie 以⽂件的形式存在
某些地⽅（不同的浏览器配置不同），你把他们找到然后删除就⾏了。但是
对于 Firefox 和 Chrome 浏览器，有很多插件可以直接编辑 cookie，⽐如我
的 Chrome 浏览器就⽤的⼀款叫做 EditThisCookie 的插件，这是他们官⽹：
这类插件可以读取浏览器在当前⽹⻚的 cookie，点开插件可以任意编辑和删
除 cookie。当然，偶尔⽩嫖⼀两次还⾏，不⿎励⾼频率⽩嫖，想常⽤还是掏
钱吧，否则⽹站赚不到钱，就只能取消免费试⽤这个机制了。
以上就是关于 cookie 和 session 的简单介绍，cookie 是 HTTP 协议的⼀部
分，不算复杂，⽽ session 是可以定制的，所以下⾯详细看⼀下实现 session
管理的代码架构吧。
⼆、session 的实现
session 的原理不难，但是具体实现它可是很有技巧的，⼀般需要三个组件
配合完成，它们分别是 Manager 、 Provider 和 Session 三个类（接
⼝）。
⼀⽂看懂 session 和 cookie
538
1、浏览器通过 HTTP 协议向服务器请求路径 /content 的⽹⻚资源，对应
路径上有⼀个 Handler 函数接收请求，解析 HTTP header 中的 cookie，得到
其中存储的 sessionID，然后把这个 ID 发给 Manager 。
2、 Manager 充当⼀个 session 管理器的⾓⾊，主要存储⼀些配置信息，⽐
如 session 的存活时间，cookie 的名字等等。⽽所有的 session 存在 Manager
内部的⼀个 Provider 中。所以 Manager 会把 sid （sessionID）传递给
Provider ，让它去找这个 ID 对应的具体是哪个 session。
3、 Provider 就是⼀个容器，最常⻅的应该就是⼀个散列表，将每个 sid
和对应的 session ⼀⼀映射起来。收到 Manager 传递的 sid 之后，它就找
到 sid 对应的 session 结构，也就是 Session 结构，然后返回它。
4、 Session 中存储着⽤户的具体信息，由 Handler 函数中的逻辑拿出这些
信息，⽣成该⽤户的 HTML ⽹⻚，返回给客户端。
那么你也许会问，为什么搞这么⿇烦，直接在 Handler 函数中搞⼀个哈希
表，然后存储 sid 和 Session 结构的映射不就完事⼉了？
这就是设计层⾯的技巧了，下⾯就来说说，为什么分成
Manager 、 Provider 和 Session 。
⼀⽂看懂 session 和 cookie
539
先从最底层的 Session 说。既然 session 就是键值对，为啥不直接⽤哈希
表，⽽是要抽象出这么⼀个数据结构呢？
第⼀，因为 Session 结构可能不⽌存储了⼀个哈希表，还可以存储⼀些辅
助数据，⽐如 sid ，访问次数，过期时间或者最后⼀次的访问时间，这样
便于实现想 LRU、LFU 这样的算法。
第⼆，因为 session 可以有不同的存储⽅式。如果⽤编程语⾔内置的哈希
表，那么 session 数据就是存储在内存中，如果数据量⼤，很容易造成程序
崩溃，⽽且⼀旦程序结束，所有 session 数据都会丢失。所以可以有很多种
session 的存储⽅式，⽐如存⼊缓存数据库 Redis，或者存⼊ MySQL 等等。
因此， Session 结构提供⼀层抽象，屏蔽不同存储⽅式的差异，只要提供
⼀组通⽤接⼝操纵键值对：
type Session interface {
// 设置键值对
Set(key, val interface{})
// 获取 key 对应的值
Get(key interface{}) interface{}
// 删除键 key
Delete(key interface{})
}
再说 Provider 为啥要抽象出来。我们上⾯那个图的 Provider 就是⼀个散
列表，保存 sid 到 Session 的映射，但是实际中肯定会更加复杂。我们
不是要时不时删除⼀些 session 吗，除了设置存活时间之外，还可以采⽤⼀
些其他策略，⽐如 LRU 缓存淘汰算法，这样就需要 Provider 内部使⽤哈
希链表这种数据结构来存储 session。
PS：关于 LRU 算法的奥妙，参⻅前⽂「LRU 算法详解」。
因此， Provider 作为⼀个容器，就是要屏蔽算法细节，以合理的数据结构
和算法组织 sid 和 Session 的映射关系，只需要实现下⾯这⼏个⽅法实
现对 session 的增删查改：
⼀⽂看懂 session 和 cookie
540
type Provider interface {
// 新增并返回⼀个 session
SessionCreate(sid string) (Session, error)
// 删除⼀个 session
SessionDestroy(sid string)
// 查找⼀个 session
SessionRead(sid string) (Session, error)
// 修改⼀个session
SessionUpdate(sid string)
// 通过类似 LRU 的算法回收过期的 session
SessionGC(maxLifeTime int64)
}
最后说 Manager ，⼤部分具体⼯作都委托给 Session 和 Provider 承担
了， Manager 主要就是⼀个参数集合，⽐如 session 的存活时间，清理过期
session 的策略，以及 session 的可⽤存储⽅式。 Manager 屏蔽了操作的具体
细节，我们可以通过 Manager 灵活地配置 session 机制。
综上，session 机制分成⼏部分的最主要原因就是解耦，实现定制化。我在
Github 上看过⼏个 Go 语⾔实现的 session 服务，源码都很简单，有兴趣的
朋友可以学习学习：
https://github.com/alexedwards/scs
https://github.com/astaxie/build-web-application-with-golang
