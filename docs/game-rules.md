# The game, as our bot saw it

A summary of the Cambridge Battlecode 2026 Autumn rules that mattered for the bot, on the final season engine (2.0.0, SDK `cambc` 4.0.0). Numbers come from the engine's own settings dump and from our probe games, not only from the published rules. Where the two disagreed, the measurement won.

## Win condition

- The first team to **1,000 tributed gems** wins at once.
- Otherwise the game ends at **round 2,000**. The tiebreak order is tributed gems, then lifetime gold earned, then champion kills.

## Draft

- Each team picks **3, 4 or 5 champions**; the map sets the number.
- In each draft round team A picks, then team B picks blind, and both picks are revealed together. A team cannot pick a champion twice, but both teams may pick the same one.
- **12 picks** are available: Ryth, Vesper Vale, Vendetta, Cassi & Issac (one pick, two bodies, one life), Stellar, Prism, Marcella, Willow, Ren, Varg, Tavi and Heart of Ruin.

## Turns

- Per turn a champion gets one walk, one basic attack and one successful spell cast (two spell slots, each with an 8-turn cooldown), plus a ward, shop, consumable and channel action.
- Melee champions may walk and attack in either order. Ranged champions walk *or* attack, unless an attack reset gives the attack back.
- Bodies alternate team by team (A0 B0 A1 B1 ...). Team A starts odd rounds and team B even rounds. Within a team, the order is priority, then draft order.

## Camps, capture and tribute

| Monster | Health | Gold | Gems | Respawn |
|---|---:|---:|---:|---:|
| Wolf | 1,350 | 40 | 4 | 40 |
| Elk | 2,400 | 90 | 15 | 60 |
| Gem cluster | 120 | 0 | 7 | 50 |
| Gold cluster | 120 | 20 | 0 | 50 |
| Golem | 5,000 | 160 | 36 | 80 |
| Monolith | 20,000 | 300 | 200 | 150 |

- To capture a camp, kill every member, then hold one of its tiles uncontested. An enemy on any of the camp's tiles resets the capture.
- Gems are carried in a bag and **tributed at an altar**. A tribute takes `max(1, ceil(gems / 10))` turns and the whole bag goes in. Damage does not break it, but a stun, death or a lock does (the bag is kept).

## Shop and items

- Champions buy on their spawn area or while dead.
- A **shard** costs 30 gold and gives one growth unit of a stat. Five shards of one family and stat combine into an **emblem** (150 gold) with a passive.
- Patch 2.0.0 brought the item list to **84 items**: 14 families x 3 stats x (shard, emblem).

## Compute and memory

| Budget | Fuel (WASM instructions) |
|---|---:|
| Normal turn | 100,000 |
| A champion's first turn | 200,000 |
| First draft turn (engine 2.0.0) | 10,000,000 |

- Nothing carries over between turns. **An overrun suspends the bot mid-code**: it resumes there next turn and the late actions land then.
- Every champion runs its **own bot instance**; globals persist within that instance.
- The team shares **8 bytes** of memory for the whole game, plus **1,024 bytes** of draft memory that is written during the draft and read-only afterwards.
- Small costs add up: a refused move or attack costs about 3,800 instructions (a C++ exception), and one `printf` line up to about 4,700.

## Engine and maps

- The engine is **deterministic**: the same bots on the same map always play the same game.
- The organisers patched the game every few days. Over the season we moved from engine 0.0.x to 1.0.2, 1.0.3 and 2.0.0.
- The ladder pool had 40 maps by late October. Sprint, qualifier and finals maps are **unseen and hand-made**, from 30x30 to 100x100, so fuel had to hold on the largest maps.
