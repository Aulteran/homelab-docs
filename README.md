# homelab-docs

Documentation for the Andrims homelab, written in Markdown and rendered as a site.

- **Source of truth:** this repo on Forgejo (`git.andrims.net`, internal only)
- **Backup:** private GitHub repo, via Forgejo push mirror
- **Site:** `docs.andrims.net` (internal only), built from `mkdocs.yml`

## Preview locally

```bash
# Either builder works with the same mkdocs.yml
pip install zensical && zensical serve
# or
pip install mkdocs-material && mkdocs serve
```

## Rules

1. **No secrets in this repo, ever.** Reference the Vaultwarden entry instead:
   `password: (Vaultwarden → "Immich DB")`. If a secret is committed, rotate it. Deleting the line is not enough, because it stays in git history and on the GitHub mirror.
2. **Every change to the lab gets a `docs/changelog.md` entry** in the same commit as the doc update.
3. **New services start from `docs/templates/service.md`.**
4. Items marked ❓ are unknown or unconfirmed. Replace them as you verify.
