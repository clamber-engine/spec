# Voice output

Titles: `title-1`

How the eight voices become the audio the console plays: which points
of its sample a voice plays on each output, how it blends them, how its
level and volumes scale the result, and how the voices are mixed. A
voice's level is in `voices.md`; what a voice is handed is in
`songs.md`; samples and their points are in `sound.md`. Echo and noise
are not covered (see "Not covered"). Vectors: `../vectors/voice-output/`.

Time is counted in audio outputs as in `songs.md` and `voices.md`
(32 000 a second). Each output has a left and a right value, each a
whole number from −32 768 to 32 767.

Rounding: ⌊ ⌋ rounds down (towards minus infinity). **Rounded to even**
means: if the number is odd, take 1 off. **Wrapped** means brought into
−32 768 to 32 767 by adding or taking off 65 536. **Limited** means
made −32 768 if below that and 32 767 if above that.

## A note's points

A **note** is what a voice plays from a start (`voices.md`) to the next
start. Its **points** are its sample's outputs as `sound.md` decodes
them, in the order played: the first pass, then loop passes (point 0 is
the first output of block 0). See "Taking blocks" for when the sample
changes during a note.

## Position

A note has a **position** `P`, a whole number counting points in
1/4096ths. Its whole part is `i` = ⌊`P` / 4096⌋ and its fraction is
`f` = ⌊(`P` mod 4096) / 16⌋, from 0 to 255.

If the start is taken at the check on output `t`, `P` is 0 on output
`t` + 7. On each output after that, `P` goes up by the voice's pitch
`p` mod 16 384 (pitches are 0 to 65 535, `songs.md`; 16 384 plays
like 0, 20 480 like 4 096). So pitch 4096 moves one point per output
and the highest, 16 383, just under four.

## Blending

On an output with position `i`, `f`, the note's **blend** `B` is made
from points `i`, `i` + 1, `i` + 2 and `i` + 3. With `x`ₖ point `i` + `k`
and `w`ₖ its weight (below), for `k` = 0 to 3:

1. `a`ₖ = ⌊`w`ₖ × `x`ₖ / 2048⌋.
2. `a`₀ + `a`₁ + `a`₂, wrapped.
3. Add `a`₃ and limit.
4. Round to even: that is `B`.

**Weights.** The weights follow a bell centred where the note is
played. Point `i` + `k` is at a distance of

`d`ₖ = `k` − 1 − (`f` + ½) / 256

points from it (from −2 to 2), and the bell is

`h`(`d`) = e^(−`d`² / (2 × 0.945²)) × (1 + cos(π`d` / 2)) / 2,

a bell of width 0.945 points brought down smoothly to 0 at a distance
of 2 points. Then

`w`ₖ = 2048 × `h`(`d`ₖ) / (`h`(`d`₀) + `h`(`d`₁) + `h`(`d`₂) + `h`(`d`₃)),

rounded to the nearest whole number. No unrounded weight is within
0.001 of a half, so ordinary floating-point arithmetic gives the same
whole numbers. The four weights add up to 2047, 2048 or 2049, and the
weights at `f` are those at 255 − `f` in reverse order.

For example, at `f` = 0 the weights are 370, 1303, 374 and 0 for points
`i` to `i` + 3: the point heard most is `i` + 1; at `f` = 128 they are
54, 967, 972 and 55.

This bell is a description of the console's weights, not a copy: the
console's own weights differ from these by at most 3 each, and the
output by a little (see "Measurements"). Two things near full scale
turn a small difference into a large one: step 2 can wrap with one set
of weights and not with the other (only with points near −32 768 or
32 767), and a sum that is −32 768 with one set and a little more with
the other gives −32 768 or nearly 32 767 under main volume −128
("Volumes and mixing", step 3).

## The voice's sound

On output `k`, a voice's **sound** is

`y` = ⌊`B` × `L` / 2048⌋, rounded to even,

where `L` is the voice's level on output `k` − 2 (`voices.md`) and `B`
is the blend on output `k`. Around a start taken at the check on output
`t`:

- On outputs `t` and `t` + 1, the note before goes on: its position
  moves on and its blend is used as above (with the level of `t` − 2
  and `t` − 1). If there was no note before, the sound is 0.
- On outputs `t` + 2 to `t` + 6 the sound is 0.
- From output `t` + 7 on, the sound is from the new note. Its level then
  is still 0 for the first outputs (`voices.md`: 4 outputs of 0 after
  the check), so with the 2-output delay it is first heard on output
  `t` + 7 or later depending on the envelope.

Before a voice's first start its sound is 0.

## Taking blocks

A note **takes** its sample's blocks one at a time: block 0 when the
start is taken, and its `m`-th block after that (`m` = 1, 2, …) on the
first output on which `i` reaches 16`m` − 12, before the blend on that
output. A block's points are point 16`m` to 16`m` + 15 of the note.

- After a block with the end flag 1, the next block taken is the loop
  block of the sample the voice is set to at the moment it is taken.
  That is the note's own sample unless the voice was handed another
  sample during the note; then the note goes on from that sample's loop
  block, and on through that sample's blocks. The decoding goes on
  across the jump as in `sound.md` (the two earlier outputs are the
  note's last two points).
- When the block taken has the end flag 1 and the loop flag 0 (the last
  block of a one-shot sample), the note **ends** on that output: the
  level becomes 0 there, and the voice is in release (`voices.md`), also
  if it was in release already. Its sound goes on for 2 more outputs
  with the levels from before (the 2-output delay), then is 0.

So with a one-shot sample of `n` blocks (`n` 2 or more) the note ends on
the first output on which `i` reaches 16(`n` − 2) + 4: the last block is
never heard, and of the block before it only about the first half.
A looping sample needs no points past its blocks: the next block is
always taken at least 12 points before its first point is used.

## Volumes and mixing

Volumes are bytes read as signed: 0 to 127 as they are, 128 to 255 as
the byte − 256 (so 255 is −1 and 128 is −128).

1. Each voice's **left part** is ⌊`y` × `l` / 128⌋ and its **right part**
   ⌊`y` × `r` / 128⌋, where `l`, `r` are its left and right volume.
2. The left parts of voices 0 to 7 are added in that order, starting
   from 0, and limited after each addition. Likewise the right parts.
3. The left output is ⌊`S` × `M` / 128⌋, where `S` is the left sum and
   `M` the left main volume; if that is 32 768 (only when `S` is
   −32 768 and `M` is −128) it is −32 768. Likewise the right output.

This is the output with the echo volume at 0 (as when a song starts,
`songs.md`); what echo adds is not covered.

## When handed values count

Starts and releases are taken at checks as in `voices.md`. A pitch, a
volume, a main volume or a sample number handed between two outputs
counts from one of the next three outputs: which one depends on the
voice and on the value, and is not covered. (The same holds for the
envelope settings and gain in `voices.md`, which were measured there
on one voice.) `songs.md` hands values once per step, about 400
outputs apart.

## Examples

These follow from the rules above; `../vectors/voice-output/` has more.

- One voice, pitch 4096, volumes 127, main volume 127, a looping sample
  whose points are all 4 096, attack setting 15 (with the start taken
  at the check on output `t`, level 2047 from output `t` + 6 on). Once
  the level is 2047: `f` is always 0, so the blend is
  ⌊370 × 4 096 / 2048⌋ + ⌊1 303 × 4 096 / 2048⌋ +
  ⌊374 × 4 096 / 2048⌋ + 0 = 740 + 2 606 + 748 = 4 094; the sound is
  ⌊4 094 × 2047 / 2048⌋ = 4 092 (already even); the left part is
  ⌊4 092 × 127 / 128⌋ = 4 060 and the output ⌊4 060 × 127 / 128⌋ =
  4 028, left and right.
- The same voice with volume −128 and main volume −128 gives 4 092:
  both negate exactly.

## Measurements

The rules were checked against an emulation of the console's sound chip
(the one used for `voices.md`), driven directly with made-up samples,
comparing the left and right value of every output. With the console's
own weights in place of the bell (measured with one point of −32 768 in
a silent looping sample, played at pitch 16, 256 outputs a point, with
level 2047 and volumes −128, so that each output gives one weight
exactly), every output matched:

- 4 000 cases with up to 8 voices: random samples (1 to 20 blocks,
  filters, loops at any block, one-shot), pitches from 1 to 65 535,
  signed volumes and main volumes, envelope settings and gains; starts
  and releases at random outputs, restarts while sounding, and pitch,
  volume, main volume and sample number handed during notes: 23 104 969
  outputs, 0 differences. (For values handed during notes the check
  used the measured output each value first counts on; see "When handed
  values count".)
- 1 000 cases of one voice and 1 000 of several voices started together,
  with nothing handed during the notes: 1 666 451 outputs, 0
  differences.

With the bell's weights, on the same 6 000 cases (49 542 840 left and
right values; about a third of the blocks are deliberately loud):
65 % of the values are the same as the console's and 90 % within 7. In
the 5 534 cases with neither of the near-full-scale effects in "Blending",
no value differs by more than 243. Of the rest, 461 cases have a blend
whose step 2 wraps with one set of weights and not the other, and 5 a
sum of −32 768 under main volume −128 with one set only.

No `title-1` sample can make step 2 wrap with either set of weights: its
points, first pass and loop passes, never come close enough to full
scale at any fraction.

The vectors are computed with the bell. The emulation differs from them
by at most 240 (case `mix-limits`).

All 70 `title-1` samples start with filter 0 and every one-shot sample
has at least 6 blocks, so the cases left out below don't arise in its
songs.

## Not covered

- Echo (echo volume, feedback, filter, delay, echo writing), noise, and
  a voice's pitch being changed by the voice before it.
- Which output a value handed between outputs first counts on (above).
- A first block with a filter other than 0 (the filter then starts from
  points of the note before, not from 0); one-shot samples of one block.
- A start on a voice within 8 outputs of its previous start; a sample
  number handed to a voice fewer than 3 outputs before the check that
  takes a start (which sample the note starts with).
- Gain settings of 128 and over (`voices.md`).
- Sound effects; other titles.
