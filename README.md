# Hytale Modding Skill for Claude Code

A Claude Code skill providing prime context for Hytale server plugin development and modding.

## Features

- **Java 25 plugin patterns** — JavaPlugin lifecycle, commands, events
- **Custom UI system** — InteractiveCustomUIPage, .ui file format, BuilderCodec
- **Asset packs** — Items, weapons, textures, bundled in plugins
- **Critical gotchas** — Flogger logging, event registration, PNG format requirements
- **Context7 integration** — Library IDs for fetching up-to-date docs

## Installation

```bash
# Add the marketplace
/plugin marketplace add zach/claude-hytale-mod

# Install the plugin
/plugin install hytale-modding@hytale-modding-marketplace
```

## Usage

After installation, the skill activates automatically when working on Hytale modding tasks, or invoke manually:

```bash
/hytale-modding:hytale-mod create a command that gives items to players
```

## What's Included

### SKILL.md (Main)
- Environment requirements (Java 25, Gradle Groovy DSL)
- Project structure template
- Plugin lifecycle pattern
- Command and event patterns
- InteractiveCustomUIPage with BuilderCodec
- .ui file format syntax
- Item and weapon definitions
- Build & deploy commands
- Key packages reference

### patterns.md (Extended Reference)
- Command argument types
- Player commands
- Event priorities and async patterns
- Scheduled tasks
- UI update methods
- Codec types
- Custom interactions
- Packet watching
- ECS components
- Animation utilities
- .blockyanim format

## Hytale Modding Resources

| Resource | Description |
|----------|-------------|
| [hytalemodding.dev](https://hytalemodding.dev) | Community documentation |
| [Britakee GitBook](https://britakee-studios.gitbook.io/hytale-modding-documentation) | Tested tutorials |
| [Plugin Template](https://github.com/HytaleModding/plugin-template) | Official starter project |
| [Hytale Toolkit](https://github.com/logan-mcduffie/hytale-toolkit) | Decompiled source |

## Requirements

- **Hytale Server** (Early Access, Dec 2025)
- **Java 25**
- **Gradle** with Groovy DSL (Kotlin DSL incompatible with JDK 25)

## License

MIT
