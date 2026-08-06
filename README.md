# superopencode

opencode with sticky per-directory tmux sessions. Twin of `superclaude`, third in the family
(`superclaude`/`sc` → Claude Code, `supervibe`/`sv` → Mistral Vibe, `superopencode`/`so` → opencode).

## Install

Clone anywhere, then symlink both scripts onto your `PATH`:

```sh
git clone https://github.com/scharc/superopencode.git
cd superopencode
ln -sfn "$PWD/bin/superopencode" ~/.local/bin/superopencode
ln -sfn "$PWD/bin/so"            ~/.local/bin/so
```

Requires `tmux`, `opencode` and `fzf`.

## Use

```
so                    fzf picker over all sessions ("new session" at top)
so n                  new session in $PWD
so <N>                attach to N-th session (most-recent first)

superopencode         attach to this directory's session, or create it
superopencode new     new session (auto-numbered: so|/path-2, -3, …)
superopencode list    list sessions for $PWD
superopencode list all  fzf picker over every so session
superopencode attach NAME
superopencode kill NAME
```

Anything else is passed straight through to opencode:
`superopencode run "fix the tests"` → `opencode --auto run "fix the tests"`.

## Behaviour

Sessions are named `so|<cwd>`, with `.` and `:` rewritten to `_` (tmux reserves them in target
specs). One session per directory; `new` auto-numbers collisions. The prefix `so|` never collides
with superclaude's `sc|` or supervibe's `sv|`.

Launches `opencode --auto` — opencode's yolo flag, auto-approving anything not explicitly denied.
`deny` rules in `opencode.json` are still enforced (currently only `rm -rf /*`). Everything marked
`ask` there — force-push, `git reset --hard`, `git clean`, `docker volume rm`, … — is auto-approved
inside these sessions. That is the point of the wrapper, but it is worth knowing.

Sessions live on Codeman's tmux socket (`tmux -L codeman`), so they show up in Codeman's mobile web
UI alongside the Claude Code ones.

## Env

| Var | Default | Purpose |
|-----|---------|---------|
| `SO_OPENCODE_CMD` | `opencode --auto` | Launch command. Set to `opencode` to drop yolo, or `opencode --agent research` to pin an agent. |
| `SO_TMUX_SOCKET` | `codeman` | tmux socket. Overrides `CODEMAN_TMUX_SOCKET` / `CODEMAN_INSTANCE`. |

## Note on labels

Claude Code publishes its conversation summary to the tmux pane title, so `sc` rows read as the
thing you were working on. opencode sets a static `OpenCode` title instead, so `so` falls back to
the directory name. If opencode ever starts setting a real title, the fallback yields to it
automatically.
