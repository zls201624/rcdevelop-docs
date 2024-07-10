# [RCJS](https://gitee.com/BTAJL/RCJS)

## 介绍
1. RCJS(RepChain Client library for JavaScript)，是基于JavaScript实现的用于与[RepChain](https://gitee.com/BTAJL/repchain)区块链网络进行交互的工具包，可作为开发RepChain DApp(Decentralized Application)的SDK。

2. RCJS对调用RepChain的相应Websocket API, Restful API等进行了封装。利用RCJS可较为方便地使DApp与RepChain进行交互。

3. RCJS兼容浏览器环境以及NodeJS环境，可根据需要在不同环境使用。

  > Note: 某些加密算法名称在不同环境可能有差异，需注意

4. RCJS为开发者提供了以下功能：
   - 获取RepChain区块链概要信息：获取区块链网络的区块数量、交易数量及最新区块哈希等信息；
   - 获取RepChain区块数据：支持订阅RepChain出块事件获取区块数据(二进制格式(protobuf序列化))，以及通过Restful API主动获取区块数据(json格式或二进制格式(protobuf序列化))；
   - 获取RepChain交易数据：通过Restful API获取交易数据(json格式或二进制格式(protobuf序列化))
   - 构造RepChain签名交易：提供RepChain交易数据的构造及交易签名功能;
   - 提交RepChain签名交易：向RepChain区块链网络提交已构造的签名交易数据；
   - 加密工具：(基于[NodeJS Crypto]() 以及 [jsrsasign]()实现)
     - 生成密码学哈希值
     - 生成非对称密钥对：支持RSA与EC算法
     - 导出/导入密钥：支持生成/解析PEM格式的公钥或私钥
     - 对数据进行签名
     - 验证签名
     - 生成X509证书
     - 生成自签名X509证书
     - 导出/导入X509证书：支持生成/解析PEM格式证书信息
     - 验证证书的签名
     - 生成/解析CSR信息: 支持生成以及反解析PEM格式的CSR信息
     
     

## 使用说明
### 安装 
```bash
yarn add rclink
```
或 
```bash
npm i --save rclink
```
或者在package.json文件中直接使用git地址, 如:
```json
"rclink": "https://gitee.com/BTAJL/RCJS.git#support/2.1.x" 
```
### API
API文档可参考[这里](https://btajl.gitee.io/rcjs)
### 使用示例
可参考测试用例代码:
- 交易构建： https://gitee.com/BTAJL/RCJS/blob/master/test/transaction.test.js
- 事件订阅：https://gitee.com/BTAJL/RCJS/blob/master/test/events.test.js
- 数据查询及交易提交: https://gitee.com/BTAJL/RCJS/blob/master/test/rest.test.js
- 加密工具：https://gitee.com/BTAJL/RCJS/blob/thyland/test/crypto.test.js



## 开发
### 安装依赖
1. 安装node.js和yarn
2. `yarn install`



### 运行测试
1. 建立RepChain开发环境
2. 运行测试用例：
    - `yarn test:node` node环境下运行测试用例
    
    - `yarn test:browser` browser环境下运行测试用例
    
    - `yarn test` 在node与browser环境下分别运行测试用例
    
    > Note: 在test/testEnvConfig.js中可修改待测试的目标代码: testEnv="dev"(表示测试位于src/下的开发代码)；testEnv="production"(表示测试位于lib/下的生产代码)