### WBX Sandbox

Isolated Linux sandboxes for AI agents. Your assistant gets a small computer of its own: it runs code there, keeps files, and sends you back a result or a link.

- **Firecracker microVMs** hosted in the EU, each with its own disk and network rules
- **Direct connect via MCP** – add `https://sandbox.wbx.cz/mcp` to Claude, ChatGPT or any MCP client and sign in (OAuth)
- **Python SDK** – `pip install wbxsandbox` · **LangChain** – `pip install langchain-wbxsandbox` · **REST API**
- Background jobs and batches, HTTPS URLs for any port, optional public IPv6, egress allowlists or no network at all
- Idle sandboxes go to sleep and keep their files; the next command wakes them

```python
from wbxsandbox import Sandbox

with Sandbox.create(template="base", timeout=120) as sb:
    print(sb.run("uname -sr").stdout)
    print(sb.run_code("print(sum(range(100)))").stdout)
```

**Start here:** [sandbox.wbx.cz](https://sandbox.wbx.cz) · [Docs](https://sandbox.wbx.cz/docs/connect) · [Examples](https://github.com/WBX-Sandbox/examples) · [YouTube](https://www.youtube.com/@wbxsandbox)

Questions: info@wbx.cz
