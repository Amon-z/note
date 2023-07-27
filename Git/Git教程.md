# Git 教程

## Window 安装 Git

下载 Git 便携版，解压并添加快捷方式到桌面。

windows ui language 中文。

主题 nord。

透明度低。

光标 方块 闪烁。

字体 IntelOne Mono Medium，Medium，三号。

窗口，调整到合适大小并选择当前大小。

将快捷方式的起始位置设置为工作路径。

```sh
$ git config --global user.name Alan
$ git config --global user.email "youremail@example.com"
```

## 创建版本库

新建一个文件夹，进入文件夹并执行`git init`命令即可创建一个空仓库。

```sh
$ git init # 初始化仓库
```

添加文件到仓库

```sh
$ git add readme.txt # 添加文件到暂存区
$ git commit -m "注释信息" # 提交暂存区文件到存储库

$ git diff readme.txt  # 比较工作区文件与存储库文件的区别

$ git status

$ git log  # 查看历史记录
$ git log --pretty=oneline  # 简化日志输出
```

### 版本回退

git中用`HEAD`表示当前版本，用`HEAD^`表示上一个版本，上上个版本就是`HEAD^^`，往上100个版本则是写成`HEAD~100`。

```sh
$ git reset --hard HEAD^ # 将版本回退到上一个版本
$ git reset --hard <commitid> # 将版本回退到某个commit
$ git reflog # 查看每一次版本变更，可用于版本回退后再回到未来
```

### 工作区和暂存区

git add是先将工作区文件提交到暂存区，commit的时候将暂存区文件提交到分支。git管理的是修改而不是文件，如果第一次修改提交到暂存区，第二次修改没提交，commit的时候只会提交第一次修改的内容到存储库。

### 撤销修改

```sh
# 丢弃工作区的修改
$ git restore <file>

# 把暂存区的修改撤销放回到工作区
$ git restore --staged <file>

#当已提交版本库但未推送到远程时可以通过版本回退撤回修改
$ git reset --hard HEAD^
```

### 删除文件

在文件资源管理器里将文件删除，或者使用rm命令删除文件。

然后使用`git rm <file> 或 git add <file>`添加到暂存区并用commit。

！！！如果是误删文件可以用`git checkout -- <file>`将文件从版本库中恢复。

## 远程仓库

创建SSH Key，在用户主目录下如果没有.ssh目录包含id_rsa和id_rsa.pub两个文件，说明没有创建SSH Key。

```sh
$ ssh-keygen -t rsa -C "youemail@example.com"
$ cat ~/.ssh/id_rsa.pub
```

将id_rsa.pub文件中的内容添加到Github或其他远程仓库。

### 先有本地库再有远程库

已有本地库的情况下，在github新建远程库，然后在本地仓库下运行命令：

```sh
$ git remote add origin git@gitlab.com:a9413530/note.git
$ git push -u origin main # 第一次推送加上-u参数，会将本地与远程的main分支关联
$ git push origin main # 非第一次推送就不用加-u了

$ git remote -v # 查看所有远程库
$ git remote rm origin
```

### 先有远程库再有本地库

github新建远程库。克隆一个本地库。

```sh
$ git clone  git@gitlab.com:a9413530/note.git
```

## 分支管理



