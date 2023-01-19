1. `require()`/`revert()` statements should have descriptive reason strings
```solidity
ClearingHouse.sol:72:    require(msg.sender == address(ASTARIA_ROUTER.LIEN_TOKEN()));
ClearingHouse.sol:199:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
ClearingHouse.sol:216:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
ClearingHouse.sol:223:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
Vault.sol:65:    require(s.allowList[msg.sender] && receiver == owner());
Vault.sol:71:    require(msg.sender == owner());
AstariaRouter.sol:341:    require(msg.sender == s.guardian);
AstariaRouter.sol:347:    require(msg.sender == s.guardian);
AstariaRouter.sol:354:    require(msg.sender == s.newGuardian);
AstariaRouter.sol:361:    require(msg.sender == address(s.guardian));
CollateralToken.sol:266:    require(ownerOf(collateralId) == msg.sender);
CollateralToken.sol:535:    require(msg.sender == s.clearingHouse[collateralId]);
CollateralToken.sol:564:      require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
VaultImplementation.sol:191:    require(msg.sender == address(ROUTER()));
PublicVault.sol:241:      require(s.allowList[receiver]);
PublicVault.sol:259:      require(s.allowList[receiver]);
PublicVault.sol:680:    require(msg.sender == address(LIEN_TOKEN()));
LienToken.sol:860:    require(position < length);

