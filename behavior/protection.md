# Protection after losing a character

Titles: `title-1`

After a hit with two characters (`losing-a-character.md`), the other
character, which takes over, can't be hurt for a while, and it flashes
for most of that time. A hit with no other character
(`knock-out.md`) ends the level, so there is no protection to describe
there.

Frames are logic frames at 60 Hz, numbered as in a trace. **H** is the
hit frame and **T** the frame on which the other character's hop starts,
both as in `losing-a-character.md`. Everything below was measured by
inputs alone, with the first character as the hit character and the
second taking over, on a stretch of ground where two large walkers
(`walker-contact.md`) walk back toward the player after the take-over.
The hit was set up in 89 different ways, which put T anywhere from
H + 39 to H + 67 and the hop's landing from H + 91 to H + 118. From two
frames after the landing, the character stood still, or walked right
for 1 to 50 frames or left for 1 to 30 frames and then stood: 7209 runs
in all.

## Protection

From **H to H + 149** the character under control (the other
character, once it has taken over) is protected:

- A contact that would hurt the player has no effect. Nothing happens on
  either side: the character keeps moving under its normal rules, and
  the walker keeps walking, through and past it.
- The end of protection doesn't depend on T or on when the hop lands.

From **H + 150** the character can be hurt again. As in
`walker-contact.md`, whether a hit happens on a frame depends on
positions at the end of the frame before. So a hurting contact that is
still there at the end of H + 149 hurts on **H + 150**, the earliest
possible second hit. A contact that starts later hurts as usual.

How this was observed:

- No run had a second hit before H + 150. Of the 4681 second hits,
  2731 came on exactly H + 150, with T anywhere from H + 39 to H + 61.
- A contact counts as one that "would hurt" when the same contact hurt
  after protection. Two contacts are the same when they have the same
  whole-pixel offsets I and U (as in `walker-contact.md`) from every
  walker within 45 px across and 60 px up or down, the same walker
  headings and speeds, and the same pose, facing and up speed. From
  H + 150 on, every one of those contacts was followed by a hit on the
  next frame. On 7205 frames from H + 114 to H + 149, in 2428 runs, the
  frame before ended in one of those same contacts, and nothing
  happened. In these, the character was standing or walking while a
  walker came through it.

### Example (made-up numbers)

The player is hit on frame 500. A walker touches the character that
took over from frame 610 to frame 680, with positions that would hurt
on every one of those frames. Nothing happens on frames 610 to 649. The
character is hurt on frame 650.

## Flashing

From **H + 38 to H + 133** the character isn't drawn on some frames: in
a repeating cycle of 4 frames it is drawn on 2 consecutive frames and
not drawn on the next 2. **T + 1** is an exception: the character is
always drawn on it, whatever the cycle says, and the cycle carries on
unchanged after it. Before H + 38 and from H + 134 on, the character is
drawn on every frame, though protection lasts to H + 149.

Where the cycle starts is **not specified**. It isn't tied to H or to
T: two runs whose hits were a multiple of 4 frames apart had the cycle
in different places, and so did two runs whose hops were. It doesn't
restart anywhere from H + 38 to H + 133. An engine may place the cycle
anywhere, as long as it keeps the same cycle through that stretch. So
the first frame the cycle leaves out is one of H + 38 to H + 41, and
the last is one of H + 130 to H + 133. (T can be as early as H + 39, so
T + 1, which is drawn anyway, can be that first frame.)

A scenario may check that:

- the character is drawn on every frame before H + 38, from H + 134
  on, and on T + 1;
- from H + 38 to H + 133 (leaving out T + 1), drawn and not-drawn
  frames follow the 2-and-2 cycle above with a single placement, and
  the first frame that placement leaves out is one of H + 38 to H + 41.

A scenario may not check which of the four placements is used. Other
than T + 1, it can't require any given frame from H + 38 to H + 133 to
be drawn or not drawn.

Measured by whether the character showed on screen, on 1410 frames
across 10 runs with different hit and hop frames. In each run a single
placement of the cycle fitted every frame.

### Example (made-up numbers)

Suppose an engine places the cycle so that the character is not drawn
on frames 540, 541, 544, 545, 548, 549, and so on (drawn on 538, 539,
542, 543, …). The player is hit on frame 500 and T is frame 559. The
character is drawn on every frame up to 539 (H + 39), and not drawn on
540, 541, 544, 545, 548, 549, 552, 553, 556 and 557. On 560 (T + 1) it
is drawn, where the cycle says not drawn; on 561 it isn't, as the cycle
says. The last frames not drawn are 632 and 633 (H + 132 and H + 133);
from 634 (H + 134) it is drawn on every frame. Placing the cycle one
frame later (not drawn on 541, 542, 545, 546, …) would be just as
correct.

## In a trace

Nothing in a trace marks protection or flashing. A contact during
protection gives no event and changes no field.

## Not covered

- Protection measured with large walkers only, on one stretch of one
  level. Other enemies and hazards, and falling out of the level, aren't
  covered.
- Which contacts hurt the second character in general: its reach
  against a walker isn't specified (`walker-contact.md` covers the
  first character).
- Coming down onto the large walker with the second character: the
  two are pushed apart (`walker-contact.md`, "The second character"),
  also during protection. Whether the push changes when protection ends
  isn't specified.
- Contacts with the other character while it stands still, from H to
  H + 36.
- Protection when the first character takes over from the second.
- Any protection after other events (a bounce, a swap, a knock-out).
- Where the flashing cycle starts.
