# Sound effects

Titles: `title-1`

How sound effects are stored and played: the effect block and its
table, the effect tempo, and how an effect takes a voice from the song
while it plays and hands it back at its end. Effects are written in the
same items as song tracks (`songs.md`); this page gives only what
differs. Vectors: `../vectors/sound-effects/`.

All 2-byte numbers are stored low byte first. Words used as in
`songs.md`: step, beat, channel, voice, wait, note, release, item,
command, effects (pitch slide, vibrato, volume sweep).

## The effect block

The **effect block** is laid out like a song block (`sound.md`, "Song
data"): a 2-byte length `n`, a 2-byte **base address** `E`, then `n`
bytes of **effect data**. Where it starts in a supported image is in
`locators.md` ("Sound: sound-effect block"). An **effect address** `A`
is byte `A` − `E` of the effect data; every address inside effect data
(track starts, jumps, calls) is an effect address.

The effect data starts with the **effect table**: entry `k` is 2 bytes,
the effect address where effect `k`'s track starts. The table runs up to
the first byte of the lowest address it holds (so the first track starts
right after the table). In `title-1` there are 119 effects, 0 to 118.
Several entries can hold the same address; effect 0 is a track that
ends at once.

An effect's **track** is a run of items read as in `songs.md` ("Reading
a track", "Commands"). Effects in `title-1` use only commands 0 to 9,
13, 15, 16, 18 to 20, 22, 23, 25 to 27, 43 and 44, notes 1 to 56, and
rests. Effects 35 to 37 and 109 never end (their tracks loop with
command 3); every other track ends with command 0. No effect track reads outside the effect
data.

## Effect slots

The song's player has an **effect slot** for each voice 0 to 7. A slot
keeps the same things a channel keeps (`songs.md`, "Channels and
voices") and plays one effect at a time on its voice. While the slot
plays, the voice is **taken**: the music channel of the same number
does not hand it anything (see "While a voice is taken").

## The effect tempo

Byte 17 of the playing song's header is the **effect tempo** `t`. The
**effect sum** starts at 0 when the song starts. At the start of every
step, after the song's own tempo sum, `t` is added to it; if it is then
256 or more, 256 is taken off and the step is an **effect beat**.
Effect tracks move on only on effect beats; their effects (pitch slide,
vibrato, volume sweep) change things on every step, as a channel's do.
No command changes the effect tempo: the tempo commands (11, 12) change
the song's tempo, and no `title-1` effect uses them.

In `title-1` the songs do not all have the same effect tempo: one song's
is lower than the rest.

## Starting an effect

An effect is started on a voice `v` between two steps. The player takes
at most one start between two steps. Starting effect `k` on voice `v`:

- The voice becomes taken (if it isn't already). Its noise is turned
  off and its echo is turned off.
- The slot's position becomes effect `k`'s table entry, and its wait 2.
- Calls: none. Lengths: one-byte, fixed length off. Fine tuning and
  transposition: 0. Pitch slide, vibrato and volume sweep: off.
- Volume: 127 left and right (and the volume a volume sweep goes back
  to at a note: 127, 127). Envelope settings `e1`, `e2`: 0, 0. The
  slot's echo flag (see "The end of an effect"): off.
- Kept from whatever last played in the slot (an earlier effect on the
  same voice, even in an earlier song): the sample, and the effects'
  settings and counters.

Nothing is handed to the voice at the start: a note the song is playing
on it goes on sounding. Starting an effect on a voice whose slot is
already playing replaces that effect the same way. Starting effect 0
(which only ends) stops a playing effect: the voice is released on the
next effect beat and handed back on the one after (below).

So on the first effect beat after the start, the wait goes from 2 to 1
and the voice is **released** (the song's note on it, if any, fades
out); on the second, the slot reads its first items.

## Each step

Within a step (`songs.md`, "Steps and beats"), after the two sums and
the fade, the channels and slots go in this order: channel 0, then slot
0 if voice 0 is taken, then channel 1, then slot 1 if voice 1 is taken,
and so on to channel 7 and slot 7. A slot runs as a channel does
("Reading a track", "Effects"), on effect beats instead of beats, and
hands its voice what a channel would: notes, releases, rests' pitch 0,
pitches and volumes from its effects. A slot whose voice is not taken
does nothing.

Commands in an effect act on the slot as they would on a channel, with
these points:

- Commands 22 and 23 (echo on/off) and 26 and 27 (noise on/off) turn
  echo or noise on or off for the slot's voice; 22 and 23 also set the
  slot's echo flag.
- A command that changes a song-wide setting changes the song's: in
  `title-1` effects, only command 25 (noise rate).
- Notes 93 to 127 take their pitch from the song's data (as in
  `songs.md`), not the effect data; no `title-1` effect has such notes.

## While a voice is taken

The music channel of a taken voice goes on as usual: its track moves on
on beats, its commands take effect (sample, volume, envelope settings,
lengths, calls, effects turned on and off, song-wide settings), and its
effects count. But it hands the voice nothing:

- No release (its wait reaching 1, a rest, or its track's end), and no
  pitch 0 at a rest.
- At a note: nothing is handed and the voice doesn't start; the
  channel's current pitch stays as it was (the note's pitch is not
  kept); its pitch slide, vibrato and volume sweep don't restart, and
  the volume sweep doesn't go back to its saved volume. The note's
  length counts as usual.
- Its effects change its current pitch and volume as usual, but neither
  is handed to the voice.

Two things still reach the voice: commands 22 and 23 turn the voice's
echo on or off (and set the channel's echo flag), and 26 and 27 turn its
noise on or off. Presets (commands 33 to 37) from a channel whose voice
is taken are not covered; no `title-1` song played with effects met one.

## The end of an effect

When a slot reads command 0, the effect ends:

- The voice is released.
- The voice is no longer taken: from the next step its music channel
  hands it things again.
- The voice's noise is turned off. Its echo is turned on if the music
  channel's echo flag is on, and off otherwise. A channel's echo flag is
  set by its own commands 22 and 23 and is not cleared when a song
  starts, so it can come from an earlier song.

The voice keeps the effect's sample, volume and envelope settings until
the channel's next note hands its own. Before that note, the channel's
pitch slide or vibrato, if on, hands the voice the channel's current
pitch, which may be from before the effect (see above); its volume
sweep hands its volume.

A slot that has ended keeps its settings (sample, effects' settings and
counters) for the next effect on that voice.

## When a song starts

When a song starts, no voice is taken: every voice belongs to its music
channel, and effects that were playing don't go on. The slots' settings
are kept.

## Effects in the game

The game starts effects only on voices 4 to 7, so the song's channels 0
to 3 are never interrupted. Which effect, on which voice, each event in
the game starts is not covered.

## Example

From `../vectors/sound-effects/`: the song has tempo 100 and effect
tempo 200; channel 4's echo flag is on. Effect 1 is: sample 2, volume
120 and 110, envelope settings 143 and 224, note 49 length 2, note 52
length 2, command 0. It is started on voice 4 after step 20.

| Step | Beat | Effect beat | Voice 4 |
|---|---|---|---|
| 20 | | yes | effect 1 starts after the step: echo off, noise off; slot wait 2 |
| 21 | yes | yes | slot wait 1: released |
| 22 | | yes | slot wait 0: three commands, then note 49: pitch 8192, sample 2, volume 120 and 110, envelope 143 and 224; wait 2 |
| 23 | | | (not an effect beat) |
| 24 | yes | yes | wait 1: released |
| 25 | | yes | note 52: pitch 9742; wait 2 |
| 26 | yes | yes | wait 1: released |
| 27 | | yes | command 0: released; voice 4 is the channel's again: noise off, echo on |
| 29 | yes | yes | channel 4's rest: released, pitch 0 |
| 34 | yes | yes | channel 4's next note: pitch 5793, its sample, volume and envelope |

## Measurements

- The effect block in the image was compared with the console's sound
  memory after start-up: the same bytes at the base address.
- Effects were started through the console's own player in the first
  level, from just before its song starts, and every step was compared
  with the rules above (which voices started and released notes, every
  value handed to a voice, echo and noise on or off for every voice,
  song-wide settings), at the end of the step. 30 runs of 7 200 frames
  or until the level restarted: 213 557 steps, 16 870 effect starts,
  65 236 effect notes, 0 differences.
  - 8 runs on song 0 with effects started at random on any voice,
    1 to 40 frames apart (924 of the 952 pairs of effect and voice;
    all 952 over all runs);
  - 7 runs with other songs in the first level (songs 1, 5, 9, 11, 12,
    15 and 23, including the one with the lower effect tempo);
  - 11 runs of play with the game starting its own effects;
  - 4 runs of the made-up song and effects in
    `../vectors/sound-effects/`, moved to suit the game, with effects 0
    to 5 started at random on every voice.
- Cases met in these runs: 7 796 starts over a playing effect, 2 255
  starts on a voice whose track had ended, 18 866 song notes and 23 998
  song releases while their voice was taken, 382 echo commands from a
  channel whose voice was taken, 4 song tracks ending while taken, and
  9 023 effect ends (6 552 giving the echo back on).

## Not covered

- Which effect, on which voice, each event in the game starts, and when.
- How long after the game asks for an effect it starts (between which
  steps).
- Presets (commands 33 to 37) from a channel whose voice is taken, and
  commands no `title-1` effect uses, in an effect.
- Other titles.
