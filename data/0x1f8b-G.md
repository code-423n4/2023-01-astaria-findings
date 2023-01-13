- [Gas](#gas)
    - [**1. Optimize ClearingHouse._execute**](#1-optimize-clearinghouse_execute)
    - [**2. Avoid compound assignment operator in state variables**](#2-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 10 = 130**](#total-gas-saved-13--10--130)

# Gas

## **1. Optimize `ClearingHouse._execute`**

```diff
  function _execute(
    address tokenContract, // collateral token sending the fake nft
    address to, // buyer
    uint256 encodedMetaData, //retrieve token address from the encoded data
    uint256 // space to encode whatever is needed,
  ) internal {
    ...
    uint256 payment = ERC20(paymentToken).balanceOf(address(this));
    ...

+   payment = ERC20(paymentToken).balanceOf(address(this));
+   if (payment > 0) {
-   if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
      ERC20(paymentToken).safeTransfer(
        ASTARIA_ROUTER.COLLATERAL_TOKEN().ownerOf(collateralId),
-       ERC20(paymentToken).balanceOf(address(this))
+       payment
      );
    }
    ...
  }
```

**Gas diff:**

In red the old values, in green the new ones:

```diff
- [PASS] testAuctionEnd() (gas: 4061002)
+ [PASS] testAuctionEnd() (gas: 4061001)
- [PASS] testLiquidationAtBoundary() (gas: 8649237)
+ [PASS] testLiquidationAtBoundary() (gas: 8648336)
- [PASS] testLiquidationNftTransfer() (gas: 4215116)
+ [PASS] testLiquidationNftTransfer() (gas: 4214396)
- [PASS] testLiquidationPaymentsOverbid() (gas: 4176825)
+ [PASS] testLiquidationPaymentsOverbid() (gas: 4176104)

- │ execute                                                    ┆ 6716            ┆ 74841 ┆ 56316  ┆ 233288 ┆ 24      │
+ │ execute                                                    ┆ 6716            ┆ 74540 ┆ 55955  ┆ 232567 ┆ 24      │
- │ fulfillAdvancedOrder                                               ┆ 115960          ┆ 150226 ┆ 159400 ┆ 175320 ┆ 3       │
+ │ fulfillAdvancedOrder                                               ┆ 115239          ┆ 149505 ┆ 158679 ┆ 174599 ┆ 3       │

```

**Affected source code:**

- [ClearingHouse.sol:163](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L163)

## **2. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint256 private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
Uint256 private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [PublicVault.sol:174](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L174)
- [PublicVault.sol:179](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L179)
- [PublicVault.sol:380](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L380)
- [PublicVault.sol:405](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L405)
- [PublicVault.sol:565](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L565)
- [PublicVault.sol:583](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L583)
- [PublicVault.sol:606](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L606)
- [PublicVault.sol:624](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L624)
- [WithdrawProxy.sol:237](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L237)
- [WithdrawProxy.sol:313](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L313)

### Total gas saved: **13 * 10 = 130**
