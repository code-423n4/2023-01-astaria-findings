# LOW FINDINGS

### [L-1]  ids parameter not used any where inside the functions. Its better to be removed from functions parameter. 

### Impact:

Without any usage its cause a confusion while calling the functions.

### Proof Of Work: 

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

          function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)  //@Audit ids parameter not used inside the functions LOW FINDINGS
          external
          view
          returns (uint256[] memory output)
          {
          output = new uint256[](accounts.length);
          for (uint256 i; i < accounts.length; ) {
          output[i] = type(uint256).max;
          unchecked {
          ++i;
          }
          }
          }

### Recommended Mitigation Steps: 

If there is no idea for using  ids parameter it can be safely removed . 

##

### [ L-2]  INITIALIZE() FUNCTION CAN BE CALLED BY ANYBODY

initialize() function can be called anybody when the contract is not initialized.

[FILE : CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)

    function initialize(
    Authority AUTHORITY_,
    ITransferProxy TRANSFER_PROXY_,
    ILienToken LIEN_TOKEN_,
    ConsiderationInterface SEAPORT_
    ) public initializer {
    __initAuth(msg.sender, address(AUTHORITY_));
    __initERC721("Astaria Collateral Token", "ACT");
    CollateralStorage storage s = _loadCollateralSlot();
    s.TRANSFER_PROXY = TRANSFER_PROXY_;
    s.LIEN_TOKEN = LIEN_TOKEN_;
    s.SEAPORT = SEAPORT_;
    (, , address conduitController) = s.SEAPORT.information();
    bytes32 CONDUIT_KEY = Bytes32AddressLib.fillLast12Bytes(address(this));
    s.CONDUIT_KEY = CONDUIT_KEY;
    s.CONDUIT_CONTROLLER = ConduitControllerInterface(conduitController);

    s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(CONDUIT_KEY, address(this));
    s.CONDUIT_CONTROLLER.updateChannel(
      address(s.CONDUIT),
      address(SEAPORT_),
      true
    );
    }

Recommended Mitigation Steps :

Add a control that makes initialize() only call the Deployer Contract;

if (msg.sender != DEPLOYER_ADDRESS) {
						revert NotDeployer();
				}

# NON CRITICAL FINDINGS

### [NC - 1 ]  NATSPEC COMMENTS SHOULD BE ADDED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

Recommendation
NatSpec comments should be increased in Contracts

PROOF OF WORK:

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)

##

### [NC-2]  accounts parameter array length should be checked. Its possible to call balanceOfBatch() function with empty array.


[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

        function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)  
          view
          returns (uint256[] memory output)
          {
          output = new uint256[](accounts.length);
          for (uint256 i; i < accounts.length; ) {
          output[i] = type(uint256).max;
          unchecked {
          ++i;
          }
          }
          }
 
### Recommended Mitigation Steps: 

   require(accounts.length>0, " Array is empty");

##

###  [NC-3] Shorter inheritance list

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)


     contract CollateralToken is
     AuthInitializable,
     ERC721,
     IERC721Receiver,
     ICollateralToken,
     ZoneInterface

##

### [NC-4] TYPOS

[FILE : Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

///Audit  vautls  => vaults 

 90 :  //invalid action private vautls can only be the owner or strategist

[FILE : PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)

///Audit  calcualtion=> calculation

   307 : // reset liquidationWithdrawRatio to prepare for re calcualtion

##

### [NC-5]   EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT


While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

[FILE : PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol)

       53 :  uint256 private constant PUBLIC_VAULT_SLOT =
    uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;



