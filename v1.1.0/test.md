# 测试



## RepChain测试

* 性能测试

  > 使用[ToolCollection](https://gitee.com/BTAJL/ToolCollection/blob/master/src/main/java/repchain/performance/PerformanceTestV3.java)进行性能测试



## 合约测试

> 使用IDE进行断点调试或单元测试

### 1. 断点调试

* xml方式

  > 通过`swagger-ui`+`xml`的方式，使用swagger-ui上的`/transaction/postTran`的接口

  1. 首先打开localhost:8081/swagger/index.html，在`postTran`接口处部署合约，使用下面的xml进行部署

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     
     <!--该用例主要是部署同样的合约，并验证返回值（存在重复的合约Id）-->
     <CSpec>
         <stype>1</stype>
         <chaincodename>PassportProofTPL</chaincodename>
         <chaincodeversion>1</chaincodeversion>
         <iptFunc></iptFunc>
         <iptArgs></iptArgs>
         <timeout>0</timeout>
         <legal_prose></legal_prose>
         <code>
         	<![CDATA[
     package rep.sc.tpl
     
     import java.nio.charset.StandardCharsets
     import java.security.MessageDigest
     
     import org.json4s.jackson.JsonMethods.parse
     import org.json4s.{DefaultFormats, MappingException}
     import rep.crypto.BytesHex
     import rep.protos.peer.ActionResult
     import rep.sc.scalax.{ContractContext, ContractException, IContract}
     
     /**
       * 物流上链数据
       *
       * @param apiIdentifier 接口标识
       * @param data          要存证的数据
       */
     case class PassportData(apiIdentifier: String, data: String)
     
     /**
       * @author zyf
       */
     class PassportProofTPL extends IContract {
     
       implicit val formats: DefaultFormats.type = DefaultFormats
     
       val sha256Digest: MessageDigest = MessageDigest.getInstance("SHA-256")
     
       override def init(ctx: ContractContext): Unit = {
         println(s"tid: $ctx.t.id")
       }
     
       /**
         * 存证
         *
         * @param ctx
         * @param data 护照链上传的数据
         * @return
         */
       def proof(ctx: ContractContext, passportData: PassportData): ActionResult = {
         val prefix = passportData.apiIdentifier
         val infix = "_"
         val suffix = BytesHex.bytes2hex(sha256Digest.digest(passportData.data.getBytes(StandardCharsets.UTF_8)))
         val key = prefix + infix + suffix
         ctx.api.setVal(key, passportData.data)
         null
       }
     
       override def onAction(ctx: ContractContext, action: String, sdata: String): ActionResult = {
     
         val jsonData = parse(sdata)
     
         action match {
     
           case "proof" =>
             try {
               val data = jsonData.extract[PassportData]
               proof(ctx, data)
             } catch {
               case mapEx: MappingException => throw ContractException(mapEx.getMessage)
             }
     
           case _ => throw ContractException(s"合约中没有对应的方法${action}")
         }
       }
     }
     		
     		]]>
         </code>
         <ctype>2</ctype>
         <state>true</state>
     </CSpec>
     ```

  2.  在src/main/scala下会生成`SC_PassportProofTPL_1.scala`文件

  3. 重启RepChain

  4. 在`SC_PassportProofTPL_1.scala`文件的相应源码处打上断点

  5. 调用合约来测试

   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    
    <!--该用例主要是测试正确转账（并且未超出余额），并验证返回值-->
    
    <CSpec>
        <stype>2</stype>
        <chaincodename>PassportProofTPL</chaincodename>
        <chaincodeversion>1</chaincodeversion>
        <iptFunc>proof</iptFunc>
        <iptArgs>
            {
              "apiIdentifier" : "353453554353453453",
              "data" : "{\"foreignCode\":\"123\", \"recordJson\":\"1111111\"}"
            }
        </iptArgs>
        <timeout>0</timeout>
        <legal_prose></legal_prose>
        <code></code>
        <ctype>2</ctype>
        <state>true</state>
    </CSpec>
   ```
  
  
  
* sdk方式

  > RCJava-core等sdk来构建交易，提交到RepChain

  1. 使用sdk部署合约
  2. 在src/main/scala下会生成`SC_PassportProofTPL_1.scala`文件
  3. 重启RepChain

  4. 在`SC_PassportProofTPL_1.scala`文件的相应源码处打上断点

  5. 使用sdk调用合约来测试

* 命令行工具

  1. 使用命令行工具部署合约
  2. 在src/main/scala下会生成`SC_PassportProofTPL_1.scala`文件
  3. 重启RepChain

  4. 在`SC_PassportProofTPL_1.scala`文件的相应源码处打上断点

  5. 使用命令行工具调用合约来测试

### 2. 合约单元测试

- 使用`scalaTest`与`akkaTestkit`

