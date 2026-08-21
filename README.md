# std

The Op standard library lib.

This lib supplies the CPU and the platform definitions for all supported
targets. The `opc` compiler loads this lib at build time from
`~/.carts/std/`. The `#[cfg()]` attribute selects the correct code for the
target triplet. The `cpu` module re-exports the selected CPU module so
`cpu::a` works directly. The `machine` module re-exports the selected
machine module so `machine::PPU` works directly.

## Structure

```
src/
  lib.op           Root: pub mod cpu; pub mod machine;
  cpu.op           CPU module: cfg-guarded mod decls + pub use re-exports
  cpu/
    mos6502/       MOS 6502 CPU family (mod.op, macros.op, ram.op)
    mos65sc02.op   MOS 65SC02 (populated)
    wdc65c816.op   WDC 65C816 (populated)
    m68000.op      Motorola 68000 (populated)
    z80.op         Zilog Z80 (populated)
    rp2A03.op      Ricoh RP2A03 (NES NTSC)
    rp2A07.op      Ricoh RP2A07 (NES PAL)
    vl65nc02.op    VLSI VL65NC02 (Atari Lynx)
    sm83.op        Sharp SM83 (Game Boy / Game Boy Color)
  machine.op       Machine module: cfg-guarded mod decls + pub use re-exports
  machine/
    nes.op         NES module: mod constants; mod types; mod macros; mod io; mod audio; mod mappers; mod memory; mod palette; mod ram;
    nes/
      constants.op NES constants (REFRESH_HZ, CR_*, ST_*, addresses, buttons)
      types.op     NES types (PPU, JOYSTICK, APU, SNDENABLE, COLOUR, OAM_ENTRY, PALETTE)
      macros.op    NES inline macros (system init, vblank, VRAM, video, sprites)
      io.op        NES joystick macros (read, test, poll)
      audio.op     NES APU register definitions
      mappers.op   NES MMC5 mapper enum and bank switching macros
      memory.op    NES VRAM memory copy and fill macros and functions
      palette.op   NES palette set and write macros and functions
      ram.op       NES shadow RAM variables (_ppu_ctl0, _joypad, etc.)
    lynx.op        Lynx module: mod constants; mod macros;
    lynx/
      constants.op Lynx constants (cart I/O, Mikey registers, ROM funcs)
      macros.op    Lynx inline macros (set_cart_segment_address)
    apple_ii.op    Apple II (stub)
    apple_ii/
      constants.op  Apple II constants (stub)
    apple_iic/      Apple IIc (stub)
    apple_iie/      Apple IIe (stub)
    apple_iie_enhanced/ Apple IIe Enhanced (stub)
    atari_800/      Atari 800 (stub)
    atari_2600/     Atari 2600 (stub)
    atari_5200/     Atari 5200 (stub)
    atari_7800/     Atari 7800 (stub)
    commodore_64/   Commodore 64 (stub)
    pcengine/       NEC PC Engine (stub)
    neogeo_aes/     Neo Geo AES (stub)
    sega_genesis/   Sega Genesis (stub)
    apple_iigs/     Apple IIgs (stub)
    snes/           Super Nintendo Entertainment System (stub)
    gameboy/        Nintendo Game Boy (populated)
    gamegear/       Sega Game Gear (stub)
    mastersystem/   Sega Master System (stub)
    sg1000/         Sega SG-1000 (stub)
    zx80/           Sinclair ZX80 (stub)
    zx81/           Sinclair ZX81 (stub)
    spectrum/       Sinclair Spectrum (stub)
    ti_85/          Texas Instruments TI-85 (stub)
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
| `vl65nc02-atari-lynx` | VLSI VL65NC02 | Atari Lynx |
| `mos6502-commodore-64` | MOS 6502 | Commodore 64 |
| `mos6502-nec-pcengine` | MOS 6502 | NEC PC Engine |
| `rp2A03-nintendo-nes-ntsc` | Ricoh RP2A03 | NES NTSC |
| `rp2A07-nintendo-nes-pal` | Ricoh RP2A07 | NES PAL |
| `m68000-neogeo-aes` | Motorola 68000 | Neo Geo AES |
| `m68000-sega-genesis` | Motorola 68000 | Sega Genesis |
| `wdc65c816-apple-iigs` | WDC 65C816 | Apple IIgs |
| `wdc65c816-nintendo-snes` | WDC 65C816 | SNES |
| `z80-neogeo-aes` | Zilog Z80 | Neo Geo AES |
| `sm83-nintendo-gameboy` | Sharp SM83 | Game Boy |
| `sm83-nintendo-gameboy-color` | Sharp SM83 | Game Boy Color |
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