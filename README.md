# 🎬 Twitch Clip Automator

[![ClawHub Skill](https://img.shields.io/badge/ClawHub-Skill-blue)](https://clawhub.ai/djc00p/twitch-clip) [![Agent Skill](https://img.shields.io/badge/Agent-Skill-blue)](#) [![Twitch Clip](https://img.shields.io/badge/Twitch-Clip-purple)](https://www.twitch.tv/djc00p23)

**A high-speed automation utility for streamers to instantly capture highlights from a live broadcast using the Twitch Clips API.**

The **Twitch Clip Automator** is a lightweight, high-performance script designed for real-time interaction. It enables streamers, moderators, or automated agents to "clip" the most recent 30–60 seconds of a live broadcast with a single command. By integrating directly with the Twitch Helix API, it bypassses the need for manual dashboard navigation, making "instant highlights" possible during high-action moments.

---

## 🚀 Features

* **⚡ Instant Capture:** One-command clipping of the most recent 30-60 seconds of a stream.
* **🛡️ Anti-Spam Protection:** Built-in 30-second cooldown logic to prevent accidental duplicate clips or API rate-limiting.
* **🤖 Agent-Ready:** Designed for seamless integration with AI agents (like ClawBot) via natural language triggers.
* **🌐 Twitch API Native:** Uses official OAuth2 authentication and the Twitch Clips API for maximum reliability.

---

## 🛠 Setup & Configuration

### 1. Twitch Developer Portal Setup

To interact with the Twitch API, you must register an application:

1. Visit the [Twitch Developer Console](https://dev.tencent.com/console/apps).
2. Create a new application.
3. **Redirect URL:** Set to `http://localhost`.
4. **Category:** Select `Other`.
5. **Credentials:** Copy your **Client ID** and securely store your **Client Secret**.

### 2. OAuth2 Authorization

You must generate an Access Token with the `clips:edit` scope. Open the following URL in your browser, replacing `YOUR_CLIENT_ID` with your actual ID:

`https://id.twitch.tv/oauth2/authorize?client_id=YOUR_CLIENT_ID&redirect_uri=http://localhost&response_type=token&scope=clips:edit`

After authorizing, you will be redirected to `localhost`. **Copy the `access_token` string from the browser's address bar.**

### 3. Environment Configuration

The script requires three critical environment variables. Add these to your shell profile (`~/.zshrc` or `~/.bashrc`) or your `.env` file:

```bash
# The ID of your Twitch Application
export TWITCH_CLIENT_ID="your_client_id"

# The Token copied from the browser redirect
export TW_ACCESS_Token="your_access_token"

# Your numeric Twitch User ID (Broadcaster ID)
export TWITCH_BROADCASTER_ID="12345678"
```

**How to find your Broadcaster ID:**
Use this helper command to query the Twitch Helix API using your current token:

```bash
curl -s -H "Authorization: Bearer $TWITCH_ACCESS_TOKEN" \
  -H "Client-Id: $TW_CLIENT_ID" \
  https://api.twitch.tv/helix/users | python3 -c "import json,sys; print(json.load(sys.stdin)['data'][0]['id'])"
```

---

## 🕹 Usage

The utility is invoked via a shell script that accepts an optional duration parameter.

**Syntax:**
`bash scripts/create_clip.sh [duration_seconds]`

**Execution Examples:**

* **Default Clip (30s):** `bash scripts/create_clip.sh`
* **Extended Clip (60s):** `bash scripts/create_clip.sh 60`

**Constraints:**

* **Minimum duration:** 30 seconds.
* **Maximum duration:** 60 seconds.
* **Live Requirement:** The stream **must** be currently live. The API will return a `404` error if the broadcaster is offline.

---

## ⏱ Cooldown Logic

To prevent API spam and redundant clips, the script enforces a **30-second cooldown** between successful executions.

* **During Cooldown:** The script will exit with `Code 2` and output: `⏳ Cooldown active — try again in Xs`.
* **Agent Instruction:** If using an AI Agent, do **not** automate retries. The agent should wait for the next user request.

---

## 📤 Response Formats

The script provides structured text outputs suitable for both terminal users and AI Agent chat responses:

| Scenario | Output Message |
| :--- | :--- |
| **Success** | `Clipped! 🎬 https://clips.twitch.tv/<clip_id>` |
| **Cooldown** | `⏳ Cooldown active — try again in Xs` |
| **Offline** | `Stream is offline — can't clip right now` |
| **Error** | `[Error Message from Twitch API]` |

---

**Original implementation by [@djc00p](https://github.com/djc00p)**
