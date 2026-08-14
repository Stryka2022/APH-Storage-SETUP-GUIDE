# APH Havoc Storage / APH Core JSON Configuration Guide

This document covers the JSON configuration used by **APH Havoc Storage / APH Core** only.

It includes:

- Main APH Storage configuration
- CodeLock integration
- Raiding
- Raid tools
- Breaching Charge / HMC4 support
- Storage whitelists
- Bed Rest
- Kit crafting and dismantling
- Virtual Storage
- APH notifications
- Discord logging
- Raid Schedule
- Raid Target Control
- Config reload/admin permissions

> **Important:** JSON field names are case-sensitive. Class names must match DayZ/mod class names exactly unless the feature explicitly uses inheritance through `IsKindOf`.

---
# 1. APH Havoc Storage

## File location

```text
$profile:aph_mods/aph_storage/aph_storage.json
```

With `-profiles=Config`, this normally resolves under:

```text
DayZServer\Config\aph_mods\aph_storage\aph_storage.json
```

APH creates the folder/config automatically when it is missing.

## What `aph_storage.json` controls

The main Storage JSON controls:

- CodeLock support and CodeLock raiding behavior
- Standard/vanilla tool raiding
- Breaching Charge / HMC4 support
- raid and interaction logging
- storage open/close behavior
- proxy visibility
- automatic storage closing
- ammo-box whitelist behavior
- refrigerator food preservation
- medical-cabinet whitelist behavior
- ammunition allowances
- bed-rest/regeneration
- APH kit crafting
- APH kit dismantling
- raid tools and tool damage
- raid animation/action selection
- virtual/file-backed storage
- storage/raid notifications
- Discord webhooks
- reload administrators
- virtual-storage enabled/excluded container classes

---

## CodeLock settings

| Setting | Meaning |
|---|---|
| `EnableCodeLockSupport` | Master APH CodeLock compatibility switch. |
| `EnableCodeLockAttachment` | Allows CodeLock attachment support on APH storage where supported. |
| `EnableCodeLockRaiding` | Allows APH raid logic to interact with locked storage/CodeLocks. |
| `DeleteCodeLockOnSuccessfulRaid` | Deletes the attached CodeLock after a successful APH raid when enabled. |

---

## Raid master settings

| Setting | Meaning |
|---|---|
| `EnableRaiding` | Master APH raiding switch. |
| `EnableVanillaToolRaiding` | Enables raid actions using tools listed in `RaidTools`. |
| `EnableBreachingChargeSupport` | Enables supported Breaching Charge integrations. |
| `EnableHMC4Support` | Enables supported HMC4 integrations. |
| `EnableRaidLogs` | Writes raid activity to APH raid logs. |
| `EnableInteractionLogs` | Writes relevant APH interaction logs. |
| `DisableContainerDamageWhenRaidingDisabled` | Prevents normal container damage being used to bypass disabled APH raiding. |
| `RaidDamagePerAction` | Damage applied per APH raid-tool action. |
| `RaidActionTimeSeconds` | Duration of a raid-tool action. |
| `ToolDamageOnRaid` | Tool condition damage applied by raid use. |
| `BreachingChargeDestroysCodeLock` | Breaching Charge explosions may destroy the CodeLock. |
| `HMC4DestroysCodeLock` | HMC4 explosions may destroy the CodeLock. |
| `OpenStorageAfterExplosiveRaid` | Opens supported storage after successful explosive raid processing. |

---

## Open / close / proxy settings

| Setting | Meaning |
|---|---|
| `ProxyMode` | `0` = always hide proxies, `1` = hide proxies only while closed, `2` = never hide proxies. |
| `AutoCloseOnServerStart` | Forces supported APH openable storage closed after startup. |
| `EnableAutoCloseStorageTimer` | Enables automatic close timeout. |
| `AutoCloseMinutes` | Main automatic close timer in minutes. Also used by current virtual-storage timeout flow. |
| `OpenCloseRange` | Maximum interaction range for APH open/close logic. |

### Important legacy setting

`AutoStoreTimeoutSeconds` still exists for old JSON compatibility, but the current runtime uses **`AutoCloseMinutes` as the unified timer**. Do not use `AutoStoreTimeoutSeconds` to tune the active timeout.

---

## Storage whitelist settings

### Ammo boxes

`EnableAmmoBoxWhitelist` enables the APH ammo-box restriction/allowance logic.

`AmmoBoxWhitelist` accepts matching base or concrete class names.

Default:

```json
[
  "Box_Base",
  "Ammunition_Base"
]
```

### Refrigerators

`EnableRefrigeratorWhitelist` enables refrigerator whitelist handling.

`EnableRefrigeratorFoodPreserve` enables APH preservation behavior for allowed refrigerator content.

Default accepted classes:

```json
[
  "SodaCan_ColorBase",
  "Bottle_Base",
  "Edible_Base"
]
```

Because `Edible_Base` is included, inherited edible items are covered.

### Medical cabinets

`EnableMedicalCabinetWhitelist` enables medical-cabinet whitelist logic.

Add third-party medical item base classes or concrete class names to `MedicalCabinetWhitelist`.

### General ammunition allowance

`EnableContainerBaseAmmunitionAllow` enables ammunition/magazine allowances through `ContainerBaseAmmunitionWhitelist`.

Default:

```json
[
  "Ammunition_Base",
  "Magazine_Base"
]
```

---

# 2. APH Bed Rest

Bed Rest is configured inside `aph_storage.json`.

| Setting | Meaning |
|---|---|
| `EnableBedRest` | Enables APH bed/sleeping-bag rest logic. |
| `BedRestRequireEmptyHands` | Requires the player to have empty hands before resting. |
| `BedRestMaximumDistance` | Maximum interaction distance. |
| `BedRestTickSeconds` | Interval between regeneration ticks. |
| `BedRestRegenerationMinutes` | Regeneration/rest duration control. |
| `BedRestCooldownMinutes` | Cooldown before reuse. |
| `BedRestHealthPerTick` | Health restored per valid tick. |
| `BedRestBloodPerTick` | Blood restored per valid tick. |
| `BedRestShockPerTick` | Shock restored per valid tick. |
| `BedRestEnergyPerTick` | Energy restored per valid tick. |
| `BedRestWaterPerTick` | Hydration restored per valid tick. |
| `BedRestAllowedBeds` | Allowed bed classes. |
| `BedRestAllowedSleepingBags` | Allowed sleeping-bag classes. |
| `BedRestPlacements` | Player placement offsets per bed class. |

### Placement entry

```json
{
  "ClassName": "APH_Double_Bed_Base",
  "OffsetX": 0.0,
  "OffsetY": 0.78,
  "OffsetZ": 0.0,
  "YawOffset": 180.0
}
```

`OffsetX/Y/Z` moves the player relative to the object model. `YawOffset` rotates the player relative to the bed.

---

# 3. APH Kit Crafting and Dismantling

## Kit crafting

| Setting | Meaning |
|---|---|
| `CanCraftKits` | Enables APH generated kit recipes. |
| `CraftKitToolTime` | Craft time used by APH kit crafting. |
| `CraftKitRecipeOne` | First required material class. |
| `CraftKitRecipeOneQty` | Quantity of first material. |
| `CraftKitRecipeTwo` | Second required material class. |
| `CraftKitRecipeTwoQty` | Quantity of second material. |
| `EnableCraftLogging` | Logs kit crafting. |
| `EnablePlacementLogging` | Logs APH placement. |

Default recipe requirements are 20 Nails + 4 Wooden Planks.

## Dismantling

| Setting | Meaning |
|---|---|
| `CanDismantleKits` | Enables dismantling. |
| `DismantleKitText` | Action text. |
| `DismantleKitTool` | Default tool class. |
| `DismantleKitToolTime` | Dismantle duration. |
| `DismantleKitToolDamage` | Default tool damage. |
| `DismantleTools` | Allowed dismantle tool classes. |
| `DismantleToolDamages` | Per-tool damage overrides. |
| `DismantleToolActions` | Animation/action command selected per tool. |
| `EnableDismantleLogging` | Logs dismantle activity. |

Supported action strings currently include:

```text
INTERACT
CRAFTING
ASSEMBLE
DIG
SKINNING
HACKTREE
HACKBUSH
CUTBARK
SPLITTING_FIREWOOD
DISASSEMBLE
```

Unknown/invalid values fall back to `DISASSEMBLE`.

---

# 4. APH Virtual Storage

APH Virtual Storage removes live inventory contents from supported containers and serializes them to APH-owned storage files, reducing the number of live inventory entities while the container is stored.

## Storage path

```text
$mission:storage_1/APH_VirtualStorage
```

Player-organized files are stored under:

```text
$mission:storage_1/APH_VirtualStorage/Players
```

## Main switches

| Setting | Meaning |
|---|---|
| `EnableVirtualContainerStorage` | Master switch. When false, APH virtual storage is inactive. |
| `VirtualStorageMonitorIntervalSeconds` | Background monitor interval. Values below 2 are reset to 5. |
| `EnableVirtualStorageLogs` | Enables virtual-storage file logging. |
| `EnableVirtualStorageDebug` | Enables additional debug logging. |
| `EnableAutoStoreAfterOpenTimeout` | Enables timed auto-store flow. |
| `EnableAutoStoreNonOpenableContainers` | Enables auto-store logic for supported non-openable containers. |
| `EnableAutoStoreCodeLockContainers` | Enables auto-store support for CodeLock containers. |
| `AutoStoreWhileLocked` | Allows storage while locked. |
| `AutoStoreWhileClosed` | Allows storage while closed. |
| `RequireItemsForStoreAction` | Requires actual contents before manual store operation is useful/allowed. |
| `VirtualStorageNotifyPlayers` | Enables player notifications. |
| `VirtualStoragePlayerLogRetentionDays` | Player log retention period. |

### Enabled and excluded containers

`VirtualStorageEnabledContainers` is the allow list.

`VirtualStorageExcludedContainers` is a hard exclusion list and wins over the enabled list.

APH classes remain supported by their APH base types. A generic `Container_Base` entry is intentionally not allowed to opt every modded container into virtual storage.

### Notifications

The six message fields control player-facing virtual-storage text:

- `VirtualStorageStoreMessage`
- `VirtualStorageRestoreMessage`
- `VirtualStorageAutoStoreMessage`
- `VirtualStorageStoreEmptyMessage`
- `VirtualStorageRestoreNoFileMessage`
- `VirtualStorageRestoreBlockedMessage`

---

# 5. APH Notification System

| Setting | Meaning |
|---|---|
| `EnableAPHNotificationSystem` | Master APH native-popup notification switch. |
| `APHStorageNotificationTitle` | Main storage notification title. |
| `APHRaidNotificationTitle` | Raid notification title. |
| `APHStorageNotificationIcon` | General storage icon. |
| `APHStorageStoredNotificationIcon` | Stored-content icon. |
| `APHStorageRestoredNotificationIcon` | Restored-content icon. |
| `APHRaidNotificationIcon` | Raid icon. |
| `APHNotificationDurationSeconds` | Popup duration. |

These paths are DayZ local mod resources, not Discord URLs.

---

# 6. Virtual Storage Discord Webhook

`EnableVirtualStorageWebhookLogs` must be `true`, and `VirtualStorageDiscordWebhookURL` must contain a real webhook URL.

Event switches:

- `VirtualStorageWebhookStoreEvents`
- `VirtualStorageWebhookRestoreEvents`
- `VirtualStorageWebhookAutoStoreEvents`
- `VirtualStorageWebhookErrorEvents`

Optional payload details:

- player name
- Steam64 ID
- date/time
- JSON filename
- world position
- operation ID
- item audit

`VirtualStorageWebhookImageURL` must be a public web URL if Discord is expected to render it.

---

# 7. Raid Discord Webhook

The raid webhook is independent from virtual-storage logging.

| Setting | Meaning |
|---|---|
| `EnableRaidDiscordWebhookLogs` | Master raid Discord switch. |
| `RaidDiscordWebhookURL` | Discord webhook URL. |
| `RaidDiscordWebhookUsername` | Bot/display name. |
| `RaidDiscordWebhookImageURL` | Public image URL. |
| `RaidWebhookArmedEvents` | Log charge-armed events. |
| `RaidWebhookBreachedEvents` | Log successful breaches. |
| `RaidWebhookBlockedEvents` | Log blocked raid attempts. |
| `RaidWebhookTestEvents` | Allows test-event webhook output. |

Include switches control player name, Steam64, time, position, container, charge and operation ID.

---

# 8. Storage Config Reload

`EnableStorageConfigReloadKeybind` enables the APH live reload request.

`StorageConfigReloadAdminSteamIDs` contains allowed Steam64 IDs.

Example:

```json
"StorageConfigReloadAdminSteamIDs": [
  "76561198000000001",
  "76561198000000002"
]
```

The current manager also accepts `ALL` in the admin list if you deliberately want every player to pass the APH storage reload-admin check.

---

# 9. Full `aph_storage.json` Example

```json
{
  "EnableCodeLockSupport": true,
  "EnableCodeLockAttachment": true,
  "EnableCodeLockRaiding": false,
  "DeleteCodeLockOnSuccessfulRaid": false,
  "EnableRaiding": true,
  "EnableVanillaToolRaiding": true,
  "EnableBreachingChargeSupport": false,
  "EnableHMC4Support": false,
  "EnableRaidLogs": true,
  "EnableInteractionLogs": true,
  "DisableContainerDamageWhenRaidingDisabled": true,
  "ProxyMode": 1,
  "AutoCloseOnServerStart": true,
  "EnableAutoCloseStorageTimer": true,
  "AutoCloseMinutes": 5,
  "OpenCloseRange": 2.0,
  "EnableAmmoBoxWhitelist": true,
  "AmmoBoxWhitelist": [
    "Box_Base",
    "Ammunition_Base"
  ],
  "EnableRefrigeratorWhitelist": true,
  "EnableRefrigeratorFoodPreserve": true,
  "RefrigeratorWhitelist": [
    "SodaCan_ColorBase",
    "Bottle_Base",
    "Edible_Base"
  ],
  "EnableMedicalCabinetWhitelist": true,
  "MedicalCabinetWhitelist": [
    "DisinfectantSpray",
    "DisinfectantAlcohol",
    "BandageDressing",
    "Rag",
    "Heatpack",
    "PurificationTablets",
    "CharcoalTablets",
    "PainkillerTablets",
    "VitaminBottle",
    "IodineTincture",
    "TetracyclineAntibiotics",
    "Epinephrine",
    "Morphine",
    "Syringe",
    "ClearSyringe",
    "BloodSyringe",
    "InjectionVial",
    "SalineBag",
    "StartKitIV",
    "SalineBagIV",
    "BloodBagEmpty",
    "BloodBagFull",
    "BloodBagIV",
    "BloodTestKit",
    "Thermometer",
    "AntiChemInjector",
    "GasMask_Filter",
    "ChelatingTablets",
    "PUT OTHER MOD CLASS NAMES HERE MEDICAL ETC!"
  ],
  "EnableContainerBaseAmmunitionAllow": true,
  "ContainerBaseAmmunitionWhitelist": [
    "Ammunition_Base",
    "Magazine_Base"
  ],
  "EnableBedRest": true,
  "BedRestRequireEmptyHands": true,
  "BedRestMaximumDistance": 2.75,
  "BedRestTickSeconds": 10.0,
  "BedRestRegenerationMinutes": 5.0,
  "BedRestCooldownMinutes": 10.0,
  "BedRestHealthPerTick": 1.0,
  "BedRestBloodPerTick": 2.0,
  "BedRestShockPerTick": 5.0,
  "BedRestEnergyPerTick": 20.0,
  "BedRestWaterPerTick": 20.0,
  "BedRestAllowedBeds": [
    "APH_Double_Bed_Base",
    "APH_Scifi_Single_Bed_Base"
  ],
  "BedRestAllowedSleepingBags": [
    "LB_SleepingBag_Base",
    "LB_SleepingBag"
  ],
  "BedRestPlacements": [
    {
      "ClassName": "APH_Double_Bed_Base",
      "OffsetX": 0.0,
      "OffsetY": 0.78,
      "OffsetZ": 0.0,
      "YawOffset": 180.0
    },
    {
      "ClassName": "APH_Scifi_Single_Bed_Base",
      "OffsetX": 0.0,
      "OffsetY": 0.62,
      "OffsetZ": 0.0,
      "YawOffset": 180.0
    },
    {
      "ClassName": "LB_SleepingBag_Base",
      "OffsetX": 0.0,
      "OffsetY": 0.1,
      "OffsetZ": 0.0,
      "YawOffset": 180.0
    }
  ],
  "EnableCraftLogging": true,
  "EnablePlacementLogging": true,
  "EnableDismantleLogging": true,
  "CanCraftKits": true,
  "CraftKitToolTime": 20,
  "CraftKitRecipeOne": "Nail",
  "CraftKitRecipeOneQty": 20,
  "CraftKitRecipeTwo": "WoodenPlank",
  "CraftKitRecipeTwoQty": 4,
  "CanDismantleKits": true,
  "DismantleKitText": "Dismantle Kit",
  "DismantleKitTool": "Screwdriver",
  "DismantleKitToolTime": 10,
  "DismantleKitToolDamage": 10,
  "BreachingChargeDestroysCodeLock": true,
  "HMC4DestroysCodeLock": true,
  "OpenStorageAfterExplosiveRaid": true,
  "RaidTools": [
    "SledgeHammer",
    "Crowbar",
    "FirefighterAxe",
    "FirefighterAxe_Black",
    "FirefighterAxe_Green",
    "Hatchet",
    "Hacksaw"
  ],
  "DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer"
  ],
  "DismantleToolDamages": [
    {
      "ClassName": "Screwdriver",
      "Damage": 10
    },
    {
      "ClassName": "Pliers",
      "Damage": 8
    },
    {
      "ClassName": "Hammer",
      "Damage": 15
    }
  ],
  "DismantleToolActions": [
    {
      "ClassName": "Screwdriver",
      "Action": "INTERACT"
    },
    {
      "ClassName": "Pliers",
      "Action": "INTERACT"
    },
    {
      "ClassName": "Hammer",
      "Action": "DISASSEMBLE"
    },
    {
      "ClassName": "Shovel",
      "Action": "DIG"
    },
    {
      "ClassName": "FieldShovel",
      "Action": "DIG"
    },
    {
      "ClassName": "Pickaxe",
      "Action": "DIG"
    }
  ],
  "RaidAlertTargetWhitelist": [
    "APH_Storage_Container_Base",
    "APH_Storage_Openable_Base",
    "APH_Storage_Placeable_Base",
    "ArmouredDoor_ColorBase",
    "Metal_Door_ColorBase",
    "Shutter_Door_ColorBase",
    "Large_Shutter_Door_ColorBase"
  ],
  "RaidDamagePerAction": 10,
  "RaidActionTimeSeconds": 600,
  "ToolDamageOnRaid": 25,
  "EnableVirtualContainerStorage": true,
  "VirtualStorageMonitorIntervalSeconds": 5,
  "EnableVirtualStorageLogs": true,
  "EnableVirtualStorageDebug": false,
  "EnableAutoStoreAfterOpenTimeout": true,
  "AutoStoreTimeoutSeconds": 0,
  "EnableAutoStoreNonOpenableContainers": true,
  "EnableAutoStoreCodeLockContainers": true,
  "AutoStoreWhileLocked": true,
  "AutoStoreWhileClosed": true,
  "RequireItemsForStoreAction": true,
  "VirtualStorageNotifyPlayers": true,
  "VirtualStorageStoreMessage": "[APH Storage] Contents stored virtually.",
  "VirtualStorageRestoreMessage": "[APH Storage] Virtual contents restored.",
  "VirtualStorageAutoStoreMessage": "[APH Storage] Contents were auto-stored for server performance.",
  "VirtualStorageStoreEmptyMessage": "[APH Storage] Nothing to store.",
  "VirtualStorageRestoreNoFileMessage": "[APH Storage] No virtual contents found for this container.",
  "VirtualStorageRestoreBlockedMessage": "[APH Storage] Restore blocked because this container still has live items.",
  "VirtualStoragePlayerLogRetentionDays": 7,
  "EnableAPHNotificationSystem": true,
  "APHStorageNotificationTitle": "APH Storage",
  "APHRaidNotificationTitle": "APH Raid Alert",
  "APHStorageNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_logs.paa",
  "APHStorageStoredNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_stored.edds",
  "APHStorageRestoredNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_restored.edds",
  "APHRaidNotificationIcon": "APH_Core/Scripts/Data/Icons/raiding.paa",
  "APHNotificationDurationSeconds": 8,
  "EnableVirtualStorageWebhookLogs": false,
  "VirtualStorageDiscordWebhookURL": "PUT WEBHOOK URL HERE",
  "VirtualStorageWebhookImageURL": "https://i.postimg.cc/TY74fyqF/storage-logs.png",
  "VirtualStorageDiscordWebhookUsername": "APH Storage Logs",
  "VirtualStorageWebhookStoreEvents": true,
  "VirtualStorageWebhookRestoreEvents": true,
  "VirtualStorageWebhookAutoStoreEvents": true,
  "VirtualStorageWebhookErrorEvents": true,
  "VirtualStorageWebhookIncludePlayerName": true,
  "VirtualStorageWebhookIncludeSteamID": true,
  "VirtualStorageWebhookIncludeDateTime": true,
  "VirtualStorageWebhookIncludeJsonFileName": true,
  "VirtualStorageWebhookIncludePosition": true,
  "VirtualStorageWebhookIncludeOperationID": true,
  "VirtualStorageWebhookIncludeItemAudit": false,
  "EnableRaidDiscordWebhookLogs": false,
  "RaidDiscordWebhookURL": "PUT RAID WEBHOOK URL HERE",
  "RaidDiscordWebhookUsername": "APH Raid Alerts",
  "RaidDiscordWebhookImageURL": "https://i.postimg.cc/ZRwrpNRd/raiding.png",
  "RaidWebhookArmedEvents": true,
  "RaidWebhookBreachedEvents": true,
  "RaidWebhookBlockedEvents": true,
  "RaidWebhookTestEvents": true,
  "RaidWebhookIncludePlayerName": true,
  "RaidWebhookIncludeSteamID": true,
  "RaidWebhookIncludeDateTime": true,
  "RaidWebhookIncludePosition": true,
  "RaidWebhookIncludeContainer": true,
  "RaidWebhookIncludeCharge": true,
  "RaidWebhookIncludeOperationID": true,
  "EnableRaidInGameMessages": true,
  "RaidArmedMessage": "[APH RAID ALERT] A breaching charge has been armed.",
  "RaidBreachedMessage": "[APH RAID ALERT] Storage has been breached.",
  "RaidBlockedMessage": "[APH RAID ALERT] Raiding is not allowed right now.",
  "EnableStorageConfigReloadKeybind": true,
  "StorageConfigReloadAdminSteamIDs": [
    "PUT_YOUR_STEAM64_ID_HERE"
  ],
  "VirtualStorageEnabledContainers": [
    "APH_Storage_Container_Base",
    "APH_Storage_Openable_Base",
    "APH_Storage_Placeable_Base",
    "Barrel_ColorBase",
    "SmallProtectorCase",
    "TentBase",
    "SeaChest",
    "WoodenCrate"
  ],
  "VirtualStorageExcludedContainers": [
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic"
  ]
}
```

---

# 10. APH Raid Schedule

## File

```text
$profile:aph_mods/aph_storage/aph_raid_schedule.json
```

## Purpose

This controls **when raiding is allowed**.

When:

```json
"EnableRaidSchedule": false
```

the schedule does not restrict raiding.

When enabled, each day can have its own start and finish time.

### Overnight windows

Overnight windows are supported.

Example:

```json
{
  "DayName": "Friday",
  "Enabled": true,
  "StartHour": 20,
  "StartMinute": 0,
  "FinishHour": 2,
  "FinishMinute": 0
}
```

This means the Friday window starts at 20:00 and continues into early Saturday until 02:00.

### Legacy master day switches

The top-level `AllowMonday` through `AllowSunday` fields are retained for migration and still act as master day switches in the current runtime. If `AllowSaturday` is false, Saturday remains blocked even if its `RaidDays` entry is enabled.

### Full example

```json
{
  "EnableRaidSchedule": false,
  "AllowMonday": true,
  "AllowTuesday": true,
  "AllowWednesday": true,
  "AllowThursday": true,
  "AllowFriday": true,
  "AllowSaturday": true,
  "AllowSunday": true,
  "StartHour": 0,
  "StartMinute": 0,
  "FinishHour": 23,
  "FinishMinute": 59,
  "RaidDays": [
    {
      "DayName": "Monday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Tuesday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Wednesday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Thursday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Friday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Saturday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    },
    {
      "DayName": "Sunday",
      "Enabled": true,
      "StartHour": 0,
      "StartMinute": 0,
      "FinishHour": 23,
      "FinishMinute": 59
    }
  ],
  "EnableRaidScheduleNotifications": true,
  "RaidScheduleStartedMessage": "Raiding time has started. Base raiding is now enabled.",
  "RaidScheduleEndedMessage": "Raiding time has ended. Base raiding is now disabled.",
  "RaidScheduleNotificationNote": "EnableRaidScheduleNotifications sends one in-game notification when the schedule changes from closed to open, and one when it changes from open to closed.",
  "TimeModeNote": "EnableRaidSchedule false means raiding is allowed every day/all day. When enabled, configure each RaidDays entry with Enabled plus StartHour/StartMinute and FinishHour/FinishMinute. Overnight windows are supported, for example Monday 20:00 to 02:00 continues into early Tuesday."
}
```

---

# 11. APH Raid Target Control

## File

```text
$profile:aph_mods/aph_storage/aph_storage_raid_target_control.json
```

## Purpose

This controls **what can be raided and what method is allowed per target class**.

Each target contains:

- `ClassName`
- `AllowRaidTools`
- `AllowBreachingCharge`
- `AllowHMC4`
- `AllowedRaidTools`
- `AllowedBreachingCharges`
- `AllowedHMC4Charges`

`ClassName` uses inheritance matching through `IsKindOf`.

If one of the per-target allowed-class arrays is empty while that raid method is enabled, APH treats it as allowing any globally supported tool/charge for that method.

### Example target

```json
{
  "ClassName": "APH_Storage_Container_Base",
  "AllowRaidTools": true,
  "AllowBreachingCharge": true,
  "AllowHMC4": true,
  "AllowedRaidTools": [
    "SledgeHammer",
    "Crowbar"
  ],
  "AllowedBreachingCharges": [
    "BreachingCharge"
  ],
  "AllowedHMC4Charges": [
    "HM_C4"
  ]
}
```

### Full default example

```json
{
  "EnableRaidTargetControl": true,
  "DefaultAllowRaidTools": false,
  "DefaultAllowBreachingCharge": false,
  "DefaultAllowHMC4": false,
  "Note": "Per-target raid control. ClassName supports inheritance via IsKindOf. Leave AllowedRaidTools/AllowedBreachingCharges/AllowedHMC4Charges empty to allow any globally enabled tool/charge for that raid method. Doors default to charges only; lockers, safes, and storage default to tools and charges.",
  "Targets": [
    {
      "ClassName": "APH_Storage_Container_Base",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Openable_Base",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Placeable_Base",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_FloorSafe",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_FloorSafe_Black",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_WallSafe",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_WallSafe_Black",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_LongWallSafe",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_LongWallSafe_Black",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_ZFA_Safe",
      "AllowRaidTools": true,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
      ],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "ArmouredDoor_ColorBase",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "Metal_Door_ColorBase",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "Shutter_Door_ColorBase",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "Large_Shutter_Door_ColorBase",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Blast_Door_Black",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Bunker_Door",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Caged_Door_Black",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Caged_Door_Green",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Metal_Door_Black",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Shutter_Door_Black",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "APH_Storage_Large_Shutter_Door_Black",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    },
    {
      "ClassName": "BBP_BASE",
      "AllowRaidTools": false,
      "AllowBreachingCharge": true,
      "AllowHMC4": true,
      "AllowedRaidTools": [],
      "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
      ],
      "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
      ]
    }
  ]
}
```

---

---

# APH Storage JSON Paths Summary

| System | File |
|---|---|
| Main Storage | `$profile:aph_mods/aph_storage/aph_storage.json` |
| Raid Schedule | `$profile:aph_mods/aph_storage/aph_raid_schedule.json` |
| Raid Target Control | `$profile:aph_mods/aph_storage/aph_storage_raid_target_control.json` |

---

# 18. Troubleshooting

If APH generates a new/default JSON unexpectedly, check the server profile path and JSON syntax first.

For bugs, config problems, class compatibility problems, or unexpected behavior, report the issue in the Rebirth Network Discord and include:

- the affected JSON
- server script log
- crash log if present
- full mod list
- classname involved
- steps to reproduce

---

## Credits

Created for APH Havoc Storage.

Maintained by APH / MISFITNO1.
