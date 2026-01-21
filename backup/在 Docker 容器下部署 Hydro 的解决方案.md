
### 1. 创建目标目录
首先，你需要创建 `/hydro` 文件夹。因为这是在根目录下创建文件夹，需要管理员权限：

```bash
sudo mkdir /hydro
```
*(系统可能会要求你输入密码，输入后按回车即可)*

### 2. 修改文件夹权限（关键步骤）
默认情况下，`sudo mkdir` 创建的文件夹属于 `root` 用户。但你日常运行 Docker 或者操作代码通常使用的是普通用户（比如你的 `donsa` 用户）。为了避免后续操作出现“权限被拒绝”的错误，建议将该文件夹的所有者改为你的当前用户。

假设你的用户名是 `donsa`，请运行：

```bash
sudo chown -R donsa:donsa /hydro
```
*(注意：请将 `donsa:donsa` 替换为你实际的用户名，冒号前面是用户，后面是组)*

### 3. 克隆项目代码
现在进入 `/hydro` 目录并执行命令克隆Hydro项目到本地：

```bash
cd /hydro
git clone https://gitcode.com/gh_mirrors/hy/Hydro.git
```

### 4. 进入安装目录
下载完成后，进入 Docker 安装目录：

```bash
cd Hydro/install/docker
```

### 5. 启动 Docker 服务
执行启动命令：

```bash
docker-compose up -d
```

这个过程会根据网络情况自动搭建，大约需要5-30分钟，全程无需人工干预。

-------------------------------------------------------------------------------------------------------

### 💡 补充说明
- **如果提示端口占用**：显示类似下面的内容
```
WARN[0000] No services to build                         
[+] up 3/3
 ✔ Container oj-mongo   Healthy                                            6.5s 
 ✔ Container oj-backend Created                                            0.0s 
 ✔ Container oj-judge   Created                                            0.0s 
Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint oj-backend (8a61cca3bd252522c3139b1b8480e36f47a03f5125c968fa1f307c10ab3b3459): failed to bind host port for 0.0.0.0:80:172.19.0.3:8888/tcp: address already in use
```
这说明你遇到的问题是 **端口冲突**，具体表现为 `oj-backend` 容器尝试绑定主机的 80 端口时失败，提示 `address already in use`。虽然你已经执行了 `docker-compose down` 和网络清理，但系统中仍有残留的进程或服务占用了 80 端口。

### 解决方法

#### 1. 查找并终止占用 80 端口的进程
运行以下命令查看是哪个进程在占用 80 端口：
```bash
sudo lsof -i :80
```
或
```bash
sudo netstat -tuln | grep :80
```
输出结果中会显示 PID（进程 ID）和进程名。例如，如果看到 `nginx` 或 `apache2` 或另一个 `docker-proxy` 进程，你可以终止它：
```bash
sudo kill -9 <PID>
```
（将 `<PID>` 替换为实际的进程 ID）

#### 2. 修改 Hydro 的端口（如果需要保留 `Nginx`）
如果你必须保留 `Nginx` 占用 80 端口，可以修改 Hydro 的端口映射（例如将 80 改为 8080或8088）：
1.  进入 Hydro 的 Docker 配置目录：
    ```bash
    cd /hydro/Hydro/install/docker
    ```
2.  编辑 `docker-compose.yml` 文件：
    ```bash
    nano docker-compose.yml
    ```
3.  找到 `oj-backend` 的 `ports` 配置，将 `80:8888` 改为 `8088:8888`（或其他未被占用的端口）：
    ```yaml
    services:
      oj-backend:
        ports:
          - "8088:8888"  # 原来是 "80:8888"
    ```
4.  修改并保存和退出（`Ctrl+O` → 回车 → `Ctrl+X`→ 输入 Y 保存 → 回车），然后重新启动 Docker 服务（安装 Hydro）：
    ```bash
    docker-compose up -d
    ```

### 📌 预防措施
- **避免端口冲突**：如果你的服务器上还有其他 Web 服务（如 Nginx），建议修改 Hydro 的端口映射。可以在 `docker-compose.yml` 文件中将 `80:8888` 改为其他未被占用的端口，例如 `8080:8888`。
- **定期清理**：使用 `docker system prune -a` 可以定期清理所有未使用的容器、网络、镜像和构建缓存，避免残留资源导致冲突。

### 📌 最后提醒
- 如果你选择 **修改 Hydro 端口为 8088** ，在访问 Hydro 时需要加上端口号（例如：`http://你的服务器IP:8088`）。
- 上内容是解决端口被占用的解决方案，如果端口未占用，可以跳过，直接执行下面的步骤。

-------------------------------------------------------------------------------------------------------

现在你可以根据需求选择合适的方法，释放 80 端口后重新启动 Hydro 即可。
