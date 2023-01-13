POTENTIAL SECURITY ISSUE
The security issue with this code is that the receiver parameter is not being used, and the deposit function will only work for the owner of the vault. This could be a potential security vulnerability, as it means that anyone can deposit tokens into the vault as long as they are on the allowlist.


POSSIBLE MITIGATION SOLUTION
Add checks to the deposit function: The deposit function should be modified to check that the msg.sender is the same as the receiver before allowing the deposit to proceed. This would ensure that only the intended recipient can deposit tokens into the vault.

CODE REFERENCE
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L59-L68