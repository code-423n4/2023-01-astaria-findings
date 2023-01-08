# Summary
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Use a literal instead of functions for a constant  | 1 |
| 2      | REQUIRE() OR REVERT() STATEMENTS THAT CHECK INPUT ARGUMENTS SHOULD BE AT THE TOP OF THE FUNCTION| 1 |
| 3      |Missing error messages in require statements| 1 |
| 4      |Add natspec documentation| 1 |
| 5      |Useless functions| 1 |
| 6     |Rename| 1 |
| 7     |TokenURI returns empty string| 1 |
| 3      |Miscellaneous| 1 |

# Details
## 1 Use a literal instead of functions for a constant
[CollateralToken.sol#L73](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73)
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
## 2 REQUIRE() OR REVERT() STATEMENTS THAT CHECK INPUT ARGUMENTS SHOULD BE AT THE TOP OF THE FUNCTION
[CollateralToken.sol#L564](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L564)
[PublicVault.sol#L170](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L170)

This can also be done for some if statements
[CollateralToken.sol#L338-L340](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L338-L340)
[CollateralToken.sol#L536](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L536) : can be put third place
[CollateralToken.sol#L564](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L564)
[AstariaRouter.sol#L466](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L466)

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

565:       require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
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
## 4 Add natspec documenation
Add more documentation to the functions, preferrably natspec documentation. Right now, natspec is only used at a few functions so it's hard to read the code.

## 5 useless functions
[CollateralToken.sol#L206-L208](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L206-L208)
```solidity
function file(File calldata incoming) public requiresAuth {
    _file(incoming);
  }
```
file() function is useless, it's better to make the _file() function public with the modifier requiresAuth().

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
## 6 Rename
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
'What' isn't a professional naming and filetype makes it easier to read.
## 7 TokenURI returns empty string
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
## Miscellaneous
### Casting to uint256 is not necessary
[AstariaRouter.sol#L531-L541](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L531-L541)
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
