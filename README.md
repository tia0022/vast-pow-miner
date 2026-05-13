<div align="center">

# vast-pow-miner

**Rent a GPU on vast.ai → mine any keccak256 PoW ERC-20 → track live P&L → auto-stop on loss.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20CUDA-green.svg)]()
[![vast.ai](https://img.shields.io/badge/GPU-vast.ai-orange.svg)](https://cloud.vast.ai/?ref_id=537207)
[![Chains](https://img.shields.io/badge/Chains-ETH%20%7C%20Polygon%20%7C%20Base%20%7C%20Arbitrum-purple.svg)]()

</div>

---

> **Support the author — sign up to vast.ai via referral:**
> **👉 https://cloud.vast.ai/?ref_id=537207**

---

## Quick Start

```bash
git clone https://github.com/tia0022/vast-pow-miner
cd vast-pow-miner
bash setup_vast.sh                            # install CUDA toolkit, Rust, Python deps, miner
nano .env                                      # paste PRIVATE_KEY
VAST_COST_PER_HOUR_USD=0.343 STOP_LOSS_USD=-3 bash start_mining.sh
```

---

## Table of Contents

- [Idiot-proof 5-step flow](#idiot-proof-5-step-flow)
- [Honest Economics](#honest-economics-read-this-before-you-press-rent)
- [Mine a Different Token](#mine-a-different-token-not-hash256)
- [Architecture](#architecture)
- [Auto-stop Triggers](#auto-stop-triggers)
- [Security Checklist](#security-checklist)
- [Disclaimer](#disclaimer)

---

## Idiot-proof 5-step flow

| Step | Action |
|------|--------|
| **1** | **Sign up vast.ai** → [cloud.vast.ai/?ref_id=537207](https://cloud.vast.ai/?ref_id=537207), top up $10 |
| **2** | **Rent a GPU** (RTX 4090 / 5090 / L40S) — pick an image with CUDA 13+ |
| **3** | **SSH in** and clone the repo: |

```bash
git clone https://github.com/tia0022/vast-pow-miner && cd vast-pow-miner
bash setup_vast.sh
```

| Step | Action |
|------|--------|
| **4** | **Paste your mining-wallet private key** into `.env` (use a throwaway wallet with ~$10 ETH for gas) |
| **5** | **Launch:** |

```bash
VAST_COST_PER_HOUR_USD=0.343 STOP_LOSS_USD=-3 bash start_mining.sh
```

> Full step-by-step for beginners: [`docs/QUICKSTART.md`](docs/QUICKSTART.md)

---

## Honest Economics (read this before you press RENT)

> No hype. No "passive income" pitch. Here's the raw data:

| Metric | HASH256 on RTX 5090 @ $0.343/hr |
|--------|---------------------------------|
| Measured GPU hashrate | 5.76 GH/s |
| Estimated network hashrate | ~18.8 TH/s |
| Expected mints per hour | ~0.018 |
| Revenue per hour (100 HASH × $0.066) | ~$0.12 |
| vast.ai cost per hour | −$0.343 |
| **Net per hour** | **−$0.22 (loss ~$5/day)** |

**Right now, HASH256 loses money.** This repo exists so you can watch the numbers in real time and auto-stop before you bleed out. If HASH price 3×'s, this turns profitable instantly. If not — switch to another profile or stop.

The code's value is **stopping the bleed fast.**

---

## Mine a Different Token (not HASH256)

Profile-driven design: one YAML per token. Drop in any PoW ERC-20 contract address and `onboard.py` builds the profile automatically.

```bash
# Generate a profile from any Ethereum-mainnet ERC-20 PoW contract:
python3 bin/onboard.py \
    --contract 0xABCD...your_contract... \
    --name mytoken

# Validate it:
python3 bin/onboard.py --profile profiles/mytoken.yaml --validate

# Mine it:
PROFILE=mytoken VAST_COST_PER_HOUR_USD=0.343 STOP_LOSS_USD=-3 bash start_mining.sh
```

`onboard.py` fetches the contract ABI from Etherscan, guesses the `mine()` function, and writes the profile YAML. You may need to edit 1-2 fields if heuristics don't match — see `profiles/template.yaml`.

**Supported chains:** Ethereum · Polygon · Base · Arbitrum

**Supported hash algorithm:** keccak256 — any token computing `hash = keccak256(abi.encodePacked(challenge, msg.sender, nonce))` with `hash < difficulty`.

> More: [`docs/ADD_PROFILE.md`](docs/ADD_PROFILE.md)

---

## Architecture

```
vast-pow-miner/
├── profiles/
│   ├── hash256.yaml          # reference profile (tested)
│   ├── template.yaml         # blank template for new tokens
│   └── README.md             # how to contribute a profile
├── bin/
│   ├── onboard.py            # contract addr → profile YAML
│   ├── generic_monitor.py    # profile-driven real-time P&L + auto-stop
│   └── scan_mints.py         # count mint activity for a profile
├── setup_vast.sh             # one-shot provisioning
├── start_mining.sh           # launch miner + monitor in tmux
├── .env.example              # environment variable template
├── docs/
│   ├── QUICKSTART.md         # step-by-step with commands (EN/CN)
│   └── ADD_PROFILE.md        # adding a new token guide
└── LICENSE                   # MIT
```

---

## Auto-stop Triggers

`bin/generic_monitor.py` kills the miner tmux session automatically when:

| Trigger | Condition |
|---------|-----------|
| **Net loss** | P&L ≤ `STOP_LOSS_USD` (default −$10) |
| **Token crash** | Price drops ≥ `HASH_DROP_PCT_STOP`% from session peak (default 50%) |
| **Gas too low** | ETH balance below 1 tx — warns but does not stop |

Every 30 seconds it writes `profit_status.json` with a full P&L snapshot.

---

## Security Checklist

Before you start mining, go through this:

- [ ] Use a **fresh wallet** with only gas ETH ($10–15)
- [ ] Withdraw mined tokens regularly — the host can read your `.env`
- [ ] Burn the wallet when you're done with the session
- [ ] Use your own Alchemy / Infura RPC, not the public fallback
- [ ] Set `STOP_LOSS_USD` conservatively (−$3 for a first test run)

---

## Disclaimer

**Not financial advice.** You are responsible for rental costs, gas costs, wallet security, and any lost funds. Token liquidity for new PoW coins is often under $100k — a single whale sell crashes the price instantly. Always run the monitor; never YOLO.

---

## License

MIT — see [`LICENSE`](LICENSE).

---

## Support the Project

- ⭐ Star this repo if it saved you from a bad mining session
- 🔗 Sign up to vast.ai via [referral link](https://cloud.vast.ai/?ref_id=537207) — it supports development
- 🐛 Open issues or PRs to contribute new token profiles
