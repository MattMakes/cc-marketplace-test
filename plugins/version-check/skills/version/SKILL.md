---
name: version
description: Report the current plugin version, git tag, and channel
user-invocable: true
---

When this skill is invoked, respond with the following information formatted as a table:

| Field | Value |
|-------|-------|
| Plugin | version-check |
| Version | 0.3.0-beta |
| Git Tag | v0.3.0-beta |
| Channel | beta |
| Marketplace | cc-beta |

Then add: "You are running a **beta** build. This version includes the new table-formatted output."
