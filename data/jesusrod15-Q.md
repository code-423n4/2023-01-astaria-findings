##ETH can be stay  locked in a contract 

this functions are marked payables and can transfer eth to contract

      https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L130

     https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L146

     https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L162

     https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L178

     https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L207


but not there is any function that transfer out those eth  
i marked this low because is a bit propabble that someone trasnfer eth calling this functions 
*************************************************************************************************************************************
##A lot setting addresses no check that be different address(0)  

     https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L97-L106

this initially can be a problem for a protocol but i marked low because then can be fix by guardian 
*****************************************************************************************************************************************
##can be lose ownership of router
 in the funcion transferOwnership

    https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AuthInitializable.sol#L95-L100

 no check that new Owner be different address(0) although can be setting again this can be bring another problems 


