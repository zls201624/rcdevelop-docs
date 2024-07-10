# RepChain部署

> 组建区块链平台，可单机、分布式部署，支持局域网、跨域部署，使用`jar`包部署

打包方式：

1. 修改build.sbt中`mainClass`，[模拟多节点](#模拟多节点)则为`rep.app.Repchain`，[单机单节点](#分布式单节点)为`rep.app.Repchain_Single`

   ```
   mainClass in (Compile, packageBin) := Some("rep.app.Repchain")
   或
   mainClass in (Compile, packageBin) := Some("rep.app.Repchain_Single")
   ```

2. 编译打包

   ```shell
   # sbt shell中使用如下命令：
   clean compile assembly
   
   # 终端使用如下命令：
   sbt
   sbt > clean compile assembly
   ```
   

环境要求：

1. jdk11+ （建议使用[zuluJdk](https://www.azul.com/downloads/zulu-community/?package=jdk)）



## 模拟多节点

> 单机模拟多节点，同一个`Jvm`上启动模拟5个节点，启动主类为`rep.app.Repchain`

* 使用IDE，配置Application为`rep.app.Repchain`并启动

* 使用jar包，打包时候Manifest中为`rep.app.Repchain`，使用

  ```bash
  java -jar RepChain.jar
  ```

  来启动，或者使用

  ```bash
  java -cp RepChain.jar rep.app.Repchain
  ```

  

## 分布式单节点

> 单机单节点，同一个`Jvm`上启动1个节点，启动主类为`rep.app.Repchain_Single`

- 可跨域、跨云进行去中心化部署，也可在专线、政务云上进行部署，单台服务器作为一个节点，节点服务器被各个机构各自维护

- 使用IDE，配置Application为`rep.app.Repchain`，**同时**在`Program arguments`中填写节点名`"1234567890123456.node1"`并启动

- 使用jar包，

  ```shell
  # 打包时候Manifest中为`rep.app.Repchain`，使用
  java -jar RepChain.jar "1234567890123456.node1"
  # 或直接指定主类
  java -cp RepChain.jar rep.app.Repchain_Single "1234567890123456.node1"
  ```

- ip与port的配置参考[相关配置](v1.0.0/config.md)



## 节点密钥替换

默认为5节点，每个节点有一个密钥对，**实际**在生产环境部署RepChain需要更换开源仓库中的密钥，使用[KeyStoreExplorer](https://keystore-explorer.org/)（或者使用[代码](https://gitee.com/BTAJL/ToolCollection/blob/master/src/main/java/repchain/genjks/GenerateJks.java)）即可生成密钥对，导出证书

1. 为每个节点替换密钥，假设有5个节点，则使用**kse**生成5个包含密钥对的`jks`并导出证书，更换`/jks/`目录下的密钥与证书
2. 使用`kse`将各个节点的证书导入到`mytrustStore.jks`中
3. 使用`GenesisBuilder.scala`工具重新生成创世块



## 新增节点

假设现在想增加组网节点，则需要生成新的`jks`，并放到`/jks/`目录下，并且将证书导入到`mytrustStore.jks`中，所有节点的`mystrustStore.jks`都需要更新

* 增加的是共识节点

  > 需要在配置文件中，将该节点配置到共识列表中

* 增加的是同步节点

需要注意的地方：

1. 如果现在没有历史区块数据，则重构`genesis.json`，将新节点的账户与证书注册

2. 如果现在有历史数据，则无需重构`genesis.json`，可根据实际情况调用**账户证书管理合约**来注册

   > 如果不涉及到账户问题，该步骤非必须，因为默认`RepChain`会从`mytrustStore.jks`中加载所有节点的证书，可以保证新增节点所提交的签名交易被接收

