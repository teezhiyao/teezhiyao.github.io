---
layout: single
title: "Multi-Profile Telegram Bot Setup with Hermes Agent and Obsidian"
date: 2025-05-16
categories: [setup, automation, homelab]
tags: [hermes-agent, obsidian, raspberry-pi, telegram, git, multi-agent]
author_profile: true
---

This post documents my setup: a Hermes Agent running on a Raspberry Pi with multiple Telegram bot profiles, each assigned to its own topic within a single supergroup. The entire system integrates with an Obsidian vault for persistent note storage and git-backed sync.

## Hardware Foundation

**Raspberry Pi 4** running Raspberry Pi OS Lite (no desktop):

```bash
# After flashing Pi OS Lite to SD card, enable SSH before first boot
touch /bootfs/ssh

# First login
ssh pi@<ip-address>
sudo raspi-config
# → Interface Options → SSH: Enable
# → Localisation → Timezone: Asia/Singapore

# Static IP via router DHCP reservation recommended for reliable access
```

Essential packages:

```bash
sudo apt update && sudo apt install -y git curl vim htop net-tools
```

## Hermes Agent Installation

```bash
# Clone and install
git clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
cd ~/.hermes/hermes-agent
python3 -m venv venv
source venv/bin/activate
pip install -e .

# Initial setup — configures LLM provider and creates ~/.hermes/.env
hermes setup
```

## Obsidian Vault Integration

The Hermes vault is at `~/obsidian/Hermes/`. The agent accesses notes directly through file system tools. The vault uses git for sync:

```bash
# Clone the vault
git clone --recurse-submodules git@github.com:teezhiyao/obsidian.git ~/obsidian
```

### Sync Script

Sync runs via a script at `~/obsidian/sync.sh`:

```bash
#!/bin/bash
cd ~/obsidian
git pull --recurse-submodules
git add -A
git commit -m "Auto-sync $(date +%Y-%m-%d_%H:%M)"
git push --recurse-submodules=on-demand
```

### Auto-Sync on a Schedule

The sync script can be run on a fixed interval using cron. On the Raspberry Pi:

```bash
crontab -e
```

Add a line to run every 30 minutes:

```cron
*/30 * * * * /home/pi/obsidian/sync.sh
```

Adjust the interval based on your needs — every 15 minutes for active writing, hourly for casual use. Note that frequent writes can wear out SD cards on a Pi, so consider running sync on a schedule that balances freshness with longevity.

### Syncing on Android (Termux + Obsidian Git)

To sync your Obsidian vault on an Android device:

1. **Install Termux** from F-Droid (recommended over the Play Store version — it's more up to date)
2. **Install dependencies:**
   ```bash
   pkg update && pkg install git openssh
   ```
3. **Set up SSH keys** (same keys you use on other devices):
   ```bash
   ssh-keygen -t rsa -b 4096
   cat ~/.ssh/id_rsa.pub
   ```
   Add the public key to GitHub under Settings → SSH and GPG keys.
4. **Clone the vault:**
   ```bash
   git clone --recurse-submodules git@github.com:teezhiyao/obsidian.git ~/storage/shared/Obsidian
   ```
5. **Grant storage permissions:**
   ```bash
   termux-setup-storage
   ```
   This makes your vault accessible from Obsidian on Android.
6. **Install Obsidian Git plugin** — Open Obsidian, go to Settings → Community plugins → Browse, search for "Obsidian Git". Configure it to run the backup/push on a schedule (e.g. every 60 minutes) or manually via command palette.

The combination of Termux for the git backend and the Obsidian Git plugin for the UI means your Android device stays in sync with the same vault — write a note on your phone during a commute, and it's on your Pi by the time you're home.

## Multi-Profile Telegram Bot Setup

### Architecture

| Component | Details |
|-----------|---------|
| Host | Raspberry Pi 4 (Raspberry Pi OS Lite) |
| LLM | DeepSeek v4 Flash (primary), GPT-4.1 / Copilot (fallback) |
| Interface | Telegram (direct messages + supergroup topics) |
| Profiles | default (personal), work, qa-planner, blog |

### Profile Layout

Each Hermes profile is a separate gateway instance with its own Telegram bot token, configuration, and personality. Profiles live under `~/.hermes/profiles/<name>/`.

```
~/.hermes/
├── .env                          # Root env (not read by profile gateways)
├── auth.json                     # Global credential pool
├── config.yaml                   # Root config
└── profiles/
    ├── default/                  # Personal assistant
    │   ├── config.yaml
    │   ├── .env                  # TELEGRAM_BOT_TOKEN + API keys
    │   └── auth.json             # Credential pool
    ├── work/
    │   ├── config.yaml
    │   ├── .env
    │   └── auth.json
    ├── qa-planner/
    │   └── ...
    └── blog/
        └── ...
```

### DM vs Group Group Strategy

Frequently used bots run via **direct messages** — the bot responds to DMs without any topic filtering. Less frequently used bots are placed in a **dedicated supergroup with Topics enabled**, each restricted to its own topic.

This avoids cluttering your DM list with bots you rarely talk to, while keeping your primary bot easily accessible via DM.

**Which bots use DM vs group:**

| Profile | Interaction Mode | Rationale |
|---------|-----------------|-----------|
| default (Personal) | DM | Daily use, quick questions, personal assistant |
| work | DM | Regular work queries |
| qa-planner | Group topic (QA) | Used less often, specialized persona |
| blog | Group topic (Blog) | Used sporadically, content-focused |

### Creating a Bot via @BotFather

```text
1. Open @BotFather on Telegram
2. Send /newbot
3. Enter display name (e.g. "BlogBot")
4. Enter username ending in "bot" (e.g. "YourBlogBot")
5. Copy the token (format: 1234567890:ABCdefGHIjklmNOPqrstUVwxyz)
6. (Optional) Set profile picture: /setuserpic
7. Disable privacy mode: /mybots → select bot → Bot Settings → Group Privacy → Turn off
```

Disabling privacy mode is required for the bot to read all messages in a group, not just replies to its own messages.

### Creating a New Profile

Clone an existing working profile as a template:

```bash
cp -r ~/.hermes/profiles/work ~/.hermes/profiles/<new-profile-name>
```

**Critical — shared API keys in `.env`:**

Each profile gateway reads **only its own `.env` file**. The global `~/.hermes/.env` is never read by profile gateways. Verify that shared API keys are present:

```bash
grep -E '^(DEEPSEEK_|COPILOT_|OPENROUTER_|FIRECRAWL_)' ~/.hermes/profiles/<new-profile-name>/.env
```

If missing, copy from a working profile:

```bash
grep -E '^(DEEPSEEK_|COPILOT_|ANTHROPIC_|OPENROUTER_|FIRECRAWL_)' \
  ~/.hermes/profiles/work/.env >> ~/.hermes/profiles/<new-profile-name>/.env
```

**Set the Telegram bot token:**

```bash
# Replace with the token from @BotFather
sed -i 's/TELEGRAM_BOT_TOKEN=.*/TELEGRAM_BOT_TOKEN=<your-token>/' \
  ~/.hermes/profiles/<new-profile-name>/.env

# Verify the token works
curl -s "https://api.telegram.org/bot<your-token>/getMe"
```

**Create SOUL.md for personality:**

```markdown
# SOUL

You are a [role]. [One-sentence description of what you do].
```

**Set up a skill for new-profile creation:**

The `telegram-topic-setup` skill at `~/.hermes/skills/autonomous-ai-agents/telegram-topic-setup/SKILL.md` automates the entire process:

```bash
# Load the skill in a Hermes chat session:
# /skill telegram-topic-setup
# Then tell the agent: "Create a new profile called <name> with bot token <token>"
```

The skill documents:
- Profile cloning steps
- Credential restoration procedures
- Topic routing configuration
- Gateway installation and startup
- Common troubleshooting scenarios

To create a new skill:

```bash
hermes skill new <skill-name> --description "<description>"
# Or create ~/.hermes/skills/<category>/<skill-name>/SKILL.md manually
```

## Topic Routing Setup

### Enabling Forum Mode

1. Create a Telegram supergroup
2. Go to Group Settings → Topics → **Enable**
3. Create topics for each profile category

### Finding Thread IDs

After adding bots to the group with privacy mode off:

1. Send a test message in each topic
2. Check the gateway logs for the session batch key:

```bash
grep "agent:main:telegram:group:" ~/.hermes/profiles/<name>/logs/gateway.log | head -5
```

The thread ID appears in the format:
```
agent:main:telegram:group:-1003961507518:{thread_id}
```

The General topic always has `thread_id=1`.

### Configuring ignored_threads

Each profile's `config.yaml` needs `allowed_chats` and `ignored_threads` under the `telegram` section:

```yaml
telegram:
  reactions: false
  allowed_chats: '-1003961507518'
  ignored_threads: '1,4,5,75'   # Comma-separated thread IDs to ignore
```

The `ignored_threads` list contains every thread ID EXCEPT the bot's own topic. When a message arrives from an ignored thread, it is silently dropped. DMs are not affected.

**Example — 4-bot setup:**

| Profile | Topic | thread_id | ignored_threads | Ignores |
|---------|-------|-----------|----------------|---------|
| default | Personal | `41` | `1,4,5,75` | General, Work, QA, Blog |
| work | Work | `4` | `1,5,41,75` | General, QA, Personal, Blog |
| qa-planner | QA | `5` | `1,4,41,75` | General, Work, Personal, Blog |
| blog | Blog | `75` | `1,4,5,41` | General, Work, QA, Personal |

**When adding a new bot, update ALL existing profiles' `ignored_threads` to include the new topic's thread ID.** Otherwise existing bots respond in the new topic.

### Starting the Gateway

```bash
# Using Hermes CLI
hermes gateway start --profile <profile-name>

# Or via systemctl directly (avoids CLI timeout)
systemctl --user start hermes-gateway-<profile-name>.service

# Verify
hermes gateway status
# ✓ default — PID 13262
# ✓ work — PID 14151
# ✓ qa-planner — PID 14172
# ✓ blog — PID 15433
```

## Session Management

Each topic maintains its own conversation history, keyed by:

```
agent:main:telegram:group:{chat_id}:{thread_id}
```

- Topics start with independent contexts
- Memory (user preferences, saved facts) is shared across all sessions within the same profile
- The bot auto-resumes the correct session when you message in the same topic again

## Sync Flow

```
Obsidian (any device) → git commit + push → GitHub → RPi sync script → local vault on RPi → Hermes reads files
```

The RPi sync script pulls changes hourly on manual trigger. The sync script also commits and pushes any changes Hermes makes to the vault (notes, task updates, etc.).

## Key Commands Reference

| Task | Command |
|------|---------|
| Create bot | @BotFather → `/newbot` |
| Disable privacy | @BotFather → `/mybots` → select bot → Bot Settings → Group Privacy → Off |
| Clone profile | `cp -r ~/.hermes/profiles/<src> ~/.hermes/profiles/<dst>` |
| Start gateway | `hermes gateway start --profile <name>` |
| Check status | `hermes gateway status` |
| View logs | `tail -f ~/.hermes/profiles/<name>/logs/gateway.log` |
| Find thread ID | `grep 'group:-1003961507518:' ~/.hermes/profiles/<name>/logs/gateway.log` |
| Sync vault | `cd ~/obsidian && ./sync.sh` |
| Verify bot token | `curl -s "https://api.telegram.org/bot<token>/getMe"` |

---

## Optional: Sub-Repo Pattern for Modular Content

Separate git repos can live inside the vault for modular management. For example, this blog lives at `Hermes/Personal/Blog/` as a standalone Jekyll site with its own GitHub Pages deployment:

```bash
# The blog has its own .git repo nested inside the main vault
ls ~/obsidian/Hermes/Personal/Blog/.git
# It's initialized separately, not as a git submodule of the vault
```

This allows the blog profile agent to edit blog content while personal notes stay in their own repository. Other candidates for this pattern include project documentation, configuration files, or per-project repos.

---

## FAQ

### Credential Pool is Empty After Cloning a Profile

**Problem:** After cloning a profile with `cp -r ~/.hermes/profiles/work ~/.hermes/profiles/<name>`, the new profile's `auth.json` may have empty credential pools. The gateway starts but can't authenticate with any LLM provider.

**Fix:** Copy the global credential pool to the new profile:

```bash
cp ~/.hermes/auth.json ~/.hermes/profiles/<new-profile-name>/auth.json
```

Then restart the gateway:

```bash
hermes gateway restart --profile <new-profile-name>
```

### Starting Hermes Gateway on System Boot

To have your Hermes gateway profiles start automatically when the Raspberry Pi boots up:

**Step 1 — Enable user linger** (so services run even when no one is SSH'd in):

```bash
sudo loginctl enable-linger $USER
```

**Step 2 — Install each profile as a systemd service:**

```bash
source ~/.hermes/hermes-agent/venv/bin/activate
hermes --profile <name> gateway install
```

This creates a systemd user service at `~/.config/systemd/user/hermes-gateway-<name>.service`.

**Step 3 — Enable the service to start on boot:**

```bash
systemctl --user enable hermes-gateway-<name>.service
```

**Step 4 — Start it now:**

```bash
systemctl --user start hermes-gateway-<name>.service
```

Repeat steps 2–4 for each profile (default, work, qa-planner, blog, etc.).

**Verify all are running:**

```bash
systemctl --user status hermes-gateway-*.service
```

### Prompt Template for Setting Up a New Bot Profile

If you're using Hermes Agent, you can ask your bot to create a new profile by loading the relevant skill and describing what you need. Here's a reusable prompt:

> Load the `telegram-topic-setup` skill and create a new Hermes profile called `<name>`. Use the Telegram bot token `<token>` from @BotFather. Clone from the `work` profile. Set it up with topic routing in the existing supergroup and configure `ignored_threads` so it only responds in its own topic.

You can save this as a note in your Obsidian vault and paste it whenever you need a new bot. The agent will:
1. Clone the profile from the source
2. Restore API keys from the source `.env`
3. Set the Telegram bot token
4. Copy the credential pool
5. Create a `SOUL.md` with the role description
6. Configure topic routing and `ignored_threads`
7. Start the gateway
