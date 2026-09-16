# Package Monorepo Template

Opinionated starter for publishing multiple TypeScript packages from a single repository using pnpm workspaces, Rollup builds, Vitest, and automated semantic versioning via Changesets.

## Tooling

- [pnpm workspaces](https://pnpm.io/workspaces)
- [TypeScript](https://www.typescriptlang.org/)
- [Rollup](https://rollupjs.org/)
- [Vitest](https://vitest.dev/)
- [ESLint](https://eslint.org/)
- [Prettier](https://prettier.io/)
- [Husky](https://typicode.github.io/husky/#/) + [lint-staged](https://github.com/okonet/lint-staged)
- [Changesets](https://github.com/changesets/changesets)
- [CI/CD Pipelines](https://docs.github.com/en/actions)

## Releasing

Releases are automated by the [release workflow](.github/workflows/release.yml):

1. Add a changeset (`pnpm changeset`) in the pull request that changes a package.
2. When that pull request lands on `main`, the workflow opens or updates a **Version Packages** pull request.
3. Merging the **Version Packages** pull request bumps versions, updates the changelogs, builds the packages and publishes them to npm, along with the git tags and GitHub releases.

### Trusted publishing

The workflow authenticates with npm through [trusted publishing](https://docs.npmjs.com/trusted-publishers): GitHub Actions mints a short-lived OIDC token that is exchanged for a publish credential, so there is no `NPM_TOKEN` secret to store or rotate, and provenance is attached automatically.

Configure it once per package on npmjs.com, under **Settings → Trusted publisher**, with:

- Publisher: **GitHub Actions**
- Organization or user: the owner of this repository
- Repository: this repository
- Workflow filename: `release.yml`

The job that publishes needs the `id-token: write` permission and has to run on a GitHub-hosted runner — self-hosted runners cannot use trusted publishing. `changeset publish` publishes through `pnpm publish` in a pnpm workspace, so the workflows run pnpm 12, the first version with its own OIDC support.
