# gasToken $CHI 的使用

最近写合约的过程中用到了CHI token节约gasfee，所以分享一下具体的使用方法。实际使用下来，在B链上还是能节约40%的gas的，值得一试。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

interface IFreeFromUpTo {
    function freeFromUpTo(address from, uint256 value) external returns (uint256 freed);
}

contract Swap {
    // CHI token的地址，这里是B链的地址
    IFreeFromUpTo constant public chi = IFreeFromUpTo(0x0000000000004946c0e9F43F4Dee607b0eF1fA1c);

    //定义一个modifier，在需要节约gas的方法上加上就好了
    modifier discountCHI {
        uint256 gasStart = gasleft();
        _;
        uint256 initialGas = 21000 + 16 * msg.data.length;
        uint256 gasSpent = initialGas + gasStart - gasleft();
        uint256 freeUpValue = (gasSpent + 14154) / 41947;

        // 方式1：CHI 是从调用者的地址中扣除，记得用这种方法务必要先approve给合约地址
        chi.freeFromUpTo(msg.sender, freeUpValue); 

        // 方式2：CHI 是从合约的地址中扣除，用这种需要给合约地址转入CHI
        //chi.freeFromUpTo(address(this), freeUpValue);
    }

    function swap() external discountCHI {
        // do something
    }
}
```

对于上面提到的两种`CHI`的扣除方式，我自己使用第一种是成功的，第二种实际测试没有扣除，知道的同学可以赐教一下。

随便找了个样例，实际使用的效果就像这个一样：https://bscscan.com/tx/0x90e0030431dd748ed9377f0a76ef956b5b0ad3c74b0ee55ee7445e71afaeb03d


Reference:

https://soliditydeveloper.com/chi-gastoken

https://blog.1inch.io/1inch-introduces-chi-gastoken-d0bd5bb0f92b