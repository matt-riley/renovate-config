# renovate-config

Shared Renovate presets for `matt-riley` repositories.

## Presets

- `default` - Baseline rules (`config:recommended`, semantic commits, a 7-day release cooldown, weekly batches, and CI-gated automerge).
- `schedule-monday` … `schedule-friday` - Move a repository's Renovate schedule to that weekday morning (`Europe/London`). Extend **last**. Monday matches the default.
- `no-automerge` - Turn automerge off. Extend **last** when CI does not report status checks to GitHub.
- `node` - Labels TypeScript, linting, testing, and build-tool updates. Configures Renovate auth for `@matt-riley` packages on GitHub Packages (requires `GITHUB_PACKAGES_TOKEN` at Renovate bot admin level; GitHub-hosted Renovate may auto-provision). Skips the release cooldown for `@matt-riley/*` packages so their PRs open immediately.
- `go` - Labels common Go dependency ecosystems, keeps Go toolchain updates in their own group, and runs `gomodTidy` after Go module updates.
- `terraform` - Labels Terraform providers and modules. Terraform/OpenTofu runtime version updates stay in their own group and require review.
- `docker` - Labels image updates and requires review for major image updates.
- `github-actions` - Pins GitHub Action digests, labels workflow dependency updates, and requires review for major updates.
- `astro` - Labels Astro, CSS/Tailwind, and Cloudflare packages.
- `zig` - Groups `build.zig.zon` dependency updates and requires manual review (automerge disabled) because Zig package updates can introduce breaking toolchain or API changes.

## Automerge

Automerge is off unless a rule turns it on.

| Update | Automerge |
| --- | --- |
| `patch`, `digest`, `pinDigest` | After GitHub reports passing status checks |
| GitHub Actions `pin`, `digest`, `pinDigest` | After GitHub reports passing status checks |
| `minor`, other `pin`, `major` | No |
| `matt-riley/go-api-template` (any update) | No |
| Vulnerability alerts | No, and they stay out of the weekly batches |
| Zig packages, Terraform/OpenTofu runtime | No |

`platformAutomerge` is `false`. Renovate merges the PR itself on a later run, and only when the branch status is green. `ignoreTests` is not set, so it stays at its default `false`: Renovate will not treat a branch with no real status checks as green.

GitHub's combined status for a commit with zero statuses is `pending` (`statuses: []`). Renovate then reports branch status `yellow` when there are also no check runs, so a repository whose CI never reports to GitHub does not automerge. `matt-riley/go-api-template` is also excluded explicitly, because its tests run on Woodpecker. Other repositories in that situation should extend `no-automerge` last:

```json
{
  "extends": [
    "github>matt-riley/renovate-config",
    "github>matt-riley/renovate-config:no-automerge"
  ]
}
```

Lock file maintenance still automerges when status checks pass. It follows the same weekday window and is never grouped with dependency updates. It does not automerge on `go-api-template`.

## Weekly schedule

Non-major updates are grouped into two pull requests so a minor update cannot be merged just because it shares a branch with a patch:

- `weekly patch and digest updates` — patch, digest, pinDigest, and GitHub Actions pins. This PR automerges only when every update on it is allowed to automerge and CI is green.
- `weekly minor and pin updates` — minor updates and other version pins. This PR never automerges.

Both open in the same weekday window: before 08:00 `Europe/London`. The default preset uses Monday. A shared preset cannot see which repository is extending it, so each repository adds one schedule preset **at the end** of `extends`. Until it does, it stays on Monday with every other repository that has not picked a day.

Zig packages, Go toolchain updates, Terraform/OpenTofu runtime updates, major updates, and vulnerability alerts stay out of those two batches.

### Which preset to extend

Alphabetical round-robin across Monday–Friday. New repositories should extend the weekday that currently has the fewest repositories, and extend that preset last.

| Repository | Preset |
| --- | --- |
| `.pi` | `schedule-monday` |
| `agengit` | `schedule-monday` |
| `agengit-docs` | `schedule-tuesday` |
| `agent-skills` | `schedule-wednesday` |
| `astro-site-template` | `schedule-thursday` |
| `brasshandoil` | `schedule-friday` |
| `crap` | `schedule-monday` |
| `deadwood` | `schedule-tuesday` |
| `elysia-crud-template` | `schedule-wednesday` |
| `eyrie` | `schedule-tuesday` (pending re-pin: currently pinned to `c66f71af`, which predates the schedule presets) |
| `flags` | `schedule-wednesday` |
| `flagz` | `schedule-thursday` |
| `gh-mfmatt` | `schedule-friday` |
| `go-api-template` | `schedule-monday` (automerge already disabled) |
| `gravitation` | `schedule-thursday` |
| `homestead` | `schedule-friday` |
| `hono-express` | `schedule-monday` |
| `hopcli` | `schedule-tuesday` |
| `infra` | `schedule-tuesday` |
| `jev-oni` | `schedule-wednesday` |
| `kes` | `schedule-thursday` (pending re-pin: currently pinned to `c66f71af`, which predates the schedule presets) |
| `lastfm-sync` | `schedule-friday` |
| `lexi` | `schedule-monday` |
| `liner` | `schedule-tuesday` |
| `lore` | `schedule-wednesday` |
| `matt-riley` | `schedule-thursday` |
| `matt-riley-ci` | `schedule-friday` |
| `matt-riley.dev` | `schedule-wednesday` |
| `mattriley.tools` | `schedule-monday` |
| `mattriley.wtf` | `schedule-thursday` |
| `newbrew` | `schedule-tuesday` |
| `nvim-plugin-template` | `schedule-wednesday` |
| `pandawaffle` | `schedule-friday` |
| `pds` | `schedule-monday` |
| `pi-browser` | `schedule-tuesday` |
| `pi-extensions` | `schedule-thursday` |
| `pint.nvim` | `schedule-friday` |
| `renovate-config` | `schedule-monday` |
| `repos-sync` | `schedule-wednesday` |
| `skill-evaluator` | `schedule-tuesday` |
| `slides.nvim` | `schedule-wednesday` |
| `ts-package-template` | `schedule-thursday` |
| `waystone.nvim` | `schedule-friday` |
| `workv3` | `schedule-thursday` |

## Usage

Add a `.github/renovate.json` file in your repo. Put the schedule preset last:

```json
{
  "extends": [
    "github>matt-riley/renovate-config",
    "github>matt-riley/renovate-config:node",
    "github>matt-riley/renovate-config:github-actions",
    "github>matt-riley/renovate-config:schedule-wednesday"
  ]
}
```

## Notes

- `"github>matt-riley/renovate-config"` loads `default.json`.
- Add only the additional presets your project needs.
- Extend at most one `schedule-*` preset, and extend it after the other presets so it wins.
- Extend `no-automerge` last if CI does not report checks to GitHub. `go-api-template` is already covered by `default`.
