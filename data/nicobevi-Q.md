# [QA] - Function state mutability can be restricted to view

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L426-L432


# [QA] - Function state mutability can be restricted to pure

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/actions/UNIV3/ClaimFees.sol#L30
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AuthInitializable.sol#L34
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L79
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L83
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L91
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L107
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L189
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/actions/UNIV3/ClaimFees.sol#L30


# [QA] - Address param could be `address(0)`

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/actions/UNIV3/ClaimFees.sol#L26


# [QA] - Use OpenZeppelin's `Base64.sol` contract instead of write your own.

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/libraries/Base64.sol


# [QA] - Add error messages or use custom errors instead

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/Vault.sol#L65
* https://github.com/code-423n4/2023-01-astaria/blob/
feb342a6666b9a97ef16d25151e020281acb1f5f/src/Vault.sol#L71
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L79
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L97
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L106
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L115
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L201
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L221
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L259
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L241
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L506
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L672
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L677
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L687
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AuthInitializable.sol#L88
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L267
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L599
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L73
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L200
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L217
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L224


# [QA] - Invalid comment on function

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L174-L179


# [QA] - Params shadowing storage variables

Function input param `owner` is shadowing the function `owner()`. The recommendation is to change the name to `_owner`.

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L120
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L129
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L141
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L152
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L714


# [QA] - `currentWithdrawProxy` is shadowing

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L395
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AstariaRouter.sol#L371

# [QA] - Unused variables

Remove any unused variable

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L262
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L343 (`tokenContract` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L140 (`stack` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L141 (`tokenContract` is not used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L142 (`tokenId` is not used)

# [QA] - Unused input param
If an input param is not being used in the function body, then it could be removed to save gas

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L411
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L573 (`uint256 shares` could be changed to `uint256`)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L174 (`caller`, `priorOrderHashes` and `criteriaResolvers` are not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L83 (`account` and `id` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L91 (`ids` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L107 (`account` and `operator` are not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L115-L120 (`tokenContract` and  `to` are not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L175 (`data` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L189-L196 (`operator_`, `from_`, `tokenId_` and `data_` are unused)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/security/V3SecurityHook.sol#L25 (`tokenContract` is not being used)
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L161 (`caller` and `offerer` are not used)
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/WithdrawProxy.sol#L143 (`assers` and `receiver` are not used)
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/WithdrawProxy.sol#L147 (`shares` are not used)

# [QA] - Constant name must be in capitalized SNAKE_CASE

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AuthInitializable.sol#L26


# [QA] - Add // solhint-disable-next-line no-inline-assembly on assembly blocks

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AuthInitializable.sol#L37
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AstariaRouter.sol#L219
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AstariaRouter.sol#L427
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L153
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L61
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/WithdrawProxy.sol#L210
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L86

# [QA] - Create an onlyGuardian modifier/private function that verifies that `msg.sender` is the guardian and throw an error otherwise
## Where
`AstariaRouter.sol`

```diff
+ error OnlyGuardianError();

+ function onlyGuardian(address guardian) private view {
+   if (msg.sender != guardian) {
+     revert OnlyGuardianError();
+   }
+ }

  function setNewGuardian(address _guardian) external {
    RouterStorage storage s = _loadRouterSlot();
-   require(msg.sender == s.guardian);
+   onlyGuardian(s.guardian);
    s.newGuardian = _guardian;
  }

  function __renounceGuardian() external {
    RouterStorage storage s = _loadRouterSlot();
-   require(msg.sender == s.guardian);
+   onlyGuardian(s.guardian);
    s.guardian = address(0);
    s.newGuardian = address(0);
  }

  function __acceptGuardian() external {
    RouterStorage storage s = _loadRouterSlot();
-   require(msg.sender == s.newGuardian);
+   onlyGuardian(s.newGuardian);
    s.guardian = s.newGuardian;
    delete s.newGuardian;
  }

  function fileGuardian(File[] calldata file) external {
    RouterStorage storage s = _loadRouterSlot();
-   require(msg.sender == address(s.guardian));
+   onlyGuardian(address(s.guardian));

    uint256 i;
    for (; i < file.length; ) {
      FileType what = file[i].what;
      bytes memory data = file[i].data;
      if (what == FileType.Implementation) {
        (uint8 implType, address addr) = abi.decode(data, (uint8, address));
        if (addr == address(0)) revert InvalidFileData();
        s.implementations[implType] = addr;
      } else if (what == FileType.CollateralToken) {
        address addr = abi.decode(data, (address));
        if (addr == address(0)) revert InvalidFileData();
        s.COLLATERAL_TOKEN = ICollateralToken(addr);
      } else if (what == FileType.LienToken) {
        address addr = abi.decode(data, (address));
        if (addr == address(0)) revert InvalidFileData();
        s.LIEN_TOKEN = ILienToken(addr);
      } else if (what == FileType.TransferProxy) {
        address addr = abi.decode(data, (address));
        if (addr == address(0)) revert InvalidFileData();
        s.TRANSFER_PROXY = ITransferProxy(addr);
      } else {
        revert UnsupportedFile();
      }
      emit FileUpdated(what, data);
      unchecked {
        ++i;
      }
    }
  }
```

# [QA] - Add visibility to storage variables

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/9bfbc09795f86d5511d58d41a2dfd26a029b2c3a/src/LienToken.sol#L53 (`ACTIVE_AUCTION` could be marked as private)


# [QA] -  Use floating pragma version for interfaces

### Where
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IAstariaRouter.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/interfaces/IAstariaVaultBase.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IBeacon.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IBeacon.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/interfaces/ICollateralToken.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC20.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC20Metadata.sol#L1
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC165.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC721.sol#L1
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC721Receiver.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC1155.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC1155Receiver.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IERC4626.sol#L4
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IFlashAction.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/ILienToken.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IPublicVault.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IRouterBase.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/ISecurityHook.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IStrategyValidator.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/ITransferProxy.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IV3PositionManager.sol#L1
* https://github.com/code-423n4/2023-01-astaria/blob/ad21252378a83001dd0051ad518953106442ca31/src/interfaces/IVaultImplementation.sol#L14
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/interfaces/IWithdrawProxy.sol#L14


# [QA] - Use fixed pragma version for contracts

## Where
* https://github.com/AstariaXYZ/astaria-gpl/blob/f430f5b8fe868d4883a8249555df76ee7752587a/src/ERC20-Cloned.sol#L2
* https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2
* https://github.com/AstariaXYZ/astaria-gpl/blob/f430f5b8fe868d4883a8249555df76ee7752587a/src/ERC4626-Cloned.sol#L2
* https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L2
* https://github.com/AstariaXYZ/astaria-gpl/blob/f430f5b8fe868d4883a8249555df76ee7752587a/src/ERC4626RouterBase.sol#L2
* https://github.com/AstariaXYZ/astaria-gpl/blob/bdd37c00f46d70e7787d05617c8d0147cd8b53ab/src/Multicall.sol#L4


# [QA] - Explicitly mark visibility of state

## Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/security/V3SecurityHook.sol#L19
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L47
* https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/VaultImplementation.sol#L51


# [QA] - Use `ROUTER()` instead of calling the low function `_getArgAddress(0)` multiple times on `ClearingHouse.sol`

### Where
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L70
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L121
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L216
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L222
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/ClearingHouse.sol#L198

```diff
- IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
+ IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(ROUTER());
```
