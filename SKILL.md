---
name: git-publish-projects
description: >-
  Safely publish one or more local project folders to separate GitHub
  repositories over SSH, using branch names publish+name+date+time. Use when
  the user points to project path(s) and asks to publish, criar repositório,
  subir pro GitHub, or git-publish-projects.
---

# Git Publish Projects (safe)

Publish **each path as its own GitHub repo**. Never mix projects. Never rewrite history.

## Branch name (required)

Local (and push) branch format:

```text
publish+{folder-name}+{YYYY-MM-DD}+{HHMM}
```

Example: `publish+motoboyajatolocal+2026-07-29+0802`

- `{folder-name}` = basename of the project path (sanitize: lowercase, keep letters/numbers/`-`/`_`, replace spaces with `-`)
- Date/time = **local machine time** when the publish branch is created
- **Publish = push only this `publish+...` branch.** Never push or update `main`/`master` as part of a normal publish.

## Local backup (required on every publish)

On **every** successful publish (after commit on the publish branch, before or right after push), create a **local filesystem backup inside the project folder**. Works for any stack (PHP, Laravel, Android, Java, etc.).

### Location (inside the project — not hidden)

```text
{project-path}/backups/{ProjectName}+{YYYY-MM-DD}+{HHMM}
```

- Parent folder: `{project-path}/backups` — **visible** (never `.backups` or other dot-directories)
- `{ProjectName}` = basename of the project path (keep original casing; sanitize spaces → `-`; keep letters/numbers/`-`/`_`)
- `{YYYY-MM-DD}+{HHMM}` = **same** local date/time used in the `publish+...` branch name
- Example: `/var/www/Entregador360Web/backups/Entregador360Web+2026-07-29+0832`
- Android example: `/home/usuario/AndroidStudioProjects/MeuApp/backups/MeuApp+2026-07-29+1405`

### How to copy

1. `mkdir -p "{project-path}/backups"`
2. Copy the project tree into the snapshot folder (prefer `rsync -a` with excludes). **Never recurse into `backups/`** (exclude `backups/` so snapshots do not nest).
3. **Exclude by default:** `backups/`, `node_modules/`, `vendor/`, `.git/`, `build/`, `target/`, `.gradle/`, `.idea/`, `.cxx/`, caches, `*.log`, large backup archives
4. **Never copy secrets into backup unless user explicitly asks:** `.env`, `.env.*` (real secrets), `wp-config.php`, keystores, `*.jks`, `*.pem`, private keys
5. Ensure `backups/` is listed in the project `.gitignore` (add if missing) so snapshots are **local only** and never pushed to GitHub
6. Include the backup path in the **plan table** and in the **final report**
7. If backup fails (disk full / permission), **stop** and report — do not pretend publish is complete without telling the user

## Publish vs `main` (required)

Two separate steps:

| Step | When | What to do |
|------|------|------------|
| **Publish** | User asks to publicar / subir | Create/commit on `publish+nome+data+hora` and `git push -u origin` **that branch only** |
| **Merge → main** | User **explicitly** asks to merge / criar main / atualizar main | Then create or update `main` (prefer fast-forward when possible) and push `main` |

Rules:

- Do **not** create, checkout-push, or set GitHub default branch to `main` during publish.
- Do **not** tell the user they “must” change Default branch in GitHub Settings after publish. That is optional and unrelated to the publish step.
- After a successful publish, report the `publish+...` branch (short) and ask **Merge?** with numbers only:

```text
Merge na main?
1) sim
2) não (manter para revisão)
```

- `1` = run merge → `main` now  
- `2` = **do not** merge; leave `publish+...` for review  
- Only merge on `1` (or if user already asked merge in the same message).
- If `main` does not exist yet, create it **only** in the merge step from the agreed publish branch.
- If `main` already exists, merge the publish branch into `main` (ff-only when safe; otherwise ask before a merge commit). No force-push.
## Hard safety rules

- **Plan first, act after explicit OK.** For every path, show a short plan; wait for user confirmation before any `git init`, commit, remote change, `gh repo create`, or push.
- **One path = one repo.** Never combine folders into one repository.
- **Never** `git push --force`, `git reset --hard`, `git clean -fd`, rebase rewrite, or delete remote branches/repos unless the user explicitly requests that exact action in this turn.
- **Never** commit secrets: `wp-config.php`, `.env`, `.env.*`, credentials, private keys, `*.pem`, backup dumps, large archives meant as backups.
- Prefer excluding by default: `uploads/`, caches, `node_modules/`, `vendor/` (unless user asks to include), `*.zip`/`*.7z` backup archives, `error_log`, `*.log`.
- If the folder **already has git history**: do **not** re-init; do **not** squash/amend old commits. Create the `publish+...` branch from current HEAD (or from the branch the user names).
- If GitHub repo **already exists**: do not overwrite default branch; push only the `publish+...` branch (and set remote if missing).
- If GitHub repo **does not exist**: create an **empty** repo, then push the **publish** branch only (not `main`).
- Default remote transport: **SSH** (`git@github.com:OWNER/REPO.git`).
- Do **not** change global `git config`. Use env `GIT_AUTHOR_*` / `GIT_COMMITTER_*` only if identity is missing and user agreed on a name/email.
- Do **not** scan the whole machine. Only use paths the user provided.
- Do **not** push `main` unless the user explicitly requested merge/main in this turn.
## Gate 0 — SSH for GitHub (mandatory, before any publish)

Always check SSH access to GitHub **before** planning commits/remotes/push.

1. Ask the user (or verify) that they use **SSH** with GitHub (`git@github.com:...`), not only HTTPS password login.
2. Test read-only (do **not** list private key files or dump `~/.ssh` contents):

```bash
GIT_SSH_COMMAND='ssh -o BatchMode=yes -o StrictHostKeyChecking=accept-new' ssh -T git@github.com
```

3. **If SSH works** (e.g. `Hi <user>! You've successfully authenticated...`): note the GitHub username from that message and continue.
4. **If SSH fails / no key / new user**: **stop publishing**. Warn clearly that SSH is required for this skill, and give short setup steps:
   - Generate a key if they have none: `ssh-keygen -t ed25519 -C "email@example.com"`
   - Show the **public** key only (`~/.ssh/id_ed25519.pub` or `id_rsa.pub`) so they can copy it
   - Add the public key at https://github.com/settings/keys (Read/write as needed)
   - Re-run the SSH test above
5. Never invent tokens, never ask the user to paste a **private** key, never disable `BatchMode` to steal interactive passwords in the background.

## Gate 0.5 — GitHub CLI (`gh`) for creating repos

SSH alone **cannot create** a GitHub repository. It only authenticates **git push/pull** after the repo exists.

**`gh`** = [GitHub CLI](https://cli.github.com/). It creates empty repos (`gh repo create`), sets visibility/description, and checks if a repo exists.

### What the agent must tell the user (generic — never hardcode a real username)

If `gh` is missing or not logged in, **stop automated create** and give this short setup (copy-paste friendly). Do **not** put the user’s real account, email, or keys into the skill text or examples as if they were universal.

**1) Install `gh` (pick one):**

```bash
# Fedora / RHEL
sudo dnf install -y gh

# Debian / Ubuntu
sudo apt install -y gh

# Sem sudo — binário em ~/.local/bin
mkdir -p ~/.local/bin ~/.local/src && cd ~/.local/src
VER=2.74.1
curl -fsSL -o gh.tgz "https://github.com/cli/cli/releases/download/v${VER}/gh_${VER}_linux_amd64.tar.gz"
tar -xzf gh.tgz && install -m 755 gh_${VER}_linux_amd64/bin/gh ~/.local/bin/gh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
export PATH="$HOME/.local/bin:$PATH"
gh --version
```

**2) Login (browser — never ask for GitHub password in chat):**

```bash
export PATH="$HOME/.local/bin:$PATH"
gh auth login --hostname github.com --git-protocol ssh --web --skip-ssh-key
```

- User confirms in the browser on **their** GitHub account.
- One-time device code appears **in the terminal** (not in chat). User types that code on GitHub’s device page.
- Prefer `--skip-ssh-key` when SSH keys are already configured (Gate 0).

**3) Verify:**

```bash
gh auth status
gh api user --jq .login
```

Login must match the intended owner/org for new repos.

**4) Agent checks before `gh repo create`:**

```bash
command -v gh && gh auth status && gh api user --jq .login
```

### Rules for the agent

- Prefer `gh repo create OWNER/NAME --private|--public --description "..."` for empty repos, then push the `publish+...` branch over SSH.
- **Never** invent, request, or echo a PAT/`GH_TOKEN` in chat. If the user volunteers a token, use `gh auth login --with-token` only with their explicit OK and do not repeat the token in logs/replies.
- **Never** ask the user to paste their GitHub **password** into the agent chat.
- If the user refuses `gh`: fall back to “create empty repo in GitHub UI (no README), then agent only adds remote + push publish branch”.
- Multi-account machines (optional): document Host aliases in `~/.ssh/config` with placeholders only, e.g. `Host github.com-work` → `IdentityFile ~/.ssh/id_ed25519_work`, remote `git@github.com-work:OWNER/REPO.git`. Do not embed a specific person’s accounts in the skill.

## Communication style (mandatory — save tokens)

- Ask **one short numbered question at a time** (or one compact block per path). Wait for the number reply.
- Do **not** dump long explanations, essays, or repeated rules while collecting answers.
- Prefer replies like: `Nome?` / `Visibilidade?` / `Desc?` / `OK publicar?` — user answers with **1 / 2 / 3 / 4** only when possible.
- After answers, one **tiny** plan line (or compact table), then execute. Report results in bullets, not paragraphs.
- Step-by-step: Gate 0 (SSH) → Gate 0.5 (`gh`) → Gate 1 questions → OK → execute. No wall of text.

## Gate 1 — Pre-publish checklist (mandatory before create/push)

After inspect, **before** any `git init` that creates remotes, `gh repo create`, or `git push`, ask with **numbered choices**. Do not skip fields.

### Question format (numbers only)

Ask **in this order**, keeping messages short:

**A) Nome**

```text
/caminho/projeto — nome do repo?
1) projeto-a
2) projeto-a-oficial
3) projeto-a-site
4) outro (digite)
```

User replies `1` / `2` / `3` or types a custom name.

**B) Visibilidade** (always these numbers)

```text
Visibilidade?
1) public
2) private
```

- `1` = public  
- `2` = private  
(If user is unsure, you may hint “recomendo 2” in one short line — still wait for 1 or 2.)

**C) Descrição** (keep suggestion or not)

Propose **one** short description, then:

```text
Desc: "…"
1) manter
2) não (sem descrição)
```

- `1` = use the suggested description  
- `2` = no description (empty)  
- If they want a **custom** description, they type the text instead of 1/2.

**D) Confirmar execução**

```text
Publicar agora?
1) sim
2) não
```

Only after `1` on D (and A–C answered), execute.

**E) Merge → main** (ask **after** successful publish — short)

```text
Merge na main?
1) sim
2) não (manter para revisão)
```

- `1` = merge publish → `main` and push `main`  
- `2` = keep only `publish+...` for review (no merge)

Example of a **compact** multi-path reply the user can send:

```text
A: 1 | B: 2 | C: 1
A: 3 | B: 2 | C: 2
Publicar: 1
Merge: 2
```

When creating the GitHub repo, pass visibility from B and description from C (`gh repo create --description "..."` / `--private` or `--public`).

## Repo name (part of Gate 1 — never invent alone)

For **each** path:

1. Propose **exactly 3** name suggestions (GitHub-safe: lowercase, hyphens, no spaces).
2. Offer option **4: outro (digite)**.
3. Wait until the user picks 1, 2, 3, or types a custom name.
4. Do **not** default silently to the folder name without that choice.

## Inputs required from user

Before acting, ensure you have:

1. One or more absolute paths
2. SSH to GitHub OK (Gate 0)
3. `gh` installed + logged in (Gate 0.5) **or** user will create empty repos in the UI
4. GitHub owner/org (from `gh api user` / SSH hello when possible; confirm if ambiguous)
5. **Gate 1 answers per path:** nome (1–4) + visibilidade (**1=public / 2=private**) + desc (**1=manter / 2=não** ou texto)
6. Explicit OK (**1** on “Publicar agora?”)
7. After publish: **Merge?** (**1** sim / **2** não — manter para revisão)

## Per-path procedure

### 1) Inspect (read-only)

For each path:

- Path exists and is a directory?
- Already a git repo? (`git rev-parse`)
- Current branch, remotes, dirty state
- Does `origin` exist? What URL?
- Does GitHub repo exist? Prefer `gh repo view OWNER/REPO` if `gh` is available; else `git ls-remote` / API. Do not invent success.

### 2) Gate 1 checklist (ask user — numbered, short)

Collect: **nome** (1–4) + **visibilidade** (1 public / 2 private) + **descrição** (1 manter / 2 não). Keep prompts tiny.

### 3) Plan (show user — compact)

Prefer one line per path, not a huge essay:

```text
OK? path → OWNER/name | public|private | "desc"|sem | branch publish+... | bkp backups/... | create? sim/não
```

Or a short table if several paths. Wait for **Publicar? 1/2**.

### 4) After OK — execute per path in isolation

1. `cd` into that path only.
2. If not a git repo: `git init -b main` (or keep default), add a sensible `.gitignore` if missing (see [gitignore-templates.md](gitignore-templates.md)), stage safe files, create initial commit **only if user OK’d committing**.
3. Create branch: `git switch -c publish+{name}+{date}+{time}` (or `git checkout -b`).
4. If there are uncommitted changes the user OK’d: commit on that branch with a clear message.
5. **Local backup (mandatory):** copy project to `{project}/backups/{ProjectName}+{YYYY-MM-DD}+{HHMM}` using the same date/time as the publish branch; ensure `backups/` is gitignored (see Local backup section).
6. Remote:
   - No remote + repo missing → create empty GitHub repo with chosen **visibility** and **description** → `git remote add origin git@github.com:OWNER/REPO.git`
   - No remote + repo exists → `git remote add origin ...` (SSH URL)
   - Remote exists → keep it unless URL is wrong; ask before changing
7. Push: `git push -u origin HEAD` (**publish branch only** — never `main` here).
8. Report (bullets, short): branch `publish+...`, repo URL, SHA, **local backup path**, repo created?
9. Ask merge with numbers — do not merge until `1`:

```text
Merge na main?
1) sim
2) não (manter para revisão)
```

### 4b) Merge → `main` (only if user answers `1`)

1. `git switch -c main` if `main` missing locally, or `git switch main`.
2. Merge the publish branch (`--ff-only` when possible; otherwise ask before non-ff merge).
3. `git push -u origin main` (first time) or `git push origin main`.
4. Do not change GitHub “Default branch” settings unless the user explicitly asks.

If `gh` is missing/unauthenticated and a new GitHub repo must be created, follow **Gate 0.5**: give the install + `gh auth login --web --skip-ssh-key` copy-paste, wait until `gh api user` works, **or** user creates the empty repo in the UI (agreed name/visibility/description), then you only add remote + push **the publish branch**. Do not invent API tokens. Do not embed any real user’s account name in skill docs or canned prompts.
### 5) Final report

One section per path: success/fail, branch `publish+...`, repo URL, visibility, description used, **local backup path** (`{project}/backups/...`), what was excluded, next step if blocked on auth.

## Anti-patterns

- Publishing without listing the plan
- Publishing without a local backup under `{project}/backups/{Project}+{date}+{time}`
- Putting backups outside the project (e.g. `~/backups/...`) — must be **inside** the project folder
- Using a **hidden** backup folder (e.g. `.backups`) — must be visible `backups/`
- Committing or pushing the `backups/` folder to GitHub
- Embedding a real user’s GitHub username, email, or SSH key material into skill docs or canned prompts (skill is public — keep examples generic)
- Skipping Gate 0.5 when automated `gh repo create` is required and `gh` is missing/unauthenticated
- Asking the user to paste a GitHub **password** or **private** key into chat
- Publishing without verifying SSH (or continuing after SSH failure)
- Skipping Gate 1 (name / visibility / description) before create or push
- Choosing a repo name alone without offering 3 suggestions + custom option
- Asking visibility as free text instead of **1) public  2) private**
- Asking description as a long form instead of **1) manter  2) não** (+ optional typed custom)
- Writing long token-heavy explanations while collecting Gate 1 answers
- Creating a repo without asking private vs public via 1/2
- Creating a repo without asking/confirming the description via 1/2
- Using one repo for multiple paths
- Pushing to `main` by default / during publish
- Creating or updating `main` without explicit **Merge? → 1**
- Skipping the post-publish **Merge? 1/2** question
- Telling the user they must set Default branch to `main` after publish
- Force-push to “fix” history
- Committing WordPress `wp-config.php` or `.env`
- Renaming/moving user folders
- Batch-scanning `/var/www` unsolicited
- Asking for or reading the user's **private** SSH key
