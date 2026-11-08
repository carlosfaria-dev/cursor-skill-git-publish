# Translating git-publish-projects

This skill is **MIT**-licensed and welcomes translations into **any language**.

## What to translate

| File | Priority | Notes |
|------|----------|--------|
| `README.md` | **Portuguese (default homepage)** | Keep PT as the GitHub landing page |
| `README.en.md` | English | SEO / international |
| `README.pt-BR.md` | Stub → points to `README.md` | Keep for old links |
| `CONTRIBUTING.md` | Optional locale copies | Or `CONTRIBUTING.<locale>.md` |
| `SKILL.md` | Prefer **English** for the agent | Optional human-only `SKILL.<locale>.md` |
| `docs/TRANSLATING.md` | This file | Translate if helpful |

## Locale codes

Use BCP 47 style filenames, for example:

- `README.es.md` — Spanish
- `README.fr.md` — French
- `README.de.md` — German
- `README.ja.md` — Japanese
- `README.zh-CN.md` — Simplified Chinese

## How to add a language

1. Copy `README.en.md` → `README.<locale>.md` (or translate from `README.md`)
2. Keep code blocks / commands accurate
3. Add a link in the **Idiomas / Languages** line of `README.md` and `README.en.md` (prefer full `blob/main/...` GitHub URLs so links work on any branch)
4. Open a PR against `main`

## Style tips

- Keep product names: GitHub, Cursor, `gh`, SSH, `SKILL.md`
- Keep branch pattern: `publish+name+YYYY-MM-DD+HHMM`
- Do not invent account-specific examples
- Preserve badge/license links

## Machine translation

MT (DeepL, Google, etc.) is OK as a **first draft**, but a human should review technical terms (`force-push`, `remote`, `merge`, `gitignore`) before merging.
