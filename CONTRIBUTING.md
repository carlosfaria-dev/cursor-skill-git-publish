# Contributing to git-publish-projects

Thanks for helping improve this **open-source Agent Skill**.

## Ways to contribute

- Improve **SKILL.md** safety rules (clearer gates, fewer tokens, no account hardcoding)
- Improve **docs** (README, setup for `git` / SSH / `gh`)
- Add **translations** → see [docs/TRANSLATING.md](docs/TRANSLATING.md)
- Fix compatibility notes for Cursor, Antigravity, Claude Code, Codex, etc.
- Report issues with reproduction steps (OS, tool, `gh`/`ssh` status — **no secrets**)

## Ground rules

1. **Do not** commit real usernames, emails, private keys, tokens, or machine-specific paths as if they were universal examples.
2. Prefer **numbered UX** in the skill (1/2/3 answers) — keep agent replies short.
3. Never suggest `git push --force`, `reset --hard`, or rewriting published history unless the issue is explicitly about documenting a user-requested danger.
4. Keep examples generic: `OWNER`, `REPO`, `seu-email@exemplo.com`.

## Development workflow

1. Fork the repo
2. Create a branch: `publish+yourname+YYYY-MM-DD+HHMM` or `docs/…` / `fix/…`
3. Edit files; keep `SKILL.md` as the source of truth for agent behavior
4. Open a Pull Request against `main` with a short “why”

## Local test checklist

- [ ] `SKILL.md` still has Gate 0 (SSH) and Gate 0.5 (`gh`)
- [ ] README language switcher links work
- [ ] No secrets in the diff
- [ ] License remains MIT unless a maintainer changes it deliberately

## Code of conduct (short)

Be respectful. Assume good intent. No harassment. Issues and PRs should stay on topic.

## License

By contributing, you agree your contributions are licensed under the [MIT License](LICENSE).
