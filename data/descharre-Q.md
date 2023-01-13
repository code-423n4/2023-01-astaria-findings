# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Add an if check to make sure the shares are not higher than the allowance  | 1 |
|2       | Other contract addresses can only be set once| 3 |
|3       | Everyone can call initialize() function| 3 |
|4       | ecrecover() function is called directly| 1 |
|5       | Wrong comments  | 2 |

## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Don't use hashing for constants | 8 |
| 2      | Require() or revert() statement that check input arguments should be at the top of the function| 5 |
| 3      |Missing error messages in require statements| 30 |
| 4      |Useless functions| 2 |
| 5     |TokenURI returns empty string| 1 |
| 6     |Use modifier instead of duplicate require| 1 |
| 7      |Miscellaneous| 1 |


# Details
# Low Risk
## 1 Add an if check to make sure the shares are not higher than the allowance
When spending allowance make sure the amount of shares don't exceed the allowance.

[PublicVault.sol#L159-L165](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L159-L165)
```diff
    if (msg.sender != owner) {
      uint256 allowed = es.allowance[owner][msg.sender]; // Saves gas for limited approvals.
+     require(shares>= allowed, "Insufficient allowance");
      if (allowed != type(uint256).max) {
        es.allowance[owner][msg.sender] = allowed - shares;
      }
    }
```


## 2 Other contract addresses can only be set once
Other contract addresses can only be set once in the initializer. There is no other setter method for this. This makes it so there is no room for error.
- [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83)
- [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80)
- [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59)

Additionally there can be checks for zero address during initialization.
Both of these issues can lead to contract reverts and force redeployment.
## 3 Everyone can call initialize() function
When the contract is not initialized, the initialize() function can be called by anybody. This can be an issue because the owner will be set in the __initAuth function.
- [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83)
- [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80)
- [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59)
## 4 ecrecover() function is called directly
[VaultImplementation.sol#L246](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L246)

The ecrecover function is called directly, this can make it vulnerable for replay attacks. 

The solution for this is to use the OpenZeppeling ECDSA helper library. Or use a number or nonce in the data.
## 5 Wrong comments
The comment states that the following computation will underflow when the balance is not enough, however this is not true. 
It's not unchecked and when the solidity version is higher than 0.8.0 it will throw an error whenever overflow or underflow occurs.

File: [PublicVault.sol#L174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L174)
```solidity
L173     //this will underflow if not enough balance
L174     es.balanceOf[owner] -= shares;
```

File: [PublicVault.sol#L155](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L155)

No if check to ensure that the requested epoch is not in the past. Might also be just a comment that's not removed
```solidity
L155     // check to ensure that the requested epoch is not in the past
```
# Non critical
## 1 Don't use hashing for constants
Use the literal value instead because it only needs to be set one time

[CollateralToken.sol#73](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73)
```solidity
73:       uint256 private constant COLLATERAL_TOKEN_SLOT =
74:          uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
```
[LienToken.sol#L50](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L50)
```solidity
50:       uint256 private constant LIEN_SLOT =
51:          uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;

53:       bytes32 constant ACTIVE_AUCTION = bytes32("ACTIVE_AUCTION");
```
[PublicVault.sol#L53](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L53)
```solidity
53:      uint256 private constant PUBLIC_VAULT_SLOT =
54:        uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
```
[VaultImplementation.sol#L44-L57](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L44-L57)

```solidity
44:       bytes32 public constant STRATEGY_TYPEHASH =
45:        keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
46:
47:       bytes32 constant EIP_DOMAIN =
48:        keccak256(
49:          "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
50:        );
51:      bytes32 constant VERSION = keccak256("0");

57:       uint256 private constant VI_SLOT =
58:         uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
```

## 2 Require() or revert() statement that check input arguments should be at the top of the function
This way no gas is wasted when the function is destined to fail.
- [CollateralToken.sol#L564](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L564)
- [PublicVault.sol#L170](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L170)
- [CollateralToken.sol#L535](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L535) : can be put behind variable declaration of s on line 526

This can also be done for some if statements
- [CollateralToken.sol#L338-L340](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L338-L340)
- [AstariaRouter.sol#L466](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L466)

## 3 Missing error messages in require statements
Usage of error messages in require statements can help at monitoring and debugging. There is also an option to make these if statements with custom error messages

File: [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)
```solidity
341:     require(msg.sender == s.guardian);

347:     require(msg.sender == s.guardian);

354:     require(msg.sender == s.newGuardian);

361:     require(msg.sender == address(s.guardian));
```
File: [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)
```solidity
72:       require(msg.sender == address(ASTARIA_ROUTER.LIEN_TOKEN()));

199, 216, 223:     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
```

File: [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)
```solidity
266:       require(ownerOf(collateralId) == msg.sender);

535:       require(msg.sender == clearingHouse);

564:       require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
```

File: [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol)
```solidity
504:       msg.sender == address(s.COLLATERAL_TOKEN.getClearingHouse(collateralId))

860:       require(position < length);
```
File: [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)
```solidity
241, 259:     require(s.allowList[receiver]);

508:     require(msg.sender == owner());

672, 687:     require(
                currentEpoch != 0 &&
                msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
            );

680:     require(msg.sender == address(LIEN_TOKEN()));
```

File: [Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)
```solidity
65:       require(s.allowList[msg.sender] && receiver == owner());

71:       require(msg.sender == owner());;
```

File: [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol)
```solidity
78, 96, 105, 114, 147, 211:         require(msg.sender == owner()); //owner is "strategist"

191:       require(msg.sender == address(ROUTER()));

```
File: [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)
```solidity
138:       require(msg.sender == VAULT(), "only vault can mint");

231:       require(msg.sender == VAULT(), "only vault can call");
```

## 4 useless functions
[CollateralToken.sol#L206-L208](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L206-L208)

file() function is useless it's better to make the _file() function public with the modifier requiresAuth().
```solidity
function file(File calldata incoming) public requiresAuth {
    _file(incoming);
  }
```

Same for stopLiens function

[LienToken.sol#L277-L289](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L277-L289)
```solidity
  function stopLiens(
    uint256 collateralId,
    uint256 auctionWindow,
    Stack[] calldata stack,
    address liquidator
  ) external validateStack(collateralId, stack) requiresAuth {
    _stopLiens(
      _loadLienStorageSlot(),
      collateralId,
      auctionWindow,
      stack,
      liquidator
    );
  }
```

'What' isn't a professional naming and filetype makes it easier to read.
## 5 TokenURI returns empty string
[LienToken.sol#L348-L358](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L348-L358)
Might be better to have a simple image for the LienToken
```solidity
  function tokenURI(uint256 tokenId)
    public
    view
    override(ERC721, IERC721)
    returns (string memory)
  {
    if (!_exists(tokenId)) {
      revert InvalidTokenId(tokenId);
    }
    return "";
  }
```
## 6 Use modifier instead of duplicate require
require(msg.sender == owner()); is used 6 times in the VaultImplementation contract. It would be better to make this a modifier. This makes it more readable.

File: [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol)
```solidity
78, 96, 105, 114, 147, 211: require(msg.sender == owner());
```
For file [WithdrawProxy.sol#L138](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L138)
modifier [onlyVault](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L230-233) is available but not used in this function.

## Miscellaneous
### Casting to uint256 is not necessary
- [AstariaRouter.sol#L531-L541](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L531-L541)
- [PublicVault.sol#L602](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L602)

The compiler does an implicit convert itself.
```solidity
531:    return
532:      _newVault(
533:        s,
534:        underlying,
535:        uint256(0),
536:        delegate,
537:        uint256(0),
538:        true,
539        allowList,
540:        uint256(0)
541      );
```
### Redundant checking of address(0)
Lots of redundant checken in the same function, function can be made a lot shorter and more readable.

[AstariaRouter.sol#L364-L390](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L364-L390)

Potential fix:
```diff
uint256 i;
    for (; i < file.length; ) {
-     FileType what = file[i].what;
      bytes memory data = file[i].data;
+     address addr = abi.decode(data, (address));
+     if (addr == address(0)) revert InvalidFileData();
+     FileType what = file[i].what;  
      if (what == FileType.Implementation) {
-       (uint8 implType, address addr) = abi.decode(data, (uint8, address));
+       (uint8 implType) = abi.decode(data, (uint8));
-       if (addr == address(0)) revert InvalidFileData();
        s.implementations[implType] = addr;
      } else if (what == FileType.CollateralToken) {
-       address addr = abi.decode(data, (address));
-       if (addr == address(0)) revert InvalidFileData();
        s.COLLATERAL_TOKEN = ICollateralToken(addr);
      } else if (what == FileType.LienToken) {
-       address addr = abi.decode(data, (address));
-       if (addr == address(0)) revert InvalidFileData();
        s.LIEN_TOKEN = ILienToken(addr);
      } else if (what == FileType.TransferProxy) {
-       address addr = abi.decode(data, (address));
-       if (addr == address(0)) revert InvalidFileData();
        s.TRANSFER_PROXY = ITransferProxy(addr);
      } else {
        revert UnsupportedFile();
      }
      emit FileUpdated(what, data);
      unchecked {
        ++i;
      }
    }
```
### ILienToken.Lien can be reformatted to just Lien 
[ILienToken.sol#L81-L86](https://github.com/code-423n4/2023-01-astaria/blob/main/interface/src/ILienToken.sol#L81-L86)

The struct is defined in ILienToken interface so there is no point in referencing to the interface to get the Lien struct. It's also a very small gas saver: around 8 gas.
```diff
struct LienActionEncumber {
    uint256 amount;
    address receiver;
-   ILienToken.Lien lien;
+   Lien lien;
    Stack[] stack;
  }
```
### Function validateLien can be a modifier
Since function [validateLien](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L562-L567) doesn't return anything useful it can be a modifier instead.

```diff
L562
-   function validateLien(Lien memory lien) public view returns (uint256 lienId) {
+   modifier validateLien(Lien  memory lien) {
-    lienId = uint256(keccak256(abi.encode(lien)));
+   uint256 lienId = uint256(keccak256(abi.encode(lien)));
    if (!_exists(lienId)) {
      revert InvalidState(InvalidStates.INVALID_LIEN_ID);
    }
+   _;
  }
```


```diff
L742
-  function getOwed(Stack memory stack) external view returns (uint88) {
+  function getOwed(Stack  memory stack) external  view validateLien(stack.lien) returns (uint88) {
-    validateLien(stack.lien);
    return _getOwed(stack, block.timestamp);
  }

  function getOwed(Stack memory stack, uint256 timestamp)
    external
-   view
+   view validateLien(stack.lien)
    returns (uint88)
  {
-   validateLien(stack.lien);
    return _getOwed(stack, timestamp);
  }
```
For this to work you need to remove the [validateLien](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L98-L101) function in the LienToken interface.

Same thing can be done for [_exists](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L385-L387) function.

### if statement returns nothing when true
File: [PublicVault.sol#L362-L364](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L362-L364)
It's better to have a custom error message here
```solidity
    if (s.currentEpoch == uint64(0)) {
      return;
    }
```

### Rename the FileType of the structs to 'fileType' instead of 'what'
- [IAstariaRouter.sol#L49-L51](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interface/IAstariaRouter.sol#L49-L51)
- [ICollateralToken.sol#L82-L83](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interface/ICollateralToken.sol#L82-L83)
- [ILienToken.sol#L30-L31](https://github.com/code-423n4/2023-01-astaria/blob/main/interface/src/ILienToken.sol#L30-L31)
```solidity
  struct File {
    FileType what;
    bytes data;
  }
```


