## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| ERC4626 's implmentation is not fully up to EIP-4626's specification| 7 |
|[L-02]| Loss of precision due to rounding| 1 |
|[L-03]| Front running attacks by the `onlyVault` | 1 |
|[L-04]| Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists| 9 |
|[L-05]| Signature Malleability of EVM's ecrecover()| 1 |
|[L-06]| Missing Event for critical parameters init and change | 5 |
|[L-07]|Use `2StepSetNewGuardian` instead of ` setNewGuardian `| 1 |
|[L-08]| Lack of Input Validation| 1 |
|[L-09]| Stack too deep when compiling| 1 |
|[L-10]| Require messages are too short or not| 22 |
|[L-11]| Allows malleable SECP256K1 signatures| 1 |
|[L-12]| Due to the novelty of the ERC4626 standard, it is safer to use as upgradeable| 1 |

Total 12 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01]|Insufficient coverage |All Contracts|
| [NC-02] |Critical Address Changes Should Use Two-step Procedure  |1|
| [NC-03] |Initial value check is missing in Set Functions| 4 |
| [NC-04] |NatSpec comments should be increased in contracts| All Contracts |
| [NC-05] |`Function writing` that does not comply with the `Solidity Style Guide`  | All contracts |
| [NC-06] |Add a timelock to critical functions | 3 |
| [NC-07] |Include return parameters in NatSpec comments| All Contracts |
| [NC-08] |Keccak Constant values should used to immutable rather than constant | 14 |
| [NC-09] |Need Fuzzing test| 33 |
| [NC-10] |Omissions in Events| 6  |
| [NC-11] |Mark visibility of initialize(...) functions as ``external``| 2 |
| [NC-12] |Use underscores for number literals |1  |
| [NC-13] |Lack of event emission after critical `initialize()` function | 2 |
| [NC-14] |`Empty blocks` should be _removed_ or _Emit_ something | 1 |
| [NC-15] |Tokens accidentally sent to the contract cannot be recovered | 1 |
| [NC-16] |Assembly Codes Specific – Should Have Comments | 12 |
| [NC-17] |Use a single file for all system-wide constants| 19 |
| [NC-18] |Take advantage of Custom Error's return value property | 23 |
| [NC-19] |Add NatSpec Mapping comment|13 |
| [NC-20] |Incorrect NatSpec comments|1 |
| [NC-21] |There is no need to cast a variable that is already an address, such as address(x)|1 |
| [NC-22] |Repeated validation logic can be converted into a function modifier|8 |
| [NC-23] |ABIEncoderV2 is still valid, but it is deprecated and has no effect|2 |
| [NC-24] |Avoid _shadowing_ `inherited state variables`|1 |

Total 24 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Project Upgrade and Stop Scenario should be |
| [S-02] |Use descriptive names for Contracts and Libraries|

Total 2 suggestions


### [L-01] ERC4626 's implmentation is not fully up to EIP-4626's specification


The `maxDeposit` and `maxMint` functions from the EIP-4626's specification are not available


Contracts implementing the IERC4626 specification;
```solidity
7 results - 7 files

src/AstariaRouter.sol:
  39: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/AstariaVaultBase.sol:
  18: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/PublicVault.sol:
  24: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/WithdrawProxy.sol:
  25: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/WithdrawVaultBase.sol:
  18: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/interfaces/IAstariaRouter.sol:
  18: import {IERC4626} from "core/interfaces/IERC4626.sol";

src/interfaces/IWithdrawProxy.sol:
  17: import {IERC4626} from "core/interfaces/IERC4626.sol";

```

Functions to be added;
```solidity

	function maxDeposit(address) public view virtual returns (uint256) {
		return type(uint256).max;
	}

	function maxMint(address) public view virtual returns (uint256) {
		return type(uint256).max;
	}

```

Could cause unexpected behavior in the future due to non-compliance with EIP-4626 standard.

Similar problem for Sentimentxyz ;
https://github.com/sentimentxyz/protocol/pull/235/files


### Recommended Mitigation Steps

maxMint() and maxDeposit() should reflect the limitation of maxSupply.

Consider changing maxMint() and maxDeposit() to:



```diff

 function maxMint(address) public view virtual returns (uint256) {
-        return type(uint256).max;
+      if (totalSupply >= maxSupply) return 0;
+      return maxSupply - totalSupply;
    }

 function maxDeposit(address) public view virtual returns (uint256) {
-       return type(uint256).max;
+       return convertToAssets(maxMint(address(0)));
    }

```


### [L-02] Loss of precision due to rounding

Add scalar so roundings are negligible

[AstariaRouter.sol#L112](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L112)

```solidity
src/AstariaRouter.sol:
  112:     s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));

```

### [L-03] Front running attacks by the `onlyVault` 


Project has one  possible attack vectors by the `onlyVault`:

With the function `setWithdrawRatio` the following is set:  Withdraw ratio

When a user uses the platform expecting a low Withdraw Ratio, the authorities can run the "setWithdrawRatio()" function up front and raise the price significantly. If the size is large enough, this can be a substantial amount of money.

```solidity
src/WithdrawProxy.sol:
  301  
  302:   function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
  303:     _loadSlot().withdrawRatio = liquidationWithdrawRatio.safeCastTo88();
  304:   }

```


### Recommended Mitigation Steps

Use a timelock to avoid instant changes of the parameters.
### [L-04] Solmate's SafeTransferLib doesn't check whether the ERC20 contract exists

Solmate's SafeTransferLib, which is often used to interact with non-compliant/unsafe ERC20 tokens, does not check whether the ERC20 contract exists. The following code will not revert in case the token doesn't exist (yet).

This is stated in the Solmate library:
https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9



```solidity

9 results - 9 files

src/AstariaRouter.sol:
  19: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  57:   using SafeTransferLib for ERC20;

src/ClearingHouse.sol:
  19: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  34:   using SafeTransferLib for ERC20;

src/CollateralToken.sol:
  32: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  70:   using SafeTransferLib for ERC20;

src/LienToken.sol:
  36: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  48:   using SafeTransferLib for ERC20;

src/PublicVault.sol:
  19: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  50:   using SafeTransferLib for ERC20;

src/TransferProxy.sol:
  17: import {SafeTransferLib, ERC20} from "solmate/utils/SafeTransferLib.sol";
  22:   using SafeTransferLib for ERC20;

src/Vault.sol:
  17: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  26:   using SafeTransferLib for ERC20;

src/VaultImplementation.sol:
  19: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  39:   using SafeTransferLib for ERC20;

src/WithdrawProxy.sol:
  18: import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
  38:   using SafeTransferLib for ERC20;
```


### Recommended Mitigation Steps

Add a contract exist control in functions;
```js
pragma solidity >=0.8.0;

function isContract(address _addr) private returns (bool isContract) {
    isContract = _addr.code.length > 0;
}

```
### [L-05] Signature Malleability of EVM's ecrecover()

**Context:**

```solidity

src/VaultImplementation.sol:
  245      VIData storage s = _loadVISlot();
  246:     address recovered = ecrecover(
  247:       keccak256(
  248:         _encodeStrategyData(
  249:           s,
  250:           params.lienRequest.strategy,
  251:           params.lienRequest.merkle.root
  252:         )
  253:       ),
  254:       params.lienRequest.v,
  255:       params.lienRequest.r,
  256:       params.lienRequest.s
  257:     );

```
**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [L-06] Missing Event for critical parameters init and change

**Context:**
```solidity

src/TransferProxy.sol:
  24:   constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY) {
  25:     //only constructor we care about is  Auth

src/AstariaRouter.sol:
  83:   function initialize(

src/CollateralToken.sol:
  80:   function initialize(

src/LienToken.sol:
  59:   function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)

src/VaultImplementation.sol:
  190:   function init(InitParams calldata params) external virtual {

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-07] Use `2StepSetNewGuardian` instead of ` setNewGuardian `

```solidity
src/AstariaRouter.sol:
  338  
  339:   function setNewGuardian(address _guardian) external {
  340:     RouterStorage storage s = _loadRouterSlot();
  341:     require(msg.sender == s.guardian);
  342:     s.newGuardian = _guardian;
  343:   }

```


**Description:**
``` setNewGuardian ``` function is used to change `guardian` address

Use a 2 structure which is safer. 

**Recommendation:**
Use 2-stage guardian address transfer :


```js
abstract contract 2StepSetNewGuardian {
    address private _pendingGuardian;

    // Guardian address
    address private guardian;
    address public newGuardian;
    event GuardianTransferStarted(address indexed previousGuardian, address indexed newGuardian);

    /**
     * @dev Returns the address of the pending Guardian.
     */
    function pendingGuardian() public view returns (address) {
        return _pendingGuardian;
    }

    /**
     * @dev Starts the Guardian transfer of the contract to a new account. Replaces the pending transfer if there is one.
     * Can only be called by the current Guardian.
     */
    function transferGuardian(address newGuardian) public onlyGuardian {
        _pendingGuardian = newGuardian;
        emit GuardianTransferStarted(guardian(), newGuardian);
    }

    /**
     * @dev Transfers Guardian of the contract to a new account (`newGuardian`) and deletes any pending Guardian.
     * Internal function without access restriction.
     */
    function _transferGuardian(address newGuardian) internal {
        delete _pendingGuardian;
        transferGuardian(newGuardian);
    }

    /**
     * @dev The new Guardian accepts the Guardian transfer.
     */
    function accept Guardian() external {
        address sender = _msgSender();
        require(pendingGuardian() == sender, " 2StepSetGuardian : caller is not the new Guardian");
        _transferGuardian(sender);
    }
}
```


### [L-08] Lack of Input Validation

For defence-in-depth purpose, it is recommended to perform additional validation against the amount that the user is attempting to deposit, mint, withdraw and redeem to ensure that the submitted amount is valid.

[OpenZeppelinTokenizedVault.sol#L9](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7c75b8aa89073376fb67d78a40f6d69331092c94/contracts/token/ERC20/extensions/ERC20TokenizedVault.sol#L95)



```diff
src/PublicVault.sol:
  147  
  148:   function _redeemFutureEpoch(
  149:     VaultData storage s,
  150:     uint256 shares,
  151:     address receiver,
  152:     address owner,
  153:     uint64 epoch
  154:   ) internal virtual returns (uint256 assets) {
  155:     // check to ensure that the requested epoch is not in the past
  156: 
  157:     ERC20Data storage es = _loadERC20Slot();
  158: 
  159:     if (msg.sender != owner) {
  160:       uint256 allowed = es.allowance[owner][msg.sender]; // Saves gas for limited approvals.
  161: 
+               require(shares <= maxRedeem(owner), "redeem more than max");

  162:       if (allowed != type(uint256).max) {
  163:         es.allowance[owner][msg.sender] = allowed - shares;
  164:       }
  165:     }
  166: 
  167:     if (epoch < s.currentEpoch) {
  168:       revert InvalidState(InvalidStates.EPOCH_TOO_LOW);
  169:     }
  170:     require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
  171:     // check for rounding error since we round down in previewRedeem.
  172: 
  173:     //this will underflow if not enough balance
  174:     es.balanceOf[owner] -= shares;
  175: 
  176:     // Cannot overflow because the sum of all user
  177:     // balances can't exceed the max uint256 value.
  178:     unchecked {
  179:       es.balanceOf[address(this)] += shares;
  180:     }
  181: 
  182:     emit Transfer(owner, address(this), shares);
  183:     // Deploy WithdrawProxy if no WithdrawProxy exists for the specified epoch
  184:     _deployWithdrawProxyIfNotDeployed(s, epoch);
  185: 
  186:     emit Withdraw(msg.sender, receiver, owner, assets, shares);
  187: 
  188:     // WithdrawProxy shares are minted 1:1 with PublicVault shares
  189:     WithdrawProxy(s.epochData[epoch].withdrawProxy).mint(shares, receiver);
  190:   }

```


### [L-09] Stack too deep when compiling

The project cannot be compiled due to the "stack too deep" error.

The “stack too deep” error is a limitation of the current code generator. The EVM stack only has 16 slots and that’s sometimes not enough to fit all the local variables, parameters and/or return variables. The solution is to move some of them to memory, which is more expensive but at least makes your code compile.

```js
[⠊] Compiling...
[⠃] Compiling 202 files with 0.8.17
[⠒] Solc 0.8.17 finished in 6.03s
Error: 
Compiler run failed
CompilerError: Stack too deep. Try compiling with `--via-ir` (cli) or the equivalent `viaIR: true` (standard JSON) while enabling the optimizer. Otherwise, try removing local variables.
   --> lib/seaport/contracts/lib/FulfillmentApplier.sol:223:9:
    |
223 |         assembly {
    |         ^ (Relevant source part starts here and spans across multiple lines).

```

ref:https://forum.openzeppelin.com/t/stack-too-deep-when-compiling-inline-assembly/11391/6

### [L-10] Require messages are too short or not

**Context:**



**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.


```solidity

22 results - 7 files

src/AstariaRouter.sol:
  341:     require(msg.sender == s.guardian);
  347:     require(msg.sender == s.guardian);
  354:     require(msg.sender == s.newGuardian);
  361:     require(msg.sender == address(s.guardian));

src/AuthInitializable.sol:
  85:     require(

src/ClearingHouse.sol:
   72:     require(msg.sender == address(ASTARIA_ROUTER.LIEN_TOKEN()));
  199:     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  216:     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  223:     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

src/CollateralToken.sol:
  266:     require(ownerOf(collateralId) == msg.sender);
  535:     require(msg.sender == s.clearingHouse[collateralId]);
  564:       require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));

src/LienToken.sol:
  504:     require(
  860:     require(position < length);

src/PublicVault.sol:
  241:       require(s.allowList[receiver]);
  259:       require(s.allowList[receiver]);
  508:     require(msg.sender == owner()); //owner is "strategist"
  672:     require(
  680:     require(msg.sender == address(LIEN_TOKEN()));
  687:     require(

src/Vault.sol:
  65:     require(s.allowList[msg.sender] && receiver == owner());
  71:     require(msg.sender == owner());

```

### [L-11] Allows malleable SECP256K1 signatures

Here, the ecrecover() method doesn't check the s range.

Homestead (EIP-2) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.
Since an order can only be confirmed once and its hash is saved, there doesn't seem to be a serious danger in existing use cases.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149


```solidity

lib/gpl/src/ERC20-Cloned.sol:
  105  
  106:   function permit(
  107:     address owner,
  108:     address spender,
  109:     uint256 value,
  110:     uint256 deadline,
  111:     uint8 v,
  112:     bytes32 r,
  113:     bytes32 s
  114:   ) public virtual {
  115:     require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
  116: 
  117:     // Unchecked because the only math done is incrementing
  118:     // the owner's nonce which cannot realistically overflow.
  119: 
  120:     unchecked {
  121:       address recoveredAddress = ecrecover(
  122:         keccak256(
  123:           abi.encodePacked(
  124:             "\x19\x01",
  125:             DOMAIN_SEPARATOR(),
  126:             keccak256(
  127:               abi.encode(
  128:                 PERMIT_TYPEHASH,
  129:                 owner,
  130:                 spender,
  131:                 value,
  132:                 _loadERC20Slot().nonces[owner]++,
  133:                 deadline
  134:               )
  135:             )
  136:           )
  137:         ),
  138:         v,
  139:         r,
  140:         s
  141:       );
  142: 
  143:       require(
  144:         recoveredAddress != address(0) && recoveredAddress == owner,
  145:         "INVALID_SIGNER"
  146:       );
  147: 
  148:       _loadERC20Slot().allowance[recoveredAddress][spender] = value;
  149:     }
  150: 
  151:     emit Approval(owner, spender, value);
  152:   }
```
### [L-12] Due to the novelty of the ERC4626 standard, it is safer to use as upgradeable

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L11

ERC-4626 is a standard to optimize and unify the technical parameters of yield-bearing vaults. It provides a standard API for tokenized yield-bearing vaults that represent shares of a single underlying ERC-20 token. ERC-4626 also outlines an optional extension for tokenized vaults utilizing ERC-20, offering basic functionality for depositing, withdrawing tokens and reading balances.


ERC4626 Tokenized Vauldt was created 2021-12-22 so this standart is safer to use as upgrade


### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is ~90%. Testing all functions is best practice in terms of security criteria.

```js
README.md:
  95: - What is the overall line coverage percentage provided by your tests?: ~90 
  (`forge coverage` currently throws a "stack too deep" error on large codebases)

```
Due to its capacity, test coverage is expected to be 100%.

### [N-02] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity

src/VaultImplementation.sol:
  209  
  210:   function setDelegate(address delegate_) external {
  211:     require(msg.sender == owner()); //owner is "strategist"
  212:     VIData storage s = _loadVISlot();
  213:     s.delegate = delegate_;
  214:     emit DelegateUpdated(delegate_);
  215:     emit AllowListUpdated(delegate_, true);
  216:   }


```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [N-03] Initial value check is missing in Set Functions

**Context:**


```js
4 results - 4 files

src/AstariaRouter.sol:
  339:   function setNewGuardian(address _guardian) external {

src/ClearingHouse.sol:
  66:   function setAuctionData(ILienToken.AuctionData calldata auctionData)

src/VaultImplementation.sol:
  210:   function setDelegate(address delegate_) external {

src/WithdrawProxy.sol:
  302:   function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {


```



Checking whether the current value and the new value are the same should be added
### [N-04] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-05] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-06] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

3 results - 3 files

src/AstariaRouter.sol:
  339:   function setNewGuardian(address _guardian) external {

src/VaultImplementation.sol:
  210:   function setDelegate(address delegate_) external {

src/WithdrawProxy.sol:
  302:   function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {

```

### [N-07] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```
### [N-08] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity
14 results - 10 files

src/AstariaRouter.sol:
  62:   uint256 private constant ROUTER_SLOT =

src/ClearingHouse.sol:
  40:   uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =

src/CollateralToken.sol:
  73:   uint256 private constant COLLATERAL_TOKEN_SLOT =

src/LienToken.sol:
  50:   uint256 private constant LIEN_SLOT =
  53:   bytes32 constant ACTIVE_AUCTION = bytes32("ACTIVE_AUCTION");

src/PublicVault.sol:
  53:   uint256 private constant PUBLIC_VAULT_SLOT =

src/VaultImplementation.sol:
  44:   bytes32 public constant STRATEGY_TYPEHASH =
  47:   bytes32 constant EIP_DOMAIN =
  51:   bytes32 constant VERSION = keccak256("0");
  57:   uint256 private constant VI_SLOT =

src/WithdrawProxy.sol:
  49:   uint256 private constant WITHDRAW_PROXY_SLOT =

src/actions/UNIV3/ClaimFees.sol:
  23:   bytes32 private constant FLASH_ACTION_MAGIC =

src/utils/Initializable.sol:
  331:   uint256 private constant INITIALIZER_SLOT =

src/utils/Pausable.sol:
  20:   uint256 private constant PAUSE_SLOT =


```

### [N-09] Need Fuzzing test

**Context:**

```solidity
33 results - 7 files

src/AstariaRouter.sol:
  266        _file(files[i]);
  267:       unchecked {
  268          ++i;

  386        emit FileUpdated(what, data);
  387:       unchecked {
  388          ++i;

  512        totalBorrowed += payout;
  513:       unchecked {
  514          ++i;

src/ClearingHouse.sol:
  97        output[i] = type(uint256).max;
  98:       unchecked {
  99          ++i;

src/CollateralToken.sol:
  199        _file(files[i]);
  200:       unchecked {
  201          ++i;

src/LienToken.sol:
  171  
  172:       unchecked {
  173          --i;

  222        }
  223:       unchecked {
  224          ++i;

  329        }
  330:       unchecked {
  331          ++i;

  478  
  479:       unchecked {
  480          potentialDebt += _getOwed(newStack[j], newStack[j].point.end);

  485  
  486:       unchecked {
  487          --i;

  521        uint256 spent;
  522:       unchecked {
  523          spent = _paymentAH(s, token, stack, i, payment, payer);

  681        if (newStack.length == oldLength) {
  682:         unchecked {
  683            ++i;

  720        maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);
  721:       unchecked {
  722          ++i;

  735        maxPotentialDebt += _getOwed(stack[i], end);
  736:       unchecked {
  737          ++i;

  864        newStack[i] = stack[i];
  865:       unchecked {
  866          ++i;

  869      for (; i < length - 1; ) {
  870:       unchecked {
  871          newStack[i] = stack[i + 1];

src/PublicVault.sol:
  177      // balances can't exceed the max uint256 value.
  178:     unchecked {
  179        es.balanceOf[address(this)] += shares;

  320  
  321:       unchecked {
  322          if (totalAssets() > expected) {

  339      // increment epoch
  340:     unchecked {
  341        s.currentEpoch++;

  378        } else {
  379:         unchecked {
  380            s.withdrawReserve -= withdrawBalance.safeCastTo88();

  403        );
  404:       unchecked {
  405          s.withdrawReserve -= drainBalance.safeCastTo88();

  447      _accrue(s);
  448:     unchecked {
  449        uint48 newSlope = s.slope + lienSlope.safeCastTo48();

  465    function _accrue(VaultData storage s) internal returns (uint256) {
  466:     unchecked {
  467        s.yIntercept = (_totalAssets(s)).safeCastTo88();

  521  
  522:     unchecked {
  523        uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();

  556    function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
  557:     unchecked {
  558        s.epochData[epoch].liensOpenForEpoch++;

  563      VaultData storage s = _loadStorageSlot();
  564:     unchecked {
  565        s.slope += computedSlope.safeCastTo48();

  581  
  582:     unchecked {
  583        s.yIntercept += assets.safeCastTo88();

  620  
  621:     unchecked {
  622        uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();

  646      _accrue(s);
  647:     unchecked {
  648        _setSlope(s, s.slope - params.lienSlope.safeCastTo48());

src/VaultImplementation.sol:
  202          s.allowList[params.allowList[i]] = true;
  203:         unchecked {
  204            ++i;

  402  
  403:       unchecked {
  404          amount -= fee;

src/WithdrawProxy.sol:
  275  
  276:       unchecked {
  277          balance -= transferAmount;

  311  
  312:     unchecked {
  313        s.expected += newLienExpectedValue.safeCastTo88();


```

**Description:**
In total 7 contracts, 33 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [N-10] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

```solidity
6 results - 4 files

src/ClearingHouse.sol:
   66:   function setAuctionData(ILienToken.AuctionData calldata auctionData)
  104:   function setApprovalForAll(address operator, bool approved) external {}
  220:   function settleLiquidatorNFTClaim() external {

src/CollateralToken.sol:
  524:   function settleAuction(uint256 collateralId) public {

src/VaultImplementation.sol:
  210:   function setDelegate(address delegate_) external {

src/WithdrawProxy.sol:
  302:   function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {


```
### [N-11] Mark visibility of initialize(...) functions as ``external``

```solidity

src/CollateralToken.sol:
   79  
   80:   function initialize(
   81:     Authority AUTHORITY_,
   82:     ITransferProxy TRANSFER_PROXY_,
   83:     ILienToken LIEN_TOKEN_,
   84:     ConsiderationInterface SEAPORT_
   85:   ) public initializer {
   86:     __initAuth(msg.sender, address(AUTHORITY_));
   87:     __initERC721("Astaria Collateral Token", "ACT");
   88:     CollateralStorage storage s = _loadCollateralSlot();
   89:     s.TRANSFER_PROXY = TRANSFER_PROXY_;
   90:     s.LIEN_TOKEN = LIEN_TOKEN_;
   91:     s.SEAPORT = SEAPORT_;
   92:     (, , address conduitController) = s.SEAPORT.information();
   93:     bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
   94:     s.CONDUIT_KEY = CONDUIT_KEY;
   95:     s.CONDUIT_CONTROLLER = ConduitControllerInterface(conduitController);
   96: 
   97:     s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(CONDUIT_KEY, address(this));
   98:     s.CONDUIT_CONTROLLER.updateChannel(
   99:       address(s.CONDUIT),
  100:       address(SEAPORT_),
  101:       true
  102:     );
  103:   }


src/LienToken.sol:
  58  
  59:   function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
  60:     public
  61:     initializer
  62:   {
  63:     __initAuth(msg.sender, address(_AUTHORITY));
  64:     __initERC721("Astaria Lien Token", "ALT");
  65:     LienStorage storage s = _loadLienStorageSlot();
  66:     s.TRANSFER_PROXY = _TRANSFER_PROXY;
  67:     s.maxLiens = uint8(5);
  68:   }

```

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750

### [N-12] Use underscores for number literals

```diff
1 result - 1 file

src/PublicVault.sol:
- 604:       uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
+	 uint256 fee = x.mulDivDown(VAULT_FEE(), 10_000);
  605        uint88 feeInShares = convertToShares(fee).safeCastTo88();

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-13] Lack of event emission after critical `initialize()` function

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the `initialize()` function:

```solidity
src/CollateralToken.sol:
   79  
   80:   function initialize(
   81:     Authority AUTHORITY_,
   82:     ITransferProxy TRANSFER_PROXY_,
   83:     ILienToken LIEN_TOKEN_,
   84:     ConsiderationInterface SEAPORT_
   85:   ) public initializer {
   86:     __initAuth(msg.sender, address(AUTHORITY_));
   87:     __initERC721("Astaria Collateral Token", "ACT");
   88:     CollateralStorage storage s = _loadCollateralSlot();
   89:     s.TRANSFER_PROXY = TRANSFER_PROXY_;
   90:     s.LIEN_TOKEN = LIEN_TOKEN_;
   91:     s.SEAPORT = SEAPORT_;
   92:     (, , address conduitController) = s.SEAPORT.information();
   93:     bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
   94:     s.CONDUIT_KEY = CONDUIT_KEY;
   95:     s.CONDUIT_CONTROLLER = ConduitControllerInterface(conduitController);
   96: 
   97:     s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(CONDUIT_KEY, address(this));
   98:     s.CONDUIT_CONTROLLER.updateChannel(
   99:       address(s.CONDUIT),
  100:       address(SEAPORT_),
  101:       true
  102:     );
  103:   }


src/LienToken.sol:
  58  
  59:   function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
  60:     public
  61:     initializer
  62:   {
  63:     __initAuth(msg.sender, address(_AUTHORITY));
  64:     __initERC721("Astaria Lien Token", "ALT");
  65:     LienStorage storage s = _loadLienStorageSlot();
  66:     s.TRANSFER_PROXY = _TRANSFER_PROXY;
  67:     s.maxLiens = uint8(5);
  68:   }


```

### [N-14] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```solidity

src/BeaconProxy.sol:
  86     */
  87:   function _beforeFallback() internal virtual {}
  88  }

```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### [N-15] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```


### [N-16] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.


```solidity
12 results - 10 files

src/AstariaRouter.sol:
  219:     assembly {
  414:     assembly {

src/BeaconProxy.sol:
  30:     assembly {
  31:       // Copy msg.data. We take full control of memory in this inline assembly

src/ClearingHouse.sol:
  61:     assembly {

src/CollateralToken.sol:
  152:     assembly {

src/LienToken.sol:
  77:     assembly {

src/PublicVault.sol:
  430:     assembly {

src/VaultImplementation.sol:
  85:     assembly {

src/WithdrawProxy.sol:
  210:     assembly {

src/libraries/CollateralLookup.sol:
  24:     assembly {

src/utils/Pausable.sol:
  40:     assembly {

```
### [N-17] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
19 results - 13 files

src/AstariaRouter.sol:
  62:   uint256 private constant ROUTER_SLOT =
  66:   uint256 private constant OUTOFBOUND_ERROR_SELECTOR =
  68:   uint256 private constant ONE_WORD = 0x20;

src/ClearingHouse.sol:
  40:   uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =

src/CollateralToken.sol:
  73:   uint256 private constant COLLATERAL_TOKEN_SLOT =

src/LienToken.sol:
  50:   uint256 private constant LIEN_SLOT =
  53:   bytes32 constant ACTIVE_AUCTION = bytes32("ACTIVE_AUCTION");

src/PublicVault.sol:
  53:   uint256 private constant PUBLIC_VAULT_SLOT =

src/VaultImplementation.sol:
  44:   bytes32 public constant STRATEGY_TYPEHASH =
  47:   bytes32 constant EIP_DOMAIN =
  51:   bytes32 constant VERSION = keccak256("0");
  57:   uint256 private constant VI_SLOT =

src/WithdrawProxy.sol:
  49:   uint256 private constant WITHDRAW_PROXY_SLOT =

src/actions/UNIV3/ClaimFees.sol:
  23:   bytes32 private constant FLASH_ACTION_MAGIC =

src/libraries/Base64.sol:
  4:   bytes internal constant TABLE =

src/strategies/CollectionValidator.sol:
  32:   uint8 public constant VERSION_TYPE = uint8(2);

src/strategies/UNI_V3Validator.sol:
  57:   uint8 public constant VERSION_TYPE = uint8(3);

src/strategies/UniqueValidator.sol:
  33:   uint8 public constant VERSION_TYPE = uint8(1);

src/utils/Pausable.sol:
  20:   uint256 private constant PAUSE_SLOT =
```
### [N-18] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.


```solidity
23 results - 3 files

src/AstariaRouter.sol:
  293:       if (denominator < numerator) revert InvalidFileData();
  301:       if (denominator < numerator) revert InvalidFileData();
  309:       if (denominator < numerator) revert InvalidFileData();
  326:       if (addr == address(0)) revert InvalidFileData();
  330:       if (addr == address(0)) revert InvalidFileData();
  333:       revert UnsupportedFile();
  369:         if (addr == address(0)) revert InvalidFileData();
  373:         if (addr == address(0)) revert InvalidFileData();
  377:         if (addr == address(0)) revert InvalidFileData();
  381:         if (addr == address(0)) revert InvalidFileData();

src/CollateralToken.sol:
  312:       revert FlashActionCallbackFailed();
  319:       revert FlashActionSecurityCheckFailed();
  327:       revert FlashActionNFTNotReturned();
  339:       revert InvalidSender();
  510:       revert InvalidConduitKey();
  513:       revert InvalidZone();
  585:         revert InvalidCollateral();
  598:       revert();
  604:       revert ProtocolPaused();

src/LienToken.sol:
   91:       revert UnsupportedFile();
  117:       revert InvalidSender();
  135:       revert InvalidRefinance();
  806:       revert InvalidLoanState();

```

### [N-19]  Add NatSpec Mapping comment

**Description:**
Add NatSpec comments describing mapping keys and values


**Context:**

```solidity
13 results - 5 files

src/interfaces/IAstariaRouter.sol:
  81:     mapping(uint8 => address) strategyValidators;
  82:     mapping(uint8 => address) implementations;
  84:     mapping(address => bool) vaults;

src/interfaces/ICollateralToken.sol:
  59:     mapping(uint256 => bytes32) collateralIdToAuction;
  60:     mapping(address => bool) flashEnabled;
  62:     mapping(uint256 => Asset) idToUnderlying;
  64:     mapping(address => address) securityHooks;
  65:     mapping(uint256 => address) clearingHouse;

src/interfaces/ILienToken.sol:
  42:     mapping(uint256 => bytes32) collateralStateHash;
  43:     mapping(uint256 => AuctionData) auctionData;
  44:     mapping(uint256 => LienMeta) lienMeta;

src/interfaces/IPublicVault.sol:
  33:     mapping(uint64 => EpochData) epochData;

src/interfaces/IVaultImplementation.sol:
  49:     mapping(address => bool) allowList;



```
**Recommendation:**

```solidity

 /// @dev    address(user) -> address(ERC0 Contract Address) -> uint256(allowance amount from user)
    mapping(address => mapping(address => uint256)) public allowance;

```


### [N-20]  Incorrect NatSpec comments

This  NatSpec comments are incorrect . Below code block has 2 slots comment but Natspec comment was declaration to only 1 slot(//slot2)

**Context:**

```solidity
src/interfaces/IAstariaRouter.sol:
  65      uint32 protocolFeeDenominator;
  66:     //slot 2
  67:     ERC20 WETH; //20
  68:     ICollateralToken COLLATERAL_TOKEN; //20
  69:     ILienToken LIEN_TOKEN; //20
  70:     ITransferProxy TRANSFER_PROXY; //20
  71:     address feeTo; //20
  72:     address BEACON_PROXY_IMPLEMENTATION; //20
  73:     uint88 maxInterestRate; //6
  74:     uint32 minInterestBPS; // was uint64
  75:     //slot 3 +

```


## [N-21] There is no need to cast a variable that is already an address, such as address(x)

```diff
 function pullToken(
    address token,
    uint256 amount,
    address recipient
  ) public payable override {
    RouterStorage storage s = _loadRouterSlot();
    s.TRANSFER_PROXY.tokenTransferFrom(
-     address(token),
+    token
      msg.sender,
      recipient,
      amount
    );
  }
```
**Description:**
There is no need to cast a variable that is already an address, such as address(token) tokenContract also address.

**Recommendation:**
Use directly variable.

### [N-22] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code


```solidity
8 results - 1 file

src/AstariaRouter.sol:
  326:       if (addr == address(0)) revert InvalidFileData();
  330:       if (addr == address(0)) revert InvalidFileData();
  369:         if (addr == address(0)) revert InvalidFileData();
  373:         if (addr == address(0)) revert InvalidFileData();
  377:         if (addr == address(0)) revert InvalidFileData();
  381:         if (addr == address(0)) revert InvalidFileData();
  397:     if (impl == address(0)) {
  444:     if (strategyValidator == address(0)) {


```


### [N-23] ABIEncoderV2 is still valid, but it is deprecated and has no effect

```solidity

2 results - 2 files

src/CollateralToken.sol:
  15  
  16: pragma experimental ABIEncoderV2;
  17  

src/LienToken.sol:
  15  
  16: pragma experimental ABIEncoderV2;
  17 

```

You can choose to use the old behaviour using pragma abicoder v1;. The pragma pragma experimental ABIEncoderV2; is still valid, but it is deprecated and has no effect. If you want to be explicit, please use pragma abicoder v2; instead.

[ABIEncoderV2](https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html#silent-changes-of-the-semantics)

## [N-24] Avoid _shadowing_ `inherited state variables`

```solidity
src/PublicVault.sol:
  116     */
  117:   function redeem(
  118:     uint256 shares,
  119:     address receiver,
  120:     address owner
  121:   ) public virtual override(ERC4626Cloned) returns (uint256 assets) {
  122:     VaultData storage s = _loadStorageSlot();
  123:     assets = _redeemFutureEpoch(s, shares, receiver, owner, s.currentEpoch);
  124:   }
```

**Description:**
In ``` src/PublicVault.sol #L123``` , there is a local variable named ```owner```, but there is a state varible named ```owner``` in the inherited ```AsteriaVaultBase.sol``` with the same name. This use causes compilers to issue warnings, negatively affecting checking and code readability. 

```js
src/AstariaVaultBase.sol:
  35  
  36:   function owner() public pure returns (address) {
  37:     return _getArgAddress(21); //ends at 44
  38:   }
```

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

### [S-01] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [S-02] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.
