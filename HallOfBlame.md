# Overview

Syncing an Ethereum node is largely reliant on IOPS, I/O Per Second. Budget SSDs will struggle to an extent, and some won't be able to sync at all.

This document aims to snapshot some known good and known bad models. 

For size, 2TB come recommended as of mid-2022. 1TB can work for now but is [getting tight](https://gist.github.com/yorickdowne/e4b271e1cbe8c8873884e08705084362).

High-level, QLC and DRAMless are far slower than "mainstream" SSDs.

IOPS wise, it's likely Geth, then Besu, then Nethermind in ascending order of IOPS requirements. I am not quite sure where Erigon fits these days.

Other than a slow SSD model, these are things that can slow IOPS down:
- Heat. Check with `smartctl -x`; the SSD should be below 50C so it does not throttle.
- TRIM not being allowed. This can happen with some [hardware RAID controllers](https://gist.github.com/yorickdowne/fd36009c19fdbee0337bffc0d5ad8284), as well as on macOS with [non-Apple SSDs](https://www.lifewire.com/enable-trim-for-ssd-in-os-x-yosemite-2260789)
- On SATA, the controller in UEFI/BIOS set to anything other than [AHCI](https://www.addictivetips.com/windows-tips/enable-ahci-bios/). Set it to AHCI for good performance.

If you haven't already, do [turn off atime](https://opensource.com/article/20/6/linux-noatime) on your DB volume, it'll increase SSD lifetime and speed things up a little bit.

The drive lists are ordered by interface and alphabetically by vendor name, not by preference. The lists are not exhaustive at all. @mwpastore linked a [filterable spreadsheet](https://docs.google.com/spreadsheets/d/1B27_j9NDPU3cNlj2HKcrfpJKHkOf-Oi1DbuuQva2gT4/edit#gid=0) in comments that has a far greater variety of drives and their characteristics.

# The Good

"Mainstream" and "Performance" drive models that can sync mainnet execution layer clients in a reasonable amount of time. Use M.2 NVMe if your machine supports it.

Note that in some cases older "Performance" PCIe 4 drives can be bought at a lower price than a PCIe 3 "Mainstream" drive - shop around.

- Often on sale: Samsung 970 EVO Plus, SK Hynix P31 Gold
- Higher TBW than most: Seagate Firecuda 530, WD Red SN700
- Lowest power draw: SK Hynix P31 Gold - great choice for Rock5 B and other low-power devices

We've started crowd-sourcing some IOPS numbers. If you want to join the fun, run `fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=150G --readwrite=randrw --rwmixread=75` and give us the read and write IOPS. Don't forget to `rm test` after.

## Hardware

M.2 NVMe "Mainstream" - TLC, DRAM, PCIe 3
- AData XPG Gammix S11/SX8200 Pro. [Several hardware revisions](https://www.tomshardware.com/news/adata-switches-nand-on-sx8200-pro-ssd-performance-impacted). It's slower than some QLC drives. 68k/22k r/w IOPS
- AData XPG Gammix S50 Lite
- HP EX950
- Mushkin Pilot-E
- Samsung 970 EVO Plus 2TB, [pre-rework](https://www.tomshardware.com/news/samsung-is-swapping-ssd-parts-too) (firmware 2B2QEXM7). 140k/46k r/w IOPS
- Samsung 970 EVO Plus 2TB, [post-rework](https://www.tomshardware.com/news/samsung-is-swapping-ssd-parts-too) (firmware 3B2QEXM7 or 4B2QEXM7). In testing this syncs just as quickly as the pre-rework drive
- SK Hynix P31 Gold
- WD Black SN750 (but not SN750 SE)
- WD Red SN700

2.5" SATA "Mainstream" - TLC, DRAM
- Crucial MX500 SATA
- Samsung 860 EVO SATA. 
- Samsung 870 EVO SATA. 63k/20k IOPS
- WD Blue 3D NAND SATA

Honorable Pi4 mention:

- Samsung T5 USB - works but is slow, avoid if at all possible and go for M.2 NVMe instead, with [Rock5 B](https://ameridroid.com/products/rock5-model-b) or [CM4](https://thepihut.com/products/pci-e-to-m-2-adapter-for-raspberry-pi-cm4-io-board). To clarify: If you stay with Pi4, then T5 USB or USB M.2 NVMe adapter should roughly perform the same. Choose either. Maybe consider going for NVMe and a USB adapter so you can upgrade to a Rock5 B in future.

M.2 NVMe "Performance" - TLC, DRAM, PCIe 4 or 5
- ADATA XPG Gammix S70
- Corsair Force MP600
- Crucial P5 Plus
- Kingston KC2000 / KC3000 / Fury Renegade
- Mushkin Redline Vortex
- Sabrent Rocket 4 Plus
- Samsung 980 Pro (**not** 980) - a [firmware update](https://www.tomshardware.com/news/samsung-980-pro-ssd-failures-firmware-update) to 5B2QGXA7 is necessary to keep them from dying, if they are firmware 3B2QGXA7. Samsung's boot Linux is a bit broken, you may want to flash [from your own Linux](https://blog.quindorian.org/2021/05/firmware-update-samsung-ssd-in-linux.html/).
- Samsung 990 Pro - there are reports of 990 Pro [rapidly losing health](https://www.tomshardware.com/news/samsung-990-pro-health-dropping-fast). A [firmware update](https://www.tomshardware.com/news/samsung-990-pro-firmware-update-released-ssd-health) to 1B2QJXD7 is meant to stop the rapid degradation, but won't reverse any that happened on earlier firmware. 
- Seagate Firecuda 530
- SK Hynix P41 Platinum / Solidigm P44 Pro
- WD Black SN850
- WD Black SN850X. 101k/33k r/w IOPS

Data center SSD drives will also work well.

## Cloud

- Any baremetal/dedicated server service
- AWS i3en.2xlarge
- AWS gp3 w/ >=10k IOPS provisioned and an m6i/a.xlarge

# The Bad

These "Budget" drive models are reportedly too slow to sync (all) mainnet execution layer clients.

## Hardware

- AData S40G/SX8100 4TB, QLC - the 2TB model is TLC and *should* be fine; 4TB is reportedly too slow
- Crucial P1, QLC - users report it can't sync Nethermind
- Crucial P2 and P3 (Plus), QLC and DRAMless - users report it can't sync Nethermind
- Kingston NV1 - [probably](https://www.techpowerup.com/290339/psa-kingston-nv1-ssd-comes-with-a-hardware-spec-lottery-tlc-or-qlc-smi-or-phison) QLC and DRAMless and thus too slow on 2TB, but could be "anything" as Kingston do not guarantee specific components.
- Kingston NV2 - like NV1 no guaranteed components
- WD Green SN350, QLC and DRAMless
- Anything both QLC and DRAMless will likely not be able to sync at all or not be able to consistently keep up with "chain head"
- Crucial BX500 SATA, HP S650 SATA, probably most SATA budget drives
- Samsung 980, DRAMless - unsure, this may belong in "Ugly". If you have one and can say for sure, please come to [ethstaker Discord](https://discord.io/ethstaker).
- Samsung T7 USB, even with current firmware

## Cloud

- Contabo SSD
- Netcup VPS Servers - reportedly able to sync Geth but not Nethermind

# The Ugly

"Budget" drive models that reportedly can sync mainnet execution layer clients, if slowly. 

Note that QLC drives usually have a markedly lower TBW than TLC, and will fail earlier.

## Hardware

- Corsair MP400, QLC
- Inland Professional 3D NAND, QLC
- Intel 660p, QLC. It's faster than some "mainstream" drives. 98k/33k r/w IOPS
- Seagata Barracuda Q5, QLC
- WD Black SN770, DRAMless
- Samsung 870 QVO SATA, QLC

## Cloud

- Contabo NVMe - fast enough but not enough space. 800 GiB is not sufficient.
- Netcup RS Servers. Reportedly fast enough to sync Nethermind or Geth; still no speed demon.