# 🧠 Minecraft AI Agent

An autonomous Minecraft AI agent powered by a **local LLM** (Ollama) with intelligent navigation, environmental awareness, and self-improving gameplay. The agent perceives its surroundings through an advanced scanner system, makes decisions via LLM reasoning, and navigates using A* pathfinding.

```
Minecraft Server (Java Edition)
        │
   Mineflayer Bot (bot.js)     ← controls the player in-game
        │
   Brain (brain.js)            ← LLM-driven decision engine
   ├── Scanner (scanner.js)    ← environmental perception & path analysis
   ├── Navigator (navigation.js) ← reactive movement fallback
   └── Ollama (local LLM)      ← reasoning & planning
```

---

## Features

- 🧠 **LLM-Powered Decision Making** — uses a local Ollama model (e.g. `llama3.1:8b`) for reasoning, planning, and natural language chat
- 🔍 **Environmental Scanner** — 8-directional terrain analysis, path obstruction detection, mob tracking, resource radar, and dropped item detection
- 🗺️ **A* Pathfinding** — uses `mineflayer-pathfinder` for intelligent route planning (jumping, swimming, cliff avoidance)
- ⛏️ **Autonomous Survival** — gathers resources, crafts tools, fights mobs, and progresses through survival milestones
- 📚 **Persistent Memory** — learns from past sessions, remembers resource locations, and extracts gameplay lessons
- 🛡️ **Self-Preservation** — auto-eats, equips armor, flees from overwhelming threats
- 💬 **In-Game Chat** — responds to player messages naturally via LLM

---

## Prerequisites

| Tool | Version | Notes |
|------|---------|-------|
| **Node.js** | ≥ 18 | `node --version` |
| **npm** | ≥ 9 | comes with Node |
| **Ollama** | latest | [ollama.com](https://ollama.com) — run `ollama pull llama3.1:8b` |
| **Minecraft Java Edition** | 1.21 recommended | Any version ≥ 1.16 works |

---

## Setup

### 1. Install dependencies
```powershell
cd minecraft_ai
npm install
```

### 2. Install & start Ollama
```powershell
ollama pull llama3.1:8b
ollama serve
```

---

## Running

> Start components in order: Minecraft → Ollama → Node server.

### Step 1 — Start Minecraft
**Singleplayer LAN** (easiest):
- Create a new world (any type)
- Open to LAN: `Escape → Open to LAN → Allow Cheats: ON → Start`
- Note the port; update `config.json` if not `25565`

**Dedicated server** (alternative):
- Use [PaperMC](https://papermc.io/downloads) with `online-mode=false`

### Step 2 — Start the AI agent
```powershell
node server.js
```
Expected output:
```
[Server] Starting...
[Server] WebSocket listening on ws://localhost:3001
[Bot] Spawned at {"x":0,"y":64,"z":0}
[Bot] ✅ Pathfinder configured
[Brain] Agent memory loaded.
[Brain] Thinking every 3s. Session #1
```

The agent will immediately start playing — gathering wood, crafting tools, and exploring.

---

## Project Structure

```
minecraft_ai/
├── config.json        ← server connection settings
├── package.json       ← Node.js dependencies
├── server.js          ← WebSocket server & bot initialization
├── bot.js             ← Mineflayer bot setup
├── brain.js           ← LLM decision engine, actions, and memory
├── scanner.js         ← Environmental perception & path data
├── navigation.js      ← Reactive movement fallback
└── agent_memory.json  ← Persistent memory (auto-created)
```

---

## Configuration (`config.json`)

| Key | Default | Description |
|-----|---------|-------------|
| `mcHost` | `localhost` | Minecraft server hostname |
| `mcPort` | `25565` | Minecraft server port |
| `mcVersion` | `1.21` | Minecraft protocol version |
| `wsPort` | `3001` | WebSocket server port |
| `botUsername` | `rl_agent` | In-game player name |

---

## Architecture

### Scanner (`scanner.js`)
The scanner provides the agent's "eyes" — a structured view of the world:
- **Surroundings scan**: 8-direction terrain analysis with elevation classification (slopes, mountains, cliffs)
- **Path data** (`getPathData()`): Structured obstruction info used by BOTH the LLM and navigator — single source of truth
- **Resource radar**: Nearby blocks, hostile mobs, food animals, dropped items
- **Path obstructions**: What's blocking each direction, required tools, wall heights

### Brain (`brain.js`)
The decision engine runs a think loop every 3 seconds:
1. Scanner builds environmental context
2. LLM receives context + inventory + goals + memory
3. LLM responds with action + reasoning + chat
4. Brain executes the action (mine, craft, explore, fight, flee)
5. Results feed back into memory for learning

### Navigator (`navigation.js`)
Reactive movement fallback when A* pathfinding is unavailable:
- Velocity-based auto-jump for small terrain steps
- Tool-aware digging (won't break stone without a pickaxe)
- Water swimming toward target
- Stuck detection with escalating strategies

---

## Troubleshooting

**Bot won't connect**
- Ensure `mcVersion` in `config.json` matches your server exactly
- For LAN, set `mcPort` to the port shown in chat

**Bot sits idle**
- Check that Ollama is running: `ollama serve`
- Verify the model is installed: `ollama list`

**`mineflayer` module not found**
- Run `npm install` in the project directory
