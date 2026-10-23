# Which song plays where

Titles: `title-1`

Which of the game's songs plays in each place the first level leads to
and on the level map, and when one song gives way to the next. Songs are
numbered as in `../formats/sound.md` ("Song data"); song `s` goes with
sample set `s`. What a song contains is in `../formats/songs.md`.

Frames are video frames at 60 Hz, counted from a named frame of the
picture, because the sound changes while the screen is black and no
trace has frames there. "Black" is the first frame on which the picture
is fully black (brightness 0, scale of `level-end.md`).

## The songs

| Place | Song |
|---|---|
| The first level | 0 |
| The scene behind the first level's floor exit | 25 |
| The room behind the first level's upper exit | 23 |
| The areas behind the first level's two wall exits | 16 (the same song in both) |
| The level map | 12 |
| The tune after finishing the first level | 26 |
| The tune when the lead alone is knocked out | 17 |
| A screen before a game starts | 9 (`../formats/sound.md`, measurements) |

Coming back to the first level from the scene, the room or a wall area
plays song 0 again, from its start.

## A change of song

Every change follows the same three steps:

1. **Cut.** The song that was playing stops; the sound is silent.
2. **Silence.** Nothing is heard.
3. **Start.** The new song starts from its first note.

The silent time between the cut and the start depends only on the new
song:

| New song | Frames from cut to start |
|---|---|
| 0 | 70 |
| 12 | 47 (after finishing a level that was finished before) |
| 16 | 48 |
| 17 | 30 |
| 23 | 31 |
| 25 | 32 |
| 26 | 44 |

The song that is playing is not cut before the event that follows; for
example the first level's song keeps playing through the walk-out and
the fade of the level's end (`level-end.md`).

## When the cut comes

- **Entering a level from the map.** On the frame `P` of the entering
  press (`level-map.md`, "Pressing at a stop"): the map is black from
  `P` + 30, the map's song is cut on `P` + 36 and the level's song
  starts on `P` + 106 (the first level: song 0, 70 frames after the
  cut). The level's first frame comes later (`level-start.md`).
- **Leaving the first level through a wall exit.** The picture is black
  on `B`. The song is cut on `B` + 6, and the song of the area behind
  the exit (16) starts after its silent time. Measured at the first wall
  exit and the second, one run each.
- **Coming back to the first level** from the scene, the room or an area
  behind a wall exit. The picture is black on `B`; the song is cut on
  `B` + 7 and song 0 starts after its silent time (70 frames later).
  One run from each of the four. The cut on arriving in the scene and
  the room was not measured.
- **Finishing a level for the first time.** The level's last frame is
  `E` (`level-end.md`, "The fade": brightness 0). The level's song is cut
  3 frames after `E`. The finishing tune (song 26) starts, after its
  silent time, on `M` − 2, with `M` the map's first frame (49 frames
  after `E`, `level-map.md`, "Up to the map"). It is heard for 154
  frames, and so is silent from `M` + 152. The map's song starts on
  `M` + 212 (first heard there), after 60 silent frames. Nothing is heard in between,
  so no cut of the map's song can be told apart there.
- **Finishing a level that was finished before.** The song is cut 3
  frames after `E` as above, but there is no tune: the map's song
  starts on `M` − 3, with `M` 53 frames
  after `E`.
- **Knocked out as the lead alone.** `H` is the lead's hit frame
  (`knock-out.md`): the level's song is cut on `H` + 9 or `H` + 10 and
  song 17 starts
  on `H` + 39 or `H` + 40, in two runs. What follows is not covered.

## The still frames on the map

After a first finish the map stands still on its frames 163 to 212
(`level-map.md`, "Still frames"). The finishing tune is silent from
frame 152 and the map's song is first heard on frame 212, so the silent
frames are 152 to 211 (60 frames). The still frames 163 to 211 (49 of
them) are silent; the last still frame, 212, is the song's first frame.
After a finish of a level that was finished before the map's song is
started while the screen is black, and there are no still frames.

## Measurements

Found by listening: the song heard on each video frame, taken from the
sound output, and the frames on which it stopped and started. A setup
method was used to reach the situations where noted.

- Which song plays: heard in the first level, the scene, the room and
  both wall areas (in the places themselves), on the map, and for the
  tunes.
- The first level's end: from a situation shortly before the marker with
  Right held, with and without the level counting as finished before, in
  one run each. The tune's sound stopped on `M` + 151 and the map's song
  started on `M` + 212.
- Entering a level from the map: the cut on `P` + 36 in every run (all
  the entering runs).
- Leaving through a wall exit and coming back: one run each, as above.
- Knocked out as the lead alone: two runs.

## Not covered

- Other levels' songs, and other titles.
- The silent times for songs not in the table, and what decides them.
- Songs on the level map for other maps, and screens reached from the
  map other than levels.
- Songs after a knock-out of the lead with a second character, of the
  second character, or of the last character; what follows song 17.
- The finishing tune with two characters at the end.
- The sound effects.
