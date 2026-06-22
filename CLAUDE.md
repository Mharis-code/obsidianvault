# Obsidian Vault Setup — Agent Context

This is the standalone GitHub repo for the `obsidian-vault-setup` Claude Code skill.

## What This Repo Contains

```
.claude/skills/
├── obsidian-vault-setup/SKILL.md   ← main setup skill (source of truth is in main project)
├── compress/SKILL.md               ← bundled from CPR repo (eliaalberti/cpr-compress-preserve-resume)
├── preserve/SKILL.md               ← bundled from CPR repo
└── resume/SKILL.md                 ← bundled from CPR repo
README.md                           ← community-facing install guide
CLAUDE.md                           ← this file
sync.ps1                            ← local sync script (gitignored)
```

## Source of Truth

GitHub: `https://github.com/Mharis-code/obsidianvault`

The `obsidian-vault-setup/SKILL.md` in this repo is a SYNCED COPY. The source of truth is:
```
c:\Haris\Haris\AI Automations\Haris 2\.claude\skills\obsidian-vault-setup\SKILL.md
```

Never edit the SKILL.md directly in this repo. Edit the source, then sync:
```
powershell -File "projects/agents/obsidian-vault-setup/sync.ps1"
```

## Bundled Dependency: compress/preserve/resume

These three skills come from the CPR repo (`github.com/eliaalberti/cpr-compress-preserve-resume`). They were fetched at packaging time and bundled here so community members have no external dependencies.

If the CPR repo releases a significant update, re-fetch and re-bundle:
```bash
git clone https://github.com/eliaalberti/cpr-compress-preserve-resume.git tmp-cpr
cp tmp-cpr/commands/compress.md .claude/skills/compress/SKILL.md
cp tmp-cpr/commands/preserve.md .claude/skills/preserve/SKILL.md
cp tmp-cpr/commands/resume.md   .claude/skills/resume/SKILL.md
rm -rf tmp-cpr
```

## Remaining External Dependency: llm-wiki-manager

The optional Step 6 of the setup skill clones `github.com/sametbrr/llm-wiki-manager`. This is NOT bundled — it is complex and maintained separately. Users who want the wiki feature install it during setup.

## Push Workflow

See `.claude/rules/obsidian-vault-setup-push.md` in the main project for the full workflow.
