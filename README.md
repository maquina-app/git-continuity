# Git Continuity

> Seamlessly transfer work-in-progress between machines without committing to git history

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Bash](https://img.shields.io/badge/bash-5.0+-green.svg)](https://www.gnu.org/software/bash/)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-lightgrey.svg)]()

Git Continuity lets you export your staged and unstaged changes as a patch, transfer it to another machine, and continue working right where you left off—all without polluting your git history with "WIP" commits.

## ✨ Features

- **Beautiful Previews** - See exactly what you're exporting/importing with [Glow](https://github.com/charmbracelet/glow) and [Bat](https://github.com/sharkdp/bat)
- **Interactive TUI** - Gorgeous terminal interface powered by [Gum](https://github.com/charmbracelet/gum)
- **Built-in SCP Transfer** - Automatically transfer patches to remote hosts
- **Saved Hosts** - Configure once, sync forever
- **Bidirectional** - Work seamlessly between desktop ↔ laptop ↔ server
- **Smart Patching** - Handles staged, unstaged, and binary files
- **Safe** - Preview before export, preview before import—no surprises
- **Graceful Degradation** - Works perfectly even without optional dependencies

## Demo

```bash
# On your desktop
$ git add src/auth.rb spec/auth_spec.rb
$ git-continuity export

╭─────────────────────────────╮
│  👁  Changes to Export      │
╰─────────────────────────────╯

 src/auth.rb        | 45 ++++++++++++++++++++++++++++++------
 spec/auth_spec.rb  | 28 ++++++++++++++++++++++
 2 files changed, 67 insertions(+), 6 deletions(-)

[Beautiful syntax-highlighted diff with Glow]

Proceed with export? (y/N) ❯ y

✓ Patch created successfully
Transfer to remote host? (y/N) ❯ y

❯ laptop
  desktop
  
📤 Transferring to laptop...
✓ Successfully transferred!

# On your laptop
$ git-continuity import

╭───────────────────────────╮
│  📥 Import Git Changes    │
╰───────────────────────────╯

❯ feature-auth-20250117.patch

📋 Patch Information
Created: 2025-01-17 14:30:22
Branch: feature/auth

Apply this patch? (y/N) ❯ y

✓ Patch applied successfully!
```

## Quick Start

### Installation

#### macOS (with Homebrew)
```bash
# Install optional dependencies (recommended for best experience)
brew install gum glow bat

# Download and install git-continuity
curl -O https://raw.githubusercontent.com/yourusername/git-continuity/main/git-continuity.sh
chmod +x git-continuity.sh
sudo mv git-continuity.sh /usr/local/bin/git-continuity
```

#### Linux
```bash
# Install Gum
go install github.com/charmbracelet/gum@latest

# Install Glow
go install github.com/charmbracelet/glow@latest

# Install Bat
# Ubuntu/Debian
sudo apt install bat

# Arch Linux
sudo pacman -S bat

# Fedora
sudo dnf install bat

# Download and install git-continuity
curl -O https://raw.githubusercontent.com/yourusername/git-continuity/main/git-continuity.sh
chmod +x git-continuity.sh
sudo mv git-continuity.sh /usr/local/bin/git-continuity
```

#### Manual Installation
```bash
# Clone the repository
git clone https://github.com/yourusername/git-continuity.git
cd git-continuity

# Make executable
chmod +x git-continuity.sh

# Optional: Add to PATH
sudo cp git-continuity.sh /usr/local/bin/git-continuity
```

### First Sync

```bash
# 1. Configure a remote host (one-time setup)
git-continuity config
# Add your laptop: user@laptop-ip:~/patches

# 2. Export your work
cd your-project
git add .
git-continuity export

# 3. On your other machine, import
git-continuity import
# Select the patch and apply!
```

## Usage

### Export (Create Patch)

```bash
# Interactive mode with preview
git-continuity export

# Specify filename
git-continuity export my-feature.patch

# Export and transfer via SCP
git-continuity export --scp laptop

# Skip preview for speed
git-continuity export --no-preview
```

### Import (Apply Patch)

```bash
# Interactive mode with preview
git-continuity import

# Direct file
git-continuity import my-feature.patch

# Skip preview for speed  
git-continuity import --no-preview
```

### Preview Patch

```bash
# Preview without applying
git-continuity preview my-feature.patch

# Interactive preview
git-continuity
# Choose "Preview patch"
```

### Configuration

```bash
# Manage remote hosts
git-continuity config

# List available patches
git-continuity list
```

## Use Cases

### Daily Development Workflow
```bash
# Morning: Start on desktop
git add src/feature.rb
git-continuity export --scp laptop

# Afternoon: Continue on laptop at coffee shop
git-continuity import
# ... work continues ...
git-continuity export --scp desktop

# Evening: Back to desktop, finalize
git-continuity import
git commit -m "Complete feature"
git push
```

### Code Review
```bash
# Teammate sends you a patch
git-continuity preview team-feature.patch
# Review the changes
git-continuity import team-feature.patch
```

### Testing on Another Machine
```bash
# Export experimental changes
git-continuity export experiment.patch

# Test on another machine without committing
# If it works: commit on original machine
# If it fails: discard and keep working
```

### Offline Work
```bash
# Export to USB drive
git-continuity export work.patch
cp work.patch /Volumes/USB/

# Import on air-gapped machine
git-continuity import /Volumes/USB/work.patch
```

## Configuration

### Remote Hosts

Configured hosts are saved in: `~/.config/git-continuity/config`

```bash
# Interactive configuration
git-continuity config

# Example config file
HOST_laptop_HOST="user@192.168.1.10"
HOST_laptop_PATH="~/git-continuity-patches"
HOST_desktop_HOST="mydesktop.local"
HOST_desktop_PATH="~/patches"
```

### SSH Config Integration

Git Continuity works seamlessly with your `~/.ssh/config`:

```bash
# ~/.ssh/config
Host laptop
    HostName 192.168.1.10
    User myuser
    IdentityFile ~/.ssh/id_rsa
    
Host desktop
    HostName 192.168.1.20
    User myuser
```

Then just use: `git-continuity export --scp laptop`

### Patch Storage

Patches are stored in: `~/.local/share/git-continuity/patches/`

This centralized location makes the import picker work automatically and keeps your patches organized.

## Preview Features

### Export Preview
Before creating a patch, see:
- File statistics (additions/deletions)
- Complete diff with syntax highlighting
- Confirmation before proceeding

### Import Preview
Before applying a patch, see:
- Patch metadata (date, branch, commit)
- Change summary
- Optional full diff review
- Untracked files list

### Preview Tools
- **Glow** (preferred): Beautiful markdown-style formatted diffs
- **Bat** (fallback): Syntax-highlighted with line numbers
- **Plain** (always works): Basic colored output

## Requirements

### Required
- Bash 5.0+
- Git

### Optional (for enhanced features)
- [Gum](https://github.com/charmbracelet/gum) - Interactive TUI
- [Glow](https://github.com/charmbracelet/glow) - Beautiful diff rendering
- [Bat](https://github.com/sharkdp/bat) - Syntax highlighting
- SSH/SCP - For automatic transfers

**Note:** Git Continuity works perfectly without optional dependencies—it gracefully falls back to basic functionality.

## Why Git Continuity?

### The Problem
You're working on a feature but need to switch machines:
- ❌ Committing WIP work pollutes git history
- ❌ Pushing incomplete code to remote is unprofessional  
- ❌ Stashing doesn't transfer between machines
- ❌ Manual `git diff > patch` is tedious and error-prone

### The Solution
Git Continuity creates portable patches of your work:
- ✅ No WIP commits in your history
- ✅ Transfer via SCP, USB, cloud—your choice
- ✅ Handles staged, unstaged, and binary files
- ✅ Preview before export/import—catch mistakes
- ✅ Simple, fast, and reliable

## Troubleshooting

### Gum/Glow/Bat not found
The script works without them—install for better UX:
```bash
brew install gum glow bat
```

### SCP permission denied
Set up SSH keys:
```bash
ssh-copy-id user@hostname
```

### Patch not applying
- Ensure you're in the correct git repository
- Check that base commits are similar
- Review conflicts with: `git status`

### Can't find patch on import
- Check: `git-continuity list`
- Use full path: `git-continuity import /full/path/to/patch.patch`

## License

MIT License - see [LICENSE](LICENSE) file for details

## Acknowledgments

Built with amazing tools from the Charm community:
- [Gum](https://github.com/charmbracelet/gum) - Interactive TUI components
- [Glow](https://github.com/charmbracelet/glow) - Markdown rendering
- [Bat](https://github.com/sharkdp/bat) - Syntax highlighting

---

**Made with ❤️ for developers who work across multiple machines**
