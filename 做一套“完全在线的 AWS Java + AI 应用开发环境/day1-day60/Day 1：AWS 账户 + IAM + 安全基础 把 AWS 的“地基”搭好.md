# Day 1：AWS 账户 + IAM + 安全基础

今天**不写 Java 代码**。目标是把 AWS 的“地基”搭好，并为后面的 GitHub Codespaces、ECS、RDS、Bedrock 做准备。

## 今天完成 5 件事

```text
Day 1
│
├── 1. AWS Account
├── 2. Billing / Cost Protection
├── 3. Root Account 安全
├── 4. IAM Admin
└── 5. AWS CLI 验证
```

---

## 1. AWS Account

如果你已经有 AWS Account，直接进入第 2 步。

如果没有：

[AWS 官方注册入口](https://aws.amazon.com/?utm_source=chatgpt.com)

注册时准备：

* Email
* 手机号码
* 信用卡/借记卡
* 美国地址
* MFA 手机验证

**建议：使用你长期使用的个人 AWS 账户，不要使用公司账户做这个学习环境。**

---

# 2. 先设置 Billing

登录 AWS Console 后：

```text
右上角账户名称
      ↓
Billing and Cost Management
```

首先确认：

```text
Payment methods
```

已经有付款方式。

然后检查：

```text
Billing preferences
```

开启账单相关通知。

### 建议设置预算

进入：

```text
Billing
 ↓
Budgets
 ↓
Create budget
```

建立：

```text
Budget name:
AWS-Java-AI-Learning

Budget:
$20/month
```

再建立一个较高的提醒：

```text
$50/month
```

这样以后如果 ECS、RDS、Bedrock 等服务产生异常费用，可以尽早发现。

---

# 3. Root Account 必须保护

AWS Root Account **不要作为日常开发账户**。

进入：

```text
IAM
 ↓
Dashboard
 ↓
Root user
```

确认 Root 用户已经：

```text
MFA = Enabled
```

推荐使用：

```text
Authenticator App
```

而不是仅依赖短信。

---

# 4. 建立 IAM 管理员

进入：

```text
IAM
 ↓
Users
 ↓
Create user
```

用户名：

```text
dev-admin
```

选择：

```text
Provide user access to AWS Management Console
```

然后创建密码。

### 权限

学习环境第一阶段可以给：

```text
AdministratorAccess
```

但这里要注意：

**这是为了 Day 1–10 快速搭建学习环境，不是生产环境权限模型。**

真正企业生产环境应该采用：

```text
Least Privilege
IAM Role
Permission Set
SSO / IAM Identity Center
```

---

# 5. 给 dev-admin 开启 MFA

进入：

```text
IAM
 ↓
Users
 ↓
dev-admin
 ↓
Security credentials
 ↓
Assign MFA device
```

使用手机 Authenticator。

完成以后：

```text
Root
   │
   └── 只负责账户级管理

dev-admin
   │
   └── 日常 AWS 开发
```

**以后不要使用 Root 登录开发。**

---

# 6. 创建 AWS CLI Access

这一点我们先**不要急着创建长期 Access Key**。

后面的 GitHub Codespaces 会采用更安全的方式连接 AWS。

我们最终希望：

```text
GitHub Codespaces
        │
        ▼
AWS IAM
        │
        ▼
Temporary Credentials
```

而不是：

```text
AWS_ACCESS_KEY_ID=永久Key
AWS_SECRET_ACCESS_KEY=永久Key
```

尤其是以后做 GitHub 项目时，**绝对不要把 AWS Access Key 写进 GitHub Repository。**

---

# 7. 先认识 AWS Region

你在美国西海岸，Day 1 建议选择：

```text
US West (Oregon)
us-west-2
```

或者：

```text
US West (N. California)
us-west-1
```

我更建议：

**`us-west-2`**

因为后面：

```text
ECS
RDS
Bedrock
ECR
CloudWatch
```

我们尽量全部放在同一个 Region。

所以以后看到：

```text
Region
```

默认选择：

```text
us-west-2
```

---

# 8. 第一个 AWS CLI 环境

今天可以先在你自己的电脑验证 AWS CLI。

如果你是 Mac：

```bash
brew install awscli
```

检查：

```bash
aws --version
```

应该看到类似：

```text
aws-cli/2.x.x
```

Windows 可以安装：

[AWS CLI 官方安装文档](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html?utm_source=chatgpt.com)

---

# 9. 今天先不要配置永久 Key

我们的最终方案是：

```text
Browser
   ↓
GitHub Codespaces
   ↓
AWS
```

所以 Day 2 我们会建立：

```text
GitHub
   ↓
Codespaces
   ↓
Java 21
   ↓
Maven
   ↓
Node.js
   ↓
Docker
   ↓
AWS CLI
   ↓
Terraform
```

然后让 Codespaces 可以安全访问 AWS。

---

# 10. Day 1 最重要的安全原则

记住这 5 条：

```text
① Root 不用于开发

② Root 开 MFA

③ dev-admin 开 MFA

④ AWS Key 不放 GitHub

⑤ 不把密码/API Key 写进 Java代码
```

后面统一使用：

```text
IAM Role
+
Secrets Manager
+
Temporary Credentials
```

---

# 11. 今天的验证清单

完成以后，你应该达到：

```text
[ ] AWS Account 已建立
[ ] Billing 已设置
[ ] $20 Budget 已建立
[ ] Root MFA 已开启
[ ] dev-admin 已建立
[ ] dev-admin MFA 已开启
[ ] Region = us-west-2
[ ] AWS CLI 已安装
[ ] aws --version 正常
```

### Day 1 完成标准

你的 AWS 架构现在应该是：

```text
AWS Account
│
├── Root
│    └── MFA
│
├── dev-admin
│    └── MFA
│
└── us-west-2
```

**做到这里就停止，不要今天创建 ECS、RDS、Bedrock。**
我们下一步先把真正的**在线开发机 GitHub Codespaces**搭起来。

我可以继续带你做 **Day 2：GitHub Codespaces + Java 21 + Maven + Node.js + Docker + AWS CLI + Terraform，一次性把浏览器里的企业 Java 开发工作站搭好。**
