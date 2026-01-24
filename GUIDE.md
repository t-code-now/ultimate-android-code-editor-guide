# Complete Setup Guide: NvChad + Python LSP on Termux

Step-by-step guide to install NvChad from scratch and set up Python development with auto-starting LSP on Android/Termux.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Part 0: Install Termux](#part-0-install-termux)
- [Part 1: Initial Setup & Dependencies](#part-1-initial-setup--dependencies)
- [Part 2: Install Neovim](#part-2-install-neovim)
- [Part 3: Manual Font Installation](#part-3-manual-font-installation)
- [Part 4: Verify Installations](#part-4-verify-installations)
- [Part 5: Backup Existing Neovim Config](#part-5-backup-existing-neovim-config)
- [Part 6: Clone NvChad](#part-6-clone-nvchad)
- [Part 7: Verify NvChad Installation](#part-7-verify-nvchad-installation)
- [Part 8: Install Python LSP (Pyright)](#part-8-install-python-lsp-pyright)
- [Part 9: Verify Pyright Installation](#part-9-verify-pyright-installation)
- [Part 10: Create NvChad Config Structure](#part-10-create-nvchad-config-structure)
- [Part 11: Create Main LSP Configuration File](#part-11-create-main-lsp-configuration-file)
- [Part 12: Verify Plugins Configuration](#part-12-verify-plugins-configuration)
- [Part 13: Create Custom Init File (Optional)](#part-13-create-custom-init-file-optional---fallback-auto-start)
- [Part 14: Test Python LSP](#part-14-test-python-lsp)
- [Part 15: Troubleshooting](#part-15-troubleshooting)
- [Quick Reference Commands](#quick-reference-commands)
- [Success Indicators](#success-indicators)
- [Next Steps](#next-steps)

---

## Prerequisites

Before starting, ensure you have:
- Android device (Android 7.0 or higher recommended)
- Internet connection (for downloading packages)
- At least 1GB free storage space (for building Neovim and NvChad)
- Patience for the setup process

---

## Part 0: Install Termux

### Step 0: Download and Install Termux

**⚠️ IMPORTANT: Do NOT install Termux from Google Play Store!**  
The Play Store version is outdated and no longer maintained.

#### Download Termux from GitHub (Recommended)

1. **Download the correct APK:**
   - Go to: https://github.com/termux/termux-app/releases
   - Scroll down to **v0.118.3** (recommended stable version)
   - Download: **`termux-app_v0.118.3+github-debug_universal.apk`**

   **Direct download link:**
   ```
   https://github.com/termux/termux-app/releases/download/v0.118.3/termux-app_v0.118.3+github-debug_universal.apk
   ```

2. **Install the APK:**
   - Open the downloaded APK file
   - Android will ask "Install unknown apps?" - tap **Settings**
   - Enable **"Allow from this source"**
   - Tap **Install**
   - Tap **Open** when installation completes

3. **First Launch:**
   - Open Termux
   - Wait for initial setup to complete (takes ~30 seconds)
   - You'll see a terminal prompt: `$`

4. **Grant Storage Permission (Optional but Recommended):**
   ```bash
   termux-setup-storage
   ```
   - Tap **Allow** when prompted
   - This lets Termux access your phone's storage

**✅ Termux is now installed!**

#### Why v0.118.3?

- ✅ **Stable release** - well-tested and reliable
- ✅ **Full package support** - all packages work correctly
- ✅ **No known major bugs**
- ✅ **Compatible with all Android versions 7.0+**

**Note:** Newer versions (v0.119.x) might work too, but v0.118.3 is proven stable for development work.

---

## Part 1: Initial Setup & Dependencies

### Step 1: Update Termux and Install Build Dependencies

```bash
# Update package lists
pkg update

# Upgrade installed packages (this may take a few minutes)
pkg upgrade -y

# Install all required build tools and dependencies
pkg install cmake ninja libtool gettext autoconf automake pkg-config unzip curl git build-essential -y

# Install Python (for Python development)
pkg install python -y

# Install Node.js (required for Pyright LSP)
pkg install nodejs -y

# Install ripgrep (for better searching in nvim)
pkg install ripgrep -y
```

**Note:** The `pkg upgrade` command may take 2-5 minutes depending on how many packages need updating. Be patient!

---

## Part 2: Install Neovim

### Step 2: Install Neovim (Try Package First, Then Build if Needed)

#### Method 1: Install from Package Repository (Try This First)

```bash
# Try installing from Termux packages first
pkg install neovim -y

# Check version
nvim --version | head -n 1
# If you see NVIM v0.9.4 or higher (v0.9.5, v0.10.x, v0.11.x), you're done! Skip to Part 3.
# If version is lower than 0.9.4, try neovim-nightly:

pkg install neovim-nightly -y
nvim --version | head -n 1
# If this shows v0.9.4+, you're good! Skip to Part 3.
```

**✅ If package installation gives you v0.9.4 or higher, STOP HERE and go to Part 3!**

---

#### Method 2: Build from Source (Only if Package Version is Too Old)

**⚠️ WARNING: Building from source can fail on some devices due to:**
- Limited RAM (needs ~1GB free)
- Limited storage (needs ~500MB)
- Build tool compatibility issues

**Only do this if the package version is too old!**

```bash
# First, check if you have enough space
df -h ~
# Make sure you have at least 1GB free

# Install build dependencies (if not already installed from Step 1)
pkg install cmake ninja libtool gettext autoconf automake pkg-config unzip curl git build-essential -y

# Remove old Neovim if installed
pkg uninstall neovim neovim-nightly -y 2>/dev/null || true

# Navigate to home directory
cd ~

# Remove old neovim folder if exists
rm -rf neovim

# Clone Neovim stable branch
echo "Cloning Neovim repository..."
git clone https://github.com/neovim/neovim --depth 1 --branch stable
cd neovim

# Build Neovim (this takes 5-10 minutes and may use lots of RAM)
echo "Building Neovim... This will take 5-10 minutes. Please wait."
echo "If build fails with memory errors, your device may not have enough RAM."

# Try building
make CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX

# If the above fails, try with lower parallelism:
# make -j1 CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX

# Install Neovim
echo "Installing Neovim..."
make install

# Verify installation
echo "Verifying Neovim installation..."
nvim --version | head -n 1
# Should show: NVIM v0.10.x or higher

# Clean up build files to save space
cd ~
rm -rf neovim

# Verify nvim command is accessible
which nvim
# Should show: /data/data/com.termux/files/usr/bin/nvim
```

**If build fails with errors:**

1. **Memory errors:** Your device doesn't have enough RAM. Try closing other apps and use:
   ```bash
   cd ~/neovim
   make -j1 CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX
   ```

2. **Compiler errors:** Try using a pre-built AppImage instead (see troubleshooting)

3. **Dependency errors:** Make sure all build tools installed:
   ```bash
   pkg install cmake ninja libtool gettext autoconf automake -y
   ```

**✅ If Neovim is installed successfully, proceed to Part 3!**

---

## Part 3: Manual Font Installation

### Step 3: Install a Nerd Font

NvChad **requires** a Nerd Font to display icons correctly.

#### Method 1: Termux:Styling App (Easiest - Recommended)

1. Install **Termux:Styling** from F-Droid:
   - Open F-Droid app
   - Search "Termux:Styling"
   - Install it

2. Open Termux:Styling app

3. Tap **"CHOOSE FONT"**

4. Select one of these recommended fonts:
   - **JetBrainsMono Nerd Font** (Recommended)
   - **FiraCode Nerd Font**
   - **Hack Nerd Font**
   - **Meslo Nerd Font**

5. Close Termux:Styling

6. **Completely close and reopen Termux** (swipe away from recent apps)

7. Test font installation:
```bash
# These should display as icons, not boxes:
echo "📁 📄 🐍 ⚙️  ✓ ✗ "
```

If you see actual icons (not boxes), font is working! ✅

#### Method 2: Manual Font Installation (Alternative)

```bash
# Create fonts directory
mkdir -p ~/.termux

# Download JetBrainsMono Nerd Font
cd /tmp
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/JetBrainsMono.zip

# Install unzip if needed
pkg install unzip -y

# Extract font
unzip JetBrainsMono.zip -d JetBrainsMono

# Copy font to Termux config
cp JetBrainsMono/JetBrainsMonoNerdFont-Regular.ttf ~/.termux/font.ttf

# Restart Termux completely
exit
```

Then close and reopen Termux.

**✅ Verify font works before continuing!**

---

## Part 4: Verify Installations

### Step 4: Verify All Installations

```bash
# Check Neovim (MUST show 0.9.4+ after installation)
nvim --version
# Expected: NVIM v0.9.4 or higher (v0.10.x, v0.11.x is perfect!)

# Check Python
python --version
# Expected: Python 3.11.x or higher

# Check pip
pip --version
# Expected: pip 23.x or higher

# Check Node.js
node --version
# Expected: v20.x or higher

# Check npm
npm --version
# Expected: 10.x or higher

# Check git
git --version
# Expected: git 2.x or higher

# Check ripgrep
rg --version
# Expected: ripgrep 13.x or higher
```

**✅ All commands should output version numbers. If Neovim shows v0.9.4+, you're ready to proceed!**

---

## Part 5: Backup Existing Neovim Config

### Step 5: Backup Existing Config (if any)

```bash
# Backup existing nvim config if it exists
[ -d ~/.config/nvim ] && mv ~/.config/nvim ~/.config/nvim.backup.$(date +%Y%m%d-%H%M%S)

# Backup existing nvim data if it exists
[ -d ~/.local/share/nvim ] && mv ~/.local/share/nvim ~/.local/share/nvim.backup.$(date +%Y%m%d-%H%M%S)
```

This ensures you don't lose any existing configuration.

---

## Part 6: Clone NvChad

### Step 6: Clone NvChad Starter Template

```bash
# Clone NvChad starter configuration
git clone https://github.com/NvChad/starter ~/.config/nvim && nvim
```

**What happens:**
1. NvChad starter template is cloned to `~/.config/nvim/`
2. Neovim opens automatically
3. Lazy.nvim will automatically start installing plugins
4. You'll see a progress window with plugin installations
5. **Wait for all plugins to finish installing** (2-5 minutes)
6. Once complete, you'll see the NvChad dashboard

**Press `q` to exit nvim once installation is complete.**

**Note about icons:** If you see boxes (□) or question marks (?) instead of icons, don't worry! We'll fix this in the troubleshooting section.

---

## Part 7: Verify NvChad Installation

### Step 7: Verify NvChad Structure

```bash
# Check NvChad structure
ls -la ~/.config/nvim/

# Should show folders like:
# lua/
# init.lua
# lazy-lock.json
# LICENSE
# README.md
```

**✅ NvChad is now installed!**

---

## Part 8: Install Python LSP (Pyright)

### Step 8: Install Pyright via Mason

Open nvim:
```bash
nvim
```

Inside nvim, open Mason by typing:
```vim
:Mason
```

**In the Mason UI:**
1. Press `/` to search
2. Type `pyright`
3. Use arrow keys (or `j`/`k`) to navigate to `pyright`
4. Press `i` to install pyright
5. Wait for installation to complete (you'll see a progress bar)
6. Press `q` to quit Mason
7. Press `q` again to quit nvim (or type `:q` and Enter)

---

## Part 9: Verify Pyright Installation

### Step 9: Verify Pyright is Installed

```bash
# Check if pyright-langserver is installed
which pyright-langserver

# Expected output:
# /data/data/com.termux/files/home/.local/share/nvim/mason/bin/pyright-langserver

# Test pyright version
pyright-langserver --version
```

**✅ If you see the path and version, pyright is installed correctly!**

---

## Part 10: Create NvChad Config Structure

### Step 10: Create Config Folders

```bash
# Create configs folder if it doesn't exist
mkdir -p ~/.config/nvim/lua/configs

# Verify structure
ls -la ~/.config/nvim/lua/
```

You should see:
```
configs/     ← Main configs folder
plugins/     ← NvChad's plugin configs
```

**Note:** The NvChad starter already has a good structure, we just need to add our LSP config.

---

## Part 11: Create Main LSP Configuration File

### Step 11: Create lspconfig.lua

This is the **most important file** for LSP auto-start.

```bash
nano ~/.config/nvim/lua/configs/lspconfig.lua
```

**Copy and paste this COMPLETE content:**

```lua
-- Load NvChad defaults
require("nvchad.configs.lspconfig").defaults()

local nvlsp = require "nvchad.configs.lspconfig"

-- Python LSP using new vim.lsp.enable API (Neovim 0.11+)
vim.api.nvim_create_autocmd("FileType", {
  pattern = "python",
  callback = function(args)
    vim.lsp.enable("pyright")
  end,
})

-- Configure pyright
vim.lsp.config.pyright = {
  cmd = { "pyright-langserver", "--stdio" },
  filetypes = { "python" },
  root_dir = vim.fs.root(0, { "pyproject.toml", "setup.py", "requirements.txt", ".git" }),
  on_attach = nvlsp.on_attach,
  on_init = nvlsp.on_init,
  capabilities = nvlsp.capabilities,
  settings = {
    python = {
      analysis = {
        autoSearchPaths = true,
        useLibraryCodeForTypes = true,
        diagnosticMode = "workspace",
      }
    }
  }
}
```

**Save the file:**
- Press `Ctrl + X`
- Press `Y` to confirm
- Press `Enter` to save

**Note:** This configuration uses the **new Neovim 0.11+ API** (`vim.lsp.config` and `vim.lsp.enable`) which avoids deprecation warnings in newer Neovim versions.

---

## Part 12: Verify Plugins Configuration

### Step 12: Check Plugins Configuration

Check if the plugins configuration loads lspconfig:

```bash
cat ~/.config/nvim/lua/plugins/init.lua
```

**Look for this section (or similar):**
```lua
{
  "neovim/nvim-lspconfig",
  config = function()
    require "configs.lspconfig"
  end,
},
```

**✅ The NvChad starter template should already have this configured correctly!**

If the section is missing or different, you may need to add it. Edit the file:
```bash
nano ~/.config/nvim/lua/plugins/init.lua
```

And ensure the LSP configuration is loaded.

---

## Part 13: Create Custom Init File (Optional - Fallback Auto-Start)

### Step 13: Create Custom Init (Optional)

**Note:** With the NvChad starter template and proper lspconfig, this step is optional but recommended as a fallback.

```bash
# Create custom folder if it doesn't exist
mkdir -p ~/.config/nvim/lua/custom

# Create init file
nano ~/.config/nvim/lua/custom/init.lua
```

**Copy and paste this content:**

```lua
-- Custom initialization

-- Auto-start pyright for Python files (fallback)
vim.api.nvim_create_autocmd("FileType", {
  pattern = "python",
  callback = function()
    vim.schedule(function()
      vim.cmd("silent! LspStart pyright")
    end)
  end,
})

-- Set Python-specific options
vim.api.nvim_create_autocmd("FileType", {
  pattern = "python",
  callback = function()
    vim.opt_local.tabstop = 4
    vim.opt_local.shiftwidth = 4
    vim.opt_local.expandtab = true
  end,
})
```

**Save the file:**
- Press `Ctrl + X`
- Press `Y`
- Press `Enter`

---

## Part 14: Test Python LSP

### Step 14: Create Test Python File

```bash
# Create a test file
nvim test.py
```

### Step 15: Add Test Code

Press `i` to enter insert mode, then type:

```python
def greet(name):
    """Greet someone by name."""
    return f"Hello, {name}!"

def add(a, b):
    """Add two numbers."""
    return a + b

# Test the functions
result = add(5, 3)
print(greet("World"))
print(f"5 + 3 = {result}")
```

Press `Esc` to exit insert mode.

### Step 16: Check LSP Status

In nvim, type:
```vim
:LspInfo
```

**Expected output:**
```
Client: pyright (id: 1, bufnr: [1])
    autostart: true
    root directory: /data/data/com.termux/files/home
    filetypes: python
    cmd: pyright-langserver --stdio
```

**✅ If you see "Client: pyright" with "autostart: true", it's working!**

### Step 17: Test LSP Features

With your cursor in the Python file:

**1. Hover Documentation:**
- Move cursor over `greet` function
- Press `K` (capital K)
- Should show function documentation in a popup

**2. Go to Definition:**
- Move cursor to `greet("World")` (on the word "greet")
- Press `gd`
- Should jump to the function definition

**3. Auto-completion:**
- Press `i` to enter insert mode
- Start typing: `gre`
- Press `Ctrl + Space`
- Should show completion suggestions

**4. Diagnostics:**
- Add an error: `x = undefined_variable`
- You should see a red underline
- Hover over it to see the error message

### Step 18: Complete Test Checklist

Open a new Python file:
```bash
nvim ~/example.py
```

Type some code and verify:

```
✅ Python syntax highlighting works
✅ LSP auto-starts (check with :LspInfo)
✅ Hover documentation works (press K)
✅ Go to definition works (press gd)
✅ Auto-completion works (Ctrl+Space)
✅ Error detection works (red underlines for errors)
✅ Icons show properly in file tree (Ctrl+n or Space+e)
```

### Step 19: Install Additional Python Tools (Optional)

```bash
# Install Python formatters and linters
pip install black isort pylint flake8

# These will enhance your Python development experience
```

---

## Part 15: Troubleshooting

### Problem: Neovim build from source fails

**Common build errors and solutions:**

#### Error: "Out of memory" or "Killed"

Your device doesn't have enough RAM to build with multiple threads.

**Solution 1: Build with single thread**
```bash
cd ~/neovim
make -j1 CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX
make install
```

**Solution 2: Use package version instead**
```bash
# Stop trying to build from source
cd ~
rm -rf neovim

# Use the package version (even if slightly older)
pkg install neovim-nightly -y

# Check version
nvim --version | head -n 1
# Even v0.9.4 or v0.9.5 will work fine with NvChad
```

---

#### Error: "CMake Error" or missing dependencies

**Solution: Reinstall build dependencies**
```bash
pkg install cmake ninja libtool gettext autoconf automake pkg-config unzip curl git build-essential -y

# Clean and retry
cd ~
rm -rf neovim
git clone https://github.com/neovim/neovim --depth 1 --branch stable
cd neovim
make CMAKE_BUILD_TYPE=Release CMAKE_INSTALL_PREFIX=$PREFIX
```

---

#### Can't Build at All?

**Solution: Use Termux package (even if older version)**

```bash
# Give up on building from source
cd ~
rm -rf neovim

# Install whatever version is available
pkg install neovim -y

# Or try nightly
pkg install neovim-nightly -y

# Check version
nvim --version | head -n 1
```

**Important:** Even Neovim v0.9.4 or v0.9.5 will work fine with NvChad. You don't necessarily need v0.10.x or v0.11.x. Building from source is ideal but not required if it causes problems on your device.

---

### Problem: LSP doesn't start automatically

**Solution 1: Manually start LSP**
```vim
:LspStart pyright
```

If this works, your config might need the fallback autocommand (Step 13).

**Solution 2: Check lspconfig.lua exists**
```bash
ls ~/.config/nvim/lua/configs/lspconfig.lua
```

If missing, recreate it (Step 11).

**Solution 3: Reinstall pyright**
```vim
:Mason
# Navigate to pyright
# Press 'X' to uninstall
# Press 'i' to reinstall
```

---

### Problem: "pyright-langserver not found"

**Solution: Check PATH**
```bash
echo $PATH | grep mason
```

If mason bin is not in PATH, add it:
```bash
echo 'export PATH="$HOME/.local/share/nvim/mason/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

### Problem: No icons showing or boxes/squares instead of icons

**Cause:** Nerd Font not installed.

**Solution 1: Install Termux:Styling (Easiest)**

1. Install **Termux:Styling** app from F-Droid
2. Open Termux:Styling
3. Choose a font with "Nerd Font" or "NF" in the name
4. Recommended fonts:
   - **JetBrainsMono Nerd Font**
   - **FiraCode Nerd Font**
   - **Hack Nerd Font**
5. Restart Termux completely
6. Open nvim - icons should now display correctly

**Solution 2: Install Font Manually in Termux**

```bash
# Create fonts directory
mkdir -p ~/.termux

# Download a Nerd Font (example: JetBrainsMono)
cd ~/.termux
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/JetBrainsMono.zip

# Install unzip if not present
pkg install unzip

# Extract font
unzip JetBrainsMono.zip -d JetBrainsMono

# Copy font file (adjust name if needed)
cp JetBrainsMono/JetBrainsMonoNerdFont-Regular.ttf font.ttf

# Restart Termux
exit
```

Then restart Termux and open nvim.

**Verify Icons Work:**
```vim
# Inside nvim, open file tree
Space + e
# or
Ctrl + n

# You should see:
📁 folder icons
📄 file icons
🐍 Python file icons
⚙️  Config file icons
```

If you still see squares or boxes (□), the font isn't installed correctly.

---

### Problem: Error "module 'configs.lspconfig' not found"

**Cause:** The file doesn't exist in the right location.

**Fix:**
```bash
# Make sure the file exists here (NOT in custom/configs/)
ls ~/.config/nvim/lua/configs/lspconfig.lua

# If it doesn't exist, create it using Step 11
```

---

## Quick Reference Commands

### Open Files
```bash
nvim filename.py          # Open a Python file
nvim .                    # Open current directory
```

### Inside Neovim
```vim
:Mason                    # Open Mason (package manager)
:LspInfo                  # Check LSP status
:checkhealth              # Check Neovim health
:Lazy                     # Open plugin manager
Ctrl + n                  # Toggle file tree (alternative)
Space + e                 # Toggle file tree (NvChad default)
Space + h                 # Toggle terminal
Space + ff                # Find files
Space + th                # Change theme
K                         # Hover documentation (on code)
gd                        # Go to definition
Space + ca                # Code actions
```

### Exit Neovim
```vim
:q                        # Quit
:q!                       # Quit without saving
:wq                       # Save and quit
ZZ                        # Save and quit (normal mode)
```

---

## Success Indicators

You've successfully set up NvChad + Python LSP if:

✅ NvChad opens without errors  
✅ Icons display correctly  
✅ `:Mason` shows pyright as installed (green checkmark)  
✅ Opening `.py` files automatically starts pyright  
✅ `:LspInfo` shows "Client: pyright" as active  
✅ Hover (K) shows documentation  
✅ Auto-completion works (Ctrl+Space)  
✅ Errors show with red underlines  

---

## Next Steps

Now that you have Python LSP working, you can:

1. **Add more language servers** - Use `:Mason` to install servers for JavaScript, Lua, C++, etc.
2. **Customize themes** - Press `Space + th` to browse themes
3. **Learn keybindings** - Press `Space + ch` to see cheatsheet (or see [KEYBINDINGS.md](KEYBINDINGS.md))
4. **Install more plugins** - Edit `~/.config/nvim/lua/plugins/init.lua`
5. **Configure formatters** - Set up black, isort for automatic formatting

---

## Summary of File Structure

```
~/.config/nvim/
├── lua/
│   ├── configs/
│   │   └── lspconfig.lua          ← YOU CREATED THIS (Main LSP config)
│   │
│   ├── custom/
│   │   └── init.lua                ← YOU CREATED THIS (Optional fallback)
│   │
│   └── plugins/
│       └── init.lua                ← Loads lspconfig.lua
│
├── init.lua                        ← NvChad's main init
└── lazy-lock.json                  ← Plugin versions
```

---

## Complete Installation Time

- **Build dependencies:** 2-3 minutes
- **Neovim installation (package):** 1-2 minutes
- **Neovim build (from source):** 8-12 minutes (if needed)
- **Nerd Font:** 1-2 minutes
- **NvChad clone:** 30 seconds
- **Plugin installation:** 2-5 minutes
- **Pyright installation:** 1-2 minutes
- **Configuration:** 3-5 minutes

**Total (with package):** ~15-20 minutes  
**Total (with build):** ~25-35 minutes for complete setup

---

🎉 **Congratulations! You now have a fully functional Python development environment with NvChad on Termux!** 🎉

**Happy coding! 🚀**

---

## Additional Resources

- [KEYBINDINGS.md](KEYBINDINGS.md) - Essential keyboard shortcuts
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) - Detailed problem-solving
- [FAQ.md](FAQ.md) - Frequently asked questions
- [NvChad Documentation](https://nvchad.com/docs/quickstart/install)
- [Neovim Documentation](https://neovim.io/doc/)

For issues or questions, open an issue on GitHub or check the FAQ!