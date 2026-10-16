# Song vectors

Synthetic vectors for `../../formats/songs.md`. The song is made up;
none of it comes from a game. Written and re-checked against the prose
by the research side's generator. The same song, with its addresses
moved to suit the game, was also played by the console's own song
player, and every step matched (see "Measurements" in `songs.md`).
Moving the song changes no event: no note takes its pitch from bytes
that hold addresses.

## Input

`block.hex`: one song block as in `../../formats/sound.md` ("Song
data"): length, base address 12288 (`3000` hex, made up), then the
song data. 16
bytes per line, each line starting with the position of its first byte
in the block (hex). Song data byte `i` is block byte `i` + 4.

What the tracks exercise:

- Channel 0: period and main volume; notes and a rest with one-byte
  lengths 0, 1, 2, 3 and 4; transposition (set, added, and wrapping past
  127); fine tuning up and down, and the sample command clearing it;
  two-byte lengths 0 and 3; a fixed length in both modes; notes 62, 70,
  105 and 127 (pitch 0, and pitch from the song's first bytes); tempo
  set and added; a call played 3 times.
- Channel 1: vibrato with and without a delay, with a negative amount,
  and turned off and on again in a rest (its counters go on); every
  kind of pitch slide (commands 8, 9, 38, 39, with and without a delay,
  and delay 255); a volume sweep, with a volume command during it.
- Channel 2: echo feedback, volume and filter values; echo and noise on
  and off for the voice; noise rate; all five presets kept, and presets
  handed to the voice during a note and in a rest (when a note follows
  on the same beat, the note's values are the ones at the end of the
  step).
- Channel 3: branch value and a branch table; calls inside calls.
- Channel 4: a loop that goes on for ever (command 3).
- Channel 5: a fade, after rests of 200 and 100 beats.
- Channel 6: two-byte lengths 256, 1 and 257.
- Channel 7: a track that ends at once.

## Expected output

`events.tsv`: steps 1 to 900 from the song start. One row per event,
tab-separated: `step`, `voice` (`-` for song-wide), `event`, `values`.
Values are as they are at the end of the step. Within a step, rows are
in this order: `period` (when it is first known or changes), then voices
0 to 7 (`release`, then `note` or the voice's `pitch`, `volume`,
`envelope`, then `echo` and `noise`), then the song-wide rows, then
`beat` if the step is a beat.

| Event | Values |
|---|---|
| `period` | the period that sets the gap after this step |
| `release` | — |
| `note` | pitch, sample, volume left, volume right, `e1`, `e2` (gain is always 127) |
| `pitch` | the voice's new pitch, without a new note (rests, effects) |
| `volume` | left, right (volume sweep, presets) |
| `envelope` | `e1`, `e2` (presets) |
| `echo`, `noise` | `on` or `off` for that voice |
| `main-volume`, `echo-volume` | left, right |
| `echo-feedback` | the value |
| `echo-filter` | the 8 values |
| `noise-rate` | 0 to 31 |
| `echo-writing` | `on` or `off` |
| `beat` | — |

A row is written whenever the song sets a value, even when it is the
same as before (for example `echo-volume 0 0` on every beat of a fade
whose echo volume is already 0).
