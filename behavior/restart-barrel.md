# The restart barrel

Titles: `title-1`

A **restart barrel** stands partway through a level. When the player
touches it, it breaks, and from then on the level's **restart point**
counts as reached: if the player is knocked out or falls out of the
level, the next time the level is entered play resumes at the restart
point instead of the level's start, with the player inside a barrel
that bursts after a short wait. This file covers when the barrel is
touched, what reaching the restart point changes, and how play resumes
there.

Frames are logic frames at 60 Hz, numbered as in a trace. `x` grows to
the right. Horizontal positions are level positions as in `view.md`;
`V` is the view's position there (the level x of the picture's left
column). Vertical positions are **level y** as in `view-vertical.md`:
they grow downward. The player's **feet line** `F` is as in
`view-vertical.md` (the player's `y` rounded up). "Right", "Left",
"Down", "Jump" and "Action" are the neutral inputs `R`, `L`, `D`, `J`,
`A` of the scenario format.

Measured on the first level's restart barrel with the first character
alone, by inputs only except where said:

- Touching: the player was put at every whole-pixel offset around the
  barrel, one pixel apart, in each pose below and facing either way,
  and run for one frame (about 15 000 placements); for the vertical
  ranges the barrel was moved up and down instead, the player keeping
  its pose. The ranges were then checked on 640 runs of random inputs
  (walking, running, jumping, rolling, crouching, standing) toward the
  barrel from both sides, 39 527 frames: in every run the barrel was
  touched on the frame the rules give, or not at all when they give no
  frame (607 touches: 285 jumping, 189 rolling, 112 walking, 12
  crouching, 9 others; 366 facing right, 241 facing left).
- Restarting: two knock-outs in a row after touching the barrel, one
  fall-out with both characters, a knock-out with the barrel moved away
  so that it was never touched, and the level finished after touching
  it and then entered again. Resuming: about 20 runs from the first
  frame of a resume, with Right or Jump held from different frames.
- Facing on resuming: three knock-outs facing left after touching the
  barrel (two hit on consecutive frames just after a turn, one walking
  left after a resume), each followed through the resume
  (122 to 197 resumed frames), with nothing, Left or Right held from
  frame 2.

## The barrel

A level's placement list (`../formats/placements.md`) places the
barrel with an entry of kind `restart-barrel` ("Kinds" there). The
barrel does not move. Its **position** (`xb`, `yb`) is the entry's
level x and level y (see "Positions" there). In the first level the
barrel's position is 9 px above the floor it stands over.

## Touching the barrel

Using whole pixels, let

- **`I`** = the player's `x` rounded down, minus `xb`;
- **`D`** = `yb` − `F`: how many px the player's feet line is above
  the barrel's position (negative when it is below).

The player touches the barrel on frame `n` when, **at the end of frame
`n`**, after the player has moved on that frame, `I` and `D` are within
the ranges for the player's **pose on `n`** and facing on `n` (both
ends included). The poses are those of `ball-contact.md` ("Which pose
the player has").

| Pose | `I`, facing right | `I`, facing left | `D` |
|---|---|---|---|
| upright | −24 to 27 | −30 to 21 | −38 to 34 |
| airborne | −24 to 24 | −27 to 21 | −55 to 13 |
| rolling | −22 to 27 | −30 to 19 | −36 to 30 |
| crouching | −27 to 18 | −21 to 24 | −29 to 38 |
| fidget | −14 to 30 | −33 to 11 | −42 to 33 |

Facing left, every `I` range is the facing-right range turned around:
from −(high end) − 3 to −(low end) − 3.

The ranges were the same on every frame measured within each pose,
whatever the speed, and with the player on the ground or in the air.
On the first frame of a crouch started while moving (Down pressed),
the pose is already crouching.

On the frame it is touched, the barrel breaks (see "Breaking") and the
level's restart point is reached. Touching it doesn't change the
player's position, speed, action or facing: a player walking into it
walks on at 2 px per frame.

The barrel can be touched only once: once touched, it is gone for the
rest of the visit to the level. With the view moved about 650 px to the
left of it and back, it did not appear again.

## Breaking

On the frame the barrel breaks it is removed. In its place there are
a flash and four pieces. Each piece moves sideways by its horizontal
speed every frame, and its upward speed starts as below and goes down
by 0.25 (64/256) every frame:

| Piece | Horizontal speed (px/frame) | Upward speed at the start (px/frame) |
|---|---|---|
| 1 | +2 | 4.75 |
| 2 | −1 | 5.75 |
| 3 | −2 | 4.75 |
| 4 | +1 | 6.25 |

The flash rises 0.5 px every frame.

## The restart point

A level with a restart barrel has a **restart point** (`xr`, `yr`). In
the first level the restart point is the barrel's position:
`xr` = `xb`, `yr` = `yb`. The level's data gives it as the first
level's restart record (`../formats/level-start.md`).

The restart point belongs to the level, not to the barrel. In a copy of
the image in which an entry near the level's start was given the
barrel's reference instead of its own, a barrel appeared there, and
touching it reached the level's restart point: after a knock-out play
resumed at the first level's restart point, far to the right, not where
the moved barrel had been.

Once the restart point is reached:

- When the player is knocked out (`knock-out.md`) or falls out of the
  level (`falling-out.md`), the next time the level is entered play
  resumes at the restart point ("Resuming" below). Without it, play
  begins at the level's start.
- It stays reached after a resume: a second knock-out after resuming
  resumed at the restart point again.
- Finishing the level (`level-end.md`) forgets it: the next time the
  level was entered, play began at the level's start, and was knocked
  out and began at the start again.

Which characters are there when play resumes is as in `following.md`
("When a level begins again"): after the fall-out with both
characters, both were there, the first leading.

## Resuming

Count the frames of the resumed level from 0, its first frame. The
player faces right on frame 0 and on every frame until an input turns
it, whichever way it faced when it was knocked out (it keeps the facing
it had on the hit until the knock-out ends, `knock-out.md`). A player
knocked out facing left resumed facing right, and holding Left from
frame 2 started a turn on frame 63 (the player faced left from frame
67 on); holding Right, or nothing, it went on as below.

| Frame | What happens |
|---|---|
| 0 | The player is at `x` = `xr`, `y` = `yr`. The picture is black. |
| 1 | The player is at `y` = `yr` − 8.5625 (`x` = `xr`), inside a barrel standing at the restart point. The player is not drawn; the barrel is. |
| 2 to 61 | The player doesn't move and inputs have no effect. The barrel shakes (below). |
| 62 | The barrel bursts: it is removed, and a flash and four pieces appear as in "Breaking" (the picture showed the burst from frame 63). The player still doesn't move and inputs still have no effect. |
| 63 on | The player is in the air and falls: `vy` is 0.4375 (7/16) on frame 63 and grows by 0.4375 every frame, until the player lands (`landing.md`). Inputs work from frame 63 on, as for any fall. |

The barrel's **shake** moves it sideways from the restart point, by an
amount that depends on the frame:

| Frames | Offset |
|---|---|
| 1 to 14 | 0 |
| 15 to 44 | +1 on frames 15, 16, 19, 20, 23, 24, …, 43, 44; 0 on the frames between (17, 18, 21, 22, …, 41, 42) |
| 45 to 61 | +1 on frames 47, 48, 51, 52, 55, 56, 59, 60; −3 on 45, 46, 49, 50, 53, 54; −7 on 57, 58, 61 |

The shake has no effect on the player.

Holding Right from frame 2, 3, 50, 60, 62 or 63 on made the player
start walking on frame 63, as if Right had been pressed on 63. Holding Jump
from frame 64 on made the player jump on the frame after landing.

On frame 0 the view's position `V` is `xr` − 78.5, so the picture's
left edge is `xr` − 79 (for `xr` of 128 or more, away from the level's
ends; `view.md`, "When an area begins"). From frame 1 the view follows
the usual rules of `view.md`.

The picture fades in from black from frame 4 on.

## What is there again after a knock-out

When play resumes after a knock-out, what happened in the level before
is partly kept. All of this was measured in the first level, knocked
out by a walker (one life lost), without an exit taken before
(`exits.md` has what an exit keeps), with the first character leading
and alone except where "Holders, and who leads" below says otherwise.

Kept as it was at the knock-out, wherever play resumes:

- the collectible count (`collectibles.md`, `count-display.md`);
- the figure count (`figure.md`);
- whether the restart point has been reached ("The restart point").

**The set of letters taken** (`letters.md`) depends on where play
resumes:

- **At the level's start** (the restart point not reached): the set is
  empty, and every letter is in the level again. Taking them again
  builds the set up again as in `letters.md`.
- **At the restart point**: the set is kept as it was at the knock-out,
  including letters taken after the restart point was reached, and the
  letters in it stay gone, whether their places are left or right of
  the restart point. The letters not in it are there.

In the level again after resuming at the level's start, as on a first
visit:

- a **bunch** taken (`bunches.md`);
- a **letter** taken (above);
- an **extra-life item** taken (kinds `rising-extra-life-1` and
  `rising-extra-life-2`, `extra-lives.md`): taking it again gives its
  lives again;
- the **figure** taken: taking it again adds one to the figure count
  again;
- a **hidden bunch** (kind `hidden-bunch`, `hidden-objects.md`) struck
  by the ground action, with its bunch taken;
- the **barrel** broken (`thrown-objects.md`), at its entry;
- the **animal crate** broken (`animal-crate.md`): it is there, and
  bouncing on it brings the animal out again;
- a **medium walker** (kind `medium-walker`) bounced on;
- the **large walkers** (kind `large-walker`) bounced on;
- the **thrower** (`thrower.md`) knocked out by the ground action
  (`ground-action-enemies.md`), by a bounce (`thrower-contact.md`) or by
  a roll (`thrower-contact.md`, "Rolling into the thrower");
- the two **extra-life items** of kind `extra-life-3` taken: taking them
  again gives their lives again;
- a **hidden barrel** (kind `hidden-barrel`, `hidden-objects.md`) set
  off, with its barrel left where it came to rest: the barrel is not
  there, the hidden object is, and a long fall onto it sets it off again
  and releases a barrel again;
- a **small walker** (kind `small-walker`) bounced on;
- the **barrel** picked up and put down unbroken somewhere else: it is
  at its entry's place again, and nothing is where it was put down;
- a **holder** (kind `holder`, `holder-breaking.md`) picked up and put
  down unbroken somewhere else: it is at its entry's place again, still
  holding the missing character, and nothing is where it was put down;
- the **barrel** or a **holder** still in the hands at the knock-out
  (`knock-out.md`, "An object in the hands"): it is at its entry's place
  again (a holder still holding the missing character);
- a **holder** broken: see "Holders, and who leads" below.

**Holders, and who leads.** A holder broken before the knock-out frees
the missing character, so both characters are there; with both there,
the first hit loses only one of them (`following.md`), and the knock-out
comes when the one left is hit. After the resume, at the level's start
or at the restart point:

- the character that was leading when the knock-out came leads, alone;
  this is the one that was following before the first of the two hits,
  whichever of the two that was (with a swap, `swap.md`, before the
  hits, it is the first character);
- the other character is missing again, and both holders are at their
  entries' places, the broken one included;
- breaking a holder again frees the missing character, which then
  follows the lead as before (`holder-breaking.md`, `following.md`).

With the first character alone throughout (no holder broken), it leads
alone after the resume, and breaking a holder frees the second
character, as on a first visit.

**After resuming at the restart point**, the same is true of every kind
in the list above except the letters (above): a bunch, the figure, the
barrel, the animal crate, the hidden bunch, a medium walker, the large
walkers, the thrower, the `extra-life-3` items and the hidden barrel are
there again, whether their places are left or right of the restart point;
so are a small walker, the barrel and a holder put down elsewhere or
still in the hands at the knock-out (at their entries' places) and a
holder broken.
Where it was measured (a bunch, the figure, the `extra-life-3` items, the
animal crate and the hidden barrel), taking, breaking or setting them off
again gives what it gave the first time. This was measured with each of
them taken, broken, bounced on, knocked out or set off after the restart
point had been reached, and with a bunch also before it.

Measured: 27 runs with a knock-out, each set of taken objects paired
with a control run that took nothing before its knock-out, judged in 151
checks, none different (8 more runs looked at the objects' places too
soon after moving there and were repeated): the lives one fewer, the count and figure
count unchanged, the set of letters as above, the objects seen at their
places after the resume, and what taking them again gave. The kinds
added after the first runs (large walkers, the thrower, `extra-life-3`,
`hidden-barrel`) and the resumes at the restart point other than for
bunches and letters were measured in 28 more runs, 14 sets each with a
control (the restart point reached in both for the restart-point sets),
judged in 118 checks the same way, none different. The holders, the
put-down barrel and holder and the small walker were measured in 24 more
runs, 12 sets each with a control (both holders broken once each, with
and without a swap before the knock-out, and each set also after
reaching the restart point), judged in 138 checks the same way plus who
led and which character was missing after the resume and after breaking
the holder again, none different. With both characters there the lead
was walked into the walkers, so these runs could take bunches on the
way; the count after the resume was the count at the knock-out. The
barrel and a holder (the one nearer the level's start) still in the
hands at the knock-out (picked up, then Action held through the hit)
were measured in 8 more runs, 4 sets each with a control (each also
after reaching the restart point), judged in 44 checks the same way plus
the object being in the hands on the frame before the hit, none
different.
The thrower knocked out by a bounce or a roll was measured in 8 more
runs, 4 sets (a bounce and a roll, each resuming at the level's start
and at the restart point) each with a control in which the thrower
wasn't knocked out: it was seen at its place after the resume in all 8.

## Example

The restart point is at `xr` = 1000, `yr` = 400, over a floor at level
y 409. The player was knocked out after touching the barrel, and the
level is entered again. Nothing is held.

| Frame | `y` | `vy` | What happens |
|---|---|---|---|
| 0 | 400 | 0 | |
| 1 to 62 | 391.4375 | 0 | Inside the barrel; it bursts on 62 |
| 63 | 391.875 | 0.4375 | |
| 64 | 392.75 | 0.875 | |
| 65 | 394.0625 | 1.3125 | |
| 66 | 395.8125 | 1.75 | |
| 67 | 398 | 2.1875 | |
| 68 | 400.625 | 2.625 | |
| 69 | 403.6875 | 3.0625 | |
| 70 | 409 | 0 | Lands: 407.1875 would be 1.8125 px above the floor (`landing.md`) |

`x` stays 1000 throughout, and `V` is 921.5 on frame 0.

Touching: a barrel at `xb` = 1000, `yb` = 400, with the player walking
right at 2 px per frame on a floor at level y 409 (`D` = −9). Frames
end with `x` = 973.5 (`I` = −27), 975.5 (`I` = −25), 977.5 (`I` = −23):
the barrel is touched on the third, the upright range starting at −24.

## Not covered

- Other levels, and how to tell where a level's restart point is, or
  that a level has one, from the level's data. For the first level,
  `../formats/level-start.md` gives it.
- The second character: whether a following character touches the
  barrel, and where each character is when play resumes with both.
- A barrel out of the picture: in the measurements the barrel, moved
  up, was never touched once it was 91 px or more above its place.
- Touching the barrel while bouncing off an enemy, carrying an object,
  being hit or knocked out, swapping, or during the Down + Action
  ground action.
- After a knock-out ("What is there again after a knock-out"): kinds
  not listed there (among them tyres and `hidden-metal-barrel`), objects
  moved in other ways (thrown without breaking, put down in other
  places), extra-life items of kinds `rising-extra-life-1` and
  `rising-extra-life-2` after resuming at the restart point, the single
  collectibles, a knock-out with both
  characters there in other ways than after breaking a holder (for
  example both there at the level's start), where the missing character
  is when both holders are broken, and a fall-out instead of a
  knock-out.
- Ways of leaving the level other than finishing it, a knock-out or a
  fall-out, and what happens to the restart point when another level is
  played before this one is entered again, or when no tries are left.
- Input on frame 1 of a resume. Right or Jump held from frame 0 acted
  on frame 1 (a Right moved the player 0.25 px to the right, a Jump left
  the player in the jump's action), and the player then stayed where it
  was inside the barrel until frame 62.
- Facing on resuming after a fall-out facing left.
- The fade-in, the sounds, how the
  pieces and the flash are drawn, where they start, when they are
  removed, and whether they touch anything.
- On which frame the picture first shows the broken barrel after it is
  touched.
- How play begins at the level's start.
