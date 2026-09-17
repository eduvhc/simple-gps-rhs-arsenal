# Simple GPS for RHS Arsenal

![Simple GPS for RHS Arsenal](Docs/Assets/image.png)

An unofficial compatibility addon that adds the Simple GPS handheld receiver to the Equipment category of the RHS USAF and AFRF arsenals.

Source: https://github.com/eduvhc/simple-gps-rhs-arsenal

## Requirements

- [Simple GPS](https://reforger.armaplatform.com/workshop/B704D613E433276B-SimpleGPS)
- [RHS - Status Quo](https://reforger.armaplatform.com/workshop/595F2BF2F44836FB-RHS-StatusQuo)

## What it changes

- Adds one GPS entry to the RHS USAF Equipment catalog.
- Adds one GPS entry to the RHS AFRF Equipment catalog.
- Sets the supply cost to zero.

This addon contains only two catalog overrides (same GUID as the RHS `USMC_InventoryItems.conf` and `MSV_InventoryItems.conf`, delta-merged by the engine). It does not include or redistribute assets or code from either dependency.

## Testing in Workbench

Both dependencies must be in the Workbench Launcher project list (Workshop downloads live in `Documents\My Games\ArmaReforger\addons`; use **+ Add Project → Scan for Projects** on that folder if the project opens without RHS).

### 1. Quick check (Resource Browser)

Open `Configs/EntityCatalog/USMC/USMC_InventoryItems.conf` from this addon. Because it is an override, the editor shows the **full RHS catalog** with `NCM_GPS_Item` appended to the *Equipment* list. If you only see the GPS entry on its own, the `.meta` GUID no longer matches the RHS file and the override is not applied.

### 2. In-game check (World Editor)

Open `Dev/ArsenalTest.ent` from this addon and press **Play**. Walk to the two arsenal boxes, open the arsenal and check the **Equipment** tab: the GPS must be listed at supply cost 0 in both the USAF box (`ArsenalBox_USMC`) and the AFRF box (`ArsenalBox_RHS`).

The test world is a sub-scene of RHS's `Worlds/RHS_Atoll/Assets_Showcase_Basic.ent` with four entities added:

| Prefab | Why it is needed |
|---|---|
| `Prefabs/MP/Modes/Plain/GameMode_Plain.et` (vanilla) | Provides `SCR_EntityCatalogManagerComponent` and `SCR_ArsenalManagerComponent`. Without a game mode every arsenal is empty and the log shows `needs a entity catalog manager!`. |
| `Prefabs/MP/Managers/Factions/FactionManager_Editor.et` (RHS) | Registers the RHS factions. Arsenal boxes only carry a faction key (`RHS_USAF` / `RHS_AFRF`) and read the item catalog from the faction. It also requires the game mode to exist, otherwise it throws `NULL pointer … m_OnPlayerDisconnected` in `EOnInit`. |
| `ArsenalBox_USMC.et`, `ArsenalBox_RHS.et` (RHS) | Free arsenals (`m_fBuyMultiplier 0`) for the two factions this addon touches. |

Symptoms of a world without these managers: the arsenal opens with **"0 available"** and an empty grid, and the log prints `No GameMode present in the world, using fallback logic!`.

Do not ship changes to the dev world with a release unless they help testing; it has no mission header, so it is never listed as a playable scenario.

### 3. Final check

Host a dedicated server with the RHS Conflict scenario you intend to run, with this addon in `-addons`, and confirm the GPS in a base arsenal, including for a client that joins in progress.
