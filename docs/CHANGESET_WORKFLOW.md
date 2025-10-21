# Changeset Workflow Guide

## Overview

This project uses [Changesets](https://github.com/changesets/changesets) to manage versioning and publishing of packages in our monorepo.

## How It Works

### 1. Making Changes

When you make changes to a package that should be published, you need to create a changeset:

```bash
pnpm changeset
```

This will prompt you to:
1. Select which packages have changed
2. Choose the version bump type (major, minor, or patch) following [Semver](https://semver.org/)
3. Write a summary of the changes

### 2. Understanding Version Bumps

- **Major** (1.0.0 → 2.0.0): Breaking changes
- **Minor** (1.0.0 → 1.1.0): New features, backwards compatible
- **Patch** (1.0.0 → 1.0.1): Bug fixes, backwards compatible

### 3. Committing Changesets

Commit the generated changeset file along with your code changes:

```bash
git add .changeset/*.md
git commit -m "feat: add new feature"
git push
```

### 4. Release Process

When you push to the `main` branch:

1. **Automatic PR Creation**: The GitHub Action will create a "Version Packages" PR
2. **Review**: Review the PR to see what versions will be bumped and what will be in the changelog
3. **Merge**: When you merge this PR:
   - Package versions are updated
   - CHANGELOGs are generated
   - Packages are published to npm (if `private: false`)
   - Git tags are created

## Common Commands

```bash
# Create a new changeset
pnpm changeset

# View current changesets
ls .changeset

# Apply changesets (bump versions locally)
pnpm changeset:version

# Publish packages (usually done by CI)
pnpm changeset:publish

# Check changeset status
pnpm changeset status
```

## Package Publishing

### Currently Private Packages

All packages are currently marked as `"private": true`, which means they won't be published to npm.

### To Make a Package Publishable

1. Remove `"private": true` from the package's `package.json`
2. Ensure the package name is properly scoped (e.g., `@openzeppelin-midnight-apps/package-name`)
3. Make sure you have proper npm permissions for the scope
4. Add an `NPM_TOKEN` secret to your GitHub repository

### Setting up NPM_TOKEN

1. Generate an npm token: https://www.npmjs.com/settings/your-username/tokens
2. Add it as a GitHub secret: Settings → Secrets and variables → Actions → New repository secret
3. Name it `NPM_TOKEN`

## Ignored Packages

The following packages are ignored in the changeset workflow (see `.changeset/config.json`):
- `@openzeppelin-midnight-apps/lunarswap-ui` (frontend app, not published)

## Best Practices

1. **Create changesets as you work**: Don't wait until the end
2. **Write clear summaries**: These become your changelog entries
3. **One changeset per logical change**: Helps with clear changelogs
4. **Review the Version PR carefully**: Ensure versions are bumped correctly
5. **Keep changeset files in version control**: Never gitignore `.changeset/*.md` files

## Troubleshooting

### "No changesets present"
You need to create at least one changeset before a release can happen.

### "Package is private"
Remove `"private": true` from `package.json` if you want to publish it.

### "Authentication failed"
Ensure your `NPM_TOKEN` is set correctly in GitHub secrets.

## Example Workflow

```bash
# 1. Make your code changes
vim packages/compact/src/Builder.ts

# 2. Create a changeset
pnpm changeset
# Select: compact
# Version: minor
# Summary: "Add support for custom build options"

# 3. Commit and push
git add .
git commit -m "feat: add custom build options"
git push

# 4. Wait for CI to create Version PR
# 5. Review and merge the Version PR
# 6. Packages are automatically published!
```

## Configuration

The changeset configuration is in `.changeset/config.json`. Key settings:

- `access`: "public" - packages will be public on npm
- `baseBranch`: "main" - releases happen from main branch
- `updateInternalDependencies`: "patch" - internal deps get patch bumps
- `ignore`: Array of packages to never version/publish

## Further Reading

- [Changesets Documentation](https://github.com/changesets/changesets/blob/main/docs/intro-to-using-changesets.md)
- [Semantic Versioning](https://semver.org/)
- [Publishing to npm](https://docs.npmjs.com/packages-and-modules/contributing-packages-to-the-registry)

