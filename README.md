# Open World RPG

> An action RPG built with **Unreal Engine 5.4** featuring fluid combat, AI-driven enemies, an open-world landscape, and a dungeon environment — crafted entirely in C++ and Blueprints.

---

## Overview

**Open World RPG** is a third-person action role-playing game developed from the ground up in Unreal Engine 5.4. The project showcases advanced gameplay systems including directional hit reactions, stamina-based combat, AI state machines, physics-driven destructibles, and a rich open-world environment built with Landmass and Water plugins.

---

## Features

### Player Character System
- **Full C++ character controller** (`ARPG_Character`) built on a shared `ABaseCharacter` base class
- **Enhanced Input System (EIS)** — modern input action/mapping context pipeline for movement, look, jump, attack, dodge, and interact
- **Groom Hair System** — real-time strand-based hair simulation via Unreal's `UGroomComponent` (requires HairStrands & AlembicHairImporter plugins)
- **Weapon equip states** — supports one-handed and two-handed weapon modes with distinct equip/unequip animation montages
- **Weapon drop & pickup** — weapons can be dropped and picked up dynamically via sphere overlap detection
- **Motion Warping** — attack animations use translation & rotation warp targets for precise positional alignment

### Combat System
- **Attack combos** — randomized montage section playback for varied attack animations (one-handed & two-handed)
- **Box Trace hit detection** on weapon swing with field system breakable object support
- **Stamina cost system** — attacking and dodging consume stamina; stamina regenerates passively over time
- **Directional hit reactions** — impact point-based hit react direction (front, back, left, right)
- **Dodge mechanic** — stamina-gated dodge with dedicated dodging action state
- **Death system** — randomized death poses (7 variations) with mesh collision disabled on death
- **Hit sounds & particle effects** on impact points
- **Configurable weapon damage** exposed per weapon Blueprint

### Enemy AI
Three fully implemented enemy archetypes, each with their own AnimBP and montage sets:

| Enemy Type | Description |
|---|---|
| **SK_Knight** | Armored melee fighter with patrol and combat AI |
| **Raptor** | Fast creature enemy with dedicated attack, death, and hit-react montages |
| **Insect** | Creature enemy with blend-space idle/walk locomotion |

Each enemy features:
- **State machine AI** — Patrolling → Chasing → Attacking → Dead (managed via `EEnemyState` enum)
- **PawnSensing** — detects the player within a configurable sight range; triggers chase and combat
- **Patrol system** — waypoint-based patrol with randomized wait timers between points
- **Dynamic health bars** — world-space `UHealthBarComponent` shown on aggro, hidden when out of combat
- **Soul spawning on death** — enemies drop collectible soul pickups
- **Auto weapon spawning** — enemies spawn their own weapon at runtime
- **Configurable radii** — patrol radius, attack radius, combat radius, acceptance radius all tunable in the editor

### Item & Pickup System
- **Base `AItem` class** with sphere overlap logic and shimmer/levitation effects
- **Weapons** — equippable with one-handed and two-handed variants (Greatsword, Knight Sword, etc.)
- **Soul Pickups** — drift upward on spawn and award souls on player contact
- **Treasure Pickups** — 8 unique treasure types (Bracelet, Goblet, Gold Bar, Sapphire, Ruby, Emerald, Diamond, Small Bracelet) award gold on pickup
- **Health Pickups** — restore player health on contact with custom VFX material and sound

### Attribute System
`UAttributes` — a reusable `UActorComponent` driving all character stats:
- **Health** (current + max) with `ReceiveDamage()`
- **Stamina** (current + max) with `UseStamina()` and `RegenStamina()`
- **Gold** — accumulated from treasure pickups
- **Souls** — accumulated from enemy soul drops

### HUD & UI
- **`WBP_PlayerOverlay`** — in-game HUD with:
  - Health progress bar
  - Stamina progress bar
  - Gold counter
  - Souls counter
- **`BP_HealthBar`** — world-space enemy health bar widget component
- **Lock-on target reticle** (`WBP_LockOnTarget`) with configurable lock-on distance
- HUD is initialized via `ASlashHUD` and bound to the player controller on `BeginPlay`

### World & Levels
Three playable maps:

| Map | Description |
|---|---|
| **Land** | Large open-world landscape (~55 MB) built with the Landmass plugin, featuring terrain sculpting, foliage, water bodies (Water + WaterExtras plugins), and precomputed lighting |
| **Dungeon** | Interior dungeon level using the Medieval Dungeon asset pack with procedural room layout via `BPP_Dungeon` |
| **Slash** | Prototype/test level for combat iteration |

### Plugins Used
| Plugin | Purpose |
|---|---|
| `HairStrands` | Real-time strand hair simulation |
| `AlembicHairImporter` | Import Alembic groom assets |
| `Water` + `WaterExtras` | Lakes, rivers, and ocean water simulation |
| `Landmass` | Procedural landscape sculpting tools |
| `MotionWarping` | Warp animations to world targets during combat |
| `MotionTrajectory` | Trajectory prediction for Motion Matching |
| `ModelingToolsEditorMode` | In-editor mesh modeling |

### Animation
- **Motion Matching** — Echo character uses `PSD_EchoMM` pose search database (`PSS_Echo`) for responsive, data-driven locomotion
- **ABP_EchoMainState** — main state machine animation blueprint for the player character
- **ABP_Echo_IK** — IK pass animation blueprint for foot/hand IK
- **ABP_SlashCharacter** — legacy slash character animation blueprint
- **ABP_EnemyTemplate** — shared enemy animation blueprint template
- Dedicated AnimBPs per enemy: `ABP_KnightAnimBP`, `ABP_Raptor`, `ABP_Insect`

### Bird Pawn (Exploration Mode)
- `ABird` pawn with independent input mapping context for fly-through exploration
- Spring arm camera with free look
- Forward/strafe/look input actions

### Destructibles
- **Chaos Field System** integration — weapon hits trigger `CreateFieldSystemBreakObjects()` for physics-driven geometry destruction (`BP_fieldSystem`)

---

## Project Structure

```
RPG/
├── Source/RPG/
│   ├── Public/
│   │   ├── Characters/         # BaseCharacter, RPG_Character, CharacterTypes, AnimInstance
│   │   ├── Enemy/              # Enemy AI class
│   │   ├── Items/
│   │   │   ├── Item.h          # Base item class
│   │   │   ├── Weapon/         # Weapon class with box trace hit detection
│   │   │   └── Pickups/        # Soul, Treasure pickups
│   │   ├── HUD/                # PlayerOverlay, HealthBar, HealthBarComponent, SlashHUD
│   │   ├── Interfaces/         # HitInterface, PickupInterface
│   │   ├── Pawns/              # Bird exploration pawn
│   │   └── RPG_Components/     # Attributes component
│   └── Private/                # All .cpp implementations
├── Content/
│   ├── Maps/                   # Land, Dungeon, Slash levels
│   ├── Blueprints/
│   │   ├── Character/          # BP_RPGcharacter, AnimBPs, Input actions
│   │   ├── Enemy/              # SK_Knight, Raptor, Insect blueprints & AnimBPs
│   │   ├── Items/
│   │   │   ├── Weapon/         # All weapon BPs (Knight, Raptor, Insect, Greatsword, etc.)
│   │   │   └── Pickups/        # Soul, Health, 8x Treasure BPs
│   │   ├── HUD/                # WBP_PlayerOverlay, WBP_LockOnTarget, BP_HealthBar
│   │   └── FieldSystems/       # Chaos destruction Field System BP
│   ├── Characters/             # Hero skeletal meshes & textures
│   ├── Audio/                  # Sound assets
│   ├── Effects/                # Particle/Niagara effects
│   ├── MotionMatching/         # Echo pose search database & schema
│   ├── MedievalDungeon/        # Dungeon environment assets
│   ├── AncientContent/         # Ancient world environment assets
│   └── ...                     # Third-party asset packs
└── RPG.uproject                # Unreal Engine 5.4 project file
```

---

## Requirements

| Requirement | Version |
|---|---|
| Unreal Engine | **5.4** |
| Visual Studio | 2022 (with C++ Game Development workload) |
| Git LFS | Required for large binary assets |
| OS | Windows 10/11 (64-bit) |

> **Note:** Git LFS is required. This project tracks binary assets (`.uasset`, `.umap`, etc.) via Git LFS. Make sure LFS is installed before cloning.

---

## Getting Started

### Clone the Repository

```bash
# Install Git LFS first (if not already installed)
git lfs install

# Clone the repository
git clone https://github.com/Rithvik-gamedev/Open-World-RPG.git
cd Open-World-RPG
```

### Open the Project

1. Right-click `RPG.uproject` and select **"Generate Visual Studio project files"**
2. Open `RPG.sln` in Visual Studio 2022
3. Set the startup project to **RPG** and build in `Development Editor` configuration
4. Double-click `RPG.uproject` to launch the Unreal Editor

### First Run

- Open any of the maps in `Content/Maps/` (`Land.umap` for the open world, `Dungeon.umap` for the interior level)
- Press **Play** in the editor to start

---

## Controls (Default)

| Action | Key |
|---|---|
| Move | W A S D |
| Look | Mouse |
| Jump | Space |
| Attack | Left Mouse Button |
| Dodge | Left Shift |
| Interact / Pick Up | E |
| Drop Weapon | R |
| Equip / Unequip | E (while no item overlapping) |

---

## Asset Packs Used

- **Echo Character** (EchoContent) — Player character mesh & animations
- **Paragon Rampage / Paragon Minions** — Additional character assets
- **SKnight Modular** — Modular knight enemy
- **Medieval Dungeon** — Dungeon interior set
- **Ancient Treasures / Ancient Content** — Treasure & world props
- **QMS Fantasy Sword Pack** — Weapon meshes
- **Forest Animals Pack** — Wildlife assets
- **European Beech** — Foliage
- **MWLandscape Auto Material** — Landscape auto-material
- **Stylized Creatures Bundle** — Creature enemies (Raptor, Insect)
- **Mixamo** — Additional character animations
- **SFX Essentials FREE** — Sound effects

---

Built with Unreal Engine 5.4 · C++ · Blueprints
