| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Inline a function/modifier that is used only once|23|
|2| Avoid use of `&&` within a `require` function|4|
|3| Avoid storage of uints or ints smaller than 32 bytes, if possible|33|
|| Total|60|
___

| No. | Description | 
|--| ----------- | 
|1|**Inline a function/modifier that is used only once**|
|  | It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.|

___			
[CollateralToken.sol: L253](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L253)
```solidity
  modifier releaseCheck(uint256 collateralId) {
```
Since `releaseCheck()` is used only once in this contract (in `function releaseToAddress()`) as shown below, it should be inlined to save gas

[CollateralToken.sol: L331-333](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L331-L333)
```solidity
  function releaseToAddress(uint256 collateralId, address releaseTo)
    public
    releaseCheck(collateralId)
```
___
[BeaconProxy.sol: L20](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L20)
```solidity
  function _getBeacon() internal pure returns (IBeacon) {
```
Since `_getBeacon()` is used only once in this contract, as shown below, it should be inlined to save gas

[BeaconProxy.sol: L62](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L62)
```solidity
    _delegate(_getBeacon().getImpl(_getArgUint8(20)));
```
___

Similarly, the functions below are called only once and should be inlined:

**BeaconProxy.sol**

[L29](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L29), [L87](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/BeaconProxy.sol#L87)

**CollateralToken.sol**

[L502-506](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L502-L506), [L541-542](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L541-L542)

**PublicVault.sol**

[L597-601](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L597-L601)

**AstariaRouter.sol**

[L407-408](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L407-L408), [L761-765](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L761-L765), [L788-792](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L788-L792)

**LienToken.sol**

[L122-125](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L122-L125),  [L233-239](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L233-L239),  [L292-298](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L292-L298), [L459-463](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L459-L463), [L512-518](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L512-L518), [L623-630](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L623-L630)

[L663-667](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L663-L667), [L715-718](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L715-L718), [L775-776](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L775-L776), [L855-856](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L855-L856), [L911-915](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L911-L915)

**VaultImplementation.sol**

[L379-383](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L379-L383), [L397](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L397)
___
___


| No. | Description | 
|--| ----------- | 
|2|**Avoid use of `&&` within a `require` function**|
|  | Splitting into separate `require()` statements saves gas|	
						
___			
[ERC20-Clonedt.sol: L143-146](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143-L146)
```solidity
      require(
        recoveredAddress != address(0) && recoveredAddress == owner,
        "INVALID_SIGNER"
      );
```
Suggestion:
```solidity
      require(recoveredAddress != address(0), "INVALID_SIGNER");
      require(recoveredAddress == owner, "INVALID_SIGNER");
```
___
Similarly for the following:

[Vault.sol: L65](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65)

[PublicVault.sol: L672-675](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L672-L675)

[PublicVault.sol: L687-690](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L687-L690)
___
___


| No. | Description | 
|--| ----------- | 
|3|**Avoid storage of uints or ints smaller than 32 bytes, if possible**|
|  | Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed|

___
[AstariaRouter.sol: L395](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L395)
```solidity
  function getImpl(uint8 implType) external view returns (address impl) {
```
___
[AstariaRouter.sol: L621](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L621)
```solidity
  function liquidate(ILienToken.Stack[] memory stack, uint8 position)
```
___
[AstariaRouter.sol: L684-688](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L684-L688)
```solidity
  function isValidRefinance(
    ILienToken.Lien calldata newLien,
    uint8 position,
    ILienToken.Stack[] calldata stack
  ) public view returns (bool) {
```
___
[AstariaRouter.sol: L712-722](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L712-L722)
```solidity
  function _newVault(
    RouterStorage storage s,
    address underlying,
    uint256 epochLength,
    address delegate,
    uint256 vaultFee,
    bool allowListEnabled,
    address[] memory allowList,
    uint256 depositCap
  ) internal returns (address vaultAddr) {
    uint8 vaultType;
```
___
[LienToken.sol: L608-613](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L608-L613)
```solidity
  function makePayment(
    uint256 collateralId,
    Stack[] calldata stack,
    uint8 position,
    uint256 amount
  )
```
___
[LienToken.sol: L790-795](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L790-L795)
```solidity
  function _payment(
    LienStorage storage s,
    Stack[] memory activeStack,
    uint8 position,
    uint256 amount,
    address payer
```
___
[LienToken.sol: L855](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L855)
```solidity
  function _removeStackPosition(Stack[] memory stack, uint8 position)
```
___
[VaultImplementation.sol: L313](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L313-L316)
```solidity
  function buyoutLien(
    ILienToken.Stack[] calldata stack,
    uint8 position,
    IAstariaRouter.Commitment calldata incomingTerms
```
___
[IBeacon.sol: L22](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IBeacon.sol#L22)
```solidity
  function getImpl(uint8) external view returns (address);
```
___
[IUniswapV3Factory.sol: L18-24](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L18-L24)
```solidity
    event PoolCreated(
        address indexed token0,
        address indexed token1,
        uint24 indexed fee,
        int24 tickSpacing,
        address pool
    );
```
___
[IUniswapV3Factory.sol: L29](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L29)
```solidity
    event FeeAmountEnabled(uint24 indexed fee, int24 indexed tickSpacing);
```
___
[IUniswapV3Factory.sol: L40](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L40)
```solidity
    function feeAmountTickSpacing(uint24 fee) external view returns (int24);
```
___
[IUniswapV3Factory.sol: L48-51](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L48-L51)
```solidity
    function getPool(
        address tokenA,
        address tokenB,
        uint24 fee
```
___
[IUniswapV3Factory.sol: L62-65](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L62-L65)
```solidity
    function createPool(
        address tokenA,
        address tokenB,
        uint24 fee
```
___
[IUniswapV3Factory.sol: L77](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol#L77)
```solidity
    function enableFeeAmount(uint24 fee, int24 tickSpacing) external;
```
___
[IUniswapV3PoolState.sol: L21-32](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L21-L32)
```solidity
    function slot0()
        external
        view
        returns (
            uint160 sqrtPriceX96,
            int24 tick,
            uint16 observationIndex,
            uint16 observationCardinality,
            uint16 observationCardinalityNext,
            uint8 feeProtocol,
            bool unlocked
        );
```
___
[IUniswapV3PoolState.sol: L64-76](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L64-L76)
```solidity
    function ticks(int24 tick)
        external
        view
        returns (
            uint128 liquidityGross,
            int128 liquidityNet,
            uint256 feeGrowthOutside0X128,
            uint256 feeGrowthOutside1X128,
            int56 tickCumulativeOutside,
            uint160 secondsPerLiquidityOutsideX128,
            uint32 secondsOutside,
            bool initialized
        );
```
___
[IUniswapV3PoolState.sol: L79](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L79)
```solidity
    function tickBitmap(int16 wordPosition) external view returns (uint256);
```
___
[IVaultImplementation.sol: L81-84](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IVaultImplementation.sol#L81-L84)
```solidity
  function buyoutLien(
    ILienToken.Stack[] calldata stack,
    uint8 position,
    IAstariaRouter.Commitment calldata incomingTerms
```
___
[IV3PositionManager.sol: L55-71](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IV3PositionManager.sol#L55-L71)
```solidity
  function positions(uint256 tokenId)
    external
    view
    returns (
      uint96 nonce,
      address operator,
      address token0,
      address token1,
      uint24 fee,
      int24 tickLower,
      int24 tickUpper,
      uint128 liquidity,
      uint256 feeGrowthInside0LastX128,
      uint256 feeGrowthInside1LastX128,
      uint128 tokensOwed0,
      uint128 tokensOwed1
    );
```
___
[IV3PositionManager.sol: L73-85](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IV3PositionManager.sol#L73-L85)
```solidity
  struct MintParams {
    address token0;
    address token1;
    uint24 fee;
    int24 tickLower;
    int24 tickUpper;
    uint256 amount0Desired;
    uint256 amount1Desired;
    uint256 amount0Min;
    uint256 amount1Min;
    address recipient;
    uint256 deadline;
  }
```
___
[IAstariaRouter.sol: L101-105](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L101-L105)
```solidity
  struct StrategyDetailsParam {
    uint8 version;
    uint256 deadline;
    address vault;
  }
```
___
[IAstariaRouter.sol: L112-121](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L112-L121)
```solidity
  struct NewLienRequest {
    StrategyDetailsParam strategy;
    ILienToken.Stack[] stack;
    bytes nlrDetails;
    MerkleData merkle;
    uint256 amount;
    uint8 v;
    bytes32 r;
    bytes32 s;
  }
```
___
[IAstariaRouter.sol: L234](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L234)
```solidity
  function liquidate(ILienToken.Stack[] calldata stack, uint8 position)
```
___
[IAstariaRouter.sol: L278](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L278)
```solidity
  function getImpl(uint8 implType) external view returns (address impl);
```
___
[IAstariaRouter.sol: L288-291](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L288-L291)
```solidity
  function isValidRefinance(
    ILienToken.Lien calldata newLien,
    uint8 position,
    ILienToken.Stack[] calldata stack
```
___
[IAstariaRouter.sol: L295-300](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L295-L300)
```solidity
  event NewVault(
    address strategist,
    address delegate,
    address vault,
    uint8 vaultType
  );
```
___
[ILienToken.sol: L36-45](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L36-L45)
```solidity
  struct LienStorage {
    uint8 maxLiens;
    address WETH;
    ITransferProxy TRANSFER_PROXY;
    IAstariaRouter ASTARIA_ROUTER;
    ICollateralToken COLLATERAL_TOKEN;
    mapping(uint256 => bytes32) collateralStateHash;
    mapping(uint256 => AuctionData) auctionData;
    mapping(uint256 => LienMeta) lienMeta;
  }
```
___
[ILienToken.sol: L60-67](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L60-L67)
```solidity
  struct Lien {
    uint8 collateralType;
    address token; //20
    address vault; //20
    bytes32 strategyRoot; //32
    uint256 collateralId; //32 //contractAddress + tokenId
    Details details; //32 * 5
  }
```
___
[ILienToken.sol: L88-91](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L88-L91)
```solidity
  struct LienActionBuyout {
    uint8 position;
    LienActionEncumber encumber;
  }
```
___
[ILienToken.sol: L222-227](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L222-L227)
```solidity
  function makePayment(
    uint256 collateralId,
    Stack[] calldata stack,
    uint8 position,
    uint256 amount
  ) external returns (Stack[] memory newStack);
```
___
[ILienToken.sol: L294-299](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L294-L299)
```solidity
  event AddLien(
    uint256 indexed collateralId,
    uint8 position,
    uint256 indexed lienId,
    Stack stack
  );
```
___
[ILienToken.sol: L306-311](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L306-L311)
```solidity
  event LienStackUpdated(
    uint256 indexed collateralId,
    uint8 position,
    StackAction action,
    uint8 stackLength
  );
```
___
___
