# Getting started

## What you need

- [Rust](https://rustup.rs) 1.93 or newer
- Git
- Minecraft: Bedrock Edition **26.51** to join (other versions are turned away)

## Build and start

```bash
git clone https://github.com/BedrockRS/bedrock-rs.git
cd bedrock-rs
```

Start the server with the script in the `server/` folder:

- **Windows:** double-click `server/start.bat`
- **Linux/macOS:** run `server/start.sh`

The first start compiles the server, which takes a few minutes. The scripts run it inside `server/`, where it keeps its configuration, worlds, plugins and keys. You can also start it yourself from that folder:

```bash
cd server
cargo run --release
```

The server finds its files relative to the folder it runs in, so always start it from `server/`.

When it's ready, the console shows:

```text
INF [bedrockrs] NetherNet listening signaling=0.0.0.0:19132 ...
INF [bedrockrs] type help in the console for a list of commands
```

## Join

In Minecraft, add a server with your computer's IP address and port **19132**. On the same computer, use `127.0.0.1`.

BedrockRS uses NetherNet, the transport Bedrock dedicated servers use since 1.26.50: the client connects over TCP port 19132 first, then UDP port 19133. Players joining from elsewhere need both ports reachable; see [Configuration](configuration.md#environment-variables) for the addresses offered to them.

## Make yourself an operator

Operators can use commands such as `/gamemode` and `/gamerule`. Once you're in the game, type this into the server console:

```text
op YourName
```

From then on, you can make others operators with `/op` in game. See [Commands](commands.md).

## Stop the server

Type `stop` in the console (or `/stop` in game as an operator), or press `Ctrl+C`. The world and everyone's progress are saved.
