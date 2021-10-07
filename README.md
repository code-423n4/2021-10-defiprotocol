# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos: 
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted. 

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [ ] Name of each contract and:
  - [ ] lines of code in each
  - [ ] external contracts called in each
  - [ ] libraries used in each
- [ ] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [ ] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [ ] Describe anything else that adds any special logic that makes your approach unique
- [ ] Identify any areas of specific concern in reviewing the code
- [ ] Add all of the code to this repo that you want reviewed
- [ ] Create a PR to this repo with the above changes.

---

# Contest prep

## 🐺 C4: Contest prep
- [X] Rename this repo to reflect contest date (if applicable)
- [X] Rename contest H1 below
- [X] Add link to report form in contest details below
- [ ] Update pot sizes
- [X] Fill in start and end times in contest bullets below.
- [ ] Move any relevant information in "contest scope information" above to the bottom of this readme.
- [ ] Add matching info to the [code423n4.com public contest data here](https://github.com/code-423n4/code423n4.com/blob/main/_data/contests/contests.csv))
- [ ] Delete this checklist.

## ⭐️ Sponsor: Contest prep
- [ ] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [ ] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [ ] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [ ] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [ ] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] Designate someone (or a team of people) to monitor DMs & questions in the C4 Discord (**#questions** channel) daily (Note: please *don't* discuss issues submitted by wardens in an open channel, as this could give hints to other wardens.)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# defiProtocol contest details
- $22,500 worth of ETH main award pot
- $2,500 worth of ETH gas optimization award pot
- Join [C4 Discord](https://discord.gg/EY5dvm3evD) to register
- Submit findings [using the C4 form](https://code423n4.com/2021-10-defiprotocol-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts October 8, 2021 00:00 UTC
- Ends October 10, 2021 23:59 UTC

This repo will be made public before the start of the contest. (C4 delete this line when made public)

[ ⭐️ SPONSORS ADD INFO HERE ]
# Contracts
- Factory.sol - 117 lines
  - Handles proposing new Baskets and creating Basket Contracts and Auction Contracts
  - Also controls various protocol parameters
- Basket.sol - 273 lines
  - Handles minting/burning basket tokens as well as holding the assets backing the Basket
  - Handles streaming fees to the basket publisher and the protocol
- Auction.sol - 159 lines
  - Handles rebalance auctions needed to rebalance baskets

Contracts are all in a standard Hardhat project

# Libraries used
- OpenZeppelin
-- SafeERC20, ERC20Upgradeable, IERC20, Ownable, Clones

# Info
- The protocol is an asset management protocol that bundles ERC20 tokens into baskets and mints an ERC20 token representing the basket. Each basket has a publisher who can propose basket weights and composition, a license streaming fee on the basket, as well as update the publisher of that basket, all of these actions are subject to a 24 hour timelock. Baskets are created through the Factory contract which also creates an Auction contract for each basket. When a new weighting proposal for a basket passes the timelock, a rebalancing auction is started where anyone can bond a portion of the basket token supply and settle the auction by rebalancing the basket. The amounts needed to rebalance the basket are determined by the new weights as well as how long it takes for the auction to become settled. If an auction has not been settled after being bonded within 24 hours, the bond can be burned and the auction will need to be started again. Bounties can also be added to the auction that are claimed by the auction settler to incentive rebalances.
- Streaming fees for a basket are split between the publisher as well as the protocol owner if the protocol fee is turned on. They are calculated and handled on each mint or burn of the basket. The protocol owner fee split must be less than 20% of the basket's license fee. 
- The protocol owner can change auction parameters, such as the auction multiplier, the min amount needed to bond, how much the auction decrements, as well as settings related to the protocol fee (fee split and receiving address). In general for this audit scope we consider the owner to be a trusted entity.
- Basket Tokens are standardized to 18 decimals
- Token weights are defined as the amount of that token in one basket token, multiplied by the current iBRation (Index to Basket Ratio)
- The protocol is designed for standard ERC20 tokens, it is not currently concerned with the potential effects of rebasing or non-standard ERC20 implementations such as tokens that take a fee on transfers. It is assumed that publishers/users will check which tokens are added to baskets.

# Areas of Concern to look at
- Logic related to rebalancing and settling auctions, that auctions are settled correctly with correct weights and in time.
- Logic related to streaming fees calculations, that they are calculated and handled correctly within minting and burning.
