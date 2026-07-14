# OpenClaw Night Watch

OpenClaw Night Watch is a docs-only runbook for a repeating solar inspection loop.

## Agent Prompt

Use [night-watch-agent-prompt.md](/Users/amychang/Desktop/labs/lxc-night-watch/night-watch-agent-prompt.md) as the canonical scheduled-agent prompt. It explicitly forbids MCP discovery and requires the run to read `ORDERS.md`, `observations.md`, and `incidents.md`, fetch the solar endpoint, append a new observation, update the current incident, verify the file updates, and only then decide whether to return `NO_REPLY`, an incident alert, or a recovery message.

## Loop

### Wake

Start an inspection cycle.

### Inspect

Query `https://planet.turingguild.com/world/solar-irradiance` and read:

- `solarIrradiance.wPerM2`
- `solarIrradiance.condition`

### Decide

Classify the reading with these rules:

- `NORMAL` when `wPerM2` is 450 or greater and there is no open incident
- `INCIDENT` when `wPerM2` is below 450
- `RECOVERED` when `wPerM2` returns to 450 or greater after an incident

Compare the new classification with the most recent observation to determine whether the state changed.

### Record

Append one entry to [observations.md](observations.md) every run. Each entry includes:

- timestamp
- `wPerM2`
- condition
- classification
- short note

Maintain [incidents.md](incidents.md) as one incident per low-sunlight episode:

- create the incident on the first drop below 450
- keep updating that same incident while sunlight remains low
- do not create duplicates during the same low-sunlight stretch
- add the recovery time and mark it recovered when sunlight returns to 450 or greater

### Report

Use the final-response contract:

- return exactly `NO_REPLY` for `NORMAL` with no state change
- return one concise Discord alert on the first `INCIDENT` transition
- return exactly `NO_REPLY` for a continued `INCIDENT` with no state change
- return one concise recovery message on the `RECOVERED` transition
- return exactly `NO_REPLY` for continued normal operation after recovery

## Files

- [ORDERS.md](ORDERS.md): inspection instructions and response rules
- [observations.md](observations.md): append-only observation log
- [incidents.md](incidents.md): one incident per low-sunlight episode
