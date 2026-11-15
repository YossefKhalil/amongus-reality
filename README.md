# Among Us Reality

**A real-life social deduction game for parks and fields.** Your phone is the referee — roles, tasks, kills, votes — and the real world is the map. The phone is deliberately a *bad* map.

> **Fan game, not affiliated with Innersloth.**
> Made in the spirit of [Among Us Arena](https://amongusarena.com/). Every line of code, every asset and every sound here is original — no Among Us art, audio or assets are used or included. Free, non-commercial, no ads, no accounts, no paid tiers.

**Status:** design complete, code not started. The full specification is in **[SPEC.md](SPEC.md)** (v0.5).

---

## The game in six lines

The phones referee, the field is the map, and the map is deliberately bad. Three buttons for everyone; the imposter's power lives in gestures — a hand on a shoulder, a long-press, a five-second look. Every look leaves a named dot with a minute on it, forever, which makes looking expensive and makes an alibi something you have to *buy* rather than claim. It's not a bluffing game, it's a hunting game: nobody is ever asked to say anything untrue, silence is legal by rule, and the smart imposter says nothing rather than something false. The dead watch everything and can't speak. And the app never once tells you who — only where, and when.

## How a round plays out

1. **Setup.** The host draws the field on a map and the app auto-generates task stations across it. Bad stations get dragged off roads and ponds. Each station gets a **Zone** task (enter the circle) or a **Tag** task (scan a printed QR code).
2. **Play (~25 minutes).** Everyone gets a role in private, three tasks, and the free compass that points to the next station. Traitors get fake tasks that look and behave identically.
3. **The kill.** No kill button exists. The imposter puts a hand on someone's shoulder — the one guaranteed channel in the whole design — and quietly selects them from the **Report** list. The victim's phone freezes into a blank screen: *stand still until found*. A dead body looks exactly like a player standing at a station.
4. **The meeting.** A body is found (within 5 m) or someone burns their one emergency call. Phones go face down, the app starts a 60-second timer, and the argument happens out loud. The only evidence on screen is the **pin map**: every peek anyone took, labelled with who, when, and roughly where. Nothing else has been recorded all round.
5. **A vote**, a secret one, changeable until the timer dies. Tie means skip.
6. **Win.** The crew finishes every task — the dead's included — or ejects every imposter. The imposters win by outnumbering what's left.

## Design pillars

- **Quality time over screen time.** The phone is a referee, not a surface.
- **No rule depends on a notification.** Every state change is server-timestamped and read when a phone wakes. The only guaranteed channels are physical — a hand, a shout — and on-demand reads.
- **Identical UI for everyone.** Every control, empty state and failure message is the same for crew and traitor.
- **Anonymity creates the argument.** The app never says *who*, only *where* and *when*.
- **The world is the map.** GPS attests and entertains the dead. It never navigates and never blocks.
- **No rule requires a false statement.** See below.

## On lying

This is not a bluffing game, it is a hunting game, and that distinction is load-bearing.

Every deception in the design is **concealment, not speech** — a hand placed quietly, a long-press on a button, a five-second look. The rules never ask a player to assert anything untrue. Silence is explicitly legal and carries no penalty. There is no interrogation mechanic anywhere: the app never asks anyone to account for themselves, and the meeting's facts come from the pin map, which the *app* supplies.

Lying is a losing strategy by construction. A pin documents where you stood and when, so a false claim about position is refuted by the game's own record. The smart imposter says nothing rather than something false. **The game is against lying and does not encourage it.**

## What is deliberately *not* in it

No vibration. No background execution. No push notifications. No offline mode. No in-app chat. No live dots showing where other players are. No boundary policing. No GPS navigation. No accounts, no analytics, no monetisation. No native app — it is a PWA, so it runs everywhere without a store.

## Build plan

No dates, only dependency order. Each milestone ends with something two people can walk around a field and test.

| Milestone | Contents | Exit test |
|---|---|---|
| **M1 — Walkable** | Lobby, join code, server-side roles, one Zone task, one Tag task, compass to next station | Two people complete a task each and the bar moves |
| **M2 — The kill** | Three-button surface, Report list, hand-on-shoulder kill, the freeze, body report, meetings and voting, win detection | A full round resolves, both win paths reachable |
| **M3 — The radar** | 3×5 s peeks, anonymous coarse contacts, live signal blips, the stealth peek, pins on the meeting map | Looking costs something, and leaving no trace is possible |
| **M4 — The full loop** | Emergency button, comms-down sabotage, poor-GPS warnings, host setup flow, Tag sheet export, end-of-round reveal, onboarding | A stranger can be handed the phone and play |

## Tech

- **Client:** PWA — Vite + TypeScript, canvas map, no tile layer in play.
- **Backend:** Supabase — anonymous sign-in, Postgres functions for the rules, Row Level Security. The server is the referee.
- **Platforms:** Android and iOS alike, added to the home screen. iOS is second-class: no haptics, ever.

## Repo layout

```
SPEC.md      the complete design document (authoritative)
README.md    this file
LICENSE      MIT
```

## Contributing & money

Nothing is built yet, so the most useful thing you can do right now is **read SPEC.md and argue with it**. Issues about the design are welcome before any code exists.

The project is free and stays free. If it turns out to be fun, donations are optional.

## License

MIT — see [LICENSE](LICENSE). Not affiliated with or endorsed by Innersloth.
