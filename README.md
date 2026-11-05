# Cambridge Battlecode 2026 Bot Technical Report

## Final Result

Cambridge Battlecode 2026 (Autumn) is over for us.

Our team, **HBH**, advanced to the **Round of 32 of the International qualifier**, out of **830 international competitors (top 4%)**, with an autonomous strategy bot written in C++.

The bot that played the qualifier was:

```text
v170 (fd4)
```

Some context first. Everyone on the team was busy with exams or away on vacation for most of the season, so none of us could focus on the bot full time. That shaped the whole project. Instead of hand-tuning the bot in long sessions, we built an experimentation platform that could keep working while we couldn't: AI coding agents wrote and tested candidate changes, a shared game queue on rented servers played the matches, and we stepped in to approve designs and uploads.

The main lesson was that in a deterministic but chaotic game, most local "improvements" are noise or proxies. The changes that survived were the ones that won more games on maps the bot had never seen, against opponents it was not tuned on, over hundreds of paired games.

## Project Summary

This repository documents the development, testing and ship decisions behind our Cambridge Battlecode 2026 bot.

We treated it as an experimentation problem as much as a bot build. The core work was:

- building a reproducible experimentation platform (BattleLab) with a shared game queue across one laptop and two rented servers;
- keeping a C++ bot inside a hard per-turn instruction budget on every map, including unseen 100x100 maps;
- running paired A/B reads (both seats, same maps) on held-out maps, with ship bars written down before the read;
- judging candidates against real opponents through the competition site's scrims, with a statistical keep/revert rule;
- decoding the ladder's replays to find where we lost games, by map type;
- refusing features that looked right in theory but did not win more games.

Season in numbers (29 Sep to 4 Nov):

| | |
|---|---:|
| Commits to the platform and bot repo | 16,000+ |
| Experiment branches | ~1,400 |
| Platform tests | ~31,000 |
| Site bot versions, 23 Oct to 31 Oct | v124 to v170 |
| Keyed switches on the integration train | 100+ |
| Engine patches absorbed mid-season | 3 (to 1.0.2, 1.0.3 and 2.0.0) |
| Game jobs queued at once in the final week | ~20,000 |

## The Game

Cambridge Battlecode 2026 Autumn is a team strategy game played by bots. In short:

- Each team **drafts 3, 4 or 5 champions** (the map sets the size) from 12, picking blind each draft round.
- Champions kill neutral camps (wolves, elk, gem and gold clusters, golems, a monolith) for **gems and gold**, carry the gems back and **tribute them at an altar**.
- The first team to **1,000 tributed gems** wins at once. Otherwise the game ends at **round 2,000** and goes to tributed gems, then lifetime gold, then champion kills.
- Gold buys **shards** (30 gold, one unit of a stat); five shards of a family combine into an **emblem** with a passive.
- Fights use basic attacks, two spells with cooldowns, stuns, shields, dashes, wards and recalls.

The constraints that shaped the bot:

- **Every champion runs its own instance of the bot.** There is no central controller.
- **The whole team shares 8 bytes of memory** for the entire game, plus 1,024 bytes written during the draft and read-only afterwards.
- **Compute is an instruction budget (WASM fuel):** 100,000 per turn, 200,000 on a champion's first turn, 10,000,000 on the first draft turn (engine 2.0.0). An overrun suspends the bot mid-code and the rest of its actions land a turn late.
- **The engine is deterministic:** the same two bots on the same map play the same game.
- **The organisers patched the game every few days**, and the qualifier and finals use **unseen, hand-made maps** from 30x30 to 100x100.

More detail in [`docs/game-rules.md`](docs/game-rules.md).

## Final Bot

Final submission:

```text
v170 (fd4)
= v161 backbone (our reference base since 30 Oct)
+ combo100r28: camp scoring on, camp reserve 28,000, smoothing law off
+ fight_d 4: no fight while the nearest enemy is more than 4 tiles away (was 6)
```

It started from the bot one of our teammates wrote (`qifq`), which we adopted as the base on 22 Oct and improved with measured fixes. v161 was the best-proven version of that line. v170 kept it and changed only two things that passed the ship rule together.

Ship evidence (win share difference vs the v161 base):

| Read | Result |
|---|---:|
| Pre-registered ship read, held-out maps, 700 paired games | **+0.104 ± 0.031** |
| Re-read on held-out maps, one v161 baseline | +0.093 (95% CI +0.056 to +0.129) |
| vs v161 and three clones of it | +0.058 (95% CI +0.030 to +0.087) |
| Real ladder opponents, 250 pinned pairs | +0.056 ± 0.043 |
| Real opponents ranked 2 to 25 only | +0.043 ± 0.050 |

Fallback hierarchy:

| Role | Bot |
|---|---|
| Final live submission | `v170` (fd4) |
| Reference base and armed rollback | `v161` |

Upload rules every version had to meet:

```text
zip = the exact source of the wasm that was tested
built with SDK 4.0.0 (engine 2.0.0)
pre-registered rule read once, on the planned number of games
a team member's go-ahead for that specific upload
rollback armed before the upload (cambc submissions activate 161)
```

## Live Competition Results

### Ladder

The ranked ladder ran all season with sprint locks along the way (sprint 1 locked 19 Oct, sprint 2 on 24 Oct, then a submission lock before the qualifier). We treated the ladder as a measuring instrument rather than a scoreboard: experimental builds went live on safety gates only, and we read them with paired scrims.

On 1 Nov we sat around **rank 63 at a rating of ~2006**, with the top-8 line around 2335, so we went into the qualifier as an underdog.

### Qualifier

The International qualifier on **4 Nov** was a best-of-5 double-elimination bracket, with the top 8 going to the finals on 18 Nov.

```text
Round of 32, out of 830 international competitors (top 4%)
```

The bracket plays unseen maps, which is why we had judged every late build on maps it had never been tuned on.

## Version Lineage

| Version | Main change | Status |
|---|---|---|
| v3 / v64 lines (Sep to mid Oct) | Our first bots on the BattleLab platform | Superseded |
| K8 rebuild (21 Oct) | From-scratch rebuild after engine 1.0.2 | Paused |
| qbase (22 Oct) | A teammate's bot `qifq` as the base, measured fixes on top | Adopted |
| v120 | Shared enemy sightings between teammates | Kept |
| v121 | Carry funnel: supports leave camp captures to the carry champion | Kept |
| v122 | Champion-specific item builds, zero fuel overruns, draft precompute | Kept |
| v123 | Compressed team search (depth 3) | Kept |
| v124 | The team search may not override a fight call when outnumbered | Kept |
| v125, v127 | Second-emblem choices for Vesper and Heart of Ruin | Kept |
| v128 to v142 | One day of rapid tuning (wards, idle rules, bank fixes, bag ambush) | v140 to v142 lost on real scrims |
| v143, v144 | Re-upload of v139, then v142 + an island-wait fix once the two read level on scrims | v144 was the sprint 2 final |
| v145, v146 | Stun walk, patch-proof item code | Kept |
| v147 to v149 | First build on engine 2.0.0, runtime fuel budgets, a correctness wave | v149 kept |
| v150 | Large correctness and value bundle | Went 4-21 on the ladder; rolled back in ~70 min |
| v151 to v158 | Ladder A/B tests against v151, fixes-only releases | v156 to v158 passed release checks |
| v159 to v161 | Ladder experiments (capture mix, lock memory, ambush freshness, deny walk) | v161 became the reference base |
| v162 to v169 | Unified value-model line and a teamwork bundle (team mode, muster, locks, shared sight) | v161 re-activated on 30 Oct |
| v170 | v161 backbone + camp scoring retune + `fight_d 4` | Final submission |

Every upload with its note is in [`docs/version-history.md`](docs/version-history.md).

## Key Technical Findings

### 1. A no-op rebuild flips a third of the results

The engine is deterministic: the same two bots on the same map always play the same game. But the bot reads its own remaining fuel, so anything that shifts instruction counts changes decisions. A rebuild with no code change flipped **36-38% of game results**. Even building the same source from a different header path changes 122 bytes of exception layout, which is enough to move fuel use.

Final rule:

```text
One game says nothing.
Judge on paired reads of hundreds of games.
Repeats of an unchanged bot are not evidence.
Compare builds only when they were built the same way.
```

### 2. Fuel is a design constraint, not a clean-up job

An overrun does not crash the bot, it suspends it mid-turn, so the late half of a turn's actions lands a turn later. That is worse than a crash in a fight. Every release had to show zero overruns at `-O3` on 135 maps from both seats.

What worked:

- moving heavy work into the draft turn, whose budget was 10x a normal turn (100x after patch 2.0.0), and storing the results in the 1,024-byte draft memory;
- per-feature fuel guards: a bag-ambush feature caused 30 overruns on our stress maps and shipped only with an 80k fuel guard, which cut that to 16;
- treating fuel cost as part of a feature's price, not something to fix after it wins.

### 3. Teamwork through 8 bytes

With one bot instance per champion and 8 bytes of shared memory, coordination is a compression problem. We packed a team word with a team mode, camp locks and muster points.

Shared sightings (v120) used a side channel instead: each champion publishes the enemies it sees in the low bits of its turn-priority integer, with its real rank kept in the high bits so turn order does not change. Later we found that the opponent can read those integers too. The cheap fix is to XOR the low bits with a key kept in draft memory, which the opponent never sees.

### 4. Measure the patch, don't trust the docs

The engine changed three times during the season. After the first patch we adopted a hard rule: **old research is a hypothesis, not evidence.** Only measurements on the current engine that passed a verifier counted. We kept one game-facts file where every row names its source and engine version.

Probes found several things the published rules did not say:

| Finding | Evidence |
|---|---|
| Every Bruiser emblem adds +1 to the other stats, not only the first | 9,371 combine events, 11 champions |
| A stun whose duration reads 0 is already spent | Victim's next turn was free in 27 of 27 cases |
| A diagonal dash over one enemy is refused; a straight dash over one enemy lands | Probe games on engine 2.0.0 |
| The Capture emblem caps the remaining capture timer at 3 while its holder stands on the camp | Probe games on engine 2.0.0 |
| Prospector's +25% is floored once per body on its whole share | Several thousand reward events |
| A 3.0.5 SDK build crashes every turn on engine 2.0.0 | Same source built with both SDKs; we pinned 4.0.0 |

### 5. Local opponents flatter you

Several changes looked strong locally and lost against real opponents:

| Change | Local | Real opponents |
|---|---|---|
| v140 to v142 | +61 ± 23 vs v137 (368 games) | -51 / -98 / -64 on top-4 scrims |
| v150 bundle | uploaded on local reads | 4-21 on the ladder; it fought more and lost |
| `entry_hp 1.5` | +19 | kept us off boss camps that top teams contest; local bots don't contest |

Final rule:

```text
Local panels only screen (they can reject, never accept).
Keep decisions come from held-out maps and real-opponent reads.
```

### 6. Win share, not proxies

Two late candidates improved the numbers we hoped would matter and still did not win more games:

| Candidate | Proxy | Win share |
|---|---|---:|
| `fh600` | 1.12 fewer deaths per game | +0.017 (95% CI -0.017 to +0.051): no-go |
| `combo100r28` alone | 0.94 fewer deaths, +12.9 gems | +0.011 ± 0.015, below the +1 se bar: not shipped alone |
| `combo100r28` + `fight_d 4` (= v170) | | **+0.104 ± 0.031: shipped** |

### 7. The smallest change that passes wins

`fight_d` is one number: the bot does not start a fight while the nearest enemy is farther than this. Going from 6 to 4 was a blunt fix for arrival maths that was wrong at 5-6 tiles. The proper fix, a forecast of who arrives when, was designed but not finished before the lock, so the one-number change is what shipped.

## Rejected Experiments

### From-scratch rebuild

Rejected for the season. A cleaner rebuild after engine 1.0.2 was paused once our teammate's bot proved clearly stronger. An earlier attempt had already shown the cost: a new bot lost clearly to our older v64 on unseen maps, and an ablation showed the loss came from the new core, not from overfitting.

### Full overhaul onto one value model

Parked. The idea was sound: let one value model price every decision instead of a stack of rules. In practice the overhaul started 285 points behind v161 on our gem-margin read and never caught up before the lock. We moved to v161's backbone with priced overrides on top instead.

### Large bundles

Rejected after v150. Bundling many correctness and value changes went 4-21 on the ladder within the hour. Later bundles still shipped, but only behind release checks and an armed rollback.

### Counters for specific teams

Parked. Countering one top team at a time looked attractive, but results between bots were not transitive: beating one opponent's style often lost to another's. We judged on a broad panel instead.

### Per-map hardcoding

Banned early on. The qualifier uses unseen maps, so every rule is keyed on a map-type classifier (team size and map features), never on a map's identity, and is read on its own map type.

## Testing Methodology

### 1. Paired reads, both seats

Every comparison ran the candidate and the control on the same maps, from both seats, against the same opponents. The score is the win share difference with its standard error, with maps as the unit.

### 2. Held-out maps

Candidates were tuned on one pool and judged on a fresh held-out pool, then confirmed on a separate holdout set. Maps were weighted to the live ladder's mix of team sizes (3, 4 and 5 champions: 17.7%, 23.4% and 58.9%).

### 3. Pre-registered ship bar

```text
Ship only if the candidate beats the base by at least +1 se on held-out maps,
deaths do not go up,
and it holds on the confirmation holdout.
```

The rule and the number of games were written down before the read. A read that ended short of the bar meant "not shown better", never "keep it anyway".

### 4. Real-opponent scrims

Scrims on the competition site only play the live submission, so a candidate's real-opponent read needs its upload. We paired scrims by map and seat across versions and judged with:

```text
REVERT  margin z <= -1.645, or McNemar one-sided p(worse) <= 0.05
KEEP    margin z >= +1.645, or McNemar p(better) <= 0.05,
        with no co-primary below -1 se and no team below -2 se
else    NOT RESOLVED: the build stays, but nothing new ships on that basis
```

A read was valid only with at least 20 complete paired maps against each top-4 team, and we stayed inside the site's scrim quota.

### 5. Factorial reads

Features interact. Isolated A/B tests were a safety screen only; the value of a feature came from a factorial or leave-one-out read on the combined build.

### 6. Never trust a negative result too early

A "no difference" read was reported with its standard error, minimum detectable effect, base and opponent field, and a losing idea was re-tested on the current base before it was dropped for good.

## Infrastructure

**BattleLab** is the platform the bot loop ran on: a Python 3.12 library with about 31,000 tests, a task board shared between devices, one git worktree per experiment, and a shared game queue. At peak it ran on one laptop and two rented servers (12 and 16 game slots), downloaded 800+ replays an hour, and kept a decoded database of the ladder's matches (130,000+ matches in one dump) with per-team stat profiles.

Because none of us could sit with the bot full time, most of the code was written and measured by **AI coding agents**, mostly Claude Code sessions, with Codex and DeepSeek workers added in the final week. Each agent worked in its own worktree on one task, and a separate adversarial verifier agent checked the work against a written gate before it counted. We set the direction, approved feature designs and gave the go-ahead for uploads, apart from a few overnight windows where builds could ship on the gates alone.

One incident set a permanent rule. During a dry run, an audit agent ran a real rollback and put an older version live. It was caught within five minutes and the right build was re-activated. From then on, agents could never upload, roll back or activate a submission.

## Safety

Every release was checked for:

| Check | Requirement |
|---|---|
| Fuel at `-O3` | 0 overruns, 135 maps, both seats |
| Fuel at `-Os` | at most 2 overruns |
| First turn | at most 170k fuel |
| Later turns | at most 80k fuel |
| Debug output | quiet build prints nothing |
| SDK | 4.0.0 (engine 2.0.0) |
| Build identity | the uploaded zip is the exact source of the tested wasm |
| Rollback | armed before the upload |

## Final Retrospective

Final submission:

```text
v170 (fd4)
```

Final result:

```text
Round of 32 in the Cambridge Battlecode 2026 International qualifier,
out of 830 international competitors (top 4%)
```

Why this bot shipped:

```text
v170
= v161 backbone (our best-proven base)
+ camp scoring retune (combo100r28)
+ fight_d 4
```

It cleared the pre-registered bar on held-out maps, held up against real ladder opponents, and changed little on top of a base we trusted.

What we would do differently:

- **Fewer parallel lines.** At one point three bot lines, a rebuild and an overhaul competed for the same compute and attention. The final bot came from the oldest, most measured line.
- **Real opponents earlier.** Local panels flattered us more than once. The scrim judge and held-out maps should have been in place from week one.
- **Watch the process overhead.** Automation let a busy team keep the bot improving, but gates, boards and rules grew faster than the bot did. Late in the season we had to cut process work to get back to bot work.

The research takeaway is the one bot competitions keep teaching: empirical validation beats theory-only patching, but only if the validation environment looks like the live one. For us that meant unseen maps, real opponents, and enough paired games to see through the chaos.

Thanks to the organisers for running the competition and patching it so actively, and to our teammate whose bot became our base.
