# Repository discoverability

## Acceptance criteria

The README identifies the MachXO2 design, its upstream authors and Michael's part-number additions, links actual sources, explains pinned-library setup, and states missing deliverables. GitHub has an accurate description and relevant topics. No licence is imposed on the unlicensed upstream design. Hardware files and submodule pointers remain unchanged.

## Checklist

- [x] Inspect sources, upstream history, project tables, and licences in pinned submodules (completed: 2026-10-26).
- [x] Add a README covering provenance, setup, file layout, status, feedback, and licence limitations (completed: 2026-10-26).
- [x] Set and verify GitHub description and topics (completed: 2026-10-26).
- [x] Check local Markdown links, initialise pinned submodules over HTTPS, review whitespace and the complete diff (completed: 2026-10-26; 7 local links resolve, design sources and submodule pointers unchanged).
- [x] Commit, push, and open [PR #1 against the fork](https://github.com/makeristic/stemma-machxo32/pull/1) (completed: 2026-10-26).

## Findings and limits

The top-level design has no clear licence. Its upstream authors are Scott Shawcroft and Mark Olsson; Michael adds suggested component part numbers. The pinned Chickadee library is CC BY 4.0, while DebugEdge uses Solderpad Hardware License 2.1. Both were checked out successfully using a command-scoped HTTPS rewrite, without modifying `.gitmodules`. KiCad 5.1.6 is recorded in the PCB. No Gerbers, firmware, bitstream, BOM CSV, assembled photos, or test reports are included. Issues are disabled on this fork, so feedback guidance uses pull requests. No electrical or CAD validation is claimed.

The schematic suggests `LCMXO2-1200HC-4MG132I`, which Lattice's datasheet identifies as 132-lead csBGA, while the schematic and PCB assign a 32-pin QFN footprint. The README flags this mismatch explicitly; resolving part selection is outside this documentation task.
