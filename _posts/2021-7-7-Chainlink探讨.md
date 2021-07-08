---
layout:     post
title:      Chainlink探讨
subtitle:   
date:       2021-7-7
author:     土猪
header-img: img/post_blockchain.jpg
catalog: true
tags:
    - 区块链
    - 加密货币
---

上次研究了the Graph这个从区块链往外部世界传递数据的产品，自己尝试了[安装运行一个测试节点](https://hive.blog/hive-105017/@chenlocus/grt-the-graph)，并且[从投资者的角度对这个产品进行了分析](https://hive.blog/hive-105017/@chenlocus/3j6biv-grt-the-graph)，最近我又对另外一个区块链上产品进行了研究，就是Chainlink，其实它比the Graph出道要早两年，它的主要目的，是把外界数据引入到区块链上来。总体来说，我以为，从投资角度说，这个Chainlink应该比the Graph更有价值，因为区块链不能没有外部世界的数据而内循环，如果区块链的创建不是服务于这个世界，而是拿加密货币来恶炒，那么它的存在有什么意义？ 而这个世界却不一定需要区块链也一样可以活得很好。



跟往常一样，我喜欢单刀直入，自己先搭建节点体验一把这个产品，Chainlink出道早，文档也比较容易理解，不繁琐，找到这个[文档](https://docs.chain.link/docs/running-a-chainlink-node/)，自己找个ubuntu操作系统的机器来安装是非常简单的活，然后再按照它上面的指示，创建一个Oracle合约和测试用户合约，然后给测试用户合约充值，因为我是在测试网rinkeby上运行的，需要去rinkeby的faucet上搞些免费的测试ETH和LINK代币，然后往测试用户合约地址上转些LINK代币，这样它在提出数据请求时候，可以付费给我的Oracle合约。还需要往节点的地址上转些ETH代币，节点是要消耗gas的。这个过程基本是如下图所示，在[Chainlink的文档](https://docs.chain.link/docs/architecture-request-model/)里也有详细说明：

![image.png](https://images.hive.blog/DQmWzwBiG4q5sVmcC6a9x4DiGzYBmpRXAs6cQLd1pgeCnTv/image.png)

真正动手做，我才知道，原来Chainlink的代币并不是我们广为常见的ERC-20，而是 [ERC-677](https://github.com/ethereum/EIPs/issues/677)， 之所以用这个玩意，就是因为它的transferAndCall的功能，就是让接受合约在收到钱财的时候，能做些事情，这样完美解决了“一手交钱，一手交货”的问题，如果交货失败，合约就回退，钱也就收不到了。

![image.png](https://images.hive.blog/DQmb6YjHU7APhTP2bqzBAi1ggD2kdjaGgiUKv3vw5Fe5DFF/image.png)



具体原理在文档中还是表述得不错的，我就不多废话，主要谈谈我的体验，一个是慢，第二个是怀疑。 从用户合约提出requestEthereumPrice开始，通过LinkToken传递给Oracle，需要用户确认交易，然后这个交易被传递到以太坊的链上，节点就专门看链上的数据，发现有请求就去外部世界找数据，把数据写回链上去（所以节点是要消耗gas的，这也是节点的成本之一），然后通过currentPrice来取得数据，整个过程大概需要2-3分钟，我就纳闷了，谁吃饱了撑着，要花钱（大概每个请求0.1LINK或者就是1LINK，就是2美刀或20多美刀）请求一个延时这么久的数据，这个买卖明眼人看着就不合算啊。那么还有一种可能性，就是自产自销，自己搞个节点，把数据给自己用，当然，钱也是左手倒右手，不过就是费点gas费。





但是，chainlink还有个功能，叫做[Flux aggregator](https://docs.chain.link/docs/architecture-decentralized-model/)，就是把能提供某个数据的节点进行数据整合，确保数据的可靠性，但是这个很愚蠢，每个节点都要付gas费给以太坊，这样每个节点都有机会提供数据，而不用打广告宣传自己的节点服务。 但是我还是想不通，谁会为了这样延迟的数据付那么多。 由于这个Flux aggregator很愚蠢，所以chainlink又推出一个功能，叫做[off-chain report](https://docs.chain.link/docs/off-chain-reporting/)，就是各个节点自己p2p公布下自己的数据，然后集成一个数据往以太坊上写，这样只出一次gas费，说不定还可以平坦费用。



想做它的节点，还是很难的，主要是信息不全面，你都不知道上哪里申请去，我去discord里头问，他们说去Market.link去sign up，然后发现连verification email都发不出来，然后又有人说这不是他们的官方网站，[这](https://reputation.link/oracles)才是他们的官方网站，market.link是由link pool维护的，啥？link pool不是他们的节点之一吗？难道是一家人里头分出来的？总之很乱。 从技术角度，这个玩意不错，从使用角度，这个玩意性能值现在的市值吗？我很怀疑。也许要下一次熊市，才会有人去揭露些什么东西出来，在没有达到熊市价格的时候，一切都将运行得很好。



下面从投资者角度来谈谈这个项目。

- 这东西取代了中心化的哪种系统或者有啥用处？

我还真不知道在有Chainlink之前，智能合约如何从外部世界获取数据呢，而智能合约都是被动执行命令的，所以只能是从外面强行塞进去的。 而区块链世界一直处于“内循环”，也不需要外部世界数据，但是随着越来越多被应用，尤其是defi的项目越来越多，就愈发需要外部金融数据的支持了，对预言机（oracle）的需求，也是最近几年出现的。因为区块链需要服务这个世界，所以它需要外部世界的数据输入，所以Chainlink在我看来，比the Graph来得更有价值。



- 节点有多少个？多少人在使用？

这个我有些迷惑，从[market.link](https://market.link/search/nodes?network=1)上看到主网有130个节点，也能看到每个节点每天的收入，提供的服务，相应数据情况；

从另一个他们宣称的[官方网站](https://reputation.link/oracles)看不出有几个，估计认为就是130个节点吧。



以其中每日数据量最多的link.pool为例来看看是，每日也就最多1000-2000个数据请求，还能赚这么多钱，这让我很奇怪啊，如果一个智能合约长期需要外部数据，这样不是要亏死了？还不如自己搭建节点，给自己提供数据呢。很多问题我们永远想不通，直到泡沫去尽。

![image.png](https://images.hive.blog/DQmZkQma4v2qZ3NcN42rQwhtV88zKvwyqFY4CpQCsoP72S4/image.png)





- 目前哪些项目使用Chainlink来[获取外部数据](https://docs.chain.link/docs/get-the-latest-price/)？

​        这个还真找不到完整的数据，应该是任何项目都可以使用Chainlink接入吧，这个只能从上面说的每个节点的数据量来大致判断了。但是[Chainlink的生态圈](https://chain.link/ecosystem)还是很完整的。

- Chainlink被应用于哪些主流区块链上？

  从[这里](https://docs.chain.link/docs/reference-contracts/)看，它的应用还是很广泛的，除了以太坊，还有币安链，xDAI等：

  ![image.png](https://images.hive.blog/DQmTbfZw4RtpTGXvYKVXNx7bZDybVHJqphY21pZPpjTixnt/image.png)



最后看看它的估值吧，我觉得，大多数区块链项目都可以使用类似传统公司的市值来评估，因为区块链项目可以被看作是一个去中心化的公司而已。在https://coinmarketcap.com/ 上，它的市值是8.5个billion：

![image.png](https://images.hive.blog/DQmeKWrCEXCpQ5yMZ2VRoRTe39VzdV9tThEKzCRwBo9uFRv/image.png)



我觉得根据现在的节点数据流量，真的太贵了，我们公司估值2 billion，集成了金融数据的供给和交易，还在不断收购market data的公司呢。最近灰度一直在小规模买入LINK，也许大鳄的心思我不懂。不过混迹港股好几年，对于大鳄买人股票，然后严重亏损的见识多了，也就不当作回事了。



我觉得，Chainlink真是一个好项目，就是市场开出价格太贵了，等它恢复理性再说吧，要不就折腾一下做节点试试看。


























相关文章阅读：

- [智能合约代替OTC市场](http://livinginau.life/2019/12/10/%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E4%BB%A3%E6%9B%BFotc%E5%B8%82%E5%9C%BA/)

- [makeDAO 的 PETH](http://livinginau.life/2019/11/16/makeDAO_peth/)
  
- [非对称加密解密的理解](http://livinginau.life/2017/12/05/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86%E7%9A%84%E7%90%86%E8%A7%A3/)
  
- 
  [在以太坊下搭建了私有链](http://livinginau.life/2017/12/05/%E5%9C%A8%E4%BB%A5%E5%A4%AA%E5%9D%8A%E4%B8%8B%E6%90%AD%E5%BB%BA%E4%BA%86%E7%A7%81%E6%9C%89%E9%93%BE/)

- 
  [Ethereum 和 Ethereum Classic](http://livinginau.life/2017/12/05/Ethereum-%E5%92%8C-Ethereum-Classic/)


- [2019年最硬核公链](http://livinginau.life/2020/01/12/%E8%B0%81%E6%98%AF2019%E5%B9%B4%E6%9C%80%E7%A1%AC%E6%A0%B8%E5%85%AC%E9%93%BE/)

- [乌龙山剿匪记](http://livinginau.life/2019/11/25/%E4%B9%8C%E9%BE%99%E5%B1%B1%E5%89%BF%E5%8C%AA%E8%AE%B0/)

- [谈谈比特股的交易费](http://livinginau.life/2019/11/16/bitshares-%E6%AF%94%E7%89%B9%E8%82%A1-%E7%9A%84%E4%BA%A4%E6%98%93%E8%B4%B9/)

- [对比特比共识机制的一些看法（my opinions on the consensus mechanism of Bitcoin )](http://livinginau.life/2019/03/05/%E5%AF%B9%E6%AF%94%E7%89%B9%E6%AF%94%E5%85%B1%E8%AF%86%E6%9C%BA%E5%88%B6%E7%9A%84%E4%B8%80%E4%BA%9B%E7%9C%8B%E6%B3%95/)

- [steem的单元测试模块](http://livinginau.life/2018/10/23/steem%E7%9A%84%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E6%A8%A1%E5%9D%97/)

- [怎么样才能当上steem的矿工](http://livinginau.life/2018/10/20/%E6%80%8E%E4%B9%88%E6%A0%B7%E6%89%8D%E8%83%BD%E5%BD%93%E4%B8%8Asteem%E7%9A%84%E7%9F%BF%E5%B7%A5/)

- [往steemit账户上存钱的若干办法 （deposit steem to my steemit account）](http://livinginau.life/2018/10/20/%E5%BE%80steemit%E8%B4%A6%E6%88%B7%E4%B8%8A%E5%AD%98%E9%92%B1%E7%9A%84%E8%8B%A5%E5%B9%B2%E5%8A%9E%E6%B3%95/)

- [steem做档案存储](http://livinginau.life/2018/10/20/steem-%E5%81%9A%E6%A1%A3%E6%A1%88%E5%AD%98%E5%82%A8/)

- [看yoyow白皮书随感](http://livinginau.life/2018/01/16/%E7%9C%8Byoyow%E7%99%BD%E7%9A%AE%E4%B9%A6%E9%9A%8F%E6%84%9F/)

- [SMTs 白皮书读后感之一统天下](http://livinginau.life/2017/12/06/SMTs-%E7%99%BD%E7%9A%AE%E4%B9%A6%E8%AF%BB%E5%90%8E%E6%84%9F%E4%B9%8B%E4%B8%80%E7%BB%9F%E5%A4%A9%E4%B8%8B/)

- [How to prevent DPOS being abused （如何防止DPOS 机制被滥用）](http://livinginau.life/2017/12/05/%E5%A6%82%E4%BD%95%E9%98%B2%E6%AD%A2DPOS-%E6%9C%BA%E5%88%B6%E8%A2%AB%E6%BB%A5%E7%94%A8/)

- [什么是硬分叉，什么是软分叉](http://livinginau.life/2017/12/05/%E4%BB%80%E4%B9%88%E6%98%AF%E7%A1%AC%E5%88%86%E5%8F%89-%E4%BB%80%E4%B9%88%E6%98%AF%E8%BD%AF%E5%88%86%E5%8F%89/)

- [以太坊和以太坊经典两个币是啥关系](http://livinginau.life/2017/12/05/Ethereum-%E5%92%8C-Ethereum-Classic/)

- [比特股钱包初用感受](http://livinginau.life/2017/12/05/BTS%E5%88%9D%E7%94%A8%E6%84%9F%E5%8F%97/)

- [比特比共识机制](http://livinginau.life/2019/03/05/%E5%AF%B9%E6%AF%94%E7%89%B9%E6%AF%94%E5%85%B1%E8%AF%86%E6%9C%BA%E5%88%B6%E7%9A%84%E4%B8%80%E4%BA%9B%E7%9C%8B%E6%B3%95/)

