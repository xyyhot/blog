---
slug: applications/linux-command
title: linux常用指令
---

- `chmod`（修改文件权限）
- `ln`（创建链接文件）
- `touch`（快速创建空文件或更新时间戳）
- `rmdir`（删除空目录）
- `man`（查看命令手册 manual）
- `diff`（比较两份文件的差异）
- `sudo`（以超级管理员 root 权限执行命令）
- `clear`（清屏）
- `telnet`（网络远程登录/测试端口连通性工具）
- mv 不仅仅是移动，也可以重命名
- cd 更改路径
- pwd显示当前目录 当前目录简写是.
- 父目录是..
- ls 列出当前目录所有文件 -l更详细 -a列出隐藏文件
- cp 源文件 目标位置
- 前面加-r表示操作进行了递归
- rm -f file 不用确定是否删除
- mkdir（创建文件夹）
- cd 切换目录 cd..返回父目录 cd ~返回home
- cat 输出文件内容
- grep 搜索文件内容 grep -n xxx 文件名 （显示行号）
- ls | grep "cpp" 把ls的结果给grep
- 输出重定向 ls > txt（覆盖原有内容）
- ls >>txt 追加
- ls 2> txt 只把错误信息输入进去
- echo\==printf
- echo $? 捕捉上一条程序的退出状态码
- 在 Bash 中，变量赋值的等号两边不能有空格。
- `MY_NAME="Alice"`：创建或修改当前 Shell 的变量。
    
- `export MY_NAME="Alice"`：创建或修改变量，并将它导出到环境中，让当前 Shell 启动的子进程也能读取。
- 

