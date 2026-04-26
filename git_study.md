---
title: git_study
date: 2026-04-26
author: PlusCJC
---

## GIT 学习

- 主要的学习视频是 [B站 - Git教程](https://www.bilibili.com/video/BV1Hkr7YYEh8/)

### 1. git配置 + github

- 配置用户名和邮箱

```bash
git config --global user.name "PlusCJC"
git config --global user.email "3447999786@qq.com"
```

### 2. 创建仓库

- 创建本地仓库

```bash
mkdir my_project
cd my_project
git init
```

- 连接远程仓库

```bash
git remote add origin https://github.com/PlusCJC/my_project.git
```

- 查看git仓库状态

```bash
git status
```

- 查看git仓库配置

```bash
git config --list
```

- 查看git日志

```bash
git log
```

### 3. 提交代码

暂存区(Staging Area / Index)和本地仓库(Local Repository)的区别

- **暂存区** 是 Git 中的一个区域，用于临时存储即将提交的文件。即准备提交的文件清单
- **本地仓库** 是已经正式保存下来的版本历史。 Git 会把暂存区里的内容保存成一个新的版本记录，也就是一个 `commit`。这些 commit 存在你电脑上的 `.git` 目录中，这就是你的本地仓库。

``` bash
# 1. 修改文件
echo "hello" > test.txt

# 2. 加入暂存区
git add test.txt

# 3. 提交到本地仓库
git commit -m "add test.txt"

```

注意，假设你已经修改了 `test.txt` 文件，并添加到暂存区，然后又修改了 `test.txt` 文件，但是**没有添加到暂存区**，那么，提交到本地仓库时，只会提交暂存区中 `test.txt` 文件的最新版本。提交到本地仓库的内容是**暂存区中的内容**，而不是工作区中的内容。