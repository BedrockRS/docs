# Configuration

## The `server/` folder

Everything the server reads or writes while it runs lives in the folder it runs in, `server/`:

| Path | What it is |
|---|---|
| `bedrockrs.toml` | The configuration, created with every setting at its default on first start |
| `ops.json` | The [operators](#operators), created by the first `op` |
| `plugins/` | [Plugins](plugins/README.md), one folder each |
| `worlds/world/` | The world: changed chunks, players, and `game_rules.json` |
| `keys/identity.pem` | The server's NetherNet identity key, created on first start. Keep it private. |

Only `plugins/`, the start scripts and `server/README.md` are part of the repository; the rest is yours.

## `bedrockrs.toml`

Settings left out keep their default, and unknown settings or wrong types stop the server with the line at fault. Restart the server after editing.

```toml
[logs]
# Show player chat, plugin broadcasts, private plugin messages and chat that
# plugins cancelled in the console.
chat = true
# Show routine internal activity: connections, logins, chunk streaming and
# saves, refused actions, and the libraries the server is built on.
system_noise = false

[players]
# The game mode of players joining for the first time: survival, creative,
# adventure or spectator. Operators change anyone's with /gamemode.
default_game_mode = "creative"
```

| Setting | Default | What it does |
|---|---|---|
| `logs.chat` | `true` | Shows chat, broadcasts and death messages in the console |
| `logs.system_noise` | `false` | Shows routine activity, for finding problems |
| `players.default_game_mode` | `"creative"` | The game mode of new players. `default` in `/gamemode` means this mode. |

When the `RUST_LOG` environment variable is set, it replaces the `[logs]` settings. `NO_COLOR` turns the console's colours off.

## Game rules

Game rules belong to the world, not the configuration: change them in game with [`/gamerule`](commands.md#gamerule). They're saved in `worlds/world/game_rules.json`. See [Gameplay](gameplay.md#game-rules) for the rules and what they do.

## Operators

Operators may run operator commands, such as `/gamemode`, `/gamerule`, `/kill` and `/stop`. The console makes the first one with `op <name>`; after that, operators can use `/op` and `/deop` in game. A player must be online to be made an operator.

`ops.json` lists them by their persistent UUID, which stays the same when they change their name:

```json
[
  {
    "uuid": "174319cc-f69f-30d8-a279-6ace57f2011e",
    "name": "Steve"
  }
]
```

The name is only there for people reading the file. Editing the file while the server runs has no effect until it restarts; use `/op` and `/deop` instead.

## Environment variables

Network settings, and a few for testing, are environment variables for now:

| Variable | Default | What it does |
|---|---|---|
| `BEDROCKRS_SIGNALING_ADDR` | `0.0.0.0:19132` | TCP address clients first connect to |
| `BEDROCKRS_MEDIA_PORT` | `19133` | UDP port for game traffic |
| `BEDROCKRS_MEDIA_IPS` | every IPv4 interface that is up | Local addresses for game traffic, comma-separated |
| `BEDROCKRS_ADVERTISE_IPS` | none | Public addresses offered to clients, comma-separated; needed behind NAT |
| `BEDROCKRS_ICE_LITE` | `true` | `false` switches from ICE-lite to full ICE |
| `BEDROCKRS_WORLD_DIR` | `worlds/world` | Where the world is saved |
| `BEDROCKRS_AUTHENTICATION` | `true` | `false` stops checking players' Microsoft accounts. **Offline testing only**: anyone can then join as anyone. |
