# claude-task

A CLI tool that creates isolated worktrees for GitHub Issues and processes them with Claude Code.

## Requirements

- macOS (uses Apple Seatbelt for sandboxing)
- [gh](https://cli.github.com/) - GitHub CLI
- [ghq](https://github.com/x-motemen/ghq) - Repository management
- [git-wo](https://github.com/kfly8/git-wo) - Worktree management
- [jq](https://jqlang.github.io/jq/) - JSON processing
- [Claude Code](https://claude.ai/claude-code) - Claude CLI

## Setup

### 1. Install Dependencies

```bash
brew install gh ghq jq

# Install git-wo
# See https://github.com/kfly8/git-wo

# Install Claude Code
curl -fsSL https://claude.ai/install.sh | bash
```

### 2. Install claude-task

```bash
git clone https://github.com/kfly8/claude-task.git
cd claude-task

# Add to PATH (add to .bashrc or .zshrc)
export PATH="$PWD/bin:$PATH"
```

## Usage

### Assign an Issue to Claude

```bash
claude-task assign https://github.com/OWNER/REPO/issues/42
```

This will:
1. Fetch the issue content from GitHub
2. Create a new git worktree with branch `issue-42-<slug>`
3. Start Claude Code in **plan mode** within a sandbox

Claude Code starts in plan mode, allowing you to review the implementation plan before execution. Press `Shift+Tab` to switch to implementation mode after approving the plan.

**Options**:
| Option | Description |
|--------|-------------|
| `--dry-run` | Preview only, no execution |
| `--no-sandbox` | Disable sandbox (not recommended) |

### Clean Up Merged Branches

```bash
claude-task clean
```

Removes worktrees and branches that have been merged into main/master.

**Options**:
| Option | Description |
|--------|-------------|
| `--dry-run` | Show what would be deleted without deleting |
| `-C <path>` | Specify repository path |

## Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ claude-task assign <ISSUE_URL>                              │
└─────────────────────────────────────────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │ 1. Fetch issue content │
              │ 2. Create worktree     │
              └────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │   Start Claude Code    │
              │   (plan mode + sandbox)│
              └────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │ Review plan, then      │
              │ implement & create PR  │
              └────────────────────────┘
                           │
                           ▼
              ┌────────────────────────┐
              │ claude-task clean      │
              │ (after PR merged)      │
              └────────────────────────┘
```

## File Structure

```
claude-task/
├── bin/
│   ├── claude-task           # Main entry point
│   └── notify-url            # macOS notification helper
├── libexec/
│   ├── claude-task-assign    # Issue assignment
│   └── claude-task-clean     # Cleanup merged worktrees
├── sandbox/
│   └── claude-task.sb        # Sandbox profile
└── README.md
```

## Security

Claude Code runs in plan mode by default, which only allows read-only operations until you approve the plan. Additionally, file access is restricted using Apple Seatbelt (sandbox-exec).

### Restrictions

| Target | Permission |
|--------|------------|
| worktree directory | Read/Write |
| Main repository .git | Read/Write (for worktree operations) |
| ~/.claude, ~/.claude.json | Read/Write |
| ~/.bun | Read/Write |
| /tmp, /var | Read/Write |
| Home directory (except above) | Read only |
| Network | Unrestricted |

### Sandbox Profile

Customize via `sandbox/claude-task.sb`.

Reference: https://www.mizdra.net/entry/2025/12/01/121805

## License

MIT
