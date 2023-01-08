## [L-01]
Title
```
SWC-111 Deprecated solidity import file
```
URL
```
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/TransferProxy.sol#L19
```
Current Code
```
import {ITransferProxy} from "core/interfaces/ITransferProxy.sol";
```
Remediation
```
// replace import file with 
import {ITransferProxy} from "src/interfaces/ITransferProxy.sol";
```