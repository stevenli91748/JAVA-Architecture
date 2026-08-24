# Day 2：搭建完全在线的开发工作站

今天的目标是：**以后只需要浏览器，不需要在本地安装 Java、Maven、Node、Docker、Terraform。**

最终：

```text
浏览器
  ↓
GitHub Codespaces
  ↓
VS Code Web
  ├── Java 21
  ├── Maven
  ├── Node.js
  ├── Docker CLI
  ├── AWS CLI
  ├── Terraform
  └── Git
```

---

## 1. 创建 GitHub Repository

登录 [GitHub](https://github.com?utm_source=chatgpt.com)。

创建：

```text
Repository name:
aws-java-ai-platform
```

建议：

```text
Visibility:
Private

Initialize:
✓ Add README
```

创建完成后，进入：

```text
Code
→ Codespaces
→ Create codespace on main
```

浏览器会打开一个 VS Code 开发环境。

---

# 2. 建立 `.devcontainer`

在项目根目录创建：

```text
.devcontainer/
└── devcontainer.json
```

内容：

```json
{
  "name": "AWS Java AI Development",
  "image": "mcr.microsoft.com/devcontainers/java:1-21-bookworm",

  "features": {
    "ghcr.io/devcontainers/features/node:1": {
      "version": "22"
    },
    "ghcr.io/devcontainers/features/docker-in-docker:2": {
      "version": "latest"
    },
    "ghcr.io/devcontainers/features/aws-cli:1": {},
    "ghcr.io/devcontainers/features/terraform:1": {}
  },

  "customizations": {
    "vscode": {
      "extensions": [
        "vscjava.vscode-java-pack",
        "vmware.vscode-spring-boot",
        "vscjava.vscode-spring-initializr",
        "redhat.vscode-yaml",
        "ms-azuretools.vscode-docker",
        "hashicorp.terraform",
        "amazonwebservices.aws-toolkit-vscode",
        "dbaeumer.vscode-eslint"
      ]
    }
  },

  "forwardPorts": [
    8080,
    3000,
    5173
  ]
}
```

保存。

然后：

```text
Ctrl + Shift + P
```

输入：

```text
Codespaces: Rebuild Container
```

选择：

```text
Rebuild Container
```

等待几分钟。

---

# 3. 验证 Java

打开 Codespaces Terminal：

```bash
java -version
```

应该是：

```text
openjdk 21
```

然后：

```bash
mvn -version
```

应该看到：

```text
Apache Maven
Java version: 21
```

---

# 4. 验证 Node.js

```bash
node --version
```

应该：

```text
v22.x.x
```

然后：

```bash
npm --version
```

---

# 5. 验证 Docker

```bash
docker --version
```

然后：

```bash
docker ps
```

如果没有报错，说明 Codespaces 已经可以运行 Docker。

---

# 6. 验证 AWS CLI

```bash
aws --version
```

应该看到类似：

```text
aws-cli/2.x.x
```

然后：

```bash
aws configure
```

**这里先不要输入永久 Access Key。**

今天先验证工具安装即可。

---

# 7. 验证 Terraform

```bash
terraform version
```

应该看到：

```text
Terraform v1.x.x
```

这样你的在线开发机就具备：

```text
Java
Maven
Node
npm
Docker
AWS CLI
Terraform
Git
```

---

# 8. 建立项目目录

在 Codespaces Terminal：

```bash
mkdir -p backend
mkdir -p frontend
mkdir -p ai-service
mkdir -p infrastructure/terraform
```

现在：

```text
aws-java-ai-platform/
│
├── .devcontainer/
│   └── devcontainer.json
│
├── backend/
│
├── frontend/
│
├── ai-service/
│
├── infrastructure/
│   └── terraform/
│
└── README.md
```

---

# 9. 创建第一个 Spring Boot 项目

我们暂时不用手工配置几十个文件。

使用 Spring Initializr：

[Spring Initializr 官方网站](https://start.spring.io/?utm_source=chatgpt.com)

选择：

```text
Project:
Maven

Language:
Java

Spring Boot:
3.x

Java:
21
```

Dependencies：

```text
Spring Web
Spring Boot Actuator
Spring Data JPA
PostgreSQL Driver
Validation
Lombok
```

暂时不要加入 AI 依赖。

AI 我们 Day 8 再接入。

下载后，把项目放入：

```text
backend/
```

最终：

```text
backend/
├── pom.xml
└── src/
```

---

# 10. 启动第一个 Java 服务

进入：

```bash
cd backend
```

运行：

```bash
./mvnw spring-boot:run
```

如果成功：

```text
Tomcat started on port 8080
```

---

# 11. 创建测试 API

创建：

```text
backend/src/main/java/com/example/demo/controller/HelloController.java
```

代码：

```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/api/hello")
    public String hello() {
        return "Hello AWS Java AI Platform!";
    }
}
```

重新运行：

```bash
./mvnw spring-boot:run
```

浏览器访问：

```text
http://localhost:8080/api/hello
```

Codespaces 会自动提示：

```text
Open in Browser
```

应该看到：

```text
Hello AWS Java AI Platform!
```

---

# 12. 加一个 Health Check

Spring Boot Actuator 已经加入。

访问：

```text
/actuator/health
```

例如：

```text
http://localhost:8080/actuator/health
```

返回：

```json
{
  "status": "UP"
}
```

这个接口以后非常重要。

AWS ECS/ALB 可以用它检查：

```text
Spring Boot
     ↓
/actuator/health
     ↓
Healthy
```

---

# 13. 创建 React

回到项目根目录：

```bash
cd ..
```

运行：

```bash
npm create vite@latest frontend -- --template react-ts
```

然后：

```bash
cd frontend
npm install
```

启动：

```bash
npm run dev -- --host 0.0.0.0
```

Vite 默认：

```text
5173
```

Codespaces 会自动出现：

```text
Open in Browser
```

打开以后看到：

```text
React + Vite
```

---

# 14. 现在你已经拥有前后端在线开发环境

```text
             Browser
                │
                ▼
        GitHub Codespaces
                │
        ┌───────┴────────┐
        │                │
        ▼                ▼
     React           Spring Boot
     :5173              :8080
        │                │
        └───────┬────────┘
                ▼
            GitHub
```

完全不需要：

```text
本地 IntelliJ
本地 Java
本地 Maven
本地 Node
本地 Docker
```

---

# 15. Git 提交

回到根目录：

```bash
cd ..
```

检查：

```bash
git status
```

然后：

```bash
git add .
```

提交：

```bash
git commit -m "Day 2: online Java AI development environment"
```

推送：

```bash
git push
```

现在代码已经进入 GitHub。

---

# 16. 今天最重要的一个概念

从今天开始，你的开发环境和运行环境彻底分开：

```text
开发环境
──────────────
GitHub Codespaces
Java
Maven
Node
Docker
Terraform
AWS CLI


运行环境
──────────────
AWS ECS
RDS
Redis
Bedrock
S3
CloudFront
```

这是企业开发非常重要的架构思想。

---

# Day 2 完成检查

```text
[ ] GitHub Repository
[ ] GitHub Codespaces
[ ] VS Code Web
[ ] Java 21
[ ] Maven
[ ] Node.js
[ ] npm
[ ] Docker
[ ] AWS CLI
[ ] Terraform
[ ] Spring Boot
[ ] /api/hello
[ ] /actuator/health
[ ] React
[ ] git push
```

完成后，你已经有了一台**完全在线的企业 Java + AI 开发工作站**。

### Day 3

下一步我们把真正的后端基础搭起来：

```text
Day 3
│
├── Spring Boot 企业项目结构
├── PostgreSQL
├── JPA
├── Flyway
├── REST API
├── Exception Handler
├── Validation
├── Swagger/OpenAPI
├── Unit Test
└── Docker Compose
```

**Day 3 的目标是：在 Codespaces 里跑通 `React → Spring Boot → PostgreSQL` 三层应用。**
