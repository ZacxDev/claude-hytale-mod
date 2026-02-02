# Class Hierarchy

## JavaPlugin Hierarchy

```
JavaPlugin
  └── CameraPlugin
  └── FarmingPlugin
  └── MemoriesPlugin
  └── NPCObjectivesPlugin
  └── NPCReputationPlugin
  └── NPCShopPlugin
  └── ObjectiveReputationPlugin
  └── ObjectivePlugin
  └── ObjectiveShopPlugin
  └── ReputationPlugin
  └── ShopPlugin
  └── ShopReputationPlugin
  └── StashPlugin
  └── TeleporterPlugin
  └── WorldLocationConditionPlugin
  └── AmbiencePlugin
  └── AssetEditorPlugin
  └── BedsPlugin
  └── BlockPhysicsPlugin
  └── BlockSpawnerPlugin
  └── BlockTickPlugin
  └── BuilderToolsPlugin
  └── MacroCommandPlugin
  └── CraftingPlugin
  └── CreativeHubPlugin
  └── CrouchSlidePlugin
  └── DeployablesPlugin
  └── FluidPlugin
  └── HytaleGenerator
  └── InstancesPlugin
  └── LANDiscoveryPlugin
  └── MantlingPlugin
  └── ModelPlugin
  └── MountPlugin
  └── NPCCombatActionEvaluatorPlugin
  └── NPCEditorPlugin
  └── ParkourPlugin
  └── PathPlugin
  └── PortalsPlugin
  └── SafetyRollPlugin
  └── SprintForcePlugin
  └── TagSetPlugin
  └── TeleportPlugin
  └── WeatherPlugin
  └── WorldGenPlugin
  └── AssetModule
  └── CommonAssetModule
  └── BlockTypeModule
  └── ConsoleModule
  └── CosmeticsModule
```

## PluginBase Hierarchy

```
PluginBase
  └── JavaPlugin
```

## AbstractCommand Hierarchy

```
AbstractCommand
  └── MemoriesCommand
  └── ObjectiveCommand
  └── ObjectiveCompleteCommand
  └── ObjectiveLocationMarkerCommand
  └── ObjectiveReachLocationMarkerCommand
  └── ObjectiveStartCommand
  └── ReputationCommand
  └── AmbienceCommands
  └── BlockSpawnerCommand
  └── ExtendFaceCommand
  └── PrefabCommand
  └── RotateCommand
  └── PrefabEditCommand
  └── BrushConfigCommand
  └── RecipeCommand
  └── MountCommand
  └── ParkourCommand
  └── PrefabPathCommand
  └── PrefabPathUpdateCommand
  └── WorldPathBuilderCommand
  └── WorldPathCommand
  └── FragmentCommands
  └── VoidEventCommands
  └── TeleportCommand
  └── WarpCommand
  └── WeatherCommand
  └── ParticleCommand
  └── AssetsCommand
  └── HitboxCollisionAddCommand
  └── HitboxCollisionCommand
  └── HitboxCollisionRemoveCommand
  └── RepulsionAddCommand
  └── RepulsionCommand
  └── RepulsionRemoveCommand
  └── PacksCommand
  └── ServerCommand
  └── ServerStatsCommand
  └── StressTestCommand
  └── HideCommand
  └── PlayerCommand
  └── PlayerCameraDemoSubCommand
  └── PlayerCameraSubCommand
  └── PlayerEffectSubCommand
  └── InventoryCommand
  └── PlayerStatsSubCommand
  └── PlayerViewRadiusSubCommand
  └── AuthCommand
  └── AuthLoginCommand
  └── GitCommand
  └── UpdateAssetsCommand
```

## AbstractPlayerCommand Hierarchy

```
AbstractPlayerCommand
  └── MemoriesCapacityCommand
  └── ObjectiveHistoryCommand
  └── ObjectivePanelCommand
  └── ClearBlocksCommand
  └── ClearEditHistory
  └── ClearEntitiesCommand
  └── ContractSelectionCommand
  └── CopyCommand
  └── CutCommand
  └── DeselectCommand
  └── EditLineCommand
  └── EnvironmentCommand
  └── ExpandCommand
  └── FillCommand
  └── FlipCommand
  └── GlobalMaskCommand
  └── HollowCommand
  └── HotbarSwitchCommand
  └── MoveCommand
  └── PasteCommand
  └── Pos1Command
  └── Pos2Command
  └── RedoCommand
  └── RepairFillersCommand
  └── ReplaceCommand
  └── SelectChunkCommand
  └── SelectChunkSectionCommand
  └── SelectionHistoryCommand
  └── SetCommand
  └── ShiftCommand
  └── StackCommand
  └── SubmergeCommand
  └── TintCommand
  └── UndoCommand
  └── UpdateSelectionCommand
  └── WallsCommand
  └── ImageImportCommand
  └── ObjImportCommand
  └── PrefabEditInfoCommand
  └── PrefabEditKillEntitiesCommand
  └── PrefabEditModifiedCommand
  └── PrefabEditSaveUICommand
  └── PrefabEditSelectCommand
  └── PrefabEditTeleportCommand
  └── PrefabEditUpdateBoxCommand
  └── BrushConfigClearCommand
  └── BrushConfigDebugStepCommand
  └── BrushConfigExitCommand
  └── BrushConfigListCommand
  └── HubCommand
```

## CustomUIPage Hierarchy

```
CustomUIPage
  └── MemoriesPage
  └── MemoriesUnlockedPage
  └── DialogPage
  └── ObjectiveAdminPanelPage
  └── BarterPage
  └── TeleporterSettingsPage
  └── RespawnPointPage
  └── ImageImportPage
  └── ObjImportPage
  └── PrefabEditorExitConfirmPage
  └── PrefabEditorLoadSettingsPage
  └── PrefabEditorSaveSettingsPage
  └── PrefabTeleportPage
  └── PrefabPage
  └── PrefabSavePage
  └── ScriptedBrushPage
  └── ConfigureInstanceBlockPage
  └── InstanceListPage
  └── ChangeModelPage
  └── PortalDeviceActivePage
  └── PortalDeviceSummonPage
  └── WarpListPage
  └── ParticleSpawnPage
  └── CommandListPage
  └── BasicCustomUIPage
  └── InteractiveCustomUIPage
  └── RespawnPage
  └── PlaySoundPage
  └── ChoiceBasePage
  └── PluginListPage
  └── EntitySpawnPage
```

## InteractiveCustomUIPage Hierarchy

```
InteractiveCustomUIPage
  └── MemoriesPage
  └── MemoriesUnlockedPage
  └── DialogPage
  └── BarterPage
  └── TeleporterSettingsPage
  └── RespawnPointPage
  └── ImageImportPage
  └── ObjImportPage
  └── PrefabEditorExitConfirmPage
  └── PrefabEditorLoadSettingsPage
  └── PrefabEditorSaveSettingsPage
  └── PrefabTeleportPage
  └── PrefabPage
  └── PrefabSavePage
  └── ScriptedBrushPage
  └── ConfigureInstanceBlockPage
  └── InstanceListPage
  └── ChangeModelPage
  └── PortalDeviceActivePage
  └── PortalDeviceSummonPage
  └── WarpListPage
  └── ParticleSpawnPage
  └── CommandListPage
  └── RespawnPage
  └── PlaySoundPage
  └── ChoiceBasePage
  └── PluginListPage
  └── EntitySpawnPage
```

## Interaction Hierarchy

```
Interaction
  └── CameraShakeInteraction
  └── ChangeFarmingStageInteraction
  └── FertilizeSoilInteraction
  └── HarvestCropInteraction
  └── UseCaptureCrateInteraction
  └── UseCoopInteraction
  └── UseWateringCanInteraction
  └── MemoriesConditionInteraction
  └── SetMemoriesCapacityInteraction
  └── CanBreakRespawnPointInteraction
  └── StartObjectiveInteraction
  └── StartObjectiveInteraction
  └── GiveItemInteraction
  └── TeleporterInteraction
  └── BedInteraction
  └── PickupItemInteraction
  └── PrefabSelectionInteraction
  └── PrefabSetAnchorInteraction
  └── LearnRecipeInteraction
  └── OpenBenchPageInteraction
  └── OpenProcessingBenchInteraction
  └── HubPortalInteraction
  └── SpawnDeployableAtHitLocationInteraction
  └── SpawnDeployableFromRaycastInteraction
  └── ExitInstanceInteraction
  └── TeleportConfigInstanceInteraction
  └── TeleportInstanceInteraction
  └── MountInteraction
  └── SeatingInteraction
  └── SpawnMinecartInteraction
  └── EnterPortalInteraction
  └── ReturnPortalInteraction
  └── ApplyEffectInteraction
  └── ApplyForceInteraction
  └── BlockConditionInteraction
  └── BreakBlockInteraction
  └── BuilderToolInteraction
  └── CameraInteraction
  └── CancelChainInteraction
  └── ChainFlagInteraction
  └── ChainingInteraction
  └── ChangeActiveSlotInteraction
  └── ChangeBlockInteraction
  └── ChangeStatInteraction
  └── ChangeStateInteraction
  └── ChargingInteraction
  └── ClearEntityEffectInteraction
  └── ConditionInteraction
  └── CooldownConditionInteraction
  └── DamageEntityInteraction
```

## SimpleInstantInteraction Hierarchy

```
SimpleInstantInteraction
  └── CameraShakeInteraction
  └── SetMemoriesCapacityInteraction
  └── StartObjectiveInteraction
  └── PickupItemInteraction
  └── PrefabSelectionInteraction
  └── PrefabSetAnchorInteraction
  └── LearnRecipeInteraction
  └── HubPortalInteraction
  └── SpawnDeployableAtHitLocationInteraction
  └── SpawnDeployableFromRaycastInteraction
  └── ExitInstanceInteraction
  └── TeleportInstanceInteraction
  └── MountInteraction
  └── CooldownConditionInteraction
  └── DestroyBlockInteraction
  └── ExplodeInteraction
  └── IncrementCooldownInteraction
  └── ResetCooldownInteraction
  └── ToggleGliderInteraction
  └── TriggerCooldownInteraction
  └── UseEntityInteraction
  └── CancelChainInteraction
  └── ChainFlagInteraction
  └── EffectConditionInteraction
  └── RunRootInteraction
  └── StatsConditionBaseInteraction
  └── ApplyEffectInteraction
  └── RemoveEntityInteraction
  └── SendMessageInteraction
  └── ChangeStatBaseInteraction
  └── CheckUniqueItemUsageInteraction
  └── ClearEntityEffectInteraction
  └── EquipItemInteraction
  └── IncreaseBackpackCapacityInteraction
  └── InterruptInteraction
  └── LaunchProjectileInteraction
  └── ModifyInventoryInteraction
  └── OpenCustomUIInteraction
  └── OpenItemStackContainerInteraction
  └── OpenPageInteraction
  └── PlacementCountConditionInteraction
  └── RefillContainerInteraction
  └── SpawnPrefabInteraction
  └── ProjectileInteraction
  └── ContextualUseNPCInteraction
  └── UseNPCInteraction
  └── TriggerSpawnMarkersInteraction
```

