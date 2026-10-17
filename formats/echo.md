# Echo

Titles: `title-1`

What the echo adds to the audio: a memory that holds what the voices
played a fixed time before, a filter over what comes back from it,
feedback into it, and the echo volume. `voice-output.md` gives each
voice's left and right part and the main output this builds on;
`songs.md` gives the values a song hands (commands 21 to 24 and the
values when a song starts). Vectors: `../vectors/echo/`.

Time is counted in audio outputs as in `voice-output.md` (32 000 a
second). The words **rounded down** (⌊ ⌋), **rounded to even**,
**wrapped** and **limited** are as in `voice-output.md`. Filter values,
the feedback and echo volumes are bytes read as signed (0 to 127 as they
are, 128 to 255 as the byte − 256).

## The echo memory

The **echo delay** `d` is a whole number from 0 to 15. The **echo
memory** holds 512 × `d` **entries** (1 entry when `d` is 0), numbered
from 0; each entry has a left and a right value, each from −32 768 to
32 767.

The **position** is an entry number. Each output reads and then
(when echo writing is on) overwrites the entry at the position; after
the output the position moves on by 1, and from the last entry back
to 0. So a value written to an entry on output `n` is read again on
output `n` + 512 × `d` (`n` + 1 when `d` is 0).

The **history** holds, for left and for right, the last 8 values taken
from the memory: `h0` (taken 7 outputs before) to `h7` (taken on this
output). Before the first output they are all 0.

In `title-1` the echo delay is 4 (2 048 outputs, 64 ms). It is set once,
when the sound starts, and songs don't change it; the memory is all 0
then.

## Each output

For left and for right separately (with the left or right part and
volume), on every output:

1. **Echo input** `E`: the parts (`voice-output.md`, "Volumes and
   mixing", step 1) of the voices with echo on, added in voice order
   0 to 7 starting from 0, limited after each addition. Every voice,
   echo on or not, also goes into the main sum as before.
2. **Taking a value**: with `e` the entry's value at the position,
   ⌊`e` / 2⌋ joins the history as `h7`; the others move down one
   (the old `h0` is dropped).
3. **Filtering**: with the filter values `c0` to `c7`, each **filter
   part** `p`*j* is ⌊`h`*j* × `c`*j* / 64⌋. Add `p0` to `p6` and wrap the
   sum; wrap `p7` on its own; add the two and limit; round to even. This
   is the **echo value** `F`.
4. **Writing**: if echo writing is on, the entry at the position
   becomes `E` + ⌊`F` × `b` / 128⌋ (that product wrapped first), limited,
   then rounded to even, where `b` is the feedback. If echo writing is
   off the entry keeps its value.
5. **Echo part**: ⌊`F` × `l` / 128⌋, wrapped, where `l` is the echo
   volume (left or right).
6. **The output** is the main output of `voice-output.md` ("Volumes and
   mixing", step 3) plus the echo part, limited.

Then the position moves on.

So `c0` goes with the oldest value in the history and `c7` with the one
just taken: a filter whose only value that isn't 0 is `c0` delays the
echo by 7 more outputs. Since every history value is from −16 384 to
16 383, the sum of `p0` to `p6` can only wrap when `c0` to `c6`, taken
without their signs, add up to 128 or more (as with 96, 0, 36, 0, 12,
0, 0, on a loud memory). The echo is heard
even with echo writing off (whatever the memory holds comes round again
unchanged), and the memory is written even with echo volume 0.

### When handed values count

As in `voice-output.md`, a value handed between two outputs counts from
one of the next three outputs, and which one is not covered. (In the
measurements the echo volume, feedback, filter values and echo writing
counted from the next output, and echo on or off for a voice from the
output after that.)

## In `title-1`

- The echo delay is 4 and the memory starts all 0 (above).
- Commands 21 to 24 of `songs.md` hand the feedback and echo volume
  (turning echo writing on), echo on and off for a voice, and the 8
  filter values `c0` to `c7` in that order. Some songs hand filters
  with which the sum of the first seven parts can wrap.
- When a song starts its echo volume and feedback are 0 and echo
  writing is off (`songs.md`). The first level's song never hands an
  echo volume, so no echo is heard in it and its memory stays all 0.
  Every other song hands one.

## Example

One voice with echo on, its sound and parts as in the first example of
`voice-output.md` (points all 4 096, left part 4 060 and main output
4 028 once its level is 2047), main volume 127; echo delay 1, filter
values 127, 0, 0, 0, 0, 0, 0, 0, echo volume 64 left and −128 right,
feedback 0, echo writing on, the memory all 0 at the start:

- Once the voice's part is steady, `E` is 4 060 (already even) and
  that is written to each entry, left and right.
- 512 outputs later the value 4 060 is taken back as 2 030; 7 outputs
  after that it is `h0`, and while `h0` is 2 030, `F` =
  ⌊2 030 × 127 / 64⌋ = 4 028 (already even).
- Left: echo part ⌊4 028 × 64 / 128⌋ = 2 014, output 4 028 + 2 014 =
  6 042. Right: echo part −4 028, output 0.

This is the case `unit-delay` in `../vectors/echo/` (the start handed
before output 4): the outputs are 4 028, 4 028 from output 13 to 530.
Output 12, the first with sound, is 2 014, 2 014; it comes back with
the filter on output 12 + 512 + 7 = 531, which is 4 028 + 1 007 =
5 035 and 4 028 − 2 014 = 2 014. From output 532 on they are 6 042, 0.

## Measurements

The rules were checked against the emulation of the console's sound
chip used for `voice-output.md`, driven directly with made-up samples
and echo memory, comparing the left and right value of every output and
the whole echo memory after the last output:

- 1 000 cases with the echo settings handed before the first output and
  not changed: 2 457 218 outputs, 0 differences.
- 2 000 cases with echo volume, feedback, filter values, echo on and off
  for voices and echo writing also handed at random outputs during the
  notes: 4 767 259 outputs, 0 differences (using the measured output
  each value first counts on).

The cases have voices as in `voice-output.md`'s checks (up to 8,
random samples, pitches, volumes, envelopes, starts, releases and
changes), echo delays 0 to 15, random filter values, feedbacks and
echo volumes (including 127 and −128), and the memory starting with
random entries (also near full scale). In 300 more cases of the second
kind (741 751 outputs, 0 differences) every edge of the rules was
counted and arose: echo input, written values and outputs limited; the
first seven filter parts wrapping, the filter sum limited; the
feedback product and the echo part wrapping. The exception is `p7`
wrapping on its own (only −128 × −16 384), which the vector case
`last-part` shows on the chip.

The vectors are computed with the weights of `voice-output.md`; the
emulation (with the console's weights) differs from them by at most
188 (case `mix-limits`), and with the console's weights it matches them
on every output and the memory.

## Not covered

- Which output a value handed between outputs first counts on (above).
- A change of echo delay while sounding (`title-1` never does that);
  where the echo memory sits in the sound memory and what happens when
  it overlaps samples or other data (it doesn't in `title-1`).
- Whether the memory is cleared when a song starts (`title-1` starts
  with it all 0; a song after one that wrote to it may find values
  left).
- The filter values before a song hands them (`songs.md`).
- A voice's pitch being changed by the voice before it, and muting all
  output. (Noise is in `voice-output.md`, "Noise": a voice's sound with
  noise on goes into the echo like any other.)
- Sound effects; other titles.
