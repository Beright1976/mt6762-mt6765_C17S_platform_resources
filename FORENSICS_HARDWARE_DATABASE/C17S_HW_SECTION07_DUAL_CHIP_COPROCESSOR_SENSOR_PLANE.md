################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 7 — DUAL-CHIP COPROCESSOR & SENSOR PLANE
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 7 — DUAL-CHIP COPROCESSOR / SENSOR PLANE
# Closure     : CLOSED WITH OPEN PROTOCOL LEADS
#
# PURPOSE:
# This section defines the dual-chip wearable coprocessor and sensor-plane
# hardware topology of the C17S platform. It records the nRF52832 coprocessor,
# its firmware artifact, its I2C4 control path, its UART1 bridge, its Wiite
# interrupt lines, its sysfs control surfaces, the PAR2822/heartrate proxy
# boundary, the Android SensorService publication boundary, and the adjacent
# MediaTek SCP / SSPM firmware-domain boundary.
#
# This is the build-facing hardware contract for the C17S sensor plane.
#
# If a future recovery, AOSP ROM, kernel, device tree, sensor HAL,
# replacement bridge daemon, SELinux policy, firmware reverse-engineering
# workflow, or security assessment needs to preserve, emulate, restrict,
# replace, or deliberately remove a behavior in the wearable sensor plane,
# that information belongs here.
#
# SCOPE:
# Included:
# - Nordic nRF52832 silicon identity and live root identity values
# - 187_187.bin Android-side nRF firmware artifact identity
# - MT6762 <-> nRF52832 I2C4 physical/logical topology
# - I2C4 address 0x50 wiite_cp / wiite_corp primary control interface
# - I2C4 address 0x33 heartrate / PAR2822 proxy boundary
# - I2C4 address 0x11 ccu_sensor_i2c_4_hw / ccu_i2c_4_hwtrg boundary
# - UART1 / ttyS1 bridge at 0x11003000 / IRQ 219
# - Live UART bridge owner, only as hardware runtime ownership evidence
# - /sys/class/misc/wiite_corp_ctrl control/status surface
# - /sys/class/misc/wiite_con_ctrl secondary pin/IRQ control surface
# - WIITE_CORP0 / WIITE_CORP1 IRQ routing and handler semantics
# - Known nRF interrupt-status bytes and event-bus values
# - Sensor ownership classification and Android SensorService publication
# - SCP / SSPM / tinysys boundary as adjacent MTK firmware domains
# - Clean AOSP preservation and replacement contract for the sensor plane
# - Open protocol leads that are not topology blockers
#
# Excluded:
# - Full APK permission blast radius.
#   APK behavior belongs to DOC09 / APK behavior map and software-security
#   sections. This section mentions software only when it proves live hardware
#   ownership of a bus, UART, IRQ, or sysfs path.
#
# - Full reverse engineering of every UART/TWIS/BLE command byte.
#   Known command surfaces are recorded for build/replacement context, but
#   complete command decoding belongs to the firmware RE appendix.
#
# - Full camera/display/audio pipeline mapping.
#   Those belong to Section 8. Camera and display dependencies are mentioned
#   only where they touch the sensor-control plane.
#
# - Full security exploitability conclusion.
#   This section records hardware trust-boundary exposure. Threat modeling and
#   CVE writeup belong to the security assessment.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : /sys/class/misc/wiite_corp_ctrl and wiite_con_ctrl captures
# TIER 3 : /sys/bus/i2c/devices, /proc/tty/driver/serial, /proc/interrupts
# TIER 4 : dumpsys sensorservice and live runtime process/file-descriptor proof
# TIER 5 : 187_187.bin hash, strings, Ghidra forced-Thumb analysis
# TIER 6 : Closed C17S hardware-digital-twin Sections 3, 4, 5, 6 and DOC21
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# Controller presence, child-node declaration, driver binding, sysfs exposure,
# UART traffic, IRQ count, SensorService publication, process file-descriptor
# ownership, and physical silicon identity are separate evidence classes.
#
# A DT node is not automatically a physically populated external component.
# A driverless I2C child is not automatically absent hardware.
# A sensor published by SensorService is not automatically owned by nRF.
# A sysfs simulation node is not a physical sensor by itself.
# A software owner is recorded here only if it owns a live hardware path.
#
# No stale values.
# No generic MTK reference assumptions.
# No borrowed BSP sensor names promoted to physical truth.
# No protocol byte is guessed.
# No Android package behavior is treated as hardware unless it proves a live bus
# or device-node owner.
################################################################################

################################################################################

# 7.0 — SECTION AUTHORITY / CLOSURE STATE

################################################################################

Section status:
Status                         : CLOSED WITH OPEN PROTOCOL LEADS
Closure class                  : Evidence-backed dual-chip coprocessor / sensor-plane hardware map
Open-lead class                : Protocol / electrical-net naming / firmware RE only
Hardware topology blockers     : none remaining

Capture basis:
Device                         : LOKMAT APPLLP 5 MAX
Board                          : WP_C17S_PIX_TFT_D4
Kernel                         : Linux 4.9.190+
Android base                   : Android 10 / MTK Q BSP
Platform BSP identity          : mt6765
Physical AP silicon identity   : MT6762V/WB
Research state                 : rooted live stock device / Magisk true-root available

Primary live surfaces used:
/sys/class/misc/wiite_corp_ctrl
/sys/class/misc/wiite_con_ctrl
/sys/bus/i2c/devices/4-0011
/sys/bus/i2c/devices/4-0033
/sys/bus/i2c/devices/4-0050
/sys/class/tty/ttyS1
/dev/ttyS1
/proc/tty/driver/serial
/proc/interrupts
/proc/scp_dvfs
/sys/devices/platform/10500000.scp
/sys/devices/platform/10440000.sspm
/system/firmware/187_187.bin
dumpsys sensorservice

Closure meaning:
The C17S hardware topology of the wearable coprocessor and sensor plane is
complete enough for AOSP bring-up, security boundary description, device-tree
preservation, and replacement-daemon planning.

Not claimed by closure:
Every UART command byte is decoded.
Every TWIS register is decoded.
Every BLE command is functionally tested.
Every PAR2822 optical measurement frame is decoded.
Every wiite_con_ctrl pin state has a final schematic net name.

################################################################################

# 7.1 — CANONICAL DUAL-CHIP TOPOLOGY SUMMARY

################################################################################

Canonical architecture:
Application processor          : MediaTek MT6762V/WB
BSP/software identity          : MT6765
External wearable coprocessor  : Nordic nRF52832
nRF CPU                        : ARM Cortex-M4F
nRF internal flash             : 512 KB class
nRF RAM                        : 64 KB class
nRF BLE class                  : BLE 5.0 class
nRF runtime chip_id            : 0x70
nRF runtime firmware version   : 48059
nRF runtime BT MAC             : ca:77:87:c7:16:00
nRF runtime system mode        : 0
Android-side DFU image         : /system/firmware/187_187.bin
DFU image size                 : 153,832 bytes
DFU image SHA256               : 56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c

Primary AP <-> nRF paths:
I2C4 / 0x50                    : wiite_cp / wiite_corp primary control path
UART1 / ttyS1                  : raw serial bridge / historical data / coordination path
WIITE_CORP0                    : IRQ 27 / wake IRQ path
WIITE_CORP1                    : IRQ 25 / no-wake / secondary event path
wiite_corp_ctrl                : sysfs control/status path, misc major 10 minor 48
wiite_con_ctrl                 : secondary pin/IRQ control path, misc major 10 minor 50

Adjacent AP firmware domains:
SCP                            : MediaTek tinysys / sensor-hub / CHRE-class domain
SSPM                           : MediaTek system/power processor / mailbox domain
SPM                            : MediaTek power-management firmware domain

Boundary rule:
The Nordic nRF52832, the MediaTek SCP, and the MediaTek SSPM are separate
firmware domains. They must not be conflated. SCP/SSPM are AP-side MTK
firmware participants. nRF52832 is the external wearable coprocessor.

################################################################################

# 7.2 — NRF52832 SILICON IDENTITY

################################################################################

Live root identity capture:
chip_id                        : 0x70
version                        : 48059
btMAC                          : ca:77:87:c7:16:00
nrf_sys_mode                   : 0

Command used:
```bash
adb shell su -c 'echo "=== NRF ROOT IDENTITY ==="; for f in chip_id version btMAC nrf_sys_mode; do p="/sys/class/misc/wiite_corp_ctrl/$f"; [ -f "$p" ] && echo "$f=$(cat "$p" 2>/dev/null)"; done'
```

Observed output:
```text
=== NRF ROOT IDENTITY ===
chip_id=0x70
version=48059
btMAC=ca:77:87:c7:16:00
nrf_sys_mode=0
```

Classification:
chip_id                        : CONFIRMED LIVE SILICON/FIRMWARE ID VALUE
version                        : CONFIRMED LIVE NRF FIRMWARE VERSION VALUE
btMAC                          : CONFIRMED LIVE NRF BLE MAC VALUE
nrf_sys_mode                   : CONFIRMED LIVE NRF MODE VALUE

Important boundary:
These values are read through the AP-side wiite_corp driver. They are not
marketing strings and not build-system defaults. They are live runtime values
returned through the nRF control interface.

################################################################################

# 7.3 — NRF FIRMWARE ARTIFACT IDENTITY — 187_187.bin

################################################################################

Android-side firmware artifact:
Path                           : /system/firmware/187_187.bin
Size                           : 153,832 bytes
Owner                          : root:root
SELinux label                  : u:object_r:system_file:s0
Timestamp                      : 2008-12-31 18:00
SHA256                         : 56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c

Live verification command:
```bash
adb shell 'su 0 sh -c '\''echo "=== NRF FIRMWARE ARTIFACT IDENTITY ==="; ls -laZ /system/firmware/187_187.bin /system/system/firmware/187_187.bin 2>/dev/null; sha256sum /system/firmware/187_187.bin /system/system/firmware/187_187.bin 2>/dev/null'\'''
```

Observed output:
```text
-rw-r--r-- 1 root root u:object_r:system_file:s0 153832 2008-12-31 18:00 /system/firmware/187_187.bin
56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c  /system/firmware/187_187.bin
```

Canonical corrected SHA256:
```text
56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c
```

Firmware analysis identity:
Format                         : raw ARM Cortex-M flat binary
Vector table offset            : file offset 0x00000000
Initial stack pointer          : 0x20014228
Reset vector raw               : 0x10015469
Reset handler                  : 0x10015468, Thumb bit cleared
Correct Ghidra loader          : Raw Binary / BinaryLoader
Correct language               : ARM:LE:32:Cortex:default
Correct FLASH base             : 0x10010000
Correct FLASH end              : 0x100358e7
Correct ISA mode               : Thumb forced across FLASH
Non-canonical old base          : 0x00010000, do not use

Important artifact boundary:
187_187.bin is the Android-side DFU/reference firmware artifact stored on eMMC.
It is not proof that Android flashing rewrites the active nRF internal flash.
The active nRF firmware executes from nRF internal flash. This artifact must be
preserved read-only for stock compatibility and future controlled DFU analysis.

Build rule:
Keep /system/firmware/187_187.bin in the product/system payload unless and until
clean firmware-update behavior is fully replaced and validated. Do not delete
or silently replace this artifact in first-stage AOSP bring-up.

################################################################################

# 7.4 — PHYSICAL CONNECTION MAP — MT6762V/WB TO NRF52832

################################################################################

Physical/logical AP-side connection set:
I2C controller                 : i2c4@11011000
Runtime adapter                : i2c-4
Primary nRF address            : 0x50 / 4-0050 / wiite_cp
Secondary heartrate node        : 0x33 / 4-0033 / heartrate
CCU hardware-trigger node       : 0x11 / 4-0011 / ccu_sensor_i2c_4_hw
UART controller                : serial@11003000
Runtime TTY                    : /dev/ttyS1
UART IRQ                       : 219
Wiite IRQ 0                    : IRQ 27 / WIITE_CORP0
Wiite IRQ 1                    : IRQ 25 / WIITE_CORP1
Secondary controller           : /sys/class/misc/wiite_con_ctrl
Main control class             : /sys/class/misc/wiite_corp_ctrl
Power rail anchor              : VMCH / vhrs class path from MT6357 regulator set

Interpretation:
The sensor plane is not a normal single HAL-to-kernel-sensor topology. It is a
mixed AP + external coprocessor topology. The AP owns Linux, Android, I2C
mastering, UART device nodes, IRQ handling, and SensorService publication. The
nRF52832 owns its own firmware execution, BLE-facing wearable logic, and part
of the wearable sensor-control plane.

################################################################################

# 7.5 — I2C4 COPROCESSOR / SENSOR BUS

################################################################################

Runtime I2C4 enumeration:
```text
/sys/bus/i2c/devices/4-0011 -> ../../../devices/platform/11011000.i2c4/i2c-4/4-0011
/sys/bus/i2c/devices/4-0033 -> ../../../devices/platform/11011000.i2c4/i2c-4/4-0033
/sys/bus/i2c/devices/4-0050 -> ../../../devices/platform/11011000.i2c4/i2c-4/4-0050
/sys/bus/i2c/devices/i2c-4  -> ../../../devices/platform/11011000.i2c4/i2c-4
```

Canonical I2C4 child map:
```text
Address  Runtime node  Name                    Driver / state
0x11     4-0011        ccu_sensor_i2c_4_hw      ccu_i2c_4_hwtrg bound
0x33     4-0033        heartrate                DT/I2C node present; no driver in uevent
0x50     4-0050        cp / wiite_cp            wiite_corp bound
```

4-0011 details:
```text
DRIVER=ccu_i2c_4_hwtrg
OF_NAME=ccu_sensor_i2c_4_hw
OF_FULLNAME=/i2c4@11011000/ccu_sensor_i2c_4_hw@11
OF_COMPATIBLE_0=mediatek,ccu_sensor_i2c_4_hw
OF_COMPATIBLE_N=1
MODALIAS=i2c:ccu_sensor_i2c_4_hw
Driver link: /sys/bus/i2c/drivers/ccu_i2c_4_hwtrg
```

4-0033 details:
```text
OF_NAME=heartrate
OF_FULLNAME=/i2c4@11011000/heartrate@33
OF_COMPATIBLE_0=mediatek,heartrate
OF_COMPATIBLE_N=1
MODALIAS=i2c:heartrate
Driver field in uevent: absent in current capture
Driver-link read result: self path /sys/devices/platform/11011000.i2c4/i2c-4/4-0033/driver
```

4-0050 details:
```text
DRIVER=wiite_corp
OF_NAME=wiite_cp
OF_FULLNAME=/i2c4@11011000/wiite_cp@50
OF_COMPATIBLE_0=wiite,cp
OF_COMPATIBLE_N=1
MODALIAS=i2c:cp
Driver link: /sys/bus/i2c/drivers/wiite_corp
```

/dev/i2c-4 live owner check:
```text
Command: ls -laZ /dev/i2c-4; fuser -v /dev/i2c-4
Output : no /dev/i2c-4 node or no visible owner in this capture
```

Evidence interpretation:
I2C4 topology is closed. The bus, controller, child nodes, and driver binding
state are proven. Address 0x50 is confirmed as the primary nRF AP-side kernel
bridge. Address 0x33 is confirmed as a heartrate/PAR2822/proxy DT/I2C node but
not confirmed as driver-bound in this capture. Address 0x11 is confirmed as a
MediaTek CCU I2C4 hardware-trigger node.

Do not simplify 4-0033 to absent. It is a present child node with unresolved
runtime driver/protocol ownership.

################################################################################

# 7.6 — WIITE_CORP_CTRL PRIMARY CONTROL SURFACE

################################################################################

Control surface root:
Path                           : /sys/class/misc/wiite_corp_ctrl
Real path                      : /sys/devices/virtual/misc/wiite_corp_ctrl
Device class                   : misc
Major                          : 10
Minor                          : 48
SELinux object label           : u:object_r:sysfs:s0
Primary backing driver         : wiite_corp bound at I2C4 address 0x50

Live uevent:
```text
MAJOR=10
MINOR=48
DEVNAME=wiite_corp_ctrl
```

Complete live attribute map:
```text
acc_dir             -rw-rw-r-- root   root   sysfs:s0  Sensor Direction:0x0
altitude_value      -r--r--r-- root   root   sysfs:s0  Altitude value: 0
baro_enable         -rw-rw-r-- root   root   sysfs:s0  DISABLED
baro_value          -r--r--r-- root   root   sysfs:s0  Baro value: 0
bprs_simu           --w------- root   root   sysfs:s0  write-only debug/simulation
btMAC               -rw-rw-rw- system system sysfs:s0  ca:77:87:c7:16:00
chip_id             -r--r--r-- root   root   sysfs:s0  0x70
command             -rw-rw-rw- system system sysfs:s0  0
config              -rw-rw-rw- system system sysfs:s0  32-position config bitmap
delay               -rw-rw-r-- root   root   sysfs:s0  pix_delay:50
dev                 -r--r--r-- root   root   sysfs:s0  10:48
event_simu          --w------- root   root   sysfs:s0  write-only debug/simulation
hrs_simu            --w------- root   root   sysfs:s0  write-only debug/simulation
i2c_log             -rw-rw-r-- root   root   sysfs:s0  i2c_log_en:0
i2c_test            -rw-r--r-- root   root   sysfs:s0  test/control node
lcm_type            -r--r--r-- root   root   sysfs:s0  Unsupport
local_rtc           -rw-rw-r-- root   root   sysfs:s0  malformed/driver-specific RTC string
longterm            -rw-rw-rw- system system sysfs:s0  NO SUPPORT
nrf_rtc             -rw-rw-rw- system system sysfs:s0  2026-6-9 23:50:0
nrf_sys_mode        -r--r--r-- root   root   sysfs:s0  0
rst_op              -rw-rw-rw- system system sysfs:s0  unknown
spo2_simu           --w------- root   root   sysfs:s0  write-only debug/simulation
step_simu           --w------- root   root   sysfs:s0  write-only debug/simulation
tempearture_value   -r--r--r-- root   root   sysfs:s0  Temperature value: 0
tilt_simu           --w------- root   root   sysfs:s0  write-only debug/simulation
uart_op             -rw-rw-rw- system system sysfs:s0  uart enable:0
uevent              -rw-r--r-- root   root   sysfs:s0  misc uevent
upgrade_op          -rw-rw-rw- system system sysfs:s0  unknown
user_command        --w--w---- root   root   sysfs:s0  write-only command path
vdd18_con           -rw-r--r-- root   root   sysfs:s0  1.8V control lead
version             -rw-rw-rw- system system sysfs:s0  48059
vibr_test           -rw-rw-r-- root   root   sysfs:s0  NO SUPPORT
```

ODM spelling rule:
The attribute name `tempearture_value` is misspelled by the vendor and must be
preserved exactly when recording evidence or writing compatibility shims.

High-risk writable hardware-control nodes:
```text
/sys/class/misc/wiite_corp_ctrl/command      -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/config       -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/rst_op       -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/upgrade_op   -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/uart_op      -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/btMAC        -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/version      -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/nrf_rtc      -rw-rw-rw- system system
/sys/class/misc/wiite_corp_ctrl/user_command --w--w---- root root
```

Hardware interpretation:
The primary nRF control surface is not hidden. It is exposed through a misc
sysfs control surface backed by the wiite_corp I2C4 driver. The security issue
is not invisibility; the issue is that high-impact reset, upgrade, UART,
configuration, and command surfaces are exposed with weak stock DAC labels and
generic sysfs labeling.

################################################################################

# 7.7 — WIITE_CON_CTRL SECONDARY CONTROL SURFACE

################################################################################

Secondary control surface root:
Path                           : /sys/class/misc/wiite_con_ctrl
Real path                      : /sys/devices/virtual/misc/wiite_con_ctrl
Device class                   : misc
Major                          : 10
Minor                          : 50
DEVNAME                        : wiite_con_ctrl
SELinux object label           : u:object_r:sysfs:s0

Live uevent:
```text
MAJOR=10
MINOR=50
DEVNAME=wiite_con_ctrl
```

Attributes:
```text
dev                 -r--r--r-- root root sysfs:s0
irq_test            -rw-r--r-- root root sysfs:s0
pin_set             -rw-r--r-- root root sysfs:s0
uevent              -rw-r--r-- root root sysfs:s0
version             -rw-r--r-- root root sysfs:s0
power/control       -rw-r--r-- root root sysfs:s0
power/runtime_*     -r--r--r-- root root sysfs:s0
subsystem           symlink to class/misc
```

Live dmesg behavior:
```text
[WIITE_CON_CTRL]Try to set pin(2) to state(0)
[WIITE_CON_CTRL]Try to set pin(2) to state(1)
```

Observed timing pattern:
Pin 2 toggles state 0 and state 1 continuously at approximately 50–70 ms
spacing in the captured dmesg tail.

Classification:
wiite_con_ctrl is a confirmed secondary control surface. It is not a duplicate
of wiite_corp_ctrl. It exposes pin and IRQ-test functions and is involved in
the continuous Wiite connection/control loop. Exact electrical net naming of
pin 2 remains an open physical lead.

Build rule:
Carry the wiite_con driver and node initially. Do not remove or stub it until
the replacement bridge can prove that sensor event delivery, wake/no-wake IRQ
behavior, and nRF polling/control all remain functional.

################################################################################

# 7.8 — UART1 / TTYS1 HARDWARE BRIDGE

################################################################################

Canonical UART1 identity:
Runtime device node            : /dev/ttyS1
Device node owner              : system:system
Device node mode               : crw-rw-rw-
SELinux object label           : u:object_r:ttyS_device:s0
Character device major/minor   : 4,65
Sysfs class link               : /sys/class/tty/ttyS1
Physical/sysfs backtrace       : /sys/devices/platform/11003000.serial
UART controller                : ST16650V2
MMIO                           : 0x11003000
IRQ                            : 219

Corrected true-root proof:
```text
uid=0(root) gid=0(root) groups=0(root) context=u:r:magisk:s0
/dev/ttyS1 -> /sys/devices/platform/11003000.serial
serinfo:1.0 driver revision:
0: uart:ST16650V2 mmio:0x11002000 irq:218 tx:0 rx:0 RTS|DTR|DSR|CD|RI
1: uart:ST16650V2 mmio:0x11003000 irq:219 tx:40 rx:1176 RTS|DTR|DSR|CD|RI
```

TTY counter classification:
tx/rx counters are runtime state. The values `tx:40 rx:1176` are canonical for
this capture only. The immutable hardware facts are ST16650V2, MMIO
0x11003000, IRQ 219, ttyS1, and character major/minor 4,65.

Earlier permission-denial note:
An earlier `/proc/tty/driver/serial` read attempt returned permission denied
when the process remained shell-context. The corrected command under true root
read the serial proc node successfully. The denial is therefore not an evidence
claim about hidden UART data and not evidence of UART absence.

Hardware classification:
UART1 is physically instantiated, exposed as ttyS1, and has live accumulated
traffic. The UART bridge is active hardware, not a dead BSP leftover.

################################################################################

# 7.9 — UART1 RUNTIME OWNER — HARDWARE OWNERSHIP ONLY

################################################################################

Purpose of this subsection:
This subsection records only the live owner of the hardware UART bridge. It is
not the APK behavior map and not the permission blast-radius section.

Confirmed owner:
Process                        : com.wiite.devicedaemon:communication
PID at capture                 : 2305
UID/GID                        : 1000 / 1000
SELinux process domain         : u:r:system_app:s0
Open file descriptor           : /proc/2305/fd/52 -> /dev/ttyS1
Process state                  : sleeping
Linux capabilities             : all zero in process status

Live FD proof:
```text
/proc/2305/fd/52 -> /dev/ttyS1
```

Candidate owner scan result:
```text
com.wiite.WiiteCommon                         PID=1310   no ttyS1 FD shown
com.wiite.devicedaemon                        PID=1968   no ttyS1 FD shown
com.wiite.devicedaemon:communication          PID=2305   fd 52 -> /dev/ttyS1
com.wiite.wearhealthuart                      PID=2556   no ttyS1 FD shown
com.wiite.wiite_heartrate                     PID=2571   no ttyS1 FD shown
com.wiite.wiite_sleep                         PID=2580   no ttyS1 FD shown
com.wiite.wiite_bloodoxygen                   PID=2603   no ttyS1 FD shown
android.hardware.sensors@2.0-service-mediatek PID=682    no ttyS1 FD shown
```

Counter-delta proof:
```text
1: uart:ST16650V2 mmio:0x11003000 irq:219 tx:40 rx:1176 RTS|DTR|DSR|CD|RI
1: uart:ST16650V2 mmio:0x11003000 irq:219 tx:40 rx:1176 RTS|DTR|DSR|CD|RI
1: uart:ST16650V2 mmio:0x11003000 irq:219 tx:40 rx:1176 RTS|DTR|DSR|CD|RI
```

Interpretation:
The stock bridge owner holds the UART device open continuously, but no counter
change occurred during the sampled 10-second window. This means the bridge was
open and idle during that exact interval, not absent.

Build rule:
A clean ROM cannot simply remove DeviceDaemon while leaving no replacement owner
for ttyS1. The replacement bridge must own ttyS1 and preserve required command,
time, health-history, and event paths.

################################################################################

# 7.10 — WIITE IRQ ROUTING AND EVENT LINES

################################################################################

Live /proc/interrupts capture:
```text
IRQ 25 : mt-eint 6 Level WIITE_CORP1
IRQ 27 : mt-eint 8 Level WIITE_CORP0
IRQ 183: MT_SYSIRQ 192 Level SCP A IPC2HOST
IRQ 206: MT_SYSIRQ 205 Level SSPM_MBOX
IRQ 207: MT_SYSIRQ 206 Level SSPM_MBOX
IRQ 208: MT_SYSIRQ 207 Level SSPM_MBOX
IRQ 209: MT_SYSIRQ 208 Level SSPM_MBOX
IRQ 233: MT_SYSIRQ 267 Level BTIF_WAKEUP_IRQ
```

Current live counts captured:
```text
25: CPU0=743 CPU1=215 CPU2=12 CPU3=128 CPU4=88 CPU5=78 CPU6=53 CPU7=34 WIITE_CORP1
27: CPU0=564 CPU1=82  CPU2=16 CPU3=74  CPU4=71 CPU5=43 CPU6=26 CPU7=29 WIITE_CORP0
```

Canonical routing:
WIITE_CORP0                    : IRQ 27 / primary wake IRQ path
WIITE_CORP1                    : IRQ 25 / secondary no-wake or event path

Handler evidence from prior dmesg/DOC21:
WIITE_CORP0 / IRQ 27           : wiite_corp_wakeirq_handler_bottom
WIITE_CORP1 / IRQ 25           : wiite_corp_nowakeirq_handler_bottom

Live-state classification:
Both WIITE_CORP0 and WIITE_CORP1 are real, counting interrupt lines. They are
not just labels. They must be preserved in device-tree and kernel configuration.

Wakeup-source grep limitation:
A broad recursive wakeup-source grep was interrupted. This section therefore
claims IRQ identity and handler routing from /proc/interrupts and dmesg/DOC21,
but does not claim a fresh `/sys/kernel/debug/wakeup_sources` row from that
specific interrupted command.

################################################################################

# 7.11 — NRF INTERRUPT STATUS AND EVENT SEMANTICS

################################################################################

Interrupt status register:
The wiite_corp path reads a 4-byte interrupt-status value on nRF IRQ assertion.
Known decoded status values are evidence from dmesg and DOC21 analysis.

Known values:
```text
00 00 10 00  : SpO2 data ready, Byte2 bit 4 set
00 00 00 02  : event sensor interrupt, Byte3 bit 1 set
00 00 02 01  : HRS data ready, prior capture
00 20 00 00  : wake event, Byte1 bit 5 set
```

Known event-bus values:
```text
9010 : general device event from IRQ 25 capture
8012 : PAR2822 measurement-cycle trigger
9005 : sensor data available
1005 : sensor data delivered to framework
```

Known live sensor notification:
```text
spo2_notify: heart_value:0 spo2_value:98
```

Interpretation:
The interrupt-status map proves that the nRF/PAR2822 plane can signal health
and event readiness to the AP. The complete bitfield map remains an open
protocol lead, but this is not a hardware-topology blocker.

################################################################################

# 7.12 — SENSOR OWNERSHIP AND PHYSICAL/LOGICAL CLASSIFICATION

################################################################################

Ownership categories:
NRF_OWNED                      : data/control originates behind nRF control plane
PAR2822_PROXY                  : optical health sensor path represented through nRF/heartrate proxy
MTK_SENSOR_STACK               : AP-side MTK/SCP/Sensor HAL publication path
BSP_GHOST_OR_ABSENT            : BSP name present but hardware not confirmed or known absent
OPEN_PHYSICAL_LEAD             : node/function exists; exact physical IC/net not fully named

Canonical sensor table:
```text
Function / sensor              Evidence path                          Classification
Accelerometer                  SensorService + acc_dir                 MTK_SENSOR_STACK / nRF-adjacent path open
Step detector                  SensorService                           MTK_SENSOR_STACK
Step counter                   SensorService + wearhealthuart client    MTK_SENSOR_STACK / nRF bridge-dependent in stock
Tilt detector                  SensorService + event path               MTK_SENSOR_STACK / nRF event path candidate
Heart rate                     SensorService HEART_RATE + heartrate@33  PAR2822_PROXY / nRF-adjacent
Heart beat                     SensorService HEART_BEAT                 MTK_SENSOR_STACK / health event path
SpO2                           spo2_simu + interrupt value + ODM health PAR2822_PROXY, not standard SensorService type here
Blood pressure                 bprs_simu node                           OPEN / simulation/control path, not standalone proof
Barometer / pressure           SensorService PRESSURE + baro nodes      MTK_SENSOR_STACK with nRF sysfs path idle at capture
Altitude                       altitude_value node                      derived/open, idle at capture
Ambient temperature            SensorService + tempearture_value node   MTK_SENSOR_STACK / nRF sysfs idle at capture
Gyroscope                      firmware strings/BSP names only          BSP_GHOST_OR_ABSENT unless future proof appears
Compass / magnetometer         no current physical proof                 BSP_GHOST_OR_ABSENT unless future proof appears
```

Important rule:
Do not force every published Android sensor into nRF ownership. SensorService
labels them as MTK, and SCP/Sensor HAL participates. The correct model is a
hybrid: nRF/PAR2822 owns wearable health/control paths, while the MTK sensor
stack publishes framework sensors and may route or synthesize some values.

################################################################################

# 7.13 — ANDROID SENSORSERVICE PUBLICATION MAP

################################################################################

Live SensorService hardware-relevant output:
```text
ACCELEROMETER             | MTK | type android.sensor.accelerometer(1)
PRESSURE                  | MTK | type android.sensor.pressure(6)
AMBIENT_TEMPERATURE       | MTK | type android.sensor.ambient_temperature(13)
STEP_DETECTOR             | MTK | type android.sensor.step_detector(18)
STEP_COUNTER              | MTK | type android.sensor.step_counter(19)
HEART_RATE                | MTK | type android.sensor.heart_rate(21)
TILT_DETECTOR             | MTK | type android.sensor.tilt_detector(22)
HEART_BEAT                | MTK | type android.sensor.heart_beat(31)
STEP_DETECTOR_WAKEUP      | MTK | type android.sensor.step_detector(18) wakeUp
```

Observed active clients at capture:
```text
ACCELEROMETER  handle 0x00000001 active
STEP_COUNTER   handle 0x00000013 active
TILT_DETECTOR  handle 0x00000016 active
LIGHT          handle 0x00000005 active by com.wiite.devicedaemon.CommunicationServices
STEP_COUNTER   active by com.wiite.wearhealthuart.service.PedometerService
```

Precision note:
The grep output did not show SpO2 as a standard AOSP SensorService type.
SpO2 remains confirmed by the nRF/PAR2822 path, simulation/control node,
interrupt-status evidence, and ODM health path. It must not be incorrectly
listed as a standard SensorService output unless a later full sensor dump proves
one.

Build rule:
Preserve the MTK sensor HAL and SCP/tinysys dependencies during first AOSP
bring-up. Do not remove nRF bridge components until the replacement bridge
feeds all required SensorService and health-stack paths.

################################################################################

# 7.14 — NRF SENSOR SYSFS IDLE VALUES

################################################################################

Live sysfs value capture:
```text
acc_dir             : Sensor Direction:0x0
baro_enable         : DISABLED
baro_value          : Baro value: 0
altitude_value      : Altitude value: 0
tempearture_value   : Temperature value: 0
delay               : pix_delay:50
config              : start ... 0-0-0-0-0-0-0-0-0-0-0-0-0-0-1-1-0-0-0-0-0-0-0-1-0-0-0-0-0-1-0-0-end ...
```

Interpretation:
These are capture-state idle values. A zero value or DISABLED state in this
capture does not prove absence of barometer, altitude, temperature, or event
function. The nodes exist and must be preserved until runtime replacement tests
prove otherwise.

Config bitmap:
The config string is a 32-position vendor bitmap. In this capture the enabled
positions are represented by `1` at positions 14, 15, 23, and 29 if counting
from zero in the printed sequence. Bit semantics remain open protocol leads.

################################################################################

# 7.15 — PAR2822 / HEARTRATE@33 BOUNDARY

################################################################################

I2C evidence:
Runtime node                   : /sys/bus/i2c/devices/4-0033
OF_NAME                        : heartrate
OF_FULLNAME                    : /i2c4@11011000/heartrate@33
OF_COMPATIBLE_0                : mediatek,heartrate
MODALIAS                       : i2c:heartrate
Driver in uevent               : absent in current capture

Associated health paths:
SensorService                  : HEART_RATE and HEART_BEAT are published
Sysfs simulation/control        : hrs_simu, spo2_simu, bprs_simu
Interrupt-status evidence       : SpO2 ready and HRS ready values known
ODM health processes            : heartrate, bloodoxygen, sleep, wearhealthuart exist in stock runtime

Classification:
4-0033 is a real DT/I2C child node and is the correct place to carry the
heartrate/PAR2822 proxy boundary. It is not yet a fully decoded physical
PAR2822 kernel-driver binding in the live Linux capture. Treat it as a present
sensor/proxy node with unresolved driver/protocol ownership.

Preservation rule:
Keep 4-0033 in device tree and preserve it in the hardware database. Do not
delete it just because no driver is shown in this capture. Do not overclaim a
fully decoded PAR2822 Linux driver until the driver binding, native path, or
bus transactions prove that final layer.

################################################################################

# 7.16 — SIMULATION / DEBUG / TEST NODES

################################################################################

Write-only simulation/debug nodes:
```text
bprs_simu       --w------- root root
hrs_simu        --w------- root root
spo2_simu       --w------- root root
step_simu       --w------- root root
tilt_simu       --w------- root root
event_simu      --w------- root root
```

Interpretation:
These nodes are hardware-control or driver-test surfaces for injecting or
triggering sensor/event behaviors. They are not themselves physical sensors.
They are evidence that the Wiite/nRF driver exposes simulation/test hooks for
health and motion events.

Security/trust-boundary note:
Simulation nodes are high-risk if reachable by untrusted code because they can
alter the apparent sensor/event state. Full exploitability and app permissions
belong to security sections. Section 7 records the hardware-control surface.

################################################################################

# 7.17 — POWER / RAIL BOUNDARY FOR SENSOR PLANE

################################################################################

Known rail relationship:
Rail name                      : VMCH / vhrs class path in MT6357 regulator set
Use                            : HR / wearable sensor rail candidate
Section 6 dependency           : MT6357 regulator inventory and power-domain map

Known related control node:
/sys/class/misc/wiite_corp_ctrl/vdd18_con
Mode                           : -rw-r--r-- root root
Classification                 : 1.8V / logic-rail control lead, exact circuit open

Power evidence boundary:
This section records nRF/sensor-plane consumers and control leads. Regulator
voltages, PMIC boot state, charger policy, battery profile, and thermal policy
belong to Section 6. Do not duplicate or override Section 6 regulator truth
here.

Preservation rule:
Do not disable VMCH/vhrs-class rails, vdd18_con-related logic, or the
wiite_corp power path during first-stage clean ROM bring-up. Sensor and nRF
bridge function must be validated before rail gating changes.

################################################################################

# 7.18 — SCP BOUNDARY — MEDIA TEK TINYSYS / SENSOR-HUB DOMAIN

################################################################################

SCP runtime nodes:
```text
/proc/scp_dvfs
/sys/devices/platform/10001000.scpsys
/sys/devices/platform/10500000.scp
/sys/devices/platform/mt-scpdvfs
/sys/devices/platform/scp_dvfs
/sys/devices/platform/mt_soc_pcm_dl1_scp_spk
/sys/devices/platform/mt_soc_pcm_voice_scp
```

SCP platform identity:
SCP platform node              : /sys/devices/platform/10500000.scp
Driver                         : /bus/platform/drivers/scp
Devicetree node                : /firmware/devicetree/base/scp@10500000
SCP DVFS node                  : /proc/scp_dvfs
SCP DVFS state                 : ON
Current SCP frequency          : 250 MHz
Expected SCP frequency         : 250 MHz
SCP sleep state                : sleep mode
SCP IRQ                        : IRQ 183 / SCP A IPC2HOST

Feature DVFS table observed:
```text
feature=0 freq=75  enable=0
feature=1 freq=356 enable=0
feature=2 freq=0   enable=0
feature=3 freq=47  enable=0
feature=4 freq=26  enable=0
feature=5 freq=0   enable=1
feature=6 freq=200 enable=0
feature=7 freq=0   enable=0
```

Sensor DVFS entries observed:
```text
sensor id=0  freq=2 enable=0
sensor id=1  freq=2 enable=0
sensor id=2  freq=2 enable=0
sensor id=3  freq=2 enable=0
sensor id=4  freq=2 enable=0
sensor id=5  freq=2 enable=0
sensor id=7  freq=2 enable=0
sensor id=16 freq=2 enable=0
sensor id=17 freq=2 enable=0
sensor id=18 freq=2 enable=0
sensor id=23 freq=2 enable=0
sensor id=26 freq=2 enable=0
sensor id=28 freq=2 enable=0
sensor id=29 freq=2 enable=0
sensor id=35 freq=3 enable=0
sensor id=36 freq=3 enable=0
sensor id=41 freq=2 enable=0
sensor id=43 freq=3 enable=0
```

Boundary rule:
SCP is an AP-side MediaTek firmware execution domain. It is not the nRF52832.
It participates in MTK sensor, DVFS, audio IPI, and CHRE/tinysys behavior. It
must be preserved during clean AOSP bring-up and must not be called bloat.

################################################################################

# 7.19 — SSPM BOUNDARY — MEDIA TEK POWER / MAILBOX DOMAIN

################################################################################

SSPM runtime node:
Path                           : /sys/devices/platform/10440000.sspm
Driver                         : /bus/platform/drivers/sspm
Devicetree node                : /firmware/devicetree/base/sspm@10440000
SELinux object label           : u:object_r:sysfs:s0

SSPM files captured:
```text
/sys/devices/platform/10440000.sspm/power/runtime_suspended_time
/sys/devices/platform/10440000.sspm/power/autosuspend_delay_ms
/sys/devices/platform/10440000.sspm/power/runtime_active_time
/sys/devices/platform/10440000.sspm/power/control
/sys/devices/platform/10440000.sspm/power/runtime_status
/sys/devices/platform/10440000.sspm/driver_override
/sys/devices/platform/10440000.sspm/modalias
/sys/devices/platform/10440000.sspm/uevent
```

SSPM interrupt map:
```text
IRQ 206 : MT_SYSIRQ 205 Level SSPM_MBOX
IRQ 207 : MT_SYSIRQ 206 Level SSPM_MBOX
IRQ 208 : MT_SYSIRQ 207 Level SSPM_MBOX
IRQ 209 : MT_SYSIRQ 208 Level SSPM_MBOX
```

Current live counts:
```text
206: 202 85 102 88 103 82 71 66
207: 165737 47073 32463 111773 30582 26343 21832 15497
208: 62165 22449 18259 23567 96195 66958 48391 38340
209: 0 0 0 0 0 0 0 0
```

Boundary rule:
SSPM is not nRF. SSPM is an MTK power/mailbox firmware domain that participates
in system power, DVFS, and mailbox signaling. It is adjacent to Section 7
because SensorService/SCP/power behavior depend on it, but it is not the
wearable coprocessor.

################################################################################

# 7.20 — STOCK RUNTIME SENSOR-PLANE OWNER MAP

################################################################################

Hardware owner roles:
```text
wiite_corp kernel driver             : owns I2C4 0x50 primary nRF sysfs path
wiite_con driver/control surface     : owns secondary pin/IRQ control path
com.wiite.devicedaemon:communication : owns /dev/ttyS1 in stock runtime
MTK sensors HAL                      : publishes Android SensorService outputs
SCP                                  : AP-side sensor/tinysys support domain
SSPM                                 : AP-side power/mailbox support domain
```

Strict scope:
Only the `owns /dev/ttyS1` fact belongs here for DeviceDaemon. APK permission
sets, package signatures, and removal policy belong to software/security docs.

Hardware implication:
A clean replacement must own both the I2C/sysfs control path and the UART path,
while preserving SensorService outputs and SCP/SSPM participation.

################################################################################

# 7.21 — DEVICE TREE / KERNEL PRESERVATION CONTRACT

################################################################################

Must preserve initially:
```text
i2c4@11011000
  ccu_sensor_i2c_4_hw@11
  heartrate@33
  wiite_cp@50

serial@11003000
  ttyS1 / UART1 / IRQ 219

WIITE_CORP0 EINT
  IRQ 27

WIITE_CORP1 EINT
  IRQ 25

wiite_corp misc device
  /sys/class/misc/wiite_corp_ctrl

wiite_con misc device
  /sys/class/misc/wiite_con_ctrl

10500000.scp
scp_dvfs
10001000.scpsys
10440000.sspm
SSPM_MBOX IRQ 206–209
SCP A IPC2HOST IRQ 183
```

Must not do in first-stage AOSP:
```text
Do not delete 4-0050 wiite_cp.
Do not delete 4-0033 heartrate.
Do not delete 4-0011 ccu_sensor_i2c_4_hw.
Do not disable ttyS1.
Do not remove WIITE_CORP0 or WIITE_CORP1 IRQ lines.
Do not remove 187_187.bin.
Do not disable SCP or SSPM firmware domains.
Do not remove Sensor HAL dependencies before replacement validation.
Do not write upgrade_op, rst_op, command, user_command, or simulation nodes casually.
```

AOSP replacement target:
Replace stock DeviceDaemon with a constrained, auditable hardware bridge that
owns ttyS1 and exposes only required sensor/health/time/event behavior. Preserve
kernel nodes until the replacement proves parity.

################################################################################

# 7.22 — CLEAN ROM REPLACEMENT CONTRACT — WIITE_BRIDGE_DAEMON

################################################################################

Replacement daemon objective:
The clean ROM should replace the stock ODM userspace bridge with a minimal,
auditable service that preserves required hardware behavior and removes
unnecessary privileged APK surfaces.

Required responsibilities:
```text
1. Own /dev/ttyS1 after boot.
2. Open ttyS1 with raw binary settings.
3. Coordinate with wiite_corp_ctrl command/config/status nodes.
4. Preserve nRF RTC sync when required.
5. Preserve step/tilt/heart/SpO2/pressure/temperature delivery paths.
6. Preserve wake/no-wake event handling semantics.
7. Avoid normal-operation writes to upgrade_op.
8. Avoid exposing debug simulation nodes to untrusted apps.
9. Restore SensorService publication parity.
10. Start early enough for sensor HAL and health services to avoid null paths.
```

Initial init class recommendation:
Service class                  : early_hal or equivalent early hardware class
SELinux domain                 : dedicated wiite_bridge_t, not generic system_app
Device access                  : explicit ttyS1 + nRF sysfs allowlist
Normal upgrade behavior         : disabled / maintenance-only

Validation pass conditions:
```text
/dev/ttyS1 owner is wiite_bridge_daemon, not stock DeviceDaemon.
SensorService publishes the same required sensors.
STEP_COUNTER and TILT_DETECTOR function.
HEART_RATE/HEART_BEAT publication remains stable.
SpO2 path remains accessible through replacement health bridge.
No crash loop in sensors@2.0-service-mediatek.
No continuous denied writes to nRF sysfs.
No accidental writes to upgrade_op.
```

################################################################################

# 7.23 — HARDWARE TRUST BOUNDARY / SECURITY NOTE

################################################################################

Hardware trust boundary:
The nRF52832 control plane sits below normal user-facing Android controls. It
can affect sensor readings, health events, wake events, UART data, device-find
behavior, time sync, firmware update mode, and possibly BLE/remote-control
behavior depending on command path.

Confirmed exposed hardware-control nodes:
```text
command
config
rst_op
upgrade_op
uart_op
user_command
bprs_simu
hrs_simu
spo2_simu
step_simu
tilt_simu
event_simu
/dev/ttyS1
```

Security-scope precision:
This section does not claim remote exfiltration or malicious server control.
It records the hardware exposure: a privileged stock control plane exists and
is weakly protected in stock runtime. Remote-control/exfiltration conclusions
require network, command-source, or server evidence and belong to the security
assessment.

Build rule:
Treat upgrade_op, rst_op, command, user_command, uart_op, and simulation nodes
as security-critical hardware controls. Relabel and restrict them in clean ROM.

################################################################################

# 7.24 — NEGATIVE / NONCONFIRMED / OPEN HARDWARE LEADS

################################################################################

Not closed as physical standalone hardware:
```text
Gyroscope             : firmware/BSP references only; no confirmed physical gyro
Compass / magnetometer: no confirmed physical component in this section
Blood pressure sensor : bprs_simu exists; no standalone physical BP sensor proven
SpO2 SensorService    : not shown as standard Android SensorService type here
```

Open leads retained:
```text
PAR2822 exact Linux/protocol ownership
4-0033 driver-binding semantics
Full TWIS register map
Full UART command map
Full BLE command runtime behavior
wiite_con_ctrl pin 2 final electrical net name
vdd18_con exact circuit function
Exact mapping of each 32-position config bitmap bit
```

Not blockers:
These open leads do not block Section 7 closure because the hardware topology,
firmware artifact, buses, IRQs, UART, sysfs control plane, SensorService
publication, and SCP/SSPM boundaries are all recorded.

################################################################################

# 7.25 — PROTOCOL SURFACE SUMMARY — CARRIED AS OPEN-LEAD CONTEXT

################################################################################

TWIS / sysfs command categories known from firmware strings:
```text
HEART RATE ON / OFF
BLOOD OXYGEN ON / OFF
STEP_COUNT ON / OFF
TILTE ON / OFF
HRV ON / OFF
NOTIFY ON / OFF
UART ON / OFF
CMD_NEW_OTA
CMD_RECOVER OK
CMD_SYS_RESET OK
RESTORE_FACTORY
RESET_TWIM
Send wake interrupt
Send unwake interrupt
USER-CMD-TEST OK
```

UART frame structure known from firmware analysis:
```text
[00] [FF] [SEQ] [LEN_MSB] [LEN_LSB] [COMMAND] [DATA...] [CRC16_MSB] [CRC16_LSB]
```

Known UART command byte labels:
```text
0x11 : CMD_ANDROID_INFO
0x22 : CMD_SYNC_PHONE_BOOK
0x23 : CMD_TODAY_24H_STEP
0xFE : CMD_LOG_INFO
0xFF : CMD_TEST_DATA
```

Known additional UART command branches:
```text
0x1D, 0x1E, 0x1F, 0x36, 0x39, 0x50, 0xF0
```

Known secondary UART event IDs:
```text
0x26, 0x3E, 0x50, 0x51
```

Protocol boundary:
These values are carried because they are required for replacement planning.
They do not convert Section 7 into a full firmware RE document. Complete byte
semantics remain open leads.

################################################################################

# 7.26 — READ-ONLY VALIDATION COMMAND SET

################################################################################

nRF identity:
```bash
adb shell su -c 'for f in chip_id version btMAC nrf_sys_mode; do p="/sys/class/misc/wiite_corp_ctrl/$f"; [ -f "$p" ] && echo "$f=$(cat "$p" 2>/dev/null)"; done'
```

wiite_corp_ctrl map:
```bash
adb shell su -c 'ls -laZ /sys/class/misc/wiite_corp_ctrl; for f in /sys/class/misc/wiite_corp_ctrl/*; do echo "### $f"; ls -laZ "$f"; [ -r "$f" ] && timeout 1 cat "$f" 2>/dev/null; done'
```

wiite_con_ctrl map:
```bash
adb shell 'su 0 sh -c '\''ls -laZ /sys/class/misc/wiite_con_ctrl; readlink -f /sys/class/misc/wiite_con_ctrl; cat /sys/class/misc/wiite_con_ctrl/uevent; ls -laZ /sys/class/misc/wiite_con_ctrl/*'\'''
```

I2C4 map:
```bash
adb shell 'su 0 sh -c '\''for d in /sys/bus/i2c/devices/4-*; do echo "### $d"; cat "$d/name" 2>/dev/null; cat "$d/uevent" 2>/dev/null; [ -e "$d/driver" ] && readlink -f "$d/driver"; done'\'''
```

UART1 proof:
```bash
adb shell 'su 0 sh -c '\''id; ls -laZ /dev/ttyS1; readlink -f /sys/class/tty/ttyS1/device; cat /proc/tty/driver/serial | sed -n "1,8p"'\'''
```

TTY owner proof:
```bash
adb shell 'su 0 sh -c '\''for name in com.wiite.devicedaemon:communication android.hardware.sensors@2.0-service-mediatek; do pid=$(pidof "$name" 2>/dev/null); [ -n "$pid" ] || continue; echo "### $name PID=$pid"; for fd in /proc/$pid/fd/*; do t=$(readlink "$fd" 2>/dev/null); case "$t" in *ttyS1*|*11003000.serial*) echo "$fd -> $t";; esac; done; done'\'''
```

IRQ proof:
```bash
adb shell 'su 0 sh -c '\''cat /proc/interrupts | grep -Ei "WIITE_CORP|SCP A IPC2HOST|SSPM_MBOX|BTIF_WAKEUP"'\'''
```

SensorService proof:
```bash
adb shell 'su 0 sh -c '\''dumpsys sensorservice | grep -Ei "heart|spo2|oxygen|step|baro|pressure|temperature|tilt|gesture|accelerometer|wiite|event"'\'''
```

SCP / SSPM proof:
```bash
adb shell 'su 0 sh -c '\''ls -laZ /proc/scp_dvfs /sys/devices/platform/10500000.scp /sys/devices/platform/10440000.sspm 2>/dev/null; cat /proc/interrupts | grep -Ei "scp|sspm|mbox"'\'''
```

Do-not-run-casually list:
```text
Do not write command.
Do not write user_command.
Do not write rst_op.
Do not write upgrade_op.
Do not write uart_op unless deliberately testing UART enable.
Do not write *_simu nodes unless intentionally testing simulated sensor events.
```

################################################################################

# 7.27 — SECTION 7 CLOSURE SUMMARY

################################################################################

Closed hardware facts:
```text
nRF52832 identity              : chip_id 0x70, version 48059, MAC ca:77:87:c7:16:00
nRF firmware artifact          : /system/firmware/187_187.bin, 153,832 bytes, SHA256 fixed
I2C4 topology                  : 4-0011, 4-0033, 4-0050 present
Primary nRF bridge             : 4-0050 wiite_cp / wiite_corp driver-bound
Heartrate/PAR proxy boundary   : 4-0033 heartrate node present, protocol/driver binding open
CCU I2C4 trigger boundary      : 4-0011 ccu_sensor_i2c_4_hw driver-bound
Primary sysfs surface          : wiite_corp_ctrl, major 10 minor 48
Secondary sysfs surface        : wiite_con_ctrl, major 10 minor 50
UART bridge                    : ttyS1 / 0x11003000 / IRQ 219 / ST16650V2 / live rx/tx
UART stock owner               : com.wiite.devicedaemon:communication fd 52 -> /dev/ttyS1
Wiite IRQs                     : IRQ 27 WIITE_CORP0, IRQ 25 WIITE_CORP1
SensorService outputs          : accel, pressure, ambient temp, step, heart rate, tilt, heart beat
SCP boundary                   : 10500000.scp, /proc/scp_dvfs, IRQ 183, 250 MHz, sleep mode
SSPM boundary                  : 10440000.sspm, IRQ 206–209 SSPM_MBOX
```

Open protocol leads:
```text
Full UART command map
Full TWIS command/register map
Full BLE command runtime map
PAR2822 exact frame semantics
wiite_con_ctrl pin 2 final electrical net
vdd18_con exact rail/load target
config bitmap bit meanings
```

Final closure statement:
The C17S contains a confirmed Nordic nRF52832 coprocessor/sensor plane with a
read-only Android-side DFU firmware artifact, a primary I2C4 wiite_cp control
interface at address 0x50, a heartrate/PAR proxy node at address 0x33, a CCU
I2C4 hardware-trigger node at address 0x11, a live UART1 / ttyS1 bridge at
0x11003000 / IRQ 219, dual Wiite interrupt lines WIITE_CORP0 and WIITE_CORP1,
a 32-data-attribute wiite_corp_ctrl sysfs surface, and a secondary
wiite_con_ctrl pin/IRQ control surface. Android SensorService publishes the
wearable-relevant sensor outputs through the MTK sensor stack, while SCP and
SSPM are confirmed as separate MediaTek firmware domains that must be preserved
but not conflated with the nRF52832.

Section 7 is closed for hardware digital-twin purposes. Remaining unresolved
items are firmware/protocol/electrical-net open leads, not blockers to the
hardware topology, AOSP preservation contract, or security-boundary statement.

################################################################################

# END SECTION 7 — DUAL-CHIP COPROCESSOR & SENSOR PLANE

################################################################################
