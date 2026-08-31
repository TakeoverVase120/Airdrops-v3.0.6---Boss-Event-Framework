# Airdrops Boss Configuration Guide

This guide explains how to create, configure and test custom bosses using the native Airdrops boss system.

Bosses are stored as individual YAML files inside:

```text
plugins/Airdrops/bosses/
```

You can create as many boss files as you need.

Example:

```text
plugins/Airdrops/bosses/
├── warden_guardian.yml
├── infernal_golem.yml
├── ancient_guardian.yml
└── custom_boss.yml
```

Each boss file defines one boss.

The filename is mainly for organization.

The important value is the internal:

```yaml
id:
```

That ID is what events use when they reference the boss.

---

# Basic Boss Example

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

This example creates a native Warden boss called:

```text
Warden Guardian
```

with:

```text
500 health
18 damage
Purple boss bar
```

---

# Boss ID

Every boss needs a unique ID.

Example:

```yaml
id: warden_guardian
```

Another boss could use:

```yaml
id: infernal_golem
```

Boss IDs should be simple and consistent.

Recommended format:

```text
lowercase_with_underscores
```

Good examples:

```text
warden_guardian
infernal_golem
ancient_guardian
void_destroyer
frost_titan
```

Avoid spaces in IDs.

Do not give two bosses the same ID.

---

# Enabled

```yaml
enabled: true
```

This determines whether Airdrops loads and allows the boss to be used.

To disable the boss without deleting its file:

```yaml
enabled: false
```

This is useful when testing or temporarily removing content.

---

# Provider

Native bosses currently use:

```yaml
provider: AIRDROPS
```

This tells Airdrops to use its own boss system.

Example:

```yaml
provider: AIRDROPS
```

The provider system is designed so future versions can support other boss and mob systems without changing the entire event format.

For native v3.0.6 bosses, use:

```text
AIRDROPS
```

---

# Entity Type

The entity determines which Minecraft mob is used.

Example:

```yaml
entity: WARDEN
```

Another example:

```yaml
entity: IRON_GOLEM
```

Other valid Minecraft entity types can also be used where supported by the server version.

Example:

```yaml
entity: ZOMBIE
```

or:

```yaml
entity: WITHER_SKELETON
```

The value should match the Bukkit/Paper entity type name.

Use uppercase names with underscores.

---

# Display Name

```yaml
display-name: "&5&lWarden Guardian"
```

This controls the boss's custom entity name.

Minecraft color formatting using `&` codes is supported.

Example:

```yaml
display-name: "&c&lInfernal Golem"
```

Another example:

```yaml
display-name: "&b&lFrost Titan"
```

---

# Boss Stats

Boss stats are configured inside:

```yaml
stats:
```

Example:

```yaml
stats:
  health: 500.0
  damage: 18.0
```

---

# Health

```yaml
health: 500.0
```

This determines the boss's maximum health.

Examples:

```yaml
health: 100.0
```

```yaml
health: 500.0
```

```yaml
health: 1000.0
```

The correct value depends on your server balance.

A high-health boss may be suitable for multiplayer encounters, while lower values may be better for smaller servers.

---

# Damage

```yaml
damage: 18.0
```

This determines the attack damage applied to supported mobs.

Example:

```yaml
damage: 25.0
```

Higher values create more dangerous encounters.

Take care when setting extremely high damage values.

---

# Boss Bar

Boss bars are configured using:

```yaml
bossbar:
```

Example:

```yaml
bossbar:
  enabled: true
  title: "&5&lWarden Guardian"
  color: PURPLE
  style: SOLID
```

---

# Enabling the Boss Bar

```yaml
enabled: true
```

To disable the boss bar:

```yaml
enabled: false
```

The boss can still function without a boss bar.

---

# Boss Bar Title

```yaml
title: "&5&lWarden Guardian"
```

This controls the text displayed in the boss bar.

The boss bar title does not have to be identical to the mob's display name.

Example:

```yaml
display-name: "&cInfernal Golem"

bossbar:
  title: "&4&lINFERNAL INVASION"
```

---

# Boss Bar Color

Example:

```yaml
color: PURPLE
```

Other Bukkit boss bar colors may be used where supported.

Common examples include:

```text
BLUE
GREEN
PINK
PURPLE
RED
WHITE
YELLOW
```

Example:

```yaml
color: RED
```

---

# Boss Bar Style

Example:

```yaml
style: SOLID
```

For v3.0.6, `SOLID` is the standard style used in the tested boss configurations.

---

# Complete Infernal Golem Example

```yaml
id: infernal_golem

enabled: true

provider: AIRDROPS

entity: IRON_GOLEM

display-name: "&c&lInfernal Golem"

stats:
  health: 750.0
  damage: 25.0

bossbar:
  enabled: true
  title: "&c&lInfernal Golem"
  color: RED
  style: SOLID
```

This creates an Iron Golem boss with:

```text
750 health
25 damage
Red boss bar
```

An event can reference it using:

```yaml
boss:
  provider: AIRDROPS
  id: infernal_golem
```

---

# Creating Your Own Boss

To create a new boss, first create a new YAML file.

Example:

```text
plugins/Airdrops/bosses/frost_titan.yml
```

Then add:

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

Save the file.

Then run:

```text
/airdrop reload
```

Airdrops will reload the boss definitions without requiring a full server restart.

---

# Testing a Boss

A configured boss can be tested directly using:

```text
/airdrop boss spawn <boss-id>
```

Example:

```text
/airdrop boss spawn frost_titan
```

For the Warden example:

```text
/airdrop boss spawn warden_guardian
```

For the Infernal Golem:

```text
/airdrop boss spawn infernal_golem
```

This is useful for checking:

```text
Entity type
Display name
Health
Damage
Boss bar
Boss loading
```

before attaching the boss to an event.

---

# Connecting a Boss to an Event

A boss does not automatically appear during an airdrop just because the boss file exists.

The boss must be referenced by an event.

Example:

```yaml
boss:
  provider: AIRDROPS
  id: frost_titan
```

The event itself must then be included in an airdrop tier's event pool.

The relationship is:

```text
Boss Definition
      |
      v
Event Definition
      |
      v
Tier Event Pool
      |
      v
Airdrop
```

For example:

```text
frost_titan
      |
      v
frozen_assault
      |
      v
legendary.events.pool
```

---

# Safe Boss Spawning

Airdrops v3.0.6 includes safe boss spawn searching.

Events define a preferred spawn offset from the airdrop.

Example:

```yaml
spawn:
  offset:
    x: 5
    y: 0
    z: 5
```

Airdrops then checks nearby terrain before spawning the boss.

The system looks for:

```text
Solid ground
Clear space above the ground
A nearby valid position
```

This helps prevent bosses from spawning inside:

```text
Terrain
Trees
Structures
Other solid blocks
```

If the exact configured location is blocked, Airdrops attempts to find a nearby suitable location.

If no suitable location can be found, the event fails safely rather than forcing the boss into an invalid block.

---

# Boss Lifecycle

When a boss is spawned as part of an event, Airdrops tracks that boss.

The normal lifecycle is:

```text
Event Starts
      |
      v
Boss Spawns
      |
      v
Boss Registered
      |
      v
Boss Bar Active
      |
      v
Players Fight Boss
      |
      v
Boss Dies
      |
      v
Boss Marked Defeated
      |
      v
Boss Removed From Active Tracking
      |
      v
Linked Event Completes
```

The event system then handles unlocking the airdrop.

---

# Boss Death and Event Completion

When an Airdrops event boss dies, Airdrops finds the event associated with that boss.

The event can then:

```text
Complete
Unlock the airdrop
Broadcast completion messages
Allow normal claiming to begin
```

This means server owners do not need command blocks or external scripts to unlock the crate after a boss is defeated.

---

# Boss Cleanup

Airdrops tracks active bosses so they can be cleaned up when required.

Boss cleanup can happen when:

```text
The boss dies
The event fails
The event expires
The active airdrop is replaced
/airdrop stop is used
The plugin shuts down
```

This prevents bosses from remaining behind after their event is no longer active.

---

# Replacement Airdrops

If another airdrop starts while an existing boss event is active, v3.0.6 now cleans the old encounter first.

The lifecycle is:

```text
Existing Event
      |
      v
Existing Boss Removed
      |
      v
Existing Structure Removed
      |
      v
Existing Airdrop State Cleared
      |
      v
New Airdrop Starts
```

This prevents old bosses from surviving after a new airdrop replaces the current event.

---

# Reloading Bosses

After editing a boss YAML file, run:

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

Bosses are loaded before events because events may reference boss IDs.

Example workflow:

```text
Edit infernal_golem.yml
      |
      v
Save file
      |
      v
/airdrop reload
      |
      v
/airdrop boss spawn infernal_golem
      |
      v
Test changes
```

---

# Recommended Boss Development Workflow

When creating a new boss, this workflow is recommended:

```text
1. Create the boss YAML
2. Give it a unique ID
3. Configure the entity
4. Configure health and damage
5. Configure the boss bar
6. Save the file
7. Run /airdrop reload
8. Test using /airdrop boss spawn <id>
9. Adjust the configuration if required
10. Create an event for the boss
11. Add the event to a tier
12. Test the full airdrop event
```

This makes it easier to identify whether a problem is caused by the boss itself or by the event configuration.

---

# Common Configuration Mistakes

## Duplicate Boss IDs

Do not create two files using the same:

```yaml
id:
```

Each boss ID should be unique.

---

## Wrong Boss ID in Event

If your boss uses:

```yaml
id: infernal_golem
```

then the event must use:

```yaml
boss:
  provider: AIRDROPS
  id: infernal_golem
```

Using the wrong ID will prevent the event from finding the correct boss.

---

## Boss Not Added to an Event

A boss definition does not automatically make the boss appear during an airdrop.

You need:

```text
Boss
   |
Event
   |
Tier Pool
```

---

## Event Not Added to a Tier

Even if both the boss and event exist, the event will not be selected by normal tier-based airdrops unless it appears in the appropriate:

```yaml
events:
  pool:
```

section of `config.yml`.

---

## Boss Disabled

Check:

```yaml
enabled: true
```

If it is false, the boss will not be available.

---

## Invalid Entity Name

Entity types must use valid Bukkit/Paper entity names.

For example:

```yaml
entity: IRON_GOLEM
```

not:

```yaml
entity: Iron Golem
```

---

## YAML Formatting

YAML indentation is important.

Correct:

```yaml
stats:
  health: 500.0
  damage: 18.0
```

Incorrect:

```yaml
stats:
health: 500.0
damage: 18.0
```

Use spaces instead of tabs.

---

# Example Boss Collection

A server could have:

```text
plugins/Airdrops/bosses/
├── common_guardian.yml
├── rare_guardian.yml
├── frost_titan.yml
├── infernal_golem.yml
├── warden_guardian.yml
└── void_destroyer.yml
```

These bosses can then be connected to different events and airdrop tiers.

For example:

```text
Common Airdrop
  -> Common Guardian

Rare Airdrop
  -> Rare Guardian

Epic Airdrop
  -> Frost Titan

Legendary Airdrop
  -> Warden Guardian
  -> Infernal Golem
  -> Void Destroyer
```

The exact setup is controlled through the server's event configuration.

---

# v3.0.6 Boss System Limitations

v3.0.6 is the foundation of the Airdrops boss framework.

The current native boss system focuses on:

```text
Entity type
Display name
Health
Damage
Boss bar
Safe spawning
Lifecycle tracking
Event linking
Cleanup
```

More advanced features such as custom abilities, phases, equipment systems, custom mob definitions, scaling and external boss provider integrations are planned for future development.

Do not assume those systems are available in v3.0.6 unless specifically documented.

---

# Example Full Workflow

Create:

```text
plugins/Airdrops/bosses/frost_titan.yml
```

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

Then create an event which references:

```yaml
boss:
  provider: AIRDROPS
  id: frost_titan
```

Then add that event to a tier inside `config.yml`.

Finally run:

```text
/airdrop reload
```

and test the entire encounter using:

```text
/airdrop test legendary
```

or test only the boss using:

```text
/airdrop boss spawn frost_titan
```

---

# Summary

Bosses in Airdrops v3.0.6 are modular YAML definitions.

To create a boss:

```text
Create a YAML file
      |
      v
Set a unique boss ID
      |
      v
Choose an entity
      |
      v
Configure stats
      |
      v
Configure the boss bar
      |
      v
Reload Airdrops
      |
      v
Test the boss
      |
      v
Reference it from an event
```

This allows server owners to create their own boss encounters without modifying or recompiling the Airdrops plugin.
