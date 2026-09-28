################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 5 — CLOCK, RESET, IRQ & WAKE TOPOLOGY
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 5 — CLOCK / RESET / IRQ / WAKE TOPOLOGY
#
# PURPOSE:
# This section defines the clock, reset, IRQ, wake, DVFS, low-power firmware,
# PMIC interrupt, SCP, SSPM, SPM, CIRQ, UART, I2C wake, and sensor-stack
# timing/control topology of the C17S hardware platform.
#
# This is the build-facing hardware timing and wake contract for the device.
#
# If a future recovery, AOSP ROM, kernel, driver stack, device tree, firmware
# interface, sensor replacement layer, clean Android sensor HAL, or full custom
# OS effort needs to know how clocks, reset lines, wake sources, interrupt
# parents, IRQ lines, SCP/SSPM/SPM firmware paths, PMIC wake paths, Wiite/nRF
# control IRQs, or runtime clock gates are connected, that information belongs
# here.
#
# SCOPE:
# Included:
# - Root clock tree
# - PLLs and major clock selectors
# - CPU / CCI DVFS topology
# - SCP DVFS topology
# - DVFSRC / DVFSP / SCPSYS topology
# - TOPRGU / watchdog reset topology
# - SPM low-power controller topology
# - SSPM firmware and mailbox topology
# - SCP firmware and IPI topology
# - SYS_CIRQ topology
# - PMIC interrupt topology
# - MT6370 PMU / sub-PMIC interrupt topology
# - PMIC clock-buffer topology
# - Full runtime interrupt topology
# - Wakeup-source topology
# - I2C4 Wiite / nRF / Pixart control-plane wake topology
# - I2C5 MT6370 / sub-PMIC wake topology
# - UART1 nRF / secondary serial clock and IRQ topology
# - USB, radio, modem, WLAN, CCCI, CLDMA, CMDQ wake/clock/IRQ participation
# - Clock debugfs availability and sampled per-clock exact values
# - I2C4/I2C5 devicetree numeric closure for sensor/power wake paths
# - pinctrl / GPIO / EINT phandle 0x3c parent closure
# - Anomalies and discrepancies discovered during live evidence capture
#
# Excluded:
# - nRF52832 protocol decoding.
#   nRF firmware, command map, UART frame semantics, and firmware reverse
#   engineering belong to the dedicated nRF / external sensor-controller
#   section.
#
# - Android APK/framework behavior.
#   Android package control-plane behavior belongs to APK/security sections.
#
# - Clean-ROM policy decisions.
#   This section records hardware truth. Removal, restriction, replacement,
#   SELinux hardening, and ODM decontamination decisions belong to build and
#   security policy documents.
#
# - Camera optical/electrical proof.
#   Camera-related clocks and IRQs are recorded here; camera sensor truth
#   belongs to the camera/hardware component section.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : /proc/interrupts, /proc/iomem, /proc/cpufreq, /proc/scp_dvfs
# TIER 3 : /sys/kernel/debug/clk, /sys/kernel/debug/wakeup_sources
# TIER 4 : /sys/firmware/devicetree/base
# TIER 5 : /sys/bus/i2c, /sys/bus/platform, /sys/devices/platform
# TIER 6 : Closed C17S hardware-digital-twin sections and DOC00–DOC21 corpus
# FINAL  : Beright / Albert acceptance
#
# RULES:
# Controller presence, devicetree declaration, runtime sysfs node, child node,
# bound driver, interrupt activity, wake-source registration, wake-enable state,
# clock-framework visibility, and physical component proof are separate evidence
# classes.
#
# A devicetree node proves declaration, not runtime activity.
#
# A platform node proves kernel-visible device creation.
#
# A bound driver proves runtime kernel binding.
#
# A clock debugfs entry proves clock-framework visibility.
#
# A clock with enable_count=0 may still be required if the controller is
# runtime-gated and activity is proven by IRQs or child wake state.
#
# A nonzero IRQ counter proves runtime interrupt activity.
#
# A wakeup-source row proves wake-source registration, not necessarily wake
# activity unless counters are nonzero.
#
# A wakeup file returning enabled proves kernel wake enablement for that device.
#
# A zero-length devicetree boolean property is recorded as present.
#
# Dynamic CPU/DVFS frequency captures are runtime state examples, not immutable
# hardware constants.
#
# Anomalous spelling, empty compatible output, zero-rate enabled clocks, orphan
# clocks, and no-driver uevents are preserved exactly.
################################################################################
################################################################################
# 5.1 — CANONICAL CLOCK / RESET / WAKE SUMMARY
################################################################################

Canonical Section 5 root facts:
TOPRGU / watchdog base         : 0x10007000
DVFSRC primary base            : 0x10012000
DVFSRC secondary range         : 0x00110780 size 0x80
SCPSYS register family         : multi-range, rooted at 0x10001000
SYS_CIRQ base                  : 0x10204000
DVFSP surfaces                 : 10227000.dvfsp and 110800.dvfsp
GCE / CMDQ mailbox base        : 0x10238000
SSPM base                      : 0x10440000
SCP base                       : 0x10500000
UART0 base                     : 0x11002000 / IRQ 218 / ttyS0
UART1 base                     : 0x11003000 / IRQ 219 / ttyS1
I2C4 base                      : 0x11011000 / hwirq 88 / Linux IRQ 224
I2C5 base                      : 0x11016000 / hwirq 130 / Linux IRQ 225
I2C4 clock-frequency            : 400,000 Hz
I2C5 clock-frequency            : 3,400,000 Hz
I2C clock parent rate           : 124,800,000 Hz
UART1 clock rate                : 26,000,000 Hz
SCP DVFS runtime state          : ON, current 250 MHz, expected 250 MHz
SSPM selector clock             : 273,000,000 Hz, enable_count 1
SPM selector clock              : 68,250,000 Hz, enable_count 1, prepare_count 1
pinctrl / EINT parent           : pinctrl@1000b000, phandle 0x3c
Wiite CP node                   : I2C4 address 0x50, driver wiite_corp, wakeup enabled
Heartrate node                  : I2C4 address 0x33, compatible mediatek,heartrate
CCU I2C4 child                  : I2C4 address 0x11, compatible mediatek,ccu_sensor_i2c_4_hw
MT6370 / sub-PMIC node          : I2C5 address 0x34, driver mt6370_pmu, wakeup enabled
WIITE_CORP0                     : GPIO/EINT 8, Linux IRQ 27
WIITE_CORP1                     : GPIO/EINT 6, Linux IRQ 25
MT6370 PMU EINT                 : GPIO/EINT 11, Linux IRQ 30
Main PMIC EINT                  : GPIO/EINT 144, Linux IRQ 163

Critical corrections:
I2C4/I2C5 clock gates           : idle/gated in clock snapshot, not proven unused
I2C4/I2C5 runtime role          : proven by IRQ activity and wake-enabled children
UART1 role                      : active secondary serial path, clock-enabled
SCP clock interpretation        : common-clock state and SCP DVFS state are separate
SSPM interrupt interpretation   : DT-declared IRQs and runtime-visible IRQs differ
pinctrl phandle 0x3c             : resolved to mediatek,mt6765-pinctrl at 0x1000b000

This device must not be modeled as a simple phone-style MTK clock tree.
It exposes a dense mixed timing/wake fabric: MediaTek DVFSRC, SCP DVFS, SSPM,
SPM, CIRQ, PMIC EINT, MT6370 nested IRQs, Wiite/nRF EINTs, UART1 secondary
serial, I2C4 sensor/control bus, I2C5 high-speed PMU bus, active modem/CCCI
wake paths, and many multimedia/media clocks.

Future kernel/device-tree/sensor work must preserve the controller count,
clock paths, reset paths, IRQ relationships, wake-source relationships, and
pinctrl/EINT parentage recorded here.

################################################################################
# 5.2 — MASTER CLOCK / RESET / LOW-POWER REGISTER MAP
################################################################################

The following clock/reset/low-power/wake-relevant register ranges are live-
declared by the device.

Reset / watchdog:
TOPRGU / watchdog              : 0x10007000

Power / DVFS / low-power:
SCPSYS primary family           : 0x10001000
DVFSRC primary                 : 0x10012000
DVFSRC secondary               : 0x00110780
SYS_CIRQ                       : 0x10204000
DVFSP                          : 0x10227000 / 0x00110800 runtime surface
SSPM cfgreg                    : 0x10440000
SCP                            : 0x10500000

Mailbox / command fabric:
GCE / CMDQ / mailbox           : 0x10238000
SSPM mailbox ranges            : 0x10450000 through 0x10491000 family
SCP additional ranges           : 0x105c0000 / 0x105c4000 / 0x105d4000

UART:
UART0 / serial0                : 0x11002000
UART1 / serial1                : 0x11003000

Sensor/power I2C wake buses:
I2C4                           : 0x11011000
I2C4 auxiliary range            : 0x11000400
I2C5                           : 0x11016000
I2C5 auxiliary range            : 0x11000580

GPIO / EINT parent:
pinctrl / GPIO / EINT           : 0x1000b000

PMIC / power:
Main PMIC platform node         : mt-pmic
MT6370 / sub-PMIC I2C node       : 5-0034
PMIC clock buffer node           : pmic_clock_buffer_ctrl

/proc/iomem anchors:
00110780-001107ff              : /dvfsrc@10012000
10001000-10001fff              : /scpsys@10001000
10012000-10012fff              : /dvfsrc@10012000
10238000-1023bfff              : /gce_mbox@10238000
10440000-1044ffff              : cfgreg
10500000-1053ffff              : /scp@10500000
105c0000-105c2fff              : /scp@10500000
105c4000-105c4fff              : /scp@10500000
105d4000-105d9fff              : /scp@10500000
11002000-1100201f              : serial
11003000-1100301f              : serial

Register ranges define the controller base addresses for future kernel,
device-tree, bootloader, hardware-abstraction, clock-tree, and wake-source work.

################################################################################
# 5.3 — TOPRGU / WATCHDOG RESET DOMAIN
################################################################################

Runtime platform node:
/sys/devices/platform/10007000.toprgu

Driver:
mtk-wdt

Devicetree node:
/sys/firmware/devicetree/base/toprgu@10007000

Compatible:
mediatek,mt6765-toprgu

Register property:
reg[0]                         : 0x10007000 size 0x1000

Interrupt property:
interrupts                     : <0x0 0x8b 0x0>
Hardware IRQ                   : 0x8b = 139

Runtime IRQ mapping:
Linux IRQ                      : 180
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 139
Trigger                        : Edge
Name                           : mt_wdt

TOPRGU watchdog is confirmed through platform binding, devicetree register
range, devicetree interrupt, and live /proc/interrupts runtime entry.
This is hardware truth and must not be inferred from generic MTK assumptions.

################################################################################
# 5.4 — DVFSRC / DVFSP / SCPSYS DOMAIN
################################################################################

DVFSRC runtime platform node:
/sys/devices/platform/10012000.dvfsrc

Driver:
helio-dvfsrc

Devicetree node:
/sys/firmware/devicetree/base/dvfsrc@10012000

Compatible:
mediatek,dvfsrc

DVFSRC register ranges:
reg[0]                         : 0x10012000 size 0x1000
reg[1]                         : 0x00110780 size 0x80

/proc/iomem cross-match:
00110780-001107ff              : /dvfsrc@10012000
10012000-10012fff              : /dvfsrc@10012000

DVFSRC exposes runtime platform subdirectories:
devfreq
helio-dvfsrc

DVFSRC is a two-range device on this platform. The secondary low address range
0x00110780–0x001107ff is real and appears in both devicetree and /proc/iomem.

DVFSP node A:
Platform node                  : /sys/devices/platform/10227000.dvfsp
Devicetree node                : /sys/firmware/devicetree/base/dvfsp@10227000
Compatible                     : mediatek,dvfsp
Modalias                       : of:NdvfspT<NULL>Cmediatek,dvfsp
Driver                         : no DRIVER= line shown in captured uevent

DVFSP node B:
Platform node                  : /sys/devices/platform/110800.dvfsp
Driver                         : dvfsp
Devicetree node                : /sys/firmware/devicetree/base/dvfsp@00110800
Compatible                     : mediatek,mt6765-dvfsp
Modalias                       : of:NdvfspT<NULL>Cmediatek,mt6765-dvfsp

The C17S exposes two DVFSP runtime surfaces. One is devicetree-visible without
a captured DRIVER= line. The second is bound to driver dvfsp and uses compatible
mediatek,mt6765-dvfsp. These must remain distinct in the hardware database.

SCPSYS runtime platform node:
/sys/devices/platform/10001000.scpsys

SCPSYS devicetree node:
/sys/firmware/devicetree/base/scpsys@10001000

SCPSYS register ranges:
reg[0]                         : 0x10001000 size 0x1000
reg[1]                         : 0x10006000 size 0x1000
reg[2]                         : 0x14002000 size 0x1000
reg[3]                         : 0x1020e000 size 0x1000
reg[4]                         : 0x18004000 size 0x1000
reg[5]                         : 0x18002000 size 0x1000

SCPSYS is multi-range on this device. It must not be reduced to only 0x10001000
in the hardware database.

################################################################################
# 5.5 — CPU / CCI DVFS TOPOLOGY
################################################################################

Procfs DVFS root:
/proc/cpufreq

DVFS domains:
/proc/cpufreq/MT_CPU_DVFS_L
/proc/cpufreq/MT_CPU_DVFS_LL
/proc/cpufreq/MT_CPU_DVFS_CCI

Each cluster directory contains:
cpufreq_freq
cpufreq_oppidx
cpufreq_turbo_mode
cpufreq_volt

Global procfs entries:
cpufreq_debug
cpufreq_dvfs_time_profile
cpufreq_power_mode
cpufreq_sched_disable
cpufreq_stress_test

MT_CPU_DVFS_L latest individual capture:
cpufreq_freq                   : 1749000 KHz
cpufreq_oppidx                 : 8
Vproc                          : 662500 uV
Vsram                          : 850000 uV

MT_CPU_DVFS_L earlier wildcard capture:
cpufreq_freq                   : 899500 KHz
cpufreq_oppidx                 : 15
Vproc                          : 675000 uV
Vsram                          : 850000 uV

MT_CPU_DVFS_L OPP table:
0                              : (1750000, 99375)
1                              : (1750000, 99375)
2                              : (1750000, 99375)
3                              : (1750000, 99375)
4                              : (1750000, 99375)
5                              : (1750000, 97500)
6                              : (1750000, 93750)
7                              : (1750000, 90000)
8                              : (1750000, 86250)
9                              : (1617000, 82500)
10                             : (1484000, 78750)
11                             : (1351000, 75000)
12                             : (1218000, 71250)
13                             : (1085000, 66875)
14                             : (979000, 64375)
15                             : (900000, 61875)

MT_CPU_DVFS_LL latest individual capture:
cpufreq_freq                   : 541750 KHz
cpufreq_oppidx                 : 12
Vproc                          : 862500 uV
Vsram                          : 962500 uV

MT_CPU_DVFS_LL earlier wildcard capture:
cpufreq_freq                   : 500750 KHz
cpufreq_oppidx                 : 14
Vproc                          : 662500 uV
Vsram                          : 850000 uV

MT_CPU_DVFS_LL OPP table:
0                              : (1050000, 86875)
1                              : (1050000, 86875)
2                              : (1050000, 86875)
3                              : (1050000, 86875)
4                              : (1050000, 86875)
5                              : (1050000, 86875)
6                              : (1050000, 86875)
7                              : (1050000, 86875)
8                              : (1050000, 86250)
9                              : (948000, 83125)
10                             : (846000, 79375)
11                             : (745000, 75000)
12                             : (643000, 71875)
13                             : (542000, 67500)
14                             : (501000, 66250)
15                             : (400000, 62500)

MT_CPU_DVFS_CCI latest individual capture:
cpufreq_freq                   : 391875 KHz
cpufreq_oppidx                 : 8
Vproc                          : 662500 uV
Vsram                          : 850000 uV

MT_CPU_DVFS_CCI earlier wildcard capture:
cpufreq_freq                   : 338875 KHz
cpufreq_oppidx                 : 8
Vproc                          : 625000 uV
Vsram                          : 850000 uV

MT_CPU_DVFS_CCI OPP table:
0                              : (1048000, 111875)
1                              : (997000, 106875)
2                              : (953000, 102500)
3                              : (915000, 98750)
4                              : (877000, 95000)
5                              : (839000, 91250)
6                              : (801000, 87500)
7                              : (763000, 83750)
8                              : (724000, 80000)
9                              : (658000, 76875)
10                             : (592000, 73750)
11                             : (525000, 70625)
12                             : (459000, 67500)
13                             : (392000, 64375)
14                             : (339000, 61875)
15                             : (300000, 60000)

Frequency and voltage are dynamic capture-state values.
OPP tables are the stable capability tables captured from procfs.

CCI discrepancy:
CCI latest frequency capture 391875 KHz corresponds closely to OPP 13 frequency
392000 KHz, while cpufreq_oppidx reports 8. Earlier 338875 KHz corresponds
closely to OPP 14 frequency 339000 KHz while cpufreq_oppidx also reported 8.
This discrepancy is recorded exactly. Do not silently correct cpufreq_oppidx.

################################################################################
# 5.6 — SCP / SCP DVFS DOMAIN
################################################################################

SCP runtime platform node:
/sys/devices/platform/10500000.scp

Driver:
scp

Devicetree node:
/sys/firmware/devicetree/base/scp@10500000

Compatible:
mediatek,scp

SCP register ranges:
reg[0]                         : 0x10500000 size 0x40000
reg[1]                         : 0x105c0000 size 0x3000
reg[2]                         : 0x105c4000 size 0x1000
reg[3]                         : 0x105d4000 size 0x6000

SCP devicetree interrupt:
interrupts                     : <0x0 0xc0 0x4>
Hardware IRQ                   : 0xc0 = 192

Runtime IRQ mapping:
Linux IRQ                      : 183
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 192
Trigger                        : Level
Name                           : SCP A IPC2HOST
Counter state                  : nonzero in captured interrupt table

SCP clock debugfs path:
scp_sel rate                   : 26000000
scp_sel enable_count           : 0
scp_ck rate                    : 26000000
scp_ck enable_count            : 0

SCP DVFS runtime platform surfaces:
/sys/devices/platform/scp_dvfs
/sys/devices/platform/mt-scpdvfs

SCP DVFS bound node:
Platform node                  : /sys/devices/platform/scp_dvfs
Driver                         : scpdvfs
Devicetree node                : /sys/firmware/devicetree/base/scp_dvfs
Compatible                     : mediatek,scp_dvfs

SCP DVFS platform-only node:
Platform node                  : /sys/devices/platform/mt-scpdvfs
Modalias                       : platform:mt-scpdvfs
Driver                         : no DRIVER= line shown in captured uevent

SCP DVFS procfs path:
/proc/scp_dvfs

SCP DVFS procfs entries:
/proc/scp_dvfs/pmicw_mode_debug
/proc/scp_dvfs/scp_dvfs_ctrl
/proc/scp_dvfs/scp_dvfs_sleep
/proc/scp_dvfs/scp_dvfs_state

SCP DVFS state:
scp_dvfs_state                 : scp status: sleep mode
SCP DVFS                       : ON
SCP frequency                  : cur=250MHz, expect=250MHz

Feature DVFS table:
feature 0                      : freq 75, enable 0
feature 1                      : freq 356, enable 0
feature 2                      : freq 0, enable 0
feature 3                      : freq 47, enable 0
feature 4                      : freq 26, enable 0
feature 5                      : freq 0, enable 1
feature 6                      : freq 200, enable 0
feature 7                      : freq 0, enable 0

Sensor DVFS rows captured:
sensor id 0                    : freq 2, enable 0
sensor id 1                    : freq 2, enable 0
sensor id 2                    : freq 2, enable 0
sensor id 3                    : freq 2, enable 0
sensor id 4                    : freq 2, enable 0
sensor id 7                    : freq 2, enable 0
sensor id 5                    : freq 2, enable 0
sensor id 18                   : freq 2, enable 0
sensor id 16                   : freq 2, enable 0
sensor id 17                   : freq 2, enable 0
sensor id 23                   : freq 2, enable 0
sensor id 43                   : freq 3, enable 0
sensor id 41                   : freq 2, enable 0
sensor id 28                   : freq 2, enable 0
sensor id 29                   : freq 2, enable 0
sensor id 35                   : freq 3, enable 0
sensor id 41 repeat             : freq 2, enable 0
sensor id 26                   : freq 2, enable 0
sensor id 36                   : freq 3, enable 0
Trailing rows                  : sensor id 0, freq 0, enable 0 repeated

Sleep/config entries:
scp_dvfs_sleep                 : SCP sleep is not configured by command yet.
pmicw_mode_debug               : pmicw_mode_debug_flag is not configured by command yet.

SCP common-clock debugfs reports 26 MHz with enable_count 0 for scp_sel/scp_ck,
while SCP DVFS procfs separately reports SCP DVFS ON and 250 MHz current/expected
frequency. Both facts are true and must be recorded separately.

SCP DVFS is active even while scp_dvfs_state reports sleep mode. Sensor-ID DVFS
rows exist and must be preserved because the dual sensor stack depends on
SCP/nRF/Pixart integration behavior.

################################################################################
# 5.7 — SSPM / SPM / SYS_CIRQ LOW-POWER DOMAIN
################################################################################

SSPM runtime platform node:
/sys/devices/platform/10440000.sspm

Driver:
sspm

Devicetree node:
/sys/firmware/devicetree/base/sspm@10440000

Compatible:
mediatek,sspm

SSPM register names and ranges:
cfgreg                         : 0x10440000 size 0x10000
mbox0_base                     : 0x10450000 size 0x100
mbox0_ctrl                     : 0x10451000 size 0x8
mbox1_base                     : 0x10460000 size 0x100
mbox1_ctrl                     : 0x10461000 size 0x8
mbox2_base                     : 0x10470000 size 0x100
mbox2_ctrl                     : 0x10471000 size 0x8
mbox3_base                     : 0x10480000 size 0x100
mbox3_ctrl                     : 0x10481000 size 0x8
mbox4_base                     : 0x10490000 size 0x100
mbox4_ctrl                     : 0x10491000 size 0x8

SSPM interrupt names:
ipc
mbox0
mbox1
mbox2
mbox3
mbox4

SSPM devicetree interrupt tuples:
ipc                            : hwirq 0xca = 202, flags 0x4
mbox0                          : hwirq 0xcd = 205, flags 0x4
mbox1                          : hwirq 0xce = 206, flags 0x4
mbox2                          : hwirq 0xcf = 207, flags 0x4
mbox3                          : hwirq 0xd0 = 208, flags 0x4
mbox4                          : hwirq 0xd1 = 209, flags 0x4

Runtime SSPM IRQs visible in /proc/interrupts:
Linux IRQ 206                  : MT_SYSIRQ 205 Level SSPM_MBOX
Linux IRQ 207                  : MT_SYSIRQ 206 Level SSPM_MBOX
Linux IRQ 208                  : MT_SYSIRQ 207 Level SSPM_MBOX
Linux IRQ 209                  : MT_SYSIRQ 208 Level SSPM_MBOX

SSPM clock debugfs:
sspm_sel rate                  : 273000000
sspm_sel enable_count          : 1
sspm_ck rate                   : 273000000
sspm_ck enable_count           : 0

SSPM devicetree declares six interrupts, but captured runtime /proc/interrupts
exposes four SSPM_MBOX rows. This must remain documented as DT-declared versus
runtime-visible topology.

SPM runtime platform node:
/sys/devices/platform/spm

SPM driver:
spm

SPM modalias:
platform:spm

SPM debug node:
debug_log

SPM debug_log mode:
-rw-rw-r-- root root

SPM clock path:
spm_sel rate                   : 68250000
spm_sel enable_count           : 1
spm_sel prepare_count          : 1
spm_sel flags                  : 0x00000804

SPM runtime IRQ mapping:
Linux IRQ                      : 179
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 181
Trigger                        : Level
Name                           : SPM
Counter state                  : all zero in captured table

SPM wake source:
spm wakeup source exists with zero counters in captured wakeup_sources table.

SPM is present as a bound platform driver and active clock selector. It
participates in low-power/wake topology even when the captured SPM IRQ and
wake-source counters are zero.

SYS_CIRQ runtime platform node:
/sys/devices/platform/10204000.sys_cirq

SYS_CIRQ devicetree node:
/sys/firmware/devicetree/base/sys_cirq@10204000

Compatible:
mediatek,sys_cirq

Modalias:
of:Nsys_cirqT<NULL>Cmediatek,sys_cirq

Driver:
no DRIVER= line shown in captured uevent

SYS_CIRQ runtime IRQ mapping:
Linux IRQ                      : 8
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 280
Trigger                        : Level
Name                           : CIRQ
Counter state                  : all zero in captured table

SYS_CIRQ exists as a platform node and devicetree node. The captured uevent does
not show a bound driver line. The interrupt table exposes CIRQ at MT_SYSIRQ 280 /
Linux IRQ 8 with zero counters in this capture.

################################################################################
# 5.8 — CLOCK DEBUGFS TOPOLOGY
################################################################################

Clock debugfs paths:
/sys/kernel/debug/clk
/d/clk

Captured listing count:
274 clock entries

Each sampled clock directory exposes:
clk_accuracy
clk_enable_count
clk_flags
clk_notifier_count
clk_phase
clk_prepare_count
clk_rate

This was confirmed for root clocks, CPU PLLs, CCI PLL, UART clocks, SCP/SSPM/SPM
clocks, crypto clocks, and anomaly clocks.

Clock debugfs is runtime state. Enable and prepare counts are instantaneous
capture values. A zero enable count does not automatically prove that a clock
path is unused.

################################################################################
# 5.9 — ROOT CLOCKS, PLLS, AND ORPHAN CLOCKS
################################################################################

Root and PLL clock values:
clk32k                         : rate 32000, enable 1, prepare 1, flags 0x00000020
clk26m                         : rate 26000000, enable 8, prepare 13, flags 0x00000020
mpll                           : rate 208000000
apll1                          : rate 196608000
msdcpll                        : rate 383999878
univ2pll                       : rate 2496000000
univpll                        : rate 1248000000
mmpll                          : rate 456999909
mfgpll                         : rate 459999970
mainpll                        : rate 1092000000
ccipll                         : rate 599999909, enable 1, prepare 1, flags 0x00000800
armpll                         : rate 799999878, enable 1, prepare 1, flags 0x00000800
armpll_l                       : rate 1499999573, enable 1, prepare 1, flags 0x00000800
dmpll_ck                       : rate 466000000

armpll, armpll_l, and ccipll are enabled/prepared and correspond to CPU/CCI
DVFS topology.

Orphan clock summary:
arm_div_pll1                   : enable 0, prepare 0, rate 0
arm_div_pll1_en                : enable 0, prepare 0, rate 0
ulposc1_ck                     : enable 1, prepare 1, rate 0
ulposc1_d32                    : enable 0, prepare 0, rate 0
ulposc1_d16                    : enable 1, prepare 1, rate 0
ulposc_sel                     : enable 1, prepare 1, rate 0
ulposc1_d8                     : enable 0, prepare 0, rate 0
disp_pwm_sel                   : enable 0, prepare 0, rate 0
f_fdisp_pwm_ck                 : enable 0, prepare 0, rate 0
ifr_disp_pwm                   : enable 0, prepare 0, rate 0
ulposc1_d4                     : enable 0, prepare 0, rate 0
ulposc1_d2                     : enable 0, prepare 0, rate 0

Per-clock anomaly confirmation:
ulposc1_ck                     : rate 0, enable 1, prepare 1
ulposc_sel                     : rate 0

ulposc1_ck is enabled/prepared while reporting rate 0 and appears in orphan
summary. Preserve as live anomaly.

################################################################################
# 5.10 — SENSOR-RELEVANT CLOCK PATHS
################################################################################

I2C clock-tree path:
univpll3_d2
  -> i2c_sel
    -> i2c_ck
      -> ifr_i2c_ap
      -> ifr_i2c_ccu
      -> ifr_i2c_sspm
      -> ifr_i2c_rsv
      -> ifr_i2c4
      -> ifr_i2c5
      -> ifr_i2c5a
      -> ifr_i2c5_imm
      -> ifr_i2c1a
      -> ifr_i2c1_imm
      -> ifr_i2c2a
      -> ifr_i2c2_imm
      -> ifr_i2c6

Captured sensor-relevant I2C clock values:
i2c_ck                         : rate 124800000, enable 0, prepare 0, flags 0x00000024
ifr_i2c4                       : rate 124800000, enable 0, prepare 0, flags 0x00000004
ifr_i2c5                       : rate 124800000, enable 0, prepare 0, flags 0x00000004
ifr_i2c5a                      : rate 124800000, enable 0, prepare 0, flags 0x00000004
ifr_i2c5_imm                   : rate 124800000, enable 0, prepare 0, flags 0x00000004

I2C4 and I2C5 clock paths are defined at 124.8 MHz but are idle/gated in this
debugfs snapshot. Runtime IRQ counters and wake-enabled child devices prove the
buses participate in live behavior.

UART clock-tree path:
uart_sel
  -> f_fuart_ck
    -> ifr_uart0
    -> ifr_uart1

Captured UART clock values:
uart_sel                       : rate 26000000
f_fuart_ck                     : rate 26000000
ifr_uart0                      : rate 26000000
ifr_uart1                      : rate 26000000, enable 1, prepare 1, flags 0x00000004

UART1 is clock-enabled and prepared at 26 MHz. This matches runtime serial
evidence showing /dev/ttyS1 active and supports its role in the nRF/Pixart /
secondary control path.

SCP / SSPM / SPM clock paths:
scp_sel                        : rate 26000000, enable 0
scp_ck                         : rate 26000000, enable 0
sspm_sel                       : rate 273000000, enable 1
sspm_ck                        : rate 273000000, enable 0
spm_sel                        : rate 68250000, enable 1, prepare 1, flags 0x00000804

SCP common-clock debugfs enable state does not alone represent SCP firmware
activity. SCP DVFS procfs reports SCP DVFS ON and 250 MHz current/expected
frequency. SSPM selector is enabled while leaf sspm_ck is not enabled in the
captured snapshot. SPM selector is active.

################################################################################
# 5.11 — PMIC / MT6370 / CLOCK-BUFFER TOPOLOGY
################################################################################

Main PMIC runtime node:
/sys/devices/platform/mt-pmic

Driver:
mt-pmic

Compatible:
mediatek,mt_pmic

Modalias:
of:Nmt-pmicT<NULL>Cmediatek,mt_pmic

Main PMIC interrupt parent:
interrupt-parent               : 0x3c
Resolved parent                : pinctrl@1000b000

Main PMIC interrupts property:
interrupts                     : <0x90 0x04 0x90 0x01>
Decoded EINT                   : 0x90 = 144

Runtime IRQ mapping:
Linux IRQ                      : 163
Controller domain              : mt-eint
Hardware IRQ                   : 144
Trigger                        : Level
Name                           : pmic-eint
Counter state                  : nonzero in captured interrupt table

Sysfs PMIC labels captured in research state:
/sys/devices/platform/mt-pmic/low_battery_protect_level : u:object_r:sysfs_pmu:s0
/sys/devices/platform/mt-pmic/low_battery_protect_stop  : u:object_r:sysfs_pmu:s0
/sys/devices/platform/mt-pmic/low_battery_protect_ut    : u:object_r:sysfs_pmu:s0
/sys/devices/platform/mt-pmic/pmic_access               : u:object_r:sysfs_pmu:s0

Notable PMIC sysfs surfaces visible:
pmic_access
buck_api
buck_ldo_info
buck_vcore_status
buck_vcore_voltage
buck_vproc_status
buck_vproc_voltage
buck_vmodem_status
buck_vmodem_voltage
buck_vpa_status
buck_vpa_voltage
buck_vs1_status
buck_vs1_voltage
low_battery_protect_level
low_battery_protect_stop
low_battery_protect_ut
battery_oc_protect_*
battery_percent_protect_*
dlpt_*
ldo_*_status
ldo_*_voltage
regulator/
mt-pmic:mt635x-auxadc/

Ownership notes from live listing:
low_battery_protect_stop       : radio system
low_battery_protect_ut         : radio system
pmic_access                    : root radio

The PMIC sysfs surface is large and includes writable regulator/protection/
access nodes. Section 5 records this as hardware-visible PMIC topology and
control surface. Build/security interpretation belongs elsewhere.

MT6370 / sub-PMIC I2C runtime node:
/sys/bus/i2c/devices/5-0034

Controller:
I2C5 / 11016000.i2c5

Name:
subpmic_pmu

Modalias:
i2c:subpmic_pmu

Driver:
mt6370_pmu

Wakeup:
enabled

Runtime uevent:
DRIVER                         : mt6370_pmu
OF_NAME                        : mt6370_pmu_dts
OF_FULLNAME                    : /mt6370_pmu_dts
OF_COMPATIBLE_N                : 0
MODALIAS                       : i2c:subpmic_pmu

Devicetree child node:
/sys/firmware/devicetree/base/i2c5@11016000/subpmic_pmu@34

Decoded child properties:
reg                            : 0x34
status                         : okay
compatible                     : mediatek,subpmic_pmu

MT6370 devicetree name anomaly:
mt6370_pmu_dts/name captured as mt637n_pmu_dts

MT6370 PMU EINT:
mt6370_pmu_eint status         : okay
interrupt-parent               : 0x3c
Resolved parent                : pinctrl@1000b000
interrupts                     : <0x0b 0x02 0x0b 0x00>
mt6370,intr_gpio               : <0x3c 0x0b 0x00>
mt6370,intr_gpio_num           : 0x0b = 11

Runtime parent IRQ:
Linux IRQ                      : 30
Controller domain              : mt-eint
Hardware IRQ                   : 11
Trigger                        : Edge
Name                           : mt6370_pmu_irq

Nested MT6370 PMU child IRQs captured:
281 mt6370_pmu_irq  93 Edge otp
282 mt6370_pmu_irq  94 Edge vdda_ovp
283 mt6370_pmu_irq  95 Edge vdda_uv
284 mt6370_pmu_irq   6 Edge chg_mivr
285 mt6370_pmu_irq  32 Edge chg_aiclmeasi
286 mt6370_pmu_irq  48 Edge attachi
287 mt6370_pmu_irq  68 Edge ovpctrl_uvp_d_evt
288 mt6370_pmu_irq  35 Edge chg_wdtmri
289 mt6370_pmu_irq  15 Edge chg_vbusov
290 mt6370_pmu_irq  27 Edge chg_tmri
291 mt6370_pmu_irq   4 Edge chg_treg
292 mt6370_pmu_irq  75 Edge fled_lvf
293 mt6370_pmu_irq  78 Edge fled2_short
294 mt6370_pmu_irq  79 Edge fled1_short
295 mt6370_pmu_irq 103 Edge ldo_oc
304 mt6370_pmu_irq 118 Edge bled_ocp

MT6370 / sub-PMIC is wake-enabled and has nested IRQ topology. Its device-tree
name anomaly mt637n_pmu_dts is recorded exactly.

PMIC clock-buffer runtime platform node:
/sys/devices/platform/pmic_clock_buffer_ctrl

Devicetree node:
/sys/firmware/devicetree/base/pmic_clock_buffer_ctrl

Status:
okay

Clock buffer quantity:
mediatek,clkbuf-quantity       : 7

Clock buffer config:
mediatek,clkbuf-config         : 2, 1, 1, 2, 0, 0, 0

Clock buffer driving current:
mediatek,clkbuf-driving-current: 1, 1, 1, 1, 1, 1, 1

PMIC clock buffer topology is explicitly configured for seven buffers. This
belongs in Section 5 because it affects clock and low-power behavior.

################################################################################
# 5.12 — PINCTRL / GPIO / EINT PARENT TOPOLOGY
################################################################################

Shared phandle value:
0x3c

Phandle owner:
/sys/firmware/devicetree/base/pinctrl@1000b000/phandle
/sys/firmware/devicetree/base/pinctrl@1000b000/linux,phandle

Decoded owner node:
Node                           : /sys/firmware/devicetree/base/pinctrl@1000b000
Name                           : pinctrl
Compatible                     : mediatek,mt6765-pinctrl
Register range                 : 0x1000b000 size 0x1000
Interrupts                     : <0x0 0xab 0x4>
Hardware IRQ                   : 0xab = 171
GPIO controller                : present, zero-length DT boolean property
Interrupt controller           : present, zero-length DT boolean property
#gpio-cells                    : 2
#interrupt-cells               : 4
phandle                        : 0x3c
linux,phandle                  : 0x3c

pinctrl@1000b000 is the shared GPIO/EINT parent for the Wiite CP IRQ GPIOs,
MT6370 PMU EINT path, and main PMIC interrupt-parent path. The gpio-controller
and interrupt-controller properties are zero-length devicetree boolean
properties and are recorded as present.

Shared EINT parent consumer map:
wiite_cp@50 irq-gpio           : <0x3c 0x08 0x00> -> EINT 8   -> WIITE_CORP0    -> Linux IRQ 27
wiite_cp@50 irq-gpio_1         : <0x3c 0x06 0x00> -> EINT 6   -> WIITE_CORP1    -> Linux IRQ 25
mt6370_pmu_dts intr_gpio       : <0x3c 0x0b 0x00> -> EINT 11  -> mt6370_pmu_irq -> Linux IRQ 30
mt-pmic interrupts             : <0x90 ...>       -> EINT 144 -> pmic-eint      -> Linux IRQ 163

This closes the shared GPIO/EINT interrupt-parent chain for the highest-value
wake/control paths in Section 5.

################################################################################
# 5.13 — WIITE / NRF / PIXART SENSOR-WAKE SPINE
################################################################################

Current Section 5 truth for the dual sensor stack:

Wiite/nRF/Pixart control plane requires:
- I2C4 controller 11011000.i2c4
- I2C4 clock domain at 124800000 Hz parent clock path
- I2C4 bus clock-frequency 400000 Hz
- wake-enabled 4-0050 cp node
- wiite_corp driver binding
- WIITE_CORP0 / WIITE_CORP1 IRQs
- UART1 11003000 serial path
- UART1 26 MHz enabled/prepared clock
- SCP DVFS ON / 250 MHz
- SSPM mailbox firmware path
- SPM low-power path
- MT6370/sub-PMIC wake/power path on I2C5

A ROM can boot while this stack is broken. GSI boot success does not prove the
C17S native sensor stack is working. Section 5 records the exact hardware
timing/wake/IRQ dependencies needed to avoid losing the dual sensor stack when
replacing or removing ODM userspace.

################################################################################
# 5.14 — I2C4 SENSOR-CONTROL DEVICETREE NUMERIC CLOSURE
################################################################################

I2C4 devicetree node:
/sys/firmware/devicetree/base/i2c4@11011000

Runtime platform node:
/sys/devices/platform/11011000.i2c4

Runtime adapter:
i2c-4

Driver:
mt-i2c

Compatible:
mediatek,i2c

Modalias:
of:Ni2c4T<NULL>Cmediatek,i2c

Controller numeric properties:
reg[0]                         : 0x11011000 size 0x1000
reg[1]                         : 0x11000400 size 0x180
interrupts                     : <0x0 0x58 0x8>
Hardware IRQ                   : 0x58 = 88
Runtime Linux IRQ              : 224
clock-frequency                : 0x00061a80 = 400000 Hz
clock-div                      : 5
scl-gpio-id                    : 0x69 = 105
sda-gpio-id                    : 0x6a = 106
id                             : 4
mem_len                        : 0x200 = 512

Runtime power state:
runtime_status                 : unsupported
power/control                  : auto
power/wakeup                   : no controller-level value returned in captured command

Clock debugfs:
i2c_ck                         : rate 124800000, enable 0, prepare 0, flags 0x00000024
ifr_i2c4                       : rate 124800000, enable 0, prepare 0, flags 0x00000004

Runtime IRQ mapping:
Linux IRQ                      : 224
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 88
Trigger                        : Level
Name                           : mt-i2c
Counter state                  : high/nonzero in captured table

I2C4 devicetree properties present:
compatible
scl-gpio-id
sda-gpio-id
clocks
clock-names
ch_offset_ccu
id
mem_len
ch_offset_default
#address-cells
interrupts
#size-cells
gpio_start
phandle
mediatek,hs_only
pu_cfg
mediatek,use-open-drain
reg
aed
clock-frequency
eh_cfg
linux,phandle
clock-div
name
rsel_cfg

I2C4 devicetree child nodes:
heartrate@33
ccu_sensor_i2c_4_hw@11
wiite_cp@50

I2C4 is a 400 kHz controller by devicetree clock-frequency while its
clock-framework parent path is defined at 124800000 Hz. The controller has
runtime IRQ activity and wake-enabled child device 4-0050. It must be treated
as an active control/wake bus, not as an idle clock-gated bus.

Wiite CP devicetree node:
/sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50

Wiite CP runtime node:
/sys/bus/i2c/devices/4-0050

Wiite CP symlink target:
../../../devices/platform/11011000.i2c4/i2c-4/4-0050

Decoded Wiite CP properties:
reg                            : 0x50
status                         : okay
compatible                     : wiite,cp
irq-gpio                       : <0x3c 0x08 0x00>
irq-gpio_1                     : <0x3c 0x06 0x00>

Runtime cross-match:
Name                           : cp
Modalias                       : i2c:cp
Driver                         : wiite_corp
Wakeup                         : enabled
uevent DRIVER                  : wiite_corp
uevent OF_NAME                 : wiite_cp
uevent OF_FULLNAME             : /i2c4@11011000/wiite_cp@50
uevent OF_COMPATIBLE_0         : wiite,cp
uevent OF_COMPATIBLE_N         : 1
uevent MODALIAS                : i2c:cp

IRQ cross-match:
irq-gpio                       : GPIO/EINT 8  -> WIITE_CORP0 -> Linux IRQ 27
irq-gpio_1                     : GPIO/EINT 6  -> WIITE_CORP1 -> Linux IRQ 25

Wiite CP wake/control IRQs are numerically closed from child devicetree
property to phandle owner to runtime Linux IRQ. This closes the Wiite/nRF EINT
parent path.

Heartrate / Pixart-side devicetree node:
/sys/firmware/devicetree/base/i2c4@11011000/heartrate@33

Heartrate runtime node:
/sys/bus/i2c/devices/4-0033

Heartrate symlink target:
../../../devices/platform/11011000.i2c4/i2c-4/4-0033

Decoded heartrate properties:
reg                            : 0x33
status                         : okay
compatible                     : mediatek,heartrate

Runtime cross-match:
Name                           : heartrate
Modalias                       : i2c:heartrate
uevent OF_NAME                 : heartrate
uevent OF_FULLNAME             : /i2c4@11011000/heartrate@33
uevent OF_COMPATIBLE_0         : mediatek,heartrate
uevent OF_COMPATIBLE_N         : 1
uevent MODALIAS                : i2c:heartrate
Driver symlink                 : not shown in captured command
Wakeup                         : no wakeup value returned in captured command

heartrate@33 is a live devicetree and runtime I2C child on the same I2C4 bus as
wiite_cp@50. Current evidence does not prove a bound kernel driver or
wake-enabled state for this child. It remains sensor-stack relevant because it
is declared okay and runtime-visible as i2c:heartrate.

CCU sensor I2C4 devicetree node:
/sys/firmware/devicetree/base/i2c4@11011000/ccu_sensor_i2c_4_hw@11

Decoded CCU sensor I2C4 properties:
reg                            : 0x11
status                         : okay
compatible                     : mediatek,ccu_sensor_i2c_4_hw

Properties present:
compatible
status
phandle
reg
linux,phandle
name

ccu_sensor_i2c_4_hw@11 is declared okay under I2C4. Runtime device binding and
exact operational role require separate confirmation before functional claims.
It is retained in Section 5 because it belongs to the I2C4 sensor/control
topology.

################################################################################
# 5.15 — I2C5 SUB-PMIC DEVICETREE NUMERIC CLOSURE
################################################################################

I2C5 devicetree node:
/sys/firmware/devicetree/base/i2c5@11016000

Runtime platform node:
/sys/devices/platform/11016000.i2c5

Runtime adapter:
i2c-5

Driver:
mt-i2c

Compatible:
mediatek,i2c

Modalias:
of:Ni2c5T<NULL>Cmediatek,i2c

Controller numeric properties:
reg[0]                         : 0x11016000 size 0x1000
reg[1]                         : 0x11000580 size 0x80
interrupts                     : <0x0 0x82 0x8>
Hardware IRQ                   : 0x82 = 130
Runtime Linux IRQ              : 225
clock-frequency                : 0x0033e140 = 3400000 Hz
clock-div                      : 5
scl-gpio-id                    : 0x30 = 48
sda-gpio-id                    : 0x31 = 49
id                             : 5
mem_len                        : 0x200 = 512

Runtime power state:
runtime_status                 : unsupported
power/control                  : auto
power/wakeup                   : no controller-level value returned in captured command

Clock debugfs:
i2c_ck                         : rate 124800000, enable 0, prepare 0, flags 0x00000024
ifr_i2c5                       : rate 124800000, enable 0, prepare 0, flags 0x00000004
ifr_i2c5a                      : rate 124800000, enable 0, prepare 0, flags 0x00000004
ifr_i2c5_imm                   : rate 124800000, enable 0, prepare 0, flags 0x00000004

Runtime IRQ mapping:
Linux IRQ                      : 225
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 130
Trigger                        : Level
Name                           : mt-i2c
Counter state                  : extremely high/nonzero in captured table

I2C5 devicetree properties present:
compatible
scl-gpio-id
sda-gpio-id
clocks
mediatek,use-push-pull
clock-names
id
mem_len
subpmic_pmu@34
#address-cells
interrupts
#size-cells
gpio_start
phandle
mediatek,hs_only
pu_cfg
reg
aed
clock-frequency
eh_cfg
linux,phandle
clock-div
name
rsel_cfg

I2C5 devicetree child:
subpmic_pmu@34

I2C5 is a high-speed 3.4 MHz controller by devicetree clock-frequency. Its
clock-framework parent path is defined at 124800000 Hz. Runtime IRQ counters
are very high, and child node 5-0034 is wake-enabled. This bus is a live
power/wake controller path.

Sub-PMIC devicetree node:
/sys/firmware/devicetree/base/i2c5@11016000/subpmic_pmu@34

Sub-PMIC runtime node:
/sys/bus/i2c/devices/5-0034

Sub-PMIC symlink target:
../../../devices/platform/11016000.i2c5/i2c-5/5-0034

Decoded sub-PMIC properties:
reg                            : 0x34
status                         : okay
compatible                     : mediatek,subpmic_pmu

Runtime cross-match:
Name                           : subpmic_pmu
Modalias                       : i2c:subpmic_pmu
Driver                         : mt6370_pmu
Wakeup                         : enabled
uevent DRIVER                  : mt6370_pmu
uevent OF_NAME                 : mt6370_pmu_dts
uevent OF_FULLNAME             : /mt6370_pmu_dts
uevent OF_COMPATIBLE_N         : 0
uevent MODALIAS                : i2c:subpmic_pmu

subpmic_pmu@34 is the devicetree I2C5 child for the MT6370/sub-PMIC path.
Runtime binding appears as mt6370_pmu and wakeup is enabled. The runtime uevent
maps the device to /mt6370_pmu_dts and reports OF_COMPATIBLE_N=0, while the
I2C5 child devicetree compatible is mediatek,subpmic_pmu. This discrepancy is
recorded exactly.

################################################################################
# 5.16 — UART1 SECONDARY SERIAL CLOCK / IRQ WAKE PATH
################################################################################

UART0 runtime serial state:
Index                          : 0
UART driver                    : ST16650V2
MMIO                           : 0x11002000
IRQ                            : 218
tx/rx                          : tx:0 rx:0
Flags                          : RTS|DTR|DSR|CD|RI
Role                           : ttyS0 / kernel console / early serial path

UART1 runtime serial state:
Index                          : 1
UART driver                    : ST16650V2
MMIO                           : 0x11003000
IRQ                            : 219
tx/rx                          : tx:16 rx:430 in latest captured state
Flags                          : RTS|DTR|DSR|CD|RI
Role                           : active secondary serial path / bridge candidate

UART1 clock path:
uart_sel -> f_fuart_ck -> ifr_uart1

UART1 clock values:
rate                           : 26000000
enable_count                   : 1
prepare_count                  : 1
flags                          : 0x00000004

UART1 runtime IRQ mapping:
Linux IRQ                      : 219
Controller domain              : MT_SYSIRQ
Hardware IRQ                   : 92
Trigger                        : Level
Name                           : serial
Counter state                  : nonzero in captured table

UART0 is present but idle in the captured serial state. UART1 is present,
clock-enabled, interrupt-active, and has live traffic. UART1 aligns with the
Section 4 interconnect map and the Section 5 wake/control-plane dependency.
It must not be treated as dead UART residue.

################################################################################
# 5.17 — WAKEUP SOURCE TOPOLOGY
################################################################################

Wakeup sources paths:
/sys/kernel/debug/wakeup_sources
/d/wakeup_sources

Both paths exist.

Important captured wake-source entries include:
PowerManager.SuspendLockout
PowerManagerService.Display
PowerManagerService.Broadcasts
PowerManagerService.WakeLocks
radio-interface
strongSwan
WLAN rx_thread
WLAN hif_thread
WLAN main_thread
WLAN Timer
WLAN timeout
WLAN interrupt
event0
event1
event2
eventpoll
KeyEvents
gpswakelock
bt_drv
wmtFuncCtrl
MT662x
charger suspend wakelock
cqdma_wakelock
leds wakelock
spm
pri_disp_wakelock
alarm
gtime timer wakelock
battery
gauge coulomb wakelock
battery wakelock
5-0034
4-0050
kpd wakelock
fdvt_lock_wakelock
dpe_lock_wakelock
isp_lock_wakelock
mt-rtc
ccu_lock_wakelock
md1_cldma_peer
md1_cldma_trm
ttyC6
ttyC5
multiple ccci_* modem wake sources
ttyC0
ttyC2
scp reset wakelock
scp wakelock
rawbulk-* entries
dual-role-usb20
USB suspend lock
alarmtimer
mt-pmic:mt635x-auxadc
dlpt_notify_lock wakelock
bat_percent_notify_lock wakelock
PTIM_wakelock
pmicThread_lock wakelock
mt_charger
usb
ac
charger
cmdq_wakelock

Important direct sensor/power wake rows:
4-0050                         : wake source exists; child wakeup file reports enabled
5-0034                         : wake source exists; child wakeup file reports enabled

The wakeup table proves that Wiite CP and MT6370/sub-PMIC are registered wake
sources. This is central to the C17S clock/reset/IRQ/wake topology and belongs
in Section 5.

Wake counts are runtime volatile. Presence and sample counters are evidence of
registration and capture-state activity, not immutable constants.

################################################################################
# 5.18 — FULL RUNTIME IRQ TOPOLOGY
################################################################################

Runtime interrupt domains captured:
GICv3
MT_SYSIRQ
mt-eint
mt6370_pmu_irq
IPI

Core timer and system IRQs:
2   GICv3     29  Level  arch_timer
3   GICv3     30  Level  arch_timer
6   MT_SYSIRQ 170 Level  mt-gpt
7   MT_SYSIRQ 179 Level  pmic_wrap
8   MT_SYSIRQ 280 Level  CIRQ
10  MT_SYSIRQ 24  Level  L2C_irq-0
11  MT_SYSIRQ 25  Level  L2C_irq-1
13  MT_SYSIRQ 134 Edge   amms_irq

External interrupt / board wake IRQs:
21  mt-eint 2   Edge   TOUCH_PANEL-eint
22  mt-eint 3   Edge   11240000.msdc cd
25  mt-eint 6   Level  WIITE_CORP1
27  mt-eint 8   Level  WIITE_CORP0
30  mt-eint 11  Edge   mt6370_pmu_irq
163 mt-eint 144 Level  pmic-eint

Watchdog, SCP, PMIC, modem, DMA, CMDQ:
179 MT_SYSIRQ 181 Level SPM
180 MT_SYSIRQ 139 Edge  mt_wdt
183 MT_SYSIRQ 192 Level SCP A IPC2HOST
184 MT_SYSIRQ 180 Edge  mtk-kpd
187 MT_SYSIRQ 158 Level m4u
188 MT_SYSIRQ 142 Level devapc
190 MT_SYSIRQ 148 Level CCIF_AP_DATA
194 MT_SYSIRQ 113 Level CQDMA
195 MT_SYSIRQ 114 Level CQDMA
196 MT_SYSIRQ 144 Level mpu
197 MT_SYSIRQ 165 Level CLDMA_AP
198 MT_SYSIRQ 149 Level CCIF0_AP
199 MT_SYSIRQ 261 Edge  MD_WDT
200 MT_SYSIRQ 125 Level eem
201 MT_SYSIRQ 154 Level mtk_cmdq

SSPM mailbox IRQs:
206 MT_SYSIRQ 205 Level SSPM_MBOX
207 MT_SYSIRQ 206 Level SSPM_MBOX
208 MT_SYSIRQ 207 Level SSPM_MBOX
209 MT_SYSIRQ 208 Level SSPM_MBOX

UART / I2C / SPI / USB:
218 MT_SYSIRQ 91  Level serial
219 MT_SYSIRQ 92  Level serial
220 MT_SYSIRQ 84  Level mt-i2c
221 MT_SYSIRQ 85  Level mt-i2c
222 MT_SYSIRQ 86  Level mt-i2c
223 MT_SYSIRQ 87  Level mt-i2c
224 MT_SYSIRQ 88  Level mt-i2c
225 MT_SYSIRQ 130 Level mt-i2c
226 MT_SYSIRQ 131 Level mt-i2c
227 MT_SYSIRQ 77  Level mt-pwm
228 MT_SYSIRQ 118 Level 1100a000.spi
237 MT_SYSIRQ 122 Level 11010000.spi
238 MT_SYSIRQ 128 Level 11012000.spi
239 MT_SYSIRQ 129 Level 11013000.spi
240 MT_SYSIRQ 95  Level 11014000.spi
241 MT_SYSIRQ 96  Level 11015000.spi
242 MT_SYSIRQ 73  Level musb-hdrc

Bluetooth / WLAN / connectivity:
230 MT_SYSIRQ 112 Level mtk btif irq
231 MT_SYSIRQ 132 Level mtk btif tx dma irq
232 MT_SYSIRQ 133 Level mtk btif rx dma irq
233 MT_SYSIRQ 267 Level BTIF_WAKEUP_IRQ
235 MT_SYSIRQ 270 Edge  CONN_LOG_IRQ
236 MT_SYSIRQ 268 Level wlan0

Storage / audio / GPU / display / camera / media IRQs:
243 MT_SYSIRQ 79  Level mtk-msdc
244 MT_SYSIRQ 80  Level mtk-msdc
245 MT_SYSIRQ 153 Level Afe_ISR_Handle
246 MT_SYSIRQ 266 Level BTCVSD_ISR_Handle
247 MT_SYSIRQ 259 Level pvrsrvkm
249 MT_SYSIRQ 210 Level mutex
251 MT_SYSIRQ 256 Level dpe
258 MT_SYSIRQ 217 Level ovl0
259 MT_SYSIRQ 229 Level ovl0_2l
260 MT_SYSIRQ 218 Level rdma0
261 MT_SYSIRQ 219 Level wdma0
263 MT_SYSIRQ 221 Level ccorr0
264 MT_SYSIRQ 222 Level aal0
267 MT_SYSIRQ 225 Level dsi0
269 MT_SYSIRQ 258 Level camera-fdvt
270 MT_SYSIRQ 230 Level Vcodec
271 MT_SYSIRQ 232 Level jpeg_enc_driver
272 MT_SYSIRQ 233 Level Vcodec
274 MT_SYSIRQ 236 Level cam2
275 MT_SYSIRQ 237 Level cam3
276 MT_SYSIRQ 243 Level camsv1
277 MT_SYSIRQ 244 Level camsv2
278 MT_SYSIRQ 241 Level camsv3
279 MT_SYSIRQ 242 Level camsv4
280 MT_SYSIRQ 246 Level ccu

MT6370 nested child IRQs:
281 mt6370_pmu_irq 93  Edge otp
282 mt6370_pmu_irq 94  Edge vdda_ovp
283 mt6370_pmu_irq 95  Edge vdda_uv
284 mt6370_pmu_irq 6   Edge chg_mivr
285 mt6370_pmu_irq 32  Edge chg_aiclmeasi
286 mt6370_pmu_irq 48  Edge attachi
287 mt6370_pmu_irq 68  Edge ovpctrl_uvp_d_evt
288 mt6370_pmu_irq 35  Edge chg_wdtmri
289 mt6370_pmu_irq 15  Edge chg_vbusov
290 mt6370_pmu_irq 27  Edge chg_tmri
291 mt6370_pmu_irq 4   Edge chg_treg
292 mt6370_pmu_irq 75  Edge fled_lvf
293 mt6370_pmu_irq 78  Edge fled2_short
294 mt6370_pmu_irq 79  Edge fled1_short
295 mt6370_pmu_irq 103 Edge ldo_oc
304 mt6370_pmu_irq 118 Edge bled_ocp

IPI rows:
IPI0                           : Rescheduling interrupts
IPI1                           : Function call interrupts
IPI2                           : CPU stop interrupts
IPI3                           : Timer broadcast interrupts
IPI4                           : IRQ work interrupts
IPI5                           : CPU wake-up interrupts
Err                            : 0

WIITE_CORP1 and WIITE_CORP0 are live board-level EINTs. UART1 IRQ 219 is
nonzero and matches active /dev/ttyS1. I2C4 IRQ 224 and I2C5 IRQ 225 are
nonzero/high activity and match Wiite/nRF and MT6370/sub-PMIC bus roles.

################################################################################
# 5.19 — MODEM / CONNECTIVITY / USB / AUDIO WAKE-CLOCK PATHS
################################################################################

USB clock values:
usb_top_sel                    : rate 62400000, enable 1, prepare 1
usb20_192m_ck                  : rate 192000000
usb20_192m_d32                 : rate 6000000
usb20_192m_d16                 : rate 12000000
usb20_192m_d8                  : rate 24000000
usb20_192m_d4                  : rate 48000000
usb20_48m_div                  : rate 48000000
ifr_icusb                      : rate 156000000, enable 1, prepare 1

Connectivity / modem clock values:
ifr_btif                       : rate 156000000, enable 0, prepare 1
ifr_ccif1_ap                   : rate 156000000, enable 1, prepare 1
ifr_ccif1_md                   : rate 156000000, enable 1, prepare 1
ifr_ccif_ap                    : rate 156000000, enable 1, prepare 1
ifr_ccif_md                    : rate 156000000, enable 1, prepare 1
ifr_cldmabclk                  : rate 156000000, enable 1, prepare 1
ifr_ccif2_ap                   : rate 156000000, enable 1, prepare 1
ifr_ccif2_md                   : rate 156000000, enable 1, prepare 1
ifr_ccif3_ap                   : rate 156000000, enable 0, prepare 0
ifr_ccif3_md                   : rate 156000000, enable 0, prepare 0
pg_conn                        : enable 1, prepare 1, rate 0

Audio-related captured clocks:
aud_intbus_sel                 : rate 26000000
apll1                          : rate 196608000
aud_1_sel                      : rate 196608000
aud_engen1_sel                 : rate 24576000
aud_engen1_ck                  : rate 24576000
aud_apll_tuner                 : rate 24576000
aud_22m                        : rate 24576000
audio_sel                      : rate 26000000
audio_ck                       : rate 26000000
ifr_audio                      : rate 156000000
ifr_audio26m                   : rate 26000000

Modem/CCIF/CLDMA clocks are active in the captured clock tree. This belongs in
Section 5 because the remaining unknown data-in/data-out path investigation
depends on wake, modem, clock, and IRQ topology.

################################################################################
# 5.20 — DISPLAY / CAMERA / MEDIA CLOCK-IRQ PATHS
################################################################################

Captured clock tree includes:
mm_sel
mm_ck
mm_dig_dsi_ck
mm_dsi0
mm_disp_ovl0
mm_disp_ovl0_2l
mm_disp_rsz0
mm_disp_rdma0
mm_disp_wdma0
mm_disp_color0
mm_disp_ccorr0
mm_disp_aal0
mm_disp_gamma0
mm_disp_dither0
mm_mdp_rdma0
mm_mdp_ccorr0
mm_mdp_rsz0
mm_mdp_rsz1
mm_mdp_tdshp0
mm_mdp_wrot0
mm_mdp_wdma0
mm_smi_common
mm_smi_comm0
mm_smi_comm1
mm_smi_larb0
mm_smi_cam_ck
mm_smi_img_ck
mm_cam_mdp_ck
mm_img_dl_relay
mm_imgdl_async
mm_fake_eng
cam
cam_larb3
cam_dfp_vad
camtg
cam_seninf
camsv0
camsv1
camsv2
cam_ccu
img_larb2
img_dip
img_fdvt
img_dpe
img_rsc
jpgenc
venc_set0_larb
venc_set1_venc
venc_set3_vdec

Primary multimedia rate captured:
mm_sel / mm_ck media domain rate : 228499954

Sensor interface clock path:
seninf_sel                     : rate 208000000
f_fseninf_ck                   : rate 208000000
mipi0a_csr_0a                  : rate 208000000
mipi0b_csr_0b                  : rate 208000000
mipi1a_csr_1a                  : rate 208000000
mipi1b_csr_1b                  : rate 208000000
mipi2a_csr_2a                  : rate 208000000
mipi2b_csr_2b                  : rate 208000000

Camera timing clocks:
camtg_sel                      : rate 24000000
camtg1_sel                     : rate 24000000
camtg2_sel                     : rate 24000000
camtg3_sel                     : rate 24000000

Power-gate entries:
pg_cam
pg_conn
pg_dis
pg_dpy
pg_ifr
pg_isp
pg_md1
pg_mfg
pg_mfg_async
pg_mfg_core0
pg_vcodec

Anomaly:
mm_fake_eng exists as a live clock debugfs entry.

Display/camera/media clocks are mostly idle in the captured clock summary but
are real hardware clock domains. The mm_fake_eng clock name is preserved exactly
as live evidence.

################################################################################
# 5.21 — ANOMALY / DISCREPANCY REGISTER
################################################################################

Research-state SELinux warning:
Current capture state is permissive/rooted/unlocked for research. Do not
describe factory stock as permissive based only on this Section 5 capture.

MT6370 name anomaly:
Live devicetree name property for mt6370_pmu_dts captured as mt637n_pmu_dts.
Recorded exactly. Do not silently correct.

CCI DVFS mismatch:
CCI cpufreq_freq captured near OPP 13/14 values while cpufreq_oppidx reports 8.
Recorded as procfs discrepancy. Do not silently correct.

ULPOSC orphan anomaly:
ulposc1_ck enable_count 1 prepare_count 1 rate 0.
ulposc_sel rate 0.
Recorded as live orphan-clock anomaly.

mm_fake_eng clock name:
mm_fake_eng exists in live clock debugfs.
Function unresolved in this section. Preserve exact clock name.

SCP clock/DVFS split:
scp_sel/scp_ck debugfs rate 26 MHz with enable_count 0.
SCP DVFS procfs reports ON, cur=250MHz, expect=250MHz.
Both facts are true. SCP firmware/DVFS state must not be reduced to
common-clock debugfs state.

SSPM DT/runtime interrupt mismatch:
SSPM devicetree declares ipc + mbox0–mbox4, hwirq 202 and 205–209.
Runtime /proc/interrupts exposes SSPM_MBOX rows for MT_SYSIRQ 205–208.
DT-declared and runtime-visible SSPM interrupt sets are distinct.

I2C clock gate versus runtime activity:
I2C4/I2C5 clocks show enable=0 prepare=0 in clock snapshot.
I2C4/I2C5 IRQs are nonzero/high.
4-0050 and 5-0034 child devices are wake-enabled.
The clocks are runtime-gated at snapshot, not proven unused.

Sub-PMIC compatible discrepancy:
Runtime uevent for 5-0034 reports OF_NAME=mt6370_pmu_dts, OF_FULLNAME=/mt6370_pmu_dts,
and OF_COMPATIBLE_N=0. I2C5 child devicetree compatible is mediatek,subpmic_pmu.
Recorded exactly.

Android shell command-structure failure note:
The placeholder/variable owner-dir command failed because Android shell treated
placeholder redirection/variable expansion incorrectly. Targeted direct commands
returned the correct values. This does not change hardware data; it records the
capture-method correction.

################################################################################
# 5.22 — VERIFICATION COMMANDS
################################################################################

Interrupt topology:
adb shell su -c 'cat /proc/interrupts'

IOMEM clock/reset/low-power anchors:
adb shell su -c 'cat /proc/iomem | grep -Ei "10007000|10012000|00110780|10001000|10204000|10227000|10238000|10440000|10500000|105c0000|105c4000|105d4000|11002000|11003000|11011000|11016000|scp|sspm|spm|dvfs|cirq|toprgu|gce|serial"'

CPU DVFS:
adb shell su -c 'ls -R /proc/cpufreq'
adb shell su -c 'cat /proc/cpufreq/MT_CPU_DVFS_L/cpufreq_freq; cat /proc/cpufreq/MT_CPU_DVFS_L/cpufreq_oppidx; cat /proc/cpufreq/MT_CPU_DVFS_L/cpufreq_volt'
adb shell su -c 'cat /proc/cpufreq/MT_CPU_DVFS_LL/cpufreq_freq; cat /proc/cpufreq/MT_CPU_DVFS_LL/cpufreq_oppidx; cat /proc/cpufreq/MT_CPU_DVFS_LL/cpufreq_volt'
adb shell su -c 'cat /proc/cpufreq/MT_CPU_DVFS_CCI/cpufreq_freq; cat /proc/cpufreq/MT_CPU_DVFS_CCI/cpufreq_oppidx; cat /proc/cpufreq/MT_CPU_DVFS_CCI/cpufreq_volt'

SCP DVFS:
adb shell su -c 'ls -la /proc/scp_dvfs'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_state'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_ctrl'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_sleep'
adb shell su -c 'cat /proc/scp_dvfs/pmicw_mode_debug'

Clock debugfs:
adb shell su -c 'ls /sys/kernel/debug/clk | wc -l'
adb shell su -c 'cat /sys/kernel/debug/clk/clk26m/clk_rate; cat /sys/kernel/debug/clk/clk26m/clk_enable_count; cat /sys/kernel/debug/clk/clk26m/clk_prepare_count; cat /sys/kernel/debug/clk/clk26m/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/clk32k/clk_rate; cat /sys/kernel/debug/clk/clk32k/clk_enable_count; cat /sys/kernel/debug/clk/clk32k/clk_prepare_count; cat /sys/kernel/debug/clk/clk32k/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/armpll/clk_rate; cat /sys/kernel/debug/clk/armpll/clk_enable_count; cat /sys/kernel/debug/clk/armpll/clk_prepare_count; cat /sys/kernel/debug/clk/armpll/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/armpll_l/clk_rate; cat /sys/kernel/debug/clk/armpll_l/clk_enable_count; cat /sys/kernel/debug/clk/armpll_l/clk_prepare_count; cat /sys/kernel/debug/clk/armpll_l/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ccipll/clk_rate; cat /sys/kernel/debug/clk/ccipll/clk_enable_count; cat /sys/kernel/debug/clk/ccipll/clk_prepare_count; cat /sys/kernel/debug/clk/ccipll/clk_flags'

Sensor-relevant clocks:
adb shell su -c 'cat /sys/kernel/debug/clk/i2c_ck/clk_rate; cat /sys/kernel/debug/clk/i2c_ck/clk_enable_count; cat /sys/kernel/debug/clk/i2c_ck/clk_prepare_count; cat /sys/kernel/debug/clk/i2c_ck/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ifr_i2c4/clk_rate; cat /sys/kernel/debug/clk/ifr_i2c4/clk_enable_count; cat /sys/kernel/debug/clk/ifr_i2c4/clk_prepare_count; cat /sys/kernel/debug/clk/ifr_i2c4/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ifr_i2c5/clk_rate; cat /sys/kernel/debug/clk/ifr_i2c5/clk_enable_count; cat /sys/kernel/debug/clk/ifr_i2c5/clk_prepare_count; cat /sys/kernel/debug/clk/ifr_i2c5/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ifr_i2c5a/clk_rate; cat /sys/kernel/debug/clk/ifr_i2c5a/clk_enable_count; cat /sys/kernel/debug/clk/ifr_i2c5a/clk_prepare_count; cat /sys/kernel/debug/clk/ifr_i2c5a/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ifr_i2c5_imm/clk_rate; cat /sys/kernel/debug/clk/ifr_i2c5_imm/clk_enable_count; cat /sys/kernel/debug/clk/ifr_i2c5_imm/clk_prepare_count; cat /sys/kernel/debug/clk/ifr_i2c5_imm/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/ifr_uart1/clk_rate; cat /sys/kernel/debug/clk/ifr_uart1/clk_enable_count; cat /sys/kernel/debug/clk/ifr_uart1/clk_prepare_count; cat /sys/kernel/debug/clk/ifr_uart1/clk_flags'
adb shell su -c 'cat /sys/kernel/debug/clk/scp_sel/clk_rate; cat /sys/kernel/debug/clk/scp_sel/clk_enable_count; cat /sys/kernel/debug/clk/scp_ck/clk_rate; cat /sys/kernel/debug/clk/scp_ck/clk_enable_count'
adb shell su -c 'cat /sys/kernel/debug/clk/sspm_sel/clk_rate; cat /sys/kernel/debug/clk/sspm_sel/clk_enable_count; cat /sys/kernel/debug/clk/sspm_ck/clk_rate; cat /sys/kernel/debug/clk/sspm_ck/clk_enable_count'
adb shell su -c 'cat /sys/kernel/debug/clk/spm_sel/clk_rate; cat /sys/kernel/debug/clk/spm_sel/clk_enable_count; cat /sys/kernel/debug/clk/spm_sel/clk_prepare_count; cat /sys/kernel/debug/clk/spm_sel/clk_flags'

Wake sources:
adb shell su -c 'cat /sys/kernel/debug/wakeup_sources'
adb shell su -c 'cat /d/wakeup_sources'

I2C4/I2C5 runtime power and child wake:
adb shell su -c 'cat /sys/devices/platform/11011000.i2c4/modalias; cat /sys/devices/platform/11011000.i2c4/uevent'
adb shell su -c 'cat /sys/devices/platform/11016000.i2c5/modalias; cat /sys/devices/platform/11016000.i2c5/uevent'
adb shell su -c 'cat /sys/devices/platform/11011000.i2c4/power/runtime_status 2>/dev/null; cat /sys/devices/platform/11011000.i2c4/power/control 2>/dev/null; cat /sys/devices/platform/11011000.i2c4/power/wakeup 2>/dev/null'
adb shell su -c 'cat /sys/devices/platform/11016000.i2c5/power/runtime_status 2>/dev/null; cat /sys/devices/platform/11016000.i2c5/power/control 2>/dev/null; cat /sys/devices/platform/11016000.i2c5/power/wakeup 2>/dev/null'
adb shell su -c 'cat /sys/bus/i2c/devices/4-0050/name; cat /sys/bus/i2c/devices/4-0050/modalias; cat /sys/bus/i2c/devices/4-0050/power/wakeup'
adb shell su -c 'cat /sys/bus/i2c/devices/4-0033/name 2>/dev/null; cat /sys/bus/i2c/devices/4-0033/modalias 2>/dev/null; cat /sys/bus/i2c/devices/4-0033/power/wakeup 2>/dev/null'
adb shell su -c 'cat /sys/bus/i2c/devices/5-0034/name; cat /sys/bus/i2c/devices/5-0034/modalias; cat /sys/bus/i2c/devices/5-0034/power/wakeup'
adb shell su -c 'cat /sys/bus/i2c/devices/4-0050/uevent 2>/dev/null; cat /sys/bus/i2c/devices/4-0033/uevent 2>/dev/null; cat /sys/bus/i2c/devices/5-0034/uevent 2>/dev/null'

I2C4 devicetree numeric closure:
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/reg'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/interrupts'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/clock-frequency'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/clock-div'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/scl-gpio-id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/sda-gpio-id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/mem_len'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/reg'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/irq-gpio'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/irq-gpio_1'
adb shell su -c 'cat /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/status; echo'
adb shell su -c 'xxd -g1 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/compatible'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/heartrate@33/reg'
adb shell su -c 'cat /sys/firmware/devicetree/base/i2c4@11011000/heartrate@33/status; echo'
adb shell su -c 'xxd -g1 /sys/firmware/devicetree/base/i2c4@11011000/heartrate@33/compatible'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/ccu_sensor_i2c_4_hw@11/reg'
adb shell su -c 'cat /sys/firmware/devicetree/base/i2c4@11011000/ccu_sensor_i2c_4_hw@11/status; echo'
adb shell su -c 'xxd -g1 /sys/firmware/devicetree/base/i2c4@11011000/ccu_sensor_i2c_4_hw@11/compatible'

I2C5 devicetree numeric closure:
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/reg'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/interrupts'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/clock-frequency'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/clock-div'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/scl-gpio-id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/sda-gpio-id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/id'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/mem_len'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/i2c5@11016000/subpmic_pmu@34/reg'
adb shell su -c 'cat /sys/firmware/devicetree/base/i2c5@11016000/subpmic_pmu@34/status; echo'
adb shell su -c 'xxd -g1 /sys/firmware/devicetree/base/i2c5@11016000/subpmic_pmu@34/compatible'

pinctrl / phandle 0x3c closure:
adb shell su -c 'find /sys/firmware/devicetree/base -name phandle -o -name linux,phandle | while read f; do v=$(xxd -p -c4 "$f" 2>/dev/null); [ "$v" = "0000003c" ] && echo "$f" && xxd -g4 "$f"; done'
adb shell su -c 'tr "\0" "\n" < /sys/firmware/devicetree/base/pinctrl@1000b000/name 2>/dev/null'
adb shell su -c 'xxd -g1 /sys/firmware/devicetree/base/pinctrl@1000b000/compatible 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/reg 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/interrupts 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/#gpio-cells 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/#interrupt-cells 2>/dev/null'
adb shell su -c 'ls -la /sys/firmware/devicetree/base/pinctrl@1000b000/gpio-controller 2>/dev/null'
adb shell su -c 'ls -la /sys/firmware/devicetree/base/pinctrl@1000b000/interrupt-controller 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/phandle 2>/dev/null'
adb shell su -c 'xxd -g4 /sys/firmware/devicetree/base/pinctrl@1000b000/linux,phandle 2>/dev/null'
adb shell su -c 'echo WIITE_CP_IRQ_GPIO; xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/irq-gpio; echo WIITE_CP_IRQ_GPIO_1; xxd -g4 /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/irq-gpio_1; echo MT6370_EINT_PARENT; xxd -g4 /sys/firmware/devicetree/base/mt6370_pmu_eint/interrupt-parent 2>/dev/null; echo MT6370_INTR_GPIO; xxd -g4 /sys/firmware/devicetree/base/mt6370_pmu_dts/mt6370,intr_gpio 2>/dev/null; echo MT_PMIC_PARENT; xxd -g4 /sys/firmware/devicetree/base/mt-pmic/interrupt-parent 2>/dev/null'
adb shell su -c 'echo MT6370_EINT_INTERRUPTS; xxd -g4 /sys/firmware/devicetree/base/mt6370_pmu_eint/interrupts 2>/dev/null; echo MT_PMIC_INTERRUPTS; xxd -g4 /sys/firmware/devicetree/base/mt-pmic/interrupts 2>/dev/null'

################################################################################
# 5.23 — SECTION 5 CLOSURE SUMMARY
################################################################################

Canonical Section 5 closure facts:
TOPRGU watchdog                : 0x10007000, mediatek,mt6765-toprgu, Linux IRQ 180
DVFSRC                         : 0x10012000 plus 0x00110780 secondary range
SCPSYS                         : multi-range, not only 0x10001000
DVFSP                          : dual runtime surfaces 10227000.dvfsp and 110800.dvfsp
SCP                            : 0x10500000, Linux IRQ 183, SCP A IPC2HOST
SCP DVFS                       : ON, cur=250MHz, expect=250MHz
SSPM                           : 0x10440000, mailbox runtime IRQs 206–209
SPM                            : spm driver bound, spm_sel 68.25 MHz active
SYS_CIRQ                       : 10204000.sys_cirq, runtime IRQ 8 / MT_SYSIRQ 280
Clock debugfs                  : present, 274 clock entries captured
Root clocks                    : clk26m, clk32k, ARM PLLs, CCI PLL captured
I2C parent clock               : 124.8 MHz path captured
I2C4 controller                : 0x11011000, 400 kHz, hwirq 88, Linux IRQ 224
I2C4 Wiite CP                  : address 0x50, wiite,cp, wiite_corp, wakeup enabled
I2C4 Wiite CP IRQs              : EINT 8 / EINT 6 through pinctrl phandle 0x3c
I2C4 heartrate                 : address 0x33, mediatek,heartrate, status okay
I2C4 CCU sensor child           : address 0x11, mediatek,ccu_sensor_i2c_4_hw, status okay
I2C5 controller                : 0x11016000, 3.4 MHz, hwirq 130, Linux IRQ 225
I2C5 sub-PMIC                  : address 0x34, mediatek,subpmic_pmu, mt6370_pmu, wakeup enabled
UART1                          : 0x11003000, ttyS1, IRQ 219, 26 MHz clock active
pinctrl / EINT parent           : 0x1000b000, mediatek,mt6765-pinctrl, phandle 0x3c
MT6370 PMU EINT                : EINT 11, Linux IRQ 30, nested IRQs present
Main PMIC EINT                 : EINT 144, Linux IRQ 163
PMIC clock buffer              : quantity 7, config 2,1,1,2,0,0,0
Wake sources                   : 4-0050 and 5-0034 registered; child wakeup enabled
Modem/connectivity clocks       : CCIF/CLDMA/connectivity clock paths active
Display/camera/media clocks     : media, SENINF, CAMTG, MIPI, display/media domains present

Section 5 conclusion:
The C17S clock/reset/IRQ/wake topology is not standard consumer Android watch
topology. The platform exposes a dense MediaTek low-power stack combined with a
Wiite/nRF/Pixart control plane, MT6370 sub-PMIC wake path, UART1 active
secondary serial path, SCP/SSPM firmware participation, and extensive modem /
connectivity wake/clock activity.

The critical hardware truth is:
The dual sensor stack is clocked, interrupted, woken, and coordinated through
multiple layers:
I2C4 + Wiite CP + UART1 + WIITE_CORP IRQs + SCP DVFS + SSPM/SPM +
MT6370/PMIC wake/power paths.

Therefore, any future clean ROM, native sensor implementation, kernel rebuild,
or driver rewrite must account for these Section 5 truths before removing ODM
userspace dependencies or replacing the stock control plane.

Section 5 status:
COMPLETE as clock, reset, IRQ, wake, DVFS, low-power, and sensor/power wake
spine topology.
Optional future expansions are full 274-clock raw table capture, suspend/resume
IRQ deltas, wakeup_sources deltas, and direct sensor-event IRQ deltas.
################################################################################

# END SECTION 5 — CLOCK, RESET, IRQ & WAKE TOPOLOGY

################################################################################
