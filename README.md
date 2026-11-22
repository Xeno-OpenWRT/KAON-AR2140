# KAON-AR2140
This is the KAON AR2140 repository.

So I recently got my hands on the Maxis WiFi 6 Router with their new WiFi 6 Home Plan idk.But basically this router has very good hardware and in my case this doesn't have native WebUI SSH or Telnet Controls that i can use but Broadcom left the OTP fuses unblown and the CFE> is Broadcom's proprietary Bootloader which has TFTPBoot and RamBoot and many more.

## Device Overview
* Manufacturer: KAON Group Co.Ltd. (formerly Kaonmedia)
* Model: AR2140
* Category: AX5400 Dual-Band Wi-Fi 6 CPE
* Primary Deployment: ISP-provisioned hardware (notably Maxis Fibre in Malaysia, as well as European telcos like Play/UPC Poland).
* Base OS/SDK: Broadcom BCA SDK (Linux kernel)+Kaon custom OpenWRT-based middleware.

### KAON AR2140 Hardware and Specifications list.
* CPU: Broadcom BCM6750 Tri-Core 1.5GHz SoC
* Flash: 256MB SKHynix NAND 
* RAM: 512MB SAMSUNG DDR3L 1600MHz
* WiFi Hardware: --2.4GHz > Integrated in CPU // --5GHz > BCM43684

### Wireless Specifications
* WiFi Class: AX5400 Dual-Band Dual-Concurrent (DBDC)
* 2.4GHz: Up to 574 Mbps (802.11ax, 2x2 MIMO, 40MHz bandwidth)
* 5GHz: Up to 4,804 Mbps (802.11ax, 4x4 MIMO, 160MHz bandwidth)
* Antennas: Internal dual-band antennas.
* Wireless Features: OFDMA, MU-MIMO, Band Steering, Smart Roaming, and EasyMesh certification.

### Ports & Connectivity
* WAN: 1x Gigabit Ethernet (10/100/1000 Mbps) port
* LAN: 4x Gigabit Ethernet (10/100/1000 Mbps) port
#### Additional Ports (might differ for different ISP(s))
* USB: 1x USB 3.0 Super-Speed port
* TELEPHONY: 1x Telephone Port (in my case)
* Physical Buttons: Power switch, Reset button , WPS button


### Firmware, Management & Notes
* Management Protocols: Web GUI (HTTP/HTTPS), TR-069, TR-181 , SNMP
* Operation Modes: Router Mode, Bridge Mode (AP / Mesh Repeater)
* VLAN Support: Supports 802.1Q VLAN tagging on WAN (e.g., VLAN 621 for Maxis Fibre)
* SSH & Root Access:
  * SSH is often enabled by default on older firmware builds, but in a restricted CLI
	* Because the underlying userland is built on Kaon's OpenWRT firmware, standard Linux Utilities are shown once you escape the stock CLI
* OpenWrt Firmware Status:
  * Mainline OpenWrt: Unsupported. Due to Broadcom's closed-source Wi-Fi drivers and proprietary hardware NAT, mainline OpenWRT has no full Wi-Fi 6 support for the BCM6750 + BCM43684 platform
* Known Security Advisories:
  * CVE-2024-3659: Authenticated OS command injection vulnerability in the administrative web portal affecting firmware versions prior to 4.2.16 (tested down to 3.2.46).
* UART / Serial Console:
  * Standard 3.3V TTL UART pads are present on the PCB (GND, TX, RX, VCC(DONT USE FOR UART))
	* Typical Broadcom CFE / U-Boot baud rate is 115200
