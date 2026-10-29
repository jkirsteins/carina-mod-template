# Throne of Carina mod template

Throne of Carina is a turn-based space strategy game that runs in the browser ([play it on itch.io](https://jkir.itch.io/carina)). This repository is the starting point for a mod.

The guide, the reference and a page that checks a mod are on the site: <https://jkirsteins.github.io/carina-mod-template/>

## Use it

Press **Use this template** on this repository, clone your copy and run the tool in it. You need [Node.js](https://nodejs.org/) 22 or newer.

```sh
npx carina-modkit check mods/prism-optics
npx carina-modkit run mods/prism-optics --turns 50
npx carina-modkit pack mods/prism-optics
```

`check` runs the game's own checks, `run` plays computer players under the mod's rules and `pack` writes a `.carinamod` to `dist/`. The site's [Your first mod](https://jkirsteins.github.io/carina-mod-template/guide/first-mod) page makes a new mod step by step.

## What is in this repository

| Path | What |
|---|---|
| `mods/` | One folder per mod. The examples below are working mods to read and copy. |
| `schemas/` | JSON Schemas of the mod files, for your editor. |
| `reference/` | The field and effect reference, also on the [site](https://jkirsteins.github.io/carina-mod-template/reference/). |
| `.vscode/settings.json` | Connects the schemas to the mod files in VS Code. |
| `CONTENT_POLICY.md` | What mods may and may not contain. |
| `LICENSE`, `LICENSE-CONTENT` | MIT for the code and settings, CC-BY-4.0 for the mods' content. |
| `.github/workflows/check.yml` | Checks, test-plays and packs every mod on each push. |

## The examples

| Folder | Id | What it is |
|---|---|---|
| `mods/prism-optics/` | `carina-team.prism-optics` | A tech line, a beam weapon, a building with its own colony picture and a balance patch. |
| `mods/hollow-choir/` | `carina-team.hollow-choir` | A race with all its pictures and music, a signature pick and a pick-exclusive weapon. |
| `mods/deep-field/` | `carina-team.deep-field` | A graphics and sound pack. It changes no rules. |
| `mods/choir-prisms/` | `carina-team.choir-prisms` | A patch-only add-on that needs Prism Optics and Hollow Choir. |
| `mods/balance-tweaks/` | `carina-team.balance-tweaks` | Three rule changes and nothing else. |
| `mods/ship-class-names/` | `carina-team.ship-class-names` | A text-only pack that renames two hull classes. |
| `mods/pulse-lance/` | `carina-team.pulse-lance` | An update that renames its records and carries a save migration. |

## Licences

The code and settings of this repository are MIT ([LICENSE](LICENSE)). The mods' content is CC-BY-4.0 ([LICENSE-CONTENT](LICENSE-CONTENT)) unless a mod's `mod.json` names another licence.
