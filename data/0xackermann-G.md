### Gas Optimizations Report
| No. | Issue |
| --- | --- |
| 1 | [Splitting `require` which contains `&&` saves gas](#G-01-splitting-require-which-contains--saves-gas)|
| 2 | [Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]](#G-02-using-uint256-instead-of-bool-in-mappings-is-more-gas-efficient-146-gas-per-instance)|
| 3 | [Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]](#G-03-use-custom-errors-rather-than-revert--require-strings-to-save-deployment-gas-68-gas-per-instance)|
| 4 | [Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#G-04-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead)|
| 5 | [Structs can be packed into fewer storage slots (20k gas)](#G-05-structs-can-be-packed-into-fewer-storage-slots-20k-gas)|
| 6 | [`require()` or `revert()` statements that check input arguments should be at the top of the function](#G-06-require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function)|
| 7 | [Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate](#G-07-multiple-address-mappings-can-be-combined-into-a-single-mapping-of-an-address-to-a-struct-where-appropriate)|
---
### [G-01] Splitting `require` which contains `&&` saves gas

---
**Description:**

Using multiple require instead of operator && can save more gas. When a require statement has 2 or more expressions with &&, separate them into individual expression results in lesser gas.

**Lines of Code:** 

- [ERC20-Cloned.sol#L143-L146](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143-L146)
- [TickMath.sol#L99-L102](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/TickMath.sol#L99-L102)
- [PublicVault.sol#L672-L675](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L672-L675)
- [PublicVault.sol#L687-L690](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L687-L690)
- [Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol#L65)

---
### [G-02] Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]

---
**Description:**

OpenZeppelin uint256 and bool comparison: 

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. The values being non-zero value makes deployment a bit more expensive.

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

**Recommendation:**

Use uint256(1) and uint256(2) instead of bool.

**Lines of Code:** 

- [ERC721.sol#L23](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L23)
- [IAstariaRouter.sol#L84](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L84)
- [ICollateralToken.sol#L60](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol#L60)
- [IVaultImplementation.sol#L49](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol#L49)

---
### [G-03] Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]

---
**Description:**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

<https://blog.soliditylang.org/2021/04/21/custom-errors/>

**Recommendation:**

Use the Custom Errors feature.

**Lines of Code:** 

- [ERC20-Cloned.sol#L115](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L115)
- [ERC20-Cloned.sol#L143-L146](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143-L146)
- [ERC4626-Cloned.sol#L25](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L25)
- [ERC4626-Cloned.sol#L27](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L27)
- [ERC4626-Cloned.sol#L43](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L43)
- [ERC4626-Cloned.sol#L94](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L94)
- [ERC721.sol#L53-L56](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L53-L56)
- [ERC721.sol#L60](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L60)
- [ERC721.sol#L90-L93](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L90-L93)
- [ERC721.sol#L113](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L113)
- [ERC721.sol#L115](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L115)
- [ERC721.sol#L117-L122](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L117-L122)
- [ERC721.sol#L155-L160](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L155-L160)
- [ERC721.sol#L171-L176](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L171-L176)
- [ERC721.sol#L200](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L200)
- [ERC721.sol#L202](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L202)
- [ERC721.sol#L219](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L219)
- [ERC721.sol#L240-L250](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L240-L250)
- [ERC721.sol#L260-L270](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L260-L270)
- [CallUtils.sol#L67](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/CallUtils.sol#L67)
- [TickMath.sol#L34](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/TickMath.sol#L34)
- [TickMath.sol#L99-L102](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/TickMath.sol#L99-L102)
- [ClearingHouse.sol#L134](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L134)
- [PublicVault.sol#L170](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L170)
- [WithdrawProxy.sol#L138](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L138)
- [WithdrawProxy.sol#L231](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L231)

---
### [G-04] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

---
**Description:**

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so.

**Recommendation:**

Use a larger size then downcast where needed

**Lines of Code:** 

- [ERC20-Cloned.sol#L111](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L111)
- [IUniswapV3PoolState.sol#L30](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L30)
- [LiquidityAmounts.sol#L11](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/LiquidityAmounts.sol#L11)
- [SafeCastLib.sol#L74](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/SafeCastLib.sol#L74)
- [SafeCastLib.sol#L77](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/SafeCastLib.sol#L77)
- [SafeCastLib.sol#L116](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/SafeCastLib.sol#L116)
- [SafeCastLib.sol#L119](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/utils/SafeCastLib.sol#L119)
- [AstariaRouter.sol#L100](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L100)
- [AstariaRouter.sol#L101](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L101)
- [AstariaRouter.sol#L102](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L102)
- [AstariaRouter.sol#L104](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L104)
- [AstariaRouter.sol#L329](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L329)
- [AstariaRouter.sol#L329](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L329)
- [AstariaRouter.sol#L368](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L368)
- [AstariaRouter.sol#L368](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L368)
- [AstariaRouter.sol#L395](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L395)
- [AstariaRouter.sol#L442](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L442)
- [AstariaRouter.sol#L442](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L442)
- [AstariaRouter.sol#L621](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L621)
- [AstariaRouter.sol#L686](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L686)
- [AstariaRouter.sol#L722](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L722)
- [AstariaRouter.sol#L725](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L725)
- [AstariaRouter.sol#L727](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L727)
- [AstariaVaultBase.sol#L32](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaVaultBase.sol#L32)
- [ClearingHouse.sol#L51](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L51)
- [CollateralToken.sol#L571](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L571)
- [LienToken.sol#L67](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L67)
- [LienToken.sol#L309](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L309)
- [LienToken.sol#L342](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L342)
- [LienToken.sol#L412](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L412)
- [LienToken.sol#L418](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L418)
- [LienToken.sol#L420](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L420)
- [LienToken.sol#L611](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L611)
- [LienToken.sol#L675](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L675)
- [LienToken.sol#L742](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L742)
- [LienToken.sol#L750](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L750)
- [LienToken.sol#L764](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L764)
- [LienToken.sol#L793](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L793)
- [LienToken.sol#L855](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L855)
- [LienToken.sol#L879](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L879)
- [PublicVault.sol#L71](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L71)
- [PublicVault.sol#L103](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L103)
- [PublicVault.sol#L224](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L224)
- [PublicVault.sol#L605](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L605)
- [VaultImplementation.sol#L315](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L315)
- [VaultImplementation.sol#L367](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L367)
- [WithdrawProxy.sol#L53](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L53)
- [WithdrawProxy.sol#L54](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L54)
- [WithdrawProxy.sol#L77](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L77)
- [WithdrawVaultBase.sol#L30](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawVaultBase.sol#L30)
- [IAstariaRouter.sol#L73](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L73)
- [IAstariaRouter.sol#L81](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L81)
- [IAstariaRouter.sol#L82](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L82)
- [IAstariaRouter.sol#L102](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L102)
- [IAstariaRouter.sol#L118](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L118)
- [IAstariaRouter.sol#L234](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L234)
- [IAstariaRouter.sol#L278](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L278)
- [IAstariaRouter.sol#L290](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L290)
- [IAstariaRouter.sol#L299](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L299)
- [IBeacon.sol#L22](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IBeacon.sol#L22)
- [IERC20Metadata.sol#L18](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC20Metadata.sol#L18)
- [ILienToken.sol#L37](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L37)
- [ILienToken.sol#L61](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L61)
- [ILienToken.sol#L70](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L70)
- [ILienToken.sol#L89](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L89)
- [ILienToken.sol#L142](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L142)
- [ILienToken.sol#L153](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L153)
- [ILienToken.sol#L225](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L225)
- [ILienToken.sol#L231](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L231)
- [ILienToken.sol#L236](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L236)
- [ILienToken.sol#L237](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L237)
- [ILienToken.sol#L296](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L296)
- [ILienToken.sol#L308](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L308)
- [ILienToken.sol#L310](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L310)
- [IPublicVault.sol#L26](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L26)
- [IPublicVault.sol#L30](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L30)
- [IPublicVault.sol#L31](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L31)
- [IPublicVault.sol#L32](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L32)
- [IPublicVault.sol#L168](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L168)
- [IPublicVault.sol#L170](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L170)
- [IRouterBase.sol#L21](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IRouterBase.sol#L21)
- [IVaultImplementation.sol#L44](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol#L44)
- [IVaultImplementation.sol#L83](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol#L83)

---
### [G-05] Structs can be packed into fewer storage slots (20k gas)

---
**Description:**

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.			

**Recommendation:**

Reorder the variables in the struct

**Lines of Code:** 

- [IAstariaRouter.sol#L101-L105](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L101-L105)

---
### [G-06] `require()` or `revert()` statements that check input arguments should be at the top of the function

---
**Description:**

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

**Lines of Code:** 
- [ERC721.sol#L113](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L113)

---
### [G-07] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

---
**Description:**

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot

**Lines of Code:** 

- [ERC20-Cloned.sol#L22-L23](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L22-L23)
- [ERC721.sol#L21-L23](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L21-L23)
- [ICollateralToken.sol#L60-L64](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol#L60-L64)

