# 🤖 Enemy AI Behaviour
### FrontLine Framework — Design Document

---

## Overview

The enemy AI is designed to feel reactive and tactical rather than aggressive and omniscient. Enemy units operate within strict rules governed by the Radio Signal Link system and capture point states — ensuring the AI feels like a believable opposing force rather than a scripted wave spawner.

---

## Core AI Behaviour Rules

> ⚠️ **Fundamental Rule:** Enemy AI will ONLY push a capture point if it is actively in the `BeingCaptured` state AND within an active enemy base signal radius.

| Situation | Enemy AI Response |
|---|---|
| Capture point is `Neutral` | No response |
| Capture point is `PlayerOwned` | No response — AI will not attack secured points |
| Capture point is `BeingCaptured` and signal-linked | Counter-attack dispatched from nearest active enemy base |
| Capture point flips to `PlayerOwned` before AI arrives | Counter-attack order cancelled, units return to base |
| No active enemy base is signal-linked to the point | No response regardless of capture state |

---

## AI Unit Types

### Infantry Squads
- Standard enemy infantry in groups of 4–8 units.
- Used for garrison defence and counter-attack responses.
- Equipped with rifles, machine guns, and anti-tank weapons.

### Mechanized Units
- APCs and IFVs used to support larger counter-attacks.
- Deployed from bases with vehicle pools.
- Provide fire support and troop transport during pushes.

### HQ Defenders
- Dedicated heavy garrison at the Enemy Main HQ.
- Large platoons of mixed infantry and mechanized units.
- Static emplacements (HMGs, AT guns, mortars).
- Do not leave the HQ perimeter under any circumstances.

### Patrols
- Small infantry teams that patrol between active enemy bases and depots.
- Patrol routes are limited to **enemy signal-linked territory only**.
- Patrol frequency increases as enemy loses territory (escalating pressure).

---

## Counter-Attack Behaviour

When a counter-attack is triggered:

1. The nearest active enemy base with a signal link to the contested point is identified.
2. A counter-attack squad is assembled from that base's unit pool.
3. Units move toward the capture point via road/path network.
4. If the point flips to `PlayerOwned` before arrival → orders cancelled, units return.
5. If units reach the point while still `BeingCaptured` → units enter the zone and contest capture.

### Counter-Attack Composition (by base tier)

| Base Type | Infantry Squads | Mechanized Units |
|---|---|---|
| Small FOB | 1 squad (4–6 units) | None |
| Medium Base | 2 squads (6–8 units) | 1 APC |
| Large Base | 3 squads (8–10 units) | 1–2 APCs / IFVs |
| Main HQ | Does not send counter-attacks — HQ defenders hold position only |

---

## Garrison Behaviour

- Every active enemy base maintains a **permanent garrison**.
- Garrison units hold defensive positions within the base perimeter.
- Garrison units will engage players who enter or approach the base.
- Garrison strength is replenished over time if the base remains active.

| Base Type | Garrison Size |
|---|---|
| Small FOB | 4–6 infantry |
| Medium Base | 8–12 infantry + 1 vehicle |
| Large Base | 12–18 infantry + 2 vehicles |
| Enemy Main HQ | 20–30 infantry + 4+ vehicles + static emplacements |

---

## Enemy Main HQ

The Enemy Main HQ is the final objective and is designed to be a significant challenge:

- **Heavily fortified** with pre-placed bunkers, sandbags, wire and emplacements.
- Large mixed garrison — infantry platoons, APCs, IFVs and static weapons.
- HQ garrison units **do not leave** the HQ perimeter.
- No counter-attacks are dispatched from the HQ — it is purely a defensive strongpoint.
- Players must breach and hold the HQ capture zone to trigger the **win condition**.

---

## AI Configuration

| Parameter | Default | Description |
|---|---|---|
| `AISkillLevel` | Medium | Overall AI accuracy and reaction time |
| `CounterAttackDelaySeconds` | 30s | Delay before counter-attack units depart |
| `GarrisonReplenishIntervalSeconds` | 120s | Time between garrison reinforcement checks |
| `PatrolRadiusMeters` | 500m | Max patrol distance from base |
| `EscalationThreshold` | 50% territory lost | Point at which patrol frequency increases |

> All values are configurable via the scenario config file.

---

## Related Systems

- [Capture Point System](./capture-point-system.md)
- [Radio Signal Link System](./radio-signal-link.md)
- [Base & HQ System](./base-hq-system.md)
