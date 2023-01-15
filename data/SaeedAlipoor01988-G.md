•	In the CollateralToken contract and in function liquidatorNFTClaim, we use param to get collateralId, then we use collateralId to get the desired auction from mapping and check that this auction is exist and ended. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L117

But again, we check that the desired auction is matched with keccak256 hash value of the param. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L124

This last computing is unnecessary because we used collateralId at L117 to get matched auction from mapping. The answer from mapping is unique and there cannot be mistakes. If there is a mistake, we should first check for matched auction and then check that matched auction exists and ends.

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the ClearingHouse contract, in 3 functions we duplicated code to check that msg.sender is address(ASTARIA_ROUTER.COLLATERAL_TOKEN()). We can create a modifier and use this modifier In functions to avoid code duplication.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L223

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L216

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L199

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the CollateralToken, and function isValidOrder, we get orderHash, caller, offerer, and zoneHash as input. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L157

We used orderHash and zoneHash to validate order but we don’t use offerer and caller in the function so we can remove them from the function as input. 

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the CollateralToken, and function isValidOrderIncludingExtraData, we get orderHash, caller, order, priorOrderHashes, and criteriaResolvers as input. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L171

We used orderHash and order to validate the order but we don’t use priorOrderHashes and caller and criteriaResolvers in the function so we can remove them from the function as input. 

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the CollateralToken contract and function releaseToAddress, we check the owner of collateralId twice. By modifier and by code in the function body.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L331

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In PublicVault contract and function transferWithdrawReserve, there is a variable called currentWithdrawProxy, but the value is s.epochData[s.currentEpoch - 1].withdrawProxy; 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366

To get currentWithdrawProxy we should use s.epochData[s.currentEpoch].withdrawProxy.

And currentWithdrawProxy is defined multiple times with the same value. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L397

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L402

And we can remove s.epochData[s.currentEpoch - 1].withdrawProxy and replace it with currentWithdrawProxy.

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the PublicVault contract and function and function processEpoch, when we want to call the claim function from WithdrawProxy contract, we check that finalAuctionEnd is not equal to zero. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L297

But in WithdrawProxy and claim function, already we used this check to be sure that finalAuctionEnd is not zero. So this check is duplicated and we can remove it from the processEpoch function.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L243

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

•	In the WithdrawProxy contract and claim function we check that if (balance < s.expected), then we use (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio). we can add the unchecked block and add (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio) to it, to decrease gas usage. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L260

////////////////////////////////////////////////////////////////////////////// *** //////////////////////////////////////////////////////////////////////////////

<x> += <y> costs more gas than <x> = <x> + <y> for state variables
Using the addition operator instead of plus-equals saves 113 gas.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L512