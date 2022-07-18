# Overview

Syncing an Ethereum node is largely reliant on IOPS, I/O Per Second. Budget SSDs will struggle to an extent, and some won't be able to sync at all.
This document aims to snapshot some known good and known bad models.

High-level, QLC and DRAMless are far slower than "mainstream" SSDs

# The Good

"Mainstream" models that can sync mainnet Geth in a reasonable amount of time

## Hardware

- SK Hynix P31 Gold
- Samsung 970 EVO Plus 2TB (the 1TB model is slower but should still work)
- WD Red SN700
- WD Black SN750 (but not SN750 SE)
- HP EX950
- Mushkin Pilot-E
- AData XPG Gammix S50 Lite
- WD 3D NAND SATA
- Crucial MX500 SATA
- Samsung 870 EVO SATA

Honorable Pi4 mention:

- Samsung T5 USB - works but is slow, avoid if at all possible and go for M.2 NVMe instead

Performance NVMe will of course also work, like SK Hynix P41 Platinum, Sabrent Rocket 4 Plus, WD Black SN850(X), Samsung 980 Pro, Kingston KC2000, ADATA XPG Gammix S70, &c.

## Cloud

- Netcup RS Servers / VPS Servers. RS is faster.
- AWS gp3 w/ 10k IOPS provisioned
- Any baremetal/dedicated server service

# The Bad

These models are reportedly too slow to sync mainnet Geth.

## Hardware

- WD Green SN350
- Crucial BX500 SATA
- Kingston NV1 (?)
- Samsung T7 USB, but depends on firmware - with the right firmware it might still sync

## Cloud

- Contabo SSD (results mixed - Contabo NVMe is fine)

# The Ugly

"Budget" models that can sync mainnet Geth, if slowly

## Hardware

- TBD