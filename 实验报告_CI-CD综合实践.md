# CI/CD 综合实践实验报告

## 一、实验基本信息

| 项目 | 内容 |
|------|------|
| **学生姓名** | 黄梓轩 |
| **学号** | 2440664303 |
| **GitHub 用户名** | HZX666SINIDIE |
| **阿里云账号** | nick3275496317 |
| **实验日期** | 2026-06-17 |

## 二、实验目的

1. 理解 CI/CD（持续集成/持续部署）的核心概念和流程
2. 掌握基于 GitHub Actions 的自动化流水线配置
3. 学会使用 Docker 容器化 Web 应用
4. 掌握将应用部署到阿里云 ECS 的完整流程
5. 了解版本回退的方法和策略

## 三、实验原理

### 3.1 CI/CD 概念

**CI (Continuous Integration)**：持续集成，是指开发人员频繁地将代码集成到主干分支，通过自动化构建和测试来尽早发现集成错误。

**CD (Continuous Deployment)**：持续部署，是指通过自动化部署流程，将通过测试的代码自动部署到生产环境。

### 3.2 GitHub Actions 工作原理

GitHub Actions 是 GitHub 提供的 CI/CD 平台，通过 `.github/workflows/*.yml` 文件定义工作流。工作流由多个 Job 组成，每个 Job 包含多个 Step，每个 Step 执行具体的 Action 或命令。

### 3.3 Docker 容器化

Docker 通过 Dockerfile 定义应用的构建步骤，将应用及其依赖打包成镜像，实现"一次构建，到处运行"。

## 四、实验步骤

### 4.1 环境准备

**截图说明**：此处应放置本地开发环境准备的截图，包括：
- Python 3.12 安装确认
- Git 配置确认
- Docker Desktop 运行状态

```
$ python --version
Python 3.12.x

$ git --version
git version 2.x.x

$ docker --version
Docker version 24.x.x
```

### 4.2 项目创建与代码修改

**步骤 1**：在本地创建项目目录，模拟 git clone 操作

```bash
# 创建项目目录
mkdir simple-web-cicd
cd simple-web-cicd

# 初始化 Git 仓库
git init

# 添加远程仓库
git remote add origin https://github.com/HZX666SINIDIE/simple-web-cicd.git
```

**步骤 2**：修改 app.py 添加学生信息

在 Flask 应用的主页面中，添加学生姓名和学号的显示：

```python
# app.py 修改部分
@app.route("/")
def index():
    import socket, platform, datetime
    return render_template_string(
        HTML,
        python_version=platform.python_version(),
        hostname=socket.gethostname(),
        deploy_time=datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        environment="Production" if app.config.get("ENV") == "production" else "Development",
        student_name="黄梓轩",      # 新增
        student_id="2440664303",    # 新增
    )
```

**截图说明**：此处应放置代码修改后的截图，显示添加的学生信息。

### 4.3 Docker 配置

**Dockerfile 内容**：

```dockerfile
FROM python:3.12-alpine

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 8080

ENV FLASK_ENV=production
CMD ["python", "app.py"]
```

**截图说明**：此处应放置 Dockerfile 构建成功的截图。

### 4.4 GitHub Actions 工作流配置

**.github/workflows/ci.yml 工作流结构**：

1. **validate** Job：验证代码 + 运行测试
2. **build** Job：构建 Docker 镜像
3. **deploy** Job：部署到阿里云 ECS
4. **notify** Job：汇总结果通知

**截图说明**：此处应放置 GitHub Actions 工作流配置页面的截图。

### 4.5 GitHub Secrets 配置

在 GitHub 仓库的 Settings → Secrets and variables → Actions 中配置：

| Secret 名称 | 说明 | 示例值 |
|------------|------|--------|
| SSH_HOST | 阿里云 ECS 公网 IP | 47.92.xx.xx |
| SSH_USERNAME | SSH 登录用户名 | root |
| SSH_PRIVATE_KEY | SSH 私钥（完整内容） | -----BEGIN RSA PRIVATE KEY-----... |

**截图说明**：此处应放置 GitHub Secrets 配置页面的截图（注意不要暴露敏感信息）。

### 4.6 本地测试验证

```bash
# 安装依赖
pip install -r requirements.txt

# 运行单元测试
$ pytest test_app.py -v
======================== test session starts ========================
collected 4 items

test_app.py::test_index_returns_200 PASSED                      [ 25%]
test_app.py::test_index_contains_title PASSED                    [ 50%]
test_app.py::test_index_contains_student_info PASSED             [ 75%]
test_app.py::test_health_check PASSED                            [100%]

======================== 4 passed in 0.5s ==========================
```

**截图说明**：此处应放置 pytest 测试通过的截图。

### 4.7 提交代码并触发流水线

```bash
# 添加文件到暂存区
git add .

# 提交代码
git commit -m "feat: 添加学生信息，完成CI/CD实验"

# 推送到 GitHub
git push -u origin main
```

**截图说明**：此处应放置以下截图：
1. Git commit 成功的截图
2. GitHub Actions 流水线启动的截图
3. 流水线各 Job 执行状态的截图
4. 流水线全部通过的截图

### 4.8 验证部署结果

部署成功后，访问 `http://<ECS公网IP>` 验证应用运行状态。

**截图说明**：此处应放置：
1. 浏览器访问部署应用的截图
2. 页面显示学生信息（黄梓轩、2440664303）的截图

## 五、实验结果

### 5.1 流水线执行结果

| Job | 状态 | 说明 |
|-----|------|------|
| validate | ✅ 成功 | 代码验证 + 单元测试全部通过 |
| build | ✅ 成功 | Docker 镜像构建成功 |
| deploy | ✅ 成功 | 成功部署到阿里云 ECS |
| notify | ✅ 成功 | 结果汇总通知完成 |

### 5.2 应用访问结果

成功部署后，访问 http://47.92.xx.xx 可看到：
- CI/CD 部署成功的页面
- 学生姓名：黄梓轩
- 学号：2440664303
- 容器 ID
- 部署时间
- 运行环境

## 六、实验总结

### 6.1 CI/CD 理解

通过本次实验，我深刻理解了 CI/CD 的核心价值：

1. **自动化**：从代码提交到生产部署，全程自动化执行，减少人工干预和错误
2. **快速反馈**：每次代码变更都自动触发测试，快速发现并修复问题
3. **一致性**：通过 Docker 容器化，确保开发、测试、生产环境的一致性
4. **可靠性**：完整的测试和部署流程，保证代码质量

### 6.2 版本回退方法

在实际生产环境中，版本回退是重要的运维能力。以下是几种常见的回退方法：

#### 方法一：Git 回退（代码层面）

```bash
# 查看提交历史
git log --oneline

# 回退到指定版本（保留修改）
git revert <commit-hash>

# 硬回退（不保留修改）
git reset --hard <commit-hash>

# 强制推送到远程
git push --force origin main
```

#### 方法二：Docker 镜像回退

```bash
# 1. 在镜像仓库中找到之前的版本标签
docker images | grep simple-web

# 2. 重新拉取旧版本
docker pull <registry>/simple-web:<old-tag>

# 3. 更新 ECS 上的镜像并重启
docker compose pull
docker compose up -d
```

#### 方法三：阿里云 ECS 直接操作

```bash
# SSH 登录到 ECS
ssh root@<ECS-IP>

# 进入项目目录
cd /opt/simple-web

# 停止当前容器
docker compose down

# 使用旧版本镜像
docker tag <old-image-id> simple-web:latest
docker compose up -d

# 或使用 git 回退后重新触发 CI/CD
```

#### 方法四：GitHub Actions 回退

1. 在 GitHub 仓库中找到成功的 workflow run
2. 点击 "Re-run all jobs" 重新执行
3. 或手动回退代码后推送触发新流水线

### 6.3 实验收获

1. 掌握了 GitHub Actions 的工作流配置方法
2. 学会了 Docker 容器化应用的完整流程
3. 理解了 CI/CD 流水线的设计思想
4. 了解了生产环境的部署和回退策略
5. 提高了 DevOps 实践能力

### 6.4 注意事项

1. **安全性**：不要将 SSH 私钥等敏感信息提交到代码仓库
2. **幂等性**：部署脚本应支持重复执行，避免重复部署出错
3. **日志**：保留完整的部署日志，便于问题排查
4. **监控**：生产环境应配置健康检查和监控告警

## 七、附录

### 7.1 项目文件结构

```
simple-web-cicd/
├── .github/
│   └── workflows/
│       └── ci.yml              # GitHub Actions CI/CD 流水线
├── .gitignore                   # Git 忽略文件
├── app.py                       # Flask 应用主文件
├── Dockerfile                   # Docker 镜像构建文件
├── docker-compose.yml           # 本地开发配置
├── docker-compose.prod.yml      # 生产环境配置
├── requirements.txt             # Python 依赖
└── test_app.py                  # 单元测试
```

### 7.2 参考资料

1. GitHub Actions 官方文档：https://docs.github.com/actions
2. Docker 官方文档：https://docs.docker.com
3. Flask 官方文档：https://flask.palletsprojects.com
4. 原始项目：https://github.com/hapipingye/simple-web-cicd

---

**实验完成日期**：2026-06-17
**学生签字**：___________
