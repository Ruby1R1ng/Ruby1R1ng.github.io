  ---
  title: "git命令"
  date: 2026-03-04
  ---

- git pull origin main
  
  从远程仓库 origin 的 main 分支拉取最新代码，并合并到你本地当前分支。
  
  用途：把你在网页上改的内容同步到本地。
  
  origin 是“远程仓库的别名”，默认指向你克隆时的那个 GitHub 仓库地址。使用`git remote -v` 会看到类似：
  ```text
  origin  https://github.com/Ruby1R1ng/Ruby1R1ng.github.io.git
  ```

- git add .
  把当前目录下所有改动（新文件、修改、删除）加入“待提交区”。
  用途：告诉 Git 这些改动我要提交。

- git commit -m "说明"
  把待提交区内容保存为一次提交（快照）。
  -m 后面是这次修改说明，比如 "update notes"。

- git push origin main
  把你本地 main 分支的新提交上传到远程 origin。
  用途：把本地改动发布到 GitHub，并触发网站自动部署。

- git status
  查看当前状态：哪些文件改了、是否已 add、是否可 commit。
  用途：最常用检查命令，建议经常先看它。

