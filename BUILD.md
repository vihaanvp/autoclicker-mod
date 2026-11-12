# Building Autoclicker

This guide covers building the Autoclicker mod for various Minecraft versions.

## Prerequisites

### For Minecraft 26.x (Modern Versions)
- **JDK 25** (required)
- **Gradle 9.7.1+** (provided via wrapper)
- **Internet connection** (for downloading dependencies)

### For Minecraft 1.20.x - 1.21.x
- **JDK 21** (required)
- **Gradle 8.8+** (provided via wrapper)
- **Internet connection**

### For Minecraft 1.16.5 - 1.19.x
- **JDK 17/16/8** (depending on version, managed by toolchain)
- **Gradle 7.5+** (provided via wrapper)

## Quick Start

```bash
# Clone repository
git clone https://github.com/vihaanvp/autoclicker-mod.git
cd autoclicker-mod

# Make wrapper executable (Linux/macOS)
chmod +x gradlew

# Build for Minecraft 26.3 (latest)
./gradlew :26.3:build

# Build for Minecraft 1.21.11
./gradlew :1.21.11:build

# Build all supported versions sequentially
./gradlew buildAllVersions
```

## Build Outputs

JAR files are placed in:
```
releases/fabric/
├── Autoclicker-fabric1.16.5-1.5.4.jar
├── Autoclicker-fabric1.21.11-1.5.4.jar
├── Autoclicker-fabric26.3-1.5.4.jar
└── ...
```

Naming convention: `Autoclicker-fabric<VERSION>-<MOD_VERSION>.jar`

## Supported Minecraft Versions

| Minecraft | Fabric API | Mod Menu | Java | Loom Plugin | Status |
|-----------|------------|----------|------|-------------|--------|
| 1.16.5    | 0.42.0     | 1.16.9   | 8    | fabric-loom-remap | ✅ |
| 1.17.1    | 0.46.1     | 2.0.14   | 16   | fabric-loom-remap | ✅ |
| 1.18.2    | 0.77.0     | 3.2.5    | 17   | fabric-loom-remap | ✅ |
| 1.19.2    | 0.77.0     | 4.1.2    | 17   | fabric-loom-remap | ✅ |
| 1.19.4    | 0.87.2     | 6.3.1    | 17   | fabric-loom-remap | ✅ |
| 1.20.1    | 0.92.7     | 7.2.2    | 21   | fabric-loom | ✅ |
| 1.20.6    | 0.100.8    | 7.2.2    | 21   | fabric-loom | ✅ |
| 1.21.1    | 0.116.10   | 7.2.2    | 21   | fabric-loom | ✅ |
| 1.21.4    | 0.119.4    | 7.2.2    | 21   | fabric-loom | ✅ |
| 1.21.8    | 0.136.1    | 15.0.2   | 21   | fabric-loom | ✅ |
| 1.21.11   | 0.140.2    | 17.0.0   | 21   | fabric-loom | ✅ |
| 26.1      | 0.145.1    | 18.0.0-alpha.8 | 25 | fabric-loom | ✅ |
| 26.2      | 0.152.2    | 18.0.0-alpha.8 | 25 | fabric-loom | ✅ |
| **26.3**  | **0.164.0** | **21.0.0** | **25** | **fabric-loom** | **✅** |

## Build Commands

### Single Version Build
```bash
# Format: ./gradlew :<MC_VERSION>:build
./gradlew :26.3:build
./gradlew :1.21.11:build
./gradlew :1.20.1:build
```

### Build All Versions
```bash
# Sequential build (avoids memory issues)
./gradlew buildAllVersions
```

### Development Tasks
```bash
# Run Minecraft client for testing
./gradlew :26.3:runClient

# Run with specific JVM args
./gradlew :26.3:runClient --debug-jvm

# Clean build
./gradlew clean :26.3:build

# Only process resources (update version in JAR manifest)
./gradlew :26.3:updateVersionResources

# Generate IDE project files (IntelliJ IDEA)
./gradlew idea
```

## Version-Specific Notes

### Minecraft 26.x (Modern)
- Uses `fabric-loom` plugin (not `fabric-loom-remap`)
- Requires JDK 25
- Uses official Mojang mappings (no remapping needed)
- JAR output via `jar` task (not `remapJar`)

### Minecraft 1.20 - 1.21
- Uses `fabric-loom` plugin
- Requires JDK 21
- Uses official Mojang mappings

### Minecraft 1.16.5 - 1.19
- Uses `fabric-loom-remap` plugin
- Uses legacy mappings (requires remapping)
- JAR output via `remapJar` task
- Uses older Fabric Loader (0.16.14)

## Troubleshooting

### Gradle Wrapper Download Timeout
```bash
# Increase timeout in gradle/wrapper/gradle-wrapper.properties
networkTimeout=120000

# Or use system Gradle if installed
gradle :26.3:build
```

### JDK Not Found
```bash
# Ensure correct JDK is installed and in PATH
# For 26.x: JDK 25
# For 1.20+: JDK 21

# Check with:
java -version
```

### Out of Memory
```bash
# Increase heap in gradle.properties
org.gradle.jvmargs=-Xmx6g -Xms2g ...
```

### Fabric API Version Mismatch
Check `build.gradle` `fabricApiVersions` map matches your target Minecraft version.

### Mod Menu Not Found
Check `modMenuVersions` map in `build.gradle`.

## CI/CD

### GitHub Actions Example
```yaml
name: Build All Versions
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: 25
      - name: Build All Versions
        run: ./gradlew buildAllVersions --no-daemon
      - name: Upload Artifacts
        uses: actions/upload-artifact@v4
        with:
          name: autoclicker-jars
          path: releases/fabric/*.jar
```

## Verifying Builds

```bash
# Check JAR contents
jar tf releases/fabric/Autoclicker-fabric26.3-1.5.4.jar

# Verify fabric.mod.json
jar xf releases/fabric/Autoclicker-fabric26.3-1.5.4.jar fabric.mod.json
cat fabric.mod.json
```

## Publishing

### Manual Release
```bash
# 1. Update version
echo "modVersion=1.5.5" > gradle/version.properties

# 2. Build target version
./gradlew :26.3:build

# 3. Create GitHub release with JAR
gh release create v1.5.5 releases/fabric/Autoclicker-fabric26.3-1.5.5.jar \
  --title "Release v1.5.5" \
  --notes "Release notes here"
```

### Modrinth/CurseForge
Upload the JAR from `releases/fabric/` to:
- [Modrinth](https://modrinth.com/mod/autoclicker.1.0)
- [CurseForge](https://www.curseforge.com/minecraft/mc-mods/autoclicker)

## Directory Structure After Build

```
autoclicker-mod/
├── build/                          # Gradle build cache
├── releases/
│   └── fabric/
│       ├── Autoclicker-fabric1.16.5-1.5.4.jar
│       ├── Autoclicker-fabric1.21.11-1.5.4.jar
│       └── Autoclicker-fabric26.3-1.5.4.jar
├── versions/
│   ├── 26.3/                       # Subproject directory (created at config time)
│   │   └── build/                  # Subproject build output
│   └── ...
└── .gradle/                        # Gradle daemon/cache
```