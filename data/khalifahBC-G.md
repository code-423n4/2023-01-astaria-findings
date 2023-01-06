##Use != 0 instead of > 0 for Unsigned Integer Comparison:-

astaria/src/WithdrawProxy.sol::280 => if (balance > 0)
astaria/src/PublicVault.sol::277 => if (timeToEpochEnd() > 0) 
astaria/src/PublicVault.sol::282 => if (s.withdrawReserve > 0) 
astaria/src/PublicVault.sol::303 => if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0) 
astaria/src/PublicVault.sol::393 => s.withdrawReserve > 0 
astaria/src/PublicVault.sol::636 => if (params.remaining > 0)
astaria/src/AstariaRouter.sol::476 => if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd)



###Use immutable for OpenZeppelin AccessControl's Roles Declarations:-

astaria/src/PublicVault.sol::54 => uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
astaria/src/AstariaRouter.sol::63 => uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
astaria/src/VaultImplementation.sol::58 => uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
astaria/src/WithdrawProxy.sol::50 => uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
astaria/src/CollateralToken.sol::74 => uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
astaria/src/CollateralToken.sol::124 => s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
astaria/src/CollateralToken.sol::310 => ) != keccak256("FlashAction.onFlashAction")
astaria/src/CollateralToken.sol::519 => s.collateralIdToAuction[collateralId] = keccak256()


###Unspecific Compiler Version Pragma:-

astaria/lib/gpl/src/ERC4626Router.sol::2 => pragma solidity ^0.8.17;
astaria/lib/gpl/src/ERC4626RouterBase.sol::2 => pragma solidity ^0.8.17;

###Do not use Deprecated Library Functions:-

astaria/src/VaultImplementation.sol::334 => ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);



###Don't Initialize Variables with Default Value:-

astaria/src/ClearingHouse.sol::95 => output = new uint256[](accounts.length);
astaria/src/ClearingHouse.sol::96 => for (uint256 i; i < accounts.length; )



###Do not use Deprecated Library Functions :-

astaria/src/VaultImplementation.sol::334 => ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
astaria/src/ClearingHouse.sol::148 => ERC20(paymentToken).safeApprove

###Cache Array Length Outside of Loop:-

astaria/src/CollateralToken.sol::198 => for (; i < files.length; ) 
astaria/src/CollateralToken.sol::473 => totalOriginalConsiderationItems: considerationItems.length

###require()  statements that check input arguments should be at the top of the function:-

astaria/src/CollateralToken.sol::535 => require(msg.sender == s.clearingHouse[collateralId]);