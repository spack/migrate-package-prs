# Spack Package PR Migration Tool

A command-line tool to copy open package pull requests from `spack/spack` to `spack/spack-packages`.

## Requirements

- `git`
- `gh` CLI tool (authenticated with GitHub)
- `jq` for JSON processing
- Current directory should be a clone of `spack/spack-packages`

## Installation Instructions

1. Either use Spack to install the tool:
   ```bash
   spack install migrate-package-prs
   spack load migrate-package-prs
   ```
2. Or, if you already have the `gh` and `jq` utilities, you can simply clone the repository and add
   it to your PATH.
   ```bash
   git clone https://github.com/spack/migrate-package-prs.git
   export PATH="$PWD/migrate-package-prs/bin:$PATH"
   ```

## Usage Instructions

1. Clone the new package repository and navigate to it:
   ```bash
   git clone https://github.com/spack/spack-packages.git
   cd spack-packages
   ```

2. Do a dry-run of the migration tool from within the `spack-packages` directory:
   ```bash
   migrate-pkg-prs
   ```

3. If that looks good, run the tool with the `--migrate` option to create actual PRs:
   ```bash
   migrate-pkg-prs --migrate
   ```

## Command Syntax

For advanced usage, you can specify options and PR numbers:

```bash
migrate-pkg-prs [OPTIONS] [PR_NUMBERS...]
```

### Arguments

- `PR_NUMBERS` - (Optional) Specific PR numbers to process

### Options

- `--author USER` - Process all open PRs for the specified GitHub user
- `--migrate` - Create PRs in `spack/spack-packages` (default: local branches only)
- `-h, --help` - Show help message

### Behavior Rules

- `--author` and `PR_NUMBERS` are mutually exclusive
- If `PR_NUMBERS` are specified: process only those PRs
- If `--author` is specified: process all open PRs for that user
- **If neither: process all open PRs for the currently authenticated GitHub user** (default behavior)

## Examples

### Copy all open PRs for current GitHub user to local branches
```bash
migrate-pkg-prs
```

### Copy and create PRs in spack/spack-packages
```bash
migrate-pkg-prs --migrate
```

### Copy all open PRs for a specific user
```bash
migrate-pkg-prs --author johndoe
```

### Copy specific PRs and create PRs in target repo
```bash
migrate-pkg-prs --migrate 12345 67890
```

### Copy only a single PR to a local branch
```bash
migrate-pkg-prs 12345
```

## How It Works

1. **Fetches** the specified PR from `spack/spack`
2. **Cherry-picks** commits (excluding merges) from the PR
3. **Creates** a local branch named `spack-pr-{PR_NUMBER}`
4. **Optionally** creates a new PR in `spack/spack-packages` with the `--migrate` flag

## Branch Naming

Local branches are created with the format: `spack-pr-{PR_NUMBER}`

## PR Migration

When using `--migrate`, the tool:
- Preserves the original PR title
- Adds a note to the PR body linking to the original PR
- Creates the PR against the `develop` branch in `spack/spack-packages`

## Error Handling

The tool handles various failure scenarios:
- Failed fetches
- Cherry-pick conflicts
- Non-package PRs
- Branch creation issues
- Authentication problems

Failed PRs are skipped and reported in the summary.

## Authentication

Ensure you're authenticated with GitHub CLI:
```bash
gh auth login
```
