---
date create: 2025-05-14 21:29:22 周三
date created: 2025-05-14
date modified: 2026-08-03
---

|文档类别|查看方式|内容简介|
|---|---|---|
|`ssh` 命令|`man ssh`|客户端使用手册（连接方式等）|
|配置文件（客户端）|`man ssh_config`|`~/.ssh/config` 格式说明|
|配置文件（服务端）|`man sshd_config`|`/etc/ssh/sshd_config` 配置说明|
|SSH Agent|`man ssh-agent`|如何运行/使用密钥代理|
|添加密钥|`man ssh-add`|如何向代理添加密钥|
|创建密钥|`man ssh-keygen`|如何生成/管理 SSH 密钥对|

你可以随时输入如 `man ssh_config` 来查看客户端配置文件的官方详解。

二、常用 SSH 相关命令一览

|命令|作用|示例|
|---|---|---|
|`ssh`|远程登录主机|`ssh user@host`|
|`ssh-keygen`|生成 SSH 密钥对|`ssh-keygen -t ed25519`|
|`ssh-copy-id`|将本地公钥复制到远程机器（快捷登录）|`ssh-copy-id user@host`|
|`ssh-agent`|启动密钥代理守护进程|`eval "$(ssh-agent -s)"`|
|`ssh-add`|添加密钥到 `ssh-agent`|`ssh-add ~/.ssh/id_ed25519`|
|`scp`|安全复制文件（SCP协议）|`scp file user@host:/path/`|
|`sftp`|安全文件传输|`sftp user@host`|
|`sshd`|SSH 服务端守护进程（Linux服务）|`sudo systemctl start sshd`|


三、SSH 配置文件详解

3.1 用户端配置文件 `~/.ssh/config`

可以针对不同主机定制连接规则，格式如下：

```ini
# 默认配置
Host *
  ForwardAgent no
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519

# 特定主机
Host github.com
  HostName ssh.github.com
  User git
  Port 443
  IdentityFile ~/.ssh/id_ed25519_github
```

|关键词|含义|
|---|---|
|`Host`|主机匹配规则，可使用通配符|
|`HostName`|实际连接地址（可不同于别名）|
|`Port`|指定端口（默认是22）|
|`User`|登录用户名|
|`IdentityFile`|指定密钥路径|
|`ProxyJump`|使用跳板机连接|
|`ForwardAgent`|是否转发 agent|
|`StrictHostKeyChecking`|是否严格验证主机公钥|

---

3.2 服务端配置文件 `/etc/ssh/sshd_config`

管理员用于配置 SSH 服务行为，例如：

```ini
Port 22
PermitRootLogin no
PasswordAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
AllowUsers user1 user2
```

常见选项说明：

|选项|说明|
|---|---|
|`Port`|SSH 服务监听的端口|
|`PermitRootLogin`|是否允许 root 登录|
|`PasswordAuthentication`|是否允许密码登录|
|`PubkeyAuthentication`|是否启用密钥登录|
|`AuthorizedKeysFile`|公钥认证用的文件路径|

四、测试 SSH 连接的常用操作

4.1 测试连接并显示调试信息
```bash
ssh -vT git@github.com
```
加 `-v` 参数可以查看详细握手过程，最高支持 `-vvv`。

 4.2 检查是否能连接远程主机
```bash
ssh user@hostname
```
若成功进入 shell，则网络/认证/配置都没问题。

4.3 检查密钥是否已加载进 ssh-agent
```bash
ssh-add -l
```
若无输出或提示“agent 没有 identities”，需要 `ssh-add ~/.ssh/id_ed25519`

五、SSH 技术细节结构总结

|层级|模块|说明|
|---|---|---|
|应用层|`ssh`, `scp`, `git@ssh`|用户工具|
|会话层|`ssh-agent`, `ssh-add`, `ssh-keygen`|身份认证与密钥管理|
|协议层|SSH-2 (RFC 4251~4254)|协议标准，包含认证、连接、多路复用等子协议|
|传输层|TCP|默认端口 22，数据加密传输|

六、SSH 所支持的认证方式

|方式|描述|
|---|---|
|Password|用户输入系统登录密码|
|PublicKey|用密钥登录，安全可靠|
|HostBased|根据主机信任关系（较少用）|
|Keyboard-interactive|多因子、验证码等（可拓展）|


七、实用配置建议示例

多平台 GitHub / GitLab / 自建服务器

```ini
# GitHub（端口443，适配校园网）
Host github.com
  HostName ssh.github.com
  Port 443
  User git
  IdentityFile ~/.ssh/id_ed25519_github

# GitLab
Host gitlab.com
  User git
  IdentityFile ~/.ssh/id_ed25519_gitlab

# 自建服务器
Host myserver
  HostName 123.123.123.123
  Port 2222
  User aiden
  IdentityFile ~/.ssh/id_rsa_server
```

八、如何学习 SSH 更深入的技术细节？

推荐文档和资源：
- OpenSSH 官方手册：[https://man.openbsd.org/ssh](https://man.openbsd.org/ssh)
- IETF RFC 4251~4254：[https://datatracker.ietf.org/doc/html/rfc4251](https://datatracker.ietf.org/doc/html/rfc4251)
- Arch Wiki SSH：[https://wiki.archlinux.org/title/SSH](https://wiki.archlinux.org/title/SSH)
- GitHub SSH 指南：[https://docs.github.com/en/authentication/connecting-to-github-with-ssh](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)


如果你想要：
- `~/.ssh/config` 模板合集（多账户、跳板机、代理等）
- ssh-agent 自动化配置脚本
- 安全加固建议
- OpenSSH 服务自建方案
我可以帮你快速整理。你也可以贴出你的实际用途，我来帮你定制配置 


###### 底部



###### termux 删除重装后再用 ssh 连接，出现下面的问题
（缘由 [[Termux#2025-05-14 17:04 界面刷新问题]]）
```bash
git fetch  
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @  
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!  
Someone could be eavesdropping on you right now (man-in-the-middle attack)!  
It is also possible that a host key has just been changed.  
The fingerprint for the ED25519 key sent by the remote host is  
SHA256:kI9pE34AiIsMUDiWudftxMQ2z1su1Ys1nE+RZ8LLT64.  
Please contact your system administrator.  
Add correct host key in /c/Users/nobir/.ssh/known_hosts to get rid of this message.  
Offending ECDSA key in /c/Users/nobir/.ssh/known_hosts:12  
Host key for [192.168.31.6]:8022 has changed and you have requested strict checking.  
Host key verification failed.  
fatal: Could not read from remote repository.

Please make sure you have the correct access rights  
and the repository exists.
```
以下是询问 chatgpt 后的解答
你遇到的这个错误提示是：
```
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```
这是 **SSH 的安全机制**在提醒你：你之前连接的主机 `[192.168.31.6]:8022` 的身份信息（即 **公钥指纹**）发生了变化。
这通常有两个原因：
📌 原因分析
✅ 正常原因（常见）
1. **远程服务器（或设备）重装系统了**
2. **SSH 配置更换了密钥（如 ED25519 替代了 ECDSA）**
3. **你连接的其实是另一个设备，但用了相同的 IP/端口**
❌ 安全风险（不常见）
可能是中间人攻击（MITM），但如果你在局域网内、IP 是自己家路由分配的，这种可能性极低。

✅ 解决方法：删除旧的 known_hosts 记录
提示里已经告诉你“出错的 key”是在：
```
Offending ECDSA key in /c/Users/nobir/.ssh/known_hosts:12
```
这表示：在你电脑的 `known_hosts` 文件中第 12 行存的是旧的主机公钥，现在这个 key 不匹配了。

💻 方法 1：手动删除出错的那一行
你可以使用文本编辑器打开该文件：
```bash
nano /c/Users/nobir/.ssh/known_hosts
```
删除第 12 行，保存即可。
或者使用命令：
```bash
ssh-keygen -R [192.168.31.6]:8022
```
这会自动从 `known_hosts` 文件中移除这个主机记录。

🚀 方法 2：重新连接并接受新的公钥
然后你重新运行一次 Git 命令或 SSH 命令，例如：
```bash
ssh -p 8022 username@192.168.31.6
```
你会看到如下提示：
```
The authenticity of host ... can't be established.
Are you sure you want to continue connecting (yes/no)?
```
输入 `yes`，系统就会将新的 host key 添加进 `known_hosts`，之后 `git fetch` 就不会再报错了。

 🔐 补充：查看远程主机实际的 SSH 公钥指纹
你可以在远程服务器上运行：
```bash
ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub
```
这会显示远程主机当前的 key 指纹（用来确认这次是不是你自己重新安装或修改的设备）。

 ✅ 总结操作步骤（推荐）
```bash
 1. 删除旧记录
ssh-keygen -R [192.168.31.6]:8022

 2. 再次连接
ssh -p 8022 username@192.168.31.6   输入 yes 接受新密钥

 3. 再次尝试 Git 操作
git fetch
```

