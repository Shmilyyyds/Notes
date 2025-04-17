# git
## 配置
- `git config --global user.name "your name"`
- `git config --global user.email "your email"`

## .gitignore
```
# 忽略所有.a文件
*.a
# lib.a除外
!lib.a
# 仅忽略当前目录下的 TODO 文件，不包括子目录
/TODO
# 忽略 build/ 目录下所有文件
build/
# 忽略 doc/notes.txt
doc/notes.txt
# 忽略doc/目录下所有.txt文件
doc/**/*.txt
```

## 常用命令
### 基础
- `git add`
- `git commit -m "commit message"`
- `git push`

### 查看日志
- `git log`
- `git log --oneline`
- `git log --graph`
> 查看已删除的日志：`git reflog`

### 回退
- `git reset --hard HEAD^`：回退到上一个版本
- `git reset --hard HEAD~n`：回退到n个版本之前
- `git reset --hard commit_id`：回退到指定版本


## 分支
### 常用命令
- `git branch`：查看分支
- `git branch <name>`：创建分支
- `git checkout <name>`：切换分支
  - `git checkout -b <name>`：创建并切换分支
- `git merge <name>`：合并分支
- `git branch -d <name>`：删除分支（做检查）
  - `git branch -D <name>`：强制删除分支


## 远程仓库
### SSH公钥
- `ssh-keygen -t rsa`：生成公钥（不断回车即可）
- `cat ~/.ssh/id_rsa.pub`：查看公钥内容
- 复制公钥内容到远程仓库的SSH公钥管理页面
- `ssh -T git@github.com`：测试SSH连接是否成功

### 匹配远程仓库
- `git remote add <alias_name> <url>`：添加远程仓库
- `git remote -v`：查看远程仓库

### 推送代码
- `git push [-f] [--set-upstream] [远程名称] [本地分支名][:远程分支名]`
  - 如果远程分支名和本地分支名相同，则可以只写本地分支`git push origin master`
  - `--set-upstream`==`-u`：设置本地分支和远程分支的关联关系，后续可以直接使用`git push`命令`git push --set-upstream origin master`
  - **如果当前分支已经和远端分支关联**，则可省略分支名和远端名`git push`

### 得到远程仓库代码
- `git clone <url> <filename>`：克隆远程仓库到本地
- `git fetch`：从远程仓库获取最新代码，但不合并到本地
- `git pull`：从远程仓库获取最新代码并合并到本地，相当于`fetch+merge`

