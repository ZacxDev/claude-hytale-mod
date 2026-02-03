# Package Index (Plugin Development Focus)

Key packages for Hytale plugin development. For the full 894-package index, see decompiled source.

## Core Plugin API

### com.hypixel.hytale.server.core.plugin

- 📦 `JavaPlugin` — Base class for plugins
- 📦 `JavaPluginInit` — Constructor parameter
- 📦 `PluginBase` — Abstract base (implements CommandOwner)
- 📦 `PluginManager` — Plugin lifecycle management
- 🔢 `PluginState` — LOADING, SETUP, STARTED, SHUTDOWN
- 🔢 `PluginType` — Plugin type enum

### com.hypixel.hytale.server.core.command

- 📦 `AbstractCommand` — Base command class
- 📦 `AbstractAsyncCommand` — Async command base
- 📦 `AbstractPlayerCommand` — Player-only commands
- 📦 `AbstractCommandCollection` — Subcommand groups
- 📦 `CommandContext` — Execution context
- 📦 `CommandRegistry` — Command registration
- 🔷 `CommandSender` — Message receiver interface
- 📦 `RequiredArg` — Required argument
- 📦 `OptionalArg` — Optional argument
- 📦 `DefaultArg` — Argument with default value
- 📦 `FlagArg` — Boolean flag argument

### com.hypixel.hytale.server.core.command.system.arguments.types

- 📦 `ArgTypes` — Static argument type constants
  - `STRING`, `INTEGER`, `DOUBLE`, `BOOLEAN`
  - `PLAYER`, `WORLD`, `ITEM`, `BLOCK`
  - `VECTOR3`, `COORDINATE`

## Event System

### com.hypixel.hytale.server.core.event

- 📦 `EventRegistry` — Event registration
- 🔢 `EventPriority` — LOWEST, LOW, NORMAL, HIGH, HIGHEST, MONITOR
- 🔷 `IEvent<K>` — Base event interface
- 🔷 `IAsyncEvent<K>` — Async event interface
- 🔷 `ICancellable` — Cancellable event marker

### Key Events

| Event | Key Type | Description |
|-------|----------|-------------|
| `PlayerConnectEvent` | `Void` | Player joins server |
| `PlayerDisconnectEvent` | `Void` | Player leaves server |
| `PlayerReadyEvent` | `String` | Player fully loaded |
| `PlayerChatEvent` | `String` | Chat message (async) |
| `BreakBlockEvent` | — | Block broken |
| `PlaceBlockEvent` | — | Block placed |
| `UseBlockEvent` | — | Block interaction |
| `DropItemEvent` | — | Item dropped |

## Entity & Player

### com.hypixel.hytale.server.core.entity

- 📦 `Entity` — Base entity component
- 📦 `LivingEntity` — Extends Entity
- 📦 `Player` — Extends LivingEntity, implements CommandSender

### com.hypixel.hytale.server.core.universe

- 📦 `PlayerRef` — Player reference (main player API)
  - `sendMessage(Message)` — Send chat message
  - `getUsername()` — Get player name
  - `getUUID()` — Get player UUID
  - `openPage(CustomUIPage)` — Open UI page
  - `teleport(Vector3d)` — Teleport player

## UI System

### com.hypixel.hytale.server.core.entity.entities.player.pages

- 📦 `CustomUIPage` — Base UI page (4-param build)
- 📦 `BasicCustomUIPage` — Read-only pages (1-param build)
- 📦 `InteractiveCustomUIPage<T>` — Pages with input handling
- 📦 `PageManager` — Page lifecycle

### com.hypixel.hytale.server.core.ui

- 📦 `UICommandBuilder` — Build UI updates
  - `append(String)` — Append .ui file
  - `set(String, Object)` — Set element property
  - `add(String, Object)` — Add to collection
  - `remove(String)` — Remove element
- 📦 `UIEventBuilder` — Build event bindings
  - `addEventBinding(type, selector, data)` — Bind event

### com.hypixel.hytale.server.core.ui.builder

- 📦 `EventData` — Event data builder
  - `of(String key, String value)` — Static key-value
  - `of(String key, String selector)` — Reference to element

### com.hypixel.hytale.protocol.packets.interface_

- 🔢 `CustomUIEventBindingType`
  - `Activating` — Button click
  - `ValueChanged` — Input changed
  - `FocusGained` — Element focused
  - `FocusLost` — Element unfocused
- 🔢 `CustomPageLifetime`
  - `CantClose` — Player cannot dismiss
  - `CanDismiss` — Escape to close
  - `CanDismissOrCloseThroughInteraction`

## Codec System

### com.hypixel.hytale.codec

- 📦 `Codec<T>` — Serialization codec
  - `STRING`, `INTEGER`, `BOOLEAN`, `DOUBLE`, `FLOAT`, `LONG`
- 📦 `KeyedCodec<T>` — Named codec for fields

### com.hypixel.hytale.codec.builder

- 📦 `BuilderCodec<T>` — Builder pattern codec
  ```java
  BuilderCodec.builder(MyClass.class, MyClass::new)
      .addField(new KeyedCodec<>("Field", Codec.STRING),
          (d, v) -> d.field = v, d -> d.field)
      .build();
  ```

## Interaction System

### com.hypixel.hytale.server.core.interaction

- 📦 `Interaction` — Base interaction class
- 📦 `SimpleInstantInteraction` — One-shot actions
- 📦 `ChargingInteraction` — Hold to charge
- 📦 `ChainingInteraction` — Combo chains
- 📦 `InteractionContext` — Execution context
- 🔢 `InteractionType` — Primary, Secondary, Ability
- 📦 `CooldownHandler` — Cooldown management

## ECS Components

### com.hypixel.hytale.component

- 📦 `ComponentType<T>` — Component type definition
- 📦 `Store<S>` — Component storage
- 📦 `Ref<S>` — Entity reference
- 📦 `Holder<S>` — Entity holder

### Common Components

| Component | Package | Purpose |
|-----------|---------|---------|
| `TransformComponent` | component | Position, rotation |
| `HealthComponent` | component | Entity health |
| `InventoryComponent` | server.core.entity | Player inventory |
| `MovementManager` | server.core.entity | Movement control |

## World & Universe

### com.hypixel.hytale.server.core.universe

- 📦 `Universe` — Server universe (extends JavaPlugin)
- 📦 `World` — Game world
- 📦 `WorldChunk` — Chunk data
- 📦 `BlockAccessor` — Block access interface

## Messaging

### com.hypixel.hytale.server.core

- 📦 `Message` — Chat message builder
  - `raw(String)` — Plain text message
  - `translatable(String, Object...)` — Localized message

## Task Scheduling

### com.hypixel.hytale.server.core.task

- 📦 `TaskRegistry` — Task scheduling
  - `runLater(Runnable, long)` — Delayed task
  - `runRepeating(Runnable, long, long)` — Repeating task

## Logging (Flogger)

```java
// Correct - Flogger fluent API
getLogger().atInfo().log("Message: %s", value);
getLogger().atWarning().log("Warning");
getLogger().atSevere().log("Error");

// WRONG - These methods don't exist
getLogger().info("message");  // Compilation error
```

## Legend

- 📦 Class
- 🔷 Interface
- 🔢 Enum
