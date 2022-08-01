# 以太坊开发从0到1


看到很多同学想接触以太坊开发，但是看到茫茫多的关键字，不知道从哪开始，我是先搭个节点呢，还是先学`solidity`呢，还是先一键发个币呢？ 我自己本身也是个初学者，我想从自身感受来出发，介绍下我理解的以太坊开发中的各个部分和学习步骤。

以太坊开发，是一个大的范围，实际上它可以分成几个小部分理解，甚至你完全不接触其中的一些部分(比如`solidity`语言)，你也可以参与到以太坊开发中来。
不过首先我想声明的是，你必须有一点点编程的基础，这样更好理解。

首先，以太坊开发，我们不是去构建它的核心组件，像开发操作系统那样写复杂的模块算法和数据结构，而是一个和以太坊交互的过程，大概分这么两种：

1. 查询数据。这类的开发就不需要你去了解智能合约的编程语言。
你需要的是一个和以太坊交互的语言，比如python或者js，去编写一个客户端，以太坊网络类似一个服务端，然后你像发送http请求一样，从以太坊网络取得你要的数据。
这方面的例子有`nansen`，`debank`等链上分析应用。

2. 如果第一种算是读数据，那么第二种就是写数据了。发币，发NFT，都是往以太坊网络存数据，
除了需要一个和以太坊交互的客户端以外，你还要自己准备要储存的数据，以及处理数据的逻辑，那么就是写智能合约。
转账也是写数据，但它只是改变原来已有的状态，所以你只需要一个交互客户端和填几个参数就ok了。

有了整体的概念以后我们再来看什么是我们要优先了解的。很明显，你必须知道以太坊世界为你抽象出的各种概念，理解这些概念甚至不需要编程基础，但又是非常重要的，不然交互无从谈起。
比如，`address`，`block`，`gas`，`transaction`等等。这一部分，很显然，我把它叫做**以太坊的基本概念**。

再然后，你要做的就是优先实践，具体说就是查询一个简单的以太坊数据，比如最新的区块号，或者某个地址的余额。
要做这件事情，需要两样东西，一个是提供以太坊查询服务的`provider`和你自己的`交互客户端`。具体到这两样东西，分别又有很多不同的选择。
`provider`分自建的和第三方服务商提供的，大白话就是给你提供接入以太坊网络服务的。因为自己本地搭建节点的话，需要耗费很多资源，只是简单查询的话，用第三方提供的节点就很方便，后边会具体介绍。

```
/*

+--------------------------------+
|                                |
|                                |
|       Ethereum Network         |
|                                |
|                                |
|           Provider             |
+--------------------------------+

            |     ^
      query |     | Deploy Contract
            |     |
            v     |
    +-------------------+
    |                   |      +--------------+
    |                   |      |              |
    |      Client       | <--- |Smart Contract|
    |                   |      |              |
    |                   |      +--------------+
    +-------------------+
    
 */
```


`交互客户端`，最简单来说，可能是三行python代码，第一行连接provider，第二行创建个Web3对象，第三行查询最新的区块号。
`客户端`同时也会负责编译，发布或调试智能合约。像上图中所示，它主要是和以太坊网络沟通的。
以上这一部分，我们谈到了两个模块，那就是`provider`和`交互客户端`。

实践完读数据，下面实践写数据。发个币，发个NFT？这时候就需要学习智能合约的语言了。什么solidity语法，ERC20或者ERC721接口标准都可以在这个阶段学习。
最后这部分很明显涉及到的模块就是`智能合约`了。

Ok, 上面提到的四个模块，`以太坊基本概念`, `provider`, `交互客户端`, `智能合约`没有一定之分，只是我学习过程中的个人理解。 第一个是概念理解，第二个结合了概念和搭环境的实操，后边两个都偏code，分别运行在本地机器和以太坊网络上。然后在看的时候穿插着看，相互映证。

好了，介绍了这些具体的模块，我想谈谈我个人对区块链或者是以太坊的理解。以太坊在我看来就是对现有计算机系统的一个更高级的抽象。
我们先有了计算机，然后抽象出了虚拟机，屏蔽了物理机这一层，在这之上又构建很多现代服务比如分布式，云计算。但更上一层的抽象是什么？
是一个统一存储，计算和网络资源的计算机，它屏蔽了单体计算机本身所具有的内存，硬盘，cpu等概念，可能变成了memory，storage，和gas。
所以以太坊是科技上更加向上的一个抽象，是下一个时代科技浪潮的基石，依托这层抽象，我们已经也会继续构建出一些新的玩法和商业模式。

下面的各个部分，会分别就我上边介绍过的，列一些有用的资源。

### 以太坊基本概念

了解以太坊当中的一些基础概念，最好先把官网的文档浏览一遍，然后再慢慢搜关键字深入了解。这一类的文档非常多，下面就只放个官网链接吧。

* [ethereum.org/intro-to-ethereum](https://ethereum.org/en/developers/docs/intro-to-ethereum/)

### Provider

手动搭建node的话，这块步骤较多，我们使用`geth`来帮助你搭建节点。它本身有三种模式`light`, `snap`, `full`.
`full`模式的话硬盘占用空间太大了2~3个T, 不当矿工的话，不建议选这个。`light`模式最快，不怎么占硬盘，但使用过程中频繁报找不到peer node，
体验很差。因为`light`模式不下载大部分的数据，很多校验操作都依赖其他的node不能独立完成。还好我们有`snap`模式，geth默认也是启动这个模式的。
`snap`模式大概占用800G的硬盘。安装和使用`geth`参考官方手册：

* [https://geth.ethereum.org/docs/install-and-build/installing-geth](https://geth.ethereum.org/docs/install-and-build/installing-geth)

如果借用第三方服务的话，非常简单，经常使用的有如下两家：

* [https://infura.io](https://infura.io/)
* [https://dashboard.alchemyapi.io](https://dashboard.alchemyapi.io)

注册完毕的话，通常你会得到一个类似这样的`https://mainnet.infura.io/v3/***`api 接口，这里我们选用了http的，还有IPC的连接方式。

然后可以通过下面这种方式，来立刻上手，和以太坊主网来一次完美的talk， 查询最新的区块编号。注意python需要先安装好`web3`的包，其他语言同理。
```python
from web3 import Web3, HTTPProvider
w3 = Web3(HTTPProvider('https://mainnet.infura.io/v3/***'))
print ("Latest Ethereum block number" , w3.eth.blockNumber)
```

### 交互客户端

这一部分就是搭建本地开发环境。上面那节，其实已经展示过了，一个极简的客户端成功地和以太坊主网完成了talk。

客户端这一部分，可以分成很多语言的栈，`python`, `js`, `java`之类的。我个人只用过`python`和`js`的。
他们分别提供了`web3py` 和`web3js`开发包，封装了各种处理消息发送和处理接收消息的模块，来方便开发。阅读下边的开发文档：

* [https://web3py.readthedocs.io/en/stable](https://web3py.readthedocs.io/en/stable/)
* [https://web3js.readthedocs.io/en/v1.7.4](https://web3js.readthedocs.io/en/v1.7.4/)

另外`js`社区还更多用另外一个包`etherjs`来开发。

除此之外，你可能已经了解到了集成开发环境的概念，比如`brownie`, `hardhat`等，`brownie`是python开发栈使用，`hardhat`是js栈使用，更专业和全面。
他们都提供了编译，集成测试，部署等操作，是你后边开发调试智能合约必不可少的工具。他们是对上边提到的`web3py`,`web3js`更进一步的封装。

* [https://eth-brownie.readthedocs.io/en/stable](https://eth-brownie.readthedocs.io/en/stable/)
* [https://hardhat.org/docs](https://hardhat.org/docs)

下面是一些官方对于各个开发栈的介绍

* [https://ethereum.org/en/developers/docs/ethereum-stack](https://ethereum.org/en/developers/docs/ethereum-stack/)
* [https://ethereum.org/en/developers/docs/frameworks](https://ethereum.org/en/developers/docs/frameworks/)

下边是一片python栈从头开始搭建到交互的文章, 值得参考

* [https://snakecharmers.ethereum.org/a-developers-guide-to-ethereum-pt-1/](https://snakecharmers.ethereum.org/a-developers-guide-to-ethereum-pt-1/)

### 智能合约 Solidity

到了这个部分，你可以研究的范围就很多了，你可以研究怎么发币和NFT，你也可以做一个mini版的swap，
还可以做数据分析，扒数据，监听event等等，范围非常大了。

入门的话，直接推荐看下面这个`WFTSolidity`中文教程，非常优质的教程，还有部署各种合约的样例

* [https://github.com/AmazingAng/WTFSolidity](https://github.com/AmazingAng/WTFSolidity)

另外还可以看下官方文档和一些英文教程

* [Solidity官方文档](https://docs.soliditylang.org/en/latest/)
* [Solidity by Example](https://solidity-by-example.org/app/erc20/)


### 总结

我大概总结了4个模块，来切分以太坊开发入门时需要理解的部分，怕很多杂乱的概念把新手朋友吓到。
值得一提的是，我刻意避免提到怎么安装具体的程序，具体有哪些步骤，需要执行哪些命令行。
一个原因是同作为初学者，很难覆盖全面，这样会造成误导；另一个原因是因为我觉得最好的方式
就是看官网，自己搜索关键字，自己实践，看别人提取之后的一些操作和命令，非常有局限性。


**欢迎加入Discord社区，讨论开发遇到的问题，一起做开源项目:** [https://discord.gg/vujpgYnwWk](https://discord.gg/vujpgYnwWk)