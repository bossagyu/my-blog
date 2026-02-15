+++
title = 'How to Update Visual Studio Code'
description = 'A complete guide to updating Visual Studio Code on Mac. Covers GUI updates, Homebrew command-line updates, auto-update settings, and troubleshooting tips.'
date = 2025-07-27T18:12:16+09:00
draft = true
categories = ['Engineering']
tags = ['VSCode', 'update']
+++

## Overview

This article explains how to update Visual Studio Code (VSCode) on Mac.
In addition to manual GUI updates, it also covers command-line updates and auto-update settings.

## Updating via GUI

1. Launch VSCode.
2. Go to Code → "Check for Updates".

![Check for Updates](image.png)

When the update starts, you will see a screen like this:

![Downloading update](image-1.png)

Once the download is complete, "Restart to Update" will be displayed.

After restarting, click "About Visual Studio Code" to confirm that the version has been updated.

![Version confirmation screen](image-3.png)

## Updating via Command Line

If you installed VSCode via Homebrew, you can update it from the terminal.

### Check the current version

```bash
code --version
```

### Update with Homebrew

```bash
brew upgrade --cask visual-studio-code
```

If VSCode was not installed via Homebrew, first remove the existing VSCode from your Applications folder, then install it via Homebrew.
Your settings and extensions can be restored via account sync, so removing it is safe.

```bash
brew install --cask visual-studio-code
```

After this, you can use `brew upgrade --cask visual-studio-code` for future updates.

## Auto-Update Settings

VSCode has an `update.mode` setting that controls update behavior.
Open Settings (`Cmd + ,`) and search for `update mode` to change it.

| Value | Behavior |
|-------|----------|
| `default` | Automatically downloads and installs updates in the background |
| `manual` | Checks for updates but requires manual installation |
| `start` | Only checks for updates when VSCode starts |
| `none` | Completely disables automatic updates |

To configure directly in settings.json:

```json
{
    "update.mode": "default"
}
```

Unless you have a specific reason, `default` is the recommended setting.

## Troubleshooting

### "Check for Updates" shows no updates available

Auto-update may be disabled. Check that `update.mode` is not set to `none` in your settings.

### Update download fails

This may be caused by proxy or firewall settings. Check the following configuration:

```json
{
    "http.proxy": "http://proxy.example.com:8080",
    "http.proxyStrictSSL": false
}
```

### Extensions stop working after update

This may be due to extension compatibility issues. Try the following steps:

1. Open the Command Palette (`Cmd + Shift + P`)
2. Run "Extensions: Disable All Installed Extensions"
3. Restart VSCode
4. Re-enable extensions one by one to identify the problematic one

### If the issue persists

Reinstalling VSCode may resolve the problem.

```bash
# If installed via Homebrew
brew reinstall --cask visual-studio-code
```

If you sync your settings via a GitHub or Microsoft account, your configuration and installed extensions will be automatically restored after reinstallation.

## Summary

This article covered how to update VSCode.

- **GUI**: Code → "Check for Updates" — a few clicks to complete
- **Command line**: `brew upgrade --cask visual-studio-code` for quick updates
- **Auto-update**: Control behavior with the `update.mode` setting

By updating regularly, you can take advantage of new features and security patches.
After updating, always check the version information to confirm the update was successful.
