# BTC(crypto currency)
## -BTC-密码学原理<br>
### 哈希
- Cryptographic hash function-密码哈希函数<br>
  - collision resistance<br>
    x≠y,H(x)=H(y) (意指当H(x)=H(y)时,求解y时只能依靠蛮力即brute-force)<br>
    用途:对消息m求哈希H(m),当m被篡改后，H(m)随着改变，且无法找到(理想情况下)消息m'使得H(m')=H(m)<br>
  - Hiding<br>
    满足x->H(x),得不到H(x)->x,即不可逆性(前提时输入空间要足够大，遍历无法求解，且要求输入分布均匀)<br>
  - digital commitment/digital equivalent of a sealed envelope(数字承诺，提供无法被篡改的第三方凭证)
  - puzzle friendly(哈希值是不可预测的，无法确定范围)<br>
    **BTC挖矿原理 H(block header)<=target**<br>
    **其中block header有很多域，其中一个域可以设计nonce<br>
    遍历nonce，找到正确的nonce即可发布新的Block**<br>
- proof of work(工作量证明)<br>
- SHA-256(Secure Hash Aligorithm)  
  - 为BTC中常用的哈希函数<br>
### 数字签名
- 去中心化<br>
   去中心化开账户后会得到<*public key,private key>*(公私钥对，且公钥公开)--公钥相当于账户名，私钥相当于账户密码
- asymmetric encryption algorithm--非对称加密体制
  公钥加密，私钥解密
- 数字签名机制
  进行BTC转账时，转账人用私钥对交易进行**签名**，交易信息发布在Block Chain上，其他人用公钥对消息进行**验证**----RSA,ESA,ECDSA(经典数字签名算法)<br>
## -BTC-数据结构
- Hash pointers(保证无环)   
  区别于普通指针---哈希指针可以存储哈希值(可以验证所指向的结构体有没有被篡改)<br>
### 区块链  
- *Block Chain is a linked list using hash pointers.*  
  ![区块链模型](Image/BlockChain.png)<br>
  下一个区块的哈希值的产生必须是对本区块所有的内容取哈希(包括指向前一区块的哈希指针)
- tamper-evident log---篡改证明记录
    若对区块链中任一区块进行修改，则该区块以后的区块哈希值全部改变（包含最后一块），即记录下最后一块的哈希值，可以监测区块是否被篡改。<br>
### Merkle tree
![Merkletree模型](Image/MerkleTree.png)
- 区块的组成
  - block header(存储Merkle tree根哈希值，并无具体交易列表信息)
  - block body(存储交易列表)<br>
- BTC节点分类  
  - 全节点(保存整个区块的信息)
  - 轻节点(只保存block header)
- Merkle Proof  
  轻节点向Merkle tree寻求验证，交易是否被写进区块链中，即在Data Blocks中找到该交易信息，不断向根节点取哈希，验证root hash是否正确  
  ![MerkleProof](Image/Merkle%20Proof.png)<br>
  轻节点向全节点发起申请，全节点提供所需哈希(红色部分)，不断进行验证得出Merkle Root。
- Proof of membership(证明Merkle Tree中包含某个交易信息)<br>
  时间复杂度 O(log(n))
## -BTC-协议
- double spending attack--双重支付攻击<br>
    类比实际生活中发行数字货币，防止双重攻击可以在央行系统(非去中心化中的管理员)登记币与币所有者(对应)。在比特币系统中，利用区块链数据结构，将由所有用户共同扮演管理员的身份。<br>
    ![交易协议](Image/交易协议.png)<br>
    比特币系统中每个交易都包含输入输出两部分，输入部分说明币的来源，输出部分说明收款人公钥的哈希。<br>
    此时区块链中有两种哈希指针<br>
    - 一种是连接各个区块之间的哈希指针(见上文)
    - 一种是指向前面某个交易的，是为了说明币的来源。<br>
- 比特币系统不支持查询个体账户的公钥(地址)的功能。<br>
  在发生交易时，举例A->B(5)，A要得到B的公钥(转账地址)的同时，B和全体用户也必须知道A的公钥(用A的公钥去验证消息，确认交易合法性)。A的公钥由A自己向全链进行广播。A在这里进行广播的公钥必须和币的来源(即接收币的时候所持有的公钥地址)相一致。(防止有恶意用户假装A进行转账)。
- 区块的组成<br>
    上文提到每一个区块由两部分组成，即Block header,Block body
  - Block header 内含信息：reversion(区块链版本信息)，hash of previous block header(哈希指针),Merkle root hash(Merkle tree的根哈希值),target(人为设计的目标阈值),nonce(随机数)。
  - Block body 内含信息：trasacation list(交易列表)
- 节点的分类<br>
  - full node(fully validating node)(保存区块链的所有信息)
  - light node(只保存block header信息)
- distributed consensus(分布式共识)
  - CAP Theorm
    - Consistency
    - Availability
    - Partition tolerance<br>
 表示在任意分布式系统中这三个性质中最多智能满足两个。
- Consensus in BitCoin
  - 所有节点在发布新区块前都会在本地预装出区块，把合法交易(节点主观上)放置进区块链中，直到成功找到nonce值的节点可以发布区块--获得记账权(包含预装的区块中交易信息)，然后所有节点验证区块信息(block header,block body)，如若无误，选择接受这个区块。
  - longest valid chain<br>
    比特币系统中的节点接受的应是最长合法链
  - froking attack(分叉攻击)
  - special situation<br>
    ![最长合法链](Image/最长合法链.png)<br>
    当两个节点在几乎相同的时间发布出两个区块，则这种状况将会维持到最长合法链的诞生，即有一条链更早发布下一个区块，较短的那条链被舍弃(orphan block)。
  - block reward(出块奖励)  
    coinbase transaction(铸币交易)--币的来源
- 共识机制分类
  - POW<br>
    POW(Proof of Work)--工作量证明
    - 数学算法简单透明，并且完全去中心化
    -POW的收益取决于所拥有的算力(占比系统总算力)
  - POS<br>
    POS(Proof of Stake)--权益证明或股权证明<br>
    权益证明模式就是一个根据所持有货币的量和时间，来发利息的一个模式<br>
    - 核心名词：币龄(每个币每天产生1币龄)
    - 当持有一定币龄发现区块时，币龄会被清空为0，当被清空某个固定的币龄时就会从区块中获得一定的奖励(利息)--持币有利息。
  - POA<br>
    POA(Proof of Authority)--授权证明<br>
    授权证明机制就是由**一组授权节点**来负责新区块的产生和区块验证,按授权节点的地址升序排序后,按顺序轮流获得记账权。 人人平等
    
## -BTC-实现
### transaction-based ledger
- UTXO (*unspent transaction output*)--未使用交易的输出<br>
  比特币的交易由交易输入和交易输出组成，每一笔交易都要花费一笔输入，产生一笔输出，而其所产生的输出就是**未花费过的交易输出**即UTXO。
  > 现实世界中没有比特币，只有UTXO。<br>

  注：<br>
  - 比特币钱包中的账户余额，实际上是钱包通过扫描区块链并聚合所有属于该用户的UTXO计算得来的。<br>
  - 除了 coinbase交易之外，所有的资金来源都必须来自前面某一个或者几个交易的 UTXO
  - 任何一笔交易的交易输入总量必须等于交易输出总量，等式两边必须配平。(total inputs=total outputs+transaction fee)
- transaction fee
  当交易写进区块链中时，将会抽取transaction fee给发布区块的节点。
- Example
  - Block example(source:blockchain.info)<br>
  ![BlockExample](Image/BlockExample.png)
  其中：<br>
    - Number of Transaction 区块中包含的交易信息
    - Output Total 总输出比特币
    - Transaction Fees 区块中所有的交易费
    - Height 区块的序号
    - Timestamp 时间戳
    - Difficulty 挖矿难度(保证出块时间在十分钟左右)
    - Nonce 随机值
    - block reward 出块奖励
    - Hash 该区块的块头哈希值
    - Previous Block 前一个区块的块头哈希值
    - Merkle Root Merkle tree的根哈希值 

    ![BlockHeader](Image/Blockheader.png)

    其中：<br>
    - unit32_t nNonce 表示nonce是32位的整数<br>
  注：32位nonce已经不能满足难度需求，需要再对block header中的某个域进行调整。

    ![BlockHeader](Image/Blockheader1.png)

    其中：<br>
    - version 当前使用比特币版本号
    - previous block header hash 前一个区块的哈希
    - merkel root hash--merkel tree的根哈希值
    - time 这个区块产生的时间
    - nBits 目标阈值编码后版本
    - nonce 随机值<br>
  观察block header中的各个域，想要对除nonce值以外的值进行修改，符合要求的只有merkle root hash.

    ![Coinbase](Image/Coinbase.png)
  Coinbase Transaction作为铸币交易，无输入，内含coinbase域，可以通过更改coinbase域写入的值来控制difficulty。
  ![Transaction](Image/Coinbase1.png)
  实际挖矿是两层循环遍历，第一层先找到extra nonce(coinbase域),第二层遍历找到nonce值才能发布新区块。<br>
  - transaction example 
  ![Transaction3](Image/Trasaction3.png)
  框内表示这个交易的输入，图中output实际指花掉的是之前哪个交易的output。
  ![Transaction1](Image/Trasaction1.png)
  框内表示UTXO
  ![Transaction2](Image/Trasaction2.png)
  交易的输入和输出都是由脚本来完成的，验证交易的合法性就是将上一笔交易的输出和这笔交易的输入配对进行验证。
### 挖矿概率
- Bernoulli trial: a random experiment with binary outcom
  - 每一次尝试nonce为伯努利试验，且两种结果的概率相差较大。
- Bernoulli process: a sequence of indepent Bernoulli trails
  - 性质：无记忆性--memoryless
- exponential distribution
  - 出块时间满足指数分布(整个系统的出块时间，且满足平均出块时间维持在10分钟)
  - 矿工的出块时间取决于矿工的算力在总算力中的占比
- geometric series<br>
  比特币系统中，每挖出21万个区块，出块奖励减半(初始出块奖励为50个比特币)，比特币系统中比特币总量为:<br>
  $210000\times50+210000\times25+210000\times12.5+\ldots$<br>
  $=210000\times50\times(1+\frac{1}{2}+\frac{1}{4}+\ldots)$<br>
  $=21000000$<br>
  即，比特币总量为2100万<br>
- Confirmation机制--等到该区块后的六区块发布出来后，该区块得到彻底确认。
## -BTC-网络
- The BitCoin Network
  application layer:BitCoin Block Chain<br>
  ***
  network layer:P2P Overlay Network
- principle:<br>simple,robust,but no efficient
### -BTC-挖矿难度
## H(block header)<=target
- $difficulty=\frac{target\ of\ difficulty\_1}{target}$<br>
  - *target of difficulty\_1*:挖矿难度为1(难度值最小)时候对应着的target
  - 如果不调整挖矿难度，出块时间会越来越短，系统中会出现越来越多的**分叉**，从而危害系统的共识性和安全(随着分叉条数越来越多，系统中总算力被分散，容易被恶意节点执行恶意操作)
- 比特币系统中每隔2016个区块要调整一次挖矿难度。<br>
  $\frac{2016\times10(min)}{60\times24}$=14(day)
  - 10(min)：比特币系统中平均出块时间为10min
  - 2016：每隔2016个区块要调整一次挖矿难度<br>
  即每隔14天调整一次挖矿难度<br>
  - $target=target\times\frac{actual time}{expected time}$
    - actual time:time spent mining the last 2016 blocks(上调下调的最大限制是4倍)
    - expected time:$10\times2016$(平均出块时间乘区块个数)
## -BTC-挖矿
- 全节点特点与功能
  - 一直在线
  - 在本地硬盘上维护完整的区块链信息
  - 在内存里维护UTXO集合，以便快速检验交易的正确性
  - 监听比特币网络上的交易信息，验证每个交易一的合法性
  - 决定哪些交易会被打包到区块里
  - 监听别的矿工挖出来的区块，验证其合法性
  - 挖矿
    - 决定沿着哪条链挖下去
    - 当出现等长的分叉的时候，选择哪一个分叉 
- 轻节点
  - 不是一直在线
  - 不用保存整个区块链，只要保存每个区块的块头
  - 不用保存全部交易，只保存与自己相关的交易
  - 无法检验大多数交易的合法性，智能检验与自己相关的那些交易的合法性
  - 无法监测网上发布的区块的正确性
  - 可以验证挖矿的难度(哈希值只用到块头内的信息)
  - 只能检测哪个是最长链，不知道哪个是最长合法链
- 挖矿设备
  - 第一代：CPU
  - 第二代：GPU
    - 特点--可以进行大量的并行运算(深度学习)<br>
    - 不足--不会使用到浮点数运算，造成使用上的性能浪费
  - 第三代：ASIC(芯片):Application Specific Integrated Circuit--特殊应用集成电路
    - 特点：具有较强的专业性，只能对特定的虚拟货币进行运算(mining puzzle)
- 矿池
  - pool manager--矿主
  - share(almost valid block)<br>当矿池中真正具有出块奖励时，share作为各个矿工的工作量证明。share：假设降低出块难度（即人为设置假区块），各个矿工挖到假区块并提交为一次share，share仅仅作为证明矿工的工作量。
## 比特币脚本
### 交易结构：<br>
```json
"result":{
  "txid": "921a...dd24",//交易序列号
  "hash": "921a...dd24",
  "version": 1,//比特币协议的版本号
  "size": 226,//交易大小
  "locktime": 0,//locktime为执行时间，此处(0)为立即执行
  "vin": [...],//输入
  "vout": [...],//输出
  "blockhash": "0000000000000000002c510d...5c0b",//这个交易所在的区块的哈希值
  "confirmations": 23,//23个确认信息
  "time": 1530846727,//交易产生时间
  "blocktime": 1530846727//区块产生时间(从一个时间点到现在过了多长时间)
}
```
### 输入部分
```json
"vin": [{
  "txid": "c0cb...c57b",
  "vout": 0,//前两行交代输入币的来源，txid表示上一个交易(产生币的交易)的序列号;vout表示上一个交易的第几个输出
  "scriptSig":{           //输入脚本，这里的scriptSig之后将以input script指代
    "asm": "3045...c57b",
    "hex": "4830...0018"
  },
}],
```
### 输出部分
```json
"vout":[{
  "value": 0.22684000,//转账金额(单位比特币)
  "n": 0,//(输出序号)
  "scriptPubKey": {//输出脚本，这里的scriptPubKey之后将以output script指代
    "asm": "DUP HASH160 628e...d743 EQUALVERIFY CHECKSIG",//可以理解为脚本代码段
    "hex": "76a9...88ac",
    "reqSigs": 1,//需要的签名数
    "type": "pubkeyhash",//输出脚本的格式
    "addresses": ["19z8LJ...QvSr"]//收款人地址(公钥)
  }
},{
  "value": 0.53756644,
  "n": 1,
  "scriptPubKey": {
    "asm": "DUP HASH160 da7d...2cd2 EQUALVERIFY CHECKSIG",
    "hex": "76a9...88ac",
    "reqSigs": 1,
    "type": "pubkeyhash",
    "addresses": ["1LvGTp...NYhX"]
  }
}],
```
- Example<br>
  ![比特币脚本](Image/比特币脚本.png)  
  联系代码段，B->C的交易输入(币)来自A->B,即将B->C的输入脚本放在前面，A->B的脚本放在后面拼接而成(安全起见，这两段代码分别执行)，若最后结果返回True则交易可以执行。
### 输入输出脚本的不同形式
- P2PK(Pay to Public Key)<br>
  ```
  input script:
  PUSHDATA(Sig)//输入为用私钥给出签名
  output script:
  PUSHDATA(PubKey)//为给出付款人的公钥
  CHECKSIG//用付款人的公钥去验证签名
  ```
- P2PKH(Pay to Public Key Hash)
  ```
  input script:
  PUSHDATA(Sig)//私钥签名
  PUSHDATA(PubKey)//付款人公钥
  output script:
  DUP//复制栈顶元素
  HASH160//弹出栈顶元素并取哈希值，并将哈希值压入栈
  PUSHDATA(PubKeyHash)//将输出脚本提供的公钥哈希压入栈
  EQUALVERIFY//弹出栈顶两个元素，比较两个元素是否相等
  CHECKSIG//弹出栈顶两个元素判断是否为TRUE
  ```
### 多重签名
- 最早的多重签名
```
input script:
False//bug导致，加一条多余信息放栈底
PUSHDATA(Sig_1)
PUSHDATA(Sig_2)
...
PUSHDATA(Sig_M)
outputScript:
M             //M个签名
PUSHDATA(pubket_1)
PUSHDATA(pubkey_2)
...
PUSHDATA(pubkey_N)
N             //N个公钥
CHECKMULTISIG
```
## -BTC-分叉(fork)
- state fork<br>
由于对区块链意见不一产生的分叉<br>
eg.forking attck(人为故意产生)--deliberate fork
- protocd fork<br>
由于比特币协议变化产生的变化<br>
  - hard fork--永久性分叉(新协议产生的new features，未升级软件的旧节点不认可这些features)
    eg:新协议中增大block size limit(1M->4M)
    ![hard_fork](Image/hard_fork.png)
    当系统中占绝大部分算力的节点更新协议后，算力集中在上面一条链(4M)，并不断延长，由于旧节点不承认新区块，即认为上面一条链不是合法链，所以下面一条链由未更新系统的节点继续延长，新节点承认两种区块(<=4M)，但由于新节点会选择最长合法链，因此，会永久形成上下两条链，形成硬分叉
  - soft fork--非永久性分叉
    eg:新协议中减小block size limit(1M->0.5M)[即新节点不承认旧节点，旧节点承认新节点]
    ![soft_fork](Image/soft_fork.png)
    当系统中占绝大部分算力的节点更新协议后，算力集中在上面一条链(0.5M)，由于旧节点承认新区块(<=1M)，所以会沿着上面一条链继续延长，因此下面一条链将被视为非法链，即分叉不会永远存在--软分叉
  - 向前兼容的协议更新导致软分叉，向后兼容的协议更新导致硬分叉(前提都是系统占半数的算力更新协议)
## -BTC-匿名性(匿名物理世界与区块链账户的关联性)
- 影响匿名性的因素
  - 一个用户不同账号互相关联起来
  - 与实体世界发生联系(资金的转入转出)
- 维持匿名性
  - application layer - coin mixing
  - network layer
- 零知识证明<br>
  concept:指易反复（证明者）向另一方（验证者）证明一个陈述是正确的，而无需透露除该陈述是正确的外的任何信息<br>
  数学基础：同态隐藏
  - 如果x,y不同，那么它们的加密函数E(x)和E(y)也不相同
  - 给定E(x)的值，很难反推出x的值
  - 给定E(x)和E(y)的值，我们可以很容易地计算出某些关于与x,y的加密函数值
    - 同态加法：通过E(x)和E(y)计算出E(x+y)的值
    - 同态乘法：通过E(x)和E(y)计算出E(xy)的值
    - 扩展到多项式
- 零币(专门为匿名性设置的币)<br>
  concept:无需证明币的来源，只需要通过零知识证明该币为系统中合法基础币

    