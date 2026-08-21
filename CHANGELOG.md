# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.4.0]

### Added
- `src/cpu/vl65nc02.op` CPU module for the VLSI VL65NC02 used in the
  Atari Lynx. The VL65NC02 is a 65SC02 core. The module includes
  `CLOCK_HZ` set to 4000000.
- `src/cpu/sm83.op` CPU module for the Sharp SM83 used in the Nintendo
  Game Boy and Game Boy Color. The module includes `CLOCK_HZ` set to
  4194304.
- `src/cpu/mos6502/macros.op` with the full HLAKit 6502 macro ports:
  assignment, bitwise, boolean, math, stack, memory, and jump macros.
- `src/cpu/mos6502/ram.op` with temp and shadow RAM variables for the
  6502 macros.
- `src/machine/nes/io.op` with joystick polling macros.
- `src/machine/nes/audio.op` with APU register definitions.
- `src/machine/nes/mappers.op` with the MMC5 mapper enum and bank
  switching macros.
- `src/machine/nes/memory.op` with VRAM memory copy and fill macros
  and functions.
- `src/machine/nes/palette.op` with palette set and write macros and
  functions.
- `src/machine/nes/ram.op` with shadow RAM variables for the NES
  machine module.
- `src/machine/gameboy/types.op` with joypad button, LCD status, and
  interrupt enums.
- `src/machine/gameboy/macros.op` with joypad, vblank, interrupt, and
  DMA macros.
- New tests `tests/vl65nc02-atari-lynx.op`,
  `tests/sm83-nintendo-gameboy.op`, and
  `tests/sm83-nintendo-gameboy-color.op`.

### Changed
- Restructured `src/cpu/mos6502.op` from a flat file to a directory
  with `mod.op`, `macros.op`, and `ram.op`.
- Populated `src/cpu/z80.op`, `src/cpu/wdc65c816.op`,
  `src/cpu/m68000.op`, and `src/cpu/mos65sc02.op` with full enum
  declarations for registers, conditions, opcodes, addressing modes,
  and interrupts.
- Expanded `src/machine/nes/constants.op` with the full set of PPU
  bitmasks, status flags, sprite constants, palette addresses, pattern
  table addresses, name table constants, joystick constants, and
  button masks.
- Expanded `src/machine/nes/types.op` with `JOYSTICK`, `APU`,
  `SNDENABLE`, `PALENT`, and `PALETTE` types. Merged `SPR_ADDRESS`,
  `SPR_IO`, and `SPR_DMA` into the `PPU` enum.
- Expanded `src/machine/nes/macros.op` with full PPU control register,
  scanline, VRAM addressing, VRAM data, and sprite macros.
- Expanded `src/machine/gameboy/constants.op` with the full hardware
  register map. Game Boy Color registers are gated on
  `#[cfg(variant = "color")]`.
- Updated `src/machine/nes.op` doc comment to reference
  `rp2A03-nintendo-nes-ntsc` and `rp2A07-nintendo-nes-pal`.
- Updated `src/machine/lynx.op` doc comment to reference
  `vl65nc02-atari-lynx`.
- Updated `src/machine/gameboy.op` doc comment to reference
  `sm83-nintendo-gameboy`.
- `src/cpu/rp2A03.op` and `src/cpu/rp2A07.op` now re-export the 6502
  macros and temp variables via `use std::cpu::mos6502::macros::*`.

### Removed
- `src/machine/lynx/loader.op` and the `mod loader;` declaration. The
  loader is application-specific and will be ported in a Lynx example
  game.
- `src/machine/gameboy_color.op` and the `src/machine/gameboy_color/`
  directory. The parser splits `sm83-nintendo-gameboy-color` into
  `machine = "gameboy"` and `variant = "color"`, so the
  `gameboy_color` module never matched.
- `tests/mos65sc02-atari-lynx.op`, `tests/z80-nintendo-gameboy.op`,
  and `tests/z80-nintendo-gameboy-color.op`. Replaced by the new
  `vl65nc02` and `sm83` test fixtures.
- Dead `#[cfg(machine = "gameboy-color")]` arms in `src/machine.op`.

## [0.3.0]

### Added
- `src/cpu/rp2A03.op` and `src/cpu/rp2A07.op` CPU family modules for the
  Ricoh RP2A03 and RP2A07. These are MOS 6502 cores without decimal mode.
  The `CLD` and `SED` opcodes and the `D` status flag are removed.
- `CLOCK_HZ` constant in the rp2A03 and rp2A07 modules. The value is
  1789773 for `ntsc` and 1662607 for `pal`.
- `#[cfg(cpu = "rp2A03")]` and `#[cfg(cpu = "rp2A07")]` arms in `cpu.op`.
- New tests `tests/rp2A03-nintendo-nes-ntsc.op`,
  `tests/rp2A07-nintendo-nes-pal.op`, and `tests/rp2A03-cpu-alias.op`.

### Changed
- The supported-targets table in `README.md` lists
  `rp2A03-nintendo-nes-ntsc` and `rp2A07-nintendo-nes-pal` for the NES.
  The `mos6502` CPU stays valid for the other 6502 targets.

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
