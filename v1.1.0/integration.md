# 与应用集成

!> 以私钥掌握在用户或者应用服务端手中来区分，如用户人手一个私钥，还是机构统一管理用户的私钥

* 直接模式

  > 用户直接使用自己的私钥构造签名交易

  ![image-20210115094831215](../_images/direct-mode.png ':size=40%')

* 代理模式

  > 应用服务代理用户提交交易，私钥掌握在应用服务提供商手中
  
  ![image-20210115095044459](../_images/indirect-mode.png ':size=40%')

## 直接模式解决方案

* [BaseApp](https://gitee.com/linkel/bar)

  > 用户使用密钥直接向RepChain提交签名交易，不经过代理服务器，私钥掌握在用户手中

  

## 代理模式解决方案

* 整体与业务集成的架构如下：

![proxy-txr](../_images/proxy-txr.png ':size=70%')

* [交易代理提交服务](https://gitee.com/linkel/repchain-tx-proxy-service)
  
  > 代理构造并提交签名交易，用户也使用交易id来查询交易的上链状态
  
* [区块推送服务](https://gitee.com/linkel/repchain-block-courier)

  > 订阅者订阅RepChain区块数据，推送服务推送给订阅者