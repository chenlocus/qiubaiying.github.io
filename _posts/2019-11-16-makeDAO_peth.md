---
layout:     post
title:      makeDAO 的 PETH
subtitle:   
date:       2019-11-16
author:     土猪
header-img: img/post_blockchain.jpg
catalog: true
tags:
    - 区块链
---

makeDAO可是以太坊deFi的拳头产品，它产生的DAI锁定一美元，目前第一阶段就以以太坊为抵押，模仿债务货币模式。 用户可以把自己的以太币抵押给它的智能合约CDP ( collateral deposit position )，然后它就产生DAI这个债务货币给用户。当然也不是白给，关于这个过程的文章，网上实在太多，我不想费口舌了。一开始我看英文白皮书感觉太长，没心思，也没时间（其实都是借口）看下去，于是找很多中文资料在手机上有事没事就看，但是一直就没弄懂PETH到底有啥用，尽管这个东西在第二阶段要取消，但是我个人认为，由于侧链技术问题，那不知道是猴年马月的事情，于是如鲠在喉，非常不爽，重新找来原汁白皮书，研究起来，总算脑袋里对这个东西的概念清晰起来。



咱把这个过程理理顺，用户手头有些以太币，缺钱买更多币，于是他（权且用他，没有性别歧视的意思）就把这10个以太币（假设市场价格200 刀）到makerDAO的网站上抵押到CDP，由于存在风险控制，CDP有一个叫做‘Liquidation​ ​Ratio’的限制，用户只能拿到1500刀的DAI。在抵押过程种，ETH先是被换成WETH （因为ETH本身不是ERC20的币，智能合约不好控制，所以就给包装成满足ERCD20标准的WETH），WETH跟ETH是1：1关系。 用户的ETH进入一个ETH池子，CDP兑换同等数量的PETH给用户，同时给用户DAI，这个PETH在外界是不能交易的，它代表的仅仅是抵押物的所有权，它是makerDAO系统的内部变量。



这样一来，makerDAO就相当于一个银行，接受抵押物（ETH），然后印钞票（DAI），它必须保证抵押物的市场价值大于或等于发去出的债务货币。当抵押物市场价格一路上升，那么用户拿DAI去兑换回ETH，这个银行没有问题。 但是当抵押物价格出现下跌时，跌破系统设定的界限，就是那个‘Liquidation​ ​Ratio'的时候，MAKEDAO就要对用户进行清算，这个跟期货合约一个道理，你要么返回DAI，要么增加抵押物，否则就要接受清算。


清算涉及到keeper 这个系统角色和CDP用户，还涉及到PETH和DAI，系统会把这个过程交给一个叫做“Liquidity Providing Contract”的智能合约去做。 这个过程是用PETH拿去卖给keeper，换取DAI，然后系统把DAI销毁掉。这样把用户抵押物扣除用户生成的DAI的数量和稳定费（Stability Fee），清算惩罚费（Liquidation Penalty），剩下的ETH还给用户，CDP被关闭掉。如果多卖了PETH，那么多得到的DAI被用于买入多余的PETH，然后销毁掉。如此动态保持PETH和市场上DAI数量的平衡。



那么当抵押物以太币价格跌到比发行在外面的债务货币市值还小的时候，用户都来挤兑咋办？系统需要保证每个DAI都值1美元！ makerDAO 平台会马上自动生成PETH来保证抵押物价值在DAI的市值之上，这个PETH是凭空生成的，其生成的数量由抵押物价值和发行在外的DAI的市值来决定。那么单个PETH的价格就降低了。但是这个看似凭空生成的PETH时候有人来买单的，买单的就是CDP用户，任何CDP用户在不小心被清算时候，都要被扣除一笔费用，叫做Liquidation Penalty（清算惩罚），用户被扣除清算惩罚后多余ETH才被返回给用户，这个Liquidation Penalty是用来购买PETH，然后销毁它，这样就减少了PETH的数量。



所以我觉得这个PETH本质上是系统平时收取的清算惩罚费来填补抵押物来保证抵押物价值不小于DAI的市值，如此，在不幸发生全局清算时候，用户的每个DAI能真的值1美元，能得到价值1美元的ETH。



最后说个题外话，关于MKR这个token，它是整个系统的管理型代币，数量固定，除了持有人能对系统参数和智能合约投票，好处就是每个CDP用户关闭CDP时候缴纳的稳定费都要用MKR去支付，系统收到MKR就销毁掉，如此它就变成紧缺并且通缩的货币了。这点我相当不能理解，为啥不干脆一点，稳定费也用DAI来计量，但是按ETH在系统里自动扣除，扣除部分放一个大池子里，像分红利一样分给所有MKR持有人。




更多区块链文章：

- [智能合约代替OTC市场](http://livinginau.life/2019/12/10/%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E4%BB%A3%E6%9B%BFotc%E5%B8%82%E5%9C%BA/)
- 
  [乌龙山剿匪记](http://livinginau.life/2019/11/25/%E4%B9%8C%E9%BE%99%E5%B1%B1%E5%89%BF%E5%8C%AA%E8%AE%B0/)

- 
  [makeDAO 的 PETH](http://livinginau.life/2019/11/16/makeDAO_peth/)

- 
  [bitshares（比特股）的交易费](http://livinginau.life/2019/11/16/bitshares-%E6%AF%94%E7%89%B9%E8%82%A1-%E7%9A%84%E4%BA%A4%E6%98%93%E8%B4%B9/)

- 
  [对比特比共识机制的一些看法（my opinions on the consensus mechanism of Bitcoin )](http://livinginau.life/2019/03/05/%E5%AF%B9%E6%AF%94%E7%89%B9%E6%AF%94%E5%85%B1%E8%AF%86%E6%9C%BA%E5%88%B6%E7%9A%84%E4%B8%80%E4%BA%9B%E7%9C%8B%E6%B3%95/)

- 
  [steem的单元测试模块](http://livinginau.life/2018/10/23/steem%E7%9A%84%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E6%A8%A1%E5%9D%97/)

- 
  [怎么样才能当上steem的矿工](http://livinginau.life/2018/10/20/%E6%80%8E%E4%B9%88%E6%A0%B7%E6%89%8D%E8%83%BD%E5%BD%93%E4%B8%8Asteem%E7%9A%84%E7%9F%BF%E5%B7%A5/)

- 
  [往steemit账户上存钱的若干办法 (deposit steem to my steemit account)](http://livinginau.life/2018/10/20/%E5%BE%80steemit%E8%B4%A6%E6%88%B7%E4%B8%8A%E5%AD%98%E9%92%B1%E7%9A%84%E8%8B%A5%E5%B9%B2%E5%8A%9E%E6%B3%95/)

- 
  [利用steem sql 寻找同城好友](http://livinginau.life/2018/10/20/%E5%88%A9%E7%94%A8steem-sql-%E5%AF%BB%E6%89%BE%E5%90%8C%E5%9F%8E%E5%A5%BD%E5%8F%8B/)

- 
  [steem做档案存储](http://livinginau.life/2018/10/20/steem-%E5%81%9A%E6%A1%A3%E6%A1%88%E5%AD%98%E5%82%A8/)

- 
  [看yoyow白皮书随感](http://livinginau.life/2018/01/16/%E7%9C%8Byoyow%E7%99%BD%E7%9A%AE%E4%B9%A6%E9%9A%8F%E6%84%9F/)

- 
  [SMTs 白皮书读后感之一统天下](http://livinginau.life/2017/12/06/SMTs-%E7%99%BD%E7%9A%AE%E4%B9%A6%E8%AF%BB%E5%90%8E%E6%84%9F%E4%B9%8B%E4%B8%80%E7%BB%9F%E5%A4%A9%E4%B8%8B/)

- 
  [非对称加密解密的理解](http://livinginau.life/2017/12/05/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86%E7%9A%84%E7%90%86%E8%A7%A3/)

- 
  [How to prevent DPOS being abused (如何防止DPOS 机制被滥用)](http://livinginau.life/2017/12/05/%E5%A6%82%E4%BD%95%E9%98%B2%E6%AD%A2DPOS-%E6%9C%BA%E5%88%B6%E8%A2%AB%E6%BB%A5%E7%94%A8/)

- 
  [在以太坊下搭建了私有链](http://livinginau.life/2017/12/05/%E5%9C%A8%E4%BB%A5%E5%A4%AA%E5%9D%8A%E4%B8%8B%E6%90%AD%E5%BB%BA%E4%BA%86%E7%A7%81%E6%9C%89%E9%93%BE/)

- 
  [历史是面镜子：从以太坊看以后的EOS](http://livinginau.life/2017/12/05/%E4%BB%8E%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%9C%8B%E4%BB%A5%E5%90%8E%E7%9A%84EOS/)

- 
  [Ethereum 和 Ethereum Classic](http://livinginau.life/2017/12/05/Ethereum-%E5%92%8C-Ethereum-Classic/)

- 
  [BTS初用感受](http://livinginau.life/2017/12/05/BTS%E5%88%9D%E7%94%A8%E6%84%9F%E5%8F%97/)

- [SBD如何锚定美元](http://livinginau.life/2017/10/05/sbd-peg-to-usd/)


