# 📦 APH Havoc Storage - Configuration Setup Guide

This guide explains how to install, configure, and manage the **APH Havoc Storage System** for DayZ servers.

APH Storage includes deployable storage, virtual storage, CodeLock support, raiding, dismantling, Discord webhooks, notifications, weapon repair benches, whitelist systems, and server-side JSON configuration.

---

## 📑 Contents

- [Installation](#installation)
- [Generated Config Location](#generated-config-location)
- [Images For GitHub](#images-for-github)
- [Main Config Overview](#main-config-overview)
- [Virtual Storage Setup](#virtual-storage-setup)
- [Idle-Based Auto Store](#idle-based-auto-store)
- [Virtual Storage Enabled Containers](#virtual-storage-enabled-containers)
- [Virtual Storage Exclusions](#virtual-storage-exclusions)
- [Virtual Storage Messages](#virtual-storage-messages)
- [CodeLock Support](#codelock-support)
- [Raiding Setup](#raiding-setup)
- [Raid Tool Timer & Damage](#raid-tool-timer--damage)
- [Dismantle Setup](#dismantle-setup)
- [BreachingCharge / HM-C4 Support](#breachingcharge--hm-c4-support)
- [Discord Webhook Logs](#discord-webhook-logs)
- [In-Game Notifications](#in-game-notifications)
- [Weapon Bench Setup](#weapon-bench-setup)
- [Whitelist Systems](#whitelist-systems)
- [Admin Reload Keybind](#admin-reload-keybind)
- [Latest Fixes & Improvements](#latest-fixes--improvements)
- [Recommended Default Config](#recommended-default-config)
- [Troubleshooting](#troubleshooting)
- [Support](#support)
- [Copyright](#copyright)

---

## Installation

### Steam Workshop Setup

Subscribe to the mod and add it to your server startup parameters.

Example:

```bat
-mod=@CF;@Dabs Framework;@APH Havoc Storage
```

Optional supported mods can be loaded alongside APH Storage if you want their support enabled:

```bat
-mod=@CF;@Dabs Framework;@CodeLock;@Breachingcharge;@HM-C4;@APH Havoc Storage
```

> CodeLock, BreachingCharge, HM-C4, and other supported mods are optional. Only enable their support in the APH config if those mods are loaded on your server.

---

## Generated Config Location

After the first server start, APH Storage will generate its config files here:

```txt
$profile:aph_mods/aph_storage/
```

Main config:

```txt
$profile:aph_mods/aph_storage/aph_storage.json
```

Weapon bench config:

```txt
$profile:aph_mods/aph_storage/aph_weapon_bench.json
```

Logs:

```txt
$profile:aph_mods/aph_storage/raid_logs.log
$profile:aph_mods/aph_storage/interaction_logs.log
$profile:aph_mods/aph_storage/startup.log
```

Virtual Storage files:

```txt
mpmissions/storage_1/APH_VirtualStorage/
```

Player Virtual Storage folders:

```txt
mpmissions/storage_1/APH_VirtualStorage/Players/STEAMID/
```

---

## Images For GitHub

Add your guide screenshots into a GitHub folder like this:

```txt
docs/images/
```

Recommended image names:

```txt
docs/images/config-folder.png
docs/images/virtual-storage.png
docs/images/virtual-storage-restore.png
docs/images/discord-webhook.png
docs/images/raid-alert.png
docs/images/storage-notification.png
docs/images/weapon-bench.png
docs/images/codelock-storage.png
docs/images/raiding-tools.png
```

Use images in Markdown like this:

```md
![Config Folder](docs/images/config-folder.png)
```

Example:

![Config Folder](docs/images/config-folder.png)

---

## Main Config Overview

The main config controls storage behaviour, raiding, virtual storage, CodeLock support, notifications, Discord logging, admin reloads, and whitelist rules.

Important enable/disable settings:

```json
{
    "EnableCodeLockSupport": 1,
    "EnableRaiding": 1,
    "EnableVirtualContainerStorage": 1,
    "EnableVirtualStorageWebhookLogs": 1,
    "EnableRaidDiscordWebhookLogs": 1
}
```

Use:

```json
1
```

to enable a feature.

Use:

```json
0
```

to disable a feature.

---

## Virtual Storage Setup

Virtual Storage improves server performance by storing supported container contents into JSON data instead of keeping all items active in the world.

Enable Virtual Storage:

```json
"EnableVirtualContainerStorage": 1
```

Enable Virtual Storage logs:

```json
"EnableVirtualStorageLogs": 1
```

Enable debug logs only when testing:

```json
"EnableVirtualStorageDebug": 0
```

> Recommended: keep debug disabled on live servers unless you are troubleshooting.

---

## Idle-Based Auto Store

APH Storage uses an idle-based auto-store system.

This means storage will **not** auto-store while a player is actively moving items around in the inventory.

Enable auto-store:

```json
"EnableAutoStoreAfterOpenTimeout": 1,
"AutoStoreTimeoutSeconds": 60
```

Recommended value:

```json
"AutoStoreTimeoutSeconds": 60
```

Increase this if your players need more time while sorting storage:

```json
"AutoStoreTimeoutSeconds": 120
```

Auto-store options:

```json
"EnableAutoStoreNonOpenableContainers": 1,
"EnableAutoStoreCodeLockContainers": 1,
"AutoStoreWhileLocked": 1,
"AutoStoreWhileClosed": 1
```

### How Idle Auto Store Works

The idle timer resets when players are:

- Moving items
- Attaching items
- Detaching items
- Opening storage
- Restoring storage
- Interacting with storage inventory

Storage will only virtual-store after the configured idle timeout has passed.

This prevents APH Storage from virtualizing a container while players are actively organizing loot.

---

## Virtual Storage Enabled Containers

These are the container classes APH Storage will allow virtual storage on:

```json
"VirtualStorageEnabledContainers": [
    "APH_Storage_Container_Base",
    "APH_Storage_Openable_Base",
    "APH_Storage_Placeable_Base",
    "Barrel_ColorBase",
    "SmallProtectorCase",
    "TentBase",
    "SeaChest",
    "WoodenCrate",
    "Container_Base"
]
```

### Supported Storage Types

- APH custom storage
- APH lockers
- APH safes
- APH shelves
- APH racks
- Wooden crates
- Sea chests
- Barrels
- Barrel shelves
- Small protector cases
- Tents
- Modded `Container_Base` storage

### Nested / Attached Container Support

APH Storage supports attached and nested containers, including:

- WoodenCrate attached to APH storage
- SeaChest attached to APH storage
- Barrel storage
- Barrel shelf storage
- Container_Base derived items
- Storage inside supported storage where allowed by DayZ inventory rules

---

## Virtual Storage Exclusions

Some containers should never be virtual-stored, especially temporary event containers, airdrop systems, vehicle airdrops, loot chest systems, public workbenches, or dynamically managed storage from other mods.

Default exclusions:

```json
"VirtualStorageExcludedContainers": [
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic"
]
```

### Script Default Equivalent

The default script setup uses:

```c
VirtualStorageExcludedContainers = new array<string>;
VirtualStorageExcludedContainers.Insert("LB_LC_Base");
VirtualStorageExcludedContainers.Insert("LB_Airdrop_Car_Base");
VirtualStorageExcludedContainers.Insert("LB_Airdrop_Base");
VirtualStorageExcludedContainers.Insert("zm_WorkbenchPublic");
```

### What These Exclusions Do

| Class | Purpose |
|---|---|
| `LB_LC_Base` | Excludes LB loot chest / related dynamic containers |
| `LB_Airdrop_Car_Base` | Excludes LB vehicle airdrops |
| `LB_Airdrop_Base` | Excludes LB airdrop containers |
| `zm_WorkbenchPublic` | Excludes ZenMod public workbenches |

### When To Add Exclusions

Add a class here if:

- It is an event object
- It is temporary
- It is controlled by another mod
- It is a public workbench
- It should never save loot through APH
- It is causing unwanted virtual storage behaviour

Example:

```json
"VirtualStorageExcludedContainers": [
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic",
    "My_Event_Container",
    "My_Public_Workbench"
]
```

---

## Virtual Storage Messages

These messages show to players when storage is stored or restored.

```json
"VirtualStorageNotifyPlayers": 1,
"VirtualStorageStoreMessage": "[APH Storage] Contents stored virtually.",
"VirtualStorageRestoreMessage": "[APH Storage] Virtual contents restored.",
"VirtualStorageAutoStoreMessage": "[APH Storage] Contents were auto-stored for server performance.",
"VirtualStorageStoreEmptyMessage": "[APH Storage] Nothing to store.",
"VirtualStorageRestoreNoFileMessage": "[APH Storage] No virtual contents found for this container.",
"VirtualStorageRestoreBlockedMessage": "[APH Storage] Restore blocked because this container still has live items."
```

You can edit the text to fit your server.

Example:

```json
"VirtualStorageStoreMessage": "[Server Storage] This container has been optimized."
```

---

## CodeLock Support

Enable CodeLock support:

```json
"EnableCodeLockSupport": 1,
"EnableCodeLockAttachment": 1
```

Enable CodeLock containers to auto-store:

```json
"EnableAutoStoreCodeLockContainers": 1
```

Allow auto-store while locked:

```json
"AutoStoreWhileLocked": 1
```

### CodeLock Raiding

```json
"EnableCodeLockRaiding": 0,
"DeleteCodeLockOnSuccessfulRaid": 0
```

Set to `1` if you want CodeLocks to be affected by raiding.

Explosive CodeLock options:

```json
"BreachingChargeDestroysCodeLock": 1,
"HMC4DestroysCodeLock": 1
```

---

## Raiding Setup

Enable raiding:

```json
"EnableRaiding": 1
```

Enable vanilla tool raiding:

```json
"EnableVanillaToolRaiding": 1
```

Default raid tools:

```json
"RaidTools": [
    "SledgeHammer",
    "Crowbar",
    "FirefighterAxe",
    "FirefighterAxe_Black",
    "FirefighterAxe_Green",
    "Hatchet",
    "Hacksaw"
]
```

Raid damage settings:

```json
"RaidDamagePerAction": 10,
"RaidActionTimeSeconds": 8,
"ToolDamageOnRaid": 25
```

---

## Raid Tool Timer & Damage

The `RaidTools` array decides which tools are allowed to raid.

The timer and damage are controlled by these global settings:

```json
"RaidDamagePerAction": 10,
"RaidActionTimeSeconds": 8,
"ToolDamageOnRaid": 25
```

### What These Settings Mean

| Setting | Meaning |
|---|---|
| `RaidTools` | List of tools allowed to perform raid actions |
| `RaidActionTimeSeconds` | How long each raid action takes |
| `RaidDamagePerAction` | Damage applied to the storage or door after each completed action |
| `ToolDamageOnRaid` | Damage applied to the raid tool after each completed action |

### Example

If the config is:

```json
"RaidDamagePerAction": 10,
"RaidActionTimeSeconds": 8,
"ToolDamageOnRaid": 25
```

Then every completed raid action:

- Takes 8 seconds
- Applies 10 damage to the target
- Applies 25 damage to the raid tool

If the target has 100 health, it would take 10 completed raid actions to destroy.

### Adding Custom Raid Tools

Add the class name into the list:

```json
"RaidTools": [
    "SledgeHammer",
    "Crowbar",
    "MyCustomRaidTool"
]
```

> The class name must match the item class used by the mod.

---

## Dismantle Setup

Dismantle tools:

```json
"DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer"
]
```

Dismantle settings:

```json
"CanDeCraftKits": 1,
"DeCraftKitText": "De-Craft Kit",
"DeCraftKitTool": "Screwdriver",
"DeCraftKitToolTime": 10,
"DeCraftKitToolDamage": 10
```

### Adding Custom Dismantle Tools

```json
"DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer",
    "Wrench"
]
```

> The dismantle tool class must match the item class name.

---

## BreachingCharge / HM-C4 Support

APH Storage supports explosive raiding for both storage objects and doors.

Enable BreachingCharge support:

```json
"EnableBreachingChargeSupport": 1
```

Enable HM-C4 support:

```json
"EnableHMC4Support": 1
```

Control whether explosives destroy CodeLocks:

```json
"BreachingChargeDestroysCodeLock": 1,
"HMC4DestroysCodeLock": 1
```

Open storage after explosive raid:

```json
"OpenStorageAfterExplosiveRaid": 1
```

### Supported Explosive Targets

- APH storage objects
- APH lockers
- APH safes
- APH shelves
- APH blast doors
- APH bunker doors
- APH armoured doors
- APH caged doors
- APH shutter doors
- Lockable APH containers

---

## Discord Webhook Logs

APH Storage supports Discord logging for both Virtual Storage and Raid events.

![Discord Webhook](docs/images/discord-webhook.png)

---

## Virtual Storage Webhooks

Enable virtual storage Discord logs:

```json
"EnableVirtualStorageWebhookLogs": 1
```

Set your webhook URL:

```json
"VirtualStorageDiscordWebhookURL": "YOUR_DISCORD_WEBHOOK_HERE"
```

Set webhook username:

```json
"VirtualStorageDiscordWebhookUsername": "APH Storage Logs"
```

Set webhook image:

```json
"VirtualStorageWebhookImageURL": "https://i.postimg.cc/TY74fyqF/storage-logs.png"
```

Enable event types:

```json
"VirtualStorageWebhookStoreEvents": 1,
"VirtualStorageWebhookRestoreEvents": 1,
"VirtualStorageWebhookAutoStoreEvents": 1,
"VirtualStorageWebhookErrorEvents": 1
```

Webhook info toggles:

```json
"VirtualStorageWebhookIncludePlayerName": 1,
"VirtualStorageWebhookIncludeSteamID": 1,
"VirtualStorageWebhookIncludeDateTime": 1,
"VirtualStorageWebhookIncludeJsonFileName": 1,
"VirtualStorageWebhookIncludePosition": 1,
"VirtualStorageWebhookIncludeOperationID": 1,
"VirtualStorageWebhookIncludeItemAudit": 0
```

> Set `VirtualStorageWebhookIncludeItemAudit` to `1` only if you want detailed item lists. This may make Discord logs much larger.

---

## Raid Discord Webhooks

Enable raid Discord logs:

```json
"EnableRaidDiscordWebhookLogs": 1
```

Set webhook URL:

```json
"RaidDiscordWebhookURL": "YOUR_RAID_WEBHOOK_HERE"
```

Set username and image:

```json
"RaidDiscordWebhookUsername": "APH Raid Alerts",
"RaidDiscordWebhookImageURL": "https://i.postimg.cc/ZRwrpNRd/raiding.png"
```

Enable event types:

```json
"RaidWebhookArmedEvents": 1,
"RaidWebhookBreachedEvents": 1,
"RaidWebhookBlockedEvents": 1,
"RaidWebhookTestEvents": 1
```

Webhook info toggles:

```json
"RaidWebhookIncludePlayerName": 1,
"RaidWebhookIncludeSteamID": 1,
"RaidWebhookIncludeDateTime": 1,
"RaidWebhookIncludePosition": 1,
"RaidWebhookIncludeContainer": 1,
"RaidWebhookIncludeCharge": 1,
"RaidWebhookIncludeOperationID": 1
```

---

## In-Game Notifications

Enable APH notification support:

```json
"EnableAPHNotificationSystem": 1
```

Notification titles:

```json
"APHStorageNotificationTitle": "APH Storage",
"APHRaidNotificationTitle": "APH Raid Alert"
```

Icons:

```json
"APHStorageNotificationIcon": "APH_Storage/Scripts/Data/Icons/storage_logs.paa",
"APHRaidNotificationIcon": "APH_Storage/Scripts/Data/Icons/raiding.paa"
```

Duration:

```json
"APHNotificationDurationSeconds": 8
```

Raid messages:

```json
"EnableRaidInGameMessages": 1,
"RaidArmedMessage": "[APH RAID ALERT] A breaching charge has been armed.",
"RaidBreachedMessage": "[APH RAID ALERT] Storage has been breached.",
"RaidBlockedMessage": "[APH RAID ALERT] Raiding is not allowed right now."
```

---

## Weapon Bench Setup

Enable weapon bench repair:

```json
"EnableGunBenchRepair": 1
```

Repair settings:

```json
"GunBenchRepairTimeSeconds": 12.0,
"GunBenchTargetHealth01": 1.0
```

Supported bench classes:

```json
"GunBenchClassNames": [
    "APH_Storage_Armoury_Table_With_GunStnad"
]
```

Supported weapon slots:

```json
"GunBenchWeaponSlotNames": [
    "RifleStand",
    "Pistol_A",
    "PistolStand"
]
```

Required parts:

```json
"GunBenchRequiredParts": [
    {
        "ClassName": "WeaponCleaningKit",
        "RequiredQuantity": 1.0,
        "ConsumeQuantity": 0.0,
        "ConsumeHealth": 20.0
    },
    {
        "ClassName": "Rag",
        "RequiredQuantity": 2.0,
        "ConsumeQuantity": 2.0,
        "ConsumeHealth": 0.0
    },
    {
        "ClassName": "MetalWire",
        "RequiredQuantity": 1.0,
        "ConsumeQuantity": 1.0,
        "ConsumeHealth": 0.0
    }
]
```

---

## Whitelist Systems

APH Storage includes whitelist controls for certain storage types.

---

### Ammo Box Whitelist

```json
"EnableAmmoBoxWhitelist": 1,
"AmmoBoxWhitelist": [
    "Box_Base",
    "Ammunition_Base"
]
```

---

### Refrigerator Whitelist

```json
"EnableRefrigeratorWhitelist": 1,
"EnableRefrigeratorFoodPreserve": 1,
"RefrigeratorWhitelist": [
    "SodaCan_ColorBase",
    "Bottle_Base",
    "Edible_Base"
]
```

---

### Medical Cabinet Whitelist

```json
"EnableMedicalCabinetWhitelist": 1,
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
    "ChelatingTablets"
]
```

---

### Container Base Ammunition Whitelist

```json
"EnableContainerBaseAmmunitionAllow": 1,
"ContainerBaseAmmunitionWhitelist": [
    "Box_Base",
    "Ammunition_Base",
    "Magazine_Base"
]
```

---

## Admin Reload Keybind

Enable config reload keybind:

```json
"EnableStorageConfigReloadKeybind": 1
```

Allowed SteamIDs:

```json
"StorageConfigReloadAdminSteamIDs": [
    "76561198328537598"
]
```

Only SteamIDs in this list can reload the APH Storage config in-game.

---

## Latest Fixes & Improvements

### Virtual Storage

- Fixed attached WoodenCrate persistence
- Fixed attached SeaChest persistence
- Fixed attached barrel storage persistence
- Fixed barrel shelf storage handling
- Fixed nested container persistence
- Fixed `Container_Base` virtual storage handling
- Added idle-based auto-store system
- Added automatic activity detection
- Improved restart recovery
- Improved duplication protection
- Improved storage synchronization
- Improved behaviour when Virtual Storage is disabled in JSON

### Raiding

- Fixed `RaidTools` reading from JSON configuration
- Fixed `DismantleTools` reading from JSON configuration
- Added BreachingCharge support for APH storage
- Added BreachingCharge support for APH doors
- Added HM-C4 support for APH storage
- Added HM-C4 support for APH doors
- Improved raid webhook support
- Improved raid notifications

### Storage / Models

- Fixed lockers opening incorrectly
- Fixed locker door alignment issues
- Updated storage slot configurations
- Improved APH storage backend validation

### Compatibility

- Added LB loot chest exclusion support
- Added LB vehicle airdrop exclusion support
- Added LB airdrop exclusion support
- Added ZenMod public workbench exclusion support

---

## Recommended Default Config

Below is a clean recommended config baseline.

```json
{
    "EnableCodeLockSupport": 1,
    "EnableCodeLockAttachment": 1,
    "EnableCodeLockRaiding": 0,
    "DeleteCodeLockOnSuccessfulRaid": 0,

    "EnableRaiding": 1,
    "EnableVanillaToolRaiding": 1,
    "EnableBreachingChargeSupport": 1,
    "EnableHMC4Support": 1,
    "EnableRaidLogs": 1,
    "EnableInteractionLogs": 1,
    "DisableContainerDamageWhenRaidingDisabled": 1,

    "ProxyMode": 1,
    "AutoCloseOnServerStart": 1,
    "EnableAutoCloseStorageTimer": 1,
    "AutoCloseMinutes": 5,
    "OpenCloseRange": 2.0,

    "EnableVirtualContainerStorage": 1,
    "EnableVirtualStorageLogs": 1,
    "EnableVirtualStorageDebug": 0,
    "EnableAutoStoreAfterOpenTimeout": 1,
    "AutoStoreTimeoutSeconds": 60,
    "EnableAutoStoreNonOpenableContainers": 1,
    "EnableAutoStoreCodeLockContainers": 1,
    "AutoStoreWhileLocked": 1,
    "AutoStoreWhileClosed": 1,
    "RequireItemsForStoreAction": 1,
    "VirtualStorageNotifyPlayers": 1,

    "VirtualStorageStoreMessage": "[APH Storage] Contents stored virtually.",
    "VirtualStorageRestoreMessage": "[APH Storage] Virtual contents restored.",
    "VirtualStorageAutoStoreMessage": "[APH Storage] Contents were auto-stored for server performance.",
    "VirtualStorageStoreEmptyMessage": "[APH Storage] Nothing to store.",
    "VirtualStorageRestoreNoFileMessage": "[APH Storage] No virtual contents found for this container.",
    "VirtualStorageRestoreBlockedMessage": "[APH Storage] Restore blocked because this container still has live items.",

    "VirtualStorageEnabledContainers": [
        "APH_Storage_Container_Base",
        "APH_Storage_Openable_Base",
        "APH_Storage_Placeable_Base",
        "Barrel_ColorBase",
        "SmallProtectorCase",
        "TentBase",
        "SeaChest",
        "WoodenCrate",
        "Container_Base"
    ],

    "VirtualStorageExcludedContainers": [
        "LB_LC_Base",
        "LB_Airdrop_Car_Base",
        "LB_Airdrop_Base",
        "zm_WorkbenchPublic"
    ],

    "RaidTools": [
        "SledgeHammer",
        "Crowbar",
        "FirefighterAxe",
        "FirefighterAxe_Black",
        "FirefighterAxe_Green",
        "Hatchet",
        "Hacksaw"
    ],

    "RaidDamagePerAction": 10,
    "RaidActionTimeSeconds": 8,
    "ToolDamageOnRaid": 25,

    "DismantleTools": [
        "Screwdriver",
        "Pliers",
        "Hammer"
    ],

    "BreachingChargeDestroysCodeLock": 1,
    "HMC4DestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1,

    "EnableAPHNotificationSystem": 1,
    "APHStorageNotificationTitle": "APH Storage",
    "APHRaidNotificationTitle": "APH Raid Alert",
    "APHStorageNotificationIcon": "APH_Storage/Scripts/Data/Icons/storage_logs.paa",
    "APHRaidNotificationIcon": "APH_Storage/Scripts/Data/Icons/raiding.paa",
    "APHNotificationDurationSeconds": 8,

    "EnableRaidInGameMessages": 1,
    "RaidArmedMessage": "[APH RAID ALERT] A breaching charge has been armed.",
    "RaidBreachedMessage": "[APH RAID ALERT] Storage has been breached.",
    "RaidBlockedMessage": "[APH RAID ALERT] Raiding is not allowed right now."
}
```

> Your generated config may contain more values than this baseline. Do not delete extra values unless you know what they are used for.

---

## Troubleshooting

### Config did not generate

Make sure the server has write permission to:

```txt
$profile:aph_mods/aph_storage/
```

Restart the server after installing the mod.

---

### Discord webhook not sending

Check:

```json
"EnableVirtualStorageWebhookLogs": 1
```

Make sure:

```json
"VirtualStorageDiscordWebhookURL": "YOUR_WEBHOOK"
```

is a valid Discord webhook URL.

Also check that your firewall or host does not block outbound webhook requests.

---

### Raid tools do not work

Check:

```json
"EnableRaiding": 1,
"EnableVanillaToolRaiding": 1
```

Confirm the exact tool class is listed:

```json
"RaidTools": [
    "Crowbar"
]
```

Also confirm the raid target is an APH-supported storage or door object.

---

### Raid tool timer or damage feels wrong

Adjust:

```json
"RaidDamagePerAction": 10,
"RaidActionTimeSeconds": 8,
"ToolDamageOnRaid": 25
```

Lower `RaidActionTimeSeconds` for faster raiding.

Increase `RaidDamagePerAction` for stronger raid damage.

Increase `ToolDamageOnRaid` if you want tools to break faster.

---

### Dismantle tools do not work

Check:

```json
"DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer"
]
```

Make sure the item class name is correct.

---

### Items disappear when storage virtualizes

Make sure you are using the latest APH Storage update with:

- Nested container protection
- Attached container protection
- Idle-only auto-store
- Barrel shelf support
- Container_Base support
- Virtual storage exclusion support

Also exclude any temporary/event containers:

```json
"VirtualStorageExcludedContainers": [
    "Your_Event_Container_Class"
]
```

---

### Auto-store happens while moving items

Make sure your build includes the idle-only auto-store update.

Recommended:

```json
"AutoStoreTimeoutSeconds": 60
```

Increase it if your players need more time:

```json
"AutoStoreTimeoutSeconds": 120
```

---

### BreachingCharge or HM-C4 does not work

Check:

```json
"EnableBreachingChargeSupport": 1,
"EnableHMC4Support": 1
```

Also make sure the BreachingCharge / HM-C4 mod is loaded before APH Storage if required by your server setup.

---

## Support

When reporting bugs, please provide:

- Server logs
- Client crash logs if applicable
- Config file
- Video or screenshots
- Steps to reproduce
- Full mod list
- Exact storage class name if known

Join the APH Discord:

```txt
https://discord.gg/2wHVcNgEn7
```

---

## Copyright

APH Storage assets, scripts, models, textures, and source files may not be repacked, reuploaded, redistributed, extracted, monetized, or reused without written permission.

Server owners may edit configuration files for personal server use only.

Unauthorized redistribution of APH assets may result in takedown action.
