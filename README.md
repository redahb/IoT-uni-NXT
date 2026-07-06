# IoT-uni-NXT

A universal stick for various IoT appliances controlled via UART redesigned around the [Seeed Studio XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html).

![1](images/main.gif)

This is a fork of [dudanov/iot-uni-dongle](https://github.com/dudanov/iot-uni-dongle) by [Sergey Dudanov](https://github.com/dudanov), who designed the original board and did the reverse-engineering work behind the [MideaUART](https://github.com/dudanov/MideaUART) protocol that this project (and its ESPHome `midea` climate component) relies on. All credit for the original concept, PCB, and protocol work goes to him - this fork only swaps the microcontroller and level-shifting hardware.

## Why this fork exists

The original design uses an [ESP12-F](https://docs.ai-thinker.com/_media/esp8266/docs/esp-12f_product_specification_en.pdf) module (ESP8266). It's a proven, cheap, and reliable choice, but it's also over a decade old at this point: single core, no Bluetooth, limited RAM, and no way to run modern ESPHome features that need more headroom.

This fork replaces it with a Seeed Studio XIAO ESP32-C5:

- Dual-band Wi-Fi 6 (2.4 GHz and 5 GHz) instead of single-band 802.11n.
- Bluetooth LE 5.4, plus 802.15.4 (Thread/Zigbee) radio hardware.
- Significantly more RAM and 8 MB of PSRAM, versus the ESP8266's very tight memory budget.
- Enough headroom to run ESPHome's `bluetooth_proxy`, or presence-detection setups like [Bermuda BLE Trilateration](https://github.com/agittins/bermuda) or [TOMMY presence tracking](https://www.tommysense.com/) alongside the AC control.

Air conditioners are powered 24/7 even when not active which makes the inside of an AC unit's control compartment a genuinely good, physically hidden spot for an always-on ESP32 node. You get a Bluetooth proxy or presence-tracking point in the room "for free", on hardware that's already there for another reason.

## Main goals

1. Unified design for all types of home devices from different manufacturers.
2. Use of various connectors with a pitch of 2.54 mm and USB.
3. Possibility of swapping the TX/RX contacts on the UART connector.
4. IR transmission and receiving.
5. Minimalistic design, built around the XIAO castellated-pin form factor.
6. Using the [Seeed Studio XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html) module, based on the ESP32-C5 SoC.

![1](images/view01.jpg) ![1](images/view02.jpg)

A far from complete list of supported brands:
1. [Midea](https://www.midea.com)
2. [Electrolux](https://www.electrolux.com)
3. [Qlima](https://www.qlima.com)
4. [Artel](https://www.artelgroup.com)
5. [Carrier](https://www.carrier.com)
6. [Comfee](https://www.feelcomfee.com)
7. [Inventor](https://www.inventorairconditioner.com)
8. [Dimstal/Simando](https://www.simando24.de)

## Socket mounting examples

![1](images/sockets01.jpg) ![1](images/sockets02.jpg)

## Signal lines TX/RX

To select the location of the signal contacts, it is necessary to close the jumper platforms with drops of solder (in the photos is marked in red).

![1](images/midea.png) ![2](images/haier.png)

## Sending and receiving IR remote control commands

Because not all AC capabilities are implemented in the UART protocol (for example, indication control and the `FollowMe` feature), the board also supports sending IR commands by supplying a demodulated signal (duty: 100%) to a dedicated GPIO on the ESP32.
To do this, connect the `IR-` pad located on the top side of the stick and the output of the TSOP IR demodulator on the display board.
The pictures below show an example for a `TSOP1738` IR receiver.

![2](images/tsop_stick.jpg)

![2](images/tsop_display.jpg)

The design also supports reading the IR signal from any remote control (including third-party ones) on a separate GPIO, through its own level shifter. This can help with researching protocols and other automation goals without resorting to additional devices.

## SMT assembly on JLCPCB

The [single-smt](jlcpcb/single-smt) directory contains the files necessary for manufacturing and assembling the board at [JLCPCB](https://jlcpcb.com).

The received order will look like this:

![1](images/smt.png) ![2](images/smt_after.png)

You just have to solder the XIAO module and the required connector on the back of the board, and select the position of the signal contacts depending on your device.