很高兴能协助你完成 Hydro 在 Docker 环境下的部署。我深知初次接触命令行和容器化部署可能会让人感到陌生，于是我精心整理出这份**保姆级**的操作指南。请不要被那些代码吓到，只要你按照步骤复制粘贴，就能顺利完成安装。你要知道，这个 Hydro 的安装再难也难不过信奥赛的哪些难题吧！

---

### 🛠️ Hydro 评测系统 Docker 部署保姆级教程

**适用场景**：本教程适用于拥有 Linux 服务器（如 Ubuntu/CentOS）或本地 Linux 环境的用户。如果你是新手，请确保你有一台已经安装好 SSH 工具（如 Xshell、MobaXterm 或 FinalShell）并能连接到 Linux 系统的电脑。

#### 📋 一、 准备工作

在开始之前，请确认你的系统满足以下条件：
1.  **操作系统**：Linux（推荐 Ubuntu 20.04/22.04 或 CentOS 7+）。
2.  **环境要求**：已安装 Docker 和 Docker Compose。
    *   *注：如果未安装，请先搜索“Linux Docker 安装教程”进行环境配置。*
3.  **资源要求**：至少 2GB 可用磁盘空间，稳定的网络连接。

---

#### 🚀 二、 核心安装步骤

请打开你的终端（Terminal），按照以下顺序执行命令。

**1. 创建工作目录**
我们需要在根目录下创建一个名为 `/hydro` 的文件夹。因为是在根目录操作，需要管理员权限。
*   **操作**：复制以下命令并回车：
    ```bash
    sudo mkdir /hydro
    ```
    *系统可能会提示输入密码，输入你的用户密码（输入时不会显示字符，输完直接回车即可）。*

**2. 修改文件夹权限（关键步骤）**
默认创建的文件夹属于 `root` 用户。为了避免后续操作出现“权限被拒绝”的错误，我们需要把文件夹的所有权改为当前用户。
*   **操作**：请先在终端输入 `whoami` 查看你的用户名（假设为 `donsa`），然后复制以下命令（将 `donsa` 替换为你自己的用户名）：
    ```bash
    sudo chown -R donsa:donsa /hydro
    ```
    *或者，你可以直接使用系统变量自动获取用户名（推荐）：*
    ```bash
    sudo chown -R $USER:$USER /hydro
    ```

**3. 下载 Hydro 项目文件**
进入刚才创建的文件夹，并从 GitHub 克隆 Hydro 的安装代码。
*   **操作**：依次执行以下三条命令（每条回车后再执行下一条）：
    ```bash
    cd /hydro
    git clone https://github.com/hydro-dev/Hydro.git
    cd Hydro/install/docker
    ```

**4. 启动 Docker 服务**
这是最激动人心的一步，Docker 会自动下载镜像并启动容器。
*   **操作**：执行以下命令：
    ```bash
    docker-compose up -d
    ```
    *系统会自动运行，这通常需要 5-30 分钟（取决于你的网速），请耐心等待，期间无需任何操作。*

**5. 检查运行状态**
安装完成后，我们需要检查容器是否正常启动。
*   **操作**：执行命令：
    ```bash
    docker ps -a
    ```
    ***如何看结果**：查看 `STATUS` 列。如果看到 `Up` 字样，或者没有出现 `Exited (x) xxx`（退出状态），说明安装成功。*

---

#### ⚠️ 三、 常见问题与注意事项（必读）

在进行下一步之前，你需要了解系统默认生成的一个特殊账号，以及可能遇到的端口冲突问题。

**1. 默认测评账号（非常重要）**
系统在安装过程中，系统会自动注册一个用于测评的账号（UID 为 2）。
*   **用户名**：`judge`
*   **密码**：`examplepassword`
*   **⚠️ 重要提醒**：出于安全考虑，**登录后台，请务必及时修改这个密码**。
    *   修改后，还需同步修改配置文件 `/hydro/Hydro/install/docker/judge/judge.yaml` 中的 `password` 字段。
    *   使用 `nano` 修改配置文件：
        1.  使用编辑器打开配置文件：`nano /hydro/Hydro/install/docker/judge/judge.yaml`
        2.  找到 `password` 将 `examplepassword` **修改成你最新的密码**。
        3.  修改后，保存并退出（按 `Ctrl+O` 写入 -> 回车 -> `Ctrl+X` 退出 -> 输入 `Y` 保存 -> 回车）。
        4.  最后重启对应的 Docker 容器（在 `cd /hydro/Hydro/install/` 目录下用 `docker-compose restart` 重启服务）
    *   **后果**：如果不修改配置文件中的密码，系统将无法进行代码测评。

**2. 端口被占用怎么办？**
如果你在执行 `docker-compose up -d` 时看到 `Error response from daemon` 或 `address already in use` 的错误，说明 80 端口被占用了（通常是 Nginx 或 Apache 占用了）。
```bash
WARN[0000] No services to build                         
[+] up 3/3
 ✔ Container oj-mongo   Healthy                                            6.5s 
 ✔ Container oj-backend Created                                            0.0s 
 ✔ Container oj-judge   Created                                            0.0s 
Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint oj-backend (8a61cca3bd252522c3139b1b8480e36f47a03f5125c968fa1f307c10ab3b3459): failed to bind host port for 0.0.0.0:80:172.19.0.3:8888/tcp: address already in use
```

*   **解决方法 A（推荐）：修改 Hydro 端口**
    1.  进入 Hydro 的 Docker 配置目录：`cd /hydro/Hydro/install/docker`
    2.  使用编辑器打开配置文件：`nano docker-compose.yml`
    3.  找到 `oj-backend` 下的 `ports` 配置。
    4.  将 `- "80:8888"` 修改为 `- "8088:8888"` （意思是把 Hydro 映射到 8088 端口）。
          ```bash
          services:
            oj-backend:
              ports:
                - "8088:8888"  # 原来是 "80:8888"
         ```
    5.  保存退出（按 `Ctrl+O` 写入 -> 回车 -> `Ctrl+X` 退出 -> 输入 `Y` 保存 -> 回车）。
    6.  重新执行 `docker-compose up -d`。
    
*   **解决方法 B：终止占用进程**
    1.  查找占用进程：`sudo lsof -i :80` 或 `sudo netstat -tuln | grep :80`
    2.  记下 PID（进程号），然后终止它：`sudo kill -9 <PID>`

---

#### 🔑 四、 设置超级管理员（新手最容易卡住的地方）

部署完成后，通过浏览器访问 `http://你的IP:端口`（如果改了端口就是 `http://你的IP:8088`）。

在首页中，你会看到提示注册账号，并执行 `hydrooj cli user setSuperAdmin` 将刚注册的用户设置为超级管理员，然后再使用 `pm2 restart hydrooj` 重启以使管理员更改立刻生效。……

**请注意**：如果你直接在终端输入`hydrooj cli user setSuperAdmin` 或 `pm2 restart hydrooj` 这个命令，会提示 **“未找到命令”**。

**原因**：因为你使用的是 Docker 部署，`hydrooj` 命令只存在于容器内部，宿主机（你的 Linux 系统）是找不到的。

**✅ 正确操作步骤：**

1.  **进入容器内部**：
    在终端执行以下命令进入后端容器：
    ```bash
    docker exec -it oj-backend sh
    ```
    *此时你的命令行提示符会变成 `#`，说明你已经进入了容器内部。*

2.  **执行设置命令**：
    假设你刚才注册的新账号 UID 是 3（在用户信息里能看到），在容器内执行：
    ```bash
    hydrooj cli user setSuperAdmin 3
    ```
    *这会将 UID 为 3 的账号设置为超级管理员。*

3.  **关于重启服务**：
    *   **误区**：不要在宿主机上尝试安装 `pm2` 或运行 `pm2 restart`。
    *   **正确做法**：Docker 容器由 Docker 管理。如果需要重启服务，请直接重启容器：
        ```bash
        docker restart oj-backend
        ```

---

#### 🛡️ 五、 恢复误操作指南

如果你不小心把默认的 `judge` 账号（UID 为 2）设置成了超级管理员，会导致安全风险或测评异常。请按照以下步骤恢复：

1.  **进入容器**（同上）：
    ```bash
    docker exec -it oj-backend sh
    ```

2.  **取消超级管理员权限**：
    执行命令将 UID 2 的权限重置为普通用户：
    ```bash
    hydrooj cli user setPriv 2 1
    ```

3.  **恢复测评机权限**：
    重新赋予它测评专用权限：
    ```bash
    hydrooj cli user setJudge 2
    ```

---

#### 🏁 六、 总结

恭喜你！完成以上步骤后，你的 Hydro 评测系统应该已经成功运行在 Docker 容器中了。

*   **访问地址**：`http://你的IP:端口`
*   **核心逻辑**：记住所有 `hydrooj` 开头的命令都需要在 `docker exec` 进入容器后才能执行。
*   **日常维护**：如果服务出现问题，最简单的办法是 `docker restart oj-backend`。

希望这份教程能助你顺利搭建起属于你的测评系统！如有其他问题，请随时提问。