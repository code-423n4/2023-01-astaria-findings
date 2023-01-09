
### 1st BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: 

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::412 => uint256 length = bs.length;
2023-01-astaria/src/AstariaRouter.sol::417 => if lt(length, end) {
2023-01-astaria/src/AstariaRouter.sol::498 => lienIds = new uint256[](commitments.length);
2023-01-astaria/src/ClearingHouse.sol::95 => output = new uint256[](accounts.length);
2023-01-astaria/src/LienToken.sol::207 => uint256 n = newStack.length;
2023-01-astaria/src/LienToken.sol::268 => if (stateHash == bytes32(0) && stack.length != 0) {
2023-01-astaria/src/LienToken.sol::301 => auctionData.stack = new AuctionStack[](stack.length);
2023-01-astaria/src/LienToken.sol::412 => uint8(params.stack.length),
2023-01-astaria/src/LienToken.sol::418 => uint8(params.stack.length),
2023-01-astaria/src/LienToken.sol::420 => uint8(newStack.length)
2023-01-astaria/src/LienToken.sol::438 => if (params.stack.length > 0) {
2023-01-astaria/src/LienToken.sol::464 => if (stack.length >= s.maxLiens) {
2023-01-astaria/src/LienToken.sol::468 => newStack = new Stack[](stack.length + 1);
2023-01-astaria/src/LienToken.sol::469 => newStack[stack.length] = newSlot;
2023-01-astaria/src/LienToken.sol::491 => stack.length > 0 && potentialDebt > newSlot.lien.details.maxPotentialDebt
2023-01-astaria/src/LienToken.sol::670 => uint256 oldLength = newStack.length;
2023-01-astaria/src/LienToken.sol::681 => if (newStack.length == oldLength) {
2023-01-astaria/src/LienToken.sol::695 => if (stack.length == 0) {
2023-01-astaria/src/LienToken.sol::712 => return _getMaxPotentialDebtForCollateralUpToNPositions(stack, stack.length);
2023-01-astaria/src/LienToken.sol::859 => uint256 length = stack.length;
2023-01-astaria/src/LienToken.sol::860 => require(position < length);
2023-01-astaria/src/LienToken.sol::861 => newStack = new ILienToken.Stack[](length - 1);
2023-01-astaria/src/LienToken.sol::869 => for (; i < length - 1; ) {
2023-01-astaria/src/LienToken.sol::879 => uint8(newStack.length)
2023-01-astaria/src/VaultImplementation.sol::302 => stack[stack.length - 1].point.end,
2023-01-astaria/src/libraries/Base64.sol::9 => uint256 len = data.length;
2023-01-astaria/src/utils/Initializable.sol::41 => return account.code.length > 0;
2023-01-astaria/src/utils/Initializable.sol::234 => if (returndata.length == 0) {
2023-01-astaria/src/utils/Initializable.sol::268 => if (returndata.length > 0) {
2023-01-astaria/src/utils/MerkleProofLib.sol::14 => if proof.length {
2023-01-astaria/src/utils/MerkleProofLib.sol::16 => let end := add(proof.offset, shl(5, proof.length))
```
#### Tools used
Manual code review

### 2nd BUG
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
TestHelpers.t.sol::553 => if (revertMessage.length > 0) {
2023-01-astaria/src/utils/Initializable.sol::41 => return account.code.length > 0;
2023-01-astaria/src/utils/Initializable.sol::268 => if (returndata.length > 0) {
```
#### Tools used
Manual code review

### 3rd BUG 
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::63 => uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
2023-01-astaria/src/AuthInitializable.sol::27 => uint256(uint256(keccak256("xyz.astaria.Auth.storage.location")) - 1);
2023-01-astaria/src/ClearingHouse.sol::41 => uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
2023-01-astaria/src/CollateralToken.sol::74 => uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
2023-01-astaria/src/CollateralToken.sol::124 => s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
2023-01-astaria/src/CollateralToken.sol::310 => ) != keccak256("FlashAction.onFlashAction")
2023-01-astaria/src/CollateralToken.sol::519 => s.collateralIdToAuction[collateralId] = keccak256(
2023-01-astaria/src/LienToken.sol::51 => uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;
2023-01-astaria/src/LienToken.sol::228 => s.collateralStateHash[params.encumber.lien.collateralId] = keccak256(
2023-01-astaria/src/LienToken.sol::271 => if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
2023-01-astaria/src/LienToken.sol::405 => s.collateralStateHash[params.lien.collateralId] = keccak256(
2023-01-astaria/src/LienToken.sol::448 => newLienId = uint256(keccak256(abi.encode(params.lien)));
2023-01-astaria/src/LienToken.sol::563 => lienId = uint256(keccak256(abi.encode(lien)));
2023-01-astaria/src/LienToken.sol::698 => s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));
2023-01-astaria/src/PublicVault.sol::54 => uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
2023-01-astaria/src/VaultImplementation.sol::45 => keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
2023-01-astaria/src/VaultImplementation.sol::48 => keccak256(
2023-01-astaria/src/VaultImplementation.sol::51 => bytes32 constant VERSION = keccak256("0");
2023-01-astaria/src/VaultImplementation.sol::58 => uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
2023-01-astaria/src/VaultImplementation.sol::154 => keccak256(
2023-01-astaria/src/VaultImplementation.sol::183 => bytes32 hash = keccak256(
2023-01-astaria/src/VaultImplementation.sol::247 => keccak256(
2023-01-astaria/src/WithdrawProxy.sol::50 => uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
2023-01-astaria/src/actions/UNIV3/ClaimFees.sol::24 => keccak256("FlashAction.onFlashAction");
2023-01-astaria/src/libraries/CollateralLookup.sol::27 => hash := keccak256(12, 52) // keccak from the 12th byte up to the entire second memory slot.
::79 => //    bytes32 hash1 = keccak256(abi.encode(stack));
2023-01-astaria/src/security/V3SecurityHook.sol::44 => return keccak256(abi.encode(nonce, operator, liquidity));
2023-01-astaria/src/strategies/CollectionValidator.sol::74 => leaf = keccak256(params.nlrDetails);
2023-01-astaria/src/strategies/UNI_V3Validator.sol::155 => leaf = keccak256(params.nlrDetails);
2023-01-astaria/src/strategies/UniqueValidator.sol::77 => leaf = keccak256(params.nlrDetails);
TestHelpers.t.sol::732 => bytes32 hash = keccak256(
2023-01-astaria/src/utils/Initializable.sol::333 => uint256(keccak256("core.astaria.xyz.initializer.storage.location")) - 1
2023-01-astaria/src/utils/MerkleProofLib.sol::35 => leaf := keccak256(0, 64) // Hash both slots of scratch space.
2023-01-astaria/src/utils/Pausable.sol::21 => uint256(keccak256("xyz.astaria.AstariaRouter.Pausable.storage.location")) -
```
#### Tools used
Manual code review

### 4th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::63 => uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
2023-01-astaria/src/AuthInitializable.sol::27 => uint256(uint256(keccak256("xyz.astaria.Auth.storage.location")) - 1)
2023-01-astaria/src/ClearingHouse.sol::41 => uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
2023-01-astaria/src/CollateralToken.sol::74 => uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
2023-01-astaria/src/LienToken.sol::51 => uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;
2023-01-astaria/src/PublicVault.sol::54 => uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
2023-01-astaria/src/VaultImplementation.sol::45 => keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
2023-01-astaria/src/VaultImplementation.sol::49 => "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
2023-01-astaria/src/VaultImplementation.sol::58 => uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
2023-01-astaria/src/WithdrawProxy.sol::50 => uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
2023-01-astaria/src/utils/Initializable.sol::411 => require(s._initializing, "Initializable: contract is not initializing");
2023-01-astaria/src/utils/Initializable.sol::423 => require(!s._initializing, "Initializable: contract is initializing");
2023-01-astaria/src/utils/Pausable.sol::21 => uint256(keccak256("xyz.astaria.AstariaRouter.Pausable.storage.location")) -
```
#### Tools used
Manual code review

### 5th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::115 => s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
2023-01-astaria/src/interfaces/IAstariaRouter.sol::67 => ERC20 WETH; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::68 => ICollateralToken COLLATERAL_TOKEN; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::69 => ILienToken LIEN_TOKEN; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::70 => ITransferProxy TRANSFER_PROXY; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::71 => address feeTo; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::72 => address BEACON_PROXY_IMPLEMENTATION; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::76 => address guardian; //20
2023-01-astaria/src/interfaces/IAstariaRouter.sol::77 => address newGuardian; //20
2023-01-astaria/src/interfaces/ILienToken.sol::62 => address token; //20
2023-01-astaria/src/interfaces/ILienToken.sol::63 => address vault; //20
2023-01-astaria/src/scripts/setup/GoerliSetup.sol::63 => uint256 seaFee = basePayment / 40;
2023-01-astaria/src/scripts/setup/GoerliSetup.sol::64 => uint256 seaFeeEnd = basePaymentEnd / 40;
2023-01-astaria/src/utils/Math.sol::30 => return (a & b) + (a ^ b) / 2;
```
#### Tools used
Manual code review