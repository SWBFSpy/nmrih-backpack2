# Fork Info
This fork includes a few adjustments to nmrih-backpack2.sp for version 2.0.18

line 593: int fullCapacity = RoundToNearest(reg.capacity * cvAmmoMultiplier.FloatValue) * 2; // allows 8 ammo boxes instead of 4 per slot

line 980: cvAmmoMultiplier = AutoExecConfig_CreateConVar("sm_backpack_ammo_stack_limit", "8", // only works if line 593 is adjusted too

line 986: cvBackpackColorize = AutoExecConfig_CreateConVar("sm_backpack_colorize", "0", // optional, if used with 1 bag only spawn, the bag will always glow green

line 996: cvNpcBackpackChance = AutoExecConfig_CreateConVar("sm_backpack_zombie_spawn_chance", "0", // setting to 0 only works if line 1343 is adjusted too

line 1343: if (1 <= cvNpcBackpackChance.FloatValue) // change "rnd" to "1" to allow only 1 backpack per round, required to disable zombie loot spawns

# [NMRiH] Backpack 2
This is a complete rewrite of [Backpack by Ryan](https://forums.alliedmods.net/showthread.php?t=308217) with added features and bug fixes.

It adds portable inventory boxes to the game. They function just like normal inventory boxes except they also pick up items that are dropped on top of them. Punching one will place it on your back and allow you to carry it around the level.

Backpacks are able to store more ammo than a regular inventory box. By default backpacks store up to 4 boxes worth of ammo per slot. This means one slot holds 4 barricade boards or 40 rounds of 9mm or 80 rounds of .22.

Backpacks are randomly colored to help distinguish them.

## Video
https://user-images.githubusercontent.com/11559683/142296315-f216a804-92b9-4bff-bbfd-e247b86dc9d1.mp4

# Requirements
- A web server to serve custom content (FastDL)
- [SourceMod](https://www.sourcemod.net/downloads.php?branch=dev) (1.12.6998 or higher is recommended for improved performance)

# Installation:
- Grab the latest ZIP files from [releases](https://github.com/dysphie/nmrih-backpack2/releases)
- Extract `nmrih-backpack-X.Y.Z.zip` into the server's `nmrih/addons/sourcemod` directory
- Extract `nmrih-backpack-assets.zip` into your game server and your FastDL server (they're automatically added to the downloads table)
	
## Using backpacks
- Punch backpack to wear it
- Press drop weapon key when fists are equipped to drop it
- Press use on a dropped backpack to access its inventory
- Drop items on top of the backpack to store them inside

## What's new in 2.0

- NMRiH 1.12.1 support
- No gamedata dependency, the plugin should be less prone to breaking on new updates
- DHooks is no longer required
- Zombies can spawn with backpacks. The backpacks can contain loot.
- Configurable placement overrides, dictactes which columns items can end up in
- Backpacks can be made to blink instead of glow
- Less edicts. Backpacks consume 1 entity when created instead of 2 or 3
- No conflicts with NMS. Backpacks no longer prevent supply choppers from spawning
- Toggleable screen hints for carrying/using backpacks
- Fixed items colliding with the player when dropped


Some old features are currently unavailable, though I plan on adding them

- Speed penalties for backpack carrier
- Backpack settings: `weight`, `admin_can_use`, `admin_can_wear`, `zombie_can_wear`, `colorize`
- Admin menu

## Backpack template options

You can configure backpack types, behavior and appareance in `addons/sourcemod/configs/backpack2.cfg`.

- `itembox_model` - Model to use on dropped backpacks.
- `ornament_model`  - Model to render on the player's back
- `sounds` - Sound effects used by this backpack, see config for examples
- `max_left [0-8]`, `max_middle [0-4]`, `max_right [0-8]` - Size limit for each column of the backpack

	Example: 
	```cpp
	"Giraffe Plushie"
	{
		"ornament_model" "models/survival/item_dufflebag_backpack.mdl"
		
		// Custom physics model
		"itembox_model" "models/anxiety/giraffe.mdl"
	
		// Play Pugman's bleeding sounds
		"sounds"
		{
			"backpack_open"
			{
				"player/bleed/pugman/bleeding02.wav" "1"
				"player/bleed/pugman/bleeding03.wav" "1"
				"player/bleed/pugman/bleeding04.wav" "1"
			}
		}
		
		// Half the capacity of a normal backpack
		"max_left" "4"
		"max_middle" "2"
		"max_right" "4"
	}
	```

## Item configs

Keys not mentioned here should be left as-is unless you know what you're doing, as they're required for the plugin to function properly.
- `loot` `yes/no` - Whether this item can be spawned as random loot for backpacks dropped by zombies
- `capacity` - Allows you to override the maximum capacity for a given ammo box. This affects random loot and ammo stacking.

	For example:
	```cpp
	// Make backpacks able to hold (20 * sm_backpack_ammo_stack_limit) boards per slot
	"ammobox_board"
	{
		"capacity"		"20"
		...
	}
	````

- `columns` - This allows you to override the placement for a given item. Valid values are `left`, `middle` and `right`, and they can be combined to allow for multiple columns.

	For example: 
	```cpp
	// Move pills to the ammo column
	"item_pills"
	{
		"id" "44"
		"columns" "right"
	}

	// Allow hammer to overflow into the "gear" column if the "weapon" column is full
	"me_sledge"
	{
		"id" "40"
		"columns" "left middle"
	}

	// Prevent maglite from being added to backpacks
	"item_maglite"
	{
		"id" "42"
		"columns" ""
	}
	```
## Cvars

Configuration variables are saved to `cfg/sourcemod/plugin.backpack2.cfg`

- `sm_backpack_count` - Number of backpacks to give out on round restart. Won't create more backpacks than there are players.
- `sm_backpack_ammo_stack_limit` - Number of ammo boxes of a type that can be stored per ammo slot.
- `sm_backpack_show_hints` `0/1` - Show screen hints about backpack usage
- `sm_backpack_colorize` - Randomly colorize backpacks to help distinguish them.
- `sm_backpack_glow` `0/1/2` - Highlight dropped backpacks. 0 = Don't, 1 = Outline glow, 2 = Pulsing brightness
- `sm_backpack_glow_blip` `0/1` - Whether glowing backpacks show up in player compasses, if applicable
- `sm_backpack_glow_distance` - Distance at which glowing backpacks stop glowing, if applicable
- `sm_backpack_zombie_chance` `[0.0-1.0]` - Chance for a zombie to spawn with a backpack. 0 means never, 1.0 means 100%. For reference, crawler chance is 0.02
- `sm_backpack_zombie_ammo_min` `[0-8]` - Minimum items to place in the 'Ammo' column for backpacks spawned as loot
- `sm_backpack_zombie_ammo_max` `[0-8]` - Maximum items to place in the 'Ammo' column for backpacks spawned as loot
- `sm_backpack_zombie_gear_min` `[0-4]` - Minimum items to place in the 'Gear' column for backpacks spawned as loot
- `sm_backpack_zombie_gear_max` `[0-4]` - Maximum items to place in the 'Gear' column for backpacks spawned as loot
- `sm_backpack_zombie_weapon_min` `[0-8]` - Minimum items to place in the 'Weapon' column for backpacks spawned as loot
- `sm_backpack_zombie_weapon_max` `[0-8]` - Maximum items to place in the 'Weapon' column for backpacks spawned as loot
- `sm_backpack_zombie_ammo_min_pct` `[0-100]` - Minimum ammo fill percentage for weapons and ammo boxes spawned as backpack loot
- `sm_backpack_zombie_ammo_max_pct` `[0-100]` - Maximum ammo fill percentage for weapons and ammo boxes spawned as backpack loot

## Admin Commands

- `sm_backpack <userid|#name>` - Gives target a backpack, requires ADMFLAG_CHEATS
