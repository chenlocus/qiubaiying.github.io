---
layout:     post
title:      智能合约之众筹(crowdsale)合约解读
subtitle:   crowdsale
date:       2020-7-31
author:     土猪
header-img: img/post_smart_contract.jpg
catalog: true
tags:
    - 区块链
    - 智能合约
---





智能合约是区块链上最重要的应用，在以太坊出现之前，各个节点仅仅处理币的交易，如同一个个账本，同一笔交易被记录在很多独立运行的节点上。智能合约是区块链技术应用的一个巨大进步，在各个节点运行一样的代码，产生一样的结果，然后这种结果进入区块过程跟交易一样，确保无人可以作弊。我一直对以太坊上智能合约感兴趣，在2017年ICO最疯狂的时候，我还学习了解了一下如何做一个众筹的智能合约，曾经在一个技术群跟一帮人讨论，发现知道的人很少，所以只好自己上网找些资料看，尽管这个东西被很多人用来骗钱，割韭菜，但是我们不可否认，甚至在腾讯的区块链白皮书中也承认这的确是区块链的一个巨大的金融方面的应用。

![image.png](https://images.hive.blog/DQmVzPEv2C2GwY7Ri26RbvH3m4DpmC86NH1bmaUjvV3a7NG/image.png)



目前，据我所知，在以太坊上开发，部署智能合约最好的工具就是solidity，我们判断一个工具是否好用，除了这个语言是否被广泛熟悉和使用外，还有就是它支持的功能是否足够，它所对应的社区是否强大能提供足够的技术支持。 EOS（柚子）从2018年6月出世以来，最支持的智能合约开发语言是C++，这是一种我们那个年代流行的开发语言，但是非常底层，没有Javascript，python那么方便使用，一般用于对实时性要求非常高的场合，比如底层通讯，金融交易等，solidity 采用了类似Javascript的语言，这样得到更多开发者的支持。python也可以在以太坊上开发智能合约，有个叫做Vyper的玩意，参见[这里](https://kauri.io/an-introduction-to-smart-contracts-with-vyper/af913a853eaf4db88627b3ff9572b770/a)，但是这个东西有些硬伤，看看目前它不支持的项目：



> And to follow similar principles, Vyper does not support:
>
> - **Modifiers**
> - **Class inheritance**
> - **Inline assembly**
> - **Function overloading**
> - **Operator overloading**
> - **Recursive calling**
> - **Infinite-length loops**
> - **Binary fixed point**



单是前面对Modifiers和Class inheritance的不支持，就无法令人忍受了，所以在我看来，这个玩意目前就是个小众化的东西。





话归正传，最近我突然对代币众筹这个话题感了兴趣，虽然我很早之前就知道怎么粗略地搞一个ICO，甚至在2017年时候，当时还有人想找我合作搞一个，我知道他干嘛用的，我看了他那个项目，一个纯碎的圈钱项目，觉得我所受的教育不允许我那样做，很多时候，善恶就在一瞬间。但是作为技术，这个值得好好研究下。 哥最不喜欢的事情就是从头造轮子，既然有现成的部件，我们何不拿来主义，我觉得最值得好好研究的就是‘[openzeppelin-solidity](https://github.com/OpenZeppelin/openzeppelin-contracts)’这个库里面的'crowdsale'文件夹下的内容，这里聚集了几乎所有跟ICO相关的项目文件。 当然，这些都是solidity语言写的。我觉得把这里头东西说清楚，可以让我更加深入而系统地了解下众筹合约这一块。



我觉得第一步要对‘Crowdsale.sol’这个合约进行详细了解，因为接下来别的众筹合约都是基于这个基础之上的继承类。先看看它引用了哪些现成的合约：



`import "../token/ERC20/IERC20.sol";`
`import "../math/SafeMath.sol";`
`import "../token/ERC20/SafeERC20.sol";`
`import "../utils/ReentrancyGuard.sol";`



‘IERC20.sol’是基于ERC20协议的代币实现的接口，在发众筹合约的时候，也要发代币token合约，这个token的具体实现它不管，只要是基于ERC20接口的协议就行，当然这个实现在‘openzeppelin-solidity’项目的token子文件夹里有很多。



’SafeMath.sol‘是智能合约数学计算库，库被引用后，它仅运行在调用它的智能合约的context（环境）里，就跟C语言库函数一样，引用库这个功能使许多合约的公共功能可以抽离出来，形成一个模块，供各个合约调用，彼此也不受影响。这个SafeMath把我们想当然的很多加减乘除计算变得更安全，比如：uint256 c = a + b;
        require(c >= a, "SafeMath: addition overflow");

假设以太坊上支持最大数是9999，如果a和b加起来超过了9999，假设是10000，这个在计算里头叫做溢出（overflow），系统很可能就认为结果是1，然后调用者还不知道，那么结果就变得出乎意料。所以用这个模块，比我们自己单纯去计算要更加安全。



’SafeERC20.sol’是ERC20代币的安全操作，它调用的是底层call，这样更加容易对错误进行处理。如果某个操作失败，它会返回错误或者回滚，而不是一身不吭，比如，你转移一笔钱失败了也没有返回，没有回退，然后调用者还以为成功了，这不是很悲剧的事情？它还会检查被调用的地址是否真的合约。





对于库函数，调用时候用using....for....，就是把库里的函数调用返回成for 后面的类型，比如

using SafeMath for uint256;
using SafeERC20 for IERC20;



这个意思用下面一段代码来解释最合适，用语言来解释可能还是很让人迷惑：

```python
pragma solidity ^0.5.0;
library MathLib {        
    function mult(uint a, uint b) public view returns (uint, address) {        
        return (a * b, address(this));    }}

contract Example {        
    using MathLib for uint;
    address owner = address(this);        
    
    
    function multEx(uint _a, uint _b) public view returns (uint, address) {        
        return _a.mult(_b);    }}
```

看见没有，这个_a.mult(_b) 的调用根直接用库的MathLib.mult(a, b)调用是一样的效果，使用了using A for B之后，调用库函数的B就成了被调用库函数的第一个参数，类似python的self。



这个‘ReentrancyGuard.sol’ 值得讲一讲，这是防重入攻击，这个不处理好，合约里的钱就被坏人偷走了。我在[这个帖子](https://hive.blog/cn/@chenlocus/reentrancy)里研究过一些，但是它这里没有用到我之前看过的任何方法，而是用了个非常简单粗暴的办法，就是在防重入的合约里定义个：uint256 private _guardCounter;  这意味着别的合约不可以访问这个私有变量。别人只要一调用就出错。把防重入合约里的modifier加到这个跟转钱相关的函数定义里，如此别人就无法重复调用buyTokens这个函数了。

`function buyTokens(address beneficiary) public nonReentrant payable`



这个构造函数要求ICO发行人先定义好她要发行的token是啥，这个需要另外一个合约来说明，而且必须是基于ERC20标准的代币。还需要发行人传入自己的钱包地址，这个地址除了账户，应该也可以是另外一个合约，如果发行人有好几个，她们可以在这个合约中对受到的以太坊进行分赃。



rate是发行的代币最小不可分割单位跟以太坊中钱的最小单位‘wei'之间的兑换比例，假设是2，然后你在Token合约中定义的小数点是3位，也就意味着你发行的代币最小单位是0.001，假设你的代币名字叫做’TOK‘，那么1个wei可以换0.002个TOK，一个以太价值： 1 **Ether** = 1,000,000,000,000,000,000 **Wei** (18个0)，算算看你的一个代币可以换几个以太？

```
constructor (uint256 rate, address payable wallet, IERC20 token) public {
        require(rate > 0, "Crowdsale: rate is 0");
        require(wallet != address(0), "Crowdsale: wallet is the zero address");
        require(address(token) != address(0), "Crowdsale: token is the zero address");

        _rate = rate;
        _wallet = wallet;
        _token = token;
    }
```



接下来说的是这个fallback函数，在这个合约收到别人转过来的钱财（不限于以太，还可以是代币）的时候，就调用它，或者别人调用了合约中不存在的函数，也调用它，黑白通吃的函数。



```
function () external payable {
        buyTokens(msg.sender);
    }
```

这个msg.sender就是转以太给你的那个人的钱包地址，也可以是合约地址，如果是合约地址，你要小心咯，它很可能收到你的代币后在它的fallback函数里还会进行别的操作，比如偷你的钱，所以合约的安全性审核是非常重要的。说到这里，不得不说说跟msg.sender容易混淆的另外一个东西： **tx**. **origin**，这个是最初发起合约调用的那个地址，这个本身不可能是合约，因为合约不会主动做什么事，假如： A 调用 B 合约，B合约调用C合约，对于C来说，msg.sender是B合约地址，tx.origin是A 地址。我到网上找了个图，来说明这个问题，这张图本身也有个**错误**，A不能是一个合约，必须是一个外部账户（就是我们常说的钱包账户）。

![image.png](https://images.hive.blog/DQmRydktQTcU62vbGSVzCEakKvPDV9P2dAKrgrSRaHF3i1P/image.png)

接下来看看buyTokens，这个函数必须防重入，这是防止黑客重复调用这个函数来盗用你的钱。它首先做一些例行检查_preValidatePurchase，看看调用它的是否是全零地址，全零地址是销毁以太用的，还会检查是不是有人耍你，转0个以太给你。接着它要用getTokenAmount来算算能给调用者几个Token，比如调用者转来10个wei，那么可以换20个Token。再计算下本合约一共圈了几个wei。接下来处于购买，反正这个最终会调用到Token合约里的safeTransfer，我也不懂它干嘛搞这么复杂，直接调用它就是了。然后做些可有可无的更新购买状态，发事件通知，最后通过wallet.transfer(msg.value)把收到的以太发给圈钱人自己的地址（钱包地址或者一个合约）。



```
 function buyTokens(address beneficiary) public nonReentrant payable {
        uint256 weiAmount = msg.value;
        _preValidatePurchase(beneficiary, weiAmount);

        // calculate token amount to be created
        uint256 tokens = _getTokenAmount(weiAmount);

        // update state
        _weiRaised = _weiRaised.add(weiAmount);

        _processPurchase(beneficiary, tokens);
        emit TokensPurchased(msg.sender, beneficiary, weiAmount, tokens);

        _updatePurchasingState(beneficiary, weiAmount);

        _forwardFunds();
        _postValidatePurchase(beneficiary, weiAmount);
    }
```





至此，一个最基本的众筹发币功能就实现了，但是圈钱模式各种各样，让人眼花缭乱，要不然怎么能很优雅地割韭菜呢？所以在此基础上，还有很多众筹方式。让我们一一看来。



先从validation文件夹下的CappedCrowdsale.sol合约开始，首先，接下来所有合约都要继承crowdsale.sol这个合约，所以不必具体实现圈钱的操作。这是一个有良心底线的合约。

![image.png](https://images.hive.blog/DQmcS4kVx4ixTMffWx6rv6mhft5zmgQE7D4wewcQTs9VaVC/image.png)

所以它对大于一定数目的以太是绝对不会接受的，在这里实现，多于cap的钱是塞不进去的：



```
function _preValidatePurchase(address beneficiary, uint256 weiAmount) internal view {
        super._preValidatePurchase(beneficiary, weiAmount);
        require(weiRaised().add(weiAmount) <= _cap, "CappedCrowdsale: cap exceeded");
    }
```



接下来再看看这个合约：IndividuallyCappedCrowdsale，这个合约可谓业界良心，它是对每个投资者（韭菜）想买币的数量设置一个上限，这样帮助人家风险控制（不至于输得连裤衩都没了）。当然，这个需要投资者自己去设置的。这个合约继承了Crowdsale, CapperRole，后面一个CapperRole很重要，投资者就是利用这个合约把自己加到具备设置投资上线的权限中，对，没有权限你还不能设置呢。这个合约中最主要的函数就是这个，它控制的是投资者的wei为单位的以太坊的投资上限：



```
 function _preValidatePurchase(address beneficiary, uint256 weiAmount) internal view {
        super._preValidatePurchase(beneficiary, weiAmount);
        // solhint-disable-next-line max-line-length
        require(_contributions[beneficiary].add(weiAmount) <= _caps[beneficiary], "IndividuallyCappedCrowdsale: beneficiary's cap exceeded");
    }

```



接下来看看validation文件夹下这个合约：PausableCrowdsale， 它继承了Crowdsale, Pausable这两个合约。 这个合约允许有Pausible权限的人随时中止和继续众筹行为，这个主要在Roles文件夹下的PauserRole来实现，从合约中看得出来，这个有中止和继续行为的人可不是普通投资者，这个需要合约所有者把你加进去的。这个合约是对购买前进行认证来控制众筹的进行，主要是加了一个modifier 函数whenNotPaused：



```
 function _preValidatePurchase(address _beneficiary, uint256 _weiAmount) internal view whenNotPaused {
        return super._preValidatePurchase(_beneficiary, _weiAmount);
    }
```



 这是个有用的东西，万一公安机关要你停止众筹行为，还可以用一下这个功能。



继续看validation文件夹下的TimedCrowdsale合约，我觉得这个合约很有用，大部分ICO项目圈钱不都有个开始和结束时间吗？和其它合约一样，它控制众筹行为是在购买前认证里完成的，函数外头加了个modifier onlyWhileOpen的函数，所以这个modifier真是非常有用，没有真是受不了：



```
function _preValidatePurchase(address beneficiary, uint256 weiAmount) internal onlyWhileOpen view {
        super._preValidatePurchase(beneficiary, weiAmount);
    }
```

这个对时间的判断是基于block.timestamp，就是当前区块的时间。当然，这种合约还是留了一手，万一人家圈钱圈得意犹未尽怎么办？还有这个函数可以把众筹结束时间来延长：

```
function _extendTime(uint256 newClosingTime) internal {
```



接下来是WhitelistCrowdsale这个合约，是对投资人进行权限限制，不是所有人都能买币的，它继承了WhitelistedRole, Crowdsale这两个合约，你想买他的币，还得事先跟他说好，需要有关系，他把你加入到白名单中，然后你才有资格去买币，这个估计一开始对于一些大的投资人或机构先一步买币。它的实现也是在买币之前来判断你是不是在白名单里头：

```
function _preValidatePurchase(address _beneficiary, uint256 _weiAmount) internal view {
        require(isWhitelisted(_beneficiary), "WhitelistCrowdsale: beneficiary doesn't have the Whitelisted role");
        super._preValidatePurchase(_beneficiary, _weiAmount);
    }
```



看完了validation文件夹，接着看price文件夹下的IncreasingPriceCrowdsale合约，它继承了TimedCrowdsale，就是说首先，它是有时间限制的，这很符合大多数ICO的特点。 这个合约是一个价格随着时间线性增加的，每个时间点的以太可以兑换的代币都不一样，这样自然有它的好处，就是增加韭菜的焦虑感，觉得越早买价格越便宜。所以我们要知道当前价格，需要得到当前区块时间戳来稍微计算一下：

```
function getCurrentRate() public view returns (uint256) {
        if (!isOpen()) {
            return 0;
        }

        // solhint-disable-next-line not-rely-on-time
        uint256 elapsedTime = block.timestamp.sub(openingTime());
        uint256 timeRange = closingTime().sub(openingTime());
        uint256 rateRange = _initialRate.sub(_finalRate);
        return _initialRate.sub(elapsedTime.mul(rateRange).div(timeRange));
    }
```

注意一下，rate☞每个wei可以兑换的代币数，所以价格上涨，意味着rate下降，每wei能买的代币变少。



看过了价格控制合约后，再来看emission文件夹下的两个合约，AllowanceCrowdsale和MintedCrowdsale，顾名思义，这两个合约就是控制代币的发放，前者是控制代币从某个钱包里发放给购买者，后者是启动即时印钞模式，每次有人购买，它就印出来给你。



AllowanceCrowdsale合约很简单，主要就是重载了crowdsale.sol合约中的deliverTokens函数：

```
function _deliverTokens(address beneficiary, uint256 tokenAmount) internal {
        token().safeTransferFrom(_tokenWallet, beneficiary, tokenAmount);
    }
```



MintedCrowdsale合约更简单，也是重载父合约中deliverTokens函数，并且其中调用了Token文件夹下的/token/ERC20/ERC20Mintable.sol合约里的函数，关于token下的合约，咱以后再说。



```
function _deliverTokens(address beneficiary, uint256 tokenAmount) internal {
        // Potentially dangerous assumption about the type of the token.
        require(
            ERC20Mintable(address(token())).mint(beneficiary, tokenAmount),
                "MintedCrowdsale: minting failed"
        );
    }
```





最后进入distribution目录下的四个合约，先看看FinalizableCrowdsale.sol，个人觉得这个合约没啥意义，主要就是在众筹结束后，作一个一次性的事情。



PostDeliveryCrowdsale合约不是在投资者购买代币的时候就把代币给人家的，而是先通过下面这个处理，先账面记录下应该给投资者账户上转移的代币，然后把投资者的代币转移到一个叫做‘_vault’的账户上去，

```
function _processPurchase(address beneficiary, uint256 tokenAmount) internal {
        _balances[beneficiary] = _balances[beneficiary].add(tokenAmount);
        _deliverTokens(address(_vault), tokenAmount);
    }
```

这个'_vault'的账户处理继承了ownership文件夹下的Secondary.sol合约。这个PostDeliveryCrowdsale合约，一定要在中众筹结束后，通过调用这个函数，才能把投资者的代币转移到她们的账户上去：

```
function withdrawTokens(address beneficiary) public {
        require(hasClosed(), "PostDeliveryCrowdsale: not closed");
        uint256 amount = _balances[beneficiary];
        require(amount > 0, "PostDeliveryCrowdsale: beneficiary is not due any tokens");

        _balances[beneficiary] = 0;
        _vault.transfer(token(), beneficiary, amount);
    }

```

我想这个合约的意图在于只有众筹结束后，才把代币分发给投资者，这样在众筹过程中出现问题也可以及时处理。



我们接着看“RefundableCrowdsale”这个合约，顾名思义，就是众筹没有达到一定的目标，就把钱（以太）返回给投资者。我们来想象一种情况，如果在众筹过程中，就把代币分发给投资者，而且众筹失败后还要把筹到的钱返回给投资者，那么就给坏蛋可趁之机，如果这个代币在众筹结束前就能够被交易（EOS就是这样，当然它众筹成功了，所以每发生我接下来说的悲剧），那么坏蛋一旦看到这个众筹有可能失败，他就在市场上抛售代币，然后接下来返回钱的时候他又拿到之前投资的钱，留下在市场上买下他的代币的韭菜欲哭无泪。既然如此，我们就需要在众筹成功后把代币分发给投资者，而不是众筹进行时，这个并没有在这里实现，而是在前面我说过的’PostDeliveryCrowdsale.sol’ 里实现了。



既然要允许返现必然要用到托管合约，就需要引入下面这个合约：

`import "../../payment/escrow/RefundEscrow.sol";`

我们在众筹结束后要一次性做一个事情，要么失败下海干活，要么成功会所嫩模，所以要引入下面这个合约：

`import "./FinalizableCrowdsale.sol";`



先看看这个合约的构造函数：

```
constructor (uint256 goal) public {
        require(goal > 0, "RefundableCrowdsale: goal is 0");
        _escrow = new RefundEscrow(wallet());
        _goal = goal;
    }

```

这意味着托管合约把收集到的以太暂存在构造众筹合约时候指定的钱包地址，然后还有一个众筹目标。咱先不多说托管合约怎么实现的，这个挺复杂，需要另外一篇文章详谈。



为了在众筹过程中收到的以太还能最后返回给投资者，这里重载了crowdsale.sol合约中的‘_forwardFunds’ 函数：

```
function _forwardFunds() internal {
        _escrow.deposit.value(msg.value)(msg.sender);
    }
```

你不用担心最后那个托管合约搞不清楚应该把多少以太分给谁，这个都在escrow合约里实现了，我们传入了参数msg.value 和msg.sender，它就能分得清楚该怎么返现。



我们再看看众筹结束后它做什么事情，如果重筹目标实现，那么通过“_escrow.beneficiaryWithdraw()” 来把筹到的钱转到自己钱包，不然就让托管合约能还钱。 

```
function _finalization() internal {
        if (goalReached()) {
            _escrow.close();
            _escrow.beneficiaryWithdraw();
        } else {
            _escrow.enableRefunds();
        }

        super._finalization();
    }
```



还钱函数是需要外界来调用的，其实关于钱的问题还是外界调用比较安全，如果这个功能放在前面的finalization里做，就容易被坏蛋用重入方法来盗取钱财。可见，最终还钱功能也是在escrow托管合约里完成的，这个合约功能还是蛮强大的。

```
function claimRefund(address payable refundee) public {
        require(finalized(), "RefundableCrowdsale: not finalized");
        require(!goalReached(), "RefundableCrowdsale: goal reached");

        _escrow.withdraw(refundee);
    }
```



这个托管合约变量被设置成private，也是用心良苦，这样黑客就无法来用别的合约来调用这里的托管合约。

`RefundEscrow private _escrow;`

![image.png](https://images.hive.blog/DQmeit7yTDEdyURHk9iNV8KLPaLvJQkkwAAXTVtuFk1K4LK/image.png)

最后来看看‘RefundablePostDeliveryCrowdsale.sol'这个合约，上面那个合约会造成如果众筹失败，并且众筹过程中发出去的币，你不能阻止好心的加密货币交易所们或者去中心化交易所将其纳入麾下能够让投资者交易，所以这个合约继承了‘PostDeliveryCrowdsale’，如此就可以在众筹结束后才把代币发放给投资者们，万一众筹失败，还可以把收到的钱财还给投资者，所以它又继承了‘RefundableCrowdsale’，谁如果用这个合约，可谓业界之良心，既考虑了ICO中投资者的利益，还考虑了市场上投资者的利益。这个合约只是重载了一个postDeliveryCrowdsale中的函数：



```
 function withdrawTokens(address beneficiary) public {
        require(finalized(), "RefundablePostDeliveryCrowdsale: not finalized");
        require(goalReached(), "RefundablePostDeliveryCrowdsale: goal not reached");

        super.withdrawTokens(beneficiary);
    }
```





智能合约也有它不足的地方，就是时间戳，一般使用矿工的本地时间作为时间戳，因为各个节点在时间上会有些微差别，导致不可靠，不能依赖时间戳做精确的事情；目前以太坊上智能合约也不能依赖随机数，因为每个节点运行结果必须是确定的；还有个问题，就是以太坊上智能合约只可以被调用，不可以主动发生，就是说在区块链上无法实现定时做某个事情的合约，虽然说智能合约中可以检查是否满足时间的条件，但是也必须是外部来调用这个函数，所以定时处理的事情需要在智能合约外完成。



接下来打算重新看看关于代币的合约。





相关文章阅读：

- [智能合约代替OTC市场](http://livinginau.life/2019/12/10/%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E4%BB%A3%E6%9B%BFotc%E5%B8%82%E5%9C%BA/)

- 
  [makeDAO 的 PETH](http://livinginau.life/2019/11/16/makeDAO_peth/)

- 
  [非对称加密解密的理解](http://livinginau.life/2017/12/05/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86%E7%9A%84%E7%90%86%E8%A7%A3/)

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
