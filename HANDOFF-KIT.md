# The Handoff Kit
*Version 2.0 · 2026-10-06 · Shared context for any project worked on by any number of AI tools, one at a time or in parallel.*

**What this is.** One file you give to any AI: Claude, ChatGPT, Codex, Gemini, Cursor, Copilot, Perplexity, DeepSeek, Kimi, or anything else that can read text. It sets up your project so every chat, in every tool, starts from the same up-to-date context, records its progress as it goes, and hands off cleanly to the next. Any number of AIs, in any mix, can work on the project at the same time. The memory lives in your project's files and its GitHub history, not inside any one chat or vendor.

---

## Quickstart for humans (60 seconds)

1. **New project:** make an empty folder. Use a normal folder, not one synced by OneDrive, Dropbox or iCloud. Open it in an AI coding tool (Claude Code, Codex, Cursor, Gemini CLI…), attach this file, and say:
   > Set up this project with the Handoff Kit.
2. **From the template:** on the Handoff Kit's GitHub page, click **Use this template** and keep the new repo **private**. Copy it to a normal folder on your computer with `gh repo clone <you>/<repo> <folder>` (or ask your AI tool to do it). Open that folder in an AI coding tool and say:
   > Set up this project with the Handoff Kit.
3. **Existing project:** same as 1, but say:
   > Install the Handoff Kit into this existing project. Don't change any existing files except to add pointers.
4. **Every chat after that:** paste **START** first and **END** last. In long sessions, paste **CHECKPOINT** after each big step. Setup saves all the prompts in your project as `docs/PROMPTS.md`; they're also in [The prompts](#the-prompts) below.
5. **Chat AIs** (ChatGPT, Claude.ai, Gemini, Perplexity, DeepSeek, Kimi, Grok…): in any coding tool, say **PACK for <the AI>: <what it'll work on>**. Paste the block it gives you into the chat; that's the chat's whole start. When you're done, paste the chat's **HANDOFF** block back into a coding tool with **RECORD HANDOFF**.

That's it. Run as many chats at once as you like, in any mix of tools; the protocol below keeps them from overwriting each other.

---

## Instructions for the AI

If you are an AI reading this file, follow this section exactly. Don't invent a different system, and don't skip steps.

### A. Interview (one message, then wait)
**Detect first, ask second.** Before asking anything, find out what you can on your own:
- the timezone, and the folder's location and contents (including untracked files);
- the git identity, whether `gh` is installed and logged in, and as which account (`gh auth status`);
- the account's private noreply email: it's `<id>+<username>@users.noreply.github.com`, where `gh api user --jq .id` gives the id;
- whether the repo name is free (`gh repo view <account>/<name>` should fail);
- whether this is a **template copy**: `HANDOFF-KIT.md` at the root, an `origin` remote already set, and no `AGENTS.md`.

Put what you found in brackets as the suggested answer, ask only what you couldn't find, and offer "reply **defaults** to accept the suggestions in brackets":
1. Project name, one sentence on what it is, and what "done" looks like.
2. Owner name and timezone [detected].
3. What the project contains: code (which languages), docs, design or media assets [suggest from the folder].
4. Folder location. If it's inside OneDrive, Dropbox, iCloud or Google Drive, recommend moving it to a non-synced folder (e.g. `~/dev/<name>` or `C:\Users\<you>\dev\<name>`) and say why in one line: sync tools lock files git is writing and upload everything, ignored files included.
5. Which AIs will work on it, both coding tools and chat AIs [Claude Code, Codex, ChatGPT…]. Only coding tools that need a pointer or setting get one (see [Tool notes](#tool-notes)); chat AIs need nothing in the repo.
6. GitHub account and repo name [detected account; a private repo named after the folder]. If `gh` is missing or logged out, say which step of [Git and GitHub setup](#git-and-github-setup) the owner needs. For a template copy, the repo already exists: just confirm it's private.
7. What must never be committed: secrets, personal data, client files, large media [.env files, credentials, exports with personal data]. Also: is anything already in this folder private, like notes or exports? [List the files you found.] Those go in `.gitignore` before the first commit. If they hold project facts (plans, open questions), copy those into `docs/STATE.md` first.
8. Things AIs must never do without asking [publish, send messages, spend money, delete data].

### B. Create the files
Create the files in [Files to create](#files-to-create), filled in from the answers. Create pointer files only for the tools named in question 5, and skip the optional Claude Code stop check unless the owner asks for it. Mark genuine unknowns as `UNKNOWN — needs <owner>`; never leave a fillable placeholder. For an **existing project**:
- Don't rewrite existing docs. If there's already a decisions log or status file, move it to `docs/decisions/0000-legacy-<original-name>.md` and `docs/sessions/0000-legacy-<original-name>.md`, unedited, and summarize it into the new `docs/STATE.md`.
- If `AGENTS.md`, `CLAUDE.md` or similar already exist, merge the protocol section in and keep everything else.
- If `.gitignore` or `.gitattributes` already exist, add the kit's lines and keep the rest.

For a **template copy**:
- `README.md` is the kit's front page. Replace it with a short README for this project.
- Move `HANDOFF-KIT.md` to `docs/HANDOFF-KIT.md` and keep it there for reference.
- If the template brought a `LICENSE` file, it's the kit's license, not the project's. Delete it unless the owner says otherwise. The kit's own license notice stays in `docs/HANDOFF-KIT.md`.

### C. Git and GitHub
Run [Git and GitHub setup](#git-and-github-setup). For a template copy, skip `git init` and the repo-creation step, because the repo and its `origin` already exist. Do everything else. The owner runs the login step themselves; give them the exact command. **Never type, request or store their password or token.**

### D. First session record
Create `docs/sessions/<YYYY-MM-DD-HHMM>-<tool>-setup.md` for this setup session, already closed with a handoff. Setup has no START, because the repo didn't exist yet. Also create a decision file for "adopted the Handoff Kit v2". Commit, push, and confirm `git status` is clean and in sync with origin. Any further work, even in the same chat, is a new session that begins with START.

### E. Hand over
Tell the owner the prompts are saved in `docs/PROMPTS.md`. Then summarize in under 10 lines: what was created, the GitHub URL, and anything they still need to do.

---

## How it works

### The principles
1. **The repo is the memory.** Nothing important lives only in a chat. If it isn't committed, it didn't happen.
2. **Every chat is a session with its own file.** The file is created at START, so other chats can see it's running. It's appended at every CHECKPOINT and closed at END with a handoff.
3. **Separate files mean no collisions.** Each session writes its own session file and its own decision files. The only shared file is `docs/STATE.md`, and it's edited only at END, after syncing.
4. **Parallel sessions never share a working folder.** Two AIs editing the same files on disk at once corrupt each other's work. Each concurrent session gets its own copy: a git worktree, a separate clone, or the tool's own cloud sandbox. All of them meet on GitHub. There's no limit on how many sessions run at once.
5. **Push early and often.** START, CHECKPOINT and END all commit and push. A crashed or abandoned chat loses at most one step.
6. **Chat AIs propose; coding tools commit.** A chat AI can't write to the repo, so a coding tool works for it at both ends: it claims the chat's session and hands it the context (PACK), and later saves what the chat produced (RECORD HANDOFF).

### Two kinds of AI
- **Coding tools** can edit files in the project folder and run git: Claude Code, Codex, Cursor, Gemini CLI, Copilot, Kimi Code CLI, Aider, opencode and the like. They follow START, CHECKPOINT and END themselves.
- **Chat AIs** are everything else: ChatGPT, Claude.ai, Gemini, Perplexity, DeepSeek, Kimi, Grok, Le Chat and any other chat app. They take part through [Chat AIs](#chat-ais).

What matters is the tool, not the model. DeepSeek or Kimi in a browser is a chat AI; the same model inside a coding tool is a coding tool.

### Project layout
```
AGENTS.md                 ← the rules; every AI reads this first
CLAUDE.md, GEMINI.md, …   ← one-line pointers to AGENTS.md, only for tools that need one (see Tool notes)
docs/
  STATE.md                ← current truth: status, next 3 tasks, blockers, do-not-touch (≤ 1 page, rewritten at END)
  PROMPTS.md              ← the prompts to paste: START, CHECKPOINT, END, …
  decisions/              ← one file per decision, never edited after the fact
    2026-10-06-use-postgres.md
  sessions/               ← one file per chat: claim, progress log, handoff
    2026-10-06-1430-claude-code-auth-flow.md
.githooks/pre-commit      ← blocks work commits that don't update the session log or STATE
.gitattributes            ← keeps the hook's line endings working on every OS
```

### Session IDs
Use `YYYY-MM-DD-HHMM-<tool>-<slug>`, in the owner's timezone, for example `2026-10-06-1430-codex-pricing-page`. `<tool>` is a short lowercase name, the same every time: `claude-code`, `codex`, `cursor`, `gemini-cli`, `copilot`, `kimi-cli`, `chatgpt`, `claude-ai`, `gemini`, `perplexity`, `deepseek`, `kimi`, `grok`, and so on. The model goes in the session file's `tool:` field. The ID names the session file, and in parallel mode it also names the branch (`s/<id>`) and the worktree folder (`../<folder>-<id>`, where `<folder>` is the name of the project's folder on disk, which may differ from the repo's name).

### The session lifecycle

**START**
1. **Sync:** `git config core.hooksPath .githooks` (it's safe to repeat; fresh clones and cloud sandboxes don't have it), then `git fetch origin`, then `git status`.
   - If the tree has uncommitted changes that aren't yours, **stop** and report them: another session didn't finish.
   - If you're behind, run `git pull --ff-only`.
2. **Read:** `AGENTS.md`, `docs/STATE.md`, the 10 newest files in `docs/decisions/`, and every session file with `status: active`.
3. **Check for overlap:** if an active session's `scope` overlaps what you're about to do, tell the owner and wait. If an active session hasn't logged anything for over 24 hours, ask the owner whether to mark it `abandoned`.
4. **Choose a mode:**
   - **Solo:** no other active sessions, and the owner hasn't mentioned another chat open in this folder. Work on `main` in this folder.
   - **Parallel:** at least one other active session, or you're unsure. Never touch the main folder; it may be in use. Create your own worktree on branch `s/<id>` and do all your work there:
     ```bash
     git worktree add ../<folder>-<id> -b s/<id> origin/main
     ```
     If your tool already gave you an isolated copy (a cloud sandbox or an app-made worktree), use that instead.
5. **Claim:** in your working copy, create `docs/sessions/<id>.md` (see the template) with `status: active`, your goal and your scope. Commit it and push it **to main on GitHub** straight away, so every other session can see the claim:
   ```bash
   git add docs/sessions/<id>.md
   git commit -m "session: start <id>"
   git push origin HEAD:main
   ```
   If the push is rejected, someone pushed first: run `git fetch origin`, then `git rebase origin/main`, re-check the active sessions for overlap, and push again.
6. **Report** to the owner in under 10 lines: what the project is, what the next task is, what you'll do first, which mode you're in, and your claim's commit hash (proof the claim reached GitHub). If the owner already gave you the task along with START, carry on with it; otherwise wait for their go.

**CHECKPOINT** (after each meaningful step, before anything risky, and at least every hour)
1. Append a line to your session file's Log: the time and what was done.
2. Add a decision file for each decision made since the last checkpoint.
3. Commit and push your branch: `git push origin HEAD` (solo pushes `main`; parallel pushes `s/<id>`).

**END**
1. **Close your session file:** set `status: closed`, fill in `ended`, and write the Handoff (done / not done / next steps / warnings).
2. **Decisions:** make sure every decision has its own file.
3. **Sync with everyone else:**
   ```bash
   git fetch origin
   git rebase origin/main
   ```
   Resolve any conflicts with the [conflict rules](#conflict-rules).
4. **Rewrite `docs/STATE.md`** from the current truth: what's now on `origin/main` plus your work. Keep it to one page. Commit it.
5. **Publish:**
   ```bash
   git push origin HEAD:main
   ```
   If the push is rejected, someone else just pushed. Repeat step 3, then push again.
6. **Clean up (parallel mode):** from the main folder, run `git worktree remove ../<folder>-<id>`, then `git branch -d s/<id>`, then `git push origin --delete s/<id>` if you pushed the branch.
7. **Verify:** `git status` is clean, `git log origin/main -1` shows your commit, and you've pasted the hash to the owner.

### Conflict rules
- **Session and decision files** never conflict, because each one belongs to a single session. If one somehow does, keep both versions.
- **`docs/STATE.md`:** start from the version on `origin/main` (the other session's truth), then apply only what your session changed. Never drop another session's tasks or blockers unless your work resolved them; if it did, say so in your handoff.
- **Code and content:** never discard someone else's change to make yours apply. If both changed the same lines and the right merge isn't obvious, stop and ask the owner.
- Never use `--force` on `main`. Never use `--no-verify`.

### Chat AIs
A chat AI can't see or change the repo, so a coding tool does both for it. Any coding tool works, and the chat app needs no access to anything.

**PACK** (in a coding tool, when the owner says "PACK for <chat AI>: <what it'll work on>")
1. Sync and read, as in START steps 1–2. Check active sessions for overlap with the chat's scope; if there's any, tell the owner and wait.
2. Claim a session **for the chat**: `docs/sessions/<id>.md`, with the chat's tool name in the ID, `mode: chat`, `status: active`, and its goal and scope. Commit it and push it to main, as in START step 5. Every other session can now see what the chat is working on.
3. Give the owner **one** code block to paste into the chat, fenced with five backticks so the files inside don't break it. In order, it holds:
   - the CHAT INSTRUCTIONS from `docs/PROMPTS.md`, with the session ID, the current date and time, and the scope filled in;
   - the full text of `AGENTS.md`, `docs/STATE.md`, the 10 newest decision files, every active session file, and any project files the work needs, each headed by its path.

   If the chat app rejects a paste that long, save the same content as a file **outside** the repo and tell the owner to attach it instead.

**In the chat:** the chat AI works with the owner. Whenever something is worth keeping, and always at the end, it outputs a **HANDOFF** block, with each file in its own fenced block headed by its path:
- its session file, closed, with the Log and Handoff filled in;
- one decision file per decision;
- the complete new text of every project file it created or changed.

**RECORD HANDOFF** (in a coding tool, with the HANDOFF block pasted after the prompt)
1. Save each file at its stated path, exactly as given. Fix only path or format problems (a wrong ID or date, a missing header), and list what you fixed. The session file replaces the chat's active claim.
2. Don't save a project file that looks cut short or contains things like "rest unchanged" or "…". Tell the owner, who can ask the chat for the complete file.
3. If a project file changed on main after the chat's session started, merge by the [conflict rules](#conflict-rules) instead of overwriting.
4. Then run END steps 3–7: sync, update STATE, push, verify.

PACK and RECORD HANDOFF push to main, so run them from a clean checkout of `origin/main`: a coding chat that's between tasks, or a throwaway worktree (`git worktree add --detach ../<folder>-tmp origin/main`, removed afterwards). Never run them on a branch that holds your own unfinished work.

**GitHub connectors:** some chat apps can read GitHub directly. Use one only if it can be limited to this one repo, read-only. Never give a chat app a token or key that reaches further. You still run PACK, for the claim and the session ID.

---

## Files to create

### `AGENTS.md`
```markdown
# Project
<Name> — <one sentence>. Done means <definition of done>.

# Stack & conventions
- <languages / file types / folder layout>
- <how to build, run, test — or "no automated tests">
- <naming, brand, spelling or style rules>
- Unknowns: <each marked UNKNOWN — needs <owner>>

# Never (without the owner's explicit approval in chat)
- <publish / send messages / spend money / delete data / …>
- Commit secrets, .env files, credentials or personal data.
- Type, request or store the owner's passwords or tokens.

# Handoff protocol (Handoff Kit v2 — mandatory for every AI, every session)
Memory lives in this repo. If it isn't committed and pushed, it didn't happen.
- Read first: this file, docs/STATE.md, the 10 newest files in docs/decisions/, every docs/sessions/*.md with `status: active`. The prompts are in docs/PROMPTS.md.
- START: run `git config core.hooksPath .githooks`; fetch and check status; stop on uncommitted changes that aren't yours; read the files above; check active sessions for overlapping scope; pick a mode (solo → work on main here; another session active or unsure → own worktree on branch s/<id>, never this folder); claim with a new docs/sessions/<YYYY-MM-DD-HHMM-tool-slug>.md committed and pushed with `git push origin HEAD:main`; report in under 10 lines, including the claim's commit hash; if the owner gave the task with START, carry on, otherwise wait for their go.
- CHECKPOINT (after each meaningful step, at least hourly): append to your session log, add a docs/decisions/<date>-<slug>.md for each decision, commit, push.
- END: close the session file with a handoff; fetch; rebase on origin/main; rewrite docs/STATE.md (max one page, from origin/main's version plus your changes); push HEAD:main (on rejection, re-fetch, rebase, retry); remove your worktree and branch; confirm clean and in sync; give the commit hash.
- Chat AIs (no repo access) go through a coding tool, working from a clean checkout of origin/main. PACK: sync; check overlap; claim docs/sessions/<id>.md for the chat (mode: chat) and push it; give the owner ONE paste block (fenced with five backticks) holding the CHAT INSTRUCTIONS from docs/PROMPTS.md (session ID, current date and time, and scope filled in) plus the full text of this file, docs/STATE.md, the 10 newest decisions, active sessions and any files the work needs. RECORD HANDOFF: save the chat's files as given, fixing format only; refuse project files that look cut short ("rest unchanged", "…"); merge by the conflict rules if a file changed on main since the chat started; then END steps.
- Never edit another session's file. Never rewrite a decision file; supersede it with a new one. Never force-push main. Never use --no-verify.
- Two AIs never edit the same working folder at the same time.
- Sandboxed tools (e.g. Codex) that block writes to .git: request escalated permissions for git commands. Never change .git permissions.
- Absolute dates only (2026-10-06, never "yesterday").

# Owner
<Name> · timezone <tz> · GitHub <owner/repo>
```

### Pointer and setting files
Most coding tools read `AGENTS.md` directly. Create a pointer or setting only for the tools named in question 5 that need one, as listed in [Tool notes](#tool-notes). Chat AIs need nothing in the repo.

### `docs/STATE.md`
```markdown
# STATE — <Project>
Updated: <YYYY-MM-DD HH:MM tz> by <session id>

## Status
- <what exists and works, with paths>
- <what's in progress, and whose move it is>

## Next 3 tasks
1. <concrete; name the file or command to start from>
2. …
3. …

## Blockers / open questions
- <what's stuck; who unblocks it>

## Do not touch
- <files, folders, actions that are off-limits without approval>
```

### `docs/sessions/<id>.md`
```markdown
---
id: <YYYY-MM-DD-HHMM-tool-slug>
tool: <tool and model, if known>
owner: <name>
status: active            # active | closed | abandoned
started: <YYYY-MM-DD HH:MM tz>
ended:
mode: solo                # solo (main) | parallel (branch s/<id>) | chat (a chat AI; claimed by PACK)
scope: <areas / files this session will touch>
---
# Goal
<one or two sentences>

# Log
<!-- append-only; newest at the bottom -->
- <HH:MM> claimed session

# Decisions
- <links to docs/decisions/ files made in this session>

# Notes
<!-- optional: findings, research, lists worth keeping that aren't decisions -->

# Handoff
<!-- filled at END -->
- Done:
- Not done:
- Next steps:
- Warnings:
```

### `docs/decisions/<YYYY-MM-DD>-<slug>.md`
```markdown
# <Short title>
Date: <YYYY-MM-DD> · Session: <session id> · Status: active   <!-- active | superseded by <file> -->

**Decision:** <what, specifically>
**Why:** <the reason>
**Alternatives rejected:** <what else, and why not>
```

### `docs/PROMPTS.md`
Copy every prompt in [The prompts](#the-prompts) exactly, each under its heading, with `<project>` filled in. Leave the session ID, date and time, and scope placeholders in CHAT INSTRUCTIONS; PACK fills those in each time. Put this line at the top: `Paste START first and END last in every coding-tool chat. For a chat AI, say PACK in a coding tool, paste its block into the chat, and bring the chat's HANDOFF back with RECORD HANDOFF.`

### `.gitattributes`
```gitattributes
# Store text with LF; hooks and shell scripts stay LF on every OS so bash can run them
* text=auto
*.sh text eol=lf
.githooks/* text eol=lf
```

### `.gitignore` (starter; adjust to the project)
```gitignore
# Secrets
.env
.env.*
!.env.example
*.local.json
*.pem
*.key

# Dependencies, builds, caches, OS cruft
node_modules/
dist/
build/
.venv/
__pycache__/
.DS_Store
Thumbs.db

# Personal data, exports, large regenerable media (edit to fit)
archive/
*.mov
```

### `.githooks/pre-commit`
Blocks a commit that changes real work without logging it in a session file or STATE:
```bash
#!/usr/bin/env bash
staged=$(git diff --cached --name-only)
# Paths that don't count as work. Edit to fit the project, e.g. drop README if the README is your product.
work=$(echo "$staged" | grep -vE '^(docs/|AGENTS\.md|CLAUDE\.md|GEMINI\.md|README|\.githooks/|\.claude/|\.github/|\.gitignore$)')
if [ -n "$work" ] && ! echo "$staged" | grep -qE '^docs/(STATE\.md|sessions/)'; then
  echo "BLOCKED: work changed but neither your docs/sessions/<id>.md log nor docs/STATE.md is in this commit. Log the step, git add it, commit again." >&2
  exit 1
fi
exit 0
```

### Optional: Claude Code stop check (`.claude/settings.json` + `.claude/hooks/require-clean.sh`)
This stops Claude Code from ending a turn with uncommitted work.
```json
{ "hooks": { "Stop": [ { "hooks": [ { "type": "command", "command": "bash \"$CLAUDE_PROJECT_DIR\"/.claude/hooks/require-clean.sh" } ] } ] } }
```
```bash
#!/usr/bin/env bash
input=$(cat)
echo "$input" | grep -q '"stop_hook_active": *true' && exit 0
if [ -n "$(git status --porcelain --untracked-files=no)" ]; then
  echo "Handoff Kit: uncommitted changes remain. Log them in your session file, commit and push (CHECKPOINT), or run END." >&2
  exit 2
fi
exit 0
```
Only enable it for owners who always end with END; it fires on every turn that leaves the tree dirty.

---

## Git and GitHub setup

Run these from the project folder.

**1. Initialize git and turn on the hook** (template copy: skip `git init`)
```bash
git init -b main
git config core.hooksPath .githooks
```
On macOS or Linux, also run `chmod +x .githooks/pre-commit`.

Skip steps 2 and 3 if `gh auth status` already shows the owner logged in.

**2. Install the GitHub CLI (once per machine)**
- Windows: `winget install --id GitHub.cli -e`
- macOS: `brew install gh`
- Linux: see https://cli.github.com

**3. Log in (the owner does this; once per machine)**
```bash
gh auth login --hostname github.com --git-protocol https --web
```
In Windows PowerShell, if `gh` isn't on PATH yet, put `&` before the quoted path: `& "C:\Program Files\GitHub CLI\gh.exe" auth login …`. Then let gh supply git's credentials for every tool on the machine:
```bash
gh auth setup-git
```

**4. Commit identity: use GitHub's private noreply address**
This keeps the owner's real email out of the history while still linking commits to their account. Build it as `<id>+<username>@users.noreply.github.com`, where `gh api user --jq .id` gives the id (it's also shown at github.com → Settings → Emails). Set it for this repo only, with no `--global`:
```bash
git config user.name "<Owner Name>"
git config user.email "<id>+<username>@users.noreply.github.com"
```

**5. First commit, then create the private repo and push**
```bash
git add -A
git add --chmod=+x .githooks/pre-commit
git commit -m "chore: set up Handoff Kit v2"
gh repo create <name> --private --source . --remote origin --push
```
The second line marks the hook executable inside git, so it runs in clones on every OS, including clones of commits made on Windows. Template copy: replace the last line with `git push origin main`.

**6. Verify**
```bash
git status -sb
gh repo view --json visibility,url
```
Expect `## main...origin/main` and `PRIVATE`.

**Existing repo with history?** Before the first push, look for secrets or personal data in old commits:
```bash
git log --all --name-only --pretty=format: | sort -u | grep -iE '\.env|secret|key|token|\.csv|\.xlsx'
```
Everything in history goes up with the first push. Removing it afterwards means rewriting history.

**No GitHub?** The kit still works locally in solo mode. Parallel sessions across machines or cloud tools need a shared remote.

---

## The prompts

Save these somewhere you can paste from.

### START (first message in any coding tool)
```text
Run the Handoff Kit START from AGENTS.md: sync with origin; if there are uncommitted changes that aren't yours, stop and list them. Read AGENTS.md, docs/STATE.md, the 10 newest docs/decisions/ and every active docs/sessions/ file. Check for overlapping scope, choose solo or parallel mode, and claim your session file (commit it and push it to main). Then, without asking me anything, tell me in under 10 lines: what this project is, the next task, what you'll do first, your mode and session ID, and the commit hash of your claim. If anything you need is missing, say exactly what. If I've given you a task in this message, start on it after the report; otherwise wait for my go.
```

### CHECKPOINT (any time mid-session)
```text
CHECKPOINT: append what's been done to your session log, add a decision file for each decision since the last checkpoint, commit and push. Reply with one line: what was saved, and the commit hash.
```

### END (last message in any coding tool)
```text
Run the Handoff Kit END from AGENTS.md, in order: close your session file with a full handoff; make sure every decision has a file; fetch and rebase on origin/main, resolving conflicts by the kit's rules; rewrite docs/STATE.md (max one page, starting from origin/main's version); push to main, retrying on rejection; remove your worktree and branch if you used one. Confirm `git status` is clean and in sync with origin, and paste the last commit hash. If anything is unfinished or can't be committed, say exactly what and why. Don't ask me questions.
```

### FIRST TIME IN A SANDBOXED TOOL (e.g. Codex; one time, after START)
```text
One-time check: show me `git remote -v`, `git config user.email` and `git status -sb`, then run `git config core.hooksPath .githooks` and `git fetch`. If your sandbox blocks writes to .git, rerun with escalated permissions and request my approval; use escalation the same way for commit and push. Never change permissions on .git.
```

### PACK (in a coding tool, to start any chat AI: ChatGPT, Claude.ai, Gemini, Perplexity, DeepSeek, Kimi…)
```text
PACK for <chat AI>: <what it will work on>
Run the Handoff Kit PACK from AGENTS.md, from a clean checkout of origin/main: sync; check active sessions for overlap with that scope (if there's any, stop and tell me); claim a session for the chat (mode: chat) and push it to main. Then give me ONE code block, fenced with five backticks, to paste into the chat: the CHAT INSTRUCTIONS from docs/PROMPTS.md with the session ID, the current date and time, and the scope filled in, followed by the full text of AGENTS.md, docs/STATE.md, the 10 newest docs/decisions/ files, every active docs/sessions/ file and any project files the work needs, each headed by its path. Outside the block, say only the session ID.
```

### CHAT INSTRUCTIONS (PACK puts these at the top of every pack; you don't paste them yourself)
```text
You're working on <project> with me under the Handoff Kit. You can't change the project yourself; a coding tool saves your work for you.
Session ID: <session id>. Now: <YYYY-MM-DD HH:MM tz>. Your scope: <scope>.
Below are the project's rules (AGENTS.md), its current state (docs/STATE.md), recent decisions, the active sessions (yours included) and the files you need. Follow every rule in AGENTS.md. Don't guess anything that isn't here; ask me. If STATE.md and what I say disagree, ask which is current. Answer directly and recommend; don't give me menus.
Whenever we've decided or produced something worth keeping, and always at the end, output a HANDOFF block containing: your session file docs/sessions/<session id>.md, complete, with status closed and the Log and Handoff filled in; one docs/decisions/<YYYY-MM-DD>-<slug>.md per decision; and the complete new text of every project file you created or changed, never shortened and with nothing left out. Put each file in its own fenced block, headed by its path. Anything not in a HANDOFF block didn't happen.
Start with under 8 lines: what the project is, the top 3 tasks from STATE.md, and what you'll do within your scope. Then wait.
```

### RECORD HANDOFF (in a coding tool, with the chat's HANDOFF block pasted below it)
```text
RECORD HANDOFF: run the Handoff Kit RECORD HANDOFF from AGENTS.md on the block below, from a clean checkout of origin/main. Save each file at its stated path exactly as given, fixing only path or format problems (tell me what you fixed); the session file replaces the chat's claim. Don't save any project file that looks cut short or says things like "rest unchanged"; tell me instead. If a project file changed on main since the chat's session started, merge it by the conflict rules rather than overwrite it. Then update docs/STATE.md and push to main following the END steps. Confirm it's clean and paste the hash.
<paste HANDOFF block here>
```

---

## Tool notes

Checked against each tool's docs on 2026-10-06. Tools change; if a tool ignores AGENTS.md, check its docs for the current instructions file, and point that file at AGENTS.md rather than copying the rules into it.

**Coding tools** (they follow START, CHECKPOINT and END themselves)

| Tool | Pointer or setting to create | Notes |
|---|---|---|
| Codex (CLI, IDE, cloud) | None; reads `AGENTS.md` | Its local sandbox blocks writes to `.git`: approve escalated git commands. Codex cloud works in its own copy and pushes a branch; finish with END. |
| Claude Code (CLI, desktop) | `CLAUDE.md` containing `@AGENTS.md` | Recent versions also read `AGENTS.md` when there's no `CLAUDE.md`; the pointer works on every version. The desktop app may run each chat in its own worktree; that's parallel mode, already isolated. |
| Gemini CLI | `GEMINI.md` containing `@AGENTS.md`, or `.gemini/settings.json`: `{ "context": { "fileName": ["AGENTS.md", "GEMINI.md"] } }` | Reads `GEMINI.md` by default. |
| GitHub Copilot (VS Code, coding agent) | None needed; optional `.github/copilot-instructions.md`: `Follow AGENTS.md in the repository root; it is mandatory.` | Copilot reads AGENTS.md; its own file is read most reliably in every mode. |
| Aider | `.aider.conf.yml`: `read: AGENTS.md` | Doesn't read AGENTS.md unless told to. |
| Cursor, Windsurf, Zed, Warp, JetBrains Junie, Amp, opencode, Roo Code, Kilo Code, goose, Kimi Code CLI, Jules, Devin, Factory | None; they read `AGENTS.md` | |

Tools that let you pick the model (Copilot, opencode, Aider, Kilo Code, Roo Code…) can run DeepSeek, Kimi, Qwen and other models. They're still coding tools. Put the model in the session's `tool:` field.

**Chat AIs** (read-only; [PACK](#chat-ais) to start, RECORD HANDOFF to save)

ChatGPT, Claude.ai, Gemini, Perplexity, DeepSeek, Kimi, Grok, Le Chat, Microsoft Copilot, Meta AI and any other chat app. Nothing goes in the repo for them. If an app offers a GitHub connector, use it only if it can be limited to this one repo, read-only; the pack works without one.

---

## Pitfalls (learned the hard way)
- **Repo inside OneDrive, Dropbox or iCloud:** the sync locks git's files ("Permission denied"), uploads ignored secrets and personal data, and can turn files into cloud-only placeholders that break builds. Use a non-synced folder; GitHub is the backup.
- **History is forever:** deleting a file in a later commit doesn't remove it from history, and the first push uploads all of it. Set up `.gitignore` before the first commit.
- **Logs are as public as the repo:** session and decision files go wherever the repo goes, including public if it's ever made public. Keep personal data, client details and anything private out of them.
- **The hook is off in fresh copies:** `core.hooksPath` is local config, so it doesn't travel with a clone. START turns it back on; a tool that skips START commits without the check.
- **STATE bloat:** if STATE grows past a page, nobody can start from it. Put detail in session and decision files and link to them.
- **Two AIs, one folder:** this is the one thing the kit can't fix after the fact. Parallel sessions must use separate worktrees, clones or sandboxes.
- **Chat AIs shorten files:** a chat AI asked for a long file often hands back part of it, with "rest unchanged" or "…". RECORD HANDOFF refuses those. Ask the chat for the complete file, or have a coding tool make the change instead.
- **Stale claims:** a session that crashed stays `active`. That includes a chat AI's claim when its HANDOFF never comes back. START flags anything with no log entry for over 24 hours; the owner decides whether to mark it `abandoned`.
- **Leftover worktrees:** old worktree folders can be read-only and block cleanup. On Windows, run `attrib -R .git\worktrees\* /S /D`, then `git worktree prune`.
- **Old wording:** when the setup changes (e.g. "local only" → GitHub), search the repo for the old wording so no file contradicts the new setup.
- **Logins belong to the human.** An AI never handles passwords or tokens; the owner runs `gh auth login`, and the AI does everything after it.

---

*The Handoff Kit v2.0. Created by Tyler Schoneman. Share freely; see the repository README for license and updates.*
