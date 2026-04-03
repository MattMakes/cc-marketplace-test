# cc-marketplace-test

A Claude Code plugin marketplace with release channels (stable/beta) managed via git branches.

## What's in this marketplace

**version-check** — A simple plugin with a single skill (`/version-check:version`) that reports the current plugin version, git tag, and release channel. Useful for verifying which version you're running after switching channels.

## How release channels work

This marketplace uses **git branches** to manage release channels. The same repo, same marketplace name (`cc-marketplace-test`), same plugin name (`version-check`) — the only difference is which branch you point to when adding the marketplace.

| Branch   | Plugin Version | Description                        |
|----------|---------------|------------------------------------|
| `main`   | `0.1.0`       | Original, no channel label         |
| `stable` | `0.1.0`       | Stable release channel             |
| `beta`   | `0.3.0-beta`  | Beta release channel (new features)|

The `@ref` suffix when adding a marketplace tells Claude Code which branch (or tag) to use:

```
/plugin marketplace add MattMakes/cc-marketplace-test@stable   # uses the stable branch
/plugin marketplace add MattMakes/cc-marketplace-test@beta     # uses the beta branch
```

### Key rules

- The **marketplace name must be the same** on all branches. Channel differentiation comes from the `@ref`, not the name.
- The **plugin version in `plugin.json` must differ** between branches. If two branches have the same version, Claude Code treats them as identical and skips updates.
- You can only have **one instance of a marketplace** installed at a time (same name = same marketplace). To switch channels, you must remove and re-add.

---

## Setup guides

### I already have the marketplace installed (from before release channels existed)

#### Switch to stable

Your existing install is from `main`, which has the same version (`0.1.0`) as `stable`. Because the versions match, Claude Code won't detect a change. You need to fully uninstall and reinstall:

```
/plugin                                                        # open plugin manager, uninstall version-check
/plugin marketplace remove cc-marketplace-test
```

Quit and restart Claude Code, then:

```
/plugin marketplace add MattMakes/cc-marketplace-test@stable
/plugin install version-check@cc-marketplace-test
```

Quit and restart Claude Code. Verify with:

```
/version-check:version
```

#### Switch to beta

```
/plugin                                                        # open plugin manager, uninstall version-check
/plugin marketplace remove cc-marketplace-test
```

Quit and restart Claude Code, then:

```
/plugin marketplace add MattMakes/cc-marketplace-test@beta
/plugin install version-check@cc-marketplace-test
```

Quit and restart Claude Code. Verify with:

```
/version-check:version
```

---

### Fresh install (no existing marketplace)

#### Install stable

```
/plugin marketplace add MattMakes/cc-marketplace-test@stable
/plugin install version-check@cc-marketplace-test
```

Restart Claude Code, then verify:

```
/version-check:version
```

#### Install beta

```
/plugin marketplace add MattMakes/cc-marketplace-test@beta
/plugin install version-check@cc-marketplace-test
```

Restart Claude Code, then verify:

```
/version-check:version
```

---

### Switching between channels

Claude Code does not support in-place channel switching. You must fully remove and reinstall.

#### From stable to beta

```
/plugin                                                        # open plugin manager, uninstall version-check
/plugin marketplace remove cc-marketplace-test
```

Quit and restart Claude Code, then:

```
/plugin marketplace add MattMakes/cc-marketplace-test@beta
/plugin install version-check@cc-marketplace-test
```

Quit and restart Claude Code. Verify with:

```
/version-check:version
```

#### From beta to stable

```
/plugin                                                        # open plugin manager, uninstall version-check
/plugin marketplace remove cc-marketplace-test
```

Quit and restart Claude Code, then:

```
/plugin marketplace add MattMakes/cc-marketplace-test@stable
/plugin install version-check@cc-marketplace-test
```

Quit and restart Claude Code. Verify with:

```
/version-check:version
```

---

## Receiving updates within a channel

When a new version is pushed to your channel's branch, update with:

```
/plugin marketplace update cc-marketplace-test
```

If the plugin version in `plugin.json` was bumped on the branch, the update will be detected and applied. If the version wasn't bumped, the update is silently skipped.

---

## Repository structure

```
cc-marketplace-test/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace catalog (name, plugins list)
└── plugins/
    └── version-check/
        ├── .claude-plugin/
        │   └── plugin.json       # Plugin manifest (name, version)
        └── skills/
            └── version/
                └── SKILL.md      # Skill definition
```

### For marketplace maintainers

To release a new beta version:

```bash
git checkout beta
# edit plugin.json — bump version (e.g., 0.4.0-beta)
# edit SKILL.md — update version/tag references
git add -A && git commit -m "Bump beta to 0.4.0-beta"
git tag v0.4.0-beta
git push origin beta --tags
```

To promote beta to stable:

```bash
git checkout stable
# edit plugin.json — set version (e.g., 1.0.0)
# edit SKILL.md — update version/tag/channel references
git add -A && git commit -m "Release 1.0.0 stable"
git tag v1.0.0
git push origin stable --tags
```

Remember: always bump the version in `plugin.json` or the update won't be detected by users.
