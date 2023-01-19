1.

Typos in:

1.1

Contract: Vault.sol

1.1.1

	line 90

	"vaults" is misspelled as "vautls"
	
Modified Comment:

	//invalid action private vaults can only be the owner or strategist

1.2
	
Contract: PublicVault.sol

1.2.1

	line 307

	"calculation" is misspelled as "calcualtion"
	
Modified Comment:

	// reset liquidationWithdrawRatio to prepare for re calculation

2.

Grammar issues in:

2.1

Contract: WithdrawProxy.sol

2.1.1

	line 32

	"are" should be "is"
	
Modified Comment:

	* according to their balance in the protocol which is redeemable 1:1 for the underlying PublicVault asset by the end

2.2

Contract: CollateralToken.sol

2.2.1

	line 126

	"dont" should be "don't"
	
Modified Comment:

	//revert auction params don't match

2.2.2

	line 507

	"its" should be "it's"
	
Modified Comment:

	//get total Debt and ensure it's being sold for more than that

2.3

Contract: PublicVault.sol

2.3.1

	line 307

	"re calculation" should be one word as "recalculation"

Modified Comment:

	// reset liquidationWithdrawRatio to prepare for recalculation

Note: line 307 comment had a typo as stated in 1.2.1 above with "calculation" being misspelled so this is not a direct excerpt from the code in scope but rather an escalation from 1.2.1

2.3.2

	line 374

	"then" should be "than"

Modified Comment:

	// prevent transfer of more assets than are available
	
2.4

Contract: AstariaRouter.sol

2.4.1

	line 780

	there is an unnecessary comma at the end of the comment after "loan"

Modified Comment:

	//router must be approved for the collateral to take a loan
	
2.5
	
Contract: LienToken.sol

2.5.1

	line 42

	"which" should be "that"

Modified Comment:

	* @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults that originate loans

2.6

Contract: VaultImplementation.sol

2.6.1

	line 281

	in this context "after commitment" should be hyphenated as "after-commitment"

Modified Comment:

	* Origination consists of a few phases: pre-commitment validation, lien token issuance, strategist reward, and after-commitment actions

2.6.2

	line 282

	"take" should be "taking"

Modified Comment:

	* Starts by depositing collateral and taking optimized-out a lien against it. Next, verifies the merkle proof for a loan commitment. Vault owners are then

2.6.3

	line 309

	"optimized-out" should not be hyphenated in this context

Modified Comment:

	* @notice Buy optimized out a lien to replace it with new terms.
	
3.

Inconsistent spacing in comments:

It is also best practice to leave at least one space after the initial comment "//" mark. This will also add consistency to comments.

3.1

Contract: TransferProxy.sol

3.1.1

	line 25

Modified Comment:

	// only constructor we care about is  Auth

3.2

Contract: Vault.sol

3.2.1

	line 76, and line 81

Modified Comment:

	// invalid action allowlist must be enabled for private vaults

	the above comment is the same for both lines

3.2.2

Modified Comment:

	line 90

	// invalid action private vautls can only be the owner or strategist

Note: If the above step at 1.1.1 were followed where this comment has a typo, this comment would be:

	// invalid action private vaults can only be the owner or strategist

3.3

Contract: ClearingHouse.sol

3.3.1

	line 71

Modified Comment:

	// only execute from the conduit

3.3.2

	line 117

Modified Comment:

	// retrieve token address from the encoded data

3.3.3

	line 130

Modified Comment:

	// we are a consideration we round up

3.3.4

	line 174

Modified Comment:

	// empty from seaport

3.3.5

	line 176

Modified Comment:

	// data is empty and useless

3.4

Contract: CollateralToken.sol

3.4.1

	line 120

Modified Comment:

	// revert no auction

3.4.2

	line 126

Modified Comment:

	// revert auction params dont match
	
Note: If the above step at 2.2.1 were followed where this comment has a grammar issue, this comment would be:
	
	// revert auction params don't match

3.4.3

	line 133

Modified Comment:

	// auction hasn't ended yet

3.4.4

	line 291

Modified Comment:

	// look to see if we have a security handler for this asset

3.4.5

	line 305

Modified Comment:

	// trigger the flash action on the receiver

3.4.6

	line 507

Modified Comment:

	// get total Debt and ensure its being sold for more than that

Note: If the above step at 2.2.2 were followed where this comment has a grammar issue, this comment would be:
	
	// get total Debt and ensure it's being sold for more than that
	
3.5

Contract: PublicVault.sol

3.5.1

	line 173

Modified Comment:

	// this will underflow if not enough balance

3.5.2

	line 508

Modified Comment:

	// owner is "strategist"

3.6

Contract: AstariaRouter.sol

3.6.1

	line 393

Modified Comment:

	// PUBLIC

3.6.2

	line 730

Modified Comment:

	// immutable data

3.6.3

	line 744

Modified Comment:

	// mutable data

3.6.4

	line 780

Modified Comment:

	// router must be approved for the collateral to take a loan,

Note: If the above step at 2.4.1 were followed where this comment has a grammar issue, this comment would be:
	
	// router must be approved for the collateral to take a loan

3.7

Contract: LienToken.sol

3.7.1

	line 126

Modified Comment:

	// the borrower shouldn't incur more debt from the buyout than they already owe

3.7.2

	line 211

Modified Comment:

	// no need to check validity before the position we're buying

3.7.3

	line 400

Modified Comment:

	// 0 - 4 are valid

3.7.4

	line 634

Modified Comment:

	// checks the lien exists

3.7.5

	line 645

Modified Comment:

	// full delete

3.7.6

	line 825

Modified Comment:

	// bring the point up to block.timestamp, compute the owed

3.7.7

	line 844

Modified Comment:

	// full delete of point data for the lien

3.8

Contract: AstariaVaultBase.sol

3.8.1

	line 29

Modified Comment:

	// ends at 20

3.8.2

	line 33

Modified Comment:

	// ends at 21

3.8.3

	line 44

Modified Comment:

	// ends at 44

3.8.4

	line 47

Modified Comment:

	// ends at 64

3.8.5

	line 55

Modified Comment:

	// ends at 116
	
4.

Comment should be deprecated in:

4.1

Contract: AstariaRouter.sol

4.1.1

There are 2 comments in line 116 where the first seems to be the answer to a test done of what the following comment describes.

Given that non of the other lines in this context has comments like this there is no need to have it here and it is fairly straightforward to understand like the other comments.

5.

Double comment initialization in:

5.1

Contract: LienToken.sol

5.1.1

	line 831
	
It is best practice to only have one "//" comment initialization and although the throughput on Github is a normal comment that is a little bit odd, in an IDE like VS Code the comment gets uncommented with a "-----" through the comment.

If the comment does actually need to be removed as generated in an IDE like VS Code then it should or else the comment should follow best practices with a single initialization of "//".

6.

A comment doesn't make sense in:

6.1

Contract: ClearingHouse.sol

6.1.1

	line 130
	
Not sure what the comment should be but if I can take an incredibly wild guess I would say

"We are in the process of rounding up" or something close but genuinely not sure.

7. 

Comment not correlating with code:

7.1.

Contract: AstariaRouter.sol

7.1.1

In the above contract on line 709, it states "LP whitelist" but in the function, it is describing there is no whitelist variable but rather an allowList to do what the comment is specifying. 

In the beginning of the comment, it also describes the variable as "allowListEnabled". This does not relate to whitelist from a naming perspective.

In theory whitelist and allowlist gets used interchangeably, and can be seen as the same thing, can be argued that this is separate from the protocol as it refers to LPs rather than protocol level engagement and is technically the same but throughout the code under scope, most notably contracts Vault.sol, PublicVault.sol, and AstariaRouter.sol "allowlist" (functions and adjusted variables included) gets used throughout but never the use of whitelist.

Consider changing the comment description from "whitelist" to "allowlist" in line 709 comment after "LP" as this will bring consistency throughout comments/code mentioned and specifically comments regarding function _newVault of contract AstariaRouter.