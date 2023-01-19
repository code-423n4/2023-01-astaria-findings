##

### [GAS-1]  Instead of using operator && on single require  . Using double REQUIRE  checks can save more gas. After splitting require statement its possible save 8 gas

BEFORE MIGRATION : 

require(s.allowList[msg.sender] && receiver == owner());

Recommended Mitigation Steps

require(s.allowList[msg.sender] );
require(s.allowList[receiver == owner());

[FILE: 2023-01-astaria/src/Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

         65:  require(s.allowList[msg.sender] && receiver == owner());

[FILE : PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)

        687:  require(
        currentEpoch != 0 &&
        msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
        );

##

### [GAS-2]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

     function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    returns (uint256 assets)
    {
    require(msg.sender == VAULT(), "only vault can mint");
    _mint(receiver, shares);
    return shares;
    }

     function withdraw(
    uint256 assets,
    address receiver,
    address owner
     )
     public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyWhenNoActiveAuction
    returns (uint256 shares)
    {
    return super.withdraw(assets, receiver, owner);
     }

      function redeem(
    uint256 shares,
    address receiver,
    address owner
      )
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyWhenNoActiveAuction
    returns (uint256 assets)
     {
    return super.redeem(shares, receiver, owner);
     }

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)

      function isValidOrderIncludingExtraData(
     bytes32 orderHash,
    address caller,
    AdvancedOrder calldata order,
    bytes32[] calldata priorOrderHashes,
    CriteriaResolver[] calldata criteriaResolvers
    ) external view returns (bytes4 validOrderMagicValue) {
    CollateralStorage storage s = _loadCollateralSlot();
    return
      s.collateralIdToAuction[uint256(order.parameters.zoneHash)] == orderHash
        ? ZoneInterface.isValidOrder.selector
        : bytes4(0xffffffff);
      }

[FILE : PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)

       function _timeToSecondEndIfPublic()
    internal
    view
    override
    returns (uint256 timeToSecondEpochEnd)
    {
    return timeToEpochEnd() + EPOCH_LENGTH();
      }

      function redeemFutureEpoch(
    uint256 shares,
    address receiver,
    address owner,
    uint64 epoch
    ) public virtual returns (uint256 assets) {
    return
      _redeemFutureEpoch(_loadStorageSlot(), shares, receiver, owner, epoch);
       }

##

### [GAS-3]  USE FUNCTION INSTEAD OF MODIFIERS

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

     modifier onlyWhenNoActiveAuction() {
    WPStorage storage s = _loadSlot();
    // If auction funds have been collected to the WithdrawProxy
    // but the PublicVault hasn't claimed its share, too much money will be sent to LPs
    if (s.finalAuctionEnd != 0) {
      // if finalAuctionEnd is 0, no auctions were added
      revert InvalidState(InvalidStates.NOT_CLAIMED);
     }
     _;
    }      

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)

     modifier releaseCheck(uint256 collateralId) {
    CollateralStorage storage s = _loadCollateralSlot();

    if (s.LIEN_TOKEN.getCollateralState(collateralId) != bytes32(0)) {
      revert InvalidCollateralState(InvalidCollateralStates.ACTIVE_LIENS);
    }
    if (s.collateralIdToAuction[collateralId] != bytes32(0)) {
      revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
    }
    _;
    }

    modifier onlyOwner(uint256 collateralId) {
    require(ownerOf(collateralId) == msg.sender);
    _;
    }

     modifier whenNotPaused() {
    if (_loadCollateralSlot().ASTARIA_ROUTER.paused()) {
      revert ProtocolPaused();
     }
    _;
    }



##

### [GAS-4] Use of uint40 Can Increase Gas Cost . Uint256 uses less gas than uint40 in loops and other situations.

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations

such as uint40, are only more effective when multiple variables can be stored in the same storage space, like in structs. 

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

       314:   uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();  

##

### [GAS-5] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

       200 : Order[] memory listings = new Order[](1);






