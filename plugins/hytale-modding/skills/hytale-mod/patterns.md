# Hytale Modding Patterns Reference

Extended patterns and examples for Hytale plugin development.

## Prefab Loading and Pasting

### Loading Prefabs from Plugin Assets

Plugins with `IncludesAssetPack: true` can bundle and load prefabs:

```java
public class PrefabManager {
    private final Map<String, IPrefabBuffer> cache = new HashMap<>();

    /**
     * Load prefab from plugin's bundled assets.
     * Path relative to Server/Prefabs/ directory.
     */
    public IPrefabBuffer loadPrefab(String prefabKey) {
        Path path = PrefabStore.get().findAssetPrefabPath(prefabKey);
        if (path == null) {
            throw new IllegalArgumentException("Prefab not found: " + prefabKey);
        }
        IPrefabBuffer buffer = PrefabBufferUtil.getCached(path);
        cache.put(prefabKey, buffer);
        return buffer;
    }

    /**
     * Get prefab dimensions.
     */
    public Vector3i getDimensions(IPrefabBuffer buffer) {
        return new Vector3i(
            buffer.getMaxX() - buffer.getMinX() + 1,
            buffer.getMaxY() - buffer.getMinY() + 1,
            buffer.getMaxZ() - buffer.getMinZ() + 1
        );
    }

    /**
     * Paste prefab at position (thread-safe).
     */
    public void pastePrefab(IPrefabBuffer buffer, World world, Vector3i position) {
        world.execute(() -> {
            PrefabUtil.paste(
                buffer,
                world,
                position,
                Rotation.None,
                true,  // loadEntities
                new FastRandom(),
                world.getEntityStore().getComponentAccessor()
            );
        });
    }

    /**
     * Release all cached buffers. Call on plugin shutdown.
     */
    public void shutdown() {
        for (IPrefabBuffer buffer : cache.values()) {
            buffer.release();
        }
        cache.clear();
    }
}
```

**Plugin Manifest** (required):
```json
{
  "IncludesAssetPack": true
}
```

**Directory Structure**:
```
plugin/src/main/resources/
├── Server/Prefabs/
│   └── MyPlugin/
│       ├── Segment_A.prefab.json
│       └── Segment_B.prefab.json
└── manifest.json
```

### Chaining Prefabs

Calculate next position based on direction:

```java
public Vector3i getNextPosition(Vector3i current, Vector3i dimensions, Direction dir) {
    return switch (dir) {
        case NORTH -> new Vector3i(current.x, current.y, current.z - dimensions.z);
        case SOUTH -> new Vector3i(current.x, current.y, current.z + dimensions.z);
        case EAST  -> new Vector3i(current.x + dimensions.x, current.y, current.z);
        case WEST  -> new Vector3i(current.x - dimensions.x, current.y, current.z);
        default    -> current;
    };
}
```

## Custom Region Entry Detection

**No built-in event exists** for detecting when players enter custom regions. Use polling or ECS ticking.

### Simple Polling Approach

```java
public class RegionManager {
    private final Map<UUID, Box> regions = new ConcurrentHashMap<>();
    private final Map<UUID, UUID> playerCurrentRegion = new ConcurrentHashMap<>();

    public void startTracking() {
        HytaleServer.SCHEDULED_EXECUTOR.scheduleAtFixedRate(() -> {
            for (Player player : getOnlinePlayers()) {
                checkPlayerRegion(player);
            }
        }, 0, 50, TimeUnit.MILLISECONDS);  // 20 checks per second
    }

    private void checkPlayerRegion(Player player) {
        TransformComponent transform = player.getEntity().get(TransformComponent.class);
        if (transform == null) return;

        Vector3d pos = transform.getPosition();
        UUID playerId = player.getUuid();

        for (Map.Entry<UUID, Box> entry : regions.entrySet()) {
            if (entry.getValue().containsPosition(Vector3d.ZERO, pos)) {
                UUID currentRegion = playerCurrentRegion.get(playerId);
                if (!entry.getKey().equals(currentRegion)) {
                    playerCurrentRegion.put(playerId, entry.getKey());
                    onPlayerEnteredRegion(player, entry.getKey());
                }
                return;
            }
        }
        // Player not in any region
        playerCurrentRegion.remove(playerId);
    }

    public void registerRegion(UUID id, Vector3i min, Vector3i max) {
        regions.put(id, new Box(min.x, min.y, min.z, max.x, max.y, max.z));
    }

    protected void onPlayerEnteredRegion(Player player, UUID regionId) {
        // Override to handle region entry
    }
}
```

### ECS Ticking System Approach (Advanced)

For better integration with Hytale's ECS:

```java
public class RegionDetectionSystem implements TickingSystem<EntityStore> {
    private final Map<UUID, Box> regions = new ConcurrentHashMap<>();
    private final Map<UUID, UUID> lastRegion = new ConcurrentHashMap<>();

    @Override
    public void tick(Store<EntityStore> store, float deltaTime) {
        store.forEach(Player.class, (ref, player) -> {
            TransformComponent transform = store.getComponent(ref, TransformComponent.class);
            if (transform == null) return;

            Vector3d pos = transform.getPosition();
            UUID playerId = player.getUuid();

            for (Map.Entry<UUID, Box> entry : regions.entrySet()) {
                if (entry.getValue().containsPosition(Vector3d.ZERO, pos)) {
                    if (!entry.getKey().equals(lastRegion.get(playerId))) {
                        lastRegion.put(playerId, entry.getKey());
                        onRegionEnter(player, entry.getKey());
                    }
                    return;
                }
            }
            lastRegion.remove(playerId);
        });
    }

    protected void onRegionEnter(Player player, UUID regionId) {
        // Handle region entry
    }
}
```

### Performance Considerations

| Players | Regions | Approach |
|---------|---------|----------|
| < 10 | < 20 | Simple polling |
| 10-50 | < 100 | ECS ticking |
| > 50 | > 100 | Spatial partitioning (octree) |

**Caveats**:
- Fast-moving players could skip regions between checks
- Polling runs on executor thread - wrap world modifications in `world.execute()`
- ECS ticking runs on world thread - safe for direct modifications

## Instance System for Minigames

Hytale has a built-in **InstancesPlugin** for isolated game instances. Use this for minigames instead of modifying the main world.

### Creating an Instance

**Critical**: `VoidWorldGenProvider` ignores the `SpawnProvider` in `instance.bson`. You MUST set the spawn provider explicitly on the WorldConfig.

```java
// Create instance from template (Server/Instances/YourGame/)
InstancesPlugin.get()
    .spawnInstance("YourMinigame", originWorld, returnTransform)
    .thenCompose(world -> {
        // CRITICAL: Set spawn provider explicitly (VoidWorldGen ignores instance.bson)
        world.getWorldConfig().setSpawnProvider(
            new GlobalSpawnProvider(new Transform(
                new Vector3d(0.0, 3.0, 0.0),  // Spawn position
                new Vector3f(0.0f, 180.0f, 0.0f)  // Facing direction
            ))
        );

        // Configure auto-cleanup
        WorldConfig config = world.getWorldConfig();
        config.setDeleteOnRemove(true);

        InstanceWorldConfig instanceConfig = InstanceWorldConfig.ensureAndGet(config);
        instanceConfig.setRemovalConditions(new RemovalCondition[]{
            WorldEmptyCondition.INSTANCE
        });

        // Initialize on world thread, then continue
        CompletableFuture<World> initFuture = new CompletableFuture<>();
        world.execute(() -> {
            // Place blocks, spawn entities here (synchronously)
            initFuture.complete(world);
        });
        return initFuture;
    })
    .thenApply(world -> {
        // AFTER initialization, teleport player
        teleportPlayerToWorld(playerRef, world);
        return world;
    });
```

### Y Coordinate Constraints

**Critical**: Valid Y range is **0-320**. Blocks at negative Y are silently ignored.

```java
// BAD - silently fails
world.setBlock(x, -3, z, blockType);  // Does nothing!

// GOOD - valid range
world.setBlock(x, 0, z, blockType);   // Works
```

### Removal Conditions

| Condition | Behavior |
|-----------|----------|
| `WorldEmptyCondition` | Remove when all players leave (with timeout) |
| `TimeoutCondition` | Remove after fixed time |
| `IdleTimeoutCondition` | Remove after idle period |

### Player Management

```java
// Teleport player to instance
InstancesPlugin.teleportPlayerToInstance(playerRef, accessor, targetWorld, returnOverride);

// Return player to origin world
InstancesPlugin.exitInstance(playerRef, accessor);
```

### Advantages Over In-World Segments

- Complete isolation (no terrain restoration needed)
- Built-in player return mechanics
- Automatic cleanup when empty
- Fresh state every session

## Entity Spawning in Prefabs

### Enabling Entity Spawning

Use the full `paste()` signature with `loadEntities: true`:

```java
PrefabUtil.paste(
    buffer,
    world,
    position,
    Rotation.None,
    true,   // force
    new FastRandom(),
    0,      // setBlockSettings
    false,  // technicalPaste
    false,  // pasteAnchorAsBlock
    true,   // loadEntities - CRITICAL for mobs
    world.getEntityStore().getComponentAccessor()
);
```

**Note**: Simple `paste()` overloads default to `loadEntities: false`.

### Tracking Spawned Entities

Entities are NOT auto-cleaned when prefabs are removed. Track them manually:

```java
public class EntityTracker {
    private final List<Ref<EntityStore>> spawnedEntities = new ArrayList<>();

    // Register listener in setup()
    public void setup(EntityStore entityStore) {
        entityStore.registerEventListener(PrefabPlaceEntityEvent.class, event -> {
            spawnedEntities.add(event.getEntityRef());
        });
    }

    // Cleanup all tracked entities
    public void cleanup(World world) {
        world.execute(() -> {
            Store<EntityStore> store = world.getEntityStore().getStore();
            for (Ref<EntityStore> ref : spawnedEntities) {
                if (ref.validate()) {
                    store.removeEntity(ref, RemoveReason.REMOVE);
                }
            }
            spawnedEntities.clear();
        });
    }
}
```

### Conditional Entity Spawning

Intercept `PrefabPlaceEntityEvent` to modify or cancel spawns:

```java
entityStore.registerEventListener(PrefabPlaceEntityEvent.class, event -> {
    if (gameDifficulty < 5) {
        event.setCancelled(true);  // Don't spawn this entity
    }
});
```

## Player Data Persistence

### Component with CODEC (Auto-Persisted)

Register a component with a CODEC for automatic persistence to player JSON:

```java
public class PlayerGameData implements Component<EntityStore> {

    public static final BuilderCodec<PlayerGameData> CODEC = BuilderCodec
        .builder(PlayerGameData.class, PlayerGameData::new)
        .append(new KeyedCodec<>("HighScore", Codec.INTEGER),
            (d, v) -> d.highScore = v, d -> d.highScore)
        .add()
        .append(new KeyedCodec<>("TotalPlays", Codec.INTEGER),
            (d, v) -> d.totalPlays = v, d -> d.totalPlays)
        .add()
        .append(new KeyedCodec<>("Unlocks", Codec.STRING_ARRAY),
            (d, v) -> { if (v != null) Collections.addAll(d.unlocks, v); },
            d -> d.unlocks.toArray(String[]::new))
        .add()
        .build();

    private int highScore = 0;
    private int totalPlays = 0;
    private final Set<String> unlocks = new HashSet<>();

    @Override
    public Component<EntityStore> clone() {
        PlayerGameData c = new PlayerGameData();
        c.highScore = this.highScore;
        c.totalPlays = this.totalPlays;
        c.unlocks.addAll(this.unlocks);
        return c;
    }

    // Getters/setters...
}
```

### Registration

```java
@Override
protected void setup() {
    // Register with name + CODEC = auto-persisted
    this.gameDataType = getEntityStoreRegistry().registerComponent(
        PlayerGameData.class,
        "MyPluginData",  // Key in player JSON
        PlayerGameData.CODEC
    );
}
```

### Usage

```java
// Get or create (creates if not exists)
PlayerGameData data = store.ensureAndGetComponent(playerRef, gameDataType);

// Modify - auto-saved on player disconnect or world save
data.setHighScore(Math.max(data.getHighScore(), newScore));
data.incrementTotalPlays();
```

### Storage Approaches Summary

| Approach | Persistence | Location | Use Case |
|----------|-------------|----------|----------|
| Component + CODEC | Automatic | `players/{uuid}.json` | Per-player progression |
| `getDataDirectory()` | Manual | `mods/PluginName/` | Global leaderboards |
| Component (no CODEC) | None | Memory | Session-only state |

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
