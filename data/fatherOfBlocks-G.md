**src/CollateralToken.sol**
- L138/139/342 - Two variables are created in memory that are never used, therefore it is not necessary to create the variables.

- L558/602 - It is not necessary to create a modifier method if it is only used once, you could directly use the if and revert within the used method.


**src/PublicVault.sol**
- L491/492/657/658 - It is not necessary to create a variable if it is only going to be used once, therefore the operation could be used directly where it is needed.


**src/LienToken.sol**
- L260/262/632/670/681 - It is not necessary to create a variable if it is only going to be used once, therefore the operation could be used directly where it is needed.


**src/VaultImplementation.sol**
- L399/400 - It is not necessary to create a variable if it is only going to be used once, therefore the operation could be used directly where it is needed.
