# Airdrops Event Configuration Guide

This guide explains how to create, configure and test custom events using the Airdrops v3.0.6 event system.

Events are stored as individual YAML files inside:

```text
plugins/Airdrops/events/
```

You can create multiple event files and assign them to different airdrop tiers through `config.yml`.

Example:

```text
plugins/Airdrops/events/
├── warden_siege.yml
├── infernal_invasion.yml
├── frost_titan_assault.yml
└── custom_event.yml
```

Each event defines an encounter that can take place around an airdrop.

---

# What Is an Event?

Bosses and events are separate systems.

A boss defines the enemy:

```text
warden_guardian
```

An event defines the encounter:

```text
warden_siege
```

The airdrop tier determines when that event can be selected.

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
warden_guardian
      |
      v
warden_siege
      |
      v
Legendary Airdrop
```

This separation allows bosses and events to remain modular.

---

# Basic Event Example

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

This event:

```text
Uses the Warden Guardian boss
Locks the airdrop
Spawns the boss near the airdrop
Broadcasts start messages
Waits for the boss to be defeated
Unlocks the airdrop when completed
Broadcasts completion messages
Has a configurable timeout
```

---

# Event ID

Every event requires a unique ID.

Example:

```yaml
id: warden_siege
```

Another event could use:

```yaml
id: infernal_invasion
```

Recommended format:

```text
lowercase_with_underscores
```

Good examples:

```text
warden_siege
infernal_invasion
frost_titan_assault
ancient_guardian_attack
void_invasion
```

Do not give two event files the same ID.

The event ID is what you place inside a tier's event pool.

Example:

```yaml
pool:
  - id: warden_siege
    weight: 50
```

---

# Enabled

```yaml
enabled: true
```

This controls whether the event is available.

To disable the event without deleting the YAML file:

```yaml
enabled: false
```

This is useful when testing or temporarily removing an encounter.

---

# Display Name

```yaml
display-name: "&5&lWarden Siege"
```

The event display name is separate from the boss display name.

For example:

```text
Event:
Warden Siege

Boss:
Warden Guardian
```

Another example:

```text
Event:
Infernal Invasion

Boss:
Infernal Golem
```

This allows an encounter to have its own identity.

---

# Airdrop Event Lock

One of the main event features introduced with the new framework is the event lock.

Configure it using:

```yaml
airdrop:
  lock-until-complete: true
```

When enabled, the airdrop cannot enter normal claiming while the encounter is still active.

The lifecycle becomes:

```text
Airdrop Spawns
      |
      v
Event Starts
      |
      v
EVENT LOCK
      |
      v
Boss Encounter
      |
      v
Boss Defeated
      |
      v
Event Completes
      |
      v
EVENT LOCK Removed
      |
      v
Normal Claiming
```

The event lock works before the existing claim and owner protection systems.

The complete lock lifecycle is:

```text
EVENT LOCK
      |
      v
Event Complete
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

This means the new event system does not replace the existing claiming system.

It adds another protection layer before claiming begins.

---

# Disabling Event Locking

An event can use:

```yaml
airdrop:
  lock-until-complete: false
```

In that case the event does not request the normal event lock.

For boss-protected airdrops, the recommended configuration is:

```yaml
lock-until-complete: true
```

---

# Connecting a Boss

Events can reference a configured boss.

Example:

```yaml
boss:
  provider: AIRDROPS
  id: warden_guardian
```

The `id` must match the ID inside a boss YAML file.

For example:

```text
plugins/Airdrops/bosses/warden_guardian.yml
```

contains:

```yaml
id: warden_guardian
```

and the event contains:

```yaml
boss:
  provider: AIRDROPS
  id: warden_guardian
```

The IDs must match.

---

# Boss Provider

Native Airdrops bosses use:

```yaml
provider: AIRDROPS
```

For v3.0.6 native encounters, use:

```text
AIRDROPS
```

The provider framework has been designed so additional boss/mob providers can be integrated in future versions.

---

# Boss Spawn Offset

The event determines the preferred boss spawn position relative to the airdrop.

Example:

```yaml
spawn:
  offset:
    x: 4
    y: 0
    z: 4
```

This requests a boss position offset from the airdrop's event origin.

Another example:

```yaml
spawn:
  offset:
    x: 6
    y: 0
    z: -4
```

The values represent:

```text
x = horizontal X offset
y = vertical offset
z = horizontal Z offset
```

---

# Safe Boss Spawn Searching

The configured offset is treated as the preferred boss position.

Airdrops does not simply force the boss into that exact block.

v3.0.6 performs a nearby safe-location search before spawning the boss.

The process is approximately:

```text
Airdrop Location
      |
      v
Apply Event Offset
      |
      v
Preferred Boss Position
      |
      v
Search Nearby Terrain
      |
      v
Check Ground
      |
      v
Check Clearance
      |
      v
Spawn Boss
```

This helps prevent bosses from spawning inside terrain, trees, structures or other solid blocks.

If the preferred location is obstructed, Airdrops searches around it for a suitable nearby position.

If no suitable position can be found, the event fails safely instead of forcing the boss into an invalid location.

---

# Event Start Messages

Events can broadcast their own messages when they begin.

Example:

```yaml
messages:
  start:
    - "&5&lWARDEN SIEGE"
    - "&7A guardian protects the airdrop!"
    - "&cDefeat it before attempting to claim the crate."
```

You can add multiple lines.

Example:

```yaml
messages:
  start:
    - "&c&lINFERNAL INVASION"
    - "&7Something powerful has arrived."
    - "&cDefeat the guardian to unlock the airdrop."
```

---

# Event Completion Messages

Completion messages are configured under:

```yaml
messages:
  complete:
```

Example:

```yaml
messages:
  complete:
    - "&a&lWARDEN SIEGE COMPLETE!"
    - "&eThe airdrop can now be claimed."
```

These messages are sent when the event successfully completes.

For a boss encounter, this normally occurs when the linked boss is defeated.

---

# Event Failure Messages

Failure messages are configured using:

```yaml
messages:
  failed:
```

Example:

```yaml
messages:
  failed:
    - "&cThe Warden Siege has failed."
```

Another example:

```yaml
messages:
  failed:
    - "&cThe Infernal Invasion has ended."
    - "&7The encounter could not be completed."
```

These messages can be used when the event fails or is cleaned up before successful completion.

---

# Event Timeout

Events can have a configurable timeout.

Example:

```yaml
timeout:
  enabled: true
  seconds: 900
```

`900` seconds equals:

```text
15 minutes
```

Other examples:

```yaml
timeout:
  enabled: true
  seconds: 600
```

for 10 minutes.

Or:

```yaml
timeout:
  enabled: true
  seconds: 1200
```

for 20 minutes.

This prevents abandoned encounters from remaining active indefinitely.

---

# Disabling the Timeout

A timeout can be disabled using:

```yaml
timeout:
  enabled: false
  seconds: 900
```

For public servers, using a timeout is generally recommended so abandoned encounters do not remain active indefinitely.

---

# Complete Infernal Invasion Example

An event using the Infernal Golem could look like:

```yaml
id: infernal_invasion

enabled: true

display-name: "&c&lInfernal Invasion"

airdrop:
  lock-until-complete: true

boss:
  provider: AIRDROPS
  id: infernal_golem

spawn:
  offset:
    x: 5
    y: 0
    z: 5

messages:
  start:
    - "&c&lINFERNAL INVASION"
    - "&7An Infernal Golem protects the airdrop."
    - "&cDefeat it to unlock the crate."

  complete:
    - "&a&lINFERNAL INVASION COMPLETE!"
    - "&eThe airdrop can now be claimed."

  failed:
    - "&cThe Infernal Invasion has failed."

timeout:
  enabled: true
  seconds: 900
```

This event references:

```yaml
boss:
  id: infernal_golem
```

which means a boss with:

```yaml
id: infernal_golem
```

must exist inside the `bosses` directory.

---

# Connecting Events to Airdrop Tiers

Creating an event file does not automatically cause it to appear during normal airdrops.

The event must be added to an airdrop tier's event pool inside:

```text
plugins/Airdrops/config.yml
```

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

This tells the tier that it can select either event.

---

# Event Enabled Setting

At the tier level:

```yaml
events:
  enabled: true
```

allows that tier to use its configured event pool.

To prevent the tier from running events:

```yaml
events:
  enabled: false
```

This allows you to have events enabled for higher-tier airdrops while keeping lower tiers as normal loot drops.

---

# Event Chance

Example:

```yaml
chance: 100
```

This controls the chance that the tier attempts to start an event.

Examples:

```yaml
chance: 100
```

Event is always attempted.

```yaml
chance: 50
```

Approximately half of eligible airdrops attempt an event.

```yaml
chance: 25
```

Approximately one quarter of eligible airdrops attempt an event.

This is separate from event weights.

---

# Event Chance vs Event Weight

These two settings control different things.

`chance` determines:

```text
Does an event happen?
```

`weight` determines:

```text
Which event is selected?
```

Example:

```yaml
events:
  enabled: true
  chance: 50

  pool:
    - id: warden_siege
      weight: 70

    - id: infernal_invasion
      weight: 30
```

First, Airdrops performs the 50% event chance.

If an event is selected, the weighted pool is then used to choose between Warden Siege and Infernal Invasion.

---

# Weighted Event Pools

Event weights are relative.

They do not need to total 100.

For example:

```yaml
pool:
  - id: warden_siege
    weight: 100

  - id: infernal_invasion
    weight: 50
```

Warden Siege has twice the selection weight of Infernal Invasion.

You could also configure:

```yaml
pool:
  - id: normal_guardian
    weight: 100

  - id: dangerous_invasion
    weight: 25

  - id: extremely_rare_event
    weight: 5
```

This makes some encounters much rarer than others.

---

# Example Tier Progression

A server could configure events differently for every rarity.

Conceptually:

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
      - id: frost_assault
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

The exact location of these sections should follow the tier structure in your generated `config.yml`.

This setup would make:

```text
Common
No event

Rare
Occasional event

Epic
More frequent event

Legendary
Guaranteed event
```

---

# Creating Your Own Event

Suppose you already created this boss:

```text
frost_titan
```

Now create:

```text
plugins/Airdrops/events/frost_titan_assault.yml
```

Add:

```yaml
id: frost_titan_assault

enabled: true

display-name: "&b&lFrost Titan Assault"

airdrop:
  lock-until-complete: true

boss:
  provider: AIRDROPS
  id: frost_titan

spawn:
  offset:
    x: 5
    y: 0
    z: 5

messages:
  start:
    - "&b&lFROST TITAN ASSAULT"
    - "&7A powerful guardian has appeared."
    - "&cDefeat it before claiming the airdrop."

  complete:
    - "&aThe Frost Titan has been defeated."
    - "&eThe airdrop is now available."

  failed:
    - "&cThe Frost Titan Assault has failed."

timeout:
  enabled: true
  seconds: 900
```

Save the file.

Then run:

```text
/airdrop reload
```

---

# Adding Your New Event to a Tier

After creating:

```text
frost_titan_assault
```

add it to an event pool.

Example:

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

The new event is now part of that tier's weighted selection pool.

---

# Testing an Event Directly

An event can be tested using:

```text
/airdrop event start <event-id>
```

Example:

```text
/airdrop event start warden_siege
```

Another example:

```text
/airdrop event start infernal_invasion
```

For your own event:

```text
/airdrop event start frost_titan_assault
```

Direct event testing is useful when developing YAML files.

---

# Testing the Full Airdrop Flow

After the individual boss and event work correctly, test the full tier system.

Example:

```text
/airdrop test legendary
```

This tests more than directly starting the event.

It tests the actual airdrop flow including:

```text
Tier
      |
      v
Structure
      |
      v
Loot
      |
      v
Event Chance
      |
      v
Weighted Event Selection
      |
      v
Boss Encounter
      |
      v
Event Lock
      |
      v
Event Completion
      |
      v
Claiming
```

This is the recommended final test for a newly configured event.

---

# Recommended Event Development Workflow

When creating a new encounter:

```text
1. Create the boss YAML
2. Reload Airdrops
3. Test the boss directly
4. Create the event YAML
5. Reference the correct boss ID
6. Reload Airdrops
7. Test the event directly
8. Add the event to a tier pool
9. Reload Airdrops
10. Test the full tier using /airdrop test
11. Test boss death and unlocking
12. Test timeout/cleanup
```

This helps isolate configuration problems.

---

# Reloading Events

After editing event files, use:

```text
/airdrop reload
```

v3.0.6 reloads the main configuration, boss definitions, event definitions and loot configuration.

The important loading order is:

```text
config.yml
      |
      v
Bosses
      |
      v
Events
      |
      v
Loot
```

Bosses are loaded before events because events can reference boss IDs.

---

# Event Completion

For the current boss-based event framework, successful completion normally occurs when the linked boss is defeated.

The lifecycle is:

```text
Boss Dies
      |
      v
Airdrops Detects Boss Death
      |
      v
Linked Event Located
      |
      v
Event Marked Complete
      |
      v
Event Removed From Active Tracking
      |
      v
Event Lock Removed
      |
      v
Completion Messages
      |
      v
Normal Claiming Available
```

---

# Event Failure

An event can also fail.

When an event fails, Airdrops performs cleanup so the encounter does not remain in an invalid state.

The failure lifecycle can include:

```text
Event Marked Failed
      |
      v
Airdrop Event Lock Removed
      |
      v
Active Boss Removed
      |
      v
Boss Unregistered
      |
      v
Event Removed From Active Tracking
      |
      v
Failure Messages Sent
```

---

# Replacing an Active Event

v3.0.6 includes important cleanup improvements when a new airdrop replaces an existing one.

Previously, replacing an active airdrop could leave its old event boss alive.

This has been fixed.

The new lifecycle is:

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
Old State Cleared
      |
      v
New Airdrop Created
```

This applies to normal and test airdrop creation.

---

# Stopping Active Events

Using:

```text
/airdrop stop
```

now does more than stop automatic scheduling.

v3.0.6 also clears the currently active airdrop lifecycle.

This means an active encounter is cleaned instead of leaving its boss and structure behind.

---

# Common Configuration Mistakes

## Event ID Does Not Match the Tier Pool

If the event contains:

```yaml
id: infernal_invasion
```

the tier must reference:

```yaml
- id: infernal_invasion
  weight: 50
```

---

## Wrong Boss ID

If your boss contains:

```yaml
id: infernal_golem
```

the event must use:

```yaml
boss:
  provider: AIRDROPS
  id: infernal_golem
```

---

## Event Is Not in the Pool

Creating an event YAML does not automatically add it to airdrop selection.

It must be included in:

```yaml
events:
  pool:
```

for the appropriate tier.

---

## Events Are Disabled for the Tier

Check:

```yaml
events:
  enabled: true
```

---

## Event Chance Is Too Low During Testing

When initially testing a new event, using:

```yaml
chance: 100
```

makes testing easier.

Once the event works, change the chance to your desired production value.

---

## Another Event Is Being Selected

If your pool contains multiple events:

```yaml
pool:
  - id: warden_siege
    weight: 50
  - id: infernal_invasion
    weight: 50
```

either event can be selected.

If you specifically want to test one event through the tier system, temporarily give only that event a positive pool entry or use the direct event testing command.

---

## Boss Is Disabled

An enabled event cannot successfully use a native boss definition that is unavailable.

Check the boss file:

```yaml
enabled: true
```

---

## Invalid YAML

YAML indentation matters.

Correct:

```yaml
spawn:
  offset:
    x: 5
    y: 0
    z: 5
```

Incorrect:

```yaml
spawn:
offset:
x: 5
y: 0
z: 5
```

Use spaces rather than tabs.

---

# Warden Siege Example

Boss:

```text
bosses/warden_guardian.yml
```

Event:

```text
events/warden_siege.yml
```

Tier pool:

```yaml
events:
  enabled: true
  chance: 100

  pool:
    - id: warden_siege
      weight: 100
```

Flow:

```text
Legendary Airdrop
      |
      v
warden_siege
      |
      v
warden_guardian
      |
      v
Event Locked
      |
      v
Warden Defeated
      |
      v
Event Complete
      |
      v
Airdrop Unlocked
```

---

# Multiple Event Example

A tier can have:

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

This gives the tier two possible encounters.

The plugin performs weighted selection when the airdrop starts.

This allows servers to build a collection of encounters instead of every Legendary airdrop behaving identically.

---

# Event System Limitations in v3.0.6

v3.0.6 establishes the foundation of the event system.

The current event framework primarily supports boss-based encounters with:

```text
Event definitions
Boss linking
Event locking
Safe boss spawning
Messages
Timeouts
Completion
Failure
Cleanup
Weighted tier selection
```

Multi-stage events, configurable waves, objectives, survival stages, boss abilities and more advanced raid mechanics are planned for future development.

Do not configure undocumented stage or wave sections in v3.0.6 and expect them to function.

---

# Future Direction

The event framework has been designed so future encounters can evolve beyond a single boss.

The planned direction includes structures such as:

```text
Airdrop Event
      |
      v
Stage 1
Mob Wave
      |
      v
Stage 2
Mob Wave
      |
      v
Stage 3
Objective
      |
      v
Final Stage
Boss
      |
      v
Event Complete
      |
      v
Airdrop Unlocked
```

These are future systems and are not being claimed as v3.0.6 functionality.

---

# Summary

To create a custom event:

```text
Create/choose a boss
      |
      v
Create events/your_event.yml
      |
      v
Give the event a unique ID
      |
      v
Reference the boss ID
      |
      v
Configure the spawn offset
      |
      v
Configure messages
      |
      v
Configure timeout
      |
      v
Add the event to config.yml
      |
      v
Run /airdrop reload
      |
      v
Test the event
```

Airdrops v3.0.6 allows server owners to build multiple boss encounters and control when they appear without modifying or recompiling the plugin.
