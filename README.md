# Personal Agent Skills

This repository stores reusable agent skills. The skills CLI can install them directly from this repository.

## Included skills

- `fastapi-app-scaffolding`
- `frontend-feedback-loops`
- `python-testing-practices`
- `implement-issue-group`
- `review-implemented-issue-group`

Each skill is in `skills/<skill-name>/` with its `SKILL.md` and any supporting files.

## Install from a VM

The repository is private, so first configure repository access on each VM. The `skills` CLI uses the Git credentials available on that machine; credentials are not passed as a `skills` command argument.

### Option 1: SSH key (recommended)

1. Create or select an SSH key for the VM.
2. Add the key's public half to a GitHub account or deploy key that has read access to this repository. Keep the private key on the VM; do not commit it here.
3. Confirm SSH authentication:

   ```bash
   ssh -T git@github.com
   ```

Then install a skill using this repository's SSH URL:

```bash
npx skills@latest add git@github.com:<OWNER>/<REPO>.git \
  --global --agent codex --skill fastapi-app-scaffolding
```

Replace `<OWNER>/<REPO>` with this repository's GitHub owner and name. To install a different skill, replace the value after `--skill` with one of the names above.

### Option 2: GitHub CLI

If GitHub CLI is installed, authenticate on the VM:

```bash
gh auth login
```

Then install using the GitHub `OWNER/REPO` shorthand:

```bash
npx skills@latest add <OWNER>/<REPO> \
  --global --agent codex --skill fastapi-app-scaffolding
```

The GitHub account used on the VM must have read access to this private repository.

## Install multiple skills

Pass `--skill` once for each skill you want. This example installs all five globally for Codex:

```bash
npx skills@latest add git@github.com:<OWNER>/<REPO>.git \
  --global --agent codex \
  --skill fastapi-app-scaffolding \
  --skill frontend-feedback-loops \
  --skill python-testing-practices \
  --skill implement-issue-group \
  --skill review-implemented-issue-group
```

Alternatively, install every skill discovered in the repository with `--skill '*'`:

```bash
npx skills@latest add git@github.com:<OWNER>/<REPO>.git \
  --global --agent codex --skill '*'
```

## Updating

After changing skills in this repository, run the install command again on each VM to refresh the installed copy. You can also use `npx skills@latest update` to update installed skills.

## Credential handling

Configure authentication through SSH or GitHub CLI on each VM. Do not put access tokens or private keys in this repository, in a repository URL, or in a command shown in shell history. For unattended environments, provide credentials through that environment's secret manager and Git authentication setup.
