要彻底卸载通过 Docker 部署的 Hydro 评测系统，我们需要执行一系列有序的操作，以确保不仅停止并移除了容器，还清理了所有相关的数据、配置和镜像，让系统恢复到部署前的状态。

请严格按照以下步骤操作，避免遗漏。

### 🛑 第一步：停止并移除 Docker 容器

首先，我们需要进入存放 `docker-compose.yml` 文件的目录，并停止、移除容器。

1.  打开终端，进入 Hydro 的 Docker 配置目录（假设你的 Hydro 是在 `/hydro` 目录下部署的）：
    ```bash
    cd /hydro/Hydro/install/docker
    ```
2.  停止并移除容器：
    ```bash
    docker-compose down
    ```
    *   这条命令会根据 `docker-compose.yml` 文件，优雅地停止并移除所有相关的容器、网络。

### 🗑️ 第二步：删除所有相关数据

这一步非常关键，它会删除题目、代码、数据库等所有持久化的数据。

1.  **删除工作目录**：这会移除所有下载的文件、配置和数据卷。
    ```bash
    sudo rm -rf /hydro
    ```
2.  **（可选）清理残留卷**：如果在第一步中没有正确删除，可能会有孤立的 Docker 卷残留。可以运行以下命令查看并清理：
    ```bash
    # 列出所有卷，查找与 hydro 相关的
    docker volume ls
    # 如果有，例如名为 "hydro_data" 的卷，可以这样删除
    docker volume rm hydro_data
    ```

### 🧹 第三步：清理 Docker 资源

容器停止并删除后，它们使用的镜像和网络可能还会保留在系统中，我们需要手动清理。

1.  **删除镜像**：列出所有镜像，找到与 `hydro` 相关的（例如 `hydrooj/hydro` 或类似名称），然后删除。
    ```bash
    # 列出镜像
    docker images
    # 删除镜像，<image_id_or_name> 替换为实际的镜像ID或名称
    docker rmi <image_id_or_name>
    ```
2.  **清理网络（可选）**：Docker 会为 compose 项目创建自定义网络。通常 `docker-compose down` 会处理，但如果仍有残留，可以手动清理未使用的网络：
    ```bash
    docker network prune
    ```

### 🔍 第四步：验证卸载结果

完成以上步骤后，可以通过以下命令验证是否还有残留：

*   查看容器：`docker ps -a` (不应再看到 `oj-backend` 或 `hydro` 相关的容器)
*   查看目录：`ls /hydro` (应提示 "No such file or directory")

### 🗑️  第五步：删除源码目录或配置文件的备份目录

前面执行的 `sudo rm -rf /hydro` 只删除了根目录下的挂载点数据，而 `/root/Hydro` 通常是源代码克隆目录或配置文件的备份目录。为了彻底卸载，我们还需要把这个目录也删除。

请按照以下步骤操作，确保彻底清理：

#### 1. 删除残留的源码目录

在终端执行以下命令，删除 `/root` 目录下的 `Hydro` 文件夹：

```bash
sudo rm -rf /root/Hydro
```

#### 2. 验证目录是否已删除

你可以通过以下命令检查目录是否还存在（如果不存在，终端不会有任何输出，这是正常的）：

```bash
ls -la /root/Hydro
```

### 3. （可选）检查是否还有其他相关文件

有时候在 `/home` 目录或其他地方也可能有相关文件。如果你记得安装时的操作路径，建议也检查一下：

```bash
# 检查你的家目录（如果是 root 用户，家目录就是 /root）
ls -la ~ | grep -i hydro
```

### 4. 最终确认

至此，你应该已经完成了以下清理工作：
*   **容器**：已通过 `docker-compose down` 停止并移除。
*   **数据**：已通过 `rm -rf /hydro` 删除核心数据卷。
*   **源码/配置**：已通过 `rm -rf /root/Hydro` 删除源码目录。


完成以上这些步骤后，Hydro 及其所有数据就已从你的系统中彻底移除了。