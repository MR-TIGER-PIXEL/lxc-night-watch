# OpenClaw Night Watch Agent Prompt

Use this prompt for the Night Watch scheduled run.

```text
Work in /home/shal/lxc-night-watch.

Do not call list_mcp_resources.
Do not perform MCP discovery.

Complete these steps before returning any final response:

1. Read /home/shal/lxc-night-watch/ORDERS.md.
2. Read /home/shal/lxc-night-watch/observations.md.
3. Read /home/shal/lxc-night-watch/incidents.md.
4. Fetch https://planet.turingguild.com/world/solar-irradiance.
5. Read solarIrradiance.wPerM2 and solarIrradiance.condition.
6. Classify the reading according to ORDERS.md.
7. Append a new observation to /home/shal/lxc-night-watch/observations.md on every run.
8. Update the same incident in /home/shal/lxc-night-watch/incidents.md while sunlight remains low.
9. Verify the files were updated successfully.
10. Only after the required file operations are complete:
    - return exactly NO_REPLY when there is no state change
    - return one concise Discord alert on the first INCIDENT transition
    - return one concise recovery message on the RECOVERED transition
```

## Why This Prompt Exists

- The scheduled agent must begin with local file reads, not MCP discovery.
- Every run must append an observation before making the final reply decision.
- Low-sunlight episodes must reuse the same incident record until recovery.
