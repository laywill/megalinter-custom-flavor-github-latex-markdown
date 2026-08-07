# MegaLinter Custom Flavor: GitHub LaTeX Markdown

A [MegaLinter](https://megalinter.io/) custom flavor containing only the
linters used across my GitHub + LaTeX + Markdown repositories, so CI pulls a
much smaller image than the official all-in-one flavors.

It is built from official MegaLinter images and maintained at
[laywill/megalinter-custom-flavor-github-latex-markdown](https://github.com/laywill/megalinter-custom-flavor-github-latex-markdown)
by William Lay.

## Embedded linters

- [ACTION_ACTIONLINT](https://megalinter.io/latest/descriptors/action_actionlint/)
- [EDITORCONFIG_EDITORCONFIG_CHECKER](https://megalinter.io/latest/descriptors/editorconfig_editorconfig_checker/)
- [JSON_JSONLINT](https://megalinter.io/latest/descriptors/json_jsonlint/)
- [JSON_V8R](https://megalinter.io/latest/descriptors/json_v8r/)
- [LATEX_CHKTEX](https://megalinter.io/latest/descriptors/latex_chktex/)
- [MARKDOWN_MARKDOWNLINT](https://megalinter.io/latest/descriptors/markdown_markdownlint/)
- [MARKDOWN_MARKDOWN_TABLE_FORMATTER](https://megalinter.io/latest/descriptors/markdown_markdown_table_formatter/)
- [REPOSITORY_BETTERLEAKS](https://megalinter.io/latest/descriptors/repository_betterleaks/)
- [REPOSITORY_DEVSKIM](https://megalinter.io/latest/descriptors/repository_devskim/)
- [REPOSITORY_DUSTILOCK](https://megalinter.io/latest/descriptors/repository_dustilock/)
- [REPOSITORY_GITLEAKS](https://megalinter.io/latest/descriptors/repository_gitleaks/)
- [REPOSITORY_GIT_DIFF](https://megalinter.io/latest/descriptors/repository_git_diff/)
- [REPOSITORY_GRYPE](https://megalinter.io/latest/descriptors/repository_grype/)
- [REPOSITORY_KINGFISHER](https://megalinter.io/latest/descriptors/repository_kingfisher/)
- [REPOSITORY_OSV_SCANNER](https://megalinter.io/latest/descriptors/repository_osv_scanner/)
- [REPOSITORY_SECRETLINT](https://megalinter.io/latest/descriptors/repository_secretlint/)
- [REPOSITORY_SYFT](https://megalinter.io/latest/descriptors/repository_syft/)
- [REPOSITORY_TRIVY](https://megalinter.io/latest/descriptors/repository_trivy/)
- [REPOSITORY_TRIVY_SBOM](https://megalinter.io/latest/descriptors/repository_trivy_sbom/)
- [REPOSITORY_TRUFFLEHOG](https://megalinter.io/latest/descriptors/repository_trufflehog/)
- [SPELL_CSPELL](https://megalinter.io/latest/descriptors/spell_cspell/)
- [SPELL_LYCHEE](https://megalinter.io/latest/descriptors/spell_lychee/)
- [YAML_V8R](https://megalinter.io/latest/descriptors/yaml_v8r/)
- [YAML_YAMLLINT](https://megalinter.io/latest/descriptors/yaml_yamllint/)

The authoritative list lives in
[megalinter-custom-flavor.yml](megalinter-custom-flavor.yml).

## How to use the custom flavor

Follow the
[MegaLinter installation guide](https://megalinter.io/latest/install-assisted/),
then swap the official flavor for this one.

### GitHub Actions

Replace the `oxsecurity/megalinter` step in `.github/workflows/mega-linter.yml`
with this repository's action:

```yaml
- name: MegaLinter
  id: ml
  uses: laywill/megalinter-custom-flavor-github-latex-markdown@main
  env:
    VALIDATE_ALL_CODEBASE: true
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Pin to a release tag rather than `@main` if you want reproducible runs, e.g.
`@v9.6.0`.

### Docker

Use the published image directly:

```sh
docker run --rm -v "$(pwd):/tmp/lint" \
  ghcr.io/laywill/megalinter-custom-flavor-github-latex-markdown/megalinter-custom-flavor:latest
```

Or with `mega-linter-runner`:

```sh
npx mega-linter-runner \
  --image ghcr.io/laywill/megalinter-custom-flavor-github-latex-markdown/megalinter-custom-flavor:latest
```

### Available image tags

| Tag                    | Built from                                     |
| ---------------------- | ---------------------------------------------- |
| `latest`               | The most recent release                        |
| `v9.6.0` (release tag) | The matching upstream MegaLinter release       |
| `beta`                 | Pushes to any branch other than `main`         |

## How the flavor is built and updated

1. **Version sync**: the `check-new-megalinter-version` workflow runs daily,
    compares upstream MegaLinter tags with this repository's tags, and creates a
    matching release when a new MegaLinter version appears.
2. **Build**: each release triggers `megalinter-custom-flavor-builder`, which
    builds an image containing only the linters listed above and pushes it to
    the GitHub Container Registry (and Docker Hub, if configured).
3. **Consumption**: [action.yml](action.yml) points downstream repositories at
    the published image.

## Configuration requirements

### Package visibility

For other repositories (or anyone else) to pull the image without
authenticating, the `megalinter-custom-flavor` package must be set to **Public**
under the repository's Packages settings. This is a one-off manual step after
the first successful build.

### Optional: Personal Access Token (use with care)

> **Security warning**: Using a Personal Access Token (PAT) is **not
> recommended**. Open-source projects have been heavily targeted by
> supply-chain attacks in recent months, and a leaked or compromised PAT can
> give attackers broad write access to your repository — better safe than sorry!
> If you do not need fully automatic daily version sync, you can skip the PAT
> entirely and trigger the `check-new-megalinter-version` workflow manually
> whenever you want to upgrade.

If you decide automatic daily releases are worth the trade-off, configure a
`PAT_TOKEN` secret as a **repository-scoped fine-grained token** with:

- **Repository access**: Only select repositories (select this repository)
- **Repository permissions**:
  - Contents: Read and write
  - Actions: Read and write

Rotate the token regularly.

### Optional: Docker Hub publishing

To publish to Docker Hub in addition to ghcr.io, configure:

- `DOCKERHUB_REPO` variable (e.g. your Docker Hub username)
- `DOCKERHUB_USERNAME` secret
- `DOCKERHUB_PASSWORD` secret

## Triggering a build manually

1. **Create a GitHub release**: builds an image tagged with the release tag
    (e.g. `v9.6.0`) and updates `latest`.
2. **Push to any branch except `main`**: builds a `beta` tagged image for
    testing.
3. **Run the workflow by hand**: Actions → *Build & Push MegaLinter Custom
    Flavor* → *Run workflow*.

## Changing the linter list

1. Edit the `linters` list in
    [megalinter-custom-flavor.yml](megalinter-custom-flavor.yml).
2. Re-run the generator so the derived files stay in sync:

    ```sh
    npx mega-linter-runner@9.6.0 --custom-flavor-setup
    ```

3. Re-apply the local adjustments described in
    [CONTRIBUTING.md](CONTRIBUTING.md), commit, and create a release.

## Development

This repo runs [MegaLinter](https://megalinter.io/) in CI on every push and pull
request to `main`. Config lives in [.mega-linter.yml](.mega-linter.yml).

- [.editorconfig](.editorconfig) and
  [.vscode/settings.json](.vscode/settings.json) keep editor formatting
  consistent (indentation, line endings, trailing whitespace).
- [.gitattributes](.gitattributes) normalizes line endings and marks binary
  files.
- [pre-commit](https://pre-commit.com/) hooks in
  [.pre-commit-config.yaml](.pre-commit-config.yaml) catch common issues
  locally before you push. Install with `pre-commit install`.
- This repo includes a [Dev Container](.devcontainer/devcontainer.json) — open
  it in VS Code and choose "Reopen in Container" for a consistent,
  pre-configured environment.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for how to file issues and submit pull
requests. This project follows a [Code of Conduct](CODE_OF_CONDUCT.md). See
[SECURITY.md](SECURITY.md) for how to report vulnerabilities.

## License

Licensed under the [Apache License 2.0](LICENSE).

[![MegaLinter is graciously provided by OX Security](https://raw.githubusercontent.com/oxsecurity/megalinter/main/docs/assets/images/ox-banner.png)](https://www.ox.security/?ref=megalinter)
