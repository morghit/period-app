# Phase Up: product plan (v1)

> **Phase Up.** The cheat code to her cycle.

Status: draft for review. Updated 2026-10-01.

## 1. The product in one paragraph

Phase Up is a mobile app (iPhone and Android) that helps guys understand and
support their girlfriend or partner through her menstrual cycle. It shows which
phase she is in, predicts her next period, and every day gives him a short,
plain-language briefing on what she may be feeling plus concrete things he can
do about it. Completing those "quests" earns XP, levels, and badges, and she
can confirm them for bonus points. A Playbook teaches the basics (and what not
to say), and a Care Kit helps him stock the right supplies before he needs them.

**Audience.** Open to everyone, no age gate. Marketing, tone, and pricing target
guys aged 18 to 29 in a relationship.

**Tone.** Mature gamer. Game language adults still use (Hard Mode, quest, XP,
level up, loadout, checkpoint, co-op, achievement unlocked). No kid slang (spawn,
noob, "GG ez"). Dry, confident humor. **Jokes are always at his expense, never
hers.** She is never the "boss" or the "enemy": he is leveling up to deserve her.

**Inspiration.** The "Stay Flexy" partner guide (movementbydavid). All Phase Up
content is original and corrected where the video oversimplifies. We will
approach the creator about promoting the app, not about using his content.

## 2. Core concepts

### 2.1 The Clock and the Switch

The signature visual, on the home screen:

- **The Clock (outer dial)** is her cycle as a clock face. Three colored arcs
  show the uterus phases: *Menses* (bleeding), *Rebuild* (lining grows back),
  *Build-up* (lining thickens). A glowing hand points at today.
- **The Switch (center)** is the ovaries: a toggle reading **Follicular** or
  **Luteal**. On ovulation day it flips, with an animation and a haptic buzz.
  "The switch flipped" is a notification and a shareable moment.
- Around the dial, the six **modes** (below) are marked so he can see what's
  coming next.

### 2.2 Modes (what he sees each day)

The textbook has four phases. Phase Up splits them into six modes so the advice
fits the day. All modes scale to **her** cycle length, not a fixed 28 days.

Let `L` = her cycle length, `P` = her period length (default 5),
`O` = estimated ovulation day = `L - 14`.

| Mode | Days (formula) | Days in a 28-day cycle | Textbook phase | Color |
|---|---|---|---|---|
| **Hard Mode** | 1 to 2 | 1 to 2 | Menstrual | Crimson |
| **Recovery Run** | 3 to P | 3 to 5 | Menstrual | Coral |
| **Power Up** | P+1 to O-4 | 6 to 10 | Follicular | Green |
| **Peak Window** | O-3 to O | 11 to 14 | Ovulation | Violet |
| **Steady State** | O+1 to L-7 | 15 to 21 | Luteal | Cyan |
| **Patience Check** | L-6 to L | 22 to 28 | Luteal (PMS) | Amber |

Two extra states:

- **Overtime**: today is past the predicted period window. Calm copy, no
  speculation (see content outline).
- **No Signal**: no cycle data yet. Prompts him to send her the link or enter
  what he knows.

Rules for unusual cycles (each one needs a unit test):

- Menstrual modes always win over any overlap (short cycles where `O-3 <= P`).
- An empty mode (for example Power Up in a 21-day cycle) is skipped, not shown
  as zero days.
- Cycle lengths are clamped to 21 to 45 for mode math. Outside that range the
  app shows phases with a "less certain" flag instead of guessing.
- Predictions are always shown as a range ("Period in 3 to 5 days"), using the
  existing forecast engine's uncertainty.

### 2.3 Signal strength

Every prediction carries a game-style accuracy meter:

| Signal | When |
|---|---|
| **Strong** | She shares through the link (or health sync) and there are 2+ logged cycles |
| **Medium** | She shares, but only one cycle is known; or he logs 3+ cycles manually |
| **Weak** | He entered one date from memory |

Weak signal shows a gentle nudge: "Better intel = better calls. Send her the link."

## 3. How she shares (her side)

| Option | Effort for her | Version |
|---|---|---|
| **Link / QR code web page** (default) | Nothing to install. A few taps a month. | v1 |
| **"I'm her" mode** in the app, synced from Apple Health / Health Connect (where Flo, Clue, and Apple Cycle Tracking can save her period dates) | One install, then automatic | v1.1 |
| **He enters what he knows** | None | v1 |

### Her web page (v1)

Opened from a link he texts her or a QR code she scans. No account, no install,
can be saved to her home screen.

1. **Pair.** "Alex wants to show up better. Share your cycle with him."
   Clear list of what he will see (phase, predicted dates, what you choose to
   share today) and what he never sees. "Stop sharing anytime."
2. **Set up.** Last period start (required). Usual cycle length and period
   length (optional, "not sure" allowed).
3. **Her page, afterwards:**
   - **"It started"** button (the most important tap of the month).
   - **Today's vibe** (optional): chips like *Need space*, *Need a hug*,
     *Snacks please*, *Low energy*, *Cramps are bad*, *Feeling great*.
   - **His quests**: recent completed quests with **Confirm** (bonus XP) and
     quick reactions ("Nailed it", "So sweet", "Ok that was smooth").
   - **Help him help you**: her favorites (snack, chocolate, drink, flowers,
     comfort show, pain relief she uses, pad/tampon/cup preference).
   - **Stop sharing**: deletes the shared data immediately.

### Privacy model

- Data is encrypted on the device before upload. The key lives in the part of
  the link after `#`, which browsers never send to servers, and on the two
  phones. Our relay only stores unreadable blobs. Marketing line:
  **"Even we can't see her data."**
- Reuses the pattern in `workers/backup` (opaque encrypted blob relay) and
  `app/src/crypto/vault.ts`.
- She can revoke at any time. Revoking deletes the blob and his app falls back
  to Weak signal with a respectful message.

## 4. Gamification: Boyfriend Level-Up

### XP

| Action | XP |
|---|---|
| Small quest (a text, a compliment) | 10 to 15 |
| Medium quest (cook, movie night, restock) | 20 |
| Big quest (plan and book a real date) | 25 to 30 |
| She confirms a quest | +50% of that quest |
| She sends a reaction | +5 |
| Playbook card read / quiz answer right | 5 |

Daily cap: 100 XP (keeps it about consistency, not farming).

### Levels

| Level | XP | Title |
|---|---|---|
| 1 | 0 | Rookie |
| 2 | 100 | Tuned In |
| 3 | 250 | Reliable |
| 4 | 500 | Clutch |
| 5 | 900 | Day One |
| 6 | 1,500 | MVP |
| 7 | 2,500 | Legend |

### Streaks

A streak day = at least one quest completed. A missed day during Power Up or
Steady State doesn't break the streak (life happens); missed days in Hard Mode
or Patience Check do. Copy: "Checkpoint saved."

### Badges (v1 set)

| Badge | How |
|---|---|
| **Hard Mode Hero** | Complete a Hard Mode quest |
| **Chocolate Runner** | 3 snack quests during a period |
| **Full Cycle** | At least one quest in all six modes in one cycle |
| **Good Listener** | 5 "listen without fixing" quests |
| **Prepared** | Care Kit fully stocked before Hard Mode starts |
| **Certified** | Finish the Playbook basics |
| **Myth Buster** | 10 quiz answers right |
| **Confirmed** | First quest she confirms |
| **Co-Op** | She's connected through the link |
| **On Fire** | 7-day streak (then 30, 90) |

She can see his level, badges, and quest history when connected.

### Later (v1.1): her rewards

She sets real rewards he unlocks at levels ("Your pick for date night", "I've
got dinner", "Massage voucher"). Strong candidate for the Pro tier.

## 5. Screens

### His app (5 tabs)

| Tab | Screen | What's on it |
|---|---|---|
| **HQ** | Home | Clock and Switch dial; mode card (headline, day X of ~L, next period range, signal strength); her vibe today (if shared); **Today's Quest** + 2 side quests with Done; XP bar, level, streak |
| | Mode detail | What she may be feeling (body / mind), Do, Avoid, linked Playbook card |
| **Forecast** | Calendar | Month view colored by mode, predicted period window, "It started" button (manual mode), cycle history |
| **Playbook** | Education | Cards: how it works, myths, "Say this, not that"; Myth or Fact quiz |
| **Kit** | Care Kit | Checklist by category, readiness meter, shop links with affiliate disclosure, restock reminder |
| **Rank** | Progress | Level, XP, badges, quest history (with her confirmations and reactions) |

Also: **Onboarding** (welcome, her nickname, get intel: send link or enter
dates, notifications opt-in, 20-second Clock and Switch tutorial),
**Her Favorites** (what he knows about her; merged with what she shares),
**Settings** (sharing, notifications, discreet mode, backup, disclaimer).

### Her web page

Pair, Set up, Her page (see section 3).

## 6. Day-by-day content outline

Every mode has: headline, "what she may be feeling" (body and mind), quests,
an Avoid list, a Playbook link, and Care Kit tie-ins. Language always says
"may" and "often": every body is different.

### Hard Mode (period, days 1 to 2)

- **Headline:** Hard Mode. Bring chocolate. Leave opinions at the door.
- **Body:** Cramps are often worst now. They come from prostaglandins, chemicals
  that make the uterus contract. Lower back pain, headaches, fatigue, bloating,
  sometimes nausea.
- **Mind:** Estrogen and progesterone are at their lowest. Energy is low;
  comfort beats plans.
- **Quests:**
  - Chocolate run: her favorite, delivered, no questions asked (20)
  - Heat pad, warmed and delivered (15)
  - Take one chore off her plate tonight (20)
  - Swap tonight's plans for a night in, without making it a thing (15)
  - Ask "What would help right now?" then do exactly that (15)
- **Avoid:** "Is it that time of the month?" Jokes about her mood. Plans that
  need her to be "on".
- **Kit:** pads / tampons / cup (her preference), the pain relief she uses,
  heat pad.

### Recovery Run (period, days 3 to P)

- **Headline:** Recovery run. Heat pad, comfort food, low-key night in.
- **Body:** Flow usually lightens and cramps ease, but she may still be tired.
- **Mind:** Mood often starts lifting toward the end of the period.
- **Quests:**
  - Cook or order her comfort meal (20)
  - Movie night, her pick, phones away (15)
  - Set up the cozy corner: blanket, tea, her show (15)
  - Short walk together, only if she's up for it (10)
  - Mid-day text that isn't a question: "Thinking about you. Dinner's handled." (10)
- **Avoid:** "You should be fine by now, right?"

### Power Up (follicular)

- **Headline:** Energy's climbing. Plan the date. Then actually listen.
- **Body:** Estrogen is rising. Energy and stamina are often up.
- **Mind:** Often more social, optimistic, up for new things.
- **Quests:**
  - Plan a real date: you pick, you book, you handle the details (30)
  - Try something new together (25)
  - Ask about something she's working on, then ask a follow-up (15)
  - Active date: hike, climbing, a class (20)
  - Phone face down for all of dinner (10)
- **Avoid:** "I dunno, what do you want to do?"

### Peak Window (ovulation)

- **Headline:** Peak window. Flirt hard. You know what that means.
- **Body:** Estrogen peaks and an LH surge triggers ovulation. Libido is often
  at its highest. Some feel a twinge on one side (called mittelschmerz).
- **Mind:** Often confident, social, flirty.
- **Quests:**
  - Compliment something specific, not generic (15)
  - Dress up for her (15)
  - Leave a flirty note or send a flirty text (15)
  - Plan a night out (25)
  - Slow dance in the kitchen. Yes, really. (15)
- **Fine print (always shown):** Follow her lead. This is also when pregnancy
  is most likely. Phase Up predictions are not birth control.
- **Avoid:** Making it only about sex. Pressure of any kind.

### Steady State (early luteal)

- **Headline:** Steady state. Hang out, compliment her. Lots.
- **Body:** Progesterone rises. She may feel calmer, sleepier, hungrier.
- **Mind:** Settled, homebody energy.
- **Quests:**
  - Plan a cozy hangout at home (15)
  - Three genuine compliments today (15)
  - Do the thing she mentioned last week (25)
  - Cook together (20)
  - Early night together (10)
- **Avoid:** Overbooking the calendar.

### Patience Check (late luteal / PMS)

- **Headline:** Patience check. Snacks stocked, reassurance on repeat. This is
  not the week to win an argument.
- **Body:** Estrogen and progesterone drop. Bloating, breast tenderness,
  headaches, cravings, poorer sleep.
- **Mind:** PMS can bring irritability, anxiety, sadness, feeling overwhelmed.
  It's real: hormone shifts affect brain chemistry, including serotonin.
- **Quests:**
  - Restock the Care Kit before Hard Mode hits (25)
  - Snack drop: her current craving, zero commentary (15)
  - Tell her one thing you love about her, unprompted (15)
  - Listen without fixing: 10 minutes, no advice unless asked (20)
  - Handle dinner (20)
- **Avoid:** "Are you PMSing?" Dismissing how she feels. Big serious talks that
  can wait a week.
- **Note:** If it's severe every month and disrupts her life, PMDD is a real,
  treatable condition. Support her talking to a doctor. Never diagnose.

### Overtime (period later than predicted)

- **Headline:** Overtime. Cycles run late sometimes.
- **Copy:** Stress, travel, sleep, and illness can all shift a cycle. Stay chill
  and don't speculate out loud. Follow her lead.
- **Quests:** Be normal. One kind check-in. Make her favorite thing happen.

### No Signal (no data)

- **Headline:** No signal.
- **Copy:** Get intel: send her the link, or add what you know.

### Daily variety

Each mode gets a pool of quests and a rotating "Pro tip" line, so two days in
the same mode don't look identical. v1 target: **6 to 8 quests per mode**
(about 45 total) and **5 Pro tips per mode**.

### Playbook (education) v1

**How it works**
1. The Clock and the Switch (the two cycles running at once)
2. Why cycles aren't always 28 days
3. What actually happens at ovulation (egg goes to the fallopian tube; the
   leftover follicle, the corpus luteum, makes progesterone)
4. Hormones 101: estrogen and progesterone in plain English
5. Cramps, explained
6. PMS is real (and what PMDD is)

**Myths**
7. "She's just being emotional"
8. "Periods sync up when you live together" (the evidence is weak)
9. "You can't get pregnant on your period" (unlikely, not impossible)
10. "Every cycle is 28 days and ovulation is day 14"

**Say this, not that**
11. Never: "Are you on your period?" Try: "Rough day? What can I do?"
    Never: "Calm down." Try: "I'm here."
    Never: "Have you tried stretching?" Try: bringing the heat pad.
    Never: "Is it hormones?" Try: "That sounds really frustrating."
12. When to suggest a doctor (very heavy bleeding, severe pain, big changes),
    and how to say it kindly

**Myth or Fact quiz**: 15 questions in v1, XP for right answers.

### Care Kit v1

| Category | Items |
|---|---|
| Essentials | Pads / tampons / cup / period underwear (her preference), the pain relief she uses (we never suggest doses), heat pad |
| Comfort | Cozy socks, hoodie, tea, bath stuff, eye mask |
| Food | Her favorite snack, dark chocolate, her comfort meal, water bottle |
| Extras | Her comfort show queued up, flowers, a handwritten note |

Readiness meter fills as items are checked. Restock reminder fires at the start
of Patience Check.

**Affiliate links:** Amazon Associates and Instacart first, then Target, Walmart,
DoorDash, or others as approved. Every shop screen shows a clear disclosure.
Most programs need a live app or site before approving, so links can launch
shortly after v1.

## 7. Notifications (opt-in, local in v1)

- Mode heads-up: "Hard Mode in about 2 days. Check your loadout."
- The switch flipped (ovulation day estimate)
- Restock reminder (start of Patience Check)
- Daily quest at a time he picks (off by default)
- Manual mode: "Did her period start?" when the predicted window passes
- **Discreet mode** (on by default): lock-screen text stays generic
  ("New quest available").

## 8. Free vs Pro

v1 launches **free**, with affiliate links. Pro arrives in v1.1 with price tests.

| Free | Pro (v1.1, to test) |
|---|---|
| Tracker, Clock and Switch, daily briefings | Full quest library and personalization |
| Starter quests, XP, levels, badges | Her custom rewards |
| Playbook basics and quiz | "I'm her" mode with health sync |
| Care Kit with shop links | Widgets, themes, cycle recaps |
| Sharing link | |

Pricing test: annual plan vs lifetime (one-time) purchase. Use RevenueCat (it
wraps App Store and Google Play billing and supports price experiments).

## 9. Technical plan

The existing codebase (React + TypeScript + Capacitor, iOS and Android shells)
is a good base.

**Keep**
- Forecast engine: `app/src/engine/cycle.ts`, `cycleForecast.ts`, `stats.ts`
  and their tests, including the 360-history estimate audit
- Date utilities, local database (Dexie), reminder recurrence engine
- Native bridges: notifications, secure vault, Apple Health / Health Connect
  (for v1.1), widgets (for v1.1)
- Crypto (`app/src/crypto/vault.ts`) and the relay pattern (`workers/backup`)

**Add**
- `engine/modes.ts`: mode math from section 2.2, with tests for cycle lengths
  21 to 45 and short/long periods
- `content/quests.ts`, `content/playbook.ts`, `content/careKit.ts`
- `state/progress.ts`: XP, levels, streaks, badges
- New screens: HQ, Forecast, Playbook, Kit, Rank, Onboarding, Her Favorites
- `workers/share`: two-way encrypted mailbox (her cycle data to him; his quest
  activity to her)
- `web/her`: the small web page she opens from the link

**Remove or park**
- Her-side deep logging, pregnancy, TTC, perimenopause, doctor report, safety
  router, AI assistant. (The assistant could return later as a Pro "Ask the
  coach: what should I say?" feature.)

**Rebrand**
- App name, icons, splash, bundle ID (for example `app.phaseup.mobile`,
  depending on the domain we get), README.

## 10. Build order

Each milestone ends with something you can install and try on your iPhone and
Android phone.

| # | Milestone | Done when |
|---|---|---|
| 0 | **Setup and rebrand** | App is called Phase Up, unused features removed, all tests still green |
| 1 | **Modes and HQ** | Manual onboarding, mode math with tests, Clock and Switch home, mode card, mode detail |
| 2 | **Quests and Level-Up** | Quest library, Done, XP, levels, streaks, badges, Rank tab |
| 3 | **Playbook and Care Kit** | Education cards, quiz, Care Kit checklist with shop links |
| 4 | **Sharing** | Share relay, link and QR pairing, her web page, "It started", today's vibe, confirm and reactions syncing |
| 5 | **Notifications and polish** | Local notifications, discreet mode, empty states, accessibility pass, privacy-friendly analytics (no health data) |
| 6 | **Beta** | TestFlight and Google Play internal testing with 10 to 20 couples; fix and tune |
| 7 | **Launch** | Store listings, screenshots, privacy labels, disclaimers, affiliate approvals, creator outreach |
| 1.1 | **Pro and her mode** | RevenueCat, price tests, her rewards, health sync, push notifications, widgets |

## 11. Risks and decisions

1. **License.** The base code is AGPL-3.0 and its README credits another author
   (Blueturboguy07). A paid, closed-source app on the stores can't ship AGPL
   code it doesn't own. Before launch: confirm who owns it, get the author's
   written permission, or replace the reused parts (mostly the forecast engine)
   with our own code. **Needs your answer.**
2. **Medical accuracy.** Have a nurse or doctor review the content before
   launch. Keep "may" and "often" language. Never diagnose.
3. **Not birth control.** Shown in onboarding, Peak Window, and the store listing.
4. **Store review.** Health-related apps get extra scrutiny. Apple doesn't allow
   HealthKit data to be used for advertising, so Care Kit suggestions must
   never be driven by health-sync data.
5. **Her consent.** Manual mode is allowed but always nudges toward her sharing.
   Copy never encourages tracking her secretly.
6. **Age rating.** No age gate in the app; the store rating comes from the
   content questionnaire at submission.
7. **Name.** Run the USPTO search for "Phase Up", grab a domain, and reserve the
   name in App Store Connect early.

## 12. Open questions

1. Who owns the original codebase (see risk 1)?
2. Company or developer name for the store listings?
3. Push notifications (needs a small server) in v1, or keep v1 local-only?
4. Any must-have quest ideas from your own experience to add to the library?
