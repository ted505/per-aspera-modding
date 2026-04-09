# Per Aspera Game Loop Analysis

Analysis of all production chains, resource flows, and game phase progression derived from the base game datamodel YAML files.

---

## Resources

### Mined (from planetary veins)

| Resource | Tier 1 Rate | Tier 2 Rate | Tier 3 Rate | Power (T1/T2/T3) |
|---|---|---|---|---|
| Aluminum | 0.1/day | 0.15/day | 0.2/day | 5 / 10 / 15 MW |
| Silicon | 0.1/day | 0.15/day | 0.2/day | 5 / 10 / 15 MW |
| Iron | 0.1/day | 0.15/day | 0.2/day | 5 / 10 / 15 MW |
| Carbon | 0.1/day | 0.15/day | 0.2/day | 5 / 15 / 20 MW |
| Chemicals | 0.1/day | 0.15/day | 0.2/day | 10 / 15 / 20 MW |
| Water | 0.1/day | 0.15/day | 0.2/day | 10 / 15 / 20 MW |
| Uranium | 0.01/day | — | — | 5 MW |

**Uranium has no tier upgrades.** At 0.01/day it takes 100 days to mine one unit — 10× slower than everything else.

### Manufactured (by factories)

| Resource | Inputs | Rate (T1/T2/T3) | Power (T1/T2/T3) |
|---|---|---|---|
| Steel | Iron ×2 + Carbon ×1 | 0.1 / 0.15 / 0.2 /day | 5 / 10 / 20 MW |
| Glass | Silicon ×2 | 0.1 / 0.15 / 0.2 /day | 5 / 10 / 20 MW |
| Electronics | Silicon ×1 + Aluminum ×1 | 0.1 / 0.15 / 0.2 /day | 5 / 10 / 20 MW |
| Parts | Steel ×1 + Aluminum ×1 | 0.1 / 0.15 / 0.2 /day | 10 / 15 / 20 MW |
| Polymers | Chemicals ×2 + Carbon ×1 | 0.1 / 0.15 / 0.2 /day | 5 / 10 / 20 MW |
| Food (factory) | Water ×1 + Chemicals ×1 | 0.1 / 0.15 / 0.2 /day | 5 / 10 / 20 MW |
| Worker Drones | Parts ×1 + Electronics ×1 + Glass ×1 + Aluminum ×1 | 0.025 / 0.035 / 0.05 /day | 15 / 25 / 35 MW |

### Food from Farms (GreenMars)

| Building | Rate | Power | Inputs | Construction Cost | Notes |
|---|---|---|---|---|---|
| Crater Farm | 0.025/day | 50 MW | None | Al×25, Glass×40, Chems×25 | Requires crater vein, pipes ×20 |
| Open Farm | 0.25/day | 80 MW | None | Al×25, Parts×25, Elec×25, Chems×25 | Requires pipes ×30 |
| Advanced Open Farm | 1.0/day | 160 MW | None | Al×50, Parts×50, Elec×50, Chems×50 | Requires pipes ×50 |

Farms consume no ongoing resources — only power and water pipes. The Advanced Open Farm at 1.0 food/day is the most efficient food source in the game once water infrastructure exists.

---

## Full Production Chain Map

```
MINED:
  Iron ──────────────────┐
  Carbon ────────────────┤─── Steel Factory ──→ Steel ─┐
                         │                             │
  Silicon ───────────────┤─── Glass Factory ──→ Glass  │
  Silicon ───────────────┤                             │
  Aluminum ──────────────┼─── Electronics Factory ─→ Electronics
                         │                             │
                         ├─── Parts Factory ───────→ Parts ──┐
  Steel ─────────────────┘                                   │
  Aluminum ──────────────────────────────────────────────────┘

  Chemicals ─┬─ Food Factory (+ Water) ──────────────→ Food
             └─ Polymers Factory (+ Carbon) ─→ Polymers

  Uranium ────── Fission Plant construction only (base game)

MANUFACTURED:
  Parts + Electronics + Polymers ──→ [Mod: Consumer Goods]
  Chemicals + Water + Carbon ──────→ [Mod: Fuel]
  Parts + Electronics + Glass + Aluminum ──→ Worker Drones
  Polymers + Electronics ──→ Repair Drones (Maintenance Facility)
  Polymers + Electronics ──→ Military Drones (Campaign)
```

### Full chain for: Consumer Goods (mod)
```
Iron Mine → Steel Factory ─┐
Carbon Mine ───────────────┤ → Parts Factory ─┐
Aluminum Mine ─────────────┘                  │
                                              ├─→ Consumer Goods Factory → Consumer Goods
Silicon Mine → Electronics Factory ───────────┤
Aluminum Mine ─────────────────────────────────┤
                                              │
Chemicals Mine → Polymers Factory ────────────┘
Carbon Mine ─────────────────────────────────────
```

### Full chain for: Fuel (mod)
```
Chemicals Mine ─┐
Water Mine ─────┼─→ Fuel Refinery → Fuel
Carbon Mine ────┘
```

---

## Power Generation

| Source | Output | Construction | Notes |
|---|---|---|---|
| Solar Panel Field | 40 MW | Al×2, Elec×2, Glass×3 | No inputs |
| Wind Turbine | 50 MW | Al×3, Elec×2, Steel×2 | No inputs |
| Areothermal Plant | 200 MW | Al×5, Steel×10, Elec×5 | Requires heat vein |
| Fission Plant | 100 MW | Al×10, Steel×10, Elec×10, Uranium×10 | No ongoing inputs (base game) |
| Fusion Plant | 600 MW | Al×10, Steel×10, Elec×25 | No inputs, highest output |
| Landing Site | 20 MW | — | Starter building |
| Battery | Storage only (2000) | Al×3, Steel×3, Elec×3, Chems×3 | — |

---

## Colonization

### Colony types

| Building | Colonists | Inputs per cycle | Research out | Power | Construction |
|---|---|---|---|---|---|
| Basic Colony | 100 | Food×2 + Water×2 | 20 RP / 50 days | 12 MW | Al×2, Steel×2, Glass×2 |
| Small Colony | 250 | Food×5 + Water×5 | 60 RP / 50 days | 30 MW | Al×6, Steel×6, Glass×6 |
| Medium Colony | 500 | Food×10 + Water×10 | 150 RP / 50 days | 60 MW | Al×18, Steel×18, Glass×18 |
| Dome Colony | 1,000 | Food×15 | 300 RP / 50 days | 120 MW | Al×20, Steel×20, Glass×20 | Needs crater, pipes×5 |
| Tent City | 2,500 | Food×20 | 900 RP / 50 days | 200 MW | Al×30, Steel×30, Glass×30 | Needs pressure, pipes×10 |
| Open City | 10,000 | Food×30 | 4,000 RP / 50 days | 400 MW | Al×50, Steel×50, Glass×50 | Needs atmosphere, pipes×20 |
| Metropolis | 200,000 | Food×150 | 50,000 RP / 100 days | 4,000 MW | Al×500, Steel×500, Glass×500 | Needs atmosphere, pipes×100 |
| Research Lab | 50 | Food×1 + Water×1 | 10 RP / 27.8 days | 6 MW | Al×2, Steel×2, Glass×2 | Needs special site |
| Autonomous Research Facility | 0 | None | 100 RP / 10 days | 100 MW | Polymers×10, Elec×10, Parts×10, Uranium×10 | Campaign only |

Key: The three largest GreenMars colonies (Dome, Tent, Open City, Metropolis) consume **food only** — no water. Water consumption stops being a colonization input at the Dome tier.

### Research rates
| Colony type | RP/day |
|---|---|
| Basic Colony (×1) | 0.4 |
| Medium Colony (×1) | 3.0 |
| Dome Colony (×1) | 6.0 |
| Tent City (×1) | 18.0 |
| Open City (×1) | 80.0 |
| Metropolis (×1) | 500.0 |
| Autonomous Research Facility (×1) | 10.0 |

A single Metropolis generates more research per day than ~62 Tent Cities.

---

## Terraforming Buildings

| Building | Inputs | Gas Effect | Power | Construction | Notes |
|---|---|---|---|---|---|
| GHG Factory | Chemicals ×1 (base) | GHG: +0.00005/day | 10 MW | Al×5, Steel×5 | Heats planet |
| Nuclear Nitrate Extractor | None | N₂: +0.01/day | 10 MW | Al×10, Steel×10, Uranium×10 | Needs nitrate vein |
| Nuclear Carbonate Extractor | None | CO₂: +0.0005/day | 10 MW | Al×5, Steel×5, Uranium×5 | Needs carbon vein |
| Oxygen Release Plant | Water ×1 + Chemicals ×1 | O₂: +0.0005/day | 10 MW | Al×5, Steel×5, Polymers×5 | |
| Oxygen Capture Plant | None | O₂: −0.005/day | 10 MW | Al×10, Steel×10, Polymers×10 | Removes excess O₂ |
| Biodome | None | Lichen/plants | 10 MW | Al×10, Steel×10, Glass×15 | Pipes ×20 |
| Aquadome | None | Cyanobacteria | 10 MW | Al×10, Steel×10, Glass×15 | |
| Animal Sanctuary | None | Animals | 10 MW | Al×15, Steel×15, Polymers×15, Glass×15 | Needs crater, pipes ×10 |

---

## Technology Tree Summary

Three lanes: **Engineering**, **Life Support/Biology**, **Space** (implied by spaceport unlocks).

### Engineering Lane
| Tech | Cost | Unlocks |
|---|---|---|
| Lane 0 | 0 RP | Building limit +1 |
| Wind Turbine | 150 RP | power_wind_turbine |
| Storage | 150 RP | storage_basic |
| Lane 1 | 150 RP | Building limit +1 |
| Battery | 750 RP | power_battery_basic |
| Mine Upgrade 2 | 750 RP | All tier 2 mines |
| Fission Plant | 750 RP | power_fission_plant + building_uranium_mine |
| Factory Upgrade 2 | 750 RP | All tier 2 factories |
| Lane 2 | 750 RP | Building limit +1 |
| Mine Upgrade 3 | 2,500 RP | All tier 3 mines |
| Nuclear Carbonate Extraction | 2,500 RP | building_nuclear_carbonate_extractor |
| Way Better / Way Best | 750 / 2,500 RP | Improved road types |
| Factory Upgrade 3 | 2,500 RP | All tier 3 factories |

### Life Support / Biology Lane
| Tech | Cost | Unlocks |
|---|---|---|
| Lane 0 | 0 RP | Colonist limit +1 |
| Colony Small | 150 RP | building_colony_small |
| Lane 1 | 150 RP | Colonist limit +1 |
| Colony Medium | 750 RP | building_colony_medium |
| GHG Factory | 750 RP | building_terraforming_ghg_factory |
| Water Distribution | 750 RP | building_water_plant, water_hub, water_converter |
| Lane 2 | 750 RP | Colonist limit +1 |
| Oxygen Release Plant | 2,500 RP | building_terraforming_oxygen_release_plant |
| Bioengineered Lichen | 2,500 RP | building_terraforming_biodome |
| Bioengineered Cyanobacteria | 2,500 RP | building_terraforming_aquadome |
| Lane 3 | 2,500 RP | Colonist limit +1 |
| Colony Dome | 10,000 RP | building_colony_dome_small |
| Crater Farm | 10,000 RP | building_farm_crater + building_terraforming_animal_sanctuary |
| Colony Tent | (10,000+) | building_colony_tent_city, building_farm_open |
| Colony Tent 2 | (10,000+) | building_colony_open_city, building_farm_open_advanced |

---

## Game Phase Analysis

### Early Game (Days 0–100 | 0–750 RP)

**Starting conditions:** Landing site gives 20 MW, 1,200 storage, and 5 aluminum + 20 steel + 5 electronics.

**Build priority:**
1. Aluminum Mine (enables all construction)
2. Worker Relay
3. Silicon Mine → Electronics Factory (silicon + aluminum → electronics)
4. Iron Mine + Carbon Mine → Steel Factory
5. Renewable power (solar 40 MW or wind 50 MW — no inputs)
6. Basic Colony (needs food×2 + water×2; requires Water Mine + Food Factory + Chemicals Mine)

**Resource flow:**
- Aluminum: primary bottleneck (used in all construction AND electronics AND parts)
- Steel: secondary bottleneck (iron×2 + carbon×1; used everywhere in construction)
- Electronics: needed for drone factories, more factories, colonies
- Food: first manufactured resource the player must produce

**Research rate:** ~2–6 RP/day (1–2 basic colonies). Technologies at 150 RP unlock in ~25–75 days.

**Power budget:** 20–80 MW

---

### Mid Game (Days 100–400 | 750–10,000 RP)

**Key unlocks at 750 RP each:**
- Tier 2 mines (+50% output)
- Tier 2 factories (+50% output)
- Fission Plant (100 MW, uranium construction cost, no inputs in base game)
- Water Distribution (enables GreenMars pipe network and large colonies)
- GHG Factory (begin terraforming)
- Colony Medium (500 colonists, 150 RP/50 days)

**Emerging build order:**
- Scale all mines to tier 2
- Add Polymers Factory (chemicals×2 + carbon×1; needed for maintenance, military, Open Cities later)
- Add Parts Factory (steel×1 + aluminum×1; feeds consumer goods and drones)
- Water plant + hubs for farms and dome colonies
- GHG factories to begin heating planet

**Bottlenecks:**
- **Chemicals** is now dual-use: Food Factory (×1 per 10 days) AND Polymers Factory (×2 per 10 days). A single tier-2 mine (0.15/day) can barely feed one factory of each type.
- **Power** scales rapidly with factory count. Moving from 100 MW to 400 MW requires multiple areothermal or fission plants.
- **Food** demand grows sharply. To unlock dome colonies (1,000 colonists), you need Food×15 per 50 days. Three tier-2 food factories is the minimum.

**Research rate:** 10–50 RP/day (mix of medium colonies and labs). 750 RP technologies unlock in 15–75 days each.

**Power budget:** 200–600 MW

---

### Late Game (Days 400+ | 10,000+ RP)

**Key unlocks:**
- Tier 3 mines and factories
- Dome Colony (1,000 colonists, crater required) → 6 RP/day
- Crater Farm (construction-heavy but no ongoing inputs)
- Open/Advanced Farms (1 food/day; requires pipes infrastructure)
- Fusion Plant (600 MW, high electronics cost but no ongoing inputs)
- Nitrogen and CO2 extractors (atmosphere building)
- Lichen/Cyanobacteria biodomes
- Tent City (needs pressurized atmosphere)
- Open City (needs breathable atmosphere)

**Production scaling challenge:**

To sustain a Metropolis (200,000 colonists):
- Food ×150 per 100 days = 1.5 food/day → 2 Advanced Open Farms (at 1.0/day each)
- Water pipes ×100 → requires 2–3 Water Plants
- Power: 4,000 MW for the Metropolis alone, plus all supporting factories
- Research output: 500 RP/day per Metropolis

A single Metropolis requires more power than most mid-game infrastructure combined.

**Power budget:** 2,000–8,000+ MW

---

## Key Resource Observations

### Resources with robust ongoing demand
| Resource | Ongoing sinks |
|---|---|
| Aluminum | All factory inputs (electronics, parts), all construction, drone factory |
| Steel | Parts factory, all construction, drone factory |
| Silicon | Electronics factory, glass factory |
| Iron | Steel factory |
| Carbon | Steel factory, polymers factory |
| Chemicals | Food factory, polymers factory |
| Water | Food factory, small/medium colonies, research labs, oxygen release plant |
| Electronics | Drone factory, maintenance facility, military drone factory |
| Parts | Drone factory, consumer goods (mod) |
| Polymers | Maintenance facility, military drone factory, consumer goods (mod) |
| Food | All colonies (ongoing) |

### Resources that become idle in the late game (base game)
| Resource | Problem |
|---|---|
| **Glass** | Used only in construction and drone factories. No ongoing production sink. Once built, glass mines accumulate. |
| **Steel** | Primarily construction. Parts factory provides some sink, but demand trails off after build phase. |
| **Uranium** | Used only for Fission Plant construction (×10) and nuclear extractors (×10). No ongoing consumption. At 0.01/day mining rate, the mine eventually produces far more than can be used. |
| **Water** | Drops out of colonization at the Dome tier. Large GreenMars colonies only need food. Water mines idle unless food factories or oxygen release plants consume them. |

### The chemicals bottleneck
Chemicals is the tightest resource in mid-game:
- Food Factory: Chemicals ×1 per 10 days
- Polymers Factory: Chemicals ×2 per 10 days
- GHG Factory (base): Chemicals ×1 per 10 days (ongoing terraforming drain)
- Tier-2 chemicals mine: 0.15/day

One tier-2 mine can supply one food factory + half a polymers factory. Scaling colonies and terraforming simultaneously requires multiple chemicals mines.

### Water pipe infrastructure overhead
The GreenMars pipe system adds a hidden complexity layer:
- Water Plant: produces 40 pipe capacity, cluster radius 250m
- Each Advanced Open Farm consumes 50 pipe capacity → one farm needs more than one Water Plant's output
- Metropolis consumes 100 pipe capacity alone
- Spatial layout matters: buildings outside pipe cluster radius receive no water delivery

---

## Power Scaling Summary

| Phase | Approx. power needed |
|---|---|
| Early game (landing + basic) | 50–120 MW |
| Mid game (full factory array) | 200–600 MW |
| Late game (dome colonies + factories) | 600–2,000 MW |
| Endgame (Metropolis + full terraform) | 4,000–8,000+ MW |

A Metropolis alone draws 4,000 MW — more than 6 Fission Plants or almost 7 Areothermal Plants. Fusion Plants (600 MW each, no inputs) become essential at this scale.

---

## Mod Impact on Resource Flow

With the ExpandedSupplyChains mod applied, the following changes to the base flow occur:

| Change | Effect |
|---|---|
| GHG Factory: Chemicals → Fuel | Chemicals is freed from terraforming duty; fuel becomes the primary GHG input |
| All spaceport projects require Fuel | Fuel becomes an ongoing logistics resource, not just a construction-phase cost |
| Fuel Refinery: Chemicals×2 + Water×1 + Carbon×1 → Fuel | Chemicals, water, and carbon all get an additional active sink |
| Consumer Goods Factory: Parts×1 + Elec×1 + Polymers×1 → Consumer Goods | Parts, electronics, and polymers all get an ongoing late-game sink |
| Large colonies consume Consumer Goods | Consumer goods becomes essential from Dome Colony onwards |
| Fission Plant: Uranium×1 ongoing consumption | Uranium mine remains active throughout game rather than being a one-time build cost |

**Remaining idle resources (with mod):** Glass still has no ongoing production sink. Steel demand drops off after the late construction phase.
