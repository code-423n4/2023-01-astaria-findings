|                                          | Issue                      | Instances |
| ---------------------------------------- | :------------------------- | :-------: |
| [QA-1](#qa-1-avoid-redundant-operations) | Avoid redundant operations |     2     |


## QA-1 Avoid redundant operations
- Description: AbiEncoderv2 is the default as of 0.8.0 and is no longer experimental. Their is no need define it explicitly. 
- Severity: Low
- Location: [LienToken.sol#L16, L](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L16), [CollateralToken.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L16)
- Count: 2

