# Flashing the IoT-uni-NXT

This guide explains how to install ESPHome firmware on a Seeed Studio XIAO ESP32 board and how to update it later over the network.

The instructions apply to the supported [XIAO ESP32-C3](https://www.seeedstudio.com/Seeed-XIAO-ESP32C3-p-5431.html?sensecap_affiliate=y1Qg8fB&referring_service=link), [XIAO ESP32-C5](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C5-p-6609.html?sensecap_affiliate=y1Qg8fB&referring_service=link), [XIAO ESP32-C6](https://www.seeedstudio.com/Seeed-Studio-XIAO-ESP32C6-p-5884.html?sensecap_affiliate=y1Qg8fB&referring_service=link) and [XIAO ESP32-S3](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html?sensecap_affiliate=y1Qg8fB&referring_service=link) variants.

## Before you start

You need:

- a supported Seeed Studio XIAO ESP32 board;
- a USB-C **data cable**;
- a computer with a free USB port;
- one of the ESPHome YAML templates from this repository;
- your Wi-Fi credentials and, where applicable, the ESPHome API and OTA credentials.

> [!IMPORTANT]
> Some USB-C cables can only supply power and cannot transfer data. If the XIAO powers on but is not detected by the computer, try a known-good data cable and another USB port.

## 1. Select and configure a template

Use the template in this repository that matches your XIAO model.

Before compiling, copy the template and adjust at least:

- the ESPHome device name;
- the friendly name;
- the Wi-Fi configuration;
- API encryption credentials;
- OTA credentials;
- any optional features you want to enable.

Do not use firmware compiled for a different ESP32 variant. For example, firmware compiled for an ESP32-C3 must not be installed on an ESP32-C5, C6 or S3.

See the ESPHome template documentation in this repository for a step-by-step explanation of the available settings.

## 2. Prepare the hardware for the first flash

The first installation must be performed through the USB-C connector on the XIAO itself.

1. Remove the IoT-uni-NXT PCB from any USB-A port.
2. Disconnect anything attached to the UART pins, including external 5V power.
3. Connect the USB-C cable directly between the XIAO and your computer.

> [!WARNING]
> Do not power the board through the UART-side 5V connection while it is also connected through USB-C.
>
> For the first flash, use **only the USB-C connector on the XIAO**.

## 3. Compile the firmware

The recommended method is the ESPHome Device Builder in Home Assistant.

1. Open ESPHome Device Builder.
2. Import or create a device using the matching YAML template.
3. Validate the configuration.
4. Open the device menu and select **Install**.
5. Choose either direct installation through the browser or **Manual download**.

For installation with an external flashing tool, select **Manual download** and download the **factory format** firmware. The downloaded file is normally named `firmware.factory.bin`.

> [!NOTE]
> A factory image contains the information needed for the initial installation on an empty or differently configured device. An OTA image is intended for updating an ESPHome device that is already running.

## 4. Install the firmware

### Option A: ESPHome Device Builder in Home Assistant

When your browser and Home Assistant setup support direct USB installation:

1. Open the device in ESPHome Device Builder.
2. Select **Install**.
3. Choose the option for installing through the computer running the browser.
4. Select the XIAO serial port.
5. Wait for compilation and installation to finish.

### Option B: ESPHome Web

Use [ESPHome Web](https://web.esphome.io/) for a browser-based installation.

1. Open ESPHome Web in a browser with WebSerial support.
2. Click **Connect**.
3. Select the serial port belonging to the XIAO.
4. Choose **Install**.
5. Select the compiled `firmware.factory.bin`.
6. Wait until installation has completed.
7. Disconnect and reconnect the USB-C cable, or press the XIAO reset button.

### Option C: ESP Flasher

ESP32-C5 and ESP32-C6 boards are sometimes not detected correctly by browser-based flashing tools. When the board is missing, identified incorrectly or flashing fails, use [ESP Flasher](https://github.com/Jason2866/ESP_Flasher).

1. Download the current ESP Flasher release for your operating system.
2. Start ESP Flasher.
3. Select the serial port belonging to the XIAO.
4. Connect to the device.
5. Select the compiled `firmware.factory.bin`.
6. Start flashing.
7. Wait for the success message.
8. Disconnect and reconnect the USB-C cable, or press reset.