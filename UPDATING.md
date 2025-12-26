# Updating the Model Catalog

## Overview

The catalog sync service supports two modes:

- **Remote (Production)**: Syncs from GitHub repository
- **Local (Development)**: Reads from local filesystem

## How to Update the Catalog

### 1. Add New Providers or Models

Edit the following files in the `model-catalog/` directory:

- `providers.yaml` - Add new provider configurations
- `models.yaml` - Add new model definitions

### 2. Update the Version

**IMPORTANT**: After making changes, you MUST increment the version number in `version.txt`:

```bash
# Current version
cat model-catalog/version.txt  # e.g., 1.0.0

# Increment to trigger sync
echo "1.0.1" > model-catalog/version.txt
```

The sync service compares the version in `version.txt` with the cached version to detect updates.

### 3. Trigger a Sync

#### Option A: Manual Trigger (Recommended for Development)

Use the admin UI or API to trigger a manual sync:

```bash
# Via API
curl -X POST http://localhost:8089/api/v1/catalog/sync
```

Or use the "Sync Now" button in the Admin UI's Catalog settings.

#### Option B: Wait for Auto-Sync (Production)

In production mode, the service automatically syncs every 24 hours if `enable_auto_sync: true`.

In development mode, auto-sync is **disabled by default** to prevent unnecessary checks.

### 4. Verify the Update

Check the catalog status:

```bash
# Via API
curl http://localhost:8089/api/v1/catalog/status
```

Or check the Admin UI's Catalog page to see:

- Current version
- New models count
- New providers count
- Last sync time

## Configuration

**Note**: Catalog sync configuration is **internal** and automatically configured based on your environment. You don't need to add any catalog configuration to your config files.

### How It Works

- **Development Mode**: Automatically detected (when `go.mod` exists or `MF_ENV=development`)
  - Uses `source: "local"` to read from the local `model-catalog/` directory
  - Auto-sync is disabled (manual trigger only)
- **Production Mode**: Default for deployed instances
  - Uses `source: "remote"` to sync from GitHub
  - Auto-sync runs every 24 hours

### Environment Variables (Optional)

You can override the auto-detection with environment variables:

```bash
# Force development mode
export MF_ENV=development

# Override catalog source directly
export MF_CATALOG_SOURCE=local  # or "remote"
export MF_CATALOG_LOCAL_PATH=model-catalog
```

## Troubleshooting

### Changes Not Appearing?

1. **Check the version number** - Did you increment `version.txt`?
2. **Trigger manual sync** - Use the API or UI to force a sync
3. **Check logs** - Look for sync errors in server logs
4. **Verify source mode** - Ensure `source: "local"` in development

### Version File Missing?

Create it:

```bash
echo "1.0.0" > model-catalog/version.txt
```

### Local Path Issues?

The service looks for files in this structure:

```
model-catalog/
├── version.txt      # Required
├── models.yaml      # Required
├── providers.yaml   # Required
└── changelog.yaml   # Optional
```

## Publishing to Production

When ready to publish your changes:

1. Commit your changes (including the version bump)
2. Push to the main branch
3. Production deployments will automatically sync from GitHub
4. Or trigger a manual sync in production

## Version Numbering

Follow semantic versioning:

- **Major** (1.0.0 → 2.0.0): Breaking changes
- **Minor** (1.0.0 → 1.1.0): New features/providers
- **Patch** (1.0.0 → 1.0.1): Bug fixes, small updates
