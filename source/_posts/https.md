---
title: 一次安全可靠的通信
date: 2018-06-20 12:28:25
tags: 
categories: 技术向
---

摘抄自——[拉风的博客——一次安全可靠的通信](http://rapheal.sinaapp.com/2015/09/16/https/)

公众号文章一直被运营商各种挟持插广告，今年逐步都切换成 HTTPS，前两周再重新梳理了一些 HTTPS 细节，趁有空整理成文章做个分享。

## Alice 与 Bob 的通信
我们以 Alice 与 Bob 一次通信来贯穿全文，一开始他们都是用铭文的形式在网络传输通信内容。

![Alice与Bob通信.jpg](https://tva4.sinaimg.cn/large/a9034e0egy1fshjuca0z2j20ru0g6dh5.jpg)
## 嗅探以及篡改
如果在他们的通信链路出现了一个 Hacker，由于通信内容都是明文可见，所以 Hacker 可以嗅探看到这些内容，也可以篡改这些内容。

![Hacker嗅探与篡改.jpg](https://tvax3.sinaimg.cn/large/a9034e0egy1fshk06gh4kj20vo0mctb6.jpg)

公众号的文章之前就遇到很多被挟持篡改了内容，插入广告。

![图文挟持样例.jpg](https://tva4.sinaimg.cn/large/a9034e0egy1fshk1i1hgtj21970og45h.jpg)

## 加密解密
既然铭文有问题，那就需要对明文进行加密处理，让中间人看不懂内容，于是乎要对原来的内容变成一段看不懂的内容，称为加密，反之则是解密。而本质其实就是一种数学运算的逆运算，类似加法减法，例如发送方可以将 abcd…xyz每个字母+1映射成 bcde…yza，使得原文字母变成看不懂的序列，而接收方只需要将每个字母-1就可以恢复成原来的序列，当然这种做法规律太容易被破解了，后边会有案例示意图：

![加密解密本质.jpg](https://tva1.sinaimg.cn/large/a9034e0egy1fshk2m68klj20v40h2go1.jpg)

## 对称加密
如果对两个二进制数 A 和 B 进行异或运算得到结果 C，那 C 和 B 再异或运算一次就会回到 A，所以异或也可以作为加密解密的运算。
![逆运算XOR.jpg](https://tva1.sinaimg.cn/large/a9034e0egy1fshk2w43daj20v40h20v2.jpg)

把操作数 A 作为明文，操作数 B 作为秘钥，结果 C 作为密文。可以看到加密解密运用同一个秘钥 B，把这种加密解密都用同一个秘钥的方式叫做对称加密。
![对称加密.jpg](https://tvax2.sinaimg.cn/large/a9034e0egy1fshk3n3f0fj20ur0h2gor.jpg)

可以看到简单的额异或加密/解密操作，需要秘钥跟明文位数相同。为了克服这个确定，需要改进一下，把明文进行分组，每组长度跟秘钥一致，分别做异或操作就可以得到密文分片，再合并到一起就得到密文了。
![对称加密——分组.jpg](https://tvax4.sinaimg.cn/large/a9034e0egy1fshk4gqih4j21360kawie.jpg)

但是这种简单分组的模式也是很容易发现规律，可以从下图看到，中间采用队员图进行 DES 的 ECB 模式加密（就是上边提到的简单分组的模式）
![对称加密不同模式对比.jpg](https://tva4.sinaimg.cn/large/a9034e0egy1fshk5l82k9j20p80awdiz.jpg)

很显然，原图一些特征在加密后还是暴露无遗，因此需要再改进一把。一般的思路就是讲上次分组运算的结果/中间结果参与到下次分组的运算中去，是的更随机混乱，更难破解。一下图片来自维基百科：
![对称加密——模式.jpg](https://tvax2.sinaimg.cn/large/a9034e0egy1fshk5w49ekj210z0imwj5.jpg)

经过改良后，Alice 与 Bob 如果能提前拿到一个对称加密的秘钥，他们就可以通过加密明文来保证他们的说话内容不会被 Hacker 看到了。

![Alice与Bob采用对称加密方式通信.jpg](https://tvax1.sinaimg.cn/large/a9034e0egy1fshk6k5z5wj20vf0j9tbp.jpg)

## 非对称加密
刚刚还引发另一个问题，这个对称加密用到的秘钥如何互相告知呢？如果在传输真正的数据之前，先把密钥传过去，那 Hacker 还是能嗅探到，那之后就了无秘密了。于是乎出现另外一种手段：
![传递密钥.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshk76ktr7j20uq0q1gpe.jpg) 

这就是非对称加密，任何人都可以通过拿到 Bob 公开的公钥对内容进行加密，然后只有 Bob 自己私有的钥匙才能解密还原出原来内容。

![非对称加密.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshk7nre2uj20sj0hkgo2.jpg) 

RSA 就是这样一个算法，具体数学证明利用了大质数乘法难以分解、费马小定理等数学理论支撑它难以破解。相对于前面的对称加密来说，其需要做乘法模除等操作，性能效率比对称加密差很多。

![RSA.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshk8h7mc0j20sj0gngo4.jpg) 

由于非对称加密的性能低，因此我们用它来先协商对称加密的秘钥即可，后续真正通信的内容还是用对称加密的手段，提高整体的性能。如果机器上安装了 OpenSSL，可以通过以下两个命令对比 RSA 和 AES 的速度比较： openssl speed rsa; openssl speed aes;

![优化Alice与Bob的通信.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshk947vhdj20vf0j9acj.jpg) 

## 认证
上边虽然解决了密钥配送的问题，但是中间人还是可以欺骗双方，只要在 Alice 向 Bob 要公钥的时候，Hacker 把自己的公钥给了 Alice，而 Alice 是不知道这个事情的，以为一直都是 Bob 跟她在通信。

![中间人欺骗.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshk9ort0nj20vy0lnq5g.jpg) 

那怎么证明现在传过来的公钥就是 Bob 给的呢？在危险的网络环境下，还是没有解决这个问题。

![怎么证明公钥就是Bob的.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkahpxyqj20sj0hctar.jpg) 

一般我们现实生活中是怎么证明 Bob 就是 Bob 呢？一般都是政府给我们每个人发一个身份证（假设身份证没法伪造），我只要看到 Bob 身份证，就证明 Bob 就是 Bob。
网络也可以这么做，如果有个大家都信任的组织 CA 给每个人出证明，那 Alice 只要拿到这个证明，检查一下是不是 CA 制作的 Bob 证书就可以证明 Bob 是 Bob。所以这个证书里面需要有两个重要的东西：Bob 的公钥+CA 做的数字签名。

![可信组织来证明大家.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkazq3nqj20re0jkmzn.jpg) 

前面说到用公钥加密，只有拥有私钥的人才能解密。数字证书有点反过来：用私钥进行加密，用公钥进行解密。CA 用自己的私钥对 Bob 的信息（包含 Bob 公钥）进行加密，由于 Alice 无条件信任 CA，所以已经提前知道 CA 的公钥，当她收到 Bob 证书的时候，只要用 CA 的公钥对 Bob 证书内容进行解密，发现能否成功解开（还需要校验完整性），此时说明 Bob 就是 Bob，那之后用证书里面的 Bob 公钥来走之前的流程，就解决了中间人欺骗这个问题了。
这种方式也是一种防抵赖的方式，让对方把消息做成一个数字签名，只要我收到信息，用对方的公钥成功解开校验这个签名，说明这个消息必然是对方发给我的，对方不可以抵赖这个行为，因为只有他才拥有做数字签名的私钥。

![可信组织来证明大家.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkbk8uenj20ve0jnjuo.jpg) 

CA 其实是有多级关系，顶层有个根 CA，只要他信任 B，B 信任 C，C信任D，那我们基本就可以认为 D 是可信的。

![证书信任链.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkc2vvmvj20rh0d6tab.jpg) 

## 完整性
上面基本上已经解决了保密性和认证，还有一个完整性没有保障。虽然 Hacker 还是看不懂内容，但是 Hacker 可以随便篡改通信内容的几个 bit 位，此时 Bob 解密看到的可能是很乱的内容，但是他也不知道这个究竟是 Alice 真是发的内容，还是被别人投投改了的内容。

![Alice你在说什么.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkcpt3v5j20vy0frdhp.jpg) 

单项 hash 函数可以把输入变成一个定长的输出串，其特点就是无法从这个输出还原回输入内容，并且不同的输入几乎不可能产生相同的诶输出，即使你要特意去找也很难找到这样的输入（抗碰撞性），因此 Alice 只要将明文内容做一个 Hash 运算得到一个 Hash 值，并一起加密传递过去给 Bob。Hacker 即便篡改了内容，Bob 解密之后发现拿到的内容以及对应计算出来的 Hash 值与传递过来的不一致，说明这个包的完整性被破坏了。

![数据完整性.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkdi464fj20vy0gadi9.jpg) 

## 一次安全可靠的通信
总结一下，安全可靠的保障：
	1. 对称加密以及非对称加密来解决：保密性
	2. 数字签名：认证、不可抵赖
	3. 单项 Hash 算法：完整性

HTTPS 的原理就是这样，真正交换应用数据之前，所协商的信息的过程称为 ssl 握手，可以用此命令：curl -c “TCP connect time: %{time_connect}, SSL connect time: %{time_appconnect}\n” -so /dev/null https://mp.weixin.qq.com; 来对比 tcp 连接时间跟 ssl 连接时间，可以看出，为了安全会带来额外的性能消耗。

来一张完整的图：

![一次可靠的通信.jpg](https://wx3.sinaimg.cn/large/a9034e0egy1fshkdqprzvj20x80ptjux.jpg) 

    