# Sound-effect vectors

Synthetic vectors for `../../formats/sound-effects.md`. The song and the
effects are made up; none of it comes from a game. Written and
re-checked against the prose by the research side's generator. The same
song and effects, with their addresses moved to suit the game, were
also played by the console's own player with effects 0 to 5 started at
random on every voice, and every step matched (see "Measurements" in
`sound-effects.md`). Moving them changes no event.

## Input

- `song.hex`: one song block as in `../../formats/sound.md` ("Song
  data"): length, base address 12288 (`3000` hex, made up), then the
  song data. Tempo 100, effect tempo 200.
- `effects.hex`: one effect block as in `sound-effects.md`: length,
  base address 20480 (`5000` hex, made up), then the effect data, which
  starts with a table of 6 effects.
- `starts.tsv`: the effect starts: `after_step`, `effect`, `voice`. An
  effect in this list starts between step `after_step` and the next.

Before the song starts, every channel's echo flag is off and no
effect slot has been used; every effect sets its sample, volume and
envelope settings before its first note.

Both `.hex` files have 16 bytes per line, each line starting with the
position of its first byte in the block (hex). Data byte `i` is block
byte `i` + 4.

The song's channels:

- 0: sets the period; a loop of two notes.
- 1: echo on; vibrato with a delay; a loop.
- 2: echo off; a volume sweep; a loop of a note and a rest.
- 3: two notes, then the track ends.
- 4: echo on; a loop of notes and rests.
- 5: a pitch slide on every note; a loop.
- 6: echo turned on, off and on again between long notes; then a loop.
- 7: a loop.

The effects:

- 0: ends at once.
- 1: two notes, then ends.
- 2: fine tuning, transposition (set and added), vibrato turned on and
  off, a pitch slide (commands 8 and 9), a call played twice, a fixed
  length, two-byte lengths, then ends.
- 3: noise rate, noise on and off, echo on, off and on again, then
  ends (the end gives the voice the channel's echo, not the effect's).
- 4: a loop that goes on for ever.
- 5: a volume sweep and vibrato with a delay and a negative amount,
  then ends.

What the starts exercise: an effect ending on a voice whose channel's
echo flag is on (voice 4, voice 1) and off (voice 5); a channel turning
its voice's echo on and then off while the voice is taken (voice 6); a
track that ends while its voice is taken (voice 3); song notes, rests,
releases and effects while their voice is taken; starts over a playing
effect (voices 1, 3, 4 and 6, one of them two steps after the first
start); effect 0 stopping effects that go on for ever (voices 2 and 6);
noise and song-wide noise rate from an effect.

## Expected output

`events.tsv`: steps 1 to 700 from the song start. One row per event,
tab-separated: `step`, `voice` (`-` for song-wide), `event`, `values`.
Values are as they are at the end of the step; a start listed after
step `n` is written in step `n` (it comes after that step's own events).
Within a step, rows are in this order: `period` (when it is first known
or changes), then voices 0 to 7 (`release`, then `note` or the voice's
`pitch`, `volume`, `envelope`, then `start`, then `echo` and `noise`),
then the song-wide rows, then `beat` if the step is a beat and
`effect-beat` if it is an effect beat.

| Event | Values |
|---|---|
| `period` | the period that sets the gap after this step |
| `release` | — |
| `note` | pitch, sample, volume left, volume right, `e1`, `e2` (gain is always 127) |
| `pitch` | the voice's new pitch, without a new note (rests, effects) |
| `volume` | left, right (volume sweep) |
| `envelope` | `e1`, `e2` |
| `start` | the effect started on the voice after this step |
| `echo`, `noise` | `on` or `off` for that voice |
| `main-volume` | left, right |
| `noise-rate` | 0 to 31 |
| `echo-writing` | `on` or `off` |
| `beat` | — |
| `effect-beat` | — |

Only what reaches a voice is listed: a channel whose voice is taken
writes no rows for it (except `echo` and `noise`). A row is written
whenever a value is set, even when it is the same as before (for
example `noise off` at every effect start).
