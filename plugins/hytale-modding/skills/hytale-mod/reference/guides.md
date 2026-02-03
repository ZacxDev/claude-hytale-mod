# Community Guides Reference

Comprehensive patterns from hytalemodding.dev community documentation.

## Command Types

### AbstractAsyncCommand
Runs on background thread - cannot edit Stores/Refs without getting world first.

```java
public class ServerRulesCommand extends AbstractAsyncCommand {
    public ServerRulesCommand() {
        super("rules", "Lists the servers rules");
    }

    @Override
    protected CompletableFuture<Void> executeAsync(@Nonnull CommandContext context) {
        context.sendMessage(Message.raw("The only rule is there are no rules."));
        return CompletableFuture.completedFuture(null);
    }
}
```

### AbstractPlayerCommand
Tied to player and world - runs on world thread, can safely access Store and Refs.

```java
public class ExampleCommand extends AbstractPlayerCommand {
    public ExampleCommand() {
        super("test", "Super test command!");
    }

    @Override
    protected void execute(@Nonnull CommandContext ctx, @Nonnull Store<EntityStore> store,
                          @Nonnull Ref<EntityStore> ref, @Nonnull PlayerRef playerRef,
                          @Nonnull World world) {
        Player player = store.getComponent(ref, Player.getComponentType());
        UUIDComponent component = store.getComponent(ref, UUIDComponent.getComponentType());
        TransformComponent transform = store.getComponent(ref, TransformComponent.getComponentType());
        player.sendMessage(Message.raw("Transform: " + transform.getPosition()));
    }
}
```

### AbstractTargetPlayerCommand
Like AbstractPlayerCommand but adds `--player <name>` argument.

### AbstractTargetEntityCommand
Uses raycast to target entity player is looking at.

```java
@Override
protected void execute(CommandContext context, Store<EntityStore> store,
                       Ref<EntityStore> ref, World world) {
    EntityStatMap stats = store.getComponent(ref, EntityStatMap.getComponentType());
    if (stats == null) {
        context.sendMessage(Message.raw("This entity has no stats!"));
        return;
    }
    int healthIdx = DefaultEntityStatTypes.getHealth();
    stats.addValue(healthIdx, 100);
}
```

## Command Arguments

| Type | Method | Usage |
|------|--------|-------|
| Required | `withRequiredArg` | Parsed left-to-right, no flag needed |
| Optional | `withOptionalArg` | Requires `--key value` syntax |
| Default | `withDefaultArg` | Returns default if not provided |
| Flag | `withFlagArg` | Boolean switch (`--debug`) |

### ArgTypes Available
- `ArgTypes.STRING`, `ArgTypes.INTEGER`, `ArgTypes.BOOLEAN`
- `ArgTypes.FLOAT`, `ArgTypes.DOUBLE`, `ArgTypes.UUID`
- `ArgTypes.PLAYER_REF` (for targeting players)

### Full Example
```java
public class HealPlayerCommand extends AbstractTargetPlayerCommand {
    private final DefaultArg<Float> healthArg;
    private final OptionalArg<String> messageArg;
    private final FlagArg debugArg;

    public HealPlayerCommand() {
        super("healplayer", "Heal a player");
        this.healthArg = withDefaultArg("health", "Amount", ArgTypes.FLOAT, 100f, "Default: 100");
        this.messageArg = withOptionalArg("message", "Message", ArgTypes.STRING);
        this.debugArg = withFlagArg("debug", "Debug mode");
    }

    @Override
    protected void execute(CommandContext ctx, Ref<EntityStore> targetRef,
                          Ref<EntityStore> senderRef, PlayerRef playerRef,
                          World world, Store<EntityStore> store) {
        float health = healthArg.get(ctx);  // Get value via context
        String msg = messageArg.get(ctx);   // May be null
        boolean debug = debugArg.get(ctx);  // true or false

        EntityStatMap stats = store.getComponent(targetRef, EntityStatMap.getComponentType());
        stats.addStatValue(DefaultEntityStatTypes.getHealth(), health);
    }
}
```

### Argument Validators
```java
OptionalArg<Integer> amount = withOptionalArg("amount", "Amount", ArgTypes.INTEGER)
    .addValidator(Validators.greaterThan(0))
    .addValidator(Validators.lessThan(1000));

// Custom validator
OptionalArg<Integer> amount = withOptionalArg("amount", "Amount", ArgTypes.INTEGER)
    .addValidator(value -> {
        if (value != null && value <= 0) {
            return ValidationResult.error("must be positive");
        }
        return ValidationResult.success();
    });
```

## Command Permissions
```java
public HealPlayerCommand() {
    super("healplayer", "Heal a player");
    requirePermission(HytalePermissions.fromCommand("rules"));
    requirePermission(
        PermissionRules.or(
            HytalePermissions.fromCommand("moderator"),
            HytalePermissions.fromCommand("admin")
        )
    );
}
```

## Command Variants & Aliases
```java
public class GiveCommand extends AbstractPlayerCommand {
    public GiveCommand() {
        super("give", "Give item to yourself");
        addUsageVariant(new GiveOtherCommand());
        addAliases("gv", "gMe");
    }
}

// Variant - no command name in super()
public class GiveOtherCommand extends AbstractAsyncCommand {
    public GiveOtherCommand() {
        super("Give item to another player");  // Description only
        this.playerArg = withRequiredArg("player", "Target", ArgTypes.PLAYER_REF);
    }
}
```

## Subcommands (AbstractCommandCollection)
```java
public class AdminCommand extends AbstractCommandCollection {
    public AdminCommand() {
        super("admin", "Admin commands");
        addSubCommand(new UserCommandCollection());
        addSubCommand(new ServerCommandCollection());
    }
}
// Results in: /admin user rules, /admin server restart
```

## ECS Core Concepts

### Store
Core of ECS - stores entities using Archetypes (grouped data chunks).

### EntityStore
Implements WorldProvider - accesses specific Hytale World. Has `entitiesByUuid` and `networkIdToRef` maps.

### ChunkStore
Stores block-related components. Contains WorldChunk, EntityChunk, BlockChunk, BlockSection.

### Holder
Blueprint for entity before it exists in Store. Like a shopping cart - collect components, then "checkout" to get Ref.

### Ref (Reference)
Safe handle/pointer to entity. Never store direct entity references. Call `validate()` to check if entity still alive.

### PlayerRef vs Player
- **PlayerRef**: Component for connection/identity. Stays active across world switches.
- **Player**: Component for physical presence. Only exists when spawned in world.

## Creating Custom Components

```java
public class PoisonComponent implements Component<EntityStore> {
    private float damagePerTick;
    private float tickInterval;
    private int remainingTicks;

    public PoisonComponent() {
        this(5f, 1.0f, 10);
    }

    public PoisonComponent(float damage, float interval, int ticks) {
        this.damagePerTick = damage;
        this.tickInterval = interval;
        this.remainingTicks = ticks;
    }

    // Copy constructor required
    public PoisonComponent(PoisonComponent other) {
        this.damagePerTick = other.damagePerTick;
        this.tickInterval = other.tickInterval;
        this.remainingTicks = other.remainingTicks;
    }

    @Nullable
    @Override
    public Component<EntityStore> clone() {
        return new PoisonComponent(this);
    }

    // Getters and setters...
}
```

## BuilderCodec for Components

```java
public static final BuilderCodec<PoisonComponent> CODEC = BuilderCodec.builder(
        PoisonComponent.class, PoisonComponent::new)
    .append(
        new KeyedCodec<Float>("DamagePerTick", Codec.FLOAT),
        (data, value) -> data.damagePerTick = value,
        (data) -> data.damagePerTick
    )
    .add()
    .append(
        new KeyedCodec<String>("PoisonName", Codec.STRING),
        (data, value) -> data.poisonName = value,
        (data) -> data.poisonName
    )
    .addValidator(Validators.nonNull())
    .add()
    .build();
```

**Important**: KeyedCodec identifiers must start uppercase and be unique across entire mod.

### Available Codecs
```
Codec.STRING, Codec.BOOLEAN, Codec.DOUBLE, Codec.FLOAT
Codec.BYTE, Codec.SHORT, Codec.INTEGER, Codec.LONG
Codec.DOUBLE_ARRAY, Codec.FLOAT_ARRAY, Codec.INT_ARRAY
Codec.LONG_ARRAY, Codec.STRING_ARRAY, Codec.PATH
Codec.INSTANT, Codec.DURATION, Codec.UUID_BINARY, Codec.UUID_STRING
```

### Map Codec
```java
var mapCodec = new KeyedCodec<>("DamageMap",
    new MapCodec<>(Codec.FLOAT, HashMap<String, Float>::new));
```

## CommandBuffer
Queue changes to entities for thread safety:

```java
commandBuffer.addComponent(ref, componentType, new MyComponent());
commandBuffer.removeComponent(ref, componentType);
MyComponent comp = commandBuffer.getComponent(ref, componentType);
```

## Player Stats

### Available Stats (DefaultEntityStatTypes)
- `getHealth()`, `getStamina()`, `getMana()`
- `getOxygen()`, `getSignatureEnergy()`, `getAmmo()`

### Modifying Stats
```java
world.execute(() -> {
    EntityStatMap statMap = store.getComponent(playerRef, EntityStatMap.getComponentType());
    if (statMap != null) {
        statMap.maximizeStatValue(DefaultEntityStatTypes.getHealth());
        statMap.addStatValue(DefaultEntityStatTypes.getStamina(), 50);
        statMap.subtractStatValue(DefaultEntityStatTypes.getHealth(), 10);
        statMap.setStatValue(DefaultEntityStatTypes.getMana(), 100);
        statMap.resetStatValue(DefaultEntityStatTypes.getOxygen());
    }
});
```

## Teleporting Players

```java
public static void teleportPlayer(Player player, int x, int y, int z) {
    World world = player.getWorld();
    if (world == null) return;

    world.execute(() -> {
        if (player.getReference() == null) return;
        Store<EntityStore> store = player.getReference().getStore();
        Teleport teleport = Teleport.createForPlayer(world,
            new Vector3d(x, y, z),    // Target position
            new Vector3f(0, 0, 0)     // Target rotation
        );
        store.addComponent(player.getReference(), Teleport.getComponentType(), teleport);
    });
}
```

## Spawning Entities

```java
World world = player.getWorld();
Store<EntityStore> store = world.getEntityStore().getStore();

world.execute(() -> {
    // Create blank holder
    Holder<EntityStore> holder = EntityStore.REGISTRY.newHolder();

    // Get model
    ModelAsset modelAsset = ModelAsset.getAssetMap().getAsset("Minecart");
    Model model = Model.createScaledModel(modelAsset, 1.0f);

    // Add components
    Vector3d position = new Vector3d(0, 100, 0);
    holder.addComponent(TransformComponent.getComponentType(),
        new TransformComponent(position, new Vector3f(0, 0, 0)));
    holder.addComponent(PersistentModel.getComponentType(),
        new PersistentModel(model.toReference()));
    holder.addComponent(ModelComponent.getComponentType(),
        new ModelComponent(model));
    holder.addComponent(BoundingBox.getComponentType(),
        new BoundingBox(model.getBoundingBox()));
    holder.addComponent(NetworkId.getComponentType(),
        new NetworkId(store.getExternalData().takeNextNetworkId()));
    holder.addComponent(Interactions.getComponentType(), new Interactions());

    // Ensure required components
    holder.ensureComponent(UUIDComponent.getComponentType());
    holder.ensureComponent(Interactable.getComponentType());

    // Spawn
    store.addEntity(holder, AddReason.SPAWN);
});
```

## Inventory Management

### Accessing Inventory
```java
Inventory inventory = player.getInventory();
```

### ItemContainer Methods
- `.getStorage()`, `.getArmor()`, `.getBackpack()`
- `.getHotbar()`, `.getUtility()`
- `.getCombinedEverything()`, `.getCombinedHotbarFirst()`

### Creating ItemStack
```java
ItemStack item = new ItemStack("Stone");
ItemStack withQty = new ItemStack("Stone", 64);

// With metadata
BsonDocument metadata = new BsonDocument();
metadata.append("customData", new BsonString("value"));
ItemStack itemMeta = new ItemStack("Stone", 64, metadata);

// With durability
ItemStack weapon = new ItemStack("DiamondSword", 1, 100.0, 100.0, metadata);
```

### Add/Remove Items
```java
ItemContainer storage = inventory.getStorage();
storage.addItemStack(item);
storage.addItemStackToSlot((short) 4, item);
storage.removeItemStack(item);
storage.removeItemStackFromSlot((short) 4);
```

### Opening Pages
```java
PageManager pageManager = player.getPageManager();
Store<EntityStore> store = player.getWorld().getEntityStore().getStore();
pageManager.setPage(player.getReference(), store, Page.Inventory);
// Available: Page.None, Page.Bench, Page.Inventory, Page.Map, Page.Custom
```

## Playing Sounds

```java
int index = SoundEvent.getAssetMap().getIndex("SFX_Cactus_Large_Hit");
World world = player.getWorld();
EntityStore entityStore = world.getEntityStore();
Ref<EntityStore> playerRef = player.getReference();

world.execute(() -> {
    TransformComponent transform = entityStore.getStore().getComponent(
        playerRef, EntityModule.get().getTransformComponentType());
    SoundUtil.playSoundEvent3dToPlayer(
        playerRef, index, SoundCategory.UI, transform.getPosition(), entityStore.getStore());
});
```

### SoundCategory
`Music`, `Ambient`, `SFX`, `UI`

## Custom UI

### File Location
`.ui` files must be in `resources/Common/UI/Custom/`

**Manifest requirement**: `"IncludesAssetPack": true`

### Troubleshooting Custom UI
**CRITICAL**: Enable **Diagnostic Mode** in Hytale client settings (General tab) to see detailed UI parsing errors. Without this, you only get generic "Failed to load CustomUI documents" errors.

### CustomUIHud (Gameplay Overlay)

HUD elements persist during gameplay and cannot be interacted with.

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
        ui.append("MyScoreHud.ui");  // File at Common/UI/Custom/MyScoreHud.ui
        ui.set("#ScoreValue.Text", String.valueOf(score));
    }

    public void updateScore(int newScore) {
        if (newScore == this.score) return;
        this.score = newScore;
        UICommandBuilder ui = new UICommandBuilder();
        ui.set("#ScoreValue.Text", String.valueOf(score));
        update(false, ui);  // false = don't clear existing UI
    }
}

// Show HUD
HudManager hudManager = player.getHudManager();
hudManager.setCustomHud(playerRef, new ScoreHud(playerRef));

// Remove HUD
hudManager.setCustomHud(playerRef, null);
```

### .ui File Format Reference

#### Working Example (Minimal HUD)
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

#### Valid Anchor Properties
All values are integers:
| Property | Description |
|----------|-------------|
| `Left` | Distance from left edge |
| `Right` | Distance from right edge |
| `Top` | Distance from top edge |
| `Bottom` | Distance from bottom edge |
| `Width` | Element width |
| `Height` | Element height |
| `Full` | Full sizing value |
| `Horizontal` | Horizontal positioning |
| `Vertical` | Vertical positioning |
| `MinWidth` | Minimum width constraint |
| `MaxWidth` | Maximum width constraint |

**INVALID**: `Fill`, `Center` (these don't exist)

#### Valid LayoutMode Values
| Value | Description |
|-------|-------------|
| `Center` | Center children |
| `Top` | Stack from top |
| `Left` | Stack from left |

**INVALID**: `TopRight`, `BottomLeft`, `Right`, `Bottom` (these cause parsing errors)

#### Valid Style Properties for Label
```
Style: (FontSize: 24);
Style: (Alignment: Center);
Style: (FontSize: 24, Alignment: Center);
```

**Valid Alignment values**: `Center` (others may not work)

#### UI Element Types
| Element | Purpose |
|---------|---------|
| `Group` | Container (like HTML div) |
| `Label` | Text display |
| `TextField` | User input field |
| `Button` | Clickable button |

#### Common UI Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `could not resolve expression for property LayoutMode` | Invalid LayoutMode value | Use `Center`, `Top`, or `Left` only |
| `could not resolve expression for property Alignment` | Invalid Alignment value | Use `Center` only |
| `could not find field X in type Anchor` | Invalid Anchor property | Check valid properties above |
| `Could not find document XXXXX` | File path mismatch | Verify file exists at `Common/UI/Custom/filename.ui` |
| `Failed to load CustomUI documents` | Generic parsing error | Enable Diagnostic Mode for details |

### InteractiveCustomUIPage
```java
public class MyPage extends InteractiveCustomUIPage<MyPage.Data> {
    public MyPage(PlayerRef playerRef) {
        super(playerRef, CustomPageLifetime.CanDismiss, Data.CODEC);
    }

    @Override
    public void build(Ref<EntityStore> ref, UICommandBuilder ui,
                      UIEventBuilder events, Store<EntityStore> store) {
        ui.append("MyPage.ui");
        events.addEventBinding(
            CustomUIEventBindingType.ValueChanged,
            "#MyInput",
            EventData.of("@MyInput", "#MyInput.Value"),
            false
        );
    }

    @Override
    public void handleDataEvent(Ref<EntityStore> ref, Store<EntityStore> store, Data data) {
        super.handleDataEvent(ref, store, data);
        System.out.println("Input: " + data.value);
        sendUpdate();  // REQUIRED - client shows "Loading..." without this
    }

    public static class Data {
        public static final BuilderCodec<Data> CODEC = BuilderCodec.builder(Data.class, Data::new)
            .append(new KeyedCodec<>("@MyInput", Codec.STRING),
                (d, v) -> d.value = v, d -> d.value)
            .add()
            .build();
        private String value;
    }
}

// Open page
player.getPageManager().openCustomPage(ref, store, new MyPage(playerRef));
// Close page
player.getPageManager().setPage(ref, store, Page.None);
```

### Dynamic UI Updates
```java
public void updateText(String newText) {
    UICommandBuilder ui = new UICommandBuilder();
    ui.set("#MyLabel.TextSpans", Message.raw(newText));
    update(false, ui);  // false = don't clear existing UI
}
```

## Item Interactions

### SimpleInstantInteraction
```java
public class MyInteraction extends SimpleInstantInteraction {
    public static final BuilderCodec<MyInteraction> CODEC = BuilderCodec.builder(
        MyInteraction.class, MyInteraction::new, SimpleInstantInteraction.CODEC
    ).build();

    @Override
    protected void firstRun(InteractionType type, InteractionContext ctx,
                           CooldownHandler cooldown) {
        CommandBuffer<EntityStore> buffer = ctx.getCommandBuffer();
        Player player = buffer.getComponent(ctx.getEntity(), Player.getComponentType());
        ItemStack item = ctx.getHeldItem();
        player.sendMessage(Message.raw("Used: " + item.getItemId()));
    }
}

// Register in setup()
getCodecRegistry(Interaction.CODEC).register("my_interaction", MyInteraction.class, MyInteraction.CODEC);
```

### Item JSON with Interaction
```json
{
  "Id": "My_Item",
  "Interactions": {
    "Secondary": {
      "Interactions": [
        { "Type": "my_interaction" }
      ]
    }
  }
}
```

### Advanced Interaction Types

**Condition**: Check before proceeding
```json
{
  "Type": "Condition",
  "Crouching": true,
  "Failed": "Block_Secondary",
  "Next": { ... }
}
```

**Charging**: Hold to charge
```json
{
  "Type": "Charging",
  "FailsOnDamage": true,
  "HorizontalSpeedMultiplier": 0.4,
  "Next": {
    "2.5": { "Type": "my_interaction" }
  },
  "Failed": { "Type": "Simple" }
}
```

**Serial**: Execute in sequence
```json
{
  "Type": "Serial",
  "Interactions": [ {...}, {...} ]
}
```

## Item Recipes

```json
{
  "Id": "My_Item",
  "Recipe": {
    "TimeSeconds": 3.5,
    "Input": [
      { "ItemId": "Ingredient_1", "Quantity": 15 },
      { "ItemId": "Ingredient_2", "Quantity": 15 }
    ],
    "BenchRequirement": [
      {
        "Id": "Workbench",
        "Type": "Crafting",
        "Categories": ["Workbench_Survival"]
      }
    ]
  }
}
```

## World Thread Safety

**Critical**: Many operations must run on world thread via `world.execute()`:
- Modifying Store/Refs
- Spawning entities
- Playing sounds at positions
- Teleporting players
- Pasting/removing prefabs via `PrefabUtil`

```java
world.execute(() -> {
    // Thread-safe operations here
});
```

### Prefab Operations

**ALWAYS** paste prefabs on the world thread:

```java
// From a scheduled task or event handler
public void spawnSegment(World world, Vector3i position, IPrefabBuffer buffer) {
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
```

### Buffer Lifecycle

`IPrefabBuffer` instances MUST be released to prevent memory leaks:

```java
// Load once, reuse many times
IPrefabBuffer buffer = PrefabBufferUtil.getCached(path);

// Use for multiple paste operations...

// Release when completely done (e.g., plugin shutdown)
buffer.release();
```

## Player Data Persistence

### Recommended: Component with CODEC

For per-player data that persists across sessions, register a component with a CODEC:

```java
// 1. Define component with CODEC
public class MyPlayerData implements Component<EntityStore> {
    public static final BuilderCodec<MyPlayerData> CODEC = BuilderCodec
        .builder(MyPlayerData.class, MyPlayerData::new)
        .append(new KeyedCodec<>("Score", Codec.INTEGER),
            (d, v) -> d.score = v, d -> d.score)
        .add()
        .build();

    private int score = 0;

    @Override
    public Component<EntityStore> clone() {
        MyPlayerData c = new MyPlayerData();
        c.score = this.score;
        return c;
    }
}

// 2. Register in setup() with name + CODEC
this.dataType = getEntityStoreRegistry().registerComponent(
    MyPlayerData.class,
    "MyPluginData",  // Key in player JSON
    MyPlayerData.CODEC
);

// 3. Use - auto-saved on disconnect
MyPlayerData data = store.ensureAndGetComponent(playerRef, dataType);
data.setScore(100);
```

Data is stored in `server/Server/universe/players/{uuid}.json` under `Components.MyPluginData`.

### Alternative: Plugin Data Directory

For global data (leaderboards), use `getDataDirectory()`:

```java
Path dataDir = getDataDirectory();  // mods/PluginName/
Path leaderboard = dataDir.resolve("leaderboard.json");
BsonUtil.writeDocument(leaderboard, myCodec.encode(data, new ExtraInfo()));
```

## Instance Worlds & Spawn Configuration

### Spawn Provider Gotcha

**Critical**: The `SpawnProvider` in `instance.bson` is often ignored by `VoidWorldGenProvider`, which defaults spawn to Y=1. You MUST set the spawn provider explicitly on the `WorldConfig` after instance creation:

```java
InstancesPlugin.get().spawnInstance("MyInstance", originWorld, returnTransform)
    .thenCompose(instanceWorld -> {
        // REQUIRED: Set spawn provider explicitly
        instanceWorld.getWorldConfig().setSpawnProvider(
            new GlobalSpawnProvider(new Transform(
                new Vector3d(0.0, 3.0, 0.0),  // Spawn position
                new Vector3f(0.0f, 180.0f, 0.0f)  // Facing direction
            ))
        );

        // Configure auto-cleanup
        InstanceWorldConfig config = InstanceWorldConfig.ensureAndGet(
            instanceWorld.getWorldConfig()
        );
        config.setRemovalConditions(new RemovalCondition[]{
            WorldEmptyCondition.INSTANCE
        });

        // ... initialization code
    });
```

### Y Coordinate Constraints

**Critical**: Hytale valid Y range is **0-320**. Blocks placed at negative Y coordinates are **silently ignored** - no error, just no block.

```java
// BAD - blocks silently ignored
world.setBlock(x, -3, z, blockTypeKey);  // Does nothing!

// GOOD - valid Y range
world.setBlock(x, 0, z, blockTypeKey);   // Works
world.setBlock(x, 320, z, blockTypeKey); // Works
```

Always verify blocks are placed:
```java
int blockId = world.getBlock(x, y, z);
if (blockId == 0) {
    logger.atWarning().log("Block not placed at Y=%d (invalid range?)", y);
}
```

### Instance World Initialization Timing

Instance worlds may not tick until a player joins. The `world.execute()` method queues tasks that only process during the world's tick cycle.

**Pattern**: Use `CompletableFuture` + `thenCompose` for proper sequencing:

```java
InstancesPlugin.get().spawnInstance(INSTANCE_NAME, originWorld, returnTransform)
    .thenCompose(instanceWorld -> {
        // Set spawn provider FIRST
        instanceWorld.getWorldConfig().setSpawnProvider(
            new GlobalSpawnProvider(SPAWN_TRANSFORM)
        );

        // Create session
        GameSession session = new GameSession(playerUUID, instanceWorld);

        // Initialize on world thread (blocks placed here)
        CompletableFuture<GameSession> initFuture = new CompletableFuture<>();
        instanceWorld.execute(() -> {
            try {
                session.initializeSync();  // Place blocks synchronously
                initFuture.complete(session);
            } catch (Exception e) {
                initFuture.completeExceptionally(e);
            }
        });

        return initFuture;
    })
    .thenApply(session -> {
        // AFTER blocks are placed, teleport player
        teleportPlayerToInstance(playerRef, originWorld, session.getInstanceWorld());
        return session;
    });
```

### Direct Block Placement in Instance Worlds

For placing blocks directly (without prefabs), use `world.setBlock()` on the world thread:

```java
// Must be called from world.execute() callback
private void placeStartPlatform(World world) {
    String blockTypeKey = "Rock_Basalt";

    for (int x = -5; x <= 5; x++) {
        for (int z = -5; z <= 5; z++) {
            for (int y = 0; y < 3; y++) {  // Y=0,1,2 (valid range)
                world.setBlock(x, y, z, blockTypeKey);
            }
        }
    }
}
```

### GlobalSpawnProvider vs IndividualSpawnProvider

| Provider | Use Case |
|----------|----------|
| `GlobalSpawnProvider` | All players spawn at same position (minigames, arenas) |
| `IndividualSpawnProvider` | Per-player spawn points (survival, RPG) |
| `FitToHeightMapSpawnProvider` | Spawn on terrain surface |

```java
// Global - fixed spawn point
new GlobalSpawnProvider(new Transform(position, rotation));

// Individual - different per player
IndividualSpawnProvider provider = new IndividualSpawnProvider();
provider.setSpawnPoint(playerUUID, transform);
```

## Weather Configuration

### Setting Weather in Instance Worlds

Void instance worlds often have white/foggy visibility because no weather system is configured. Fix by adding `ForcedWeather` to `instance.bson`:

```json
{
  "Version": 4,
  "WorldGen": { "Type": "Void" },
  "GameplayConfig": "Default",
  "ForcedWeather": "Zone1_Sunny",
  ...
}
```

### Available Weather Types

Weather assets follow the naming pattern `Zone{N}_{Type}`. Known working types:

| Weather ID | Description |
|------------|-------------|
| `Zone1_Sunny` | Clear sky, minimal fog (default fallback) |
| `Zone1_Overcast` | Cloudy but good visibility |
| `Zone1_Rain` | Rain with particles |
| `Zone1_Storm` | Heavy rain, reduced visibility |

### Setting Weather Programmatically

Use `WeatherResource.setForcedWeather()` or the WorldConfig:

```java
// Via WorldConfig (persisted)
instanceWorld.getWorldConfig().setForcedWeather("Zone1_Sunny");
instanceWorld.getWorldConfig().markChanged();

// Via WeatherResource (runtime only)
WeatherResource weather = store.getResource(WeatherResource.getResourceType());
weather.setForcedWeather("Zone1_Sunny");
```

### Weather Asset Structure

Weather assets define fog, sky colors, and atmospheric effects via time-based keyframes:

```java
// Key weather properties (from decompiled Weather.java)
float[] fogDistance;          // [fogNear, fogFar] - default [-96, 1024]
TimeFloat[] fogDensities;     // Fog density over time
TimeColor[] fogColors;        // Fog color over time
TimeColorAlpha[] skyTopColors;
TimeColorAlpha[] skyBottomColors;
FogOptions fogOptions;        // Extra fog control
```

### FogOptions Properties

| Property | Type | Description |
|----------|------|-------------|
| `ignoreFogLimits` | boolean | Bypass fog distance limits |
| `effectiveViewDistanceMultiplier` | float | Multiply view distance |
| `fogFarViewDistance` | float | Override far fog distance |
| `fogHeightCameraOffset` | float | Vertical fog offset |
| `fogHeightCameraOverriden` | boolean | Use fixed camera height for fog |
| `fogHeightCameraFixed` | float | Fixed height value |

### Using /weather Command

Built-in weather commands (require WeatherPlugin):

```
/weather get                    # Show current weather
/weather set <weather_id>       # Force specific weather
/weather reset                  # Clear forced weather
```

### Common Weather Issues

**White/cloudy visibility in void world**: Missing `ForcedWeather` in instance.bson. Add `"ForcedWeather": "Zone1_Sunny"`.

**Weather not changing**: Weather is tied to biome/environment in normal worlds. Use `setForcedWeather()` to override.

**Fog too dense**: Check `FogDistance` values. First value (fogNear) should be negative (e.g., -96), second (fogFar) should be large (e.g., 1024).

## Useful Resources

- Visual UI Editor: https://hytale.ellie.au/
- IntelliJ UI Plugin: https://plugins.jetbrains.com/plugin/29783-hytale-ui-support
- Create UI from Java: https://www.curseforge.com/hytale/mods/hyui
- Multiple HUDs: https://www.curseforge.com/hytale/mods/multiplehud
