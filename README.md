<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://x402-avm.github.io/logo/dark.svg">
  <img alt="x402-avm" src="https://x402-avm.github.io/logo/light.svg" height="40">
</picture>

# x402-avm

Charge per call for MCP tools and HTTP APIs, paid on Algorand with the [x402](https://github.com/x402-foundation/x402) protocol. A server answers an unpaid request with `402 Payment Required` and a price; the client signs an Algorand payment and retries; the server runs the tool and settles the payment only if the tool succeeds. Buyers need a funded Algorand account, not an API key, and pay exactly the price: the server's fee payer covers the network fee. Prices are in ALGO or any Algorand Standard Asset, such as USDC.

[![Watch the two-minute demo](https://x402-avm.github.io/videos/x402-avm-demo-poster.png)](https://x402-avm.github.io/videos/x402-avm-demo.mp4)

Two-minute demo: a real payment on LocalNet, spend controls, TestNet and MainNet, and the docs.

## Packages

| Package | Version | Install | What it does |
|---|---|---|---|
| [`ai-economy-sdk`](https://github.com/x402-avm/ai-economy-sdk) (Python, MIT) | 0.5.1 | `pip install ai-economy-sdk` | Turns a FastMCP server into a paid service (`PaymentGate`) and pays for tools from Python clients and agents (`paid_mcp_session`), with spend controls, passes, identity proofs and replay protection. |
| [`x402-algorand`](https://github.com/x402-avm/x402-algorand) (Python, Apache-2.0) | 0.3.0 | `pip install x402-algorand` | The Algorand [`exact` scheme](https://github.com/x402-foundation/x402/blob/main/specs/schemes/exact/scheme_exact_algo.md) as a plugin for the official x402 Python SDK, with ready-made signers (`PayerAvmSigner`, `ServerAvmSigner`). |
| [`x402-avm-js`](https://github.com/x402-avm/x402-avm-js) (TypeScript, MIT) | 0.4.1 | `npm install x402-avm-js algosdk` | Pays 402 responses from the browser or Node.js 20+ with any Algorand wallet, within spend limits checked before signing. |

`ai-economy-sdk` installs `x402-algorand`, which installs the official `x402` package (2.28 or later). Both Python packages support Python 3.10 to 3.14.

## Quickstart

Needs Python 3.10+, Node.js 20+, Docker and [AlgoKit](https://github.com/algorandfoundation/algokit-cli). Everything runs on LocalNet, with no real funds. These are the commands of the [docs Quickstart](https://x402-avm.github.io/quickstart), which explains each step.

```bash
algokit localnet start                  # algod :4001, indexer :8980, KMD :4002
git clone https://github.com/x402-avm/examples.git
cd examples
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
npm install

# Terminal 1: a paid MCP tool (echo, 5,000 microALGO per call) on http://127.0.0.1:9201/mcp
python localnet/quickstart_server.py
# Terminal 2: pay for one call from Python
python localnet/quickstart_client.py    # Result: echo: hello world / Balance drop: 5000 microALGO

# Pay an x402 HTTP endpoint from JavaScript
python localnet/server_for_js.py        # terminal 1: paywall on http://127.0.0.1:9221/paid
node localnet/js_pay.mjs                # terminal 2
```

## Links

- Documentation: https://x402-avm.github.io ([llms.txt](https://x402-avm.github.io/llms.txt) for AI tools)
- Examples: https://github.com/x402-avm/examples
- Demo: [marketplace-demo](https://github.com/x402-avm/marketplace-demo), a demonstration marketplace of 64 paid AI tools from 4 vendors on LocalNet
- Questions and ideas: [GitHub Discussions](https://github.com/x402-avm/x402-avm/discussions)
- Security: report vulnerabilities privately, following the [security policy](https://github.com/x402-avm/.github/blob/main/SECURITY.md); do not open public issues for them
- In this repository: [ARCHITECTURE.md](ARCHITECTURE.md) (payment lifecycle and trust model), [ROADMAP.md](ROADMAP.md), [AGENTS.md](AGENTS.md)

## Licence

`ai-economy-sdk` and `x402-avm-js` are MIT. `x402-algorand` is Apache-2.0 and ships a `NOTICE` that reproduces the upstream notices. The content of this hub repository is MIT; see [LICENSE](LICENSE).

x402-avm is an independent project, not affiliated with GoPlausible's x402-avm packages, from which x402-algorand is derived (see [NOTICE](https://github.com/x402-avm/x402-algorand/blob/main/NOTICE)).
