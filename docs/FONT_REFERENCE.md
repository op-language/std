# Font Format Reference

This document describes the font system used by the Op std library.

## Binary Layout

A font blob is a flat byte array with this structure:

1.  **Byte 0**: Flags byte. The lower 2 bits set the encoding mode. Bit 2
    (value 4) sets the compressed flag.

2.  **Byte 1**: Tile count. The number of 8x8 tiles in the font.

3.  **Encoding table**: 256, 128, or 0 bytes. The size depends on the
    encoding mode. Each byte maps a character code to a tile index.

4.  **Tile data**: `tile_count * 8` bytes. Each tile is 8 bytes. Each byte
    is one row of 8 pixels. The most significant bit is the leftmost pixel.
    A set bit is the foreground color. A clear bit is the background color.

## Encoding Modes

The flags byte lower 2 bits set the encoding mode:

-   **0 (FONT_256ENCODING)**: 256-byte encoding table. Maps char codes
    0x00 through 0xFF to tile indices.

-   **1 (FONT_128ENCODING)**: 128-byte encoding table. Maps char codes
    0x00 through 0x7F to tile indices.

-   **2 (FONT_NOENCODING)**: No encoding table. The char code is the tile
    index directly.

## Compressed Flag

Bit 2 of the flags byte (value 4) sets the compressed flag. When set,
the tile data is 1bpp (1 bit per pixel). The loader expands the 1bpp data
to 2bpp at load time using the foreground and background colors. When
clear, the tile data is already in the native 2bpp format.

All five bundled fonts use the compressed flag (1bpp data).

## 1bpp to 2bpp Expansion

For each row byte of 1bpp data:

1.  Read the byte into the accumulator.
2.  For each of the 8 bits (MSB first):
    -   When the bit is 1, use the foreground color.
    -   When the bit is 0, use the background color.
3.  Shift the color value into two bit planes (plane 0 and plane 1).

The Game Boy writes the two planes as interleaved bytes: plane 0 row 0,
plane 1 row 0, plane 0 row 1, plane 1 row 1, and so on. The NES writes
the two planes as separate blocks: all 8 bytes of plane 0, then all 8
bytes of plane 1.

## Font Allocation

The font system uses a table of 6 font handle entries. Each entry is 3
bytes: 1 byte for the first tile index and 2 bytes for the font data
pointer.

The `font_load` function finds a free slot in the table. A free slot has
a null font pointer. The function stores the font data pointer and the
first free tile index. It advances the first free tile index by the tile
count.

## Per-Font Details

### font_ibm (f_ibm_sh.s)

-   **Symbol**: `_font_ibm`
-   **Flags**: 0x05 (128-encoding, compressed)
-   **Tiles**: 102
-   **Total size**: 946 bytes (2 + 128 + 816)
-   **Coverage**: ASCII 0x20 through 0x7E map to tiles 1 through 95. Six
    extra glyphs at tiles 96 through 101 for char codes 0x0E, 0x0F,
    0x1C, 0x1D, 0x1E, 0x1F. Control chars 0x00 through 0x1F map to tile 0
    (space) except for the six extra glyphs.
-   **Style**: Normal IBM font.

### font_ibm_fixed (f_ibm_full.s)

-   **Symbol**: `_font_ibm_fixed`
-   **Flags**: 0x04 (256-encoding, compressed)
-   **Tiles**: 255
-   **Total size**: 2298 bytes (2 + 256 + 2040)
-   **Coverage**: Full 0x00 through 0xFF. Char 0x00 maps to tile 0x00.
    Chars 0x01 through 0xFF map to tiles 0x01 through 0xFF.
-   **Style**: Backwards compatible full IBM font.

### font_italic (f_italic.s)

-   **Symbol**: `_font_italic`
-   **Flags**: 0x05 (128-encoding, compressed)
-   **Tiles**: 93
-   **Total size**: 874 bytes (2 + 128 + 744)
-   **Coverage**: Printable ASCII 0x21 through 0x7D map to tiles 1
    through 92. Char 0x7F and control chars map to tile 0 (space).
-   **Style**: Italic (slanted glyphs).

### font_min (f_min.s)

-   **Symbol**: `_font_min`
-   **Flags**: 0x05 (128-encoding, compressed)
-   **Tiles**: 37
-   **Total size**: 426 bytes (2 + 128 + 296)
-   **Coverage**: Digits 0 through 9 map to tiles 1 through 10. Uppercase
    A through Z map to tiles 11 through 36. Lowercase letters reuse the
    uppercase tiles. No punctuation. All other chars map to tile 0
    (space).
-   **Style**: Minimal (digits and uppercase only).

### font_spect (f_spect.s)

-   **Symbol**: `_font_spect`
-   **Flags**: 0x05 (128-encoding, compressed)
-   **Tiles**: 96
-   **Total size**: 898 bytes (2 + 128 + 768)
-   **Coverage**: ASCII 0x20 through 0x7F map to tiles 0 through 95. Control
    chars 0x00 through 0x1F map to tile 0 (space).
-   **Style**: Spectrum style font.

## Extracted Binary Files

The raw font bytes are in `std/src/font/data/`:

| File | Source | Size |
|------|--------|------|
| `ibm.bin` | `f_ibm_sh.s` | 946 bytes |
| `ibm_fixed.bin` | `f_ibm_full.s` | 2298 bytes |
| `italic.bin` | `f_italic.s` | 874 bytes |
| `min.bin` | `f_min.s` | 426 bytes |
| `spect.bin` | `f_spect.s` | 898 bytes |

Each `.bin` file contains the complete font blob: the 2-byte header, the
encoding table, and the 1bpp tile data. The bytes match the source `.s`
files exactly.
