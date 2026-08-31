# Airdrops Configuration Guide

This guide covers the main configuration options available in Airdrops v3.0.6.

The main configuration file is located at:

```text
plugins/Airdrops/config.yml
```

The configuration controls the core behaviour of the plugin, including:

- Enabled worlds
- Airdrop locations
- Automatic scheduling
- Rarity tiers
- Tier chances
- Structures
- Loot roll amounts
- Claiming
- Sounds
- Particles
- Boss events
- Event chances
- Weighted event pools

Additional systems use their own configuration files and folders:

```text
plugins/Airdrops/
├── config.yml
├── loot.yml
├── bosses/
└── events/
```

Boss configuration is documented separately in:

```text
docs/BOSSES.md
```

Event configuration is documented separately in:

```text
docs/EVENTS.md
```

---

# Before Editing Configuration

It is recommended that you make a backup of your configuration before making major changes.

YAML formatting is important.

Always use spaces rather than tabs.

Correct:

```yaml
location:
  min-distance: 500
  max-distance: 3000
```

Incorrect:

```yaml
location:
min-distance: 500
max-distance: 3000
```

After making supported configuration changes, run:

```text
/airdrop reload
```

Airdrops v3.0.6 reloads the main configuration along with boss definitions, event definitions and loot configuration.

---

# Main Airdrop Section

The core Airdrops settings are located underneath the main:

```yaml
airdrop:
```

section.

A simplified example looks like:

```yaml
airdrop:

  worlds:
    - world
    - world_the_end

  location:
    min-distance: 500
    max-distance: 3000

  structure:
    enabled: true
    default: basic_crate

  loot-rolls:
    common:
      min: 3
      max: 5

    rare:
      min: 4
      max: 6

    epic:
      min: 5
      max: 8

    legendary:
      min: 7
      max: 10

  claiming:
    enabled: true
    claim-time-seconds: 10
```

Your generated `config.yml` may contain additional sections and comments depending on the version.

When configuring your server, use the generated configuration as the authoritative template for available settings.

---

# Worlds

Airdrops can be configured to operate in multiple worlds.

Example:

```yaml
airdrop:
  worlds:
    - world
    - world_the_end
```

Each entry must match the actual Bukkit/Paper world name loaded by your server.

For a standard server these may include names such as:

```text
world
world_nether
world_the_end
```

Custom server worlds may have different names.

For example:

```yaml
airdrop:
  worlds:
    - survival
    - resource_world
    - events_world
```

Only add worlds that actually exist and are loaded by the server.

---

# Finding Your World Names

Airdrops logs available and loaded worlds during relevant airdrop operations.

This is useful for server owners who are unsure of the exact internal world name.

World names must match exactly.

For example, if the server loads:

```text
world_the_end
```

do not configure:

```text
the_end
```

unless that is actually the name of your world.

---

# Nether Considerations

The Nether can technically be included if your configuration and server setup support it.

However, server owners should consider the Nether roof and unusual terrain generation when deciding whether Nether airdrops are appropriate.

For example:

```yaml
airdrop:
  worlds:
    - world
    - world_the_end
```

can be used if you intentionally do not want airdrops spawning in the Nether.

---

# Airdrop Location Distance

Airdrops can search for locations within a configurable distance range.

Example:

```yaml
airdrop:
  location:
    min-distance: 500
    max-distance: 3000
```

This means the location search targets positions between the configured minimum and maximum distance from the world's central reference area used by the plugin.

---

# Minimum Distance

Example:

```yaml
min-distance: 500
```

This helps prevent airdrops from appearing too close to the central area.

Increasing the value pushes potential airdrop locations farther away.

---

# Maximum Distance

Example:

```yaml
max-distance: 3000
```

This limits how far away the location search can target.

A larger value creates a wider potential airdrop area.

---

# Example Distance Configurations

Smaller map:

```yaml
location:
  min-distance: 250
  max-distance: 1500
```

Larger survival server:

```yaml
location:
  min-distance: 500
  max-distance: 5000
```

Always choose values appropriate for your world border, server size and player population.

---

# Safe Airdrop Location Searching

Airdrops uses its location system to find suitable positions rather than blindly placing airdrops at arbitrary coordinates.

Location searching is handled separately from boss safe-spawn searching.

The general airdrop flow is:

```text
Select World
      |
      v
Generate Candidate Area
      |
      v
Search for Suitable Location
      |
      v
Validate Location
      |
      v
Place Airdrop
```

Bosses spawned by events have their own nearby safe-spawn system after the airdrop itself has been placed.

---

# Structures

Physical airdrop structures can be enabled through:

```yaml
airdrop:
  structure:
    enabled: true
    default: basic_crate
```

---

# Enabling Structures

```yaml
enabled: true
```

allows Airdrops to place its configured physical structures.

If structure placement is disabled:

```yaml
enabled: false
```

the structure system will not be used in the same way.

---

# Default Structure

Example:

```yaml
default: basic_crate
```

This determines the fallback/default structure used where appropriate.

Airdrop tiers can also use their own configured structure.

This allows different rarities to have visually different drops.

For example:

```text
Common
Basic Crate

Rare
Supply Crate

Epic
Epic Balloon

Legendary
Legendary Balloon
```

The exact structure names must correspond to structures defined in your configuration.

---

# Structure Cleanup

Airdrops tracks the blocks placed as part of an active structure.

When the active airdrop is cleaned up, its structure can also be removed.

v3.0.6 includes additional cleanup fixes to prevent old structures from remaining when another airdrop replaces the current one.

The replacement lifecycle is:

```text
Existing Airdrop
      |
      v
Existing Event Cleaned
      |
      v
Existing Boss Removed
      |
      v
Existing Structure Removed
      |
      v
New Airdrop Created
```

---

# Loot Rolls

The number of loot selections performed for each rarity can be configured.

Example:

```yaml
airdrop:
  loot-rolls:

    common:
      min: 3
      max: 5

    rare:
      min: 4
      max: 6

    epic:
      min: 5
      max: 8

    legendary:
      min: 7
      max: 10
```

This controls the number of loot rolls, not necessarily the total number of individual item units inside the container.

---

# Common Loot Rolls

```yaml
common:
  min: 3
  max: 5
```

A Common airdrop performs between 3 and 5 configured loot rolls.

---

# Rare Loot Rolls

```yaml
rare:
  min: 4
  max: 6
```

---

# Epic Loot Rolls

```yaml
epic:
  min: 5
  max: 8
```

---

# Legendary Loot Rolls

```yaml
legendary:
  min: 7
  max: 10
```

This gives higher rarity airdrops the ability to generate more loot selections.

Actual loot contents are configured separately in:

```text
plugins/Airdrops/loot.yml
```

---

# Claiming

The claiming system can be configured through:

```yaml
airdrop:
  claiming:
    enabled: true
    claim-time-seconds: 10
```

---

# Enabling Claiming

```yaml
enabled: true
```

enables the Airdrops claiming system.

---

# Claim Time

Example:

```yaml
claim-time-seconds: 10
```

This determines how long the configured claim process takes.

For example:

```yaml
claim-time-seconds: 5
```

would make claiming faster.

```yaml
claim-time-seconds: 20
```

would create a longer claim period.

Choose a value that fits your server's PvP and airdrop gameplay.

---

# Event Lock vs Claim Lock

Boss events introduce an additional protection stage before normal claiming.

For an event-protected airdrop:

```text
EVENT LOCK
      |
      v
Boss/Event Completed
      |
      v
CLAIM LOCK
      |
      v
Player Claims
      |
      v
OWNER LOCK
```

The event must complete before the normal claiming system becomes available.

This means your existing claiming settings continue to work after the boss encounter has finished.

---

# Airdrop Tiers

Airdrops supports the following standard rarity tiers:

```text
Common
Rare
Epic
Legendary
```

Each tier can have its own settings.

Depending on your generated configuration, tier settings can control values such as:

```text
Display name
Color
Rarity/selection weight
Expiry
Structure
Event settings
```

A conceptual tier configuration can look like:

```yaml
legendary:
  display-name: "&6&lLegendary Airdrop"
  color: YELLOW
  expiry-minutes: 30
  rarity: 5
  structure: legendary_balloon
```

Use the exact key names generated by your version of `config.yml`.

---

# Tier Display Name

Example:

```yaml
display-name: "&6&lLegendary Airdrop"
```

This determines how the tier is presented to players where the setting is used.

Minecraft `&` formatting codes can be used where supported.

---

# Tier Color

Example:

```yaml
color: YELLOW
```

Tier colors are used by supported visual systems such as tier-specific display and particle behaviour.

Use a valid color supported by Airdrops.

---

# Tier Expiry

Example:

```yaml
expiry-minutes: 30
```

This determines how long the airdrop remains active before its configured cleanup/expiry behaviour occurs.

For example:

```yaml
expiry-minutes: 15
```

would create a shorter-lived drop.

---

# Tier Structure

A tier can select a structure using a configured structure ID.

Example:

```yaml
structure: legendary_balloon
```

Another tier could use:

```yaml
structure: basic_crate
```

This allows each rarity to have its own physical appearance.

---

# Tier Event Configuration

v3.0.6 allows individual tiers to have their own event configuration.

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

This controls whether the tier can start an event and which events are available.

---

# Enabling Events for a Tier

```yaml
events:
  enabled: true
```

allows the tier to use events.

To create a normal tier without event encounters:

```yaml
events:
  enabled: false
```

For example, you could configure:

```text
Common
No events

Rare
Occasional events

Epic
Frequent events

Legendary
Guaranteed events
```

---

# Event Chance

Example:

```yaml
chance: 100
```

This determines the chance that an eligible airdrop starts an event.

For example:

```yaml
chance: 25
```

means the tier has approximately a 25% event chance.

```yaml
chance: 50
```

means approximately 50%.

```yaml
chance: 100
```

means an event is always attempted when that tier is used and events are enabled.

---

# Event Pool

The event pool determines which events can be selected.

Example:

```yaml
pool:
  - id: warden_siege
    weight: 50

  - id: infernal_invasion
    weight: 50
```

The `id` must match an event definition inside:

```text
plugins/Airdrops/events/
```

For example:

```text
events/warden_siege.yml
```

contains:

```yaml
id: warden_siege
```

and the tier references:

```yaml
- id: warden_siege
  weight: 50
```

---

# Event Weights

Weights are relative and do not need to total 100.

Example:

```yaml
pool:
  - id: warden_siege
    weight: 70

  - id: infernal_invasion
    weight: 30
```

makes Warden Siege more likely.

You could also use:

```yaml
pool:
  - id: normal_guardian
    weight: 100

  - id: dangerous_invasion
    weight: 25

  - id: extremely_rare_event
    weight: 5
```

This gives you much more control over encounter rarity.

---

# Event Chance and Weight Are Different

This is important.

`chance` controls:

```text
Does this airdrop get an event?
```

`weight` controls:

```text
Which event does it get?
```

For example:

```yaml
events:
  enabled: true
  chance: 50

  pool:
    - id: warden_siege
      weight: 75

    - id: infernal_invasion
      weight: 25
```

The flow is:

```text
Airdrop Tier Selected
      |
      v
50% Event Chance
      |
   +--+--+
   |     |
   No    Yes
   |     |
Normal   v
Drop   Weighted Pool
         |
    +----+----+
    |         |
Warden      Infernal
Siege       Invasion
```

---

# Example: Legendary Event Setup

A Legendary tier could be configured to always run one of two encounters:

```yaml
legendary:

  events:
    enabled: true
    chance: 100

    pool:
      - id: warden_siege
        weight: 50

      - id: infernal_invasion
        weight: 50
```

This means:

```text
Legendary Airdrop
      |
      v
Event Guaranteed
      |
      v
50/50 Weighted Selection
      |
   +--+--+
   |     |
Warden  Infernal
Siege   Invasion
```

---

# Example: Rare Events

A Rare tier could use:

```yaml
rare:

  events:
    enabled: true
    chance: 20

    pool:
      - id: rare_guardian
        weight: 100
```

This would give Rare airdrops an occasional encounter rather than guaranteeing one every time.

---

# Example: No Events

To keep Common airdrops simple:

```yaml
common:

  events:
    enabled: false
```

This allows lower tiers to remain traditional loot drops while higher tiers become encounters.

---

# Example Tier Progression

A server could conceptually configure:

```yaml
common:
  events:
    enabled: false

rare:
  events:
    enabled: true
    chance: 20

    pool:
      - id: rare_guardian
        weight: 100

epic:
  events:
    enabled: true
    chance: 50

    pool:
      - id: frost_titan_assault
        weight: 100

legendary:
  events:
    enabled: true
    chance: 100

    pool:
      - id: warden_siege
        weight: 50

      - id: infernal_invasion
        weight: 50
```

Remember to place these event sections inside the correct tier structure used by your generated `config.yml`.

---

# Configuring Bosses

Bosses are not defined directly inside the main `config.yml`.

Boss definitions are stored inside:

```text
plugins/Airdrops/bosses/
```

Example:

```text
plugins/Airdrops/bosses/warden_guardian.yml
```

A boss definition can contain settings such as:

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

See:

```text
docs/BOSSES.md
```

for the full boss configuration guide.

---

# Configuring Events

Events are stored inside:

```text
plugins/Airdrops/events/
```

Example:

```text
plugins/Airdrops/events/warden_siege.yml
```

The event then references a boss:

```yaml
boss:
  provider: AIRDROPS
  id: warden_guardian
```

Finally, the main `config.yml` determines which tier can select that event:

```yaml
events:
  enabled: true
  chance: 100

  pool:
    - id: warden_siege
      weight: 100
```

The full relationship is:

```text
bosses/warden_guardian.yml
      |
      v
events/warden_siege.yml
      |
      v
config.yml
legendary.events.pool
      |
      v
Legendary Airdrop
```

See:

```text
docs/EVENTS.md
```

for the complete event guide.

---

# Loot Configuration

The actual loot tables are configured separately in:

```text
plugins/Airdrops/loot.yml
```

The main configuration determines how many loot rolls a tier receives.

For example:

```yaml
loot-rolls:
  legendary:
    min: 7
    max: 10
```

`loot.yml` determines what those rolls can actually select.

This separation means:

```text
config.yml
      |
      v
How many loot rolls?

loot.yml
      |
      v
What items can those rolls produce?
```

---

# Reloading Configuration

After editing supported configuration files, run:

```text
/airdrop reload
```

v3.0.6 reloads:

```text
config.yml
bosses/*.yml
events/*.yml
loot.yml
```

The important order is:

```text
Main Configuration
      |
      v
Boss Definitions
      |
      v
Event Definitions
      |
      v
Loot
```

Boss definitions are loaded before events because events can reference boss IDs.

---

# Example Configuration Workflow

Suppose you want to add a new Legendary Frost Titan event.

First create:

```text
bosses/frost_titan.yml
```

with:

```yaml
id: frost_titan
enabled: true
provider: AIRDROPS
entity: IRON_GOLEM
display-name: "&b&lFrost Titan"

stats:
  health: 900.0
  damage: 28.0

bossbar:
  enabled: true
  title: "&b&lFrost Titan"
  color: BLUE
  style: SOLID
```

Then create:

```text
events/frost_titan_assault.yml
```

and reference:

```yaml
boss:
  provider: AIRDROPS
  id: frost_titan
```

Then add the event to your Legendary event pool:

```yaml
events:
  enabled: true
  chance: 100

  pool:
    - id: warden_siege
      weight: 40

    - id: infernal_invasion
      weight: 40

    - id: frost_titan_assault
      weight: 20
```

Run:

```text
/airdrop reload
```

Then test the full tier:

```text
/airdrop test legendary
```

---

# Testing Configuration Changes

Useful testing commands include:

```text
/airdrop test common
/airdrop test rare
/airdrop test epic
/airdrop test legendary
```

To test a boss independently:

```text
/airdrop boss spawn <boss-id>
```

Example:

```text
/airdrop boss spawn warden_guardian
```

To test an event independently:

```text
/airdrop event start <event-id>
```

Example:

```text
/airdrop event start warden_siege
```

To reload configuration:

```text
/airdrop reload
```

---

# Recommended Testing Order

When making major configuration changes, test them incrementally.

Recommended workflow:

```text
1. Back up your existing configuration

2. Make one group of changes

3. Save the files

4. Run /airdrop reload

5. Check the console for warnings/errors

6. Test the boss directly if applicable

7. Test the event directly if applicable

8. Test the complete airdrop tier

9. Test claiming

10. Test cleanup
```

This is much easier to troubleshoot than changing many unrelated settings at once.

---

# Common Configuration Problems

## YAML Indentation

YAML is indentation-sensitive.

Correct:

```yaml
events:
  enabled: true
  chance: 100

  pool:
    - id: warden_siege
      weight: 50
```

Incorrect:

```yaml
events:
enabled: true
chance: 100
pool:
- id: warden_siege
weight: 50
```

Use spaces, not tabs.

---

# Incorrect World Name

If an airdrop world isn't being used, verify that the configured name matches the server's actual loaded world name.

Example:

```yaml
worlds:
  - world
  - world_the_end
```

---

# Incorrect Structure ID

If a tier references:

```yaml
structure: legendary_balloon
```

then that structure ID needs to exist in the structure configuration used by your installation.

---

# Incorrect Event ID

If the event file contains:

```yaml
id: infernal_invasion
```

the pool must use:

```yaml
- id: infernal_invasion
  weight: 50
```

---

# Incorrect Boss ID

If the boss contains:

```yaml
id: infernal_golem
```

the event must reference:

```yaml
boss:
  provider: AIRDROPS
  id: infernal_golem
```

---

# Disabled Event

Check both levels.

The event file itself:

```yaml
enabled: true
```

and the tier:

```yaml
events:
  enabled: true
```

Both need to allow the event to be used.

---

# Event Not Appearing During Testing

Check the tier's:

```yaml
chance:
```

If you are testing, temporarily using:

```yaml
chance: 100
```

makes it easier to confirm the event system is working.

Also remember that multiple events in the pool are selected by weight.

---

# Configuration Safety

Before installing a new Airdrops version, it is recommended that you back up:

```text
plugins/Airdrops/
```

especially:

```text
config.yml
loot.yml
bosses/
events/
```

New versions may introduce additional configuration options.

Always compare your existing configuration with the configuration/documentation supplied for the version you are installing.

---

# v3.0.6 Configuration Structure

At a high level, v3.0.6 configuration is divided into responsibilities:

```text
config.yml
      |
      +-- Core airdrop behaviour
      +-- Worlds
      +-- Locations
      +-- Structures
      +-- Loot roll counts
      +-- Claiming
      +-- Tiers
      +-- Tier event pools

loot.yml
      |
      +-- Loot definitions

bosses/
      |
      +-- Boss definitions

events/
      |
      +-- Event definitions
```

This separation is intentional.

It allows server owners to expand their boss and event collection without turning the main `config.yml` into one enormous file.

---

# Recommended Server Setup

For a straightforward production setup, you might use:

```text
Common
Normal loot airdrop
No event

Rare
Better loot
Small event chance

Epic
High-value loot
Moderate event chance

Legendary
Best loot
Guaranteed boss event
```

For example:

```yaml
common:
  events:
    enabled: false

rare:
  events:
    enabled: true
    chance: 20

epic:
  events:
    enabled: true
    chance: 50

legendary:
  events:
    enabled: true
    chance: 100
```

The exact event pools can then be tailored to your server's progression.

---

# v3.0.6 Event Example

A tested Legendary setup uses two possible encounters:

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

This allows a Legendary airdrop to select between the Warden Siege and Infernal Invasion encounters.

---

# Configuration Philosophy

Airdrops is designed around server-owner configurability.

Where practical, content should be configurable without modifying or recompiling the plugin.

The boss and event framework introduced in v3.0.6 expands this approach by moving encounter definitions into individual YAML files.

The intended structure is:

```text
Configure the server
      |
      v
Create content in YAML
      |
      v
Reload Airdrops
      |
      v
Test
      |
      v
Deploy
```

rather than requiring server owners to edit Java code for every new encounter.

---

# Summary

The main `config.yml` controls the overall behaviour of Airdrops.

The most important relationship introduced in v3.0.6 is:

```text
config.yml
Tier Event Pool
      |
      v
Event Definition
      |
      v
Boss Definition
```

For example:

```text
Legendary Tier
      |
      v
infernal_invasion
      |
      v
infernal_golem
```

When configuring your server:

```text
1. Configure core Airdrops settings
2. Configure your tiers
3. Configure loot
4. Create any required bosses
5. Create events
6. Add events to tier pools
7. Run /airdrop reload
8. Test each component
9. Test the complete airdrop lifecycle
```

For more detailed encounter documentation, see:

```text
docs/BOSSES.md
docs/EVENTS.md
```
