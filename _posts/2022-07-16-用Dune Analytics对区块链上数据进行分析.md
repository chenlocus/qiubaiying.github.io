---
layout:     post
title:      用Dune Analytics对区块链上数据进行分析
subtitle:   
date:       2022-07-16
author:     Howard
header-img: img/post_blockchain.jpg
catalog: false
tags:
    - blockchain
    - cryptocurrency
    - ethereum
---





因为我平时经常关注区块链和加密货币，所以最近偶然被推送了一个东西，叫做Dune Analytics，是一个对区块链上数据进行分析的工具网站：
https://dune.com/

这是很多人把链上数据集中到PostgresSQL数据库中，然后通过查询和数据可视化，形成一个个dashboard，你可以查询这些dashboard，或者稍微改变下里面的sql查询语句，找到自己所需要的数据，还可以fork别人的dashboard，然后修改形成自己的风格。

这里有许多别人做的dashboard，我们可以直接查询：

![image.png](https://images.hive.blog/DQmRPErBDAFRdMwCpzjXRQXe6hszgrcC4gS9qj7EdYsxPfk/image.png)

也可以在右边搜索框里输入你想要查询的项目数据，比如"uniswap"来找到跟这个项目相关的数据：

![image.png](https://images.hive.blog/DQmQAgNxdyqkNKZh3qJy6hxxmjoghcGVrbKePEijr8us8Ya/image.png)

这个数据分析工具的好处是，既可以跟踪链上数据，搞技术的朋友也可以练习一下自己的sql查询能力，你不需要从头做起，找个别人的项目来修修改改就可以了，既享受搞sql得乐趣，也不用太费脑子。

这里是一个比较完整的教学。
<iframe width="560" height="315" src="https://www.youtube.com/embed/IHC8HAZvw_Q" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>









相关文章阅读：

- [智能合约代替OTC市场](http://livinginau.life/2019/12/10/%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E4%BB%A3%E6%9B%BFotc%E5%B8%82%E5%9C%BA/)
- [makeDAO 的 PETH](http://livinginau.life/2019/11/16/makeDAO_peth/)
- [非对称加密解密的理解](http://livinginau.life/2017/12/05/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86%E7%9A%84%E7%90%86%E8%A7%A3/)
- [在以太坊下搭建了私有链](http://livinginau.life/2017/12/05/%E5%9C%A8%E4%BB%A5%E5%A4%AA%E5%9D%8A%E4%B8%8B%E6%90%AD%E5%BB%BA%E4%BA%86%E7%A7%81%E6%9C%89%E9%93%BE/)
- [Ethereum 和 Ethereum Classic](http://livinginau.life/2017/12/05/Ethereum-%E5%92%8C-Ethereum-Classic/)

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













