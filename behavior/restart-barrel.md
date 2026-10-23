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
- Touching while holding an object: a `barrel`, moved in a copy of the
  image to rest just left of the restart barrel, picked up and held
  with Action. Holding it, the player was put at every whole-pixel `I`
  from −45 to 45 and every `D` from −75 to 75 (the barrel moved 60 px
  up for the `D` ranges) and run for one frame, on 427 frames of
  standing, walking and jumping, facing either way, and on full grids
  for four of those frames (123 630 placements). Then 640 runs of
  random walking, jumping and standing with Action held toward the
  barrel from both sides, 56 942 frames: in every run the barrel was
  touched on the frame the rules give, or not at all when they give no
  frame (617 touches: 372 in the air, 241 walking, 4 standing; 368
  facing right, 249 facing left). Each of the 617 runs was played again
  with the barrel moved away so that it was never touched: on the 61
  frames from the touch on, the player's position, action, facing and
  picture and the held object's position were the same in both.
- Restarting: two knock-outs in a row after touching the barrel, one
  fall-out with both characters, a knock-out with the barrel moved away
  so that it was never touched, and the level finished after touching
  it and then entered again. Resuming: about 20 runs from the first
  frame of a resume, with Right or Jump held from different frames.
- Input on frame 1: every set of Left, Right, Up, Down, Jump and Action
  held on frame 1 only (64 sets), from the first frame of two resumes,
  one after a knock-out and one after a fall-out, 130 frames each. The
  two resumes gave the same `x`, `y`, `vx`, `vy` and facing on every
  frame for every set. On all 16 490 frames checked (the landing frames
  of jumps and, for the ground action with a direction, frames after
  frame 102 or after leaving the floor left out) the player's position,
  speed and facing were as the rules in "Input on frame 1" give. Also
  26 runs from the knock-out resume with Jump, Left, Right, Up, Down and
  Down + Action held longer, released, or pressed again on frame 63
  (200 frames each).
- Facing on resuming: three knock-outs facing left after touching the
  barrel (two hit on consecutive frames just after a turn, one walking
  left after a resume), each followed through the resume
  (122 to 197 resumed frames), with nothing, Left or Right held from
  frame 2; and six fall-outs facing left after touching the barrel
  (five walking back off a ledge into a pit, at two different times,
  and one jumping into it with Left released; 257 to 297 resumed
  frames), with nothing, Left (twice) or Right held from frame 2. The
  player's position, action and facing on the resumed frames were
  compared with the knock-out runs holding the same input (148, 157
  and 197 frames).

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
| carrying | −23 to 22 | −25 to 20 | −38 to 34 |

Facing left, every `I` range is the facing-right range turned around:
from −(high end) − 3 to −(low end) − 3.

The ranges were the same on every frame measured within each pose,
whatever the speed, and with the player on the ground or in the air.
On the first frame of a crouch started while moving (Down pressed),
the pose is already crouching.

The pose is **carrying** while the player holds an object
(`carrying.md`) after its pick-up has ended (from G + 15 on, as frames
are named there): standing, walking, and in the air. While it lasts it
is the pose whatever the player is doing; the carrying ranges were the
same on every frame measured. The held object
doesn't touch the barrel itself: only the player's position counts.

On the frame it is touched, the barrel breaks (see "Breaking") and the
level's restart point is reached. Touching it doesn't change the
player's position, speed, action or facing: a player walking into it
walks on at 2 px per frame. A player holding an object goes on holding
it, and the object stays where it would have been without the barrel.

The barrel can be touched only once: once touched, it is gone for the
rest of the visit to the level. With the view moved about 650 px to the
left of it and back, it did not appear again.

**Only the lead touches it.** With both characters there, only the
character under control (`following.md`) can touch the barrel. The
follower never touches it, wherever it is.

Measured on the first level with both characters there, first or second
leading: 80 runs of 600 frames of random standing, walking, running,
rolls, jumps and crouching around the barrel. Whenever the lead was
near the barrel, the barrel was moved 100 px up or down, out of the
lead's reach; otherwise it was at its place. The barrel was at its place
on 39 492 frames. On 579 of them the follower was where every pose's
ranges above meet (`I` from −14 to 11, `D` from −29 to 13), in various
poses, facing either way. The barrel was never touched. In 30 control
runs the barrel was moved out of the follower's reach instead, and the
lead touched it in every one.

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
characters, both were there, the same one leading. Where the follower
is on each frame of the resume is in `following.md` ("At the restart
point").

## Resuming

Count the frames of the resumed level from 0, its first frame. The
player faces right on frame 0 and on every frame until an input turns
it, whichever way it faced when it was knocked out (it keeps the facing
it had on the hit until the knock-out ends, `knock-out.md`) or when it
fell out of the level (`falling-out.md`). A player knocked out or
fallen out facing left resumed facing right, and holding Left from
frame 2 started a turn on frame 63 (the player faced left from frame
67 on); holding Right, or nothing, it went on as below. The resumed
frames don't depend on how the level was left: with the same inputs,
the player was in the same place, in the same action and facing the
same way on every resumed frame after a fall-out as after a knock-out.

| Frame | What happens |
|---|---|
| 0 | The player is at `x` = `xr`, `y` = `yr`. The picture is black. |
| 1 | The player is at `y` = `yr` − 8.5625 (`x` = `xr`), inside a barrel standing at the restart point. The player is not drawn; the barrel is. Input held on this frame can change this and later frames ("Input on frame 1" below). |
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
the usual rules of `view.md`. Vertically it stays where it is on frames
1 to 62 and follows the usual rules from frame 63 (`view-vertical.md`,
"When an area begins").

The picture fades in from black from frame 4 on.

### Input on frame 1

Input held on frame 0 has no effect. Input held on frame 1 acts on
frame 1, and some of it has effects that last into frame 63 and later;
the table above is the case with nothing held on frame 1. Up, Down and
Action held alone on frame 1 have no effect.

**Position.** Left and Right held on frame 1 move the player to the
right on frame 1, and `x` then stays at the new place until frame 63:

| Held on frame 1 | `x` on frames 1 to 62 |
|---|---|
| Right without Left | `xr` + 0.25 (`xr` + 0.375 with Action) |
| Left without Right | `xr` + 0.75 (`xr` + 0.625 with Action) |
| Both, or neither | `xr` |

Holding Left moves the player right as well. From frame 63 on, the
player moves from there as it would from `xr`: after Right held on frame
1 only, every later frame is as with nothing held, 0.25 px further
right, and the player doesn't start walking.

Besides the position, the first of these that applies says what else
frame 1's input does:

1. **Jump held** (whatever else is held): the player jumps on frame 63.
   On frames 1 to 62, `y` is `yr` − 8.71875 (0.15625 higher than with no
   jump). On frame 63 the player moves down by 0.4375, or by 0.28125 if
   Jump is held on frame 63, and `vy` is −7: frame 63 is the jump's
   take-off frame. From frame 64 on, each frame is as in `jump-arc.md`
   ("Each frame after T"), with frame 1 as the press frame and frames 2
   to 62 left out: the lighter gravity applies while Jump has been held
   on frame 1 and on every frame from 63 up to this one, whatever is
   held on frames 2 to 62. With Jump held on frame 1 only, the top of
   the jump is 52.5 px above `y` on frame 63; held on, 83.625 px, as for
   any jump (`jump-arc.md`, "How long jump is held"). The player lands
   as in `landing.md` (on frame 96, and 114 holding Jump, where the floor
   is 9 px below `yr`). Inputs from frame 63 on act as for any jump.
   There is no turn and no ground action.
2. **Down and Action held, Up not held**: the ground action
   (`ground-action.md`) starts on frame 1, though the player is in the
   air, with the direction set by Left and Right on frame 1 as usual.
   It is in progress on frames 1 to 119; frame 120 is its first frame
   after (`E`), and from frame 121 the usual rules apply. `y` and `vy`
   are as with nothing held on frame 1 (the player falls from frame 63
   and lands). With direction 0 the player doesn't move until frame
   121; Left or Right held from frame 2 or 63 on makes no difference
   before then. With direction ±1, `vx` is 0 until frame 62, and from
   frame 63 the speed follows `ground-action.md` ("Speed and position")
   with frame 63 as `P`: ±0.375 on frame 63, ±0.703125 on 64, and so on,
   ±3 from frame 102 (`P` + 39) on flat floor.
3. **Left held without Right**: a turn (`ground-speed.md`, "Facing and
   turns") starts on frame 1 and goes on from frame 63: the player
   faces left from frame 64 on, and frame 67 is the turn's last frame
   (as for a turn with `T` + 4 on frame 64). Frames 2 to 62 are as in the
   table. With Left held on, the player moves left in the air from
   frame 63 and walks left after landing; with nothing held it lands
   and stands facing left.

Right held on frame 1, alone or with Up, Down or Action (but not Down
and Action without Up), changes only the position.

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
walkers, the thrower, the extra-life items of all three kinds and the
hidden barrel are there again, whether their places are left or right
of the restart point;
so are a small walker, the barrel and a holder put down elsewhere or
still in the hands at the knock-out (at their entries' places) and a
holder broken.
Where it was measured (a bunch, the figure, the extra-life items, the
animal crate and the hidden barrel), taking, breaking or setting them off
again gives what it gave the first time. This was measured with each of
them taken, broken, bounced on, knocked out or set off after the restart
point had been reached, and with a bunch and the `rising-extra-life-1`
and `rising-extra-life-2` items also before it.

**Extra-life items that went away untaken.** A `rising-extra-life-1` or
`rising-extra-life-2` item that appeared and went away without being
taken before the knock-out (`extra-lives.md`, "Going away untaken"),
whether it rose out of the level or went away past the window's left
edge, is there again after the resume, at the level's start and at the
restart point. It appears when its entry comes into the window and
rises as on a first visit, and taking it gives its lives. Without a
knock-out it stays gone.

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
The first level's three rising extra-life items taken before a knock-out
with play resuming at the restart point were measured in 8 more runs, 2
sets each with a control in which only the restart point was reached: all
three taken and then the restart point reached, or the two farther from
the restart point taken, the restart point reached and then the nearest
one taken. In half of the runs each item's place was watched after the
resume, in the other half each item was taken: the same items appeared
and gave the same lives as in the control (all three, 4 lives in total),
judged in 36 checks, none different.
The thrower knocked out by a bounce or a roll was measured in 8 more
runs, 4 sets (a bounce and a roll, each resuming at the level's start
and at the restart point) each with a control in which the thrower
wasn't knocked out: it was seen at its place after the resume in all 8.
The first level's three rising extra-life items going away untaken
were measured in 16 runs: two ways of going away (all three left to
rise out of the level, or the view moved right until each had gone past
the window's left edge), each resuming at the level's start and at the
restart point (reached after the items had gone), each with a control
in which the items hadn't appeared before the knock-out (in the
restart-point controls, the item nearest the restart point had appeared
and risen out of the level while the restart point was reached). In
half of the runs each item's place was watched after the resume, in the
other half each item was taken: the same items appeared and gave the
same lives as in the control, judged in 84 checks, none different. In 2
more runs without a knock-out, taking the items right after they had
gone found none of them and gave no lives.

## What is there again after a fall-out

A fall-out (`falling-out.md`) instead of a knock-out leaves the level in
the same state for play to resume in, with one difference when both
characters are there.

**The same as after a knock-out.** Everything in "What is there again
after a knock-out" above holds after a fall-out, at the level's start
and at the restart point: one life fewer, the count, the figure count
and the restart point kept, the set of letters emptied or kept as
there, every kind in that list in the level again, and objects put down
elsewhere at their entries' places. An object still in the hands when
the player falls out stays in the hands, moving with the player, to the
last frame of the level (`falling-out.md`, "The end of the level"); it
is at its entry's place again after the resume (the holder still holding
the missing character).

**Both characters there.** With both characters there when the lead
falls out, the level ends as in `falling-out.md`, and neither
character is lost on its own. After the resume, at the level's start
or at the restart point:

- both characters are there: the character that was leading when it
  fell out leads, and the other one follows it (`following.md`);
- this holds whichever of the two was leading: with a swap (`swap.md`)
  before the fall-out, the second character leads after the resume
  (measured at the restart point);
- the holders are at their entries' places again, as after a knock-out,
  even though no character is missing; breaking one then frees nobody
  (`holder-breaking.md`, "With both characters there").

This differs from a knock-out with both there, after which the
character that led at the knock-out resumes alone ("Holders, and who
leads").

Measured: every set of the knock-out measurements above except the two
with a swap repeated with a fall-out (the lead dropped into the gap of `falling-out.md`) where
those had the knock-out: 89 runs, each set with a control, judged in the
same 411 checks, none different, except where the checks expected one
character to resume alone. Those runs, with the first character
leading, the second following and a holder broken, resumed with the
first character leading and the second following (4 runs). The barrel
and a holder in the hands (4 runs) stayed in the hands on every frame
from the fall to the level's end. Both characters walking into the gap
together, a holder broken earlier and the restart point reached, the
first leading or (after a swap) the second, letting go of Right at five
points each (10 runs, input only): one life fewer, the resume at the restart
point, the same character leading, the other following, and the holder
near the restart point there again in all 10.

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

## Pictures

What the barrel, its burst and the player look like in `title-1`. Each
picture has a label used in this file and is named by its picture hash
(`../formats/sprites.md`, "Picture hashes"); it is drawn at the
object's position as `../formats/sprites.md` says ("Where the picture is
drawn"). The picture given for a frame is the one on screen with that
frame's view. Measured with the first character.

| Picture | Hash |
|---|---|
| `barrel-1` | `562e07d3e5eb26cd3fc9223e22190faf009870fd8e2cf0bd90ffa0bf79a789ca` |
| `barrel-2` | `309f680c2c481950e3dbd36f9e17da192aa9782641dff8433015cbaadc7e1b06` |
| `barrel-3` | `442a5452fce74126fd3f1005208d55408d56964d9ef904e2694bfcd1a052f3da` |
| `barrel-4` | `8c87a6022438563fd58fa298b1fad5b8b70a08c38e71bf7ea2a851fce355415d` |
| `barrel-5` | `c0dd7ecbfa7b37f5b26c64679e9a5edaed72437d8c3693413f8f0d7d631c314c` |
| `barrel-6` | `0cea18fcf7d5ee110c7ea68ffa46dd90f3e5fc82a51c204eb4edc14980006d57` |
| `barrel-7` | `e786986fb2d112f1608d5aaeefb989ddf358630114c89e2e05e4f679ed18d465` |
| `barrel-8` | `2b572c77efb704e87c769015de1d4f4305b13f712a5ebd26d4e9d3970d440c30` |
| `sparkle-3` | `7776428202573c17811467a0c4f422b576b00c8918e0cbac5f130c3cc8175a50` |
| `sparkle-4` | `f07b75e93852d7444d095ddb831c34def31f52069e674664fbc494e23c1b4383` |
| `sparkle-5` | `114d3728fb1de82f6b7fa1d4959cf4f32deae355e325839f197e230e153dbf2a` |
| `sparkle-6` | `5c960746e43782515b9b2d94abd499e7365ab10f95305f0f5109b31823e2a6f0` |
| `sparkle-7` | `bbdaffcfbd4be1922f1945a36fd8634cd32a93800429c36693d69c878cca7b96` |
| `sparkle-8` | `906a05bc5b6b7ba817905b62010c28560bd10e97ca9b096817fed96a57deb071` |
| `sparkle-9` | `642748ee41edc5e4f8bc5ecd6e3176c6afb26f227fb8e894a409a3c64bd45369` |
| `sparkle-10` | `e5681aa759c2cd7835443834292b841c526264e96d83886ad95904255e4e3e9e` |
| `sparkle-11` | `f17a682f5c784b6ba87ac43ac3194209fe21b6e0595d4a2a45185603912cc425` |
| `sparkle-12` | `5c1ee43fce6eaf21600b6e686ebcd3abb12886a233791dbfb3b879725d71464e` |
| `sparkle-13` | `7c20d31f4b9bdbc6c8345e88ae42a6a057af5d1e1c230937f1d487d5badf2e14` |
| `sparkle-14` | `1bd25301bb04aaff29f3ddbd6432559453021882edeb347151460d544fb48678` |

The flash and the pieces are those of `thrown-objects.md` ("Pictures"):
`flash-1` to `flash-8` and `piece-a-1` to `piece-a-8`, `piece-b-1` to
`piece-b-8`. Of the sparkle's 15 pictures, `sparkle-1`, `sparkle-2` and
`sparkle-15` are the first two and the last; they have too few opaque
pixels to be named, so only `sparkle-3` to `sparkle-14` are in the table.

**When a hash isn't found.** The importer looks for every hash in this
section. The eight `barrel` pictures belong together, and so do
the twelve `sparkle` pictures. If any hash of a group isn't found, the
engine shows its own art for the group, keeping the timing given here.
The sparkle's three unnamed pictures are always the engine's own, drawn
at the places given below.

### The barrel

- **Standing in the level, not touched:** the eight pictures in turn,
  `barrel-1` to `barrel-8`, 2 frames each, round and
  round. It is never mirrored. The cycle ran on while the barrel was far
  from the player and was in the same step in runs that began at different
  places; which frame it is tied to is not covered.
- **Touched in the level:** on the frame it is touched ("Breaking") it is
  no longer drawn. The flash and the pieces are drawn from that frame:
  `flash-1` × 2, `flash-2` × 2, `flash-3` × 3, `flash-4` × 2, `flash-5` × 3,
  `flash-6` × 2, `flash-7` × 3, `flash-8` × 2, then gone; the pieces as in
  `thrown-objects.md` ("Breaking"), counting `j` from 0 on that frame. The
  flash is not mirrored.
- **On a resume:** from frame 1 to 61 the barrel is `barrel-1` on
  every frame, shaking or not (the shake only moves it, "Resuming"); the
  pictures of the cycle above are not shown. On frame 62 it is no longer
  drawn, and the flash (`flash-1` on frame 62) and the pieces are drawn as
  above, not mirrored, with the same timing.

Measured by the pictures on screen: in 7 runs, 1 979 frames of the
standing barrel in the level showed the picture that the cycle above gives
for that frame's step, and no frame showed another one (24 more frames
were not counted because the barrel was not wholly on the screen). On a
resume, in 11 runs the barrel was `barrel-1` on each of frames 4
to 61 (frames 1 to 3 are black). The flash and the pieces showed the
pictures above on every frame on which they were recognisable.

### The sparkles

After the barrel is touched in the level, **sparkles** appear near the
place where it was; none appear on a resume (none in 896 frames of two
resumes). A sparkle shows `sparkle-1` to `sparkle-15` in turn, 2 frames
each (30 frames in all), then is gone, never mirrored. It is drawn at the
barrel's position plus an offset, from 3 px left to 11 px right and from
7 to 20 px above it; each sparkle keeps its offset. The offsets seen, as
(px right, px above): (+7, 13), (+11, 9), (+11, 12), (−3, 7), (+6, 20),
(−2, 10) and (−1, 13).

Counting `n` from 0 on the frame the barrel is touched, a sparkle starts
on one of the frames 4, 20, 36, 44, 52, 60 and 68 and on no others
(12 runs of 500 to 900 frames). Which of them start a sparkle, and which
offset each one has, differed between runs: the one at n = 20 started in
every run and always had the offset (+7, 13), and each run had 4 to 6
sparkles in all. What decides that is not covered. In 3 runs with the
player standing near, 193 frames of sparkles showed the picture this
list gives, and none showed another one; on 167 more no sparkle picture
could be recognised (partly outside the screen, or covered), and they
were not counted.

### The player

On frame 0 the picture is black ("Resuming") and the player is not drawn
from frame 1 to frame 62. From frame 63 the player shows the pictures of
its action, as if that action had begun before frame 63:

- **No input.** The list of `player-frames.md` ("Standing still", first
  character) with its index counted from 0 for `stand-1` on the first
  frame S: the picture on frame `f` is the one at index `f` + 143, as if S
  were frame −143. So `wait-2` is shown on frames 63 to 65 (the list's position on 58 to
  61 would be `wait-1`; nothing is drawn then), `wait-3` on 66 to 69, and so on through the
  wait, the later `stand-1` and the sway, to the second wait. This is the
  case while the player is still in the air on frames 63 to 70, and after
  the landing. It held on every frame measured (1 573 frames in 2
  runs, one after a knock-out and one after a fall-out, up to frame
  about 890, none differing).
- **Right held from frame 63, nothing else:** `walk-1` on 63 to 65,
  `walk-2` on 66 to 68, `walk-3` on 69 and 70, `walk-4` on 71 to 73,
  and on as in "Walking, running and turning" for walking begun on frame 63
  (observed up to frame 86, "Measured" below).
- **Left held from frame 63** (the player faces right on frame 63): a turn
  begun on frame 63: `turn-a` on 63 and 64, `turn-b` on 65 to 68, `turn-a` on
  69 and 70, `walk-1` from 71 ("Walking, running and turning").
- **Right held from frame 1 too:** `walk-2` on 63 and 64, `walk-3` on 65
  to 67, `walk-4` on 68 and 69; the walking steps count from frame 1.

Measured by the pictures on screen: each of the three cases with input in
1 run of a resume, on frames 63 to 86 (24 frames each, 72 in all); on every
frame the picture was the one listed, none differing. The Right runs
left the floor on frame 87, so later frames were not compared. The cases
with input were not observed after both ways of resuming.

The pictures with no input are the same after a knock-out and after a
fall-out.

## Not covered

- Other levels, and how to tell where a level's restart point is, or
  that a level has one, from the level's data. For the first level,
  `../formats/level-start.md` gives it.
- A barrel out of the picture: in the measurements the barrel, moved
  up, was never touched once it was 91 px or more above its place.
- Touching the barrel while bouncing off an enemy, being hit or
  knocked out, swapping, or during the Down + Action ground action.
  While holding an object: during its pick-up (G to G + 14 in
  `carrying.md`), a throw or a put-down, and holding a `holder` (only
  a `barrel` was held).
- After a knock-out ("What is there again after a knock-out"): kinds
  not listed there (among them tyres and `hidden-metal-barrel`), objects
  moved in other ways (thrown without breaking, put down in other
  places), the single collectibles, a knock-out with both
  characters there in other ways than after breaking a holder (for
  example both there at the level's start), and where the missing
  character is when both holders are broken.
- After a fall-out ("What is there again after a fall-out"): the same
  items as after a knock-out, and the other character falling out while
  the lead stays in the level.
- Ways of leaving the level other than finishing it, a knock-out or a
  fall-out, and what happens to the restart point when another level is
  played before this one is entered again, or when no tries are left.
- Swap held on frame 1 of a resume; the ground action started on frame
  1 after the player leaves the floor or meets a slope (it was followed
  only on flat floor); and a turn started on frame 1 with Right held
  from frame 2 (the player moved right while facing left).
- Objects of the level whose place comes into the window
  (`walker-view.md`) during a resume. A walker and a placed ball whose
  places came into the window on frames 76 to 81, with the player walking
  left from frame 63 (the ball also with the player running), both
  appeared on frame 82, not on the frame their place came in.
- The fade-in, the sounds, where the pieces and the flash start, when
  they are removed, and whether they touch anything.
- The pictures with the second character there, other levels, which frame
  the standing barrel's cycle is tied to, which frames start a sparkle,
  and the player's pictures on a resume with other input than the cases in
  "The player".
- How play begins at the level's start.
