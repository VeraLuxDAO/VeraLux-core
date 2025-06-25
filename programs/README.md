**Detailed Program Structure**


Each program follows a consistent structure optimized for Solana development, leveraging the Anchor framework where applicable. Below, I’ll break down the purpose of each file and directory within a program, followed by specifics for each VeraLux contract.


**General Structure for Each Program**


**lib.rs:** 
Contains the declare_id! macro to define the program’s unique ID.
Defines the program entrypoint, routing instructions to their respective handlers.


**constants.rs:** Stores program-specific constants (e.g., fees, PDA seeds, configuration values).


**errors.rs:** Defines custom error codes using Anchor’s #[error_code] attribute for clear error handling.


**state/:** 

**mod.rs:** Re-exports all state structs for easy access.
Account files (e.g., user.rs, pool.rs): Define on-chain data structures using Anchor’s #[account] attribute.


**instructions/:** 

**mod.rs:** Re-exports instruction handlers.
Instruction files (e.g., stake.rs, transfer.rs): Contain #[derive(Accounts)] structs for account validation and handler functions for program logic.

**events.rs:** 
Defines #[event] structs for logging key actions, enabling real-time updates for frontends like the VeraLux Profile Page.

**utils.rs:** 
Includes helper functions, mathematical utilities, PDA derivations, and CPI calls to interact with other programs.



**1. Token Management Program**

Manages token transfers and burns, serving as the foundation for token interactions across the ecosystem.


**lib.rs:** Declares the program ID and entrypoint.

**constants.rs:** Token decimals, mint address, etc.

**errors.rs:** Errors like InsufficientBalance, Unauthorized.


**state/**

**mod.rs:** Re-exports.

**token.rs:** Defines token account states (e.g., user balances).


**instructions/**

**mod.rs:** Re-exports.

**transfer.rs:** Handles token transfers with tax collection logic (CPI to Treasury Management).

**burn.rs:** Handles token burning.

**events.rs:** TransferEvent, BurnEvent.

**utils.rs:** CPI helpers for SPL token interactions.


**Key Interactions:** 

Called by Staking, Treasury Management, and Vesting for token movements.



**2. Staking Program**

Handles staking, unstaking, and reward distribution for users.

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Staking tiers, reward rates, lock periods.

**errors.rs:** Errors like InvalidTier, LockPeriodNotElapsed.


**state/**

**mod.rs:** Re-exports.

**user.rs:** User staking data (amount, tier, lock period).

**pool.rs:** Staking pool data (total staked, reward rates).


**instructions/**

**mod.rs:** Re-exports.

**stake.rs:** Stakes tokens (CPI to Token Management).

**unstake.rs:** Unstakes tokens after lock period.

**claim_rewards.rs:** Distributes rewards (CPI to Treasury Management).

**upgrade_tier.rs:** Upgrades user staking tier.

**events.rs:** StakeEvent, UnstakeEvent, RewardClaimedEvent.

**utils.rs:** CPI calls to Token Management and Treasury Management, voting power calculations.

**Key Interactions:** 

Queries Token Management for transfers, Treasury Management for rewards, and provides voting power to Governance.




**3. Governance Program**

Manages proposals and voting, enabling community-driven decisions.

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Quorum threshold, voting period duration.

**errors.rs:** Errors like ProposalExpired, InsufficientVotingPower.


**state/**

**mod.rs:** Re-exports.

**proposal.rs:** Proposal data (description, voting deadline, actions).

**vote.rs:** User vote records.


**instructions/**

**mod.rs:** Re-exports.

**create_proposal.rs:** Creates a new proposal.

**vote.rs:** Records user votes (CPI to Staking for voting power).

**execute_proposal.rs:** Executes approved proposals (CPI to Treasury Management or others).

**events.rs:** ProposalCreatedEvent, VoteCastEvent, ProposalExecutedEvent.

**utils.rs:** CPI calls to Staking for voting power, helpers for proposal execution.

**Key Interactions:** 

Queries Staking for voting power, updates Treasury Management or Vesting via proposals.




**4. Airdrops Program**

Distributes tokens to eligible users based on staking data.

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Airdrop campaign parameters.

**errors.rs:** Errors like IneligibleUser, CampaignEnded.


**state/**

**mod.rs:** Re-exports.

**campaign.rs:** Campaign data (eligibility rules, total amount).


**instructions/**

**mod.rs:** Re-exports.

**create_campaign.rs:** Sets up a new airdrop.

**distribute_airdrop.rs:** Distributes tokens (CPI to Staking and Treasury Management).

**events.rs:** AirdropDistributedEvent.

**utils.rs:** CPI calls to Staking for eligibility, Treasury Management for funds.

**Key Interactions:** 

Queries Staking for eligibility, requests funds from Treasury Management.




**5. Treasury Management Program**

Collects taxes and manages fund distribution to staking, airdrops, and vesting.

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Tax rates, pool addresses.

**errors.rs:** Errors like InsufficientFunds, UnauthorizedWithdrawal.


**state/**

**mod.rs:** Re-exports.

**pool.rs:** Treasury pool data (staking rewards, liquidity).

**tax_config.rs:** Tax rate and allocation settings.


**instructions/**

**mod.rs:** Re-exports.

**distribute_tax.rs:** Distributes collected taxes (CPI from Token Management).

**withdraw.rs:** Withdraws funds to staking, airdrops, or vesting.

**update_tax_config.rs:** Updates tax settings (via Governance).

**events.rs:** TaxDistributedEvent, WithdrawalEvent.

**utils.rs:** CPI calls to Token Management for transfers.

**Key Interactions:** 

Receives taxes from Token Management, funds Staking, Airdrops, and Vesting.




**6. Vesting Program**

Manages token release schedules for beneficiaries.

**lib.rs:** Program ID and entrypoint.

**constants.rs:** Vesting cliffs, durations.

**errors.rs:** Errors like VestingNotMatured, InvalidBeneficiary.


**state/**

**mod.rs:** Re-exports.

**vesting_schedule.rs:** Vesting data (amount, release schedule).


**instructions/**

**mod.rs:** Re-exports.

**claim_vested_tokens.rs:** Releases vested tokens (CPI to Treasury Management).

**update_vesting_schedule.rs:** Updates schedules (via Governance).

**events.rs:** VestedTokensClaimedEvent.

**utils.rs:** CPI calls to Treasury Management for token releases.

**Key Interactions:** 

Requests token releases from Treasury Management.




**Key Interconnections and Optimizations**

To ensure the VeraLux ecosystem operates efficiently:

**CPI Calls:** Each program’s utils.rs includes CPI logic for secure interactions (e.g., Staking calls Token Management for transfers).

**Public Getter Functions:** Provide read-only access to state (e.g., Governance queries Staking’s voting power).

**Event Emissions:** Enable real-time updates for the VeraLux website’s Profile Page.


**Optimizations**

**Modularity:** Separate programs allow independent upgrades and reduce complexity.

**Shared Testing:** The tests/ directory at the root supports integration tests to verify inter-program behavior.

**CPI Organization:** For programs with heavy CPI usage (e.g., Treasury Management), a cpi/ subdirectory could organize calls (e.g., cpi/token.rs).

**Event Consistency:** Standardized event naming ensures uniform frontend integration.


**This structure provides an optimal foundation for VeraLux, balancing scalability, maintainability, and security while meeting the ecosystem’s multi-contract needs. Each program is self-contained yet interconnected, supporting VeraLux’s vision of a robust, community-driven platform.**


VeraLux Program Structure: Detailed Technical Overview
Table of Contents
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

Introduction
The VeraLux ecosystem is a decentralized Web3 platform built on Solana, powered by the LUX token. It consists of seven core programs: Token Management, Staking, Governance, Airdrops, Treasury Management, Vesting, and Migration. This README.md provides a detailed technical overview of each program, including their roles, key components, and interactions, ensuring developers have a clear understanding of the architecture and can easily navigate to specific details.
Program Architecture Overview
Each VeraLux program is built using the Anchor framework for Solana, following a modular structure that balances independence and interoperability. This allows developers to work on individual programs while ensuring seamless integration across the ecosystem. Below is the typical file structure for each program:
lib.rs: Defines the program ID and entrypoint, routing instructions to handlers.

constants.rs: Stores program-specific constants (e.g., fees, PDA seeds).

errors.rs: Custom error codes (e.g., #[error_code] in Anchor).

state/  
mod.rs: Re-exports state structs.  

Account files (e.g., user.rs): Define on-chain data structures with #[account].

instructions/  
mod.rs: Re-exports instruction handlers.  

Instruction files (e.g., stake.rs): Contain #[derive(Accounts)] and handler logic.

events.rs: Defines #[event] structs for logging actions.

utils.rs: Includes utility functions, CPI calls, and PDA derivations.

This structure ensures consistency, making it easy for developers to locate specific components when revisiting the codebase.
Detailed Program Breakdown
1. Token Management
Role: Manages token transfers and burns, serving as the foundation for all token-related operations in VeraLux.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Token decimals, mint address.

errors.rs: InsufficientBalance, Unauthorized.

state/token.rs: Token account states (e.g., user balances).

instructions/transfer.rs: Handles transfers with tax logic.

instructions/burn.rs: Handles token burning.

events.rs: TransferEvent, BurnEvent.

utils.rs: SPL token CPI helpers.

State Structures: Tracks user token balances and metadata.

Instruction Handlers: transfer (includes tax CPI to Treasury Management), burn.

Events: Logs transfers and burns for frontend integration.

Utility Functions: SPL token CPI wrappers.

Interactions: Called by Staking, Treasury Management, Vesting, and Migration for token operations.

2. Staking
Role: Manages staking, unstaking, and reward distribution for LUX token holders.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Staking tiers, reward rates, lock periods.

errors.rs: InvalidTier, LockPeriodNotElapsed.

state/user.rs: User staking data (amount, tier, lock period).

state/pool.rs: Staking pool data (total staked, reward rates).

instructions/stake.rs: Stakes tokens.

instructions/unstake.rs: Unstakes after lock period.

instructions/claim_rewards.rs: Distributes rewards.

instructions/upgrade_tier.rs: Upgrades staking tier.

events.rs: StakeEvent, UnstakeEvent, RewardClaimedEvent.

utils.rs: Voting power calculations, CPI helpers.

State Structures: User stakes and pool aggregates.

Instruction Handlers: stake, unstake, claim_rewards, upgrade_tier.

Events: Tracks staking actions and rewards.

Utility Functions: Calculates voting power for Governance, CPI to Token Management.

Interactions: Transfers tokens via Token Management, requests rewards from Treasury Management, provides voting power to Governance.

3. Governance
Role: Facilitates community-driven proposals and voting.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Quorum threshold, voting period duration.

errors.rs: ProposalExpired, InsufficientVotingPower.

state/proposal.rs: Proposal data (description, deadline, actions).

state/vote.rs: User vote records.

instructions/create_proposal.rs: Creates proposals.

instructions/vote.rs: Records votes.

instructions/execute_proposal.rs: Executes approved proposals.

events.rs: ProposalCreatedEvent, VoteCastEvent, ProposalExecutedEvent.

utils.rs: CPI to Staking, proposal execution helpers.

State Structures: Proposals and vote tallies.

Instruction Handlers: create_proposal, vote, execute_proposal.

Events: Logs proposal lifecycle events.

Utility Functions: Queries Staking for voting power, executes actions via CPI.

Interactions: Queries Staking for voting power, updates Treasury Management or Vesting via proposals.

4. Airdrops
Role: Distributes tokens to eligible users based on staking activity.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Airdrop campaign parameters.

errors.rs: IneligibleUser, CampaignEnded.

state/campaign.rs: Campaign data (eligibility, total amount).

instructions/create_campaign.rs: Sets up airdrops.

instructions/distribute_airdrop.rs: Distributes tokens.

events.rs: AirdropDistributedEvent.

utils.rs: CPI to Staking and Treasury Management.

State Structures: Campaign details and distribution status.

Instruction Handlers: create_campaign, distribute_airdrop.

Events: Logs distribution events.

Utility Functions: Eligibility checks, CPI for funding.

Interactions: Queries Staking for eligibility, requests funds from Treasury Management.

5. Treasury Management
Role: Collects taxes and distributes funds to staking, airdrops, and vesting.
Key Files:
lib.rs: Program ID and entrypoint.

constants.rs: Tax rates, pool addresses.

errors.rs: InsufficientFunds, UnauthorizedWithdrawal.

state/pool.rs: Treasury pool data (rewards, liquidity).

state/tax_config.rs: Tax settings.

instructions/distribute_tax.rs: Allocates taxes.

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




