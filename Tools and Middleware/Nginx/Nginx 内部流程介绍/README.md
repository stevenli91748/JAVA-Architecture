
**Nginx 内部流程介绍**

# 1. Nginx 框架模型流程如下图：

<a href="https://ibb.co/RSdnTh5"><img src="https://i.ibb.co/MhQTk8X/nginx3.webp" alt="nginx3" border="0"></a>

# 2. Master初始化

<a href="https://ibb.co/SPwFxK2"><img src="https://i.ibb.co/PNGqY5R/nginx4.webp" alt="nginx4" border="0"></a>
<a href="https://ibb.co/VJ9vXFF"><img src="https://i.ibb.co/ck3NzWW/nginx5.webp" alt="nginx5" border="0"></a>

# 3. Worker 初始化

<a href="https://ibb.co/86byXRZ"><img src="https://i.ibb.co/18MCbpW/nginx6.webp" alt="nginx6" border="0"></a>
<a href="https://ibb.co/gyq7Dyd"><img src="https://i.ibb.co/PxXY5xM/nginx7.webp" alt="nginx7" border="0"></a>

# 4. 静态文件请求 IO 流程如下图

<a href="https://ibb.co/4F1CMWs"><img src="https://i.ibb.co/NFTfpx1/nginx8.webp" alt="nginx8" border="0"></a>

# 5. HTTP 请求流程如下图

<a href="https://ibb.co/BPybCQZ"><img src="https://i.ibb.co/0XCTG6M/nginx9.webp" alt="nginx9" border="0"></a>

**HTTP 请求 11 个阶段** 

<a href="https://ibb.co/sWmsnnc"><img src="https://i.ibb.co/xFCzWWw/nginx10.webp" alt="nginx10" border="0"></a>
