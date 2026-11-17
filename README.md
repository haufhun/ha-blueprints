# ha-blueprints

Personal Home Assistant blueprints. They contain no secrets or entity IDs.

## Motion Light - Sun & Time Schedule

[![Open your Home Assistant instance and show the blueprint import dialog.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fhaufhun%2Fha-blueprints%2Fblob%2Fmain%2Fautomation%2Fmotion_light_sun_schedule.yaml)

Import URL:
`https://github.com/haufhun/ha-blueprints/blob/main/automation/motion_light_sun_schedule.yaml`

Turns a light on with motion and off after no motion. Brightness and the
no-motion timeout are configurable per window:

| Window | Runs | Default brightness | Default timeout |
| --- | --- | --- | --- |
| Morning | sunrise to morning cutoff (08:00) | 30% | 5 min |
| Day | morning cutoff to sunset | 100% | 15 min |
| Sunset | sunset to wind-down start | 60% | 5 min |
| Wind-down | 3 h after sunset to late-night start | 30% | 3 min |
| Late night | late-night start (22:00) to sunrise | 10% | 2 min |

Sunset and sunrise can each be shifted by an offset in minutes. Wind-down only
exists when it starts before late night, so it appears in winter (sunset about
17:00 gives wind-down at about 20:00) and does not occur in summer (sunset
about 21:00 would put it after 22:00).

### Behavior

- **Manual override:** brightness is only set when the light is off. If the
  light is already on when motion is detected (for example dimmed from the
  wall), it is left at its current level.
- **Full brightness:** double-tapping the upper paddle sets 100%. A light at
  full brightness (double-tap, or set at the wall) uses the full-brightness
  timeout (default 15 min) when motion clears, so a shower is not cut short.
  The rule looks at the light's brightness when motion clears, so it holds
  across the automation restarting on new motion.
- **Timeout:** after motion clears, the light turns off once the timeout has
  elapsed. New motion restarts the automation and cancels a pending off.
- **Sunrise after the morning cutoff** (winter, or a late cutoff offset): the
  morning window is empty and the late-night window runs until sunrise, then
  the day window applies.
- The light must report its state and brightness to Home Assistant (a smart
  dimmer or smart bulb). A switch that only cuts power to the bulb is not
  supported. The switch must be a Z-Wave central-scene dimmer that emits
  `zwave_js_value_notification` events.
- Requires `sun.sun` and Home Assistant 2024.10 or newer.

## Switch Gestures - Enable/Disable Automation With Light Feedback

Import URL:
`https://github.com/haufhun/ha-blueprints/blob/main/automation/switch_toggle_automation.yaml`

Companion to the motion blueprint, one instance per Z-Wave dimmer switch.

- Triple-tap the **upper** paddle: enables the target automation; the light
  blinks 3 times quickly.
- Triple-tap the **lower** paddle: disables the target automation; the light
  blinks twice, slowly.
- The light is restored to its prior state after the blinks.

This is a separate automation because a disabled automation cannot hear the
tap that turns it back on. The enabled/disabled state survives restarts.
