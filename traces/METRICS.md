# Metrics (v1)

Metrics are computed from a trace the same way for both sides, so their
definitions must be exact. "Frame" means a trace row. If any event a
metric's definition names never occurs where required (including the
closing `land` for `apex_height`, `airtime` and `bounce_height`, and the
`jump_start` for `coyote_frames`), the metric is **missing**, and a missing
metric fails the comparison unless both traces are missing it.

| Name | Definition |
|---|---|
| `apex_height` | For the first `jump_start`: the largest value of `-y` from that frame until the next `land`, minus `-y` on the `jump_start` frame |
| `airtime` | Frames from the first `jump_start` to the next `land`, counting the `land` frame but not the `jump_start` frame |
| `top_speed` | The largest `abs(vx)` in the trace |
| `frames_to_top` | Frames from the first frame where `vx` is non-zero to the first frame where `abs(vx)` equals `top_speed` |
| `roll_distance` | For the first `roll_start`: `x` on the next `roll_end` frame minus `x` on the `roll_start` frame |
| `coyote_frames` | Frames from the first `leave_ground` to the next `jump_start`, if that jump happens before any `land`; missing otherwise. Scenarios measuring the ledge-jump window run once per candidate frame |
| `bounce_height` | For the first `bounce`: the largest value of `-y` until the next `land`, minus `-y` on the `bounce` frame |
| `final_x` | `x` on the last frame |
| `final_y` | `y` on the last frame |

Height windows ("from that frame until the next `land`") include both
the starting frame and the `land` frame. A trace that ends before the
closing `land` makes the metric missing; the window never runs to the end
of the trace instead.

## Curves

`curve FIELDS from EVENT for N tol T` aligns the two traces on the first
frame carrying `EVENT` in each, then compares `FIELDS` on that frame and
the following `N - 1` frames. Positions are compared relative to the
aligned frame (`x - x₀`, `y - y₀`); velocities are compared directly.
If either trace ends before `N` frames, the comparison fails.

If `EVENT` never occurs in one trace, the curve fails. If it occurs in
neither trace, the curve passes, the same rule as for missing metrics.

The first frame where any field is outside tolerance is reported as the
**first divergence**.
