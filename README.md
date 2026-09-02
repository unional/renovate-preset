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
- **No jump from a stable version onto a prerelease** (`ignoreUnstable`). A
  dependency already tracking a prerelease line keeps getting that line's later
  prereleases; the preset does not stand between a repo and a prerelease it has
  deliberately adopted.
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

A consuming repo's own settings and `packageRules` are applied after the
preset's, so a repo that wants a different soak, or a bound on one dependency,
sets it locally:

```json
{
  "extends": ["github>unional/renovate-preset"],
  "packageRules": [
    {
      "matchPackageNames": ["typescript"],
      "allowedVersions": "<6"
    }
  ]
}
```
