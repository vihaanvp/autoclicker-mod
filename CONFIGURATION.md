# Configuration Guide

Autoclicker provides extensive configuration options accessible via the in-game GUI (default **F10**) or by editing the config file directly.

## Configuration File Location

```
<minecraft_directory>/config/autoclicker.json
```

Example:
- Windows: `%appdata%\.minecraft\config\autoclicker.json`
- Linux: `~/.minecraft/config/autoclicker.json`
- macOS: `~/Library/Application Support/minecraft/config/autoclicker.json`

## In-Game GUI (Recommended)

Press **F10** (default) to open the configuration screen. All changes are saved automatically when you click "Done".

## Configuration Options

### Keybinds

| Option | Default | Description |
|--------|---------|-------------|
| `keyMute` | `296` (F7) | Toggle master volume mute/unmute |
| `keyToggleAttack` | `297` (F8) | Toggle Auto Attack on/off |
| `keyTogglePlace` | `298` (F9) | Toggle Auto Planting on/off |
| `keyOpenGUI` | `299` (F10) | Open configuration GUI |

> **Note**: Key codes use GLFW key codes (not Windows virtual key codes). F7-F10 = 296-299. The mod automatically migrates old VK codes (118-121) on load.

### Auto Attack

| Option | Default | Range | Description |
|--------|---------|-------|-------------|
| `autoAttackEnabled` | `false` | true/false | Enable/disable auto attack |
| `attackInterval` | `20` | 1-100 | Ticks between attacks (20 = 1 second) |
| `attackRandomnessEnabled` | `true` | true/false | Enable randomized attack intervals |
| `attackRandomness` | `5` | 0-20 | Randomness range in ticks (±value) |
| `attackArmorStands` | `true` | true/false | Attack armor stands |
| `attackHostileMobs` | `true` | true/false | Attack hostile mobs (zombies, skeletons, etc.) |
| `attackNeutralMobs` | `false` | true/false | Attack neutral mobs (endermen, piglins, etc.) |

**Randomness Calculation:**
```
Actual interval = attackInterval ± random(0, attackRandomness * 2) - attackRandomness
Example: interval=20, randomness=5 → range 15-25 ticks
Minimum interval is always 1 tick
```

### Auto Planting

| Option | Default | Range | Description |
|--------|---------|-------|-------------|
| `autoPlaceEnabled` | `false` | true/false | Enable/disable auto planting |
| `placeInterval` | `5` | 1-40 | Ticks between placements (5 = 0.25 seconds) |
| `placeRandomnessEnabled` | `true` | true/false | Enable randomized placement intervals |
| `placeRandomness` | `3` | 0-10 | Randomness range in ticks (±value) |
| `useBoneMeal` | `true` | true/false | Automatically use bone meal on crops |

**Supported Items (Main Hand):**
- Seeds: Wheat, Beetroot, Melon, Pumpkin, Torchflower, Pitcher Pod
- Crops: Carrot, Potato, Nether Wart
- Saplings: Oak, Spruce, Birch, Jungle, Acacia, Dark Oak, Cherry, Mangrove
- Nether: Crimson Fungus, Warped Fungus
- Other: Red/Brown Mushroom, Sugar Cane, Cactus, Bamboo, Kelp, Sea Pickle, Vines, Glow Berries

**Bone Meal Targets:**
- Crops, Saplings, Stem blocks, Sea Pickle, Kelp, Vines, Mushrooms, Flowers, Pitcher Crop

### Auto Refill

| Option | Default | Range | Description |
|--------|---------|-------|-------------|
| `autoRefillMainHand` | `true` | true/false | Auto-refill main hand when item runs low |
| `autoRefillOffHand` | `true` | true/false | Auto-refill off-hand when item runs low |
| `refillCountThreshold` | `0` | 0-63 | Refill when count ≤ threshold (0 = only when empty) |
| `refillDurabilityThreshold` | `1` | 0-100 | Refill when durability % ≤ threshold |

**How It Works:**
1. When auto-attack/place uses an item, the mod "remembers" the item type and slot
2. On next use, checks if current item matches memory and is below threshold
3. If so, searches inventory (backpack → hotbar → off-hand) for same item
4. Swaps automatically using vanilla inventory operations

**Refill Priority:**
1. Backpack (slots 9-35)
2. Hotbar (slots 0-8)
3. Off-hand (slot 40)

### Auto Disable (Timeout)

| Option | Default | Range | Description |
|--------|---------|-------|-------------|
| `autoDisableAttack` | `true` | true/false | Auto-disable attack after timeout |
| `autoDisablePlace` | `true` | true/false | Auto-disable place after timeout |
| `autoDisableTimeout` | `120` | 1-240 | Seconds of inactivity before auto-disable |

**Behavior:**
- Tracks last successful attack/place action
- If no successful action for `autoDisableTimeout` seconds, disables the feature
- Sends chat notification when auto-disabled
- Resets on manual toggle or reconnect

### Audio / Mute System

| Option | Default | Description |
|--------|---------|-------------|
| `muteOnAutoAttack` | `true` | Mute when auto attack is active |
| `muteOnAutoPlace` | `true` | Mute when auto place is active |
| `muteWhenMinimized` | `true` | Mute when Minecraft window loses focus |

**Priority System (Highest → Lowest):**
1. **Manual (F7)** - User explicitly toggles mute
2. **Linked** - Auto attack/place enabled with mute options on
3. **Minimized** - Window unfocused/minimized

**Persistence:**
- Master volume saved to `config/autoclicker_mute_state.txt` on mute
- Restored automatically on game launch
- Survives force-close (task manager, launcher "Close Game")

## Example Configuration

```json
{
  "keyMute": 296,
  "keyToggleAttack": 297,
  "keyTogglePlace": 298,
  "keyOpenGUI": 299,
  "autoAttackEnabled": false,
  "attackInterval": 20,
  "attackRandomness": 5,
  "attackRandomnessEnabled": true,
  "attackArmorStands": true,
  "attackHostileMobs": true,
  "attackNeutralMobs": false,
  "autoPlaceEnabled": false,
  "placeInterval": 5,
  "placeRandomness": 3,
  "placeRandomnessEnabled": true,
  "useBoneMeal": true,
  "autoRefillMainHand": true,
  "autoRefillOffHand": true,
  "refillCountThreshold": 0,
  "refillDurabilityThreshold": 1,
  "autoDisableAttack": true,
  "autoDisablePlace": true,
  "autoDisableTimeout": 120,
  "muteOnAutoAttack": true,
  "muteOnAutoPlace": true,
  "muteWhenMinimized": true
}
```

## Resetting Configuration

1. **In-Game**: Open GUI (F10), click "Cancel" to revert unsaved changes
2. **Delete File**: Delete `autoclicker.json` and restart - regenerates defaults
3. **Manual Edit**: Edit JSON directly (validates on load)

## Advanced: Config File Format

The config uses JSON with these types:
- **Numbers**: Integers only (no decimals for intervals)
- **Booleans**: `true`/`false`
- **Key Codes**: Integers (GLFW key codes)

Invalid values are automatically corrected on load (see `ConfigManager.validateAndFix()`).

## Migration Notes

### From Pre-1.5.0
- Old keybind format used Windows VK codes (118-121 for F7-F10)
- Automatically migrated to GLFW codes (296-299) on first load
- If keybinds show as numbers in controls menu, press the key to re-bind

### Version Upgrades
- New options added with sensible defaults
- Removed options ignored (no breaking changes)
- Run `/autoclicker config reset` (if command added in future) to regenerate