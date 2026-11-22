# BSC DEX exit

Sell a newly launched BSC token into BNB from the Binance Agentic Wallet (baw). Use it for a fresh pair with thin liquidity, especially a share token or game token that may tax transfers. The route below is proven by the ENCORE sale on 2026-11-22.

## Confirm the holding first

- Read the balance on-chain from the exact wallet that will sell, at its full address, on two RPC endpoints. A prefix is not an address.

- Confirm the wallet belongs to the user before any sell step. A balance in another person's wallet is not the user's holding. On 2026-11-22, 2,500 ENCORE in wallet 0x049b01c4 turned out to belong to buahlil.ai, not the user, and every sell attempt from it was doomed.

- Treat a claimed balance without a full address and a block number as unverified.

## Check the tax before the slippage

- Read the token contract for a transfer tax before choosing slippage. Many new share tokens tax transfers. ENCORE taxed 3 percent, so 2,500 sent put 2,425 into the pair.

- Set slippage above the tax. A 1 percent slippage on a 3 percent tax token always fails. Use slippage auto, or at least the tax plus 2 percent.

- Quote with getAmountsOut on the pair. Confirm the quote on two endpoints when the numbers decide the sale.

## Confirm trading is open

- Check launched() or run a quote before executing. A reverted quote means trading is closed.

- A swap receipt with status 1 proves the token launched. A "not launched" claim needs a failed quote as evidence, not an assumption.

## Sell in this order

- Try the baw market swap first. It is the simplest route.

- If the market swap errors (for example ORDER_API_ERROR 30003001), do not force it. Place a limit sell at a fair price as the fallback.

- If the user wants the DEX route, use baw contract-call. Approve the PancakeSwap V2 router first, then call swapExactTokensForETH with the correct slippage. The preview must be clean, with simulation success, no risks, and no allowance changes beyond the router approval.

- A PENDING_CONFIRMATION or high-risk flag is normal for a DEX swap. The user approves it in the Binance app. Do not resend.

- A FAILED order with a null txHash executed nothing and burned no gas. Fix the cause, usually slippage, before retrying.

## Verify from the chain, not the order list

- Confirm the sale from the receipt (status 1) and a post-sale balance of 0 on two endpoints.

- The baw order list can be empty or misleading. A contract-call swap does not appear as a market order. The on-chain transaction is the record.

- Never retry blind when the outcome is unknown. If a swap may have submitted silently, a retry can double-sell. Check the order and the balance first.

## Check the gas against the proceeds

- The DEX route needs two transactions, approve plus swap. Check the net stays positive after gas. On BSC each transaction costs roughly 0.01 to 0.03 dollars, so a sale near 0.33 dollars still clears. Skip a sale whose value is below the total gas.

## Worked example (ENCORE, 2026-11-22)

- The token is 0x5c2e327f1B503289c1C8eE4616b7557701Ce7F26 and the PancakeSwap V2 pair is 0x1edc5b0ebde07f58889d25b3871142d31bc5e0e5, with a 3 percent tax.

- The market swap failed with 30003001. A limit order was placed, then the user chose DEX. Approve plus swap, with an in-app tap, turned 2,500 ENCORE into 0.0005916 BNB. The transaction is 0x0d26dfed4af7cd05733f5721d9ffc2083f2e439c6329b32a31a1687d50f9eb33, status 1, block 135085805, and the balance of 0 was verified on two endpoints.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Alternatives for better results

- A limit sell avoids the two-transaction DEX cost when the user can wait for a fill.

- When the pool is thin, sell the full balance in one transaction. Splitting it pays gas and tax on every leg.
