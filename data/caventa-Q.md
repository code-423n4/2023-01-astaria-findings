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
whenNoPausedOrShutdown
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

We should remove the payable keyword as there is no msg.value in this function and tokenTransferFrom function. See the tokenTransferFrom function of TransferProxy.sol below

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

    +++ ASTARIA_ROUTER.LIEN_TOKEN().deleteCollateralStateHashViaClearingHouse(
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

Change the following function by remove certain lines

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

It is not recommended to encodePacked two strings as strings because string is a dynamic type variable

See the following comment at https://docs.soliditylang.org/en/v0.8.11/abi-spec.html

If you use keccak256(abi.encodePacked(a, b)) and both a and b are dynamic types, it is easy to craft collisions in the hash value by moving parts of a into b and vice-versa. More specifically, abi.encodePacked("a", "bc") == abi.encodePacked("ab", "c"). If you use abi.encodePacked for signatures, authentication or data integrity, make sure to always use the same types and check that at most one of them is dynamic. Unless there is a compelling reason, abi.encode should be preferred.

Change

abi.encodePacked to abi.encode 

10.

I modify the existing test unit of AstariaTest.sol to apply for a loan with zero Ether and repay with zero Ether and the test passed.

```
function testBasicPublicVaultLoan() public {
    TestNFT nft = new TestNFT(1);
    address tokenContract = address(nft);
    uint256 tokenId = uint256(0);

    uint256 initialBalance = WETH9.balanceOf(address(this));

    // create a PublicVault with a 14-day epoch
    address publicVault = _createPublicVault({
      strategist: strategistOne,
      delegate: strategistTwo,
      epochLength: 14 days
    });

    // lend 50 ether to the PublicVault as address(1)
    _lendToVault(
      Lender({addr: address(1), amountToLend: 50 ether}),
      publicVault
    );

    // borrow 10 eth against the dummy NFT
    (, ILienToken.Stack[] memory stack) = _commitToLien({
      vault: publicVault,
      strategist: strategistOne,
      strategistPK: strategistOnePK,
      tokenContract: tokenContract,
      tokenId: tokenId,
      lienDetails: standardLienDetails,
      --- amount: 10 ether,
      +++ amount: 0 ether,
      isFirstLien: true
    });

    uint256 collateralId = tokenContract.computeId(tokenId);

    // make sure the borrow was successful
    --- assertEq(WETH9.balanceOf(address(this)), initialBalance + 10 ether);

    vm.warp(block.timestamp + 9 days);

    --- _repay(stack, 0, 10 ether, address(this));
    +++ _repay(stack, 0, 0 ether, address(this));
  }
```

It does not make sense to borrow or repay 0 ether. I would suggest adding the zero amount checking to

_createLien and _payment internal function of LienTokens.sol

```
function _createLien(
    LienStorage storage s,
    ILienToken.LienActionEncumber memory params
  ) internal returns (uint256 newLienId, ILienToken.Stack memory newSlot) {
    if (s.collateralStateHash[params.lien.collateralId] == ACTIVE_AUCTION) {
      revert InvalidState(InvalidStates.COLLATERAL_AUCTION);
    }
    if (
      params.lien.details.liquidationInitialAsk < params.amount ||
      params.lien.details.liquidationInitialAsk == 0
    ) {
      revert InvalidState(InvalidStates.INVALID_LIQUIDATION_INITIAL_ASK);
    }

    if (params.stack.length > 0) {
      if (params.lien.collateralId != params.stack[0].lien.collateralId) {
        revert InvalidState(InvalidStates.COLLATERAL_MISMATCH);
      }

      if (params.lien.token != params.stack[0].lien.token) {
        revert InvalidState(InvalidStates.ASSET_MISMATCH);
      }
    }

    +++ require(params.amount > 0, "ZERO amount");

    newLienId = uint256(keccak256(abi.encode(params.lien)));
    Point memory point = Point({
      lienId: newLienId,
      amount: params.amount.safeCastTo88(),
      last: block.timestamp.safeCastTo40(),
      end: (block.timestamp + params.lien.details.duration).safeCastTo40()
    });
    _mint(params.receiver, newLienId);
    return (newLienId, Stack({lien: params.lien, point: point}));
  }
```

```
function _payment(
    LienStorage storage s,
    Stack[] memory activeStack,
    uint8 position,
    uint256 amount,
    address payer
  ) internal returns (Stack[] memory, uint256) {
    Stack memory stack = activeStack[position];
    uint256 lienId = stack.point.lienId;

    +++ require(amount > 0, "zero Amount");

    if (s.lienMeta[lienId].atLiquidation) {
      revert InvalidState(InvalidStates.COLLATERAL_AUCTION);
    }
    uint64 end = stack.point.end;
    // Blocking off payments for a lien that has exceeded the lien.end to prevent repayment unless the msg.sender() is the AuctionHouse
    if (block.timestamp >= end) {
      revert InvalidLoanState();
    }
    uint256 owed = _getOwed(stack, block.timestamp);
    address lienOwner = ownerOf(lienId);
    bool isPublicVault = _isPublicVault(s, lienOwner);

    address payee = _getPayee(s, lienId);

    if (amount > owed) amount = owed;
    if (isPublicVault) {
      IPublicVault(lienOwner).beforePayment(
        IPublicVault.BeforePaymentParams({
          interestOwed: owed - stack.point.amount,
          amount: stack.point.amount,
          lienSlope: calculateSlope(stack)
        })
      );
    }

    //bring the point up to block.timestamp, compute the owed
    stack.point.amount = owed.safeCastTo88();
    stack.point.last = block.timestamp.safeCastTo40();

    if (stack.point.amount > amount) {
      stack.point.amount -= amount.safeCastTo88();
      //      // slope does not need to be updated if paying off the rest, since we neutralize slope in beforePayment()
      if (isPublicVault) {
        IPublicVault(lienOwner).afterPayment(calculateSlope(stack));
      }
    } else {
      amount = stack.point.amount;
      if (isPublicVault) {
        // since the openLiens count is only positive when there are liens that haven't been paid off
        // that should be liquidated, this lien should not be counted anymore
        IPublicVault(lienOwner).decreaseEpochLienCount(
          IPublicVault(lienOwner).getLienEpoch(end)
        );
      }
      delete s.lienMeta[lienId]; //full delete of point data for the lien
      _burn(lienId);
      activeStack = _removeStackPosition(activeStack, position);
    }

    s.TRANSFER_PROXY.tokenTransferFrom(stack.lien.token, payer, payee, amount);

    emit Payment(lienId, amount);
    return (activeStack, amount);
  }
```

11.

Duplicate public vaults and private vaults can be created if they have the same

address(this),
vaultType,
msg.sender,
underlying,
block.timestamp,
epochLength,
vaultFee

See the _newVault function of AstariaRouter.sol

```
 function _newVault(
    RouterStorage storage s,
    address underlying,
    uint256 epochLength,
    address delegate,
    uint256 vaultFee,
    bool allowListEnabled,
    address[] memory allowList,
    uint256 depositCap
  ) internal returns (address vaultAddr) {
    uint8 vaultType;

    if (epochLength > uint256(0)) {
      vaultType = uint8(ImplementationType.PublicVault);
    } else {
      vaultType = uint8(ImplementationType.PrivateVault);
    }

    //immutable data
    vaultAddr = ClonesWithImmutableArgs.clone(
      s.BEACON_PROXY_IMPLEMENTATION,
      abi.encodePacked(
        address(this),
        vaultType,
        msg.sender,
        underlying,
        block.timestamp,
        epochLength,
        vaultFee
      )
    );

    //mutable data
    IVaultImplementation(vaultAddr).init(
      IVaultImplementation.InitParams({
        delegate: delegate,
        allowListEnabled: allowListEnabled,
        allowList: allowList,
        depositCap: depositCap
      })
    );

    s.vaults[vaultAddr] = true;

    emit NewVault(msg.sender, delegate, vaultAddr, vaultType);

    return vaultAddr;
  }
```

We don't want this because other loan activities can reuse the same object. And

```
vaultAddr = ClonesWithImmutableArgs.clone(
      s.BEACON_PROXY_IMPLEMENTATION,
      abi.encodePacked(
        address(this),
        vaultType,
        msg.sender,
        underlying,
        block.timestamp,
        epochLength,
        vaultFee
      )
    );
```

This code still creates different vault Address for the vault objects with the same parameter.

To fix this, add

```
 mapping(bytes => bool) vaultsSignature
```

to IAstariaRouter.sol

Then, change the _newVault function

```
function _newVault(
    RouterStorage storage s,
    address underlying,
    uint256 epochLength,
    address delegate,
    uint256 vaultFee,
    bool allowListEnabled,
    address[] memory allowList,
    uint256 depositCap
  ) internal returns (address vaultAddr) {
    uint8 vaultType;

    if (epochLength > uint256(0)) {
      vaultType = uint8(ImplementationType.PublicVault);
    } else {
      vaultType = uint8(ImplementationType.PrivateVault);
    }

    +++ require(
    +++ s.vaultsSignature[
    +++ abi.encodePacked(
    +++ address(this),
    +++ vaultType,
    +++ msg.sender,
    +++ underlying,
    +++ block.timestamp,
    +++ epochLength,
    +++ vaultFee
    +++ )
    +++ ], "Vault exists");

    //immutable data
    vaultAddr = ClonesWithImmutableArgs.clone(
      s.BEACON_PROXY_IMPLEMENTATION,
      abi.encodePacked(
        address(this),
        vaultType,
        msg.sender,
        underlying,
        block.timestamp,
        epochLength,
        vaultFee
      )
    );

    //mutable data
    IVaultImplementation(vaultAddr).init(
      IVaultImplementation.InitParams({
        delegate: delegate,
        allowListEnabled: allowListEnabled,
        allowList: allowList,
        depositCap: depositCap
      })
    );

    s.vaults[vaultAddr] = true;
    +++ s.vaultsSignature[
    +++ abi.encodePacked(
    +++ address(this),
    +++ vaultType,
    +++ msg.sender,
    +++ underlying,
    +++ block.timestamp,
    +++ epochLength,
    +++ vaultFee
    +++ )
    +++ ] = true;


    emit NewVault(msg.sender, delegate, vaultAddr, vaultType);

    return vaultAddr;
  }

```