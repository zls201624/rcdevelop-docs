# 合约

>  部署在区块链上的可执行代码

## 生命周期

RepChain对合约的有着完整生命周期管理

- 部署

  [用户](v1.0.0/term.md#user)编写好合约，通过构造并提交部署类型的签名交易，将合约部署到RepChain上

- 调用

  合约部署后，RepChain的[用户](v1.0.0/term.md#user)即可调用该合约

- 升级

  合约的部署者，可以对合约进行升级操作，如已部署的Contract_A版本好为1，可升级合约Contract_A版本为2 ... 3 ... N

- 禁用/启用

  合约的部署者，可对其已部署的合约做禁用/启用操作



## 合约开发

* [开发规范](https://gitee.com/BTAJL/RepChain_Linter)

  * 合约类继承自`IContract`，并且不能继承其他接口
  * 不能使用随机数
  * 不能使用`LocalDateTime`
  * case class中不要使用Any类型
  * 在import类的时候，要使用全路径

  示例代码：

  ```scala
  package rep.sc.tpl
  
  import org.json4s._
  import org.json4s.jackson.JsonMethods._
  import rep.app.conf.SystemProfile
  import rep.protos.peer.ChaincodeId
  import rep.utils.IdTool
  import rep.sc.scalax.IContract
  import rep.sc.scalax.ContractContext
  import rep.sc.scalax.ContractException
  import rep.protos.peer.ActionResult
  
  final case class Transfer(from:String, to:String, amount:Int)
  
  /**
   * 资产管理合约
   */
  class ContractAssetsTPL extends IContract{
  
    // 需要跨合约读账户
    val chaincodeName = SystemProfile.getAccountChaincodeName
    val chaincodeVersion = SystemProfile.getAccountChaincodeVersion
    val signerPrefix = "signer_"
  
    implicit val formats = DefaultFormats
  
      def init(ctx: ContractContext){
        println(s"tid: $ctx.t.id")
      }
      
      def set(ctx: ContractContext, data:Map[String,Int]) :ActionResult=	 {
        println(s"set data:$data")
        for((k,v)<-data){
          ctx.api.setVal(signerPrefix+k, v)
        }
        null
      }
      
      def transfer(ctx: ContractContext, data:Transfer) :ActionResult={
        if(!data.from.equals(ctx.t.getSignature.getCertId.creditCode))
          throw ContractException("只允许从本人账户转出")      
        val signerKey =  data.to
        // 跨合约读账户，该处并未反序列化
        if(ctx.api.getStateEx(chaincodeName,signerPrefix+data.to)==null)
          throw ContractException("目标账户不存在")
        val sfrom:Any =  ctx.api.getVal(signerPrefix+data.from)
        var dfrom =sfrom.asInstanceOf[Int]
        if(dfrom < data.amount)
          throw ContractException("余额不足")
        ctx.api.setVal(signerPrefix+data.from,dfrom - data.amount)
        var dto = ctx.api.getVal(signerPrefix+data.to).toString.toInt
        ctx.api.setVal(signerPrefix+data.to,dto + data.amount)
         null
      }
  
      def put_proof(ctx: ContractContext, data:Map[String,Any]): ActionResult={
      //先检查该hash是否已经存在,如果已存在,抛异常
      for((k,v)<-data){
        var pv0:Any = ctx.api.getVal(signerPrefix+k)
        if(pv0 != null)
          throw ContractException(s"$k 已存在，当前值为 $pv0")
        ctx.api.setVal(signerPrefix+k,v)
        print("putProof:"+k+":"+v)
      }
        null
    }
  
    /**
       * 根据action,找到对应的method，并将传入的json字符串parse为method需要的传入参数
       */
      def onAction(ctx: ContractContext,action:String, sdata:String ):ActionResult={
        val json = parse(sdata)      
        action match {
          case "transfer" => 
            transfer(ctx,json.extract[Transfer])
          case "set" => 
            set(ctx, json.extract[Map[String,Int]])
          case "putProof" =>
            put_proof(ctx, json.extract[Map[String,Any]])
        }
      }
  }
  
  ```

* 注意

  ?> 1. 合约方法中，如果有错直接`throw  ContractException("message")`，如果没有错误，则返回`null` <br>2. `ctx.api.setVal`底层使用leveldb数据库，使用该方法可保存数据到worldState中<br>3. `ctx.api.getVal`可从worldState中读取数据<br>4. `ctx.api.getStateEx`可跨合约读（不可以跨合约写，合约中写操作只能针对本合约）<br>5. `ctx.api.getLogger`可获取logger，合约日志可记录在该Logger中

* 测试

  > [参考](v1.0.0/test.md#合约测试)