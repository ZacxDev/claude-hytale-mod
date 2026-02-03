# API Reference

Core APIs for plugin development.

## com.hypixel.hytale.server.core.plugin

### AssetRegistry

**Type**: class

### IRegistry

**Type**: interface

### JavaPlugin

**Type**: class

**Extends**: `PluginBase`

### JavaPluginInit

**Type**: class

**Extends**: `PluginInit`

### MapKeyMapRegistry

**Type**: class

**Implements**: `IRegistry`

### MissingPluginDependencyException

**Type**: class

**Extends**: `RuntimeException`

### PendingLoadJavaPlugin

**Type**: class

**Extends**: `PendingLoadPlugin`

### PendingLoadPlugin

**Type**: class

### PluginBase

**Type**: class

**Implements**: `CommandOwner`

### PluginClassLoader

**Type**: class

**Extends**: `URLClassLoader`

### PluginCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PluginInit

**Type**: class

### PluginListPage

**Type**: class

**Extends**: `InteractiveCustomUIPage<PluginListPage.PluginListPageEventData>`

### PluginListPageManager

**Type**: class

### PluginManager

**Type**: class

### PluginSetupEvent

**Type**: class

**Extends**: `PluginEvent`

### PluginState

**Type**: enum

### PluginType

**Type**: enum

## com.hypixel.hytale.server.core.command

### AbbreviationMap

**Type**: class

### AbstractAsyncCommand

**Type**: class

**Extends**: `AbstractCommand`

### AbstractAsyncPlayerCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractAsyncWorldCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractCommand

**Type**: class

### AbstractCommandCollection

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractPlayerCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractTargetEntityCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractTargetPlayerCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AbstractWorldCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### ArgumentType

**Type**: class

**Implements**: `SuggestionProvider`

### AssetTagsCommand

**Type**: class

**Extends**: `CommandBase`

### AssetsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### AssetsDuplicatesCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### AuthCancelCommand

**Type**: class

**Extends**: `CommandBase`

### AuthCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### AuthLoginBrowserCommand

**Type**: class

**Extends**: `CommandBase`

### AuthLoginCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### AuthLoginDeviceCommand

**Type**: class

**Extends**: `CommandBase`

### AuthLogoutCommand

**Type**: class

**Extends**: `CommandBase`

### AuthPersistenceCommand

**Type**: class

**Extends**: `CommandBase`

### AuthSelectCommand

**Type**: class

**Extends**: `CommandBase`

### AuthStatusCommand

**Type**: class

**Extends**: `CommandBase`

### BackupCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### BooleanFlagArgumentType

**Type**: class

**Extends**: `ArgumentType<Boolean>`

### Bot

**Type**: class

**Extends**: `SimpleChannelInboundHandler<Packet>`

### BotConfig

**Type**: class

### CameraDemo

**Type**: class

### ChunkCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### ChunkFixHeightMapCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkForceTickCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkInfoCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkLightingCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkLoadCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkLoadedCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### ChunkMarkSaveCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkMaxSendRateCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### ChunkRegenerateCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ChunkResendCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### ChunkTintCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### ChunkTrackerCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### ChunkUnloadCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### CommandBase

**Type**: class

**Extends**: `AbstractCommand`

### CommandContext

**Type**: class

### CommandException

**Type**: class

**Extends**: `RuntimeException`

### CommandListPage

**Type**: class

**Extends**: `InteractiveCustomUIPage<CommandListPage.CommandListPageEventData>`

### CommandManager

**Type**: class

**Implements**: `CommandOwner`

### CommandOwner

**Type**: interface

### CommandRegistration

**Type**: class

**Extends**: `Registration`

### CommandRegistry

**Type**: class

**Extends**: `Registry<CommandRegistration>`

### CommandSender

**Type**: interface

**Extends**: `IMessageReceiver, PermissionHolder`

### CommandUtil

**Type**: class

### CommandValidationResults

**Type**: class

**Extends**: `ValidationResults`

### CommandsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### ConvertPrefabsCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### Coord

**Type**: class

### DamageCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### DebugPlayerPositionCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### DefaultArg

**Type**: class

**Extends**: `AbstractOptionalArg<DefaultArg<DataType>, DataType>`

**Implements**: `AbstractOptionalArg.DefaultValueArgument<DataType>`

### DefaultValueArgument

**Type**: interface

### DumpCommandsCommand

**Type**: class

**Extends**: `CommandBase`

### EntityCleanCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityCloneCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### EntityCountCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityDumpCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityEffectCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityHideFromAdventurePlayersCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityIntangibleCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityInvulnerableCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityLodCommand

**Type**: class

**Extends**: `CommandBase`

### EntityMakeInteractableCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityNameplateCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityRemoveCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityResendCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntitySnapshotHistoryCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntitySnapshotLengthCommand

**Type**: class

**Extends**: `CommandBase`

### EntitySnapshotSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### EntityStatsAddCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsDumpCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsGetCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsResetCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsSetCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsSetToMaxCommand

**Type**: class

**Extends**: `AbstractTargetEntityCommand`

### EntityStatsSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### EntityTrackerCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### EntityWrappedArg

**Type**: class

**Extends**: `WrappedArg<UUID>`

### EventTitleCommand

**Type**: class

**Extends**: `CommandBase`

### FlagArg

**Type**: class

**Extends**: `AbstractOptionalArg<FlagArg, Boolean>`

**Implements**: `AbstractOptionalArg.DefaultValueArgument<Boolean>`

### GameModeArgumentType

**Type**: class

**Extends**: `SingleArgumentType<GameMode>`

### GameModeCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### GeneralCommandException

**Type**: class

**Extends**: `CommandException`

### GitCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### GiveArmorCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### GiveCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### HelpCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### HideCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### HitDetectionCommand

**Type**: class

**Extends**: `CommandBase`

### HitboxCollisionAddCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### HitboxCollisionCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### HitboxCollisionRemoveCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### HudManagerTestCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### IntCoord

**Type**: class

### InventoryBackpackCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### InventoryClearCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### InventoryCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### InventoryItemCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### InventorySeeCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### ItemStateCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### KickCommand

**Type**: class

**Extends**: `CommandBase`

### KillCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### LightingCalculationCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### LightingCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### LightingGetCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### LightingInfoCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### LightingInvalidateCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### LightingSendCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### LightingSendToggleCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ListArgumentType

**Type**: class

**Extends**: `ArgumentType<List<DataType>>`

### LogCommand

**Type**: class

**Extends**: `CommandBase`

### MatchResult

**Type**: class

**Implements**: `Comparable<MatchResult>`

### MaxPlayersCommand

**Type**: class

**Extends**: `CommandBase`

### MessageTranslationTestCommand

**Type**: class

**Extends**: `CommandBase`

### MultiArgumentContext

**Type**: class

### MultiArgumentType

**Type**: class

**Extends**: `ArgumentType<DataType>`

### NetworkCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### NoPermissionException

**Type**: class

**Extends**: `CommandException`

### NotifyCommand

**Type**: class

**Extends**: `CommandBase`

### OptionalArg

**Type**: class

**Extends**: `AbstractOptionalArg<OptionalArg<DataType>, DataType>`

### PIDCheckCommand

**Type**: class

**Extends**: `CommandBase`

### PacketStatsCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PacksCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PacksListCommand

**Type**: class

**Extends**: `CommandBase`

### ParseResult

**Type**: class

### ParserContext

**Type**: class

### PingCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCameraDemoActivateCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCameraDemoDeactivateCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCameraDemoSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerCameraResetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCameraSideScrollerCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCameraSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerCameraTopdownCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerEffectApplyCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### PlayerEffectClearCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### PlayerEffectSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerResetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerRespawnCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### PlayerStatsAddCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsDumpCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsGetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsResetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsSetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsSetToMaxCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerStatsSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerViewRadiusGetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerViewRadiusSetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### PlayerViewRadiusSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### PlayerZoneCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### ProcessedArgumentType

**Type**: class

**Extends**: `ArgumentType<OutputType>`

### ReferCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### RelativeChunkPosition

**Type**: class

### RelativeDirection

**Type**: enum

### RelativeDoublePosition

**Type**: class

### RelativeFloat

**Type**: class

### RelativeIntPosition

**Type**: class

### RelativeInteger

**Type**: class

### RelativeIntegerRange

**Type**: class

### RelativeVector3i

**Type**: class

### RepulsionAddCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### RepulsionCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### RepulsionRemoveCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### RequiredArg

**Type**: class

**Extends**: `Argument<RequiredArg<DataType>, DataType>`

### SenderTypeException

**Type**: class

**Extends**: `CommandException`

### ServerCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### ServerDumpCommand

**Type**: class

**Extends**: `CommandBase`

### ServerGCCommand

**Type**: class

**Extends**: `CommandBase`

### ServerStatsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### ServerStatsCpuCommand

**Type**: class

**Extends**: `CommandBase`

### ServerStatsGcCommand

**Type**: class

**Extends**: `CommandBase`

### ServerStatsMemoryCommand

**Type**: class

**Extends**: `CommandBase`

### ShowBuilderToolsHudCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### SingleArgumentType

**Type**: class

**Extends**: `ArgumentType<DataType>`

### SleepCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### SleepOffsetCommand

**Type**: class

**Extends**: `CommandBase`

### SleepTestCommand

**Type**: class

**Extends**: `CommandBase`

### SoundCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### SoundPlay2DCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### SoundPlay3DCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### SpawnBlockCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### StashCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### StopCommand

**Type**: class

**Extends**: `CommandBase`

### StopNetworkChunkSendingCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### StressTestCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### StressTestStartCommand

**Type**: class

**Extends**: `AbstractAsyncWorldCommand`

### StressTestStopCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### SudoCommand

**Type**: class

**Extends**: `CommandBase`

### SuggestionProvider

**Type**: interface

**Annotations**: @FunctionalInterface

### SuggestionResult

**Type**: class

### ToggleBlockPlacementOverrideCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### Tokenizer

**Type**: class

### UpdateAssetsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### UpdatePrefabsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### ValidateCPBCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### VersionCommand

**Type**: class

**Extends**: `CommandBase`

### WhereAmICommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### WhoAmICommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### WhoCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### WorldGenBenchmarkCommand

**Type**: class

**Extends**: `CommandBase`

### WorldGenCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WorldGenReloadCommand

**Type**: class

**Extends**: `AbstractAsyncWorldCommand`

### WorldMapClearMarkersCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### WorldMapCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WorldMapDiscoverCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### WorldMapReloadCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldMapUndiscoverCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### WorldMapViewRadiusGetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### WorldMapViewRadiusRemoveCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### WorldMapViewRadiusSetCommand

**Type**: class

**Extends**: `AbstractTargetPlayerCommand`

### WorldMapViewRadiusSubCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WrappedArg

**Type**: class

### WrappedArgumentType

**Type**: class

**Extends**: `SingleArgumentType<DataType>`

## com.hypixel.hytale.server.core.event

### AddPlayerToWorldEvent

**Type**: class

**Implements**: `IEvent<String>`

### BootEvent

**Type**: class

**Implements**: `IEvent<Void>`

### BreakBlockEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### ChangeGameModeEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### CraftRecipeEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### DamageBlockEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### DiscoverZoneEvent

**Type**: class

**Extends**: `EcsEvent`

### DrainPlayerFromWorldEvent

**Type**: class

**Implements**: `IEvent<String>`

### DropItemEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### EntityEvent

**Type**: class

**Implements**: `IEvent<KeyType>`

### EntityRemoveEvent

**Type**: class

**Extends**: `EntityEvent<Entity, String>`

### GroupPermissionChangeEvent

**Type**: class

**Implements**: `IEvent<Void>`

### InteractivelyPickupItemEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### LivingEntityInventoryChangeEvent

**Type**: class

**Extends**: `EntityEvent<LivingEntity, String>`

### LivingEntityUseBlockEvent

**Type**: class

**Implements**: `IEvent<String>`

**Annotations**: @Deprecated

### PlaceBlockEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### PlayerChatEvent

**Type**: class

**Implements**: `IAsyncEvent<String>`, `ICancellable`

### PlayerConnectEvent

**Type**: class

**Implements**: `IEvent<Void>`

### PlayerCraftEvent

**Type**: class

**Extends**: `PlayerEvent<String>`

**Annotations**: @Deprecated

### PlayerDisconnectEvent

**Type**: class

**Extends**: `PlayerRefEvent<Void>`

### PlayerEvent

**Type**: class

**Implements**: `IEvent<KeyType>`

### PlayerGroupEvent

**Type**: class

**Extends**: `PlayerPermissionChangeEvent`

### PlayerInteractEvent

**Type**: class

**Extends**: `PlayerEvent<String>`

**Implements**: `ICancellable`

**Annotations**: @Deprecated

### PlayerMouseButtonEvent

**Type**: class

**Extends**: `PlayerEvent<Void>`

**Implements**: `ICancellable`

### PlayerMouseMotionEvent

**Type**: class

**Extends**: `PlayerEvent<Void>`

**Implements**: `ICancellable`

### PlayerPermissionChangeEvent

**Type**: class

**Implements**: `IEvent<Void>`

### PlayerReadyEvent

**Type**: class

**Extends**: `PlayerEvent<String>`

### PlayerRefEvent

**Type**: class

**Implements**: `IEvent<KeyType>`

### PlayerSetupConnectEvent

**Type**: class

**Implements**: `IEvent<Void>`, `ICancellable`

### PlayerSetupDisconnectEvent

**Type**: class

**Implements**: `IEvent<Void>`

### PrepareUniverseEvent

**Type**: class

**Implements**: `IEvent<Void>`

**Annotations**: @Deprecated

### ShutdownEvent

**Type**: class

**Implements**: `IEvent<Void>`

### SwitchActiveSlotEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### UseBlockEvent

**Type**: class

**Extends**: `EcsEvent`

## com.hypixel.hytale.server.core.entity

### ActiveEntityEffect

**Type**: class

**Implements**: `Damage.Source`

### AnimationUtils

**Type**: class

### BasicCustomUIPage

**Type**: class

**Extends**: `CustomUIPage`

### BlockEntity

**Type**: class

**Implements**: `Component<EntityStore>`

### BlockWindow

**Type**: class

**Extends**: `Window`

**Implements**: `ValidatedWindow`

### CameraManager

**Type**: class

**Implements**: `Component<EntityStore>`

### ChainSyncStorage

**Type**: interface

### ChoiceBasePage

**Type**: class

**Extends**: `InteractiveCustomUIPage<ChoiceBasePage.ChoicePageEventData>`

### ChoiceElement

**Type**: class

### ChoiceInteraction

**Type**: class

### ChoiceRequirement

**Type**: class

### ContainerBlockWindow

**Type**: class

**Extends**: `BlockWindow`

**Implements**: `ItemContainerWindow`

### ContainerWindow

**Type**: class

**Extends**: `Window`

**Implements**: `ItemContainerWindow`

### CustomUIHud

**Type**: class

### CustomUIPage

**Type**: class

### DamageDataComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### DamageDataSetupSystem

**Type**: class

**Extends**: `HolderSystem<EntityStore>`

### EffectControllerComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### Entity

**Type**: class

**Implements**: `Component<EntityStore>`

### EntityGroup

**Type**: class

**Implements**: `Component<EntityStore>`

### EntitySnapshot

**Type**: class

### EntityUtils

**Type**: class

### ExplosionConfig

**Type**: class

### ExplosionUtils

**Type**: class

### Frozen

**Type**: class

**Implements**: `Component<EntityStore>`

### HiddenPlayersManager

**Type**: class

### HotbarManager

**Type**: class

### HudManager

**Type**: class

### InteractionChain

**Type**: class

**Implements**: `ChainSyncStorage`

### InteractionContext

**Type**: class

### InteractionEntry

**Type**: class

### InteractionManager

**Type**: class

**Implements**: `Component<EntityStore>`

### InteractiveCustomUIPage

**Type**: class

**Extends**: `CustomUIPage`

### InvalidatablePersistentRef

**Type**: class

**Extends**: `PersistentRef`

### ItemContainerWindow

**Type**: interface

### ItemRepairElement

**Type**: class

**Extends**: `ChoiceElement`

### ItemRepairPage

**Type**: class

**Extends**: `ChoiceBasePage`

### ItemStackContainerWindow

**Type**: class

**Extends**: `Window`

**Implements**: `ItemContainerWindow`

### ItemUtils

**Type**: class

### KnockbackComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### KnockbackSystems

**Type**: class

### LivingEntity

**Type**: class

**Extends**: `Entity`

### MaterialContainerWindow

**Type**: interface

### MaterialExtraResourcesSection

**Type**: class

### MovementConfig

**Type**: class

**Implements**: `JsonAssetWithMap<String`, `IndexedLookupTableAssetMap<String`, `MovementConfig>>`, `NetworkSerializable<MovementSettings>`

### MovementManager

**Type**: class

**Implements**: `Component<EntityStore>`

### MovementStatesComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### MovementStatesSystems

**Type**: class

### Nameplate

**Type**: class

**Implements**: `Component<EntityStore>`

### NameplateSystems

**Type**: class

### PageManager

**Type**: class

### PersistentRef

**Type**: class

### PersistentRefCount

**Type**: class

**Implements**: `Component<EntityStore>`

### PlaySoundPage

**Type**: class

**Extends**: `InteractiveCustomUIPage<PlaySoundPage.PlaySoundPageEventData>`

### Player

**Type**: class

**Extends**: `LivingEntity`

**Implements**: `CommandSender`, `PermissionHolder`, `MetricProvider`

### PlayerConfigData

**Type**: class

### PlayerDeathPositionData

**Type**: class

### PlayerRespawnPointData

**Type**: class

### PlayerWorldData

**Type**: class

### ProjectileComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### RepairItemInteraction

**Type**: class

**Extends**: `ChoiceInteraction`

### RespawnPage

**Type**: class

**Extends**: `InteractiveCustomUIPage<RespawnPage.RespawnPageEventData>`

### StatModifiersManager

**Type**: class

### UUIDComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### UniqueItemUsagesComponent

**Type**: class

**Implements**: `Component<EntityStore>`

### ValidatedWindow

**Type**: interface

### Window

**Type**: class

### WindowManager

**Type**: class

## com.hypixel.hytale.server.core.universe

### AbstractByteSectionPalette

**Type**: class

**Implements**: `ISectionPalette`

### AbstractCachedAccessor

**Type**: class

### AbstractShortSectionPalette

**Type**: class

**Implements**: `ISectionPalette`

### AddWorldEvent

**Type**: class

**Extends**: `WorldEvent`

**Implements**: `ICancellable`

### AllWorldsLoadedEvent

**Type**: class

**Implements**: `IEvent<Void>`

### BitFieldArr

**Type**: class

### BlockAccessor

**Type**: interface

### BlockBulkCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### BlockBulkFindCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### BlockBulkFindHereCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockBulkReplaceCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockChunk

**Type**: class

**Implements**: `Component<ChunkStore>`

### BlockCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### BlockComponentChunk

**Type**: class

**Implements**: `Component<ChunkStore>`

### BlockGetCommand

**Type**: class

**Extends**: `SimpleBlockCommand`

### BlockGetStateCommand

**Type**: class

**Extends**: `SimpleBlockCommand`

### BlockInspectFillerCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockInspectPhysicsCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockInspectRotationCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockMapMarker

**Type**: class

**Implements**: `Component<ChunkStore>`

### BlockMapMarkersResource

**Type**: class

**Implements**: `Resource<ChunkStore>`

### BlockPositionData

**Type**: class

**Implements**: `IBlockPositionData`

### BlockPositionProvider

**Type**: class

**Implements**: `Component<ChunkStore>`

### BlockRotationUtil

**Type**: class

### BlockRowCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockSection

**Type**: class

**Implements**: `Component<ChunkStore>`

### BlockSelectCommand

**Type**: class

**Extends**: `AbstractPlayerCommand`

### BlockSetCommand

**Type**: class

**Extends**: `SimpleBlockCommand`

### BlockSetStateCommand

**Type**: class

**Extends**: `SimpleBlockCommand`

### BlockSetTickingCommand

**Type**: class

**Extends**: `SimpleBlockCommand`

### BlockState

**Type**: class

**Implements**: `Component<ChunkStore>`

**Annotations**: @Deprecated

### BlockStateModule

**Type**: class

**Extends**: `JavaPlugin`

**Annotations**: @Deprecated

### BlockStateRegistration

**Type**: class

**Extends**: `Registration`

### BlockStateRegistry

**Type**: class

**Extends**: `Registry<BlockStateRegistration>`

### BreakValidatedBlockState

**Type**: interface

### BufferChunkLoader

**Type**: class

**Implements**: `IChunkLoader`

### BufferChunkSaver

**Type**: class

**Implements**: `IChunkSaver`

### ByteSectionPalette

**Type**: class

**Extends**: `AbstractByteSectionPalette`

### CalculationResult

**Type**: enum

### ChunkAccessor

**Type**: interface

**Extends**: `IChunkAccessorSync<WorldChunk>`

**Annotations**: @Deprecated

### ChunkColumn

**Type**: class

**Implements**: `Component<ChunkStore>`

**Annotations**: @Deprecated

### ChunkEvent

**Type**: class

**Implements**: `IEvent<String>`

### ChunkFlag

**Type**: enum

**Implements**: `Flag`

### ChunkLightData

**Type**: class

### ChunkLightDataBuilder

**Type**: class

**Extends**: `ChunkLightData`

### ChunkLightingManager

**Type**: class

**Implements**: `Runnable`

### ChunkPreLoadProcessEvent

**Type**: class

**Extends**: `ChunkEvent`

**Implements**: `IProcessedEvent`

### ChunkSaveEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### ChunkSavingSystems

**Type**: class

### ChunkSection

**Type**: class

**Implements**: `Component<ChunkStore>`

### ChunkSectionReference

**Type**: class

### ChunkStore

**Type**: class

**Implements**: `WorldProvider`

### ChunkSystems

**Type**: class

### ChunkUnloadEvent

**Type**: class

**Extends**: `CancellableEcsEvent`

### ChunkUnloadingSystem

**Type**: class

**Extends**: `TickingSystem<ChunkStore>`

**Implements**: `RunWhenPausedSystem<ChunkStore>`

### ChunkWorldMap

**Type**: class

**Implements**: `IWorldMap`

### ClientEffectWorldSettings

**Type**: class

### ConnectedBlockFaceTags

**Type**: class

### ConnectedBlockOutput

**Type**: class

### ConnectedBlockPatternRule

**Type**: class

### ConnectedBlockRuleSet

**Type**: class

### ConnectedBlockShape

**Type**: class

### ConnectedBlocksModule

**Type**: class

**Extends**: `JavaPlugin`

### ConnectedBlocksUtil

**Type**: class

### CustomConnectedBlockPattern

**Type**: class

**Extends**: `CustomTemplateConnectedBlockPattern`

### CustomConnectedBlockTemplateAsset

**Type**: class

**Implements**: `JsonAssetWithMap<String`, `DefaultAssetMap<String`, `CustomConnectedBlockTemplateAsset>>`

### CustomTemplateConnectedBlockPattern

**Type**: class

### CustomTemplateConnectedBlockRuleSet

**Type**: class

**Extends**: `ConnectedBlockRuleSet`

### DataStore

**Type**: interface

### DataStoreProvider

**Type**: interface

### DeathMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### DefaultChunkStorageProvider

**Type**: class

**Implements**: `IChunkStorageProvider`

### DefaultPlayerStorageProvider

**Type**: class

**Implements**: `PlayerStorageProvider`

### DefaultResourceStorageProvider

**Type**: class

**Implements**: `IResourceStorageProvider`

### DestroyableBlockState

**Type**: interface

**Annotations**: @Deprecated

### DisabledWorldMapProvider

**Type**: class

**Implements**: `IWorldMapProvider`

### DiskDataStore

**Type**: class

**Implements**: `DataStore<T>`

### DiskDataStoreProvider

**Type**: class

**Implements**: `DataStoreProvider`

### DiskPlayerStorageProvider

**Type**: class

**Implements**: `PlayerStorageProvider`

### DiskResourceStorageProvider

**Type**: class

**Implements**: `IResourceStorageProvider`

### DummyWorldGenProvider

**Type**: class

**Implements**: `IWorldGenProvider`

### EmptyBlockAccessor

**Type**: class

**Implements**: `BlockAccessor`

### EmptyChunkStorageProvider

**Type**: class

**Implements**: `IChunkStorageProvider`

### EmptyResourceStorageProvider

**Type**: class

**Implements**: `IResourceStorageProvider`

### EmptySectionPalette

**Type**: class

**Implements**: `ISectionPalette`

### EntityChunk

**Type**: class

**Implements**: `Component<ChunkStore>`

### EntityStore

**Type**: class

**Implements**: `WorldProvider`

### EnvironmentChunk

**Type**: class

**Implements**: `Component<ChunkStore>`

### EnvironmentColumn

**Type**: class

### EnvironmentRange

**Type**: class

### FitToHeightMapSpawnProvider

**Type**: class

**Implements**: `ISpawnProvider`

### FlatWorldGenProvider

**Type**: class

**Implements**: `IWorldGenProvider`

### FloodLightCalculation

**Type**: class

**Implements**: `LightCalculation`

### FluidSection

**Type**: class

**Implements**: `Component<ChunkStore>`

### FullBrightLightCalculation

**Type**: class

**Implements**: `LightCalculation`

### GeneratedBlockChunk

**Type**: class

### GeneratedBlockStateChunk

**Type**: class

### GeneratedChunk

**Type**: class

### GeneratedChunkSection

**Type**: class

### GeneratedEntityChunk

**Type**: class

### GetChunkFlags

**Type**: class

### GlobalSpawnProvider

**Type**: class

**Implements**: `ISpawnProvider`

### HalfByteSectionPalette

**Type**: class

**Extends**: `AbstractByteSectionPalette`

### IBenchmarkableWorldGen

**Type**: interface

**Extends**: `IWorldGen`

### IBlockPositionData

**Type**: interface

### IChunkAccessorSync

**Type**: interface

**Annotations**: @Deprecated

### IChunkLoader

**Type**: interface

**Extends**: `Closeable`

### IChunkSaver

**Type**: interface

**Extends**: `Closeable`

### IChunkStorageProvider

**Type**: interface

### INonPlayerCharacter

**Type**: interface

### IPath

**Type**: interface

### IPathWaypoint

**Type**: interface

### IResourceStorageProvider

**Type**: interface

### ISectionPalette

**Type**: interface

### ISpawnProvider

**Type**: interface

### IWorldChunks

**Type**: interface

**Extends**: `IChunkAccessorSync<WorldChunk>, IWorldChunksAsync`

**Annotations**: @Deprecated

### IWorldChunksAsync

**Type**: interface

**Annotations**: @Deprecated

### IWorldGen

**Type**: interface

### IWorldGenBenchmark

**Type**: interface

### IWorldGenProvider

**Type**: interface

### IWorldMap

**Type**: interface

### IWorldMapProvider

**Type**: interface

### ImageBuilder

**Type**: class

### IndexedStorageChunkStorageProvider

**Type**: class

**Implements**: `IChunkStorageProvider`

### IndividualSpawnProvider

**Type**: class

**Implements**: `ISpawnProvider`

### IntBytePalette

**Type**: class

### ItemContainerBlockState

**Type**: interface

### ItemContainerState

**Type**: class

**Extends**: `BlockState`

**Implements**: `ItemContainerBlockState`, `DestroyableBlockState`, `MarkerBlockState`

### LaunchPad

**Type**: class

**Implements**: `Component<ChunkStore>`

### LightCalculation

**Type**: interface

### LocalCachedChunkAccessor

**Type**: class

**Implements**: `OverridableChunkAccessor<WorldChunk>`

### MapMarkerTracker

**Type**: class

### MarkerBlockState

**Type**: interface

### MigrationChunkStorageProvider

**Type**: class

**Implements**: `IChunkStorageProvider`

### MoonPhaseChangeEvent

**Type**: class

**Extends**: `EcsEvent`

### NoSuchBlockStateException

**Type**: class

**Extends**: `Exception`

### OverridableChunkAccessor

**Type**: interface

**Extends**: `ChunkAccessor<X>`

### POIMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### PaletteTypeEnum

**Type**: enum

### ParticleUtil

**Type**: class

### PatternRotationDefinition

**Type**: class

### PerWorldDataMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### PlaceBlockSettings

**Type**: class

### PlacedByBlockState

**Type**: interface

### PlayerIconMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### PlayerRef

**Type**: class

**Implements**: `Component<EntityStore>`, `MetricProvider`, `IMessageReceiver`

### PlayerRefAddedSystem

**Type**: class

**Extends**: `RefSystem<EntityStore>`

### PlayerStorage

**Type**: interface

### PlayerStorageProvider

**Type**: interface

### PlayerUtil

**Type**: class

### PlayerVelocityInstructionSystem

**Type**: class

**Extends**: `EntityTickingSystem<EntityStore>`

### RemoveWorldEvent

**Type**: class

**Extends**: `WorldEvent`

**Implements**: `ICancellable`

### RespawnBlock

**Type**: class

**Implements**: `Component<ChunkStore>`

### RespawnMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### RoofConnectedBlockRuleSet

**Type**: class

**Extends**: `ConnectedBlockRuleSet`

**Implements**: `StairLikeConnectedBlockRuleSet`

### Rotation3D

**Type**: class

**Annotations**: @Deprecated

### SendableBlockState

**Type**: interface

**Annotations**: @Deprecated

### SetBlockSettings

**Type**: class

### SetTickingCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### ShortBytePalette

**Type**: class

### ShortSectionPalette

**Type**: class

**Extends**: `AbstractShortSectionPalette`

### SimpleBlockCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### SimplePathWaypoint

**Type**: class

**Implements**: `IPathWaypoint`

### SoundUtil

**Type**: class

### SpawnMarkerProvider

**Type**: class

**Implements**: `WorldMapManager.MarkerProvider`

### SpawnUtil

**Type**: class

### StairConnectedBlockRuleSet

**Type**: class

**Extends**: `ConnectedBlockRuleSet`

**Implements**: `StairLikeConnectedBlockRuleSet`

### StairLikeConnectedBlockRuleSet

**Type**: interface

### StartWorldEvent

**Type**: class

**Extends**: `WorldEvent`

### TickableBlockState

**Type**: interface

### Universe

**Type**: class

**Extends**: `JavaPlugin`

**Implements**: `IMessageReceiver`, `MetricProvider`

### ValidatableWorldGen

**Type**: interface

### ValidationOption

**Type**: enum

### VoidWorldGenProvider

**Type**: class

**Implements**: `IWorldGenProvider`

### World

**Type**: class

**Extends**: `TickingThread`

**Implements**: `Executor`, `ExecutorMetricsRegistry.ExecutorMetric`, `ChunkAccessor<WorldChunk>`, `IWorldChunks`, `IMessageReceiver`

### WorldAddCommand

**Type**: class

**Extends**: `CommandBase`

### WorldChunk

**Type**: class

**Implements**: `BlockAccessor`, `Component<ChunkStore>`

### WorldCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WorldConfig

**Type**: class

### WorldConfigCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WorldConfigPauseTimeCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldConfigProvider

**Type**: interface

### WorldConfigSaveSystem

**Type**: class

**Extends**: `DelayedSystem<EntityStore>`

### WorldConfigSeedCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldConfigSetPvpCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldConfigSetSpawnCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldConfigSetSpawnDefaultCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldEvent

**Type**: class

**Implements**: `IEvent<String>`

### WorldGenLoadException

**Type**: class

**Extends**: `Exception`

### WorldGenTimingsCollector

**Type**: class

### WorldGenWorldMapProvider

**Type**: class

**Implements**: `IWorldMapProvider`

### WorldListCommand

**Type**: class

**Extends**: `CommandBase`

### WorldLoadCancelledException

**Type**: class

**Extends**: `RuntimeException`

### WorldLoadCommand

**Type**: class

**Extends**: `CommandBase`

### WorldLocationCondition

**Type**: class

### WorldMap

**Type**: class

**Implements**: `NetworkSerializable<UpdateWorldMap>`

### WorldMapLoadException

**Type**: class

**Extends**: `Exception`

### WorldMapManager

**Type**: class

**Extends**: `TickingThread`

### WorldMapSettings

**Type**: class

### WorldMapTracker

**Type**: class

**Implements**: `Tickable`

### WorldNotificationHandler

**Type**: class

### WorldPath

**Type**: class

**Implements**: `IPath<SimplePathWaypoint>`

### WorldPathChangedEvent

**Type**: class

**Implements**: `IEvent<Void>`

### WorldPathConfig

**Type**: class

### WorldPauseCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldPerfCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldPerfGraphCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldPerfResetCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldPregenerateSystem

**Type**: class

**Extends**: `StoreSystem<ChunkStore>`

### WorldProvider

**Type**: interface

### WorldPruneCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### WorldRemoveCommand

**Type**: class

**Extends**: `CommandBase`

### WorldSaveCommand

**Type**: class

**Extends**: `AbstractAsyncCommand`

### WorldSetDefaultCommand

**Type**: class

**Extends**: `CommandBase`

### WorldSettingsCommand

**Type**: class

**Extends**: `AbstractCommandCollection`

### WorldTpsCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

### WorldTpsResetCommand

**Type**: class

**Extends**: `AbstractWorldCommand`

## com.hypixel.hytale.server.core.ui

### Anchor

**Type**: class

### Area

**Type**: class

### Builder

**Type**: class

### DropdownEntryInfo

**Type**: class

### FileBrowserEventData

**Type**: class

### FileListProvider

**Type**: interface

**Annotations**: @FunctionalInterface

### ItemGridSlot

**Type**: class

### LocalizableString

**Type**: class

### PatchStyle

**Type**: class

### ServerFileBrowser

**Type**: class

### UICommandBuilder

**Type**: class

### UIEventBuilder

**Type**: class

### Value

**Type**: class

### ValueCodec

**Type**: class

**Implements**: `Codec<Value<T>>`

## com.hypixel.hytale.server.core.prefab

Prefab loading and management APIs for loading, pasting, and manipulating prefabs at runtime.

### PrefabStore

**Type**: class (Singleton)

**Access**: `PrefabStore.get()`

**Key Methods**:
- `getAssetPrefabFromAnyPack(String key)` → `BlockSelection` - Load prefab from any registered asset pack (including plugins with `IncludesAssetPack: true`)
- `findAssetPrefabPath(String key)` → `Path` - Get filesystem path to prefab file
- `getPrefab(Path path)` → `BlockSelection` - Load prefab from specific path
- `getAssetPrefabsPath()` → `Path` - Get base assets prefab directory

**Usage**:
```java
// Load from plugin's bundled assets
BlockSelection selection = PrefabStore.get().getAssetPrefabFromAnyPack("HytaleRun/Segment.prefab.json");
```

### PrefabBufferUtil

**Type**: class

**Package**: `com.hypixel.hytale.server.core.prefab.selection.buffer`

**Key Methods**:
- `getCached(Path path)` → `IPrefabBuffer` - Load prefab buffer with caching (efficient for repeated use)

**Usage**:
```java
Path path = PrefabStore.get().findAssetPrefabPath("MyPrefab.prefab.json");
IPrefabBuffer buffer = PrefabBufferUtil.getCached(path);
// Use buffer...
buffer.release();  // CRITICAL: Always release when done
```

### IPrefabBuffer

**Type**: interface

**Package**: `com.hypixel.hytale.server.core.prefab.selection.buffer.impl`

**Key Methods**:
- `getMinX()`, `getMaxX()` → `int` - X-axis bounds
- `getMinY()`, `getMaxY()` → `int` - Y-axis bounds
- `getMinZ()`, `getMaxZ()` → `int` - Z-axis bounds
- `release()` - Free resources (MUST be called)

**Dimension Calculation**:
```java
int width = buffer.getMaxX() - buffer.getMinX() + 1;
int height = buffer.getMaxY() - buffer.getMinY() + 1;
int depth = buffer.getMaxZ() - buffer.getMinZ() + 1;
```

### BlockSelection

**Type**: class

**Package**: `com.hypixel.hytale.server.core.prefab.selection.standard`

High-level mutable prefab representation for building/editing. For runtime pasting, prefer `IPrefabBuffer`.

## com.hypixel.hytale.server.core.util

### PrefabUtil

**Type**: class

**Key Methods**:
- `paste(IPrefabBuffer, World, Vector3i, Rotation, boolean loadEntities, FastRandom, ComponentAccessor)` - Paste prefab at position
- `remove(IPrefabBuffer, World, Vector3i, boolean force, FastRandom, SetBlockSettings)` - Remove pasted prefab blocks

**Usage**:
```java
PrefabUtil.paste(
    buffer,
    world,
    new Vector3i(x, y, z),
    Rotation.None,
    true,  // loadEntities - spawn entities from prefab
    new FastRandom(),
    world.getEntityStore().getComponentAccessor()
);
```

**Thread Safety**: Must be called on world thread. Use `world.execute(() -> { ... })` from other threads.

## com.hypixel.hytale.math

### Box

**Type**: class

Axis-aligned bounding box for spatial queries.

**Constructor**: `Box(double minX, double minY, double minZ, double maxX, double maxY, double maxZ)`

**Key Methods**:
- `containsPosition(Vector3d origin, Vector3d point)` → `boolean` - Check if point is inside box relative to origin

**Usage**:
```java
Box region = new Box(0, 0, 0, 100, 64, 100);
Vector3d playerPos = transform.getPosition();
if (region.containsPosition(Vector3d.ZERO, playerPos)) {
    // Player is inside region
}
```

## com.hypixel.hytale.server.core.modules.spatial

### SpatialResource

**Type**: class

KD-tree spatial structure for efficient entity queries.

**Access**: `store.getResource(PlayerSpatialSystem.PLAYER_SPATIAL_RESOURCE)`

**Key Methods**:
- `getSpatialStructure()` → KD-tree structure
- `ordered(Vector3d position, double radius, List<Result> results)` - Query entities within radius

### PlayerSpatialSystem

**Type**: class

System providing efficient spatial queries for players.

**Resource**: `PLAYER_SPATIAL_RESOURCE` - SpatialResource for player queries

## com.hypixel.hytale.builtin.instances

Instance system for isolated game worlds (minigames, dungeons, etc.).

### InstancesPlugin

**Type**: class (Plugin)

**Access**: `InstancesPlugin.get()`

**Key Methods**:
- `spawnInstance(String name, World forWorld, Transform returnPoint)` → `CompletableFuture<World>` - Create new instance from template
- `teleportPlayerToInstance(PlayerRef, ComponentAccessor, World targetWorld, Transform returnOverride)` - Move player to instance
- `exitInstance(PlayerRef, ComponentAccessor)` - Return player to origin world

**Usage**:
```java
// Create instance
CompletableFuture<World> instance = InstancesPlugin.get()
    .spawnInstance("MyMinigame", originWorld, returnTransform);

// Cleanup: teleport all players out, instance auto-deletes if configured
InstancesPlugin.exitInstance(playerRef, accessor);
```

### InstanceWorldConfig

**Type**: class

Configuration for instance world behavior.

**Key Methods**:
- `ensureAndGet(WorldConfig)` → `InstanceWorldConfig` - Get or create instance config
- `setRemovalConditions(RemovalCondition[])` - Configure auto-removal triggers

### RemovalCondition

**Type**: interface

Conditions that trigger instance world removal.

**Implementations**:
- `WorldEmptyCondition.INSTANCE` - Remove when all players leave
- `TimeoutCondition` - Remove after fixed time
- `IdleTimeoutCondition` - Remove after idle period

**Usage**:
```java
InstanceWorldConfig config = InstanceWorldConfig.ensureAndGet(worldConfig);
config.setRemovalConditions(new RemovalCondition[]{
    WorldEmptyCondition.INSTANCE
});
worldConfig.setDeleteOnRemove(true);  // Delete files on removal
```

## com.hypixel.hytale.server.core.event.events.ecs

### PrefabPlaceEntityEvent

**Type**: class

Event fired when an entity is spawned from a prefab. Use to track or modify spawned entities.

**Key Methods**:
- `getEntityRef()` → `Ref<EntityStore>` - Reference to spawned entity
- `getHolder()` → `Holder<EntityStore>` - Entity holder (for modification)
- `setCancelled(boolean)` - Cancel entity spawn

**Usage**:
```java
entityStore.registerEventListener(PrefabPlaceEntityEvent.class, event -> {
    // Track spawned entity
    spawnedEntities.add(event.getEntityRef());

    // Or conditionally cancel
    if (shouldCancel) {
        event.setCancelled(true);
    }
});
```

## com.hypixel.hytale.server.core.modules.entity.component

### FromPrefab

**Type**: class (Marker Component)

Marker component added to entities spawned from prefabs. Automatically cleared after initialization by `ClearFromPrefabMarker` system.

**Note**: Cannot be used for long-term tracking of prefab-sourced entities - add your own tracking component instead.

