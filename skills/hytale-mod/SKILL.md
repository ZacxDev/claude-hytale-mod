---
name: hytale-mod
description: Hytale server plugin and modding development. Use for Java plugins, commands, events, Custom UI, asset packs, items, weapons, and animations.
allowed-tools: [Read, Grep, Glob, Bash, Edit, Write, Task]
argument-hint: "[task description]"
---

# Hytale Modding Skill

Prime context for Hytale server plugin development and modding (Early Access, Dec 2025).

## Environment Requirements

- **Java 25** (required by Hytale server)
- **Gradle Groovy DSL** only — Kotlin DSL (`.gradle.kts`) fails with JDK 25
- **Maven coordinates**: `com.hypixel.hytale:Server:2026.01.28-87d03be09`

## Project Structure

```
plugin-name/
├── build.gradle              # Groovy DSL, shadowJar
├── src/main/
│   ├── java/                 # Plugin code
│   └── resources/
│       ├── manifest.json     # Plugin manifest
│       ├── Server/           # Data assets (items, blocks)
│       └── Common/           # Visual assets (icons, UI, textures)
```

## Gradle Configuration

```gradle
plugins {
    id 'java'
    id 'com.github.johnrengelman.shadow' version '8.1.1'
}

java {
    sourceCompatibility = JavaVersion.VERSION_25
    targetCompatibility = JavaVersion.VERSION_25
}

repositories {
    mavenCentral()
    maven {
        name = "hytale-release"
        url = uri("https://maven.hytale.com/release")
    }
    maven {
        name = "hytale-pre-release"
        url = uri("https://maven.hytale.com/pre-release")
    }
}

dependencies {
    compileOnly("com.hypixel.hytale:Server:2026.01.28-87d03be09")
}
```

## Plugin Lifecycle

```java
package com.example.myplugin;

import com.hypixel.hytale.server.core.plugin.JavaPlugin;
import com.hypixel.hytale.server.core.plugin.JavaPluginInit;
import javax.annotation.Nonnull;

public class MyPlugin extends JavaPlugin {

    public MyPlugin(@Nonnull JavaPluginInit init) {
        super(init);
        getLogger().atInfo().log("Plugin loaded!");
    }

    @Override
    protected void setup() {
        // Register commands, events, components
        getLogger().atInfo().log("Plugin setup!");
    }

    @Override
    protected void start() {
        // Start tasks, load config
        getLogger().atInfo().log("Plugin started!");
    }

    @Override
    protected void shutdown() {
        // Save data, clean up resources
        getLogger().atInfo().log("Plugin shutdown!");
    }
}
```

## Critical Gotchas

### Logging (Flogger, not SLF4J)
```java
getLogger().atInfo().log("message");   // CORRECT
getLogger().info("message");           // WRONG - method doesn't exist
```

### Event Registration
| Event Type | Method | Example |
|------------|--------|---------|
| `IBaseEvent<Void>` | `register(Class, Consumer)` | `PlayerConnectEvent` |
| `IAsyncEvent<K>` | `registerGlobal(Class, Consumer)` | `PlayerChatEvent` |

Using `register()` with keyed events **fails at compile time**.

### Command Permissions
Commands auto-generate permissions. Make public with:
```java
@Override
protected boolean canGeneratePermission() {
    return false;
}
```

### PNG Textures
All PNGs **MUST be 8-bit/channel RGBA**. 16-bit crashes the client.
```bash
magick input.png -depth 8 -type TrueColorAlpha PNG32:output.png
file texture.png  # Should show "8-bit/color RGBA"
```

### Asset Pack Paths
- Item definitions: `Server/Item/Items/` (NOT `Common/Assets/Items/`)
- Item icons: `Common/Icons/ItemsGenerated/`
- UI files: `Common/UI/Custom/`

## Command Pattern

```java
import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.command.system.AbstractCommand;
import com.hypixel.hytale.server.core.command.system.CommandContext;
import com.hypixel.hytale.server.core.command.system.arguments.system.OptionalArg;
import com.hypixel.hytale.server.core.command.system.arguments.types.ArgTypes;
import java.util.concurrent.CompletableFuture;

public class MyCommand extends AbstractCommand {
    private final OptionalArg<String> nameArg;

    public MyCommand() {
        super("mycommand", "Description of my command");
        nameArg = withOptionalArg("name", "A name argument", ArgTypes.STRING);
    }

    @Override
    protected CompletableFuture<Void> execute(CommandContext ctx) {
        String name = ctx.get(nameArg);
        ctx.sendMessage(Message.raw("Hello, " + (name != null ? name : "world") + "!"));
        return CompletableFuture.completedFuture(null);
    }
}

// Register in setup():
// getCommandRegistry().registerCommand(new MyCommand());
```

## Event Pattern

```java
// Unkeyed event (PlayerConnectEvent implements IBaseEvent<Void>)
getEventRegistry().register(PlayerConnectEvent.class, event -> {
    PlayerRef player = event.getPlayerRef();
    player.sendMessage(Message.raw("Welcome, " + player.getUsername() + "!"));
});

// Keyed event (PlayerChatEvent implements IAsyncEvent<String>)
// MUST use registerGlobal - register() won't compile
getEventRegistry().registerGlobal(PlayerChatEvent.class, event -> {
    String content = event.getContent();
    PlayerRef sender = event.getSender();
});
```

## Custom UI Pattern

### InteractiveCustomUIPage (with input)
```java
import com.hypixel.hytale.codec.Codec;
import com.hypixel.hytale.codec.KeyedCodec;
import com.hypixel.hytale.codec.builder.BuilderCodec;
import com.hypixel.hytale.component.Ref;
import com.hypixel.hytale.component.Store;
import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.entity.entities.player.pages.InteractiveCustomUIPage;
import com.hypixel.hytale.server.core.ui.builder.*;
import com.hypixel.hytale.server.core.universe.PlayerRef;
import com.hypixel.hytale.server.core.universe.world.storage.EntityStore;
import com.hypixel.hytale.protocol.packets.interface_.*;

public class MyPage extends InteractiveCustomUIPage<MyPage.EventData> {

    public MyPage(PlayerRef playerRef) {
        super(playerRef, CustomPageLifetime.CanDismiss, EventData.CODEC);
    }

    @Override
    public void build(Ref<EntityStore> ref, UICommandBuilder ui,
                      UIEventBuilder events, Store<EntityStore> store) {
        ui.append("MyPage.ui");

        // Button click binding (static key - must start uppercase)
        events.addEventBinding(
            CustomUIEventBindingType.Activating,
            "#SaveButton",
            com.hypixel.hytale.server.core.ui.builder.EventData.of("Action", "save")
        );

        // Text field binding (reference key - @ prefix reads element value)
        events.addEventBinding(
            CustomUIEventBindingType.ValueChanged,
            "#NameInput",
            com.hypixel.hytale.server.core.ui.builder.EventData.of("@NameInput", "#NameInput.Value"),
            false  // don't send on initial load
        );
    }

    @Override
    public void handleDataEvent(Ref<EntityStore> ref, Store<EntityStore> store,
                                EventData data) {
        if ("save".equals(data.action)) {
            close();
        } else if (data.nameInput != null) {
            // Handle text input change
        }
    }

    // Typed event data with BuilderCodec for deserialization
    public static class EventData {
        public static final BuilderCodec<EventData> CODEC =
            BuilderCodec.builder(EventData.class, EventData::new)
                .addField(new KeyedCodec<>("Action", Codec.STRING),
                    (d, v) -> d.action = v, d -> d.action)
                .addField(new KeyedCodec<>("NameInput", Codec.STRING),
                    (d, v) -> d.nameInput = v, d -> d.nameInput)
                .build();

        public String action;
        public String nameInput;
    }
}
```

### .ui File Format
```
$Common = "Common.ui";
@MyTex = PatchStyle(TexturePath: "MyBackground.png");

Group {
  LayoutMode: Center;

  Group #Panel {
    Background: @MyTex;
    Anchor: (Width: 400, Height: 300);
    LayoutMode: Top;

    Label #Title {
      Style: (FontSize: 24, Alignment: Center);
      Anchor: (Top: 20, Height: 40);
      Text: "Hello World";
    }

    TextField #NameInput {
      Style: $Common.@DefaultInputFieldStyle;
      Background: $Common.@InputBoxBackground;
      Anchor: (Top: 10, Width: 300, Height: 50);
      Padding: (Full: 10);
    }
  }
}
```

**Syntax:**
- `$Variable = "file.ui"` — import another UI file
- `@Variable = PatchStyle(...)` — define reusable texture
- `#ElementId` — unique identifier for Java access
- Element types: `Group`, `Label`, `TextField`
- LayoutMode: `Center`, `Top`

## Item Definition (bundled in plugin)

**manifest.json:**
```json
{
  "Group": "com.example",
  "Name": "MyPlugin",
  "Version": "1.0.0",
  "Main": "com.example.myplugin.MyPlugin",
  "IncludesAssetPack": true,
  "Dependencies": {
    "Hytale:EntityModule": "*",
    "Hytale:BlockModule": "*"
  }
}
```

**Server/Item/Items/My_Item.json:**
```json
{
  "Id": "My_Item",
  "TranslationProperties": {
    "Name": "My Custom Item",
    "Description": "A custom item from my plugin"
  },
  "Icon": "Icons/ItemsGenerated/My_Item.png",
  "Quality": "Common",
  "MaxStack": 64,
  "Categories": ["Items.Example"]
}
```

## Weapon Definition

```json
{
  "Id": "Custom_Sword",
  "Parent": "Template_Weapon_Sword",
  "TranslationProperties": { "Name": "Custom Sword" },
  "Model": "Resources/Custom_Sword/model.blockymodel",
  "Texture": "Resources/Custom_Sword/texture.png",
  "Icon": "Icons/ItemsGenerated/Custom_Sword.png",
  "PlayerAnimationsId": "Sword",
  "Quality": "Rare",
  "MaxStack": 1
}
```

Key weapon properties:
- `PlayerAnimationsId` — links to player animation set
- `Parent` — inherit from template (reduces duplication)
- `Interactions` — Primary/Secondary/Ability attack chains

## Build & Deploy

```bash
# Build plugin JAR
gradle -p plugin-name shadowJar

# Copy to server mods folder
cp plugin-name/build/libs/*.jar server/Server/mods/

# Start server
./server/start.sh
```

## Context7 Library IDs

For fetching up-to-date documentation:
```
/websites/hytalemodding_dev_en                  # Community guides (1125 snippets)
/logan-mcduffie/hytale-toolkit                  # Decompiled source (45103 snippets)
/websites/britakee-studios_gitbook_io_hytale-modding-documentation  # Tutorials (457 snippets)
```

## Key Packages

| Package | Purpose |
|---------|---------|
| `com.hypixel.hytale.server.core.plugin` | JavaPlugin, JavaPluginInit |
| `com.hypixel.hytale.server.core.command.system` | AbstractCommand, CommandContext |
| `com.hypixel.hytale.event` | EventRegistry |
| `com.hypixel.hytale.server.core.entity.entities.player.pages` | CustomUIPage, InteractiveCustomUIPage |
| `com.hypixel.hytale.codec.builder` | BuilderCodec |
| `com.hypixel.hytale.server.core.interaction` | Interaction system |
| `com.hypixel.hytale.protocol.packets.interface_` | CustomUIEventBindingType, CustomPageLifetime |

## Plugin Registries

| Registry | Access Method | Purpose |
|----------|---------------|---------|
| `logger` | `getLogger()` | Plugin logging (Flogger) |
| `eventRegistry` | `getEventRegistry()` | Event handling |
| `commandRegistry` | `getCommandRegistry()` | Command registration |
| `taskRegistry` | `getTaskRegistry()` | Scheduled tasks |
| `dataDirectory` | `getDataDirectory()` | Persistent storage path |
| `assetRegistry` | `getAssetRegistry()` | Game assets |

## Reference

For extended patterns (ECS, animations, packet handling, interactions), see [patterns.md](patterns.md).
