# AGENTS.md

## Repository Overview

This repository is a **Packwiz modpack** source tree for **Rigo Craft**.

Current target from `pack.toml`:
- Minecraft: `26.2`
- Loader: `Quilt 0.30.1-beta.2`
- Pack version: `26.2-11`

The repo is intentionally minimal and stores only pack metadata and tracked content files.

## Directory Layout

- `pack.toml` - pack identity, target game/loader versions, and index hash.
- `index.toml` - authoritative file index and hashes used by Packwiz.
- `mods/` - one `*.pw.toml` per mod (source URL, hash, update metadata, side).
- `shaderpacks/` - shaderpack zip files tracked by index.
- `packwiz.exe` - local Packwiz binary used for all pack operations.

## Core Rules for Changes

1. Use `packwiz.exe` for add/remove/update workflows whenever possible.
2. Run `packwiz refresh` after manual metadata/content edits.
3. Always bump `pack.toml` `version` whenever any pack file changes (mods, index, shaderpacks, configs, metadata) so launchers detect updates.
4. Keep `side` accurate in each mod metafile (`client`, `server`, or `both`).
5. Do not hand-edit `index.toml` unless absolutely necessary; let Packwiz regenerate it.

## Typical Actions

### 1) Add a Mod (Modrinth)

```powershell
.\packwiz.exe modrinth add <slug-or-id> -y
.\packwiz.exe refresh
```

If a specific version is required:

```powershell
.\packwiz.exe modrinth add <slug-or-id> --version-id <modrinth_version_id> -y
.\packwiz.exe refresh
```

### 2) Remove a Mod

```powershell
.\packwiz.exe remove <slug> -y
.\packwiz.exe refresh
```

Notes:
- The remove argument is the Packwiz project slug (usually metafile basename).
- If remove fails, check `mods/*.pw.toml` for the exact slug name.

### 3) Update a Single Mod

```powershell
.\packwiz.exe update <slug> -y
.\packwiz.exe refresh
```

### 4) Update All Mods

```powershell
.\packwiz.exe update -a -y
.\packwiz.exe refresh
```

### 5) Verify Installed Entries

```powershell
.\packwiz.exe list
```

Use this after major changes to confirm adds/removals.

## Standard Workflow (Feature/Fix)

1. Make change with Packwiz (`add`, `remove`, `update`) or edit `*.pw.toml` if needed.
2. Run `packwiz refresh`.
3. Validate expected entries with `packwiz list`.
4. Increment pack version in `pack.toml` (required for every pack change).
5. Re-run `packwiz refresh` if version or tracked files changed.
6. Check git diff/status and keep only intended changes.

## Troubleshooting Patterns

### Quilt says dependency is missing

- Confirm the dependency exists as `mods/<name>.pw.toml`.
- Confirm it appears in `index.toml` and `packwiz list`.
- Confirm `side` is correct (`both` for required client+server deps).
- Re-sync/reinstall launcher/server instance (stale instances are common).

### Quilt says two mods conflict ("breaks")

- Remove one of the conflicting mods.
- Refresh index and re-sync instances.

### Client/server mismatch persists after repo fix

- Bump `pack.toml` version.
- Rebuild or force re-sync the instance mod folder.

## Git Hygiene

Before commit:

```powershell
git status --short
git diff
```

Expect typical touched files:
- `pack.toml`
- `index.toml`
- `mods/*.pw.toml`
- `shaderpacks/*` (when adding/removing shader zips)

## Quick Checklist

- [ ] Mod metadata valid (`name`, `filename`, `download`, `update`)
- [ ] Correct `side` on affected mods
- [ ] `packwiz refresh` completed successfully
- [ ] `packwiz list` reflects intended state
- [ ] `pack.toml` version bumped for this change set
- [ ] Git working tree contains only intended files
