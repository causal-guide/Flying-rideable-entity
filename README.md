
# Minecraft Bedrock: Flying Pig using `minecraft:input_air_controlled`

This is an example of how to enable **flying control for walking entities** (like pigs) in Minecraft Bedrock Edition using `minecraft:input_air_controlled`. Normally, this component **only works on hover entities** (those with `movement.hover`), not with typical ground entities using `movement.basic` or `movement.generic`.

We bypass this limitation by dynamically toggling gravity and switching movement-related components using custom events (`fly` and `walk`) when a rider enters or exits the entity.

---

## 🚀 How It Works

### 🔁 Dynamic Gravity Toggle

When the rider mounts the pig:

- Gravity is removed (via `fly` event)
- `walk` component group is removed
- `fly` component group is added (disabling gravity)

When the rider dismounts the pig:

- Gravity is restored (via `walk` event)
- `fly` component group is removed
- `walk` component group is re-added

This enables air control using `minecraft:input_air_controlled` for a flying experience.

---

## 🐷 Full Rideable Component

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

---

## 🧠 Enabling Flight with Component Groups

Defined under `component_groups`:

### ✈️ Fly Group — Line ~18

```json
"fly": {
    "minecraft:physics": {
        "has_gravity": false
    },
    "minecraft:flying_speed": {
        "value": 0.0833333
    }
}
```

### 🚶 Walk Group — Line ~13

```json
"walk": {
    "minecraft:physics": {
        "has_gravity": true
    }
}
```

---

## 🧩 Fly and Walk Events

Located in the `events` section — Line ~349:

### 📤 Fly Event

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
```

### 📥 Walk Event

```json
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

## 🎮 Air Control Component

To enable control in the air, we add the `minecraft:input_air_controlled` component:

Located at **Line ~209** in the `minecraft:pig_saddled` group:

```json
"minecraft:input_air_controlled": {
    "strafe_speed_modifier": 1,
    "backwards_movement_modifier": 0.5
}
```

---

## ✅ Summary

| Feature                        | Enabled by                              |
|-------------------------------|------------------------------------------|
| Air Control                   | `minecraft:input_air_controlled`        |
| Toggle Gravity                | `fly` and `walk` events                  |
| Disable Fall Damage           | `minecraft:damage_sensor`               |
| Player Ride & Fly             | `minecraft:rideable` with enter/exit events |

This setup allows standard walking mobs to be controlled mid-air, creating a "hover pig" without switching the entity to `movement.hover`.

---
