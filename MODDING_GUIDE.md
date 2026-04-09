# Per Aspera Modding Guide

Per Aspera is a Mars terraforming strategy game. Mods are defined entirely through YAML files and loaded by the game engine at startup. This document summarizes how the modding system works, based on the official "Modding v2.0" PDF and the base game's `datamodel/` folder.

## Mod Location

Mods live in:

```
{Per Aspera install}/Per Aspera_Data/StreamingAssets/Mods/{YourModFolder}/
```

All file paths in YAML are relative to the mod's root folder. For example, `Icons/MyIcon` resolves to `...Mods/{YourModFolder}/Icons/MyIcon`.

> **Warning:** Using mods or console commands permanently disables achievements for that savegame.

## Manifest Structure

Every mod requires a `manifest.yaml` at its root. This is the entry point that tells the game what to load.

```yaml
modId: "MyMod"                    # Unique identifier for the mod

compatibleGameVersions:           # Game versions this mod works with
  - "1.8"                         # First two numbers from the main menu version string

requiredMods:                     # Other mods this one depends on (loaded first)
  - "SomeOtherMod"

optionalManifests:                # Sub-manifests loaded only when their requiredMods are present
  - optional-campaign.yaml        # e.g. a sub-manifest with requiredMods: ["Campaign"]

# --- Setups (optional, one file each) ---
# These define global variables, system parameters, or world initialization.
generalSetup: GeneralSetup.yaml
initialSetup: InitialSetup.yaml
droneSetup: DroneSetup.yaml
frontendSetup: FrontendSetup.yaml
planetSetup: PlanetSetup.yaml
combatSetup: CombatSetup.yaml
waySetup: WaySetup.yaml
maintenanceSetup: MaintenanceSetup.yaml
scatterSetup: ScatterSetup.yaml

# --- Type Lists (optional, multiple files allowed per entry) ---
# These define game objects: buildings, resources, technologies, etc.
building:
  filenames:
    - building.yaml
  replace: false          # false = additive (merge with base game), true = replace all prior entries

resource:
  filenames:
    - resource.yaml
  replace: true

technology:
  filenames:
    - technology-engineering.yaml
    - technology-space.yaml
    - technology-biology.yaml
  replace: false
```

### The `replace` Flag

Each type list has a `replace` field:
- **`false`** (additive): Your items are merged on top of previously loaded items (base game + earlier mods). Items with the same key override individual fields; new keys add new items.
- **`true`**: All previously loaded items in that category are discarded and replaced entirely by yours.

## Supported Features

You can add or modify:
- **Buildings** (mines, factories, power, terraforming, military, colonies)
- **Technologies** (tech tree nodes across Engineering, Space, Biology branches)
- **Resources** (mined, manufactured, placement, released)
- **Quests, Random Events, Special Projects**
- **Knowledge base entries**
- **Scatter zones, Points of interest**
- **Custom icons** (PNG files referenced by path)
- **Localization** (new languages or extending existing ones via CSV)

### Limitations

- You **cannot** add new custom 3D prefabs or meshes for buildings, resources, special projects, or roads.
- You must reuse existing prefabs. The game provides extra mod-specific prefabs under the `ModBuildings/` path:
  - `ModColony_1` through `_3`
  - `ModCoreBuilding01_1` through `02_3`
  - `ModFactory01_1` through `02_3`
  - `ModMine01_1` through `02_3`
  - `ModPowerBuilding01_1` through `02_3`
  - `ModTerraformingBuilding_1` through `_3`

## Overriding Base Game Items

To modify an existing item, create an entry in your mod YAML with the **same key** as the base game item. Only the fields you specify are overridden; everything else stays unchanged.

```yaml
# Override the Worker Hub's name and power consumption (all other fields unchanged)
building_drone_base:
  name: "Other name"
  powerConsumption: 25.0
```

### Override Behaviors

| What you do | Result |
|---|---|
| Set a string or number field | That field is replaced |
| Add to a dictionary (e.g. `requiredConstructionResources`) with an existing key | That entry is replaced |
| Add to a dictionary with a new key | The new entry is added |
| Use the `!replace` tag on a collection | The entire collection is replaced |

```yaml
# Add uranium to construction cost (existing costs remain)
building_drone_base:
  requiredConstructionResources:
    !resource resource_uranium: 10

# Replace ALL construction costs with just uranium
building_drone_base:
  !replace requiredConstructionResources:
    !resource resource_uranium: 10
```

## Mod Stacking

Multiple mods can be enabled simultaneously. They load top-to-bottom in the Mods menu. Later mods override earlier ones (and the base game) using the same key-based override system.

## YAML Tag Reference

The game uses custom YAML tags to reference other data types:

| Tag | Usage | Example |
|---|---|---|
| `!resource` | Reference a resource type | `!resource resource_aluminum: 5` |
| `!technology` | Reference a technology | `!technology technology_lane_engineering_0` |
| `!building` | Reference a building type | `!building building_drone_base` |
| `!buildingCategory` | Reference a building category | `!buildingCategory category_power` |
| `!knowledge` | Reference a knowledge entry | `!knowledge knowledge_resource_aluminum` |
| `!project` | Reference a special project | `!project project_colonist_migration` |
| `!popup` | Reference a popup | `!popup popup_tutorial_building_management` |
| `!replace` | Replace a collection entirely | `!replace requiredConstructionResources:` |
| `!SpecialProjectControllerBase` | Special project controller | Used in project YAML |

## Console Commands for Testing

Open the console in-game with `~`. Key commands for mod testing:

- `UnlockBuilding <key>` - Unlock a specific building
- `UnlockAllBuildings` - Unlock all buildings
- `UnlockAllTech` - Unlock all technologies
- `UnlockAllSpecialProjects` - Unlock all special projects
- `FactionAddResearchPoints <amount>` - Add research points
- `BunchOfResources` - Add resources to selected/main building
- `FinishConstructions` - Instantly finish all pending constructions
- `FinishUpgrades` - Instantly finish all pending upgrades
- `RevealAllResourceVeins` - Reveal all resource veins on the map
- `Bind <KeyCode> <Command>` - Bind a key to a console command (e.g. `Bind U UnlockAllBuildings`)

## Publishing

Mods are published to the **Steam Workshop** via the in-game Mods menu "Publish" button. You provide a mod name, description, folder path, preview images, and tags.

## Tools

The official recommendation is to use Notepad++ with YAML language mode. Tabs and spaces are not interchangeable in YAML.
