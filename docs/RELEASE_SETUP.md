# Release & Publishing Setup

## ✅ What's Been Configured

### 1. Changesets Installed & Initialized
- Package: `@changesets/cli@^2.29.7`
- Configuration: `.changeset/config.json`
- Documentation: `docs/CHANGESET_WORKFLOW.md`

### 2. Package Scripts Added
```json
{
  "changeset": "changeset",
  "changeset:version": "changeset version",
  "changeset:publish": "changeset publish"
}
```

### 3. GitHub Actions Workflow
- File: `.github/workflows/release.yml`
- Triggers on pushes to `main` branch
- Automatically creates "Version Packages" PRs
- Publishes packages when PR is merged

### 4. Configuration Details
```json
{
  "access": "public",           // Packages will be public on npm
  "baseBranch": "main",         // Releases from main branch
  "ignore": ["@openzeppelin-midnight-apps/lunarswap-ui"]  // UI app won't be versioned
}
```

## 🚀 Next Steps

### Before Publishing (One-Time Setup)

#### 1. **Set Up NPM Token** (Required for publishing)
```bash
# Generate a token at https://www.npmjs.com/settings/YOUR_USERNAME/tokens
# Choose "Automation" token type
# Then add it to GitHub:
# Settings → Secrets and variables → Actions → New repository secret
# Name: NPM_TOKEN
# Value: <your-token>
```

#### 2. **Configure Package Scope** (If publishing scoped packages)
Make sure you have npm permissions for `@openzeppelin-midnight-apps` scope, or update package names.

#### 3. **Mark Packages as Public** (When ready to publish)
Currently ALL packages are `"private": true`. To publish a package:

```json
{
  "name": "@openzeppelin-midnight-apps/compact",
  "version": "1.2.0",
  "private": false,  // ← Change this to false or remove it
  ...
}
```

Do this for each package you want to publish.

## 📝 Daily Workflow

### When Making Changes

1. **Make your code changes**
   ```bash
   # Edit files as normal
   vim packages/compact/src/Builder.ts
   ```

2. **Create a changeset**
   ```bash
   pnpm changeset
   ```
   
   You'll be prompted:
   ```
   🦋  Which packages would you like to include?
   ◯ @openzeppelin-midnight-apps/compact
   ◯ @openzeppelin-midnight-apps/compact-std
   ◯ @openzeppelin-midnight-apps/math
   ...
   
   🦋  What kind of change is this for compact? (Select one)
   ❯ patch   (Bug fixes)
     minor   (New features, backward compatible)
     major   (Breaking changes)
   
   🦋  Please enter a summary for this change:
   Add support for custom build options
   ```

3. **Commit everything together**
   ```bash
   git add .
   git commit -m "feat: add custom build options"
   git push origin feat/your-branch
   ```

4. **Create and merge your PR**
   - Your feature PR is reviewed and merged
   - The changeset file goes with it

### When Ready to Release

1. **Merge to main**
   - Once your feature PR is merged to `main`
   - GitHub Actions will automatically run

2. **Review "Version Packages" PR**
   - A bot will create a PR titled "Version Packages"
   - This PR shows:
     - Which packages will be bumped
     - New version numbers
     - Generated CHANGELOG entries
   - Review it carefully!

3. **Merge the Version PR**
   - When you merge it:
     - ✅ Versions updated in `package.json` files
     - ✅ CHANGELOGs generated/updated
     - ✅ Git tags created
     - ✅ Packages published to npm (if not private)

## 🔍 Useful Commands

```bash
# Check what changes are pending
pnpm changeset status

# See which packages will be released
pnpm changeset status --verbose

# List all changesets
ls .changeset/*.md

# Test version bumping locally (doesn't publish)
pnpm changeset:version

# Preview what would be published (doesn't actually publish)
pnpm changeset:publish --dry-run
```

## 📚 Package Status

Currently **ALL** packages are marked as private:

- ✓ `@openzeppelin-midnight-apps/access` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/compact` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/compact-std` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/math` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/shielded-token` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/structs` - `"private": true`
- ✓ `@openzeppelin-midnight-apps/lunarswap-ui` - `"private": true` (ignored in changesets)

**Action Required:** Remove `"private": true` from packages you want to publish!

## 🛡️ Safety Features

1. **Private packages won't publish** - Even if changesets tries, npm will reject
2. **Preview in PRs** - You see version bumps before they happen
3. **Conventional commits** - Using `feat:`, `fix:`, `chore:` helps track changes
4. **Workspace dependencies** - Changesets handles internal dependencies automatically

## 📖 Further Reading

- **Workflow Guide:** `docs/CHANGESET_WORKFLOW.md`
- **Changesets Docs:** https://github.com/changesets/changesets
- **Semantic Versioning:** https://semver.org/

## ⚠️ Important Notes

1. **First release:** The first time you merge a "Version Packages" PR, all changed packages will be released
2. **Multiple changesets:** You can have many changesets before releasing - they'll all be combined
3. **Granular changesets:** Create one changeset per logical change for better changelogs
4. **CI/CD:** The GitHub Action needs `NPM_TOKEN` secret set to publish

## 🎯 Quick Test

Try creating a test changeset right now:

```bash
# Create a test changeset
pnpm changeset

# Select a package (like compact)
# Choose "patch"
# Write: "Test changeset setup"

# Check status
pnpm changeset status

# You should see the pending change!
```

Then you can delete the changeset file:
```bash
rm .changeset/<generated-file>.md
```

---

**Setup Complete!** You're ready to start managing versions and releases with changesets. 🎉

