[https://www.thesslstore.com/blog/difference-sha-1-sha-2-sha-256-hash-algorithms/](https://www.thesslstore.com/blog/difference-sha-1-sha-2-sha-256-hash-algorithms/)
[http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012](http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012)


翻译一篇 原文链接 [http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012](http://commandlinefanatic.com/cgi-bin/showarticle.cgi?article=art012)

任何人在使用网络一段时间后，都会遇到一些不明确的安全提示，比如“本网站的证书已过期”，。。等等。
而研究表明，绝大多数人在浏览网页时直接忽视这些提示-实际上，我们当中又有多少人真的知道该担心什么呢？
大多网络用户-及时是资深网民-对什么是证书以及其作用都只是一知半解。但是，证书的概念(精确的说，X.509证书)却在当代网络安全扮演关键作用。

或许你已经猜到，web浏览器是通过数字加密算法来保障数据传输的安全性的。
数字加密算法是基于key的-意味着敏感的输入信息结合key一起再应用数学算法生成毫无意义的文本来模糊化-毫无意义，至少对于没有key的人来说如此。
从数学术语来说，一种加密算法E作用于敏感的明文P和密钥K上，产生了加密文本C，加密后的C是可以安全的在公开网络传输的， C=E(P, K)。同样的，解密算法D被信息接受者应用在加密文本C上，以获得明文文本: 
P = D(C, K)；整个过程完成了一次基于电子加密的数据传输。

广义上讲，有两大类数字加密算法，即对称加密和公开加密(非对称加密)。
在对称加密中，加密和解密使用同一个key。在公开加密中，key被分开成两部分，一部分用来加密(公钥)，而另一部分用来解密(私钥)。这两个keys联系紧密，实际上产生公钥/私钥对的过程复杂而精确，但一旦生成，公钥就可以自由的、安全的分发出去。


公钥用来加密，私钥用来解密。这种机制允许交换信息的双方能够在明文传输渠道中安全交换信息。
接收者生成key对，然后可以将公钥通过不安全的网络环境传递给消息发送者，然后发送者使用公钥加密一些信息，而这些信息只有接收者(拥有秘钥的人)能够解密，从而完成安全传输。

Although there are a few different public-key encryption algorithms, the most popular — and fortunately, the easiest to understand — is the RSA algorithm, named after its three inventors Rivest, Shamir and Adelman. To apply the RSA algorithm, you must find three numbers `e`, `d` and `n` related such that `((m^e^)^d^) % n = m`. Here, `e` and `n` comprise the *public key* and `d` is the private key. When one party wishes to send a message in confidence to the holder of the private key, he computes and transmits `c = (m^e^) % n`. The recipient then recovers the original message `m` using `m = (c^d^) % n`.

虽然有好几种不同的公钥加密算法，但最常用也是最易理解的是RSA算法，该命名来自于其三个发明者Rivest, Shamir and Adelman。
要应用RSA算法，你必须找到3个数字e,d,n 使得 `((m^e^)^d^) % n = m` 这里e 和 n 组成公钥，d则是私钥。
当发送者一方向拥有密钥的一方发送加密消息时，发送者加密`c = (m^e^) % n`，然后接收者解密`m = (c^d^) % n`
 
这种机制基本能干活了，但是有个缺陷，即不能防止中间人攻击。
一个窃听者会置身于发送者和接收者之间，当接收者发送公钥出去时，窃听者会拦截住，并换成自己的公钥再发送出去，当消息发送者接收到公钥后，使用此公钥加密需要发送的敏感信息，然后发送出去，同样，窃听者拦截，并用自己的私钥解密获取到敏感信息，然后再用之前拦截所得的接收者的公钥加密后再发送出去，最终消息到达真正的接收者。
在这种场景下，发送者和接收者都无法知道遭受攻击，加密的意义也就形同虚设了。
对此，现有的最好解决方案就是PKI(public key infrastructure)，PKI的核心是有一系列能担保公钥合法性的机构。
该机制下，如果你收到来自Bob的公钥，你只需要和这些机构确认下该公钥是否合法，即是否真的来自Bob，如果被中间人替换，则这些机构会识别出。

那么，首先怎么才能和这些机构建立可信任的关系呢？你怎么知道你是在和认证机构沟通，而不是一个中间人？
答案依旧在公钥上，但是这次我们把目标倒过来。
公钥能够用来给拥有私钥的实体加密信息，即私钥能够用来证明该实体对公钥的所有权。
消息发送者使用公钥来加密信息，但现在反过来，我们让私钥持有者使用私钥加密一段信息，然后将此加密串连同这段信息一块发送出去，由于只能有公钥可以解密此加密串并获得这段信息，然后检查与传输过来的信息是否一致，从而能够证明发送者是私钥的拥有者。这就是电子签名。

当一对key被用来当做签名时，不是用公钥计算加密文本`c = m^e^%n` ，而是使用私钥计算签名 `s = m^d^%n` ，（上面介绍过，d是私钥），然后将s和m一块发送出去，当接收者接收到这些信息，再用公钥就能够完成验签`m = s^e^%n` ，即根据签名和公钥能够得到被加密的m，在和接收到的m对比，一致的话，则表明身份验证成功。
即，我所拥有的公钥，确实是你产生的。实际应用中，为了缩短s，常常用md5或者sha-1等计算m的哈希值，然后再得到s。
 
So, what does all this have to do with certificates? Well, fundamentally, a certificate is a holder for public key, along with a few assertions about the owner of the public key. When you establish a secure connection with a website, that website presents a certificate containing, at least, two pieces of information: the public key of the site and the digital signature supplied by a trusted certificate authority. The browser then uses that public key to establish a secure connection.
通过这种方式，公证机构就能够给其它实体提供担保了。
PKI中，证书颁发者担此角色
这一切与证书有什么关系呢？
本质上，一个证书里面即包含有公钥，并附带一些关于owner的信息资料。
当你和一个网站建立安全连接时，该网站会发送一个证书，至少包含两个信息(网站公钥和第三方证书颁发机构的电子签名)。浏览器就会使用此公钥来建立安全连接
![image.png | center | 960x892](https://cdn.yuque.com/lark/2018/png/450b004d-1b19-48ce-937e-6118eec01715.png "")



