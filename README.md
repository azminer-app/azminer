<div align="center">

<img src=".github/assets/azminer-header.png" alt="AZminer — Native GPU mining for Pearl, Quantus, Parano1d and Vearl" width="1200">

[![Release](https://img.shields.io/github/v/release/azminer-app/azminer?label=release&color=F2A65A&labelColor=0F1011&logo=github&logoColor=white)](https://github.com/azminer-app/azminer/releases/latest)
[![Website](https://img.shields.io/badge/website-azminer.app-F2A65A?labelColor=0F1011&logo=cloudflare&logoColor=white)](https://azminer.app)
[![Telegram](https://img.shields.io/badge/Telegram-join-F2A65A?labelColor=0F1011&logo=telegram&logoColor=white)](https://t.me/+DYOBpOCui9EzNDM1)
[![Discord](https://img.shields.io/badge/Discord-join-F2A65A?labelColor=0F1011&logo=discord&logoColor=white)](https://discord.gg/uzDmVnyups)

[Coins](#supported-coins) · [Quick start](#quick-start) · [Support](#support) · [Download](#download) · [GPU support](#gpu-support) · [Performance](#performance) · [Options](#options) · [Dev fee](#dev-fee)

</div>

AZminer is a native NVIDIA GPU miner for new proof-of-work coins. One self-contained binary for Windows and Linux, a HiveOS integration bundle, pool failover, per-GPU tuning, temperature protection, a stall watchdog and a monitoring API.

## Supported coins

| Coin | Algorithm | Select with | Pool in the examples below | Guide |
| --- | --- | --- | --- | --- |
| **Vearl** (new) | `vearl` (vpool/2 pools) | `-a vearl` | `pool.vearl.org:3333` | [azminer.app/vearl](https://azminer.app/vearl) |
| **Pearl (PRL)** | `pearlhash` | `-a pearl` | `prl.kryptex.network:7048` | [azminer.app/pearl](https://azminer.app/pearl) |
| **Quantus** | `qpow-poseidon2` | `-a quantus` | `qtc.kryptex.network:7049` | [azminer.app/quantus](https://azminer.app/quantus) |
| **Parano1d** | `parano1d` | `-a parano1d` | `stratum+ssl://stratum.innovlab.cc:19601` | [azminer.app/parano1d](https://azminer.app/parano1d) |

Hashrates are not comparable across coins (each algorithm has its own unit). Compare cards within one coin, and use a pool's calculator for earnings.

## Quick start

Two steps: get the miner for your system, then run the command for your coin. Every command is complete; replace `YOUR_WALLET` with your address for that coin. `rig1` is the worker name shown on the pool; change it per machine.

<details>
<summary><strong>New to mining? Four words you need</strong></summary>

- **Wallet**: the address that receives the coins. Create it in the coin's official wallet, then paste it after `-u`. Never share its seed phrase; AZminer never asks for it.
- **Pool**: a server that combines many miners and pays each by work done. The examples already include one; any pool for the coin works, and each pool charges its own fee.
- **Hashrate**: how fast your GPU works, in H/s. Higher is more coins. Units differ per coin, so compare within one coin only.
- **Accepted share**: a unit of work the pool confirmed. When the log shows accepted shares, you are mining.

</details>

### Step 1 · Get the miner

#### Linux

```bash
curl -fL -o azminer https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11-linux-x86_64
chmod +x azminer
```

Needs an NVIDIA driver and GLIBC 2.28 or newer. Then run the command for your coin from Step 2.

#### Windows

1. Download and extract the [ZIP package](https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11-windows-x86_64.zip) into a folder without spaces, such as `C:\azminer`.
2. Open `start-vearl.bat` (or `start-pearl.bat`, `start-quantus.bat`, `start-parano1d.bat`) in Notepad, set `WALLET`, save, double-click.

The Step 2 commands also work in Command Prompt with `azminer.exe` in place of `./azminer`. Needs Windows 10/11 and an NVIDIA driver.

#### HiveOS

Add a custom miner once, then one flight sheet per coin.

| Field | Value |
| --- | --- |
| Miner name | `azminer` |
| Installation URL | `https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11.tar.gz` |
| Hash algorithm | `vearl`, `pearl`, `quantus` or `parano1d` |
| Wallet and worker template | `%WAL%.%WORKER_NAME%` |
| Pool URL | the pool from the coin's example in Step 2 |

### Step 2 · Pick your coin and run

<details open>
<summary><strong>Vearl</strong> · new · pool.vearl.org:3333</summary>

```bash
./azminer -a vearl -o pool.vearl.org:3333 -u YOUR_WALLET.rig1
```

Vearl addresses start with `vrl1`. Official pool, 1% fee; `pool.vearl.org:3366` is its solo port.

</details>

<details>
<summary><strong>Pearl (PRL)</strong> · prl.kryptex.network:7048</summary>

```bash
./azminer -a pearl -o prl.kryptex.network:7048 -u YOUR_WALLET.rig1
```

</details>

<details>
<summary><strong>Quantus</strong> · qtc.kryptex.network:7049</summary>

```bash
./azminer -a quantus -o qtc.kryptex.network:7049 -u YOUR_WALLET.rig1
```

</details>

<details>
<summary><strong>Parano1d</strong> · stratum.innovlab.cc:19601 (TLS)</summary>

```bash
./azminer -a parano1d -o stratum+ssl://stratum.innovlab.cc:19601 -u YOUR_WALLET.rig1
```

Parano1d addresses start with `o1`. Parano1d pools use TLS, so keep the `stratum+ssl://` prefix.

</details>

**You are mining when the log shows accepted shares**, usually within two minutes. Nothing after five minutes? Ask in [Telegram](https://t.me/+DYOBpOCui9EzNDM1) with your GPU, driver version and the last 20 log lines.

## Support

- **Telegram** for quick questions and release news: https://t.me/+DYOBpOCui9EzNDM1
- **Discord** for overclock settings and GPU-by-GPU channels: https://discord.gg/uzDmVnyups
- **GitHub Issues** for bugs: include the azminer version, OS, driver version, GPU, selected coin and the log.

Release announcements are posted in both communities and on https://azminer.app.

## Download

Current version: **v1.2.11** · every file has a SHA-256 in [SHA256SUMS](https://github.com/azminer-app/azminer/releases/download/v1.2.11/SHA256SUMS). See [Releases](https://github.com/azminer-app/azminer/releases/latest) for every build and its changelog.

New in v1.2.11:

- Pearl and Quantus hashrate up 1–5% over v1.2.10, depending on the GPU.
- Better power efficiency (more hashes per watt) on Pearl and Quantus.
- Parano1d and Vearl unchanged.

v1.2.10 added higher hashrate on Volta, Ampere, Ada and Blackwell GPUs, AMD compatibility fixes on Windows, correct Vearl sigma reporting and miner-side figures that match the pool.

| Platform | Download | Requirements |
| --- | --- | --- |
| **Windows x86_64** | [ZIP package](https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11-windows-x86_64.zip) | Windows 10/11 and an NVIDIA driver |
| **Linux x86_64** | [Standalone binary](https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11-linux-x86_64) | GLIBC ≥ 2.28 and an NVIDIA driver |
| **HiveOS / rig integration** | [Integration bundle](https://github.com/azminer-app/azminer/releases/download/v1.2.11/azminer-v1.2.11.tar.gz) | Binary, integration scripts and setup instructions |

<details>
<summary><strong>Verify a download</strong></summary>

Download `SHA256SUMS` from the same release. Keep the package's original filename.

```bash
# Linux
sha256sum --ignore-missing -c SHA256SUMS
```

```powershell
# Windows PowerShell
Get-FileHash .\azminer-v1.2.11-windows-x86_64.zip -Algorithm SHA256
```

```bat
:: Windows Command Prompt
certutil -hashfile azminer-v1.2.11-windows-x86_64.zip SHA256
```

Compare the hash with the entry for that filename in `SHA256SUMS`. Download only from this repository or azminer.app.

</details>

## GPU support

AZminer is built for **NVIDIA** GPUs. v1.2.10 also resolves compatibility issues for AMD GPUs on Windows; see the release notes for the current AMD status. The CUDA build targets SM 7.5, 8.6, 8.9 and 12.0 for all coins; Vearl additionally runs on SM 6.0, 6.1, 7.0 and 8.0.

| Architecture | GPU family | Status |
| --- | --- | --- |
| Blackwell | RTX 50xx | Supported |
| Ada Lovelace | RTX 40xx | Supported |
| Ampere | RTX 30xx | Supported |
| Ampere (data center) | A100 | Vearl only |
| Turing | RTX 20xx / GTX 16xx | Supported |
| Volta | Tesla V100 | Vearl only |
| Pascal | GTX 10xx / Tesla P100 | Vearl only |

A GPU is supported if its architecture is listed here. Run `azminer -list` to see detected CUDA devices and route support.

## Performance

v1.2.11 raises Pearl and Quantus hashrate by 1–5% with better power efficiency, on top of the v1.2.10 gains on Volta, Ampere, Ada and Blackwell GPUs. Per-GPU tables are being re-measured for this version and will be published on [azminer.app](https://azminer.app/#benchmarks) and in the release notes. Actual results vary with driver, cooling and tuning. Measured a GPU on v1.2.11? Send it through a [Hashrate report](https://github.com/azminer-app/azminer/issues/new?template=hashrate-report.md).


## Options

Run `azminer --help` for the complete reference. Long options accept both `-long` and `--long`.

| Option | Purpose |
| --- | --- |
| `-a`, `-algo` | Select `pearl`, `quantus`, `parano1d` or `vearl` |
| `-o`, `-pool` | Primary pool endpoint |
| `-u`, `-wal` | Pool login / wallet |
| `-worker` | Worker name |
| `-p`, `-pass` | Pool password; default `x` |
| `-g`, `-gpus` | GPUs to use, e.g. `0,1`; default `all` |
| `-config` | Load a JSON configuration file; CLI flags override it |

### Pool failover

```bash
./azminer -a pearl \
  -o PRIMARY_POOL_HOST:PORT -u YOUR_WALLET.rig1 -p x \
  -pool2 BACKUP_POOL_HOST:PORT -wal2 YOUR_WALLET.rig1 -pass2 x
```

<details>
<summary><strong>Advanced controls and diagnostics</strong></summary>

| Control | Options |
| --- | --- |
| Core / memory offsets | `-cclock`, `-mclock` |
| Power limit | `-powlim` |
| Fan control / temperature target | `-tt` |
| Fan bounds / temperature cap | `-fanmin`, `-fanmax`, `-tmax` |
| Thermal pause / resume | `-tstop`, `-tstart` (°C) |
| GPU duty-cycle cap | `-gpow` (1–100) |
| Reconnect / failover policy | `-fret`, `-ftimeout`, `-retrydelay` |
| Watchdog / restart policy | `-wdog`, `-wdtimeout`, `-rmode` |
| Timed operation / pause windows | `-timeout`, `-pauseat`, `-resumeat` |
| File logging | `-log`, `-logfile`, `-logdir`, `-logsmaxsize` |
| List devices / compiled algorithms | `-list`, `--list-coins` |
| Verify compiled kernels and exit | `--integrity-selftest` |
| Print version | `-V`, `--version` |

GPU tuning uses NVML. Logs use color in a terminal and plain text when piped.

</details>

## Monitoring API

The standalone API listens on `127.0.0.1:3333` by default. Use `-cdmport` to change it (`0`, `port` or `IP:port`).

```bash
curl -s http://127.0.0.1:3333/getstat      # getstat1 format
curl -s http://127.0.0.1:3333/stats.json   # readable JSON
```

The API also answers `miner_getstat1` and `miner_getstat2`. For Vearl, `/stats.json` also reports the sigma rate per GPU (`sigma_per_s`) and in total (`total_sigma_per_s`). In the HiveOS bundle the port is `CUSTOM_API_PORT` in `h-manifest.conf`, default `4068`, and `h-stats.sh` reports hashrate, temperature, fan, shares and bus numbers.

## Troubleshooting

| Issue | Check |
| --- | --- |
| Linux permission error | `chmod +x azminer` |
| Missing GLIBC version | Use GLIBC 2.28 or newer |
| No GPU found | Run `-list`; check the driver and that the architecture is supported |
| Pool connection or authorization error | Endpoint, transport (`stratum+tcp` vs `stratum+ssl`), coin, wallet, worker format, password |
| API or rig statistics unavailable | Standalone uses port 3333, the HiveOS bundle 4068 |
| Unexpected hashrate | Selected coin, GPU, driver, clocks, power limit, temperature |

## About

AZminer is built and maintained by the AZminer team. Binaries are published only on this repository's Releases page and on azminer.app; every release ships with `SHA256SUMS`.

## Dev fee

AZminer charges a 2% dev fee on every coin. It is collected through short, periodic mining intervals on the developer wallet, and every hashrate published here already includes it. Pool fees are separate and set by the pool.

## License

AZminer is proprietary software; all rights reserved. Third-party components retain their own licenses. See the license notice included with each release package.
