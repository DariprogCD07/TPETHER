🌀 DexSwap - Detailed Explanation

Overview:
----------
DexSwap is a decentralized exchange (DEX) smart contract implemented in Solidity. Inspired by the mechanics of Uniswap, it enables users to:

1. Provide liquidity to a pool of two ERC-20 tokens.
2. Withdraw liquidity from existing pools.
3. Swap one token for another with automated pricing.
4. Retrieve token prices and estimated swap results.

Core Concepts:
--------------

1. Liquidity Pools:
   - Each token pair (tokenX, tokenY) is linked to a `PoolData` structure.
   - This structure includes:
     • `totalLiquidity`: Total liquidity tokens issued.
     • `liquidityBalance`: Mapping of user addresses to their liquidity shares.
     • `reserves`: Contains token reserves (`tokenX` and `tokenY`) as 128-bit values.

2. Events:
   - `LiquidityProvisioned`: Emitted when a user adds liquidity to a pool.
   - `LiquidityWithdrawn`: Emitted when liquidity is removed by a user.
   - `SwapExecuted`: Emitted after a successful token exchange.

Functions:
----------

1. `addLiquidity(tokenX, tokenY, desiredX, desiredY, minX, minY, recipient, deadline)`
   - Lets a user supply liquidity to a token pair pool.
   - Calculates actual required proportions and protects against slippage.
   - Mints and assigns liquidity tokens to the recipient address.

2. `removeLiquidity(tokenX, tokenY, liquidity, minX, minY, recipient, deadline)`
   - Withdraws a user’s proportional share of reserves from the pool.
   - Burns their liquidity tokens and returns tokenX and tokenY.
   - Enforces slippage protection and deadline verification.

3. `swapExactTokensForTokens(amountInput, minOutput, tokenPath, receiver, deadline)`
   - Swaps an exact input amount for another token.
   - Supports only direct (2-token) paths.
   - Calculates the output using the constant product formula.
   - Sends output tokens to the receiver address.

4. `getPrice(tokenX, tokenY)`
   - Returns the current exchange rate of tokenX in terms of tokenY.
   - Result is scaled by 1e18 to handle decimals precisely.

5. `getAmountOut(amountIn, reserveIn, reserveOut)`
   - A pure utility function to compute the expected output amount.
   - Based on the constant product formula used in AMM models.
   - Validates input and reserves.

Error Handling (Shortstrings):
------------------------------
To minimize gas costs, concise error messages are used:
  - `"Deadline exceeded"` — Operation was submitted after the deadline.
  - `"Tokens must differ"` — tokenX and tokenY must be different.
  - `"Amounts must be positive"` — One or more token amounts is zero.
  - `"Slippage exceeded"` — Actual amounts fell below the user’s minimum.
  - `"Zero liquidity"` — No liquidity specified for removal.
  - `"Insufficient balance"` — User does not have enough liquidity tokens.
  - `"Only direct swaps supported"` — Swap path must include exactly 2 tokens.
  - `"Zero input"` — Swap input amount must be greater than zero.
  - `"No liquidity"` — Reserves are missing or empty.
  - `"Invalid reserves"` — One or both reserve values are invalid.

Security:
---------
- The contract uses OpenZeppelin’s `ReentrancyGuard` to prevent reentrancy attacks.
- Validations are implemented for timeouts, token equality, reserve values, and minimum amounts to protect users and pools.

