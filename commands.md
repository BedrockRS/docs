# Commands

Commands work as in vanilla: type `/` in chat to see the ones you may use, with their arguments autocompleted. Players only see and run what they're allowed to; operator commands need an [operator](configuration.md#operators).

## The server console

Anything typed into the server's console runs as a command, with or without the `/`, with every permission. What it prints goes to the console. Commands that act on "you" by default, such as `gamemode` or `kill`, need a player named when run from the console.

## Built-in commands

| Command | Who | What it does |
|---|---|---|
| `/help [command]` | everyone | Lists the commands you can use, or explains one |
| `/list` | everyone | Lists the players online |
| `/version` | everyone | Shows the server's version |
| `/gamemode <gameMode> [player]` | operators | Sets a game mode |
| `/gamerule [rule] [value]` | operators | Lists, shows or sets game rules |
| `/kill [target]` | operators | Kills a player |
| `/op <player>` | operators | Makes an online player an operator |
| `/deop <player>` | operators | Takes away a player's operator status |
| `/stop` | operators | Saves everything and stops the server |

Players are named as they appear in game, in any case; names with spaces go in double quotes, such as `"Cool Guy"`. `@s` means whoever runs the command. Other selectors (`@a`, `@p`, …) aren't supported yet.

Commands that plugins add appear alongside these. When a plugin's command has the same name as a built-in one, the built-in one wins.

### `/gamemode`

```text
/gamemode <gameMode: GameMode> [player: target]
/gamemode <gameMode: int> [player: target]
```

As in vanilla, the mode is `survival`, `creative`, `adventure`, `spectator` or `default` (the server's [default game mode](configuration.md#bedrockrstoml)), or their short forms `s`, `c`, `a` and `d`, or the numbers `0` (survival), `1` (creative) and `2` (adventure). Without a player, it changes your own.

### `/gamerule`

```text
/gamerule
/gamerule <rule: BoolGameRule> [value: Boolean]
```

On its own it lists every rule. With a rule, it shows its value; with a value, it sets it for everyone at once and saves it with the world. See [Gameplay](gameplay.md#game-rules) for the rules BedrockRS supports so far; the others vanilla lists are answered with "not supported yet".

### `/kill`

```text
/kill [target: target]
```

Kills a player in any game mode (yourself, without a target). Plugins hear of it as damage with the cause `selfDestruct`, and may cancel it.
