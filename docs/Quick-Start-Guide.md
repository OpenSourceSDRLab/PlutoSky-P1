---
title: PlutoSky P1 Quick Start Guide
product: PlutoSky P1
doc_id: OSSDRLAB-PlutoSky-P1-QSG
version: 1.0
date: 2026-11-12
repo: https://github.com/OpenSourceSDRLab/PlutoSky-P1
---

<!-- ossl:header -->
<p><a href="https://www.opensourcesdrlab.com"><img src="../images/OpenSourceSDRLab-banner.jpg" alt="OpenSourceSDRLab" width="100%"></a></p>

# PlutoSky P1 Quick Start Guide

| Product | Document | Version | Date |
|---|---|---|---|
| PlutoSky P1 | OSSDRLAB-PlutoSky-P1-QSG | 1.0 | 2026-11-12 |
<!-- /ossl:header -->

## 1. Overview

The PlutoSky P1 is a USB-powered software-defined radio built around the Analog Devices AD9363 RF transceiver and a Xilinx Zynq XC7Z010 SoC. It follows the Analog Devices ADALM-PLUTO design, runs the official PlutoSDR firmware and works with all PlutoSDR software.

This guide covers the first connection, unlocking the full 70–6000 MHz range and updating the firmware. The detailed manual with screenshots and GNU Radio examples is on the blog: <https://blog.opensourcesdrlab.com/archives/plutosky-p1-user-manual>.

## 2. What you need

| Item | Notes |
|---|---|
| PlutoSky P1 | |
| USB-C cable | a data cable, connected to the **OTG** port |
| Antennas or SMA cables | for TX1/RX1 (and TX2/RX2 in 2R2T mode) |
| PC | Windows, Linux or macOS |
| PlutoSDR USB drivers (Windows only) | from Analog Devices: <https://wiki.analog.com/university/tools/pluto/drivers/windows> |
| libiio | from Analog Devices: <https://github.com/analogdevicesinc/libiio/releases> |
| SDR software | for example IIO Oscilloscope (Analog Devices), GNU Radio, SDR# or SDR++ |
| Serial terminal / SSH client | for example PuTTY, only for the steps in section 5 |

## 3. Board connectors

| Connector | Function |
|---|---|
| TX1, RX1, TX2, RX2 | SMA RF ports of the AD9363 |
| OTG (USB-C) | power and data: USB network adapter (RNDIS/ECM), USB drive, serial console |
| UART (USB-C) | serial console through the on-board CH340 USB-to-UART bridge, 115200 baud |
| DFU button | hold while powering up to enter DFU (recovery) mode |
| LED, DONE | status LED and FPGA configuration done |

> **Tip:** If the board is not detected, the USB port may not supply enough current. Use a port directly on the PC or a powered hub.

## 4. First connection

1. Windows only: run the PlutoSDR USB driver installer as administrator, then install libiio.
2. Connect the **OTG** port to the PC. After about 20 seconds the board appears as:
    - a network adapter (the board is `192.168.2.1`, the PC gets `192.168.2.10`);
    - a USB drive named PlutoSDR with `info.html` and `config.txt`;
    - a serial port (PlutoSDR Serial Console).
3. Check the connection:

    ```
    iio_info -u ip:192.168.2.1
    ```

    The output lists the AD9363 (`ad9361-phy`) and the firmware version.
4. Open IIO Oscilloscope, GNU Radio or another PlutoSDR-compatible program and connect to `ip:192.168.2.1` (or `usb:`).

To use several boards on one PC, change the IP address or host name in `config.txt` on each board's USB drive, save, and eject the drive; the board applies the new settings.

## 5. Unlocking 70–6000 MHz and 2R2T mode

The AD9363 is shipped with the factory range 325–3800 MHz and 20 MHz bandwidth. The firmware can treat it as an AD9364 (1R1T) or AD9361 (2R2T) to widen the tuning range. Use at your own risk; performance outside the specified range is not guaranteed.

1. Log in through the serial console or with `ssh root@192.168.2.1`. User `root`, password `analog`.
2. For 1R1T with the wide range:

    ```
    fw_setenv attr_name compatible
    fw_setenv attr_val ad9364
    reboot
    ```

3. For 2R2T (both channel pairs) with the wide range:

    ```
    fw_setenv attr_name compatible
    fw_setenv attr_val ad9361
    fw_setenv mode 2r2t
    reboot
    ```

4. After the reboot, check with `fw_printenv attr_name attr_val mode`.

## 6. Updating the firmware

The PlutoSky P1 uses the official firmware from Analog Devices: <https://github.com/analogdevicesinc/plutosdr-fw/releases>.

**Normal update**

1. Download and unzip the latest `plutosdr-fw-<version>.zip`.
2. Copy `pluto.frm` to the PlutoSDR USB drive and eject the drive (do not unplug it).
3. The LED flashes quickly while the flash is written. When it stops and the USB drive reappears, unplug and reconnect the board.

**Recovery (DFU)**

1. Hold the DFU button while connecting the OTG port; only one LED stays on.
2. Flash `pluto.dfu` (and if needed `boot.dfu`, `uboot-env.dfu`) from the same release with `dfu-util`, as described in the Analog Devices firmware documentation: <https://wiki.analog.com/university/tools/pluto/users/firmware>.
3. Reconnect the board.

## 7. Hardware files in this repository

| Path | Contents |
|---|---|
| `hardware/PlutoSky-P1_Schematic.pdf` | Board schematic |
| `images/` | Product photos |

The FPGA design and Linux system are the Analog Devices PlutoSDR reference ([hdl](https://github.com/analogdevicesinc/hdl), project `pluto`, and [plutosdr-fw](https://github.com/analogdevicesinc/plutosdr-fw)); build them from those repositories.

## 8. Support

- Product page: <https://opensourcesdrlab.com/products/plutosky-p1>
- User manual: <https://blog.opensourcesdrlab.com/archives/plutosky-p1-user-manual>
- Email: support@opensourcesdrlab.com
- Forum: <https://forum.opensourcesdrlab.com>
- Discord: <https://discord.gg/nnxY6DkmvT>

<!-- ossl:footer -->
---
<sub>© 2026 OpenSourceSDRLab. All rights reserved. · <a href="https://www.opensourcesdrlab.com">www.opensourcesdrlab.com</a></sub>
<!-- /ossl:footer -->
