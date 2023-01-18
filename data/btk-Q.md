### Total L issues

| Number | Issues Details                                                              | Context |
|--------|-----------------------------------------------------------------------------|---------|
| [L-01] | Use `safeMint` instead of mint for ERC721                                   |  2      |
| [L-02] | ERC4626 does not work with fee-on-transfer tokens                           |  1      |
| [L-03] | ERC4626Cloned 's implmentation is not fully up to EIP-4626's specification  |  1      |
| [L-04] | Lack of `nonReentrant` modifier                                             |  2      |
| [L-05] | No storage gap for upgradeable contracts                                    |  3      |
| [L-06] | Take warnings seriously                                                     |  5      |
| [L-07] | Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists   |  5      |


### Total NC issues

| Number  | Issues Details                                                   | Context       |
|---------|------------------------------------------------------------------|---------------|
| [NC-01] | Lack of event emit                                               |  7            |
| [NC-02] | Require messages are too short and unclear                       |  24           |
| [NC-03] | Use `bytes.concat()` and `string.concat()`                       |  11           |
| [NC-04] | Lines are too long                                               |  6            |
| [NC-05] | Empty blocks should be removed or Emit something                 |  2            |
| [NC-06] | Reusable require statements should be changed to a modifier      |  6            |
| [NC-07] | Use a more recent version of OpenZeppelin dependencies           |  1            |
| [NC-08] | Critical changes should use-two step procedure                   |  1            |
| [NC-09] | Include `@return` parameters in NatSpec comments                 | All Contracts |
| [NC-10] | Function writing does not comply with the `Solidity Style Guide` | All Contracts |
| [NC-11] | The protocol should include NatSpec                              | 1             |
| [NC-12] | Avoid shadowing inherited state variables                        | 3             |


## [L-01] Use `safeMint` instead of mint for ERC721

## Impact
Users could lost their NFTs if `msg.sender` is a contract address that does not support `ERC721`, the NFT can be frozen in the contract forever.

As per the documentation of EIP-721:

> A wallet/broker/auction application MUST implement the wallet interface if it will accept safe transfers.

> Ref: https://eips.ethereum.org/EIPS/eip-721

As per the documentation of ERC721.sol by Openzeppelin:

> Ref: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol#L274-L285

### Lines of code

- [CollateralToken.sol:588](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L588)
- [LienToken.sol:455](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L455)

### Recommended Mitigation Steps 

Use [`_safeMint`](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC721.sol#L193-L202) instead of [`mint`](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC721.sol#L157-L170) to check received address support for ERC721 implementation.

```solidity
    function _safeMint(
        address to,
        uint256 tokenId,
        bytes memory data
    ) internal virtual {
        _mint(to, tokenId);
        require(
            _checkOnERC721Received(address(0), to, tokenId, data),
            "ERC721: transfer to non ERC721Receiver implementer"
        );
    }
```

## [L-02] ERC4626 does not work with fee-on-transfer tokens

### Impact
The ERC4626-Cloned.deposit/mint functions do not work well with fee-on-transfer tokens as the `assets` variable is the pre-fee amount, including the fee, whereas the `totalAssets` do not include the fee anymore.

This can be abused to mint more shares than desired.

```solidity
  function deposit(uint256 assets, address receiver)
    public
    virtual
    returns (uint256 shares)
  {
    // Check for rounding error since we round down in previewDeposit.
    require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

    require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
    // Need to transfer before minting or ERC777s could reenter.
    ERC20(asset()).safeTransferFrom(msg.sender, address(this), assets);

    _mint(receiver, shares);

    emit Deposit(msg.sender, receiver, assets, shares);

    afterDeposit(assets, shares);
  }
```

### Lines of code

- [ERC4626-Cloned.sol:19](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L19-L36)

### Recommended Mitigation Steps 
`assets` should be the amount excluding the fee (i.e the amount the contract actually received), therefore it's recommended to use the balance change before and after the transfer instead of the amount.

## [L-03] ERC4626Cloned 's implmentation is not fully up to EIP-4626's specification

Must return the maximum amount of shares mint would allow to be deposited to receiver and not cause a revert, which must not be higher than the actual maximum that would be accepted (it should underestimate if necessary).

This assumes that the user has infinite assets, i.e. must not rely on balanceOf of asset.

```solidity
  function maxDeposit(address) public view virtual returns (uint256) {
    return type(uint256).max;
  }

  function maxMint(address) public view virtual returns (uint256) {
    return type(uint256).max;
  }
```

Could cause unexpected behavior in the future due to non-compliance with EIP-4626 standard.

### Lines of code
- [ERC4626-Cloned.sol:147](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L147-L153)

### Recommended Mitigation Steps 

`maxMint()` and `maxDeposit()` should reflect the limitation of maxSupply.

## [L-04] Lack of `nonReentrant` modifier

It is a best practice to use the `nonReentrant` modifier when you make external calls to untrustable entities because even if an auditor did not think of a way to exploit it, an attacker just might.

### Lines of code

- [CollateralToken.sol:578](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L578)
- [AstariaRouter.sol:L519](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L519)

## [L-05] No storage gap for upgradeable contracts

### Impact

For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) for a description of this storage variable. 

### Lines of code

- [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)
- [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)
- [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol)

### Recommended Mitigation Steps 

Consider adding a storage gap at the end of the upgradeable contract:
```solidity
  /**
   * @dev This empty reserved space is put in place to allow future versions to add new
   * variables without shifting down storage in the inheritance chain.
   * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
   */
  uint256[50] private __gap;
```

## [L-06] Take warnings seriously

If the compiler warns you about something, you should change it. Even if you do not think that this particular warning has security implications, there might be another issue buried beneath it. Any compiler warning we issue can be silenced by slight changes to the code.

> Ref: https://docs.soliditylang.org/en/v0.8.17/security-considerations.html#take-warnings-seriously

### Lines of code

- [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)
- [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)
- [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)
- [AstariaRouter.so](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)
- [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol)

## [L-07] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists 

Solmate's `SafeTransferLib.sol`, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist.

> Ref: https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9

### Lines of code

- [AstariaRouter.sol:19](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L19)
- [ClearingHouse.sol:19](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L19)
- [CollateralToken.sol:32](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L32)
- [LienToken.sol:36](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L36)
- [PublicVault.sol:19](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L19)
- [Vault.sol:17](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L17)
- [VaultImplementation.sol:19](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L19)
- [WithdrawProxy.sol:18](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L18)

### Recommended Mitigation Steps 

Add a contract exist check in functions or use Openzeppelin `safeERC20` instead.

## [NC-01] Lack of event emit

The below methods do not emit an event when the state changes, something that it's very important for dApps and users.

### Lines of code

- [AstariaRouter.sol:352](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L352-L357)
- [PublicVault.sol:507](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L507)
- [LienToken.sol#:122](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L122)
- [LienToken.sol:233](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L233)
- [LienToken.sol:292](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L292)
- [LienToken.sol:512](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L512)
- [LienToken.sol:690](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L690)

## [NC-02] Require messages are too short and unclear

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.

### Lines of code

- [Vault.sol:65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)
- [Vault.sol:71](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L71)
- [ClearingHouse.sol:72](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L72)
- [ClearingHouse.sol:199](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199)
- [ClearingHouse.sol:216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216)
- [ClearingHouse.sol:223](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223)
- [CollateralToken.sol:266](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L266)
- [CollateralToken.sol:535](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L535)
- [CollateralToken.sol:564](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L564)
- [PublicVault.sol:241](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L241)
- [PublicVault.sol:259](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L259)
- [PublicVault.sol:508](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L508)
- [PublicVault.sol:67](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672)
- [PublicVault.sol:680](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L680)
- [PublicVault.sol:687](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687)
- [LienToken.sol:504](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L504)
- [LienToken.sol:860](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L860)
- [VaultImplementation.sol:78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78)
- [VaultImplementation.sol:105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105)
- [VaultImplementation.sol:147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147)
- [VaultImplementation.sol:191](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L191)
- [AstariaRouter.sol:361](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L361)

## [NC-03] Use `bytes.concat()` and `string.concat()`

Solidity version 0.8.4 introduces: 
- `bytes.concat()` vs `abi.encodePacked(<bytes>,<bytes>)`
- `string.concat()` vs `abi.encodePacked(<string>,<string>)`

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=bytes.concat#the-functions-bytes-concat-and-string-concat

### Lines of code

- [Vault.sol:35](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L35)
- [Vault.sol:46](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L46)
- [WithdrawProxy.sol:110](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L110)
- [WithdrawProxy.sol:124](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L124)
- [CollateralToken.sol:569](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L569)
- [PublicVault.sol:83](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L83)
- [PublicVault.sol:93](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L93)
- [PublicVault.sol:222](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L222)
- [AstariaRouter.sol:733](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L733)
- [ERC20-Cloned.sol:123](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L123)
- [VaultImplementation.sol:187](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L187)

## [NC-04] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length.

> Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

### Lines of code

- [WithdrawProxy.sol:54](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54)
- [WithdrawProxy.sol:56](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L56)
- [AstariaRouter.sol:75](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L75)
- [LienToken.sol:42](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L42)
- [VaultImplementation.sol:282](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L282)
- [VaultImplementation.sol:363](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L363)

## [NC-05] Empty blocks should be removed or Emit something

### Lines of code

- [ClearingHouse.sol:104](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104)
- [ClearingHouse.sol:180](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L180-L186)

### Recommended Mitigation Steps 
The empty blocks should do something useful, such as emitting an event or reverting.

## [NC-06] Reusable require statements should be changed to a modifier

### Lines of code 

- [VaultImplementation.sol:78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78)
- [VaultImplementation.sol:96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96)
- [VaultImplementation.sol:105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105)
- [VaultImplementation.sol:114](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114)
- [VaultImplementation.sol:147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147)
- [VaultImplementation.sol:211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211)

### Recommended Mitigation Steps 

```solidity
modifier onlyOwner() {
    require(msg.sender == owner());
    _;
}
```

## [NC-07] Use a more recent version of OpenZeppelin dependencies

For security, it is best practice to use the latest OpenZeppelin version.

```solidity
  "name": "openzeppelin-solidity",
  "description": "Secure Smart Contract library for Solidity",
  "version": "4.6.0",
```  

### Lines of code

- [package.json:4](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/6766b2de3bd0473bb7107fd8f83ef8c83c5b1fb3/package.json#L4)

### Recommended Mitigation Steps 

Old version of OpenZeppelin is used `(4.6.0)`, newer version can be used [`(4.7.3)`](https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.7.3).

## [NC-08] Critical changes should use-two step procedure

The `CollateralToken.sol` inherits the Solmate `Auth.sol` contract, which does not have a two-step procedure for critical changes.

```solidity
    function transferOwnership(address newOwner) public virtual requiresAuth {
        owner = newOwner;

        emit OwnershipTransferred(msg.sender, newOwner);
    }
```

### Lines of code

- [CollateralToken.sol:27](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L27)

### Recommended Mitigation Steps 

Consider adding two step procedure on the critical functions where the first is announcing a pending new owner and the new address should then claim its ownership.


## [NC-09] Include return parameters in NatSpec comments

If Return parameters are declared, you must prefix them with `/// @return`. Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

### Recommended Mitigation Steps

Include the `@return` argument in the NatSpec comments.

## [NC-10] Function writing does not comply with the `Solidity Style Guide`

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external / public / internal / private`
- `view / pure`

### Recommended Mitigation Steps

Follow Solidity Style Guide.

## [NC-11] The protocol should include NatSpec    

It is recommended that Solidity contracts are fully annotated using NatSpec, it is clearly stated in the Solidity official documentation.

- In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

- Some code analysis programs do analysis by reading NatSpec details, if they can't see the tags `(@param, @dev, @return)`, they do incomplete analysis.

### Lines of code

- [ClearingHouse.sol:180](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L180-L232)
- [ClearingHouse.sol:36](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L36-L102)
- [WithdrawProxy.sol:230](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L230-L318)
- [CollateralToken.sol:412](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L412-L545)
- [PublicVault.sol:611](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L611-L725)
- [PublicVault.sol:490](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L490-L568)
- [AstariaRouter.sol:273](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L273-L703)
- [LienToken.sol:265](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L265-L656)

### Recommended Mitigation Steps 

Include [`NatSpec`](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) comments in the codebase.

## [NC-12] Avoid shadowing inherited state variables         

In `PublicVault.sol` there is a local variable named `owner`, but there is a function named `owner()` in the inherited `AstariaVaultBase.sol` with the same name. This use causes compilers to issue warnings, negatively affecting checking and code readability.
```solidity
  function owner() public pure returns (address) {
    return _getArgAddress(21); //ends at 44
  }
```
### Lines of code

- [AstariaVaultBase.sol:36](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L36)
- [PublicVault.sol:120](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L120) / [PublicVault.sol:129](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L129) / [PublicVault.sol:141](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L141) / [PublicVault.sol:152](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L152)

### Recommended Mitigation Steps 

Avoid using variables with the same name.
