
进程组件角色：

* Master 进程：监视工作进程的状态；当工作进程死掉后重启一个新的；处理信号和通知工作进程。

* Worker 进程：处理客户端请求，从主进程处获得信号做相应的事情。

* Cache Loader 进程：加载缓存索引文件信息，然后退出。

* Cache Manager进程：管理磁盘的缓存大小，超过预定值大小后最少使用数据将被删除


**Nginx 的框架模型如下图:**

<a href="https://ibb.co/3S04s22"><img src="https://i.ibb.co/mhHbBkk/nginx1.webp" alt="nginx1" border="0"></a>

**框架模型流程如下图：**

<a href="https://ibb.co/GpwX6yQ"><img src="https://i.ibb.co/DWXNJhf/nginx2.webp" alt="nginx2" border="0"></a>
