# Day 11：Spring Security + JWT 用户认证

前 10 天已经把：

```text
React
Spring Boot
PostgreSQL
RDS
Docker
ECR
ECS
ALB
Bedrock
RAG
CI/CD
```

串起来了。

今天开始做**企业应用最重要的安全层：Authentication + Authorization**。

最终：

```text
React
  │
  │ Login
  ▼
Spring Security
  │
  ▼
JWT
  │
  ├── User
  ├── Role
  └── Permission
  │
  ▼
Protected API
```

---

# 1. 今天完成什么

```text id="3k9h7m"
Day 11
│
├── Spring Security
├── User Password
├── BCrypt
├── Login API
├── JWT Access Token
├── JWT Authentication Filter
├── Role
├── Protected API
└── React Login
```

最终实现：

```text
POST /api/auth/register
POST /api/auth/login

GET /api/users       ← 需要 JWT
GET /api/admin       ← ADMIN 才能访问
```

---

# 2. 为什么今天必须做 Security

目前：

```text id="r9n5w2"
Internet
   ↓
ALB
   ↓
ECS
   ↓
/api/users
```

任何人都可以：

```bash
curl /api/users
```

这是不能接受的。

今天变成：

```text id="1q7x4k"
Internet
   ↓
ALB
   ↓
Spring Security
   ↓
JWT Validation
   ↓
Controller
```

---

# 3. 添加 Spring Security

`backend/pom.xml`：

```xml id="8w3m1q"
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

JWT：

```xml id="5r9k2p"
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.6</version>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.6</version>
    <scope>runtime</scope>
</dependency>
```

---

# 4. User 数据库增加密码和角色

创建 Flyway：

```text id="1y8m4c"
V3__add_security_fields.sql
```

```sql id="s3j8v1"
ALTER TABLE users
ADD COLUMN password_hash VARCHAR(255);

ALTER TABLE users
ADD COLUMN role VARCHAR(50) NOT NULL DEFAULT 'USER';
```

数据库：

```text id="h4p7n2"
users
│
├── id
├── name
├── email
├── password_hash
├── role
└── created_at
```

---

# 5. 修改 User Entity

增加：

```java id="v6m2k8"
@Column(name = "password_hash")
private String passwordHash;

@Column(nullable = false)
private String role;
```

构造：

```java id="q9x4c1"
public User(
        String name,
        String email,
        String passwordHash) {

    this.name = name;
    this.email = email;
    this.passwordHash = passwordHash;
    this.role = "USER";
    this.createdAt = LocalDateTime.now();
}
```

---

# 6. 绝对不要保存明文密码

错误：

```text id="5t2n8x"
password = "123456"
```

数据库：

```text id="z4p7k1"
password_hash
```

应该是：

```text id="w8c3m6"
123456
 ↓
BCrypt
 ↓
$2a$...
```

---

# 7. BCrypt PasswordEncoder

创建：

```text id="2v9q4x"
SecurityConfig.java
```

先：

```java id="r5m8k2"
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

# 8. Security Configuration

```java id="n7x3p9"
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(
            HttpSecurity http) throws Exception {

        http
            .csrf(csrf -> csrf.disable())

            .authorizeHttpRequests(auth -> auth

                .requestMatchers(
                    "/api/auth/**",
                    "/swagger-ui/**",
                    "/v3/api-docs/**",
                    "/actuator/health"
                ).permitAll()

                .anyRequest()
                .authenticated()
            )

            .sessionManagement(session ->
                session.sessionCreationPolicy(
                    SessionCreationPolicy.STATELESS
                )
            );

        return http.build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

重点：

```text id="n6f4y2"
STATELESS
```

因为我们使用 JWT，而不是传统 Server Session。

---

# 9. Register API

创建：

```text id="0v7c4m"
auth/
├── AuthController.java
├── AuthService.java
├── RegisterRequest.java
└── LoginRequest.java
```

Register Request：

```java id="x2m7k5"
public record RegisterRequest(
        String name,
        String email,
        String password
) {}
```

---

# 10. 注册 Service

```java id="j8q4n1"
@Service
public class AuthService {

    private final UserRepository userRepository;
    private final PasswordEncoder passwordEncoder;

    public AuthService(
            UserRepository userRepository,
            PasswordEncoder passwordEncoder) {

        this.userRepository = userRepository;
        this.passwordEncoder = passwordEncoder;
    }

    public User register(RegisterRequest request) {

        String hash =
            passwordEncoder.encode(
                request.password()
            );

        User user = new User(
            request.name(),
            request.email(),
            hash
        );

        return userRepository.save(user);
    }
}
```

---

# 11. Register Controller

```java id="p4x9m2"
@RestController
@RequestMapping("/api/auth")
public class AuthController {

    private final AuthService authService;

    public AuthController(
            AuthService authService) {

        this.authService = authService;
    }

    @PostMapping("/register")
    public User register(
            @RequestBody RegisterRequest request) {

        return authService.register(request);
    }
}
```

---

# 12. 测试 Register

```bash id="z7m2q5"
curl -X POST \
http://localhost:8080/api/auth/register \
-H "Content-Type: application/json" \
-d '{
  "name":"Alice",
  "email":"alice@example.com",
  "password":"StrongPassword123!"
}'
```

数据库：

```sql id="f3k8v1"
SELECT
    id,
    email,
    password_hash,
    role
FROM users;
```

你应该看到：

```text id="n6p4x9"
alice@example.com
$2a$...
USER
```

---

# 13. 创建 JWT Service

```java id="w5c9r2"
@Service
public class JwtService {

    private final SecretKey key;

    public JwtService(
            @Value("${jwt.secret}")
            String secret) {

        this.key =
            Keys.hmacShaKeyFor(
                secret.getBytes(
                    StandardCharsets.UTF_8
                )
            );
    }

    public String generateToken(
            User user) {

        return Jwts.builder()
            .subject(user.getEmail())
            .claim(
                "role",
                user.getRole()
            )
            .issuedAt(new Date())
            .expiration(
                new Date(
                    System.currentTimeMillis()
                    + 1000L * 60 * 60
                )
            )
            .signWith(key)
            .compact();
    }
}
```

---

# 14. JWT Secret

开发环境：

```yaml id="3r7kq5"
jwt:
  secret: ${JWT_SECRET}
```

不要：

```yaml id="7q4p9x"
jwt:
  secret: abc123
```

以后：

```text id="g8m2c6"
AWS Secrets Manager
        ↓
ECS
        ↓
JWT_SECRET
```

Day 12 我们会正式做。

---

# 15. Login

`LoginRequest`：

```java id="c9x4m7"
public record LoginRequest(
        String email,
        String password
) {}
```

Service：

```java id="a7k2p5"
public String login(
        LoginRequest request) {

    User user =
        userRepository
            .findByEmail(request.email())
            .orElseThrow(
                () -> new RuntimeException(
                    "Invalid credentials"
                )
            );

    if (!passwordEncoder.matches(
            request.password(),
            user.getPasswordHash())) {

        throw new RuntimeException(
            "Invalid credentials"
        );
    }

    return jwtService.generateToken(user);
}
```

---

# 16. Login Controller

```java id="h3v8m1"
@PostMapping("/login")
public Map<String, String> login(
        @RequestBody LoginRequest request) {

    String token =
        authService.login(request);

    return Map.of(
        "accessToken",
        token
    );
}
```

---

# 17. 测试 Login

```bash id="q5m9c2"
curl -X POST \
http://localhost:8080/api/auth/login \
-H "Content-Type: application/json" \
-d '{
  "email":"alice@example.com",
  "password":"StrongPassword123!"
}'
```

返回：

```json id="t7x3n8"
{
  "accessToken": "eyJhbGciOiJIUzI1NiJ9..."
}
```

这就是 JWT。

---

# 18. JWT 请求

现在访问：

```bash id="j8p2v5"
curl \
http://localhost:8080/api/users
```

应该：

```text id="b6q9x1"
401 Unauthorized
```

因为没有 Token。

加入：

```bash id="s4m7k2"
curl \
http://localhost:8080/api/users \
-H "Authorization: Bearer YOUR_TOKEN"
```

应该：

```text id="d9x3p7"
200 OK
```

---

# 19. JWT Filter

现在 Spring Security 还不知道：

```text
Authorization:
Bearer eyJ...
```

是什么意思。

创建：

```text id="r5c8m2"
JwtAuthenticationFilter.java
```

核心逻辑：

```java id="w7q3n9"
@Component
public class JwtAuthenticationFilter
        extends OncePerRequestFilter {

    private final JwtService jwtService;

    public JwtAuthenticationFilter(
            JwtService jwtService) {

        this.jwtService = jwtService;
    }

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        String authHeader =
            request.getHeader(
                "Authorization"
            );

        if (authHeader == null ||
            !authHeader.startsWith("Bearer ")) {

            filterChain.doFilter(
                request,
                response
            );

            return;
        }

        String token =
            authHeader.substring(7);

        String email =
            jwtService.extractEmail(token);

        // Validate token
        // Load user
        // Set SecurityContext

        filterChain.doFilter(
            request,
            response
        );
    }
}
```

---

# 20. 把 Filter 加入 Security

```java id="f2n8x5"
@Bean
SecurityFilterChain securityFilterChain(
        HttpSecurity http,
        JwtAuthenticationFilter jwtFilter)
        throws Exception {

    http
        .csrf(csrf -> csrf.disable())

        .authorizeHttpRequests(auth -> auth

            .requestMatchers(
                "/api/auth/**",
                "/swagger-ui/**",
                "/v3/api-docs/**",
                "/actuator/health"
            ).permitAll()

            .anyRequest()
            .authenticated()
        )

        .sessionManagement(session ->
            session.sessionCreationPolicy(
                SessionCreationPolicy.STATELESS
            )
        )

        .addFilterBefore(
            jwtFilter,
            UsernamePasswordAuthenticationFilter.class
        );

    return http.build();
}
```

---

# 21. Role Authorization

现在用户：

```text id="c7m2p8"
USER
ADMIN
```

创建 Admin API：

```java id="n5x8k3"
@GetMapping("/admin")
@PreAuthorize("hasRole('ADMIN')")
public String admin() {
    return "Admin Area";
}
```

SecurityConfig：

```java id="x4m7q9"
@EnableMethodSecurity
```

于是：

```text id="f3c8v1"
USER
 ↓
/api/admin
 ↓
403 Forbidden
```

而：

```text id="m9p2k6"
ADMIN
 ↓
/api/admin
 ↓
200 OK
```

---

# 22. React 登录

建立：

```text id="6c1m8x"
frontend/src/pages/LoginPage.tsx
```

核心：

```tsx id="v7p4q2"
async function login() {

  const response =
    await apiClient.post(
      "/api/auth/login",
      {
        email,
        password
      }
    );

  const token =
    response.data.accessToken;

  localStorage.setItem(
    "accessToken",
    token
  );
}
```

---

# 23. Axios 自动添加 JWT

修改：

```text id="n8x3k5"
src/api/client.ts
```

```typescript id="q4m7v2"
import axios from "axios";

export const apiClient = axios.create({
  baseURL:
    import.meta.env.VITE_API_BASE_URL,
});

apiClient.interceptors.request.use(
  (config) => {

    const token =
      localStorage.getItem(
        "accessToken"
      );

    if (token) {
      config.headers.Authorization =
        `Bearer ${token}`;
    }

    return config;
  }
);
```

现在：

```text id="p8v3m6"
React
 ↓
Axios Interceptor
 ↓
Authorization: Bearer JWT
 ↓
Spring Security
```

---

# 24. 测试完整 Login

```text id="7j2k4m"
Browser
   ↓
Login
   ↓
POST /api/auth/login
   ↓
JWT
   ↓
Browser Storage
   ↓
GET /api/users
   ↓
Bearer JWT
   ↓
Spring Security
   ↓
200
```

---

# 25. 一个重要的生产安全问题

今天为了学习：

```text id="r2k7m4"
localStorage
```

可以先使用。

但生产环境更推荐考虑：

```text id="s9x4p1"
HttpOnly Secure SameSite Cookie
```

因为把长期 JWT 放在 `localStorage` 会增加 XSS 风险。

以后做正式生产认证时，我们会进一步改成：

```text id="v6m3q8"
Short-lived Access Token
+
Refresh Token
+
HttpOnly Secure Cookie
```

---

# 26. JWT 生命周期

不要让 Access Token：

```text id="q1x8m4"
30 days
```

建议：

```text id="c7p3n9"
Access Token
15–60 minutes
```

然后：

```text id="w5m2k7"
Refresh Token
longer lifetime
```

这样即使 Access Token 泄露，风险窗口也比较小。

---

# 27. Day 11 架构

```text id="x8p4m2"
                      React
                        │
                        ▼
                  Login Page
                        │
                        ▼
              POST /api/auth/login
                        │
                        ▼
                Spring Security
                        │
                        ▼
                  UserRepository
                        │
                        ▼
                   PostgreSQL
                        │
                        ▼
                       JWT
                        │
                        ▼
                   React Client
                        │
               Authorization Header
                        │
                        ▼
                JwtAuthenticationFilter
                        │
                        ▼
                    Controller
```

---

# 28. 今天的安全层

```text id="u5k8m1"
                    Internet
                       │
                       ▼
                      ALB
                       │
                       ▼
                 ECS Fargate
                       │
                       ▼
               Spring Security
                       │
             ┌─────────┴─────────┐
             │                   │
          Authentication      Authorization
             │                   │
            JWT                Role
             │                   │
             └─────────┬─────────┘
                       ▼
                   REST API
```

---

# 29. Day 11 检查清单

```text id="2m7x9p"
[ ] Spring Security
[ ] BCrypt
[ ] User password_hash
[ ] User role
[ ] Register API
[ ] Login API
[ ] JWT generation
[ ] JWT validation
[ ] JWT Filter
[ ] Bearer Token
[ ] Protected API
[ ] USER role
[ ] ADMIN role
[ ] React Login
[ ] Axios JWT interceptor
```

---

# 30. 今天完成后的状态

你的应用已经从：

```text id="h8v3q2"
公网 API
```

升级成：

```text id="q4m7x9"
公网
 ↓
ALB
 ↓
ECS
 ↓
Spring Security
 ↓
JWT
 ↓
Role
 ↓
Enterprise API
```

同时保留：

```text id="a2n6p8"
RDS
Bedrock
RAG
CI/CD
```

**Day 12 的重点是把今天留下的两个安全问题解决掉：**

```text
DB_PASSWORD
JWT_SECRET
        ↓
AWS Secrets Manager
        ↓
ECS Task
```

并把 ECS 从目前的开发配置逐步改成**不在 Task Definition / GitHub / Docker Image 中保存任何密码和 Secret 的企业安全配置**。
