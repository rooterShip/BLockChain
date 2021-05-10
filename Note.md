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
  进行BTC转账时，转账人用私钥对交易进行**签名**，交易信息发布在Block Chain上，其他人用公钥对消息进行**验证**----RSA,ESA,ECDSA(经典数字签名算法)
  

    