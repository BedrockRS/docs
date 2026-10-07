# Slash commands

Plugins add slash commands with `server.command(definition)`. They're real commands: players get them in `/help` and autocompleted as they type, subcommands included, and the server checks every argument before your code runs, answering mistakes with the right usage.

```lua
server.command({
	name = "warp",
	description = "Travel between warps",
	aliases = { "w" },
	-- /warp <name>
	args = { { name = "name", type = "string" } },
	run = function(ctx)
		ctx.reply(`Warping to {ctx.args.name}...`)
	end,
	subcommands = {
		-- /warp set <name> [public]
		set = {
			description = "Make a warp where you stand",
			args = {
				{ name = "name", type = "string" },
				{ name = "public", type = "bool", optional = true },
			},
			run = function(ctx)
				if not ctx.sender then
					ctx.error("Only players can set warps.")
					return
				end
				ctx.reply(`Set warp {ctx.args.name}.`)
			end,
		},
		-- /warp admin reload, for operators only
		admin = {
			permission = "operator",
			subcommands = {
				reload = {
					description = "Reload the warps",
					run = function(ctx)
						return "Warps reloaded."
					end,
				},
			},
		},
	},
})
```

Players then see:

```text
/warp <name: string>
/warp set <name: string> [public: Boolean]
/warp admin reload        (operators only)
```

## The definition

| Field | Required | What it is |
|---|---|---|
| `name` | yes | The command's name, without `/`: lower case letters, digits, `_` and `-` |
| `description` | no | Shown in `/help` and while typing |
| `aliases` | no | Other names that run the same command |
| `permission` | no | `"any"` (the default) or `"operator"` |
| `args` | no | The arguments of `/name` itself; needs `run` |
| `run` | no | What runs for `/name` itself |
| `subcommands` | no | Subcommands by name |

A command needs `run`, `subcommands`, or both.

### Subcommands

Each entry of `subcommands` maps a name (same rules as command names) to a table with `description`, `permission`, `args`, `run` and `subcommands`. Subcommands nest as deep as you like, and each may run, lead to more subcommands, or both.

- A subcommand's **permission** can only narrow its parent's: under an `"operator"` subcommand, everything is for operators.
- When a word could be both a subcommand and an argument, the **subcommand wins**.
- Subcommands are listed alphabetically.
- A command or subcommand that only leads to subcommands answers `/warp admin` with the subcommands it has.

### Arguments

Each argument is a table: `{ name = "...", type = "...", optional = true }`.

| Type | Accepts | `ctx.args` gets |
|---|---|---|
| `string` (default) | one word, or a quoted string with spaces | a string |
| `text` | the rest of the line, as typed; must be last | a string |
| `int` | a whole number | a number |
| `number` | any number | a number |
| `bool` | `true` or `false` | a boolean |
| `player` | an online player's name, or `@s` | a [player table](players.md) |
| `gamemode` | vanilla's game mode names: `survival`, `creative`, `adventure`, `spectator`, `default`, `s`, `c`, `a`, `d` | the name typed |
| `enum` | one of `values`, in any case | the value, as listed |

```lua
{ name = "colour", type = "enum", values = { "red", "green", "blue" } }
```

An `enum` shows its name as the argument's type, as in `<colour: colour>`; set `enum = "Colour"` to show another.

Optional arguments come after the required ones. A left-out optional argument is `nil` in `ctx.args`.

## The handler

`run` receives a `ctx` table:

| Field | What it is |
|---|---|
| `sender` | The [player](players.md) who ran it, or `nil` for the server console |
| `console` | `true` when the console ran it |
| `command` | The command's name, whichever alias was typed |
| `path` | The subcommands typed, such as `{ "admin", "reload" }` |
| `args` | The arguments, by name |
| `reply(message)` | Answers in white |
| `error(message)` | Answers in red |

A string the handler returns is a reply too. Answers go to the player's chat, or to the console. `ctx.reply` and `ctx.error` work with `.` and `:` alike.

If the handler raises an error, the player sees "An error occurred while running this command." and the console gets the details.

Replies made after the handler returned (by keeping `ctx` for a later event) reach the player as a chat message.

## Checked when the plugin loads

A definition with a mistake stops the plugin from loading, with the reason in the console, so a misspelled `subcomands`, an unknown argument type or a required argument after an optional one never reaches players. Registering the same command name twice in one plugin is an error too.

## Names already taken

The server's own commands come first, then plugins in name order. A command whose name is already taken isn't added, and an alias that's taken is dropped; the console warns either way.

## Hot reload

Commands follow their plugin: reloading it replaces them, unloading it removes them, and every player's autocompletion updates.
