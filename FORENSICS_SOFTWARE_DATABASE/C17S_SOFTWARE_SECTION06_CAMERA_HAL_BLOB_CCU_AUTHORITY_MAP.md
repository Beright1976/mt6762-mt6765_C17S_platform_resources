################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 06 — CAMERA STACK — HAL, BLOBS, SENSOR IDENTITIES & CCU
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 06 — CAMERA / HAL / BLOB / CCU AUTHORITY MAP
# Closure     : CLOSED WITH BF2553L PHYSICAL-FUNCTION, EXACT SENINF ROUTING, IRTX ENDPOINT, AND FLED2 PHYSICAL-MAPPING LEADS OPEN
# Date        : 2026-06-16 live provider, sensor, I2C, SENINF, flashlight, health/SpO2 separation, blob-manifest, and dependency-chain closure pass
################################################################################

################################################################################
# SCOPE
################################################################################
This section documents the software-facing camera stack for the C17S software
hardware/ROM digital twin. It is the canonical software authority for Android
camera provider publication, MTK Camera HAL 2.4 registration, camera sensor
identities, camera I2C topology, camera EEPROM and autofocus paths, CCU
participation, SENINF/MIPI exposure, MT6370 flashlight behavior, BF2553L lower
camera-main-two classification, camera HAL runtime dependency maps, and the
camera blob universe required to preserve stock camera functionality in a clean
AOSP 11 ROM.

Included:
- Android camera provider: android.hardware.camera.provider@2.4::ICameraProvider/internal/0.
- Android camera service and framework-facing camera inventory.
- MTK camera background service surfaces: vendor.mediatek.hardware.camera.bgservice@1.0 and @1.1.
- MTK camera lomo-effect HAL surface.
- Active Android cameras: GC2385 rear/back and GC030A front.
- Lower camera-main-two path: BF2553L loader path, I2C6 camera_main_two, I2C6 camera_main_two_af, and I2C6 camera_main_two_eep.
- Camera I2C topology: I2C2 main/front camera, MAINAF, main/sub EEPROM; I2C4 CCU hardware-trigger and health separation; I2C6 main2/BF2553L candidate path.
- Camera dev nodes and SELinux labels: /dev/kd_camera_hw, /dev/camera-isp, /dev/camera-dpe, /dev/camera-fdvt, /dev/CAM_CAL_DRV, /dev/MAINAF.
- Platform camera nodes: kd_camera_hw1, seninf1, seninf2, seninf3, seninf4, CAM_CAL_DRV.0, flashlight_core, flashlights_mt6370.
- SENINF/MIPI/CSI exposed hardware blocks and runtime closure state.
- MT6370 flashlight, BLED, FLED1, FLED2, and IR-transmitter rail separation.
- Heart-rate and SpO2 separation from the camera stack.
- camerahalserver runtime process state, mapped libraries, active loader scaffold, and file-descriptor behavior.
- Sensor tuning and IdxMgr blob set for GC2385, GC030A, and BF2553L.
- Sony PDAF loader-required scaffold libraries.
- libcamalgo.gyro.so classification as a camera algorithm dependency, not gyroscope hardware proof.
- CCU firmware and CCU library chain.
- Full normalized camera blob manifest with size, mode, owner/group, and SHA256 for captured entries.
- Clean ROM retain/strip/reconcile disposition.

Excluded:
- General Android SensorService ownership; see Section 04.
- Heart-rate, SpO2, step counter, Wiite/nRF command protocol, and health APK behavior except where testing separates those paths from camera.
- Display/preview graphics rendering except camera preview dependency cross-reference; see Section 05.
- Media codec internals, audio-video sync, or encoder pipeline beyond camera library dependencies.
- APK decompilation of camera or health applications.
- Final SELinux policy minimization.
- Physical teardown or board tracing.

################################################################################
# SOURCE AUTHORITY
################################################################################
Source authority order:
TIER 1 : Live rooted C17S runtime evidence from adb shell under su.
TIER 2 : Clean extracted super_unpacked filesystem authority.
TIER 3 : Completed software Sections 00 through 05.
TIER 4 : Hardware digital-twin database cross-reference.
FINAL  : Beright / Albert live correction gate.

Primary live captures consumed:
- `lshal`, `service list`, and `getprop` for camera provider/service publication.
- `dumpsys media.camera` / camera provider debug output.
- `/proc/driver/camera_info` and `/proc/driver/imgsensor_status_info`.
- `/sys/bus/i2c/devices/*` for camera, CCU, EEPROM, AF, health, and PMU nodes.
- `/sys/devices/platform/*` for kd_camera_hw and SENINF blocks.
- `/sys/class/flashlight/*`, `/sys/class/leds/*`, and `/sys/class/regulator/*` for MT6370 flash/BLED and irtx_ldo separation.
- `/dev/*` camera device-node ownership/label capture.
- `/proc/<camerahalserver-pid>/maps`, `/proc/<pid>/fd`, process status, dmesg, and logcat filters.
- Controlled rear/front capture, flashlight/torch, heart-rate, and SpO2 correlation gates.

Primary file-authority captures consumed:
- Gate 5B normalized camera blob manifest.
- Gate 5B normalized SHA256 manifest.
- Gate 5C critical camera dependency extract.
- Host-side strings/readelf evidence for BF2553L tuning and libcameracustom loader dependencies.

################################################################################
# RULES
################################################################################
No stale values.
No borrowed MTK values.
No generic MT6765 camera assumptions.
No promotion from file presence to runtime activity.
No promotion from loader dependency to physical hardware.
No promotion from I2C node presence to Android camera provider publication.
No promotion from public part-name lookup to physical placement on this board.
No collapse of health/SpO2 optical behavior into camera unless runtime logs show camera participation.
No collapse of `irtx_ldo` into BF2553L unless board tracing or runtime control proves a shared endpoint.
No claim of exact SENINF routing unless stock runtime or extracted device tree exposes exact sensor-to-SENINF assignment.

Layer-specific camera truth rules:
1. Android camera count is defined by camera provider debug output, not by camera-named I2C nodes or tuning blobs.
2. A sensor tuning blob is a loader/camera-stack fact, not physical hardware proof by itself.
3. BF2553L is not classified as an exposed Android camera on current evidence.
4. BF2553L is classified as a loader-required lower camera-main-two path because it is present in the camera custom dependency chain and the board exposes a complete I2C6 camera_main_two topology.
5. Sony PDAF libraries are loader scaffold on this build. They are not evidence of Sony IMX physical cameras.
6. `libcamalgo.gyro.so` is a camera algorithm dependency. It is not gyroscope hardware proof.
7. Heart-rate and SpO2 are separated from camera when runtime logs show Wiite/nRF ownership and no camera-provider/BF2553L correlation.
8. Exact SENINF routing is not asserted when stock runtime interfaces expose only SENINF block presence, MIPI lane count, and sensor identity.
9. First clean-ROM boot must preserve the complete loader-required camera blob set.

################################################################################
# 06.0 — CAPTURE BOUNDARY
################################################################################
| Field | Value | Classification |
|---|---|---|
| Device | LOKMAT APPLLP 5 MAX / C17S | PROJECT_DEVICE |
| Board | WP_C17S_PIX_TFT_D4 | PROJECT_BOARD |
| Stock vendor context | Android 10 / API 29 vendor stack | STOCK_VENDOR_CONTEXT |
| Clean ROM target | AOSP 11 with retained Android 10 vendor blobs | TARGET_CONTEXT |
| Camera provider | android.hardware.camera.provider@2.4::ICameraProvider/internal/0 | LIVE_HAL |
| camerahalserver capture PID | 696 | BOOT_VARIABLE_CAPTURE_PID |
| camerahalserver state | running | LIVE_INIT_STATE |
| cameraserver state | running | LIVE_INIT_STATE |
| Provider physical devices | 2 | LIVE_PROVIDER_DEBUG |
| Provider virtual devices | 2 | LIVE_PROVIDER_DEBUG |
| Active Android rear camera | GC2385 / internal/0 / BACK | LIVE_PROVIDER_DEBUG |
| Active Android front camera | GC030A / internal/1 / FRONT | LIVE_PROVIDER_DEBUG |
| Lower main2 path | BF2553L / camera_main_two / I2C6 | LOADER + I2C_TOPOLOGY |
| Current BF2553L Android provider state | not provider-enumerated | CLOSED_NEGATIVE |
| Exact sensor-to-SENINF assignment | not exposed by tested stock interfaces | CLOSED_NON_EXPOSURE |
| FLED1 torch path | active tested MT6370 flash/torch path | LIVE_TESTED |
| FLED2 torch path | present, not triggered in tested torch path | OPEN_MAPPING |
| irtx_ldo | 3.4 V IR-transmitter rail candidate, not enabled in tested flash/health gates | OPEN_ENDPOINT |
| Health/SpO2 path | Wiite/nRF controlled, not camera-correlated | CLOSED_SEPARATION |

################################################################################
# 06.1 — COMPLETION MATRIX
################################################################################
| Scope item | Final state | Evidence / treatment |
|---|---|---|
| Camera provider HAL | CLOSED | provider@2.4 internal/0 published and camerahalserver running |
| Android camera count | CLOSED | provider debug publishes two physical devices |
| GC2385 rear camera | CLOSED | provider device 0 / BACK; camera_info; I2C2 camera_main |
| GC030A front camera | CLOSED | provider device 1 / FRONT; camera_info; I2C2 camera_sub |
| BF2553L Android-camera status | CLOSED_NEGATIVE | not provider-enumerated in current stock runtime |
| BF2553L lower path | CLOSED_AS_LOWER_PATH | I2C6 camera_main_two + AF-style + EEPROM + loader dependency |
| BF2553L physical function | OPEN | physical aperture candidate exists but function not electrically mapped |
| Public BF2553L class | CAMERA_SENSOR_CLASS | public lookup supports camera/image sensor class, not IR emitter |
| Sony PDAF libs | CLOSED_AS_LOADER_SCAFFOLD | libcameracustom.so requires Sony PDAF wrappers |
| Gyro camera library | CLOSED_AS_CAMERA_DEPENDENCY | libcamalgo.gyro.so camera-stack dependency; no gyro hardware proof |
| Camera I2C topology | CLOSED | I2C2, I2C4 CCU, I2C6 main2 nodes mapped |
| MAINAF | CLOSED | I2C2 0x0C and /dev/MAINAF |
| EEPROM / CAM_CAL | CLOSED | I2C2 main/sub EEPROM and I2C6 main2 EEPROM |
| CCU path | CLOSED | I2C2/I2C4 CCU hw-trigger nodes and lib3a CCU chain |
| SENINF blocks | CLOSED | SENINF1-4 platform nodes confirmed |
| Exact SENINF assignment | CLOSED_NON_EXPOSURE | stock runtime tested does not expose exact mapping |
| MT6370 FLED1 | CLOSED | torch/strobe values changed and dmesg tied path to mt-flash-led1 |
| MT6370 FLED2 | OPEN_MAPPING | class node present but not triggered in tested path |
| MT6370 BLED | CLOSED_AS_SEPARATE | present and stable in torch gate; display/backlight path, not tested camera torch |
| irtx_ldo | OPEN_ENDPOINT | regulator exists; remained disabled in tested flash/health gates |
| Heart-rate / SpO2 separation | CLOSED | Wiite/nRF logs prove health path separate from camera |
| Full camera blob manifest | CLOSED | normalized Gate 5B manifest excludes CA false positives |
| Critical dependency chain | CLOSED | Gate 5C readelf extract confirms loader-critical relationships |
| Clean ROM disposition | CLOSED_INITIAL | preserve first boot; reduce only after validation |

################################################################################
# 06.2 — HIGH-LEVEL CAMERA ARCHITECTURE
################################################################################
The C17S camera stack is an MTK Camera HAL 2.4 stack running through
`camerahalserver` and exposing exactly two Android camera devices to the
framework. The framework-visible devices are GC2385 rear/back and GC030A front.

The lower camera fabric is larger than the Android provider inventory. The board
and blob stack also expose a `camera_main_two` path on I2C6 with AF-style and
EEPROM companions, and the MTK camera custom library directly depends on
`bf2553L_mipi_raw_IdxMgr.so`. This is not treated as a third Android camera.
It is treated as a loader-required and board-declared lower camera-main-two path
with open physical function.

```text
Android Framework / cameraserver
        |
        v
android.hardware.camera.provider@2.4::ICameraProvider/internal/0
        |
        v
/vendor/bin/hw/camerahalserver
        |
        +-- android.hardware.camera.provider@2.4-impl-mediatek.so
        +-- vendor.mediatek.hardware.camera.bgservice@1.1-impl.so
        +-- vendor.mediatek.hardware.camera.lomoeffect@1.0-impl.so
        |
        +-- libmtkcam_device3 / libmtkcam_pipelinemodel / libmtkcam_hwnode
        +-- libcam.halsensor / libcameracustom
        +-- libcam.hal3a.v3 / lib3a.* / lib3a.ccu*
        +-- libcam.iopipe / libcamdrv_isp / libcamdrv_tuning_mgr
        |
        +-- GC2385 rear sensor path
        +-- GC030A front sensor path
        +-- BF2553L lower camera-main-two loader path
```

################################################################################
# 06.3 — CAMERA HAL / HIDL / SERVICE SURFACE
################################################################################
Live publication:
```text
FM    Y android.frameworks.cameraservice.service@2.0::ICameraService/default
DM,FC Y android.hardware.camera.provider@2.4::ICameraProvider/internal/0
DM    Y vendor.mediatek.hardware.camera.bgservice@1.0::IBGService/internal/0
DM    Y vendor.mediatek.hardware.camera.bgservice@1.1::IBGService/internal/0
FC    ? android.hardware.camera.provider@2.4::ICameraProvider/internal/0 PID 696
X     ? vendor.mediatek.hardware.camera.bgservice@1.1::IBGService/internal/0 PID 696
FC    ? android.hardware.camera.provider@2.4::I*/* (/vendor/lib64/hw/) (-mediatek) PID 696
X     ? vendor.mediatek.hardware.camera.bgservice@1.1::I*/* (/vendor/lib64/hw/) PID 696
X     ? vendor.mediatek.hardware.camera.lomoeffect@1.0::I*/* (/vendor/lib64/hw/)
```

Runtime properties:
```text
init.svc.camerahalserver=running
init.svc.cameraserver=running
camera.disable_zsl_mode=1
persist.sys.force.no.flashlight=false
ro.camera.sound.forced=0
ro.vendor.mtk_camera_app_version=3
vendor.camera.mdp.cz.enable=1
vendor.camera.mdp.dre.enable=0
```

Classification:
```text
[CAMERA_PROVIDER_HAL_CLOSED]
[CAMERAHALSERVER_RUNNING]
[MTK_CAMERA_BG_SERVICE_PRESENT]
[LOMO_EFFECT_SURFACE_PRESENT]
[ZSL_DISABLED_BY_PROPERTY]
[MTK_CAMERA_APP_VERSION_3]
```

################################################################################
# 06.4 — ANDROID CAMERA PROVIDER INVENTORY
################################################################################
Provider debug inventory:
```text
Camera Hal Service camerahalserver pid:696 64BIT
Physical Devices: # 2

[00] BACK
  hasFlashUnit:1
  SENSOR_DRVNAME_GC2385_MIPI_RAW
  orientation 90

[01] FRONT
  hasFlashUnit:0
  SENSOR_DRVNAME_GC030A_MIPI_RAW
  orientation 270

Virtual Devices: #2

device@3.5/internal/0
  torch AVAILABLE_OFF
  hasFlashUnit 1

device@3.5/internal/1
  torch NOT_AVAILABLE
  hasFlashUnit 0

Open Devices #0
max multi-open 1
```

| Android camera ID | Facing | Sensor | Flash | Orientation | Provider status |
|---|---:|---|---:|---:|---|
| internal/0 | BACK | GC2385 / SENSOR_DRVNAME_GC2385_MIPI_RAW | yes | 90 | ACTIVE_ANDROID_CAMERA |
| internal/1 | FRONT | GC030A / SENSOR_DRVNAME_GC030A_MIPI_RAW | no | 270 | ACTIVE_ANDROID_CAMERA |

BF2553L is not listed as an Android provider physical device and is not listed
as a virtual camera device. Android camera inventory is two physical provider
published devices.

################################################################################
# 06.5 — SENSOR IDENTITY FROM /proc/driver/camera_info
################################################################################
```text
CAM_Info[0]:gc2385_mipi_raw;
Pre/Cap/Vid/HSV/SLV: 1600x1200, grab x/y 0/0, delay_frm=2
Seninf_Type=1 (MIPI)
output_format=0 (B)
Driving_Current=3 (8mA)
mclk_freq=24
mipi_lane=1
PDAF_Support=0
HDR_Support=0

CAM_Info[1]:gc030a_mipi_raw;
Pre/Cap/Vid/HSV/SLV: 640x480, grab x/y 0/0, delay_frm=2
Seninf_Type=1 (MIPI)
output_format=0 (B)
Driving_Current=2 (6mA)
mclk_freq=24
mipi_lane=1
PDAF_Support=0
HDR_Support=0
```

| Sensor | Android role | Resolution class | MIPI | Lanes | MCLK | Drive | PDAF | HDR | Classification |
|---|---|---:|---:|---:|---:|---:|---:|---:|---|
| GC2385 | rear/back / internal/0 | 1600x1200 | yes | 1 | 24 MHz | 8 mA | no | no | ACTIVE_ANDROID_CAMERA |
| GC030A | front / internal/1 | 640x480 | yes | 1 | 24 MHz | 6 mA | no | no | ACTIVE_ANDROID_CAMERA |
| BF2553L | not Android provider-enumerated | not published | raw tuning present | unknown active routing | unknown active routing | not exposed | not provider-proven | not provider-proven | LOWER_CAMERA_MAIN_TWO_LOADER_PATH |

################################################################################
# 06.6 — GC2385 REAR / BACK CAMERA
################################################################################
GC2385 is the active rear/back Android camera.

Evidence summary:
```text
Provider: internal/0, BACK, SENSOR_DRVNAME_GC2385_MIPI_RAW, orientation 90, hasFlashUnit 1.
/proc/driver/camera_info: gc2385_mipi_raw, 1600x1200, MIPI, 1 lane, 24 MHz MCLK, 8 mA, PDAF 0, HDR 0.
I2C sensor: 2-001a camera_main, driver kd_camera_hw, compatible mediatek,camera_main, reg 0x1a.
AF companion: 2-000c camera_main_af, driver MAINAF, compatible mediatek,camera_main_af, reg 0x0c.
Calibration companion: 2-0050 camera_main_eeprom, driver CAM_CAL_DRV, compatible mediatek,camera_main_eeprom, reg 0x50.
```

Required blobs:
```text
vendor/lib64/gc2385_mipi_raw_IdxMgr.so
vendor/lib64/gc2385_mipi_raw_tuning.so
vendor/lib/gc2385_mipi_raw_tuning.so
```

Classification:
```text
[GC2385_ACTIVE_ANDROID_REAR_CAMERA]
[I2C2_CAMERA_MAIN_0x1A]
[MAINAF_COMPANION_PRESENT]
[CAM_CAL_MAIN_EEPROM_PRESENT]
[MIPI_1_LANE]
[24MHZ_MCLK]
[FLASH_UNIT_AVAILABLE]
[KEEP_FOR_FIRST_BOOT]
```

################################################################################
# 06.7 — GC030A FRONT CAMERA
################################################################################
GC030A is the active front Android camera.

Evidence summary:
```text
Provider: internal/1, FRONT, SENSOR_DRVNAME_GC030A_MIPI_RAW, orientation 270, hasFlashUnit 0.
/proc/driver/camera_info: gc030a_mipi_raw, 640x480, MIPI, 1 lane, 24 MHz MCLK, 6 mA, PDAF 0, HDR 0.
I2C sensor: 2-002d camera_sub, driver kd_camera_hw_bus2, compatible mediatek,camera_sub, reg 0x2d.
Calibration companion: 2-0052 camera_sub_eeprom, driver CAM_CAL_DEV2, compatible mediatek,camera_sub_eeprom, reg 0x52.
```

Required blobs:
```text
vendor/lib64/gc030a_mipi_raw_IdxMgr.so
vendor/lib64/gc030a_mipi_raw_tuning.so
vendor/lib/gc030a_mipi_raw_tuning.so
```

Classification:
```text
[GC030A_ACTIVE_ANDROID_FRONT_CAMERA]
[I2C2_CAMERA_SUB_0x2D]
[CAM_CAL_SUB_EEPROM_PRESENT]
[MIPI_1_LANE]
[24MHZ_MCLK]
[NO_FLASH_UNIT]
[KEEP_FOR_FIRST_BOOT]
```

################################################################################
# 06.8 — BF2553L / CAMERA_MAIN_TWO LOWER PATH
################################################################################
BF2553L is not an exposed Android camera on the captured stock runtime. It is a
real camera-stack path and must be retained for first boot because it is named by
the loader chain and because the board exposes a complete I2C6 camera-main-two
topology.

Layer classification:
```text
Android provider layer:     NOT_ENUMERATED
Camera custom loader layer: REQUIRED_BY_LIBCAMERACUSTOM
Tuning / NVRAM layer:       BF2553L RAW tuning object present
I2C topology layer:         I2C6 camera_main_two + camera_main_two_af + camera_main_two_eep
Physical layer:             open; visible auxiliary optical aperture/window candidate observed but not mapped
```

I2C6 topology:
```text
6-000c camera_main_two_af
  OF path: /i2c6@1100d000/camera_main_two_af@0c
  compatible: mediatek,camera_main_two_af
  status: okay
  reg: 0x0c

6-002d camera_main_two
  driver: kd_camera_hw_bus3
  modalias: i2c:camera_main_two
  OF path: /i2c6@1100d000/camera_main_two@2d
  compatible: mediatek,camera_main_two
  status: okay
  reg: 0x2d

6-0050 camera_main_two_eep
  driver: CAM_CAL_DEV3
  modalias: i2c:camera_main_two_eeprom
  OF path: /i2c6@1100d000/camera_main_two_eeprom@50
  compatible: mediatek,camera_main_two_eeprom
  status: okay
  reg: 0x50
```

Camera hardware parent main2 supply surfaces:
```text
vcamaf_main2-supply
vcamd_main2-supply
vcamio_main2-supply
```

BF2553L tuning string evidence:
```text
bf2553L_mipi_raw_tuning.so
getAENVRAM_bf2553lmipiraw
getAWBNVRAM_bf2553lmipiraw
NSFeature::RAWSensorInfo<9555>::getInstance
impGetDefaultData
impGetFlickerPara
bf2553lmipiraw
```

Required blobs:
```text
vendor/lib64/bf2553L_mipi_raw_IdxMgr.so
vendor/lib64/bf2553L_mipi_raw_tuning.so
vendor/lib/bf2553L_mipi_raw_tuning.so
```

Final classification:
```text
[LOWER_CAMERA_MAIN_TWO_PATH]
[CAMERA_HAL_LOADER_REQUIRED]
[I2C6_CAMERA_MAIN_TWO_TOPOLOGY_CONFIRMED]
[MAIN2_POWER_SURFACE_PRESENT]
[RAW_TUNING_OBJECT_PRESENT]
[NOT_ANDROID_PROVIDER_ENUMERATED]
[NO_REAR_CAPTURE_CORRELATION_OBSERVED]
[NO_FRONT_CAPTURE_CORRELATION_OBSERVED]
[NO_FLASH_MODE_CORRELATION_OBSERVED]
[NO_HEALTH_SPO2_CORRELATION_OBSERVED]
[OPEN_PHYSICAL_FUNCTION_LEAD]
[KEEP_FOR_FIRST_BOOT]
```

Correct database wording:
```text
BF2553L is not classified as an exposed Android camera. It is classified as a
loader-required lower camera-main-two path with confirmed I2C6 topology,
main2 power-supply surfaces, BF2553L RAW tuning/index blobs, and unresolved
physical function.
```

################################################################################
# 06.9 — PUBLIC BF2553L CLASSIFICATION BOUNDARY
################################################################################
Public lookup for BF2553L supports classification as a camera/image-sensor class
part, including references to BF2553L as a 5 MP BSI camera sensor in phone camera
spec contexts. Public lookup does not support BF2553L as an IR transmitter.

This external classification is used only to avoid misclassifying BF2553L as an
IR emitter. It is not used to prove physical placement on this board.

Final rule:
```text
BF2553L public class: camera / image sensor candidate
BF2553L not classified as: IR transmitter, IR LED, flashlight emitter, health optical emitter
Board-specific physical function: open until teardown, board trace, or active runtime proof
```

################################################################################
# 06.10 — I2C CAMERA / CCU / EEPROM / AF MAP
################################################################################
| Bus-node | Name | Driver | Role | Final classification |
|---|---|---|---|---|
| 2-000c | camera_main_af | MAINAF | GC2385 autofocus companion | ACTIVE_CAMERA_COMPANION |
| 2-0010 | ccu_sensor_i2c_2_hw | ccu_i2c_2_hwtrg | CCU sensor I2C hardware trigger | CCU_CAMERA_SUPPORT |
| 2-001a | camera_main | kd_camera_hw | GC2385 rear camera sensor path | ACTIVE_REAR_CAMERA |
| 2-002d | camera_sub | kd_camera_hw_bus2 | GC030A front camera sensor path | ACTIVE_FRONT_CAMERA |
| 2-0050 | camera_main_eeprom | CAM_CAL_DRV | main camera calibration | ACTIVE_CALIBRATION |
| 2-0052 | camera_sub_eeprom | CAM_CAL_DEV2 | sub/front camera calibration | ACTIVE_CALIBRATION |
| 4-0011 | ccu_sensor_i2c_4_hw | ccu_i2c_4_hwtrg | CCU hardware trigger | CCU_CAMERA_SUPPORT |
| 4-0033 | heartrate | heartrate | health optical path | SECTION04_HEALTH_NOT_CAMERA |
| 4-0050 | cp | wiite_corp | Wiite/nRF control path | SECTION04_NRF_NOT_CAMERA |
| 5-0034 | subpmic_pmu | mt6370_pmu | MT6370 PMU / FLED/BLED | FLASH_POWER_SUPPORT |
| 6-000c | camera_main_two_af | camera_main_two_af | main2 AF-style companion | LOWER_CAMERA_MAIN2 |
| 6-002d | camera_main_two | kd_camera_hw_bus3 | BF2553L/main2 candidate path | LOWER_CAMERA_MAIN2 |
| 6-0050 | camera_main_two_eep | CAM_CAL_DEV3 | main2 calibration path | LOWER_CAMERA_MAIN2 |

Important separation:
```text
I2C4 4-0033 heartrate and I2C4 4-0050 wiite_corp are not BF2553L.
They belong to the health / Wiite / nRF path.

I2C6 6-002d camera_main_two is not an Android camera provider device.
It belongs to the lower camera-main-two fabric.
```

################################################################################
# 06.11 — CAMERA DEVICE NODES, OWNERSHIP, AND SELINUX
################################################################################
Live camera device nodes:
```text
/dev/kd_camera_hw   crw-rw---- system camera u:object_r:kd_camera_hw_device:s0 242,0
/dev/camera-isp     crw-rw---- system camera u:object_r:camera_isp_device:s0 237,0
/dev/camera-dpe     crw-rw---- system camera u:object_r:camera_dpe_device:s0 236,0
/dev/camera-fdvt    crw-rw---- system camera u:object_r:camera_fdvt_device:s0 235,0
/dev/CAM_CAL_DRV    crw-rw---- system camera u:object_r:CAM_CAL_DRV_device:s0 241,0
/dev/MAINAF         crw-rw---- system camera u:object_r:MAINAF_device:s0 231,0
```

Idle `camerahalserver` file descriptors in the capture showed binder, trace,
DMA-buffer/graphics and memory-management resources such as `/dev/dri/renderD128`,
`/dev/ion`, `/dev/MTK_SMI`, and `/dev/mtk_cmdq`. Camera hardware nodes may be
opened transiently during sensor operation and should not be treated as absent
from idle FD state.

ROM implication:
```text
Preserve camera group ownership and SELinux object labels.
Do not remove /dev/CAM_CAL_DRV, /dev/MAINAF, /dev/kd_camera_hw, ISP/DPE/FDVT nodes.
Do not assume idle FD absence means hardware irrelevance.
```

################################################################################
# 06.12 — PLATFORM CAMERA AND SENINF NODES
################################################################################
Confirmed platform nodes:
```text
1a040000.kd_camera_hw1 -> DRIVER=image_sensor, OF_COMPATIBLE mediatek,camera_hw
1a040000.seninf1       -> mediatek,seninf1
1a041000.seninf2       -> mediatek,seninf2
1a042000.seninf3       -> mediatek,seninf3
1a043000.seninf4       -> mediatek,seninf4
CAM_CAL_DRV.0          -> DRIVER=CAM_CAL_DRV
flashlight_core        -> DRIVER=flashlight, mediatek,flashlight_core
flashlights_mt6370     -> DRIVER=flashlights-mt6370
```

Camera hardware parent exposed clock/pinctrl surfaces:
```text
CLK_TOP_CAMTG_SEL
CLK_TOP_CAMTG1_SEL
CLK_TOP_CAMTG2_SEL
CLK_TOP_CAMTG3_SEL
CLK_MCLK_6M
CLK_MCLK_12M
CLK_MCLK_13M
CLK_MCLK_24M
CLK_MCLK_26M
CLK_MCLK_48M
CLK_MCLK_52M
CLK_CAM_SENINF_CG
CLK_MIPI_C0_26M_CG
CLK_MIPI_C1_26M_CG
CLK_MIPI_ANA_0A_CG
CLK_MIPI_ANA_0B_CG
CLK_MIPI_ANA_1A_CG
CLK_MIPI_ANA_1B_CG
CLK_MIPI_ANA_2A_CG
CLK_MIPI_ANA_2B_CG
CLK_SCP_SYS_CAM
```

Pinctrl names:
```text
default
cam0_rst0 / cam0_rst1
cam0_pnd0 / cam0_pnd1
cam1_rst0 / cam1_rst1
cam1_pnd0 / cam1_pnd1
cam0_mclk_on / cam0_mclk_off
cam1_mclk_on / cam1_mclk_off
```

Main2 support surfaces:
```text
vcamaf_main2-supply
vcamd_main2-supply
vcamio_main2-supply
```

Classification:
```text
[KD_CAMERA_HW_PARENT_CLOSED]
[SENINF_BLOCKS_PRESENT]
[CAMERA_CLOCK_SURFACES_PRESENT]
[MAIN2_CAMERA_POWER_SURFACE_PRESENT]
```

################################################################################
# 06.13 — SENINF / CSI / MIPI ROUTING CLOSURE
################################################################################
Confirmed SENINF blocks:
| Block | Platform node | Compatible | Base | Size |
|---|---|---|---|---|
| SENINF1 | /sys/devices/platform/1a040000.seninf1 | mediatek,seninf1 | 0x1a040000 | 0x1000 |
| SENINF2 | /sys/devices/platform/1a041000.seninf2 | mediatek,seninf2 | 0x1a041000 | 0x1000 |
| SENINF3 | /sys/devices/platform/1a042000.seninf3 | mediatek,seninf3 | 0x1a042000 | 0x1000 |
| SENINF4 | /sys/devices/platform/1a043000.seninf4 | mediatek,seninf4 | 0x1a043000 | 0x1000 |

Runtime closure:
```text
GC2385 = MIPI, 1 lane, 24 MHz MCLK.
GC030A = MIPI, 1 lane, 24 MHz MCLK.
/proc/driver/imgsensor_status_info returned only: ERR_L0, 0.
Rear/front runtime SENINF filters did not expose exact sensor-to-SENINF mapping.
Debugfs discovery exposed clocks, not direct sensor-to-SENINF assignment.
Clock-state sampling returned rates but zero enable/prepare counts at sample time.
```

Final SENINF wording:
```text
SENINF1-4 are confirmed platform hardware blocks at 0x1a040000, 0x1a041000,
0x1a042000, and 0x1a043000. The active Android cameras, GC2385 and GC030A, are
confirmed as MIPI sensors with one MIPI lane each, 24 MHz MCLK, no PDAF, and no
HDR. The kd_camera_hw1 parent exposes CAMTG, SENINF, MIPI, camera MCLK, SCP
camera, and main2 camera power-supply surfaces. However, the stock live
interfaces tested — camera_info, imgsensor_status_info, dmesg/logcat runtime
filters, debugfs discovery, and clock-state sampling — do not expose exact
sensor-to-SENINF index assignment. Therefore exact GC2385/GC030A/BF2553L SENINF
port assignment is not asserted in this section.
```

################################################################################
# 06.14 — MT6370 FLASHLIGHT / BLED / IRTX SEPARATION
################################################################################
Live flashlight class:
```text
/sys/class/flashlight/mt-flash-led1
  name mt6370-fled
  torch_max_brightness 31
  strobe_max_brightness 256

/sys/class/flashlight/mt-flash-led2
  name mt6370-fled
  torch_max_brightness 31
  strobe_max_brightness 256

/sys/class/flashlight/mt6370_pmu_bled
  name mt6370_pmu_bled
  torch_max_brightness 255
  strobe_max_brightness 255
```

Gate 3C result:
```text
mt-flash-led1 changed during the tested flashlight/torch path.
mt-flash-led2 remained at zero during the tested path.
mt6370_pmu_bled remained stable during the tested torch path.
irtx_ldo stayed disabled with num_users=0 during the tested torch path.
BF2553L / camera_main_two showed no flashlight-mode correlation.
```

Dmesg tied flash behavior to MT6370 and mt-flash-led1:
```text
flashlights_mt6370: mt6370_set_scenario: Decrease voltage level.
charger_manager_enable_high_voltage_charging: user: mt-flash-led1, en = 0
```

Classification:
| Surface | Classification | Disposition |
|---|---|---|
| mt-flash-led1 | ACTIVE_TESTED_FLASH_TORCH_PATH | KEEP |
| mt-flash-led2 | PRESENT_DRIVER_BOUND_FLASH_CHANNEL / NOT_TRIGGERED_IN_GATE3C | KEEP / OPEN_PHYSICAL_CHANNEL_MAPPING |
| mt6370_pmu_bled | PRESENT_BLED_PATH / NOT_CAMERA_FLASH_TRIGGERED_IN_GATE3C | KEEP / CROSS_REFERENCE_DISPLAY_POWER_LED_PATH |
| irtx_ldo | IR_TRANSMITTER_RAIL_CANDIDATE / NOT_USED_BY_TESTED_FLASH_PATH | OPEN_PHYSICAL_ENDPOINT |
| BF2553L | NO_FLASH_MODE_CORRELATION_OBSERVED | KEEP_LOWER_CAMERA_PATH |

################################################################################
# 06.15 — HEART-RATE / SPO2 / CAMERA SEPARATION
################################################################################
Heart-rate and SpO2 runtime testing did not correlate BF2553L/camera_main_two
with the health optical path. During heart-rate and SpO2 activation, the kernel
log showed `sensors@2.0-ser` enabling HRS/SPO2 through `wiite_corp`, writing
feature configuration frames to the nRF52832, receiving IRQ 27 events, and
publishing EBUS notifications. The `irtx_ldo` rail remained disabled with
`num_users=0` in the captured poll window. Therefore the health/SpO2 path is
recorded as Wiite/nRF-controlled and separated from the Android camera provider
and BF2553L lower camera path.

Heart-rate key sequence:
```text
[HRS] hrs_store_active buf=20,1
[HRS] hrs_store_active en=1
[wiite_corp] nrf_ctrl_hrs_enable: set nrf control heart rate sensor enable
Config write to 52832: 0x01, 0x03, 0x01, 0x04
[HRS] hrs_real_enable: enable heart_rate real enable
IRQ 27 event path via wiite_corp / nrf_ctrl_irq_process
EBUS notify value:9002
```

SpO2 key sequence:
```text
[BLOD_OXYGEN] spo2_store_active en=1
[wiite_corp] nrf_ctrl_spo2_enable: set nrf control heart rate sensor enable
Config write to 52832: 0x04, 0x03, 0x01, 0x04
IRQ 27 event path via wiite_corp / nrf_ctrl_irq_process
spo2_notify: heart_value:0 spo2_value:0
```

Final classification:
```text
Heart-rate: [HEALTH_FUNCTION_CONFIRMED] [WIITE_NRF_CONTROLLED] [NOT_CAMERA_PROVIDER] [NOT_BF2553L_CORRELATED]
SpO2:       [SPO2_FUNCTION_CONFIRMED] [WIITE_NRF_CONTROLLED] [NOT_CAMERA_PROVIDER] [NOT_BF2553L_CORRELATED]
irtx_ldo:   [IR_TRANSMITTER_RAIL_CANDIDATE] [NOT_ENABLED_DURING_CAPTURED_HRS_SPO2_POLL] [OPEN_PHYSICAL_ENDPOINT]
BF2553L:    [NO_HEALTH_MODE_CORRELATION_OBSERVED] [NO_SPO2_MODE_CORRELATION_OBSERVED]
```

################################################################################
# 06.16 — CAMERAHALSERVER RUNTIME PROCESS AND MAPS
################################################################################
Capture process:
```text
Camera Hal Service camerahalserver pid:696 64BIT
UID: cameraserver / 1047
GID: audio / 1005
groups: 1000 1003 1004 1006 1013 1015 1026
threads: 8
```

Runtime-mapped / loader-critical library classes included:
```text
/vendor/bin/hw/camerahalserver
/vendor/lib64/hw/android.hardware.camera.provider@2.4-impl-mediatek.so
/vendor/lib64/hw/vendor.mediatek.hardware.camera.bgservice@1.1-impl.so
/vendor/lib64/libcameracustom.so
/vendor/lib64/libcam.halsensor.so
/vendor/lib64/libcam.hal3a.v3.so
/vendor/lib64/libcam.feature_utils.so
/vendor/lib64/libcamalgo.gyro.so
/vendor/lib64/lib3a.ccudrv.so
/vendor/lib64/lib3a.ccuif.so
/vendor/lib64/gc2385_mipi_raw_IdxMgr.so
/vendor/lib64/gc030a_mipi_raw_IdxMgr.so
/vendor/lib64/bf2553L_mipi_raw_IdxMgr.so
/vendor/lib64/libSonyIMX230PdafLibrary*.so
/vendor/lib64/libSonyIMX338PdafLibrary*.so
/vendor/lib64/libSonyIMX386PdafLibrary*.so
/vendor/lib64/libSonyIMX519PdafLibrary*.so
/vendor/lib64/mtkcam/libmtkcam_streaminfo_plugin-p1stt.so
```

Important mapping boundary:
```text
Filtered process maps clearly showed bf2553L_mipi_raw_IdxMgr.so mapped.
The filtered map evidence did not independently prove bf2553L_mipi_raw_tuning.so mapped live at the same moment.
The tuning blob remains required as a sensor-specific camera tuning blob and loader-adjacent file, but runtime-map wording must stay exact.
```

################################################################################
# 06.17 — SONY PDAF LOADER SCAFFOLD
################################################################################
`libcameracustom.so` directly requires the Sony PDAF wrappers:
```text
libSonyIMX230PdafLibraryWrapper.so
libSonyIMX386PdafLibraryWrapper.so
libSonyIMX338PdafLibraryWrapper.so
libSonyIMX519PdafLibraryWrapper.so
```

The corresponding Sony PDAF libraries are present:
```text
libSonyIMX230PdafLibrary.so
libSonyIMX338PdafLibrary.so
libSonyIMX386PdafLibrary.so
libSonyIMX519PdafLibrary.so
```

Classification:
```text
[LOADER_REQUIRED_SCAFFOLD]
[NOT_PHYSICAL_SONY_SENSOR_PROOF]
[KEEP_FOR_FIRST_BOOT]
[STRIP_ONLY_AFTER_BOOT_AND_CAMERA_VALIDATION]
```

################################################################################
# 06.18 — LIBCAMALGO.GYRO.SO CLASSIFICATION
################################################################################
`libcamalgo.gyro.so` is present in the normalized camera blob manifest and appears
as a dependency in MTK camera streaming/hwnode paths. It is classified strictly
as a camera algorithm dependency.

Final classification:
```text
[ACTIVE_RUNTIME_DEPENDENCY]
[CAMERA_ALGO_LIBRARY]
[NO_GYRO_HARDWARE_PROOF]
[KEEP_FOR_FIRST_BOOT]
```

This does not alter the hardware-database negative for gyroscope.

################################################################################
# 06.19 — CCU / 3A / ISP DEPENDENCY CHAIN
################################################################################
CCU firmware/binaries:
```text
vendor/bin/lib3a.ccu.dm
vendor/bin/lib3a.ccu.pm
```

CCU userspace libraries:
```text
vendor/lib64/lib3a.ccudrv.so
vendor/lib64/lib3a.ccuif.so
```

3A stack:
```text
lib3a.ae.core.so
lib3a.ae.so
lib3a.af.core.so
lib3a.af.so
lib3a.awb.core.so
lib3a.awb.so
lib3a.flash.so
lib3a.gma.so
lib3a.lce.so
lib3a.log.so
libcam3a_imem.so
```

`libcam.hal3a.v3.so` directly depends on CCU, ISP-feature, tuning, iopipe,
NVRAM, LSC, flicker, FDFT, NR, AE, AF, AWB, flash, and camera custom libraries.
This closes CCU/3A as first-boot-required for stock camera behavior.

Classification:
```text
[CCU_PRESENT]
[3A_CHAIN_PRESENT]
[ISP_TUNING_CHAIN_PRESENT]
[KEEP_FOR_FIRST_BOOT]
```

################################################################################
# 06.20 — CRITICAL CAMERA DEPENDENCY EXTRACT
################################################################################
Gate 5C extracted the critical dependency chain below. This is the disposition
basis for first-boot carry/strip rules.

```text
--- vendor/bin/hw/camerahalserver ---
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_ulog.so
NEEDED: android.hardware.camera.provider@2.4.so
NEEDED: vendor.mediatek.hardware.camera.frhandler@1.0.so
NEEDED: vendor.mediatek.hardware.camera.postproc@1.0.so
NEEDED: vendor.mediatek.hardware.camera.bgservice@1.0.so
NEEDED: vendor.mediatek.hardware.camera.bgservice@1.1.so
NEEDED: libmtkcam_grallocutils.so
NEEDED: vendor.mediatek.hardware.camera.atms@1.0.so

--- vendor/lib64/libcameracustom.so ---
NEEDED: libaedv.so
NEEDED: libcam.hal3a.log.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libSonyIMX230PdafLibraryWrapper.so
NEEDED: libSonyIMX386PdafLibraryWrapper.so
NEEDED: libSonyIMX338PdafLibraryWrapper.so
NEEDED: libSonyIMX519PdafLibraryWrapper.so
NEEDED: android.hardware.power@1.0.so
NEEDED: vendor.mediatek.hardware.power@1.1.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: gc2385_mipi_raw_IdxMgr.so
NEEDED: gc030a_mipi_raw_IdxMgr.so
NEEDED: bf2553L_mipi_raw_IdxMgr.so
SONAME:  libcameracustom.so

--- vendor/lib64/libcam.halsensor.so ---
NEEDED: libcameracustom.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_metadata.so
NEEDED: libcamdrv_isp.so
NEEDED: libmtkcam_ulog.so
SONAME:  libcam.halsensor.so

--- vendor/lib64/libcam.hal3a.v3.so ---
NEEDED: lib3a.ccudrv.so
NEEDED: lib3a.ccuif.so
NEEDED: libion.so
NEEDED: libion_mtk.so
NEEDED: libfeatureiodrv_mem.so
NEEDED: libcamalgo.ispfeature.so
NEEDED: libmtkcam_tuning_utils.so
NEEDED: libmtkcam_ulog.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_imgbuf.so
NEEDED: libmtkcam_hwutils.so
NEEDED: lib3a.gma.so
NEEDED: lib3a.lce.so
NEEDED: libcamalgo.lsc.so
NEEDED: libcamalgo.flicker.so
NEEDED: libcamalgo.fdft.so
NEEDED: libcam3a_imem.so
NEEDED: libmtkcam_metadata.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_sysutils.so
NEEDED: libcam.pdtblgen.so
NEEDED: libcam.iopipe.so
NEEDED: libcam.hal3a.v3.nvram.so
NEEDED: libcam.hal3a.v3.lsctbl.so
NEEDED: libcamdrv_tuning_mgr.so
NEEDED: libcam.utils.sensorprovider.so
NEEDED: android.hardware.power@1.0.so
NEEDED: vendor.mediatek.hardware.power@2.0.so
NEEDED: libcamalgo.nr.so
NEEDED: libcameracustom.so
NEEDED: lib3a.af.core.so
NEEDED: lib3a.af.so
NEEDED: lib3a.awb.so
NEEDED: lib3a.awb.core.so
NEEDED: lib3a.flash.so
NEEDED: lib3a.ae.so
NEEDED: lib3a.ae.core.so
SONAME:  libcam.hal3a.v3.so

--- vendor/lib64/libmtkcam_hwnode.so ---
NEEDED: android.hardware.camera.device@3.2.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_ulog.so
NEEDED: libmtkcam_grallocutils.so
NEEDED: libmtkcam_imgbuf.so
NEEDED: libmtkcam_streamutils.so
NEEDED: libmtkcam_hwutils.so
NEEDED: libmtkcam_metadata.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_prerelease.so
NEEDED: libcam.feature_utils.so
NEEDED: libcameracustom.so
NEEDED: libcam.utils.sensorprovider.so
NEEDED: libcam.iopipe.so
NEEDED: libfeature_lmv.so
NEEDED: libfeature_3dnr.so
NEEDED: libfeature_rss.so
NEEDED: libfeature_fsc.so
NEEDED: libion_mtk.so
NEEDED: libion.so
NEEDED: libfeature_eis.so
NEEDED: libcameracustom.eis.so
NEEDED: libmtkcam_exif.so
NEEDED: libstereoinfoaccessor_vsdof.so
NEEDED: libmtkcam_sysutils.so
NEEDED: libcam.vhdr.so
NEEDED: libmfllcore.so
NEEDED: libcamalgo.dngop.so
NEEDED: libcamalgo.lmv.so
NEEDED: libcamalgo.gyro.so
NEEDED: libcamalgo.eis.so
NEEDED: libcamalgo.fdft.so
NEEDED: libfeature.face.so
NEEDED: libcamalgo.fsc.so
NEEDED: libdpframework.so
NEEDED: libmtkcam_tuning_utils.so
NEEDED: libmtkcam.featurepipe.streaming.so
NEEDED: libmtkcam.featurepipe.capture.so
NEEDED: libmtkcam_synchelper.so
NEEDED: lib3a.flash.so
NEEDED: lib3a.awb.core.so
NEEDED: lib3a.af.core.so
NEEDED: lib3a.ae.core.so
SONAME:  libmtkcam_hwnode.so

--- vendor/lib64/libmtkcam.featurepipe.streaming.so ---
NEEDED: libcamalgo.warp.so
NEEDED: libcamalgo.fsc.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_imgbuf.so
NEEDED: libmtkcam_ulog.so
NEEDED: libcam.iopipe.so
NEEDED: libmtkcam_rsc.so
NEEDED: libdpframework.so
NEEDED: libfeature.face.so
NEEDED: libcameracustom.so
NEEDED: libfeature_3dnr.so
NEEDED: libfeature_fsc.so
NEEDED: libmtkcam_tuning_utils.so
NEEDED: libcam.feature_utils.so
NEEDED: libmtkcam_3rdparty.so
NEEDED: libmtkcam_3rdparty.core.so
NEEDED: libcamalgo.lmv.so
NEEDED: libcamalgo.gyro.so
NEEDED: libcamalgo.eis.so
NEEDED: libfeature_eis.so
NEEDED: libcameracustom.eis.so
NEEDED: libmtkcam_exif.so
NEEDED: libmtkcam_metadata.so
NEEDED: libcamalgo.fdft.so
NEEDED: libfeature.stereo.provider.so
NEEDED: libion.so
NEEDED: libmtkcam_debugutils.so
SONAME:  libmtkcam.featurepipe.streaming.so

--- vendor/lib64/libmtkcam.featurepipe.capture.so ---
NEEDED: libcamalgo.fdft.so
NEEDED: lib3a.af.core.so
NEEDED: lib3a.flash.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_imgbuf.so
NEEDED: libmtkcam_ulog.so
NEEDED: libcam.iopipe.so
NEEDED: libdpframework.so
NEEDED: libcameracustom.so
NEEDED: libmtkcam_tuning_utils.so
NEEDED: libmtkcam_metadata.so
NEEDED: libcam.feature_utils.so
NEEDED: libcamalgo.dngop.so
NEEDED: libmtkcam_debugutils.so
NEEDED: lib3a.ae.core.so
NEEDED: libmtkcam_hwutils.so
NEEDED: libfeature.face.so
NEEDED: vendor.mediatek.hardware.power@2.0.so
NEEDED: libmfllcore.so
NEEDED: libcamalgo.mfnr.so
NEEDED: libmtkcam_exif.so
NEEDED: libmtkcam.featurepipe.vsdof_util.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_3rdparty.so
NEEDED: libmtkcam_3rdparty.core.so
NEEDED: libfeature.stereo.provider.so
NEEDED: lib3a.awb.core.so
SONAME:  libmtkcam.featurepipe.capture.so

--- vendor/lib64/libmtkcam_pipelinemodel_session.so ---
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_imgbuf.so
NEEDED: libmtkcam_ulog.so
NEEDED: libmtkcam_metadata.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_streamutils.so
NEEDED: libmtkcam_hwutils.so
NEEDED: libmtkcam_pipeline.so
NEEDED: libmtkcam_pipelinepolicy_factory.so
NEEDED: libmtkcam_pipelinemodel_utils.so
NEEDED: libcameracustom.so
NEEDED: libfeature.stereo.provider.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_prerelease.so
NEEDED: libmtkcam_pipelinemodel_zsl.so
NEEDED: libmtkcam_pipelinemodel_capture.so
NEEDED: vendor.mediatek.hardware.power@2.0.so
SONAME:  libmtkcam_pipelinemodel_session.so

--- vendor/lib64/libmtkcam_device3.so ---
NEEDED: android.hardware.camera.device@3.2.so
NEEDED: android.hardware.camera.device@3.3.so
NEEDED: android.hardware.camera.device@3.4.so
NEEDED: android.hardware.camera.device@3.5.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_ulog.so
NEEDED: libmtkcam_debugutils.so
NEEDED: libmtkcam_metadata.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_device3_app.so
NEEDED: libmtkcam_pipelinemodel.so
NEEDED: android.hardware.power@1.0.so
NEEDED: vendor.mediatek.hardware.power@2.0.so
NEEDED: libmtkcam_hwutils.so
SONAME:  libmtkcam_device3.so

--- vendor/lib64/hw/android.hardware.camera.provider@2.4-impl-mediatek.so ---
NEEDED: android.hardware.camera.common@1.0.so
NEEDED: android.hardware.camera.provider@2.4.so
NEEDED: android.hardware.camera.device@1.0.so
NEEDED: android.hardware.camera.device@3.2.so
NEEDED: libmtkcam_metadata.so
NEEDED: libmtkcam_modulehelper.so
NEEDED: libmtkcam_stdutils.so
NEEDED: libmtkcam_debugutils.so
NEEDED: libmtkcam_ulog.so
NEEDED: libmtkcam_metastore.so
NEEDED: libmtkcam_hwutils.so
SONAME:  android.hardware.camera.provider@2.4-impl-mediatek.so
```

################################################################################
# 06.21 — NORMALIZED CAMERA BLOB MANIFEST
################################################################################
Gate 5B normalized the camera manifest and excluded the first broad-pass false
positive CA certificate paths caused by matching `*3a*`. The normalized counts
were:

```text
3 vendor/bin
1 vendor/bin/hw
1 vendor/etc/init
1 vendor/etc/permissions
9 vendor/lib
123 vendor/lib64
3 vendor/lib64/hw
1 vendor/lib64/mtkcam
1 vendor/lib/hw
```

Canonical normalized camera blob table:

| Path | Size | Mode | Owner | Group | SHA256 |
|---|---:|---:|---|---|---|
| `vendor/bin/hw/camerahalserver` | 15880 | 644 | beright1976 | beright1976 | `cce134823576b072c62984fb42c2e216e60f1bb0ae00408625285f5c1808a3a9` |
| `vendor/bin/lib3a.ccu.dm` | 6676 | 644 | beright1976 | beright1976 | `c940701ab6f3f56c940e3714ad27fd83f519d3328457a899e8f0d7d90813b095` |
| `vendor/bin/lib3a.ccu.pm` | 29628 | 644 | beright1976 | beright1976 | `f299406cf6984e9c7ecd977c658026617127434df1103e025662f52126a47c73` |
| `vendor/bin/mtkcam-debug` | 41360 | 644 | beright1976 | beright1976 | `7c6d2c8b3f637941cf218a06a3ef71a648a31660f5e12f527f5720bebb1f37c8` |
| `vendor/etc/init/camerahalserver.rc` | 272 | 644 | beright1976 | beright1976 | `2f616b01dc163b4b8c72819d0e4d7fa7649fa47b9ef91f7165ced33659464c77` |
| `vendor/etc/permissions/android.hardware.camera.xml` | 1369 | 644 | beright1976 | beright1976 | `bf6cfa49449430b36c9b250f567d25ccdef79046b2721ec40a169f6137515680` |
| `vendor/lib/bf2553L_mipi_raw_tuning.so` | 2367776 | 644 | beright1976 | beright1976 | `a3e30fdd4b0cde5e2e80215949267d8753ca9b3fdb5e4bbd41ecc0f015f5b2b7` |
| `vendor/lib/gc030a_mipi_raw_tuning.so` | 2388244 | 644 | beright1976 | beright1976 | `946cbe5d1f56b08b59e40eeff12ea44b3d3cf94526856eae4d92a06de01e823b` |
| `vendor/lib/gc2385_mipi_raw_tuning.so` | 2453784 | 644 | beright1976 | beright1976 | `7c0f5043e65933673fef681db68d064c898be2a9dae25ebbcbc7d2c197e8545d` |
| `vendor/lib/hw/vendor.mediatek.hardware.camera.bgservice@1.1-impl.so` | 46660 | 644 | beright1976 | beright1976 | `741b4ccc713cc11d6fe4bc1d6fa97e6b93b82cdb4542d58f820637005b4edc3b` |
| `vendor/lib/libmtkcam.eventcallback.so` | 28416 | 644 | beright1976 | beright1976 | `74b9f1ab79e3d9291c99f1f9276b316554780ad609588cec24077e9a9716e4fc` |
| `vendor/lib/libmtkcam_debugutils.so` | 60060 | 644 | beright1976 | beright1976 | `9f71bc7a162d9e86a8e907c02e9d36c9379190f1f3adc5528585d0f6bc5852c4` |
| `vendor/lib/libmtkcam_stdutils.so` | 114912 | 644 | beright1976 | beright1976 | `9de7c1aa1ecdf9dbf96875f980df3bcb25fca4b34abfadfc5d7dddde7da50f6a` |
| `vendor/lib/libmtkcam_ulog.so` | 191204 | 644 | beright1976 | beright1976 | `8aa68d00da5101a789bfa68f227cc3c484b04d9cd8a30bb9b4588d55dbb070d9` |
| `vendor/lib/vendor.mediatek.hardware.camera.bgservice@1.0.so` | 88900 | 644 | beright1976 | beright1976 | `3540b3b09fb73b979cc90c4ffe710ff3512c0094f29bb2360dc434db350b75a3` |
| `vendor/lib/vendor.mediatek.hardware.camera.bgservice@1.1.so` | 101960 | 644 | beright1976 | beright1976 | `8ee78c97a330819f0a7e1ff8bb99e8f0bb5ef289f2f42e2f34d04a24bd936412` |
| `vendor/lib64/bf2553L_mipi_raw_IdxMgr.so` | 15024 | 644 | beright1976 | beright1976 | `18331bbf84075b3385eb376e2253baa34062e55fe8a417f1a198bb243183df93` |
| `vendor/lib64/bf2553L_mipi_raw_tuning.so` | 2367144 | 644 | beright1976 | beright1976 | `5ba7395e17d2b18ea28182a28bdbb571612b619a00a889da4d517883e8031c28` |
| `vendor/lib64/gc030a_mipi_raw_IdxMgr.so` | 15024 | 644 | beright1976 | beright1976 | `37c0ea486be3dcadf7f65d69793ddad99350966f6b0d77a4035daf96027dd297` |
| `vendor/lib64/gc030a_mipi_raw_tuning.so` | 2391712 | 644 | beright1976 | beright1976 | `96fae196b640ee8f05c82b1da7646d9c4b1ca2c451d5b493d9a5634689242db5` |
| `vendor/lib64/gc2385_mipi_raw_IdxMgr.so` | 15024 | 644 | beright1976 | beright1976 | `4b5cc9b67589b84b49b3691a1110e0d2916dbc07866e357ea3a73e2a20c0902a` |
| `vendor/lib64/gc2385_mipi_raw_tuning.so` | 2453160 | 644 | beright1976 | beright1976 | `55ad70696d6a5dcdce8ee660841f2362ace9ddf68bc7ab951aedc230e9b56695` |
| `vendor/lib64/hw/android.hardware.camera.provider@2.4-impl-mediatek.so` | 153200 | 644 | beright1976 | beright1976 | `392cb9e68d131ccc1bfbc95eedcee8cdad43d776d33bbdf2505ef479ad12891f` |
| `vendor/lib64/hw/vendor.mediatek.hardware.camera.bgservice@1.1-impl.so` | 46992 | 644 | beright1976 | beright1976 | `c7f391a766bc4c3f61032ead2c288b8403d2a7fc1d4953018631bbfa5d40eed4` |
| `vendor/lib64/hw/vendor.mediatek.hardware.camera.lomoeffect@1.0-impl.so` | 24736 | 644 | beright1976 | beright1976 | `defae3738158d10df01a3237400308be3a27bb2632858f03fc7e14a96b43a054` |
| `vendor/lib64/lib3a.ae.core.so` | 264432 | 644 | beright1976 | beright1976 | `c0d414f30d0031560d6342f4fdb6799cb619c23cb1fb841c2d1e5a9491094f93` |
| `vendor/lib64/lib3a.ae.so` | 162616 | 644 | beright1976 | beright1976 | `03cb10f9e6d21558768b25900ed6fa60988f12c4162c6e97014c7b43a62fdfba` |
| `vendor/lib64/lib3a.af.core.so` | 296872 | 644 | beright1976 | beright1976 | `12714c9ae8029344f600b51c98fd68144332713ab21ff0b15a1ea5f7a8e55d9c` |
| `vendor/lib64/lib3a.af.so` | 281128 | 644 | beright1976 | beright1976 | `6dcfeb5c733ddbb43725bfefb9a5e8b3a9b98647b0c2815d62efb40262528374` |
| `vendor/lib64/lib3a.awb.core.so` | 148816 | 644 | beright1976 | beright1976 | `5232196c5b99971acc4cfc4cd0c5954b84c9aa690681f2b073e2940b020ec246` |
| `vendor/lib64/lib3a.awb.so` | 51392 | 644 | beright1976 | beright1976 | `46eb2ad1af8a4f74ab2bfb7deea3db29a47071eb9a4336a75b7e88a2c542991b` |
| `vendor/lib64/lib3a.ccudrv.so` | 57648 | 644 | beright1976 | beright1976 | `9f5d3bf59a130006c697ce6a8f87ca4cd9002c873d1cacd800aa316e0b89f58c` |
| `vendor/lib64/lib3a.ccuif.so` | 47360 | 644 | beright1976 | beright1976 | `06d518bd761ed811e7450230f5fdc4a5d3477a4d0e66f224eb99702106e9203e` |
| `vendor/lib64/lib3a.flash.so` | 160496 | 644 | beright1976 | beright1976 | `ed0d73ddc3c59cd62018747f372d0aa96b74fea5032c2aa7c23cade353ebcbb3` |
| `vendor/lib64/lib3a.gma.so` | 39536 | 644 | beright1976 | beright1976 | `815e203ea4d88f2ae074d61dd22cf1f2de0a5f70d9dc39ceb6f72c7a188888cf` |
| `vendor/lib64/lib3a.lce.so` | 60072 | 644 | beright1976 | beright1976 | `75f0e5099b3a8000593b19dc17adb0e95687f6e08a2c831a27339921c19908b8` |
| `vendor/lib64/lib3a.log.so` | 15504 | 644 | beright1976 | beright1976 | `51aa79bd65b63f1e8e18f7a95e83ea34ac8b51ffe178a8a2dd6c23ec32b06f7f` |
| `vendor/lib64/libSonyIMX230PdafLibrary.so` | 14904 | 644 | beright1976 | beright1976 | `9b10970050a5e79e90ad2db5c0ba4ab8cff6ffaa4c31ab7d80f2ec55dbc81654` |
| `vendor/lib64/libSonyIMX230PdafLibraryWrapper.so` | 35704 | 644 | beright1976 | beright1976 | `e6b75a02633fd9c0ea9309970b246572f301dbdc3d15a2a8eed753ca2eb5ba1f` |
| `vendor/lib64/libSonyIMX338PdafLibrary.so` | 14904 | 644 | beright1976 | beright1976 | `f42d59149c9c0e09c8c3658cc39e452daec5c3de47ce458d7b71d8d91cd781be` |
| `vendor/lib64/libSonyIMX338PdafLibraryWrapper.so` | 35712 | 644 | beright1976 | beright1976 | `74e50e241f4d5effd6f32a429cf20a6fbe6ad74ba81abe8ab9a4136b35ed0f76` |
| `vendor/lib64/libSonyIMX386PdafLibrary.so` | 14904 | 644 | beright1976 | beright1976 | `6986c4ab4576d3814c6a86c113600bac84913ad16bd38a9676d33100d2f76f48` |
| `vendor/lib64/libSonyIMX386PdafLibraryWrapper.so` | 35712 | 644 | beright1976 | beright1976 | `33f70c9665a31470d40fecc35efbf355f30d7468936b765d0b2b0d8df74cb90b` |
| `vendor/lib64/libSonyIMX519PdafLibrary.so` | 14904 | 644 | beright1976 | beright1976 | `79842bed4b2c3baebfa819faff02dd80f4a5ffed710ff8f3fee784f504901b6c` |
| `vendor/lib64/libSonyIMX519PdafLibraryWrapper.so` | 35704 | 644 | beright1976 | beright1976 | `10190ba586cdb8391609a2a3fbadfa3caae99d82526b04612b97e8d148a00d3e` |
| `vendor/lib64/libcam.feature_utils.so` | 198120 | 644 | beright1976 | beright1976 | `c6dd95db2175c9e8b2b39568dfff7d82506e7fbdd224ff049d5b529340ec60ea` |
| `vendor/lib64/libcam.hal3a.cctsvr.so` | 157544 | 644 | beright1976 | beright1976 | `a6930a1b9fbc8bf9260c09f301daa3ef7e45d665b1041af6a6c35668f22ce532` |
| `vendor/lib64/libcam.hal3a.log.so` | 15304 | 644 | beright1976 | beright1976 | `15369950f87f47db523ac1b1daafbd7ab067607832bb7a6c1345635b0a70cb5e` |
| `vendor/lib64/libcam.hal3a.v3.dng.so` | 36792 | 644 | beright1976 | beright1976 | `c404c5fcd1b55d4b2e5f8f6436be2f6171032a9e3bb2ea44665a72eefbc6ec45` |
| `vendor/lib64/libcam.hal3a.v3.lsctbl.so` | 49520 | 644 | beright1976 | beright1976 | `0dfc34250cc6f976c5fc342baf5ce6d2d6214da6ca8496d4f603514729a11cc0` |
| `vendor/lib64/libcam.hal3a.v3.nvram.so` | 58064 | 644 | beright1976 | beright1976 | `630f44e72b7f01688b55414ff91ad08a48fe0de1543cade519c879d33714d401` |
| `vendor/lib64/libcam.hal3a.v3.so` | 2670344 | 644 | beright1976 | beright1976 | `dc4b755e4b1276c8365311e46eba27c49427a6778d96909b7eddd3f487c1a135` |
| `vendor/lib64/libcam.halisp.so` | 24352 | 644 | beright1976 | beright1976 | `1a069c636a84358a975812844ccf51ad6c223acbecb18d25044e5ba9badcad2d` |
| `vendor/lib64/libcam.halsensor.so` | 744112 | 644 | beright1976 | beright1976 | `26479ce31a860969989a3b868a685ab2424fba38b88c17fde227fdf378f764dd` |
| `vendor/lib64/libcam.iopipe.so` | 582032 | 644 | beright1976 | beright1976 | `e05e576c4dac9bdff507e1904814a9df104f723c7e6b582392d3ff3b13ad300c` |
| `vendor/lib64/libcam.lcs.so` | 32728 | 644 | beright1976 | beright1976 | `76fdb4eabfe3eb230ca21e30cc1bbcc533188739c4972d8fe944bd86163912fe` |
| `vendor/lib64/libcam.pdtblgen.so` | 40488 | 644 | beright1976 | beright1976 | `6517c6a96e33ba1f17d6ee5c67442777fff7cf16c82fea819585e9c20b28b5ac` |
| `vendor/lib64/libcam.utils.sensorprovider.so` | 111240 | 644 | beright1976 | beright1976 | `f064cd16422e0612ae3f548aabb66195085cefe40490d008925111c750498e91` |
| `vendor/lib64/libcam.vhdr.so` | 58632 | 644 | beright1976 | beright1976 | `804ccc9ba649743a2194cd7ce7b879dd6bca688d57f7f42cedf697ebf6eb7002` |
| `vendor/lib64/libcam3a_imem.so` | 23920 | 644 | beright1976 | beright1976 | `51e0acd613b2467b68b6d1d64a7ab33a5ae16814fe0202bb5a3a4e1dfd7cd412` |
| `vendor/lib64/libcamalgo.3dnr.so` | 43840 | 644 | beright1976 | beright1976 | `9e24c5c217edccc2299293b9627e0632993777f158cff263ce4bfb6e228d2daa` |
| `vendor/lib64/libcamalgo.abf.so` | 56176 | 644 | beright1976 | beright1976 | `90c83fc9af5d577a7e112ba013ff0fa677a23c24b7d1c8988d1b86a43f7715d1` |
| `vendor/lib64/libcamalgo.asd.so` | 302152 | 644 | beright1976 | beright1976 | `4d1ee95c6cec750e0878d65e306cd6756078611ad87b3667165fc041f54bf2a9` |
| `vendor/lib64/libcamalgo.dngop.so` | 43832 | 644 | beright1976 | beright1976 | `2703110f7417e538850f1826667f38b1f7479656548392542fcac8de4587a6c2` |
| `vendor/lib64/libcamalgo.eis.so` | 261912 | 644 | beright1976 | beright1976 | `8357ff8ccc0eadf260f53393847b593c64b785df09fdd1ca1f414ac30a73260d` |
| `vendor/lib64/libcamalgo.fdft.so` | 10278856 | 644 | beright1976 | beright1976 | `9076d0bb59de23f1656e77fa7857c6cb3b303eec656d8c6fec093cd7b4e00f86` |
| `vendor/lib64/libcamalgo.flicker.so` | 84672 | 644 | beright1976 | beright1976 | `3f0b0524c51a8ed267592ca10882f5c579412e96f9ffe529dc9f40fc58bf86ea` |
| `vendor/lib64/libcamalgo.fsc.so` | 31264 | 644 | beright1976 | beright1976 | `1a5442f862003e26758d3096dbbe78cb9300f487342be455f4e199b9f37d4e0f` |
| `vendor/lib64/libcamalgo.gyro.so` | 80968 | 644 | beright1976 | beright1976 | `d71bf4130e875ff017f93060a7793f3e61e82519a5ee2cad9de42548f4deb6bb` |
| `vendor/lib64/libcamalgo.hdr.so` | 93584 | 644 | beright1976 | beright1976 | `e1b5dc04e6a3cfe37e1dbc577be8ca93e2e93534f9af6c519bc040d7d33ca52a` |
| `vendor/lib64/libcamalgo.ispfeature.so` | 88304 | 644 | beright1976 | beright1976 | `45a69480dcb3d9bf65341c6c972ea27f89891bb66735653e5a2834e036dbce05` |
| `vendor/lib64/libcamalgo.lmv.so` | 52112 | 644 | beright1976 | beright1976 | `44d00c951573d3ebe42e7b64b515201be863986ca63d3baab753b54e61970bc0` |
| `vendor/lib64/libcamalgo.lsc.so` | 241592 | 644 | beright1976 | beright1976 | `f7d61d803f0cafd277e44940640ba7e91b591940b604a80b6a7a0b39058ced90` |
| `vendor/lib64/libcamalgo.mfnr.so` | 175888 | 644 | beright1976 | beright1976 | `42ffcb180146b9cdcc985de016fc5604e73e9a860a27020b02f19d7f6eac3983` |
| `vendor/lib64/libcamalgo.nr.so` | 322448 | 644 | beright1976 | beright1976 | `05c8d6e50cf799feecf64ad93421192d182ce2a26930c885680db829c3794cb5` |
| `vendor/lib64/libcamalgo.utility.so` | 89888 | 644 | beright1976 | beright1976 | `1ff7f531a10e052171926bcdfd12750ead3c890f92a56b7e396d43926ec0e2c7` |
| `vendor/lib64/libcamalgo.warp.so` | 57720 | 644 | beright1976 | beright1976 | `112964982d61c4fd92cce5ff34ab85974e8301e5cf4122864f350ac45bd9c6d1` |
| `vendor/lib64/libcamdrv_imem.so` | 48776 | 644 | beright1976 | beright1976 | `1267f51732defafd811cd6e0c0766499fadaf616c407bb5074f06df7b004f7b7` |
| `vendor/lib64/libcamdrv_isp.so` | 272704 | 644 | beright1976 | beright1976 | `06c4e56dc0afc356bbd4ee1b6485fb91e1abfd1aaf6b9c4bfe3c5e4073810ea0` |
| `vendor/lib64/libcamdrv_tuning_mgr.so` | 65616 | 644 | beright1976 | beright1976 | `ce573e612ba56240b69eb3ac43e59bf7a005d4dead8130a3988d526e6f0af898` |
| `vendor/lib64/libcamdrv_twin.so` | 355880 | 644 | beright1976 | beright1976 | `b9fe8a5551ff7e88e16ecb357b3e5963456c14445db36a207a0e1ba7274bfce1` |
| `vendor/lib64/libcameracustom.eis.so` | 41080 | 644 | beright1976 | beright1976 | `e50aae47d2eff38a990c3d336393a3dd8fa8d001149ce6266e1efeae6594b3a2` |
| `vendor/lib64/libcameracustom.plugin.so` | 24416 | 644 | beright1976 | beright1976 | `f7e7a72b30c3f9e18155317a4f31beaa838440515c9dbf4478f197ae16dad8af` |
| `vendor/lib64/libcameracustom.so` | 1403944 | 644 | beright1976 | beright1976 | `4be74085851d7d93115f6335d30d07696ea813c30f175045f1911da0e59819ad` |
| `vendor/lib64/libmtkcam.eventcallback.so` | 19720 | 644 | beright1976 | beright1976 | `b9796f3c6607b5e02c769cf0fa73c8215d1c8962b1e6d1875d7c9043f7d8ca95` |
| `vendor/lib64/libmtkcam.featurepipe.capture.so` | 1475520 | 644 | beright1976 | beright1976 | `2aeb2ad1ecab99bf77dfe582bd85b3da6c4326add9132aa372d796ab1346c9b5` |
| `vendor/lib64/libmtkcam.featurepipe.streaming.so` | 1732464 | 644 | beright1976 | beright1976 | `36569237b41552c319afc1d58c996154306c4876b962e93de674b4ceb634c2ba` |
| `vendor/lib64/libmtkcam.featurepipe.vsdof_util.so` | 87528 | 644 | beright1976 | beright1976 | `eaa130256813fbb525121b85fc5c9a3d429b439975d9ab4357ef2593b073b2c3` |
| `vendor/lib64/libmtkcam.logicalmodule.so` | 794936 | 644 | beright1976 | beright1976 | `45b492f46979c1eebf96126f5eadd74b6d26105abad3eb4138eb0044ed3d3de1` |
| `vendor/lib64/libmtkcam_3rdparty.core.so` | 396256 | 644 | beright1976 | beright1976 | `a1f75e51fc0d62cf974a311af0fd1fa72eb9314e66c43f8ee9a13985daf3c18f` |
| `vendor/lib64/libmtkcam_3rdparty.customer.so` | 54096 | 644 | beright1976 | beright1976 | `8e43af14cd9b6213053817f57d7a1f8a0e888903d119947567a22ed269ce3c98` |
| `vendor/lib64/libmtkcam_3rdparty.mtk.so` | 449144 | 644 | beright1976 | beright1976 | `d3d81a985043f55648c4ecc2ec4fcb1770a50c979dfeee157f8663f027c2f5b3` |
| `vendor/lib64/libmtkcam_3rdparty.so` | 15312 | 644 | beright1976 | beright1976 | `5d05a9a59a99fc8a6a6273a5957828e75b46cffa748d5b7155590afd1009bd2d` |
| `vendor/lib64/libmtkcam_calibration_convertor.so` | 15464 | 644 | beright1976 | beright1976 | `f075e8fd75b8489d6cb4f3a6afad85bb9766fbabb0f1b96594059c55a78c558d` |
| `vendor/lib64/libmtkcam_calibration_provider.so` | 168896 | 644 | beright1976 | beright1976 | `0fb1339d740a40379b9ac30973d140c742ce51841bb1fe8ac3b5cdc893b25232` |
| `vendor/lib64/libmtkcam_debugutils.so` | 88688 | 644 | beright1976 | beright1976 | `1838cfc7d50dfacf6da813a237e171e03ceedd35c9d0b328becf9c6679226484` |
| `vendor/lib64/libmtkcam_device3.so` | 266984 | 644 | beright1976 | beright1976 | `91f1228adbb2e659d9c7720ed6ad15be5cf461b32780dc18b8fc7fc26d640b35` |
| `vendor/lib64/libmtkcam_device3_app.so` | 601128 | 644 | beright1976 | beright1976 | `54c4e172e0ad4de3717569f0691408e7f484d190de5905d9d332a1d6bef62a55` |
| `vendor/lib64/libmtkcam_exif.so` | 74568 | 644 | beright1976 | beright1976 | `d43ab5dcdc7bda34a4e5f3fe8a95820d0a070b8c1f6cbc181d43927ff4200001` |
| `vendor/lib64/libmtkcam_fdvt.so` | 1273320 | 644 | beright1976 | beright1976 | `1be5c8517f861771799d04ad8f7bae947103fa16fa32cc81b7c5d22ffb9fc731` |
| `vendor/lib64/libmtkcam_featurepolicy.so` | 307264 | 644 | beright1976 | beright1976 | `45b16e8562299a3dfb948c8ee887552cc063ad9ef5eb6dc1f72a1da43d1574d7` |
| `vendor/lib64/libmtkcam_fwkutils.so` | 86200 | 644 | beright1976 | beright1976 | `a644f6a1aa6423f55d6f8ed2c29b7f652134dcedf1efef9c58a7b909c243cfe3` |
| `vendor/lib64/libmtkcam_grallocutils.so` | 59488 | 644 | beright1976 | beright1976 | `23c7f7dc074b4ca5eefe45558b33a1af819903b9382dd5d3b2e6878bc362043a` |
| `vendor/lib64/libmtkcam_hwnode.so` | 2416816 | 644 | beright1976 | beright1976 | `f161a1c67a9a3f70cd4d85c759941138c3960fd59ac5a9d41b3fabcd5ff7c86a` |
| `vendor/lib64/libmtkcam_hwutils.so` | 366608 | 644 | beright1976 | beright1976 | `8cc663c54c154a48bbbe7171345eec0ba4c2cb5f64b01117236d383e82f9824f` |
| `vendor/lib64/libmtkcam_imem.so` | 32264 | 644 | beright1976 | beright1976 | `23e2d1a1e6a10086f619cda800ccf81cb257f7678b84dc1a7460cb535f992089` |
| `vendor/lib64/libmtkcam_imgbuf.so` | 394368 | 644 | beright1976 | beright1976 | `2321ccf8b920c54489ac595e74297f8e1bdb53028cea498b619b0bce8c27507c` |
| `vendor/lib64/libmtkcam_metadata.so` | 190856 | 644 | beright1976 | beright1976 | `0665854eb756ecf6766ea7a68ba08513bd9b2b8051fe3669154b586bf3f498b4` |
| `vendor/lib64/libmtkcam_metastore.so` | 1145768 | 644 | beright1976 | beright1976 | `c05d1f6144eb558b13ac4d6a063bb5e2fad59e62044235ad5c75cac0e2d67bb1` |
| `vendor/lib64/libmtkcam_modulefactory_aaa.so` | 20064 | 644 | beright1976 | beright1976 | `4a8bcf89f31d9a0fd7330736fa5e061236897c7054b44d879c8add627c1baaf9` |
| `vendor/lib64/libmtkcam_modulefactory_custom.so` | 15640 | 644 | beright1976 | beright1976 | `1cbd7aa2a8e826688c3ab76eb9e745b0cb10daba66fc4746f59184eed06ccb86` |
| `vendor/lib64/libmtkcam_modulefactory_drv.so` | 15760 | 644 | beright1976 | beright1976 | `3e090c283a51ea3aff0748a841f420cda85f1d2f17d18c0e7bcb33db9c8dfd2c` |
| `vendor/lib64/libmtkcam_modulefactory_feature.so` | 15720 | 644 | beright1976 | beright1976 | `eaa2c0d3de8cee7d16bf9e6b139f834a92ae7df5f3b8dfdf6864bc0e4892af55` |
| `vendor/lib64/libmtkcam_modulefactory_utils.so` | 15656 | 644 | beright1976 | beright1976 | `d315c59e24ec83eb48e459d7951642767e47585adbddd5ac518d097e423d5814` |
| `vendor/lib64/libmtkcam_modulehelper.so` | 23672 | 644 | beright1976 | beright1976 | `2b6b44739f160839f96b951216c79ad279ffc30dd2314a8ff8d358eec296d9e5` |
| `vendor/lib64/libmtkcam_pipeline.so` | 565504 | 644 | beright1976 | beright1976 | `1c918ec2c3e946e2ba3d95789ff00141605ee191f6db9a9d0aa6e6146e5cd11b` |
| `vendor/lib64/libmtkcam_pipeline_fbm.so` | 127000 | 644 | beright1976 | beright1976 | `fdb1aab89d55b0576cec748e28d9f1cb60d332fdfd72b1a289f6d2e414e3a3b2` |
| `vendor/lib64/libmtkcam_pipelinemodel.so` | 72040 | 644 | beright1976 | beright1976 | `f9b3a26e1d0c8a89a2996d145c3f7a8d45bccb23bddd1e3ba84db510a22f87a1` |
| `vendor/lib64/libmtkcam_pipelinemodel_adapter.so` | 37200 | 644 | beright1976 | beright1976 | `3c6c4948b40516dc56c3311c1fff41ec421490ddd74e745dc86b76d00deb842a` |
| `vendor/lib64/libmtkcam_pipelinemodel_capture.so` | 71984 | 644 | beright1976 | beright1976 | `b7eff02da97676cb8544900535b3881a7c1db2e7ed0dc7072cb016ac47aefb66` |
| `vendor/lib64/libmtkcam_pipelinemodel_session.so` | 415040 | 644 | beright1976 | beright1976 | `7bc3464174329a1d2543f44a2fdb4462b3e998442ee35c43b48502c991e1ac17` |
| `vendor/lib64/libmtkcam_pipelinemodel_utils.so` | 282448 | 644 | beright1976 | beright1976 | `b2aa2ae756a2a093339df298443938d32add4581510d0202addbec97107be309` |
| `vendor/lib64/libmtkcam_pipelinemodel_zsl.so` | 155432 | 644 | beright1976 | beright1976 | `f4f34b75992c11ee5eff88e3f83d674fb7517b9ba5c424b94a302c5170505aa5` |
| `vendor/lib64/libmtkcam_pipelinepolicy-security.so` | 24576 | 644 | beright1976 | beright1976 | `46edb87c0545364441d014ae03ffb3d9f7d33537fa95a807236e52769493e806` |
| `vendor/lib64/libmtkcam_pipelinepolicy-smvr.so` | 71488 | 644 | beright1976 | beright1976 | `290880b9fe97817a02f981abae0641eb8c6558d4d22cac3e439e5ff16dcfa15e` |
| `vendor/lib64/libmtkcam_pipelinepolicy.so` | 543664 | 644 | beright1976 | beright1976 | `dbcf49dfb254f319c6f0ca8a8a864d9e0be33f77d8c3d3b6e6053c7d991a090a` |
| `vendor/lib64/libmtkcam_pipelinepolicy_factory.so` | 49472 | 644 | beright1976 | beright1976 | `3e4f19915c0b3b2b7775bc79313a06f7c6db5852687012c420d92eb5a11231c4` |
| `vendor/lib64/libmtkcam_prerelease.so` | 75424 | 644 | beright1976 | beright1976 | `042b4aaff2b8d13ce6aa7cb2468a21d80d28c77eef37c07bd4fb314e1388f33a` |
| `vendor/lib64/libmtkcam_rsc.so` | 49880 | 644 | beright1976 | beright1976 | `f8cffd54d96e602d230361173287eae6b02e0654ea79be44cedb821cb911ea3e` |
| `vendor/lib64/libmtkcam_stdutils.so` | 198672 | 644 | beright1976 | beright1976 | `34366eeec8cc4539bd4ee3ca1124d4af533ebeaac4bd41ea9c3fc8b072efc558` |
| `vendor/lib64/libmtkcam_streamutils.so` | 310960 | 644 | beright1976 | beright1976 | `a8342c5dfd9423fd31ae597be2f5c98e36a77ee908c7c068bdf9c1e6109f0752` |
| `vendor/lib64/libmtkcam_synchelper.so` | 91744 | 644 | beright1976 | beright1976 | `1a6af004716ce491b8780497f580fa98f9883c12c03a82a25fb251494c781df5` |
| `vendor/lib64/libmtkcam_sysutils.so` | 80480 | 644 | beright1976 | beright1976 | `00929213a9ef06be265c015313d4ece8c4844636eeaefd8f4c592c8a3a5b255a` |
| `vendor/lib64/libmtkcam_tuning_utils.so` | 82984 | 644 | beright1976 | beright1976 | `08bdd57d2a630b275282a87eddc33e4deb8c2f0721af2f58905620cbd93dee3f` |
| `vendor/lib64/libmtkcam_ulog.so` | 256328 | 644 | beright1976 | beright1976 | `352ca4b85160afe796ee952fc40a6a60b536bc06b62ac49a084c4596e06c8fcf` |
| `vendor/lib64/mtkcam/libmtkcam_streaminfo_plugin-p1stt.so` | 29328 | 644 | beright1976 | beright1976 | `2e5e3bfb2dccd0c2013a69558e51f90df92a2dbe7543d89fd654ee246fb18b9f` |
| `vendor/lib64/vendor.mediatek.hardware.camera.atms@1.0.so` | 79840 | 644 | beright1976 | beright1976 | `e32626f68219014dbf43b9c58efc8ebb16c024dc7604e7b0c25c1a0fb91793fe` |
| `vendor/lib64/vendor.mediatek.hardware.camera.bgservice@1.0.so` | 116976 | 644 | beright1976 | beright1976 | `c11f15962e21ed06afd9825fccc7d0c1578224efdbb690ef55071a5bfd1dba67` |
| `vendor/lib64/vendor.mediatek.hardware.camera.bgservice@1.1.so` | 130568 | 644 | beright1976 | beright1976 | `2de1a5a0213d2f9ace3e1208e9765a4a8082b08fbc8c209863ec825a62c3477e` |
| `vendor/lib64/vendor.mediatek.hardware.camera.frhandler@1.0.so` | 151408 | 644 | beright1976 | beright1976 | `116eff703219d7d558b82958f1fa250c22ad6b0002ee1c3adf76f82cf8e9abc1` |
| `vendor/lib64/vendor.mediatek.hardware.camera.lomoeffect@1.0.so` | 100744 | 644 | beright1976 | beright1976 | `f3916bfa8a351c043b2339829e792806f5a3d3802167a5bc02bb924f6021d221` |
| `vendor/lib64/vendor.mediatek.hardware.camera.postproc@1.0.so` | 79544 | 644 | beright1976 | beright1976 | `8029b1dd21520906ef9495a23b2b8685826bd908a348349e73b8f57afc8b2930` |
| `vendor/lib64/vendor.mediatek.hardware.camera.security@1.0.so` | 147288 | 644 | beright1976 | beright1976 | `58f0f479edbaabf87cc4a364bd6d30f8d452b6fcc05805b95304247cd2738347` |

################################################################################
# 06.22 — CAMERA BLOB CLASSIFICATION SUMMARY
################################################################################
| Blob family | Role | First-boot disposition | Notes |
|---|---|---|---|
| camerahalserver + provider impl | Android camera HAL service/provider | KEEP | Required for provider@2.4 internal/0 |
| vendor.mediatek.hardware.camera.* | MTK camera extension HALs | KEEP | bgservice/lomoeffect/postproc/frhandler/atms/security surfaces |
| GC2385 blobs | rear sensor tuning/index | KEEP | active rear camera |
| GC030A blobs | front sensor tuning/index | KEEP | active front camera |
| BF2553L blobs | lower main2 tuning/index | KEEP | loader-required lower path, not provider-enumerated |
| Sony PDAF libs/wrappers | loader scaffold | KEEP_FIRST_BOOT | not Sony physical hardware proof |
| lib3a.* / libcam.hal3a.* | AE/AF/AWB/flash/3A/CCU/NVRAM | KEEP | required by HAL3A stack |
| libcamalgo.* | MTK camera algorithms | KEEP | includes gyro algorithm dependency, not gyro hardware |
| libcamdrv_* | ISP/imem/tuning/twin driver userspace chain | KEEP | HAL and iopipe dependency chain |
| libcameracustom* | camera custom/sensor index loader | KEEP | directly names GC2385, GC030A, BF2553L, Sony PDAF wrappers |
| libmtkcam_* | MTK device3/pipeline/policy/framework chain | KEEP | core pipeline |
| 32-bit support subset | compat support | KEEP_FIRST_BOOT | do not strip before validation |

################################################################################
# 06.23 — ORIGINAL DATABASE RECONCILIATION LEDGER
################################################################################
| Prior wording / risk | Current Section 06 authority |
|---|---|
| BF2553L as third physical Android camera | Corrected: BF2553L is lower camera-main-two loader/I2C path, not Android provider-enumerated. Physical function open. |
| BF2553L as absent BSP leftover | Corrected: not a disposable leftover. It is loader-required and I2C6 topology is present. |
| BF2553L tuning blob as proof of physical camera | Corrected: tuning blob is loader/camera-stack evidence; physical endpoint remains open. |
| BF2553L as IR transmitter candidate | Corrected: BF2553L public class and local tuning indicate camera/image sensor class, not IR emitter. `irtx_ldo` owns the IR-transmitter-rail candidate. |
| Sony PDAF libs as physical Sony cameras | Corrected: loader scaffold only. |
| libcamalgo.gyro.so as gyroscope proof | Corrected: camera algorithm dependency only. No gyroscope hardware proof. |
| SENINF lane assignment guessed from sensor order | Corrected: exact sensor-to-SENINF assignment not exposed by tested stock interfaces. |
| Health/SpO2 optical path merged with camera | Corrected: Wiite/nRF-controlled health path, not camera provider/BF2553L correlated. |

################################################################################
# 06.24 — PHYSICAL OPTICAL APERTURE LEAD
################################################################################
A visible auxiliary optical aperture/window exists near the main camera cluster.
It is not automatically classified as BF2553L and is not automatically classified
as `irtx_ldo`.

Candidate mapping:
```text
1. mt-flash-led1 / MT6370 FLED path
   - proven active during flash/torch gate

2. BF2553L / camera_main_two
   - proven lower camera-main-two path
   - not runtime-correlated with rear/front/flash/health gates yet

3. irtx_ldo optical/IR endpoint
   - real 3.4 V IR-transmitter rail candidate
   - not observed enabled in flash or health/SpO2 gates

4. health optical emitter/sensor path under Wiite/nRF
   - runtime-confirmed through wiite_corp/nRF/IRQ 27
   - physical emitter/sensor package still needs board/visual mapping
```

Final physical aperture wording:
```text
A visible auxiliary optical aperture/window is physically visible adjacent to the
main camera cluster. This aperture is not classified as ambient light or proximity
hardware because ALS/PS are fake/simulated in SensorService. It is also not
promoted to BF2553L or irtx_ldo without endpoint proof. It is recorded as an
OPEN_PHYSICAL_MAPPING lead with strongest current candidates: MT6370 FLED1,
BF2553L/camera_main_two, irtx_ldo optical/IR endpoint, or Wiite/nRF health optical
package depending on board trace or controlled optical observation.
```

################################################################################
# 06.25 — ROM DISPOSITION TABLE
################################################################################
| Component / family | Action | Reason |
|---|---|---|
| `/vendor/bin/hw/camerahalserver` | KEEP | Camera HAL provider service binary |
| `camerahalserver.rc` | KEEP | Required init service definition |
| `android.hardware.camera.xml` | KEEP | Framework feature declaration for active camera hardware |
| provider@2.4 MTK impl | KEEP | Required provider implementation |
| MTK camera bgservice/lomoeffect libs | KEEP | Runtime HAL side surfaces |
| GC2385 tuning/index | KEEP | Active rear camera |
| GC030A tuning/index | KEEP | Active front camera |
| BF2553L tuning/index | KEEP_FIRST_BOOT | Loader-required lower main2 path; not disposable |
| Sony PDAF libs/wrappers | KEEP_FIRST_BOOT | libcameracustom dependency scaffold |
| libcamalgo.gyro.so | KEEP_FIRST_BOOT | Camera dependency; not gyro proof |
| lib3a / CCU stack | KEEP | AE/AF/AWB/flash/CCU/ISP behavior |
| libcamdrv_* | KEEP | ISP/imem/tuning userspace driver chain |
| libmtkcam_* pipeline/framework | KEEP | Core MTK camera pipeline |
| 32-bit camera support subset | KEEP_FIRST_BOOT | Do not strip until clean-ROM validation proves unused |
| health/SpO2 Wiite/nRF components | NOT_CAMERA_OWNED | Section 04 owns retain/replace decisions |
| irtx_ldo endpoint | OPEN | Do not implement as camera until mapped |

First-boot rule:
```text
For initial AOSP 11 bring-up, preserve the complete normalized camera blob set,
provider registration, camera device nodes, camera SELinux labels, camera init
service, and MTK camera linker namespace compatibility. Reduction happens only
after camera open/preview/capture/torch/front/back validation passes.
```

################################################################################
# 06.26 — VALIDATION COMMANDS FOR FUTURE REGRESSION
################################################################################
Provider/service:
```bash
adb shell 'su -c "lshal | grep -Ei "camera.provider|bgservice|lomoeffect"; getprop | grep -Ei "init.svc.camera|camera""'
adb shell 'su -c "dumpsys media.camera"'
```

Sensor identity:
```bash
adb shell 'su -c "cat /proc/driver/camera_info; cat /proc/driver/imgsensor_status_info 2>/dev/null"'
```

I2C topology:
```bash
adb shell 'su -c "for n in 2-000c 2-0010 2-001a 2-002d 2-0050 2-0052 4-0011 4-0033 4-0050 5-0034 6-000c 6-002d 6-0050; do echo --- $n ---; cat /sys/bus/i2c/devices/$n/name 2>/dev/null; readlink -f /sys/bus/i2c/devices/$n/driver 2>/dev/null; cat /sys/bus/i2c/devices/$n/uevent 2>/dev/null; done"'
```

SENINF/static platform:
```bash
adb shell 'su -c "for p in /sys/devices/platform/1a040000.kd_camera_hw1 /sys/devices/platform/1a040000.seninf1 /sys/devices/platform/1a041000.seninf2 /sys/devices/platform/1a042000.seninf3 /sys/devices/platform/1a043000.seninf4; do echo --- $p ---; cat $p/uevent 2>/dev/null; done"'
```

Flash/IR separation:
```bash
adb shell 'su -c "for p in /sys/class/flashlight/*; do echo --- $p ---; cat $p/name $p/mode $p/torch_brightness $p/strobe_brightness 2>/dev/null; done; for r in /sys/class/regulator/regulator.*; do name=$(cat $r/name 2>/dev/null); [ "$name" = "irtx_ldo" ] && echo $r && cat $r/microvolts $r/state $r/num_users; done"'
```

Process maps:
```bash
adb shell 'su -c "pid=$(pidof camerahalserver); echo PID=$pid; cat /proc/$pid/maps | grep -Ei "gc2385|gc030a|bf2553|SonyIMX|libcamalgo.gyro|libcameracustom|libcam.halsensor|libmtkcam|lib3a""'
```

################################################################################
# 06.27 — OPEN LEADS
################################################################################
Open leads preserved by this section:

1. BF2553L physical function.
   - I2C6 topology and loader path are proven.
   - Android provider enumeration is negative.
   - Rear/front/flash/health correlation did not activate BF2553L in tested paths.
   - Physical aperture candidate exists but is not mapped.

2. Exact sensor-to-SENINF assignment.
   - SENINF1-4 hardware blocks are proven.
   - GC2385/GC030A MIPI lane count and MCLK are proven.
   - Stock runtime interfaces tested do not expose exact mapping.

3. FLED2 physical mapping.
   - Node exists.
   - It did not trigger in the tested torch path.

4. irtx_ldo endpoint.
   - Regulator exists at 3.4 V and is named as IR-transmitter rail candidate.
   - It did not enable in tested torch or HRS/SpO2 gates.

5. Physical auxiliary aperture/window mapping.
   - Observation exists.
   - Candidate endpoints remain MT6370 FLED1, BF2553L/camera_main_two,
     irtx_ldo optical/IR endpoint, or Wiite/nRF health optical package.

################################################################################
# 06.28 — FINAL CLOSURE STATEMENT
################################################################################
Section 06 is closed as the canonical software camera authority for the C17S.
The Android-facing camera stack is two-camera: GC2385 rear/back and GC030A front.
The lower camera fabric includes BF2553L / camera_main_two as a loader-required
and I2C6-declared main2 path, but BF2553L is not Android-provider-enumerated and
its physical function remains open. SENINF1-4 are proven hardware blocks, but
exact sensor-to-SENINF assignment is not exposed by tested stock runtime
interfaces and is therefore not asserted. MT6370 FLED1 is the active tested
flash/torch path. FLED2 and irtx_ldo remain open physical endpoint leads.
Heart-rate and SpO2 are separated from camera and belong to the Wiite/nRF path.
The normalized camera blob universe and critical dependency chain are captured
and must be preserved for first clean-ROM boot.

################################################################################
# END SECTION 06 — CAMERA STACK — HAL, BLOBS, SENSOR IDENTITIES & CCU
################################################################################
