# Finishing a level

Titles: `title-1`

A level is finished by walking past its **end marker**. From then on the
player has no control: the lead walks right on its own, the picture
fades to black, and the level is over. This file covers when the
marker is passed, how the characters move afterwards, when the fade
starts and how long it lasts.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`;
`V` is the view's position there (the level x of the picture's left
column). Vertical positions are **level y** as in `view-vertical.md`:
they grow downward, measured from the top of the level. The lead's
**feet line** `F` is as in `view-vertical.md`. "Right", "Left", "Up",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `U`, `D`,
`J`, `A` of the scenario format.

Measured on the end of the first level with the first character
leading, alone and (one run) with the second character following. The
lead reached the marker by inputs only: walking, running, rolling,
landing from a jump, about 100 runs of small steps from standing that
ended at positions 1/8 to 2 px apart, and standing past the marker. For
the marker's range the marker was also moved, up to 9 px up or down and
up to 400 px left, with the lead walking or standing still; for the
walk and the fade it was moved so that the lead passed it with the view
at the level's end and 96 px before it. About 240 runs in all. With
the second character leading and the first following, 320 runs with
random inputs, 78 runs of small steps and 10 with the marker moved ("The
second character leading").

## The end marker

A level's placement list (`../formats/placements.md`) places its end
marker with an entry. The marker does not move and is not drawn. Its
**position** (`xm`, `ym`) is the entry's level x and level y (see
"Positions" there). An entry places an end marker when its reference's
kind is `end-marker` ("Kinds" there).

The first level's room has an end marker too: it is the room's way out
("In the first level's room").

## When the marker is passed

Using whole pixels, let `X` be the lead's `x` rounded down.

The lead passes the marker on frame `n` when all of these hold **at the
end of frame `n`**, after the lead has moved on that frame:

1. Right was held on frame `n` (with or without other inputs; see
   below);
2. the lead is on the ground (`ground` is 1);
3. `X` ≥ `xm`;
4. `F` − `ym` is from −4 to 4 (the lead's feet line is at most 4 px
   above or below the marker's position).

There is no limit on the right: a lead standing 128 px right of the
marker passed it on the first frame Right was held.

Call the next frame, `n` + 1, **`T`**: the first frame of the walk-out.

Things that do **not** pass the marker:

- Going past it with no direction held, for example sliding to a stop
  after Right is released: the lead stood 7 px past the marker without
  passing it, then passed it on the first frame it held Right.
- Holding only Up, or only Left, standing past the marker.
- Being in the air: a lead jumping past the marker with Right held
  passed it on the frame it landed (`n` = the landing frame).
- Passing above or below the marker by more than 4 px: a lead walking
  on a higher floor over the marker, Right held, never passed it.

Right held together with Up, Jump (a jump already held through a
landing) or Action (running) counts. A roll counts: the lead passed the
marker on a frame of a roll with Right held.

The boundaries were measured as follows: frames ending 0.13 px left of
`xm` did not pass the marker, and frames ending 0.26 px right of it did;
nothing in between was measured. `F` − `ym` of −4 and 4 passed it and −5
and 5 did not, with the lead's feet on whole px.

## The walk-out

From `T` on, nothing held has any effect: holding Left, Jump and
Action, or Down and Action, or nothing, on every frame from `T` gave
exactly the same frames as holding Right.

On every frame from `T` on:

- **Speed.** `vx` is +2 (512/256) from `T` on, whatever it was before
  (2.89 during a roll, 3 running, 0.25 in a small step).
- **Moving.** On `T` the lead doesn't move. From `T` + 1 on, `x` grows
  by exactly 2 every frame.
- **Height.** From `T` + 1 on `ground` is 0, and the lead goes up by
  1/8 px (32/256) every frame, until the fade starts (next section);
  from then on it goes neither up nor down. This takes no account of
  the ground: the lead passes through walls and doesn't follow the
  floor. At the first level's end the lead walked through the wall that
  ends the level on the right.
- **Facing.** A lead facing right keeps facing right. A lead facing
  left on `T` (one run: a turn to the right was in progress when the
  marker was passed) stopped that turn on `T` and started a new turn
  (`ground-speed.md`, 8 frames) on `T` + 1; its facing became right on
  `T` + 5. The lead's pictures are those of walking.
- **The view stops.** `V` keeps the value it had on `T` until the level
  is over.
- **Drawn behind the scenery.** From `T` on, the characters are drawn
  behind the level's scenery: at the first level's end they disappear
  behind the rock face on the right as they walk on.

### The other character

In the one run with both characters, the other character went on
following the lead (`following.md`): it moved right by exactly 2 px on
every frame from `T` on, staying 35.83 px behind the lead (its distance
on `T`), and its height followed the lead's earlier positions along the
trail, going up as the lead had. It was drawn behind the scenery too.

## The fade

The fade starts on the frame after the first frame from `T` on that
ends with

    X − ⌊V⌋ ≥ 240

that is, with the lead 240 px or more right of the picture's left edge
(16 px from its right edge, the picture being 256 px wide). Call the
first frame of the fade **`F0`**. It depends only on where the picture
is, not on the marker: moving the marker left or right changed `T` but
left `F0` on the same `x` of the lead.

The **brightness** of the whole picture is on a scale from 15 (full) to
0 (black), and is 15 until the fade. On `F0` it becomes 14, and it goes
down by 1 every second frame: frames `F0` + 2`j` and `F0` + 2`j` + 1
have brightness 14 − `j`. It reaches 0 on `F0` + 28.

`F0` + 28 is the last frame of the level. The level map follows
(`level-map.md`, "After finishing a level"). Who is there when a level
begins again is in `following.md`.

The lead's rise stops on `F0`: its height on `F0` is that of `F0` − 1.

## The second character leading

Everything above holds when the second character is the lead (after a
swap, `swap.md`) and the first character follows it. "When the marker is
passed" uses the second character's position, ground and the input
held; the walk-out and the fade are as above, frame for frame. In
particular `vx` is +2 from `T` on and `x` grows by 2 a frame from
`T` + 1, although the second character walks at 2.25
(`second-character.md`). `F0` + 28 is the last frame of the level, as
above. Right held together with Down passes the marker too, as it does
in the room.

The first character went on following the lead from `T` on. Its
movement during the walk-out is not covered.

### Checks with the second character leading

At the first level's end, with the second character leading and both
characters standing left of the marker first:

- **Random inputs.** 320 runs of random inputs biased to the right
  (Right alone and with Action, Jump, Up or Down; Action, Down, Down with
  Action, Jump, Left, Left with Action, nothing), held through the walk-out
  and the fade too, for up to 1 500 frames. Every frame before `T`
  (96 343 in all) was judged by "When the marker is passed", including
  every frame of the 35 runs that never passed it (34 ended on the
  higher floor over the marker, one left of it). In each of the 285 runs
  that passed it, it was passed on exactly the frame the rules give,
  with `X` − `xm` from 0 to 15 on that frame, 19 times on the landing
  frame of a jump or fall, 7 times with Down and Right held. Every frame from `T` to `F0` + 28 (17 173,
  with other inputs than Right alone held on 11 482 of them; one run
  ran out of frames 19 frames after `T`) was as in "The walk-out" and
  "The fade". No frame was different. In 4 runs played on, no logic
  frame followed `F0` + 28 in the level.
- **The marker's range.** 78 runs of small steps from standing near the
  marker: frames ending 0.26 px left of `xm` with Right held didn't pass
  it, and frames ending 0.67 px right of it did. With the marker moved
  up or down by 3 or 4 px the lead passed it, and by 5, 6 or 7 px it
  didn't.

## In the first level's room

The room the first level's upper exit leads to (`exits.md`) has an end
marker of its own: passing it is how the room is left. Everything above
holds for it, with the picture's left edge always 0 (`level-ends.md`,
"In the first level's room"), except for the fade's start, the last
frame and what follows:

- **The fade** starts on the frame after the first frame from `T` + 1
  on that ends with `X` ≥ 240. When the lead's `X` is less than 240 on
  `T`, this is the same frame as in "The fade". When it is 240 or more
  on `T`, the fade starts on `T` + 2: the lead rises by 1/8 px on
  `T` + 1 only.
- **The last frame.** The brightness is 0 on `F0` + 28 and on `F0` + 29,
  and `F0` + 29 is the room's last frame. The lead walks on by 2 px on
  both.
- **What follows.** The level isn't finished and the level map doesn't
  follow. On the next logic frame, play is in the first level, which
  begins as it does from the level map, at the first level's
  room-return record (`exits.md`, "Where the first level's exits lead").
  That logic frame is the level's frame 0.

Synthetic example: the lead runs right with Right held and passes the
marker on frame `n`, ending it at `x` = 241.25. On `T` it stays at
241.25 (`X` = 241 ≥ 240 already, but `T` doesn't count), and `vx` = 2.
`T` + 1 ends at 243.25, 1/8 px higher, and is the first frame from
`T` + 1 with `X` ≥ 240, so `F0` = `T` + 2: brightness 14 on `T` + 2 and
`T` + 3, 0 on `T` + 30 and `T` + 31. `T` + 31, with `x` = 303.25, is the
room's last frame, and the next logic frame is the first level's frame
0.

Also measured in the room: Right held together with Down passed the
marker (37 runs), as did Right with Jump, Action or Up.

What follows the walk-out depends on the area the marker is in, not on
which `end-marker` reference placed it. In copies of the image, the
room's marker was given the first level's end-marker reference, and the
first level's end marker was given the room's; in a third copy, a class 1
entry near the first level's start became a class 2 entry with the
room's reference. Every frame was the same as with the marker's own
reference: in the room, play went back to the level; in the level, the
level was finished and the level map followed.

### Checks in the room

- 500 runs with random inputs biased to the right (Right alone and with
  Action, Jump, Up or Down; Action, Down, Down with Action, Jump, Left,
  Left with Action, nothing): 200 from a standing start in the room, 200
  with the lead alone put at a random point left of the marker first,
  and 100 with the other character following (it went on following the
  lead; not checked frame by frame).
  Every frame before `T` (46 521) was judged by "When the marker is
  passed": the marker was passed on exactly the frame the rules give, in
  every run, with `X` − `xm` from 0 to 8 on that frame, 100 times on the
  landing frame of a jump or fall. Every frame from `T` to the last
  (16 982, inputs held on 15 001 of those from `T` + 1) was as in "The
  walk-out" and "The fade" with the changes above, the last frame was
  `F0` + 29 in every run, and the next was the first level's frame 0.
  The first 91 frames there, with nothing held from frame 1, were the same
  as the level's own start from the level map in every run (45 500
  frames).
- Three runs passed the marker while turning toward the right: the turn
  went as in "The walk-out" ("Facing").
- With the marker moved up or down by 4 px (a change made for the test
  while the game ran), a lead walking right on the floor passed it; by
  5 px it didn't, and was stopped at the room's right end. With the
  marker moved 8, 40 or 100 px left, `T` changed and the fade started
  on the frame after the first frame ending with `X` ≥ 240, as above.

## Example

The marker is at `xm` = 1000, `ym` = 449. The lead walks right on a flat
floor with its feet line at 449, Right held, `vx` 2, the view at
`V` = 800. Frames end with `x` = 997.5, then 999.5 (`X` = 999 < 1000:
not passed), then 1001.5 on frame `n` (`X` = 1001: passed). `T` = `n` +
1.

| Frame | `x` | `vx` | Height change | `ground` | Brightness |
|---|---|---|---|---|---|
| `T` | 1001.5 | 2 | none | 1 | 15 |
| `T` + 1 | 1003.5 | 2 | up 1/8 | 0 | 15 |
| `T` + `k` (1 ≤ `k` ≤ 20) | 1001.5 + 2`k` | 2 | up 1/8 | 0 | 15 |
| `T` + 21 = `F0` | 1043.5 | 2 | none | 0 | 14 |
| `F0` + 1 | 1045.5 | 2 | none | 0 | 14 |
| `F0` + 2 | 1047.5 | 2 | none | 0 | 13 |
| `F0` + 28 | 1099.5 | 2 | none | 0 | 0 |

Frame `T` + 20 ends with `x` = 1041.5: `X` − `V` = 241 ≥ 240, so the
fade starts on `T` + 21 (`T` + 19 ended at 1039.5: 239). The lead rose
on `T` + 1 to `T` + 20, 2.5 px in all. The level's last frame is `F0` +
28 = `T` + 49.

At the first level's end, walking in at 2 px per frame, the level's
last frame came 61 frames after `T`.

## Not covered

- Other levels' end markers, and areas other than the first level and
  its room.
- Passing the marker with Left and Right both held, with Down held
  (other than in the room and with the second character leading), on
  the take-off frame of a jump, while carrying an object, and while being
  hit or bouncing off an enemy.
- The first character's movement during the walk-out when it follows
  the second character.
- Whether the other character can pass the marker, and the other
  character when it is not following (joining, being carried).
- Enemies and other objects during the walk-out.
- A lead that is already 240 px or more right of the picture's left
  edge on `T`, other than in the room ("In the first level's room").
- The sound during the walk-out and the fade.
