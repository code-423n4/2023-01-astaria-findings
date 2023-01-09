## Gas

### 1. Cache array length outside of loop

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

The following should be refactored:

```
./src/AstariaRouter.sol:265:    for (; i < files.length; ) {
./src/AstariaRouter.sol:364:    for (; i < file.length; ) {
./src/AstariaRouter.sol:506:    for (; i < commitments.length; ) {
./src/LienToken.sol:153:    for (uint256 i = params.encumber.stack.length; i > 0; ) {
./src/LienToken.sol:304:    for (; i < stack.length; ) {
./src/LienToken.sol:472:    for (uint256 i = stack.length; i > 0; ) {
./src/LienToken.sol:520:    for (; i < stack.length;) {
./src/LienToken.sol:669:    for (uint256 i; i < newStack.length; ) {
./src/LienToken.sol:734:    for (; i < stack.length; ) {
./src/ClearingHouse.sol:96:    for (uint256 i; i < accounts.length; ) {
./src/VaultImplementation.sol:201:      for (; i < params.allowList.length; ) {
./src/CollateralToken.sol:198:    for (; i < files.length; ) {
```

Tool used: `grep -rn ".length" .`

### 2. Use functions instead of modifiers

The following should be refactored in functions like in this example:

```
modifier onlyVault() {  
  require(msg.sender == VAULT(), "only vault can call");  
  _;  
}

function onlyVault() private view {
	require(msg.sender == VAULT(), "only vault can call");
}
```

and then it's usage should become: 

```
function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {  
  _loadSlot().withdrawRatio = liquidationWithdrawRatio.safeCastTo88();  
}

function setWithdrawRatio(uint256 liquidationWithdrawRatio) public {  
  onlyVault();
  _loadSlot().withdrawRatio = liquidationWithdrawRatio.safeCastTo88();  
}
```

This should be refactored:

```
src/AstariaRouter.sol:196:  modifier validVault(address targetVault) {
src/LienToken.sol:265:  modifier validateStack(uint256 collateralId, Stack[] memory stack) {
src/WithdrawProxy.sol:152:  modifier onlyWhenNoActiveAuction() {
src/WithdrawProxy.sol:230:  modifier onlyVault() {
src/PublicVault.sol:679:  modifier onlyLienToken() {
src/AuthInitializable.sol:51:  modifier requiresAuth() virtual {
src/VaultImplementation.sol:131:  modifier whenNotPaused() {
src/CollateralToken.sol:253:  modifier releaseCheck(uint256 collateralId) {
src/CollateralToken.sol:265:  modifier onlyOwner(uint256 collateralId) {
src/CollateralToken.sol:602:  modifier whenNotPaused() {
```

Tool used: `grep -rn "modifier" src`