################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 09 — CONNECTIVITY SOFTWARE STACK — WI-FI, BLUETOOTH, GNSS & WMT
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 09 — CONNECTIVITY / WMT / WI-FI / BLUETOOTH / GNSS AUTHORITY MAP
# Closure     : CLOSED WITH LIVE WMT PARENT CHAIN, MODULE HASHES, INIT RC AUTHORITY, FIRMWARE SHA256, FEATURE XMLS, AGPS/SUPL CONFIG, AND BSP GHOSTS CLASSIFIED
# Date        : 2026-06-17 live connectivity, WMT, Wi-Fi, Bluetooth, GNSS, LBS, AGPS, firmware, feature XML, process-map, and device-node closure pass
################################################################################

################################################################################
# SCOPE
################################################################################
This section documents the C17S connectivity software stack as a software
digital-twin database section. It records the MediaTek WMT/connsys parent plane,
the child Wi-Fi, Bluetooth, and GNSS/GPS software paths, the kernel module
dependency chain, runtime services, HAL/HIDL publication, Android framework
service surface, WMT character devices, network interfaces, firmware/config
files, feature XML declarations, AGPS/SUPL configuration, logging/debug surfaces,
factory/META-mode residues, and negative/BSP ghost boundaries.

This section covers:
- WMT/connsys parent: `wmt_drv.ko`, `wmt_loader`, `wmt_launcher`, `/dev/stpwmt`,
  `/dev/wmtdetect`, connsys firmware/config, and WMT firmware log channels.
- Wi-Fi: `wlan_drv_gen4m.ko`, `wmt_chrdev_wifi.ko`, `vendor.wifi_hal_legacy`,
  `wpa_supplicant`, `wificond`, `wlan_assistant`, `hostapd`, `wlan0`, `p2p0`,
  `ap0`, gen4m property selection, Wi-Fi firmware, Wi-Fi config files, and
  Wi-Fi feature XML declarations.
- Bluetooth: `bt_drv.ko`, `android.hardware.bluetooth@1.0-service-mediatek`,
  `BT_FW.cfg`, `soc1_0_ram_bt_1_1_hdr.bin`, `/dev/stpbt`, `/dev/btif`,
  Bluetooth config files, Bluetooth feature XML declarations, and framework
  Bluetooth state.
- GNSS/LBS/AGPS: `gps_drv.ko`, `MNL.bin`, `gnss_service`, `mnld`,
  `mtk_agpsd`, `lbs_hidl_service`, `lbs_dbg`, `/dev/stpgps`, `/dev/gps_emi`,
  `/dev/ttyC2`, GNSS feature XML, AGPS profile XML, and SUPL certificate store.
- All Section 09 connectivity firmware/config files captured by SHA256.
- Factory/META mode connectivity residues, including FM declarations that do not
  become physical FM proof.
- Cross-section boundaries to radio/IWLAN/CCCI, audio Bluetooth routes, nRF/ODM
  sensor control, USB physical interfaces, and software negative registers.

This section does not perform a physical RF board trace. Physical presence or
absence is inherited only from the hardware database and owner-confirmed device
truth. Software declarations, feature XML files, SELinux labels, framework
services, and BSP leftovers are not promoted into hardware unless they are backed
by physical/runtime evidence in the proper layer. This is not a ROM build guide;
clean-ROM relevance is limited to preservation, replacement, stripping, and
risk boundaries.

################################################################################
# 09.0 — SOURCE AUTHORITY AND CAPTURE LEDGER
################################################################################

Authority rules used by this section:
- Live rooted C17S evidence is runtime authority for `/proc`, `/sys`, `/dev`, `getprop`, `ps`, `lshal`, `service list`, `dumpsys`, `dmesg`, and `logcat`.
- Clean partition/source documents remain file-source authority where already canonical; live `/vendor` hashes are explicitly marked as live vendor-file hashes.
- Hardware database truth prevents software leftovers from becoming physical hardware claims.
- Corrected live evidence overrides stale original seed values when the same field is recaptured.

Source intake:
| Source | Bytes | SHA256 | Use |
| --- | --- | --- | --- |
| C17S_SOFTWARE_SECTIONS_DIRECTORY.md | 24093 | 2a13d7c9e35a35287ee3f3ccfcd001d9da11111cc7dc2c6cb0d5d846a6fd80f2 | Section 09 scope and cross-section title authority |
| C17S_SOFTWARE_DATABASE_EXPANSION_WORKFLOW.md | 34860 | f5091b20d3fd22842c88267c4cd5b515cc6dab49c7b624ca2122bc66870afaf8 | Workflow doctrine, authority tiers, delta-ledger rules |
| C17S_SOFTWARE_Forensics.md | 521817 | 4ec9f99599aef2d5c571fb6127aa60b0f90ecc47b7b0903d9f3b448292c9347c | Original Section 9 seed and prior WMT/module model |
| C17S_LOKMAT5MAX_HARDWARE_DATABASE.md | 726019 | 66c96aa30d7238633dac46979c2ff6bf62474d5d9472c94d6b33dfebdc8b84dd | Physical truth boundary and negative hardware guardrail |
| C17S_SOFTWARE_SECTION08_FINAL.md | 69666 | 0bd0dd374734e319f9781556b67057347b62fae9418518ef0683596739424798 | Upstream completed radio/IWLAN/CCCI authority |


Live capture intake:
| Capture | File | Bytes | SHA256 | Role |
| --- | --- | --- | --- | --- |
| Live capture Gate 09F-09O | Pasted text (2).txt | 290120 | 0a0ef7c36bb4389664a5129dc9ca60a0b46783931278413d4ddeb484102a07b3 | Live runtime gates consumed by this section |
| Corrected closure Gate 09B/09C/09D/09E/09G/09H/09I/09J | Pasted text.txt | 158492 | 7de18c4fad2179d75197f45f7a65159714352c676fe70687d7e228645074e2d8 | Live runtime gates consumed by this section |


################################################################################
# 09.1 — CLASSIFICATION LEGEND
################################################################################
[VERIFIED]              : Captured from live C17S or canonical upstream source.
[ACTIVE_RUNTIME]        : Running or present in the live runtime capture.
[FILE_AUTHORITY]        : File exists with size/hash/path evidence.
[LIVE_VENDOR_FILE_HASH] : Hash captured from live mounted vendor/system/product path.
[REQUIRED]              : Required to preserve stock behavior or first clean-ROM parity.
[DECLARED_STOPPED]      : Declared but stopped/lazy/on-demand in the capture.
[DORMANT_BSP_DIAGNOSTIC]: Present artifact, not active during capture.
[BSP_GHOST]             : Software/BSP leftover; not physical hardware proof.
[NEGATIVE_HARDWARE]     : Hardware database/user truth blocks promotion to physical device.
[MOVED_OUT_OF_SCOPE]    : Retained as a database entry but owned by another section.
[PRIVATE_RUNTIME_CAPTURE]: Raw evidence contains user/environment identifiers; canonical body redacts them.
[OPEN_LEAD]             : Preserved as a named research lead but not blocking section closure.

################################################################################
# 09.2 — SECTION DELTA LEDGER
################################################################################
| Original / Scope Item | Disposition | Closure |
| --- | --- | --- |
| Original WMT parent module model | KEEP / EXPAND | `wmt_drv.ko` remains parent; live use-count proves children `wlan_drv_gen4m`, `wmt_chrdev_wifi`, `gps_drv`, `bt_drv`; file hashes added. |
| Original Wi-Fi model | KEEP / EXPAND | gen4m Wi-Fi confirmed with `ro.vendor.wlan.gen=gen4m`, active `wlan0`, `p2p0`, `ap0`, Wi-Fi HAL 1.0-1.3, supplicant, wificond, wlan_assistant, firmware hashes. |
| Original Bluetooth model | KEEP / EXPAND | `bt_drv.ko`, MTK Bluetooth HAL, `/dev/stpbt`, `BT_FW.cfg`, BT RAM firmware, BT config files, HAL maps, and dynamic framework on/off state captured. |
| Original GNSS/GPS model | KEEP / EXPAND | `gps_drv.ko`, MNL.bin, GNSS HAL, `mnld`, `mtk_agpsd`, LBS HIDL fanout, AGPS profile, SUPL CA store, and private location redaction boundary captured. |
| Original USB content | MOVE / CROSS-REF | USB gadget/VID/PID belongs to external physical/build boundary, not the primary Section 09 software scope; preserved as cross-reference only. |
| Original `met.ko` | KEEP_CLASSIFIED | File present and hashed; not active in runtime; retained as dormant BSP diagnostic/profiling module. |
| Original `fpsgo.ko` | DEMOTE / MOVE | Independent performance module, not WMT/Wi-Fi/BT/GNSS dependency; retained by original seed but moved to blob/build inventory boundary. |
| Factory/META `fmradio_drv.ko` declarations | DEMOTE | Declared only in factory/META RC files; not loaded, not found by module-file locate, and not physical FM proof. |
| Feature XML declarations | EXPAND | Wi-Fi, Wi-Fi Direct, Passpoint, Bluetooth, BLE, GPS/location, IPsec tunnel XML metadata and hashes captured. |
| Broken earlier gates | CORRECTED | Initial loop/quote failures for device nodes, RC files, FD/maps, firmware hashes, feature XML metadata, and AGPS configs were resolved by corrected capture. |


################################################################################
# 09.3 — DEVICE CONNECTIVITY IDENTITY
################################################################################
| Property / Field | Captured value | Classification |
| --- | --- | --- |
| ro.hardware / ro.boot.hardware | mt6762 / mt6762 | [VERIFIED] [LIVE_IDENTITY] |
| ro.board.platform / ro.vendor.mediatek.platform | mt6765 / MT6765 | [VERIFIED] [BSP_PLATFORM] |
| ro.build.display.id | APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613 | [VERIFIED] [BUILD_ID] |
| vendor.connsys.driver.ready | yes | [VERIFIED] [CONNSYS_READY] |
| vendor.connsys.formeta.ready | yes | [VERIFIED] [META_READY] |
| persist.vendor.connsys.chipid | 0x6765 | [VERIFIED] [CONNSYS_CHIPID] |
| persist.vendor.connsys.bt_fw_ver | t-neptune-main-soc1_0e1-1916-CERVINO_SOC1_0_E1_ASIC-20200326221201 | [VERIFIED] [BT_FW_VERSION] |
| persist.vendor.connsys.patch.version | 200326221201000 | [VERIFIED] [CONNSYS_PATCH_VERSION] |
| persist.vendor.connsys.wifi_fw_ver | 200326221201000 | [VERIFIED] [WIFI_FW_VERSION] |
| vendor.wlan.driver.version | 1_0_1-20250613073152 | [VERIFIED] [WIFI_DRIVER_VERSION] |
| vendor.wlan.firmware.version | t-neptune-main-soc1_0e1-1916-CERVINO_SOC1_0_E1_ASIC-20200326221201 | [VERIFIED] [WIFI_FW_VERSION_STRING] |
| wlan.driver.status | ok | [VERIFIED] [WIFI_DRIVER_READY] |
| ro.vendor.wlan.gen | gen4m | [VERIFIED] [WIFI_DRIVER_SELECTOR] |
| wifi.interface / wifi.active.interface | wlan0 / wlan0 | [VERIFIED] [WIFI_STA_IFACE] |
| wifi.direct.interface | p2p0 | [VERIFIED] [WIFI_P2P_IFACE] |
| wifi.tethering.interface / ro.vendor.wifi.sap.interface | ap0 / ap0 | [VERIFIED] [SOFTAP_IFACE] |
| ro.mediatek.wlan.p2p / ro.mediatek.wlan.wsc | 1 / 1 | [VERIFIED] [P2P_WSC_DECLARED] |
| ro.vendor.mtk_wapi_support | 1 | [VERIFIED] [WAPI_DECLARED] |
| ro.default_wifi_hotspot | APPLLP_5_MAX | [VERIFIED] [HOTSPOT_NAME] |
| ro.product.bt.name | APPLLP_5_MAX | [VERIFIED] [BT_DEVICE_NAME] |
| ro.vendor.mtk.bt_sap_enable | true | [VERIFIED] [BT_SAP_DECLARED] |
| persist.vendor.bluetooth.state | 0 then 1 across capture | [VERIFIED] [DYNAMIC_FRAMEWORK_STATE] |
| ro.vendor.mtk_gps_support | 1 | [VERIFIED] [GNSS_DECLARED] |
| ro.vendor.mtk_agps_app | 1 | [VERIFIED] [AGPS_APP_DECLARED] |
| persist.sys.gps.lpp | 0 | [VERIFIED] [LPP_PROFILE_PROPERTY] |
| vendor.gps.clock.type | 21 | [VERIFIED] [GNSS_CLOCK_PROPERTY] |
| vendor.gps.gps.version | 0x6765 | [VERIFIED] [GNSS_PLATFORM_VERSION] |
| gsm.network.type | IWLAN | [VERIFIED] [SECTION08_CROSS_REF] |
| persist.vendor.mtk.wfc.enable / viwifi_support | 1 / 1 | [VERIFIED] [WFC_IWLAN_CROSS_REF] |
| ro.vendor.connsys.dedicated.log | 1 | [VERIFIED] [CONNSYS_LOGGING] |

Runtime identity shows the same MTK split already established by earlier
sections: live hardware reports `mt6762`, while board/BSP platform reports
`mt6765` / `MT6765`. Section 09 therefore treats `mt6762` as live hardware
identity and `mt6765` as BSP/platform namespace, not as a contradiction.

The CERVINO/SOC1_0 E1 ASIC string appears consistently in Wi-Fi and Bluetooth
firmware properties. The connsys chip property `0x6765` ties the connectivity
firmware/driver family to the MT6765 BSP stack while the device hardware identity
remains C17S/MT6762-class at runtime.

################################################################################
# 09.4 — WMT / CONNSYS ARCHITECTURE
################################################################################
The C17S connectivity stack is not three independent wireless stacks. It is a
MediaTek WMT/connsys parent plane with child Wi-Fi, Bluetooth, and GNSS/GPS
transports.

Canonical dependency model:

    wmt_drv.ko
      ├── wlan_drv_gen4m.ko
      │     └── wmt_chrdev_wifi.ko
      ├── bt_drv.ko
      └── gps_drv.ko

Live `/proc/modules` confirms:
- `wmt_drv` loaded size 1,224,704 with use-count 5 and dependents
  `wlan_drv_gen4m,wmt_chrdev_wifi,gps_drv,bt_drv`.
- `wmt_chrdev_wifi` loaded size 28,672 with dependency on `wlan_drv_gen4m`.
- `wlan_drv_gen4m`, `bt_drv`, and `gps_drv` are live child drivers.
- `met.ko` is present as a file but is not a live WMT dependency.
- `fpsgo.ko` was present in the original seed as a loaded independent module,
  but it is not a WMT/connsys connectivity dependency.

The parent-plane service is `wmt_launcher`, running as system in
`u:r:mtk_wmt_launcher:s0` and holding `/dev/stpwmt` open. The one-shot loader
`wmt_loader` is declared but stopped after boot/initialization. The property
`vendor.connsys.driver.ready=yes` is the stage-2 trigger for Wi-Fi, Bluetooth,
and GPS child driver insertion.

################################################################################
# 09.5 — KERNEL MODULE INVENTORY AND HASH REGISTER
################################################################################
| Module | Path | File bytes | Loaded size | Flags | Runtime | Use/dependent field | Role | SHA256 | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| wmt_drv.ko | /vendor/lib/modules/wmt_drv.ko | 2,133,064 | 1,224,704 | (O) | YES | 5 | Parent WMT/connsys module. Dependents: wlan_drv_gen4m, wmt_chrdev_wifi, gps_drv, bt_drv. | 2726de3e349b3cecfa533978bc5583c4c0e817c76e88c8a28bd409f7c121a785 | [VERIFIED] [ACTIVE_RUNTIME] [CONNECTIVITY_PARENT] [REQUIRED] |
| wlan_drv_gen4m.ko | /vendor/lib/modules/wlan_drv_gen4m.ko | 5,158,760 | 1,994,752 | (O) | YES | 0 | Main Wi-Fi gen4m driver selected by ro.vendor.wlan.gen=gen4m. | f19c5675b02c6feb8e73c5d6ad31c23968c9e0931fc7a48229a72b76c2805175 | [VERIFIED] [ACTIVE_RUNTIME] [WIFI_DRIVER] [REQUIRED] |
| wmt_chrdev_wifi.ko | /vendor/lib/modules/wmt_chrdev_wifi.ko | 57,720 | 28,672 | (O) | YES | 1 wlan_drv_gen4m | Wi-Fi WMT character-device bridge; depends on wlan_drv_gen4m and wmt_drv. | e01a7e1f3feae836bd6027f261d722eb08ebec89cd79078ec5b4b11988e7f594 | [VERIFIED] [ACTIVE_RUNTIME] [WIFI_WMT_BRIDGE] [REQUIRED] |
| bt_drv.ko | /vendor/lib/modules/bt_drv.ko | 52,216 | 28,672 | (O) | YES | 0 | Bluetooth kernel transport driver under WMT. | cb8de1956ff35f2d62b1a09313c1cd86d053f3bb0a3d075df5f6bcc99e27b0be | [VERIFIED] [ACTIVE_RUNTIME] [BT_DRIVER] [REQUIRED] |
| gps_drv.ko | /vendor/lib/modules/gps_drv.ko | 72,192 | 65,536 | (O) | YES | 0 | GNSS/GPS kernel transport driver under WMT. | 2e7e750e4db4debef2cb238c0ccabac9abc3627506c030d872f4473d3b846ff8 | [VERIFIED] [ACTIVE_RUNTIME] [GNSS_DRIVER] [REQUIRED] |
| met.ko | /vendor/lib/modules/met.ko | 901,856 | — | (O) | NO | — | MTK Embedded Trace profiling module. Present on disk, not loaded in Section 09 runtime capture. | 39b066ea41161046b5298dbecb659c48602bd1109ed4a541dbb7ba1fe148c266 | [VERIFIED] [DORMANT_BSP_DIAGNOSTIC] [KEEP_CLASSIFIED] |
| fpsgo.ko | /vendor/lib/modules/fpsgo.ko | 34,608 | 811,008 | (PO) | YES in original seed | 0 | Independent MTK frame/performance module captured by original seed; not a WMT/Wi-Fi/BT/GNSS dependency. | not rehashed in Section 09 closure gate | [ORIGINAL_SEED] [MOVED_TO_SECTION_10_OR_15] [NOT_CONNECTIVITY_DEPENDENCY] |


Stage ordering:
1. `on boot` in `/vendor/etc/init/init.wmt_drv.rc` inserts `wmt_drv.ko`.
2. `wmt_loader` / `wmt_launcher` initialize the connsys/WMT parent plane.
3. When `vendor.connsys.driver.ready=yes`, RC triggers insert:
   - `bt_drv.ko`
   - `gps_drv.ko`
   - `wmt_chrdev_wifi.ko`
   - `wlan_drv_${ro.vendor.wlan.gen}.ko`, resolved by property to `wlan_drv_gen4m.ko`
4. `wlan_assistant` starts after Wi-Fi char-device and driver insertion.
5. `hostapd` is declared but disabled/oneshot and remains stopped unless SoftAP
   is requested.

################################################################################
# 09.6 — CONNECTIVITY INIT RC AUTHORITY MAP
################################################################################
| RC file | Lines | Bytes | SHA256 | Effective Section 09 content | Classification |
| --- | --- | --- | --- | --- | --- |
| /vendor/etc/init/init.wmt_drv.rc | 2 | 50 | c99409267d7255e6c3435e88269f6956d0452a6d1cd383fa02c5bdd07e217ed4 | on boot -> insmod /vendor/lib/modules/wmt_drv.ko | [VERIFIED] [WMT_STAGE1_LOAD] |
| /vendor/etc/init/init.wlan_drv.rc | 6 | 222 | ebc138d43457d785ddab62a9365edb714ac0ed84334886cd6bea5a7391d161bd | on vendor.connsys.driver.ready=yes -> insmod wmt_chrdev_wifi.ko, wlan_drv_${ro.vendor.wlan.gen}.ko, start wlan_assistant | [VERIFIED] [WIFI_STAGE2_LOAD] |
| /vendor/etc/init/init.bt_drv.rc | 3 | 97 | 2eae40c94cc3e63264eda963b28488005fe641d0e812f0e2397f4a5ed068eb93 | on vendor.connsys.driver.ready=yes -> insmod bt_drv.ko | [VERIFIED] [BT_STAGE2_LOAD] |
| /vendor/etc/init/init.gps_drv.rc | 4 | 100 | afbc9f03f719f29a2b734d20449e2625f30a2b048c43b04e75c655b1cbe897e2 | on vendor.connsys.driver.ready=yes -> insmod gps_drv.ko | [VERIFIED] [GNSS_STAGE2_LOAD] |
| /vendor/etc/init/init_connectivity.rc | 43 | 1155 | d39faaec6a7e9075fbb4c52d7edb8d8d550dc6212ad57c65a0db9ea12da9c2c5 | mkdir stp_dump/connsyslog; service wmt_loader early_hal oneshot; service wmt_launcher early_hal; stp_dump property triggers | [VERIFIED] [WMT_SERVICE_AUTHORITY] |
| /vendor/etc/init/hw/init.connectivity.rc | 76 | 1669 | 1a1472c528c9f974bfb10a2b095786ba0ef11432d2f9f75c01f47af41e69a7a3 | post-fs-data node perms; service mnld; service lbs_dbg; service wpa_supplicant | [VERIFIED] [CORE_CONNECTIVITY_RC] |
| /vendor/etc/init/hw/factory_init.connectivity.rc | 93 | 1943 | 7ffec7a83549f0795738446020ccbe858f5f8ae852deaedfc461dada059e1f06 | factory WMT services, WLAN assistant, BT/Wi-Fi/GPS insmod, fmradio_drv ghost declaration | [VERIFIED] [FACTORY_MODE] [FM_GHOST_DECLARED] |
| /vendor/etc/init/hw/meta_init.connectivity.rc | 88 | 1766 | 7fc55cfc7fbca1a1af8e901b456e2852c7f2313da5243d9389bb4855b2529a7e | META WMT services, WLAN assistant, BT/Wi-Fi/GPS insmod, fmradio_drv ghost declaration | [VERIFIED] [META_MODE] [FM_GHOST_DECLARED] |
| /vendor/etc/init/hostapd.android.rc | 22 | 665 | 27dad27e4491bb32f4043e1fd914c98b6d74886be9057d5ab04e4d5ad2d73529 | service hostapd /vendor/bin/hw/hostapd; IHostapd 1.0/1.1; disabled; oneshot; NET_ADMIN/NET_RAW | [VERIFIED] [SOFTAP_DECLARED] [STOPPED_RUNTIME] |
| /vendor/etc/init/wlan_assistant.rc | 3 | 104 | 1a7b5b331c70f79a44a7875fbf6bb68d50ee0bd2237f0babf420917e7b4f14ef | service wlan_assistant /vendor/bin/wlan_assistant; class main; user wifi; group system gps inet | [VERIFIED] [WIFI_SUPPORT_DAEMON] |
| /vendor/etc/init/android.hardware.wifi@1.0-service-lazy-mediatek.rc | 8 | 267 | 4cb024b3b2401632fb24ca63b5610379c585a9a901c660aa47f875c300139a9f | service vendor.wifi_hal_legacy; IWifi default; oneshot disabled class hal; NET_ADMIN/NET_RAW/SYS_MODULE; user wifi group wifi gps | [VERIFIED] [WIFI_HAL] |
| /vendor/etc/init/android.hardware.bluetooth@1.0-service-mediatek.rc | 15 | 572 | 3a3e1e7e315f73a9c1f116a3cfac9351cfaaf7f5fa7ea59221df097a1622d0b9 | service bluetooth-1-0; class hal; user bluetooth; group bluetooth; SYS_NICE; VTS property start/stop triggers | [VERIFIED] [BT_HAL] |
| /vendor/etc/init/android.hardware.gnss@2.0-service-mediatek.rc | 4 | 130 | 4b0c281a449f2203f7b9299baea6cd2743e147b4e8c85d8b36944e0e57f570ee | service gnss_service; class hal; user system; group system gps | [VERIFIED] [GNSS_HAL] |
| /vendor/etc/init/lbs_hidl_service.rc | 4 | 139 | e28158512d8c365f7682c90970b89607b8a8fb2fde38149dcd007495f097f11f | service lbs_hidl_service; class hal; user system; group system gps radio inet sdcard_r sdcard_rw | [VERIFIED] [LBS_HAL_ADJACENT] |
| /vendor/etc/init/mtk_agpsd_p.rc | 11 | 302 | bd967073fa77e9f9be471ffdf796079f7c524402380409fc014466c710b8246c | mkdir /data/vendor/agps_supl; service agpsd /vendor/bin/mtk_agpsd; sockets agpsd/agpsd2/agpsd3 | [VERIFIED] [AGPS_DAEMON] |
| /system/etc/init/wificond.rc | 5 | 135 | 7ec3ef548e88bcd13577b59239ca88b594454622818e4a9190ad274b1aadee38 | service wificond /system/bin/wificond; user/group wifi; NET_RAW/NET_ADMIN | [VERIFIED] [AOSP_WIFI_FRAMEWORK_DAEMON] |
| /system/etc/init/wifi-events.rc | 64 | 3577 | 3ccbfdac0927cb7a90825736e262ddb015d7e3bd301222a90a6d02862614caa3 | configures Wi-Fi ftrace instance and net/cfg80211 events; controlled by sys.wifitracing.started | [VERIFIED] [WIFI_TRACE_SURFACE] |
| /system/etc/init/init.connectivity.rc | 18 | 319 | 52d5081c94e39f4bffdd4856f990d5b4e6dc93c992be82ce9ead3d52865120c4 | creates /data/debuglogger/connsyslog/bthci for BT debug logging | [VERIFIED] [BT_DEBUG_LOG_SURFACE] |

The RC stack is layered. The small driver RC files are direct module-load
authorities. `init_connectivity.rc` owns the WMT loader/launcher and WMT dump
controls. `init.connectivity.rc` under `vendor/etc/init/hw/` owns the main GPS,
LBS, and supplicant services plus node permissions. Factory and META variants
preserve factory-mode WMT/WLAN/GPS/BT behavior and also carry the `fmradio_drv.ko`
ghost declaration.

################################################################################
# 09.7 — WMT / CONNECTIVITY DEVICE NODES
################################################################################
| Node | Mode | Owner | SELinux label | Major,Minor | Role | Classification |
| --- | --- | --- | --- | --- | --- | --- |
| /dev/stpwmt | crw-rw---- | system:system | u:object_r:stpwmt_device:s0 | 190,0 | WMT launcher control endpoint | [VERIFIED] [WMT_PARENT_NODE] |
| /dev/wmtdetect | crw-rw---- | system:system | u:object_r:wmtdetect_device:s0 | 154,0 | WMT detection/control endpoint | [VERIFIED] [WMT_NODE] |
| /dev/wmtWifi | crw-rw---- | wifi:wifi | u:object_r:wmtWifi_device:s0 | 153,0 | Wi-Fi WMT char endpoint | [VERIFIED] [WIFI_WMT_NODE] |
| /dev/btif | crw------- | system:system | u:object_r:btif_device:s0 | 244,0 | BTIF kernel interface | [VERIFIED] [BT_NODE] |
| /dev/stpbt | crw-rw---- | bluetooth:bluetooth | u:object_r:stpbt_device:s0 | 192,0 | Bluetooth STP transport; BT HAL opens this node | [VERIFIED] [BT_STP_NODE] |
| /dev/stpgps | crw-rw---- | gps:gps | u:object_r:mnld_device:s0 | 191,0 | GPS STP transport | [VERIFIED] [GNSS_STP_NODE] |
| /dev/gps_emi | crw------- | root:root | u:object_r:gps_emi_device:s0 | 221,0 | GPS EMI node; init grants chmod 666 in connectivity RC | [VERIFIED] [GNSS_EMI_NODE] |
| /dev/fw_log_wmt | crw-rw---- | system:system | u:object_r:fw_log_wmt_device:s0 | 223,0 | WMT firmware log channel | [VERIFIED] [CONNSYS_LOG_NODE] |
| /dev/fw_log_wifi | crw-rw---- | wifi:wifi | u:object_r:fw_log_wifi_device:s0 | 219,0 | Wi-Fi firmware log channel | [VERIFIED] [WIFI_LOG_NODE] |
| /dev/fw_log_bt | crw-rw---- | bluetooth:bluetooth | u:object_r:fw_log_bt_device:s0 | 222,0 | Bluetooth firmware log channel | [VERIFIED] [BT_LOG_NODE] |
| /dev/fw_log_gps | crw-rw---- | gps:gps | u:object_r:fw_log_gps_device:s0 | 220,0 | GPS firmware log channel | [VERIFIED] [GNSS_LOG_NODE] |
| /dev/ttyC2 | crw-rw---- | radio:radio | u:object_r:agps_device:s0 | 239,5 | AGPS modem channel opened by mtk_agpsd as FD 36 | [VERIFIED] [AGPS_CCCI_CHANNEL] [SECTION08_CROSS_REF] |

The corrected device-node pass resolved the earlier broken `$n` loop. No
`/sys/class/misc/<node>/dev` or `uevent` metadata was emitted for the targeted
nodes in the capture, but `/dev` ownership, major/minor, and SELinux labels are
complete. The runtime FD pass separately proves which daemons open selected nodes:
`wmt_launcher` opens `/dev/stpwmt`, Bluetooth HAL opens `/dev/stpbt`, and
`mtk_agpsd` opens `/dev/ttyC2`.

################################################################################
# 09.8 — NETWORK INTERFACE INVENTORY
################################################################################
| Interface | Sysfs path | Runtime state | Uevent type | IFINDEX | Role | Classification |
| --- | --- | --- | --- | --- | --- | --- |
| wlan0 | /sys/devices/platform/CONNAC/net/wlan0 | up | DEVTYPE=wlan | 75 | Active Wi-Fi STA interface; IP route through wlan0 was live in capture. | [VERIFIED] [ACTIVE_RUNTIME] [WIFI_STA] |
| p2p0 | /sys/devices/virtual/net/p2p0 | down | DEVTYPE=wlan | 76 | Wi-Fi Direct/P2P interface exists but was down/no-carrier during capture. | [VERIFIED] [DECLARED_RUNTIME_NODE] [P2P] |
| ap0 | /sys/devices/virtual/net/ap0 | down | DEVTYPE=wlan | 77 | SoftAP/tethering interface exists but hostapd was stopped during capture. | [VERIFIED] [DECLARED_RUNTIME_NODE] [SOFTAP] |

Wi-Fi runtime capture shows:
- Wi-Fi is enabled.
- `wlan0` is up and active.
- `p2p0` exists and is down/no-carrier.
- `ap0` exists and is down.
- `hostapd` is declared but stopped.
- `wlan0` carries the active default Wi-Fi route in the capture.
- Exact SSID/BSSID/IP/MAC data is a private runtime capture artifact and is not
  promoted into the public canonical body except where interface existence,
  state, and role are required.

################################################################################
# 09.9 — SERVICE STATE REGISTER
################################################################################
| init.svc name | State | Classification |
| --- | --- | --- |
| wmt_launcher | running | [ACTIVE_RUNTIME] [WMT_PARENT] |
| wmt_loader | stopped | [DECLARED_STOPPED] [ONESHOT_EARLY_HAL] |
| vendor.wifi_hal_legacy | running | [ACTIVE_RUNTIME] [WIFI_HAL] |
| wpa_supplicant | running | [ACTIVE_RUNTIME] [WIFI_SUPPLICANT] |
| wificond | running | [ACTIVE_RUNTIME] [WIFI_FRAMEWORK] |
| wlan_assistant | running | [ACTIVE_RUNTIME] [MTK_WIFI_SUPPORT] |
| hostapd | stopped | [DECLARED_STOPPED] [SOFTAP_ON_DEMAND] |
| bluetooth-1-0 | running | [ACTIVE_RUNTIME] [BT_HAL] |
| gnss_service | running | [ACTIVE_RUNTIME] [GNSS_HAL] |
| mnld | running | [ACTIVE_RUNTIME] [GNSS_ENGINE] |
| agpsd | running | [ACTIVE_RUNTIME] [AGPS_DAEMON] |
| lbs_hidl_service | running | [ACTIVE_RUNTIME] [LBS_HIDL] |
| lbs_dbg | running | [ACTIVE_RUNTIME] [LBS_DEBUG] |

The C17S runs the connectivity HAL and daemon layer continuously even when a
framework feature is logically off. Bluetooth demonstrates this separation: the
Bluetooth HAL service is running in both OFF and ON framework states; the
framework state changes from `persist.vendor.bluetooth.state=0` to `1` when
enabled by application request, but `bluetooth-1-0` remains a live HAL service.

################################################################################
# 09.10 — RUNTIME PROCESS / FD / MAP AUTHORITY
################################################################################
| Process | PID in capture | SELinux domain | UID/GID | Command | FD / map evidence | Classification |
| --- | --- | --- | --- | --- | --- | --- |
| wmt_launcher | 415 | u:r:mtk_wmt_launcher:s0 | system/system | /vendor/bin/wmt_launcher -p /vendor/firmware/ -o 1 | /dev/stpwmt FD5; pmsg; socket | [ACTIVE_RUNTIME] [WMT_PARENT_DAEMON] |
| android.hardware.wifi@1.0-service-lazy-mediatek | 17017 | u:r:mtk_hal_wifi:s0 | wifi/wifi | /vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek | hwbinder; libwifi-hal.so; android.hardware.wifi@1.0-1.3 maps | [ACTIVE_RUNTIME] [WIFI_HAL] |
| wpa_supplicant | 17052 | u:r:hal_wifi_supplicant_default:s0 | wifi/wifi | /vendor/bin/hw/wpa_supplicant -g@android:wpa_wlan0 | hwbinder; many control sockets; vendor.mediatek.hardware.wifi.supplicant@2.0/@2.1 maps | [ACTIVE_RUNTIME] [WIFI_SUPPLICANT] |
| hostapd | — | — | — | /vendor/bin/hw/hostapd | Declared in RC; not running in capture | [DECLARED_STOPPED] [SOFTAP_ON_DEMAND] |
| wificond | 688 | u:r:wificond:s0 | wifi/wifi | /system/bin/wificond | binder, hwbinder, trace_marker, sockets | [ACTIVE_RUNTIME] [AOSP_WIFI_DAEMON] |
| wlan_assistant | 708 | u:r:wlan_assistant:s0 | wifi/system | /vendor/bin/wlan_assistant | socket, pmsg, inotify | [ACTIVE_RUNTIME] [MTK_WIFI_SUPPORT] |
| android.hardware.bluetooth@1.0-service-mediatek | 517 | u:r:mtk_hal_bluetooth:s0 | bluetooth/bluetooth | /vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek | /dev/stpbt FD10; libbluetooth_mtk.so; libbt-vendor.so; NVRAM libs | [ACTIVE_RUNTIME] [BT_HAL] |
| android.hardware.gnss@2.0-service-mediatek | 524 | u:r:mtk_hal_gnss:s0 | system/system | /vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek | hwbinder/vndbinder; gps.default.so; flp.default.so; GNSS 1.0/1.1/2.0 libs | [ACTIVE_RUNTIME] [GNSS_HAL] |
| mnld | 638 | u:r:mnld:s0 | gps/gps | /vendor/bin/mnld | 16 threads; many sockets; libmnl.so; libDR.so; geofence; NVRAM libs | [ACTIVE_RUNTIME] [GNSS_ENGINE_DAEMON] |
| mtk_agpsd | 703 | u:r:mtk_agpsd:s0 | gps/gps | /vendor/bin/mtk_agpsd | 45 threads; many sockets; /dev/ttyC2 FD36 | [ACTIVE_RUNTIME] [AGPS_DAEMON] |
| lbs_hidl_service | 538 | u:r:lbs_hidl_service:s0 | system/system | /vendor/bin/lbs_hidl_service | hwbinder; vendor.mediatek.hardware.lbs@1.0; many sockets | [ACTIVE_RUNTIME] [LBS_HIDL] |
| lbs_dbg | 639 | u:r:lbs_dbg:s0 | system/gps | /system/bin/lbs_dbg | hwbinder; sockets; trace_marker | [ACTIVE_RUNTIME] [LBS_DEBUG_SERVICE] |
| [mtk_wmtd] | 666 | u:r:kernel:s0 | root | kernel thread | WMT kernel daemon thread | [ACTIVE_RUNTIME] [KERNEL_THREAD] |
| [mtk_wmtd_worker] | 670 | u:r:kernel:s0 | root | kernel thread | WMT worker thread | [ACTIVE_RUNTIME] [KERNEL_THREAD] |
| [conn-md-thread] | 59 | u:r:kernel:s0 | root | kernel thread | Connectivity/modem thread; cross-section with radio CCCI | [ACTIVE_RUNTIME] [KERNEL_THREAD] [SECTION08_CROSS_REF] |

Runtime process-map closure proves the stack composition, not only service names.

Key process truths:
- `wmt_launcher` is the parent user-space WMT daemon and directly opens
  `/dev/stpwmt`.
- `android.hardware.wifi@1.0-service-lazy-mediatek` maps `libwifi-hal.so` and
  Android Wi-Fi HIDL libraries through @1.3.
- `wpa_supplicant` maps Android supplicant HIDL and MTK vendor supplicant
  extension libraries.
- `wificond` is AOSP framework-side Wi-Fi control and uses binder/hwbinder.
- Bluetooth HAL maps MTK Bluetooth vendor libraries and NVRAM libraries and
  opens `/dev/stpbt`.
- GNSS HAL maps `gps.default.so`, `flp.default.so`, and MTK GNSS HAL
  implementation libraries.
- `mnld` maps `libmnl.so`, NVRAM libraries, `libDR.so`, geofence, and sensor
  bridge libraries; this is the live MTK GNSS engine daemon.
- `mtk_agpsd` opens `/dev/ttyC2`, tying AGPS to the modem/CCCI side as a
  Section 08 cross-reference.

################################################################################
# 09.11 — HAL / HIDL / BINDER PUBLICATION MAP
################################################################################
| Interface / service | Evidence | Owner / route | Classification |
| --- | --- | --- | --- |
| android.hardware.wifi@1.0::IWifi/default | lshal DM,FC Y | vendor.wifi_hal_legacy / mtk_hal_wifi | [VERIFIED] [WIFI_HAL] |
| android.hardware.wifi@1.1::IWifi/default | lshal DM,FC Y | vendor.wifi_hal_legacy / mtk_hal_wifi | [VERIFIED] [WIFI_HAL] |
| android.hardware.wifi@1.2::IWifi/default | lshal DM,FC Y | vendor.wifi_hal_legacy / mtk_hal_wifi | [VERIFIED] [WIFI_HAL] |
| android.hardware.wifi@1.3::IWifi/default | lshal DM,FC Y | vendor.wifi_hal_legacy / mtk_hal_wifi | [VERIFIED] [WIFI_HAL] |
| android.hardware.wifi.supplicant@1.0::ISupplicant/default | lshal DM,FC Y | wpa_supplicant | [VERIFIED] [SUPPLICANT_HAL] |
| android.hardware.wifi.supplicant@1.1::ISupplicant/default | lshal DM,FC Y | wpa_supplicant | [VERIFIED] [SUPPLICANT_HAL] |
| android.hardware.wifi.supplicant@1.2::ISupplicant/default | lshal DM,FC Y | wpa_supplicant | [VERIFIED] [SUPPLICANT_HAL] |
| vendor.mediatek.hardware.wifi.supplicant@2.0::ISupplicant/default | lshal DM Y | wpa_supplicant vendor extension | [VERIFIED] [MTK_SUPPLICANT_EXT] |
| android.system.wifi.keystore@1.0::IKeystore/default | lshal FM Y | system Wi-Fi keystore | [VERIFIED] [WIFI_KEYSTORE] |
| android.hardware.bluetooth@1.0::IBluetoothHci/default | lshal DM,FC Y; PID 517 | bluetooth-1-0 / mtk_hal_bluetooth | [VERIFIED] [BT_HCI_HAL] |
| android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default | lshal DM,FC Y; PID 516 in Section 09 lshal | Bluetooth audio HAL provider | [VERIFIED] [BT_AUDIO_HAL] [SECTION07_CROSS_REF] |
| android.hardware.gnss@1.0::IGnss/default | lshal DM Y | gnss_service compatibility surface | [VERIFIED] [GNSS_HAL] |
| android.hardware.gnss@1.1::IGnss/default | lshal DM,FC Y | gnss_service compatibility surface | [VERIFIED] [GNSS_HAL] |
| android.hardware.gnss@2.0::IGnss/default | lshal DM,FC Y; PID 524 | gnss_service / mtk_hal_gnss | [VERIFIED] [GNSS_HAL] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/AgpsInterface | lshal DM Y | lbs_hidl_service | [VERIFIED] [LBS_HIDL] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/AgpsDebugInterface | lshal DM Y | lbs_hidl_service | [VERIFIED] [LBS_HIDL] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_agps2framework | lshal DM Y | AGPS/framework bridge | [VERIFIED] [LBS_BRIDGE] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_framework2agps | lshal DM Y | framework/AGPS bridge | [VERIFIED] [LBS_BRIDGE] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_agps | lshal DM Y | LPPE AGPS socket bridge | [VERIFIED] [LPPE_SOCKET] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_bt | lshal DM Y | LPPE BT socket bridge | [VERIFIED] [LPPE_SOCKET] [BT_CROSS_REF] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_ipaddr | lshal DM Y | LPPE IP address bridge | [VERIFIED] [LPPE_SOCKET] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_lbs | lshal DM Y | LPPE LBS socket bridge | [VERIFIED] [LPPE_SOCKET] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_network | lshal DM Y | LPPE network bridge | [VERIFIED] [LPPE_SOCKET] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_sensor | lshal DM Y | LPPE sensor bridge; does not prove separate physical sensor | [VERIFIED] [LPPE_SOCKET] [SECTION04_CROSS_REF] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_lppe_socket_wlan | lshal DM Y | LPPE WLAN bridge | [VERIFIED] [LPPE_SOCKET] [WIFI_CROSS_REF] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_meta2mnld | lshal DM Y | META/GNSS bridge | [VERIFIED] [META_DIAG] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2debugService | lshal DM Y | MNLD debug bridge | [VERIFIED] [DEBUG_BRIDGE] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2mtklogger | lshal DM Y | MTK logger bridge | [VERIFIED] [LOGGING_BRIDGE] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mnld2nlputils | lshal DM Y | NLP utilities bridge | [VERIFIED] [LBS_BRIDGE] |
| vendor.mediatek.hardware.lbs@1.0::ILbs/mtk_mtklogger2mnld | lshal DM Y | MTK logger to MNLD bridge | [VERIFIED] [LOGGING_BRIDGE] |


Framework Binder services in scope:
| Binder service | Interface | Classification |
| --- | --- | --- |
| wifi | android.net.wifi.IWifiManager | [VERIFIED] [FRAMEWORK_SERVICE] |
| wifip2p | android.net.wifi.p2p.IWifiP2pManager | [VERIFIED] [FRAMEWORK_SERVICE] |
| wificond | android.net.wifi.IWificond | [VERIFIED] [FRAMEWORK_SERVICE] |
| wifiscanner | android.net.wifi.IWifiScanner | [VERIFIED] [FRAMEWORK_SERVICE] |
| bluetooth_manager | android.bluetooth.IBluetoothManager | [VERIFIED] [FRAMEWORK_SERVICE] |
| location | android.location.ILocationManager | [VERIFIED] [FRAMEWORK_SERVICE] |
| country_detector | android.location.ICountryDetector | [VERIFIED] [FRAMEWORK_SERVICE] |
| connectivity | android.net.IConnectivityManager | [VERIFIED] [FRAMEWORK_SERVICE] [SECTION08_CROSS_REF] |
| ethernet | android.net.IEthernetManager | [VERIFIED] [DECLARED_FRAMEWORK_SERVICE] [NO_PHYSICAL_ETHERNET_PROOF] |
| network_stack | android.net.INetworkStackConnector | [VERIFIED] [FRAMEWORK_SERVICE] |
| netd | android.net.INetd | [VERIFIED] [FRAMEWORK_SERVICE] |
| mtkconnmetrics | com.mediatek.net.connectivity.IMtkIpConnectivityMetrics | [VERIFIED] [MTK_CONNECTIVITY_METRICS] |
| connmetrics | android.net.IIpConnectivityMetrics | [VERIFIED] [FRAMEWORK_SERVICE] |

The LBS fanout is intentionally preserved in Section 09 because it is the
software bridge between GNSS, AGPS, BT, WLAN, network, sensor, logger, META, and
framework domains. The presence of LBS `mtk_lppe_socket_sensor` is not physical
sensor proof; physical sensor ownership remains Section 04 / hardware database.

################################################################################
# 09.12 — WI-FI STACK
################################################################################
The C17S Wi-Fi stack is MTK gen4m over WMT/connsys.

Verified Wi-Fi layers:
- Kernel parent: `wmt_drv.ko`.
- Kernel Wi-Fi child: `wlan_drv_gen4m.ko`.
- Kernel WMT Wi-Fi char bridge: `wmt_chrdev_wifi.ko`.
- Driver selector: `ro.vendor.wlan.gen=gen4m`.
- Driver status: `wlan.driver.status=ok`.
- Driver version: `1_0_1-20250613073152`.
- Firmware version string:
  `t-neptune-main-soc1_0e1-1916-CERVINO_SOC1_0_E1_ASIC-20200326221201`.
- HAL: `vendor.wifi_hal_legacy`, process
  `/vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek`, HIDL
  `android.hardware.wifi@1.0` through `@1.3`.
- Supplicant: `/vendor/bin/hw/wpa_supplicant -g@android:wpa_wlan0`, HIDL
  `android.hardware.wifi.supplicant@1.0` through `@1.2` plus MTK vendor
  supplicant extension.
- Framework daemon: `/system/bin/wificond`.
- MTK support daemon: `/vendor/bin/wlan_assistant`.
- Interfaces: `wlan0`, `p2p0`, `ap0`.
- SoftAP daemon: `hostapd` declared with IHostapd 1.0/1.1 but stopped in capture.

Runtime Wi-Fi behavior:
- `wlan0` was active and connected in capture.
- Wi-Fi route table had active Wi-Fi default route.
- `p2p0` and `ap0` existed but were down.
- `WifiVendorHal getWifiLinkLayerStats_1_3_Internal` returned
  `ERROR_NOT_SUPPORTED`; this is a HAL capability limitation, not Wi-Fi failure.
- Kernel/log capture showed cfg80211 connect/key events and `wlan0` TX/RX paths.
- Exact SSID/BSSID and private IP details are retained only in raw capture and
  are redacted from the canonical section body.

Wi-Fi config files:
| Path | Bytes | SHA256 | Classification |
| --- | --- | --- | --- |
| /vendor/etc/wifi/wpa_supplicant.conf | 318 | 20795c73c5f4b0a6a1ed33ea7bcd39ed7df21c255d5d7cd145922de29914a7cc | [VERIFIED] [WIFI_SUPPLICANT_CONFIG] |
| /vendor/etc/wifi/wpa_supplicant_overlay.conf | 127 | d5cc6c93e3c268d4ea58dc2f841e2bb63b4cdf47786379fcc828b71811706b77 | [VERIFIED] [WIFI_SUPPLICANT_OVERLAY] |
| /vendor/etc/wifi/p2p_supplicant_overlay.conf | 135 | b053c6ad5f23feec0b657aab58712f5375b803b9a93f489feede5931007c161e | [VERIFIED] [WIFI_P2P_OVERLAY] |


################################################################################
# 09.13 — BLUETOOTH STACK
################################################################################
The C17S Bluetooth stack is MTK Bluetooth HCI over WMT/connsys, not the nRF52832
sensor/control bridge. The nRF/Wiite bridge belongs to Section 04 / Section 13.
Section 09 owns the Android/MTK Bluetooth HCI, BT HAL, BT firmware, BT config,
BT feature XML, and framework Bluetooth manager surfaces.

Verified Bluetooth layers:
- Kernel parent: `wmt_drv.ko`.
- Kernel Bluetooth child: `bt_drv.ko`.
- Device nodes: `/dev/stpbt`, `/dev/btif`, `/dev/fw_log_bt`.
- HAL: `/vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek`.
- HAL interface: `android.hardware.bluetooth@1.0::IBluetoothHci/default`.
- Audio cross-surface: `android.hardware.bluetooth.audio@2.0`.
- Vendor libraries: `libbluetooth_mtk.so`, `libbt-vendor.so`,
  `android.hardware.bluetooth@1.0-impl-mediatek.so`, NVRAM libraries.
- Firmware/config: `BT_FW.cfg`, `soc1_0_ram_bt_1_1_hdr.bin`, MTK Bluetooth
  stack config files.
- Feature XML: classic Bluetooth and Bluetooth LE declarations are both present.

Framework Bluetooth behavior:
- Initial capture: framework Bluetooth OFF, HAL still running, device name
  `APPLLP_5_MAX`, no crash count.
- Later capture: framework Bluetooth ON after application request by
  `com.wiite.home`; adapter state reached `OnState`.
- Bonded-device address and external accessory name from raw capture are private
  runtime data and are redacted from the canonical body. The fact of BR/EDR
  profile exposure and active framework state is retained.

Bluetooth config and audio-adjacent files:
| Path | Bytes | SHA256 | Classification |
| --- | --- | --- | --- |
| /system/etc/bluetooth/bt_did.conf | 2658 | 43414c110b64158bd2625ff0a1cdde7dbf02e7923d660b93b0a2055d379b9455 | [VERIFIED] [BT_STACK_CONFIG] |
| /system/etc/bluetooth/bt_stack.conf | 1952 | a9d1235b711007d6bcea8c6aa8c99d83e552e6e81951069515a266a57a2e3033 | [VERIFIED] [BT_STACK_CONFIG] |
| /system/etc/bluetooth/mtk_bt_fw.conf | 1475 | 9c7d0a8f5edd4795c4a82242b9f488fd28fc1bad8030ed9f072726b2f3b94804 | [VERIFIED] [MTK_BT_CONFIG] |
| /system/etc/bluetooth/mtk_bt_stack.conf | 1680 | 2e1a86ea5bbb7ecfb6967914a8e0d7758871da3cf466dbda5022a11b6b524eb1 | [VERIFIED] [MTK_BT_CONFIG] |
| /vendor/etc/bluetooth_audio_policy_configuration.xml | 2099 | 877d8745fe542a2e1070d773d64c7c6b3d03168679d3d8355dd8ee283c5ec884 | [VERIFIED] [BT_AUDIO_POLICY] [SECTION07_CROSS_REF] |
| /vendor/etc/audio_policy_configuration_bluetooth_legacy_hal.xml | 14794 | 720c734a9ac2086ed577038565fbac05873cdd1c8f3b349b735748d15202a0de | [VERIFIED] [BT_AUDIO_POLICY] [SECTION07_CROSS_REF] |


################################################################################
# 09.14 — GNSS / LBS / AGPS STACK
################################################################################
The C17S GNSS stack is MTK GNSS/LBS over WMT/connsys with AGPS and modem-side
assistance.

Verified GNSS layers:
- Kernel parent: `wmt_drv.ko`.
- Kernel GNSS child: `gps_drv.ko`.
- Firmware marker: `MNL.bin`.
- HAL: `/vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek`.
- HAL interfaces: `android.hardware.gnss@1.0`, `@1.1`, `@2.0`.
- GNSS engine daemon: `/vendor/bin/mnld`.
- AGPS daemon: `/vendor/bin/mtk_agpsd`.
- LBS HIDL bridge: `/vendor/bin/lbs_hidl_service`.
- LBS debug service: `/system/bin/lbs_dbg`.
- Device nodes: `/dev/stpgps`, `/dev/gps_emi`, `/dev/fw_log_gps`.
- Modem/AGPS channel: `/dev/ttyC2`, opened by `mtk_agpsd` in FD capture.
- Runtime properties: `ro.vendor.mtk_gps_support=1`,
  `ro.vendor.mtk_agps_app=1`, `vendor.gps.gps.version=0x6765`,
  `vendor.gps.clock.type=21`.

Location framework behavior:
- `gnss_service`, `mnld`, `agpsd`, `lbs_hidl_service`, and `lbs_dbg` were all
  running.
- Android location framework had GPS, network, fused, and passive provider
  surfaces.
- Raw dumpsys contained last-known precise coordinates. These are intentionally
  not reproduced in this canonical section body. The existence of GPS/network/
  fused/passive provider state is retained; exact coordinates remain raw-evidence
  only.

AGPS / GNSS / SUPL config register:
| Path / family | Lines | Bytes | SHA256 / count | Role | Classification |
| --- | --- | --- | --- | --- | --- |
| /vendor/etc/agps_profiles_conf2.xml | 888 | 27012 | 5d37714fbc3015728bcf358f48fc316c4d27a7ac3e211bdace0a5d918f362d36 | Primary MTK AGPS/SUPL profile XML. Current profile GOOGLE; contains Google, lab, CMCC, T-Mobile, carrier/lab profiles. | [VERIFIED] [AGPS_PROFILE] [NETWORK_ENDPOINTS_PRESENT] |
| /system/etc/gps_debug.conf | 52 | 1585 | 336f1c49c575c018e1d07b107afd5db25cb1e8a22a7d4f5afa0a41c1c25220e7 | Sample/debug override config; all SUPL settings commented in capture. | [VERIFIED] [DEBUG_CONFIG] [NOT_ACTIVE_PROFILE_PROOF] |
| /vendor/etc/init/mtk_agpsd_p.rc | 11 | 302 | bd967073fa77e9f9be471ffdf796079f7c524402380409fc014466c710b8246c | AGPS daemon init authority. | [VERIFIED] [AGPS_INIT] |
| /vendor/etc/security/cacerts_supl/ | 6 prod + 18 lab cert files | see SUPL CA table | hashes captured individually | SUPL trust-anchor directory including carrier/lab TLS anchors. | [VERIFIED] [SUPL_TLS_TRUST_STORE] |


SUPL certificate catalog:
| Cert | Path | Lines | Bytes | SHA256 |
| --- | --- | --- | --- | --- |
| 111e6273.0 | /vendor/etc/security/cacerts_supl/111e6273.0 | 88 | 4716 | 3ae7355e769a926cee48de3da29e6b57b7efc5d7ed647c7aa3cd8975f62c0f9d |
| 3ad48a91.0 | /vendor/etc/security/cacerts_supl/3ad48a91.0 | 78 | 4361 | 3a6489a0f5de39e20e7220c0fcfaddba21d2e7e8f8014dcfe07ba89ef4faecc6 |
| 7651b327.0 | /vendor/etc/security/cacerts_supl/7651b327.0 | 49 | 2657 | 7eb7fbe2f35f864060b5265d2b04fd5531003271456c706335203a86cb093678 |
| 848ea856.0 | /vendor/etc/security/cacerts_supl/848ea856.0 | 49 | 2624 | b5e6ed63c7e74db2586c4c67902741e83b3b1a00bce0554a74e13417c927927f |
| c542733d.0 | /vendor/etc/security/cacerts_supl/c542733d.0 | 55 | 2925 | e0e3eaa2ee0827bc9f5f2fdaae4117bdcc4661b02bd8b89a1f075279136e7f10 |
| f336e3d1.0 | /vendor/etc/security/cacerts_supl/f336e3d1.0 | 61 | 3148 | 529843b12ded7392e049f45c1b60ce9bb982a8c9ce64386aec8ab4bebe2321f9 |
| lab/18dc3a8d.0 | /vendor/etc/security/cacerts_supl/lab/18dc3a8d.0 | 81 | 4512 | 5f3ecb45f86bc58fe63f1a439742be4d20b1101de25c17bc35deea29b080f782 |
| lab/1bc5ca0c.0 | /vendor/etc/security/cacerts_supl/lab/1bc5ca0c.0 | 60 | 3090 | 034b3b64ad6c298931f20d294b74e72626ec63047c2576b149e62682933b0abf |
| lab/1bc5ca0c.1 | /vendor/etc/security/cacerts_supl/lab/1bc5ca0c.1 | 50 | 2636 | b2678118214feafed63271a07b3e57bd4d4ed6818014ffc3af6bd829da8a0b75 |
| lab/2cbfcc3a.0 | /vendor/etc/security/cacerts_supl/lab/2cbfcc3a.0 | 40 | 2105 | 02527fe06786f2f7f43fc31ecb3a88c8854130b179c676bbc1b22b2822499c15 |
| lab/35ef1c14.0 | /vendor/etc/security/cacerts_supl/lab/35ef1c14.0 | 41 | 2204 | f12064d0f70a65d5f3c1aeaa65124951f7ae4ae3b86fa578d9e5eab79e157e6b |
| lab/35ef1c14.1 | /vendor/etc/security/cacerts_supl/lab/35ef1c14.1 | 73 | 4264 | 10f501f3a1869bcbdeb3df36811be2bc3cbb90236826424b490af5eb06dcfac1 |
| lab/35ef1c14.2 | /vendor/etc/security/cacerts_supl/lab/35ef1c14.2 | 52 | 2884 | 030a4ba830203bc28f3f9d83ee861d2d15483ec50b7cbafa2227e776985e5400 |
| lab/35ef1c14.3 | /vendor/etc/security/cacerts_supl/lab/35ef1c14.3 | 73 | 4264 | 5a7764600b2005e7190d125f144da843c43abf6a263d09e052f0bc821b6da75d |
| lab/35ef1c14.4 | /vendor/etc/security/cacerts_supl/lab/35ef1c14.4 | 115 | 6992 | b508d1edd1b9f1680157831fcfc28acf91d38902a752335a05e625dcaf864cb4 |
| lab/586933d9.0 | /vendor/etc/security/cacerts_supl/lab/586933d9.0 | 61 | 3238 | ff52c8e5a6b1a5fe6f4842509c2d4368bbd839972ae499b80d35d967fb0a0b63 |
| lab/66272d0d.0 | /vendor/etc/security/cacerts_supl/lab/66272d0d.0 | 55 | 3102 | 6210e27e05a6d457869143d895afb89b0fafec4180c1d9c62357c43c700135ee |
| lab/a64489a3.0 | /vendor/etc/security/cacerts_supl/lab/a64489a3.0 | 39 | 2056 | 69b3bcba482bcf98693cd07753e5b1be330fb9914942f26fd4e1c0688ffea82e |
| lab/a64489a3.1 | /vendor/etc/security/cacerts_supl/lab/a64489a3.1 | 113 | 6844 | 3777e5b1ff28c3329b88d84c0d236124d0b1c5ded5e9000bcda277ae8cd9567f |
| lab/b5805141.0 | /vendor/etc/security/cacerts_supl/lab/b5805141.0 | 63 | 3224 | 4938d66d39b6a8e411220481048eb1f745ec057b9734a0a6dca46e1c6eae2b4c |
| lab/c9dbfc2e.0 | /vendor/etc/security/cacerts_supl/lab/c9dbfc2e.0 | 42 | 2233 | c9c21ba6116e7c461460818f380baa3876e0df980af527bc76b37f0bcd1865f1 |
| lab/ed7b215e.0 | /vendor/etc/security/cacerts_supl/lab/ed7b215e.0 | 41 | 2218 | e9dbce38d4aa8da1c5a8172d2b93c745cf0c2f854605076c5163206b3b60c06b |
| lab/ef4b31e6.0 | /vendor/etc/security/cacerts_supl/lab/ef4b31e6.0 | 50 | 2791 | c33bb95ba29267cf6d869d80b5ffe34f40a99566d0b526a3be779ac1d1856330 |


################################################################################
# 09.15 — CONNECTIVITY FIRMWARE CATALOG
################################################################################
| Artifact | Path | Bytes | SHA256 | Role | Classification |
| --- | --- | --- | --- | --- | --- |
| WMT_SOC.cfg | /vendor/firmware/WMT_SOC.cfg | 121 | 6845e0f30219d5aedaf3791b8318eaaf7ae22d0087702268a815c8724243e2e8 | WMT / connsys chip configuration | [VERIFIED] [WMT_CONFIG] [REQUIRED] |
| BT_FW.cfg | /vendor/firmware/BT_FW.cfg | 410 | e018d7b8153af454d7e5c51626d584582904ffc5ba2bd8edb5d2a1ed27124b67 | Bluetooth firmware configuration | [VERIFIED] [BT_CONFIG] [REQUIRED] |
| MNL.bin | /vendor/firmware/MNL.bin | 128 | 38723a2e5e8a17aa7950dc008209944e898f69a7bd10a23c839d341e935fd5ca | MTK GNSS/MNL firmware marker | [VERIFIED] [GNSS_MNL] [REQUIRED] |
| WIFI_RAM_CODE_soc1_0_1_1.bin | /vendor/firmware/WIFI_RAM_CODE_soc1_0_1_1.bin | 612,652 | bffe8f712bf16666b5ccec1d7eb1149334e6b8f66652a68e4ebfb63347634566 | Wi-Fi RAM code image | [VERIFIED] [WIFI_FIRMWARE] [REQUIRED] |
| soc1_0_patch_mcu_1_1_hdr.bin | /vendor/firmware/soc1_0_patch_mcu_1_1_hdr.bin | 26,350 | f90816944375a023801af68bb5cf1cb163c76d0cb3a52b79b34084cc8aaee91e | SOC1 MCU patch image | [VERIFIED] [CONNSYS_MCU_PATCH] [REQUIRED] |
| soc1_0_ram_mcu_1_1_hdr.bin | /vendor/firmware/soc1_0_ram_mcu_1_1_hdr.bin | 104,324 | 47a349e4ddae156404fa4f65c824ad3da91d1027c9cbdfe0bb3ef16d47ca1600 | SOC1 MCU RAM image | [VERIFIED] [CONNSYS_MCU_FIRMWARE] [REQUIRED] |
| soc1_0_ram_wifi_1_1_hdr.bin | /vendor/firmware/soc1_0_ram_wifi_1_1_hdr.bin | 195,228 | f99d7a212e4c4d00bf33f8241ff5bd4b8b93315558646e448c25a05762cc9191 | SOC1 Wi-Fi RAM image | [VERIFIED] [WIFI_FIRMWARE] [REQUIRED] |
| soc1_0_ram_bt_1_1_hdr.bin | /vendor/firmware/soc1_0_ram_bt_1_1_hdr.bin | 570,765 | 36fcbd3e272fbd496c330f31f90ba31c07ee5a9d48888fefa2d27c352966ed19 | SOC1 Bluetooth RAM image | [VERIFIED] [BT_FIRMWARE] [REQUIRED] |

Firmware conclusion:
- The Section 09 firmware inventory is closed for WMT, Wi-Fi, Bluetooth, MCU,
  and GNSS/MNL artifacts.
- All listed firmware files are rooted under `/vendor/firmware`.
- All listed firmware files carry `u:object_r:vendor_file:s0` in the corrected
  live vendor-file capture.
- Wi-Fi and Bluetooth runtime version strings match the SOC1/CERVINO family
  represented by the shipped firmware filenames.

################################################################################
# 09.16 — FEATURE XML DECLARATIONS
################################################################################
| Path | Lines | Bytes | SHA256 | Declared feature(s) | Classification |
| --- | --- | --- | --- | --- | --- |
| /vendor/etc/permissions/android.hardware.wifi.xml | 20 | 829 | c1315743359a2c41344582074fd6238ce22600c0b28183e2901d9075585ff0a2 | android.hardware.wifi | [VERIFIED] [FEATURE_XML] [WIFI] |
| /vendor/etc/permissions/android.hardware.wifi.direct.xml | 20 | 843 | 88816300cf08cae66fb8346550d3a8cb27b7ac679827ca86ef5840aedc472cd5 | android.hardware.wifi.direct | [VERIFIED] [FEATURE_XML] [WIFI_DIRECT] |
| /vendor/etc/permissions/android.hardware.wifi.passpoint.xml | 20 | 845 | 9996ae086333a6abcc7a1d124280d48e9632f02db8f38ba0bde7c529cbba6d1e | android.hardware.wifi.passpoint | [VERIFIED] [FEATURE_XML] [WIFI_PASSPOINT] |
| /vendor/etc/permissions/android.hardware.bluetooth.xml | 19 | 820 | 08305a972411f4f0068c23e77e00154826f5335a3e12fc1d6042ca130e1db3fd | android.hardware.bluetooth | [VERIFIED] [FEATURE_XML] [BT] |
| /vendor/etc/permissions/android.hardware.bluetooth_le.xml | 19 | 830 | 845f59a4e13fbdda3b48c30a3a0510db45a261af15f31d4925c939dc48d8e3e0 | android.hardware.bluetooth_le | [VERIFIED] [FEATURE_XML] [BT_LE_DECLARED] |
| /vendor/etc/permissions/android.hardware.location.gps.xml | 22 | 942 | 24bcd790812b438782c59424ec0db74b4dff228d8a96919ce39cf428ffc9c903 | android.hardware.location, android.hardware.location.network, android.hardware.location.gps | [VERIFIED] [FEATURE_XML] [GNSS] |
| /vendor/etc/permissions/android.software.ipsec_tunnels.xml | 24 | 875 | 2e0e32d8a1d69171f6d9929b097a0836d85a5639592c076071b6c366232274e0 | android.software.ipsec_tunnels | [VERIFIED] [FEATURE_XML] [RADIO_WFC_CROSS_REF] |
| /vendor/etc/permissions/handheld_core_hardware.xml | 98 | 4884 | df3ca34bf69555997e658db1468050ea1f4e3dead000b5f4726076c61a1301fa | location/network/Bluetooth in core handheld feature set; comments require GPS and Wi-Fi XML when present | [VERIFIED] [CORE_FEATURE_XML] |

Feature declaration conclusion:
- Wi-Fi, Wi-Fi Direct, Wi-Fi Passpoint, Bluetooth, Bluetooth LE, GPS/location,
  and IPsec tunnel software features are declared.
- IPsec tunnels are retained as a radio/WFC/IWLAN cross-reference owned by
  Section 08 for WFC/ePDG/StrongSwan behavior, but the feature XML is captured
  here because it appears in the connectivity declaration surface.
- Bluetooth LE declaration does not prove that the nRF52832 is part of the MTK
  Bluetooth stack. It only declares Android BLE API support at framework level.

################################################################################
# 09.17 — AGPS / SUPL PROFILE BOUNDARY
################################################################################
`/vendor/etc/agps_profiles_conf2.xml` is the primary AGPS/SUPL profile file in
this capture. It contains a current profile named `GOOGLE` using
`supl.google.com` port `7275` with TLS enabled, additional Google non-TLS/IP
profiles, lab profiles, carrier/operator profiles, and a T-Mobile LBS entry.

Classification:
- The file is a [VERIFIED] [AGPS_PROFILE] artifact.
- The endpoints are configuration content, not proof that every endpoint is
  contacted during normal runtime.
- `gps_debug.conf` is a [DEBUG_CONFIG] file with sample/commented override
  settings and is not promoted to active SUPL configuration.
- SUPL certificates under `/vendor/etc/security/cacerts_supl/` and
  `/vendor/etc/security/cacerts_supl/lab/` form a [SUPL_TLS_TRUST_STORE].
- AGPS daemon runtime and `/dev/ttyC2` FD capture prove that the AGPS path can
  cross the radio/modem boundary; detailed CCCI/radio handling is Section 08.

################################################################################
# 09.18 — DEBUG, LOGGING, FACTORY, AND META SURFACES
################################################################################
| Surface | Path / property | Evidence | Classification |
| --- | --- | --- | --- |
| WMT dump directories | /data/vendor/stp_dump; /data/vendor/connsyslog | Created by init_connectivity.rc on post-fs-data. | [VERIFIED] [WMT_DUMP_SURFACE] |
| stp_dump service | /vendor/bin/stp_dump3 | Disabled service controlled by persist.vendor.connsys.coredump.mode. | [VERIFIED] [DUMP_SERVICE] [DISABLED_BY_DEFAULT] |
| Connsys dynamic dump | persist.vendor.connsys.dynamic.dump=0 | Runtime property present and disabled in capture. | [VERIFIED] [DYNAMIC_DUMP_DISABLED] |
| Dedicated connsys logging | ro.vendor.connsys.dedicated.log=1 | Runtime property enables dedicated connsys logging path. | [VERIFIED] [LOGGING_DECLARED] |
| BT debug logger | /data/debuglogger/connsyslog/bthci | Created by /system/etc/init/init.connectivity.rc. | [VERIFIED] [BT_DEBUG_LOG_SURFACE] |
| Wi-Fi tracing | /system/etc/init/wifi-events.rc | Creates `/sys/kernel/debug/tracing/instances/wifi` and enables cfg80211/net events when boot_completed. | [VERIFIED] [WIFI_TRACE_SURFACE] |
| LBS debug | lbs_dbg | Running as system/gps with HIDL/binder debug surfaces. | [VERIFIED] [LBS_DEBUG_SURFACE] |
| META readiness | vendor.connsys.formeta.ready=yes | Live property declares META readiness. | [VERIFIED] [META_READY] |
| Factory/META RC FM declaration | fmradio_drv.ko | Declared in factory/meta init but not live and not located as module file in Section09 closure. | [BSP_GHOST] [FACTORY_META_ONLY] |


################################################################################
# 09.19 — SECURITY AND TRUST BOUNDARY NOTES
################################################################################
Section 09 trust boundaries:
- WMT/connsys is a privileged parent plane for Wi-Fi, Bluetooth, and GNSS child
  transports.
- `wmt_launcher` runs as UID/GID 1000 in a dedicated MTK WMT domain and directly
  opens `/dev/stpwmt`.
- Wi-Fi HAL runs as `wifi` with NET_ADMIN/NET_RAW/SYS_MODULE capabilities
  declared in RC.
- `wpa_supplicant` runs in `hal_wifi_supplicant_default` and maps both AOSP and
  MTK vendor supplicant extension libraries.
- Bluetooth HAL runs as `bluetooth`, opens `/dev/stpbt`, and maps MTK Bluetooth
  vendor/NVRAM libraries.
- GNSS HAL runs as system, `mnld` and `mtk_agpsd` run as gps, and `mtk_agpsd`
  opens `/dev/ttyC2`, crossing into the modem/AGPS path.
- LBS HIDL exposes multiple bridge endpoints across framework, AGPS, WLAN, BT,
  sensor, logger, META, and MNLD surfaces.
- Raw captures contain private SSID/BSSID, Bluetooth address/bonded accessory,
  IP address, and precise location. These remain raw evidence and are redacted
  from the canonical public body.

No Section 09 evidence proves an independent out-of-band nRF BLE stack inside
the MTK WMT plane. The nRF/Wiite sensor/control plane remains separate and is
owned by Section 04 / Section 13.

################################################################################
# 09.20 — NEGATIVE / BSP GHOST / MOVED REGISTER
################################################################################
| Item | Evidence / disposition | Classification |
| --- | --- | --- |
| fmradio_drv.ko | Declared in factory_init.connectivity.rc and meta_init.connectivity.rc on vendor.connsys.driver.ready=yes; not found loaded in /proc/modules and not returned by module-file locate gate. | [BSP_GHOST] [FACTORY_META_DECLARATION] [DO_NOT_PROMOTE_TO_RF_HARDWARE] |
| FM route/log residues | Audio/media/system files contain FM-related surfaces elsewhere; Section 09 found no active FM RF driver in live WMT module set. | [BSP_GHOST] [SECTION07_CROSS_REF] |
| NFC | System permission/library residue such as com.android.nfc_extras appears in broad grep capture; hardware database/user physical truth says NFC absent. | [BSP_GHOST] [NEGATIVE_HARDWARE] [SECTION17] |
| HDMI | BSP/software string residue may exist; no physical HDMI is confirmed and Section 09 does not classify HDMI as connectivity hardware. | [BSP_GHOST] [NEGATIVE_HARDWARE] [SECTION17] |
| IR | Software/string residue must not be promoted to hardware; physical IR/NFC/HDMI absent per project hardware boundary unless separately proven. | [BSP_GHOST] [NEGATIVE_HARDWARE] [SECTION17] |
| Ethernet | Framework binder service `ethernet` is present as Android framework service; no physical Ethernet interface/hardware is proven. | [FRAMEWORK_SERVICE_ONLY] [NEGATIVE_HARDWARE] |
| USB original Section09 block | Original seed included USB gadget VID/PID and MTP/ADB. Current software Section 09 keeps it as cross-reference only; physical/external interface handling belongs to hardware Section 10 and build-property preservation to software Section 15. | [MOVED_OUT_OF_PRIMARY_SCOPE] [CROSS_REFERENCE_ONLY] |
| Precise Wi-Fi/Bluetooth/GNSS runtime identifiers | SSID/BSSID, bonded device identifiers, Bluetooth address, and last-known GPS coordinates appear in raw capture. Canonical body redacts these while preserving runtime state, service, interface, and stack conclusions. | [PRIVATE_RUNTIME_CAPTURE] [PUBLIC_REDACTION] |


################################################################################
# 09.21 — CROSS-SECTION BOUNDARIES
################################################################################
| Section | Boundary |
| --- | --- |
| Section 01 | Consumes init-service and RC import/service graph identities. Section 09 owns only connectivity-specific RC meaning. |
| Section 02 | Consumes HAL/VINTF interface inventory. Section 09 owns connectivity behavior and runtime stack closure. |
| Section 04 | Owns Android sensor stack, SCP, nRF bridge, Wiite/nRF sensor/control plane. Section 09 does not collapse nRF into MTK Bluetooth. |
| Section 07 | Owns audio policy/routes including Bluetooth audio. Section 09 owns Bluetooth transport/HCI/control stack. |
| Section 08 | Owns radio, CCCI, IMS, WFC/ePDG, IWLAN, and cellular packet interfaces. Section 09 cross-references `/dev/ttyC2`, IPsec tunnels, IWLAN properties, and ccmni routes only where connectivity surfaces overlap. |
| Section 10 | Consumes module/blob inventory and vendor library catalog. Section 09 identifies required connectivity modules/firmware but is not the full blob inventory. |
| Section 11 | Consumes firmware blob catalog and hardware identity map. Section 09 provides connectivity firmware hashes and roles. |
| Section 13 | Owns ODM/Wiite control layer and nRF framework integration. Section 09 records only direct connectivity overlap such as com.wiite.home enabling framework Bluetooth. |
| Section 15 | Consumes build-property preservation and clean-ROM carry/replacement decisions. Section 09 provides source truth for those decisions. |
| Section 17 | Consumes BSP ghosts and software negative register entries: FM driver declaration, NFC residue, HDMI/IR/Ethernet software-only surfaces, and USB movement register. |
| Section 18 | Consumes final ROM verification gates: WMT module chain, firmware presence, interface bring-up, HAL registration, and GNSS/BT/Wi-Fi smoke tests. |


################################################################################
# 09.22 — CLEAN-ROM / REPLACEMENT BOUNDARY
################################################################################
Section 09 is not a build recipe. The following are database-preservation rules
for downstream build sections:

Must preserve for first functional parity:
- `wmt_drv.ko`, `wlan_drv_gen4m.ko`, `wmt_chrdev_wifi.ko`, `bt_drv.ko`,
  `gps_drv.ko`.
- `/vendor/firmware/WMT_SOC.cfg`
- `/vendor/firmware/BT_FW.cfg`
- `/vendor/firmware/MNL.bin`
- `/vendor/firmware/WIFI_RAM_CODE_soc1_0_1_1.bin`
- `/vendor/firmware/soc1_0_patch_mcu_1_1_hdr.bin`
- `/vendor/firmware/soc1_0_ram_mcu_1_1_hdr.bin`
- `/vendor/firmware/soc1_0_ram_wifi_1_1_hdr.bin`
- `/vendor/firmware/soc1_0_ram_bt_1_1_hdr.bin`
- Connectivity RC files listed in Section 09.6, or functionally equivalent
  clean replacements with identical load ordering.
- Device-node labels/permissions for WMT, Wi-Fi, BT, GPS, GPS EMI, and firmware
  log nodes.
- Wi-Fi, Bluetooth, Bluetooth LE, GPS/location, Wi-Fi Direct, and Wi-Fi
  Passpoint feature XMLs unless deliberately reducing feature exposure.
- AGPS profile and SUPL trust store if preserving stock GNSS assistance behavior.

Safe to demote or strip only after downstream verification:
- Factory/META-only `fmradio_drv.ko` declarations if no module/file/physical FM
  path is preserved.
- Stopped/lazy `hostapd` only if SoftAP/tethering is intentionally not supported.
- `met.ko` if all profiling/debug dependence is proven absent in clean ROM.
- BT/WMT/GNSS debug/logging surfaces if replacement policy intentionally removes
  diagnostic paths and SELinux is adjusted accordingly.

Do not strip blindly:
- WMT firmware/config files.
- The WMT parent driver or any child driver.
- `wlan_assistant`, `wmt_launcher`, `mnld`, `mtk_agpsd`, or `lbs_hidl_service`
  unless a clean replacement is designed and tested.

################################################################################
# 09.23 — SECTION CLOSURE MATRIX
################################################################################
| Scope item | State | Closure statement |
| --- | --- | --- |
| WMT/connsys parent | Closed | `wmt_drv.ko` live, hashed, load order captured, parent process/node captured. |
| Wi-Fi driver/HAL/supplicant | Closed | gen4m module chain, Wi-Fi HAL, supplicant, wificond, wlan_assistant, interface state, firmware/config hashes captured. |
| SoftAP/hostapd | Closed | Declared disabled/oneshot; stopped at capture; `ap0` exists down; classification does not block closure. |
| Bluetooth | Closed | BT driver/HAL, `/dev/stpbt`, firmware/config, feature XML, OFF and ON framework states captured. |
| Bluetooth LE | Closed | Feature XML and framework GATT surfaces captured; not collapsed into nRF plane. |
| GNSS/GPS | Closed | GPS driver, MNL.bin, GNSS HAL, `mnld`, AGPS, LBS, feature XML, raw location redaction boundary captured. |
| AGPS/SUPL | Closed | AGPS daemon, profile XML, SUPL trust store, modem `/dev/ttyC2` FD captured. |
| WMT device nodes | Closed | Targeted nodes with owner/label/major/minor captured. |
| Firmware SHA256 | Closed | All scoped WMT/Wi-Fi/BT/GNSS firmware/config artifacts hashed. |
| Feature XML | Closed | Wi-Fi, Wi-Fi Direct, Passpoint, Bluetooth, BLE, GPS/location, IPsec tunnel declarations hashed/body captured. |
| Init RC | Closed | Driver RC, WMT RC, Wi-Fi/BT/GNSS HAL RC, hostapd, wificond, Wi-Fi trace, AGPS/LBS RC captured. |
| Process FD/maps | Closed | Corrected process FD/maps captured for parent and child daemons/HALs. |
| BSP ghosts | Closed | FM/NFC/HDMI/IR/Ethernet/USB scope movements classified. |
| Privacy-sensitive runtime data | Closed | SSID/BSSID/IP/BT address/bonded device/GPS coordinates retained in raw capture but redacted from public body. |


################################################################################
# 09.24 — FINAL SECTION 09 DATABASE CONCLUSION
################################################################################
The C17S connectivity software stack is fully represented as a layered
MTK WMT/connsys architecture. `wmt_drv.ko` is the parent kernel module and the
parent control plane for Wi-Fi, Bluetooth, and GNSS/GPS. The active child driver
set is `wlan_drv_gen4m.ko`, `wmt_chrdev_wifi.ko`, `bt_drv.ko`, and `gps_drv.ko`.
The user-space parent is `wmt_launcher`, which runs as system in the MTK WMT
SELinux domain and opens `/dev/stpwmt`.

Wi-Fi is live through gen4m, `vendor.wifi_hal_legacy`, MTK/AOSP supplicant,
`wificond`, `wlan_assistant`, and interfaces `wlan0`, `p2p0`, and `ap0`.
Bluetooth is live as an MTK HCI HAL stack over `/dev/stpbt`, with framework
Bluetooth dynamically OFF/ON across capture but HAL service continuously present.
GNSS is live through MTK GNSS HAL, `mnld`, `mtk_agpsd`, LBS HIDL, MNL firmware,
AGPS profile configuration, and SUPL trust anchors. AGPS crosses into the modem
side through `/dev/ttyC2`, which is a Section 08 cross-boundary, not a separate
Section 09 radio ownership claim.

All scoped kernel modules, init RC files, WMT/child device nodes, runtime
services, HAL/HIDL interfaces, Binder surfaces, firmware/config files, feature
XMLs, Wi-Fi/BT/GNSS daemon maps, and AGPS/SUPL artifacts are now captured and
classified. Original seed USB material, FM declarations, NFC/HDMI/IR/Ethernet
residues, and private runtime identifiers are preserved in the database with the
correct classification instead of being silently removed.

Section 09 is closed to software digital-twin grade.

################################################################################
# END SECTION 09 — CONNECTIVITY SOFTWARE STACK — WI-FI, BLUETOOTH, GNSS & WMT
################################################################################
