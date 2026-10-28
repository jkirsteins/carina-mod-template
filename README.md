# Throne of Carina mod template

Start your own mod for Throne of Carina here: fork or copy this repository, follow "Your first mod" below, and
you have a working mod in a few minutes.

## What a mod is

A mod is a folder (or a `.carinamod` file, which is that folder zipped) holding **data only**: JSON records and
rule changes, text, pictures and sound. No code runs from a mod. The game checks every mod when you import it and
tells you exactly what is wrong and where.

What mods can change today:

- **Balance**: any number the rules use, from weapon damage and building costs to research costs, tax rates and
  combat rules.
- **Technology**: new research fields spliced into the existing research areas, new applications, and what they
  unlock.
- **Ships**: new beam weapons, missiles, fighters, armour, shields, computers, drives and specials; hull stats.
- **Races**: new races, race picks and governments, and changes to the stock ones.
- **Buildings**: new buildings made of the game's economy, research and defence effects.

What needs a newer game: new kinds of behaviour (new effect kinds, new special weapon kinds, new hull classes,
new random events), scenarios and campaigns, and interface changes.

In multiplayer every player needs the same rule mods; the game checks this by hash, not by name. A player who
joins a game with mods they lack, or have in another version, sees which ones differ; the game never sends mods
between players. Every player holds the full rules of the game, so a mod cannot keep a rule secret. Art, sound and
text may differ between players.

## What is in this repository

| Path | What |
|---|---|
| `mods/` | One folder per mod. The four examples below are working mods to read and copy. |
| `schemas/` | JSON Schemas of the mod files, for your editor. |
| `reference/` | The field and effect reference: every field, its limits, and how the computer players use it. |
| `.vscode/settings.json` | Connects the schemas to the mod files in VS Code: completion and checks as you type. |
| `CONTENT_POLICY.md` | What mods may and may not contain. |
| `LICENSE`, `LICENSE-CONTENT` | MIT for the code and settings, CC-BY-4.0 for the mods' content. |
| `.github/workflows/check.yml` | Checks, test-plays and packs every mod on each push. |

## The examples

The four folders under `mods/` are the example mods. In the game they are in the mod catalog: open MODS, then
BROWSE, and install them like any mod (Choir Prisms needs Prism Optics and Hollow Choir installed too). They are
small on purpose, and each one shows one part of modding.

| Folder | Id | What it shows |
|---|---|---|
| `mods/prism-optics/` | `carina-team.prism-optics` | A tech line spliced into the research tree, a beam weapon the computer players design ships with, a building they build (with its `autobuild` formula and its own colony picture), a balance patch with `expect`, and a discovery known only at the Advanced start (a second patch row bars it from the Post-Warp start). |
| `mods/hollow-choir/` | `carina-team.hollow-choir` | A race with every picture and its theme music made for it (no art base), a signature pick called Resonance, and a weapon only empires that take that pick can research. |
| `mods/deep-field/` | `carina-team.deep-field` | A graphics and sound pack: new title and galaxy backdrops and music. It changes no rules, so it is switched on in the library, per player. |
| `mods/choir-prisms/` | `carina-team.choir-prisms` | A compatibility add-on: patches only, needs Prism Optics and Hollow Choir, and gives Resonance empires Refractive Optics at the Average start. |

Two limits worth knowing, both of the game and not of the examples: a building a mod adds is not rebuilt in one
step by the quick-rebuild of Hard and Impossible computer players (that table is keyed by the game's own building
numbers, a later version will read it from data), and a weapon's cost is one value for every empire (a cost that
depends on the empire needs a new kind of effect).

Check them the same way as your own: `npx carina-modkit check mods/choir-prisms` finds the two mods it needs next
to it, and `npx carina-modkit run mods/choir-prisms --turns 50` plays them together.

## Your first mod

You need [Node.js](https://nodejs.org/) 22 or newer. An editor that reads JSON Schemas (VS Code, for example)
helps. Every command runs in this folder. The commands below are written `npx carina-modkit <command>`: the game's
mod tool, which `npx` fetches from npm the first time (add `--yes` to skip its question, as the CI workflow does).

Create the mod. The id is yours forever: `yourname.slug`, lowercase letters, digits and hyphens.

<!-- walkthrough: run -->
```sh
npx carina-modkit init mods/my-first-mod --id yourname.my-first-mod --title "My first mod" --author "Your name"
```

This writes `mods/my-first-mod/mod.json` and an empty `patches/balance.json`, and adds the new folder to the VS
Code settings.

Add a weapon. Records you add go in `records/<catalog>/`; their keys start with your id, `-` read as `_`:

<!-- walkthrough: file mods/my-first-mod/records/weapons/beams.json -->
```json
{
  "$schema": "carina-records/1",
  "catalog": "weapons",
  "records": [
    {
      "key": "yourname.my_first_mod.arc_lance",
      "application": "laser_cannon",
      "class": "beam",
      "ammo": null,
      "space": 12,
      "cost": 9,
      "damage": [4, 10],
      "strategicDamage": [4, 10],
      "mods": ["hv", "pd", "ap", "af"],
      "inherentBits": 0,
      "monster": false,
      "nameKey": "wpn.yourname.my_first_mod.arc_lance.name"
    }
  ]
}
```

`application` names the research result that unlocks it (here the same one as the laser cannon); references to
other records are always keys, never numbers. Give it a name in `i18n/en/`, with the plural and the short note
the ship design screens show (about 35 characters fit; the same key with `.plural` and `.note` in place of `.name`):

<!-- walkthrough: file mods/my-first-mod/i18n/en/text.json -->
```json
{
  "wpn.yourname.my_first_mod.arc_lance.name": "Arc Lance",
  "wpn.yourname.my_first_mod.arc_lance.plural": "Arc Lances",
  "wpn.yourname.my_first_mod.arc_lance.note": "A crackling short-range beam."
}
```

Change a rule with a patch row: longer battles.

<!-- walkthrough: file mods/my-first-mod/patches/balance.json -->
```json
{
  "$schema": "carina-patch/1",
  "patches": [
    {
      "op": "set",
      "catalog": "combat-rules",
      "key": "standard",
      "path": "roundLimit",
      "value": 60,
      "expect": { "roundLimit": 50 }
    }
  ]
}
```

Check it, pack it, and let the computer players try it:

<!-- walkthrough: run -->
```sh
npx carina-modkit check mods/my-first-mod
npx carina-modkit pack mods/my-first-mod
npx carina-modkit run mods/my-first-mod --turns 50
```

`check` prints what the game would say on import. `pack` writes `dist/yourname.my-first-mod-0.1.0.carinamod` and
prints its hashes. `run` plays 50 turns with computer players under your rules and reports whether they used what
you added.

## Folder layout and manifest

```
mod.json                          the manifest
records/<catalog>/<file>.json     records you add
patches/<file>.json               changes to records (yours and the game's)
i18n/<locale>/<file>.json         text, keyed; English (en) is required for every key your records name
assets/                           pictures and sound, listed in assets/roles.json (see "Pictures and sound")
preview.png                       320x240 picture for the mod list
```

`mod.json` fields:

| Field | Required | What |
|---|---|---|
| `$schema` | yes | `"carina-mod/1"` |
| `id` | yes | `yourname.slug`; never change it after release |
| `version` | yes | `1.2.3`; raise it with every release |
| `format` | yes | `1` |
| `rulesVersion` | yes | starts at 1; raise it when old saves need a migration |
| `phase` | yes | `content` (adds things), `patch` (changes things), `final` (last word) |
| `title`, `summary` | title | up to 40 and 200 characters, Latin-1 |
| `authors` | yes | `[{ "name": "...", "url": "https://..." }]` |
| `license` | yes | an SPDX id, for example `CC-BY-4.0` |
| `sources` | no | outside material: `{ "files", "author", "license", "url" }` |
| `aiUse` | no | what you made with generative AI: `graphics`, `sound`, `text` |
| `dependencies`, `loadAfter` | no | other mods yours needs, conflicts with, or loads after |
| `allowFeature`, `allowEmbed`, `basedOn`, `game` | no | see [reference/limits.md](reference/limits.md) and the schema |

## Pictures and sound

`assets/roles.json` (`"$schema": "carina-roles/1"`) connects your files to the places the game draws and plays
them. Each row of `roles` is `{ "role": "<role id>", "file": "assets/<path>" }`:

- A **role** is a place in the game: a title backdrop, a galaxy star field, a race portrait, a sound effect, a
  music cue. [reference/roles.md](reference/roles.md) lists every role with its kind, exact size and frame grammar; a file
  of the wrong size or kind is refused when you import it, with the role named.
- **Pictures** are PNG files. **Sound** is Ogg (Opus or Vorbis) with an `.m4a` twin for browsers that do not play
  Ogg: the twin has the same role with `.m4a` added. Music is at most 300 seconds, an effect at most 10.
- A **race** you add either names an art base, `{ "race": "<your race key>", "artBase": "<stock race key>" }`
  in `races`, and every picture it does not bring is the base race's, or names none and brings the whole list
  itself: portraits, ambassador, emblems, council medallions, ships, colonist figures and theme music.
  `mods/hollow-choir/` brings all of it; the reference lists the roles a race without an art base must supply.
- `artFrom` draws a new building like a base one: `{ "catalog": "buildings", "key": "<your building>", "from":
  "<base building key>" }`.
- **Building pictures.** A building you add is drawn on the colony lot from its own atlas, the role
  `colony.lot.<building key with each _ written as ->`: 36 sprites, one per cell of the 6 x 6 lot, named
  `r<0-5>c<0-5>_x<X>_y<Y>` in the `frames` list of the role row. `mods/prism-optics/` is the worked example, and
  [reference/roles.md](reference/roles.md#building-lot-art) gives the grammar and the screen box of every cell. `artFrom`
  (above) is the shortcut when a base building's picture will do; a building has one or the other.
- A package that has only pictures, text or sound (like `mods/deep-field/`) is a **cosmetic pack**: it changes
  nothing about the rules, so it needs no agreement between players and is switched on in the library.

## Patch operations

Rows address a catalog, a record `key` and a `path` of property names. `set` replaces a value; `append`,
`insertAfter` and `removeValue` change lists; `remove` deletes a record; `splice` links a new research field into
its area. `expect` makes a row fail loudly when a game update changed the value you build on; `optional: true`
skips a row whose target is gone. Details: [reference/patch-ops.md](reference/patch-ops.md).

Two rows from the examples. Prism Optics makes one of its discoveries known from the start at the Advanced start only
(the Post-Warp start knows every application that costs 250 or less, so Prism Optics also adds the discovery's
applications to the Post-Warp exclusion list; Pre-Warp and Average empires research it). Starting knowledge is a list per start level; a path may name a level by its key, so this row
adds one entry to the Advanced list and touches no other level (`field` makes the field researched with all its
applications; `app` would make a single application known; either takes a `when` condition):

```json
{
  "op": "append",
  "catalog": "start-state",
  "key": "classic",
  "path": "levels.advanced.alsoKnown",
  "value": { "field": "carina_team.prism_optics.refractive_optics" }
}
```

Hollow Choir keeps one weapon for itself. A removal row takes applications away from an empire at the start; a
row with `unless` takes them away from every empire except those that have one of the listed picks (or play one
of the listed races):

```json
{
  "op": "append",
  "catalog": "start-state",
  "key": "classic",
  "path": "tree.removals",
  "value": {
    "unless": { "picks": ["carina_team.hollow_choir.resonance"] },
    "apps": ["carina_team.hollow_choir.resonance_lance"]
  }
}
```

The row is bound to the **pick**, so a custom race that takes Resonance gets the weapon too; naming the race
(`"races": [...]`) instead binds it to that race alone. The weapon cannot reach any other empire by any other
route either (research, trade, theft, capture): the game checks the same rule everywhere an empire can gain
technology.

## Dependencies and compatibility patches

A mod can name other mods it needs in `dependencies`: `{ "id": "carina-team.prism-optics", "range": "^1.0.0",
"kind": "required" }`. The game then switches the needed mods on with yours, and refuses to start without them.
`loadAfter` only orders two mods that are both on; `conflicts` names mods yours cannot share a game with.

The **phase** orders the load: the game's own rules first, then `content` mods (they add things), then `patch`
mods (they change things, including things other mods added), then `final`. Mods of one phase load in
dependency order, then by id. A mod in the `patch` phase therefore always sees every record of every `content`
mod, which is what a compatibility add-on needs.

`mods/choir-prisms/` is the example: no records, no pictures, no text, only patch rows. It requires Prism Optics
and Hollow Choir, loads after both, and gives empires that have the Resonance pick Refractive Optics at the
Average start (Prism Optics gives it to everyone at Advanced and keeps it out of Post-Warp, so the example has no
Post-Warp row):

```json
{
  "op": "append",
  "catalog": "start-state",
  "key": "classic",
  "path": "levels.average.alsoKnown",
  "value": {
    "field": "carina_team.prism_optics.refractive_optics",
    "when": { "picks": ["carina_team.hollow_choir.resonance"] }
  }
}
```

The order a game with all four composes in is `carina.base`, `carina.profile.standard`, `carina-team.hollow-choir`,
`carina-team.prism-optics` (unrelated, so by id), `carina-team.choir-prisms`; the Rules picker shows it. `check`,
`run` and `pack` look for the mods a folder requires in the other folders of `mods/`, so you do not list them with
`--with`.

## The check, pack and run loop

1. Edit files; your editor flags mistakes as you type.
2. `npx carina-modkit check mods/<mod>`: the game's own checks. Every issue names the file, the JSON path, a code
   and what was expected.
3. `npx carina-modkit run mods/<mod> --turns 50`: computer players play under your rules.
4. `npx carina-modkit pack mods/<mod>`: the `.carinamod` to share. The same folder packs to the same file on the same Node.js version (different Node versions compress the bytes differently); the `packageHash` printed by `check` is the mod's identity and does not depend on it.

## Importing into the game and testing

A packed mod: open MODS on the game's main menu (the mod library) and press IMPORT, or drop the `.carinamod` on
the screen in a desktop browser. The library shows what the mod adds and changes and any issues, the same list
`check` prints. A rules mod is then chosen per game: in New Game press RULES, switch your mod on and ACCEPT. The
picker shows the order the mods compose in and lets you pick a winner when two mods change the same value. A
cosmetic pack (text, art or sound only) is switched on or off in the library.

While you work on the mod, use dev mode: MODS, then DEV MODE (or open the game with `?moddev` at the end of its
address). Press FOLDER, pick `mods/<mod>`, and the game reads it, compiles it and lists any issues. In Chrome or
Edge the game reads the folder again within a second of every save; in browsers without folder access (Firefox,
Safari) press FOLDER again after editing. RESTART starts your last new game again (same setup and seed) under
your rules, or opens New Game if you have not started one; LOAD loads a game you saved under the mod as it is now
(after a rules change such a save no longer matches: RESTART); COPY copies the issues as text; POLICY shows the
content policy.

## Getting listed in the catalog

The game's mod catalog is a curated list. To get listed, open a pull request or a submission issue in the
[catalog repository](https://github.com/jkirsteins/carina-mods) (its SUBMITTING.md says how) with your `.carinamod` and its sha256 from `pack`. If your mod lives elsewhere, for example on
itch.io, the catalog can list it as an external page: players see it in the game, open your page, download the
file and import it, and the game shows whether the file matches the listed sha256. Every listed mod follows
[CONTENT_POLICY.md](CONTENT_POLICY.md).

## Reference

- [reference/README.md](reference/README.md): what each catalog holds, with links to every catalog page
- [reference/patch-ops.md](reference/patch-ops.md), [reference/effects.md](reference/effects.md),
  [reference/rules.md](reference/rules.md), [reference/limits.md](reference/limits.md)
- `schemas/`: the JSON Schemas (`npx carina-modkit vscode` rewrites them and the editor wiring for every mod
  folder here)

## Licences

The code and settings of this repository are MIT ([LICENSE](LICENSE)); the mods' content is CC-BY-4.0
([LICENSE-CONTENT](LICENSE-CONTENT)) unless a mod's `mod.json` names another licence.
