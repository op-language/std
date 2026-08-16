# std

The Op standard library lib.

This lib supplies the CPU and the platform definitions for all supported
targets. The `opc` compiler loads this lib at build time from
`~/.carts/std/`. The `#[cfg()]` attribute selects the correct code for the
target triplet. The `pub use ... as cpu;` and `pub use ... as machine;`
aliases expose the selected CPU and machine under a stable name. A program
that does `use std::*;` can write `cpu::a`, `machine::PPU`, etc. directly.

## Structure

```
src/
  lib.op           Root: cfg-guarded mod decls + pub use aliases
  mos6502.op       MOS 6502 CPU family (populated)
  mos65sc02.op     MOS 65SC02 (stub)
  wdc65c816.op     WDC 65C816 (stub)
  m68000.op        Motorola 68000 (stub)
  z80.op           Zilog Z80 / Sharp LR35902 (stub)
  nes.op           NES module: mod constants; mod types; mod macros;
  nes/
    constants.op   NES constants (REFRESH_HZ, CR_*, ST_*, addresses)
    types.op       NES types (PPU, SPR, COLOUR enums, OAM_ENTRY, SCROLL)
    macros.op      NES inline macros (system init, vblank, VRAM, video)
  lynx.op          Lynx module: mod constants; mod macros; mod loader;
  lynx/
    constants.op   Lynx constants (cart I/O, Mikey registers, ROM funcs)
    macros.op      Lynx inline macros (set_cart_segment_address)
    loader.op      Lynx loader stubs (micro loader, secondary loader)
  apple_ii.op      Apple II (stub)
  apple_ii/
    constants.op   Apple II constants (stub)
  apple_iic/       Apple IIc (stub)
  apple_iie/       Apple IIe (stub)
  apple_iie_enhanced/ Apple IIe Enhanced (stub)
  atari_800/       Atari 800 (stub)
  atari_2600/      Atari 2600 (stub)
  atari_5200/      Atari 5200 (stub)
  atari_7800/      Atari 7800 (stub)
  commodore_64/    Commodore 64 (stub)
  pcengine/        NEC PC Engine (stub)
  neogeo_aes/      Neo Geo AES (stub)
  sega_genesis/    Sega Genesis (stub)
  apple_iigs/      Apple IIgs (stub)
  snes/            Super Nintendo Entertainment System (stub)
  gameboy/         Nintendo Game Boy (stub)
  gameboy_color/   Nintendo Game Boy Color (stub)
  gamegear/        Sega Game Gear (stub)
  mastersystem/    Sega Master System (stub)
  sg1000/          Sega SG-1000 (stub)
  zx80/            Sinclair ZX80 (stub)
  zx81/            Sinclair ZX81 (stub)
  spectrum/        Sinclair Spectrum (stub)
  ti_85/           Texas Instruments TI-85 (stub)
```

## Supported Targets

The lib has no default target. The compiler builds it for a specific triplet
when a ROM project includes it and builds with a target flag.

| Triplet | CPU | Platform |
|---------|-----|----------|
| `mos6502-apple-ii` | MOS 6502 | Apple II |
| `mos6502-apple-iic` | MOS 6502 | Apple IIc |
| `mos6502-apple-iie` | MOS 6502 | Apple IIe |
| `mos6502-apple-iie-enhanced` | MOS 6502 | Apple IIe Enhanced |
| `mos6502-atari-800-ntsc` | MOS 6502 | Atari 800 NTSC |
| `mos6502-atari-800-pal` | MOS 6502 | Atari 800 PAL |
| `mos6502-atari-2600` | MOS 6502 | Atari 2600 |
| `mos6502-atari-5200` | MOS 6502 | Atari 5200 |
| `mos6502-atari-7800` | MOS 6502 | Atari 7800 |
| `mos65sc02-atari-lynx` | MOS 65SC02 | Atari Lynx |
| `mos6502-commodore-64` | MOS 6502 | Commodore 64 |
| `mos6502-nec-pcengine` | MOS 6502 | NEC PC Engine |
| `mos6502-nintendo-nes-ntsc` | Ricoh 2A03 | NES NTSC |
| `mos6502-nintendo-nes-pal` | Ricoh 2A07 | NES PAL |
| `m68000-neogeo-aes` | Motorola 68000 | Neo Geo AES |
| `m68000-sega-genesis` | Motorola 68000 | Sega Genesis |
| `wdc65c816-apple-iigs` | WDC 65C816 | Apple IIgs |
| `wdc65c816-nintendo-snes` | WDC 65C816 | SNES |
| `z80-neogeo-aes` | Zilog Z80 | Neo Geo AES |
| `z80-nintendo-gameboy` | Sharp LR35902 | Game Boy |
| `z80-nintendo-gameboy-color` | Sharp LR35902 | Game Boy Color |
| `z80-sega-gamegear` | Zilog Z80 | Sega Game Gear |
| `z80-sega-genesis` | Zilog Z80 | Sega Genesis |
| `z80-sega-mastersystem` | Zilog Z80 | Sega Master System |
| `z80-sega-sg1000` | Zilog Z80 | Sega SG-1000 |
| `z80-sinclair-zx80` | Zilog Z80 | Sinclair ZX80 |
| `z80-sinclair-zx81` | Zilog Z80 | Sinclair ZX81 |
| `z80-sinclair-spectrum` | Zilog Z80 | Sinclair Spectrum |
| `z80-ti-85` | Zilog Z80 | Texas Instruments TI-85 |

## License

Apache-2.0