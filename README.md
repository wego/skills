# wego

Live flight and hotel search for AI agents, through the `wego` CLI.

The skill takes a travel request in plain language and works it against live
inventory. It resolves places, searches and compares flights and hotels, reads
fares and room rates, refines a search without starting a new one, and hands back
a Wego or partner checkout link for whichever option the user picked. Every price
it quotes came from the response that carried it, never from an estimate.

The skill and the CLI divide the work. The skill runs the conversation, ranks the
options, and decides where the user has to confirm. The CLI handles
authorization, credential storage, API access, one JSON envelope, and metering.

## Install

The skill drives the `wego` CLI, so both need to be present. Either order works.

Add the skill to your agent from this repository:

```bash
npx --yes skills add https://github.com/wego/skills --skill wego
```

Install the CLI. The skill offers this itself when the command is missing, and
asks for your approval before running it:

```bash
curl -fsSL https://api.wego.com/install | bash
```

If you already have the CLI, it carries the same skill and can install it for you:

```bash
wego skill install
```

## What the skill covers

- resolving place names into codes, across cities, airports and hotels;
- searching and comparing flights, then reading fares, baggage and refundability
  on the trip a user picks;
- searching hotels, then reading room and rate detail, board basis and
  cancellation terms;
- planning a flight and a hotel together for one trip;
- refining an existing search instead of creating another;
- generating Wego or partner checkout links;
- reference lookups that need no search: visa-free destinations for a passport,
  public holidays in a market, published airline timetables, nearby airports.

## Try one search

An agent using the skill collects the missing inputs and drives the CLI itself.
The equivalent direct command is:

```bash
wego flights search SIN KUL 2026-10-20 --adults 1 --currency SGD --site SG
```

Every subcommand prints one JSON envelope on stdout, and status or recovery
guidance on stderr. Agents branch on the process exit code rather than on stderr
wording, and carry opaque identifiers such as `searchId`, `tripId` and `rateId`
forward exactly as returned.

## Safety boundaries

- **Nothing is ever booked.** The skill searches, compares, and generates
  checkout links. No booking, payment, cancellation or change happens through it,
  and it never reports one that did.
- The skill asks the user before it installs or updates the CLI, and before it
  writes pricing preferences to their machine.
- Login completes in the user's own browser. The skill never asks for an access
  token, and never reads or prints the credentials file.
- Every price comes from the response that carried it, and each priced read names
  its currency and which setting chose that currency.
- Creating a search is metered, because the API is a research preview. The skill
  works inside that budget and tells the user when it reaches a limit, including
  a limit it waited out.

## This repository is generated

Every file here is published from `wego/wego-ai` by the release pipeline, and the
publish runs on each promote of the CLI to its stable ring.

**Do not edit anything in this repository by hand.** An edit here is not a change
to the plugin. It is a change that the next promote overwrites, silently and
without a conflict, because the publish writes the file rather than merging it.

To change the plugin, change its source in `wego/wego-ai` and let a promote carry
it across:

| Published here | Source |
|---|---|
| `plugin.json` | `apps/cli/plugin/plugin.json` |
| `README.md` | `apps/cli/plugin/README.md` |
| `LICENSE` | `apps/cli/plugin/LICENSE` |
| `skills/wego/SKILL.md` | `apps/cli/.claude/skills/wego/SKILL.md` |

## Issues and questions

File them upstream, against `wego/wego-ai`. Issues opened here reach the same
people more slowly, and the fix lands upstream regardless.

## License

Copyright 2026 Wego.

Licensed under the [Apache License, Version 2.0](LICENSE).
