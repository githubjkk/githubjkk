# iTop + MySQL 8 Docker 快速启动

## 1. 准备源码
在当前目录下拉取 iTop 到 `itop/` 目录：

```bash
git clone https://github.com/Combodo/iTop.git itop
cd itop
git checkout 3.2.2-1
cd ..
```

## 2. 启动
```bash
docker compose up -d --build
```

## 3. 打开安装向导
浏览器访问：

```text
http://localhost:8080/setup
```

## 4. 安装向导数据库参数
- DB Host: `db`
- DB Name: `itop`
- DB User: `itop`
- DB Password: `itoppass`

## 5. 常用命令

查看日志：
```bash
docker compose logs -f
```

停止：
```bash
docker compose down
```

停止并删除数据库数据：
```bash
docker compose down -v
```

## 6. 说明
- MySQL 8 可用，但 iTop 官方文档明确更推荐 MariaDB。
- 该方案适合测试、演示、POC。
- 生产环境建议额外配置反向代理、TLS、备份、邮件、持久化策略。
