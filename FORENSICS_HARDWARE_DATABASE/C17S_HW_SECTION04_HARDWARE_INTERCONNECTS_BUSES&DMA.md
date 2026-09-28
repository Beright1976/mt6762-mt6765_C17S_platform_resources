################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 4 — HARDWARE INTERCONNECTS, BUSES & DMA
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 4 — HARDWARE INTERCONNECTS, BUSES & DMA
#
# PURPOSE:
# This section defines the physical and firmware-visible interconnect fabric
# of the C17S platform.
#
# This is the build-facing hardware bus contract for the device.
#
# If a future recovery, AOSP ROM, kernel, driver stack, device tree, firmware
# interface, or full custom OS needs to know how silicon blocks, controllers,
# child devices, IRQ lines, mailbox paths, DMA engines, or runtime bus nodes
# are connected, that information belongs here.
#
# SCOPE:
# Included:
# - /proc/iomem register map for interconnect-relevant controllers
# - AXI / infrastructure bus fabric
# - EMI / DRAMC memory-controller fabric
# - DVFSRC / SCPSYS / vcore fabric
# - GCE / CMDQ / mailbox fabric
# - AP DMA, UART DMA, CQDMA, CLDMA, display DMA surfaces
# - All seven I2C controller domains
# - I2C devicetree properties
# - I2C runtime adapters
# - I2C child nodes, names, modaliases, and bound drivers
# - All six SPI controller domains
# - SPI devicetree properties
# - SPI runtime master enumeration
# - SPI child-device absence in the current live state
# - UART0 / UART1 register, tty, IRQ, modalias, and traffic state
# - USB / MUSB / UDC / configfs gadget state
# - SCP IPC, SSPM mailbox, Wiite EINT, touch EINT, MT6370 IRQ paths
# - MIPI CSI receiver fabric
# - MIPI DSI TX fabric
# - Display RDMA / WDMA / MDP DMA paths
# - Runtime /proc, /sys, and devicetree interconnect surfaces
# - Build-use rules for future kernel/device-tree/source work
#
# Excluded:
# - nRF52832 protocol decoding.
# The I2C/UART/IRQ bridge paths are recorded here, but nRF firmware and
# command protocol belong to the dedicated nRF section.
#
# - Camera optical/electrical proof.
# Camera bus nodes are recorded here; camera sensor truth belongs to the
# camera/hardware component section.
#
# - APK/framework behavior.
# Android package control-plane behavior belongs to APK/security sections.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : /proc/iomem
# TIER 3 : /sys/firmware/devicetree/base
# TIER 4 : /sys/bus/i2c, /sys/bus/spi, /sys/bus/platform, /sys/class
# TIER 5 : /proc/interrupts, /proc/tty/driver/serial
# TIER 6 : Closed DOC00–DOC21 corpus
# FINAL  : Beright / Albert acceptance
#
# RULES:
# Controller presence, devicetree declaration, runtime sysfs node, child node,
# bound driver, interrupt activity, and physical component proof are separate
# evidence classes.
#
# A software node does not by itself prove a physical component.
#
# A declared bus child does not by itself prove working hardware.
#
# A zero-length devicetree boolean property is recorded as present.
#
# Old “3 SPI bus” wording is invalid. Live evidence proves six SPI controller
# register domains.
#
# Section 4 records the interconnect topology. It does not over-claim sensor,
# camera, nRF, or ODM protocol behavior beyond the bus evidence.
################################################################################
################################################################################
# 4.1 — CANONICAL INTERCONNECT SUMMARY
################################################################################

Controller counts:
I2C controllers                : 7
SPI controllers                : 6
UART / serial controllers      : 2
USB UDC path                   : musb-hdrc
AP DMA channels exposed        : 4
CQDMA engines                  : present
CLDMA modem DMA                : present
GCE / CMDQ                     : present
GCE mailbox                    : present
GCE secure mailbox             : present
SCP IPC                        : present
SSPM mailbox IRQs              : present
MIPI CSI receiver domains      : present
MIPI DSI TX domain             : present
Display RDMA / WDMA / MDP DMA  : present

Critical corrections:
Old SPI count                  : 3
Correct SPI count              : 6
Old I2C6 understanding          : single 6-002d node
Correct I2C6 topology           : 6-000c, 6-002d, 6-0050 camera_main_two chain
I2C5 role                       : 3.4 MHz high-speed MT6370 PMU bus
I2C4 role                       : Wiite CP / heartrate / CCU path
UART1 role                      : active secondary serial path with live RX/TX

This device must not be modeled as a simple phone-style MTK board.
It exposes a dense mixed fabric of I2C camera chains, Wiite control-plane
routing, SCP/SSPM mailboxes, CMDQ/GCE DMA fabric, MIPI camera/display fabric,
and a high-speed PMU I2C bus.
Future kernel/device-tree work must preserve the controller count, register
ranges, IRQ relationships, child nodes, and bus modes recorded here.

################################################################################

# 4.2 — MASTER REGISTER MAP

################################################################################

The following interconnect-relevant register ranges are live-declared by the
device.

Core infrastructure:
Watchdog / TOPRGU              : 0x10007000
DVFSRC                         : 0x10012000
SCP system control             : 0x10500000
EMI controller                 : 0x10219000
DRAMC                          : 0x1022a000
GCE / CMDQ / mailbox           : 0x10238000
AXI bus domain                 : 0x18000000 – 0x180fffff

DMA:
AP DMA                         : 0x11000000
UART DMA controller            : 0x11000680
CQDMA                          : 0x10212000
Modem CLDMA AO                 : 0x10014000 family
Modem CLDMA non-AO             : 0x1021b000 / 0x1021c000 family

UART:
UART0 / serial0                : 0x11002000
UART1 / serial1                : 0x11003000

I2C:
I2C0                           : 0x11007000
I2C1                           : 0x11008000
I2C2                           : 0x11009000
I2C6                           : 0x1100d000
I2C3                           : 0x1100f000
I2C4                           : 0x11011000
I2C5                           : 0x11016000

SPI:
SPI0                           : 0x1100a000
SPI1                           : 0x11010000
SPI2                           : 0x11012000
SPI3                           : 0x11013000
SPI4                           : 0x11014000
SPI5                           : 0x11015000

USB:
USB0                           : 0x11200000
USB SIF / USB1P SIF             : 0x11210000
USB PHY / secondary PHY domain  : 0x11cc0000

MIPI camera receiver:
MIPI RX ANA CSI0A              : 0x11c10000
MIPI RX ANA CSI0B              : 0x11c11000
MIPI RX ANA CSI1A              : 0x11c12000
MIPI RX ANA CSI1B              : 0x11c13000
MIPI RX ANA CSI2A              : 0x11c14000
MIPI RX ANA CSI2B              : 0x11c15000

MIPI display transmitter:
MIPI TX0 / DSI                 : 0x11c80000

Display / MDP DMA:
MDP RDMA0                      : 0x14004000
MDP WDMA0                      : 0x14008000
DISP RDMA0                     : 0x1400d000
DISP WDMA0                     : 0x1400e000

Register ranges define the controller base addresses for future kernel,
device-tree, bootloader, and hardware-abstraction work.
The six SPI ranges and seven I2C ranges must be carried exactly.

################################################################################

# 4.3 — AXI / INFRASTRUCTURE FABRIC

################################################################################

AXI / infrastructure bus:
Register range                 : 0x18000000 – 0x180fffff
Role                           : infrastructure interconnect / bus fabric
Build relevance                : kernel low-level platform bring-up, DMA, bus
fabric, interconnect, and debug correlation

Related infrastructure domains:
DVFSRC                         : 0x10012000
SCPSYS                         : 0x10001000 family
SCP SysCtrl                    : 0x10500000
EMI                            : 0x10219000
DRAMC                          : 0x1022a000
GCE / CMDQ                     : 0x10238000

AXI/infrastructure fabric is not a replaceable Android component.
It must be represented by the kernel/device-tree platform exactly enough that
clocks, DMA, mailbox, display, camera, USB, and coprocessor paths initialize
against the correct MMIO ranges.

################################################################################

# 4.4 — EMI / DRAMC / MEMORY-CONTROLLER FABRIC

################################################################################

EMI controller:
Register base                  : 0x10219000
Role                           : external memory interface / memory fabric

DRAMC:
Register base                  : 0x1022a000
Role                           : DRAM controller domain

Related boot evidence:
Preloader DRAM type trap        : 0x6, 0x2
EMI MDL index                  : 13
Physical memory                : LPDDR4, 6 GB class device

EMI/DRAMC configuration is preloader-owned for the current clean AOSP track.
Preserve preloader.
Do not attempt memory-controller reconfiguration in Android userspace.
Future kernel-from-source work must respect the memory map and reserved-memory
handoff already proven in Sections 1 and 3.

################################################################################

# 4.5 — DVFSRC / SCPSYS / VCORE INTERCONNECT

################################################################################

DVFSRC:
Register base                  : 0x10012000
Role                           : vcore / DVFS resource coordination

SCPSYS:
Register family                : 0x10001000.scpsys
Role                           : power-domain / subsystem power coordination

SCP DVFS relationship:
Runtime surface                : /proc/scp_dvfs
SCP DVFS state                 : ON
SCP current frequency          : 250 MHz
SCP expected frequency         : 250 MHz

SSPM relationship:
SSPM live platform             : /sys/devices/platform/10440000.sspm
SSPM mailbox IRQs              : 206, 207, 208, 209

SPMFW relationship:
SPMFW firmware domain          : active
SPM reserved memory            : 0x77ff0000 size 0x10000

DVFSRC, SCPSYS, SCP DVFS, SSPM, and SPMFW are interlocked.
Do not alter SCP/SSPM/SPMFW firmware or kernel nodes independently.
Power, thermal, low-power, and wake behavior depend on this fabric.

################################################################################

# 4.6 — GCE / CMDQ / MAILBOX DOMAIN

################################################################################

GCE / CMDQ:
Register base                  : 0x10238000
Platform node                  : 10238000.gce
Modalias                       : of:NgceT<NULL>Cmediatek,gceCsyscon
Runtime IRQ                   : 201 mtk_cmdq

GCE mailbox:
Platform node                  : 10238000.gce_mbox
Modalias                       : of:Ngce_mboxT<NULL>Cmediatek,mailbox-gce

GCE secure mailbox:
Platform node                  : 10238000.gce_mbox_svp
Role                           : secure / protected command queue mailbox path

Known devicetree functional surfaces:
stream_done
frame_done
MDP
DISP
CAMSYS
VDEC / VENC
USB
SCP SRAM
secure_thread
mailbox-gce
thread_count

GCE/CMDQ is a central DMA/command fabric for camera, display, media, USB, and
possibly secure paths.
Camera/display bring-up must preserve GCE/CMDQ nodes.
Do not treat GCE as optional if display/camera/media paths are required.

################################################################################

# 4.7 — DMA FABRIC

################################################################################

AP DMA / UART DMA:
Platform node                  : 11000680.dma-controller
Modalias                       : of:Ndma-controllerT<NULL>Cmediatek,mt6577-uart-dma
Runtime DMA channels:
dma0chan0
dma0chan1
dma0chan2
dma0chan3

CQDMA:
Platform node                  : 10212000.cq_dma
Modalias                       : of:Ncq_dmaT<NULL>Cmediatek,mt-cqdma-v1
Runtime IRQs                  : 194 CQDMA, 195 CQDMA

Modem CLDMA:
AO register family:
10014000.mdcldma
10014000.apcldmain_ao
10014400.apcldmaout_ao
10014800.apcldmamisc_ao
10015000.mdcldmain_ao
10015400.mdcldmaout_ao
10015800.mdcldmamisc_ao

Non-AO register family:
1021b000.apcldmain
1021b400.apcldmaout
1021b800.apcldmamisc
1021c000.mdcldmain
1021c400.mdcldmaout
1021c800.mdcldmamisc

Runtime IRQ                   : 197 CLDMA_AP

Display DMA:
MDP RDMA0                      : 14004000.mdp_rdma0
MDP WDMA0                      : 14008000.mdp_wdma0
DISP RDMA0                     : 1400d000.disp_rdma0
DISP WDMA0                     : 1400e000.disp_wdma0
Runtime IRQs                  : 260 rdma0, 261 wdma0

DMA is not one block on this platform.
UART/AP DMA, CQDMA, CLDMA, CMDQ/GCE, and display DMA are separate build
dependencies.
A future kernel or custom OS must preserve DMA channel/controller mappings for
serial, modem, display, camera/media, and command-queue paths.

################################################################################

# 4.8 — I2C CONTROLLER MATRIX

################################################################################

All seven I2C controllers are live-declared in devicetree, visible at runtime,
and use compatible string:

compatible                     : mediatek,i2c

I2C0:
Node                           : i2c0@11007000
Runtime adapter                : i2c-0
Adapter name                   : mt-i2c
Register range 0               : 0x11007000 size 0x1000
Register range 1               : 0x11000080 size 0x80
DT interrupt cells             : 0x00000000 0x00000054 0x00000008
DT hwirq                       : 0x54 = 84
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0x53 = 83
SDA GPIO                       : 0x52 = 82
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : touch child present

I2C1:
Node                           : i2c1@11008000
Runtime adapter                : i2c-1
Adapter name                   : mt-i2c
Register range 0               : 0x11008000 size 0x1000
Register range 1               : 0x11000100 size 0x80
DT interrupt cells             : 0x00000000 0x00000055 0x00000008
DT hwirq                       : 0x55 = 85
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0x54 = 84
SDA GPIO                       : 0x51 = 81
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : no exposed child node in /sys/bus/i2c/devices

I2C2:
Node                           : i2c2@11009000
Runtime adapter                : i2c-2
Adapter name                   : mt-i2c
Register range 0               : 0x11009000 size 0x1000
Register range 1               : 0x11000180 size 0x180
DT interrupt cells             : 0x00000000 0x00000056 0x00000008
DT hwirq                       : 0x56 = 86
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0x67 = 103
SDA GPIO                       : 0x68 = 104
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : primary camera bus chain present

I2C3:
Node                           : i2c3@1100f000
Runtime adapter                : i2c-3
Adapter name                   : mt-i2c
Register range 0               : 0x1100f000 size 0x1000
Register range 1               : 0x11000300 size 0x100
DT interrupt cells             : 0x00000000 0x00000057 0x00000008
DT hwirq                       : 0x57 = 87
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0x32 = 50
SDA GPIO                       : 0x33 = 51
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : no exposed child node in /sys/bus/i2c/devices

I2C4:
Node                           : i2c4@11011000
Runtime adapter                : i2c-4
Adapter name                   : mt-i2c
Register range 0               : 0x11011000 size 0x1000
Register range 1               : 0x11000400 size 0x180
DT interrupt cells             : 0x00000000 0x00000058 0x00000008
DT hwirq                       : 0x58 = 88
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0x69 = 105
SDA GPIO                       : 0x6a = 106
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : Wiite CP / heart-rate / CCU chain present

I2C5:
Node                           : i2c5@11016000
Runtime adapter                : i2c-5
Adapter name                   : mt-i2c
Register range 0               : 0x11016000 size 0x1000
Register range 1               : 0x11000580 size 0x80
DT interrupt cells             : 0x00000000 0x00000082 0x00000008
DT hwirq                       : 0x82 = 130
Trigger flags                  : 0x8
Clock frequency                : 0x0033e140 = 3,400,000 Hz
SCL GPIO                       : 0x30 = 48
SDA GPIO                       : 0x31 = 49
mediatek,use-open-drain        : not shown in live property check
mediatek,hs_only               : present, zero-length boolean
Child state                    : MT6370 sub-PMU / charger child present

I2C6:
Node                           : i2c6@1100d000
Runtime adapter                : i2c-6
Adapter name                   : mt-i2c
Register range 0               : 0x1100d000 size 0x1000
Register range 1               : 0x11000600 size 0x80
DT interrupt cells             : 0x00000000 0x00000083 0x00000008
DT hwirq                       : 0x83 = 131
Trigger flags                  : 0x8
Clock frequency                : 0x00061a80 = 400,000 Hz
SCL GPIO                       : 0xa1 = 161
SDA GPIO                       : 0xa2 = 162
mediatek,use-open-drain        : present, zero-length boolean
mediatek,hs_only               : present, zero-length boolean
Child state                    : camera_main_two chain present

Runtime I2C Linux IRQ range:
Observed Linux IRQ entries     : 220–226 mt-i2c
Note                           : DT hwirq values above are the controller
interrupt cells; Linux IRQ numbers are the
runtime mapped IRQs.

I2C0, I2C1, I2C2, I2C3, I2C4, and I2C6 are 400 kHz buses.
I2C5 is a 3.4 MHz high-speed PMU bus.
I2C5 must not be treated like the other open-drain 400 kHz buses.
The I2C controller count is seven, not six.
All seven controllers must be retained in device tree and kernel configuration.

################################################################################

# 4.9 — I2C CHILD-DEVICE TOPOLOGY

################################################################################

I2C0 child topology:
Device                         : 0-005d
Address                        : 0x5d
Name                           : cap_touch
Driver                         : /sys/bus/i2c/drivers/hyn_ts
Modalias                       : i2c:cap_touch
Function class                 : touch controller
Build action                   : preserve for touch input

I2C1 child topology:
Runtime adapter                : i2c-1
Exposed child devices          : none
Build action                   : preserve controller; no child driver required from
live sysfs evidence

I2C2 child topology:
Device                         : 2-000c
Address                        : 0x0c
Name                           : camera_main_af
Driver                         : /sys/bus/i2c/drivers/MAINAF
Modalias                       : i2c:camera_main_af
Function class                 : main camera actuator / AF path

Device                         : 2-0010
Address                        : 0x10
Name                           : ccu_sensor_i2c_2_hw
Driver                         : /sys/bus/i2c/drivers/ccu_i2c_2_hwtrg
Modalias                       : i2c:ccu_sensor_i2c_2_hw
Function class                 : camera CCU I2C hardware trigger

Device                         : 2-001a
Address                        : 0x1a
Name                           : camera_main
Driver                         : /sys/bus/i2c/drivers/kd_camera_hw
Modalias                       : i2c:camera_main
Function class                 : main camera sensor path

Device                         : 2-002d
Address                        : 0x2d
Name                           : camera_sub
Driver                         : /sys/bus/i2c/drivers/kd_camera_hw_bus2
Modalias                       : i2c:camera_sub
Function class                 : sub camera sensor path

Device                         : 2-0050
Address                        : 0x50
Name                           : camera_main_eeprom
Driver                         : /sys/bus/i2c/drivers/CAM_CAL_DRV
Modalias                       : i2c:camera_main_eeprom
Function class                 : main camera calibration EEPROM

Device                         : 2-0052
Address                        : 0x52
Name                           : camera_sub_eeprom
Driver                         : /sys/bus/i2c/drivers/CAM_CAL_DEV2
Modalias                       : i2c:camera_sub_eeprom
Function class                 : sub camera calibration EEPROM

I2C3 child topology:
Runtime adapter                : i2c-3
Exposed child devices          : none
Build action                   : preserve controller; no live child node exposed

I2C4 child topology:
Device                         : 4-0011
Address                        : 0x11
Name                           : ccu_sensor_i2c_4_hw
Driver                         : /sys/bus/i2c/drivers/ccu_i2c_4_hwtrg
Modalias                       : i2c:ccu_sensor_i2c_4_hw
Function class                 : CCU I2C hardware trigger path

Device                         : 4-0033
Address                        : 0x33
Name                           : heartrate
Driver                         : unresolved / no normal /sys/bus/i2c/drivers target shown
Modalias                       : i2c:heartrate
Function class                 : heart-rate / PAR2822 path candidate
Evidence rule                  : node exists; driver binding is not shown as a
normal bus-driver target in this capture

Device                         : 4-0050
Address                        : 0x50
Name                           : cp
Driver                         : /sys/bus/i2c/drivers/wiite_corp
Modalias                       : i2c:cp
Function class                 : Wiite control-plane bridge path
Build action                   : preserve for stock functionality until clean
replacement path is proven

I2C5 child topology:
Device                         : 5-0034
Address                        : 0x34
Name                           : subpmic_pmu
Driver                         : /sys/bus/i2c/drivers/mt6370_pmu
Modalias                       : i2c:subpmic_pmu
Function class                 : MT6370 sub-PMU / charger
Build action                   : preserve for charging/power functionality

I2C6 child topology:
Device                         : 6-000c
Address                        : 0x0c
Name                           : camera_main_two_af
Driver                         : unresolved / no normal /sys/bus/i2c/drivers target shown
Modalias                       : i2c:camera_main_two_af
Function class                 : third-camera actuator/AF declared path

Device                         : 6-002d
Address                        : 0x2d
Name                           : camera_main_two
Driver                         : /sys/bus/i2c/drivers/kd_camera_hw_bus3
Modalias                       : i2c:camera_main_two
Function class                 : camera_main_two / BF2553L trap path

Device                         : 6-0050
Address                        : 0x50
Name                           : camera_main_two_eep
Driver                         : /sys/bus/i2c/drivers/CAM_CAL_DEV3
Modalias                       : i2c:camera_main_two_eep
Function class                 : third-camera calibration EEPROM declared path

I2C6 evidence rule:
The full 6-000c / 6-002d / 6-0050 camera_main_two chain is live-declared.
This proves bus topology and kernel node presence.
It does not by itself prove a physically functional third camera module.
The BF2553L/camera_main_two path remains a required carry/trap until camera
HAL behavior and physical proof are fully resolved.

Build-use interpretation:
I2C child topology is build-critical.
Camera HAL, touch, Wiite bridge, PMU, CCU, and calibration drivers depend on
these bus children.
Removing “unused-looking” child nodes can break HAL startup even if physical
hardware is uncertain.

I2C3 child topology:
Runtime adapter                : i2c-3
Declared DT children           : ext_buck_lp4@57 (RT5738), ext_buck_lp4x@50 (RT5738)
Exposed live children          : none
RT5738 hardware existence      : confirmed — g_rt5738_0_hw_exist=1, g_rt5738_1_hw_exist=1
                                 from preloader pl_lk boot log
RT5738 kernel driver           : NOT loaded — zero dmesg probe, zero sysfs surface
Explanation                    : Preloader owns LPDDR4 rail configuration before
                                 kernel starts. Kernel RT5738 driver not bound.
                                 BSP package contains driver code; it does not run.
Build action                   : preserve I2C3 controller and DT child declarations.
                                 Do not expect RT5738 kernel sysfs surface.

################################################################################

# 4.10 — SPI CONTROLLER MATRIX

################################################################################

Correct SPI controller count:
Live SPI controller count      : 6
Old “3 SPI” statement          : invalid / stale
Runtime SPI children           : none exposed in /sys/bus/spi/devices
Runtime SPI masters            : six masters exposed under /sys/class/spi_master

Common SPI compatible string:
compatible                     : mediatek,mt6765-spi

Common clock names:
parent-clk
sel-clk
spi-clk

Common pad select:
mediatek,pad-select            : 0

SPI0:
Node                           : spi@1100a000
Runtime master                 : spi32766
Register range                 : 0x1100a000 size 0x1000
DT interrupt cells             : 0x00000000 0x00000076 0x00000008
DT hwirq                       : 0x76 = 118
Runtime Linux IRQ              : 228
Runtime platform device        : 1100a000.spi

SPI1:
Node                           : spi@11010000
Runtime master                 : spi32765
Register range                 : 0x11010000 size 0x1000
DT interrupt cells             : 0x00000000 0x0000007a 0x00000008
DT hwirq                       : 0x7a = 122
Runtime Linux IRQ              : 237
Runtime platform device        : 11010000.spi

SPI2:
Node                           : spi@11012000
Runtime master                 : spi32764
Register range                 : 0x11012000 size 0x1000
DT interrupt cells             : 0x00000000 0x00000080 0x00000008
DT hwirq                       : 0x80 = 128
Runtime Linux IRQ              : 238
Runtime platform device        : 11012000.spi

SPI3:
Node                           : spi@11013000
Runtime master                 : spi32763
Register range                 : 0x11013000 size 0x1000
DT interrupt cells             : 0x00000000 0x00000081 0x00000008
DT hwirq                       : 0x81 = 129
Runtime Linux IRQ              : 239
Runtime platform device        : 11013000.spi

SPI4:
Node                           : spi@11014000
Runtime master                 : spi32762
Register range                 : 0x11014000 size 0x1000
DT interrupt cells             : 0x00000000 0x0000005f 0x00000008
DT hwirq                       : 0x5f = 95
Runtime Linux IRQ              : 240
Runtime platform device        : 11014000.spi

SPI5:
Node                           : spi@11015000
Runtime master                 : spi32761
Register range                 : 0x11015000 size 0x1000
DT interrupt cells             : 0x00000000 0x00000060 0x00000008
DT hwirq                       : 0x60 = 96
Runtime Linux IRQ              : 241
Runtime platform device        : 11015000.spi

Runtime SPI device state:
/sys/bus/spi/devices           : empty
Runtime IRQ counters           : all six SPI IRQ entries present; counters zero
in current capture

The board exposes six MT6765 SPI controller domains.
Current live Android state exposes no SPI child devices.
Preserve all six SPI controller nodes for source/kernel parity.
Do not invent attached SPI children without physical or runtime proof.
Do not reduce SPI count to three.

################################################################################

# 4.11 — UART / SERIAL DOMAIN

################################################################################

UART0:
Platform node                  : 11002000.serial
MMIO                           : 0x11002000
tty                            : /dev/ttyS0
Device node                    : crw------- root root 4,64
Driver                         : ST16650V2
Runtime IRQ                    : 218
Runtime counters               : tx:0 rx:0
Modalias                       : of:NserialT<NULL>Cmediatek,mt6758-uartCmediatek,mt6577-uart
Cmdline role                   : console=ttyS0,921600n1
Build role                     : kernel console / early serial path

UART1:
Platform node                  : 11003000.serial
MMIO                           : 0x11003000
tty                            : /dev/ttyS1
Device node                    : crw-rw-rw- system system 4,65
Driver                         : ST16650V2
Runtime IRQ                    : 219
Runtime counters               : tx:8 rx:316
Modalias                       : of:NserialT<NULL>Cmediatek,mt6758-uartCmediatek,mt6577-uart
Build role                     : active secondary serial path / bridge candidate

UART0 is the kernel console path.
UART1 is live and has traffic.
UART1 is world-writable and system-owned in the live stock environment.
Do not remove UART1 if Wiite/nRF/bridge functionality is required.
nRF protocol decoding is not part of Section 4, but the physical UART1
interconnect belongs here.

################################################################################

# 4.12 — USB / MUSB / UDC DOMAIN

################################################################################

USB register domains:
USB0                           : 0x11200000
USB SIF / USB1P SIF             : 0x11210000
USB PHY secondary domain        : 0x11cc0000

Runtime UDC:
/sys/class/udc                 : musb-hdrc
UDC symlink target             : /sys/devices/platform/mt_usb/musb-hdrc/udc/musb-hdrc

Runtime gadget:
ConfigFS path                  : /config/usb_gadget/g1
Ownership                      : shell shell
Gadget state                   : present

Modalias check:
/sys/devices/platform/11200000.usb0/modalias:
no such file or directory in tested sysfs path

Runtime IRQ:
USB / musb-hdrc                : IRQ 242

USB device mode depends on MUSB/UDC and configfs gadget g1.
ADB/debug transport depends on preserving this path.
Do not assume a generic DWC3 path; this device exposes musb-hdrc.
USB0 modalias absence at the tested path must be recorded, not guessed around.

################################################################################

# 4.13 — INTERRUPT / IRQ SUMMARY

################################################################################

Critical runtime IRQs observed:
IRQ 21                         : TOUCH_PANEL-eint
IRQ 25                         : WIITE_CORP1
IRQ 27                         : WIITE_CORP0
IRQ 30                         : mt6370_pmu_irq
IRQ 183                        : SCP A IPC2HOST
IRQ 194                        : CQDMA
IRQ 195                        : CQDMA
IRQ 197                        : CLDMA_AP
IRQ 201                        : mtk_cmdq
IRQ 206                        : SSPM_MBOX
IRQ 207                        : SSPM_MBOX
IRQ 208                        : SSPM_MBOX
IRQ 209                        : SSPM_MBOX
IRQ 218                        : serial / UART0
IRQ 219                        : serial / UART1
IRQ 220–226                    : mt-i2c controller IRQ range
IRQ 228                        : 1100a000.spi
IRQ 237                        : 11010000.spi
IRQ 238                        : 11012000.spi
IRQ 239                        : 11013000.spi
IRQ 240                        : 11014000.spi
IRQ 241                        : 11015000.spi
IRQ 242                        : musb-hdrc
IRQ 260                        : rdma0
IRQ 261                        : wdma0

Touch, Wiite control-plane, MT6370 PMU, SCP IPC, SSPM mailbox, CQDMA, CLDMA,
CMDQ, serial, I2C, SPI, USB, and display DMA all have live IRQ relationships.
WIITE_CORP0 and WIITE_CORP1 are real interrupt lines, not just sysfs labels.
The IRQ map must be preserved in future kernel/device-tree work.

################################################################################

# 4.14 — WIITE CONTROL-PLANE INTERCONNECTS

################################################################################

I2C control path:
Bus                            : I2C4
Controller                     : i2c4@11011000
Runtime adapter                : i2c-4
Child                          : 4-0050
Name                           : cp
Driver                         : /sys/bus/i2c/drivers/wiite_corp
Modalias                       : i2c:cp

Runtime interrupt lines:
WIITE_CORP0                    : IRQ 27
WIITE_CORP1                    : IRQ 25

Related UART path:
UART1                          : 0x11003000 / ttyS1
Runtime counters               : tx:8 rx:316
Device node mode               : crw-rw-rw- system system

Evidence boundary:
Section 4 records the interconnect path.
nRF/Wiite protocol contents and command semantics belong to the dedicated
control-plane / nRF / sensor-controller sections.

The Wiite control-plane is physically represented by I2C4 cp@50 plus EINT
lines and likely secondary serial involvement.
Clean ROM work must intentionally decide whether to preserve, replace, or
stub this path.
Removing the node blindly risks breaking sensor/bridge/power/watch behavior.

################################################################################

# 4.15 — TOUCH INTERCONNECT

################################################################################

Touch bus:
I2C controller                 : I2C0
Runtime adapter                : i2c-0
Address                        : 0x5d
Device                         : 0-005d
Name                           : cap_touch
Driver                         : hyn_ts
Modalias                       : i2c:cap_touch

Touch IRQ:
Runtime IRQ                    : 21 TOUCH_PANEL-eint

Bus electrical/DT values:
Clock frequency                : 400,000 Hz
SCL GPIO                       : 83
SDA GPIO                       : 82
use-open-drain                 : present
hs_only                        : present

Touch depends on I2C0 and TOUCH_PANEL-eint.
Preserve I2C0 controller, cap_touch child node, hyn_ts driver relationship,
and touch EINT in kernel/device-tree work.

################################################################################

# 4.16 — POWER / CHARGER INTERCONNECT

################################################################################

Sub-PMU / charger bus:
I2C controller                 : I2C5
Runtime adapter                : i2c-5
Clock frequency                : 3,400,000 Hz
SCL GPIO                       : 48
SDA GPIO                       : 49
mediatek,hs_only               : present
use-open-drain                 : not shown

Child:
Device                         : 5-0034
Address                        : 0x34
Name                           : subpmic_pmu
Driver                         : mt6370_pmu
Modalias                       : i2c:subpmic_pmu

IRQ:
Runtime IRQ                    : 30 mt6370_pmu_irq

I2C5 is the high-speed MT6370 PMU/charger bus.
It must not be copied from the 400 kHz open-drain I2C model.
Charging, fuel, USB power, and PMU behavior depend on this path.
Preserve MT6370 PMU node and IRQ relationship.

################################################################################

# 4.17 — CAMERA / CCU I2C INTERCONNECTS

################################################################################

Primary camera bus:
Bus                            : I2C2
Clock                          : 400,000 Hz
Children:
camera_main_af@0c
ccu_sensor_i2c_2_hw@10
camera_main@1a
camera_sub@2d
camera_main_eeprom@50
camera_sub_eeprom@52

Secondary/bridge camera-control bus:
Bus                            : I2C4
Clock                          : 400,000 Hz
Children:
ccu_sensor_i2c_4_hw@11
heartrate@33
cp@50

Third camera / trap chain:
Bus                            : I2C6
Clock                          : 400,000 Hz
Children:
camera_main_two_af@0c
camera_main_two@2d
camera_main_two_eep@50

MIPI CSI receiver fabric:
CSI0A                          : 11c10000.mipi_rx_ana_csi0a
CSI0B                          : 11c11000.mipi_rx_ana_csi0b
CSI1A                          : 11c12000.mipi_rx_ana_csi1a
CSI1B                          : 11c13000.mipi_rx_ana_csi1b
CSI2A                          : 11c14000.mipi_rx_ana_csi2a
CSI2B                          : 11c15000.mipi_rx_ana_csi2b

GCE/CMDQ relationship:
Camera/display/media command paths are tied to GCE/CMDQ and frame/stream-done
signaling.

Evidence rule:
I2C nodes prove declared bus topology.
MIPI CSI nodes prove receiver fabric.
Camera physical truth and sensor-function proof belong to the camera hardware
section.
BF2553L/camera_main_two remains a required BSP/HAL carry/trap until fully
resolved.

Camera HAL stability depends on preserving I2C2, I2C4 CCU trigger, I2C6
camera_main_two chain, MIPI CSI fabric, and GCE/CMDQ support.
Do not delete camera_main_two nodes just because physical function is not yet
fully proven.

################################################################################

# 4.18 — DISPLAY / MIPI DSI / DISPLAY DMA INTERCONNECT

################################################################################

MIPI DSI TX:
Node                           : 11c80000.mipi_tx0
Role                           : display MIPI DSI transmitter

Display DMA / path:
MDP RDMA0                      : 14004000.mdp_rdma0
MDP WDMA0                      : 14008000.mdp_wdma0
DISP RDMA0                     : 1400d000.disp_rdma0
DISP WDMA0                     : 1400e000.disp_wdma0

Runtime IRQs:
rdma0                          : IRQ 260
wdma0                          : IRQ 261

Related Section 3 LK display facts:
LK panel name                  : lk_st7701S_V3_480X640_dsi_vdo
Resolution                     : 480 x 640
DSI mode                       : BURST_VDO_MODE
Lane count                     : 1
PLL_CLOCK                      : 320
Framebuffer address            : 0x7fae0000

Display depends on DSI TX, display DMA, LK handoff, and framebuffer setup.
Preserve display DMA/GCE/CMDQ nodes and DSI TX node for display bring-up.
Do not assume generic Android display path.

################################################################################

# 4.19 — SCP / SSPM / AUDIO IPI INTERCONNECTS

################################################################################

SCP:
Platform node                  : 10500000.scp
Runtime node                   : /sys/devices/platform/10500000.scp
Runtime misc                   : /sys/class/misc/scp
Runtime vcore request          : /sys/class/misc/scp_vcore_req
IRQ                            : 183 SCP A IPC2HOST
Devicetree node                : /sys/firmware/devicetree/base/scp@10500000

SCP DVFS:
Runtime node                   : /proc/scp_dvfs
Platform nodes                 : /sys/devices/platform/scp_dvfs
/sys/devices/platform/mt-scpdvfs
Runtime state                  : SCP DVFS ON, current 250 MHz, expected 250 MHz

SSPM:
Platform node                  : /sys/devices/platform/10440000.sspm
Runtime misc                   : /sys/class/misc/sspm
IRQs                           : 206–209 SSPM_MBOX

Audio IPI:
Runtime misc                   : /sys/class/misc/audio_ipi
Platform voice SCP node        : /sys/devices/platform/mt_soc_pcm_voice_scp
Platform DL1 SCP speaker node  : /sys/devices/platform/mt_soc_pcm_dl1_scp_spk
Devicetree voice SCP           : /sys/firmware/devicetree/base/mt_soc_pcm_voice_scp
Devicetree DL1 SCP speaker     : /sys/firmware/devicetree/base/mt_soc_pcm_dl1_scp_spk

SCP is an interconnect participant, not only a firmware blob.
Audio IPI, SCP DVFS, SCP IPC, SSPM mailbox, and SCP platform nodes must be
preserved for functional bring-up.
SCP firmware function details belong to Section 3; interconnect surfaces
belong here.

################################################################################

# 4.20 — SPI CHILD-ABSENCE RULE

################################################################################

Runtime SPI master evidence:
/sys/class/spi_master:
spi32766 -> 1100a000.spi
spi32765 -> 11010000.spi
spi32764 -> 11012000.spi
spi32763 -> 11013000.spi
spi32762 -> 11014000.spi
spi32761 -> 11015000.spi

Runtime SPI child evidence:
/sys/bus/spi/devices           : empty

Six SPI controllers are present and registered.
No SPI child devices are exposed in the current live runtime state.
This means controller presence is proven; attached SPI peripheral function is
not proven by live sysfs.

Preserve six SPI controllers in device tree/kernel.
Do not add child devices without evidence.
Do not reduce the controller count.


################################################################################

# 4.21 — BUILD-USE RULES

################################################################################

General:
Preserve all live controller nodes until proven unnecessary by build testing.
Controller nodes are not Android bloat.
Child nodes with HAL dependencies must not be removed merely because physical
component proof is incomplete.

I2C:
Keep all seven I2C controllers.
Keep I2C0 touch path.
Keep I2C2 camera/CCU/calibration path.
Keep I2C4 Wiite/CCU/heartrate path.
Keep I2C5 MT6370 PMU path at 3.4 MHz.
Keep I2C6 camera_main_two chain unless and until camera HAL replacement proves
it can be safely removed.
Do not treat I2C5 as a 400 kHz open-drain bus.

SPI:
Keep all six SPI controllers.
Do not invent child devices.
Do not reduce SPI count to three.

UART:
Keep ttyS0 as console/serial0 path.
Keep ttyS1 as active secondary serial/bridge path.
Preserve ttyS1 permissions intentionally or replace with a controlled bridge
design in clean ROM policy.

USB:
Preserve musb-hdrc UDC and configfs gadget path for ADB/recovery/debug.
Do not assume DWC3 or generic USB controller.

DMA / GCE / CMDQ:
Preserve AP DMA, CQDMA, CLDMA, GCE, GCE mailbox, and GCE secure mailbox nodes.
Camera, display, modem, and media paths can depend on these.

IRQ:
Preserve touch EINT, Wiite EINTs, MT6370 IRQ, SCP IPC, SSPM mailbox, I2C,
SPI, serial, USB, CMDQ, CLDMA, CQDMA, RDMA, and WDMA interrupt mappings.

Physical-proof rule:
Section 4 proves interconnect topology.
Physical module proof must be carried in the relevant hardware section.

################################################################################

# 4.22 — VERIFICATION COMMANDS

################################################################################

Register map:
adb shell su -c 'cat /proc/iomem | grep -Ei "11002000|11003000|11007000|11008000|11009000|1100a000|1100d000|1100f000|11010000|11011000|11012000|11013000|11014000|11015000|11016000|11200000|11210000|11cc0000|10500000|10219000|1022a000|10238000|10012000|10007000|18000000|axi|emi|dramc|gce|mailbox|i2c|spi|uart|serial|usb|scp|dvfs|watchdog"'

I2C runtime:
adb shell su -c 'ls -la /sys/bus/i2c/devices'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-0/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-1/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-2/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-3/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-4/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-5/name'
adb shell su -c 'cat /sys/bus/i2c/devices/i2c-6/name'

I2C controller properties:
adb shell su -c 'cat /sys/firmware/devicetree/base/i2c0@11007000/name'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c0@11007000/reg'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c0@11007000/interrupts'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c0@11007000/clock-frequency'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c0@11007000/scl-gpio-id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c0@11007000/sda-gpio-id'

Repeat the same property reads for:
i2c1@11008000
i2c2@11009000
i2c3@1100f000
i2c4@11011000
i2c5@11016000
i2c6@1100d000

I2C child identity:
adb shell su -c 'cat /sys/bus/i2c/devices/<bus-address>/name'
adb shell su -c 'readlink -f /sys/bus/i2c/devices/<bus-address>/driver 2>/dev/null'
adb shell su -c 'cat /sys/bus/i2c/devices/<bus-address>/modalias 2>/dev/null'

SPI runtime:
adb shell su -c 'ls -la /sys/class/spi_master 2>/dev/null'
adb shell su -c 'ls -la /sys/bus/spi/devices 2>/dev/null'

SPI controller properties:
adb shell su -c 'cat /sys/firmware/devicetree/base/spi@1100a000/name'
adb shell su -c 'tr "\0" "\n" < /sys/firmware/devicetree/base/spi@1100a000/compatible'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/spi@1100a000/reg'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/spi@1100a000/interrupts'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/spi@1100a000/mediatek,pad-select'
adb shell su -c 'tr "\0" "\n" < /sys/firmware/devicetree/base/spi@1100a000/clock-names'

Repeat the same property reads for:
spi@11010000
spi@11012000
spi@11013000
spi@11014000
spi@11015000

UART:
adb shell su -c 'cat /proc/tty/driver/serial'
adb shell su -c 'ls -la /dev/ttyS0 /dev/ttyS1'
adb shell su -c 'readlink -f /sys/devices/platform/11002000.serial/tty/* 2>/dev/null'
adb shell su -c 'readlink -f /sys/devices/platform/11003000.serial/tty/* 2>/dev/null'
adb shell su -c 'cat /sys/devices/platform/11002000.serial/modalias'
adb shell su -c 'cat /sys/devices/platform/11003000.serial/modalias'

DMA / GCE / USB:
adb shell su -c 'ls -la /sys/devices/platform/11000680.dma-controller/dma'
adb shell su -c 'cat /sys/devices/platform/11000680.dma-controller/modalias'
adb shell su -c 'cat /sys/devices/platform/10212000.cq_dma/modalias'
adb shell su -c 'cat /sys/devices/platform/10238000.gce/modalias'
adb shell su -c 'cat /sys/devices/platform/10238000.gce_mbox/modalias'
adb shell su -c 'ls -la /sys/class/udc 2>/dev/null'
adb shell su -c 'ls -la /config/usb_gadget 2>/dev/null'

IRQ:
adb shell su -c 'cat /proc/interrupts | grep -Ei "i2c|spi|uart|serial|usb|musb|scp|sspm|gce|cmdq|dma|wiite|gpio|touch|hyn|mt6370|rdma|wdma"'

Platform/devicetree surface discovery:
adb shell su -c 'find /sys/bus/platform/devices /sys/devices/platform /sys/firmware/devicetree/base -maxdepth 2 2>/dev/null | grep -Ei "spi|i2c|uart|serial|usb|musb|dma|gce|cmdq|mailbox|emi|dramc|axi|dvfsrc|scp|ipi|audio_ipi|mipi|rdma|wdma" | sort | head -400'

################################################################################

# 4.23 — SECTION 4 CLOSURE SUMMARY

################################################################################

Closed interconnect facts:
I2C controller count           : 7
SPI controller count           : 6
UART controller count          : 2
USB UDC                        : musb-hdrc
USB gadget                     : configfs g1
AP DMA channels                : dma0chan0–dma0chan3
CQDMA                          : present
CLDMA_AP                       : present
GCE / CMDQ                     : present
GCE mailbox                    : present
GCE secure mailbox             : present
SCP IPC                        : present
SSPM mailbox                   : present
MIPI CSI receiver fabric       : present
MIPI DSI TX                    : present
Display RDMA / WDMA            : present
Touch EINT                     : present
Wiite EINTs                    : WIITE_CORP0 / WIITE_CORP1 present
MT6370 PMU IRQ                 : present

Closed I2C facts:
I2C0                           : touch / cap_touch / hyn_ts
I2C1                           : controller present, no live child
I2C2                           : camera/CCU/calibration chain
I2C3                           : controller present, no live child
I2C4                           : CCU / heartrate / Wiite CP chain
I2C5                           : 3.4 MHz MT6370 PMU high-speed bus
I2C6                           : camera_main_two three-node chain

Closed SPI facts:
SPI0–SPI5                      : six MT6765 SPI controllers
SPI masters                    : spi32766 through spi32761
SPI child devices              : none exposed
SPI pad-select                 : 0 for all six
SPI clock names                : parent-clk, sel-clk, spi-clk for all six


The correct SPI count is six.
The correct I2C count is seven.
I2C5 is the MT6370 3.4 MHz high-speed PMU bus.
I2C6 is a complete camera_main_two chain, not a single orphan node.
Wiite control-plane interconnect is represented by I2C4 cp@50 plus WIITE_CORP
interrupt lines and likely UART1 participation.
Section 4 proves bus topology, not every physical child function.

Any future AOSP ROM, kernel, driver, device-tree, or custom OS work must
preserve this interconnect map until a tested replacement proves otherwise.

Section 4 status:
COMPLETE as hardware interconnect, bus, IRQ, mailbox, and DMA map for C17S.
################################################################################

# END SECTION 4 — HARDWARE INTERCONNECTS, BUSES & DMA

################################################################################

