### Goal

Enable money-markets and vault managers to exit sizable MaxBTC positions atomically on Neutron, and within minutes from any chain, instead of waiting for the ~24-hour standard redemption cycle.

### Overview

MaxBTC keeps a small slice of wBTC deposits—about ten percent—in a separate “buffer” vault that holds wrapped BTC on Neutron. That vault continuously posts single-sided wBTC limit orders on Duality at a preset premium of roughly one-to-two percent above the maxBTC redemption price. 

Whenever a lender, liquidator, or vault manager needs an immediate exit, they simply market-sell their MaxBTC into those orders and receive wBTC in the same transaction. Handling this logic via Duality instead of a custom liquidation mechanism provides a few benefits:

- Improved execution quality for liquidators (aggregates public and protocol liquidity and fills the order using the best priced liquidity first)
- A single, simpler integration for liquidity and liquidations
- Cross-chain availability via routing/aggregator protocols such as Skip Go, LiFi, etc.

The main vault automatically tops the buffer up when deposits grow and pulls idle funds back into the strategy when scheduled withdrawals shrink TVL, so the reserve stays near its target size.

### Why hold the buffer on Neutron vs Ethereum?

Duplicating the buffer across networks is capital inefficient and would delay implementation. Between Neutron and Ethereum, Neutron is the superior choice because of the composability with Duality, and the faster finality:

- Neutron → Ethereum / Other networks settles in less than 30s
- Ethereum → Neutron / Other networks settles in ~30 minutes