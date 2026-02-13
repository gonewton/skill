# newton monitor

## Purpose
Launches a terminal UI that streams every active ailoop channel (`<project>/<branch>`) from the workspace, groups messages by project/branch, and exposes a queue for answering questions or approving/denying authorizations without leaving the terminal.

## Configuration
- Newton discovers the workspace root by walking up from the current directory until it finds a directory that contains `.newton`.
- It looks under `.newton/configs/` for the first `.conf` file (alphabetical) that defines both `ailoop_server_http_url` and `ailoop_server_ws_url`. If `.newton/configs/monitor.conf` exists and defines both keys, it is preferred.
- When either URL needs to be overridden for the current run, use `--http-url` or `--ws-url`.

## Options
- `--http-url <URL>`: Override the ailoop HTTP base URL for this session.
- `--ws-url <URL>`: Override the ailoop WebSocket URL for this session.

## Example Invocation
```bash
newton monitor
```

## Keybindings
- `q` / `Ctrl+C`: Quit the monitor UI.
- `Tab` / `n` / `Down` / `j`: Focus the next pending queue item.
- `Up` / `k`: Focus the previous queue item.
- `Enter` / `a`: Answer the focused question (enter text) or treat authorization as approve.
- `d`: Deny the focused authorization.
- `/`: Start typing a project or `project/branch` filter for the stream view.
- `V`: Toggle between tiles and list stream layouts.
- `Q`: Toggle the dedicated queue tab that consumes the full screen.
- `?`: Show the help overlay with keybindings.
- `Esc`: Cancel the current input/filter/help UX or exit the queue tab.

## Tips
- Blocking messages (questions and authorizations) appear in the queue automatically. Approvals/denies post to `/api/v1/messages/:id/response` and the response circulates back through the stream when successful.
- Near-timeout alerts flash the queue entry when a pending item is about to expire; move focus with `n`/`Tab` and act quickly.
- The default stream layout is tiles; switch to list (`V`) for a single column, scrollable view when you want a chronological feed.
