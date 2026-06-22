# Obsidian Vault Setup

Connect your Obsidian vault to Claude Code in 15 minutes. Every session gets automatically logged to your vault, and an optional LLM-managed wiki builds a structured knowledge base from those logs over time.

**Built by Haris — AI Systems for Solopreneurs**

---

## What You Get

- `/compress` — saves a full session log to `YourVault/CC-Session-Logs/` at the end of every conversation
- `/preserve` — updates your project's `CLAUDE.md` with key learnings from the session
- `/resume` — loads your vault context + recent session logs at the start of a new session
- LLM Wiki Manager (optional) — ingests session logs into a structured, cross-referenced knowledge graph inside Obsidian

---

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- [Obsidian](https://obsidian.md) installed with a vault created
- Python 3.9+ (required only if installing the LLM Wiki Manager in Step 6)
- Git

---

## Install

### 1. Clone this repo

```bash
git clone https://github.com/Mharis-code/obsidianvault.git
```

### 2. Copy the skills into Claude Code

**Mac / Linux:**
```bash
cp -r obsidian-vault-setup/.claude/skills/* ~/.claude/skills/
```

**Windows (PowerShell):**
```powershell
Copy-Item -Recurse -Force "obsidian-vault-setup\.claude\skills\*" "$env:USERPROFILE\.claude\skills\"
```

This installs four skills: `obsidian-vault-setup`, `compress`, `preserve`, and `resume`.

### 3. Run the setup skill

Open Claude Code in any project and run:

```
/obsidian-vault-setup
```

The skill will guide you through the rest interactively — vault path and optional wiki bootstrap.

---

## Daily Workflow

```
Work in Claude Code
  → /compress          (saves session log to vault, then compact the conversation)
  → /compact           (compresses the context window)
  
Next session:
  → /resume            (loads vault context + recent session summaries)
  → /preserve          (optional: updates CLAUDE.md with key decisions)
  
Grow the wiki:
  → "Ingest CC-Session-Logs/[filename] into the wiki"
```

---

## Skills Reference

| Skill | Command | What it does |
|---|---|---|
| obsidian-vault-setup | `/obsidian-vault-setup` | One-time setup: verifies installs, configures vault path, optional wiki bootstrap |
| compress | `/compress` | Saves full session log to `CC-Session-Logs/` in your vault |
| preserve | `/preserve` | Updates project `CLAUDE.md` with session learnings |
| resume | `/resume [N] [keyword]` | Loads CLAUDE.md + last N session summaries, optional topic search |

---

## LLM Wiki Manager

The optional wiki step installs the [llm-wiki-manager](https://github.com/sametbrr/llm-wiki-manager) skill, which gives Claude the ability to ingest session logs into a structured knowledge base with entities, concepts, and source summaries — all cross-referenced and searchable inside Obsidian.

After setup, ingest a log by telling Claude:
```
Ingest CC-Session-Logs/22-06-2026-14_30-your-topic.md into the wiki
```

---

## Updating

To get the latest version of the skills:

```bash
cd obsidian-vault-setup
git pull
cp -r .claude/skills/* ~/.claude/skills/
```

---

## Community

Part of **AI Systems for Solopreneurs** — [join the Skool community](https://www.skool.com) to get templates, course content, and live help building AI systems for your business.
