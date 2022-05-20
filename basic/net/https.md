# https

# 一. 简介

## 1. 概述

定义：常称为HTTP over TLS、HTTP over SSL或HTTP Secure，是一种通过计算机网络进行安全通信的传输协议。HTTPS经由HTTP进行通信，但利用SSL/TLS来加密数据包。

通俗理解：加密的http，https = HTTP + TLS





![协议分层_new2](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/%E5%8D%8F%E8%AE%AE%E5%88%86%E5%B1%82_new2.png)



## 2. 历史

1995: SSL 2.0, 由Netscape提出，这个版本由于设计缺陷，并不安全，很快被发现有严重漏洞，已经废弃。
1996: SSL 3.0. 写成RFC，开始流行。目前已经不安全，必须禁用。
1999: TLS 1.0. 互联网标准化组织ISOC接替NetScape公司，发布了SSL的升级版TLS 1.0版.
2006: TLS 1.1. 作为RFC 4346 发布。主要修复了CBC模式相关的如BEAST攻击等漏洞
2008: TLS 1.2. 作为RFC 5246 发布。增进安全性。目前的主流版本。
2018: TLS 1.3. 作为RFC 8446 发布。增进安全性，增强性能。

 

## 3. 实现

实现有很多：OpenSSL、LibreSSL、BoringSSL。但最通用的实现是Openssl，也是事实上的标准。





# 二. 密码学

## 1. 知识图谱

![密码学知识图谱](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/%E5%AF%86%E7%A0%81%E5%AD%A6%E7%9F%A5%E8%AF%86%E5%9B%BE%E8%B0%B1.png)

## 2. 非对称加密

（这部分内容是我的笔记，主要是我用来复习，应该不会讲）

### 1. RSA

#### 1. 原理

1. 互质关系

```
因数：假如a*b=c（a、b、c都是整数)，那么我们称a和b就是c的因数
质数：大于1的自然数中，除了1和它本身外不再有其它因数的数
互质关系：如果两个正整数，除了1以外，没有其他公因数，我们就称这两个数是互质关系

推论：
	1. 任意两个质数构成互质关系，比如13和61
	2. 一个数是质数，另一个数只要不是前者的倍数，两者就构成互质关系，比如3和10
	3. 1和任意一个自然数都是互质关系
```

2. 欧拉函数

```
问题：任意给定正整数n，请问在小于等于n的正整数之中，有多少个与n构成互质关系？（比如，在1到8之中，有多少个数与8构成互质关系？）
计算这个值的方法就叫做欧拉函数

1. 如果n=1，则 φ(1) = 1 。因为1与任何数（包括自身）都构成互质关系。
2. 如果n是质数，则 φ(n)=n-1 。因为质数与小于它的每一个数，都构成互质关系。比如5与1、2、3、4都构成互质关系。
3. 如果n是质数的某一个次方，即 n = p^k (p为质数，k为大于等于1的整数)，则φ(p^k)=p^k - p^(k-1)
	比如 φ(8) = φ(2^3) =2^3 - 2^2 = 8 -4 = 4。
4. 如果n可以分解成两个互质的整数之积n=p1*p2，
则φ(n) = φ(p1p2) 
根据中国剩余定理， φ(p1p2) = φ(p1)φ(p2)
根据互质关系的推论2、3， φ(p1)φ(p2)=(p-1)(p-2)
那么φ(n) = φ(p1p2) = φ(p1)φ(p2)=(p-1)(p-2)
5. 因为任意一个大于1的正整数，都可以写成一系列质数的积。
φ(n) = p1^k1*p2^k2...pr^kr 
= φ(p1^k1)*φ(p2^k2)...φ(pr^kr)
= p1^k1*p2^k2...pr^kr *(1-1/p1)*(1-1/p2)...(1-1/pr)
= n*(1-1/p1)*(1-1/p2)...(1-1/pr)
```

3. 欧拉定理

如果两个正整数a和n互质，则n的欧拉函数φ(n)满足下面的等式

![image-20220221153835049](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/image-20220221153835049.png)

比如3和7互质，而7的欧拉函数φ(7)等于6，所以3的6次方（729）减去1，可以被7整除（728/7=104）。

4. 模反元素

如果两个正整数a和n互质，那么一定可以找到整数b，使得 ab-1 被n整除，或者说ab被n除的余数是1。
![image-20220221154232880](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/image-20220221154232880.png)
这时，b叫做a的模反元素

比如，3和11互质，那么3的模反元素就是4，因为 (3 × 4)-1 可以被11整除



#### 2. 应用

```
生成公私钥：
1. 选择2个不相等的质数p, q。比如p=61, q=53
2. 计算pq的乘积 n = p×q = 61×53 = 3223
3. 计算n的欧拉函数φ(n)，φ(n) = (p-1)(q-1) = 60×52 = 3120
4. 随机选择一个整数e，条件是1< e < φ(n)，且e与φ(n) 互质。比如选择e = 17，（实际应用中，常常选择65537。）
5. 计算e对于φ(n)的模反元素d。ed ≡ 1 (mod φ(n))，中间经过一系列计算，最后算出d=2753
6. 至此，计算完成，最后算出n=3233,e=17,d=2753
公钥=(n,e)=(3233,17)
私钥=(n,d)=(3233,2753)
实际应用中e通常为65537（十进制），十六进制为10001

加密
假设信息为m=65，那么加密就是算出下式的c
m^e ≡ c (mod n)
65^17 ≡ c (mod 3233),c=2790

解密
c^d ≡ m (mod n),证明略
27902753 ≡ m(mod 3233),m=65

签名
m^d ≡ s (mod n)
校验
s^e ≡ m (mod n)
```

#### 3. 小结

m:message 信息
e:encryption加密秘钥（私钥）
d:decryption解密秘钥（公钥）
s:signature签名信息
c:ciphertext密文

|      |                 |
| ---- | --------------- |
| 加密 | m^e ≡ c (mod n) |
| 解密 | c^d ≡ m (mod n) |
| 签名 | m^d ≡ s (mod n) |
| 校验 | s^e ≡ m (mod n) |



## 3. 密钥交换算法

（这部分内容是我的笔记，主要是我用来复习，应该不会讲）

### 1. DH

```
假设 client 和 server 需要协商密钥，p=2579，则本原根 a = 2。
Client 选择随机数 Kc = 123 做为自己的私钥，计算 Yc = a^Kc  mod p = 2^123 mod 2579 = 2400，把 Yc 作为公钥发送给 server。
Server 选择随机数 Ks = 293 作为自己的私钥，计算 Ys = a^Ks  mod p = 2^293 mod 2579 = 968，把 Ys 作为公钥发送给 client。
Client 计算：premaster secrect =  Ys^Kc mod (p) = 968^123  mod(2579) = 434
Server 计算premaster secrect = Yc^Ks mod(p) =2400^293 mod(2579) =434
为什么两者相等？
(Ys)^Kc mod p = (a^Ks mod p)^Kc mod p = (a^Ks)^Kc mod p
(Yc)^Ks mod p = (a^Kc mod p)^Ks mod p = (a^Kc)^Ks mod p
此处用到了取模运算规则
```

### 2. ECDHE

```
ECDHE 算法实现要复杂很多，主要分为两部分：diffie-hellman 算法（简称为 DH）及 ECC（椭圆曲线算术）。
定义一个满足椭圆方程的有限域，即挑选 p, a, b 满足如下方程：y^2 mod p = (x^3+ax +b) mod p
 挑选基点 G = (x, y)，G 的阶为 n。n 为满足 nG = 0 的最小正整数。
Client 选择私钥 Kc (0 <Kc<n )，产生公钥 Yc =Kc *G
server 选择私钥 Ks 并产生公钥 Ys =Ks*G
client 计算premaster secret= Kc*Ys，
server 计算premaster secret= Ks*Yc，
这两者的结果是一样的，为什么两者相等？
Kc*Ys=Kc*(Ks*G)=Kc*Ks*G
Ks*Yc=Ks*(Kc*G)=Ks*Kc*G
```

## 4. 数字签名

### 1. 概述

定义：又称**公钥数字签名**，是一种功能类似写在纸上的普通签名、但是使用了公钥加密领域的技术，以用于鉴别数字信息的方法。

用途：使接收签名的用户能够相信，这份数据是已知的发送者创建的，并且在网络传输中没有被篡改。也就是实现了认证 + 完整性。

### 2. 原理

![Digital_Signature_diagram_zh-CN](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/Digital_Signature_diagram_zh-CN.svg)



# 三. PKI

## 1. 知识图谱

![PKI_new](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/PKI_new.png)

## 2. 概述

### 1. 定义

定义：又称**公开密钥基础架构**、**公钥基础建设**、**公钥基础设施**、**公开密码匙基础建设**或**公钥基础架构**，是一组由硬件、软件、参与者、管理政策与流程组成的基础架构，其目的在于创造、管理、分配、使用、存储以及撤销数字证书。

通俗理解：以证书为核心的网络认证体系。

### 2. 流程

![PKI_structure](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/PKI_structure.png)

```
1. 服务端向CA申请证书，CA签发证书，并将证书放到证书仓库。
2. 服务端将证书部署到服务器。
3. 用户与服务端进行TLS握手时，请求证书，服务端发送证书。
4. 用户收到证书后进行校验。
5. 如果服务端私钥泄漏，向CA申请吊销证书，CA吊销。
```



## 3. 证书

### 1. 证书内容

```
openssl x509 -noout -text -in baidu.pem

Certificate:
    Data:
    		# 证书版本
        Version: 3 (0x2)
        # 序列号
        Serial Number:
            72:39:dc:c9:be:b5:c9:cd:79:54:15:f9
    Signature Algorithm: sha256WithRSAEncryption
    		# 证书签发者
        Issuer: C=BE, O=GlobalSign nv-sa, CN=GlobalSign Organization Validation CA - SHA256 - G2
        # 证书有效期
        Validity
            Not Before: Jul  1 01:16:03 2021 GMT
            Not After : Aug  2 01:16:03 2022 GMT
        # 证书的Subject信息
        Subject: C=CN, ST=beijing, L=beijing, OU=service operation department, O=Beijing Baidu Netcom Science Technology Co., Ltd, CN=baidu.com
        # 证书包含的公钥
        Subject Public Key Info:
        		# 公钥算法，RSA
            Public Key Algorithm: rsaEncryption
            		# 公钥长度
                Public-Key: (2048 bit)
                # 公钥n信息
                Modulus:
                    00:9b:51:c1:9b:46:50:20:79:d4:d3:99:21:be:02:
                    57:86:45:0a:66:7d:8a:e2:22:b5:13:82:9a:bf:1f:
                    83:19:aa:fb:cf:73:04:2d:07:4c:43:b6:5b:85:58:
                    37:ee:16:9e:a0:8f:a7:6f:05:83:a4:c8:5b:17:73:
                    cd:80:d6:93:2e:38:87:b0:6a:dd:97:4b:37:78:b5:
                    61:d3:3a:d3:92:3b:47:d1:0d:14:05:d7:65:8a:56:
                    e9:3c:4c:4d:3c:55:aa:e1:67:9d:db:66:39:01:f2:
                    a9:ba:8f:cb:52:30:b3:98:a7:3e:68:40:ef:d9:6a:
                    13:ac:f8:d7:4c:43:58:ab:21:63:f2:e8:06:80:d2:
                    f9:94:7b:ab:81:51:56:75:c3:2c:b1:5a:0e:eb:a3:
                    28:fe:ae:fe:d6:32:eb:d3:4f:8e:09:43:bf:81:d7:
                    18:c5:42:c4:b4:f6:a8:1f:9c:3c:77:af:85:c7:67:
                    9b:05:c3:bf:19:2e:6c:87:f7:49:e1:76:dd:97:92:
                    95:06:62:78:91:55:b6:59:e2:39:ed:e4:76:a6:cf:
                    16:1a:80:d0:17:17:75:43:86:fb:a4:e7:f4:30:68:
                    06:ce:eb:7a:85:04:2c:24:2f:c5:66:ad:b6:1f:da:
                    d0:ee:06:47:0e:58:d0:a8:49:b5:e2:c4:2f:7d:a8:
                    f5:61
                # 公钥e信息
                Exponent: 65537 (0x10001)
        # 证书扩展
        X509v3 extensions:
        		# 证书使用扩展
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            # CA信息
            Authority Information Access:
                CA Issuers - URI:http://secure.globalsign.com/cacert/gsorganizationvalsha2g2r1.crt
                OCSP - URI:http://ocsp2.globalsign.com/gsorganizationvalsha2g2
						# 证书策略
            X509v3 Certificate Policies:
                Policy: 1.3.6.1.4.1.4146.1.20
                  CPS: https://www.globalsign.com/repository/
                Policy: 2.23.140.1.2.2
						# 基本约束
            X509v3 Basic Constraints:
                CA:FALSE
            # CRL地址
            X509v3 CRL Distribution Points:

                Full Name:
                  URI:http://crl.globalsign.com/gs/gsorganizationvalsha2g2.crl
						# 可选名称
            X509v3 Subject Alternative Name:
                DNS:baidu.com, DNS:baifubao.com, DNS:www.baidu.cn, DNS:www.baidu.com.cn, DNS:mct.y.nuomi.com, DNS:apollo.auto, DNS:dwz.cn, DNS:*.baidu.com, DNS:*.baifubao.com, DNS:*.baidustatic.com, DNS:*.bdstatic.com, DNS:*.bdimg.com, DNS:*.hao123.com, DNS:*.nuomi.com, DNS:*.chuanke.com, DNS:*.trustgo.com, DNS:*.bce.baidu.com, DNS:*.eyun.baidu.com, DNS:*.map.baidu.com, DNS:*.mbd.baidu.com, DNS:*.fanyi.baidu.com, DNS:*.baidubce.com, DNS:*.mipcdn.com, DNS:*.news.baidu.com, DNS:*.baidupcs.com, DNS:*.aipage.com, DNS:*.aipage.cn, DNS:*.bcehost.com, DNS:*.safe.baidu.com, DNS:*.im.baidu.com, DNS:*.baiducontent.com, DNS:*.dlnel.com, DNS:*.dlnel.org, DNS:*.dueros.baidu.com, DNS:*.su.baidu.com, DNS:*.91.com, DNS:*.hao123.baidu.com, DNS:*.apollo.auto, DNS:*.xueshu.baidu.com, DNS:*.bj.baidubce.com, DNS:*.gz.baidubce.com, DNS:*.smartapps.cn, DNS:*.bdtjrcv.com, DNS:*.hao222.com, DNS:*.haokan.com, DNS:*.pae.baidu.com, DNS:*.vd.bdstatic.com, DNS:*.cloud.baidu.com, DNS:click.hm.baidu.com, DNS:log.hm.baidu.com, DNS:cm.pos.baidu.com, DNS:wn.pos.baidu.com, DNS:update.pan.baidu.com
            # 扩展密钥用法
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            # CA密钥标识符
            X509v3 Authority Key Identifier:
                keyid:96:DE:61:F1:BD:1C:16:29:53:1C:C0:CC:7D:3B:83:00:40:E6:1A:7C
						# 使用者密钥标识符
            X509v3 Subject Key Identifier:
                34:92:9A:2F:0C:71:62:BC:3D:DB:23:6D:6D:3E:B3:D1:1D:11:9D:ED
            1.3.6.1.4.1.11129.2.4.2:
                ...i.g.u."EE.YU$V.?./..m..#&c..K.]..\n......z_.&9.....F0D.  .y.e........9..|C.LK...!f.N..$U. <`s/=]...o.-...0/.....hqF...8....v.)y...99!.Vs.c.w..W}.`
..M]&\%].....z_.&*.....G0E.!..]...........9..aZ...2b&
...F.... g.B...|.n43....x.....X.W..sy.../.v.U.....6.J...W<S...8xp%../..........z_.&K.....G0E. y*s|...y.^....$........j....jlC~.!...2...O....X<.nq../...i..f....".
		# 签名算法和签名值
    Signature Algorithm: sha256WithRSAEncryption
         39:8a:00:49:92:48:16:58:de:3e:9c:ce:83:39:1b:b1:ac:9a:
         95:f9:56:ff:7c:2d:82:d3:a8:36:5b:e6:cf:7d:fd:4a:98:72:
         48:f2:b7:f6:52:d4:0b:09:2c:a2:5c:33:47:e2:9a:9b:3e:97:
         bd:d8:ba:00:09:c9:ae:1e:b3:bc:de:e8:1e:e1:de:d9:05:f8:
         a9:b0:3d:ed:b9:7a:b2:a9:3c:93:4e:07:8c:f0:5e:cc:8b:f3:
         75:33:6e:55:82:e5:99:42:9f:9c:81:54:fb:ad:ce:28:0c:38:
         42:60:95:68:e1:4d:5f:83:2d:a4:32:76:d8:51:1c:1d:66:b7:
         9c:ad:1f:19:f9:40:e4:7c:74:4d:db:2a:bb:ea:f3:24:4d:e9:
         38:77:21:52:3c:fd:1e:81:1e:90:00:84:ae:c8:66:fc:e3:81:
         78:91:d0:43:78:99:2a:a4:85:31:3c:9f:6b:ef:48:9e:1e:39:
         4d:51:07:b7:53:4d:ff:e2:13:ab:e3:ca:6d:7c:21:f6:e2:fa:
         22:73:f4:65:71:75:77:da:08:8e:f7:2d:5b:e6:01:c9:f7:96:
         0c:5f:2d:a8:d7:3e:4c:5e:c2:92:78:e4:1b:4e:9b:28:36:9f:
         18:77:f2:bb:f5:6a:64:71:78:0f:ea:d5:68:7f:11:57:b4:ff:
         0f:b0:e4:73
```

```
version
表示证书的版本号，目前有3个版本（v1, v2, v3）

serialNumber
每个证书都有唯一的编号，注意这个编号是CA内部唯一，但是不同CA可能重复

issuer
证书的签发者，主要由由国家（C）、组织（O）、子组织名（CN）组成

Subject Alternative Name
简称SAN，表示证书包含的域名。
注意这个有2种写法：www.baidu.com、*.baidu.com。
第一个表示具体的域名，第二个表示泛域名。*类似bash的通配符，但是不包含.
也就是说*.baidu.com匹配a.baidu.com、b.baidu.com，不匹配a.b.baidu.com和baidu.com。
```



### 2. 证书链

![PKI_certificate_chain](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/PKI_certificate_chain.png)

```
部署在服务器的通常是证书链。
结构如下，要保证顺序。
	服务器证书
	中间证书
```



## 4. 用户

### 1. 请求证书

```
TLS握手中请求服务端的证书
```

### 2. 校验证书

```
TLS握手中收到证书后，对证书进行校验。
校验的内容：
1. 证书链完整
通过签名逐级验证，直到根证书。根证书通常由用户集成，比如浏览器自带一批根证书。
2. 服务器证书的校验
	1. 域名：用户访问的域名与证书的SAN扩展是否匹配。
	2. 日期：证书是否在有效期内。
	3. 公钥用途：证书的公钥是否能够数字签名、加密。
3. 中间证书校验
	1. 日期：证书是否在有效期内。
	2. 公钥用途：证书的公钥是否能够数字签名、密钥证书签名、CRL签名。
	3. 基础约束：证书是否能够签发证书。
4. 吊销校验
证书是否被吊销
```



## 5. 服务端

### 1. 申请证书

```
通常的流程是：
1. 首先生成非对称密钥对。
2. 用密钥对生成CSR文件，这个文件和最终的证书相比，只缺少CA的签名。
3. 将CSR发给CA，CA用私钥签名后，会将签名后的证书、中间证书发给申请者。

注意事项：
1. 最重要的是选择声誉好、靠谱的CA。如果CA签发了错误的证书，影响很大的话，可能会被浏览器从根证书列表中移除。比如赛门铁克前些年发生过类似的事情。
```

### 2. 部署证书

```
将证书链和私钥部署到服务器即可。
注意：
1. 必须部署证书链，不能仅部署服务端证书，如果没有证书链、证书链顺序不对、证书链缺失，用户（一般是浏览器）会校验失败。
2. 证书链可以不包含根证书，因为根证书一般都集成在用户（一般是浏览器）手中。
3. 包含根证书一般也不会有问题，只是会增大网络传输的数据。
```

## 6. CA

### 1. 签发证书

### 2. 吊销证书

```
吊销证书的方式：CRL、OCSP
CRL： 是由CA定期更新的一个列表，包含了所有已被作废的证书，浏览器可以定期下载这个列表用于验证证书合法性
OCSP：是一个在线查询接口，浏览器可以实时查询单个证书的合法性
OCSP-Stapling：是指服务端在证书链中包含颁发机构对证书的 OCSP 查询结果，从而让浏览器跳过自己去验证的过程
```

![OCSP_Stapling](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/OCSP_Stapling.png)

# 四. TLS协议

（基于TLS1.2，RFC5246）

## 1. 知识图谱

![TLS_protocol](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol.png)



![TLS_cipher_suite](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_cipher_suite.png)

## 2. 概述

### 1. 协议目标

TLS的设计目标是构建一个安全传输层（Transport Layer Security ），在基于连接的传输层（如tcp）之上提供：

1. 密码学安全
   (1). 保密，使用对称加密
   (2). 完整性，使用MAC校验机制，一旦被篡改，通信双方会立刻发现
   (3). 认证，使用证书
2. 互操作性：根据公开的rfc，任何符合rfc的软件实现都可以互操作，不受限于任何专利技术
3. 可扩展性，很方便的支持新的加密算法
4. 相对效率，加密操作通常是CPU密集型的，尤其是非对称加密。所以TLS提供session方案来提高效率。



## 3. 握手协议

#### 1. 流程图

```
      Client                                               Server

      ClientHello                  -------->
                                                      ServerHello
                                                     Certificate*
                                               ServerKeyExchange*
                                              CertificateRequest*
                                   <--------      ServerHelloDone
      Certificate*
      ClientKeyExchange
      CertificateVerify*
      [ChangeCipherSpec]
      Finished                     -------->
                                               [ChangeCipherSpec]
                                   <--------             Finished
      Application Data             <------->     Application Data
```

主要步骤

1. 协商密码套件（Cipher suite）
2. 通过证书认证身份
3. 双方生成PremasterSecret（也可以说是MasterSecret、或者理解为对称加密的密钥）

#### 2. 详情

**1. Client hello**

![TLS_protocol_client_hello](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_client_hello.png)

```
client_version
表示客户端支持的TLS/SSL协议版本，该值表示支持的最高版本号，如果该值是TLS v1.2，表示客户端支持的版本号可以是TLS v1.2以下所有的版本。

random
客户端的随机数，该值非常有用，使用PRF算法计算主密钥和密钥块会用到，校验完整的握手消息也会用到，生成预备主密钥也会用到。

session_id
和会话恢复有关。一个客户端和服务器完成一次握手，服务器会发送一个会话ID给客户端，下次连接的时候客户端会发送该会话ID给服务器，如果服务器端校验存在该会话ID，就会恢复上一次连接，从而减少握手过程，提升效率。对于一次全新的连接来说，客户端传递的session_id为空，是一次完整的握手过程。

cipher_suites
客户端发送其支持的密码套件列表，可以发送多个密码套件，排在第一个的优先选择。

extensions：
  Server_name：客户端要访问的hostname，（对SNI的支持）
  Renegotiation_info：客户端支持安全重协商
  SessionTicket TLS：客户端支持SessionTicket
  Signature_algorithms：客户端支持的签名算法，用于验证证书的签名。
  Status_request：客户端支持OCSP stapling（空）
  Application Layer Protocol Negotiation(ALPN)：客户端支持的http协议列表
```



**2. Server hello** 

![TLS_protocol_server_hello](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_server_hello.png)

```
server_version
服务器根据客户端传递的版本号选择一个双方都支持的版本，不同的协议版本，客户端和服务器端处理逻辑是不同的。

random
和客户端一样，服务器端也会生成一个随机数，作用和客户端发送的随机数类似。

session_id
如果客户端传输的session_id不为空，则服务器会从缓存中寻找是否存在同样的session_id，如果找到表示可以进行会话恢复，可以复用上一个连接。如果没有找到，则进行一个完整的握手过程，传递一个新的session_id。

cipher_suite
服务器根据客户端传递的密码套件列表，选择一个双方都支持的密码套件进行处理。

compression_method
根据客户端传递的compression_method，决定使用的压缩算法，一般情况下不启用压缩算法。

扩展：
Renegotiation_info：服务器支持安全重协商
SessionTicket TLS：客户端支持SessionTicket
Status_request：服务器支持OCSP stapling（空）
Application Layer Protocol Negotiation(ALPN)：服务器选择的http协议
```



**3. certificate**

![TLS_protocol_certificate](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_certificate.png)

```
服务器发送的证书链
通过证书进行认证
```



**4. Server key exchange**

![TLS_protocol_server_key_exchange](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_server_key_exchange.png)

```
发送一些协商信息来完成premaster secret的交换
RSA秘钥交换时：此信息不发送
ECDHE秘钥交换时：ServerDiffieHellmanPublic：Ys
```



**5. Server hello done**

![TLS_protocol_server_hello_done](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_server_hello_done.png)

```
这条消息表明服务器已经发送完了秘钥交换的协商信息
```



**6. Client key exchange**

![TLS_protocol_client_key_exchange_rsa](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_client_key_exchange_rsa.png)



![TLS_protocol_client_key_exchange_ecdhe](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_client_key_exchange_ecdhe.png)

```
RSA秘钥交换时：加密的premaster secret
ECDHE秘钥交换时：clientDiffieHellmanPublic：Yc
```



**7. New session ticket**

![TLS_protocol_new_session_ticket](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_new_session_ticket.png)

```
将session ticket的数据发送给客户端
```



**8. Change cipher spec protocol**

![TLS_protocol_change_cipher_spec](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_change_cipher_spec.png)

```
通知接收方，该消息后将使用刚才协商好的Cipher Suite和key来加密数据
```



#### 3. 生成密钥

**流程**

```
1. 通过server key exchange、client key exchange，计算出premaster_secret。（RSA和DH的生成方式不同）
   1. RSA：客户端生成premaster secret，公钥加密后，传递给服务器
   2. ECDHE：
      client端计算premaster secret= Kc * Ys
      server端计算premaster secret= Ks * Yc

2. 计算master_secret
master_secret = PRF(pre_master_secret, "master secret", ClientHello.random + ServerHello.random)
PRF(secret, label, seed) = P_<hash>(secret, label + seed)
P_hash(secret, seed) = HMAC_hash(secret, A(1) + seed) +
                             HMAC_hash(secret, A(2) + seed) +
                             HMAC_hash(secret, A(3) + seed) + ...
+ indicates concatenation.
A() is defined as:
	A(0) = seed
  A(i) = HMAC_hash(secret, A(i-1))

3.计算key_block
key_block = PRF(SecurityParameters.master_secret,
                      "key expansion",
                      SecurityParameters.server_random +
                      SecurityParameters.client_random);

4. 计算密钥
从key_block分割出下面6个密钥
client_write_MAC_key[SecurityParameters.mac_key_length]
server_write_MAC_key[SecurityParameters.mac_key_length]
client_write_key[SecurityParameters.enc_key_length]
server_write_key[SecurityParameters.enc_key_length]
client_write_IV[SecurityParameters.fixed_iv_length]
server_write_IV[SecurityParameters.fixed_iv_length]

client_write_MAC_key：客户端发送数据时，客户端用此秘钥来计算MAC，服务器用此秘钥来验证
server_write_MAC_key：服务器发送数据时，服务器用此秘钥来计算MAC，客户端用此秘钥来验证
client_write_key：客户端发送数据时，客户端用此秘钥加密数据，服务器用此秘钥解密
server_write_key：服务器发送数据时，服务器用此秘钥加密数据，客户端用此秘钥解密
client_write_IV：分组加密算法使用的初始化向量
server_write_IV：分组加密算法使用的初始化向量
```



#### 4. 密码套件

一个CipherSuite（密码套件）是4个算法的组合：
1个authentication (认证)算法
1个encryption(加密)算法
1个message authentication code (消息认证码 简称MAC)算法
1 个key exchange(密钥交换)算法

例

ECDHE-RSA-AES128-SHA256 
使用 ECDHE做密钥交换
使用RSA做认证
使用AES-128做加密算法
使用SHA256做MAC算法

```
openssl ciphers -V | column -t
0xC0,0x30  -  ECDHE-RSA-AES256-GCM-SHA384    TLSv1.2  Kx=ECDH  Au=RSA     Enc=AESGCM(256)        Mac=AEAD
0xC0,0x2C  -  ECDHE-ECDSA-AES256-GCM-SHA384  TLSv1.2  Kx=ECDH  Au=ECDSA   Enc=AESGCM(256)        Mac=AEAD
0xC0,0x28  -  ECDHE-RSA-AES256-SHA384        TLSv1.2  Kx=ECDH  Au=RSA     Enc=AES(256)           Mac=SHA384
0xC0,0x24  -  ECDHE-ECDSA-AES256-SHA384      TLSv1.2  Kx=ECDH  Au=ECDSA   Enc=AES(256)           Mac=SHA384
0xC0,0x14  -  ECDHE-RSA-AES256-SHA           SSLv3    Kx=ECDH  Au=RSA     Enc=AES(256)           Mac=SHA1
0xC0,0x0A  -  ECDHE-ECDSA-AES256-SHA         SSLv3    Kx=ECDH  Au=ECDSA   Enc=AES(256)           Mac=SHA1
0x00,0x9F  -  DHE-RSA-AES256-GCM-SHA384      TLSv1.2  Kx=DH    Au=RSA     Enc=AESGCM(256)        Mac=AEAD
0x00,0x6B  -  DHE-RSA-AES256-SHA256          TLSv1.2  Kx=DH    Au=RSA     Enc=AES(256)           Mac=SHA256
0x00,0x39  -  DHE-RSA-AES256-SHA             SSLv3    Kx=DH    Au=RSA     Enc=AES(256)           Mac=SHA1
0xCC,0xA9  -  ECDHE-ECDSA-CHACHA20-POLY1305  TLSv1.2  Kx=ECDH  Au=ECDSA   Enc=ChaCha20-Poly1305  Mac=AEAD
0xCC,0xA8  -  ECDHE-RSA-CHACHA20-POLY1305    TLSv1.2  Kx=ECDH  Au=RSA     Enc=ChaCha20-Poly1305  Mac=AEAD
0xCC,0xAA  -  DHE-RSA-CHACHA20-POLY1305      TLSv1.2  Kx=DH    Au=RSA     Enc=ChaCha20-Poly1305  Mac=AEAD
0xFF,0x85  -  GOST2012256-GOST89-GOST89      SSLv3    Kx=GOST  Au=GOST01  Enc=GOST-28178-89-CNT  Mac=GOST89IMIT
0x00,0xC4  -  DHE-RSA-CAMELLIA256-SHA256     TLSv1.2  Kx=DH    Au=RSA     Enc=Camellia(256)      Mac=SHA256
0x00,0x88  -  DHE-RSA-CAMELLIA256-SHA        SSLv3    Kx=DH    Au=RSA     Enc=Camellia(256)      Mac=SHA1
0x00,0x81  -  GOST2001-GOST89-GOST89         SSLv3    Kx=GOST  Au=GOST01  Enc=GOST-28178-89-CNT  Mac=GOST89IMIT
0x00,0x9D  -  AES256-GCM-SHA384              TLSv1.2  Kx=RSA   Au=RSA     Enc=AESGCM(256)        Mac=AEAD
0x00,0x3D  -  AES256-SHA256                  TLSv1.2  Kx=RSA   Au=RSA     Enc=AES(256)           Mac=SHA256
0x00,0x35  -  AES256-SHA                     SSLv3    Kx=RSA   Au=RSA     Enc=AES(256)           Mac=SHA1
0x00,0xC0  -  CAMELLIA256-SHA256             TLSv1.2  Kx=RSA   Au=RSA     Enc=Camellia(256)      Mac=SHA256
0x00,0x84  -  CAMELLIA256-SHA                SSLv3    Kx=RSA   Au=RSA     Enc=Camellia(256)      Mac=SHA1
0xC0,0x2F  -  ECDHE-RSA-AES128-GCM-SHA256    TLSv1.2  Kx=ECDH  Au=RSA     Enc=AESGCM(128)        Mac=AEAD
0xC0,0x2B  -  ECDHE-ECDSA-AES128-GCM-SHA256  TLSv1.2  Kx=ECDH  Au=ECDSA   Enc=AESGCM(128)        Mac=AEAD
0xC0,0x27  -  ECDHE-RSA-AES128-SHA256        TLSv1.2  Kx=ECDH  Au=RSA     Enc=AES(128)           Mac=SHA256
0xC0,0x23  -  ECDHE-ECDSA-AES128-SHA256      TLSv1.2  Kx=ECDH  Au=ECDSA   Enc=AES(128)           Mac=SHA256
0xC0,0x13  -  ECDHE-RSA-AES128-SHA           SSLv3    Kx=ECDH  Au=RSA     Enc=AES(128)           Mac=SHA1
0xC0,0x09  -  ECDHE-ECDSA-AES128-SHA         SSLv3    Kx=ECDH  Au=ECDSA   Enc=AES(128)           Mac=SHA1
0x00,0x9E  -  DHE-RSA-AES128-GCM-SHA256      TLSv1.2  Kx=DH    Au=RSA     Enc=AESGCM(128)        Mac=AEAD
0x00,0x67  -  DHE-RSA-AES128-SHA256          TLSv1.2  Kx=DH    Au=RSA     Enc=AES(128)           Mac=SHA256
0x00,0x33  -  DHE-RSA-AES128-SHA             SSLv3    Kx=DH    Au=RSA     Enc=AES(128)           Mac=SHA1
0x00,0xBE  -  DHE-RSA-CAMELLIA128-SHA256     TLSv1.2  Kx=DH    Au=RSA     Enc=Camellia(128)      Mac=SHA256
0x00,0x45  -  DHE-RSA-CAMELLIA128-SHA        SSLv3    Kx=DH    Au=RSA     Enc=Camellia(128)      Mac=SHA1
0x00,0x9C  -  AES128-GCM-SHA256              TLSv1.2  Kx=RSA   Au=RSA     Enc=AESGCM(128)        Mac=AEAD
0x00,0x3C  -  AES128-SHA256                  TLSv1.2  Kx=RSA   Au=RSA     Enc=AES(128)           Mac=SHA256
0x00,0x2F  -  AES128-SHA                     SSLv3    Kx=RSA   Au=RSA     Enc=AES(128)           Mac=SHA1
0x00,0xBA  -  CAMELLIA128-SHA256             TLSv1.2  Kx=RSA   Au=RSA     Enc=Camellia(128)      Mac=SHA256
0x00,0x41  -  CAMELLIA128-SHA                SSLv3    Kx=RSA   Au=RSA     Enc=Camellia(128)      Mac=SHA1
0xC0,0x11  -  ECDHE-RSA-RC4-SHA              SSLv3    Kx=ECDH  Au=RSA     Enc=RC4(128)           Mac=SHA1
0xC0,0x07  -  ECDHE-ECDSA-RC4-SHA            SSLv3    Kx=ECDH  Au=ECDSA   Enc=RC4(128)           Mac=SHA1
0x00,0x05  -  RC4-SHA                        SSLv3    Kx=RSA   Au=RSA     Enc=RC4(128)           Mac=SHA1
0x00,0x04  -  RC4-MD5                        SSLv3    Kx=RSA   Au=RSA     Enc=RC4(128)           Mac=MD5
0xC0,0x12  -  ECDHE-RSA-DES-CBC3-SHA         SSLv3    Kx=ECDH  Au=RSA     Enc=3DES(168)          Mac=SHA1
0xC0,0x08  -  ECDHE-ECDSA-DES-CBC3-SHA       SSLv3    Kx=ECDH  Au=ECDSA   Enc=3DES(168)          Mac=SHA1
0x00,0x16  -  EDH-RSA-DES-CBC3-SHA           SSLv3    Kx=DH    Au=RSA     Enc=3DES(168)          Mac=SHA1
0x00,0x0A  -  DES-CBC3-SHA                   SSLv3    Kx=RSA   Au=RSA     Enc=3DES(168)          Mac=SHA1
```







## 4. 记录层协议

#### 1. 流程图

![记录层协议](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/%E8%AE%B0%E5%BD%95%E5%B1%82%E5%8D%8F%E8%AE%AE.png)



![TLS_protocol_application_data](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_protocol_application_data.png)

#### 2. 详情

基于SecurityParameters来进行：分段、压缩、算MAC、加密

```
struct {
   ConnectionEnd          entity;
   PRFAlgorithm           prf_algorithm;
   BulkCipherAlgorithm    bulk_cipher_algorithm;
   CipherType             cipher_type;
   uint8                  enc_key_length;
   uint8                  block_length;
   uint8                  fixed_iv_length;
   uint8                  record_iv_length;
   MACAlgorithm           mac_algorithm;
   uint8                  mac_length;
   uint8                  mac_key_length;
   CompressionMethod      compression_algorithm;
   opaque                 master_secret[48];
   opaque                 client_random[32];
   opaque                 server_random[32];
} SecurityParameters;
```



## 5. 警告协议

```
Alert protocol

bad_certificate：证书已损坏。可能是签名未验证通过
certificate_revoked：证书被签名者撤销
certificate_expired：证书过期
bad_record_mac：收到了不正确的MAC 
unexpected_message：接收了不合适的报文
decompression_failure：解压缩函数收到不适当的输入
illegal_parameter：握手报文中的一个字段超出范围或与其他字段不兼容。
handshake_failer：握手过程失败。
no_certificate: 未提供证书
unsupported_certificate: 未支持的证书格式 
certificate_unknown: 未知证书
```



## 6. 扩展

### 1. SNI

![TLS_extension_sni](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_extension_sni.png)

```
类似于http的host请求头，表明客户端要访问的域名。
```

### 2. ALPN

![TLS_extension_alpn](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_extension_alpn.png)

```
用于协商TLS的上层协议的版本。主要用于支持http2/http3。
1. 客户端发送其支持的http版本列表。
2. 服务端从列表中选择一个http版本用于后续的http交互。
```

### 3. Signature Algorithms

![TLS_extension_signature_algorithms](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/TLS_extension_signature_algorithms.png)

```
1. 客户端发送这个扩展，表明其支持的签名算法。
2. 服务端后续发送给客户端的证书，所使用的签名算法必须在客户端支持范围内。
```



## 7. session

### 1. 概述

```
1. 为什么要有session？
由于TLS产生了一些性能损耗（网络延时、CPU消耗），所以TLS从设计之初，就采用了万能手段—**加cache**，有2种cache手段：session id，和session ticket。把握手的结果直接缓存起来，绕过握手运算。

2. session缓存的内容有哪些？
会话标识符（session identifier）：每个会话都有唯一编号。
证书（peer certificate）：对端的证书，一般情况下都为空。
压缩算法（compression method）：一般不启用。
密码套件（cipher spec）：客户端和服务器端协商出的密码套件。
主密钥（master secret）：每个会话会保存一个主密钥，注意不是预备主密钥。
会话可恢复标识（is resumable）：表示某个会话是否可恢复。

通过这些会话信息，最终能够生成TLS记录层协议所需要的加密参数（securityparameters），从而能够保护应用层的数据。

3. session id和session ticket区别：
session id：缓存内容存储在服务器
session ticket：缓存内容存储在客户端

4. 部署时的注意事项
如果有负载均衡，session id的数据最好集群共享，否则会导致部分session id失效。
通常更建议使用session ticket，因为既可以降低服务器存储session的压力，也不用考虑共享session的事情。
```



### 2. session id

```
Client                                                Server

ClientHello                   -------->
                                                 ServerHello
                                          [ChangeCipherSpec]
                              <--------             Finished
[ChangeCipherSpec]
Finished                      -------->
Application Data              <------->     Application Data
```



### 3. session ticket

```
Client                                                Server
ClientHello
(SessionTicket extension)     -------->
                                                 ServerHello
                             (empty SessionTicket extension)
                                            NewSessionTicket
                                          [ChangeCipherSpec]
                              <--------             Finished
[ChangeCipherSpec]
Finished                      -------->
Application Data              <------->     Application Data
```



# 五. 部署&运维

## 1. 部署

### 1. 申请证书

自签名证书

免费证书，推荐Let's Encrypt

收费证书

### 2. 部署证书和私钥

1. 证书和私钥都部署在服务器上。
2. 证书部署在服务器上，私钥部署在一台单独的服务器。keyless方案。

#### 2.1  keyless

keyless的思路

1. 将私钥部署在一台单独的服务器（key server）
2. 其他服务器在tls握手时，如果需要用到私钥进行加解密，就讲需要加解密的内容发送到key server，key server处理完再发送回去。

![ssl_handshake_rsa](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/ssl_handshake_rsa.jpg)

![cloudflare_keyless_ssl_handshake_rsa](https://raw.githubusercontent.com/wangyx9858/resource/main/image/uPic/cloudflare_keyless_ssl_handshake_rsa.jpg)



keyless比较符合CDN公司的需求，因为客户将私钥交给CDN，有时会不放心，尤其是一些很重要证书的私钥。

但是keyless对客户来说，会带来额外的负担。

1. 搭建key server，且要保证高可用，是一个成本很高的事情。
2. 如果cdn公司的服务器和key server之间网络拥塞，会影响用户的tls握手。
3. key server如果故障，且没有容灾备份，可能影响大量用户的tls握手。





### 3. http跳转

	1. 301/302
	2. hsts
	 	1. 作用：强制客户端（如浏览器）使用HTTPS与服务器创建连接。
	 	2. 服务器开启方法：当客户端通过HTTPS发出请求时，在服务器返回的HTTP响应头中包含Strict-Transport-Security字段。非加密传输时设置的HSTS字段无效。
	 	3. 示例：https://example.com/ 的响应头含有Strict-Transport-Security: max-age=31536000; includeSubDomains。
	     这意味着：
	     在接下来的一年（31536000秒）中，浏览器只要向example.com或其子域名发送HTTP请求时，必须采用HTTPS来发起连接。比如，用户点击超链接或在地址栏输入 http://www.example.com/ ，浏览器应当自动将 http 转写成 https，然后直接向 https://www.example.com/ 发送请求。



## 2. 安全

### 1. 安全隐患

主要从2方面考虑

1. 规避协议漏洞，禁用协议不安全的特性
2. 规避算法漏洞，禁用不安全的算法

这部分内容多，不易理解，且需要长期关注，大部分人可能没有精力。

建议

	1. 使用云服务的推荐设置
	1. 参考一些网站：https://www.ssllabs.com/、https://wiki.mozilla.org/Security/Server_Side_TLS
	1. 升级openssl版本

### 2. 双向认证

双向认证指的是：除了客户端校验服务端的证书外，服务端也要校验客户端的证书，也就是在tls握手阶段，客户端要发送证书。

双向认证一般用于银行系统，或者一些对认证有很高要求的场景，可以考虑替换http层的认证方案，比如Basic Auth、hash签名、jwt。





## 3. 性能

### 1. 性能损耗

1. 网络延时。完整握手2RTT，Session复用1RTT
2. 加解密的CPU消耗。
   1. tls对cpu的消耗，从我参与过的项目看并不大。在第一家公司的时候，将一个域名从http切换成https，这个域名的带宽在一台机器上大概400M，占机器总带宽的1/5左右，qps占比1/2。切换https后，cpu使用率的峰值没有明显变化，可能就增加了2%左右。

### 2. 优化方法

针对网络延时的优化

1. 支持session
2. 保持tcp长连接，尽量复用tcp连接，而不是每次都创建新连接
3. 升级tls1.3，1.3支持0RTT/1RTT，大幅优化了网络延时。
4. 在满足业务需求的条件下，优先使用301、hsts，避免使用302。减少频繁302跳转消耗掉的时间。



针对cpu消耗的优化

1. 支持session
2. 服务端在tls握手时，尽量选择加解密快的算法



## 4. 常见故障

1. 证书和域名不匹配。
2. 证书链错误
   1. 证书链顺序错误
   2. 证书链缺少中间证书
3. 证书过期
4. 配置下发系统故障，有的服务器没有配置/更新证书