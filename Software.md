# Software Architecture & Unlocking Guide

This document outlines the boot chain, security state, and step-by-step methodology for unlocking the **KAON AR2140** (Maxis Fibre Malaysia variant) and transitioning it to a custom **OpenWrt** environment.

---

## 1. Security & Bootloader Analysis

While the Maxis-provisioned firmware removes local WebUI, SSH, and Telnet access for end users, the underlying hardware security remains open:

* **OTP Fuses (Unblown):** Broadcom's One-Time Programmable (OTP) security fuses are **not blown**. Secure Boot / signed image enforcement is inactive, allowing unsigned kernels and custom `initramfs` images to execute freely.
* **Bootloader (`CFE>`):** Broadcom's proprietary Common Firmware Environment (`CFE>`) is accessible directly over the 3.3V UART header at `115200 8N1`.
* **Built-in Recovery Primitives:** The stock CFE build retains network and memory boot commands—specifically `TFTPBoot` and `RamBoot`—enabling non-destructive kernel testing in the 512 MB DDR3L RAM without touching the SK Hynix NAND flash.

### Boot Chain Overview
```text
[BootROM (Unblown OTP)] 
       │
       ▼
[Broadcom CFE Bootloader] ──(UART Break / TFTP)──► [RAM Boot: Custom OpenWrt initramfs]
       │
       ▼ (Normal Boot)
[NAND: bootfs (Linux Kernel + DTB)] ──► [NAND: rootfs (Kaon OpenWrt + Broadcom .ko blobs)]
```

---

## 2. Phase 1: Full NAND Backup & Reconnaissance

**⚠️ Rule #1:** Never write to NAND until a full raw backup of all partitions and board calibration data (`nvram` / `data`) is stored safely off-device.

### Step 1.1: Map the Environment & Partitions
Connect a 3.3V USB-to-TTL adapter (`GND`, `TX`, `RX` only — **leave `VCC` disconnected**) and interrupt the boot sequence to drop into the `CFE>` prompt:

```text
CFE> printenv
CFE> show devices
```
Record the memory addresses, MTD partition offsets, and boot arguments (`bootline`).

### Step 1.2: Extract Firmware & Proprietary Blobs
From a full NAND dump (or live rootfs extraction), isolate the proprietary Broadcom components required for full hardware acceleration:
1. **Kernel Modules (`.ko`):**
   * `wlcsm.ko` — Broadcom Wireless Configuration Shared Memory
   * `dhd.ko` / `wl.ko` — Proprietary Wi-Fi 6 drivers (`BCM6750` 2.4 GHz + `BCM43684` 5 GHz)
   * `bcm_enet.ko` / `bcmvlan.ko` — Ethernet MAC & Gigabit switch drivers
   * `pktflow.ko` / `archer.ko` — Broadcom Runner / Flow Cache hardware NAT offload
2. **Firmware & Userland Binaries:**
   * Wi-Fi radio firmware blobs (typically under `/etc/wlan/` or `/lib/firmware/brcm/`)
   * Control utilities (`wl` / `wlctl`, `nvram`, `fcctl`, `ethswctl`)
3. **Device Tree Blob (`.dtb`):**
   * Extract the stock `.dtb` from `bootfs` and decompile it to inspect GPIO mappings (LEDs, Reset, WPS), UART pins, and switch port definitions:
     ```bash
     dtc -I dtb -O dts -o stock-ar2140.dts stock-ar2140.dtb
     ```

---

## 3. Phase 2: Non-Destructive Boot via `RamBoot` / `TFTPBoot`

To test OpenWrt without risking a brick, load images directly into RAM over Ethernet using CFE.

### Host TFTP Setup
1. Set your host machine's Ethernet interface to a static IP matching CFE's expected `serverip` (e.g., `192.168.1.100/24`).
2. Start a TFTP server hosting your test `initramfs` image (`openwrt-ar2140-initramfs.bin`).
3. Connect your host PC to one of the router's **LAN** ports.

### Booting into RAM from `CFE>`
At the `CFE>` console, fetch and execute the image in memory:
```text
# Verify or set network parameters in CFE (adjust to match your CFE env)
CFE> ifconfig eth0 -addr=192.168.1.1 -mask=255.255.255.0

# Load and boot initramfs image over TFTP without flashing NAND
CFE> boot -tftp 192.168.1.100:openwrt-ar2140-initramfs.bin
```
*(Note: Depending on the exact CFE build flags, `ramboot` or `tftpboot` memory load commands may be used—verify syntax via `help` at the `CFE>` prompt.)*

---

## 4. Phase 3: OpenWrt Integration Strategy

Because Broadcom's Wi-Fi 6 (`BCM43684`) and Runner hardware NAT rely on closed-source drivers, two integration paths are viable:

### Path A: Stock Broadcom Kernel + Upstream OpenWrt Rootfs *(Fastest Path to Full Hardware)*
* **How it works:** Keep the stock Broadcom BCA Linux kernel (preserving 100% ABI compatibility with the extracted `.ko` modules) and pivot into a freshly compiled `arm_cortex-a7_neon-vfpv4` OpenWrt `squashfs` + `overlay` rootfs.
* **Pros:** Full AX5400 Wi-Fi 6 (160 MHz, OFDMA, MU-MIMO), hardware NAT offload, and working Ethernet switch out of the box alongside modern LuCI and `opkg` packages.
* **Cons:** Locked to the stock Broadcom kernel version.

### Path B: Custom OpenWrt Kernel + ABI-Matched `.ko` Modules *(Cleanest OpenWrt Build)*
* **How it works:** Check the exact `vermagic` string on the extracted `.ko` files:
  ```bash
  modinfo wl.ko | grep vermagic
  ```
  Compile OpenWrt's kernel using the matching upstream kernel tag and `.config` struct layout (`CONFIG_SMP`, `CONFIG_PREEMPT`, `CONFIG_MODVERSIONS`) so the vendor `.ko` modules load cleanly via `insmod` inside a native OpenWrt build.
* **Pros:** Native OpenWrt build system integration, custom DTS, and clean `sysupgrade` support.
* **Cons:** Requires strict kernel config matching to prevent symbol/struct mismatches when loading proprietary `.ko` modules.

---

## 5. Next Milestones
- [ ] Publish sanitized `CFE>` `printenv` and `help` output
- [ ] Decompile stock `.dtb` and map GPIOs (LEDs, buttons, USB power enable)
- [ ] Document exact `insmod` load order and dependencies for extracted `.ko` modules
- [ ] Verify first OpenWrt `initramfs` boot over `TFTPBoot` / `RamBoot`
