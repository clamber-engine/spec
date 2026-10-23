# Sound data

Titles: `title-1`

How the recorded sounds that music and sound effects are made from
(**samples**) are stored and turned into audio, which samples are there
while each song plays, and where each song's data is stored. What a
song's data says (tracks, notes, timing, loops, and what each voice is
handed) is in `songs.md`. Vectors: `../vectors/sound/`.

Where the tables below start in a supported image is in `locators.md`,
under the names given here. Locations are as in `rom-image.md`; all
2-byte numbers are stored low byte first.

## Samples

A sample is a short recorded sound, stored compressed. Samples are
numbered from 0.

### The sample table

Entry `k` of the **sample table** is 3 bytes: the location of sample
`k`'s **record**. In `title-1` the table has samples 0 to 69. The 45
bytes after entry 69, up to the end of the bank, are all zero (a
location of zero bytes is not in the image).

### Sample records

| Bytes | Meaning |
|---|---|
| 0–1 | **Loop offset** `L` |
| 2–3 | **Length** `n` |
| 4 to `n` + 3 | The sample's **data**: `n` bytes |

The data is a run of **blocks** of 9 bytes each. Block `i` is bytes 9`i`
to 9`i` + 8 of the data, for `i` from 0 to ⌊`n` / 9⌋ − 1. Bytes left
over after the last whole block are not part of the sample. In `title-1`
there is one left-over byte in 68 samples and none in 2.

The first byte of a block is its **header**:

| Bits | Meaning |
|---|---|
| 7–4 | **Shift** `r`, 0 to 15 |
| 3–2 | **Filter** `f`, 0 to 3 |
| 1 | **Loop flag** |
| 0 | **End flag** |

The other 8 bytes hold 16 **steps**, two per byte, the high 4 bits
first. Each step is a signed 4-bit number `v`: 0 to 7 are 0 to 7, and
8 to 15 are −8 to −1.

### Last block and loop

A sample's **last block** is the first block whose end flag is 1. In
`title-1` it is always the last whole block of the data.

If the last block's loop flag is 1, the sample **loops**; otherwise it
is **one-shot**. A looping sample's **loop block** is block `L` / 9. In
`title-1`, `L` is always a multiple of 9 and is 0 in every one-shot
sample; 32 of the 70 samples loop, 24 of them from block 0.

## Decoding a sample

Decoding turns the steps into **output values**: signed whole numbers
from −32 768 to 32 767, one per step. Each output depends on the step
and on the two outputs before it. Before a sample's first block, both
earlier outputs count as 0.

For each step `v` in order, with `r` and `f` from the step's block
header, `y1` the output before this one, `y2` the one before that, and
⌊ ⌋ rounding down (towards minus infinity):

1. **Scale.** If `r` is 12 or less, `s` = ⌊`v` × 2^`r` / 2⌋. If `r` is
   13, 14 or 15, `s` = −2 048 when `v` is negative and 0 otherwise.
2. **Filter.** With `a` = `y1` and `b` = ⌊`y2` / 2⌋, add to `s`:
   - filter 0: nothing;
   - filter 1: ⌊`a` / 2⌋ + ⌊−`a` / 32⌋;
   - filter 2: `a` − `b` + ⌊`b` / 16⌋ + ⌊−3`a` / 64⌋;
   - filter 3: `a` − `b` + ⌊−13`a` / 128⌋ + ⌊3`b` / 16⌋.
3. **Limit.** If `s` is above 32 767 it becomes 32 767; if below
   −32 768 it becomes −32 768.
4. **Output.** The output is 2`s`, brought into range: if 2`s` is
   32 768 or more, subtract 65 536; if it is below −32 768, add 65 536.
   So every output is even, and an `s` from 16 384 to 32 767 gives a
   negative output (for example `s` = 32 767 gives −2).

### Playing a sample through

A sample is played by decoding its blocks from block 0 to its last
block, in order: the **first pass**.

- A one-shot sample ends there. Its blocks after the last block are
  never decoded.
- A looping sample goes on from its loop block after its last block,
  through to the last block again, and again, without end. Each time
  through is a **loop pass** (loop pass 1, 2, …). The earlier outputs
  carry on across the jump: the first step of a loop pass uses the
  last two outputs before it, which are the end of the previous pass.

So when the loop block, or a block after it, uses a filter other than
0, a loop pass can give different outputs from the same blocks in the
first pass, and from other loop passes.

### Turning a sample into audio with a loop point

One output is one audio sample point. The rate at which a song plays a
sample is its pitch (`songs.md`).

- One-shot: the first pass.
- Looping: the first pass, with the loop starting at output 16 × (loop
  block) and ending after the last output. This is exact when every loop
  pass gives the same outputs as the loop blocks did in the first pass.

In `title-1` that holds for 30 of the 32 looping samples. The other two
are samples 16 and 26; both are in sample set 0, so the first level's
song can use them:

- Sample 26: loop passes 2, 3, 4, … are all the same, and differ from
  loop pass 1 and from the first pass. An exact audio version is the
  first pass, then loop pass 1, then loop pass 2, with the loop being
  loop pass 2.
- Sample 16: from loop pass 3 on, the passes take turns between two
  versions (passes 3, 5, 7, … are the same, and so are 4, 6, 8, …).
  An exact audio version is the first pass and loop passes 1 to 4, with
  the loop being loop passes 3 and 4 together. Next to each other, the
  two versions differ by at most 18 in any output.

An importer can find this for any looping sample by decoding loop
passes until a run of passes repeats.

## Which samples a song has

Songs are numbered from 0. While song `s` plays, the samples there are:

1. the **shared samples**: the numbers in the **shared sample list**;
2. the samples of **sample set** `s`: the numbers in that set's list.

A **list** is a run of 2-byte sample numbers ended by the number 65 535
(which is not a sample).

Entry `s` of the **sample-set table** is 2 bytes: the address of set
`s`'s list, in the same bank as the table. In `title-1` the table has
sets 0 to 27, and sets 18 and 26 have the same list. The shared list
has 29 numbers. No list has a number twice, and in sets 0 to 26 no
number is also in the shared list.

The first level plays song 0, with sample set 0 (10 samples). On
entering the first level, these 39 samples (shared, then set 0) are
the samples in place, with the loop points above.

## Song data

Entry `s` of the **song table** is 3 bytes: the location of song `s`'s
**block**. In `title-1` the table has songs 0 to 26.

| Bytes | Meaning |
|---|---|
| 0–1 | Length `m` |
| 2–3 | **Base address** `D` |
| 4 to `m` + 3 | The song's data: `m` bytes |

The song data refers to places in itself by 2-byte addresses: address
`A` is byte `A` − `D` of the song data. In `title-1`, `D` is the same in
all 27 songs. The first level's song data was unchanged after 800
frames of play.

## Measurements

- Decoding: all 70 samples decoded as above gave the console's own
  decoded values, every output equal, for the first pass and, for the
  32 looping samples, four loop passes.
- Which samples a song has and where they come from: the samples and
  their loop points, and the song data, read as above matched what the
  game had in place at five points, each after loading had finished:
  two in the first level (song 0, set 0), two on the world map (song
  12, set 12) and one on a screen before a game starts (song 9, set 9).

## Not covered

- What song data means: see `songs.md`, which also lists what of it
  is not covered yet (how a voice makes sound from its settings).
- Sound effects: which sounds the first level plays, when, and how they
  are stored.
- Effects applied to the mixed sound (echo and others).
- Other levels' songs, and what is in place before the first song of a
  game. Which songs play in the places the first level leads to is in
  `../behavior/music.md`.
- Sample set 27, which no song has.
- Other titles.
