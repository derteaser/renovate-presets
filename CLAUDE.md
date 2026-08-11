# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

Shared [Renovate](https://docs.renovatebot.com/) configuration presets, consumed by other repos via `extends: ["github>derteaser/renovate-presets"]` (default) or `"github>derteaser/renovate-presets:<name>"` (named presets).

Named presets are **additive building blocks** — consumers extend `default` plus any ecosystem preset(s) they need. See [README.md](README.md) for the full composition pattern.

## Files

- `default.json` — the default preset. Extends `config:best-practices`, enables a set of `:automerge*` presets, turns on `lockFileMaintenance` with automerge, and defines `packageRules` (pnpm auto-merge for minor/patch/pin; AlpineJS monorepo grouping). Also extends the sibling `gitmoji` preset.
  - Scheduling is load-bearing and easy to break: `schedule:weekly` confines non-patch updates to `before 4am on monday`, and Renovate's default `prHourlyLimit` of 2 then caps a repo at 8 non-patch PRs per week. `prHourlyLimit` is raised to 6 for that reason, and `lockFileMaintenance` gets its own window offset from the batch so it is neither starved nor spammed: it **creates** in `after 4am and before 6am on monday` (after the batch window closes, so it is not starved by the batch's hourly PR budget) and **automerges** in a separate, later window (`after 6am and before 10am on monday`). Splitting create from automerge is what prevents PR spam: with `automerge` on and a single wide-open window (the earlier `* * * * 1` all-Monday schedule), each hourly run would create → automerge → recreate the PR all day. Keeping the create window closed before the automerge window opens means the PR can't be recreated after it merges — one PR per week, merged the same Monday. Renovate ignores minute-granularity in cron, so never widen these into an all-day window. Changing any of these together requires re-checking that lock file maintenance still gets a slot and does not loop.
- `gitmoji.json` — named preset (`:gitmoji`) that styles commits/labels with gitmoji (`⬆️ Upgrade`, `📌 Pin`, `⬇️ Downgrade`) and disables major-version automerge globally.
- `laravel.json` — named preset (`:laravel`) for Laravel projects. Groups the Filament and Livewire monorepos.
- `kirby.json` — named preset (`:kirby`) for Kirby CMS projects. Groups `getkirby/*` Composer packages.
- `astro.json` — named preset (`:astro`) for Astro projects. Groups `@astro-community/*` community plugins (core `astro` + `@astrojs/*` are grouped by Renovate's built-in `monorepo:astro`).
- `react-native.json` — named preset (`:react-native`). Groups React Native core and React Navigation (Expo is grouped by Renovate's built-in `monorepo:expo`).

All preset files must validate against `https://docs.renovatebot.com/renovate-schema.json` (referenced via `$schema`).

## Editing guidance

- Changes here ship to every consumer the moment they land on `main` — Renovate fetches presets from GitHub directly, there is no build/publish step. Treat `main` as production.
- When adding a new named preset, create `<name>.json` at the repo root; consumers reference it as `github>derteaser/renovate-presets:<name>`.
- Verify preset changes with Renovate's config validator before merging: `npx --package renovate -- renovate-config-validator *.json`.
- Commit messages in this repo follow gitmoji style (see recent `git log`): prefix with an emoji matching the change type (🔧 config, 🐛 fix, etc.).
