# ESPHome configuration

This page explains the ESPHome template used for the IoT-uni-NXT dongle.

Use the template that matches the XIAO module you soldered to the board:

- `template-xiao-c3.yaml` for [Seeed Studio XIAO ESP32 C3](https://www.seeedstudio.com/Seeed-XIAO-ESP32C3-p-5431.html?sensecap_affiliate=y1Qg8fB&referring_service=link)
- `template-xiao-c5.yaml` for [Seeed Studio XIAO ESP32 C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html?sensecap_affiliate=y1Qg8fB&referring_service=link)
- `template-xiao-c6.yaml` for [Seeed Studio XIAO ESP32 C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html?sensecap_affiliate=y1Qg8fB&referring_service=link)
- `template-xiao-s3.yaml` for [Seeed Studio XIAO ESP32-S3](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html?sensecap_affiliate=y1Qg8fB&referring_service=link)

The main differences between these templates are the ESPHome board settings and the GPIO pins used for UART and optional IR features. Do not copy GPIO numbers from one XIAO variant to another unless you have verified the pin mapping.

Lines starting with `#` are disabled by default. They are optional examples for extra features such as IR transmission, IR reception, FollowMe updates, display control, and swing control. To enable one of these blocks, remove the leading `#` characters and replace the placeholders.

## Placeholders

The template contains several `**********` placeholders. Replace them with values that make sense for your installation.

Common examples:

```yaml
esphome:
  name: bedroom-ac
  friendly_name: Bedroom AC
```

Use a short, lowercase `name` because this becomes the ESPHome node name and usually also the network hostname. Use `friendly_name` for the human-readable name shown in Home Assistant.

## Device identity

```yaml
esphome:
  name: "**********"
  friendly_name: "**********"
```

This defines the ESPHome device name.

- `name` is the internal ESPHome node name.
- `friendly_name` is the display name used by Home Assistant.

Good examples for `name` are `bedroom-ac`, `living-room-ac`, or `office-ac`.

### Temporary external component for ESP-IDF UART support

When using the ESP-IDF framework, UART support for the `midea` climate component currently requires [a pending ESPHome change](https://github.com/esphome/esphome/pull/12646). Until that change has been merged into the official ESPHome codebase, the YAML template uses an external component source:

```yaml
external_components:
  - source: github://pr#12646
    components: [midea]
    refresh: 1d
```

This tells ESPHome to load the `midea` component from pull request [`#12646`](https://github.com/esphome/esphome/pull/12646) instead of using the version bundled with the installed ESPHome release.

Once the pull request has been merged and the change is available in a regular ESPHome release, this block should no longer be needed and can be removed from the YAML configuration.

## ESP32 board configuration

```yaml
esp32:
  board:
  variant:
  framework:
    type: esp-idf

psram:
  mode:
  speed:
  ignore_not_found:
```

**DO NOT MODIFY THIS**. This tells ESPHome which ESP32 variant it is compiling for.

## Home Assistant API

```yaml
api:
  encryption:
    key: "**********"
```

This enables the native ESPHome API used by Home Assistant.

The `encryption` key secures communication between the ESPHome node and Home Assistant. ESPHome usually generates this key automatically when you create a new device, or you can generate one manually at [https://esphome.io/components/api](https://esphome.io/components/api).

You can copy the generated key from your ESPHome dashboard configuration.

## Wi-Fi configuration

```yaml
wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  min_auth_mode: WPA2
  ap:
    ssid: "**********"
    password: !secret ap_password
```

This connects the IoT-uni-NXT dongle to your Wi-Fi network.

- `ssid` and `password` are read from your ESPHome `secrets.yaml` file.
- `min_auth_mode: WPA2` prevents the device from connecting to networks using weaker authentication.
- `ap` defines the fallback access point that appears if the device cannot connect to your Wi-Fi. Name the ssid in this section something you can recognize like `AC Fallback Access Point`.

## Captive portal

```yaml
captive_portal:
```

The captive portal is used together with the fallback access point.

If the dongle cannot connect to your Wi-Fi, it starts its own temporary Wi-Fi network. You can connect to that network and use the captive portal to enter new Wi-Fi credentials.

## OTA updates

```yaml
ota:
  - platform: esphome
    password: !secret ota_password
```

This enables wireless firmware updates through ESPHome.

After the first USB flash, you can normally update the firmware over Wi-Fi. The `ota_password` protects the device from unauthorized OTA updates.

## Logger

```yaml
logger:
  hardware_uart: USB_SERIAL_JTAG / USB_CDC
```

**DO NOT MODIFY THIS**. The logger sends ESPHome log output over USB. This is important because the main UART pins are used for communication with the air conditioner. Using `USB_SERIAL_JTAG` or `USB_CDC` keeps logging separate from the UART connection to the AC.

## UART connection to the air conditioner

```yaml
uart:
  tx_pin:
  rx_pin:
  baud_rate: 9600
```

**DO NOT MODIFY THIS**. This defines the UART connection between the XIAO module and the air conditioner.

- `tx_pin` is the ESP32 transmit pin.
- `rx_pin` is the ESP32 receive pin.
- `baud_rate: 9600` is used by the Midea-compatible UART protocol.

The IoT-uni-NXT dongle has solder jumpers for selecting the physical TX/RX pin order on the connector. Use those solder jumpers to match your AC unit's connector pinout.

## Midea climate component

```yaml
climate:
  - platform: midea
    name: **********
    autoconf: true
    beeper: false
    visual:
      min_temperature: 16
      max_temperature: 30
      temperature_step: 0.5
    outdoor_temperature:
      name: **********
    power_usage:
      name: **********
    humidity_setpoint:
      name: **********
```

This creates the air conditioner entity in Home Assistant using ESPHome's Midea UART climate component.

### `name`

```yaml
name: **********
```

This is the name of the climate entity in Home Assistant.

Example:

```yaml
name: Bedroom AC
```

### `autoconf`

```yaml
autoconf: true
```

This lets ESPHome query the air conditioner and automatically detect supported features where possible.

### `beeper`

```yaml
beeper: false
```

This disables beep feedback for supported commands.

Some AC units still beep for certain commands or for commands sent through other paths. Behavior depends on the indoor unit firmware.

### Visual temperature range

```yaml
visual:
  min_temperature: 16
  max_temperature: 30
  temperature_step: 0.5
```

This controls how the climate entity is displayed in Home Assistant.

It does not force the AC to support temperatures outside its own firmware limits. It only tells Home Assistant which range and step size to show in the UI.

### Optional AC-reported sensors

```yaml
outdoor_temperature:
  name: **********
power_usage:
  name: **********
humidity_setpoint:
  name: **********
```

These create extra Home Assistant entities if the AC reports the data.

- `outdoor_temperature` exposes the outdoor temperature reported by the AC, if available.
- `power_usage` exposes the current power usage reported by the AC, if available.
- `humidity_setpoint` exposes the humidity setpoint where supported. This is experimental in ESPHome.

Not all indoor units support all of these values. If your AC does not report a value, the entity may stay unavailable or not be useful.

## Optional: IR transmitter

Disabled by default:

```yaml
#remote_transmitter:
#  pin: GPIO6
#  carrier_duty_percent: 100%
```

Enable this block if you connect the IoT-uni-NXT dongle's IR+ output to the AC's IR receiver path.

This is mainly useful for Midea features that are normally controlled through IR commands, such as FollowMe, display control, and louver step control.

Important: this configuration is intended for a direct logic-level connection to the AC's IR receiver/demodulator path, not for directly driving a normal IR LED. That is why `carrier_duty_percent` is set to `100%`.

## Optional: IR receiver

Disabled by default:

```yaml
#remote_receiver:
#  pin:
#    number: GPIO5
#    inverted: true
#    mode:
#      input: true
#      pullup: true
#  dump: all
```

Enable this block if you want the dongle to read IR remote control signals.

This can be useful for:

- researching the IR protocol used by your AC remote;
- logging commands from the original remote;
- detecting commands from third-party remotes;
- building Home Assistant automations based on remote control input.

### `inverted: true`

Most TSOP-style IR receiver/demodulator outputs are active-low: the output is normally high and goes low when an IR pulse is detected.

`inverted: true` tells ESPHome to interpret that correctly.

### `pullup: true`

This enables the ESP32's internal pull-up resistor on the receiver input.

It helps keep the input stable when no IR signal is present.

### `dump: all`

This tells ESPHome to print decoded IR signals to the logs.

This is useful while testing or reverse-engineering, but it can create a lot of log output. Once you know which protocol and codes you need, you may want to replace `dump: all` with a more specific configuration or disable it again.

## Optional: FollowMe using a Home Assistant sensor

Disabled by default:

```yaml
#sensor:
#  - platform: homeassistant
#    entity_id: **********
#    id: **********
#    internal: true
#    filters:
#      - throttle: 10s
#      - heartbeat: 30s
#      - debounce: 1s
#    on_value:
#      midea_ac.follow_me:
#        temperature: !lambda "return x;"
#        beeper: false
```

This block imports a temperature sensor from Home Assistant and sends its value to the AC using the Midea FollowMe command.

FollowMe tells the air conditioner to use a room temperature value from another location instead of relying only on the indoor unit's internal temperature sensor. Officially this can be done by placing the remote somewhere away from the air conditioner. This ESPHome function pretends to be a remote control.

Typical use cases:

- using a temperature sensor near the couch, desk, or bed;
- compensating for an indoor unit that measures temperature too close to the ceiling;
- improving comfort in larger rooms.

### `entity_id`

```yaml
entity_id: **********
```

Replace this with the Home Assistant entity ID of your temperature sensor.

Example:

```yaml
entity_id: sensor.bedroom_temperature
```

### `id`

```yaml
id: **********
```

This is the internal ESPHome ID for the imported sensor.

Example:

```yaml
id: bedroom_temperature
```

### `internal: true`

```yaml
internal: true
```

This keeps the imported sensor internal to ESPHome.

The sensor already exists in Home Assistant, so there is usually no need to expose it back to Home Assistant as a second entity.

### Filters

```yaml
filters:
  - throttle: 10s
  - heartbeat: 30s
  - debounce: 1s
```

These filters prevent excessive FollowMe updates.

- `throttle: 10s` limits how often new values are passed on.
- `heartbeat: 30s` sends a periodic update even if the value has not changed.
- `debounce: 1s` filters out very short unstable changes.

You can increase these values if you want fewer updates.

### FollowMe action

```yaml
on_value:
  midea_ac.follow_me:
    temperature: !lambda "return x;"
    beeper: false
```

Whenever the imported sensor value updates, ESPHome sends that temperature to the AC.

- `temperature: !lambda "return x;"` sends the current sensor value.
- `beeper: false` prevents beep feedback on each FollowMe update where supported.

This optional block requires `remote_transmitter` to be enabled, because ESPHome sends FollowMe as an IR command.

## Optional: template buttons

Disabled by default:

```yaml
#button:
#  - platform: template
#    name: **********
#    icon: mdi:theme-light-dark
#    on_press:
#      midea_ac.display_toggle:
#  - platform: template
#    name: **********
#    icon: mdi:tailwind
#    on_press:
#      midea_ac.swing_step:
```

This block creates extra buttons in Home Assistant.

### Display toggle button

```yaml
midea_ac.display_toggle:
```

This toggles the AC display or indicator light, if supported by your unit.

Example name:

```yaml
name: Bedroom AC Display Toggle
```

Depending on the AC model, this may work over UART or require the optional IR transmitter.

### Swing step button

```yaml
midea_ac.swing_step:
```

This moves the louver by one step.

Example name:

```yaml
name: Bedroom AC Swing Step
```

This action requires the optional IR transmitter.

## What to (not) change

For a basic installation, you need to change:

1. `esphome.name`
2. `esphome.friendly_name`
3. the climate `name`
4. the names of any extra sensors you want to expose
5. the API encryption key generated by ESPHome
6. your Wi-Fi and OTA secrets in `secrets.yaml`

Do not change:

- the UART baud rate
- the UART GPIO pins
- the logger UART setting
- the IR GPIO pins

## Minimal configuration

For basic AC control over UART, the required parts are:

- `esphome`
- `esp32`
- `api`
- `wifi`
- `ota`
- `logger`
- `uart`
- `climate`

## Further reading

- [ESPHome Midea climate component](https://esphome.io/components/climate/midea)
- [ESPHome remote transmitter](https://esphome.io/components/remote_transmitter)
- [ESPHome remote receiver](https://esphome.io/components/remote_receiver)
- [ESPHome logger component](https://esphome.io/components/logger)