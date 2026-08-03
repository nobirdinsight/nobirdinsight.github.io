---
aliases: []
date created: 2024-01-01 09:37:41 Mon
date modified: 2026-08-03
tags: []
---

- https://termux.dev/docs


[[Termux 使用配置教程]]
##### 基本操作
##### alias 起别名方法
```bash
nano ~/.bashrc
# 编辑内容，例如：
alias mycmd='ls -la; pwd'
# 使用命令退出并保存
# 使更改生效
source ~/.bashrc
```

###### 访问手机目录
```shell
termux-setup-storage
```

###### 内部软连接
```
ln -s 目的地址 连接到哪里
```

###### 常用模板
```shell
alias cd0='cd /storage/emulated/0/'
alias cd001='cd /storage/emulated/0/001_local'
alias cd000='cd /storage/emulated/0/000_sync'
alias cdob='cd /storage/emulated/0/001_local/001_personal'

alias http='cd /storage/emulated/0/001_local/local_http_server/;python -m http.server'

alias obpull='git fetch;git pull'
alias obsync='cdob;git fetch;git add .;git commit -m "phone";git pull;git push;date +"%Y-%m-%d %H:%M:%S";'


export LANG=en_US.UTF-8
export LC_CTYPE=en_US.UTF-8

```

2025-03-02 23:52 完成obsync部署，这下同步obsidian笔记很舒服了。
2025-05-14 23:00 更新模板



- 2025-02-23 15:10 手机输命令不方便，来设计常用的快捷命令。
	- 感觉缩减不到哪里去啊，不如用插件但手机那个插件不可用。真是完蛋的。
	- 不如还想以前一样，搞个一组命令吧。
		- 先来分析设计一下又哪些情况，然后分别写个命令组出来。


##### 使用函数
在 bash 中，使用函数可以替代别名来执行复杂的命令序列，而且函数提供了更多的灵活性和更强的功能。以下是如何定义和使用 bash 函数的步骤：
###### 定义函数
1. 编辑 bash 配置文件：
   打开~/. bashrc 文件进行编辑，这个文件是 bash 的配置文件，用于存储别名、函数等。
   ```bash
   nano ~/.bashrc
   ```
   或者使用你喜欢的其他编辑器。
2. 添加函数定义：
   在~/. bashrc 文件中添加你的函数定义。函数定义的基本格式如下：
   ```bash
   函数名() {
       命令1
       命令2
       ...
   }
   ```
   例如，定义一个名为 `update_system` 的函数，用于更新和升级 Termux 包：
   ```bash
   update_system() {
       pkg update
       pkg upgrade
   }
   ```
3. 保存并关闭编辑器。
4. 使更改生效：
   为了让新定义的函数在当前会话中立即生效，你需要重新加载~/. bashrc 文件：
   ```bash
   source ~/.bashrc
   ```
###### 使用函数
一旦函数被定义并加载，你就可以像调用普通命令一样调用它：
```bash
update_system
```
这将执行函数中定义的所有命令。
###### 函数的优势
- **参数传递**：函数可以接受参数，这使得它们比别名更灵活。
- **返回值**：函数可以返回值，允许更复杂的逻辑。
- **局部变量**：函数可以定义局部变量，避免污染全局命名空间。
- **控制结构**：函数内部可以使用 if、for、while 等控制结构，实现更复杂的逻辑。
###### 示例：带参数的函数
以下是一个带参数的函数示例，该函数用于创建一个目录并进入该目录：
```bash
mkcd() {
    mkdir -p "$1"
    cd "$1"
}
```
调用这个函数时，你可以传递一个目录名作为参数：
```bash
mkcd my_new_directory
```
这将创建一个名为 `my_new_directory` 的目录（如果尚不存在），然后进入该目录。
通过使用函数，你可以创建更强大、更灵活的命令组合，从而提高你的工作效率。



##### 内部文件夹同步

将 Termux 内部文件夹同步到手机存储文件夹，可以使用 rsync 命令，它是一个非常强大的工具，用于同步文件和目录。以下是具体步骤：
1. 安装 rsync：
   - Termux 默认没有安装 rsync，你需要先安装它。可以通过以下命令安装：
     ```sh
     pkg install rsync
     ```
2. 确定源目录和目标目录：
   - 源目录：Termux 内部文件夹，例如 `$HOME`（通常是 `/data/data/com.termux/files/home`）。
   - 目标目录：手机存储中的文件夹，例如 `/sdcard/termux-home`。
3. 创建目标目录（如果尚未创建）：
   - 在手机存储中创建目标目录。你可以使用 Termux 的命令行工具，也可以使用手机的文件管理器。
   - 在 Termux 中创建目标目录的命令如下：
     ```sh
     mkdir -p /sdcard/termux-home
     ```
4. 使用 rsync 同步文件夹：
   - 使用以下命令将 Termux 内部文件夹同步到手机存储文件夹：
     ```sh
     rsync -av --ignore-missing-args "$HOME/" "/sdcard/termux-home/"
     ```
   - 解释：
     - `-a`：归档模式，表示递归同步并保持文件属性。
     - `-v`：详细模式，显示同步过程。
     - `--ignore-missing-args`：忽略不存在的文件或目录。
5. 验证同步：
   - 你可以通过以下命令验证同步是否成功：
     ```sh
     ls -l /sdcard/termux-home
     ```
6. 自动化同步（可选）：
   - 如果你希望定期自动同步文件夹，可以编写一个简单的脚本并使用 cron 定时任务来执行它。
   - 创建一个脚本文件，例如 `sync-termux.sh`：
     ```sh
     #!/bin/bash
     rsync -av --ignore-missing-args "$HOME/" "/sdcard/termux-home/"
     ```
   - 给脚本文件执行权限：
     ```sh
     chmod +x sync-termux.sh
     ```
   - 将脚本添加到 cron 定时任务：
     ```sh
     crontab -e
     ```
   - 在打开的编辑器中添加一行，例如每天凌晨 1 点同步：
     ```sh
     0 1 * * * /path/to/sync-termux.sh
     ```
###### 注意事项
1. **权限问题**：
   - Termux 运行在特定的用户和权限下，确保 Termux 有足够的权限访问手机存储。你可能需要在手机的设置中授予 Termux 访问存储的权限。
2. **数据安全性**：
   - 将 Termux 的目录同步到手机存储可能会影响数据的安全性。手机存储的数据更容易被其他应用访问，请谨慎操作。
3. **系统更新和兼容性**：
   - 不同版本的 Android 和 Termux 可能会有不同的行为。确保在操作前备份重要数据。
通过以上步骤，你应该能够成功地将 Termux 内部文件夹同步到手机存储文件夹。这样可以更方便地管理和访问 Termux 中的文件。

##### 使用过程中遇到问题
###### 2025-05-14 17:04 界面刷新问题
输入字符、输出字符时，界面没有反应，需要收起输入法或者唤起输入法面板，内容才会变，

[[#访问手机存储|访问手机存储]]

2025-05-14 23:01 下午最后是重 termux 解决的，发现基础命令很好用,，记录一下

```shell
cp -r ~/ /storage/emulated/0/001_local/linshi
```
最开始在 chatgpt 的建议下尝试复制整个内部目录方便恢复
虽然复制到一半因为权限问题中断了，核心目的的迁移 obsidian git 库的目的没实现，但确实也复制出一些重要配置，对我后面快速恢复核心功能起到重要作用
因为上面的核心目的没有实现，所以又用下面命令，实现迁移 obsidian git 仓库迁移
```bash
cp ~/001_personal.git /storage/emulated/0/001_local/linshi
```
而后重装了 termux 软件，将三个复制过去，基本就恢复了
1. `001_personal.git`
2. `.ssh/`
3. `.bashrc`

主机重连的时候：[[ssh使用#termux 删除重装后再用 ssh 连接，出现下面的问题]]
后面也看到了，现在设备名叫 `u0_402` 了

2025-05-14 23:47 刚刚发现，并没有能正常工作，其实前面只解决到了能连接，实际termux 那里git 都没下呢



[[未写完-替换或添加软件源]]

