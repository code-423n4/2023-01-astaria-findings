1.

There are some typos across the contracts.

```
memoizing - should be memorizing
offerer - should be offerrer
calcualtion - should be calculation
```

2. 

See VaultImplentation.sol

```
modifier whenNotPaused() {
 if (ROUTER().paused()) {
   revert InvalidRequest(InvalidRequestReason.PAUSED);
 }

 if (_loadVISlot().isShutdown) {
   revert InvalidRequest(InvalidRequestReason.SHUTDOWN);
 }
 _;
}
```

Obviously, this modifier caters for shutdown as well. Therefore the modifier name should change from 

```
whenNotPaused
```

to

```
whenNoPausedAndShutdown
```

3.

See AstariaRouter.sol

```
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

We should remove the payable keyword as the function does not have msg.value in this function and tokenTransferFrom function. See the below tokenTransferFrom function of TransferProxy.sol

```
  function tokenTransferFrom(
    address token,
    address from,
    address to,
    uint256 amount
  ) external requiresAuth {
    ERC20(token).safeTransferFrom(from, to, amount);
  }
```

As this is an override function, we need to remove the payable keyword of the same function signature in its parent contract which is ERC4626Router.sol so there won't be a compilation error.

4.

See vaultImpelentation.sol

```
function modifyDepositCap(uint256 newCap) external {
 require(msg.sender == owner()); //owner is "strategist"
 _loadVISlot().depositCap = newCap.safeCastTo88();
}
```

This function Is the parent contract of PublicVault that allows strategists to modify the maximum deposit cap value at any time. However, it should consider if the total deposit amount for the current vault is greater than the deposit cap. 

I would suggest the following changes

a. Remove

```
function modifyDepositCap(uint256 newCap) external;
```

from IVaultImplementation.sol

b. Add the deleted function signature to IPublicVault.sol

c. Add

```
  function modifyDepositCap(uint256 newCap) external {
    require(msg.sender == owner()); //owner is "strategist"
    require(newCap == 0 || newCap <= totalAssets());
    _loadVISlot().depositCap = newCap.safeCastTo88();
  }
```

to PublicVault.sol

[Note: newCap can be 0 as this means there is no limit. Also, the reason why we move the modifyDepositCap function from parent contract to child contract is that the totalAssets() only can be called in PublicVault.sol. It is fine to remove the access of the function in Vault.sol because private vault does not call the same function]

5.

See settleLiquidatorNFTClaim function of ClearingHouse.sol

```
  function settleLiquidatorNFTClaim() external {
    IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));

    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
    ClearingHouseStorage storage s = _getStorage();
    ASTARIA_ROUTER.LIEN_TOKEN().payDebtViaClearingHouse(
      address(0),
      COLLATERAL_ID(),
      0,
      s.auctionStack.stack
    );
  }
```

It calls payDebtViaClearingHouse function of Astaria router. See the payDebtViaClearingHouse function of AstariaRoter.sol below

```
  function payDebtViaClearingHouse(
    address token,
    uint256 collateralId,
    uint256 payment,
    AuctionStack[] memory auctionStack
  ) external {
    LienStorage storage s = _loadLienStorageSlot();
    require(
      msg.sender == address(s.COLLATERAL_TOKEN.getClearingHouse(collateralId))
    );

    _payDebt(s, token, payment, msg.sender, auctionStack);
    delete s.collateralStateHash[collateralId];
  }
```

The first parameter token = address(0) and the third parameter payment = 0, so basically settleLiquidatorNFTClaim function call tells the code to ignore this line

```
    _payDebt(s, token, payment, msg.sender, auctionStack); 
```

The logic is not wrong but it is ugly and it costs gas too (I don't want to RESUBMIT this in Gas report.)

What we can do is create another function in LienToken.sol

```
   function deleteCollateralStateHashViaClearingHouse(
    uint256 collateralId
  ) external {
    LienStorage storage s = _loadLienStorageSlot();
    require(
      msg.sender == address(s.COLLATERAL_TOKEN.getClearingHouse(collateralId))
    );
    delete s.collateralStateHash[collateralId];
  }
```

And change the settleLiquidatorNFTClaim function

```
  function settleLiquidatorNFTClaim() external {
    IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));

    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
    ClearingHouseStorage storage s = _getStorage();
    --- ASTARIA_ROUTER.LIEN_TOKEN().payDebtViaClearingHouse(
    ---  address(0),
    ---  COLLATERAL_ID(),
    ---  0,
    ---  s.auctionStack.stack
    --- );

    +++ ASTARIA_ROUTER.LIEN_TOKEN().payDebtViaClearingHouse(
    +++  COLLATERAL_ID()
    +++);
  }
```

And also add a new function signature to ILienToken.sol

```
  function deleteCollateralStateHashViaClearingHouse(
    uint256 collateralId
  ) external;
```

6.

See PublicVault.sol

```
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

The code above handles assets with 18 decimal places differently compare to the one with lower decimal places. 
It assumes 18 decimal places is the smallest decimal place of an ERC20 token which is incorrect. 
We can have 19, 20, or any erc20 contract which is larger than 18.
Therefore, we should change the code to handle those numbers greater than 18 too.

Change

```
  function minDepositAmount()
    public
    view
    virtual
    override(ERC4626Cloned)
    returns (uint256)
  {
   ---  if (ERC20(asset()).decimals() == uint8(18)) {
   +++ if (ERC20(asset()).decimals() >= uint8(18)) {
      return 100 gwei;
    } else {
      return 10**(ERC20(asset()).decimals() - 1);
    }
  }
```

7.

See CollateralToken.sol

```
  function releaseToAddress(uint256 collateralId, address releaseTo)
    public
    releaseCheck(collateralId)
    onlyOwner(collateralId)
  {
    CollateralStorage storage s = _loadCollateralSlot();

    if (msg.sender != ownerOf(collateralId)) {
      revert InvalidSender();
    }
    Asset memory underlying = s.idToUnderlying[collateralId];
    address tokenContract = underlying.tokenContract;
    _burn(collateralId);
    delete s.idToUnderlying[collateralId];
    _releaseToAddress(s, underlying, collateralId, releaseTo);
  }
```

We should not check if the collateralId belongs to the msg.sender again as it is checked in the onlyOwner modifier (See below)

```
  modifier onlyOwner(uint256 collateralId) {
    require(ownerOf(collateralId) == msg.sender);
    _;
  }
```

Change

```
  function releaseToAddress(uint256 collateralId, address releaseTo)
    public
    releaseCheck(collateralId)
    onlyOwner(collateralId)
  {
    CollateralStorage storage s = _loadCollateralSlot();

    --- if (msg.sender != ownerOf(collateralId)) {
    ---  revert InvalidSender();
    ---}
    Asset memory underlying = s.idToUnderlying[collateralId];
    address tokenContract = underlying.tokenContract;
    _burn(collateralId);
    delete s.idToUnderlying[collateralId];
    _releaseToAddress(s, underlying, collateralId, releaseTo);
  }
```

8.

There is additional casting in the following code

```
payable(address(s.clearingHouse[collateralId]))
```

of

_generateValidOrderParameters function of CollateralToken.sol

Change

```
--- payable(address(s.clearingHouse[collateralId]))
+++ payable(s.clearingHouse[collateralId])
```

9.

See PublicVault.sol

return string(abi.encodePacked("AST-Vault-", ERC20(asset()).symbol()));
return string(abi.encodePacked("AST-V-", ERC20(asset()).symbol()));

See Vault.sol

return string(abi.encodePacked("AST-Vault-", ERC20(asset()).symbol()));
return string(abi.encodePacked("AST-V", owner(), "-", ERC20(asset()).symbol()));

It is not recommend to encodePacked two strings as string is dynamic type

See the following comment in https://docs.soliditylang.org/en/v0.8.11/abi-spec.html

If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

Change

abi.encodePacked to abi.encode 