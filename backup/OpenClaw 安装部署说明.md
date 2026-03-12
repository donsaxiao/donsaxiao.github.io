本文档主要介绍 OpenClaw 在 Linux 客户端或者服务器上的安装部署过程。推荐使用开源的 Linux 运维管理面板1Panel 进行安装部署，整个过程简单快捷。OpenClaw安装完成后，也可通过 1Panel 运维管理面板快速完成飞书渠道对接配置，方便大家快速实现基于飞书的个人 AI 助理。

# 资源环境准备
### 本次OpenClaw安装部署需要准备以下资源环境： 
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

# 1Panel安装部署
1Panel安装部署时首先登录到Linux客户端或服务器端，然后按照以下过程快速部署即可：

## 步骤一：获取root权限
登录服务器后首先输入如下命令切换至root权限。
```
sudo su -
```
## 步骤二：执行在线安装命令
输入以下在线安装脚本命令开始执行安装。
```
bash -c "$(curl -sSL https://resource.fit2cloud.com/1panel/package/v2/quick_start.sh) "
```
## 步骤三：指定1Panel安装目录
安装脚本会首先提醒设置指定安装目录，没有特殊情况直接回车选择默认即可。
```
设置1Panel 安装目录（默认为 /opt）：
```
## 步骤四：完成Docker的安装
指定目录后，安装脚本会自动检测当前服务器是否安装Docker，未安装时，会询是否安装，此处输入“y”确认安装即可。
```
检测到未安装 Docker，是否安装[y/n]: y
```
## 步骤五：镜像加速器配置并设置默认参数
Docker安装完成后，会如下图所示会分别提醒是否配置镜像加速并逐步提醒设置1Panel的端口、安全入口以及面板用户密码等，根据个人情况完成设置即可。其中端口需要保证已开通。
