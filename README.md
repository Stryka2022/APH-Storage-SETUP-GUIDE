# APH Havoc Storage

APH Havoc Storage is a DayZ storage, placement, security, and raid-support mod for server owners. It provides configurable storage objects, lock support, virtual storage performance handling, kit placement, dismantling, raid tools, explosive raid integrations, webhooks, notifications, and admin reload utilities.

This README is written for the current APH Storage config set and includes the latest V5 selected Headgear/Gloves storage fix, configurable dismantle tool actions, and raid schedule support.

---

## Features

### Storage and placement
- APH storage containers, lockers, safes, crates, fridges, medical cabinets, ammo boxes, armoury furniture, doors, racks, and gun walls.
- Kit placement using DayZ placement actions.
- Optional kit crafting and dismantling.
- Auto-close support for openable storage.
- Optional automatic virtual storing after timeout.

### Gear storage and mannequin/locker support
- Gear slot storage and swapping.
- Selected item handling for worn gear and held/selected gear.
- V5 fix for selected `Headgear` and `Gloves` not storing or swapping correctly.
- Safer movement using source-to-destination `InventoryLocation` handling for sensitive clothing slots.

### Lock and raid support
- CodeLock attachment support.
- Tool raiding using configurable raid tools.
- BreachingCharge and HM-C4 support toggles.
- Optional CodeLock destruction/drop behavior after successful raid.
- Per-target raid control for storage, safes, doors, BBP targets, and custom objects.
- Raid day/time schedule support.

### Virtual storage
- Stores container contents virtually to reduce live entity load.
- Auto-store options for closed, locked, openable, and non-openable containers.
- Restore-blocking safeguards to reduce duplication risk.
- Optional player notifications and webhook logging.

### Admin and logging
- Raid logs.
- Interaction logs.
- Crafting, placement, and dismantle logs.
- Discord webhook support for storage and raid events.
- Admin config reload keybind support.

---

## Requirements

Install any dependency mods your server configuration uses. Common examples:

```txt
CF
CodeLock
Dabs Framework
BreachingCharge
HM-C4
BaseBuildingPlus
DayZ Expansion
```

Not every dependency is mandatory for every server. Enable only the APH features that match your installed mod stack.

---

## Installation

1. Add the mod to your server and client mod list.
2. Add required dependency mods before APH Havoc Storage in the launch order.
3. Start the server once to generate APH config files.
4. Stop the server.
5. Edit the generated config files in your server profile/config folder.
6. Restart the server.
7. Test storage, placement, locking, virtual storage, and raid behavior.

Example launch order:

```txt
@CF;@Dabs Framework;@Code Lock;@Breachingcharge;@BaseBuildingPlus;@APH Havoc Storage
```

Adjust the order to match your server pack.

---

## Config files

APH uses these main config files:

```txt
aph_storage.json
aph_raid_schedule.json
aph_storage_raid_target_control.json
```

They are normally generated in your server profile/config area after first server start.

---

## Main config: `aph_storage.json`

This is the core APH config.

Important switches:

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
    "DisableContainerDamageWhenRaidingDisabled": 1,
    "ProxyMode": 1,
    "AutoCloseOnServerStart": 1,
    "EnableAutoCloseStorageTimer": 1,
    "AutoCloseMinutes": 5,
    "OpenCloseRange": 2.0
}
```

### CodeLock behavior

```json
{
    "EnableCodeLockSupport": 1,
    "EnableCodeLockAttachment": 1,
    "EnableCodeLockRaiding": 0,
    "DeleteCodeLockOnSuccessfulRaid": 0
}
```

Recommended default:

- Keep `EnableCodeLockSupport` enabled if using CodeLock.
- Keep `DeleteCodeLockOnSuccessfulRaid` set to `0` if you want locks dropped or preserved instead of deleted.
- Enable `EnableCodeLockRaiding` only if your raid design needs APH to interact directly with CodeLock raid behavior.

---

## Raid tools

APH separates raid tools from dismantle tools.

Configured raid tools:

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
{
    "RaidDamagePerAction": 10,
    "RaidActionTimeSeconds": 600,
    "ToolDamageOnRaid": 25
}
```

Only add a tool to `RaidTools` if you want that tool to perform APH raid actions.

---

## Dismantle tools and tool animations

Dismantle tools are configured separately:

```json
"DismantleTools": [
    "Screwdriver",
    "Pliers",
    "Hammer"
]
```

Tool damage:

```json
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
]
```

Tool action animation mapping:

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
]
```

Supported action values:

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

Use this section to stop tools from all using the same hammer/disassemble animation.

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

APH kits use the standard DayZ placement action flow:

```c
AddAction(ActionTogglePlaceObject);
AddAction(ActionPlaceObject);
```

This gives players the normal placement/wheel placement behavior when placing APH kit objects.

---

## Selected Headgear and Gloves fix

This update includes a V5 fix for selected `Headgear` and `Gloves` not storing or swapping when the item is selected or held.

The issue was caused by sensitive clothing slots silently failing through standard attachment wrapper movement in some cases. The fix uses direct source-to-destination inventory movement:

```c
InventoryLocation src = new InventoryLocation;
item.GetInventory().GetCurrentInventoryLocation(src);

InventoryLocation dst = new InventoryLocation;
dst.SetAttachment(target, item, slotId);

player.ServerTakeToDst(src, dst);
```

Test cases after installing:

```txt
Selected helmet -> empty Headgear slot
Selected helmet -> occupied Headgear slot
Selected gloves -> empty Gloves slot
Selected gloves -> occupied Gloves slot
```

Also test normal gear slots to confirm nothing regressed:

```txt
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

## Virtual storage

Virtual storage can reduce server load by storing contents outside the live container when conditions are met.

Core settings:

```json
{
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
    "VirtualStoragePlayerLogRetentionDays": 7
}
```

Enabled containers:

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

Excluded containers:

```json
"VirtualStorageExcludedContainers": [
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic"
]
```

Add third-party containers to the excluded list if APH should not virtual-store them.

---

## Ammo box whitelist

```json
{
    "EnableAmmoBoxWhitelist": 1,
    "AmmoBoxWhitelist": [
        "Box_Base",
        "Ammunition_Base"
    ],
    "EnableContainerBaseAmmunitionAllow": 1,
    "ContainerBaseAmmunitionWhitelist": [
        "Ammunition_Base",
        "Magazine_Base"
    ]
}
```

---

## Refrigerator whitelist

```json
{
    "EnableRefrigeratorWhitelist": 1,
    "EnableRefrigeratorFoodPreserve": 1,
    "RefrigeratorWhitelist": [
        "SodaCan_ColorBase",
        "Bottle_Base",
        "Edible_Base"
    ]
}
```

---

## Medical cabinet whitelist

```json
{
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
}
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
    ],
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
}
```

---

## Raid schedule: `aph_raid_schedule.json`

If raid scheduling is disabled, raiding is allowed every day/all day:

```json
{
    "EnableRaidSchedule": 0
}
```

When enabled, configure each day:

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
        },
        {
            "DayName": "Sunday",
            "Enabled": 1,
            "StartHour": 12,
            "StartMinute": 0,
            "FinishHour": 22,
            "FinishMinute": 0
        }
    ]
}
```

Overnight windows are supported, for example Monday 20:00 to Tuesday 02:00.

Notification settings:

```json
{
    "EnableRaidScheduleNotifications": 1,
    "RaidScheduleStartedMessage": "Raiding time has started. Base raiding is now enabled.",
    "RaidScheduleEndedMessage": "Raiding time has ended. Base raiding is now disabled."
}
```

---

## Per-target raid control: `aph_storage_raid_target_control.json`

This config decides which APH objects can be raided and by which method.

Global defaults:

```json
{
    "EnableRaidTargetControl": 1,
    "DefaultAllowRaidTools": 0,
    "DefaultAllowBreachingCharge": 0,
    "DefaultAllowHMC4": 0
}
```

Example target entry:

```json
{
    "ClassName": "APH_Storage_Container_Base",
    "AllowRaidTools": 1,
    "AllowBreachingCharge": 1,
    "AllowHMC4": 1,
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
}
```

Rules:

- `ClassName` can be a base class or an exact class.
- `AllowRaidTools` controls APH tool raiding.
- `AllowBreachingCharge` controls BreachingCharge compatibility.
- `AllowHMC4` controls HM-C4 compatibility.
- Empty allowed lists mean any globally enabled item for that method can be used.
- Filled allowed lists restrict that method to exact classnames.

Recommended defaults:
- Storage, lockers, safes: tools and charges enabled.
- Doors: charges enabled, tools disabled.
- BBP/base objects: configure according to your server rules.

---

## BreachingCharge integration

Enable support in `aph_storage.json`:

```json
{
    "EnableRaiding": 1,
    "EnableBreachingChargeSupport": 1,
    "BreachingChargeDestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1,
    "DeleteCodeLockOnSuccessfulRaid": 0
}
```

Allow target classes in `aph_storage_raid_target_control.json`:

```json
{
    "ClassName": "APH_Storage_Openable_Base",
    "AllowRaidTools": 1,
    "AllowBreachingCharge": 1,
    "AllowHMC4": 1,
    "AllowedBreachingCharges": [
        "HDSN_BreachingCharge",
        "HDSN_BreachingChargeHeavy",
        "BreachingCharge",
        "BreachingChargeHeavy"
    ]
}
```

Also add APH classnames to the third-party BreachingCharge config if that mod requires a destroyable object list.

Example:

```json
"DestroyableObjects": {
    "APH_Storage_Openable_Base": "Tier3",
    "APH_Storage_Container_Base": "Tier3",
    "APH_Storage_FloorSafe": "Tier2",
    "APH_Storage_WallSafe": "Tier2",
    "APH_Storage_Blast_Door_Black": "Tier4",
    "APH_Storage_Bunker_Door": "Tier4"
}
```

Recommended BreachingCharge behavior for lock-focused raids:

```json
{
    "OnlyDestroyLocks": 1,
    "DeleteObjectsDirectly": 0,
    "DestroyLocksFirst": 0,
    "PlacementDistance": 2.0
}
```

---

## HM-C4 integration

Enable support:

```json
{
    "EnableRaiding": 1,
    "EnableHMC4Support": 1,
    "HMC4DestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1
}
```

Allow charges on targets:

```json
{
    "ClassName": "APH_Storage_Container_Base",
    "AllowHMC4": 1,
    "AllowedHMC4Charges": [
        "HM_C4",
        "HM_C4_Kit",
        "HMC4"
    ]
}
```

---

## Discord webhooks

### Virtual storage webhook

```json
{
    "EnableVirtualStorageWebhookLogs": 0,
    "VirtualStorageDiscordWebhookURL": "PUT WEBHOOK URL HERE",
    "VirtualStorageWebhookImageURL": "https://i.postimg.cc/TY74fyqF/storage-logs.png",
    "VirtualStorageDiscordWebhookUsername": "APH Storage Logs",
    "VirtualStorageWebhookStoreEvents": 1,
    "VirtualStorageWebhookRestoreEvents": 1,
    "VirtualStorageWebhookAutoStoreEvents": 1,
    "VirtualStorageWebhookErrorEvents": 1,
    "VirtualStorageWebhookIncludePlayerName": 1,
    "VirtualStorageWebhookIncludeSteamID": 1,
    "VirtualStorageWebhookIncludeDateTime": 1,
    "VirtualStorageWebhookIncludeJsonFileName": 1,
    "VirtualStorageWebhookIncludePosition": 1,
    "VirtualStorageWebhookIncludeOperationID": 1,
    "VirtualStorageWebhookIncludeItemAudit": 0
}
```

### Raid webhook

```json
{
    "EnableRaidDiscordWebhookLogs": 0,
    "RaidDiscordWebhookURL": "PUT RAID WEBHOOK URL HERE",
    "RaidDiscordWebhookUsername": "APH Raid Alerts",
    "RaidDiscordWebhookImageURL": "https://i.postimg.cc/ZRwrpNRd/raiding.png",
    "RaidWebhookArmedEvents": 1,
    "RaidWebhookBreachedEvents": 1,
    "RaidWebhookBlockedEvents": 1,
    "RaidWebhookTestEvents": 1,
    "RaidWebhookIncludePlayerName": 1,
    "RaidWebhookIncludeSteamID": 1,
    "RaidWebhookIncludeDateTime": 1,
    "RaidWebhookIncludePosition": 1,
    "RaidWebhookIncludeContainer": 1,
    "RaidWebhookIncludeCharge": 1,
    "RaidWebhookIncludeOperationID": 1
}
```

Replace webhook placeholders before enabling webhook logs.

---

## In-game notifications

```json
{
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

---

## Admin config reload keybind

```json
{
    "EnableStorageConfigReloadKeybind": 1,
    "StorageConfigReloadAdminSteamIDs": [
        "PUT_YOUR_STEAM64_ID_HERE"
    ]
}
```

Replace the placeholder with your Steam64 ID.

---

## Common APH raid classnames

Use these in APH target control or third-party raid configs where needed:

```txt
APH_Storage_Container_Base
APH_Storage_Openable_Base
APH_Storage_Placeable_Base
APH_Storage_FloorSafe
APH_Storage_FloorSafe_Black
APH_Storage_WallSafe
APH_Storage_WallSafe_Black
APH_Storage_LongWallSafe
APH_Storage_LongWallSafe_Black
APH_Storage_ZFA_Safe
ArmouredDoor_ColorBase
Metal_Door_ColorBase
Shutter_Door_ColorBase
Large_Shutter_Door_ColorBase
APH_Storage_Blast_Door_Black
APH_Storage_Bunker_Door
APH_Storage_Caged_Door_Black
APH_Storage_Caged_Door_Green
APH_Storage_Metal_Door_Black
APH_Storage_Shutter_Door_Black
APH_Storage_Large_Shutter_Door_Black
APH_Storage_50_Cal_AmmoBox
APH_Storage_AmmoBox
APH_Storage_AmmoBox_H
APH_Storage_ArmoryRack
APH_Storage_ArmouryCabinet
APH_Storage_ArmouryTable
APH_Storage_Armoury_Gear_Cabinet
APH_Storage_Armoury_Locker
APH_Storage_Armoury_Locker_GB_Black
APH_Storage_Armoury_Modular_Big_GunWall
APH_Storage_Armoury_Modular_GunWall
APH_Storage_Armoury_Modular_Small_GunWall
APH_Storage_Armoury_Modular_Small_Melee_Display
APH_Storage_Armoury_Rack
APH_Storage_Armoury_Rifle_Cabinet
APH_Storage_Armoury_Table
APH_Storage_Armoury_Table_With_GunStnad
APH_Storage_Armoury_Table_With_GunStnads
APH_Storage_Blast_Door
APH_Storage_Bunker_Door_ColorBase
APH_Storage_Celingfan
APH_Storage_Crate_ATKT1
APH_Storage_Crate_ATKT2
APH_Storage_Crate_ATKT3
APH_Storage_Crate_Anarchy
APH_Storage_Crate_Avengers
APH_Storage_Crate_Black2
APH_Storage_Crate_Broly
APH_Storage_Crate_Buu1
APH_Storage_Crate_Buu2
APH_Storage_Crate_Buu3
APH_Storage_Crate_Charzard1
APH_Storage_Crate_Charzard2
APH_Storage_Crate_Charzard3
APH_Storage_Crate_Charzard4
APH_Storage_Crate_Galaxy
APH_Storage_Crate_Gengar1
APH_Storage_Crate_Gengar2
APH_Storage_Crate_Gengar3
APH_Storage_Crate_Gengar4
APH_Storage_Crate_Goku1
APH_Storage_Crate_Goku2
APH_Storage_Crate_Goku3
APH_Storage_Crate_Goku4
APH_Storage_Crate_Green
APH_Storage_Crate_Itachi1
APH_Storage_Crate_MK
APH_Storage_Crate_Mavel
APH_Storage_Crate_Money1
APH_Storage_Crate_Pokemon1st
APH_Storage_Crate_Purple1
APH_Storage_Crate_SmashBros
APH_Storage_Crate_SnakeEyes
APH_Storage_Crate_StormShadow
APH_Storage_Crate_Teal
APH_Storage_Crate_Vegeta
APH_Storage_Data_Crates_APH_Scifi_Crate
APH_Storage_Data_Crates_APH_Scifi_Crate_Modular_4_Tier_Rack
APH_Storage_FreshCrate
APH_Storage_Fridges
APH_Storage_GrenadeBox
APH_Storage_Gun_Cabinet
APH_Storage_Gun_ShowCase
APH_Storage_Industrial_WaterTank
APH_Storage_Large_Shutter_Door
APH_Storage_MLocker
APH_Storage_MLocker_Bio
APH_Storage_MLocker_Boom
APH_Storage_MLocker_Carbon
APH_Storage_MLocker_Graffiti_1
APH_Storage_MLocker_Green
APH_Storage_MLocker_Tempt_Fate
APH_Storage_MedicalCabinet
APH_Storage_Metal_Door
APH_Storage_Modular_BigGunWall
APH_Storage_Modular_GunWall
APH_Storage_Modular_SmallGunWall
APH_Storage_Money_Crate
APH_Storage_Old_Double_Bed
APH_Storage_Old_Sofa
APH_Storage_Retro_Fridge_White
APH_Storage_Rife_Case_2_Black
APH_Storage_Scifi_4_Tier_Rack
APH_Storage_Scifi_Crate_Black
APH_Storage_ShowCase_Gun_Cabinet
APH_Storage_Shutter_Door
APH_Storage_SingleRow_GR
APH_Storage_WeaponCrate_Green
APH_Storage_WeaponCrate_Green_WithDirt
APH_Storage_Weapon_Crate
APH_Storage_Weapon_Crate2
```

---

## Testing checklist

After installing or updating:

```txt
Server starts without script compile errors.
JSON configs load without parse errors.
APH storage places correctly from kits.
Placement wheel action appears.
Storage opens and closes.
CodeLock attaches if enabled.
Storage auto-closes if enabled.
Virtual storage stores and restores contents.
Virtual storage does not restore into a container with live items.
Selected helmet stores into empty Headgear slot.
Selected helmet swaps with occupied Headgear slot.
Selected gloves store into empty Gloves slot.
Selected gloves swap with occupied Gloves slot.
Other clothing slots still store/swap correctly.
Dismantle tools show the configured action style.
Raid tools only show on allowed targets.
BreachingCharge/HM-C4 only work on allowed targets.
Raid schedule blocks/allows raiding at the correct time.
Discord webhook sends only when enabled and URL is valid.
```

---

## Troubleshooting

### Server will not compile

Check the latest crash log for the exact file and line.

Common causes:
- Wrong script file version copied.
- Old patched file mixed with newer config class.
- Missing dependency mod.
- Function called on the wrong DayZ class.
- Packed PBO not rebuilt after replacing scripts.

### Headgear or Gloves still will not store

Check:
- Both client and server are running the updated PBO.
- The tested storage object has valid `Headgear` and `Gloves` attachment slots.
- The item itself has valid `inventorySlot` config.
- No other mod is overriding inventory movement for the same object.
- Test empty slot first, then occupied slot.
- Remove old cached/generated files only if your server workflow requires it.

### Dismantle tool uses the wrong animation

Check:
- Tool classname exists in `DismantleTools`.
- Tool classname has an entry in `DismantleToolActions`.
- The action string is one of the supported values.
- Your existing `aph_storage.json` was updated; old generated configs will not always auto-add new sections unless regenerated or manually edited.

### Breaching charge will not place

Check:
- `EnableBreachingChargeSupport` is `1`.
- The target allows BreachingCharge in `aph_storage_raid_target_control.json`.
- The charge classname is allowed or the allowed list is empty.
- The APH classname is added to the third-party BreachingCharge config if required.
- The object has a CodeLock if your raid rules require lock-only raids.

### Virtual storage does not restore

Check:
- Container class is in `VirtualStorageEnabledContainers`.
- Container class is not in `VirtualStorageExcludedContainers`.
- The container does not already contain live items.
- The virtual storage file exists.
- Logs are enabled while debugging.

---

## Updating from an older version

1. Back up your old PBO and profile configs.
2. Replace the updated script files.
3. Rebuild and repack the PBO.
4. Start a test server first.
5. Compare your old `aph_storage.json` with the current config.
6. Manually add missing sections such as `DismantleToolActions`.
7. Test selected Headgear and Gloves storage/swap.
8. Test dismantling tool actions.
9. Test raiding and virtual storage before pushing live.

---

## Recommended GitHub structure

```txt
APH_Havoc_Storage/
├── Scripts/
├── Config_Examples/
│   ├── aph_storage.json
│   ├── aph_raid_schedule.json
│   └── aph_storage_raid_target_control.json
├── docs/
│   └── assets/
├── patches/
├── README.md
└── CHANGELOG.md
```

---

## Changelog

### V5
- Fixed selected `Headgear` not storing/swapping.
- Fixed selected `Gloves` not storing/swapping.
- Added direct `InventoryLocation` movement for sensitive gear slots.
- Preserved normal storage/swap behavior for other clothing slots.
- Kept configurable dismantle tool action support.
- Kept placement wheel/action support.

### V4
- Fixed compile error caused by invalid `EntityAI.ConfigGetText` use.
- Replaced item config reads with `GetGame().ConfigGetText` / `GetGame().ConfigGetTextArray`.

### V3
- Improved held-item swap logic.
- Removed dead-end occupied-slot return path.
- Added slot fallback handling.

### V2
- Split dismantle action handling into fixed action variants.
- Added better config fallback for dismantle tool actions.

### V1
- Added first Headgear support pass.
- Added initial configurable dismantle tool actions.

---

## Credits

Created for APH Havoc Storage.

Maintained with DayZ modding support from APH MISFITNO1.
