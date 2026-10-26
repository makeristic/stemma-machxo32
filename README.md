# STEMMA QT MachXO2 breakout

A KiCad design for a Lattice MachXO2 FPGA breakout in a 32-pin QFN package, with two STEMMA QT I²C connectors, 3.3 V regulation, I²C level shifting, GPIO headers, and a DebugEdge programming connector.

This repository is a fork of [chickadee-tech/stemma-machxo32](https://github.com/chickadee-tech/stemma-machxo32). The original design is by Scott Shawcroft, with DebugEdge changes by Mark Olsson. Michael Cutler's fork adds suggested component part numbers to the schematic.

## Status

This is a historical hardware design from September–October 2020. The repository includes a schematic and routed two-layer PCB, but no assembled-board photos, test reports, fabrication exports, FPGA bitstream, or programming instructions. These files do not establish that the design has been built or electrically validated.

The [source audit](docs/code-audit-report.md) corrected the suggested FPGA part to **`LCMXO2-1200HC-4SG32I`**, matching the 32-pin QFN package, pin functions, and 3.3 V supply intent documented by [Lattice](https://www.latticesemi.com/view_document?document_id=38834). It also repaired DebugEdge schematic connections to agree with the existing PCB. Remaining footprint, clearance, thermal-pad, and licence questions still require review before manufacture or reuse.

## Files

* [KiCad project](stemma-mach32.pro), [legacy schematic](stemma-mach32.sch), [PCB layout](stemma-mach32.kicad_pcb), and [netlist](stemma-mach32.net). The filenames use `mach32`, while the repository name uses `machxo32`.
* [Symbol library table](sym-lib-table) and [footprint library table](fp-lib-table).
* [.gitmodules](.gitmodules), which describes the external `kicad-libs` and `debug-edge` libraries pinned as Git submodules.
* [Versioned historical symbol cache](stemma-mach32-cache.lib), preserving the symbols used for source checks.

Suggested component part numbers are embedded in the schematic fields; there is no standalone BOM CSV.

## Open or modify the design

Clone the fork with its pinned libraries. Public HTTPS submodule URLs avoid requiring SSH setup:

```sh
git clone --recurse-submodules https://github.com/makeristic/stemma-machxo32.git
cd stemma-machxo32
```

For an existing checkout, run `git submodule sync` followed by `git submodule update --init --recursive`.

Open `stemma-mach32.pro` in KiCad. The PCB records **KiCad 5.1.6**, with legacy `.pro` and `.sch` formats; import into a copy when using a newer version. The project tables use `${KIPRJMOD}` for the submodules and `${KISYSMOD}` for the standard KiCad footprint library, so install the corresponding KiCad libraries and check library paths before editing.

The submodules supply:

* [Chickadee Tech KiCad libraries](https://github.com/chickadee-tech/kicad-libs): the `lattice` symbols and `chickadee` footprints.
* [DebugEdge](https://github.com/debug-edge/DebugEdge): the `Connector_DebugEdge` symbols and footprints.

Review the schematic, FPGA part selection, power supplies, programming interface, and design rules before generating fabrication files. No HDL project or host I²C software is included.

## Feedback and related projects

This fork does not have Issues enabled. Submit [pull requests to this fork](https://github.com/makeristic/stemma-machxo32/pulls) for documentation or suggested-part corrections, including the commit, KiCad version, and component references. Changes intended for the original design should identify the upstream project and its authors.

Related Makeristic projects: [STM32WB55 Feather](https://github.com/makeristic/Feather_STM32WB55), [STUSB4500 USB-PD breakout](https://github.com/makeristic/STUSB4500_Breakout), and [KiCad library](https://github.com/makeristic/Makeristic-KiCad-Library).

## Licence and attribution

No licence for the top-level hardware design was found in this fork or in the upstream repository metadata during this review. It has therefore not been relicensed as MIT. Obtain the upstream authors' permission or a clear licence grant before relying on redistribution or reuse rights.

The pinned submodules carry their own terms: [Chickadee Tech's library is CC BY 4.0](https://github.com/chickadee-tech/kicad-libs/blob/114e708df9f8237ff84cfc5284e7f81555c1f4d6/LICENSE), and [DebugEdge uses Solderpad Hardware License 2.1](https://github.com/debug-edge/DebugEdge/blob/3c2c808082ba6d3af2069bf030e18878714d6500/LICENSE). The Chickadee library also excludes use of Chickadee Tech trademarks in its README. These terms are separate from this fork's design; third-party symbols, footprints, and marks are not granted new permissions by this README.

Standard cached symbols retain [KiCad's CC BY-SA 4.0 licence and exception](LICENSES/KiCad-Libraries.md); custom cached symbols retain the pinned submodules' terms. See the [audit](docs/code-audit-report.md) for exact source versions.
