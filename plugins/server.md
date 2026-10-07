# The server table

Every plugin has a global `server` table.

## `server.on(event, handler)`

Calls `handler` with the event's table whenever `event` happens. See [Events](events.md).

## `server.broadcast(message)`

Shows a message in every player's chat. Empty messages are an error. Broadcasts also show in the console while `logs.chat` is on.

```lua
server.broadcast("§6The server restarts in 5 minutes.")
```

## `server.player(uuid)`

The online player with that UUID (in any case) as a [player table](players.md), or `nil` if they aren't online. A player can be found from their [`player_join`](events.md#player_join) until their [`player_quit`](events.md#player_quit) handlers have run.

```lua
local newest = nil
server.on("player_join", function(event)
	newest = event.player.uuid
end)

-- later
local player = if newest then server.player(newest) else nil
if player then
	player.send_message("You were the last to join!")
end
```

## `server.command(definition)`

Adds a slash command. See [Slash commands](commands.md).

## `print(...)` and `log`

Write to the server console, labelled with the plugin's name. See [Output](README.md#output).
