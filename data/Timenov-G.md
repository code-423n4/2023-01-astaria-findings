# Astaria gas optimization report by Timenov

## Summary
G-01 Using `> 0` costs more gas than using `!= 0`
G-02 Using `i++` costs more gas than using `++i`.
G-03 Using `x += y` costs more gas than using `x = x + y`

### [G-01] Using `> 0` costs more gas than using `!= 0`
*There are 11 instances of this issue.*

```solidity
File: src/ClearingHouse.sol

160: if (ERC20(paymentToken).balanceOf(address(this)) > 0)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol

```solidity
File: src/WithdrawProxy.sol

280: if (balance > 0)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol

```solidity
File: src/PublicVault.sol

277: if (timeToEpochEnd() > 0)
282: if (s.withdrawReserve > 0)
303: if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0)
393: if (s.withdrawReserve > 0 && timeToEpochEnd() == 0 && withdrawProxy != address(0))
363: if (params.remaining > 0)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol

```solidity
File: src/AstariaRouter.sol

476: if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol

```solidity
File: src/LienToken.sol

438: if (params.stack.length > 0)
491: if (stack.length > 0 && potentialDebt > newSlot.lien.details.maxPotentialDebt)
642: if (payment > 0)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol

### [G-02] Using `i++` costs more gas than using `++i`.
*There are 3 instances of this issue.*

```solidity
File: src/PublicVault.sol

341: s.currentEpoch++;
539: s.epochData[epoch].liensOpenForEpoch--;
558: s.epochData[epoch].liensOpenForEpoch++;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol

### [G-03] Using x += y costs more gas than using x = x + y
*There are 21 instances of this issue.*

```solidity
File: src/WithdrawProxy.sol

237: s.withdrawReserveReceived += amount;
277: balance -= transferAmount;
313: s.expected += newLienExpectedValue.safeCastTo88();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol

```solidity
File: src/PublicVault.sol

174: es.balanceOf[owner] -= shares;
179: es.balanceOf[address(this)] += shares;
380: s.withdrawReserve -= withdrawBalance.safeCastTo88();
405: s.withdrawReserve -= drainBalance.safeCastTo88();
565: s.slope += computedSlope.safeCastTo48();
583: s.yIntercept += assets.safeCastTo88();
606: s.strategistUnclaimedShares += feeInShares;
624: s.yIntercept += params.increaseYIntercept.safeCastTo88();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol

```solidity
File: src/AstariaRouter.sol

512: totalBorrowed += payout;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol

```solidity
File: src/LienToken.sol

160: potentialDebt += _getOwed(params.encumber.stack[j], params.encumber.stack[j].point.end);
210: maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);
480: potentialDebt += _getOwed(newStack[j], newStack[j].point.end);
524: totalSpent += spent;
525: payment -= spent;
679: totalCapitalAvailable -= spent;
720: maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);
735: maxPotentialDebt += _getOwed(stack[i], end);
830: stack.point.amount -= amount.safeCastTo88();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol