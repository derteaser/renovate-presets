---
name: new-preset
description: Scaffold a new named Renovate preset JSON file at the repository root, validate it, and remind the user that merging to main ships immediately to every consumer.
disable-model-invocation: true
---

# new-preset

Create a new named Renovate preset at the repository root. The preset becomes available to consumers as `github>derteaser/renovate-presets:<name>`.

## Arguments

The user invokes as `/new-preset <name>` where `<name>` is the preset slug (no extension). If they don't supply one, ask for it.

## Steps

1. Refuse if `<name>.json` already exists at the repo root — ask whether they want to edit the existing preset instead.

2. Create `<name>.json` at the repo root with this skeleton:

   ```json
   {
     "$schema": "https://docs.renovatebot.com/renovate-schema.json",
     "description": "TODO: one-line description of what this preset does",
     "packageRules": []
   }
   ```

   Replace the `description` placeholder with a real description if the user provided one; otherwise leave the TODO and flag it.

3. Validate the new file:

   ```bash
   npx --yes --package renovate -- renovate-config-validator <name>.json
   ```

4. Report to the user:
   - The new file path and its consumer `extends:` string (`github>derteaser/renovate-presets:<name>`).
   - A reminder: **merging to `main` immediately affects every repo that extends this preset** — they should review carefully and consider announcing the change before pushing.
   - Suggest they also update `CLAUDE.md` if the new preset introduces a pattern worth documenting.
