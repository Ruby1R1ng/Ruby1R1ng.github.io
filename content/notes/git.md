---
title: "git命令"
---

# Git指令

# git 初始化
Linux/macOS 终端
```bash
git init
```

# 创建gitignore

```bash
touch .gitignore
```
Windows
```bash
notepad .gitignore
```
把这个内容放进 .gitignore 里
```bash
__pycache__/
*.pyc
.venv/
venv/
env/
.idea/
.vscode/
.env
data/
outputs/
checkpoints/
*.pth
*.pt
*.ckpt
.DS_Store
*.log
```

# Git添加文件
保存后，先运行：
```bash
git status
```
看看当前有哪些文件会被纳入版本管理。  查看当前状态：哪些文件改了、是否已 add、是否可 commit。

然后只添加你想提交的文件，比如：
```bash
git add main.py README.md
```
如果想添加所有文件就用
```bash
git add .
```

# 提交
```bash
git commit -m "Initial commit"
```
给这个远程仓库地址起一个名字，名字叫 origin
```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```
git remote add：添加一个远程仓库

origin：你给这个远程仓库取的名字

后面那串 URL：这个远程仓库真正的地址

# 推送

把当前分支重命名成 main
```bash
git branch -M main
```
-M 可以理解成：强制重命名

把本地的 main 分支推送到远程仓库 origin 上，并建立跟踪关系。
```bash
git push -u origin main
```

# 拉取


```bash
git pull origin main
```
从远程仓库 origin 的 main 分支拉取最新代码，并合并到你本地当前分支。
  
origin 是“远程仓库的别名”，默认指向你克隆时的那个 GitHub 仓库地址。使用`git remote -v` 会看到类似：
```text
origin  https://github.com/Ruby1R1ng/Ruby1R1ng.github.io.git
```



