# wego

The Wego agent plugin: search and compare flights and hotels, resolve places, and
generate checkout links through the `wego` CLI.

## This repository is generated

Every file here is published from `wego/wego-ai` by the release pipeline, and the
publish runs on each promote of the CLI to its stable ring.

**Do not edit anything in this repository by hand.** An edit here is not a change
to the plugin – it is a change that the next promote overwrites, silently and
without a conflict, because the publish writes the file rather than merging it.

To change the plugin, change its source in `wego/wego-ai` and let a promote carry
it across:

| Published here | Source |
|---|---|
| `plugin.json` | `apps/cli/plugin/plugin.json` |
| `README.md` | `apps/cli/plugin/README.md` |
| `skills/wego/SKILL.md` | `apps/cli/.claude/skills/wego/SKILL.md` |

## Issues and questions

File them upstream, against `wego/wego-ai`. Issues opened here reach the same
people more slowly, and the fix lands upstream regardless.
