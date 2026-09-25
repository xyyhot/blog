---
slug: applications/git-push-or-git-push-origin-main
title: git push和git push origin main之间的区别
---

### `git push`

```bash
git push
```

推送当前所在的分支。

如果当前分支已经设置了上游分支，例如：

```bash
git push -u origin main
```

之后直接执行：

```bash
git push
```

通常等价于：

```bash
git push origin main
```

### `git push origin main`

```bash
git push origin main
```

明确指定：

- 远程仓库：`origin`
- 本地分支：`main`
- 推送目标：通常是远程的 `main` 分支

无论当前处于哪个本地分支，它都会尝试推送本地 `main` 分支。

例如当前在 `dev` 分支上，执行：

```bash
git push origin main
```

推送的仍然是本地 `main`，而不是 `dev`。

## 常见首次推送方式

```bash
git push -u origin main
```

`-u` 会建立本地 `main` 与远程 `origin/main` 的关联。首次执行后，后续通常只需：

```bash
git push
git pull
```
