# Simple Web CI/CD 项目

## 项目信息

- **学生姓名**：黄梓轩
- **学号**：2440664303
- **GitHub 用户名**：HZX666SINIDIE
- **阿里云账号**：nick3275496317

## 项目简介

本项目基于 [hapipingye/simple-web-cicd](https://github.com/hapipingye/simple-web-cicd) 修改，是一个极简的 Flask Web 应用，用于演示 CI/CD 完整流程。

## 技术栈

- **后端**：Python 3.12 + Flask 3.1.0
- **容器化**：Docker + Docker Compose
- **CI/CD**：GitHub Actions
- **部署**：阿里云 ECS

## 项目结构

```
simple-web-cicd/
├── .github/
│   └── workflows/
│       └── ci.yml          # GitHub Actions 工作流
├── .gitignore
├── app.py                  # Flask 应用主文件
├── Dockerfile              # Docker 镜像构建文件
├── docker-compose.yml      # 本地开发环境
├── docker-compose.prod.yml # 生产环境配置
├── requirements.txt        # Python 依赖
└── test_app.py            # 单元测试
```

## 本地运行

```bash
# 安装依赖
pip install -r requirements.txt

# 运行应用
python app.py

# 运行测试
pytest test_app.py -v

# Docker 本地运行
docker-compose up --build
```

## CI/CD 流水线说明

本项目实现了完整的 CI/CD 流水线：

1. **代码验证 (validate)**：安装依赖 + 运行 pytest 测试
2. **构建镜像 (build)**：构建 Docker 镜像并导出为 tar.gz
3. **部署到 ECS (deploy)**：通过 SCP 传输到阿里云服务器并启动
4. **结果通知 (notify)**：汇总流水线执行结果

## GitHub Secrets 配置

部署到阿里云 ECS 需要配置以下 Secrets：

- `SSH_HOST`：服务器 IP 地址
- `SSH_USERNAME`：SSH 用户名
- `SSH_PRIVATE_KEY`：SSH 私钥

## 环境说明

- **开发环境**：`FLASK_ENV=development`
- **生产环境**：`FLASK_ENV=production`
