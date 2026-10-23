# SimpleEdit

**An Agentic Development Environment for desktop and mobile.** Run your agents, understand what they wrote, decide what ships.

When anyone can run ten agents, the scarce thing is a human who understands what ships.

It runs Claude Code, Codex and OpenCode in their own terminals. Use Threads to shape a plan with your agent, Workflows to run dozens of agents that implement it, and the Review Room to understand what changed before it ships.

> **Alpha software.** Expect rough edges, missing features and occasional bugs. Feedback is very welcome: see [Feedback and bug reports](#feedback-and-bug-reports).

---

## Why SimpleEdit

Running many agents in parallel is now normal. Every editor can do it. The hard part has moved: someone must read what the agents wrote, understand it, and stand behind it. Most tools answer with more bots that post comments and approve PRs. SimpleEdit does the opposite.

- **Two agents per change: one builds, one explains.** One agent writes the change. A separate, read-only agent that didn't write it helps you understand it: an overview, a reading order, answers to your questions. It never posts, approves or merges. Nothing goes to GitHub that you did not write or accept.
- **A stack is one review.** A chain of dependent PRs opens as one review, layer by layer.
- **Talk to the agent where the code is.** Comment on a line in the editor, in a diff or in a rendered plan doc. The agent answers in that thread.
- **Native agents, not a wrapper.** Each agent runs in its own unmodified terminal UI. You keep your login, your subscription, your settings, your MCP servers and your slash commands.
- **Fits your team as it is.** Review a colleague's PR or stack with the same overview, reading order and companion you use for your agents' work. Your review goes out through GitHub, in your name. Nobody else needs to install anything.

---

## Features

- **Sessions.** Durable agent sessions. Each one has its own editor, diff and git log. Group sessions, fork one, hand one off to a fresh context, or resume it after a restart.
- **Threads.** Line-anchored conversations with a session's agent. They work in the editor, in the diff, in the rendered markdown view and on the phone. The agent answers when it is idle, so you never type into a running turn.
- **Screen PRs.** Triage your review queue into "Needs your attention", "Quick pass" and "Waiting on author". Get an overview per PR, run a deep review when you ask for it, write your review in the composer, and screen the queue on a schedule. "Continue reviewing" keeps every review you started.
- **Review Room.** One room per PR stack or local branch. It has an overview, a reading order, a commit picker and Viewed marks. Findings, other reviewers' comments and your notes all become threads. A read-only companion agent answers your questions. You submit one review per layer, written by you.
- **Workflows.** Large projects without context bloat. A lead agent turns your plan into a task graph that hidden workers carry out, each in a fresh context. The lead wakes only for decisions, so it keeps the project view. When a worker fails or the plan missed a decision, you and the lead fix the plan, patch the running graph, and the run continues. Review gates stop it until you pass them in the Review Room.
- **Backlog.** Sessions you've prepared but can't start yet, because they would collide with work that is running. Your agents add to it too: a running session can park a follow-up there and stay on its task. When the coast is clear, start the item and it opens as a session with the prompt, provider and model you prepared.
- **Phone companion.** Sessions, backlog, Screen PRs and the full Review Room on your phone, over Tailscale or OpenTunnel, with push notifications.
- **Providers and models.** Claude Code, Codex and OpenCode, plus local models through Ollama. Pick the model per session. A pill shows the tokens each session used.
- **Your agent can drive it.** Every session gets SimpleEdit's tools. Ask your agent to show you a diff, start or park a session, message another agent, ask you for a review or run a workflow.
- **Claude memory, in view.** See what Claude Code remembers about your project, with broken links and unindexed files flagged. Every session of a project shares one memory, and you fix it like any other file.
- **A real editor.** Monaco (the editor of VS Code) with language servers for TypeScript, CSS, JSON, Rust, Python and Go.

## Guides

Short guides for the main workflows are on [simpleedit.org](https://simpleedit.org/#guides):

- [Getting started](https://simpleedit.org/guides/getting-started/)
- [Reviewing plans with SimpleEdit](https://simpleedit.org/guides/reviewing-plans/)
- [Reviewing a PR stack](https://simpleedit.org/guides/reviewing-a-pr-stack/)
- [Running a Workflow](https://simpleedit.org/guides/running-a-workflow/)
- [Driving SimpleEdit from your agent](https://simpleedit.org/guides/driving-simpleedit-from-your-agent/)
- [Working with worktrees](https://simpleedit.org/guides/working-with-worktrees/)
- [Claude memory](https://simpleedit.org/guides/claude-memory/)

---

## Worktrees

SimpleEdit works best with a bare git repository and one worktree per topic. It can clone a repo into that layout for you, or you can open an existing bare repo.

```
myproject.git/          ← bare repo
myproject-main/         ← main worktree
myproject-feature-a/    ← one agent works here
myproject-feature-b/    ← another agent, another branch
```

Each session tracks its own worktree. You can review a diff in one session while an agent still works in another.

---

## Installing

SimpleEdit does not install or authenticate an agent CLI for you. Install and sign in to at least one provider first:

```bash
# Claude Code
claude

# Codex
codex login

# OpenCode
opencode providers login
```

Codex sessions use the native Codex TUI and your existing sandbox, approval, profile, MCP, and authentication defaults. SimpleEdit adds its MCP bridge and lifecycle reporter only for that session. On first use, Codex may show that the reporter hooks need review; run `/hooks` in the embedded terminal and trust the SimpleEdit hook definition. Until then the session still works, but SimpleEdit labels status and worktree reporting as degraded and derives only basic running/exited state from the PTY.

Screen PRs triage, PR overviews, deep-review lenses and synthesis can run on Codex too. These bounded runs always use `codex exec --ephemeral` with a read-only sandbox and approvals disabled.

### macOS — Homebrew (recommended)

```bash
brew trust pago/simpleedit
brew install --cask pago/simpleedit/simpleedit
```

Then, to update:

```bash
brew upgrade --cask simpleedit
```

The cask clears the download quarantine flag for you, so there is no Gatekeeper
detour — SimpleEdit launches straight away.

Both lines matter. Homebrew 6 refuses to load casks from non-official taps
unless you either trust the tap or name it in full on the command line, so
without `brew trust` a plain `brew upgrade` **silently skips SimpleEdit** rather
than updating it. You only ever run it once.

### macOS — manual download

Grab the latest `.dmg` from the [Releases page](https://github.com/pago/simpleedit-releases/releases).

SimpleEdit is ad-hoc signed but not notarized by Apple, so a downloaded copy
needs a one-time approval:

1. Open the `.dmg` and drag **SimpleEdit** to Applications.
2. Double-click SimpleEdit. macOS will refuse to open it the first time.
3. Go to **System Settings → Privacy & Security**, scroll to the note about SimpleEdit being blocked, and click **Open Anyway**.

Alternatively, clear the download quarantine flag from a terminal and launch normally:

```bash
xattr -dr com.apple.quarantine /Applications/SimpleEdit.app
```

> If you see **"SimpleEdit is damaged and can't be opened"** on an older build, it predates ad-hoc signing — download the latest release, or run the `xattr` command above.

Because Apple notarization is what Squirrel's signature check wants, a manually
installed copy can download an update but not install it. The in-app updater
therefore only offers a restart on Windows and Linux; on macOS, prefer Homebrew.

### Updating a Homebrew install

A Homebrew copy updates itself: the banner offers **Update & Restart**, which
quits SimpleEdit, runs the upgrade, and reopens it. If it fails, the next launch
says so and offers the log.

If you would rather run it by hand, use a terminal **other than SimpleEdit**.
`brew upgrade` replaces the app bundle, and run from inside SimpleEdit the
upgrade is a child of the app being replaced — quitting the app would kill the
upgrade partway through. The **Update & Restart** button exists precisely because
it sidesteps that.

### Windows / Linux

Grab the `.exe` (Windows) or `.AppImage` / `.deb` (Linux) from the
[Releases page](https://github.com/pago/simpleedit-releases/releases). These builds
self-update in place — the banner's **Restart & Update** button works.

---

## Language server support

SimpleEdit provides LSP-powered diagnostics, hover info, and go-to-definition for several languages. Each language server is resolved from your project's `node_modules/.bin` first, then your system PATH — so you can install them locally per project or globally.

### TypeScript / JavaScript

```bash
# local (recommended — picks up project's own TypeScript)
npm install --save-dev typescript typescript-language-server

# global
npm install -g typescript typescript-language-server
```

Both `typescript` and `typescript-language-server` are needed. SimpleEdit automatically passes the project's local `tsserver.js` path to the language server so it uses the same TypeScript version as your build.

### CSS / SCSS / Less

```bash
# local
npm install --save-dev vscode-langservers-extracted

# global
npm install -g vscode-langservers-extracted
```

### JSON

Included in `vscode-langservers-extracted` (see CSS above).

### Rust

Install `rust-analyzer` via rustup or your system package manager:

```bash
rustup component add rust-analyzer
# or: brew install rust-analyzer
```

### Python

```bash
pip install python-lsp-server
# or: pipx install python-lsp-server
```

### Go

```bash
go install golang.org/x/tools/gopls@latest
```

---

## Feedback and bug reports

Please use this repository's [issues](https://github.com/pago/simpleedit-releases/issues/new/choose):

- **Bug report**: something doesn't work as described, or crashes.
- **Feature request**: something you'd like SimpleEdit to do.

Issues here are for people using SimpleEdit. The roadmap and day-to-day planning
are kept elsewhere, so a request may be closed with a note on where it stands
rather than tracked here.

---

## About this repository

This repository publishes SimpleEdit's releases (installers, update metadata and
release notes) and collects feedback. The source code is developed in a private
repository.
