---
layout:     post
title:      python和ganache玩转solidity智能合约
subtitle:   
date:       2020-8-8
author:     土猪
header-img: img/post_blockchain.jpg
catalog: true
tags:
    - 区块链
    - 智能合约
---





这维州疫情，看来得旷日持久了，您一个个窝居家中，也不休假，万一以后辞职或裁员时候，一个个那么多年休假，公司都要现金补偿的，怎么吃得消哇，所以连我们公司都开始强制年休假了，每天今年必须修完一定年假数目。 所以咱都打算今年一年窝在家里了，很多人的mental health都出现了问题，每天zoom开会，上课，时间一长，人就会疲惫，无聊，总是看youtube电影也会非常无聊，所以一定要给自己找些健康积极的事情做。 天晴的时候，我可以在花园里垦地，做菜床，除草等等，所谓做一行爱一行，渐渐我喜欢了这种体力劳动。 但是墨尔本的冬天，会经常下雨，今年已经算是降雨量比较小的了，但是阴雨绵绵的日子，怎么打法无聊的时间呢？对于我来说，除了写些博客，就是玩弄代码了，古人云：玩物丧志，玩人丧德。 哥玩弄代码，既不丧志，也不无德。



<img src="https://images.hive.blog/DQmeNiUCS8qkeomHBKFsSsHNFeW1Y2xFLzeNgo1S3GabGGc/image.png" alt="image.png" style="zoom:200%;" />

把玩弄代码的过程写出来，除了赚些hive，还有个很大的好处，就是以后有tricky的问题，可以到自己博客上来找答案。这次我玩的是用python跟智能合约交互，我以前用javascript玩过智能合约，用truffle framework，里头包括合约的开发，下载，测试一条龙服务，可谓周到，无奈我是个勤劳的人（贱人啊贱人，命该受苦），不喜欢别人给我服务太周到，那个框架让我没有一种触感，特别是和智能合约的交互过程。曾经一度我用python玩过智能合约交互，那种把私钥掌握在手里，通过abi文件取到合约，然后和智能合约的交互过程让我感觉到一种真实。所以我打算搞一个众筹合约，然后通过python来跟它玩玩，当然，智能合约的编译，下载的过程我还是用了truffle框架，主要是我不想在过分细节地方纠缠不清。



先写2个合约，一个是代币合约，一个是众筹合约，代币合约如下：



```
pragma solidity ^0.5.0;

import 'openzeppelin-solidity/contracts/token/ERC20/ERC20Mintable.sol';
import "openzeppelin-solidity/contracts/token/ERC20/ERC20Burnable.sol";
import "openzeppelin-solidity/contracts/token/ERC20/ERC20.sol";
import "openzeppelin-solidity/contracts/token/ERC20/ERC20Detailed.sol";


/**
 * title EOS
 * ERC20 Token 10000 tokens
 */
contract EOSToken is ERC20, ERC20Detailed,ERC20Mintable, ERC20Burnable {
    // modify token name
    string public constant NAME = "EOS";
    // modify token symbol
    string public constant SYMBOL = "EOS";
    // modify token decimals
    uint8 public constant DECIMALS = 18;
    // modify initial token supply
    uint256 public constant INITIAL_SUPPLY = 10000 * (10 ** uint256(DECIMALS)); // 10000 tokens

    /**
     * @dev Constructor that gives msg.sender all of existing tokens.
     */
    constructor () public ERC20Detailed(NAME, SYMBOL, DECIMALS) {
        _mint(msg.sender, INITIAL_SUPPLY);
    }
}
```



众筹合约如下：

```
pragma solidity >=0.4.21 <0.6.0;

import './EOSToken.sol';
/*import 'openzeppelin-solidity/contracts/crowdsale/emission/MintedCrowdsale.sol';*/
import 'openzeppelin-solidity/contracts/crowdsale/validation/TimedCrowdsale.sol';



contract EOSTokenCrowdsale is TimedCrowdsale {
    constructor
        (
            uint256 _openingTime,
            uint256 _closingTime,
            uint256 _rate,
            address payable  _wallet,
            ERC20Mintable _token
        )
        public
        Crowdsale(_rate, _wallet, _token)
        TimedCrowdsale(_openingTime, _closingTime) {

        }

}
```



如果把这些合约下载到真实以太坊区块链上去玩，对我而言浪费了钱，在家上班，本来就浪费了很多水电煤，还到以太坊上浪费gas，多么不忍啊，对别人来说，浪费了本来就拥挤的链上资源。所以我还是用了模拟以太坊区块链的神器----ganache，把这个运行起来：

![image.png](https://images.hive.blog/DQmQtEUYG6MvJHaWANVVvcLT2JqiH2hgqscyfFAxYAZS92s/image.png)



它自动给我生成了10个账号，每个账号都有100个ETH，足够我玩的。

然后通过`truffle compile` 和`truffle migrate`编译和下载到区块链上去，就可以开始玩了。

我喜欢用python玩的另外一个理由就是可以用jupyter notebook，即时运行出现结果。它提供无数编辑窗口，然那个你可以随便玩。玩之前，需要做些准备工作，我们需要知道连的是哪个区块链，众筹合约和代币合约的地址，还要找到一个账号，知道它的私钥和地址，得到合约的控制权，这些如果用truffle，它都在某个地方悄悄替你做了，它有些像apple手机，让你感觉在雾中，现在这个方式，像是使用android手机，你知道文件存在哪个文件夹里，很多设置都公开透明。

```
import time
from web3 import Web3, HTTPProvider
import crowdsale_contract_abi,token_contract_abi


w3 = Web3(HTTPProvider('http://127.0.0.1:7545'))


crowdsale_contract_address     = '0xce5Ca8dCc4dF68905D9590500F207c475e41Fb33'
token_contract_address = '0x655d294c9DFF8E3d3E3B38Da3A7Db2638c61D8CC'
wallet_private_key   = '1aa859fc0a9370adcf2a58fb5fa964ac55e6b04c307250e075302000afef9e44'
wallet_address = w3.eth.accounts[0]
total_supply = 10000 * (10 ** 18)

crowdsale_contract_address = w3.toChecksumAddress(crowdsale_contract_address)
token_contract_address = w3.toChecksumAddress(token_contract_address)

wallet_address = w3.toChecksumAddress(wallet_address)

token_contract = w3.eth.contract(address = token_contract_address, abi = token_contract_abi.abi)
crowdsale_contract = w3.eth.contract(address = crowdsale_contract_address, abi = crowdsale_contract_abi.abi)

```



跟智能合约有三种交互，一种直接发以太到智能合约的地址里去，这也是参加众筹的方式，你发以太币过去，它那边给你发代币，这时候智能合约会调用自己的fallback函数。用python里通过自己构造一个transaction的结构，用私钥签名后发送到链上，然后等待结果，记住，只要是往链上的写操作，都是耗钱的，损耗的是gas。



```
amount_in_wei = w3.toWei(amount_in_ether,'ether');

    nonce = w3.eth.getTransactionCount(wallet_address)
    print(nonce)
    print(amount_in_wei)

    txn_dict = {
            'to': crowdsale_contract_address,
            'value': amount_in_wei,
            'gas': 2000000,
            'gasPrice': w3.toWei('40', 'gwei'),
            'nonce': nonce,
            'chainId': 3
    }

    signed_txn = w3.eth.account.signTransaction(txn_dict, wallet_private_key)

    txn_hash = w3.eth.sendRawTransaction(signed_txn.rawTransaction)

    txn_receipt = None
    count = 0
    while txn_receipt is None and (count < 30):

        txn_receipt = w3.eth.getTransactionReceipt(txn_hash)

        print(txn_receipt)

        time.sleep(10)


    if txn_receipt is None:
        return {'status': 'failed', 'error': 'timeout'}

```



跟智能合约的另一种交互就是调用合约本身的带改变状态的那些函数，比如调用代币合约的transfer函数，这个也是烧钱的，消耗签名账户的gas。这个有两种方式做，一种是如上所说，自己建造个transaction结构，然后发链上：

```
    nonce = w3.eth.getTransactionCount(wallet_address)

    txn_dict = contract.functions.transfer(to_address, amount).buildTransaction({
        'chainId': 3,
        'gas': 140000,
        'gasPrice': w3.toWei('40', 'gwei'),
        'nonce': nonce,
    })

    signed_txn = w3.eth.account.signTransaction(txn_dict, private_key=wallet_private_key)
```



跟智能合约的第三种交互是免费的，因为这仅仅是读取链上数据，这个也最简单，比如读取某个账户的balance：

```
token_contract.functions.balanceOf(account).call()
```



再谈谈我遇到的一些坑，我先是把众筹合约设置成MintedCrowdsale和TimedCrowdsale的继承人，然后我运行程序后遇到的第一个坑就是说众筹时间不到，这个时间显然是区块链上，也就是ganache上时间，然后我用这段话来查询了当前区块的时间戳：

```
blockNumber = w3.eth.blockNumber
unixtime = w3.eth.getBlock(blockNumber).timestamp
print(unixtime)
print(datetime.utcfromtimestamp(unixtime).strftime('%Y-%m-%d %H:%M:%S'))

```

显示的时间还真的比我预定开始时间少，真奇怪啊，这个跟我电脑时间还不一样，而且没有区块挖出来，它时间就一直不变，我上网搜了下怎么操纵ganache上时间，用这个方法：

```
w3 = Web3(HTTPProvider('http://127.0.0.1:7545'))
provider = w3.providers[0]
provider.make_request("evm_increaseTime", [36000])

```

然后需要随便在区块链上做一个transaction，即便失败的也行，这样区块就挪动了，上面增加的时间才会生效，果然有用啊，亲测有效。

接下来又运行，说我没有minter role，考，难道印钱还要minter role？我自己下载的合约，我应该有这个权限的呀。然后我发现，实际上众筹合约和代币合约是两个地址，minter role认可的有权限的人是我电脑这个钱包地址，而操作它的是众筹合约地址，这个地址不具备minter role，它实际上是如下这个关系，这样就想通了：

```
my account address(minter role)--->crowdsale contract ---->token contract
```



终于要发币了，我从电脑上转0.03eth给众筹合约，结果是合约没有足够多的钱，靠，怎么会，我在代币合约里一开始就初始化了1000，000，000个代币啊，怎么会没有足够代币给别人。后来我查了openzeppelin-solidity的文档，原来这些代币并不在众筹合约里，我需要先把足够的代币转移到众筹合约里，然后才可以搞众筹，收以太，发代币。这样整个发币流程才走通了。



作为一个偏执狂，必须要测试通过这件事件才完美，所以我用了python 的unittest framework来做这件事情，当然，我以前用mocha framework，能很好地跟truffle框架匹配，但是那让我没有一种实在的感觉。期间也碰到问题，就是unittest 在jupyter notebook下不好使，原因不必详谈，这样做就可以：

```
 unittest.main(argv=['first-arg-is-ignored'], exit=False)
```



```
import time
import unittest
from web3 import Web3, HTTPProvider
import json

class TestCrowdSale(unittest.TestCase):
    
    def setUp(self):
        self.w3 = Web3(HTTPProvider('http://127.0.0.1:7545'))
        truffleFile_token = json.load(open('../build/contracts/EOSToken.json'))
        truffleFile_crowdsale = json.load(open('../build/contracts/EOSTokenCrowdsale.json'))
        self.crowdsale_contract_address     = '0xce5Ca8dCc4dF68905D9590500F207c475e41Fb33'
        self.token_contract_address = '0x655d294c9DFF8E3d3E3B38Da3A7Db2638c61D8CC'
        self.wallet_private_key   = '1aa859fc0a9370adcf2a58fb5fa964ac55e6b04c307250e075302000afef9e44'
        self.wallet_address = self.w3.eth.accounts[0]

        self.crowdsale_contract_address = self.w3.toChecksumAddress(self.crowdsale_contract_address)
        self.token_contract_address = self.w3.toChecksumAddress(self.token_contract_address)

        self.wallet_address = self.w3.toChecksumAddress(self.wallet_address)

        self.token_contract = self.w3.eth.contract(address = self.token_contract_address, abi = truffleFile_token['abi'])
        self.crowdsale_contract = self.w3.eth.contract(address = self.crowdsale_contract_address, abi = truffleFile_crowdsale['abi'])
        self.w3.eth.defaultAccount = self.w3.eth.accounts[0]
        self.total_supply = 10000 * (10 ** 18)


    def TransferTokenTo(self,dest,amount):
    
        tx_hash = self.token_contract.functions.transfer(dest,amount)
        tx_hash = tx_hash.transact()
        # Wait for transaction to be mined...
        self.w3.eth.waitForTransactionReceipt(tx_hash)
    
#         print("token in token contract:",self.token_contract.functions.balanceOf(token_contract_address).call())
#         print("token in crowdsale contract:",self.token_contract.functions.balanceOf(crowdsale_contract_address).call())

    def TransferToken(self,originate,to,amount):
    
        tx_hash = self.token_contract.functions.transferFrom(originate,dest,amount)
        tx_hash = tx_hash.transact()
        # Wait for transaction to be mined...
        self.w3.eth.waitForTransactionReceipt(tx_hash)
    

    def send_ether_to_crowdsale_contract(self,amount_in_ether):

        amount_in_wei = self.w3.toWei(amount_in_ether,'ether');

        nonce = self.w3.eth.getTransactionCount(self.wallet_address)
        print(nonce)
        print(amount_in_wei)

        txn_dict = {
            'to': self.crowdsale_contract_address,
            'value': amount_in_wei,
            'gas': 2000000,
            'gasPrice': self.w3.toWei('40', 'gwei'),
            'nonce': nonce,
            'chainId': 3
        }

        signed_txn = self.w3.eth.account.signTransaction(txn_dict, self.wallet_private_key)

        txn_hash = self.w3.eth.sendRawTransaction(signed_txn.rawTransaction)

        txn_receipt = None
        count = 0
        while txn_receipt is None and (count < 30):

            txn_receipt = self.w3.eth.getTransactionReceipt(txn_hash)

            print(txn_receipt)

            time.sleep(10)


        if txn_receipt is None:
            return {'status': 'failed', 'error': 'timeout'}

        return {'status': 'added', 'txn_receipt': txn_receipt}

    def get_token_balance(self,account):
        return self.token_contract.functions.balanceOf(account).call()
    
    
    
    def test_buyToken(self):
        beforeBalance = self.get_token_balance(self.wallet_address)
        self.send_ether_to_crowdsale_contract(0.03)
        time.sleep(15)
        afterBalance = self.get_token_balance(self.wallet_address)
        tokenGet = afterBalance-beforeBalance
        
        self.assertEqual(tokenGet, 0.03*(10**18))
        
    
    

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit=False)
```

测试跑通，生活又多了点乐趣：

```
.
----------------------------------------------------------------------
Ran 1 test in 25.228s

OK
​
```



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

