# **VeraLux Program Structure: Detailed Technical Overview**

## **Table of Contents**
#### Introduction 
#### Program Architecture Overview 
#### Detailed Program Breakdown 
  #### - 1. Token Management 
  #### - 2. Staking 
  #### - 3. Governance 
  #### - 4. Airdrops 
  #### - 5. Treasury Management 
  #### - 6. Vesting 
  #### - 7. Migration
#### Key Interconnections 


## **Introduction**
The VeraLux ecosystem is a decentralized Web3 platform built on Solana, powered by the LUX token. It consists of seven core programs: **Token Management, Staking, Governance, Airdrops, Treasury Management, Vesting, and Migration**. This README.md provides a detailed technical overview of each program, including their roles, key components, and interactions, ensuring developers have a clear understanding of the architecture and can easily navigate to specific details.


## **Program Architecture Overview**
Each VeraLux program is built using the Anchor framework for Solana, following a modular structure that balances independence and interoperability. This allows developers to work on individual programs while ensuring seamless integration across the ecosystem. Below is the typical file structure for each program:

**lib.rs:**  _Defines the program ID and entrypoint, routing instructions to handlers._

**constants.rs:** _Stores program-specific constants (e.g., fees, PDA seeds)._

**errors.rs:** _Custom error codes (e.g., #[error_code] in Anchor)._

**state/mod.rs:** _Re-exports state structs._  
Account files (e.g., user.rs): Define on-chain data structures with #[account].

**instructions/mod.rs:** _Re-exports instruction handlers._  
Instruction files (e.g., stake.rs): Contain #[derive(Accounts)] and handler logic.

**events.rs:** _Defines #[event] structs for logging actions._

**utils.rs:** _Includes utility functions, CPI calls, and PDA derivations._

**This structure ensures consistency, making it easy for developers to locate specific components when revisiting the codebase.**


## **Detailed Program Breakdown**

### 1. **Token Management**
**Manages token transfers and burns, serving as the foundation for all token-related operations in VeraLux.**


**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Token decimals, mint address._

**errors.rs:** _InsufficientBalance, Unauthorized._

**state/token.rs:** _Token account states (e.g., user balances)._

**instructions/transfer.rs:** _Handles transfers with tax logic._

**instructions/burn.rs:** _Handles token burning._

**events.rs:** _TransferEvent, BurnEvent._

**utils.rs:** _SPL token CPI helpers._


#### Core Managment
##### State Structures: *Tracks user token balances and metadata.*
##### Instruction Handlers: *transfer (includes tax CPI to Treasury Management), burn.*
##### Events: _Logs transfers and burns for frontend integration._
##### Utility Functions: _SPL token CPI wrappers._
##### Interactions: _Called by Staking, Treasury Management, Vesting, and Migration for token operations._

--------------------------------------------------------------------------------------------------------------------

### **2. Staking**
**Manages staking, unstaking, and reward distribution for LUX token holders.**


**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Staking tiers, reward rates, lock periods._

**errors.rs:** _InvalidTier, LockPeriodNotElapsed._

**state/user.rs:** _User staking data (amount, tier, lock period)._

**state/pool.rs:** _Staking pool data (total staked, reward rates)._

**instructions/stake.rs:** _Stakes tokens._

**instructions/unstake.rs:** _Unstakes after lock period._

**instructions/claim_rewards.rs:** _Distributes rewards._

**instructions/upgrade_tier.rs:** _Upgrades staking tier._

**events.rs:** _StakeEvent, UnstakeEvent, RewardClaimedEvent._

**utils.rs:** _Voting power calculations, CPI helpers._

#### Core Managment
##### State Structures: _User stakes and pool aggregates._

##### Instruction Handlers: _stake, unstake, claim_rewards, upgrade_tier._

##### Events: _Tracks staking actions and rewards._

##### Utility Functions: **Calculates voting power for Governance, CPI to Token Management.**

##### Interactions: _Transfers tokens via Token Management, requests rewards from Treasury Management, provides voting power to Governance._

--------------------------------------------------------------------------------------------------------------------------

### 3. Governance
**Facilitates community-driven proposals and voting.**

**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Quorum threshold, voting period duration._

**errors.rs:** _ProposalExpired, InsufficientVotingPower._

**state/proposal.rs:** _Proposal data (description, deadline, actions)._

**state/vote.rs:** _User vote records._

**instructions/create_proposal.rs:** _Creates proposals._

**instructions/vote.rs:** _Records votes._

**instructions/execute_proposal.rs:** _Executes approved proposals._

**events.rs:** _ProposalCreatedEvent, VoteCastEvent, ProposalExecutedEvent._

**utils.rs:** _CPI to Staking, proposal execution helpers._

#### Core Management
##### State Structures: _Proposals and vote tallies._
##### Instruction Handlers: _create_proposal, vote, execute_proposal._
##### Events: _Logs proposal lifecycle events._
##### Utility Functions: _Queries Staking for voting power, executes actions via CPI._
##### Interactions: Queries Staking for voting power, updates Treasury Management or Vesting via proposals.
---------------------------------------------------------------------------------------------------------------
### 4. Airdrops
**Distributes tokens to eligible users based on staking activity.**

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Airdrop campaign parameters.

**errors.rs:** IneligibleUser, CampaignEnded.

**state/campaign.rs:** Campaign data (eligibility, total amount).

**instructions/create_campaign.rs:** Sets up airdrops.

**instructions/distribute_airdrop.rs:** Distributes tokens.

**events.rs:** AirdropDistributedEvent.

**utils.rs:** CPI to Staking and Treasury Management.

#### Core Management
##### State Structures: _Campaign details and distribution status._
##### Instruction Handlers: **create_campaign, distribute_airdrop.**
##### Events: _Logs distribution events._
##### Utility Functions: _Eligibility checks, CPI for funding._
##### Interactions: _Queries Staking for eligibility, requests funds from Treasury Management._
------------------------------------------------------------------------------------------------------------------------
### 5. Treasury Management
**Collects taxes and distributes funds to staking, airdrops, and vesting.**

**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Tax rates, pool addresses._

**errors.rs:** _InsufficientFunds, UnauthorizedWithdrawal._

**state/pool.rs:** _Treasury pool data (rewards, liquidity)._

**state/tax_config.rs:** _Tax settings._

**instructions/distribute_tax.rs:** _Allocates taxes._

**instructions/withdraw.rs:** _Funds other programs._

**instructions/update_tax_config.rs:** _Updates tax settings._

**events.rs:** _TaxDistributedEvent, WithdrawalEvent._

**utils.rs:** _CPI to Token Management._

#### Core Management
##### State Structures: _Treasury pools and tax configurations._
##### Instruction Handlers: _distribute_tax, withdraw, update_tax_config._
##### Events: _Logs tax and withdrawal actions._
##### Utility Functions: _Token transfer CPIs._
##### Interactions: _Receives taxes from Token Management, funds Staking, Airdrops, and Vesting._

-------------------------------------------------------------------------------------------------------------

### 6. Vesting
**Manages token release schedules for beneficiaries.**

**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Vesting cliffs, durations._

**errors.rs:** _VestingNotMatured, InvalidBeneficiary._

**state/vesting_schedule.rs:** _Vesting data (amount, schedule)._

**instructions/claim_vested_tokens.rs:** _Releases tokens._

**instructions/update_vesting_schedule.rs:** _Updates schedules._

**events.rs:** _VestedTokensClaimedEvent._

**utils.rs:** _CPI to Treasury Management._

#### Core Management
##### State Structures: _Vesting schedules and claim status._
##### Instruction Handlers: _claim_vested_tokens, update_vesting_schedule._
##### Events: _Logs token claims._
##### Utility Functions: _Token release CPIs._
##### **Interactions:** _Requests tokens from Treasury Management._
------------------------------------------------------------------------------------------------------------------------
### 7. Migration
**Handles token migration from old to new contracts.**

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Old and new token addresses.

**errors.rs:** MigrationNotInitiated, InvalidMigrationAmount.

**state/migration.rs:** Migration state (status, user records).

**instructions/initiate_migration.rs:** Starts migration.

**instructions/complete_migration.rs:** Completes migration.

**events.rs:** MigrationInitiatedEvent, MigrationCompletedEvent.

**utils.rs:** CPI to Token Management.

#### Core Management
##### State Structures: _Migration progress and user data._
##### Instruction Handlers: _initiate_migration, complete_migration._
##### Events: _Logs migration milestones._
##### Utility Functions: _Token burn/transfer CPIs._
##### Interactions: _Uses Token Management for token operations._
-------------------------------------------------------------------------------------------------------------------
## Key Interconnections

**The VeraLux programs interact through:**

**Cross-Program Invocations (CPIs):** For state changes (e.g., Staking calls Token Management for transfers).

**Data Queries:** For read-only access (e.g., Governance queries Staking for voting power).

**Events:** For frontend updates (e.g., TransferEvent from Token Management).

#### **Examples:**

**Token Management -- Treasury Management:** _Taxes flow from transfers to treasury._

**Staking -- Governance:** _Staking provides voting power for proposals._

**Treasury -- Management  Others:** _Funds Staking rewards, Airdrops, and Vesting releases._

**Migration -- Token Management:** _Manages token burns and transfers during migration._




