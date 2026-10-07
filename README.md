# The Handoff Kit

**One file you give to any AI so that every chat, in every tool, picks up exactly where the last one left off, even when many are working at once.**

AI chats forget. Switch from Claude to ChatGPT to Codex, or open a second chat, and you re-explain everything. Decisions get lost, two chats overwrite each other, and nobody knows what's current.

The Handoff Kit fixes that by moving the memory out of the chat and into your project's files and GitHub history, with a small protocol every AI follows:

- **`AGENTS.md`**: the rules every AI reads first.
- **`docs/STATE.md`**: the current truth on one page: status, next 3 tasks, blockers.
- **`docs/decisions/`**: one file per decision, so nobody re-argues what's settled.
- **`docs/sessions/`**: one file per chat: what it claimed, what it did, and its handoff.
- **START / CHECKPOINT / END prompts**: every chat claims its work, saves as it goes, and hands off cleanly. Parallel chats work in separate copies and merge through GitHub.

It works with any AI, any number of them, at the same time:
- **Coding tools** work on the project directly: Claude Code, Codex, Cursor, Gemini CLI, Copilot, Kimi Code CLI, Aider, opencode, Windsurf and more.
- **Chat AIs** take part through copy-paste: ChatGPT, Claude.ai, Gemini, Perplexity, DeepSeek, Kimi, Grok, or any other chat app. A coding tool gives you one block to paste into the chat (**PACK**), and saves what the chat produced when you paste its answer back (**RECORD HANDOFF**). The chat app never needs access to your repo.

## Quickstart

**Option A: from the template (new projects)**
1. Click **Use this template** at the top of this page, and keep the new repo **private**.
2. Copy it to a normal folder on your computer, not one synced by OneDrive, Dropbox or iCloud: `gh repo clone <you>/<repo> <folder>`, or ask your AI tool to do it.
3. Open that folder in an AI coding tool and say: **"Set up this project with the Handoff Kit."**

**Option B: just the file (new or existing projects)**
1. Download [`HANDOFF-KIT.md`](HANDOFF-KIT.md), or copy its contents.
2. Open your project folder in an AI coding tool and attach the file.
3. Say: **"Set up this project with the Handoff Kit."** (For an existing project: "Install the Handoff Kit into this existing project.")

**Then, either way:** answer the AI's questions, or reply "defaults". From then on, start every chat with **START** and end it with **END**. Setup saves every prompt in your project as `docs/PROMPTS.md`.

You'll need a free GitHub account. The AI walks you through the rest; you only run the one login command yourself.

## Why it works

- **Memory lives in the repo, not the chat.** Any AI, any day, starts from the same files.
- **Separate files don't collide.** Each chat writes only its own session and decision files. The one shared page, STATE, is merged at the end of each chat.
- **Parallel chats never share a folder.** Each one gets its own worktree, sandbox or clone, and they meet on GitHub.
- **Push early, push often.** A crashed chat loses at most one step.

## FAQ

**Does it work with ChatGPT, Perplexity, DeepSeek, Kimi…?** Yes, with any chat app. In any coding tool, say "PACK for Perplexity: research competitor pricing" (for example). You get one block to paste into the chat, with everything it needs: the rules, the current state, and its own session ID. At the end, the chat gives you a HANDOFF block. Paste that into a coding tool with RECORD HANDOFF, and it's saved.

**How many AIs can work at once?** As many as you like, in any mix. Each one claims what it's working on, so the others can see it, and coding tools each work in their own copy of the project.

**Do I need to know git?** No. The AI runs git. You approve, and you run one login command once per computer.

**Is my project public?** No. The kit creates a **private** GitHub repo by default.

**What if two chats edit the same thing?** Each chat claims its scope at START. If the scopes overlap, the second chat stops and asks you. If changes still clash at END, the kit's conflict rules say to keep everyone's work and ask you when it's ambiguous.

## License

<!-- TODO: choose before publishing — MIT recommended for maximum reuse -->

---
Created by Tyler Schoneman. Feedback and improvements welcome: open an issue.
