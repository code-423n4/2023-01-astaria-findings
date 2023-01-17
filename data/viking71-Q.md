# MISSING ERROR MESSAGE IN REQUIRE STATEMENTS

## Impact 
Not user friendly

## Description
Error messages are not present in most of the functions. 

Providing error messages while a condition fails in `require` statements will help the user to know why the transaction reverted.

## Reference
This issue can also be found in previous contests
https://github.com/code-423n4/2021-12-defiprotocol-findings/issues/10 

## Code Instances
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol

## Recommendation
Add a custom error message in all `require` statements

# HAVEN'T DOCUMENTED ABOUT STATE OF NFT IF NO BIDDER IN AUCTION

## Impact
No clear idea of Liquidation of NFT collateral.

## Description
Liquidation of NFT is described. When loan length exceeds, liquidation of collateral takes place. A Dutch Auction is conducted for 72 hours for the particular NFT, but documentation never addresses about the condition where no buyer appeared to the auction and NFT goes unsold.

## Instance
https://docs.astaria.xyz/docs/protocol-mechanics/liquidations#dutch-auctions

## Recommendation 
Include documentation for this. Suggested to mention the ownership of NFT after Dutch Auction is completed without any buyer. 
