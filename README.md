# git-publish-projects

> Skill **segura** (Cursor / Agent Skills) para publicar pastas locais em **repositórios GitHub separados**, via SSH, com branch `publish+nome+data+hora`.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-SKILL.md-0A7EA4)](SKILL.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**Idiomas:** [Português (Brasil)](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/README.md) · [English](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/README.en.md) · [Traduzir](https://github.com/carlosfaria-dev/cursor-skill-git-publish/blob/main/docs/TRANSLATING.md)

**Palavras-chave:** skill Cursor, Agent Skills, GitHub CLI, `gh`, publicar com SSH, git seguro, multi-repo, open source

Este repositório contém **somente a skill** (instruções para o agente). Não inclui sites, WordPress nem código de produção.

## O que esta skill faz

- Você aponta **um ou mais caminhos** de pastas
- O agente **verifica SSH no GitHub** antes de publicar; se falhar, **avisa e para**
- Perguntas **numeradas e curtas** (economia de tokens):
  - nome: **1 / 2 / 3** ou **4** outro
  - visibilidade: **1) public** / **2) private**
  - descrição: **1) manter** / **2) não**
  - publicar: **1) sim** / **2) não**
  - merge: **1) sim** / **2) não (manter para revisão)**
- Cada pasta vira **seu próprio** repositório
- Publish sobe **só** a branch `publish+{nome}+{YYYY-MM-DD}+{HHMM}`
- Backup local obrigatório: `{projeto}/backups/{Projeto}+{YYYY-MM-DD}+{HHMM}`
- A `main` só muda se você escolher merge **1**
- Sem force-push, sem reescrever histórico, sem secrets (`.env`, `wp-config.php`, etc.)

## O que ela NÃO faz

- Não vasculha a máquina inteira
- Não mistura vários projetos no mesmo repo
- Não altera `git config` global
- Não publica sem confirmação explícita

## Requisitos — o que baixar e configurar

Use **a sua** conta GitHub (documentação genérica).

### 1. Git

```bash
git --version
```

### 2. Chave SSH

```bash
ssh-keygen -t ed25519 -C "seu-email@exemplo.com" -f ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

Cole em GitHub → Settings → **SSH and GPG keys** → **New SSH key** (não use GPG).

```bash
ssh -T git@github.com
```

### 3. GitHub CLI (`gh`) — para criar repos automaticamente

SSH **não cria** repositório; só faz push depois que o repo existe. O `gh` cria o repo vazio.

```bash
# Fedora
sudo dnf install -y gh

# ou binário: https://github.com/cli/cli/releases → ~/.local/bin/gh
export PATH="$HOME/.local/bin:$PATH"
gh auth login --hostname github.com --git-protocol ssh --web --skip-ssh-key
gh api user --jq .login
```

O código de 1 uso aparece **no terminal**. Não cole senha nem chave **privada** no chat do agente.

**Sem `gh`:** crie o repo vazio no site (sem README); a skill só conecta o remote + push da `publish+…`.

## Instalação (Cursor)

```bash
mkdir -p ~/.cursor/skills
git clone https://github.com/carlosfaria-dev/cursor-skill-git-publish.git ~/.cursor/skills/git-publish-projects
```

Exemplo de uso no chat:

> publica com git-publish-projects: `/caminho/absoluto/do/projeto`

### Outras ferramentas

| Ferramenta | Pasta |
|------------|--------|
| Cursor | `~/.cursor/skills/` |
| Antigravity | `.agents/skills/` ou `~/.gemini/config/skills/` |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` ou `.agents/skills/` |

## Como usar

1. Passe um ou mais caminhos absolutos  
2. Responda as perguntas numeradas  
3. Confirme publicar com **1**  
4. Merge na `main`: **1** sim / **2** manter para revisão  

## Contribuições (open source)

PRs são bem-vindos: docs, traduções, segurança, compatibilidade.

→ [CONTRIBUTING.md](CONTRIBUTING.md) · [docs/TRANSLATING.md](docs/TRANSLATING.md)

## Licença

[MIT](LICENSE) — use, modifique, faça fork e contribua.

## Links

- Repositório: https://github.com/carlosfaria-dev/cursor-skill-git-publish  
- Docs Cursor Skills: https://cursor.com/docs/skills  
- English README: [README.en.md](README.en.md)
