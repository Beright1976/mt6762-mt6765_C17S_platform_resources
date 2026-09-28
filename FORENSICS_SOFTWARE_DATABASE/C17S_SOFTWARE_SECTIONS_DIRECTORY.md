################################################################################
#  C17S SOFTWARE DIGITAL TWIN — SECTION DIRECTORY
#  LOKMAT APPLLP 5 MAX (C17S)
#  Author: beright1976
#
#  DOCTRINE:
#  Every section mirrors the hardware database standard.
#  No value is assumed. No value is borrowed. No value is inferred.
#  Every entry requires a [VERIFIED] marker backed by a physical evidence
#  source. A section is not complete until every field is verified.
#  The live device is the authority for all runtime data.
#  The clean TWRP partition pulls are the authority for all file-sourced data.
#
#  SOURCE AUTHORITY (in order of trust):
#  TIER 1 : Live rooted device — adb shell, su, Magisk, getenforce permissive
#  TIER 2 : TWRP-extracted clean partition pulls
#           system/ — 8,859 files
#           vendor/ — 1,468 files
#           product/ — 286 files
#  TIER 3 : Binary analysis — apktool, JADX, Ghidra, strings, xxd
#  TIER 4 : Cross-reference against hardware database Sections 1-12
#
################################################################################


################################################################################
#  SECTION INDEX
################################################################################

Section 00 — Document Control, Partition Architecture & Source Authority
Section 01 — Init System, RC Files & Service Graph
Section 02 — HAL Layer — HIDL/VINTF Manifest & Interface Inventory
Section 03 — Cryptographic Stack, Library Chain & Namespace Isolation
Section 04 — Sensor Stack — SCP, nRF Bridge & Android SensorService
Section 05 — Display, GPU & Graphics Stack
Section 06 — Camera Stack — HAL, Blobs, Sensor Identities & CCU
Section 07 — Audio Stack — Policy, HAL, Parameters & Routes
Section 08 — Radio, Telephony & Modem Software Stack
Section 09 — Connectivity Software Stack — Wi-Fi, Bluetooth, GNSS & WMT
Section 10 — VNDK-29, VNDK-SP-29 & Vendor Blob Inventory
Section 11 — Firmware Blob Catalog & Hardware Identity Map
Section 12 — APK Inventory — All 127 Packages, Metadata & Disposition
Section 13 — ODM Control Layer — Wiite/nRF Framework Integration
Section 14 — SELinux Policy Map
Section 15 — Build Specification — BoardConfig, device.mk & Properties
Section 16 — Security & Threat Analysis — ODM APKs & Framework Patches
Section 17 — Software Negative Register & BSP Ghost Software
Section 18 — ROM Build Gate & Verification Checklist


################################################################################
#  SECTION SUMMARIES AND SCOPE
################################################################################

Section 00 — Document Control, Partition Architecture & Source Authority
  Scope:
  - Authoritative partition structure, extraction methodology, file counts
  - Complete GPT partition table with all 38 entries, sizes, and block devices
  - Dynamic partition map (lpdump): system, vendor, product, dm-0/1/2/3
  - Fstab analysis — both mt6762 and mt6765 variants, ghost entries
  - Live mount table from /proc/mounts
  - Kernel cmdline verbatim
  - Security state — stock versus research capture boundary
  - Cross-reference map to hardware database and doc set
  Status: SUBSTANTIALLY POPULATED — verify all entries, expand fstab analysis


Section 01 — Init System, RC Files & Service Graph
  Scope:
  - Complete RC file inventory: 68 flat + 17 hw/ subdirectory = 85 total
  - Full content analysis of every RC file including import chains,
    triggers, actions, service definitions, property triggers
  - Complete service graph showing start order, dependencies, class membership
  - Critical ordering constraints: init.modem.rc last, keymaster early_hal
  - ODM anomalies: iAmCdRom.iso mount, /FWUpgradeInit.rc injection,
    init.mt6762.rc == init.mt6765.rc byte-for-byte, ODM "sevice" typo
  - wiite_corp_ctrl permissions set in post-fs-data
  - ttyS1 chmod 666 confirmed
  - All property triggers documented
  - Boot timing anchors from bootprof cross-referenced to hardware Section 1
  Status: SUBSTANTIALLY POPULATED — complete flat RC file content analysis


Section 02 — HAL Layer — HIDL/VINTF Manifest & Interface Inventory
  Scope:
  - Complete VINTF manifest from /vendor/etc/vintf/manifest.xml
  - All HAL declarations: name, version, transport, interface, instance
  - HAL-to-binary mapping: every declared interface to its process binary
  - HAL-to-RC-file mapping: every service to its RC declaration
  - HIDL vs AIDL classification for every interface
  - Lazy versus eager HAL classification
  - Cross-reference to hardware sections for each HAL's physical domain
  - ODM-specific HAL extensions: mtkradioex, RadioOp, mtkpower, pq, gpu,
    nvram, mms, mtkcodecservice, atci, netdagent, lbs
  - Compatibility matrix: target FCM version, kernel requirements
  Status: PARTIALLY POPULATED — expand to full interface inventory


Section 03 — Cryptographic Stack, Library Chain & Namespace Isolation
  Scope:
  - Complete OpenSSL / BoringSSL / libcrypto chain identification
  - ODM-modified library identification: which libraries have diverged from AOSP
  - Namespace isolation requirements: which blobs need rpath correction
  - Bionic exception: which libraries must NOT be isolated
  - patchelf target list
  - libcrypto-ss, libssl-ss, libcurl-ss, libcharon-ss — Strongswan/ePDG analysis
  - Keymaster 4.0 library chain: confirmed pure-soft path
  - Gatekeeper 1.0 library chain: confirmed libSoftGatekeeper path
  - FBE crypto path: fscrypt v1 on kernel 4.9, legacy keyctl fallback
  - DXCC silicon versus Android crypto userspace path — separation documented
  Status: PARTIALLY POPULATED — Ghidra analysis pending on ODM-modified libs


Section 04 — Sensor Stack — SCP, nRF Bridge & Android SensorService
  Scope:
  - SCP RTOS sensor overlay results — all 5 sensor slots
  - MTK sensor HAL: android.hardware.sensors@2.0-service-mediatek
  - Android SensorService published sensors: full list with type, vendor, version
  - nRF52832 sensor bridge mapping: which sensors route through Wiite/nRF
  - Fake/simulated sensors: fakeAls, fakePs — confirmed not physical ICs
  - Accelerometer path: published via MTK sensor stack / nRF bridge
  - Heart rate path: PAR2822 proxy through nRF — confirmed I2C4 4-0033
  - SpO2 path: nRF health stack — not a standard SensorService type
  - Blood pressure: simulation/control surface — no standalone IC
  - Barometer: nRF-adjacent path — BMP280 not confirmed on SCP side
  - Gyroscope: BSP ghost — no physical IC
  - Magnetometer/compass: BSP ghost candidate — no confirmed IC
  - ALS/proximity: vendor/bridge path — no standalone IC
  - Step counter: confirmed published and active
  - Sensor HAL blob dependency chain
  Status: PARTIALLY POPULATED — complete SensorService full published list


Section 05 — Display, GPU & Graphics Stack
  Scope:
  - LCM/panel identity: ST7701S confirmed (NT35695B superseded)
  - DSI command mode parameters: timing, resolution 480x640, framebuffer
  - LCD backlight path: MT6370 BLED
  - PowerVR GE8320 graphics stack: EGL, GLES, Vulkan, OpenCL
  - GPU HAL chain: gralloc, hwcomposer, allocator, mapper, composer
  - egl.cfg content — verified
  - MEOW driver codename confirmed
  - GPU OPP table from Section 6 cross-reference
  - SurfaceFlinger configuration
  - Hardware composer version and capabilities
  - HWUI configuration
  - mali.ko commented out — confirmed not loaded
  - gralloc.mt6765.so and hwcomposer.mt6765.so confirmed present
  - All GPU-related vendor blobs with sizes and SHA256
  Status: PARTIALLY POPULATED — complete blob inventory and egl.cfg verification


Section 06 — Camera Stack — HAL, Blobs, Sensor Identities & CCU
  Scope:
  - Camera HAL: android.hardware.camera.provider@2.4 internal/0
  - Camera sensor inventory: GC2385 (main), GC030A (front), BF2553L (third)
  - Camera IC identification confirmed via I2C2 and I2C6
  - Autofocus: MAINAF on I2C2 0x0C
  - EEPROM: CAM_CAL_DRV on I2C2 0x50 and I2C6 0x50
  - SENINF1-4 lane assignments
  - CCU hardware sensor I2C bindings
  - Flashlight: MT6370 FLED path — physical mapping open lead
  - Camera HAL blob complete dependency chain
  - Sony PDAF libraries: loader-required scaffold — carry on first boot
  - libcamalgo.gyro.so: carry as camera HAL dependency
  - All camera-related vendor blobs with sizes and SHA256
  - ROM disposition for each camera blob
  Status: PARTIALLY POPULATED — complete blob inventory and SENINF assignment


Section 07 — Audio Stack — Policy, HAL, Parameters & Routes
  Scope:
  - Primary audio HAL: audio.primary.mt6765.so
  - All 8 audio output devices and 8 audio input devices
  - Audio policy configuration XML — complete
  - Audio effects chain — all 8 soundfx libraries
  - All 99 audio_param/ XML files — documented by category
  - Aurisys parameter configuration
  - Active audio paths: speaker, BT SCO, BT A2DP, USB audio, cellular voice
  - BSP ghost audio routes: earpiece, wired headset, dock, HDMI
  - FM audio PCM route — declared; RF path open
  - ANC_Record route — open physical lead
  - Back microphone directional mapping — open physical lead
  - BES Loudness active: persist.vendor.audiohal.besloudness_state=1
  - ROM build requirements: complete file list for carry
  Status: SUBSTANTIALLY POPULATED — verify complete blob inventory


Section 08 — Radio, Telephony & Modem Software Stack
  Scope:
  - MD1 modem firmware: MOLY.LR12A.R3.MP.V107.4
  - RIL daemon: mtkfusionrild with libmtk-ril.so
  - CCCI channel inventory — all device nodes
  - Telephony framework: phone, IMS, mtkIms, carrier_config binder services
  - VoLTE/IMS/WFC service set: all 5 VoLTE daemons
  - SIM/UICC: single SIM, USIM type, gsm.sim.state=LOADED
  - Radio HAL chain: android.hardware.radio + MTK RadioEx + RadioOp
  - APN database files
  - APDB modem database files
  - GSM muxd: gsm0710muxd on /dev/ttyC0
  - Network interface: ccmni0 active cellular data
  - All radio-related vendor blobs and daemons
  - IPSec/Strongswan libraries: analysis status
  - Hashed ICCID persistence in NVRAM — security implication
  Status: PARTIALLY POPULATED — complete daemon and blob inventory


Section 09 — Connectivity Software Stack — Wi-Fi, Bluetooth, GNSS & WMT
  Scope:
  - WMT/connsys parent: wmt_drv.ko — child dependency chain
  - Wi-Fi: wlan_drv_gen4m.ko, gen4m firmware, wpa_supplicant, hostapd
  - Wi-Fi firmware: WIFI_RAM_CODE_soc1_0_1_1.bin, soc1_0_ram_wifi_1_1_hdr.bin
  - Bluetooth: bt_drv.ko, BT_FW.cfg, soc1_0_ram_bt_1_1_hdr.bin
  - GNSS: gps_drv.ko, MNL.bin, mnld, mtk_agpsd
  - WMT device nodes inventory
  - Connectivity init RC analysis
  - All connectivity firmware files with SHA256
  - AGPS configuration
  - Wi-Fi/BT/GPS feature XML declarations
  Status: SUBSTANTIALLY POPULATED — verify firmware SHA256 and complete inventory


Section 10 — VNDK-29, VNDK-SP-29 & Vendor Blob Inventory
  Scope:
  - Complete VNDK-29 library list: 64-bit and 32-bit sets
  - Complete VNDK-SP-29 library list: 39 files + hw/ subdirectory
  - ODM-modified VNDK libraries identified (different symbol tables)
  - Vendor blob classification: required / optional / ghost
  - Blob dependency map — all 1,468 vendor files
  - dlopen call targets identified via Ghidra — status per blob
  - patchelf rpath correction targets
  - Library namespace declarations in /vendor/etc/ld.config.*.txt
  Status: PARTIALLY POPULATED — requires Ghidra analysis completion


Section 11 — Firmware Blob Catalog & Hardware Identity Map
  Scope:
  - All firmware files in /vendor/firmware/ with size, SHA256, hardware owner
  - 187_187.bin: nRF52832 DFU reference image — confirmed /system/firmware/
  - WIFI_RAM_CODE, BT firmware, WMT_SOC.cfg
  - MNL.bin GNSS firmware
  - rgx.fw PowerVR GPU firmware
  - scp1/scp2 firmware — tinysys-loader-CM4_A confirmed
  - sspm_1/sspm_2 firmware
  - spmfw — MTK SPM firmware
  - All camera-related firmware and calibration files
  - Hardware identity cross-reference: every firmware blob to its IC
  Status: PARTIALLY POPULATED — complete SHA256 inventory


Section 12 — APK Inventory — All 127 Packages, Metadata & Disposition
  Scope:
  - All 127 APKs across all partitions: system, vendor, product, data
  - Per APK: path, package name, UID, version, permissions, ROM disposition
  - Classification: KEEP / STRIP / REPLACE / ANALYZE
  - System-UID (UID 1000) packages — complete list with security classification
  - priv-app packages — complete list
  - ODM/Wiite packages — complete list
  - Standard AOSP packages — confirmed functional mapping
  - User-installed packages on research device — not carried to clean ROM
  Status: SUBSTANTIALLY POPULATED — complete disposition for all 127


Section 13 — ODM Control Layer — Wiite/nRF Framework Integration
  Scope:
  - wiite_corp driver: all 34 sysfs attributes with permissions and semantics
  - wiite_con driver: pin_set, irq_test surfaces
  - UART1/ttyS1 protocol: known command bytes and event types
  - nRF52832 chip_id 0x70, firmware version 48059
  - Android health APK set that interfaces with nRF bridge
  - I2C4 wiite_cp@50 driver binding — confirmed
  - init.rc permission grants for wiite_corp_ctrl attributes
  - 32-position config bitmap semantics — open protocol lead
  - TWIS register map — open protocol lead
  - Full UART1 command map — open protocol lead
  - BLE command runtime behavior — open protocol lead
  - Framework integration: WiiteCommon, WiiteDeviceDaemon, health UART apps
  - ROM strategy: replace ODM bridge daemon with clean implementation
  Status: SUBSTANTIALLY POPULATED — protocol leads require RE completion


Section 14 — SELinux Policy Map
  Scope:
  - Precompiled_sepolicy location and decompile status
  - Policy version: 29.0 (Android 10)
  - All ODM-specific SELinux domains
  - Security contexts for wiite_corp_ctrl, wiite_con_ctrl, ttyS1
  - Known AVC denials in stock: system_app accessing wiite_corp_ctrl/command
  - ODM policy divergence from AOSP — what was added
  - ROM strategy: rebuild from AOSP base with minimum required ODM additions
  - pms_sysapp_grant_permission_list.txt — confirmed referenced in framework
  Status: PARTIALLY POPULATED — requires precompiled_sepolicy decompile


Section 15 — Build Specification — BoardConfig, device.mk & Properties
  Scope:
  - Complete BoardConfig.mk: all verified constants from hardware database
  - BOARD_KERNEL_CMDLINE: verbatim from verified live capture
  - BOARD_KERNEL_BASE, BOARD_KERNEL_OFFSET, BOARD_TAGS_OFFSET, BOARD_DTB_OFFSET
  - BOARD_BOOTIMAGE_PARTITION_SIZE and all partition sizes
  - TARGET_BOARD_PLATFORM=mt6765, TARGET_CPU_ABI=arm64-v8a
  - FBE: fileencryption=aes-256-xts, filenames mode aes-256-cts
  - fstab: md_udc as /metadata, ghost entries stripped
  - device.mk: PRODUCT_PROPERTY_OVERRIDES verified from live device
  - GMS fingerprint spoof property
  - BES Loudness property
  - BT A2DP standby property
  - PRODUCT_COPY_FILES: complete list derived from verified data
  - Treble configuration: PRODUCT_FULL_TREBLE_OVERRIDE, VNDK_VERSION
  Status: PARTIALLY POPULATED — PRODUCT_COPY_FILES requires completion


Section 16 — Security & Threat Analysis — ODM APKs & Framework Patches
  Scope:
  - Stopwatch.apk: complete static analysis — confirmed surveillance agent
  - AdupsFota.apk: confirmed C2 architecture with fota5p.adups.com endpoints
  - WearDevicemsg.apk: confirmed com.nasa.cook SDK with device fingerprinting,
    .honor hidden directory (s.smali line 1054), chmod 777 (lines 323-344),
    XOR encryption, JSON HTTP POST exfiltration — Google malware flag confirmed
  - com.tiamaes.watch.activation: analysis pending
  - Configuration.isSpecialApp() framework patch: documented
  - WiitePackageManagerUtil: documented
  - pms_sysapp_grant_permission_list.txt: referenced in framework
  - HeilsFaceUnlockDM101: analysis pending
  - IPSec/Strongswan library analysis: pending Ghidra
  - services.jar and mediatek-services.jar Wiite strings
  - Hashed ICCID NVRAM persistence
  - iAmCdRom.iso: purpose unknown — requires analysis
  - All remaining system-UID APKs: analysis pending
  - CVE filing status: CVE Request 2025814 active
  Status: SUBSTANTIALLY POPULATED — pending items listed above


Section 17 — Software Negative Register & BSP Ghost Software
  Scope:
  - Software routes that have no physical endpoint on this device
  - Ghost RC service references: md1dsp, md1arm7, md3img, odmdtbo mounts
  - Dead code: iAmCdRom.iso mount, /FWUpgradeInit.rc reference
  - Legacy platform trigger: ro.board.platform=mt6753 (wrong platform)
  - A/B preloader symlinks created on A-only device
  - Mali GPU: driver commented out — all Mali software routes dead
  - Goodix GT1151: firmware present but never loaded — Hynitron is real
  - rt5509 smart PA: referenced in audio config — not physically present
  - MATV: HAL/config residue — not on this device
  - NFC software stack: declared in some manifests — no hardware
  - Dual-SIM VINTF variants: only single-SIM runtime confirmed
  - Earpiece/headphone/dock audio routes: declared only — no physical endpoint
  - FM radio software route: audio route exists — RF path not confirmed
  - HDMI audio route: declared — no hardware
  - Keyboard/jogball LED software references: no physical hardware
  - Wireless charging software references: no hardware
  - All software ghosts cross-referenced to hardware database Section 12
  Status: PARTIALLY POPULATED — consolidate from all sections


Section 18 — ROM Build Gate & Verification Checklist
  Scope:
  - Section completion gate: all 17 sections must be marked complete
  - Binary audit gate: blob dependency map, dlopen targets, patchelf list
  - Framework audit gate: services.jar, framework.jar, KEEP APK audit
  - Hardware parity gate: all Section 15 values traceable to hardware evidence
  - App disposition gate: all 127 APKs have KEEP/STRIP/REPLACE decision
  - Pre-build checklist
  - Post-build verification: boot, HAL registration, sensors, camera, audio,
    radio, crypto, FBE, tcpdump clean confirmation
  Status: FRAMEWORK DEFINED — all gates carry forward from existing Section 17


################################################################################
#  SECTION DEPENDENCY MAP
################################################################################

Section 00 → consumed by all sections (partition and source authority)
Section 01 → consumed by Sections 02, 04, 08, 09, 13 (service start graph)
Section 02 → consumed by Sections 04, 05, 06, 07, 08, 09, 15 (HAL declarations)
Section 03 → consumed by Sections 10, 15, 16, 18 (crypto chain and isolation)
Section 04 → consumed by Sections 13, 15, 17 (sensor stack and nRF bridge)
Section 05 → consumed by Sections 15, 17 (GPU/display build requirements)
Section 06 → consumed by Sections 15, 17 (camera blob requirements)
Section 07 → consumed by Sections 15, 17 (audio config carry list)
Section 08 → consumed by Sections 15, 17 (radio daemons and blobs)
Section 09 → consumed by Sections 15, 17 (connectivity firmware and blobs)
Section 10 → consumed by Sections 15, 18 (VNDK set for build)
Section 11 → consumed by Sections 04, 05, 06, 09, 15 (firmware file list)
Section 12 → consumed by Sections 16, 18 (APK disposition)
Section 13 → consumed by Sections 15, 16, 17, 18 (ODM control layer)
Section 14 → consumed by Sections 15, 18 (SELinux for ROM)
Section 15 → consumed by Section 18 (build specification complete)
Section 16 → consumed by Sections 12, 18 (security decisions)
Section 17 → consumed by Sections 15, 18 (what NOT to carry)
Section 18 → final gate (nothing builds until all gates clear)


################################################################################
#  CROSS-REFERENCE TO HARDWARE DATABASE
################################################################################

SW Section 00 ← HW Section 2  (partition map authority)
SW Section 01 ← HW Section 3  (boot chain and firmware execution)
SW Section 02 ← HW Sections 6-9 (HAL owners per hardware domain)
SW Section 03 ← HW Section 11 (DXCC, RPMB, security substrate)
SW Section 04 ← HW Section 7  (nRF52832 and sensor plane)
SW Section 05 ← HW Section 8  (display and GPU hardware)
SW Section 06 ← HW Section 8  (camera hardware)
SW Section 07 ← HW Section 8  (audio hardware)
SW Section 08 ← HW Section 9  (baseband hardware)
SW Section 09 ← HW Section 9  (connectivity hardware)
SW Section 10 ← HW Sections 1-12 (all hardware domains)
SW Section 11 ← HW Sections 3, 7, 8, 9 (firmware execution domains)
SW Section 12 ← HW Section 11 (trust admission surface)
SW Section 13 ← HW Section 7  (nRF52832 coprocessor plane)
SW Section 14 ← HW Sections 7, 10, 11 (security boundaries)
SW Section 15 ← HW Section 1  (silicon identity, offsets, constants)
SW Section 16 ← HW Section 11 (hardware trust admission)
SW Section 17 ← HW Section 12 (negative hardware register)
SW Section 18 ← HW Sections 1-12 (all hardware parity verification)


################################################################################
#  STATUS LEGEND
################################################################################

COMPLETE         : All entries verified with cited evidence. No open items.
SUBSTANTIALLY POPULATED : Primary content present. Verification and expansion needed.
PARTIALLY POPULATED     : Structure and key entries present. Significant content missing.
FRAMEWORK DEFINED       : Section structure and scope defined. Content to be filled.
PENDING                 : Not yet started.


################################################################################
#  CURRENT STATUS SUMMARY
################################################################################

Section 00 — SUBSTANTIALLY POPULATED
Section 01 — SUBSTANTIALLY POPULATED
Section 02 — PARTIALLY POPULATED
Section 03 — PARTIALLY POPULATED
Section 04 — PARTIALLY POPULATED
Section 05 — PARTIALLY POPULATED
Section 06 — PARTIALLY POPULATED
Section 07 — SUBSTANTIALLY POPULATED
Section 08 — PARTIALLY POPULATED
Section 09 — SUBSTANTIALLY POPULATED
Section 10 — PARTIALLY POPULATED
Section 11 — PARTIALLY POPULATED
Section 12 — SUBSTANTIALLY POPULATED
Section 13 — SUBSTANTIALLY POPULATED
Section 14 — PARTIALLY POPULATED
Section 15 — PARTIALLY POPULATED
Section 16 — SUBSTANTIALLY POPULATED
Section 17 — PARTIALLY POPULATED
Section 18 — FRAMEWORK DEFINED


################################################################################
#  KEY ADDITIONS VERSUS ORIGINAL DATABASE
################################################################################

Section 03 (Cryptographic Stack) — expanded from original Section 3
  WearDevicemsg com.nasa.cook SDK analysis is now complete evidence
  Ghidra analysis of ODM-modified libs still needed

Section 16 (Security Analysis) — expanded from original Section 16
  WearDevicemsg full analysis complete with exact smali line references
  Google malware flag on WearDevicemsg confirmed
  CVE Request 2025814 filed with supplemental evidence June 9 2026
  AdupsFota confirmed at /product/priv-app/AdupsFota/AdupsFota.apk
  Three independent telemetry frameworks confirmed on same device

Section 17 (Software Negative Register) — NEW
  Consolidates all software-ghost and dead-route declarations
  Mirrors hardware database Section 12 methodology
  Prevents BSP software residue from being carried into clean ROM

Section 18 (ROM Build Gate) — replaces original Section 17
  Carries all original gate items forward
  Adds WearDevicemsg and Adups confirmed stripped requirement
  Adds tcpdump clean boot confirmation requirement


################################################################################
#  END SOFTWARE DIGITAL TWIN SECTION DIRECTORY
################################################################################
