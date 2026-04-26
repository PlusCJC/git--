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
git log # 会显示完整的log
git log --oneline # 会显示简化的log
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

### 4. 查看差异与撤销操作

#### 查看差异 `git diff`

```bash
# 查看工作区 vs 暂存区 的差异（还没 add 的修改）
git diff

# 查看暂存区 vs 本地仓库 的差异（add 了但还没 commit 的修改）
git diff --cached

# 查看工作区 vs 本地仓库 的差异（所有修改）
git diff HEAD
```

#### 撤销工作区修改 `git restore` / `git checkout`

```bash
# 撤销工作区中某个文件的修改（回到暂存区的状态）
git restore test.txt
# 等价于旧写法：git checkout -- test.txt

# 撤销所有工作区修改（危险！会丢失未暂存的修改）
git restore .
```

#### 取消暂存 `git restore --staged`

```bash
# 把文件从暂存区撤回工作区（不会丢失修改内容）
git restore --staged test.txt
# 等价于旧写法：git reset HEAD test.txt
```

#### 回退提交 `git reset`

```bash
# 回退到上一个提交，修改内容保留在工作区（混合模式，默认）
git reset HEAD~1

# 回退到上一个提交，修改内容保留在暂存区（软模式）
git reset --soft HEAD~1

# 回退到上一个提交，修改内容全部丢弃（硬模式，危险！）
git reset --hard HEAD~1

# 回退到指定 commit
git reset --hard abc1234
```

#### 反做提交 `git revert`（更安全）

```bash
# 创建一个新 commit 来撤销指定 commit 的修改（不会改写历史）
git revert abc1234
```

> **`reset` vs `revert` 的区别**：
> - `reset` 是"时光倒流"，直接删除历史记录，适合还没 push 的提交
> - `revert` 是"新加一个反向提交"，不改写历史，适合已经 push 的提交（团队协作时必须用这个）

### 5. 分支（Branch）

分支是 Git 最核心的功能之一，让你可以在不影响主线的情况下开发新功能。

#### 基本操作

```bash
# 查看所有分支
git branch

# 创建新分支
git branch feature-login

# 切换分支
git switch feature-login
# 等价于旧写法：git checkout feature-login

# 创建并切换到新分支（一步到位）
git switch -c feature-register
# 等价于旧写法：git checkout -b feature-register

# 删除分支（已合并的）
git branch -d feature-login

# 强制删除分支（未合并也删）
git branch -D feature-login

# 重命名当前分支
git branch -m new-name
```

#### 合并分支 `git merge`

```bash
# 先切回主分支
git switch main

# 把 feature-login 分支合并到当前分支（main）
git merge feature-login
```

> **Fast-forward 合并**：如果 main 没有新提交，Git 会直接把 main 指针移到 feature 分支的最新提交，这叫快进合并。
> 
> **三方合并**：如果 main 也有新提交，Git 会创建一个新的 merge commit，合并两个分支的修改。

#### 查看分支合并图

```bash
git log --oneline --graph --all
```

### 6. 远程仓库操作

```bash
# 克隆远程仓库
git clone https://github.com/user/repo.git

# 查看远程仓库信息
git remote -v

# 推送到远程仓库
git push origin main

# 拉取远程更新（合并到当前分支）
git pull origin main

# 拉取远程更新但不合并（只下载）
git fetch origin

# 设置上游分支（推送时自动关联）
git push -u origin main
# 之后只需 git push / git pull 即可
```

> **`pull` vs `fetch`**：
> - `git pull` = `git fetch` + `git merge`
> - `fetch` 更安全，可以先查看远程变更再决定是否合并

### 7. 暂存工作 `git stash`

当你正在开发一个功能，突然需要切换到另一个分支修 bug，但当前修改还没完成不想提交：

```bash
# 暂存当前工作区的修改
git stash

# 查看暂存列表
git stash list

# 恢复最近一次暂存（并删除该暂存记录）
git stash pop

# 恢复最近一次暂存（保留暂存记录）
git stash apply

# 恢复指定的暂存
git stash apply stash@{2}

# 删除指定暂存
git stash drop stash@{0}

# 清空所有暂存
git stash clear
```

### 8. 变基 `git rebase`

rebase 可以让提交历史更加整洁（线性），但需要谨慎使用。

```bash
# 把当前分支变基到 main 上（让当前分支的提交基于 main 的最新状态）
git switch feature-login
git rebase main

# 交互式变基（可以合并、修改、删除提交）
git rebase -i HEAD~3
# 会打开编辑器，让你选择对最近3个commit的操作：
# pick   = 保留
# squash = 合并到上一个commit
# reword = 修改commit信息
# drop   = 删除
```

> ⚠️ **黄金法则**：**永远不要对已经 push 到远程的 commit 做 rebase**，否则会导致他人的提交历史混乱。

### 9. 解决合并冲突

当两个分支修改了同一文件的同一位置时，会产生冲突：

```bash
# 合并时出现冲突
git merge feature-login
# Auto-merging index.html
# CONFLICT (content): Merge conflict in index.html

# 打开冲突文件，会看到类似内容：
# <<<<<<< HEAD
# 当前分支的内容
# =======
# 被合并分支的内容
# >>>>>>> feature-login

# 手动编辑文件，保留需要的内容，删除冲突标记
# 然后标记冲突已解决
git add index.html
git commit
```

### 10. 标签 `git tag`

标签用于标记重要的版本节点，如发布版本：

```bash
# 创建轻量标签
git tag v1.0

# 创建附注标签（推荐，包含更多信息）
git tag -a v1.0 -m "Release version 1.0"

# 查看所有标签
git tag

# 查看标签详情
git show v1.0

# 给历史提交打标签
git tag -a v0.9 abc1234

# 推送标签到远程
git push origin v1.0
git push origin --tags  # 推送所有标签

# 删除标签
git tag -d v1.0
git push origin --delete v1.0  # 删除远程标签
```

### 11. .gitignore 文件

在项目根目录创建 `.gitignore` 文件，指定 Git 不跟踪的文件：

```gitignore
# 编译产物
*.o
*.class
build/
dist/

# 依赖目录
node_modules/
vendor/

# 环境配置
.env
.env.local

# IDE 配置
.vscode/
.idea/

# 系统文件
.DS_Store
Thumbs.db
```

### 12. 工业级工作流 —— Git Flow

在团队协作中，规范的工作流至关重要。Git Flow 是最经典的模型：

```
main        ──●──────────●──────────●──  （生产环境，只合并，不直接提交）
              \          /          /
release     ───●────────●         /      （发布准备分支）
                \      /         /
develop    ──●───●──●───●──●──●──●──●──  （开发主线）
              \      /    \  /
feature   ────●──●──●     ●──●          （功能分支）
                              \
hotfix   ──────────────────────●──●──   （紧急修复分支，基于 main）
```

#### 分支说明

| 分支 | 用途 | 来源 | 合并到 |
|------|------|------|--------|
| `main` | 生产环境代码 | - | - |
| `develop` | 开发主线 | main | release |
| `feature/*` | 新功能开发 | develop | develop |
| `release/*` | 发布准备 | develop | main + develop |
| `hotfix/*` | 紧急修复 | main | main + develop |

#### 团队协作常用命令

```bash
# 1. 从 develop 创建功能分支
git switch -c feature/user-auth develop

# 2. 开发完成后，定期同步 develop 的更新
git fetch origin
git rebase origin/develop

# 3. 推送到远程
git push -u origin feature/user-auth

# 4. 在 GitHub/GitLab 上创建 Pull Request / Merge Request

# 5. Code Review 通过后合并到 develop

# 6. 删除已合并的功能分支
git branch -d feature/user-auth
git push origin --delete feature/user-auth
```

### 13. 高级操作

```bash
# Cherry-pick：将某个 commit 应用到当前分支
git cherry-pick abc1234

# Bisect：二分查找引入 bug 的 commit
git bisect start
git bisect bad              # 当前版本有 bug
git bisect good v1.0        # v1.0 没有 bug
# Git 会自动二分，你标记 good/bad 直到找到问题 commit
git bisect reset            # 结束查找

# Blame：查看文件每行是谁在什么时候修改的
git blame index.html

# Reflog：查看所有操作记录（即使 reset 了也能找回）
git reflog

# 从 reflog 中恢复误删的 commit
git reset --hard abc1234
```

### 14. 实用技巧

```bash
# 修改最近一次 commit 的信息
git commit --amend -m "new message"

# 查看简洁的分支图
git log --oneline --graph --all --decorate

# 临时忽略文件权限变更
git config core.fileMode false

# 清除未跟踪的文件（先查看会删什么）
git clean -n

# 清除未跟踪的文件（真正删除）
git clean -fd

# 只暂存文件的部分修改
git add -p index.html
```

