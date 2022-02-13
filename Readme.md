# Nomination contract
Largely based on [https://github.com/EmelyanenkoK/nominator_pool/tree/v2](nominator_pool) this contract is solve isolated simple problem.

**Given that we have one validator and nominator create trustless solution when nominator may lend assets to validator and share reward.**

This contract bases on a few simplifications:
1. Only one nominator (which may and should be a nomination-pool contract).
2. Contract participates only in one round and is locked till it gets stake back. Thus two contracts are needed to validate all the time.
3. Optimistic but still robust approach of ignoring elector responses.

# Guarantees
Controler guarantees the following:
1. Validator can not withdraw user funds
2. Validator cover regular fines as advertised (publicly available `controller_cover_ability` variable)
3. Profit is shared as advertised (publicly available `controller_reward_share` variable)
4. Funds may be recovered even if validator is uncooperative

# Features
1. Fully wallet compatible (no changes on validation scripts)
2. Nominator notifications


## Bonus
Restricted wallet which fully compatible with v3 wallet but allows to send funds to elector, config and owner only. That way owner private key may be stored in cold storage
