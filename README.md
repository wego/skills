# Wego skill for AI agents

Live flight and hotel search for AI agents, through the `wego` CLI.

Made by [Wego](https://www.wego.com), the travel search engine and online travel
agency for the Middle East, North Africa and beyond. Docs, quickstart and use cases
live at [docs.wego.com](https://docs.wego.com); the CLI reference is at
[docs.wego.com/cli](https://docs.wego.com/cli) and the HTTP contract at
[docs.wego.com/api](https://docs.wego.com/api).

The skill takes a travel request in plain language and works it against live
inventory. It resolves places, searches and compares flights and hotels, reads
fares and room rates, refines a search without starting a new one, and hands back
a Wego or partner checkout link for whichever option the user picked. Every price
it quotes came from the response that carried it, never from an estimate.

The skill and the CLI divide the work. The skill runs the conversation, ranks the
options, and decides where the user has to confirm. The CLI handles
authorization, credential storage, API access, one JSON envelope, and metering.

## Works with

Any agent that supports [skills](https://agentskills.io/) and a machine or
sandbox to run [our CLI](https://docs.wego.com/cli). Claude Code, Codex, Cursor,
OpenClaw, Hermes and Pi, to name a few.

## Install

The skill drives the `wego` CLI, so both need to be present. Either order works.

Add the skill to your agent from this repository:

```bash
npx --yes skills add https://github.com/wego/skills --skill wego
```

Install the CLI. The skill offers this itself when the command is missing, and
asks for your approval before running it:

```bash
curl -fsSL https://docs.wego.com/cli/install | bash
```

If you already have the CLI, it carries the same skill and can install it for you:

```bash
wego skill install
```

## What the skill covers

- resolving place names into codes, across cities, airports and hotels;
- searching and comparing flights, then reading fares, baggage and refundability
  on the trip a user picks;
- reading what a flight is like to sit through: overnight legs, layover length,
  ground segments sold under a flight number;
- searching hotels, then reading room and rate detail, board basis and
  cancellation terms;
- reading guest reviews by topic and traveller type;
- planning a flight and a hotel together for one trip;
- refining an existing search instead of creating another;
- generating Wego or partner checkout links, and share links for a search that
  do not expire;
- reference lookups that need no search: visa-free destinations for a passport,
  public holidays in a market, published airline timetables, nearby airports;
- sending feedback about the tool or the results to the Wego team.

## Try one search

An agent using the skill collects the missing inputs and drives the CLI itself.
The equivalent direct command, Dubai to London for one adult on 25 December 2026:

```bash
wego flights search DXB LON 2026-12-25 --adults 1 --currency AED --site AE
```

```json
{
  "searchId": "b88a5ef1970abcd0msr",
  "currencyCode": "AED",
  "metadata": { "totalCandidates": 748, "hasMore": true, "snapshotFareCount": 4270 },
  "results": [
    {
      "tripId": "b88a5ef1970abcd0msr:RJ613~25~0645~0920-RJ111~25~1155~1420",
      "price": { "total": 769, "currency": "AED", "websiteCount": 19, "hasWegoFare": true },
      "legs": [ { "from": "DXB", "to": "LHR", "departsAt": "2026-12-25T06:45:00.000+04:00",
                  "arrivesAt": "2026-12-25T14:20:00.000Z", "durationMinutes": 695, "stops": 1,
                  "via": [ "AMM" ], "airlines": [ { "code": "RJ", "name": "Royal Jordanian" } ] } ],
      "badges": [ "best_value" ]
    }
  ]
}
```

The response above is trimmed to one of the ten cards on the first page, with the
fields the skill reads; nothing was added.

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

## License

Copyright 2026 Wego.

Licensed under the [Apache License, Version 2.0](LICENSE).

## Telemetry

The published CLI sends one usage event per command to Wego, and it is on by
default. Each event carries the command and subcommand, flag names, a few enum
values, the exit code, duration, CLI version, OS and architecture, a random
per-machine device id (a fixed `ephemeral` marker when the machine cannot store
one), and a session id. When you are logged in the event is keyed on your Wego
account id; logged out it is anonymous. It never sends your search text, dates or
messages. A build run from source, or against a non-production backend, sends
nothing.

To opt out:

```bash
wego telemetry disable
```

or set `WEGO_CLI_TELEMETRY=off` for one run. `wego telemetry status` shows the
current setting, and `WEGO_CLI_TELEMETRY=log` prints the event instead of sending
it, if you want to see exactly what leaves your machine.

Separately from telemetry, every API request carries a session id header so the
API can group the commands of one task into a funnel. The opt-out above does not
remove it; `WEGO_CLI_NO_SESSION=1` does.
