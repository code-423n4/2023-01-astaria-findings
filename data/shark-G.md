## 1. Enable/disable functions can be merged into a toggle function

The following functions have almost the same functionality:

File: `VaultImplementation.sol` [Line 101-117](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L101-L117)

```solidity
101:  /**
102:   * @notice disable the allowList for the vault
103:   */
104:  function disableAllowList() external virtual {
105:    require(msg.sender == owner()); //owner is "strategist"
106:    _loadVISlot().allowListEnabled = false;
107:    emit AllowListEnabled(false);
108:  }
109:
110:  /**
111:   * @notice enable the allowList for the vault
112:   */
113:  function enableAllowList() external virtual {
114:    require(msg.sender == owner()); //owner is "strategist"
115:    _loadVISlot().allowListEnabled = true;
116:    emit AllowListEnabled(true);
117:  }
```

To save gas, consider merging the functionalities into a new function, `setAllowListEnabled()`:

```solidity
  /**
   * @notice enable/disable the allowList for the vault
   */
  function setAllowListEnabled(bool isEnabled) external virtual {
    require(msg.sender == owner()); //owner is "strategist"
    _loadVISlot().allowListEnabled = isEnabled;
    emit AllowListEnabled(isEnabled);
  }
```

## 2. Splitting `require()` statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per &&.

- File: `PublicVault.sol` [Line 672-675](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672-L675)
- File: `PublicVault.sol` [Line 687-690](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687-L690)
- File: `Vault.sol` [Line 65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

## 3. Unused local variable

File: `PublicVault.sol` [Line 251-265](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L251-L265)

```solidity
  function deposit(uint256 amount, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[receiver]);
    }

    uint256 assets = totalAssets();

    return super.deposit(amount, receiver);
  }
```

As seen above, `uint256 assets` is never used. As such, it may be removed to save gas.

## 4. Unchecked math saves gas

Use the `unchecked` keyword to avoid unnecessary arithmetic checks and save gas when an underflow/overflow will not happen.

The following code line could be moved into the `unchecked` block as it will not overflow:

File: `AstariaRouter.sol` [Line 512](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L512)

## 5. Unused parameters

In the following functions, the parameters are never used:

- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L88
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-L102

## 6. `x += y` costs more gas than `x = x + y` (same for `-=`)

Using the addition operator instead of plus equals saves around **22 gas**

There are _22_ instances of this issue:

File: `AstariaRouter.sol` ([Line 512](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L512))

```solidity
512:      totalBorrowed += payout;
```

File: `LienToken.sol` [L160](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L160), [L161](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L161), [L162](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L162), [L164](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L164), [L210](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L210), [L480](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L480), [L524](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L524), [L525](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L525), [L679](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L679), [L720](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L720), [L735](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L735), [L830](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L830)

```solidity
160:      potentialDebt += _getOwed(
161:        params.encumber.stack[j],
162:        params.encumber.stack[j].point.end
164:      );

210:      maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);

480:        potentialDebt += _getOwed(newStack[j], newStack[j].point.end);

524:        totalSpent += spent;
525:        payment -= spent;

679:      totalCapitalAvailable -= spent;

720:      maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);

735:      maxPotentialDebt += _getOwed(stack[i], end);

830:      stack.point.amount -= amount.safeCastTo88();
```

File: `PublicVault.sol`, ([#L174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L174), [#L179](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L179), [#L380](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L380), [#L405](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L405), [#L565](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L565), [#L583](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L583), [#L606](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606), [#L624](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L624))

```solidity
174:    es.balanceOf[owner] -= shares;

179:      es.balanceOf[address(this)] += shares;

380:          s.withdrawReserve -= withdrawBalance.safeCastTo88();

405:        s.withdrawReserve -= drainBalance.safeCastTo88();

565;      s.slope += computedSlope.safeCastTo48();

583:      s.yIntercept += assets.safeCastTo88();

606:      s.strategistUnclaimedShares += feeInShares;

624:      s.yIntercept += params.increaseYIntercept.safeCastTo88();
```

Files: `VaultImplementation.sol` [Line 404](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L404)

```solidity
404:        amount -= fee;
```

File: `WithdrawProxy.sol` ([Line 237](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L237), [Line 277](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L277), [Line 313](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L313))

```solidity
237    s.withdrawReserveReceived += amount;

277        balance -= transferAmount;

313      s.expected += newLienExpectedValue.safeCastTo88();
```
