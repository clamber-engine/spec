# Nibble packing vectors

Synthetic vectors for `../../formats/nibble-packing.md`. Every block is
made up: random headers and random command streams. None of the data
comes from a game.

Each case is a pair of files:

- `NAME.packed.hex`: the packed block (39-byte header, then the nibble
  stream), in hex, 32 bytes per line.
- `NAME.unpacked.hex`: the unpacked data, in hex, 32 bytes per line.

Each packed block is exactly as long as its stream: the stream's end
(command 0 with `n` = 0) is in its last byte.

| Case | What it exercises |
|---|---|
| `each-command` | Commands 1 to 15 in random order, each many times, starting at both nibble alignments |
| `copies` | Literal bytes and every kind of copy (commands 9 to 14), including copies from a distance less than their length, which repeat bytes they have just appended |
| `runs-and-pairs` | Commands 0 and 3 to 8 and 15: literals, runs of a given byte, the run bytes, the single bytes, the pair and the stored pairs |
| `random-a`, `random-b`, `random-c` | 1500, 1500 and 3000 random commands of every kind |
| `ends-mid-byte` | A header of zeros and the stream bytes 15 A0 0F: command 1 (byte value 0x5A), then the end in the middle of the second byte; the third byte's low nibble is not used. Unpacked: one byte, 0x5A |

Every command appears in the first six cases both starting at the high
nibble of a byte and at the low nibble.
