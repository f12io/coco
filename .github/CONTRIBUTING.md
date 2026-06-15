# Contributing to Coco

Thanks for taking the time to improve Coco. This guide keeps the project workflow predictable for contributors and maintainers.

## Getting Started

1.  **Fork** the repo on GitHub.
2.  **Clone** the project to your own machine.
3.  **Install dependencies**:
    ```bash
    npm install
    ```

## Development Workflow

1.  **Create a branch** for your feature or fix:
    ```bash
    git checkout -b feature/amazing-new-color-space
    ```
2.  **Develop** in the `src` directory.
    - `src/data`: Named colors and static data.
    - `src/spaces`: Color space parsers and serializers (e.g., `hsl.ts`, `oklch.ts`).
    - `src/core`: Core conversion logic and types.
3.  **Test** your changes.
    - Run tests in watch mode while you work:
      ```bash
      npm test
      ```
    - Add new tests to `test/` for any new functionality.

## Testing & Verification

We use **Vitest** for testing.

- **Run all tests**: `npm test`
- **Run coverage**: `npm run coverage`

Please ensure all tests pass before submitting your PR. If you are fixing a bug, please include a regression test.

## Coding Style

This project uses **Prettier** and **ESLint** to ensure code consistency.

- **Format code**:
  ```bash
  npm run format
  ```
- **Lint code**:

  ```bash
  npm run lint
  ```

- Use **TypeScript** for everything.
- Prefer functional implementations over classes where possible.
- Keep dependencies minimal (or zero, if possible).

## Building

To build the distribution files (including minified outputs and type definitions):

```bash
npm run build
```

This generates:

- `dist/f12io-coco.js` (ESM)
- `dist/f12io-coco.cjs` (CommonJS)
- `dist/f12io-coco.umd.cjs` (UMD)
- `dist/f12io-coco.d.ts` (TypeScript Declarations)

## Pull Requests

- Work from a feature or fix branch created from `main`.
- Keep changes focused on one concern.
- Add or update tests when behavior changes.
- Run lint, tests, and build before opening a pull request. When ready open a PR to `main`.
- Do not commit generated release artifacts unless the change specifically requires it.

## Branch Naming

Use short, descriptive branch names with a type prefix:

```text
feat/hsl-parser
fix/color-alpha
test/conversions
docs/color-spaces
chore/github-actions
```

Prefer lowercase words separated with hyphens. Match the prefix to the main intent of the change.

Recommended pre-PR check:

```sh
npm run lint
npm test
npm run build
```

## Commit Style

Use a lightweight Conventional Commits style:

Good examples:

```text
test: add color conversion coverage
fix: correct color alpha serialization
chore: update release workflow validation
```

## Release Cycle

Releases are cut from `main` with npm's version command through the project release script.

Before releasing, make sure all intended changes have already landed on remote `main`.

```sh
git checkout main
git pull --ff-only origin main
npm run release -- patch
```

You can also release an explicit version:

```sh
npm run release -- 2.0.1
```

The release script runs:

```sh
npm version <version>
git push origin main --follow-tags
```

The `preversion` lifecycle script runs before npm creates the version commit and tag. It requires:

- current branch is `main`
- working tree is clean
- local `main` exactly matches `origin/main`
- lint passes
- tests pass
- build passes

After the version commit and tag are pushed, the GitHub release workflow validates the tag again before publishing. It requires:

- tag is semver-shaped, such as `v2.0.1`
- tag points to current `origin/main` HEAD
- tag is the latest semver tag on `main`
- tag version matches `package.json`
- `package-lock.json` versions match `package.json`
- package artifacts and npm package contents are present

If validation passes, the workflow creates a draft GitHub Release with assets, publishes to npm, then publishes the GitHub Release.

## Failed Releases

If the workflow fails before npm publish, check whether a draft GitHub Release was created for the tag. Delete or update that draft before retrying from a corrected commit.

If npm publish succeeds but the final GitHub Release publish step fails, do not create a new npm version just to retry the GitHub Release. Publish the existing draft release for the already-published tag:

```sh
gh release edit v2.0.1 --draft=false --latest
```

## Reporting Issues

When opening an issue, include:

- Coco version
- browser/runtime environment
- minimal reproduction
- expected behavior
- actual behavior

Small reproductions make fixes much faster.
