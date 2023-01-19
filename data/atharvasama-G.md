## GAS OPTIMIZATIONS

### [G-01]: PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT CAN BE DECLARED EXTERNAL INSTEAD 
> ** File: ClearingHouse.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L43
> ** File: ClearingHouse.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L51

### [G-02]: ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
> ** File: VaultImplementation.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L155

### [G-03]: INTERNAL FUNCTIONS CALLED ONLY ONCE CAN BE INLINED TO SAVE GAS
> **File: AstariaRouter.sol => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L407
> **File: BeaconProxy.sol => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L29


### [G-04]: UNNECESSARY CASTING OF ADDRESS
Explicit conversion of s.guardian is unnecessary and can be omitted to save gas.
> **File: AstariaRouter.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L361

### [G-05]: USE FUNCTION INSTEAD OF MODIFIERS
Modifier code is inlined, meaning that it gets added at the beginning and the end of the function it modifies.
A trick to reduce the contract size while using modifiers is to write a function that implements the modifier logic, and make the modifier invoke that function. That way the code implementing the modifier functionality will not be replicated, only the function invocation will.
> ** File: AstariaRouter.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L196

### [G-06]: USING X += Y COSTS MORE GAS THAN X = X + Y
There are 18 instances of this.
> ** File: AstariaRouter.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L512
> ** File: WithdrawProxy.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L237
> ** File: WithdrawProxy.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L313
> ** File: PublicVault.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L179
> ** File: PublicVault.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L565
> ** File: PublicVault.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L583
> ** File: PublicVault.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L606
> ** File: PublicVault.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L624
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L160
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L210
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L480
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L524
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L720
> ** File: LienToken.sol** => https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L735
> ** File: ERC20-Cloned.sol** => https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L59
> ** File: ERC20-Cloned.sol** => https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L98
> ** File: ERC20-Cloned.sol** => https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L174
> ** File: ERC20-Cloned.sol** => https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L179