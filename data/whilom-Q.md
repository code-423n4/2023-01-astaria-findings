# Audit Findings 

# Low Vulerability Findings

## [L-01] `createLien()` in `LienToken.sol` should have a `whenNotPaused` modifier

Affected Code: https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L389

`requestLienPosition()` in AstariaRouter.sol is just a wrapper to `createLien()`. When the contract is paused, a privileged user that passes `requiresAuth()` can directly call `createLien()` which is not paused instead of `requestLienPosition()` to achieve the same effect. 

Recommendation: Add a check in `createLien()` to verify that contract is not paused. 

# Non-Critical Findings 

## [N-01] Astaria Router Guardian does not emit when changed

Affected code: https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AstariaRouter.sol#L339

Setting, rencouncing and accepting guardian does not emit events and thus difficult to track. 

Recommendation: Emit a ChangedGaurdian() event. 

## [N-02] Function mint does not use a predeclared modifier 

Affected Code: https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/WithdrawProxy.sol#L132

Using `onlyVault()` modifier leads to cleaner code. 

Recommendation: Replace `require(msg.sender == VAULT(), "only vault can mint");` with `onlyVault()` modifier. 

## [N-03] liquidatorNFTClaim() adds an unecessary check of `liquidator == 0`

Affected code: https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/CollateralToken.sol#L118

Code checks whether `liquidator == address(0)` however `address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);` will throw an `revert InvalidState(InvalidStates.COLLATERAL_NOT_LIQUIDATED);` if `liquidator == 0`. Thus, the check is not necessary.

## [N-04] Comment in the wrong line 

Affected Code: https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L155

Comment `// check to ensure that the requested epoch is not in the past` is in line 155 but code is in line 167. 

Recommendation: Move comment next to actual code.

## [N-05] Pause only prevents deposits and new loan positions but does not prevent liquidations or loan payments 

There can be case where OpenSea experiences a hack however due to the way the contract is setup, there is no way to pause liquidations. 

Since the liquidation engine is described clearly, the users know that this is a risk. 