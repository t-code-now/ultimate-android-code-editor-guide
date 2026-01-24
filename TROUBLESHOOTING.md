# Troubleshooting Guide

Common issues and their solutions when setting up NvChad on Android/Termux.

---

## 📱 Termux Issues

### Issue: "Cannot install Termux from Play Store"

**Problem:** Play Store version is outdated and broken.

**Solution:**
1. Uninstall Play Store version if installed
2. Download from GitHub: https://github.com/termux/termux-app/releases
3. Install `termux-app_v0.118.3+github-debug_universal.apk`

---

### Issue: "Package not found" errors

**Problem:** Package repositories not updated.

**Solution:**
```bash
pkg update
pkg upgrade -y
```

---

### Issue: "Repository is under maintenance"

**Problem:** Termux mirrors are down.

**Solution:**
```bash
# Change to a different mirror
termux-change-repo

# Select a different mirror from the list
# Then try again:
pkg update
```

---

## 🖥️ Neovim Installation Issues

### Issue: Neovim version too old (< 0.9.4)

**Problem:** Package version doesn't meet NvChad requirements.

**Solution 1: Try nightly version**
```bash
pkg install neovim-nightly -y
nvim --version
```

**Solution 2: Check what version you have**
```bash
nvim --version | head -n 1
# If v0.9.4+ → OK
# If v0.11.x → Perfect!
```

---

### Issue: Neovim build from source fails

**Problem:** Not enough RAM or storage.

**Solution: Use package version instead**
```bash
# Stop trying to build
cd ~
rm -rf neovim

# Use package (even if slightly older)
pkg install neovim -y

# Even v0.9.4 works fine with NvChad
```

---

### Issue: "Out of memory" during build

**Problem:** Device has limited RAM.

**Solution: Single-threaded build**
```bash
cd ~/neovim
make -j1 CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX
make install
```

Or just use the package:
```bash
pkg install neovim-nightly -y
```

---

## 🎨 Font and Icons Issues

### Issue: Boxes (□) or question marks (?) instead of icons

**Problem:** Nerd Font not installed.

**Solution 1: Termux:Styling app (easiest)**
1. Install "Termux:Styling" from F-Droid
2. Open app → Choose Font
3. Select "JetBrainsMono Nerd Font"
4. Restart Termux

**Solution 2: Manual installation**
```bash
mkdir -p ~/.termux
cd ~/.termux
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/JetBrainsMono.zip
pkg install unzip -y
unzip JetBrainsMono.zip -d JetBrainsMono
cp JetBrainsMono/JetBrainsMonoNerdFont-Regular.ttf font.ttf
exit
```

Then restart Termux completely.

**Verify:**
```bash
echo "📁 📄 🐍 ⚙️  ✓ ✗"
# Should show icons, not boxes
```

---

### Issue: Icons work in Termux but not in Neovim

**Problem:** Font not applied to Neovim properly.

**Solution:**
```bash
# Restart Termux completely
# Close from recent apps, then reopen
# Then start nvim
nvim
```

---

## 🔧 NvChad Installation Issues

### Issue: "NvChad requires Neovim >= 0.9.4"

**Problem:** Neovim version is too old.

**Solution:**
```bash
# Check version
nvim --version | head -n 1

# If too old, update:
pkg upgrade neovim -y

# Or try nightly:
pkg install neovim-nightly -y
```

---

### Issue: Plugins fail to install

**Problem:** Network issues or missing dependencies.

**Solution:**
```bash
# Inside nvim, try reinstalling:
:Lazy sync

# If that fails, clean and reinstall:
rm -rf ~/.local/share/nvim
git clone https://github.com/NvChad/starter ~/.config/nvim
nvim
```

---

### Issue: NvChad dashboard doesn't appear

**Problem:** Startup errors.

**Solution:**
```bash
# Check for errors
nvim
:messages

# Check health
:checkhealth
```

---

## 🐍 Python LSP Issues

### Issue: LSP doesn't start automatically

**Problem:** Configuration not loaded or Pyright not installed.

**Solution 1: Check Pyright is installed**
```bash
which pyright-langserver
# Should output: ~/.local/share/nvim/mason/bin/pyright-langserver
```

**Solution 2: Install Pyright via Mason**
```vim
# Inside nvim:
:Mason
# Search for pyright (press /)
# Press 'i' to install
```

**Solution 3: Manually start LSP**
```vim
# Inside nvim with a .py file open:
:LspStart pyright
```

**Solution 4: Check config exists**
```bash
ls ~/.config/nvim/lua/configs/lspconfig.lua
# Should exist
```

---

### Issue: "pyright-langserver not found"

**Problem:** Mason binary not in PATH.

**Solution:**
```bash
# Add to PATH
echo 'export PATH="$HOME/.local/share/nvim/mason/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verify
which pyright-langserver
```

---

### Issue: Deprecation warning on startup

**Problem:** Using old lspconfig API with Neovim 0.11+.

**Solution:** Update `~/.config/nvim/lua/configs/lspconfig.lua` to use the new API (see Part 11 of guide).

The updated config uses `vim.lsp.config` instead of `require("lspconfig")`.

---

### Issue: LSP shows errors on valid Python code

**Problem:** Pyright can't find installed packages.

**Solution 1: Create pyrightconfig.json**
```bash
cd /path/to/your/project
nano pyrightconfig.json
```

Add:
```json
{
  "typeCheckingMode": "basic",
  "reportMissingImports": false
}
```

**Solution 2: Use virtual environment**
```bash
python -m venv venv
source venv/bin/activate
pip install <your-packages>
nvim yourfile.py
```

---

### Issue: No autocomplete suggestions

**Problem:** LSP not attached or cmp not configured.

**Solution:**
```vim
# Check LSP status
:LspInfo

# Should show "Client: pyright (id: 1, bufnr: [1])"

# If not attached, restart:
:LspRestart
```

---

## 🗂️ Configuration Issues

### Issue: "module 'configs.lspconfig' not found"

**Problem:** File in wrong location or doesn't exist.

**Solution:**
```bash
# Check file exists in correct location
ls ~/.config/nvim/lua/configs/lspconfig.lua

# NOT in custom/configs/!
# Should be in configs/ directly

# If missing, create it (see Part 11 of guide)
```

---

### Issue: Multiple lspconfig files causing conflicts

**Problem:** Duplicate configs in different locations.

**Solution:**
```bash
# Remove duplicate
rm ~/.config/nvim/lua/custom/configs/lspconfig.lua

# Keep only this one:
ls ~/.config/nvim/lua/configs/lspconfig.lua
```

---

### Issue: Changes to config don't take effect

**Problem:** Need to reload configuration.

**Solution:**
```vim
# Inside nvim:
:source ~/.config/nvim/init.lua

# Or just restart nvim
:qa
nvim
```

---

## ⌨️ Keyboard Issues on Android

### Issue: No Esc key on phone keyboard

**Solution 1: Use volume key**
1. Open Termux
2. Long press screen → Settings → More
3. Enable "Use volume keys as"
4. Set Volume Down = Esc

**Solution 2: Use Hacker's Keyboard app**
1. Install "Hacker's Keyboard" from F-Droid/Play Store
2. Much better for coding
3. Has all special keys (Esc, Ctrl, Alt, Tab)

**Solution 3: Enable extra keys bar**
1. Termux → Long press → Settings
2. Enable "Extra keys"
3. Shows Ctrl, Alt, Esc buttons on screen

---

### Issue: Can't type special characters

**Solution:**
Use Hacker's Keyboard app or enable Termux extra keys bar (see above).

---

## 💾 Storage Issues

### Issue: "No space left on device"

**Problem:** Not enough storage for installation.

**Solution:**
```bash
# Check free space
df -h ~

# Clean up
pkg clean
apt clean

# Remove build files if you tried building from source
rm -rf ~/neovim

# Remove old Neovim backups if they exist
rm -rf ~/.config/nvim.backup.*
rm -rf ~/.local/share/nvim.backup.*
```

---

### Issue: Termux taking too much space

**Solution:**
```bash
# Check what's using space
du -sh ~/.local/share/nvim/*
du -sh ~/storage/*

# Clean package cache
pkg clean

# Remove unused packages
pkg autoremove
```

---

## 🔄 Reset and Start Fresh

### Complete reset (nuclear option)

If nothing works, start completely fresh:

```bash
# 1. Remove all Neovim data
rm -rf ~/.config/nvim
rm -rf ~/.local/share/nvim
rm -rf ~/.local/state/nvim
rm -rf ~/.cache/nvim

# 2. Uninstall Neovim
pkg uninstall neovim neovim-nightly -y

# 3. Clean cache
pkg clean

# 4. Start fresh from Part 2 of the guide
pkg install neovim -y
```

---

## 📞 Getting Help

If you're still stuck:

1. **Check error messages:**
   ```vim
   :messages
   :checkhealth
   ```

2. **Search GitHub issues:**
   - [NvChad Issues](https://github.com/NvChad/NvChad/issues)
   - [Neovim Issues](https://github.com/neovim/neovim/issues)
   - [This Guide's Issues](https://github.com/yourusername/ultimate-android-code-editor-guide/issues)

3. **Provide this info when asking for help:**
   - Android version
   - Termux version (`termux-info`)
   - Neovim version (`nvim --version`)
   - Error messages (`:messages` output)
   - What you were trying to do

---

## 🔍 Debug Commands

Useful commands for diagnosing issues:

```bash
# System info
termux-info

# Neovim version and build info
nvim --version

# Check what's installed
pkg list-installed | grep -i nvim
pkg list-installed | grep -i python
pkg list-installed | grep -i node

# Check paths
which nvim
which python
which node
which pyright-langserver

# Storage usage
df -h
du -sh ~/.local/share/nvim
du -sh ~/.config/nvim
```

Inside Neovim:
```vim
:checkhealth
:LspInfo
:Mason
:Lazy
:messages
```

---

**Still having issues?** Open an issue on GitHub with details!