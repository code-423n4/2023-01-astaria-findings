# Gas Report
## Finding Summary
||Issue|Instances|Gas Savings (from provided tests)|
|-|-|-|-|
|[G-01]|&& In If Statement(s)|4|-5142
|[G-02]|`i--`/`i-=1` Used Over `--i`|3|-243
|[G-03]|`x += y` Used|13|-90
|[G-04]|&& In Require Statement(s)|1|-72
|[G-05]|Struct Can Be Packed Into Fewer Slots|1|

### [G-01] && In If Statement(s)

Seperating if statements saves gas.
**Example**
```solidity
//From
if (a != HIGH && b != LOW) {
	//Do Something
}
//To
if (a != HIGH) {
	if (b != LOW) {
		//Do Something
	}
}
```

*/src/PublicVault.sol*
Links: [586](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L586).
```solidity
586:	if (v.depositCap != 0 && totalAssets() >= v.depositCap) {
```

*/src/AstariaRouter.sol*
Links: [476](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L476).
```solidity
476:	if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd) {
```

*/src/LienToken.sol*
Links: [268](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L268), [271](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L271).
```solidity
268:	if (stateHash == bytes32(0) && stack.length != 0) {
271:	if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
```

### [G-02] `i--`/`i-=1` Used Over `--i`

`--i` increments `i` directly. When using `i--`/`i-=1` solc must create a temporary value, thus resulting in higher gas.

*/src/PublicVault.sol*
Links: [539](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L539).
```solidity
539:	s.epochData[epoch].liensOpenForEpoch--;
```
**Suggested Change**
```solidity
539:	--s.epochData[epoch].liensOpenForEpoch;
```

*/lib/gpl/src/ERC721.sol*
Links: [136](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L136), [223](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L223).
```solidity
136:	s._balanceOf[from]--;
223:	s._balanceOf[owner]--;
```
**Suggested Change**
```solidity
136:	--s._balanceOf[from];
223:	--s._balanceOf[owner];
```

### [G-03] `x += y` Used 

`x = x + y` is cheaper than `x += y` in some cases like below.

*/src/WithdrawProxy.sol*
Links: [237](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L237), [313](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L313).
```solidity
237:	s.withdrawReserveReceived += amount;
313:	s.expected += newLienExpectedValue.safeCastTo88();
```

*/src/PublicVault.sol*
Links: [565](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L565), [583](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L583), [606](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606), [624](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L624).
```solidity
565:	s.slope += computedSlope.safeCastTo48();
583:	s.yIntercept += assets.safeCastTo88();
606:	s.strategistUnclaimedShares += feeInShares;
624:	s.yIntercept += params.increaseYIntercept.safeCastTo88();
```

*/src/AstariaRouter.sol*
Links: [512](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L512).
```solidity
512:	totalBorrowed += payout;
```

*/src/LienToken.sol*
Links: [160](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L160), [210](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L210), [480](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L480), [524](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L524), [720](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L720), [735](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L735).
```solidity
160:	potentialDebt += _getOwed(
210:	maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);
480:	potentialDebt += _getOwed(newStack[j], newStack[j].point.end);
524:	totalSpent += spent;
720:	maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);
735:	maxPotentialDebt += _getOwed(stack[i], end);
```

### [G-04] && In Require Statement(s)

Seperating require statements saves gas.
**Example**
```solidity
//From
require(a != HIGH && b != LOW);
//To
require(a != HIGH);
require(b != LOW);
```

*/src/Vault.sol*
Links: [65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65).
```solidity
65:	require(s.allowList[msg.sender] && receiver == owner());
```

### [G-05] Struct Can Be Packed Into Fewer Slots

Structs are divided into 32 slots. Minimizing the number of slots used per struct saves on gas.

*src/interfaces/IAstariaRouter.sol*
* The [`StrategyDetailsParam`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L101) struct uses 3 32 byte slots: [[1], [32], [20]] respectively. The slots can be rearranged as follows to minimize the number of slots to 2: [[1, 20], [32]].