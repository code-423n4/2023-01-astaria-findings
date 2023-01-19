1.  No need for Pragma Experimental ABIEncoderv2

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L16

The ABI coder v2 is activated by default in solidity 0.8 and above. Therefore there is no need declaring it in the contract. As seen in this doc, https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html, infact it should be pragma abicoder v2 if there is need to declare it explicitly instead of pragma experimental ABIEncoderV2 which has deprecated.

2. Incorrect Natspec

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L74

It should be "@dev Receive function that delegates calls to the address returned by `_implementation()`. Will run if call data
   * is empty" and not "@dev Fallback function that delegates calls to the address returned by `_implementation()`. Will run if call data
   * is empty"

3. Discrepancies in code and documentation

In the documentation, it says "PrivateVaults may be opened by any user and funded with that user's own capital. Borrowers can then take out loans on NFTs supported by the PrivateVault strategy" but the code says otherwise of which only allowlisted user can open the vault

References

Doc:  https://docs.astaria.xyz/docs/smart-contracts/privatecvault

Code:  https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65



