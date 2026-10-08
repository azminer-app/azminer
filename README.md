<div align="center">

# azminer

**Native NVIDIA GPU miner for Pearl and Quantus.**

One executable. Per-GPU tuning. Pool failover. Monitoring built for rigs.

[![Release](https://img.shields.io/github/v/release/azminer-app/azminer?label=release&color=2563eb)](https://github.com/azminer-app/azminer/releases/latest)
[![Platforms](https://img.shields.io/badge/platform-Windows%20%7C%20Linux-334155)](#download)
[![GPU](https://img.shields.io/badge/GPU-NVIDIA-76b900)](#supported-gpus)
[![Dev fee](https://img.shields.io/badge/dev%20fee-2%25-f59e0b)](#coins-and-fees)
[![License](https://img.shields.io/badge/license-Proprietary-64748b)](#license)

[**Download**](https://github.com/azminer-app/azminer/releases/latest) · [Quick start](#quick-start) · [GPU support](#supported-gpus) · [Performance](#measured-performance) · [Options](#options) · [API](#monitoring-api)

</div>

## At a glance

| Coins | Hardware | Platforms | Dev fee |
| :--- | :--- | :--- | :--- |
| **Pearl · Quantus** | **NVIDIA GPUs** | **Windows · Linux** | **2% per coin** |

**azminer** is a self-contained native miner. Select a coin, set your pool and wallet, and start mining. An NVIDIA driver is required.

- **GPU control:** clock offsets, power limits, fans and device selection.
- **Thermal protection:** target-temperature control and automatic pause/resume.
- **Rig operation:** pool failover, a stall watchdog and scheduled operation.
- **Monitoring:** statistics API, readable JSON and bundled integration scripts.
- **Logging:** color terminal output, plain piped output and bounded log files.

The proof-of-work runs on reference CUDA kernels. The surrounding Stratum, failover, tuning, thermal control, watchdog, API and logging implementation is written specifically for azminer.

## Download

Get the current version from [Releases](https://github.com/azminer-app/azminer/releases/latest). The links below are pinned to **v1.2.1**, the version documented here.

| Platform | Download | Requirements / contents |
| --- | --- | --- |
| **Windows** x86_64 | [Download ZIP](https://github.com/azminer-app/azminer/releases/download/v1.2.1/azminer-v1.2.1-windows-x86_64.zip) | Windows 10/11 and an NVIDIA driver. Includes `start-pearl.bat` and `start-quantus.bat`. |
| **Linux** x86_64 | [Download binary](https://github.com/azminer-app/azminer/releases/download/v1.2.1/azminer-v1.2.1-linux-x86_64) | Standalone executable. GLIBC **2.28 or newer** and an NVIDIA driver. |
| **Rig integration** | [Download bundle](https://github.com/azminer-app/azminer/releases/download/v1.2.1/azminer-v1.2.1.tar.gz) | Custom-miner bundle with the binary, `h-*.sh` integration scripts and package README. |
| **Checksums** | [SHA256SUMS](https://github.com/azminer-app/azminer/releases/download/v1.2.1/SHA256SUMS) | SHA-256 checksums for the release downloads. |

See [Verify your download](#verify-your-download) for checksum commands.

## Quick start

Replace `YOUR_PEARL_WALLET` or `YOUR_QUANTUS_WALLET` with your own payout address, and `POOL_HOST:PORT` with your chosen pool endpoint. The `.rig1` suffix is the worker name in these examples; use the login format your pool expects.

### Windows

1. Download and extract the Windows ZIP.
2. Open `start-pearl.bat` or `start-quantus.bat` in a text editor.
3. Set `WALLET` to your address for the selected coin, save the file and double-click the launcher.

For example, in `start-pearl.bat`:

```bat
set "WALLET=YOUR_PEARL_WALLET"
```

To run directly from the extracted folder:

```bat
:: Pearl
azminer.exe -a pearl ^
  -o stratum+tcp://POOL_HOST:PORT ^
  -u YOUR_PEARL_WALLET.rig1

:: Quantus
azminer.exe -a quantus ^
  -o stratum+tcp://POOL_HOST:PORT ^
  -u YOUR_QUANTUS_WALLET.rig1
```

### Linux

Download the executable:

```bash
curl -fL -o azminer https://github.com/azminer-app/azminer/releases/download/v1.2.1/azminer-v1.2.1-linux-x86_64
chmod +x azminer
```

Then choose one coin:

```bash
# Pearl
./azminer -a pearl \
  -o stratum+tcp://POOL_HOST:PORT \
  -u YOUR_PEARL_WALLET.rig1
```

```bash
# Quantus
./azminer -a quantus \
  -o stratum+tcp://POOL_HOST:PORT \
  -u YOUR_QUANTUS_WALLET.rig1
```

Use the endpoint, wallet, worker and password format documented by your selected pool.

## Coins and fees

| Coin | Algorithm | Selection | Miner dev fee |
| --- | --- | --- | --- |
| Pearl (PRL) | `pearlhash` | `-a pearl` | **2%** |
| Quantus | `qpow-poseidon2` | `-a quantus` | **2%** |

The dev fee is collected through short, periodic mining intervals using the developer wallet. **Published hashrate figures in this README already account for that fee.** Any pool fee is separate and follows the pool's own terms.

List the algorithms compiled into your build:

```bash
./azminer --list-coins
```

## Supported GPUs

azminer is **NVIDIA-only**. The current build targets **SM 7.5, 8.6, 8.9 and 12.0**.

| Generation | Example cards | Status |
| --- | --- | --- |
| Blackwell | RTX 50xx | Supported |
| Ada Lovelace | RTX 40xx | Supported |
| Ampere | RTX 30xx | Supported |
| Turing | RTX 20xx / GTX 16xx | Supported |
| Pascal | GTX 10xx | Planned; not supported in v1.2.1 |

Check the detected CUDA devices and route support on your machine:

```bash
./azminer -list
```

## Measured performance

The following figures are from the project's hardware regression sweep with approximately stock clocks. Driver versions, cooling, power limits and overclock settings affect results. The hashrates account for the 2% miner dev fee.

**Compare hashrate within the same coin.** Pearl uses TH/s here; Quantus uses MH/s. The two algorithms' numbers are not directly comparable.

| GPU | Generation | Pearl | Quantus | Reported power |
| --- | --- | ---: | ---: | ---: |
| RTX 4070 Ti | Ada | 162.6 TH/s | 693.6 MH/s | ~284 W |
| RTX 4070 SUPER | Ada | 143.2 TH/s | 613.7 MH/s | ~218 W |
| RTX 5060 Ti | Blackwell | 99.5 TH/s | 412.4 MH/s | ~165 W |
| RTX 5070 Ti | Blackwell | 96.5 TH/s | 382.0 MH/s | ~94 W |
| RTX 4060 Ti | Ada | 89.6 TH/s | 387.2 MH/s | ~165 W |
| RTX 3070 | Ampere | 80.0 TH/s | 355.2 MH/s | ~219 W |

<details>
<summary><strong>View results for 10 more GPUs</strong></summary>

| GPU | Generation | Pearl | Quantus | Reported power |
| --- | --- | ---: | ---: | ---: |
| RTX 3080 | Ampere | 75.3 TH/s | 407.9 MH/s | ~190 W |
| RTX 3060 Ti | Ampere | 65.3 TH/s | 296.1 MH/s | ~180 W |
| RTX 4060 | Ada | 61.8 TH/s | 283.0 MH/s | ~114 W |
| RTX 2070 SUPER | Turing | 61.5 TH/s | 285.1 MH/s | ~206 W |
| RTX 2060 | Turing | 49.5 TH/s | 231.8 MH/s | ~188 W |
| RTX 3060 | Ampere | 43.5 TH/s | 201.1 MH/s | ~110 W |
| RTX 3060 Laptop | Ampere | 34.0 TH/s | 177.4 MH/s | ~60 W |
| RTX 5060 Laptop | Blackwell | 27.8 TH/s | 143.9 MH/s | ~27 W |
| GTX 1660 Ti | Turing | 1.18 TH/s | 175.0 MH/s | ~57 W |
| GTX 1660 SUPER | Turing | 1.08 TH/s | 162.8 MH/s | ~59 W |

</details>

Quantus results reflect the approximately **10% throughput improvement in v1.2.1**. Each row includes one reported power figure; separate power measurements for each coin are not provided in this table.

## Pool configuration

### Wallet and worker

Use the pool's expected login string in `-u`. For a pool that accepts `wallet.worker`:

```bash
./azminer -a pearl \
  -o stratum+tcp://POOL_HOST:PORT \
  -u YOUR_PEARL_WALLET.rig1 -p x
```

`-worker` can append a worker name. Use either the complete login in `-u` or the separate worker option, according to your pool's requirements.

### Secondary pool

Set the backup endpoint and its credentials explicitly:

```bash
./azminer -a pearl \
  -o PRIMARY_POOL_HOST:PORT -u YOUR_PEARL_WALLET.rig1 -p x \
  -pool2 BACKUP_POOL_HOST:PORT -wal2 YOUR_PEARL_WALLET.rig1 -pass2 x
```

Replace both endpoints before running. Reconnect and failover behavior is configurable with `-fret`, `-ftimeout` and `-retrydelay`; consult `--help` for their values.

If the URL scheme is omitted, azminer defaults to `stratum+tcp://`. Supply the endpoint and transport documented by your pool.

## GPU selection and tuning

### Select cards

First run `-list` to identify the GPU indices. For example, mine on GPUs 0 and 1:

```bash
./azminer -a pearl \
  -o stratum+tcp://POOL_HOST:PORT \
  -u YOUR_PEARL_WALLET.rig1 -gpus 0,1
```

The default is `all`. Use `-gpow` to cap GPU duty cycle from **1 to 100**.

### Clocks, power and temperature

| Setting | Options |
| --- | --- |
| Core and memory offsets | `-cclock`, `-mclock` |
| Power limit | `-powlim` |
| Fan control / target | `-tt` |
| Fan bounds and temperature cap | `-fanmin`, `-fanmax`, `-tmax` |
| Thermal pause and resume | `-tstop`, `-tstart` |

These controls use NVML. Run `azminer --help` for the accepted units, per-GPU syntax and values before applying settings.

## Options

Long options accept both `-long` and `--long`. The table below is a reference to common controls; `azminer --help` is the complete reference for your installed build.

<details>
<summary><strong>View the full option reference</strong></summary>

### Mining and connection

| Option | Argument | Purpose |
| --- | --- | --- |
| `-a`, `-algo` | `pearl` or `quantus` | Select the coin / algorithm. |
| `-o`, `-pool` | `[scheme://]host:port` | Primary pool; defaults to TCP when the scheme is omitted. |
| `-u`, `-wal` | Login / wallet | Pool login. |
| `-worker` | Name | Append a worker name. |
| `-p`, `-pass` | Password | Pool password; default `x`. |
| `-pool2`, `-wal2`, `-pass2` | Pool / login / password | Secondary pool and its credentials. |
| `-fret`, `-ftimeout`, `-retrydelay` | See `--help` | Reconnect and failover policy. |
| `-g`, `-gpus` | `all` or indices such as `0,1` | Select GPUs; default `all`. |
| `-gpow` | `1..100` | Cap GPU duty cycle. |
| `-config` | JSON file path | Load configuration; CLI flags override file settings. |

### Rig operation and monitoring

| Option | Purpose |
| --- | --- |
| `-cclock`, `-mclock`, `-powlim` | Per-GPU clock offsets and power limits. |
| `-tt`, `-fanmin`, `-fanmax`, `-tmax` | Fan control and temperature management. |
| `-tstop`, `-tstart` | Thermal pause/resume thresholds in °C. |
| `-wdog`, `-wdtimeout`, `-rmode` | Watchdog and restart policy. |
| `-timeout`, `-pauseat`, `-resumeat` | Timed operation and scheduled pause/resume. |
| `-cdmport` | API address: `0`, `port` or `IP:port`; default `127.0.0.1:3333`. |
| `-log`, `-logfile`, `-logdir`, `-logsmaxsize` | File logging and size bounds. |

### Information and diagnostics

| Option | Purpose |
| --- | --- |
| `-list` | List CUDA devices and route support. |
| `--list-coins` | List compiled algorithms. |
| `--integrity-selftest` | Authenticate the compiled kernels and exit. |
| `-V`, `--version` | Print the version. |
| `--help` | Print all options. |

</details>

## Monitoring API

The standalone miner's API defaults to **`127.0.0.1:3333`**. Set the port or bind address with `-cdmport`.

| Interface | Purpose |
| --- | --- |
| `miner_getstat1` / `miner_getstat2` | Statistics methods for compatible monitoring integrations. |
| `GET /getstat` | getstat1 statistics over HTTP. |
| `GET /stats.json` | Readable JSON statistics. |

```bash
curl -s http://127.0.0.1:3333/getstat
curl -s http://127.0.0.1:3333/stats.json
```

For an explicit local address:

```bash
./azminer -a pearl \
  -o stratum+tcp://POOL_HOST:PORT \
  -u YOUR_PEARL_WALLET.rig1 \
  -cdmport 127.0.0.1:3333
```

Compatible monitoring integrations can consume these statistics. The [rig integration bundle](#rig-integration) uses port **4068** by default, rather than the standalone default of 3333.

## Rig integration

Download the [custom-miner bundle](https://github.com/azminer-app/azminer/releases/download/v1.2.1/azminer-v1.2.1.tar.gz), extract it and follow the package README.

Install the extracted `azminer/` folder in your rig manager's custom-miner directory and make the binary executable. Configure the miner using these values:

| Field | Value |
| --- | --- |
| Miner | Custom |
| Miner name | `azminer` |
| Wallet template | Your wallet and worker in the format the pool expects, e.g. `wallet.worker` |
| Pool URL | The pool's `host:port` or complete endpoint |
| Algorithm | `pearl` or `quantus` |
| Extra config arguments | Optional; for example, `-g 0` or `-g all` |

The bundle's `h-stats.sh` reports hashrate, temperature, fan, shares and bus numbers to the rig manager. Its API port comes from `CUSTOM_API_PORT` in `h-manifest.conf`, with a default of **4068**. Install the command-line dependencies listed in the package README before using the integration scripts.

## Verify your download

Download [SHA256SUMS](https://github.com/azminer-app/azminer/releases/download/v1.2.1/SHA256SUMS) from the **same release** as your package.

<details>
<summary><strong>Show checksum commands</strong></summary>

### Linux

Keep the downloaded package's original filename and run this command in the download directory:

```bash
sha256sum --ignore-missing -c SHA256SUMS
```

### Windows PowerShell

```powershell
Get-FileHash .\azminer-v1.2.1-windows-x86_64.zip -Algorithm SHA256
```

Compare the returned hash with the entry for that exact filename in `SHA256SUMS`.

### macOS — checking a downloaded package

```bash
shasum -a 256 azminer-v1.2.1-windows-x86_64.zip
```

Compare the result with `SHA256SUMS`. This verifies a downloaded package; azminer builds are available for Windows and Linux.

</details>

## Troubleshooting

<details>
<summary><strong>View common issues and checks</strong></summary>

| Symptom | What to check |
| --- | --- |
| Linux reports `Permission denied` | Make the downloaded binary executable with `chmod +x`. |
| Linux reports a missing GLIBC version | The Linux build requires GLIBC 2.28 or newer. |
| A GPU is missing or unavailable | Run `-list`, check the NVIDIA driver and confirm the GPU is supported. The current build targets the NVIDIA architectures listed above. |
| Pool authorization fails | Check the selected coin, payout address, worker format and password against the pool's instructions. |
| Pool connection fails | Check the host, port and transport. An omitted scheme defaults to `stratum+tcp://`. |
| API requests fail | Check `-cdmport` and the bind address. The standalone default is `127.0.0.1:3333`; the integration bundle defaults to port 4068. |
| Rig statistics are missing | Check the API on the port in `h-manifest.conf`, then confirm `h-stats.sh` and its dependencies are available. |
| Hashrate differs from the table | Compare the same coin and GPU, then check clocks, power limits, temperature and driver version. |

When reporting an issue, include the azminer version, operating system, NVIDIA driver version, GPU model, selected coin and relevant log lines. Remove wallet details or credentials you do not want to publish.

</details>

## FAQ

<details>
<summary><strong>Which coins can I mine?</strong></summary>

Pearl with `-a pearl`, or Quantus with `-a quantus`.

</details>

<details>
<summary><strong>Can I choose my own pool?</strong></summary>

Yes. Set your primary endpoint with `-o` and an optional backup with `-pool2`. Use the endpoint and login format documented by the pool.

</details>

<details>
<summary><strong>Do the benchmark figures include the dev fee?</strong></summary>

Yes. Published hashrates already account for the 2% miner dev fee on both coins.

</details>

<details>
<summary><strong>Do I need an interpreter or separate runtime?</strong></summary>

No. The miner is a self-contained executable. Install an NVIDIA driver and meet the platform requirements in [Download](#download).

</details>

<details>
<summary><strong>Which GPUs are supported?</strong></summary>

azminer is NVIDIA-only. See [Supported GPUs](#supported-gpus) for the architectures supported by v1.2.1. Pascal support is planned.

</details>

<details>
<summary><strong>Can I configure it from a file?</strong></summary>

Yes. Use `-config` with a JSON configuration file. Command-line flags take precedence over the file.

</details>

## Support

For bug reports and feature requests, [open a GitHub issue](https://github.com/azminer-app/azminer/issues). For downloads and version-specific changes, see [Releases](https://github.com/azminer-app/azminer/releases).

## License

azminer is proprietary software; all rights reserved. Third-party components retain their own licenses. See the license notice included with each release package.
