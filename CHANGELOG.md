# Changelog

All notable changes to Autoclicker are documented here.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.4] - 2026-11-07

### Added
- **Minecraft 26.3 support** (Fabric API 0.164.0+26.3, Fabric Loader 0.19.5+)
- Gradle 9.7.1 wrapper support
- Fabric Loom 1.18-SNAPSHOT for 26.3
- Mod Menu 21.0.0 dependency for 26.3

### Fixed
- **Breaking API changes in 26.3:**
  - `InputConstants.Type.KEYSYM` → `InputConstants.Type.KEYBOARD` for key bindings
  - `player.swing(hand, true)` → `player.swing(hand, SwingAnimation.DEFAULT, true)` for swing animations
  - `piercingAttack(piercing)` → `piercingAttack(stack.getAttackAnimation(), piercing)` for spear/pike attacks
- GLFW → SDL backend migration handled via vanilla `InputConstants`

### Documentation
- Added `CONTRIBUTING.md` - Contribution guidelines
- Added `BUILD.md` - Build instructions for all versions
- Added `CONFIGURATION.md` - Complete configuration reference
- Added `DEVELOPER.md` - Technical architecture documentation
- Added `USAGE.md` - End-user usage guide
- Added `CHANGELOG.md` - This file
- Updated `README.md` with 26.3 compatibility and version history

## [1.5.3] - 2026-XX-XX

### Added
- **Minecraft 26.2 support** (Fabric API 0.152.2+26.2)
- **Auto Refill system** - Automatically replaces depleted items in hand
  - Main hand refill (`autoRefillMainHand`)
  - Off-hand refill (`autoRefillOffHand`)
  - Count threshold (`refillCountThreshold`)
  - Durability threshold (`refillDurabilityThreshold`)
- **Auto Disable timeout** - Automatically disables features after inactivity
  - Attack timeout (`autoDisableAttack`, `autoDisableTimeout`)
  - Place timeout (`autoDisablePlace`, `autoDisableTimeout`)
- **Persistent mute state** - Volume survives force-close
  - Saves to `config/autoclicker_mute_state.txt`
  - Restores on next launch
- **Enhanced mute system** - Three priority levels
  - Manual (F7) > Linked > Minimized

### Changed
- Refactored codebase for better maintainability
- Version-specific source set reorganization
- Improved keybind handling (GLFW codes)
- Better error handling and validation

### Fixed
- Volume restoration race conditions
- Inventory swap edge cases
- Config migration for legacy key codes

## [1.5.2] - 2026-XX-XX

### Added
- Minecraft 26.1.2 support
- Mod Menu integration (F10 opens config via Mod Menu)
- Randomized intervals for attack/place
- Neutral mob targeting option

### Fixed
- Off-hand bone meal usage
- Config validation edge cases

## [1.5.1] - 2026-XX-XX

### Added
- Minecraft 26.1 support
- Auto bone meal for planting
- Improved GUI with scrollable list

### Fixed
- Spear/pike attack animation fix
- Keybind registration for 26.x

## [1.5.0] - 2026-XX-XX

### Added
- Major rewrite for modern Minecraft versions (1.20+)
- Unified version-specific architecture
- Fabric API 0.145+ support
- Java 21/25 toolchain support

### Changed
- Minimum Minecraft version: 1.16.5
- Dropped legacy version support (pre-1.16.5 moved to separate branches)

## [1.4.x] - Legacy Versions

### 1.4.x Series (1.16.5 - 1.19.4)
- Original autoclicker implementation
- Basic auto attack and auto place
- Simple mute toggle
- Legacy Fabric API versions

---

## Version Support Matrix

| Mod Version | Minecraft | Fabric API | Fabric Loader | Java | Status |
|-------------|-----------|------------|---------------|------|--------|
| 1.5.4 | 26.3 | 0.164.0 | 0.19.5+ | 25 | ✅ Current |
| 1.5.4 | 26.2 | 0.152.2 | 0.18.4+ | 25 | ✅ Supported |
| 1.5.4 | 26.1 | 0.145.1 | 0.18.4+ | 25 | ✅ Supported |
| 1.5.4 | 1.21.11 | 0.140.2 | 0.18.4+ | 21 | ✅ Supported |
| 1.5.4 | 1.21.10 | 0.138.4 | 0.18.4+ | 21 | ✅ Supported |
| 1.5.4 | 1.21.8 | 0.136.1 | 0.18.4+ | 21 | ✅ Supported |
| 1.5.4 | 1.20.1 | 0.92.7 | 0.18.4+ | 21 | ✅ Supported |
| 1.5.3 | 1.19.4 | 0.87.2 | 0.16.14 | 17 | ✅ Supported |
| 1.5.3 | 1.18.2 | 0.77.0 | 0.16.14 | 17 | ✅ Supported |
| 1.5.3 | 1.16.5 | 0.42.0 | 0.16.14 | 8 | ✅ Supported |

---

## Upgrade Guide

### From 1.5.3 to 1.5.4
- **No breaking changes** for users
- Config automatically migrates
- Just update the JAR

### From 1.4.x to 1.5.x
- Delete old `autoclicker.json` (new format)
- Rebind keys in Controls menu (F7-F10 defaults)
- New features: Auto Refill, Auto Disable, Bone Meal

---

## Contributors

- **小奇** - Original author & maintainer
- **vihaanvp** - 26.3 port, documentation, build system

## License

GPL-3.0 - See [LICENSE](LICENSE) for details.