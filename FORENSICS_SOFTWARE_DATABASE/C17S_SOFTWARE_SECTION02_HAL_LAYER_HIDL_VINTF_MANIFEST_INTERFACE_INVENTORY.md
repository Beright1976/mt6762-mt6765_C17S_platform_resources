################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 02 — HAL LAYER, HIDL/VINTF MANIFEST & INTERFACE INVENTORY
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 02 — HAL / VINTF / INTERFACE AUTHORITY MAP
# Date        : 2026-06-14 live source-pack, runtime, SELinux, and binary integration pass
################################################################################


################################################################################
# SCOPE
################################################################################

This section documents the HAL authority layer for the C17S software digital twin.
It is the canonical authority section for VINTF manifests, HIDL interface declarations,
runtime `lshal` registration, HAL-to-RC mapping, HAL-to-process mapping, SELinux
hwservice/service namespace mapping, HAL binary identity, passthrough implementation
inventory, and high-risk HAL-adjacent IPC boundaries.

This section covers:
- Complete vendor device manifest authority from `/vendor/etc/vintf/manifest.xml`.
- Vendor VINTF fragment authority under `/vendor/etc/vintf/manifest/`.
- Complete ODM VINTF variant inventory under `/odm/etc/vintf/`.
- Complete system framework VINTF manifest and fragment authority under `/system/etc/vintf/`.
- Compatibility matrix authority: target FCM/device matrix and framework matrices.
- Runtime HIDL registration from `lshal`.
- Binderized service inventory and passthrough implementation inventory.
- HAL service declaration mapping back to init RC files.
- Live `init.svc.*` state for HAL, vendor, radio, IMS, GNSS, connectivity, DRM, security, and media services.
- Live process identity, UID, PID, PPID, SELinux domain, and command line for HAL-related processes.
- SELinux `hwservice_contexts`, `vndservice_contexts`, and framework Binder `service_contexts`.
- HAL binary path, file size, SELinux exec label where captured, and SHA256 identity.
- Hardware cross-reference boundaries for radio, secure element, sensor, camera, WMT/connectivity, USB, GPU/display, DRM/security, and Wiite/nRF-adjacent control.
- Clean-ROM decisions: keep, replace, harden, strip, or carry as ghost/dependency.

This section does not decompile APKs, native libraries, or firmware payloads. It records
the authority layer that makes those later analyses reachable. APK behavior belongs to
Sections 12, 13, and 16. Native-library dependency closure belongs to Section 10.
Sensor protocol and nRF bridge detail belongs to Section 04. Radio payload and modem
exfiltration analysis belongs to Section 08 and Section 16.


################################################################################
# SOURCE AUTHORITY
################################################################################

TIER 1 : Live rooted C17S runtime snapshot
         - `/data/local/tmp/section02_runtime_snapshot.txt` captured from the device.
         - Authority for `getprop`, `init.svc.*`, `lshal`, `service list`, `ps -A -Z`,
           live hwservice registration, runtime process identity, and Binder service list.

TIER 2 : Live source pack
         - `/data/local/tmp/section02_hal_pack.tar.gz` pulled from the device.
         - Authority for VINTF XML files, init RC files, SELinux context files,
           HAL service binaries under captured `bin_hw`, and HAL implementation libraries
           under captured `lib/hw` and `lib64/hw`.

TIER 3 : Targeted live follow-up captures
         - Secure-element runtime, package, dumpsys, and library-string captures.
         - NFC/eSE ghost node check.
         - SELinux hwservice/service context high-value maps.
         - Live HAL binary hash inventory.
         - Shell-execution boundary check and invalid-capture trap correction.

TIER 4 : Section 00 / Section 01 / hardware digital-twin cross-reference
         - Section 00 provides partition, source, mount, and current research-state authority.
         - Section 01 provides init service graph and RC file authority.
         - Hardware database prevents HAL declarations from being promoted to physical hardware truth.

FINAL  : Beright / Albert acceptance after live re-verification.

Source artifact hashes:
- `section02_hal_pack.tar.gz` SHA256: `82b6d16e5fd2a6c71598712f26573c9be2af2dc3858c68b50c90c79b435edb49`
- `section02_runtime_snapshot.txt` SHA256: `6822256af4d8d9f48832bc84ea30515d4f35efb4e1eab16fe69ce8e3ba692bf9`
- `C17S_SOFTWARE_SECTION00_FINAL.md` SHA256: `74f9979548846e2b76140fcb217cf8fb271a27b01c8e1b779380c93cce66a29f`
- `C17S_SOFTWARE_SECTION01_FINAL.md` SHA256: `6caa50c8a4bf13551efd7c7c226f46634ec1d1ebbfa2a698523cfc2a066f91df`
- `C17S_LOKMAT5MAX_HARDWARE_DATABASE.md` SHA256: `66c96aa30d7238633dac46979c2ff6bf62474d5d9472c94d6b33dfebdc8b84dd`


################################################################################
# RULES
################################################################################

No stale values.
No borrowed values from similar MTK devices.
No assumption promoted to software truth.
No software declaration promoted to hardware truth.
No VINTF declaration promoted to runtime registration without `lshal` proof.
No `lshal` registration promoted to physical hardware truth without hardware cross-reference.
No RC service promoted to HAL interface truth without VINTF or `lshal` evidence.
No binary existence promoted to runtime execution without live process or service-state evidence.
No `app_process`, `sh`, `su`, Magisk, or adb capture process promoted to stock behavior.
No product APK privilege promoted to HAL namespace without Binder/HwBinder/service evidence.
No original database entry is deleted; every original entry is verified, expanded,
corrected, moved, demoted, left open, or disproved by stronger evidence.
Layer-specific truths remain layer-specific.

Classification vocabulary used in this section:

[VERIFIED]                       Proven by source pack, runtime snapshot, targeted live command, or cited prior section.
[ACTIVE_RUNTIME]                 Registered/running in current live runtime.
[DECLARED]                       Present in manifest, RC, SELinux context, or matrix but not necessarily active.
[VINTF_DECLARED]                 Present in a VINTF manifest or fragment.
[VINTF_VARIANT_LIBRARY]          Present as one of the ODM SIM/eSE manifest variants.
[REGISTERED_HAL]                 Present in `lshal` binderized or passthrough runtime output.
[PASSTHROUGH_IMPL]               Present as a `lib/hw` or `lib64/hw` implementation and/or `lshal` passthrough entry.
[RC_DECLARED]                    Present in an init RC service block.
[PROCESS_ACTIVE]                 Present in live `ps -A -Z` output.
[HWSERVICE_CONTEXT_DECLARED]     Present in SELinux hwservice context map.
[BINDER_SERVICE_DECLARED]        Present in SELinux Binder service context map or `service list`.
[ABSENT_IN_SOURCE_PACK]          Referenced but absent from the captured source pack.
[ABSENT_NAMESPACE_ROOT]          Context or VINTF root absent in current capture.
[BSP_GHOST]                      BSP-declared route with no active physical/runtime proof in this section.
[ODM_RADIO_TOPOLOGY_SELECTOR]    Manifest profile selecting single/dual/triple/quad SIM and secure element topology.
[SINGLE_SIM_SELECTED]            The active vendor manifest selects one SIM/radio path.
[SIM_SECURE_ELEMENT_SELECTED]    Active secure-element HAL instance is UICC/SIM1.
[NOT_NFC_PROOF]                  Secure-element or eSE string does not prove physical NFC.
[SECURITY_CRITICAL]              Boundary with security material, radio, root/system control, OTA, modem, secure element, or DRM implications.
[CONTROL_BOUNDARY]               Boundary where HAL/service/daemon IPC can carry command/control traffic.
[EXFIL_ROUTE_CANDIDATE]          Route has privileges/IPC/storage/radio adjacency sufficient for later Section 16 review; endpoint/payload not proven here.
[RESEARCH_ARTIFACT]              Created by adb/su/Magisk/tooling, not stock runtime evidence.
[INVALID_CAPTURE]                Command failed or overmatched; retained to prevent future misclassification.
[KEEP]                           Required for first clean boot or proven active hardware function.
[REPLACE]                        Replace with clean AOSP or purpose-built implementation.
[HARDEN]                         Keep only with reduced permissions, SELinux confinement, and audited IPC.
[STRIP]                          Remove when no active hardware/runtime requirement exists.
[MOVE]                           Belongs primarily to a later software section.
[OPEN_LEAD]                      Real evidence but unresolved beyond Section 02 scope.


################################################################################
# 2.1 — CAPTURE IDENTITY
################################################################################

Runtime identity from Section 02 snapshot:

ro.hardware                     : mt6762 [VERIFIED]
ro.boot.hardware                : mt6762 [VERIFIED]
ro.board.platform               : mt6765 [VERIFIED]
ro.vendor.mediatek.platform     : MT6765 [VERIFIED]
ro.vendor.rc                    : /vendor/etc/init/hw/ [VERIFIED]

Interpretation:
- MT6762 / MT6762V-WB is the physical/kernel hardware identity.
- MT6765 / MT6765 BSP is the software HAL/platform identity.
- Both identities are true only in their own layers.
- HAL, VINTF, and init service naming are dominated by MT6765 BSP paths.


################################################################################
# 2.2 — SECTION 02 SOURCE-PACK INVENTORY
################################################################################

| Source pack class | Count |
|---|---|
| Vendor VINTF XML files | 4 |
| System VINTF XML files | 12 |
| ODM VINTF variant XML files | 8 |
| Vendor init RC files | 85 |
| System init RC files | 62 |
| Product init RC files | 0 |
| Vendor SELinux files | 12 |
| System SELinux files | 9 |
| Vendor bin/hw binaries | 35 |
| System bin/hw binaries | 2 |
| Vendor lib/hw + lib64/hw implementations | 94 |
| System lib/hw + lib64/hw implementations | 4 |
Product contribution:
- `/product/etc/vintf` absent in live context. [ABSENT_NAMESPACE_ROOT]
- Product init files absent in captured Section 02 source pack. [ABSENT_NAMESPACE_ROOT]
- Product SELinux hwservice/service context roots absent in targeted live context capture. [ABSENT_NAMESPACE_ROOT]

Interpretation:
Product APKs and product privileged apps still matter later, but product does not provide
a separate VINTF/HAL namespace authority root in this Section 02 capture.


################################################################################
# 2.3 — VINTF FILE AUTHORITY
################################################################################

| File | Bytes | Lines | SHA256 |
|---|---|---|---|
| vendor/etc_vintf/compatibility_matrix.xml | 1167 | 40 | 92d0d66a855062b088ca140a5d88732250474c4ea70fae92f5cc45e2ce4b4a74 |
| vendor/etc_vintf/manifest.xml | 18488 | 536 | ccd076001e731956f76e5cb20afe5aefb172b155954f97a26145ee3c03713249 |
| vendor/etc_vintf/manifest/android.hardware.cas@1.1-service.xml | 323 | 11 | 8800d467c218775f624eeea8b808cefc3cac8fb35c0998e3e5ea3925fe85848f |
| vendor/etc_vintf/manifest/android.hardware.gpu@1.0-service.xml | 337 | 11 | 568003516856284f3d49db489f404eacb06301bb80a1675020e06364dcb9b92d |
| system/etc_vintf/compatibility_matrix.1.xml | 71968 | 2287 | c615e6da0f2eb70bb6eb4f04aff141de27c99055c93f1f35aa3310b2bccd9ebb |
| system/etc_vintf/compatibility_matrix.2.xml | 74191 | 2359 | 861fc3e91bb7086f77c84ff475bd1e2378622f5c93e3e25bab0a4640628df617 |
| system/etc_vintf/compatibility_matrix.3.xml | 90344 | 2883 | a898c517009d0f03bde580bb125dc56729477c52a26cf4d8d2bea708c1373a20 |
| system/etc_vintf/compatibility_matrix.4.xml | 113177 | 3641 | 84f47c4c87e99d08172a83c07fb9148b30ab971377dd09e6b6d84cf40b4e6c31 |
| system/etc_vintf/compatibility_matrix.device.xml | 496 | 16 | 467f143d92047ff5977679f32dbd42df23f79b63f3edcb90e8b991b4427ff8fa |
| system/etc_vintf/compatibility_matrix.legacy.xml | 71977 | 2287 | 1b24af46222e43bb4fab897a0c3d526d11edfc73a7eba1067631c996548df9a5 |
| system/etc_vintf/manifest.xml | 3027 | 98 | 44f3bd5a52117e12104521ab3abe3cea78c2c7767114a6e3641f43e5f25d7aad |
| system/etc_vintf/manifest/android.frameworks.stats@1.0-service.xml | 306 | 11 | a651013edf8ac7d2804c53ba05e8eb27794d0dd5db66cc509c73351f9f2bab01 |
| system/etc_vintf/manifest/android.hidl.allocator@1.0-service.xml | 307 | 11 | 9e6fee7d22163cfdb0392e10b1dee1f0d7efb24d4f5c3b50adddc3368dc4f11e |
| system/etc_vintf/manifest/android.system.suspend@1.0-service.xml | 312 | 11 | 8fa3da936a54184f2bf0652bbeb9c5d3c0c290bd508276f599e9bd4213846dd0 |
| system/etc_vintf/manifest/manifest_android.frameworks.cameraservice.service@2.0.xml | 330 | 11 | de9f639a23e72c2a935ff5a9b1356ea51b16f2203ae979f23ac9c634019830c6 |
| system/etc_vintf/manifest/manifest_media_c2_software.xml | 317 | 11 | e526447e29999bd58f609805ef77b70c1e4456cf19e5e682f07ef771013b3e05 |
| odm/etc_vintf/manifest_dsds.xml | 2140 | 58 | 186288dbea8f0dfed1cea2dcd2509a9f0501ea40f6f2989bd71cd0fa9452fd43 |
| odm/etc_vintf/manifest_dsds_se.xml | 2283 | 63 | 52ccc2d84a94107282264f6527c5fd43e80e6c017548756bd0c481326934fbcc |
| odm/etc_vintf/manifest_dsds_se_ese.xml | 2321 | 64 | de9baf6f13ea480787a7fa3cdaf16af612127bc45d2b3b7161bd45c6e9cfebe9 |
| odm/etc_vintf/manifest_qsqs.xml | 3284 | 85 | 2ab7d8c8885812faa9c37867dcd3da5eb679d7155b82bdde49680eadf4352ccc |
| odm/etc_vintf/manifest_ss.xml | 1473 | 42 | f73a456b31268b3a5944636fcefd5f9193b6c553c45a7fb712e7a9b820c08eca |
| odm/etc_vintf/manifest_ss_se.xml | 1578 | 46 | 5955454c7e9a122b11693ab10245700f7ec5805277a34f7cf5bf2fd1539b60ec |
| odm/etc_vintf/manifest_ss_se_ese.xml | 1616 | 47 | 363838e80ba0485b8f2d48b55cdd83caedde336252a879b86665e4cd4d1829f3 |
| odm/etc_vintf/manifest_tsts.xml | 2617 | 69 | c8f94f1dc5bb5f35d0e39636b8dbbd2e845f6f92c435449dc471fe124200fbf9 |
Primary vendor device manifest:
- `/vendor/etc/vintf/manifest.xml`
- target-level: 4
- type: device
- root input comment references `manifest_ss_se.xml`, making the selected radio/secure-element profile single-SIM + SIM secure element.

Vendor device compatibility matrix:
- `/vendor/etc/vintf/compatibility_matrix.xml`
- Declares framework HAL requirements and vendor/system SDK/VNDK boundary.


################################################################################
# 2.4 — COMPLETE VENDOR DEVICE MANIFEST HAL DECLARATION TABLE
################################################################################

| Source | HAL name | Format | Transport | Version | Interface | Instance | FQName |
|---|---|---|---|---|---|---|---|
| vendor/etc_vintf/manifest.xml | android.hardware.audio | hidl | hwbinder | 5.0 | IDevicesFactory | default | @5.0::IDevicesFactory/default |
| vendor/etc_vintf/manifest.xml | android.hardware.audio.effect | hidl | hwbinder | 5.0 | IEffectsFactory | default | @5.0::IEffectsFactory/default |
| vendor/etc_vintf/manifest.xml | android.hardware.bluetooth | hidl | hwbinder | 1.0 | IBluetoothHci | default | @1.0::IBluetoothHci/default |
| vendor/etc_vintf/manifest.xml | android.hardware.bluetooth.audio | hidl | hwbinder | 2.0 | IBluetoothAudioProvidersFactory | default | @2.0::IBluetoothAudioProvidersFactory/default |
| vendor/etc_vintf/manifest.xml | android.hardware.camera.provider | hidl | hwbinder | 2.4 | ICameraProvider | internal/0 | @2.4::ICameraProvider/internal/0 |
| vendor/etc_vintf/manifest.xml | android.hardware.configstore | hidl | hwbinder | 1.1 | ISurfaceFlingerConfigs | default | @1.1::ISurfaceFlingerConfigs/default |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.0 | ICryptoFactory | default | @1.0::ICryptoFactory/default |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.0 | IDrmFactory | default | @1.0::IDrmFactory/default |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.2 | ICryptoFactory | clearkey | @1.2::ICryptoFactory/clearkey |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.2 | ICryptoFactory | widevine | @1.2::ICryptoFactory/widevine |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.2 | IDrmFactory | clearkey | @1.2::IDrmFactory/clearkey |
| vendor/etc_vintf/manifest.xml | android.hardware.drm | hidl | hwbinder | 1.2 | IDrmFactory | widevine | @1.2::IDrmFactory/widevine |
| vendor/etc_vintf/manifest.xml | android.hardware.gatekeeper | hidl | hwbinder | 1.0 | IGatekeeper | default | @1.0::IGatekeeper/default |
| vendor/etc_vintf/manifest.xml | android.hardware.gnss | hidl | hwbinder | 1.1 | IGnss | default | @1.1::IGnss/default |
| vendor/etc_vintf/manifest.xml | android.hardware.gnss | hidl | hwbinder | 2.0 | IGnss | default | @2.0::IGnss/default |
| vendor/etc_vintf/manifest.xml | android.hardware.graphics.allocator | hidl | hwbinder | 2.0 | IAllocator | default | @2.0::IAllocator/default |
| vendor/etc_vintf/manifest.xml | android.hardware.graphics.composer | hidl | hwbinder | 2.1 | IComposer | default | @2.1::IComposer/default |
| vendor/etc_vintf/manifest.xml | android.hardware.graphics.mapper | hidl | passthrough | 2.1 | IMapper | default | @2.1::IMapper/default |
| vendor/etc_vintf/manifest.xml | android.hardware.health | hidl | hwbinder | 2.0 | IHealth | default | @2.0::IHealth/default |
| vendor/etc_vintf/manifest.xml | android.hardware.keymaster | hidl | hwbinder | 4.0 | IKeymasterDevice | default | @4.0::IKeymasterDevice/default |
| vendor/etc_vintf/manifest.xml | android.hardware.light | hidl | hwbinder | 2.0 | ILight | default | @2.0::ILight/default |
| vendor/etc_vintf/manifest.xml | android.hardware.media.omx | hidl | hwbinder | 1.0 | IOmx | default | @1.0::IOmx/default |
| vendor/etc_vintf/manifest.xml | android.hardware.media.omx | hidl | hwbinder | 1.0 | IOmxStore | default | @1.0::IOmxStore/default |
| vendor/etc_vintf/manifest.xml | android.hardware.memtrack | hidl | hwbinder | 1.0 | IMemtrack | default | @1.0::IMemtrack/default |
| vendor/etc_vintf/manifest.xml | android.hardware.power | hidl | hwbinder | 1.3 | IPower | default | @1.3::IPower/default |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.2 | IRadio | em1 | @1.2::IRadio/em1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.2 | IRadio | imsAospSlot1 | @1.2::IRadio/imsAospSlot1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.2 | IRadio | se1 | @1.2::IRadio/se1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.2 | IRadio | slot1 | @1.2::IRadio/slot1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.2 | ISap | slot1 | @1.2::ISap/slot1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio | hidl | hwbinder | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| vendor/etc_vintf/manifest.xml | android.hardware.radio.config | hidl | hwbinder | 1.1 | IRadioConfig | default | @1.1::IRadioConfig/default |
| vendor/etc_vintf/manifest.xml | android.hardware.secure_element | hidl | hwbinder | 1.0 | ISecureElement | SIM1 | @1.0::ISecureElement/SIM1 |
| vendor/etc_vintf/manifest.xml | android.hardware.sensors | hidl | hwbinder | 2.0 | ISensors | default | @2.0::ISensors/default |
| vendor/etc_vintf/manifest.xml | android.hardware.thermal | hidl | hwbinder | 1.0 | IThermal | default | @1.0::IThermal/default |
| vendor/etc_vintf/manifest.xml | android.hardware.usb | hidl | hwbinder | 1.1 | IUsb | default | @1.1::IUsb/default |
| vendor/etc_vintf/manifest.xml | android.hardware.vibrator | hidl | hwbinder | 1.0 | IVibrator | default | @1.0::IVibrator/default |
| vendor/etc_vintf/manifest.xml | android.hardware.wifi | hidl | hwbinder | 1.3 | IWifi | default | @1.3::IWifi/default |
| vendor/etc_vintf/manifest.xml | android.hardware.wifi.hostapd | hidl | hwbinder | 1.1 | IHostapd | default | @1.1::IHostapd/default |
| vendor/etc_vintf/manifest.xml | android.hardware.wifi.supplicant | hidl | hwbinder | 1.2 | ISupplicant | default | @1.2::ISupplicant/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.atci | hidl | hwbinder | 1.0 | IAtcid | default | @1.0::IAtcid/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.camera.bgservice | hidl | hwbinder | 1.1 | IBGService | internal/0 | @1.1::IBGService/internal/0 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.engineermode | hidl | hwbinder | 1.2 | IEmd | EmHidlServer | @1.2::IEmd/EmHidlServer |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | AgpsDebugInterface | @1.0::ILbs/AgpsDebugInterface |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | AgpsInterface | @1.0::ILbs/AgpsInterface |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_agps2framework | @1.0::ILbs/mtk_agps2framework |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_debugService2mnld | @1.0::ILbs/mtk_debugService2mnld |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_framework2agps | @1.0::ILbs/mtk_framework2agps |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lbs_log_v2s | @1.0::ILbs/mtk_lbs_log_v2s |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_agps | @1.0::ILbs/mtk_lppe_socket_agps |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_bt | @1.0::ILbs/mtk_lppe_socket_bt |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_ipaddr | @1.0::ILbs/mtk_lppe_socket_ipaddr |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_lbs | @1.0::ILbs/mtk_lppe_socket_lbs |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_network | @1.0::ILbs/mtk_lppe_socket_network |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_sensor | @1.0::ILbs/mtk_lppe_socket_sensor |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_lppe_socket_wlan | @1.0::ILbs/mtk_lppe_socket_wlan |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_meta2mnld | @1.0::ILbs/mtk_meta2mnld |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_mnld2debugService | @1.0::ILbs/mtk_mnld2debugService |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_mnld2mtklogger | @1.0::ILbs/mtk_mnld2mtklogger |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_mnld2nlputils | @1.0::ILbs/mtk_mnld2nlputils |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.lbs | hidl | hwbinder | 1.0 | ILbs | mtk_mtklogger2mnld | @1.0::ILbs/mtk_mtklogger2mnld |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mms | hidl | hwbinder | 1.3 | IMms | default | @1.3::IMms/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkcodecservice | hidl | hwbinder | 1.1 | IMtkCodecService | default | @1.1::IMtkCodecService/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkpower | hidl | hwbinder | 1.0 | IMtkPerf | default | @1.0::IMtkPerf/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkpower | hidl | hwbinder | 1.0 | IMtkPower | default | @1.0::IMtkPower/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.mtkradioex | hidl | hwbinder | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.netdagent | hidl | hwbinder | 1.0 | INetdagent | default | @1.0::INetdagent/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.nvram | hidl | hwbinder | 1.1 | INvram | default | @1.1::INvram/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.power | hidl | hwbinder | 2.1 | IPower | default | @2.1::IPower/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.pq | hidl | hwbinder | 2.3 | IPictureQuality | default | @2.3::IPictureQuality/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.radio_op | hidl | hwbinder | 2.0 | IRadioOp | OpImsRILd1 | @2.0::IRadioOp/OpImsRILd1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.radio_op | hidl | hwbinder | 2.0 | IRadioOp | slot1 | @2.0::IRadioOp/slot1 |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.videotelephony | hidl | hwbinder | 1.0 | IVideoTelephony | default | @1.0::IVideoTelephony/default |
| vendor/etc_vintf/manifest.xml | vendor.mediatek.hardware.wifi.supplicant | hidl | hwbinder | 2.0 | ISupplicant | default | @2.0::ISupplicant/default |
| vendor/etc_vintf/manifest/android.hardware.cas@1.1-service.xml | android.hardware.cas | hidl | hwbinder | 1.1 | IMediaCasService | default | @1.1::IMediaCasService/default |
| vendor/etc_vintf/manifest/android.hardware.gpu@1.0-service.xml | vendor.mediatek.hardware.gpu | hidl | hwbinder | 1.0 | IGraphicExt | default | @1.0::IGraphicExt/default |

################################################################################
# 2.5 — ODM VINTF VARIANT MANIFEST LIBRARY
################################################################################

| Variant file | Bytes | Lines | SHA256 | HAL rows | Secure element instances | Radio / RadioEx instances | Classification |
|---|---|---|---|---|---|---|---|
| /odm/etc/vintf/manifest_dsds.xml | 2140 | 58 | 186288dbea8f0dfed1cea2dcd2509a9f0501ea40f6f2989bd71cd0fa9452fd43 | 23 | none | em1, em2, imsAospSlot1, imsAospSlot2, imsSlot1, imsSlot2, mtkAssist1, mtkAssist2, mtkCap1, mtkCap2, mtkEm1, mtkEm2, mtkRcs1, mtkRcs2, mtkSe1, mtkSe2, mtkSlot1, mtkSlot2, se1, se2, slot1, slot2 | [VINTF_VARIANT_LIBRARY] [OMAPI_DISABLED_VARIANT_NOT_SELECTED] |
| /odm/etc/vintf/manifest_dsds_se.xml | 2283 | 63 | 52ccc2d84a94107282264f6527c5fd43e80e6c017548756bd0c481326934fbcc | 24 | SIM1, SIM2 | em1, em2, imsAospSlot1, imsAospSlot2, imsSlot1, imsSlot2, mtkAssist1, mtkAssist2, mtkCap1, mtkCap2, mtkEm1, mtkEm2, mtkRcs1, mtkRcs2, mtkSe1, mtkSe2, mtkSlot1, mtkSlot2, se1, se2, slot1, slot2 | [VINTF_VARIANT_LIBRARY] [NOT_SELECTED] |
| /odm/etc/vintf/manifest_dsds_se_ese.xml | 2321 | 64 | de9baf6f13ea480787a7fa3cdaf16af612127bc45d2b3b7161bd45c6e9cfebe9 | 25 | SIM1, SIM2, eSE1 | em1, em2, imsAospSlot1, imsAospSlot2, imsSlot1, imsSlot2, mtkAssist1, mtkAssist2, mtkCap1, mtkCap2, mtkEm1, mtkEm2, mtkRcs1, mtkRcs2, mtkSe1, mtkSe2, mtkSlot1, mtkSlot2, se1, se2, slot1, slot2 | [VINTF_VARIANT_LIBRARY] [SIM_PLUS_ESE_VARIANT_NOT_SELECTED] |
| /odm/etc/vintf/manifest_qsqs.xml | 3284 | 85 | 2ab7d8c8885812faa9c37867dcd3da5eb679d7155b82bdde49680eadf4352ccc | 44 | none | em1, em2, em3, em4, imsAospSlot1, imsAospSlot2, imsAospSlot3, imsAospSlot4, imsSlot1, imsSlot2, imsSlot3, imsSlot4, mtkAssist1, mtkAssist2, mtkAssist3, mtkAssist4, mtkCap1, mtkCap2, mtkCap3, mtkCap4, mtkEm1, mtkEm2, mtkEm3, mtkEm4, mtkRcs1, mtkRcs2, mtkRcs3, mtkRcs4, mtkSe1, mtkSe2, mtkSe3, mtkSe4, mtkSlot1, mtkSlot2, mtkSlot3, mtkSlot4, se1, se2, se3, se4, slot1, slot2, slot3, slot4 | [VINTF_VARIANT_LIBRARY] [NOT_SELECTED] |
| /odm/etc/vintf/manifest_ss.xml | 1473 | 42 | f73a456b31268b3a5944636fcefd5f9193b6c553c45a7fb712e7a9b820c08eca | 12 | none | em1, imsAospSlot1, imsSlot1, mtkAssist1, mtkCap1, mtkEm1, mtkRcs1, mtkSe1, mtkSlot1, se1, slot1 | [VINTF_VARIANT_LIBRARY] [OMAPI_DISABLED_VARIANT_NOT_SELECTED] |
| /odm/etc/vintf/manifest_ss_se.xml | 1578 | 46 | 5955454c7e9a122b11693ab10245700f7ec5805277a34f7cf5bf2fd1539b60ec | 12 | SIM1 | em1, imsAospSlot1, imsSlot1, mtkAssist1, mtkCap1, mtkEm1, mtkRcs1, mtkSe1, mtkSlot1, se1, slot1 | [SINGLE_SIM_SELECTED] [SIM_SECURE_ELEMENT_SELECTED] |
| /odm/etc/vintf/manifest_ss_se_ese.xml | 1616 | 47 | 363838e80ba0485b8f2d48b55cdd83caedde336252a879b86665e4cd4d1829f3 | 13 | SIM1, eSE1 | em1, imsAospSlot1, imsSlot1, mtkAssist1, mtkCap1, mtkEm1, mtkRcs1, mtkSe1, mtkSlot1, se1, slot1 | [VINTF_VARIANT_LIBRARY] [SIM_PLUS_ESE_VARIANT_NOT_SELECTED] |
| /odm/etc/vintf/manifest_tsts.xml | 2617 | 69 | c8f94f1dc5bb5f35d0e39636b8dbbd2e845f6f92c435449dc471fe124200fbf9 | 33 | none | em1, em2, em3, imsAospSlot1, imsAospSlot2, imsAospSlot3, imsSlot1, imsSlot2, imsSlot3, mtkAssist1, mtkAssist2, mtkAssist3, mtkCap1, mtkCap2, mtkCap3, mtkEm1, mtkEm2, mtkEm3, mtkRcs1, mtkRcs2, mtkRcs3, mtkSe1, mtkSe2, mtkSe3, mtkSlot1, mtkSlot2, mtkSlot3, se1, se2, se3, slot1, slot2, slot3 | [VINTF_VARIANT_LIBRARY] [NOT_SELECTED] |

Complete ODM VINTF variant declarations:

| Variant source | HAL name | Version | Interface | Instance | FQName |
|---|---|---|---|---|---|
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | slot2 | @1.4::IRadio/slot2 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot2 | @1.4::IRadio/imsAospSlot2 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | se2 | @1.4::IRadio/se2 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.radio | 1.4 | IRadio | em2 | @1.4::IRadio/em2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot2 | @1.5::IMtkRadioEx/mtkSlot2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot2 | @1.5::IMtkRadioEx/imsSlot2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe2 | @1.5::IMtkRadioEx/mtkSe2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm2 | @1.5::IMtkRadioEx/mtkEm2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist2 | @1.5::IMtkRadioEx/mtkAssist2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs2 | @1.5::IMtkRadioEx/mtkRcs2 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_dsds.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap2 | @1.5::IMtkRadioEx/mtkCap2 |
| odm/etc_vintf/manifest_dsds.xml | android.hardware.secure_element |  |  |  |  |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | slot2 | @1.4::IRadio/slot2 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot2 | @1.4::IRadio/imsAospSlot2 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | se2 | @1.4::IRadio/se2 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.radio | 1.4 | IRadio | em2 | @1.4::IRadio/em2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot2 | @1.5::IMtkRadioEx/mtkSlot2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot2 | @1.5::IMtkRadioEx/imsSlot2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe2 | @1.5::IMtkRadioEx/mtkSe2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm2 | @1.5::IMtkRadioEx/mtkEm2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist2 | @1.5::IMtkRadioEx/mtkAssist2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs2 | @1.5::IMtkRadioEx/mtkRcs2 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_dsds_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap2 | @1.5::IMtkRadioEx/mtkCap2 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM1 | @1.0::ISecureElement/SIM1 |
| odm/etc_vintf/manifest_dsds_se.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM2 | @1.0::ISecureElement/SIM2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | slot2 | @1.4::IRadio/slot2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot2 | @1.4::IRadio/imsAospSlot2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | se2 | @1.4::IRadio/se2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.radio | 1.4 | IRadio | em2 | @1.4::IRadio/em2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot2 | @1.5::IMtkRadioEx/mtkSlot2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot2 | @1.5::IMtkRadioEx/imsSlot2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe2 | @1.5::IMtkRadioEx/mtkSe2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm2 | @1.5::IMtkRadioEx/mtkEm2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist2 | @1.5::IMtkRadioEx/mtkAssist2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs2 | @1.5::IMtkRadioEx/mtkRcs2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap2 | @1.5::IMtkRadioEx/mtkCap2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM1 | @1.0::ISecureElement/SIM1 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM2 | @1.0::ISecureElement/SIM2 |
| odm/etc_vintf/manifest_dsds_se_ese.xml | android.hardware.secure_element | 1.0 | ISecureElement | eSE1 | @1.0::ISecureElement/eSE1 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | slot2 | @1.4::IRadio/slot2 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | slot3 | @1.4::IRadio/slot3 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | slot4 | @1.4::IRadio/slot4 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot2 | @1.4::IRadio/imsAospSlot2 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot3 | @1.4::IRadio/imsAospSlot3 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot4 | @1.4::IRadio/imsAospSlot4 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | se2 | @1.4::IRadio/se2 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | se3 | @1.4::IRadio/se3 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | se4 | @1.4::IRadio/se4 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | em2 | @1.4::IRadio/em2 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | em3 | @1.4::IRadio/em3 |
| odm/etc_vintf/manifest_qsqs.xml | android.hardware.radio | 1.4 | IRadio | em4 | @1.4::IRadio/em4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot2 | @1.5::IMtkRadioEx/mtkSlot2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot3 | @1.5::IMtkRadioEx/mtkSlot3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot4 | @1.5::IMtkRadioEx/mtkSlot4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot2 | @1.5::IMtkRadioEx/imsSlot2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot3 | @1.5::IMtkRadioEx/imsSlot3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot4 | @1.5::IMtkRadioEx/imsSlot4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe2 | @1.5::IMtkRadioEx/mtkSe2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe3 | @1.5::IMtkRadioEx/mtkSe3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe4 | @1.5::IMtkRadioEx/mtkSe4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm2 | @1.5::IMtkRadioEx/mtkEm2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm3 | @1.5::IMtkRadioEx/mtkEm3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm4 | @1.5::IMtkRadioEx/mtkEm4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist2 | @1.5::IMtkRadioEx/mtkAssist2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist3 | @1.5::IMtkRadioEx/mtkAssist3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist4 | @1.5::IMtkRadioEx/mtkAssist4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs2 | @1.5::IMtkRadioEx/mtkRcs2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs3 | @1.5::IMtkRadioEx/mtkRcs3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs4 | @1.5::IMtkRadioEx/mtkRcs4 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap2 | @1.5::IMtkRadioEx/mtkCap2 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap3 | @1.5::IMtkRadioEx/mtkCap3 |
| odm/etc_vintf/manifest_qsqs.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap4 | @1.5::IMtkRadioEx/mtkCap4 |
| odm/etc_vintf/manifest_ss.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_ss.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_ss.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_ss.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_ss.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_ss.xml | android.hardware.secure_element |  |  |  |  |
| odm/etc_vintf/manifest_ss_se.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_ss_se.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_ss_se.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_ss_se.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_ss_se.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_ss_se.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM1 | @1.0::ISecureElement/SIM1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.secure_element | 1.0 | ISecureElement | SIM1 | @1.0::ISecureElement/SIM1 |
| odm/etc_vintf/manifest_ss_se_ese.xml | android.hardware.secure_element | 1.0 | ISecureElement | eSE1 | @1.0::ISecureElement/eSE1 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | slot1 | @1.4::IRadio/slot1 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | slot2 | @1.4::IRadio/slot2 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | slot3 | @1.4::IRadio/slot3 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot1 | @1.4::IRadio/imsAospSlot1 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot2 | @1.4::IRadio/imsAospSlot2 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | imsAospSlot3 | @1.4::IRadio/imsAospSlot3 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | se1 | @1.4::IRadio/se1 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | se2 | @1.4::IRadio/se2 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | se3 | @1.4::IRadio/se3 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | em1 | @1.4::IRadio/em1 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | em2 | @1.4::IRadio/em2 |
| odm/etc_vintf/manifest_tsts.xml | android.hardware.radio | 1.4 | IRadio | em3 | @1.4::IRadio/em3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot1 | @1.5::IMtkRadioEx/mtkSlot1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot2 | @1.5::IMtkRadioEx/mtkSlot2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSlot3 | @1.5::IMtkRadioEx/mtkSlot3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot1 | @1.5::IMtkRadioEx/imsSlot1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot2 | @1.5::IMtkRadioEx/imsSlot2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | imsSlot3 | @1.5::IMtkRadioEx/imsSlot3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe1 | @1.5::IMtkRadioEx/mtkSe1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe2 | @1.5::IMtkRadioEx/mtkSe2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkSe3 | @1.5::IMtkRadioEx/mtkSe3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm1 | @1.5::IMtkRadioEx/mtkEm1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm2 | @1.5::IMtkRadioEx/mtkEm2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkEm3 | @1.5::IMtkRadioEx/mtkEm3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist1 | @1.5::IMtkRadioEx/mtkAssist1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist2 | @1.5::IMtkRadioEx/mtkAssist2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkAssist3 | @1.5::IMtkRadioEx/mtkAssist3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs1 | @1.5::IMtkRadioEx/mtkRcs1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs2 | @1.5::IMtkRadioEx/mtkRcs2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkRcs3 | @1.5::IMtkRadioEx/mtkRcs3 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap1 | @1.5::IMtkRadioEx/mtkCap1 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap2 | @1.5::IMtkRadioEx/mtkCap2 |
| odm/etc_vintf/manifest_tsts.xml | vendor.mediatek.hardware.mtkradioex | 1.5 | IMtkRadioEx | mtkCap3 | @1.5::IMtkRadioEx/mtkCap3 |
Canonical ODM selector interpretation:
- `manifest_ss_se.xml` is the selected profile through the primary vendor manifest input list. [SINGLE_SIM_SELECTED] [SIM_SECURE_ELEMENT_SELECTED]
- DSDS, TSTS, QSQS, and eSE variants are shipped as variant-library profiles but are not selected by the active vendor manifest. [VINTF_VARIANT_LIBRARY]
- eSE declarations in non-selected ODM variants are not physical eSE proof. [NOT_NFC_PROOF]


################################################################################
# 2.6 — SYSTEM FRAMEWORK VINTF MANIFEST AUTHORITY
################################################################################

| Source | HAL name | Format | Transport | Version | Interface | Instance | FQName |
|---|---|---|---|---|---|---|---|
| system/etc_vintf/manifest.xml | android.frameworks.displayservice | hidl | hwbinder | 1.0 | IDisplayService | default | @1.0::IDisplayService/default |
| system/etc_vintf/manifest.xml | android.frameworks.schedulerservice | hidl | hwbinder | 1.0 | ISchedulingPolicyService | default | @1.0::ISchedulingPolicyService/default |
| system/etc_vintf/manifest.xml | android.frameworks.sensorservice | hidl | hwbinder | 1.0 | ISensorManager | default | @1.0::ISensorManager/default |
| system/etc_vintf/manifest.xml | android.hidl.manager | hidl | hwbinder | 1.2 | IServiceManager | default | @1.2::IServiceManager/default |
| system/etc_vintf/manifest.xml | android.hidl.memory | hidl | passthrough | 1.0 | IMapper | ashmem | @1.0::IMapper/ashmem |
| system/etc_vintf/manifest.xml | android.hidl.token | hidl | hwbinder | 1.0 | ITokenManager | default | @1.0::ITokenManager/default |
| system/etc_vintf/manifest.xml | android.system.net.netd | hidl | hwbinder | 1.1 | INetd | default | @1.1::INetd/default |
| system/etc_vintf/manifest.xml | android.system.wifi.keystore | hidl | hwbinder | 1.0 | IKeystore | default | @1.0::IKeystore/default |
| system/etc_vintf/manifest.xml | netutils-wrapper | native |  | 1.0 |  |  |  |
| system/etc_vintf/manifest/android.frameworks.stats@1.0-service.xml | android.frameworks.stats |  | hwbinder | 1.0 | IStats | default | @1.0::IStats/default |
| system/etc_vintf/manifest/android.hidl.allocator@1.0-service.xml | android.hidl.allocator |  | hwbinder | 1.0 | IAllocator | ashmem | @1.0::IAllocator/ashmem |
| system/etc_vintf/manifest/android.system.suspend@1.0-service.xml | android.system.suspend |  | hwbinder | 1.0 | ISystemSuspend | default | @1.0::ISystemSuspend/default |
| system/etc_vintf/manifest/manifest_android.frameworks.cameraservice.service@2.0.xml | android.frameworks.cameraservice.service |  | hwbinder | 2.0 | ICameraService | default | @2.0::ICameraService/default |
| system/etc_vintf/manifest/manifest_media_c2_software.xml | android.hardware.media.c2 |  | hwbinder | 1.0 | IComponentStore | software | @1.0::IComponentStore/software |
System framework manifest interpretation:
- Framework services are declared by `/system/etc/vintf/manifest.xml` and fragments under `/system/etc/vintf/manifest/`.
- Framework declarations include camera service, sensor service, stats, allocator, suspend, and software codec2.
- These declarations are framework authority, not proof of additional device hardware.


################################################################################
# 2.7 — COMPATIBILITY MATRIX AUTHORITY
################################################################################

| File | Bytes | Lines | SHA256 |
|---|---|---|---|
| vendor/etc_vintf/compatibility_matrix.xml | 1167 | 40 | 92d0d66a855062b088ca140a5d88732250474c4ea70fae92f5cc45e2ce4b4a74 |
| system/etc_vintf/compatibility_matrix.1.xml | 71968 | 2287 | c615e6da0f2eb70bb6eb4f04aff141de27c99055c93f1f35aa3310b2bccd9ebb |
| system/etc_vintf/compatibility_matrix.2.xml | 74191 | 2359 | 861fc3e91bb7086f77c84ff475bd1e2378622f5c93e3e25bab0a4640628df617 |
| system/etc_vintf/compatibility_matrix.3.xml | 90344 | 2883 | a898c517009d0f03bde580bb125dc56729477c52a26cf4d8d2bea708c1373a20 |
| system/etc_vintf/compatibility_matrix.4.xml | 113177 | 3641 | 84f47c4c87e99d08172a83c07fb9148b30ab971377dd09e6b6d84cf40b4e6c31 |
| system/etc_vintf/compatibility_matrix.device.xml | 496 | 16 | 467f143d92047ff5977679f32dbd42df23f79b63f3edcb90e8b991b4427ff8fa |
| system/etc_vintf/compatibility_matrix.legacy.xml | 71977 | 2287 | 1b24af46222e43bb4fab897a0c3d526d11edfc73a7eba1067631c996548df9a5 |
Matrix interpretation:
- Vendor device matrix requires framework-side HIDL services, vendor-ndk 29, and system-sdk 29.
- System matrices carry framework compatibility levels 1 through 4 plus legacy and device matrices.
- Vendor manifest `target-level="4"` aligns with Android 10 / VNDK 29 framework compatibility expectations.
- Matrix entries are requirements/allowances, not live runtime proof.


################################################################################
# 2.8 — RUNTIME HIDL REGISTRATION SUMMARY
################################################################################

| Runtime class | Rows |
|---|---|
| Binderized services registered with hwservicemanager | 206 |
| Interfaces returned through passthrough getService() | 27 |
| Available passthrough implementation rows | 50 |
| HAL-related init.svc entries captured | 65 |
| HAL-related process rows captured | 64 |
Important lshal rule:
`lshal -p` was not useful for authoritative PID mapping on this device because it returned only
a Server column with mostly `N/A` for binderized services. PID/process authority for this
section comes from `ps -A -Z` and init RC mapping, not from `lshal -p`. [INVALID_CAPTURE] [RESEARCH_RULE]


################################################################################
# 2.9 — COMPLETE RUNTIME BINDERIZED HIDL SERVICE TABLE
################################################################################

| VINTF flag | Registered | Interface / instance | Server / client field |
|---|---|---|---|
| FM | Y | android.frameworks.cameraservice.service@2.0::ICameraService/default | N/A        N/A |
| FM | Y | android.frameworks.displayservice@1.0::IDisplayService/default | N/A        N/A |
| FM | Y | android.frameworks.schedulerservice@1.0::ISchedulingPolicyService/default | N/A        N/A |
| FM | Y | android.frameworks.sensorservice@1.0::ISensorManager/default | N/A        N/A |
| FM | Y | android.frameworks.stats@1.0::IStats/default | N/A        N/A |
| DM,FC | Y | android.hardware.audio.effect@5.0::IEffectsFactory/default | N/A        N/A |
| DM,FC | Y | android.hardware.audio@5.0::IDevicesFactory/default | N/A        N/A |
| DM,FC | Y | android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default | N/A        N/A |
| DM,FC | Y | android.hardware.bluetooth@1.0::IBluetoothHci/default | N/A        N/A |
| DM,FC | Y | android.hardware.camera.provider@2.4::ICameraProvider/internal/0 | N/A        N/A |
| DM | Y | android.hardware.cas@1.0::IMediaCasService/default | N/A        N/A |
| DM,FC | Y | android.hardware.cas@1.1::IMediaCasService/default | N/A        N/A |
| DM | Y | android.hardware.configstore@1.0::ISurfaceFlingerConfigs/default | N/A        N/A |
| DM,FC | Y | android.hardware.configstore@1.1::ISurfaceFlingerConfigs/default | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::ICryptoFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::ICryptoFactory/default | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::ICryptoFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::IDrmFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::IDrmFactory/default | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.0::IDrmFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.1::ICryptoFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.1::ICryptoFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.1::IDrmFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.1::IDrmFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.2::ICryptoFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.2::ICryptoFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.2::IDrmFactory/clearkey | N/A        N/A |
| DM,FC | Y | android.hardware.drm@1.2::IDrmFactory/widevine | N/A        N/A |
| DM,FC | Y | android.hardware.gatekeeper@1.0::IGatekeeper/default | N/A        N/A |
| DM | Y | android.hardware.gnss@1.0::IGnss/default | N/A        N/A |
| DM,FC | Y | android.hardware.gnss@1.1::IGnss/default | N/A        N/A |
| DM,FC | Y | android.hardware.gnss@2.0::IGnss/default | N/A        N/A |
| DM,FC | Y | android.hardware.graphics.allocator@2.0::IAllocator/default | N/A        N/A |
| DM,FC | Y | android.hardware.graphics.composer@2.1::IComposer/default | N/A        N/A |
| DM,FC | Y | android.hardware.health@2.0::IHealth/default | N/A        N/A |
| DM,FC | Y | android.hardware.keymaster@4.0::IKeymasterDevice/default | N/A        N/A |
| DM,FC | Y | android.hardware.light@2.0::ILight/default | N/A        N/A |
| FM | Y | android.hardware.media.c2@1.0::IComponentStore/software | N/A        N/A |
| DM,FC | Y | android.hardware.media.omx@1.0::IOmx/default | N/A        N/A |
| DM,FC | Y | android.hardware.media.omx@1.0::IOmxStore/default | N/A        N/A |
| DM,FC | Y | android.hardware.memtrack@1.0::IMemtrack/default | N/A        N/A |
| DM,FC | Y | android.hardware.power@1.0::IPower/default | N/A        N/A |
| DM,FC | Y | android.hardware.power@1.1::IPower/default | N/A        N/A |
| DM,FC | Y | android.hardware.power@1.2::IPower/default | N/A        N/A |
| DM,FC | Y | android.hardware.power@1.3::IPower/default | N/A        N/A |
| DM | Y | android.hardware.radio.config@1.0::IRadioConfig/default | N/A        N/A |
| DM,FC | Y | android.hardware.radio.config@1.1::IRadioConfig/default | N/A        N/A |
| DM | Y | android.hardware.radio@1.0::IRadio/em1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.0::IRadio/imsAospSlot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.0::IRadio/se1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.0::IRadio/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.0::ISap/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.1::IRadio/em1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.1::IRadio/imsAospSlot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.1::IRadio/se1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.1::IRadio/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.1::ISap/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.2::IRadio/em1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.2::IRadio/imsAospSlot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.2::IRadio/se1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.2::IRadio/slot1 | N/A        N/A |
| DM,FC | Y | android.hardware.radio@1.2::ISap/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.3::IRadio/em1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.3::IRadio/imsAospSlot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.3::IRadio/se1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.3::IRadio/slot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.4::IRadio/em1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.4::IRadio/imsAospSlot1 | N/A        N/A |
| DM | Y | android.hardware.radio@1.4::IRadio/se1 | N/A        N/A |
| DM,FC | Y | android.hardware.radio@1.4::IRadio/slot1 | N/A        N/A |
| DM,FC | Y | android.hardware.secure_element@1.0::ISecureElement/SIM1 | N/A        N/A |
| DM,FC | Y | android.hardware.sensors@2.0::ISensors/default | N/A        N/A |
| DM,FC | Y | android.hardware.thermal@1.0::IThermal/default | N/A        N/A |
| DM,FC | Y | android.hardware.usb@1.0::IUsb/default | N/A        N/A |
| DM,FC | Y | android.hardware.usb@1.1::IUsb/default | N/A        N/A |
| DM,FC | Y | android.hardware.vibrator@1.0::IVibrator/default | N/A        N/A |
| DM,FC | Y | android.hardware.wifi@1.0::IWifi/default | N/A        N/A |
| DM,FC | Y | android.hardware.wifi@1.1::IWifi/default | N/A        N/A |
| DM,FC | Y | android.hardware.wifi@1.2::IWifi/default | N/A        N/A |
| DM,FC | Y | android.hardware.wifi@1.3::IWifi/default | N/A        N/A |
| FM | Y | android.hidl.allocator@1.0::IAllocator/ashmem | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/AgpsDebugInterface | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/AgpsInterface | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/OpImsRILd1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/SIM1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/ashmem | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/clearkey | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/em1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/imsAospSlot1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/imsSlot1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/internal/0 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkAssist1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkCap1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkEm1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkRcs1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkSe1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtkSlot1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_agps2framework | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_debugService2mnld | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_framework2agps | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lbs_log_v2s | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_agps | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_bt | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_ipaddr | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_lbs | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_network | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_sensor | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_lppe_socket_wlan | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_meta2mnld | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_mnld2debugService | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_mnld2mtklogger | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_mnld2nlputils | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/mtk_mtklogger2mnld | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/se1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/slot1 | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/software | N/A        N/A |
| X | Y | android.hidl.base@1.0::IBase/widevine | N/A        N/A |
| FM | Y | android.hidl.manager@1.0::IServiceManager/default | N/A        N/A |
| FM | Y | android.hidl.manager@1.1::IServiceManager/default | N/A        N/A |
| FM | Y | android.hidl.manager@1.2::IServiceManager/default | N/A        N/A |
| FM | Y | android.hidl.token@1.0::ITokenManager/default | N/A        N/A |
| FM | Y | android.system.net.netd@1.0::INetd/default | N/A        N/A |
| FM | Y | android.system.net.netd@1.1::INetd/default | N/A        N/A |
| FM | Y | android.system.suspend@1.0::ISystemSuspend/default | N/A        N/A |
| FM | Y | android.system.wifi.keystore@1.0::IKeystore/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.camera.bgservice@1.0::IBGService/internal/0 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.camera.bgservice@1.1::IBGService/internal/0 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.gpu@1.0::IGraphicExt/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/AgpsDebugInterface | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/AgpsInterface | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_agps2framework | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_debugService2mnld | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_framework2agps | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lbs_log_v2s | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_agps | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_bt | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_ipaddr | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_lbs | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_network | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_sensor | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_wlan | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_meta2mnld | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2debugService | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2mtklogger | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2nlputils | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mtklogger2mnld | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mms@1.0::IMms/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mms@1.1::IMms/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mms@1.2::IMms/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mms@1.3::IMms/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkpower@1.0::IMtkPerf/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkpower@1.0::IMtkPower/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.0::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.1::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.2::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.3::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.4::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/imsSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkAssist1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkCap1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkEm1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkRcs1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkSe1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.mtkradioex@1.5::IMtkRadioEx/mtkSlot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.netdagent@1.0::INetdagent/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.nvram@1.0::INvram/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.nvram@1.1::INvram/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.power@2.0::IPower/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.power@2.1::IPower/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.pq@2.0::IPictureQuality/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.pq@2.1::IPictureQuality/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.pq@2.2::IPictureQuality/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.pq@2.3::IPictureQuality/default | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.radio_op@2.0::IRadioOp/OpImsRILd1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.radio_op@2.0::IRadioOp/slot1 | N/A        N/A |
| DM | Y | vendor.mediatek.hardware.videotelephony@1.0::IVideoTelephony/default | N/A        N/A |

################################################################################
# 2.10 — PASSTHROUGH RUNTIME AND IMPLEMENTATION TABLES
################################################################################

Passthrough interfaces returned by `getService()`:
| VINTF flag | Registered | Interface / instance | Observed server/client field |
|---|---|---|---|
| FC | ? | android.hardware.audio.effect@5.0::IEffectsFactory/default | N/A        516    516 |
| FC | ? | android.hardware.audio@5.0::IDevicesFactory/default | N/A        516    516 |
| FC | ? | android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default | N/A        516    516 |
| FC | ? | android.hardware.bluetooth@1.0::IBluetoothHci/default | N/A        517    517 |
| FC | ? | android.hardware.camera.provider@2.4::ICameraProvider/internal/0 | N/A        696    696 |
| FC | ? | android.hardware.drm@1.0::ICryptoFactory/default | N/A        520    520 |
| FC | ? | android.hardware.drm@1.0::IDrmFactory/default | N/A        520    520 |
| FC | ? | android.hardware.gatekeeper@1.0::IGatekeeper/default | N/A        523    523 |
| FC | ? | android.hardware.gnss@2.0::IGnss/default | N/A        524    524 |
| FC | ? | android.hardware.graphics.allocator@2.0::IAllocator/default | N/A        525    525 |
| FC | ? | android.hardware.graphics.composer@2.1::IComposer/default | N/A        526    526 |
| DM,FC | ? | android.hardware.graphics.mapper@2.1::IMapper/default | N/A        N/A    526 550 696 1075 1238 1881 3850 13148 17931 |
| FC | ? | android.hardware.light@2.0::ILight/default | N/A        528    528 |
| FC | ? | android.hardware.memtrack@1.0::IMemtrack/default | N/A        529    529 |
| FC | ? | android.hardware.power@1.3::IPower/default | N/A        540    540 |
| FC | ? | android.hardware.thermal@1.0::IThermal/default | N/A        532    532 |
| FC | ? | android.hardware.vibrator@1.0::IVibrator/default | N/A        536    536 |
| FM | ? | android.hidl.memory@1.0::IMapper/ashmem | N/A        N/A    526 693 706 |
| X | ? | vendor.mediatek.hardware.camera.bgservice@1.1::IBGService/internal/0 | N/A        696    696 |
| X | ? | vendor.mediatek.hardware.mms@1.3::IMms/default | N/A        704    704 |
| X | ? | vendor.mediatek.hardware.mtkcodecservice@1.1::IMtkCodecService/default | N/A        1357 |
| X | ? | vendor.mediatek.hardware.mtkpower@1.0::IMtkPerf/default | N/A        540    540 |
| X | ? | vendor.mediatek.hardware.mtkpower@1.0::IMtkPower/default | N/A        540    540 |
| X | ? | vendor.mediatek.hardware.nvram@1.1::INvram/default | N/A        541    541 |
| X | ? | vendor.mediatek.hardware.power@2.1::IPower/default | N/A        540    540 |
| X | ? | vendor.mediatek.hardware.pq@2.3::IPictureQuality/default | N/A        706    706 |
| X | ? | vendor.mediatek.hardware.videotelephony@1.0::IVideoTelephony/default | N/A        537    537 |

Available passthrough implementation rows:
| VINTF flag | Registered | Interface / implementation path | Observed client field |
|---|---|---|---|
| FC | ? | android.hardware.audio.effect@5.0::I*/* | (/vendor/lib/hw/)                  N/A        N/A    516 |
| FC | ? | android.hardware.audio.effect@5.0::I*/* | (/vendor/lib64/hw/)                N/A        N/A |
| FC | ? | android.hardware.audio@5.0::I*/* | (/vendor/lib/hw/) (-mediatek)             N/A        N/A    516 |
| FC | ? | android.hardware.audio@5.0::I*/* | (/vendor/lib64/hw/) (-mediatek)           N/A        N/A |
| FC | ? | android.hardware.bluetooth.audio@2.0::I*/* | (/vendor/lib/hw/) (-mediatek)   N/A        N/A    516 |
| FC | ? | android.hardware.bluetooth.audio@2.0::I*/* | (/vendor/lib64/hw/) (-mediatek) N/A        N/A |
| FC | ? | android.hardware.bluetooth@1.0::I*/* | (/vendor/lib/hw/) (-mediatek)         N/A        N/A |
| FC | ? | android.hardware.bluetooth@1.0::I*/* | (/vendor/lib64/hw/) (-mediatek)       N/A        N/A    517 |
| FC | ? | android.hardware.camera.provider@2.4::I*/* | (/vendor/lib64/hw/) (-mediatek) N/A        N/A    696 |
| FC | ? | android.hardware.drm@1.0::I*/* | (/vendor/lib/hw/)                           N/A        N/A    520 |
| FC | ? | android.hardware.gatekeeper@1.0::I*/* | (/vendor/lib/hw/)                    N/A        N/A |
| FC | ? | android.hardware.gatekeeper@1.0::I*/* | (/vendor/lib64/hw/)                  N/A        N/A    523 |
| FC | ? | android.hardware.gnss@2.0::I*/* | (/vendor/lib64/hw/) (-mediatek)            N/A        N/A    524 |
| FC | ? | android.hardware.graphics.allocator@2.0::I*/* | (/vendor/lib/hw/)            N/A        N/A |
| FC | ? | android.hardware.graphics.allocator@2.0::I*/* | (/vendor/lib64/hw/)          N/A        N/A    525 |
| FC | ? | android.hardware.graphics.composer@2.1::I*/* | (/vendor/lib/hw/)             N/A        N/A |
| FC | ? | android.hardware.graphics.composer@2.1::I*/* | (/vendor/lib64/hw/)           N/A        N/A    526 |
| DM | ? | android.hardware.graphics.mapper@2.0::I*/* | (/vendor/lib/hw/) (-2.1)        N/A        N/A    513 1330 |
| DM | ? | android.hardware.graphics.mapper@2.0::I*/* | (/vendor/lib64/hw/) (-2.1)      N/A        N/A    512 526 550 696 1075 1238 1262 1376 1587 1639 1855 1881 1906 1929 1940 2084 2144 2229 2494 2519 2532 2570 2584 3850 3881 12485 13148 13632 14145 14709 16243 16382 16505 16719 16872 17643 17714 17931 |
| FC | ? | android.hardware.light@2.0::I*/* | (/vendor/lib/hw/) (-mediatek)             N/A        N/A |
| FC | ? | android.hardware.light@2.0::I*/* | (/vendor/lib64/hw/) (-mediatek)           N/A        N/A    528 |
| FC | ? | android.hardware.memtrack@1.0::I*/* | (/vendor/lib/hw/)                      N/A        N/A |
| FC | ? | android.hardware.memtrack@1.0::I*/* | (/vendor/lib64/hw/)                    N/A        N/A    529 |
| FC | ? | android.hardware.power@1.3::I*/* | (/vendor/lib/hw/) (-mediatek)             N/A        N/A |
| FC | ? | android.hardware.power@1.3::I*/* | (/vendor/lib64/hw/) (-mediatek)           N/A        N/A    540 |
| FC | ? | android.hardware.thermal@1.0::I*/* | (/vendor/lib/hw/)                       N/A        N/A |
| FC | ? | android.hardware.thermal@1.0::I*/* | (/vendor/lib64/hw/)                     N/A        N/A    532 |
| FC | ? | android.hardware.vibrator@1.0::I*/* | (/vendor/lib/hw/)                      N/A        N/A |
| FC | ? | android.hardware.vibrator@1.0::I*/* | (/vendor/lib64/hw/)                    N/A        N/A    536 |
| FM | ? | android.hidl.memory@1.0::I*/* | (/system/lib/hw/)                            N/A        N/A |
| FM | ? | android.hidl.memory@1.0::I*/* | (/system/lib/vndk-sp-29/hw/)                 N/A        N/A    693 |
| FM | ? | android.hidl.memory@1.0::I*/* | (/system/lib64/hw/)                          N/A        N/A |
| FM | ? | android.hidl.memory@1.0::I*/* | (/system/lib64/vndk-sp-29/hw/)               N/A        N/A    526 706 |
| X | ? | vendor.mediatek.hardware.camera.bgservice@1.1::I*/* | (/vendor/lib/hw/)      N/A        N/A |
| X | ? | vendor.mediatek.hardware.camera.bgservice@1.1::I*/* | (/vendor/lib64/hw/)    N/A        N/A    696 |
| X | ? | vendor.mediatek.hardware.camera.lomoeffect@1.0::I*/* | (/vendor/lib64/hw/)   N/A        N/A |
| X | ? | vendor.mediatek.hardware.mms@1.3::I*/* | (/vendor/lib/hw/)                   N/A        N/A |
| X | ? | vendor.mediatek.hardware.mms@1.3::I*/* | (/vendor/lib64/hw/)                 N/A        N/A    704 |
| X | ? | vendor.mediatek.hardware.mtkcodecservice@1.1::I*/* | (/vendor/lib/hw/)       N/A        N/A |
| X | ? | vendor.mediatek.hardware.mtkcodecservice@1.1::I*/* | (/vendor/lib64/hw/)     N/A        N/A |
| X | ? | vendor.mediatek.hardware.mtkpower@1.0::I*/* | (/vendor/lib/hw/)              N/A        N/A |
| X | ? | vendor.mediatek.hardware.mtkpower@1.0::I*/* | (/vendor/lib64/hw/)            N/A        N/A    540 |
| X | ? | vendor.mediatek.hardware.nvram@1.1::I*/* | (/vendor/lib/hw/)                 N/A        N/A |
| X | ? | vendor.mediatek.hardware.nvram@1.1::I*/* | (/vendor/lib64/hw/)               N/A        N/A    541 |
| X | ? | vendor.mediatek.hardware.power@2.1::I*/* | (/vendor/lib/hw/)                 N/A        N/A |
| X | ? | vendor.mediatek.hardware.power@2.1::I*/* | (/vendor/lib64/hw/)               N/A        N/A    540 |
| X | ? | vendor.mediatek.hardware.pq@2.3::I*/* | (/vendor/lib/hw/)                    N/A        N/A |
| X | ? | vendor.mediatek.hardware.pq@2.3::I*/* | (/vendor/lib64/hw/)                  N/A        N/A    706 |
| X | ? | vendor.mediatek.hardware.videotelephony@1.0::I*/* | (/vendor/lib/hw/)        N/A        N/A    537 |
| X | ? | vendor.mediatek.hardware.videotelephony@1.0::I*/* | (/vendor/lib64/hw/)      N/A        N/A |

################################################################################
# 2.11 — LIVE INIT.SVC HAL-RELATED STATE
################################################################################

| init.svc name | State | Classification |
|---|---|---|
| audioserver | running | [ACTIVE_RUNTIME] |
| bluetooth-1-0 | running | [ACTIVE_RUNTIME] |
| camerahalserver | running | [ACTIVE_RUNTIME] |
| drm | running | [ACTIVE_RUNTIME] |
| gatekeeperd | running | [ACTIVE_RUNTIME] |
| gnss_service | running | [ACTIVE_RUNTIME] |
| gpu-1-0 | running | [ACTIVE_RUNTIME] |
| gpu | running | [ACTIVE_RUNTIME] |
| health-hal-2-0 | running | [ACTIVE_RUNTIME] |
| hidl_memory | running | [ACTIVE_RUNTIME] |
| hwservicemanager | running | [ACTIVE_RUNTIME] |
| ipsec_mon | running | [ACTIVE_RUNTIME] |
| lbs_dbg | running | [ACTIVE_RUNTIME] |
| lbs_hidl_service | running | [ACTIVE_RUNTIME] |
| light-hal-2-0 | running | [ACTIVE_RUNTIME] |
| mms-1-3 | running | [ACTIVE_RUNTIME] |
| mtk_secure_element_hal_service | running | [ACTIVE_RUNTIME] |
| mtkcodecservice-1-1 | stopped | [DECLARED_STOPPED_OR_LAZY] |
| netdagent | running | [ACTIVE_RUNTIME] |
| nvram-hidl-1-1 | running | [ACTIVE_RUNTIME] |
| nvram_daemon | stopped | [DECLARED_STOPPED_OR_LAZY] |
| power-hal-1-0 | running | [ACTIVE_RUNTIME] |
| pq-2-2 | running | [ACTIVE_RUNTIME] |
| servicemanager | running | [ACTIVE_RUNTIME] |
| terservice | stopped | [DECLARED_STOPPED_OR_LAZY] |
| thermal | running | [ACTIVE_RUNTIME] |
| thermal_manager | stopped | [DECLARED_STOPPED_OR_LAZY] |
| thermald | running | [ACTIVE_RUNTIME] |
| thermalloadalgod | running | [ACTIVE_RUNTIME] |
| usbd | stopped | [DECLARED_STOPPED_OR_LAZY] |
| vendor.audio-hal-2-0 | running | [ACTIVE_RUNTIME] |
| vendor.bip | running | [ACTIVE_RUNTIME] |
| vendor.cas-hal-1-1 | running | [ACTIVE_RUNTIME] |
| vendor.ccci3_fsd | running | [ACTIVE_RUNTIME] |
| vendor.ccci_fsd | running | [ACTIVE_RUNTIME] |
| vendor.ccci_rpcd | running | [ACTIVE_RUNTIME] |
| vendor.configstore-hal | running | [ACTIVE_RUNTIME] |
| vendor.drm-clearkey-hal-1-2 | running | [ACTIVE_RUNTIME] |
| vendor.drm-hal-1-0 | running | [ACTIVE_RUNTIME] |
| vendor.drm-widevine-hal-1-2 | running | [ACTIVE_RUNTIME] |
| vendor.epdg_wod | running | [ACTIVE_RUNTIME] |
| vendor.gatekeeper-1-0 | running | [ACTIVE_RUNTIME] |
| vendor.gralloc-2-0 | running | [ACTIVE_RUNTIME] |
| vendor.gsm0710muxd | running | [ACTIVE_RUNTIME] |
| vendor.hwcomposer-2-1 | running | [ACTIVE_RUNTIME] |
| vendor.keymaster-4-0 | running | [ACTIVE_RUNTIME] |
| vendor.memtrack-hal-1-0 | running | [ACTIVE_RUNTIME] |
| vendor.ril-daemon-mtk | running | [ACTIVE_RUNTIME] |
| vendor.sensors-hal-2-0 | running | [ACTIVE_RUNTIME] |
| vendor.thermal-hal-1-0 | running | [ACTIVE_RUNTIME] |
| vendor.usb-hal-1-1 | running | [ACTIVE_RUNTIME] |
| vendor.vibrator-1-0 | running | [ACTIVE_RUNTIME] |
| vendor.volte_imcb | running | [ACTIVE_RUNTIME] |
| vendor.volte_stack | running | [ACTIVE_RUNTIME] |
| vendor.volte_ua | running | [ACTIVE_RUNTIME] |
| vendor.wifi_hal_legacy | running | [ACTIVE_RUNTIME] |
| vndservicemanager | running | [ACTIVE_RUNTIME] |
| volte_imsm_93 | running | [ACTIVE_RUNTIME] |
| volte_md_status | running | [ACTIVE_RUNTIME] |
| vtservice | running | [ACTIVE_RUNTIME] |
| vtservice_hidl | running | [ACTIVE_RUNTIME] |
| wfca | running | [ACTIVE_RUNTIME] |
| wificond | running | [ACTIVE_RUNTIME] |
| wmt_launcher | running | [ACTIVE_RUNTIME] |
| wmt_loader | stopped | [DECLARED_STOPPED_OR_LAZY] |

################################################################################
# 2.12 — HAL-RELATED INIT RC SERVICE DECLARATION CATALOG
################################################################################

| File:Line | Service | Command | Runtime | Options / interfaces / sockets / caps | Tags |
|---|---|---|---|---|---|
| system/etc_init/android.hidl.allocator@1.0-service.rc:1 | hidl_memory | /system/bin/hw/android.hidl.allocator@1.0-service | running | class hal; user system; group system |  |
| system/etc_init/android.system.suspend@1.0-service.rc:1 | system_suspend | /system/bin/hw/android.system.suspend@1.0-service |  | class early_hal; user system; group system wakelock; capabilities BLOCK_SUSPEND | CAPABILITY_SERVICE |
| system/etc_init/audioserver.rc:1 | audioserver | /system/bin/audioserver | running | class core; user audioserver; # media gid needed for /dev/fm (radio) and for /data/misc/media (tee); group audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw; capabilities BLOCK_SUSPEND; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks; onrestart restart vendor.audio-hal-2-0; #onrestart restart vendor.audio-hal-4-0-msd; # Keep the original service name for backward compatibility when upgrading; # O-MR1 devices with framework-only.; onrestart restart audio-hal-2-0 | CAPABILITY_SERVICE;RADIO_IMS_WFC |
| system/etc_init/cameraserver.rc:1 | cameraserver | /system/bin/cameraserver |  | class main; user cameraserver; group audio camera input drmrpc; ioprio rt 4; writepid /dev/cpuset/camera-daemon/tasks /dev/stune/top-app/tasks; rlimit rtprio 10 10 |  |
| system/etc_init/drmserver.rc:1 | drm | /system/bin/drmserver | running | class main; user drm; # Add drmserver access sdcard and internal storage permission; group drm system inet drmrpc readproc sdcard_rw media_rw; writepid /dev/cpuset/foreground/tasks |  |
| system/etc_init/gatekeeperd.rc:1 | gatekeeperd | /system/bin/gatekeeperd /data/misc/gatekeeper | running | class late_start; user system; writepid /dev/cpuset/system-background/tasks |  |
| system/etc_init/gpuservice.rc:1 | gpu | /system/bin/gpuservice | running | class core; user gpu_service; group graphics |  |
| system/etc_init/hwservicemanager.rc:1 | hwservicemanager | /system/bin/hwservicemanager | running | user system; disabled; group system readproc; critical; onrestart setprop hwservicemanager.ready false; onrestart class_restart main; onrestart class_restart hal; onrestart class_restart early_hal; writepid /dev/cpuset/system-background/tasks; class animation; shutdown critical | DISABLED_DECLARED |
| system/etc_init/init.thermald.rc:1 | thermald | /system/bin/thermald | running | class main; user system; group system net_admin |  |
| system/etc_init/init.vtservice.rc:3 | vtservice | /system/bin/vtservice | running | class main; user system; group system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw; ioprio rt 4 | RADIO_IMS_WFC |
| system/etc_init/keystore.rc:1 | keystore | /system/bin/keystore /data/misc/keystore |  | class main; user keystore; group keystore drmrpc readproc log; writepid /dev/cpuset/foreground/tasks |  |
| system/etc_init/mediadrmserver.rc:1 | mediadrm | /system/bin/mediadrmserver |  | class main; user media; group mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| system/etc_init/mediaextractor.rc:1 | mediaextractor | /system/bin/mediaextractor |  | class main; user mediaex; group drmrpc mediadrm readproc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| system/etc_init/mediaserver.rc:1 | media | /system/bin/mediaserver |  | class main; user media; group audio camera inet net_bt net_bt_admin net_bw_acct drmrpc mediadrm readproc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks |  |
| system/etc_init/mtkbootanim.rc:1 | bootanim | /system/bin/mtkbootanimation |  | class core animation; user graphics; group graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| system/etc_init/mtkbootanim.rc:10 | banim_shutmp3 | /system/bin/mtkbootanimation shut mp3 |  | class core animation; user graphics; group graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| system/etc_init/mtkbootanim.rc:18 | banim_shutnomp3 | /system/bin/mtkbootanimation shut nomp3 |  | class core animation; user graphics; group graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| system/etc_init/netdiag.rc:27 | netdiag | /system/bin/netdiag |  | class main; user shell; group log system radio sdcard_rw inet sdcard_r media_rw net_raw net_admin net_bw_stats readproc net_bt input; capabilities NET_ADMIN NET_RAW | CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| system/etc_init/servicemanager.rc:1 | servicemanager | /system/bin/servicemanager | running | class core animation; user system; group system readproc; critical; onrestart restart healthd; onrestart restart zygote; onrestart restart audioserver; onrestart restart media; onrestart restart surfaceflinger; onrestart restart inputflinger; onrestart restart drm; onrestart restart cameraserver; onrestart restart keystore; onrestart restart gatekeeperd; onrestart restart thermalservice; writepid /dev/cpuset/system-background/tasks; shutdown critical |  |
| system/etc_init/surfaceflinger.rc:1 | surfaceflinger | /system/bin/surfaceflinger |  | class core animation; user system; group graphics drmrpc readproc; onrestart restart zygote; writepid /dev/stune/foreground/tasks; socket pdx/system/vr/display/client     stream 0666 system graphics u:object_r:pdx_display_client_endpoint_socket:s0; socket pdx/system/vr/display/manager    stream 0666 system graphics u:object_r:pdx_display_manager_endpoint_socket:s0; socket pdx/system/vr/display/vsync      stream 0666 system graphics u:object_r:pdx_display_vsync_endpoint_socket:s0 | SOCKET_SERVICE |
| system/etc_init/terserver.rc:4 | terservice | /system/bin/terservice | stopped | class core; user radio; group radio cache inet misc; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| system/etc_init/usbd.rc:1 | usbd | /system/bin/usbd | stopped | class late_start; oneshot; user root; group root usb system | ONESHOT_DECLARED |
| system/etc_init/wait_for_keymaster.rc:1 | wait_for_keymaster | /system/bin/wait_for_keymaster |  | user root; group root system; priority -20; ioprio rt 0 |  |
| system/etc_init/wificond.rc:1 | wificond | /system/bin/wificond | running | class main; user wifi; group wifi net_raw net_admin; capabilities NET_RAW NET_ADMIN | CAPABILITY_SERVICE |
| vendor/etc_init/android.hardware.audio@5.0-service-mediatek.rc:4 | vendor.audio-hal-2-0 | /vendor/bin/hw/android.hardware.audio@5.0-service-mediatek | running | class hal; user audioserver; # media gid needed for /dev/fm (radio) and for /data/misc/media (tee); group audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw; capabilities BLOCK_SUSPEND SYS_NICE; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks; # audioflinger restarts itself when it loses connection with the hal; # and its .rc file has an "onrestart restart audio-hal" rule, thus; # an additional auto-restart from the init process isn't needed.; oneshot; interface android.hardware.audio@5.0::IDevicesFactory default | INTERFACE_DECLARED;ONESHOT_DECLARED;CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/android.hardware.bluetooth@1.0-service-mediatek.rc:1 | bluetooth-1-0 | /vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek | running | class hal; user bluetooth; group bluetooth; writepid /dev/stune/foreground/tasks; capabilities SYS_NICE | CAPABILITY_SERVICE |
| vendor/etc_init/android.hardware.cas@1.1-service.rc:1 | vendor.cas-hal-1-1 | /vendor/bin/hw/android.hardware.cas@1.1-service | running | class hal; user media; group mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| vendor/etc_init/android.hardware.configstore@1.1-service.rc:1 | vendor.configstore-hal | /vendor/bin/hw/android.hardware.configstore@1.1-service | running | class hal animation; user system; group system |  |
| vendor/etc_init/android.hardware.drm@1.0-service.rc:1 | vendor.drm-hal-1-0 | /vendor/bin/hw/android.hardware.drm@1.0-service | running | interface android.hardware.drm@1.0::ICryptoFactory default; interface android.hardware.drm@1.0::IDrmFactory default; class hal; user media; group mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | INTERFACE_DECLARED;ENGINEERING_DIAG |
| vendor/etc_init/android.hardware.drm@1.2-service.clearkey.rc:1 | vendor.drm-clearkey-hal-1-2 | /vendor/bin/hw/android.hardware.drm@1.2-service.clearkey | running | interface android.hardware.drm@1.0::ICryptoFactory clearkey; interface android.hardware.drm@1.0::IDrmFactory clearkey; interface android.hardware.drm@1.1::ICryptoFactory clearkey; interface android.hardware.drm@1.1::IDrmFactory clearkey; interface android.hardware.drm@1.2::ICryptoFactory clearkey; interface android.hardware.drm@1.2::IDrmFactory clearkey; class hal; user media; group media mediadrm; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | INTERFACE_DECLARED;ENGINEERING_DIAG |
| vendor/etc_init/android.hardware.drm@1.2-service.widevine.rc:5 | vendor.move_data_sh | /system/bin/move_widevine_data.sh |  | class late_start; user media; group media mediadrm system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| vendor/etc_init/android.hardware.drm@1.2-service.widevine.rc:12 | vendor.drm-widevine-hal-1-2 | /vendor/bin/hw/android.hardware.drm@1.2-service.widevine | running | interface android.hardware.drm@1.0::ICryptoFactory widevine; interface android.hardware.drm@1.0::IDrmFactory widevine; interface android.hardware.drm@1.1::ICryptoFactory widevine; interface android.hardware.drm@1.1::IDrmFactory widevine; interface android.hardware.drm@1.2::ICryptoFactory widevine; interface android.hardware.drm@1.2::IDrmFactory widevine; interface android.hidl.base@1.0::IBase widevine; class hal; user media; group media mediadrm drmrpc system; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | INTERFACE_DECLARED;ENGINEERING_DIAG |
| vendor/etc_init/android.hardware.gatekeeper@1.0-service.rc:1 | vendor.gatekeeper-1-0 | /vendor/bin/hw/android.hardware.gatekeeper@1.0-service | running | class hal; user system; group system |  |
| vendor/etc_init/android.hardware.gnss@2.0-service-mediatek.rc:1 | gnss_service | /vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek | running | class hal; user system; group system gps |  |
| vendor/etc_init/android.hardware.graphics.allocator@2.0-service.rc:1 | vendor.gralloc-2-0 | /vendor/bin/hw/android.hardware.graphics.allocator@2.0-service | running | class hal animation; interface android.hardware.graphics.allocator@2.0::IAllocator default; user system; group graphics drmrpc; capabilities SYS_NICE; onrestart restart surfaceflinger | INTERFACE_DECLARED;CAPABILITY_SERVICE |
| vendor/etc_init/android.hardware.graphics.composer@2.1-service.rc:1 | vendor.hwcomposer-2-1 | /vendor/bin/hw/android.hardware.graphics.composer@2.1-service | running | class hal animation; user system; group graphics drmrpc; capabilities SYS_NICE; onrestart restart surfaceflinger; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE |
| vendor/etc_init/android.hardware.health@2.0-service.rc:1 | health-hal-2-0 | /vendor/bin/hw/android.hardware.health@2.0-service | running | class hal; user system; group system; capabilities WAKE_ALARM; file /dev/kmsg w | CAPABILITY_SERVICE |
| vendor/etc_init/android.hardware.keymaster@4.0-service.rc:1 | vendor.keymaster-4-0 | /vendor/bin/hw/android.hardware.keymaster@4.0-service | running | class early_hal; user system; group system drmrpc |  |
| vendor/etc_init/android.hardware.light@2.0-service-mediatek.rc:1 | light-hal-2-0 | /vendor/bin/hw/android.hardware.light@2.0-service-mediatek | running | interface android.hardware.light@2.0::ILight default; class hal; user system; group system; # shutting off lights while powering-off; shutdown critical | INTERFACE_DECLARED |
| vendor/etc_init/android.hardware.media.omx@1.0-service.rc:1 | mediacodec | /vendor/bin/hw/android.hardware.media.omx@1.0-service |  | class main; user mediacodec; group camera drmrpc mediadrm media_rw sdcard_rw media system audio inet net_bt net_bt_admin net_bw_acct sdcard_r; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| vendor/etc_init/android.hardware.memtrack@1.0-service.rc:1 | vendor.memtrack-hal-1-0 | /vendor/bin/hw/android.hardware.memtrack@1.0-service | running | class hal; user system; group system |  |
| vendor/etc_init/android.hardware.secure_element@1.0-service-mediatek.rc:1 | mtk_secure_element_hal_service | /vendor/bin/hw/android.hardware.secure_element@1.0-service-mediatek | running | class hal; user secure_element; group secure_element |  |
| vendor/etc_init/android.hardware.sensors@2.0-service-mediatek.rc:1 | vendor.sensors-hal-2-0 | /vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek | running | class main; user system; group system; rlimit rtprio 10 10 |  |
| vendor/etc_init/android.hardware.thermal@1.0-service.rc:1 | vendor.thermal-hal-1-0 | /vendor/bin/hw/android.hardware.thermal@1.0-service | running | class hal; user system; group system |  |
| vendor/etc_init/android.hardware.usb@1.1-service-mediatek.rc:16 | vendor.usb-hal-1-1 | /vendor/bin/hw/android.hardware.usb@1.1-service-mediatek | running | class hal; user system; group system |  |
| vendor/etc_init/android.hardware.vibrator@1.0-service.rc:1 | vendor.vibrator-1-0 | /vendor/bin/hw/android.hardware.vibrator@1.0-service | running | class hal; user system; group system |  |
| vendor/etc_init/android.hardware.wifi@1.0-service-lazy-mediatek.rc:1 | vendor.wifi_hal_legacy | /vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek | running | interface android.hardware.wifi@1.0::IWifi default; oneshot; disabled; class hal; capabilities NET_ADMIN NET_RAW SYS_MODULE; user wifi; group wifi gps | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED;CAPABILITY_SERVICE |
| vendor/etc_init/atcid.rc:1 | atcid-daemon-u | /vendor/bin/atcid |  | interface vendor.mediatek.hardware.atci@1.0::IAtcid default; class hal; class main; socket adb_atci_socket stream 660 radio system; user system; group radio system media bluetooth wifi; disabled; oneshot | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/audiocmdservice_atci.rc:1 | audio-daemon | /vendor/bin/audiocmdservice_atci |  | class main; socket atci-audio stream 666 system system; user system; group system audio; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;ENGINEERING_DIAG |
| vendor/etc_init/camerahalserver.rc:1 | camerahalserver | /vendor/bin/hw/camerahalserver | running | class main; user cameraserver; group audio camera input drmrpc sdcard_rw system media graphics; ioprio rt 4; capabilities SYS_NICE; writepid /dev/cpuset/camera-daemon/tasks /dev/stune/top-app/tasks | CAPABILITY_SERVICE |
| vendor/etc_init/em_hidl_user.rc:1 | emhidl_user | /vendor/bin/em_hidl |  | interface vendor.mediatek.hardware.engineermode@1.2::IEmd EmHidlServer; class hal; oneshot; disabled; user system; group bluetooth | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED;ENGINEERING_DIAG |
| vendor/etc_init/fuelgauged_nvram_init.rc:1 | fuelgauged_nvram | /vendor/bin/fuelgauged_nvram |  | class main; user system; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/gsm0710muxd.rc:1 | vendor.gsm0710muxd | /vendor/bin/gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic | running | class main; user root; group radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hostapd.android.rc:14 | hostapd | /vendor/bin/hw/hostapd |  | interface android.hardware.wifi.hostapd@1.0::IHostapd default; interface android.hardware.wifi.hostapd@1.1::IHostapd default; class main; capabilities NET_ADMIN NET_RAW; user wifi; group wifi net_raw net_admin; disabled; oneshot | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED;CAPABILITY_SERVICE |
| vendor/etc_init/hw/factory_init.connectivity.rc:34 | wmt_loader | /vendor/bin/wmt_loader | stopped | user system; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/hw/factory_init.connectivity.rc:39 | wmt_launcher | /vendor/bin/wmt_launcher -p /vendor/firmware/ | running | user system; group system |  |
| vendor/etc_init/hw/factory_init.connectivity.rc:55 | wlan_assistant | /vendor/bin/wlan_assistant |  | class main; user wifi; group system gps inet |  |
| vendor/etc_init/hw/factory_init.rc:644 | thermal_manager | /vendor/bin/thermal_manager | stopped | user root; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/hw/factory_init.rc:649 | vendor.ccci_fsd | /vendor/bin/ccci_fsd 0 | running | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/factory_init.rc:654 | ccci_mdinit | /vendor/bin/ccci_mdinit 0 |  | user system; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/factory_init.rc:659 | vendor.ccci3_fsd | /vendor/bin/ccci_fsd 2 | running | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/factory_init.rc:664 | ccci3_mdinit | /vendor/bin/ccci_mdinit 2 |  | user system; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/factory_init.rc:669 | ccci_rpcd | /vendor/bin/ccci_rpcd 0 |  | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/factory_init.rc:680 | mdlogger | /system/bin/mdlogger |  | class main; user shell; group shell system radio sdcard_rw inet; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:687 | dualmdlogger | /system/bin/dualmdlogger |  | class main; user shell; group shell system radio sdcard_rw inet; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:694 | factory_no_image | /vendor/bin/factory |  | user radio system wifi media audio gps; group radio system wifi media audio gps bluetooth wakelock; capabilities BLOCK_SUSPEND NET_RAW NET_ADMIN SYS_ADMIN SYS_BOOT | CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:699 | emdlogger1 | /system/bin/emdlogger1 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:706 | emdlogger2 | /system/bin/emdlogger2 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:713 | emdlogger3 | /system/bin/emdlogger3 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:720 | emdlogger5 | /system/bin/emdlogger5 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:727 | emdlogger6 | /system/bin/emdlogger6 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/factory_init.rc:734 | connsyslogger | /vendor/bin/connsyslogger |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/init.connectivity.rc:44 | mnld | /vendor/bin/mnld |  | class main; user gps; group gps inet misc sdcard_rw sdcard_r media_rw system radio wakelock; socket mnld stream 660 gps system | SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.connectivity.rc:51 | lbs_dbg | /system/bin/lbs_dbg | running | class main; user system; group gps inet misc sdcard_rw sdcard_r media_rw system radio; socket lbs_dbg stream 660 gps system | SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.connectivity.rc:63 | wpa_supplicant | /vendor/bin/hw/wpa_supplicant \ |  | -g@android:wpa_wlan0; interface android.hardware.wifi.supplicant@1.0::ISupplicant default; interface android.hardware.wifi.supplicant@1.1::ISupplicant default; interface android.hardware.wifi.supplicant@1.2::ISupplicant default; socket wpa_wlan0 dgram 660 wifi wifi; class main; disabled; oneshot | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| vendor/etc_init/hw/init.modem.rc:22 | md_minilog_util | /vendor/bin/md_minilog_util |  | user radio; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.modem.rc:28 | viarild | /vendor/bin/viarild -l /vendor/lib/libviatelecom-withuim-ril.so -- -d /dev/ttySDIO4 |  | class core; socket rild-via stream 660 radio radio; socket msap_c2k_socket1 stream 660 radio radio; socket msap_c2k_socket2 stream 660 radio radio; socket msap_c2k_socket3 stream 660 radio radio; socket msap_c2k_socket4 stream 660 radio radio; socket rpc stream 660 radio system; socket rildc-debug stream 660 radio system; socket rild-ctclient stream 660 radio system; socket rild-atci-c2k stream 660 radio system; group radio cache inet misc audio sdcard_r sdcard_rw log system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/init.modem.rc:47 | permission_check | /vendor/bin/permission_check |  | class main; user root; group system radio; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.modem.rc:53 | pppd_gprs | /system/bin/pppd_dt /dev/ttyACM0 debug noauth refuse-eap usepeerdns nodetach |  | user root; group radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.mt6762.rc:1009 | pppoe_wlan0 | /vendor/bin/launchpppoe |  | user root; group radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.mt6762.rc:1016 | cmmb | /system/bin/cmmbsp |  | class main; user root; group system audio camera graphics inet net_bt net_bt_admin net_bw_acct media; ioprio rt 4 |  |
| vendor/etc_init/hw/init.mt6762.rc:1076 | meta_tst | /vendor/bin/meta_tst |  | class main; user system; group radio gps system wifi audio bluetooth media; capabilities NET_RAW NET_ADMIN; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/init.mt6765.rc:1009 | pppoe_wlan0 | /vendor/bin/launchpppoe |  | user root; group radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/init.mt6765.rc:1016 | cmmb | /system/bin/cmmbsp |  | class main; user root; group system audio camera graphics inet net_bt net_bt_admin net_bw_acct media; ioprio rt 4 |  |
| vendor/etc_init/hw/init.mt6765.rc:1076 | meta_tst | /vendor/bin/meta_tst |  | class main; user system; group radio gps system wifi audio bluetooth media; capabilities NET_RAW NET_ADMIN; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/init.project.rc:51 | fuse_usbotg | /system/bin/sdcard -u 1023 -g 1023 -w 1023 -d /mnt/media_rw/usbotg /storage/usbotg |  | class late_start; disabled | DISABLED_DECLARED |
| vendor/etc_init/hw/meta_init.connectivity.rc:28 | wmt_loader | /vendor/bin/wmt_loader | stopped | user system; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/hw/meta_init.connectivity.rc:33 | wmt_launcher | /vendor/bin/wmt_launcher -p /vendor/firmware/ | running | user system; group system |  |
| vendor/etc_init/hw/meta_init.connectivity.rc:49 | wlan_assistant | /vendor/bin/wlan_assistant |  | class main; user wifi; group system gps inet |  |
| vendor/etc_init/hw/meta_init.modem.rc:31 | vendor.ccci_fsd | /vendor/bin/ccci_fsd 0 | running | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.modem.rc:36 | ccci_mdinit | /vendor/bin/ccci_mdinit 0 |  | user system; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.modem.rc:41 | vendor.ccci3_fsd | /vendor/bin/ccci_fsd 2 | running | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.modem.rc:46 | ccci3_mdinit | /vendor/bin/ccci_mdinit 2 |  | user system; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.modem.rc:51 | ccci_rpcd | /vendor/bin/ccci_rpcd 0 |  | user radio; group radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.rc:571 | emdlogger1 | /system/bin/emdlogger1 |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw | RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/meta_init.rc:576 | meta_tst | /vendor/bin/meta_tst |  | user radio system wifi gps; group radio system wifi audio bluetooth media gps | RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/meta_init.rc:580 | connsyslogger | /system/bin/connsyslogger |  | class main; user shell; group system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/hw/meta_init.rc:585 | loghidlvendorservice | /vendor/bin/loghidlvendorservice |  | class main; user shell; group system shell radio | RADIO_IMS_WFC |
| vendor/etc_init/hw/meta_init.rc:590 | loghidlsysservice | /system/bin/loghidlsysservice |  | class main; user shell; group system shell radio | RADIO_IMS_WFC |
| vendor/etc_init/init.bip.rc:19 | vendor.bip | /vendor/bin/bip | running | class core; socket vendor.bip stream 660 root system; user root; group system log inet radio net_admin root; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.cccifsd.rc:22 | vendor.ccci_fsd | /vendor/bin/ccci_fsd 0 | running | user radio; group radio system root; class core; priority -20; ioprio rt 4; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.cccifsd.rc:30 | vendor.ccci3_fsd | /vendor/bin/ccci_fsd 2 | running | user radio; group  radio system; class core; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.cccimdinit.rc:21 | ccci_mdinit | /vendor/bin/ccci_mdinit 0 |  | user system; group radio system; class core; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.cccimdinit.rc:27 | ccci3_mdinit | /vendor/bin/ccci_mdinit 2 |  | user system; group radio system; class core; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.cccirpcd.rc:3 | vendor.ccci_rpcd | /vendor/bin/ccci_rpcd 0 | running | user radio; group radio system; class core; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.thermal.rc:1 | thermal | /vendor/bin/thermal | running | class main; user system; group system |  |
| vendor/etc_init/init.thermal_manager.rc:1 | thermal_manager | /vendor/bin/thermal_manager | stopped | class main; user root; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/init.thermalloadalgod.rc:1 | thermalloadalgod | /vendor/bin/thermalloadalgod | running | class main; user system; group system |  |
| vendor/etc_init/init.volte_imcb.rc:3 | vendor.volte_imcb | /vendor/bin/volte_imcb | running | class main; socket volte_imsa1 stream 660 root radio; socket volte_imsa2 stream 660 root radio; socket volte_ut stream 660 root radio; user root; group system log inet radio; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.volte_imsm_93.rc:51 | volte_imsm_93 | /vendor/bin/volte_imsm_93 | running | class main; user root; group radio cache inet misc log system wifi; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.volte_md_status.rc:8 | volte_md_status | /system/vendor/bin/volte_md_status | running | class core; user root; group radio log system; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/init.volte_stack.rc:4 | vendor.volte_stack | /vendor/bin/volte_stack | running | class main; socket volte_stack stream 660 root system; user root; group system log inet radio; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.volte_ua.rc:3 | vendor.volte_ua | /vendor/bin/volte_ua | running | class main; socket volte_ua stream 660 root system; user root; group system log inet radio net_admin root; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.vtservice_hidl.rc:3 | vtservice_hidl | /vendor/bin/hw/vtservice_hidl | running | class hal; socket volte_vt stream 660 root system; user system; group system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw; ioprio rt 4 | SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.wfca.rc:5 | wfca | /vendor/bin/wfca | running | class core; socket wfca stream 660 root system; socket wfca_rds stream 660 root system; user root; group system log inet radio net_admin root | SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init.wod.rc:26 | vendor.epdg_wod | /vendor/bin/epdg_wod | running | class late_start; socket wod_action stream 660 root system; socket wod_sim    stream 660 root system; user root; group root system log inet radio net_admin; disabled | DISABLED_DECLARED;SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/init_connectivity.rc:10 | wmt_loader | /vendor/bin/wmt_loader | stopped | class early_hal; user system; group system; oneshot | ONESHOT_DECLARED |
| vendor/etc_init/init_connectivity.rc:16 | wmt_launcher | /vendor/bin/wmt_launcher -p /vendor/firmware/ -o 1 | running | class early_hal; user system; group system |  |
| vendor/etc_init/ipsec_mon.rc:1 | ipsec_mon | /vendor/bin/ipsec_mon | running | class main; user system; group net_admin net_raw system misc; capabilities NET_ADMIN NET_RAW | CAPABILITY_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/lbs_hidl_service.rc:1 | lbs_hidl_service | /vendor/bin/lbs_hidl_service | running | class hal; user system; group system gps radio inet sdcard_r sdcard_rw | RADIO_IMS_WFC |
| vendor/etc_init/mtk_agpsd_p.rc:4 | agpsd | /vendor/bin/mtk_agpsd |  | class main; socket agpsd stream 660 gps system; socket agpsd2 stream 660 gps inet; socket agpsd3 stream 660 gps inet; user gps; group gps radio inet sdcard_r sdcard_rw misc system | SOCKET_SERVICE;RADIO_IMS_WFC |
| vendor/etc_init/mtkrild.rc:5 | vendor.ril-daemon-mtk | /vendor/bin/hw/mtkfusionrild -l libmtk-ril.so | running | class main; socket sap_uim_socket1 stream 660 bluetooth bluetooth; socket rild-oem stream 660 radio system; socket volte_imsvt1 stream 660 root radio; socket rild-atci stream 660 root radio; user radio; group radio cache inet misc audio log readproc wakelock sdcard_r sdcard_rw system; capabilities BLOCK_SUSPEND NET_ADMIN NET_RAW; oneshot; disabled | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE;CAPABILITY_SERVICE;RADIO_IMS_WFC;ENGINEERING_DIAG |
| vendor/etc_init/muxreport.rc:1 | vendor.muxreport-daemon | /vendor/bin/muxreport |  | class core; user radio; group radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/netdagent.rc:1 | netdagent | /vendor/bin/netdagent | running | class late_start; #socket netdagent stream 0660 root system; user system; group net_admin net_raw system misc; capabilities NET_ADMIN NET_RAW | CAPABILITY_SERVICE |
| vendor/etc_init/nvram_daemon.rc:1 | nvram_daemon | /vendor/bin/nvram_daemon | stopped | class core; user root; group system radio wifi audio bluetooth media; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| vendor/etc_init/vendor.mediatek.hardware.gpu@1.0-service.rc:1 | gpu-1-0 | /vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service | running | class hal; user system; group system graphics |  |
| vendor/etc_init/vendor.mediatek.hardware.mms@1.3-service.rc:1 | mms-1-3 | /vendor/bin/hw/vendor.mediatek.hardware.mms@1.3-service | running | class main; user system; group system graphics |  |
| vendor/etc_init/vendor.mediatek.hardware.mtkcodecservice@1.1-service.rc:1 | mtkcodecservice-1-1 | /vendor/bin/hw/vendor.mediatek.hardware.mtkcodecservice@1.1-service | stopped | interface vendor.mediatek.hardware.mtkcodecservice@1.1::IMtkCodecService default; oneshot; disabled; class hal; user system; group audio media sdcard_r | INTERFACE_DECLARED;DISABLED_DECLARED;ONESHOT_DECLARED |
| vendor/etc_init/vendor.mediatek.hardware.mtkpower@1.0-service.rc:1 | power-hal-1-0 | /vendor/bin/hw/vendor.mediatek.hardware.mtkpower@1.0-service | running | class hal; capabilities NET_ADMIN NET_RAW SYS_MODULE; user system; group system readproc wifi media media_rw camera audio | CAPABILITY_SERVICE |
| vendor/etc_init/vendor.mediatek.hardware.nvram@1.1-sevice.rc:1 | nvram-hidl-1-1 | /vendor/bin/hw/vendor.mediatek.hardware.nvram@1.1-service | running | class hal; user root; group system bluetooth media audio radio | RADIO_IMS_WFC |
| vendor/etc_init/vendor.mediatek.hardware.pq@2.2-service.rc:1 | pq-2-2 | /vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service | running | class main; user system; group system graphics |  |
| vendor/etc_init/wlan_assistant.rc:1 | wlan_assistant | /vendor/bin/wlan_assistant |  | class main; user wifi; group system gps inet |  |

################################################################################
# 2.13 — LIVE HAL-RELATED PROCESS MAP
################################################################################

| SELinux domain | User | PID | PPID | Name | Args | Classification |
|---|---|---|---|---|---|---|
| u:r:audioserver:s0 | audioserver | 544 | 1 | audioserver | audioserver | [PROCESS_ACTIVE] |
| u:r:drmserver:s0 | drm | 649 | 1 | drmserver | drmserver | [PROCESS_ACTIVE] |
| u:r:epdg_wod:s0 | root | 836 | 1 | epdg_wod | epdg_wod | [PROCESS_ACTIVE] |
| u:r:gatekeeperd:s0 | system | 711 | 1 | gatekeeperd | gatekeeperd /data/misc/gatekeeper | [PROCESS_ACTIVE] |
| u:r:gpuservice:s0 | gpu_service | 547 | 1 | gpuservice | gpuservice | [PROCESS_ACTIVE] |
| u:r:hal_cas_default:s0 | media | 518 | 1 | android.hardware.cas@1.1-service | android.hardware.cas@1.1-service | [PROCESS_ACTIVE] |
| u:r:hal_configstore_default:s0 | system | 519 | 1 | android.hardware.configstore@1.1-service | android.hardware.configstore@1.1-service | [PROCESS_ACTIVE] |
| u:r:hal_drm_clearkey:s0 | media | 521 | 1 | android.hardware.drm@1.2-service.clearkey | android.hardware.drm@1.2-service.clearkey | [PROCESS_ACTIVE] |
| u:r:hal_drm_default:s0 | media | 520 | 1 | android.hardware.drm@1.0-service | android.hardware.drm@1.0-service | [PROCESS_ACTIVE] |
| u:r:hal_drm_widevine:s0 | media | 522 | 1 | android.hardware.drm@1.2-service.widevine | android.hardware.drm@1.2-service.widevine | [PROCESS_ACTIVE] |
| u:r:hal_gatekeeper_default:s0 | system | 523 | 1 | android.hardware.gatekeeper@1.0-service | android.hardware.gatekeeper@1.0-service | [PROCESS_ACTIVE] |
| u:r:hal_graphics_allocator_default:s0 | system | 525 | 1 | android.hardware.graphics.allocator@2.0-service | android.hardware.graphics.allocator@2.0-service | [PROCESS_ACTIVE] |
| u:r:hal_graphics_composer_default:s0 | system | 526 | 1 | android.hardware.graphics.composer@2.1-service | android.hardware.graphics.composer@2.1-service | [PROCESS_ACTIVE] |
| u:r:hal_health_default:s0 | system | 527 | 1 | android.hardware.health@2.0-service | android.hardware.health@2.0-service | [PROCESS_ACTIVE] |
| u:r:hal_keymaster_default:s0 | system | 413 | 1 | android.hardware.keymaster@4.0-service | android.hardware.keymaster@4.0-service | [PROCESS_ACTIVE] |
| u:r:hal_memtrack_default:s0 | system | 529 | 1 | android.hardware.memtrack@1.0-service | android.hardware.memtrack@1.0-service | [PROCESS_ACTIVE] |
| u:r:hal_thermal_default:s0 | system | 532 | 1 | android.hardware.thermal@1.0-service | android.hardware.thermal@1.0-service | [PROCESS_ACTIVE] |
| u:r:hal_vibrator_default:s0 | system | 536 | 1 | android.hardware.vibrator@1.0-service | android.hardware.vibrator@1.0-service | [PROCESS_ACTIVE] |
| u:r:hwservicemanager:s0 | system | 361 | 1 | hwservicemanager | hwservicemanager | [PROCESS_ACTIVE] |
| u:r:ipsec_mon:s0 | system | 702 | 1 | ipsec_mon | ipsec_mon | [PROCESS_ACTIVE] |
| u:r:kernel:s0 | root | 198 | 2 | [mtk_vibrator] | [mtk_vibrator] | [PROCESS_ACTIVE] |
| u:r:kernel:s0 | root | 199 | 2 | [usb20_st_wq] | [usb20_st_wq] | [PROCESS_ACTIVE] |
| u:r:kernel:s0 | root | 200 | 2 | [usb20_low_power] | [usb20_low_power] | [PROCESS_ACTIVE] |
| u:r:kernel:s0 | root | 666 | 2 | [mtk_wmtd] | [mtk_wmtd] | [PROCESS_ACTIVE] |
| u:r:kernel:s0 | root | 670 | 2 | [mtk_wmtd_worker] | [mtk_wmtd_worker] | [PROCESS_ACTIVE] |
| u:r:lbs_dbg:s0 | system | 639 | 1 | lbs_dbg | lbs_dbg | [PROCESS_ACTIVE] |
| u:r:lbs_hidl_service:s0 | system | 538 | 1 | lbs_hidl_service | lbs_hidl_service | [PROCESS_ACTIVE] |
| u:r:mediacodec:s0 | mediacodec | 693 | 1 | media.codec | media.codec hw/android.hardware.media.omx@1.0-service | [PROCESS_ACTIVE] |
| u:r:mediadrmserver:s0 | media | 657 | 1 | mediadrmserver | mediadrmserver | [PROCESS_ACTIVE] |
| u:r:mediaswcodec:s0 | mediacodec | 709 | 1 | media.swcodec | media.swcodec oid.media.swcodec/bin/mediaswcodec | [PROCESS_ACTIVE] |
| u:r:mnld:s0 | gps | 638 | 1 | mnld | mnld | [PROCESS_ACTIVE] |
| u:r:mtk_hal_audio:s0 | audioserver | 516 | 1 | android.hardware.audio@5.0-service-mediatek | android.hardware.audio@5.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_bluetooth:s0 | bluetooth | 517 | 1 | android.hardware.bluetooth@1.0-service-mediatek | android.hardware.bluetooth@1.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_camera:s0 | cameraserver | 696 | 1 | camerahalserver | camerahalserver | [PROCESS_ACTIVE] |
| u:r:mtk_hal_gnss:s0 | system | 524 | 1 | android.hardware.gnss@2.0-service-mediatek | android.hardware.gnss@2.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_gpu:s0 | system | 539 | 1 | vendor.mediatek.hardware.gpu@1.0-service | vendor.mediatek.hardware.gpu@1.0-service | [PROCESS_ACTIVE] |
| u:r:mtk_hal_light:s0 | system | 528 | 1 | android.hardware.light@2.0-service-mediatek | android.hardware.light@2.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_mms:s0 | system | 704 | 1 | vendor.mediatek.hardware.mms@1.3-service | vendor.mediatek.hardware.mms@1.3-service | [PROCESS_ACTIVE] |
| u:r:mtk_hal_power:s0 | system | 540 | 1 | vendor.mediatek.hardware.mtkpower@1.0-service | vendor.mediatek.hardware.mtkpower@1.0-service | [PROCESS_ACTIVE] |
| u:r:mtk_hal_pq:s0 | system | 706 | 1 | vendor.mediatek.hardware.pq@2.2-service | vendor.mediatek.hardware.pq@2.2-service | [PROCESS_ACTIVE] |
| u:r:mtk_hal_secure_element:s0 | secure_element | 531 | 1 | android.hardware.secure_element@1.0-service-mediatek | android.hardware.secure_element@1.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_sensors:s0 | system | 694 | 1 | android.hardware.sensors@2.0-service-mediatek | android.hardware.sensors@2.0-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_usb:s0 | system | 534 | 1 | android.hardware.usb@1.1-service-mediatek | android.hardware.usb@1.1-service-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_hal_wifi:s0 | wifi | 1229 | 1 | android.hardware.wifi@1.0-service-lazy-mediatek | android.hardware.wifi@1.0-service-lazy-mediatek | [PROCESS_ACTIVE] |
| u:r:mtk_wmt_launcher:s0 | system | 415 | 1 | wmt_launcher | wmt_launcher -p /vendor/firmware/ -o 1 | [PROCESS_ACTIVE] |
| u:r:netdagent:s0 | system | 721 | 1 | netdagent | netdagent | [PROCESS_ACTIVE] |
| u:r:nvram_agent_binder:s0 | root | 541 | 1 | vendor.mediatek.hardware.nvram@1.1-service | vendor.mediatek.hardware.nvram@1.1-service | [PROCESS_ACTIVE] |
| u:r:rild:s0 | radio | 982 | 1 | mtkfusionrild | mtkfusionrild -l libmtk-ril.so | [PROCESS_ACTIVE] |
| u:r:secure_element:s0:c44,c260,c512,c768 | secure_element | 1940 | 512 | com.android.se | com.android.se | [PROCESS_ACTIVE] |
| u:r:servicemanager:s0 | system | 360 | 1 | servicemanager | servicemanager | [PROCESS_ACTIVE] |
| u:r:shell:s0 | shell | 18447 | 1760 | sh | sh -c su -c "echo === SECTION02 HAL RUNTIME SNAPSHOT ===; {?echo === PLATFORM_IDENTITY ===;?getprop ro.hardware;?getprop ro.boot.hardware;?getprop ro.board.platform;?getprop ro.vendor.mediatek.platform;?getprop ro.vendor.rc;?echo === INIT_SVC_HAL_RELATED ===;?getprop \| grep -E \"^\\[init\\.svc\\.(vendor\\.\|android\\.hardware\|.*hal\|.*hidl\|.*service\|vndservicemanager\|hwservicemanager\|servicemanager\|camerahalserver\|mtk\|nvram\|lbs\|wmt\|gnss\|ril\|volte\|wfca\|ipsec\|netdagent\|gpu\|pq\|mms\|secure_element\|keymaster\|gatekeeper\|sensors\|thermal\|wifi\|bluetooth\|audio\|drm\|usb\|vibrator\|light\|health\|memtrack\|composer\|gralloc)\" \| sort;?echo === HIDL_LSHAL_DEFAULT ===;?lshal 2>&1;?echo === HIDL_LSHAL_INTERFACES_IF_SUPPORTED ===;?lshal -i 2>&1;?echo === HIDL_LSHAL_PIDS_IF_SUPPORTED ===;?lshal -p 2>&1;?echo === HIDL_LSHAL_DEBUG_INFO_IF_SUPPORTED ===;?lshal -d 2>&1;?echo === BINDER_SERVICE_LIST ===;?service list;?echo === PROCESS_MAP_HAL_RELATED ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z \| grep -E \"(hwservicemanager\|servicemanager\|vndservicemanager\|android\\.hardware\|vendor\\.mediatek\|camerahalserver\|mtkfusionrild\|volte\|wfca\|epdg\|ipsec\|netdagent\|gnss\|mnld\|lbs\|wmt\|wlan\|bluetooth\|sensors\|secure_element\|keymaster\|gatekeeper\|drm\|widevine\|clearkey\|gpu\|pq\|mms\|nvram\|thermal\|usb\|vibrator\|light\|health\|memtrack\|composer\|gralloc\|audio\|cas\|codec)\" \| sort;?echo === HWSERVICE_CONTEXTS_LIVE ===;?cat /system/etc/selinux/plat_hwservice_contexts 2>/dev/null;?cat /vendor/etc/selinux/vendor_hwservice_contexts 2>/dev/null;?cat /vendor/etc/selinux/nonplat_hwservice_contexts 2>/dev/null;?echo === VINTF_RUNTIME_FILES ===;?ls -laZ /vendor/etc/vintf /system/etc/vintf /product/etc/vintf /odm/etc/vintf 2>&1;?} > /data/local/tmp/section02_runtime_snapshot.txt; sha256sum /data/local/tmp/section02_runtime_snapshot.txt; ls -l /data/local/tmp/section02_runtime_snapshot.txt" | [PROCESS_ACTIVE] |
| u:r:shell:s0 | shell | 18449 | 18447 | su | su -c echo === SECTION02 HAL RUNTIME SNAPSHOT ===; {?echo === PLATFORM_IDENTITY ===;?getprop ro.hardware;?getprop ro.boot.hardware;?getprop ro.board.platform;?getprop ro.vendor.mediatek.platform;?getprop ro.vendor.rc;?echo === INIT_SVC_HAL_RELATED ===;?getprop \| grep -E "^\[init\.svc\.(vendor\.\|android\.hardware\|.*hal\|.*hidl\|.*service\|vndservicemanager\|hwservicemanager\|servicemanager\|camerahalserver\|mtk\|nvram\|lbs\|wmt\|gnss\|ril\|volte\|wfca\|ipsec\|netdagent\|gpu\|pq\|mms\|secure_element\|keymaster\|gatekeeper\|sensors\|thermal\|wifi\|bluetooth\|audio\|drm\|usb\|vibrator\|light\|health\|memtrack\|composer\|gralloc)" \| sort;?echo === HIDL_LSHAL_DEFAULT ===;?lshal 2>&1;?echo === HIDL_LSHAL_INTERFACES_IF_SUPPORTED ===;?lshal -i 2>&1;?echo === HIDL_LSHAL_PIDS_IF_SUPPORTED ===;?lshal -p 2>&1;?echo === HIDL_LSHAL_DEBUG_INFO_IF_SUPPORTED ===;?lshal -d 2>&1;?echo === BINDER_SERVICE_LIST ===;?service list;?echo === PROCESS_MAP_HAL_RELATED ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z \| grep -E "(hwservicemanager\|servicemanager\|vndservicemanager\|android\.hardware\|vendor\.mediatek\|camerahalserver\|mtkfusionrild\|volte\|wfca\|epdg\|ipsec\|netdagent\|gnss\|mnld\|lbs\|wmt\|wlan\|bluetooth\|sensors\|secure_element\|keymaster\|gatekeeper\|drm\|widevine\|clearkey\|gpu\|pq\|mms\|nvram\|thermal\|usb\|vibrator\|light\|health\|memtrack\|composer\|gralloc\|audio\|cas\|codec)" \| sort;?echo === HWSERVICE_CONTEXTS_LIVE ===;?cat /system/etc/selinux/plat_hwservice_contexts 2>/dev/null;?cat /vendor/etc/selinux/vendor_hwservice_contexts 2>/dev/null;?cat /vendor/etc/selinux/nonplat_hwservice_contexts 2>/dev/null;?echo === VINTF_RUNTIME_FILES ===;?ls -laZ /vendor/etc/vintf /system/etc/vintf /product/etc/vintf /odm/etc/vintf 2>&1;?} > /data/local/tmp/section02_runtime_snapshot.txt; sha256sum /data/local/tmp/section02_runtime_snapshot.txt; ls -l /data/local/tmp/section02_runtime_snapshot.txt | [PROCESS_ACTIVE] |
| u:r:system_app:s0 | system | 2519 | 512 | com.wiite.wearhealthuart | com.wiite.wearhealthuart | [PROCESS_ACTIVE] |
| u:r:thermal:s0 | system | 699 | 1 | thermal | thermal | [PROCESS_ACTIVE] |
| u:r:thermald:s0 | system | 652 | 1 | thermald | thermald | [PROCESS_ACTIVE] |
| u:r:thermalloadalgod:s0 | system | 701 | 1 | thermalloadalgod | thermalloadalgod | [PROCESS_ACTIVE] |
| u:r:vndservicemanager:s0 | system | 362 | 1 | vndservicemanager | vndservicemanager /dev/vndbinder | [PROCESS_ACTIVE] |
| u:r:volte_imcb:s0 | system | 835 | 1 | volte_imcb | volte_imcb | [PROCESS_ACTIVE] |
| u:r:volte_imsm_93:s0 | root | 820 | 1 | volte_imsm_93 | volte_imsm_93 | [PROCESS_ACTIVE] |
| u:r:volte_md_status:s0 | root | 369 | 1 | volte_md_status | volte_md_status | [PROCESS_ACTIVE] |
| u:r:volte_stack:s0 | root | 823 | 1 | volte_stack | volte_stack | [PROCESS_ACTIVE] |
| u:r:volte_ua:s0 | root | 827 | 1 | volte_ua | volte_ua | [PROCESS_ACTIVE] |
| u:r:wfca:s0 | root | 565 | 1 | wfca | wfca | [PROCESS_ACTIVE] |
| u:r:wlan_assistant:s0 | wifi | 708 | 1 | wlan_assistant | wlan_assistant | [PROCESS_ACTIVE] |

################################################################################
# 2.14 — SELINUX HWSERVICE CONTEXT AUTHORITY
################################################################################

Present context roots:
- `/vendor/etc/selinux/vendor_hwservice_contexts` [VERIFIED]
- `/system/etc/selinux/plat_hwservice_contexts` [VERIFIED]

Absent context roots in targeted live capture:
- `/system_ext/etc/selinux/system_ext_hwservice_contexts` [ABSENT_NAMESPACE_ROOT]
- `/product/etc/selinux/product_hwservice_contexts` [ABSENT_NAMESPACE_ROOT]
- `/odm/etc/selinux/odm_hwservice_contexts` [ABSENT_NAMESPACE_ROOT]

Vendor hwservice contexts:
| Hwservice key | SELinux object context |
|---|---|
| vendor.mediatek.hardware.bluetooth::IMtkBluetoothHci | u:object_r:mtk_hal_bluetooth_hwservice:s0 |
| vendor.mediatek.hardware.mtkradioex::IMtkRadioEx | u:object_r:mtk_hal_rild_hwservice:s0 |
| vendor.mediatek.hardware.radio::ISap | u:object_r:mtk_hal_rild_hwservice:s0 |
| vendor.mediatek.hardware.interfaces_tc1.mtkradioex_tc1::IMtkRadioEx | u:object_r:mtk_hal_rild_hwservice:s0 |
| vendor.mediatek.hardware.radio_op::IRadioOp | u:object_r:mtk_hal_rild_hwservice:s0 |
| vendor.mediatek.hardware.mtkpower::IMtkPerf | u:object_r:mtk_hal_power_hwservice:s0 |
| vendor.mediatek.hardware.mtkpower::IMtkPower | u:object_r:mtk_hal_power_hwservice:s0 |
| vendor.mediatek.hardware.power::IPerf | u:object_r:mtk_hal_power_hwservice:s0 |
| vendor.mediatek.hardware.power::IPower | u:object_r:mtk_hal_power_hwservice:s0 |
| vendor.mediatek.hardware.lbs::ILbs | u:object_r:mtk_hal_lbs_hwservice:s0 |
| vendor.mediatek.hardware.imsa::IImsa | u:object_r:mtk_hal_imsa_hwservice:s0 |
| vendor.mediatek.hardware.nvram::INvram | u:object_r:nvram_agent_binder_hwservice:s0 |
| vendor.mediatek.hardware.pq::IPictureQuality | u:object_r:mtk_hal_pq_hwservice:s0 |
| vendor.mediatek.hardware.keymaster_attestation::IKeymasterDevice | u:object_r:mtk_hal_keyattestation_hwservice:s0 |
| vendor.mediatek.hardware.fm::IFmRadio | u:object_r:mtk_hal_fm_hwservice:s0 |
| vendor.mediatek.hardware.log::ILog | u:object_r:mtk_hal_log_hwservice:s0 |
| vendor.mediatek.hardware.engineermode::IEmd | u:object_r:mtk_hal_em_hwservice:s0 |
| vendor.mediatek.hardware.mms::IMms | u:object_r:mtk_hal_mms_hwservice:s0 |
| vendor.mediatek.hardware.gpu::IGraphicExt | u:object_r:mtk_hal_gpu_hwservice:s0 |
| vendor.mediatek.hardware.modemdbfilter::ICopyDBFilter | u:object_r:mtk_hal_md_dbfilter_hwservice:s0 |
| vendor.mediatek.hardware.camera.lomoeffect::ILomoEffect | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.camera.ccap::ICCAPControl | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.camera.bgservice::IBGService | u:object_r:mtk_hal_bgs_hwservice:s0 |
| vendor.mediatek.hardware.camera.isphal::IISPModule | u:object_r:mtk_hal_bgs_hwservice:s0 |
| vendor.mediatek.hardware.camera.postproc::IPostDevice | u:object_r:mtk_hal_bgs_hwservice:s0 |
| vendor.mediatek.hardware.hdmi::IMtkHdmiService | u:object_r:mtk_hal_hdmi_hwservice:s0 |
| vendor.mediatek.hardware.camera.atms::IATMs | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.camera.advcam::IAdvCamControl | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.wfo::IWifiOffload | u:object_r:mtk_hal_wfo_hwservice:s0 |
| vendor.mediatek.hardware.camera.lomoeffect::ILomoEffect | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.keyinstall::IKeyinstall | u:object_r:mtk_hal_keyinstall_hwservice:s0 |
| vendor.mediatek.hardware.videotelephony::IVideoTelephony | u:object_r:mtk_hal_videotelephony_hwservice:s0 |
| vendor.mediatek.hardware.presence::IPresence | u:object_r:volte_uce_ua_hwservice:s0 |
| vendor.mediatek.hardware.mtkcodecservice::IMtkCodecService | u:object_r:hal_mtkcodecservice_hwservice:s0 |
| vendor.mediatek.hardware.netdagent::INetdagent | u:object_r:mtk_hal_netdagent_hwservice:s0 |
| vendor.mediatek.hardware.rcs::IRcs | u:object_r:volte_rcs_ua_hwservice:s0 |
| vendor.mediatek.hardware.camera.callbackclient::IMtkCallbackClient | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.camera.ccap::ICCAPControl | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.dfps::IFpsPolicyService | u:object_r:mtk_hal_dfps_hwservice:s0 |
| vendor.mediatek.hardware.dplanner::IDPlanner | u:object_r:mtk_hal_dplanner_hwservice:s0 |
| vendor.mediatek.hardware.keymanage::IKeymanage | u:object_r:mtk_hal_keymanage_hwservice:s0 |
| vendor.mediatek.hardware.pplagent::IPplAgent | u:object_r:mtk_hal_pplagent_hwservice:s0 |
| vendor.mediatek.hardware.atci::IAtcid | u:object_r:hal_atci_hwservice:s0 |
| vendor.mediatek.hardware.dmc::IDmcService | u:object_r:mtk_hal_dmc_hwservice:s0 |
| vendor.mediatek.hardware.apmonitor::IApmService | u:object_r:mtk_hal_apm_hwservice:s0 |
| vendor.microtrust.hardware.thh::IThhDevice | u:object_r:hal_thh_hwservice:s0 |
| vendor.microtrust.hardware.tui::ITuiDevice | u:object_r:hal_tui_hwservice:s0 |
| vendor.microtrust.hardware.capi::IClientApiDevice | u:object_r:hal_capi_hwservice:s0 |
| vendor.mediatek.hardware.wifi.supplicant::ISupplicant | u:object_r:hal_wifi_supplicant_hwservice:s0 |
| vendor.mediatek.hardware.camera.security::ISecureCamera | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.camera.frhandler::IFRHandler | u:object_r:hal_camera_hwservice:s0 |
| vendor.mediatek.hardware.mdmonitor::IMDMonitorService | u:object_r:mtk_mdm_hidl_server:s0 |
| vendor.mediatek.hardware.omadm::IOmadm | u:object_r:mtk_hal_omadm_hwservice:s0 |
| vendor.mediatek.hardware.nwk_opt::INwkOpt | u:object_r:mtk_hal_nwk_opt_hwservice:s0 |
| vendor.mediatek.hardware.clientapi::IClientapi | u:object_r:volte_clientapi_ua_hwservice:s0 |
| vendor.trustonic.tee::ITee | u:object_r:hal_tee_hwservice:s0 |
| vendor.trustonic.tee.tui::ITui | u:object_r:hal_tee_hwservice:s0 |
| vendor.trustonic.teeregistry::ITeeRegistry | u:object_r:hal_teeregistry_hwservice:s0 |

Framework/platform hwservice contexts:
| Hwservice key | SELinux object context |
|---|---|
| android.frameworks.bufferhub::IBufferHub | u:object_r:fwk_bufferhub_hwservice:s0 |
| android.frameworks.cameraservice.service::ICameraService | u:object_r:fwk_camera_hwservice:s0 |
| android.frameworks.displayservice::IDisplayService | u:object_r:fwk_display_hwservice:s0 |
| android.frameworks.schedulerservice::ISchedulingPolicyService | u:object_r:fwk_scheduler_hwservice:s0 |
| android.frameworks.sensorservice::ISensorManager | u:object_r:fwk_sensor_hwservice:s0 |
| android.frameworks.stats::IStats | u:object_r:fwk_stats_hwservice:s0 |
| android.hardware.atrace::IAtraceDevice | u:object_r:hal_atrace_hwservice:s0 |
| android.hardware.audio.effect::IEffectsFactory | u:object_r:hal_audio_hwservice:s0 |
| android.hardware.audio::IDevicesFactory | u:object_r:hal_audio_hwservice:s0 |
| android.hardware.authsecret::IAuthSecret | u:object_r:hal_authsecret_hwservice:s0 |
| android.hardware.automotive.audiocontrol::IAudioControl | u:object_r:hal_audiocontrol_hwservice:s0 |
| android.hardware.automotive.evs::IEvsEnumerator | u:object_r:hal_evs_hwservice:s0 |
| android.hardware.automotive.vehicle::IVehicle | u:object_r:hal_vehicle_hwservice:s0 |
| android.hardware.biometrics.face::IBiometricsFace | u:object_r:hal_face_hwservice:s0 |
| android.hardware.biometrics.fingerprint::IBiometricsFingerprint | u:object_r:hal_fingerprint_hwservice:s0 |
| android.hardware.bluetooth::IBluetoothHci | u:object_r:hal_bluetooth_hwservice:s0 |
| android.hardware.bluetooth.a2dp::IBluetoothAudioOffload | u:object_r:hal_audio_hwservice:s0 |
| android.hardware.bluetooth.audio::IBluetoothAudioProvidersFactory | u:object_r:hal_audio_hwservice:s0 |
| android.hardware.boot::IBootControl | u:object_r:hal_bootctl_hwservice:s0 |
| android.hardware.broadcastradio::IBroadcastRadio | u:object_r:hal_broadcastradio_hwservice:s0 |
| android.hardware.broadcastradio::IBroadcastRadioFactory | u:object_r:hal_broadcastradio_hwservice:s0 |
| android.hardware.camera.provider::ICameraProvider | u:object_r:hal_camera_hwservice:s0 |
| android.hardware.configstore::ISurfaceFlingerConfigs | u:object_r:hal_configstore_ISurfaceFlingerConfigs:s0 |
| android.hardware.confirmationui::IConfirmationUI | u:object_r:hal_confirmationui_hwservice:s0 |
| android.hardware.contexthub::IContexthub | u:object_r:hal_contexthub_hwservice:s0 |
| android.hardware.cas::IMediaCasService | u:object_r:hal_cas_hwservice:s0 |
| android.hardware.drm::ICryptoFactory | u:object_r:hal_drm_hwservice:s0 |
| android.hardware.drm::IDrmFactory | u:object_r:hal_drm_hwservice:s0 |
| android.hardware.dumpstate::IDumpstateDevice | u:object_r:hal_dumpstate_hwservice:s0 |
| android.hardware.gatekeeper::IGatekeeper | u:object_r:hal_gatekeeper_hwservice:s0 |
| android.hardware.gnss::IGnss | u:object_r:hal_gnss_hwservice:s0 |
| android.hardware.graphics.allocator::IAllocator | u:object_r:hal_graphics_allocator_hwservice:s0 |
| android.hardware.graphics.composer::IComposer | u:object_r:hal_graphics_composer_hwservice:s0 |
| android.hardware.graphics.mapper::IMapper | u:object_r:hal_graphics_mapper_hwservice:s0 |
| android.hardware.health::IHealth | u:object_r:hal_health_hwservice:s0 |
| android.hardware.health.storage::IStorage | u:object_r:hal_health_storage_hwservice:s0 |
| android.hardware.input.classifier::IInputClassifier | u:object_r:hal_input_classifier_hwservice:s0 |
| android.hardware.ir::IConsumerIr | u:object_r:hal_ir_hwservice:s0 |
| android.hardware.keymaster::IKeymasterDevice | u:object_r:hal_keymaster_hwservice:s0 |
| android.hardware.light::ILight | u:object_r:hal_light_hwservice:s0 |
| android.hardware.lowpan::ILowpanDevice | u:object_r:hal_lowpan_hwservice:s0 |
| android.hardware.media.omx::IOmx | u:object_r:hal_omx_hwservice:s0 |
| android.hardware.media.omx::IOmxStore | u:object_r:hal_omx_hwservice:s0 |
| android.hardware.media.c2::IComponentStore | u:object_r:hal_codec2_hwservice:s0 |
| android.hardware.memtrack::IMemtrack | u:object_r:hal_memtrack_hwservice:s0 |
| android.hardware.neuralnetworks::IDevice | u:object_r:hal_neuralnetworks_hwservice:s0 |
| android.hardware.nfc::INfc | u:object_r:hal_nfc_hwservice:s0 |
| android.hardware.oemlock::IOemLock | u:object_r:hal_oemlock_hwservice:s0 |
| android.hardware.power::IPower | u:object_r:hal_power_hwservice:s0 |
| android.hardware.power.stats::IPowerStats | u:object_r:hal_power_stats_hwservice:s0 |
| android.hardware.radio.config::IRadioConfig | u:object_r:hal_telephony_hwservice:s0 |
| android.hardware.radio.deprecated::IOemHook | u:object_r:hal_telephony_hwservice:s0 |
| android.hardware.radio::IRadio | u:object_r:hal_telephony_hwservice:s0 |
| android.hardware.radio::ISap | u:object_r:hal_telephony_hwservice:s0 |
| android.hardware.renderscript::IDevice | u:object_r:hal_renderscript_hwservice:s0 |
| android.hardware.secure_element::ISecureElement | u:object_r:hal_secure_element_hwservice:s0 |
| android.hardware.sensors::ISensors | u:object_r:hal_sensors_hwservice:s0 |
| android.hardware.soundtrigger::ISoundTriggerHw | u:object_r:hal_audio_hwservice:s0 |
| android.hardware.tetheroffload.config::IOffloadConfig | u:object_r:hal_tetheroffload_hwservice:s0 |
| android.hardware.tetheroffload.control::IOffloadControl | u:object_r:hal_tetheroffload_hwservice:s0 |
| android.hardware.thermal::IThermal | u:object_r:hal_thermal_hwservice:s0 |
| android.hardware.thermal::IThermalCallback | u:object_r:thermalcallback_hwservice:s0 |
| android.hardware.tv.cec::IHdmiCec | u:object_r:hal_tv_cec_hwservice:s0 |
| android.hardware.tv.input::ITvInput | u:object_r:hal_tv_input_hwservice:s0 |
| android.hardware.usb::IUsb | u:object_r:hal_usb_hwservice:s0 |
| android.hardware.usb.gadget::IUsbGadget | u:object_r:hal_usb_gadget_hwservice:s0 |
| android.hardware.vibrator::IVibrator | u:object_r:hal_vibrator_hwservice:s0 |
| android.hardware.vr::IVr | u:object_r:hal_vr_hwservice:s0 |
| android.hardware.weaver::IWeaver | u:object_r:hal_weaver_hwservice:s0 |
| android.hardware.wifi::IWifi | u:object_r:hal_wifi_hwservice:s0 |
| android.hardware.wifi.hostapd::IHostapd | u:object_r:hal_wifi_hostapd_hwservice:s0 |
| android.hardware.wifi.offload::IOffload | u:object_r:hal_wifi_offload_hwservice:s0 |
| android.hardware.wifi.supplicant::ISupplicant | u:object_r:hal_wifi_supplicant_hwservice:s0 |
| android.hidl.allocator::IAllocator | u:object_r:hidl_allocator_hwservice:s0 |
| android.hidl.base::IBase | u:object_r:hidl_base_hwservice:s0 |
| android.hidl.manager::IServiceManager | u:object_r:hidl_manager_hwservice:s0 |
| android.hidl.memory::IMapper | u:object_r:hidl_memory_hwservice:s0 |
| android.hidl.token::ITokenManager | u:object_r:hidl_token_hwservice:s0 |
| android.system.net.netd::INetd | u:object_r:system_net_netd_hwservice:s0 |
| android.system.suspend::ISystemSuspend | u:object_r:system_suspend_hwservice:s0 |
| android.system.wifi.keystore::IKeystore | u:object_r:system_wifi_keystore_hwservice:s0 |
| * | u:object_r:default_android_hwservice:s0 |

################################################################################
# 2.15 — SELINUX BINDER SERVICE CONTEXT AUTHORITY
################################################################################

Present Binder service-context authority:
- `/system/etc/selinux/plat_service_contexts` [VERIFIED]
- `/vendor/etc/selinux/vndservice_contexts` [VERIFIED]

Absent searched legacy/product/odm roots in targeted live capture:
- `/vendor/etc/selinux/vendor_service_contexts` [ABSENT_NAMESPACE_ROOT]
- `/system_ext/etc/selinux/system_ext_service_contexts` [ABSENT_NAMESPACE_ROOT]
- `/product/etc/selinux/product_service_contexts` [ABSENT_NAMESPACE_ROOT]
- `/odm/etc/selinux/odm_service_contexts` [ABSENT_NAMESPACE_ROOT]

Important naming rule:
The vendor service context file present in the source pack is `vndservice_contexts`.
The searched `vendor_service_contexts` path is absent. Do not collapse these names.

Vendor vndservice contexts:
| Service key | SELinux object context |
|---|---|
| * | u:object_r:default_android_vndservice:s0 |
| android.IRemosaicDaemon | u:object_r:remosaic_daemon_service:s0 |

High-value framework Binder service contexts:
| Service key | SELinux object context |
|---|---|
| android.security.keystore | u:object_r:keystore_service:s0 |
| android.service.gatekeeper.IGateKeeperService | u:object_r:gatekeeper_service:s0 |
| bluetooth_manager | u:object_r:bluetooth_manager_service:s0 |
| bluetooth | u:object_r:bluetooth_service:s0 |
| broadcastradio | u:object_r:broadcastradio_service:s0 |
| carrier_config | u:object_r:radio_service:s0 |
| netd_listener | u:object_r:netd_listener_service:s0 |
| drm.drmManager | u:object_r:drmserver_service:s0 |
| econtroller | u:object_r:radio_service:s0 |
| euicc_card_controller | u:object_r:radio_service:s0 |
| gpu | u:object_r:gpu_service:s0 |
| ions | u:object_r:radio_service:s0 |
| iphonesubinfo_msim | u:object_r:radio_service:s0 |
| iphonesubinfo2 | u:object_r:radio_service:s0 |
| iphonesubinfo | u:object_r:radio_service:s0 |
| ims | u:object_r:radio_service:s0 |
| imms | u:object_r:imms_service:s0 |
| ircs | u:object_r:radio_service:s0 |
| isms_msim | u:object_r:radio_service:s0 |
| isms2 | u:object_r:radio_service:s0 |
| isms | u:object_r:radio_service:s0 |
| isub | u:object_r:radio_service:s0 |
| location | u:object_r:location_service:s0 |
| media.aaudio | u:object_r:audioserver_service:s0 |
| media.audio_flinger | u:object_r:audioserver_service:s0 |
| media.audio_policy | u:object_r:audioserver_service:s0 |
| media.camera | u:object_r:cameraserver_service:s0 |
| media.camera.proxy | u:object_r:cameraproxy_service:s0 |
| media.log | u:object_r:audioserver_service:s0 |
| media.player | u:object_r:mediaserver_service:s0 |
| media.metrics | u:object_r:mediametrics_service:s0 |
| media.extractor | u:object_r:mediaextractor_service:s0 |
| media.codec | u:object_r:mediacodec_service:s0 |
| media.resource_manager | u:object_r:mediaserver_service:s0 |
| media.sound_trigger_hw | u:object_r:audioserver_service:s0 |
| media.drm | u:object_r:mediadrmserver_service:s0 |
| media_projection | u:object_r:media_projection_service:s0 |
| media_resource_monitor | u:object_r:media_session_service:s0 |
| media_router | u:object_r:media_router_service:s0 |
| media_session | u:object_r:media_session_service:s0 |
| netd | u:object_r:netd_service:s0 |
| otadexopt | u:object_r:otadexopt_service:s0 |
| phone_msim | u:object_r:radio_service:s0 |
| phone1 | u:object_r:radio_service:s0 |
| phone2 | u:object_r:radio_service:s0 |
| phone | u:object_r:radio_service:s0 |
| power | u:object_r:power_service:s0 |
| radio.phonesubinfo | u:object_r:radio_service:s0 |
| radio.phone | u:object_r:radio_service:s0 |
| radio.sms | u:object_r:radio_service:s0 |
| rcs | u:object_r:radio_service:s0 |
| secure_element | u:object_r:secure_element_service:s0 |
| sensorservice | u:object_r:sensorservice_service:s0 |
| sensor_privacy | u:object_r:sensor_privacy_service:s0 |
| simphonebook_msim | u:object_r:radio_service:s0 |
| simphonebook2 | u:object_r:radio_service:s0 |
| simphonebook | u:object_r:radio_service:s0 |
| sip | u:object_r:radio_service:s0 |
| statusbar | u:object_r:statusbar_service:s0 |
| telephony.registry | u:object_r:registry_service:s0 |
| usb | u:object_r:usb_service:s0 |
| wifip2p | u:object_r:wifip2p_service:s0 |
| wifiscanner | u:object_r:wifiscanner_service:s0 |
| wifi | u:object_r:wifi_service:s0 |
| wificond | u:object_r:wificond_service:s0 |
| wifiaware | u:object_r:wifiaware_service:s0 |
| wifirtt | u:object_r:rttmanager_service:s0 |
| memory_dumper | u:object_r:mediaserver_service:s0 |
| imsa | u:object_r:radio_service:s0 |
| mtkIms | u:object_r:radio_service:s0 |
| GbaService | u:object_r:radio_service:s0 |
| media.mmsdk | u:object_r:mtk_advcamserver_service:s0 |
| media.advcam | u:object_r:mtk_advcamserver_service:s0 |
| power_hal_mgr_service | u:object_r:mtk_power_hal_mgr_service:s0 |
| phoneEx | u:object_r:mtk_radio_service:s0 |
| telephony.mtkregistry | u:object_r:mtk_registry_service:s0 |
| iphonesubinfoEx | u:object_r:mtk_phonesubinfo_service:s0 |
| mtksimphonebook | u:object_r:mtk_simphonebook_service:s0 |
| fm_radio_service | u:object_r:mtk_fm_radio_service:s0 |
| capctrl | u:object_r:mtk_radio_service:s0 |
| GoogleOtaBinder | u:object_r:ota_agent_service:s0 |
| GpuAppSpectatorService | u:object_r:gas_srv_service:s0 |
| isubstub | u:object_r:radio_service:s0 |
| wfo | u:object_r:radio_service:s0 |
| imtksms | u:object_r:radio_service:s0 |
| media.VTS | u:object_r:vtservice_service:s0 |
| media.VTS.HiDL | u:object_r:vtservice_hidl_service:s0 |
| mwis | u:object_r:radio_service:s0 |
| mediatek.campostalgo | u:object_r:camerapostalgo_service:s0 |

################################################################################
# 2.16 — HIGH-VALUE LIVE BINDER SERVICE LIST
################################################################################

| Index | Binder service | Interface |
|---|---|---|
| 0 | capctrl | com.mediatek.capctrl.aidl.IMtkCapCtrl |
| 2 | mtkIms | com.mediatek.ims.internal.IMtkImsService |
| 3 | mwis | com.mediatek.wfo.IMwiService |
| 4 | secure_element | android.se.omapi.ISecureElementService |
| 5 | carrier_config | com.android.internal.telephony.ICarrierConfigLoader |
| 6 | phoneEx | com.mediatek.internal.telephony.IMtkTelephonyEx |
| 7 | phone | com.android.internal.telephony.ITelephony |
| 8 | imtksms | com.mediatek.internal.telephony.IMtkSms |
| 9 | iphonesubinfoEx | com.mediatek.internal.telephony.IMtkPhoneSubInfoEx |
| 10 | mtksimphonebook | com.mediatek.internal.telephony.phb.IMtkIccPhoneBook |
| 11 | isms | com.android.internal.telephony.ISms |
| 12 | iphonesubinfo | com.android.internal.telephony.IPhoneSubInfo |
| 13 | simphonebook | com.android.internal.telephony.IIccPhoneBook |
| 14 | ircs | android.telephony.ims.aidl.IRcs |
| 16 | isubstub | com.mediatek.internal.telephony.IMtkSub |
| 18 | isub | com.android.internal.telephony.ISub |
| 19 | telephony.mtkregistry | com.mediatek.internal.telephony.IMtkTelephonyRegistryEx |
| 20 | mtk_telecom | com.mediatek.internal.telecom.IMtkTelecomService |
| 21 | contexthub | android.hardware.location.IContextHubService |
| 22 | mtkconnmetrics | com.mediatek.net.connectivity.IMtkIpConnectivityMetrics |
| 23 | netd_listener | android.net.metrics.INetdEventListener |
| 25 | bluetooth_manager | android.bluetooth.IBluetoothManager |
| 26 | power_hal_mgr_service | com.mediatek.powerhalmgr.IPowerHalMgr |
| 27 | search_engine_service | com.mediatek.search.ISearchEngineManagerService |
| 31 | imms | com.android.internal.telephony.IMms |
| 34 | media.camera.proxy | android.hardware.ICameraServiceProxy |
| 36 | media_projection | android.media.projection.IMediaProjectionManager |
| 40 | media_router | android.media.IMediaRouterService |
| 41 | media_resource_monitor | android.media.IMediaResourceMonitor |
| 42 | media_session | android.media.session.ISessionManager |
| 61 | usb | android.hardware.usb.IUsbManager |
| 63 | midi | android.media.midi.IMidiManager |
| 65 | audio | android.media.IAudioService |
| 69 | country_detector | android.location.ICountryDetector |
| 70 | location | android.location.ILocationManager |
| 78 | wifip2p | android.net.wifi.p2p.IWifiP2pManager |
| 79 | wifiscanner | android.net.wifi.IWifiScanner |
| 80 | wifi | android.net.wifi.IWifiManager |
| 91 | statusbar | com.android.internal.statusbar.IStatusBarService |
| 116 | telephony.registry | com.android.internal.telephony.ITelephonyRegistry |
| 124 | sensorservice | android.gui.SensorServer |
| 126 | media.sound_trigger_hw | android.hardware.ISoundTriggerHwService |
| 128 | media.audio_policy | android.media.IAudioPolicyService |
| 130 | media.camera | android.hardware.ICameraService |
| 131 | sensor_privacy | android.hardware.ISensorPrivacyManager |
| 143 | otadexopt | android.content.pm.IOtaDexopt |
| 149 | power | android.os.IPowerManager |
| 155 | media.VTS | android.hardware.IVTService |
| 156 | netd | android.net.INetd |
| 158 | media.extractor | android.media.IMediaExtractorService |
| 159 | media.resource_manager | android.media.IResourceManagerService |
| 160 | media.player | android.media.IMediaPlayerService |
| 162 | SurfaceFlinger | android.ui.ISurfaceComposer |
| 164 | drm.drmManager | drm.IDrmManagerService |
| 165 | media.metrics | android.media.IMediaAnalyticsService |
| 166 | android.service.gatekeeper.IGateKeeperService | android.service.gatekeeper.IGateKeeperService |
| 169 | android.security.keystore | android.security.keystore.IKeystoreService |
| 170 | wificond | android.net.wifi.IWificond |
| 171 | media.drm | android.media.IMediaDrmService |
| 174 | media.audio_flinger | android.media.IAudioFlinger |
| 175 | gpu | android.graphicsenv.IGpuService |
| 176 | ashmem_device_service | android.ashmemd.IAshmemDeviceService |
| 178 | suspend_control | android.system.suspend.ISuspendControlService |
Binder interpretation:
- Binder services `secure_element`, `phone`, `phoneEx`, `mtkIms`, `imtksms`, `capctrl`, `wfo`, `media.VTS`,
  camera/media services, keystore, gatekeeper, and power services form the framework-facing side of the HAL/control plane.
- This table records service presence. It does not prove endpoint traffic or payload content.


################################################################################
# 2.17 — LIVE HAL / DAEMON BINARY IDENTITY TABLE
################################################################################

The following table is populated from the targeted live binary hash inventory. It is the canonical
Section 02 identity table for live HAL and HAL-adjacent daemon executables. It records the exact
path, SELinux exec label, size, and SHA256 used in this capture.

| Path | SELinux exec label | Bytes | SHA256 |
|---|---|---|---|
| /vendor/bin/hw/android.hardware.audio@5.0-service-mediatek | u:object_r:mtk_hal_audio_exec:s0 | 15472 | 4b9217f439be7f77d6887d84a008de749da96e64a5ba8ce71f033a655d244da2 |
| /vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek | u:object_r:mtk_hal_bluetooth_exec:s0 | 11512 | b2282f717a77a07ce772753a6e72e2dc0c7f04d557e721ed3f5589a7b9725de4 |
| /vendor/bin/hw/android.hardware.cas@1.1-service | u:object_r:hal_cas_default_exec:s0 | 39696 | 2a71aa05330efe843583c4f1069680356b5516eb0b93ab923e480e65a746cdc9 |
| /vendor/bin/hw/android.hardware.configstore@1.1-service | u:object_r:hal_configstore_default_exec:s0 | 21848 | fbe1c2ebe48cfb36250cf4ac0a911384a54dd75308c00839d77d7f383026b46a |
| /vendor/bin/hw/android.hardware.drm@1.0-service | u:object_r:hal_drm_default_exec:s0 | 11120 | ae17cace2c9482931740abe88db93d1217fb456c244c8f1c1e955e2b643f2b8b |
| /vendor/bin/hw/android.hardware.drm@1.2-service.clearkey | u:object_r:hal_drm_clearkey_exec:s0 | 152336 | b8aca481ed3ab1d6723fb20ffeff9ddaffdd30ef8a974cecd012119d2826ade3 |
| /vendor/bin/hw/android.hardware.drm@1.2-service.widevine | u:object_r:hal_drm_widevine_exec:s0 | 24604 | 36aa79155c6f737d12d1380ce3c89fbbedc5efca61fc03c9ac128c3d391f9abd |
| /vendor/bin/hw/android.hardware.gatekeeper@1.0-service | u:object_r:hal_gatekeeper_default_exec:s0 | 11600 | 53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a |
| /vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek | u:object_r:mtk_hal_gnss_exec:s0 | 11536 | 63a251a035a9544e71b87f9bb0b1ec3bec571bcfb4f5d426d0005286282efccc |
| /vendor/bin/hw/android.hardware.graphics.allocator@2.0-service | u:object_r:hal_graphics_allocator_default_exec:s0 | 11584 | de14c988341b1bd421ad97191b9d45ec176bcd468feb42704e72f9b97fae774c |
| /vendor/bin/hw/android.hardware.graphics.composer@2.1-service | u:object_r:hal_graphics_composer_default_exec:s0 | 11672 | 9a718498f8f8effc1bbcca7c1c9b92a2b6d0ea22b4eb7529c8b54e33fab28b89 |
| /vendor/bin/hw/android.hardware.health@2.0-service | u:object_r:hal_health_default_exec:s0 | 70264 | 99765c43349b504decae3f4cab27184c82657b50a5ebd4a2dd72e77088e4b6e0 |
| /vendor/bin/hw/android.hardware.keymaster@4.0-service | u:object_r:hal_keymaster_default_exec:s0 | 11848 | 712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8 |
| /vendor/bin/hw/android.hardware.light@2.0-service-mediatek | u:object_r:mtk_hal_light_exec:s0 | 11544 | 2e4dc2555a9e8b32103a6f4f0a182acc8230122f95e2e34517bc585df23b080b |
| /vendor/bin/hw/android.hardware.media.omx@1.0-service | u:object_r:mediacodec_exec:s0 | 15620 | 41dbae862102f96f56d717b795b2a1add40021dff828f4b40adf1ba1f07301c7 |
| /vendor/bin/hw/android.hardware.memtrack@1.0-service | u:object_r:hal_memtrack_default_exec:s0 | 11616 | 74848d5eb054097ba61d59abe7841d37959d6136d6c4c27d375040a7efc5d336 |
| /vendor/bin/hw/android.hardware.secure_element@1.0-service-mediatek | u:object_r:mtk_hal_secure_element_exec:s0 | 74544 | 087af7354a27e3763ccf7010e204d186211c5f6c25de76227e985fd27dbfd5da |
| /vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek | u:object_r:mtk_hal_sensors_exec:s0 | 73176 | 57b0a2f704fe387619d20c6754237e79eecd21c160dd6e89614e47897b5021cb |
| /vendor/bin/hw/android.hardware.thermal@1.0-service | u:object_r:hal_thermal_default_exec:s0 | 11632 | 2ac62bea7c3ea008ba2a7d186e3cdd8c605f341e268e65bec3b23a0522f24f52 |
| /vendor/bin/hw/android.hardware.usb@1.1-service-mediatek | u:object_r:mtk_hal_usb_exec:s0 | 38832 | aba123a38640d4aaf6a11f1e96cb8ab1c84d81bdaf9e0f11cc2322adf0520040 |
| /vendor/bin/hw/android.hardware.vibrator@1.0-service | u:object_r:hal_vibrator_default_exec:s0 | 11608 | 163aa4adb03819c8d81eb8892a6f1ed1a3b88b9144a975e97cfb457451735b5c |
| /vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek | u:object_r:mtk_hal_wifi_exec:s0 | 427424 | e3cebe504789d4a83b10b7d4b015b70f27fa05543d6eb21e11cc979bdae95e6d |
| /vendor/bin/hw/camerahalserver | u:object_r:mtk_hal_camera_exec:s0 | 15880 | cce134823576b072c62984fb42c2e216e60f1bb0ae00408625285f5c1808a3a9 |
| /vendor/bin/hw/mtkfusionrild | u:object_r:rild_exec:s0 | 20168 | de07d5b07985746f475e02e048d5423b0138f4b44e5a27bf0b89521a9204df04 |
| /vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service | u:object_r:mtk_hal_gpu_exec:s0 | 29976 | 6dd788d13979d080066ea915342579b187f2df4e637e7c8d3d7509aea4b4afe8 |
| /vendor/bin/hw/vendor.mediatek.hardware.mms@1.3-service | u:object_r:mtk_hal_mms_exec:s0 | 37448 | 82a8329b7dff23a1463a77767f0242951d47529443832d9bdcdbae149df004b9 |
| /vendor/bin/hw/vendor.mediatek.hardware.mtkcodecservice@1.1-service | u:object_r:hal_mtkcodecservice_default_exec:s0 | 11616 | 158fac72bf3971427daa5ecd5e0474b3404e69be41006d35c7a9077d8c86f6c3 |
| /vendor/bin/hw/vendor.mediatek.hardware.mtkpower@1.0-service | u:object_r:mtk_hal_power_exec:s0 | 37240 | 6d1d9766cd41152cc6a0cf10729e5519a50523387931b6585b28d119f329aed8 |
| /vendor/bin/hw/vendor.mediatek.hardware.nvram@1.1-service | u:object_r:nvram_agent_binder_exec:s0 | 37488 | b72901064a945bae46af316659c9d4038bf1bd255437fb4cc5dbfcd468136815 |
| /vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service | u:object_r:mtk_hal_pq_exec:s0 | 37448 | 859928c744e3eec38623b2024a960bef3faa27a9ab7842d9b2a12c82c053c0b1 |
| /vendor/bin/hw/vtservice_hidl | u:object_r:vtservice_hidl_exec:s0 | 11120 | e815435af1dbaf0af9ce0e9a2eda39965faa2e9b8c5e69b34bd8fc3614f916f6 |
| /vendor/bin/epdg_wod | u:object_r:epdg_wod_exec:s0 | 137200 | 4b471aa9c72132d600c0d135b7fee0da4795c26e67c0edad64fc8904f1821d16 |
| /vendor/bin/ipsec_mon | u:object_r:ipsec_mon_exec:s0 | 50264 | 20e9049844291dea861052a8ed755aac74393a750c3676a8014b45fd51a7ec40 |
| /vendor/bin/lbs_hidl_service | u:object_r:lbs_hidl_service_exec:s0 | 11384 | 131d4e81d1ef644600b7e2491481a990e8369be81b54fc6cd63e3a704ddfb87e |
| /vendor/bin/mnld | u:object_r:mnld_exec:s0 | 262736 | 5937bbf5d16807217e98f37a9a6c593d536cfbf36066035258de539f6011374c |
| /vendor/bin/netdagent | u:object_r:netdagent_exec:s0 | 56048 | 401b4ddb824642185e6bd2196c96658c5ddaadffd4f7bcb5f4cba70f3a3792da |
| /vendor/bin/volte_imcb | u:object_r:volte_imcb_exec:s0 | 680412 | dc09ac7eb6b8ebdc564b18bbaf8a211d19f2ac3fb0d1aa6b7c66aa99c7eb5708 |
| /vendor/bin/volte_imsm_93 | u:object_r:volte_imsm_93_exec:s0 | 165440 | ed86775e6a67580f6bfc3c22353d8a99e916bc456548df49de42cc6911ff4b94 |
| /vendor/bin/volte_md_status | u:object_r:volte_md_status_exec:s0 | 18120 | 640ce5f28e2ca9a0df2af54fd07f095f078855a07184feebf1dea29a8d6f0dc9 |
| /vendor/bin/volte_stack | u:object_r:volte_stack_exec:s0 | 755376 | d38ccfc5a59984183ce85b801e9d5e0479b293ac0128ea5826d03b8c045315ab |
| /vendor/bin/volte_ua | u:object_r:volte_ua_exec:s0 | 1525552 | b1c00d8dea0f77a3ae884019f0f314e12405d40f62dab6a23f36b9f7a4bb31b0 |
| /vendor/bin/wfca | u:object_r:wfca_exec:s0 | 71232 | 0aaef1c50d6d6d78cc394f40a97d5efe2424a4674bb8e17a192e8329088c7c1b |
| /vendor/bin/wmt_launcher | u:object_r:mtk_wmt_launcher_exec:s0 | 37104 | 70b5224af4276eef4a27a405919e5a147b24739569b3a24f5ee1cb8d3cd9a2a7 |
| /system/bin/vtservice | u:object_r:vtservice_exec:s0 | 11060 | 2806ca092db758520e2a7842efc8597f4b1ca040dd7b8a28966e9d31cede595d |

################################################################################
# 2.18 — SOURCE-PACK BIN/HW BINARY INVENTORY
################################################################################

| File | Bytes | SHA256 |
|---|---|---|
| vendor/bin_hw/android.hardware.audio@5.0-service-mediatek | 15472 | 4b9217f439be7f77d6887d84a008de749da96e64a5ba8ce71f033a655d244da2 |
| vendor/bin_hw/android.hardware.bluetooth@1.0-service-mediatek | 11512 | b2282f717a77a07ce772753a6e72e2dc0c7f04d557e721ed3f5589a7b9725de4 |
| vendor/bin_hw/android.hardware.cas@1.1-service | 39696 | 2a71aa05330efe843583c4f1069680356b5516eb0b93ab923e480e65a746cdc9 |
| vendor/bin_hw/android.hardware.configstore@1.1-service | 21848 | fbe1c2ebe48cfb36250cf4ac0a911384a54dd75308c00839d77d7f383026b46a |
| vendor/bin_hw/android.hardware.drm@1.0-service | 11120 | ae17cace2c9482931740abe88db93d1217fb456c244c8f1c1e955e2b643f2b8b |
| vendor/bin_hw/android.hardware.drm@1.2-service.clearkey | 152336 | b8aca481ed3ab1d6723fb20ffeff9ddaffdd30ef8a974cecd012119d2826ade3 |
| vendor/bin_hw/android.hardware.drm@1.2-service.widevine | 24604 | 36aa79155c6f737d12d1380ce3c89fbbedc5efca61fc03c9ac128c3d391f9abd |
| vendor/bin_hw/android.hardware.gatekeeper@1.0-service | 11600 | 53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a |
| vendor/bin_hw/android.hardware.gnss@2.0-service-mediatek | 11536 | 63a251a035a9544e71b87f9bb0b1ec3bec571bcfb4f5d426d0005286282efccc |
| vendor/bin_hw/android.hardware.graphics.allocator@2.0-service | 11584 | de14c988341b1bd421ad97191b9d45ec176bcd468feb42704e72f9b97fae774c |
| vendor/bin_hw/android.hardware.graphics.composer@2.1-service | 11672 | 9a718498f8f8effc1bbcca7c1c9b92a2b6d0ea22b4eb7529c8b54e33fab28b89 |
| vendor/bin_hw/android.hardware.health@2.0-service | 70264 | 99765c43349b504decae3f4cab27184c82657b50a5ebd4a2dd72e77088e4b6e0 |
| vendor/bin_hw/android.hardware.keymaster@4.0-service | 11848 | 712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8 |
| vendor/bin_hw/android.hardware.light@2.0-service-mediatek | 11544 | 2e4dc2555a9e8b32103a6f4f0a182acc8230122f95e2e34517bc585df23b080b |
| vendor/bin_hw/android.hardware.media.omx@1.0-service | 15620 | 41dbae862102f96f56d717b795b2a1add40021dff828f4b40adf1ba1f07301c7 |
| vendor/bin_hw/android.hardware.memtrack@1.0-service | 11616 | 74848d5eb054097ba61d59abe7841d37959d6136d6c4c27d375040a7efc5d336 |
| vendor/bin_hw/android.hardware.secure_element@1.0-service-mediatek | 74544 | 087af7354a27e3763ccf7010e204d186211c5f6c25de76227e985fd27dbfd5da |
| vendor/bin_hw/android.hardware.sensors@2.0-service-mediatek | 73176 | 57b0a2f704fe387619d20c6754237e79eecd21c160dd6e89614e47897b5021cb |
| vendor/bin_hw/android.hardware.thermal@1.0-service | 11632 | 2ac62bea7c3ea008ba2a7d186e3cdd8c605f341e268e65bec3b23a0522f24f52 |
| vendor/bin_hw/android.hardware.usb@1.1-service-mediatek | 38832 | aba123a38640d4aaf6a11f1e96cb8ab1c84d81bdaf9e0f11cc2322adf0520040 |
| vendor/bin_hw/android.hardware.vibrator@1.0-service | 11608 | 163aa4adb03819c8d81eb8892a6f1ed1a3b88b9144a975e97cfb457451735b5c |
| vendor/bin_hw/android.hardware.wifi@1.0-service-lazy-mediatek | 427424 | e3cebe504789d4a83b10b7d4b015b70f27fa05543d6eb21e11cc979bdae95e6d |
| vendor/bin_hw/camerahalserver | 15880 | cce134823576b072c62984fb42c2e216e60f1bb0ae00408625285f5c1808a3a9 |
| vendor/bin_hw/hostapd | 940776 | 75195bdd43ee86aa304ad909a5097e00fde3e8b520e18bb416c41b140a257923 |
| vendor/bin_hw/mtkfusionrild | 20168 | de07d5b07985746f475e02e048d5423b0138f4b44e5a27bf0b89521a9204df04 |
| vendor/bin_hw/power_legacy_test_v_1_0 | 11800 | 8cd6c7f30968d504bc22722d1e5ea7f673e811fb54caf5834553bdf18c1abf98 |
| vendor/bin_hw/power_native_test_v_1_0 | 20192 | c0f8f8279f6161c4cc21d73a38ac27111c27d24e10b84b682f15be5f425c8106 |
| vendor/bin_hw/vendor.mediatek.hardware.gpu@1.0-service | 29976 | 6dd788d13979d080066ea915342579b187f2df4e637e7c8d3d7509aea4b4afe8 |
| vendor/bin_hw/vendor.mediatek.hardware.mms@1.3-service | 37448 | 82a8329b7dff23a1463a77767f0242951d47529443832d9bdcdbae149df004b9 |
| vendor/bin_hw/vendor.mediatek.hardware.mtkcodecservice@1.1-service | 11616 | 158fac72bf3971427daa5ecd5e0474b3404e69be41006d35c7a9077d8c86f6c3 |
| vendor/bin_hw/vendor.mediatek.hardware.mtkpower@1.0-service | 37240 | 6d1d9766cd41152cc6a0cf10729e5519a50523387931b6585b28d119f329aed8 |
| vendor/bin_hw/vendor.mediatek.hardware.nvram@1.1-service | 37488 | b72901064a945bae46af316659c9d4038bf1bd255437fb4cc5dbfcd468136815 |
| vendor/bin_hw/vendor.mediatek.hardware.pq@2.2-service | 37448 | 859928c744e3eec38623b2024a960bef3faa27a9ab7842d9b2a12c82c053c0b1 |
| vendor/bin_hw/vtservice_hidl | 11120 | e815435af1dbaf0af9ce0e9a2eda39965faa2e9b8c5e69b34bd8fc3614f916f6 |
| vendor/bin_hw/wpa_supplicant | 2594800 | 8193f9efb926f05a42e74480947bb886cfc352b4f4ee76a2829215a42bc0d576 |
| system/bin_hw/android.hidl.allocator@1.0-service | 25608 | f6bc202215ce3a8d959cdbc21f89bcc66469f3d0dca3d73373d04ab882cb749f |
| system/bin_hw/android.system.suspend@1.0-service | 107744 | ad55285849cd1d5b9f7e8ea2f73310ea58b25274664180ae505be176a09672d3 |
Interpretation:
- Source-pack `bin_hw` hashes are file-source authority.
- Live binary identity table above is runtime-path authority with SELinux labels.
- Where live hash and source-pack hash differ for the same named binary, the live path is the authority for current runtime and the source-pack entry remains file-source evidence for the captured pack. This section does not silently merge them.


################################################################################
# 2.19 — HAL IMPLEMENTATION LIBRARY INVENTORY
################################################################################

This table records the captured `/lib/hw` and `/lib64/hw` implementation layer. Complete dependency closure and VNDK namespace isolation belong to Section 10; this table exists here because these implementation files are the passthrough/HIDL implementation boundary for Section 02.

| File | Bytes | SHA256 |
|---|---|---|
| vendor/lib_hw/android.hardware.audio.effect@5.0-impl.so | 219092 | 0cb7bcf05df73b932784d040f64cbc8db56eaaf628032f0f8e2b4c0cc93e29bd |
| vendor/lib_hw/android.hardware.audio@5.0-impl-mediatek.so | 184140 | 1d834c01e77e874686cc97122c709b609216e1f44ae8a6f23df0e5331538ea93 |
| vendor/lib_hw/android.hardware.bluetooth.audio@2.0-impl-mediatek.so | 77992 | a93ca4220d2b49858064a12110f958b86414324b7270ad58cb0ce33f8beff1fb |
| vendor/lib_hw/android.hardware.bluetooth@1.0-impl-mediatek.so | 62152 | 156495376fbb0b26edb922a712fcda742d979ede0d106bddf4ae966b2f7b3b9a |
| vendor/lib_hw/android.hardware.drm@1.0-impl.so | 74452 | 5c0750704fee32542a46759c65c34fc7ea822260578e153e0ae1525f8084694c |
| vendor/lib_hw/android.hardware.gatekeeper@1.0-impl.so | 19956 | a35914484d83cbd71e78360dd884a3451471f959bf4d98b08f4fa9e3ba24d17c |
| vendor/lib_hw/android.hardware.graphics.allocator@2.0-impl.so | 24580 | e08fd77637989d8b2df4c602a204cced8861832ac4832419f3e89cc07c6e2e7b |
| vendor/lib_hw/android.hardware.graphics.composer@2.1-impl.so | 79400 | 8f4221d1c37d21d755629564e6b1ffedd5d4fa93b5c2836a583c8da89de8c999 |
| vendor/lib_hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 29980 | 79dba7c0136c21a627cfd0c92fe9e247b0694c791d62e47ee7124fdb00b87b9c |
| vendor/lib_hw/android.hardware.light@2.0-impl-mediatek.so | 33620 | 3e4d94319a86b5663d3aaef3e165c57521b5dd91c319ac1195a3bcab81ee743b |
| vendor/lib_hw/android.hardware.memtrack@1.0-impl.so | 19900 | 18c2d991747f0d256b557fa47145cb8fa92875a79e4aed1eab4ad1066d3bdcbb |
| vendor/lib_hw/android.hardware.power@1.3-impl-mediatek.so | 51116 | 051c4c3129875c5e6da51248f6aca86d9e6e2606a022bdf017b0bd0aacee304e |
| vendor/lib_hw/android.hardware.thermal@1.0-impl.so | 24340 | 9ffc3e385ee436f867a75a41deb7c66f1284ca2b1fe0c7265cea7f074e35c645 |
| vendor/lib_hw/android.hardware.vibrator@1.0-impl.so | 19964 | b9c5554b9bc95af674d81b8a503b52b4235c8220105f5479293dc53680e43f12 |
| vendor/lib_hw/audio.bluetooth.default.so | 71584 | 3ae20675ed7cdbd54fa28875d3ac2c8bad46bf160a5ec5fb859727c9767a0d6c |
| vendor/lib_hw/audio.primary.default.so | 19820 | ab623abe82f1b1f9d8424a0172f88a0f9a780cac585f2049bbd5fcb59572fca9 |
| vendor/lib_hw/audio.primary.mt6765.so | 1948368 | d02514e529059095c1a39aa0bf0af9fe71fce44f11360f1654e4e16bfe86cc10 |
| vendor/lib_hw/audio.r_submix.default.so | 37788 | da077262c3d2efc3c2b0ab5498399c42c535e10a25774e5d7fd1d665c4607ec5 |
| vendor/lib_hw/audio.r_submix.mt6765.so | 55048 | 4e73b45a18f2812e035a383d3300eafd2fb0f81f31b24846f3eeff93038b97c1 |
| vendor/lib_hw/audio.usb.default.so | 24328 | 0a536723eca21757ed96588f1bb0f1f13619517d3789bb7363eb42a805a4726a |
| vendor/lib_hw/audio.usb.mt6765.so | 63488 | 947f0475c725f0610c87c3a720c4295c3db76eb472e371594bbd419ac08b7de3 |
| vendor/lib_hw/audio_policy.stub.so | 15416 | 758dc1a49fd20483a174decc47536a6eac2cce65533dc5c8c2064e770eef5b75 |
| vendor/lib_hw/flp.default.so | 37324 | 5ba6a477c027df413ded702d7ebec0d42e6ee1b7026b952c8625de4ac05c2fe1 |
| vendor/lib_hw/gatekeeper.default.so | 37472 | beef8d59dd10d54e8eb35941ba06e3dd5ef0268481e93c9c17eba3f1b78c5f46 |
| vendor/lib_hw/gps.default.so | 68036 | 563856bc761e4686568b13bdac7d76cd0e96ee13545321296912a44412926986 |
| vendor/lib_hw/gralloc.default.so | 28788 | bb60cb89dcff1b310035433b4712f5002cbb267c08ffacfc92b09574942bb8e3 |
| vendor/lib_hw/gralloc.mt6765.so | 552832 | b7a3ec5bc130e5c6d9fe3c24f0f9d85c61981da0c306c2c167521580ba8e90e7 |
| vendor/lib_hw/libSoftGatekeeper.so | 37472 | beef8d59dd10d54e8eb35941ba06e3dd5ef0268481e93c9c17eba3f1b78c5f46 |
| vendor/lib_hw/lights.mt6765.so | 28692 | 10a4137316161913987033898777426b1442831b909e030b72664491cac65379 |
| vendor/lib_hw/local_time.default.so | 15064 | 5a7733167fa047a09f2b60e15c0721f34ca13f4a8e804f086a6c0664fb676504 |
| vendor/lib_hw/memtrack.mt6765.so | 24252 | 8fc0e0fbd9de3a528b77a02f8a040bb8bda8d3e5a149fad995571209590188b8 |
| vendor/lib_hw/power.default.so | 15040 | 81f4aaf70472202b2971695430cafd4d7f7109995ffbb0c0320e855285d980ac |
| vendor/lib_hw/power.mt6765.so | 24208 | db35fb99aff62a184a4659e832766ad970588434c4475b0b18851aa2308fd079 |
| vendor/lib_hw/sound_trigger.primary.mt6765.so | 54400 | 580edfeb9412da6c18246763402e803f0d63608b151955ddc8358e578bf4b92c |
| vendor/lib_hw/thermal.mt6765.so | 28376 | 99c704205368cff8d5e90d6ab90c552a18e9473711798009b23baabd52ca55e4 |
| vendor/lib_hw/vendor.mediatek.hardware.camera.bgservice@1.1-impl.so | 46660 | 741b4ccc713cc11d6fe4bc1d6fa97e6b93b82cdb4542d58f820637005b4edc3b |
| vendor/lib_hw/vendor.mediatek.hardware.mms@1.3-impl.so | 50432 | d9a18b428fea9d6a1576ec4413fdd794e45ce34878a91d0505ec79496a17d3da |
| vendor/lib_hw/vendor.mediatek.hardware.mtkcodecservice@1.1-impl.so | 88080 | 9ac0e49d875686a007335ec83f52da2853180b29f48b161af15850873589c558 |
| vendor/lib_hw/vendor.mediatek.hardware.mtkpower@1.0-impl.so | 46584 | a5ef9af6b7a1c59ce62a519f998475d2b265b38a16f26c6f8fe02f6e470a2d5c |
| vendor/lib_hw/vendor.mediatek.hardware.nvram@1.1-impl.so | 37940 | fe891e988469e075cbcb8ee850f80675110da0d6dfd3639ee190a6ca0a2afd78 |
| vendor/lib_hw/vendor.mediatek.hardware.power@2.1-impl.so | 59728 | 4fceb9262c928812245c71dbf861c307a10f80c6658ebc77dcda6fe562945094 |
| vendor/lib_hw/vendor.mediatek.hardware.pq@2.3-impl.so | 131772 | 6b028a1e5a9cf765577a44e1b3ee983b026ab25f20f04596c4265e9284b7be3f |
| vendor/lib_hw/vendor.mediatek.hardware.videotelephony@1.0-impl.so | 37104 | ea51d0776bee755ef09b54dadeee6b67042a5c5ee1d32f88d90054f887bc3ba4 |
| vendor/lib_hw/vibrator.default.so | 15156 | b98754b40a066fb689b06aaa0525fa06a28355e661e1c901e151c633b5ff4ed8 |
| vendor/lib_hw/vulkan.mt6765.so | 543016 | d174bf26d577768fb1e34f2853192a4856a1752f20e623ca0a80add4bcafafd1 |
| vendor/lib64_hw/android.hardware.audio.effect@5.0-impl.so | 302176 | 5c72a265cb95b49a7fd16c2c73ac6cade9eec21e30e7f5d51cf8df944dd740ac |
| vendor/lib64_hw/android.hardware.audio@5.0-impl-mediatek.so | 236056 | f2ac9e6a33b630703d0343e4e2ad6982ea8cae2e82c4257bc760d69f408d6b78 |
| vendor/lib64_hw/android.hardware.bluetooth.audio@2.0-impl-mediatek.so | 101208 | baa7f4691301fa7bee78de2b426db67365be80e86bbdca54cd38c1b385f8da60 |
| vendor/lib64_hw/android.hardware.bluetooth@1.0-impl-mediatek.so | 79336 | 1bbebcdae592d758c0b2b6649ea4f653142cf032e821a85929a9bcd6d736ab6e |
| vendor/lib64_hw/android.hardware.camera.provider@2.4-impl-mediatek.so | 153200 | 392cb9e68d131ccc1bfbc95eedcee8cdad43d776d33bbdf2505ef479ad12891f |
| vendor/lib64_hw/android.hardware.gatekeeper@1.0-impl.so | 20600 | 82a911dcfe40e0abbb843aa824fd25db979002279b3d6410cafbc80c2e37073c |
| vendor/lib64_hw/android.hardware.gnss@2.0-impl-mediatek.so | 196792 | 890a1d8aa5d1e2d8a4f1d7e9df23c5dce2ff1e6a0586ce2600cfc7ac5a2234fb |
| vendor/lib64_hw/android.hardware.graphics.allocator@2.0-impl.so | 24904 | 8a8f18a08c0bebc96c1f936ca971dd16b06f02c0db7b4fb5bd3c8a5f4960aa65 |
| vendor/lib64_hw/android.hardware.graphics.composer@2.1-impl.so | 104224 | a3ace507225480020f0ae9e36be72f22a83714575b844f666841418fe3b0c98e |
| vendor/lib64_hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 38696 | 2d6c882b22326ed01a578f1d8545a73838c903ffb568b986665f7b30f47ed9c0 |
| vendor/lib64_hw/android.hardware.light@2.0-impl-mediatek.so | 25168 | 1b389b35db11a21af3e37d343e35e0489ed35c65454826ef317ffa0321cfdafa |
| vendor/lib64_hw/android.hardware.memtrack@1.0-impl.so | 20576 | 3c2f67fcb8bb81812b92f26cea4bb30ba025f6ce93d85b098d0eef95aca4cd95 |
| vendor/lib64_hw/android.hardware.power@1.3-impl-mediatek.so | 55032 | c8a04fc5a1905a121a450cd39f02b273bb97fe48b85a3f4fd6a39edcf5ad8143 |
| vendor/lib64_hw/android.hardware.thermal@1.0-impl.so | 29024 | 44a5d2781ad7ec42a31c3dc3462a54fe049dd0cada7af412f65d1f91283b1ad8 |
| vendor/lib64_hw/android.hardware.vibrator@1.0-impl.so | 20648 | 64583f4d34661a11ea953277af5d7bb26e0fd981551679b0fe8c22cb09509f35 |
| vendor/lib64_hw/audio.bluetooth.default.so | 92864 | c8f5e6bdc5e18a1b7c4c39394ff98ee16143e2ecc67b9b2458b29d3af59d8101 |
| vendor/lib64_hw/audio.primary.default.so | 15584 | 303b8d7f6494ee865a7ea7699348ffbc5bb0922fb78be25fdff4681ead5546ef |
| vendor/lib64_hw/audio.primary.mt6765.so | 2042304 | 2343e25a161103c2bd1c90b711461a7ee7843de4c54447eb21b086a2f0bd0bc5 |
| vendor/lib64_hw/audio.r_submix.default.so | 41960 | 98d9815f90d5d3859c8103091e9a1c4dcf2581fb27571c42e68f56599cff0bf6 |
| vendor/lib64_hw/audio.r_submix.mt6765.so | 50176 | 9892838d224a5757a93bea27d517adfedb0fd8872878d4c931e8572e7294c388 |
| vendor/lib64_hw/audio.usb.default.so | 28416 | 6a70ec052af08504a6a08ad0677b231c8f8557b931c2b6a4870bc65c3a6ca498 |
| vendor/lib64_hw/audio.usb.mt6765.so | 62968 | b6754ae89ca8bcb80378d80c06ba166ac54c142671c5e3c01454cde5faf41c7b |
| vendor/lib64_hw/audio_policy.stub.so | 15424 | 774cc0909307949816794df94848929c6a10592f2850224dee954ec753abc159 |
| vendor/lib64_hw/flp.default.so | 36352 | 97af9b26b41922b908bc483a789ee4a21a398b16bf5fab209063d4b959e7d058 |
| vendor/lib64_hw/gatekeeper.default.so | 32536 | a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a |
| vendor/lib64_hw/gps.default.so | 75192 | 6e5aef500088efac7abee8a9ff09b4e537b2945b815c59b2b3b76d32a0eb9005 |
| vendor/lib64_hw/gralloc.default.so | 23824 | d6bfcd749ea6f386e981fcc9b3c35c9bc89d2cde2da43b6aef73a896cae568aa |
| vendor/lib64_hw/gralloc.mt6765.so | 616632 | 89ec41a1a401551d88422d8c22df1ea523b2153e00fd5fc1c8a2ff1cacc60570 |
| vendor/lib64_hw/hwcomposer.mt6765.so | 682224 | 4887675a3d5f4b85496c4e677f402be62806302a928927b95f54e412c4cd7694 |
| vendor/lib64_hw/libSoftGatekeeper.so | 32536 | a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a |
| vendor/lib64_hw/lights.mt6765.so | 23936 | 15d0b588a217c085d669672fe8339c8463e2ab3d85c943ca6cbaf0b7fa2b3276 |
| vendor/lib64_hw/local_time.default.so | 15128 | c29e8e35e22fda1948a41eab01c5424aa1d4c7187696654fb4a2840ee8086919 |
| vendor/lib64_hw/memtrack.mt6765.so | 15200 | 6703bb1524964b0e6fb552aadbd4aa5ced9d8a10bdc6443322d408c9bd1f3086 |
| vendor/lib64_hw/power.default.so | 15120 | 1fd48e82c2442915405589c4b68c0321cd07dce6df4c56811817177749bb55d7 |
| vendor/lib64_hw/power.mt6765.so | 15128 | d872bf30bb1480a74c1fd539e149267be2812b5356932105f9b72c4520a38513 |
| vendor/lib64_hw/sensors.mt6765.so | 261136 | 5664b0aa4379b4fb844456513f0974d3b4c57b22e179e2ddb08673fa40008cf0 |
| vendor/lib64_hw/sound_trigger.primary.mt6765.so | 49512 | a648dfeab026c76f612b35cf6b9559ff5e62fc23f3442c393d4f52b036753aa6 |
| vendor/lib64_hw/thermal.mt6765.so | 15480 | 0e4c55f5898b818adab1ecbfa3d3e8c6cbaf66427b6863ce12377152618de406 |
| vendor/lib64_hw/vendor.mediatek.hardware.camera.bgservice@1.1-impl.so | 46992 | c7f391a766bc4c3f61032ead2c288b8403d2a7fc1d4953018631bbfa5d40eed4 |
| vendor/lib64_hw/vendor.mediatek.hardware.camera.lomoeffect@1.0-impl.so | 24736 | defae3738158d10df01a3237400308be3a27bb2632858f03fc7e14a96b43a054 |
| vendor/lib64_hw/vendor.mediatek.hardware.mms@1.3-impl.so | 46688 | a1c57cc4aa552f9c4a542920cb61960910ba75089fda5c5502facba89a14a16f |
| vendor/lib64_hw/vendor.mediatek.hardware.mtkcodecservice@1.1-impl.so | 87560 | 512d12d5689de6b72ae15035ee980d3464070877adddb6043759f2092298c6ca |
| vendor/lib64_hw/vendor.mediatek.hardware.mtkpower@1.0-impl.so | 46208 | 7c0fe1dd4b3f90e847e58181dd4a8eb5bdb790befe8dfd5aa140114e772eace1 |
| vendor/lib64_hw/vendor.mediatek.hardware.nvram@1.1-impl.so | 37896 | 4788746025b49ab97c9d91fcf72c436c18a2999069e4a67ef1e52046b1c0a6fd |
| vendor/lib64_hw/vendor.mediatek.hardware.power@2.1-impl.so | 59784 | e1a9b7d1a197a4958df8a826787463fc834978bef8245f27b0dc62963de92d0c |
| vendor/lib64_hw/vendor.mediatek.hardware.pq@2.3-impl.so | 177208 | fecfc33bf5e43a75bba53c08a2f4ca65f49081abfb427dc05710a05bb2ea0190 |
| vendor/lib64_hw/vendor.mediatek.hardware.videotelephony@1.0-impl.so | 37672 | 9e0e4c2e05119c19654abdb1e71e05ca632e8517a8893f162837ed55eb488b02 |
| vendor/lib64_hw/vibrator.default.so | 15216 | 5369783d5a275fd47a7b7a80171d6e61b2390f5a59576537060ce6d71029ea26 |
| vendor/lib64_hw/vulkan.mt6765.so | 683024 | a0aec798ba07930dae09b6092b32e05e05af8326e6edd7e8683e50caf73acbb5 |
| system/lib_hw/android.hidl.memory@1.0-impl.so | 24768 | 3a1bf4f358af787de9faa394b56833547e6963885d3200a2ce6ad4bdbe81b176 |
| system/lib_hw/audio.a2dp.default.so | 42568 | 0ae2956417865e6b6bc413a37738d8335009505dec56241d0f5b494de20c15b0 |
| system/lib64_hw/android.hidl.memory@1.0-impl.so | 25824 | 7eef920833fe6577ef4bd907f46231be94908f0d56bec3c67f8d8d7a93a91178 |
| system/lib64_hw/audio.a2dp.default.so | 55144 | c55d9b8a5265d238fae3d53bb9aa3772d003636e2ba44f795ca017642579527d |

################################################################################
# 2.20 — DOMAIN CROSS-REFERENCE AND CLEAN-ROM ACTION MATRIX
################################################################################

| Domain | Declared / registered interface | Runtime owner | Runtime state | Clean-ROM action | Boundary interpretation |
|---|---|---|---|---|---|
| Audio | android.hardware.audio@5.0; audio.effect@5.0; bluetooth.audio@2.0 | vendor.audio-hal-2-0 / audioserver | ACTIVE | KEEP then audit in Section 07 | Audio path required for speaker, mic, BT audio, voice. |
| Bluetooth | android.hardware.bluetooth@1.0; vendor.mediatek.hardware.bluetooth | bluetooth-1-0 | ACTIVE | KEEP; strip Wiite-only policy later | BT HCI active; WMT parent boundary in Section 09. |
| Camera | android.hardware.camera.provider@2.4 internal/0; MTK bgservice | camerahalserver | ACTIVE | KEEP with exact sensor set | Physical cameras GC2385/GC030A verified; BF2553L remains trap/loader dependency, not physical proof here. |
| Display/GPU | graphics allocator/composer/mapper; vendor.mediatek.hardware.gpu; PQ | gralloc/hwcomposer/gpu/pq | ACTIVE | KEEP | Display path and GPU/PQ are required; detailed display stack in Section 05. |
| DRM | drm@1.0 default; drm@1.2 clearkey/widevine | vendor.drm-* / mediadrm | ACTIVE | KEEP; record current L3 research boundary | Current capture Widevine L3 is research-state, not stock claim. |
| Gatekeeper/Keymaster | gatekeeper@1.0; keymaster@4.0 | vendor.gatekeeper-1-0 / vendor.keymaster-4-0 | ACTIVE | KEEP; security substrate review in Section 03/11 | Early HAL keymaster and gatekeeper are boot/security required. |
| GNSS/LBS | gnss@2.0; vendor.mediatek.hardware.lbs@1.0 | gnss_service / lbs_hidl_service / mnld | ACTIVE | KEEP but constrain sockets | LBS exposes many HIDL socket-like instances and GNSS/AGPS bridges. |
| Radio/RIL/IMS | radio@1.0-1.4; radio.config; mtkradioex@1.0-1.5; radio_op@2.0 | mtkfusionrild / IMS / VoLTE / WFC daemons | ACTIVE | KEEP for telephony; harden and audit | Largest HAL control surface in Section 02; payload analysis in Section 08/16. |
| Secure element | android.hardware.secure_element@1.0::ISecureElement/SIM1 | mtk_secure_element_hal_service + com.android.se | ACTIVE | KEEP only if SIM/UICC OMAPI required | SIM1 UICC secure element; not NFC proof. |
| Sensors | android.hardware.sensors@2.0 | vendor.sensors-hal-2-0 | ACTIVE | REPLACE/HARDEN with nRF bridge contract | Android Sensor HAL meets Wiite/nRF external control plane; protocol detail Section 04. |
| USB | android.hardware.usb@1.0/@1.1 | vendor.usb-hal-1-1 | ACTIVE | KEEP | MTK USB controller boundary; external interface Section 09/10 hardware cross-ref. |
| Wi-Fi | wifi@1.0-1.3; hostapd; supplicant; MTK supplicant | vendor.wifi_hal_legacy / wpa_supplicant / hostapd | ACTIVE/Lazy | KEEP | Wi-Fi active; WMT parent and firmware Section 09. |
| WMT | not a formal Android HAL; WMT daemon boundary | wmt_launcher | ACTIVE | KEEP | Connsys parent for Wi-Fi/BT/GNSS; opens /dev/stpwmt. |
| ATCI/EngineerMode | vendor.mediatek.hardware.atci; engineermode | atcid / emhidl_user | DECLARED mostly disabled | STRIP or firewall in clean ROM | Factory/engineering diagnostic control surface. |
| NFC/eSE ghost | android.hardware.nfc context exists; ST54/irq_nfc DT nodes exist | No active NFC HAL | GHOST/ABSENT runtime | STRIP NFC HAL; keep negative record | Secure element is SIM/UICC path, not physical NFC proof. |
| Wiite wearhealthuart | Not HAL-declared; HAL-adjacent UID1000 provider/service/database/socket path | com.wiite.wearhealthuart | ACTIVE app boundary | REPLACE; escalate to Sections 12/13/16 | Boot receiver + PedometerService + WalkProvider + local DB + broad privileges. |

################################################################################
# 2.21 — SECURE ELEMENT / OMAPI / SIM1 UICC BOUNDARY
################################################################################

VINTF declaration:
- `/vendor/etc/vintf/manifest.xml` declares `android.hardware.secure_element@1.0::ISecureElement/SIM1`. [VINTF_DECLARED]
- Selected ODM profile `manifest_ss_se.xml` carries single-SIM + secure-element profile. [SINGLE_SIM_SELECTED] [SIM_SECURE_ELEMENT_SELECTED]

RC declaration:
- `/vendor/etc/init/android.hardware.secure_element@1.0-service-mediatek.rc`
- service `mtk_secure_element_hal_service`
- command `/vendor/bin/hw/android.hardware.secure_element@1.0-service-mediatek`
- class `hal`
- user/group `secure_element`

Runtime registration:
- `lshal`: `android.hardware.secure_element@1.0::ISecureElement/SIM1`. [REGISTERED_HAL]
- Binder service: `secure_element: [android.se.omapi.ISecureElementService]`. [BINDER_SERVICE_DECLARED]
- Process: `android.hardware.secure_element@1.0-service-mediatek` under `u:r:mtk_hal_secure_element:s0`. [PROCESS_ACTIVE]
- App owner: `com.android.se`, UID 1068, `/system/app/SecureElement/SecureElement.apk`, persistent system app.

Dumpsys boundary:
- Terminal: `SIM1`
- `mIsConnected:true`
- open channel list empty
- ARA false
- ARF false
- initial channel access denied with reason `Incorrect path`

Binary/string boundary:
- HAL binary contains MTK UICC Secure Element strings and radio/RIL failure strings.
- Strings include `MTK UICC Secure Element Service`, `MtkUiccSEService`, `get MTK IRadio failed`, `RIL service died`, `SIM%d`, `External SIM doesn't support secure element`.

Canonical classification:
[ACTIVE_RUNTIME] [REGISTERED_HAL] [SIM1_UICC_SECURE_ELEMENT] [MTK_RADIO_DEPENDENT_HAL] [RIL_BOUNDARY] [NOT_NFC_PROOF] [NOT_ESE_SELECTED] [SECTION11_CROSS_REFERENCE] [SECTION16_SECURITY_REVIEW_REQUIRED]

Interpretation:
The secure-element path is a SIM/UICC secure-element path bound to MTK radio/RIL.
It is not NFC hardware proof and must not be used to claim physical NFC presence.


################################################################################
# 2.22 — NFC / ST54 / ESE GHOST RESOLUTION
################################################################################

Observed software/declaration evidence:
- Platform SELinux context includes `android.hardware.nfc::INfc -> u:object_r:hal_nfc_hwservice:s0`. [DECLARED]
- Hardware framework matrices allow secure-element regex instances `SIM[1-9][0-9]*` and `eSE[1-9][0-9]*`. [DECLARED]
- ODM variant library includes non-selected eSE variant manifests. [VINTF_VARIANT_LIBRARY]

Observed device-tree/sysfs evidence from targeted live check:
- `/sys/devices/platform/nfc` exists. [DECLARED]
- `/sys/devices/platform/irq_nfc` exists. [DECLARED]
- `/sys/firmware/devicetree/base/nfc` compatible string: `mediatek,nfc-gpio-v2`.
- `/sys/firmware/devicetree/base/irq_nfc` compatible string: `mediatek,irq_nfc-eint`.
- GPIO/reset/IRQ declarations exist in device tree.

Runtime negative evidence:
- No active NFC HAL process was observed.
- No active ST54/ST21/eSE daemon was observed.
- Active secure-element process is `mtk_secure_element_hal_service` plus `com.android.se`, tied to SIM/UICC properties.
- Live properties show UICC/USIM state, not an NFC CLF runtime.

Canonical classification:
[BSP_GHOST_DEVICE_TREE] [NO_PHYSICAL_NFC_OBSERVED] [NO_ACTIVE_NFC_HAL] [NO_ST54SPI_RUNTIME] [NO_ESE_RUNTIME] [SECURE_ELEMENT_IS_SIM_UICC_PATH] [KEEP_AS_NEGATIVE_HARDWARE_AND_HAL_BOUNDARY]

Clean-ROM action:
- Do not ship NFC HAL as active hardware support unless physical NFC is later proven.
- Keep the negative record in Section 17.


################################################################################
# 2.23 — CAMERA PROVIDER RUNTIME RESOLUTION
################################################################################

Runtime provider:
- `android.hardware.camera.provider@2.4::ICameraProvider/internal/0` registered in `lshal`. [REGISTERED_HAL]
- `camerahalserver` process active under `u:r:mtk_hal_camera:s0`. [PROCESS_ACTIVE]
- RC service `camerahalserver /vendor/bin/hw/camerahalserver` declared by `/vendor/etc/init/camerahalserver.rc`. [RC_DECLARED]
- Binary identity: `/vendor/bin/hw/camerahalserver`, label `u:object_r:mtk_hal_camera_exec:s0`, SHA256 `cce134823576b072c62984fb42c2e216e60f1bb0ae00408625285f5c1808a3a9`.

Targeted `lshal debug` camera provider result:
- Physical Devices #2
- BACK camera: `SENSOR_DRVNAME_GC2385_MIPI_RAW`, hasFlashUnit 1, orientation 90.
- FRONT camera: `SENSOR_DRVNAME_GC030A_MIPI_RAW`, hasFlashUnit 0, orientation 270.
- Virtual device `device@3.5/internal/0`: torch available.
- Virtual device `device@3.5/internal/1`: torch not available.

BF2553L classification:
- BF2553L appears as a BSP/camera-loader trap path in prior hardware work.
- It is not promoted to physical third-camera truth by Section 02.
- Carrying BF2553L-related loader scaffolding may be required to avoid Camera HAL aborts, but physical classification remains unresolved/ghost until hardware proof.

Clean-ROM action:
- Keep `camerahalserver` and the required MTK camera provider stack for first boot.
- Carry loader-required camera scaffolding until Section 06 fully resolves dependencies.


################################################################################
# 2.24 — RADIO / RIL / IMS / WFC / EPDG HAL CONTROL PLANE
################################################################################

Runtime radio HAL surface:
- Android Radio HAL versions registered from `@1.0` through `@1.4`.
- Instances include `slot1`, `imsAospSlot1`, `em1`, and `se1`.
- `android.hardware.radio.config@1.0` and `@1.1` registered.
- `android.hardware.radio@1.0/@1.1/@1.2::ISap/slot1` registered.
- MTK extension `vendor.mediatek.hardware.mtkradioex@1.0` through `@1.5` registered across instances:
  `imsSlot1`, `mtkAssist1`, `mtkCap1`, `mtkEm1`, `mtkRcs1`, `mtkSe1`, `mtkSlot1`.
- MTK `vendor.mediatek.hardware.radio_op@2.0::IRadioOp/OpImsRILd1` and `/slot1` registered.

Runtime daemon/process surface:
- `mtkfusionrild` active as `u:r:rild:s0`.
- `wfca`, `epdg_wod`, `ipsec_mon`, `volte_stack`, `volte_ua`, `volte_imcb`, `volte_imsm_93`, `volte_md_status`, `vtservice`, and `vtservice_hidl` active.
- Binder services include `phone`, `phoneEx`, `mtkIms`, `imtksms`, `capctrl`, `wfo`, and related telephony services.

Known socket/device boundary from Section 02 targeted resolution:
- `mtkfusionrild`: `/dev/socket/sap_uim_socket1`, `/dev/socket/rild-oem`, `/dev/socket/volte_imsvt1`, `/dev/socket/rild-atci`, `@c2kagpsd2`, `/dev/ccci_ss_xcap`, `/dev/ccci_mdx_sta`.
- `wfca`: `/dev/socket/wfca`, `/dev/socket/wfca_rds`, abstract modem/RTP control socket, `/dev/ccci_imsa`.
- `epdg_wod`: `/dev/socket/wod_action`, `/dev/socket/wod_sim`, `@wod_ipsec`, `@wod_dns`, `/dev/ccci_woa`.
- `volte_*`: VoLTE sockets and IMS modem channels.
- `ipsec_mon`: network/IPSec monitoring boundary with `NET_ADMIN`/`NET_RAW` adjacency.

Canonical classification:
[ACTIVE_RUNTIME] [REGISTERED_HAL] [RADIO_IMS_WFC] [CONTROL_BOUNDARY] [EXFIL_ROUTE_CANDIDATE] [SECTION08_PRIMARY] [SECTION16_SECURITY_REVIEW_REQUIRED]

Clean-ROM action:
- Keep radio HAL chain for LTE/voice/SIM functionality.
- Remove or harden engineering/diagnostic and nonessential WFC/ePDG surfaces only after Section 08 determines functional requirements.
- Do not remove `mtkfusionrild` or MTK RadioEx blindly; GSI testing proved telephony depends on this family.


################################################################################
# 2.25 — LBS / GNSS / AGPS HIDL SOCKET-LIKE INTERFACE FANOUT
################################################################################

Runtime GNSS/LBS surface:
- `android.hardware.gnss@1.0`, `@1.1`, and `@2.0::IGnss/default` registered in `lshal`.
- `vendor.mediatek.hardware.lbs@1.0::ILbs` exposes a large instance fanout:
  `AgpsDebugInterface`, `AgpsInterface`, `mtk_agps2framework`, `mtk_framework2agps`,
  `mtk_lppe_socket_agps`, `mtk_lppe_socket_bt`, `mtk_lppe_socket_ipaddr`,
  `mtk_lppe_socket_lbs`, `mtk_lppe_socket_network`, `mtk_lppe_socket_sensor`,
  `mtk_lppe_socket_wlan`, `mtk_meta2mnld`, `mtk_mnld2debugService`,
  `mtk_mnld2mtklogger`, `mtk_mnld2nlputils`, and `mtk_mtklogger2mnld`.

Runtime daemons:
- `gnss_service`
- `lbs_hidl_service`
- `mnld`
- `lbs_dbg`

Canonical classification:
[ACTIVE_RUNTIME] [REGISTERED_HAL] [GNSS_LBS_CONTROL_BOUNDARY] [AGPS_SOCKET_FANOUT] [SECTION09_CROSS_REFERENCE]

Clean-ROM action:
- Keep GNSS/LBS for GPS functionality.
- Audit AGPS/debug/logging sockets in Section 09 and Section 16.


################################################################################
# 2.26 — SENSOR HAL / WIITE / NRF BRIDGE BOUNDARY
################################################################################

Runtime Android HAL surface:
- `android.hardware.sensors@2.0::ISensors/default` registered. [REGISTERED_HAL]
- Service `vendor.sensors-hal-2-0` running. [ACTIVE_RUNTIME]
- Process `android.hardware.sensors@2.0-service-mediatek` active under `u:r:mtk_hal_sensors:s0`.
- Binary identity: `/vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek`, label `u:object_r:mtk_hal_sensors_exec:s0`, SHA256 `57b0a2f704fe387619d20c6754237e79eecd21c160dd6e89614e47897b5021cb`.

Hardware cross-reference:
- Android Sensor HAL crosses into Wiite/nRF external control/sensor plane.
- Prior Section 01 proved init permission mutation for `/sys/class/misc/wiite_corp_ctrl/*` and `/dev/ttyS1`.
- Hardware database identifies UART1 / `/dev/ttyS1` as the nRF52832 UART bridge and `/sys/class/misc/wiite_corp_ctrl` as Wiite/nRF control/status surface.
- Sensor protocol details, calibration, direct I2C health path, and nRF firmware belong to Section 04.

Canonical classification:
[ACTIVE_RUNTIME] [REGISTERED_HAL] [SENSOR_SUBSTRATE] [ODM_CONTROL_BOUNDARY] [UART_BRIDGE_ADJACENT] [SECTION04_PRIMARY] [SECTION13_CROSS_REFERENCE]

Clean-ROM action:
- Do not blindly keep Wiite framework code.
- Preserve hardware sensor function through a clean bridge or replacement path.
- Treat Wiite system-app sensor access as separate from the Android Sensor HAL declaration.


################################################################################
# 2.27 — WMT / WIFI / BLUETOOTH / CONNECTIVITY PARENT BOUNDARY
################################################################################

Runtime surface:
- Wi-Fi HAL `android.hardware.wifi@1.0` through `@1.3::IWifi/default` registered.
- Wi-Fi HAL service `vendor.wifi_hal_legacy` running even though declared disabled/oneshot/lazy.
- Bluetooth HAL `android.hardware.bluetooth@1.0::IBluetoothHci/default` registered.
- Bluetooth audio providers factory registered.
- WMT launcher active as `wmt_launcher`.
- WMT path is HAL-adjacent rather than an Android framework HAL.

Known node boundary:
- `wmt_launcher` opens `/dev/stpwmt`.
- WMT is parent coordination plane for Wi-Fi, Bluetooth, and GNSS on this MTK platform.

Canonical classification:
[ACTIVE_RUNTIME] [CONNECTIVITY_PARENT] [WMT_CONTROL_BOUNDARY] [SECTION09_PRIMARY]

Clean-ROM action:
- Keep WMT/wifi/bt/gnss stack for first functional ROM.
- Strip or harden only factory/logging/diagnostic routes after Section 09 closes firmware and node dependencies.


################################################################################
# 2.28 — WIITE WEARHEALTHUART HAL-ADJACENT ROUTE CANDIDATE
################################################################################

This package is not a HAL declared in VINTF, but it is included in Section 02 because it crosses the
HAL-adjacent boundary: UID1000 system app, boot receiver, service/provider, local database, sockets,
and telephony/network permissions.

Observed package surface:
- Package: `com.wiite.wearhealthuart`
- Path: `/product/app/WearHealthStepUart/WearHealthStepUart.apk`
- UID: 1000
- Flags: SYSTEM, DEBUGGABLE, HAS_CODE, ALLOW_CLEAR_USER_DATA, ALLOW_BACKUP
- Private flags include PRODUCT and legacy external storage.
- Receiver: `.service.BootReceiver` for `android.intent.action.BOOT_COMPLETED`
- Service: `.service.PedometerService`
- Provider: `.provider.WalkProvider`
- Data path: `/data/data/com.wiite.wearhealthuart`
- Database: `/data/data/com.wiite.wearhealthuart/databases/database.db`
- Device `sqlite3` was absent, so no schema was extracted on-device in this section.

Observed permission surface includes:
- privileged telephony and network permissions
- install/delete package permissions
- reboot/master clear/system settings permissions
- Bluetooth privileged permissions
- VPN/network settings permissions
- logs/USB/tethering/hardware-test related permissions

Canonical classification:
[EXFIL_ROUTE_CANDIDATE] [UID1000_SYSTEM_APP] [DEBUGGABLE_SYSTEM_APP] [BOOT_PERSISTENT_TRIGGER] [LOCAL_SENSOR_DATABASE] [OPEN_SOCKET_SURFACE] [CONTENT_PROVIDER_SURFACE] [TELEPHONY_PERMISSION_SURFACE] [SECTION12_CROSS_REFERENCE] [SECTION13_CROSS_REFERENCE] [SECTION16_ESCALATE]

Section 02 boundary:
This section proves the HAL-adjacent route candidate. It does not prove remote endpoint,
payload, or exfiltration content. Those belong to Section 16.


################################################################################
# 2.29 — SHELL EXECUTION / TOOLING BOUNDARY TRAPS
################################################################################

Invalid and corrected capture history is retained because this device is nonstandard and future agents
must not misclassify research tooling as stock behavior.

Trap 1 — broad process grep:
- A grep pattern matching `service` overmatched normal HAL service names.
- Result contained legitimate init-started HAL services, not shell-launched payloads.
- Classification: [INVALID_CAPTURE] [GREP_PATTERN_FALSE_POSITIVE]

Trap 2 — awk nested quoting:
- An exact-shell executable check failed with `awk: non-terminated string {print...`.
- No runtime conclusion may be drawn from that attempt.
- Classification: [INVALID_CAPTURE]

Trap 3 — heredoc/temp-script attempt:
- A multiline heredoc command was unsafe for the host terminal workflow and created host-side shell-history fragments.
- This is a tooling mistake, not device behavior.
- No runtime conclusion may be drawn from the malformed heredoc attempt.
- Classification: [INVALID_CAPTURE] [RESEARCH_ARTIFACT]

Corrected shell executable check:
- Observed `/system/bin/sh` PID 8167, UID 2000 shell, parent `adbd`, command was the active adb capture.
- Observed `/system/bin/sh` PID 8173, UID 0 root, parent `magiskd`, command was the root-side su/Magisk execution of the active capture.
- No unexplained stock `/system/bin/sh` runtime process was observed in the corrected snapshot.

Canonical rule:
/system/bin/sh, toybox, cmd, am, pm, content, settings, logcat, screencap, input,
curl, wget, nc, or busybox must be escalated only when spawned by a Wiite package,
UID1000 package, system_app domain, OTA package, factory/meta service, radio/ATCI/
engineermode service, vendor daemon, hidden receiver, content provider, or Binder-triggered
privileged process with parent/FD/socket proof.


################################################################################
# 2.30 — ORIGINAL DATABASE RECONCILIATION LEDGER
################################################################################

| Original / upstream entry | Disposition | Resolution |
|---|---|---|
| MT6762 physical / MT6765 BSP split | KEEP / EXPAND | Runtime identity and VINTF/RC/BSP paths prove both values in separate layers. |
| Vendor manifest target-level 4 | KEEP | Primary vendor manifest target-level is 4. |
| Single-SIM selected profile | EXPAND | Primary manifest input list references `manifest_ss_se.xml`; ODM variants for DSDS/TSTS/QSQS/eSE retained as non-selected library. |
| Secure element SIM1 | EXPAND / CORRECT | Active path is SIM1/UICC secure element, not NFC proof. |
| NFC/eSE | CORRECT / GHOST | NFC/irq_nfc DT nodes and hwservice context exist, but no active NFC/ST54 runtime; secure element remains SIM/UICC. |
| Radio HAL / MTK RadioEx / RadioOp | KEEP / EXPAND | Runtime registration confirms dense radio/IMS/RadioEx/RadioOp surface. |
| netdagent | KEEP / EXPAND | VINTF, hwservice context, RC, process, and binary hash captured. |
| LBS/GNSS | KEEP / EXPAND | GNSS + LBS HIDL fanout captured; socket-like instances retained. |
| Camera HAL | KEEP / EXPAND | Camera provider registered; targeted debug identified GC2385 BACK and GC030A FRONT. |
| BF2553L | MOVE / OPEN_LEAD | Not physical camera proof in Section 02; carry trap to Section 06. |
| Sensor HAL / Wiite/nRF | KEEP / MOVE | Sensor HAL registered and active; protocol/nRF details move to Section 04. |
| Wiite wearhealthuart | MOVE / ESCALATE | HAL-adjacent UID1000 route candidate documented here; full behavior moves to Sections 12/13/16. |
| Factory/meta/ATCI/engineermode | KEEP / HARDEN_OR_STRIP | Diagnostic services and hwservice contexts exist; final disposition later. |
| Product VINTF / product init / product SELinux contexts | DISPROVED AS ACTIVE ROOTS | No product VINTF/init/service-context authority root in this capture. |

################################################################################
# 2.31 — CLEAN-ROM SECTION 02 GATE REQUIREMENTS
################################################################################

| Gate area | Requirement | Constraint |
|---|---|---|
| VINTF manifest | Build device manifest must declare only active required HALs plus required MTK extensions. | Do not import unselected DSDS/TSTS/QSQS/eSE profiles. |
| Radio | Keep Android Radio HAL + MTK RadioEx + RadioOp for first boot. | Harden diagnostics later; do not remove RIL/RadioEx blindly. |
| Secure element | Keep SIM1 secure-element only if OMAPI/SIM functions required. | Do not advertise NFC/eSE from SIM1 evidence. |
| NFC | Do not ship active NFC HAL without physical proof. | Keep negative register. |
| Sensors | Provide clean Sensor HAL/nRF bridge replacement or carefully confined carry path. | Do not retain Wiite UID1000 control plane as trusted. |
| Camera | Keep camera provider and loader-required blobs. | Resolve BF2553L trap in Section 06. |
| Wi-Fi/BT/GNSS/WMT | Keep MTK connectivity stack initially. | Close firmware/node dependencies in Section 09. |
| DRM/security HALs | Keep keymaster, gatekeeper, DRM services. | Record current unlock/L3 research state. |
| ATCI/engineering | Strip or disable unless required for first boot. | Never leave broad factory/engineering sockets exposed. |
| SELinux | Use hwservice/service context maps to build least-privilege policy. | Do not run final ROM permissive. |
| Product apps | Do not treat product APKs as HAL authority. | Evaluate UID1000 apps in Sections 12/13/16. |

################################################################################
# 2.32 — CLOSED TRAPS AND OPEN LEADS
################################################################################

| Lead / trap | Status | Resolution |
|---|---|---|
| `lshal -p` PID mapping | CLOSED | Not authoritative on this device; use ps/init mapping. |
| Broad `service` grep | CLOSED | False positive pattern; legitimate HAL services overmatched. |
| Awk/heredoc shell check | CLOSED | Invalid capture/tooling issue; not device behavior. |
| Product VINTF root | CLOSED | Absent in current capture. |
| Product init root | CLOSED | Absent in source pack. |
| Product SELinux hw/service context roots | CLOSED | Absent in targeted live capture. |
| Secure element equals NFC | CLOSED | Disproved. Active path is SIM1/UICC secure element. |
| NFC/irq_nfc DT nodes | OPEN_LEAD / NEGATIVE | Declared BSP nodes only; no active NFC runtime. |
| BF2553L camera path | OPEN_LEAD | Carry as camera HAL trap/dependency, not physical truth here. |
| Wiite wearhealthuart endpoint/payload | OPEN_LEAD | Route candidate proven; payload/endpoint belongs Section 16. |
| ATCI/engineermode runtime | OPEN_LEAD | Declared diagnostic HALs; final strip/harden decision later. |
| LBS AGPS/debug fanout | OPEN_LEAD | Runtime fanout proven; security review later. |

################################################################################
# 2.33 — FINAL SECTION 02 INTERPRETATION
################################################################################

Section 02 resolves the HAL authority plane.

The C17S runtime is a MediaTek Android 10 / VNDK 29 HAL stack with an MT6762 physical
identity and an MT6765 BSP/HAL software identity. The primary vendor manifest is target-level
4 and selects a single-SIM + SIM secure-element profile through `manifest_ss_se.xml`.
The device runs a dense MTK HAL extension surface: RadioEx, RadioOp, LBS, netdagent, NVRAM,
PQ, MMS, GPU, mtkpower, videotelephony, ATCI, and engineering/diagnostic contexts.

Runtime `lshal` confirms the active HAL surface. `init.svc.*`, init RC files, process maps,
SELinux contexts, and binary hashes reconcile the service layer beneath that surface.
The highest-risk Section 02 boundaries are radio/IMS/WFC/ePDG, SIM1 secure element, LBS/GNSS
fanout, Wiite/nRF-adjacent sensors, WMT connectivity, and the UID1000 Wiite wearhealthuart
HAL-adjacent route candidate.

The NFC/eSE contradiction is resolved for this section: the active secure-element path is
SIM1/UICC and MTK radio-bound. NFC/ST54/eSE remains a declared/BSP ghost or non-selected
variant path unless later physical proof appears.

The final clean ROM must preserve active hardware HALs required for display, camera, audio,
radio, Wi-Fi/Bluetooth/GNSS, sensors, USB, DRM, keymaster, and gatekeeper, while stripping or
hardening ODM/engineering/diagnostic/control surfaces and replacing Wiite UID1000 control
paths with auditable implementations.

################################################################################
# SECTION 02 ARTIFACT CLOSURE
################################################################################

Output file                  : C17S_SOFTWARE_SECTION02_FINAL.md
Canonical status             : CANONICAL FINAL SECTION 02 — HAL / VINTF / INTERFACE AUTHORITY MAP

Final line count, byte count, and SHA256 are computed externally after write and
reported with the delivered artifact. The hash is not embedded here because embedding
a file hash inside the same file changes the hash value.

################################################################################
