我来为你提供一个完整的本地文件上传到GitHub的完整流程。以下是详细步骤：

## 1. 准备工作

### 1.1 检查Git是否已安装
打开Git Bash，输入以下命令检查：
```bash
git --version
```

### 1.2 配置Git用户信息（如未配置）
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## 2. 初始化本地仓库

### 2.1 进入你的本地文件夹
```bash
cd /path/to/your/local/folder
```
或者使用相对路径。

### 2.2 初始化Git仓库
```bash
git init
```

## 3. 添加文件到暂存区

### 3.1 查看当前状态
```bash
git status
```

### 3.2 添加所有文件
```bash
# 添加所有文件
git add .

# 或者添加特定文件
# git add filename.txt
```

## 4. 提交更改

```bash
git commit -m "Initial commit: add all files"
```
（可以替换引号内的描述为你的提交信息）

## 5. 连接到GitHub远程仓库

### 5.1 添加远程仓库地址
```bash
git remote add origin https://github.com/syntaxnom/forge.git
```

### 5.2 验证远程仓库
```bash
git remote -v
```
应该显示：
```
origin  https://github.com/syntaxnom/forge.git (fetch)
origin  https://github.com/syntaxnom/forge.git (push)
```

## 6. 推送到GitHub

### 6.1 首次推送
```bash
git push -u origin main
```
或者如果你的默认分支是master：
```bash
git push -u origin master
```

### 6.2 如果遇到错误（分支不匹配）
如果出现错误提示分支不匹配，可以执行：
```bash
# 重命名本地分支（如果本地是master）
git branch -M main

# 然后再推送
git push -u origin main
```

## 7. 完整流程示例

以下是完整的命令序列，你可以复制粘贴执行：

```bash
# 1. 进入项目文件夹
cd "C:/Users/YourName/your-project-folder"

# 2. 初始化Git
git init

# 3. 检查状态
git status

# 4. 添加文件
git add .

# 5. 提交
git commit -m "Initial commit"

# 6. 添加远程仓库
git remote add origin https://github.com/syntaxnom/forge.git

# 7. 重命名分支（如果需要）
git branch -M main

# 8. 推送
git push -u origin main
```

## 8. 后续更新流程

当你有新更改时：

```bash
# 1. 添加更改
git add .

# 2. 提交
git commit -m "描述你的更改"

# 3. 推送到GitHub
git push
```

## 9. 常见问题解决

### 9.1 如果GitHub仓库不为空
如果你的GitHub仓库已经有文件（如README.md）：

```bash
# 先拉取远程更改
git pull origin main --allow-unrelated-histories

# 解决可能的冲突后推送
git push origin main
```

### 9.2 权限问题
如果提示权限错误：
- 确认你已登录GitHub账号
- 使用SSH替代HTTPS（需要配置SSH密钥）

### 9.3 查看日志
```bash
git log --oneline
```

## 10. 可选：创建.gitignore文件

创建`.gitignore`文件来忽略不需要上传的文件：

```bash
# 在项目根目录创建.gitignore文件
# 添加要忽略的文件/文件夹，例如：

# 系统文件
.DS_Store
Thumbs.db

# 依赖文件夹
node_modules/
venv/

# 编译文件
*.pyc
__pycache__/

# 环境变量文件
.env
```

## 提示
1. 使用 `git status` 随时查看状态
2. 使用 `git log` 查看提交历史
3. 使用 `git diff` 查看具体更改内容
4. 使用 `git branch` 查看分支

这样你的本地文件就成功上传到GitHub了！有任何问题可以告诉我具体错误信息。

# 完整Git操作指南：从基础到进阶

## 一、添加指定文件并上传的完整流程

### 1.1 单文件上传流程
```bash
# 1. 进入项目目录
cd /path/to/your/project

# 2. 查看文件状态
git status

# 3. 添加指定文件（单个）
git add specific-file.txt

# 4. 提交到本地仓库
git commit -m "Add specific-file.txt"

# 5. 推送到GitHub
git push origin main
```

### 1.2 多文件选择性上传
```bash
# 1. 添加多个指定文件
git add file1.txt file2.js file3.css

# 2. 或者使用通配符
git add src/*.js      # 添加src目录下所有js文件
git add docs/*.md     # 添加docs目录下所有md文件
git add images/*.png  # 添加images目录下所有png文件

# 3. 查看暂存区状态
git status --short

# 4. 提交
git commit -m "Add multiple specific files"

# 5. 推送
git push origin main
```

### 1.3 排除文件后再添加
```bash
# 1. 先添加所有文件
git add .

# 2. 从暂存区移除不需要的文件
git reset unwanted-file.txt

# 3. 或者直接添加时排除
git add . -- ':!unwanted-file.txt' ':!temp-folder/'

# 4. 提交剩余文件
git commit -m "Add all files except excluded ones"
```

## 二、创建和使用分支的完整流程

### 2.1 基础分支操作流程
```bash
# 1. 查看当前分支
git branch
git branch -a  # 查看所有分支（包括远程）

# 2. 创建新分支
git branch feature/new-feature

# 3. 切换到新分支
git checkout feature/new-feature

# 或者一步完成创建和切换
git checkout -b feature/new-feature

# 4. 在新分支上开发
# 进行代码修改...

# 5. 提交更改
git add .
git commit -m "Implement new feature"

# 6. 推送到远程仓库（首次推送需要设置上游）
git push -u origin feature/new-feature
```

### 2.2 分支合并流程
```bash
# 方法一：合并分支（保留分支历史）
# 1. 切换到主分支
git checkout main

# 2. 拉取最新代码
git pull origin main

# 3. 合并分支
git merge feature/new-feature

# 4. 解决可能出现的冲突
# 如果有冲突，编辑文件解决冲突后：
git add resolved-file.txt
git commit -m "Merge feature/new-feature"

# 5. 推送到远程
git push origin main

# 方法二：变基合并（保持线性历史）
# 1. 在功能分支上
git checkout feature/new-feature
git rebase main

# 2. 解决可能的冲突
# 3. 切回主分支并合并
git checkout main
git merge feature/new-feature
```

### 2.3 分支删除流程
```bash
# 1. 删除本地分支（已合并）
git branch -d feature/old-feature

# 2. 强制删除本地分支（未合并）
git branch -D feature/abandoned-feature

# 3. 删除远程分支
git push origin --delete feature/old-feature
# 或者
git push origin :feature/old-feature
```

## 三、分支使用技巧和流程

### 3.1 Git Flow工作流
```bash
# 主要分支结构：
# main/master - 生产分支
# develop     - 开发分支
# feature/*   - 功能分支
# release/*   - 发布分支
# hotfix/*    - 热修复分支

# 1. 初始化Git Flow（如果需要）
git flow init

# 2. 开始新功能
git flow feature start authentication

# 3. 完成功能开发
git flow feature finish authentication

# 4. 开始发布版本
git flow release start v1.0.0

# 5. 紧急修复生产问题
git flow hotfix start fix-login-bug
```

### 3.2 GitHub Flow（简化版）
```bash
# 1. 主分支始终保持可部署状态
# 2. 从main分支创建新分支
git checkout -b feature/add-user-profile

# 3. 开发并频繁提交
git add .
git commit -m "Add user profile component"
git commit -m "Add profile validation"
git commit -m "Fix profile image upload"

# 4. 推送并创建Pull Request
git push origin feature/add-user-profile

# 5. 在GitHub上发起Pull Request
# 6. 讨论、审查代码
# 7. 部署和测试
# 8. 合并到main分支
```

### 3.3 分支管理最佳实践
```bash
# 1. 保持分支更新
git checkout feature/my-feature
git rebase main  # 或 git merge main

# 2. 交互式变基整理提交
git rebase -i HEAD~5  # 整理最近5次提交

# 3. 分支命名规范
# feature/user-authentication
# bugfix/login-error
# hotfix/security-patch
# release/v1.2.0
# chore/update-dependencies

# 4. 定期清理旧分支
# 查看已合并的分支
git branch --merged main

# 删除已合并的本地分支
git branch --merged main | grep -v "^\*\|main\|develop" | xargs -n 1 git branch -d
```

## 四、常用指令速查表

### 4.1 基础操作
```bash
# 配置
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
git config --list

# 仓库操作
git init
git clone https://github.com/user/repo.git
git remote add origin https://github.com/user/repo.git
git remote -v
git remote remove origin

# 状态查看
git status
git status -s  # 简略状态
git log
git log --oneline --graph --all
git show <commit-hash>
git diff
git diff --staged
```

### 4.2 提交相关
```bash
# 添加文件
git add <file>
git add .
git add -A
git add -p  # 交互式添加

# 提交
git commit -m "message"
git commit -am "message"  # 添加并提交已跟踪文件
git commit --amend        # 修改最近一次提交
git commit --amend --no-edit  # 添加文件到最近提交但不修改信息

# 撤销
git restore <file>        # 撤销工作区修改
git restore --staged <file>  # 从暂存区移除
git reset HEAD <file>     # 同上，旧写法
git reset --soft HEAD~1   # 撤销提交但保留修改
git reset --hard HEAD~1   # 完全撤销提交
git revert <commit-hash>  # 创建新提交来撤销旧提交
```

### 4.3 分支操作
```bash
# 创建和切换
git branch
git branch <name>
git checkout <branch>
git checkout -b <branch>
git switch <branch>       # Git 2.23+
git switch -c <branch>    # 创建并切换

# 合并
git merge <branch>
git merge --no-ff <branch>  # 非快进合并
git rebase <branch>
git rebase -i <commit>    # 交互式变基

# 删除
git branch -d <branch>
git branch -D <branch>
```

### 4.4 远程操作
```bash
# 推送
git push
git push origin <branch>
git push -u origin <branch>  # 设置上游分支
git push --force            # 强制推送（谨慎使用）
git push --force-with-lease # 更安全的强制推送

# 拉取
git pull
git pull origin <branch>
git fetch                   # 仅获取不合并
git fetch --prune          # 获取并清理已删除的远程分支

# 跟踪分支
git branch -vv             # 查看跟踪关系
git branch -u origin/<branch>  # 设置上游分支
```

### 4.5 高级操作
```bash
# 储藏
git stash
git stash save "message"
git stash list
git stash apply
git stash pop
git stash drop
git stash clear

# 标签
git tag
git tag v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin --tags
git push origin v1.0.0
```

## 五、常见问题及解决办法

### 5.1 推送失败问题
```bash
# 问题：远程有本地没有的提交
error: failed to push some refs to 'github.com:user/repo.git'
hint: Updates were rejected because the remote contains work that you do not have locally.

# 解决方案：
# 方法1：先拉取再合并
git pull origin main
# 解决可能出现的冲突
git add .
git commit -m "Merge remote changes"
git push origin main

# 方法2：变基
git pull --rebase origin main
git push origin main

# 方法3：强制推送（谨慎！会覆盖他人提交）
git push --force origin main
# 或更安全的
git push --force-with-lease origin main
```

### 5.2 合并冲突解决
```bash
# 冲突时Git会显示：
CONFLICT (content): Merge conflict in file.txt

# 解决步骤：
# 1. 查看冲突文件
git status

# 2. 打开冲突文件，找到冲突标记：
<<<<<<< HEAD
本地代码
=======
远程代码
>>>>>>> branch-name

# 3. 手动编辑文件，保留需要的代码，删除冲突标记

# 4. 标记冲突已解决
git add resolved-file.txt

# 5. 继续合并
git commit -m "Resolve merge conflict"

# 或者使用合并工具
git mergetool
```

### 5.3 撤销错误操作
```bash
# 1. 撤销未暂存的修改
git checkout -- <file>
# 或（Git 2.23+）
git restore <file>

# 2. 撤销已暂存的修改
git reset HEAD <file>
# 或
git restore --staged <file>

# 3. 撤销最近一次提交但保留修改
git reset --soft HEAD~1

# 4. 完全撤销最近一次提交
git reset --hard HEAD~1

# 5. 撤销特定的提交（创建新提交来撤销）
git revert <commit-hash>

# 6. 找回误删的分支或提交
# 查看历史操作
git reflog
# 恢复到指定操作
git reset --hard HEAD@{n}
```

### 5.4 大文件问题
```bash
# 问题：尝试推送大文件
remote: error: File large-file.zip is 1024.00 MB; this exceeds GitHub's file size limit of 100.00 MB

# 解决方案：
# 1. 从Git历史中移除大文件
# 安装git-filter-repo（推荐）或使用BFG Repo Cleaner

# 2. 使用Git LFS（大文件存储）
# 安装Git LFS
git lfs install

# 跟踪大文件类型
git lfs track "*.psd"
git lfs track "*.zip"
git lfs track "*.exe"

# 提交.lfsconfig文件
git add .gitattributes
git commit -m "Add Git LFS tracking"

# 推送到GitHub
git push origin main
```

### 5.5 权限问题
```bash
# 问题：没有权限推送
ERROR: Permission to user/repo.git denied to other-user.

# 解决方案：
# 1. 检查远程地址
git remote -v

# 2. 如果是HTTPS，检查是否已登录
# 清除凭据（Windows）
git credential-manager reject https://github.com

# 3. 使用SSH替代HTTPS
# 生成SSH密钥
ssh-keygen -t ed25519 -C "your_email@example.com"

# 添加到ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 复制公钥到GitHub
cat ~/.ssh/id_ed25519.pub

# 更改远程地址
git remote set-url origin git@github.com:user/repo.git
```

### 5.6 忽略文件问题
```bash
# 问题：已提交的文件无法被.gitignore忽略

# 解决方案：
# 1. 从Git中删除但保留本地文件
git rm --cached <file>
git rm -r --cached <folder>

# 2. 添加到.gitignore
echo "file-to-ignore.txt" >> .gitignore
echo "folder-to-ignore/" >> .gitignore

# 3. 提交更改
git add .gitignore
git commit -m "Add to .gitignore and remove from tracking"

# 4. 推送到远程
git push origin main

# 注意：这种方法只对未提交过的文件有效
# 对于已提交的历史文件，需要使用git filter-repo清理历史
```

### 5.7 分支混乱问题
```bash
# 问题：分支太多，难以管理

# 清理策略：
# 1. 列出已合并到main的分支
git branch --merged main

# 2. 批量删除已合并的本地分支
git branch --merged main | grep -v "^\*\|main\|develop" | xargs -n 1 git branch -d

# 3. 列出远程已合并的分支
git branch -r --merged main

# 4. 批量删除远程已合并的分支
git branch -r --merged main | grep -v "main" | sed 's/origin\///' | xargs -n 1 git push origin --delete

# 5. 同步本地分支列表
git fetch --prune
```

### 5.8 提交信息错误
```bash
# 修改最近一次提交信息
git commit --amend -m "New commit message"

# 修改多个提交信息（交互式变基）
git rebase -i HEAD~3
# 在编辑器中，将需要修改的提交前的"pick"改为"reword"或"r"

# 修改作者信息
git commit --amend --author="New Author <new@email.com>"

# 修改更早的提交（需要强制推送）
git rebase -i HEAD~5
# 编辑后强制推送
git push --force-with-lease origin main
```

## 六、实用别名和配置

### 6.1 Git别名设置
```bash
# 添加到 ~/.gitconfig 或执行以下命令

# 简写
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.pl pull
git config --global alias.ps push

# 实用别名
git config --global alias.lg "log --oneline --graph --decorate --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.wip "!git add -A && git commit -m 'WIP'"
```

### 6.2 配置文件示例
```ini
# ~/.gitconfig
[user]
    name = Your Name
    email = your.email@example.com

[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    lg = log --oneline --graph --decorate --all
    unstage = reset HEAD --
    last = log -1 HEAD

[core]
    editor = code --wait  # 使用VSCode作为编辑器
    autocrlf = true       # Windows换行符处理

[push]
    default = simple

[pull]
    rebase = false

[init]
    defaultBranch = main
```

这个指南涵盖了从基础到进阶的Git操作，你可以根据需要使用相应的命令。记得在执行破坏性操作（如强制推送、硬重置）前，确保理解其影响。

# SSH配置和使用的完整流程

## 一、SSH密钥生成与配置

### 1.1 检查现有SSH密钥
```bash
# 查看是否已有SSH密钥
ls -al ~/.ssh
```
应该看到类似的文件：
- `id_rsa` 和 `id_rsa.pub` (RSA密钥)
- `id_ed25519` 和 `id_ed25519.pub` (Ed25519密钥)

### 1.2 生成新的SSH密钥（推荐Ed25519算法）
```bash
# 使用Ed25519算法（更安全、更快）
ssh-keygen -t ed25519 -C "your_email@example.com"

# 或者使用RSA算法（兼容性更好）
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

**生成过程示例：**
```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/YourName/.ssh/id_ed25519):
# 直接按Enter使用默认路径

Enter passphrase (empty for no passphrase):
# 可以设置密码（推荐），也可以直接按Enter跳过
Enter same passphrase again:
# 再次输入密码或直接按Enter

Your identification has been saved in /c/Users/YourName/.ssh/id_ed25519
Your public key has been saved in /c/Users/YourName/.ssh/id_ed25519.pub
```

### 1.3 启动SSH代理并添加密钥
```bash
# 1. 确保ssh-agent正在运行
eval "$(ssh-agent -s)"
# 应该显示：Agent pid 12345

# 2. 将SSH私钥添加到ssh-agent
ssh-add ~/.ssh/id_ed25519
# 如果设置了密码，会提示输入

# 3. 检查密钥是否已添加
ssh-add -l
```

## 二、将SSH密钥添加到GitHub

### 2.1 复制公钥
```bash
# 方法1：使用clip命令（Windows Git Bash）
cat ~/.ssh/id_ed25519.pub | clip

# 方法2：使用cat查看并手动复制
cat ~/.ssh/id_ed25519.pub
# 会显示类似的内容：
# ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJdL... your_email@example.com
```

### 2.2 在GitHub中添加SSH密钥

**步骤：**
1. 登录GitHub
2. 点击右上角头像 → **Settings**
3. 左侧菜单 → **SSH and GPG keys**
4. 点击 **New SSH key** 按钮
5. 填写：
   - **Title**: 给你的密钥起个名字（如"My Windows Laptop"）
   - **Key type**: 保持默认 **Authentication Key**
   - **Key**: 粘贴刚才复制的公钥内容
6. 点击 **Add SSH key**

## 三、测试SSH连接

```bash
# 测试连接到GitHub
ssh -T git@github.com

# 成功会显示：
# Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

如果失败，检查：
```bash
# 检查密钥权限（重要！）
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub

# 如果还是不行，使用详细模式查看问题
ssh -Tv git@github.com
```

## 四、配置Git使用SSH

### 4.1 修改现有仓库的远程地址
```bash
# 查看当前远程地址
git remote -v
# 如果是HTTPS地址，会显示：
# origin  https://github.com/syntaxnom/forge.git (fetch)
# origin  https://github.com/syntaxnom/forge.git (push)

# 修改为SSH地址
git remote set-url origin git@github.com:syntaxnom/forge.git

# 验证更改
git remote -v
# 现在应该显示：
# origin  git@github.com:syntaxnom/forge.git (fetch)
# origin  git@github.com:syntaxnom/forge.git (push)
```

### 4.2 克隆新仓库使用SSH
```bash
# 克隆时直接使用SSH地址
git clone git@github.com:syntaxnom/forge.git
```

## 五、多Git账户SSH配置

如果你有多个GitHub账户或需要连接不同服务器：

### 5.1 生成第二个密钥
```bash
# 为工作账户生成密钥
ssh-keygen -t ed25519 -C "work_email@company.com" -f ~/.ssh/id_ed25519_work

# 为个人账户生成密钥（如果之前已生成，跳过）
# ssh-keygen -t ed25519 -C "personal_email@gmail.com" -f ~/.ssh/id_ed25519_personal
```

### 5.2 配置SSH配置文件
```bash
# 创建或编辑SSH配置文件
nano ~/.ssh/config
# 或使用文本编辑器打开
notepad ~/.ssh/config
```

**添加以下内容：**
```config
# 个人GitHub账户（默认）
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes

# 工作GitHub账户
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes

# 公司GitLab
Host gitlab.company.com
    HostName gitlab.company.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes
```

### 5.3 使用不同的主机别名
```bash
# 克隆工作仓库（使用自定义主机名）
git clone git@github-work:syntaxnom/forge.git

# 配置现有仓库使用不同账户
git remote set-url origin git@github-work:syntaxnom/forge.git
```

## 六、常见问题解决

### 6.1 SSH连接超时
```bash
# 1. 检查防火墙设置
# 2. 测试网络连接
ping github.com

# 3. 如果使用代理，配置SSH通过代理
# 在~/.ssh/config中添加：
# Host github.com
#     ProxyCommand nc -X connect -x proxy.example.com:8080 %h %p
```

### 6.2 权限被拒绝
```bash
# 常见错误：
# Permission denied (publickey).

# 解决方案：
# 1. 确认公钥已正确添加到GitHub
# 2. 检查私钥权限
ls -la ~/.ssh/id_ed25519
# 应该是 -rw------- (600)

# 3. 重新启动ssh-agent
# 在Git Bash中：
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 4. 使用详细模式调试
ssh -Tv git@github.com
```

### 6.3 密钥密码忘记
```bash
# 如果忘记了密钥密码，需要重新生成
# 1. 备份旧密钥（如果需要）
cp ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.backup
cp ~/.ssh/id_ed25519.pub ~/.ssh/id_ed25519.pub.backup

# 2. 生成新密钥（不带密码）
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""

# 3. 更新GitHub上的公钥
# 4. 重新添加到ssh-agent
ssh-add ~/.ssh/id_ed25519
```

### 6.4 Windows特定问题
```bash
# 如果ssh-agent无法启动
# 1. 检查服务是否运行
Get-Service ssh-agent

# 2. 手动启动服务
Start-Service ssh-agent

# 3. 设置服务为自动启动
Set-Service -Name ssh-agent -StartupType Automatic

# 在Git Bash中，确保使用Windows的ssh-agent
# 编辑~/.bashrc或~/.bash_profile，添加：
eval $(ssh-agent) > /dev/null
ssh-add ~/.ssh/id_ed25519 2>/dev/null
```

## 七、SSH配置文件详解

完整示例 `~/.ssh/config`：
```config
# 全局配置
Host *
    # 保持连接活跃，防止超时
    ServerAliveInterval 60
    ServerAliveCountMax 5
    # 压缩数据，提高速度
    Compression yes
    # 使用更快的加密算法
    Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com
    # 禁用主机密钥检查（不推荐，仅用于测试）
    # StrictHostKeyChecking no
    # 使用已知主机文件
    UserKnownHostsFile ~/.ssh/known_hosts

# GitHub个人账户（默认）
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
    # 端口（默认22）
    Port 22

# GitHub工作账户
Host github-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes

# 公司内部Git服务器
Host git.company.com
    HostName git.company.com
    User git
    IdentityFile ~/.ssh/id_rsa_company
    Port 2222  # 如果使用非标准端口
    # 使用代理跳板
    # ProxyJump jump-host.company.com

# 备用配置：使用RSA密钥的旧服务器
Host legacy-server
    HostName legacy.example.com
    User admin
    IdentityFile ~/.ssh/id_rsa
    # 旧服务器可能不支持新算法
    KexAlgorithms diffie-hellman-group-exchange-sha256
    MACs hmac-sha2-256
```

## 八、自动化脚本（可选）

### 8.1 一键SSH配置脚本
创建 `setup-ssh.sh`：
```bash
#!/bin/bash

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

echo -e "${GREEN}开始配置SSH...${NC}"

# 1. 检查SSH目录
if [ ! -d ~/.ssh ]; then
    mkdir ~/.ssh
    chmod 700 ~/.ssh
    echo "创建 ~/.ssh 目录"
fi

# 2. 检查现有密钥
if [ -f ~/.ssh/id_ed25519 ]; then
    echo -e "${YELLOW}发现现有SSH密钥，跳过生成${NC}"
else
    # 3. 生成新密钥
    read -p "请输入邮箱地址: " email
    ssh-keygen -t ed25519 -C "$email" -f ~/.ssh/id_ed25519
    
    if [ $? -eq 0 ]; then
        echo -e "${GREEN}SSH密钥生成成功！${NC}"
    else
        echo -e "${RED}密钥生成失败${NC}"
        exit 1
    fi
fi

# 4. 启动ssh-agent
eval "$(ssh-agent -s)" > /dev/null

# 5. 添加密钥
ssh-add ~/.ssh/id_ed25519

# 6. 显示公钥
echo -e "\n${GREEN}你的公钥是：${NC}"
echo "================================"
cat ~/.ssh/id_ed25519.pub
echo "================================"
echo -e "\n${YELLOW}请复制上面的公钥到GitHub${NC}"
echo "访问：https://github.com/settings/keys"

# 7. 测试连接
read -p "按回车键测试SSH连接..."
ssh -T git@github.com
```

### 8.2 使用脚本
```bash
# 1. 保存脚本
# 2. 给予执行权限
chmod +x setup-ssh.sh

# 3. 运行脚本
./setup-ssh.sh
```

## 九、验证和测试

### 9.1 完整的验证流程
```bash
# 1. 验证SSH配置
ssh -T git@github.com

# 2. 验证特定仓库的连接
cd your-repo
git remote -v

# 3. 测试推送（小测试）
touch test-ssh.txt
echo "Testing SSH connection" > test-ssh.txt
git add test-ssh.txt
git commit -m "Test SSH connection"
git push origin main

# 4. 清理测试文件
git rm test-ssh.txt
git commit -m "Remove test file"
git push origin main
```

### 9.2 故障排查命令
```bash
# 查看详细的SSH连接信息
ssh -vvv git@github.com

# 检查ssh-agent状态
ssh-add -l

# 查看SSH配置
ssh -G github.com

# 检查网络连接
telnet github.com 22
# 或
nc -zv github.com 22
```

## 十、最佳实践

1. **使用Ed25519算法**：比RSA更安全、更快
2. **设置强密码**：即使密钥泄露，密码也能提供保护
3. **定期更换密钥**：建议每6-12个月更换一次
4. **使用不同的密钥**：为不同的服务使用不同的密钥
5. **备份私钥**：将私钥保存在安全的地方（加密存储）
6. **禁用密码登录**：在服务器上禁用密码登录，只允许SSH密钥
7. **使用ssh-agent转发**（谨慎使用）：可以在多台服务器间转发密钥
   ```bash
   ssh -A user@server1  # 启用代理转发
   ```

完成以上步骤后，你就成功配置了SSH，可以更安全、更方便地使用Git了。SSH连接不需要每次都输入密码（除非你为密钥设置了密码），大大提高了工作效率。