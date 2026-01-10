## 问：netsh interface portproxy add 命令可否一次添加多条？

**直接回答你的问题：**
**不可以。** `netsh interface portproxy add` 这条命令本身**不支持**在单次执行时通过一个命令行添加多条映射规则。

每一个 `add` 命令只能定义一个具体的“监听端口”到“目标地址+端口”的映射关系。

---

### 💡 如何高效地添加多条规则？

虽然命令本身不支持批量，但你可以通过以下几种方式来“批量”处理你的需求，而不需要手动一条条去敲：

#### 1. 编写批处理脚本 (.bat)
这是最简单的方法。你可以把多条命令写在一个 `.bat` 文件里，一次性运行。

**操作步骤：**
1. 新建一个文本文档。
2. 将你的多条命令粘贴进去：
```batch
netsh interface portproxy add v6tov4 listenaddress=2001:db8::100 listenport=8080 connectaddress=192.168.100.104 connectport=80
netsh interface portproxy add v6tov4 listenaddress=2001:db8::100 listenport=8081 connectaddress=192.168.100.103 connectport=80
netsh interface portproxy add v6tov4 listenaddress=2001:db8::100 listenport=9000 connectaddress=192.168.100.102 connectport=9000
```
3. 保存文件，将后缀名改为 `.bat`（例如 `add_port_rules.bat`）。
4. **右键点击该文件，选择“以管理员身份运行”**（这点非常重要，否则会提示权限不足）。

#### 2. 使用 PowerShell 脚本 (更灵活)
如果你熟悉 PowerShell，可以使用循环来生成命令，或者使用更高级的脚本逻辑。

**示例代码：**
```powershell
# 定义规则数组 [监听端口, 目标IP, 目标端口]
$rules = @(
    @(8080, "192.168.100.104", 80),
    @(8081, "192.168.100.103", 80),
    @(9000, "192.168.100.102", 9000)
)

foreach ($rule in $rules) {
    $listenPort = $rule[0]
    $connectAddr = $rule[1]
    $connectPort = $rule[2]
    
    $cmd = "netsh interface portproxy add v6tov4 listenaddress=2001:db8::100 listenport=$listenPort connectaddress=$connectAddr connectport=$connectPort"
    Write-Host "执行: $cmd"
    cmd /c $cmd
}
```

#### 3. 导入/导出配置 (适合迁移)
如果你已经在另一台机器上配置好了，或者手动配置好了一部分，可以使用 `dump` 命令导出当前所有配置，修改后重新导入。

*   **导出当前所有规则：**
    ```cmd
    netsh interface portproxy dump > portproxy_rules.txt
    ```
*   **执行脚本文件（导入）：**
    ```cmd
    netsh -f portproxy_rules.txt
    ```

### ⚠️ 针对你命令的特别提醒

你给出的命令是 `v6tov4`（IPv6 监听转 IPv4 目标），这在 Windows 中是完全支持的，但请注意以下几点：

1.  **协议限制**：`netsh portproxy` **仅支持 TCP 协议**，不支持 UDP。如果你的服务需要 UDP，请不要使用此命令。
2.  **防火墙放行**：添加完端口转发规则后，**必须**在 Windows 防火墙中放行对应的监听端口（如 8080, 8081, 9000），否则外部流量会被防火墙拦截。
    *   *命令示例：*
    `netsh advfirewall firewall add rule name="Allow Port 8080" dir=in action=allow protocol=TCP localport=8080`
3.  **服务依赖**：确保宿主机的 **IP Helper** 服务已启动并设置为自动，否则端口代理可能无法工作。

**总结：** 虽然命令行不支持直接批量添加，但通过写一个简单的脚本文件，你可以瞬间完成几十条规则的部署。