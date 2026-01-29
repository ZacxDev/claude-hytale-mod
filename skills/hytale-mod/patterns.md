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
