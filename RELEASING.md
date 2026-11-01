# Releasing pytm

Releases are built and published by `.github/workflows/release.yml` when a
`vX.Y.Z` tag is pushed. Nothing is uploaded by hand and no API tokens exist.

## Steps

1. Open a PR that sets `version` in `pyproject.toml` and moves the
   `CHANGELOG.md` entries from `[Unreleased]` to a new `[X.Y.Z] - date`
   section. Call out behavior changes under `BREAKING`.
2. Merge it and wait for `build+test` to pass on `master`.
3. Tag the merged commit and push the tag (needs Maintain or Admin):

   ```
   git fetch upstream
   git tag vX.Y.Z upstream/master
   git push upstream vX.Y.Z
   ```

4. Watch the run: `gh run watch -R OWASP/pytm`.
5. Check the new version on https://test.pypi.org/project/pytm/, then approve
   the `pypi` deployment (Actions run > Review deployments). A maintainer
   listed as a reviewer on the `pypi` environment must approve it.
6. Confirm the release on PyPI and GitHub. The GitHub release is created
   automatically with generated notes and the built files.

## What the workflow does

| Job | Environment | Runs for |
|---|---|---|
| `build` | none | every `v*` tag |
| `publish-testpypi` | `testpypi` | every `v*` tag |
| `publish-pypi` | `pypi` (reviewer approval) | stable tags only |
| `github-release` | none | stable tags only |

- `build` fails if the tag does not match `version` in `pyproject.toml`, or if
  the tagged commit is not on the default branch.
- Tags like `v1.5.0rc1` are published to TestPyPI only. Use one to rehearse a
  release.
- PyPI and TestPyPI versions cannot be reused. If a release goes wrong, yank
  it and publish a new version.

## Setup this depends on

- PyPI and TestPyPI trusted publishers for `OWASP/pytm`, workflow
  `release.yml`, environments `pypi` and `testpypi`.
- GitHub environments `pypi` (required reviewers, administrator bypass off,
  `v*` tags only) and `testpypi` (`v*` tags only).
- A `v*` tag ruleset limiting who can create, move or delete release tags.
