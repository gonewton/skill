# newton monitor

## Purpose

Terminal UI that streams ailoop channels, surfaces blocking questions and authorizations in a queue, and posts responses back to the ailoop HTTP API.

## Configuration

Newton discovers the workspace root by walking up until it finds `.newton/`.

Monitor needs **both** HTTP and WebSocket URLs. Resolution order:

1. CLI: `--http-url` and `--ws-url` (partial overrides merge with config when the other value comes from a file).
2. `.newton/configs/monitor.conf` if present.
3. Other `.conf` files in `.newton/configs/`, sorted by filename, until both `ailoop_server_http_url` and `ailoop_server_ws_url` are set.

See [configuration.md](configuration.md) for key names. `ailoop_ws_url` alone is **not** used by Newton; set the server pair or pass CLI URLs.

## Options

- `--http-url <URL>`: ailoop HTTP base URL for this session.
- `--ws-url <URL>`: ailoop WebSocket URL for this session.
- `--backend`: Also start Newton's HTTP API (`newton serve`) alongside the monitor (see `newton monitor --help`).

## Example

```bash
newton monitor

newton monitor --http-url http://127.0.0.1:8081 --ws-url ws://127.0.0.1:8080
```

## UI

Use `?` in the TUI for keybindings (filtering, queue focus, layout toggles, quit). Details vary by Newton version; prefer on-screen help over stale prose here.
