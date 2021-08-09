# 以API 先行方法 驱动的方式开展项目开发的步骤

**API 先行方法图**
<a href="https://ibb.co/Cs8C3vj"><img src="https://i.ibb.co/6Jw97bK/0d6dc0d4de2602d64e0d0f1712042f92.jpg" alt="0d6dc0d4de2602d64e0d0f1712042f92" border="0"></a>

  1  前端和后端团队共同设计 API。将前端团队拉入设计这一理念，使得 API 开发人员可更好地获得用户体验。进而缩短功能实现的开发周期，因为开发人员不必去解决设计上的理解偏差。
  
  2  构建一组称为“Mock 后端”的样例服务，用于处理大多数的静态响应。
  
  3  后端和前端团队齐头并进地实现系统。其中，前端团队在开发中使用的是 Mock 后端；
  
  4  在后端完成后，再连接 API，完成集成测试。
  
  API 先行方法可大大降低项目的延迟和成本超支问题。这些问题主要源自于前端和后端团队之间的沟通不畅，从而导致 API 和后端系统变更。设计 API 之后，为便于前端团队进行 API 调用和测试，需要
  留一定的时间去启动和运行后端系统。为了解决此问题，前端团队可以设置为调用 Mock 后端的 Dummy 服务，去模仿所设计的 API，并返回 Dummy 数据。详细做法可参考: [API mock指南](https://stoplight.io/mock-api-guide/basics/)

**在软件工程中，façade 设计模式用于隐藏系统的复杂性，为用户提供更友好的接口。API façade 背后的理念同样如此，它向应用编程人员提供复杂后端系统的简化 API。前文所说的 API 先行方法，需设计的正是置于 API façade 之前的边缘 API。**

**下图展示了 API façade 是如何将企业系统中的各个部分黏合在一起**

**企业部署概览图：**
<a href="https://ibb.co/c3CyFy9"><img src="https://i.ibb.co/DW81C1F/d4c8ae8376805b04dccf227ee7fc8758.jpg" alt="d4c8ae8376805b04dccf227ee7fc8758" border="0"></a>

从图中看，我们针对组织和用户（包括移动端、Web 以及 API 的直接用户）具有不同的后端系统。需要组合**使用边缘 API 和 API façade 连接双方实体。**

**1. API façade 中包括简化的 RESTful API，用于开放后端系统的功能；**

**2. 边缘 API 组成 API 网关，提供 API 的全生命周期管理功能，包括限速、认证、授权等。**

**对 API façade 做进一步分析，可以看到它是由工具 API 和领域 API 组成的**

<a href="https://ibb.co/BN03GYw"><img src="https://i.ibb.co/tXVKz13/d85b97f1e5b61d673ff871113c83cd73.jpg" alt="d85b97f1e5b61d673ff871113c83cd73" border="0"></a>

