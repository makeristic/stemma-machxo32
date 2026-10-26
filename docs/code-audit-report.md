# Source and hardware audit

Reviewed 2026-10-26 with KiCad nightly 10.99.0. Scope: component/package identity, external-library reproducibility, schematic/netlist/PCB connectivity, and physical-rule checks. No board, FPGA-programming, or production validation is claimed.

## Fixed: incompatible FPGA part number

The schematic's five U2 units suggested `LCMXO2-1200HC-4MG132I`, a 132-ball csBGA device, while U2's PCB footprint and symbol are 32-pin QFN. Corrected all five source fields and the stored netlist field to **`LCMXO2-1200HC-4SG32I`**.

[Lattice's current family datasheet](https://www.latticesemi.com/view_document?document_id=38834), ordering information, lists this exact 1280-LUT, HC, speed-4, industrial-grade part in 32-pin QFN. HC retains the existing 3.3 V core-supply intent; a ZE part would not be an equivalent supply choice. The [manufacturer's 32-QFN migration/pinout file](https://www.latticesemi.com/view_document?document_id=51687) matches the pinned symbol's 32 package-pin functions, including VCC 2/18, GND 3/22, I²C 27/28, JTAG 1/29/30/32, and the bank supplies. The 5 × 5 mm body and 0.5 mm pitch match the committed footprint family. This corrects package identity, not every aspect of assembly or FPGA configuration.

## Fixed: DebugEdge schematic disagreed with the PCB

The pinned `Connector_DebugEdge.lib` symbol has GND pin 1 at the top, Vref pin 5 at the bottom, TDO pin 6 on the upper right, and TDI pin 7 on the middle right. The schematic's old power symbols/labels targeted different positions. A fresh export therefore swapped GND/+3V3, assigned TDI to pin 6, left pin 7 floating, and left TDO disconnected, despite the historical netlist and PCB having the intended mapping.

Moved the power symbols, TDO/TDI labels and unused-pin flag to the pinned symbol's actual pin positions (`stemma-mach32.sch`, DebugEdge section). Fresh schematic export now agrees with the existing PCB: J5.1 GND, J5.5 +3V3, J5.6 TDO, J5.7 TDI; pin 8 remains intentionally unused. No copper connectivity changed. This prevents a future PCB update from propagating incorrect schematic connections.

## Reproducibility and provenance

- Versioned a complete legacy symbol cache using [KiCad symbols tag 5.1.7](https://github.com/KiCad/kicad-symbols/tree/5.1.7), commit `bf475af94877e8fd9cf80e667578ff61835e02bb`, plus the exact custom symbols from the pinned submodules. KiCad community material retains [its licence and exception](../LICENSES/KiCad-Libraries.md).
- Preserved the submodule pins: Chickadee library `114e708df9f8237ff84cfc5284e7f81555c1f4d6` (CC BY 4.0), DebugEdge `3c2c808082ba6d3af2069bf030e18878714d6500` (Solderpad 2.1). Only the public clone transport changed from SSH to HTTPS; recursive submodule checkout succeeds without SSH setup.
- Updated PCB FB1's displayed value to the schematic's `30Ω@100MHz`, preserving its footprint and net assignments.
- Preserved Scott Shawcroft/Mark Olsson's ownership. No clear top-level design licence was found, so no MIT grant was imposed on their design. The caches and submodules retain their respective notices; they do not supply a new licence for the complete board.

## Verification and remaining work

Fresh schematic export succeeds. Its complete pin-connectivity graph equals the stored netlist across **115 pins**, ignoring only generated net-name spelling differences. All 28 schematic components now match PCB values/footprints; PCB pad nets agree with the netlist. Board-only logo graphics are excluded. The FPGA part fields contain no remaining MG132 suggestion.

With isolated historical-symbol and installed modern-footprint tables, ERC reports **119 findings**: 100 endpoints on a finer grid than the modern default, 16 historical footprint names missing from the modern standard libraries, and 3 supply-drive declarations. The DebugEdge disconnected-label/pin and wrong-ground warnings are gone. Installing the corresponding historical footprints avoids substituting newer land patterns blindly; embedded PCB geometry was preserved.

Refilled DRC reports **62 findings**, **0 shorts**, and **0 unconnected items**: 28 copper/edge clearances against the 0.5 mm default, 16 historical footprint lookup failures, 11 differences against modern footprints, 1 courtyard overlap, and 6 silkscreen findings. Modern rules and best-effort legacy zone conversion require interpretation against the intended fabrication process; the counts are not a historical manufacturing verdict.

The central U2 pad 33 is unassigned in the PCB and absent from the original 32-pin symbol. Lattice recommends soldering the central thermal pad to top-layer ground for thermal performance. Review the package-specific land pattern, exposed-pad treatment, thermal path, and assembly process before manufacturing; this audit did not invent an additional schematic pin or modify upstream libraries.

Representative commands, run on temporary copies with caches and submodules present:

```sh
git submodule update --init --recursive
kicad-cli-nightly sch export netlist --output /tmp/machxo.net stemma-mach32.sch
kicad-cli-nightly sch erc --format json --severity-all --exit-code-violations --output /tmp/machxo-erc.json stemma-mach32.sch
kicad-cli-nightly pcb drc --format json --severity-all --refill-zones --exit-code-violations --output /tmp/machxo-drc.json stemma-mach32.kicad_pcb
```

ERC/DRC return exit 5 because findings remain. No rules or errors were suppressed; audit-only library tables used temporary `XDG_CONFIG_HOME`. No shared settings, submodule commits, routing, or fabricated output were changed. No Gerbers, bitstream, HDL project, complete BOM, or test report is supplied. Clear the licence and remaining engineering questions before treating this as a reusable manufacturing release.
