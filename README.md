# APH Havoc Storage

APH Havoc Storage is a DayZ storage, placement, security, raid-support, persistence, and utility framework for server owners.

It provides configurable storage objects, kit placement, lock support, virtual storage, performance controls, dismantling, raiding, explosive integrations, webhooks, in-game notifications, admin reload utilities, gun-bench support, and sleep/rest functionality.

This README reflects the current APH Storage feature set, including the latest virtual-storage performance changes, JSON-configurable monitor interval, storage notification icons, raid scheduling, selected Headgear/Gloves handling, A6/BUCA gun-bench compatibility, and sleep/rest support.

---

## Features

### Storage and placement

- APH storage containers, lockers, safes, crates, fridges, medical cabinets, ammo boxes, armoury furniture, doors, racks, gun walls, beds, and utility objects.
- Kit placement using the standard DayZ placement system.
- Optional kit crafting and dismantling.
- Configurable dismantle tools and animations.
- Auto-close support for openable storage.
- Optional automatic virtual storing.
- Physical storage remains fully functional when virtual storage is disabled.

### Gear storage and mannequin / locker support

- Gear-slot storage and swapping.
- Selected item handling for worn and held gear.
- Improved `Headgear` and `Gloves` storage/swap handling.
- Safer source-to-destination `InventoryLocation` movement for sensitive clothing slots.
- Support for normal gear slots such as Vest, Body, Legs, Feet, Back, Mask, Eyewear, and Armband.

### Lock and raid support

- CodeLock attachment support.
- Combination-lock compatibility.
- Configurable tool raiding.
- BreachingCharge support.
- HM-C4 support.
- Optional CodeLock destruction/drop behavior.
- Per-target raid controls.
- Raid day/time schedules.
- Raid logs, webhook alerts, and in-game raid notifications.

### Virtual storage

- Stores container contents virtually to reduce live entity load.
- Manual and automatic store / restore.
- Restart-safe persistence.
- Duplication and repeated-restore protection.
- Attached and nested container support.
- Magazine ammo-count restoration.
- Barrel and barrel-shelf persistence handling.
- Optional player notifications and webhook logs.
- Configurable virtual-storage monitor interval.
- Full runtime shutdown when `EnableVirtualContainerStorage = 0`.

### Sleep / rest

- Rest interaction on supported APH beds.
- Server-side rest configuration.
- Bed-rest placement support.
- Designed to remain independent from virtual storage.

### Gun bench / armoury

- Weapon repair-bench support.
- Vanilla attachment compatibility.
- A6 attachment compatibility.
- BUCA attachment compatibility.
- Original weapon `inventorySlot[]` values are preserved.
- APH bench slots are added alongside native gun slots.
- Support for buttstocks, handguards, foregrips, bipods, suppressors, muzzles, optics, lights, lasers, and related attachments.

### Admin and logging

- Raid logs.
- Interaction logs.
- Crafting, placement, dismantle, store, and restore logs.
- Discord webhook support.
- Per-player virtual-storage logging.
- Admin config reload keybind support.
- In-game reload feedback.

---

## Requirements

Install only the dependency mods required by the APH features you enable.

Common examples:

```txt
CF
CodeLock
Dabs Framework
BreachingCharge
HM-C4
BaseBuildingPlus
DayZ Expansion
A6
BUCA
```

Not every dependency is mandatory for every server.

---

## Installation

1. Add APH Havoc Storage to the server and client mod list.
2. Load any required dependency mods before APH.
3. Start the server once to generate APH config files.
4. Stop the server.
5. Edit the generated JSON files.
6. Restart the server.
7. Test placement, storage, locking, virtual storage, raiding, notifications, and gun-bench behavior.

Example launch order:

```txt
@CF;@Dabs Framework;@Code Lock;@Breachingcharge;@BaseBuildingPlus;@APH Havoc Storage
```

Adjust this to match your server stack.

---

## Config files

APH Storage uses these main configuration files:

```txt
aph_storage.json
aph_raid_schedule.json
aph_storage_raid_target_control.json
```

They are generated in the server profile/config area after first startup.

---

# Main config: `aph_storage.json`

## Core settings

```json
{
    "EnableCodeLockSupport": 1,
    "EnableCodeLockAttachment": 1,
    "EnableCodeLockRaiding": 0,
    "DeleteCodeLockOnSuccessfulRaid": 0,

    "EnableRaiding": 1,
    "EnableVanillaToolRaiding": 1,
    "EnableBreachingChargeSupport": 0,
    "EnableHMC4Support": 0,

    "EnableRaidLogs": 1,
    "EnableInteractionLogs": 1,

    "ProxyMode": 1,

    "AutoCloseOnServerStart": 1,
    "EnableAutoCloseStorageTimer": 1,
    "AutoCloseMinutes": 5,
    "OpenCloseRange": 2.0
}
```

---

## Virtual storage

```json
{
    "EnableVirtualContainerStorage": 1,
    "VirtualStorageMonitorIntervalSeconds": 5,

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

    "VirtualStoragePlayerLogRetentionDays": 7
}
```

### Virtual-storage monitor interval

`VirtualStorageMonitorIntervalSeconds` controls how often eligible virtual-storage containers are checked.

Examples:

```txt
2  = every 2 seconds
5  = every 5 seconds
10 = every 10 seconds
30 = every 30 seconds
60 = every 60 seconds
```

Recommended default:

```json
"VirtualStorageMonitorIntervalSeconds": 5
```

Values below 2 seconds fall back to 5 seconds.

### Important performance behavior

When:

```json
"EnableVirtualContainerStorage": 0
```

the virtual-storage system is fully disabled.

The disabled state prevents:

- virtual refresh callbacks,
- delayed retries,
- repeating monitor ticks,
- virtual inventory checks,
- virtual state synchronization,
- virtual file operations,
- virtual player-folder operations,
- pending virtual auto-store callbacks.

Previously registered virtual callbacks are removed/reset when the feature is disabled at runtime.

Normal physical storage, persistence, locking, raiding, notifications, and auto-close remain unaffected.

---

## Enabled containers

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

## Excluded containers

```json
"VirtualStorageExcludedContainers": [
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic"
]
```

Add third-party storage classes here if APH should not virtual-store them.

---

## In-game notifications

```json
{
    "EnableAPHNotificationSystem": 1,
    "APHStorageNotificationTitle": "APH Storage",
    "APHRaidNotificationTitle": "APH Raid Alert",

    "APHStorageNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_logs.edds",
    "APHStorageStoredNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_stored.edds",
    "APHStorageRestoredNotificationIcon": "APH_Core/Scripts/Data/Icons/storage_restored.edds",
    "APHRaidNotificationIcon": "APH_Core/Scripts/Data/Icons/raiding.edds",

    "APHNotificationDurationSeconds": 8
}
```

Storage events automatically use the appropriate icon:

```txt
Generic Storage  -> storage_logs.edds
Stored           -> storage_stored.edds
Restored         -> storage_restored.edds
Raid Alert       -> raiding.edds
```

---

## Raid tools

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

```json
{
    "RaidDamagePerAction": 10,
    "RaidActionTimeSeconds": 600,
    "ToolDamageOnRaid": 25
}
```

---

## Dismantle tools and animations

```json
"DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer"
]
```

```json
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
    }
]
```

Supported values:

```txt
INTERACT
CRAFTING
CRAFT
ASSEMBLE
DIG
ANIMALSKINNING
SKINNING
DISASSEMBLE
```

---

## Kit crafting and dismantling

```json
{
    "CanCraftKits": 1,
    "CraftKitToolTime": 20,
    "CraftKitRecipeOne": "Nail",
    "CraftKitRecipeOneQty": 20,
    "CraftKitRecipeTwo": "WoodenPlank",
    "CraftKitRecipeTwoQty": 4,

    "CanDismantleKits": 1,
    "DismantleKitText": "Dismantle Kit",
    "DismantleKitTool": "Screwdriver",
    "DismantleKitToolTime": 10,
    "DismantleKitToolDamage": 10
}
```

---

## Placement action

APH kits use the standard DayZ placement flow:

```c
AddAction(ActionTogglePlaceObject);
AddAction(ActionPlaceObject);
```

---

## Selected Headgear and Gloves fix

APH includes improved storage/swap handling for selected `Headgear` and `Gloves`.

The movement logic uses direct source-to-destination inventory locations for sensitive slots:

```c
InventoryLocation src = new InventoryLocation;
item.GetInventory().GetCurrentInventoryLocation(src);

InventoryLocation dst = new InventoryLocation;
dst.SetAttachment(target, item, slotId);

player.ServerTakeToDst(src, dst);
```

Recommended tests:

```txt
Selected helmet -> empty Headgear slot
Selected helmet -> occupied Headgear slot
Selected gloves -> empty Gloves slot
Selected gloves -> occupied Gloves slot

Vest
Body
Legs
Feet
Back
Mask
Eyewear
Armband
```

---

## Gun bench repair

```json
{
    "EnableGunBenchRepair": 1,
    "GunBenchRepairTimeSeconds": 12.0,
    "GunBenchTargetHealth01": 1.0,

    "GunBenchClassNames": [
        "APH_Storage_Armoury_Table_With_GunStnad"
    ],

    "GunBenchWeaponSlotNames": [
        "RifleStand",
        "Pistol_A",
        "PistolStand"
    ]
}
```

APH Core also contains compatibility support for vanilla, A6, and BUCA attachments where configured.

---

# Raid schedule: `aph_raid_schedule.json`

Disable scheduling to allow raiding every day:

```json
{
    "EnableRaidSchedule": 0
}
```

Example scheduled setup:

```json
{
    "EnableRaidSchedule": 1,
    "RaidDays": [
        {
            "DayName": "Friday",
            "Enabled": 1,
            "StartHour": 18,
            "StartMinute": 0,
            "FinishHour": 23,
            "FinishMinute": 59
        },
        {
            "DayName": "Saturday",
            "Enabled": 1,
            "StartHour": 12,
            "StartMinute": 0,
            "FinishHour": 23,
            "FinishMinute": 59
        }
    ]
}
```

Overnight windows are supported.

---

# Per-target raid control: `aph_storage_raid_target_control.json`

```json
{
    "EnableRaidTargetControl": 1,
    "DefaultAllowRaidTools": 0,
    "DefaultAllowBreachingCharge": 0,
    "DefaultAllowHMC4": 0
}
```

Example:

```json
{
    "ClassName": "APH_Storage_Container_Base",
    "AllowRaidTools": 1,
    "AllowBreachingCharge": 1,
    "AllowHMC4": 1
}
```

---

## BreachingCharge / HM-C4

Enable only the integration you use:

```json
{
    "EnableRaiding": 1,
    "EnableBreachingChargeSupport": 1,
    "EnableHMC4Support": 1,
    "BreachingChargeDestroysCodeLock": 1,
    "HMC4DestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1
}
```

---

## Discord webhooks

APH supports separate virtual-storage and raid webhooks.

### Virtual storage

```json
{
    "EnableVirtualStorageWebhookLogs": 0,
    "VirtualStorageDiscordWebhookURL": "PUT WEBHOOK URL HERE",
    "VirtualStorageDiscordWebhookUsername": "APH Storage Logs"
}
```

### Raid

```json
{
    "EnableRaidDiscordWebhookLogs": 0,
    "RaidDiscordWebhookURL": "PUT RAID WEBHOOK URL HERE",
    "RaidDiscordWebhookUsername": "APH Raid Alerts"
}
```

Local `.paa` / `.edds` files cannot be displayed by Discord. Use a public `http/https` image URL for webhook images.

---

## Admin config reload

```json
{
    "EnableStorageConfigReloadKeybind": 1,
    "StorageConfigReloadAdminSteamIDs": [
        "PUT_YOUR_STEAM64_ID_HERE"
    ]
}
```

---

## Testing checklist

```txt
Server starts without script compile errors.
APH JSON configs load.
Storage kits place correctly.
Storage opens/closes correctly.
CodeLock attaches when enabled.
Auto-close works.
Virtual storage stores/restores when enabled.
No virtual monitor runs when EnableVirtualContainerStorage = 0.
Configured monitor interval is respected.
Virtual restore is blocked when live items already exist.
Selected Headgear stores/swaps.
Selected Gloves stores/swaps.
Raid schedule works.
Raid target controls work.
BreachingCharge / HM-C4 only work on allowed targets.
Storage Stored / Restored notifications use the correct icons.
Gun bench accepts configured vanilla/A6/BUCA attachments.
Sleep/rest interaction works on supported APH beds.
```

---

## Troubleshooting

### Virtual storage causes recurring server work

Check:

```json
"EnableVirtualContainerStorage": 0
```

If disabled, no APH virtual-storage monitor should run.

If enabled, increase:

```json
"VirtualStorageMonitorIntervalSeconds": 10
```

or higher depending on server size and object count.

### Virtual storage does not restore

Check:

- container is enabled,
- container is not excluded,
- container does not already contain live items,
- virtual storage file exists,
- logs/debug are enabled while testing.

### Gun-bench attachment will not attach

Check:

- the item keeps its original/native weapon slot,
- the APH bench slot was added alongside it,
- required A6/BUCA addon loads before APH Core,
- no other mod overwrites the same final attachment class afterward.

---

## Updating from an older version

1. Back up the old PBO and profile configs.
2. Replace the updated Core.
3. Rebuild/repack if using source.
4. Start a test server.
5. Compare old JSON with current settings.
6. Add new config entries manually if using an existing generated JSON.
7. Test virtual storage enabled and disabled.
8. Test gun bench, raiding, notifications, and physical persistence before going live.

---

## Latest changes

- Added complete virtual-storage shutdown when `EnableVirtualContainerStorage = 0`.
- Removed unnecessary virtual background scheduling when disabled.
- Added cleanup for previously scheduled virtual callbacks.
- Added JSON-configurable `VirtualStorageMonitorIntervalSeconds`.
- Added dedicated Stored / Restored notification icons.
- Improved restart recovery and restore protection.
- Improved nested storage and barrel persistence.
- Improved selected Headgear / Gloves storage and swapping.
- Expanded A6 and BUCA gun-bench compatibility.
- Improved external attachment inheritance/load order.
- Added sleep/rest support.
- Preserved normal physical storage, raiding, locks, and auto-close when virtual storage is disabled.

---

## Credits

Created for APH Havoc Storage.

Maintained by APH / MISFITNO1.
