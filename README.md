# renovate-preset

Preset config for my repositories.

<https://github.com/renovatebot/renovate>

## Usage

```json
{
  "extends": ["github>unional/renovate-preset"]
}
```

`github>unional/renovate-preset` resolves to `default.json` on the default
branch, so a change here reaches every consuming repo on its next run. There is
no tag to move.

## What the preset provides

- **Semver ranges are preserved** (`:preserveSemverRanges`) — a `^1.2.3` range
  stays a range; Renovate does not pin it.
- **No prereleases once a dependency is on one.** `ignoreUnstable` only blocks
  *stable to unstable*. Once a dependency is pinned to a prerelease, every later
  prerelease of the same line reads as an ordinary patch and flows straight
  through — that is how a `type-plus` 8.x beta reached eighteen published
  packages. The preset closes it from the other side: while the current version
  is a prerelease, only stable versions are candidates.
- **A 24 hour release soak** (`minimumReleaseAge: "1 day"`), matching pnpm's
  supply-chain `minimumReleaseAge`. Without it Renovate proposes versions the
  install itself will refuse, and the resulting trickle of red PRs is pressure to
  weaken the soak.
- **`rebaseWhen: "behind-base-branch"`** — branches are refreshed against the
  base branch rather than only when they conflict.

The preset deliberately carries **no automerge**. Enabling automerge is a
behaviour change in every consuming repo and belongs to each repo, not to the
preset. Repos that want it set it locally.

### Overriding

A consuming repo's own `packageRules` are applied after the preset's, so a repo
that deliberately tracks a prerelease (or wants a different soak) overrides it
locally:

```json
{
  "extends": ["github>unional/renovate-preset"],
  "packageRules": [
    {
      "matchPackageNames": ["typescript"],
      "allowedVersions": null
    }
  ]
}
```

One consequence of the prerelease guard worth knowing: a dependency with **no**
stable line at all stops getting update PRs, since every candidate is a
prerelease. Those show up on the dependency dashboard, and the override above is
the way out.
