# Day 13：Route 53 + ACM + HTTPS + ALB

今天把 Day 12 的：

```text
http://xxxx.elb.amazonaws.com
```

升级成真正企业应用的：

```text
https://api.yourdomain.com
```

架构：

```text
User
 │
 ▼
HTTPS :443
 │
 ▼
Route 53
 │
 ▼
ALB
 │
 ▼
ECS Fargate :8080
 │
 ▼
RDS
```

---

# 1. 今天完成

```text
[1] 注册/准备域名
[2] Route 53 Hosted Zone
[3] ACM SSL Certificate
[4] DNS Validation
[5] ALB HTTPS :443
[6] HTTP → HTTPS Redirect
[7] Route 53 Alias
[8] 测试 HTTPS
```

---

# 2. 推荐域名结构

不要让前端和 API 共用一个地址。

建议：

```text
yourdomain.com
```

前端：

```text
https://app.yourdomain.com
```

API：

```text
https://api.yourdomain.com
```

以后：

```text
api.yourdomain.com
```

负责：

```text
Spring Boot
```

而：

```text
app.yourdomain.com
```

负责：

```text
React
```

---

# 3. Route 53

进入：

[Amazon Route 53 Console](https://console.aws.amazon.com/route53/?utm_source=chatgpt.com)

选择：

```text
Hosted zones
 → Create hosted zone
```

输入：

```text
yourdomain.com
```

Type：

```text
Public hosted zone
```

创建。

---

# 4. 如果域名不是在 Route 53 买的

例如你在：

```text
Cloudflare
GoDaddy
Namecheap
```

购买域名也可以。

只需要把域名 DNS 的 Name Servers 改成 Route 53 提供的：

```text
ns-xxxx.awsdns-xx.org
ns-xxxx.awsdns-xx.com
ns-xxxx.awsdns-xx.net
ns-xxxx.awsdns-xx.co.uk
```

具体以你 Hosted Zone 显示的 NS 为准。

---

# 5. 创建 API 域名

假设：

```text
yourdomain.com
```

我们使用：

```text
api.yourdomain.com
```

注意：

**下面的 `yourdomain.com` 换成你的真实域名。**

---

# 6. ACM SSL Certificate

进入：

[AWS Certificate Manager Console](https://console.aws.amazon.com/acm/?utm_source=chatgpt.com)

### 非常重要

如果 ALB 在：

```text
us-west-2
```

ACM Certificate 也必须在：

```text
us-west-2
```

不要在：

```text
us-east-1
```

创建给 ALB 使用的证书。

---

# 7. 创建 Certificate

选择：

```text
Request
 → Request a public certificate
```

Domain：

```text
api.yourdomain.com
```

如果以后还需要：

```text
app.yourdomain.com
```

可以申请：

```text
*.yourdomain.com
```

但第一阶段建议：

```text
api.yourdomain.com
```

简单明确。

---

# 8. Validation

选择：

```text
DNS validation
```

点击：

```text
Request
```

状态：

```text
Pending validation
```

---

# 9. DNS Validation

ACM 会给你一个 CNAME。

类似：

```text
Name:
_xxxxxxxxx.api.yourdomain.com

Value:
_xxxxxxxxx.acm-validations.aws.
```

进入：

```text
Route 53
 → Hosted Zone
 → yourdomain.com
```

创建 CNAME。

如果 ACM 页面出现：

```text
Create records in Route 53
```

直接选择它最方便。

等待：

```text
Issued
```

---

# 10. 为什么 ACM 免费

AWS 公共 ACM Certificate 用于 AWS 集成服务，例如 ALB：

```text
ACM
 ↓
ALB
```

通常不需要单独购买 SSL Certificate。

这也是 AWS 企业架构常见方式。

---

# 11. 修改 ALB

进入：

```text
EC2
 → Load Balancers
 → java-ai-alb
```

找到：

```text
Listeners
```

现在应该只有：

```text
HTTP :80
```

---

# 12. 创建 HTTPS Listener

点击：

```text
Add listener
```

选择：

```text
Protocol:
HTTPS

Port:
443
```

Security Group：

```text
java-ai-alb-sg
```

确认 Inbound：

```text
HTTPS
443
0.0.0.0/0
```

---

# 13. SSL Certificate

Certificate：

```text
From ACM
```

选择：

```text
api.yourdomain.com
```

然后：

```text
Default action:
Forward to

java-ai-tg
```

保存。

现在：

```text
Internet
   │
 HTTPS 443
   ▼
 ALB
   │
   ▼
ECS :8080
```

---

# 14. HTTP 自动跳转 HTTPS

现在 HTTP：

```text
80
```

不要再直接 Forward。

修改 Listener：

```text
HTTP :80
```

Action：

```text
Redirect to HTTPS
```

设置：

```text
Protocol:
HTTPS

Port:
443

Status:
HTTP_301
```

于是：

```text
http://api.yourdomain.com
              │
              ▼
https://api.yourdomain.com
```

---

# 15. Route 53 Alias

进入：

```text
Route 53
 → Hosted zones
 → yourdomain.com
```

创建：

```text
Record
```

Name：

```text
api
```

Type：

```text
A
```

打开：

```text
Alias
```

Route traffic to：

```text
Application Load Balancer
```

选择：

```text
java-ai-alb
```

创建。

---

# 16. DNS 最终结构

```text
yourdomain.com
│
├── app
│     ↓
│   CloudFront
│
└── api
      ↓
     ALB
      ↓
     ECS
```

也就是：

```text
app.yourdomain.com
```

和：

```text
api.yourdomain.com
```

分开。

---

# 17. 测试 DNS

Terminal：

```bash
nslookup api.yourdomain.com
```

或者：

```bash
dig api.yourdomain.com
```

DNS 生效后应该能解析到 ALB。

---

# 18. 测试 HTTPS

```bash
curl https://api.yourdomain.com/actuator/health
```

返回：

```json
{
  "status": "UP"
}
```

说明：

```text
DNS
 ↓
ACM
 ↓
HTTPS
 ↓
ALB
 ↓
ECS
```

全部成功。

---

# 19. 测试 HTTP Redirect

```bash
curl -I http://api.yourdomain.com/actuator/health
```

应该看到：

```text
HTTP/1.1 301
Location: https://api.yourdomain.com/actuator/health
```

这一步很重要。

生产环境不要让用户继续使用 HTTP。

---

# 20. React API 地址

Day 4：

```env
VITE_API_BASE_URL=http://localhost:8080
```

现在：

```env
VITE_API_BASE_URL=https://api.yourdomain.com
```

然后：

```bash
npm run build
```

---

# 21. CORS 也要修改

之前：

```java
.allowedOrigins(
    "http://localhost:5173"
)
```

现在前端以后会：

```text
https://app.yourdomain.com
```

所以：

```java
.allowedOrigins(
    "https://app.yourdomain.com"
)
```

不要：

```java
.allowedOrigins("*")
```

生产环境不要这样做。

---

# 22. 如果暂时没有前端域名

可以先：

```java
.allowedOrigins(
    "http://localhost:5173"
)
```

开发环境保留。

生产：

```java
.allowedOrigins(
    "https://app.yourdomain.com"
)
```

更好的方式是通过环境变量：

```yaml
app:
  cors:
    allowed-origins: ${CORS_ALLOWED_ORIGINS}
```

然后 ECS：

```text
CORS_ALLOWED_ORIGINS
=
https://app.yourdomain.com
```

---

# 23. Spring Boot 配置

推荐：

```yaml
app:
  cors:
    allowed-origins:
      ${CORS_ALLOWED_ORIGINS}
```

Java：

```java
@Configuration
public class CorsConfig {

    @Value("${app.cors.allowed-origins}")
    private String allowedOrigins;

    @Bean
    public WebMvcConfigurer corsConfigurer() {

        return new WebMvcConfigurer() {

            @Override
            public void addCorsMappings(
                    CorsRegistry registry) {

                registry
                    .addMapping("/api/**")
                    .allowedOrigins(
                        allowedOrigins
                    )
                    .allowedMethods(
                        "GET",
                        "POST",
                        "PUT",
                        "DELETE",
                        "OPTIONS"
                    )
                    .allowedHeaders("*");
            }
        };
    }
}
```

---

# 24. Security Headers

今天顺便把一些基本安全 Header 加进去。

Spring Security：

```java
http.headers(headers ->
    headers
        .contentSecurityPolicy(
            csp -> csp.policyDirectives(
                "default-src 'self'"
            )
        )
        .frameOptions(
            frame -> frame.deny()
        )
);
```

注意：

CSP 要根据 React、Swagger、CloudFront 等实际资源逐步调整，**不要直接把一个过严的 CSP 放进生产导致前端功能全部失效**。

---

# 25. HSTS

HTTPS 稳定以后可以启用：

```java
http.headers(headers ->
    headers.httpStrictTransportSecurity(
        hsts -> hsts
            .includeSubDomains(true)
            .maxAgeInSeconds(31536000)
    )
);
```

HSTS 的作用：

```text
Browser
 ↓
强制 HTTPS
```

但建议等 HTTPS 已经稳定后再启用。

---

# 26. 今天的完整网络

现在：

```text
                     Internet
                        │
                        ▼
               api.yourdomain.com
                        │
                      HTTPS
                        │
                        ▼
                     Route 53
                        │
                        ▼
                       ALB
                    :443 / :80
                        │
                        ▼
                 ECS Fargate
                     :8080
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
         Spring      Spring AI     RAG
         Security       │           │
             │          ▼           ▼
             │       Bedrock       RDS
             │                     │
             └─────────────────────┘
```

---

# 27. 今天的安全边界

现在：

```text
Internet
   │
   │ 443
   ▼
 ALB
```

ALB：

```text
80  → Redirect
443 → ECS
```

ECS：

```text
8080
仅允许
ALB Security Group
```

RDS：

```text
5432
仅允许
ECS Security Group
```

这就是非常重要的：

```text
Internet
   ↓
ALB
   ↓
ECS
   ↓
RDS
```

**Internet 不应该直接访问 ECS 或 RDS。**

---

# 28. Security Group 最终关系

```text
Internet
   │
   │ 443
   ▼
ALB-SG
   │
   │ 8080
   ▼
ECS-SG
   │
   │ 5432
   ▼
RDS-SG
```

具体：

### ALB-SG

```text
Inbound:
80  ← 0.0.0.0/0
443 ← 0.0.0.0/0
```

### ECS-SG

```text
Inbound:
8080 ← ALB-SG
```

### RDS-SG

```text
Inbound:
5432 ← ECS-SG
```

这是今天最值得记住的网络模型。

---

# 29. Day 13 测试清单

```text
[ ] Route 53 Hosted Zone
[ ] api.yourdomain.com
[ ] ACM Certificate
[ ] DNS Validation
[ ] Certificate = Issued
[ ] ALB :443
[ ] HTTP :80 → HTTPS
[ ] Route 53 Alias
[ ] HTTPS /actuator/health
[ ] HTTPS /api/users
[ ] HTTPS /api/auth/login
[ ] HTTPS /api/chat
[ ] HTTPS /api/rag/query
[ ] CORS
[ ] Security Groups
```

---

# 30. 到 Day 13，你的系统已经接近生产

```text
                         Internet
                            │
                         HTTPS
                            │
                            ▼
                    api.yourdomain.com
                            │
                        Route 53
                            │
                            ▼
                           ALB
                         443/80
                            │
                            ▼
                       ECS Fargate
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
       Spring Security   Spring AI        RAG
             │              │              │
             ▼              ▼              ▼
            JWT          Bedrock        pgvector
                                           │
                                           ▼
                                         RDS
                            │
                            ▼
                    Secrets Manager
```

### Day 14

下一步建议把前端正式上线：

```text
React
 ↓
npm build
 ↓
S3
 ↓
CloudFront
 ↓
https://app.yourdomain.com
```

同时把：

```text
app.yourdomain.com
        ↓
CloudFront
        ↓
S3
```

和今天完成的：

```text
api.yourdomain.com
        ↓
ALB
        ↓
ECS
```

组合起来，形成完整的**AWS Java + AI 全栈生产架构**。
