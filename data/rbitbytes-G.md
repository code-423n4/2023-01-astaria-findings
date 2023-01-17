
Vault.sol
require() statements that use && saves gas
https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65
65 require(s.allowList[msg.sender] && receiver == owner());


ClearingHouse.sol
Using storage instead of memory for structs/arrays saves gas
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L200
200 Order[] memory listings = new Order[](1);

WithdrawProxy.sol
using 'indexed' for value types such as uint, bool, and address saves gas costs
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L42


CollateralToken.sol
Using Storage Instead Of memory Saves gas
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L432
432  OfferItem[] memory offer = new OfferItem[](1);
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L484
484 uint256[] memory prices = new uint256[](2);

PublicVault.sol
require() statements that use && saves gas
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672
672 require(currentEpoch != 0 && msg.sender == s.epochData[currentEpoch - 1].withdrawProxy);
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687
687 require(currentEpoch != 0 && msg.sender == s.epochData[currentEpoch - 1].withdrawProxy);

AstariaRouter.sol
Use calldata instead of memory for function parameters
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L719
719 address[] memory allowList,

LienToken.sol
Use calldata instead of memory for function parameters
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L562
562 function validateLien(Lien memory lien) public view returns (uint256 lienId)
