################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 12 — NEGATIVE HARDWARE, BSP GHOSTS & OPEN PHYSICAL LEADS
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 12 — NEGATIVE HARDWARE / BSP GHOST LEDGER
# Closure     : CLOSED AS CONSOLIDATED NEGATIVE-HARDWARE REGISTER WITH NAMED OPEN LEADS
#
# PURPOSE:
# This final hardware digital-twin section consolidates all negative hardware,
# BSP ghost, declared-only, superseded, unpopulated, owner-confirmed absent,
# capture-absent, and open physical lead records discovered across Sections 1-11.
#
# The C17S software tree contains broad MediaTek/ODM BSP inheritance. Many
# features, routes, nodes, libraries, fstab lines, VINTF variants, policy labels,
# init references, and framework declarations exist because this build was
# assembled from a reusable phone/PoS/scanner/watch BSP family. This section
# prevents those inherited software surfaces from being promoted into physical
# hardware truth.
#
# This is a hardware digital-twin section. It is not a ROM build recipe, not an
# APK behavior report, not a threat conclusion, and not a teardown substitute.
# It records the boundary between:
# - physically present hardware,
# - physically absent hardware,
# - BSP residue,
# - declared-only software routes,
# - loader-required scaffolds that must be carried even when not physically real,
# - unpopulated board boundaries,
# - unresolved physical leads that require future proof.
#
# If a future clean AOSP ROM, kernel port, device tree, HAL cleanup, SELinux
# policy, recovery build, public hardware database, or security assessment needs
# to decide whether to preserve, remove, stub, emulate, carry, or investigate a
# questionable component, that final decision point belongs here.
#
# SCOPE:
# Included:
# - Confirmed absent external hardware: removable SD slot, HDMI, NFC, IR,
#   UWB, Ethernet, wireless charging, unproven physical 3.5 mm jack, unproven
#   physical earpiece path, and unproven external dock paths
# - Confirmed absent or non-promoted sensors: gyroscope, physical ALS, proximity,
#   standalone blood pressure sensor, standalone compass/magnetometer, and
#   unresolved barometer/ambient-temperature physical ownership
# - BSP ghost drivers and route scaffolds: Mali residue, Goodix GT1151 firmware,
#   rt5509 smart PA, hl7593/fan53526/RT5738 ambiguity, MT6753 triggers,
#   Sony PDAF loader scaffolds, audio-policy ghosts, MATV/FM residue, md2/md3
#   modem status nodes, C2K legacy nodes, and dead mount references
# - Absent boot/storage surfaces: vendor_boot, init_boot, vendor_kernel_boot,
#   dedicated dtb partition, odmdtbo, A/B slots, md1dsp, md1arm7, md3img,
#   persist, secro, inactive metadata p11, protective/hybrid MBR, backup GPT
#   final header state
# - Declared-only or template surfaces: multi-SIM/secure-element VINTF variants,
#   sdcard1/externdevice fstab, audio dock/HDMI/headset routes, feature XML
#   inheritance, fake/simulated sensor nodes, factory/meta fake_encrypt trigger,
#   and current research-capture trust-state caveats
# - Open physical leads retained for future teardown, logic capture, controlled
#   functional test, source audit, or stock-state archival proof
# - Do-not-demote register for real hardware that may look suspicious but must
#   not be removed: Wiite/nRF control plane, ttyS1, Wi-Fi/Bluetooth/GNSS/modem,
#   MT6370 charger/LED/PMU, Hynitron touch, active ST7701S display, PowerVR GPU,
#   md_udc metadata, RPMB, DXCC/TRNG/security_ao/efuse, SIM1 secure element
#
# Excluded:
# - Repeating full positive hardware topology from Sections 1-11. This section
#   references positive truths only when needed to prevent false demotion.
# - APK network or exfiltration behavior. Software behavior belongs to APK and
#   security-analysis documents; this section records only hardware-admission or
#   hardware-control implications.
# - Secret extraction, private identifiers, device-unique DRM/RPMB/key material,
#   subscriber data, or Wi-Fi/Bluetooth/GNSS private values.
# - Speculative board population guesses without live, physical, or owner
#   inspection evidence.
#
# SOURCE AUTHORITY:
# TIER 1 : Closed C17S hardware Sections 1-11
# TIER 2 : Live adb/root captures: getprop, pm list features, service list,
#          lshal, ps -A -Z, /sys, /proc, /dev, mount, fstab, blockdev, sha256sum
# TIER 3 : Owner physical inspection for populated-versus-absent board-edge
#          hardware where software evidence also supports BSP residue
# TIER 4 : Prior canonical platform and component maps used only where they do
#          not conflict with later live hardware sections
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# - Software route exists does not mean physical component exists.
# - Blob exists does not mean physical hardware exists.
# - Feature XML exists does not mean populated hardware exists.
# - Devicetree node exists does not mean board-populated hardware exists.
# - Driver source or library name exists does not mean active binding exists.
# - A HAL dependency may require carrying a ghost/scaffold even when the physical
#   device is absent.
# - Owner physical inspection can close negative board-edge leads when live
#   runtime evidence also lacks feature/device proof.
# - Do not delete every ghost automatically. First classify whether it is:
#   (a) dead residue, (b) safe removable BSP leftover, (c) loader-required
#   scaffold, (d) unpopulated board boundary, (e) open physical lead, or
#   (f) real hardware with an odd software name.
# - Capture-state idle or zero does not prove absent hardware.
# - Absence in a filtered command does not prove global absence unless the owning
#   section already closed the search boundary.
# - Public database copies must redact serials, MACs, subscriber IDs, private
#   network values, GNSS coordinates, DRM identifiers, RPMB/key material, and
#   host-local usernames/paths.
################################################################################

################################################################################
# 12.0 — SECTION CLASSIFICATION LEGEND
################################################################################

OWNER_CONFIRMED_ABSENT       : Owner physical inspection confirms hardware/port not present.
ABSENT_FROM_GPT              : No named partition exists in live GPT/by-name map.
ABSENT_IN_FEATURE_CAPTURE    : pm feature/runtime feature sweep did not declare capability.
ABSENT_IN_ACTIVE_CAPTURE     : Not active/present in the captured runtime state.
ABSENT_PHYSICAL_HARDWARE     : Physical component not populated or not proven after owning-section closure.
BSP_GHOST                    : Inherited BSP residue; software reference exists but hardware is absent/not populated.
BSP_GHOST_CANDIDATE          : Likely inherited BSP residue; requires final proof before removal if dependency exists.
DECLARED_ROUTE_ONLY          : Audio/framework/fstab/feature route exists but physical endpoint not proven.
DECLARED_UNBOUND             : DT/config declaration exists but no live driver/class endpoint was bound.
UNPOPULATED_BOARD_BOUNDARY   : Controller/host/path exists but the board lacks the physical endpoint.
LOADER_REQUIRED_SCAFFOLD     : Physically absent/unconfirmed, but HAL/service loader requires file/name to avoid abort.
SUPERSEDED_DATABASE_INFERENCE: Older database value replaced by stronger later evidence.
FAKE_OR_SIMULATED_SENSOR     : Framework/vendor sensor value is synthetic, simulated, or bridge-derived, not a standalone IC.
OPEN_PHYSICAL_LEAD           : Needs teardown, schematic, controlled test, trace, source, or stock capture.
OPEN_PROTOCOL_LEAD           : Hardware exists but command/register/protocol semantics are unresolved.
NOT_ABSENT                   : Do not demote; positive hardware evidence exists elsewhere.
DO_NOT_REMOVE_FIRST_BOOT     : Preserve during initial clean ROM bring-up even if later removable.
REMOVE_OR_STRIP              : Safe candidate for clean ROM removal when no loader dependency exists.
PUBLIC_REDACT                : Must be redacted/generalized in public copies.
################################################################################

################################################################################
# 12.1 — CANONICAL SECTION 12 SUMMARY
################################################################################

Device family reality:
The C17S is a physically compact watch/phone board using MT6762V/WB silicon
under an MT6765 software BSP identity. The stock software carries broad
phone-class, MTK reference, ODM template, and product-family residues. Section
12 is the final filter that keeps those residues from contaminating the hardware
truth model.

Primary negative hardware conclusions:
```text
Physical removable SD slot        : OWNER_CONFIRMED_ABSENT
HDMI physical interface           : OWNER_CONFIRMED_ABSENT / BSP_GHOST
NFC physical hardware             : OWNER_CONFIRMED_ABSENT / BSP_GHOST
IR / consumer IR physical output  : OWNER_CONFIRMED_ABSENT / BSP_GHOST
Wireless charging                 : OWNER_CONFIRMED_ABSENT / ABSENT_PHYSICAL_HARDWARE
UWB                               : ABSENT_IN_FEATURE_CAPTURE / no live node captured
Ethernet                          : ABSENT_IN_FEATURE_CAPTURE / no eth interface captured
5G/NR                             : ABSENT_IN_FEATURE_CAPTURE
CDMA/C2K physical mode            : BSP legacy surfaces only; no CDMA feature declaration
Dual-SIM runtime                  : not confirmed; captured properties show single-SIM
Physical NFC secure element       : not promoted; Secure Element is SIM1/UICC boundary
Physical ALS/proximity package    : not confirmed; ALS/light is vendor/bridge/fake path
Gyroscope                         : no confirmed physical gyro; BSP/camera scaffold only
Standalone proximity sensor       : no confirmed physical proximity sensor
Standalone blood pressure sensor  : no physical BP sensor proven; bprs_simu exists
Standalone compass/magnetometer   : no current physical proof
Physical earpiece                 : not proven; audio-policy route only / ghost candidate
3.5 mm headphone jack             : not proven; ACCDET/audio-policy route only
Analog/digital dock               : not proven; audio-policy route only
FM RF tuner                       : feature absent; audio/PCM route exists; RF path open
MATV                              : BSP ghost; absent from this device
Mali GPU                          : BSP residue; real GPU is PowerVR GE8320
Goodix GT1151 touch               : BSP residue; real touch is Hynitron hyn_ts I2C0 0x5d
rt5509 smart PA                   : BSP residue; physical IC absent/not used
md1dsp/md1arm7/md3img             : ABSENT_FROM_GPT
persist/secro partitions          : ABSENT_FROM_GPT in captured geometry
metadata p11 as /metadata         : false; inactive ghost; real /metadata is md_udc p10
vendor_boot/init_boot             : absent
vendor_kernel_boot                : absent
A/B slots                         : absent
Dedicated dtb partition           : absent
odmdtbo partition                 : absent
```

Primary open physical leads retained:
```text
FLED1/FLED2 exact physical mapping
BLED exact display/backlight/flash routing
Rear HR/SpO2 red/green emitter electrical ownership
PAR2822 exact Linux/protocol ownership
I2C4 4-0033 heartrate/PAR proxy driver semantics
Blood pressure simulation/control meaning
Wiite control-plane pin2 net name
vdd18_con circuit function
32-position nRF/Wiite config bitmap semantics
Full UART1 command map
Full TWIS/register map
FM RF path, if any
ACCDET physical jack or pogo/accessory path
Back microphone physical/directional mapping
ANC_Record physical route
KEY_CAMERA and KEY_RESTART physical button truth
hyn_ts gesture keycodes
Locked-stock Widevine level
TEE userspace node naming
DXCC consumer path
/dev/sec ioctl semantics
Stock locked/enforcing evidence archive
```

Primary do-not-demote facts:
```text
Wiite/nRF control plane           : real hardware; not BSP ghost
nRF52832 firmware/controller      : real external coprocessor; active through I2C4/UART1/IRQs
UART1 ttyS1                       : real active UART bridge at 0x11003000 IRQ219
I2C4 wiite_cp@50                  : real live driver-bound CP path
I2C5 MT6370                       : real charger/PMU/LED rail controller
PowerVR GE8320                    : real GPU; Mali is residue
ST7701S display                   : active panel truth; NT35695B is superseded inference
Hynitron hyn_ts                   : real touch IC; Goodix GT1151 is residue
MD1 modem                         : real; md1dsp/md1arm7/md3img are absent
Wi-Fi/Bluetooth/GNSS              : real live connsys/WMT child stacks
RPMB /dev/rpmb0 / mmcblk0rpmb     : real security boundary
DXCC/TRNG/EFUSEC/SECURITY_AO      : real DT-declared silicon security blocks
SIM1 Secure Element               : real UICC/OMAPI boundary; not NFC proof
md_udc p10                        : real active metadata mount
metadata p11                      : ghost/inactive trap
```
################################################################################

################################################################################
# 12.2 — MASTER NEGATIVE HARDWARE REGISTER
################################################################################

| ID | Item | Class | Evidence basis | ROM / database action |
|---|---|---|---|---|
| S12-NEG-001 | Removable SD physical slot | OWNER_CONFIRMED_ABSENT / BSP_GHOST | fstab sdcard1 and externdevice host exist, but physical unit has no slot | Do not model as real removable storage; retain as BSP/unpopulated boundary |
| S12-NEG-002 | HDMI | OWNER_CONFIRMED_ABSENT / BSP_GHOST | Property/audio-policy residue only; no feature/node/port | Remove/ignore HDMI hardware model; keep residue note |
| S12-NEG-003 | NFC | OWNER_CONFIRMED_ABSENT / BSP_GHOST | nfc GPIO/property leftovers; no Android NFC feature; no physical NFC hardware | Do not enable NFC; do not confuse SIM1 SE with NFC |
| S12-NEG-004 | IR / Consumer IR | OWNER_CONFIRMED_ABSENT / BSP_GHOST | property residue; no feature/node/physical IR hardware | Do not enable consumer IR; irtx_ldo remains open/lead only |
| S12-NEG-005 | Wireless charging | OWNER_CONFIRMED_ABSENT | no charging coil/receiver hardware in physical unit; wired USB/MT6370 charging is real | Do not model Qi/wireless charging hardware |
| S12-NEG-006 | UWB | ABSENT_IN_FEATURE_CAPTURE | no pm feature and no live class/device node captured | Omit |
| S12-NEG-007 | Ethernet | ABSENT_IN_FEATURE_CAPTURE | no pm feature; no eth interface/device captured | Omit; WearDevicemsg MAC-harvest code does not prove eth hardware |
| S12-NEG-008 | 5G/NR | ABSENT_IN_FEATURE_CAPTURE | no 5G feature; modem is LTE/GSM/IMS-class MD1 | Do not model 5G modem |
| S12-NEG-009 | eSIM | ABSENT_IN_FEATURE_CAPTURE | no eSIM feature; secure element is SIM1/UICC | Do not model eSIM |
| S12-NEG-010 | CDMA/C2K physical stack | BSP_GHOST_CANDIDATE | C2K CCCI nodes exist; no CDMA feature declaration | Do not promote to CDMA hardware |
| S12-NEG-011 | Dual-SIM runtime | ABSENT_IN_ACTIVE_CAPTURE | captured config single-SIM; many VINTF variants exist | Use single-SIM model; keep ODM template variants as residue |
| S12-NEG-012 | Physical earpiece | BSP_GHOST_CANDIDATE | audio route declared; no physical proof | Do not claim until functional/teardown proof |
| S12-NEG-013 | 3.5 mm headphone jack | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | ACCDET/audio routes exist; physical jack not proven | Do not claim physical jack |
| S12-NEG-014 | Analog dock | DECLARED_ROUTE_ONLY / BSP_GHOST | audio policy route only | Omit physical dock |
| S12-NEG-015 | Digital dock | DECLARED_ROUTE_ONLY / BSP_GHOST | audio policy route only | Omit physical dock |
| S12-NEG-016 | HDMI audio | DECLARED_ROUTE_ONLY / BSP_GHOST | audio policy route only; no HDMI hardware | Omit HDMI audio hardware |
| S12-NEG-017 | FM radio RF | OPEN_PHYSICAL_LEAD / DECLARED_ROUTE_ONLY | PCM/audio route exists; no FM feature/module/RF proof | Do not claim FM hardware unless tested |
| S12-NEG-018 | MATV | BSP_GHOST | HAL/config residue; absent from form factor | Remove/ignore |
| S12-NEG-019 | Physical ALS package | ABSENT_PHYSICAL_HARDWARE / FAKE_OR_SIMULATED_SENSOR | LIGHT path active through vendor/bridge behavior; no standalone ALS proof | Do not expose as standalone ALS without proof |
| S12-NEG-020 | Proximity sensor | ABSENT_PHYSICAL_HARDWARE | no confirmed physical proximity component | Omit proximity hardware |
| S12-NEG-021 | Gyroscope | BSP_GHOST_OR_ABSENT | firmware/BSP/camera references only; no physical gyro proof | Do not model gyro; carry camera scaffold only if needed |
| S12-NEG-022 | Compass/magnetometer | BSP_GHOST_OR_ABSENT / OPEN_PHYSICAL_LEAD | no current physical proof; template declarations exist | Do not claim until bus/IC proof |
| S12-NEG-023 | Standalone blood pressure sensor | FAKE_OR_SIMULATED_SENSOR / OPEN_LEAD | bprs_simu node exists; no standalone BP hardware proven | Keep as simulation/control path only |
| S12-NEG-024 | Goodix GT1151 touch | BSP_GHOST | Goodix firmware residue; real touch is Hynitron | Do not enable Goodix as active touch |
| S12-NEG-025 | Mali GPU | BSP_GHOST | Mali residue; real GPU is PowerVR GE8320/Rogue | Do not enable Mali |
| S12-NEG-026 | rt5509 smart PA | BSP_GHOST | DTS/sysfs residue; absent/not used board audio path | Do not add as real PA |
| S12-NEG-027 | hl7593/fan53526 buck refs | BSP_GHOST_CANDIDATE | BSP refs; not active Android child | Do not rely; distinguish from RT5738/ext-buck open lead |
| S12-NEG-028 | mt6370 RGBLED | DECLARED_UNBOUND | DT-declared; no live LED class nodes | Keep open; do not claim physical RGB LED |
| S12-NEG-029 | button/keyboard/jogball backlight | BSP_GHOST | init refs; watch has no keyboard/jogball | Omit physical keyboard/jogball LEDs |
| S12-NEG-030 | vendor_boot | ABSENT_FROM_GPT | no partition | Do not model vendor_boot |
| S12-NEG-031 | init_boot | ABSENT_FROM_GPT | no partition | Do not model init_boot |
| S12-NEG-032 | vendor_kernel_boot | ABSENT_FROM_GPT | no partition | Do not model vendor_kernel_boot |
| S12-NEG-033 | Dedicated dtb partition | ABSENT_FROM_GPT | no partition | DTB is boot-image/header path, not dedicated partition |
| S12-NEG-034 | A/B slots | ABSENT | no slot suffix/A-B layout | A-only model |
| S12-NEG-035 | odmdtbo | ABSENT_FROM_GPT / FSTAB_GHOST | fstab references absent partition | Strip from clean ROM fstab |
| S12-NEG-036 | md1dsp | ABSENT_FROM_GPT / FSTAB_GHOST | no by-name partition | Strip fstab/init references |
| S12-NEG-037 | md1arm7 | ABSENT_FROM_GPT / FSTAB_GHOST | no by-name partition | Strip fstab/init references |
| S12-NEG-038 | md3img | ABSENT_FROM_GPT / FSTAB_GHOST | no by-name partition; only md3 status residues | Strip fstab/init references; do not claim second/third modem |
| S12-NEG-039 | persist partition | ABSENT_FROM_GPT | no persist partition in captured geometry | Do not mount/use |
| S12-NEG-040 | secro partition | ABSENT_FROM_GPT | no secro partition in captured geometry | Do not mount/use |
| S12-NEG-041 | metadata p11 as /metadata | GHOST_PARTITION | p11 exists as metadata name but active /metadata is md_udc p10 | Never mount p11 as /metadata |
| S12-NEG-042 | Protective MBR / Hybrid MBR | ABSENT/ZEROED | geometry section records zeroed/absent MBR structures | Do not assume protective MBR behavior |
| S12-NEG-043 | backup GPT final header | ABSENT/ZEROED | final LBA header absent/zeroed in capture | Preserve exact geometry facts; do not invent normal GPT backup |
| S12-NEG-044 | iAmCdRom.iso | DEAD_CODE | file absent; init mount reference exists | Remove mount command |
| S12-NEG-045 | /FWUpgradeInit.rc | LIVE_ABSENT_REFERENCE | extracted reference; not live implementation | Remove unless later ramdisk proof appears |
| S12-NEG-046 | MT6753 trigger | BSP_GHOST | wrong platform family trigger | Remove from clean init |
| S12-NEG-047 | MNL_AT.bin | FABRICATED/NOT_FOUND_IN_LATEST_CAPTURE | latest checks did not find file | Do not list unless found |
| S12-NEG-048 | Hardware-backed Android Keymaster | ABSENT_FOR_CAPTURED_USERSPACE_PATH | captured path is pure-soft/passthrough | Do not claim hardware-backed KM without new proof |
| S12-NEG-049 | AVB enforcement current capture | ABSENT_CURRENT_CAPTURE | owner research state: orange/unlocked/disabled flags | Keep stock vs research boundary |
| S12-NEG-050 | nRF firmware on active eMMC path | ABSENT_ACTIVE_PATH | operational firmware is in nRF flash; eMMC file is reference/update artifact | Do not model eMMC firmware as active runtime store |
################################################################################

################################################################################
# 12.3 — SENSOR NEGATIVE / FAKE / OPEN REGISTER
################################################################################

12.3.1 — Closed absent / non-promoted sensor facts

| Sensor / path | Current Section 12 classification | Evidence / explanation | Action |
|---|---|---|---|
| Gyroscope | BSP_GHOST_OR_ABSENT | firmware/BSP/camera scaffold references only; no confirmed physical gyro | Do not expose gyro; carry libcamalgo.gyro only if camera HAL depends |
| Physical ALS / ambient light sensor | FAKE_OR_SIMULATED_SENSOR / ABSENT_PHYSICAL_HARDWARE | LIGHT path is vendor/bridge/ODm event behavior; no standalone ALS IC proof | Do not model physical ALS without bus/IC proof |
| Proximity sensor | ABSENT_PHYSICAL_HARDWARE | no confirmed physical proximity component | Do not expose proximity feature |
| Standalone compass/magnetometer | BSP_GHOST_OR_ABSENT / OPEN_PHYSICAL_LEAD | no current physical proof | Do not claim until bus node/IC proof exists |
| Standalone blood pressure sensor | FAKE_OR_SIMULATED_SENSOR / OPEN_LEAD | bprs_simu node exists; not physical sensor proof | Keep as simulation/control surface |
| Standalone SpO2 SensorService type | NOT_STANDARD_SENSOR_IN_CAPTURE | SpO2 not shown as standard AOSP SensorService type in current output | Keep SpO2 under nRF/PAR/ODM health path |
| BMP280 barometer | PRIOR_BSP_SENSOR_NOT_PROVEN | absent from SCP path; pressure path is MTK/nRF-adjacent | Do not model BMP280 unless IC proof appears |
| LIS2HH12 accelerometer | PRIOR_BSP_SENSOR_NOT_PROVEN | absent from SCP path; accelerometer published via MTK stack/bridge | Do not model LIS2HH12 unless IC proof appears |
| AK09918 compass | ABSENT/BSP_GHOST | listed as absent in component map | Do not enable |

12.3.2 — Real sensor/control items that must not be demoted

| Item | Why not absent | Action |
|---|---|---|
| nRF52832 | chip_id/version/sysfs/UART/I2C/IRQ evidence | Preserve; replace daemon only after bridge equivalent exists |
| PAR2822 / heartrate@33 boundary | I2C4 4-0033 node and health stack context exist | Keep as open physical/protocol lead |
| Accelerometer SensorService | published and active | Preserve MTK sensor HAL/SCP/nRF bridge until replacement passes tests |
| Step detector/counter | published and active | Preserve |
| Heart rate / heart beat | published and health stack present | Preserve health path |
| Pressure/barometer/ambient temperature nodes | nodes/publication exist but physical ownership not closed | Preserve until source/teardown/function testing closes |
| Wiite simulation nodes | real sysfs control surfaces, not physical sensors | Restrict/harden; do not promote to standalone ICs |
################################################################################

################################################################################
# 12.4 — EXTERNAL / BOARD-EDGE NEGATIVE REGISTER
################################################################################

| Surface | Section 12 truth | Evidence boundary | Action |
|---|---|---|---|
| Removable SD slot | no physical slot | fstab sdcard1/externdevice are BSP/unpopulated boundary; owner inspection closes physical absence | Remove physical SD model; leave vold policy only if needed |
| USB OTG storage | capable/declaration, no attached media in capture | usbotg:auto and dual-role USB exist; no mounted OTG volume | Do not call absent USB OTG; classify no active media |
| HDMI | absent | no feature/node/port; property/audio residue only | Strip/ignore hardware |
| NFC | absent | no feature/node/physical hardware; DT GPIO residue | Strip/ignore NFC; keep SIM1 SE separate |
| IR / consumer IR | absent | no feature/node/physical hardware; irtx_ldo open only | Do not claim IR |
| Wireless charging | absent | owner physical inspection and no wireless receiver path in compiled hardware sections | Do not model Qi/wireless charger |
| UWB | absent in capture | no feature/node | Omit |
| Ethernet | absent in capture | no eth interface/device; code scanning for eth MAC is software behavior only | Omit |
| Extra USB ACM ports | disabled | vendor.usb.acm_cnt=0 and acm_port0/1 empty | Do not model as active serial ports |
| Type-C-specific naming | residue/open | type_c_port0 fields exist, but live controller is musb-hdrc USB2.0 dual-role path | Treat connector/pinout as teardown lead |
################################################################################

################################################################################
# 12.5 — DISPLAY / AUDIO / INPUT NEGATIVE REGISTER
################################################################################

| Entry | Prior/declared source | Section 12 treatment | Action |
|---|---|---|---|
| NT35695B panel identity | prior database inference | SUPERSEDED_DATABASE_INFERENCE; active truth is ST7701S path | Do not re-promote without DTBO/kernel/physical proof |
| nt35695B_fhd_dsi_cmd_auo_rt5081_drv | older inference | superseded/not active | Leave as historical false lead |
| Physical headphone jack | ACCDET + audio policy | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | Do not claim jack until physical/pogo/accessory proof |
| Earpiece | audio policy | BSP_GHOST_CANDIDATE | Do not claim physical earpiece |
| Built-in back mic | audio policy address back | OPEN_PHYSICAL_LEAD | Needs directional/functional/board proof |
| ANC_Record | PCM route | OPEN_PHYSICAL_LEAD | Needs ANC mic/route proof |
| FM tuner input/output | audio policy + PCM | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | Needs RF proof |
| HDMI audio in/out | audio policy | DECLARED_ROUTE_ONLY / BSP_GHOST | Remove hardware model |
| Analog dock headset | audio policy | DECLARED_ROUTE_ONLY / BSP_GHOST | Remove physical dock model |
| Digital dock headset | audio policy | DECLARED_ROUTE_ONLY / BSP_GHOST | Remove physical dock model |
| KEY_CAMERA | key capability | OPEN_PHYSICAL_LEAD | Needs getevent physical-button test |
| KEY_RESTART | key capability | OPEN_PHYSICAL_LEAD | Needs getevent physical-button test |
| hyn_ts gesture keycodes | touch/input path | OPEN_PHYSICAL_LEAD | Needs controlled gesture capture |
| Device-specific keylayout mtk-kpd/hyn_ts/mtk-tpd | grep search | absent in capture | Do not invent keylayout |
| FLED1/FLED2 physical mapping | MT6370 class nodes | OPEN_PHYSICAL_LEAD | Controlled torch/board trace required |
| BLED physical mapping | MT6370 BLED class | OPEN_PHYSICAL_LEAD | Map to display/backlight/flash physically |
| Keyboard/jogball LEDs | init refs | BSP_GHOST | watch form factor has no keyboard/jogball |
################################################################################

################################################################################
# 12.6 — POWER / CHARGING / THERMAL NEGATIVE REGISTER
################################################################################

| Entry | Classification | Evidence / explanation | Action |
|---|---|---|---|
| Wireless charging | OWNER_CONFIRMED_ABSENT | no physical coil/receiver path in this unit | Do not enable Qi/wireless charge stack |
| MT6370 RGBLED | DECLARED_UNBOUND | DT-declared; no live LED class endpoints | Open lead; do not claim physical RGB LED |
| mt6370_pmu_led1-4 | DECLARED_UNBOUND | declared names only; no live LED class endpoints | Do not map to physical LEDs |
| button-backlight refs | BSP_GHOST | init refs; physical key backlight not confirmed | Do not claim |
| keyboard-backlight refs | BSP_GHOST | no physical keyboard on watch | Remove/ignore |
| jogball-backlight refs | BSP_GHOST | no physical jogball on watch | Remove/ignore |
| irtx_ldo | OPEN_PHYSICAL_LEAD | LDO present/disabled; physical IR emitter not proven | Keep open; do not claim IR |
| External LPDDR4 bucks / RT5738 | OPEN_PHYSICAL_LEAD | DT/source surfaces; not exposed as live Android I2C3 children in capture | Do not mark absent; retain hidden/pre-Linux/conditional lead |
| vsim2 LDO | SINGLE-SIM_UNUSED_CANDIDATE | single-SIM device; verify before disabling | Do not blindly remove before SIM validation |
| tzimgs -127C thermal readings | INVALID/IDLE_SENTINEL | thermal zone sentinel; not real temperature | Do not use as physical sensor value |
################################################################################

################################################################################
# 12.7 — RADIO / BASEBAND / CONNECTIVITY NEGATIVE REGISTER
################################################################################

| Entry | Classification | Evidence / explanation | Action |
|---|---|---|---|
| md1dsp | ABSENT_FROM_GPT | no partition | Strip fstab/init references |
| md1arm7 | ABSENT_FROM_GPT | no partition | Strip fstab/init references |
| md3img | ABSENT_FROM_GPT | no partition | Strip fstab/init references |
| /dev/ccci_md2_sta | BSP_GHOST_CANDIDATE | status node only; one modem reported | Do not model MD2 modem |
| /dev/ccci_md3_sta | BSP_GHOST_CANDIDATE | status node only; no md3img | Do not model MD3 modem |
| C2K PPP/AGPS nodes | LEGACY_BSP_SURFACE | c2k nodes exist; no CDMA feature | Do not claim CDMA hardware |
| NFC feature | ABSENT_IN_FEATURE_CAPTURE | no pm feature | Omit |
| UWB feature | ABSENT_IN_FEATURE_CAPTURE | no pm feature | Omit |
| Ethernet feature | ABSENT_IN_FEATURE_CAPTURE | no pm feature/interface | Omit |
| eSIM feature | ABSENT_IN_FEATURE_CAPTURE | no pm feature | Omit |
| 5G/NR feature | ABSENT_IN_FEATURE_CAPTURE | no pm feature | Omit |
| FM RF | OPEN_PHYSICAL_LEAD | audio routes exist but no RF feature/module proof | Do not claim FM hardware |
| USB tether/RNDIS | ABSENT_IN_ACTIVE_CAPTURE | not active in captured USB config | Do not claim active tethering |
| IMS socket PID owner | OPEN_LEAD | port correlation exists; exact PID owner unresolved | Future ss/lsof/proc-net closure |

False-negative guard:
Bluetooth, Wi-Fi, GNSS, LTE/IMS telephony, WMT/connsys, CCCI MD1, and ccmni0
are confirmed live hardware/software stacks and must not be classified as fake.
################################################################################

################################################################################
# 12.8 — BOOT / STORAGE / PARTITION NEGATIVE REGISTER
################################################################################

| Entry | Classification | Evidence / explanation | Action |
|---|---|---|---|
| vendor_boot | ABSENT_FROM_GPT | no partition | Do not build vendor_boot target |
| init_boot | ABSENT_FROM_GPT | no partition | Do not build init_boot target |
| vendor_kernel_boot | ABSENT_FROM_GPT | no partition | Do not build vendor_kernel_boot target |
| dedicated dtb | ABSENT_FROM_GPT | no partition | Keep DTB within boot-image model |
| odmdtbo | ABSENT_FROM_GPT / FSTAB_GHOST | fstab reference to absent partition | Strip from clean fstab |
| A/B slots | ABSENT | no slot suffix; A-only geometry | Do not configure A/B OTA |
| protective MBR | ABSENT/ZEROED | LBA0 zeroed in captured geometry | Preserve exact geometry |
| hybrid MBR | ABSENT | not present | Do not invent hybrid layout |
| backup GPT final header | ABSENT/ZEROED | final LBA header absent/zeroed | Preserve as device fact |
| md1dsp/md1arm7/md3img | ABSENT_FROM_GPT | absent by-name/GPT | Strip emmc fstab ghosts |
| persist | ABSENT_FROM_GPT | not in live partition map | Do not mount persist |
| secro | ABSENT_FROM_GPT | not in live partition map | Do not mount secro |
| metadata p11 | GHOST_PARTITION | p11 exists but inactive; not mounted as /metadata | Never use for FBE metadata |
| md_udc p10 | NOT_ABSENT | active /metadata mount | Preserve |
| iAmCdRom.iso | DEAD_CODE | file absent | Remove init mount command |
| /FWUpgradeInit.rc | LIVE_ABSENT_REFERENCE | extracted reference only | Remove unless live ramdisk proof appears |
| vendor.crypto.fake_encrypt | FACTORY_META_TRIGGER | factory/meta mode trigger | Do not confuse with normal FBE truth |
################################################################################

################################################################################
# 12.9 — SECURITY SUBSTRATE NEGATIVE / OPEN REGISTER
################################################################################

| Entry | Classification | Evidence / explanation | Action |
|---|---|---|---|
| Hardware-backed Android Keymaster path | NOT_CONFIRMED_TEE_BACKED / SOFTWARE_PATH_CONFIRMED | Keymaster userspace path shows pure-soft/passthrough components | Do not claim hardware-backed KM |
| Gatekeeper hardware path | SOFTWARE_BACKED_SECURITY_PATH | gatekeeper.default.so -> libSoftGatekeeper.so | Do not claim hardware GK |
| Current AVB enforcement | CURRENT_RESEARCH_CAPTURE_NON_ENFORCING | owner research state; orange/unlocked/vbmeta flags | Preserve stock-vs-research distinction |
| Stock Widevine level | OPEN_LEAD | current capture L3; stock may have been L1 before unlock | Need locked-stock evidence |
| TEE userspace node naming | OPEN_LEAD | DT declares Mobicore/TEE, but no /dev/tee*/tz*/trust* node captured | Need source/strace/full scan |
| DXCC consumer path | OPEN_LEAD | DXCC block exists; Android KM path is pure-soft | Need non-secret trace/source |
| /dev/sec ioctl semantics | OPEN_LEAD / DO_NOT_BRUTE_FORCE | /dev/sec exists, semantics not mapped | Use source/symbols, not ioctl fuzzing |
| NFC Secure Element assumption | FALSE_PROMOTION | secure_element SIM1 exists; no NFC feature/hardware | Keep SE as SIM/UICC only |
################################################################################

################################################################################
# 12.10 — BSP GHOSTS THAT MAY STILL BE LOADER-REQUIRED
################################################################################

These entries are dangerous to remove blindly. They may be physically false but
software-loader true. They should be preserved during first clean-ROM boot until
controlled removal testing proves otherwise.

| Item | Physical truth | Loader/runtime truth | First-boot action |
|---|---|---|---|
| BF2553L_mipi_raw / camera_main_two | not physically proven / possible BSP trap | camera HAL may abort-loop if removed | CARRY initially |
| Sony PDAF libraries | sensors are not Sony PDAF | camerahalserver loads them regardless | CARRY initially |
| libcamalgo.gyro.so | no gyro hardware proven | camera HAL may load gyro algorithm scaffold | CARRY if dependency remains |
| audio policy ghost routes | physical endpoints unproven | audio HAL/policy may expect XML schema/routes | Simplify only after audio boot tests |
| Secure Element VINTF variants | only SIM1 active | ODM contains many SS/DSDS/SE/ESE variants | Keep active manifest minimal; archive residue |
| md2/md3 status nodes | no MD2/MD3 modem | legacy status nodes may exist | Do not build extra modem images |
| factory/meta fake_encrypt triggers | not normal user FBE | factory/meta init may reference | Strip only outside factory-mode support |
| Wiite simulation nodes | not standalone physical sensors | may be used by stock health/control plane | Replace/harden, not blind delete |
################################################################################

################################################################################
# 12.11 — OPEN PHYSICAL LEAD MASTER QUEUE
################################################################################

The following are not blockers for database closure. They remain named future
work items.

| ID | Lead | Current class | Required closure proof |
|---|---|---|---|
| S12-OPEN-001 | FLED1 physical mapping | OPEN_PHYSICAL_LEAD | controlled torch test or board trace |
| S12-OPEN-002 | FLED2 physical mapping | OPEN_PHYSICAL_LEAD | controlled torch test or board trace |
| S12-OPEN-003 | BLED physical routing | OPEN_PHYSICAL_LEAD | backlight/flash/board mapping proof |
| S12-OPEN-004 | Rear HR/SpO2 red/green emitter driver assignment | OPEN_PHYSICAL_LEAD | scope/current/board trace to MT6370/PAR/nRF |
| S12-OPEN-005 | PAR2822 exact Linux/protocol ownership | OPEN_PROTOCOL_LEAD | source, logic capture, I2C/UART trace |
| S12-OPEN-006 | I2C4 4-0033 heartrate driver semantics | OPEN_PHYSICAL_LEAD | driver binding/source/probe trace |
| S12-OPEN-007 | TWIS register map | OPEN_PROTOCOL_LEAD | firmware RE + controlled writes on sacrificial setup |
| S12-OPEN-008 | UART1 command map | OPEN_PROTOCOL_LEAD | non-secret UART capture/protocol RE |
| S12-OPEN-009 | BLE command runtime behavior | OPEN_PROTOCOL_LEAD | nRF firmware RE + RF/protocol logs |
| S12-OPEN-010 | wiite_con_ctrl pin 2 net name | OPEN_PHYSICAL_LEAD | board trace/schematic/logic probe |
| S12-OPEN-011 | vdd18_con circuit function | OPEN_PHYSICAL_LEAD | regulator trace/source/board mapping |
| S12-OPEN-012 | nRF 32-position config bitmap | OPEN_PROTOCOL_LEAD | firmware RE + controlled feature toggles |
| S12-OPEN-013 | ACCDET physical jack/pogo/accessory path | OPEN_PHYSICAL_LEAD | teardown/continuity/accessory test |
| S12-OPEN-014 | Back mic route | OPEN_PHYSICAL_LEAD | directional audio test/board proof |
| S12-OPEN-015 | ANC_Record route | OPEN_PHYSICAL_LEAD | audio capture/proc/board proof |
| S12-OPEN-016 | FM RF path | OPEN_PHYSICAL_LEAD | FM feature/module/RF tuning/antenna proof |
| S12-OPEN-017 | KEY_CAMERA physical source | OPEN_PHYSICAL_LEAD | getevent with physical button test |
| S12-OPEN-018 | KEY_RESTART physical source | OPEN_PHYSICAL_LEAD | getevent with physical button test |
| S12-OPEN-019 | hyn_ts gesture keycodes | OPEN_PHYSICAL_LEAD | controlled getevent gestures |
| S12-OPEN-020 | Physical compass/magnetometer | OPEN_PHYSICAL_LEAD | bus node/IC marking/functional proof |
| S12-OPEN-021 | Hidden/pre-Linux external LPDDR4 buck path | OPEN_PHYSICAL_LEAD | bootlog/source/board markings/regulator proof |
| S12-OPEN-022 | Locked-stock Widevine level | OPEN_LEAD | preserved stock getprop/dumpsys/media.drm proof |
| S12-OPEN-023 | TEE userspace node naming | OPEN_LEAD | source/strace/full node scan |
| S12-OPEN-024 | DXCC consumer path | OPEN_LEAD | source or non-secret tracing |
| S12-OPEN-025 | /dev/sec ioctl semantics | OPEN_LEAD | kernel source/symbol map |
| S12-OPEN-026 | Stock locked/enforcing evidence archive | OPEN_LEAD | old stock logs/photos/captures if available |
| S12-OPEN-027 | Type-C/micro-USB physical connector pinout | OPEN_PHYSICAL_LEAD | teardown/continuity; live controller is MUSB USB2 |
| S12-OPEN-028 | IMS socket exact PID ownership | OPEN_LEAD | proc-net inode mapping while service active |
################################################################################

################################################################################
# 12.12 — DO-NOT-DEMOTE REGISTER
################################################################################

These items may look like ODM residue or odd BSP inheritance, but positive
hardware evidence exists. They are not negative hardware.

| Item | Why it must not be demoted | Owning section |
|---|---|---|
| MT6762V/WB silicon | physical/BROM identity | Section 1 |
| MT6765 software BSP identity | software ABI/platform layer | Section 1 |
| Samsung 3V6CMB eMMC | live mmc geometry/CID | Section 2 |
| md_udc p10 | mounted /metadata | Sections 2/4/11 |
| PowerVR GE8320 | real GPU | Sections 1/8 |
| ST7701S display | active LCM truth | Section 8 |
| Hynitron hyn_ts touch | I2C0 0x5d live input path | Sections 4/8 |
| MT6370 PMU/charger | I2C5 0x34 live | Sections 4/6/8/10 |
| MT6357 PMIC/fuel gauge | live power stack | Section 6 |
| nRF52832 | external coprocessor/control plane | Section 7 |
| Wiite CP 4-0050 | live I2C4 driver-bound CP | Sections 7/10 |
| UART1 ttyS1 | active ST16650V2 bridge, IRQ219 | Sections 4/7/10 |
| Wiite misc control nodes | real board-control surfaces | Sections 7/10 |
| MD1 modem | live CCCI/RIL/IMS stack | Section 9 |
| WMT/connsys | parent for Wi-Fi/Bluetooth/GNSS | Section 9 |
| Wi-Fi wlan0 | live connected interface | Section 9 |
| Bluetooth | live enabled HAL/framework | Section 9 |
| GNSS | live fix-capable stack | Section 9 |
| USB MUSB/UDC | live MTP/ADB gadget | Section 10 |
| RPMB | live block and char boundary | Section 11 |
| DXCC/TRNG/EFUSEC/SECURITY_AO | DT-declared security blocks | Section 11 |
| SIM1 Secure Element | live secure_element HAL | Sections 9/11 |
################################################################################

################################################################################
# 12.13 — CLEAN ROM DECISION MATRIX
################################################################################

| Decision | Items | Rule |
|---|---|---|
| Remove/strip safely after dependency check | md1dsp, md1arm7, md3img, odmdtbo, iAmCdRom.iso, /FWUpgradeInit.rc, MT6753 triggers | Absent from GPT/live path; dead references only |
| Do not enable | Mali, Goodix GT1151, NFC, HDMI, IR, wireless charging, UWB, Ethernet, 5G, eSIM, MATV | Physical feature absent or not proven |
| Preserve initially despite ghost/uncertain physical truth | BF2553L_mipi_raw, Sony PDAF libs, libcamalgo.gyro.so, audio policy scaffold | Loader/HAL stability risk |
| Preserve and harden | ttyS1, wiite_corp_ctrl, wiite_con_ctrl, nRF/Wiite bridge, upgrade/rst/uart/control nodes | Real hardware control surfaces |
| Preserve exact corrected path | md_udc p10 for /metadata | p11 metadata is ghost |
| Keep open, not absent | PAR2822 4-0033, rear HR/SpO2 LED routing, FLED/BLED mapping, external LPDDR4 bucks | Evidence exists but ownership/physical mapping incomplete |
| Treat as stock-vs-research caveat | AVB/verity/SELinux/Widevine level | Current capture differs from original stock state |
################################################################################

################################################################################
# 12.14 — PUBLIC REDACTION REGISTER
################################################################################

Do not publish raw/private values when carrying Section 12 evidence forward.

PUBLIC_REDACT categories:
```text
USB serial number
Android serial number
Wi-Fi MAC / BSSID / SSID / IP
Bluetooth MAC / peer addresses
nRF / Wiite BT MAC
SIM ICCID / IMSI / MSISDN / subscriber IDs
Carrier account values and private operator identifiers
GNSS coordinates
DRM/Widevine identifiers or provisioning values
RPMB contents, counters, keying material, replay state
DXCC-derived values or any hardware unique key material
Host computer usernames and local paths
High-entropy RTC/control-plane values unless needed in private debugging
```
################################################################################

################################################################################
# 12.15 — FINAL SECTION VERDICT
################################################################################

Verdict:
Section 12 is the consolidated negative-hardware and BSP-ghost ledger for the
C17S hardware digital twin.

The device is not a generic MT6765 phone board with every inherited BSP feature
populated. It is a compact C17S watch/phone board with a real MT6762V/WB SoC,
PowerVR GPU, ST7701S display, Hynitron touch, MT6357/MT6370 power stack, MD1
phone-class modem, WMT Wi-Fi/Bluetooth/GNSS, USB MUSB/UDC, nRF52832/PAR health
control plane, Wiite board-control surfaces, RPMB/security blocks, and a large
signed-stock ODM software plane.

The following must remain negative unless future proof changes them:
- no removable SD slot,
- no HDMI,
- no NFC,
- no IR/consumer IR,
- no wireless charging,
- no UWB,
- no Ethernet,
- no 5G/NR,
- no eSIM,
- no confirmed physical gyroscope,
- no confirmed physical proximity sensor,
- no confirmed standalone ALS sensor,
- no confirmed standalone blood-pressure sensor,
- no confirmed physical earpiece/headphone/dock path,
- no active metadata p11 usage,
- no vendor_boot/init_boot/vendor_kernel_boot/A-B boot model,
- no md1dsp/md1arm7/md3img modem images,
- no Mali GPU truth,
- no Goodix touch truth.

The following must remain open leads, not false facts:
- PAR2822 and 4-0033 ownership,
- FLED/BLED/HR-SpO2 emitter routing,
- FM RF path,
- ACCDET/jack/accessory path,
- back mic/ANC/key/gesture physical mappings,
- DXCC consumer path,
- TEE node naming,
- /dev/sec semantics,
- locked-stock Widevine level.

The following must never be treated as BSP ghosts:
- Wiite/nRF control plane,
- UART1 ttyS1,
- I2C4 cp@50,
- MT6370 PMU/charger/LED paths,
- MD1 modem,
- Wi-Fi/Bluetooth/GNSS,
- Hynitron touch,
- ST7701S display,
- PowerVR GE8320,
- md_udc metadata,
- RPMB/DXCC/TRNG/EFUSEC/SECURITY_AO,
- SIM1 secure element.

Final rule:
A clean ROM must remove false hardware claims, preserve required scaffolds until
replacement tests pass, harden real hardware-control surfaces, and keep every
open physical lead explicitly named instead of promoting guesses into truth.

################################################################################
# END SECTION 12 — NEGATIVE HARDWARE, BSP GHOSTS & OPEN PHYSICAL LEADS
################################################################################
