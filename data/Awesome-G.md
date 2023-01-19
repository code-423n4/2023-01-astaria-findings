# 1 . Splitting `require()` Statements That Use `&&` Saves Gas - (Saves ~`3` Gas per `&&`)

Instead of using the `&&` operator in a single require statement to check multiple conditions, using various require statements with 1 condition per require statement will save ~3 GAS per `&&`.

Affected line of code:

- [Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

# 2. Avoid re-initializing default values to save gas

Depending on the type of variable you do not need to initialize the value for example a `uint` value by default is `0` so there is no need to reinitialize the value.

Affected lines of code:

- [LienToken.sol#L152](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L152)
- [LienToken.sol#L636](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L636)


# 3. Regular addition/subtraction assignment saves gas

Using standard addition or subtraction assignment (`x = x + y` or `x = x - y`) rather than the shorthand versions (`x += y` or `x -= y`) saves gas. This can save approximately 22 gas per occurrence.

Affected line of code:

- [LienToken.sol#L160](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L160)

- [LienToken.sol#L210](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L210)

- [LienToken.sol#L480](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L480)

- [LienToken.sol#L524](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L524)

- [LienToken.sol#L525](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L525)

- [LienToken.sol#L679](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L679)

- [LienToken.sol#L720](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L720)

- [LienToken.sol#L735](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L735)

- [LienToken.sol#L830](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L830)

- [AstariaRouter.sol#L512](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L512)

- [PublicVault.sol#L174](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L174)
- [PublicVault.sol#L179](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L179)
- [PublicVault.sol#L380](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L380)
- [PublicVault.sol#L405](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L405)

- [PublicVault.sol#L565](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L565)
- [PublicVault.sol#L583](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L583)
- [PublicVault.sol#L606](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L606)
- [PublicVault.sol#L624](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L624)
- [WithdrawProxy.sol#L237](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L237)
- [WithdrawProxy.sol#L277](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L277)
- [WithdrawProxy.sol#L313](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L313)

- [VaultImplementation.sol#L404](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L404)

# 4. Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

For example, the code at [AstariaRouter.sol#L505-L515](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L505-L515)
can be refactored to as:

```solidity
File: main/src/AstariaRouter.sol
    uint256 i;
    for (; i < commitments.length; ) {
      if (i != 0) {
        commitments[i].lienRequest.stack = stack;
      }
      uint256 payout;
      (lienIds[i], stack, payout) = _executeCommitment(s, commitments[i]);
      unchecked {
      totalBorrowed += payout;
        ++i;
      }
```

Affected line of code:

- [AstariaRouter.sol#L505-L515](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L505-L515)

# 5. Use named return values as local variables

Using named return values as temporary local variables can help to save on gas costs.

For example, the code at [AstariaRouter.sol#L402-L405](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L402-L405) could be refactored as follows:

```solidity
File: main/src/AstariaRouter.sol
    function getAuctionWindow(bool includeBuffer) public view returns (uint256    auctionWindow) {
        RouterStorage storage s = _loadRouterSlot();
        auctionWindow = s.auctionWindow + (includeBuffer ? s.auctionWindowBuffer : 0);
    }
```

Affected line of code:

- [AstariaRouter.sol#L402-L405](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L402-L405)

# 6. inline `internal` functions

If an `internal` functions code block only uses 1 line of code or the `internal` function is called by one instance can be inline to save gas.

This will save around 20 to 40 gas this is because of two extra JUMP instructions and additional stack operations needed for function calls.

For example the function on [lines 556-560](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L556-L560)...

```solidity
File: main/src/PublicVault.sol
Line 556:  function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
Line 557:    unchecked {
Line 558:      s.epochData[epoch].liensOpenForEpoch++;
Line 559:    }
Line 560:  }
```

...is only called on [line 455](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L455), instead the function `_increaseOpenLiens` could be omitted and
inline

```solidity
Line 455:  _increaseOpenLiens(s, epoch);
```

Affected line of code:

- [PublicVault.sol#L556-L560](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L556-L560)