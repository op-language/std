# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0]

### Added
- `pub use <cpu> as cpu;` and `pub use <machine> as machine;` aliases at
  the lib root. `use std::*;` now exposes `cpu::a`, `machine::PPU`, etc.
  flat.
- `pub use` re-exports inside each CPU and machine module so the alias
  namespace is flat (e.g. `cpu::a`, not `cpu::CPU_REG::a`).
- New tests `tests/cpu-alias.op` and `tests/machine-alias.op` that
  exercise the aliases.

### Changed
- Flattened the lib structure: CPU and machine files now live at `src/`
  root. `mod mos6502;` in `lib.op` resolves to `src/mos6502.op`.
- Renamed all `const` submodules to `constants` (`const.op` to
  `constants.op`, `mod const;` to `mod constants;`).
- Renamed hyphenated machine files and mod identifiers to underscores
  (e.g. `apple-ii.op` to `apple_ii.op`, `mod apple_ii;`). The `cfg`
  value stays hyphenated (`machine = "apple-ii"`).
- Deleted `src/cpu.op`, `src/machine.op`, `src/cpu/`, and `src/machine/`.

### Breaking
- The module paths changed. Code that referenced `std::cpu::mos6502::a`
  or `std::machine::nes::PPU` must now use `std::cpu::a` or
  `std::machine::PPU` via the aliases.
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
