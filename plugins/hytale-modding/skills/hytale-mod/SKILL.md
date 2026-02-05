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
- Item models: `Common/Items/ItemName/ItemName.blockymodel`
- Item textures: `Common/Items/ItemName/ItemName_Texture.png`
- UI files: `Common/UI/Custom/`
- Entity effects: `Server/Entity/Effects/`
- **Mods folder**: `server/Server/mods/` (NOT `server/mods/`)

### Pickup Interaction Bug
The JSON-based `Pickup` interaction type has a **latent NPE bug**: `InteractionModule.getInteractionManagerComponent()` returns null because `EntityModule` constructs `PlayerItemEntityPickupSystem` before `InteractionModule.setup()` runs. No vanilla items use Pickup interactions, so this was never caught.

**Workaround**: Use `LivingEntityInventoryChangeEvent` to detect items entering inventory, then apply effects programmatically. See [patterns.md](patterns.md#powerup-item-pattern-inventory-based) for the full pattern.

### Custom Items (CRITICAL)
**Items REQUIRE an `Id` field** matching the filename:
```json
{
  "Id": "My_Item",
  "TranslationProperties": { "Name": "My Item" },
  "Icon": "Icons/ItemsGenerated/My_Item.png",
  "Model": "Items/My_Item/My_Item.blockymodel",
  "Texture": "Items/My_Item/My_Item_Texture.png"
}
```
- Even though server logs warn "Unused key(s): Id", items without `Id` are **silently ignored**
- Model/Texture paths must reference bundled assets — you **cannot** use vanilla asset paths
- Items are namespaced: `/spawnitem pluginname:ItemId`
- Commands are namespaced: `/pluginname:commandname`

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

### CustomUIHud (Gameplay Overlay)
HUD elements persist during gameplay. File must be at `resources/Common/UI/Custom/`.

```java
import com.hypixel.hytale.server.core.entity.entities.player.hud.CustomUIHud;
import com.hypixel.hytale.server.core.entity.entities.player.hud.HudManager;
import com.hypixel.hytale.server.core.ui.builder.UICommandBuilder;
import com.hypixel.hytale.server.core.universe.PlayerRef;

public class ScoreHud extends CustomUIHud {
    private int score = 0;

    public ScoreHud(PlayerRef playerRef) {
        super(playerRef);
    }

    @Override
    protected void build(UICommandBuilder ui) {
        ui.append("ScoreHud.ui");  // Common/UI/Custom/ScoreHud.ui
        ui.set("#ScoreValue.Text", String.valueOf(score));
    }

    public void updateScore(int newScore) {
        if (newScore == this.score) return;
        this.score = newScore;
        UICommandBuilder ui = new UICommandBuilder();
        ui.set("#ScoreValue.Text", String.valueOf(score));
        update(false, ui);  // false = incremental update
    }
}

// Show: hudManager.setCustomHud(playerRef, new ScoreHud(playerRef));
// Hide: hudManager.setCustomHud(playerRef, null);
```

**ScoreHud.ui:**
```
Group {
  Group #ScorePanel {
    Anchor: (Top: 20, Right: 20, Width: 200, Height: 50);

    Label #ScoreValue {
      Style: (FontSize: 32);
      Text: "0";
    }
  }
}
```

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
Group {
  LayoutMode: Center;

  Group #Panel {
    Anchor: (Width: 400, Height: 300);
    LayoutMode: Top;

    Label #Title {
      Style: (FontSize: 24);
      Anchor: (Top: 20, Height: 40);
      Text: "Hello World";
    }

    TextField #NameInput {
      Anchor: (Top: 10, Width: 300, Height: 50);
    }
  }
}
```

**Syntax:**
- `$Variable = "file.ui"` — import another UI file
- `@Variable = PatchStyle(...)` — define reusable texture
- `#ElementId` — unique identifier for Java access
- Element types: `Group`, `Label`, `TextField`, `Button`

**Valid LayoutMode values:** `Center`, `Top`, `Left` (NOT TopRight, BottomLeft, etc.)

**Valid Anchor properties:** `Left`, `Right`, `Top`, `Bottom`, `Width`, `Height`, `Full`, `Horizontal`, `Vertical`, `MinWidth`, `MaxWidth`

**Valid Style properties:** `FontSize` (integer), `Alignment` (`Center` only confirmed working)

**Troubleshooting:** Enable **Diagnostic Mode** in Hytale client settings for detailed UI parsing errors.

## Item Definition (bundled in plugin)

### Complete Working Example

**Directory structure:**
```
my-plugin/src/main/resources/
├── manifest.json
├── Server/Item/Items/
│   └── My_Item.json
└── Common/
    ├── Icons/ItemsGenerated/
    │   └── My_Item.png            # 64x64, 8-bit RGBA PNG
    └── Items/My_Item/
        ├── My_Item.blockymodel
        └── My_Item_Texture.png    # 8-bit RGBA PNG
```

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
  "TranslationProperties": {
    "Name": "My Custom Item",
    "Description": "A custom item from my plugin"
  },
  "Id": "My_Item",
  "Icon": "Icons/ItemsGenerated/My_Item.png",
  "Model": "Items/My_Item/My_Item.blockymodel",
  "Texture": "Items/My_Item/My_Item_Texture.png",
  "Quality": "Common",
  "MaxStack": 64,
  "Categories": ["Items.Example"]
}
```

**CRITICAL**: The `Id` field MUST be present and match the filename. Items without `Id` are silently ignored.

**Spawn the item:** `/spawnitem myplugin:My_Item`

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

## Item Catalog Reference

This skill includes a complete dump of **3,021 vanilla items** from the Hytale server.

### Using the Item Catalog

**To find a specific item or category**, read [reference/ITEMS.md](reference/ITEMS.md):
- Summary stats (259 weapons, 121 armor, 32 tools, 121 consumables)
- Items grouped by category
- Full table with ID, name, type, and model path

**To look up item IDs quickly**, read [reference/items-compact.json](reference/items-compact.json):
```json
[
  {"id": "Weapon_Longsword_Iron", "name": "server.items.Weapon_Longsword_Iron.name"},
  {"id": "Armor_Helmet_Iron", "name": "server.items.Armor_Helmet_Iron.name"}
]
```

**To find vanilla asset paths** (for Parent templates), read [reference/asset-paths.txt](reference/asset-paths.txt):
```
Icons/ItemsGenerated/Weapon_Longsword_Iron.png
Items/Weapons/Longsword/Iron.blockymodel
Items/Weapons/Longsword/Iron_Texture.png
```

### Common Item Templates (Parent values)

| Template | Use For |
|----------|---------|
| `Template_Weapon_Sword` | Swords, longswords |
| `Template_Weapon_Axe` | Axes |
| `Template_Weapon_Spear` | Spears, polearms |
| `Template_Weapon_Bow` | Bows |
| `Template_Armor_Helmet` | Head armor |
| `Template_Armor_Chestplate` | Chest armor |
| `Template_Tool_Pickaxe` | Mining tools |
| `Template_Tool_Shovel` | Digging tools |

### Item Naming Conventions

- **Weapons**: `Weapon_[Type]_[Material]` (e.g., `Weapon_Longsword_Iron`)
- **Armor**: `Armor_[Slot]_[Material]` (e.g., `Armor_Helmet_Cobalt`)
- **Tools**: `Tool_[Type]_[Material]` (e.g., `Tool_Pickaxe_Copper`)
- **Consumables**: `Consumable_[Name]` (e.g., `Consumable_Potion_Health`)
- **Ingredients**: `Ingredient_[Name]` (e.g., `Ingredient_Iron_Ingot`)

## Build & Deploy

```bash
# Build plugin JAR
gradle -p plugin-name shadowJar

# Copy to server mods folder (IMPORTANT: server/Server/mods/, NOT server/mods/)
cp plugin-name/build/libs/*.jar server/Server/mods/

# Start server
./server/start.sh

# Verify plugin loaded (check server logs for):
# [PluginManager] - com.example:PluginName from path PluginName-1.0.0.jar
# [PluginName|P] Plugin loaded!

# Verify item registered (check logs for warning, which means it's working):
# [AssetStore|Item] Unused key(s) in 'My_Item' file /Server/Item/Items/My_Item.json: Id
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
| `com.hypixel.hytale.server.core.prefab` | PrefabStore, PrefabBufferUtil, IPrefabBuffer |
| `com.hypixel.hytale.server.core.util` | PrefabUtil (paste/remove prefabs) |
| `com.hypixel.hytale.server.core.asset.type.entityeffect.config` | EntityEffect |
| `com.hypixel.hytale.server.core.entity.effect` | EffectControllerComponent |
| `com.hypixel.hytale.server.core.inventory.transaction` | Transaction, ItemStackTransaction, ActionType |
| `com.hypixel.hytale.math` | Box (AABB containment checks) |
| `com.hypixel.hytale.server.core.modules.spatial` | SpatialResource, PlayerSpatialSystem |
| `com.hypixel.hytale.builtin.instances` | InstancesPlugin, InstanceWorldConfig, RemovalCondition |
| `com.hypixel.hytale.server.core.event.events.ecs` | PrefabPlaceEntityEvent (entity spawn interception) |

## Plugin Registries

| Registry | Access Method | Purpose |
|----------|---------------|---------|
| `logger` | `getLogger()` | Plugin logging (Flogger) |
| `eventRegistry` | `getEventRegistry()` | Event handling |
| `commandRegistry` | `getCommandRegistry()` | Command registration |
| `taskRegistry` | `getTaskRegistry()` | Scheduled tasks |
| `dataDirectory` | `getDataDirectory()` | Persistent storage path |
| `assetRegistry` | `getAssetRegistry()` | Game assets |

## Decompiled Source Reference

This skill includes documentation extracted from the decompiled HytaleServer.jar (5,237 Java files, 894 packages).

### Reference Files

| File | Description |
|------|-------------|
| [reference/guides.md](reference/guides.md) | Community patterns (commands, ECS, UI, inventory) |
| [reference/events.md](reference/events.md) | Complete events list with registration patterns |
| [reference/packets.md](reference/packets.md) | Client-to-server packet reference |
| [reference/decompiled.md](reference/decompiled.md) | Navigation guide for decompiled source |
| [reference/PACKAGES.md](reference/PACKAGES.md) | Key packages for plugin development |
| [reference/API_REFERENCE.md](reference/API_REFERENCE.md) | Core plugin APIs |
| [reference/ITEMS.md](reference/ITEMS.md) | **Complete item catalog (3,021 items)** - IDs, models, textures, categories |
| [reference/items-compact.json](reference/items-compact.json) | Quick lookup: item IDs and names |
| [reference/asset-paths.txt](reference/asset-paths.txt) | All vanilla texture/model asset paths |

### Quick Hierarchy Reference

**Plugins**: `PluginBase` → `JavaPlugin` → Your plugin

**Commands**:
- `AbstractCommand` → Basic commands
- `AbstractAsyncCommand` → Async commands
- `AbstractPlayerCommand` → Player-only commands
- `AbstractCommandCollection` → Subcommand groups

**UI Pages**:
- `CustomUIPage` → Base page
- `BasicCustomUIPage` → Read-only pages
- `InteractiveCustomUIPage<T>` → Pages with input

**Interactions**:
- `Interaction` → Base class
- `SimpleInstantInteraction` → One-shot actions
- `ChargingInteraction` → Hold to charge
- `ChainingInteraction` → Combo chains

### Built-in Plugin Examples

The decompiled source contains ~57 built-in plugins showing Hypixel's implementation patterns:

| Plugin | Purpose |
|--------|---------|
| `CraftingPlugin` | Recipe and crafting system |
| `WeatherPlugin` | Weather effects |
| `PortalsPlugin` | Portal mechanics |
| `ShopPlugin` | NPC shops |
| `ObjectivePlugin` | Quest objectives |
| `FarmingPlugin` | Farming mechanics |

## Prefab System

### Loading Prefabs from Plugin Assets

Plugins with `IncludesAssetPack: true` can bundle and load prefabs at runtime:

```java
// Load prefab from plugin's Server/Prefabs/ directory
Path path = PrefabStore.get().findAssetPrefabPath("MyPlugin/Segment.prefab.json");
IPrefabBuffer buffer = PrefabBufferUtil.getCached(path);

// Get dimensions
int width = buffer.getMaxX() - buffer.getMinX() + 1;
int depth = buffer.getMaxZ() - buffer.getMinZ() + 1;

// Paste on world thread
world.execute(() -> {
    PrefabUtil.paste(buffer, world, position, Rotation.None, true, new FastRandom(), accessor);
});

// CRITICAL: Release when done (e.g., plugin shutdown)
buffer.release();
```

### Region Detection

No built-in event exists for custom region entry. Use polling:

```java
HytaleServer.SCHEDULED_EXECUTOR.scheduleAtFixedRate(() -> {
    for (Player player : players) {
        Vector3d pos = player.getEntity().get(TransformComponent.class).getPosition();
        Box region = new Box(minX, minY, minZ, maxX, maxY, maxZ);
        if (region.containsPosition(Vector3d.ZERO, pos)) {
            onPlayerInRegion(player);
        }
    }
}, 0, 50, TimeUnit.MILLISECONDS);
```

See [patterns.md](patterns.md) for full implementation examples.

## Instance System (Minigames)

For isolated game worlds (minigames, dungeons), use the Instance system:

```java
// Create instance from template
CompletableFuture<World> instance = InstancesPlugin.get()
    .spawnInstance("MyMinigame", originWorld, returnTransform);

// Configure auto-cleanup when empty
instance.thenAccept(world -> {
    world.getWorldConfig().setDeleteOnRemove(true);
    InstanceWorldConfig.ensureAndGet(world.getWorldConfig())
        .setRemovalConditions(new RemovalCondition[]{ WorldEmptyCondition.INSTANCE });
});

// Return player to origin world
InstancesPlugin.exitInstance(playerRef, accessor);
```

## Player Data Persistence

Register a component with CODEC for auto-persistence to player JSON:

```java
// In setup()
this.dataType = getEntityStoreRegistry().registerComponent(
    MyData.class, "MyPluginData", MyData.CODEC);

// Usage - auto-saved on disconnect
MyData data = store.ensureAndGetComponent(playerRef, dataType);
data.setValue(100);
```

## Reference

For extended patterns (ECS, animations, packet handling, interactions, **prefabs**, **region detection**, **entity effects**), see [patterns.md](patterns.md).

For decompiled source navigation, see [reference/decompiled.md](reference/decompiled.md).
