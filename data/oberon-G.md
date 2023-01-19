### REFACTOR DUPLICATED REQUIRE() INTO A FUNCTION/MODIFIER
---
```javascript
function sGuard() private view {
    require(msg.sender == s.guardian);
}

//require(msg.sender == s.guardian);
+sGuard();
```
[AstariaRouter.sol:L341_347](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L341)
[VaultImplementation.sol:L78_96_105_114_147_211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78)
[PublicVault.sol:L241_259](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L241)
[PublicVault.sol:L673_688](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L673)
[ClearingHouse.sol:L199_216_223](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199)

---
### !&& USE MULTIPLE REQUIRE
---
```javascript
require(currentEpoch != 0);
require(msg.sender == s.epochData[currentEpoch - 1].withdrawProxy);
```
[PublicVault.sol:L672_687](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672)
[Vault.sol:L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

---
### USE ASSEMBLY HASH
---
```javascript
function assemblyHash(uint256 a, uint256 b) public view {
    assembly {
        mstore(0x00, a)
        mstore(0x20, b)
        let hashedVal := keccak256(0x00, 0x40)
    }
}
```
[VaultImplementation.sol:L45_48_51_58_154_183_247](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L45)
[AstariaRouter.sol:L63](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L63)
[ClearingHouse.sol:L41](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L41)
[PublicVault.sol:L54](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L54)
[LienToken.sol:L51_288_271_405_448_563_698](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L51)