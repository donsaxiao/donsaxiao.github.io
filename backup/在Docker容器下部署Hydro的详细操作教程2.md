# 在Docker容器下部署Hydro的详细操作教程

## 1. 为什么选择Docker部署Hydro

Hydro作为新一代高效强大的信息学在线测评系统，通过Docker容器化部署具有以下优势：
- **环境隔离**：确保系统稳定性，避免依赖冲突
- **快速部署**：减少繁琐的环境配置时间
- **资源隔离**：提高运行效率，优化资源使用
- **易于维护**：简化升级和备份流程

## 2. 准备工作与环境要求

在开始部署前，请确保您的系统满足以下条件：
- **操作系统**：Linux（推荐Ubuntu 20.04/22.04）
- **Docker环境**：已安装Docker和Docker Compose
- **磁盘空间**：至少2GB可用空间
- **内存要求**：建议4GB以上
- **网络连接**：稳定的网络连接用于下载依赖
- **权限要求**：具有sudo权限的用户账户

## 3. 详细安装步骤

### 3.1 创建目标目录
```bash
sudo mkdir /hydro
```
系统可能会要求输入密码，输入后按回车确认。

### 3.2 修改文件夹权限（关键步骤）
```bash
sudo chown -R $USER:$USER /hydro
```
此命令将/hydro目录的所有权更改为当前用户，避免后续操作出现权限问题。`$USER`会自动替换为当前用户名。

### 3.3 克隆项目代码
```bash
cd /hydro
git clone https://gitcode.com/gh_mirrors/hy/Hydro.git
```

### 3.4 进入安装目录
```bash
cd Hydro/install/docker
```

### 3.5 启动Docker服务
```bash
docker-compose up -d
```
此过程会自动下载所需镜像并启动服务，根据网络情况可能需要5-30分钟。系统会自动完成以下操作：
- 拉取MongoDB 7镜像
- 拉取Hydro后端服务镜像
- 拉取评测服务镜像
- 配置网络连接
- 启动所有容器

## 4. 常见问题解决方案

### 4.1 端口冲突问题

如果启动时遇到端口占用错误（提示"address already in use"），请按以下步骤解决：

#### 4.1.1 检查占用80端口的进程
```bash
sudo lsof -i :80
# 或
sudo netstat -tuln | grep :80
```

#### 4.1.2 终止占用进程（如果不需要保留）
```bash
sudo kill -9 <PID>  # 替换<PID>为实际进程ID
```

#### 4.1.3 修改Hydro端口映射（如果需要保留现有Web服务）
```bash
nano docker-compose.yml
```
找到`oj-backend`服务的端口配置部分：
```yaml
services:
  oj-backend:
    ports:
      - "80:8888"  # 修改此行
```
将`"80:8888"`修改为`"8088:8888"`（或其他未被占用的端口），然后保存退出（Ctrl+O → 回车 → Ctrl+X）。

重启服务：
```bash
docker-compose up -d
```

### 4.2 设置超级管理员

安装完成后，访问`http://服务器IP:端口号`（如修改过端口，使用修改后的端口，例如8088），注册一个账号。

**注意**：Docker部署下，不能在宿主机直接执行`hydrooj`命令，必须在容器内部执行。以下是正确操作方法：

#### 4.2.1 进入容器执行命令（推荐方法）
```bash
docker exec -it oj-backend sh
```
如果提示`sh`不可用，尝试：
```bash
docker exec -it oj-backend bash
```

#### 4.2.2 设置超级管理员
在容器内部执行：
```bash
hydrooj cli user setSuperAdmin 3
```
**重要提示**：在Docker部署中，新注册的用户ID通常为3，而不是2。UID为2的用户是系统预置的judge账号，不应设置为超级管理员。

#### 4.2.3 退出容器
设置完成后，按`Ctrl+D`或输入`exit`退出容器。

### 4.3 重启服务

在Docker环境中，不需使用`pm2 restart hydrooj`命令，应使用Docker命令重启容器：
```bash
docker restart oj-backend
```

#### 4.3.1 常用Docker管理命令
```bash
# 查看容器状态
docker ps -a

# 查看服务日志
docker logs oj-backend

# 停止服务
docker-compose down

# 启动已停止的服务
docker-compose up -d
```

## 5. 系统架构解析

Hydro的Docker部署包含三个核心服务：

### 5.1 后端服务 (oj-backend)
- 基于Node.js 22构建
- 运行在容器内部8888端口
- 提供核心API和Web界面
- 映射到宿主机的80或自定义端口

### 5.2 测评服务 (oj-evaluator)
- 包含完整的编译器环境
- 支持多种编程语言的代码评测
- 与后端服务通过内部网络通信

### 5.3 数据库服务 (oj-mongo)
- 使用MongoDB 7数据库
- 持久化存储所有系统数据
- 数据卷映射到宿主机的/hydro/mongo-data目录

## 6. 配置详解与优化

### 6.1 端口映射配置
默认配置将宿主机80端口映射到容器8888端口。如需修改，在`docker-compose.yml`中调整：
```yaml
services:
  oj-backend:
    ports:
      - "8088:8888"  # 格式：宿主机端口:容器端口
```

### 6.2 测评服务编译器支持
测评服务默认包含以下编译器/解释器：
- GCC (C语言)
- G++ (C++)
- Python3
- Free Pascal
- OpenJDK 17 (Java)
- PHP CLI
- Rust
- Glasgow Haskell
- JavaScriptCore
- Go语言
- Ruby
- Mono (.NET框架)

### 6.3 数据持久化配置
系统数据通过Docker卷持久化存储：
- MongoDB数据：映射到`/hydro/mongo-data`
- 上传文件：存储在容器内部，建议配置备份策略

## 7. 初始配置与安全提醒

### 7.1 默认账号信息
系统自动创建评测账号：
- **用户名**：judge
- **默认密码**：examplepassword

### 7.2 安全强化措施
1. **修改默认密码**：
   - 登录系统后，进入管理面板
   - 找到judge账号，修改密码
   - 同步更新评测服务配置文件中的密码

2. **配置HTTPS**：
   - 建议使用Nginx反向代理并配置SSL证书
   - 或在docker-compose.yml中配置HTTPS支持

### 7.3 误操作处理指南

如果错误地将UID为2的judge用户设置为超级管理员，执行以下步骤恢复：

1. 进入容器：
   ```bash
   docker exec -it oj-backend sh
   ```

2. 重置为普通用户权限：
   ```bash
   hydrooj cli user setPriv 2 1
   ```

3. 设置正确的评测权限：
   ```bash
   hydrooj cli user setJudge 2
   ```

4. 退出容器并重启服务：
   ```bash
   exit
   docker restart oj-backend
   ```

## 8. 维护与备份策略

### 8.1 定期清理Docker资源
```bash
# 清理无用的容器、网络、镜像
docker system prune -f

# 深度清理（谨慎使用）
docker system prune -a -f
```

### 8.2 数据备份
```bash
# 备份MongoDB数据
docker exec oj-mongo mongodump --out /backup
docker cp oj-mongo:/backup /path/to/backup/

# 恢复数据
docker cp /path/to/backup oj-mongo:/restore
docker exec oj-mongo mongorestore /restore
```

### 8.3 系统升级
```bash
# 拉取最新代码
cd /hydro/Hydro
git pull

# 重建并启动服务
cd install/docker
docker-compose down
docker-compose up -d --build
```

## 9. 验证部署成功

部署完成后，通过浏览器访问`http://服务器IP:端口号`，应能看到Hydro登录界面。使用注册的超级管理员账号登录，进入管理面板，确认以下功能正常：
- 用户管理
- 题目管理
- 比赛创建
- 评测功能测试

至此，您已成功在Docker容器环境下部署了Hydro在线测评系统，可以根据实际需求进一步配置和优化。