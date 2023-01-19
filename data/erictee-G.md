### [G-01] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
src/ClearingHouse.sol:L104  function setApprovalForAll(address operator, bool approved) external {}

src/ClearingHouse.sol:L186  ) public {}

```
### [G-02] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
src/WithdrawProxy.sol:L237    s.withdrawReserveReceived += amount;

src/WithdrawProxy.sol:L277        balance -= transferAmount;

src/WithdrawProxy.sol:L313      s.expected += newLienExpectedValue.safeCastTo88();

src/LienToken.sol:L160      potentialDebt += _getOwed(

src/LienToken.sol:L210      maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);

src/LienToken.sol:L480        potentialDebt += _getOwed(newStack[j], newStack[j].point.end);

src/LienToken.sol:L524        totalSpent += spent;

src/LienToken.sol:L525        payment -= spent;

src/LienToken.sol:L679      totalCapitalAvailable -= spent;

src/LienToken.sol:L720      maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);

src/LienToken.sol:L735      maxPotentialDebt += _getOwed(stack[i], end);

src/LienToken.sol:L830      stack.point.amount -= amount.safeCastTo88();

src/PublicVault.sol:L174    es.balanceOf[owner] -= shares;

src/PublicVault.sol:L179      es.balanceOf[address(this)] += shares;

src/PublicVault.sol:L380          s.withdrawReserve -= withdrawBalance.safeCastTo88();

src/PublicVault.sol:L405        s.withdrawReserve -= drainBalance.safeCastTo88();

src/PublicVault.sol:L565      s.slope += computedSlope.safeCastTo48();

src/PublicVault.sol:L583      s.yIntercept += assets.safeCastTo88();

src/PublicVault.sol:L606      s.strategistUnclaimedShares += feeInShares;

src/PublicVault.sol:L624      s.yIntercept += params.increaseYIntercept.safeCastTo88();

src/VaultImplementation.sol:L404        amount -= fee;

src/AstariaRouter.sol:L512      totalBorrowed += payout;

```

### [G-03] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
src/Vault.sol:L65    require(s.allowList[msg.sender] && receiver == owner());

src/PublicVault.sol:L672    require(

src/PublicVault.sol:L687    require(


```

