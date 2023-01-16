### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove the `initializer` modifier | 3 |
| [G-02] |Use hardcode address instead ``address(this)``| 26 |
| [G-03] |Duplicated require()/if() checks should be refactored to a modifier or function |13 |
| [G-04] |Structs can be packed into fewer storage slots  | 1 |
| [G-05] | State variable can be used instead of struct |1|
| [G-06] | Using delete instead of setting `info` struct to 0 saves gas | 5 |
| [G-07] |Gas optimization by changing the emit order | 2 |
| [G-08] | Empty blocks should be removed or emit something | 7 |
| [G-09] | Using `storage` instead of `memory` for `structs/arrays` saves gas  | 15 |
| [G-10] |] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement | 2 |
| [G-11] | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead  |7 |
| [G-12] |Setting the _constructor_ to `payable`  | 4 |
| [G-13] |Functions guaranteed to revert_ when callled by normal users can be marked `payable`   | 8 |
| [G-14] |Use a more recent version of solidity | 7 |
| [G-15] |Use ``double require`` instead of using ``&&``  | 4 |
| [G-16] |Use nested if and, avoid multiple check combinations |8 |
| [G-17] |Sort Solidity operations using short-circuit mode  | 11 |
| [G-18] |Optimize names to save gas |All contracts |
| [G-19] |Upgrade Solidity's optimizer  |  |
| [G-20] |``>=`` costs less gas than ``>`` | 1 |

Total 20 issues

### [G-01] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

3 results - 3 files:
```solidity
src/AstariaRouter.sol:
  83:   function initialize(
  93   ) external initializer {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83

```solidity
src/CollateralToken.sol:
  80:   function initialize(
  85   ) public initializer {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80


```solidity
src/LienToken.sol:
  59:   function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
  61     initializer
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59


In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:


```diff
src/LienToken.sol#L59
  59:    function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
- 61     initializer                                                        
+             require(address(this).code.length == 0, 'not in constructor');
```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!


### [G-02] Use hardcode address instead ``address(this)``

Instead of ``address(this)``, it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's ``script.sol`` and solmate````LibRlp.sol`` contracts can do this.

Reference:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824


26 results - 7 files:
```solidity
lib\gpl\src\ERC20-Cloned.sol: 
  158    function computeDomainSeparator() internal view virtual returns (bytes32) {
  167:       address(this)
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L167


```solidity
lib\gpl\src\ERC4626-Cloned.sol:
  29:    ERC20(asset()).safeTransferFrom(msg.sender, address(this), assets);
  45:    ERC20(asset()).safeTransferFrom(msg.sender, address(this), assets);
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L29

```solidity
lib\gpl\src\ERC4626Router.sol:
  30:    pullToken(vault.asset(), amount, address(this));
  44:    pullToken(address(asset), amount, address(this));
  57:    pullToken(address(vault), amountShares, address(this));
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L30

```solidity
src\AstariaRouter.sol:
  734:   address(this),
  784:   address(this)
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L734


```solidity
src\ClearingHouse.sol:
  132:   uint256 payment = ERC20(paymentToken).balanceOf(address(this));
    160:   if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
  163:   ERC20(paymentToken).balanceOf(address(this))
  217:   ERC721(tokenContract).safeTransferFrom(address(this), target, tokenId);
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L132


```solidity
src\CollateralToken.sol:
   93:   bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
   97:   s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(CONDUIT_KEY, address(this));
  228:   s.CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
  237:   address(this)
  464:   zone: address(this),
  512:   if (listingOrder.parameters.zone != address(this)) {
  564:   require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
  579:   address(this),
  584:   if (msg.sender == address(this) || msg.sender == address(s.LIEN_TOKEN)) {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L93

```solidity
src\PublicVault.sol:
  179:   es.balanceOf[address(this)] += shares;
  182:   emit Transfer(owner, address(this), shares);
  226:   address(this), // vault
  336:   _burn(address(this), proxySupply);
  372:   uint256 withdrawBalance = ERC20(asset()).balanceOf(address(this));
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L179

### [G-03] Duplicated require()/if() checks should be refactored to a modifier or function

13 results 5 files:
```solidity
src/AstariaRouter.sol:
  341:    require(msg.sender == s.guardian);
  347:    require(msg.sender == s.guardian);
```
[AstariaRouter.sol#L341](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L341), [AstariaRouter.sol#L347](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L347)


```solidity
src/ClearingHouse.sol:
  199:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  216:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  223:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
```
[ClearingHouse.sol#L199](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199), [ClearingHouse.sol#L216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216), [ClearingHouse.sol#L223](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223)


```solidity
src/PublicVault.sol:
  508:    require(msg.sender == owner()); //owner is "strategist»
```
[PublicVault.sol#L508](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L508)


```solidity
src/Vault.sol:
  71:     require(msg.sender == owner());
```
[Vault.sol#L71](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L71)


```solidity
src/VaultImplementation.sol:
   78:    require(msg.sender == owner()); //owner is "strategist"
   96:    require(msg.sender == owner()); //owner is "strategist"
  105:    require(msg.sender == owner()); //owner is "strategist"
  114:    require(msg.sender == owner()); //owner is "strategist"
  147:    require(msg.sender == owner()); //owner is "strategist"
  211:    require(msg.sender == owner()); //owner is "strategist»
```
[VaultImplementation.sol#L78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78), [VaultImplementation.sol#L96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96), [VaultImplementation.sol#L105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105), [VaultImplementation.sol#L114](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114), [VaultImplementation.sol#L147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147), [VaultImplementation.sol#L211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211)


**Recommendation:**

You can consider adding a modifier like below.

```solidity
 modifer checkOwner () {
        require(require(msg.sender == owner());
        _;
 }
```

Here are the data available in the covered contracts. The use of this situation in contracts that are not covered will also provide gas optimization.


### [G-04] Structs can be packed into fewer storage slots

The `StrategyDetailsParam` struct can be packaged as suggested below.
```solidity  
src\interfaces\IAstariaRouter.sol:
  101   struct StrategyDetailsParam {
  102     uint8 version;        // slot0
  103     uint256 deadline;     // slot1
  104     address vault;        // slot2
  105   }
```
``1 slot`` can be ``saved`` here
```diff
src\interfaces\IAstariaRouter.sol:
  101   struct StrategyDetailsParam {
- 102     uint8 version;                
  103     uint256 deadline;     // slot0
+ 102     uint8 version;        // slot1
  104     address vault;        // slot1
  105   }
```

### [G-05] State variable can be used instead of struct

Using the 'uint256 remainder' state variable instead of the 'LiquidationPaymentParams' structure saves gas.

```solidity  
src\interfaces\IPublicVault.sol:
  54:   struct LiquidationPaymentParams {
  55     uint256 remaining;
  56   }
```

### [G-06] Using delete instead of setting `info` struct to 0 saves gas

5 results - 2 files
```solidity
src/PublicVault.sol:
  308:    s.liquidationWithdrawRatio = 0;
  327:    s.withdrawReserve = 0;
  377:    s.withdrawReserve = 0;
  511:    s.strategistUnclaimedShares = 0;
```
[PublicVault.sol#L308](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L308), [PublicVault.sol#L327](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L327), [PublicVault.sol#L377](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L377), [PublicVault.sol#L511](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L511)


```solidity
src/WithdrawProxy.sol:
  284:    s.finalAuctionEnd = 0;
```
[WithdrawProxy.sol#L284](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L284)


### [G-07] Gas optimization by changing the emit order

2 results - 1 file:

Considering the risk of reentrancy, moving 'Emit' to the end of the transaction provides gas optimization to avoid cost in case of ‘revert' of the transaction.

**lib\gpl\src\ERC4626-Cloned.sol#L54-L77**
```solidity
lib\gpl\src\ERC4626-Cloned.sol:
  54    function withdraw(
  70:       beforeWithdraw(assets, shares);
  72:       _burn(owner, shares);
  74:       emit Withdraw(msg.sender, receiver, owner, assets, shares);
  76:       ERC20(asset()).safeTransfer(receiver, assets);
  77:   }
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L74

```diff
lib\gpl\src\ERC4626-Cloned.sol:
  54    function withdraw(
  70:       beforeWithdraw(assets, shares);
  72:       _burn(owner, shares);
- 74:       emit Withdraw(msg.sender, receiver, owner, assets, shares);
  76:       ERC20(asset()).safeTransfer(receiver, assets);
+ 74:       emit Withdraw(msg.sender, receiver, owner, assets, shares);
  77:   }
```

**lib/gpl/src/ERC4626-Cloned.sol#L79-L103**
```solidity
lib/gpl/src/ERC4626-Cloned.sol:
   79    function redeem(
   96       beforeWithdraw(assets, shares);
   98       _burn(owner, shares);
  100:      emit Withdraw(msg.sender, receiver, owner, assets, shares);
  102       ERC20(asset()).safeTransfer(receiver, assets);
  103    }
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L100

```diff
lib/gpl/src/ERC4626-Cloned.sol:
   79    function redeem(
   96       beforeWithdraw(assets, shares);
   98       _burn(owner, shares);
- 100:      emit Withdraw(msg.sender, receiver, owner, assets, shares);
  102       ERC20(asset()).safeTransfer(receiver, assets);
+ 100       emit Withdraw(msg.sender, receiver, owner, assets, shares);
  103   }
```

### [G-08] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

7 results - 4 files:
```solidity
lib/gpl/src/ERC4626-Cloned.sol:
  165:   function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
 
  167:   function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```
[ERC4626-Cloned.sol#L165](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L165), [ERC4626-Cloned.sol#L167](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L167)

```solidity
src/BeaconProxy.sol:
  87:   function _beforeFallback() internal virtual {}
```
[BeaconProxy.sol#L87](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L87)

```solidity
src/ClearingHouse.sol:
  104:   function setApprovalForAll(address operator, bool approved) external {}

  180   function safeBatchTransferFrom(
  186:   ) public {}
```
[ClearingHouse.sol#L104](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104), [ClearingHouse.sol#L186](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L186)

```solidity
src/VaultImplementation.sol:
  268   function _afterCommitToLien(
  272:   ) internal virtual {}

  274   function _beforeCommitToLien(IAstariaRouter.Commitment calldata)
  277:   {}
```
[VaultImplementation.sol#L272](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L277), [VaultImplementation.sol#L272](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L277)


### [G-09] Using `storage` instead of `memory` for `structs/arrays` saves gas

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

15 results - 4 files:
```solidity
src/AstariaRouter.sol:
  527:    address[] memory allowList = new address[](1);
```
[AstariaRouter.sol#L527](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L527)


```solidity
src/ClearingHouse.sol:
  200:    Order[] memory listings = new Order[](1);
```
[ClearingHouse.sol#L200](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L200)


```solidity
src/CollateralToken.sol:
  137:    Asset memory underlying = s.idToUnderlying[collateralId];
  214:    bytes memory data = incoming.data;
  341:    Asset memory underlying = s.idToUnderlying[collateralId];
  390:    Asset memory underlying = _loadCollateralSlot().idToUnderlying[
  432:    OfferItem[] memory offer = new OfferItem[](1);
  434:    Asset memory underlying = s.idToUnderlying[collateralId];
  444:    ConsiderationItem[] memory considerationItems = new ConsiderationItem[](2);
  484:    uint256[] memory prices = new uint256[](2);
  562:    Asset memory incomingAsset = s.idToUnderlying[collateralId];
```
[CollateralToken.sol#L137](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L137), [CollateralToken.sol#L214](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L214), [CollateralToken.sol#L341](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L341), [CollateralToken.sol#L390](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L390), [CollateralToken.sol#L432](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L432), [CollateralToken.sol#L434](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L434), [CollateralToken.sol#L444](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L444), [CollateralToken.sol#L484](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L484), [CollateralToken.sol#L562](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L562)


```solidity
src/LienToken.sol:
   84:    bytes memory data = incoming.data;
  299:    AuctionData memory auctionData;
  305:    AuctionStack memory auctionStack;
  401:    Stack memory newStackSlot;
```
[LienToken.sol#L84](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L84), [LienToken.sol#L299](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L299), [LienToken.sol#L305](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L305), [LienToken.sol#L401](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L401)


### [G-10] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement

```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.

2 results - 1 file:

```solidity
src/LienToken.sol:
  623   function _paymentAH(
  628     uint256 payment,
  633     uint256 owing = stack[position].amountOwed;
  637:     if (owing > payment.safeCastTo88()) {
  638       remaining = owing - payment;
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L637


```solidity
src/LienToken.sol:
  790   function _payment(
  829:     if (stack.point.amount > amount) {
  830       stack.point.amount -= amount.safeCastTo88();
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L829


### [G-11] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contracts gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 

Use a larger size then downcast where needed.

7 results - 2 files:
```solidity
src\AstariaRouter.sol:

 ///@audit uint8 nlrType
  442:    uint8 nlrType = uint8(_sliceUint(commitment.lienRequest.nlrDetails, 0));

  //@audit uint8 vaultType
  725:    vaultType = uint8(ImplementationType.PublicVault);

  727:    vaultType = uint8(ImplementationType.PrivateVault);
```
[AstariaRouter.sol#L442](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L442), [AstariaRouter.sol#L725](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L725), 
[AstariaRouter.sol#L727](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L727)


```solidity
src\PublicVault.sol:

  ///@audit uint64 epoch
  227:    epoch + 1 // claimable epoch

  ///@audit uint40 lienEnd
  453:    uint64 epoch = getLienEpoch(lienEnd);

  /// @audit uint88 feeInShares
  606:    s.strategistUnclaimedShares += feeInShares;

 ///@audit uint64 lienEpoch
  657:    uint256 timeToEnd = timeToEpochEnd(lienEpoch);
```
[PublicVault.sol#L227](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L227), [PublicVault.sol#L453](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L453), [PublicVault.sol#L606](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606), [PublicVault.sol#L657](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L657)

### [G-12] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

4 results - 4 files
```solidity
src/AstariaRouter.sol:
  70:   constructor() {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L70

```solidity
src/CollateralToken.sol:
  76:   constructor() {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L76

```solidity
src/LienToken.sol:
  55:   constructor() {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L55

```solidity
src/TransferProxy.sol:
  24:   constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY) {
```
https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L24


**Recommendation:**
Set the constructor to ```payable```

### [G-13]  Functions guaranteed to revert_ when callled by normal users can be marked `payable` 

**_Note: Automated findings of this gas optimization by Code4rena were found. (https://gist.github.com/Picodes/d16459938599db69f9ad88c73a2d2990#gas-7-functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable). However, when the codes within the scope were examined, it was noticed that the results below were not found in the Automated findings file. For this reason, eight results within the scope are reported._**


If a function modifier or require such as onlyOwner-admin is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2) which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

8 result - 3 files:
```solidity
src/CollateralToken.sol:
  270   function flashAction(
  274:   ) external onlyOwner(collateralId) {

  331   function releaseToAddress(uint256 collateralId, address releaseTo)
  334:     onlyOwner(collateralId)
```
[CollateralToken.sol#L274](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L274), [CollateralToken.sol#L333-L334](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L333-L334)


```solidity
src/PublicVault.sol:
  515   function beforePayment(BeforePaymentParams calldata params)
  517:     onlyLienToken
  
  615   function handleBuyoutLien(BuyoutLienParams calldata params)
  617:     onlyLienToken
  
  632   function updateAfterLiquidationPayment(
  634:   ) external onlyLienToken {

  640   function updateVaultAfterLiquidation(
  643:   ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
```
[PublicVault.sol#L517](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L517), [PublicVault.sol#L617](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L617), [PublicVault.sol#L634](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L634), [PublicVault.sol#L643](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L643)


```solidity
src/WithdrawProxy.sol:
  289   function drain(uint256 amount, address withdrawProxy)
  291:     onlyVault

  306   function handleNewLiquidation(
  309:   ) public onlyVault {
```
[WithdrawProxy.sol#L171](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L171), [WithdrawProxy.sol#L192](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L192), [WithdrawProxy.sol#L291](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L291), [WithdrawProxy.sol#L309](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L309)



**Recommendation:**
Functions guaranteed to revert when called by normal users can be marked payable  (for only `onlyOwner, onlyLienToken, onlyVault` functions)


### [G-14] Use a more recent version of solidity

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings.

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value. 

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

7 results - 7 files:
```solidity
lib/gpl/src/ERC20-Cloned.sol:
  2: pragma solidity >=0.8.16;
```


```solidity
lib/gpl/src/ERC721.sol:
  2: pragma solidity ^0.8.16;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2


```solidity
lib/gpl/src/ERC4626-Cloned.sol:
  2: pragma solidity >=0.8.16;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L2


```solidity
lib/gpl/src/interfaces/IMulticall.sol:
  4: pragma solidity >=0.7.5;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IMulticall.sol#L4


```solidity
lib/gpl/src/interfaces/IUniswapV3Factory.sol:
  2: pragma solidity >=0.5.0;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L2


```solidity
lib/gpl/src/interfaces/IUniswapV3PoolState.sol:
  2: pragma solidity >=0.5.0;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L2


```solidity
lib/gpl/src/interfaces/IWETH9.sol:
  1: pragma solidity ^0.8.13;
```
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol#L1


### [G-15] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

4 results - 3 files:
```solidity
lib/gpl/src/ERC20-Cloned.sol:
  143:       require(
  144:         recoveredAddress != address(0) && recoveredAddress == owner,
  145:         "INVALID_SIGNER"
  146:       );
```
[ERC20-Cloned.sol#L143-L146](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143-L146)


```solidity
src/PublicVault.sol:
  672:     require(
  673:       currentEpoch != 0 &&
  674:         msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
  675:     );

  687:     require(
  688:       currentEpoch != 0 &&
  689:         msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
  690:     );
```
[PublicVault.sol#L672-L675](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672-L675), [PublicVault.sol#L687-L690](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687-L690)


```solidity
src/Vault.sol:
  65:     require(s.allowList[msg.sender] && receiver == owner());
```
[Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)


**Recommendation Code:**
 ```diff
src/Vault.sol#L65
- 65:     require(s.allowList[msg.sender] && receiver == owner());
+           require(s.allowList[msg.sender]);                     
+           require(receiver == owner());                         
```

### [G-16] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

8 results - 4 files:
```solidity
src/CollateralToken.sol:
  526:    if (
  527        s.collateralIdToAuction[collateralId] == bytes32(0) &&
  528        ERC721(s.idToUnderlying[collateralId].tokenContract).ownerOf(
  529        s.idToUnderlying[collateralId].tokenId
  530        ) !=
  531        s.clearingHouse[collateralId]
  532     ) {
```
[CollateralToken.sol#L526-L532](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L526-L532)


```solidity
src/PublicVault.sol:
  295:    if (
  296         address(previousWithdrawProxy) != address(0) &&
  297         previousWithdrawProxy.getFinalAuctionEnd() != 0
  298     ) {

  392:    if (
  393         s.withdrawReserve > 0 &&
  394         timeToEpochEnd() == 0 &&
  395         withdrawProxy != address(0)
  396     ) {
  
  586:    if (v.depositCap != 0 && totalAssets() >= v.depositCap) {
```
[PublicVault.sol#L295-L298](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L295-L298), [PublicVault.sol#L392-L396](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L392-L396), [PublicVault.sol#L586](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L586)


```solidity
src/VaultImplementation.sol:
  66:     if (msg.sender != owner() && msg.sender != s.delegate) {

  237:    if (
  238         msg.sender != holder &&
  239         receiver != holder &&
  240         receiver != operator &&
  241         !CT.isApprovedForAll(holder, msg.sender)
  242     ) {

  258:    if (
  259         (recovered != owner() && recovered != s.delegate) ||
  260         recovered == address(0)
  261     ) {
```
[VaultImplementation.sol#L66](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L66), [VaultImplementation.sol#L237-L242](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L237-L242), [VaultImplementation.sol#L258-L261](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L258-L261)


```solidity
src/AstariaRouter.sol:
  476:    if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd) {
```
[AstariaRouter.sol#L476](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L476)


**Recomendation Code:**
```diff
src/AstariaRouter.sol#L476
- 476:    if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd) {
+         if (timeToSecondEpochEnd > 0) {                                           
+ 	     if (details.duration > timeToSecondEpochEnd) {                         
+	     }                                                                      
+         }                                                                         
```

### [G-17] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

11 results - 7 files:
```solidity
lib/gpl/src/ERC20-Cloned.sol:
  143    require(
  144:       recoveredAddress != address(0) && recoveredAddress == owner,
```
[ERC20-Cloned.sol#L144](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L144)


```solidity
src/AstariaRouter.sol:
  458:   if (details.rate == uint256(0) || details.rate > s.maxInterestRate) {

  476:   if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd) {
```
[AstariaRouter.sol#L458](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L458)
[AstariaRouter.sol#L476](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L476)


```solidity
src/LienToken.sol:
  268:   if (stateHash == bytes32(0) && stack.length != 0) {

  271:   if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
  
  431    if (
  432:       params.lien.details.liquidationInitialAsk < params.amount ||
  433        params.lien.details.liquidationInitialAsk == 0
```
[LienToken.sol#L268](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L268), [LienToken.sol#L271](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L271), [LienToken.sol#L432](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L432)


```solidity
src/Vault.sol:
  65:    require(s.allowList[msg.sender] && receiver == owner());
```
[Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)


```solidity
lib/gpl/src/ERC721.sol:
  117    require(
  118:       msg.sender == from ||
  119        s.isApprovedForAll[from][msg.sender] ||
  120        msg.sender == s.getApproved[id],
```
[ERC721.sol#L118](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L118)


```solidity
src/CollateralToken.sol:
  116    if (
  117:       s.collateralIdToAuction[collateralId] == bytes32(0) ||
  118        liquidator == address(0)

  584:   if (msg.sender == address(this) || msg.sender == address(s.LIEN_TOKEN)) {
```
[CollateralToken.sol#L117](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L117), [CollateralToken.sol#L584](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L584)


```solidity
src/VaultImplementation.sol:
  258    if (
  259:       (recovered != owner() && recovered != s.delegate) ||
  260        recovered == address(0)
```
[VaultImplementation.sol#L259](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L259)


### [G-18] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` AstariaRouter.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

AstariaRouter.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
32299871  =>  _transferAndDepositAssetIfAble(RouterStorage,address,uint256)
64219450  =>  isValidVault(address)
4951ec4a  =>  initialize(Authority,ICollateralToken,ILienToken,ITransferProxy,address,address,address,address,address)
a7d76ed7  =>  mint(IERC4626,address,uint256,uint256)
a625eea8  =>  deposit(IERC4626,address,uint256,uint256)
1e45b063  =>  withdraw(IERC4626,address,uint256,uint256)
8897310d  =>  redeem(IERC4626,address,uint256,uint256)
83bc744b  =>  redeemFutureEpoch(IPublicVault,uint256,address,uint64)
73d15414  =>  pullToken(address,uint256,address)
e96472b2  =>  _loadRouterSlot()
017e7e58  =>  feeTo()
1ff0c9d6  =>  BEACON_PROXY_IMPLEMENTATION()
41700b97  =>  LIEN_TOKEN()
96d6401d  =>  TRANSFER_PROXY()
f5f1f1a7  =>  COLLATERAL_TOKEN()
bce1e58b  =>  __emergencyPause()
c65a7ecf  =>  __emergencyUnpause()
9c427620  =>  fileBatch(File[])
7ec71dc0  =>  file(File)
82f0d462  =>  _file(File)
e87f7c31  =>  setNewGuardian(address)
9c37b27f  =>  __renounceGuardian()
d54aa70a  =>  __acceptGuardian()
2452a88c  =>  fileGuardian(File[])
0472a61d  =>  getImpl(uint8)
e9edc477  =>  getAuctionWindow(bool)
0a5a2e90  =>  _sliceUint(bytes,uint256)
010de84d  =>  validateCommitment(IAstariaRouter.Commitment,uint256)
2d419ce6  =>  _validateCommitment(RouterStorage,IAstariaRouter.Commitment,uint256)
5463ff49  =>  commitToLiens(IAstariaRouter.Commitment[])
54d84628  =>  newVault(address,address)
22ba6a33  =>  newPublicVault(uint256,address,address,uint256,bool,address[],uint256)
e8c65c52  =>  requestLienPosition(IAstariaRouter.Commitment,address)
fb2036ae  =>  canLiquidate(ILienToken.Stack)
6af6521b  =>  liquidate(ILienToken.Stack[],uint8)
0f0245ad  =>  getProtocolFee(uint256)
1276cf53  =>  getLiquidatorFee(uint256)
aaf6b93b  =>  getBuyoutFee(uint256)
c6ccd36d  =>  isValidRefinance(ILienToken.Lien,uint8,ILienToken.Stack[])
402c5629  =>  _newVault(RouterStorage,address,uint256,address,uint256,bool,address[],uint256)
70983c65  =>  _executeCommitment(RouterStorage,IAstariaRouter.Commitment)
```

### [G-19] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.


### [G-20] ``>=`` costs less gas than ``>``

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.

```solidity
src\PublicVault.sol:
  603:       uint256 x = (amount > interestOwing) ? interestOwing : amount;
```