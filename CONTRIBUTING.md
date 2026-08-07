# Contributing

Thanks for taking the time to contribute.

## Reporting bugs / requesting features

Please use the provided [issue templates](.github/ISSUE_TEMPLATE/) when
opening an issue.

## Submitting changes

1. Fork/branch and make your changes.
2. If you have [pre-commit](https://pre-commit.com/) installed, run
    `pre-commit run --all-files` before committing — it catches most issues
    locally, faster than waiting for CI.
3. Open a pull request using the provided
    [PR template](.github/PULL_REQUEST_TEMPLATE.md).
4. [MegaLinter](https://megalinter.io/) runs in CI on every PR; fix any
    reported issues (or apply its suggested auto-fixes).
5. A reviewer will be automatically requested per
    [CODEOWNERS](.github/CODEOWNERS).

## Regenerating the flavor

`megalinter-custom-flavor.yml`, `action.yml`, `README.md` and the two
`megalinter-custom-flavor-*` / `check-new-megalinter-version` workflows are
produced by `npx mega-linter-runner@9.6.0 --custom-flavor-setup`. Re-running
the generator **overwrites** them, so the following local adjustments have to
be re-applied afterwards, otherwise this repo's own MegaLinter run fails:

- **Action pinning** — every `uses:` in the generated workflows is pinned to a
  commit SHA with a trailing `# vX.Y.Z` comment (required by `ACTION_ZIZMOR`'s
  `unpinned-uses` audit, and kept current by Dependabot).
- **`persist-credentials: false`** on the builder workflow's checkout step
  (`ACTION_ZIZMOR` `artipacked`).
- **Quoted redirect targets** — `>> "$GITHUB_OUTPUT"` /
  `>> "$GITHUB_STEP_SUMMARY"`, and the summary step's single grouped redirect
  (`ACTION_ACTIONLINT` / ShellCheck `SC2086` and `SC2129`).
- **No trailing whitespace** in the generated workflows
  (`EDITORCONFIG_EDITORCONFIG_CHECKER`).
- **README** — the generated README duplicates its "How to use the custom
  flavor" section (`MARKDOWN_MARKDOWNLINT` `MD024`) and drops this repo's
  Development / Contributing / License sections; the committed README merges
  both.

## Code of Conduct

This project follows a [Code of Conduct](CODE_OF_CONDUCT.md). By
participating, you're expected to uphold it.
