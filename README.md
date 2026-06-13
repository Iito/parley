# Parley

**One binary that turns every machine on your network — Apple Silicon Macs, NVIDIA workstations, spare CPU boxes — into one shared, private LLM cluster.** OpenAI- and Ollama-compatible, so the tools you already use just work.

→ **Downloads & full docs: [lmparley.com](https://lmparley.com)** · [Releases](https://github.com/Iito/parley/releases)

---

## How it works

Run `parley serve` on each machine. That's the whole setup — no config file.

- **Discovery is automatic.** Nodes find each other on the LAN. New machines join the cluster the moment they start; they drop off cleanly when they stop.
- **Routing is automatic.** A request goes to the node that already has the model warm; ties break toward the shortest queue. You don't pick the node.
- **Naming is by capability.** Address the cluster as `parley:code`, `parley:fast`, `parley:best`, `parley:reason`, and Parley resolves to the best concrete model available across all nodes — or just use a model name from `parley list`.
- **Security is built in.** Inter-node traffic is TLS-encrypted with per-machine certificates; peers are identity-pinned on first contact. No telemetry, no phone-home.

```
$ parley serve
parley serve
  local:   http://localhost
  network: http://192.168.1.42

$ parley pull qwen3-coder:30b
$ parley status
```

---

## APIs

Parley speaks three API families on every node, so existing code and tools point straight at it:

| API | Endpoints | Connect by |
|---|---|---|
| **OpenAI** | `POST /v1/chat/completions`, `GET /v1/models` | `OPENAI_BASE_URL=http://<node>/v1` |
| **Ollama** | `/api/chat`, `/api/generate`, `/api/tags`, `/api/show`, `/api/ps` | `OLLAMA_HOST=http://<node>` |
| **Anthropic** | `POST /v1/messages` | base URL `http://<node>` |

```bash
curl http://<node>/v1/chat/completions \
  -H 'Content-Type: application/json' \
  -d '{"model":"parley:code","messages":[{"role":"user","content":"hello"}]}'
```

---

## Chat interfaces

Because Parley implements the OpenAI and Ollama APIs, the local-LLM ecosystem's chat front-ends work against it. In the app, set the **OpenAI base URL** to `http://<node>/v1` (or the **Ollama host** to `http://<node>`) and pick a `parley:*` alias or any model from `parley list`.

The lists below are drawn from Ollama's community integrations and checked against Parley's API surface — only interfaces that actually work are listed.

### Web

- [Open WebUI](https://github.com/open-webui/open-webui) — extensible, self-hosted web UI
- [LibreChat](https://github.com/danny-avila/LibreChat) — multi-provider ChatGPT-style UI
- [Lobe Chat](https://github.com/lobehub/lobe-chat) — modern UI with a plugin ecosystem
- [NextChat](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web) — cross-platform ChatGPT UI
- [big-AGI](https://github.com/enricoros/big-AGI) — AI suite for professionals
- [Hollama](https://github.com/fmaclen/hollama) — minimal web UI
- [Chatbox](https://github.com/Bin-Huang/Chatbox) — desktop & web client
- [Chatbot UI](https://github.com/ivanfioravanti/chatbot-ollama) — ChatGPT-style Ollama front-end
- [chat](https://github.com/swuecho/chat) — chat web app for teams
- [ollama-gui](https://github.com/chyok/ollama-gui) — minimal Python/Tkinter client
- [Onyx](https://github.com/onyx-dot-app/onyx) <sup>†</sup> — connected AI workspace
- [Perplexica](https://github.com/ItzCrazyKns/Perplexica) <sup>†</sup> — open-source Perplexity-style answer engine
- [ChatOllama](https://github.com/sugarforever/chat-ollama) <sup>†</sup> — chatbot with knowledge bases
- [Bionic GPT](https://github.com/bionic-gpt/bionic-gpt) <sup>†</sup> — on-premise AI platform

### Desktop

- [Dify](https://github.com/langgenius/dify) — LLM app development platform
- [Maid](https://github.com/Mobile-Artificial-Intelligence/maid) — cross-platform desktop & mobile client
- [Witsy](https://github.com/nbonamy/witsy) — AI desktop app for Mac, Windows, Linux
- [Cherry Studio](https://github.com/kangfenmao/cherry-studio) — multi-provider desktop client
- [Ollama App](https://github.com/JHubi1/ollama-app) — desktop & mobile client
- [PyGPT](https://github.com/szczyglis-dev/py-gpt) — desktop AI assistant for Linux, Windows, Mac
- [Alpaca](https://github.com/Jeffser/Alpaca) — GTK4 client for Linux and macOS
- [SwiftChat](https://github.com/aws-samples/swift-chat) — cross-platform (iOS, Android, macOS)
- [Enchanted](https://github.com/AugustDev/enchanted) — native macOS and iOS client
- [Ollama Grid Search](https://github.com/dezoito/ollama-grid-search) — evaluate and compare models
- [macai](https://github.com/Renset/macai) — macOS client
- [AI Studio](https://github.com/MindWorkAI/AI-Studio) — multi-provider desktop IDE
- [Reins](https://github.com/ibrahimcetin/reins) — Ollama client with parameter tuning
- [MindMac](https://mindmac.app) — AI chat client for Mac
- [Msty](https://msty.app) — multi-model desktop client
- [BoltAI](https://boltai.com) — AI chat client for Mac
- [AnythingLLM](https://github.com/Mintplex-Labs/anything-llm) <sup>†</sup> — all-in-one desktop AI app

<sup>†</sup> **Chat works normally.** These also offer built-in document-RAG / search, which needs an embeddings model — point that feature at a dedicated embeddings provider, since Parley serves chat completions only (no `/v1/embeddings`). Tools with a built-in local embedder (e.g. AnythingLLM) handle RAG on their own.

**Model management:** download models with `parley pull` rather than an app's in-app "download" button — Parley manages weights across the mesh and doesn't expose Ollama's `/api/pull`. A few tools that *require* in-app pulling to start (LLocal.in, Hillnote, datvodinh's RAG Chatbot) therefore aren't supported. Some closed macOS apps (IntelliBar, Kerlig, Perfect Memory AI) only talk to an Ollama on the default local address — run a node or a localhost proxy on the same machine to use them.

---

## License

The inference engine is open source; the mesh is source-available under a commercial license. See [lmparley.com](https://lmparley.com) for details and pricing.
