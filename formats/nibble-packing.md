# Nibble packing

Titles: `title-2`

How `title-2` packs some of its data. A **packed block** is a 39-byte
header followed by a stream of 4-bit **nibbles** that says how to build
the unpacked bytes. The level maps of `level-ground.md` ("In
`title-2`") are packed this way. Vectors: `../vectors/nibble-packing/`.

## The header

| Bytes | Meaning |
|---|---|
| 0 | Not used |
| 1 | **Run byte A** |
| 2 | **Run byte B** |
| 3 | **Single byte A** |
| 4 | **Single byte B** |
| 5–6 | The **pair**: two bytes, in this order |
| 7–38 | Sixteen **stored pairs** `S0` to `S15`, two bytes each, in this order (`S0` is bytes 7 and 8) |

## The nibble stream

The stream starts at byte 39 of the block. Each byte holds two nibbles:
its high 4 bits come first, then its low 4 bits. The stream is read one
nibble at a time, in order, with nothing skipped between commands, so a
command can start in the middle of a byte.

**Reading a byte value** means reading two nibbles; the value is 16 ×
the first + the second. **Reading a number of 3 (or 4) nibbles** means
reading them in turn, the first one being the most significant: for
nibbles `a`, `b`, `c` the number is 256`a` + 16`b` + `c`.

The output starts empty and only grows. Its **length** is the number of
bytes appended so far.

## Commands

The stream is a run of commands. Each starts with one nibble, the
command number `c`. What it reads after that and what it appends:

| `c` | Reads next | Appends |
|---|---|---|
| 0 | a nibble `n` | If `n` is 0, nothing: the stream ends here. Otherwise reads `n` byte values and appends them in order |
| 1 | a byte value `v` | `v` |
| 2 | two byte values | both, in order |
| 3 | a nibble `n`, then a byte value `v` | `v`, `n` + 3 times |
| 4 | a nibble `n` | run byte A, `n` + 3 times |
| 5 | a nibble `n` | run byte B, `n` + 3 times |
| 6 | nothing | the pair (2 bytes) |
| 7 | nothing | single byte A |
| 8 | nothing | single byte B |
| 9 | a nibble `n` | a copy of 2 bytes from distance `n` + 2 |
| 10 | a nibble `n`, then a byte value `d` | a copy of `n` + 3 bytes from distance `n` + 3 + `d` |
| 11 | a nibble `n`, then a number `e` of 3 nibbles | a copy of `n` + 3 bytes from distance `e` + 259 |
| 12 | a nibble `n`, then a number `d` of 4 nibbles | a copy of `n` + 3 bytes from distance `d` |
| 13 | nothing | a copy of 1 byte from distance 1 (the last byte again) |
| 14 | nothing | a copy of 2 bytes from distance 2 (the last two bytes again) |
| 15 | a nibble `i` | stored pair `Si` (2 bytes) |

A **copy of `k` bytes from distance `d`** appends `k` bytes one at a
time. Each one is the byte at position (length − `d`) of the output, with
the length as it is just before that byte is appended (positions count
from 0). So the copy starts `d` bytes back from the end, and when `d` is
less than `k` it goes on into bytes it has just appended: a copy of 6
bytes from distance 2 after `…, 5, 9` appends 5, 9, 5, 9, 5, 9.

The unpacked data is the output when the stream ends. A stream that ends
in the middle of a byte leaves the rest of that byte unused.

## Example (synthetic)

A block whose header has run byte A = 0x00, single byte A = 0x11, the
pair = 0x22, 0x33 and `S3` = 0x44, 0x55 (other header bytes 0), followed
by the stream bytes

    1A B7 42 6F 3D 9A 2F 10 30 1C 00 00

The nibbles are 1, A, B, 7, 4, 2, 6, F, 3, D, 9, A, 2, F, 1, 0, 3, 0,
1, C, 0, 0, 0, 0:

| Nibbles | Command | Output after it |
|---|---|---|
| 1 A B | 1: byte value 0xAB | AB |
| 7 | 7: single byte A | AB 11 |
| 4 2 | 4: run byte A, 2 + 3 = 5 times | AB 11 00 00 00 00 00 |
| 6 | 6: the pair | … 00 22 33 |
| F 3 | 15: `S3` | … 22 33 44 55 |
| D | 13: the last byte again | … 44 55 55 |
| 9 A | 9: 2 bytes from distance 10 + 2 = 12 | … 55 55 AB 11 |
| 2 F 1 0 3 | 2: byte values 0xF1, 0x03 | … AB 11 F1 03 |
| 0 1 C 0 | 0: 1 byte value, 0xC0 | … F1 03 C0 |
| 0 0 | 0 with `n` = 0: end | |

The unpacked data is the 17 bytes

    AB 11 00 00 00 00 00 22 33 44 55 55 AB 11 F1 03 C0

The last nibble of the stream's last byte (0) is not used.

## Checks

Every block that the game unpacked while each of its 48 level entries
loaded was unpacked by these rules and compared, byte for byte, with the
bytes the game itself wrote: 304 unpackings of 78 different blocks,
986 416 bytes in all, with no differences. Every command came up, each
one both starting at the high nibble of a byte and at the low nibble.

## Not covered

- Copies from a distance greater than the length so far, or of
  distance 0 (command 12 can express both). They don't occur in the
  title's blocks.
- Which other data of the title is packed this way, other than the
  level maps.
