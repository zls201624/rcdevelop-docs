# 配置文件

> 1. 主要在`conf/system.conf`中进行配置，如比较了解[akka](https://akka.io/docs/)，可直接在`src/main/resources/application.conf`中进行配置
> 2. 日志配置文件在`conf/logback.xml`



## 通信

> RepChain中节点通信方式为基于`TLS`（目前已支持`TLS1.3`）的**P2P**通信

!> 注意区别[v1.0.0的配置](v1.0.0/config.md#通信)

- 非**NAT**与**docker**环境下使用如下配置（hostname为主机ip，port为节点之间P2P通信端口）

  ```json
  remote {
  	#artery模式下的配置
      artery {
        	canonical.hostname = "192.168.2.35"  	# external (logical) hostname
        	canonical.port = 22526				    # external (logical) port
      }
  }
  ```

- NAT与docker环境下使用如下配置

  ```json
  remote {
  	#artery模式下的配置
      artery {
        	canonical.hostname = "139.10.1.3"  	# external (logical) hostname
        	canonical.port = 22526				# external (logical) port
       	bind.hostname = “192.168.2.35”		# internal (logical) hostname
          bind.port = 25526					# internal (logical) port
      }
  }
  ```



## 集群

> 需要配置组网的种子节点（<b>akka://RepChain@<font color=#ff000>canonical.hostname:canonical.port</font></b>）

!> 注意区别[v1.0.0的配置](v1.0.0/config.md#集群)

```json
cluster {
    #种子节点的配置
    seed-nodes = ["akka://Repchain@127.0.0.1:22522", "akka://Repchain@127.0.0.1:22523", "akka://Repchain@127.0.0.1:22526"]
}
```

注： 第一个启动的组网节点，<u> **必须自己在种子节点列表中** </u>

* 单机模拟多节点

  > 如上配置即可，使用默认配置，在rep.app.Rep,chain.scala中默认指定了通信端口，如果想使用随机通信端口，将canonical.port设置为0，并使用启动类rep.app.RepChain_Multi

* 分布式单机单节点

  > `seed-nodes`中需要配置种子节点的外网地址
  >
  > ```shell
  > cluster {
  >     #种子节点的配置
  >     seed-nodes = ["akka://Repchain@139.10.1.3:22522", "akka://Repchain@139.10.1.4:22523"]
  > }
  > ```



## 系统

- 服务端口

  > RepChain的RestFul服务端口

- 交易生产方式

  > 自动交易可用来测试环境搭建是否成功

- 背书比例

  > 收集到背书的比例，收集到足够的背书即可出块

- 区块配置

  > 可配置每个区块包含的交易数的多少

```json
system {
    #交易生产方式
	trans_create_type = 0 		# 0,手动;1,自动
	httpServicePort = 8081		# http服务的端口号，默认为8081
    number_of_endorsement = 2	# 收集到背书的比例
    block {
    	#块内交易的最大数量
    	trans_num_limit = 80
    	#块内交易标准最小数量
    	trans_num_min = 1
    	#交易数量不足，重试次数
    	retry_time = 10
    	#区块的最大长度，不能大于传输的消息的最大长度，单位是字节
    	block_length = 240000
  	}
}
```



## 共识节点

> 网络中参与共识与背书，负责执行交易、打包区块

可通过`conf/system.conf`中`vote#vote_node_list`进行配置：

```json
vote {
    #最低投票人数量
    vote_node_min = 4
    #参与共识的节点别名
    vote_node_list = ["121000005l35120456.node1", "12110107bi45jh675g.node2","122000002n00123567.node3",                        
                      "921000005k36123789.node4", "921000006e0012v696.node5"]
}
```

1. `vote_node_list`配置了5个节点作为共识节点，如果组网节点还有`***.node6`、`***.node7`，则`node6`与`node7`作为同步节点，只同步区块，不参与共识背书，只有配置在列表中的节点才参与共识；
2. 如果目前在网**稳定节点**数目小于`vote_node_min`，则系统不能正常工作
3. RepChain网络中各个节点的配置中该项都一致



## 存储

> RepChain中需要存储数据的主要有两部分：1. 区块数据；2. worldState

可通过`conf/system.conf`中的`storage#dbpath`(worldState部分)与`#blockpath`(区块数据)进行配置

```json
storage {
	dbpath = "/repchaindata/data/leveldbdata"
    blockpath = "/repchaindata/data/blockdata"
    filemax = 200000000#单位为字节
}
```



## 共识算法

> 目前RepChain支持三种共识算法，分别为：**CFRD**、**RAFT**、**PBFT**

- CFRD

  > 自研无协商随机抽签算法，并已申请专利

- RAFT
- PBFT

可供过`conf/system.conf`中的`consensus#type`配置项进行配置

```json
consensus {
	#共识类型，目前只支持一种
	type = "CFRD"//内置三种共识协议，CFRD、RAFT、PBFT
	blocknumberofraft = 100
}
```

注：`blocknumberofraft`配置项只在`type = "RAFT"`时候起作用

## 日志

> 使用logback.xml进行配置

vm参数中配置 `-Dlogback.configurationFile=conf/logback.xml` 即可启动