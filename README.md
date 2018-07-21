# Use To Pickup

This mod changes how players pick up items. Instead of picking them up by touching them, one must aim at the item and press the Use button to pick it up. Now you won't accidentally pick up that blue armor when yours is only down to 198%!

Also, this mod shows neat-looking brackets around the item, and its name.

## Usage

Just load this into GZDoom with your favorite mods. This should work with (almost?) everything.

It works with multiplayer in theory, though I haven't had a chance to test that.

## Options

This mod adds a submenu to the bottom of the Options menu. There are two settings:

### Allow full Use range

In vanilla, you can only pick up an item that you're directly touching. This mod *can* work the same way, but it's irritating to have to stand on the item and look almost straight down at it to pick it up. If this option is on (which it is by default), you can instead pick up items at up to your use range. The “use range” is the distance at which you can activate objects (switches, doors, etc).

But there's a problem. Some maps may place an item just outside of reach until you're supposed to get it—but *not* outside of use range! As a result, this mod may let you effectively cheat on those maps by getting items before you should. This option is provided so that you can decide whether to allow this in your game.

This is a server-side option. If in a multiplayer game, the setting of the server/host is applied to all players.

### Enable Mod

Toggles on/off the functionality of this mod.

This is a client-side option. In multiplayer games, players may independently decide whether they want to use this, or use the vanilla pickup behavior.

## Credits

Much smarter people than me made this possible. They include:

* Graf Zahl and crew wrote the engine that this runs on.
* Gutawer's [Gutamatics](https://gitlab.com/Gutawer/gzdoom-gutamatics/) is used for projecting where the brackets should appear on the screen.
* Nash [showed me how to do that](https://forum.zdoom.org/viewtopic.php?f=122&t=61330#p1064117).
