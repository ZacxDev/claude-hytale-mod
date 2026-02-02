# Hytale Modding Patterns Reference

Extended patterns and examples for Hytale plugin development.

## Command Arguments

```java
// Required argument
private final Argument<String> nameArg;
nameArg = withArg("name", "Player name", ArgTypes.STRING);

// Optional argument
private final OptionalArg<Integer> countArg;
countArg = withOptionalArg("count", "Item count", ArgTypes.INTEGER);

// Get values in execute()
String name = ctx.get(nameArg);
Integer count = ctx.get(countArg);  // may be null
```

## Player Commands

```java
public class MyPlayerCommand extends AbstractPlayerCommand {
    public MyPlayerCommand() {
        super("mycommand", "Player-only command");
    }

    @Override
    protected CompletableFuture<Void> execute(CommandContext ctx, PlayerRef player) {
        player.sendMessage(Message.raw("Hello " + player.getUsername()));
        return CompletableFuture.completedFuture(null);
    }
}
```

## Event Priority

```java
getEventRegistry().register(EventPriority.HIGH, PlayerConnectEvent.class, event -> {
    // Runs before normal priority handlers
});
```

Priority order: `LOWEST` → `LOW` → `NORMAL` → `HIGH` → `HIGHEST` → `MONITOR`

## Async Events

```java
getEventRegistry().registerAsyncGlobal(PlayerChatEvent.class, future -> {
    return future.thenApply(event -> {
        // async processing
        return event;
    });
});
```

## Scheduled Tasks

```java
// Run once after delay (20 ticks = 1 second)
getTaskRegistry().runLater(() -> {
    getLogger().atInfo().log("Delayed task");
}, 20);

// Run repeatedly
getTaskRegistry().runRepeating(() -> {
    getLogger().atInfo().log("Repeating task");
}, 0, 20);  // start immediately, repeat every second
```

## UI Updates

### Page Updates
```java
public void updateLabel(String text) {
    UICommandBuilder cmd = new UICommandBuilder();
    cmd.set("#MyLabel.Text", text);
    sendUpdate(cmd);  // Pages use sendUpdate()
}
```

### HUD Updates
```java
public void updateStatus(String text) {
    UICommandBuilder cmd = new UICommandBuilder();
    cmd.set("#Status.TextSpans", Message.raw(text));
    update(false, cmd);  // HUD uses update()
}
```

## Event Binding Types

| Type | Trigger |
|------|---------|
| `Activating` | Button click |
| `ValueChanged` | TextField input |
| `FocusGained` | Element focused |
| `FocusLost` | Element unfocused |

## Event Data Keys

- **Static keys** (literal values): Must start with uppercase, e.g., `"Action"`
- **Reference keys** (element values): Prefix with `@`, e.g., `"@InputField"`

```java
events.addEventBinding(
    CustomUIEventBindingType.ValueChanged,
    "#NameInput",
    EventData.of("@NameInput", "#NameInput.Value"),  // @ prefix = read element
    false  // don't send on initial load
);
```

## Codec Types

```java
Codec.STRING
Codec.INTEGER
Codec.BOOLEAN
Codec.DOUBLE
Codec.FLOAT
Codec.LONG
```

## Custom Interaction

```java
public class MyInteraction extends SimpleInstantInteraction {
    public static final BuilderCodec<MyInteraction> CODEC =
        BuilderCodec.builder(MyInteraction.class,
            MyInteraction::new,
            SimpleInstantInteraction.CODEC
        ).build();

    @Override
    protected void firstRun(InteractionType type, InteractionContext ctx,
                           CooldownHandler cooldown) {
        // Interaction logic
    }

    @Override
    public float getAnimationDuration(Item item) {
        return 0.5f;
    }
}

// Register in setup()
getInteractionRegistry().register("my_interaction", MyInteraction.CODEC);
```

## Packet Watching

```java
PacketAdapters adapters = getPacketAdapters();
adapters.addWatcher(SyncInteractionChains.class, new PacketWatcher<>() {
    @Override
    public void onSend(PlayerAuthentication auth, SyncInteractionChains packet) {
        // Observe outgoing packets
    }
});
```

## ECS Components

```java
// Add component to entity
holder.addComponent(TransformComponent.getComponentType(),
    new TransformComponent(position, rotation));

// Get component from ref
TransformComponent transform = ref.get(TransformComponent.getComponentType());
```

## Model Assets

```java
ModelAsset modelAsset = ModelAsset.getAssetMap().getAsset("Minecart");
Model model = Model.createScaledModel(modelAsset, 1.0f);
```

## Animation Utils

```java
AnimationUtils.playAnimation(
    entityRef,
    AnimationSlot.Action,
    "CustomSword",          // PlayerAnimationsId
    "swing_heavy",          // Animation name
    componentAccessor
);
```

## Manifest Dependencies

```json
{
  "Dependencies": {
    "Hytale:EntityModule": "*",
    "Hytale:BlockModule": "*"
  }
}
```

## Item Quality Values

`Common`, `Uncommon`, `Rare`, `Epic`, `Legendary`

## Weapon Interaction Chain

```
Item.Interactions.Primary
    → RootInteraction (cooldown, click handling)
        → Interaction chain (animation, timing)
            → Selector (hit detection)
                → Damage interaction
```

## .blockyanim Format

```json
{
  "formatVersion": 1,
  "duration": 30,
  "holdLastKeyframe": false,
  "nodeAnimations": {
    "NodeName": {
      "position": [],
      "orientation": [],
      "shapeStretch": [],
      "shapeVisible": [],
      "shapeUvOffset": []
    }
  }
}
```

- Duration in ticks (20 = 1 second)
- Orientation uses quaternions (x, y, z, w)
- Interpolation: `"smooth"` or `"step"`

## Page Lifetime

| Value | Behavior |
|-------|----------|
| `CantClose` | Player cannot dismiss |
| `CanDismiss` | Escape to close |
| `CanDismissOrCloseThroughInteraction` | Escape or UI interaction |

## UI Class Hierarchy

```
CustomUIPage (abstract — 4-param build)
├── BasicCustomUIPage (abstract — 1-param build, read-only)
└── InteractiveCustomUIPage<T> (abstract — typed event handling)

CustomUIHud (abstract — 1-param build)
```

## Full EventRegistry Methods

| Method | Key Type | Use Case |
|--------|----------|----------|
| `register(Class, Consumer)` | `Void` only | Unkeyed events |
| `register(Class, K, Consumer)` | Any `K` | Keyed events filtered by key |
| `registerGlobal(Class, Consumer)` | Any `K` | All keyed events |
| `registerAsync(Class, Function)` | `Void` only | Async unkeyed |
| `registerAsyncGlobal(Class, Function)` | Any `K` | Async all keyed |
| `registerUnhandled(Class, Consumer)` | Any `K` | Unhandled events only |

## Complete Working Weapon Example

Based on a tested flail weapon mod. This pattern works.

### Directory Structure

```
flail-mod/src/main/resources/
├── manifest.json
├── Server/Item/Items/
│   └── Weapon_Flail_Iron.json
└── Common/
    ├── Icons/ItemsGenerated/
    │   └── Weapon_Flail_Iron.png         # 64x64, 8-bit RGBA
    └── Items/Weapon_Flail_Iron/
        ├── Weapon_Flail_Iron.blockymodel
        └── Weapon_Flail_Iron_Texture.png  # 8-bit RGBA
```

### manifest.json

```json
{
  "Group": "com.example.hytale",
  "Name": "FlailMod",
  "Version": "1.0.0",
  "Description": "A chain flail weapon",
  "Authors": [{ "Name": "Author", "Email": "", "Url": "" }],
  "Website": "",
  "Main": "com.example.flailmod.FlailPlugin",
  "Dependencies": {
    "Hytale:EntityModule": "*",
    "Hytale:BlockModule": "*"
  },
  "OptionalDependencies": {},
  "LoadBefore": {},
  "DisabledByDefault": false,
  "IncludesAssetPack": true,
  "SubPlugins": []
}
```

### Server/Item/Items/Weapon_Flail_Iron.json

```json
{
  "TranslationProperties": {
    "Name": "Iron Flail",
    "Description": "A heavy chain flail weapon"
  },
  "Id": "Weapon_Flail_Iron",
  "Icon": "Icons/ItemsGenerated/Weapon_Flail_Iron.png",
  "Model": "Items/Weapon_Flail_Iron/Weapon_Flail_Iron.blockymodel",
  "Texture": "Items/Weapon_Flail_Iron/Weapon_Flail_Iron_Texture.png",
  "PlayerAnimationsId": "Battleaxe",
  "Quality": "Uncommon",
  "MaxStack": 1,
  "Categories": ["Items.Weapons"]
}
```

**Key points:**
- `Id` MUST match filename (without `.json`) — required for asset loader
- `PlayerAnimationsId: "Battleaxe"` reuses vanilla battleaxe animations
- Model/Texture paths reference bundled files, NOT vanilla assets
- `Categories: ["Items.Weapons"]` for proper inventory categorization

### Simple .blockymodel (handle + ball)

```json
{
  "lod": "auto",
  "nodes": [{
    "id": "1", "name": "R-Attachment",
    "position": { "x": 0, "y": 0, "z": 0 },
    "orientation": { "x": 0, "y": 0, "z": 0, "w": 1 },
    "shape": { "type": "none", "settings": { "isPiece": true } },
    "children": [{
      "id": "2", "name": "Origin_Item",
      "position": { "x": 0, "y": 0, "z": 0 },
      "orientation": { "x": 0, "y": 0, "z": 0, "w": 1 },
      "shape": { "type": "none", "settings": { "isPiece": true } },
      "children": [
        {
          "id": "3", "name": "Handle",
          "position": { "x": 0, "y": 15, "z": 0 },
          "orientation": { "x": 0, "y": 0, "z": 0, "w": 1 },
          "shape": {
            "type": "box",
            "settings": { "size": { "x": 6, "y": 30, "z": 6 } },
            "textureLayout": {
              "front": { "offset": { "x": 0, "y": 0 } },
              "left": { "offset": { "x": 6, "y": 0 } },
              "back": { "offset": { "x": 12, "y": 0 } },
              "right": { "offset": { "x": 18, "y": 0 } },
              "top": { "offset": { "x": 0, "y": 30 } },
              "bottom": { "offset": { "x": 6, "y": 30 } }
            }
          }
        },
        {
          "id": "4", "name": "Flail_Head",
          "position": { "x": 0, "y": 50, "z": 0 },
          "orientation": { "x": 0, "y": 0, "z": 0, "w": 1 },
          "shape": {
            "type": "box",
            "settings": { "size": { "x": 12, "y": 12, "z": 12 } },
            "textureLayout": {
              "front": { "offset": { "x": 36, "y": 0 } },
              "left": { "offset": { "x": 48, "y": 0 } },
              "back": { "offset": { "x": 60, "y": 0 } },
              "right": { "offset": { "x": 72, "y": 0 } },
              "top": { "offset": { "x": 36, "y": 12 } },
              "bottom": { "offset": { "x": 48, "y": 12 } }
            }
          }
        }
      ]
    }]
  }]
}
```

### Create Placeholder Texture with ImageMagick

```bash
# Create simple gray metal texture (8-bit RGBA)
magick -size 96x48 xc:'#808080' \
  -fill '#5a5a5a' -draw "rectangle 0,0 24,36" \
  -fill '#6a6a6a' -draw "rectangle 36,0 84,24" \
  -depth 8 -type TrueColorAlpha PNG32:Weapon_Flail_Iron_Texture.png

# Verify format
file Weapon_Flail_Iron_Texture.png  # Should show "8-bit/color RGBA"
```

### Java Plugin + GiveCommand

```java
// FlailPlugin.java
package com.example.flailmod;

import com.example.flailmod.commands.GiveFlailCommand;
import com.hypixel.hytale.server.core.plugin.JavaPlugin;
import com.hypixel.hytale.server.core.plugin.JavaPluginInit;
import javax.annotation.Nonnull;

public class FlailPlugin extends JavaPlugin {
    public FlailPlugin(@Nonnull JavaPluginInit init) {
        super(init);
        getLogger().atInfo().log("FlailMod loaded!");
    }

    @Override
    protected void setup() {
        getCommandRegistry().registerCommand(new GiveFlailCommand());
        getLogger().atInfo().log("FlailMod setup complete!");
    }

    @Override
    protected void start() {
        getLogger().atInfo().log("FlailMod enabled!");
    }

    @Override
    protected void shutdown() {
        getLogger().atInfo().log("FlailMod disabled!");
    }
}

// commands/GiveFlailCommand.java
package com.example.flailmod.commands;

import com.hypixel.hytale.server.core.Message;
import com.hypixel.hytale.server.core.command.system.AbstractCommand;
import com.hypixel.hytale.server.core.command.system.CommandContext;
import java.util.concurrent.CompletableFuture;

public class GiveFlailCommand extends AbstractCommand {
    public GiveFlailCommand() {
        super("giveflail", "Give yourself the Iron Flail weapon");
    }

    @Override
    protected CompletableFuture<Void> execute(CommandContext ctx) {
        ctx.sendMessage(Message.raw("Use /spawnitem flailmod:Weapon_Flail_Iron"));
        return CompletableFuture.completedFuture(null);
    }

    @Override
    protected boolean canGeneratePermission() {
        return false;  // Allow all players
    }
}
```

### Build and Deploy

```bash
# Build
gradle -p flail-mod shadowJar

# Deploy (IMPORTANT: server/Server/mods/, NOT server/mods/)
cp flail-mod/build/libs/FlailMod-1.0.0.jar server/Server/mods/

# Start server and verify in logs:
# [PluginManager] - com.example.hytale:FlailMod from path FlailMod-1.0.0.jar
# [FlailMod|P] FlailMod loaded!
# [AssetStore|Item] Unused key(s) in 'Weapon_Flail_Iron' file ...: Id  # This WARNING means it WORKS

# Test in-game
/spawnitem flailmod:Weapon_Flail_Iron
```

### Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Item not found | Missing `Id` field | Add `"Id": "ItemName"` to JSON |
| Item not found | Model/Texture paths point to vanilla | Bundle assets in `Common/` |
| Plugin not loaded | JAR in wrong folder | Use `server/Server/mods/` |
| No logs at all | Manifest error | Check `manifest.json` syntax |
| 16-bit PNG crash | Wrong PNG format | Use `magick ... PNG32:output.png` |
