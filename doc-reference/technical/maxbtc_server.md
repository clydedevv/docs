## **Overview**

This server implements a comprehensive JLP (Jupiter Liquidity Provider) token strategy execution system. The architecture is based on 6 microservices (+1 non-process) that coordinate to manage portfolio exposure, execute trades, and maintain collateral requirements. The system relies on two primary sources of truth:

1. **On-chain and Exchange Data**: Real-time information from Solana smart contracts, Ethereum Smart contract and Binance exchange accounts
2. **Persistent Database**: MongoDB collections that track system state, exposures, and execution history

## **Architecture**

The server is built in Rust and follows a modular design pattern. Each microservice has a specific responsibility within the strategy execution workflow. Communication between modules happens through shared database state and the global JLP token tracking system. The application uses Axum for HTTP endpoints, asynchronous Tokio tasks for background processes, and direct RPC/WebSocket connections to blockchain networks and exchanges

![image.png](attachment:c538423d-5320-413d-ac23-f28d33cfd1a9:image.png)

[Untitled Diagram](https://drive.google.com/file/d/1oInF0pqiX5Tg7oOJ8CuBC9JgsJwOv4m3/view?usp=sharing)

## **Microservices**

### **1. Input Module (input_processing/)**

- Provides an estimate of Buy price for the system, which incorporates the fees for getting into the system (example, 1.006 BTC ~ 1 maxBTC) —> Oracle / SC
- Listens to any deposit signal and update SC [Maybe valid for a day?] —>SC
- Processes initial configuration and updates the main status document —>MongoDB
- Converts 70% of assets to USDC for JLP buying and buy BTC on futures - entering delta hedge —>Binance
- Handles JLP position entry in tranches —>Multi-sig
- Enters hedge positions (SOL, ETH, BTC) on centralized exchanges in tranches —> Binance

### **2. Cefi-Defi Exposure Management (exposure_management/cefi_defi_delta.rs)**

- Runs delta exposure calculations every minute —>MongoDB / SC (SOL)
- Executes trades to close delta mismatches —> Binance
- Updates main documentation and logs execution details —>MongoDB

### **3. Fee Hedge (exposure_management/fees_earned_delta.rs) —> Independent**

- Store value of system every 30 mins —> MongoDB
- Every 30 minutes to check unhedged delta arising from protocol fees —> Binance / MongoDB

### **4. Collateral Rebalancing (collateral/asset_rebalancing) —> For Kai**

- Continuously monitors collateral-to-value ratio (target >88%) —> SC (SOL) / CEX / MongoDB
- Sells JLP positions —> Multi-sig
- Unwinds SOL, ETH, BTC hedges —> CEX
- Transfers assets to centralized exchange futures/portfolio margin accounts —> Multi-sig / CEX
- Generates comprehensive reporting documentation —> MongoDB
- Update main documentation —> MongoDB

### **5. Exit Modules (exit_processing)**

- Provide an estimate of exit slippage or Sell Price (example, 1 maxBTC ~ 0.994 BTC) —> Oracle / SC
- Monitors smart contract for withdrawal instructions via WebSockets —> SC
- Sells JLP positions —> Multi-sig
- Unwinds SOL, ETH, BTC hedges —> CEX
- Transfers assets to centralized exchanges —> Multi-sig
- Unhedges BTC delta positions —> CEX
- Sends required assets to smart contract —> CEX
- Update Main documentation —> MongoDB

### **6. Collateral Management (collateral/asset_management ) —> CEX dependent**

- Ensures USDT balance remains above 1% of open positions —> MongoDB
- Executes BTC sales for USDT when necessary
- Maintains delta neutrality by adjusting hedge positions
- Creates and updates reporting documentation

## Non - process Microservices

### **1. Execution - CEX dependent (execution/)**

- Looks for depth of order book and break the order into smaller orders and send accordingly
- Entering and unwinding Delta hedge - entering simultaneously with minimal slippage

### 

## **Integration Points**

The system integrates with several external services:

- **CEX (Binance)**: Real-time price feeds and order execution via WebSockets
- **Smart Contract**: On-chain position management and custody information
- **MongoDB**
- **Squad Multi-sig**: Secure transaction approval workflow

|  | CEX | SC (ETH) | SC (SOL) | MongoDB | Multi-sig |
| --- | --- | --- | --- | --- | --- |
| **1. Input Module** | Yes | Yes | - | Yes | Yes |
| **2. Cefi-Defi Exposure Management** | Yes | - | Yes | Yes | Yes |
| **3. Fee Hedge** | Yes | - | Yes | Yes | Yes |
| **4. Collateral Management** | Yes | - | - | Yes | - |
| **5. Collateral Rebalancing** | Yes | - | Yes | Yes | Yes |
| **6. Exit Modules** | Yes | Yes | - | Yes | Yes |

## **Implementation Details**

The codebase is organized into logical modules:

- src/rpc/: Contains Solana and other blockchain RPC client implementations
- src/external_ws/: WebSocket clients for exchange data and order execution
- src/models/: MongoDB data models for persisting strategy state
- src/fetch_systems/: Data retrieval from various external systems
- src/exposure_management/: Core strategy execution logic
- src/execution/: Trade execution and order management

The server uses a shared global state for JLP token tracking (JLP_TOKENS) which acts as an in-memory cache that is periodically verified against on-chain data.

## **Deployment and Configuration**

The server uses environment variables and configuration files for deployment settings. It can operate in development, test, or production modes with appropriate security controls for each environment.

This architecture enables robust, scalable management of the JLP strategy with redundancy, error handling, and complete audit trails for all operations.

## Security Concerns

1. Binance API Keys : IPs are whitelisted and only allowed to trade. Read only keys can work without IP whitelisting for monitoring - Ceffu
2. Reduce the number of microservices : Idea is to make it modular, different devs can work independently. Can run on a single server for sure!
3. Microservices are made mutually exhaustive to each other - sync if required, but can be done with a lag, we do it on timestamp based. Example, input module should be in sync with Cefi-Defi Exposure Management
4. Creating Main doc status - part of data monitoring, persistence
5. System reset - should resume based on data from CEX’s position and JLP position in the mulit-sig
6. Binance security : whitelist address - Multi-sig or the ETH SC, Ceffu setup 

Scope for Multi-sig - 

- Buying capability of JLP
- Sell Capability of JLP
- Transfer to CEX
- Whitelist CEX addresss