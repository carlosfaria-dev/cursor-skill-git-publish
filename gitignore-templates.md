# .gitignore templates (use only when missing)

Pick the closest match; merge carefully if a `.gitignore` already exists (never wipe user rules without asking).

## Always exclude (any stack)

```gitignore
.env
.env.*
*.pem
*.key
error_log
*.log
.DS_Store
Thumbs.db
.idea/
.vscode/
```

## WordPress

```gitignore
wp-config.php
wp-config-local.php
wp-content/uploads/
wp-content/cache/
wp-content/upgrade/
wp-content/backup*/
wp-content/ai1wm-backups/
wp-content/wpvividbackups/
wp-content/wflogs/
wp-content/litespeed/
wp-content/w3tc-config/
*.wpress
*.sql
*.sql.gz
wp-cli.phar
*.zip
*.7z
```

## Node

```gitignore
node_modules/
dist/
build/
.next/
.npm/
npm-debug.log*
coverage/
```

## PHP / Composer

```gitignore
/vendor/
.phpunit.result.cache
```
