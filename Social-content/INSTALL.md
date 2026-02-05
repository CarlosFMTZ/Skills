# Installation Guide

## Quick Start (5 minutes)

### 1. Install the npm package

```bash
npm install -g openclaw-social-content
```

### 2. Start the worker service

```bash
# Start in foreground (for testing)
openclaw-social-content start

# Or run in background
nohup openclaw-social-content start &
```

Verify it's running:
```bash
curl http://127.0.0.1:37779/api/health
# Should return: {"status":"ok","timestamp":"..."}
```

### 3. Install the OpenClaw extension

```bash
# Get the package location
PKG_PATH=$(npm root -g)/openclaw-social-content

# Create extension directory
mkdir -p ~/.openclaw/extensions/openclaw-social

# Copy extension files
cp $PKG_PATH/extension/index.ts ~/.openclaw/extensions/openclaw-social/
cp $PKG_PATH/extension/openclaw.plugin.json ~/.openclaw/extensions/openclaw-social/
cp $PKG_PATH/extension/package.json ~/.openclaw/extensions/openclaw-social/

# Install extension dependencies
cd ~/.openclaw/extensions/openclaw-social && npm install
```

### 4. Configure OpenClaw

Edit `~/.openclaw/openclaw.json` and add/update the plugins section:

```json
{
  "plugins": {
    "slots": {
      "social": "openclaw-social"
    },
    "allow": ["openclaw-social"],
    "entries": {
      "openclaw-social": {
        "enabled": true,
        "config": {
          "workerUrl": "http://127.0.0.1:37779",
          "autoPost": true,
          "autoAnalyze": true,
          "maxContentTokens": 4000
        }
      }
    }
  }
}
```

### 5. Restart OpenClaw

```bash
openclaw gateway restart
```

---

## Automated Install Script

For convenience, you can use the install script:

```bash
curl -fsSL https://raw.githubusercontent.com/yourusername/openclaw_social_content/main/skill/install.sh | bash
```

Or if you have the skill installed via ClawHub:
```bash
./scripts/install.sh
```

---

## Verify Installation

### Check worker status
```bash
curl http://127.0.0.1:37779/api/stats
# Should show social activity stats
```

### Check OpenClaw logs
```bash
tail ~/.openclaw/logs/*.log | grep openclaw-social
# Should show: "openclaw-social: plugin registered"
```

### Test auto-post
Send a message to your agent that triggers a social post, then check logs:
```bash
tail ~/.openclaw/logs/*.log | grep "post"
# Should show social post activity when relevant
```

---

## Troubleshooting

### Worker won't start
```bash
# Check if port is in use
lsof -i :37779

# Kill existing process
kill $(lsof -t -i:37779)

# Restart worker
openclaw-social-content start
```

### Extension not loading
```bash
# Check extension directory
ls ~/.openclaw/extensions/openclaw-social/

# Should have: index.ts, openclaw.plugin.json, package.json, node_modules/

# If missing node_modules:
cd ~/.openclaw/extensions/openclaw-social && npm install
```

### Auto-post not working
1. Check `plugins.slots.social` is set to `"openclaw-social"`
2. Restart gateway after config changes
3. Check logs for errors: `tail ~/.openclaw/logs/*.log | grep -i error`

---

## Uninstall

```bash
# Remove extension
rm -rf ~/.openclaw/extensions/openclaw-social

# Remove from openclaw.json plugins.slots.social and plugins.entries.openclaw-social

# Stop worker
kill $(lsof -t -i:37779)

# Uninstall npm package
npm uninstall -g openclaw-social-content

# Optional: remove database/config
rm -rf ~/.openclaw-social/
```