# SHELL 脚本使用

### 获取接口请求的返回值，并赋值给变量

```shell
commitHash=$(curl -s http://ci.rmxc.tech/job/Test-k8s-front-pplmc-admin/lastSuccessfulBuild/api/json）
```

注意：

1. 这里对 curl 的请求用$()进行了包裹
2. shell 脚本对空格敏感，这里的 commitHash 后的=号之间没有空格
3. 返回的是 json 格式的字符串
4. curl 的`-s`参数表示只输出结果，不包含额外的 total 等统计信息

### 在 shell 中使用正则过滤

```shell
echo 'usename123' | grep -E -o '\d+'
```

注意：

1. 这里使用了`-E`参数，表示使用正则扩展语法，在 window 下是`-P`

### 获取输出中的第一个值

```shell
head -n 1
# 如：echo 'usename123' | grep -E -o '\d+' | head -n 1
```

### 查看指定目录的大小

```bash
# du -sh [指定目录]
du -sh node_modules
```

### 查看当前目录下所有文件及文件夹大小

```bash
du -sh * .[^.]*
```

### 多命令顺序执行

| 多命令执行符 | 基本格式           | 作用                                                |
| ------------ | ------------------ | --------------------------------------------------- |
| ;            | 命令 1 ; 命令 2    | 多个命令按照先后顺序执行，命令之间没有逻辑关系。    |
| &&           | 命令 1 && 命令 2   | 逻辑与。 只有当命令 1 执行正确，命令 2 才会执行。   |
| \|\|         | 命令 1 \|\| 命令 2 | 逻辑或。 只有当命令 1 执行不正确，命令 2 才会执行。 |
| \|           | 命令 1 \| 命令 2   | 管道符。 将命令 1 的正确输出作为命令 2 的操作对象。 |

### mac 上通过命令行启动 Terminal 新 tab 或 window

```bash
# 激活Termial并新建一个tab
osascript -e 'tell application "Terminal" to activate' -e 'tell application "System Events" to tell process "Terminal" to keystroke "d" using command down'
# 在当前Termial新建一个tab(此时Termial(或iterm2)必须是激活状态)
osascript -e 'tell application "System Events" to tell process "Terminal" to keystroke "t" using command down'
```

说明：

1. osascript 是 mac 上的命令行工具，可以执行 AppleScript 脚本
2. `tell application "Terminal" to activate` 激活 Terminal(这一步可以省略)
3. `tell application "System Events" to tell process "iTerm" to keystroke "d" using command down` 告诉系统事件，对 Terminal 进行操作，按下 command+d，新建一个 tab

### shell 管理

```bash

# 查看当前shell
echo $SHELL

# 切换shell
chsh -s /bin/zsh

# 查看设备上装的shell
cat /etc/shells
```
