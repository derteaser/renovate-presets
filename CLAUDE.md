# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

Shared [Renovate](https://docs.renovatebot.com/) configuration presets, consumed by other repos via `extends: ["github>derteaser/renovate-presets"]` (default) or `"github>derteaser/renovate-presets:gitmoji"` (named preset).

## Files

- `default.json` — the default preset. Extends `config:best-practices`, enables a set of `:automerge*` presets, turns on `lockFileMaintenance` with automerge, and defines `packageRules` (pnpm auto-merge for minor/patch/pin; grouping for AlpineJS, Turf, Filament monorepos). Also extends the sibling `gitmoji` preset.
- `gitmoji.json` — named preset (`:gitmoji`) that styles commits/labels with gitmoji (`⬆️ Upgrade`, `📌 Pin`, `⬇️ Downgrade`) and disables major-version automerge.

Both files must validate against `https://docs.renovatebot.com/renovate-schema.json` (referenced via `$schema`).

## Editing guidance

- Changes here ship to every consumer the moment they land on `main` — Renovate fetches presets from GitHub directly, there is no build/publish step. Treat `main` as production.
- When adding a new named preset, create `<name>.json` at the repo root; consumers reference it as `github>derteaser/renovate-presets:<name>`.
- Verify preset changes with Renovate's config validator before merging: `npx --package renovate -- renovate-config-validator default.json gitmoji.json`.
- Commit messages in this repo follow gitmoji style (see recent `git log`): prefix with an emoji matching the change type (🔧 config, 🐛 fix, etc.).
