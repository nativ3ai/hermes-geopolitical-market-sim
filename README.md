# PrediHermes

PrediHermes is a Hermes skill for geopolitical market forecasting. It wires together:

<!-- MARKEE:START:0x57f50086e359d24cc65bcc5614e30123ef39ec76 -->
```
  ╔══════════════════════════════════════════════════════╗
  ║                  ⡷⢾ ⣎⣱ ⣏⡱ ⣇⠜ ⣏⡉ ⣏⡉                 ║
  ║                  ⠇⠸ ⠇⠸ ⠇⠱ ⠇⠱ ⠧⠤ ⠧⠤                 ║
  ╠══════════════════════════════════════════════════════╣
  ║                                                      ║
  ║   gm🪧                                                ║
  ║                                                      ║
  ╠══════════════════════════════════════════════════════╣
  ║                 0.007 ETH to change                  ║
  ╚══════════════════════════════════════════════════════╝
                 ││                      ││
   ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░
```
*Show the world you support this repo! [Change this message at markee.xyz](https://markee.xyz/ecosystem/platforms/github/0x57f50086e359d24cc65bcc5614e30123ef39ec76) ^*
<!-- MARKEE:END:0x57f50086e359d24cc65bcc5614e30123ef39ec76 -->

- WorldOSINT headless feeds for modular OSINT ingestion
- Polymarket Gamma/CLOB for open market discovery and pricing
- MiroFish for multi-agent simulation and counterfactual branches
- Hermes chat and cron for operator workflows

Current local-first release surface:

- `skill/geopolitical-market-sim/SKILL.md`
- `skill/geopolitical-market-sim/scripts/geopolitical_market_pipeline.py`
- `skill/geopolitical-market-sim/tools/predihermes/review.py`
- `skill/geopolitical-market-sim/tools/predihermes_tui/`

That means the published skill now includes the operator CLI, artifact compiler, and Rust TUI workbench that were previously only available in the installed local copy.

## What It Does

PrediHermes is not a fixed Iran-only pipeline. The core workflow is topic-modular:

1. Track a topic with keywords, regions, and selected WorldOSINT modules.
2. Pull open Polymarket markets related to that topic.
3. Build a seed packet from current OSINT signals and market context.
4. Run a MiroFish simulation with configurable rounds and agent count.
5. Compare simulated probability against market-implied probability.
6. Optionally branch a past simulation by injecting a new actor and measuring the butterfly effect.

## Architecture

```text
WorldOSINT headless -> PrediHermes pipeline -> Polymarket discovery -> seed packet
                                                          |
                                                          v
                                                     MiroFish run
                                                          |
                                                          v
                                                 Hermes summary / cron
```

## Companion Repos

- WorldOSINT headless: https://github.com/nativ3ai/worldosint-headless
- MiroFish fork used for this workflow: https://github.com/nativ3ai/MiroFish
- PrediHermes local installer: https://github.com/nativ3ai/prediup
- GeoCatalyst terminal companion: https://github.com/nativ3ai/geocatalyst
- Optional universal transcriber add-on: https://github.com/nativ3ai/universal-video-transcriber

This repo is the Hermes skill and bootstrap layer. The WorldOSINT and MiroFish repos remain separate companions.

Companion baseline for this release:

- `hermes-geopolitical-market-sim`: current local-first skill release
- `MiroFish`: `v0.2.0` release line with PrediHermes operator tooling, local graph mode, profile manifests, and branch workbench support
- `worldosint-headless`: current `main` baseline, unchanged by this release

## Prerequisites

- Hermes Agent installed and working
- Python `3.10+`
- Node.js `18+`
- `git`
- `npm`
- `ollama` only if you want the fully local edition without external model keys

Check the host before installing:

```bash
./install.sh --doctor
```

## Install

### PrediHermes Local

This is the recommended install if you want the slim CLI-first stack:

- local SQLite graph backend
- local Ollama model for MiroFish
- WorldOSINT headless only
- MiroFish backend only
- no Zep
- no MiroFish frontend required
- no external LLM key required

One-command installer:

```bash
brew tap nativ3ai/prediup
brew install predihermes
prediup install
```

Direct repo path:

```bash
git clone https://github.com/nativ3ai/hermes-geopolitical-market-sim.git
cd hermes-geopolitical-market-sim
./install.sh --bootstrap-local
```

Common local-edition variants:

```bash
./install.sh --bootstrap-local --ollama-model qwen2.5:3b
./install.sh --bootstrap-local --with-video-transcriber
```

### Skill only

This installs the PrediHermes skill into Hermes without cloning companions:

```bash
git clone https://github.com/nativ3ai/hermes-geopolitical-market-sim.git
cd hermes-geopolitical-market-sim
./install.sh
```

Installed path:

- `~/.hermes/skills/research/geopolitical-market-sim`

### PrediHermes Full

This is the legacy/full companion-stack bootstrap. Keep this if you want the richer multi-repo setup and optional UI paths.

This installs the skill, clones WorldOSINT and MiroFish, installs their dependencies, and writes helper launchers.

```bash
git clone https://github.com/nativ3ai/hermes-geopolitical-market-sim.git
cd hermes-geopolitical-market-sim
./install.sh --bootstrap-stack
```

### Full local stack with optional transcriber

```bash
./install.sh --bootstrap-stack --with-video-transcriber
```

### Optional launchd add-on

```bash
./install.sh --bootstrap-stack --with-launchd
```

## What The Installer Creates

Default local stack layout:

```text
~/predihermes/
├── bin/
│   ├── predihermes
│   ├── predihermes-worldosint
│   ├── predihermes-worldosint-ws
│   ├── predihermes-mirofish-backend
│   ├── predihermes-mirofish-ui
│   ├── predihermes-stack-up
│   ├── predihermes-stack-down
│   ├── predihermes-stack-status
│   └── predihermes-stack-health
└── companions/
    ├── worldosint-headless/
    ├── MiroFish/
    └── universal-video-transcriber/   # only if enabled
```

The installer also writes non-secret local stack pointers into:

- `~/.hermes/.env`

Local-edition extras:

- writes `LLM_API_KEY=ollama`
- writes `LLM_BASE_URL=http://127.0.0.1:11434/v1`
- writes `LLM_MODEL_NAME=<selected model>`
- writes `GRAPH_BACKEND=local`
- installs helper aliases:
  - `predihermes-local-up`
  - `predihermes-local-status`
  - `predihermes-local-health`

## Required Manual Configuration

PrediHermes can bootstrap the stack, but configuration differs by profile.

### Hermes provider

Hermes just needs a working provider. Configure that however you prefer:

```bash
hermes model
```

If you use OpenAI Codex through ChatGPT OAuth, select `openai-codex` in `hermes model`.

### Local edition model defaults

If you used `--bootstrap-local`, the installer sets MiroFish to Ollama automatically:

- `LLM_API_KEY=ollama`
- `LLM_BASE_URL=http://127.0.0.1:11434/v1`
- `LLM_MODEL_NAME=qwen2.5:7b` by default
- `GRAPH_BACKEND=local`

By default the installer will:

- install Ollama on macOS if Homebrew is available
- start the Ollama daemon if needed
- pull the requested model if it is missing

Use these flags if you want a different local profile:

```bash
./install.sh --bootstrap-local --ollama-model qwen2.5:3b
./install.sh --bootstrap-local --skip-ollama-pull
./install.sh --bootstrap-local --ollama-base-url http://127.0.0.1:11434/v1
```

### MiroFish backend modes

PrediHermes now assumes the MiroFish fork can run either with Zep or with the local SQLite graph backend.

Recommended default:

- `GRAPH_BACKEND=local`

Modes:

- `GRAPH_BACKEND=local`
  - stores graph state under `backend/uploads/graphs/*.sqlite3`
  - does not require `ZEP_API_KEY`
- `GRAPH_BACKEND=auto`
  - uses Zep only when a Zep key is present
  - otherwise falls back to local SQLite
- `GRAPH_BACKEND=zep`
  - forces the Zep backend
  - requires `ZEP_API_KEY`

### MiroFish secrets

Set these in:

- `~/predihermes/companions/MiroFish/.env`

Required for the full / legacy profile:

- `LLM_API_KEY`
- `LLM_BASE_URL`
- `LLM_MODEL_NAME`
- `GRAPH_BACKEND=local` for the default local SQLite graph store

For the local edition, the installer writes those values for you and no external API key is required.

Optional:

- `ZEP_API_KEY` only if you switch `GRAPH_BACKEND=zep`

Notes:

- `GRAPH_BACKEND=auto` will use Zep only when a Zep key is present.
- `GRAPH_BACKEND=local` stores graph state under `backend/uploads/graphs/*.sqlite3`.

## Start The Stack

### One-command local bring-up

If you used `--bootstrap-local`, this is the cleanest operator path:

```bash
~/predihermes/bin/predihermes-local-up
~/predihermes/bin/predihermes-local-status
~/predihermes/bin/predihermes-local-health
```

If you used the full / legacy bootstrap, or want the lower-level commands, use:

```bash
~/predihermes/bin/predihermes-stack-up
```

Default behavior:

- starts WorldOSINT headless API
- starts the WorldOSINT websocket bridge
- starts the MiroFish backend
- writes logs to `~/predihermes/logs`
- tracks PIDs in `~/predihermes/run`
- does not require the MiroFish frontend

Optional flags:

```bash
~/predihermes/bin/predihermes-stack-up --with-ui
~/predihermes/bin/predihermes-stack-up --without-ws
~/predihermes/bin/predihermes-stack-up --force-restart
```

Useful follow-up commands:

```bash
~/predihermes/bin/predihermes-stack-status
~/predihermes/bin/predihermes-stack-health
~/predihermes/bin/predihermes-stack-down
```

### WorldOSINT

If you used bootstrap:

```bash
~/predihermes/bin/predihermes-worldosint
```

Optional websocket bridge:

```bash
~/predihermes/bin/predihermes-worldosint-ws
```

Default base URL:

- `http://127.0.0.1:3000`

Manual WorldOSINT startup remains:

```bash
git clone https://github.com/nativ3ai/worldosint-headless.git
cd worldosint-headless
npm install
npm run dev
```

### MiroFish

If you used bootstrap:

```bash
~/predihermes/bin/predihermes-mirofish-backend
```

Default API:

- `http://127.0.0.1:5001`

Optional UI helper still exists for the full / legacy stack:

```bash
~/predihermes/bin/predihermes-mirofish-ui
```

Manual MiroFish startup remains:

```bash
git clone https://github.com/nativ3ai/MiroFish.git
cd MiroFish
cp .env.example .env
printf 'LLM_API_KEY=ollama\nLLM_BASE_URL=http://127.0.0.1:11434/v1\nLLM_MODEL_NAME=qwen2.5:7b\nGRAPH_BACKEND=local\n' >> .env
cd backend && uv sync && cd ..
FLASK_DEBUG=False uv run --directory backend python run.py
```

## Verify

Helper-based verification:

```bash
~/predihermes/bin/predihermes-stack-status
~/predihermes/bin/predihermes-stack-health
~/predihermes/bin/predihermes health
~/predihermes/bin/predihermes list-worldosint-modules
~/predihermes/bin/predihermes command-catalog
```

Direct skill path also works:

```bash
python3 ~/.hermes/skills/research/geopolitical-market-sim/scripts/geopolitical_market_pipeline.py health
python3 ~/.hermes/skills/research/geopolitical-market-sim/scripts/geopolitical_market_pipeline.py list-worldosint-modules
python3 ~/.hermes/skills/research/geopolitical-market-sim/scripts/geopolitical_market_pipeline.py command-catalog
```

## Optional GeoCatalyst Terminal

GeoCatalyst is the companion operator terminal for this stack. Use it when you
want a live TUI or JSON-first CLI over:

- Polymarket markets and focused WS orderbooks
- catalyst timelines and source drilldown
- PrediHermes / MiroFish simulation artifacts
- alerting, watchlists, pins, and what-if flows

Install it globally:

```bash
npm install -g geocatalyst
```

Then verify and launch:

```bash
geocatalyst doctor
geocatalyst stack up
geocatalyst
```

Useful headless examples:

```bash
geocatalyst market desk --selector "US x Iran ceasefire by April 15?" --mode live
geocatalyst market timeline --selector "US x Iran ceasefire by April 15?" --mode live
geocatalyst simulation detail --selector "US x Iran ceasefire by April 15?" --mode live
```

## Core CLI Flow

Add `~/predihermes/bin` to `PATH` if you want shorter commands. Otherwise call the helper with the full path.

### Inspect available OSINT modules

```bash
predihermes list-worldosint-modules
```

### Track a topic

```bash
predihermes track-topic \
  --topic-id iran-conflict \
  --topic "Iran conflict and nuclear diplomacy" \
  --market-query "Iran nuclear deal" \
  --keyword iran --keyword israel --keyword hormuz --keyword nuclear --keyword enrichment --keyword iaea \
  --region-code IR --region-code IL --region-code SA --region-code US
```

### Plan before running

```bash
predihermes plan-tracked iran-conflict --target-agents 48
```

### Run without simulation

```bash
predihermes run-tracked iran-conflict
```

### Run with simulation

```bash
predihermes run-tracked iran-conflict --simulate
```

### Override rounds and agent count per run

```bash
predihermes run-tracked iran-conflict \
  --simulate \
  --simulation-mode manual \
  --target-rounds 36 \
  --target-agents 60
```

### Update a topic modularly

```bash
predihermes update-topic iran-conflict --add-module maritime_snapshot
predihermes update-topic iran-conflict --remove-module maritime_snapshot
predihermes update-topic iran-conflict --set-max-rounds 28
predihermes update-topic iran-conflict --set-simulation-mode auto --set-target-agents 48
```

### Show dashboard

```bash
predihermes dashboard iran-conflict
```

## Use From Hermes Chat

Load the skill:

```bash
hermes -s geopolitical-market-sim
```

Natural prompt examples:

- `Use PrediHermes stack-up flow and bring up the local services required for a run, then verify health.`
- `Use PrediHermes list-worldosint-modules and suggest 6 modules for maritime risk.`
- `Use PrediHermes update-topic iran-conflict: add module maritime_snapshot and set max rounds to 28.`
- `Use PrediHermes plan-tracked iran-conflict and tell me if the feed is good enough.`
- `Use PrediHermes run-tracked iran-conflict in manual mode with 40 rounds and 60 agents.`
- `Use PrediHermes run-tracked iran-conflict with simulate and summarize implied vs forecast.`
- `Use PrediHermes dashboard iran-conflict and summarize top drift signals.`

## Schedule Runs

Daily scheduled run example:

```bash
hermes cron create 'every 1d' \
  "Using the terminal tool, run: predihermes run-tracked iran-conflict --simulate. Then read run_summary.md and simulation_summary.md and return market question, yes bid/ask, directional call, reasons, and artifact paths." \
  --skill geopolitical-market-sim \
  --name 'Iran Conflict Market Sim' \
  --deliver local
```

## Counterfactual Branches

Create a branch from a base simulation:

```bash
curl -X POST http://127.0.0.1:5001/api/simulation/<base_simulation_id>/counterfactual \
  -H 'Content-Type: application/json' \
  -d '{
    "actor": {
      "name": "Swiss backchannel envoy",
      "entity_type": "Diplomat",
      "profession": "Diplomat",
      "country": "Switzerland",
      "stance": "mediator",
      "bio": "Quiet envoy coordinating verification-first diplomacy.",
      "persona": "Prioritizes de-escalation, inspections, and sequencing.",
      "interested_topics": ["backchannel diplomacy", "IAEA inspections"],
      "activity_level": 0.55,
      "influence_weight": 3.4,
      "posts_per_hour": 1.2,
      "comments_per_hour": 0.9,
      "active_hours": [8,9,10,11,12,13,14,15,16,17,18]
    },
    "injection_round": 12,
    "opening_statement": "Verification-first sequencing is the only viable path."
  }'
```

Start the new branch:

```bash
curl -X POST http://127.0.0.1:5001/api/simulation/start \
  -H 'Content-Type: application/json' \
  -d '{
    "simulation_id": "<new_simulation_id>",
    "platform": "parallel",
    "force": true,
    "enable_graph_memory_update": true
  }'
```

Useful monitoring endpoints:

- `GET /api/simulation/<id>/run-status`
- `GET /api/simulation/<id>/run-status/detail`
- `GET /api/simulation/<id>/timeline`
- `GET /api/simulation/<id>/actions?round_num=<n>`

## Optional launchd

Install launchd agents:

```bash
./launchd/install_launchd.sh \
  --worldosint-root /absolute/path/to/worldosint-headless \
  --mirofish-root /absolute/path/to/MiroFish
```

Installed labels:

- `com.predihermes.worldosint`
- `com.predihermes.worldosint-ws`
- `com.predihermes.mirofish-backend`

Check status:

```bash
launchctl list | grep predihermes
tail -f ~/Library/Logs/predihermes/worldosint.out.log
tail -f ~/Library/Logs/predihermes/mirofish-backend.out.log
```

Uninstall:

```bash
./launchd/uninstall_launchd.sh
```

## Troubleshooting

- `predihermes: command not found`
  - Use `~/predihermes/bin/predihermes` or add `~/predihermes/bin` to `PATH`.
- MiroFish simulation fails immediately
  - Check `~/predihermes/companions/MiroFish/.env` and verify `LLM_*` values are set.
  - If you do not use Zep, keep `GRAPH_BACKEND=local`.
  - If you do use Zep, set `GRAPH_BACKEND=zep` and provide `ZEP_API_KEY`.
- Local edition cannot talk to Ollama
  - Run `ollama list` and confirm the selected model exists.
  - Rerun `./install.sh --bootstrap-local` or `prediup install`.
  - Check `~/predihermes/bin/predihermes-local-health`.
- WorldOSINT or MiroFish health checks fail
  - Verify both services are running and the URLs in `~/.hermes/.env` match your local stack.
- Optional transcriber fails because `ffmpeg` or `yt-dlp` is missing
  - Rerun `./install.sh --bootstrap-stack --with-video-transcriber` or install those tools manually.
- A run artifact is referenced but missing
  - Verify it with `test -f`, `stat`, or `ffprobe` before claiming it exists.

## Repository Layout

- `skill/geopolitical-market-sim/SKILL.md`
- `skill/geopolitical-market-sim/agents/openai.yaml`
- `skill/geopolitical-market-sim/scripts/geopolitical_market_pipeline.py`
- `install.sh`
- `launchd/install_launchd.sh`
- `launchd/uninstall_launchd.sh`
