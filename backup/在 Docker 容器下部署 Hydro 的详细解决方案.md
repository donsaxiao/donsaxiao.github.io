Hydro 作为新一代高效强大的信息学在线测评系统，通过 Docker 容器化部署能够实现快速安装和环境隔离。本文将详细介绍Hydro 的 Docker 部署方案，帮助你在容器化环境下实现高效运行（本文同样适合在 1panel 下部署）。

### 为什么选择 Docker 部署 Hydro？
Docker 部署 Hydro 具有多重优势：环境隔离确保系统稳定性，快速部署减少配置时间，资源隔离提高运行效率。特别适合需要快速搭建测评环境的教育机构和竞赛组织者。 

### 准备工作与环境要求
在开始部署前，请确保您的系统满足以下条件：
• Docker环境：已安装Docker和Docker Compose 
• Linux系统：推荐使用Linux操作系统 
• 磁盘空间：至少2GB可用空间 
• 网络连接：稳定的网络连接用于下载依赖

## 快速安装步骤详解

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

---

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
     **修改后，并保存退出**（`Ctrl+O` 写入→ 回车 → `Ctrl+X`退出→ 输入 Y 保存 → 回车）。

4.  重新启动 Docker 服务（安装 Hydro）：
    ```bash
    docker-compose up -d
    ```

### 📌 预防措施
- **避免端口冲突**：如果你的服务器上还有其他 Web 服务（如 Nginx），建议修改 Hydro 的端口映射。可以在 `docker-compose.yml` 文件中将 `80:8888` 改为其他未被占用的端口，例如 `8080:8888`。
- **定期清理**：使用 `docker system prune -a` 可以定期清理所有未使用的容器、网络、镜像和构建缓存，避免残留资源导致冲突。

### 📌 最后提醒
- 如果你选择 **修改 Hydro 端口为 8088** ，在访问 Hydro 时需要加上端口号（例如：`http://你的服务器IP:8088`）。
- 上内容是解决端口被占用的解决方案，如果端口未占用，可以跳过，直接执行下面的步骤。

---

### 📌 特别注意 Ubuntu 和 docker-compose 部署的 Hydro，操作命令是不一样的
安装完成后，在浏览器访问 **http://ip:8088** 时，在首页提示如下：
■ 在右上角注册一个账号。
■ 回到刚刚的终端，使用 `hydrooj cli user setSuperAdmin 2` 将 UID 为 2 的用户设置为超级管理员。 
■ 使用 `pm2 restart hydrooj` 重启以使管理员更改立刻生效。

当你按照首页上的提示，在终端使用 `hydrooj cli user setSuperAdmin 2` 时，会出现 `hydrooj：未找到命令`

这是一个非常典型的新手误区。简单来说：**你试图在宿主机（你的 Ubuntu 系统）上执行只有在 Hydro 容器内部才能识别的命令。**

因为你使用的是 `docker-compose` 部署，所有的服务（包括 Node.js 运行时和 pm2 进程管理器）都封装在隔离的容器里。宿主机（你现在操作的终端）并不认识 `hydrooj` 或 `pm2` 这些命令。

请按照以下步骤解决：

---

### 方法一：进入容器执行（推荐）

你需要先进入 `oj-backend` 容器的内部，才能执行这些命令。

1.  **进入容器**
    复制并执行以下命令，进入 Hydro 的后端容器：
    ```bash
    docker exec -it oj-backend sh
    ```
    *   提示：如果报错 `sh` 不可用，可以尝试 `bash`（虽然该镜像通常只包含轻量级的 `sh`）。

2.  **执行命令**
    进入容器后，你会看到命令行提示符变成了类似 `/app #`。此时直接执行初始化命令：
    ```bash
    hydrooj cli user setSuperAdmin 2
    ```
    *   **注意**：这里的 `2` 是指用户的 ID。但值得注意的是在 Docker 容器下部署的 Hydro，用户 ID 往往是 `3` 而不是 `2`。因为用户 ID 2 是系统内已经存在的 judge，请改为：
        ```bash
        hydrooj cli user setSuperAdmin 3
        ```

---

### 方法二：如果必须在宿主机运行（使用完整路径）

如果你不想进入容器，也可以通过 `docker exec` 在容器内部直接运行单条命令。

**设置超级管理员**
执行以下命令（同样建议先试 `2`，再试 `3`）：
```bash
docker exec oj-backend hydrooj cli user setSuperAdmin 3
```

**关于 `pm2` 的说明**
在 Docker 部署中，**不需要**手动重启 `pm2`。
*   容器内的进程是由 Docker 管理的（通过 `entrypoint.sh` 启动）。
*   如果你需要重启服务，请重启整个容器，而不是容器内部的进程：
    ```bash
    docker restart oj-backend
    ```

### 总结
*   **不要**在宿主机（Ubuntu 终端）安装 `pm2` 或 `hydrooj`，它们属于容器内部环境。
*   **所有**涉及 `hydrooj` 的管理命令，都必须在 `docker exec` 进入容器后执行。

---

## ❓ 聪明你的会问：
### 既然 Docker 不需要手动重启 pm2，那 `pm2 restart hydrooj` 这个命令在 Docker 中如何替代”?

<html>
<body>
<!--StartFragment--><html><head></head><body><p>在 Docker 部署中，确实<strong>不需要</strong>（通常也<strong>无法</strong>）在容器内部手动执行 <code>pm2 restart hydrooj</code>。这是因为 Docker 容器的设计理念是“不可变基础设施”，服务的生命周期由 Docker 本身管理，而不是容器内的进程管理器。</p>
<p>要替代 <code>pm2 restart hydrooj</code> 的功能（即重启 Hydro 服务），你应该直接重启 Docker 容器本身。</p>
<h3>✅ 最佳替代方案：重启容器</h3>
<p>使用以下命令来重启 <code>oj-backend</code> 容器：</p>
<pre><code class="language-bash">docker restart oj-backend
</code></pre>
<p><strong>为什么这样可行？</strong></p>
<ol>
<li><strong>自动拉起</strong>：你的 <code>docker-compose.yml</code> 文件中配置了 <code>restart: always</code>（或者类似策略），这意味着只要容器停止（无论是正常停止还是崩溃），Docker 都会自动尝试重新启动它。</li>
<li><strong>重新初始化</strong>：<code>docker restart</code> 会先优雅地停止容器，然后根据配置重新运行启动命令（通常是 <code>entrypoint.sh</code> 或直接运行 <code>hydrooj</code>）。这相当于在容器内部重启了整个应用进程，效果等同于重启 PM2 或 Node.js 进程。</li>
</ol>
<hr>
<h3>🛠️ 补充：如果你必须进入容器内部操作</h3>
<p>虽然不推荐，但如果你确实需要进入容器内部查看 PM2 状态或进行调试（例如确认进程是否挂起），可以使用以下命令：</p>
<ol>
<li><strong>进入容器终端</strong>：<pre><code class="language-bash">docker exec -it oj-backend sh
</code></pre>
</li>
<li><strong>在容器内部执行 PM2 命令</strong>（仅限调试）：<br>如果你确认容器内安装了 PM2 且你想手动控制：<pre><code class="language-bash">pm2 restart hydrooj
</code></pre>
<em>注意：大多数轻量级 Docker 镜像为了精简体积，可能根本没装 <code>pm2</code> 命令行工具，或者它不在 PATH 环境变量中。因此，直接在宿主机上使用 <code>docker restart</code> 是最稳妥、最符合 Docker 规范的做法。</em></li>
</ol>
<h3>📌 总结</h3>

操作目标 | 宿主机（你的 Ubuntu）命令 | 说明
-- | -- | --
重启 Hydro 服务 | docker restart oj-backend | 推荐做法。利用 Docker 的生命周期管理，最稳定。
查看服务日志 | docker logs oj-backend | 替代 pm2 logs，查看输出内容。
进入容器调试 | docker exec -it oj-backend sh | 只有在排查复杂问题时才需要进入内部。


<p>所以，下次你想“重启服务”时，只需敲下 <code>docker restart oj-backend</code> 即可。</p>
</body></html><!--EndFragment-->
</body>
</html>

---
以上内容是 Donsa 原创，请注意版权，复制时必须保留我的版权声明！