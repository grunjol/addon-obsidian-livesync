# Obsidian LiveSync — Home Assistant App

Self-hosted Obsidian LiveSync server for Home Assistant. Syncs multiple Obsidian vaults with CouchDB in real-time without running Obsidian.

Built on the [Self-hosted LiveSync CLI](https://github.com/vrtmrz/obsidian-livesync), this app runs one daemon per vault, continuously synchronizing your markdown notes between CouchDB and the local filesystem.

## Features

- Sync multiple Obsidian vaults simultaneously
- Real-time sync via CouchDB `_changes` feed or periodic polling
- End-to-end encryption support (passphrase per vault)
- File system watching for local changes
- Graceful shutdown via Home Assistant Supervisor
- Multi-architecture support (amd64, aarch64)

## Requirements

- A CouchDB instance accessible from your Home Assistant host
- The [Self-hosted LiveSync](https://obsidian.md/plugins?id=obsidian-livesync) plugin installed in Obsidian on your devices

## Configuration

See [DOCS.md](./obsidian-livesync/DOCS.md) for full configuration reference.

## License

MIT
