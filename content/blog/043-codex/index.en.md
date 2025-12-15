+++
title = 'How to Use Codex CLI'
date = 2025-10-12T10:00:00+09:00
draft = false
categories = ['Engineering']
tags = ['Codex', 'CLI', 'AI', 'Tools']
+++

## Overview
This article summarizes how to access Codex from your local environment via the command line with Codex CLI. It covers installation, sign-in methods, command execution examples, and common commands.

## How to Install Codex CLI
If you already have Node.js, you can install it via npm:

```bash
npm install -g codex-cli
```

If you use Homebrew, you can also install it as follows:

```bash
brew tap codexcli/tap
brew install codex
```

After installation, run the following and confirm the version is displayed:

```bash
codex --version
```

## How to Sign In
Codex CLI requires authentication on the first run. You can sign in with either an API key or your ChatGPT account, depending on your use case.

### Sign in with an API key
```bash
codex login --token <YOUR_API_KEY>
```

You can also store it in an environment variable to avoid typing it every time:

```bash
export CODEX_API_KEY=<YOUR_API_KEY>
```

### Sign in with your ChatGPT account
If you prefer the browser-based flow, omit the token and run `codex login`:

```bash
codex login
```

This opens a browser and shows the ChatGPT sign-in screen. Sign in with your email, Google, or Apple account, grant access, and the token will be saved when you return to the CLI.

After signing in, run the following command in either case. If your account information appears, authentication succeeded.

```bash
codex whoami
```

## How to Run Codex (with examples)
### Generate code with a one-off prompt

```bash
codex run "Write FizzBuzz in Python"
```

### Summarize while attaching a file as context

```bash
codex run "Summarize this file" --file README.md
```

### Try interactive chat mode

```bash
codex chat
```

In chat mode, you can enter prompts to receive responses in sequence. Press `Ctrl + D` to exit.

#### Slash commands in chat
In chat mode, type a `/` prefix to perform helper actions.

| Command | Purpose |
| --- | --- |
| `/help` | Display available commands |
| `/new` | Reset the conversation history and start a new thread |
| `/file <path>` | Attach the specified file as context |
| `/exit` | Exit chat |

## How to Use the Commands
Here are the main commands:

| Command | Description |
| --- | --- |
| `codex login --token <API_KEY>` | Sign in with an API key |
| `codex login` | Start browser authentication with your ChatGPT account |
| `codex whoami` | Check the currently signed-in account |
| `codex run "<prompt>"` | Send a one-off prompt and get the result |
| `codex run "<prompt>" --file <path>` | Run with a file included as context |
| `codex chat` | Use interactive chat mode |
| `codex history` | Review recent execution history |

## Summary
Once installed and signed in, you can immediately start using Codex from the command line. Knowing the frequently used commands helps you quickly generate scripts or summarize files.
