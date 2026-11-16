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
| Day | morning cutoff to sunset | 100% | 15 min |
| Sunset | sunset to late-night start | 60% | 5 min |
| Late night | late-night start (23:00) to sunrise | 10% | 2 min |
| Morning | sunrise to morning cutoff (08:00) | 30% | 5 min |

Sunset and sunrise can each be shifted by an offset in minutes.

### Behavior

- **Manual override:** brightness is only set when the light is off. If the
  light is already on when motion is detected (for example dimmed from the
  wall), it is left at its current level.
- **Timeout:** after motion clears, the light turns off once the timeout for
  the window in effect at that moment has elapsed. New motion restarts the
  automation and cancels a pending off.
- **Sunrise after the morning cutoff** (winter, or a late cutoff offset): the
  morning window is empty and the late-night window runs until sunrise, then
  the day window applies.
- The light must report its state to Home Assistant (a smart dimmer or smart
  bulb). A switch that only cuts power to the bulb is not supported.
- Requires `sun.sun`, Home Assistant 2024.10 or newer (collapsible input
  sections, `triggers:`/`actions:` syntax).
