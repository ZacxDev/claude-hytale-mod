# Client-to-Server Packets Reference

Complete reference for packets the client can send to the server.

## Player Packets

| Packet | ID | Key Fields |
|--------|-----|------------|
| `SetClientId` | 100 | `clientId` |
| `SetGameMode` | 101 | `gameMode` |
| `SetMovementStates` | 102 | `movementStates` |
| `SetBlockPlacementOverride` | 103 | `enabled` |
| `JoinWorld` | 104 | `clearWorld`, `fadeInOut`, `worldUuid` |
| `ClientReady` | 105 | `readyForChunks`, `readyForGameplay` |
| `LoadHotbar` | 106 | `inventoryRow` |
| `SaveHotbar` | 107 | `inventoryRow` |
| `ClientMovement` | 108 | `movementStates`, `relativePosition`, `absolutePosition`, `bodyOrientation`, `lookOrientation`, `velocity`, `mountedTo` |
| `ClientTeleport` | 109 | `teleportId`, `modelTransform`, `resetVelocity` |
| `UpdateMovementSettings` | 110 | `movementSettings` |
| `MouseInteraction` | 111 | `clientTimestamp`, `activeSlot`, `itemInHandId`, `screenPoint`, `mouseButton`, `mouseMotion`, `worldInteraction` |
| `DamageInfo` | 112 | `damageSourcePosition`, `damageAmount`, `damageCause` |
| `ReticleEvent` | 113 | `eventIndex` |
| `DisplayDebug` | 114 | `shape`, `matrix`, `color`, `time`, `fade`, `frustumProjection` |
| `ClearDebugShapes` | 115 | — |
| `SyncPlayerPreferences` | 116 | `showEntityMarkers`, `armorItemsPreferredPickupLocation`, `allowNPCDetection`, `respondToHit` |
| `ClientPlaceBlock` | 117 | `position`, `rotation`, `placedBlockId` |
| `UpdateMemoriesFeatureStatus` | 118 | `isFeatureUnlocked` |
| `RemoveMapMarker` | 119 | `markerId` |

## Inventory Packets

| Packet | ID | Key Fields |
|--------|-----|------------|
| `UpdatePlayerInventory` | 170 | `storage`, `armor`, `hotbar`, `utility`, `builderMaterial`, `tools`, `backpack`, `sortType` |
| `SetCreativeItem` | 171 | `inventorySectionId`, `slotId`, `item`, `override` |
| `DropCreativeItem` | 172 | `item` |
| `SmartGiveCreativeItem` | 173 | `item`, `moveType` |
| `DropItemStack` | 174 | `inventorySectionId`, `slotId`, `quantity` |
| `MoveItemStack` | 175 | `fromSectionId`, `fromSlotId`, `quantity`, `toSectionId`, `toSlotId` |
| `SmartMoveItemStack` | 176 | `fromSectionId`, `fromSlotId`, `quantity`, `moveType` |
| `SetActiveSlot` | 177 | `inventorySectionId`, `activeSlot` |
| `SwitchHotbarBlockSet` | 178 | `itemId` |
| `InventoryAction` | 179 | `inventorySectionId`, `inventoryActionType`, `actionData` |

## Window Packets

| Packet | ID | Key Fields |
|--------|-----|------------|
| `OpenWindow` | 200 | `id`, `windowType`, `windowData`, `inventory`, `extraResources` |
| `UpdateWindow` | 201 | `id`, `windowData`, `inventory`, `extraResources` |
| `CloseWindow` | 202 | `id` |
| `SendWindowAction` | 203 | `id`, `action` |
| `ClientOpenWindow` | 204 | `type` |

## Chunk Packets

| Packet | ID | Key Fields |
|--------|-----|------------|
| `SetChunk` | 131 | `x`, `y`, `z`, `localLight`, `globalLight`, `data` |
| `SetChunkHeightmap` | 132 | `x`, `z`, `heightmap` |
| `SetChunkTintmap` | 133 | `x`, `z`, `tintmap` |
| `SetChunkEnvironments` | 134 | `x`, `z`, `environments` |
| `SetFluids` | 136 | `x`, `y`, `z`, `data` |

## Other Packets

| Packet | ID | Key Fields |
|--------|-----|------------|
| `ClientReferral` | 18 | `hostTo`, `data` |
| `SetUpdateRate` | 29 | `updatesPerSecond` |
| `SetTimeDilation` | 30 | `timeDilation` |
| `SetPaused` | 158 | `paused` |
| `SetEntitySeed` | 160 | `entitySeed` |
| `SetPage` | 216 | `page`, `canCloseThroughInteraction` |
| `SetServerAccess` | 252 | `access`, `password` |
| `SetMachinimaActorModel` | 261 | `model`, `sceneName`, `actorName` |
| `SetServerCamera` | 280 | `clientCameraView`, `isLocked`, `cameraSettings` |
| `SetFlyCameraMode` | 283 | `entering` |
| `SyncInteractionChains` | 290 | `updates` |

## Listening to Packets

```java
@Override
protected void setup() {
    // Register packet listener
    getPacketRegistry().registerListener(MouseInteraction.class, (packet, player) -> {
        getLogger().atInfo().log("Player %s clicked: button=%d",
            player.getUsername(), packet.getMouseButton());
    });
}
```

## Packet Source Location

Packets are found in decompiled source at:
```
com/hypixel/hytale/protocol/packets/
├── player/           # Player packets
├── world/            # World sync packets
├── entities/         # Entity packets
├── interaction/      # Interaction packets
└── interface_/       # UI packets
```

*Based on Hytale Server build 2026.01.13-dcad8778f*
