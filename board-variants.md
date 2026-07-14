# Board variants

IoT-uni-NXT is designed around the Seeed Studio XIAO ESP32 form factor. The same PCB can be assembled with a [XIAO ESP32-C3](https://www.seeedstudio.com/Seeed-XIAO-ESP32C3-p-5431.html?sensecap_affiliate=y1Qg8fB&referring_service=link), [XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html?sensecap_affiliate=y1Qg8fB&referring_service=link), [XIAO ESP32-C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html?sensecap_affiliate=y1Qg8fB&referring_service=link) or [XIAO ESP32-S3](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html?sensecap_affiliate=y1Qg8fB&referring_service=link).

The hardware choice is made during assembly: solder the XIAO module you want to use. The firmware choice is made in ESPHome: use the matching `template-xiao-**.yaml` file from this repository.

## Supported XIAO modules

| Module | CPU | Flash | RAM | PSRAM | Wi-Fi | BT | Thread | Zigbee | Antenna |
|---|---|---|---|---|---|---|---|---|---|
| [ESP32-C3](https://www.seeedstudio.com/Seeed-XIAO-ESP32C3-p-5431.html?sensecap_affiliate=y1Qg8fB&referring_service=link)[^1] | 1x RISC-V 160Mhz | 4MB | 400KB | ✗ | Wi-Fi 4<br/>(2.4GHz) | 5.0 | ✗ | ✗ | U.FL |
| [ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html?sensecap_affiliate=y1Qg8fB&referring_service=link) | 1x RISC-V 240Mhz,<br/>1x RISC-V 48Mhz | 8MB | 384KB | 8MB | Wi-Fi 6<br/>(2.4GHz, 5GHz) | 5.0 | ✓ | ✓ | U.FL |
| [ESP32-C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html?sensecap_affiliate=y1Qg8fB&referring_service=link) | 1x RISC-V 160Mhz,<br/>1x RISC-V 20MHz | 4MB | 512KB | ✗ | WiFi 6<br/>(2.4GHz) | 5.3 | ✓ | ✓ | Onboard,<br/>U.FL |
| [ESP32-S3](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html?sensecap_affiliate=y1Qg8fB&referring_service=link) | 2x Xtensa LX7 240Mhz | 8MB | 512KB | 8MB | WiFi 4<br/>(2.4GHz) | 5.0 | ✗ | ✗ | U.FL |


## Which one should I use?

Choose the **XIAO ESP32-C3** if you want the simplest and most inexpensive build, mainly for basic UART AC control with a light ESPHome configuration.

Choose the **XIAO ESP32-C5** if you want the broadest wireless feature set in one module: dual-band 2.4/5 GHz Wi-Fi 6, Bluetooth LE, 802.15.4 radio hardware, and PSRAM.

Choose the **XIAO ESP32-C6** if you want 2.4 GHz Wi-Fi 6 and a 802.15.4 radio hardware for Thread, Zigbee, or Matter-related experiments, but do not need 5 GHz Wi-Fi or extra PSRAM.

Choose the **XIAO ESP32-S3** if your priority is CPU and memory headroom for heavier ESPHome configurations, AI calculations, and you do not need 5 GHz Wi-Fi or Zigbee/Thread.

## Important notes

- The PCB uses the XIAO form factor, but the ESP32 GPIO mapping differs per module.
- Do not reuse the ESPHome template for another module.
- The UART connector, TX/RX solder jumpers, IR output, IR input, and connector options are part of the IoT-uni-NXT PCB and do not change between variants.
- The selected XIAO module determines wireless capabilities, available memory, and which ESPHome features are realistic to run alongside AC control.

## Sources

- [Seeed Studio XIAO ESP32-C3 wiki](https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/)
- [Seeed Studio XIAO ESP32-C5 wiki](https://wiki.seeedstudio.com/xiao_esp32c5_getting_started/)
- [Seeed Studio XIAO ESP32-C6 wiki](https://wiki.seeedstudio.com/xiao_esp32c6_getting_started/)
- [Seeed Studio XIAO ESP32-S3 wiki](https://wiki.seeedstudio.com/xiao_esp32s3_getting_started/)