# BedrockRS documentation

Guides and reference for **BedrockRS**, the Minecraft: Bedrock Edition server written in Rust by Mistvale Studios.

> [!NOTE]
> BedrockRS is in early development, and so is this documentation. It describes the `main` branch of [bedrock-rs](https://github.com/BedrockRS/bedrock-rs): Minecraft: Bedrock Edition 26.51, protocol 2193.

## Running a server

- [Getting started](getting-started.md): build the server, start it and join it
- [Configuration](configuration.md): the `server/` folder, `bedrockrs.toml`, environment variables and operators
- [Commands](commands.md): the built-in commands and the server console
- [Gameplay](gameplay.md): game modes, health, damage, death and game rules

## Writing plugins

- [Plugins](plugins/README.md): what a plugin is, its `plugin.json`, hot reload and limits
- [Events](plugins/events.md): reacting to what happens in the game, and cancelling it
- [Players](plugins/players.md): what you can read and do with a player
- [The server table](plugins/server.md): `server.on`, `server.broadcast`, `server.player`, logging
- [Slash commands](plugins/commands.md): commands with subcommands and typed arguments

## Contributing

Found something wrong or missing? Open an issue or a pull request here. For the server itself, see [bedrock-rs](https://github.com/BedrockRS/bedrock-rs) and its contributing guide.

BedrockRS is not affiliated with Mojang Studios or Microsoft.
