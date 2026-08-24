
# Day 3：Spring Boot 企业后端 + PostgreSQL

今天把昨天的在线开发环境真正变成一个**企业 Java 后端**。

今天完成：

```text
React
  ↓ REST
Spring Boot
  ↓ JPA
PostgreSQL
  ↓
Docker Compose
```

并加入：

* Spring Data JPA
* PostgreSQL
* Flyway
* Validation
* Global Exception Handler
* OpenAPI / Swagger
* JUnit
* Docker Compose

---

# 1. 今天最终架构

```text
┌──────────────────────┐
│ GitHub Codespaces    │
│                      │
│ React :5173          │
│      │               │
│      ▼               │
│ Spring Boot :8080    │
│      │               │
│      ▼               │
│ PostgreSQL :5432     │
└──────────────────────┘
```

今天 PostgreSQL **先运行在 Docker Container**。

以后：

```text
DEV
 ↓
Amazon RDS PostgreSQL

TEST
 ↓
Amazon RDS PostgreSQL
```

---

# 2. 进入 backend

在 Codespaces Terminal：

```bash
cd ~/aws-java-ai-platform/backend
```

如果你的路径不同：

```bash
pwd
```

确认当前目录有：

```text
pom.xml
src/
```

---

# 3. 修改 `pom.xml`

确保依赖包含：

```xml
<dependencies>

    <!-- REST API -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- PostgreSQL -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- Flyway -->
    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-core</artifactId>
    </dependency>

    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-database-postgresql</artifactId>
    </dependency>

    <!-- Actuator -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <!-- Swagger -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.8.13</version>
    </dependency>

    <!-- Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

</dependencies>
```

如果你的 Spring Boot 版本较新，`springdoc` 版本可以按官方兼容版本调整。

---

# 4. 创建 PostgreSQL Docker

在项目根目录：

```bash
cd ..
```

创建：

```text
docker-compose.yml
```

内容：

```yaml
services:

  postgres:
    image: postgres:17
    container_name: java-ai-postgres

    environment:
      POSTGRES_DB: enterprise_ai
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_password

    ports:
      - "5432:5432"

    volumes:
      - postgres_data:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app_user -d enterprise_ai"]
      interval: 5s
      timeout: 5s
      retries: 10

volumes:
  postgres_data:
```

启动：

```bash
docker compose up -d
```

检查：

```bash
docker ps
```

应该看到：

```text
java-ai-postgres
```

---

# 5. 配置 Spring Boot

进入：

```bash
cd backend
```

打开：

```text
src/main/resources/application.yml
```

写：

```yaml
spring:

  application:
    name: aws-java-ai-backend

  datasource:
    url: jdbc:postgresql://localhost:5432/enterprise_ai
    username: app_user
    password: app_password
    driver-class-name: org.postgresql.Driver

  jpa:
    hibernate:
      ddl-auto: validate

    open-in-view: false

    properties:
      hibernate:
        format_sql: true

  flyway:
    enabled: true
    locations: classpath:db/migration

server:
  port: 8080

management:
  endpoints:
    web:
      exposure:
        include: health,info
```

注意：

```yaml
ddl-auto: validate
```

我们**不让 Hibernate 自动修改数据库结构**。

数据库结构交给：

```text
Flyway
```

管理。

这更接近企业项目。

---

# 6. 创建数据库 Migration

创建：

```text
src/main/resources/db/migration/V1__create_users.sql
```

内容：

```sql
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

以后数据库变化：

```text
V1__create_users.sql

V2__add_user_status.sql

V3__create_orders.sql

V4__create_documents.sql
```

这就是企业常用的 Database Migration。

---

# 7. 创建 Entity

目录：

```text
src/main/java/com/example/demo/user/
```

创建：

```text
User.java
```

```java
package com.example.demo.user;

import jakarta.persistence.*;

import java.time.LocalDateTime;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(name = "created_at", nullable = false)
    private LocalDateTime createdAt;

    public User() {
    }

    public User(String name, String email) {
        this.name = name;
        this.email = email;
        this.createdAt = LocalDateTime.now();
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public LocalDateTime getCreatedAt() {
        return createdAt;
    }
}
```

---

# 8. Repository

创建：

```text
UserRepository.java
```

```java
package com.example.demo.user;

import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface UserRepository
        extends JpaRepository<User, Long> {

    Optional<User> findByEmail(String email);
}
```

---

# 9. DTO

不要让 Controller 直接接收 Entity。

创建：

```text
CreateUserRequest.java
```

```java
package com.example.demo.user;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;

public record CreateUserRequest(

        @NotBlank
        String name,

        @NotBlank
        @Email
        String email
) {
}
```

---

# 10. Service

创建：

```text
UserService.java
```

```java
package com.example.demo.user;

import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    public User create(CreateUserRequest request) {

        User user = new User(
                request.name(),
                request.email()
        );

        return repository.save(user);
    }

    public List<User> findAll() {
        return repository.findAll();
    }
}
```

---

# 11. Controller

创建：

```text
UserController.java
```

```java
package com.example.demo.user;

import jakarta.validation.Valid;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @PostMapping
    public User create(
            @Valid @RequestBody CreateUserRequest request) {

        return service.create(request);
    }

    @GetMapping
    public List<User> findAll() {
        return service.findAll();
    }
}
```

现在：

```text
POST /api/users
GET  /api/users
```

已经可以工作。

---

# 12. 启动 Spring Boot

```bash
./mvnw clean test
```

如果：

```text
BUILD SUCCESS
```

然后：

```bash
./mvnw spring-boot:run
```

---

# 13. 测试 POST

打开另外一个 Terminal：

```bash
curl -X POST http://localhost:8080/api/users \
-H "Content-Type: application/json" \
-d '{"name":"John","email":"john@example.com"}'
```

应该得到类似：

```json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com",
  "createdAt": "2026-08-20T..."
}
```

---

# 14. 测试 GET

```bash
curl http://localhost:8080/api/users
```

应该：

```json
[
  {
    "id": 1,
    "name": "John",
    "email": "john@example.com"
  }
]
```

现在：

```text
React
   ↓
Spring Boot
   ↓
JPA
   ↓
PostgreSQL
```

真正打通了。

---

# 15. Swagger

浏览器打开：

```text
http://localhost:8080/swagger-ui/index.html
```

你应该看到：

```text
aws-java-ai-backend

GET  /api/users
POST /api/users
```

以后我们会把：

```text
User API
Order API
Product API
AI Chat API
RAG API
Agent API
```

全部通过 Swagger 管理。

---

# 16. Health Check

打开：

```text
http://localhost:8080/actuator/health
```

应该：

```json
{
  "status": "UP"
}
```

这就是以后 ECS 的健康检查入口。

---

# 17. 今天再加一个非常重要的东西：全局异常处理

创建：

```text
GlobalExceptionHandler.java
```

```java
package com.example.demo.common;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.*;

import java.util.Map;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public Map<String, String> handleValidation(
            MethodArgumentNotValidException ex) {

        return Map.of(
                "error",
                "Invalid request"
        );
    }
}
```

现在：

```text
POST /api/users
```

如果：

```json
{
  "name": "",
  "email": "abc"
}
```

不会直接返回 Spring 默认错误页面。

---

# 18. 项目结构

Day 3 完成以后：

```text
backend/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       │
│   │   │       ├── user/
│   │   │       │   ├── User.java
│   │   │       │   ├── UserRepository.java
│   │   │       │   ├── UserService.java
│   │   │       │   ├── UserController.java
│   │   │       │   └── CreateUserRequest.java
│   │   │       │
│   │   │       └── common/
│   │   │           └── GlobalExceptionHandler.java
│   │   │
│   │   └── resources/
│   │       ├── application.yml
│   │       └── db/
│   │           └── migration/
│   │               └── V1__create_users.sql
│   │
│   └── test/
│
└── pom.xml
```

---

# 19. Docker Compose

项目根目录：

```text
aws-java-ai-platform/
│
├── backend/
├── frontend/
├── ai-service/
├── infrastructure/
│
├── docker-compose.yml
│
└── .devcontainer/
```

运行：

```bash
docker compose up -d
```

查看：

```bash
docker ps
```

关闭：

```bash
docker compose down
```

---

# 20. Day 3 的企业架构已经形成

```text
                   React
                     │
                     │ REST
                     ▼
              Spring Boot
                     │
          ┌──────────┼──────────┐
          │          │          │
       Controller  Service    Validation
                     │
                     ▼
                   JPA
                     │
                     ▼
                PostgreSQL
                     │
                     ▲
                   Flyway
```

再加：

```text
Swagger
Actuator
JUnit
Docker
```

这已经不是简单的 Java Demo，而是一个可以继续扩展成企业应用的基础。

---

# Day 3 必做检查

```text
[ ] PostgreSQL Docker 正常
[ ] Spring Boot 正常
[ ] Flyway V1 执行成功
[ ] User Entity
[ ] User Repository
[ ] User Service
[ ] User Controller
[ ] POST /api/users
[ ] GET /api/users
[ ] Validation
[ ] Global Exception Handler
[ ] Swagger
[ ] Actuator
[ ] mvnw clean test
```

### 今天的最终结果

```text
GitHub Codespaces
        │
        ├── React
        │
        └── Spring Boot
                │
                ▼
             JPA
                │
                ▼
        PostgreSQL Docker
```

**Day 4 我们把 React 真正接到 Spring Boot：React 登录/用户页面 → REST API → PostgreSQL，并加入 CORS、环境变量、API Client 和前端项目结构。**

