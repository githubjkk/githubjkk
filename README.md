# iTop + MySQL 8 Docker 中文部署手册

这套文件用于在本地或测试环境中快速启动 **iTop + MySQL 8**。  
iTop 官方仓库地址：<https://github.com/Combodo/iTop>

> 说明：iTop 官方支持 MySQL 8，但官方文档更推荐 MariaDB。当前这套配置按你的要求使用 MySQL 8。

---

## 一、目录结构

```text
itop-docker/
├── .env
├── docker-compose.yml
├── Dockerfile
├── .dockerignore
├── README.md
└── itop/                 # 这里放 iTop 源码
```

---

## 二、前置要求

请先确保机器已安装：

- Docker
- Docker Compose Plugin
- Git

可用以下命令检查：

```bash
docker --version
docker compose version
git --version
```

---

## 三、克隆 iTop 源码

在当前项目目录下执行：

```bash
git clone https://github.com/Combodo/iTop.git itop
cd itop
git checkout 3.2.2-1
cd ..
```

如果你想跟踪官方开发分支，也可以改成：

```bash
git clone -b develop https://github.com/Combodo/iTop.git itop
```

说明：
- `3.2.2-1` 适合做稳定测试版本。
- `develop` 是官方当前默认分支，适合跟踪最新开发代码。

---

## 四、配置 `.env`

项目根目录已提供 `.env` 文件，用来避免把密码直接写死在 `docker-compose.yml` 里。

默认内容如下：

```dotenv
COMPOSE_PROJECT_NAME=itop
TZ=Asia/Shanghai

MYSQL_DATABASE=itop
MYSQL_USER=itop
MYSQL_PASSWORD=change_me_itop
MYSQL_ROOT_PASSWORD=change_me_root

ITOP_HTTP_PORT=8080
```

你至少应修改以下两项：

- `MYSQL_PASSWORD`
- `MYSQL_ROOT_PASSWORD`

例如：

```dotenv
MYSQL_PASSWORD=itop_123456
MYSQL_ROOT_PASSWORD=root_123456
```

---

## 五、启动服务

首次启动：

```bash
docker compose up -d --build
```

查看容器状态：

```bash
docker compose ps
```

查看日志：

```bash
docker compose logs -f
```

---

## 六、进入 iTop 安装向导

浏览器访问：

```text
http://localhost:8080/setup
```

如果你在 `.env` 里改了端口，例如：

```dotenv
ITOP_HTTP_PORT=18080
```

那么访问地址就是：

```text
http://localhost:18080/setup
```

---

## 七、安装向导中的数据库参数

在 iTop 安装页填写：

- **DB Host**: `db`
- **DB Name**: `.env` 中的 `MYSQL_DATABASE`
- **DB User**: `.env` 中的 `MYSQL_USER`
- **DB Password**: `.env` 中的 `MYSQL_PASSWORD`

如果你保持默认值，就是：

- DB Host: `db`
- DB Name: `itop`
- DB User: `itop`
- DB Password: `change_me_itop`

---

## 八、常用运维命令

### 1. 停止服务

```bash
docker compose down
```

### 2. 停止并删除数据库卷

> 注意：会删除 MySQL 数据。

```bash
docker compose down -v
```

### 3. 重新构建并启动

```bash
docker compose up -d --build
```

### 4. 查看 Web 容器日志

```bash
docker logs -f itop-web
```

如果你修改了 `COMPOSE_PROJECT_NAME`，容器名也会变化。更稳妥的方式是：

```bash
docker compose logs -f web
```

### 5. 进入 MySQL 容器

```bash
docker exec -it itop-db mysql -uroot -p
```

如果你修改了项目名，建议先执行：

```bash
docker compose ps
```

确认容器名后再进入。

---

## 九、常见问题

### 1. 打开 `/setup` 报 404 或空白页

优先检查：

```bash
docker compose ps
docker compose logs -f web
```

并确认 `itop/` 目录中确实已经有 iTop 源码。

### 2. 数据库连接失败

检查：

- `db` 容器是否已 healthy
- `.env` 中的数据库用户名/密码是否与安装向导填写一致
- 是否误改了服务名 `db`

### 3. 端口冲突

如果 `8080` 被占用，把 `.env` 改成：

```dotenv
ITOP_HTTP_PORT=18080
```

然后重启：

```bash
docker compose up -d
```

### 4. 想升级 iTop 版本

进入源码目录切换 tag：

```bash
cd itop
git fetch --tags
git checkout 3.2.2-1
cd ..
```

然后重启：

```bash
docker compose up -d --build
```

---

## 十、生产环境建议

这套方案适合：

- 本地测试
- 演示环境
- POC 验证

如果要上生产，建议补充：

- Nginx / Traefik 反向代理
- HTTPS 证书
- 备份策略（数据库卷与附件）
- 独立持久化目录
- 邮件发送配置
- 监控与日志采集

---

## 十一、快速命令汇总

```bash
# 1) 克隆 iTop 源码
git clone https://github.com/Combodo/iTop.git itop
cd itop
git checkout 3.2.2-1
cd ..

# 2) 修改 .env 中的密码
vim .env

# 3) 启动
docker compose up -d --build

# 4) 查看状态
docker compose ps

# 5) 安装
# 浏览器打开 http://localhost:8080/setup
```
