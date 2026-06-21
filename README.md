# APH Havoc Storage Setup Guide

Updated for the current APH Storage config set.

APH Havoc Storage is a DayZ storage and security system for server owners. It adds lockable storage, doors, safes, armoury furniture, gun walls, ammo boxes, medical/fridge storage logic, virtual storage performance handling, CodeLock support, raid tools, breaching charge / HM-C4 integration, Discord webhooks, in-game notifications, and admin reload support.

---

## What this mod does

APH Storage gives servers a configurable storage framework instead of just static containers.

Main features:

- Lockable storage using CodeLock support.
- APH doors, safes, lockers, weapon racks, gun walls, crates, ammo boxes, fridges, medical cabinets, and armoury furniture.
- Virtual storage system that can store items outside the live container to reduce server load.
- Auto close and auto store timers for open storage.
- Raid tools support using configured vanilla/custom tools.
- Breaching Charge and HM-C4 support.
- Per-target raid control so each storage/door class can allow tools, charges, HM-C4, or none.
- Raid schedule control for server raid days.
- CodeLock destruction support on successful raid.
- Option to open storage automatically after explosive raid.
- Discord webhook logs for storage and raids.
- In-game APH notifications for storage and raid events.
- Kit crafting and de-crafting options.
- Gun bench repair system.
- Ammo box, fridge, and medical cabinet item whitelists.
- Admin config reload keybind support.

---

## Config file locations

After first server start, the APH configs are expected in your server profile/config area.

Use these files:

```text
aph_storage.json
aph_raid_schedule.json
aph_storage_raid_target_control.json
```

Third-party raiding mods such as BreachingCharge use their own config file. APH classes must be added to that third-party config if the third-party mod requires a destroyable object list.

Example third-party file used in this guide:

```text
breachingcharge.json
```

---

## Quick setup

1. Install APH Havoc Storage on the server and client.
2. Install required dependencies used by your server pack, for example CodeLock if you want locked storage.
3. Start the server once so default JSON files generate.
4. Stop the server.
5. Edit `aph_storage.json` for main APH settings.
6. Edit `aph_raid_schedule.json` if you want raid days/times.
7. Edit `aph_storage_raid_target_control.json` to choose which storage/door classes are raidable and by what method.
8. If using BreachingCharge, HM-C4, or another raid mod, add APH classnames into that mod’s own destroyable/raid target config.
9. Restart the server.
10. Test with a CodeLocked APH storage item and the raid method you enabled.

---

## Important main settings in `aph_storage.json`

These are the core switches server owners normally change first.

```json
{
    "EnableCodeLockSupport": 1,
    "EnableCodeLockAttachment": 1,
    "EnableRaiding": 1,
    "EnableVanillaToolRaiding": 1,
    "EnableBreachingChargeSupport": 0,
    "EnableHMC4Support": 0,
    "BreachingChargeDestroysCodeLock": 1,
    "HMC4DestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1,
    "DeleteCodeLockOnSuccessfulRaid": 0,
    "DisableContainerDamageWhenRaidingDisabled": 1,
    "ProxyMode": 1,
    "AutoCloseOnServerStart": 1,
    "EnableAutoCloseStorageTimer": 1,
    "AutoCloseMinutes": 5,
    "OpenCloseRange": 2.0,
    "EnableVirtualContainerStorage": 1,
    "EnableAutoStoreAfterOpenTimeout": 1,
    "AutoStoreTimeoutSeconds": 60,
    "EnableAutoStoreNonOpenableContainers": 1,
    "EnableAutoStoreCodeLockContainers": 1,
    "AutoStoreWhileLocked": 1,
    "AutoStoreWhileClosed": 1,
    "EnableAPHNotificationSystem": 1,
    "EnableVirtualStorageWebhookLogs": 0,
    "EnableRaidDiscordWebhookLogs": 0,
    "EnableRaidInGameMessages": 1,
    "EnableStorageConfigReloadKeybind": 1
}
```

### Recommended basic raiding setup

To allow APH raid tools and explosive integrations, enable the core raid system first:

```json
{
    "EnableRaiding": 1,
    "EnableVanillaToolRaiding": 1,
    "EnableBreachingChargeSupport": 1,
    "EnableHMC4Support": 1,
    "BreachingChargeDestroysCodeLock": 1,
    "HMC4DestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1,
    "DeleteCodeLockOnSuccessfulRaid": 0
}
```

`DeleteCodeLockOnSuccessfulRaid` should stay `0` if you want the CodeLock to be dropped/preserved instead of deleted.

---

## Raid tools

The configured raid tools are:

```json
[
    "SledgeHammer",
    "Crowbar",
    "FirefighterAxe",
    "FirefighterAxe_Black",
    "FirefighterAxe_Green",
    "Hatchet",
    "Hacksaw"
]
```

Tool raid settings:

```json
{
    "RaidDamagePerAction": 10,
    "RaidActionTimeSeconds": 600,
    "ToolDamageOnRaid": 25
}
```

Only tools listed in `RaidTools` should trigger APH raid actions. If a custom tool is added by another mod, add its exact classname to this list.

---

## Dismantle tools

Dismantle tools are separate from raid tools.

```json
[
    "Screwdriver",
    "Pliers",
    "Hammer"
]
```

Use this list only for normal dismantle/de-craft actions. Do not use it as the raid tool list unless you want those tools to raid too.

---

## Virtual storage system

Virtual storage is used to improve performance by storing container contents virtually when the storage is closed, idle, or timed out depending on your settings.

Current virtual storage settings:

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

### Virtual storage enabled containers

```json
[
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

### Virtual storage excluded containers

These are ignored by APH virtual storage:

```json
[
    "LB_LC_Base",
    "LB_Airdrop_Car_Base",
    "LB_Airdrop_Base",
    "zm_WorkbenchPublic"
]
```

Add third-party containers here if APH should not virtual-store them.

---

## Whitelist storage logic

### Ammo boxes

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

### Fridges

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

### Medical cabinets

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

## Gun bench repair system

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

This allows selected APH gun bench classes to repair weapons using the configured required parts.

---

## Crafting and de-crafting kits

```json
{
    "CanCraftKits": 1,
    "CraftKitToolTime": 20,
    "CraftKitRecipeOne": "Nail",
    "CraftKitRecipeOneQty": 20,
    "CraftKitRecipeTwo": "WoodenPlank",
    "CraftKitRecipeTwoQty": 4,
    "CanDeCraftKits": 1,
    "DeCraftKitText": "De-Craft Kit",
    "DeCraftKitTool": "Screwdriver",
    "DeCraftKitToolTime": 10,
    "DeCraftKitToolDamage": 10
}
```

---

## Discord webhooks and in-game notifications

### APH notification system

```json
{
    "EnableAPHNotificationSystem": 1,
    "APHStorageNotificationTitle": "APH Storage",
    "APHRaidNotificationTitle": "APH Raid Alert",
    "APHStorageNotificationIcon": "APH_Storage/Scripts/Data/Icons/storage_logs.paa",
    "APHRaidNotificationIcon": "APH_Storage/Scripts/Data/Icons/raiding.paa",
    "APHNotificationDurationSeconds": 8
}
```

### Virtual storage webhooks

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

Set `EnableVirtualStorageWebhookLogs` to `1` and replace `VirtualStorageDiscordWebhookURL` with your Discord webhook URL.

### Raid webhooks

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
    "RaidWebhookIncludeOperationID": 1,
    "EnableRaidInGameMessages": 1,
    "RaidArmedMessage": "[APH RAID ALERT] A breaching charge has been armed.",
    "RaidBreachedMessage": "[APH RAID ALERT] Storage has been breached.",
    "RaidBlockedMessage": "[APH RAID ALERT] Raiding is not allowed right now."
}
```

Set `EnableRaidDiscordWebhookLogs` to `1` and replace `RaidDiscordWebhookURL` with your Discord raid webhook URL.

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

Add your Steam64 ID to `StorageConfigReloadAdminSteamIDs`.

---

## Raid schedule setup: `aph_raid_schedule.json`

Current file:

```json
{
    "EnableRaidSchedule": 0,
    "AllowMonday": 1,
    "AllowTuesday": 1,
    "AllowWednesday": 1,
    "AllowThursday": 1,
    "AllowFriday": 1,
    "AllowSaturday": 1,
    "AllowSunday": 1,
    "TimeModeNote": "EnableRaidSchedule false means raiding is allowed every day. Turn it on and set allowed days for raid weekends/custom raid days."
}
```

### How it works

- `EnableRaidSchedule: 0` means raiding is allowed every day.
- `EnableRaidSchedule: 1` means APH checks the allowed day flags.
- Set each day to `1` to allow raiding on that day.
- Set each day to `0` to block raiding on that day.

Example raid weekend setup:

```json
{
    "EnableRaidSchedule": 1,
    "AllowMonday": 0,
    "AllowTuesday": 0,
    "AllowWednesday": 0,
    "AllowThursday": 0,
    "AllowFriday": 1,
    "AllowSaturday": 1,
    "AllowSunday": 1,
    "TimeModeNote": "Friday, Saturday and Sunday raid days enabled."
}
```

---

## Per-target raid control: `aph_storage_raid_target_control.json`

This file decides what can raid each APH class.

Current global settings:

```json
{
    "EnableRaidTargetControl": 1,
    "DefaultAllowRaidTools": 0,
    "DefaultAllowBreachingCharge": 0,
    "DefaultAllowHMC4": 0,
    "Note": "Per-target raid control. ClassName supports inheritance via IsKindOf. Leave AllowedRaidTools/AllowedBreachingCharges/AllowedHMC4Charges empty to allow any globally enabled tool/charge for that raid method. Doors default to charges only; lockers, safes, and storage default to tools and charges."
}
```

### How target control works

Each entry uses:

```json
{
    "ClassName": "APH_Storage_Container_Base",
    "AllowRaidTools": 1,
    "AllowBreachingCharge": 1,
    "AllowHMC4": 1,
    "AllowedRaidTools": [],
    "AllowedBreachingCharges": [],
    "AllowedHMC4Charges": []
}
```

Rules:

- `ClassName` can be a base class or exact class.
- APH uses inheritance-style matching where supported, so base classes such as `APH_Storage_Container_Base` can cover child storage classes.
- `AllowRaidTools: 1` allows configured raid tools.
- `AllowBreachingCharge: 1` allows configured breaching charges.
- `AllowHMC4: 1` allows configured HM-C4 charges.
- Empty allowed lists mean any globally enabled item for that method can be used.
- Filled allowed lists mean only those exact tool/charge classes can be used.

### Default behaviour in the supplied target config

- General APH storage base classes allow raid tools, BreachingCharge and HM-C4.
- Safes allow raid tools, BreachingCharge and HM-C4.
- APH doors are set to charges only by default: raid tools disabled, BreachingCharge/HM-C4 enabled.

---

## APH classes for raid mods

Use these classes when another raiding mod needs APH objects added to its own config.

```text
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

## Third-party BreachingCharge setup

The uploaded BreachingCharge example uses this structure:

```json
{
    "CreateLogs": 1,
    "Charges": [ ... ],
    "Tiers": [ ... ],
    "DestroyableObjects": {
        "ClassNameHere": "Tier1"
    }
}
```

### Charge setup notes

Your example charge entries include:

```json
[
    {
        "Classname": "HDSN_BreachingCharge",
        "DamageToObjects": 0,
        "DamageToDestroyableObjectsRadius": 0.0,
        "VerticalDistanceModeObjects": 0,
        "MaxVerticalDistanceObjects": 0.0,
        "MaxDamageToPlayers": 100.0,
        "MinDamageToPlayers": 50.0,
        "DamageToPlayersRadius": 5.0,
        "MaxDamageToPlayersRadius": 2.0,
        "MaxVerticalDistancePlayers": 0.0,
        "OnlyDestroyLocks": 1,
        "DeleteObjectsDirectly": 0,
        "DestroyLocksFirst": 0,
        "PlacementDistance": 2.0,
        "ToolDamageOnDefuse": 10.0,
        "DestroyOtherCharges": 0,
        "TimeToPlant": 30.0,
        "TimeToExplode": 180.0,
        "TimeToDefuse": 20.0,
        "LightBrightness": 0.5,
        "LightRadius": 10.0,
        "LightColorStart": [
            0.0,
            1.0,
            0.0
        ],
        "LightColorHalfway": [
            1.0,
            1.0,
            0.0
        ],
        "LightColorEnd": [
            1.0,
            0.0,
            0.0
        ],
        "BeepingSoundSet": "HDSN_SoundSet_Beeping",
        "BeepingSoundEndTime": 2.0,
        "SwitchInterval": 2.0,
        "ExplosionSoundSet": "Landmine_Explosion_SoundSet",
        "DefuseTools": [
            "Unarmed"
        ]
    },
    {
        "Classname": "HDSN_BreachingChargeHeavy",
        "DamageToObjects": 2,
        "DamageToDestroyableObjectsRadius": 0.0,
        "MaxDamageToPlayers": 100.0,
        "MinDamageToPlayers": 50.0,
        "DamageToPlayersRadius": 5.0,
        "MaxDamageToPlayersRadius": 2.0,
        "OnlyDestroyLocks": 1,
        "DeleteObjectsDirectly": 0,
        "DestroyLocksFirst": 0,
        "PlacementDistance": 2.0,
        "ToolDamageOnDefuse": 10.0,
        "TimeToPlant": 30.0,
        "TimeToExplode": 180.0,
        "TimeToDefuse": 20.0,
        "LightBrightness": 0.5,
        "LightRadius": 10.0,
        "LightColorStart": [
            0.0,
            1.0,
            0.0
        ],
        "LightColorHalfway": [
            1.0,
            1.0,
            0.0
        ],
        "LightColorEnd": [
            1.0,
            0.0,
            0.0
        ],
        "BeepingSoundSet": "HDSN_SoundSet_Beeping",
        "BeepingSoundEndTime": 2.0,
        "SwitchInterval": 2.0,
        "ExplosionSoundSet": "Landmine_Explosion_SoundSet",
        "DefuseTools": [
            "Unarmed"
        ]
    }
]
```

Recommended APH behaviour for charge-only lock raiding:

```json
{
    "OnlyDestroyLocks": 1,
    "DeleteObjectsDirectly": 0,
    "DestroyLocksFirst": 0,
    "PlacementDistance": 2.0
}
```

This keeps the raid focused on locks instead of deleting the whole APH storage object.

### Tiers

Your example uses these tiers:

```json
[
    {
        "Name": "Tier1",
        "Health": 2,
        "AcceptedChargeTypes": [
            "HDSN_BreachingCharge",
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier2",
        "Health": 2,
        "AcceptedChargeTypes": [
            "HDSN_BreachingCharge",
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier3",
        "Health": 4,
        "AcceptedChargeTypes": [
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier4",
        "Health": 6,
        "AcceptedChargeTypes": [
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier5",
        "Health": 8,
        "AcceptedChargeTypes": [
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier6",
        "Health": 10,
        "AcceptedChargeTypes": [
            "HDSN_BreachingChargeHeavy"
        ]
    },
    {
        "Name": "Tier7",
        "Health": 12,
        "AcceptedChargeTypes": [
            "HDSN_BreachingChargeHeavy"
        ]
    }
]
```

### APH entries inside `DestroyableObjects`

Your example already includes APH classes like this:

```json
{
    "APH_Storage_Openable_Base": "Tier3",
    "ArmouredDoor_ColorBase": "Tier3",
    "Metal_Door_ColorBase": "Tier3",
    "Shutter_Door_ColorBase": "Tier3",
    "Large_Shutter_Door_ColorBase": "Tier2",
    "APH_Storage_Blast_Door_Black": "Tier4",
    "APH_Storage_Bunker_Door": "Tier4",
    "APH_Storage_Caged_Door_Black": "Tier3",
    "APH_Storage_Caged_Door_Green": "Tier3",
    "APH_Storage_Metal_Door_Black": "Tier3",
    "APH_Storage_Shutter_Door_Black": "Tier3",
    "APH_Storage_Large_Shutter_Door_Black": "Tier3",
    "APH_Storage_FloorSafe": "Tier2",
    "APH_Storage_FloorSafe_Black": "Tier2",
    "APH_Storage_WallSafe": "Tier2",
    "APH_Storage_WallSafe_Black": "Tier2",
    "APH_Storage_LongWallSafe": "Tier2",
    "APH_Storage_LongWallSafe_Black": "Tier2",
    "APH_Storage_ZFA_Safe": "Tier4",
    "APH_Storage_50_Cal_AmmoBox": "Tier2",
    "APH_Storage_AmmoBox": "Tier2",
    "APH_Storage_AmmoBox_H": "Tier2",
    "APH_Storage_ArmoryRack": "Tier2",
    "APH_Storage_ArmouryCabinet": "Tier2",
    "APH_Storage_ArmouryTable": "Tier2",
    "APH_Storage_Armoury_Gear_Cabinet": "Tier2",
    "APH_Storage_Armoury_Locker": "Tier2",
    "APH_Storage_Armoury_Locker_GB_Black": "Tier2",
    "APH_Storage_Armoury_Modular_Big_GunWall": "Tier2",
    "APH_Storage_Armoury_Modular_GunWall": "Tier2",
    "APH_Storage_Armoury_Modular_Small_GunWall": "Tier2",
    "APH_Storage_Armoury_Modular_Small_Melee_Display": "Tier2",
    "APH_Storage_Armoury_Rack": "Tier2",
    "APH_Storage_Armoury_Rifle_Cabinet": "Tier2",
    "APH_Storage_Armoury_Table": "Tier2",
    "APH_Storage_Armoury_Table_With_GunStnad": "Tier2",
    "APH_Storage_Armoury_Table_With_GunStnads": "Tier2",
    "APH_Storage_Blast_Door": "Tier4",
    "APH_Storage_Bunker_Door_ColorBase": "Tier4",
    "APH_Storage_Celingfan": "Tier2",
    "APH_Storage_Crate_ATKT1": "Tier2",
    "APH_Storage_Crate_ATKT2": "Tier2",
    "APH_Storage_Crate_ATKT3": "Tier2",
    "APH_Storage_Crate_Anarchy": "Tier2",
    "APH_Storage_Crate_Avengers": "Tier2",
    "APH_Storage_Crate_Black2": "Tier2",
    "APH_Storage_Crate_Broly": "Tier2",
    "APH_Storage_Crate_Buu1": "Tier2",
    "APH_Storage_Crate_Buu2": "Tier2",
    "APH_Storage_Crate_Buu3": "Tier2",
    "APH_Storage_Crate_Charzard1": "Tier2",
    "APH_Storage_Crate_Charzard2": "Tier2",
    "APH_Storage_Crate_Charzard3": "Tier2",
    "APH_Storage_Crate_Charzard4": "Tier2",
    "APH_Storage_Crate_Galaxy": "Tier2",
    "APH_Storage_Crate_Gengar1": "Tier2",
    "APH_Storage_Crate_Gengar2": "Tier2",
    "APH_Storage_Crate_Gengar3": "Tier2",
    "APH_Storage_Crate_Gengar4": "Tier2",
    "APH_Storage_Crate_Goku1": "Tier2",
    "APH_Storage_Crate_Goku2": "Tier2",
    "APH_Storage_Crate_Goku3": "Tier2",
    "APH_Storage_Crate_Goku4": "Tier2",
    "APH_Storage_Crate_Green": "Tier2",
    "APH_Storage_Crate_Itachi1": "Tier2",
    "APH_Storage_Crate_MK": "Tier2",
    "APH_Storage_Crate_Mavel": "Tier2",
    "APH_Storage_Crate_Money1": "Tier2",
    "APH_Storage_Crate_Pokemon1st": "Tier2",
    "APH_Storage_Crate_Purple1": "Tier2",
    "APH_Storage_Crate_SmashBros": "Tier2",
    "APH_Storage_Crate_SnakeEyes": "Tier2",
    "APH_Storage_Crate_StormShadow": "Tier2",
    "APH_Storage_Crate_Teal": "Tier2",
    "APH_Storage_Crate_Vegeta": "Tier2",
    "APH_Storage_Data_Crates_APH_Scifi_Crate": "Tier2",
    "APH_Storage_Data_Crates_APH_Scifi_Crate_Modular_4_Tier_Rack": "Tier2",
    "APH_Storage_FreshCrate": "Tier2",
    "APH_Storage_Fridges": "Tier2",
    "APH_Storage_GrenadeBox": "Tier2",
    "APH_Storage_Gun_Cabinet": "Tier2",
    "APH_Storage_Gun_ShowCase": "Tier2",
    "APH_Storage_Industrial_WaterTank": "Tier2",
    "APH_Storage_Large_Shutter_Door": "Tier2",
    "APH_Storage_MLocker": "Tier2",
    "APH_Storage_MLocker_Bio": "Tier2",
    "APH_Storage_MLocker_Boom": "Tier2",
    "APH_Storage_MLocker_Carbon": "Tier2",
    "APH_Storage_MLocker_Graffiti_1": "Tier2",
    "APH_Storage_MLocker_Green": "Tier2",
    "APH_Storage_MLocker_Tempt_Fate": "Tier2",
    "APH_Storage_MedicalCabinet": "Tier2",
    "APH_Storage_Metal_Door": "Tier2",
    "APH_Storage_Modular_BigGunWall": "Tier2",
    "APH_Storage_Modular_GunWall": "Tier2",
    "APH_Storage_Modular_SmallGunWall": "Tier2",
    "APH_Storage_Money_Crate": "Tier2",
    "APH_Storage_Old_Double_Bed": "Tier2",
    "APH_Storage_Old_Sofa": "Tier2",
    "APH_Storage_Retro_Fridge_White": "Tier2",
    "APH_Storage_Rife_Case_2_Black": "Tier2",
    "APH_Storage_Scifi_4_Tier_Rack": "Tier2",
    "APH_Storage_Scifi_Crate_Black": "Tier2",
    "APH_Storage_ShowCase_Gun_Cabinet": "Tier2",
    "APH_Storage_Shutter_Door": "Tier2",
    "APH_Storage_SingleRow_GR": "Tier2",
    "APH_Storage_WeaponCrate_Green": "Tier2",
    "APH_Storage_WeaponCrate_Green_WithDirt": "Tier2",
    "APH_Storage_Weapon_Crate": "Tier2",
    "APH_Storage_Weapon_Crate2": "Tier2"
}
```

### Minimal BreachingCharge APH example

Copy this style into `DestroyableObjects` if your BreachingCharge config is missing APH storage:

```json
{
    "APH_Storage_Openable_Base": "Tier3",
    "APH_Storage_Container_Base": "Tier3",
    "ArmouredDoor_ColorBase": "Tier3",
    "Metal_Door_ColorBase": "Tier3",
    "Shutter_Door_ColorBase": "Tier3",
    "Large_Shutter_Door_ColorBase": "Tier2",
    "APH_Storage_Blast_Door_Black": "Tier4",
    "APH_Storage_Bunker_Door": "Tier4",
    "APH_Storage_Caged_Door_Black": "Tier3",
    "APH_Storage_Caged_Door_Green": "Tier3",
    "APH_Storage_Metal_Door_Black": "Tier3",
    "APH_Storage_Shutter_Door_Black": "Tier3",
    "APH_Storage_Large_Shutter_Door_Black": "Tier3",
    "APH_Storage_FloorSafe": "Tier2",
    "APH_Storage_FloorSafe_Black": "Tier2",
    "APH_Storage_WallSafe": "Tier2",
    "APH_Storage_WallSafe_Black": "Tier2",
    "APH_Storage_LongWallSafe": "Tier2",
    "APH_Storage_LongWallSafe_Black": "Tier2",
    "APH_Storage_ZFA_Safe": "Tier4"
}
```

### Important for other raid mods

For any other raiding mod, the same rule applies:

1. Enable the raid method in `aph_storage.json`.
2. Allow the target class in `aph_storage_raid_target_control.json`.
3. Add APH classnames to the third-party raid mod config if that mod needs its own object list.
4. Use APH base classes where the third-party mod supports inherited/base class matching.
5. If the third-party mod only supports exact classnames, add every APH child class you want raidable.

---

## Recommended APH + BreachingCharge setup

In `aph_storage.json`:

```json
{
    "EnableRaiding": 1,
    "EnableBreachingChargeSupport": 1,
    "BreachingChargeDestroysCodeLock": 1,
    "OpenStorageAfterExplosiveRaid": 1,
    "DeleteCodeLockOnSuccessfulRaid": 0,
    "EnableRaidDiscordWebhookLogs": 1,
    "EnableRaidInGameMessages": 1
}
```

In `aph_storage_raid_target_control.json`, make sure the target allows the charge:

```json
{
    "ClassName": "APH_Storage_Container_Base",
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

In `breachingcharge.json`, add the APH target under `DestroyableObjects`:

```json
{
    "APH_Storage_Openable_Base": "Tier3",
    "APH_Storage_FloorSafe": "Tier2",
    "APH_Storage_Blast_Door_Black": "Tier4"
}
```

---

## Testing checklist

Use this checklist after changing raid configs:

- Server starts without JSON errors.
- `aph_storage.json` has the raid method enabled.
- `aph_storage_raid_target_control.json` allows the storage class and method.
- Third-party raid mod config contains APH classes.
- Target storage has a CodeLock attached if your raid rule requires CodeLock raiding.
- Breaching charge places but does not instantly open the door/storage.
- On successful raid, CodeLock is removed/dropped according to APH settings.
- Door/storage opens after explosive raid if `OpenStorageAfterExplosiveRaid` is enabled.
- Raid armed and breached messages appear in Discord/in-game if enabled.
- Non-raidable classes do not show raid actions.

---

## Troubleshooting

### Breaching charge will not place

Check:

- `EnableBreachingChargeSupport` is `1` in `aph_storage.json`.
- `AllowBreachingCharge` is `1` for that target in `aph_storage_raid_target_control.json`.
- The charge classname is listed in `AllowedBreachingCharges` or the list is empty.
- The APH class is added to the third-party BreachingCharge `DestroyableObjects` list.

### Raid tools show on the wrong item

Check:

- `EnableVanillaToolRaiding` is enabled.
- `AllowRaidTools` is correct for that class.
- `DefaultAllowRaidTools` is not allowing everything by accident.
- Tool classname is only added to `RaidTools` if you want it to raid.

### Storage does not restore items

Check:

- `EnableVirtualContainerStorage` is enabled.
- The class is inside `VirtualStorageEnabledContainers`.
- The class is not inside `VirtualStorageExcludedContainers`.
- The container does not already have live items when restore runs, because restore can be blocked to avoid duplication.

### Discord webhook does not send

Check:

- The correct webhook toggle is enabled.
- The webhook URL is not still set to `PUT WEBHOOK URL HERE`.
- Your Discord webhook URL is valid.
- Server firewall allows outgoing webhook requests.

---

## Server owner notes

- Keep backups of all APH JSON files before editing.
- Change one feature at a time, restart, and test.
- Use exact classnames.
- For third-party mods, APH can only work correctly if that mod also knows APH is a valid raid target.
- Use base classes for easier setup only when the third-party mod supports base/inheritance matching.
- Use exact child classnames when the third-party mod does not support inheritance.

---

## Current supplied config files covered by this guide

- `aph_storage.json`
- `aph_raid_schedule.json`
- `aph_storage_raid_target_control.json`
- `breachingcharge.json` example

