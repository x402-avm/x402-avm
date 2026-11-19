# Roadmap

These are plans, not promises. They are items the current code and release process point to, listed without dates. Priorities can change.

## Releases

- Publish to PyPI and npm from CI using trusted publishing, so releases do not depend on a maintainer's machine or a long-lived token.
- Keep the three packages version-compatible: `ai-economy-sdk` depends on `x402-algorand`, and the `x402` floor (>= 2.28) follows the official SDK.

## Testing

- Run the offline suites of each package in CI on every pull request.
- Run the `LOCALNET=1` suites in CI against an AlgoKit LocalNet container.
- Run the read-only `PUBLIC_NETS=1` checks on a schedule, to catch changes in public node behaviour.

## Examples and documentation

- More runnable examples: USDC on TestNet and MainNet, passes, and a standalone facilitator.
- Keep the documentation site and `llms.txt` in step with each release.

## Packages

- Track the official x402 SDK and the CAIP-2 Algorand profile as they change.
- Wallet adapter examples for `x402-avm-js` beyond the ones already tested.

Suggestions are welcome as issues in the relevant repository.
