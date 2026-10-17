# Trace vocabulary (v1)

Engine-neutral names for player states and events. Both sides map their
own internal states onto these. Add names as scenarios need them; never
rename or reuse a name, since existing traces refer to it.

## States

| Name | Meaning |
|---|---|
| `idle` | Grounded, not moving horizontally |
| `walk` | Grounded, moving, action not held |
| `run` | Grounded, moving, action held |
| `roll` | Roll attack in progress |
| `jump` | Airborne and rising after a jump |
| `fall` | Airborne and descending, or airborne without having jumped |
| `bounce` | Airborne after bouncing off an entity |
| `hurt` | Taking damage, control suspended |
| `swap` | Switching lead character |
| `ground_action` | Down + Action ground action in progress (`../behavior/ground-action.md`) |
| `swim` | Swimming underwater: in open water, at a wall or on the floor (`../behavior/swimming.md`, "In a trace") |

## Events

| Name | Meaning |
|---|---|
| `jump_start` | A jump began this frame |
| `jump_release` | Jump input released while still rising |
| `land` | Became grounded this frame |
| `leave_ground` | Stopped being grounded without jumping (walked off a ledge) |
| `roll_start` | A roll began this frame |
| `roll_end` | A roll ended this frame: the first frame after the roll, whose state is no longer `roll` |
| `bounce` | Bounced off an entity this frame |
| `hurt` | Took damage this frame |
| `turn` | Facing direction changed this frame |
| `stroke` | A swimming stroke began this frame (`../behavior/swimming.md`) |
