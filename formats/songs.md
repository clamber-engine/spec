# Songs

Titles: `title-1`

What a song's data says: eight tracks of notes, rests and commands,
played in steps and beats, and what each channel hands its voice (which
sample, at what pitch, how loud, with which envelope settings) and when.
Where the data is and which samples a song has are in `sound.md`; how a
voice turns those settings into sound is in `voices.md` (so far its
level; the rest is not covered). Vectors: `../vectors/songs/`.

All 2-byte numbers are stored low byte first. An **address** is a song
address as in `sound.md` ("Song data"): address `A` is byte `A` − `D` of
the song data. Numbers called signed are bytes read as −128 to 127 (128
to 255 are −128 to −1).

## Header

| Bytes | Meaning |
|---|---|
| 0–15 | Track addresses for channels 0 to 7, 2 bytes each |
| 16 | Starting **tempo** `T` |
| 17 | The **effect tempo**, used by sound effects (`sound-effects.md`) |

The rest of the song data holds the tracks; tracks may jump into and
call each other's data, and may start anywhere after the header.

## Steps and beats

A song plays in **steps**. The time from one step to the next is
4 × `P` audio outputs (an output as in `sound.md`; 32 000 outputs make a
second), where `P` is the **period**: 60 when the song starts, then as
set by command 42. The gap after a step uses the period as it is at the
end of that step. With `P` = 100, which every `title-1` song sets on its
first beat, there are 80 steps a second.

The **tempo sum** starts at 0. At the start of each step, `T` is added
to it; if the sum is then 256 or more, 256 is taken off and the step is
a **beat**. So a fraction `T` / 256 of the steps are beats. Tracks move
on only on beats; the effects below change things on every step.

Within a step, in order:

1. The tempo sum, as above.
2. On a beat, the fade (see "Song-wide settings").
3. Channels 0 to 7 in turn: on a beat, the channel's track moves on
   ("Reading a track"); then the channel's effects run ("Effects").

A command that changes the tempo takes effect from the next step. When
two channels change the same song-wide setting in one step, the
higher-numbered channel's value is the one that holds.

## Channels and voices

There are 8 channels. Channel `i` plays its track on **voice** `i`
(while a sound effect has taken the voice, the channel hands it nothing:
`sound-effects.md`). A voice plays one sample at a time. At a note it is handed:

- the **sample** (a sample number as in `sound.md`);
- the **pitch** `p`: the voice plays its sample at `p` / 4096 sample
  outputs per audio output, so 4096 plays it as stored;
- the **volume**, left and right, each a byte;
- two **envelope settings** `e1` and `e2`, each a byte, and a **gain
  setting** of 127, which the voice uses instead of the envelope when
  the top bit of `e1` is 0 (as in some notes of song 11).

The voice then **starts** the note: the sample from its beginning. A
**release** lets the note go: it fades out. How the envelope settings,
the gain, starts and releases set the voice's level is in `voices.md`.

What a channel keeps:

| | When the song starts |
|---|---|
| Position in the data | its track address |
| **Wait** (beats until it next reads its track) | 1 |
| Calls (see command 4) | none |
| Length mode | one-byte lengths, fixed length off |
| Fine tuning, transposition | 0 |
| Pitch, vibrato and volume effects | all off |
| Sample, volume, envelope settings | as left by what played before |
| Effect settings and counters | as left by what played before |

Every `title-1` song sets a channel's sample, volume and envelope
settings before that channel's first note, except one: in song 23,
channel 2's first note comes before its envelope settings.

## Reading a track

A track is a run of **items**. The first byte `b` of an item says what
it is:

| `b` | Item |
|---|---|
| 0 to 48 | Command `b`, followed by its arguments ("Commands") |
| 49 to 127 | Not used in `title-1`; not covered |
| 128 | Rest, followed by a length (none with a fixed length) |
| 129 to 255 | Note `b` − 128 (1 to 127), followed by a length (likewise) |

On a beat, a channel whose track has not ended takes 1 off its wait:

- If the wait is now 1, the voice is released.
- If the wait is now 0, the channel reads items from its position:
  every command it meets takes effect at once, in order, on this beat,
  and the channel goes on to the next item; it stops after a note or a
  rest, which sets the wait to the item's length, or at command 0, which
  ends the track.

So a note of length `n` of 2 or more sounds for `n` − 1 beats, is
released, and the channel's next note or rest comes one beat later. A
note of length 1 is not released before the next item. A rest releases
the voice at once (releasing a voice already released changes nothing).

### Lengths

A length is a number of beats.

- **One-byte lengths** (from the song start, and after command 44): one
  byte `n`; 0 counts as 1.
- **Two-byte lengths** (after command 43): a high byte `h`, then a low
  byte `l`; the length is 256`h` + `l`, and 0 counts as 1.
- **Fixed length** (after command 6, until command 7): notes and rests
  have no length bytes; each lasts the fixed length.

### Notes

A note with number `k` is played as **note** `m` = (`k` + transposition)
mod 128. Its pitch `p` is:

| `m` | `p` |
|---|---|
| 1 to 60 | 4096 × 2^((`m` − 37) / 12), rounded to the nearest whole number; but 1626 for `m` = 21 and 2436 for `m` = 28 (one more than rounding gives) |
| 61 | 16 383 |
| 0, 62 to 92 | 0 |
| 93 to 127 | The 2-byte number at bytes 2`m` − 186 and 2`m` − 185 of the song data |

So note 37 is pitch 4096 and each note is a semitone above the one
before; notes 1, 13, 25, 37, 49 are 512, 1024, 2048, 4096, 8192. Note
`m` from 93 up takes its pitch from the song's own first bytes (the
header and the start of the track data): song 5 has such notes, and
song 8 has notes 62 and over with pitch 0.

Then the fine tuning `f` (signed): if `f` is not 0, `d` = ⌊`p` × |`f`| /
1024⌋, and the pitch is `p` + `d` when `f` is positive and `p` − `d`
when it is negative, in either case mod 65 536.

At the note, the channel's **current pitch** becomes that pitch, and
the voice is handed the channel's sample, that pitch, the channel's
volume and envelope settings, and gain 127, and starts the note. The
effects that are on restart ("Effects").

### Rests

A rest releases the voice and sets the voice's pitch to 0. The channel's
current pitch stays as it was.

## Commands

Arguments are bytes, in order after the command byte; `a` is a 2-byte
address. A command needs no beat of its own: commands are read on the
same beat as the note or rest after them.

| Command | Arguments | What it does |
|---|---|---|
| 0 | — | Ends the track: the voice is released and the channel reads no more |
| 1 | `s` | Sample `s`; fine tuning becomes 0 |
| 2 | `l`, `r` | Volume left `l`, right `r` |
| 3 | `a` | Go on at `a` |
| 4 | `n`, `a` | Call: go on at `a`; see below |
| 5 | — | Return from a call; see below |
| 6 | `n` (one-byte mode) or `h`, `l` (two-byte mode) | Fixed length on; see below |
| 7 | — | Fixed length off |
| 8 | `w`, `r`, `n`, `d`, `f` | Pitch slide on, amount `d` (signed) |
| 9 | `w`, `r`, `n`, `d`, `f` | Pitch slide on, amount −`d` |
| 10 | — | Pitch slide off |
| 11 | `t` | Tempo `t` |
| 12 | `t` | Tempo + `t`, mod 256 |
| 13 | `N`, `r`, `d` | Vibrato on, delay 0 |
| 14 | — | Vibrato off |
| 15 | `N`, `r`, `d`, `w` | Vibrato on, delay `w` |
| 16 | `e1`, `e2` | Envelope settings |
| 17 | `l`, `r` | Main volume `l`, `r` (at once) |
| 18 | `f` | Fine tuning `f` (signed) |
| 19 | `t` | Transposition `t` |
| 20 | `t` | Transposition + `t`, mod 256 |
| 21 | `b`, `l`, `r` | Echo feedback `b`, echo volume `l`, `r`; echo writing on, noise rate 0 |
| 22 | — | Echo on for this voice |
| 23 | — | Echo off for this voice |
| 24 | 8 bytes | The 8 echo filter values |
| 25 | `q` | Noise rate `q` (0 to 31; other values not covered) |
| 26 | — | Noise on for this voice |
| 27 | — | Noise off for this voice |
| 28 to 32 | `l`, `r`, `e1`, `e2` | Keeps them as preset `b` − 28 (0 to 4) |
| 33 to 37 | — | The voice's volume and envelope settings become preset `b` − 33's, at once |
| 38 | `w`, `r`, `f`, `d` | Pitch slide on, `n` = 2`f` mod 256, amount −`d` |
| 39 | `w`, `r`, `f`, `d` | Pitch slide on, `n` = 2`f` mod 256, amount `d` |
| 40 | `s`, `l`, `r` | Sample `s` and volume `l`, `r`; fine tuning becomes 0 |
| 41 | `s` | Fade speed `s` |
| 42 | `P` | Period `P` |
| 43 | — | Two-byte lengths |
| 44 | — | One-byte lengths |
| 45 | table | Go on at entry `v` of the table of addresses that follows, `v` the branch value |
| 46 | `v` | Branch value `v` |
| 47 | `N`, `r`, `d`, `w` | Volume sweep on; the volume now is kept as its **saved volume** |
| 48 | — | Volume sweep off |

Here "−`d`" is `d` (signed) negated, except that −(−128) is −128.
No `title-1` song uses commands 13, 25 to 39, 41 or 45; the vectors do.

Settings for the voice (sample, volume, envelope settings, fine tuning,
transposition) are used from the channel's next note; only commands 17
and 33 to 37 change what is sounding at once, and the effects below.

**Calls.** Command 4 `n`, `a` goes on at `a`. At the matching command
5, the channel goes back to `a` until the data from `a` has been played
`n` times in all (`n` = 0 means 256), and then goes on after the
command 4. Calls can be inside calls; `title-1` songs go 4 deep at most.
Deeper than 8 is not covered.

**Fixed length.** In one-byte mode, command 6 `n` sets the fixed
length's low byte to `n`; in two-byte mode, command 6 `h`, `l` sets its
high byte to `h` and low byte to `l`. The fixed length is 256 × high +
low. Command 7 sets both to 0. While the low byte is 0, notes and rests
have length bytes as usual. (No `title-1` song uses command 6 in
two-byte mode.)

**Branch value.** Command 45's table has an entry for each value the
song can have. The branch value is song-wide; what it is before a
command 46 is set by the game and not covered.

**Presets.** Commands 28 to 32 keep four values; commands 33 to 37 hand
them to the voice at once (left and right volume, `e1`, `e2`). The
channel's own volume and envelope settings don't change, so the next
note uses those again.

## Effects

Each channel has three effects, each on or off: the **pitch slide**, the
**vibrato** and the **volume sweep**. The commands that turn an effect
on set its settings. At every note, each effect that is on restarts its
counters, as below. On every step, beat or not, after the channel's
track has moved on (on a beat), each effect that is on can make a
**change**, in the order pitch slide, vibrato, volume sweep.

A change to the pitch adds to the channel's current pitch (mod 65 536)
and hands the voice the new pitch. A change to the volume adds to the
channel's left and right volume (each mod 256) and hands the voice the
new volume. Neither restarts the note.

Times below count from the step `S` of the note: the note's own step
can already have a change. A count of 0 in `r`, `n` or `N` (other than
in `h`, below) means 256. The steps given are for a channel whose track
goes on; see the end of this section for one that has ended.

- **Pitch slide** (`w`, `r`, `n`, amount `d`, `f`): `n` changes. With
  `w` = 0, they come on steps `S` + `r` − 1, `S` + 2`r` − 1, … ; with
  `w` from 1 to 254, on steps `S` + `w` − 1, `S` + `w` − 1 + `r`, … ;
  with `w` = 255, never. The first `f` changes take `d` off the pitch;
  the rest add `d`. (Commands 38 and 39, with `n` = 2`f`, go `f` steps
  one way and `f` back.)
- **Vibrato** (`N`, `r`, `d`, `w`): at the note, `d` becomes |`d`| (but
  −128 stays −128). No changes on steps `S` to `S` + `w` − 1; then
  changes on steps `S` + `w` + `r` − 1, `S` + `w` + 2`r` − 1, … , for as
  long as the vibrato is on. The first `h` changes add `d`, where `h` =
  ⌊`N` / 2⌋ with `N` as stored (0 to 255), and `h` = 0 means 256; then
  the sign turns every `N` changes: the next `N` take `d` off, the next
  `N` add it, and so on.
- **Volume sweep** (`N`, `r`, `d`, `w`): at the note, the voice gets the
  channel's volume as it is (which may still be swept from the note
  before), and then the channel's volume goes back to the saved volume.
  Changes come as for the vibrato, on the same steps and with the same
  signs, each adding to both left and right.

An effect's counters only restart at a note. An effect turned on, or
given new settings, between notes (for example in a rest) uses the new
settings at once (a vibrato or sweep amount `d` with its sign as given),
but its counters go on from where they stopped, with the channel's
current pitch and volume. If the effect hasn't been on at a note on that
channel since the song started, its counters are as left by what played
before (not covered; `title-1` songs do this in places, at the start
and in rests).

When a track has ended, its channel's effects don't run on the step it
ended, nor on any later beat, but they still run on the steps that are
not beats; their counters then move on those steps only.

## Song-wide settings

| | When the song starts |
|---|---|
| Period | 60 |
| Tempo, tempo sum | header byte 16, 0 |
| Main volume | 127, 127 |
| Echo volume, echo feedback | 0, 0 and 0 |
| Echo writing, noise rate | off, 0 |
| Echo and noise for each voice | off |
| Fade amount, fade speed | 0, 0 |
| Echo filter values, branch value | not covered |

The **fade**: on a beat, before the channels, if the fade speed `s` is
not 0 and the fade amount `A` is below 255, `A` becomes the smaller of
`A` + `s` and 255, and the main volume becomes `L` − `A` and `R` − `A`
(each 0 if that is below 0), where `L`, `R` are the main volume last
set by command 17 (127, 127 before one); the echo volume likewise from
the values last set by command 21 (0, 0 before one). Command 17 sets the
main volume at once, fade or not.

Command 25 keeps echo writing as it is; command 21 turns it on.

## Example

A made-up track for channel 0, one-byte lengths, tempo 128, so the
beats are steps 2, 4, 6, … (the other channels' tracks end at once):

```
1 3          sample 3
2 64 64      volume 64, 64
16 143 224   envelope settings
13 4 2 10    vibrato on: N 4, r 2, d 10
165 2        note 37, length 2
128 1        rest, length 1
0            end
```

| Step | Beat | What happens to voice 0 |
|---|---|---|
| 1 | | nothing (the wait is still 1) |
| 2 | yes | wait 0: the four commands, then note 37 starts: pitch 4096, sample 3, volume 64, 64, envelope settings 143, 224, gain 127; wait 2. Vibrato restarts: `h` = 2 |
| 3 | | vibrato change 1: pitch 4106 |
| 4 | yes | wait 1: released |
| 5 | | change 2: pitch 4116; the sign turns |
| 6 | yes | wait 0: the rest: released, pitch 0; wait 1 |
| 7 | | change 3: pitch 4106 (current pitch 4116 − 10) |
| 8 | yes | wait 0: end: released; no effects this step |
| 9, 10 | | (step 10 is a beat: no effects) |
| 11 | | change 4: pitch 4096 |
| 15, 19 | | changes 5 and 6: 4086, 4076; then the sign turns |

## Measurements

- Every `title-1` song (0 to 26) was played in the first level with the
  game changed to play that song with its sample set and to start no
  sound effects, for 36 000 frames (about 47 900 steps, 10 minutes).
  The 21 songs that loop went round at least 3 times; the other 6 end. On every step, which
  voices started and released notes, and every value the rules above
  hand the voices and every song-wide setting, matched the game's at
  the end of the step: 0 differences. Steps where an effect's counters
  were left from before the song (above) were left out for that voice's
  pitch and volume; song 23's note before its envelope was compared
  without the envelope.
- The made-up song in `../vectors/songs/` (every command 0 to 48), with
  its addresses moved to suit the game, was played the same way: 1 527
  steps, 0 differences.
- Step length: with period 100, 3 198 732 outputs in 7 997 steps
  (399.99 per step).

## Not covered

- How a voice makes sound from its settings, apart from its level
  (`voices.md`): how outputs between sample points are made, how left
  and right volume, main volume, echo (feedback, filter, volume, delay,
  echo writing) and noise sound, and pitches of 16 384 and over
  (reached only by song 5's notes above 92).
- What the game does with songs: when it starts, stops or changes them,
  what it plays before a song starts, the branch value it sets, a mono
  sound option, and the first step's timing.
- Items 49 to 127, periods of 0, calls deeper than 8, and command 45
  with a branch value past its table.
- Other titles.
