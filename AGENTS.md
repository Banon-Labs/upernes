# Agent Instructions

This repository is a fork of `mandraga/upernes`, a NES-to-SNES ROM recompilation experiment. The current strategic goal is to expand mapper support incrementally while preserving a working baseline.

## Project direction

- Treat mapper support as an incremental platform effort, not a one-off game hack.
- Preferred roadmap:
  1. Baseline mapper-0 conversion with `Super Mario Bros.` / existing test ROMs.
  2. Add mapper 2 / UxROM using `Mega Man (U).nes` as the representative target.
  3. Add mapper 1 / MMC1 using `Mega Man 2 (U).nes`.
  4. Add mapper 4 / MMC3 using `Mega Man 3 (U) [!].nes` first.
  5. Validate broader MMC3 behavior with `Mega Man 4 (U).nes`, `Mega Man 5 (U).nes`, and `Mega Man 6 (U).nes`.
  6. Then attempt `Crystalis.nes` as a higher-risk MMC3 + battery/SRAM target.
- Do not remove mapper/header checks just to get farther; unsupported mapper work must add real mapper semantics and tests/diagnostics.
- Use Docker as the default reproducible toolchain path unless local tool availability is explicitly being improved.

## Build and conversion quick reference

```bash
# Build Docker image when needed
cd docker && ./init_docker.sh

# Convert a ROM through the Docker toolchain from the repo root
docker run --rm --user "$(id -u):$(id -g)" \
  -v "$PWD:/opt/workspace" \
  -v /mnt/y/Emulation/ROMs/NES:/roms:ro \
  -w /opt/workspace \
  upernes_image \
  bash -lc 'bash ./conversion.sh "/roms/Super Mario Bros. (JU) (PRG0) [!].nes"'
```

## Beads Issue Tracker

This project uses **bd (beads)** for issue tracking. Use `bd` for all task tracking; do not create markdown TODO lists.

### Quick reference

```bash
bd ready --json
bd show <id> --json
bd update <id> --status in_progress --json
bd close <id> --reason "Completed" --json
```

## Workspace policy

- The workspace-level `~/projects/AGENTS.md` remains binding.
- Do not use `git push` unless the user explicitly authorizes it for the current task. Beads/Dolt sync is separate from git push.
- Do not run bare `bd dolt pull`; use the workspace-approved Beads sync sequence when syncing is required.
- Before claiming completion for script/tooling changes, run the relevant workspace smoke tests required by `~/projects/AGENTS.md`.

## Session completion

1. File/update Beads issues for remaining work.
2. Run relevant quality gates for changed code/scripts.
3. Commit local work when appropriate.
4. Sync Beads only with the workspace-approved sequence when needed.
5. Report concise handoff context and validation evidence.
