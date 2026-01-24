# Essential Keybindings

Quick reference for the most important keyboard shortcuts in NvChad.

## 📌 Legend

- `Space` = Leader key (main prefix for most commands)
- `Ctrl` = Control key
- `+` = Press keys simultaneously
- `→` = Then press (sequence)

---

## 🚀 Most Important (Learn These First)

| Keybinding | Action | Description |
|------------|--------|-------------|
| `Space + e` | Toggle file tree | Open/close file explorer |
| `Space + h` | Toggle terminal | Open/close integrated terminal |
| `Space + ff` | Find files | Search and open files |
| `Space + fw` | Find word | Search text in files |
| `Space + th` | Change theme | Browse and select themes |
| `:q` | Quit | Close current file |
| `:w` | Save | Write changes to disk |
| `:wq` | Save and quit | Write and close file |

---

## 📁 File Navigation

| Keybinding | Action |
|------------|--------|
| `Space + e` | Toggle file tree |
| `Ctrl + n` | Toggle file tree (alternative) |
| `Space + ff` | Find files (fuzzy search) |
| `Space + fa` | Find all files |
| `Space + fb` | Find buffers (open files) |
| `Space + fo` | Find old files (recent) |
| `Ctrl + o` | Jump back |
| `Ctrl + i` | Jump forward |

---

## 🖥️ Terminal

| Keybinding | Action |
|------------|--------|
| `Space + h` | Toggle horizontal terminal |
| `Space + v` | Toggle vertical terminal |
| `Esc Esc` | Exit terminal mode |
| `Ctrl + x` | Close terminal |

---

## ✏️ Editing

### Basic Editing
| Keybinding | Action |
|------------|--------|
| `i` | Insert mode (before cursor) |
| `a` | Insert mode (after cursor) |
| `o` | Insert new line below |
| `O` | Insert new line above |
| `Esc` | Return to normal mode |
| `u` | Undo |
| `Ctrl + r` | Redo |
| `dd` | Delete line |
| `yy` | Copy line |
| `p` | Paste after cursor |
| `P` | Paste before cursor |

### Selection
| Keybinding | Action |
|------------|--------|
| `v` | Visual mode (character) |
| `V` | Visual line mode |
| `Ctrl + v` | Visual block mode |
| `ggVG` | Select all |

---

## 🔍 Search and Replace

| Keybinding | Action |
|------------|--------|
| `/pattern` | Search forward |
| `?pattern` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `Space + fw` | Find word in files |
| `:%s/old/new/g` | Replace all in file |

---

## 🧩 LSP (Code Intelligence)

| Keybinding | Action | Description |
|------------|--------|-------------|
| `K` | Hover documentation | Show info about symbol |
| `gd` | Go to definition | Jump to where symbol is defined |
| `gr` | Go to references | Find all uses of symbol |
| `gi` | Go to implementation | Jump to implementation |
| `Space + ca` | Code actions | Show available fixes |
| `Space + ra` | Rename symbol | Rename across project |
| `Space + fm` | Format code | Auto-format current file |
| `[d` | Previous diagnostic | Jump to previous error |
| `]d` | Next diagnostic | Jump to next error |

---

## 🎨 Appearance

| Keybinding | Action |
|------------|--------|
| `Space + th` | Change theme |
| `Space + uu` | Update NvChad |
| `Ctrl + h/j/k/l` | Navigate windows |

---

## 📦 Plugin Management

| Keybinding | Action |
|------------|--------|
| `:Mason` | Open Mason (LSP installer) |
| `:Lazy` | Open Lazy (plugin manager) |
| `:checkhealth` | Check Neovim health |
| `:LspInfo` | Check LSP status |

---

## 🪟 Window Management

| Keybinding | Action |
|------------|--------|
| `Ctrl + h` | Move to left window |
| `Ctrl + j` | Move to window below |
| `Ctrl + k` | Move to window above |
| `Ctrl + l` | Move to right window |
| `Space + x` | Close current window |
| `:split` | Split horizontal |
| `:vsplit` | Split vertical |

---

## 📋 Buffers (Open Files)

| Keybinding | Action |
|------------|--------|
| `Tab` | Next buffer |
| `Shift + Tab` | Previous buffer |
| `Space + x` | Close buffer |
| `Space + b` | List buffers |

---

## 💡 Tips

### Terminal on Android
Since Android keyboards often lack physical keys:

1. **Use Hacker's Keyboard** app for better key support
2. **Volume Down = Esc** (in Termux settings)
3. **Enable "Extra Keys" bar** in Termux (shows Ctrl, Alt, Esc buttons)

### Learning Curve
1. **Day 1:** Learn `Space + e`, `Space + h`, `:w`, `:q`
2. **Week 1:** Add `Space + ff`, `K`, `gd`
3. **Month 1:** Master visual mode and LSP features

### Custom Keybindings
Edit `~/.config/nvim/lua/mappings.lua` to add your own shortcuts.

---

## 🎓 Practice Exercises

Try these to build muscle memory:

1. Open file tree → Navigate → Open file → Close tree
2. Write code → Press `K` on function → Read docs
3. Error in code → Press `Space + ca` → Select fix
4. Open terminal → Run script → Close terminal
5. Find file → Edit → Save → Switch to another file

---

## 📚 Resources

- [Neovim Tutorial](https://neovim.io/doc/user/) - Official docs
- [NvChad Docs](https://nvchad.com/docs/quickstart/install) - Configuration guide
- Run `:Tutor` in Neovim - Interactive tutorial

---

**Tip:** Print this page and keep it nearby while learning!