# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0]

### Added
- `pub use <cpu>::*;` re-exports inside `cpu.op` so `cpu::a` works
  directly. The selected CPU module is cfg-guarded and re-exported
  flat.
- `pub use <machine>::*;` re-exports inside `machine.op` so
  `machine::PPU` works directly. The selected machine module is
  cfg-guarded and re-exported flat.
- `pub use` re-exports inside each CPU and machine module so the
  namespace is flat (e.g. `cpu::a`, not `cpu::CPU_REG::a`).
- New tests `tests/cpu-alias.op` and `tests/machine-alias.op` that
  exercise the re-exports.

### Changed
- Restructured the lib: `lib.op` now declares `pub mod cpu; pub mod
  machine;`. The `cpu.op` and `machine.op` files hold the cfg-guarded
  `mod` declarations and the `pub use <cpu>::*` re-exports. CPU files
  live in `src/cpu/` and machine files live in `src/machine/`.
- Renamed all `const` submodules to `constants` (`const.op` to
  `constants.op`, `mod const;` to `mod constants;`).
- Renamed hyphenated machine files and mod identifiers to underscores
  (e.g. `apple-ii.op` to `apple_ii.op`, `mod apple_ii;`). The `cfg`
  value stays hyphenated (`machine = "apple-ii"`).

### Breaking
- The `const` submodule is now `constants`. Code that did
  `use std::machine::nes::const::*;` must rename to `constants`.
- Hyphenated machine module names are now underscore names.

## [0.1.0]

### Added
- Initial `std` lib scaffold.
- `src/lib.op` root file with `mod cpu; mod machine;` declarations.
- `src/cpu.op` with `#[cfg(cpu = "...")]`-guarded `mod` declarations for all
  supported CPU families.
- `src/machine.op` with `#[cfg(machine = "...")]`-guarded `mod` declarations
  for all supported machines.
- `src/cpu/mos6502.op` with 6502 status flags, registers, condition keywords,
  opcode mnemonic list, addressing modes, and interrupt vector names.
- `src/machine/nes.op` module file with `mod const; mod types; mod macros;`
  and `use` declarations.
- `src/machine/nes/const.op` with NES conditional constants, PPU control
  bitmasks, status flags, and palette and name table addresses.
- `src/machine/nes/types.op` with NES PPU, SPR, and COLOUR enums, OAM_ENTRY
  and SCROLL structs, and OAM_BUFFER type alias.
- `src/machine/nes/macros.op` with NES system inline macros ported from
  HLAKit and the nes-code.op example.
- `src/machine/lynx.op` module file with `mod const; mod macros; mod loader;`
  and `use` declarations.
- `src/machine/lynx/const.op` with Atari Lynx cart I/O registers, Mikey
  registers, and ROM function addresses. Ported from HLAKit.
- `src/machine/lynx/macros.op` with the `set_cart_segment_address` inline
  macro. Ported from HLAKit.
- `src/machine/lynx/loader.op` with Lynx micro loader and secondary loader
  stubs. Ported from HLAKit.
- Stub files for all remaining CPU families and machines.
