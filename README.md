
# Minecraft Bedrock: Flying Pig using `minecraft:input_air_controlled`

This is an example of how to enable **flying control for entities** in Minecraft Bedrock Edition using `minecraft:input_air_controlled`. 

# How It Works

## Air Control Component

To enable control in the air, we add the `minecraft:input_air_controlled` component:

Located at **Line ~209** in the `minecraft:pig_saddled` group:

```json
"minecraft:input_air_controlled": {
    "strafe_speed_modifier": 1,
    "backwards_movement_modifier": 0.5
}
```

---
# Bug?

Normally, this component **only works on hover entities** (those with `movement.hover`),
not with typical ground entities using `movement.basic` or `movement.generic`.

We bypass this limitation by dynamically toggling gravity and switching movement-related components using custom events (`fly` and `walk`) when a rider enters or exits the entity.

---

## ON/OFF-ing gravity

Note : this is for non `movement.hover` if your entity already has that component you dont need this

### Walk and Fly Component Groups

Located at **Line ~13** in `component_groups`

```json
"walk": {
    "minecraft:physics": {
        "has_gravity": true
    }
}
"fly": {
    "minecraft:physics": {
        "has_gravity": false
    },
    "minecraft:flying_speed": {
        "value": 0.0833333
    }
}
```
We use this component groups to remove and restore gravity
### Rideable component
Located at **Line ~183** in `component_groups.minecraft:pig_saddled`:

```json
"minecraft:rideable": {
    "seat_count": 1,
    "interact_text": "action.interact.ride.horse",
    "family_types": [
        "player"
    ],
    "seats": {
        "position": [
            0.0,
            0.63,
            0.0
        ]
    },
    "on_rider_enter_event": "fly", // Call fly event on enter
    "on_rider_exit_event": "walk"  // Call walk event on exit
}
```

We use this to call fly and walk events  

#### Fly and Walk Events

Located in the `events` section — Line ~349:

```json
"fly": {
    "add": {
        "component_groups": [
            "fly"
        ]
    },
    "remove": {
        "component_groups": [
            "walk"
        ]
    }
}
"walk": {
    "remove": {
        "component_groups": [
            "fly"
        ]
    },
    "add": {
        "component_groups": [
            "walk"
        ]
    }
}
```

this code is for adding and removing component groups

---

## 🛬 Disable Fall Damage

When dismounted in the air, the pig would fall and take damage. To prevent this, we use `minecraft:damage_sensor` to ignore fall damage.

Defined under `component_groups.minecraft:pig_saddled` — Line ~212:

```json
"minecraft:damage_sensor": {
    "triggers": {
        "cause": "fall",
        "deals_damage": "no"
    }
}
```

---


### what the code do:

- Gravity is removed (via `fly` event) when player ride pig
- `walk` component group is removed
- `fly` component group is added (disabling gravity)

When the rider dismounts the pig:

- Gravity is restored (via `walk` event) when player exit riding pig
- `fly` component group is removed
- `walk` component group is re-added

This enables air control using `minecraft:input_air_controlled` 




---





