# AZminer options

Every option works with one or two dashes (`-pool` and `--pool`). Run `azminer --help` for the short list.

```bash
./azminer -a <coin> -o <pool> -u <wallet>[.worker] [options]
```

## Required

| Option | Value | Description |
| --- | --- | --- |
| `-a`, `-algo` | `vearl`, `pearl`, `quantus`, `parano1d` | Coin to mine |
| `-o`, `-pool` | `host:port` | Pool address. Add `stratum+ssl://` for TLS |
| `-u`, `-wal` | `wallet` or `wallet.worker` | Your wallet address |

## Pool

| Option | Value | Description |
| --- | --- | --- |
| `-p`, `-pass` | text | Pool password. Default `x` |
| `-worker` | name | Worker name shown on the pool |
| `-pool2` | `host:port` | Backup pool, used when the primary fails |
| `-wal2`, `-pass2`, `-worker2` | as above | Login for the backup pool. Needs `-pool2` |
| `-fret`, `-ftimeout`, `-ptimeout`, `-retrydelay` | number | Reconnect and failover policy |

## GPUs

| Option | Value | Description |
| --- | --- | --- |
| `-g`, `-gpus` | `all` or `0,1` | GPUs to use. Default `all` |
| `-list` | | List GPUs and which coins each can mine |
| `-gpow` | `1`–`100` | GPU duty-cycle cap |

## Tuning

One value applies to every GPU; a comma list sets one value per GPU.

| Option | Value | Description |
| --- | --- | --- |
| `-cclock`, `-mclock` | MHz | Core / memory clock offset |
| `-cclock-lock` (`-lcc`) | MHz | Lock the core clock, e.g. `-lcc 1500` |
| `-mclock-lock` (`-lmc`) | MHz | Lock the memory clock, e.g. `-lmc 405,810` |
| `-powlim` | number | Power limit |
| `-resetoc`, `-leaveoc` | | Reset or keep overclock settings |

## Fans and temperature

| Option | Value | Description |
| --- | --- | --- |
| `-tt` | °C | Target temperature; turns on fan control |
| `-fanmin`, `-fanmax` | % | Fan speed range. Needs `-tt` |
| `-fcm` | number | Fan control mode. `-fcm 0` cannot be used with other fan options |
| `-tmax` | °C | Temperature cap |
| `-tstop`, `-tstart` | °C | Pause a GPU at `-tstop`, resume at `-tstart` (must be lower) |

## Stability

| Option | Value | Description |
| --- | --- | --- |
| `-wdog`, `-wdtimeout`, `-rmode` | | Watchdog and restart policy |
| `-timeout` | number | Stop after a set time |
| `-pauseat`, `-resumeat` | `hh:mm` | Pause and resume every day (24-hour). Use both |

## Monitoring and logs

| Option | Value | Description |
| --- | --- | --- |
| `-cdmport` | `0`, `port` or `IP:port` | Stats API. Default `127.0.0.1:3333`; `0` turns it off |
| `-log`, `-logfile`, `-logdir`, `-logsmaxsize` | | File logging with a size cap |
| `-quiet`, `-nocolor`, `-debug` | | Less output, no colors, extra diagnostics |
| `-gswin`, `-gsi` | | Stats display |
| `-config` | `file.json` | Read options from a JSON file. Command-line options win |

API endpoints: `/getstat` (getstat1 format) and `/stats.json`.

## NVIDIA CMP (Linux, run with sudo)

For CMP 40HX, 50HX, 70HX and 90HX on driver 610.43.03. Use one at a time.

| Option | Description |
| --- | --- |
| `--cmp-status` | Show the driver state and exit |
| `--cmp-unlock` | Install the unlock patch if needed. Add `-a …` to start mining after |
| `--cmp-reunlock` | Rebuild and reinstall the patch |
| `--cmp-rollback` | Restore the previous driver |

Exit code `75` means reboot to load the driver.

## Other

| Option | Description |
| --- | --- |
| `--list-coins` | List supported coins |
| `--integrity-selftest` | Verify the miner files and exit |
| `-V`, `--version` | Print the version |
| `-h`, `--help` | Show help |

## Examples

```bash
# Backup pool
./azminer -a pearl -o prl.kryptex.network:7048 -u YOUR_WALLET.rig1 \
  -pool2 BACKUP_HOST:PORT -wal2 YOUR_WALLET.rig1

# Fan control and thermal pause
./azminer -a vearl -o pool.vearl.org:3333 -u YOUR_WALLET.rig1 \
  -tt 65 -fanmin 40 -fanmax 90 -tstop 85 -tstart 75

# Mine only at night
./azminer -a quantus -o qtc.kryptex.network:7049 -u YOUR_WALLET.rig1 \
  -pauseat 07:00 -resumeat 22:00
```

Questions or a missing option? [Open an issue](https://github.com/azminer-app/azminer/issues).
