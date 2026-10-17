# Voices

Titles: `title-1`

How a voice turns what it is handed (`songs.md`, "Channels and voices")
into sound. So far this covers the voice's **level**: how loud its
envelope lets it be on each audio output, from its envelope settings,
its gain setting, its starts and its releases. The rest is not covered
yet (see "Not covered"). Vectors: `../vectors/voices/`.

Time is counted in audio outputs as in `songs.md` (32 000 a second).
"Output `k`" of a voice means the level it has for that output. Settings
handed to a voice count from the next output on.

## The level

A voice's **level** `L` is a whole number from 0 to 2047. It is 0 before
the voice's first start. Between outputs the voice is in one of four
**parts**: attack, decay, sustain or release. It is in release before
its first start.

## The shared count

All eight voices share a **count** `c` that goes up by 1 on every
output. Only which outputs it falls on matters: what follows depends on
`c` being even, and on `c` plus a number being a multiple of a period.
Its value when a song starts is not covered; an engine may start it
anywhere.

## Rates

A **rate** is a number from 0 to 31. Rate 0 never makes a step. Rate
`r` from 1 to 31 has a **period** `P`:

| Rates | Periods |
|---|---|
| 1 to 7 | 2048, 1536, 1280, 1024, 768, 640, 512 |
| 8 to 15 | 384, 320, 256, 192, 160, 128, 96, 80 |
| 16 to 23 | 64, 48, 40, 32, 24, 20, 16, 12 |
| 24 to 31 | 10, 8, 6, 5, 4, 3, 2, 1 |

A step at rate `r` is **due** on an output when `c` + `q` is a multiple
of `P`, where `q` is 0 when `P` is a power of 2 (1, 2, 4, …, 2048), 496
when `P` is 3 times a power of 2, and 744 when `P` is 5 times a power of
2. So steps come every `P` outputs, on the same outputs for every voice,
whenever the voice's note started.

## Starts and releases

Outputs with an even `c` are **checks**. A start or a release handed to
a voice is taken at the voice's next check (on the output it was handed
for, if that one is a check).

A **start** taken at a check:

- The level stays as it was for that output: no step, whatever the part.
- The level is 0 for the next 4 outputs.
- From the output after those (the 5th after the check) the voice is in
  attack and its level follows "Each output" below.

A start taken at the check right after a check that took a start (2
outputs later) is lost: nothing happens, and the earlier start goes on.

A **release** taken at a check puts the voice in release from that
output on: on each output, `L` becomes `L` − 8, or 0 if that is below 0,
whatever the settings. So a released note is silent at most 256 outputs
later. A handed release is taken again at every check until a start is
handed; handing a start takes it back. If a release is handed after a
start but before the check that takes that start, the start happens and
the voice is released on the same output, so it stays at 0. This does
not happen in `title-1` songs (see "Measurements").

When a voice plays a sample that has no loop (`sound.md`) to its end,
its level becomes 0 and it is in release. Exactly on which output, from
the pitch and the sample's length, is not covered.

## Each output

On an output where the voice is not in release and not in the 4 outputs
of 0 after a start:

**Fixed level.** If the top bit of `e1` is 0, `L` = 16 × (`g` mod 128),
where `g` is the gain setting. Songs hand gain 127, so the level is 2032.
The part stays as it was. (Gain settings of 128 and over: not covered;
songs never hand them.)

**Envelope.** If the top bit of `e1` is 1, its fields are:

| Field | Value |
|---|---|
| attack setting `a` | `e1` mod 16 |
| decay setting `d` | ⌊`e1` / 16⌋ mod 8 |
| sustain line `s` | ⌊`e2` / 32⌋ |
| sustain rate | `e2` mod 32 |

The voice works out a **next level** `N` and a rate from its part:

| Part | `N` | Rate |
|---|---|---|
| attack | `L` + 32 (`L` + 1024 when `a` = 15) | 2`a` + 1 |
| decay | `L` − 1 − ⌊(`L` − 1) / 256⌋ | 2`d` + 16 |
| sustain | `L` − 1 − ⌊(`L` − 1) / 256⌋ | the sustain rate |

Then, in this order:

1. In decay: if ⌊`N` / 256⌋ = `s`, the voice goes to sustain (the rate
   for this output stays the decay rate).
2. If `N` is over 2047, `N` becomes 2047, and in attack the voice goes to
   decay.
3. If a step at the rate is due on this output, `L` becomes `N`.
   Otherwise `L` stays as it was.

Steps 1 and 2 happen on every such output, whether a step is due or
not. So:

- An attack with `a` from 0 to 14 climbs by 32 a step. At 2016 the next
  level is over 2047, so on the next output the voice goes to decay; if
  a step was due there, `L` is 2047, otherwise it stays 2016 and decay
  starts from 2016.
- A decay stops one step above the line: on the first output where its
  next level would be at the line (⌊`N` / 256⌋ = `s`), the voice goes to
  sustain with `L` still above it. For example with `s` = 0 a fast decay
  ends at 256, with `s` = 6 at 1799.
- With `s` = 7 a decay from 2047 goes to sustain on its first output,
  without a step.
- A decay or sustain step takes off ⌊(`L` − 1) / 256⌋ + 1: 8 at 2047, 1
  from 256 down to 1, and nothing at 0.
- Settings handed in the middle of a note (for example by commands 16
  and 33 to 37 in `songs.md`) apply from the next output in the part the
  voice is in; they don't change the part.

## Examples

These follow from the rules above; `../vectors/voices/` has more.

- `e1` with `a` = 15 and `d` = 0, `e2` with `s` = 7, sustain rate 0
  (a fast, held note), start taken at a check on output `t`: the level
  is the old level on `t`, 0 on `t` + 1 to `t` + 4, 1024 on `t` + 5 and
  2047 from `t` + 6 on, as long as the note is held.
- The same note, released at a check on output `u`: 2039 on `u`, 2031
  on `u` + 1, and so on, 0 from `u` + 255 on.
- `a` = 0 (rate 1, period 2048): the attack takes 63 steps of 32 to
  reach 2016, about 4 seconds.

## Measurements

The rules were checked against an emulation of the console's sound chip
(the one used for all sound measurements so far), driven directly, one
voice at a time, with a silent looping sample:

- 4 000 made-up cases: random envelope settings and gains, random
  counts at the first start, starts, settings changed during notes,
  releases held for random times, starts again; 113 466 311 outputs, 0
  differences.
- Every envelope and gain setting a `title-1` song starts a note with
  (356 different ones in the first 7 200 frames of each of the 27
  songs), each started at a random count, held 100 000 outputs and
  released: 35 742 400 outputs, 0 differences.
- The vectors: 0 differences.
- In the same 27 song runs (27 790 steps that start notes), no voice
  was released between a start and the check that took it, and no
  start came while a release was still handed.

## Not covered

- How the level scales the sample's outputs, how outputs between sample
  points are made, left and right volume, main volume, echo and noise.
- On which output a sample without a loop ends the note.
- Gain settings of 128 and over.
- On which output, within a step, a song's start or release reaches the
  voice (`songs.md` hands them per step).
- Sound effects.
- Other titles.
