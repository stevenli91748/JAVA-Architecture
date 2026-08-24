# Day 4：React + Spring Boot 全链路联调

今天把前端真正接上昨天的 Java 后端。

最终实现：

```text
Browser
   │
   ▼
React + TypeScript
   │
   │ HTTP/REST
   ▼
Spring Boot :8080
   │
   ▼
JPA
   │
   ▼
PostgreSQL
```

今天重点不是做漂亮 UI，而是建立**企业前后端开发规范**。

---

# 1. 今天完成什么

```text
Day 4
│
├── React 项目结构
├── TypeScript API Client
├── Axios
├── CORS
├── Environment Variables
├── User 页面
├── Create User
├── User List
└── React → Spring Boot → PostgreSQL
```

---

# 2. 安装 Axios

进入：

```bash id="g1f3e6"
cd frontend
```

安装：

```bash id="q6s4q0"
npm install axios
```

---

# 3. 建立 React 企业目录

把 `src` 整理成：

```text id="zj6d1r"
frontend/
└── src/
    ├── api/
    │   └── client.ts
    │
    ├── components/
    │   └── UserForm.tsx
    │
    ├── pages/
    │   └── UsersPage.tsx
    │
    ├── types/
    │   └── user.ts
    │
    ├── App.tsx
    └── main.tsx
```

这个结构以后可以继续扩展：

```text id="g1j7p9"
pages/
├── LoginPage
├── DashboardPage
├── UsersPage
├── OrdersPage
├── ChatPage
├── RagPage
└── AgentPage
```

---

# 4. User Type

创建：

```text id="o8l5hc"
src/types/user.ts
```

```typescript id="jpw5zv"
export interface User {
  id: number;
  name: string;
  email: string;
  createdAt: string;
}

export interface CreateUserRequest {
  name: string;
  email: string;
}
```

以后不要到处写：

```typescript
{
  id: number;
  name: string;
}
```

统一使用 TypeScript 类型。

---

# 5. Axios API Client

创建：

```text id="7d9q1z"
src/api/client.ts
```

```typescript id="x9h0sc"
import axios from "axios";

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  headers: {
    "Content-Type": "application/json",
  },
});
```

---

# 6. API Service

继续创建：

```text id="8bl0ml"
src/api/userApi.ts
```

```typescript id="2z6k9q"
import { apiClient } from "./client";
import type {
  User,
  CreateUserRequest,
} from "../types/user";

export async function getUsers(): Promise<User[]> {
  const response = await apiClient.get<User[]>("/api/users");

  return response.data;
}

export async function createUser(
  request: CreateUserRequest
): Promise<User> {

  const response =
    await apiClient.post<User>(
      "/api/users",
      request
    );

  return response.data;
}
```

现在前端有了：

```text id="3wz9hb"
React
  ↓
userApi.ts
  ↓
Axios
  ↓
Spring Boot
```

---

# 7. 配置 Frontend Environment

在 `frontend` 创建：

```text id="a9v9g3"
.env.development
```

内容：

```env id="v0t4c8"
VITE_API_BASE_URL=http://localhost:8080
```

注意：

**Vite 的前端环境变量必须使用 `VITE_` 前缀。**

以后部署 AWS：

```text id="j4uhj3"
DEV
VITE_API_BASE_URL=https://api-dev.example.com

TEST
VITE_API_BASE_URL=https://api-test.example.com

PROD
VITE_API_BASE_URL=https://api.example.com
```

---

# 8. Spring Boot CORS

现在 React：

```text
localhost:5173
```

Spring Boot：

```text
localhost:8080
```

两个 Origin 不一样。

所以浏览器会涉及 CORS。

创建：

```text id="xg4b7p"
WebConfig.java
```

```java id="1s31uq"
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.*;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(
            CorsRegistry registry) {

        registry.addMapping("/api/**")
                .allowedOrigins(
                        "http://localhost:5173"
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
}
```

开发环境这样即可。

**生产环境不要直接使用 `allowedOrigins("*")`。**

---

# 9. UserForm

创建：

```text id="q8g46e"
src/components/UserForm.tsx
```

```tsx id="pc1by2"
import { useState } from "react";
import { createUser } from "../api/userApi";

interface Props {
  onCreated: () => void;
}

export default function UserForm({
  onCreated,
}: Props) {

  const [name, setName] = useState("");
  const [email, setEmail] = useState("");

  async function handleSubmit(
    event: React.FormEvent
  ) {
    event.preventDefault();

    await createUser({
      name,
      email,
    });

    setName("");
    setEmail("");

    onCreated();
  }

  return (
    <form onSubmit={handleSubmit}>

      <input
        value={name}
        placeholder="Name"
        onChange={(e) =>
          setName(e.target.value)
        }
      />

      <input
        value={email}
        placeholder="Email"
        onChange={(e) =>
          setEmail(e.target.value)
        }
      />

      <button type="submit">
        Create User
      </button>

    </form>
  );
}
```

---

# 10. UsersPage

创建：

```text id="jp1v2v"
src/pages/UsersPage.tsx
```

```tsx id="9wmv1m"
import { useEffect, useState } from "react";

import UserForm from "../components/UserForm";

import {
  getUsers,
} from "../api/userApi";

import type { User } from "../types/user";

export default function UsersPage() {

  const [users, setUsers] =
    useState<User[]>([]);

  async function loadUsers() {
    const data = await getUsers();

    setUsers(data);
  }

  useEffect(() => {
    loadUsers();
  }, []);

  return (
    <div>

      <h1>Users</h1>

      <UserForm
        onCreated={loadUsers}
      />

      <hr />

      {users.map((user) => (
        <div key={user.id}>
          <strong>
            {user.name}
          </strong>

          <div>
            {user.email}
          </div>
        </div>
      ))}

    </div>
  );
}
```

---

# 11. 修改 App.tsx

```tsx id="2cz1pf"
import UsersPage from "./pages/UsersPage";

function App() {
  return <UsersPage />;
}

export default App;
```

---

# 12. 启动 Spring Boot

Terminal 1：

```bash id="35kzgc"
cd backend
./mvnw spring-boot:run
```

确认：

```text id="i4x4ul"
Tomcat started on port 8080
```

---

# 13. 启动 PostgreSQL

Terminal 2：

```bash id="25yvkw"
docker compose up -d
```

确认：

```bash id="d8h0px"
docker ps
```

应该有：

```text
java-ai-postgres
```

---

# 14. 启动 React

Terminal 3：

```bash id="2t5v70"
cd frontend
npm run dev -- --host 0.0.0.0
```

打开：

```text id="wq8pm7"
5173
```

你应该看到：

```text
Users

Name
Email

[Create User]

John
john@example.com
```

---

# 15. 测试完整链路

在网页输入：

```text id="4b5z1r"
Name:
Alice

Email:
alice@example.com
```

点击：

```text id="d6v9p7"
Create User
```

实际发生：

```text id="p7gqwy"
React
 │
 │ POST /api/users
 ▼
Axios
 │
 ▼
Spring Boot Controller
 │
 ▼
UserService
 │
 ▼
UserRepository
 │
 ▼
JPA
 │
 ▼
PostgreSQL
```

然后：

```text id="9u4o8g"
PostgreSQL
 │
 ▼
GET /api/users
 │
 ▼
Spring Boot
 │
 ▼
React
 │
 ▼
Browser
```

这就是完整的 Full Stack 联调。

---

# 16. Chrome/浏览器检查

按：

```text
F12
```

打开：

```text
Network
```

创建用户时应该看到：

```text
POST
/api/users
200
```

然后刷新用户列表：

```text
GET
/api/users
200
```

如果看到：

```text
CORS error
```

检查：

```text
WebConfig.java
```

以及：

```text
.env.development
```

---

# 17. 今天加入一个重要规范：不要把 API URL 写死

不要：

```typescript
axios.get(
  "http://localhost:8080/api/users"
);
```

使用：

```typescript
apiClient.get("/api/users");
```

然后：

```env
VITE_API_BASE_URL=http://localhost:8080
```

这样以后部署 AWS 时只需要改变：

```env
VITE_API_BASE_URL
```

而不用修改 JavaScript/TypeScript 代码。

---

# 18. 最终 Day 4 架构

```text
                 Browser
                    │
                    ▼
              React / Vite
                    │
                 Axios
                    │
          GET/POST /api/users
                    │
                    ▼
             Spring Boot
                    │
             ┌──────┴──────┐
             │             │
        Controller       Validation
             │
             ▼
          Service
             │
             ▼
        Repository
             │
             ▼
            JPA
             │
             ▼
       PostgreSQL
```

---

# 19. 今天的项目结构

```text
aws-java-ai-platform/
│
├── backend/
│   └── src/main/
│       ├── java/
│       │   └── com/example/demo/
│       │       ├── config/
│       │       │   └── WebConfig.java
│       │       │
│       │       ├── common/
│       │       │   └── GlobalExceptionHandler.java
│       │       │
│       │       └── user/
│       │           ├── User.java
│       │           ├── UserRepository.java
│       │           ├── UserService.java
│       │           ├── UserController.java
│       │           └── CreateUserRequest.java
│       │
│       └── resources/
│           ├── application.yml
│           └── db/migration/
│               └── V1__create_users.sql
│
├── frontend/
│   ├── src/
│   │   ├── api/
│   │   │   ├── client.ts
│   │   │   └── userApi.ts
│   │   ├── components/
│   │   │   └── UserForm.tsx
│   │   ├── pages/
│   │   │   └── UsersPage.tsx
│   │   ├── types/
│   │   │   └── user.ts
│   │   └── App.tsx
│   │
│   └── .env.development
│
├── ai-service/
│
├── infrastructure/
│   └── terraform/
│
└── docker-compose.yml
```

---

# Day 4 完成标准

```text
[ ] React 可以启动
[ ] Spring Boot 可以启动
[ ] PostgreSQL 可以启动
[ ] React → Spring Boot 成功
[ ] POST /api/users 成功
[ ] GET /api/users 成功
[ ] 数据进入 PostgreSQL
[ ] React 能显示数据库数据
[ ] CORS 正常
[ ] Axios API Client
[ ] TypeScript 类型
[ ] 环境变量
```

## 到这里，你已经完成了

```text
Day 1
AWS Account + IAM
       ↓
Day 2
Cloud IDE
       ↓
Day 3
Spring Boot + PostgreSQL
       ↓
Day 4
React + Spring Boot + PostgreSQL
```

**Day 5 我们开始进入 AWS：把今天的本地 Docker PostgreSQL 换成 Amazon RDS PostgreSQL，并建立 VPC、Private Subnet、Security Group、RDS，然后让 Codespaces 中的 Spring Boot 连接真正的 AWS 数据库。**
