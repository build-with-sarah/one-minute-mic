# Handoff: One Minute Mic

## Overview
One Minute Mic is a single-page browser game (no navigation, no account). You get a random
topic and 60 seconds to talk about it out loud. The mic listens for one thing only: whether
you are still talking. Go silent longer than the configured pause tolerance and you are
buzzed out. Survive the full minute and you bank a clean minute and your day streak goes up.

Tone: buzzer game show. Loud, fast, funny when you lose. Explicitly NOT a wellness app,
speech coach, or productivity dashboard.

## About the Design Files
`One Minute Mic.dc.html` in this bundle is a **design reference created in HTML** — a static
board document showing all seven states side by side at their real pixel sizes. It is not
production code to copy. The task is to **recreate these designs in the target codebase's
existing environment** (React, Vue, Svelte, whatever is in use) using its established
patterns. If no codebase exists yet, pick a framework — a single React + Vite page with the
Web Audio API is sufficient; there is no backend.

The HTML file is a "Design Component" board doc: seven fixed-size boards stacked vertically
with mono labels. Read the inline styles for exact values; every number below is taken from it.

## Fidelity
**High fidelity.** Final colors, typography, spacing, and copy. Recreate pixel-close.
Animation timings and the audio behaviour are specified in prose below because a static board
cannot show them.

## Design Tokens

### Colors
| Token | Hex | Use |
|---|---|---|
| ink | `#0c0818` | page behind boards, inset panels |
| shell | `#140d24` | primary app background |
| shell-raised | `#2b1b4d` | chips, pills, empty meter track |
| line | `#4a3a72` | borders, keycaps, dim meter bars |
| line-dim | `#3a2c5c` | faintest meter bar |
| lime | `#d9ff3d` | primary accent, voice meter, win screen bg |
| magenta | `#ff2e88` | danger / silence / buzz screen bg |
| cyan | `#2ee6ff` | secondary accent, countdown screen bg |
| white | `#ffffff` | primary text on dark |
| text-muted | `#a99ccc` | body text on dark |
| text-dim | `#8b7fb0` | mono labels on dark |
| on-lime | `#3d4a08` | secondary text on lime bg |
| on-magenta | `#5c0630` | secondary text on magenta bg |
| on-cyan | `#0b3a44` | secondary text on cyan bg |

Note: on colored full-screen states (buzz, win, countdown) the dark ink `#140d24` is used for
headline type and buttons, and the matching `on-*` tone for supporting copy.

### Typography
- **Display / all numbers**: Anton, 400 only. Uppercase. Negative letter-spacing that scales
  with size (-2px at 88px, -8px at 220px, -12px at 230px, -14px at 440px).
  **Important**: Anton's ink box overflows a tight line box. Use `line-height: 1` for very
  large digits and give them explicit clearance (≈10px) above and below, or the glyphs
  collide with neighbouring labels.
- **UI / body**: Space Grotesk 400/500/700.
- **Labels, readouts, keycaps**: Space Mono 400/700, uppercase, letter-spacing 1–8px
  (wider spacing on shorter labels: 8px on "SECONDS LEFT", 2–4px on section labels, 1px on
  stat rows). Sizes 11–16px.

Google Fonts: `family=Anton&family=Space+Mono:wght@400;700&family=Space+Grotesk:wght@400;500;700`

### Spacing & shape
- Board padding: 56px 64px (most boards), 36px 56px (live minute), 32px (narrow).
- Grid gaps: 48–56px between major columns, 10–22px within stacks.
- **No border radius anywhere.** Every surface is a hard rectangle. This is deliberate.
- Emphasis is done with flat color blocks, 2–3px solid borders, `outline` + `outline-offset`
  for selection, and small rotations (`rotate(1.5deg)` / `rotate(-2deg)` / `rotate(2deg)`)
  on the streak badge, the win card, and the BZZZZT wordmark.
- One shadow only, on the board frames themselves in the doc (`0 40px 90px rgba(0,0,0,.5)`);
  the app itself uses no drop shadows.

### Keyframes
```css
@keyframes pulse { 0%,100% { opacity:1 } 50% { opacity:.35 } }
```
Applied at `1s infinite` on the "LISTENING" indicator and `.6s infinite` on the silence
warning text.

## Target viewport
Designed for 1440 x 900, full window, no centered narrow column. Board 07 proves the layout
at 620 x 900. Between those widths, collapse the live minute's two-column layout
(timer | voice meter) into a stacked single column and let the voice meter go full-width
and short. Below ~560px is out of scope.

## Screens / Views

### 01 — Starting up (lobby)
**Purpose**: choose topic decks, choose pause tolerance, see your streak, start.

Layout: 1440x900, `display:grid; grid-template-rows:auto 1fr auto`, padding 56px 64px.
A decorative 620x620 `#2b1b4d` circle, `filter:blur(10px)`, positioned top:-160px right:-120px.

- **Header row** (space-between):
  - Wordmark "One Minute / Mic" — Anton 88px, line-height .86, white with "Mic" in lime.
    Below it Space Mono 15px `#a99ccc`: "Talk for 60 seconds. Stop talking and you're out."
  - **Streak badge**: magenta block, padding 18px 26px, `rotate(2deg)`, flex gap 18px.
    Anton 72px "12" in `#140d24`, next to Space Mono 12px/1.5 700 uppercase
    "day / streak / ●●●●●".
- **Middle** (`grid-template-columns:1fr 400px; gap:48px; align-content:center`):
  - Left column, gap 14px:
    - Mono 13px label "Pick your topic decks" (letter-spacing 3px).
    - Deck chips, wrapping flex gap 14px, padding 12px 18px, Space Grotesk 700 18px, with the
      card count in Space Mono 13px at 50–60% opacity. Selected = lime bg / `#140d24` text
      (Office Life 48, Embarrassing 31) or cyan (Deep End 19); unselected = `#2b1b4d` bg /
      `#a99ccc` text (Hot Takes 27, Nonsense 64, Childhood 22).
    - Mono 13px label "How long can you pause?" (margin-top 12px).
    - Four pause buttons, flex gap 12px, padding 14px 20px, Anton 26px value with a Space Mono
      11px caption under it: 0.8s BRUTAL / 1.5s HOUSE RULES / 2.5s GENEROUS /
      4.0s BARELY A GAME. Selected (1.5s) = magenta bg, `#140d24` text,
      `outline:4px solid #d9ff3d; outline-offset:3px`. Others `#2b1b4d` / `#a99ccc`.
  - Right column — **Mic check panel**: `#0c0818` bg, `2px solid #2b1b4d`, padding 26px, gap 16px.
    Mono 12px "Mic check"; a live level meter (11 bars, 12px wide, gap 5px, 84px tall,
    align-items flex-end, lime with one cyan peak bar, inactive bars `#4a3a72`); Space Grotesk
    16px white "Say anything. If the bars move, it can hear you."; a 1px `#2b1b4d` rule; then a
    Space Mono 12px/1.8 stat block — BEST RUN 9 CLEAN MINUTES / BANKED 41 MINUTES /
    WORST EXIT 2.4 SECONDS (that last value in magenta).
- **Footer row** (space-between):
  - Lime CTA block, Anton 32px `#140d24`, padding 18px 56px 20px: "HIT SPACE TO GO".
    Beside it Space Mono 13px `#8b7fb0`: "no account, no saving, / nobody is listening but you".
  - Keycap legend, right: `SPACE` START, `B` BUZZ ME OUT. Keycap style: padding 10px 20px,
    `2px solid #4a3a72` with `border-bottom-width:5px`, white mono text, letter-spacing 2px.

### 02 — The topic, revealed
Radial background: `radial-gradient(circle at 50% 45%, #33205c 0%, #140d24 62%)`.
- Top row, Space Mono 13px `#8b7fb0` letter-spacing 3px: "Office Life · card 12 of 48" and
  "pause allowed **1.5s**" (value in magenta).
- Centered stack, gap 34px: lime Space Mono 15px letter-spacing 6px "Your topic is";
  the topic in Anton 150px, line-height .92, white, `letter-spacing:-3px`, `max-width:1180px`,
  `text-wrap:balance` — "A meeting that should have been an email"; then two buttons
  (cyan "GIVE ME THIS ONE" Anton 26px padding 16px 30px; outlined `2px solid #4a3a72`
  "NEW TOPIC ↻"); then Space Mono 13px `#8b7fb0` "2 skips left today".
- Footer: "STREAK **12 DAYS**" left; `SPACE` LOCK IT IN right.

### 03 — Three, two, one
Full cyan background `#2ee6ff`.
- Top: the topic, Space Mono 14px 700, `#0b3a44`, letter-spacing 4px, uppercase.
- Center, flex gap 64px: the current count in Anton 440px `#140d24` letter-spacing -14px ("2"),
  and to its right a column showing the sequence — Anton 96px "3" at 28% opacity `#0b3a44`
  (already spent), Anton 96px "1" in magenta (next), Anton 60px `#140d24` "TALK!".
- Footer, Space Mono 14px 700 `#0b3a44`: "DEEP BREATH. DON'T PLAN. JUST START." and "MIC LIVE ●".

Behaviour: one count per 700–800ms, each number scaling down from ~1.15 with a hard ease-out;
the big number is the live count, the side column is the on-deck sequence. Advance straight
into the live minute with no transition — the cut should feel abrupt.

### 04 — The live minute (HERO, drawn mid-panic, half drained)
Rows: `auto 1fr auto auto`, gap 16px, padding 32px 56px, background `#140d24`, plus a danger
vignette overlay: absolutely positioned, `box-shadow: inset 0 0 220px 70px rgba(255,46,136,.42)`.
The vignette's alpha should ramp 0 → .42 as the silence meter drains, so panic is ambient.

- **Header**: the topic in a `#2b1b4d` pill (Space Grotesk 700 22px, padding 12px 20px), then
  Space Mono 12px `#8b7fb0` "ROUND 1 · STREAK 12". Right: "● LISTENING", Space Mono 14px 700
  magenta, letter-spacing 3px, `animation: pulse 1s infinite`.
- **Middle** (`grid-template-columns:1fr 300px; gap:48px; align-items:center; min-height:0`):
  - Left: Space Mono 16px `#8b7fb0` letter-spacing 8px "SECONDS LEFT" (margin-bottom 10px);
    the clock in **Anton 230px, line-height 1, white, letter-spacing -12px** ("23"); then a
    row (margin-top 18px, gap 14px) with a 14px-tall `#2b1b4d` track filled 62% lime, and
    Space Mono 13px `#8b7fb0` "37 / 60 SURVIVED".
  - Right — **voice meter** ("it hears you"): lime Space Mono 12px letter-spacing 4px label;
    a `#0c0818` box with `2px solid #2b1b4d`, 210px tall, padding 14px, containing 12 bars
    14px wide, gap 6px, `align-items:flex-end`, heights driven by live amplitude, lime when
    above the talking threshold and `#4a3a72` / `#3a2c5c` when below; caption Space Mono 12px
    `#8b7fb0` centered: "volume doesn't matter. / only whether you're talking."
- **Silence panel**: `#0c0818`, `3px solid #ff2e88`, padding 18px 24px, gap 10px.
  Anton 34px magenta "SILENCE — 0.8s LEFT" (counts down live), and right-aligned Space Mono
  14px 700 magenta "SAY SOMETHING. ANYTHING." with `animation: pulse .6s infinite`.
  Below: a 36px-tall `#2b1b4d` track with a magenta fill (53% in the mock — meaning ~0.8s of
  the 1.5s remains) and three inset tick dividers (`2px solid rgba(12,8,24,.55)`) splitting it
  into quarters. Under it, Space Mono 12px scale labels 0.0s / 0.5s / 1.0s / "1.5s — BUZZ"
  (last one magenta).
  The fill drains left-to-right in real time and snaps back to full the instant voice returns.
- **Footer**: Space Mono 13px `#8b7fb0` "PAUSES USED THIS ROUND **4** · LONGEST **1.2s**";
  right, keycap `B` BUZZ ME OUT.

### 05 — Buzzed out
Full magenta background `#ff2e88`.
- Top row, Space Mono 14px 700 `#5c0630` letter-spacing 4px: the topic; "silence detected at 00:05".
- Center: **"BZZZZT!"** in Anton 230px `#140d24` letter-spacing -8px, `rotate(-2deg)`;
  Anton 62px white "FIVE SECONDS. FIVE." (this line is generated from how long they lasted —
  see copy rules); Space Grotesk 26px 500 `#5c0630`, max-width 760px:
  "You went quiet for 1.6 seconds, which is 0.1 more than you agreed to. Rules are rules."
- Three stats, flex gap 44px, Space Mono 13px 700 `#5c0630` labels over Anton 56px `#140d24`
  values: LASTED 00:05 / STREAK 12 → 12 / TODAY'S ATTEMPTS 7.
- Two buttons, Anton 32px, padding 20px 44px: "SAME TOPIC, AGAIN" (`#140d24` bg, lime text)
  and "NEW TOPIC" (lime bg, `#140d24` text).
- Footer: Space Grotesk 20px 700 `#5c0630` "Streak survives until midnight. You have time.";
  right, dark keycap `SPACE` GO AGAIN (on colored screens keycaps invert: `#140d24` bg, white text).

Never scolding. Mock the situation, not the player.

### 06 — You made it
Full lime background `#d9ff3d`.
- Top row, Space Mono 14px 700 `#3d4a08`: "One Minute Mic · 19 aug 2026" and
  "zero buzzes. one clean minute."
- `grid-template-columns:1fr 460px; gap:56px`:
  - Left: "YOU / SURVIVED" in Anton 220px `#140d24` line-height .8 letter-spacing -8px;
    Space Grotesk 28px 500 `#3d4a08` max-width 640px: "Sixty seconds on **"A meeting that
    should have been an email"** and you never once stopped talking." (topic bolded in `#140d24`);
    two buttons Anton 30px padding 20px 40px — "SCREENSHOT THIS" (`#140d24`/lime) and
    "ONE MORE MINUTE" (magenta/`#140d24`).
  - Right — **the shareable card**: `#140d24`, padding 40px, gap 28px, `rotate(1.5deg)`.
    Space Mono 12px `#8b7fb0` "BANKED"; Anton 120px lime "13" beside Anton 34px white
    "DAY STREAK"; a row of 7 26px squares gap 7px (6 lime = previous days, last cyan = today);
    1px `#2b1b4d` rule; Space Mono 14px/2.1 `#a99ccc` stats — CLEAN MINUTES 42 /
    LONGEST PAUSE 1.3s / CLOSE CALLS 3 (magenta) / ATTEMPTS TODAY 8, values white 700.
    This card is the screenshot target: keep it self-contained and legible when cropped alone.
- Footer: "Tomorrow's deck unlocked: **Hot Takes**"; keycaps `SPACE` GO AGAIN, `B` BUZZ ME OUT.

### 07 — Live minute, narrow window (620 x 900)
Same states, one column. Rows `auto 1fr auto auto`, gap 20px, padding 32px, same magenta
vignette at `inset 0 0 160px 50px`.
- Header becomes two stacked lines: the mono meta row (ROUND/STREAK left, ● LISTENING right)
  above the full-width topic pill (Space Grotesk 700 17px, padding 10px 14px).
- Center: mono 12px letter-spacing 6px "SECONDS LEFT"; **Anton 180px line-height 1** clock
  (margin-bottom 10px); the voice meter becomes a full-width horizontal box 112px tall with
  `flex:1` bars, gap 5px; mono 11px "37 / 60 SURVIVED" under it.
- Silence panel condenses: Anton 28px "SILENCE — 0.8s LEFT", 32px drain bar (no tick labels),
  then the pulsing "SAY SOMETHING. ANYTHING." at mono 11px.
- Footer: "PAUSES 4" left, `B` BUZZ ME OUT keycap right (smaller: padding 8px 14px, 4px bottom border).

## Interactions & Behavior

### Keyboard (show the keys on screen at all times)
- **Space** — start a round; on the topic screen, lock the topic in; on end screens, go again.
- **B** — buzz yourself out immediately (works during the live minute; the legend is visible
  on the lobby and end screens too).
- Prevent Space from scrolling or re-triggering focused buttons.

### Voice detection
- `getUserMedia({audio:true})` → `AudioContext` → `AnalyserNode`, `fftSize` 1024ish.
- Per frame (`requestAnimationFrame`) compute RMS. Smooth with a short EMA (~0.2 rise,
  ~0.4 fall) so the bars feel snappy but not jittery.
- Calibrate a noise floor over the first ~500ms of the mic-check panel; "talking" =
  smoothed RMS above floor by a fixed margin. Volume never matters beyond that threshold —
  say so in the UI (it is copy on board 04).
- Voice meter: map the last N smoothed frames to the bar heights, newest at the right, so it
  scrolls like a scope. Bars below threshold render in the dim colors.

### The silence timer
- While talking: silence remaining = the configured tolerance (full bar), and the danger
  vignette is at 0.
- On silence: count down in real time (bar drains, Anton label counts down to one decimal,
  vignette ramps up, the pulsing warning appears).
- On voice return: snap the bar back to full immediately — the recovery should feel like relief.
- On reaching zero: buzz. Fire the buzzer sound, cut to board 05 with no transition.

### Round clock
60s, integer seconds displayed. At 0 → board 06. Never pause the clock.

### Motion
- Countdown numbers: scale 1.15 → 1, 120ms ease-out, one per ~750ms.
- Screen changes: hard cuts, no fades. The one exception is the win screen, which can
  flash white for ~80ms on entry.
- BZZZZT wordmark: 2–3 quick ±3px horizontal shakes over ~180ms on entry.
- Respect `prefers-reduced-motion`: drop the shake, the flash, and the pulse animations;
  keep the meters (they are information, not decoration).

### Audio out
A buzzer on loss and a short rising ding on survival. Both can be synthesized with an
`OscillatorNode` (buzzer: square wave ~110Hz, 400ms, slight detune; win: two-note triad).

### Failure states to handle
- **Mic permission denied / dismissed**: the lobby's mic-check panel becomes the blocker —
  same panel, magenta border, Anton headline "NO MIC, NO GAME", a button to retry, and one
  line explaining nothing is recorded or uploaded. Space does nothing until permission lands.
- **No input detected while a round runs** (mic muted at the OS level): treat as silence — the
  drain does its job and the buzz explains itself.

## State Management
Single page, all state client-side. No backend, no account.

```
screen: 'lobby' | 'topic' | 'countdown' | 'live' | 'lost' | 'won'
decks: Set<string>              // selected deck ids, at least one required
pauseTolerance: 0.8 | 1.5 | 2.5 | 4.0   // seconds, default 1.5
topic: string                   // drawn from the union of selected decks
skipsLeft: number               // per day, starts at 3
secondsLeft: number             // 60 → 0
silenceLeft: number             // pauseTolerance → 0, resets on voice
talking: boolean
levels: number[]                // rolling amplitude window for the meter
pausesUsed: number, longestPause: number   // this round
attemptsToday: number
streak: number, lastPlayedDate: string     // localStorage
cleanMinutes: number, bestRun: number, worstExit: number  // localStorage
```

Transitions: lobby --Space--> topic --Space--> countdown --auto--> live;
live --silenceLeft<=0 or B--> lost; live --secondsLeft<=0--> won;
lost/won --Space--> topic (new draw) or countdown (same topic).

Streak: increment only on the first survived minute of a calendar day; a loss never decrements.
"Streak survives until midnight" is literal — the streak breaks only when a day passes with
no clean minute. Persist in localStorage; there is no server.

## Content
Real topics, one line each, written to be talkable-about by anyone. Ship at least 30 per deck.
Seed examples from the design:
- Office Life: "A meeting that should have been an email"
- Deep End: "Silence is a skill", "Something you are genuinely bad at"

Copy rules:
- The loss headline is generated from elapsed time and should be funny about the number, not
  the person: under 10s → "FIVE SECONDS. FIVE." style incredulity; 10–40s → matter-of-fact;
  50s+ → "SO CLOSE IT HURTS".
- The loss body always states the arithmetic: how long they went quiet vs. what they agreed to.
- Never coaching, never tips, never "try to relax".

## Assets
None. No images, no icons, no illustration — the design is type, flat color blocks, and
CSS-drawn meters. The only external dependency is the Google Fonts link. Glyph characters
used inline: ● ↻ → " " and — .

## Files
- `One Minute Mic.dc.html` — the seven-board design reference (open it in a browser; boards
  are labelled 01–07 top to bottom, each at its real pixel size).
