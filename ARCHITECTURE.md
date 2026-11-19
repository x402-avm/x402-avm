# Architecture

How the packages in the x402-avm organisation fit together, how a payment moves through them, and who is trusted with what.

## Components

| Component | Role | Implemented in |
|---|---|---|
| Client SDK | Reads the 402 challenge, builds the payment group, has the buyer sign their part, retries with `PAYMENT-SIGNATURE`. Applies spend controls. | `ai-economy-sdk` (`paid_mcp_session`, Python), `x402-avm-js` (`payForRequirement`, browser and Node), `x402-algorand` (client scheme for the official `x402Client`) |
| Resource server / gate | Sits in front of the tool or API. Issues the 402 challenge, checks the payment, runs the tool, settles on success. | `ai-economy-sdk` (`PaymentGate`, `AIEconomySDK`), or the official x402 server with `x402-algorand` registered |
| Facilitator | Verifies a payment and, when asked, submits it. In `PaymentGate` it runs in-process; with the official SDK it can be a separate service. | `x402-algorand` (`register_exact_avm_facilitator`), `ai-economy-sdk` (`DefaultAvmSigner`) |
| Algorand node (algod) | Simulates the group before the tool runs and confirms it at settlement. | External: AlgoNode for MainNet and TestNet by default, AlgoKit LocalNet locally |
| Indexer | Read-only history. Used to find active passes (prepaid access) for a caller. | External: AlgoNode indexer by default |

The split between the SDKs: `x402-algorand` is the protocol layer (the `exact` scheme on Algorand, plugged into the official SDK). `ai-economy-sdk` is built on it and adds the MCP integration, passes, identity proofs and spend controls. `x402-avm-js` is a TypeScript client for the same flow.

## Payment lifecycle

1. The client calls a tool or endpoint without payment.
2. The server answers `402` with a `PAYMENT-REQUIRED` header: x402 version, and a list of accepted options, each with network (CAIP-2), asset, amount in base units, and the `payTo` address.
3. The client picks the option on its own network (`payForRequirement` throws if there is none) and checks it against its spend controls.
4. The client builds an atomic group of two transactions and signs only its own:
   - transaction 0: zero-amount payment from the fee payer to itself, fee 2,000 microALGO, signed later by the server;
   - transaction 1: the buyer's payment (ALGO `pay` or ASA `axfer`) to `payTo` for the exact price, fee 0.
5. The client retries the request with the group in a `PAYMENT-SIGNATURE` header.
6. The server computes the replay key (SHA-256 of the header payload) and reserves it. A key already reserved or committed is rejected with 402.
7. The facilitator checks the signature and simulates the group on the node. Simulation confirms the group is valid and the buyer can pay, without broadcasting it.
8. The server runs the tool.
9. If the tool succeeds, the server signs transaction 0 and submits the group. It then commits the replay key and returns the result with a `PAYMENT-RESPONSE` header.
10. If the tool raises or returns an MCP error, nothing is submitted, the reservation is released, and the buyer is not charged. If settlement itself fails, the client receives an error stating that settlement failed.

Without a fee payer the payment is a single buyer-signed transaction with the standard 1,000 microALGO fee.

## Trust model

- **Who signs what.** The buyer signs only transaction 1, which pays the exact price to `payTo`. The server (fee payer) signs transaction 0. The buyer's key never leaves the client.
- **Fee payer.** Algorand pools fees across an atomic group, so transaction 0 covers both transactions. The buyer's balance falls by exactly the price. The server bears the 2,000 microALGO network fee, and so must hold enough ALGO.
- **What the buyer risks.** A signed group is useless without the server's signature on transaction 0, and the server submits it only after the tool succeeds. The group has a bounded validity window.
- **What the server risks.** It simulates before running the tool, so an unfunded or malformed payment is rejected early. It does not run a tool for a payment that cannot settle.
- **Replay protection.** A payment payload can be used for one successful call. The reservation happens before the tool runs and is released on failure, so a failed call can be retried with the same payment.
- **Network binding.** Network ids are CAIP-2 values derived from the genesis hash. A client pays only an offer on the network of its algod, and a facilitator on one network rejects groups built for another (TestNet payment against a MainNet facilitator is refused).
- **Identity proofs.** An optional `X-Identity-Txn` header carries a signed zero-fee self-payment with the note `x402-identity:<address>:<timestamp>` and validity rounds 1 to 1000. It cannot execute on any live network, so it works only as a credential; the server accepts a timestamp within 120 seconds of its clock.
- **Passes.** A pass is an ordinary payment to a vendor with the note `x402-pass:<scope>`, valid 30 days by default. The gate looks passes up in the indexer, so the indexer is trusted for that decision.
- **Spend controls.** The Python clients refuse unknown assets and amounts over a cap. The `x402-algorand` default is 1,000,000 base units (1 ALGO or 1 USDC) per payment.

## Where each part lives

| Part | Repository |
|---|---|
| `PaymentGate`, `AIEconomySDK`, `paid_mcp_session`, passes, identity, replay store | [ai-economy-sdk](https://github.com/x402-avm/ai-economy-sdk) |
| Algorand `exact` scheme: client, server and facilitator registration, CAIP-2 helpers | [x402-algorand](https://github.com/x402-avm/x402-algorand) |
| Browser and Node client: `decodePaymentRequired`, `payForRequirement`, network presets | [x402-avm-js](https://github.com/x402-avm/x402-avm-js) |
| Runnable flows on LocalNet, TestNet and MainNet | [examples](https://github.com/x402-avm/examples) |
| Multi-vendor marketplace (gateway, web app, chain bootstrap, 64 listings) | [marketplace-demo](https://github.com/x402-avm/marketplace-demo) |
| Concepts, tutorials, reference | [x402-avm.github.io](https://github.com/x402-avm/x402-avm.github.io) |
