# std

The Op standard library bank.

This bank supplies the CPU and the platform definitions for all supported
targets. The `opc` compiler loads this bank at build time from
`~/.carts/std/`. The `#[cfg()]` attribute selects the correct code for the
target triplet.

## Structure

```
src/
  bank.op            Root file: mod cpu; mod machine;
  cpu.op             CPU module: cfg-guarded mod declarations per CPU
  machine.op         Machine module: cfg-guarded mod declarations per machine
  cpu/
    mos6502.op       MOS 6502 CPU family (populated)
    mos65sc02.op     MOS 65SC02 (stub)
    wdc65c816.op     WDC 65C816 (stub)
    m68000.op        Motorola 68000 (stub)
    z80.op           Zilog Z80 / Sharp LR35902 (stub)
  machine/
    nes.op           NES module: mod const; mod types; mod macros;
    nes/
      const.op       NES constants (REFRESH_HZ, CR_*, ST_*, addresses)
      types.op       NES types (PPU, SPR, COLOUR enums, OAM_ENTRY, SCROLL)
      macros.op      NES inline macros (system init, vblank, VRAM, video)
    lynx.op          Lynx module: mod const; mod macros; mod loader;
    lynx/
      const.op       Lynx constants (cart I/O, Mikey registers, ROM funcs)
      macros.op      Lynx inline macros (set_cart_segment_address)
      loader.op      Lynx loader stubs (micro loader, secondary loader)
    apple-ii.op      Apple II module (stub)
    apple-ii/
      const.op       Apple II constants (stub)
    apple-iic/       Apple IIc (stub)
    apple-iie/       Apple IIe (stub)
    apple-iie-enhanced/ Apple IIe Enhanced (stub)
    atari-800/       Atari 800 (stub)
    atari-2600/      Atari 2600 (stub)
    atari-5200/      Atari 5200 (stub)
    atari-7800/      Atari 7800 (stub)
    commodore-64/    Commodore 64 (stub)
    pcengine/        NEC PC Engine (stub)
    neogeo-aes/      Neo Geo AES (stub)
    sega-genesis/    Sega Genesis (stub)
    apple-iigs/      Apple IIgs (stub)
    snes/            Super Nintendo Entertainment System (stub)
    gameboy/         Nintendo Game Boy (stub)
    gameboy-color/   Nintendo Game Boy Color (stub)
    gamegear/        Sega Game Gear (stub)
    mastersystem/    Sega Master System (stub)
    sg1000/          Sega SG-1000 (stub)
    zx80/            Sinclair ZX80 (stub)
    zx81/            Sinclair ZX81 (stub)
    spectrum/        Sinclair Spectrum (stub)
    ti-85/           Texas Instruments TI-85 (stub)
```

## Supported Targets

The bank has no default target. The compiler builds it for a specific triplet
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