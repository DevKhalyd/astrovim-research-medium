# Neovim Configuration Report

**Base:** [AstroNvim](https://github.com/AstroNvim/AstroNvim) v6+ template  
**Config path:** `~/.config/nvim`  

---

## Global Settings

### Leader keys (`lua/lazy_setup.lua`)

| Option | Value |
|--------|-------|
| `mapleader` | Space (` `) |
| `maplocalleader` | `,` |
| `icons_enabled` | `true` |
| `update_notifications` | `true` |

### Lazy.nvim (`lua/lazy_setup.lua`)

- **Install colorschemes:** `astrotheme`, `habamax` (fallback during install)
- **UI backdrop:** `100`
- **Disabled built-in plugins:** `gzip`, `netrwPlugin`, `tarPlugin`, `tohtml`, `zipPlugin`

### LSP / Lua (`.neoconf.json`)

- **lua_ls** formatting via LSP is **disabled** (`Lua.format.enable: false`) — likely to use StyLua externally or via Mason instead.

---

## Active Custom Configurations

### 1. Colorscheme & UI — `lua/plugins/astroui.lua`

| Setting | Value |
|---------|-------|
| **Colorscheme** | `astrodark` |
| **Highlight overrides** | None (placeholders commented out) |
| **LSP loading icons** | Braille spinner (`⠋` through `⠏`) |

```lua
colorscheme = "astrodark"
icons = {
  LSPLoading1 = "⠋", LSPLoading2 = "⠙", ... LSPLoading10 = "⠏",
}
```

---

### 2. Statusline — `lua/plugins/heirline.lua`

Overrides the **mode component** in the default AstroNvim statusline:

- **Padding:** `{ left = 1, right = 1 }` on mode text

```lua
opts.statusline[1] = status.component.mode({
  mode_text = { padding = { left = 1, right = 1 } }
})
```

---

### 3. Git signs — `lua/plugins/gitsigns.lua`

Custom gutter sign characters (loaded on `User AstroGitFile`):

| Sign | Character |
|------|-----------|
| Add | `+` |
| Change | `~` |
| Delete | `_` |

---

### 4. Terminal — `lua/plugins/toggleterm.lua`

| Setting | Value | Effect |
|---------|-------|--------|
| `auto_scroll` | `false` | Terminal output does not auto-snap to the bottom on new output |