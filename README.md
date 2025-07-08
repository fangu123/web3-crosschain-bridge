 Web3 跨链桥项目说明文档 / Web3 Cross-chain Bridge Project Documentation

 一、项目简介 / 1. Project Overview

本项目为一个基于Web3的跨链桥系统，旨在实现不同区块链网络间的资产跨链转移。项目包含后端服务、智能合约、前端交互三大部分，支持资产流动性管理、跨链兑换、历史记录查询等功能。

This project is a Web3-based cross-chain bridge system designed to enable asset transfers between different blockchain networks. It consists of backend services, smart contracts, and frontend interaction, supporting liquidity management, cross-chain swaps, and history queries.

---

 二、目录结构说明 / 2. Directory Structure

```
web3-crosschain-bridge/
├── backend/            后端服务 / Backend services
│   ├── src/
│   │   ├── api/        API接口 / API endpoints
│   │   ├── models/     数据模型 / Data models
│   │   ├── services/   区块链服务、事件监听 / Blockchain services, event listeners
│   │   └── app.js      应用入口 / App entry
│   ├── package.json    后端依赖 / Backend dependencies
├── contracts/          智能合约 / Smart contracts
│   ├── pools/          跨链池合约 / Cross-chain pool contracts
│   ├── adapters/       桥接适配器合约 / Bridge adapter contracts
│   ├── PriceOracle.sol 价格预言机 / Price oracle
│   └── Router.sol      路由合约 / Router contract
├── frontend/           前端项目 / Frontend project
│   └── src/
│       ├── pages/      页面 / Pages
│       ├── components/ 组件 / Components
│       ├── api/        前端API / Frontend API
│       └── utils/      工具函数 / Utilities
├── scripts/            合约部署脚本 / Contract deployment scripts
├── docs/               文档 / Documentation
└── 需求文档.txt        需求说明 / Requirements
```

---

 三、后端架构与主要功能 / 3. Backend Architecture & Main Features

 1. 技术栈 / Technology Stack
- Node.js
- ethers.js（区块链交互 / Blockchain interaction）
- dotenv（环境变量 / Environment variables）

 2. 主要模块说明 / Main Modules

 2.1 app.js
- 应用入口，负责加载API路由、初始化服务。
- App entry, responsible for loading API routes and initializing services.

 2.2 models
- LiquidityOp.js：流动性操作数据模型 / Liquidity operation data model
- Transaction.js：跨链交易数据模型 / Cross-chain transaction data model
- index.js：模型聚合导出 / Model aggregator export

 2.3 services
- ethService.js：以太坊链交互服务，负责合约调用、交易发送等 / Ethereum service for contract calls and transactions
- eventListener.js：区块链事件监听，处理链上事件回调 / Blockchain event listener for on-chain event callbacks

 2.4 api
- crosschain.js：跨链相关API（如发起跨链、查询状态）/ Cross-chain APIs (swap, status query)
- liquidity.js：流动性管理API（如添加/移除流动性）/ Liquidity management APIs (add/remove liquidity)
- history.js：历史记录API（如查询用户操作历史）/ History APIs (user operation history)

---

 3. API接口详细说明（补充）/ 3. Detailed API Endpoints (Supplement)

 3.1 跨链相关接口（crosschain.js）/ Cross-chain APIs (crosschain.js)

 1. 发起跨链兑换 / Initiate Cross-chain Swap

- **接口 / Endpoint**：`POST /api/crosschain/swap`
- **功能 / Function**：发起资产从源链到目标链的跨链兑换 / Initiate asset swap from source chain to target chain
- **请求参数 / Request Parameters**（JSON格式 / JSON format）：

  | 参数名     | 类型   | 必填 | 说明         | Name      | Type   | Required | Description           |
  | ----------| ------ | ---- | ------------|-----------|--------|----------|----------------------|
  | fromChain | string | 是   | 源链ID      | fromChain | string | Yes      | Source chain ID      |
  | toChain   | string | 是   | 目标链ID    | toChain   | string | Yes      | Target chain ID      |
  | asset     | string | 是   | 资产合约地址 | asset     | string | Yes      | Asset contract addr  |
  | amount    | string | 是   | 兑换数量（单位：最小单位）| amount    | string | Yes      | Amount (in smallest unit) |
  | user      | string | 是   | 用户钱包地址 | user      | string | Yes      | User wallet address  |

- **请求示例 / Request Example**：
  ```json
  {
    "fromChain": "1",
    "toChain": "56",
    "asset": "0x1234...abcd",
    "amount": "1000000000000000000",
    "user": "0x5678...efgh"
  }
  ```

- **响应示例 / Response Example**：
  ```json
  {
    "success": true,
    "txHash": "0xabc123...",
    "message": "Swap transaction submitted"
  }
  ```

- **错误响应示例 / Error Response Example**：
  ```json
  {
    "success": false,
    "message": "Insufficient balance"
  }
  ```

---

 2. 查询跨链交易状态 / Query Cross-chain Transaction Status

- **接口 / Endpoint**：`GET /api/crosschain/status?txHash=xxx`
- **功能 / Function**：根据交易哈希查询跨链交易状态 / Query cross-chain transaction status by txHash
- **请求参数 / Request Parameters**：

  | 参数名 | 类型   | 必填 | 说明     | Name   | Type   | Required | Description   |
  | ------| ------ | ---- | --------|--------|--------|----------|---------------|
  | txHash| string | 是   | 交易哈希 | txHash | string | Yes      | Transaction hash |

- **响应示例 / Response Example**：
  ```json
  {
    "status": "pending",
    "detail": "Waiting for confirmation"
  }
  ```

---

 3.2 流动性相关接口（liquidity.js）/ Liquidity APIs (liquidity.js)

 1. 添加流动性 / Add Liquidity

- **接口 / Endpoint**：`POST /api/liquidity/add`
- **功能 / Function**：为指定链和资产添加流动性 / Add liquidity for a specific chain and asset
- **请求参数 / Request Parameters**（JSON格式 / JSON format）：

  | 参数名  | 类型   | 必填 | 说明         | Name    | Type   | Required | Description         |
  | -------| ------ | ---- | ------------|---------|--------|----------|--------------------|
  | chainId| string | 是   | 链ID        | chainId | string | Yes      | Chain ID           |
  | asset  | string | 是   | 资产合约地址 | asset   | string | Yes      | Asset contract addr|
  | amount | string | 是   | 数量（最小单位）| amount  | string | Yes      | Amount (smallest unit) |
  | user   | string | 是   | 用户地址     | user    | string | Yes      | User address       |

- **请求示例 / Request Example**：
  ```json
  {
    "chainId": "1",
    "asset": "0x1234...abcd",
    "amount": "5000000000000000000",
    "user": "0x5678...efgh"
  }
  ```

- **响应示例 / Response Example**：
  ```json
  {
    "success": true,
    "txHash": "0xdef456...",
    "message": "Liquidity added"
  }
  ```

---

 2. 移除流动性 / Remove Liquidity

- **接口 / Endpoint**：`POST /api/liquidity/remove`
- **功能 / Function**：移除指定链和资产的流动性 / Remove liquidity for a specific chain and asset
- **请求参数 / Request Parameters**（同上 / Same as above）

- **响应示例 / Response Example**：
  ```json
  {
    "success": true,
    "txHash": "0xghi789...",
    "message": "Liquidity removed"
  }
  ```

---

 3.3 历史记录接口（history.js）/ History APIs (history.js)

 1. 查询用户历史操作 / Query User Operation History

- **接口 / Endpoint**：`GET /api/history?user=xxx`
- **功能 / Function**：查询指定用户的所有历史操作记录 / Query all operation history for a user
- **请求参数 / Request Parameters**：

  | 参数名 | 类型   | 必填 | 说明     | Name | Type   | Required | Description |
  | ------| ------ | ---- | --------|------|--------|----------|-------------|
  | user  | string | 是   | 用户地址 | user | string | Yes      | User address|

- **响应示例 / Response Example**：
  ```json
  {
    "history": [
      {
        "type": "swap",
        "chain": "1",
        "asset": "0x1234...abcd",
        "amount": "1000000000000000000",
        "status": "success",
        "timestamp": 1710000000
      },
      {
        "type": "addLiquidity",
        "chain": "56",
        "asset": "0x5678...efgh",
        "amount": "5000000000000000000",
        "status": "pending",
        "timestamp": 1710001000
      }
    ]
  }
  ```

---

 四、智能合约设计 / 4. Smart Contract Design

 1. 合约列表与功能 / Contract List & Functions

 1.1 CrossChainPool.sol
- 功能：管理跨链池的资产流动性，记录用户存取款
- Function: Manage cross-chain pool liquidity, record user deposits/withdrawals
- 主要方法 / Main Methods：
  - `addLiquidity(address asset, uint256 amount)`: 添加流动性 / Add liquidity
  - `removeLiquidity(address asset, uint256 amount)`: 移除流动性 / Remove liquidity
  - `getPoolBalance(address asset)`: 查询池余额 / Get pool balance

 1.2 BridgeAdapter.sol
- 功能：桥接不同链的资产转移，适配不同跨链协议
- Function: Bridge asset transfer between chains, adapt to different protocols
- 主要方法 / Main Methods：
  - `bridgeAsset(address from, address to, uint256 amount)`: 发起资产桥接 / Initiate asset bridging
  - `onBridgeCompleted(bytes32 txHash)`: 处理桥接完成事件 / Handle bridge completion event

 1.3 PriceOracle.sol
- 功能：提供资产价格预言机服务
- Function: Provide price oracle service
- 主要方法 / Main Methods：
  - `getPrice(address asset)`: 查询资产价格 / Get asset price
  - `updatePrice(address asset, uint256 price)`: 更新资产价格 / Update asset price

 1.4 Router.sol
- 功能：路由跨链请求，协调各模块
- Function: Route cross-chain requests, coordinate modules
- 主要方法 / Main Methods：
  - `swap(address fromChain, address toChain, address asset, uint256 amount)`: 跨链兑换 / Cross-chain swap

 2. 合约部署脚本 / Contract Deployment Scripts

- `deploy-bridgeAdapter.js`：部署BridgeAdapter合约 / Deploy BridgeAdapter contract
- `deploy-priceOracle.js`：部署PriceOracle合约 / Deploy PriceOracle contract

---

 五、前端架构与主要页面 / 5. Frontend Architecture & Main Pages

 1. 页面（pages）/ Pages

- `Swap.tsx`：跨链兑换页面，用户选择源链、目标链、资产及数量，发起跨链操作
- `Swap.tsx`: Cross-chain swap page, users select source/target chain, asset, amount, and initiate swap
- `Liquidity.tsx`：流动性管理页面，用户添加/移除流动性
- `Liquidity.tsx`: Liquidity management page, users add/remove liquidity

 2. 组件（components）/ Components

- `ChainSelector.tsx`：链选择器组件 / Chain selector component
- `AssetSelector.tsx`：资产选择器组件 / Asset selector component

 3. 前端API（api/crosschain.ts）/ Frontend API (api/crosschain.ts)

- 封装了与后端跨链相关的API请求 / Encapsulates cross-chain related API requests to backend
- 负责发起swap、查询状态等操作 / Responsible for swap initiation, status query, etc.

 4. 工具函数（utils/erc20.ts）/ Utility Functions (utils/erc20.ts)

- 提供ERC20代币相关的工具方法，如余额查询、授权等 / Provides ERC20 utility functions, e.g., balance query, approval

---

 六、业务流程与数据流 / 6. Business Flow & Data Stream

1. 用户在前端页面选择跨链兑换或流动性操作，填写相关信息。
2. 前端通过API将请求发送到后端。
3. 后端调用区块链服务（ethService.js），与智能合约交互，发起链上操作。
4. eventListener.js监听链上事件，更新操作状态。
5. 操作结果通过API返回前端，前端展示给用户。
6. 历史操作通过history接口查询，便于用户追溯。

1. User selects cross-chain swap or liquidity operation on the frontend and fills in the information.
2. Frontend sends the request to the backend via API.
3. Backend calls blockchain service (ethService.js), interacts with smart contracts, and initiates on-chain operations.
4. eventListener.js listens to on-chain events and updates operation status.
5. Operation results are returned to the frontend via API and displayed to the user.
6. User can query operation history via the history API.

---

 七、启动与部署说明 / 7. Startup & Deployment Guide

 1. 环境要求 / Environment Requirements

- Node.js 16.x 及以上 / Node.js 16.x or above
- npm 8.x 及以上 / npm 8.x or above

 2. 依赖安装 / Dependency Installation

后端依赖（在 backend 目录下）：
Backend dependencies (in backend directory):
```bash
cd backend
npm install
```

前端依赖（如有 package.json，类似操作）：
Frontend dependencies (if package.json exists):
```bash
cd frontend
npm install
```

 3. 启动后端服务 / Start Backend Service

（假设 app.js 作为入口，需补充启动脚本）
(Assuming app.js is the entry, add start script if needed)
```bash
node src/app.js
```

 4. 部署智能合约 / Deploy Smart Contracts

在项目根目录下运行部署脚本，例如：
Run deployment scripts in project root, e.g.:
```bash
node scripts/deploy-bridgeAdapter.js
node scripts/deploy-priceOracle.js
```

 5. 配置环境变量 / Configure Environment Variables

在 backend 目录下创建 `.env` 文件，配置区块链节点、私钥等敏感信息。
Create `.env` in backend directory to configure blockchain node, private key, etc.

---

 八、补充说明与建议 / 8. Additional Notes & Suggestions

- 建议完善前端 package.json 及启动脚本，便于一键启动。
- It is recommended to complete frontend package.json and start scripts for one-click startup.
- 后端可增加API鉴权、参数校验等安全措施。
- Backend can add API authentication, parameter validation, and other security measures.
- 合约部署后需记录合约地址，前后端需保持同步。
- After contract deployment, record contract addresses and keep frontend/backend in sync.
- 建议补充单元测试，提升系统健壮性。
- Add unit tests to improve system robustness.

---

如需进一步细化某一部分（如API参数示例、合约代码注释等），请告知！
If you need further details (e.g., API parameter examples, contract code comments), please let me know!
