# Hytale Modding Skill for Claude Code

A Claude Code skill for Hytale server plugin development (Early Access, Jan 2026).

## Installation

```bash
/plugin marketplace add ZacxDev/claude-hytale-mod
/plugin install hytale-modding@hytale-modding-marketplace
```

## Usage

The skill activates automatically when working on Hytale modding tasks. You can also invoke it directly:

```bash
/hytale-modding:hytale-mod create a command that gives items to players
```

## What's Included

- **Plugin patterns** — JavaPlugin lifecycle, commands, events, scheduled tasks
- **Custom UI** — InteractiveCustomUIPage, .ui file format, BuilderCodec
- **Asset packs** — Items, weapons, textures, models
- **Critical gotchas** — Flogger logging, event registration, PNG requirements
- **API reference** — Decompiled source documentation, class hierarchies

## Requirements

- Java 25
- Gradle (Groovy DSL only — Kotlin DSL fails with JDK 25)
- Hytale Server

## Resources

- [hytalemodding.dev](https://hytalemodding.dev) — Community docs
- [Plugin Template](https://github.com/HytaleModding/plugin-template) — Starter project
- [Hytale Toolkit](https://github.com/logan-mcduffie/hytale-toolkit) — Decompiled source

## License

MIT
