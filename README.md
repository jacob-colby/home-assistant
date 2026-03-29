# Daily Driver — Home Assistant Dashboard

A comprehensive, modern Home Assistant dashboard designed for a two-person household. Minimalist dark aesthetic inspired by Apple Home and Mushroom Cards.

---

## File Structure

```
home-assistant/
├── ui-lovelace.yaml                     # Main dashboard (all 6 views)
├── themes/
│   └── daily_driver_dark.yaml          # Custom dark theme
└── packages/
    ├── couple_automations.yaml         # Couple-specific scenes & automations
    └── actionable_notifications.yaml  # Smart push notification engine
```

---

## Dashboard Views

| View | Icon | Purpose |
|------|------|---------|
| **Home** | `mdi:home-variant-outline` | Landing page — weather, status chips, quick lights, climate, scenes |
| **Rooms** | `mdi:floor-plan` | Room-by-room Bubble Card controls |
| **Devices** | `mdi:devices` | Full device inventory, conditional vacuum/laundry cards |
| **Us Two** | `mdi:heart-outline` | Commute times, shared to-do, couple scenes, energy graph |
| **History** | `mdi:chart-line` | Mini-graph sparklines — temp, humidity, power, motion |
| **Settings** | `mdi:cog-outline` | Automation toggles, system metrics, theme selector |

---

## HACS Requirements

Install all of the following via **HACS → Frontend**:

| Card / Integration | Purpose |
|--------------------|---------|
| [Mushroom Cards](https://github.com/piitaya/lovelace-mushroom) | Core card suite — entity, light, climate, person, chips |
| [Bubble Card](https://github.com/Clooos/Bubble-Card) | iOS-style room summary cards |
| [Mini Graph Card](https://github.com/kalkih/mini-graph-card) | Compact sparkline history graphs |
| [Auto Entities](https://github.com/thomasloven/lovelace-auto-entities) | Dynamic entity lists (lights, batteries, etc.) |
| [Card Mod](https://github.com/thomasloven/lovelace-card-mod) | CSS overrides per card |
| [Stack In Card](https://github.com/custom-cards/stack-in-card) | Nest cards without outer gap |
| [Layout Card](https://github.com/thomasloven/lovelace-layout-card) | Masonry / custom grid layouts |
| [Weather Chart Card](https://github.com/mlamberts78/weather-chart-card) | Detailed weather forecast |
| [ApexCharts Card](https://github.com/RomRider/apexcharts-card) | Energy / sensor history charts |
| [Button Card](https://github.com/custom-cards/button-card) | Fully customisable tile buttons |

---

## Setup Instructions

### 1. Enable `ui-lovelace.yaml` mode

Add to `configuration.yaml`:
```yaml
lovelace:
  mode: yaml
  resources:
    # HACS cards — copy exact paths from HACS after install
    - url: /hacsfiles/lovelace-mushroom/mushroom.js
      type: module
    - url: /hacsfiles/bubble-card/bubble-card.js
      type: module
    - url: /hacsfiles/mini-graph-card/mini-graph-card-bundle.js
      type: module
    - url: /hacsfiles/lovelace-auto-entities/auto-entities.js
      type: module
    - url: /hacsfiles/lovelace-card-mod/card-mod.js
      type: module
    - url: /hacsfiles/stack-in-card/stack-in-card.js
      type: module
    - url: /hacsfiles/lovelace-layout-card/layout-card.js
      type: module
    - url: /hacsfiles/weather-chart-card/weather-chart-card.js
      type: module
    - url: /hacsfiles/apexcharts-card/apexcharts-card.js
      type: module
    - url: /hacsfiles/button-card/button-card.js
      type: module
```

### 2. Enable packages

Add to `configuration.yaml`:
```yaml
homeassistant:
  packages: !include_dir_named packages/

frontend:
  themes: !include_dir_merge_named themes/
```

### 3. Create a notify group (for couple notifications)

Add to `configuration.yaml`:
```yaml
notify:
  - platform: group
    name: everyone
    services:
      - service: mobile_app_your_phone      # replace with your app service
      - service: mobile_app_partner_phone   # replace with partner's app service
```

### 4. Apply the theme

In the HA UI: **Profile → Theme → Daily Driver Dark**

Or use the button card in the Settings view, or add to `configuration.yaml`:
```yaml
frontend:
  themes: !include_dir_merge_named themes/
```
Then call the service once:
```yaml
service: frontend.set_theme
data:
  name: "Daily Driver Dark"
```

### 5. Replace entity IDs

Search every file for `REPLACE_ME` and substitute your actual entity IDs. Common ones:

| Placeholder | What to replace with |
|-------------|----------------------|
| `person.you` | Your HA person entity |
| `person.partner` | Partner's person entity |
| `climate.thermostat` | Your climate device |
| `lock.front_door` | Your lock entity |
| `light.living_room` / `light.bedroom` etc. | Your light entities or groups |
| `media_player.living_room` | TV / speaker media player |
| `vacuum.roomba` | Robot vacuum |
| `binary_sensor.washer_active` | Smart plug power sensor (use template binary sensor if needed) |
| `sensor.your_commute_time` | Waze or Google Travel Time integration sensor |
| `notify.mobile_app_your_phone` | HA Companion app notify service |

---

## Couple-Specific Automations

### Included scenes
| Scene | Lights | Climate | Notes |
|-------|--------|---------|-------|
| **Wake Up** | Bedroom 40%, warm | 70°F auto | Triggered by morning alarm helper |
| **Dinner Time** | Kitchen 90%, dining 80% | — | Actionable notification at 5:30 PM |
| **Movie Time** | Living room 10% dim blue | — | Auto-triggers when TV plays after 7 PM |
| **Date Night** | Dim pink/red ambience | 72°F | Manually triggered |
| **Good Night** | All off, bedroom 5% | 68°F | Runs goodnight script + lock check |
| **Away** | All off | 62°F eco | Both persons absent for 5+ min |

### Quality-of-life automations
1. **Welcome Home** — first person arriving runs welcome routine
2. **Auto Away Mode** — both gone 5 min → lock, eco temps, all off
3. **Adaptive Morning Lights** — gentle sunrise simulation at alarm time
4. **Partner ETA** — notify when partner is ~10 min out (Waze)
5. **Movie Auto Dim** — TV starts playing after 7 PM → dim lights automatically
6. **Dinner Reminder** — actionable notification at 5:30 PM with Dinner Scene button
7. **Guest Mode** — toggle to suspend personal automations (morning alarm, dinner reminder)
8. **Vacation Mode** — random light cycling to simulate occupancy

---

## Actionable Notifications

| Notification | Trigger | Actions |
|-------------|---------|---------|
| 🌀 **Dryer Done** | Dryer powers off | Keep Warm 15 min / Dismiss |
| 🫧 **Washer Done** | Washer powers off | Start Dryer / Dismiss |
| 🔓 **Door Unlocked** | Unlocked >10 min while away | Lock Now / Ignore |
| 🌧️ **Rain + Open Window** | Rain forecast + window open | Alert only |
| 💡 **Lights Left On** | Both away, lights still on | Turn All Off / Leave On |
| 🔋 **Low Battery** | Daily 9 AM check, device <15% | Alert only |
| 🥵/🥶 **Extreme Temp** | Indoor >82°F or <60°F | Set Auto Mode / Dismiss |
| 🌅 **Morning Brief** | Alarm time + 5 min | Weather, commute, light check |

---

## Recommended Integrations

| Integration | Purpose |
|-------------|---------|
| [Waze Travel Time](https://www.home-assistant.io/integrations/waze_travel_time/) | Live commute sensor |
| [HA Companion App](https://companion.home-assistant.io/) | Push notifications & presence |
| [HACS](https://hacs.xyz) | Frontend card management |
| [Mushroom Themes](https://github.com/piitaya/lovelace-mushroom) | Theme variables |
| [Local Calendar](https://www.home-assistant.io/integrations/local_calendar/) | Shared couple calendar |
| [Shopping List](https://www.home-assistant.io/integrations/shopping_list/) or **To-do** | Shared grocery list |
| [Adaptive Lighting](https://github.com/basnijholt/adaptive-lighting) | Auto color temp throughout day |
| [Browser Mod](https://github.com/thomasloven/hass-browser_mod) | Tablet kiosk mode, popup dialogs |
