QA1. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L37
Wrong documentation in this line, it should ends at 41 instead of 44.
```
return _getArgAddress(21); //ends at 41
```

QA2. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L47
wrong documentation in this line, it should end with 61 instead of 64.
```
return _getArgAddress(41); //ends at 61

```

QA3. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L55
Wrong documentation, it should end with 125 instead of 116.
```
return _getArgUint256(93); //ends at 125
```

QA4. https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L129-L141
When supply == 0, both ``previewWithdraw()`` and ``previewRedeem()`` returns 10e18, it should return the input instead of a fixed valure regardless of in the input.
```
function previewMint(uint256 shares) public view virtual returns (uint256) {
    uint256 supply = totalSupply(); // Saves an extra SLOAD if totalSupply is non-zero.

    return supply == 0 ? shares : shares.mulDivUp(totalAssets(), supply);
  }

  function previewWithdraw(
    uint256 assets
  ) public view virtual returns (uint256) {
    uint256 supply = totalSupply(); // Saves an extra SLOAD if totalSupply is non-zero.

    return supply == 0 ? assets : assets.mulDivUp(supply, totalAssets());
  }


```

QA5. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AuthInitializable.sol#L41-L45
Zero address check for ``_owner`` and ``_authority`` is needed.

QA6. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AuthInitializable.sol#L95
For safety issue, transferring ownership should take two steps, first step is to propose a new pending owner, and the second step is let the new pending owner to accept the proposal and becomes the ownership, maybe using Zeppelin's claimable.sol: https://github.com/aragon/zeppelin-solidity/blob/master/contracts/ownership/Claimable.sol

QA7. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L302-L304
It is necessary to check the range of ``liquidationWithdrawRatio`` to make sure it is not greater than 1e18 (100%)

```
 function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
      if(liquidationWithdrawRatio > 1e18) revert OutOfRange(liquidationWithdrawRatio);

    _loadSlot().withdrawRatio = liquidationWithdrawRatio.safeCastTo88();
  }
```

QA8. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L462
WE need to check the ``point.end`` for ``newSlot`` as follows:
```
if (block.timestamp >= newSlot.point.end) {
        revert InvalidState(InvalidStates.EXPIRED_LIEN);
      }

```

QA9. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L70
Should include the address of the vault
```
emit NonceUpdated(address(this), s.strategistNonce);
```

QA10. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L289-L300
Zero address check for ``withdrawProxy`` is necessary to avoid losing funding. 

QA11. https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L27
Wrong check of ``minDepositAmount()``, we should check ``assets`` instead of ``shares``, the correction is:
```
require(assets > minDepositAmount(), "VALUE_TOO_SMALL");

```

QA12: https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L48
We do not need to approve allowance here since there is no need the vault to move funding from the AstariaRouter to the vault or any other account. Instead, the user should approve the  AstariaRouter contract ``shares`` of Vaulttokens allowance.

QA13: https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L66
Zero address check for ``_TRANSFER_PROXY`` is necessary.


 QA14. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L586-L588
Minor correct: exceedling cap should be ">":
```
if (v.depositCap != 0 && totalAssets() > v.depositCap) {
      revert InvalidState(InvalidStates.DEPOSIT_CAP_EXCEEDED);
}

```

QA15. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L916
Zero address check for ``newPayee`` is needed. 

QA16. Inconsistent representation of ``s.withdrawRatio`` in terms of # of decimals:
Below, ``s.withdrawRatio`` is represented as a WAD (18 decimals).
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L260
But below, ``s.withdrawRatio`` is represented as 10**ERC20(asset()).decimals(), which depends
on the particular ``asset()`` used. To make it consistent, we can always use the WAD representation
for ``s.withdrawRatio``, thus

```
transferAmount = uint256(s.withdrawRatio).mulDivDown(
        balance,
        1e18
      );
```
QA17. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L586-L588
The comparison with ``v.depositCap`` should use ``>`` instead of ``>=``.
```
if (v.depositCap != 0 && totalAssets() = v.depositCap) {
      revert InvalidState(InvalidStates.DEPOSIT_CAP_EXCEEDED);
    }
```

QA18: https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L281
Zero address check for ``liquidator`` is needed to avoid losing funding to the zero address


QA19. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L293
Need to check that ``denominator`` is not equal to zero to avoid divide by zero revert. 

QA20: https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L529-L532
Adding the address of the vault to the event: ``SlopeUpdated(newSlope)`` so that we can monitor which vault's slope has been updated. 

 QA21. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L204-L210
Both functions should return uint88 because uint88 are the datatypes of these two variables.

```

struct VaultData {
    uint88 yIntercept;
    uint48 slope;
    uint40 last;
    uint64 currentEpoch;
    uint88 withdrawReserve;
    uint88 liquidationWithdrawRatio;
    uint88 strategistUnclaimedShares;
    mapping(uint64 => EpochData) epochData;
  }

function getWithdrawReserve() public view returns (uint88) {
    return _loadStorageSlot().withdrawReserve;
  }

  function getLiquidationWithdrawRatio() public view returns (uint88) {
    return _loadStorageSlot().liquidationWithdrawRatio;
  }

```

QA22. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L69
Refactor all of the following code using function  ``ROUTER()``: 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L120
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L215
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L198
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L221

QA23. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L337-L342
Maybe we should use the maximum ``liquidationInitialAsk`` here in all the stacks, otherwise, if we chose the ``liquidationInitialAsk`` from stack[0] by default, some ``liquidationInitialAsk`` agreements for other stacks might have already been violated. 
 
QA24. G24. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L715-L725
We need to check that we will not exceed ``stack.length`` here, otherwise, it is an out-of-array-bound error:
```
uint stacklen = stack.length;
if(n > stacklen ) n = stacklen;
```

QA25. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L255-L263
The interests can only be calculated up to stack.point.end, so we need some adjustment here:
```
function _getInterest(Stack memory stack, uint256 timestamp)
    internal
    pure
    returns (uint256)
  {
    if(timestamp > stack.point.end) timestamp = stack.point.end;

    uint256 delta_t = timestamp - stack.point.last;

    return (delta_t * stack.lien.details.rate).mulWadDown(stack.point.amount);
  }
```

QA26. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L727-L740
We need adjust ``end`` according to each ``stack[i].point.end`` since we will not charge interest beyond ``stack[i].point.end``.
```
 function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
    public
    view
    validateStack(stack[0].lien.collateralId, stack)
    returns (uint256 maxPotentialDebt)
  {
    uint256 i; 
    uint256 realEnd; 
    for (; i < stack.length; ) {
      if(end > stack[i].point.end) realEnd = stack[i].point.end;
      else realEnd = end;
 
      maxPotentialDebt += _getOwed(stack[i], realEnd); // @audit: need to calculate what the real end should be
      unchecked {
        ++i;
      }
    }
  }
```


