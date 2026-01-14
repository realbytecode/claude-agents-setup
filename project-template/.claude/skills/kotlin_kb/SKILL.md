---
name: kotlin_kb
description: Kotlin/Android knowledge base generation - tools and commands for creating architectural diagrams
---

# Kotlin/Android Knowledge Base Tools

## Module Dependencies

**Tool:** module-graph - Gradle plugin for module dependency diagrams

### Setup (one-time)

Add to root `build.gradle.kts`:

```kotlin
plugins {
    id("dev.iurysza.modulegraph") version "0.13.0"
}

moduleGraphConfig {
    readmePath.set("${rootDir}/README.md")
    heading.set("### Module Graph")
    theme.set(Theme.NEUTRAL)
    setStyleByModuleType.set(true)
}
```

### Generate

```bash
./gradlew createModuleGraph
```

**Output:** Mermaid diagram auto-inserted into README.md under "### Module Graph" heading

**Alternative output location:** Change `readmePath` to point to `knowledge-base/module-dependencies.md`

## Class Diagrams

**Tool:** uml-reverse-mapper - generates diagrams from JVM bytecode

### Setup

Download `urm-core.jar` from [releases](https://github.com/iluwatar/uml-reverse-mapper/releases)

### Generate

```bash
# Build project first
./gradlew assembleDebug

# Generate Mermaid diagram
java -cp "app/build/intermediates/javac/debug/classes:urm-core.jar" \
  com.iluwatar.urm.DomainMapperCli \
  -p com.example.module \
  -s mermaid \
  -f module-class-diagram.mermaid
```

**Key options:**
- `-p` - package to analyze
- `-s` - output format: `mermaid`, `plantuml`, `graphviz`
- `-f` - output file

**Note:** Reflects JVM bytecode structure. Kotlin constructs appear as Java equivalents:
- `data class` → regular class with generated methods
- `sealed class` → abstract class with subclasses
- `object` → class with static INSTANCE field
- `companion object` → nested static class

**Refinement needed:** Use AI to add Kotlin-specific stereotypes (e.g., «data», «sealed», «object»)

## Typical Workflow

```bash
# 1. Module dependencies (Mermaid - will be embedded in architecture-summary.md)
./gradlew createModuleGraph
# (Inserts Mermaid into README.md - KB agent will extract and embed properly)

# 2. Build project
./gradlew assembleDebug

# 3. Class diagrams per module (Mermaid - will be embedded in module markdown)
java -cp "app/build/intermediates/javac/debug/classes:urm-core.jar" \
  com.iluwatar.urm.DomainMapperCli \
  -p com.example.auth \
  -s mermaid \
  -f auth-classes.mermaid

java -cp "app/build/intermediates/javac/debug/classes:urm-core.jar" \
  com.iluwatar.urm.DomainMapperCli \
  -p com.example.core \
  -s mermaid \
  -f core-classes.mermaid

# 4. Knowledge-base agent will:
#    - Refine diagrams (add Kotlin stereotypes)
#    - Embed diagrams in markdown files with descriptions
#    - Create overview/architecture-summary.md with module graph
#    - Create modules/<module-name>.md with class diagrams
```

## Project Customization

Update this file with:
- Your package names
- Modules to document
- Build variant (debug/release)
- CI integration commands
