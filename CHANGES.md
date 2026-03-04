# Changes from upstream (southleft/figma-console-mcp)

This fork adds one change: **periodic port rescanning** in the Desktop Bridge plugin.

## Problem

The upstream plugin scans ports 9223–9232 only once on startup. When a new Claude Code
session starts later (binding to a new port), the plugin never discovers it. Users must
manually close and reopen the plugin to trigger a rescan.

## Fix

Added a `setInterval(wsScanAndConnect, 5000)` to `figma-desktop-bridge/ui.html`. Every
5 seconds, the plugin probes unconnected ports for new MCP servers. The existing
`wsScanAndConnect()` function already:

- Guards against overlapping scans (`isScanning` flag)
- Skips ports with active connections (`isPortConnected` check)
- Resets backoff counters when at least one server is connected

This means the periodic rescan is safe, lightweight, and has no effect on existing
connections.

## Switching back to upstream

If upstream merges this behavior, update your MCP config to use `figma-console-mcp@latest`
instead of this fork, and re-import the upstream plugin manifest in Figma.
