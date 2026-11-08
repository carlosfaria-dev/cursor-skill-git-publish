# git-publish-projects

> **Safe Cursor / Agent Skill** to publish one or more local project folders to **separate GitHub repositories** over SSH, using review branches named `publish+name+date+time`.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-SKILL.md-0A7EA4)](SKILL.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**Languages:** [Português (Brasil)](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/README.md) · [English](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/README.en.md) · [Help translate](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/docs/TRANSLATING.md)

**Keywords:** Cursor skill, Agent Skills, GitHub CLI, `gh`, SSH publish, safe git push, multi-repo publish, open source skill

This repository contains **only the skill** (agent instructions). No production app code.

## Why this exists

Publishing years of local work to GitHub is easy to get wrong (force-push, mixed folders, leaking `.env` / `wp-config.php`). This skill tells the agent to plan first, act only after explicit OK, create **one repo per path**, push only a `publish+…` branch, and keep a local `backups/` snapshot.

## Requirements

1. **Git**
2. **SSH** — `ssh -T git@github.com`
3. **GitHub CLI (`gh`)** recommended to *create* empty repos (SSH cannot create repositories)

```bash
sudo dnf install -y gh   # Fedora example
gh auth login --hostname github.com --git-protocol ssh --web --skip-ssh-key
gh api user --jq .login
```

Never paste GitHub passwords or **private** keys into an agent chat.

## Install (Cursor)

```bash
mkdir -p ~/.cursor/skills
git clone https://github.com/carlosfaria-dev/cursor-skill-git-publish.git ~/.cursor/skills/git-publish-projects
```

> publish with git-publish-projects: `/absolute/path/to/project`

## Contributing

PRs welcome → [CONTRIBUTING.md](CONTRIBUTING.md) · [docs/TRANSLATING.md](docs/TRANSLATING.md)

## License

[MIT](LICENSE)

## Links

- https://github.com/carlosfaria-dev/cursor-skill-git-publish
- Portuguese homepage: [README.md](README.md)
