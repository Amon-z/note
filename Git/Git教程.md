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

设置ssh密钥。

## 创建版本库

新建一个文件夹，进入文件夹并执行`git init`命令即可创建一个空仓库。

```sh
$ mkdir test
$ cd test
$ git init
```

添加文件到仓库

```sh
$ touch readme.txt
$ git status
$ git add readme.txt
$ git commit -m "wrote a readme file"
$ echo hello > readme.txt
$ git diff readme.txt
$ git log # 查看历史记录
$ git log --pretty=oneline # 简化日志输出
```

### 版本回退

git中用`HEAD`表示当前版本，用`HEAD^`表示上一个版本，上上个版本就是`HEAD^^`，往上100个版本则是写成`HEAD~100`。

```sh
$ git reset --hard HEAD^ # 将版本回退到上一个版本
$ git reflog # 查看每一次版本变更
```

### 工作区和暂存区

git add是先将工作区文件提交到暂存区，commit的时候将暂存区文件提交到分支。

git管理的时候修改而不是文件。



### 撤销修改

```sh
# 丢弃工作区的修改，如果自修改后还没有被放到暂存区，撤销修改后将和版本库相同，否则则和暂存区相同
$ git checkout -- readme.txt  

# 可以把暂存区的修改撤销放回到工作区
$ git reset HEAD readme.txt  

#当已提交版本库但未推送到远程时可以通过版本回退撤回修改
$ git reset --hard HEAD^
```



### 删除文件

















