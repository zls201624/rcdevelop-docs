# 常见问答

1. 默认Jks密码<br>node1...node5为“123”，super_admin的为“super_admin”，trustStore的为“changeme”

2. Jks中默认生成的密钥对使用的什么椭圆曲线算法<br>RepChain1.1.0版本改用secp256r1，不再使用secp256k1（RepChain1.0版本用的是secp256k1）

3. 运行RepChain提示磁盘不足<br>可能有两种原因：1、system.conf中配置的存储路径所在磁盘空间不足；2、用户不具有存储路径下的写权限

4. windows下报leveldbJni的错误<br>可能是缺c++运行时环境

5. 使用jar包运行RepChain时，提示chainCode不存在<br>system.conf中将合约运行模式改为1

6. 提交调用合约的交易没有入块<br>可能是合约不存在，或者合约名不对，或者版本不对，也可能是数据格式不对，可以查看相关日志，比如SandBox_Logger的日志，或者是Business_Logger的日志

7. 可以通过api检索存储的数据或者leveldb中的数据么？<br>当前版本不可以，建议通过同步区块数据来做分析处理

8. 提交的交易，接口返回了txid，却没有出块（提交的交易可能是调用合约的交易、也可能是部署合约类型的交易，从以下几个方面考虑）

   * 交易txid是否有重复（即相同的txid交易已经存在于链上）
   * 调用的合约是否存在<br>1. 是否先前已经被注册<br>2. 是否合约名或者版本号写错了

   * 调用的方法是否存在<br>1. 核对一下合约里的方法名
   * 调用的方法的参数输入是否正确<br>1. 核对一下方法可接受的参数

   * 是否私钥对应的证书未注册，所以验签通过不了

9. 导入工程后，build失败（大多是idea的自带sbt插件的问题）<br>1. 重启ide，多刷新几次，或者清缓存<br>2. 再或者在命令行中使用sbt-compile之后，再导入idea<br>3. idea中改用自己环境的sbt<br>4. sbt版本不必选择最新，如1.3.x即可