## Gas Optimizations

| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES | 22 |
| [GAS-2](#GAS-2) | SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS | 3 |
| [GAS-3](#GAS-3) | FUNCTION STATE MUTABILITY CAN BE RESTRICTED TO PURE | 2 |
| [GAS-4](#GAS-4) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 20 |
| [GAS-5](#GAS-5) | ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW | 1 |

###  [GAS-1] X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Instances (22)*:
```solidity
File: AstariaRouter.sol

513:      totalBorrowed += payout; 

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L513)

```solidity
File: LienToken.sol

159:      potentialDebt += _getOwed(

209:      maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);

473:      potentialDebt += _getOwed(newStack[j], newStack[j].point.end);

517:      totalSpent += spent;

518:      payment -= spent;

661:      totalCapitalAvailable -= spent;

704:      maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);

722:      maxPotentialDebt += _getOwed(stack[i], end);

814:      stack.point.amount -= amount.safeCastTo88();

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol)

```solidity
File: PublicVault.sol

174:      es.balanceOf[owner] -= shares;

179:      es.balanceOf[address(this)] += shares;

374:      s.withdrawReserve -= withdrawBalance.safeCastTo88();

399:      s.withdrawReserve -= drainBalance.safeCastTo88();

556:      s.slope += computedSlope.safeCastTo48();

573:      s.yIntercept += assets.safeCastTo88();

596:      s.strategistUnclaimedShares += feeInShares;

613:      s.yIntercept += params.increaseYIntercept.safeCastTo88();

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)

```solidity
File: WithdrawProxy.sol

230:      s.withdrawReserveReceived += amount;

270:      balance -= transferAmount;

305:      s.expected += newLienExpectedValue.safeCastTo88();

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

```solidity
File: VaultImplementation.sol

403:      amount -= fee;
```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L403)

### [GAS-2] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Saves 16 gas per instance. 

*Instances (3):*
```solidity
File: PublicVault.sol

662:      currentEpoch != 0 &&

667:      currentEpoch != 0 &&
```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L662)

```solidity
File: Vault.sol

60:      require(s.allowList[msg.sender] && receiver == owner());

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L60)

### [GAS-3] FUNCTION STATE MUTABILITY CAN BE RESTRICTED TO PURE

*Instances (2):*
```solidity
File: AuthInitializable.sol

34:       function _getAuthSlot() internal view returns (AuthStorage storage s) {

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AuthInitializable.sol#L34)

```solidity
File: utils/Initializable.sol

346:       function _getInitializerSlot()

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/utils/Initializable.sol#L346)

### [GAS-4] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

*Instances (20):*
* [`AstariaRouter` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L134)
* [`AstariaRouter` Instance 2](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L150)
* [`AstariaRouter` Instance 3](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L166)
* [`AstariaRouter` Instance 4](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L182)
* [`AstariaRouter` Instance 5](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L192)
* [`AstariaRouter` Instance 6](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L429)
* [`AstariaRouter` Instance 7](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L768-L769)
* [`CollateralToken` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L162)
* [`CollateralToken` Instance 2](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L177)
* [`LienToken` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L427)
* [`LienToken` Instance 2](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L580)
* [`LienToken` Instance 3](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L603)
* [`LienToken` Instance 4](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L710)
* [`PublicVault` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L143)
* [`PublicVault` Instance 2](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L717)
* [`VaultImplementation` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L357)
* [`WithdrawProxy` Instance 1](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L136)
* [`WithdrawProxy` Instance 2](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L147)
* [`WithdrawProxy` Instance 3](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L172)
* [`WithdrawProxy` Instance 4](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L193)

### [GAS-5] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per instance.

*Instances (1):*
```solidity
File: VaultImplementation.sol

69:       s.strategistNonce++;

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L69)