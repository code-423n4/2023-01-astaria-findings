## 1. Functions that can only be called by users with correct permissions/roles can be marked `payable`

Functions with access control will cost less gas when marked as `payable` (assuming the caller has correct permissions). This is because the compiler doesn't need to check for `msg.value`, which saves approximately **20 gas** per call.

For example, the following functions may be marked `payable` to save gas:

- File: `CashManager.sol` [Line 336-350](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L336-L350)
- File: `CashManager.sol` [Line 526-528](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L526-L528)
- File: `CashManager.sol` [Line 533-535](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L533-L535)
- File: `KYCRegistry.sol` [Line 144-150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144-L150)

## 2. Using `storage` instead of `memory` for structs/arrays saves gas

When retrieving data from a `storage` location, assigning the data to a `memory` variable will cause all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

Here are some instances of this issue:

File: `CTokenCash.sol` ([Line 221](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L221), [Line 433](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L433), [Line 506](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L506), [Line 590](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L590), [Line 1027](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1027))

```solidity
221:    Exp memory exchangeRate = Exp({mantissa: exchangeRateCurrent()});
...
433:    Exp memory simpleInterestFactor = mul_(
...
506:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
...
590:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
...
1027:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
```

## 3. Splitting `require()` statements that use && saves gas

Instead of using the `&&` operator to check multiple conditions, use multiple `require()` statements. This will save approximately 3 gas per `&&`.

For example:

File: `OndoPriceOracleV2.sol` [Line 292-296](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292-L296)

```solidity
    require(
      (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );
```

In the above `require()` statement, it could be split up into two:

```solidity
    require(answeredInRound >= roundId, "Chainlink oracle price is stale");
    require(updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay, "Chainlink oracle price is stale");
```

## 4. Revert as early as possible

`require()` statements should be placed as early as possible to save gas in the event of a revert.

Consider placing the following `require()` statements as early as possible:

- File: `CashKYCSender.sol` [Line 63 - Line 66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L63-L66)
- File: `CashKYCSenderReceiver.sol` [Line 63 - Line 66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L63-L66)
- File: `Cash.sol` [Line 29 - Line 40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L29-L40)

## 5. `++i/i++` should be `unchecked` if possible to save gas

In solidity 0.8.0 and above, the `unchecked` keyword should be used when overflowing will not happen.

For example, the following instances of `++i/i++` could be `unchecked`:

- File: `CashManager.sol` [Line 750](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750)
- File: `CashManager.sol` [Line 786](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786)
- File: `CashManager.sol` [Line 933](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933)
- File: `CashManager.sol` [Line 961](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961)

## 6. Strict equalities cost less gas than non-strict

Strict equalities (`<`, `>`) cost less gas than non-strict (`<=`, `>=`). This is because strict equality uses fewer opcodes.

Here are some instances of this issue:

- File: `CashManager.sol` [Line 288-290](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L288-L290)
- File: `CashManager.sol` [Line 413](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L413)
