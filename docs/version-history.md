# Version history

Every upload from v124 (23 Oct) to v170 (31 Oct), named by the version number the competition site showed. Times are UTC. "Local" numbers are our own paired reads; "scrims" are games against real opponents on the competition site.

| Version | Uploaded | What changed | Outcome |
|---|---|---|---|
| v124 | 23 Oct 19:40 | v123 + the team search may not override a fight call when outnumbered | +103.8 locally vs v123; kept |
| v125 | 23 Oct | v124 + Vesper buys Treasure as her second emblem | Kept |
| v126 | 23 Oct 21:56 | The same zip as v125, uploaded twice by accident | Duplicate |
| v127 | 24 Oct 00:16 | v125 + Heart of Ruin buys Jungle-Focus as her second emblem | Kept |
| v128 | 24 Oct 11:36 | No note recorded | |
| v129 | 24 Oct 14:34 | Exit padding, wards and ward aim, Prism focus, plan side, altar ring and claim drop | |
| v130 | 24 Oct 15:19 | Idle and tile-hold rules | |
| v131 | 24 Oct 15:55 | `entry_hp 1.5` (camp entry health threshold) | Later found to keep us off contested boss camps |
| v132 | 24 Oct 16:10 | Upload attempt that returned an invalid response | Void |
| v133 | 24 Oct 16:11 | Enemy separation weight, a bank fix, Prism hunt | |
| v134 | 24 Oct 16:20 | Shield rule, Heart of Ruin thresholds, cast-with-mate, hop tuning | |
| v135 | 24 Oct 16:35 | Heart of Ruin threshold retune | |
| v136 | 24 Oct 17:48 | Carry cap and carry order (Vesper > Heart of Ruin > Vendetta), camp focus with an 80k fuel guard | |
| v137 | 24 Oct 18:51 | A focus-target rule (`foc_most`) | Comparison base for v140 to v142 |
| v138 | 24 Oct 19:21 | Bag ambush: two bodies ambush an enemy carrying 250+ gems | 30 overruns on stress maps |
| v139 | 24 Oct 20:14 | v137 + bag ambush behind an 80k fuel guard | Overruns cut to 16 |
| v140 | 24 Oct 20:33 | Dash while asleep, gem switch, max wait, boss return fix | Worse than v137 on top-4 scrims (-51) |
| v141 | 24 Oct 20:39 | Calm fix, danger weight, boss health, monolith items | Worse than v137 on top-4 scrims (-98) |
| v142 | 24 Oct 21:06 | Ward cadence and aim | +61 ± 23 locally vs v137, but -64 on top-4 scrims |
| v143 | 24 Oct 21:38 | Re-upload of v139 | Back to the last build that held up |
| v144 | 24 Oct 22:11 | v142 + an island-wait fix (v142 and v143 read level on scrims, -5 ± 62) | Sprint 2 final |
| v145 | 25 Oct 22:01 | v144 + stun walk | |
| v146 | 25 Oct 23:31 | Patch-proof item code, pick-starve and pick-pre guards | |
| v147 | 26 Oct 03:53 | First module-bot build, on the new SDK 4.0.0 (engine 2.0.0) | |
| v148 | 26 Oct 06:19 | Init fuel fixes, same strength | Removed three map-specific overruns |
| v149 | 26 Oct 08:16 | Correctness wave + runtime fuel budgets | Kept |
| v150 | 26 Oct 14:54 | Large correctness and value bundle | **4-21 on the ladder; rolled back** |
| v151 | 26 Oct 16:05 | Rollback: the exact bytes of v149 | Base for the ladder A/B tests |
| v152 | 26 Oct 16:49 | Ladder A/B #1: v150 without its three riskiest switches | Not shown better than v151 |
| v153 | 26 Oct 18:36 | Ladder A/B #2: train head with 35 switches on | Not shown better than v151 |
| v154 | 26 Oct 22:11 | Ladder A/B #3 | Pulled (a buggy train head); v151 pinned again |
| v155 | 27 Oct 01:02 | Fixes release; A/B #4 vs v151 | |
| v156 | 27 Oct 04:41 | Release check passed vs v155 | Kept |
| v157 | 27 Oct 10:22 | Release check passed vs v156 | Kept |
| v158 | 27 Oct 15:05 | Fixes-only release: deaths -0.71 ± 0.49, margin +67 ± 36, 0 faults | Kept |
| v159 | 27 Oct 16:17 | Experiment: context pull | Ranked A/B vs v158 |
| v160 | 27 Oct 21:41 | Bundle 2: capture mix, lock memory, tail reserve and more | Ranked A/B vs v159 |
| v161 | 27 Oct 22:57 | v160 + ambush freshness, deny walk, an edge-oscillation fix | **Became the reference base** |
| v162 | 28 Oct 06:37 | Unified value-model build | |
| v163 | 28 Oct 15:18 | The old decision line inside the unified code | Kept |
| v164 | 28 Oct 16:37 | Mistaken rollback to v160 by an audit agent during a dry run | Undone within five minutes |
| v165 | 28 Oct 23:42 | Teamwork bundle: team word, team mode, muster and contest, team join, locks, shared sight | Used to tune the new line on the ladder |
| v166 | 29 Oct 08:54 | Bundle 1d: the v165 profile plus a monolith rule, without team join | Release check passed |
| v167 | 29 Oct 18:09 | Unified line build | |
| v168 | 29 Oct 21:05 | Unified line build | |
| v169 | 30 Oct 00:43 | Unified line build | |
| v161 | 30 Oct 12:51 | v161 re-activated as the live base | Every later build had to beat it on held-out maps |
| **v170** | **31 Oct 22:43** | **v161 backbone + camp scoring retune (`combo100r28`) + `fight_d 4`** | **+0.104 ± 0.031 on 700 held-out pairs vs v161; final submission** |
