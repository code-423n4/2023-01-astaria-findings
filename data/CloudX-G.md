## CollateralToken.sol

source: `2023-01-astaria/src/CollateralToken.sol`

### G-1

The `_file(address) internal {}` method uses the `data` variable on line 213 defined with the `memory` type.
This `data` variable is not modified in the code, so it can be declared with the `calldata` type.

You could save an average of 547 gas per execution.

```
|                              | function name | min     | avg     | median | max    | # calls |
| ---------------------------  | ------------- | ------  | ------  | ------ | ------ | ------- |
| no optimization              | fileBatch     | 32740   | 34671   | 34671  | 36603  | 2       |
| with optimization            | fileBatch     | 32111   | 34218   | 34218  | 36325  | 2       |


```

### G-2

The `liquidatorNFTClaim(OrderParameters) external {}` method uses a read from the storage on line 117 and 124 to perform a comparison in an `if`.

We could optimize this part of the code by reading only once to create a memory variable. This memory variable can then be used to perform necessary comparisons with less gas consumption. With this modification, we can save 140 of gas per execution.

```
|                         | function name | min     | avg     | median | max    | # calls |
| ----------------------  | ------------- | ------  | ------  | ------ | ------ | ------- |
| no optimization         | liquidatorNFT claim | 45980 | 45980 | 45980 | 45980 | 1 |
| with optimization       | liquidatorNFT claim | 45840 | 45840 | 45840 | 45840 | 1 |

```
### G-3

The `liquidatorNFTClaim(OrderParameters) external {}` method, on line number 137 and 138, the variables `address tokenContract` y `uint256 tokenId` are declared 
but not used in the code. By removing these declarations, we can get an improvement of 17 units of gas per execution and 200 units of gas in deployment.


```

|                         | function name | min     | avg     | median | max    | # calls | deployment cost | deployment size |
| ----------------------  | ------------- | ------  | ------  | ------ | ------ | ------- | ---------------- | -------------- |
| no optimization    | liquidatorNFT claim | 45980   | 45980  | 45980  | 45980  | 1       |    3502698       |    17719     |
| with optimization | liquidatorNFT claim  | 45963   | 45963  | 45963  | 45963  | 1       |   3502498        |    17718     |



```

### G-4

The `releaseToAddress(uint256 collateralId, address releaseTo) public {}` method has a redundant check on line 338, as the same check is performed on line 334 through a modifier. By removing this redundant check, we can get an improvement of 348 units of gas per execution in each execution and 11408 units of gas in deployment.

source: [https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L338-L340](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L338-L340)

```
|                         | function name | min     | avg     | median | max    | # calls | deployment cost | deployment size |
| ----------------------  | ------------- | ------  | ------  | ------ | ------ | ------- | ---------------- | -------------- |
| no optimization         | releaseToAddress | 34537 | 34537 | 34537 | 34537 |     1    |     3502698   |     17719 |
| with optimization       | releaseToAddress | 34189 | 34189 | 34189 | 34189 |     1    |     3491290   |     17662 |


```

### G-5

The `function releaseToAddress(uint256 collateralId, address releaseTo) public {}` method on line number 342 the variable `address tokenContract` are declared but not used in the code. By removing these declarations, we can get an improvement of 21 units of gas per execution and 409 units of gas in deployment.

```
| function name                                 | min       | avg       | median   | max       | # calls | deployment cost | deployment size |
| -------------------------------------------- | --------: | --------: | --------: | --------: | ------: | --------------: | --------------: |
| releaseToAddress                              | 34537     | 34537     | 34537     | 34537     | 1       | 3502698         | 17719           |
| releaseToAddress                              | 34516     | 34516     | 34516     | 34516     | 1       | 3503107         | 17721           |

```

## AstariaRouter.sol

source: `2023-01-astaria/src/AstariaRouter.sol`

### G-6

The `_file(address) internal {}` method uses the `data` variable on line 280 defined with the `memory` type.
This `data` variable is not modified in the code, so it can be declared with the `calldata` type.

You could save 972 gas per execution.

```
| function name                                 | min       | avg       | median   | max       | # calls |
| -------------------------------------------- | --------: | --------: | --------: | --------: | ------: |
| fileBatch                                    | 84449     | 84449     | 84449     | 84449     | 1       |
| fileBatch                                    | 83477     | 83477     | 83477     | 83477     | 1       |
```

## LienToken.sol

source: `2023-01-astaria/src/LienToken.sol`

### G-7

The `_file(address) internal {}` method uses the `data` variable on line 84 defined with the `memory` type.
This `data` variable is not modified in the code, so it can be declared with the `calldata` type.

You could save 241 gas per execution.

```
| function name                                 | min       | avg       | median   | max       | # calls |
| -------------------------------------------- | --------: | --------: | --------: | --------: | ------: |
| file                                         | 28218     | 31193     | 31193     | 34169     | 2       |
| file                                         | 27977     | 30952     | 30952     | 33928     | 2       |
```

## ClearingHouse.sol

source: `2023-01-astaria/src/ClearingHouse.sol`

### G-8

The `_execute(address, address, uint256, uint256) internal {}` method uses the variable `payment`. We reuse and rename the variable to `balance`, with the intention of saving storage reads on lines 160 and 163.

You could save 721 gas per execution.

```
| function name                                 | min       | avg       | median   | max       | # calls |
| -------------------------------------------- | --------: | --------: | --------: | --------: | ------: |
| safeTransferFrom                             | 61408     | 100185    | 83728     | 207900    | 2       |
| safeTransferFrom                             | 60687     | 99584     | 83007     | 207179    | 2       |
```

Code example:

source: [https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L160-L165](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L160-L165)

```solidity
{
...
	balance = ERC20(paymentToken).balanceOf(address(this));
    if (balance > 0) {
      ERC20(paymentToken).safeTransfer(
        ASTARIA_ROUTER.COLLATERAL_TOKEN().ownerOf(collateralId),
        balance
      );
	 }
...
}
```

## PublicVault.sol

source: `2023-01-astaria/src/PublicVault.sol`

### G-9

Remove variable declaration to save memory expasion cost. You could avoid the `timeToEnd` variable declaration due to the code could avoid it without loosing readable experience.

You will save 23 gas units per execution and 1209 for deployment

```
| function name                                 | min       | avg       | median   | max       | # calls |
| -------------------------------------------- | --------: | --------: | --------: | --------: | ------: |
| updateVaultAfterLiquidation                   | 10589     | 10589     | 10589     | 10589     | 1       |
| updateVaultAfterLiquidation                   | 10576     | 10576     | 10576     | 10576     |         |
```

source: [https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L657](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L657)

Please take a look to refactored method in case you consider this properly:

```
function updateVaultAfterLiquidation(
    uint256 maxAuctionWindow,
    AfterLiquidationParams calldata params
  ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
    VaultData storage s = _loadStorageSlot();    _accrue(s);
    unchecked {
      _setSlope(s, s.slope - params.lienSlope.safeCastTo48());
    }    
		if (s.currentEpoch != 0) {
      transferWithdrawReserve();
    }
    uint64 lienEpoch = getLienEpoch(params.lienEnd);
    _decreaseEpochLienCount(s, lienEpoch);    
		if (timeToEpochEnd(lienEpoch) < maxAuctionWindow) {
      _deployWithdrawProxyIfNotDeployed(s, lienEpoch);
      withdrawProxyIfNearBoundary = s.epochData[lienEpoch].withdrawProxy;      WithdrawProxy(withdrawProxyIfNearBoundary).handleNewLiquidation(
        params.newAmount,
        maxAuctionWindow
      );
    }
  }
```
