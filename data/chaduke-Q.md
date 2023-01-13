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

 