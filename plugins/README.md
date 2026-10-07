# Plugins

Plugins are scripts written in [Luau](https://luau.org), Roblox's fast, typed dialect of Lua. There's nothing to compile: save a file and the server reloads the plugin while it runs.

## A first plugin

Each plugin is a folder in `server/plugins/` with a `plugin.json` and an entry script:

```text
server/plugins/
  greeter/
    plugin.json
    main.luau
```

`plugin.json`:

```json
{
  "name": "greeter",
  "description": "Greets players",
  "version": "1.0.0",
  "author": "You",
  "main": "main.luau"
}
```

`main.luau`:

```lua
server.on("player_join", function(event)
	event.player.send_message(`§aWelcome, {event.player.name}!`)
end)
```

Start the server (or save the file while it runs) and the console shows:

```text
INF [Plugins] Loaded plugin: greeter (1.0.0, You)
```

Anything the script prints while it loads follows that line. The script runs once when the plugin loads. Most plugins use that run to listen for [events](events.md) and add [slash commands](commands.md).

## `plugin.json`

| Field | What it is |
|---|---|
| `name` | Unique among the server's plugins: 1 to 64 letters, digits, `-` and `_` |
| `description` | Shown when the plugin loads |
| `version` | Any non-empty text, such as `1.0.0` |
| `author` | Who wrote it |
| `main` | The entry script, a `.luau` file inside the plugin's folder |

All five are required, and unknown fields are errors, so typos don't go unnoticed.

## Hot reload

The server watches `server/plugins/`. When you save, add or remove a file, it waits a moment for the editor to finish, then:

- **loads** new plugin folders,
- **reloads** plugins whose files changed, in a fresh VM: event handlers and commands from the old version are gone, and the new version's take their place,
- **unloads** plugins whose folder or `plugin.json` was removed.

A plugin whose new version fails to load (a syntax error, a mistake in a command definition, an error while its script runs) **keeps running its previous version**, and the console says why.

## The sandbox

Each plugin runs in its own Luau VM, on a thread of its own, away from the game:

- Plugins don't share globals; the standard libraries and the server's tables are read-only.
- A plugin may use up to **64 MiB** of memory.
- Each call into a plugin (loading it, an event handler, a command) may run for at most **1 second**; past that it's stopped with an error. Event handlers and commands that wait on a plugin for longer than 2 seconds go ahead without it.
- There's no access to files or the network, beyond [requiring](#splitting-a-plugin-across-files) scripts in the plugin's own folder.

## Splitting a plugin across files

`require` loads other scripts from the plugin's folder, with Luau's require-by-string paths:

```text
server/plugins/warps/
  plugin.json
  main.luau
  storage.luau
  commands/
    init.luau
    admin.luau
```

```lua
-- main.luau
local storage = require("./storage")    -- storage.luau
local commands = require("./commands")  -- commands/init.luau
```

- Paths start with `./` (next to the requiring script) or `../` (up a folder). A script may end in `.luau` or `.lua`, which you leave out.
- A folder can be required as a module through its `init.luau`. Inside it, `./x` is next to the folder, and `@self/x` is inside it: `require("@self/admin")` in `commands/init.luau` loads `commands/admin.luau`.
- Nothing outside the plugin's folder can be required, not even through `../` or a link pointing out of it. `.luaurc` files and their aliases are ignored.
- A module runs once per plugin: requiring it again, from anywhere in the plugin, returns what it returned the first time. That's how scripts share state.
- Modules count towards the plugin's 1-second limit and memory, like the rest of its code.
- Saving any script in the folder reloads the plugin, not just its entry script.

## Output

`print(...)` and `log.info(...)` write to the server's console, labelled with the plugin's name. There are `log.trace`, `log.debug`, `log.info`, `log.warn` and `log.error`; `print` is `log.info`.

```lua
log.warn("running low on", 3, "things")
```

```text
WRN [greeter] running low on	3	things
```

## Colours

Text sent to players can use Minecraft's `§` formatting codes: `§a` green, `§c` red, `§e` yellow, `§7` grey, `§l` bold, `§r` reset, and so on. The console shows them as colours too.

## Next

- [Events](events.md): everything plugins can listen for
- [Players](players.md): what you can do with a player
- [The server table](server.md)
- [Slash commands](commands.md)

The sample plugin in [`server/plugins/hello`](https://github.com/BedrockRS/bedrock-rs/tree/main/server/plugins/hello) shows most of the API.
