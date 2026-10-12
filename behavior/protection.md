# Protection after losing a character

Titles: `title-1`

After a hit with two characters (`losing-a-character.md`), the other
character, which takes over, can't be hurt for a while, and it flashes
for most of that time. A hit with no other character
(`knock-out.md`) ends the level, so there is no protection to describe
there.

Frames are logic frames at 60 Hz, numbered as in a trace. **H** is the
hit frame and **T** the frame on which the other character's hop starts,
both as in `losing-a-character.md`. Measured with the first character
as the hit character and the second taking over, on flat ground, with
the large walker of `walker-contact.md` coming back toward the player
after the take-over: standing still while it walks into the player from
behind, walking into it, and jumping in place or toward it, starting
at many frames between the hop's landing and the end of protection (about
250 runs by inputs). Whether a contact during protection would have hurt
was confirmed by making the protection end early on chosen frames: the
player was then hurt as `walker-contact.md` describes. T ranged from
H + 47 to H + 67 and the hop's landing from H + 88 to H + 118.

## Protection

From **H to H + 149** the character under control (the other
character, once it has taken over) is protected:

- A contact that would hurt the player (`walker-contact.md`) has no
  effect. Nothing happens on either side: the character keeps moving
  under its normal rules, and the walker keeps walking, through and past
  it. This was measured with contacts that hurt lasting up to 33 frames
  in a row, ending on H + 149, and with contacts during the run to the
  hit spot, the hop, standing, walking and jumping.
- The end of protection doesn't depend on T or on when the hop lands.

From **H + 150** the character can be hurt again, by the rule in
`walker-contact.md` (with no other character left, the hit is the one
in `knock-out.md`). That rule looks at positions at the end of the
frame before the hit, so a hurting contact that is still there at the
end of H + 149 hurts on **H + 150**, the earliest possible second hit.
A contact that starts later hurts as usual.

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

The cycle doesn't start at H or at T. It follows a count of logic frames
that runs through play, so the first frame not drawn can be H + 38,
H + 39, H + 40 or H + 41, depending on the frame of the hit. This spec
doesn't define that count's starting point; it is the same count
throughout, with no restart at the hit or at T.

### Example (made-up numbers)

The cycle has the character not drawn on frames 540, 541, 544, 545,
548, 549, and so on (drawn on 538, 539, 542, 543, …). The player is hit
on frame 500 and T is frame 559. The character is drawn on every frame
up to 539 (H + 39), and not drawn on 540, 541, 544, 545, 548, 549, 552,
553, 556 and 557. On 560 (T + 1) it is drawn, where the cycle says not
drawn; on 561 it isn't, as the cycle says. The last frames not drawn
are 632 and 633 (H + 132 and H + 133); from 634 (H + 134) it is drawn
on every frame.

## In a trace

Nothing in a trace marks protection or flashing. A contact during
protection gives no event and changes no field.

## Not covered

- Protection measured with one kind of walker only (the large walker)
  and on one site. Other enemies and hazards, and falling out of the
  level, aren't covered.
- Coming down onto the large walker with the second character: it
  doesn't hurt and doesn't bounce, and the two are pushed apart. This
  also happens during protection. It isn't specified yet, and neither is
  whether it changes when protection ends.
- Contacts with the other character while it stands still, from H to
  H + 36.
- Protection when the first character takes over from the second.
- Any protection after other events (a bounce, a swap, a knock-out).
