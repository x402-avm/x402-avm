<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://x402-avm.github.io/logo/dark.svg">
  <img alt="x402-avm" src="https://x402-avm.github.io/logo/light.svg" height="40">
</picture>

# x402-avm

**Let AI agents and apps pay for your tools and APIs per call, in ALGO or USDC, with no accounts and no API keys.**

An AI agent can't fill in a sign-up form or a credit card. A tool worth a tenth of a cent per call can't be sold on card rails at all. x402-avm makes the price part of the HTTP response: your server answers `402 Payment Required`, the caller's wallet pays on Algorand and retries, and the result comes back in the same request.

[![Watch the two-minute demo](https://x402-avm.github.io/videos/x402-avm-demo-poster.png)](https://x402-avm.github.io/videos/x402-avm-demo.mp4)

[Documentation](https://x402-avm.github.io) · [Quickstart](https://x402-avm.github.io/quickstart) · [Examples](https://github.com/x402-avm/examples) · [Discussions](https://github.com/x402-avm/x402-avm/discussions)

## What you can build

- **Sell an MCP tool.** Put a price on each tool of a FastMCP server, and any MCP client with a wallet can call it. → [Guide](https://x402-avm.github.io/build/sell-mcp-tools)
- **Paywall an HTTP API.** Charge per request on a FastAPI or Flask route with the official x402 SDK. → [Guide](https://x402-avm.github.io/build/paywall-http-api)
- **Let your agent pay.** Give an agent a wallet and a budget, and it pays for tools on its own within limits checked before anything is signed. → [Guide](https://x402-avm.github.io/build/ai-agents)
- **Pay from a web app.** Let users pay with Pera, Defly or any Algorand wallet. → [Guide](https://x402-avm.github.io/build/pay-from-browser)

## Why it's built this way

- **The buyer pays only when the call works.** The server checks the payment, runs the tool, and submits the payment to the chain only if the tool succeeded.
- **The buyer pays exactly the price.** The server covers the network fee, so 0.005 ALGO costs 0.005 ALGO.
- **Nothing sits in the middle.** Money goes straight from the buyer's account to yours. There is no platform account and no payout schedule.
- **Spend limits come first.** Clients refuse any payment over a per-call cap (1 ALGO or 1 USDC by default) or to an asset, network or recipient you didn't allow, before signing.
- **It's the open standard.** The packages implement [x402](https://www.x402.org) and the official [Algorand `exact` scheme](https://github.com/x402-foundation/x402/blob/main/specs/schemes/exact/scheme_exact_algo.md), so they work with any other x402 software on Algorand.

## Try it in five minutes

Everything runs on your machine on Algorand LocalNet, with test money. You need Python 3.10+, Docker and [AlgoKit](https://github.com/algorandfoundation/algokit-cli).

```bash
algokit localnet start
git clone https://github.com/x402-avm/examples.git && cd examples
python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt

python localnet/quickstart_server.py    # terminal 1: a tool that costs 0.005 ALGO per call
python localnet/quickstart_client.py    # terminal 2: pays for one call
# Result: echo: hello world
# Balance drop: 5000 microALGO
```

The [Quickstart](https://x402-avm.github.io/quickstart) walks through what just happened. When you're ready for real money, see [Going to MainNet](https://x402-avm.github.io/production/mainnet).

## The packages

Most projects need only one.

| Package | Install | Use it to |
|---|---|---|
| [`ai-economy-sdk`](https://github.com/x402-avm/ai-economy-sdk) | `pip install ai-economy-sdk` | Sell MCP tools, and pay for them from Python and agents |
| [`x402-algorand`](https://github.com/x402-avm/x402-algorand) | `pip install x402-algorand` | Add Algorand to the official x402 Python SDK, for HTTP APIs and facilitators |
| [`x402-avm-js`](https://github.com/x402-avm/x402-avm-js) | `npm install x402-avm-js algosdk` | Pay from a browser wallet or Node.js |

The repositories in this organisation:

- [`examples`](https://github.com/x402-avm/examples): runnable examples for LocalNet, TestNet and MainNet.
- [`marketplace-demo`](https://github.com/x402-avm/marketplace-demo): a demo marketplace of 64 paid AI tools from 4 vendors, running on LocalNet.
- [`x402-avm.github.io`](https://github.com/x402-avm/x402-avm.github.io): the documentation site, including [`llms.txt`](https://x402-avm.github.io/llms.txt) for AI coding tools.
- This repository: the [architecture and trust model](ARCHITECTURE.md), the [roadmap](ROADMAP.md) and [AGENTS.md](AGENTS.md).

## Get involved

Questions and ideas go in [Discussions](https://github.com/x402-avm/x402-avm/discussions), and bugs go in the issues of the relevant repository. Report security problems privately, following the [security policy](https://github.com/x402-avm/.github/blob/main/SECURITY.md).

## Licence

`ai-economy-sdk` and `x402-avm-js` are MIT. `x402-algorand` is Apache-2.0, with a `NOTICE` that reproduces the upstream notices. This repository is MIT.

x402-avm is an independent project. It is not affiliated with GoPlausible's x402-avm packages, from which x402-algorand is derived (see [NOTICE](https://github.com/x402-avm/x402-algorand/blob/main/NOTICE)).
