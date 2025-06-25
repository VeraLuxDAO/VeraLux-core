# **VeraLux Program Structure: Detailed Technical Overview**

## **Table of Contents**

Introduction (#introduction)

Program Architecture Overview (#program-architecture-overview)

Detailed Program Breakdown (#detailed-program-breakdown)

  1. Token Management (#1-token-management)

  2. Staking (#2-staking)

  3. Governance (#3-governance)

  4. Airdrops (#4-airdrops)

  5. Treasury Management (#5-treasury-management)

  6. Vesting (#6-vesting)

  7. Migration (#7-migration)

Key Interconnections (#key-interconnections)


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



### 3. Governance
**Facilitates community-driven proposals and voting.**

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Quorum threshold, voting period duration.

**errors.rs:** ProposalExpired, InsufficientVotingPower.

**state/proposal.rs:** Proposal data (description, deadline, actions).

**state/vote.rs:** User vote records.

**instructions/create_proposal.rs:** Creates proposals.

**instructions/vote.rs:** Records votes.

**instructions/execute_proposal.rs:** Executes approved proposals.

**events.rs:** ProposalCreatedEvent, VoteCastEvent, ProposalExecutedEvent.

**utils.rs:** CPI to Staking, proposal execution helpers.

#### Core Management
##### State Structures: _Proposals and vote tallies._
##### Instruction Handlers: _create_proposal, vote, execute_proposal._
##### Events: _Logs proposal lifecycle events._
##### Utility Functions: _Queries Staking for voting power, executes actions via CPI._
##### Interactions: Queries Staking for voting power, updates Treasury Management or Vesting via proposals.

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

### 5. Treasury Management
**Collects taxes and distributes funds to staking, airdrops, and vesting.**

**lib.rs:** _Program ID and entrypoint._

**constants.rs:** _Tax rates, pool addresses._

**errors.rs:** _InsufficientFunds, UnauthorizedWithdrawal._

**state/pool.rs:** _Treasury pool data (rewards, liquidity)._

**state/tax_config.rs:** _Tax settings._

**instructions/distribute_tax.rs:** _Allocates taxes._

instructions/withdraw.rs: Funds other programs.

instructions/update_tax_config.rs: Updates tax settings.

events.rs: TaxDistributedEvent, WithdrawalEvent.

utils.rs: CPI to Token Management.

State Structures: Treasury pools and tax configurations.

Instruction Handlers: distribute_tax, withdraw, update_tax_config.

Events: Logs tax and withdrawal actions.

Utility Functions: Token transfer CPIs.

Interactions: Receives taxes from Token Management, funds Staking, Airdrops, and Vesting.

6. Vesting
Role: Manages token release schedules for beneficiaries.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Vesting cliffs, durations.

errors.rs: VestingNotMatured, InvalidBeneficiary.

state/vesting_schedule.rs: Vesting data (amount, schedule).

instructions/claim_vested_tokens.rs: Releases tokens.

instructions/update_vesting_schedule.rs: Updates schedules.

events.rs: VestedTokensClaimedEvent.

utils.rs: CPI to Treasury Management.

State Structures: Vesting schedules and claim status.

Instruction Handlers: claim_vested_tokens, update_vesting_schedule.

Events: Logs token claims.

Utility Functions: Token release CPIs.

Interactions: Requests tokens from Treasury Management.

7. Migration
Role: Handles token migration from old to new contracts.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Old and new token addresses.

errors.rs: MigrationNotInitiated, InvalidMigrationAmount.

state/migration.rs: Migration state (status, user records).

instructions/initiate_migration.rs: Starts migration.

instructions/complete_migration.rs: Completes migration.

events.rs: MigrationInitiatedEvent, MigrationCompletedEvent.

utils.rs: CPI to Token Management.

State Structures: Migration progress and user data.

Instruction Handlers: initiate_migration, complete_migration.

Events: Logs migration milestones.

Utility Functions: Token burn/transfer CPIs.

Interactions: Uses Token Management for token operations.

Key Interconnections
The VeraLux programs interact through:
Cross-Program Invocations (CPIs): For state changes (e.g., Staking calls Token Management for transfers).

Data Queries: For read-only access (e.g., Governance queries Staking for voting power).

Events: For frontend updates (e.g., TransferEvent from Token Management).

Examples:
Token Management  Treasury Management: Taxes flow from transfers to treasury.

Staking  Governance: Staking provides voting power for proposals.

Treasury Management  Others: Funds Staking rewards, Airdrops, and Vesting releases.

Migration  Token Management: Manages token burns and transfers during migration.




