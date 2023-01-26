# Overview

Syncing an Ethereum node is largely reliant on IOPS, I/O Per Second. Budget SSDs will struggle to an extent, and some won't be able to sync at all.

This document aims to snapshot some known good and known bad models. 

For size, 2TB come recommended as of mid-2022. 1TB can work for now but is [getting tight](https://gist.github.com/yorickdowne/e4b271e1cbe8c8873884e08705084362).

High-level, QLC and DRAMless are far slower than "mainstream" SSDs.

IOPS wise, it's likely Geth, then Besu, then Nethermind in ascending order of IOPS requirements. I am not quite sure where Erigon fits these days.

Other than a slow SSD, these are things that can slow IOPS down:
- Heat. Check with `smartctl -x`; the SSD should be below 50C so it does not throttle.
- TRIM not being allowed. This can happen with some [hardware RAID controllers](https://gist.github.com/yorickdowne/fd36009c19fdbee0337bffc0d5ad8284), as well as on macOS with [non-Apple SSDs](https://www.lifewire.com/enable-trim-for-ssd-in-os-x-yosemite-2260789)
- On SATA, the controller in UEFI/BIOS set to anything other than [AHCI](https://www.addictivetips.com/windows-tips/enable-ahci-bios/)

If you haven't already, do [turn off atime](https://opensource.com/article/20/6/linux-noatime) on your DB volume, it'll increase SSD lifetime and speed things up a little bit.

The drive lists are ordered by interface and alphabetically by vendor name, not by preference.

# The Good

"Mainstream" models that can sync mainnet Geth in a reasonable amount of time. Use M.2 NVMe if your machine supports it.
Note that in some cases older "Performance" PCIe 4 models can go for less than a PCIe 3 "mainstream" drive - shop around.

## Hardware

M.2 NVMe "mainstream" - TLC, DRAM, PCIe 3
- AData XPG Gammix S50 Lite
- HP EX950
- Mushkin Pilot-E
- Samsung 970 EVO Plus 2TB, [pre-rework](https://www.tomshardware.com/news/samsung-is-swapping-ssd-parts-too) (firmware 2B2QEXM7)
- Samsung 970 EVO Plus 2TB, [post-rework](https://www.tomshardware.com/news/samsung-is-swapping-ssd-parts-too) (firmware 3B2QEXM7 or 4B2QEXM7). In testing this syncs just as quickly as the pre-rework drive. 
- SK Hynix P31 Gold
- WD Black SN750 (but not SN750 SE)
- WD Red SN700

2.5" SATA "mainstream" - TLC, DRAM
- Crucial MX500 SATA
- Samsung 860/870 EVO SATA
- WD Blue 3D NAND SATA

Honorable Pi4 mention:

- Samsung T5 USB - works but is slow, avoid if at all possible and go for M.2 NVMe instead, with [Rock5 B](https://ameridroid.com/products/rock5-model-b) or [CM4](https://thepihut.com/products/pci-e-to-m-2-adapter-for-raspberry-pi-cm4-io-board). To clarify: If you stay with Pi4, then T5 USB or USB M.2 NVMe adapter should roughly perform the same. Choose either. Maybe consider going for NVMe and a USB adapter so you can upgrade to a Rock5 B in future.

Performance NVMe will of course also work. This is a non-exhaustive list!
- ADATA XPG Gammix S70
- Corsair Force MP600
- Crucial P5 Plus
- Kingston KC2000 / KC3000 / Fury Renegade
- Mushkin Redline Vortex
- Sabrent Rocket 4 Plus
- Samsung 980 Pro (**not** 980)
- Samsung 990 Pro - there are reports of 990 Pro [rapidly losing health](https://www.tomshardware.com/news/samsung-990-pro-health-dropping-fast)
- SK Hynix P41 Platinum
- WD Black SN850(X)
- &c. A "performance NVMe" uses TLC, DRAM, and PCIe 4 or 5.

Likewise, data center SSD drives will work well.

## Cloud

- Any baremetal/dedicated server service
- AWS gp3 w/ >=10k IOPS provisioned and an m6i/a.xlarge

# The Bad

These models are reportedly too slow to sync mainnet Geth.

## Hardware

- Crucial P3 Plus, QLC and DRAMless - users report that it can sync Geth but cannot stay in sync for Nethermind
- Kingston NV1 ([probably](https://www.techpowerup.com/290339/psa-kingston-nv1-ssd-comes-with-a-hardware-spec-lottery-tlc-or-qlc-smi-or-phison) QLC and DRAMless and thus too slow on 2TB, but could be "anything" as Kingston do not guarantee specific components)
- Kingston NV2 (too slow to sync Nethermind in testing; might be able to sync Geth; like NV1 no guaranteed components)
- WD Green SN350
- Anything both QLC and DRAMless will likely not be able to sync at all or not be able to consistently keep up with "chain head"
- Crucial BX500 SATA, HP S650 SATA, probably most SATA budget drives
- Samsung 980 - unsure, this may belong in "Ugly". If you have one and can say for sure, please come to ethstaker Discord.
- Samsung T7 USB, even with current firmware

## Cloud

- Contabo SSD
- Netcup VPS Servers - reportedly able to sync Geth but not Nethermind

# The Ugly

"Budget" models that reportedly can sync mainnet Geth, if slowly

## Hardware

- Corsair MP400, QLC
- Inland Professional 3D NAND, QLC
- Intel 660p, QLC
- Seagata Barracuda Q5, QLC
- WD Black SN770, DRAMless
- Samsung 870 QVO SATA, QLC

## Cloud

- Contabo NVMe - fast enough but not enough space. 800 GiB is not sufficient.
- Netcup RS Servers. Reportedly fast enough to sync Nethermind or Geth; still no speed demon.