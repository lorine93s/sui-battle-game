## Sui Battle Game

A Move-based battle engine for the Sui blockchain featuring a faithful implementation of the Pokémon v1 damage formula, plus a minimal game layer that supports two modes:
- PvB: single player versus a bot
- PvP: multiplayer (player versus player)

This repository includes:
- `packages/pokemon`: reusable Move library implementing damage and stats
- `packages/game-v1`: game primitives (arena, battle orchestration, PvP)
- `cli`: simple Node.js CLI to interact with deployed modules on testnet

### Features
- **Damage formula v1**: Deterministic, on-chain formula aligned with classic Pokémon rules
- **Composable Move modules**: Separation between formula (`pokemon`) and game logic (`game-v1`)
- **Two game modes**: PvB (bot) and PvP (player vs player)
- **CLI prototype**: Quick way to try battles on Sui testnet
- **Apache-2.0 licensed**: Open for use and extension

### Repository Structure
```
battle-game/
  ├─ packages/
  │  ├─ pokemon/
  │  │  ├─ Move.toml
  │  │  ├─ sources/
  │  │  │  ├─ pokemon_v1.move
  │  │  │  └─ stats.move
  │  │  └─ tests/
  │  └─ game-v1/
  │     ├─ Move.toml
  │     └─ sources/
  │        ├─ arena.move
  │        ├─ arena_pvp.move
  │        └─ battle.move
  └─ cli/
     ├─ package.json
     ├─ v1/
     │  ├─ pvb.js   # single player vs bot
     │  └─ pvp.js   # multiplayer
     └─ README.md
```

### Quick Start

#### Prerequisites
- Node.js 18+ and `pnpm` (or `npm`)
- Sui CLI installed and configured (`sui client switch --env testnet`)
- Rust toolchain (for Move build) via `rustup`

#### Install
```bash
# Clone
git clone https://github.com/<your-username>/<your-repo>.git
cd battle-game

# Install CLI deps
cd cli
pnpm install
```

#### Build Move Packages
```bash
# From repo root
sui move build --path packages/pokemon
sui move build --path packages/game-v1
```

#### Test Move Packages (optional)
```bash
sui move test --path packages/pokemon
# Add tests for game-v1 as needed
```

### Deploy to Sui (Testnet)
You can deploy the packages separately (recommended) or in a single app package. Below is an example flow to publish each package. Make sure your testnet wallet has funds.

```bash
# 1) Publish pokemon package
sui client publish --gas-budget 100000000 --skip-dependency-verification --path packages/pokemon

# 2) Publish game-v1 package
sui client publish --gas-budget 100000000 --skip-dependency-verification --path packages/game-v1
```

Record the resulting package IDs and module names; you will pass them to the CLI.

### CLI Usage

The CLI provides two quick prototypes. Ensure you have:
- testnet package IDs for `pokemon` and `game-v1`
- a funded testnet address selected in `sui client active-address`

Example invocations:
```bash
# PvB: Player vs Bot
node v1/pvb.js --gamePackage <GAME_PKG_ID> --pokemonPackage <PKM_PKG_ID> --gasBudget 100000000

# PvP: Player vs Player
node v1/pvp.js --gamePackage <GAME_PKG_ID> --pokemonPackage <PKM_PKG_ID> --player2 <ADDRESS> --gasBudget 100000000
```

See `cli/README.md` for full argument details if available. If not, run:
```bash
node v1/pvb.js --help
node v1/pvp.js --help
```

### Development Notes

- `packages/pokemon`
  - `stats.move`: definitions and helpers for base stats
  - `pokemon_v1.move`: core damage formula and related utilities
- `packages/game-v1`
  - `arena.move`: game arena setup and lifecycle
  - `arena_pvp.move`: PvP-specific arena logic
  - `battle.move`: battle flow and damage application

Design goal: keep the formula reusable while letting `game-v1` evolve independently (new modes, items, abilities).

### Roadmap
- Expanded test suite for `game-v1`
- More battle mechanics (status, crits, STAB, type chart, accuracy/evasion)
- Matchmaking and lobbies for PvP
- Example front-end dApp
- On-chain storage for player profiles and battle history

### Contributing
Issues and PRs are welcome. Please:
- Keep modules small and cohesive
- Add/extend tests where possible
- Follow Move best practices and Sui module conventions

### Contact
For questions or collaboration, reach out on Telegram: [@lorine93s](https://t.me/lorine93s)

### License
Apache-2.0. See [LICENSE](./LICENSE).
