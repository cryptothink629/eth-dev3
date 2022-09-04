# BSC链 full node 全节点搭建

这篇文章的背景是我前不久刚搭了以太坊链的节点，想跑一些程序在以太坊上，但gas费太高，调bug成本太高，就想着换一个EVM兼容链先试试。
选bsc的原因是用户基数本身也够大，过程中有些bug搜一搜还是能搜到的。bsc node 安装也比较偏简单，就是以太坊的geth fork过来的，
`geth` 的参数选项完全兼容， 非常方便。

话不多说， 先放上bsc官方`geth`仓库链接: `https://github.com/bnb-chain/bsc`. 其实有兴趣的同学，照着里边的描述完全可以自己搭出来了， 不过bsc版的geth安装步骤描述没那么清晰，得花点时间。

容量问题，bsc和eth一样，也有`snap`模式，而且也是geth的启动默认模式。在snap模式下，你需要至少至少留800G的硬盘空间。硬盘类型也必须至少是SSD的。

实际安装的问题，官方推荐了两种方式`Sync From Snapshot`, `Sync From Genesis Block`. 第一种是相当于你提前下载一个压缩包， 这个
压缩包里有bsc官方已经帮你压缩处理好的历史数据，你把数据拿到，然后直接运行就好了。第二种也好理解，你自己慢慢从第一个区块下载，
到处寻找peers节点问问能不能帮帮你 :) 很明显第一种快太多了.

整理下思路，我们要做的事情就是：1. 下载 snapshot(历史数据), 1.1 同时安装bsc版的gth 客户端， 2然后运行就好了。

### 1. 下载snapshot

官方获取snapshot的地址`https://github.com/bnb-chain/bsc-snapshots`, 照着里边的介绍做，你就下好了数据，
并且把数据解压放到`datadir`里，等着geth客户端使用。

值得一提的是，下载时除了用`wget`, 里边推荐使用`aria2c` 这个软件， 分布式下载，速度巨快，我这里也非常推荐。
```shell
# 注意从上边的snapshot链接找到你最新的下载包
aria2c -o geth.tar.lz4 -s14 -x14 -k100M https://download.bsc-snapshot.workers.dev/geth-20220820.tar.lz4 -o geth.tar.lz4
```

### 2. 准备geth

上边下载的同时，我们准备geth客户端。
这里我们选择的方式是直接下载编译好的二进制文件，免去了其它方式会遇到的很多麻烦，比如安装各种依赖，找不到库等。
```shell
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep geth_linux |cut -d\" -f4)
```
会得到一个可执行文件`geth_linux`, 记得给他赋予可执行权限。

然后需要下载genesis文件，不然我们后边即使用了下好snapshot也是不行的哦。
```shell
wget   $(curl -s https://api.github.com/repos/bnb-chain/bsc/releases/latest |grep browser_ |grep mainnet |cut -d\" -f4)
unzip mainnet.zip
```
解压后会得到两个文件`genesis.json` 和 `config.toml`, 后一个是正对geth的配置文件，里边已经预配置好了一些东西
比如chainId之类的。

### 3. run

等到下载完成，选好你的data文件夹，配置好你要的参数在`config.toml`里，然后开始运行同步吧。从snapshot同步到最新的，还会需要一小点时间。
```
geth --config ./config.toml --datadir ./node
```

#### Tips

```
# keep light storage
nohup geth snapshot prune-state --datadir {the data dir of your bsc node} &
```

## 总结

这篇文章介绍了bsc链的节点部署，而且推荐了一种简洁有效的路径来完成，没有试图大而全的介绍完所有的其他方法。 遗憾的是这篇文章并不是手把手教学，只适合搭建过以太坊链的，
对环境配置有一些概念的同学。

**加入Discord社区，讨论任何环境搭建遇到的问题:** [https://discord.gg/vujpgYnwWk](https://discord.gg/vujpgYnwWk)

Reference:

https://github.com/bnb-chain/bsc

https://github.com/bnb-chain/bsc-snapshots

https://medium.com/@pieterc/running-a-full-node-with-geth-bsc-610085141248

https://github.com/bnb-chain/bsc/issues/502

https://github.com/ethereum/go-ethereum/issues/24413
