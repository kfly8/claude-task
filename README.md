# claude-task

GitHub ProjectsとClaude Codeを連携させて、IssueをClaudeに自動処理させるCLIツールです。

## 必要なもの

- [gh](https://cli.github.com/) - GitHub CLI
- [ghq](https://github.com/x-motemen/ghq) - リポジトリ管理
- [git-wo](https://github.com/kfly8/git-wo) - worktree管理
- [Claude Code](https://claude.ai/claude-code) - Claude CLI

## GitHub Projectの準備

Statusフィールドに以下のオプションを用意してください：

- Ready
- WIP
- Review
- Blocked
- Done

## インストール

```bash
git clone https://github.com/kfly8/claude-task.git
cd claude-task

# PATHに追加（例）
export PATH="$PWD/bin:$PATH"
```

## 使い方

### 1. プロジェクトの初期化

```bash
claude-task init https://github.com/users/YOUR_NAME/projects/NUMBER
```

### 2. IssueをClaudeに割り当て

```bash
claude-task assign https://github.com/OWNER/REPO/issues/42
```

オプション：
- `--dry-run` - 実行内容の確認のみ
- `--no-skip` - `--dangerously-skip-permissions` を使わない

## 動作の流れ

1. Issueの内容を取得
2. Project statusを「WIP」に更新
3. `git-wo` で worktree を作成
4. Claude Codeを起動してタスクを実行
5. 完了時にPRを作成し、statusを「Review」に更新
6. ブロック時はstatusを「Blocked」に更新し、Issueにコメント

## License

MIT
