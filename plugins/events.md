# Events

Listen for an event with `server.on(name, handler)`. A plugin may register any number of handlers, for the same event or different ones; they run in the order they were registered, plugin by plugin in name order.

```lua
server.on("block_break", function(event)
	log.info(`{event.player.name} broke {event.block}`)
end)
```

Unknown event names are an error, so a typo stops the plugin from loading.

| Event | Cancellable | When |
|---|---|---|
| [`player_join`](#player_join) | yes | A player finished loading into the world |
| [`player_quit`](#player_quit) | yes | A player who joined left |
| [`player_chat`](#player_chat) | yes | A player sent a chat message |
| [`player_damage`](#player_damage) | yes | A player is about to take damage |
| [`player_death`](#player_death) | no | A player died |
| [`player_respawn`](#player_respawn) | no | A dead player respawned |
| [`block_break`](#block_break) | no | A player broke a block |
| [`block_place`](#block_place) | no | A player placed a block |

Event tables are read-only. Players in them are [player tables](players.md), whose methods keep working after the event.

## Cancelling

Cancellable events have `event.cancel()` and `event.is_cancelled()`. Every handler still runs after one cancels, so later handlers can check. The server waits up to 2 seconds for the plugins to decide; past that, the event goes ahead.

## `player_join`

`{ player, cancel(), is_cancelled() }`

A player has finished loading into the world (they're past the loading screen). Unless a handler cancels the event, everyone then sees vanilla's yellow "Steve joined the game", in their own language.

Cancel it to send your own message instead:

```lua
server.on("player_join", function(event)
	event.cancel()
	server.broadcast(`§a+ {event.player.name}`)
end)
```

From this event on, `server.player(uuid)` finds the player.

## `player_quit`

`{ player, cancel(), is_cancelled() }`

A player whose join plugins heard of has left, however they left: they quit, lost their connection, were kicked, or logged in somewhere else. Unless a handler cancels the event, everyone sees vanilla's "Steve left the game".

```lua
server.on("player_quit", function(event)
	event.cancel()
	server.broadcast(`§c- {event.player.name}`)
end)
```

The player can still be found with `server.player(uuid)` until the handlers have run, but they're no longer in the game: messages to them go nowhere.

## `player_chat`

`{ player, message, cancel(), is_cancelled() }`

A player sent a chat message; `message` is what they typed, trimmed. Cancelling it means nobody sees it.

```lua
server.on("player_chat", function(event)
	if string.find(string.lower(event.message), "badword", 1, true) then
		event.cancel()
		event.player.send_message("§cYour message was blocked.")
	end
end)
```

## `player_damage`

`{ player, cause, amount, health, cancel(), is_cancelled() }`

A player is about to take `amount` damage (a point is half a heart). `health` is their health before it. `cause` is a [damage cause](players.md#damage-causes) such as `fall` or `void`. Cancelling it spares them.

Players who can't be hurt in their game mode (creative, spectator) never get here, except for the causes `selfDestruct` (`/kill`) and `override`.

```lua
-- No fall damage near spawn.
server.on("player_damage", function(event)
	if event.cause == "fall" then
		event.cancel()
	end
end)
```

## `player_death`

`{ player, cause, message }`

A player died. `cause` is the [damage cause](players.md#damage-causes) of what killed them, and `message` is the death message in English, such as "Steve hit the ground too hard". Players see the message in their own language unless the `showdeathmessages` game rule is off.

## `player_respawn`

The player.

A dead player pressed Respawn and is back at the world spawn with full health.

## `block_break`

`{ player, position = { x, y, z }, block }`

A player broke a block. `block` is the name of the block that was there, such as `minecraft:stone`. It has already happened and can't be cancelled yet.

## `block_place`

`{ player, position = { x, y, z }, block }`

A player placed a block; `block` is its name. It has already happened and can't be cancelled yet.
