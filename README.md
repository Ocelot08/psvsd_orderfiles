# psvsd_orderfiles

Eagle design files for Yifan Lu's PlayStation Vita hardware boards: **PSVSD**, **psvemmc**, and a 3G-slot breakout board. This repo contains the schematics, board layouts, BOMs and part libraries for those boards.

All designs are by Yifan Lu (2017–2018) and are licensed under [CC BY 4.0](LICENSE.txt).

## Boards

| Folder | Board | What it does | Size |
|--------|-------|--------------|------|
| [`psvsd/`](psvsd) | PSVSD | microSD adapter for the Vita's 3G modem slot | 30.0 × 33.2 mm |
| [`psvemmc/`](psvemmc) | psvemmc | USB adapter for reading and writing the Vita's internal eMMC | 38–41.5 × 22.5–32.5 mm, depending on version |
| [`breakout/`](breakout) | 3G slot breakout | Exposes every signal on the 3G slot as test pads | 30.0 × 33.4 mm |

All boards are 2-layer.

### PSVSD

PSVSD replaces the 3G modem card in a 3G Vita (PCH-1100) with a microSD card slot. The 3G slot carries USB, so the board is a USB-to-SD card reader in the modem's form factor. Homebrew drivers then mount the card as storage. The silkscreen points to `psvsd.henkaku.xyz`.

Each version also has two optional 5-pin headers (`JP1`, `JP2`) that break out the Vita's **SysCon** and **Kermit** UARTs (RX, TX, CTS, RFR, GND) for debugging.

| File | Silkscreen | Reader IC | Notes | BOM |
|------|------------|-----------|-------|-----|
| `psvsd-v10.sch/.brd` | v1.2 | Microchip USB2244 | Crystal, power-on reset circuit, dual LDO | [`bom.csv`](psvsd/bom.csv) |
| `psvsd-v20.sch/.brd` | v2.0 | Genesys GL823 | Cheaper reader IC, no crystal | [`bom_v2.csv`](psvsd/bom_v2.csv) (Seeed part numbers) |
| `psvsd-v21.sch/.brd` | v2.1 | Genesys GL823 | Simplified power circuit, grounded card shield, 21 parts | [`bom_v21.csv`](psvsd/bom_v21.csv) |

v2.1 is the latest PSVSD design and has the fewest parts.

### psvemmc

psvemmc connects to the eMMC bus on the Vita's motherboard through a 2×10 header and wires. A TI TXS02612 switch routes the eMMC either to the Vita or to a Microchip USB2244 card reader (`SD_SEL`). With the bus routed to the reader, a PC can read and write the eMMC over micro-USB. The header also carries SPI, UART and power-switch/reset lines.

| File | Silkscreen | Changes | BOM |
|------|------------|---------|-----|
| `psvemmc-v10` | v1.1 | First version. Crystal caps added, translator direction fixed | none |
| `psvemmc-v12` | v1.2 | Replaced the broken level translator; unified drill sizes; thermal vias | [`bom_psvemmc_v12.csv`](psvemmc/bom_psvemmc_v12.csv) |
| `psvemmc-v20` | v2.1 | New layout; added I²C EEPROM and a GND pad | none |
| `psvemmc-v30` | v3.1 | USB port protection (TPD4S014), clock buffering, tented vias, fixed clock-buffer pin order | [`bom_psvemmc_v30.csv`](psvemmc/bom_psvemmc_v30.csv) |

v3.1 is the latest psvemmc design. Its BOM had a part-number fix in the last upstream commit, so use the BOM as it is in this repo.

### 3G slot breakout

This board plugs into the 3G slot and brings each signal out to a labeled test pad:

- USB D+/D−
- The SysCon, Kermit and EXT UARTs
- USIM lines
- Modem control lines: `PSHOLD`, `ONSWA`, `MODRST`, `RESOUT`, `NAND_EDL`, `VBUS_CTRL`, `AWR`, `CWR`
- GPIOs

It's for probing the slot. It isn't a finished product.

## Libraries

`lbr/` holds the custom Eagle libraries the designs use, including the Vita 3G-slot connector (`mini-pci-e.lbr`, device `VITA-CUSTOM`), USB2244, GL823, TXS02612, LP3996, TPD4S014 and the microSD sockets. [`lbr/SOURCES.txt`](lbr/SOURCES.txt) lists where the third-party libraries came from.

## Ordering boards

The repo doesn't include Gerbers, so you have to export them first:

1. Open the `.brd` file in Autodesk Eagle (8.2 or later) or Fusion Electronics. KiCad can also import Eagle files (File → Import → Non-KiCad Project).
2. Export Gerbers and drill files with your fab's CAM job, or with the default 2-layer job.
3. Order a 2-layer PCB. The boards plug into the Vita's slot, so check the fab's board-thickness options against the original modem card before you order.
4. Source parts from the matching BOM CSV. The PSVSD v2.0 BOM uses Seeed SKUs, so it can go straight into Seeed Fusion PCBA.

Most parts are 0603 or 0402 passives and QFN/WSON ICs, so you'll need a stencil and reflow (or a PCBA service).

## Credits and license

Designed by Yifan Lu. This repo is a copy of the original design history (68 commits, May 2017 – Aug 2018). The designs are licensed under the Creative Commons Attribution 4.0 International license; see [`LICENSE.txt`](LICENSE.txt). If you share or sell boards made from these files, credit Yifan Lu.
