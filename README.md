# AI Savers and Thiefs — Autonomous Agent Maze Simulation

A Java-based artificial intelligence project where autonomous agents navigate a grid maze, competing to collect coins and deposit them in a bank. Built as an AI coursework assignment, the simulation features two distinct agent types — the **Saver** and the **Thief** — each driven by weighted heuristic decision-making.

## 🎮 Overview

The simulation takes place on a **30×30 grid maze** containing walls, coins, power coins, and a bank. Agents perceive their surroundings through a **5×5 vision sensor** (24 cells, excluding the center) and an **olfactory sensor** that detects nearby entities by distance.

### Agents

| Agent | Goal | Strategy |
|-------|------|----------|
| **Saver** (`Poupador`) | Collect coins and deposit them at the bank | Explores the maze, avoids thieves, learns bank location, balances risk vs. reward |
| **Thief** (`Ladrão`) | Chase and rob savers of their coins | Tracks savers using vision and smell sensors, avoids dead ends, prefers unexplored paths |

## 🧠 How It Works

### Saver — Goal-Based Behavior with Adaptive Weights

The Saver uses a **weighted evaluation system** across its 24-cell vision grid to decide each move:

- **Exploration map**: Tracks visited positions and penalizes revisits to encourage coverage of new areas.
- **Goal switching**: Starts in `explore` mode; transitions to `get coins` once the bank location is discovered.
- **Bank awareness**: Savers share the bank's location via a static variable — when one Saver spots another, the bank location is communicated.
- **Threat avoidance**: Thieves in the vision field apply strong negative weights; coin evaluation is reduced when thieves are nearby.
- **Emotional parameters**: `ambition` and `fear` (randomly initialized) influence coin-seeking behavior and risk tolerance, with a meta-learning rate for adaptation.
- **Tie-breaking**: When multiple directions have equal weight, one is chosen at random.

### Thief — Multi-Layered Pursuit Heuristics

The Thief uses a **layered weight system** to chase savers:

- **Proximity detection**: Assigns escalating weights based on saver distance — maximum (adjacent), close (diagonal), straight-line, medium, and long range.
- **Olfactory tracking**: Uses the smell sensor (8-cell grid) to detect savers up to 5 cells away, with distance-based weight scaling.
- **Dead-end detection**: Identifies corridors where movement is restricted in both opposing directions.
- **Stuck detection**: Tracks consecutive turns spent in the same position and penalizes the blocked direction.
- **Exploration preference**: Maintains a position visit history (capped at 50 entries) and favors least-visited adjacent cells.
- **Invalid move elimination**: Immediately rules out moves into walls, boundaries, coins, banks, and other thieves.

## 📁 Project Structure

```
Trabalho IA - Poupador/
├── src/
│   ├── algoritmo/
│   │   ├── Poupador.java        # Saver agent logic
│   │   ├── Ladrao.java          # Thief agent logic
│   │   └── enums/
│   │       ├── MoveMapping.java
│   │       └── VisionMapping.java
│   └── aplicacao/
│       └── Executa.java          # Main entry point
├── Labirintos/
│   ├── Labirinto.txt             # Default maze layout
│   └── Caracol.txt               # Spiral maze layout
├── lib/
│   └── poupador.jar              # Simulation framework (GUI + engine)
└── classes/                      # Compiled .class files
```

### Maze Cell Codes

| Code | Meaning |
|------|---------|
| `0` | Free space |
| `1` | Wall |
| `3` | Bank |
| `4` | Coin |
| `5` | Power Coin |
| `100–199` | Saver agent (ID encoded) |
| `200–299` | Thief agent (ID encoded) |

## 🚀 Running

### Prerequisites

- **Java 8** (JDK 1.8) or later — [download here](https://www.oracle.com/java/technologies/downloads/)

### From the command line

```bash
cd "Trabalho IA - Poupador"

# Compile (if classes are missing or you've made changes)
javac -cp "lib/poupador.jar" -d classes src/algoritmo/*.java src/algoritmo/enums/*.java src/aplicacao/*.java

# Run
java -cp "classes:lib/poupador.jar" aplicacao.Executa
```

> **Windows users**: replace `:` with `;` in the classpath — e.g. `"classes;lib/poupador.jar"`

### From an IDE

1. Open the project in **Eclipse** or **IntelliJ** (config files for both are included).
2. Ensure `lib/poupador.jar` is on the classpath (should be automatic).
3. Run `aplicacao.Executa` as the main class.

### Using the GUI

Once running, the simulation window opens with playback controls:
- **▶ Play** — start/resume the simulation
- **⏸ Pause** — pause the simulation
- **⏹ Stop** — end the current run
- **📂 Load** — load a maze file from `Labirintos/`

## 🔧 Configuration

- **Mazes** can be edited or swapped in the `Labirintos/` folder. Each maze is a space-separated 30×30 grid of cell codes.
- **Agent parameters** (weights, thresholds, learning rates) can be tuned directly in `Poupador.java` and `Ladrao.java`.

## 📝 Notes

- The Saver's `ambition` and `fear` parameters are initialized randomly each run, leading to varied behavioral profiles.
- Thief agents cannot walk on coins, power coins, or banks — only free spaces and saver-occupied cells.
- The Saver penalizes backtracking by applying a negative weight to the cell it just came from.
- Power coins cost the Saver 5 coins to pick up, so they are avoided when the saver's balance is low.
