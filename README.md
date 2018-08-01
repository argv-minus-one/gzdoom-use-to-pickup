# Use To Pickup

This mod changes how players pick up items. Instead of picking them up by touching them, one must aim at the item and press the Use button to pick it up. Now you won't accidentally pick up that blue armor when yours is only down to 198%!

Also, this mod shows neat-looking brackets around the item, and its name.

## Usage

Just load this into GZDoom with your favorite mods. It's not mod- or game-specific.

It works with multiplayer in theory, though I haven't had a chance to test that.

There will probably be conflicts with some other mods. Be sure to open an issue (or, better yet, a PR) if you find one.

## Options

This mod adds a submenu to the bottom of the Options menu. There are several settings:

### Allow full Use range

In vanilla, you can only pick up an item that you're directly touching. This mod *can* work the same way, but it's irritating to have to stand on the item and look almost straight down at it to pick it up. If this option is on (which it is by default), you can instead pick up items at up to your use range. The “use range” is the distance at which you can activate objects (switches, doors, etc).

But there's a problem. Some maps may place an item just outside of reach until you're supposed to get it—but *not* outside of use range! As a result, this mod may let you effectively cheat on those maps by getting items before you should. This option is provided so that you can decide whether to allow this in your game.

This is a server-side option. If in a multiplayer game, the setting of the server/host is applied to all players.

### Item scan frequency

Controls how frequently this mod will scan each player's surroundings for items that can be picked up. Normally, it scans on every tic, but setting this to a value other than 1 will stagger out the scans to happen every 2 tics, every 3 tics, etc.

The reason for this setting is performance. Scanning takes a fair bit of CPU time, and this overhead is multiplied by the number of players in the game. In single-player, the default setting of 1 should be fine, but in a large multiplayer game, it will likely slow down the game.

### Enable Mod

Toggles on/off the functionality of this mod.

This is a client-side option. In multiplayer games, players may independently decide whether they want to use this, or use the vanilla pickup behavior.

### Item bracket color & item bracket opacity

When an item is highlighted by this mod, brackets are drawn around it, and a textual label is drawn under it. These control the color and opacity of the brackets and label.

Opacity is given as a value between 0 and 1, where 0 is completely invisible and 1 is completely opaque. For example, setting this to 0.5 will result in the brackets and label being only 50% opaque.

### Fade-in time & fade-out time

When you look at an item, the brackets and labels around it will fade in for a moment. Similarly, when you look away, the brackets and labels will fade out. These options control how long, in seconds, the fade in/out animation lasts.

### Delay before allowing pickup

When you look at an item, there's a slight delay before you can actually pick it up. That's to help protect you from accidentally picking up the wrong item. This lets you adjust how long that delay is, in seconds. Set to 0 to turn this feature off.

### Toggle Enabled

A key binding for toggling the mod on/off. This also appears in the “Customize Controls” menu.

### Push Item Away

If there are a whole bunch of items clustered together, and you can't find the one you want among all the clutter, you can solve the problem the same way as you would in real life: by pushing aside the clutter! Bind this key to do that.

## For Modders

By default, this mod looks at the `Tag` property of actors to decide on their name, and applies use-to-pickup behavior to all actors that can be picked up. This behavior can be customized on a per-class basis by including a `UseToPickup.ini` file in your mod, with the replacement settings. Take a look at [the one that comes with this mod](UseToPickup.ini) for an example of how this looks.

The following keys are recognized:

* `AlwaysTouchPickup`
	
	If yes, this item will always be picked up by touching it, even if this mod is enabled.

* `Tag`
	
	Default name of the item, shown under the brackets. Used if the item's `Tag` property is absent.

* `UsePickupMessageAsTag`
	
	If yes, and if an item has no `Tag` but does have a `PickupMessage`, then use that.

* `ForceTag`
	
	If yes, always use the `Tag` in the INI as the displayed name of the item.

* `UseSpriteDimensions`
	
	Normally, this mod will attempt to figure out the real dimensions of affected items by examining their sprites. If this is set to no, that will not happen for affected actors; their normal `Radius` and `Height` properties will be used, and `ZOffset` will be assumed to be zero. Default is yes.
	
	This mod is not able to detect the correct dimensions of models or voxels. It is also not able to detect whether a given actor has a model or voxel representation. For this reason, `UseSpriteDimensions` must be explicitly turned off for such actors.

* `Radius` & `Height`
	
	If provided, these set the radius and height of affected items for the purposes of positioning brackets around them. If both are provided, `UseSpriteDimensions=no` is implied.
	
	In vanilla, the actor properties `Radius` and `Height` are not usually correct for inventory item classes, because nothing can collide with them anyway. However, this mod *does* need those values to be correct. It can figure out the correct values by looking at their sprites, but won't give correct results for actors represented by voxels or models. These two INI settings allow you to supply the correct values explicitly, without changing the actual actor classes.

* `ZOffset`
	
	If provided, this sets how far off the ground affected items float, for the purposes of positioning brackets around them. This will override sprite-based detection of that value.
	
	Many games have collectable items that appear to float above the ground, rather than lying on the ground, such as Doom's Soulsphere. This floating is not represented by any actor property. As far as the engine is concerned, they are lying on the ground, not floating in the air.
	
	As with `Radius` and `Height`, this mod does need that information, and can examine sprites to determine the correct value, but that won't work for non-sprite actors. For those actors, this INI setting needs to be provided instead.

The INI format is defined by [the INIFile library](https://github.com/argv-minus-one/gzdoom-zscript-utils/tree/master/INIFile). Sections are merged as specified in the documentation for that library, [under “Merging Data by Actor Class”](https://github.com/argv-minus-one/gzdoom-zscript-utils/blob/master/INIFile/README.md#user-content-merging-data-by-actor-class).

## Credits

Much smarter people than me made this possible. They include:

* Graf Zahl and crew wrote the engine that this runs on.
* Gutawer's [Gutamatics](https://gitlab.com/Gutawer/gzdoom-gutamatics/) is used for projecting where the brackets should appear on the screen.
* Nash [showed me how to do that](https://forum.zdoom.org/viewtopic.php?f=122&t=61330#p1064117).
