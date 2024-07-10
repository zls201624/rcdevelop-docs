# 快速搭建RepChain开发环境

> 快速搭建开发环境，体验区块链
>



## 环境准备

> 需要一些基础环境和工具，如`Jdk`、`IntelliJ IDEA`

需要安装：

1. install [ZuluJdk11+](https://www.azul.com/downloads/zulu-community/?&architecture=x86-64-bit&package=jdk)

   > 建议使用`jdk13`，可使用idea来安装（<u>需要idea为较新的版本</u>）

2. install [Git](https://git-scm.com/downloads)

   > 小贴士：如果开发平台为`win7`可使用`GitBash`替换`cmd/powerShell`来作为常用终端

3. install [Idea](https://www.jetbrains.com/idea/download/#section=windows)

   1. install **scala plugin**

      > 1. 如果是**新装**idea，则在安装idea的时候，[将scala plugin勾选安装](https://www.jetbrains.com/help/idea/run-for-the-first-time.html#additional_plugins)
      > 2. 如果是**已有**idea，则在[ide中搜索scala plugin并安装](https://www.jetbrains.com/help/idea/managing-plugins.html#install_plugin_from_repo)

   2. install [sbt](http://www.scala-sbt.org/release/docs/Setup.html)（**可选**）

      > 如果安装了scala plugin则可在idea中使用[sbt shell](https://www.jetbrains.com/help/idea/sbt-support.html#sbt_shell)来编译RepChain，当然如果本步骤安装了sbt，也可以在终端下使用sbt来编译RepChain



## 开始搭建

假设目前想在目录`/project/`下进行合约开发或者体验RepChain，则可通过以下几种方式来快速搭建环境

* 终端命令行

  > 前提：jdk与sbt已经安装
  
  ```shell
  cd /project/
  git clone https://gitee.com/BTAJL/repchain.git
  cd repchain
  sbt clean compile
  # 修改conf/system.conf中给的contractOperationMode = 1
sbt run
  ```

* idea

  > 前提：jdk与scala plugin已经安装
  
  `File->New->Project from Version Control->Git.`后续使用sbt shell来执行clean compile等命令，使用`rep/app/RepChain.scala`作为主类入口，即可启动RepChain
  

!> <u>**注意的地方：**</u>

- 在启动RepChain之前，需要根据需要修改[存储位置](v1.0.0/config.md#存储)

  > 如果出现磁盘不足的异常，可能有两种可能的原因：1、文件磁盘确实不足，修改配置文件`conf/system.conf`中`diskspaceManager`；2、可能是权限不足，用户在存储目录下不具有写权限
  
  

##  实时状态图

启动RepChain之后，可通过在浏览器中打开`localhost:8081/web/g1.html`查看实时图，实时图的端口可[配置](v1.1.0/config.md#系统)，通过实时图可以查看目前RepChain的工作状态，如果能打开实时图，也可证明区块链环境搭建成功

![front_graph](../_images/front_graph.png ':size=85%')



## API接口

启动RepChain之后，可通过在浏览器中打开`localhost:8081/swagger/index.html`查看接口文档

![swagger-ui](../_images/swagger-ui.png ':size=85%')

