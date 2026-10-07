# Gameplay

BedrockRS aims to play like vanilla Bedrock. This page covers what it does so far and where it still differs.

## Game modes

Each player has their own game mode, saved with them. New players get `default_game_mode` from [`bedrockrs.toml`](configuration.md#bedrockrstoml); operators change anyone's with [`/gamemode`](commands.md#gamemode).

| | Survival | Creative | Adventure | Spectator |
|---|---|---|---|---|
| Break and place blocks | yes | yes, instantly | no | no |
| Placing uses up the block | yes | no | | |
| Broken blocks drop their item | yes | no | | |
| Creative inventory | no | yes | no | no |
| Fly | no | yes | no | always, through blocks |
| Takes damage | yes | only from `/kill` | yes | only from `/kill` |
| Picks up items | yes | yes | yes | no |
| Seen by other players | yes | yes | yes | no |

Breaking time isn't checked yet: survival players break a block when their client says it's broken.

## Health and damage

Players have 20 points of health (ten hearts), saved with them. What hurts them so far:

- **Falling:** the fall height minus 3 blocks, rounded up, as in vanilla. Jumps don't hurt; flying, or going up, starts the count over.
- **The void:** below y = -64, 4 damage every half second.
- **`/kill`**, and plugins.

The world is always on **peaceful** difficulty for now, where players regain one point of health every second. Hunger, difficulties, combat and armour are still to come.

## Death and respawning

When a player dies:

- They see the death screen, and everyone sees the vanilla death message, such as "Steve hit the ground too hard", in their own language.
- Other players see them fall over before they disappear.
- Everything they carry drops where they died, unless `keepinventory` is on.

Pressing **Respawn** brings them back at the world spawn with full health. A player who leaves while dead comes back at the spawn next time.

## Join and quit messages

When a player has finished loading into the world, everyone sees vanilla's "Steve joined the game"; when they leave, "Steve left the game". Plugins can [replace these](plugins/events.md#player_join).

## Game rules

Game rules are set with [`/gamerule`](commands.md#gamerule) and saved with the world. BedrockRS supports these so far:

| Rule | Default | What it does |
|---|---|---|
| `falldamage` | `true` | Whether players take fall damage |
| `keepinventory` | `false` | Whether players keep what they carry when they die |
| `naturalregeneration` | `true` | Whether players regain health over time |
| `showcoordinates` | `true` | Whether players see their coordinates. Vanilla's default is `false`. |
| `showdeathmessages` | `true` | Whether everyone is told when a player dies |
