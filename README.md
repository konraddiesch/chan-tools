# chan-tools

Standalone tools for analyzing and plotting TouchDesigner `.chan` performance
recordings. Project-agnostic: the tool lives here, the `.chan` data lives in
whatever project you point it at.

## Contents

| File | Purpose |
|---|---|
| `chan_mcp.py` | MCP server (JSON-RPC 2.0 over stdio) exposing `chan_list`, `chan_info`, `chan_analyze`, `chan_plot` |
| `chan_stats.py` | CLI: per-channel statistics (global max, local min between peaks, mean, median, std, variance) |
| `plot_chan.py` | CLI: matplotlib plots of `.chan` channels |

## Data project root

The MCP server resolves the project it analyzes from:

1. `CHAN_PROJECT_ROOT` (env var), else
2. the process working directory (`cwd`).

MCP hosts spawn local servers with `cwd` set to the project workspace, so no
env var is needed in the common case. The helper scripts are always located
relative to this repo, via `Path(__file__)`.

## CLI usage

```sh
python3 chan_stats.py <file.chan> [--channel CH ...] [--peak-distance N]
python3 plot_chan.py <file.chan> [--channels CH ...] [--output OUT.png] [--window N]
```

Paths are absolute or relative to `CHAN_PROJECT_ROOT` / `cwd`.

## MCP config

opencode (`opencode.json`) — user/OS-agnostic via `{env:HOME}`:

```json
{
  "mcp": {
    "chan-tools": {
      "type": "local",
      "command": ["python3", "{env:HOME}/Code/chan-tools/chan_mcp.py"],
      "enabled": true
    }
  }
}
```

Claude Code (`.claude/settings.json`):

```json
{
  "mcpServers": {
    "chan-tools": {
      "type": "stdio",
      "command": "bash",
      "args": ["-lc", "exec python3 \"$HOME/Code/chan-tools/chan_mcp.py\""]
    }
  }
}
```

## Requirements

`numpy`, `matplotlib`.
