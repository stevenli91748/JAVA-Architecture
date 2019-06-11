
目录
---

#   JDK 源码阅读
#   [JAVA SE 8 API](https://docs.oracle.com/javase/8/docs/api/)

#   [JAVA EE 6 API](https://docs.oracle.com/javaee/6/api/)

#   [JAVA  Communication API](https://docs.oracle.com/cd/E17802_01/products/products/javacomm/reference/api/javax/comm/package-summary.html)




# 有用的参考


[JavaTM Platform Standard Edition 6 API 规范](http://www.cjsdn.net/doc/jdk60/index.html?overview-summary.html)

[从JDK源码看String(上)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484623&idx=1&sn=5bc4c40498d62e778ab045438096c67b&chksm=a69da9f191ea20e74d1b747eacce3f04e2b3b5f6265ec1a2bb2a2a7d0f6c38a118569277289f&scene=21#wechat_redirect)

[从JDK源码看String(下)](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484627&idx=1&sn=2afa783ef1626d7f6b7000a5cdce7a8a&chksm=a69da9ed91ea20fbeb030e79a7d556d5dfb80dd234cfc9f44332c0091c349321c79047923295&scene=21#wechat_redirect)

[从JDK源码看StringBuffer](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484508&idx=1&sn=d53dd7af2806a4c2aa50e124b2eb5c37&chksm=a69da96291ea20748569bc795ee8780775af677d0748e840ccfafcbfc8aa5b4fc2683b47ce96&scene=21#wechat_redirect)

[从JDK源码看StringBuilder](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484489&idx=1&sn=d1eb0f0efb4b3999c0e132b16aeb285d&chksm=a69da97791ea2061c0646044009836f8eb2008209af58eb6be1eff0bba20bc2a362ba303a027&scene=21#wechat_redirect)

[从JDK源码看Java域名解析](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484339&idx=1&sn=c7ac4ca4a567e78c6f9b246955a9864e&chksm=a69dae8d91ea279bd0bfd237a23303881c7d3c49a91e4bca1c5d6b809b90d65a5bdab9235bbe&scene=21#wechat_redirect)





[从JDK源码角度看Short](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483926&idx=1&sn=f768b06a9979c363f758a43fb92534bb&chksm=a69daf2891ea263e832bca66c0f8ae8bd7350b8b8629306a19fd56bb0bb80fe24c2d696692e7&scene=21#wechat_redirect)

[从JDK源码角度看Integer](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483937&idx=1&sn=f8d891ec753e33cf5b11f531ae3dc857&chksm=a69daf1f91ea260906d99263f9dc2783c29ea521de456a0b98b9ec46a4b62ed9a3535f7c5cb4&scene=21#wechat_redirect)

[从JDK源码角度看Long](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483967&idx=1&sn=057417264f8ac07dad035bc1d66ac4a8&chksm=a69daf0191ea26177934386dcf21407a70fd6d69e8717b526c8baa3a73e5c3fdda9cfe5c6a71&scene=21#wechat_redirect)

[从JDK源码角度看Long](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483967&idx=1&sn=057417264f8ac07dad035bc1d66ac4a8&chksm=a69daf0191ea26177934386dcf21407a70fd6d69e8717b526c8baa3a73e5c3fdda9cfe5c6a71&scene=21#wechat_redirect)



[从 JDK 源码角度看 Boolean](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483887&idx=1&sn=bf7a77740aa817034b8abeaf2fecc236&chksm=a69dacd191ea25c7aba00d0355c6a4021a8bb1b371e57b3561c9b593c39a3145eec5240c08b2&scene=21#wechat_redirect)

[从JDK源码角度看Object](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483894&idx=1&sn=f469d097db8666f9d439f14a48dadcd9&chksm=a69dacc891ea25deb0ac29276b88ef38e910fd81d6d3e781cd13eb621e91a00d04509cbe8dea&scene=21#wechat_redirect)

[从JDK源码角度看Float]()



[从JDK源码看System.exit](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247483992&idx=1&sn=65e072ed84780e05e551c147908c15b8&chksm=a69daf6691ea2670a6dfb7f4a015a2d64678aa1b17fe6194845a252387af8502cc091fd31cfc&scene=21#wechat_redirect)

[从JDK源码看InputStream](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484022&idx=1&sn=e585747b49387b29a64561c7a0e181d1&chksm=a69daf4891ea265e671608fe5315859ad4bcd92e456b2cb1a3633c6b5bc4d680f48e2eb974d2&scene=21#wechat_redirect)

[从JDK源码看OutputStream](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484118&idx=1&sn=748538031e6e7832c99d6919cfe69d2b&chksm=a69dafe891ea26fe4b657692cee95b8adbd076b0cf2f6d2a0b4e6d8e4e379bd1647d2383bd44&scene=21#wechat_redirect)

[从JDK源码看Writer](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484010&idx=1&sn=72709a17e5ce9a1b23e00322d2ea2c28&chksm=a69daf5491ea26425d92307b03debb6d573e5a4351c54270272b2df2b0882e77511f485124c5&scene=21#wechat_redirect)

[从JDK源码看Reader](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484126&idx=1&sn=10e13aa9f1e62092244aaab522e0a092&chksm=a69dafe091ea26f6f33c98572266391a7924fdbba6a96553edaabcc8ae90af86763285eec1f7&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（Windows）上篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484030&idx=1&sn=92edb2e331437c5969eda5cbc5afe245&chksm=a69daf4091ea26564b984b2a652944e78cae1f402e6b03fba94c8eaa2b9c8db1502bed5b6631&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（Windows）中篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484037&idx=1&sn=a06f1a5172a73693579cbbe292eb37e2&chksm=a69dafbb91ea26ada4ec6739a82fc7d34c269fb0674d2b2ea71349fd27aae503bd0d969f8392&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（Windows）下篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484090&idx=1&sn=2729438dd97c562ac50565b7b0befba0&chksm=a69daf8491ea2692e25494acdbcd3a77e57aaec1330a296fb3191c1e7e757d4ac58def120f6a&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（unix-like）上篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484105&idx=1&sn=ac5e932b1b0ec20cdf1f0c1be545d91a&chksm=a69daff791ea26e1f6ecb4995c866da7ee24baff3d56c31bb4fd46dde709bf21141ce1665b35&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（unix-like）中篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484108&idx=1&sn=0fbd9fd31b713c2dd8317084f9969727&chksm=a69daff291ea26e48ac8bd21ea40b68f90c4c9fe7300fef63644e9b83dbda79b0921b08038c4&scene=21#wechat_redirect)

[JDK不同操作系统的FileSystem（unix-like）下篇](https://mp.weixin.qq.com/s?__biz=MjM5MzA1Mzc3Nw==&mid=2247484111&idx=1&sn=06bc82b2575221bf66634f30af05bc58&chksm=a69daff191ea26e724a800110e9f8e571a88e0c1de7bad4d54f0ac66e3a7062626a2779d3490&scene=21#wechat_redirect)
