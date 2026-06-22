---
name: obsidian-vault-setup
description: Use when setting up Obsidian as a Claude Code knowledge base. Verifies compress/preserve/resume skills are installed, configures session log saving to the vault, and bootstraps the LLM wiki manager for ingesting session logs into a structured knowledge graph.
allowed-tools: Read, Write, Bash, AskUserQuestion, Edit
argument-hint: [leave blank to start the guided setup]
---

# /obsidian-vault-setup

Connects your Obsidian vault to Claude Code so every session is automatically logged, searchable, and ingested into a growing knowledge base. Setup takes about 15 minutes.

---

## Instructions for Claude

When the user runs `/obsidian-vault-setup`, follow these steps in order. Do not skip steps. Ask one question at a time.

---

### Step 1: Welcome

Tell the user:

> "This setup connects your Obsidian vault to Claude Code. Here is what will happen:
> 1. compress, preserve, and resume skills will be verified as installed
> 2. compress will be configured to save every session log to your vault automatically
> 3. Optionally: the LLM Wiki Manager skill will be installed to ingest session logs into a structured knowledge base
>
> Let's start."

---

### Step 2: Ask for the vault path

Use AskUserQuestion:
- **question:** "What is the full path to your Obsidian vault folder? (e.g. C:\\Users\\YourName\\Documents\\MyVault or /Users/yourname/Documents/MyVault)"
- **header:** "Vault path"
- **multiSelect:** false
- **options:**
  1. `{ label: "I'll type it in", description: "Enter the full path to your Obsidian vault" }`
  2. `{ label: "I haven't created a vault yet", description: "Open Obsidian first: download from obsidian.md, create a new vault, then re-run /obsidian-vault-setup." }`

If the user selects option 2, stop and tell them to create the vault first, then re-run `/obsidian-vault-setup`.

If the user types their path, store it as `VAULT_PATH`. Strip any trailing slash.

Verify the path exists:
```bash
ls "{VAULT_PATH}"
```

If the directory does not exist, tell the user and ask them to check the path and re-run.

---

### Step 3: Verify bundled skills are installed

Check that compress, preserve, and resume are installed at `~/.claude/skills/`:

```bash
ls ~/.claude/skills/compress/SKILL.md ~/.claude/skills/preserve/SKILL.md ~/.claude/skills/resume/SKILL.md 2>/dev/null && echo "all present" || echo "missing"
```

**If all present:** Tell the user the bundled skills are detected and continue to Step 4.

**If any are missing:** Tell the user:

> "The compress, preserve, or resume skills are not detected. These ship with the obsidian-vault-setup repo.
>
> To install them, copy the `.claude/skills/` folder from the cloned repo into your `~/.claude/skills/`:
>
> **Mac/Linux:**
> ```bash
> cp -r /path/to/obsidian-vault-setup/.claude/skills/* ~/.claude/skills/
> ```
>
> **Windows (PowerShell):**
> ```powershell
> Copy-Item -Recurse -Force "C:\path\to\obsidian-vault-setup\.claude\skills\*" "$env:USERPROFILE\.claude\skills\"
> ```
>
> Then re-run `/obsidian-vault-setup`."

Stop. Do not continue until the user confirms the skills are copied.

---

### Step 4: Configure compress to save to the vault

Read the installed compress skill to confirm its current content:
```
Read ~/.claude/skills/compress/SKILL.md
```

Use the Edit tool on `~/.claude/skills/compress/SKILL.md` to replace the Step 5 block.

The `old_string` to target is the exact heading and project-root detection block:

```
### Step 5: Detect Project Root & Save

**Generate filename:**
```
DD-MM-YYYY-HH_MM-{topic-name}.md
```
Example: `05-03-2026-17_30-api-auth-refactor.md`

**Detect project root:**

```
1. Get current working directory (pwd)
2. Find project root: walk up from pwd looking for CLAUDE.md or .git
3. If found: project_root = that directory
4. If not found: project_root = pwd
5. Session logs path: {project_root}/CC-Session-Logs/
6. Create folder if it doesn't exist: mkdir -p "{project_root}/CC-Session-Logs/"
7. Write session log there
```

**Save the session log:**
```bash
# Create folder if needed
mkdir -p "{project_root}/CC-Session-Logs/"

# Write session log
Write tool -> {project_root}/CC-Session-Logs/{filename}
```
```

Replace it with (substituting `{VAULT_PATH}` with the actual vault path the user provided):

```
### Step 5: Save to Obsidian Vault

**Generate filename:**
```
DD-MM-YYYY-HH_MM-{topic-name}.md
```
Example: `05-03-2026-17_30-api-auth-refactor.md`

**Vault path (configured by /obsidian-vault-setup):**
```
vault_root = {VAULT_PATH}
Session logs path: {VAULT_PATH}/CC-Session-Logs/
```

**Save the session log:**
```bash
# Create folder if needed
mkdir -p "{VAULT_PATH}/CC-Session-Logs/"

# Write session log
Write tool -> {VAULT_PATH}/CC-Session-Logs/{filename}
```
```

Also update the confirmation output in compress.md's Step 6 — find `**Project:** {project_root basename}` and replace it with `**Project:** {current working directory basename} → logged to Obsidian vault`.

Tell the user: "compress configured. All future `/compress` logs will save to `{VAULT_PATH}/CC-Session-Logs/`."

---

### Step 5: Install and initialise the LLM Wiki Manager

Use AskUserQuestion:
- **question:** "Would you like to install the LLM Wiki Manager skill? This gives Claude the ability to ingest your session logs into a structured, cross-referenced knowledge base that grows over time."
- **header:** "LLM wiki"
- **multiSelect:** false
- **options:**
  1. `{ label: "Yes, install it", description: "Clones the llm-wiki-manager skill and bootstraps the wiki in your vault" }`
  2. `{ label: "Skip for now", description: "You can install it later manually" }`

**If skip:** Move to Step 6.

**If yes:**

**5a: Install the skill globally**

Check if already installed:
```bash
ls ~/.claude/skills/llm-wiki-manager/SKILL.md 2>/dev/null && echo "exists" || echo "missing"
```

If missing, clone it:
```bash
git clone https://github.com/sametbrr/llm-wiki-manager.git ~/.claude/skills/llm-wiki-manager
```

Verify the scripts are present:
```bash
ls ~/.claude/skills/llm-wiki-manager/scripts/init_wiki.py
```

If the clone failed or scripts are missing, tell the user to clone it manually and skip to Step 6.

Tell the user: "llm-wiki-manager skill installed. It is now available in every Claude Code session."

**5b: Bootstrap the wiki in the vault**

Check if `CLAUDE.md` already exists in the vault:
```bash
ls "{VAULT_PATH}/CLAUDE.md" 2>/dev/null && echo "exists" || echo "missing"
```

If it already exists, tell the user the wiki appears to already be initialised and skip to Step 6.

If it does not exist, run the init script:
```bash
python ~/.claude/skills/llm-wiki-manager/scripts/init_wiki.py \
  --path "{VAULT_PATH}" \
  --name "Insights" \
  --topic "Personal knowledge base capturing insights from Claude Code sessions"
```

Then log the bootstrap:
```bash
python ~/.claude/skills/llm-wiki-manager/scripts/append_log.py \
  --path "{VAULT_PATH}" \
  --action bootstrap \
  --title "Wiki initialised" \
  --details "Topic: Claude Code session insights. Vault: {VAULT_PATH}"
```

Tell the user:
> "Wiki bootstrapped at `{VAULT_PATH}`. Created:
> - `wiki/` — Claude-managed pages (concepts, entities, sources, notes)
> - `raw/` — drop source files here for ingestion
> - `CLAUDE.md` — schema Claude reads on every session
>
> To use it: run `/compress` to save a session log, then say *'Ingest CC-Session-Logs/[filename] into the wiki'*."

---

### Step 6: Final summary

Output this confirmation block. Include the wiki row only if Step 5 was completed:

```markdown
## Setup Complete

### What was configured
- `/compress`, `/preserve`, `/resume` — available in every Claude Code session
- Session logs — saving to `{VAULT_PATH}/CC-Session-Logs/`
- LLM wiki structure — `{VAULT_PATH}/wiki/`  ← include only if wiki was set up

### Next steps
1. Open your Obsidian vault in Claude Code to give it access to vault files
2. Have a conversation, then run `/compress` — your log will appear in Obsidian
3. (If wiki was set up) Run `/compress`, then say: "Ingest CC-Session-Logs/[filename] into the wiki"

### Daily workflow
Work in Claude Code → `/compress` → `/compact` → ingest log into wiki → next session → `/resume`
```

---

## Error handling

- If any command fails, show the error and tell the user to run the failed command manually
- If the vault path does not exist, stop and ask the user to check it
- Never guess or assume a path — always ask if uncertain
- If `~/.claude/skills/` does not exist, tell the user to create it: `mkdir -p ~/.claude/skills/`
- On Windows, `~` resolves to `C:\Users\<username>` in Git Bash; PowerShell users should use `$env:USERPROFILE`
- Python may be `python3` on Mac/Linux — if `python` fails, retry with `python3`
