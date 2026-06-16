# Obsidian LiveSync

## Overview

Self-hosted Obsidian LiveSync server that syncs multiple Obsidian vaults with CouchDB in real-time without running Obsidian.

Built on the [Self-hosted LiveSync CLI](https://github.com/vrtmrz/obsidian-livesync), it runs as a background service in Home Assistant, keeping your vaults continuously synchronized via CouchDB.

## Installation

1. Navigate to the Home Assistant App Store
2. Click the three dots in the upper right corner and select "Repositories"
3. Add the repository URL: `https://github.com/grunjol/hassio-repository`
4. Find the "Obsidian LiveSync" app in the list and click "Install"

## Configuration

### Option: `couchdb_uri`

The full URL to your CouchDB instance. Must be accessible from the Home Assistant host.

Example: `http://192.168.1.100:5984`

### Option: `couchdb_user`

CouchDB admin username. Required for replication.

### Option: `couchdb_password`

CouchDB admin password.

### Option: `polling_interval`

Seconds between sync polls. Default is `0`, which uses the CouchDB `_changes` feed for near-real-time sync. Set to a positive value (e.g. `60`) if your CouchDB instance does not support long-lived HTTP connections.

### Option: `vaults`

A list of vaults to sync. Each vault entry requires:

- **`name`** — Unique identifier for this vault. Directories are created at:
  - `/share/obsidian-livesync/{name}/` for the vault files (markdown notes)
  - `/config/data/{name}/` for the PouchDB database and settings
- **`dbname`** — CouchDB database name for this vault (must be unique per vault)
- **`passphrase`** — Encryption passphrase for this vault. Must match the passphrase configured in the Self-hosted LiveSync Obsidian plugin on your devices.

Example configuration:

```yaml
couchdb_uri: "http://192.168.1.100:5984"
couchdb_user: "admin"
couchdb_password: "your-secure-password"
polling_interval: 0
vaults:
  - name: "personal"
    dbname: "obsidian-personal"
    passphrase: "your-encryption-passphrase"
  - name: "work"
    dbname: "obsidian-work"
    passphrase: "another-passphrase"
```

## How it works

1. On startup, the app generates a `.livesync/settings.json` file per vault
2. A `livesync-cli daemon` process is launched per vault
3. Each daemon runs an initial mirror scan, then continuously syncs:
   - **CouchDB → vault files**: via the `_changes` feed or polling
   - **Vault files → CouchDB**: via file system watching (chokidar)
4. All daemons shut down gracefully when the app is stopped

## Connecting Obsidian on your devices

Install the [Self-hosted LiveSync](https://obsidian.md/plugins?id=obsidian-livesync) plugin in Obsidian and configure it with:

- **Remote CouchDB URI**: Same as `couchdb_uri` above
- **Database name**: Same as `dbname` for the vault
- **Username/Password**: Same as `couchdb_user` / `couchdb_password`
- **Passphrase**: Same as `passphrase` for the vault
- **End to End Encryption**: Enabled

Use the plugin's "Setup URI" feature to easily replicate the configuration across devices.

## Data locations

| Data | Path |
|------|------|
| Vault files (markdown) | `/share/obsidian-livesync/{name}/` |
| PouchDB database | `/config/data/{name}/` |
| Settings per vault | `/config/data/{name}/.livesync/settings.json` |
| Ignore file per vault | `/share/obsidian-livesync/{name}/.livesync/ignore` |

## Sync ignore

A `.livesync/ignore` file is created automatically in each vault directory. It excludes Obsidian workspace files and trash by default. Edit this file to customize which files are excluded from sync. Patterns follow [minimatch](https://github.com/isaacs/minimatch) glob syntax. Restart the app after editing.

## Troubleshooting

**CouchDB connection issues**: Ensure your CouchDB instance is reachable from the Home Assistant host. The app does not run a CouchDB server — you must have one available separately.

**Sync not working**: Check the app logs in Home Assistant. Verify that the CouchDB credentials, database names, and passphrases match between the app and your Obsidian plugin configuration.

## Support

- [Self-hosted LiveSync CLI Documentation](https://github.com/vrtmrz/obsidian-livesync/tree/main/src/apps/cli)
- [Self-hosted LiveSync Plugin](https://obsidian.md/plugins?id=obsidian-livesync)
- [App GitHub Repository](https://github.com/grunjol/addon-obsidian-livesync)
