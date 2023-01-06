## Summary
|ID     | Finding|  Gas saved| Instances |
|:----: | :---           |              :----:    |  :----:         |
|1       | Storage variables read more than once in a function must be assigned to a memory variable | 100 | 1 |
| 2      | | 18| 1 |
| 3      |Miscellaneous| 0| 1 |

## Details
### 1 Storage variables read more than once in a function must be assigned to a memory variable
[CollateralToken.sol#L115-L130](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L115-L130)
Gas saved: 100
```diff 
	address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
+	bytes32 auction = s.collateralIdToAuction[collateralId];	
        if (
-         s.collateralIdToAuction[collateralId] == bytes32(0) ||
+        auction == bytes32(0) ||
          liquidator == address(0)
        ) {
         //revert no auction
         revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
        }
        if (
-         s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
+        auction != keccak256(abi.encode(params))
        ) {
         //revert auction params dont match
         revert InvalidCollateralState(
         InvalidCollateralStates.INVALID_AUCTION_PARAMS
       );
     }
```
[CollateralToken.sol#L299-L313](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L299-L313)
Gas saved: 135