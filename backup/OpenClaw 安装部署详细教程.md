最近群里对 OpenClaw 的安装和使用成了热门话题。出于热心我整理了这份详细的教程供西大同学们参考，希望能帮助你快速进入 AI 的大门。

本文档主要介绍 OpenClaw 在 Linux 客户端或者服务器上的安装部署过程。**推荐使用开源的 Linux 运维管理面板 1Panel 进行安装部署**，整个过程简单快捷。OpenClaw 安装完成后，也可通过 1Panel 运维管理面板快速完成飞书渠道对接配置，方便大家快速实现基于飞书的个人 AI 助理。

如果你已具备相应的能力，也可以使用其他方法安装，请参考 OpenClaw 的官方文档：https://docs.openclaw.ai

# 一、资源环境准备
### 本次 OpenClaw 安装部署需要准备以下资源环境： 
• 客户端或服务器准备： 
• 操作系统：支持主流 Linux 发行版本（基于 Debian / RedHat，包括国产操作系统） 
• 服务器架构：x86_64、aarch64 
• 内存要求：建议可用内存在 2GB 以上 
• 浏览器要求：请使用 Chrome、FireFox、IE10+、Edge 等现代浏览器 
• 网络要求：可访问互联网 
• 大语言模型API key 
• 公有模型：如DeepSeek 、Kimi、OpenAI等大语言模型 API Key 
• 本地模型 
• 1Panel v2.1.0 安装部署： 
• 可参考1Panel官方文档完成安装部署。具体参见如下：
https://1panel.cn/docs/v2/installation/online_installation/

基于以上环境，我们可以分为1Panel安装部署、大语言模型API Key准备、OpenClaw安装部署以及飞书渠道配置四部分完成整个安装部署过程，具体参见如下详细说明文档。

# 二、1Panel 安装部署
1Panel安装部署时首先登录到Linux客户端或服务器端，然后按照以下过程快速部署即可：

## 步骤 2.1：获取 root 权限
登录服务器后首先输入如下命令切换至 root 权限。
```
sudo su -
```
## 步骤 2.2：执行在线安装命令
输入以下在线安装脚本命令开始执行安装。
```
bash -c "$(curl -sSL https://resource.fit2cloud.com/1panel/package/v2/quick_start.sh) "
```
## 步骤 2.3：指定 1Panel 安装目录
安装脚本会首先提醒设置指定安装目录，没有特殊情况直接回车选择默认即可。
```
设置1Panel 安装目录（默认为 /opt）：
```
## 步骤 2.4：完成 Docker 的安装
指定目录后，安装脚本会自动检测当前服务器是否安装Docker，未安装时，会询是否安装，此处输入“y”确认安装即可。
```
检测到未安装 Docker，是否安装[y/n]: y
```
## 步骤 2.5：镜像加速器配置并设置默认参数
Docker安装完成后，会如下图所示会分别提醒是否配置镜像加速并逐步提醒设置1Panel的端口、安全入口以及面板用户密码等，根据个人情况完成设置即可。其中端口需要保证已开通。

<img width="1268" height="761" alt="Image" src="https://github.com/user-attachments/assets/9c3e4239-53e4-4ab6-8058-6060eb7e7666" />

## 步骤 2.6：
获取 1Panel 面板的登录信息 统一设置完成后，系统会自动打印出 1Panel 的登录访问信息，如下图所示，大家保存方便后续使用。  

<img width="1268" height="707" alt="Image" src="https://github.com/user-attachments/assets/b644d916-3357-466d-8939-576d5fee2420" />

## 步骤 2.7：
验证 1Panel 部署成功 将步骤六中获取的外部地址输入浏览器即可进入 1Panel 面板登录页面，输入对应的面板用户和密码，确认 1Panel 已经安装部署完成。  

<img width="1268" height="761" alt="Image" src="https://github.com/user-attachments/assets/9b813094-3c96-4864-a276-6dbffaa43e92" />

## 步骤 2.8：
1Panel访问地址设置 进入面板后，切换到“面板设置”页面，将默认访问地址设置为1Panel访问的公网IP，方便后续部署的应用可以通过点击“跳转”按钮进行访问。  

<img width="1048" height="875" alt="Image" src="https://github.com/user-attachments/assets/c62dfb8c-2337-40b0-80f3-01549017ccbb" />

## 步骤 2.9：
升级到V2.1.0最新版本（可选） 对于已有1Panel，登录面板后，点击右下角更新，将当前1Panel更新升级到v2.1.0版本。   

<img width="1269" height="727" alt="Image" src="https://github.com/user-attachments/assets/73bcce08-70c1-48ac-a483-ab7df24dab6c" />


# 三、大语言模型 API 申请
大家可以参加 OpenClaw 官网支持的模型列表，分别获取大语言模型的 API Key，本次以 DeepSeek 为例。其它公有模型和本地模型大家自行参考相关文档准备。 

## 步骤 3.1：
DeepSeek 开发者平台注册 进入DeepSeek 开发者平台：```https://platform.deepseek.com/ ```，首先完成个人实名认证并注册，并完成充值。如下图所示：

<img width="1069" height="620" alt="Image" src="https://github.com/user-attachments/assets/b783864e-6b79-4f9d-98cb-54abd0c34072" />

## 步骤 3.2：
获取 DeepSeek 的 API key 如下图所示，进入API keys，点击创建 API key，创建完成后将 API Key 妥善保管，方便后续使用

<img width="1267" height="578" alt="Image" src="https://github.com/user-attachments/assets/6dd61aa0-3864-4e7f-9156-fafdb38a0782" />


# 四、OpenClaw 安装部署
OpenClaw 的安装基于1Panel的智能体管理进行部署，在部署过程中我们需要结合上一步骤 DeepSeek 大模型的 API key。在1Panel 中将 OpenClaw 的部署以及大模型 API key 账号的管理分为两部分进行，将二者进行了解绑，主要是为了方便大家进行 OpenClaw 个人助理的模型配置调整。 

## 步骤 4.1：
添加模型账号 进入「AI」管理中的「智能体」管理菜单，点击进入后首先切换进入「模型账号」管理，点击「添加模型账号」，然后按照模型账号创建要求选择模型供应商并输入模型账号信息，完成模型账号创建。

<img width="1265" height="545" alt="Image" src="https://github.com/user-attachments/assets/89ef633d-3439-419e-a656-4eb97ddf9443" />

## 步骤 4.2：
创建智能体 准备好模型账号后，切换进入「智能体」，点击创建智能体，根据要求输入相关参数即可。具体参见如下图所示：

<img width="1269" height="736" alt="Image" src="https://github.com/user-attachments/assets/7cd3c7ba-31d4-458b-a991-bce8f99f6f01" />

其中创建智能体过程中的参数定义参见如下详细说明： 
• 名称：默认openclaw，可以自定义作为容器的名称； 
• 应用版本：指 OpenClaw 的版本，如上图默认为当前最新的版本2026.2.9； 
• WebUI 端口：OpenClaw 默认的端口为18789，可自行定义端口且确保已开通； 
• Bridge 端口：OpenClaw 默认的端口为18790，可自行定义端口且确保已开通； 
• 模型供应商：当前支持的模型如图所示，这里选择刚才添加的 DeepSeek 模型供应商，如果已添加对应模型供应商的模型，则会直接提醒模型账号数量；

<img width="1064" height="736" alt="Image" src="https://github.com/user-attachments/assets/ac59b423-5ed3-4daf-9c64-e67813e19cc4" />

• 其他模型参数：
选择模型供应商后，系统会自动获取已经维护的模型账号，如下图所示，也可勾选「手动输入模型」自行输入，如未创建模型账号，点击「创建模型账号」完成创建即可。

<img width="1154" height="1038" alt="Image" src="https://github.com/user-attachments/assets/79688605-077c-4f1c-ae84-95c68c2b773c" />

• Token：为 OpenClaw 的 Web UI 访问地址的 token，这里系统会自动生成，方便后续直接访问。 
• 其他参数：其他参数均保持默认配置即可。  
以上参数配置完成后，直接点击确认，OpenClaw 开始安装。直到如下图所示，代表完成安装。

<img width="933" height="645" alt="Image" src="https://github.com/user-attachments/assets/a7b1a7a0-066a-405d-8705-cb31c07fb184" />

## 步骤 4.3：
验证 OpenClaw 部署成功 OpenClaw 安装部署完成后，进入智能体列表页面，如下图所示，点击 WebUI 直接跳转即可进入OpenClaw页面。

<img width="1049" height="369" alt="Image" src="https://github.com/user-attachments/assets/d875c833-aa58-42f7-8ed0-3b1934665d0c" />

进入 OpenClaw 页面后，输入信息看AI助理是否回复，如有正常回复，如下图所示，说明确认 OpenClaw 部署成功。

<img width="1044" height="648" alt="Image" src="https://github.com/user-attachments/assets/0c9d2743-4580-4cb5-a027-413939378e7a" />


# 五、飞书渠道配置
**截止目前，整个 OpenClaw 已经完全部署完成**，我们接着在配置下飞书渠道。配置飞书渠道我们首先需要在飞书中创建一个可用的机器人，参照如下步骤逐步完成即可。 说明：本次介绍使用的是飞书个人账号，如果是企业账号则版本发布以及权限授权需要进行管理员审批，其他操作步骤一致。 

## 步骤 5.1：
创建企业自建应用 首先我们登录飞书进入飞书开放平台（链接：``` https://open.feishu.cn/app?lang=zh-CN ```）进入「开发者后台」，选择「企业自建应用」，点击「创建企业自建应用」，如下图所示：

<img width="1054" height="651" alt="Image" src="https://github.com/user-attachments/assets/3a0246d1-32fd-4159-b6e3-77800ac07072" />

如下图所示，根据要求输入相关应用名称以及基本信息点击创建即可。

<img width="802" height="697" alt="Image" src="https://github.com/user-attachments/assets/779d7859-fc9f-42dc-a7ed-2886965270c7" />

## 步骤 5.2：
创建机器人 如下图所示，点击创建机器人，完成机器人创建。

<img width="1266" height="829" alt="Image" src="https://github.com/user-attachments/assets/78982fbe-937e-4a36-bb73-2d6479c3e62d" />

进入机器人页面后，点击机器人配置后的编辑按钮，定义机器人名称，如下图所示：

<img width="1268" height="817" alt="Image" src="https://github.com/user-attachments/assets/5de74678-93a9-4367-9764-230dd4378c89" />

## 步骤 5.3：
权限配置 完成机器人创建后，点击进入「权限管理」，并点击「批量导入/导出权限」。

<img width="1268" height="814" alt="Image" src="https://github.com/user-attachments/assets/bc217f3c-48ce-47b4-ad63-5438a35e079c" />

点击「批量导入/导出权限」，清空默认配置权限信息，将如下所示权限授权脚本复制粘贴，点击保存即可。
```bash
{
  "scopes": {
    "tenant": [
      "aily:file:read",
      "aily:file:write",
      "application:application.app_message_stats.overview:readonly",
      "application:application:self_manage",
      "application:bot.menu:write",
      "cardkit:card:write",
      "contact:contact.base:readonly",
      "contact:user.employee_id:readonly",
      "corehr:file:download",
      "docs:document.content:read",
      "event:ip_list",
      "im:chat",
      "im:chat.access_event.bot_p2p_chat:read",
      "im:chat.members:bot_access",
      "im:message",
      "im:message.group_at_msg:readonly",
      "im:message.group_msg",
      "im:message.p2p_msg:readonly",
      "im:message:readonly",
      "im:message:send_as_bot",
      "im:resource",
      "sheets:spreadsheet",
      "wiki:wiki:readonly"
    ],
    "user": [
      "aily:file:read",
      "aily:file:write",
      "contact:contact.base:readonly",
      "im:chat.access_event.bot_p2p_chat:read"
    ]
  }
}
```

以上代码复制粘贴后，效果如下图所示：

<img width="1269" height="1065" alt="Image" src="https://github.com/user-attachments/assets/ff71dbd4-a5c4-480e-a3ac-75eabab4cb57" />

点击「下一步，确认新增权限」，并最终确保所有权限均已开通。其中个人用户自行确认企业账户需要管理员审核。确保所有权限均已开通，如下图所示：

<img width="1268" height="787" alt="Image" src="https://github.com/user-attachments/assets/7388726e-e6fd-4085-a6b0-ca2c6ef742ac" />

## 步骤 5.4：
获取凭证并 1Panel 配置 进入飞书平台在「凭证与基础信息」中获取应用凭证，如下图所示：

<img width="1267" height="616" alt="Image" src="https://github.com/user-attachments/assets/1ef71d6f-e11c-4db5-9f15-a61eb723f786" />

获取后，进入 1Panel 的「智能体」的「配置」页面，完成飞书聊天渠道配置，点击保存，如下图所示：

<img width="1266" height="746" alt="Image" src="https://github.com/user-attachments/assets/5d577c80-0347-4d1b-9400-3b10748584ff" />

## 步骤 5.5：
创建事件与回调 如下图所示，进入「事件与回调」菜单，分别完成订阅方式设置以及事件添加。

<img width="1197" height="694" alt="Image" src="https://github.com/user-attachments/assets/1cf82f61-5c63-4407-833c-d3e14cee29d3" />

<img width="1197" height="694" alt="Image" src="https://github.com/user-attachments/assets/ab12bc7f-6b51-4266-b9d7-7cbdf0766381" />

#### 订阅方式设置：
参照如下图选择长链接订阅方式：

<img width="1267" height="574" alt="Image" src="https://github.com/user-attachments/assets/84c8f44e-b06b-4bad-8557-eff7133a6255" />

#### 添加事件：
输入 ```im.message.receive_v1``` 搜索，基于「应用身份订阅」勾选接收消息，最后确认添加即可。

<img width="937" height="803" alt="Image" src="https://github.com/user-attachments/assets/23567245-56f1-4bca-a9c7-f85f9f02cc96" />

## 步骤 5.6：
创建并发布版本 确认完成后点击「创建版本」，然后根据要求输入版本相关信息并发布，个人账号无需审批，企业账号需要进行企业审批。

<img width="1266" height="912" alt="Image" src="https://github.com/user-attachments/assets/30c39858-c729-4d06-9276-ca71a84e7909" />

## 步骤 5.7：
飞书渠道验证确认 以上信息配置完成后，我们进入飞书客户端，如下图打开应用：

<img width="1268" height="736" alt="Image" src="https://github.com/user-attachments/assets/f9ead217-7263-4859-a4ee-994192239483" />

最后我们再进行简单尝试，如下图所示即代表配置成功。

<img width="1266" height="738" alt="Image" src="https://github.com/user-attachments/assets/f9931135-a2fa-46f5-a261-656eb90bfde9" />


# 六、模型配置修改
如果在 OpenClaw 个人 AI 助理使用时，需要切换模型，同样进入「智能体」列表，点击「配置」，进入模型切换菜单，完成模型配置修改点击「保存」即可。如下图所示：

<img width="1265" height="396" alt="Image" src="https://github.com/user-attachments/assets/b5a315a2-18b3-43d6-92e1-79e44b8243d2" />