# Cambridge Battlecode 2026 Bot

**Round of 32** in the International qualifier, out of 830 international competitors (top 4%). Team HBH.

Everyone on the team was busy with exams or on vacation for most of the season, so we couldn't fully focus on the bot. Most of the work ran through an automated loop instead: AI coding agents built and tested changes, a game queue on rented servers played the matches, and we approved what shipped.

## The game

Teams draft 3 to 5 champions, farm camps for gems and gold, and race to tribute 1,000 gems at an altar. Each champion runs its own copy of the bot (C++ compiled to WASM) with a budget of 100k instructions per turn, and the whole team shares only 8 bytes of memory.

## Final bot: v170

```text
v170 = v161 base (built on a teammate's bot)
     + camp scoring retune
     + fight_d 4: only start a fight when the nearest enemy is within 4 tiles (was 6)
```

It won +0.104 ± 0.031 more games than v161 over 700 paired games on held-out maps.

## Technical details

- **Paired testing on unseen maps.** Each candidate played the base on the same maps from both seats, and was judged on win share against a bar set before the read. The qualifier uses unseen maps, so the maps we tuned on never judged a build.
- **The game is chaotic.** The engine is deterministic, but the bot reads its own remaining fuel, so a rebuild with no code change flipped 36-38% of results. One game says nothing.
- **Fuel.** An overrun suspends the bot mid-turn. Every release needed zero overruns on 135 maps, and heavy work moved into the draft turn's larger budget.
- **Teamwork in 8 bytes.** Team mode, camp locks and muster points are packed into one team word. Enemy sightings are shared through the low bits of each champion's priority integer.
- **Real opponents over local tests.** v150 looked fine locally and went 4-21 on the ladder. It was rolled back in about 70 minutes.
- **Win share over proxies.** Builds that died less but didn't win more were not shipped.

## Numbers

5 weeks, 16,000+ commits, ~1,400 experiment branches, ~31,000 platform tests, site versions v124 to v170.
