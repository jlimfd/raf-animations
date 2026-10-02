# Refer a Friend: completion celebration

A self-contained HTML prototype of the Refer a Friend (RaF) **Complete** screen. It shows how the screen celebrates a finished referral the first time someone sees it, then settles into a quiet, static state.

It is a design prototype, not production code. Everything lives in one file with no build step or dependencies.

## Viewing it

Open `index.html` in a browser (double-click it). The page shows a phone frame with two buttons below it:

- **↻ First visit** plays the celebration.
- **Return visit** shows the finished, static state.

The celebration plays once per browser. After that the page opens in the return-visit state, which is how the real feature should behave. Two URL options help with reviews:

| URL | Behaviour |
|---|---|
| `index.html?celebrate` | Always plays the celebration |
| `index.html?view=return` | Always opens the finished state |

On a phone-width screen the prototype fills the viewport and the buttons are hidden.

## The celebration

One short arc, about 3 seconds, then nothing moves. The reward card is the event, not the whole screen.

| Time | What happens | Why |
|---|---|---|
| 0.05s | "Complete" pill fades in and its check draws itself | Confirms the status first |
| 0.55s | A soft brand-blue light blooms behind the $50 card, then fades | Marks the card's arrival without moving it |
| 1.15s to 2.55s | A tapered blue comet makes one lap of the card, leaving a faint hairline | Draws the eye around the reward |
| 2.55s | Ring closes: hairline brightens, $50 settles, small stars pop off the card edges, success haptic fires | The single peak of the sequence |
| ~3.15s to 4.15s | Hairline fades out; the card rests borderless like the others | The ring belongs to the moment, not the resting state |

Timing constants are in the `T` object in `index.html`. The lap uses `cubic-bezier(.45, 0, .25, 1)`.

### Return visits and reduced motion

Return visits, and anyone with **Reduce motion** turned on, see the finished state straight away: borderless card, no animation, no haptic.

## Haptics

One haptic per celebration, fired in the same frame the ring closes and the stars appear.

| Platform | Call |
|---|---|
| iOS | `UINotificationFeedbackGenerator().notificationOccurred(.success)`; call `prepare()` about 1s earlier. SwiftUI (iOS 17+): `.sensoryFeedback(.success, trigger:)` |
| Android | `view.performHapticFeedback(HapticFeedbackConstants.CONFIRM)` (Android 11+), falling back to `CONTEXT_CLICK` |

- First visit only. Never on return visits, scroll, or re-render.
- Skip it when Reduce motion is on.
- When system haptics are off, the OS mutes it. Do not add a custom fallback.

One modest success tap keeps the celebration proportionate, which matters in a gambling product.

The web prototype approximates this with `navigator.vibrate`. It only works in Android Chrome after the user has tapped the page. iOS Safari does not support vibration, so iPhone reviewers will not feel it.

## Design tokens

All colours, type, spacing and radii come from the **Formation Design System**, mode **FD Base Dark**. They are defined as CSS variables at the top of `index.html`.

| Use | Token |
|---|---|
| Page / card | `background/base` #0A0A0A, `background/surface` #1C1D1D |
| Text | `content/strong` (headings), `content/default` (body), `content/subtle` (labels) |
| Success | `system/positive/content/accent`, `/background/subtle`, `/border/default` |
| Comet, bloom | `brand/primary/default`, `link/default/base` |
| Type | Inter (`primaryFont`), weights 400 / 700, Formation type ramp |
| Spacing | `space1` to `space8` (4 to 32px) |
| Corners | `borderRadiusRounded` 8px, `borderRadiusCircle` for the pill |

### Known gaps

- The step rings and connector lines use `system/positive/border/default`, which is 2.08:1 on the card, below the 3:1 target for graphics. The check marks carry the meaning at 8.8:1.
- Icons are hand-drawn SVGs. Production should use Formation's UI icon library.
- Only the dark theme is built. Formation's FD Base Light mode would map one-to-one.
- Inter loads from Google Fonts and falls back to the system font offline.

## Files

| File | Purpose |
|---|---|
| `index.html` | The prototype |
| `README.md` | This file |
