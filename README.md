<div align="center">

# azminer

**A fast, native GPU miner for Pearl and Quantus.**

No Python, no WSL, no extra runtimes — a single binary that starts in a second.

[![Version](https://img.shields.io/badge/version-0.1.0-2563eb)](https://github.com/azminer-app/azminer/releases)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20Linux%20%7C%20HiveOS-334155)](#download)
[![GPU](https://img.shields.io/badge/NVIDIA-Turing%20%E2%86%92%20Blackwell-76b900)](#supported-gpus)
[![Dev fee](https://img.shields.io/badge/dev%20fee-2%25-f59e0b)](#coins--dev-fee)
[![License](https://img.shields.io/badge/license-Proprietary-64748b)](#license)

[Download](#download) · [Quick start](#quick-start) · [Options](#options) · [API](#monitoring-api) · [GPUs](#supported-gpus)

</div>

---

## Overview

**azminer** is a native miner for two proof-of-work coins — **Pearl** (`pearlhash`)
and **Quantus** (`qpow-poseidon2`). The proof-of-work itself runs on the original,
reference CUDA kernels; everything around it — Stratum, failover, overclocking,
thermal control, watchdog, the monitoring API and the log UX — is written from
scratch for speed and a small footprint.

- **Native and lightweight.** One self-contained executable. No interpreter, no
  virtual environment, no background services.
- **Two coins, one tool.** Switch between Pearl and Quantus with a single flag.
- **Built for rigs.** Per-GPU clocks, power limits, closed-loop fans, thermal
  pause/resume, pool failover and a hardware watchdog.
- **Drop-in monitoring.** Claymore-compatible stats API plus a clean `stats.json`,
  so HiveOS, RaveOS and most dashboards read it out of the box.

## Download

Grab the latest build from the [**Releases**](https://github.com/azminer-app/azminer/releases) page.

| Platform | Package | Notes |
| --- | --- | --- |
| **Windows** (x86_64) | `azminer-v0.1.0-windows-x86_64.zip` | Windows 10/11, NVIDIA driver installed |
| **Linux** (x86_64) | `azminer-v0.1.0-linux-x86_64.tar.gz` | GLIBC ≥ 2.28, includes the HiveOS package |
| **Integrations bundle** | `azminer-v0.1.0.tar.gz` | HiveOS custom-miner package + helper scripts |

Each release also ships a `SHA256SUMS` file — see [Verifying your download](#verifying-your-download).

## Quick start

Every package includes a ready-to-edit launcher per coin, preset to the Kryptex
pools. Open the launcher, set your wallet, and run it.

### Windows

1. Unzip `azminer-v0.1.0-windows-x86_64.zip`.
2. Open `start-pearl.bat` (or `start-quantus.bat`) in Notepad and set your wallet:
   ```bat
   set "WALLET=YOUR_PEARL_WALLET"
   ```
3. Double-click the launcher.

### Linux

```bash
tar xzf azminer-v0.1.0-linux-x86_64.tar.gz
cd azminer-v0.1.0-linux-x86_64
# edit start-pearl.sh and set WALLET=...
./start-pearl.sh
```

### Run it by hand

```bash
# Pearl
azminer -a pearl   -o stratum+tcp://prl.kryptex.network:7048 -u WALLET.worker

# Quantus
azminer -a quantus -o stratum+tcp://qtc.kryptex.network:7049 -u WALLET.worker
```

Add `-pool2 ...` for failover, `-gpus 0,1` to pick cards, and `-cdmport 3333` to
expose the stats API. See [Options](#options).

## Coins & dev fee

| Coin | Algorithm | Flag | Suggested pool | Dev fee |
| --- | --- | --- | --- | --- |
| **Pearl** | `pearlhash` | `-a pearl` | `prl.kryptex.network:7048` (`prl-eu` failover) | 2% |
| **Quantus** | `qpow-poseidon2` | `-a quantus` | `qtc.kryptex.network:7049` (`qtc-eu` failover) | 2% |

> The dev fee is a short, periodic switch to the developer wallet and is already
> reflected in every hashrate figure below. You can point `-o` / `-pool2` at any
> Stratum pool — the Kryptex endpoints are just the defaults in the launchers.

## Features

- **Dual-coin PoW** — Pearl (`pearlhash`) and Quantus (`qpow-poseidon2`) from one binary.
- **Pool failover** — primary + secondary pool with configurable retry, failover
  and reconnect policy (`-pool2`, `-fret`, `-ftimeout`, `-retrydelay`).
- **Overclocking (NVML)** — per-GPU core/mem offsets, power limit and fan control
  (`-cclock`, `-mclock`, `-powlim`, `-tt`).
- **Closed-loop thermals** — target-temperature fan control plus thermal
  pause/resume (`-tmax`, `-fanmin`/`-fanmax`, `-tstop`/`-tstart`).
- **Watchdog & scheduling** — restart on stalls, timed operation and pause windows
  (`-wdog`, `-wdtimeout`, `-timeout`, `-pauseat`/`-resumeat`).
- **GPU selection & tuning** — mine on specific cards and cap duty cycle
  (`-gpus 0,1`, `-gpow`).
- **Monitoring API** — Claymore-style `getstat1`/`getstat2` and a `stats.json`.
- **Clean logs** — color TTY output, auto plain/no-color when piped, and bounded
  file logging (`-log`, `-logfile`, `-logdir`, `-logsmaxsize`).
- **Integrity self-test** — `--integrity-selftest` authenticates the compiled
  kernels before you mine.

## Options

Both `-long` and `--long` spellings are accepted. Run `azminer --help` for the
complete list; the most common flags:

| Flag | Argument | Description |
| --- | --- | --- |
| `-a`, `-algo` | `pearl \| quantus` | Algorithm to mine |
| `-o`, `-pool` | `[scheme://]host:port` | Primary pool (defaults to `stratum+tcp://`) |
| `-u`, `-wal` | `wallet` | Login / wallet; `-worker` appends a worker name |
| `-p`, `-pass` | `password` | Pool password (default `x`) |
| `-pool2` / `-wal2` / `-pass2` | | Failover pool credentials |
| `-fret` / `-ftimeout` / `-retrydelay` | | Reconnect & failover policy |
| `-g`, `-gpus` | `all \| 0,1` | GPU selection (default `all`) |
| `-list` | | List CUDA devices and route support |
| `-gpow` | `1..100` | GPU duty-cycle cap |
| `-cclock` / `-mclock` / `-powlim` | per-GPU | Core clock, memory clock, power limit |
| `-tt` / `-fanmin` / `-fanmax` / `-tmax` | per-GPU | Fan target, fan range, temp cap |
| `-tstop` / `-tstart` | °C | Thermal pause / resume thresholds |
| `-wdog` / `-wdtimeout` / `-rmode` | | Watchdog & restart policy |
| `-timeout` / `-pauseat` / `-resumeat` | | Scheduled operation |
| `-cdmport` | `0 \| port \| IP:port` | Monitoring API (default `127.0.0.1:3333`) |
| `-log` / `-logfile` / `-logdir` / `-logsmaxsize` | | Bounded file logging |
| `-config` | `file.json` | JSON config (CLI flags override the file) |
| `--list-coins` | | List compiled algorithms |
| `--integrity-selftest` | | Authenticate compiled kernels and exit |
| `-V`, `--version` | | Print version |

## Monitoring API

azminer serves a Claymore-compatible JSON API (default `127.0.0.1:3333`, set with
`-cdmport`). It answers `miner_getstat1` / `miner_getstat2` and exposes a readable
`stats.json`, so HiveOS, RaveOS and most mining dashboards read it with no extra
configuration.

```bash
curl -s http://127.0.0.1:3333/getstat        # Claymore getstat1
curl -s http://127.0.0.1:3333/stats.json     # human-readable stats
```

## HiveOS / RaveOS

The Linux package contains a custom-miner bundle under `hiveos/azminer/`
(the standalone `azminer-v0.1.0.tar.gz` ships the same files).

1. Copy the `azminer/` folder to `/hive/miners/custom/azminer/` on the rig and make
   the binary executable (`chmod +x azminer`).
2. Create a flight sheet: **Miner = custom**, **Miner name = azminer**.
   - **Wallet template** → `wallet.worker`
   - **Pool URL** → `host:port` (scheme optional; defaults to `stratum+tcp://`)
   - **Algorithm** → `pearl` (or `quantus`)
   - **Extra config args** (optional) → e.g. `-g 0` or `-g all`

The bundle's `h-stats.sh` reads the azminer API and reports hashrate, temperature,
fan, shares and bus numbers back to HiveOS. The API port is set in
`h-manifest.conf` (`CUSTOM_API_PORT`, default `4068`). `jq` and `curl` are required
on the rig (both present on HiveOS). A README with the full details is included in
the package.

## Supported GPUs

azminer targets NVIDIA **Turing, Ampere, Ada and Blackwell** (SM 7.5 / 8.6 / 8.9 / 12.0).
**Pascal (GTX 10xx) and older are not supported.**

| Generation | Example cards | Status |
| --- | --- | --- |
| Blackwell (RTX 50xx) | RTX 5070 Ti, 5060 Ti | ✅ Supported |
| Ada (RTX 40xx) | RTX 4070 Ti, 4070 SUPER, 4060 Ti | ✅ Supported |
| Ampere (RTX 30xx) | RTX 3080, 3070, 3060 Ti | ✅ Supported |
| Turing (RTX 20xx / GTX 16xx) | RTX 2070 SUPER, GTX 1660 Ti | ✅ Supported |
| Pascal (GTX 10xx) | GTX 1080 Ti, 1060 | ❌ Not supported |

### Measured performance

From the hardware regression sweep (stock-ish clocks; results vary with driver,
cooling and overclock). Pearl and Quantus are different algorithms, so their
hashrates are **not** comparable to each other — only across cards within one coin.

| GPU | Gen | Pearl (`pearlhash`) | Quantus (`qpow-poseidon2`) | Power |
| --- | --- | --- | --- | --- |
| RTX 4070 Ti | Ada | 162.6 TH/s | 630.5 MH/s | ~284 W |
| RTX 4070 SUPER | Ada | 143.2 TH/s | 557.9 MH/s | ~218 W |
| RTX 5060 Ti | Blackwell | 99.5 TH/s | 374.9 MH/s | ~165 W |
| RTX 5070 Ti | Blackwell | 96.5 TH/s | 347.3 MH/s | ~94 W |
| RTX 4060 Ti | Ada | 89.6 TH/s | 352.0 MH/s | ~165 W |
| RTX 3070 | Ampere | 80.0 TH/s | 322.9 MH/s | ~219 W |
| RTX 3080 | Ampere | 75.3 TH/s | 370.8 MH/s | ~190 W |
| RTX 3060 Ti | Ampere | 65.3 TH/s | 269.2 MH/s | ~180 W |
| RTX 4060 | Ada | 61.8 TH/s | 257.3 MH/s | ~114 W |
| RTX 2070 SUPER | Turing | 61.5 TH/s | 259.2 MH/s | ~206 W |
| RTX 2060 | Turing | 49.5 TH/s | 210.7 MH/s | ~188 W |
| RTX 3060 | Ampere | 43.5 TH/s | 182.8 MH/s | ~110 W |
| RTX 3060 Laptop | Ampere | 34.0 TH/s | 161.3 MH/s | ~60 W |
| RTX 5060 Laptop | Blackwell | 27.8 TH/s | 130.8 MH/s | ~27 W |
| GTX 1660 Ti | Turing | 1.18 TH/s | 159.1 MH/s | ~57 W |
| GTX 1660 SUPER | Turing | 1.08 TH/s | 148.0 MH/s | ~59 W |

## Verifying your download

Every release includes `SHA256SUMS`. After downloading, check the archive against it:

```bash
# Linux / macOS
sha256sum -c SHA256SUMS

# Windows (PowerShell)
Get-FileHash .\azminer-v0.1.0-windows-x86_64.zip -Algorithm SHA256
```

## FAQ

**Does it work on Pascal (GTX 10xx)?** No. CUDA initialization fails on Pascal and
older; the miner needs Turing (SM 7.5) or newer.

**Is there a dev fee?** Yes, 2% on both coins. It is already included in the
hashrate figures above.

**Can I use my own pool?** Yes. Pass any Stratum endpoint to `-o` (and `-pool2`
for failover). The scheme is optional and defaults to `stratum+tcp://`.

**AMD / Intel GPUs?** Not supported — azminer is NVIDIA-only.

## License

azminer is **proprietary** software; all rights reserved. The binary vendors
third-party components under their own licenses (BLAKE3 — CC0/Apache-2.0;
TweetNaCl — public domain). See the license notice shipped with each release
package.
