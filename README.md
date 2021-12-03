# BBC Micro Repton 3 game file format

## Introduction

Repton was a popular Boulderdash clone on the BBC Microcomputer in the mid 1980s.

This document describes the format of the Repton 3 game file.

It may contain omissions or errors.

Original version 2003.
Revised 2021.

## Conventions

Throughout this document hexadecimal numbers are prefixed by a **$** sign.

16-bit quantities are stored little-endian.

## Observations

The files appear to be of a fixed 9760 bytes long.

They each contain 8 level maps and a set of 48 sprites.

Additionally, each level has a game password; an editor password; a time limit and an individual colour scheme.

## File contents

### Offsets $00…$3F Password data for the individual levels

The passwords for the individual levels are encrypted and stored at the following locations.

The passwords themselves are ASCII strings up to seven characters in length and terminated by a carriage return.

The passwords are encrypted with an exclusive OR scheme.

The passwords occupy the first 64 bytes of any game file.

To decrypt a byte location simply XOR it with $3F less its location.

For example, to decrypt the byte at location $18, in memory XOR it with $27 (=$3F-$18).

| Offset      | Contents                       |
|-------------|--------------------------------|
| **$00…$07** | Encrypted password for map ‘A' |
| **$08…$0F** | Encrypted password for map ‘B' |
| **$10…$17** | Encrypted password for map ‘C' |
| **$18…$1F** | Encrypted password for map ‘D' |
| **$20…$27** | Encrypted password for map ‘E' |
| **$28…$2F** | Encrypted password for map ‘F' |
| **$30…$37** | Encrypted password for map ‘G' |
| **$38…$3F** | Encrypted password for map ‘H' |

### Offsets $40…$4F Time limits for the individual levels

Each individual level has a time limit.
They are to be found at the given locations as 16-bit words.

| Offset      | Contents               |
|-------------|------------------------|
| **$40…$41** | Time limit for map ‘A' |
| **$42…$43** | Time limit for map ‘B' |
| **$44…$45** | Time limit for map ‘C' |
| **$46…$47** | Time limit for map ‘D' |
| **$48…$49** | Time limit for map ‘E' |
| **$4A…$4B** | Time limit for map ‘F' |
| **$4C…$4D** | Time limit for map ‘G' |
| **$4E…$4F** | Time limit for map ‘H' |

**N.B.** The default time limit in an unedited game file for any level is 9999 units.

### Offsets $50…$5F Editor codes for the individual levels

Each level has a special 16-bit ‘Editor code', a password of sorts that had to be given
to the original game editor before you would have been allowed to manipulate it.

| Offset      | Contents                |
|-------------|-------------------------|
| **$50…$51** | Editor code for map ‘A' |
| **$52…$53** | Editor code for map ‘B' |
| **$54…$55** | Editor code for map ‘C' |
| **$56…$57** | Editor code for map ‘D' |
| **$58…$59** | Editor code for map ‘E' |
| **$5A…$5B** | Editor code for map ‘F' |
| **$5C…$5D** | Editor code for map ‘G' |
| **$5E…$5F** | Editor code for map ‘H' |

**N.B.** The default editor code in an unedited game file for any level is zero.

### Offsets $60…$DF Teleport information for the individual levels

First a short note on grid like nature of the levels.

They have 28 columns indexed from 0 to 27 and 24 rows indexed from 0 to 23.
The point (0, 0) is the top left-hand corner and the point (27, 23) is the bottom right-hand corner.

Each level can have up to four teleport sites.

Each map has 16 bytes describing its teleports.
Each teleport is described by 4 bytes.
The teleport information is stored in succession.

| Offset  | Contents                  |
|---------|---------------------------|
| **$00** | Teleport x co-ordinate    |
| **$01** | Teleport y co-ordinate    |
| **$02** | Destination x co-ordinate |
| **$03** | Destination y co-ordinate |

**N.B.** For the teleport to be a valid one, _all_ co-ordinate values must lie within
the specified range of co-ordinates for the map, i.e. (0-27, 0-23).

The locations of these data are shown below.

| Offset      | Contents                  |
|-------------|---------------------------|
| **$60…$6F** | Teleport data for map ‘A' |
| **$70…$7F** | Teleport data for map ‘B' |
| **$80…$8F** | Teleport data for map ‘C' |
| **$90…$9F** | Teleport data for map ‘D' |
| **$A0…$AF** | Teleport data for map ‘E' |
| **$B0…$BF** | Teleport data for map ‘F' |
| **$C0…$CF** | Teleport data for map ‘G' |
| **$D0…$DF** | Teleport data for map ‘H' |

### Offsets $E0…$FF Palette data for the individual levels

Each level has a palette, or colour scheme, comprising one background colour and
three foreground colours thus four colours in total.

The colour information for each level is stored in a 4-byte chunk.

The first byte in the chunk indicates the background colour and the subsequent three indicate the foreground colours.

Each of the four bytes contains a number between 0 and 7 corresponding to a colour in the BBC Micro's palette.
No colour may appear twice in the scheme.

See the section on the BBC Micro palette for details.

| Offset      | Contents                       |
|-------------|--------------------------------|
| **$E0…$E3** | Colour scheme data for map ‘A' |
| **$E4…$E7** | Colour scheme data for map ‘B' |
| **$E8…$EB** | Colour scheme data for map ‘C' |
| **$EC…$EF** | Colour scheme data for map ‘D' |
| **$F0…$F3** | Colour scheme data for map ‘E' |
| **$F4…$F7** | Colour scheme data for map ‘F' |
| **$F8…$FB** | Colour scheme data for map ‘G' |
| **$FC…$FF** | Colour scheme data for map ‘H' |

### Offsets $100…$E1F Individual level data

Each level is a 28 x 24 grid (28 columns and 24 rows) and is stored in 420 bytes.
Each cell can contain one of thirty-two possible entries and is therefore stored as 5 bits.

Starting in the top left-hand corner, the map data is stored in a sequence of 84 5-byte ‘words'.
Each 5-byte word contains 8 map cells.

The individual bytes that make up the words are in some sense stored ‘back-to-front'
in the game file. The low byte is stored first and the high byte is stored last.

This is best illustrated by an example.

Let's consider a typical 5-byte word represented by the bytes **A**, **B**, **C**, **D**
and **E**. In the game file they appear in the order presented. **A** first then **B** and
so on with **E** stored last. In memory, however, they should appear like this **EDCBA**.

Now consider the first three map cells (denoted by X, Y, and Z), in a collection
of eight (going from left to right on screen). The format of the two lower bytes
(**A** and **B**) is shown below and the pattern presented continues for the remaining
3 bytes and 5 cells.


| B7  | B6  | B5  | B4  | B3  | B2  | B1  | B0  | A7  | A6  | A5  | A4  | A3  | A2  | A1  | A0  |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| -   | Z   | Z   | Z   | Z   | Z   | Y   | Y   | Y   | Y   | Y   | X   | X   | X   | X   | X   |

As for the location of the specific data;

| Offset        | Contents         |
|---------------|------------------|
| **$100…$2A3** | Data for map ‘A' |
| **$2A4…$447** | Data for map ‘B' |
| **$448…$5EB** | Data for map ‘C' |
| **$5EC…$78F** | Data for map ‘D' |
| **$790…$933** | Data for map ‘E' |
| **$934…$AD7** | Data for map ‘F' |
| **$AD8…$C7B** | Data for map ‘G' |
| **$C7C…$E1F** | Data for map ‘H' |

The values a cell can take correspond to the following game objects,

| Value/Sprite Number | Corresponding Object                |
|---------------------|-------------------------------------|
| **$00**             | Boulder                             |
| **$01**             | Diamond                             |
| **$02**             | Dirt 1                              |
| **$03**             | Dirt 2                              |
| **$04**             | Time Capsule                        |
| **$05**             | Skull                               |
| **$06**             | Empty Space                         |
| **$07**             | Wall 1                              |
| **$08**             | Wall 2 (Left edge bold)             |
| **$09**             | Wall 3 (Right edge bold)            |
| **$0A**             | Wall 4 (Top edge even, bold)        |
| **$0B**             | Wall 5 (Bottom edge even, bold)     |
| **$0C**             | Rounded Wall 1 (Top Sloping like /) |
| **$0D**             | Rounded Wall 2 (Top Sloping like \) |
| **$0E**             | Rounded Wall 3                      |
| **$0F**             | Rounded Wall 4                      |
| **$10**             | Wall 1                              |
| **$11**             | Wall 2 (Top Sloping like /)         |
| **$12**             | Wall 3 (Top Sloping like \)         |
| **$13**             | Wall 4                              |
| **$14**             | Wall 5                              |
| **$15**             | Obstacle (e.g. TV in Baby Repton)   |
| **$16**             | Safe                                |
| **$17**             | Cage (For spirits)                  |
| **$18**             | Monster Egg                         |
| **$19**             | Key                                 |
| **$1A**             | Fungus                              |
| **$1B**             | Bomb                                |
| **$1C**             | Teleport                            |
| **$1D**             | Crown                               |
| **$1E**             | Repton                              |
| **$1F**             | Spirit (Animation Cell 1)           |

### Offsets $E20…$261F Sprite data

Each game file contains 48 sprites.

Each sprite is 16 pixels wide and 32 pixels deep at 2 bits per pixel (4 colours).

Therefore, each sprite is 128 bytes in length.

Furthermore, each sprite is made of 16 8-byte tiles (4x8 pixels) arranged in a 4 by 4 grid,
each byte corresponding to a tile row of 4 pixels.

The tiles are stored sequentially from left to right and top to bottom.

A pair of bits represents each individual pixel and each byte stores four pixels.
Let there be for pixels P, Q, R, T stored in a byte. Then they are stored as follows

| 7   | 6   | 5   | 4   | 3   | 2   | 1   | 0   |
|-----|-----|-----|-----|-----|-----|-----|-----|
| P   | Q   | R   | T   | P   | Q   | R   | T   |

With bits 7, 6, 5 and 4 considered as ‘high' in the pairing.
The pixel values then correspond to a colour in a colour scheme set out for a given level.

The first 32 sprites correspond to the objects listed in the previous section.
The remaining 16 sprites are for animation and they are

| Sprite  | Number Details                  |
|---------|---------------------------------|
| **$20** | Spirit (Animation Cell 2)       |
| **$21** | Monster (Animation Cell 1)      |
| **$22** | Monster (Animation Cell 2)      |
| **$23** | Repton looking left             |
| **$24** | Repton looking right            |
| **$25** | Repton walking right (Cell 1)   |
| **$26** | Repton walking right (Cell 2)   |
| **$27** | Repton walking right (Cell 3)   |
| **$28** | Repton walking right (Cell 4)   |
| **$29** | Repton walking left (Cell 1)    |
| **$2A** | Repton walking left (Cell 2)    |
| **$2B** | Repton walking left (Cell 3)    |
| **$2C** | Repton walking left (Cell 4)    |
| **$2D** | Repton walking up/down (Cell 1) |
| **$2E** | Repton walking up/down (Cell 2) |
| **$2F** | Monster hatching                |

The sprites $1E, $23 and $24 form an animation sequence after an input time out.
This gives the effect of Repton looking around after he hasn't moved around for a while.

## BBC Micro Palette

Repton 3 ran in the BBC Micro's Mode 5 display.
This entitled the game to a choice of four from a possible eight colours.
Each colour was assigned a unique number between 0 and 7.

| Number  | Corresponding Colour | R   | G   | B   |
|---------|----------------------|-----|-----|-----|
| **$00** | Black                | $00 | $00 | $00 |
| **$01** | Red                  | $FF | $00 | $00 |
| **$02** | Green                | $00 | $FF | $00 |
| **$03** | Yellow               | $FF | $FF | $00 |
| **$04** | Blue                 | $00 | $00 | $FF |
| **$05** | Magenta              | $FF | $00 | $FF |
| **$06** | Cyan                 | $00 | $FF | $FF |
| **$07** | White                | $FF | $FF | $FF |
