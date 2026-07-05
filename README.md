# Injective EVM Console

A small, self-contained dev console for **Injective EVM** (chain `1776`). Every value is a live call to Injective mainnet, no backend, no build step, no API keys.

**Live:** https://injective-console.leverlabs.workers.dev

> A live tool on Injective mainnet. Reads are free and keyless; the write panel signs and broadcasts a real transaction from your connected wallet (defaults to a 0-value self-transfer, so only gas is spent). Not affiliated with Injective Labs.

## What it does

- **RPC failover** — races the public sentry RPC against a fallback, measures latency, and routes reads to whichever is healthy (the drop-in pattern a production app needs, since the public endpoint is shared and best-effort).
- **Live stats + block feed** — chain ID, block, gas price, base fee, block time, and a rolling feed of the latest blocks (tx count, gas used).
- **Native markets (exchange module)** — the on-chain order book, spot and perpetual markets pulled live from the Injective LCD, with each perp's oracle type and fees. This is the module a Solidity contract reaches through the exchange precompile.
- **Send transaction (write)** — signs and broadcasts a real transaction through the connected wallet and follows it to confirmation with a Blockscout link. Defaults to a 0-value self-transfer, so only gas is spent; the full write path (build, sign, broadcast, confirm) end to end.
- **JSON-RPC console** — pick a method, send it against the active RPC, see the raw response.
- **Precompile registry** — makes real `eth_call`s: the Injective exchange precompile at `0x…0065` (proves it is a live system contract), plus the standard `identity` and `sha256` precompiles (verifiable output).
- **Address inspector** — balance and contract-vs-EOA detection via `eth_getCode`.
- **Address derivation** — the `0x` and Cosmos `inj1…` addresses derive from the same key; the bech32 form is computed in-browser.

## APIs used (all free, public, no key)

| Surface | Endpoint |
|---|---|
| EVM JSON-RPC | `https://sentry.evm-rpc.injective.network` (+ a fallback) |
| Exchange module (markets) | `https://sentry.lcd.injective.network/injective/exchange/v1beta1/...` |
| Precompiles | `eth_call` to fixed addresses (`0x…0065` exchange, `0x…0002/0004` standard) |

## Run locally

It is a single static file. Any static server works:

```bash
python3 -m http.server 8788 --directory public
# open http://localhost:8788
```

Or just open `public/index.html` in a browser (the Injective RPC/LCD allow cross-origin requests, so live calls work from `file://` too).

## Deploy (Cloudflare Workers)

Assets-only Worker, no build:

```bash
wrangler deploy
```

`wrangler.toml` points at `./public`. Set your account via `wrangler login` or `CLOUDFLARE_ACCOUNT_ID`.

## Notes

- No secrets, no keys, no tracking. All data is read live from public Injective endpoints.
- Built with plain HTML, CSS, and JavaScript. Precompile addresses and chain config match the [Injective EVM docs](https://docs.injective.network/developers-evm/precompiles).

## License

MIT
