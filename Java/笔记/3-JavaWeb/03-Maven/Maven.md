# Maven

## 简介

Apache Maven 是一个项目管理和构建工具，它基于项目对象模型(POM)的概念，通过一小段描述信息来管理项目的构建、报告和文档

- 主要功能
  - 提供了一套标准化的项目结构
  - 提供了一套标准化的构建流程（编译，测试，打包，发布……）
  - 提供了一套依赖管理机制

- 依赖管理
  - Maven 使用标准的坐标配置来管理各种依赖
  - 只需要简单的配置就可以完成依赖管理

![image-20231110022329514](./assets/image-20231110022329514.png)

![image-20231110022447139](./assets/image-20231110022447139.png)

## Maven安装配置

下载Maven，解压。[下载链接](https://maven.apache.org/download.cgi)。

配置系统环境变量。

配置本地仓库：修改conf/settings.xml 中的 `<localRepository>` 为一个指定目录。

配置阿里云私服，修改 conf/settings.xml 中的 `<mirrors>`标签，添加如下子标签

```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

## Maven常用命令

compile ：编译

clean：清理

test：测试

package：打包

install：安装

## Maven生命周期

Maven 构建项目生命周期描述的是一次构建过程经历经历了多少个事件，同一生命周期内，执行后边的命令，前边的所有命令会自动执行

- Maven 对项目构建的生命周期划分为3套

  - clean：清理工作

  - default：核心工作，例如编译，测试，打包，安装等

  - site：产生报告，发布站点等

![image-20231110022924028](./assets/image-20231110022924028.png)

![image-20231110110702157](./assets/image-20231110110702157.png)

## IDEA配置Maven

打开IDEA，在不打开项目的情况下打开设置，或者按Ctrl+Alt+s，搜索Maven。

修改Maven的路径，配置文件的路径和本地仓库的路径并保存。

![image-20231110040252120](./assets/image-20231110040252120.png)

## Maven坐标详解

- 坐标

  - Maven 中的坐标是资源的唯一标识

  - 使用坐标来定义项目或引入项目中需要的依赖

- Maven坐标主要组成
  - groupId：定义当前Maven项目隶属组织名称（通常是域名反写，例如：com.baidu）
  - artifactId：定义当前Maven项目名称（通常是模块名称，例如 order-service、goods-service）
  - version：定义当前项目版本号

![image-20231110024509622](./assets/image-20231110024509622.png)

## IDEA创建Maven项目

IDEA创建Maven项目

IDEA导入Maven项目

IDEA安装Maven-Helper插件

## 依赖管理

- 添加依赖

  - 使用坐标导入jar包

    - 在 pom.xml 中编写 `<dependencies>`标签

    - 在`<dependencies>`标签中 使用`<dependency>`引入坐标

    - 定义坐标的 `groupId,artifactId,version`

    - 点击刷新按钮，使坐标生效

  - 使用坐标导入jar包-快捷方式
    - 在pom.xml中按Alt + Insert，选择 Dependency
    - 在弹出的面板中搜索对应坐标，然后双击选中对应坐标
    - 点击刷新按钮，使坐标生效

- 修改依赖自动生效自动导入
  - 选择 IDEA中 File --> Settings
  - 在弹出的面板中找到 Build Tools
  - 选择 Any changes，点击 ok 即可生效

## 依赖范围

![image-20231110025114083](./assets/image-20231110025114083.png)