git的工作方式：

保存每一次更新的文件快照。

![1566267731370](.\git1.png)

特性：

1. 本地保存所有文件和资源，不需要联网，就可以不断提交更新，当有网的时候，可以上传到远程仓库。本地也保存了当前项目的所有历史更新，方便查看。
2. 按照内容的校验和，判断文件是否修改和完整。git使用SHA-1哈希值作为索引，不依赖文件名。
3. 通过快照，git可以方便的回退和重现版本，删除数据等等。



文件三种状态：

- 已提交（committed）：该文件被安全地保存在数据库中
- 已修改（modified）：已经修改，但还没保存
- 已暂存（staged）：已修改的文件放在下次提交时要保存的清单中



.git文件夹是git用来保存元数据和对象数据库的地方。



git的工作流程：

1. 在工作目录中修改某些文件
2. 对修改后的文件进行快照，保存到暂存区域（一个文本文件在.git目录下）
3. 提交更新，将保存在暂存区域的文件快照永久转储到git目录中



git指令：

- `git init`：初始化一个git仓库
- `git clone`: 克隆一个远程仓库
- `git add .`：暂存已经修改的所有文件
- `git commit -m "xxx"`：提交所有暂存的文件，保存到本地数据库中
- `git push origin master`：推送所有修改的代码到远程
- `git checkout -b "xxx"`：创建并切换到xxx分值上
- `git merge xxx`：在当前分支，将xxx分值合入到当前分支
- `git rm file`：移除对file文件的提交
- `git status`：查看当前分支修改状况
- `git log`：查看commit的修改情况
- `git fetch origin`：拉去远程的更新
- `git commit --amend`：修改前一次commit的信息，这样就可以不用提交那么多次commit
- `git reset --hard version`：回退代码
  - `git reset --hard HEAD^`：HEAD表示当前版本，`^`表示上一个版本，`^^`表示上两个版本。
