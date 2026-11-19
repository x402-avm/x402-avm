# AGENTS.md

Guidance for AI agents working with the x402-avm organisation. This is the hub repository; it holds documentation only, no code.

## What the organisation contains

SDKs for paying for APIs and AI tools per call on Algorand with x402 (HTTP 402 Payment Required). Documentation: https://x402-avm.github.io. Index for models: https://x402-avm.github.io/llms.txt.

## Which repository for which task

| Task | Repository | Install |
|---|---|---|
| Make an MCP server charge per tool call; pay for MCP tools from Python | [ai-economy-sdk](https://github.com/x402-avm/ai-economy-sdk) | `pip install ai-economy-sdk` |
| Add Algorand payments to a server or client built on the official x402 Python SDK | [x402-algorand](https://github.com/x402-avm/x402-algorand) | `pip install x402-algorand` |
| Pay a 402 response from a browser or Node with an Algorand wallet | [x402-avm-js](https://github.com/x402-avm/x402-avm-js) | `npm install x402-avm-js algosdk` |
| Find a runnable example (`localnet/`, `testnet/`, `mainnet/`) | [examples](https://github.com/x402-avm/examples) | clone |
| See a multi-vendor deployment | [marketplace-demo](https://github.com/x402-avm/marketplace-demo) | clone; LocalNet only |
| Change the documentation | [x402-avm.github.io](https://github.com/x402-avm/x402-avm.github.io) | clone |

Each repository has its own `AGENTS.md` at its root. Read it before changing that repository; it takes precedence over this file.

- [x402-avm/AGENTS.md](https://github.com/x402-avm/x402-avm/blob/main/AGENTS.md)
- [ai-economy-sdk/AGENTS.md](https://github.com/x402-avm/ai-economy-sdk/blob/main/AGENTS.md)
- [x402-algorand/AGENTS.md](https://github.com/x402-avm/x402-algorand/blob/main/AGENTS.md)
- [x402-avm-js/AGENTS.md](https://github.com/x402-avm/x402-avm-js/blob/main/AGENTS.md)
- [examples/AGENTS.md](https://github.com/x402-avm/examples/blob/main/AGENTS.md)
- [marketplace-demo/AGENTS.md](https://github.com/x402-avm/marketplace-demo/blob/main/AGENTS.md)
- [x402-avm.github.io/AGENTS.md](https://github.com/x402-avm/x402-avm.github.io/blob/main/AGENTS.md)

## Running tests

| Mode | Python (`ai-economy-sdk`, `x402-algorand`) | JavaScript (`x402-avm-js`) | Needs |
|---|---|---|---|
| Offline | `python -m pytest tests -q` | `npm test` | nothing |
| `LOCALNET=1` | `LOCALNET=1 python -m pytest tests -q` | `LOCALNET=1 npm test` | AlgoKit LocalNet running (algod on port 4001, KMD on 4002) |
| `PUBLIC_NETS=1` | `PUBLIC_NETS=1 python -m pytest tests -q -m public` | `PUBLIC_NETS=1 npm test` | internet access to public nodes |

`PUBLIC_NETS=1` tests are read-only: payments are built and simulated, not sent, and need no funded key. Run the offline suite first. Which modes a given package supports is stated in its own README.

## Conventions

- Network ids are CAIP-2: MainNet `algorand:wGHE2Pwdvd7S12BL5FaOP20EGYesN73k`, TestNet `algorand:SGO1GKSzyE7IEPItTxCByw9x8FmnrCDe`. Do not hand-write the 44-character base64 form in new code.
- Amounts are integers in base units (microALGO, or the ASA's smallest unit). USDC has 6 decimals. USDC ASA ids: MainNet 31566704, TestNet 10458941.
- The payment group has two transactions: the server pays the 2,000 microALGO fee, the buyer pays exactly the price.
- Never commit private keys, mnemonics or tokens. Read them from the environment.
- Never send a transaction on MainNet from a test. Use LocalNet for settlement tests and `PUBLIC_NETS=1` simulation for public networks.
- Do not set the LocalNet dev-mode block offset; it shifts the chain clock.
- Use plain, precise language in documentation. No marketing terms, no emojis.

## Links

- Documentation: https://x402-avm.github.io
- llms.txt: https://x402-avm.github.io/llms.txt
- Architecture: [ARCHITECTURE.md](ARCHITECTURE.md)
- Security policy: https://github.com/x402-avm/.github/blob/main/SECURITY.md
- Official x402: https://github.com/x402-foundation/x402
