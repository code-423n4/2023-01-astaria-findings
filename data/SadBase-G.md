
## [G-01] Unused variable can reduce gas usage

### Function `deposit()`

```solidity

File:PublicVault.sol

Line 251:

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

The variable `assets` are not used and it does not mutate state variables.

Running report on the test cases show a reduction in gas usage

`export FORK_URL="https://rpc.ankr.com/eth" && forge snapshot --ffi --fork-url $FORK_URL --fork-block-number 15934974 --diff .gas-snapshot`

```bash
testBasicPrivateVaultLoan() (gas: 0 (0.000%)) 
testCollateralTokenFileSetup() (gas: 0 (0.000%)) 
testFeesExample() (gas: 0 (0.000%)) 
testIncrementNonceAsStrategistAndDelegate() (gas: 0 (0.000%)) 
testLienTokenFileSetup() (gas: 0 (0.000%)) 
testVaultShutdown() (gas: 0 (0.000%)) 
testClaimFeesAgainstV3Liquidity() (gas: 0 (0.000%)) 
testCannotExceedMinMaxPublicVaultEpochLength() (gas: 0 (0.000%)) 
testCannotRandomAccountIncrementNonce() (gas: 0 (0.000%)) 
testFailInvalidSignature() (gas: 0 (0.000%)) 
testFailSoloLendNotAppraiser() (gas: 0 (0.000%)) 
testInvalidFileData() (gas: 0 (0.000%)) 
testBlockingLiquidationsProcessEpoch() (gas: -609 (-0.010%)) 
testLiquidationNftTransfer() (gas: -608 (-0.014%)) 
testLiquidationPaymentsOverbid() (gas: -609 (-0.015%)) 
testLiquidationNearBoundaryNoWithdraws() (gas: -609 (-0.015%)) 
testLiquidationBoundaryEpochOrdering() (gas: -1218 (-0.017%)) 
testAuctionEnd() (gas: -761 (-0.019%)) 
testBuyoutLienDifferentCollateral() (gas: -761 (-0.022%)) 
testBuyoutLien() (gas: -761 (-0.024%)) 
testLiquidation5050Split() (gas: -1522 (-0.027%)) 
testFailPayLienAfterLiquidate() (gas: -761 (-0.027%)) 
testTwoLoansDiffCollateralSameStack() (gas: -761 (-0.027%)) 
testWithdrawLiquidatedNoBids() (gas: -761 (-0.027%)) 
testFinalAuctionEnd() (gas: -761 (-0.028%)) 
testAuctionEndNoBids() (gas: -761 (-0.029%)) 
testCannotBorrowMoreThanMaxPotentialDebt() (gas: -761 (-0.029%)) 
testCannotCommitToLienPotentialDebtExceedsLiquidationInitialAsk() (gas: -761 (-0.030%)) 
testPublicVaultSlopeIncDecIntegration() (gas: -761 (-0.032%)) 
testBasicPublicVaultLoan() (gas: -761 (-0.033%)) 
testMakeTwoPayments() (gas: -761 (-0.033%)) 
testCannotProcessEpochWithUnliquidatedLien() (gas: -761 (-0.034%)) 
testNewLienExceeds2XEpoch() (gas: -761 (-0.034%)) 
testFailLienDurationZero() (gas: -761 (-0.034%)) 
testFutureLiquidationWithBlockingWithdrawReserve() (gas: -1827 (-0.035%)) 
testReleaseToAddress() (gas: -761 (-0.035%)) 
testCannotLiquidationInitialAsk0() (gas: -761 (-0.037%)) 
testCannotLiquidationInitialAskExceedsAmountBorrowed() (gas: -761 (-0.037%)) 
testCannotBorrowMoreThanMaxAmount() (gas: -761 (-0.038%)) 
testCannotLienRateZero() (gas: -761 (-0.038%)) 
testZeroizedVaultNewLP() (gas: -1522 (-0.041%)) 
testLiquidationAtBoundary() (gas: -3805 (-0.044%)) 
testWithdrawProxy() (gas: -761 (-0.046%)) 
testMultipleVaultsWithLiensOnTheSameCollateral() (gas: -3805 (-0.047%)) 
testEpochProcessionMultipleActors() (gas: -3044 (-0.087%)) 
Overall gas change: -35920 (-1.042%)
```

### Function `transferWithdrawReserve()`

```solidity

File:PublicVault.sol
Line 397

function transferWithdrawReserve() public {
    VaultData storage s = _loadStorageSlot();

    if (s.currentEpoch == uint64(0)) {
      return;
    }

    address currentWithdrawProxy = s
      .epochData[s.currentEpoch - 1]
      .withdrawProxy;
    // prevents transfer to a non-existent WithdrawProxy
    // withdrawProxies are indexed by the epoch where they're deployed
    if (currentWithdrawProxy != address(0)) {
      uint256 withdrawBalance = ERC20(asset()).balanceOf(address(this));

      // prevent transfer of more assets then are available
      if (s.withdrawReserve <= withdrawBalance) {
        withdrawBalance = s.withdrawReserve;
        s.withdrawReserve = 0;
      } else {
        unchecked {
          s.withdrawReserve -= withdrawBalance.safeCastTo88();
        }
      }

      ERC20(asset()).safeTransfer(currentWithdrawProxy, withdrawBalance);
      WithdrawProxy(currentWithdrawProxy).increaseWithdrawReserveReceived(
        withdrawBalance
      );
      emit WithdrawReserveTransferred(withdrawBalance);
    }

    address withdrawProxy = s.epochData[s.currentEpoch].withdrawProxy;
    if (
      s.withdrawReserve > 0 &&
      timeToEpochEnd() == 0 &&
      withdrawProxy != address(0)
    ) {
      // address currentWithdrawProxy = s
      //   .epochData[s.currentEpoch - 1]
      //   .withdrawProxy;
      uint256 drainBalance = WithdrawProxy(withdrawProxy).drain(
        s.withdrawReserve,
        s.epochData[s.currentEpoch - 1].withdrawProxy
      );
      unchecked {
        s.withdrawReserve -= drainBalance.safeCastTo88();
      }
      WithdrawProxy(currentWithdrawProxy).increaseWithdrawReserveReceived(
        drainBalance
      );
    }
}
```

variable `currentWithdrawProxy` is re-declared even though there are no changes to the address.

Removing the re-declaration of the variable reduces gas usage.



## [G-02] Caching of state variables can reduce gas usage

```solidity
File: PublicVault.sol

function processEpoch() public {
    // check to make sure epoch is over
    if (timeToEpochEnd() > 0) {
      revert InvalidState(InvalidStates.EPOCH_NOT_OVER);
    }
    VaultData storage s = _loadStorageSlot();

    if (s.withdrawReserve > 0) {
      revert InvalidState(InvalidStates.WITHDRAW_RESERVE_NOT_ZERO);
    }

    WithdrawProxy currentWithdrawProxy = WithdrawProxy(
      s.epochData[s.currentEpoch].withdrawProxy
    );

    // split funds from previous WithdrawProxy with PublicVault if hasn't been already
    if (s.currentEpoch != 0) {
      WithdrawProxy previousWithdrawProxy = WithdrawProxy(
        s.epochData[s.currentEpoch - 1].withdrawProxy
      );
      if (
        address(previousWithdrawProxy) != address(0) &&
        previousWithdrawProxy.getFinalAuctionEnd() != 0
      ) {
        previousWithdrawProxy.claim();
      }
    }

    if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0) {
      revert InvalidState(InvalidStates.LIENS_OPEN_FOR_EPOCH_NOT_ZERO);
    }

    // reset liquidationWithdrawRatio to prepare for re calcualtion
    s.liquidationWithdrawRatio = 0;

    // check if there are LPs withdrawing this epoch
    if ((address(currentWithdrawProxy) != address(0))) {
      uint256 proxySupply = currentWithdrawProxy.totalSupply();

      s.liquidationWithdrawRatio = proxySupply
        .mulDivDown(1e18, totalSupply())
        .safeCastTo88();

      currentWithdrawProxy.setWithdrawRatio(s.liquidationWithdrawRatio);
      uint256 expected = currentWithdrawProxy.getExpected();

      unchecked {
        if (totalAssets() > expected) {
          s.withdrawReserve = (totalAssets() - expected)
            .mulWadDown(s.liquidationWithdrawRatio)
            .safeCastTo88();
        } else {
          s.withdrawReserve = 0;
        }
      }
      _setYIntercept(
        s,
        s.yIntercept -
          totalAssets().mulDivDown(s.liquidationWithdrawRatio, 1e18)
      );
      // burn the tokens of the LPs withdrawing
      _burn(address(this), proxySupply);
    }

    // increment epoch
    unchecked {
      s.currentEpoch++;
    }
  }
```

`totalAssets()` can be cached and memoized to reduce gas usage

```solidity

uint256 assets = totalAssets();

unchecked {
	if (assets > expected) {
	  s.withdrawReserve = (assets - expected)
		.mulWadDown(s.liquidationWithdrawRatio)
		.safeCastTo88();
	} else {
	  s.withdrawReserve = 0;
	}
  }
  _setYIntercept(
	s,
	s.yIntercept -
	  assets.mulDivDown(s.liquidationWithdrawRatio, 1e18)
  );
  // burn the tokens of the LPs withdrawing
  _burn(address(this), proxySupply);
}
```

Running `export FORK_URL="https://rpc.ankr.com/eth" && forge snapshot --ffi --fork-url $FORK_URL --fork-block-number 15934974 --diff .gas-snapshot`:

```bash
testAuctionEnd() (gas: 0 (0.000%)) 
testAuctionEndNoBids() (gas: 0 (0.000%)) 
testBasicPrivateVaultLoan() (gas: 0 (0.000%)) 
testBasicPublicVaultLoan() (gas: 0 (0.000%)) 
testBuyoutLienDifferentCollateral() (gas: 0 (0.000%)) 
testCollateralTokenFileSetup() (gas: 0 (0.000%)) 
testFeesExample() (gas: 0 (0.000%)) 
testFinalAuctionEnd() (gas: 0 (0.000%)) 
testIncrementNonceAsStrategistAndDelegate() (gas: 0 (0.000%)) 
testLienTokenFileSetup() (gas: 0 (0.000%)) 
testLiquidationNftTransfer() (gas: 0 (0.000%)) 
testMakeTwoPayments() (gas: 0 (0.000%)) 
testNewLienExceeds2XEpoch() (gas: 0 (0.000%)) 
testReleaseToAddress() (gas: 0 (0.000%)) 
testTwoLoansDiffCollateralSameStack() (gas: 0 (0.000%)) 
testVaultShutdown() (gas: 0 (0.000%)) 
testClaimFeesAgainstV3Liquidity() (gas: 0 (0.000%)) 
testPublicVaultSlopeIncDecIntegration() (gas: 0 (0.000%)) 
testCannotBorrowMoreThanMaxAmount() (gas: 0 (0.000%)) 
testCannotBorrowMoreThanMaxPotentialDebt() (gas: 0 (0.000%)) 
testCannotCommitToLienPotentialDebtExceedsLiquidationInitialAsk() (gas: 0 (0.000%)) 
testCannotExceedMinMaxPublicVaultEpochLength() (gas: 0 (0.000%)) 
testCannotLienRateZero() (gas: 0 (0.000%)) 
testCannotLiquidationInitialAsk0() (gas: 0 (0.000%)) 
testCannotLiquidationInitialAskExceedsAmountBorrowed() (gas: 0 (0.000%)) 
testCannotProcessEpochWithUnliquidatedLien() (gas: 0 (0.000%)) 
testCannotRandomAccountIncrementNonce() (gas: 0 (0.000%)) 
testFailInvalidSignature() (gas: 0 (0.000%)) 
testFailLienDurationZero() (gas: 0 (0.000%)) 
testFailPayLienAfterLiquidate() (gas: 0 (0.000%)) 
testFailSoloLendNotAppraiser() (gas: 0 (0.000%)) 
testInvalidFileData() (gas: 0 (0.000%)) 
testLiquidationPaymentsOverbid() (gas: -590 (-0.014%)) 
testLiquidationAtBoundary() (gas: -1486 (-0.017%)) 
testBlockingLiquidationsProcessEpoch() (gas: -1189 (-0.020%)) 
testLiquidationNearBoundaryNoWithdraws() (gas: -1189 (-0.029%)) 
testLiquidationBoundaryEpochOrdering() (gas: -2378 (-0.033%)) 
testFutureLiquidationWithBlockingWithdrawReserve() (gas: -1779 (-0.034%)) 
testEpochProcessionMultipleActors() (gas: -1486 (-0.043%)) 
testBuyoutLien() (gas: -1486 (-0.047%)) 
testLiquidation5050Split() (gas: -2972 (-0.052%)) 
testWithdrawLiquidatedNoBids() (gas: -1486 (-0.053%)) 
testMultipleVaultsWithLiensOnTheSameCollateral() (gas: -5944 (-0.073%)) 
testZeroizedVaultNewLP() (gas: -2972 (-0.080%)) 
testWithdrawProxy() (gas: -1486 (-0.089%)) 
Overall gas change: -26443 (-0.585%)
```

## [G-03] Redundant Conditional Checks

```solidity
File: CollateralToken.sol

function releaseToAddress(uint256 collateralId, address releaseTo)
    public
    releaseCheck(collateralId)
    onlyOwner(collateralId)
{
    CollateralStorage storage s = _loadCollateralSlot();

    if (msg.sender != ownerOf(collateralId)) {
      revert InvalidSender();
    }
    Asset memory underlying = s.idToUnderlying[collateralId];
    address tokenContract = underlying.tokenContract;
    _burn(collateralId);
    delete s.idToUnderlying[collateralId];
    _releaseToAddress(s, underlying, collateralId, releaseTo);
}
```

The statement of checking `msg.sender` is redundant with the `onlyOwner` modifier as they are essentially checking for the same condition.

Removing the conditional statement does not affect the checks but it will reduce gas usage.

```solidity
testReleaseToAddress() (gas: -429 (-0.020%))
```
