Claude Desktop Without Anthropic (AI Gateway Basics)

![GitHub stars](https://img.shields.io/github/stars/network-tocoder/free-claude-desktop-bifrost?style=for-the-badge&color=orange)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Mac-blue?style=for-the-badge)
![Cost](https://img.shields.io/badge/Cost-Zero-brightgreen?style=for-the-badge)
![Gateway](https://img.shields.io/badge/Gateway-Bifrost-teal?style=for-the-badge)

> **Run Claude Desktop's full Cowork + Code experience — powered by free models via Bifrost AI Gateway. No Anthropic subscription. No API key. Zero cost.**

---
## 🎓 Complete AI Gateway Masterclass (Free)

This repository is part of a **3-part AI Gateway series**. Each video has a matching GitHub repository with all commands, configs, and source files.

| Part | YouTube | GitHub |
|------|---------|--------|
| 🚀 **Part 1 – Claude Desktop Without Anthropic (AI Gateway Basics)** | https://www.youtube.com/watch?v=4DlNb2weD_s | https://github.com/network-tocoder/Claude-Desktop-Without-Anthropic-AI-Gateway-Basics |
| ⚡ **Part 2 – Unlimited Claude + Gemini + OpenCode with Bifrost** | https://www.youtube.com/watch?v=LOrjVQnA4EY | https://github.com/network-tocoder/Run-Claude-Code-Gemini-CLI-OpenCode-One-Gateway |
| 🔥 **Part 3 – Complete $0 AI Coding Stack (Bifrost + Claude + NVIDIA + VS Code)** | https://www.youtube.com/watch?v=LzPvQAkQ8tw | https://github.com/network-tocoder/Run-Any-AI-Model-in-VS-Code-CLI-One-Gateway |

> 📚 **Recommended:** Start with **Part 1** if you're new to AI Gateways, then continue through Parts 2 and 3 to build the complete production-ready stack.
---


## ⚡ What Is This?

Use **Claude Desktop** (Cowork + Code tabs) with **free models** by routing requests through **Bifrost AI Gateway**. Two approaches covered — cloud and local.

| Tool | Role |
|------|------|
| 🖥️ **Claude Desktop** | Anthropic's desktop app — Cowork + Code tabs |
| 🌉 **Bifrost** | Open-source AI gateway — intercepts and reroutes requests |
| 🌐 **OpenRouter** | Cloud path — access free models like Nemotron 120B |
| 💻 **LM Studio** | Local path — run models on your own machine |

---

## 🎯 How It Works

```
Claude Desktop → Bifrost Gateway → OpenRouter → Free Model (Cloud)
Claude Desktop → LM Studio → Local Model (Local)
```

Claude Desktop thinks it's talking to Anthropic. Bifrost intercepts the request, routing rule fires, and silently reroutes it to a free model. Claude Desktop has no idea.

| What You Get | What's Behind It |
|-------------|-----------------|
| Cowork tab (background agents) | Free model via OpenRouter or LM Studio |
| Code tab (visual diffs, git) | Same |
| Scheduled tasks | Same |
| Live artifacts | Same |
| 71 Claude Code tools | Same |

---

## 🖥️ Requirements

| Requirement | Detail |
|------------|--------|
| OS | Windows / Mac |
| Node.js | v18+ (for Bifrost npx install) |
| GPU | ❌ Not required (needed for larger local models) |
| Anthropic Account | ❌ Not required |
| API Key | Free — openrouter.ai |

---

## 🚀 Path 1 — Cloud Setup (Bifrost + OpenRouter)

### Step 1 — Install Claude Desktop

1. Go to [claude.com/download](https://claude.com/download)
2. Download for your OS (Windows / Mac)
3. Run the installer
4. **Do NOT sign in** — skip the login screen

---

### Step 2 — Install Bifrost

Open a terminal and run:

```bash
npx -y @maximhq/bifrost
```

Wait for the ASCII art and initialization logs. Bifrost serves UI on `http://localhost:8080`.

Open your browser → go to `http://localhost:8080` → you should see a clean empty dashboard.

> **GitHub:** [github.com/maximhq/bifrost](https://github.com/maximhq/bifrost)
> **Docs:** [docs.getbifrost.ai](https://docs.getbifrost.ai/quickstart/gateway/setting-up)

---

### Step 3 — Get OpenRouter API Key

1. Go to [openrouter.ai](https://openrouter.ai)
2. Sign up for free
3. Go to **API Keys** → **Create Key**
4. Name it, leave credit limit blank, no expiration
5. Copy the key

> **Finding free models:** Go to Models → search `:free` → browse available free models

---

### Step 4 — Add OpenRouter to Bifrost

1. In Bifrost UI → **Models** → **Model Providers**
2. Click **+ Add Provider** → select **OpenRouter**
3. Click **+ Add new key** → paste your API key
4. Name it `key` → leave Allowed Models as **All Models**
5. Save

You should see: green checkmark, Weight 1, Enabled.

---

### Step 5 — Configure Claude Desktop Third-Party Inference

1. Open Claude Desktop (don't sign in)
2. Menu bar → **Developer** → **Configure Third-Party Inference**

> If you don't see Developer in the menu bar: go to **Help** → **Troubleshooting** → **Enable Developer Mode**

3. Configure these fields:

| Field | Value |
|-------|-------|
| Connection | Gateway |
| Credential kind | Static API key |
| Gateway base URL | `http://localhost:8080/anthropic` |
| Gateway API key | `dummy-key` (any value — Bifrost handles auth) |
| Gateway auth scheme | bearer |

4. Scroll down → **Test Model Discovery** → should find 18 models
5. Click **Apply Changes** → Claude Desktop restarts

You'll see an error — "Gateway returned an error" — this is expected. We need a routing rule.

---

### Step 6 — Create Routing Rule

1. In Bifrost UI → **Models** → **Routing Rules** → **New Rule**

| Field | Value |
|-------|-------|
| Rule Name | `Opus to Free Nemotron` |
| Description | Intercepts Claude requests and routes to Nemotron free |
| Enable Rule | ON |
| Scope | Global |
| Priority | 0 |

2. **Rule Builder** — add two conditions with OR:

| # | Field | Operator | Value |
|---|-------|----------|-------|
| 1 | Model | contains | `claude` |
| OR | | | |
| 2 | Model | = | `anthropic/claude-opus-4.8` (select from dropdown) |

**CEL Preview should show:**
```
model.contains("claude") || model == "anthropic/claude-opus-4.8"
```

3. **Routing Targets:**

| Field | Value |
|-------|-------|
| Provider | OpenRouter |
| Model | `nvidia/nemotron-3-super-120b-a12b:free` |
| Weight | 1 |
| API Key | key (your configured key) |

> ⚠️ Make sure the model name includes `:free` at the end

4. **Save Rule**

---

### Step 7 — Test

1. Go back to Claude Desktop → click **Check Again** on error banner
2. Error should be gone — Cowork and Code tabs visible
3. Code tab → type: `Hello - What model are you?`
4. Response should come back — routing is working

**Verify in Bifrost:** LLM Logs → you should see requests with 100% success rate and $0 cost.

---

## 💻 Path 2 — Local Setup (LM Studio)

### Step 1 — Install LM Studio

<details>
<summary>🪟 Windows</summary>

```powershell
winget install ElementLabs.LMStudio -e
lms --version
```
</details>

<details>
<summary>🍎 macOS</summary>

Download from [lmstudio.ai](https://lmstudio.ai) and install.
```bash
lms --version
```
</details>

---

### Step 2 — Start Server & Download Model

```bash
# Start the local server
lms server start

# Browse available models
lms get

# Download a Google Gemma model
lms get "google"
# Select: google/gemma-4-e4b
```

---

### Step 3 — Load Model with Claude-Compatible Alias

```bash
lms load google/gemma-4-e4b --context-length 8000 --identifier "claude-opus-4.8"
```

> ⚠️ Claude Desktop only discovers models with `claude`, `opus`, `sonnet`, or `haiku` in the name. The `--identifier` flag aliases your local model.

**Verify:**

```bash
# Check running models
lms ps

# Test the API
curl http://localhost:1234/v1/models

# Quick chat test
lms chat claude-opus-4.8 -p "Hello, what model are you?"
```

---

### Step 4 — Connect Claude Desktop to LM Studio

1. Claude Desktop → **Developer** → **Configure Third-Party Inference**

| Field | Value |
|-------|-------|
| Connection | Gateway |
| Credential kind | Static API key |
| Gateway base URL | `http://localhost:1234` |
| Gateway API key | `lm-studio` |
| Gateway auth scheme | bearer |

2. **Test Model Discovery** → should find 1 model: `claude-opus-4.8`
3. **Apply Changes** → Save and Restart
4. Continue without signing in

---

### Step 5 — Context Window Fix

Claude Desktop sends a ~36K token system prompt. If your context is set to 8K, the model will struggle.

```bash
# Unload the model
lms unload claude-opus-4.8

# Reload with larger context
lms load google/gemma-4-e4b --context-length 40000 --identifier "claude-opus-4.8"
```

> ⚠️ Larger context requires more RAM. 40K context on CPU with 32GB RAM will work but will be slow. A GPU significantly improves performance.

---

## 🔄 Switching Between Cloud & Local

| Path | Gateway base URL |
|------|-----------------|
| Cloud (Bifrost + OpenRouter) | `http://localhost:8080/anthropic` |
| Local (LM Studio) | `http://localhost:1234` |

Switch in Claude Desktop: **Developer** → **Configure Third-Party Inference** → change Gateway base URL → **Apply Changes**.

---

## ⚠️ Important Notes

- **This is NOT Claude Opus** — you get Claude Desktop's UI and agentic framework, but the intelligence comes from whatever model you plug in (Nemotron, Gemma, etc.)
- **Free model caveats** — OpenRouter free models have daily rate limits. Some models log prompts for improvement. Don't send sensitive data
- **Local model tradeoffs** — complete privacy and no rate limits, but limited by your hardware
- **Quality varies** — the results depend entirely on the model behind the frontend, not on Claude Desktop itself

---

## 🔧 Troubleshooting

| Issue | Fix |
|-------|-----|
| Gateway returned error (500) | No routing rule configured — create one in Bifrost |
| Gateway returned error (429) | Free model rate limit hit — wait or switch model |
| Model discovery finds 0 models | Check Gateway base URL includes `/anthropic` path |
| LM Studio model not discovered | Ensure `--identifier` contains `claude` or `opus` |
| Context truncation errors | Increase `--context-length` when loading model |
| Bifrost won't start | Check if port 8080 is already in use |
| Claude Desktop stuck on sign-in | Delete config: `%APPDATA%\Claude\claude_desktop_config.json` |

---

## 🔧 Reset Everything

**Reset Bifrost:**
```powershell
# Stop Bifrost (Ctrl+C in terminal)
# Delete data
Remove-Item -Recurse -Force "$env:APPDATA\bifrost"
# Restart fresh
npx -y @maximhq/bifrost
```

**Reset Claude Desktop:**
```powershell
Remove-Item -Force "$env:APPDATA\Claude\claude_desktop_config.json"
# Restart Claude Desktop
```

**Unload LM Studio model:**
```bash
lms unload claude-opus-4.8
```

---

## 🔗 Resources

| Resource | Link |
|----------|------|
| Claude Desktop | [claude.com/download](https://claude.com/download) |
| Bifrost GitHub | [github.com/maximhq/bifrost](https://github.com/maximhq/bifrost) |
| Bifrost Docs | [docs.getbifrost.ai](https://docs.getbifrost.ai/quickstart/gateway/setting-up) |
| OpenRouter | [openrouter.ai](https://openrouter.ai) |
| LM Studio | [lmstudio.ai](https://lmstudio.ai) |
| Claude 3P Docs | [claude.com/docs/cowork/3p/overview](https://claude.com/docs/cowork/3p/overview) |
| Claude Code Gateway | [docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/llm-gateway) |

---

## 🔗 Related Videos on My Channel

| Video | Topic |
|-------|-------|
| 🎬 Free Claude Code with MiniMax M2.5 | VS Code integration, zero cost |
| 🎬 Claude Code with Gemma 4 (Local) | Run Claude Code locally, no GPU |
| 🎬 Free Claude Code with Nvidia NIM | Cloud free coding alternative |
| 🎬 Claude Code Complete Tutorial | Master Claude Code A–Z |

---

## 🔗 Links

[![YouTube](https://img.shields.io/badge/YouTube-NetworkCoder-red?style=for-the-badge&logo=youtube)](https://www.youtube.com/@NetworkCoder)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=for-the-badge&logo=github)](https://github.com/network-tocoder)
[![Bifrost](https://img.shields.io/badge/Bifrost-Gateway-teal?style=for-the-badge)](https://github.com/maximhq/bifrost)
[![OpenRouter](https://img.shields.io/badge/OpenRouter-Free_Models-purple?style=for-the-badge)](https://openrouter.ai)
[![LM Studio](https://img.shields.io/badge/LM_Studio-Local_AI-blue?style=for-the-badge)](https://lmstudio.ai)

---

<p align="center">
  <strong>Same Cockpit. Free Engine. Zero Cost.</strong><br/>
  <em>Built with Claude Desktop + Bifrost + OpenRouter + LM Studio</em>
</p>
