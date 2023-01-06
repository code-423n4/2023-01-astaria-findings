## Summary
|ID     | Finding|  Gas saved| Instances |
|:----: | :---           |              :----:    |  :----:         |
|1       | Use a literal instead of functions for a constant|   22   | 1 |
| 2      | Set timestamp check at the top so it doesn't need to do all the functions before if it's false| 18| 1 |
| 3      || 0| 1 |

## Details
### 1 
[CollateralToken.sol#L73](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73)

### 2
[CollateralToken.sol#L109-L134](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L109-L134)