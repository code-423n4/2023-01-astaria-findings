### [L-01] Functions in PublicVault shadow `owner` variable.

Since PublicVault is derived from `VaultImplementation` which is derived from `AstariaVaultBase`
almost all function shadow `AstariaVaultBase.owner` variable which can lead to unexpected behavior. To avoid this behavior better to use `_owner` in function params

### [L-02] Last Solc version is not recommended for deployment

solc-0.8.17 is not recommended for deployment since it can have unknown bugs.

Better to deploy using solc-0.8.16

### [QA-01] Merging functions with similar functionality

It’s also reduce contract size and saves some deployment gas

```solidity
file: VaultImplementation.sol

function disableAllowList() external virtual 
function enableAllowList() external virtual 

After Merging 

function enableAllowList(bool var) external virtual {
                              ^ true of false
    ...
    _loadVISlot().allowListEnabled = var;
    emit AllowListEnabled(var);
}
```

### [QA-02] Unnecessary function declaration

Since VaultImplementation is an abstract contract and is derived from AstariaVaultBase where name() and symbol() are already declared no need to declare them again. 

```solidity
file: VaultImplementation.sol

function name() external view virtual override returns (string memory);
function symbol() external view virtual override returns (string memory);
```

### [QA-03] Unused local variable

Also it will saves gas on `deposit()` call.

```solidity
file: PublicVault.sol

function deposit(uint256 amount, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[receiver]);
    }

    uint256 assets = totalAssets(); // <- Unused local variable

    return super.deposit(amount, receiver);
  }
```

### [QA-04] Unnecessary non generic function implementation

Function `disableAllowList()` `enableAllowList()` and `modifyAllowList()` is not generic and should not be implemented in Vault. This function is only for Public Vault so they should be removed from `VaultImplementation` since it’s a generic abstract contact for vaults. Declare this function in `IPublicVault` and implement it only in `PublicVault`. It also reduces contract size and saves some deployment gas.

```solidity
file: Vault.sol
function disableAllowList() external pure override(VaultImplementation) {
function enableAllowList() external pure override(VaultImplementation) {
function modifyAllowList(address, bool) external pure override(VaultImplementation) {
```

### [QA-05] Unused function params

`params` passed to `_beforeCommitToLien` never used in VaultImplementation or in overridden functions in derived contracts. 

```solidity
file: VaultImplementation.sol
function _beforeCommitToLien(IAstariaRouter.Commitment calldata params)
```

### [QA-06] Code consistency: 0 & uint256(0)

No need to explicitly convert 0 to uint256. This issue is found everywhere in the code

```solidity
file: PublicVault.sol
if (timeToEpochEnd() > 0) {
if (timeToEpochEnd() == uint256(0)) {

if (s.withdrawReserve > 0) {
if (s.withdrawReserve > uint256(0)) {
```

### [QA-06] Redundant expression

```solidity
file: LienToken.sol

uint256 i;
for (i; i < n; ) {
```

### [QA-07] Missing Inheritance

`ClearingHouse` should inherit from `IRouterBase` since it implement `ROUTER()` and `IMPL_TYPE()` that declared in `IRouterBase` 

```solidity
file: ClearingHouse.sol

function ROUTER() external view returns (IAstariaRouter);
function IMPL_TYPE() external view returns (uint8);
```