################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 04 — SENSOR STACK — SCP, nRF BRIDGE & ANDROID SENSORSERVICE
# 
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 04 — SENSOR / HEALTH / NRF BRIDGE AUTHORITY MAP
# Date        : 2026-06-15 live SensorService, HAL, sysfs, I2C, init, SELinux, package, ECO-mode, and IRQ integration pass
################################################################################


################################################################################
# SCOPE
################################################################################

This section documents the software-facing sensor stack for the C17S software
digital twin. It is the canonical authority section for Android SensorService
publication, MTK Sensor HAL runtime behavior, MTK virtual sensor misc devices,
SCP/tinysys sensor-overlay results, Wiite/nRF bridge exposure, PAR2822-adjacent
health paths, system UID health packages, stock ECO/Sports reduced-runtime
behavior, sensor-related init permission rules, and sensor-control SELinux /
ownership boundaries.

This section covers:

- SCP RTOS sensor overlay results for all five SCP sensor slots.
- MTK Sensor HAL:
    android.hardware.sensors@2.0-service-mediatek
    vendor.sensors-hal-2-0
    android.hardware.sensors@2.0::ISensors/default
- Android SensorService published sensors: handle, name, vendor, type,
  permission, wake behavior, active state, and active clients.
- MTK virtual sensor misc-device substrate:
    /dev/m_acc_misc
    /dev/m_hrs_misc
    /dev/m_baro_misc
    /dev/m_step_c_misc
    /dev/m_spo2_misc
    /dev/m_bprs_misc
    /dev/m_ebus_misc
    /dev/m_tilt_misc
- /sys/class/sensor control nodes and active/delay/batch/name/dev values.
- nRF52832 / Wiite bridge mapping:
    wiite_corp_ctrl
    wiite_con_ctrl
    I2C4 4-0050 wiite,cp
    I2C4 4-0033 mediatek,heartrate
    /dev/ttyS1
    WIITE_CORP IRQ activity
- Stock ECO / Sports reduced-runtime mode:
    Android runtime remains active
    ADB remains available
    Wiite/nRF/health/sensor substrate remains active
    Wi-Fi, Bluetooth, and cellular radio surfaces are reduced or powered off
- Fake/simulated sensors:
    fakeAls
    fakePs
    *_simu nodes
    BPRS simulation/control surface
- Health feature boundaries:
    heart rate
    SpO2 / blood oxygen
    step counter / pedometer
    sleep/health app ownership
    blood-pressure surface status
- Sensor HAL blob dependency chain and VNDK/HIDL linkage observed in process maps.
- SELinux labels, UID/GID ownership, and init permission mutation that expose
  the sensor and Wiite control plane to system UID software.
- Required-runtime, replace-owner, harden, and ghost/negative classifications
  for the software database.

This section does not own:

- Full APK decompilation of Wiite health apps. That belongs to Sections 12, 13,
  and 16.
- Full nRF UART command-map reverse engineering. That belongs to Section 13 and
  firmware/protocol follow-up.
- GNSS/GPS stack internals. GPS belongs to Section 09; this section only records
  that Google/location clients consume accelerometer and tilt.
- Camera orientation behavior beyond accelerometer registration evidence.
  Camera belongs to Section 06.
- SELinux policy minimization. Section 04 records labels and access surfaces;
  final policy minimization belongs to Section 14.
- Negative hardware master register. Section 04 records sensor-specific
  negatives; Section 17 consolidates software ghosts and Section 12 hardware
  negative records.
- Product strategy, distribution strategy, or full system design. Section 04 is
  a software database section. Only the final note records downstream relevance.

################################################################################
# SOURCE AUTHORITY
################################################################################

TIER 1 : Live rooted C17S runtime evidence
         - `dumpsys sensorservice` full dump.
         - `lshal` / `service list` / `getprop` sensor and Wiite checks.
         - `ps -A -Z` process state for sensor HAL and Wiite health/control apps.
         - `/proc/<sensor-hal-pid>/maps`, `/proc/<sensor-hal-pid>/fd`, and status.
         - `/dev/m_*_misc` char devices and open-file ownership.
         - `/sys/class/sensor/*` control nodes and readable values.
         - `/sys/class/misc/wiite_corp_ctrl/*` and `/sys/class/misc/wiite_con_ctrl/*`.
         - `/sys/bus/i2c/devices/*` live I2C device map.
         - `/dev/ttyS1`, tty sysfs, and open UART file descriptors.
         - `/proc/bus/input/devices` and `/dev/input` negative input-sensor proof.
         - `dmesg` filtered Wiite/SCP/sensor traces.
         - `dumpsys package` / `pm path` for Wiite health/control packages.
         - live init/uevent/SELinux rule grep for sensor and Wiite permission authority.

TIER 2 : Clean extracted filesystem / prior completed software sections
         - Section 00 provides partition/source/current-state boundary.
         - Section 01 provides init RC, permission mutation, service graph, and ttyS1 authority.
         - Section 02 provides VINTF/HAL registration and sensor HAL boundary authority.
         - Section 03 provides namespace and VNDK separation rules upstream of blob usage.

TIER 3 : Static and hardware database cross-reference
         - Hardware Section 7 / C17S hardware database provides nRF52832, PAR2822,
           I2C4, SCP, Wiite control-plane, and absent-sensor authority.
         - Original software forensics database provides SCP overlay transcript,
           sensor truth table, and prior sensor topology claims resolved here.
         - Static reverse engineering is used only where explicitly stated; this
           section does not promote static strings to active runtime without live proof.

FINAL  : Beright / Albert live correction gate
         - Human-observed stock feature set:
             heart rate visible
             SpO2 / blood oxygen visible
             step counter visible
             accelerometer/tilt behavior visible or implied by runtime
             GPS visible but belongs to Section 09
             blood-pressure feature not observed and likely external/simulation


################################################################################
# RULES
################################################################################

No stale values.
No borrowed values from similar MTK devices.
No software declaration promoted to physical hardware truth.
No SensorService publication promoted to physical IC identity.
No sysfs node promoted to active measurement without runtime/client/source proof.
No HAL-opened node promoted to public SensorService publication unless SensorService proves it.
No Wiite/nRF app ownership promoted to required hardware ownership unless a transport dependency proves it.
No missing SensorService entry may be used to delete a proven lower-layer HAL/kernel device node.
No zero idle value may be used as proof of hardware absence.
No BP/BPRS software surface may be promoted to onboard physical blood-pressure hardware without physical evidence.
Layer-specific truths remain layer-specific.

Classification vocabulary used in this section:

[VERIFIED]                         Proven by live output, completed upstream section, or hardware database cross-reference.
[ACTIVE_RUNTIME]                   Running, registered, opened, mapped, producing sysfs output, or otherwise live.
[REGISTERED_HAL]                   Present in live HIDL/HAL registration.
[BINDER_SERVICE]                   Present in framework Binder/service list.
[FRAMEWORK_PUBLISHED]              Published by Android SensorService.
[ACTIVE_SENSOR]                    Active in SensorService at capture time.
[ACTIVE_CLIENT]                    Live client connected to SensorService.
[PROCESS_ACTIVE]                   Live process shown in `ps -A -Z`.
[ACTIVE_HAL_DEVICE_NODE]           The Sensor HAL holds an open FD to the char device.
[KERNEL_CLASS_PRESENT]             Present under `/sys/class/sensor`.
[MISC_DEVICE_PRESENT]              Present as a `/dev/m_*_misc` char device.
[I2C_PRESENT]                      Present under `/sys/bus/i2c/devices`.
[DRIVER_BOUND]                     I2C/platform node has a driver symlink.
[NO_DRIVER_SYMLINK]                Device node present but no driver symlink observed.
[WIITE_CONTROL_SURFACE]            Exposed through Wiite sysfs/control nodes.
[UART_BRIDGE]                      Uses or exposes `/dev/ttyS1`.
[ODM_UID1000_OWNER]                Owned or used by UID 1000 Wiite/ODM package.
[PRIVILEGED_PRODUCT_APP]           Product priv-app/system app with privileged permission grant.
[SIMULATION_SURFACE]               Simulation/write/control node exists.
[USER_VISIBLE_FUNCTION_CONFIRMED]  User confirmed visible stock feature or normal UI feature.
[NOT_STANDARD_SENSORSERVICE_TYPE]  Not present as a standard SensorService type in captured dump.
[PHYSICAL_SOURCE_CONFIRMED]        Physical path/IC is confirmed by hardware database.
[PHYSICAL_SOURCE_UNRESOLVED]       Software path exists; physical IC/source remains unresolved.
[BSP_GHOST]                        Declared or referenced but not active/physical.
[NEGATIVE]                         Confirmed absent for this device layer.
[OPEN_PROTOCOL_LEAD]               Live topology is known; protocol/register semantics remain undecoded.
[KEEP]                             Required runtime surface for confirmed stock function.
[REPLACE]                          Stock owner/control-plane should not remain authoritative.
[STRIP]                            Ghost, stub, or non-required stock surface after dependency proof.
[HARDEN]                           Present surface has unsafe permission or overbroad policy.
[MOVE_TO_SECTION_XX]               Belongs primarily to another section.


################################################################################
# 04.0 — CAPTURE BOUNDARY
################################################################################

Current live capture state:

  Device family                  : C17S / LOKMAT APPLLP 5 MAX
  Board                          : WP_C17S_PIX_TFT_D4
  Physical silicon family         : MT6762V/WB layer
  Vendor software/BSP layer       : MT6765 layer
  Android base                    : stock Android 10 / API 29 vendor stack
  Bootloader / research state      : unlocked / research state
  Root                            : available through su/Magisk during capture
  SELinux                         : permissive research state at capture
  Runtime authority               : live rooted device for active topology
  Static authority                : extracted stock filesystem and prior closed sections
  Database boundary               : software-facing sensor / health / nRF / ECO-mode facts

Interpretation:

  The current research state is valid for live topology, open file descriptors,
  node presence, service state, package ownership, HAL registration, sysfs
  topology, and stock reduced-runtime behavior. Stock-lock/enforcing state must
  not be inferred from permissive research mode unless stock-captured evidence
  already exists. This section records software database facts. Downstream
  consequences are confined to the final note.

################################################################################
# 04.1 — COMPLETION MATRIX
################################################################################

| Scope item | Closure state | Evidence class | Section result |
|---|---|---|---|
| SCP RTOS sensor overlay results — all 5 slots | CLOSED | SCP log / hardware DB | LIS2HH12 absent, AKM09918 absent, fakeAls loaded, BMP280 absent on SCP side, fakePs active. |
| MTK sensor HAL | CLOSED | live lshal/service/ps/maps/fd | `android.hardware.sensors@2.0-service-mediatek` is active, registered, maps `sensors.mt6765.so` and `libksensor.so`, opens `/dev/m_*_misc`. |
| Android SensorService full list | CLOSED | full `dumpsys sensorservice` | 10 h/w sensors, all vendor label MTK, full handle/type/vendor/version list recorded. |
| nRF52832 bridge mapping | CLOSED WITH PROTOCOL LEADS | sysfs/I2C/tty/process evidence | I2C4 `4-0050` wiite,cp driver-bound; wiite_corp_ctrl and wiite_con_ctrl exposed; ttyS1 owned by DeviceDaemon communication process. |
| fakeAls/fakePs | CLOSED | SCP overlay + SensorService/light behavior | fakeAls/fakePs are not physical ICs; LIGHT channel is framework-published but suspicious/encoded/Wiite-owned. |
| accelerometer path | CLOSED | SensorService + HAL misc node + sysfs | Framework published; active; HAL opens `/dev/m_acc_misc`; class node exists. |
| heart rate path | CLOSED | SensorService + HAL misc node + I2C4 heartrate | Framework published as HEART_RATE; HAL opens `/dev/m_hrs_misc`; user-visible; I2C4 `4-0033` present. |
| SpO2 path | CLOSED WITH PROTOCOL LEAD | user observation + HAL misc node + sysfs name | User-visible; HAL opens `/dev/m_spo2_misc`; class node name `nrf_ctrl_spo2`; not a standard SensorService type in capture. |
| blood pressure | CLOSED AS UNCONFIRMED PHYSICAL | HAL misc node + sysfs name + user correction | BPRS surface exists as `nrf_ctrl_bprs`; physical onboard sensor not confirmed; likely simulation/external/ODM placeholder. |
| barometer/pressure | CLOSED WITH SOURCE UNRESOLVED | SensorService + HAL node + sysfs + SCP negative | SensorService PRESSURE exists; `/dev/m_baro_misc`; BMP280 absent on SCP side; physical source unresolved. |
| gyroscope | CLOSED NEGATIVE | SensorService fusion disabled + hardware DB | No published gyro; no physical gyro proof; classify negative/BSP ghost if strings exist. |
| magnetometer/compass | CLOSED NEGATIVE | SCP AKM09918 ACK error + no SensorService mag | No confirmed physical magnetometer/compass. |
| ALS/proximity | CLOSED AS FAKE/BRIDGE | SCP fakeAls/fakePs + SensorService light behavior | No standalone ALS/prox IC; LIGHT likely bridge/event channel in this stock image. |
| step counter | CLOSED | SensorService + active Wiite client + HAL node | Published, active, Wiite pedometer client, `/dev/m_step_c_misc`. |
| HAL blob dependency chain | CLOSED FOR ACTIVE PATH | process maps | service binary, `sensors.mt6765.so`, `libksensor.so`, HIDL/VNDK sensor libs mapped. |
| stock ECO / Sports reduced-runtime mode | CLOSED FOR DATABASE PURPOSE | live ECO-mode checks | Android/ADB remain active; sensor/health/Wiite substrate remains active; Wi-Fi, Bluetooth, and cellular radio are reduced/powered off; ECO parity is not a Section 04 functional requirement. |
| init permission authority | CLOSED | live grep init RC | `init.sensor_1_0.rc` owns `/dev/m_*` and `/sys/class/sensor`; `init.mt6762/mt6765.rc` owns Wiite sysfs and ttyS1. |
| SELinux/process labels | CLOSED | live ps/ls -Z | `mtk_hal_sensors`, `system_app`, sensor device labels, `sysfs_sensor`, and ttyS labels recorded. |
| software disposition table | CLOSED | integrated analysis | Required runtime surfaces, replace-owner surfaces, hardening surfaces, ghost surfaces, and open protocol leads are separated. |


################################################################################
# 04.2 — HIGH-LEVEL ARCHITECTURE
################################################################################

The C17S sensor stack is not a single ordinary MTK phone sensor stack. It is a
hybrid wearable stack with four distinct software/hardware-facing planes:

  1. Android framework publication plane
     - SensorService publishes Android-visible sensors.
     - Apps see `ACCELEROMETER`, `LIGHT`, `PRESSURE`, `AMBIENT_TEMPERATURE`,
       `STEP_DETECTOR`, `STEP_COUNTER`, `HEART_RATE`, `TILT_DETECTOR`,
       `HEART_BEAT`, and `STEP_DETECTOR_WAKEUP`.

  2. MTK Sensor HAL / vendor blob plane
     - `android.hardware.sensors@2.0-service-mediatek`
     - `vendor.sensors-hal-2-0`
     - `/vendor/lib64/hw/sensors.mt6765.so`
     - `/vendor/lib64/libksensor.so`
     - HIDL/VNDK sensor interfaces
     - `/dev/m_*_misc` virtual sensor nodes.

  3. Wiite / nRF control plane
     - `/sys/class/misc/wiite_corp_ctrl`
     - `/sys/class/misc/wiite_con_ctrl`
     - I2C4 `4-0050` compatible `wiite,cp`, driver `wiite_corp`
     - `/dev/ttyS1` UART bridge
     - Wiite UID1000 system apps and health apps
     - WIITE_CORP IRQ activity.

  4. PAR2822 / health proxy plane
     - I2C4 `4-0033`, name `heartrate`, compatible `mediatek,heartrate`
     - no driver symlink observed in live scan
     - heart-rate and SpO2 user-visible functions
     - health app ownership under UID1000
     - protocol and exact frame semantics remain open.

The correct model is therefore hybrid:

  - MTK Sensor HAL publishes framework sensors and opens MTK virtual sensor
    misc nodes.
  - nRF/PAR/Wiite bridge owns or influences wearable health/control paths.
  - Some framework sensors are genuine user-visible functions.
  - Some framework or sysfs surfaces are bridge/event/simulation surfaces.
  - Some BSP-declared sensor identities are ghosts and must not be promoted to
    physical hardware.

Do not collapse every Android-published sensor into nRF ownership. Do not
collapse every nRF/sysfs health surface into SensorService publication.


################################################################################
# 04.3 — RUNTIME HAL / HIDL / BINDER SURFACE
################################################################################

Live HIDL registration:

| Interface | Instance | Status | Classification |
|---|---:|---|---|
| `android.frameworks.sensorservice@1.0::ISensorManager/default` | default | registered | [REGISTERED_HAL] [FRAMEWORK_SENSOR_MANAGER] |
| `android.hardware.sensors@2.0::ISensors/default` | default | registered | [REGISTERED_HAL] [ACTIVE_RUNTIME] [SECTION04_PRIMARY] |

Framework Binder services:

| Binder service | Interface | Status | Classification |
|---|---|---|---|
| `sensorservice` | `android.gui.SensorServer` | present | [BINDER_SERVICE] [ACTIVE_RUNTIME] |
| `sensor_privacy` | framework service | present | [BINDER_SERVICE] [ACTIVE_RUNTIME] |

Service state:

| Init service | Binary | Runtime state | Classification |
|---|---|---|---|
| `vendor.sensors-hal-2-0` | `/vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek` | running | [ACTIVE_RUNTIME] [REGISTERED_HAL] |

Process identity:

| Field | Value |
|---|---|
| PID at capture | `694` |
| Process name | `android.hardware.sensors@2.0-service-mediatek` |
| Short status name | `sensors@2.0-ser` |
| UID / GID | `1000 / 1000` (`system`) |
| SELinux domain | `u:r:mtk_hal_sensors:s0` |
| Threads | `3` |
| Command line | `/vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek` |
| Seccomp | `0` in research capture |
| Runtime classification | [ACTIVE_RUNTIME] [REGISTERED_HAL] [MTK_SENSOR_HAL] [SYSTEM_UID_HAL] |

Database interpretation:

  The MTK Sensor HAL is the framework-facing service that SensorService depends on.
  It is an active database dependency for the stock sensor stack. Wiite apps are
  separate control-plane owners and must not be conflated with HAL registration.


################################################################################
# 04.4 — SENSOR HAL PROCESS MAP / BLOB CHAIN
################################################################################

The active Sensor HAL maps the following sensor-relevant files:

| Runtime mapped file | Role | Classification |
|---|---|---|
| `/vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek` | binderized HAL service executable | [ACTIVE_RUNTIME] [REGISTERED_HAL] |
| `/vendor/lib64/hw/sensors.mt6765.so` | MTK sensor HAL implementation library | [ACTIVE_RUNTIME] [HAL_IMPL] [KEEP] |
| `/vendor/lib64/libksensor.so` | MTK/vendor sensor helper/calibration/math library | [ACTIVE_RUNTIME] [HAL_DEPENDENCY] [KEEP] |
| `/system/lib64/vndk-29/android.hardware.sensors@2.0.so` | HIDL sensor interface library | [ACTIVE_RUNTIME] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.sensors@1.0.so` | legacy HIDL sensor interface dependency | [ACTIVE_RUNTIME] [VNDK_29] |
| `/dev/hwbinder` | HIDL binder transport | [ACTIVE_RUNTIME] [IPC] |
| `/system/lib64/vndk-29/libbinder.so` | binder runtime | [ACTIVE_RUNTIME] [VNDK_29] |
| `/system/lib64/vndk-sp-29/libhidlbase.so` | HIDL base | [ACTIVE_RUNTIME] [VNDK_SP_29] |
| `/system/lib64/vndk-sp-29/libhwbinder.so` | HIDL binder transport | [ACTIVE_RUNTIME] [VNDK_SP_29] |
| `/system/lib64/vndk-29/libfmq.so` | Fast Message Queue dependency | [ACTIVE_RUNTIME] [VNDK_29] |
| `/system/lib64/vndk-sp-29/libhardware.so` | hardware module support | [ACTIVE_RUNTIME] [VNDK_SP_29] |

Runtime dependency rule:

  `sensors.mt6765.so` and `libksensor.so` are active runtime dependencies for
  the Sensor HAL. They are not optional residue and cannot be demoted to unused
  BSP files while the stock HAL is the observed SensorService provider.

Namespace rule:

  Do not isolate or patch these libraries blindly. They are normal VNDK/vendor
  HAL consumers. Any namespace/RPATH work must be based on actual linker failure,
  not on TWRP recovery-specific isolation behavior.


################################################################################
# 04.5 — ANDROID SENSORSERVICE PUBLISHED INVENTORY
################################################################################

Full SensorService state at capture:

  Total hardware sensors reported : 10
  Running sensors                 : 10
  Fusion state                    : 9-axis fusion disabled
                                    game fusion disabled
                                    geomag fusion disabled
  Sensor privacy                  : disabled
  Mode                            : NORMAL
  Wake lock                       : not held
  Direct connections              : 0

Published SensorService list:

| Handle | Name | Vendor | Ver | Android type | Permission | Mode / flags | Section classification |
|---:|---|---|---:|---|---|---|---|
| `0x00000001` | `ACCELEROMETER` | `MTK` | 1 | `android.sensor.accelerometer(1)` | n/a | continuous, non-wake | [FRAMEWORK_PUBLISHED] [ACTIVE_SENSOR] |
| `0x00000005` | `LIGHT` | `MTK` | 1 | `android.sensor.light(5)` | n/a | on-change, non-wake | [FRAMEWORK_PUBLISHED] [WIITE_EVENT_CHANNEL_CANDIDATE] |
| `0x00000006` | `PRESSURE` | `MTK` | 1 | `android.sensor.pressure(6)` | n/a | continuous, non-wake | [FRAMEWORK_PUBLISHED] [PHYSICAL_SOURCE_UNRESOLVED] |
| `0x0000000d` | `AMBIENT_TEMPERATURE` | `MTK` | 1 | `android.sensor.ambient_temperature(13)` | n/a | on-change, non-wake | [FRAMEWORK_PUBLISHED] [PHYSICAL_SOURCE_UNRESOLVED] |
| `0x00000012` | `STEP_DETECTOR` | `MTK` | 1 | `android.sensor.step_detector(18)` | `ACTIVITY_RECOGNITION` | special-trigger, non-wake | [FRAMEWORK_PUBLISHED] |
| `0x00000013` | `STEP_COUNTER` | `MTK` | 1 | `android.sensor.step_counter(19)` | `ACTIVITY_RECOGNITION` | on-change, non-wake | [FRAMEWORK_PUBLISHED] [ACTIVE_SENSOR] |
| `0x00000015` | `HEART_RATE` | `MTK` | 1 | `android.sensor.heart_rate(21)` | `BODY_SENSORS` | on-change, non-wake | [FRAMEWORK_PUBLISHED] [USER_VISIBLE_FUNCTION_CONFIRMED] |
| `0x00000016` | `TILT_DETECTOR` | `MTK` | 1 | `android.sensor.tilt_detector(22)` | n/a | special-trigger, wakeUp | [FRAMEWORK_PUBLISHED] [ACTIVE_SENSOR] |
| `0x0000001f` | `HEART_BEAT` | `MTK` | 1 | `android.sensor.heart_beat(31)` | n/a | special-trigger, non-wake | [FRAMEWORK_PUBLISHED] [HEALTH_EVENT_SURFACE] |
| `0x00000065` | `STEP_DETECTOR_WAKEUP` | `MTK` | 1 | `android.sensor.step_detector(18)` | `ACTIVITY_RECOGNITION` | special-trigger, wakeUp | [FRAMEWORK_PUBLISHED] |

SensorService non-public health boundary:

  SpO2 is not published as a standard SensorService type in this dump. This does
  not mean SpO2 is absent. Later HAL/sysfs evidence proves `/dev/m_spo2_misc`,
  `/sys/class/sensor/m_spo2_misc`, and `nrf_ctrl_spo2`. User observation also
  confirms SpO2 readings are visible on the stock device.

Fusion interpretation:

  Gyro-based 9-axis and game fusion are disabled in SensorService. No physical
  gyroscope is proven. Magnetometer/geomagnetic fusion is also disabled, and the
  AKM09918 SCP slot failed ACK. This supports negative/ghost classification for
  gyroscope and magnetometer, but the absence conclusion is grounded in combined
  hardware/SCP/runtime evidence, not SensorService alone.


################################################################################
# 04.6 — SENSORSERVICE ACTIVE CLIENTS
################################################################################

Active sensors at capture:

| Sensor | Handle | Connections | Active client |
|---|---:|---:|---|
| `ACCELEROMETER` | `0x00000001` | 1 | Google location BatchSignalCollector |
| `LIGHT` | `0x00000005` | 1 | `com.wiite.devicedaemon.CommunicationServices` |
| `STEP_COUNTER` | `0x00000013` | 1 | `com.wiite.wearhealthuart.service.PedometerService` |
| `TILT_DETECTOR` | `0x00000016` | 1 | Google location RealWakeUpTiltDetector |

Active connection table:

| Connection | Package / component | UID | Sensor | Interpretation |
|---:|---|---:|---|---|
| 0 | `com.wiite.wearhealthuart.service.PedometerService` | 1000 | `STEP_COUNTER` | UID1000 Wiite pedometer client. |
| 1 | `com.wiite.devicedaemon.CommunicationServices` | 1000 | `LIGHT` | UID1000 Wiite control/event client; suspicious non-ALS usage. |
| 2 | `com.google.android.location.collectionlib.BatchSignalCollector$BatchSensorEventListener` | 10046 | `ACCELEROMETER` | Google/location motion collection client. |
| 3 | `com.google.android.location.os.real.RealWakeUpTiltDetector$MySensorEventListener` | 10046 | `TILT_DETECTOR` | Google/location wake tilt detector. |

Previous registrations:

  Accelerometer registrations include `hidl_client_pid_*`, `com.android.camera.PhotoModule`,
  `android.view.OrientationEventListener`, and Google location SensorScanner /
  BatchSignalCollector / SensorTimestampCalibrator entries.

Interpretation:

  The input subsystem is not the delivery path for wearable sensors. SensorService
  and the MTK Sensor HAL deliver the framework path. Wiite UID1000 packages are
  active clients/owners for pedometer and LIGHT/event behavior. Google location
  uses accelerometer and tilt. Camera/orientation uses accelerometer.


################################################################################
# 04.7 — MTK VIRTUAL SENSOR MISC-DEVICE SUBSTRATE
################################################################################

The active Sensor HAL holds open file descriptors to eight `/dev/m_*_misc` char
devices. This is a core Section 04 finding. These nodes are not decorative BSP
residue; they are opened by the live Sensor HAL process.

| Device node | Major/minor | Hex major/minor | SELinux label | HAL FD | Opener | Classification |
|---|---:|---:|---|---:|---|---|
| `/dev/m_acc_misc` | `251:0` | `fb:0` | `u:object_r:m_acc_misc_device:s0` | `6r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [MISC_DEVICE_PRESENT] |
| `/dev/m_hrs_misc` | `251:20` | `fb:14` | `u:object_r:m_hrs_misc_device:s0` | `7r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [HEALTH] |
| `/dev/m_baro_misc` | `251:5` | `fb:5` | `u:object_r:m_baro_misc_device:s0` | `8r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [PRESSURE] |
| `/dev/m_step_c_misc` | `251:18` | `fb:12` | `u:object_r:m_step_c_misc_device:s0` | `9r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [STEP_COUNTER] |
| `/dev/m_spo2_misc` | `251:30` | `fb:1e` | `u:object_r:m_hrs_misc_device:s0` | `10r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [SPO2] |
| `/dev/m_bprs_misc` | `251:12` | `fb:c` | `u:object_r:m_hrs_misc_device:s0` | `11r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [BPRS_SURFACE] |
| `/dev/m_ebus_misc` | `251:4` | `fb:4` | `u:object_r:m_hrs_misc_device:s0` | `12r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [EVENT_BUS] |
| `/dev/m_tilt_misc` | `251:21` | `fb:15` | `u:object_r:m_tilt_misc_device:s0` | `15r` | `sensors@2.0-ser` PID 694 | [ACTIVE_HAL_DEVICE_NODE] [TILT] |

Ownership:

  All nodes are `crw-rw---- system:system` except label class differences.
  HAL access is by `system` UID inside `u:r:mtk_hal_sensors:s0`.

Critical interpretation:

  The existence of `/dev/m_spo2_misc`, `/dev/m_bprs_misc`, and `/dev/m_ebus_misc`
  proves lower-layer HAL/kernel surfaces that do not map one-to-one to standard
  SensorService public sensor types. SpO2 is lower-layer active even though not
  a standard SensorService type in the captured dump. BPRS is lower-layer active
  as a software/HAL surface but not proven as an onboard physical sensor.

Database rule:

  Do not classify any `/dev/m_*_misc` node as removable merely because it is
  not a public Android sensor type. HAL-opened kernel nodes are active database
  facts and must remain in the required-runtime set until dependency tests prove
  otherwise.


################################################################################
# 04.8 — /SYS/CLASS/SENSOR RUNTIME MAP
################################################################################

The kernel exposes a virtual sensor class for the MTK sensor misc substrate:

| Class node | Runtime target | dev | Name/active values | Classification |
|---|---|---:|---|---|
| `/sys/class/sensor/m_acc_misc` | `/sys/devices/virtual/sensor/m_acc_misc` | `251:0` | `accactive=1000`, `accbatch=0`, `acccali=0`, `accdevnum=0`, `accflush=0` | [KERNEL_CLASS_PRESENT] [ACCELEROMETER] |
| `/sys/class/sensor/m_baro_misc` | `/sys/devices/virtual/sensor/m_baro_misc` | `251:5` | `baroactive=100000`, `barobatch=0`, `barodevnum=0`, `baroflush=0` | [KERNEL_CLASS_PRESENT] [PRESSURE] |
| `/sys/class/sensor/m_bprs_misc` | `/sys/devices/virtual/sensor/m_bprs_misc` | `251:12` | `bprs_active=0`, `bprs_batch=0`, `bprs_flush=0`, `bprs_name=nrf_ctrl_bprs` | [KERNEL_CLASS_PRESENT] [BPRS_SURFACE] |
| `/sys/class/sensor/m_ebus_misc` | `/sys/devices/virtual/sensor/m_ebus_misc` | `251:4` | `ebus_active=1`, `ebus_batch=0`, `ebus_devnum=0`, `ebus_flush=0`, `ebus_simu=permission denied` | [KERNEL_CLASS_PRESENT] [EVENT_BUS] |
| `/sys/class/sensor/m_hrs_misc` | `/sys/devices/virtual/sensor/m_hrs_misc` | `251:20` | `hrs_active=0`, `hrs_batch=0`, `hrs_flush=0`, `hrs_name=nrf_ctrl_hrs` | [KERNEL_CLASS_PRESENT] [HEART_RATE] |
| `/sys/class/sensor/m_spo2_misc` | `/sys/devices/virtual/sensor/m_spo2_misc` | `251:30` | `spo2_active=0`, `spo2_batch=0`, `spo2_flush=0`, `spo2_name=nrf_ctrl_spo2` | [KERNEL_CLASS_PRESENT] [SPO2] |
| `/sys/class/sensor/m_step_c_misc` | `/sys/devices/virtual/sensor/m_step_c_misc` | `251:18` | `step_cactive=1000`, `step_cbatch=0`, `step_cdevnum=0`, `step_cflush=0` | [KERNEL_CLASS_PRESENT] [STEP_COUNTER] |
| `/sys/class/sensor/m_tilt_misc` | `/sys/devices/virtual/sensor/m_tilt_misc` | `251:21` | `tilt_active=0`, `tilt_batch=0`, `tilt_devnum=0`, `tilt_flush=0`, `tilt_simu=permission denied` | [KERNEL_CLASS_PRESENT] [TILT] |

All class nodes are under `u:object_r:sysfs_sensor:s0` for contained files
where captured. Control entries are primarily `system:system 0660` or root-only
where write-only simulation hooks exist.

Value interpretation:

  `*_active=0` does not prove absence. HRS, SpO2, BPRS, and Tilt were idle at
  the specific sysfs capture moment but the class nodes and HAL open FDs were
  present. The correct classification is present/active substrate with
  capture-state idle value.

  `nrf_ctrl_hrs`, `nrf_ctrl_spo2`, and `nrf_ctrl_bprs` are important names.
  They bind the health surfaces to an nRF-control naming model at the kernel
  class/HAL layer.


################################################################################
# 04.9 — SCP / TINYSYS SENSOR OVERLAY RESULTS
################################################################################

SCP RTOS and firmware boundary:

| Item | Value | Classification |
|---|---|---|
| SCP RTOS | FreeRTOS | [VERIFIED] [SCP_DOMAIN] |
| Boot timing | under 1 ms in SCP transcript | [VERIFIED] |
| Firmware memory `ap_loader_start` | `0x9f900000` | [VERIFIED] |
| Firmware size | `0x3b800` = 244,736 bytes | [VERIFIED] |
| MPEKlib version | `MPE_CAL_A_VER_18082801` | [VERIFIED] |

SCP overlay sensor slots:

| Slot | BSP sensor identity | Result | Section classification |
|---|---|---|---|
| `sec01` | `lis2hh12` accelerometer | NOT PRESENT / ACK error | [BSP_GHOST] [SCP_NEGATIVE] |
| `sec02` | `akm09918` magnetometer | NOT PRESENT / ACK error | [BSP_GHOST] [NEGATIVE] |
| `sec03` | `cm36558` ALS / fakeAls | fakeAls LOADED; no physical ALS | [SIMULATION_SURFACE] [NO_PHYSICAL_ALS] |
| `sec04` | `bmp280` barometer | NOT PRESENT on SCP side | [BSP_GHOST] [SCP_NEGATIVE] |
| `sec05` | fakePs proximity | ACTIVE / fakePs auto detect success | [SIMULATION_SURFACE] [NO_PHYSICAL_PROXIMITY] |

SCP I2C probe result:

  Probed SCP-side addresses:
    `0x51`, `0x77`, `0xEE`, `0x0C`

  Result:
    all ACK errors in SCP evidence.

Interpretation:

  SCP/tinysys exists and must be preserved, but the named SCP-side sensor ICs
  are not physical confirmed devices on this board. The Android framework
  still publishes pressure, temperature, accelerometer, step, heart, tilt, and
  related sensors through the MTK HAL and Wiite/nRF-adjacent stack. Therefore:

  - LIS2HH12 cannot be treated as the real accelerometer.
  - AKM09918 cannot be treated as a real compass/magnetometer.
  - CM36558 cannot be treated as a real ALS.
  - BMP280 cannot be treated as the real barometer on the SCP side.
  - fakePs is a software stub only.

Database rule:

  SCP/tinysys firmware and kernel interfaces are required-runtime surfaces.
  Ghost physical sensor identities remain negative records unless loader proof
  requires a stub declaration. Do not classify SCP as unused just because
  SCP-side named sensor ICs are absent; SCP is a platform firmware/interconnect
  domain.


################################################################################
# 04.10 — I2C SENSOR / HEALTH / WIITE TOPOLOGY
################################################################################

Relevant live I2C4 nodes:

| Bus/address | Name | Runtime path | Modalias | Compatible | Driver state | Classification |
|---|---|---|---|---|---|---|
| `4-0011` | `ccu_sensor_i2c_4_hw` | `/sys/devices/platform/11011000.i2c4/i2c-4/4-0011` | `i2c:ccu_sensor_i2c_4_hw` | `mediatek,ccu_sensor_i2c_4_hw` | driver `ccu_i2c_4_hwtrg` | [I2C_PRESENT] [DRIVER_BOUND] |
| `4-0033` | `heartrate` | `/sys/devices/platform/11011000.i2c4/i2c-4/4-0033` | `i2c:heartrate` | `mediatek,heartrate` | no driver symlink observed | [I2C_PRESENT] [NO_DRIVER_SYMLINK] [HEALTH_PROXY] |
| `4-0050` | `cp` | `/sys/devices/platform/11011000.i2c4/i2c-4/4-0050` | `i2c:cp` | `wiite,cp` | driver `wiite_corp` | [I2C_PRESENT] [DRIVER_BOUND] [NRF_CONTROL] |

I2C4 controller:

  Controller path:
    `/sys/devices/platform/11011000.i2c4/i2c-4`

  Register base from hardware database:
    `0x11011000`

Interpretation:

  `4-0050` is the primary Wiite/nRF control interface and is driver-bound.
  `4-0033` is a present heartrate/PAR/health endpoint but lacks a driver symlink
  in the captured live sysfs. Absence of a driver symlink is not absence of the
  endpoint. It must be treated as a present I2C/DT endpoint with unresolved
  exact userspace/kernel ownership.

Other I2C devices observed in the same scan are not Section 04 primary but
define collision/board context:

| Bus/address | Name | Role |
|---|---|---|
| `0-005d` | `cap_touch` | touch panel, Section 05/10 boundary |
| `2-000c`, `2-0010`, `2-001a`, `2-002d`, `2-0050`, `2-0052` | camera/CCU/eeprom | camera stack, Section 06 |
| `5-0034` | `subpmic_pmu` | MT6370/sub-PMIC, power Section |
| `6-000c`, `6-002d`, `6-0050` | camera_main_two chain | camera stack, Section 06 |

Database rule:

  Preserve I2C4 controller and child declarations for `4-0011`, `4-0033`, and
  `4-0050`. Do not remove `4-0033` just because it has no driver symlink. The
  health path depends on the board declaring and preserving this endpoint.


################################################################################
# 04.11 — WIITE_CORP_CTRL SYSFS CONTROL SURFACE
################################################################################

Primary Wiite/nRF misc surface:

| Item | Value |
|---|---|
| Path | `/sys/class/misc/wiite_corp_ctrl` |
| Runtime target | `/sys/devices/virtual/misc/wiite_corp_ctrl` |
| Major/minor | `10:48` |
| Classification | [WIITE_CONTROL_SURFACE] [ACTIVE_RUNTIME] [NRF_CONTROL] |

Readable values captured:

| Attribute | Value / result | Interpretation |
|---|---|---|
| `acc_dir` | `Sensor Direction:0x0` | accelerometer orientation/config. |
| `altitude_value` | `Altitude value: 0` | capture idle; derived/open path. |
| `baro_enable` | `DISABLED` | capture idle; not absence proof. |
| `baro_value` | `Baro value: 0` | capture idle; pressure path still exists. |
| `btMAC` | `ca:77:87:c7:16:00` | nRF/bridge MAC-like identity value. |
| `chip_id` | `0x70` | nRF/control-plane identity anchor. |
| `command` | `0` | command control file. |
| `config` | `start ... 0-0-0-0-0-0-0-0-0-0-0-0-0-0-1-1-0-0-0-0-0-0-0-1-0-0-0-0-0-1-0-0-end ...` | 32-position vendor bitmap; semantics open. |
| `delay` | `pix_delay:50` | PixArt/PAR or health sensor timing surface. |
| `i2c_log` | `i2c_log_en:0` | Wiite I2C log flag. |
| `i2c_test` | I/O error | test/control file, not safe readable state. |
| `lcm_type` | `Unsupport` | unrelated/unsupported display hook. |
| `local_rtc` | malformed/garbage-like capture value | not trusted for real time. |
| `longterm` | `NO SUPPORT` | unsupported feature surface. |
| `nrf_rtc` | `2026-6-14 23:50:0` | nRF-side RTC/control time. |
| `nrf_sys_mode` | `0` | nRF system mode value. |
| `rst_op` | `unknown` | reset operation control surface. |
| `tempearture_value` | `Temperature value: 0` | ODM typo preserved; capture idle. |
| `uart_op` | `uart enable:0` | UART operation/control flag. |
| `uevent` | `MAJOR=10 MINOR=48 DEVNAME=wiite_corp_ctrl` | misc-device identity. |
| `upgrade_op` | `unknown` | firmware/upgrade operation surface. |
| `version` | `48059` | nRF/control firmware version anchor. |
| `vibr_test` | `NO SUPPORT` | unsupported feature/test surface. |

Permission denied / control nodes:

| Attribute | Result | Classification |
|---|---|---|
| `bprs_simu` | permission denied on cat | [SIMULATION_SURFACE] [BPRS_SURFACE] |
| `event_simu` | permission denied on cat | [SIMULATION_SURFACE] [EVENT_BUS] |
| `hrs_simu` | permission denied on cat | [SIMULATION_SURFACE] [HEART_RATE] |
| `spo2_simu` | permission denied on cat | [SIMULATION_SURFACE] [SPO2] |
| `step_simu` | permission denied on cat | [SIMULATION_SURFACE] [STEP_COUNTER] |
| `tilt_simu` | permission denied on cat | [SIMULATION_SURFACE] [TILT] |
| `user_command` | permission denied on cat | [COMMAND_SURFACE] |
| `vdd18_con` | I/O error | [POWER_CONTROL_OPEN_LEAD] |

Critical interpretation:

  `wiite_corp_ctrl` is a system-writable control plane, not a normal Android
  sensor abstraction. It exposes chip identity, firmware version, command,
  config, UART, upgrade, RTC, MAC, simulation, and health/control surfaces.
  It is a required stock control surface. The stock UID1000 owners are separate
  ownership/control-plane facts and must be recorded independently from the
  hardware-facing node.

Config bitmap open lead:

  The config string contains 32 printed positions. In the captured sequence,
  `1` appears at positions 14, 15, 23, and 29 if counting from zero. Bit
  semantics remain an [OPEN_PROTOCOL_LEAD] for Section 13/protocol RE.


################################################################################
# 04.12 — WIITE_CON_CTRL SYSFS CONTROL SURFACE
################################################################################

Secondary Wiite control surface:

| Item | Value |
|---|---|
| Path | `/sys/class/misc/wiite_con_ctrl` |
| Runtime target | `/sys/devices/virtual/misc/wiite_con_ctrl` |
| Major/minor | `10:50` |
| Version string | `driver vreion:(1.0 20200104)` |
| Classification | [WIITE_CONTROL_SURFACE] [ACTIVE_RUNTIME] [PIN_IRQ_CONTROL] |

Readable/control values:

| Attribute | Value / result | Interpretation |
|---|---|---|
| `dev` | `10:50` | misc-device identity. |
| `irq_test` | I/O error | control/test file. |
| `pin_set` | I/O error | pin-control file. |
| `uevent` | `MAJOR=10 MINOR=50 DEVNAME=wiite_con_ctrl` | misc-device identity. |
| `version` | `driver vreion:(1.0 20200104)` | driver version string with ODM typo. |

Kernel activity:

  Filtered dmesg repeatedly shows:
    `[WIITE_CON_CTRL]Try to set pin(2) to state(0)`
    `[WIITE_CON_CTRL]Try to set pin(2) to state(1)`

  The repeated toggling occurs from `kworker/0:2` context in the captured ring
  buffer. This proves live kernel activity on the Wiite control plane. It does
  not by itself decode the electrical net attached to pin 2.

Database rule:

  Record the driver/node as active until protocol behavior is understood.
  World/system-writable control remains a hardening classification, but pin role,
  protocol ownership, and wake/IRQ behavior are not decoded in this section.


################################################################################
# 04.13 — UART / TTY BRIDGE
################################################################################

UART bridge:

| Item | Value |
|---|---|
| Device node | `/dev/ttyS1` |
| Backing sysfs | `/sys/class/tty/ttyS1` |
| Controller | `11003000.serial` |
| Major/minor | `4:65` |
| Live permissions | `crw-rw-rw- system system` |
| SELinux label | `u:object_r:ttyS_device:s0` |
| Live open owner | `com.wiite.devicedaemon:communication` PID 2144 FD 52u |
| Classification | [UART_BRIDGE] [ACTIVE_RUNTIME] [ODM_UID1000_OWNER] [SECURITY_CRITICAL] |

Interpretation:

  `/dev/ttyS1` is the Android-side UART bridge into the Wiite/nRF control plane.
  It is not safe as world-writable production configuration. In the stock vendor
  init files it is explicitly chmod 666 and chowned to `system:system`.

Database disposition:

  Required stock surfaces:
    `/dev/ttyS1`
    `11003000.serial`
    `com.wiite.devicedaemon:communication` open FD ownership, PID 2144, FD 52u

  Unsafe stock exposure:
    `/dev/ttyS1` is world-writable in the captured stock init configuration.
    This permission state is not a hardware requirement.

  Open semantic lead:
    UART payload schema and full command map remain undecoded.


################################################################################
# 04.14 — INPUT SUBSYSTEM NEGATIVE BOUNDARY
################################################################################

Live input devices observed:

| Input device | Function |
|---|---|
| `mtk-kpd` | key/button input |
| `hyn_ts` | touch controller |
| `mtk-tpd` | touch panel / platform touch |

Live `/dev/input` nodes:

| Node | Major/minor | Owner/group | Label |
|---|---:|---|---|
| `/dev/input/event0` | `13:64` | `root:input` | `u:object_r:input_device:s0` |
| `/dev/input/event1` | `13:65` | `root:input` | `u:object_r:input_device:s0` |
| `/dev/input/event2` | `13:66` | `root:input` | `u:object_r:input_device:s0` |

Negative conclusion:

  No accelerometer, step, tilt, heart-rate, SpO2, pressure, or Wiite event input
  device was observed in `/proc/bus/input/devices` or `/dev/input`. The wearable
  sensor path is not the normal Linux input subsystem path. It is SensorService
  + MTK Sensor HAL + `/dev/m_*_misc` + Wiite/nRF/PAR control path.


################################################################################
# 04.15 — WIITE / HEALTH PACKAGE OWNERSHIP
################################################################################

The user-visible and control-facing health stack is owned by UID1000 Wiite
packages. These are not ordinary unprivileged health apps. They inherit the
system UID permission cluster.

| Package | APK path | UID | Version | Target SDK | Requested / relevant permissions | Classification |
|---|---|---:|---|---:|---|---|
| `com.wiite.wearhealthuart` | `/product/app/WearHealthStepUart/WearHealthStepUart.apk` | 1000 | null / code 0 | 28 | `WRITE_SETTINGS`, `RECEIVE_BOOT_COMPLETED`; BootReceiver present | [ODM_UID1000_OWNER] [STEP_OWNER] [REPLACE] |
| `com.wiite.wiite_heartrate` | `/product/app/WearHeartRateUart/WearHeartRateUart.apk` | 1000 | `1.0.2` / code 2 | 30 | `BODY_SENSORS` | [ODM_UID1000_OWNER] [HEART_RATE_OWNER] [REPLACE] |
| `com.wiite.wiite_bloodoxygen` | `/product/app/WearSpoUart/WearSpoUart.apk` | 1000 | `1.0.3` / code 3 | 30 | `BODY_SENSORS` | [ODM_UID1000_OWNER] [SPO2_OWNER] [REPLACE] |
| `com.wiite.wiite_sleep` | `/product/app/WearSleepUart/WearSleepUart.apk` | 1000 | `1.0.2` / code 2 | 30 | broad UID1000 inherited grants | [ODM_UID1000_OWNER] [HEALTH_APP] [REPLACE] |
| `com.wiite.devicedaemon` | `/product/priv-app/WearDeviceDeamPix/WearDeviceDeamPix.apk` | 1000 | `1.0.2` / code 2 | 28 | `FOREGROUND_SERVICE`, `BLUETOOTH`, `BLUETOOTH_ADMIN`, location, phone state, storage, boot, wakelock, write settings, system alert | [PRIVILEGED_PRODUCT_APP] [UART_OWNER] [WIITE_CONTROL_OWNER] [REPLACE] |
| `com.wiite.WiiteCommon` | `/product/priv-app/WearCommon/WearCommon.apk` | 1000 | `1.1` / code 2 | 29 | `WRITE_SETTINGS`, `REBOOT`; privileged/product/direct-boot-aware | [PRIVILEGED_PRODUCT_APP] [ODM_CONTROL] [REPLACE] |
| `com.wiite.power` | `/product/app/WearInChargingUi/WearInChargingUi.apk` | 1000 | `1.3` / code 3 | 24 | `RECEIVE_BOOT_COMPLETED`, `SYSTEM_ALERT_WINDOW` | [ODM_UID1000_OWNER] [POWER_UI_ADJACENT] [REPLACE_OR_ANALYZE] |

Observed running process set in sensor/health/control capture:

| Process | SELinux domain | UID | Role |
|---|---|---:|---|
| `com.wiite.WiiteCommon` | `u:r:system_app:s0` | system / 1000 | common ODM framework. |
| `com.wiite.devicedaemon` | `u:r:system_app:s0` | system / 1000 | ODM device daemon. |
| `com.wiite.devicedaemon:communication` | `u:r:system_app:s0` | system / 1000 | UART/communication owner; opens ttyS1. |
| `com.wiite.wearhealthuart` | `u:r:system_app:s0` | system / 1000 | step/pedometer owner; SensorService client. |
| `com.wiite.wiite_heartrate` | `u:r:system_app:s0` | system / 1000 | heart-rate app. |
| `com.wiite.wiite_sleep` | `u:r:system_app:s0` | system / 1000 | sleep/health app. |
| `com.wiite.wiite_bloodoxygen` | `u:r:system_app:s0` | system / 1000 | SpO2 app. |
| `com.wiite.power` | `u:r:system_app:s0` | system / 1000 | power/charging UI adjacent. |
| `com.wiite.wiitestore` | `u:r:system_app:s0` | system / 1000 | store/control-plane adjacent; Section 16 risk. |
| `com.wiite.cleantask` | `u:r:system_app:s0` | system / 1000 | task/control app; Section 16/12. |

Security interpretation:

  The health/control apps are not isolated by package identity. Because they run
  under UID1000 / system_app, their effective trust boundary is the UID1000
  cluster. Required health functionality and stock UID1000 ownership must be separated in
  the database. The ODM cluster is an ownership/control-plane finding, not proof
  that all listed packages are hardware requirements.


################################################################################
# 04.16 — INIT / UEVENT PERMISSION AUTHORITY
################################################################################

Sensor device permission authority from `init.sensor_1_0.rc`:

| Target | Init action | Result |
|---|---|---|
| `/dev/m_hrs_misc` | chmod 0660 / chown system system | HAL/system access to heart-rate misc node. |
| `/dev/m_spo2_misc` | chmod 0660 / chown system system | HAL/system access to SpO2 misc node. |
| `/dev/m_bprs_misc` | chmod 0660 / chown system system | HAL/system access to BPRS misc node. |
| `/dev/m_ebus_misc` | chmod 0660 / chown system system | HAL/system access to event-bus misc node. |
| `/dev/m_baro_misc` | chmod 0660 / chown system system | HAL/system access to barometer/pressure misc node. |
| `/dev/m_acc_misc` | chmod 0660 / chown system system | HAL/system access to accelerometer misc node. |
| `/dev/m_tilt_misc` | chmod 0660 / chown system system | HAL/system access to tilt misc node. |
| `/dev/m_step_c_misc` | chmod 0660 / chown system system | HAL/system access to step-counter misc node. |
| `/dev/barometer` | chmod 0660 / chown system system | legacy/adjacent barometer char-device expectation. |

Sensor sysfs permission authority from `init.sensor_1_0.rc`:

| Class node | Controlled attributes |
|---|---|
| `/sys/class/sensor/m_acc_misc` | `accenablenodata`, `accactive`, `accdelay`, `accbatch`, `accflush`, `acccali` |
| `/sys/class/sensor/m_tilt_misc` | `tilt_enablenodata`, `tilt_active`, `tilt_delay`, `tilt_batch`, `tilt_flush` |
| `/sys/class/sensor/m_hrs_misc` | `hrs_active`, `hrs_delay`, `hrs_batch`, `hrs_flush`, `hrs_cali` |
| `/sys/class/sensor/m_spo2_misc` | `spo2_active`, `spo2_delay`, `spo2_batch`, `spo2_flush`, `spo2_name` |
| `/sys/class/sensor/m_bprs_misc` | `bprs_active`, `bprs_delay`, `bprs_batch`, `bprs_flush`, `bprs_name` |
| `/sys/class/sensor/m_ebus_misc` | `ebus_enablenodata`, `ebus_active`, `ebus_delay`, `ebus_batch`, `ebus_flush`, `ebus_devnum` |
| `/sys/class/sensor/m_baro_misc` | `baroenablenodata`, `baroactive`, `barodelay`, `barobatch`, `baroflush` |
| `/sys/class/sensor/m_step_c_misc` | `step_cactive`, `step_cdelay`, `step_cbatch`, `step_cflush` |

Barometer calibration/control attributes declared by init:

  `/sys/bus/platform/drivers/barometer/i32kP`
  `/sys/bus/platform/drivers/barometer/i32kT`
  `/sys/bus/platform/drivers/barometer/pressure`
  `/sys/bus/platform/drivers/barometer/temperature`
  `/sys/bus/platform/drivers/barometer/calib_param_T_gain`
  `/sys/bus/platform/drivers/barometer/calib_param_T_dVbe`
  `/sys/bus/platform/drivers/barometer/calib_param_T_Vbe`
  `/sys/bus/platform/drivers/barometer/calib_param_c0`
  `/sys/bus/platform/drivers/barometer/calib_param_c00`
  `/sys/bus/platform/drivers/barometer/calib_param_c1`
  `/sys/bus/platform/drivers/barometer/calib_param_c01`
  `/sys/bus/platform/drivers/barometer/calib_param_c02`
  `/sys/bus/platform/drivers/barometer/calib_param_c10`
  `/sys/bus/platform/drivers/barometer/calib_param_c11`
  `/sys/bus/platform/drivers/barometer/calib_param_c12`
  `/sys/bus/platform/drivers/barometer/calib_param_c20`
  `/sys/bus/platform/drivers/barometer/calib_param_c21`
  `/sys/bus/platform/drivers/barometer/calib_param_c30`

Wiite/nRF control permission authority from `init.mt6762.rc` and
`init.mt6765.rc`:

| Target | Action | Security implication |
|---|---|---|
| `/sys/class/misc/wiite_corp_ctrl/rst_op` | chown system system; chmod 0666 | reset operation exposed broadly. |
| `/sys/class/misc/wiite_corp_ctrl/upgrade_op` | chown system system; chmod 0666 | upgrade/firmware operation exposed broadly. |
| `/sys/class/misc/wiite_corp_ctrl/nrf_rtc` | chown system system; chmod 0666 | nRF RTC writable/readable. |
| `/sys/class/misc/wiite_corp_ctrl/btMAC` | chown system system; chmod 0666 | nRF/BT MAC-like value exposed. |
| `/sys/class/misc/wiite_corp_ctrl/longterm` | chown system system; chmod 0666 | control surface. |
| `/sys/class/misc/wiite_corp_ctrl/command` | chown system system; chmod 0666 | command surface. |
| `/sys/class/misc/wiite_corp_ctrl/version` | chown system system; chmod 0666 | version surface writable by mode. |
| `/sys/class/misc/wiite_corp_ctrl/config` | chown system system; chmod 0666 | 32-bit/position config surface. |
| `/sys/class/misc/wiite_corp_ctrl/uart_op` | chown system system; chmod 0666 | UART control surface. |
| `/dev/ttyS1` | chown system system; chmod 666 | world-writable UART bridge in stock init. |

Critical interpretation:

  The sensor misc nodes are intentionally exposed to `system:system` for the
  MTK Sensor HAL and system apps. The Wiite/nRF control surface is much broader:
  multiple command/upgrade/UART/config sysfs nodes are mode `0666`, and ttyS1 is
  mode `666`. This is the vulnerable stock control-plane exposure and is marked
  [HARDEN].

Database rule:

  Keep the `init.sensor_1_0.rc` sensor-node authority as a required-runtime
  database fact. Classify Wiite/ttyS1 permission mutations as hardening targets.
  Stock `chmod 666 /dev/ttyS1` is not a hardware requirement.


################################################################################
# 04.17 — SELINUX LABELS AND PROCESS DOMAINS
################################################################################

Runtime process/domain map:

| Runtime object | SELinux domain | Role |
|---|---|---|
| `android.hardware.sensors@2.0-service-mediatek` | `u:r:mtk_hal_sensors:s0` | MTK Sensor HAL. |
| `system_server` | `u:r:system_server:s0` | framework SensorService owner. |
| Wiite health/control apps | `u:r:system_app:s0` | UID1000 ODM owner cluster. |
| `irq/27-WIITE_CO` | `u:r:kernel:s0` | Wiite IRQ thread. |
| `irq/25-WIITE_CO` | `u:r:kernel:s0` | Wiite IRQ thread. |

Device node labels:

| Node | Label |
|---|---|
| `/dev/m_acc_misc` | `u:object_r:m_acc_misc_device:s0` |
| `/dev/m_baro_misc` | `u:object_r:m_baro_misc_device:s0` |
| `/dev/m_hrs_misc` | `u:object_r:m_hrs_misc_device:s0` |
| `/dev/m_spo2_misc` | `u:object_r:m_hrs_misc_device:s0` |
| `/dev/m_bprs_misc` | `u:object_r:m_hrs_misc_device:s0` |
| `/dev/m_ebus_misc` | `u:object_r:m_hrs_misc_device:s0` |
| `/dev/m_step_c_misc` | `u:object_r:m_step_c_misc_device:s0` |
| `/dev/m_tilt_misc` | `u:object_r:m_tilt_misc_device:s0` |
| `/dev/ttyS1` | `u:object_r:ttyS_device:s0` |
| `/sys/class/sensor` contents | `u:object_r:sysfs_sensor:s0` where captured |
| `/sys/class/misc/wiite_corp_ctrl` symlink | `u:object_r:sysfs:s0` |
| `/sys/class/misc/wiite_con_ctrl` symlink | `u:object_r:sysfs:s0` |

Policy-string evidence:

  The readable precompiled/current policy strings include sensor device types
  such as `m_pedo_misc_device`, `m_spo2_misc_device`, `m_temp_misc_device`,
  `m_tilt_misc_device`, `m_alti_misc_device`, `m_baro_misc_device`,
  `m_bprs_misc_device`, `m_ebus_misc_device`, `m_gyro_misc_device`, and
  `m_hmdy_misc_device`. String presence is not active runtime proof by itself,
  but it confirms the vendor policy namespace was built to cover a broader MTK
  sensor misc taxonomy than the physically confirmed device.

Policy interpretation:

  Keep only device types and access rules required by active nodes and retained
  HAL paths. Treat `m_gyro_misc_device`, `m_hmdy_misc_device`, and other unused
  taxonomy entries as Section 17 candidates unless future runtime proof appears.


################################################################################
# 04.18 — SENSOR-BY-SENSOR TRUTH TABLE
################################################################################

| Function | SensorService | HAL device node | /sys/class/sensor | Wiite/nRF/PAR surface | Physical status | Database disposition |
|---|---|---|---|---|---|---|
| Accelerometer | `ACCELEROMETER` handle `0x1`, active | `/dev/m_acc_misc` | `m_acc_misc` | `acc_dir`, nRF/Wiite-adjacent architecture | active function; exact IC not LIS2HH12 | [KEEP] HAL/node; preserve framework output. |
| LIGHT / Wiite event | `LIGHT` handle `0x5`, active by DeviceDaemon | no dedicated light misc node captured | no ALS physical node captured | Wiite devicedaemon CommunicationServices client; encoded values observed | no physical ALS confirmed; fakeAls loaded | [HARDEN/REPLACE] do not expose as real ALS without proof. |
| Pressure / barometer | `PRESSURE` handle `0x6` | `/dev/m_baro_misc` | `m_baro_misc` | `baro_enable`, `baro_value`, barometer platform attrs | BMP280 absent on SCP; source unresolved | [KEEP] HAL/node; [OPEN_PROTOCOL_LEAD] physical source. |
| Ambient temperature | `AMBIENT_TEMPERATURE` handle `0xd` | not separated in `/dev` capture; possible broader vendor path | policy contains `m_temp_misc_device` string, no active node captured here | `tempearture_value` typo in wiite_corp_ctrl | source unresolved | [KEEP] framework if HAL publishes; preserve typo node if bridge depends. |
| Step detector | `STEP_DETECTOR` handle `0x12`; `STEP_DETECTOR_WAKEUP` handle `0x65` | step counter node only captured | `m_step_c_misc` | step_simu / nRF firmware path candidate | active function | [KEEP] framework and wake behavior. |
| Step counter / pedometer | `STEP_COUNTER` handle `0x13`, active | `/dev/m_step_c_misc` | `m_step_c_misc` | Wiite PedometerService client | user-visible confirmed | [KEEP] node; [REPLACE] Wiite owner. |
| Heart rate | `HEART_RATE` handle `0x15` | `/dev/m_hrs_misc` | `m_hrs_misc`, `hrs_name=nrf_ctrl_hrs` | I2C4 `4-0033`, Wiite heartrate app | user-visible confirmed; PAR2822/nRF-adjacent | [KEEP] node/I2C; [REPLACE] app owner. |
| Heart beat | `HEART_BEAT` handle `0x1f` | likely health event path via HRS/ebus | HRS/EBUS class surfaces | nRF health event path candidate | event surface; physical semantic open | [KEEP] if HAL publishes; protocol lead. |
| SpO2 / blood oxygen | not standard SensorService type in capture | `/dev/m_spo2_misc` | `m_spo2_misc`, `spo2_name=nrf_ctrl_spo2` | Wiite bloodoxygen app; I2C4 health/PAR path | user-visible confirmed | [KEEP] node; [REPLACE] app owner; [OPEN_PROTOCOL_LEAD] exact protocol. |
| Blood pressure / BPRS | not standard SensorService type in capture | `/dev/m_bprs_misc` | `m_bprs_misc`, `bprs_name=nrf_ctrl_bprs` | `bprs_simu` and BPRS class surface | onboard physical source not confirmed; likely external/simulation/placeholder | [KEEP AS SURFACE] until protocol/owner semantics are decoded; do not classify physical. |
| EBUS / event bus | no standard SensorService type | `/dev/m_ebus_misc` | `m_ebus_misc`, `ebus_active=1`, `ebus_simu` | event/control bus | semantic open | [KEEP] required-runtime surface; protocol lead. |
| Tilt detector | `TILT_DETECTOR` handle `0x16`, active, wakeUp | `/dev/m_tilt_misc` | `m_tilt_misc` | `tilt_simu`; Wiite/nRF event path candidate | active function | [KEEP] framework and wake path. |
| Proximity | no standard SensorService PROXIMITY in dump | no real prox node | fakePs SCP result | fakePs only | no physical proximity sensor | [STRIP/NEGATIVE] unless loader-required stub. |
| Ambient light physical ALS | SensorService LIGHT exists | no real ALS node | fakeAls SCP result | Wiite event/client behavior | no physical ALS confirmed | [HARDEN] LIGHT as event channel; do not claim physical ALS. |
| Gyroscope | no gyro SensorService publication; fusion disabled | no gyro active node | policy string may include `m_gyro_misc_device` only | none proven | absent/ghost | [STRIP/NEGATIVE] unless loader-required ghost. |
| Magnetometer / compass | no mag SensorService publication; AKM09918 ACK error | no mag active node | none confirmed | none proven | absent/ghost | [STRIP/NEGATIVE]. |
| GPS/GNSS | not a Section 04 sensor | GNSS stack in Section 09 | n/a | Google location consumes accel/tilt | GPS user-visible but separate subsystem | [MOVE_TO_SECTION_09]. |


################################################################################
# 04.19 — USER-VISIBLE HEALTH FUNCTION SET
################################################################################

The stock device user-visible health set confirmed by user observation:

  1. Heart rate.
  2. SpO2 / blood oxygen.
  3. Step counter / pedometer.

Observed/likely motion set:

  1. Accelerometer.
  2. Tilt detector / wake-gesture style function.

Not confirmed as onboard user-visible physical health measurement:

  Blood pressure.

Blood pressure conclusion:

  The software stack exposes `m_bprs_misc`, `bprs_name=nrf_ctrl_bprs`, and
  `bprs_simu`. The HAL opens `/dev/m_bprs_misc`. This proves a BPRS software,
  HAL, and kernel class surface. It does not prove a physical onboard blood
  pressure sensor. Given user observation that normal visible health tracking is
  heart rate, SpO2, and steps, the proper classification is:

    [ACTIVE_HAL_DEVICE_NODE]
    [KERNEL_CLASS_PRESENT]
    [HEALTH_SURFACE_DECLARED]
    [USER_VISIBLE_FUNCTION_NOT_CONFIRMED]
    [PHYSICAL_SOURCE_UNRESOLVED]
    [EXTERNAL_DEVICE_OR_SIMULATION_CANDIDATE]

SpO2 conclusion:

  SpO2 is different from blood pressure. It is user-visible, the HAL opens
  `/dev/m_spo2_misc`, and the class node reports `spo2_name=nrf_ctrl_spo2`.
  It is not a standard SensorService sensor in the captured dump, but it is a
  real stock device health function exposed through the lower HAL/kernel/nRF
  health path.


################################################################################
# 04.20 — FAKE / SIMULATED / GHOST SENSOR REGISTER
################################################################################

| Surface | Evidence | Classification | Database disposition |
|---|---|---|---|
| `fakeAls` | SCP sec03 loads fakeAls, no physical CM36558 ALS | [SIMULATION_SURFACE] [NO_PHYSICAL_ALS] | Do not expose as physical ALS. Preserve only if HAL requires event channel. |
| `fakePs` | SCP sec05 fakePs active, no physical proximity IC | [SIMULATION_SURFACE] [NO_PHYSICAL_PROXIMITY] | Keep stub only if loader-required; otherwise strip/negative. |
| `bprs_simu` | Wiite control node and `m_bprs_misc` exist | [SIMULATION_SURFACE] [BPRS_SURFACE] | Do not classify as physical BP sensor. |
| `spo2_simu` | Wiite control node exists while real SpO2 function is user-visible | [SIMULATION_SURFACE] [SPO2_TEST_CONTROL] | Preserve only for bridge if required; not proof of absence. |
| `hrs_simu` | Wiite control node exists while heart rate is user-visible | [SIMULATION_SURFACE] [HRS_TEST_CONTROL] | Test/control surface; harden/restrict. |
| `step_simu` | Wiite control node exists while step counter is active | [SIMULATION_SURFACE] [STEP_TEST_CONTROL] | Test/control surface; harden/restrict. |
| `tilt_simu` | Wiite control and class node exists | [SIMULATION_SURFACE] [TILT_TEST_CONTROL] | Test/control surface; harden/restrict. |
| `ebus_simu` | class node write-only/permission denied read | [SIMULATION_SURFACE] [EVENT_BUS_TEST_CONTROL] | Test/control surface; harden/restrict. |
| LIS2HH12 | SCP ACK error | [BSP_GHOST] | Do not add as physical accelerometer. |
| AKM09918 | SCP ACK error | [BSP_GHOST] [NEGATIVE] | Do not add compass/magnetometer hardware. |
| CM36558 | fakeAls result | [BSP_GHOST] [NEGATIVE] | Do not add physical ALS. |
| BMP280 | SCP ACK error / absent on SCP side | [BSP_GHOST] for SCP BMP280 | Preserve pressure path, but do not claim SCP BMP280. |
| Gyroscope | no physical proof, fusion disabled | [NEGATIVE] [BSP_GHOST_IF_STRINGS] | Do not add physical gyro. |


################################################################################
# 04.21 — STOCK ECO / SPORTS REDUCED-RUNTIME MODE
################################################################################

The stock firmware exposes a user-visible ECO / Sports low-power mode. This
mode is included in Section 04 only because it clarifies the sensor, health,
Wiite, nRF, UART, and communication-control boundary. It is stock behavior and
is not treated as a required functional feature of the software database.

ECO mode classification:

  [STOCK_BEHAVIOR]
  [ANDROID_RUNTIME_STILL_ACTIVE]
  [ADB_REMAINS_AVAILABLE]
  [SENSOR_HEALTH_SUBSTRATE_ACTIVE]
  [WIITE_NRF_CONTROL_PLANE_ACTIVE]
  [COMMUNICATION_SURFACE_REDUCED]
  [MODE_COMMAND_UNDECODED]
  [NOT_TARGET_FEATURE]

Live ECO-mode state captured through targeted checks:

| Surface | ECO-mode value / state | Classification |
|---|---|---|
| ADB shell | available | [ANDROID_RUNTIME_STILL_ACTIVE] [ADB_REMAINS_AVAILABLE] |
| `system_server` | running | [ANDROID_RUNTIME_STILL_ACTIVE] |
| `android.hardware.sensors@2.0-service-mediatek` | running, PID `694` at capture | [SENSOR_HAL_ACTIVE] |
| `android.hardware.health@2.0-service` | running, PID `527` at capture | [HEALTH_HAL_ACTIVE] |
| `com.wiite.devicedaemon` | running, PID `1881` at capture | [ODM_UID1000_OWNER] [PROCESS_ACTIVE] |
| `com.wiite.devicedaemon:communication` | running, PID `2144` at capture | [ODM_UID1000_OWNER] [UART_BRIDGE_OWNER] |
| `com.wiite.wearhealthuart` | running, PID `2519` at capture | [ODM_UID1000_OWNER] [HEALTH_OWNER] |
| `com.wiite.power` | running, PID `18847` at capture | [ODM_UID1000_OWNER] [POWER_UI_OWNER] |
| `com.wiite.wiite_bloodoxygen` | running, PID `18877` at capture | [ODM_UID1000_OWNER] [SPO2_OWNER] |

nRF / Wiite ECO-mode control state:

| Node | ECO-mode value | Classification |
|---|---|---|
| `/sys/class/misc/wiite_corp_ctrl/nrf_sys_mode` | `0` | [WIITE_CONTROL_SURFACE] [MODE_SELECTOR_NOT_PROVEN] |
| `/sys/class/misc/wiite_corp_ctrl/command` | `0` | [WIITE_CONTROL_SURFACE] |
| `/sys/class/misc/wiite_corp_ctrl/uart_op` | `uart enable:0` | [WIITE_CONTROL_SURFACE] [UART_IDLE_STATE] |
| `/sys/class/misc/wiite_corp_ctrl/config` | `start ... 0-0-0-0-0-0-0-0-0-0-0-0-0-0-1-1-0-0-0-0-0-0-0-1-0-0-0-0-0-1-0-0-end ...` | [WIITE_CONTROL_SURFACE] [CONFIG_BITMAP_UNCHANGED] |

Interpretation:

  ECO mode is not proven to be represented by the exposed `nrf_sys_mode` value.
  During ECO-mode capture, `nrf_sys_mode` remained `0`, `command` remained `0`,
  `uart_op` reported `uart enable:0`, and the captured config bitmap matched the
  known stock bitmap. Therefore, the exposed readable sysfs state does not close
  the ECO command selector. ECO command selection remains an undecoded stock
  control path.

UART state in ECO mode:

| Surface | ECO-mode state | Classification |
|---|---|---|
| `/dev/ttyS1` | `crw-rw-rw- system system u:object_r:ttyS_device:s0 4,65` | [UART_BRIDGE] [UNSAFE_WORLD_WRITABLE] |
| Open owner | `com.wiite.devicedaemon:communication`, PID `2144`, FD `52u` | [ODM_UID1000_OWNER] [UART_BRIDGE_OWNER] [ACTIVE_RUNTIME] |

Interpretation:

  ECO mode does not close or remove the Android-side UART bridge. The same
  DeviceDaemon communication process remains the live owner of `/dev/ttyS1`.
  The UART path remains a stock control-plane surface even while radios are
  reduced.

Sensor / health ECO-mode active flags:

| Node | ECO-mode value | Classification |
|---|---:|---|
| `/sys/class/sensor/m_acc_misc/accactive` | `1000` | [KERNEL_CLASS_PRESENT] [ACTIVE_RUNTIME] |
| `/sys/class/sensor/m_step_c_misc/step_cactive` | `1000` | [KERNEL_CLASS_PRESENT] [ACTIVE_RUNTIME] |
| `/sys/class/sensor/m_tilt_misc/tilt_active` | `1` | [KERNEL_CLASS_PRESENT] [ACTIVE_RUNTIME] |
| `/sys/class/sensor/m_ebus_misc/ebus_active` | `1` | [KERNEL_CLASS_PRESENT] [EVENT_BUS_ACTIVE] |
| `/sys/class/sensor/m_hrs_misc/hrs_active` | `0` | [KERNEL_CLASS_PRESENT] [IDLE_AT_CAPTURE] |
| `/sys/class/sensor/m_spo2_misc/spo2_active` | `0` | [KERNEL_CLASS_PRESENT] [IDLE_AT_CAPTURE] |

Interpretation:

  ECO mode preserves the sensor-health substrate. Accelerometer, step counter,
  tilt, and EBUS remain active. Heart-rate and SpO2 class nodes remain present
  but idle at the moment captured. The idle state of HR/SpO2 does not negate
  their confirmed user-visible and HAL/kernel paths; it only records the
  immediate ECO snapshot value.

Communication ECO-mode state:

| Surface | ECO-mode state | Classification |
|---|---|---|
| Wi-Fi | `Wi-Fi is disabled`; `curState=StaDisabledState`; `Current wifi mode: WifiDisabledState` | [COMMUNICATION_SURFACE_REDUCED] |
| Bluetooth | `enabled: false`; `state: OFF`; `Bluetooth Service not connected` | [COMMUNICATION_SURFACE_REDUCED] |
| Telephony service state | `mVoiceRegState=3(POWER_OFF)`, `mDataRegState=3(POWER_OFF)` | [COMMUNICATION_SURFACE_REDUCED] [RADIO_POWER_OFF] |
| Telephony data state | `mDataConnectionState=0` | [COMMUNICATION_SURFACE_REDUCED] |
| Telephony radio power | `mRadioPowerState=0` | [COMMUNICATION_SURFACE_REDUCED] [RADIO_POWER_OFF] |

Interpretation:

  ECO mode reduces communication surfaces while leaving Android, ADB, Sensor HAL,
  health HAL, Wiite DeviceDaemon, Wiite health apps, `/dev/ttyS1`, and key
  sensor class nodes alive. It is therefore a reduced Android runtime mode, not
  a full MCU-only/off-Android mode.

Transition-log status:

  A filtered logcat transition capture was attempted while in ECO mode and did
  not produce a useful decoded ECO command sequence. No `nrf_sys_mode` change,
  no new readable `command` value, and no explicit transition frame were closed
  from the available log. The ECO transition command remains an open semantic
  lead. This does not block Section 04 closure because the live runtime boundary
  is established.

Database conclusion:

  ECO / Sports mode is a stock control-layer behavior that demonstrates the
  separation between communication surfaces and the sensor-health substrate. It
  must be recorded as context for Wiite/nRF/EBUS/ttyS1 behavior. It must not be
  treated as proof that the ODM ECO mode is a required feature or that every
  Wiite process is required for ordinary sensor publication.


################################################################################
# 04.22 — SOFTWARE DATABASE DISPOSITION REGISTER
################################################################################

This register separates stock runtime requirements from stock ownership and
unsafe exposure. It is a database disposition register, not a deployment plan.

Required runtime surfaces:

| Surface | Reason | Classification |
|---|---|---|
| `android.hardware.sensors@2.0-service-mediatek` | active SensorService provider | [KEEP] [REGISTERED_HAL] |
| `/vendor/lib64/hw/sensors.mt6765.so` | active HAL implementation library | [KEEP] [HAL_IMPL] |
| `/vendor/lib64/libksensor.so` | active HAL dependency | [KEEP] [HAL_DEPENDENCY] |
| `/dev/m_acc_misc` | accelerometer path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_hrs_misc` | heart-rate path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_spo2_misc` | SpO2 path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_step_c_misc` | step counter path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_tilt_misc` | tilt path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_baro_misc` | pressure/barometer path | [KEEP] [ACTIVE_HAL_DEVICE_NODE] |
| `/dev/m_ebus_misc` | active event-bus path | [KEEP] [OPEN_PROTOCOL_LEAD] |
| `/dev/m_bprs_misc` | BPRS surface; physical status unresolved | [KEEP_UNTIL_DECODED] [OPEN_PROTOCOL_LEAD] |
| `/sys/class/sensor/*` | HAL/class control ABI | [KEEP] [KERNEL_CLASS_PRESENT] |
| I2C4 `4-0050` `wiite,cp` | driver-bound Wiite/nRF control endpoint | [KEEP] [I2C_PRESENT] [DRIVER_BOUND] |
| I2C4 `4-0033` `mediatek,heartrate` | health/PAR endpoint present; no driver symlink | [KEEP_UNTIL_DECODED] [I2C_PRESENT] [NO_DRIVER_SYMLINK] |
| I2C4 `4-0011` `ccu_sensor_i2c_4_hw` | driver-bound I2C4 hardware-trigger endpoint | [KEEP] [I2C_PRESENT] [DRIVER_BOUND] |
| `/dev/ttyS1` / `11003000.serial` | live UART bridge transport | [KEEP] [UART_BRIDGE] |
| SCP/tinysys interfaces | active platform sensor substrate | [KEEP] [ACTIVE_RUNTIME] |

Stock ownership / control-plane surfaces:

| Surface | Reason | Classification |
|---|---|---|
| `com.wiite.devicedaemon` | UID1000 control owner | [ODM_UID1000_OWNER] [REPLACE_OWNER] |
| `com.wiite.devicedaemon:communication` | UART bridge owner | [ODM_UID1000_OWNER] [UART_BRIDGE_OWNER] [REPLACE_OWNER] |
| `com.wiite.wearhealthuart` | pedometer/health owner | [ODM_UID1000_OWNER] [REPLACE_OWNER] |
| `com.wiite.wiite_heartrate` | heart-rate UI/control owner | [ODM_UID1000_OWNER] [REPLACE_OWNER] |
| `com.wiite.wiite_bloodoxygen` | SpO2 UI/control owner | [ODM_UID1000_OWNER] [REPLACE_OWNER] |
| `com.wiite.wiite_sleep` | sleep/health owner | [ODM_UID1000_OWNER] [REPLACE_OWNER] |
| `com.wiite.power` | stock power/ECO UI owner | [ODM_UID1000_OWNER] [STOCK_BEHAVIOR_OWNER] |
| `com.wiite.WiiteCommon` | shared Wiite privileged framework owner | [ODM_UID1000_OWNER] [PRIVILEGED_PRODUCT_APP] |

Unsafe exposure surfaces:

| Surface | Evidence | Classification |
|---|---|---|
| `/dev/ttyS1` mode `0666` | live node is world-writable | [UNSAFE_WORLD_WRITABLE] [HARDEN] |
| `/sys/class/misc/wiite_corp_ctrl/command` | command node exposed to system_app; AVC denials permissive at capture | [WIITE_CONTROL_SURFACE] [HARDEN] |
| `/sys/class/misc/wiite_corp_ctrl/upgrade_op` | upgrade/DFU-adjacent control surface | [WIITE_CONTROL_SURFACE] [HARDEN] |
| `/sys/class/misc/wiite_corp_ctrl/config` | live configuration bitmap | [WIITE_CONTROL_SURFACE] [OPEN_PROTOCOL_LEAD] |
| `*_simu` nodes | test/simulation command surfaces | [SIMULATION_SURFACE] [HARDEN] |

Ghost / negative surfaces:

| Surface | Evidence | Classification |
|---|---|---|
| LIS2HH12 as physical accelerometer | SCP ACK error; accelerometer still published through different path | [BSP_GHOST] [NEGATIVE_AS_PHYSICAL_IDENTITY] |
| AKM09918 as physical magnetometer | SCP ACK error; no SensorService magnetometer | [BSP_GHOST] [NEGATIVE] |
| CM36558 as physical ALS/proximity | fakeAls/fakePs context; no standalone ALS/prox IC confirmed | [BSP_GHOST] [NEGATIVE_AS_PHYSICAL_IDENTITY] |
| BMP280 as SCP barometer | SCP slot absent / ACK error; pressure source unresolved | [BSP_GHOST] [NEGATIVE_AS_SCP_SOURCE] |
| Gyroscope | no SensorService gyro; fusion disabled; no physical proof | [NEGATIVE] |
| Standalone proximity sensor | fakePs only | [NEGATIVE] |
| Standalone ambient-light sensor | fakeAls / LIGHT event-channel candidate | [NEGATIVE_AS_STANDALONE_IC] |


################################################################################
# 04.23 — OPEN LEADS
################################################################################

These items remain open but do not block Section 04 closure:

| Open lead | Current known truth | Blocking status |
|---|---|---|
| Full nRF UART command map | ttyS1 path and owner are known; heart-rate task/frame evidence exists; full command semantics not decoded. | Not blocking Section 04 database closure. |
| SpO2 exact trigger path | SpO2 user-visible result and HAL BloodOxygen path are confirmed; command trigger did not expose the same clean task frame as heart rate. | Not blocking; classify as protocol/semantic lead. |
| PAR2822 exact frame semantics | `4-0033` endpoint and HR/SpO2 functions known; exact protocol open. | Not blocking; requires static/native/protocol work. |
| `m_ebus_misc` semantics | active HAL node, `ebus_active=1`, WiiteEventSensor event values observed; event meanings unknown. | Preserve as open event-bus lead. |
| ECO transition command | ECO runtime boundary is closed; `nrf_sys_mode` stayed `0`; no decoded transition frame captured. | Not blocking; stock behavior context only. |
| BPRS/Blood pressure function | HAL/kernel surface exists; physical onboard sensor not confirmed; external/simulation/placeholder candidate. | Not a confirmed onboard physical feature. |
| Barometer physical source | SensorService pressure and `m_baro_misc` exist; BMP280 absent on SCP side. | Preserve pressure database path; exact source open. |
| Temperature physical source | SensorService ambient temperature and `tempearture_value` node exist; exact source open. | Preserve if HAL publishes. |
| LIGHT channel semantics | SensorService LIGHT active by DeviceDaemon; fakeAls/no standalone ALS; encoded event values observed. | Treat as event/control channel until decoded. |
| `wiite_con_ctrl` pin 2 net | live toggling in dmesg; electrical net unknown. | Preserve as driver/protocol lead. |
| Config bitmap meanings | 32-position bitmap captured; ones at positions 14, 15, 23, and 29 zero-indexed; bit meanings unknown. | Preserve as Section 13 follow-up. |
| SELinux exact minimal policy | labels known; final allow rules require Section 14 policy work. | Section 14. |


################################################################################
# 04.24 — ORIGINAL CLAIM RECONCILIATION LEDGER
################################################################################

| Original / upstream claim | Disposition | Resolution |
|---|---|---|
| Sensor stack depends on MTK Sensor HAL | KEEP / EXPAND | HAL registered, running, mapped, and opening misc nodes. |
| nRF bridge is central to wearable sensors | KEEP / EXPAND | I2C4 `4-0050`, Wiite sysfs, ttyS1, health names, and UID1000 owners confirm bridge/control plane. |
| All Android sensors route only through nRF | CORRECT | Correct model is hybrid: MTK Sensor HAL publishes framework sensors; nRF/PAR/Wiite owns or influences health/control paths. |
| Accelerometer exists | KEEP / EXPAND | SensorService active + `/dev/m_acc_misc`; LIS2HH12 identity rejected. |
| Heart rate exists | KEEP / EXPAND | SensorService HEART_RATE + `/dev/m_hrs_misc` + `nrf_ctrl_hrs` + user-visible + UART task/frame evidence. |
| SpO2 exists | KEEP / EXPAND | Not standard SensorService type in dump, but `/dev/m_spo2_misc`, `nrf_ctrl_spo2`, HAL BloodOxygen result, and user-visible function confirm it. |
| Blood pressure exists as hardware | DEMOTE | Software/HAL/class surface exists; physical onboard sensor not confirmed; external/simulation/placeholder candidate. |
| Step counter exists | KEEP / EXPAND | SensorService active + Wiite PedometerService + `/dev/m_step_c_misc`. |
| Tilt exists | KEEP / EXPAND | SensorService active/wake + `/dev/m_tilt_misc`; ECO snapshot showed `tilt_active=1`. |
| Barometer/pressure exists | KEEP / OPEN | SensorService pressure + `/dev/m_baro_misc`; physical source unresolved, BMP280 absent on SCP side. |
| Ambient light physical sensor exists | CORRECT | SensorService LIGHT exists, but fakeAls/no physical ALS; Wiite event/control candidate. |
| Proximity exists | DISPROVED AS PHYSICAL | fakePs only; no physical proximity sensor confirmed. |
| Magnetometer/compass exists | DISPROVED AS PHYSICAL | AKM09918 ACK error; no physical compass confirmed. |
| Gyroscope exists | DISPROVED AS PHYSICAL | no SensorService gyro, fusion disabled, no physical proof. |
| ttyS1 is part of nRF/control plane | KEEP / EXPAND | live device node, serial backing, DeviceDaemon communication FD, init chmod 666, ECO ownership retained. |
| Wiite sysfs attrs are important | KEEP / EXPAND | chip_id/version/config/uart/upgrade/command/simulation surfaces captured; ECO mode did not change exposed nRF mode fields. |
| UID1000 health apps are control owners | KEEP / EXPAND | package paths, UID1000, permissions, processes, SensorService clients captured. |
| ECO/Sports mode is Android-off | CORRECT | ECO is reduced Android runtime: ADB, system_server, Sensor HAL, Health HAL, Wiite apps, ttyS1, and sensor nodes remain active. |
| ECO/Sports mode is required feature parity | DEMOTE | Stock behavior documented for control-layer context only; not required for Section 04 functional sensor truth. |


################################################################################
# 04.25 — FINAL SECTION 04 CLOSURE STATEMENT
################################################################################

Section 04 is closed for the current C17S software digital-twin purpose.

Closed facts:

  - Android SensorService publishes 10 MTK-labeled framework sensors.
  - The MTK Sensor HAL is registered, running, mapped, and active.
  - The active Sensor HAL opens eight MTK virtual sensor misc devices.
  - `/sys/class/sensor` exposes the matching virtual sensor class nodes.
  - Heart rate is a confirmed user-visible, framework-published, HAL/kernel/nRF
    health function with captured UART task/frame evidence.
  - SpO2 is a confirmed user-visible HAL/kernel/nRF health function, even though
    it is not a standard SensorService type in the captured dump.
  - Blood pressure is a software/HAL/kernel surface only; onboard physical
    hardware is not confirmed.
  - The Wiite/nRF control plane is live through I2C4, sysfs, ttyS1, IRQ activity,
    and UID1000 Wiite apps.
  - Stock ECO/Sports mode is a reduced Android runtime mode: ADB and Android
    remain active, sensor/health/Wiite surfaces remain active, and Wi-Fi,
    Bluetooth, and cellular radio surfaces are reduced or powered off.
  - ECO/Sports mode does not map to a changed readable `nrf_sys_mode` value in
    the captured evidence.
  - SCP/tinysys is active and required for the stock sensor substrate, but the
    BSP sensor identities LIS2HH12, AKM09918, CM36558, and BMP280 are not
    physical-confirmed C17S board sensors.
  - fakeAls and fakePs are software stubs, not standalone physical ICs.
  - Gyroscope, magnetometer/compass, standalone proximity, and standalone ALS are
    negative/ghost for physical hardware purposes.
  - Open protocol leads are isolated to UART payload schema, SpO2 trigger
    semantics, PAR/health endpoint semantics, EBUS event meanings, BPRS status,
    barometer/temperature physical source, Wiite config bitmap, and wiite_con pin
    semantics.

Closure classification:

  [SECTION04_STATUS]
    FULLY POPULATED FOR CURRENT SOURCE AUTHORITY.
    CLOSED WITH OPEN PROTOCOL LEADS.
    STOCK ECO / SPORTS MODE RUNTIME BOUNDARY CLOSED FOR DATABASE PURPOSE.
    OPEN LEADS ARE PROTOCOL OR SEMANTIC LEADS, NOT BASIC PRESENCE FAILURES.


################################################################################
# 04.26 — SMALL BUILD RELEVANCE NOTE
################################################################################

The target build does not require ODM ECO/Sports feature parity. ECO/Sports mode
is documented only because it clarifies the stock Wiite/nRF/sensor-health control
boundary. The only Section 04 build-relevant rule is that confirmed hardware
functions and required runtime surfaces must remain functional while ODM/Wiite
ownership, unsafe permissions, and unnecessary ghost surfaces are removed or
handled outside this database section.


################################################################################
# END SECTION 04 — SENSOR STACK — SCP, nRF BRIDGE & ANDROID SENSORSERVICE
################################################################################
