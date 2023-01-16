## [L-01] Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

- [lib/gpl/src/ERC20-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L2) => `pragma solidity >=0.8.16;`
- [lib/gpl/src/ERC4626Router.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L2) => `pragma solidity ^0.8.17;`
- [lib/gpl/src/ERC4626RouterBase.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L2) => `pragma solidity ^0.8.17;`
- [lib/gpl/src/ERC721.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2) => `pragma solidity ^0.8.16;`
- [lib/gpl/src/interfaces/IERC4626Router.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626Router.sol#L2) => `pragma solidity ^0.8.17;`
- [lib/gpl/src/interfaces/IERC4626RouterBase.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L2) => `pragma solidity ^0.8.17;`
- [lib/gpl/src/interfaces/IWETH9.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol#L1) => `pragma solidity ^0.8.13;`


### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)



## [L-02] SPDX-License-Identifier missing

### Description

Missing license agreements (SPDX-License-Identifier) may result in lawsuits and improper forms of use of code.

### Findings

- [src/interfaces/IERC20Metadata.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC20Metadata.sol) => `The Solidity file is missing the SPDX-License-Identifier`
- [src/interfaces/IERC721.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC721.sol) => `The Solidity file is missing the SPDX-License-Identifier`
- [src/interfaces/IV3PositionManager.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol) => `The Solidity file is missing the SPDX-License-Identifier`
- [lib/gpl/src/interfaces/IWETH9.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol) => `The Solidity file is missing the SPDX-License-Identifier`

### References

- [Missing license identifier | UMA DVM 2.0 Audit](https://blog.openzeppelin.com/uma-dvm-2-0-audit/)


## [L-03] Unnamed return parameters

### Description

To increase explicitness and readability, take into account introducing and utilizing named return parameters.

### Findings

```Solidity
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol
::224 =>   function feeTo() public view returns (address) {
::229 =>   function BEACON_PROXY_IMPLEMENTATION() public view returns (address) {
::402 =>   function getAuctionWindow(bool includeBuffer) public view returns (uint256) {
::650 =>   function getProtocolFee(uint256 amountIn) external view returns (uint256) {
::657 =>   function getLiquidatorFee(uint256 amountIn) external view returns (uint256) {
::680 =>   function isValidVault(address vault) public view returns (bool) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol
::32 =>   function IMPL_TYPE() public pure returns (uint8) {
::36 =>   function owner() public pure returns (address) {
::50 =>   function START() public pure returns (uint256) {
::54 =>   function EPOCH_LENGTH() public pure returns (uint256) {
::58 =>   function VAULT_FEE() public pure returns (uint256) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol
::47 =>   function COLLATERAL_ID() public pure returns (uint256) {
::51 =>   function IMPL_TYPE() public pure returns (uint8) {
::78 =>   function supportsInterface(bytes4 interfaceId) external view returns (bool) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol
::370 =>   function getConduitKey() public view returns (bytes32) {
::375 =>   function getConduit() public view returns (address) {
::412 =>   function securityHooks(address target) public view returns (address) {

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol
::35 =>   function balanceOf(address account) external view returns (uint256) {
::52 =>   function transfer(address to, uint256 amount) public virtual returns (bool) {
::76 =>   function totalSupply() public view virtual returns (uint256) {
::154 =>   function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {
::158 =>   function computeDomainSeparator() internal view virtual returns (bytes32) {

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol
::129 =>   function previewMint(uint256 shares) public view virtual returns (uint256) {
::143 =>   function previewRedeem(uint256 shares) public view virtual returns (uint256) {
::147 =>   function maxDeposit(address) public view virtual returns (uint256) {
::151 =>   function maxMint(address) public view virtual returns (uint256) {
::155 =>   function maxWithdraw(address owner) public view virtual returns (uint256) {
::160 =>   function maxRedeem(address owner) public view virtual returns (uint256) {

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol
::26 =>   function getApproved(uint256 tokenId) public view returns (address) {
::59 =>   function balanceOf(address owner) public view virtual returns (uint256) {
::79 =>   function name() public view returns (string memory) {
::83 =>   function symbol() public view returns (string memory) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol
::246 =>   function getInterest(Stack calldata stack) public view returns (uint256) {
::385 =>   function _exists(uint256 tokenId) internal view returns (bool) {
::702 =>   function calculateSlope(Stack memory stack) public pure returns (uint256) {
::742 =>   function getOwed(Stack memory stack) external view returns (uint88) {
::893 =>   function getPayee(uint256 lienId) public view returns (address) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol
::196 =>   function getCurrentEpoch() public view returns (uint64) {
::200 =>   function getSlope() public view returns (uint256) {
::204 =>   function getWithdrawReserve() public view returns (uint256) {
::208 =>   function getLiquidationWithdrawRatio() public view returns (uint256) {
::212 =>   function getYIntercept() public view returns (uint256) {
::271 =>   function computeDomainSeparator() internal view override returns (bytes32) {
::461 =>   function accrue() public returns (uint256) {
::465 =>   function _accrue(VaultData storage s) internal returns (uint256) {
::490 =>   function _totalAssets(VaultData storage s) internal view returns (uint256) {
::546 =>   function getLienEpoch(uint64 end) public pure returns (uint64) {
::552 =>   function getEpochEnd(uint256 epoch) public pure returns (uint64) {
::699 =>   function timeToEpochEnd() public view returns (uint256) {
::703 =>   function timeToEpochEnd(uint256 epoch) public view returns (uint256) {
::722 =>   function timeToSecondEpochEnd() public view returns (uint256) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol
::60 =>   function getStrategistNonce() external view returns (uint256) {
::142 =>   function getShutdown() external view returns (bool) {
::152 =>   function domainSeparator() public view virtual returns (bytes32) {
::366 =>   function recipient() public view returns (address) {
::397 =>   function _handleProtocolFee(uint256 amount) internal returns (uint256) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol
::77 =>   function decimals() public pure override returns (uint8) {
::215 =>   function getFinalAuctionEnd() public view returns (uint256) {
::220 =>   function getWithdrawRatio() public view returns (uint256) {
::225 =>   function getExpected() public view returns (uint256) {

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol
::30 =>   function IMPL_TYPE() public pure override(IRouterBase) returns (uint8) {
::34 =>   function asset() public pure virtual override(IERC4626) returns (address) {
::38 =>   function VAULT() public pure returns (address) {
::42 =>   function CLAIMABLE_EPOCH() public pure returns (uint64) {
```

### References

- [Unnamed return parameters | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#unnamed-return-parameters)