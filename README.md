# claude-skills

Claude Code skills by [Neo Ighodaro](https://neoighodaro.com), distributed as a plugin marketplace.

## Installation

```
/plugin marketplace add neoighodaro/claude-skills
/plugin install package-updates@neoighodaro
```

## Plugins

### package-updates

Two skills that teach agents to update dependencies without walking you into a supply chain attack. The reasoning behind them is written up in [Claude Code Skills for safe PHP and JS package updates](https://neoighodaro.com/blog/14-claude-skill-for-safe-dependency-updates-php-javascript-typescript/).

- **`package-update-js`** — for JavaScript/TypeScript projects (bun, pnpm, npm, yarn)
- **`package-update-php`** — for PHP/Composer projects

Both begin by detecting the project's own toolchain — which package manager the lockfile indicates, how Composer is invoked (directly, or through Sail/Docker/a Makefile), and which scripts serve as the type-check, static-analysis, and test steps — then use those commands for the rest of the run. No assumptions about your setup are baked in.

They differ in tooling but encode the same rules:

1. **Research before touching anything.** Supply chain incidents from the last six months get cross-referenced against the project's dependency list, plus the registry's own audit. Every package gets a verdict: SAFE, AFFECTED, or INVESTIGATE.
2. **The seven-day quarantine.** Never install a version less than seven days old. Compromised releases are almost always caught and yanked within hours or days, so an age gate skips the poisoned window entirely. Releases fixing a known CVE are exempt. Every major JS package manager now enforces this natively — but under a different name, in a different file, in a different unit (bun in seconds, pnpm and yarn in minutes, npm in days), so the skill carries the conversion table. Composer has no equivalent, so there the gate is checked by hand against `composer outdated`'s release dates.
3. **Scripts don't run until you say so.** Composer updates run with `--no-scripts`; post-install scripts only run after the update has been reviewed. Bun doesn't run lifecycle scripts for arbitrary dependencies by default.
4. **Pin everything, exactly.** No `^`, no `~`, no ranges. Updates happen through explicit `composer require pkg:x.y.z` / `bun add pkg@x.y.z`, never a blanket update command. (npm and pnpm write a `^` range by default, so `--save-exact` is mandatory there.)
5. **One at a time, verify after each.** Each package (or ecosystem group) is updated individually, followed by the project's own verification commands. Majors are never applied without explicit approval.

## License

[MIT](LICENSE) © Neo Ighodaro
