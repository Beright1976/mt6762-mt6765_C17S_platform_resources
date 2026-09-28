################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 6 — POWER MANAGEMENT, THERMAL & CHARGING
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 6 — POWER / THERMAL / CHARGING
#
# PURPOSE:
# This section defines the power-management, thermal-control, charging,
# battery, fuel-gauge, regulator, DVFS/OPP, charger-current, and ZRAM power
# policy topology of the C17S platform.
#
# This is the build-facing power contract for the device.
#
# If a future recovery, AOSP ROM, kernel, device tree, charger stack, health
# HAL, thermal HAL, PMIC driver, DVFS policy, ZRAM policy, battery profile,
# or clean hardware-control layer needs to preserve, emulate, replace,
# restrict, or deliberately remove a power-domain behavior, that information
# belongs here.
#
# SCOPE:
# Included:
# - MT6357 main PMIC identity and regulator surface
# - PMIC wrapper / PWRAP / boot-latch / reset-state evidence
# - MT6370 charger / sub-PMU on I2C5 address 0x34
# - MT6370 charger policy, IRQ, regmap, MFD child devices, LED/flash surfaces
# - MT6370 LDO / DSV / BLED / FLED / RGBLED classification
# - External LPDDR4 buck regulator declarations and runtime evidence boundary
# - Fuel gauge / battery profile / real capacity / charge counter
# - /sys/class/power_supply runtime contract
# - USB / AC / charger input-current behavior
# - Full regulator-class inventory, including enabled/disabled runtime state
# - Thermal zones, current temperatures, trip points, and sentinel readings
# - Cooling-device inventory and functional grouping
# - /proc/driver/thermal MTK control-plane surface
# - Encoded /vendor/etc/.tp thermal policy artifacts and loader chain
# - CPU L / LL / CCI DVFS surfaces, OPP tables, voltage state, and discrepancies
# - GPU / MFG OPP power table and thermal-policy bridge
# - DVFSRC / SPM / SSPM / SCP power-policy boundary
# - ZRAM / swap / memory-pressure power policy
# - Power / Health / Thermal / fuelgauged service ownership
# - Init / ueventd / sysfs controls touching power and thermal state
# - IRQ / wake power events cross-reference
# - BSP ghosts, negative power hardware, and unresolved physical leads
#
# Excluded:
# - Full nRF52832 firmware / command protocol.
#   That belongs to the dedicated dual-chip coprocessor / sensor-plane section.
#
# - Full camera optical/electrical proof.
#   Camera rail gating and thermal zones are recorded here; camera sensor truth
#   belongs to the visual / camera / hardware component sections.
#
# - Security-removal decisions.
#   This section records hardware and runtime truth. Replacement/removal belongs
#   to build policy and ODM replacement sections.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : /proc/pl_lk, /proc/bootprof, /proc/interrupts, /proc/cpufreq,
#          /proc/driver/thermal, /proc/swaps, /proc/meminfo
# TIER 3 : /sys/class/power_supply, /sys/class/regulator,
#          /sys/class/thermal, /sys/class/leds, /sys/class/flashlight,
#          /sys/kernel/debug/wakeup_sources, /sys/kernel/debug/rt-regmap
# TIER 4 : /sys/firmware/devicetree/base and DTBO-derived evidence
# TIER 5 : Vendor init RC, thermal policy files, HAL/service inventory
# TIER 6 : Closed C17S hardware-digital-twin sections and DOC00–DOC21 corpus
# FINAL  : Beright / Albert acceptance
#
# RULES:
# Controller presence, devicetree declaration, module presence, runtime sysfs
# node, driver binding, class-device exposure, IRQ registration, wake-source
# registration, regulator enable state, current draw, and physical component
# proof are separate evidence classes.
#
# A declared DT node is not automatically a physically populated component.
# A missing Android child node is not automatically proof of absent hardware.
# A bound driver is not automatically proof of visible physical behavior.
# Dynamic voltage/current/temperature/frequency values are capture-state facts.
# Static identifiers, voltage ranges, policy names, hashes, and topology paths
# may be carried as stable evidence once cross-verified.
#
# No stale values.
# No borrowed MTK reference values promoted to this device.
# No ODM marketing value overrides live fuel-gauge evidence.
# No opaque vendor policy file is edited or treated as plaintext.
# No open physical lead is promoted to confirmed hardware without proof.
################################################################################

################################################################################

# 6.0 — SECTION AUTHORITY / EVIDENCE SOURCES

################################################################################

Section status:
Status                         : CLOSED WITH OPEN LEADS
Closure class                  : Evidence-backed power / thermal / charging map
Open-lead class                : Physical mapping / hidden pre-Linux rails only

Capture basis:
Device                         : LOKMAT APPLLP 5 MAX
Board                          : WP_C17S_PIX_TFT_D4
Kernel                         : Linux 4.9.190+
Android base                   : Android 10 / MTK Q BSP
Platform BSP identity          : mt6765
Physical silicon identity      : MT6762V/WB
Research state                 : rooted live stock device / permissive context

Primary live surfaces used:
/sys/class/power_supply
/sys/class/regulator
/sys/class/thermal
/sys/class/leds
/sys/class/flashlight
/sys/bus/i2c/devices
/sys/bus/platform/devices
/sys/kernel/debug/rt-regmap/mt6370_pmu
/sys/kernel/debug/wakeup_sources
/proc/interrupts
/proc/driver/thermal
/proc/cpufreq
/proc/gpufreq
/proc/swaps
/proc/meminfo
/vendor/etc/.tp
/vendor/etc/init
/system/etc/init

Important evidence-language rule:
This section records direct evidence and evidence boundaries. It intentionally
preserves declared-but-unbound and hidden/pre-Linux cases rather than deleting
or simplifying them.

################################################################################

# 6.1 — CANONICAL POWER TOPOLOGY SUMMARY

################################################################################

Canonical power architecture:
Main PMIC                      : MediaTek MT6357
Sub-PMU / charger              : MediaTek / Richtek MT6370 PMU family
Fuel gauge                     : MT6357 integrated fuel-gauge path
Battery chemistry              : Li-ion
Battery learned full capacity  : 2,946,000 uAh
Charger class                  : MT6370 charger / primary_chg / load switch
USB input path                 : USB power supply, 5,000,000 uV / 500,000 uA live cap
Regulator framework entries    : regulator.0 through regulator.33
Main PMIC regulators           : regulator.1 through regulator.32
Sub-PMU LDO regulator          : regulator.33 / irtx_ldo
Thermal zones                  : thermal_zone0 through thermal_zone17
Cooling devices                : cooling_device0 through cooling_device97
ZRAM swap                      : zram0, 3,317,564 kB
ZRAM compression               : lz4 active
Swappiness                     : 150
Page cluster                   : 0

Power-domain split:
MT6357                         : core rails, RF/WCN rails, SIM rails, USB LDO,
                                 camera rails, vibration LDO, battery gauge,
                                 PMIC die and buck thermal zones
MT6370                         : charger, load switch, BLED, FLED1, FLED2,
                                 LDO irtx_ldo, DSV display-bias declaration,
                                 RGBLED declaration, regmap, nested IRQ source
External buck plane            : LPDDR4 VDD2/VDDQ DT-derived RT5738 class,
                                 hidden/not exposed as Android I2C child here
Thermal control                : MTK phone-class thermal stack, not minimal watch
                                 policy; controls CPU/GPU/camera/modem/backlight/
                                 charging/battery-current/PMIC/reset domains

################################################################################

# 6.2 — MT6357 MAIN PMIC

################################################################################

Main PMIC identity:
PMIC model                     : MT6357
Chip code                      : 0x5721
Observed source                : boot / pl_lk / PMIC register dump
Runtime PMIC platform root     : /sys/devices/platform/mt-pmic
Regulator parent root          : /sys/devices/platform/mt-pmic/regulator
PMIC debugfs root              : /sys/kernel/debug/mtk_pmic

Boot / pl_lk PMIC state:
POWER_HOLD                     : 0x1 active
TOP_RST_STATUS                 : 0x4F  register 0x152
PONSTS                         : 0x1   register 0x0C
POFFSTS                        : 0x80  register 0x0E
PGSTATUS0                      : 0xFFFE register 0x14
PSOCSTATUS                     : 0x0   register 0x16
THERMALSTATUS                  : 0x0   register 0x18
BUCK_OC_SDN_STATUS             : 0x0   register 0x1434
BUCK_OC_SDN_EN                 : 0x31F register 0x1444
STRUP_CON4                     : 0x0   register 0xA1C
TOP_RST_MISC                   : 0x1204 register 0x14C
TOP_CLK_TRIM                   : 0x6CC0 register 0x38E
just_rst                       : 0
EFUSE SW Load                  : none
Battery present                : yes
USB DL without battery         : disabled

Boot voltage anchors:
VPROC boot voltage             : 800,000 uV
VSRAM_PROC boot voltage        : 900,000 uV
VCORE boot voltage             : 800,000 uV
VMODEM boot voltage            : 800,000 uV

Preloader regulator registration:
vs1                            : registered OK
vmodem                         : registered OK
vcore                          : registered OK
vproc                          : registered OK
vpa                            : registered OK
vsram_others                   : registered OK
vsram_proc                     : registered OK
vdram                          : registered OK

Runtime PMIC debug anchors:
/sys/devices/platform/mt-pmic/pmic_access : 0x0
/sys/kernel/debug/mtk_pmic/cmp_pmic_reg   : Compare PMIC registers surface
/sys/kernel/debug/mtk_pmic/dump_pmic_reg  : MT6357 register dump surface

Representative PMIC register confirmations:
R[0x008]                       : 0x5721
R[0x00a]                       : 0x5721
R[0x014]                       : 0xfffe
R[0x018]                       : 0x0
R[0x152]                       : 0x4f
R[0x388]                       : 0x4
R[0x38e]                       : 0x6cc0
R[0x40c]                       : 0x5aa5
R[0x40e]                       : 0x5aa5

################################################################################

# 6.3 — PWRAP / PMIC BUS / BOOT LATCH STATE

################################################################################

PMIC wrapper:
Runtime driver surface         : /sys/bus/platform/drivers/pmic_wrap/pwrap
Driver read string             : no implement
Interrupt line                 : IRQ 7 / MT_SYSIRQ 179 / Level / pmic_wrap

PWRAP calibration / validation:
si_ck_sel                      : 1
si_sample_ctrl                 : 0x20
rdata                          : 0x5AA5 PASS
Strobe calibration             : (40, 5)
Read test                      : PASS
Write test                     : PASS

Last command snapshot:
last cmd0 addr                 : 0xA08
last cmd0 wdata                : 0x0
last cmd1 addr                 : 0x5C2
last cmd1 wdata                : 0x1
last cmd2 addr                 : 0x588
last cmd2 wdata                : 0x4321

PMIC interrupt path:
PMIC interrupt GPIO            : GPIO144
Interrupt type                 : IRQ_TYPE_LEVEL_HIGH
Live interrupt                 : IRQ 163 / mt-eint 144 / Level / pmic-eint
DTBO owner                     : PMIC EINT fragment

################################################################################

# 6.4 — MT6370 CHARGER / SUB-PMU

################################################################################

MT6370 identity:
Device                         : MT6370 charger / sub-PMU / MFD family
I2C adapter                    : i2c-5
I2C runtime child              : /sys/bus/i2c/devices/5-0034
I2C address                    : 0x34
Adapter controller             : 11016000.i2c5
Controller base                : 0x11016000
Bus speed                      : 3.4 MHz high-speed
SCL GPIO                       : GPIO48
SDA GPIO                       : GPIO49
I2C child name                 : subpmic_pmu
I2C modalias                   : i2c:subpmic_pmu
Driver                         : mt6370_pmu
OF_NAME                        : mt6370_pmu_dts
OF_FULLNAME                    : /mt6370_pmu_dts

Live MT6370 platform children:
mt6370_pmu_core                : present / bound
mt6370_pmu_charger             : present / bound
mt6370_pmu_bled                : present / bound
mt6370_pmu_fled.0              : present / bound
mt6370_pmu_fled.1              : present / bound
mt6370_pmu_ldo                 : present / bound
mt6370_pmu_dsv                 : present / MFD child surface
mt6370_pmu_rgbled              : present / declared / unbound
flashlights_mt6370             : present

MT6370 module versions:
mt6370_pmu_bled                : 1.0.0_G
mt6370_pmu_charger             : 1.1.25_MTK
mt6370_pmu_core                : 1.0.0_G
mt6370_pmu_fled                : 1.0.3_MTK
mt6370_pmu_i2c                 : 1.0.2_G
mt6370_pmu_ldo                 : 1.0.1_G

Debug flags:
/sys/module/mt6370_pmu_charger/parameters/dbg_log_en : N
/sys/module/mt6370_pmu_i2c/parameters/dbg_log_en     : N

MT6370 interrupt topology:
Root interrupt GPIO            : GPIO11 / mt6370_pmu_irq
DT root intr_gpio raw cell     : 0x3c / 60
DT intr_gpio_num               : 11
DT mt6370_pmu_eint status      : okay
Legacy/DTBO note               : charger-enable GPIO was previously carried as
                                 GPIO154; live DT cell mt6370,chg_en_gpio=0x3c
                                 is preserved as an open binding conflict.
Live interrupt                 : IRQ 30 / mt-eint 11 / Edge / mt6370_pmu_irq

Nested MT6370 IRQs observed:
IRQ 281                        : mt6370_pmu_irq 93  Edge otp
IRQ 282                        : mt6370_pmu_irq 94  Edge vdda_ovp
IRQ 283                        : mt6370_pmu_irq 95  Edge vdda_uv
IRQ 284                        : mt6370_pmu_irq 6   Edge chg_mivr
IRQ 285                        : mt6370_pmu_irq 32  Edge chg_aiclmeasi
IRQ 286                        : mt6370_pmu_irq 48  Edge attachi
IRQ 287                        : mt6370_pmu_irq 68  Edge ovpctrl_uvp_d_evt
IRQ 288                        : mt6370_pmu_irq 35  Edge chg_wdtmri
IRQ 289                        : mt6370_pmu_irq 15  Edge chg_vbusov
IRQ 290                        : mt6370_pmu_irq 27  Edge chg_tmri
IRQ 291                        : mt6370_pmu_irq 4   Edge chg_treg
IRQ 292                        : mt6370_pmu_irq 75  Edge fled_lvf
IRQ 293                        : mt6370_pmu_irq 78  Edge fled2_short
IRQ 294                        : mt6370_pmu_irq 79  Edge fled1_short
IRQ 295                        : mt6370_pmu_irq 103 Edge ldo_oc
IRQ 304                        : mt6370_pmu_irq 118 Edge bled_ocp

MT6370 regmap:
Debug path                     : /sys/kernel/debug/rt-regmap/mt6370_pmu
Error state                    : No Error
Name                           : mt6370_pmu
Slave address                  : 0x34
Watchdog                       : 0
reg_size                       : 1
size                           : 1
Cache mode                     : Cache Write Through
Block mode                     : IO_PASS_THROUGH
Register dump range            : reg0x00 through reg0xef exposed
Representative registers       : reg0x00=0xf4, reg0x01=0x16, reg0xef=0xff

MT6370 charger DT policy:
Device-tree cell encoding      : big-endian u32
Host od -tu4 decimal output    : byte-swapped; decoded values below are canonical

aicr                           : 500,000 uA input-current limit
cv                             : 4,350,000 uV constant-voltage target
ichg                           : 2,000,000 uA programmed charge-current ceiling
ieoc                           : 50,000 uA end-of-charge current
mivr                           : 4,400,000 uV minimum input-voltage regulation
ircmp_resistor                 : 25,000 raw/unit per MTK binding
ircmp_vclamp                   : 32,000 raw/unit per MTK binding
dc_wdt                         : 4,000,000 raw DT value / unit not locked
safety_timer                   : 12 raw DT value / unit not locked
mt6370,chg_en_gpio             : 0x3c / 60 raw DT cell; binding conflict open

MT6370 charger names:
primary_chg/name               : mt6370_chg
primary_load_switch/name       : mt6370_ls
Charger compatible             : mediatek,mt6370_pmu_charger
Charger interrupt names        : chg_mivr, chg_aiclmeasi, attachi,
                                 ovpctrl_uvp_d_evt, chg_wdtmri, chg_vbusov,
                                 chg_tmri, chg_treg

MT6370 LDO:
Regulator class                : regulator.33
Name                           : irtx_ldo
Configured voltage             : 3,400,000 uV
Minimum voltage                : 1,600,000 uV
Maximum voltage                : 4,000,000 uV
State                          : disabled
Users                          : 0
Classification                 : confirmed MT6370 LDO endpoint; physical IR
                                 transmitter not proven

MT6370 DSV display-bias declaration:
dsv_pos minimum                : 4,000,000 uV
dsv_pos maximum                : 6,000,000 uV
dsv_neg minimum                : 4,000,000 uV
dsv_neg maximum                : 6,000,000 uV
Classification                 : declared MT6370 display-bias rail pair; runtime
                                 regulator-class endpoint not separately exposed

MT6370 BLED:
Driver                         : /sys/bus/platform/drivers/mt6370_pmu_bled
OF_NAME                        : bled
Compatible                     : mediatek,mt6370_pmu_bled
LED class                      : /sys/class/leds/mt6370_pmu_bled
Flashlight class               : /sys/class/flashlight/mt6370_pmu_bled
Brightness                     : 255
Max brightness                 : 255
Flashlight mode                : Off
Torch brightness               : 128
Torch max                      : 255
Strobe brightness              : 255
Strobe max                     : 255
Strobe timeout                 : 208
DT max_bled_brightness         : 512
DT channel enable              : 0x0000000f / channels 0-3 enabled
DT use_pwm                     : boolean present
DT pwm_fsample                 : 2
DT pwm_hys                     : 0
DT pwm_hys_en                  : 1
DT bl_ocp_level                : 2
DT bl_ovp_level                : 3
Interrupt                      : bled_ocp
Classification                 : confirmed bound/live MT6370 LED/backlight/
                                 flashlight-class surface

MT6370 FLED1:
Driver                         : /sys/bus/platform/drivers/mt6370_pmu_fled
OF_NAME                        : mt6370_pmu_fled1
Compatible                     : mediatek,mt6370_pmu_fled1
Runtime class                  : /sys/class/flashlight/mt-flash-led1
Name                           : mt6370-fled
Type                           : LED
Mode                           : Off
Torch brightness               : 0
Torch max                      : 31
Strobe brightness              : 0
Strobe max                     : 256
Strobe timeout                 : 0
DT enabled                     : 1
DT strobe_cur                  : 1,200,000 uA
DT torch_cur                   : 300,000 uA
DT strobe_timeout              : 2,400 raw
Classification                 : confirmed bound/live MT6370 FLED channel

MT6370 FLED2:
Driver                         : /sys/bus/platform/drivers/mt6370_pmu_fled
OF_NAME                        : mt6370_pmu_fled2
Compatible                     : mediatek,mt6370_pmu_fled2
Runtime class                  : /sys/class/flashlight/mt-flash-led2
Name                           : mt6370-fled
Type                           : LED
Mode                           : Off
Torch brightness               : 0
Torch max                      : 31
Strobe brightness              : 0
Strobe max                     : 256
Strobe timeout                 : 0
DT enabled                     : 1
DT strobe_cur                  : 1,000,000 uA
DT torch_cur                   : 200,000 uA
DT strobe_timeout              : 1,200 raw
Classification                 : confirmed bound/live MT6370 FLED channel

MT6370 RGBLED:
Runtime MFD child              : /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_rgbled
OF_NAME                        : rgbled
Compatible                     : mediatek,mt6370_pmu_rgbled
Declared LED names             : mt6370_pmu_led1, mt6370_pmu_led2,
                                 mt6370_pmu_led3, mt6370_pmu_led4
Default triggers               : cc_mode, cc_mode, cc_mode, none
Interrupt names                : isink4_short, isink3_short, isink2_short,
                                 isink1_short, isink4_open, isink3_open,
                                 isink2_open, isink1_open
Driver symlink                 : absent
LED class nodes                : absent for mt6370_pmu_led1-4
Classification                 : DT-declared MFD child, unbound at runtime,
                                 not confirmed physical output

LED class inventory:
lcd-backlight                  : brightness 10, max 255, trigger none
mt6370_pmu_bled                : brightness 255, max 255, trigger none
vibrator                       : brightness 0, max 255, trigger none

Physical LED mapping:
FLED1/FLED2 physical mapping   : open
BLED physical role             : open; likely backlight/auxiliary LED surface but
                                 not reduced to a single physical assumption
Rear HR/SpO2 emitters          : open; not assigned to MT6370 without proof
RGB current sinks              : declared but unbound; not physical truth yet

################################################################################

# 6.5 — EXTERNAL BUCK REGULATORS / LPDDR4 POWER

################################################################################

External buck evidence class:
Classification                 : device-derived / DT-declared / driver-supported
                                 / runtime-child-hidden-or-pre-Linux
Delete status                  : must not delete
Absent status                  : must not mark absent

DT-declared LPDDR4 buck nodes:
/sys/firmware/devicetree/base/i2c3@1100f000/ext_buck_lp4@57
/sys/firmware/devicetree/base/i2c3@1100f000/ext_buck_lp4x@50

Driver/module surfaces:
/sys/bus/i2c/drivers/rt5738    : exists
/sys/module/rt5738_regulator   : exists

I2C3 runtime state:
I2C adapter                    : /sys/bus/i2c/devices/i2c-3
Controller                     : 1100f000.i2c3
Runtime child 3-0057           : not exposed in this live Android capture
Runtime child 3-0050           : not exposed in this live Android capture
Other 3-* children             : none exposed in this live Android capture

DT-derived rail roles:
ext_buck_lp4@57                : logical rail ext_buck_vdd2
Function                       : LPDDR4 VDD2 rail
Voltage range                  : 300,000 uV to 1,300,000 uV
vsel_pin                       : 0

ext_buck_lp4x@50               : logical rail ext_buck_vddq
Function                       : LPDDR4 VDDQ rail
Voltage range                  : 300,000 uV to 1,300,000 uV
vsel_pin                       : 1

Interpretation boundary:
The database lists the external buck regulators because the exact device exposes
them through device-derived DT evidence and RT5738 driver surfaces. Their absence
from Android runtime I2C child enumeration does not prove absence. DRAM power may
be initialized before Linux, hidden behind preloader/LK handoff, not registered
as normal regulator-class devices, or only conditionally enumerated. The evidence
is preserved exactly.

################################################################################

# 6.6 — BATTERY PACK / FUEL GAUGE / CAPACITY PROFILE

################################################################################

Fuel gauge identity:
Gauge path                     : MT6357 integrated fuel gauge / battery framework
Battery power supply           : /sys/class/power_supply/battery
Fuel-gauge service stack       : fuelgauged / fuelgauged_init.rc / fuelgauged_nvram_init.rc
Health HAL tie-in              : android.hardware.health@2.0-service

Battery chemistry and capacity:
Technology                     : Li-ion
Present                        : 1 / true
Health                         : Good
Reported/learned full charge   : 2,946,000 uAh
Charge counter at full capture : 2,946,000 uAh
Capacity percentage            : 100
ODM/overlay mismatch           : older/marketing 1,500 mAh class values are not
                                 canonical over live gauge capacity
Canonical database capacity    : 2,946 mAh learned/full gauge value

Battery dynamic captures:
Capture A:
Status                         : Charging
Cycle count                    : 44
Capacity                       : 100
Current_now                    : 900 uA
Current_avg                    : 0
Voltage_now                    : 4,369,000 uV
Charge_full                    : 2,946,000 uAh
Charge_counter                 : 2,946,000 uAh
Temperature                    : 272 / 27.2 C

Capture B:
Status                         : Charging
Cycle count                    : 64
Capacity                       : 100
Current_now                    : 0
Current_avg                    : 0
Voltage_now                    : 4,376,000 uV
Charge_full                    : 2,946,000 uAh
Charge_counter                 : 2,946,000 uAh
Temperature                    : 301 / 30.1 C

dumpsys battery snapshot:
AC powered                     : false
USB powered                    : true
Wireless powered               : false
Max charging current           : 500,000 uA
Max charging voltage           : 5,000,000 uV
Charge counter                 : 2,946,000 uAh
Status                         : 2 / charging
Health                         : 2 / good
Present                        : true
Level                          : 100
Scale                          : 100
Voltage                        : 4,376 mV
Temperature                    : 299 / 29.9 C
Technology                     : Li-ion

################################################################################

# 6.7 — POWER_SUPPLY RUNTIME NODES

################################################################################

Power-supply class inventory:
/sys/class/power_supply/ac      -> ../../devices/platform/mt_charger/power_supply/ac
/sys/class/power_supply/battery -> ../../devices/platform/battery/power_supply/battery
/sys/class/power_supply/charger -> ../../devices/platform/mt_charger/power_supply/charger
/sys/class/power_supply/usb     -> ../../devices/platform/mt_charger/power_supply/usb

AC uevent:
POWER_SUPPLY_NAME              : ac
POWER_SUPPLY_ONLINE            : 0

Battery uevent:
POWER_SUPPLY_NAME              : battery
POWER_SUPPLY_STATUS            : Charging
POWER_SUPPLY_HEALTH            : Good
POWER_SUPPLY_PRESENT           : 1
POWER_SUPPLY_TECHNOLOGY        : Li-ion
POWER_SUPPLY_CYCLE_COUNT       : 64
POWER_SUPPLY_CAPACITY          : 100
POWER_SUPPLY_CURRENT_NOW       : 0
POWER_SUPPLY_CURRENT_AVG       : 0
POWER_SUPPLY_VOLTAGE_NOW       : 4,376,000 uV
POWER_SUPPLY_CHARGE_FULL       : 2,946,000 uAh
POWER_SUPPLY_CHARGE_COUNTER    : 2,946,000 uAh
POWER_SUPPLY_TEMP              : 301

Charger uevent:
POWER_SUPPLY_NAME              : charger
POWER_SUPPLY_ONLINE            : 1

USB uevent:
POWER_SUPPLY_NAME              : usb
POWER_SUPPLY_ONLINE            : 1
POWER_SUPPLY_CURRENT_MAX       : 500,000 uA
POWER_SUPPLY_VOLTAGE_MAX       : 5,000,000 uV

################################################################################

# 6.8 — USB / AC / CHARGER INPUT CURRENT PROFILE

################################################################################

Live cable/input state:
AC online                      : 0
USB online                     : 1
Charger online                 : 1
USB voltage max                : 5,000,000 uV
USB current max                : 500,000 uA
Dumpsys max charging voltage   : 5,000,000 uV
Dumpsys max charging current   : 500,000 uA

Charger policy versus live input:
MT6370 DT aicr                 : 500,000 uA
MT6370 DT ichg                 : 2,000,000 uA
MT6370 DT cv                   : 4,350,000 uV
MT6370 DT ieoc                 : 50,000 uA
Thermal abcct current limit    : 2000 raw / mA-class charging limit
Thermal bcct levels            : 550 mA, 350 mA, 150 mA

Interpretation:
The charger policy allows a high charge-current ceiling, but the live USB power
supply capture is capped at 500 mA. Thermal policy can also clamp charge current
through bcct/abcct. The correct database model is charger-capability plus
capture-state input limit, not one flattened current number.

################################################################################

# 6.9 — REGULATOR AND RAIL INVENTORY

################################################################################

Regulator class count:
Total entries                  : 34
Range                          : regulator.0 through regulator.33

Regulator parent split:
regulator.0                    : dummy regulator
regulator.1-regulator.32       : MT6357 main PMIC regulator framework
regulator.33                   : MT6370 sub-PMU LDO / irtx_ldo

Full live regulator table:
regulator.0  regulator-dummy parent=reg-dummy                                  state=dummy    microvolts=-        min=-        max=-        users=0
regulator.1  vs1            parent=MT6357 / mt-pmic                           state=enabled  microvolts=2100000  min=1200000  max=2200000  users=0
regulator.2  vmodem         parent=MT6357 / mt-pmic                           state=enabled  microvolts=650000   min=500000   max=1193750  users=0
regulator.3  vcore          parent=MT6357 / mt-pmic                           state=enabled  microvolts=650000   min=518750   max=1312500  users=0
regulator.4  vproc          parent=MT6357 / mt-pmic                           state=enabled  microvolts=862500   min=518750   max=1312500  users=0
regulator.5  vpa            parent=MT6357 / mt-pmic                           state=disabled microvolts=500000   min=500000   max=3650000  users=0
regulator.6  vfe28          parent=MT6357 / mt-pmic                           state=enabled  microvolts=2800000  min=-        max=-        users=0
regulator.7  vxo22          parent=MT6357 / mt-pmic                           state=enabled  microvolts=2200000  min=2200000  max=2400000  users=0
regulator.8  vrf18          parent=MT6357 / mt-pmic                           state=enabled  microvolts=1800000  min=-        max=-        users=0
regulator.9  vrf12          parent=MT6357 / mt-pmic                           state=enabled  microvolts=1200000  min=-        max=-        users=0
regulator.10 vefuse         parent=MT6357 / mt-pmic                           state=disabled microvolts=1800000  min=1200000  max=3300000  users=0
regulator.11 vcn33_bt       parent=MT6357 / mt-pmic                           state=enabled  microvolts=3300000  min=3300000  max=3500000  users=2
regulator.12 vcn33_wifi     parent=MT6357 / mt-pmic                           state=enabled  microvolts=3300000  min=3300000  max=3500000  users=0
regulator.13 vcn28          parent=MT6357 / mt-pmic                           state=disabled microvolts=2800000  min=-        max=-        users=0
regulator.14 vcn18          parent=MT6357 / mt-pmic                           state=enabled  microvolts=1800000  min=-        max=-        users=1
regulator.15 vcama          parent=MT6357 / mt-pmic                           state=disabled microvolts=2800000  min=2500000  max=2800000  users=0
regulator.16 vcamd          parent=MT6357 / mt-pmic                           state=disabled microvolts=1800000  min=1000000  max=1800000  users=0
regulator.17 vcamio         parent=MT6357 / mt-pmic                           state=disabled microvolts=1800000  min=-        max=-        users=0
regulator.18 vldo28         parent=MT6357 / mt-pmic                           state=disabled microvolts=2800000  min=2800000  max=3000000  users=0
regulator.19 vsram_others   parent=MT6357 / mt-pmic                           state=enabled  microvolts=900000   min=518750   max=1312500  users=0
regulator.20 vsram_proc     parent=MT6357 / mt-pmic                           state=enabled  microvolts=850000   min=518750   max=1312500  users=0
regulator.21 vaux18         parent=MT6357 / mt-pmic                           state=enabled  microvolts=1800000  min=-        max=-        users=0
regulator.22 vaud28         parent=MT6357 / mt-pmic                           state=enabled  microvolts=2800000  min=-        max=-        users=0
regulator.23 vio28          parent=MT6357 / mt-pmic                           state=enabled  microvolts=2800000  min=-        max=-        users=0
regulator.24 vio18          parent=MT6357 / mt-pmic                           state=enabled  microvolts=1800000  min=-        max=-        users=0
regulator.25 vdram          parent=MT6357 / mt-pmic                           state=disabled microvolts=1100000  min=1100000  max=1200000  users=0
regulator.26 vmc            parent=MT6357 / mt-pmic                           state=disabled microvolts=3000000  min=1800000  max=3300000  users=0
regulator.27 vmch           parent=MT6357 / mt-pmic                           state=enabled  microvolts=3300000  min=2900000  max=3300000  users=1
regulator.28 vemc           parent=MT6357 / mt-pmic                           state=enabled  microvolts=3000000  min=2900000  max=3300000  users=1
regulator.29 vsim1          parent=MT6357 / mt-pmic                           state=enabled  microvolts=1800000  min=1700000  max=3100000  users=0
regulator.30 vsim2          parent=MT6357 / mt-pmic                           state=disabled microvolts=1800000  min=1700000  max=3100000  users=0
regulator.31 vibr           parent=MT6357 / mt-pmic                           state=disabled microvolts=2000000  min=1200000  max=3300000  users=0
regulator.32 vusb33         parent=MT6357 / mt-pmic                           state=enabled  microvolts=3000000  min=3000000  max=3100000  users=0
regulator.33 irtx_ldo       parent=MT6370 / I2C5 / 5-0034 / mt6370_pmu_ldo    state=disabled microvolts=3400000  min=1600000  max=4000000  users=0

Enabled rails at capture:
vs1, vmodem, vcore, vproc, vfe28, vxo22, vrf18, vrf12, vcn33_bt,
vcn33_wifi, vcn18, vsram_others, vsram_proc, vaux18, vaud28, vio28,
vio18, vmch, vemc, vsim1, vusb33

Disabled rails at capture:
vefuse, vcn28, vcama, vcamd, vcamio, vldo28, vdram, vmc, vsim2, vibr,
irtx_ldo

Important rail corrections:
vsim2                          : disabled in this live capture; carry as
                                 available/declaration, not default-on waste
Camera rails                   : vcama/vcamd/vcamio/vldo28 present but idle-off;
                                 this is normal rail gating, not absence
vdram regulator.25             : disabled while DRAM is live; LPDDR4 active power
                                 is not represented by this LDO state alone
vmch regulator.27              : enabled / 3.3 V / users=1; carried as critical
                                 sensor/nRF/heart-rate rail lead from earlier docs
irtx_ldo regulator.33          : confirmed endpoint, disabled, open physical lead

################################################################################

# 6.10 — THERMAL ZONE MAP

################################################################################

Thermal class count:
Thermal zones                  : 18
Range                          : thermal_zone0 through thermal_zone17
Thermal policy                 : backward_compatible on exposed zones
Most zone modes                : disabled in this live capture

Thermal zones and current temperatures:
zone0  mtktsbattery            : 31.000 C
zone1  mtktsAP                 : 33.000 C
zone2  mtktsbtsmdpa            : 33.000 C
zone3  mtktscpu                : 34.800 C
zone4  mtktspa                 : 36.000 C
zone5  mtktspmic               : 33.990 C
zone6  mtktswmt                : 29.000 C
zone7  tzimgs0                 : -127.000 C sentinel / invalid idle reading
zone8  tzimgs1                 : -127.000 C sentinel / invalid idle reading
zone9  tzimgs2                 : -127.000 C sentinel / invalid idle reading
zone10 tzimgs3                 : -127.000 C sentinel / invalid idle reading
zone11 tzimgs4                 : -127.000 C sentinel / invalid idle reading
zone12 tzimgs5                 : -127.000 C sentinel / invalid idle reading
zone13 mt6357tsbuck1           : 32.368 C
zone14 mt6357tsbuck2           : 33.977 C
zone15 battery                 : 31.100 C
zone16 mtktscharger            : 32.000 C
zone17 mtktscharger2           : 32.000 C

Thermal zone domain map:
mtktsbattery                   : MTK framework battery thermal path
battery                        : direct battery thermistor / battery framework zone
mtktsAP                        : AP / board ambient thermal path
mtktscpu                       : CPU die thermal path
mtktsbtsmdpa                   : modem / BTS / PA thermal path
mtktspa                        : secondary PA thermal path
mtktspmic                      : MT6357 PMIC die thermal path
mtktswmt                       : WiFi / BT / GNSS combo thermal path
mt6357tsbuck1                  : MT6357 buck thermal sensor 1
mt6357tsbuck2                  : MT6357 buck thermal sensor 2
mtktscharger                   : MT6370 charger thermal path 1
mtktscharger2                  : MT6370 charger thermal path 2
tzimgs0-tzimgs5                : image/camera thermal zones; instantiated but
                                 sentinel current readings at idle

Trip points exposed through sysfs:
mtktsbattery                   : 60,000 active
mtktsAP                        : 100,000 / 90,000 / 53,000 / 47,001 /
                                 47,000 / 45,001 / 45,000 active
mtktsbtsmdpa                   : 120,000 / 110,000 / 100,000 / 68,000 active
mtktscpu                       : 117,000 / 55,000 / 54,000 active
mtktspa                        : 120,000 active
mtktspmic                      : 136,000 active
mtktswmt                       : 120,000 active
tzimgs0-tzimgs5                : 130,000 active each
mt6357tsbuck1                  : 150,000 active
mt6357tsbuck2                  : 150,000 active
mtktscharger                   : 120,000 active
mtktscharger2                  : 120,000 active
battery framework zone         : no trip_point_* files exposed in capture

Sentinel rule:
The tzimgs* -127000 readings are invalid/sentinel current readings. They prove
neither absence nor physical activity of camera sensors. The zones and policy
exist and must be carried.

################################################################################

# 6.11 — THERMAL POLICY / thermal.conf / COOLER TRIP LOGIC

################################################################################

Thermal control root:
/proc/driver/thermal           : present
Ownership pattern              : root:system for most MTK thermal controls
Function class                 : MTK phone-class thermal control plane

Thermal control files present:
clNR_status, clVR_FPS_status, cl_cam_dual_off_setting, clabcct,
clabcct_lcmoff, clatm, clatm_gpu_threshold, clatm_setting, clbcct, clctm,
clfps, clfps_level, clmutt, clmutt_tm_pid, clmutt_tmd_pid, clphpb, clsd_dbt,
clsd_pid, clsd_rst, clwmt_val, clwmt_wfdstat, clwmtx_pid, mdm_mdinfo,
mdm_mdinfoex, mdm_mdinfoex_thre, mdm_sw, mdm_timeout, mdm_value,
mtm_indicator, mtm_monitor, mtm_scen_call, ta_fg_pid, ta_log, tp_pid,
tp_test, tz6357buck1, tz6357buck2, tzbattery, tzbts, tzbts_param, tzbtspa,
tzbtspa_param, tzcharger, tzcharger2, tzcpu, tzcpu_Tj_out_via_HW_pin,
tzcpu_cal, tzcpu_fastpoll, tzcpu_log, tzcpu_read_temperature,
tzcpu_talking_flag, tzimgs0, tzimgs1, tzimgs2, tzimgs3, tzimgs4, tzimgs5,
tzimgs_debug, tzpa, tzpmic, tzpmic_ate, tzpmic_log, tzts1, tzts2, tzts3,
tzts4, tzts5, tztsAll_enable, tzwmt, wifi_in_soc

Cooling-device count:
Total cooling devices          : 98
Range                          : cooling_device0 through cooling_device97
All captured cur_state         : 0
All captured max_state         : 1

Full cooling-device table:
cooling_device0  : mtk-cl-shutdown02        cur_state=0 max_state=1
cooling_device1  : mtk-cl-shutdown01        cur_state=0 max_state=1
cooling_device2  : mtk-cl-shutdown00        cur_state=0 max_state=1
cooling_device3  : mtk-cl-backlight01       cur_state=0 max_state=1
cooling_device4  : mtk-cl-backlight02       cur_state=0 max_state=1
cooling_device5  : mtk-cl-backlight03       cur_state=0 max_state=1
cooling_device6  : mtk-cl-kshutdown07       cur_state=0 max_state=1
cooling_device7  : mtk-cl-kshutdown06       cur_state=0 max_state=1
cooling_device8  : mtk-cl-kshutdown05       cur_state=0 max_state=1
cooling_device9  : mtk-cl-kshutdown04       cur_state=0 max_state=1
cooling_device10 : mtk-cl-kshutdown03       cur_state=0 max_state=1
cooling_device11 : mtk-cl-kshutdown02       cur_state=0 max_state=1
cooling_device12 : mtk-cl-kshutdown01       cur_state=0 max_state=1
cooling_device13 : mtk-cl-kshutdown00       cur_state=0 max_state=1
cooling_device14 : mtk-cl-cam02             cur_state=0 max_state=1
cooling_device15 : mtk-cl-cam01             cur_state=0 max_state=1
cooling_device16 : mtk-cl-cam00             cur_state=0 max_state=1
cooling_device17 : mtk-cl-cam-urgent        cur_state=0 max_state=1
cooling_device18 : mtktsbattery-sysrst      cur_state=0 max_state=1
cooling_device19 : mtktspa-sysrst           cur_state=0 max_state=1
cooling_device20 : mtktspmic-sysrst         cur_state=0 max_state=1
cooling_device21 : mtktswmt-sysrst          cur_state=0 max_state=1
cooling_device22 : mtktswmt-pa1             cur_state=0 max_state=1
cooling_device23 : mtktswmt-pa2             cur_state=0 max_state=1
cooling_device24 : clnothing1               cur_state=0 max_state=1
cooling_device25 : clnothing2               cur_state=0 max_state=1
cooling_device26 : clnothing3               cur_state=0 max_state=1
cooling_device27 : clnothing4               cur_state=0 max_state=1
cooling_device28 : clnothing5               cur_state=0 max_state=1
cooling_device29 : tzimgs0-sysrst           cur_state=0 max_state=1
cooling_device30 : tzimgs1-sysrst           cur_state=0 max_state=1
cooling_device31 : tzimgs2-sysrst           cur_state=0 max_state=1
cooling_device32 : tzimgs3-sysrst           cur_state=0 max_state=1
cooling_device33 : tzimgs4-sysrst           cur_state=0 max_state=1
cooling_device34 : tzimgs5-sysrst           cur_state=0 max_state=1
cooling_device35 : mtk-cl-mutt03            cur_state=0 max_state=1
cooling_device36 : mtk-cl-mutt02            cur_state=0 max_state=1
cooling_device37 : mtk-cl-mutt01            cur_state=0 max_state=1
cooling_device38 : mtk-cl-mutt00            cur_state=0 max_state=1
cooling_device39 : mtk-cl-noIMS             cur_state=0 max_state=1
cooling_device40 : mtk-cl-mdoff             cur_state=0 max_state=1
cooling_device41 : mtk-cl-adp-mutt          cur_state=0 max_state=1
cooling_device42 : mtk-cl-fps03             cur_state=0 max_state=1
cooling_device43 : mtk-cl-fps02             cur_state=0 max_state=1
cooling_device44 : mtk-cl-fps01             cur_state=0 max_state=1
cooling_device45 : mtk-cl-fps00             cur_state=0 max_state=1
cooling_device46 : mtk-cl-adp-fps           cur_state=0 max_state=1
cooling_device47 : mtk-cl-bcct02            cur_state=0 max_state=1
cooling_device48 : mtk-cl-bcct01            cur_state=0 max_state=1
cooling_device49 : mtk-cl-bcct00            cur_state=0 max_state=1
cooling_device50 : abcct                    cur_state=0 max_state=1
cooling_device51 : abcct_lcmoff             cur_state=0 max_state=1
cooling_device52 : cpu_adaptive_0           cur_state=0 max_state=1
cooling_device53 : cpu_adaptive_1           cur_state=0 max_state=1
cooling_device54 : cpu_adaptive_2           cur_state=0 max_state=1
cooling_device55 : cpu00                    cur_state=0 max_state=1
cooling_device56 : cpu01                    cur_state=0 max_state=1
cooling_device57 : cpu02                    cur_state=0 max_state=1
cooling_device58 : cpu03                    cur_state=0 max_state=1
cooling_device59 : cpu04                    cur_state=0 max_state=1
cooling_device60 : cpu05                    cur_state=0 max_state=1
cooling_device61 : cpu06                    cur_state=0 max_state=1
cooling_device62 : cpu07                    cur_state=0 max_state=1
cooling_device63 : cpu08                    cur_state=0 max_state=1
cooling_device64 : cpu09                    cur_state=0 max_state=1
cooling_device65 : cpu10                    cur_state=0 max_state=1
cooling_device66 : cpu11                    cur_state=0 max_state=1
cooling_device67 : cpu12                    cur_state=0 max_state=1
cooling_device68 : cpu13                    cur_state=0 max_state=1
cooling_device69 : cpu14                    cur_state=0 max_state=1
cooling_device70 : cpu15                    cur_state=0 max_state=1
cooling_device71 : cpu16                    cur_state=0 max_state=1
cooling_device72 : cpu17                    cur_state=0 max_state=1
cooling_device73 : cpu18                    cur_state=0 max_state=1
cooling_device74 : cpu19                    cur_state=0 max_state=1
cooling_device75 : cpu20                    cur_state=0 max_state=1
cooling_device76 : cpu21                    cur_state=0 max_state=1
cooling_device77 : cpu22                    cur_state=0 max_state=1
cooling_device78 : cpu23                    cur_state=0 max_state=1
cooling_device79 : cpu24                    cur_state=0 max_state=1
cooling_device80 : cpu25                    cur_state=0 max_state=1
cooling_device81 : cpu26                    cur_state=0 max_state=1
cooling_device82 : cpu27                    cur_state=0 max_state=1
cooling_device83 : cpu28                    cur_state=0 max_state=1
cooling_device84 : cpu29                    cur_state=0 max_state=1
cooling_device85 : cpu30                    cur_state=0 max_state=1
cooling_device86 : cpu31                    cur_state=0 max_state=1
cooling_device87 : cpu32                    cur_state=0 max_state=1
cooling_device88 : cpu33                    cur_state=0 max_state=1
cooling_device89 : mtktscpu-sysrst          cur_state=0 max_state=1
cooling_device90 : mtktsbuck-sysrst         cur_state=0 max_state=1
cooling_device91 : mtktsAP-sysrst           cur_state=0 max_state=1
cooling_device92 : mtkclNR                  cur_state=0 max_state=1
cooling_device93 : mtkclVR_FPS              cur_state=0 max_state=1
cooling_device94 : sysrst.6357buck1         cur_state=0 max_state=1
cooling_device95 : sysrst.6357buck2         cur_state=0 max_state=1
cooling_device96 : mtktscharger-sysrst      cur_state=0 max_state=1
cooling_device97 : mtktscharger2-rst        cur_state=0 max_state=1

Cooling-device functional groups:
Shutdown / reset               : mtk-cl-shutdown*, mtk-cl-kshutdown*,
                                 mtkts*-sysrst, tzimgs*-sysrst,
                                 sysrst.6357buck*, mtktscharger*-rst
Display / backlight            : mtk-cl-backlight01/02/03
Camera                         : mtk-cl-cam00/01/02, mtk-cl-cam-urgent
Modem / telephony              : mtk-cl-mutt*, mtk-cl-noIMS, mtk-cl-mdoff,
                                 mtk-cl-adp-mutt
FPS / frame-rate               : mtk-cl-fps*, mtk-cl-adp-fps, mtkclVR_FPS
Charging / battery current     : mtk-cl-bcct*, abcct, abcct_lcmoff
CPU adaptive                   : cpu_adaptive_0/1/2, cpu00-cpu33
WMT / RF                       : mtktswmt-pa1, mtktswmt-pa2, WMT proc nodes
Placeholder                    : clnothing1-clnothing5

Key /proc thermal policies:

clabcct:
abcct_cur_bat_chr_curr_limit   : 2000
abcct_cur_chr_input_curr_limit : -1
abcct_pep30_cur_input_curr_limit : 0
abcct_target_temp              : 46,000
abcct_kp                       : 1000
abcct_ki                       : 200000
abcct_kd                       : 5
abcct_max_bat_chr_curr_limit   : 2000
abcct_min_bat_chr_curr_limit   : 0
abcct_input_current_limit_on   : 0
abcct_HW_thermal_solution      : 3000

clabcct_lcmoff:
chrlmt_lcmoff_policy_enable    : 1
abcct_lcmoff_cur_bat_chr_curr_limit : 2000
abcct_lcmoff_pep30_cur_input_curr_limit : 5000
abcct_lcmoff_target_temp       : 48,000
abcct_lcmoff_kp                : 1000
abcct_lcmoff_ki                : 200000
abcct_lcmoff_kd                : 5
abcct_lcmoff_max_bat_chr_curr_limit : 2000
abcct_lcmoff_min_bat_chr_curr_limit : 600
abcct_lcmoff_pep30_max_input_curr_limit : 5000
abcct_lcmoff_pep30_min_input_curr_limit : 2000

clbcct:
curr_limit                     : 65535
mtk-cl-bcct00                  : 550 mA, state 0
mtk-cl-bcct01                  : 350 mA, state 0
mtk-cl-bcct02                  : 150 mA, state 0

clatm:
Version                        : 3
GPU threshold                  : H 80 / L 40
cpu_adaptive_00 first_step     : 2000
cpu_adaptive_00 m/M CPU        : 685 / 1275
cpu_adaptive_00 m/M GPU        : 294 / 717
cpu_adaptive_01 first_step     : 1000
cpu_adaptive_01 m/M CPU        : 400 / 1275
cpu_adaptive_01 m/M GPU        : 294 / 717
cpu_adaptive_02 first_step     : 3960
cpu_adaptive_02 m/M CPU        : 600 / 3960
cpu_adaptive_02 m/M GPU        : 800 / 2000

clctm:
ctm                            : 2
Target Tj 0                    : 85,000
Target Tj 2                    : 55,000
Tpcb 1                         : 43,000
Tpcb 2                         : 46,000
Exit Tj 0                      : 75,000
Exit Tj 2                      : 51,000
Enter_a                        : 515000
Enter_b                        : 10000
Exit_a                         : 419000
Exit_b                         : 8000
K_TT                           : 4000
MAX_K_SUM_TT                   : 40000
K_SUM_TT_LOW/HIGH              : 500 / 500
MIN_SUM_TT/MAX_SUM_TT          : -800000 / 800000
MIN_TTJ                        : 65000
CATMP_STEADY_TTJ_DELTA         : 13500

clfps:
FPS levels                     : 4 / 60-60 / 30-30 / 20-20 / 15-15
mtk-cl-fps00-03                : state 0

clmutt:
curr_limit                     : 0
noIMS                          : 0
mdoff                          : 0
amutt_target_temp              : 71,000
mtk-cl-mutt00                  : 4 1 10401, state 0
mtk-cl-mutt01                  : 1 2 20101, state 0
mtk-cl-mutt02                  : 1 60 3c0101, state 0
mtk-cl-mutt03                  : 1 255 ff0101, state 0

Modem thermal:
mdm_mdinfo                     : md1 T2g=-127000 T3g=35000 tx=-50;
                                 md2 T2g=-127000 T3g=-127000 tx=-127
mdm_mdinfoex_thre              : all zero threshold list in capture

PMIC / buck thermal:
tzpmic trip0                  : 136,000 -> mtktspmic-sysrst
tz6357buck1 trip0             : 150,000 -> sysrst.6357buck1
tz6357buck2 trip0             : 150,000 -> sysrst.6357buck2
tzpmic_ate                    : Thermal ate test: FAIL

Battery thermal:
tzbattery trip0               : 60,000 -> mtktsbattery-sysrst
trips 1-9                     : 48,000 / 46,000 / 45,000 / 44,000 /
                                43,000 / 42,000 / 41,000 / 40,000 / 39,000

AP / BTS thermal:
tzbts trips                   : 100,000 / 90,000 / 53,000 / 47,001 /
                                47,000 / 45,001 / 45,000 / 60,000 /
                                55,000 / 50,000
Coolers                       : mtktsAP-sysrst, mtk-cl-kshutdown01,
                                mtk-cl-cam00, abcct_lcmoff,
                                abcct_2nd_lcmoff, abcct, abcct_2nd

PA / modem thermal:
tzbtspa trips                 : 120,000 / 110,000 / 100,000 / 68,000 /
                                80,000 / 70,000 / 65,000 / 60,000 /
                                55,000 / 50,000
Coolers                       : mtk-cl-kshutdown02, mtk-cl-mdoff,
                                mtk-cl-noIMS, mtk-cl-adp-mutt

Charger thermal:
tzcharger                     : trip0 120,000 -> mtktscharger-sysrst,
                                polling delay 2000 ms
tzcharger2                    : trip0 120,000 -> mtktscharger2-rst,
                                polling delay 2000 ms

CPU thermal:
tzcpu trips                   : 117,000 -> mtktscpu-sysrst;
                                55,000 -> cpu_adaptive_0;
                                54,000 -> cpu_adaptive_1
tzcpu interval                : 200 ms
tzcpu_read_temperature        : current temp 34,500
TS_CON0                        : 0x201080
Fastpoll                       : trip 60,000 factor 2
Calibration fields             : captured; efuse words read as 0x0 in this proc
                                 output; preserve without reinterpretation

Image/camera thermal:
tzimgs0-tzimgs5               : Trip_0_temp=130,000 each
Cool_dev0                     : matching tzimgsN-sysrst
Time_ms                       : 1000

Thermal policy files:
/vendor/etc/.tp/thermal.conf          size 3842 sha256 67f2e75f215f08a3a431413f42d3598902c13c3668b3cc95b4f259c38ee4b05b
/vendor/etc/.tp/.thermal_policy_00    size 3842 sha256 67f2e75f215f08a3a431413f42d3598902c13c3668b3cc95b4f259c38ee4b05b
/vendor/etc/.tp/.thermal_policy_03    size 3842 sha256 67f2e75f215f08a3a431413f42d3598902c13c3668b3cc95b4f259c38ee4b05b
/vendor/etc/.tp/.thermal_policy_05    size 3842 sha256 67f2e75f215f08a3a431413f42d3598902c13c3668b3cc95b4f259c38ee4b05b
/vendor/etc/.tp/thermal.off.conf      size 3020 sha256 f767a910d6d163354be7602678ff3b91b57a4941b1b90887e10c722d78a2223a

Policy file classification:
thermal.conf, .thermal_policy_00, .thermal_policy_03, and .thermal_policy_05
are byte-identical. thermal.off.conf is distinct. Direct cat/sed output is
encoded/obfuscated vendor payload text. The files are not treated as corrupted,
not treated as plaintext, and not manually editable. Runtime behavior is
reconstructed through procfs/sysfs thermal state.

Policy loader proof:
/vendor/bin/thermal_manager strings:
loadmtc thermal.conf
/vendor/etc/.tp/thermal.conf

/vendor/bin/thermal strings:
thermal_policy_%02d
/proc/driver/thermal/mdm_mdinfoex_thre
/proc/driver/thermal/mdm_mdinfoex
/proc/driver/thermal/clmutt_tm_pid
/proc/driver/thermal/tp_pid
/proc/driver/thermal/mdm_mdinfo

/system/bin/thermald strings:
/proc/driver/thermal/clwmtx_pid
/proc/driver/thermal/clmutt_tmd_pid
/proc/driver/thermal/clsd_pid

################################################################################

# 6.12 — CPU L / LL / CCI DVFS VOLTAGE TABLES

################################################################################

DVFS proc root:
/proc/cpufreq

DVFS domains:
/proc/cpufreq/MT_CPU_DVFS_L
/proc/cpufreq/MT_CPU_DVFS_LL
/proc/cpufreq/MT_CPU_DVFS_CCI

Each cluster domain exposes:
cpufreq_freq
cpufreq_oppidx
cpufreq_turbo_mode
cpufreq_volt

MT_CPU_DVFS_L latest individual capture:
cpufreq_freq                   : 1,749,000 KHz
cpufreq_oppidx                 : 8
Vproc                          : 662,500 uV
Vsram                          : 850,000 uV

MT_CPU_DVFS_L earlier wildcard capture:
cpufreq_freq                   : 899,500 KHz
cpufreq_oppidx                 : 15
Vproc                          : 675,000 uV
Vsram                          : 850,000 uV

MT_CPU_DVFS_L OPP table:
0  : (1750000, 99375)
1  : (1750000, 99375)
2  : (1750000, 99375)
3  : (1750000, 99375)
4  : (1750000, 99375)
5  : (1750000, 97500)
6  : (1750000, 93750)
7  : (1750000, 90000)
8  : (1750000, 86250)
9  : (1617000, 82500)
10 : (1484000, 78750)
11 : (1351000, 75000)
12 : (1218000, 71250)
13 : (1085000, 66875)
14 : (979000, 64375)
15 : (900000, 61875)

MT_CPU_DVFS_LL latest individual capture:
cpufreq_freq                   : 541,750 KHz
cpufreq_oppidx                 : 12
Vproc                          : 862,500 uV
Vsram                          : 962,500 uV

MT_CPU_DVFS_LL earlier wildcard capture:
cpufreq_freq                   : 500,750 KHz
cpufreq_oppidx                 : 14
Vproc                          : 662,500 uV
Vsram                          : 850,000 uV

MT_CPU_DVFS_LL OPP table:
0  : (1050000, 86875)
1  : (1050000, 86875)
2  : (1050000, 86875)
3  : (1050000, 86875)
4  : (1050000, 86875)
5  : (1050000, 86875)
6  : (1050000, 86875)
7  : (1050000, 86875)
8  : (1050000, 86250)
9  : (948000, 83125)
10 : (846000, 79375)
11 : (745000, 75000)
12 : (643000, 71875)
13 : (542000, 67500)
14 : (501000, 66250)
15 : (400000, 62500)

MT_CPU_DVFS_CCI latest individual capture:
cpufreq_freq                   : 391,875 KHz
cpufreq_oppidx                 : 8
Vproc                          : 662,500 uV
Vsram                          : 850,000 uV

MT_CPU_DVFS_CCI earlier wildcard capture:
cpufreq_freq                   : 338,875 KHz
cpufreq_oppidx                 : 8
Vproc                          : 625,000 uV
Vsram                          : 850,000 uV

MT_CPU_DVFS_CCI OPP table:
0  : (1048000, 111875)
1  : (997000, 106875)
2  : (953000, 102500)
3  : (915000, 98750)
4  : (877000, 95000)
5  : (839000, 91250)
6  : (801000, 87500)
7  : (763000, 83750)
8  : (724000, 80000)
9  : (658000, 76875)
10 : (592000, 73750)
11 : (525000, 70625)
12 : (459000, 67500)
13 : (392000, 64375)
14 : (339000, 61875)
15 : (300000, 60000)

OPP table unit rule:
The proc OPP table second field is preserved exactly as captured. Runtime
cpufreq_volt reports Vproc/Vsram in uV. The table values appear in MTK proc
format and must not be rewritten into a different unit without source-level
binding proof.

CCI discrepancy:
CCI latest frequency 391,875 KHz corresponds closely to OPP 13 frequency
392,000 KHz, while cpufreq_oppidx reports 8. Earlier 338,875 KHz corresponds
closely to OPP 14 frequency 339,000 KHz while cpufreq_oppidx also reported 8.
This discrepancy is recorded exactly. Do not silently correct cpufreq_oppidx.

################################################################################

# 6.13 — GPU / MFG OPP POWER TABLE

################################################################################

GPU identity:
GPU                            : PowerVR GE8320
Architecture                   : Rogue
Internal codename              : MEOW
Driver family evidence         : libGLES_meow.so

GPU OPP table:
Level 0                        : 650 MHz / Vproc 800 mV / Vsram 875 mV / 716 mW
Level 1                        : 500 MHz / Vproc 700 mV / Vsram 875 mV / 419 mW
Level 2                        : 400 MHz / Vproc 650 mV / Vsram 875 mV / 293 mW

Thermal bridge:
/proc/driver/thermal/tzcpu repeats GPU table lines:
g 0 650000 716
g 1 500000 419
g 2 400000 293

PVR runtime status:
Firmware status                : OK in previous PVR status capture
Hardware reset count           : 0 HWR resets in previous status capture
APM event count                : high/normal dynamic counter; not a fault alone

Power interpretation:
The thermal-control stack contains direct GPU power-budget knowledge. GPU OPP
values are therefore both graphics-driver evidence and thermal-policy evidence.

################################################################################

# 6.14 — DVFSRC / SPM / SSPM / SCP POWER POLICY BOUNDARY

################################################################################

DVFS / low-power controller boundary:
DVFSRC register root           : 0x10012000
DVFSP runtime platform         : mediatek,mt6765-dvfsp surface in Section 5
SCPSYS root                    : /sys/devices/platform/10001000.scpsys
SPM IRQ                        : IRQ 179 / MT_SYSIRQ 181 / Level / SPM
SSPM mailbox IRQs              : IRQ 206-209 / MT_SYSIRQ 205-208 / SSPM_MBOX
SCP firmware domain            : power-adjacent tinysys/SCP path, not PMIC owner

Section boundary:
DVFSRC/SPM/SSPM/SCP participate in power policy, sleep/wake timing, mailbox
coordination, and thermal/power budget. MT6357/MT6370 own the electrical rail
and charger endpoints described in this section. Detailed clock/reset/IRQ/wake
routing remains Section 5; detailed SCP/nRF/sensor firmware protocol remains
Section 7.

################################################################################

# 6.15 — ZRAM / SWAP / MEMORY PRESSURE POWER POLICY

################################################################################

Memory class:
Total RAM                      : 6,031,948 kB
Swap device                    : zram0
Swap total                     : 3,317,564 kB
Swap free sample               : 2,836,436 kB
CMA total                      : 0
NUMA topology                  : single node

ZRAM configuration:
Total size                     : 3,317,564 kB
Compression algorithm          : lz4 active
Available algorithms           : lzo, lz4, deflate
Sysfs algorithm string         : lzo [lz4] deflate
Page cluster                   : 0
Swappiness                     : 150

Init source:
init.ago.rc                    : Above-1G Optimization
Writes                         : /sys/block/zram0/comp_algorithm lz4
Writes                         : /proc/sys/vm/page-cluster 0
Activates                      : swapon_all /vendor/etc/fstab.enableswap
Writes                         : /dev/memcg/memory.swappiness 150
Writes                         : /dev/memcg/apps/memory.swappiness 150
Writes                         : /dev/memcg/system/memory.swappiness 150
Writes                         : /proc/sys/vm/swappiness 150

Interpretation:
The device uses a large aggressive ZRAM policy despite 6 GB physical RAM. This
is a runtime memory-pressure and power-latency policy. It must be preserved in
initial clean-ROM bring-up until memory behavior is proven stable.

################################################################################

# 6.16 — POWER HAL / HEALTH HAL / THERMAL HAL / fuelgauged SERVICES

################################################################################

Live thermal/power processes:
PID 534                        : android.hardware.thermal@1.0-service / system
PID 540                        : vendor.mediatek.hardware.mtkpower@1.0-service / system
PID 659                        : thermald / system
PID 701                        : thermal / system
PID 703                        : thermalloadalgod / system

SELinux domains:
android.hardware.thermal@1.0-service : u:r:hal_thermal_default:s0
vendor.mediatek.hardware.mtkpower@1.0-service : u:r:mtk_hal_power:s0
thermald                       : u:r:thermald:s0
thermal                        : u:r:thermal:s0
thermalloadalgod               : u:r:thermalloadalgod:s0

Thermal RC service definitions:
service thermald /system/bin/thermald
    class main
    user system
    group system net_admin

service thermal /vendor/bin/thermal
    class main
    user system
    group system

service thermal_manager /vendor/bin/thermal_manager
    class main
    user root
    group system
    oneshot

service thermalloadalgod /vendor/bin/thermalloadalgod
    class main
    user system
    group system

service vendor.thermal-hal-1-0 /vendor/bin/hw/android.hardware.thermal@1.0-service
    class hal
    user system
    group system

Power/health/thermal binaries and libraries:
/system/bin/thermald
/vendor/bin/thermal
/vendor/bin/thermal_manager
/vendor/bin/thermalloadalgod
/vendor/bin/hw/android.hardware.thermal@1.0-service
/vendor/bin/hw/android.hardware.health@2.0-service
/vendor/bin/hw/vendor.mediatek.hardware.mtkpower@1.0-service
/vendor/lib64/hw/thermal.mt6765.so
/vendor/lib64/hw/android.hardware.thermal@1.0-impl.so
/vendor/lib/hw/thermal.mt6765.so
/vendor/lib/libthermalalgo.so
/vendor/lib64/hw/power.mt6765.so

HAL classification:
Thermal HAL                    : vendor MT6765 thermal implementation
Power HAL                      : vendor MTK power service + power.mt6765.so
Health HAL                     : android.hardware.health@2.0-service tied to
                                 battery/gauge power_supply surfaces
Fuelgauge                      : MT6357 gauge service/init/NVRAM relationship

################################################################################

# 6.17 — INIT / UEVENTD / SYSFS POWER CONTROLS

################################################################################

Thermal init files:
/system/etc/init/init.thermald.rc
/vendor/etc/init/init.thermal.rc
/vendor/etc/init/init.thermal_manager.rc
/vendor/etc/init/init.thermalloadalgod.rc
/vendor/etc/init/android.hardware.thermal@1.0-service.rc

Charging init references:
/system/etc/init/kpoc_charger.rc
/vendor/etc/init/hw/init.mt6765.usb.rc
/vendor/etc/init/hw/init.project.rc
/vendor/etc/init/hw/meta_init.rc
/vendor/etc/init/hw/factory_init.rc
/vendor/etc/init/hw/init.mt6762.rc
/vendor/etc/init/hw/init.mt6765.rc

Backlight sysfs permissions:
/sys/class/leds/lcd-backlight/brightness chmod/chown references exist in
vendor init files.
/sys/class/leds/lcd-backlight/duty and /div are assigned root:radio in MTK init
references.
Backlight trigger nodes for button/keyboard/jogball exist as BSP-style init
references even when matching physical hardware may not exist.

Power-specific init controls:
init.ago.rc                    : zram LZ4 / swap / swappiness / page-cluster
init.project.rc                : spm_loader / /dev/spm permissions; camera AF
                                 nodes; remosaic daemon ghost references
fuelgauged_init.rc             : fuel-gauge service initialization
fuelgauged_nvram_init.rc       : fuel-gauge NVRAM initialization

KPOC / charger-mode behavior:
kpoc_charger                   : /system/bin/kpoc_charger
Class                          : charger
Healthd charger boot signal    : sys.boot_from_charger_mode=1 can stop charger
                                 class and transition toward full boot

################################################################################

# 6.18 — WAKE / IRQ POWER EVENTS CROSS-REFERENCE

################################################################################

Power-related interrupts:
pmic_wrap                      : IRQ 7 / MT_SYSIRQ 179 / Level
mt6370_pmu_irq                 : IRQ 30 / mt-eint 11 / Edge
pmic-eint                      : IRQ 163 / mt-eint 144 / Level
SPM                            : IRQ 179 / MT_SYSIRQ 181 / Level
MUSB                           : IRQ 242 / MT_SYSIRQ 73 / Level
MD_WDT                         : IRQ 199 / MT_SYSIRQ 261 / Edge
SSPM_MBOX                      : IRQ 206-209 / MT_SYSIRQ 205-208 / Level

Power wake-source snapshot:
radio-interface                : event_count 91
charger                        : active/event 9279, wakeup_count 2
spm                            : zero in capture
battery                        : active/event 1994, wakeup_count 2
gauge coulomb wakelock          : active/event 493
battery wakelock               : active/event 1
5-0034                         : zero in capture
ccci_raw_usb                   : zero in capture
dual-role-usb20                : active 29, event 54, expire 29
USB suspend lock               : active/event 27
mt-pmic:mt635x-auxadc           : active/event 289332, wakeup_count 394
pmicThread_lock wakelock        : active/event 1206, wakeup_count 16
mt_charger                     : zero in capture
usb                            : active 428, event 432, wakeup_count 1
ac                             : active 429, event 432, wakeup_count 1
charger power_supply           : active/event 379, wakeup_count 1

Interpretation:
The strongest sampled wake contributors are PMIC AUXADC, charger/power_supply,
battery, USB, and PMIC thread paths. This matches a compact high-density device
where gauge, charger, USB, and PMIC monitoring remain active while idle.

################################################################################

# 6.19 — BSP GHOSTS / NEGATIVE POWER HARDWARE

################################################################################

Declared but not active/bound:
MT6370 RGBLED                  : DT-declared, unbound, no LED-class nodes
mt6370_pmu_led1-4              : declared names, no live LED class endpoints
button-backlight init refs     : BSP-style init references; physical key backlight
                                 not confirmed here
keyboard-backlight refs        : BSP-style init references; physical keyboard not
                                 present on watch form factor
jogball-backlight refs         : BSP-style init references; physical jogball not
                                 present on watch form factor

Open physical leads:
irtx_ldo                       : MT6370 LDO present at 3.4 V config, disabled;
                                 physical IR emitter/transmitter not proven
FLED1/FLED2                    : live driver-bound channels; physical side-flash
                                 mapping not yet pinned
BLED                           : live LED/flashlight class endpoint; exact physical
                                 routing not fully pinned
Rear HR/SpO2 red/green emitters: physically observed at sensor cluster but not
                                 assigned to MT6370/PAR2822/nRF without proof
External LPDDR4 bucks          : device-derived DT + RT5738 surfaces; not exposed
                                 as live Android 3-0050/3-0057 children

Negative hardware rule:
This section only records power/thermal negative evidence. Full absent-hardware
and BSP ghost catalog belongs to Section 12.

################################################################################

# 6.20 — OPEN LEADS / REVERIFICATION QUEUE

################################################################################

Open lead 1 — external LPDDR4 buck binding:
Evidence                       : DT nodes ext_buck_lp4@57 and ext_buck_lp4x@50;
                                 rt5738 driver/module exists; i2c-3 exists
Missing in capture             : live /sys/bus/i2c/devices/3-0057 and 3-0050
Status                         : not absent; hidden/pre-Linux/conditional open
Required future proof           : boot log probe trace, physical board marking,
                                 regulator debug binding, or preloader source

Open lead 2 — MT6370 FLED physical mapping:
Evidence                       : FLED1/FLED2 driver-bound and class-exposed
Missing in capture             : channel-to-physical-LED proof
Required future proof           : controlled low-current torch test with visual
                                 observation and thermal/current monitoring

Open lead 3 — MT6370 RGBLED physical truth:
Evidence                       : DT-declared RGBLED node and four LED names
Missing in capture             : bound driver and LED class endpoints
Status                         : declared/unbound/open
Required future proof           : driver binding, LED class nodes, direct register
                                 behavior, board trace, or physical LED response

Open lead 4 — irtx_ldo physical endpoint:
Evidence                       : MT6370 regulator.33 irtx_ldo, 3.4 V config
Missing in capture             : physical IR hardware behavior
Status                         : open physical lead
Required future proof           : board trace, emitted IR response, consumer link,
                                 or schematic/teardown

Open lead 5 — thermal policy decode:
Evidence                       : hashes, loader path, runtime proc behavior
Missing in capture             : plaintext decoded policy source
Status                         : encoded/opaque vendor artifact; not a blocker
Required future proof           : decode loadmtc format, reverse thermal_manager,
                                 or reconstruct clean equivalent from runtime

Open lead 6 — health optical LEDs:
Evidence                       : physical rear red/green emitters observed; nRF/
                                 Pixart/PAR2822 sensor plane exists elsewhere
Missing in Section 6            : electrical driver assignment
Status                         : belongs to Section 7/8 physical sensor mapping;
                                 do not assign to MT6370 here

################################################################################

# 6.21 — CLOSURE SUMMARY

################################################################################

Section 6 closure verdict:
MT6357 PMIC                    : COMPLETE
PWRAP / PMIC boot state         : COMPLETE
MT6370 charger/sub-PMU          : COMPLETE
MT6370 charger policy           : COMPLETE
MT6370 IRQ / regmap             : COMPLETE
MT6370 BLED/FLED/LDO/DSV/RGBLED : COMPLETE by evidence class
External LPDDR4 buck evidence   : COMPLETE with hidden/pre-Linux open lead
Battery / fuel gauge            : COMPLETE
power_supply runtime            : COMPLETE
USB/AC/charger input profile    : COMPLETE
Regulator inventory             : COMPLETE
Thermal zones                   : COMPLETE
Thermal trip points             : COMPLETE
Cooling devices                 : COMPLETE
Thermal proc controls           : COMPLETE
Thermal policy file identity    : COMPLETE
Thermal services / SELinux       : COMPLETE
CPU L / LL / CCI DVFS            : COMPLETE
GPU OPP / thermal bridge         : COMPLETE
ZRAM / memory-pressure policy    : COMPLETE
Wake / IRQ power cross-reference : COMPLETE
BSP ghosts / open physical leads : COMPLETE as queue

Canonical closure line:
Section 6 — Power Management, Thermal & Charging is CLOSED WITH OPEN LEADS.
MT6357 PMIC, MT6370 charger/sub-PMU, fuel gauge, battery profile, regulator
rails, thermal zones, cooling devices, thermal policy, CPU/GPU DVFS/OPP, charger
current control, and ZRAM memory-pressure power policy are all present and
evidence-backed. Remaining items are physical mapping or opaque-policy decode
leads, not blockers to section closure.

################################################################################
# END SECTION 6 — POWER MANAGEMENT, THERMAL & CHARGING
################################################################################
