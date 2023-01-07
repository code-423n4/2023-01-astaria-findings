src/ClearingHouse.sol

The contract implements the IERC1155 interface, but does not properly implement the required functions.

In particular, the balanceOf and balanceOfBatch functions are expected to return the balance of a given token ID for a given account, but both functions simply return the maximum possible value of a uint256. This behavior is incorrect and could cause issues if these functions are relied upon by other contracts.

The IERC1155 interface should be properly implemented by providing correct implementations for the balanceOf and balanceOfBatch functions.

