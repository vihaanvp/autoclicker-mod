# Contributing to Autoclicker

Thank you for your interest in contributing to Autoclicker! This document provides guidelines for contributing to this project.

## Code of Conduct

This project follows a standard open-source code of conduct. Please be respectful and constructive in all interactions.

## How to Contribute

### Reporting Bugs

Before submitting a bug report:
1. Check if the issue already exists in [GitHub Issues](https://github.com/vihaanvp/autoclicker-mod/issues)
2. Test with the latest version
3. Provide clear reproduction steps

**Bug Report Template:**
- Minecraft version (e.g., 26.3)
- Fabric Loader version
- Fabric API version
- Mod version
- Steps to reproduce
- Expected behavior
- Actual behavior
- Logs (if applicable)

### Suggesting Features

Feature requests are welcome! Please:
1. Check existing issues first
2. Clearly describe the feature and use case
3. Explain why it would be valuable

### Pull Requests

1. **Fork the repository** and create a feature branch
2. **Follow the code style** (see Code Style below)
3. **Write clear commit messages** (see Commit Messages below)
4. **Test your changes** on at least one Minecraft version
5. **Update documentation** if needed
6. **Submit a PR** with a clear description

## Development Setup

### Prerequisites

- **JDK 25** (required for Minecraft 26.x)
- **Gradle 9.7.1+** (wrapper included)
- **Git**

### Building

```bash
# Clone the repository
git clone https://github.com/vihaanvp/autoclicker-mod.git
cd autoclicker-mod

# Build for a specific version (e.g., 26.3)
./gradlew :26.3:build

# Build all versions
./gradlew buildAllVersions
```

Output JARs will be in `releases/fabric/`.

### Running in Development Environment

```bash
# Run Minecraft client for development (26.3)
./gradlew :26.3:runClient

# Run with debug port
./gradlew :26.3:runClient --debug-jvm
```

### Project Structure

```
autoclicker-mod/
├── build.gradle          # Main build configuration
├── settings.gradle       # Subproject inclusion
├── gradle/               # Gradle wrapper & version properties
├── versions/             # Version-specific subprojects (created at build time)
├── src/
│   ├── main/             # Shared source code (all versions)
│   │   ├── java/com/example/autoclicker/
│   │   │   ├── config/       # Configuration classes
│   │   │   ├── event/        # Event handlers
│   │   │   ├── feature/      # Core features (AutoClicker, AutoRefill, MuteFeature)
│   │   │   └── tool/         # Shared utilities
│   │   └── resources/        # Shared resources (fabric.mod.json, lang, assets)
│   └── version-specific/   # Version-specific implementations
│       ├── shared-1.20/    # Shared code for 1.20+
│       ├── 1.16.5/         # 1.16.5 specific
│       ├── 1.17.1-1.18.2/  # 1.17-1.18.2 specific
│       ├── 1.19-1.19.2/    # 1.19-1.19.2 specific
│       ├── 26.1/           # 26.1 specific
│       ├── 26.2/           # 26.2 specific
│       └── 26.3/           # 26.3 specific
```

## Code Style

### Java

- Follow standard Java conventions
- Use 4-space indentation
- Maximum line length: 120 characters
- Use meaningful variable/method names
- Add Javadoc for public APIs
- Prefer `final` for immutable variables
- Use try-with-resources for I/O

### Version-Specific Code

When adding version-specific implementations:

1. **Create new files in the appropriate `version-specific/` directory**
2. **Implement the same interface** as other versions
3. **Use the version-specific imports** from that Minecraft version
4. **Don't duplicate shared code** - put common logic in `src/main/`

Example structure for a new version adapter:
```java
// src/version-specific/NEW_VERSION/java/com/example/autoclicker/tool/SwingHelper.java
package com.example.autoclicker.tool;

import net.minecraft.client.player.LocalPlayer;
import net.minecraft.world.InteractionHand;
import net.minecraft.world.item.component.SwingAnimation; // New import for 26.3+

public final class SwingHelper {
    public static void swingArm(LocalPlayer player, InteractionHand hand) {
        player.swing(hand, SwingAnimation.DEFAULT, true); // Version-specific implementation
    }
}
```

### Configuration

- All config options in `Config.java` with clear comments
- Default values should be sensible
- Add validation in `ConfigManager.validateAndFix()`
- Document new options in `CONFIGURATION.md`

## Commit Messages

Follow conventional commit format:

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Build process, dependencies, etc.

**Examples:**
```
feat(26.3): add support for Minecraft 26.3
fix(autorefill): handle off-hand swap correctly
docs: update configuration guide
refactor(mute): simplify state machine
```

## Testing

### Manual Testing Checklist

Before submitting a PR, test:
- [ ] Mod loads without errors
- [ ] Auto Attack works on mobs/armor stands
- [ ] Auto Plant works with seeds/saplings
- [ ] Bone meal automation works
- [ ] Auto Refill works (main hand & off-hand)
- [ ] Mute features work (manual, linked, minimized)
- [ ] Config GUI opens and saves correctly
- [ ] Keybinds work (F7-F10 defaults)
- [ ] No crashes on disconnect/reconnect
- [ ] No crashes on game exit

### Version Testing

Test on at least:
- Latest 26.x version (currently 26.3)
- One 1.21.x version
- One 1.20.x version (if changes affect shared code)

## Adding Support for New Minecraft Versions

1. **Add version to `build.gradle`:**
   - Add to `fabricApiVersions` map
   - Add to `modMenuVersions` map
   - Add to `getVersionGroups` closure
   - Add to `versionOrder` in `buildAllVersions`

2. **Create version-specific directory:**
   ```bash
   mkdir -p src/version-specific/NEW_VERSION/java/com/example/autoclicker/tool
   ```

3. **Implement required adapters:**
   - `SwingHelper.java`
   - `SelectedSlotHelper.java`
   - `ScreenHelper.java`
   - `KeyBindLoaderImpl.java`
   - `ConfigScreenImpl.java`
   - `BooleanElement.java`
   - `AttackCompat.java`
   - `ScrollableList.java`
   - `Replace.java` (if inventory changes)

4. **Test build:**
   ```bash
   ./gradlew :NEW_VERSION:build
   ```

5. **Update documentation:**
   - README.md compatibility list
   - CHANGELOG.md
   - BUILD.md if needed

## License

By contributing, you agree that your contributions will be licensed under the project's license (GPL-3.0).

## Questions?

- Open a [GitHub Discussion](https://github.com/vihaanvp/autoclicker-mod/discussions)
- Join QQ Group: **172442817**