# portconf

> Gentoo Portage configuration cleaner and manager

Originally written by [megabaks](https://github.com/megabaks/portconf) (2012–2014).

## Overview

`portconf` is a comprehensive tool for cleaning and managing `/etc/portage` configuration files in Gentoo Linux. It helps maintain clean USE flags, keywords, masks, and other portage settings.

## Features

### USE Flags Management

- **Validate** USE flags against available versions (requires `eix`)
- **Remove** invalid or duplicate USE flags
- **Sort** USE flags alphabetically, preserving last defined state (on/off)
- **Merge** all flags into one line per atom (handles `<>=~` version switches)
- **Remove** profile-defined flags from `make.conf`

### Atom Management

- **Remove** incorrect/not_found/not_installed atoms
- **Check** `/etc/portage/env` for non-existent configurations
- **Sort** atoms alphabetically

### Keywords Management

- **Sort** and deduplicate keywords
- **Remove** duplicate keywords, keeping only the last defined

### Mask/Unmask Management

- **Remove** incorrect masked/unmasked atoms
- **Clean** profile-defined masked USE flags

### World File Management

- **Regenerate** world file (removes installed but unused packages)
- **Backup** and **restore** world file state

### Overlays Management

- **Remove** unused overlays
- **Clean** dependency cache (`/var/cache/edb/dep`)
- **Support** symlink-based ebuilds

### Backup & Restore

- **Auto backup** before operations (configurable)
- **Restore** to any previous state
- **Backup** `/etc/portage`, `make.conf`, and `world` files

### Conversion

- **Convert** `package.*` flat files to directories
- **Convert** directories back to flat files

## Requirements

- **Gentoo Linux** (requires Portage)
- **root privileges** (for modifying `/etc/portage`)
- **Required utilities:**
  - `eix` — for USE flag validation and cache management
  - `qlist` — for package listing
  - `emerge` — for world file operations
  - `eselect` — for profile management
  - `versionsort` — for version comparison

## Usage

### Basic Usage

```bash
# Show help
portconf --help

# Dry run (no changes)
portconf -n --full

# Apply changes with auto-yes
portconf -y --full

# Backup only
portconf --backup

# Restore from backup
portconf --restore
```

### Command Line Options

#### Common

| Flag | Description |
|------|-------------|
| `-b, --backup` | Create backup of current `/etc/portage` state |
| `-r, --restore` | Restore `/etc/portage` from backup |
| `-s, --sort` | Sort entries alphabetically |
| `-y, --yes` | Auto-answer Yes to all prompts |
| `-p, --pretend` | Check only (no changes) |
| `-n, --dry-run` | Alias for `--pretend` |
| `--no-backup` | Skip backup before operations |
| `--no-diff` | Skip diff prompts, apply changes directly |
| `-h, --help` | Show help |

#### USE Flags

| Flag | Description |
|------|-------------|
| `-us, --use-sort` | Sort USE flags, remove duplicates |
| `-ui, --use-invalid` | Remove invalid USE flags |
| `-um, --use-make` | Remove profile-defined flags from `make.conf` |
| `-sup, --stupid-use-profile` | Remove incorrect masked/unmasked USE flags |
| `-uf, --use-full` | Full USE cleanup (`-us -ui -um -sup`) |

#### Keywords

| Flag | Description |
|------|-------------|
| `-ku, --keyword-uniq` | Sort and deduplicate keywords |
| `-ko, --keyword-one` | Keep only the latest keyword per atom |

#### Trash Cleanup

| Flag | Description |
|------|-------------|
| `-t, --trash` | Remove not_found/not_installed entries, empty/backup/unused files |
| `-ft, --force-trash` | Remove all not_installed entries (excludes `package.mask`) |
| `-sm, --stupid-mask` | Remove incorrect masked atoms |
| `-sum, --stupid-unmask` | Remove incorrect unmasked atoms |
| `-c, --rm-comments` | Remove lines starting with comment |
| `-ac, --rm-all-comments` | Remove all comments |
| `-f, --full` | Full cleanup (`-s -t -uf -ku -sm -sum`) |

#### Converting

| Flag | Description |
|------|-------------|
| `-f2d, --files-2-dirs` | Convert `package.*` files to directories |
| `-d2f, --dirs-2-files` | Convert directories to files |

#### Profile

| Flag | Description |
|------|-------------|
| `-apu, --all-profiles-use` | List USE flags defined in all profiles |
| `-pu, --profiles-use` | List USE flags of selected profile |
| `-cpu, --current-profile-use` | List USE flags of current profile |

#### World

| Flag | Description |
|------|-------------|
| `-wb, --world-backup` | Backup current world file state |
| `-wr, --world-restore` | Restore world file from backup |
| `-wg, --world-regen` | Regenerate world file (auto-backup) |

#### Overlays

| Flag | Description |
|------|-------------|
| `-fr, --fix-repos` | Remove unused overlays and dep-cache |
| `-rc, --regen-cache` | Create temporary eix-cache (recommended for overlays) |

## Examples

### Full Cleanup

```bash
# Full cleanup with backup and auto-yes
portconf -y -f

# Full cleanup with dry-run to preview changes
portconf -n -f

# Full cleanup with no backup
portconf --no-backup -f
```

### USE Flag Management

```bash
# Sort and deduplicate USE flags
portconf -us

# Remove invalid USE flags
portconf -ui

# Full USE cleanup
portconf -uf
```

### World File Management

```bash
# Regenerate world file with backup
portconf -wg

# Backup world file
portconf -wb

# Restore world file
portconf -wr
```

### Overlays Cleanup

```bash
# Remove unused overlays and dep-cache
portconf -fr

# With temporary eix-cache
portconf -fr -rc
```

## Configuration

### `/etc/portconf.conf`

Optional configuration file for custom settings:

```bash
# Number of backups to keep (default: 10)
COUNT=10

# Categories to ignore (one per line)
IGNORE_CATEGORY="
  virtual/
  sys-kernel/linux-headers
"

# Package names to ignore (one per line)
IGNORE_PN="
  linux-headers
  virtual/*
"
```

## Backup Management

Backups are stored in `/var/lib/portconf/` with timestamps:

```
/var/lib/portconf/
  ├── etc_portage_YY.MM.DD-HH.MM.tar.bz2
  ├── world_YY.MM.DD-HH.MM.tar.bz2
  └── ...
```

Restore from backup:

```bash
portconf --restore
```

## Safety

- **Dry-run mode** (`-n` or `-p`) shows what would be done without making changes
- **Auto-backup** before operations (configurable)
- **Diff view** before applying changes (configurable)
- **No destructive operations** without confirmation

## License

Distributed under the terms of the GNU General Public License v3 or later.

## Credits

Original script by [megabaks](https://github.com/megabaks/portconf) (2012–2014).
