# Events Reference

Complete list of events available in Hytale server.

## IEvent (Synchronous)

Standard synchronous events.

### Player Events
| Event | Description |
|-------|-------------|
| `PlayerConnectEvent` | Player connects to server |
| `PlayerDisconnectEvent` | Player disconnects |
| `PlayerReadyEvent` | Player fully loaded and ready |
| `PlayerMouseButtonEvent` | Mouse button input |
| `PlayerMouseMotionEvent` | Mouse movement input |

### World Events
| Event | Description |
|-------|-------------|
| `AddWorldEvent` | World added |
| `RemoveWorldEvent` | World removed |
| `StartWorldEvent` | World started |
| `AddPlayerToWorldEvent` | Player added to world |
| `DrainPlayerFromWorldEvent` | Player removed from world |

### Asset Events
| Event | Description |
|-------|-------------|
| `AssetPackRegisterEvent` | Asset pack registered |
| `AssetPackUnregisterEvent` | Asset pack unregistered |
| `RegisterAssetStoreEvent` | Asset store registered |
| `RemoveAssetStoreEvent` | Asset store removed |
| `GenerateAssetsEvent` | Assets generation |
| `LoadedAssetsEvent` | Assets loaded |
| `RemovedAssetsEvent` | Assets removed |
| `LoadAssetEvent` | Single asset loaded |

### Entity Events
| Event | Description |
|-------|-------------|
| `EntityRemoveEvent` | Entity removed |
| `LivingEntityInventoryChangeEvent` | Inventory changed (keyed by world name) |
| `LoadedNPCEvent` | NPC loaded |
| `AllNPCsLoadedEvent` | All NPCs loaded |

#### LivingEntityInventoryChangeEvent Details

Keyed event (`IEvent<String>`) — key is the world name. Use `registerGlobal()` to listen across all worlds.

**Key Methods**:
- `getEntity()` → `LivingEntity` - The entity whose inventory changed
- `getTransaction()` → `Transaction` - The inventory transaction (cast to `ItemStackTransaction` for item operations)
- `getItemContainer()` → `ItemContainer` - The container that changed

**Transaction Analysis**:
```java
getEventRegistry().registerGlobal(LivingEntityInventoryChangeEvent.class, event -> {
    Transaction transaction = event.getTransaction();
    if (!(transaction instanceof ItemStackTransaction ist)) return;

    ActionType action = ist.getAction();
    if (action != null && action.isAdd()) {
        // Item was added to inventory
        ItemStack query = ist.getQuery();
        String itemId = query.getItemId();
    }

    // Get slot info
    var slotTxns = ist.getSlotTransactions();
    if (!slotTxns.isEmpty()) {
        short slot = slotTxns.get(0).getSlot();
    }
});
```

**Caution**: Modifying inventory inside this event causes recursion. Defer with `world.execute()`:
```java
player.getWorld().execute(() -> container.removeItemStackFromSlot(slot));
```

### Chunk Events
| Event | Description |
|-------|-------------|
| `ChunkPreLoadProcessEvent` | Before chunk loads |

### System Events
| Event | Description |
|-------|-------------|
| `BootEvent` | Server boot |
| `ShutdownEvent` | Server shutdown |
| `AllWorldsLoadedEvent` | All worlds loaded |
| `WindowCloseEvent` | Window closed |

### Plugin Events
| Event | Description |
|-------|-------------|
| `PluginSetupEvent` | Plugin setup phase |

### Misc Events
| Event | Description |
|-------|-------------|
| `ItemContainerChangeEvent` | Item container changed |
| `TreasureChestOpeningEvent` | Treasure chest opened |
| `MessagesUpdated` | Messages updated |
| `GenerateDefaultLanguageEvent` | Language generation |
| `GenerateSchemaEvent` | Schema generation |
| `GenerateServerStateEvent` | Server state generation |
| `WorldPathChangedEvent` | World path changed |
| `SingleplayerRequestAccessEvent` | Singleplayer access request |

### Editor Events (Asset Editor)
| Event | Description |
|-------|-------------|
| `AssetEditorActivateButtonEvent` | Editor button activated |
| `AssetEditorAssetCreatedEvent` | Asset created in editor |
| `AssetEditorClientDisconnectEvent` | Editor client disconnected |
| `AssetEditorSelectAssetEvent` | Asset selected in editor |
| `AssetEditorUpdateWeatherPreviewLockEvent` | Weather preview lock changed |

## IAsyncEvent (Asynchronous)

Events that run asynchronously - use `registerGlobal()` not `register()`.

| Event | Key Type | Description |
|-------|----------|-------------|
| `PlayerChatEvent` | `String` | Player chat message |
| `AssetEditorFetchAutoCompleteDataEvent` | — | Editor autocomplete |
| `AssetEditorRequestDataSetEvent` | — | Editor data request |
| `SendCommonAssetsEvent` | — | Common assets sent |

## EcsEvent (Entity Component System)

Events tied to ECS operations.

### CancellableEcsEvent

Can be cancelled to prevent the action.

| Event | Description |
|-------|-------------|
| `BreakBlockEvent` | Block broken |
| `PlaceBlockEvent` | Block placed |
| `DamageBlockEvent` | Block damaged |
| `ChangeGameModeEvent` | Game mode changed |
| `ChunkSaveEvent` | Chunk saved |
| `ChunkUnloadEvent` | Chunk unloaded |
| `CraftRecipeEvent.Pre` | Before crafting |
| `CraftRecipeEvent.Post` | After crafting |
| `Damage` | Entity damaged |
| `DropItemEvent.Drop` | Item dropped |
| `DropItemEvent.PlayerRequest` | Player requests drop |
| `InteractivelyPickupItemEvent` | Item picked up |
| `PrefabPasteEvent` | Prefab pasted |
| `SwitchActiveSlotEvent` | Active slot switched |

### Non-Cancellable EcsEvent
| Event | Description |
|-------|-------------|
| `UseBlockEvent.Pre` | Before block use |
| `UseBlockEvent.Post` | After block use |
| `DiscoverInstanceEvent.Display` | Instance discovered |
| `DiscoverZoneEvent.Display` | Zone discovered |
| `MoonPhaseChangeEvent` | Moon phase changed |

## Deprecated Events

| Event | Status |
|-------|--------|
| `LivingEntityUseBlockEvent` | DEPRECATED |
| `PlayerCraftEvent` | DEPRECATED |
| `PlayerInteractEvent` | DEPRECATED |
| `PrepareUniverseEvent` | DEPRECATED |

## Event Registration

### Unkeyed Events (IEvent with Void key)
```java
getEventRegistry().register(PlayerConnectEvent.class, event -> {
    PlayerRef player = event.getPlayerRef();
    player.sendMessage(Message.raw("Welcome!"));
});
```

### Keyed Async Events (IAsyncEvent)
```java
// MUST use registerGlobal for keyed events
getEventRegistry().registerGlobal(PlayerChatEvent.class, event -> {
    String content = event.getContent();
    PlayerRef sender = event.getSender();
});
```

### ECS Events
```java
getEventRegistry().register(BreakBlockEvent.class, event -> {
    if (shouldPrevent) {
        event.setCancelled(true);
    }
});
```

## Event Priority
Use `EventPriority` enum: `LOWEST`, `LOW`, `NORMAL`, `HIGH`, `HIGHEST`, `MONITOR`

```java
getEventRegistry().register(PlayerConnectEvent.class, EventPriority.HIGH, event -> {
    // Runs before NORMAL priority handlers
});
```
