### 1、整型溢出
计算机中整数变量有上下界，如果在算术运算中出现越界，即超出整数类型的最大表示范围，数字便会如表盘上的时针从12到1一般，
由一个极大值变为一个极小值或直接归零。此类越界的情形在传统的软件程序中很常见，但是否存在安全隐患取决于程序上下文，部分溢出是良性的（如tcp序号等），甚至是故意引入的（例如用作hash运算等）。
以太坊虚拟机（EVM）为整数指定固定大小的数据类型。这意味着一个整型变量只能有一定范围的数字表示。例如，一个 uint8 ，只能存储在范围 [0,255] 的数字。
试图存储 256 到一个 uint8 将变成 0。不加注意的话，只要没有检查用户输入又执行计算，导致数字超出存储它们的数据类型允许的范围，Solidity 中的变量就可以被用来组织攻击。
##### 乘法溢出：
案例：CVE-2018-10299（https://nvd.nist.gov/vuln/detail/CVE-2018-10299）
在该合约当中的第24行，在计算需要转账的总额度amount时未使用SafeMath函数进行溢出检查，直接将转账的地址个数与每个地址接收的代币数量进行乘法操作，如果这里输入极大的value，那么amount的值将有可能发生溢出，导致代币增发。
案例二： CryptonitexCoin：
合约地址：https://etherscan.io/address/0x2c0c06dae6bc7d7d7d8b0d67a1f1a47d6165e373#code
##### 加法溢出
案例： GEMCHAIN
合约地址：https://etherscan.io/address/0xfb340423dfac531b801d7586c98fe31e12a32f31#code
#### 防范
Openzeppline:
https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/math/SafeMath.sol

pragma solidity ^0.5.2;

/**
 * @title SafeMath
 * @dev Unsigned math operations with safety checks that revert on error
 */
library SafeMath {
    /**
     * @dev Multiplies two unsigned integers, reverts on overflow.
     */
    function mul(uint256 a, uint256 b) internal pure returns (uint256) {
        // Gas optimization: this is cheaper than requiring 'a' not being zero, but the
        // benefit is lost if 'b' is also tested.
        // See: https://github.com/OpenZeppelin/openzeppelin-solidity/pull/522
        if (a == 0) {
            return 0;
        }

        uint256 c = a * b;
        require(c / a == b);

        return c;
    }

    /**
     * @dev Integer division of two unsigned integers truncating the quotient, reverts on division by zero.
     */
    function div(uint256 a, uint256 b) internal pure returns (uint256) {
        // Solidity only automatically asserts when dividing by 0
        require(b > 0);
        uint256 c = a / b;
        // assert(a == b * c + a % b); // There is no case in which this doesn't hold

        return c;
    }

    /**
     * @dev Subtracts two unsigned integers, reverts on overflow (i.e. if subtrahend is greater than minuend).
     */
    function sub(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b <= a);
        uint256 c = a - b;

        return c;
    }

    /**
     * @dev Adds two unsigned integers, reverts on overflow.
     */
    function add(uint256 a, uint256 b) internal pure returns (uint256) {
        uint256 c = a + b;
        require(c >= a);

        return c;
    }

    /**
     * @dev Divides two unsigned integers and returns the remainder (unsigned integer modulo),
     * reverts when dividing by zero.
     */
    function mod(uint256 a, uint256 b) internal pure returns (uint256) {
        require(b != 0);
        return a % b;
    }
}
##### 应用了SafeMath函数的智能合约实例：
https://etherscan.io/address/0xB8c77482e45F1F44dE1745F52C74426C631bDD52#code
