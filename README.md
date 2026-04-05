# renovate-config

Shared Renovate presets for `matt-riley` repositories.

## Presets

- `default` - Baseline rules (`config:recommended`, semantic commits, a 2-day release cooldown before dependency PRs, automerge for minor/patch, guarded majors, vulnerability alerts, and lockfile maintenance).
- `node` - Groups TypeScript, linting, testing, and build-tool updates; automerges `devDependencies`.
- `go` - Groups common Go dependency ecosystems and automerges indirect minor/patch updates.
- `terraform` - Groups Terraform providers and modules, and keeps Terraform/OpenTofu runtime version updates manual.
- `docker` - Groups image updates, pins digests, and requires review for major updates.
- `github-actions` - Pins GitHub Action digests and groups workflow dependency updates.
- `astro` - Groups Astro, CSS/Tailwind, and Cloudflare packages.

## Usage

Add a `.github/renovate.json` file in your repo:

```json
{
  "extends": [
    "github>matt-riley/renovate-config",
    "github>matt-riley/renovate-config:terraform",
    "github>matt-riley/renovate-config:github-actions"
  ]
}
```

## Notes

- `"github>matt-riley/renovate-config"` loads `default.json`.
- Add only the additional presets your project needs.
