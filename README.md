# renovate-presets

Shared [Renovate](https://docs.renovatebot.com/) configuration presets.

## Usage

### Minimum — universal baseline

Every consumer should extend the default preset. It pulls in `config:best-practices`, automerges patch/digest/linter/tester/type updates, runs `lockFileMaintenance` weekly, groups AlpineJS and pnpm updates, and styles commits via the sibling `gitmoji` preset.

Non-patch updates are batched into the `before 4am on monday` window (`schedule:weekly`); patches run at any time. `prHourlyLimit` is raised to 6 so that batch can actually drain — at Renovate's default of 2, a 4-hour window caps the repo at 8 non-patch PRs per week. `lockFileMaintenance` is scheduled across all of Monday rather than sharing the same 4-hour window, so it is never crowded out by the weekly batch.

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>derteaser/renovate-presets"]
}
```

### Stack-specific additions

Named presets are composable — add any that match your project.

**Laravel / Filament / Livewire**

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>derteaser/renovate-presets",
    "github>derteaser/renovate-presets:laravel"
  ]
}
```

**Kirby CMS**

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>derteaser/renovate-presets",
    "github>derteaser/renovate-presets:kirby"
  ]
}
```

**Astro**

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>derteaser/renovate-presets",
    "github>derteaser/renovate-presets:astro"
  ]
}
```

**React Native**

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>derteaser/renovate-presets",
    "github>derteaser/renovate-presets:react-native"
  ]
}
```

Mix and match: a Laravel-API + React-Native-client repo extends all three of `default`, `laravel`, and `react-native`.

## Available presets

| Preset | Purpose |
|---|---|
| `default` | Universal baseline (best-practices + pnpm + AlpineJS + gitmoji) |
| `gitmoji` | Gitmoji commit style + labels; disables major automerge globally |
| `laravel` | Groups Filament and Livewire monorepos |
| `kirby` | Groups `getkirby/*` Composer packages |
| `astro` | Groups `@astro-community/*` plugins (core grouped by Renovate's built-in `monorepo:astro`) |
| `react-native` | Groups React Native core + React Navigation (Expo grouped by Renovate's built-in `monorepo:expo`) |

## Contributing

Changes ship to every consumer the moment they land on `main` — there's no build or publish step. Validate before merging:

```sh
npx --package renovate -- renovate-config-validator *.json
```
