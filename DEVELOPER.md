# Developer Guide

Technical documentation for Autoclicker mod internals, architecture, and development workflows.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                      Fabric Mod Entry                         │
│                     Main (ClientModInitializer)               │
└──────────────────────────┬────────────────────────────────────┘
                           │
           ┌───────────────┼───────────────┐
           ▼               ▼               ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │ ConfigManager│ │ EventHandler │ │ ModMenuIntegration│
    └──────┬────────┘ └──────┬────────┘ └─────────────┘
           │                 │
           ▼                 ▼
    ┌─────────────┐ ┌──────────────────────────────┐
    │   Config    │ │         Features              │
    │  (Data)     │ │  ┌─────────┐ ┌─────────────┐  │
    └─────────────┘ │  │AutoClick│ │  AutoRefill │  │
                    │  └────┬────┘ └──────┬──────┘  │
                    │       │             │          │
                    │       ▼             ▼          │
                    │  ┌─────────────────────────┐   │
                    │  │      MuteFeature        │   │
                    │  └─────────────────────────┘   │
                    └────────────────────────────────┘
           │
           ▼
    ┌─────────────────────────────────────────────┐
    │         Version-Specific Adapters            │
    │  (SwingHelper, KeyBindLoader, ScreenHelper,  │
    │   AttackCompat, ItemUseAdapter, etc.)        │
    └─────────────────────────────────────────────┘
```

## Core Modules

### Main (`Main.java`)
- Fabric `ClientModInitializer` entry point
- Loads config, registers events
- Provides `sendMessage()` for chat feedback
- Logger instance (Log4j2, compatible with all versions)

### Config System (`config/`)
- **`Config.java`**: POJO with all configuration fields + defaults
- **`ConfigManager.java`**: Gson-based load/save/migrate/validate
  - Auto-migrates legacy VK keycodes → GLFW
  - Validates ranges on load
  - Provides snapshot/restore for GUI cancel

### Event Handler (`event/EventHandler.java`)
- Registers Fabric API events:
  - `ClientTickEvents.END_CLIENT_TICK` - Main game loop
  - `ClientLifecycleEvents.CLIENT_STARTED/STOPPING` - Volume persistence
  - `ClientPlayConnectionEvents.DISCONNECT` - Cleanup on disconnect
- Manages keybind presses via `KeyBindLoader`
- Delegates to features

### Features (`feature/`)

#### `AutoClicker.java` - Core Automation
```java
// Main tick loop
tick(Minecraft client) {
  // 1. Update cooldowns
  // 2. Check feature enabled state changes
  // 3. Auto Attack logic
  // 4. Auto Place logic
  // 5. Timeout checks
}
```
- **Attack**: Raycasts to entity, filters by type, uses `AttackCompat`
- **Place**: Raycasts to block, checks hand items, uses `ItemUseAdapter`
- **Timing**: Tick counters + cooldowns + configurable intervals + randomness
- **Timeout**: Tracks last success time, disables if exceeded

#### `AutoRefill.java` - Inventory Management
```java
// Memory-based refill system
onAttackUsed() / onPlaceUsed() {
  // 1. Record current item + slot (memory)
  // 2. On next use, check if item depleted
  // 3. Search inventory for replacement
  // 4. Swap via Replace utility
}
```
- Separate memory for attack/place + main/off-hand (4 slots)
- Thresholds: count + durability percentage
- Smart detection: ignores manual item changes

#### `MuteFeature.java` - Audio Management
```java
// State machine with persistence
enum MuteState { UNMUTED, MANUAL_MUTED, AUTO_MUTED, MINIMIZED_MUTED }
```
- **Persistence**: Saves volume to file on mute, restores on launch
- **Priority**: Manual > Linked (auto features) > Minimized
- **Race protection**: 1s grace period after mute, ignores external volume changes
- **Cleanup**: Force-restores on game exit/disconnect

### Version Adapters (`tool/`)

| Adapter | Purpose | Key Methods |
|---------|---------|-------------|
| `SwingHelper` | Arm swing animation | `swingArm(player, hand)` |
| `KeyBindLoader` | Keybinding registration | `createKeyBind()`, `registerKeyBind()` |
| `ScreenHelper` | Screen/overlay API | `current()`, `open()`, `showOverlayMessage()` |
| `AttackCompat` | Spear/pike attack | `attack(client, entity)` |
| `ItemUseAdapter` | Right-click block | `useItemOn(client, hand, hit)` |
| `SelectedSlotHelper` | Inventory slot access | `getSelectedSlot()`, `getMainHandItem()` |
| `MasterVolume` | Volume get/set | `set(options, volume)` |
| `TextCompat` | Component creation | `translatable()`, `literal()` |
| `Plantables` | Version-specific items | `isExtraPlantable()`, `isExtraBonemealTarget()` |
| `ScrollableList` | Custom GUI list | Custom widget with scrollbar |
| `Replace` | Inventory swapping | `refillSlot()` |
| `ConfigScreenImpl` | ModMenu integration | `mouseScrolled()` override |

## Version-Specific Source Sets

### Build System (`build.gradle`)

```groovy
def getVersionGroups = { mcVersion ->
    if (mcVersion ==~ /26\.3(?:\.[1-9])?/) {
        return ["shared-1.20", "26.3"]  // ← 26.3 uses shared + 26.3 specific
    }
    // ... other versions
}
```

### Directory Structure
```
src/version-specific/
├── shared-1.20/          # Common code for 1.20+
│   └── tool/             # TextCompat, Plantables, MasterVolume, ItemUseAdapter
├── 26.3/                 # 26.3 specific adapters
│   └── tool/
├── 26.2/                 # 26.2 specific
├── 26.1/                 # 26.1 specific
├── 1.21.11/              # 1.21.11 specific
├── 1.20.2-1.21.4/        # Shared for this range
└── ...                   # Other version ranges
```

### Adding a New Version

1. **Update `build.gradle`:**
```groovy
// In fabricApiVersions map
"NEW_VERSION": "FABRIC_API_VERSION",

// In modMenuVersions map
"NEW_VERSION": "MOD_MENU_VERSION",

// In getVersionGroups
if (mcVersion ==~ /NEW_VERSION_PATTERN/) {
    return ["shared-1.20", "NEW_VERSION"]
}

// In buildAllVersions versionOrder
'NEW_VERSION',
```

2. **Create adapter files:**
```bash
mkdir -p src/version-specific/NEW_VERSION/java/com/example/autoclicker/tool
# Create all 10 adapter files
```

3. **Test build:**
```bash
./gradlew :NEW_VERSION:build
```

## Key Technical Details

### Tick Loop Timing
```java
// AutoClicker.tick() runs every client tick (20 TPS)
attackTickCounter++;           // Increments each tick
if (attackTickCounter >= interval && cooldown <= 0) {
    performAttack();           // Execute attack
    attackTickCounter = 0;     // Reset counter
    cooldown = getCooldown();  // Set cooldown (with randomness)
}
```

### Raycasting for Targets
```java
HitResult hitResult = client.hitResult;
if (hitResult instanceof EntityHitResult) {
    // Attack logic - check entity type, distance, etc.
}
if (hitResult instanceof BlockHitResult) {
    // Place logic - check block, hand item, etc.
}
```

### Inventory Swapping (`Replace.java`)
- **Backpack → Hotbar/Offhand**: Uses `ContainerInput.SWAP` via `gameMode.handleContainerInput()`
- **Hotbar → Offhand**: F-key swap (send `SWAP_ITEM_WITH_OFFHAND` packet)
- **Hotbar → Hotbar**: Change `selectedSlot` + send `ServerboundSetCarriedItemPacket`
- **Safety**: Checks `containerMenu == inventoryMenu` (no container open)

### Mute State Persistence
```java
// File: config/autoclicker_mute_state.txt
// Content: double volume (e.g., "1.0")
// Written on: any mute activation
// Read on: CLIENT_STARTED event
// Deleted on: volume restore
```

### GUI System
- **`ConfigScreen`** (abstract): Shared GUI logic in `shared-1.20`
- **`ConfigScreenImpl`**: Version-specific scroll handling
- **`ScrollableList`**: Custom widget using `extractWidgetRenderState` (26.1+)
- **`GuiFactory`**: Creates version-specific `BooleanElement`
- **Elements**: `BooleanElement` (extends Button), `IntSliderElement` (wraps SliderWidget)

## 26.3 Breaking Changes Handled

| Change | Adapter | Fix |
|--------|---------|-----|
| `InputConstants.Type.KEYSYM` → `KEYBOARD` | `KeyBindLoaderImpl` | Updated import + constant |
| `player.swing(hand, true)` → `player.swing(hand, SwingAnimation.DEFAULT, true)` | `SwingHelper` | Added `SwingAnimation` param |
| `piercingAttack(piercing)` → `piercingAttack(animation, piercing)` | `AttackCompat` | Added `stack.getAttackAnimation()` |
| GLFW → SDL backend | (Handled by using vanilla `InputConstants`) | No code change needed |

## Debugging Tips

### Enable Debug Logging
```java
// In Main.java
public static final Logger LOGGER = LogManager.getLogger("AutoClicker");
// Logs at DEBUG level show:
// - Attack/place timing
// - Refill memory operations
// - Mute state transitions
// - Timeout checks
```

### Common Issues
| Issue | Cause | Solution |
|-------|-------|----------|
| Keybinds show as numbers | Old VK codes in config | Press key to rebind, or delete config |
| Attack doesn't damage spears | Missing `AttackCompat` call | Ensure `AttackCompat.attack()` used |
| Crash on 26.3 | Wrong Loom version | Use `fabric-loom:1.18-SNAPSHOT` + Gradle 9.7.1 |
| Volume not restored | Mute state file corrupted | Delete `autoclicker_mute_state.txt` |

## Testing Checklist

Before committing:
- [ ] `./gradlew :26.3:build` passes
- [ ] `./gradlew :1.21.11:build` passes
- [ ] `./gradlew :1.20.1:build` passes
- [ ] Run client, test all features manually
- [ ] Check keybinds work (F7-F10)
- [ ] Config GUI opens, saves, cancels correctly
- [ ] Disconnect/reconnect doesn't crash
- [ ] Game exit restores volume

## Performance Considerations

- **Tick loop**: Minimal allocations, reuse objects
- **Raycasting**: Uses `client.hitResult` (already computed by game)
- **Inventory**: Only scans on refill trigger, not every tick
- **Config**: Cached in memory, saved only on changes
- **Mute**: Checks volume only when muted, 1s grace period

## Future Architecture Ideas

- [ ] Command system (`/autoclicker`)
- [ ] Per-profile configurations
- [ ] Advanced targeting filters (name, distance, NBT)
- [ ] Macro recorder for complex sequences
- [ ] Integration with other mods (e.g., auto-fish, auto-break)