|                                                                 | Issue                                           | Instances |
| --------------------------------------------------------------- | :---------------------------------------------- | :-------: |
| [GAS-1](#gas-1-declare-constructor-as-payable)                  | Declare Constructor as payable                  |     3     |
| [GAS-2](#gas-2-use-solidity-ir-based-codegen-compiler-pipeline) | Use Solidity IR-based Codegen compiler pipeline |    N/A    |
| [GAS-3](#gas-3-function-modifiers-can-be-inefficient)           | Function modifiers can be inefficient           |    12     |


---
## GAS-1: Declare Constructor as payable
- Description: You eliminate the payable check. Saving gas during deployment.
- location: [AstariaRouter.sol#L70](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L70), [CollateralToken.sol#L76](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L76), [LienToken.sol#L55](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L55)
- count: 3 

## GAS-2: Use Solidity IR-based Codegen compiler pipeline.
- Description: The IR-based code generator was introduced with an aim to not only allow code generation to be more transparent and auditable but also to enable more powerful optimization passes that span across functions.
- Location: Project wide.
- Proof of gas savings: running `forge snapshot --via-ir --ffi --diff` shows a 3-6% gas savings.
```bash
$ forge snapshot --via-ir --ffi --diff

testCannotRandomAccountIncrementNonce() (gas: [31m219[0m ([31m0.125%[0m)) 
testCannotExceedMinMaxPublicVaultEpochLength() (gas: [31m111[0m ([31m0.245%[0m)) 
testIncrementNonceAsStrategistAndDelegate() (gas: [32m-1011[0m ([32m-0.456%[0m)) 
testVaultShutdown() (gas: [31m2464[0m ([31m1.247%[0m)) 
testBlockingLiquidationsProcessEpoch() (gas: [32m-131851[0m ([32m-2.183%[0m)) 
testCollateralTokenFileSetup() (gas: [31m856[0m ([31m2.203%[0m)) 
testFutureLiquidationWithBlockingWithdrawReserve() (gas: [32m-134864[0m ([32m-2.561%[0m)) 
testLiquidationAtBoundary() (gas: [32m-224573[0m ([32m-2.596%[0m)) 
testLiquidationBoundaryEpochOrdering() (gas: [32m-185905[0m ([32m-2.597%[0m)) 
testMultipleVaultsWithLiensOnTheSameCollateral() (gas: [32m-218248[0m ([32m-2.692%[0m)) 
testLiquidationNftTransfer() (gas: [32m-122563[0m ([32m-2.908%[0m)) 
testLiquidationPaymentsOverbid() (gas: [32m-125750[0m ([32m-3.011%[0m)) 
testLiquidationNearBoundaryNoWithdraws() (gas: [32m-125170[0m ([32m-3.047%[0m)) 
testLiquidation5050Split() (gas: [32m-175433[0m ([32m-3.060%[0m)) 
testInvalidFileData() (gas: [31m1084[0m ([31m3.101%[0m)) 
testZeroizedVaultNewLP() (gas: [32m-115274[0m ([32m-3.110%[0m)) 
testAuctionEnd() (gas: [32m-126373[0m ([32m-3.112%[0m)) 
testBuyoutLienDifferentCollateral() (gas: [32m-109070[0m ([32m-3.123%[0m)) 
testEpochProcessionMultipleActors() (gas: [32m-111946[0m ([32m-3.209%[0m)) 
testFailPayLienAfterLiquidate() (gas: [32m-95116[0m ([32m-3.323%[0m)) 
testFinalAuctionEnd() (gas: [32m-92660[0m ([32m-3.397%[0m)) 
testWithdrawLiquidatedNoBids() (gas: [32m-96137[0m ([32m-3.459%[0m)) 
testBuyoutLien() (gas: [32m-109339[0m ([32m-3.478%[0m)) 
testCannotCommitToLienPotentialDebtExceedsLiquidationInitialAsk() (gas: [32m-90879[0m ([32m-3.525%[0m)) 
testAuctionEndNoBids() (gas: [32m-93057[0m ([32m-3.538%[0m)) 
testTwoLoansDiffCollateralSameStack() (gas: [32m-100120[0m ([32m-3.544%[0m)) 
testCannotBorrowMoreThanMaxPotentialDebt() (gas: [32m-97845[0m ([32m-3.772%[0m)) 
testFailLienDurationZero() (gas: [32m-91078[0m ([32m-4.050%[0m)) 
testNewLienExceeds2XEpoch() (gas: [32m-92717[0m ([32m-4.110%[0m)) 
testPublicVaultSlopeIncDecIntegration() (gas: [32m-98998[0m ([32m-4.145%[0m)) 
testCannotProcessEpochWithUnliquidatedLien() (gas: [32m-94202[0m ([32m-4.158%[0m)) 
testBasicPublicVaultLoan() (gas: [32m-96544[0m ([32m-4.212%[0m)) 
testCannotLiquidationInitialAskExceedsAmountBorrowed() (gas: [32m-87715[0m ([32m-4.280%[0m)) 
testMakeTwoPayments() (gas: [32m-98955[0m ([32m-4.325%[0m)) 
testCannotLiquidationInitialAsk0() (gas: [32m-89449[0m ([32m-4.360%[0m)) 
testBasicPrivateVaultLoan() (gas: [32m-91747[0m ([32m-4.362%[0m)) 
testCannotBorrowMoreThanMaxAmount() (gas: [32m-88968[0m ([32m-4.408%[0m)) 
testReleaseToAddress() (gas: [32m-96509[0m ([32m-4.411%[0m)) 
testCannotLienRateZero() (gas: [32m-89332[0m ([32m-4.426%[0m)) 
testLienTokenFileSetup() (gas: [31m1633[0m ([31m4.553%[0m)) 
testWithdrawProxy() (gas: [32m-92944[0m ([32m-5.575%[0m)) 
testFailSoloLendNotAppraiser() (gas: [32m-86386[0m ([32m-6.212%[0m)) 
testFailInvalidSignature() (gas: [32m-86317[0m ([32m-6.219%[0m)) 
testFeesExample() (gas: [32m-847[0m ([32m-27.689%[0m)) 
Overall gas change: [32m-4059525[0m ([32m-151.166%[0m)
```

## GAS-3: Function modifiers can be inefficient
- Description: The code of modifiers is inlined inside the modified function, thus adding up size and costing gas. Limit the modifiers. Internal functions are not inlined, but called as separate functions. They are slightly more expensive at run time, but save a lot of redundant bytecode in deployment, if used more than once.
- Location: 12 modifiers in 7 files
- count: 12
