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





















