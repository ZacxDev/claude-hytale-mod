# Decompiled Source Navigation

Reference for navigating the decompiled HytaleServer.jar source code.

## Overview

The Hytale server JAR can be decompiled to provide 5,237 readable Java files across 894 packages. This reference helps locate and understand the internal APIs.

## Statistics

- **Total Classes**: 5,061 (Hytale-specific, excludes bundled deps)
- **Packages**: 894
- **Interfaces**: 316
- **Enums**: 226
- **Classes**: 4,519

## Context7 Library IDs

For AI-assisted documentation lookup:

```
/websites/hytalemodding_dev_en                  # Community guides (1125 snippets)
/logan-mcduffie/hytale-toolkit                  # Decompiled source (45103 snippets)
/websites/britakee-studios_gitbook_io_hytale-modding-documentation  # Tutorials (457 snippets)
```

## Key Package Categories

### Plugin System
```
com/hypixel/hytale/server/core/plugin/
├── JavaPlugin.java          # Base class for all plugins
├── JavaPluginInit.java      # Plugin initialization context
├── PluginBase.java          # Abstract plugin base
├── PluginManager.java       # Plugin loading/lifecycle
├── PluginState.java         # Plugin states enum
└── PluginType.java          # Plugin types enum
```

### Command System
```
com/hypixel/hytale/server/core/command/
├── AbstractCommand.java           # Base command class
├── AbstractAsyncCommand.java      # Async command base
├── AbstractPlayerCommand.java     # Player-only commands
├── AbstractCommandCollection.java # Command groups
├── CommandContext.java            # Execution context
├── CommandRegistry.java           # Registration API
└── system/
    └── arguments/                 # Argument types
```

### Event System
```
com/hypixel/hytale/server/core/event/
├── EventRegistry.java       # Event registration
├── EventPriority.java       # Priority levels
└── events/
    ├── player/              # PlayerConnectEvent, PlayerChatEvent, etc.
    ├── block/               # UseBlockEvent, BlockBreakEvent, etc.
    └── entity/              # EntitySpawnEvent, etc.
```

### UI System
```
com/hypixel/hytale/server/core/entity/entities/player/pages/
├── CustomUIPage.java             # Base UI page
├── InteractiveCustomUIPage.java  # Pages with input handling
└── PageManager.java              # Page lifecycle

com/hypixel/hytale/server/core/ui/
├── UICommandBuilder.java         # UI update builder
├── UIEventBuilder.java           # Event binding builder
└── builder/EventData.java        # Event data codecs
```

### Entity/ECS System
```
com/hypixel/hytale/component/
├── ComponentType.java       # Component type definition
├── Store.java               # Component storage
├── Ref.java                 # Entity references
└── system/                  # ECS systems
```

### Interaction System
```
com/hypixel/hytale/server/core/interaction/
├── Interaction.java              # Base interaction
├── SimpleInstantInteraction.java # Instant actions
├── InteractionContext.java       # Execution context
├── InteractionType.java          # Primary/Secondary/etc.
└── CooldownHandler.java          # Cooldown management
```

### Network Protocol
```
com/hypixel/hytale/protocol/
├── packets/                 # All packet definitions
│   ├── player/              # Player packets
│   ├── world/               # World sync packets
│   ├── entities/            # Entity packets
│   ├── interaction/         # Interaction packets
│   └── interface_/          # UI packets
└── io/                      # Network I/O
```

### Built-in Plugins
```
com/hypixel/hytale/builtin/
├── adventure/               # Quest/objective systems
│   ├── objectives/          # Objective tracking
│   ├── npcobjectives/       # NPC quest givers
│   ├── shop/                # Shop system
│   └── reputation/          # Faction reputation
├── crafting/                # Crafting system
├── portals/                 # Portal mechanics
├── weather/                 # Weather system
└── worldgen/                # World generation
```

## Inheritance Chains (Key Hierarchies)

### JavaPlugin
```
PluginBase
└── JavaPlugin (your plugins extend this)
    └── 57 built-in plugins (CraftingPlugin, WeatherPlugin, etc.)
```

### Command
```
AbstractCommand
├── AbstractAsyncCommand
│   ├── AbstractPlayerCommand (player-only)
│   ├── AbstractAsyncWorldCommand
│   └── AbstractCommandCollection (subcommands)
└── ~120 built-in commands
```

### UI Page
```
CustomUIPage
└── InteractiveCustomUIPage<T>
    └── ~50 built-in pages (BarterPage, DialogPage, etc.)
```

### Interaction
```
Interaction
├── SimpleInstantInteraction (one-shot)
├── ChargingInteraction (hold to charge)
├── ChainingInteraction (combo chains)
└── ~100 interaction types
```

## Common Lookups

When working with decompiled source, use these search patterns:

### Find events
```bash
find decompiled/vineflower -path "*event*" -name "*Event.java"
```

### Find commands
```bash
grep -l "extends Abstract.*Command" decompiled/vineflower -r --include="*.java"
```

### Find interactions
```bash
find decompiled/vineflower -path "*interaction*" -name "*.java"
```

### Find UI pages
```bash
grep -l "extends.*CustomUIPage" decompiled/vineflower -r --include="*.java"
```

### Find codec definitions
```bash
grep -r "BuilderCodec.builder" decompiled/vineflower --include="*.java"
```

## Tips for Exploration

1. **Start with hierarchy**: Check HIERARCHY.md to understand class relationships
2. **Use API reference**: API_REFERENCE.md has the most important plugin-facing APIs
3. **Search by pattern**: Use grep to find usage patterns in built-in code
4. **Read built-in plugins**: `builtin/` shows how Hypixel implements features
5. **Check protocol**: `protocol/packets/` reveals client-server communication
