**我们发送请求通过负载均衡软件，负载均衡发到微服务网关上，网关进行用户的认证，解析出用户的基本信息，通过认证后，携带用户本身的标识到后台微服务，微服务获得标识后进行相关的鉴权，这样就能形成一个完整的权限链路**

**我们都知道网关适合做认证和鉴权，但在安全层面，我们要求更严格的权限，对于有些项目而言，本身网络跟外部隔离，加上其他的安全手段，所以只要求在网关层做认证和鉴权就行，对于后台服务之间的调用，不需要做微服务之间权限的控制。但某些时候我们还是需要对服务与服务之间的调用进行鉴权，知道某个用户是否有权限调用某个接口，这些都要进行鉴权**

# 目录
* [Spring Cloud认证鉴权实战案例1 ---采用前后端分离的架构，我们利用Spring Cloud Gateway实现用户的认证，解析JWT后传递用户信息到后端服务，后端服务根据用户和路由信息进行鉴权](https://weread.qq.com/web/reader/71d32370716443e271df020k5423294025b54229abfc040)
* [Spring Cloud认证鉴权实战案例2---Zuul+OAuth2.0+JWT实战](https://weread.qq.com/web/reader/71d32370716443e271df020k64232b60230642e92efb54c)
* 认证与鉴权方案
  * 单体应用下的常用方案---我们在传统的单体应用中，一般会写一个固定的认证和鉴权的包，里面包含了很多认证和鉴权的类，当用户发起请求时我们可以利用session的方式，把用户存入session并生成一个sessionId，之后返回客户端。客户端可以存在cookie里，从而在后续的请求中顺利通过验证
  * JWT常用方案---JWT和传统的Cookie/Session会话管理相比较有着多方面的优势，因为Cookie/Session需要在服务器Session存用户信息，然后拿客户端Cookie存储的SessionId获取用户信息，这个过程需要消耗
                 服务器的内存和对客户端的要求比较严格（需支持Cookie），而JWT最大的特性在于就是无状态、去中心化，所以JWT更适用分布式的场景，不需要在多台服务器做会话同步这种消耗服务器性能的操作。
                 另外JWT和Redis+Token这两种会话管理小伙伴们看项目情况选择，别有用了JWT还使用Redis存储的，因为你这种做法对JWT来说就是“伤害不大,但侮辱性极强”的做法，就当着它的面说我就看不上你
                 的最自以为是的“无状态”特性
  * 微服务下SSO单点登录方案
    * [分布式Session方案](https://weread.qq.com/web/reader/71d32370716443e271df020k861322a025a8613985ec87a)---业界很多项目都使用了这样的方案,很多场景下，这种方案是推荐的，因为很方便同时可以做扩展，也可以保证高可用的方案。但是这种方案的缺点是依赖于第三方部件，且这些部件需要做高可用，并且需要增加安全的控制，所以对于实现起来有一定复杂度
    * [客户端Token与网关结合方案](https://weread.qq.com/web/reader/71d32370716443e271df020k861322a025a8613985ec87a)
    * [浏览器Cookie与网关结合方案](https://weread.qq.com/web/reader/71d32370716443e271df020k861322a025a8613985ec87a)
    * [网关与Token和微服务之间鉴权结合](https://weread.qq.com/web/reader/71d32370716443e271df020k861322a025a8613985ec87a)
* [OAuth](https://weread.qq.com/web/reader/0e03209071c96a420e02710k7f632b502707f6ffaa6bf2e)
* [OAuth 2](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Security/Oauth2.md)
* [JWT](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Security/JWT.md)
* [Spring Security](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Security/README.md)
* [Spring Cloud Security](https://github.com/stevenli91748/JAVA-Architecture/blob/master/JAVA%20Framework/Spring%20Cloud/Spring%20Cloud%20Security/README.md)
