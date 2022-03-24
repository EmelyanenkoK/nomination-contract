# Nomination contract system
Nomination contract system consist of two isolated modules:
* validator-controller - contract which handle sending requests to elector and tracking profit-loss
* profit-pool - contract which handle pooling user funds: it combines funds of all nominators, send it to validator-controller and distribute reward (on demand).



## Validator-controller
This contract solves isolated simple problem:

**Given that we have one validator and nominator create trustless solution when nominator may lend assets to validator and share reward.**

This contract bases on a few simplifications:
1. Only one nominator (which may and should be a nomination-pool contract).
2. Contract participates only in one round and is locked till it gets stake back. Thus two contracts are needed to validate all the time.
3. Optimistic but still robust approach of ignoring elector responses.

### Guarantees
Controler guarantees the following:
1. Validator can not withdraw user funds
2. Validator cover regular fines as advertised (publicly available `controller_cover_ability` variable)
3. Profit is shared as advertised (publicly available `controller_reward_share` variable)
4. Funds may be recovered even if validator is uncooperative

### Features
1. Fully wallet compatible (no changes on validation scripts)
2. Nominator notifications



## Profit-pool
This contract gets funds from user and send [jettons](https://github.com/ton-blockchain/token-contract) in exchange. Those jettons represent share of all pool' money (for instance if there is 10 jettons and user has 2, it means he own 20% of pool' funds).

However, since stake is locked during validation, it is impossible to immediately send funds to validation or withdraw them. To handle this, upon receiving funds from nominator profit-pool mints the awPJ (awaited pool jettons). Rate of PJ/awPJ is  determined on the end of validation round and awPJ can be redeemed to PJ in any moment after that.
The same way when nominator wants to withdraw funds, it immediately gets awTON (awaited TONs) which can be redeemed to TON after end of the round (when relevant awTON/TON rate became known).

### new round - new token
Unfortunately, since PJ/awPJ as well as TON/awTON rates change from round to round, awaited tokens not equal to each other. That is why for each round a new kind of awPJ or awTON is created (but only if there were deposits/withdrawals).

Distribution of PJ to awPJ owners or TON to awTON owners happens as follows:
upon end of the round when PJ/awPJ rate became known profit pool mints PJ and transfer them to awPJ minter contract. Then each owner of awPJ may burn awPJ and gets his share of PJ on minter. The same goes for TONs and awTONs (the only difference is that TONs are obviously not minted but returned from validator-controller SC at the end of the round).

## Bonus: Restricted wallet
Restricted wallet which fully compatible with v3 wallet but allows to send funds to elector, config and owner only. That way owner private key may be stored in cold storage
