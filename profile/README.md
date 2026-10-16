<p align="center">
  <img src="https://veilus.io/favicon.svg" width="80" alt="Veilus logo" />
</p>

<h1 align="center">Veilus</h1>

<p align="center">
  <a href="https://github.com/veilus/releases/releases/latest"><img src="https://img.shields.io/github/v/release/veilus/releases?label=app" alt="Latest app release" /></a>
  <a href="https://www.npmjs.com/package/veilus"><img src="https://img.shields.io/npm/v/veilus?label=npm%20veilus" alt="npm veilus" /></a>
  <a href="https://pypi.org/project/veilus/"><img src="https://img.shields.io/pypi/v/veilus?label=pypi%20veilus" alt="PyPI veilus" /></a>
  <a href="https://www.npmjs.com/package/veilus-mcp"><img src="https://img.shields.io/npm/v/veilus-mcp?label=veilus-mcp" alt="npm veilus-mcp" /></a>
  <br />
  <a href="https://crates.io/crates/veilus-fingerprint"><img src="https://img.shields.io/crates/v/veilus-fingerprint?label=veilus-fingerprint" alt="crates.io veilus-fingerprint" /></a>
  <a href="https://crates.io/crates/veilus-human-input"><img src="https://img.shields.io/crates/v/veilus-human-input?label=veilus-human-input" alt="crates.io veilus-human-input" /></a>
</p>

<h3 align="center">Anti-detect browser your AI assistant drives over MCP.</h3>

<table><tr><td>
Veilus is a desktop app for running many browser profiles on one computer. Each profile has its own fingerprint, proxy and cookies, and runs on a Chromium build we patch at the C++ source level. Connect Claude Code, Cursor or any MCP client and your assistant creates the profiles, writes the Playwright script, tests it, fixes it, and runs it across profiles — after you approve it.
</td></tr></table>

<p align="center">
  <a href="https://www.youtube.com/watch?v=8NRDNW_4KNU"><img src="https://img.youtube.com/vi/8NRDNW_4KNU/maxresdefault.jpg" width="600" alt="Video: one sentence, many browsers — an AI assistant drives Veilus over MCP" /></a>
  <br /><em>One sentence, many browsers: an AI assistant drives Veilus over MCP (YouTube, 55 s)</em>
</p>

<p align="center">
  <a href="https://veilus.io/?utm_source=github&utm_medium=org_profile">Website</a> ·
  <a href="https://veilus.io/download/">Download</a> ·
  <a href="https://docs.veilus.io">Docs</a> ·
  <a href="https://veilus.io/developers/">MCP</a> ·
  <a href="https://veilus.io/pricing/">Pricing</a> ·
  <a href="https://t.me/veilusbrowser">Telegram</a> ·
  <a href="https://x.com/veilusbrowser">X</a>
</p>

- **Fingerprint patched in Chromium's C++ source** — 54 patches on Chromium 154; no page script, no extension. [How](#patched-in-c-not-injected)
- **Own fingerprint and proxy per profile** — 59 fingerprint fields, each read by C++ code inside the engine; HTTP or SOCKS5 proxy per profile
- **Built-in MCP server, 39 tools** — profiles, proxies, scripts, batch runs, schedules, datasets, and direct page control (navigate, click, type, snapshot)
- **Your assistant writes the automation** — it saves a Playwright script, runs it on a few profiles, reads each profile's error and fixes it
- **You approve before it scales** — a script added over MCP or the REST API runs on at most 3 profiles until you approve it in the app; batch runs and schedules need that approval
- **Use your own tools** — Node and Python SDKs attach Playwright to a profile's browser; Puppeteer, browser-use, Stagehand, Crawl4AI and others tested ([list](https://github.com/veilus/sdk/blob/main/examples/TESTED.md))
- **Local by default** — profiles live on your machine and the local API listens only on localhost; Veilus Sync (optional) copies profiles to a Git repository or Google Drive folder you choose
- **Free for 5 profiles** on one device, no card

## Quick start

**1. Install the app** — [Windows 10/11 (x64)](https://veilus.io/download/) or [macOS 13.5+ (Apple Silicon)](https://veilus.io/download/). The browser engine downloads inside the app on first launch.

**2. Turn on the local API** — open **API & MCP** in the app sidebar, switch it on and create a token. This needs a paid plan or the 7-day Pro trial (request it in the app). On Windows, also set `VEILUS_BINARY` to the app path shown on that page.

**3. Connect your assistant:**

```bash
# Claude Code
claude mcp add --scope user veilus -e VEILUS_API_TOKEN=<token> -- npx -y veilus-mcp
```

```jsonc
// Cursor, Claude Desktop and other MCP clients
{
  "mcpServers": {
    "veilus": {
      "command": "npx",
      "args": ["-y", "veilus-mcp"],
      "env": { "VEILUS_API_TOKEN": "<token>" }
    }
  }
}
```

Then ask in plain words, for example: *"Import these 10 proxies, create 10 profiles, write a script that logs the page title of example.com and test it on 3 profiles."* Once you approve the script in the app, the assistant can run it on every profile or schedule it.

For Claude Code there is also a plugin that runs the whole job — proxies, profiles, a tested script, a schedule, a first run and a report — from one request:

```bash
claude plugin marketplace add veilus/claude-plugin
claude plugin install veilus@veilus
```

## Drive a profile from your own code

The SDKs launch a profile through the app's local API, then attach Playwright to that profile's own browser over CDP, so your script runs with the profile's fingerprint, proxy and cookies.

**Node.js** — `npm install veilus playwright-core`

```js
import { Veilus } from 'veilus';

const veilus = new Veilus(); // reads VEILUS_API_TOKEN
const profiles = await veilus.listProfiles();

const { page, close } = await veilus.connect(profiles[0].id);
await page.goto('https://example.com');
console.log(await page.title());
await close(); // the profile keeps running
await veilus.stop(profiles[0].id);
```

**Python** — `pip install "veilus[playwright]"`

```python
import asyncio
from veilus import Veilus

async def main():
    veilus = Veilus()  # reads VEILUS_API_TOKEN
    profiles = await veilus.list_profiles()

    session = await veilus.connect(profiles[0]["id"])
    await session.page.goto("https://example.com")
    print(await session.page.title())
    await session.close()  # the profile keeps running
    await veilus.stop(profiles[0]["id"])

asyncio.run(main())
```

Don't create a new context with your own user agent, viewport or locale — it would override the profile's values. Full REST reference: [docs.veilus.io/reference/rest-api](https://docs.veilus.io/reference/rest-api/).

## What your assistant can do

| Area | MCP tools |
|---|---|
| Profiles | `list_profiles` `create_profiles` `launch_profile` `stop_profile` `set_profile_variables` `get_capacity` |
| Proxies | `import_proxies` `list_proxy_pools` `assign_proxy_pool` `get_profile_proxy` `test_proxy` |
| Scripts | `save_script` `list_scripts` `get_script` `compile_script` `run_script` `get_run_result` `list_runs` |
| Batch and schedules | `run_batch` `create_schedule` `list_schedules` `set_schedule_enabled` `run_schedule_now` |
| Page control | `navigate` `snapshot` `click` `type` `press_key` `scroll` `evaluate_js` `wait_for_element` `set_file` |
| Datasets | `create_dataset` `append_dataset_rows` `list_datasets` `get_dataset_rows` `assign_dataset` `unassign_dataset` `reset_dataset_rows` |

There is no tool that approves a script. Approval happens only in the app, by you. Tool reference: [docs.veilus.io/reference/mcp](https://docs.veilus.io/reference/mcp/).

## Patched in C++, not injected

A site checks a fingerprint by asking the browser: `navigator.hardwareConcurrency`, a canvas hash, the WebGL renderer, the list of fonts. If the answer is changed by JavaScript injected into the page or by an extension, that script is itself something the site can look for — an overridden getter, a function whose `toString()` is wrong, a property in the wrong place, a value that does not match what a worker or an iframe reports.

Veilus changes the answer where Chromium computes it. The profile's values are read by C++ code inside the engine — in Blink, the GPU process, the audio stack, the network service — and returned through the same code paths a normal Chrome uses. There is no script in the page and no extension for a site to find.

| Area | Patched in C++ |
|---|---|
| Graphics | Canvas, WebGL (parameters and extension lists), WebGPU adapter, GPU capabilities, glyph metrics |
| Audio | AudioContext output, sample rate, channel count |
| Fonts | Font list, generic font families, system font on macOS, bundled fonts so a Windows profile has Windows fonts |
| Navigator and UA | User agent, UA Client Hints and brands, device memory, platform APIs, battery, Do Not Track, speech voices |
| Screen | Screen size and color depth, CSS media features, pointer and hover, window frame, a Windows profile running on a Mac |
| Locale and time | Timezone, ICU locale, `Accept-Language` in JavaScript and on the wire, geolocation |
| Network | WebRTC IP handling, including a fake-IP mode; DNS through the proxy |
| Media and permissions | Number of cameras and microphones, permission states |
| Automation signals | `navigator.webdriver`, CDP `Runtime.enable` on pages and in Web Workers, inspector stack frames, driver source URLs, `performance.now` precision |

How the patch set is kept honest:

- **One set, one build.** The 54 `.patch` files are applied in order to the Chromium 154.0.8037.58 source before every engine build. The Windows engine released on 2026-10-16 was built from exactly this set.
- **Every field has a consumer.** Each of the 59 fingerprint fields the app generates is read by at least one patch; a test fails when a field has none, unless it is listed as a known gap with a tracked issue.
- **Patches are checked against stock Chrome.** A premise test runs the patches' probes on unmodified Chrome for Testing of the same version, to confirm the leak each patch closes still exists there.
- **Measured, not claimed.** Results on public detection sites are in [Detection sites](#detection-sites), with the date and what was not measured.

The engine source is closed. The fingerprint generator that produces the values is open: [fingerprint-generator](https://github.com/veilus/fingerprint-generator).

## How it works

```
 Claude Code / Cursor ──stdio──▶ veilus-mcp ──▶ Veilus app (mcp) ─┐
 Your script (SDK)    ──HTTP───▶ local REST API (localhost only) ──┤
                                                                   ▼
                                         profile ─▶ patched Chromium engine
                                                    own fingerprint · own proxy · own cookies
```

- **Engine:** Chromium 154.0.8037.58 with 54 C++ patches — see below.
- **Fingerprints:** generated from a Bayesian network trained on real browser data ([fingerprint-generator](https://github.com/veilus/fingerprint-generator)), so values that belong together stay together.
- **Input:** mouse paths, wheel scrolling and typing rhythm come from [human-input](https://github.com/veilus/human-input).
- **Load control:** up to 16 browsers open at once, shared by every way of opening one (by hand, schedule, API, assistant); batch runs wait when CPU or RAM is high.

## Detection sites

Measured 2026-10-14 on a Mac, engine 154.0.8037.58, Playwright attached over CDP, profile without a proxy.

| Site | Veilus |
|---|---|
| demo.fingerprint.com/playground — bot | not detected |
| CreepJS — headless score | 0% |
| browserscan.net/bot-detection | Normal |
| bot.sannysoft.com | all passed |
| bot.incolumitas.com | WEBDRIVER fails — it fails on a plain Chrome for Testing window too |
| deviceandbrowserinfo.com/are_you_a_bot | **bot** on 2026-10-14 — it caught CDP inside a Web Worker. The engine was patched and re-released on 2026-10-16; re-measured on Windows the same day: not a bot |

Not measured: Windows, runs through a proxy, long sessions. fingerprint.com also flagged *tampering* in this run because the profile's timezone (Honolulu) did not match the test machine's IP (Vietnam) — use a proxy in the profile's location.

## Platforms

| | |
|---|---|
| Windows | 10 / 11, x64 — `.exe` installer |
| macOS | 13.5 or newer, Apple Silicon — `.dmg` |
| Linux | not available |

## Pricing

| Plan | Price | What you get |
|---|---|---|
| Free | $0 | 5 profiles, 1 device, latest engine |
| Monthly | $9 per device / month | Unlimited profiles, Veilus Flow, schedules, batch runs, local REST API and MCP, Veilus Sync, script templates |
| Lifetime | $99 (1 device) · $149 (3) · $199 (5) | Same as Monthly, one payment, updates included |

Prices in VND, refunds and details: [veilus.io/pricing](https://veilus.io/pricing/). The app includes a 7-day Pro trial.

## Open source

The desktop app and the browser engine are closed source. These parts are open:

| Repository | What it is | Install |
|---|---|---|
| [sdk](https://github.com/veilus/sdk) | Launch Veilus profiles from Node and Python and drive them with Playwright; `veilus-mcp` launcher | `npm install veilus playwright-core` · `pip install "veilus[playwright]"` · `npx -y veilus-mcp` |
| [claude-plugin](https://github.com/veilus/claude-plugin) | Claude Code plugin: run a browser-automation job over MCP from one request | `claude plugin install veilus@veilus` |
| [fingerprint-generator](https://github.com/veilus/fingerprint-generator) | Rust library that generates browser fingerprints and HTTP headers from a Bayesian network | [`veilus-fingerprint`](https://crates.io/crates/veilus-fingerprint) |
| [human-input](https://github.com/veilus/human-input) | Human-like mouse paths, wheel scrolling and typing rhythm, in Rust | [`veilus-human-input`](https://crates.io/crates/veilus-human-input) |
| [docs](https://github.com/veilus/docs) · [website](https://github.com/veilus/website) · [blog](https://github.com/veilus/blog) | Sources of docs.veilus.io, veilus.io and blog.veilus.io | |
| [releases](https://github.com/veilus/releases) | Installers and release notes | |

## FAQ

**Do I need to write code?** No. Your assistant writes the scripts over MCP, or you build them in the app. The SDKs are for people who want to drive profiles from their own code.

**Which AI assistants work?** Any MCP client that runs a stdio server: Claude Code, Cursor, Claude Desktop and others. The plugin is for Claude Code.

**Can the assistant run something on all my profiles without me?** No. Until you approve a script in the app, it runs on at most 3 profiles per run, and batch runs and schedules refuse it.

**Where is my data?** On your machine. Veilus Sync is optional; it copies profiles to your own Git repository or Google Drive folder. Veilus does not encrypt the synced profile data — only the access token, locally.

**What is it for?** Agencies managing client accounts, sellers with several storefronts, QA teams testing across devices and locations, and developers automating browsers. The [Acceptable Use Policy](https://veilus.io/acceptable-use/) forbids fraud, account takeover and fake engagement.

---

<p align="center">
  <sub>Built by an independent developer in Vietnam · <a href="https://veilus.io/changelog/">Changelog</a> · <a href="https://veilus.io/privacy/">Privacy</a> · <a href="https://veilus.io/terms/">Terms</a></sub>
</p>
