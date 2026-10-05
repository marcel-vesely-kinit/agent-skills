# Agent skills repository design

## Goal

Make this repository a private, portable source for five existing agent skills. On another VM, the owner should be able to authenticate to the repository and install a chosen skill with the Vercel `skills` CLI.

## Requirements

- Include `fastapi-app-scaffolding`, `frontend-feedback-loops`, `python-testing-practices`, `implement-issue-group`, and `review-implemented-issue-group`.
- Use a repository layout the `skills` CLI discovers without a custom manifest.
- Preserve each skill folder's contents, including `agents/openai.yaml` files where present.
- Add a root README with private-repository setup and install instructions.
- Do not put access tokens, private keys, or other credentials in the repository or command examples.

## Layout

Place each skill under `skills/<skill-name>/`, preserving the source directory contents. This is a standard skill container path recognized by the CLI. Do not introduce a package manifest, custom installer, or generated copies beyond those skill folders.

The root README will list the included skills and show individual and all-skill install commands targeting Codex globally. The README will explain that the CLI uses credentials configured on the VM and that each VM needs its own repository access.

## Private repository authentication

Document SSH authentication as the recommended method for a private GitHub repo: configure an SSH key for the VM, add its public key to the GitHub account or organization with read access, verify SSH access, then pass the repository's SSH URL to `npx skills@latest add`.

Also mention GitHub CLI authentication as an alternative for GitHub HTTPS or shorthand sources. Describe tokens only as an authentication mechanism managed by Git or GitHub CLI. Never place a token in a URL, CLI argument, README example, or committed file.

## Acceptance criteria

- The repository contains all five skills at `skills/<name>/SKILL.md`.
- Any adjacent source files are retained with their skill.
- The CLI can discover the skills from the repository's standard `skills/` directory.
- README instructions cover VM authentication and install commands for a single skill and all five skills.
- No secret material is added.

## Out of scope

- Publishing or changing repository visibility.
- Automating credential provisioning across VMs.
- Editing the contents or behavior of the five skills.
- Adding tests or a custom installer.
