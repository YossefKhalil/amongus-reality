# SPEC — Real-World Social Deduction Game

**Title:** Among Us Reality (fan game, unaffiliated) · **Status:** design draft v0.5 · **Code:** none yet

**Name risk, accepted knowingly:** the title uses the Among Us name by choice, following the Among Us Arena precedent. Mitigations that stay in force: repository and package identifiers stay generic (`amongus-reality`), every asset and sound is original, and the "unaffiliated fan game" notice sits on the README and the itch page.

---

## 0. One line

A real-world social deduction game for a field. The phones referee — roles, tasks, kills, votes — and the world is the map. The phone is deliberately a *bad* map.

---

## 1. Pillars

1. **Quality time over screen time.** The phone is a referee, not a surface. Budget: roughly 60 seconds of voluntary screen use per player per 20 minutes, excluding the meeting screen.
2. **No rule depends on a notification.** Every state change is server-timestamped and read when a phone wakes. The only guaranteed channels are **physical** (a hand, a shout) and **on-demand reads**.
3. **Identical UI for everyone.** Every control, empty state and failure message is the same for crew and traitor. The traitor's power lives in *gestures*, not in buttons.
4. **Anonymity creates lies.** The app never says *who*, only *where* and *when*. A tool that names people ends the argument the game is made of.
5. **The world is the map.** GPS attests and entertains the dead. It never navigates and never blocks.
6. **No rule requires a false statement.** See §2.

---

## 2. Truth and deception

This is not a bluffing game. It is a hunting game.

- **Every deception in the design is concealment, not speech.** A hand placed quietly on a shoulder, a long-press on a button, a five-second look. Nothing in the rules asks a player to say anything untrue.
- **Silence is legal and carries no penalty.** Refusing to answer is a valid move, and the rules say so explicitly. Silence is not evidence.
- **There is no interrogation mechanic.** The app never asks a player to account for themselves. The meeting's evidence is the pin map (§11), which the *app* supplies — so nobody has to testify for the crew to have facts.
- **Lying is a losing strategy, by construction.** A pin documents where you stood and when. A false claim about position can be refuted by the app's own record, so the smart traitor says nothing rather than something false.
- **The traitor wins by being unobserved, not by being believed.** The crew's skill is observation and reasoning, not lie-detection.

**The game states its position openly.** The rules page and the itch.io page carry the same line: this game is against lying and does not encourage it. Every deception it contains is a hidden act, never a false statement.

**Read aloud in the lobby, next to the hand-on-the-shoulder rule:**

> You are never required to answer a question. Refusing to answer is a normal move, like passing in a card game. Silence is not evidence.

If the standard a group holds is stricter than this — if even concealment is unacceptable — then the traitor role itself is the problem, and the honest fix is a different game: cooperative deduction, where the hidden thing is a fact about the world rather than a person's guilt, and every player is on the same side. That variant is *not* v1 and is not in this spec.

---

## 3. Session shape

| Property | Value |
|---|---|
| Players | 6 minimum, 6–20 supported, 6–8 for the first test |
| Traitors | 1 per 5–6 players (1 at 6–8, 2 at 10–12, 3 at 14–18) |
| Tasks per player | 3 |
| Stations | players + 2 |
| Task radius | 5–8 m (wider only when GPS is poor) |
| Station spacing | ≥ 30 m |
| Round target | ~25 minutes |
| Field (6–8) | ~70 × 50 m |
| Field (10–12) | ~100 × 65 m |
| Field (14–16) | ~120 × 80 m |

**Field validation is soft only.** Warn, never block:

- area per player below ~250 m² → "too crowded, kills are trivial"
- area per player above ~900 m² → "too big, rounds become walking"
- aspect ratio above ~1.6:1 → "long thin field, the ends are unreachable"
- any two stations more than ~120 m apart → "90-second walk between stations"

---

## 4. Roles and win

- Roles are assigned by the server. Each phone shows only its own, behind a hold-to-reveal.
- **Crew win:** every task completed (living *and* dead), or every traitor ejected.
- **Traitor win:** traitors outnumber the living crew.
- Traitors receive **fake tasks** with identical UI. Fake tasks never advance the crew bar. Completing one arms a sabotage.

---

## 5. Controls — the whole game surface

Three always-visible buttons, identical for every player. Nothing else is on the play screen.

| Button | Tap | Traitor-only gesture |
|---|---|---|
| **Map** | 5 s peek | long-press 1.5 s → **stealth peek**, once per round |
| **Emergency** | Call a meeting. Once per player per match. | hold 3 s → **comms down** |
| **Report** | Open the player list | — |

For a crewmate, every traitor gesture does nothing and produces no feedback. No gesture is remarkable from the outside — from a distance it all reads as someone looking at their phone a beat too long.

**The Report list is the heart of the design.** Always available, lists every player in the match, never marks who is dead. Confirmed bodies are struck from the list only after the meeting where they were found; every other chip stays, alive or undiscovered corpse.

- Crew selects a dead player within ~5 m → **body reported**, meeting called.
- Crew selects anyone else → `No body found nearby`.
- Traitor selects a living player within ~12 m, off cooldown → **kill**.
- Traitor on cooldown, out of range, or selecting a corpse → `No body found nearby` — *the identical message*.

---

## 6. Tasks

**Trigger types, assigned per station by the host:**

- **Zone** — enter the radius, the task opens. Verified by GPS attestation.
- **Tag** — scan a printed QR code. The scan is the proof; GPS adds nothing.

**Rules**

- Mini-games are **chosen at implementation**, not fixed here. Every candidate must be **PWA-friendly** (touch and device sensors reachable from a plain browser, no native API), **one-handed** (the traitor's other hand is on a shoulder), **silent**, 5–10 seconds, and visually unremarkable at 10 metres, so a bystander cannot distinguish a task from a kill.
- Completion is attested: position and timestamp stamped server-side. Poor GPS → **accepted with a warning**. Never blocked.
- Finishing early grants bonus tasks, crew and traitors alike, so early completion is not a tell.
- Live progress is hidden from everyone. The crew bar appears **only on the meeting screen**.

---

## 7. The kill

1. The traitor puts a hand on the victim's shoulder. This is the **guaranteed channel** and the real gate — it cannot be missed, muted, or spoofed by GPS.
2. While touching, the traitor opens **Report** and selects the victim. This is the **record**.

**Server gates:** victim alive · within ~12 m · 30 s since the traitor's last kill · not during a meeting.

**On success:** the victim's phone locks to `eliminated`, names the traitor privately, and asks for acknowledgement. It then becomes a **blank screen: "stand still until found"** — zero interactions, no map, no glow. A corpse looking at a lit phone is a walking tell.

**On failure:** `No body found nearby`. The traitor gets nothing else. The cooldown is enforced silently and policed by the traitor's own clock. Success is read from the victim freezing at arm's length.

A tap without a press, or a press without a tap, is a traitor mistake, not a rules ambiguity: the tap means "you are dead," the press means "it happened."

---

## 8. Bodies and the dead

- On death the victim **freezes instantly** and holds still. A body looks exactly like a player standing at a station.
- A body is reportable only within ~5 m. Bodies never appear on radar.
- A body stays where it fell until found. If it is never found, it stays there for the round.
- **After a body is found**, the dead move to the **meeting area** and stay there. They cannot speak during meetings, and the living may not walk over to chat with them.
- The dead get a **spectator map**: the full field, live, plus ghost tasks. The only live map in the game.
- **Ghost tasks count toward the crew win** and resolve at **double duration**, so dying costs the crew a vote, a reporter and a body — and time.
- The dead do not vote.

---

## 9. Meetings and voting

**Triggers:** a body reported, or an emergency use.

- The report starts the meeting. The start is **stamped server-side**; a phone that wakes late is late, never wrong.
- **The timer starts at the report and runs 60 seconds, or starts immediately if every phone has confirmed presence — whichever comes first.** Presence confirmation gates *voting*, never the clock. A dead battery cannot stall a meeting.
- Discussion is **in person**, phones face down, no in-app chat. The meeting call itself is made by voice.
- The meeting screen shows: the **pin map**, the **crew bar** (a plain progress bar — no numbers, no counts, nothing to read), a **vote dropdown**, and the countdown. Timer is tuneable (default 60 s).
- Votes are secret and changeable until the timer expires. At expiry, non-voters count as skip. Tie = skip.
- The ejected player's role is revealed. A player who merely died has their role revealed to nobody.
- After the meeting: radar charges refill, the pin map clears.

**Never shown:** time of death, a "died here" marker, or any computed cause. The crew reconstructs *when* by asking **"when did you last see him alive?"** — a designed ritual, not an oversight.

---

## 10. The radar

Tap the Map button for a **5-second peek**. Three peeks per meeting window.

- Contacts are **anonymous** and coarse: a zone, never a name, never a precise dot. GPS error must never become evidence.
- Your own position is highlighted.
- **Exposure:** during the peek you appear to *other current peekers* as an anonymous **signal blip** — "a human is looking" — not as your identity. There is **no tail**: when the peek closes, you are gone.
- **Stealth peek:** one per round, traitor only, long-press. No pin, no signal blip, no record of any kind. It never happened.
- Bodies and ghosts produce no blips.
- **Always free, never rationed:** a compass and next-station arrow, so running out of peeks costs information but never participation.

---

## 11. The peek map — the meeting's only evidence

The only thing recorded, all round, is **peeks**. Nothing else — not task scans, not app opens, not kills, not reports.

At a meeting the map shows **pins since the last meeting**: one **dot** per peek, labelled with *who* peeked, *when*, and the **coarse zone** they stood in. That is the entire dataset.

- A pin shows a peeker and a place. It does **not** show what they saw: contacts are live information only, so a peeker can be wrong about them at the meeting and the app will not correct them.
- Pins carry **minute-resolution timestamps**. Two peeks in the same minute render on the same mark, and the map shows that two people were looking during that minute — but never whether their 5-second windows actually overlapped, and never in what order. Only the live signal blip ever showed that, and only to the two people involved, and only at the time. The rules never resolve it, so "did you see me?" stays a question only witnesses can answer.
- Nothing ever shows a body, a trail, a death, or a time of death. The victim's only trace is the last pin near them.
- **Pins double as alibis.** A pin is documentary proof of where you stood. This gives crew a reason to peek beyond curiosity, and gives the traitor a reason to place an alibi deliberately.
- The **stealth peek** is the only hole in the record, which is exactly what makes it precious.
- **Log discipline:** no role-dependent record may ever exist. If a future version logs *killed*, *sabotaged*, or a report-list open, the map becomes a traitor detector and the game dies.

---

## 12. Sabotage

Armed by completing a traitor task. **3-minute cooldown**, random **20–60 second fuse** so the effect can never be tied to the task that caused it. **Never announced** — discovered by use.

| Sabotage | Gesture | Effect |
|---|---|---|
| **Comms down** | hold Emergency 3 s | Radar returns nothing. Scans during the outage don't post to the crew bar until the relay tag is physically scanned. |

*Deferred to v2:* **ghost echo** (60 s of phantom contacts), and the 60-second reactor sprint, which needs a push notification and therefore violates pillar 2.

---

## 13. Setup and onboarding

**Host flow:** join → draw the field polygon on a satellite/tile map (setup only, never in play) → auto-generate stations → drag bad stations off roads and ponds → assign Zone or Tag per station → export a printable Tag sheet (PDF) → open the lobby.

**The host is a player.** No admin powers, no hidden knowledge beyond config and map area. The host may leave. The match ends only by tasks, ejection of all traitors, or traitors outnumbering the crew.

**Lobby checks:** join code or QR, display name, GPS accuracy, clock skew, audio unlocked by a user gesture, battery, iOS silent-switch warning. Rules-of-engagement read aloud here: **the kill involves a hand on the shoulder.**

**Intended group:** adult friend groups who already touch each other casually; the design target is single-gender groups.

**Onboarding:** after the start countdown, the role reveal shows *crewmate* or *imposter* with swipeable instruction cards under it. GIFs are a later pass; the card structure ships first. The traitor's cards teach the one thing no UI can: hand on the shoulder, then Report, then thirty seconds before the next one.

---

## 14. Reliability contract

- Server is the referee; every event is timestamped and authoritative.
- Clients render on open. No rule may break because a phone was asleep.
- GPS failures degrade to warnings plus an honour-system tap. GPS may never say "you may not play".
- Nothing depends on vibration, audio, background execution, or push.
- The alarm is a courtesy. The server is the truth.

---

## 15. Balance levers

| Lever | Default | If traitor too strong | too weak |
|---|---|---|---|
| Peeks per meeting window | 3 | raise | lower |
| Peek duration | 5 s | raise | lower |
| Kill cooldown | 30 s | raise | lower |
| Kill range gate | 12 m | tighten | loosen |
| Report range | 5 m | — | — |
| Ghost task duration | 2× | lower | raise |
| Station spacing | 30 m | tighten | widen |
| Sabotage cooldown | 3 min | shorten | lengthen |
| Meeting timer | 60 s | — | — |
| Stealth peeks per round | 1 | — | — |
| Traitors | 1 per 5–6 | — | second only above 10 players |

---

## 16. Tech and distribution

- **Client:** PWA. Vite + TypeScript. Canvas map, no tile layer in play.
- **Backend:** Supabase — anonymous sign-in, Postgres functions for rules, Row Level Security.
- **No** vibration, no background execution, no push dependency, no native APK in v1.
- **iOS:** second-class via Add to Home Screen. No haptics, ever.
- **Distribution:** GitHub open source + itch.io "name your own price". No store, no ads, no accounts, no paid tiers.
- **Style:** fan-game aesthetic with an explicit "unaffiliated" notice. No copied assets.

---

## 17. Open questions

| # | Question | Status |
|---|---|---|
| — | No blocking design questions remain. | closed |
| a | Mini-game set: decided at implementation, under the PWA-friendly constraints in §6 | by design |
| b | All balance numbers (cooldowns, ranges, timers, spacing) are starting values and need a playtest | by design |
| c | The Among Us name carries a takedown risk. Accepted knowingly; mitigations listed at the top of this spec | risk |

---

## 18. Dropped / non-goals

Vents · in-app chat · QR kills · live other-player dots · decoy buttons · a visible sabotage button · a live shared progress bar · offline mode · Bluetooth · fully virtual mode · host speaker device · per-phone sirens as a rule · vibration · background execution · revealed time of death · precise position dots · GPS navigation · boundary policing · the GPS jammer · the reactor sprint · ghost echo (v2) · cooldown void rules · exposure tails · task/app-open logging · continuous position recording · a cooperative variant · APK in v1 · iOS haptics · accounts · monetisation.

---

## 19. Build order

No dates, no deadlines — only the dependency order, chosen so that the first milestone already produces something two people can walk around a field and test.

**M1 — Walkable.** Lobby with join code, display names and server-side role assignment. One Zone task and one Tag task. The free compass and next-station arrow. A crew bar on a stub meeting screen. *Exit test: two people complete a task each and the bar moves.*

**M2 — The kill.** The three-button surface. The Report list. Hand-on-shoulder plus Report-select, the 12 m gate, the 30 s cooldown. The freeze, the corpse blank screen, the 5 m body report. Meeting start, timer, presence confirmation, vote dropdown, skip and tie handling. Win and loss detection. *Exit test: a full round resolves, both win paths reachable.*

**M3 — The radar.** Three peeks of 5 s. Anonymous coarse contacts. Live signal blips during a peek. The stealth peek. Pins on the meeting map — name, minute, zone. *Exit test: looking costs something and leaving no trace is possible.*

**M4 — The full loop.** The emergency button. Comms down. Attestation warnings on poor GPS. The host setup flow: field drawing, station generation, Tag sheet export. Lobby readiness checks. End-of-round reveal of every role and the full pin map. Onboarding cards behind the role reveal.

**After M4.** Playtest, tune the levers in §15, add the onboarding GIFs, publish the GitHub release and the itch page.