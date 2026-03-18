# pk — Terminal Kanban Board

A single-file, zero-install terminal kanban board built with Python. Renders a three-column board (TODO → IN PROGRESS → DONE) right in your terminal using [Rich](https://github.com/Textualize/rich) and SQLite.

![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue)
![License: MIT](https://img.shields.io/badge/license-MIT-green)

## Features

- Three-column kanban board rendered in the terminal
- Color-coded project cards (customizable per project)
- Priority indicators (▲ high, ■ medium, ▼ low)
- SQLite storage — no server, no config, no signup
- Adapts to terminal width; falls back to flat list on narrow terminals
- Single file, ~230 lines of Python

## Requirements

- Python 3.10+
- [Rich](https://github.com/Textualize/rich) (`pip install rich`)
- SQLite3 (included in Python stdlib)

## Installation

### Option 1: Direct download

```bash
curl -o pk https://raw.githubusercontent.com/<you>/pk-board/main/pk
chmod +x pk
```

### Option 2: Clone the repo

```bash
git clone https://github.com/<you>/pk-board.git
cd pk-board
chmod +x pk
```

### Option 3: Just copy the file

`pk` is a single file — copy it anywhere on your system.

### Set up a shell alias (recommended)

Add to your `~/.zshrc` or `~/.bashrc`:

```bash
alias pk='python3 /path/to/pk'
```

If you're using a virtualenv that has `rich` installed:

```bash
alias pk='/path/to/venv/bin/python3 /path/to/pk'
```

Reload your shell:

```bash
source ~/.zshrc
```

## Quick Start

```bash
# Add your first task
pk add "Set up CI pipeline"

# Add with project and priority
pk add "Fix auth bug" -p myproject -P 1

# View the board
pk
```

## Usage

### View the board

```bash
pk              # kanban board (default)
pk board        # same thing
pk ls           # flat list view
```

### Add tasks

```bash
pk add "Task title"                       # default: general project, medium priority
pk add "Task title" -p projectname        # assign to a project
pk add "Task title" -P 1                  # high priority (1=high, 2=med, 3=low)
pk add "Task title" -d "More details"     # add a description
pk add "Deploy v2" -p myapp -P 1 -d "Needs QA signoff"   # all options
```

### Move tasks between columns

```bash
pk mv 3 progress     # move task #3 → IN PROGRESS
pk mv 3 todo         # move task #3 → TODO
pk mv 3 done         # move task #3 → DONE
```

Status aliases for quick typing:

| Full name  | Alias |
|------------|-------|
| `todo`     | `t`   |
| `progress` | `p`   |
| `done`     | `d`   |

```bash
pk mv 3 p            # same as: pk mv 3 progress
pk done 3            # shortcut for: pk mv 3 done
```

### Edit tasks

```bash
pk edit 3 -t "New title"         # change title
pk edit 3 -P 1                   # change priority
pk edit 3 -p newproject          # reassign to another project
pk edit 3 -d "Updated details"   # update description
pk edit 3 -t "Title" -P 1       # multiple changes at once
```

### Delete tasks

```bash
pk rm 3              # delete task #3 (asks for confirmation)
pk rm 3 -y           # delete without confirmation
```

### Archive and filter

```bash
pk archive           # archive all DONE tasks (hides from board)
pk board -p myapp    # board filtered to one project
pk ls -p myapp       # list filtered to one project
```

## Board Layout

```
       TODO (2)              IN PROGRESS (1)            DONE (1)
 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ╭────────────────────╮  ╭────────────────────╮  ╭────────────────────╮
  │ ▲ #1 Fix auth bug  │  │ ■ #3 Write tests   │  │ ▼ #4 Update docs   │
  │    myproject       │  │    general         │  │    general         │
  ╰────────────────────╯  ╰────────────────────╯  ╰────────────────────╯
  ╭────────────────────╮
  │ ■ #2 Add logging   │
  │    myproject       │
  ╰────────────────────╯
```

- **Border color** is set per project (customizable in the `PROJECT_COLORS` dict)
- **Priority icons**: `▲` high (red), `■` medium (yellow), `▼` low (dim)

## Customization

Edit the constants near the top of `pk`:

```python
# Add your own project colors
PROJECT_COLORS = {
    "greenday": "green",
    "clawdbot": "blue",
    "myproject": "magenta",
}

# Priority display
PRIORITY_ICONS = {
    1: ("[red]▲[/red]", "HIGH"),
    2: ("[yellow]■[/yellow]", "MED"),
    3: ("[dim]▼[/dim]", "LOW"),
}
```

## Storage

Tasks are stored in `.pk.db` (SQLite) in the same directory as the `pk` script. The database is created automatically on first run.

### Schema

```sql
CREATE TABLE tasks (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    title       TEXT NOT NULL,
    project     TEXT NOT NULL DEFAULT 'general',
    priority    INTEGER NOT NULL DEFAULT 2,
    status      TEXT NOT NULL DEFAULT 'todo',
    description TEXT DEFAULT '',
    created_at  TEXT DEFAULT (datetime('now','localtime')),
    updated_at  TEXT DEFAULT (datetime('now','localtime')),
    archived    INTEGER NOT NULL DEFAULT 0
);
```

## Command Reference

| Command | Description |
|---------|-------------|
| `pk` | Show kanban board |
| `pk add "title"` | Add a task |
| `pk mv <id> <status>` | Move task to status |
| `pk done <id>` | Mark task as done |
| `pk edit <id>` | Edit task fields |
| `pk rm <id>` | Delete a task |
| `pk archive` | Archive done tasks |
| `pk ls` | Flat list view |
| `pk board` | Kanban board view |

### Flags

| Flag | Used with | Description |
|------|-----------|-------------|
| `-p, --project` | `add`, `edit`, `ls`, `board` | Project name |
| `-P, --priority` | `add`, `edit` | Priority: 1=high, 2=med, 3=low |
| `-d, --description` | `add`, `edit` | Task description |
| `-t, --title` | `edit` | New title |
| `-y, --yes` | `rm` | Skip delete confirmation |

## License

MIT License — see [LICENSE](LICENSE) for details.
