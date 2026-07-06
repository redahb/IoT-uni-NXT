# IoT-uni-NXT

A universal dongle for UART-controlled IoT appliances, designed around the [Seeed Studio XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html).

This is a fork of [dudanov/iot-uni-dongle](https://github.com/dudanov/iot-uni-dongle) by [Sergey Dudanov](https://github.com/dudanov), who designed the original board and did the reverse-engineering work behind the [MideaUART](https://github.com/dudanov/MideaUART) protocol that this project (and its ESPHome `midea` climate component) relies on. All credit for the original concept goes to him.

## Why this fork exists

The original design uses an [ESP12-F](https://docs.ai-thinker.com/_media/esp8266/docs/esp-12f_product_specification_en.pdf) module (ESP8266). It's a proven, inexpensive, and reliable choice, but it's also over a decade old at this point. Single core, no Bluetooth, limited RAM, and no way to run modern [ESPHome](https://esphome.io) features that need more headroom.

This fork replaces it with a [Seeed Studio XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html):

- Dual-band Wi-Fi 6 (2.4 GHz and 5 GHz) instead of single-band 802.11n.
- Bluetooth LE 5.4, plus 802.15.4 (Thread/Zigbee) radio hardware.
- Significantly more RAM and 8 MB of PSRAM, versus the ESP8266's very tight memory budget.
- Enough headroom to run ESPHome's `bluetooth_proxy`, or presence-detection setups like [Bermuda BLE Trilateration](https://github.com/agittins/bermuda) or [TOMMY presence tracking](https://www.tommysense.com/) alongside the AC control.

Air conditioners are powered 24/7, even when they are not actively cooling or heating, which makes the inside of an AC unit's control compartment an excellent, physically hidden location for an always-on ESP32 node. This effectively gives you a Bluetooth proxy or presence-tracking node in the room "for free", on hardware that's already there for another reason.

## Main goals

1. A unified design for UART-controlled devices from different manufacturers.
2. Support for 2.54 mm-pitch connectors (like USB-A).
3. Configurable TX/RX pin order on the UART connector.
4. IR transmission and reception.
5. Minimal design, built around the XIAO form factor.
6. Using the [Seeed Studio XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html) module, based on the ESP32-C5 SoC.

[![Assembled PCB - Front](images/thumbs/assembled-front.png)](images/assembled-front.png) [![Assembled PCB - Back](images/thumbs/assembled-back.png)](images/assembled-back.png)

A non-exhaustive list of supported brands:
1. [Midea](https://www.midea.com)
2. [Electrolux](https://www.electrolux.com)
3. [Qlima](https://www.qlima.com)
4. [Artel](https://www.artelgroup.com)
5. [Carrier](https://www.carrier.com)
6. [Comfee](https://www.feelcomfee.com)
7. [Inventor](https://www.inventorairconditioner.com)
8. [Dimstal/Simando](https://www.simando24.de)

## Connector mounting examples

[![USB-A Connector](images/thumbs/connector-usba.png)](images/connector-usba.png) [![JST Connector](images/thumbs/connector-jst.png)](images/connector-jst.png) [![Pinheader](images/thumbs/connector-pinheader.png)](images/connector-pinheader.png)

## TX/RX signal routing

To choose the TX/RX pin order, bridge the appropriate solder jumpers:

[![RX-TX Midea](images/thumbs/rxtx-midea.png)](images/rxtx-midea.png) [![RX-TX Haier](images/thumbs/rxtx-haier.png)](images/rxtx-haier.png)

## Sending and receiving IR remote control commands

Because the UART protocol does not expose all AC features (e.g. display control and the `FollowMe` feature), the board also supports sending IR commands by feeding a demodulated IR signal without carrier modulation to a dedicated GPIO on the ESP32.
To do this, connect the `IR+` pad on the bottom side of the board to the input of the TSOP IR receiver/demodulator on the AC board.
The pictures below show an example for a TSOP1738 IR receiver.

[![TSOP IoT-uni-NXT Side](images/thumbs/tsop-dongle.png)](images/tsop-dongle.png) [![TSOP AC Side](images/thumbs/tsop-display.png)](images/tsop-display.png)

The design also supports reading the IR signal from any remote control (including third-party ones) on a separate GPIO, through its own level shifter. This can help with protocol research and other automation tasks without resorting to additional devices.

## SMT assembly on JLCPCB

The [single-smt](jlcpcb/single-smt) directory contains the files required to manufacture and assemble the board at [JLCPCB](https://jlcpcb.com).

The boards you receive from JLCPCB will look like this:

[![Unpopulated PCB Front](images/thumbs/factory-front.png)](images/factory-front.png) [![Unpopulated PCB Back](images/thumbs/factory-back.png)](images/factory-back.png)

After assembly, solder the XIAO module and the required connector to the back of the board, then configure the TX/RX pin order for your device.