# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables                                                    |     6     |
| [G-002] | Splitting require() statements that use `&&` saves gas                                                                             |     4     |
| [G-003] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |     8     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |     8     |
| [G-005] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |     6     |
| [G-006] | Using `calldata` instead of memory for read-only arguments in external functions saves gas                                         |    16     |
| [G-007] | internal functions only called once can be inlined to save gas                                                                     |     5     |
| [G-008] | Replace modifier with function                                                                                                     |     5     |
| [G-009] | Use elementary types or a user-defined type instead of a struct that has only one member                                           |     2     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:6

[src/AstariaRouter.sol#L512](https://github.com/code-423n4/2023-01-astaria/tree/main//src/AstariaRouter.sol#L512)

```solidity
512:    totalBorrowed += payout;
```

[src/LienToken.sol#L679](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L679)

```solidity
679:    totalCapitalAvailable -= spent;
```

[src/LienToken.sol#L525](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L525)

```solidity
525:    payment -= spent;
```

[src/LienToken.sol#L524](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L524)

```solidity
524:    totalSpent += spent;
```

[src/WithdrawProxy.sol#L277](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L277)

```solidity
277:    balance -= transferAmount;
```

[src/VaultImplementation.sol#L404](https://github.com/code-423n4/2023-01-astaria/tree/main//src/VaultImplementation.sol#L404)

```solidity
404:    amount -= fee;
```

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:4

[lib/gpl/src/ERC20-Cloned.sol#L143-L146](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/ERC20-Cloned.sol#L143-L146)

```solidity
143:    require(
144:            recoveredAddress != address(0) && recoveredAddress == owner,
145:            "INVALID_SIGNER"
146:          );
```

[src/Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/tree/main//src/Vault.sol#L65)

```solidity
65:    require(s.allowList[msg.sender] && receiver == owner());
```

[src/PublicVault.sol#L672-L675](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L672-L675)

```solidity
672:    require(
673:          currentEpoch != 0 &&
674:            msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
675:        );
```

[src/PublicVault.sol#L687-L690](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L687-L690)

```solidity
687:    require(
688:          currentEpoch != 0 &&
689:            msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:        );
```

## [G-003] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:8

[lib/gpl/src/interfaces/IMulticall.sol#L15](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/interfaces/IMulticall.sol#L15)

```solidity
15:    ) external payable returns (bytes[] memory results);
```

[src/AstariaRouter.sol#L527](https://github.com/code-423n4/2023-01-astaria/tree/main//src/AstariaRouter.sol#L527)

```solidity
527:    address[] memory allowList = new address[](1);
```

[src/ClearingHouse.sol#L200](https://github.com/code-423n4/2023-01-astaria/tree/main//src/ClearingHouse.sol#L200)

```solidity
200:    Order[] memory listings = new Order[](1);
```

[src/CollateralToken.sol#L432](https://github.com/code-423n4/2023-01-astaria/tree/main//src/CollateralToken.sol#L432)

```solidity
432:    OfferItem[] memory offer = new OfferItem[](1);
```

[src/CollateralToken.sol#L484](https://github.com/code-423n4/2023-01-astaria/tree/main//src/CollateralToken.sol#L484)

```solidity
484:    uint256[] memory prices = new uint256[](2);
```

[src/CollateralToken.sol#L444](https://github.com/code-423n4/2023-01-astaria/tree/main//src/CollateralToken.sol#L444)

```solidity
444:    ConsiderationItem[] memory considerationItems = new ConsiderationItem[](2);
```

[src/interfaces/ILienToken.sol#L220](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L220)

```solidity
220:    ) external returns (Stack[] memory newStack);
```

[src/interfaces/ILienToken.sol#L227](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L227)

```solidity
227:    ) external returns (Stack[] memory newStack);
```

### Recommendation

Using `storage` instead of `memory`

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:8

[src/WithdrawProxy.sol#L309](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L309)

```solidity
309:    ) public onlyVault {
```

[src/WithdrawProxy.sol#L302](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L302)

```solidity
302:    function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

[src/WithdrawProxy.sol#L235](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L235)

```solidity
235:    function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {
```

[src/PublicVault.sol#L534](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L534)

```solidity
534:    function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
```

[src/PublicVault.sol#L643](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L643)

```solidity
643:    ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
```

[src/PublicVault.sol#L634](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L634)

```solidity
634:    ) external onlyLienToken {
```

[src/PublicVault.sol#L562](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L562)

```solidity
562:    function afterPayment(uint256 computedSlope) public onlyLienToken {
```

[src/CollateralToken.sol#L274](https://github.com/code-423n4/2023-01-astaria/tree/main//src/CollateralToken.sol#L274)

```solidity
274:    ) external onlyOwner(collateralId) {
```

## [G-005] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:6

[lib/gpl/src/ERC721.sol#L22-L23](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/ERC721.sol#L22-L23)

```solidity
22:    mapping(uint256 => address) getApproved;
23:        mapping(address => mapping(address => bool)) isApprovedForAll;
```

[lib/gpl/src/ERC721.sol#L20-L21](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/ERC721.sol#L20-L21)

```solidity
20:    mapping(uint256 => address) _ownerOf;
21:        mapping(address => uint256) _balanceOf;
```

[lib/gpl/src/ERC20-Cloned.sol#L22-L24](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/ERC20-Cloned.sol#L22-L24)

```solidity
22:    mapping(address => uint256) balanceOf;
23:        mapping(address => mapping(address => uint256)) allowance;
24:        mapping(address => uint256) nonces;
```

[src/interfaces/IAstariaRouter.sol#L82-L84](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/IAstariaRouter.sol#L82-L84)

```solidity
82:    mapping(uint8 => address) implementations;
83:        //A strategist can have many deployed vaults
84:        mapping(address => bool) vaults;
```

[src/interfaces/ICollateralToken.sol#L59-L60](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ICollateralToken.sol#L59-L60)

```solidity
59:    mapping(uint256 => bytes32) collateralIdToAuction;
60:        mapping(address => bool) flashEnabled;
```

[src/interfaces/ICollateralToken.sol#L62-L65](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ICollateralToken.sol#L62-L65)

```solidity
62:    mapping(uint256 => Asset) idToUnderlying;
63:        //mapping of a security token hook for an nft's token contract address
64:        mapping(address => address) securityHooks;
65:        mapping(uint256 => address) clearingHouse;
```

## [G-006] Using `calldata` instead of memory for read-only arguments in external functions saves gas

### Impact

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a `for-loop` to copy each index of the `calldata` to the `memory` index. Each iteration of this `for-loop` costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.<br><br> If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one.

### Findings

Total:16

[src/AstariaRouter.sol#L621](https://github.com/code-423n4/2023-01-astaria/tree/main//src/AstariaRouter.sol#L621)

```solidity
621:    function liquidate(ILienToken.Stack[] memory stack, uint8 position)
```

[src/AstariaRouter.sol#L490](https://github.com/code-423n4/2023-01-astaria/tree/main//src/AstariaRouter.sol#L490)

```solidity
490:    function commitToLiens(IAstariaRouter.Commitment[] memory commitments)
```

[src/LienToken.sol#L497-L501](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L497-L501)

```solidity
497:    function payDebtViaClearingHouse(
498:        address token,
499:        uint256 collateralId,
500:        uint256 payment,
501:        AuctionStack[] memory auctionStack
```

[src/LienToken.sol#L513-L517](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L513-L517)

```solidity
513:    function payDebtViaClearingHouse(
514:        address token,
515:        uint256 collateralId,
516:        uint256 payment,
517:        AuctionStack[] memory auctionStack
```

[src/LienToken.sol#L624-L628](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L624-L628)

```solidity
624:    function payDebtViaClearingHouse(
625:        address token,
626:        uint256 collateralId,
627:        uint256 payment,
628:        AuctionStack[] memory auctionStack
```

[src/LienToken.sol#L706](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L706)

```solidity
706:    function getMaxPotentialDebtForCollateral(Stack[] memory stack)
```

[src/LienToken.sol#L855](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L855)

```solidity
855:    function _removeStackPosition(Stack[] memory stack, uint8 position)
```

[src/LienToken.sol#L727](https://github.com/code-423n4/2023-01-astaria/tree/main//src/LienToken.sol#L727)

```solidity
727:    function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
```

[src/interfaces/IAstariaRouter.sol#L179](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/IAstariaRouter.sol#L179)

```solidity
179:    function commitToLiens(Commitment[] memory commitments)
```

[src/interfaces/ILienToken.sol#L204-L208](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L204-L208)

```solidity
204:    function payDebtViaClearingHouse(
205:        address token,
206:        uint256 collateralId,
207:        uint256 payment,
208:        AuctionStack[] memory auctionStack
```

[src/interfaces/ILienToken.sol#L266](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L266)

```solidity
266:    function getMaxPotentialDebtForCollateral(ILienToken.Stack[] memory stack)
```

[src/interfaces/ILienToken.sol#L121-L123](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L121-L123)

```solidity
121:    function makePayment(
122:        uint256 collateralId,
123:        Stack[] memory stack,
```

[src/interfaces/ILienToken.sol#L205-L207](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L205-L207)

```solidity
205:    function makePayment(
206:        uint256 collateralId,
207:        Stack[] memory stack,
```

[src/interfaces/ILienToken.sol#L216-L218](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L216-L218)

```solidity
216:    function makePayment(
217:        uint256 collateralId,
218:        Stack[] memory stack,
```

[src/interfaces/ILienToken.sol#L222-L224](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L222-L224)

```solidity
222:    function makePayment(
223:        uint256 collateralId,
224:        Stack[] memory stack,
```

[src/interfaces/ILienToken.sol#L276-L277](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/ILienToken.sol#L276-L277)

```solidity
276:    function getMaxPotentialDebtForCollateral(
277:        ILienToken.Stack[] memory stack,
```

## [G-007] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:5

[lib/gpl/src/ERC721.sol#L237](https://github.com/code-423n4/2023-01-astaria/tree/main//lib/gpl/src/ERC721.sol#L237)

```solidity
237:    function _safeMint(address to, uint256 id) internal virtual {
```

[src/PublicVault.sol#L556](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L556)

```solidity
556:    function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
```

[src/BeaconProxy.sol#L29](https://github.com/code-423n4/2023-01-astaria/tree/main//src/BeaconProxy.sol#L29)

```solidity
29:    function _delegate(address implementation) internal virtual {
```

[src/BeaconProxy.sol#L20](https://github.com/code-423n4/2023-01-astaria/tree/main//src/BeaconProxy.sol#L20)

```solidity
20:    function _getBeacon() internal pure returns (IBeacon) {
```

[src/VaultImplementation.sol#L397](https://github.com/code-423n4/2023-01-astaria/tree/main//src/VaultImplementation.sol#L397)

```solidity
397:    function _handleProtocolFee(uint256 amount) internal returns (uint256) {
```

## [G-008] Replace modifier with function

### Impact

Modifiers make code more elegant, but cost more than normal functions.

### Findings

Total:5

[src/WithdrawProxy.sol#L230](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L230)

```solidity
230:    modifier onlyVault() {
```

[src/WithdrawProxy.sol#L152](https://github.com/code-423n4/2023-01-astaria/tree/main//src/WithdrawProxy.sol#L152)

```solidity
152:    modifier onlyWhenNoActiveAuction() {
```

[src/PublicVault.sol#L679](https://github.com/code-423n4/2023-01-astaria/tree/main//src/PublicVault.sol#L679)

```solidity
679:    modifier onlyLienToken() {
```

[src/VaultImplementation.sol#L131](https://github.com/code-423n4/2023-01-astaria/tree/main//src/VaultImplementation.sol#L131)

```solidity
131:    modifier whenNotPaused() {
```

[src/CollateralToken.sol#L602](https://github.com/code-423n4/2023-01-astaria/tree/main//src/CollateralToken.sol#L602)

```solidity
602:    modifier whenNotPaused() {
```

## [G-009] Use elementary types or a user-defined type instead of a struct that has only one member

### Impact

Use elementary types or a user-defined type instead of a struct that has only one member.

### Findings

Total:2

[src/ClearingHouse.sol#L36-L38](https://github.com/code-423n4/2023-01-astaria/tree/main//src/ClearingHouse.sol#L36-L38)

```solidity
36:    struct ClearingHouseStorage {
37:        ILienToken.AuctionData auctionStack;
38:      }
```

[src/interfaces/IPublicVault.sol#L54-L56](https://github.com/code-423n4/2023-01-astaria/tree/main//src/interfaces/IPublicVault.sol#L54-L56)

```solidity
54:    struct LiquidationPaymentParams {
55:        uint256 remaining;
56:      }
```
