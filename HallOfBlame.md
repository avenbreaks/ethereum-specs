# Overview

Syncing an Ethereum node is largely reliant on IOPS, I/O Per Second. Budget SSDs will struggle to an extent, and some won't be able to sync at all.

This document aims to snapshot some known good and known bad models. 

For size, 2TB come recommended as of mid-2022. 1TB can work for now but is [getting tight](https://gist.github.com/yorickdowne/e4b271e1cbe8c8873884e08705084362).

High-level, QLC and DRAMless are far slower than "mainstream" SSDs.

IOPS wise, Erigon uses the least by far, then Geth, then Besu, then Nethermind in ascending order of IOPS requirements.

Other than a slow SSD, these are things that can slow IOPS down:
- Heat. Check with `smartctl -x`; the SSD should be below 50C so it does not throttle.
- TRIM not being allowed. This can happen with some hardware RAID controllers, as well as on macOS with non-Apple SSDs

# The Good

"Mainstream" models that can sync mainnet Geth in a reasonable amount of time. Use M.2 NVMe if your machine supports it.

## Hardware

M.2 NVMe
- SK Hynix P31 Gold
- Samsung 970 EVO Plus 2TB (the 1TB model is slower but should still work)
- WD Red SN700
- WD Black SN750 (but not SN750 SE)
- HP EX950
- Mushkin Pilot-E
- Mushkin Redline Vortex
- AData XPG Gammix S50 Lite

2.5" SATA
- WD Blue 3D NAND SATA
- Crucial MX500 SATA
- Samsung 870 EVO SATA

Honorable Pi4 mention:

- Samsung T5 USB - works but is slow, avoid if at all possible and go for M.2 NVMe instead, with [Rock5 B](https://ameridroid.com/products/rock5-model-b) or [CM4](https://thepihut.com/products/pci-e-to-m-2-adapter-for-raspberry-pi-cm4-io-board). To clarify: If you stay with Pi4, then T5 USB or USB M.2 NVMe adapter should roughly perform the same. Choose either. Maybe consider upgradability in future.

Performance NVMe will of course also work, like SK Hynix P41 Platinum, Sabrent Rocket 4 Plus, WD Black SN850(X), Samsung 980 Pro, Kingston KC2000, ADATA XPG Gammix S70, &c.

## Cloud

- Netcup RS Servers / VPS Servers. RS is faster.
- AWS gp3 w/ 10k IOPS provisioned and an m6i/a.xlarge
- Any baremetal/dedicated server service
- Contabo NVMe - fast enough but not enough space. 800 GiB is not sufficient.

# The Bad

These models are reportedly too slow to sync mainnet Geth. If you have one of these, try Erigon.

## Hardware

- WD Green SN350
- Kingston NV1 ([probably](https://www.techpowerup.com/290339/psa-kingston-nv1-ssd-comes-with-a-hardware-spec-lottery-tlc-or-qlc-smi-or-phison) QLC and DRAMless and thus too slow on 2TB, but could be "anything" as Kingston do not guarantee specific components)
- Anything both QLC and DRAMless will likely not be able to sync at all or not be able to consistently keep up with "chain head"
- Crucial BX500 SATA, HP S650 SATA, probably most SATA budget drives
- Samsung T7 USB, even with current firmware

## Cloud

- Contabo SSD

# The Ugly

"Budget" models that can sync mainnet Geth, if slowly

## Hardware

- Inland Professional 3D NAND - it's QLC but reportedly can sync Geth
- Seagata Barracuda Q5 - it's QLC but reportedly can sync Geth