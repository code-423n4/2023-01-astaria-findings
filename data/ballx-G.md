# c4udit Report

## Files analyzed
- src/AstariaRouter.sol
- src/AstariaVaultBase.sol
- src/AuthInitializable.sol
- src/BeaconProxy.sol
- src/ClearingHouse.sol
- src/CollateralToken.sol
- src/LienToken.sol
- src/PublicVault.sol
- src/TransferProxy.sol
- src/Vault.sol
- src/VaultImplementation.sol
- src/WithdrawProxy.sol
- src/WithdrawVaultBase.sol
- src/actions/UNIV3/ClaimFees.sol
- src/interfaces/IAstariaRouter.sol
- src/interfaces/IAstariaVaultBase.sol
- src/interfaces/IBeacon.sol
- src/interfaces/ICollateralToken.sol
- src/interfaces/IERC1155.sol
- src/interfaces/IERC1155Receiver.sol
- src/interfaces/IERC165.sol
- src/interfaces/IERC20.sol
- src/interfaces/IERC20Metadata.sol
- src/interfaces/IERC4626.sol
- src/interfaces/IERC721.sol
- src/interfaces/IERC721Receiver.sol
- src/interfaces/IFlashAction.sol
- src/interfaces/ILienToken.sol
- src/interfaces/IPublicVault.sol
- src/interfaces/IRouterBase.sol
- src/interfaces/ISecurityHook.sol
- src/interfaces/IStrategyValidator.sol
- src/interfaces/ITransferProxy.sol
- src/interfaces/IV3PositionManager.sol
- src/interfaces/IVaultImplementation.sol
- src/interfaces/IWithdrawProxy.sol
- src/libraries/Base64.sol
- src/libraries/CollateralLookup.sol
- src/scripts/Setup.sol
- src/scripts/deployments/AstariaStack.sol
- src/scripts/deployments/Deploy.sol
- src/scripts/deployments/strategies/CollectionStrategy.sol
- src/scripts/deployments/strategies/UniqueStrategy.sol
- src/scripts/deployments/strategies/V3Strategy.sol
- src/scripts/setup/GoerliSetup.sol
- src/security/V3SecurityHook.sol
- src/strategies/CollectionValidator.sol
- src/strategies/UNI_V3Validator.sol
- src/strategies/UniqueValidator.sol
- src/test/AstariaTest.t.sol
- src/test/ForkedTesting.t.sol
- src/test/IntegrationTest.t.sol
- src/test/RevertTesting.t.sol
- src/test/TestHelpers.t.sol
- src/test/WithdrawTesting.t.sol
- src/test/utils/Strings2.sol
- src/utils/Initializable.sol
- src/utils/Math.sol
- src/utils/MerkleProofLib.sol
- src/utils/Pausable.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
src/LienToken.sol::152 => uint256 potentialDebt = 0;
src/LienToken.sol::636 => uint256 remaining = 0;
src/WithdrawProxy.sol::254 => uint256 transferAmount = 0;
src/test/AstariaTest.t.sol::870 => for (uint8 i = 0; i < args.length; i++) {
src/test/IntegrationTest.t.sol::139 => for (uint160 i = 0; i < lienSize; i++) {
src/test/IntegrationTest.t.sol::195 => for (uint256 i = 0; i < lienSize; i++) {
src/test/TestHelpers.t.sol::92 => for (uint256 i = 0; i < size; ++i) {
src/test/TestHelpers.t.sol::968 => for (uint256 i = 0; i < lenders.length; i++) {
src/test/TestHelpers.t.sol::1260 => for (uint256 i = 0; i < _offerItems.length; i++) {
src/test/utils/Strings2.sol::13 => for (uint256 i = 0; i < length; ++i) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
src/AstariaRouter.sol::265 => for (; i < files.length; ) {
src/AstariaRouter.sol::364 => for (; i < file.length; ) {
src/AstariaRouter.sol::412 => uint256 length = bs.length;
src/AstariaRouter.sol::417 => if lt(length, end) {
src/AstariaRouter.sol::498 => lienIds = new uint256[](commitments.length);
src/AstariaRouter.sol::506 => for (; i < commitments.length; ) {
src/AstariaRouter.sol::707 => * @param epochLength The length of each epoch for a new PublicVault. If 0, deploys a PrivateVault.
src/ClearingHouse.sol::95 => output = new uint256[](accounts.length);
src/ClearingHouse.sol::96 => for (uint256 i; i < accounts.length; ) {
src/CollateralToken.sol::198 => for (; i < files.length; ) {
src/CollateralToken.sol::473 => totalOriginalConsiderationItems: considerationItems.length
src/LienToken.sol::153 => for (uint256 i = params.encumber.stack.length; i > 0; ) {
src/LienToken.sol::207 => uint256 n = newStack.length;
src/LienToken.sol::268 => if (stateHash == bytes32(0) && stack.length != 0) {
src/LienToken.sol::301 => auctionData.stack = new AuctionStack[](stack.length);
src/LienToken.sol::304 => for (; i < stack.length; ) {
src/LienToken.sol::412 => uint8(params.stack.length),
src/LienToken.sol::418 => uint8(params.stack.length),
src/LienToken.sol::420 => uint8(newStack.length)
src/LienToken.sol::438 => if (params.stack.length > 0) {
src/LienToken.sol::464 => if (stack.length >= s.maxLiens) {
src/LienToken.sol::468 => newStack = new Stack[](stack.length + 1);
src/LienToken.sol::469 => newStack[stack.length] = newSlot;
src/LienToken.sol::472 => for (uint256 i = stack.length; i > 0; ) {
src/LienToken.sol::491 => stack.length > 0 && potentialDebt > newSlot.lien.details.maxPotentialDebt
src/LienToken.sol::520 => for (; i < stack.length;) {
src/LienToken.sol::669 => for (uint256 i; i < newStack.length; ) {
src/LienToken.sol::670 => uint256 oldLength = newStack.length;
src/LienToken.sol::681 => if (newStack.length == oldLength) {
src/LienToken.sol::695 => if (stack.length == 0) {
src/LienToken.sol::712 => return _getMaxPotentialDebtForCollateralUpToNPositions(stack, stack.length);
src/LienToken.sol::734 => for (; i < stack.length; ) {
src/LienToken.sol::859 => uint256 length = stack.length;
src/LienToken.sol::860 => require(position < length);
src/LienToken.sol::861 => newStack = new ILienToken.Stack[](length - 1);
src/LienToken.sol::869 => for (; i < length - 1; ) {
src/LienToken.sol::879 => uint8(newStack.length)
src/VaultImplementation.sol::201 => for (; i < params.allowList.length; ) {
src/VaultImplementation.sol::302 => stack[stack.length - 1].point.end,
src/interfaces/IAstariaRouter.sol::141 => * @param epochLength The length of each epoch for the new PublicVault.
src/interfaces/IERC1155.sol::74 => * - `accounts` and `ids` must have the same length.
src/interfaces/IERC1155.sol::130 => * - `ids` and `amounts` must have the same length.
src/interfaces/IERC1155Receiver.sol::46 => * @param ids An array containing ids of each token being transferred (order and length must match values array)
src/interfaces/IERC1155Receiver.sol::47 => * @param values An array containing amounts of each token being transferred (order and length must match ids array)
src/libraries/Base64.sol::9 => uint256 len = data.length;
src/scripts/setup/GoerliSetup.sol::97 => //    // offer.length
src/test/AstariaTest.t.sol::870 => for (uint8 i = 0; i < args.length; i++) {
src/test/TestHelpers.t.sol::553 => if (revertMessage.length > 0) {
src/test/TestHelpers.t.sol::887 => emit log_named_uint("signature length", signature.length);
src/test/TestHelpers.t.sol::888 => if (signature.length == 65) {
src/test/TestHelpers.t.sol::968 => for (uint256 i = 0; i < lenders.length; i++) {
src/test/TestHelpers.t.sol::1144 => if (params.consideration.length == uint8(3)) {
src/test/TestHelpers.t.sol::1185 => emit log_named_uint("length", fulfillments.length);
src/test/TestHelpers.t.sol::1247 => _considerationItems.length
src/test/TestHelpers.t.sol::1258 => _considerationItems.length
src/test/TestHelpers.t.sol::1260 => for (uint256 i = 0; i < _offerItems.length; i++) {
src/test/TestHelpers.t.sol::1324 => require(vaults.length == lienDetails.length, "vaults not equal to liens");
src/test/TestHelpers.t.sol::1327 => memory commitments = new IAstariaRouter.Commitment[](vaults.length);
src/test/TestHelpers.t.sol::1329 => for (uint256 i; i < vaults.length; i++) {
src/test/utils/Strings2.sol::5 => require(input.length < type(uint256).max / 2 - 1);
src/test/utils/Strings2.sol::7 => bytes memory hex_buffer = new bytes(2 * input.length + 2);
src/test/utils/Strings2.sol::12 => uint256 length = input.length;
src/test/utils/Strings2.sol::13 => for (uint256 i = 0; i < length; ++i) {
src/utils/Initializable.sol::37 => // This method relies on extcodesize/address.code.length, which returns 0
src/utils/Initializable.sol::41 => return account.code.length > 0;
src/utils/Initializable.sol::234 => if (returndata.length == 0) {
src/utils/Initializable.sol::268 => if (returndata.length > 0) {
src/utils/MerkleProofLib.sol::14 => if proof.length {
src/utils/MerkleProofLib.sol::16 => let end := add(proof.offset, shl(5, proof.length))
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
src/AstariaRouter.sol::476 => if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd) {
src/ClearingHouse.sol::160 => if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
src/LienToken.sol::153 => for (uint256 i = params.encumber.stack.length; i > 0; ) {
src/LienToken.sol::438 => if (params.stack.length > 0) {
src/LienToken.sol::472 => for (uint256 i = stack.length; i > 0; ) {
src/LienToken.sol::491 => stack.length > 0 && potentialDebt > newSlot.lien.details.maxPotentialDebt
src/LienToken.sol::642 => if (payment > 0)
src/PublicVault.sol::277 => if (timeToEpochEnd() > 0) {
src/PublicVault.sol::282 => if (s.withdrawReserve > 0) {
src/PublicVault.sol::303 => if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0) {
src/PublicVault.sol::393 => s.withdrawReserve > 0 &&
src/PublicVault.sol::636 => if (params.remaining > 0)
src/WithdrawProxy.sol::280 => if (balance > 0) {
src/test/TestHelpers.t.sol::553 => if (revertMessage.length > 0) {
src/utils/Initializable.sol::41 => return account.code.length > 0;
src/utils/Initializable.sol::268 => if (returndata.length > 0) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
src/AstariaRouter.sol::63 => uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
src/AuthInitializable.sol::27 => uint256(uint256(keccak256("xyz.astaria.Auth.storage.location")) - 1);
src/ClearingHouse.sol::41 => uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
src/CollateralToken.sol::74 => uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
src/CollateralToken.sol::124 => s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
src/CollateralToken.sol::310 => ) != keccak256("FlashAction.onFlashAction")
src/CollateralToken.sol::519 => s.collateralIdToAuction[collateralId] = keccak256(
src/LienToken.sol::51 => uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;
src/LienToken.sol::228 => s.collateralStateHash[params.encumber.lien.collateralId] = keccak256(
src/LienToken.sol::271 => if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
src/LienToken.sol::405 => s.collateralStateHash[params.lien.collateralId] = keccak256(
src/LienToken.sol::448 => newLienId = uint256(keccak256(abi.encode(params.lien)));
src/LienToken.sol::563 => lienId = uint256(keccak256(abi.encode(lien)));
src/LienToken.sol::698 => s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));
src/PublicVault.sol::54 => uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
src/VaultImplementation.sol::45 => keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
src/VaultImplementation.sol::48 => keccak256(
src/VaultImplementation.sol::51 => bytes32 constant VERSION = keccak256("0");
src/VaultImplementation.sol::58 => uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
src/VaultImplementation.sol::154 => keccak256(
src/VaultImplementation.sol::183 => bytes32 hash = keccak256(
src/VaultImplementation.sol::247 => keccak256(
src/WithdrawProxy.sol::50 => uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
src/actions/UNIV3/ClaimFees.sol::24 => keccak256("FlashAction.onFlashAction");
src/interfaces/IERC1155Receiver.sol::17 => * `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
src/interfaces/IERC1155Receiver.sol::25 => * @return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` if transfer is allowed
src/interfaces/IERC1155Receiver.sol::41 => * `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
src/interfaces/IERC1155Receiver.sol::49 => * @return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` if transfer is allowed
src/libraries/CollateralLookup.sol::27 => hash := keccak256(12, 52) // keccak from the 12th byte up to the entire second memory slot.
src/scripts/Setup.sol::79 => //    bytes32 hash1 = keccak256(abi.encode(stack));
src/security/V3SecurityHook.sol::44 => return keccak256(abi.encode(nonce, operator, liquidity));
src/strategies/CollectionValidator.sol::74 => leaf = keccak256(params.nlrDetails);
src/strategies/UNI_V3Validator.sol::155 => leaf = keccak256(params.nlrDetails);
src/strategies/UniqueValidator.sol::77 => leaf = keccak256(params.nlrDetails);
src/test/TestHelpers.t.sol::732 => bytes32 hash = keccak256(
src/utils/Initializable.sol::333 => uint256(keccak256("core.astaria.xyz.initializer.storage.location")) - 1
src/utils/MerkleProofLib.sol::35 => leaf := keccak256(0, 64) // Hash both slots of scratch space.
src/utils/Pausable.sol::21 => uint256(keccak256("xyz.astaria.AstariaRouter.Pausable.storage.location")) -
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
src/AstariaRouter.sol::18 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/AstariaRouter.sol::19 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/AstariaRouter.sol::21 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/AstariaRouter.sol::26 => } from "clones-with-immutable-args/ClonesWithImmutableArgs.sol";
src/AstariaRouter.sol::28 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/AstariaRouter.sol::30 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/AstariaRouter.sol::31 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/AstariaRouter.sol::33 => import {IVaultImplementation} from "core/interfaces/IVaultImplementation.sol";
src/AstariaRouter.sol::34 => import {IAstariaVaultBase} from "core/interfaces/IAstariaVaultBase.sol";
src/AstariaRouter.sol::35 => import {IStrategyValidator} from "core/interfaces/IStrategyValidator.sol";
src/AstariaRouter.sol::42 => import {OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
src/AstariaRouter.sol::63 => uint256(keccak256("xyz.astaria.AstariaRouter.storage.location")) - 1;
src/AstariaVaultBase.sol::16 => import {IAstariaVaultBase} from "core/interfaces/IAstariaVaultBase.sol";
src/AstariaVaultBase.sol::17 => import {Clone} from "clones-with-immutable-args/Clone.sol";
src/AstariaVaultBase.sol::19 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/AstariaVaultBase.sol::20 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/AuthInitializable.sol::27 => uint256(uint256(keccak256("xyz.astaria.Auth.storage.location")) - 1);
src/BeaconProxy.sol::17 => import {Clone} from "clones-with-immutable-args/Clone.sol";
src/ClearingHouse.sol::16 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/ClearingHouse.sol::19 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/ClearingHouse.sol::20 => import {Clone} from "clones-with-immutable-args/Clone.sol";
src/ClearingHouse.sol::23 => import {Bytes32AddressLib} from "solmate/utils/Bytes32AddressLib.sol";
src/ClearingHouse.sol::26 => } from "seaport/interfaces/ConduitControllerInterface.sol";
src/ClearingHouse.sol::28 => import {Order} from "seaport/lib/ConsiderationStructs.sol";
src/ClearingHouse.sol::29 => import {IERC721Receiver} from "core/interfaces/IERC721Receiver.sol";
src/ClearingHouse.sol::41 => uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
src/CollateralToken.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/CollateralToken.sol::19 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/CollateralToken.sol::22 => import {IERC721Receiver} from "core/interfaces/IERC721Receiver.sol";
src/CollateralToken.sol::25 => import {ISecurityHook} from "core/interfaces/ISecurityHook.sol";
src/CollateralToken.sol::26 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/CollateralToken.sol::28 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/CollateralToken.sol::31 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/CollateralToken.sol::32 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/CollateralToken.sol::34 => import {ZoneInterface} from "seaport/interfaces/ZoneInterface.sol";
src/CollateralToken.sol::35 => import {Bytes32AddressLib} from "solmate/utils/Bytes32AddressLib.sol";
src/CollateralToken.sol::38 => } from "clones-with-immutable-args/ClonesWithImmutableArgs.sol";
src/CollateralToken.sol::42 => } from "seaport/interfaces/ConduitControllerInterface.sol";
src/CollateralToken.sol::45 => } from "seaport/interfaces/ConsiderationInterface.sol";
src/CollateralToken.sol::56 => } from "seaport/lib/ConsiderationStructs.sol";
src/CollateralToken.sol::59 => import {SeaportInterface} from "seaport/interfaces/SeaportInterface.sol";
src/CollateralToken.sol::74 => uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
src/LienToken.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/LienToken.sol::24 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/LienToken.sol::27 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/LienToken.sol::29 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/LienToken.sol::30 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/LienToken.sol::36 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/LienToken.sol::51 => uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;
src/PublicVault.sol::18 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/PublicVault.sol::19 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/PublicVault.sol::26 => import {IERC20Metadata} from "core/interfaces/IERC20Metadata.sol";
src/PublicVault.sol::30 => } from "clones-with-immutable-args/ClonesWithImmutableArgs.sol";
src/PublicVault.sol::32 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/PublicVault.sol::40 => import {IAstariaVaultBase} from "core/interfaces/IAstariaVaultBase.sol";
src/PublicVault.sol::54 => uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
src/TransferProxy.sol::17 => import {SafeTransferLib, ERC20} from "solmate/utils/SafeTransferLib.sol";
src/TransferProxy.sol::19 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/Vault.sol::17 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/VaultImplementation.sol::18 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/VaultImplementation.sol::19 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/VaultImplementation.sol::20 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/VaultImplementation.sol::22 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/VaultImplementation.sol::27 => import {IVaultImplementation} from "core/interfaces/IVaultImplementation.sol";
src/VaultImplementation.sol::45 => keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
src/VaultImplementation.sol::49 => "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
src/VaultImplementation.sol::58 => uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
src/WithdrawProxy.sol::17 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/WithdrawProxy.sol::18 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/WithdrawProxy.sol::22 => import {IWithdrawProxy} from "core/interfaces/IWithdrawProxy.sol";
src/WithdrawProxy.sol::24 => import {IERC20Metadata} from "core/interfaces/IERC20Metadata.sol";
src/WithdrawProxy.sol::50 => uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
src/WithdrawVaultBase.sol::16 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/WithdrawVaultBase.sol::17 => import {IWithdrawProxy} from "core/interfaces/IWithdrawProxy.sol";
src/WithdrawVaultBase.sol::19 => import {Clone} from "clones-with-immutable-args/Clone.sol";
src/actions/UNIV3/ClaimFees.sol::17 => import {IV3PositionManager} from "core/interfaces/IV3PositionManager.sol";
src/actions/UNIV3/ClaimFees.sol::19 => import {IERC721Receiver} from "core/interfaces/IERC721Receiver.sol";
src/interfaces/IAstariaRouter.sol::17 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/interfaces/IAstariaRouter.sol::20 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/interfaces/IAstariaRouter.sol::25 => import {IERC4626RouterBase} from "gpl/interfaces/IERC4626RouterBase.sol";
src/interfaces/IAstariaRouter.sol::26 => import {OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
src/interfaces/IAstariaVaultBase.sol::16 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/interfaces/IAstariaVaultBase.sol::17 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/ICollateralToken.sol::17 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/interfaces/ICollateralToken.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/ICollateralToken.sol::23 => } from "seaport/interfaces/ConsiderationInterface.sol";
src/interfaces/ICollateralToken.sol::26 => } from "seaport/interfaces/ConduitControllerInterface.sol";
src/interfaces/ICollateralToken.sol::28 => import {Order, OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
src/interfaces/IERC1155Receiver.sol::17 => * `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))`
src/interfaces/IERC1155Receiver.sol::25 => * @return `bytes4(keccak256("onERC1155Received(address,address,uint256,uint256,bytes)"))` if transfer is allowed
src/interfaces/IERC1155Receiver.sol::41 => * `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))`
src/interfaces/IERC1155Receiver.sol::49 => * @return `bytes4(keccak256("onERC1155BatchReceived(address,address,uint256[],uint256[],bytes)"))` if transfer is allowed
src/interfaces/IERC4626.sol::6 => import {IERC20Metadata} from "core/interfaces/IERC20Metadata.sol";
src/interfaces/ILienToken.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/ILienToken.sol::19 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/interfaces/ILienToken.sol::20 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/interfaces/IPublicVault.sol::17 => import {IVaultImplementation} from "core/interfaces/IVaultImplementation.sol";
src/interfaces/IRouterBase.sol::16 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/IStrategyValidator.sol::16 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/IStrategyValidator.sol::17 => import {ILienToken} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/IVaultImplementation.sol::17 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/interfaces/IVaultImplementation.sol::18 => import {IAstariaVaultBase} from "core/interfaces/IAstariaVaultBase.sol";
src/interfaces/IWithdrawProxy.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/libraries/Base64.sol::5 => "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
src/scripts/Setup.sol::18 => import {AstariaStack} from "core/scripts/deployments/AstariaStack.sol";
src/scripts/Setup.sol::19 => import {MockERC20} from "solmate/test/utils/mocks/MockERC20.sol";
src/scripts/deployments/AstariaStack.sol::20 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/scripts/deployments/Deploy.sol::21 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/scripts/deployments/Deploy.sol::25 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/scripts/deployments/Deploy.sol::34 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/scripts/deployments/Deploy.sol::35 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/scripts/deployments/Deploy.sol::42 => } from "seaport/interfaces/ConsiderationInterface.sol";
src/scripts/deployments/Deploy.sol::45 => } from "lib/seaport/lib/openzeppelin-contracts/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";
src/scripts/deployments/Deploy.sol::48 => } from "lib/seaport/lib/openzeppelin-contracts/contracts/proxy/transparent/ProxyAdmin.sol";
src/scripts/deployments/Deploy.sol::51 => } from "lib/seaport/lib/openzeppelin-contracts/contracts/proxy/utils/Initializable.sol";
src/scripts/deployments/Deploy.sol::178 => //      vm.setEnv("TRANSPARENT_UPGRADEABLE_PROXY_ADDR", address(transparentUpgradeableProxy));
src/scripts/deployments/Deploy.sol::233 => //      vm.setEnv("TRANSPARENT_UPGRADEABLE_PROXY_ADDR", address(transparentUpgradeableProxy));
src/scripts/deployments/Deploy.sol::264 => "PUBLIC_VAULT_IMPLEMENTATION_ADDR=",
src/scripts/deployments/strategies/CollectionStrategy.sol::17 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/scripts/deployments/strategies/CollectionStrategy.sol::18 => import {CollectionValidator} from "core/strategies/CollectionValidator.sol";
src/scripts/deployments/strategies/UniqueStrategy.sol::17 => import {UniqueValidator} from "../../../strategies/UniqueValidator.sol";
src/scripts/deployments/strategies/UniqueStrategy.sol::19 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/scripts/deployments/strategies/V3Strategy.sol::18 => import {UNI_V3Validator} from "core/strategies/UNI_V3Validator.sol";
src/scripts/deployments/strategies/V3Strategy.sol::21 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/scripts/deployments/strategies/V3Strategy.sol::22 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/scripts/setup/GoerliSetup.sol::28 => } from "seaport/lib/ConsiderationStructs.sol";
src/security/V3SecurityHook.sol::15 => import {IV3PositionManager} from "core/interfaces/IV3PositionManager.sol";
src/security/V3SecurityHook.sol::16 => import {ISecurityHook} from "core/interfaces/ISecurityHook.sol";
src/strategies/CollectionValidator.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/strategies/CollectionValidator.sol::20 => import {IStrategyValidator} from "core/interfaces/IStrategyValidator.sol";
src/strategies/CollectionValidator.sol::69 => "invalid borrower requesting commitment"
src/strategies/UNI_V3Validator.sol::18 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/strategies/UNI_V3Validator.sol::19 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/strategies/UNI_V3Validator.sol::21 => import {IStrategyValidator} from "core/interfaces/IStrategyValidator.sol";
src/strategies/UNI_V3Validator.sol::22 => import {IV3PositionManager} from "core/interfaces/IV3PositionManager.sol";
src/strategies/UNI_V3Validator.sol::23 => import {IUniswapV3Factory} from "gpl/interfaces/IUniswapV3Factory.sol";
src/strategies/UNI_V3Validator.sol::24 => import {IUniswapV3PoolState} from "gpl/interfaces/IUniswapV3PoolState.sol";
src/strategies/UniqueValidator.sol::18 => import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";
src/strategies/UniqueValidator.sol::20 => import {IStrategyValidator} from "core/interfaces/IStrategyValidator.sol";
src/strategies/UniqueValidator.sol::70 => "invalid borrower requesting commitment"
src/test/AstariaTest.t.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/AstariaTest.t.sol::20 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/AstariaTest.t.sol::23 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/AstariaTest.t.sol::37 => import {OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
src/test/AstariaTest.t.sol::939 => "WithdrawProxy not deployed inside 3 days window from epoch end"
src/test/AstariaTest.t.sol::944 => "Auction time is not being set correctly"
src/test/AstariaTest.t.sol::1115 => "WithdrawProxy balance not correct"
src/test/ForkedTesting.t.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/ForkedTesting.t.sol::20 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/ForkedTesting.t.sol::23 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/ForkedTesting.t.sol::27 => } from "openzeppelin/token/ERC1155/IERC1155Receiver.sol";
src/test/ForkedTesting.t.sol::31 => import {IV3PositionManager} from "core/interfaces/IV3PositionManager.sol";
src/test/ForkedTesting.t.sol::33 => import {ICollateralToken} from "../interfaces/ICollateralToken.sol";
src/test/ForkedTesting.t.sol::39 => import {IVaultImplementation} from "../interfaces/IVaultImplementation.sol";
src/test/IntegrationTest.t.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/IntegrationTest.t.sol::20 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/IntegrationTest.t.sol::23 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/RevertTesting.t.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/RevertTesting.t.sol::20 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/RevertTesting.t.sol::23 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/RevertTesting.t.sol::27 => } from "openzeppelin/token/ERC1155/IERC1155Receiver.sol";
src/test/RevertTesting.t.sol::32 => import {ICollateralToken} from "../interfaces/ICollateralToken.sol";
src/test/RevertTesting.t.sol::38 => import {IVaultImplementation} from "../interfaces/IVaultImplementation.sol";
src/test/RevertTesting.t.sol::81 => "vault was incremented, when it shouldn't be"
src/test/TestHelpers.t.sol::20 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/TestHelpers.t.sol::21 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/TestHelpers.t.sol::24 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/TestHelpers.t.sol::25 => import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
src/test/TestHelpers.t.sol::28 => import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
src/test/TestHelpers.t.sol::31 => import {ICollateralToken} from "core/interfaces/ICollateralToken.sol";
src/test/TestHelpers.t.sol::34 => import {IStrategyValidator} from "core/interfaces/IStrategyValidator.sol";
src/test/TestHelpers.t.sol::35 => import {CollateralLookup} from "core/libraries/CollateralLookup.sol";
src/test/TestHelpers.t.sol::38 => } from "seaport/interfaces/ConduitControllerInterface.sol";
src/test/TestHelpers.t.sol::42 => } from "../strategies/CollectionValidator.sol";
src/test/TestHelpers.t.sol::46 => } from "../strategies/UNI_V3Validator.sol";
src/test/TestHelpers.t.sol::50 => } from "../strategies/UniqueValidator.sol";
src/test/TestHelpers.t.sol::63 => import {Bytes32AddressLib} from "solmate/utils/Bytes32AddressLib.sol";
src/test/TestHelpers.t.sol::64 => import {Deploy} from "core/scripts/deployments/Deploy.sol";
src/test/TestHelpers.t.sol::69 => } from "seaport/interfaces/ConsiderationInterface.sol";
src/test/TestHelpers.t.sol::81 => } from "seaport/lib/ConsiderationStructs.sol";
src/test/TestHelpers.t.sol::83 => import {ConduitController} from "seaport/conduit/ConduitController.sol";
src/test/TestHelpers.t.sol::97 => import {BaseOrderTest} from "lib/seaport/test/foundry/utils/BaseOrderTest.sol";
src/test/TestHelpers.t.sol::494 => inputs[1] = "./scripts/loan-proof-generator.js";
src/test/TestHelpers.t.sol::1307 => "Incorrect number of WithdrawTokens minted"
src/test/WithdrawTesting.t.sol::19 => import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
src/test/WithdrawTesting.t.sol::20 => import {MockERC721} from "solmate/test/utils/mocks/MockERC721.sol";
src/test/WithdrawTesting.t.sol::23 => } from "solmate/auth/authorities/MultiRolesAuthority.sol";
src/test/WithdrawTesting.t.sol::38 => import {OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
src/test/WithdrawTesting.t.sol::303 => "First lien not pointing to first WithdrawProxy"
src/test/WithdrawTesting.t.sol::323 => "Second lien not pointing to second WithdrawProxy"
src/test/WithdrawTesting.t.sol::356 => "WithdrawProxy 0 should have 0 assets"
src/test/WithdrawTesting.t.sol::361 => "WithdrawProxy 1 should have 0 assets"
src/test/WithdrawTesting.t.sol::455 => "Epoch 0 withdrawReserve calculation incorrect"
src/test/WithdrawTesting.t.sol::475 => "PublicVault yIntercept calculation incorrect"
src/test/WithdrawTesting.t.sol::496 => "withdrawReserve should be 0 after transfer"
src/test/WithdrawTesting.t.sol::502 => "PublicVault yIntercept calculation incorrect"
src/test/WithdrawTesting.t.sol::648 => "PublicVault slope after epoch 0 should be 0"
src/test/WithdrawTesting.t.sol::653 => "Incorrect PublicVault withdrawReserve calculation after epoch 0"
src/test/WithdrawTesting.t.sol::658 => "Incorrect PublicVault withdrawRatio calculation after epoch 0"
src/test/WithdrawTesting.t.sol::721 => "PublicVault slope should be 0 after epoch 0"
src/test/WithdrawTesting.t.sol::726 => "VaultToken balance should have been burned after epoch 0"
src/test/WithdrawTesting.t.sol::731 => "PublicVault withdrawReserve calculation after epoch 0 incorrect"
src/test/WithdrawTesting.t.sol::736 => "PublicVault yIntercept after epoch 0 should be 0"
src/test/WithdrawTesting.t.sol::784 => "incorrect PublicVault yIntercept calculation after lien 2 repayment"
src/test/WithdrawTesting.t.sol::789 => "incorrect PublicVault totalAssets() after lien 2 repayment"
src/test/WithdrawTesting.t.sol::794 => "PublicVault slope should be 0 after second lien repayment"
src/test/WithdrawTesting.t.sol::799 => "PublicVault liquidationWithdrawRatio should be 0"
src/test/WithdrawTesting.t.sol::808 => "Incorrect PublicVault withdrawReserve calculation after epoch 1"
src/test/WithdrawTesting.t.sol::898 => "Incorrect PublicVault totalAssets()"
src/utils/Initializable.sol::66 => "Address: unable to send value, recipient may have reverted"
src/utils/Initializable.sol::131 => "Address: low-level call with value failed"
src/utils/Initializable.sol::149 => "Address: insufficient balance for call"
src/utils/Initializable.sol::168 => functionStaticCall(target, data, "Address: low-level static call failed");
src/utils/Initializable.sol::201 => "Address: low-level delegate call failed"
src/utils/Initializable.sol::333 => uint256(keccak256("core.astaria.xyz.initializer.storage.location")) - 1
src/utils/Initializable.sol::367 => "Initializable: contract is already initialized"
src/utils/Initializable.sol::396 => "Initializable: contract is already initialized"
src/utils/Initializable.sol::411 => require(s._initializing, "Initializable: contract is not initializing");
src/utils/Initializable.sol::423 => require(!s._initializing, "Initializable: contract is initializing");
src/utils/Pausable.sol::21 => uint256(keccak256("xyz.astaria.AstariaRouter.Pausable.storage.location")) -
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
src/AstariaRouter.sol::115 => s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
src/AstariaRouter.sol::116 => //63419583966; // 200% apy / second
src/interfaces/IAstariaRouter.sol::67 => ERC20 WETH; //20
src/interfaces/IAstariaRouter.sol::68 => ICollateralToken COLLATERAL_TOKEN; //20
src/interfaces/IAstariaRouter.sol::69 => ILienToken LIEN_TOKEN; //20
src/interfaces/IAstariaRouter.sol::70 => ITransferProxy TRANSFER_PROXY; //20
src/interfaces/IAstariaRouter.sol::71 => address feeTo; //20
src/interfaces/IAstariaRouter.sol::72 => address BEACON_PROXY_IMPLEMENTATION; //20
src/interfaces/IAstariaRouter.sol::76 => address guardian; //20
src/interfaces/IAstariaRouter.sol::77 => address newGuardian; //20
src/interfaces/IERC20.sol::65 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
src/interfaces/IERC4626.sol::88 => * - MUST return 2 ** 256 - 1 if there is no limit on the maximum amount of assets that may be deposited.
src/interfaces/IERC4626.sol::134 => * - MUST return 2 ** 256 - 1 if there is no limit on the maximum amount of shares that may be minted.
src/interfaces/ILienToken.sol::62 => address token; //20
src/interfaces/ILienToken.sol::63 => address vault; //20
src/scripts/setup/GoerliSetup.sol::63 => uint256 seaFee = basePayment / 40;
src/scripts/setup/GoerliSetup.sol::64 => uint256 seaFeeEnd = basePaymentEnd / 40;
src/test/AstariaTest.t.sol::892 => input.borrowAmount * 2
src/test/AstariaTest.t.sol::1105 => garbage.bidderBalance + (garbage.bid * 2) - garbage.actualPrice,
src/test/IntegrationTest.t.sol::157 => maxPotentialDebt: i * 20 ether,
src/test/IntegrationTest.t.sol::201 => assertTrue(withdrawProxies[1] != address(0)); // 2 days from epoch end
src/test/TestHelpers.t.sol::981 => WETH9.deposit{value: amount * 2}();
src/test/TestHelpers.t.sol::982 => WETH9.approve(address(TRANSFER_PROXY), amount * 2);
src/test/TestHelpers.t.sol::983 => WETH9.approve(address(LIEN_TOKEN), amount * 2);
src/test/TestHelpers.t.sol::1055 => WETH9.deposit{value: bidAmount * 2}();
src/test/TestHelpers.t.sol::1056 => WETH9.approve(bidderConduits[incomingBidder.bidder].conduit, bidAmount * 2);
src/test/utils/Strings2.sol::5 => require(input.length < type(uint256).max / 2 - 1);
src/utils/Initializable.sol::53 => * https://diligence.consensys.net/posts/2019/09/stop-using-soliditys-transfer-now/[Learn more].
src/utils/Math.sol::29 => // (a + b) / 2 can overflow.
src/utils/Math.sol::30 => return (a & b) + (a ^ b) / 2;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
src/ClearingHouse.sol::203 => ERC721(order.parameters.offer[0].token).approve(
src/LienToken.sol::374 => super.transferFrom(from, to, id);
src/actions/UNIV3/ClaimFees.sol::52 => ERC721(asset.token).transferFrom(
src/scripts/Setup.sol::47 => //    astariaWETH.approve(TRANSFER_PROXY_ADDR, type(uint256).max);
src/test/ForkedTesting.t.sol::60 => ERC721(nft).transferFrom(holder, address(this), tokenId);
src/test/TestHelpers.t.sol::419 => TestNFT(tokenContract).approve(address(ASTARIA_ROUTER), tokenId);
src/test/TestHelpers.t.sol::429 => TestNFT(tokenContract).approve(address(ASTARIA_ROUTER), tokenId);
src/test/TestHelpers.t.sol::937 => WETH9.approve(address(TRANSFER_PROXY), lender.amountToLend);
src/test/TestHelpers.t.sol::952 => WETH9.approve(vault, lender.amountToLend);
src/test/TestHelpers.t.sol::982 => WETH9.approve(address(TRANSFER_PROXY), amount * 2);
src/test/TestHelpers.t.sol::983 => WETH9.approve(address(LIEN_TOKEN), amount * 2);
src/test/TestHelpers.t.sol::1003 => WETH9.approve(address(TRANSFER_PROXY), amount);
src/test/TestHelpers.t.sol::1004 => WETH9.approve(address(LIEN_TOKEN), amount);
src/test/TestHelpers.t.sol::1056 => WETH9.approve(bidderConduits[incomingBidder.bidder].conduit, bidAmount * 2);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
src/scripts/Setup.sol::14 => pragma solidity ^0.8.17;
src/scripts/setup/GoerliSetup.sol::14 => pragma solidity ^0.8.17;
src/utils/Initializable.sol::4 => pragma solidity ^0.8.1;
src/utils/MerkleProofLib.sol::2 => pragma solidity >=0.8.0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
src/ClearingHouse.sol::148 => ERC20(paymentToken).safeApprove(
src/VaultImplementation.sol::334 => ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
src/test/TestHelpers.t.sol::1293 => ERC20(publicVault).safeApprove(address(ASTARIA_ROUTER), vaultTokenBalance);
src/test/TestHelpers.t.sol::1309 => ERC20(address(withdrawProxy)).safeApprove(address(this), type(uint256).max);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

