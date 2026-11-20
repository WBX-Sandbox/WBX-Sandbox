### WBX Sandbox

**Give your AI agent a computer of its own.** WBX Sandbox runs code in isolated Linux microVMs: every sandbox is a Firecracker virtual machine with its own kernel, disk, processes and network rules, started from a memory snapshot on servers in the EU. Your agent installs what it needs, does the work there and hands back a result, a file or a link – nothing runs on your machine.

Connect it to Claude, ChatGPT or any MCP client with one URL, or build on it with the Python SDK, the TypeScript SDK or the REST API. You pay per second of vCPU and memory, from prepaid credit.

#### What you get

| | |
|---|---|
| **Sandboxes** | Run commands and code (Python, JavaScript, bash), stream output, start background processes, open a terminal, read and write files. Templates for Python, data analysis, a real Chromium browser, Node.js, Go, Rust and more; you are root and can install anything. [Docs](https://sandbox.wbx.cz/docs/sandboxes) |
| **Sleep and wake** | Idle sandboxes can fall asleep instead of being destroyed: files are kept, compute is not billed, and the next request wakes them in a few seconds with the same ID. Name a sandbox to come back to it in a later chat. [Docs](https://sandbox.wbx.cz/docs/sandboxes/sleep) |
| **Public URLs** | A web app, API or dev server in the sandbox gets its own HTTPS address per port, protected by a token, over IPv4 and IPv6. [Docs](https://sandbox.wbx.cz/docs/sandboxes/urls) |
| **Network control** | Full internet, an allowlist of destinations, or no network at all – for the whole team or a single sandbox. Optional public IPv6 /64 per sandbox. Private networks and outgoing e-mail are always blocked. Built-in package cache for pip, npm and Go modules. [Docs](https://sandbox.wbx.cz/docs/sandboxes/network) |
| **Background jobs** | Submit a command and disconnect. The log, result files (SHA-256 checked) and the cost are kept for 7 days after the sandbox is gone. Server-side waiting, a compact log digest for LLMs with recognised test results, signed webhooks. [Docs](https://sandbox.wbx.cz/docs/jobs) |
| **Batches** | Up to 1,000 jobs in one call with a concurrency limit and one price limit for the whole batch, dependencies between jobs and result files passed from job to job. Report per job as JSON or CSV. [Docs](https://sandbox.wbx.cz/docs/jobs/batches) |
| **Cost control** | A price limit per job, checked against the worst case before it starts; monthly and daily budgets; compute classes from economy to performance and high-memory. [Budgets](https://sandbox.wbx.cz/docs/jobs/budget) · [Compute classes](https://sandbox.wbx.cz/docs/jobs/compute) |
| **Signed execution receipts** | Every job records what ran: the full command, the exact template build, checksums of inputs and results, measured CPU time and peak memory. Once the job ends, WBX signs the receipt with Ed25519, so anyone can check it was not edited. [Docs](https://sandbox.wbx.cz/docs/jobs/receipts) |
| **WebAssembly jobs** | Short, deterministic WASI modules run without starting a virtual machine – faster to start, cheaper, no network. [Docs](https://sandbox.wbx.cz/docs/jobs/wasm) |
| **Metrics** | CPU, memory, network and disk of every running sandbox, measured from the host every 10 seconds: an hour of history, a live stream and charts in the portal. [Docs](https://sandbox.wbx.cz/docs/sandboxes/metrics) |
| **Desktop & Computer Use** (beta) | A Linux desktop your agent sees in screenshots and controls with the mouse and keyboard. Watch it live in the portal and take over at any time. Enabled per team on request. [Docs](https://sandbox.wbx.cz/docs/sandboxes/desktop) |
| **Native MCP server** | `https://sandbox.wbx.cz/mcp` with OAuth sign-in – sandboxes, files, public URLs, jobs, batches and budgets as tools for your assistant. [Docs](https://sandbox.wbx.cz/docs/mcp) |

#### Quick start

**AI assistants (MCP).** Add `https://sandbox.wbx.cz/mcp` as a custom connector in Claude or ChatGPT and sign in – or, in Claude Code:

```bash
claude mcp add --transport http wbxsandbox https://sandbox.wbx.cz/mcp
```

Then ask: *“Use WBX Sandbox to make me a Snake game I can play in my browser, and send me the link.”* Setup for Cursor, VS Code, Codex CLI, Gemini CLI and other clients: [Connect your AI](https://sandbox.wbx.cz/docs/connect).

**Python** – `pip install wbxsandbox` (no dependencies, Python 3.9+)

```python
from wbxsandbox import Sandbox

with Sandbox.create(template="base") as sb:
    r = sb.run("python3 -c 'print(6 * 7)'")
    print(r.exit_code, r.stdout)                 # 0 42
    sb.files.write("/home/user/hello.txt", "hi")
# leaving the block destroys the sandbox
```

**TypeScript** – `npm install wbxsandbox` (no dependencies; Node.js 20+, Deno, Bun)

```ts
import { WBX } from "wbxsandbox";

const wbx = new WBX({ apiKey: process.env.WBXSANDBOX_API_KEY! });
const sandbox = await wbx.sandboxes.create({ template: "base", name: "my-project" });
console.log((await sandbox.commands.run("python3 --version")).stdout);
await sandbox.sleep();   // files kept, no compute billed – the next request wakes it up
```

**REST** – plain HTTPS and JSON from any language

```bash
SB=$(curl -s -X POST https://sandbox.wbx.cz/v1/sandboxes -H "Authorization: Bearer $WBX_KEY" \
  -H "Content-Type: application/json" -d '{"template": "base", "timeout_s": 300}' | jq -r .id)
curl -s -X POST https://sandbox.wbx.cz/v1/sandboxes/$SB/exec -H "Authorization: Bearer $WBX_KEY" \
  -H "Content-Type: application/json" -d '{"cmd": "uname -sr"}'
curl -s -X DELETE https://sandbox.wbx.cz/v1/sandboxes/$SB -H "Authorization: Bearer $WBX_KEY"
```

Create an API key in the portal. [Quickstart](https://sandbox.wbx.cz/docs/quickstart) · [REST API reference](https://sandbox.wbx.cz/docs/api)

#### Repositories

| Repository | |
|---|---|
| [wbxsandbox-python](https://github.com/WBX-Sandbox/wbxsandbox-python) | Python SDK (`pip install wbxsandbox`), including the `wbx connect` terminal command. |
| [wbxsandbox-js](https://github.com/WBX-Sandbox/wbxsandbox-js) | TypeScript / JavaScript SDK (`npm install wbxsandbox`) for Node.js, Deno, Bun and edge runtimes. |
| [examples](https://github.com/WBX-Sandbox/examples) | Runnable use cases and one script per SDK feature, each use case with a matching prompt for AI assistants. |
| [openai-agents-wbxsandbox](https://github.com/WBX-Sandbox/openai-agents-wbxsandbox) | Runs the shell and file tools of OpenAI Agents SDK sandbox agents in a WBX sandbox. |
| [langchain-wbxsandbox](https://github.com/WBX-Sandbox/langchain-wbxsandbox) | Sandbox backend for LangChain Deep Agents. |
| [codex-plugin](https://github.com/WBX-Sandbox/codex-plugin) | Codex plugin: the WBX Sandbox MCP server plus a skill on how to use it well. |

#### Links

[sandbox.wbx.cz](https://sandbox.wbx.cz) – start free, no card required · [Documentation](https://sandbox.wbx.cz/docs) · [Pricing](https://sandbox.wbx.cz/pricing) · [Security and isolation](https://sandbox.wbx.cz/docs/security) · [YouTube](https://www.youtube.com/@wbxsandbox) · Questions: info@wbx.cz
