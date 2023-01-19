# Gas optimization

## G-1: Use custom errors instead of revert strings

```solidity
function getImpl(uint8 implType) external view returns (address impl) {
    impl = _loadRouterSlot().implementations[implType];
    if (impl == address(0)) {
      revert("unsupported/impl");
    }
  }
```

When impl contract is not defined, the reverting transaction would consume more gas because it’s not using custom errors;

## G-2: Use assembly to fill the output array of `balanceOfBatch` in ClearingHouse.sol

Replace 

```solidity
function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
    external
    view
    returns (uint256[] memory output)
  {
    output = new uint256[](accounts.length);
    for (uint256 i; i < accounts.length; ) {
      output[i] = type(uint256).max;
      unchecked {
        ++i;
      }
    }
  }
```

with

```solidity
function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids) public view returns (uint256[] memory output) {
    assembly {
        // Set the length of the output array
        let len := mload(accounts)
        mstore(output, len)

        // Set every bit of the output array to 1
        let i := 0
        for {
            lt(i, len)
        } {
            mstore8(add(output, add(i, 32)), 255)
            i := add(i, 32)
        }
    }
}
```

## G-3: Use `!= 0` instead of `> 0` in `_execute` of ClearingHouse.sol

The following snippet 

```solidity
if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
      ERC20(paymentToken).safeTransfer(
        ASTARIA_ROUTER.COLLATERAL_TOKEN().ownerOf(collateralId),
        ERC20(paymentToken).balanceOf(address(this))
      );
    }
```

can be replaced with

```solidity
if (ERC20(paymentToken).balanceOf(address(this)) != 0) {
      ERC20(paymentToken).safeTransfer(
        ASTARIA_ROUTER.COLLATERAL_TOKEN().ownerOf(collateralId),
        ERC20(paymentToken).balanceOf(address(this))
      );
    }
```

## G-4: Unnecessary default value in `_paymentAH` of LienToken.sol

In the following snippet in `_paymentAH()` of LienToken.sol, the variable `remaining` is unnecessarily initialized with a default value.

```solidity
uint256 remaining = 0;
if (owing > payment.safeCastTo88()) {
  remaining = owing - payment;
} else {
  payment = owing;
}
```

This can be rewritten in the following manner to reduce the gas cost.

```solidity
uint256 remaining;
if (owing > payment.safeCastTo88()) {
  remaining = owing - payment;
} else {
  payment = owing;
}
```

## G-5: Use temporary variable in `_getPayee` of LienToken.sol

```solidity
function _getPayee(
    LienStorage storage s,
    uint256 lienId
  ) internal view returns (address) {
    return
      s.lienMeta[lienId].payee != address(0)
        ? s.lienMeta[lienId].payee
        : ownerOf(lienId);
  }
```

should be rewritten in the following manner to reduce the gas cost:

```solidity
function _getPayee(LienStorage storage s, uint256 lienId) internal view returns (address) {
    address payee = s.lienMeta[lienId].payee;
    if (payee != address(0)) {
        return payee;
    } else {
        return ownerOf(lienId);
    }
}
```

## G-6: Use `!= 0` instead of `> 0` in `processEpoch` of PublicVault.sol

The following snippet 

```solidity
if (s.withdrawReserve > 0) {
      revert InvalidState(InvalidStates.WITHDRAW_RESERVE_NOT_ZERO);
}
```

can be rewritten in the following manner to reduce the gas cost.

```solidity
if (s.withdrawReserve != 0) {
      revert InvalidState(InvalidStates.WITHDRAW_RESERVE_NOT_ZERO);
}
```

## G-7: Assign `liquidationWithdrawRatio` in the else statement in `processEpoch` of PublicVault.sol

The following snippet

```solidity
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
```

should be rewritten in the following manner to save the gas cost (fewer `sstore` when currentWithdrawProxy is set)

```solidity
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
} else {
	s.liquidationWithdrawRatio = 0;
}
```

## G-8: Use `!= 0` instead of `> 0` in `_beforeCommitToLien` of PublicVault.sol

The following snippet

```solidity
if (s.withdrawReserve > uint256(0)) {
      transferWithdrawReserve();
    }
```

can be rewritten in the following manner to reduce the gas cost

```solidity
if (s.withdrawReserve != uint256(0)) {
      transferWithdrawReserve();
    }
```

## G-9: Unnecessary `mulDivDown` in `totalAssets()` of PublicVault.sol

The following snippet 

```solidity
return uint256(s.slope).mulDivDown(delta_t, 1) + uint256(s.yIntercept);
```

can be replaced with

```solidity
return uint256(s.slope) * delta_t + uint256(s.yIntercept);
```

## G-10: Use more efficient increments/decrements in PublicVault.sol

`i++` and `i--` can be rewritten as `++i` and `--i` respectively to reduce the gas cost in the following snippets in PublicVault.sol:

```solidity
s.epochData[epoch].liensOpenForEpoch--; // in _decreaseEpochLienCount
```

```solidity
s.epochData[epoch].liensOpenForEpoch++; // in _increaseOpenLiens
```

They can also be safely wrapped in `unchecked` to disable the overflow/underflow checks.

This can be applied to the following snippet as well:

```solidity
unchecked {
      s.currentEpoch++;
}  // in processEpoch
```

## G-11: Use more efficient increments/decrements in VaultImplementation.sol

`i++` and `i--` can be rewritten as `++i` and `--i` respectively to reduce the gas cost in the following snippets in VaultImplementation.sol:

```solidity
s.strategistNonce++; // in incrementNonce()
```

They can also be safely wrapped in `unchecked` to disable the overflow/underflow checks.

## G-12: Use inline statement in `settleAuction` of CollateralToken.sol

```solidity
_settleAuction(s, collateralId);
```

does not do much inside and can be replaced with the following to reduce a function call.