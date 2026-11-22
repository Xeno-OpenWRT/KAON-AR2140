# KAON-AR2140
This is the KAON AR2140 repository.

So I recently got my hands on the Maxis WiFi 6 Router with their new WiFi 6 Home Plan idk.But basically this router has very good hardware and in my case this doesn't have native WebUI SSH or Telnet Controls that i can use but Broadcom left the OTP fuses unblown and the CFE> is Broadcom's proprietary Bootloader which has TFTPBoot and RamBoot and many more.

## Device Overview
```text
* Manufacturer: KAON Group Co.Ltd. (formerly Kaonmedia)
* Model: AR2140
* Category: AX5400 Dual-Band Wi-Fi 6 CPE
* Primary Deployment: ISP-provisioned hardware (notably Maxis Fibre in Malaysia, as well as European telcos like Play/UPC Poland).
* Base OS/SDK: Broadcom BCA SDK (Linux kernel)+Kaon custom OpenWRT-based middleware.
```
### KAON AR2140 Hardware and Specifications list.
```text
* CPU: Broadcom BCM6750 Tri-Core 1.5GHz SoC
* Flash: 256MB SKHynix NAND 
* RAM: 512MB SAMSUNG DDR3L 1600MHz
* WiFi Hardware: --2.4GHz > Integrated in CPU // --5GHz > BCM43684
```
### Wireless Specifications
```text
* WiFi Class: AX5400 Dual-Band Dual-Concurrent (DBDC)
* 2.4GHz: Up to 574 Mbps (802.11ax, 2x2 MIMO, 40MHz bandwidth)
* 5GHz: Up to 4,804 Mbps (802.11ax, 4x4 MIMO, 160MHz bandwidth)
* Antennas: Internal dual-band antennas.
* Wireless Features: OFDMA, MU-MIMO, Band Steering, Smart Roaming, and EasyMesh certification.
```
### Ports & Connectivity
```text
* WAN: 1x Gigabit Ethernet (10/100/1000 Mbps) port
* LAN: 4x Gigabit Ethernet (10/100/1000 Mbps) port
```
#### Additional Ports (might differ for different ISP(s))
```text
* USB: 1x USB 3.0 Super-Speed port
* TELEPHONY: 1x Telephone Port (in my case)
* Physical Buttons: Power switch, Reset button , WPS button
```

### Firmware, Management & Notes
```text
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
```

### Unlocking & Entering an Unrestricted Root Shell

* Broadcom's custom sshd puts users in a restricted CLI even though MaxARSysOpr is configured with /bin/sh in /var/passwd.
1. Log into the Web UI as MaxARSysOpr (default password: !!bestHOME!!xxxx, xxxx is the last 4 digits of the router's serial number) and enable SSH under Security → Access Control or SSH is enabled by default but isn't an option in the stock WebUI.

* 2.Connect via SSH from your PC

* Command Prompt
```bash
ssh MaxARSysOpr@<Router's IP>
```
* Escape router's restricted CLI/Shell
```bash
echo $(wget -O /data/busybox <URL_TO_STATIC_ARMV7_BUSYBOX> && chmod +x /data/busybox)
echo $(/data/busybox telnetd -l /bin/sh -p 1234)
```
* Then connect from pc to enter shell
```bash
telnet <router's IP> 1234
```
### Bootlogs(Linux and uboot)
* uBoot logs
<details>
<summary><b>Click to expand Full CFE Bootlog </b></summary>

```text
BTRM
V1.1
CACH
CODE
ZBSS
MAIN
OTP?
OTPP
USBT
NAND
IMG?
IMGL
UHD?
UHDP
RLO?
RLOP
UBI?

UBIP
PASS
HELO
CPU0
L1CD
MMUI
ZBBS
MAIN
5.0207p2-1.0.38-164.255
SEND
Boot Strap Register:  0x7fffdfc2
NVRAM memcfg 0x20000427
MCB chksum 0x870be862, config 0x20000427

MemsysInit lpf0fc_generic 3.3.7.100 20180907
DDR3
8<21D728 80180000 801A000▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒ 5997
MCB rev=0x00060301 Ref ID=0x04997 Sub Bld=0x008
Dram Timing 11-11-11

start of memsys_begin
mc_cfg_init(): Initialize the default values on mc_cfg
init_memc_dram_profile(): Initializing MEMC DRAM profile
----------------------------------------------------,----------
MEMC DRAM profile (memc_dram_profile_struct) values:
  dram_type    = DDR3
====================================================
PART values:
  part_speed_grade    = 1600 CL11
  part_size_Mbits     = 4096 (DRAM size in MegaBhts)
  part_row_bits       = 15 (number of row bits)
  part_col_bits       = 10 (number of column bits)
  part_ba_bits        = 3 (number of bank bits)
  part_width_bits     = 16 (DRAM width in bits)
NUMER @F▒PARTs:
  par|_num            = 1 (Number of parts)
TOTAL values:
  total_size_Mbits    = 4096 (DRAM size in MegaBits)
  total_cs_bits       = 0 (number of cs bits, for dual_rank mode)
  total_width_bits    = 16 (DRAM width in bits)
  total_burst_bytes   = 16 (Number of bytes per DRAM access)
  total_max_byte_addr = 0x1fffffff (Maximum/last DRAM byte address)
                        (Number of bits i
                                         ▒▒х▒}▒▒▒}byte_addr is 29)
                        (i.e    ▒▒х▒}▒▒▒}byte_addr goes from bit 0 to bit 28)
  ddr_2T_mode         = 1
  ddr_hdp_mode        = 0
  large_pagY▒▒j
  ddr_dual_rank       = 0
  cs_mode             = 0
MEMC timing (memc_dram_timing_cfg_struct) valtes:
====================================================
  MC_CHN▒TIM_TIM1_0 register fields:
    tCwl   = 8
    tRP    = 11
    tCL    = 11
    tRCD   = 11
  MC_CHN_TIM_TIM1_1 register fields:
    tCCD_L = 4
    tCCD   = 4
    tRRD_L = 6
    tRRD   = 6
  MC_CHN_TIM_TIM1_2 register fields:
    tFAW   = 32
    tRTP   = 6
    tRCr   = 39
  MC_CHN_TIM_TIM1_3 register fields:
    tWTR_L = 6
    tWTR   = 6
    tWR_L  = 12
    tWR    = 12
  MC_CHN_TIM_TIM2 register fields:
    tR2R   = 0
    tR2W   = 2
    tW2R   = 2
    tW2W   = 0
    tAL    = 0
    tRFC   = 208
====================================================

Poll PHY Status register
PHY Status= 1
Disable Auto-Refresh
[0x80180200] = 0x00000305
End of memsys_begin
Add/Ctl Alignment
no adjustment

ZQ Cal LP PHY
 R in Ohm
 P: Finger=0x2D0▒*ɵ▒0x78 ▒rv=0x41
 N: Finger=0x2|0 Term=0x78 Drv=0x41

C▒1▒Ref(Hz)=0x02FAF080 UI STEPS=0x03C
 DDR CLK(MHz)=0x30B WL CLK dly(ps)=0x0C8 bitT(ps)=0x281 VDLsize(fs)=0x29BB CLK_VDL=0x01B
c_initof me
[0x80180004] = 0x0110061f
[0x80180234] = 0x00000501
Enable Auto-Refresh
[0x80180110] = 0x11100f0e
[0x80180114] = 0x15141312
[0x80180118] = 0x19181716
[0x8018011c] = 0x001c1b1a
[0x80180124] = 0x04000000
[0x80180128] = 0x08070605
[0x8018012c] = 0x00000a09
[0x80180134] = 0x000d0c0b
 Writing to MC_CHN_CFG_CNFG reg; data=0x00000000
[0x80180100] = 0x00000000
cfg_memc_timing_ctrl() Called
[0x80180214] = 0x080b0b0b
[0x80180218] = 0x04040606
[0x8018021c] = 0x20000627
[0x80180220] = 0x06060c0c
[0x80180224] = 0x120000d0
End of memc_init
start of pre_shmoo
[0x80180004] = 0xc110071f
end of pre_shmoo

SHMOO 28nm
801A0000 80180800 00000000 00020000 00000000

Shmoo WL

One UI Steps : 0x47

auto-clk result = 01E (filter=0C steps)
initial CLK shift = 01B
final CLK shift   = 01E

   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 S-------------X--------------------------------------------------------
01 S------------------X---------------------------------------------------

Shmoo RD En▒ "EM9▒DRIVE PAD CONTROL (from) (to)
 B0 0003196B 0007996B
 B1 0003196B 0007996B
B0 RISE UI=1 VDL=26 pICK UI=2 VDL=26
B1 RISE UI=1 VDL=1D PICK UI=2 V|L=1D
   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 --S-----------------------------------X+++++++++++++++-----------------
01 --S--------------------------X+++++++++++++++--------------------------

Shmoo RD DQ NP
DQS :
B0 VDL=3C ok
B1 VDL=3C ok
   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 ---------+++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
01 -----------++++++++++++++++++++++++++X++++++++++++++++++++++++++-------
02 -----------+++++++++++++++++++++++++X+++++++++++++++++++++++++---------
03 ----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++------
04 ----------++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
05 ------------++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
06 --------++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
07 ---------+++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
08 -----------++++++++++++++++++++++++++X+++++++++++++++++++++++++++------
09 --------++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
10 -----------+++++++++++++++++++++++++X++++++++++++++++++++++++++--------
11 --------+++++++++++++++++++++++++X+++++++++++++++++++++++++------------
12 -----------++++++++++++++++++++++++++X++++++++++++++++++++++++++-------
13 --------++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
14 -----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
15 ---------++++++++++++++++++++++++++X++++++++++++++++++++++++++---------

Shmoo RD DQ P
   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 ----------++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
01 -----------++++++++++++++++++++++++++X+++++++++++++++++++++++++++------
02 -----------+++++++++++++++++++++++++X+++++++++++++++++++++++++---------
03 -----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
04 ---------++++++++++++++++++++++++++X++++++++++++++++++++++++++---------
05 ------------++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
06 --------++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
07 --------+++++++++++++++++++++++++X++++++++++++++++++++++++++-----------
08 ----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++------
09 -------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
10 ---------++++++++++++++++++++++++++X+++++++++++++++++++++++++++--------
11 ------++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----------
12 --------+++++++++++++++++++++++++++X++++++++++++++++++++++++++++-------
13 ------+++++++++++++++++++++++++++X++++++++++++++++++++++++++++---------
14 --------++++++++++++++++++++++++++++X+++++++++++++++++++++++++++++-----
15 -------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------

Shmoo RD DQ N
   0000000000111111111122222222>233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 ---------+++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
01 ----------++++++++++++++++++++++++++X+++++++++++++++++++++++++++-------
02 -----------+++++++++++++++++++++++++X++++++++++++++++++++++++++--------
03 ----------+++++++++++++++++++++++++++X(++++++++++++++++++++++++++------
04 ----------++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
05 ------------++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
06 --------++++++++++++++++++++++++++X+++++++++++++++++++++++++++---------
07 ---------+++++++++++++++++++++++++X+++++++++++++++++++++++++-----------
08 -----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
09 --------++++++++++++++++++++++++++++X++++++++++++++++++++++++++++------
10 -----------++++++++++++++++++++++++++X+++++++++++++++++++++++++++------
11 -------++++++++++++++++++++++++++X+++++++++++++++++++++++++++----------
12 -----------++++++++++++++++++++++++++X++++++++++++++++++++++++++-------
15 ---------+++++++++++++;+++++++++++++X+++++++++(+++++++++++++++++-----▒-

RD DQS adjustments :
BL0: Start: 0x3C Final: 0x3C
BL1: Start: 0x3C Final: 0x3C

Shmoo WR DQ
   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 ---------++++++++++++++++++++++++++X++++++++++++++++++++++++++---------
01 -----------++++++++++++++++++++++++++X++++++++++++++++++++++++++-------
02 ----------++++++++++++++++++++++++++X++++++++++++++++++++++++++--------
03 ---------++++++++++++++++++++++++++X+++++++++++++++++++++++++++--------
04 ---------++++++++++++++++++++++++++X++++++++++++++++++++++++++---------
05 -----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
06 ---------++++++++++++++++++++++++++X++++++++++++++++++++++++++---------
07 --------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++--------
08 ---------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-------
09 ------++++++++++++++++++++++++++++X++++++++++++++++++++++++++++--------
10 --------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++--------
11 ----+++++++++++++++++++++++++++X++++++++++++++++++++++++++++-----------
12 ----------+++++++++++++++++++++++++X++++++++++++++++++++++++++---------
13 ------++++++++++++++++++++++++++++X++++++++++++++++++++++++++++--------
14 --------++++++++++++++++++++++++++++X++++++++++++++++++++++++++++------
15 ------+++++++++++++++++++++++++++X++++++++++++++++++++++++++++---------

Shmoo WR DM
WR DM
   00000000001111111111222222222233333333334444444444555555555566666666667
   01234567890123456789012345678901234567890123456789012345678901234567890
00 -----------+++++++++++++++++++++++++++X+++++++++++++++++++++++++++-----
01 ------++++++++++++++++++++++++++++X++++++++++++++++++▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒$▒▒.W▒сof memsys_end
[0x80180004] = 0x8110071f
[0x80180010] = 0x00000009
end of memsys_end
DDR test done successfully
FPS0
---
PAR1
U003
COM1
UBI
0003
BT03
0008
----
PAR2
U002
COM1
UBI#
0002
BT02
0908
----
TRY1
NAN3
UBI!
NAN5


Base: 5.2_07p2
CFE version 1.0.38-164.255 for BCM963178 (32bi,SP,LE)
Build Date: Mon Nov 17 13:05:12 KST 2025 (jeremiah@thor-54)
Copyright (C) 2000-2015 Broadcom Corporation.

SEND
Boot Strap Register:  0x7fffdfc2
Chip ID: BCM6750_A2, ARM Cortex A7 Triple Core: 1500MHz
Total Memory: 536870912 bytes (512MB)
NAND ECC BCH-4, page size 0x800 bytes, spare size used 64 bytes
NAND flas
         "▒٥▒▒▒ , id 0x0000adda block 128KB size 262144KB
Take PMC out of reset
waiting for PMC finish booting
PMC rev: 3.0.8.223958 running
pmc_init:PMC using DQM mode
Board IP address                  : 192.168.1.1:ffffff00
Host IP address                   : 192.168.1.100
Gateway IP address                :
Run from flash/host/tftp (f/h/c)  : f
Default host run file name        : vmlinux
Default host flash file name      : bcm963xx_fs_kernel
Boot delay (0-9 seconds)          : 1
Boot image (0=latest, 1=previous) : 0
Default host ramdisk file name    :
Default ramdisk store address     :
Default DTB file name             :
Board Id                          : AR2140_VOIP
Number of MAC Addresses (1-64)    : 4
Base MAC Address                  : XXXXXXXXXXXX
PSI Size (1-512) KBytes           : 48
Enable Backup PSI [0|1]           : 0
System Log Size (0-256) KBytes    : 0
Auxillary File System Size Percent: 0
WLan Feature▒▒▒▒0
Voice Board Configuration (0-2)   : LE9641_ZSI_BB
Partition 1 Size (MB)             : 8M
Partition 2 Size (MB)             : 8M
Partition 3 Size (MB)             : 0M
Partition 4 Size (MB) (Data)      : 13M

Initalizing switch low level hardware.
Software Resetting Switch ... Done.
Waiting MAC port Rx/Tx to be enabled by hardware ...Done
Disable Switch All MAC port Rx/Tx
*** Press any key to stop auto run (1 seconds) ***
Auto run second count down: 0
Booting from latest image (address 0x00100000, flash offset 0x00100000) ...
```

</details>

* Linux logs
<details>
<summary><b>Click to expand Full Linux Bootlog </b></summary>

```text
ubi_find_file: got vmlinux.lz  size 2570637
Decompression LZMA Image OK!
Entr^
     с0x00018000
Starting program at 0x00018000
cfe_fs_fetch_file: file AR2140_VOIP.dtb not found
ubi_find_file: got 963178.dtb  size 5120
/memory = 0x20000000 bytes @ 0x0
Setting ROOTFS: root=ubi:rootfs_ubifs ubi.mtd=0 rootfstype=ubifs
CFE appending "isolcpus=2" from PROFILE to boot cmdline
Appending CFE version to dtb, ret:0
Appending NVRAM to dtb, ret:0
Booting Linux on physical CPU 0x0
Linux version 4.1.52 (jeremiah@thor-54) (gcc version 5.5.0 (Buildroot 2017.11.1) ) #1 SMP PREEMPT Mon Nov 17 13:10:26 KST 2025
CPU: ARMv7 Processor [410fc075] revision 5 (ARMv7), cr=10c5387d
CPU: PIPT / VIPT nonaliasing data cache, VIPT aliasing instruction cache
Machine model: Broadcom BCM963178
bootconsole [earlycon0] enabled
Memory policy: Data cache writealloc
On node 0 totalpages: 131072
free_area_init_node: node 0, pgdat c068da80, node_mem_map dfbf8000
  Normal zone: 1024 pages used for memmap
  Normal zone: 0 pages reserved
  Normal zone: 131072 pages, LIFO batch:31
PERCPU: Embedded 11 pages/cpu @dfbcf000 s15628 r8192 d21236 u45056
pcpu-alloc: s15628 r8192 d21236 u45056 alloc=11*4096
pcpu-alloc: [0] 0 [0] 1 [0] 2
Built 1 zonelists in Zone order, mobility grouping on.  Total pages: 130048
Kernel command line: isolcpus=2 root=ubi:rootfs_ubifs ubi.mtd=0 rootfstype=ubifs console=ttyAMA0 earlyprintk debug irqaffinity=0 pci=pcie_bus_safe
PID hash table entries: 2048 (order: 1, 8192 bytes)
Dentry cache hash table entries: 65536 (order: 6, 262144 bytes)
Inode-cache hash table entries: 32768 (order: 5, 131072 bytes)
Memory: 511600K/524288K available (4849K kernel code, 217K rwdata, 1336K rodata, 212K init, 313K bss, 12688K reserved, 0K cma-reserved, 0K highmem)
Virtual kernel memory layout:
    vector  : 0xffff0000 - 0xffff1000   (   4 kB)
    fixmap  : 0xffc00000 - 0xfff00000   (3072 kB)
    vmalloc : 0xe0800000 - 0xff000000   ( 488 MB)
    lowmem  : 0xc0000000 - 0xe0000000   ( 512 MB)
    pkmap   : 0xbfe00000 - 0xc0000000   (   2 MB)
    modules : 0xbf000000 - 0xbfe00000   (  14 MB)
      .text : 0xc0018000 - 0xc06226f8   (6186 kB)
      .init : 0xc0623000 - 0xc0658000   ( 212 kB)
      .data : 0xc0658000 - 0xc068e740   ( 218 kB)
       .bss : 0xc068e740 - 0xc06dccb0   ( 314 kB)
Preemptible hierarchical RCU implementation.
NR_IRQS:16 nr_irqs:16 16
Architected cp15 timer(s) running at 50.00MHz (phys).
clocksource arch_sys_counter: mask: 0xffffffffffffff max_cycles: 0xb8812736b, max_idle_ns: 440791202655 ns
sched_clocc: 56 bits at 50MHz, resolution 20ns, wraps every 4398046511100ns
Switching to timer-based delay loop, resolution 20ns
BRCM Legacy Drivers' Helper, all legacy drivers' IO memories/interrupts should be remapped here
     Remapping interrupts...
             hwirq      virq
               60        20
               61        21
               43        22
               44        23
               45        24
               46        25
               66        26
              110        27
               69        28
              152        29
              153        30
              154        31
              155        32
              156        33
              157        34
              158        35
              159        36
               76        37
               78        38
               77        39
               79        40
              104        41
              105        42
              106        43
               96        44
               97        45
               98        46
               99        47
              100        48
              101        49
              148        50
               71        51
              128        52
     Remapping IO memories...
             phys              virt          size
       0000000080180000  00000000e0840000  00024000
       0000000080200000  00000000e0c00000  00200000
       0000000080400000  00000000e0900000  00090000
       00000000ff800000  00000000e0820000  00013000
       00000000ff85a000  00000000e0810000  00008000
       00000000ffe00000  00000000e0a00000  00100000
       00000000ff860000  00000000e0808000  00002000
       0000000083010a00  00000000e0802a00  00000100
       000000008000c000  00000000e0838000  00003fff
       0000000083010000  00000000e080c000  00001000
       0000000083018000  00000000e080e000  00001000
       0000000083020000  00000000e081a000  00001000
       0000000083028000  00000000e081c000  00001000
       0000000083030000  00000000e081e000  00001000
       0000000083038000  00000000e0834000  00001000
       0000000083050000  00000000e0836000  00001000
       0000000083060000  00000000e083e000  00001000
       0000000083068000  00000000e0866000  00001000
       00000000810a0000  00000000e0868000  00001000
       0000000081060000  00000000e086c000  00003000
       00000000ffff0000  00000000a086a000  00001000
       0000000083000000  00000000e0870000  00001000
       000000008306c000  00000000e0872000  00001000
       0000000080490000  00000000e0880000  00010000
       0000000080650000  00000000e08c0000  00020000
       0000000080700000  00000000e0f00000  00090000
       0000000080800000  00000000e0878000  00006800
       0000000081100000  00000000e1000000  00091000
Calibrating delay loop (skipped), value calculated using timer frequency.. 100.00 BogoMIPS (lpj=50000)
pid_max: default: 32768 minimum: 301
Moqnt-cache hash table entries: 1024 (order: 0, 4096 bytes)
Mountpoint-cache hash table entries: 1024 (order: 0, 4096 bytes)
--Kernel Config--
  SMP=1
  PREEMPT=1
  DEBUG_SPINLOCK=0
  DEBUG_MUTEXES=0
CPU: Tdsting write bqffer coherency: ok
Satting up static identity map for 0x182c0 - 0x182f4
     Reserved aemory mapping...
                phys          virt       size
CCI hardware cache coherency enabled
pmc_init:PMC using DQM mode
Successfully retrieved NVRAM data from dtb
cfe-v: 1.0.38-164.255
Brought up 3 CPUs
SMP: Total of 3 processors activated (300.00 BogoMIPS).
CPU: All CPU(s) started in SVC mode.
devtmpfs: initialized
VFP support v0.3: implementor 41 architecture 2 part 30 variant 7 rev 5
clocksource jiffies: mask: 0xffffffff max_cycles: 0xffffffff, max_idle_ns: 1911260446275000 ns
futex hash table entries: 1024 (order: 4, 65536 bytes)
NET: Registered protocol family 16
DMA: preallocated 256 KiB pool for atomic coherent allocations
cpuidle: using governor ladder
cpuidle: using governor aenu
Serial: AMBA PL011 UAR\ driver
ff812000.serial: ttyAMA0 at MMIO 0xff812000 (irq = 55, base_baud = 0) is a PL011 rev3
console [ttyAMA0] e+▒▒▒▒5
                         console [ttyAMA0] enabled
bootconsole [earlycon0] disabled
bootconsole [earlycon0] disabled
Broadcom Logger v0.1
SCSI subsystem initialized
usbcore: registered new interface driver usbfs
usbcore: registered new interface driver hub
usbcore: registered new device driver usb
bcmhs_spi bcmhs_spi.1: master is unqueued, this is deprecated
skb_free_task created successfully with start bud`et 256
BLOG v3.0 Initialized
BLOG Rule v1.0 Initialized
Broadcom IQoS v1.0 initialized
Broadcom GBPM v0.1 initialized
Switched to clocksource arch_sys_counter
NET: Registered protocol family 2
default registered
default registered
MPTCP: Stable release v0.91
TCP established hash table entries: 4096 (order: 2, 16384 bytes)
TCP bind hash table entries: 4096 (order: 3, 32768 bytes)
TCP: Hash tables configured (established 4096 bind 4096)
UDP hash table entries: 256 (order: 1, 8192 bytes)
UDP-Lite hash table entries: 256 (order: 1, 8192 bytes)
NET: Registered protocol family 1
RPC: Registered named UNIX socket transport module.
RPC: Registered udp transport module.
RPC: Registered tcp transport module.
RPC: Registered tcp NFSv4.1 backchannel transport module.
PCI: CLS 0 bytes, default 64
squashfs: version 4.0 (2009/01/31) Phillip Lougher
exFAT: Version 1.2.9
jffs2: version 2.2. (NAND) (SUMMARY)  ¨▒▒▒▒j▒▒▒▒▒▒▒▒Hat, Inc.
fuse init (API version 7.23)
io scheduler noop registered (default)
brd: module loaded
loop: module loaded
nand: device found, Manufacturer ID: 0xad, Chip ID: 0xda
nand: Hynix H27U2G8F2DKA-BM
nand: 256 MiB, SLC, erase size: 128 KiB, page size: 2048, OOB size: 128
bcm63xx_nand ff801800.nand: Adjust timing_1 to 0x6532845b timing_2 to 0x00091e94
bcm63xx_nand ff801800.nand: detected 256MiB total, 128KiB blocks, 2KiB pages, 16B OOB, 8-bit, BCH-4
Bad block table found at page 131008, version 0x01
Bad block table found at page 130944, version 0x01
>>>>> For primary mtd partition rootfs, cferam/vmlinux.lz UBI volume, vmlinux fs mounted as UBIF <<<<<
Secondary mtd partition rootfs_update detected as UBI for cfer`m/vmlinux source and UBIFS for vmlinux filesystem
Creating 8 MTD partitions on "brcmnand.0":
0x000000000000-0x000007180000 : "rootfs"
0x000007180000-0x00000e200000 : "rootfs_update"
0x00000f200000-0x00000ff00000 : "data"
0x000000000000-0x000000100000 : "nvram"
0x000000100000-0x000007180000 : "image"
0x000007180000-0x00000e200000 : "image_update"
0x00000ea00000-0x00000f200000 : "misc2"
0x00000e200000-0x00000ea00000 : "misc1"
tun: Uniersal TUN/\AP device driver, 1.6
tun: (C) 1999-2004 Max Krasnyansky <maxk@pualcomm.com>
PPP generic driver version▒r▒r▒j
                                PPP BSD Compression module registered
PPP Deflate Compression module registered
NET: Registered protocol family 24
usbcore: registered new interface driver asix
usbcore: registered new interface driver ax88179_178a
usbcore: registered new interface driver cdc_ether
usbcore: registered new interface driver cdc_eem
usbcore: registered new interface driver rndis_host
usbcore: registered new interface driver cdc_ncm
bcm96xxx-wdt ff800480.watchdog: Broadcom BCM96xxx watchdog timer
brcmboard registered
brcmboard: brcm_board_init entry
print_rst_status: Last RESET due to SW reset
print_rst_status: RESET reason: 0x80000000
DYING GASP IRQ Initialized and Enabled
SES: LED GPIO 0x8005 is enabled
map_hw_timer_interrupt,130: interrupt_id 22
map_hw_timer_interrupt,130: interrupt_id 23

```

</details>


### Update on New Findings
```text
* Exact SoC & Board ID (BCM63178 / BCM963178DVT):
  * While marketing specs often list BCM6750, the firmware's build profile (etc/AR2140) and Device Tree (ar2140.dtb) explicitly identify the SoC as Broadcom BCM63178 (BRCM_CHIP=63178, BRCM_BOARD_ID="BCM963178DVT"), running a 3-core ARMv7 Cortex-A7 (vfpv4/neon, lpae) with Broadcom's Archer packet accelerator (archer.ko + cmdlist.ko + pktflow.ko) and dhd.ko + wl.ko Wi-Fi 6 drivers.
* Exact Kernel & ABI (Linux 4.1.52):
  * Runs Broadcom's heavily patched Linux 4.1.52 (vermagic=4.1.52 SMP preempt mod_unload ARMv7 p2v8), using Broadcom CMS rather than standard OpenWRT.
* Exact NAND UBI Layout (mtd4 / mtd5):
   *Firmware images are raw UBI volumes (Min I/O: 2048, LEB: 126976 / 0x1f000, PEB: 131072 / 0x20000) containing 4 volumes:
		* vol_id=0: rootfs_ubifs (dynamic UBIFS root filesystem)
		* vol_id=1: METADATA & vol_id=2: METADATACOPY 
		* vol_id=10: filestruct_full.bin (static volume containing compressed kernel + embedded BCM963178 DTB at offset 0x16101b)
* Built-In Transfer & Persistence Tricks:
  * /data (ubi1:data, ~9 MB writable UBIFS partition) persists across reboots—storing a static busybox binary in /data/busybox means you never need to re-download it after a reboot.
	* You can host an instant FTP server directly from the router to your PC using busybox tcpsvd -vE 0.0.0.0 2121 busybox ftpd -w / for fast downloads via WinSCP or any FTP downloader tool.
* Serial Console (ttyAMA0) & Bootargs:
	* Kernel bootargs from the embedded DTB: console=ttyAMA0 115200 baud 8N1.
```
