# Pluxora Example Plugins

Example plugin packages for Pluxora. Use this repository to learn the plugin layout, command format, event permissions, plugin-local dependencies, config files, storage, and dashboard components.

Add the `pluxora-package` GitHub topic to this repository if you want it to appear in Pluxora GitHub Discovery.

These plugins are intentionally small and readable. They are good references for plugin authors, but the music example is not meant to replace a full Lavalink music plugin.

## Plugin Inventory

| Plugin | Enabled by default | Dependencies | Demonstrates |
| --- | --- | --- | --- |
| `command-example` | Yes | None | Basic commands, aliases, slash options, user permissions, config reads, dashboard component. |
| `utility-example` | Yes | `fs-extra` | Discord events, auto-moderation, XP storage, config writes, dashboard metrics. |
| `music-example` | No | `@discordjs/voice`, `ffmpeg-static`, `play-dl` | Simple voice connection, queue, YouTube/Spotify lookup, plugin-local media dependencies. |

## `command-example`

Small command-focused plugin for the most common command patterns.

### Commands

| Command | Alias | What it does |
| --- | --- | --- |
| `/hello [name]` | `/hi` | Sends a configurable greeting. |
| `/echo <text>` |  | Echoes text back. Requires the user to have `ManageMessages`. |

### Config

```json
{
  "greeting": "Hello",
  "allowEcho": true,
  "echoPrefix": "Echo"
}
```

### What To Copy From It

- Command object structure.
- Slash option definitions.
- Prefix aliases.
- Cooldowns.
- Per-command user permission checks.
- A minimal dashboard component using `dashboard.getComponent(ctx)`.

## `utility-example`

Utility plugin showing how to combine commands, events, plugin storage, and runtime config updates.

### Features

- Auto-moderation for blocked words.
- Optional deletion of blocked messages.
- XP gain on messages with a per-user cooldown.
- JSON storage under the plugin data directory.
- Dashboard component showing how many guilds/users have XP data.

### Commands

| Command | Alias | What it does |
| --- | --- | --- |
| `/level [user]` | `/rank` | Shows XP, level, and message count for a user. |
| `/xp-top` |  | Shows the top 10 XP users in the guild. |
| `/automod <action> [word]` |  | Adds, removes, lists, enables, or disables blocked words. Requires `ManageGuild`. |

### Event Permission

This plugin listens to `messageCreate`, so its manifest includes:

```json
"permissions": [
  "discord.commands",
  "discord.events.messageCreate",
  "dashboard.components",
  "storage"
]
```

The bot must also have message-related Discord intents if you want text-message behavior in production.

### Config

```json
{
  "automod": {
    "enabled": true,
    "blockedWords": ["badword"],
    "deleteMessage": true,
    "warnMessage": "That message was blocked by auto-moderation."
  },
  "xp": {
    "enabled": true,
    "minPerMessage": 5,
    "maxPerMessage": 14,
    "cooldownMs": 60000
  }
}
```

### What To Copy From It

- `ctx.storagePath` usage.
- Reading and writing plugin-owned data.
- Event-driven behavior with permission manifest checks.
- Editing plugin config from a command.
- Dashboard metrics generated from plugin data.

## `music-example`

Minimal music plugin using Discord voice directly. It is useful for understanding voice plugin structure, but for real production playback prefer a Lavalink plugin such as `music-rainlink`.

### Features

- Joins the user's voice channel.
- Resolves YouTube search terms and YouTube URLs.
- Resolves Spotify links by searching YouTube for the matching track.
- Maintains a simple per-guild queue.
- Plays audio with `@discordjs/voice` and `play-dl`.
- Leaves voice on stop or when the queue ends, depending on config.

### Commands

| Command | Alias | What it does |
| --- | --- | --- |
| `/play <query>` |  | Queues and plays a YouTube/Spotify track. |
| `/pause` |  | Pauses playback. |
| `/resume` | `/unpause` | Resumes playback. |
| `/stop` |  | Stops playback and clears the queue. |
| `/queue` |  | Shows the current queue. |

### Config

```json
{
  "maxQueueSize": 50,
  "volume": 0.65,
  "leaveOnStop": true,
  "leaveOnQueueEnd": true
}
```

### Requirements

- `GuildVoiceStates` intent in Pluxora core config.
- Voice permissions in the guild/channel.
- Plugin dependencies installed locally by Pluxora.

### What To Copy From It

- Lazy loading heavy dependencies in `load(ctx)`.
- Joining a voice channel safely.
- Per-guild queue state.
- Cleaning up voice connections in `unload()`.
- Dashboard component showing active queues.

## Installing These Examples

From the Pluxora dashboard:

1. Go to **Plugins**.
2. Use **GitHub Discovery**.
3. Search this repository.
4. Pick the plugin package you want to install.
5. Enable it and sync slash commands if needed.

From owner commands:

```text
!pluginsearch example
!plugin enable command-example
!plugin sync-commands
```

## Repository Layout

```text
command-example/
  package.json
  index.js
  config.json
utility-example/
  package.json
  index.js
  config.json
music-example/
  package.json
  index.js
  config.json
```

## Plugin Author Notes

- Keep plugin IDs lowercase and stable.
- Declare every Discord event permission you need.
- Use `ctx.client` for tracked listeners and `ctx.rawClient` only for libraries that need raw gateway access.
- Put defaults in `config.json` and `defaultConfig`.
- Keep plugin dependencies in the plugin `package.json`.
- Add the `pluxora-package` GitHub topic to repositories that should appear in discovery.
- These examples use the [Zero-Clause BSD license](LICENSE), so you can copy, modify, and reuse them as templates.
