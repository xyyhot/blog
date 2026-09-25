---
aliases:
  - git-command
slug: applications/git-command
title: Git 常用命令
---

## 三步走

```bash
三步走
1.git add . #把当前目录的所有文件提交到待上传区
2.git commit -m "提交内容"
3.git push #推送到远程仓库





```
## 仓库配置

```bash
git config --global user.name "你的名字"
git config --global user.email "you@example.com"

git config --list
```

## 创建与获取仓库

```bash
git init                         # 初始化仓库
git clone <仓库地址>              # 克隆远程仓库
git remote -v                    # 查看远程仓库地址
git remote add origin <仓库地址>  # 添加远程仓库
```

## 查看状态与历史

```bash
git status                       # 查看工作区状态
git log                          # 查看提交历史
git log --oneline                # 简洁查看提交历史
git diff                         # 查看未暂存修改
git diff --staged                # 查看已暂存修改
```

## 提交代码

```bash
git add <文件名>                 # 暂存指定文件
git add .                        # 暂存当前目录所有修改
git commit -m "提交说明"          # 创建提交
git commit -am "提交说明"         # 暂存并提交已被 Git 跟踪的文件
```

## 分支操作

```bash
git branch                       # 查看本地分支
git branch -a                    # 查看所有分支
git branch <分支名>              # 创建分支
git switch <分支名>              # 切换分支
git switch -c <分支名>           # 创建并切换分支
git branch -d <分支名>           # 删除已合并分支
git merge <分支名>               # 合并分支
```

## 远程同步

```bash
git fetch                        # 获取远程更新，不合并
git pull                         # 获取远程更新并合并
git push                         # 推送当前分支
git push origin main             # 推送本地 main 到 origin/main
git push -u origin main          # 推送并建立上游分支关联
```

## 撤销操作

```bash
git restore <文件名>             # 放弃工作区修改
git restore --staged <文件名>    # 取消暂存
git reset --soft HEAD~1           # 撤销提交，保留修改
git reset --hard HEAD~1           # 撤销提交并删除修改，谨慎使用
```
