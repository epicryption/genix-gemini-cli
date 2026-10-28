---
name: agent-tui
description: >
  Main Agents: Do NOT use this skill directly. If you need to test the TUI, invoke the `tui_tester` subagent.
  Drive terminal UI (TUI) applications programmatically for testing, automation, and inspection.
  Use when: automating CLI/TUI interactions, regression testing terminal apps, or verifying interactive behavior.
  Also use when: user asks "what is agent-tui", "what does agent-tui do", "demo agent-tui", "show me agent-tui", "how does agent-tui work", or wants to see it in action.
---

## 🚨 CRITICAL: macOS Daemon Workaround & Genix Gemini CLI Usage 🚨

When using `agent-tui` in this macOS environment, the default background daemonization process crashes, causing `Connection refused (os error 61)` errors.

**You MUST start the daemon manually shielded from TTY hangups before running any `agent-tui` commands.** Using `nohup` is insufficient; you must use `tmux` to provide a fully isolated pseudo-terminal.

To support parallel runs, **only restart the daemon if it is not currently running:**

```bash
# Check if daemon is alive, start it in tmux if it is not
if ! agent-tui sessions >/dev/null 2>&1; then
  tmux kill-session -t agent-tui 2>/dev/null || true
  agent-tui daemon stop 2>/dev/null || true
  rm -f /tmp/agent-tui*
  tmux new-session -d -s agent-tui 'agent-tui daemon start --foreground > /tmp/agent-tui-daemon.log 2>&1'
  sleep 1
fi