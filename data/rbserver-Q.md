## [01] SOLMATE'S `SafeTransferLib` LIBRARY DOES NOT CHECK FOR ERC20 TOKEN CONTRACT'S EXISTENCE
Solmate's `SafeTransferLib` library is used throughout the protocol. As indicated by the following comment in this library, it does not check if the corresponding ERC20 token contract exists or not, which can be problematic. For example, for a public vault, if the asset token becomes non-existent for some reason after some amounts of it are deposited by the liquidity providers, a borrower's borrowing user action will eventually call the `VaultImplementation._requestLienAndIssuePayout` function, which further calls `ERC20(asset()).safeTransfer(receiver, payout)`. Even though the asset token does not exist anymore, calling `ERC20(asset()).safeTransfer(receiver, payout)` will not revert because Solmate's `SafeTransferLib` library is used. As a result, the borrower can succeed in sending her or his NFT as the collateral but fail to receive any asset token in return. As a mitigation, please consider using OpenZeppelin's `SafeERC20` library, instead of Solmate's, for ERC20 tokens' safe transfers because it does check for ERC20 token contract's existence.

https://github.com/rari-capital/solmate/blob/main/src/utils/SafeTransferLib.sol#L9
```solidity
/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L379-L395
```solidity
  function _requestLienAndIssuePayout(
    IAstariaRouter.Commitment calldata c,
    address receiver
  )
    internal
    returns (
      uint256 newLienId,
      ILienToken.Stack[] memory stack,
      uint256 slope,
      uint256 payout
    )
  {
    ...
    ERC20(asset()).safeTransfer(receiver, payout);
  }
```

## [02] MALICIOUS OR COMPROMISED GUARDIAN CAN STEAL USERS' FUNDS
( Please note that the following instance is not found in https://gist.github.com/Picodes/d16459938599db69f9ad88c73a2d2990#m-1-centralization-risk-for-trusted-owners. )

The guardian is allowed to call the following `AstariaRouter.fileGuardian` function for updating important components used in this protocol. If becoming malicious or compromised, the guardian can call this function to change some of these components to the malicious ones. For example, by calling this function, the guardian is able to change the `s.TRANSFER_PROXY` to a malicious contract. Afterwards, when a user starts a user action that eventually calls the `AstariaRouter.pullToken` function below, the user's funds can be transferred and lost to such malicious contract. As a mitigation, please consider making the `AstariaRouter.fileGuardian` time-delayed, such as making it only callable by a timelock contract, for giving users more time to react when malicious usage of this function is detected.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L359-L391
```solidity
  function fileGuardian(File[] calldata file) external {
    RouterStorage storage s = _loadRouterSlot();
    require(msg.sender == address(s.guardian));

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
      ...
    }
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L203-L215
```solidity
  function pullToken(
    address token,
    uint256 amount,
    address recipient
  ) public payable override {
    RouterStorage storage s = _loadRouterSlot();
    s.TRANSFER_PROXY.tokenTransferFrom(
      address(token),
      msg.sender,
      recipient,
      amount
    );
  }
```

## [03] UNSAFE `decimals()` CALL FOR ASSET TOKENS THAT DO NOT IMPLEMENT `decimals()`
For a public vault, when minting shares or depositing asset tokens by the liquidity providers, the following `PublicVault.minDepositAmount` function is eventually called, which further calls `ERC20(asset()).decimals()`. According to https://eips.ethereum.org/EIPS/eip-20,  `decimals()` is optional so it is possible that the strategist wants to create a public vault with an asset token that does not implement `decimals()`. However, when this occurs, calling the `PublicVault.minDepositAmount` function will revert, and none of such asset token can be lent to this public vault. As a mitigation, to support the usage of such asset token for a public vault, helper functions like BoringCrypto's [`safeDecimals`](https://github.com/boringcrypto/BoringSolidity/blob/ccb743d4c3363ca37491b87c6c9b24b1f5fa25dc/contracts/libraries/BoringERC20.sol#L52-L55) can be used instead of directly calling `decimals()` in the `PublicVault.minDepositAmount` function.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L96-L108
```solidity
  function minDepositAmount()
    public
    view
    virtual
    override(ERC4626Cloned)
    returns (uint256)
  {
    if (ERC20(asset()).decimals() == uint8(18)) {
      return 100 gwei;
    } else {
      return 10**(ERC20(asset()).decimals() - 1);
    }
  }
```


## [04] `_safeMint` FUNCTION CAN BE CALLED INSTEAD OF `_mint` FUNCTION FOR MINTING `CollateralToken` NFT
When the `CollateralToken.onERC721Received` function is called, `_mint(from_, collateralId)` is executed. If the `from_` input, which is the address of the owner of the collateral deposited, corresponds to a contract, calling the `_mint` function does not check if the receiving contract supports the ERC721 protocol; if not supported, the minted `CollateralToken` NFT can be locked and cannot be retrieved. To make sure that the receiving contract supports the ERC721 protocol, please consider calling the `_safeMint` function instead of the `_mint` function in the `CollateralToken.onERC721Received` function.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L553-L600
```solidity
  function onERC721Received(
    address, /* operator_ */
    address from_,
    uint256 tokenId_,
    bytes calldata // calldata data_
  ) external override whenNotPaused returns (bytes4) {
    ...
    uint256 collateralId = msg.sender.computeId(tokenId_);

    ...
    if (incomingAsset.tokenContract == address(0)) {
      ...

      _mint(from_, collateralId);

      ...
    } else {
      revert();
    }
  }
```

## [05] MULTIPLE FUNCTIONS IN `AstariaRouter` CONTRACT HAVE `payable` MODIFIER
Some functions in the `AstariaRouter` contract, such as the followings functions, have the `payable` modifier. However, the purposes of these functions do not require users to provide ETH. When users accidentally send ETH when calling these functions, the users will lose these sent ETH amounts to the `AstariaRouter` contract. To protect users from losing ETH accidentally, please consider removing the `payable` modifiers from these functions.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L123-L137
```solidity
  function mint(
    IERC4626 vault,
    address to,
    uint256 shares,
    uint256 maxAmountIn
  )
    public
    payable
    virtual
    override
    validVault(address(vault))
    returns (uint256 amountIn)
  {
    return super.mint(vault, to, shares, maxAmountIn);
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L139-L153
```solidity
  function deposit(
    IERC4626 vault,
    address to,
    uint256 amount,
    uint256 minSharesOut
  )
    public
    payable
    virtual
    override
    validVault(address(vault))
    returns (uint256 sharesOut)
  {
    return super.deposit(vault, to, amount, minSharesOut);
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L155-L169
```solidity
  function withdraw(
    IERC4626 vault,
    address to,
    uint256 amount,
    uint256 maxSharesOut
  )
    public
    payable
    virtual
    override
    validVault(address(vault))
    returns (uint256 sharesOut)
  {
    return super.withdraw(vault, to, amount, maxSharesOut);
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L171-L185
```solidity
  function redeem(
    IERC4626 vault,
    address to,
    uint256 shares,
    uint256 minAmountOut
  )
    public
    payable
    virtual
    override
    validVault(address(vault))
    returns (uint256 amountOut)
  {
    return super.redeem(vault, to, shares, minAmountOut);
  }
```

## [06] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following functions are some examples that miss the `@param` and/or `@return` comments. Please consider completing the NatSpec comments for functions like these.

```solidity
src\AstariaRouter.sol
  90: address _WITHDRAW_IMPL, 
  252: function __emergencyPause() external requiresAuth whenNotPaused { 
  259: function __emergencyUnpause() external requiresAuth whenPaused { 
  426: function validateCommitment(  
  552: ) public whenNotPaused returns (address) {  
  712: function _newVault( 
```

## [07] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The following functions are some examples that miss NatSpec comments. Please consider adding NatSpec comments for functions like these.

```solidity
lib\gpl\src\ERC721.sol
  69: function __initERC721(string memory _name, string memory _symbol) internal {  

src\AstariaRouter.sol
  217: function _loadRouterSlot() internal pure returns (RouterStorage storage rs) { 
  345: function __renounceGuardian() external {  

src\CollateralToken.sol
  80: function initialize(  
  145: function _loadCollateralSlot()  

src\TransferProxy.sol
  28: function tokenTransferFrom( 

src\VaultImplementation.sol
  190: function init(InitParams calldata params) external virtual {  
```