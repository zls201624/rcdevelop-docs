# 构建创世块

?> RepChain区块主要由高度、交易、交易执行合约后worldState的值、区块的Hash、前块的Hash、worldState的Hash、以及背书信息组成。创世块指的是区块链的第一个区块，既然是区块，那么它的内容就和其他区块内容几乎一样，但是有一点小区别，就是作为第一个区块，没有前块Hash的信息。

## 创世块

RepChain组网成功且网内稳定节点数大于最低投票人，会触发创世块的生成，系统通过读取`json/genesis.json`来构建创世块。

!> 创世块内的签名交易（包括部署合约、调用合约类型的签名交易）是会被执行的

## 构建方式

从上一小节我们知道了系统会读取`json/genesis.json`，因此我们通过构造`genesis.json`来<strong><font color=#FF0000>定制</font></strong>创世块内容，默认RepChain提供了`GenesisBuilder`等构造该`json`文件的工具。

构建逻辑如下：

1. 部署账户证书管理合约（<font color=#FF0000>必选</font>）
2. 注册初始组网节点的账户与证书的交易（<font color=#FF0000>建议</font>）
3. 部署业务合约（<font color=#FF0000>可选</font>）
4. 在 **3** 的基础上，构造调用业务合约的交易（<font color=#FF0000>可选</font>）

包含在源码中的3个小工具：

* GenesisBuilder

  使用super_admin的密钥构建部署账户证书管理合约的交易（部署合约类型交易）、注册节点账户和证书的交易（调用合约类型交易）；使用node1的秘钥构建部署ContractAssetsTPL的交易（部署合约类型交易，属于可选的业务合约）；使用super_admin的密钥构建为节点账户赋金额的交易（调用合约类型交易）；

  !> 其中程序中所用到的<u>**节点名**</u>以及密钥等都是项目中默认自带的，因此实际使用中一般使用GenesisBuilderMulti或者自己根据实际需要仿照编程。

* GenesisBuilderMulti

  与GenesisBuilder相似，唯一不同的是程序根据`jks`目录下的各个节点的`jks`文件名来注册账户以及对应的证书，如果RepChain使用的是非项目自带的jks与证书，可通过改造该程序来构造genesis.json

* GenesisBuilderTool

  在GenesisBuilder的基础上改造而成，可封装在RepChain.jar中，并使用命令来生成genesis.json，假设工程打包为RepChain.jar之后

  java -Dgenesis=./json/genesis.json -cp RepChain.jar rep.utils.GenesisBuilderTool  args，具体命令请看源码，用户可根据实际情况来修改

!> 构造genesis.json的原理比较简单，主要就是用户根据自己的需要构造好交易即可，构造签名交易的密钥对只能选用super_admin或者节点密钥