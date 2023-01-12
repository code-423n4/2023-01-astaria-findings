# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Signature malleability for `ecrecover` | 1 |
| [L&#x2011;02] | Prefer using _safeMint over _mint| 2 |
| [L&#x2011;03] | Critical Changes Should Use Two-step Procedure` | 1 |
| [L&#x2011;04] | Missing zero address check in function `releaseToAddress` in `CollateralToken.sol` | 1 |

Total: 5 instances over 4 issues

## Non-critical

| N-C    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Constants should be defined rather than using magic numbers  | 3 |
| [N&#x2011;02] | Unclear comments in `ClearningHouse.sol` | 1 |
| [N&#x2011;03] | Unnamed parameter in function `_execute` | 1 |
| [N&#x2011;04] | Functions setApprovalForAll and safeBatchTransferFrom are missing implementation and event emission  | 2 |
| [N&#x2011;05] | Typos in comments | 2 |
| [N&#x2011;06] | Unused imports| 2 |
| [N&#x2011;07] | Missing natspec | 1 |

Total: 12 instances over 7 issues

## Low Risk

### \[L-01\] Signature malleability for `ecrecover` 

File: `VaultImplementation.sol`

The implementation of a cryptographic signature system in Ethereum contracts often assumes that the signature is unique, but signatures can be altered without the possession of the private key and still be valid. The EVM specification defines several so-called ‘precompiled’ contracts one of them being ecrecover which executes the elliptic curve public key recovery. A malicious user can slightly modify the three values v, r and s to create other valid signatures. A system that performs signature verification on contract level might be susceptible to attacks if the signature is part of the signed message hash. Valid signatures could be created by a malicious user to replay previously signed messages.

```solidity
address recoveredAddress = ecrecover(
```

Line of code:
- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/VaultImplementation.sol#L246


Use OpenZeppelin ECDSA library.

### \[L-02\] Prefer using _safeMint over _mint

Both LienToken::_createLien and CollateralToken::onERC721Received use ERC721's _mint method, which is missing the check if the account to mint the NFT to is a smart contract that can handle ERC721 tokens. The _safeMint method does exactly this, so prefer using it over _mint but always add a nonReentrant modifier, since calls to _safeMint can reenter.

```solidity
 _mint(from_, collateralId);
```

```solidity
 _mint(params.receiver, newLienId);
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L588

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L455 


### \[L-03\] Critical Changes Should Use Two-step Procedure
The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference: https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure 

File: `VaultImplementation.sol`

```solidity
function setDelegate(address delegate_) external
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L210


### \[L-04\] Missing zero address check in function `releaseToAddress` in `CollateralToken.sol`

File: `CollateralToken.sol`

```solidity
 function _releaseToAddress(
    CollateralStorage storage s,
    Asset memory underlyingAsset,
    uint256 collateralId,
    address `releaseTo`
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/CollateralToken.sol#L356

Consider adding zero address check.


## Non-critical


### \[NC-01\] Constants should be defined rather than using magic numbers 

File: `PublicVault.sol`

```solidity
uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L604

File: `AstariaRouter.sol`

```solidity
s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
```

```solidity
s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AstariaRouter.sol#L112

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AstariaRouter.sol#L115



### \[NC-02\] Unclear comments in `ClearningHouse.sol`

The comments quality is poor, please revisit the commenting in this contract and consider improving it for better 


### \[NC-03\] Unnamed parameter in function `_execute`

File: `ClearingHouse.sol`

```solidity
 function _execute(
    address tokenContract, // collateral token sending the fake nft
    address to, // buyer
    uint256 encodedMetaData, //retrieve token address from the encoded data
    uint256 // space to encode whatever is needed,
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/ClearingHouse.sol#L118

### \[NC-04\] Functions setApprovalForAll and safeBatchTransferFrom are missing implementation and event emission 

File: `ClearingHouse.sol`

```solidity
function setApprovalForAll(address operator, bool approved) external {}
```

```solidity
function safeBatchTransferFrom(
    address from,
    address to,
    uint256[] calldata ids,
    uint256[] calldata amounts,
    bytes calldata data
  ) public {}
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L104

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L180

### \[NC-05\] Typos in comments

File: `Vault.sol`

Change `vautls` to `vaults`

File: `PublicVault.sol`

Change `calcualtion` to `calculation`


### \[NC-06\]  Unused imports

Consider removing them from the contracts

File: `ClearingHouse.sol`

```solidity
 import {ConduitControllerInterface} from "seaport/interfaces/ConduitControllerInterface.sol"; 
```


File: `LienToken.sol`

```solidity
import {Initializable} from "./utils/Initializable.sol";
```


### \[NC-07\] Missing natspec

NatSpec documentation to all public methods and variables is essential for better understanding of the code by developers and auditors and is strongly recommended.

Most of the functions in the contract are missing natspec, some of them are missing only couple of params.

Consider adding natspec documentation for all the code.
