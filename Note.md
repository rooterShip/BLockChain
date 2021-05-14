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
   去中心化开账户后会得到*<*public key,private key>*(公私钥对，且公钥公开)--公钥相当于账户名，私钥相当于账户密码
- asymmetric encryption algorithm--非对称加密体制
  公钥加密，私钥解密
- 数字签名机制
  进行BTC转账时，转账人用私钥对交易进行**签名**，交易信息发布在Block Chain上，其他人用公钥对消息进行**验证**----RSA,ESA,ECDSA(经典数字签名算法)<br>
## -BTC-数据结构
- Hash pointersI(保证无环)   
  区别于普通指针---哈希指针可以存储哈希值(可以验证所指向的结构体有没有被篡改<br>
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
    - 一种是指向前面某个交易的，是为了说明币的 来源。<br>
  - 比特币系统不支持查询个体账户的公钥(地址)的 功能。<br>
  在发生交易时，举例A->B(5)，A要得到B的公钥(转账地址)的同时，B和全体用户也必须知道A的公钥(用A的公钥去验证消息，确认交易合法性)。A的公钥由A自己向全链进行广播。A在这里进行广播的公钥必须和币的来源(即接收币的时候所持有的公钥地址)相一致。(防止有恶意用户假装A进行转账)。
  - 区块的组成
    上文提到每一个区块由两部分组成，即Block header,Block body
    - Block header 内含信息：reversion(区块链版本信息)，hash of previous block header(哈希指针),Merkle root hash(Merkle tree的根哈希值),target(人为设计的目标阈值),nonce(见上文挖矿原理)。
  
  
   


  

    