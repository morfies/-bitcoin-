## bitcoin

比特币的所有权由三个部分组成： digital keys, bitcoin addresses, and digital signatures
digital keys 不在网络上存储，而是由比特币钱包生成，Keys enable many of the interesting properties of bitcoin, including decentralized trust and control, ownership attestation, and the cryptographic-proof security model.

digital signature 在区块链中的所有比特币交易都需要合法的电子签名，电子签名只能由秘钥生成
这个用来花钱的电子签名也被称为witness，这是密码学中的叫法；比特币交易中的电子签名标志着这笔交易所花的钱的真正所有者

Keys是成对生成的，由private(secret) key 和 public key组成。可以将public key想象成银行卡号，private key就像密码；这些Keys很少直接被比特币用户看见，它们被钱包存储和管理着。

在一笔比特币的付款部分，收款人的public key 已电子指纹的方式展现出来，被称为比特币地址，就如同银行支票上的受益人。
大部分场景下，比特币地址由publickey生成，但是，并不是所有比特币地址都代表publickey，有的代表可以代表scripts，后面讲到
比特币地址是用户唯一经常看到的所有keys的代表，因为他们会将地址共享出去

Public key cryptography
公钥加密发明于1970s，是计算机和信息安全的数学基础
从公钥加密的发明以来，几种合适的数学方法被发掘了，包括素数幂，椭圆曲线乘法等；这些方法几乎不可逆，意思是说它们从一个方向计算很简单，但反过来计算却不可行；
在这些数学方法的基础上，密码学诞生了数字秘钥和不可伪造的数字签名，比特币就是使用的椭圆曲线算法来做其加密算法

In bitcoin, we use public key cryptography to create a key pair that controls access to bitcoin. The key pair consists of a private key and—​derived from it—​a unique public key. The public key is used to receive funds, and the private key is used to sign transactions to spend the funds
比特币中使用公钥加密技术生成一对keys来管理比特币，这一对keys由秘钥和一个唯一的公钥(由秘钥产生)组成，公钥用来接收资产，私钥用来给花费资产的交易签名。

There is a mathematical relationship between the public and the private key that allows the private key to be used to generate signatures on messages. This signature can be validated against the public key without revealing the private key.
公钥和私钥之间有一种数学联系，使得私钥可以用来生成电子签名，而这个签名可以在不暴露私钥的前提下和相应的公钥进行匹配验证。

When spending bitcoin, the current bitcoin owner presents her public key and a signature (different each time, but created from the same private key) in a transaction to spend those bitcoin. Through the presentation of the public key and signature, everyone in the bitcoin network can verify and accept the transaction as valid, confirming that the person transferring the bitcoin owned them at the time of the transfer.
当花费比特币时，当前拥有者会在交易中提供他的公钥和电子签名(签名每次不相同，但都由私钥产生)；通过公钥和电子签名，网络上的每个节点都能验证当前交易的合法性，确保当前资产转移人在交易发生时刻确实拥有该笔比特币。

## Private and Public Keys

A bitcoin wallet contains a collection of key pairs, each consisting of a private key and a public key. The private key (k) is a number, usually picked at random. From the private key, we use elliptic curve multiplication, a one-way cryptographic function, to generate a public key (K). From the public key (K), we use a one-way cryptographic hash function to generate a bitcoin address (A). In this section, we will start with generating the private key, look at the elliptic curve math that is used to turn that into a public key, and finally, generate a bitcoin address from the public key.
比特币钱包中有一堆key对，每个key对都由私钥和公钥组成。每个私钥(k)是一个随机数，通过椭圆曲线乘法(一种单向加密方法)生成公钥(K),再通过单向加密哈希方法，由公钥生成一个比特币地址(A)。

为什么要使用非对称加密(Private/Public Keys)?
Why is asymmetric cryptography used in bitcoin? It’s not used to "encrypt" (make secret) the transactions. Rather, the useful property of asymmetric cryptography is the ability to generate digital signatures. A private key can be applied to the digital fingerprint of a transaction to produce a numerical signature. This signature can only be produced by someone with knowledge of the private key. However, anyone with access to the public key and the transaction fingerprint can use them to verify the signature. This useful property of asymmetric cryptography makes it possible for anyone to verify every signature on every transaction, while ensuring that only the owners of private keys can produce valid signatures.
为什么比特币使用的是非对称加密呢，因为比特币并不是需要加密交易信息，而是借助非对称加密算法的特性生成电子签名。
私钥能够应用在一笔交易的电子指纹(hash)上来生成电子签名，这个签名仅能被拥有私钥的人产生出来。但是呢，任何拥有其公钥和该笔交易的电子指纹的人都能够验证签名的合法性。
这个非对称加密的特性使得任何人都能够验证任何交易上的签名合法性，且能保证只要私钥的owner才能产生合法的电子签名。

### Private Keys

私钥是纯数字，256位二进制随机串。一旦失去，无法恢复

More precisely, the private key can be any number between 0 and n - 1 inclusive, where n is a constant (n = 1.1578 \* 1077, slightly less than 2256) defined as the order of the elliptic curve used in bitcoin.
要生成这么一个key，我们随机产生256bit的数字，然后比较是否小于n，是，则使用，否，则重试

In programming terms, this is usually achieved by feeding a larger string of random bits, collected from a cryptographically secure source of randomness, into the SHA256 hash algorithm, which will conveniently produce a 256-bit number

2^256差不多等于10的77次方，什么概念呢，我们能看见的宇宙中所包含的原子大约有10的80次方！

### Public Keys

公钥是私钥通过椭圆曲线乘法计算得来，且不可逆:K = k \* G, k是私钥，G称为generator point，而K则是公钥。
> Elliptic curve multiplication is a type of function that cryptographers call a "trap door" function: it is easy to do in one direction (multiplication) and impossible to do in the reverse direction (division). The owner of the private key can easily create the public key and then share it with the world knowing that no one can reverse the function and calculate the private key from the public key. This mathematical trick becomes the basis for unforgeable and secure digital signatures that prove ownership of bitcoin funds.

Elliptic Curve Cryptography Explained
好复杂，反正是不可逆，可以通过私钥和预先定义好的G算出公钥，k\*G并不是通常意义上的数字相乘，而是在椭圆曲线上找到k个G相加的结果点，不可逆
比特币中G是固定的，所以，对每个私钥k，只有唯一确定的公钥K与之对应，从而关系固定

### Bitcoin Addresses

A bitcoin address is a string of digits and characters that can be shared with anyone who wants to send you money. Addresses produced from public keys consist of a string of numbers and letters, beginning with the digit "1." Here’s an example of a bitcoin address:
1J7mdg5rbQyUHENYdx39WVWK7fsLpEoXZy
比特币地址由一串数字和字母组成，可以分享给需要给你转账的人。

The bitcoin address is what appears most commonly in a transaction as the "recipient" of the funds. If we compare a bitcoin transaction to a paper check, the bitcoin address is the beneficiary, which is what we write on the line after "Pay to the order of." On a paper check, that beneficiary can sometimes be the name of a bank account holder, but can also include corporations, institutions, or even cash. Because paper checks do not need to specify an account, but rather use an abstract name as the recipient of funds, they are very flexible payment instruments. Bitcoin transactions use a similar abstraction, the bitcoin address, to make them very flexible. A bitcoin address can represent the owner of a private/public key pair, or it can represent something else, such as a payment script, as we will see in [p2sh]. For now, let’s examine the simple case, a bitcoin address that represents, and is derived from, a public key.
比特币地址在一笔交易中通常就是该笔资金的收款人。如果和实物交易相比，那么就是银行支票上的受益人，在物理世界中，受益方有可能是具体的人，也有可能是公司机构等。
同样的，比特币地址也有此灵活性，可以代表拥有此公钥的所有人，也可以代表一个支付脚本。

Starting with the public key K, we compute the SHA256 hash and then compute the RIPEMD160 hash of the result, producing a 160-bit (20-byte) number：
A = RIPEMD160(SHA256(K))

Base58Check
比特币地址是Base58Check编码的

所以，整个流程是这样：
公钥 -> (sha256 -> ripemd160 两次hash)160bits -> Base58Check编码 -> 比特币地址

Base58 and Base58Check Encoding
为了能够更紧凑的表示大数值，很多计算机系统使用有字母参与的大于10为基数的表示法，比如16进制
base64则是10个数字+26个大写字母+26个小写字母+两个特殊字符(“`&#x201d; and "/") 那么base58是转为比特币设计的，剔除了base64中易混淆和难读的字符(0 (number zero), O (capital o), l (lower L), I (capital i), and the symbols &#x201c;`” and "/")

Base58Check是啥呢
为了额外加强安全，避免转录或者书写错误，比特币引入了Base58Check，Base58Check是base58编码格式，有内置的校验码
校验码即是追加在后面的4bytes的数据，这些校验码是由被编码的内容hash而来，从而能有效监测数据书写错误。保证了比特币地址的安全可靠，不易写错。

To convert data (a number) into a Base58Check format, we first add a prefix to the data, called the "version byte," which serves to easily identify the type of data that is encoded. For example, in the case of a bitcoin address the prefix is zero (0x00 in hex), whereas the prefix used when encoding a private key is 128 (0x80 in hex). A list of common version prefixes is shown in Base58Check version prefix and encoded result examples.

Next, we compute the "double-SHA" checksum, meaning we apply the SHA256 hash-algorithm twice on the previous result (prefix and data):

checksum = SHA256(SHA256(prefix+data))
From the resulting 32-byte hash (hash-of-a-hash), we take only the first four bytes. These four bytes serve as the error-checking code, or checksum. The checksum is concatenated (appended) to the end.

要将一段数据转成Base58Check格式，步骤是这样的，首先给数据加version前缀，表示不同的使用场景
然后，对(prefix+data)数据做两次sha计算，再取结果的前4字节数据，拼到data后面

最后，在对prefix+data+checksum 三部分组成的数据做Base58编码，即得到最终投入各场景使用的数字

Compressed public keys
为了节省存储空间，比特币公钥的存储做了很多优化压缩，比如base58压缩，然后公钥本来是椭圆曲线上的一个点，有xy两个坐标，但是基于x是能计算出y的，于是比特币只存储了x，使得空间节省一半。

经压缩的公钥也对应着同一个私钥，即也是由同一个私钥生成的。但是，与非压缩的公钥看上去差异很大，更甚，如果我们按同样的流程用这个压缩版的公钥生成比特币地址的话，会与非压缩版本生成的地址不一致。
这会引起疑惑，因为意味着单一私钥能生成不同的公钥，进而能生成不同的比特币地址，但是，这些地址所对应的私钥是一致的，这就够了。

## Wallets
比特币钱包其实可以理解为一个数据结构，用来存储和管理用户的keys

一种常见的误解是以为钱包里存放着比特币。事实上，钱包里面只存放着keys，比特币是以记录的方式存在于整个比特币网络里，用户通过使用自己的key来给交易签名的方式在网络中流通着比特币。这么理解，比特币钱包就像是钥匙扣。

本质上有两大类钱包，根据钱包里面的keys是否有关联关系来区分。
第一种是不确定性钱包(*nondeterministic wallet*)，包里的所有key各自随机生成，相互间没有关联关系。这类钱包也叫做JBOK，Just a Bunch Of Keys.
第二种称之为确定性钱包(*deterministic wallet*)，所有的key都是由一个被称为seed的主key衍生出来。所有的这些key是有关联的，并且能够用原始seed重新生成。对这种钱包，有不同的key衍生方法，最常用的是一种树形结构，被称为*HD*wallet(*hierarchical deterministic*)。
#### Nondeterministic (Random) Wallets
在第一版比特币钱包(现在称为Bitcoin Core)实现中，钱包即为一堆随机生成的key的集合。
比如，最初的Bitcoin Core客户端会在第一次启动时预先生成100个随机key，后续再根据需要生成更多key，每个key只使用一次。这类钱包主键被确定性钱包取代，因为它们维护备份和导出都太麻烦了。
随机key的劣势在于，如果你生成了很多key，你需要经常备份所有，每个key都必须备份，不然它所关联的比特币就丢失了。这种备份方式就和避免地址复用的原则相违背了。

![image.png | center | 568x391](https://cdn.yuque.com/lark/2018/png/a99842db-1b9e-4577-94aa-731a02b32956.png "")

#### Deterministic (Seeded) Wallets
 In a deterministic wallet, the seed is sufficient to recover all the derived keys, and therefore a single backup at creation time is sufficient. The seed is also sufficient for a wallet export or import, allowing for easy migration of all the user’s keys between different wallet implementations. [Type-1 deterministic (seeded) wallet: a deterministic sequence of keys derived from a seed](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc#Type1_wallet) shows a logical diagram of a deterministic wallet.
确定性钱包或基于种子seed的钱包，是通过运用单向hash方法，基于一个seed能衍生出一系列key。
seed是随机数再结合一些其它数据，比如序列号或者链码。
在确定性钱包中，seed能够用来恢复所有衍生的key，所以在创建时单独备份一次就足够了。seed同样足够用来导入或导出到其它钱包中。

![image.png | center | 465x335](https://cdn.yuque.com/lark/2018/png/b11210bb-8fed-4062-a26b-5588a3f5fe0c.png "")

#### HD Wallets (BIP-32/BIP-44)
Deterministic wallets were developed to make it easy to derive many keys from a single "seed." The most advanced form of deterministic wallets is the HD wallet defined by the BIP-32 standard. HD wallets contain keys derived in a tree structure, such that a parent key can derive a sequence of children keys, each of which can derive a sequence of grandchildren keys, and so on, to an infinite depth. This tree structure is illustrated in [Type-2 HD wallet: a tree of keys generated from a single seed](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc#Type2_wallet).
最高级的确定性钱包就是BIP-32标准所出的HD wallet，树状结构，能够一层一层的无限向下衍生
![image.png | center | 586x395](https://cdn.yuque.com/lark/2018/png/11a955b8-c672-48f9-9224-6170ea81b2e7.png "")

HD钱包两大优势，一是树状结构能很好的和组织结构对应起来，比如一组key用来接收付款，一组key用来支出等，或者直接与公司部门结构对应，给不同部门分配不同类组下的key。
第二个优势就是用户能在不用访问到对应私钥的前提下生成一系列公钥。这使得HD钱包能在不安全的服务器上使用，或者用来专门做收款功能。公钥不用提前生成，且服务器没有能够用来支出的私钥。

#### Seeds and Mnemonic Codes (BIP-39)
HD钱包是管理多key和多地址很有力的机制。如果使用英文单词作为助记符来生成seed的话，正如BIP-39定义，这种钱包就更易使用了。
#### Wallet Best Practices
As bitcoin wallet technology has matured, certain common industry standards have emerged that make bitcoin wallets broadly interoperable, easy to use, secure, and flexible. These common standards are:
* Mnemonic code words, based on BIP-39
* HD wallets, based on BIP-32
* Multipurpose HD wallet structure, based on BIP-43
* Multicurrency and multiaccount wallets, based on BIP-44

These standards may change or may become obsolete by future developments, but for now they form a set of interlocking technologies that have become the de facto wallet standard for bitcoin.
The standards have been adopted by a broad range of software and hardware bitcoin wallets, making all these wallets interoperable. A user can export a mnemonic generated on one of these wallets and import it in another wallet, recovering all transactions, keys, and addresses.
Some example of software wallets supporting these standards include (listed alphabetically) Breadwallet, Copay, Multibit HD, and Mycelium. Examples of hardware wallets supporting these standards include (listed alphabetically) Keepkey, Ledger, and Trezor.
### Wallet Technology Details

#### Mnemonic Code Words (BIP-39)

| Tip | Mnemonic words are often confused with "brainwallets." They are not the same. The primary difference is that a brainwallet consists of words chosen by the user, whereas mnemonic words are created randomly by the wallet and presented to the user. This important difference makes mnemonic words much more secure, because humans are very poor sources of randomness. |
| :--- | :--- |



助记符单词是一系列代表用来生成seed随机数的英文单词，通过这些单词能够重建seed，然后根据seed能够衍生出key。一个基于确定性钱包标准实现的钱包在创建时会向用户展示12到24个单词序列。这一单词序列即可用来作为钱包备份，通过它可以恢复所有衍生的key。单词的优势是简单易记，且抄写不易出错。
助记符在BIP-39中定义，并在当今已经成为业界标准了。
#### Generating mnemonic words

助记符单词由钱包根据BIP-39的标准流程自动生成。钱包从一个熵源开始，添加一个校验码，然后将熵映射到单词列表：
1. Create a random sequence (entropy) of 128 to 256 bits.
2. Create a checksum of the random sequence by taking the first (entropy-length/32) bits of its SHA256 hash.
3. Add the checksum to the end of the random sequence.
4. Split the result into 11-bit length segments.
5. Map each 11-bit value to a word from the predefined dictionary of 2048 words.
6. The mnemonic code is the sequence of words.
    

![image.png | center | 531x549](https://cdn.yuque.com/lark/2018/png/729d9d54-03f2-4983-ac69-ff35661c6f0a.png "")

![image.png | center | 546x224](https://cdn.yuque.com/lark/2018/png/3fa814c5-27bd-4c3f-93bc-dd7ed80f49b6.png "")

##### From mnemonic to seed
助记单词代表着128~256bit的熵，这个熵值然后通过PBKDF2方法延展到512bit成为seed，最终，通过这个seed就能够用来创建确定性钱包以及其需要的key了。
这个延展方法PBKDF2有两个参数：助记符和salt，salt的目的是防止暴力破解。
在BIP-39标准中，salt还有另外一个目的，就是作为用户密码，给安全性加双保险。
The process described in steps 7 through 9 continues from the process described previously in [Generating mnemonic words](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc#generating_mnemonic_words):
1. The first parameter to the PBKDF2 key-stretching function is the *mnemonic* produced from step 6.
2. The second parameter to the PBKDF2 key-stretching function is a *salt*. The salt is composed of the string constant "`mnemonic`" concatenated with an optional user-supplied passphrase string.
3. PBKDF2 stretches the mnemonic and salt parameters using 2048 rounds of hashing with the HMAC-SHA512 algorithm, producing a 512-bit value as its final output. That 512-bit value is the seed.
![image.png | center | 598x419](https://cdn.yuque.com/lark/2018/png/1b31ccda-8a41-4477-8abb-877d8bd312f4.png "")


| Tip | The key-stretching function, with its 2048 rounds of hashing, is a very effective protection against brute-force attacks against the mnemonic or the passphrase. It makes it extremely costly (in computation) to try more than a few thousand passphrase and mnemonic combinations, while the number of possible derived seeds is vast (2^512^). |
| :--- | :--- |


BIP-39标准允许在生成seed的时候加入额外的用户密码，如果用户没指定密码，则salt为常量字符串"mnemonic"，如果指定了密码，则salt就不同了，从而能产生不同的seed，事实上，给定一个确定的助记符单词序列，每个不同的密码就会产生不同的seed，从而对应着巨大不同的seed空间，即未初始化的钱包。这个集合如此巨大(2^512^)以至于暴力破击基本无望。

| Tip | There are no "wrong" passphrases in BIP-39. Every passphrase leads to some wallet, which unless previously used will be empty. |
| :--- | :--- |



这个可选的密码创造了两个重要特性：
* 成为钱包安全的第二因子，让助记符本身单方面无用，及时助记符被盗用也没事
* 第二个感觉没看懂，没啥意义(A form of plausible deniability or "duress wallet," where a chosen passphrase leads to a wallet with a small amount of funds used to distract an attacker from the "real" wallet that contains the majority of funds)
#### Creating an HD Wallet from the Seed
HD wallets are created from a single *root seed*, which is a 128-, 256-, or 512-bit random number. Most commonly, this seed is generated from a *mnemonic* as detailed in the previous section.
HD wallets能有单一的root seed创建，seed是512bit的随机数，钱包中的每一个key都是由seed确定性产生的，这使得能够在所有兼容的钱包体系中通过同一个seed完整恢复出所有的key。这就使得备份或者导入导出方便快捷了。
![image.png | center | 655x290](https://cdn.yuque.com/lark/2018/png/208cd297-9e1a-439f-804e-8cf8b6676f0c.png "")

root seed通过HMAC-SHA512哈希算法能够得到一个主私钥m(master private key)和一个主链码c(master chain code)。私钥可以通过椭圆曲线产生对应的公钥M，链码c则用来作为熵输入到函数中产生子key
##### Private child key derivation
HD wallets使用CKD方法(Child Key Derivation)来根据父key衍生出子key
CDK方法基于单向哈希方法，它结合了：
* 父私钥或者父公钥
* 链码
* 序列号
The chain code is used to introduce deterministic random data to the process, so that knowing the index and a child key is not sufficient to derive other child keys. Knowing a child key does not make it possible to find its siblings, unless you also have the chain code. The initial chain code seed (at the root of the tree) is made from the seed, while subsequent child chain codes are derived from each parent chain code.

These three items (parent key, chain code, and index) are combined and hashed to generate children keys, as follows.
这三者(父key、链码、序列号)结合并哈希后，就能产生子key(就像通过seed产生主私钥、主公钥、链码一样)，这样一层层迭代，就能产生无穷的子key了。
The parent public key, chain code, and the index number are combined and hashed with the HMAC-SHA512 algorithm to produce a 512-bit hash. This 512-bit hash is split into two 256-bit halves. The right-half 256 bits of the hash output become the chain code for the child. The left-half 256 bits of the hash are added to the parent private key to produce the child private key. In [Extending a parent private key to create a child private key](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch05.asciidoc#CKDpriv), we see this illustrated with the index set to 0 to produce the "zero" (first by index) child of the parent.
![image.png | center | 614x358](https://cdn.yuque.com/lark/2018/png/d9ccd1ae-5b8c-4ada-98e9-5044c45719bf.png "")

从而，每一组父key，能够产生最多2^31个子key。
#### Using derived child keys

子私钥能用来做什么呢？可以用来生成公钥，进而生成比特币地址，然后就可以用在交易中了。

| Tip | A child private key, the corresponding public key, and the bitcoin address are all indistinguishable from keys and addresses created randomly. The fact that they are part of a sequence is not visible outside of the HD wallet function that created them. Once created, they operate exactly as "normal" keys. |
| :--- | :--- |



##### Extended keys
 "extended key" 即上面提到的由父key生成的子key(512bit公钥或者私钥)和链码(512bit)连起来。
因为通过拓展key能生成一系列的子key，它常被用来作为一个新的分支。
有两类，私钥和链码连起来的叫私密拓展key，能用来生成完整的分支；公钥和链码连起来叫做公开拓展key，只能用来生成公钥分支
##### Public child key derivation



## Transactions
### Introduction
交易是比特币系统中最重要的部分。比特币系统中所有其它部分都是设计用来确保交易能被创建、传输、校验，并最终添加到全球账本即区块链上。__交易信息其实就是一种数据结构，它记录着比特币网络中参与者之间的价值传递信息__。

本章节介绍交易的各种细节。

### Transactions in Detail
在前面我们有提到，使用block explorer查看Alice买咖啡时与Bob之间的交易信息。
从上面可以看到交易从Alice的比特币地址到Bob的地址，这是简化后的交易信息。
#### Transactions—Behind the Scenes
在幕后，真实的交易和在explorer上看到的信息有很大差异的。事实上，在用户界面看到的好多数据结构，在底层的比特币网络中并不存在。
我们可以用Bitcoin Core的命令行工具(getrawtransaction and decoderawtransaction)来查看、解码一个交易，看看它长什么样：
```js
{
  "version": 1,
  "locktime": 0,
  "vin": [
    {
      "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
      "vout": 0,
      "scriptSig" : "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d172787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.01500000,
      "scriptPubKey": "OP_DUP OP_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 OP_EQUALVERIFY OP_CHECKSIG"
    },
    {
      "value": 0.08450000,
      "scriptPubKey": "OP_DUP OP_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP_EQUALVERIFY OP_CHECKSIG",
    }
  ]
}
```
你会注意到，好多信息没看到！Alice的地址呢？Bob的地址呢？Alice付款的0.1比特呢？
在比特币中，没有比特币结余，没有发送者，没有接收者，没有结余，没有账户，没有地址。所有这些东西都是在上层结构中为了方便用户理解才构建出来的。
你会注意到很多奇怪的字段和16进制字符串。别担心，我们继续解释。

### Transaction Outputs and Inputs
一笔比特币交易记录中最基本的信息块是交易输出部分。交易输出是不可分割的比特币金额，记录在区块链上，并被整个网络合法接纳。比特币网络中的完整节点(full nodes即包含全网所有交易记录的节点)会跟踪所有可用来支出的输出块信息，即UTXO(Unspent Transaction Outputs)，所有UTXO记录的集合叫做UTXO set，当前是百万量级。UTXO集会随着新的UTXO记录产生而增加，随着UTXO被消费而减少。每一笔交易就代表UTXO集的一次变动。
当我们说一个用户收到一笔比特币，实际意思是比特币钱包发现了一个UTXO，该UTXO能够使用钱包中管理的某个key来支付使用。因此，用户的比特币结余即为该用户钱包中所有的key能够支配的UTXO的总和。结余的概念是钱包应用创造的。钱包通过扫描整个区块链所有记录，集合出该钱包能支配的所有UTXO。大多数钱包会维护一个数据库来存储结余，便于快速查询使用。


一笔交易输出(transaction output)可以是由satoshi计价的任何数字，比如美元是由最小单位美分计价，比特币则是由最小单位satoshi计价(1bit = 10^8satoshi)，虽然一个输出可以是任意数值，但一旦被创建，它就不可分割了。这是交易输出所具备的一个重要特性：outputs是分散的不可拆分的单元，由satoshi计价。一个未支出的output只能被作为整体在交易中支出。
如果一笔UTXO笔要支付的费用大，任然需要整体支付，然后再产生零钱。比如你有20btc，但需要支付1btc，你需要将20btc作为整体支出，然后会产生两笔输出，一笔是1btc支付给你的交易方，一笔是19btc支付给你自己。
如果需要支付的金额比较大，则钱包会在UTXO中找到一个组合，让组合后的金额大等于支付金额。

一笔交易花费的是之前记录的还未被花费的交易输出outputs，然后创建新的交易输出，而这新的输出记录又可以被将来的交易使用。如此这般，比特币价值就在区块链上完成传递。

有一种特殊交易coinbase交易 ，存在于每个区块的第一笔交易中。
这笔交易是由挖矿胜出者放在这里，且其中的比特币是全新铸造出的，即其中的比特币不会消耗UTXO集。

| Tip | What comes first? Inputs or outputs, the chicken or the egg? Strictly speaking, outputs come first because coinbase transactions, which generate new bitcoin, have no inputs and create outputs from nothing. |
| :--- | :--- |



#### Transaction Outputs
每一笔比特币交易都有输出outputs，并记录在比特币账本上。所有的输出都会创建可花销的比特币记录成为UTXO，然后会被整个网络确认，然后其所有者就可以在后续交易中使用了。
交易输出由两部分组成：
* 比特币数值，由最小单温satoshi计价
* 一串加密信息，决定着支配该笔比特币的必要条件
加密串也被称为锁定脚本(locking script)，__见证脚本(witness script__)或者scriptPubKey。
Now, let’s look at Alice’s transaction (shown previously in [Transactions—Behind the Scenes](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc#transactions_behind_the_scenes)) and see if we can identify the outputs. In the JSON encoding, the outputs are in an array (list) named vout:
现在我们再回头看看Alice的交易记录，看看能不能定位出交易输出。在json格式下，outputs即为vout属性下的数组信息：
```js
"vout": [
  {
    "value": 0.01500000,
    "scriptPubKey": "OP_DUP OP_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 OP_EQUALVERIFY
    OP_CHECKSIG"
  },
  {
    "value": 0.08450000,
    "scriptPubKey": "OP_DUP OP_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP_EQUALVERIFY OP_CHECKSIG",
  }
]
```
可以看到有两部分组成，value和scriptPubKey，Bitcoin Core这里是按btc显示的，但在交易本身，是按satoshi计价的
The topic of locking and unlocking UTXO will be discussed later, in [Script Construction (Lock + Unlock)](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc#tx_lock_unlock). The scripting language that is used for the script in scriptPubKey is discussed in [Transaction Scripts and Script Language](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc#tx_script). But before we delve into those topics, we need to understand the overall structure of transaction inputs and outputs.


![image.png | center | 752x429](https://cdn.yuque.com/lark/2018/png/a92ce755-0355-4663-87fe-396a6c7ba28d.png "")



#### Transaction serialization—outputs
当交易信息在网络中传输或者在应用间交换时，它们会被序列化(序列化 (Serialization)将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对象将其当前状态写入到临时或持久性存储区。以后，可以通过从存储区中读取或反序列化对象的状态，重新创建该对象)，
Table 1. Transaction output serialization

| Size | Field | Description |
| :--- | :--- | :--- |
| 8 bytes (little-endian) | Amount | Bitcoin value in satoshis (10^-8^ bitcoin) |
| 1–9 bytes (VarInt) | Locking-Script Size | Locking-Script length in bytes, to follow |
| Variable | Locking-Script | A script defining the conditions needed to spend the output |

多数比特币库和框架不会将交易信息存储为字节流，因为这会导致每次需要访问一个字段时都经过复杂的解析过程。方便起见，比特币框架都直接以基于对象的数据结构存储着数据(json).

反序列化或者交易解析即将字节流转换为可读的数据结构的过程。序列化则是指将数据结构转换为字节流以便网络传输或者哈希或者存储到硬盘。大多数比特币库都有内置的序列化和反序列化方法。
#### Transaction Inputs
交易输入是确定哪个UTXO将被消费，附带提供能证明owner身份的解锁脚本(unlocking script)。
To build a transaction, a wallet selects from the UTXO it controls, UTXO with enough value to make the requested payment. Sometimes one UTXO is enough, other times more than one is needed. For each UTXO that will be consumed to make this payment, the wallet creates one input pointing to the UTXO and unlocks it with an unlocking script.
在创建交易时，钱包会从它能访问的UTXO中选择足够额度的UTXO来请求支付。有时候一个UTXO就足够了，有时则需要不止一个。对选中的每个UTXO，钱包会创建一笔交易输入指向它，并用解锁脚本解锁。

仔细解读下交易输入input：第一部分是一个指针，通过引用交易的哈希值和具体output的索引指向唯一的UTXO；第二部分则是解锁脚本，这是钱包构造的，来验证满足UTXO中的支配条件。通常，解锁脚本是由电子签名和公钥组成，能证明所有权的，但也不是所有解锁脚本都包含签名；第三部分，即序列号，这个后面会提到。


比如在Alice的交易信息中，vin部分则为交易输入：
```js
"vin": [
  {
    "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",
    "vout": 0,
    "scriptSig" : "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d172787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",
    "sequence": 4294967295
  }
]
```
As you can see, there is only one input in the list (because one UTXO contained sufficient value to make this payment). The input contains four elements:
* A transaction ID, referencing the transaction that contains the UTXO being spent
* An output index (vout), identifying which UTXO from that transaction is referenced (first one is zero)
* A scriptSig, which satisfies the conditions placed on the UTXO, unlocking it for spending
* A sequence number (to be discussed later)

可以看到，输入中只有一条记录，因为这一个UTXO足够支付了。input有以下四个元素：
* 一个交易id，指向足够用来支付的UTXO
* 一个output的索引，确定这笔交易中具体的某个UTXO(从0开始索引)
* scriptSig，电子签名相关的，来满足UTXO的条件认证
* 序列号(后续讨论)
在Alice的交易中，input中的指针指向交易id为:
```
7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18
```

然后具体output的索引为0 (i.e., the first UTXO created by that transaction). __The unlocking script is constructed by Alice’s wallet by first retrieving the referenced UTXO, examining its locking script, and then using it to build the necessary unlocking script to satisfy it__.

[https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc)

更多交易中签名及验签的算法细节，请阅读原文，这里我要进入下一章了。

第七章  [https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch07.asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch07.asciidoc)
讲衍生出的高级交易形态和实现，暂时略过

## 第八章 [https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc)
The Bitcoin Network
### Peer-to-Peer Network Architecture
比特币网络是架构在因特网之上的一个p2p网络。p2p意味着网络中的节点彼此是对等的，相同的，没有特殊节点。所有节点都担负着提供服务的任务。在平铺的网状结构中，没有层级关系，没有中心服务器及中心节点。

比特币是点对点的电子现金系统，因此网络架构既是这种系统的反应也是基础。分布式的控制权是设计的核心原则，而这又只能由一个平铺的、分布式的P2P共识网络来完成。
比特币网络特指网络中运行比特币P2P协议的节点构成的集合。除了比特币P2P协议，还有例如Stratum这种用来挖矿和钱包的协议。
### Node Types and Roles
虽然节点是对等的，他们的角色基于它们的功能会有所不同，比如有路由、区块链数据库、挖矿、钱包等。
一个全节点是拥有所有这些功能的节点
![image.png | center | 403x370](https://cdn.yuque.com/lark/2018/png/2075/1522070342392-1da31af6-6153-41fb-857e-73d85494d0f2.png "")

所有的节点都至少拥有路由功能。所有节点会校验和传播交易和区块，并发现和维护节点间的连接。

有些节点会维护完整的最新的区块链数据，因而称为full node全节点。能够自治的权威的进行交易验证。有些节点只维护了部分区块链信息，通过SPV(simplified payment verification)的方式来验证交易，这些节点称为SPV节点或者轻节点。
挖矿节点竞争来产生新的区块，通过专门的硬件和PoW算法。有些挖矿节点是全节点，而有些是轻节点，轻节点会参与到矿池中，矿池会维护一个全节点，从而辅助轻节点验证交易。

用户钱包现在一般都是SPV节点了

### The Extended Bitcoin Network
The main bitcoin network, running the bitcoin P2P protocol, consists of between 5,000 and 8,000 listening nodes running various versions of the bitcoin reference client (Bitcoin Core) and a few hundred nodes running various other implementations of the bitcoin P2P protocol, such as Bitcoin Classic, Bitcoin Unlimited, BitcoinJ, Libbitcoin, btcd, and bcoin. A small percentage of the nodes on the bitcoin P2P network are also mining nodes, competing in the mining process, validating transactions, and creating new blocks. Various large companies interface with the bitcoin network by running full-node clients based on the Bitcoin Core client, with full copies of the blockchain and a network node, but without mining or wallet functions. These nodes act as network edge routers, allowing various other services (exchanges, wallets, block explorers, merchant payment processing) to be built on top.


### Bitcoin Relay Networks
虽然比特币的点对点网络满足了各种类型节点的常规需求，但它固有的高延时对有特殊需求的挖矿节点来说不是好事。
矿机们在延展区块链时所参与的PoW竞争是时间敏感的。因此矿机需要尽可能的缩短与胜出节点之间的网络传输，才能尽早的开始下一区块的竞争。即，在挖矿方面，网络延时直接和利润相关。
一个比特币中继网络即尝试最小化矿机间的网络延时。最初的比特币中继网络[Bitcoin Relay Network](http://www.bitcoinrelaynetwork.org/)是由核心开发者Matt Corallo在2015年开发的，使得矿机之间的区块同步延时很低。该网络有一系列专门的节点(部署在aws上)组成，连接着绝大多数的矿机和矿池。
该网络后来被FIBRE(Fast Internet Bitcoin Relay Engine)替代，同样是他开发的。UDP-based，区块更紧凑，传输的数据更少。
另一个还在提议中的中继网络是Falcon。cut-through-routing instead of store-and-forward。即类似于文件流，收到部分区块数据后就开始传输，而不是等到整个区块接收完整。
中继网络不是比特币网络的替代，而是在其上层提供附加能力的网络。

### Network Discovery
当一个新节点启动后，它必须发现网络中的其它节点一遍参与进去。要开始这个过程，必须发现至少一个存在的节点，并连接。当然，节点的地理位置是无关的，所以，可以随机选择连接节点。
要连接到已存在的节点，必须建立tcp连接到端口8333。连接时，需要握手协议(see [The initial handshake between peers](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc#network_handshake))，即通过传输版本消息，这消息包含如下信息：
* nVersion 
    * The bitcoin P2P protocol version the client "speaks" (e.g., 70002)
* nLocalServices 
    * A list of local services supported by the node, currently just NODE\_NETWORK
* nTime
    * The current time
* addrYou
    * The IP address of the remote node as seen from this node
* addrMe
    * The IP address of the local node, as discovered by the local node
* subver
    * A sub-version showing the type of software running on this node (e.g., `/Satoshi:0.9.2.1/`)
* BestHeight
    * The block height of this node’s blockchain
(See [GitHub](http://bit.ly/1qlsC7w) for an example of the version network message.)
这个版本消息会是节点间交互的第一个消息。接收此消息的节点会检查发起连接节点的NVersion，看看是否兼容，如果兼容，则会发送应答并建立连接。

![image.png | center | 752x739](https://cdn.yuque.com/lark/2018/png/2075/1522079964229-f29b043a-e7be-4899-b2c4-a5f45f8f2341.png "")


那么一个新节点是如何发现现有节点的呢？一种方法是查询DNS节点(DNS seeds)，这些服务器会提供比特币节点的ip地址。
Alternatively, a bootstrapping node that knows nothing of the network must be given the IP address of at least one bitcoin node, after which it can establish connections through further introductions. The command-line argument -seednode can be used to connect to one node just for introductions using it as a seed. After the initial seed node is used to form introductions, the client will disconnect from it and use the newly discovered peers.
Once one or more connections are established, the new node will send an addr message containing its own IP address to its neighbors. The neighbors will, in turn, forward the addr message to their neighbors, ensuring that the newly connected node becomes well known and better connected. Additionally, the newly connected node can send getaddr to the neighbors, asking them to return a list of IP addresses of other peers. That way, a node can find peers to connect to and advertise its existence on the network for other nodes to find it.
 
[Address propagation and discovery](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc#address_propagation) shows the address discovery protocol.

![image.png | center | 568x550](https://cdn.yuque.com/lark/2018/png/2075/1522079986467-c85374bc-09c3-4dbf-aad7-8f8b64f31a3d.png "")

为了有丰富的路径接入比特币网络，一个节点必须连接到不通的节点；而这些路径并不可靠，因为节点随来随去，因此节点必须持续发现新节点并帮助其它新进节点完成启动。
启动只需要连接到一个节点即可，因为这个节点可以介绍其它更多的节点。
启动后，节点会记住与它最近连接最成功的节点，一遍下次启动时再去连接，当然，如果还是遇到了连接不上，那么就会从种子节点出发完成启动。
On a node running the Bitcoin Core client, you can list the peer connections with the command getpeerinfo
想覆写节点发现的自动化过程的话，可以通过 -connect=<IPAddress> 这个选项指定一些IP，则会只和这些IP连接。
如果连接间没有数据传输，节点间会周期发送消息来保持连接
如果节点在90分钟内没有任何交流，则可以认为该节点已失去连接，可以寻找一个新节点了。
如此，网络就可以动态调整，在没有中心控制的机制下收放自如。
### Full Nodes
全节点即为维护有整个区块链的所有交易数据的节点。
在早些年，比特币网络中的所有节点都是全节点，当下则Bitcoin Core client是全节点。在过去两年，出现了轻节点，它们不用维护整个区块链的数据，接下来介绍。
全节点维护着整个区块链从一开始到现在的所有交易数据，因此它们可以独立的构建和验证，从创世区块一直到最近的区块。
它们不需要依赖其它节点来完成权威验证过程，但是依赖网络来接收新的交易区块，然后验证完成并整合进本地数据库。
全节点有各种语言的不同实现，但目前使用最多的还是Bitcoin COre，称为Satoshi client。
### Exchanging "Inventory"
一个全节点在连接上同僚后，要做的第一件事就是尝试创建完整的区块链。
如果是个全新的节点，即没有区块链，那么它只有一个块，即创世区块，这是静态嵌入在客户端中的。从序列0开始，新的节点将从网络同步中下载成千上万的区块，并重新建立起完整的区块链。
同步区块链的过程从版本消息开始，因为消息中有一个BestHeight(当前区块数量)。
一个节点会从同僚的版本消息中看到它们都各自有多少区块，并能和自己所有的区块链进行比较。
节点间会交换getblocks消息，该消息包含节点各自最新区块的hash，然后其中一个节点就能够识别该hash不是最新的，从而确定自己拥有的区块链比它同僚的区块链要长。
拥有更长链的节点能够知道其它节点需要哪些区块来保持最新。它会识别差异中最早的500个块，并将它们的hash通过inv(inventory)消息发送出去，那些缺失这些块的节点就会接受这个消息(发送getdata消息来请求区块数据，并用inv消息中的hash来验证这些区块)。
例如，假设一个节点只有创世区块，它会从它的同僚中收到inv消息包含有接下来的500个区块，然后它会从与它连接的同僚节点中请求这些区块(会负载均衡的方式去请求)，该节点会跟踪记录有多少区块在一个连接的传输中，避免请求超出限制MAX\_BLOCKS\_IN\_TRANSIT\_PER\_PEER。
随着接收到每个区块，它会追加到自己本地区块链上。随着本地区块链逐步建立，更多的节点会被请求和接收到，直到和网络中其它节点保持数据同步。

### Simplified Payment Verification (SPV) Nodes
不是所有节点都有能力存储完整的区块链，许多节点运行在空间和电力受限的设备比如手机平板嵌入式设备等。对这些设备，SPV方法被使用来让它们可以在不拥有完整区块链的情况下运作。这种类型的客户端称之为SPVclient或者轻客户端。随着比特币热潮，SPV节点称为了最常见的节点，特别是比特币钱包。
SPV节点只下载区块头信息，不下载区块中的交易信息，如此，所需空间比完整区块链少了1000倍。SPV节点由于没有完整的UTXO，不能独立的知道还剩多少可以支出，于是使用了一种不同的方法，即需要依赖同僚节点来提供相关信息。
<span data-type="color" style="color:#F5222D;"><strong>一个恰当的比喻：</strong></span>
一个全节点就像装备了完整的详细地图的旅行者来到一座陌生的城市。SPV节点则像陌生城市中只知道一条主路而其它街道信息得随机在街上找人问路的旅行者。
虽然，两者都能够以实地访问具体某条街道的方式去验证这条街道是否存在，没有地图的旅行者完全不知道主道两边都有些什么街道。即使站在23号教堂街前，他也不知道是否还有其它的23号教堂街存在，对他而言，最好的机会就是问足够多的人得到足够多的信息确认，并希望这些人中没多少人在骗他。
SPV verifies transactions by reference to their *depth* in the blockchain instead of their *height*. Whereas a full blockchain node will construct a fully verified chain of thousands of blocks and transactions reaching down the blockchain (back in time) all the way to the genesis block, an SPV node will verify the chain of all blocks (but not all transactions) and link that chain to the transaction of interest.
SPV节点验证交易的方式是参考交易在区块链中的深度depth而不是高度height。
当全节点需要构建一条完整的验证链(包括区块和交易数据)直到创世区块，SPV节点则只需验证区块数据，然后将相关的交易数据在关联到链上。。。。(这里没懂，看下面例子)
例如，当需要验证区块300,000中的一条交易时，全节点会将所有300,000个区块都连接起来，并构建一个完整的UTXO数据库，验证该笔交易对应的UTXO还没被支出。
然后SPV节点则没法验证该交易对应的UTXO是否被支出，它需要在交易数据和包含该交易的区块间建立连接(merkle path)。然后，SPV节点等待直到有6个新的区块追加到当前区块上300,001 through 300,006，则从侧面可以确认当前区块是有效的。The fact that other nodes on the network accepted block 300,000 and then did the necessary work to produce six more blocks on top of it is proof, by proxy, that the transaction was not a double-spend.

An SPV node cannot be persuaded that a transaction exists in a block when the transaction does not in fact exist.The SPV node establishes the existence of a transaction in a block by requesting a merkle path proof and by validating the Proof-of-Work in the chain of blocks. However, a transaction’s existence can be "hidden" from an SPV node. An SPV node can definitely prove that a transaction exists but cannot verify that a transaction, such as a double-spend of the same UTXO, doesn’t exist because it doesn’t have a record of all transactions. This vulnerability can be used in a denial-of-service attack or for a double-spending attack against SPV nodes. To defend against this, an SPV node needs to connect randomly to several nodes, to increase the probability that it is in contact with at least one honest node. This need to randomly connect means that SPV nodes also are vulnerable to network partitioning attacks or Sybil attacks, where they are connected to fake nodes or fake networks and do not have access to honest nodes or the real bitcoin network.

从实用性来讲，网络连接通畅的SPV节点安全性是足够的，这是在资源消耗与安全之间的一种平衡。但是要万无一失的保障安全的话，全节点是不会失败的。

| Tip | A full blockchain node verifies a transaction by checking the entire chain of thousands of blocks below it in order to guarantee that the UTXO is not spent, whereas an SPV node checks how deep the block is buried by a handful of blocks above it. |
| :--- | :--- |



SPV节点使用getheaders消息(而不是getblocks)来获取区块头。响应节点一次可以返回2000个区块头，这过程和全节点获取所有区块数据的过程是一样的。SPV节点还会设置一个filter来过滤应答节点发送的未来区块流和交易数据。用getdata请求来获取感兴趣的交易数据，应答的同僚节点则生成一个tx消息，包含所需交易信息。
由于SPV节点只取用户相关的交易数据，如果网络被第三方监控，通过跟踪这些被请求的交易数据，就可能关联获取到用户的比特币地址，从而一定程度上泄露用户隐私。
SPV节点之后不久，开发者新加了一个特性叫做bloom filters来解决隐私风险。Bloom filters允许SPV节点接收所有交易数据的一个子集，而不是特定关心的交易数据（through a filtering mechanism that uses probabilities rather than fixed patterns）。
### Bloom Filters
一个Bloom filter是一个概率搜索过滤器，一种描述一个想要的模式，但又不需要明确指明的方法。
Bloom filters提供了一种高效的途径，能够在保护隐私的同时表达一种搜索模式。
它们被SPV节点用来请求符合特定模式的交易数据，而同时不会泄露具体哪个地址或者交易是它们想要的。

在我们先前的比喻中，没有地图信息的游客在询问一个具体地址23号教堂大街的方位如果他向陌生人询问这个地点，则在不经意间泄露了他的目的地。
如果使用bloom filter，则会类似于问，“有不有地方地名是以U-R-C-H结尾的呢？”
这样提问，就会比直接问一个具体的地址泄露更少的目的地信息。
__通过调整搜索的精确度，游客就可以控制泄露信息的多少，当然结果也会变得没那么精确了。__
__Bloom filters就是为SPV节点提供了这样一种功能。__

#### How Bloom Filters Work
Bloom filters are implemented as a variable-size array of N binary digits (a bit field) and a variable number of M hash functions. T__he hash functions are designed to always produce an output that is between 1 and N, corresponding to the array of binary digits__. The hash functions are generated __deterministically__, so that any node implementing a bloom filter will always use the same hash functions and get the same results for a specific input. By choosing different length (N) bloom filters and a different number (M) of hash functions, the bloom filter can be tuned, varying the level of accuracy and therefore privacy.

In [An example of a simplistic bloom filter, with a 16-bit field and three hash functions](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc#bloom1), 例子中我们使用16bit的数组和3个hash函数来做演示。

bloom filter初始化时，bit数组中所有为都是0，要添加一个模式到bloom filter时，这个模式依次被每个hash函数计算哈希，哈希的结果是1到N(N为数组长度)的数字，响应的数组中该位则会被置为1，从而记录着hash函数的输出。这样直到所有的M个哈希函数都被计算完毕，则该搜索模式就被记录在位数组中了。
[Adding a pattern "A" to our simple bloom filter](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc#bloom2) is an example of adding a pattern "A" to the simple bloom filter shown in [An example of a simplistic bloom filter, with a 16-bit field and three hash functions](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc#bloom1).
再次添加一个模式到Bloom filter的过程同上，模式会被所有函数依次哈希计算，然后结果会以相应位被置1的方式被记录在位数组中。
注意到随着bloom filter被添加越多的搜索模式，hash的结果会发生碰撞，即一个位可能多次被置1，即不变。
本质上，随着越多的模式重叠发生越多，bloom filter会越来越饱和，然后搜索的精确性就随之降低。
这就是为什么Bloom filter是一种概率数据结构了，随着越多的模式加入，filter会越发不精准。
The accuracy depends on the number of patterns added versus the size of the bit array (N) and number of hash functions (M). A larger bit array and more hash functions can record more patterns with higher accuracy. A smaller bit array or fewer hash functions will record fewer patterns and produce less accuracy. ---这里没懂，按上面的说法，M个哈希函数都要参与计算的，M越大，则会越多的位被置1，从而导致精度丢失才对啊。


要测试一个模式是否是bloom filter的一部分，可以将模式通过M个哈希函数计算，然后和位数组匹配，如果所有相关的位都置1了，那么我们认为这个模式很可能是bloom filter的一部分，这是有概率的。
反过来，要测试一个模式不属于filter的一部分则是确定性的

### How SPV Nodes Use Bloom Filters
Bloom filters 被SPV节点用来在从同僚节点接收交易数据时进行过滤，只选择感兴趣的交易数据，但同时又不会泄露具体感兴趣的地址。
An SPV node will initialize a bloom filter as "empty"; in that state the bloom filter will not match any patterns. The SPV node will then make a list of all the addresses, keys, and hashes that it is interested in. It will do this by extracting the public key hash and script hash and transaction IDs from any UTXO controlled by its wallet. The SPV node then adds each of these to the bloom filter, so that the bloom filter will "match" if these patterns are present in a transaction, without revealing the patterns themselves.
就是SPV节点会利用自己钱包所控制的所有UTXO数据来提取出地址、keys、hash等，再添加到搜索模式中，然后以这些模式去全节点中取数据，这样就避免了泄露单一地址信息。
![image.png | center | 639x262](https://cdn.yuque.com/lark/2018/png/2075/1522203509800-8c770380-2e4f-4399-a44a-4fe2c14ddb94.png "")
![image.png | center | 622x349](https://cdn.yuque.com/lark/2018/png/2075/1522205098672-3b0306cc-3ead-4921-b310-682bc331d919.png "")
![image.png | center | 728x409](https://cdn.yuque.com/lark/2018/png/2075/1522205168890-bcf21f7d-93fe-430b-a4b2-5a1c46e5f6c9.png "")
![image.png | center | 752x440](https://cdn.yuque.com/lark/2018/png/2075/1522206317946-b601cfda-0dec-4d90-bfae-c44baf060a88.png "")
![image.png | center | 752x437](https://cdn.yuque.com/lark/2018/png/2075/1522206384207-bb6e3236-3cac-45b1-bf25-ac8d69ff9bc0.png "")

SPV节点将会发送一条filterload消息到同僚节点，该消息即包含bloom filter。
然后，同僚节点在发送交易数据时就会用此bloom filter来进行检查过滤了。
全节点会根据bloom filter来检查交易数据的如下几部分：
* 交易ID
* 每条交易输出的locking script的数据结构，每个key和hash
* 每条交易输入
* 每条交易输入的签名结构

> By checking against all these components, bloom filters can be used to match public key hashes, scripts, OP\_RETURN values, public keys in signatures, or any future component of a smart contract or complex script.

通过检查这些部分，bloom filters就能够用来匹配公钥hash、scripts、OP\_RETURN values等等

在filter建立起来后，同僚节点会将每笔交易的输出和filter进行比较，只有匹配filter的交易才会被发送给请求节点。
在回应节点发出的getdata消息时，同僚节点将发送一条merkleblock消息，这消息只包含匹配区块的__区块头信息__以及匹配交易的merkle path信息。然后同僚节点还会发送一条tx消息，包含着匹配到的交易数据。
全节点将交易数据发送给SPV节点时，SPV节点会丢弃不相关的，然后使用相关的交易数据来建立更新自己的UTXO集合和钱包收支数据，随着它更新自己数据集的同时，SPV节点可以动态调整bloom filter来进行后面的数据获取工作，如此这般。
SPV节点可以通过发送filteradd消息来动态的添加搜索匹配模式。也可以使用filterclear消息来清除filter。因为bloom filter是不能去掉其中一个模式的，所以，只能先clear，再添加的方式运作。
The network protocol and bloom filter mechanism for SPV nodes is defined in [BIP-37 (Peer Services)](http://bit.ly/1x6qCiO).
### Encrypted and Authenticated Connections
大多数比特币新用户以为比特币节点的通讯是加密的。实际上最初的比特币实现中，通讯是明文的。这对全节点来说没啥隐私问题，但对SPV来说却是个大问题。
为了增加比特币网络的隐私和安全性，有两种加密通讯方案：*Tor Transport* and *P2P Authentication and Encryption* with BIP-150/151
。。。。这两种没详细说。。。。


### Transaction Pools
几乎比特币上的所有节点都维护着一个暂时的未确认交易数组，称为mempool或transaction pool。节点用这个池子来记录着网络感知到但还没包含到一个区块中的交易。例如，钱包节点会使用这个池子中的交易数据来跟踪用户钱包接收到的但还未确认的资金情况。
随着交易信息被接受和确认，它们被追加到transaction pool中，并中继给其它相邻节点再传播到整个网络。
Some node implementations also maintain a separate pool of orphaned transactions. If a transaction’s inputs refer to a transaction that is not yet known, such as a missing parent, the orphan transaction will be stored temporarily in the orphan pool until the parent transaction arrives.

When a transaction is added to the transaction pool, the orphan pool is checked for any orphans that reference this transaction’s outputs (its children). Any matching orphans are then validated. If valid, they are removed from the orphan pool and added to the transaction pool, completing the chain that started with the parent transaction. In light of the newly added transaction, which is no longer an orphan, the process is repeated recursively looking for any further descendants, until no more descendants are found. Through this process, the arrival of a parent transaction triggers a cascade reconstruction of an entire chain of interdependent transactions by re-uniting the orphans with their parents all the way down the chain.
Both the transaction pool and orphan pool (where implemented) are stored in local memory and are not saved on persistent storage; rather, they are dynamically populated from incoming network messages. When a node starts, both pools are empty and are gradually populated with new transactions received on the network.
Some implementations of the bitcoin client also maintain an UTXO database or pool, which is the set of all unspent outputs on the blockchain. Although the name "UTXO pool" sounds similar to the transaction pool, it represents a different set of data. Unlike the transaction and orphan pools, the UTXO pool is not initialized empty but instead contains millions of entries of __unspent transaction outputs__, __everything that is unspent from all the way back to the genesis block__. The UTXO pool may be housed in local memory or as an indexed database table on persistent storage.
Whereas the transaction and orphan pools represent a single node’s local perspective and might vary significantly from node to node depending upon when the node was started or restarted, the UTXO pool represents the emergent consensus of the network and therefore will vary little between nodes. Furthermore, the transaction and orphan pools only contain unconfirmed transactions, while the UTXO pool only contains __confirmed__ outputs.


## 第九章 区块链
### Introduction
区块链是一种数据结构，有序，然后每个指向其前一个(back-linked)的由交易数据组成的块。可以存储在文件中，或者数据库中。Bitcoin Core client是将区块链信息存储在levelDB中的。
区块链通常被形象想象为垂直堆栈模型，因为每个块建立在其它块之上，创世区块为栈底；也正是这种类比，因此用height来形容区块离第一个块的距离，top或tip则指代最新添加的那个区块。
区块链上的每一个区块都是由一个hash值唯一确定，该hash由sha256计算区块头得来。每个区块同样会通过区块头中的"previous block hash"字段指向前一个区块，即父区块。换言之，每个区块在其头部包含着其父区块的hash，如此形成一条链，直到最初的创世区块。
虽然每个块只能有一个父块(因为只有一个previous block hash字段)，但可能会短暂的出现多个子块的情况，这种情况会发生在blockchain fork阶段，但是最终，fork会被解决，链不会形成分叉。
previous block hash字段由于存在区块头中，因此会影响当前块的hash值。
于是，如果父块的hash身份发生改变，则子块需要跟着变，依次会形成级联效应。
这种级联关系保证了一旦某个区块产生了很多代子区块，如果要改变它，则需要强制重新计算其后代所有区块，而区块链的机制决定这种计算是需要消耗不可估计的计算力和能量的，从而意味着区块链上的历史区块几乎不能改变，这是区块链的一个重要特性。

可以将区块链想象成地质结构中的不同层次，表层可能会随着季节变化而变化，甚至可能在还没完全落定时被狂风吹走。但是一旦往下深入几层，地质结构就会越来越稳定。当你往下看几百英尺时，你也能看到已经存在了几百万年的地址快照。
在区块链中，最新添加的几个区块可能会在有分支出现的情况下重新被计算和修订。最上面的6个区块就像地质结构中的表层。一旦你的深度超过了6个区块，则越来越不太可能发生变动了。

### Structure of a Block
一个区块是一个容器数据结构，负责整合交易数据并添加到区块链上去。
区块由区块头(包含metadata)和紧接着的一序列交易数据组成，头部有80字节，而平均一条交易有400字节，平均一个区块可以包含1900条交易。因此，一个完整的区块会比单纯的一个区块头大差不多一万倍。

| Size | Field | Description |
| :--- | :--- | :--- |
| 4 bytes | Block Size | The size of the block, in bytes, following this field |
| 80 bytes | Block Header | Several fields form the block header |
| 1–9 bytes (VarInt) | Transaction Counter | How many transactions follow |
| Variable | Transactions | The transactions recorded in this block |


### Block Header
区块头由三组meta数据组成。首先是指向前一个区块的hash，此值将本区块和前一区块相连；
第二部分是__difficulty、timestamp、nonce，这和挖矿竞争有关__；
第三部分是__merkle tree root__，用来高效归总区块中所有交易数据的数据结构。

| Size | Field | Description |
| :--- | :--- | :--- |
| 4 bytes | Version | A version number to track software/protocol upgrades |
| 32 bytes | Previous Block Hash | A reference to the hash of the previous (parent) block in the chain |
| 32 bytes | Merkle Root | A hash of the root of the merkle tree of this block’s transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | Difficulty Target | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | Nonce | A counter used for the Proof-of-Work algorithm |

The nonce, difficulty target, and timestamp are used in the mining process and will be discussed in more detail in [[mining]](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch09.asciidoc#mining).

### Block Identifiers: Block Header Hash and Block Height
一个区块的主要标识符是它的hash或叫做电子指纹，区块头通过sha256两次计算得来。
结果产生的32字节hash值称为block hash，或者更精确的block header hash。比如创世区块的hash是： 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f。
区块的hash唯一确定一个区块，但是注意到，block hash实际并不包含在区块的数据结构中，既不会在区块传输中存在，也不会在持久化存储中作为区块链的一部分。它只是在每个节点接收到该区块是计算得到，block hash一般会被分开存储在节点的数据库中，作为该区块的metadata的一部分，用来辅助索引和快速提取区块等工作。
区块的另外一种标识是它在区块链中的位置序号，称为block height。创世区块的height是0，后续添加则序号依次加1.
与block hash不同，区块高度不是唯一标识符。虽然一个区块总会对应一个块高，但是反过来则不同，即一个块高并不总是只对应一个区块。在有分支的情况下回出现。
同样的，块高也不是区块数据结构中的一部分。

| Tip | A block’s *block hash* always identifies a single block uniquely. A block also always has a specific *block height*. However, it is not always the case that a specific block height can identify a single block. Rather, two or more blocks might compete for a single position in the blockchain. |
| :--- | :--- |



### The Genesis Block
创世区块是2009年创建的。是区块链上所有区块的共同祖先，即不管你从链上那个区块开始，顺着链往父节点找，总能达到创世区块。
每个节点都会一开始都会至少拥有一个区块，即创世区块，因为它是硬编码的，不可变更。所有节点都知道创世区块的hash和结构，被创建的时间，甚至里面的唯一一条交易。
See the statically encoded genesis block inside the Bitcoin Core client, in [chainparams.cpp](http://bit.ly/1x6rcwP).
The following identifier hash belongs to the genesis block:
```
000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
```

You can search for that block hash in any block explorer website, such as *blockchain.info*, and you will find a page describing the contents of this block, with a URL containing that hash:
[https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f](https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f)
Using the Bitcoin Core reference client on the command line:
![image.png | center | 572x1239](https://cdn.yuque.com/lark/2018/png/2075/1522293191538-1fc7d501-0143-4684-b9a8-e67c42fce388.png "")
![image.png | center | 602x301](https://cdn.yuque.com/lark/2018/png/2075/1522301729156-7c80f8bf-7275-4f24-9d47-2eb9a89500d0.png "")
![image.png | center | 616x308](https://cdn.yuque.com/lark/2018/png/2075/1522301969419-ad7d182c-7b41-4515-9002-e9a8d7dc2632.png "")
![image.png | center | 752x122](https://cdn.yuque.com/lark/2018/png/2075/1522644317825-5afb7c45-fedd-4825-911a-4392a45507b9.png "")
![image.png | center | 752x289](https://cdn.yuque.com/lark/2018/png/2075/1522644004068-5a4362ff-38fd-4749-b079-9aa169e65c3e.png "")
![image.png | center | 752x313](https://cdn.yuque.com/lark/2018/png/2075/1522644743540-d963db49-f4e3-4a7d-8030-64391ffc6ee2.png "")
![image.png | center | 494x311](https://cdn.yuque.com/lark/2018/png/2075/1522654918622-8251e840-d4c9-4774-a342-832cfba3c01a.png "")

[A merkle path used to prove inclusion of a data element](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch09.asciidoc#merkle_tree_path)[A merkle path used to prove inclusion of a data element](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch09.asciidoc#merkle_tree_path)
[Duplicating one data element achieves an even number of data elements](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch09.asciidoc#merkle_tree_odd)
```powershell
$ bitcoin-cli getblock 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
```

```json
{
    "hash" : "000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f",
    "confirmations" : 308321,
    "size" : 285,
    "height" : 0,
    "version" : 1,
    "merkleroot" : "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",
    "tx" : [
        "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b"
    ],
    "time" : 1231006505,
    "nonce" : 2083236893,
    "bits" : "1d00ffff",
    "difficulty" : 1.00000000,
    "nextblockhash" : "00000000839a8e6886ab5951d76f411475428afc90947ee320161bbf18eb6048"
}
```

### Linking Blocks in the Blockchain
全节点维护者区块链的完全拷贝。这份本地的拷贝数据会随着在网络中发现新区块而被频繁更新，并追加新区块到链上。当节点收到来自网络的新区块时，它会校验这些区块，并把它们链接到区块链上。为了建立正确的链接，节点会检查接收到的区块的头部信息，找到‘previous block hash’字段。

假如，一个节点上的区块链有277,314个区块，则最后一个区块是277,314，它的block hash是：
```
00000000000000027e7ba6fe7bad39faf3b5a83daed765f05f7d1b71a1632249
```

然后这个节点从网络中接收了一个新区块：
```json
{
    "size" : 43560,
    "version" : 2,
    "previousblockhash" :
        "00000000000000027e7ba6fe7bad39faf3b5a83daed765f05f7d1b71a1632249",
    "merkleroot" :
        "5e049f4030e0ab2debb92378f53c0a6e09548aea083f3ab25e1d94ea1155e29d",
    "time" : 1388185038,
    "difficulty" : 1180923195.25802612,
    "nonce" : 4215469401,
    "tx" : [
        "257e7497fb8bc68421eb2c7b699dbab234831600e7352f0d9e6522c7cf3f6c77",

 #[... many more transactions omitted ...]

        "05cfd38f6ae6aa83674cc99e4d75a1458c165b7ab84725eda41d018a09176634"
    ]
}
```

通过解析这个区块，节点发现它的previousblockhash字段的值在链上是有的，且正好为最后一个块，因此，接收到的新块即为277,314块的子块，于是接收到的区块会追加到链上，称为277,315块。

### Merkle Trees
链上的每个区块通过merkle tree来包含该块内的所有交易数据的摘要。
一个merkle tree，又称为二进制hash树(a binary hash tree)，是一种用来快速摘要和验证大数据集完整性的数据结构。
__Merkle tree在比特币中被用来摘要一个区块中的所有交易数据，生成一个总体的电子指纹，提供了一种非常高效的验证一条交易是否被区块包含的途径__。Merkle tree是通过递归的hash一对节点，直到收敛为一个hash节点，即merkle root。用到的加密hash算法是sha256两次计算，即double-sha256.
> When N data elements are hashed and summarized in a merkle tree, you can check to see if any one data element is included in the tree with__ at most 2\*log~2~(N) calculations__, making this a very efficient data structure.

Merkle tree是从底部开始构建的，在下面的例子中，我们从4笔交易开始，A、B、C、D，这四笔交易则为merkle tree的叶子节点，交易数据本身不存在merkle tree中，而是它们的hash值，则，最终的4个叶子节点为H~A~, H~B~, H~C~, and H~D：~
```
HA = SHA256(SHA256(Transaction A))
```
连续的两个节点再组队，hash值拼接起来，继续double-hash而成父节点：
```
HAB = SHA256(SHA256(HA + HB))
```
这个过程一直执行，直到剩下一个节点，成为merkle root。这个32字节的hash则被存储在区块头中，它是所有交易数据的摘要。

由于merkle tree是二进制树，它需要偶数个叶子节点，如果只有奇数条交易数据，则最后一条交易数据会被复制一份，成为a balanced tree. 

要证明一个指定的交易存在于一个区块中，只需生成log~2~(N)个32字节的hash，这些hash值构成该条交易通向merkle tree root的认证路径或者叫做merkle path。这就高级了，可以非常高效的在大量交易数据中定位具体一条交易数据。



> 这部分感觉需要看源码，没明白这个merkleblock message的生成细节。

## 第十章 挖矿和共识机制
### 介绍
挖矿一词有一定的误导性，通过和稀有金属的开采过程联系，挖矿一词会将我们的注意力集中到得到的回报上，即新产生的比特币。
虽然挖矿是靠比特币来激励维持的过程，但它的本质目的并不是激励本身，也不是新币发行。
挖矿是支撑整个比特币这个去中心化清算所的一种机制，通过它来验证和清算交易记录。
挖矿这个发明，使得比特币因之而特别，它是一种去中心化的安全机制，是点对点电子资金系统的基础。

挖矿机制保障着比特币系统的安全性，并使得在没有中央集权的模式下达到全网共识。而挖矿中的新出货币和交易费则是整个过程的激励机制，使得矿工能够甘愿付出来维护整个网络的安全，同时也给系统提供新币的来源。

<div class="bi-table">
  <table>
    <colgroup>
      <col width="95px" />
      <col width="655px" />
    </colgroup>
    <tbody>
      <tr>
        <td rowspan="1" colspan="1">
          <div data-type="p">Tip</div>
        </td>
        <td rowspan="1" colspan="1">
          <div data-type="p">The purpose of mining is not the creation of new bitcoin. That’s the incentive system. Mining is the mechanism by which bitcoin’s <em>security</em> is <em>decentralized</em>.</div>
          <div data-type="p"><span data-type="color" style="color:#F5222D;">挖矿的目的不是开采新币，这属于激励机制的范围。</span></div>
          <div data-type="p"><span data-type="color" style="color:#F5222D;">挖矿本身只是使得比特币系统的安全性能够去中心化的一种机制。</span></div>
        </td>
      </tr>
    </tbody>
  </table>
</div>


矿工会校验新的交易记录，并记录在全球账本中。一个新的区块会包含上个区块开采以后的所有交易记录，新区块差不多每10分钟会被开采出来，而随着这个过程，里面所包含的交易记录就被添加到了区块链上。
交易被添加到区块链上即意味着该交易得到了确认，从而使得从这些交易获取到比特币的所有者能够花销他们的比特币了。
矿工会在挖矿(保障网络安全机制)的过程中得到两类回报：一是随着每个区块被开采时发行的新币，二是该区块的每笔交易过程中所产生的交易费。
为了赢得报酬，矿工竞争来计算一个基于加密哈希算法的数学问题，谁先计算找到答案，则谁获得这些报酬(PoW过程，最终计算出来的值会被包含到新的区块中，它是矿工工作量的证明)，并获取将这些交易记录到区块链上的权利，这就是比特币安全模型的基础。
整个过程之所以称为挖矿，是因为它模拟了自然开采稀有金属的过程，即开采了是逐渐减少的。比特币网络的资金发行是通过挖矿，就类似中央银行发行纸币一样。矿工在成功挖到区块后所能获得的最大量新币额度差不多每四年减半(准确的说是每隔210000区块)，直到2140年后，就不会再由新币产生。

在本章中，我们首先学习挖矿作如何为一种货币供应机制，然后看看挖矿最重要的功能：去中心化共识机制。
#### Bitcoin Economics and Currency Creation
比特币是在每个区块被创建时铸造出来的，稳步缩减。每个区块平均10分钟产生，没210000个区块，大约四年，每次产生新币的数量减半。最初的四年，没产生一个区块，则有新币50btc。
在2012年，新币发行速率缩减到每个区块25btc，在2016年7月缩减到12.5btc，直到差不多2140年后，不会有新币产生，则矿工的奖励只靠交易费来源。

这种有限且紧缩的发行机制创建了固定的货币供应机制来对抗通货膨胀；不像法定货币能够被无限制的由中央银行印制发行。
__通货紧缩的货币__
比特币固有缩减机制导致了它本质上是通货紧缩的。
通货紧缩现象：货币的供需(需求大于供给)不匹配导致价值升值，与通货膨胀相反，价值的通货紧缩意味着资金会随着时间而购买力增强。
于是，许多经济学家争议说通货紧缩的经济是灾难，需要不惜代价的避免。因为在通货紧缩时期，人们会囤积货币而不是花费它们。

> Bitcoin experts argue that deflation is not bad per se. Rather, deflation is associated with a collapse in demand because that is the only example of deflation we have to study. In a fiat currency with the possibility of unlimited printing, it is very difficult to enter a deflationary spiral unless there is a complete collapse in demand and an unwillingness to print money. Deflation in bitcoin is not caused by a collapse in demand, but by a predictably constrained supply.
> The positive aspect of deflation, of course, is that it is the opposite of inflation. Inflation causes a slow but inevitable debasement of currency, resulting in a form of hidden taxation that punishes savers in order to bail out debtors (including the biggest debtors, governments themselves). Currencies under government control suffer from the moral hazard of easy debt issuance that can later be erased through debasement at the expense of savers.
> It remains to be seen whether the deflationary aspect of the currency is a problem when it is not driven by rapid economic retraction, or an advantage because the protection from inflation and debasement far outweighs the risks of deflation.
### Decentralized Consensus
在前一章节，我们了解了区块链(比特币中所有交易记录的全球账本)，网络中所有用户都接受的所有权的权威记录。

但是如何在不能信任任何人的情况下，全网用户能够达成共识，接受唯一一个真理呢？
所有传统支付系统都依赖一个信任模型即中央机构来提供清算服务，即验证并清算所有交易。
比特币网络没有中央机构，但是所有全节点都拥有一份完整的可被信任的全球账本副本。
区块链并不由中央机构创建，而是独立的被网络中各个节点所收集起来。网络中每个节点根据在非安全网络中接收到的信息，能够达成共识，并共同维护着相同的全球账本。
本章会介绍这些共识过程的细节。

<strong>Satoshi Nakamoto’s main invention is the decentralized mechanism for </strong><strong><em>emergent consensus</em></strong><strong>. Emergent, because consensus is not achieved explicitly—there is no election or fixed moment when consensus occurs. Instead, consensus is an emergent artifact of the asynchronous interaction of thousands of independent nodes, all following simple rules. All the properties of bitcoin, including currency, transactions, payments, and the security model that does not depend on central authority or trust, derive from this invention.</strong>
<span data-type="color" style="color:#F5222D;">中本聪的主要发明是 紧急共识的去中心化机制。紧急的意思是，共识不是确定获得的，没有选举或者固定时刻来产生共识。相反，共识是由成千上万的节点都遵守相同的规则来异步交互达成的。比特币网络的所有特性，包括资金、</span>
<span data-type="color" style="color:#F5222D;">交易、支付、不基于中央机构的安全信任模型，都是从这个发明衍生的。</span>

比特币网络的去中心化共识是由四个过程的相互作用产生的，这四个过程在全网所有节点上各自独立运作：
* 独立的交易验证，发生在所有全节点上
* 独立的整合这些交易到新区块中，这由挖矿节点完成，附带着有工作量证明的计算结果
* 独立的新区块验证，及追加到区块链上
* 独立的选择最长链作为最终区块链，这是所有节点参与的。
接下来部分会介绍这些过程。

### Independent Verification of Transactions
在交易章节，我们看到了比特币钱包创建交易的过程：收集UTXO，提供unlocking scripts，然后创建新的outputs来指定新的资产拥有者。然后生成的交易记录会被发送给周围节点然后传播到整个网络。
但是，在分发交易到同僚节点前，每个比特币节点都需要首先验证交易是否有效，这保证了只有合法的交易才会传播到整个网络，而非法的交易会在第一个遇到它的节点中就被丢弃。
交易验证过程是遵循了一长串checklist:
* 交易数据的语法和数据结构正确
* 交易的输入输出都不为空
* 交易记录的大小要小于 MAX\_BLOCK\_SIZE.
* 每笔output以及总和都要小于21m btc，总数
* None of the inputs have hash=0, N=–1 (coinbase transactions should not be relayed).
* nLocktime is equal to INT\_MAX, or nLocktime and nSequence values are satisfied according to MedianTimePast.
* The transaction size in bytes is greater than or equal to 100.
* The number of signature operations (SIGOPS) contained in the transaction is less than the signature operation limit.
* The unlocking script (scriptSig) can only push numbers on the stack, and the locking script (scriptPubkey) must match IsStandard forms (this rejects "nonstandard" transactions).
* __A matching transaction in the pool, or in a block in the main branch, must exist__.
* <span data-type="color" style="color:#F5222D;">For each input, if the referenced output exists in any other transaction in the pool, the transaction must be rejected</span>.(在交易章节我们知道，vin由指定的交易、对应的vout等等组成，这里的意思是，如果当前交易的vin所指定的交易vout还在pool中，即上一笔交易还没被确认，则当前交易是不能有效的)
* For each input, look in the main branch and the transaction pool to find the referenced output transaction. If the output transaction is missing for any input, this will be an __orphan transaction__. Add to the orphan transactions pool, if a matching transaction is not already in the pool.
* For each input, if the referenced output transaction is a coinbase output, it must have at least COINBASE\_MATURITY (100) confirmations.
* For each input, the referenced output must exist and cannot already be spent.（即需要在UTXO集中）
* Using the referenced output transactions to get input values, check that each input value, as well as the sum, are in the allowed range of values (less than 21m coins, more than 0).
* Reject if the sum of input values is less than sum of output values.
* __Reject if transaction fee would be too low (minRelayTxFee) to get into an empty block__.
* The unlocking scripts for each input must validate against the corresponding output locking scripts.

These conditions can be seen in detail in the functions AcceptToMemoryPool, CheckTransaction, and CheckInputs in Bitcoin Core. Note that the conditions change over time, to address new types of denial-of-service attacks or sometimes to relax the rules so as to include more types of transactions.
<strong>By independently verifying each transaction as it is received and before propagating it, every node builds a pool of valid (but unconfirmed) transactions known as the </strong><strong><em>transaction pool</em></strong><strong>, </strong><strong><em>memory pool</em></strong><strong>, or </strong><strong><em>mempool</em></strong><strong>.</strong>
通过节点在传播交易之前的独立验证，节点会维护一个合法的交易数据池，称之为transaction pool、mempool。
### Mining Nodes
比特币网络中有一些专有节点，称之为矿工节点。
在第一章我们介绍了Jing，就是一名比特币矿工，他通过运行专业的硬件设备来挖矿赚取比特币。

Jing的连接到一个全节点，当然还有一些矿工没有全节点(在Mining Pools部分见)，和其它所有全节点一样，JIng的节点会接收并传播网络中所有未确认的交易数据，Jing的节点还会将这些数据整个近新的区块中。
Jing的节点也和其它节点一样，时刻监听网络中新节点的到来。但是，这对矿机来说有着特别的意义。
矿工之间的竞争随着新节点的到来而结束，因为新节点意味着有赢家诞生。
所以，接收到一个有效的区块意味着有人在这轮竞争中胜出了而他们失败了，但是呢，上一轮的结束也同时意味着下一轮竞争的开始。
#### Aggregating Transactions into Blocks
在验证交易合法之后，节点会将交易添加到mempool中，这里所有交易等待被打包进一个区块。Jing的节点同样会收集、验证、并传播所有的交易数据，这功能和所有节点一样，不同于其他节点的是，jing的节点会在合适的阶段将这些交易数据整合进一个候选区块中。
同样以Alice向Bob买咖啡时的交易为例，Alice的交易被包含在277316的区块中，我们假设这个区块被Jing的节点率先打包。
Jing的矿机节点维护着整个区块链的完整副本，在Alice付款时，Jinig的节点刚好集合了277,314个区块，Jing的节点继续监听网络中的交易数据，努力挖掘新的区块，也同时发现被其它节点发掘的新区块。正当Jing的节点在挖矿的同时，它接收到了网络中的第277,315个区块，该区块的到达意味着对区块277,315的竞争结束，也意味着下一区块挖矿竞争的开始。
在过去的十分钟，当Jing的节点在计算区块277,315的答案时，它同时也在收集下一区块所需的交易数据，到此刻，它的mempool中已经收集了几百笔交易。当收到区块277,315，并验证通过之后，Jing的节点会将该区块中的所有交易和它自己的mempool中收集的交易数据匹配去重，剩下的mempool中还未被确认的交易数据则会被等待打包进下一区块。
Jing的节点立刻创建一个空的区块，即候选区块277,316，之所以称之为代理区块，因为它还不是一个合法区块啊。
区块只有在矿工成功找到PoW的答案时，才能变成合法。
当Jing的节点整合mempool中的所有交易时，发现有418笔交易共0.09094928btc的交易费。
#### The Coinbase Transaction
任何区块的第一笔交易比较特别，称为coinbase transaction，这是由Jing的节点创建的，里面包含了开采此区块的奖励。

| Note | When block 277,316 was mined, the reward was 25 bitcoin per block. Since then, one "halving" period has elapsed. The block reward changed to 12.5 bitcoin in July 2016. It will be halved again in 210,000 blocks, in the year 2020. |
| :--- | :--- |



Jing的节点创建一笔由coinbase支付给它自己钱包的一笔交易：‘支付Jing的地址25.09094928btc’，支付总额为coinbase奖励(25新的btc)和所有该区块包含的交易产生的交易费。

Example 4. Coinbase transaction
```
$ bitcoin-cli getrawtransaction d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f 1
```

```
{
    "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0f03443b0403858402062f503253482fffffffff0110c08d9500000000232102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac00000000",
    "txid" : "d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f",
    "version" : 1,
    "locktime" : 0,
    "vin" : [
        {
            "coinbase" : "03443b0403858402062f503253482f",
            "sequence" : 4294967295
        }
    ],
    "vout" : [
        {
            "value" : 25.09094928,
            "n" : 0,
            "scriptPubKey" : {
                "asm" : "02aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21OP_CHECKSIG",
                "hex" : "2102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [
                    "1MxTkeEP2PmHSMze5tUZ1hAV3YTKu2Gh1N"
                ]
            }
        }
    ]
}
```

和常规的交易不同，coinbase交易并不会花销UTXO作为inputs。
它只有一个input，称为coinbase，即创建全新的比特币；coinbase交易也只有一个output，支付给挖矿者自己的比特币地址。比如在这个例子中，coinbase支付给Jing的地址1MxTkeEP2PmHSMze5tUZ1hAV3YTKu2Gh1N一共25.09094928btc。
#### Coinbase Reward and Fees
为了创建这笔coinbase交易，Jing的节点首先计算所有交易产生的交易费：
```
Total Fees = Sum(Inputs) - Sum(Outputs)
```

In block 277,316, the total transaction fees are 0.09094928 bitcoin.
然后，Jing的节点计算正确的开采奖励金，这笔奖励是基于块高(block height)计算的，从50btc开始，每隔21w区块减半：
```
CAmount GetBlockSubsidy(int nHeight, const Consensus::Params& consensusParams)
{
    int halvings = nHeight / consensusParams.nSubsidyHalvingInterval;
    // Force block reward to zero when right shift is undefined.
    if (halvings >= 64)
        return 0;

    CAmount nSubsidy = 50 * COIN;
    // Subsidy is cut in half every 210,000 blocks which will occur approximately every 4 years.
    nSubsidy >>= halvings;
    return nSubsidy;
}
```

The initial subsidy is calculated in satoshis by multiplying 50 with the COIN constant (100,000,000 satoshis). This sets the initial reward (nSubsidy) at 5 billion satoshis.

| Tip | If Jing’s mining node writes the coinbase transaction, what stops Jing from "rewarding" himself 100 or 1000 bitcoin? The answer is that an incorrect reward would result in the block <span data-type="color" style="color:#F5222D;">being deemed invalid by everyone else, wasting Jing’s electricity used for Proof-of-Work. Jing only gets to spend the reward if the block is accepted by everyone</span>. |
| :--- | :--- |



#### Structure of the Coinbase Transaction
通过这些计算，Jing的节点最终会创建一笔coinbase交易支付给他自己25.09094928btc。
正如所见，coinbase交易有种特殊的格式；它的vin并没有指向一笔之前的UTXO，而是有一笔coinbase input。我们来和常规的交易做个对比吧：

在coinbase交易中，前面两个字段被设置为不指向特定UTXO的值，即transaction hash字段全部设置为32字节的0，output序号被设置为4字节的0xFF，unlocking script则由coinbase data代替。
#### Coinbase Data
coinbase交易没有unlocking script(scriptSig)，取而代之的是coinbase data，2~100字节长度，除了最初的几个字节，剩下的数据由挖矿者自己决定，可以是任何数据。
比如在创世区块中，中本聪写的是 "The Times 03/Jan/2009 Chancellor on brink of second bailout for banks"。 当前，挖矿者使用coinbasedata来存放额外的nonce值和区分矿池的字符串数据等。
前面的几个字节在一开始是任意的，但从BIP-34后，version2的区块必须将块高信息包含到coinbase data字段的开始几个字节。
在区块277,316中，我们看到coinbase data字段包含16进制的数值<span data-type="color" style="color:#F5222D;">03443b0403858402062f503253482f</span>，我们来解码看看：
第一个字节03指示脚本执行引擎将接下来的03个字节push到脚本栈中
接下来的三个字节443b04，即0x443b04表示的是little-endian(backward, least-significant byte first)格式的块高，将字节反转过来即为0x043b44，即十进制的277,316。
接下来的几个字节0385840206用来编码额外的nonce，即随机数，这是用来找到PoW答案的。
剩下的数据2f503253482f是ASCII编码的/P2SH/，暗示开采这个区块的节点支持P2SH协议(BIP-16中见)。
### Constructing the Block Header
为了创建区块头，挖矿的节点需要填充6个字段：

| Size | Field | Description |
| :--- | :--- | :--- |
| 4 bytes | Version | A version number to track software/protocol upgrades |
| 32 bytes | Previous Block Hash | A reference to the hash of the previous (parent) block in the chain |
| 32 bytes | Merkle Root | A hash of the root of the merkle tree of this block’s transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | Target | The Proof-of-Work algorithm target for this block |
| 4 bytes | Nonce | A counter used for the Proof-of-Work algorithm |

在277,316区块被开采的时间，描述区块结构的版本号是2，用little-ending 表示则为0x02000000
第二个字段prevhash则为上一个区块277,315的区块头的hash值：
```
0000000000000002a7bbd25a417c0374cc55261021e8a9ca74442b01284f0569
```


| Tip | By selecting the specific *parent* block, indicated by the Previous Block Hash field in the candidate block header, Jing is committing his mining power to extending the chain that ends in that specific block. In essence, this is how Jing "votes" with his mining power for the longest-difficulty valid chain. |
| :--- | :--- |



接下来则是对所有的419笔交易生成merkle tree，然后才能将merkle root填充到第三个字段。
coinbase交易作为第一笔交易，接下来的418笔交易追加其后。
正如在merkle tree章节介绍的一样，由于交易节点非偶数，最后一笔交易会被复制，从而一共有420个叶子节点，每个节点即为对应交易数据的hash，直到最终生成merkle root:
```
c91c008c26e50763e9f548bb8b2fc323735f73577effbc55502c51eb4cc7cf2e
```

Jing’s mining node will then add a 4-byte timestamp, encoded as a Unix "epoch" timestamp, which is based on the number of seconds elapsed from January 1, 1970, midnight UTC/GMT. The time 1388185914 is equal to Friday, 27 Dec 2013, 23:11:54 UTC/GMT.
然后是target字段, which defines the required Proof-of-Work to make this a valid block. The target is stored in the block as a "target bits" metric, which is a mantissa-exponent encoding of the target. The encoding has a 1-byte exponent, followed by a 3-byte mantissa (coefficient). In block 277,316, for example, the target bits value is 0x1903a30c. The first part 0x19 is a hexadecimal exponent, while the next part, 0x03a30c, is the coefficient. The concept of a target is explained in [Retargeting to Adjust Difficulty](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch10.asciidoc#target) and the "target bits" representation is explained in [Target Representation](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch10.asciidoc#target_bits).
The final field is the nonce, which is initialized to zero.
__<span data-type="color" style="color:#F5222D;">随着所有的头部字段被填充，挖矿的过程就可以真正开始了。目标是找到一个nonce值，使得区块头的hash值小于target。</span>__
![image.png | center | 752x466](https://cdn.yuque.com/lark/2018/png/2075/1522735128355-a4d2ebd8-25b2-439c-a10f-cb9b64373a1f.png "")

挖矿节点将需要几十亿甚至更多的静思园才能找到一个合适的nonce。
### Mining the Block
sha256方法被用来挖矿。简单来说，挖矿的过程就是在只改变nonce的情况下，重复hash区块头，直到找到合适的nonce值满足小于target。hash的结果不能被提前确定，也没有固定的pattern，这种特性使得唯一的办法就是比拼算力。
#### Proof-of-Work Algorithm
哈希算法是以任意长度的数据作为输入，能产生固定长度的确定性的结果，即针对输入的电子签名。对任何确定的输入，其产生的结果一定是相同的，且能够非常容易的被验证。
加密哈希的一个重要特性是：要找到两个输入能产生同样的输出结果在计算上是不可行的(即哈希碰撞很难发生)。由此可以推理，要产生想要的输出结果，几乎是不可能由自己选择输入数据来决定的，唯一可行的办法就是尝试随机输入。
在比特币中，添加随机因子的字段称为nonce，即从0开始一直自增，知道找到合适的值能产生满足target的hash。

那么如果以这个算法来设置一种挑战的话，我们可以这样设定目标：找到一个字符串使得产生的16进制hash以一个0开始，从实验结果可以看到，这并不难，比如要哈希‘I am Satoshi Nakamoto’的话，经过13此迭代，即‘I am Satoshi Nakamoto13’的hash为0ebc56d59a34f5082aaef3d66b37a661696c2b618e62432727216ba9531041a5，从而满足了要求。
从概率而言，如果哈希方法的输出是均匀分布的，我们应该可以从每16个hash中找到一个已0开头的值(0~F)。
从数值的角度，以0开头，意味着数值小于0x1000000000000000000000000000000000000000000000000000000000000000，我们把这个阈值称之为target，于是我们的目标即为找到小于target的hash值。
可以看到，如果我们调整target，其值越小，则越难找到一个这样的hash值。

> 简单类比一下，想象这么一个游戏，玩家重复投掷一对骰子，以尝试获得一个小于target的点数。
> 在第一轮，我们把target设为12，则，除非你两个骰子都是6点，否则你都赢了。
> 在接下来一轮，我们把target设为11，则玩家获得的点数最多不能大于10，同样也是比较容易得到的。
> 如果在几轮之后，我们把target设为5，现在你会发现会有一半以上的几率你骰子的点数会超过5。
> 即随着target越来越小，需要投掷的尝试次数会指数级增加。
> 最后，当target是2的时候，概率上每36次才会产生一次满足条件的机会。

__<span data-type="color" style="color:#F5222D;">从观察者的角度来看这个问题，他知道target是2，那么当一个人赢了，则观察者可以很好的假设他平均尝试了36次。</span>__
__<span data-type="color" style="color:#F5222D;">换句话说，根据target的设置，我们能够预估要解决此target需要付出的工作量。这就是Proof-of-work。</span>__

| Tip | Even though each attempt produces a random outcome, the probability of any possible outcome can be calculated in advance. Therefore, an outcome of specified difficulty constitutes proof of a specific amount of work. |
| :--- | :--- |



在上面提到的实验中，一个赢得比赛的nonce是13，这个结果是能够被任何人独立验证的，只需将13拼接到字符串‘I am Satoshi Nakamoto’后面，然后计算其hash，比对结果真的小于target即可验证。
这个结果也是工作量证明，因为它证明了我们是干活了的才最终找到了这个nonce。
__我们需要花13次hash计算来找到合适的nonce，但只需要一次hash计算即可验证结果正确性。如果我们把target设置更低(难度更高)，则就需要更多的hash计算来找到合适的nonce，但对于验证来说也是只需要一次hash计算即可。__
__此外，通过target的设置，任何人都可以用概率模型预估计算难度，即需要多大的工作量来找到合适的nonce。__

| Tip | The Proof-of-Work must produce a hash that is *less than* the target. A higher target means it is less difficult to find a hash that is below the target. A lower target means it is more difficult to find a hash below the target. The target and difficulty are inversely related. |
| :--- | :--- |



比特币中的PoW和上面描述的过程基本差不多，矿工在构建完候选区块后，就开始尝试计算区块头的hash来看看是否小于设定的target了，如果不满足，则会调整nonce(通常是自增1)再试，在比特币网络的当前难度系数下，矿机需要计算上亿次才能找到合适的nonce值。
#### Target Representation
在上面我们通过命令行获取到277316区块的数据来看，target数值存放在‘target bits’或‘bits’字段，值为0x1903a30c，这个数值是PoW中target的系数/指数表示法，即前面两个16进制数字表示的是指数，后面的6个数字表示的是系数。
如此，在这个区块中，指数为0x19，系数为0x03a30c。

这意味着对于高度为277316的合法区块，它的区块头哈希值必须小于该target。在二进制中，该哈希值必须有60个以上的0作为开头。
在这种难度系数下，一个矿机如果每秒处理1M哈希运算 (1 terahash per second or 1 __<span data-type="color" style="color:#F5222D;">TH/sec</span>__) ，平均需要59天。
#### Retargeting to Adjust Difficulty
<span data-type="color" style="color:#F5222D;">上面我们看到target决定着挖矿计算的难度，那么为什么要设计为可调节，以及怎么调节的呢？</span>
比特币网络中的区块平均每10分钟产生一个，这是比特币网络的心跳，也是比特币发行的频率和交易确定的速度。这些速度是需要维持几十年恒定的。而在这期间，计算机的处理能力是会快速提升的，还有就是参与挖矿的节点和电脑也会频繁发生变化。
为了维持10分钟的频率，挖矿的难度系数需要根据这些变量来动态调整。
事实上，PoW target是一个动态变量，会周期性的调整已匹配10分钟产生一个新区块的频率。
那么这样一个动态调整在完全去中心化的网络中是怎么实现的呢？
这种调整会在每个节点上独自自动完成，每隔2016个区块，所有节点会重新调整难度系数。
首先计算这2016个区块所花费的总时间与20160分钟比较，如果挖矿过快，则会加大难度系数来降低速度；如果挖矿过慢，则降低难度系数。

```
New Target = Old Target * (Actual Time of Last 2016 Blocks / 20160 minutes)
```


pow.cpp
```cpp
   // Limit adjustment step
    int64_t nActualTimespan = pindexLast->GetBlockTime() - nFirstBlockTime;
    LogPrintf("  nActualTimespan = %d  before bounds\n", nActualTimespan);
    if (nActualTimespan < params.nPowTargetTimespan/4)
        nActualTimespan = params.nPowTargetTimespan/4;
    if (nActualTimespan > params.nPowTargetTimespan*4)
        nActualTimespan = params.nPowTargetTimespan*4;

    // Retarget
    const arith_uint256 bnPowLimit = UintToArith256(params.powLimit);
    arith_uint256 bnNew;
    arith_uint256 bnOld;
    bnNew.SetCompact(pindexLast->nBits);
    bnOld = bnNew;
    bnNew *= nActualTimespan;
    bnNew /= params.nPowTargetTimespan;

    if (bnNew > bnPowLimit)
        bnNew = bnPowLimit;
```
#### Successfully Mining the Block
之前有描述过，Jing的节点构建好候选区块后就开始挖矿了，专有的集成电路以不可思议的速度计算这sha256hash，区块头的nonce字段进行高速尝试，但是由于只有32字节，当穷举完所有数字还是没满足时，矿机会调整区块头，开始使用coinbase extra空间，并重置nonce继续尝试。
等差不多10分钟后，其中一台机器发现了合适的nonce，于是将此值回传到矿机节点上。

当把计算所得的nonce值添加到区块头，计算得到hash：
```
0000000000000001b6b9a13b095e96db41c4a928b97ef2d944a9b31b2cc7bdc4
```

要比target小:
```
0000000000000003A30C00000000000000000000000000000000000000000000
```

于是，​Jing的矿机节点立刻将此区块发送给同僚节点，这些相邻节点接收到区块会验证，然后广播到全网。随着新的区块在全网传播，所有节点会将它追加到自己维护的区块链上，同时这些节点会丢弃自己未完成的挖矿任务，转而开始新一轮的挖矿竞争。随着全网开始以Jing的区块作为父节点开启新一轮挖矿，这些节点本质上是给Jing的区块投票。





### 






![image.png | center | 752x617](https://cdn.yuque.com/lark/2018/png/2075/1522667347577-389d821b-0a23-423f-ba96-134f507c4872.png "")

















































![image.png | center | 465x670](https://cdn.yuque.com/lark/2018/png/2075/1522125974427-53429c3e-12ad-41cd-b3de-a108570658c0.png "")
![image.png | center | 462x524](https://cdn.yuque.com/lark/2018/png/2075/1522154529727-02fb6657-0fa5-4901-b89c-259aeb6b6345.png "")


### 













##### 


























蚂蚁区块链：信任链接的基础设施
公有链：能源消耗的不值，pow、pos
联盟链：蚂蚁
共识收敛
PDFT
零知识证明
激励机制
跨链，即不同链条之间要相互映射（类比交通系统，公路网、铁路网、航空等的沟通）
牛逼，智能合约和租房的类比，签名授权直接接入房门授权进出的硬件系统里 IoT + 生物学(人身份识别)

#### 区块链本质是解决信息流、物流、资金流的可信问题
数据可信->物的可信->资产可信->人的可信

金融
民生
工业

公益的有迹可循，都是基于支付宝的收支记录
奶粉正品追踪
保险+智能合约
茅台酒不可篡改的身份证，造假copy的话，类比双花问题，会被发现具体哪个环节造假
雄安租房智能系统，结合租龄，看是否有学区等

18年预测：
* 金融和供应链会有落地
* 第三代区块链(网状、共识的效率)
* 零知识证明会改善现有互信与隐私的矛盾
* 跨链的突破




loopring 协议，为去中心化交易所提供一种思路
中心化交易所的问题：
* 数据库是可以改的，即安全性
* 透明性
* 流动性

loopring enable：
trustlessly
anonymously
securely
in decentralized way.

AI与区块链的结合，AI自己进化学习，让自己拥有的经济越来越多，从而，人与人、人与机器、机器与机器之间都可以做生意
AI可以是专业技能领域的，身上有智能协议，完成特定任务，然后赚钱等等
机器人的安全性，不被黑客利用；传统的中心化模式是不可能完成的，总会被人黑掉
等等，这叫Consensus-Economy

AI怎么交易呢？
loopring是如何工作的

order-ring: 识别交易流通的环状关系，增加流动性，优化价格等？










bitcoin
比特币的所有权由三个部分组成： digital keys, bitcoin addresses, and digital signatures
digital keys 不在网络上存储，而是由比特币钱包生成，Keys enable many of the interesting properties of bitcoin, including decentralized trust and control, ownership attestation, and the cryptographic-proof security model.​digital signature 在区块链中的所有比特币交易都需要合法的电子签名，电子签名只能由秘钥生成
这个用来花钱的电子签名也被称为witness，这是密码学中的叫法；比特币交易中的电子签名标志着这笔交易所花的钱的真正所有者​Keys是成对生成的，由private(secret) key 和 public key组成。可以将public key想象成银行卡号，private key就像密码；这些Keys很少直接被比特币用户看见，它们被钱包存储和管理着。​在一笔比特币的付款部分，收款人的public key 已电子指纹的方式展现出来，被称为比特币地址，就如同银行支票上的受益人。
大部分场景下，比特币地址由publickey生成，但是，并不是所有比特币地址都代表publickey，有的代表可以代表scripts，后面讲到
比特币地址是用户唯一经常看到的所有keys的代表，因为他们会将地址共享出去​Public key cryptography
公钥加密发明于1970s，是计算机和信息安全的数学基础
从公钥加密的发明以来，几种合适的数学方法被发掘了，包括素数幂，椭圆曲线乘法等；这些方法几乎不可逆，意思是说它们从一个方向计算很简单，但反过来计算却不可行；
在这些数学方法的基础上，密码学诞生了数字秘钥和不可伪造的数字签名，比特币就是使用的椭圆曲线算法来做其加密算法​In bitcoin, we use public key cryptography to create a key pair that controls access to bitcoin. The key pair consists of a private key and—​derived from it—​a unique public key. The public key is used to receive funds, and the private key is used to sign transactions to spend the funds
比特币中使用公钥加密技术生成一对keys来管理比特币，这一对keys由秘钥和一个唯一的公钥(由秘钥产生)组成，公钥用来接收资产，私钥用来给花费资产的交易签名。​There is a mathematical relationship between the public and the private key that allows the private key to be used to generate signatures on messages. This signature can be validated against the public key without revealing the private key.
公钥和私钥之间有一种数学联系，使得私钥可以用来生成电子签名，而这个签名可以在不暴露私钥的前提下和相应的公钥进行匹配验证。​When spending bitcoin, the current bitcoin owner presents her public key and a signature (different each time, but created from the same private key) in a transaction to spend those bitcoin. Through the presentation of the public key and signature, everyone in the bitcoin network can verify and accept the transaction as valid, confirming that the person transferring the bitcoin owned them at the time of the transfer.
当花费比特币时，当前拥有者会在交易中提供他的公钥和电子签名(签名每次不相同，但都由私钥产生)；通过公钥和电子签名，网络上的每个节点都能验证当前交易的合法性，确保当前资产转移人在交易发生时刻确实拥有该笔比特币。​Private and Public Keys
A bitcoin wallet contains a collection of key pairs, each consisting of a private key and a public key. The private key (k) is a number, usually picked at random. From the private key, we use elliptic curve multiplication, a one-way cryptographic function, to generate a public key (K). From the public key (K), we use a one-way cryptographic hash function to generate a bitcoin address (A). In this section, we will start with generating the private key, look at the elliptic curve math that is used to turn that into a public key, and finally, generate a bitcoin address from the public key.
比特币钱包中有一堆key对，每个key对都由私钥和公钥组成。每个私钥(k)是一个随机数，通过椭圆曲线乘法(一种单向加密方法)生成公钥(K),再通过单向加密哈希方法，由公钥生成一个比特币地址(A)。​为什么要使用非对称加密(Private/Public Keys)?
Why is asymmetric cryptography used in bitcoin? It’s not used to "encrypt" (make secret) the transactions. Rather, the useful property of asymmetric cryptography is the ability to generate digital signatures. A private key can be applied to the digital fingerprint of a transaction to produce a numerical signature. This signature can only be produced by someone with knowledge of the private key. However, anyone with access to the public key and the transaction fingerprint can use them to verify the signature. This useful property of asymmetric cryptography makes it possible for anyone to verify every signature on every transaction, while ensuring that only the owners of private keys can produce valid signatures.
为什么比特币使用的是非对称加密呢，因为比特币并不是需要加密交易信息，而是借助非对称加密算法的特性生成电子签名。
私钥能够应用在一笔交易的电子指纹(hash)上来生成电子签名，这个签名仅能被拥有私钥的人产生出来。但是呢，任何拥有其公钥和该笔交易的电子指纹的人都能够验证签名的合法性。
这个非对称加密的特性使得任何人都能够验证任何交易上的签名合法性，且能保证只要私钥的owner才能产生合法的电子签名。​Private Keys
私钥是纯数字，256位二进制随机串。一旦失去，无法恢复​More precisely, the private key can be any number between 0 and n - 1 inclusive, where n is a constant (n = 1.1578 \* 1077, slightly less than 2256) defined as the order of the elliptic curve used in bitcoin.
要生成这么一个key，我们随机产生256bit的数字，然后比较是否小于n，是，则使用，否，则重试​In programming terms, this is usually achieved by feeding a larger string of random bits, collected from a cryptographically secure source of randomness, into the SHA256 hash algorithm, which will conveniently produce a 256-bit number​2^256差不多等于10的77次方，什么概念呢，我们能看见的宇宙中所包含的原子大约有10的80次方！​Public Keys
公钥是私钥通过椭圆曲线乘法计算得来，且不可逆:K = k \* G, k是私钥，G称为generator point，而K则是公钥。Elliptic curve multiplication is a type of function that cryptographers call a "trap door" function: it is easy to do in one direction (multiplication) and impossible to do in the reverse direction (division). The owner of the private key can easily create the public key and then share it with the world knowing that no one can reverse the function and calculate the private key from the public key. This mathematical trick becomes the basis for unforgeable and secure digital signatures that prove ownership of bitcoin funds.​Elliptic Curve Cryptography Explained
好复杂，反正是不可逆，可以通过私钥和预先定义好的G算出公钥，k\*G并不是通常意义上的数字相乘，而是在椭圆曲线上找到k个G相加的结果点，不可逆
比特币中G是固定的，所以，对每个私钥k，只有唯一确定的公钥K与之对应，从而关系固定​Bitcoin Addresses
A bitcoin address is a string of digits and characters that can be shared with anyone who wants to send you money. Addresses produced from public keys consist of a string of numbers and letters, beginning with the digit "1." Here’s an example of a bitcoin address:
1J7mdg5rbQyUHENYdx39WVWK7fsLpEoXZy
比特币地址由一串数字和字母组成，可以分享给需要给你转账的人。​The bitcoin address is what appears most commonly in a transaction as the "recipient" of the funds. If we compare a bitcoin transaction to a paper check, the bitcoin address is the beneficiary, which is what we write on the line after "Pay to the order of." On a paper check, that beneficiary can sometimes be the name of a bank account holder, but can also include corporations, institutions, or even cash. Because paper checks do not need to specify an account, but rather use an abstract name as the recipient of funds, they are very flexible payment instruments. Bitcoin transactions use a similar abstraction, the bitcoin address, to make them very flexible. A bitcoin address can represent the owner of a private/public key pair, or it can represent something else, such as a payment script, as we will see in [p2sh]. For now, let’s examine the simple case, a bitcoin address that represents, and is derived from, a public key.
比特币地址在一笔交易中通常就是该笔资金的收款人。如果和实物交易相比，那么就是银行支票上的受益人，在物理世界中，受益方有可能是具体的人，也有可能是公司机构等。
同样的，比特币地址也有此灵活性，可以代表拥有此公钥的所有人，也可以代表一个支付脚本。​Starting with the public key K, we compute the SHA256 hash and then compute the RIPEMD160 hash of the result, producing a 160-bit (20-byte) number：
A = RIPEMD160(SHA256(K))​Base58Check
比特币地址是Base58Check编码的​所以，整个流程是这样：
公钥 -> (sha256 -> ripemd160 两次hash)160bits -> Base58Check编码 -> 比特币地址​Base58 and Base58Check Encoding
为了能够更紧凑的表示大数值，很多计算机系统使用有字母参与的大于10为基数的表示法，比如16进制
base64则是10个数字+26个大写字母+26个小写字母+两个特殊字符(“&#x201d; and "/") 那么base58是转为比特币设计的，剔除了base64中易混淆和难读的字符(0 (number zero), O (capital o), l (lower L), I (capital i), and the symbols &#x201c;” and "/")​Base58Check是啥呢
为了额外加强安全，避免转录或者书写错误，比特币引入了Base58Check，Base58Check是base58编码格式，有内置的校验码
校验码即是追加在后面的4bytes的数据，这些校验码是由被编码的内容hash而来，从而能有效监测数据书写错误。保证了比特币地址的安全可靠，不易写错。​To convert data (a number) into a Base58Check format, we first add a prefix to the data, called the "version byte," which serves to easily identify the type of data that is encoded. For example, in the case of a bitcoin address the prefix is zero (0x00 in hex), whereas the prefix used when encoding a private key is 128 (0x80 in hex). A list of common version prefixes is shown in Base58Check version prefix and encoded result examples.​Next, we compute the "double-SHA" checksum, meaning we apply the SHA256 hash-algorithm twice on the previous result (prefix and data):​checksum = SHA256(SHA256(prefix+data))
From the resulting 32-byte hash (hash-of-a-hash), we take only the first four bytes. These four bytes serve as the error-checking code, or checksum. The checksum is concatenated (appended) to the end.​要将一段数据转成Base58Check格式，步骤是这样的，首先给数据加version前缀，表示不同的使用场景
然后，对(prefix+data)数据做两次sha计算，再取结果的前4字节数据，拼到data后面​最后，在对prefix+data+checksum 三部分组成的数据做Base58编码，即得到最终投入各场景使用的数字​Compressed public keys
为了节省存储空间，比特币公钥的存储做了很多优化压缩，比如base58压缩，然后公钥本来是椭圆曲线上的一个点，有xy两个坐标，但是基于x是能计算出y的，于是比特币只存储了x，使得空间节省一半。​经压缩的公钥也对应着同一个私钥，即也是由同一个私钥生成的。但是，与非压缩的公钥看上去差异很大，更甚，如果我们按同样的流程用这个压缩版的公钥生成比特币地址的话，会与非压缩版本生成的地址不一致。
这会引起疑惑，因为意味着单一私钥能生成不同的公钥，进而能生成不同的比特币地址，但是，这些地址所对应的私钥是一致的，这就够了。​Wallets比特币钱包其实可以理解为一个数据结构，用来存储和管理用户的keys​一种常见的误解是以为钱包里存放着比特币。事实上，钱包里面只存放着keys，比特币是以记录的方式存在于整个比特币网络里，用户通过使用自己的key来给交易签名的方式在网络中流通着比特币。这么理解，比特币钱包就像是钥匙扣。​本质上有两大类钱包，根据钱包里面的keys是否有关联关系来区分。第一种是不确定性钱包(nondeterministic wallet)，包里的所有key各自随机生成，相互间没有关联关系。这类钱包也叫做JBOK，Just a Bunch Of Keys.第二种称之为确定性钱包(deterministic wallet)，所有的key都是由一个被称为seed的主key衍生出来。所有的这些key是有关联的，并且能够用原始seed重新生成。对这种钱包，有不同的key衍生方法，最常用的是一种树形结构，被称为HDwallet(hierarchical deterministic)。Nondeterministic (Random) Wallets在第一版比特币钱包(现在称为Bitcoin Core)实现中，钱包即为一堆随机生成的key的集合。比如，最初的Bitcoin Core客户端会在第一次启动时预先生成100个随机key，后续再根据需要生成更多key，每个key只使用一次。这类钱包主键被确定性钱包取代，因为它们维护备份和导出都太麻烦了。随机key的劣势在于，如果你生成了很多key，你需要经常备份所有，每个key都必须备份，不然它所关联的比特币就丢失了。这种备份方式就和避免地址复用的原则相违背了。​​Deterministic (Seeded) Wallets In a deterministic wallet, the seed is sufficient to recover all the derived keys, and therefore a single backup at creation time is sufficient. The seed is also sufficient for a wallet export or import, allowing for easy migration of all the user’s keys between different wallet implementations. Type-1 deterministic (seeded) wallet: a deterministic sequence of keys derived from a seed shows a logical diagram of a deterministic wallet.确定性钱包或基于种子seed的钱包，是通过运用单向hash方法，基于一个seed能衍生出一系列key。seed是随机数再结合一些其它数据，比如序列号或者链码。在确定性钱包中，seed能够用来恢复所有衍生的key，所以在创建时单独备份一次就足够了。seed同样足够用来导入或导出到其它钱包中。​​HD Wallets (BIP-32/BIP-44)Deterministic wallets were developed to make it easy to derive many keys from a single "seed." The most advanced form of deterministic wallets is the HD wallet defined by the BIP-32 standard. HD wallets contain keys derived in a tree structure, such that a parent key can derive a sequence of children keys, each of which can derive a sequence of grandchildren keys, and so on, to an infinite depth. This tree structure is illustrated in Type-2 HD wallet: a tree of keys generated from a single seed.最高级的确定性钱包就是BIP-32标准所出的HD wallet，树状结构，能够一层一层的无限向下衍生​HD钱包两大优势，一是树状结构能很好的和组织结构对应起来，比如一组key用来接收付款，一组key用来支出等，或者直接与公司部门结构对应，给不同部门分配不同类组下的key。第二个优势就是用户能在不用访问到对应私钥的前提下生成一系列公钥。这使得HD钱包能在不安全的服务器上使用，或者用来专门做收款功能。公钥不用提前生成，且服务器没有能够用来支出的私钥。​Seeds and Mnemonic Codes (BIP-39)HD钱包是管理多key和多地址很有力的机制。如果使用英文单词作为助记符来生成seed的话，正如BIP-39定义，这种钱包就更易使用了。Wallet Best PracticesAs bitcoin wallet technology has matured, certain common industry standards have emerged that make bitcoin wallets broadly interoperable, easy to use, secure, and flexible. These common standards are:Mnemonic code words, based on BIP-39HD wallets, based on BIP-32Multipurpose HD wallet structure, based on BIP-43Multicurrency and multiaccount wallets, based on BIP-44These standards may change or may become obsolete by future developments, but for now they form a set of interlocking technologies that have become the de facto wallet standard for bitcoin.The standards have been adopted by a broad range of software and hardware bitcoin wallets, making all these wallets interoperable. A user can export a mnemonic generated on one of these wallets and import it in another wallet, recovering all transactions, keys, and addresses.Some example of software wallets supporting these standards include (listed alphabetically) Breadwallet, Copay, Multibit HD, and Mycelium. Examples of hardware wallets supporting these standards include (listed alphabetically) Keepkey, Ledger, and Trezor.Wallet Technology Details

Mnemonic Code Words (BIP-39)TipMnemonic words are often confused with "brainwallets." They are not the same. The primary difference is that a brainwallet consists of words chosen by the user, whereas mnemonic words are created randomly by the wallet and presented to the user. This important difference makes mnemonic words much more secure, because humans are very poor sources of randomness.助记符单词是一系列代表用来生成seed随机数的英文单词，通过这些单词能够重建seed，然后根据seed能够衍生出key。一个基于确定性钱包标准实现的钱包在创建时会向用户展示12到24个单词序列。这一单词序列即可用来作为钱包备份，通过它可以恢复所有衍生的key。单词的优势是简单易记，且抄写不易出错。助记符在BIP-39中定义，并在当今已经成为业界标准了。Generating mnemonic words

助记符单词由钱包根据BIP-39的标准流程自动生成。钱包从一个熵源开始，添加一个校验码，然后将熵映射到单词列表：Create a random sequence (entropy) of 128 to 256 bits.Create a checksum of the random sequence by taking the first (entropy-length/32) bits of its SHA256 hash.Add the checksum to the end of the random sequence.Split the result into 11-bit length segments.Map each 11-bit value to a word from the predefined dictionary of 2048 words.The mnemonic code is the sequence of words.

​​From mnemonic to seed助记单词代表着128~256bit的熵，这个熵值然后通过PBKDF2方法延展到512bit成为seed，最终，通过这个seed就能够用来创建确定性钱包以及其需要的key了。这个延展方法PBKDF2有两个参数：助记符和salt，salt的目的是防止暴力破解。在BIP-39标准中，salt还有另外一个目的，就是作为用户密码，给安全性加双保险。The process described in steps 7 through 9 continues from the process described previously in Generating mnemonic words:The first parameter to the PBKDF2 key-stretching function is the mnemonic produced from step 6.The second parameter to the PBKDF2 key-stretching function is a salt. The salt is composed of the string constant "mnemonic" concatenated with an optional user-supplied passphrase string.PBKDF2 stretches the mnemonic and salt parameters using 2048 rounds of hashing with the HMAC-SHA512 algorithm, producing a 512-bit value as its final output. That 512-bit value is the seed.​TipThe key-stretching function, with its 2048 rounds of hashing, is a very effective protection against brute-force attacks against the mnemonic or the passphrase. It makes it extremely costly (in computation) to try more than a few thousand passphrase and mnemonic combinations, while the number of possible derived seeds is vast (2512).BIP-39标准允许在生成seed的时候加入额外的用户密码，如果用户没指定密码，则salt为常量字符串"mnemonic"，如果指定了密码，则salt就不同了，从而能产生不同的seed，事实上，给定一个确定的助记符单词序列，每个不同的密码就会产生不同的seed，从而对应着巨大不同的seed空间，即未初始化的钱包。这个集合如此巨大(2512)以至于暴力破击基本无望。TipThere are no "wrong" passphrases in BIP-39. Every passphrase leads to some wallet, which unless previously used will be empty.这个可选的密码创造了两个重要特性：成为钱包安全的第二因子，让助记符本身单方面无用，及时助记符被盗用也没事第二个感觉没看懂，没啥意义(A form of plausible deniability or "duress wallet," where a chosen passphrase leads to a wallet with a small amount of funds used to distract an attacker from the "real" wallet that contains the majority of funds)Creating an HD Wallet from the SeedHD wallets are created from a single root seed, which is a 128-, 256-, or 512-bit random number. Most commonly, this seed is generated from a mnemonic as detailed in the previous section.HD wallets能有单一的root seed创建，seed是512bit的随机数，钱包中的每一个key都是由seed确定性产生的，这使得能够在所有兼容的钱包体系中通过同一个seed完整恢复出所有的key。这就使得备份或者导入导出方便快捷了。​root seed通过HMAC-SHA512哈希算法能够得到一个主私钥m(master private key)和一个主链码c(master chain code)。私钥可以通过椭圆曲线产生对应的公钥M，链码c则用来作为熵输入到函数中产生子keyPrivate child key derivationHD wallets使用CKD方法(Child Key Derivation)来根据父key衍生出子keyCDK方法基于单向哈希方法，它结合了：父私钥或者父公钥链码序列号The chain code is used to introduce deterministic random data to the process, so that knowing the index and a child key is not sufficient to derive other child keys. Knowing a child key does not make it possible to find its siblings, unless you also have the chain code. The initial chain code seed (at the root of the tree) is made from the seed, while subsequent child chain codes are derived from each parent chain code.​These three items (parent key, chain code, and index) are combined and hashed to generate children keys, as follows.这三者(父key、链码、序列号)结合并哈希后，就能产生子key(就像通过seed产生主私钥、主公钥、链码一样)，这样一层层迭代，就能产生无穷的子key了。The parent public key, chain code, and the index number are combined and hashed with the HMAC-SHA512 algorithm to produce a 512-bit hash. This 512-bit hash is split into two 256-bit halves. The right-half 256 bits of the hash output become the chain code for the child. The left-half 256 bits of the hash are added to the parent private key to produce the child private key. In Extending a parent private key to create a child private key, we see this illustrated with the index set to 0 to produce the "zero" (first by index) child of the parent.​从而，每一组父key，能够产生最多2^31个子key。Using derived child keys

子私钥能用来做什么呢？可以用来生成公钥，进而生成比特币地址，然后就可以用在交易中了。TipA child private key, the corresponding public key, and the bitcoin address are all indistinguishable from keys and addresses created randomly. The fact that they are part of a sequence is not visible outside of the HD wallet function that created them. Once created, they operate exactly as "normal" keys.Extended keys "extended key" 即上面提到的由父key生成的子key(512bit公钥或者私钥)和链码(512bit)连起来。因为通过拓展key能生成一系列的子key，它常被用来作为一个新的分支。有两类，私钥和链码连起来的叫私密拓展key，能用来生成完整的分支；公钥和链码连起来叫做公开拓展key，只能用来生成公钥分支Public child key derivation​​​TransactionsIntroduction交易是比特币系统中最重要的部分。比特币系统中所有其它部分都是设计用来确保交易能被创建、传输、校验，并最终添加到全球账本即区块链上。交易信息其实就是一种数据结构，它记录着比特币网络中参与者之间的价值传递信息。本章节介绍交易的各种细节。​Transactions in Detail在前面我们有提到，使用block explorer查看Alice买咖啡时与Bob之间的交易信息。从上面可以看到交易从Alice的比特币地址到Bob的地址，这是简化后的交易信息。​Transactions—Behind the Scenes在幕后，真实的交易和在explorer上看到的信息有很大差异的。事实上，在用户界面看到的好多数据结构，在底层的比特币网络中并不存在。我们可以用Bitcoin Core的命令行工具(getrawtransaction and decoderawtransaction)来查看、解码一个交易，看看它长什么样：{  "version": 1,  "locktime": 0,  "vin": [    {      "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",      "vout": 0,      "scriptSig" : "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d172787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",      "sequence": 4294967295    }  ],  "vout": [    {      "value": 0.01500000,      "scriptPubKey": "OP\_DUP OP\_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 OP\_EQUALVERIFY OP\_CHECKSIG"    },    {      "value": 0.08450000,      "scriptPubKey": "OP\_DUP OP\_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP\_EQUALVERIFY OP\_CHECKSIG",    }  ]}你会注意到，好多信息没看到！Alice的地址呢？Bob的地址呢？Alice付款的0.1比特呢？在比特币中，没有比特币结余，没有发送者，没有接收者，没有结余，没有账户，没有地址。所有这些东西都是在上层结构中为了方便用户理解才构建出来的。你会注意到很多奇怪的字段和16进制字符串。别担心，我们继续解释。​Transaction Outputs and Inputs一笔比特币交易记录中最基本的信息块是交易输出部分。交易输出是不可分割的比特币金额，记录在区块链上，并被整个网络合法接纳。比特币网络中的完整节点(full nodes即包含全网所有交易记录的节点)会跟踪所有可用来支出的输出块信息，即UTXO(Unspent Transaction Outputs)，所有UTXO记录的集合叫做UTXO set，当前是百万量级。UTXO集会随着新的UTXO记录产生而增加，随着UTXO被消费而减少。每一笔交易就代表UTXO集的一次变动。当我们说一个用户收到一笔比特币，实际意思是比特币钱包发现了一个UTXO，该UTXO能够使用钱包中管理的某个key来支付使用。因此，用户的比特币结余即为该用户钱包中所有的key能够支配的UTXO的总和。结余的概念是钱包应用创造的。钱包通过扫描整个区块链所有记录，集合出该钱包能支配的所有UTXO。大多数钱包会维护一个数据库来存储结余，便于快速查询使用。

​一笔交易输出(transaction output)可以是由satoshi计价的任何数字，比如美元是由最小单位美分计价，比特币则是由最小单位satoshi计价(1bit = 10^8satoshi)，虽然一个输出可以是任意数值，但一旦被创建，它就不可分割了。这是交易输出所具备的一个重要特性：outputs是分散的不可拆分的单元，由satoshi计价。一个未支出的output只能被作为整体在交易中支出。如果一笔UTXO笔要支付的费用大，任然需要整体支付，然后再产生零钱。比如你有20btc，但需要支付1btc，你需要将20btc作为整体支出，然后会产生两笔输出，一笔是1btc支付给你的交易方，一笔是19btc支付给你自己。
如果需要支付的金额比较大，则钱包会在UTXO中找到一个组合，让组合后的金额大等于支付金额。​一笔交易花费的是之前记录的还未被花费的交易输出outputs，然后创建新的交易输出，而这新的输出记录又可以被将来的交易使用。如此这般，比特币价值就在区块链上完成传递。​有一种特殊交易coinbase交易 ，存在于每个区块的第一笔交易中。这笔交易是由挖矿胜出者放在这里，且其中的比特币是全新铸造出的，即其中的比特币不会消耗UTXO集。TipWhat comes first? Inputs or outputs, the chicken or the egg? Strictly speaking, outputs come first because coinbase transactions, which generate new bitcoin, have no inputs and create outputs from nothing.Transaction Outputs每一笔比特币交易都有输出outputs，并记录在比特币账本上。所有的输出都会创建可花销的比特币记录成为UTXO，然后会被整个网络确认，然后其所有者就可以在后续交易中使用了。交易输出由两部分组成：比特币数值，由最小单温satoshi计价一串加密信息，决定着支配该笔比特币的必要条件加密串也被称为锁定脚本(locking script)，见证脚本(witness script)或者scriptPubKey。Now, let’s look at Alice’s transaction (shown previously in Transactions—Behind the Scenes) and see if we can identify the outputs. In the JSON encoding, the outputs are in an array (list) named vout:现在我们再回头看看Alice的交易记录，看看能不能定位出交易输出。在json格式下，outputs即为vout属性下的数组信息："vout": [  {    "value": 0.01500000,    "scriptPubKey": "OP\_DUP OP\_HASH160 ab68025513c3dbd2f7b92a94e0581f5d50f654e7 OP\_EQUALVERIFY    OP\_CHECKSIG"  },  {    "value": 0.08450000,    "scriptPubKey": "OP\_DUP OP\_HASH160 7f9b1a7fb68d60c536c2fd8aeaa53a8f3cc025a8 OP\_EQUALVERIFY OP\_CHECKSIG",  }]可以看到有两部分组成，value和scriptPubKey，Bitcoin Core这里是按btc显示的，但在交易本身，是按satoshi计价的The topic of locking and unlocking UTXO will be discussed later, in Script Construction (Lock + Unlock). The scripting language that is used for the script in scriptPubKey is discussed in Transaction Scripts and Script Language. But before we delve into those topics, we need to understand the overall structure of transaction inputs and outputs.

​Transaction serialization—outputs当交易信息在网络中传输或者在应用间交换时，它们会被序列化(序列化 (Serialization)将对象的状态信息转换为可以存储或传输的形式的过程。在序列化期间，对象将其当前状态写入到临时或持久性存储区。以后，可以通过从存储区中读取或反序列化对象的状态，重新创建该对象)，Table 1. Transaction output serializationSizeFieldDescription8 bytes (little-endian)AmountBitcoin value in satoshis (10-8 bitcoin)1–9 bytes (VarInt)Locking-Script SizeLocking-Script length in bytes, to followVariableLocking-ScriptA script defining the conditions needed to spend the output多数比特币库和框架不会将交易信息存储为字节流，因为这会导致每次需要访问一个字段时都经过复杂的解析过程。方便起见，比特币框架都直接以基于对象的数据结构存储着数据(json).​反序列化或者交易解析即将字节流转换为可读的数据结构的过程。序列化则是指将数据结构转换为字节流以便网络传输或者哈希或者存储到硬盘。大多数比特币库都有内置的序列化和反序列化方法。Transaction Inputs交易输入是确定哪个UTXO将被消费，附带提供能证明owner身份的解锁脚本(unlocking script)。To build a transaction, a wallet selects from the UTXO it controls, UTXO with enough value to make the requested payment. Sometimes one UTXO is enough, other times more than one is needed. For each UTXO that will be consumed to make this payment, the wallet creates one input pointing to the UTXO and unlocks it with an unlocking script.在创建交易时，钱包会从它能访问的UTXO中选择足够额度的UTXO来请求支付。有时候一个UTXO就足够了，有时则需要不止一个。对选中的每个UTXO，钱包会创建一笔交易输入指向它，并用解锁脚本解锁。​仔细解读下交易输入input：第一部分是一个指针，通过引用交易的哈希值和具体output的索引指向唯一的UTXO；第二部分则是解锁脚本，这是钱包构造的，来验证满足UTXO中的支配条件。通常，解锁脚本是由电子签名和公钥组成，能证明所有权的，但也不是所有解锁脚本都包含签名；第三部分，即序列号，这个后面会提到。


比如在Alice的交易信息中，vin部分则为交易输入："vin": [  {    "txid": "7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18",    "vout": 0,    "scriptSig" : "3045022100884d142d86652a3f47ba4746ec719bbfbd040a570b1deccbb6498c75c4ae24cb02204b9f039ff08df09cbe9f6addac960298cad530a863ea8f53982c09db8f6e3813[ALL] 0484ecc0d46f1918b30928fa0e4ed99f16a0fb4fde0735e7ade8416ab9fe423cc5412336376789d172787ec3457eee41c04f4938de5cc17b4a10fa336a8d752adf",    "sequence": 4294967295  }]As you can see, there is only one input in the list (because one UTXO contained sufficient value to make this payment). The input contains four elements:A transaction ID, referencing the transaction that contains the UTXO being spentAn output index (vout), identifying which UTXO from that transaction is referenced (first one is zero)A scriptSig, which satisfies the conditions placed on the UTXO, unlocking it for spendingA sequence number (to be discussed later)可以看到，输入中只有一条记录，因为这一个UTXO足够支付了。input有以下四个元素：一个交易id，指向足够用来支付的UTXO一个output的索引，确定这笔交易中具体的某个UTXO(从0开始索引)scriptSig，电子签名相关的，来满足UTXO的条件认证序列号(后续讨论)在Alice的交易中，input中的指针指向交易id为:7957a35fe64f80d234d76d83a2a8f1a0d8149a41d81de548f0a65a8a999f6f18然后具体output的索引为0 (i.e., the first UTXO created by that transaction). The unlocking script is constructed by Alice’s wallet by first retrieving the referenced UTXO, examining its locking script, and then using it to build the necessary unlocking script to satisfy it.​​https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch06.asciidoc​​更多交易中签名及验签的算法细节，请阅读原文，这里我要进入下一章了。​第七章  https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch07.asciidoc
讲衍生出的高级交易形态和实现，暂时略过​第八章 https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch08.asciidoc
The Bitcoin NetworkPeer-to-Peer Network Architecture比特币网络是架构在因特网之上的一个p2p网络。p2p意味着网络中的节点彼此是对等的，相同的，没有特殊节点。所有节点都担负着提供服务的任务。在平铺的网状结构中，没有层级关系，没有中心服务器及中心节点。​比特币是点对点的电子现金系统，因此网络架构既是这种系统的反应也是基础。分布式的控制权是设计的核心原则，而这又只能由一个平铺的、分布式的P2P共识网络来完成。比特币网络特指网络中运行比特币P2P协议的节点构成的集合。除了比特币P2P协议，还有例如Stratum这种用来挖矿和钱包的协议。Node Types and Roles虽然节点是对等的，他们的角色基于它们的功能会有所不同，比如有路由、区块链数据库、挖矿、钱包等。一个全节点是拥有所有这些功能的节点​所有的节点都至少拥有路由功能。所有节点会校验和传播交易和区块，并发现和维护节点间的连接。

有些节点会维护完整的最新的区块链数据，因而称为full node全节点。能够自治的权威的进行交易验证。有些节点只维护了部分区块链信息，通过SPV(simplified payment verification)的方式来验证交易，这些节点称为SPV节点或者轻节点。挖矿节点竞争来产生新的区块，通过专门的硬件和PoW算法。有些挖矿节点是全节点，而有些是轻节点，轻节点会参与到矿池中，矿池会维护一个全节点，从而辅助轻节点验证交易。​用户钱包现在一般都是SPV节点了​The Extended Bitcoin NetworkThe main bitcoin network, running the bitcoin P2P protocol, consists of between 5,000 and 8,000 listening nodes running various versions of the bitcoin reference client (Bitcoin Core) and a few hundred nodes running various other implementations of the bitcoin P2P protocol, such as Bitcoin Classic, Bitcoin Unlimited, BitcoinJ, Libbitcoin, btcd, and bcoin. A small percentage of the nodes on the bitcoin P2P network are also mining nodes, competing in the mining process, validating transactions, and creating new blocks. Various large companies interface with the bitcoin network by running full-node clients based on the Bitcoin Core client, with full copies of the blockchain and a network node, but without mining or wallet functions. These nodes act as network edge routers, allowing various other services (exchanges, wallets, block explorers, merchant payment processing) to be built on top.

Bitcoin Relay Networks虽然比特币的点对点网络满足了各种类型节点的常规需求，但它固有的高延时对有特殊需求的挖矿节点来说不是好事。矿机们在延展区块链时所参与的PoW竞争是时间敏感的。因此矿机需要尽可能的缩短与胜出节点之间的网络传输，才能尽早的开始下一区块的竞争。即，在挖矿方面，网络延时直接和利润相关。一个比特币中继网络即尝试最小化矿机间的网络延时。最初的比特币中继网络Bitcoin Relay Network是由核心开发者Matt Corallo在2015年开发的，使得矿机之间的区块同步延时很低。该网络有一系列专门的节点(部署在aws上)组成，连接着绝大多数的矿机和矿池。该网络后来被FIBRE(Fast Internet Bitcoin Relay Engine)替代，同样是他开发的。UDP-based，区块更紧凑，传输的数据更少。另一个还在提议中的中继网络是Falcon。cut-through-routing instead of store-and-forward。即类似于文件流，收到部分区块数据后就开始传输，而不是等到整个区块接收完整。中继网络不是比特币网络的替代，而是在其上层提供附加能力的网络。Network Discovery当一个新节点启动后，它必须发现网络中的其它节点一遍参与进去。要开始这个过程，必须发现至少一个存在的节点，并连接。当然，节点的地理位置是无关的，所以，可以随机选择连接节点。要连接到已存在的节点，必须建立tcp连接到端口8333。连接时，需要握手协议(see The initial handshake between peers)，即通过传输版本消息，这消息包含如下信息：nVersion The bitcoin P2P protocol version the client "speaks" (e.g., 70002)nLocalServices A list of local services supported by the node, currently just NODE\_NETWORKnTimeThe current timeaddrYouThe IP address of the remote node as seen from this nodeaddrMeThe IP address of the local node, as discovered by the local nodesubverA sub-version showing the type of software running on this node (e.g., /Satoshi:0.9.2.1/)BestHeightThe block height of this node’s blockchain(See GitHub for an example of the version network message.)这个版本消息会是节点间交互的第一个消息。接收此消息的节点会检查发起连接节点的NVersion，看看是否兼容，如果兼容，则会发送应答并建立连接。​​​那么一个新节点是如何发现现有节点的呢？一种方法是查询DNS节点(DNS seeds)，这些服务器会提供比特币节点的ip地址。Alternatively, a bootstrapping node that knows nothing of the network must be given the IP address of at least one bitcoin node, after which it can establish connections through further introductions. The command-line argument -seednode can be used to connect to one node just for introductions using it as a seed. After the initial seed node is used to form introductions, the client will disconnect from it and use the newly discovered peers.Once one or more connections are established, the new node will send an addr message containing its own IP address to its neighbors. The neighbors will, in turn, forward the addr message to their neighbors, ensuring that the newly connected node becomes well known and better connected. Additionally, the newly connected node can send getaddr to the neighbors, asking them to return a list of IP addresses of other peers. That way, a node can find peers to connect to and advertise its existence on the network for other nodes to find it. ​Address propagation and discovery shows the address discovery protocol.

​为了有丰富的路径接入比特币网络，一个节点必须连接到不通的节点；而这些路径并不可靠，因为节点随来随去，因此节点必须持续发现新节点并帮助其它新进节点完成启动。启动只需要连接到一个节点即可，因为这个节点可以介绍其它更多的节点。启动后，节点会记住与它最近连接最成功的节点，一遍下次启动时再去连接，当然，如果还是遇到了连接不上，那么就会从种子节点出发完成启动。On a node running the Bitcoin Core client, you can list the peer connections with the command getpeerinfo想覆写节点发现的自动化过程的话，可以通过 -connect=<IPAddress> 这个选项指定一些IP，则会只和这些IP连接。如果连接间没有数据传输，节点间会周期发送消息来保持连接如果节点在90分钟内没有任何交流，则可以认为该节点已失去连接，可以寻找一个新节点了。如此，网络就可以动态调整，在没有中心控制的机制下收放自如。Full Nodes全节点即为维护有整个区块链的所有交易数据的节点。在早些年，比特币网络中的所有节点都是全节点，当下则Bitcoin Core client是全节点。在过去两年，出现了轻节点，它们不用维护整个区块链的数据，接下来介绍。全节点维护着整个区块链从一开始到现在的所有交易数据，因此它们可以独立的构建和验证，从创世区块一直到最近的区块。它们不需要依赖其它节点来完成权威验证过程，但是依赖网络来接收新的交易区块，然后验证完成并整合进本地数据库。全节点有各种语言的不同实现，但目前使用最多的还是Bitcoin COre，称为Satoshi client。Exchanging "Inventory"一个全节点在连接上同僚后，要做的第一件事就是尝试创建完整的区块链。如果是个全新的节点，即没有区块链，那么它只有一个块，即创世区块，这是静态嵌入在客户端中的。从序列0开始，新的节点将从网络同步中下载成千上万的区块，并重新建立起完整的区块链。同步区块链的过程从版本消息开始，因为消息中有一个BestHeight(当前区块数量)。一个节点会从同僚的版本消息中看到它们都各自有多少区块，并能和自己所有的区块链进行比较。节点间会交换getblocks消息，该消息包含节点各自最新区块的hash，然后其中一个节点就能够识别该hash不是最新的，从而确定自己拥有的区块链比它同僚的区块链要长。拥有更长链的节点能够知道其它节点需要哪些区块来保持最新。它会识别差异中最早的500个块，并将它们的hash通过inv(inventory)消息发送出去，那些缺失这些块的节点就会接受这个消息(发送getdata消息来请求区块数据，并用inv消息中的hash来验证这些区块)。例如，假设一个节点只有创世区块，它会从它的同僚中收到inv消息包含有接下来的500个区块，然后它会从与它连接的同僚节点中请求这些区块(会负载均衡的方式去请求)，该节点会跟踪记录有多少区块在一个连接的传输中，避免请求超出限制MAX\_BLOCKS\_IN\_TRANSIT\_PER\_PEER。随着接收到每个区块，它会追加到自己本地区块链上。随着本地区块链逐步建立，更多的节点会被请求和接收到，直到和网络中其它节点保持数据同步。

​Simplified Payment Verification (SPV) Nodes不是所有节点都有能力存储完整的区块链，许多节点运行在空间和电力受限的设备比如手机平板嵌入式设备等。对这些设备，SPV方法被使用来让它们可以在不拥有完整区块链的情况下运作。这种类型的客户端称之为SPVclient或者轻客户端。随着比特币热潮，SPV节点称为了最常见的节点，特别是比特币钱包。SPV节点只下载区块头信息，不下载区块中的交易信息，如此，所需空间比完整区块链少了1000倍。SPV节点由于没有完整的UTXO，不能独立的知道还剩多少可以支出，于是使用了一种不同的方法，即需要依赖同僚节点来提供相关信息。一个恰当的比喻：一个全节点就像装备了完整的详细地图的旅行者来到一座陌生的城市。SPV节点则像陌生城市中只知道一条主路而其它街道信息得随机在街上找人问路的旅行者。虽然，两者都能够以实地访问具体某条街道的方式去验证这条街道是否存在，没有地图的旅行者完全不知道主道两边都有些什么街道。即使站在23号教堂街前，他也不知道是否还有其它的23号教堂街存在，对他而言，最好的机会就是问足够多的人得到足够多的信息确认，并希望这些人中没多少人在骗他。SPV verifies transactions by reference to their depth in the blockchain instead of their height. Whereas a full blockchain node will construct a fully verified chain of thousands of blocks and transactions reaching down the blockchain (back in time) all the way to the genesis block, an SPV node will verify the chain of all blocks (but not all transactions) and link that chain to the transaction of interest.SPV节点验证交易的方式是参考交易在区块链中的深度depth而不是高度height。当全节点需要构建一条完整的验证链(包括区块和交易数据)直到创世区块，SPV节点则只需验证区块数据，然后将相关的交易数据在关联到链上。。。。(这里没懂，看下面例子)例如，当需要验证区块300,000中的一条交易时，全节点会将所有300,000个区块都连接起来，并构建一个完整的UTXO数据库，验证该笔交易对应的UTXO还没被支出。然后SPV节点则没法验证该交易对应的UTXO是否被支出，它需要在交易数据和包含该交易的区块间建立连接(merkle path)。然后，SPV节点等待直到有6个新的区块追加到当前区块上300,001 through 300,006，则从侧面可以确认当前区块是有效的。The fact that other nodes on the network accepted block 300,000 and then did the necessary work to produce six more blocks on top of it is proof, by proxy, that the transaction was not a double-spend.​An SPV node cannot be persuaded that a transaction exists in a block when the transaction does not in fact exist.The SPV node establishes the existence of a transaction in a block by requesting a merkle path proof and by validating the Proof-of-Work in the chain of blocks. However, a transaction’s existence can be "hidden" from an SPV node. An SPV node can definitely prove that a transaction exists but cannot verify that a transaction, such as a double-spend of the same UTXO, doesn’t exist because it doesn’t have a record of all transactions. This vulnerability can be used in a denial-of-service attack or for a double-spending attack against SPV nodes. To defend against this, an SPV node needs to connect randomly to several nodes, to increase the probability that it is in contact with at least one honest node. This need to randomly connect means that SPV nodes also are vulnerable to network partitioning attacks or Sybil attacks, where they are connected to fake nodes or fake networks and do not have access to honest nodes or the real bitcoin network.​从实用性来讲，网络连接通畅的SPV节点安全性是足够的，这是在资源消耗与安全之间的一种平衡。但是要万无一失的保障安全的话，全节点是不会失败的。TipA full blockchain node verifies a transaction by checking the entire chain of thousands of blocks below it in order to guarantee that the UTXO is not spent, whereas an SPV node checks how deep the block is buried by a handful of blocks above it.SPV节点使用getheaders消息(而不是getblocks)来获取区块头。响应节点一次可以返回2000个区块头，这过程和全节点获取所有区块数据的过程是一样的。SPV节点还会设置一个filter来过滤应答节点发送的未来区块流和交易数据。用getdata请求来获取感兴趣的交易数据，应答的同僚节点则生成一个tx消息，包含所需交易信息。​由于SPV节点只取用户相关的交易数据，如果网络被第三方监控，通过跟踪这些被请求的交易数据，就可能关联获取到用户的比特币地址，从而一定程度上泄露用户隐私。SPV节点之后不久，开发者新加了一个特性叫做bloom filters来解决隐私风险。Bloom filters允许SPV节点接收所有交易数据的一个子集，而不是特定关心的交易数据（through a filtering mechanism that uses probabilities rather than fixed patterns）。Bloom Filters一个Bloom filter是一个概率搜索过滤器，一种描述一个想要的模式，但又不需要明确指明的方法。Bloom filters提供了一种高效的途径，能够在保护隐私的同时表达一种搜索模式。它们被SPV节点用来请求符合特定模式的交易数据，而同时不会泄露具体哪个地址或者交易是它们想要的。​在我们先前的比喻中，没有地图信息的游客在询问一个具体地址23号教堂大街的方位如果他向陌生人询问这个地点，则在不经意间泄露了他的目的地。如果使用bloom filter，则会类似于问，“有不有地方地名是以U-R-C-H结尾的呢？”这样提问，就会比直接问一个具体的地址泄露更少的目的地信息。通过调整搜索的精确度，游客就可以控制泄露信息的多少，当然结果也会变得没那么精确了。Bloom filters就是为SPV节点提供了这样一种功能。​How Bloom Filters WorkBloom filters are implemented as a variable-size array of N binary digits (a bit field) and a variable number of M hash functions. The hash functions are designed to always produce an output that is between 1 and N, corresponding to the array of binary digits. The hash functions are generated deterministically, so that any node implementing a bloom filter will always use the same hash functions and get the same results for a specific input. By choosing different length (N) bloom filters and a different number (M) of hash functions, the bloom filter can be tuned, varying the level of accuracy and therefore privacy.​In An example of a simplistic bloom filter, with a 16-bit field and three hash functions, 例子中我们使用16bit的数组和3个hash函数来做演示。​​bloom filter初始化时，bit数组中所有为都是0，要添加一个模式到bloom filter时，这个模式依次被每个hash函数计算哈希，哈希的结果是1到N(N为数组长度)的数字，响应的数组中该位则会被置为1，从而记录着hash函数的输出。这样直到所有的M个哈希函数都被计算完毕，则该搜索模式就被记录在位数组中了。​Adding a pattern "A" to our simple bloom filter is an example of adding a pattern "A" to the simple bloom filter shown in An example of a simplistic bloom filter, with a 16-bit field and three hash functions.再次添加一个模式到Bloom filter的过程同上，模式会被所有函数依次哈希计算，然后结果会以相应位被置1的方式被记录在位数组中。注意到随着bloom filter被添加越多的搜索模式，hash的结果会发生碰撞，即一个位可能多次被置1，即不变。本质上，随着越多的模式重叠发生越多，bloom filter会越来越饱和，然后搜索的精确性就随之降低。这就是为什么Bloom filter是一种概率数据结构了，随着越多的模式加入，filter会越发不精准。The accuracy depends on the number of patterns added versus the size of the bit array (N) and number of hash functions (M). A larger bit array and more hash functions can record more patterns with higher accuracy. A smaller bit array or fewer hash functions will record fewer patterns and produce less accuracy. ---这里没懂，按上面的说法，M个哈希函数都要参与计算的，M越大，则会越多的位被置1，从而导致精度丢失才对啊。​​​​要测试一个模式是否是bloom filter的一部分，可以将模式通过M个哈希函数计算，然后和位数组匹配，如果所有相关的位都置1了，那么我们认为这个模式很可能是bloom filter的一部分，这是有概率的。​反过来，要测试一个模式不属于filter的一部分则是确定性的​​How SPV Nodes Use Bloom FiltersBloom filters 被SPV节点用来在从同僚节点接收交易数据时进行过滤，只选择感兴趣的交易数据，但同时又不会泄露具体感兴趣的地址。An SPV node will initialize a bloom filter as "empty"; in that state the bloom filter will not match any patterns. The SPV node will then make a list of all the addresses, keys, and hashes that it is interested in. It will do this by extracting the public key hash and script hash and transaction IDs from any UTXO controlled by its wallet. The SPV node then adds each of these to the bloom filter, so that the bloom filter will "match" if these patterns are present in a transaction, without revealing the patterns themselves.就是SPV节点会利用自己钱包所控制的所有UTXO数据来提取出地址、keys、hash等，再添加到搜索模式中，然后以这些模式去全节点中取数据，这样就避免了泄露单一地址信息。SPV节点将会发送一条filterload消息到同僚节点，该消息即包含bloom filter。然后，同僚节点在发送交易数据时就会用此bloom filter来进行检查过滤了。全节点会根据bloom filter来检查交易数据的如下几部分：交易ID每条交易输出的locking script的数据结构，每个key和hash每条交易输入每条交易输入的签名结构​By checking against all these components, bloom filters can be used to match public key hashes, scripts, OP\_RETURN values, public keys in signatures, or any future component of a smart contract or complex script.​通过检查这些部分，bloom filters就能够用来匹配公钥hash、scripts、OP\_RETURN values等等​在filter建立起来后，同僚节点会将每笔交易的输出和filter进行比较，只有匹配filter的交易才会被发送给请求节点。在回应节点发出的getdata消息时，同僚节点将发送一条merkleblock消息，这消息只包含匹配区块的区块头信息以及匹配交易的merkle path信息。然后同僚节点还会发送一条tx消息，包含着匹配到的交易数据。全节点将交易数据发送给SPV节点时，SPV节点会丢弃不相关的，然后使用相关的交易数据来建立更新自己的UTXO集合和钱包收支数据，随着它更新自己数据集的同时，SPV节点可以动态调整bloom filter来进行后面的数据获取工作，如此这般。SPV节点可以通过发送filteradd消息来动态的添加搜索匹配模式。也可以使用filterclear消息来清除filter。因为bloom filter是不能去掉其中一个模式的，所以，只能先clear，再添加的方式运作。The network protocol and bloom filter mechanism for SPV nodes is defined in BIP-37 (Peer Services).Encrypted and Authenticated Connections大多数比特币新用户以为比特币节点的通讯是加密的。实际上最初的比特币实现中，通讯是明文的。这对全节点来说没啥隐私问题，但对SPV来说却是个大问题。为了增加比特币网络的隐私和安全性，有两种加密通讯方案：Tor Transport and P2P Authentication and Encryption with BIP-150/151。。。。这两种没详细说。。。。

Transaction Pools几乎比特币上的所有节点都维护着一个暂时的未确认交易数组，称为mempool或transaction pool。节点用这个池子来记录着网络感知到但还没包含到一个区块中的交易。例如，钱包节点会使用这个池子中的交易数据来跟踪用户钱包接收到的但还未确认的资金情况。随着交易信息被接受和确认，它们被追加到transaction pool中，并中继给其它相邻节点再传播到整个网络。Some node implementations also maintain a separate pool of orphaned transactions. If a transaction’s inputs refer to a transaction that is not yet known, such as a missing parent, the orphan transaction will be stored temporarily in the orphan pool until the parent transaction arrives.​When a transaction is added to the transaction pool, the orphan pool is checked for any orphans that reference this transaction’s outputs (its children). Any matching orphans are then validated. If valid, they are removed from the orphan pool and added to the transaction pool, completing the chain that started with the parent transaction. In light of the newly added transaction, which is no longer an orphan, the process is repeated recursively looking for any further descendants, until no more descendants are found. Through this process, the arrival of a parent transaction triggers a cascade reconstruction of an entire chain of interdependent transactions by re-uniting the orphans with their parents all the way down the chain.Both the transaction pool and orphan pool (where implemented) are stored in local memory and are not saved on persistent storage; rather, they are dynamically populated from incoming network messages. When a node starts, both pools are empty and are gradually populated with new transactions received on the network.Some implementations of the bitcoin client also maintain an UTXO database or pool, which is the set of all unspent outputs on the blockchain. Although the name "UTXO pool" sounds similar to the transaction pool, it represents a different set of data. Unlike the transaction and orphan pools, the UTXO pool is not initialized empty but instead contains millions of entries of unspent transaction outputs, everything that is unspent from all the way back to the genesis block. The UTXO pool may be housed in local memory or as an indexed database table on persistent storage.Whereas the transaction and orphan pools represent a single node’s local perspective and might vary significantly from node to node depending upon when the node was started or restarted, the UTXO pool represents the emergent consensus of the network and therefore will vary little between nodes. Furthermore, the transaction and orphan pools only contain unconfirmed transactions, while the UTXO pool only contains confirmed outputs.​第九章 区块链Introduction区块链是一种数据结构，有序，然后每个指向其前一个(back-linked)的由交易数据组成的块。可以存储在文件中，或者数据库中。Bitcoin Core client是将区块链信息存储在levelDB中的。区块链通常被形象想象为垂直堆栈模型，因为每个块建立在其它块之上，创世区块为栈底；也正是这种类比，因此用height来形容区块离第一个块的距离，top或tip则指代最新添加的那个区块。区块链上的每一个区块都是由一个hash值唯一确定，该hash由sha256计算区块头得来。每个区块同样会通过区块头中的"previous block hash"字段指向前一个区块，即父区块。换言之，每个区块在其头部包含着其父区块的hash，如此形成一条链，直到最初的创世区块。虽然每个块只能有一个父块(因为只有一个previous block hash字段)，但可能会短暂的出现多个子块的情况，这种情况会发生在blockchain fork阶段，但是最终，fork会被解决，链不会形成分叉。previous block hash字段由于存在区块头中，因此会影响当前块的hash值。于是，如果父块的hash身份发生改变，则子块需要跟着变，依次会形成级联效应。这种级联关系保证了一旦某个区块产生了很多代子区块，如果要改变它，则需要强制重新计算其后代所有区块，而区块链的机制决定这种计算是需要消耗不可估计的计算力和能量的，从而意味着区块链上的历史区块几乎不能改变，这是区块链的一个重要特性。

可以将区块链想象成地质结构中的不同层次，表层可能会随着季节变化而变化，甚至可能在还没完全落定时被狂风吹走。但是一旦往下深入几层，地质结构就会越来越稳定。当你往下看几百英尺时，你也能看到已经存在了几百万年的地址快照。在区块链中，最新添加的几个区块可能会在有分支出现的情况下重新被计算和修订。最上面的6个区块就像地质结构中的表层。一旦你的深度超过了6个区块，则越来越不太可能发生变动了。Structure of a Block一个区块是一个容器数据结构，负责整合交易数据并添加到区块链上去。区块由区块头(包含metadata)和紧接着的一序列交易数据组成，头部有80字节，而平均一条交易有400字节，平均一个区块可以包含1900条交易。因此，一个完整的区块会比单纯的一个区块头大差不多一万倍。SizeFieldDescription4 bytesBlock SizeThe size of the block, in bytes, following this field80 bytesBlock HeaderSeveral fields form the block header1–9 bytes (VarInt)Transaction CounterHow many transactions followVariableTransactionsThe transactions recorded in this blockBlock Header区块头由三组meta数据组成。首先是指向前一个区块的hash，此值将本区块和前一区块相连；第二部分是difficulty、timestamp、nonce，这和挖矿竞争有关；第三部分是merkle tree root，用来高效归总区块中所有交易数据的数据结构。SizeFieldDescription4 bytesVersionA version number to track software/protocol upgrades32 bytesPrevious Block HashA reference to the hash of the previous (parent) block in the chain32 bytesMerkle RootA hash of the root of the merkle tree of this block’s transactions4 bytesTimestampThe approximate creation time of this block (seconds from Unix Epoch)4 bytesDifficulty TargetThe Proof-of-Work algorithm difficulty target for this block4 bytesNonceA counter used for the Proof-of-Work algorithmThe nonce, difficulty target, and timestamp are used in the mining process and will be discussed in more detail in [mining].Block Identifiers: Block Header Hash and Block Height一个区块的主要标识符是它的hash或叫做电子指纹，区块头通过sha256两次计算得来。结果产生的32字节hash值称为block hash，或者更精确的block header hash。比如创世区块的hash是： 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f。区块的hash唯一确定一个区块，但是注意到，block hash实际并不包含在区块的数据结构中，既不会在区块传输中存在，也不会在持久化存储中作为区块链的一部分。它只是在每个节点接收到该区块是计算得到，block hash一般会被分开存储在节点的数据库中，作为该区块的metadata的一部分，用来辅助索引和快速提取区块等工作。区块的另外一种标识是它在区块链中的位置序号，称为block height。创世区块的height是0，后续添加则序号依次加1.与block hash不同，区块高度不是唯一标识符。虽然一个区块总会对应一个块高，但是反过来则不同，即一个块高并不总是只对应一个区块。在有分支的情况下回出现。同样的，块高也不是区块数据结构中的一部分。TipA block’s block hash always identifies a single block uniquely. A block also always has a specific block height. However, it is not always the case that a specific block height can identify a single block. Rather, two or more blocks might compete for a single position in the blockchain.The Genesis Block创世区块是2009年创建的。是区块链上所有区块的共同祖先，即不管你从链上那个区块开始，顺着链往父节点找，总能达到创世区块。每个节点都会一开始都会至少拥有一个区块，即创世区块，因为它是硬编码的，不可变更。所有节点都知道创世区块的hash和结构，被创建的时间，甚至里面的唯一一条交易。See the statically encoded genesis block inside the Bitcoin Core client, in chainparams.cpp.The following identifier hash belongs to the genesis block:000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26fYou can search for that block hash in any block explorer website, such as blockchain.info, and you will find a page describing the contents of this block, with a URL containing that hash:​https://blockchain.info/block/000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f​Using the Bitcoin Core reference client on the command line:​A merkle path used to prove inclusion of a data element​A merkle path used to prove inclusion of a data element​​Duplicating one data element achieves an even number of data elements​$ bitcoin-cli getblock 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f{    "hash" : "000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f",    "confirmations" : 308321,    "size" : 285,    "height" : 0,    "version" : 1,    "merkleroot" : "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b",    "tx" : [        "4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b"    ],    "time" : 1231006505,    "nonce" : 2083236893,    "bits" : "1d00ffff",    "difficulty" : 1.00000000,    "nextblockhash" : "00000000839a8e6886ab5951d76f411475428afc90947ee320161bbf18eb6048"}Linking Blocks in the Blockchain全节点维护者区块链的完全拷贝。这份本地的拷贝数据会随着在网络中发现新区块而被频繁更新，并追加新区块到链上。当节点收到来自网络的新区块时，它会校验这些区块，并把它们链接到区块链上。为了建立正确的链接，节点会检查接收到的区块的头部信息，找到‘previous block hash’字段。​假如，一个节点上的区块链有277,314个区块，则最后一个区块是277,314，它的block hash是：00000000000000027e7ba6fe7bad39faf3b5a83daed765f05f7d1b71a1632249然后这个节点从网络中接收了一个新区块：{    "size" : 43560,    "version" : 2,    "previousblockhash" :        "00000000000000027e7ba6fe7bad39faf3b5a83daed765f05f7d1b71a1632249",    "merkleroot" :        "5e049f4030e0ab2debb92378f53c0a6e09548aea083f3ab25e1d94ea1155e29d",    "time" : 1388185038,    "difficulty" : 1180923195.25802612,    "nonce" : 4215469401,    "tx" : [        "257e7497fb8bc68421eb2c7b699dbab234831600e7352f0d9e6522c7cf3f6c77",​ #[... many more transactions omitted ...]​        "05cfd38f6ae6aa83674cc99e4d75a1458c165b7ab84725eda41d018a09176634"    ]}通过解析这个区块，节点发现它的previousblockhash字段的值在链上是有的，且正好为最后一个块，因此，接收到的新块即为277,314块的子块，于是接收到的区块会追加到链上，称为277,315块。​​Merkle Trees链上的每个区块通过merkle tree来包含该块内的所有交易数据的摘要。一个merkle tree，又称为二进制hash树(a binary hash tree)，是一种用来快速摘要和验证大数据集完整性的数据结构。Merkle tree在比特币中被用来摘要一个区块中的所有交易数据，生成一个总体的电子指纹，提供了一种非常高效的验证一条交易是否被区块包含的途径。Merkle tree是通过递归的hash一对节点，直到收敛为一个hash节点，即merkle root。用到的加密hash算法是sha256两次计算，即double-sha256.When N data elements are hashed and summarized in a merkle tree, you can check to see if any one data element is included in the tree with at most 2\*log~2~(N) calculations, making this a very efficient data structure.​Merkle tree是从底部开始构建的，在下面的例子中，我们从4笔交易开始，A、B、C、D，这四笔交易则为merkle tree的叶子节点，交易数据本身不存在merkle tree中，而是它们的hash值，则，最终的4个叶子节点为HA, HB, HC, and HD：HA = SHA256(SHA256(Transaction A))连续的两个节点再组队，hash值拼接起来，继续double-hash而成父节点：HAB = SHA256(SHA256(HA + HB))这个过程一直执行，直到剩下一个节点，成为merkle root。这个32字节的hash则被存储在区块头中，它是所有交易数据的摘要。​​由于merkle tree是二进制树，它需要偶数个叶子节点，如果只有奇数条交易数据，则最后一条交易数据会被复制一份，成为a balanced tree. ​​要证明一个指定的交易存在于一个区块中，只需生成log~2~(N)个32字节的hash，这些hash值构成该条交易通向merkle tree root的认证路径或者叫做merkle path。这就高级了，可以非常高效的在大量交易数据中定位具体一条交易数据。​​​​​​这部分感觉需要看源码，没明白这个merkleblock message的生成细节。​第十章 挖矿和共识机制介绍挖矿一词有一定的误导性，通过和稀有金属的开采过程联系，挖矿一词会将我们的注意力集中到得到的回报上，即新产生的比特币。虽然挖矿是靠比特币来激励维持的过程，但它的本质目的并不是激励本身，也不是新币发行。挖矿是支撑整个比特币这个去中心化清算所的一种机制，通过它来验证和清算交易记录。挖矿这个发明，使得比特币因之而特别，它是一种去中心化的安全机制，是点对点电子资金系统的基础。​挖矿机制保障着比特币系统的安全性，并使得在没有中央集权的模式下达到全网共识。而挖矿中的新出货币和交易费则是整个过程的激励机制，使得矿工能够甘愿付出来维护整个网络的安全，同时也给系统提供新币的来源。TipThe purpose of mining is not the creation of new bitcoin. That’s the incentive system. Mining is the mechanism by which bitcoin’s security is decentralized.挖矿的目的不是开采新币，这属于激励机制的范围。挖矿本身只是使得比特币系统的安全性能够去中心化的一种机制。矿工会校验新的交易记录，并记录在全球账本中。一个新的区块会包含上个区块开采以后的所有交易记录，新区块差不多每10分钟会被开采出来，而随着这个过程，里面所包含的交易记录就被添加到了区块链上。交易被添加到区块链上即意味着该交易得到了确认，从而使得从这些交易获取到比特币的所有者能够花销他们的比特币了。矿工会在挖矿(保障网络安全机制)的过程中得到两类回报：一是随着每个区块被开采时发行的新币，二是该区块的每笔交易过程中所产生的交易费。为了赢得报酬，矿工竞争来计算一个基于加密哈希算法的数学问题，谁先计算找到答案，则谁获得这些报酬(PoW过程，最终计算出来的值会被包含到新的区块中，它是矿工工作量的证明)，并获取将这些交易记录到区块链上的权利，这就是比特币安全模型的基础。整个过程之所以称为挖矿，是因为它模拟了自然开采稀有金属的过程，即开采了是逐渐减少的。比特币网络的资金发行是通过挖矿，就类似中央银行发行纸币一样。矿工在成功挖到区块后所能获得的最大量新币额度差不多每四年减半(准确的说是每隔210000区块)，直到2140年后，就不会再由新币产生。​在本章中，我们首先学习挖矿作如何为一种货币供应机制，然后看看挖矿最重要的功能：去中心化共识机制。Bitcoin Economics and Currency Creation比特币是在每个区块被创建时铸造出来的，稳步缩减。每个区块平均10分钟产生，没210000个区块，大约四年，每次产生新币的数量减半。最初的四年，没产生一个区块，则有新币50btc。在2012年，新币发行速率缩减到每个区块25btc，在2016年7月缩减到12.5btc，直到差不多2140年后，不会有新币产生，则矿工的奖励只靠交易费来源。​​这种有限且紧缩的发行机制创建了固定的货币供应机制来对抗通货膨胀；不像法定货币能够被无限制的由中央银行印制发行。通货紧缩的货币比特币固有缩减机制导致了它本质上是通货紧缩的。通货紧缩现象：货币的供需(需求大于供给)不匹配导致价值升值，与通货膨胀相反，价值的通货紧缩意味着资金会随着时间而购买力增强。于是，许多经济学家争议说通货紧缩的经济是灾难，需要不惜代价的避免。因为在通货紧缩时期，人们会囤积货币而不是花费它们。​Bitcoin experts argue that deflation is not bad per se. Rather, deflation is associated with a collapse in demand because that is the only example of deflation we have to study. In a fiat currency with the possibility of unlimited printing, it is very difficult to enter a deflationary spiral unless there is a complete collapse in demand and an unwillingness to print money. Deflation in bitcoin is not caused by a collapse in demand, but by a predictably constrained supply.The positive aspect of deflation, of course, is that it is the opposite of inflation. Inflation causes a slow but inevitable debasement of currency, resulting in a form of hidden taxation that punishes savers in order to bail out debtors (including the biggest debtors, governments themselves). Currencies under government control suffer from the moral hazard of easy debt issuance that can later be erased through debasement at the expense of savers.It remains to be seen whether the deflationary aspect of the currency is a problem when it is not driven by rapid economic retraction, or an advantage because the protection from inflation and debasement far outweighs the risks of deflation.Decentralized Consensus在前一章节，我们了解了区块链(比特币中所有交易记录的全球账本)，网络中所有用户都接受的所有权的权威记录。​但是如何在不能信任任何人的情况下，全网用户能够达成共识，接受唯一一个真理呢？所有传统支付系统都依赖一个信任模型即中央机构来提供清算服务，即验证并清算所有交易。比特币网络没有中央机构，但是所有全节点都拥有一份完整的可被信任的全球账本副本。区块链并不由中央机构创建，而是独立的被网络中各个节点所收集起来。网络中每个节点根据在非安全网络中接收到的信息，能够达成共识，并共同维护着相同的全球账本。本章会介绍这些共识过程的细节。​Satoshi Nakamoto’s main invention is the decentralized mechanism for emergent consensus. Emergent, because consensus is not achieved explicitly—there is no election or fixed moment when consensus occurs. Instead, consensus is an emergent artifact of the asynchronous interaction of thousands of independent nodes, all following simple rules. All the properties of bitcoin, including currency, transactions, payments, and the security model that does not depend on central authority or trust, derive from this invention.中本聪的主要发明是 紧急共识的去中心化机制。紧急的意思是，共识不是确定获得的，没有选举或者固定时刻来产生共识。相反，共识是由成千上万的节点都遵守相同的规则来异步交互达成的。比特币网络的所有特性，包括资金、交易、支付、不基于中央机构的安全信任模型，都是从这个发明衍生的。

比特币网络的去中心化共识是由四个过程的相互作用产生的，这四个过程在全网所有节点上各自独立运作：独立的交易验证，发生在所有全节点上独立的整合这些交易到新区块中，这由挖矿节点完成，附带着有工作量证明的计算结果独立的新区块验证，及追加到区块链上独立的选择最长链作为最终区块链，这是所有节点参与的。接下来部分会介绍这些过程。Independent Verification of Transactions在交易章节，我们看到了比特币钱包创建交易的过程：收集UTXO，提供unlocking scripts，然后创建新的outputs来指定新的资产拥有者。然后生成的交易记录会被发送给周围节点然后传播到整个网络。但是，在分发交易到同僚节点前，每个比特币节点都需要首先验证交易是否有效，这保证了只有合法的交易才会传播到整个网络，而非法的交易会在第一个遇到它的节点中就被丢弃。交易验证过程是遵循了一长串checklist:交易数据的语法和数据结构正确交易的输入输出都不为空交易记录的大小要小于 MAX\_BLOCK\_SIZE.每笔output以及总和都要小于21m btc，总数None of the inputs have hash=0, N=–1 (coinbase transactions should not be relayed).nLocktime is equal to INT\_MAX, or nLocktime and nSequence values are satisfied according to MedianTimePast.The transaction size in bytes is greater than or equal to 100.The number of signature operations (SIGOPS) contained in the transaction is less than the signature operation limit.The unlocking script (scriptSig) can only push numbers on the stack, and the locking script (scriptPubkey) must match IsStandard forms (this rejects "nonstandard" transactions).A matching transaction in the pool, or in a block in the main branch, must exist.For each input, if the referenced output exists in any other transaction in the pool, the transaction must be rejected.(在交易章节我们知道，vin由指定的交易、对应的vout等等组成，这里的意思是，如果当前交易的vin所指定的交易vout还在pool中，即上一笔交易还没被确认，则当前交易是不能有效的)For each input, look in the main branch and the transaction pool to find the referenced output transaction. If the output transaction is missing for any input, this will be an orphan transaction. Add to the orphan transactions pool, if a matching transaction is not already in the pool.For each input, if the referenced output transaction is a coinbase output, it must have at least COINBASE\_MATURITY (100) confirmations.For each input, the referenced output must exist and cannot already be spent.（即需要在UTXO集中）Using the referenced output transactions to get input values, check that each input value, as well as the sum, are in the allowed range of values (less than 21m coins, more than 0).Reject if the sum of input values is less than sum of output values.Reject if transaction fee would be too low (minRelayTxFee) to get into an empty block.The unlocking scripts for each input must validate against the corresponding output locking scripts.These conditions can be seen in detail in the functions AcceptToMemoryPool, CheckTransaction, and CheckInputs in Bitcoin Core. Note that the conditions change over time, to address new types of denial-of-service attacks or sometimes to relax the rules so as to include more types of transactions.By independently verifying each transaction as it is received and before propagating it, every node builds a pool of valid (but unconfirmed) transactions known as the transaction pool, memory pool, or mempool.通过节点在传播交易之前的独立验证，节点会维护一个合法的交易数据池，称之为transaction pool、mempool。Mining Nodes比特币网络中有一些专有节点，称之为矿工节点。在第一章我们介绍了Jing，就是一名比特币矿工，他通过运行专业的硬件设备来挖矿赚取比特币。Jing的连接到一个全节点，当然还有一些矿工没有全节点(在Mining Pools部分见)，和其它所有全节点一样，JIng的节点会接收并传播网络中所有未确认的交易数据，Jing的节点还会将这些数据整个近新的区块中。Jing的节点也和其它节点一样，时刻监听网络中新节点的到来。但是，这对矿机来说有着特别的意义。矿工之间的竞争随着新节点的到来而结束，因为新节点意味着有赢家诞生。所以，接收到一个有效的区块意味着有人在这轮竞争中胜出了而他们失败了，但是呢，上一轮的结束也同时意味着下一轮竞争的开始。Aggregating Transactions into Blocks在验证交易合法之后，节点会将交易添加到mempool中，这里所有交易等待被打包进一个区块。Jing的节点同样会收集、验证、并传播所有的交易数据，这功能和所有节点一样，不同于其他节点的是，jing的节点会在合适的阶段将这些交易数据整合进一个候选区块中。同样以Alice向Bob买咖啡时的交易为例，Alice的交易被包含在277316的区块中，我们假设这个区块被Jing的节点率先打包。Jing的矿机节点维护着整个区块链的完整副本，在Alice付款时，Jinig的节点刚好集合了277,314个区块，Jing的节点继续监听网络中的交易数据，努力挖掘新的区块，也同时发现被其它节点发掘的新区块。正当Jing的节点在挖矿的同时，它接收到了网络中的第277,315个区块，该区块的到达意味着对区块277,315的竞争结束，也意味着下一区块挖矿竞争的开始。在过去的十分钟，当Jing的节点在计算区块277,315的答案时，它同时也在收集下一区块所需的交易数据，到此刻，它的mempool中已经收集了几百笔交易。当收到区块277,315，并验证通过之后，Jing的节点会将该区块中的所有交易和它自己的mempool中收集的交易数据匹配去重，剩下的mempool中还未被确认的交易数据则会被等待打包进下一区块。Jing的节点立刻创建一个空的区块，即候选区块277,316，之所以称之为代理区块，因为它还不是一个合法区块啊。区块只有在矿工成功找到PoW的答案时，才能变成合法。当Jing的节点整合mempool中的所有交易时，发现有418笔交易共0.09094928btc的交易费。The Coinbase Transaction任何区块的第一笔交易比较特别，称为coinbase transaction，这是由Jing的节点创建的，里面包含了开采此区块的奖励。NoteWhen block 277,316 was mined, the reward was 25 bitcoin per block. Since then, one "halving" period has elapsed. The block reward changed to 12.5 bitcoin in July 2016. It will be halved again in 210,000 blocks, in the year 2020.Jing的节点创建一笔由coinbase支付给它自己钱包的一笔交易：‘支付Jing的地址25.09094928btc’，支付总额为coinbase奖励(25新的btc)和所有该区块包含的交易产生的交易费。

Example 4. Coinbase transaction$ bitcoin-cli getrawtransaction d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f 1{    "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0f03443b0403858402062f503253482fffffffff0110c08d9500000000232102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac00000000",    "txid" : "d5ada064c6417ca25c4308bd158c34b77e1c0eca2a73cda16c737e7424afba2f",    "version" : 1,    "locktime" : 0,    "vin" : [        {            "coinbase" : "03443b0403858402062f503253482f",            "sequence" : 4294967295        }    ],    "vout" : [        {            "value" : 25.09094928,            "n" : 0,            "scriptPubKey" : {                "asm" : "02aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21OP\_CHECKSIG",                "hex" : "2102aa970c592640d19de03ff6f329d6fd2eecb023263b9ba5d1b81c29b523da8b21ac",                "reqSigs" : 1,                "type" : "pubkey",                "addresses" : [                    "1MxTkeEP2PmHSMze5tUZ1hAV3YTKu2Gh1N"                ]            }        }    ]}和常规的交易不同，coinbase交易并不会花销UTXO作为inputs。它只有一个input，称为coinbase，即创建全新的比特币；coinbase交易也只有一个output，支付给挖矿者自己的比特币地址。比如在这个例子中，coinbase支付给Jing的地址1MxTkeEP2PmHSMze5tUZ1hAV3YTKu2Gh1N一共25.09094928btc。Coinbase Reward and Fees为了创建这笔coinbase交易，Jing的节点首先计算所有交易产生的交易费：Total Fees = Sum(Inputs) - Sum(Outputs)In block 277,316, the total transaction fees are 0.09094928 bitcoin.然后，Jing的节点计算正确的开采奖励金，这笔奖励是基于块高(block height)计算的，从50btc开始，每隔21w区块减半：CAmount GetBlockSubsidy(int nHeight, const Consensus::Params& consensusParams){    int halvings = nHeight / consensusParams.nSubsidyHalvingInterval;    // Force block reward to zero when right shift is undefined.    if (halvings >= 64)        return 0;​    CAmount nSubsidy = 50 \* COIN;    // Subsidy is cut in half every 210,000 blocks which will occur approximately every 4 years.    nSubsidy >>= halvings;    return nSubsidy;}The initial subsidy is calculated in satoshis by multiplying 50 with the COIN constant (100,000,000 satoshis). This sets the initial reward (nSubsidy) at 5 billion satoshis.TipIf Jing’s mining node writes the coinbase transaction, what stops Jing from "rewarding" himself 100 or 1000 bitcoin? The answer is that an incorrect reward would result in the block being deemed invalid by everyone else, wasting Jing’s electricity used for Proof-of-Work. Jing only gets to spend the reward if the block is accepted by everyone.Structure of the Coinbase Transaction通过这些计算，Jing的节点最终会创建一笔coinbase交易支付给他自己25.09094928btc。正如所见，coinbase交易有种特殊的格式；它的vin并没有指向一笔之前的UTXO，而是有一笔coinbase input。我们来和常规的交易做个对比吧：

​在coinbase交易中，前面两个字段被设置为不指向特定UTXO的值，即transaction hash字段全部设置为32字节的0，output序号被设置为4字节的0xFF，unlocking script则由coinbase data代替。Coinbase Datacoinbase交易没有unlocking script(scriptSig)，取而代之的是coinbase data，2~100字节长度，除了最初的几个字节，剩下的数据由挖矿者自己决定，可以是任何数据。比如在创世区块中，中本聪写的是 "The Times 03/Jan/2009 Chancellor on brink of second bailout for banks"。 当前，挖矿者使用coinbasedata来存放额外的nonce值和区分矿池的字符串数据等。前面的几个字节在一开始是任意的，但从BIP-34后，version2的区块必须将块高信息包含到coinbase data字段的开始几个字节。在区块277,316中，我们看到coinbase data字段包含16进制的数值03443b0403858402062f503253482f，我们来解码看看：第一个字节03指示脚本执行引擎将接下来的03个字节push到脚本栈中接下来的三个字节443b04，即0x443b04表示的是little-endian(backward, least-significant byte first)格式的块高，将字节反转过来即为0x043b44，即十进制的277,316。接下来的几个字节0385840206用来编码额外的nonce，即随机数，这是用来找到PoW答案的。剩下的数据2f503253482f是ASCII编码的/P2SH/，暗示开采这个区块的节点支持P2SH协议(BIP-16中见)。Constructing the Block Header为了创建区块头，挖矿的节点需要填充6个字段：SizeFieldDescription4 bytesVersionA version number to track software/protocol upgrades32 bytesPrevious Block HashA reference to the hash of the previous (parent) block in the chain32 bytesMerkle RootA hash of the root of the merkle tree of this block’s transactions4 bytesTimestampThe approximate creation time of this block (seconds from Unix Epoch)4 bytesTargetThe Proof-of-Work algorithm target for this block4 bytesNonceA counter used for the Proof-of-Work algorithm在277,316区块被开采的时间，描述区块结构的版本号是2，用little-ending 表示则为0x02000000第二个字段prevhash则为上一个区块277,315的区块头的hash值：0000000000000002a7bbd25a417c0374cc55261021e8a9ca74442b01284f0569TipBy selecting the specific parent block, indicated by the Previous Block Hash field in the candidate block header, Jing is committing his mining power to extending the chain that ends in that specific block. In essence, this is how Jing "votes" with his mining power for the longest-difficulty valid chain.接下来则是对所有的419笔交易生成merkle tree，然后才能将merkle root填充到第三个字段。coinbase交易作为第一笔交易，接下来的418笔交易追加其后。正如在merkle tree章节介绍的一样，由于交易节点非偶数，最后一笔交易会被复制，从而一共有420个叶子节点，每个节点即为对应交易数据的hash，直到最终生成merkle root:c91c008c26e50763e9f548bb8b2fc323735f73577effbc55502c51eb4cc7cf2eJing’s mining node will then add a 4-byte timestamp, encoded as a Unix "epoch" timestamp, which is based on the number of seconds elapsed from January 1, 1970, midnight UTC/GMT. The time 1388185914 is equal to Friday, 27 Dec 2013, 23:11:54 UTC/GMT.然后是target字段, which defines the required Proof-of-Work to make this a valid block. The target is stored in the block as a "target bits" metric, which is a mantissa-exponent encoding of the target. The encoding has a 1-byte exponent, followed by a 3-byte mantissa (coefficient). In block 277,316, for example, the target bits value is 0x1903a30c. The first part 0x19 is a hexadecimal exponent, while the next part, 0x03a30c, is the coefficient. The concept of a target is explained in Retargeting to Adjust Difficulty and the "target bits" representation is explained in Target Representation.The final field is the nonce, which is initialized to zero.随着所有的头部字段被填充，挖矿的过程就可以真正开始了。目标是找到一个nonce值，使得区块头的hash值小于target。挖矿节点将需要几十亿甚至更多的静思园才能找到一个合适的nonce。Mining the Blocksha256方法被用来挖矿。简单来说，挖矿的过程就是在只改变nonce的情况下，重复hash区块头，直到找到合适的nonce值满足小于target。hash的结果不能被提前确定，也没有固定的pattern，这种特性使得唯一的办法就是比拼算力。Proof-of-Work Algorithm哈希算法是以任意长度的数据作为输入，能产生固定长度的确定性的结果，即针对输入的电子签名。对任何确定的输入，其产生的结果一定是相同的，且能够非常容易的被验证。加密哈希的一个重要特性是：要找到两个输入能产生同样的输出结果在计算上是不可行的(即哈希碰撞很难发生)。由此可以推理，要产生想要的输出结果，几乎是不可能由自己选择输入数据来决定的，唯一可行的办法就是尝试随机输入。在比特币中，添加随机因子的字段称为nonce，即从0开始一直自增，知道找到合适的值能产生满足target的hash。​那么如果以这个算法来设置一种挑战的话，我们可以这样设定目标：找到一个字符串使得产生的16进制hash以一个0开始，从实验结果可以看到，这并不难，比如要哈希‘I am Satoshi Nakamoto’的话，经过13此迭代，即‘I am Satoshi Nakamoto13’的hash为0ebc56d59a34f5082aaef3d66b37a661696c2b618e62432727216ba9531041a5，从而满足了要求。从概率而言，如果哈希方法的输出是均匀分布的，我们应该可以从每16个hash中找到一个已0开头的值(0~F)。从数值的角度，以0开头，意味着数值小于0x1000000000000000000000000000000000000000000000000000000000000000，我们把这个阈值称之为target，于是我们的目标即为找到小于target的hash值。可以看到，如果我们调整target，其值越小，则越难找到一个这样的hash值。​简单类比一下，想象这么一个游戏，玩家重复投掷一对骰子，以尝试获得一个小于target的点数。在第一轮，我们把target设为12，则，除非你两个骰子都是6点，否则你都赢了。在接下来一轮，我们把target设为11，则玩家获得的点数最多不能大于10，同样也是比较容易得到的。如果在几轮之后，我们把target设为5，现在你会发现会有一半以上的几率你骰子的点数会超过5。即随着target越来越小，需要投掷的尝试次数会指数级增加。最后，当target是2的时候，概率上每36次才会产生一次满足条件的机会。​从观察者的角度来看这个问题，他知道target是2，那么当一个人赢了，则观察者可以很好的假设他平均尝试了36次。换句话说，根据target的设置，我们能够预估要解决此target需要付出的工作量。这就是Proof-of-work。TipEven though each attempt produces a random outcome, the probability of any possible outcome can be calculated in advance. Therefore, an outcome of specified difficulty constitutes proof of a specific amount of work.在上面提到的实验中，一个赢得比赛的nonce是13，这个结果是能够被任何人独立验证的，只需将13拼接到字符串‘I am Satoshi Nakamoto’后面，然后计算其hash，比对结果真的小于target即可验证。这个结果也是工作量证明，因为它证明了我们是干活了的才最终找到了这个nonce。我们需要花13次hash计算来找到合适的nonce，但只需要一次hash计算即可验证结果正确性。如果我们把target设置更低(难度更高)，则就需要更多的hash计算来找到合适的nonce，但对于验证来说也是只需要一次hash计算即可。此外，通过target的设置，任何人都可以用概率模型预估计算难度，即需要多大的工作量来找到合适的nonce。TipThe Proof-of-Work must produce a hash that is less than the target. A higher target means it is less difficult to find a hash that is below the target. A lower target means it is more difficult to find a hash below the target. The target and difficulty are inversely related.比特币中的PoW和上面描述的过程基本差不多，矿工在构建完候选区块后，就开始尝试计算区块头的hash来看看是否小于设定的target了，如果不满足，则会调整nonce(通常是自增1)再试，在比特币网络的当前难度系数下，矿机需要计算上亿次才能找到合适的nonce值。Target RepresentationIn Using the command line to retrieve block 277,316, we saw that the block contains the target, in a notation called "target bits" or just "bits," which in block 277,316 has the value of 0x1903a30c. This notation expresses the Proof-of-Work target as a coefficient/exponent format, with the first two hexadecimal digits for the exponent and the next six hex digits as the coefficient. In this block, therefore, the exponent is 0x19 and the coefficient is 0x03a30c.The formula to calculate the difficulty target from this representation is​

​














​





​




​








​





​

​

​​







​








​







​​​​​​​信任链接的基础设施公有链：能源消耗的不值，pow、pos联盟链：共识收敛PDFT零知识证明激励机制跨链，即不同链条之间要相互映射（类比交通系统，公路网、铁路网、航空等的沟通）牛逼，智能合约和租房的类比，签名授权直接接入房门授权进出的硬件系统里 IoT + 生物学(人身份识别)​区块链本质是解决信息流、物流、资金流的可信问题数据可信->物的可信->资产可信->人的可信​金融民生工业​公益的有迹可循，都是基于支付宝的收支记录奶粉正品追踪保险+智能合约茅台酒不可篡改的身份证，造假copy的话，类比双花问题，会被发现具体哪个环节造假雄安租房智能系统，结合租龄，看是否有学区等​18年预测：金融和供应链会有落地第三代区块链(网状、共识的效率)零知识证明会改善现有互信与隐私的矛盾跨链的突破​​​​loopring 协议，为去中心化交易所提供一种思路
中心化交易所的问题：数据库是可以改的，即安全性透明性流动性​loopring enable：
trustlessly
anonymously
securely
in decentralized way.​AI与区块链的结合，AI自己进化学习，让自己拥有的经济越来越多，从而，人与人、人与机器、机器与机器之间都可以做生意
AI可以是专业技能领域的，身上有智能协议，完成特定任务，然后赚钱等等
机器人的安全性，不被黑客利用；传统的中心化模式是不可能完成的，总会被人黑掉
等等，这叫Consensus-Economy​AI怎么交易呢？
loopring是如何工作的​order-ring: 识别交易流通的环状关系，增加流动性，优化价格等？​​​​​​​​​​​
