# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.9.0]

### Added
- The `w65c02` CPU module (`src/cpu/w65c02.op`): status flags, register,
  condition, opcode, addressing-mode, and interrupt enums for the WDC
  W65C02S, with the 8 MHz `CLOCK_HZ` constant. The opcode enum covers
  the 6502 base, the 65SC02 additions, the Rockwell
  `RMB`/`SMB`/`BBR`/`BBS` instructions, and `WAI`/`STP`.
- The Commander X16 machine module (`src/machine/x16/`): VERA, composer,
  layer, audio, YM2151, VIA, banking, and emulator-debug register
  constants and selector enums in `constants.op`; sprite, PSG, and
  controller types in `types.op`; and inline macros for VERA addressing,
  bank switching, interrupts, video on/off, VSYNC wait, PSG output, and
  `system_initialize` in `macros.op`.
- The `w65c02` and `x16` modules in the cfg registries (`src/cpu.op`,
  `src/machine.op`), and the `machine/x16` module root file is `mod.op`
  for `mod` resolution.
- The `tests/w65c02-commander-x16.op` triplet test and the `w65c02`
  case in `tests/cpu-alias.op`.
- NES font subset loading (`src/font/nes.op`):
  - `font_load_string(font, str, len)` loads only the glyphs used by a
    string into CHR-RAM, one tile slot per character, starting at
    `font_first_free_tile + 1`. Tile slot 0 stays blank so `cls()`
    renders an empty background. It records each character's slot in
    the new remap table and advances `font_first_free_tile`.
  - `font_init_remap()` fills the remap table with 0xFF (unset). Call
    it after `font_init`.
  - `_tile_remap: [u8; 128]` maps character codes 0x00-0x7F to the
    absolute CHR tile slot holding the current font's glyph.
- The per-character loops of `draw_text` and `font_load_string` moved
  into single-copy non-inline fns (`_dt_loop`, `_fls_loop`). Inline fn
  labels are section-global, so two inlined copies of a labeled loop
  collide. This makes both functions safe to call several times.

### Changed
- `draw_text` and `putchar` now resolve the tile index through
  `_tile_remap` when the entry is set and the char is below 0x80.
  Unset entries (0xFF) and char codes 0x80 and above keep the legacy
  `char + first_tile` behaviour.
- `font_load` now fills `_tile_remap` with `first + enc[c]` for
  c = 0..127 after loading, so full loads of non-identity fonts draw
  correctly.
- `font_load` reserves CHR tile slot 0: glyphs start at
  `font_first_free_tile + 1` and the free-tile advance adds the
  reserved slot.

## [0.7.0]

### Added
- Game Boy font subset loading (`src/font/gameboy.op`):
  - `font_load_string(font, str, len)` loads only the glyphs used by a
    string into VRAM, one tile slot per character, starting at
    `font_first_free_tile + 1`. Tile slot 0 stays blank so `cls()`
    renders an empty background. It records each character's slot in
    the new remap table and advances `font_first_free_tile`.
  - `font_init_remap()` fills the remap table with 0xFF (unset). Call
    it after `font_init`.
  - `_tile_remap: [u8; 128]` maps character codes 0x00-0x7F to the
    absolute VRAM tile slot holding the current font's glyph.
- The per-character loops of `draw_text` and `font_load_string` moved
  into single-copy non-inline fns (`_dt_loop`, `_fls_loop`). Inline fn
  labels are section-global, so two inlined copies of a labeled loop
  collide. This makes both functions safe to call several times.

### Fixed
- `_font_load_tiles` now honours the `first` parameter. VRAM writes
  start at `0x8000 + first*16` instead of always 0x8000, so a second
  font no longer overwrites the first.
- `draw_text` and `putchar` now resolve the tile index through
  `_tile_remap` when the entry is set. Previously the index was
  `char + first_tile`, which ignored the font's encoding table and
  only rendered correctly for identity-encoded fonts (NES, IBM fixed).
  Unset entries (0xFF) and char codes 0x80 and above keep the legacy
  `char + first_tile` behaviour.
- `font_load` now fills `_tile_remap` with `first + enc[c]` for
  c = 0..127 after loading, so full loads of non-identity fonts draw
  correctly.
- `font_load` advances `font_first_free_tile` by the true tile count.
  The count was read with `ld_a (font.tile_count)`, which loads from
  memory address `font.tile_count` instead of the const value; it is
  now loaded with `ld #font.tile_count`.

### Notes
- `src/font/gameboy.op` avoids `if ... else` and `while`. opc 0.10.0
  emits a 6502-style JMP (0x4C) for those constructs on SM83 instead
  of JP (0xC3). The encoding-mode branches use `if` without an else
  block plus explicit `jp` labels.

## [0.6.0]

### Added
- Font loading and text display API (`src/font/`):
  - `font_t` and `font_handle_t` structs, `FONT_ENCODING`,
    `FONT_SIZE`, `FONT_STYLE`, and `FONT_SPACING` enums in
    `src/font/types.op`.
  - `font_init`, `set_text_color` shared API in `src/font/api.op`.
  - Game Boy font functions in `src/font/gameboy.op`: `font_load`,
    `font_register`, `font_set`, `draw_text`, `putchar`, `gotoxy`,
    `cls`, and the internal `_font_load_tiles` 1bpp-to-2bpp tile
    expander.
  - NES font functions in `src/font/nes.op`: `font_load`,
    `font_register`, `font_set`, `draw_text`, `putchar`, `cls`.
  - Font RAM variables in `src/font/ram.op`: `font_first_free_tile`,
    `_font_current`, `_cursor_x`, `_cursor_y`, `_current_fg`,
    `_current_bg`.
  - Bundled font data arrays in `src/font/data/`: NES, IBM, IBM fixed,
    italic, min, and spect fonts. Each font has target-specific arrays
    gated by `#[cfg]` (e.g. `NES_SMALL_NORMAL_GAMEBOY` for DMG,
    `NES_SMALL_NORMAL_GAMEBOY_COLOR` for CGB) and a `FONT_*` struct
    constant.
- `src/panic.op` crash handler with `panic` and `assert` macros.
- `docs/FONT_REFERENCE.md` documenting the binary font blob format.
- `debug` feature in `Cart.toml`.

### Changed
- `src/font.op` re-exports the platform-specific font module
  (`gameboy` or `nes`) and the shared `api`, `types`, and `data`
  modules.
- `src/machine/gameboy/macros.op`: `system_initialize` now zeros
  `LCD::BG_PALETTE`. Added `cgb_set_bg_palette` and
  `cgb_set_sprite_palette` macros (gated on `#[cfg(variant = "color")]`).

## [0.5.0]

### Added
- `src/machine/gameboy/ram.op` with the `_joypad_raw` shadow variable
  used by the `read_joypad` macro.
- VRAM and memory constants in `src/machine/gameboy/constants.op`:
  `TILE_MAP_0_ADDRESS`, `TILE_MAP_1_ADDRESS`, `TILE_PATTERN_0_ADDRESS`,
  `TILE_PATTERN_1_ADDRESS`, `OAM_ADDRESS`, `SCREEN_WIDTH`,
  `SCREEN_HEIGHT`, `TILE_MAP_WIDTH`, `TILE_MAP_HEIGHT`.
- LCD control bitmasks in `src/machine/gameboy/constants.op`:
  `LCD_DISPLAY_ENABLE`, `LCD_WINDOW_MAP_9C00`, `LCD_WINDOW_ENABLE`,
  `LCD_TILE_DATA_8800`, `LCD_BG_MAP_9C00`, `LCD_SPRITE_8x16`,
  `LCD_SPRITE_ENABLE`, `LCD_BG_ENABLE`.
- CGB palette registers in `src/machine/gameboy/constants.op` gated on
  `#[cfg(variant = "color")]`: `BCPS` (0xFF68), `BCPD` (0xFF69),
  `OCPS` (0xFF6A), `OCPD` (0xFF6B).
- `DMG_SHADE` enum in `src/machine/gameboy/types.op` with the four
  monochrome shades: `WHITE`, `LIGHT_GRAY`, `DARK_GRAY`, `BLACK`.
- VRAM and system macros in `src/machine/gameboy/macros.op`:
  `system_initialize`, `turn_video_on`, `turn_video_off`,
  `vram_set_address_hl`, `vram_write_a`, `vram_write`,
  `vram_clear_address`, `assign`, `assign_16i`.
- CGB palette macros in `src/machine/gameboy/macros.op` gated on
  `#[cfg(variant = "color")]`: `cgb_set_bg_palette`,
  `cgb_set_sprite_palette`. Updated to use `CGB_PALETTE::BCPS`,
  `CGB_PALETTE::BCPD`, `CGB_PALETTE::OCPS`, `CGB_PALETTE::OCPD`.

### Changed
- Rewrote `src/machine/gameboy/macros.op` to use SM83 mnemonics. The
  `read_joypad`, `vblank_wait`, and `dma_copy` macros now use `ld`,
  `ldh`, `and` instead of the 6502 `lda`, `sta`, `and`.
- Fixed `LCD_STATUS` enum in `src/machine/gameboy/types.op`. Set
  `HBLANK` to 0 and `VBLANK` to 1. The old values had both set to 0.
- The `vblank_wait` macro now waits for STAT mode 1 (vblank) by
  polling `LCD::STATUS` and testing `and #3` for zero (mode 0 is
  hblank, not vblank).
- Changed CGB palette registers from `#[addr(...)]` variables to
  `enum CGB_PALETTE` constants in `src/machine/gameboy/constants.op`.
  The `#[addr]` variables were unresolved symbols at link time because
  they occupy hardware register space (0xFF68-0xFF6B), not RAM. As
  enum constants, they resolve to their address values like `LCD::CONTROL`.

### Changed

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
