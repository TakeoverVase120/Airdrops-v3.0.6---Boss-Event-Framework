# Airdrops

Airdrops is a highly configurable airdrop, loot and event system for Minecraft Paper servers.

Create automated or manually triggered airdrops with configurable rarity tiers, custom structures, weighted loot, claiming mechanics, boss encounters and event-based gameplay.

## Current Version

**Airdrops v3.0.6**

v3.0.6 introduces the foundation of the new configurable **Boss and Event Framework**, allowing server owners to create custom bosses and encounters through YAML configuration files.

## Features

- Automatic and manually triggered airdrops
- Common, Rare, Epic and Legendary tiers
- Configurable tier chances
- Multi-world support
- Safe asynchronous airdrop location searching
- Configurable minimum and maximum spawn distances
- Fully configurable airdrop structures
- Chest and barrel support
- Weighted loot
- Guaranteed loot
- Configurable loot rolls per tier
- Custom item names and lore
- Enchantments
- Shulker box contents
- Claim timers
- Contested claiming
- Owner protection
- Particle effects and sounds
- Configurable bosses
- Configurable boss health and damage
- Boss bars
- Configurable events
- Event-locked airdrops
- Weighted event pools
- Event chances per tier
- Safe boss spawn searching
- Event timeouts
- Event start, completion and failure messages
- Discord integration
- Runtime configuration reloading

## Boss and Event System

Airdrops v3.0.6 introduces a modular boss and event system.

Bosses are stored inside:

```text
plugins/Airdrops/bosses/
```

Events are stored inside:

```text
plugins/Airdrops/events/
```

A boss defines the enemy itself, while an event defines the encounter surrounding an airdrop.

For example:

```text
warden_guardian
        |
        v
warden_siege
        |
        v
Legendary Airdrop
```

Server owners can create additional boss and event YAML files without modifying the plugin source code.

## Example Boss

```yaml
id: warden_guardian

enabled: true

provider: AIRDROPS

entity: WARDEN

display-name: "&5&lWarden Guardian"

stats:
  health: 500.0
  damage: 18.0

bossbar:
  enabled: true
  title: "&5&lWarden Guardian"
  color: PURPLE
  style: SOLID
```

## Example Event

```yaml
id: warden_siege

enabled: true

display-name: "&5&lWarden Siege"

airdrop:
  lock-until-complete: true

boss:
  provider: AIRDROPS
  id: warden_guardian

spawn:
  offset:
    x: 4
    y: 0
    z: 4

messages:
  start:
    - "&5&lWARDEN SIEGE"
    - "&7A guardian protects the airdrop!"
    - "&cDefeat it before attempting to claim the crate."

  complete:
    - "&a&lWARDEN SIEGE COMPLETE!"
    - "&eThe airdrop can now be claimed."

  failed:
    - "&cThe Warden Siege has failed."

timeout:
  enabled: true
  seconds: 900
```

## Weighted Event Pools

Events can be assigned to individual airdrop tiers through `config.yml`.

Example:

```yaml
events:
  enabled: true
  chance: 100

  pool:
    - id: warden_siege
      weight: 50

    - id: infernal_invasion
      weight: 50
```

`chance` determines whether an event occurs.

`weight` determines which event is selected when multiple events are available.

Weights do not need to total 100.

## Event Lifecycle

A boss event follows this general lifecycle:

```text
Airdrop Spawns
      |
      v
Event Selected
      |
      v
Boss Spawns
      |
      v
Airdrop Event Locked
      |
      v
Players Defeat Boss
      |
      v
Event Completes
      |
      v
Airdrop Unlocks
      |
      v
Normal Claiming Begins
```

## Commands

```text
/airdrop start
/airdrop stop
/airdrop test <tier>
/airdrop info
/airdrop reload
/airdrop boss spawn <boss-id>
/airdrop event start <event-id>
```

Example:

```text
/airdrop test legendary
```

Boss testing:

```text
/airdrop boss spawn warden_guardian
```

Event testing:

```text
/airdrop event start warden_siege
```

## Installation

1. Download the latest Airdrops `.jar` from the Releases section.
2. Stop your Minecraft server.
3. Place the Airdrops `.jar` inside your server's `plugins` directory.
4. Start the server.
5. Airdrops will generate its configuration files.
6. Configure Airdrops for your server.
7. Use `/airdrop reload` after making supported configuration changes.

It is recommended that you back up your existing configuration before updating between versions.

## Configuration

The main configuration can be found at:

```text
plugins/Airdrops/config.yml
```

Loot configuration can be found at:

```text
plugins/Airdrops/loot.yml
```

Boss definitions:

```text
plugins/Airdrops/bosses/
```

Event definitions:

```text
plugins/Airdrops/events/
```

More detailed configuration documentation will be available in the repository documentation.

## v3.0.6

Airdrops v3.0.6 includes the new Boss and Event Framework alongside important lifecycle and cleanup improvements.

Major additions include:

- Configurable YAML bosses
- Configurable YAML events
- Warden Siege encounter
- Infernal Invasion encounter
- Weighted event selection
- Event chances
- Event locking
- Safe boss spawning
- Boss and event testing commands
- Improved `/airdrop stop`
- Improved `/airdrop reload`
- Active boss cleanup
- Active structure cleanup
- Replacement airdrop cleanup

See the GitHub Releases section for the complete v3.0.6 changelog and download.

## Support

If you encounter a bug, please provide as much information as possible, including:

- Airdrops version
- Paper/server version
- Relevant console errors
- Steps required to reproduce the issue
- Relevant configuration

Bug reports can be submitted through the repository's Issues section.

## Development

Airdrops is actively being developed.

The v3.0.6 Boss and Event Framework provides the foundation for future systems including multi-stage encounters, configurable waves, objectives, abilities, custom mobs, custom items and more advanced raid-style airdrops.
