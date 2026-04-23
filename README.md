# WobMors

A 2000s-style mafia browser RPG, inspired by **Mob Wars** (David Maestri, 2008), **Mafia Wars** (Zynga), and **Mob Wars: La Cosa Nostra** (Kano). Server-rendered, grindy, ad-free, and offline-friendly — a working Phase 1 of that feature surface you can run locally in one command.

Not affiliated with Kano or any prior *Mob Wars* / *Mafia Wars* title. This is an independent homage.

## Run it

```bash
npm install
npm start
# → WobMors running at http://localhost:3456
```

Override the port:

```powershell
$env:PORT = "4000"; npm start    # PowerShell
```
```bash
PORT=4000 npm start               # bash
```

Then open [http://localhost:3456](http://localhost:3456), register → name your gangster → you're in.

## What's in (Phase 1)

- **5-stat character model** — Health, Energy, Stamina, Attack, Defense with the asymmetric skill-point economy from LCN (Stamina costs 2 SP per point; Max Health gives +10 per SP).
- **Regen-over-time vitals** — Energy 1/180s, Stamina 1/300s, Health 1/90s, computed on read, accurate across offline periods.
- **Jobs** — 15 jobs across 5 tiers, gated by level, consume energy, pay cash + XP, with mastery counters and random loot drops.
- **Inventory + loadouts** — weapons/armor/vehicles with separate attack- and defense-optimised best-loadout selection.
- **PvP fights** — `(stat + item totals) × random[0.85, 1.15]` attacker vs defender; cash stolen on win; both sides take HP damage.
- **Hitlist** — place a bounty on any player; hunters cost 2 stamina per hit, get a 15% attack bonus.
- **Properties / City** — 7 property types, linear income scaling by level, 24h uncollected cap, compute-on-read income (never ticks).
- **Favor Points** — earned on level up, spend for full vital refills.
- **World chat**.
- **NPC seed** — 60 mobsters with gear, so you can fight from minute one.

## Roadmap

### Phase 2 — social + deep systems
- Friend/mob member recruitment with equip-three-items rule
- Syndicates (guilds) + shared chat + syndicate quests
- Crafting / workshop (recipes, components, reverse-craft, hidden blueprints)
- Multi-city with job mastery → permanent passives (property discount, income boost)
- Ambush, punches, Familia

### Phase 3 — events + endgame
- **Syndicate Wars** (bi-weekly, divisions, top-15 member stats, 5-min respawn, decaying kill bonus)
- **Battle Arena** (level 250+, 24h brawl→sudden-death)
- **Raid Bosses** (ranks 0→25+, 2000-action threshold, superior drops, world bosses)
- Achievements, global + social leaderboards, seasonal events

## Architecture

```
server.js      Express routes + session auth + flash messages
db.js          SQLite schema (users, characters, inventory, job_mastery,
               properties, hitlist, fight_log, chat_messages)
data.js        Static game catalog: jobs, items, properties, NPC names,
               regen rates, skill-cost config, XP curve
game.js        Core logic: regen, level-up, jobs, fights, hitlist,
               properties, favor refills, NPC seeding
views/         EJS templates — one per page + HUD/nav partials
public/        CSS (dark gold/red mafia aesthetic)
```

### Design decisions worth knowing

- **Vitals are compute-on-read**, stored as `(value, updated_at)` — no cron ticks, no drift on server restart, handles offline regen correctly.
- **Fight resolution is server-authoritative** — client never sees the RNG seed.
- **Item auto-loadout** — each fight picks the best `1 + mob_size` items per slot; offense ranks by `atk*2 + def`, defense by `def*2 + atk`.
- **Property collection is also compute-on-read**, with a capped uncollected bucket — no background jobs per property.
- **SQLite + `better-sqlite3`** — single-file DB, synchronous API, zero-config for a game of this scale.

## Stack

- **Node.js 22+**
- **Express** — routing + sessions
- **better-sqlite3** — DB
- **EJS** — server-rendered templates (no SPA, matches the 2000s aesthetic)
- **bcryptjs** — password hashing

## Contributing

Issues + PRs welcome. The game-data catalog in `data.js` is the easiest place to start — new jobs, items, and properties are pure data additions.

## License

MIT — see [LICENSE](LICENSE).
