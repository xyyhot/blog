# MIT Missing Semester笔记
[计算机教育中缺失的一课 · the missing semester of your cs education](https://missing-semester-cn.github.io/)

> 课程主页：https://missing.csail.mit.edu/2026

---

# 第 1 讲：课程概览 + Shell 入门

## 1.1 核心理念：为什么要学 Shell

- **图形界面的局限**：GUI 只能做被开发者预先设计好的事。批量重命名 1000 个文件、统计日志中出现最多的错误——GUI 要么做不到，要么要点几百次鼠标。
- **Shell 的本质**：基于文本的编程环境，可以**组合小程序**完成任意复杂任务。每个程序只做一件事并做好，通过管道连接起来就是强大工具。

## 1.2 Shell 是什么、如何使用

- Shell 是一段程序，接收命令、解析后交给操作系统执行
- 常见 shell：bash（最通用）、zsh（macOS 默认）、fish（现代化）
- `$PATH` 环境变量决定 shell 到哪些目录搜索可执行程序：

```bash
echo $PATH          # /usr/local/bin:/usr/bin:/bin:...
which date          # 查找 date 程序的实际路径
```

## 1.3 文件系统导航

```bash
pwd                 # 当前所在目录
cd /home            # 绝对路径切换
cd ./foo            # 相对路径
cd ..               # 上级目录
cd ~                # 家目录
cd -                # 上一个目录
ls -l               # 详细列表（权限、大小、修改时间）
mv old new          # 重命名/移动
cp -r src dst       # 递归复制目录
mkdir dir           # 新建目录
rm -r dir           # 删除目录
```

- 特殊路径：`.` 当前、`..` 上级、`~` 家目录
- 获取帮助：`man cmd`（手册页）、`cmd --help`（快速帮助）、`tldr cmd`（实用示例集）

## 1.4 现代替代工具

| 传统 | 替代 | 优势 |
|---|---|---|
| cd | `zoxide` | `z foo` 直接跳到最常访问的匹配目录 |
| ls | `eza` | 彩色、图标、git 状态列 |
| cat | `bat` | 语法高亮、行号 |
| grep | `ripgrep (rg)` | 快几个数量级、默认递归、忽略 .gitignore |
| find | `fd` | 语法直观 |

## 1.5 文本处理三剑客 + find

### grep — 行级正则搜索
```bash
grep pattern file           # 输出匹配行
grep -i                     # 忽略大小写
grep -v                     # 反选不匹配的行
grep -r pattern dir/        # 递归搜目录
grep -n                     # 显示行号
grep -C 3                   # 显示上下各 3 行
```

### sed — 流编辑器
```bash
sed 's/old/new/' file       # 每行替换第一处
sed 's/old/new/g' file      # 全局替换
sed -i 's/old/new/g' file   # 直接改原文件（危险，先备份！）
```

### awk — 按列处理
```bash
awk '{print $2}' file           # 打印第 2 列
awk -F, '{print $1}' file.csv   # 逗号分隔取第 1 列
awk '$10 > 100' access.log      # 过滤条件行
```

### find — 条件查找
```bash
find ~/Downloads -type f -name "*.zip" -mtime +30
# 类型=文件 名字模式 30天前修改
find . -name "*.tmp" -delete    # 找到即删
```

## 1.6 管道与重定向：组合的艺术

- `<` 读入输入文件；`>` 写出（覆盖）；`>>` 追加
- 管道 `|` 把左边程序的 stdout 接到右边程序的 stdin

### 经典示例：统计日志中最常见的断连用户
```bash
ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log

cat ssh.log | grep -oE ".*Disconnected from (invalid|authenticating) user [^ ]+" \
  | sort | uniq -c | sort -nk1,1 | tail -n10 \
  | sed -E 's/.*user ([^ ]+)/\2/' 
```
分解：
1. `grep -oE` 用扩展正则只输出匹配部分
2. `sort` 排序（uniq 只对相邻重复计数）
3. `uniq -c` 去重并计数
4. `sort -nk1,1` 按次数数值排序
5. `tail -n10` 取最多前 10 个
6. `\2` 引用 sed 的第二个捕获组提取用户名

## 1.7 编写 Shell 脚本

```bash
#!/bin/bash                    # shebang 指定解释器
foo=bar                        # 等号两边不能有空格！
echo "$foo"                    # 双引号做变量替换 → bar
echo '$foo'                    # 单引号字面量 → $foo

if [ $? -ne 0 ]; then
  echo "failed"
fi

for i in $(seq 1 10); do       # 循环 1 到 10
  echo $i
done
```

### ⭐ 脚本安全带：set -euo pipefail
```bash
set -euo pipefail
```
- `-e`：任何命令失败立即退出——防止错误被静默忽略
- `-u`：引用未定义变量报错——防拼写错误的变量悄悄变空串
- `-o pipefail`：管道任一环节失败整条算失败——bash 默认只看最后一个命令

### 特殊变量速查
| 变量 | 含义 |
|---|---|
| `$0` | 脚本名 |
| `$1..$9` | 位置参数 |
| `$@` | 全部参数 |
| `$#` | 参数个数 |
| `$?` | 上一命令退出码 |
| `$$` | 当前脚本 PID |
| `$!` | 最后一个后台进程 PID |
| `$_` | 上一命令的最后一个参数 |

其他要点：
- 命令替换用 `$(...)` 不用反引号（可嵌套）
- 函数：`mcd () { mkdir -p "$1"; cd "$1"; }`

### ⭐ 必备：shellcheck
写完脚本必跑 [shellcheck](https://www.shellcheck.net/)，能抓出绝大多数常见 bug 和反模式。

---

# 第 2 讲：命令行环境

## 2.1 信号与作业控制

### 常用信号
| 信号 | 触发方式 | 说明 |
|---|---|---|
| SIGINT | `Ctrl-C` / `kill -INT` | 中断，进程可捕获做清理 |
| SIGQUIT | `Ctrl-\` | 退出并转储核心 |
| SIGTSTP | `Ctrl-Z` | 暂停进程 |
| SIGTERM | `kill` 默认 | 请求优雅终止，可捕获 |
| SIGKILL | `kill -9` | **无法捕获**，内核直接杀死 |
| SIGHUP | 终端挂断 | nohup 可忽略；守护进程常用来重载配置 |

### 作业控制工作流
```bash
sleep 100        # Ctrl-Z 暂停
bg %1            # 转后台继续
jobs             # 查看作业列表
fg %1            # 调回前台
nohup cmd &      # 关终端也不死
disown %1        # 从作业表移除已运行进程
```
不过，如果我们是在另一个 bash 会话里启动的，这个策略就失效了，因为 `wait` 只对当前 shell 的子进程有效。讲义里还没提到的一点是：`kill` 命令成功时退出状态为 0，失败时则非 0。`kill -0` 不会真的发送信号，但如果进程不存在，它会返回非 0。
```
while kill -0 "$pid" 2>/dev/null; do
    sleep 1
done
```
## 2.2 tmux 终端复用器（重点）

解决 SSH 断线丢工作的问题。层级：**Session > Window > Pane**

| 操作 | 快捷键 |
|---|---|
| 分离会话（后台保留） | `<C-b> d` |
| 恢复会话 | `tmux a` |
| 新建窗口 | `<C-b> c` |
| 下/上一个窗口 | `<C-b> n` / `p` |
| 水平分屏 | `<C-b> "` |
| 垂直分屏 | `<C-b> %` |
| 缩放当前 pane | `<C-b> z` |

三大价值：① 会话持久化；② 多窗口分屏；③ 共享会话结对编程。

## 2.3 SSH 与远程机器

```bash
ssh-keygen -a 100 -t ed25519        # 生成密钥对（ed25519 现代推荐）
ssh-copy-id -i ~/.ssh/id_ed25519 alice@remote   # 免密登录配置
ssh alice@server 'ls | wc -l'       # 远程非交互执行
scp local.txt server:/path/         # 复制文件
rsync -avP src/ server:/path/       # 增量同步：只传差异，支持续传
```

### ~/.ssh/config 主机别名（强烈推荐）
```
Host vm
    User alice
    HostName 172.16.174.141
    IdentityFile ~/.ssh/id_ed25519
    RemoteForward 9999 localhost:8888   # 端口转发也能配
```
之后直接 `ssh vm`；scp/rsync/git 都认这个别名。

## 2.4 通配符 Globbing

通配符由 **shell 在执行前展开**成文件名列表，程序本身看不到 `*`：
```bash
convert image.{png,jpg}         # 展开为两个参数
touch {a,b,c}.txt               # 三个文件
mv *{.py,.sh} folder            # 组合
rm **/*.py                      # ** 递归匹配子目录
```

## 2.5 流与重定向

stdin(0) / stdout(1) / stderr(2)；**管道只接 stdout**。

```bash
cmd 2> err.txt              # 只重定向 stderr
cmd &> all.txt              # 两个流都重定向
cmd 2> /dev/null            # 丢弃错误输出
diff <(ls src) <(ls dst)    # 进程替换：命令输出当文件用
```

⭐ **管道中的程序是并发执行的**，数据流式流动，不是跑完一个再下一个。

## 2.6 环境变量与退出码

```bash
foo=bar             # shell 变量（仅本 shell）
export foo=bar      # 环境变量（子进程可见）
TZ=Asia/Tokyo date  # 单次设置运行
$?                  # 0=成功，非零=失败
cmd && echo ok      # 成功才执行
cmd || echo fail    # 失败才执行

```

常见退出码：1 通用错误、127 命令不存在、126 无权限、128+n 被信号 n 杀死。
## 2.7 美元符号的用法
### 1. 变量取值（最常见）

`$名字` 表示"取出这个名字变量的值"：

bash

复制

```
name="world"
echo $name        # 输出 world
echo "hi $name"   # 输出 hi world
```

刚才 `marco` 函数里的 `$MARCO_SAVED_DIR` 就是取那个变量的值。

### 2. `$()` 命令替换

`$(命令)` 表示"执行命令，把它的输出**当作值**"：

bash

复制

```
echo $(pwd)      # 输出当前目录
MARCO_SAVED_DIR="$(pwd)"   # 把 pwd 的输出存进变量
```

### 3. `$n` 位置参数（脚本/函数参数）

`$1`、`$2`... 表示第几个参数，`$0` 是脚本名，`$#` 是参数个数：

bash

复制

```
f() { echo "第一个参数是 $1"; }
f hello          # 输出 第一个参数是 hello
```

### 4. 特殊变量

|写法|含义|
|---|---|
|`$?`|上一条命令的退出码|
|`$$`|当前进程 PID|
|`$!`|最近一个后台进程的 PID|
|`$@` / `$*`|所有参数|
|`$HOME`|主目录|
|`$PATH`|可执行文件搜索路径|

### 5. `${...}` 花括号（区分边界/默认值）

用花括号可以把变量名和后面的字符**分隔开**，还能做默认值等操作：

bash

复制

```
name=cat
echo $namefood      # 空！因为 bash 把 $namefood 当一个变量名
echo ${name}food    # catfood，用 {} 明确变量名
echo ${name:-默认值} # 变量为空时用默认值
```

### 6. `$((...))` 算术运算

bash

复制

```
echo $((2 + 3))     # 输出 5
```

### 一句话

`$` 在 shell 里**本身没有意义，它是"后面跟的东西被当变量/命令/特殊值处理"的标记**。最常见的两种情况：`$变量名`（取值）和 `$(命令)`（命令替换）。想知道具体是哪个，看它后面跟着什么。
## 2.8 Dotfiles 定制

- 配置位置：`~/.bashrc`、`~/.vimrc`、`~/.gitconfig`、`~/.tmux.conf`、`~/.ssh/config`
- alias 别名：`alias ll="ls -lh"`（不能带中间参数，复杂逻辑写函数）
- **dotfiles 管理**：独立 git 仓库 + symlink 安装脚本，换机一键还原
- 插件：zsh-autosuggestions（历史提示）、zsh-syntax-highlighting（语法着色）
- ⚠️ `curl xxx | bash` 有风险：先下载审查再执行
`alias`（别名）是 shell 里给命令/命令组合起一个**简短快捷方式**的功能。

```
alias ll='ls -l'
```

之后敲 `ll` 就等价于 `ls -l`。

## 几个关键点

**1. 查看已有哪些别名**

bash

复制

```
alias           # 列出所有别名
alias ll        # 只看 ll 这一个
```

**2. 临时创建（只在当前 shell 生效）**

bash

复制

```
alias 名字='命令'
```

**3. 删除别名**

bash

复制

```
unalias ll
```

**4. 永久生效（写入配置文件）**

bash

复制

```
echo "alias ll='ls -l'" >> ~/.bashrc   # bash
source ~/.bashrc
```

（zsh 用 `~/.zshrc`）

## 常用别名例子

bash

复制

```
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias gs='git status'
alias gc='git commit'
alias ..='cd ..'         # 连续两个点也能是别名！
alias grep='grep --color'
alias rm='rm -i'         # 删东西前先问，防手滑
```
## 2.9 Shell 中的 AI（新趋势）

- `llm cmd "自然语言描述"` 生成命令
- LLM 接入管道处理非结构化文本
- Claude Code 等 agent 作为"元 shell"

---

# 第 3 讲：开发环境与工具（Vim 等）
1.vim文本编辑
2.语言服务器
3.ide提供的高级功能
4.ai驱动开发
重新做笔记，手机相册
## 3.1 为什么学 Vim

- 编辑是最高频操作，Vim 的**模式化编辑 + 动词名词组合**让编辑速度质变，实现高效文本编辑
- Vim 键位无处不在：IDE 插件、浏览器 Vimium、shell vi mode——一次学会到处受益
- 服务器救急也必须会基本操作
- 减少键盘与鼠标之间的切换使用
```
**文本缓冲区（text buffer）就是程序在内存里开辟的一块区域，用来暂时存放正在编辑或处理的文本内容。**

它是一个中间层：你看到的内容并不是直接写在磁盘文件上，而是先装在这块内存里，你所有的输入、删除、修改都发生在这里，最后再由程序决定何时写回文件。

不同语境下它指的东西略有差别，但核心思想一致：

1. **文本编辑器里的缓冲区**——这是最常见的用法。Vim、Emacs、VS Code 里打开一个“标签页/窗口”，背后对应的就是一个 buffer。你编辑的从来不是文件本身，而是这个内存副本；执行保存（`:w`、Ctrl+S）时才把缓冲区内容写进磁盘。所以 Emacs 里有句经典说法：**"你永远在编辑 buffer，而不是 file。"** 一个 buffer 可能对应已打开的文件、还没命名的新建文件，甚至不落盘的内容（比如 Vim 里 `:new` 出来的草稿缓冲区，或终端里 `echo hello` 暂存的那段可复制文本）。

2. **一般编程意义上的缓冲区**——任何程序为了处理数据而临时持有的字节序列，比如读文件时读到的字符串存在一个 char 数组/StringBuilder 里，或 C 语言里的字符数组。这里强调的是“攒一批再处理”的内存暂存。

3. **键盘输入缓冲区**——你在终端敲字时，按键先进队列（input buffer），程序按自己的节奏读取。这也是为什么在程序卡住时继续打字，等它恢复后字符会一下子涌出来。

理解它的关键就一句话：**缓冲区是“工作副本”，文件是“持久存储”，两者之间有明确的同步动作（保存/刷新）**。这也解释了一些日常现象：编辑器崩溃后未保存的内容丢了——因为它们只存在于内存里的 buffer 中；而某些终端会限制回滚历史的大小——因为那份缓冲区是固定大小的内存。
```
## 3.2 五种模式
C-v =ctrl+v
在正常模式下移动，在插入模式下编辑

| 模式           | 进入方式                    | 用途        |
| ------------ | ----------------------- | --------- |
| Normal       | 默认 / `ESC`              | 导航、组合编辑命令 |
| Insert       | `i`                     | 插入文字      |
| Replace      | `R`                     | 连续覆盖替换    |
| Visual       | `v`(字符) `V`(行) `C-v`(块) | 选中再操作     |
| Command-line | `:`                     | 执行命令      |

一切操作骨架：**动词 + 名词**。
注意：只使用默认模式无法编辑，需要使用i模式
## 3.3 移动命令（名词）
移动，选择，编辑，计数，修饰符

| 按键               | 含义                  |
| ---------------- | ------------------- |
| `h j k l`        | 左下上右                |
| `w / b / e`      | 下词首 / 此词首 / 此词尾     |
| `0 / ^ / $`      | 行首 / 首个非空 / 行尾      |
| `gg / G / :N`    | 文件头 / 尾 / 第 N 行     |
| `f{c} / t{c}`    | 本行找字符（`;` `,` 重复跳转） |
| `/regex` + `n/N` | 搜索 / 下一个、上一个        |
| `{ / }`          | 上/下一段落（空行）          |
| `C-o / C-i`      | 跳转历史后退/前进           |
| H / M / L        | 屏幕头/屏幕中/屏幕底         |
| C-d              | 向下滚动                |
| C-u              | 向上滚动                |
| %                | 找匹配的括号和引号           |

块选择：`C-v` 纵向选列，配合 `I`/`A` 批量插入——多行编辑神器。

## 3.4 编辑命令（动词）

| 按键 | 含义 |
|---|---|
| `x` / `s` | 删字符 / 删并进入插入 |
| `d{motion}` | 删除：`dw` 删词、`dd` 删行、`D` 删到行尾 |
| `c{motion}` | 修改（删除并进插入）：`cw` `cc` |
| `y{motion}` / `p` | 复制 / 粘贴 |
| `u` / `C-r` | 撤销 / 重做 |
| `o` / `O` | 下/上方新开一行 |
| `.` | ⭐ 重复上一个修改——组合技灵魂 |

## 3.5 组合：计数 + i/a 修饰符
修饰符可改变动词或名词的含义
- 计数：`3w` 前 3 词、`7dw` 删 7 词
- **i=inner 内部，a=around 连边界**：
  - `ci(` 改括号内内容（保留括号）
  - `da[` 删方括号及内容
  - `ci"` 改引号内字符串
- 例：把 `"hello"` 改 `'world'` → `ci"'world'<ESC>`

学习法：所有软件开 vim mode；一周不用鼠标方向键；入门必做 `vimtutor`。

## 3.6 扩展生态

- Neovim：现代化 fork，Lua 配置
- 插件：fzf.vim/telescope.nvim（模糊搜索）、treesitter（语法树）

## 3.7 LSP 语言服务器协议

- 解决 M×N 问题（M 个编辑器 × N 门语言）→ M+N：语言 server + 编辑器 client
- 功能：补全、跳转定义、查引用、悬停文档、重命名
- Python→Pylance/pyright；Go→gopls；Rust→rust-analyzer
- 注意：装扩展后要配好项目环境（虚拟解释器等），否则功能失灵
- 一个形象的类比：LSP 之于编辑器和语言，就像 USB 之于电脑和外设——以前每种设备一根专用线（键盘协议、打印机协议各不相同），现在统一成一个插口和一套握手规则，任何设备接上任何机器都能工作。

## 3.8 AI 开发三种形态（2026 视角）

| 形态   | 交互               | 场景             |
| ---- | ---------------- | -------------- |
| 自动补全 | 光标处幽灵文本 Tab 采纳   | 顺思路快速写码；注释引导生成 |
| 内联聊天 | 选代码让 AI 改        | 局部重构、加错误处理     |
| 编码代理 | 对话下达任务，自主读写文件跑命令 | 见第 7 讲         |
![[Quicker_20260827_182734.png]]
---
这么写可以让ai根据你的要求来修改，但只能在这个光标后面修改，不能全局
关于隐私问题：可以在设置里让ai不保留你的代码，但服务商不一定说到做到
# 第 4 讲：调试与性能分析

## 4.0 方法论

> "最有效的调试工具仍是仔细思考 + 适当放置的打印语句。" —— Kernighan

调试金字塔（从便宜到昂贵）：思考 → 打印/日志 → 调试器 → 记录回放 → 系统追踪 → 内存检测。

⚠️ 编译前提：带 `-g`（DWARF 调试符号）和 `-fno-omit-frame-pointer`（栈帧指针）。

## 4.1 第一层：打印 / 日志
对打印更有条理的运用

- 日志框架优势：级别过滤（INFO/DEBUG/WARN/ERROR）、结构化 JSON 输出、去向可控
- 系统日志：
```bash
ls /var/log/
sudo journalctl -u nginx           # systemd 服务日志
sudo journalctl -u nginx --since today
sudo dmesg                          # 内核环形缓冲区
```

## 4.2 第二层：调试器 gdb/lldb

```
b file:line   设断点       c        继续
step / next   步入 / 步过   finish   步出函数
p var         打印变量     bt       调用栈回溯
watch expr    值变化中断——追"谁改了我的变量"
layout src    TUI 模式看源码
```
Python 用 pdb：`python -m pdb prog.py`，命令几乎一致。

## 4.3 第三层：记录回放 rr（Linux）

专治 **Heisenbug**（竞态、时序相关 bug——加打印就不复现）：
```bash
rr record ./prog && rr replay
(rr) reverse-continue   # 反向运行到断点
(rr) watch var          # 变量被改坏时停下，倒着找罪魁祸首
```
杀手锏：**时间旅行调试**——先看崩溃现场，再倒推原因。每次回放完全确定。

## 4.4 第四层：系统调用与事件追踪

```bash
strace -e trace=file ./prog   # 只追踪文件相关调用
strace -p <PID>               # 附着运行中进程
strace -c ./prog              # 系统调用耗时统计
```
- `bpftrace`：eBPF 内核态聚合统计，更强大
- 网络：tcpdump/Wireshark 抓包；HTTPS 用 mitmproxy 或浏览器 DevTools
然后讲了一个东西叫sanitizer
## 4.5 第五层：内存错误检测

```bash
gcc -fsanitize=address -g prog.c -o prog   # ASan：越界/use-after-free/泄漏
gcc -fsanitize=thread                       # TSan：数据竞争
gcc -fsanitize=undefined                    # UBSan：未定义行为
valgrind --leak-check=full ./prog           # 无需重编译但慢约20倍
```
ASan 能精确指出出错内存的分配点和释放点完整调用栈。

## 4.6 第六层：LLM 辅助调试

擅长：解释晦涩报错（C++ 模板、Rust borrow checker）、分析崩溃栈、跨语言问题。
⚠️ 幻觉风险：结论必须落到工具验证（复现、断点、sanitizer）。

## 4.7 性能分析
与调试有惊人的关联性

### time 三兄弟
```bash
time curl https://example.com
# real  墙钟时间   user 用户态CPU   sys 内核态CPU
```
real ≫ user+sys ⇒ 时间花在等待 IO/网络。

### 资源监控
`htop`（进程）、`iotop`（磁盘IO）、`free -h`（内存）、`lsof`（打开文件）、`ss -tlnp`（端口占用）

### CPU 剖析两大流派
1. **采样式**（低开销，生产首选）：`perf record -g ./prog` → `perf report` → 火焰图
2. **追踪式**（精确但慢数十倍）：`valgrind --tool=callgrind`

内存剖析：`valgrind --tool=massif`

### 基准测试
```bash
hyperfine --warmup 3 'fd .py' 'find . -name "*.py"'
```
数据导出 CSV/JSON 后用 gnuplot/matplotlib 画图发现规律。
允许你输入任何数量的命令并会对每个命令进行多次测试

---

# 第 5 讲：版本控制与 Git

## 5.1 心法：自底向上理解数据模型

大多数人把 Git 当黑盒命令集来背，遇到冲突就慌。正确姿势：**先懂数据模型，命令自然水到渠成**。

### 三种对象
```
blob    = 文件内容快照（一堆字节，不含文件名！）
tree    = 目录：{名字 -> blob/tree} 的映射
commit  = { parents[], author, message, tree 指向根目录 }
```
关键认知：**commit 是整个项目目录树的快照（snapshot），不是 diff！**

### 历史是 DAG（有向无环图）
- commit **不可变**；引用（reference）**可变**
- 分支 `main` 就是指向某 commit 的可移动指针；`HEAD` 表示"我当前在哪"
- 普通 commit 一个 parent，merge commit 两个，初始 commit 零个

### 仓库本质
```
仓库 = objects（按 SHA-1 内容寻址）+ references（指针）
所有 git 命令 = 对这张图的操作（加节点 / 移指针）
```

### 暂存区
介于工作区与仓库之间，允许精确挑选下次快照包含哪些改动。

## 5.2 基础命令速查

```bash
git init / status / add / commit
git add -p                       # ⭐ 交互式逐块暂存
git log --all --graph --decorate # 彩色图示历史
git diff [file]                  # 工作区 vs 暂存区
git diff --staged                # 暂存区 vs 上次提交

# 分支
git branch <name>; git switch <name>; git checkout -b <name>
git merge <rev>                  # 合并（保留拓扑，产生合并节点）
git rebase                       # 变基（线性历史）——勿对已推送的共享历史 rebase！

# 远程
git remote add origin <url>
git push origin main             # 推送
git fetch / pull / clone         # 拉取 / 拉取+合并 / 克隆

# 撤销
git commit --amend               # 修改上次提交（未推送前）
git reset <file>                 # 取消暂存
git restore                      # 丢弃工作区改动
```

## 5.3 高级技巧

```bash
git blame file                   # 每行最后修改者
git stash / stash pop            # 暂存与恢复工作现场
git bisect                       # 二分查找引入 bug 的提交
git revert <commit>              # 生成反向提交（安全撤销已推送提交）
git worktree                     # 多分支同时检出——并行 AI agent 必备
```

学习资源：Pro Git 书、learngitbranching.js.org、ohshitgit.com。

---

# 第 6 讲：打包与发布代码

## 6.1 依赖与环境管理

- 包管理器解析传递依赖并安装；版本约束冲突即"依赖地狱"
- 解决方案：**虚拟环境隔离**（Python venv），每个项目独立依赖集
- 推荐 `uv` 替代 pip（快几个数量级）：`uv pip install requests`

## 6.2 制品与打包

- **源码 vs 制品**：制品是从源码构建出的可分发产物（如 wheel `.whl`）
- 元数据写 `pyproject.toml`（现代标准，替代 setup.py/requirements.txt）
- 预编译二进制 vs 源码编译：前者快但要匹配平台架构

## 6.3 语义化版本 SemVer

格式 `MAJOR.MINOR.PATCH`：
| 升级位 | 含义 | 兼容性 |
|---|---|---|
| PATCH | 仅修 bug | 向后兼容 |
| MINOR | 新增功能 | 向后兼容 |
| MAJOR | 破坏性变更 | 不兼容 |

版本约束写法：`==8.0.1` 精确、`>=8.0` 下限、`>=1.24,<2.0` 范围、`~=2.1.0` 兼容版本。

## 6.4 可重现性

- **锁文件**（lock file）固定全部传递依赖确切版本（如 `uv.lock`）
- 原则：**库用宽松范围，应用锁精确版本**
- 极致可重现：Nix/Bazel 封闭构建（hermetic build）

## 6.5 容器技术

- VM 虚拟整台机器；容器共享宿主内核，更轻更快
- **镜像**=模板，**容器**=镜像的运行实例
- Dockerfile 最佳实践：
  - 利用镜像分层缓存加速构建
  - 用 slim 基础镜像；合并 RUN 减少层
  - 固定依赖版本；清理缓存
  - 不要以 root 运行；**不要把秘密打进镜像**
- 多容器编排：docker-compose.yml（web + redis），服务名即 DNS 主机名
- 生产级编排用 Kubernetes（学习曲线陡，小项目勿用）

## 6.6 配置管理原则

同一份代码通过不同配置部署到 dev/staging/prod，绝不改代码改环境。敏感信息（API key）不进版本库。

## 6.7 发布渠道

GitHub Releases（附预编译二进制）、PyPI/npm/crates.io/Docker Hub 注册表、GitHub Pages 静态站点。

---

# 第 7 讲：AI 编程代理 Agentic Coding ★ 2026 新增

## 7.1 什么是 Coding Agent

带工具访问能力（读写文件、搜索网页、执行 shell）的对话式 AI。
**心智模型：你是实习生经理**——它干活，你给指导、纠正错误、把关质量。

## 7.2 工作原理

- LLM = 给定提示串时对补全串的概率分布建模，推理即采样
- Agent harness 把模型输出解释为工具调用请求，结果喂回上下文再推理
- 上下文窗口有限且过长会降低表现

## 7.3 使用场景

实现新功能（配合 TDD 效果最好）、修复报错（让它自己跑检查形成反馈循环）、重构、代码评审、理解陌生代码库、当自然语言 shell、vibe coding。

## 7.4 高级用法

| 技术 | 说明 |
|---|---|
| 并行 agent | 多实例干不同任务，用 `git worktree` 隔离工作目录 |
| MCP | Model Context Protocol，开放协议连接外部工具 |
| 清空/回退上下文 | 无关任务开新会话；走错路 rewind 而非追加纠正 |
| Compaction | 对话过长自动摘要压缩历史 |
| llms.txt | 为 LLM 设计的高密度文档标准位置 |
| AGENTS.md / CLAUDE.md | agent 的 README，启动时预载通用指示 |
| Skills | 按需加载的技能包，避免 AGENTS.md 膨胀 |
| Subagents | 子代理处理专项任务，隔离上下文 |

## 7.5 注意事项（重要）

- AI 是概率模型必犯错：**必须 review 输出的正确性与安全性**
- 警惕 debug 螺旋：agent 钻牛角尖还说服你它是对的
- 不过度依赖，保持计算思维；关键代码考虑手写
- 默认确认每次工具调用；yolo 模式只在 VM/容器等隔离环境用

代表工具：Claude Code、OpenAI Codex、开源 opencode。

---

# 第 8 讲：超越代码 Beyond the Code ★ 2026 新增

核心主题：**写作要传达 why，而不只是 what**

## 8.1 好的注释类型

1. TODO——带足够上下文："此 O(n²) 循环 n<100 时够用"
2. 参考文献——论文/来源链接 + 与参考实现的差异
3. 正确性论证——代码展示步骤，注释解释为何正确
4. 血泪教训——调 30 分钟以上的非显然 fix 必须记录
5. 魔数/承重细节——"必须用 BTreeSet 因为下面依赖遍历顺序"
6. **Why not**——为什么没用显而易见的方案，否则后人会把它当 bug"修好"

## 8.2 README 四问（漏斗结构）

做什么 → 为什么关心 → 怎么用 → 怎么安装。先展示用法再讲安装。

## 8.3 Commit Message

正文回答四个问题：
1. 什么问题迫使这次变更？
2. 考虑过什么替代方案？
3. 权衡是什么？
4. 有什么意外之处？

复杂变更用 Problem → Solution → Implications 结构。每个 commit 只含一个连贯变更（`git add -p` 拆分）。

LLM 写 commit 技巧：在同一会话里让它写（含 why 上下文），或要求它聚焦 why 并向你提问缺失信息。

## 8.4 协作

### 提 Issue 要素
环境信息、期望 vs 实际、具体复现步骤、已尝试的方法；先搜索已有 issue；**最小可复现示例是无价之宝**。安全漏洞私聊维护者。

### 提 PR
读 CONTRIBUTING.md、从小贡献起步、隔离变更、解释 why、标出重点审查处。
**提交 AI 生成的代码前必须自己尽调**——你无法解释的代码是在给维护者挖坑。

### 做 Review
对事不对人、给可执行建议、多问少命令、区分阻塞问题与偏好建议、肯定优点。

## 8.5 提问的艺术

先陈述自己的理解再问对错；问具体的 yes/no 问题；不懂就打断承认；不接受半懂回答；提问前先做基本调研。

## 8.6 AI 礼仪

AI 参与了工作要**披露**（及用在哪些部分）；遵守团队 AI 政策；面试/考核不确定能否用就直接问；明令禁止绝不用。

---

# 第 9 讲：代码质量与 CI ★ 2026 新增

## 9.1 Formatter 格式化

自动统一表面风格（引号、空格、import 排序）。Prettier 可配置；Black/gofmt 近零配置（终结风格争论）。配置进版本库，配 EditorConfig。

## 9.2 Linter 静态检查

不运行代码即可发现反模式与潜在问题。规则按项目配置、按行禁用。Ruff 等还能自动修复。

semgrep：AST 级"语义 grep"，自定义规则：
```bash
semgrep -l python -e "subprocess.Popen(..., shell=True, ...)"
```

## 9.3 测试

- 层次：单元测试 → 集成测试 → 端到端测试
- 方法：TDD（先写测试）、回归测试（bug 复现即写测试防退化）、基于属性的测试（Hypothesis）
- 外部依赖用 mock
- ⚠️ 覆盖率是度量不是目标，别为覆盖率写垃圾测试

## 9.4 Pre-commit 钩子

[pre-commit](https://pre-commit.com/) 框架：commit 前自动跑 formatter/linter/测试。

## 9.5 持续集成 CI

GitHub Actions 在 push/PR/定时触发脚本：
- 跑 check-only 的 formatter/linter/test，发现问题即红
- 编译确保通过、类型检查确保通过
- 测试矩阵覆盖多 OS/语言版本
- 可延伸为持续部署 CD（push 即自动发布）

学习方法：研究优秀开源项目的 `.github/workflows/` 和 `pyproject.toml`。

## 9.6 命令运行器

`just` 把 `uv run ruff check --fix` 变成 `just lint`；npm scripts 同理。

## 9.7 正则表达式速成

```regex
abc            字面量           .        任意单字符
[abc] / [^abc] 字符类/取反      a|b      或
\d \w \b       数字/单词字符/词边界
(...)?*+{N}    分组与量词（?零或一 *任意 +一或多 {N}恰好N）
^ $            行首/行尾
```
要点：
- 捕获组用于提取替换（VS Code 用 `$1`，Vim 用 `\1`）
- `\d{4}-\d{2}-\d{2}` 匹配格式但不验证合法性
- 正则表达力有上限（无法匹配 aⁿbⁿ；HTML 不是正则语言），复杂解析用真 parser
- 调试工具 regex101.com；可让 LLM 生成再人工验证

---

# 附录 A：通配符（Glob）vs 正则表达式

通配符匹配**文件名**，由 Shell 在执行前展开；正则匹配**文本内容**，功能强大得多。

| 符号 | 通配符含义 | 正则含义 |
|---|---|---|
| `*` | 任意个任意字符 | 前一字符重复 0+ 次 |
| `?` | 恰好一个任意字符 | 前一字符重复 0 或 1 次 |
| `.` | 字面量点号 | 任意单字符 |

```bash
rm *.py              # 通配符：shell 找出所有 .py 再传给 rm
grep "a.*b" file.txt # 正则："任意字符串"是 .* 不是 *
```

zsh/bash 支持 `**` 递归匹配子目录：`rm **/*.py`。

---

# 附录 B：四阶段学习路径建议

1. **第一阶段**：shell + git（第 1、2、5 讲）——日常效率立竿见影
2. **第二阶段**：编辑器与调试（第 3、4 讲）——开发核心技能
3. **第三阶段**：工程化（第 6、9 讲）——写出可交付的项目
4. **第四阶段**：AI 工作流（第 7、8 讲）——2026 年必备的新生产力

其他建议：
- **动手优先**：每讲 exercises 是课程精华
- Windows 用户先装 WSL2（本课命令均为 Unix shell）
- 官方练习无标准答案——探索过程本身就是价值
