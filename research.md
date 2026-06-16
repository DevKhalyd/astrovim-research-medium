# AstroNvim Overview & Setup Guide

A practical guide for migrating from a traditional IDE to an **AstroNvim** workflow: edit code in Neovim, run agents in a split terminal, and manage Git with Lazygit.

**Related docs:** [medium_article.md](medium_article.md) (Medium draft) · [article_expectations.md](article_expectations.md) (article goals) · [lua_config.md](lua_config.md) (your custom Lua settings)

---

## How to read this guide

Sections are ordered for someone setting up from scratch:

1. **Overview** — what AstroNvim is and how key notation works  
2. **Installation** — Neovim, terminal, and tools  
3. **Core concepts** — parsers, LSP, debugger  
4. **Modes** — foundation for every shortcut below  
5. **Daily commands** — save, quit, edit, tabs  
6. **Code navigation** — move around and jump to definitions  
7. **File explorer & search** — Neo-tree and project search  
8. **Terminal (toggleterm)** — run agents alongside your code  
9. **Agent workflow** — end-to-end steps  
10. **Lazygit** — stage, commit, push  
11. **Lua configuration** — where your config lives  

Throughout this doc, **`<Space>` means the Leader key** (configured as Space in AstroNvim). Press Space, release, then press the next key — e.g. `<Space> e` is Space, then `e`.

---

## 1. Overview

### What is AstroNvim?

[AstroNvim](https://github.com/AstroNvim/AstroNvim) is a Neovim distribution: a pre-configured template with plugins for file trees, LSP (autocomplete and diagnostics), Git integration, fuzzy search, and built-in terminal splits. Your config lives in `~/.config/nvim`.

### Leader key & Which-Key menu

| Key | Role |
| :--- | :--- |
| `<Space>` | **Leader** — prefix for most AstroNvim shortcuts |
| `,` | **Local leader** — buffer-local mappings |
| `<Space>` (alone, in Normal mode) | Opens the **Which-Key** menu to discover available actions |

### Quick start (after installation)

```bash
cd /path/to/your/project
nvim .
```

Then: `<Space> e` to open the file tree, `<Space> t h` to open a terminal at the bottom, and `<Space> g g` to open Lazygit.

---

## 2. Installation

### Neovim (macOS Apple Silicon)

1. Download the **macOS arm64** build from [Neovim Releases](https://github.com/neovim/neovim/releases/tag/stable).
2. Add Neovim to your PATH in `~/.zshenv`:

```bash
export PATH="/Users/username/SoftwareDevelopment/nvim-macos-arm64/bin:$PATH"
```

3. Verify the install:

```bash
nvim --version
cc --version    # C compiler (Xcode Command Line Tools)
which pbcopy && which pbpaste   # macOS clipboard tools
```

### AstroNvim

Follow the official [AstroNvim installation guide](https://docs.astronvim.com/#-installation). In short, back up any existing config and clone the template into `~/.config/nvim`, then launch `nvim` and let plugins install.

### Terminal emulator (Ghostty)

Install from [Ghostty Docs](https://ghostty.org/docs), then create a config file:

```bash
mkdir -p ~/.config/ghostty
# Open ~/.config/ghostty/config in your editor
```

Add font settings:

```ini
font-family = JetBrains Mono
font-size = 14
```

Test that Nerd Font icons render:

```bash
ghostty +show-face --string="A"
ghostty +show-face --string="󰘬"
```

### CLI tools

```bash
brew install fzf zoxide lazygit
```

| Tool | Purpose | Notes |
| :--- | :--- | :--- |
| [fzf](https://github.com/junegunn/fzf) | Fuzzy finder | Used by AstroNvim search |
| [zoxide](https://github.com/ajeetdsouza/zoxide) | Smart `cd` | Add a project: `zoxide add .` |
| [lazygit](https://lazygit.dev/) | Git TUI | Opened from Neovim via `<Space> g g` |

### Bundled plugins (reference)

AstroNvim ships with these; you normally do **not** install them manually:

- [gitsigns.nvim](https://github.com/lewis6991/gitsigns.nvim) — Git gutter signs  
- [neo-tree.nvim](https://github.com/nvim-neo-tree/neo-tree.nvim) — File explorer  
- [toggleterm.nvim](https://github.com/akinsho/toggleterm.nvim) — Split terminals  

---

## 3. Core concepts

| Command | What it does |
| :--- | :--- |
| `:TSInstall <language>` | Installs a **Tree-sitter** parser — syntax highlighting and structural understanding of your code |
| `:LspInstall` | Installs a **Language Server** — autocomplete, go-to-definition, diagnostics, and refactor hints |
| `:DapInstall` | Installs a **debugger adapter** — pause, step, and inspect running code |

On first open of a new language, AstroNvim may prompt you to install the matching parser and LSP. Accept when asked.

---

## 4. Modes

Neovim behavior depends on which **mode** you are in. Most shortcuts in sections 5–10 require **Normal mode** (press `Esc` to return to it).

| Mode | How to enter | How to exit | When to use |
| :--- | :--- | :--- | :--- |
| **Normal** | Default when you open a file; `Esc` from any mode | `i`, `a`, `v`, or `:` to switch modes | Navigate, delete, copy/paste, run shortcuts — spend most of your time here |
| **Insert** | `i` (before cursor), `a` (after cursor), `o` (new line below) | `Esc` or `Ctrl + [` | Typing and editing text |
| **Visual** | `v` (character), `V` (line), `Ctrl + v` (block) | `Esc` | Select text to yank, delete, or indent |
| **Command-line** | `:` (commands) or `/` (search) | `Enter` to run, `Esc` to cancel | `:w` save, `:q` quit, `:%s/old/new/g` replace |
| **Terminal** | Open terminal (`<Space> t h`), then `i` to type | `Ctrl + \` then `Ctrl + n` | Shell commands and agents inside Neovim |

---

## 5. Most common commands

These work in **Normal mode** unless noted.

### Save & quit

| Keys | Action |
| :--- | :--- |
| `:w` | Save current file |
| `:w filename.ext` | Save as (first save with a new name) |
| `:q` | Quit (fails if there are unsaved changes) |
| `:q!` | Force quit without saving |
| `:wq` | Save and quit |

### Movement & editing

| Keys | Action |
| :--- | :--- |
| `h`, `j`, `k`, `l` | Left, down, up, right |
| `yy` | Copy (yank) the current line |
| `p` | Paste below cursor |
| `dd` | Delete the current line |
| `dw` | Delete from cursor to end of word |
| `X` | Delete character before cursor (like Backspace) |
| `u` | Undo |
| `Ctrl + r` | Redo |

### Tabs & buffers (AstroNvim)

| Keys | Action |
| :--- | :--- |
| `[b` / `]b` | Previous / next buffer (tab) |
| `<Space> c` | Close current buffer |
| `Shift + H` / `Shift + L` | Jump to first / last buffer in list |

### AstroNvim shortcuts

| Keys | Action |
| :--- | :--- |
| `<Space>` | Which-Key menu |
| `<Space> h` | Go to home directory (in some contexts) |
| `<Space> n` | New file |

> **Neo-tree note:** To create a file in the file tree, open Neo-tree (`<Space> e`), select a folder, then use the tree’s create action (shown in Which-Key when Neo-tree is focused). This is different from pressing `a` in Normal mode, which enters Insert mode.

---

## 6. Code navigation

| Key | Action |
| :--- | :--- |
| `w` | Next word start |
| `b` | Previous word start |
| `e` | End of current word |
| `0` | Start of line |
| `$` | End of line |
| `gg` | Top of file |
| `G` | Bottom of file |
| `gd` | Go to definition |
| `gI` | Go to implementation |
| `gr` | Go to references |
| `K` (Shift + k) | Hover documentation |
| `Ctrl + u` / `Ctrl + d` | Scroll up / down half a page |

---

## 7. File explorer & searching

| Shortcut | Action |
| :--- | :--- |
| `<Space> e` | Toggle Neo-tree (sidebar file explorer) |
| `<Space> f f` | Fuzzy-find files by name |
| `<Space> f w` | Live grep — search text across the project |
| `<Space> f o` | Recently opened files |
| `<Space> f c` | Search for the word under the cursor |

---

## 8. Terminal (toggleterm)

| Shortcut / command | Action | Agent workflow use |
| :--- | :--- | :--- |
| `<Space> t h` | Toggle **horizontal** terminal (bottom split) | **Recommended for agents** — code on top, terminal below |
| `<Space> t v` | Toggle **vertical** terminal (right split) | Code on the left, terminal on the right |
| `Ctrl + h` / `Ctrl + l` | Move focus left / right between windows | Switch between code and terminal without closing the terminal |
| `Ctrl + \` then `Ctrl + n` | Terminal → Normal mode | Scroll and copy agent output without typing into the shell |
| `2<Space> t h` | Open a **2nd** horizontal terminal | Run manual commands while an agent is busy in terminal 1 |
| `exit` | End the shell session | Stops the agent process; toggling only hides the terminal |
| `:ToggleTermSendVisualSelection` | Send visual selection to terminal | Highlight code in Visual mode, run this command to paste into the agent prompt |

---

## 9. Agent workflow

Typical loop: edit → run agent in terminal → review changes → commit with Git.

| Step | Action | Shortcut / command | Purpose |
| :--- | :--- | :--- | :--- |
| 1. Open | Start in project root | `nvim .` | Load AstroNvim in your repo |
| 2. Explore | Open file tree | `<Space> e` | Navigate project files |
| 3. Prompt | Open horizontal terminal | `<Space> t h` | Bottom split to run your agent (Cursor CLI, etc.) |
| 4. Focus | Switch window focus | `Ctrl + h` or `Ctrl + l` | Move between editor and terminal |
| 5. Review | Toggle terminal hidden | `<Space> t h` | Hide terminal to read full-width code |
| 6. Refresh | Reload buffer | `:e` | Pick up file changes if Neovim didn’t auto-reload |
| 7. Git | Open Lazygit | `<Space> g g` or `<Space> t l` | Stage, commit, and push |

---

## 10. Lazygit

**Open from AstroNvim:** `<Space> g g` or `<Space> t l` (both open Lazygit in a toggle terminal).

| Action | Key | How to use |
| :--- | :--- | :--- |
| Stage / unstage file | `Space` | Highlight a file in the Files panel |
| Stage all files | `a` | Stage or unstage every modified file |
| Commit | `c` | Type message, `Enter` to confirm, `Esc` to cancel |
| Commit with hook | `w` | Commit with pre-commit hook behavior |
| Push | `P` (Shift + p) | Push to remote |
| Pull | `p` | Pull from remote |
| Switch panels | `1`–`5` | 1 Status, 2 Files, 3 Branches, 4 Commits, 5 Stash |
| View diff | `Enter` | Open diff for highlighted file |
| Discard changes | `d` | **Warning:** discards uncommitted changes in that file |
| Quit | `q` | Return to Neovim |

---

## 11. Lua configuration

| Path | Purpose |
| :--- | :--- |
| `~/.config/nvim/lua/` | Your AstroNvim user config (plugins, mappings, options) |
| `~/ResearchProjects/AstroNvim/` | This research project (notes and config report) |

For a detailed breakdown of your current customizations (colorscheme, statusline, gitsigns, toggleterm), see **[lua_config.md](lua_config.md)**.
