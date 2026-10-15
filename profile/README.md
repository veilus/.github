<p align="center">
  <img src="https://veilus.io/favicon.svg" width="80" alt="Veilus logo" />
</p>

<h1 align="center">Veilus</h1>

<p align="center">
  <strong>Anti-detect browser your AI assistant drives over MCP.</strong>
</p>

<p align="center">
  <a href="https://veilus.io/?utm_source=github&utm_medium=org_profile">Website</a> ·
  <a href="https://veilus.io/download/">Download</a> ·
  <a href="https://docs.veilus.io">Docs</a> ·
  <a href="https://veilus.io/developers/">MCP</a> ·
  <a href="https://t.me/veilusbrowser">Telegram</a> ·
  <a href="https://x.com/veilusbrowser">X</a>
</p>

---

Veilus is a desktop app for running many accounts on one computer. Each profile gets its own fingerprint and proxy (HTTP or SOCKS5) and runs on a Chromium build we patch ourselves.

Automation is built in. Connect Claude Code, Cursor or any MCP client, and your assistant can create profiles, drive a running page, write and test Playwright scripts, run them across profiles and schedule them. A script added over MCP runs on at most 3 profiles until you approve it in the app, and the local API only listens on your machine.

- **Platforms:** Windows 10/11 (x64) and macOS 13.5+ (Apple Silicon). No Linux yet.
- **Your data:** profiles stay on your machine. Veilus Sync is optional and copies them to a Git repository or Google Drive folder you choose.
- **Price:** free for 5 profiles on one device. MCP, the local API, scripts and schedules need a paid plan; there is a 7-day Pro trial in the app. See [pricing](https://veilus.io/pricing/).

## Open source

The app and the browser engine are closed source. These parts are open:

| Repository | What it is | Install |
|---|---|---|
| [sdk](https://github.com/veilus/sdk) | Launch Veilus profiles from Node and Python and drive them with Playwright | `npm install veilus playwright-core` · `pip install "veilus[playwright]"` |
| [claude-plugin](https://github.com/veilus/claude-plugin) | Claude Code plugin: run a browser-automation job over MCP from one request | `claude plugin marketplace add veilus/claude-plugin` · `claude plugin install veilus@veilus` |
| [fingerprint-generator](https://github.com/veilus/fingerprint-generator) | Rust library that generates browser fingerprints from a Bayesian network | [`veilus-fingerprint`](https://crates.io/crates/veilus-fingerprint) |
| [human-input](https://github.com/veilus/human-input) | Human-like mouse paths, wheel scrolling and typing rhythm, in Rust | [`veilus-human-input`](https://crates.io/crates/veilus-human-input) |
| [docs](https://github.com/veilus/docs) · [website](https://github.com/veilus/website) · [blog](https://github.com/veilus/blog) | Sources of docs.veilus.io, veilus.io and blog.veilus.io | |
| [releases](https://github.com/veilus/releases) | Installers and release notes | |

## Use

Veilus is for agencies managing client accounts, sellers with several storefronts, QA teams testing across devices and locations, and developers automating browsers. The [Acceptable Use Policy](https://veilus.io/acceptable-use/) forbids fraud, account takeover and fake engagement.

---

<p align="center">
  <sub>Built by an independent developer in Vietnam.</sub>
</p>
