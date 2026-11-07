# Usage Guide

Complete guide to using Autoclicker mod features.

## Installation

1. Install [Fabric Loader](https://fabricmc.net/use/) for your Minecraft version
2. Install [Fabric API](https://modrinth.com/mod/fabric-api) (matching version)
3. Download Autoclicker JAR for your version from [Releases](https://github.com/vihaanvp/autoclicker-mod/releases)
4. Place JAR in `mods/` folder
5. Launch Minecraft

## Default Keybinds

| Key | Action |
|-----|--------|
| **F7** | Toggle master volume mute/unmute |
| **F8** | Toggle Auto Attack on/off |
| **F9** | Toggle Auto Planting on/off |
| **F10** | Open configuration GUI |

> **Change keybinds**: Options → Controls → Key Binds → Autoclicker

## Auto Attack (F8)

### What It Does
Automatically attacks valid entities you're looking at, at a configurable interval.

### Supported Targets
| Target | Default | Config Option |
|--------|---------|---------------|
| Armor Stands | ✅ Enabled | `attackArmorStands` |
| Hostile Mobs | ✅ Enabled | `attackHostileMobs` |
| Neutral Mobs | ❌ Disabled | `attackNeutralMobs` |
| Passive Mobs | ❌ Disabled | (Not supported) |

### How to Use
1. Aim at a valid target (crosshair on entity)
2. Press **F8** to enable
3. Mod attacks automatically at set interval
4. Press **F8** again to disable

### Settings
- **Interval**: 20 ticks (1 second) default, adjustable 1-100 ticks
- **Randomness**: ±5 ticks default, makes timing less predictable
- **Auto-disable**: Turns off after 120s of no successful attacks

### Tips
- Works while AFK (if not paused)
- Works with GUIs open (chest, inventory, etc.)
- Doesn't interfere with manual clicks
- Spear/pike weapons (tridents, modded spears) supported via special attack logic

## Auto Planting (F9)

### What It Does
Automatically places/plant items from your hand onto valid blocks you're looking at.

### Supported Items (Hold in Main Hand)

**Seeds & Crops:**
- Wheat, Beetroot, Melon, Pumpkin seeds
- Carrots, Potatoes, Nether Wart
- Torchflower Seeds, Pitcher Pods (1.20+)

**Saplings:**
- All vanilla saplings (Oak, Spruce, Birch, Jungle, Acacia, Dark Oak)
- Cherry, Mangrove saplings (1.20+)
- Crimson/Warped Fungus (on Nylium)

**Other:**
- Sugar Cane (on sand/dirt near water)
- Cactus (on sand)
- Bamboo (on grass/dirt/moss)
- Kelp/Sea Pickle (underwater)
- Mushrooms (on nylium/mycelium/podzol)
- Vines, Glow Berries, Hanging Roots

### Bone Meal Automation
Hold **Bone Meal in off-hand** while planting:
- Auto-detects bone-mealable blocks (crops, saplings, etc.)
- Uses bone meal first, then plants
- Configurable: `useBoneMeal` (default: on)

### How to Use
1. Hold plantable item in main hand
2. (Optional) Hold bone meal in off-hand
3. Aim at valid block (farmland, grass, nylium, etc.)
4. Press **F9** to enable
5. Mod places automatically at set interval
6. Press **F9** again to disable

### Settings
- **Interval**: 5 ticks (0.25s) default, adjustable 1-40 ticks
- **Randomness**: ±3 ticks default
- **Auto-disable**: Turns off after 120s of no successful placements

## Auto Refill

### What It Does
Automatically replaces depleted items in your hand with same items from inventory.

### How It Works
1. When auto-attack/place uses an item, mod "remembers" it
2. On next use, checks if current item is low/empty
3. Searches inventory (backpack → hotbar → off-hand) for replacement
4. Swaps automatically

### Settings
| Option | Default | Description |
|--------|---------|-------------|
| `autoRefillMainHand` | ✅ On | Refill main hand |
| `autoRefillOffHand` | ✅ On | Refill off-hand |
| `refillCountThreshold` | `0` | Refill when count ≤ this (0 = only when empty) |
| `refillDurabilityThreshold` | `1%` | Refill when durability % ≤ this |

### Example Scenarios
- **Tree farm**: Hold saplings + bone meal → auto-plant + auto-bonemeal + auto-refill
- **Mob grinder**: Hold sword → auto-attack + auto-refill when sword breaks
- **Bartering**: Hold gold ingots in off-hand → auto-refill for piglin bartering

## Smart Mute System (F7)

### Three Priority Levels

1. **Manual (F7)** - Highest
   - Press F7 to force mute/unmute
   - Overrides all other mute logic
   - Remembers your volume

2. **Linked** - Medium
   - Mutes when Auto Attack enabled + `muteOnAutoAttack` on
   - Mutes when Auto Plant enabled + `muteOnAutoPlace` on
   - Restores when both disabled

3. **Minimized** - Lowest
   - Mutes when Minecraft window loses focus
   - Restores when window focused
   - Requires `muteWhenMinimized` on

### Persistence
- Volume saved to disk on every mute
- **Survives force-close** (Task Manager, launcher "Close Game")
- Automatically restores on next game launch
- No manual intervention needed

## Configuration GUI (F10)

Press **F10** to open the in-game config screen.

### Sections
1. **Auto Attack** - Enable, interval, randomness, targets
2. **Auto Place** - Enable, interval, randomness, bone meal
3. **Auto Refill** - Main/off-hand toggles, thresholds
4. **Auto Disable** - Timeout settings
5. **Auto Mute** - All mute options

### Buttons
- **Done** - Save changes and close
- **Cancel** - Revert to values when GUI opened

## Common Farm Setups

### XP Farm (Mob Grinder)
```
Settings:
- Auto Attack: ON
- Attack Interval: 20 (1s)
- Attack Hostile Mobs: ON
- Auto Refill Main Hand: ON (sword)
- Mute on Auto Attack: ON
```

### Tree Farm
```
Settings:
- Auto Place: ON
- Place Interval: 5 (0.25s)
- Use Bone Meal: ON
- Hold: Saplings (main) + Bone Meal (off-hand)
- Auto Refill Both: ON
- Refill Count Threshold: 1
```

### Semi-Auto Crop Farm
```
Settings:
- Auto Place: ON
- Place Interval: 10
- Use Bone Meal: ON
- Hold: Seeds (main) + Bone Meal (off-hand)
- Aim at farmland, press F9
```

### Piglin Bartering
```
Settings:
- Auto Place: ON (right-click to barter)
- Place Interval: 10-20
- Hold: Gold Ingots (off-hand)
- Auto Refill Off Hand: ON
- Refill Count Threshold: 5
```

## Troubleshooting

### Keybinds Show as Numbers
- **Cause**: Old config used Windows VK codes
- **Fix**: Press the key in Controls menu to rebind, or delete `autoclicker.json`

### Auto Attack Not Working
- Check you're looking at valid target (crosshair on entity)
- Verify target type is enabled in config
- Check attack interval isn't too high
- Ensure not in creative mode (attack strength check)

### Auto Place Not Working
- Check you're holding valid plantable item
- Verify you're looking at correct block type
- Bone meal: ensure off-hand has bone meal, block is bone-mealable
- Check place interval

### Volume Stuck at 0
- Press F7 to manually unmute
- Delete `config/autoclicker_mute_state.txt`
- Restart game

### Mod Not Loading
- Verify Fabric API version matches Minecraft version
- Check JDK version (25 for 26.x, 21 for 1.20+)
- Check logs for errors

## Anti-Cheat Considerations

⚠️ **Warning**: This is an automation mod. While it includes anti-detection features:

- Randomized timing intervals
- Event-driven (no fake input simulation)
- No cursor hijacking

**It may still be detected by server anti-cheat systems.**

- Always check server rules before using
- Some servers explicitly allow/deny autoclickers
- Use at your own risk

## Multiplayer Notes

- **Client-side only** - no server install needed
- Works on any server with Fabric client
- Respects server-side attack cooldowns (attack strength)
- Doesn't send fake packets - uses vanilla interaction system

## Uninstalling

1. Remove JAR from `mods/`
2. (Optional) Delete `config/autoclicker.json` and `config/autoclicker_mute_state.txt`
3. Restart game

## Support

- **Issues**: [GitHub Issues](https://github.com/vihaanvp/autoclicker-mod/issues)
- **Discussions**: [GitHub Discussions](https://github.com/vihaanvp/autoclicker-mod/discussions)
- **QQ Group**: 172442817