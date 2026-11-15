# REA Mirror Preservation Policy

## Purpose

This repository preserves the upstream REA source and Git history so that
Converter can continue to use and maintain a known version if the upstream
project becomes unavailable or changes direction.

- Upstream: https://github.com/morluto/rea
- Mirror: https://github.com/sons1111/rea-mirror
- License: MIT (retain upstream copyright and license notices)

## Ref layout

The `main` branch is the mirror-maintenance branch. It contains the original
upstream snapshot plus our mirror-management files.

The scheduled workflow copies upstream refs into separate namespaces:

- Upstream branch `main` becomes mirror branch `upstream/main`.
- Upstream branch `feature/example` becomes `upstream/feature/example`.
- Upstream tag `vX.Y.Z` becomes `upstream/vX.Y.Z`.

Do not use the mirror's `main` branch as the canonical upstream source after
the maintenance workflow is added. Use `upstream/main` or a pinned commit
from that namespace.

## Synchronization behavior

- A GitHub Actions workflow fetches all upstream branches and tags daily.
- It can also be started manually with workflow_dispatch.
- Only the reserved `upstream/*` branch and tag namespaces are force-updated.
- Local maintenance files and the maintenance branch are not overwritten by
  upstream changes.
- Upstream refs that disappear are not automatically deleted from this mirror.
  They remain available as preserved refs unless manually removed.
- Before a non-fast-forward branch update, the workflow archives the previous
  branch head as an immutable tag under
  `archive/upstream-branch-heads/<commit-sha>`.
- If an upstream tag object changes, the previous tag object is archived under
  `archive/upstream-tag-objects/<object-sha>` before the namespaced tag moves.
- Upstream branches and tags that disappear are not automatically deleted from
  this mirror; old refs remain available unless manually removed.

## Required GitHub settings

After adding the workflow:

1. Open Settings -> Actions -> General.
2. Ensure Actions are enabled for this fork.
3. Under Workflow permissions, select Read and write permissions.
4. Save the setting.
5. Open Actions -> Upstream archive sync and run it once manually.

The workflow requests only `contents: write` through the repository-scoped
GITHUB_TOKEN. It does not need a personal access token.

## Promotion and release policy

Fetching upstream does not automatically change Converter's active engine.
Converter must pin a specific mirror commit. A new upstream version should be
reviewed, tested, and promoted separately.

Keep the upstream license, copyright notices, third-party notices, and
dependency licenses with every redistributed build.
