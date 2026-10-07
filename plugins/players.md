# Players

Events hand you players as read-only tables, and `server.player(uuid)` finds one who is online:

| Field | What it is |
|---|---|
| `name` | The name shown in game. Players can change it, so don't use it to remember them. |
| `uuid` | The player's persistent identity, a UUID string that stays the same across sessions and name changes. Use it to remember players. |

```lua
local seen = {}
server.on("player_join", function(event)
	local player = event.player
	if seen[player.uuid] then
		player.send_message("Welcome back!")
	end
	seen[player.uuid] = true
end)
```

## Methods

Methods work with `.` and `:` alike (`player.kick()` or `player:kick()`), and keep working after the event that gave you the player. Acting on a player who has left does nothing.

They're requests to the server, carried out right after: a change doesn't show in the same handler.

### `send_message(message)`

Shows a message in that player's chat only. Empty messages are an error.

```lua
player.send_message("§7Only you can see this.")
```

### `set_game_mode(mode)`

Changes their game mode: `survival`, `creative`, `adventure`, `spectator`, `default` (the server's default), or `s`, `c`, `a`, `d`. Anything else is an error.

### `set_health(health)`

Sets their health, from 0 to 20 (a point is half a heart). 0 kills them, with the damage cause `override`. Works in every game mode; it does nothing to a dead player.

### `damage(amount, cause?)`

Hurts them by `amount` (above 0), as `cause` would. `cause` is one of the [damage causes](#damage-causes) below, `none` if you leave it out; others are an error.

It works like any other damage: creative players and spectators are spared (except for `selfDestruct` and `override`), and [`player_damage`](events.md#player_damage) handlers may cancel it.

```lua
player.damage(4, "magic")
```

### `kick(reason?)`

Disconnects them, showing `reason`, or "You were kicked from the server." without one.

## Damage causes

Damage causes are vanilla's, named as Minecraft's Script API names them (`EntityDamageCause`):

`anvil`, `blockExplosion`, `campfire`, `contact`, `drowning`, `entityAttack`, `entityExplosion`, `fall`, `fallingBlock`, `fire`, `fireTick`, `fireworks`, `flyIntoWall`, `freezing`, `lava`, `lightning`, `maceSmash`, `magic`, `magma`, `none`, `override`, `piston`, `projectile`, `ramAttack`, `selfDestruct`, `sonicBoom`, `soulCampfire`, `stalactite`, `stalagmite`, `starve`, `suffocation`, `temperature`, `thorns`, `void`, `wither`

The server deals `fall`, `void`, `selfDestruct` (`/kill`) and `override` itself so far. Plugins can deal any of them.
