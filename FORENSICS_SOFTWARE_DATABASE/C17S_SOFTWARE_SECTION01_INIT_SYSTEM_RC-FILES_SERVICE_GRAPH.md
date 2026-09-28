################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 01 — INIT SYSTEM, RC FILES & SERVICE GRAPH
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 01 — INIT AUTHORITY MAP
# Date        : 2026-06-14 live source-pack and runtime integration pass
################################################################################

################################################################################
# SCOPE
################################################################################

This section documents the boot-init control plane for the C17S software digital twin.
It is the authority section for root init files, vendor init RC files, system init RC
files, ueventd files, service declarations, init triggers, action commands, device
permission mutation, class membership, runtime init.svc state, and the handoff from
init/system_server into the ODM UID-1000 Wiite control plane.

This section covers:
- Root init and ueventd files from the current live source pack.
- Vendor `/vendor/etc/init` flat and `hw/` RC inventory.
- System `/system/etc/init` RC inventory.
- Product init absence.
- MT6762 runtime identity versus MT6765 BSP/software identity.
- `init.mt6762.rc` / `init.mt6765.rc` byte-for-byte identity.
- Complete import graph and imported-target resolution state.
- Complete service declaration catalog.
- Complete trigger/action-command catalog.
- Runtime service state from `init.svc.*` properties.
- Live process graph for init-started services and Wiite system-app processes.
- Wiite/nRF sysfs and `/dev/ttyS1` permission mutation boundary.
- Factory/meta/diagnostic init profiles shipped in the production vendor tree.
- Radio, IMS, WFC, ePDG, RIL, CCCI, WMT, GNSS, Wi-Fi, Bluetooth, camera, sensor,
  security, and USB init-service boundaries.
- Clean-ROM carry / replace / strip / harden decisions for Section 15 and Section 18.

This section does not decompile APKs, native libraries, or firmware. APK behavior,
native reverse engineering, SELinux policy, and binary protocol analysis are carried
forward to Sections 13, 14, and 16. This section records the init-layer facts that
make those later control paths reachable.

################################################################################
# SOURCE AUTHORITY
################################################################################

TIER 1 : Live rooted C17S runtime snapshot
         - `/data/local/tmp/section01_runtime_snapshot.txt` pulled from the device.
         - Authority for `getprop`, `init.svc.*`, `ps -A -Z`, binder service list,
           live `/dev/ttyS1`, live Wiite sysfs nodes, and open UART file descriptor.

TIER 2 : Live source pack
         - `/data/local/tmp/section01_init_pack.tar.gz` pulled from the device.
         - Authority for root init files, root/vendor ueventd files, vendor init RC
           files, system init RC files, file hashes, line counts, services, imports,
           triggers, and action commands.

TIER 3 : Prior original software database
         - Original Section 01 claims are retained and resolved against the new source
           pack and runtime snapshot.
         - No original claim is deleted without a status transition.

TIER 4 : Hardware digital twin cross-reference
         - Used only to prevent software declarations from being promoted to physical
           hardware truth.

FINAL  : Beright / Albert acceptance after live re-verification.

Source artifact hashes:
- `section01_init_pack.tar.gz` SHA256: `bf375777e43cef2018a93bd339d75c3f41904acbe5974c142cd11b3b115ee458`
- `section01_runtime_snapshot.txt` SHA256: `243033d8944bb4c07e155a78b869439c584edabc3a9b1e3eb0dfe80cbc12d87f`

################################################################################
# RULES
################################################################################

No stale values.
No borrowed values from similar MTK devices.
No assumption promoted to software truth.
No software declaration promoted to hardware truth.
No device node promoted to active runtime use without live runtime/process evidence.
No package/process control path promoted beyond the layer proven in this section.
No original database entry is deleted; every original entry is verified, expanded,
corrected, moved, demoted, left open, or disproved by stronger evidence.
Layer-specific truths remain layer-specific.

Classification vocabulary used in this section:

[VERIFIED]                    Proven by the source pack, runtime snapshot, or both.
[ACTIVE_RUNTIME]              Present and active in the live runtime snapshot.
[FILE_AUTHORITY]              Present in the live source pack.
[DECLARED]                    Present in RC source but not necessarily active.
[DECLARED_IMPORT]             Imported by an RC file.
[ABSENT_IN_SOURCE_PACK]       Referenced path not present in the captured source pack.
[BSP_GHOST]                   Declared by platform/BSP but not matched to active hardware truth here.
[FACTORY_META_DIAGNOSTIC]     Factory/meta/diagnostic boot profile or service.
[SECURITY_CRITICAL]           Grants broad capability, hardware access, radio access, root/system control,
                              writable sysfs, writable char device, or update/debug surface.
[ODM_CONTROL_BOUNDARY]        Boundary where init exposes a route used by Wiite/ODM software.
[UART_BRIDGE]                 Serial bridge route between Android userspace and external control/sensor plane.
[KEEP]                        Original database entry proven correct.
[EXPAND]                      Original entry proven correct but expanded with additional evidence.
[CORRECT]                     Original entry real but corrected by layer/path/status.
[MOVE]                        Belongs primarily to a later section.
[OPEN_LEAD]                   Device-derived but still requires later binary/runtime proof.
[DISPROVED]                   Contradicted by stronger evidence; retained with proof.

################################################################################
# 1.1 — CAPTURE IDENTITY
################################################################################

ro.vendor.rc                    : /vendor/etc/init/hw/ [VERIFIED]
ro.hardware                     : mt6762 [VERIFIED]
ro.boot.hardware                : mt6762 [VERIFIED]
ro.board.platform               : mt6765 [VERIFIED]
ro.vendor.mediatek.platform     : MT6765 [VERIFIED]
ro.build.type                   : user [VERIFIED]
ro.boot.atm                     : disabled [VERIFIED]

Interpretation:
- The runtime hardware property layer identifies `mt6762`.
- The board/BSP/vendor software layer identifies `mt6765` / `MT6765`.
- `${ro.vendor.rc}` resolves to `/vendor/etc/init/hw/`.
- Both MT6762 and MT6765 strings are valid only in their respective layers.

################################################################################
# 1.2 — SOURCE FILE INVENTORY SUMMARY
################################################################################

| Source tree | Count | Interpretation |
|---|---:|---|
| Root init/ueventd files | 8 | Rootfs init and ueventd source pack files. |
| Vendor flat RC files | 68 | `/vendor/etc/init/*.rc` excluding `hw/`. |
| Vendor `hw/` RC files | 17 | `/vendor/etc/init/hw/*.rc`. |
| Vendor total RC files | 85 | Matches original 85-file vendor inventory. |
| System init RC files | 62 | `/system/etc/init` recursive source-pack inventory. |
| Product init RC files | 0 | Product init absent in this capture. |
| Total RC files parsed | 155 | Root + vendor + system + product. |

Parser totals:
- Service declarations parsed: 211
- Import statements parsed: 96
- Action triggers parsed: 508
- Action commands parsed: 4506

################################################################################
# 1.3 — VENDOR RC INVENTORY
################################################################################

Vendor flat RC files, 68 total:

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `vendor/init/android.hardware.audio@5.0-service-mediatek.rc` | 788 | 15 | `b162ea7de962ced35584459e49daba6bebcbaa0d0ad510bc546ac9c5f5b9b26e` |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc` | 572 | 15 | `3a3e1e7e315f73a9c1f116a3cfac9351cfaaf7f5fa7ea59221df097a1622d0b9` |
| `vendor/init/android.hardware.cas@1.1-service.rc` | 188 | 6 | `c737804ff1e0e6b24c87cc8de688e10f397dcaf0139a8a5c8c80053365fadede` |
| `vendor/init/android.hardware.configstore@1.1-service.rc` | 144 | 4 | `c051342d9c9bcadf188c45a11fc698141a8964ffbd568c0db9ff0911bfb41e11` |
| `vendor/init/android.hardware.drm@1.0-service.rc` | 311 | 8 | `6e1aa30187a88a4420c4128b96c1278fb9337036b38415b40d8680f61de2f230` |
| `vendor/init/android.hardware.drm@1.2-service.clearkey.rc` | 580 | 12 | `70166232f132511be6cd8c6f5f895896887edf1209a3f8cc6becfe3a40a92023` |
| `vendor/init/android.hardware.drm@1.2-service.widevine.rc` | 923 | 24 | `b32c921223523d2054c571126c4aa1af3297baf54efe8c181842427db2d4b54f` |
| `vendor/init/android.hardware.gatekeeper@1.0-service.rc` | 132 | 4 | `ac31b40c9feabca1c449a5b39ff8e1427fa88c9c911f6a4f96f8674b656d9e40` |
| `vendor/init/android.hardware.gnss@2.0-service-mediatek.rc` | 130 | 4 | `4b0c281a449f2203f7b9299baea6cd2743e147b4e8c85d8b36944e0e57f570ee` |
| `vendor/init/android.hardware.graphics.allocator@2.0-service.rc` | 293 | 7 | `94721a0986d8299393254e3a13719b14dcb1d673274f960112d432a16d6cb226` |
| `vendor/init/android.hardware.graphics.composer@2.1-service.rc` | 270 | 7 | `ebf95c6a24b082e199929a10f538fb555aaeadfd163e3dc9587c21e7baec7bbe` |
| `vendor/init/android.hardware.health@2.0-service.rc` | 170 | 6 | `bbacdc0e8f564d5a538dfd2842e67bb6dd702eb21d7b7ae37ad763274e4dab5d` |
| `vendor/init/android.hardware.keymaster@4.0-service.rc` | 143 | 4 | `704311ad47af7ae9a8e16c432aedf72158768205bf020f9cbe1c216a3ee88234` |
| `vendor/init/android.hardware.light@2.0-service-mediatek.rc` | 253 | 8 | `37b0818814bda21e5c5c3d9c6ebcdb8cf386c3801ac832a1370114d72b3100c1` |
| `vendor/init/android.hardware.media.omx@1.0-service.rc` | 283 | 6 | `e77dc7e908342cc8828641a88e6583667a023bf1f020c40d6d38ab5788b03f1d` |
| `vendor/init/android.hardware.memtrack@1.0-service.rc` | 132 | 4 | `d2c696bd2aed77b119d307ff6e6287f07e0ac4875408afc588c4b1bc7d2bde27` |
| `vendor/init/android.hardware.secure_element@1.0-service-mediatek.rc` | 170 | 4 | `dde7b691864c8670e9e18a35c0ffb76dff97b52fd6d6cfcde90eebe29a63e066` |
| `vendor/init/android.hardware.sensors@2.0-service-mediatek.rc` | 164 | 5 | `23cddad6c36a95f0d719872febf35b1404ea5d3358e2b2c01ba9b3f2fbd6c165` |
| `vendor/init/android.hardware.thermal@1.0-service.rc` | 130 | 4 | `3c9c698550dd54bad4a7392cddf1bd7670004b1fc82e2b8092efc71d9388b059` |
| `vendor/init/android.hardware.usb@1.1-service-mediatek.rc` | 995 | 19 | `00eb71a0d4240593d4626fd3f051c5a0bfca290895af7e458f8503406bc94011` |
| `vendor/init/android.hardware.vibrator@1.0-service.rc` | 128 | 4 | `d3227ec6d5b16308d39d8e87613316aa92c25c6a962de5148a086b8a1f4fea23` |
| `vendor/init/android.hardware.wifi@1.0-service-lazy-mediatek.rc` | 267 | 8 | `4cb024b3b2401632fb24ca63b5610379c585a9a901c660aa47f875c300139a9f` |
| `vendor/init/atcid.rc` | 492 | 18 | `1d41a7f2d38d7de746c4e92864a1293bfc8cd45c638fe6234203b3a556b9aef5` |
| `vendor/init/audiocmdservice_atci.rc` | 181 | 8 | `4c38c958052e71915cb5e35037faf3b44c13a5332ecf65f2975d5f48c3621750` |
| `vendor/init/bootperf.rc` | 264 | 7 | `c20eec842db47efa080f710b1a6f8c3d17e9a86fa680c90fc52b6f2e3b7e9a37` |
| `vendor/init/camerahalserver.rc` | 272 | 7 | `2f616b01dc163b4b8c72819d0e4d7fa7649fa47b9ef91f7165ced33659464c77` |
| `vendor/init/em_hidl_user.rc` | 190 | 7 | `d707bb6f984953f6f5fd954ed6e85dd6a30eed2f848e1e8459883247e401d7eb` |
| `vendor/init/fuelgauged_init.rc` | 90 | 4 | `c6ed285861915bf4e097219a519d2bbf0577fd298e76895e8353e551f93163a3` |
| `vendor/init/fuelgauged_nvram_init.rc` | 115 | 6 | `2bc23468174e2bea32e54c6a382c4f97cce17ca6255b83756e309c66a12514b8` |
| `vendor/init/gsm0710muxd.rc` | 173 | 7 | `98cc2496b883e337bcb9e7bc9ceac8a606e2317cc5da3ec8a9a9474353201094` |
| `vendor/init/hostapd.android.rc` | 665 | 22 | `27dad27e4491bb32f4043e1fd914c98b6d74886be9057d5ab04e4d5ad2d73529` |
| `vendor/init/init.bip.rc` | 574 | 28 | `6ed6befc0580e34c7eab24c97c3c3a25d14c37cea96d189fbc277fac86755cf6` |
| `vendor/init/init.bt_drv.rc` | 97 | 3 | `2eae40c94cc3e63264eda963b28488005fe641d0e812f0e2397f4a5ed068eb93` |
| `vendor/init/init.cccifsd.rc` | 682 | 35 | `b5810a6bbfa691310b13345d7e842542c5a5a8f3ebee9052bbfdf33bdafa6ab0` |
| `vendor/init/init.cccimdinit.rc` | 767 | 32 | `81c46acff6f8b2235a41e637b37fc335c63d629bac93c76814172c8f62041cbf` |
| `vendor/init/init.cccirpcd.rc` | 118 | 8 | `946fd9b36b3c3d26036c923388025b06ca86364491c5ffbba865eb2fd16221de` |
| `vendor/init/init.gps_drv.rc` | 100 | 4 | `afbc9f03f719f29a2b734d20449e2625f30a2b048c43b04e75c655b1cbe897e2` |
| `vendor/init/init.md_apps.rc` | 293 | 6 | `e62a200fc7d5d592206735b0d6995518523bae59c9a7b1c9fc34abad942eaaec` |
| `vendor/init/init.thermal.rc` | 83 | 3 | `8cf87bfd8728c85f591c789bb1f54fd97c2c3fcd51e0ad180a9b99fee169cf36` |
| `vendor/init/init.thermal_manager.rc` | 110 | 5 | `d739fb217b0cfe5b951920efb36d236121f9db7b0c96210b4d3bf4cd482e12d2` |
| `vendor/init/init.thermalloadalgod.rc` | 102 | 4 | `c4b574f224b859fb9c0b59e7e31666af58b469890fe15f4da0e7ba36953a27e7` |
| `vendor/init/init.volte_imcb.rc` | 447 | 17 | `2b1a08e2e5e0eab9a78b62d366312e7c750af535083cec1a114b0b42ef1cf54e` |
| `vendor/init/init.volte_imsm_93.rc` | 1393 | 60 | `ba22f7dfc06ec1f45529a2ee8ffeadb61a1092fcae1d1b08ef603f6e4cdbcf51` |
| `vendor/init/init.volte_md_status.rc` | 247 | 17 | `21b8a8728d2291f216abe397799f936d8a2b2c127d9e6908b336dc86c0505bc2` |
| `vendor/init/init.volte_stack.rc` | 233 | 10 | `726bb222414b7af15ffa55148848e16c9dd5cab99bb2dbaa6894175b1b658f23` |
| `vendor/init/init.volte_ua.rc` | 239 | 10 | `1bd8bab39fa9c5c22ce035c6174b4dbd257d66e948279e2cf5463041df9a3266` |
| `vendor/init/init.vtservice_hidl.rc` | 270 | 8 | `1af90b109d4f59545b04ad02a2876abdcef4e02bf4bded1d2ba113b4fdb675c1` |
| `vendor/init/init.wfca.rc` | 263 | 15 | `50dd34078681cc2f5c5287890bd32da1d45583348c7097e38a3576af022ff29c` |
| `vendor/init/init.wlan_drv.rc` | 222 | 6 | `ebc138d43457d785ddab62a9365edb714ac0ed84334886cd6bea5a7391d161bd` |
| `vendor/init/init.wmt_drv.rc` | 50 | 2 | `c99409267d7255e6c3435e88269f6956d0452a6d1cd383fa02c5bdd07e217ed4` |
| `vendor/init/init.wod.rc` | 970 | 32 | `0ae0b156bd52250961640bef4bffd4beebd7be41895c1c2d8aafa92f4b0ffb94` |
| `vendor/init/init_connectivity.rc` | 1155 | 43 | `d39faaec6a7e9075fbb4c52d7edb8d8d550dc6212ad57c65a0db9ea12da9c2c5` |
| `vendor/init/ipsec_mon.rc` | 182 | 10 | `6cca4735245bbcda52f8853f4d4a8e299cfdb334479d450ceb94b7b4f7a4e4d1` |
| `vendor/init/lbs_hidl_service.rc` | 139 | 4 | `e28158512d8c365f7682c90970b89607b8a8fb2fde38149dcd007495f097f11f` |
| `vendor/init/mtk_agpsd_p.rc` | 302 | 11 | `bd967073fa77e9f9be471ffdf796079f7c524402380409fc014466c710b8246c` |
| `vendor/init/mtkrild.rc` | 691 | 21 | `8c1767987ae47dd9f10f27add33dac0aa79c03e2fbcb8f80a82a9f013afeeb67` |
| `vendor/init/muxreport.rc` | 211 | 9 | `967c97a8687da1a69dfe5338dba2d379885262508e7f9fb136d2fc40fe4d7171` |
| `vendor/init/netdagent.rc` | 346 | 12 | `da4bd02eef0f49b95104f41ce0dee8c8739110df69c4382a081dc0753e34c956` |
| `vendor/init/nvram_daemon.rc` | 136 | 4 | `a6cd1a716873790eb49e1abaf1b5f9ebf91509abc9cfdd2c68abf451deaae23a` |
| `vendor/init/vendor.mediatek.hardware.gpu@1.0-service.rc` | 128 | 4 | `ffe134802c5ec0fb61021518f904803af21457d9b123a2f00cb4f9107cd01c7d` |
| `vendor/init/vendor.mediatek.hardware.mms@1.3-service.rc` | 128 | 3 | `5c5e1074325a38728373f9a6f77e8ffa480edba2d74a48b766f946bf358dd78e` |
| `vendor/init/vendor.mediatek.hardware.mtkcodecservice@1.1-service.rc` | 267 | 7 | `97115d89a73a26280186d0b3fd0cc81112e65e63332e769f399d9e8c8f8bc3f6` |
| `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc` | 12258 | 220 | `3fc691e5c0dc2ea130e8f02d7e1f5522ad9153ba947a4d3d01b91e7df83133e9` |
| `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc` | 305 | 7 | `adb50f2d600b9d77db57be9ac7f84d5c843d58b9e20925bd9e25574475e0bafd` |
| `vendor/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc` | 153 | 3 | `b75822d2a39f1bf11d1086eca6fdc120322232c85f6914921817810e0430ece6` |
| `vendor/init/vendor.mediatek.hardware.pq@2.2-service.rc` | 127 | 4 | `1de61b823621c11eedf426fffcd13fe7fdbbf1166ab13851a1ec074d876c2a5d` |
| `vendor/init/vndservicemanager.rc` | 199 | 6 | `73067e8623d55dd3517edefb3cb520315fdb7edcc193aec5c3f2785c81b7f350` |
| `vendor/init/wlan_assistant.rc` | 104 | 3 | `1a7b5b331c70f79a44a7875fbf6bb68d50ee0bd2237f0babf420917e7b4f14ef` |

Vendor `hw/` RC files, 17 total:

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `vendor/init/hw/factory_init.connectivity.rc` | 1943 | 93 | `7ffec7a83549f0795738446020ccbe858f5f8ae852deaedfc461dada059e1f06` |
| `vendor/init/hw/factory_init.project.rc` | 937 | 37 | `fec77385643179163bdf94c05f6b87c483a4922f8b5c5c9bb74816fd17db4df1` |
| `vendor/init/hw/factory_init.rc` | 27653 | 831 | `7b10e8a5235b96d4c24170109fa3aa7ba2c258967cef5865c7ddf0345f58dd76` |
| `vendor/init/hw/init.aee.rc` | 47 | 2 | `09475098dbb9f8f61c1fd2747b3a87371b9b452beec6f7118c126d110352a3dd` |
| `vendor/init/hw/init.ago.rc` | 548 | 18 | `939bbe1730e9f24b88aba4c0ddb6dc7e8564f7de72470b453077997ca39404cc` |
| `vendor/init/hw/init.connectivity.rc` | 1669 | 76 | `1a1472c528c9f974bfb10a2b095786ba0ef11432d2f9f75c01f47af41e69a7a3` |
| `vendor/init/hw/init.modem.rc` | 1938 | 69 | `f587b9a3f7900de9828cc19ac5774c8195a96ed541ce33788560437ab0c03cbf` |
| `vendor/init/hw/init.mt6762.rc` | 39950 | 1115 | `a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9` |
| `vendor/init/hw/init.mt6765.rc` | 39950 | 1115 | `a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9` |
| `vendor/init/hw/init.mt6765.usb.rc` | 43547 | 888 | `e4eb8039f5238c17a49ca3c38c62feb8c6d66eb426f07793595e8ffa66b934d0` |
| `vendor/init/hw/init.project.rc` | 2083 | 73 | `c368b4617c97d82ddee4cb447810a07a9cf8dd6c472de5ffb958da75b016d166` |
| `vendor/init/hw/init.sensor_1_0.rc` | 16678 | 322 | `e68cf5022f6a52cde0d297bbc383165ac4eac9d90fbb9eef89b566f4739181dd` |
| `vendor/init/hw/meta_init.connectivity.rc` | 1766 | 88 | `7fc55cfc7fbca1a1af8e901b456e2852c7f2313da5243d9389bb4855b2529a7e` |
| `vendor/init/hw/meta_init.modem.rc` | 1335 | 54 | `0a7e4330aa8e226c0fc5b1a60ff2a69b942c9161b5d415f13743b4d58a8ab579` |
| `vendor/init/hw/meta_init.project.rc` | 197 | 14 | `2777f0f39ef29d2cd5350820d8a8e2d5c0b361fd8aa31b241a80fdca000c991d` |
| `vendor/init/hw/meta_init.rc` | 23214 | 660 | `6d2f16a17f0c45719872deb65c167b932147207bf5479c9a09b299977658f6c5` |
| `vendor/init/hw/multi_init.rc` | 1893 | 34 | `f0c23e41bea89bcdeea72002e675d3c4ee62024022fd288b43f86ed05dcff01c` |

################################################################################
# 1.4 — ROOT, SYSTEM, AND PRODUCT INIT INVENTORY
################################################################################

Root init and ueventd files:

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `root/init.environ.rc` | 2335 | 17 | `76514d83e54495b911932699dfa775f54ba7dadbac945074fcd0b6f7804ad420` |
| `root/init.rc` | 33780 | 877 | `e87ae6ccbc537cded9af4cb7790b17dfaa01188217ebca18f52752bb7a9fc21b` |
| `root/init.usb.configfs.rc` | 7690 | 140 | `1384154db54fe5a8b3fb62f9c8e9c6732c379066c941364c25d853ac55aa994b` |
| `root/init.usb.rc` | 5649 | 134 | `4dde3aa1f087c79abdc960af50897a60ae794e742c6f1086e1c7a3d252eee2c6` |
| `root/init.zygote32.rc` | 563 | 15 | `99b41ae737cfc340d672f0bbd2c9dea3ed1ed73a3187cd61ab2c3fef0f3f90e3` |
| `root/init.zygote64_32.rc` | 981 | 25 | `dffaf27b5859fc37d2623bbd65cc7d5b7a7fc81eefc9ffeade1e784bd92cfcc0` |
| `root/ueventd.rc` | 2608 | 69 | `fa500e62a3601815b5416e63d946b1f25fea53630e4d14c3c982070d4590cd5f` |
| `root/vendor_ueventd.rc` | 5695 | 137 | `bfa2949e39e1673a5ed3b5cb021a8d497529cfc744b1255335d48b0267c1fa23` |

System init files, recursive:

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `system/init/android.hidl.allocator@1.0-service.rc` | 117 | 4 | `a56e76ce0cb3933dcda463c7b401aee5bf15c78d16279edc98db7bda23bf4ec9` |
| `system/init/android.system.suspend@1.0-service.rc` | 166 | 5 | `c7164caf27ccdc9df71555d54006d087be131ef0afa5bd380b42bb9c5726eb67` |
| `system/init/apexd.rc` | 237 | 13 | `4c3a1493ff698ba54e87b1b5c48b87e4acb08fc3a10b92df3c6102d4a4d9805c` |
| `system/init/art_apex_boot_integrity.rc` | 813 | 21 | `f57734e8ce776711767f118221ea4e420746488c5508e08579b4d76f5ac06e3f` |
| `system/init/ashmemd.rc` | 84 | 4 | `bcbfcbb7e338ebb68200e7d74bd497a9b1e0d4e49a9972fd2693c43a78df4996` |
| `system/init/atrace.rc` | 13164 | 211 | `74b3b16c2eb2b1edb71cb8f4808823e58c10857849850dbed2f4b75c4170ba23` |
| `system/init/audioserver.rc` | 941 | 23 | `47d3ff679adfd74959cde93642b87bdcdc344cf8e8d96f8ce6aadfef6b4d83f2` |
| `system/init/batterywarning.rc` | 194 | 9 | `06a9dbee22ece78c156f83068c69ca63fbfde8548b212df8e1a7ade682388405` |
| `system/init/blank_screen.rc` | 113 | 5 | `1e4d509471f2f88e46a15c1247bb0e5d8a4732b2dcc066eaa09aa70019e525be` |
| `system/init/bootlogoupdater.rc` | 135 | 5 | `7e58b0c82114ac091f5e1b9aa69a3c5fb090e69cac2acf2e98da4a9071bf9d0d` |
| `system/init/bootstat.rc` | 4386 | 83 | `20b8bfc02221876fa544defb393f8af14a597f3d64e3c94516a89007a3b4ba1c` |
| `system/init/bpfloader.rc` | 406 | 9 | `259be2c42427fb406b725db675d1ea9371fa33e4a88e56f071c003dbdef42851` |
| `system/init/camerapostalgo.rc` | 155 | 5 | `2cb63d4f5d688524e42a13c1620104786b6ddbb8aef562500a8653d60411af0f` |
| `system/init/cameraserver.rc` | 229 | 7 | `d815c248c165636d01a4a690ab67529e5ceb4ddce57c59e51a911d1b50fec4cc` |
| `system/init/drmserver.rc` | 231 | 6 | `02dfda5ae7294658929eccbe49ed1557ba9f8d567aaf9a117f8332e187576a03` |
| `system/init/dumpstate.rc` | 799 | 25 | `4c21c674375e424706c281721e078089237b2f2d5c0d84ce97f9f1e0fcfc6364` |
| `system/init/flags_health_check.rc` | 484 | 11 | `82ab57484e6aa15b5c76994dec159cd566e2e6508a5374518a6c4898bfeb175e` |
| `system/init/gatekeeperd.rc` | 152 | 4 | `59b31eb1590b763c68952251c545f181184ac6afc3173bd0410bad9b15bba547` |
| `system/init/gpuservice.rc` | 90 | 4 | `225d1e72e81e44d654adda9b684aa3a41d9cb649c730a8d55f902787a83bbf8e` |
| `system/init/gsid.rc` | 356 | 15 | `8708238068e64e89149b4f913a1a22b95d1d45ce63da340ae897aed0dcb98c9d` |
| `system/init/heapprofd.rc` | 1490 | 36 | `4b7dedfd9c2c59f59e51f119e1283d6f1c2f67bd13f214d636a3b35b98da8aa9` |
| `system/init/hw/vendor_init_as_system.rc` | 8900 | 219 | `ea6ab95ba5f5e82ff4f7b6f08615217f2c1dbe7285ea72ed846b42b681d7aa4f` |
| `system/init/hwservicemanager.rc` | 367 | 12 | `0c33428da6bbd8033b1c5db78aa9bc0ec8420e2c037a0118984737c4bc623284` |
| `system/init/idmap2d.rc` | 84 | 4 | `4dc195022f473d4b93770b441997d7086dfa07de82f73a713167bf6d62596779` |
| `system/init/incidentd.rc` | 851 | 23 | `a989c870659f49b09f9fa618513cab952878326142331af9b7d2a2e59629c7ca` |
| `system/init/init.connectivity.rc` | 319 | 18 | `52d5081c94e39f4bffdd4856f990d5b4e6dc93c992be82ce9ead3d52865120c4` |
| `system/init/init.thermald.rc` | 96 | 4 | `1ca63c57e501e59c7e6c624c22cd69539fe56568e14da56c64b4abd9a3792083` |
| `system/init/init.vtservice.rc` | 210 | 7 | `1d7653b24a93aa340e1b7c30cf86a9732b8a732e50cd923279af949f331b4808` |
| `system/init/installd.rc` | 4674 | 103 | `4d81a436ef00b40621ea0b2e4138064e4ef669bd0b27b0cb4ccebdc7720cf91a` |
| `system/init/iorapd.rc` | 1104 | 29 | `1b406e8acdee7292c8d91ec07dbe1f825351cf30abac411a179b5689d89fda3c` |
| `system/init/keystore.rc` | 172 | 5 | `229b527defae1ef40d7b97436662f3468b366ddc2a2b846256c901b5bf62e643` |
| `system/init/kpoc_charger.rc` | 174 | 6 | `66fd1bcc9714df014be3f5b0163bceef20b4be57e036ef07f54232c71689deae` |
| `system/init/lmkd.rc` | 263 | 8 | `1f7c5b4cafedb3b3ce282f09bf0af92bafa2632befbe3ba403bee6bf558e58f8` |
| `system/init/logd.rc` | 879 | 34 | `5bf6957ca7525aef10efcc40571d52a58f2787d1c024fb472fb3ec84a42ccf7c` |
| `system/init/lpdumpd.rc` | 1346 | 35 | `8bc75276337f68cbf977e1bb732e95cde28d5a928f4d3a96155c52c3f5b73b5a` |
| `system/init/mdnsd.rc` | 142 | 7 | `6dcc3f787ed0bb94abb3843504604625c62852072219d8bf15e8628baef2cb95` |
| `system/init/mediadrmserver.rc` | 158 | 6 | `641ecb76d79626933d331ec9d7914a1d517829196ee953a088040884ef555402` |
| `system/init/mediaextractor.rc` | 175 | 6 | `2836162e20a0e99a2c1623c691306bbd62cfc4beccb9bacd3767886dfccf3474` |
| `system/init/mediametrics.rc` | 178 | 6 | `9577f9b48e8672cd85052ed096818864efdac9d2af4a31ba5aaa7db095477d5d` |
| `system/init/mediaserver.rc` | 239 | 6 | `187317dbf336b0739f5d6cfd8361f42722c3ecb356c1f098e3c531e9f2063fc7` |
| `system/init/mobile_log_d.rc` | 1374 | 51 | `13ec06c22b7f146eeed687fe7007bbe010ef5d68ed97b6257b00aa63d9e5ab64` |
| `system/init/mtkbootanim.rc` | 575 | 24 | `0eb21d3b9376e3a080a51b5d4940260d53838c6b9dda6b05a561d167bfdf49d6` |
| `system/init/mtpd.rc` | 178 | 8 | `242323dcf2fa1517f0c89e2dd58697aa4f378df1da0c9223270171f7e9e9a380` |
| `system/init/netd.rc` | 468 | 11 | `7c0ebda8e3ea7c394f05ab097ae15793b96dbde743f278255466b48c380c9ed1` |
| `system/init/netdiag.rc` | 1157 | 31 | `074487ecc6e9b5517510edcd9d26848158f09a7c580f7f6e25660dac9d33de12` |
| `system/init/perfetto.rc` | 2323 | 64 | `5594497498160c38ca6726a99f5d0d25e651293ea6885f67cf87a86f8472df53` |
| `system/init/racoon.rc` | 230 | 9 | `c58b0ffdf22e92b58655464d6a9671c00830f572bd3ca4b8afdbaae6f60f7b91` |
| `system/init/recovery-persist.rc` | 127 | 3 | `0c5e3bee4329907791a861067722bbea0d56ad6639cd796fba2526c26bc975fa` |
| `system/init/rss_hwm_reset.rc` | 912 | 26 | `5e664b1dbc2de60620861eed1bb82e2bbd7169526798216c142c78a2dc8f9999` |
| `system/init/servicemanager.rc` | 557 | 18 | `48769fd38dce3feff79a9793219dfb18c752e7dd5f569cfb82c3a2e7e218d4ff` |
| `system/init/statsd.rc` | 1112 | 30 | `d2f635f82544938d3ba7afbd8ab4fa1219d77dd404672eac697a6ffdc6af10d6` |
| `system/init/storaged.rc` | 225 | 8 | `c51225e9f407e23f8099d42dcc7ef956fdcaa29e14d500c84f7fbbe51555af5e` |
| `system/init/surfaceflinger.rc` | 559 | 9 | `c31a17738008b7171c6639fb6f35d580c95f96b73261684940bdc41558c9111d` |
| `system/init/terserver.rc` | 153 | 8 | `090151351f81078342d7e2a016c7e584c849c6b56d7584f6316609a019189c88` |
| `system/init/tombstoned.rc` | 399 | 11 | `1224a6de9b2708d606d21ad52d61df0b03925252f09d983b5889940456e95f67` |
| `system/init/uncrypt.rc` | 390 | 17 | `63b33e2a2271d0c598bca842d0e6b03564004ed1e0e2995e8dd8ed5616455e82` |
| `system/init/usbd.rc` | 103 | 5 | `772b27f3affb6e67d1a6bc83a2b201cbdc053854789326ac9833ab84277ff995` |
| `system/init/vdc.rc` | 525 | 12 | `f5cd9833d92d17e5ca7d871a84f84f78000c68a8f7a8359938dcae7daadc0459` |
| `system/init/vold.rc` | 326 | 8 | `f1d6459d7acd102602eefb361228a34f43f286e66d9b8001f422d0ce7915bc1e` |
| `system/init/wait_for_keymaster.rc` | 127 | 5 | `960326cc4eda9d34347feaaee1e1add9f8d1e299242cf15fbd78f6f159bc47d7` |
| `system/init/wifi-events.rc` | 3577 | 64 | `3ccbfdac0927cb7a90825736e262ddb015d7e3bd301222a90a6d02862614caa3` |
| `system/init/wificond.rc` | 135 | 5 | `7ec3ef548e88bcd13577b59239ca88b594454622818e4a9190ad274b1aadee38` |

Product init files: none present in the captured source pack. [VERIFIED]

################################################################################
# 1.5 — INIT.MT6762.RC / INIT.MT6765.RC IDENTITY
################################################################################

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `vendor/init/hw/init.mt6762.rc` | 39950 | 1115 | `a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9` |
| `vendor/init/hw/init.mt6765.rc` | 39950 | 1115 | `a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9` |

Result: `init.mt6762.rc` and `init.mt6765.rc` are byte-for-byte identical. [VERIFIED]
Interpretation: the device ships one MTK platform init body under both platform identities.
This is not normalized away. It is a core evidence point for the MT6762 runtime / MT6765
BSP split.

################################################################################
# 1.6 — IMPORT GRAPH AND TARGET RESOLUTION
################################################################################

| Source file | Line | Import target | Resolved source-pack path | Status |
|---|---:|---|---|---|
| `root/init.rc` | 7 | `/init.environ.rc` | `root/init.environ.rc` | PRESENT |
| `root/init.rc` | 8 | `/init.usb.rc` | `root/init.usb.rc` | PRESENT |
| `root/init.rc` | 9 | `/init.${ro.hardware}.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `root/init.rc` | 10 | `/vendor/etc/init/hw/init.${ro.hardware}.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `root/init.rc` | 11 | `/init.usb.configfs.rc` | `root/init.usb.configfs.rc` | PRESENT |
| `root/init.rc` | 12 | `/init.${ro.zygote}.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 6 | `/system/etc/init/hw/vendor_init_as_system.rc` | `system/init/hw/vendor_init_as_system.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 7 | `${ro.vendor.rc}factory_init.connectivity.rc` | `vendor/init/hw/factory_init.connectivity.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 8 | `/init.environ.rc` | `root/init.environ.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 9 | `/vendor/etc/init/trustonic.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 10 | `${ro.vendor.rc}init.nvdata.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 11 | `/vendor/etc/init/microtrust.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 12 | `/vendor/etc/init/trustkernel.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 13 | `/vendor/etc/init/hw/init.aee.rc` | `vendor/init/hw/init.aee.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 14 | `/system/etc/init/hw/init.aee.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 15 | `/vendor/etc/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc` | `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 16 | `/vendor/etc/init/android.hardware.boot@1.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/factory_init.rc` | 19 | `/system/etc/init/apexd.rc` | `system/init/apexd.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 20 | `/system/etc/init/art_apex_boot_integrity.rc` | `system/init/art_apex_boot_integrity.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 23 | `/vendor/etc/init/hw/factory_init.project.rc` | `vendor/init/hw/factory_init.project.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 24 | `/system/etc/init/servicemanager.rc` | `system/init/servicemanager.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 25 | `/system/etc/init/hwservicemanager.rc` | `system/init/hwservicemanager.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 26 | `/system/etc/init/android.hidl.allocator@1.0-service.rc` | `system/init/android.hidl.allocator@1.0-service.rc` | PRESENT |
| `vendor/init/hw/factory_init.rc` | 27 | `/system/etc/init/vold.rc` | `system/init/vold.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 3 | `${ro.vendor.rc}init.connectivity.rc` | `vendor/init/hw/init.connectivity.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 4 | `/vendor/etc/init/hw/init.mt6765.usb.rc` | `vendor/init/hw/init.mt6765.usb.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 5 | `/vendor/etc/init/hw/init.project.rc` | `vendor/init/hw/init.project.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 6 | `/FWUpgradeInit.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6762.rc` | 7 | `/vendor/etc/init/hw/init.aee.rc` | `vendor/init/hw/init.aee.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 8 | `/system/etc/init/hw/init.aee.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6762.rc` | 9 | `${ro.vendor.rc}init.volte.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6762.rc` | 10 | `${ro.vendor.rc}init.mal.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6762.rc` | 11 | `${ro.vendor.rc}init.sensor_1_0.rc` | `vendor/init/hw/init.sensor_1_0.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 12 | `${ro.vendor.rc}init.ago.rc` | `vendor/init/hw/init.ago.rc` | PRESENT |
| `vendor/init/hw/init.mt6762.rc` | 15 | `${ro.vendor.rc}init.modem.rc` | `vendor/init/hw/init.modem.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 3 | `${ro.vendor.rc}init.connectivity.rc` | `vendor/init/hw/init.connectivity.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 4 | `/vendor/etc/init/hw/init.mt6765.usb.rc` | `vendor/init/hw/init.mt6765.usb.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 5 | `/vendor/etc/init/hw/init.project.rc` | `vendor/init/hw/init.project.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 6 | `/FWUpgradeInit.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6765.rc` | 7 | `/vendor/etc/init/hw/init.aee.rc` | `vendor/init/hw/init.aee.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 8 | `/system/etc/init/hw/init.aee.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6765.rc` | 9 | `${ro.vendor.rc}init.volte.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6765.rc` | 10 | `${ro.vendor.rc}init.mal.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/init.mt6765.rc` | 11 | `${ro.vendor.rc}init.sensor_1_0.rc` | `vendor/init/hw/init.sensor_1_0.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 12 | `${ro.vendor.rc}init.ago.rc` | `vendor/init/hw/init.ago.rc` | PRESENT |
| `vendor/init/hw/init.mt6765.rc` | 15 | `${ro.vendor.rc}init.modem.rc` | `vendor/init/hw/init.modem.rc` | PRESENT |
| `vendor/init/hw/init.project.rc` | 3 | `init.mt6765.usb.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 6 | `/system/etc/init/hw/vendor_init_as_system.rc` | `system/init/hw/vendor_init_as_system.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 7 | `/init.environ.rc` | `root/init.environ.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 8 | `/vendor/etc/init/hw/init.aee.rc` | `vendor/init/hw/init.aee.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 9 | `/system/etc/init/hw/init.aee.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 10 | `/vendor/etc/init/trustonic.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 11 | `${ro.vendor.rc}meta_init.connectivity.rc` | `vendor/init/hw/meta_init.connectivity.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 12 | `/vendor/etc/init/hw/meta_init.project.rc` | `vendor/init/hw/meta_init.project.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 13 | `/vendor/etc/init/microtrust.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 14 | `/vendor/etc/init/trustkernel.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 16 | `/system/etc/init/apexd.rc` | `system/init/apexd.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 17 | `/system/etc/init/art_apex_boot_integrity.rc` | `system/init/art_apex_boot_integrity.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 19 | `/system/etc/init/vold.rc` | `system/init/vold.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 20 | `/system/etc/init/servicemanager.rc` | `system/init/servicemanager.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 21 | `/system/etc/init/hwservicemanager.rc` | `system/init/hwservicemanager.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 22 | `/system/etc/init/android.hidl.allocator@1.0-service.rc` | `system/init/android.hidl.allocator@1.0-service.rc` | PRESENT |
| `vendor/init/hw/meta_init.rc` | 23 | `/vendor/etc/init/android.hardware.boot@1.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/meta_init.rc` | 26 | `${ro.vendor.rc}meta_init.modem.rc` | `vendor/init/hw/meta_init.modem.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 3 | `/system/etc/init/aee_aed.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 4 | `/system/etc/init/aee_aed64.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 5 | `/vendor/etc/init/aee_aedv.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 6 | `/vendor/etc/init/aee_aedv64.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 7 | `/system/etc/init/logd.rc` | `system/init/logd.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 8 | `/system/etc/init/logd_e.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 9 | `/system/etc/init/logcatd.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 10 | `/system/etc/init/storagemanagerd.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 11 | `/system/etc/init/mobile_log_d.rc` | `system/init/mobile_log_d.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 12 | `/system/etc/init/android.system.suspend@1.0-service.rc` | `system/init/android.system.suspend@1.0-service.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 13 | `/vendor/etc/init/storageproxyd.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 14 | `/vendor/etc/init/android.hardware.keymaster@3.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 15 | `/vendor/etc/init/android.hardware.keymaster@3.0-service.trustonic.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 16 | `/vendor/etc/init/android.hardware.keymaster@4.0-service.rc` | `vendor/init/android.hardware.keymaster@4.0-service.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 17 | `/vendor/etc/init/android.hardware.keymaster@4.0-service.trustonic.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 18 | `/vendor/etc/init/android.hardware.keymaster@4.0-service.beanpod.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 19 | `/vendor/etc/init/android.hardware.keymaster@4.0-service.trustkernel.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 20 | `/vendor/etc/init/android.hardware.keymaster@4.0-service.trusty.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 21 | `/vendor/etc/init/vendor.mediatek.hardware.keyinstall@1.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 22 | `/vendor/etc/init/vendor.mediatek.hardware.keymanage@1.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 23 | `/vendor/etc/init/android.hardware.audio@4.0-service-mediatek.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 24 | `/vendor/etc/init/vendor.mediatek.hardware.keymaster_attestation@1.1-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 25 | `/vendor/etc/init/android.hardware.nfc@1.2-service-st.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 26 | `/vendor/etc/init/tee.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 27 | `/vendor/etc/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc` | `vendor/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 28 | `/vendor/etc/init/vendor.mediatek.hardware.nvram@1.1-sevice-lazy.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 29 | `/vendor/etc/init/android.hardware.light@2.0-service-mediatek.rc` | `vendor/init/android.hardware.light@2.0-service-mediatek.rc` | PRESENT |
| `vendor/init/hw/multi_init.rc` | 30 | `/vendor/etc/init/android.hardware.light@2.0-service-mediatek-lazy.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 31 | `/vendor/etc/init/merged_hal_service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 32 | `/vendor/etc/init/vendor.microtrust.hardware.capi@2.0-service.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 33 | `/vendor/etc/init/spm_script.rc` | `` | ABSENT_IN_SOURCE_PACK |
| `vendor/init/hw/multi_init.rc` | 34 | `/vendor/etc/init/nvram_daemon.rc` | `vendor/init/nvram_daemon.rc` | PRESENT |

Critical imported-target findings:
- `/FWUpgradeInit.rc` is imported by both platform files and is absent from the source pack. [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK]
- `/system/etc/init/hw/init.aee.rc` is imported by platform/factory/meta files and is absent from the source pack. [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK]
- `${ro.vendor.rc}init.volte.rc` and `${ro.vendor.rc}init.mal.rc` are imported by both platform files and absent from the source pack. [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK]
- `init.modem.rc` is the final platform import in both `init.mt6762.rc` and `init.mt6765.rc`. [VERIFIED]

################################################################################
# 1.7 — SERVICE DECLARATION SUMMARY
################################################################################

| Service source | Declarations |
|---|---:|
| Root init | 10 |
| Vendor flat | 66 |
| Vendor hw | 66 |
| System | 69 |
| Product | 0 |

| Runtime init.svc state | Count |
|---|---:|
| running | 93 |
| stopped | 18 |

Class membership declared in service blocks:

| Class | Declaration count |
|---|---:|
| `main` | 82 |
| `core` | 43 |
| `hal` | 28 |
| `late_start` | 13 |
| `animation` | 12 |
| `early_hal` | 4 |
| `charger` | 1 |

Declared service-user distribution:

| User | Declaration count |
|---|---:|
| `system` | 66 |
| `root` | 33 |
| `<unset>` | 31 |
| `shell` | 16 |
| `radio` | 15 |
| `media` | 8 |
| `graphics` | 6 |
| `wifi` | 6 |
| `nobody` | 5 |
| `cameraserver` | 3 |
| `logd` | 3 |
| `audioserver` | 2 |
| `vpn` | 2 |
| `gps` | 2 |
| `drm` | 1 |
| `gpu_service` | 1 |
| `incidentd` | 1 |
| `iorapd` | 1 |
| `keystore` | 1 |
| `lmkd` | 1 |
| `mdnsr` | 1 |
| `mediaex` | 1 |
| `statsd` | 1 |
| `tombstoned` | 1 |
| `bluetooth` | 1 |
| `mediacodec` | 1 |
| `secure_element` | 1 |

################################################################################
# 1.8 — COMPLETE SERVICE DECLARATION CATALOG
################################################################################

| File:Line | Service | Command | Runtime | Classes | User | Group | Options / interfaces / sockets / caps | Tags |
|---|---|---|---|---|---|---|---|---|
| `root/init.rc:828` | `ueventd` | `/system/bin/ueventd` | `running` | `core` | `` | `` | class=core; u:r:ueventd:s0; critical |  |
| `root/init.rc:834` | `console` | `/system/bin/sh` | `` | `core` | `shell` | `shell log readproc` | class=core; user=shell; group=shell log readproc; u:r:shell:s0; disabled | DISABLED_DECLARED |
| `root/init.rc:851` | `flash_recovery` | `/system/bin/install-recovery.sh` | `stopped` | `main` | `` | `` | class=main; oneshot | ONESHOT_DECLARED |
| `root/init.rc:855` | `bootanim` | `/system/bin/mtkbootanimation` | `stopped` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `root/init.rc:863` | `banim_shutmp3` | `/system/bin/mtkbootanimation shut mp3` | `` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `root/init.rc:871` | `banim_shutnomp3` | `/system/bin/mtkbootanimation shut nomp3` | `` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `root/init.usb.rc:15` | `adbd` | `/system/bin/adbd --root_seclabel=u:r:su:s0` | `running` | `core` | `` | `` | class=core; socket=adbd seqpacket 660 system system; u:r:adbd:s0; disabled | DISABLED_DECLARED;SOCKET_SERVICE |
| `root/init.zygote32.rc:1` | `zygote` | `/system/bin/app_process -Xzygote /system/bin --zygote --start-system-server` | `running` | `main` | `root` | `root readproc reserved_disk` | class=main; user=root; group=root readproc reserved_disk; socket=zygote stream 660 root system ; usap_pool_primary stream 660 root system; priority -20; onrestart write /sys/android_power/request_state wake; onrestart write /sys/power/state on; onrestart restart audioserver; onrestart restart cameraserver; onrestart restart media; onrestart restart netd; onrestart restart wificond; writepid /dev/cpuset/foreground/tasks | SOCKET_SERVICE |
| `root/init.zygote64_32.rc:1` | `zygote` | `/system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server --socket-name=zygote` | `running` | `main` | `root` | `root readproc reserved_disk` | class=main; user=root; group=root readproc reserved_disk; socket=zygote stream 660 root system ; usap_pool_primary stream 660 root system; priority -20; onrestart write /sys/android_power/request_state wake; onrestart write /sys/power/state on; onrestart restart audioserver; onrestart restart cameraserver; onrestart restart media; onrestart restart netd; onrestart restart wificond; writepid /dev/cpuset/foreground/tasks | SOCKET_SERVICE |
| `root/init.zygote64_32.rc:17` | `zygote_secondary` | `/system/bin/app_process32 -Xzygote /system/bin --zygote --socket-name=zygote_secondary --enable-lazy-preload` | `running` | `main` | `root` | `root readproc reserved_disk` | class=main; user=root; group=root readproc reserved_disk; socket=zygote_secondary stream 660 root system ; usap_pool_secondary stream 660 root system; priority -20; onrestart restart zygote; writepid /dev/cpuset/foreground/tasks | SOCKET_SERVICE |
| `system/init/android.hidl.allocator@1.0-service.rc:1` | `hidl_memory` | `/system/bin/hw/android.hidl.allocator@1.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system |  |
| `system/init/android.system.suspend@1.0-service.rc:1` | `system_suspend` | `/system/bin/hw/android.system.suspend@1.0-service` | `running` | `early_hal` | `system` | `system wakelock` | class=early_hal; user=system; group=system wakelock; capabilities=BLOCK_SUSPEND | CAPABILITY_SERVICE |
| `system/init/apexd.rc:1` | `apexd` | `/system/bin/apexd` | `running` | `core` | `root` | `system` | class=core; user=root; group=system; critical |  |
| `system/init/apexd.rc:8` | `apexd-bootstrap` | `/system/bin/apexd --bootstrap` | `stopped` | `` | `root` | `system` | user=root; group=system; disabled; oneshot; critical | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/ashmemd.rc:1` | `ashmemd` | `/system/bin/ashmemd` | `running` | `core` | `nobody` | `nobody` | class=core; user=nobody; group=nobody |  |
| `system/init/atrace.rc:209` | `boottrace` | `/system/bin/atrace --async_start -f /data/misc/boottrace/categories` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/audioserver.rc:1` | `audioserver` | `/system/bin/audioserver` | `running` | `core` | `audioserver` | `audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw` | class=core; user=audioserver; group=audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw; capabilities=BLOCK_SUSPEND; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks; onrestart restart vendor.audio-hal-2-0; onrestart restart audio-hal-2-0 | CAPABILITY_SERVICE |
| `system/init/batterywarning.rc:2` | `batterywarning` | `/system/bin/batterywarning` | `running` | `main` | `system` | `system` | class=main; user=system; group=system; disabled | DISABLED_DECLARED |
| `system/init/blank_screen.rc:1` | `blank_screen` | `/system/bin/blank_screen` | `` | `` | `system` | `system` | user=system; group=system; oneshot | ONESHOT_DECLARED |
| `system/init/bootlogoupdater.rc:3` | `bootlogoupdater` | `/system/bin/boot_logo_updater` | `stopped` | `core` | `` | `graphics` | class=core; group=graphics; oneshot | ONESHOT_DECLARED |
| `system/init/bpfloader.rc:1` | `bpfloader` | `/system/bin/bpfloader` | `stopped` | `main` | `` | `` | class=main; capabilities=SYS_ADMIN; oneshot; rlimit memlock 67108864 67108864 | CAPABILITY_SERVICE;ONESHOT_DECLARED |
| `system/init/camerapostalgo.rc:1` | `camerapostalgo` | `/system/bin/camerapostalgo` | `` | `main` | `cameraserver` | `camera input system media graphics sdcard_rw` | class=main; user=cameraserver; group=camera input system media graphics sdcard_rw; disabled | DISABLED_DECLARED |
| `system/init/cameraserver.rc:1` | `cameraserver` | `/system/bin/cameraserver` | `running` | `main` | `cameraserver` | `audio camera input drmrpc` | class=main; user=cameraserver; group=audio camera input drmrpc; ioprio rt 4; writepid /dev/cpuset/camera-daemon/tasks /dev/stune/top-app/tasks; rlimit rtprio 10 10 |  |
| `system/init/drmserver.rc:1` | `drm` | `/system/bin/drmserver` | `running` | `main` | `drm` | `drm system inet drmrpc readproc sdcard_rw media_rw` | class=main; user=drm; group=drm system inet drmrpc readproc sdcard_rw media_rw; writepid /dev/cpuset/foreground/tasks | SECURITY_SUBSTRATE |
| `system/init/dumpstate.rc:6` | `dumpstate` | `/system/bin/dumpstate -s` | `` | `main` | `` | `` | class=main; socket=dumpstate stream 0660 shell log; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/dumpstate.rc:14` | `dumpstatez` | `/system/bin/dumpstate -S -d -z \\` | `` | `main` | `` | `` | class=main; socket=dumpstate stream 0660 shell log; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/dumpstate.rc:22` | `bugreportd` | `/system/bin/dumpstate -w` | `` | `main` | `` | `` | class=main; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/gatekeeperd.rc:1` | `gatekeeperd` | `/system/bin/gatekeeperd /data/misc/gatekeeper` | `running` | `late_start` | `system` | `` | class=late_start; user=system; writepid /dev/cpuset/system-background/tasks | SECURITY_SUBSTRATE |
| `system/init/gpuservice.rc:1` | `gpu` | `/system/bin/gpuservice` | `running` | `core` | `gpu_service` | `graphics` | class=core; user=gpu_service; group=graphics |  |
| `system/init/gsid.rc:1` | `gsid` | `/system/bin/gsid` | `stopped` | `` | `root` | `root system media_rw` | user=root; group=root system media_rw; disabled | DISABLED_DECLARED |
| `system/init/heapprofd.rc:14` | `heapprofd` | `/system/bin/heapprofd` | `` | `late_start` | `nobody` | `nobody readproc` | class=late_start; user=nobody; group=nobody readproc; capabilities=KILL DAC_READ_SEARCH; socket=heapprofd stream 0666 root root; disabled; writepid /dev/cpuset/foreground/tasks; onrestart exec_background - nobody shell -- /system/bin/heapprofd --cleanup-after-crash | CAPABILITY_SERVICE;DISABLED_DECLARED;SOCKET_SERVICE |
| `system/init/hwservicemanager.rc:1` | `hwservicemanager` | `/system/bin/hwservicemanager` | `running` | `animation` | `system` | `system readproc` | class=animation; user=system; group=system readproc; disabled; critical; onrestart setprop hwservicemanager.ready false; onrestart class_restart main; onrestart class_restart hal; onrestart class_restart early_hal; writepid /dev/cpuset/system-background/tasks | DISABLED_DECLARED |
| `system/init/idmap2d.rc:1` | `idmap2d` | `/system/bin/idmap2d` | `stopped` | `main` | `system` | `system` | class=main; user=system; group=system |  |
| `system/init/incidentd.rc:15` | `incidentd` | `/system/bin/incidentd` | `running` | `main` | `incidentd` | `incidentd log readproc` | class=main; user=incidentd; group=incidentd log readproc; capabilities=KILL SYS_PTRACE | CAPABILITY_SERVICE |
| `system/init/init.thermald.rc:1` | `thermald` | `/system/bin/thermald` | `running` | `main` | `system` | `system net_admin` | class=main; user=system; group=system net_admin |  |
| `system/init/init.vtservice.rc:3` | `vtservice` | `/system/bin/vtservice` | `running` | `main` | `system` | `system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw` | class=main; user=system; group=system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw; ioprio rt 4 |  |
| `system/init/installd.rc:2` | `installd` | `/system/bin/installd` | `running` | `main` | `` | `` | class=main |  |
| `system/init/iorapd.rc:15` | `iorapd` | `/system/bin/iorapd` | `stopped` | `main` | `iorapd` | `iorapd` | class=main; user=iorapd; group=iorapd; writepid /dev/cpuset/system-background/tasks |  |
| `system/init/keystore.rc:1` | `keystore` | `/system/bin/keystore /data/misc/keystore` | `running` | `main` | `keystore` | `keystore drmrpc readproc log` | class=main; user=keystore; group=keystore drmrpc readproc log; writepid /dev/cpuset/foreground/tasks |  |
| `system/init/kpoc_charger.rc:4` | `kpoc_charger` | `/system/bin/kpoc_charger` | `` | `charger` | `` | `system wakelock` | class=charger; group=system wakelock; capabilities=BLOCK_SUSPEND SYS_ADMIN SYS_BOOT | CAPABILITY_SERVICE |
| `system/init/lmkd.rc:1` | `lmkd` | `/system/bin/lmkd` | `running` | `core` | `lmkd` | `lmkd system readproc` | class=core; user=lmkd; group=lmkd system readproc; capabilities=DAC_OVERRIDE KILL IPC_LOCK SYS_NICE SYS_RESOURCE; socket=lmkd seqpacket 0660 system system; critical; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE;SOCKET_SERVICE |
| `system/init/logd.rc:1` | `logd` | `/system/bin/logd` | `running` | `` | `logd` | `logd system package_info readproc` | user=logd; group=logd system package_info readproc; capabilities=SYSLOG AUDIT_CONTROL SETGID; socket=logd stream 0666 logd logd ; logdr seqpacket 0666 logd logd ; logdw dgram+passcred 0222 logd logd; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE;SOCKET_SERVICE |
| `system/init/logd.rc:12` | `logd-reinit` | `/system/bin/logd --reinit` | `stopped` | `` | `logd` | `logd` | user=logd; group=logd; disabled; oneshot; writepid /dev/cpuset/system-background/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/logd.rc:20` | `logd-auditctl` | `/system/bin/auditctl -r 5` | `stopped` | `` | `logd` | `logd` | user=logd; group=logd; capabilities=AUDIT_CONTROL; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/lpdumpd.rc:17` | `lpdumpd` | `/system/bin/lpdumpd` | `` | `` | `system` | `system` | user=system; group=system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mdnsd.rc:1` | `mdnsd` | `/system/bin/mdnsd` | `running` | `main` | `mdnsr` | `inet` | class=main; user=mdnsr; group=inet; socket=mdnsd stream 0660 mdnsr inet; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/mediadrmserver.rc:1` | `mediadrm` | `/system/bin/mediadrmserver` | `running` | `main` | `media` | `mediadrm drmrpc` | class=main; user=media; group=mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | SECURITY_SUBSTRATE |
| `system/init/mediaextractor.rc:1` | `mediaextractor` | `/system/bin/mediaextractor` | `running` | `main` | `mediaex` | `drmrpc mediadrm readproc` | class=main; user=mediaex; group=drmrpc mediadrm readproc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| `system/init/mediametrics.rc:1` | `mediametrics` | `/system/bin/mediametrics` | `running` | `main` | `media` | `media` | class=main; user=media; group=media; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks |  |
| `system/init/mediaserver.rc:1` | `media` | `/system/bin/mediaserver` | `running` | `main` | `media` | `audio camera inet net_bt net_bt_admin net_bw_acct drmrpc mediadrm readproc` | class=main; user=media; group=audio camera inet net_bt net_bt_admin net_bw_acct drmrpc mediadrm readproc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks |  |
| `system/init/mobile_log_d.rc:7` | `mobile_log_d` | `/system/bin/mobile_log_d` | `running` | `main` | `` | `` | class=main |  |
| `system/init/mobile_log_d.rc:10` | `mmedialogstart` | `/system/bin/mobile_log_d --ftrace mmedia` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mobile_log_d.rc:14` | `bsplogstart` | `/system/bin/mobile_log_d --ftrace bsp` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mobile_log_d.rc:18` | `trace_buf_off` | `/system/bin/mobile_log_d --ftrace off` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mobile_log_d.rc:22` | `mobile_log_d_contorl` | `/system/bin/mobile_log_d --control copy` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mobile_log_d.rc:26` | `mobile_log_d_sublog_config` | `/system/bin/mobile_log_d --control sublog_config` | `` | `` | `` | `` | disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mtkbootanim.rc:1` | `bootanim` | `/system/bin/mtkbootanimation` | `stopped` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mtkbootanim.rc:10` | `banim_shutmp3` | `/system/bin/mtkbootanimation shut mp3` | `` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mtkbootanim.rc:18` | `banim_shutnomp3` | `/system/bin/mtkbootanimation shut nomp3` | `` | `core,animation` | `graphics` | `graphics audio` | class=core,animation; user=graphics; group=graphics audio; disabled; oneshot; writepid /dev/stune/top-app/tasks | DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/mtpd.rc:1` | `mtpd` | `/system/bin/mtpd` | `` | `main` | `vpn` | `vpn inet` | class=main; user=vpn; group=vpn inet; capabilities=NET_ADMIN NET_RAW; socket=mtpd stream 600 system system; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/netd.rc:1` | `netd` | `/system/bin/netd` | `running` | `main` | `` | `` | class=main; socket=dnsproxyd stream 0660 root inet ; mdns stream 0660 root system ; fwmarkd stream 0660 root inet; onrestart restart zygote; onrestart restart zygote_secondary | SOCKET_SERVICE |
| `system/init/netdiag.rc:27` | `netdiag` | `/system/bin/netdiag` | `running` | `main` | `shell` | `log system radio sdcard_rw inet sdcard_r media_rw net_raw net_admin net_bw_stats readproc net_bt input` | class=main; user=shell; group=log system radio sdcard_rw inet sdcard_r media_rw net_raw net_admin net_bw_stats readproc net_bt input; capabilities=NET_ADMIN NET_RAW | CAPABILITY_SERVICE |
| `system/init/perfetto.rc:15` | `traced` | `/system/bin/traced` | `` | `late_start` | `nobody` | `nobody` | class=late_start; user=nobody; group=nobody; socket=traced_consumer stream 0666 root root ; traced_producer stream 0666 root root; disabled; writepid /dev/cpuset/system-background/tasks | DISABLED_DECLARED;SOCKET_SERVICE |
| `system/init/perfetto.rc:24` | `traced_probes` | `/system/bin/traced_probes` | `` | `late_start` | `nobody` | `nobody readproc log` | class=late_start; user=nobody; group=nobody readproc log; capabilities=DAC_READ_SEARCH; disabled; writepid /dev/cpuset/system-background/tasks; onrestart exec_background - nobody shell -- /system/bin/traced_probes --cleanup-after-crash | CAPABILITY_SERVICE;DISABLED_DECLARED |
| `system/init/racoon.rc:1` | `racoon` | `/system/bin/racoon` | `` | `main` | `vpn` | `vpn inet` | class=main; user=vpn; group=vpn inet; capabilities=NET_ADMIN NET_BIND_SERVICE NET_RAW; socket=racoon stream 600 system system; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/rss_hwm_reset.rc:15` | `rss_hwm_reset` | `/system/bin/rss_hwm_reset` | `` | `late_start` | `nobody` | `nobody readproc` | class=late_start; user=nobody; group=nobody readproc; capabilities=DAC_OVERRIDE; disabled; oneshot; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED |
| `system/init/servicemanager.rc:1` | `servicemanager` | `/system/bin/servicemanager` | `running` | `core,animation` | `system` | `system readproc` | class=core,animation; user=system; group=system readproc; critical; onrestart restart healthd; onrestart restart zygote; onrestart restart audioserver; onrestart restart media; onrestart restart surfaceflinger; onrestart restart inputflinger; onrestart restart drm; onrestart restart cameraserver; onrestart restart keystore; onrestart restart gatekeeperd; onrestart restart thermalservice; writepid /dev/cpuset/system-background/tasks |  |
| `system/init/statsd.rc:15` | `statsd` | `/system/bin/statsd` | `running` | `main` | `statsd` | `statsd log` | class=main; user=statsd; group=statsd log; socket=statsdw dgram+passcred 0222 statsd statsd; writepid /dev/cpuset/system-background/tasks | SOCKET_SERVICE |
| `system/init/storaged.rc:1` | `storaged` | `/system/bin/storaged` | `running` | `main` | `root` | `package_info` | class=main; user=root; group=package_info; capabilities=DAC_READ_SEARCH; priority 10; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE |
| `system/init/surfaceflinger.rc:1` | `surfaceflinger` | `/system/bin/surfaceflinger` | `running` | `core,animation` | `system` | `graphics drmrpc readproc` | class=core,animation; user=system; group=graphics drmrpc readproc; socket=pdx/system/vr/display/client stream 0666 system graphics u:object_r:pdx_display_client_endpoint_socket:s0 ; pdx/system/vr/display/manager stream 0666 system graphics u:object_r:pdx_display_manager_endpoint_socket:s0 ; pdx/system/vr/display/vsync stream 0666 system graphics u:object_r:pdx_display_vsync_endpoint_socket:s0; onrestart restart zygote; writepid /dev/stune/foreground/tasks | SOCKET_SERVICE |
| `system/init/terserver.rc:4` | `terservice` | `/system/bin/terservice` | `stopped` | `core` | `radio` | `radio cache inet misc` | class=core; user=radio; group=radio cache inet misc; oneshot | ONESHOT_DECLARED |
| `system/init/tombstoned.rc:1` | `tombstoned` | `/system/bin/tombstoned` | `running` | `late_start` | `tombstoned` | `system` | class=late_start; user=tombstoned; group=system; socket=tombstoned_crash seqpacket 0666 system system ; tombstoned_intercept seqpacket 0666 system system ; tombstoned_java_trace seqpacket 0666 system system; writepid /dev/cpuset/system-background/tasks | SOCKET_SERVICE |
| `system/init/uncrypt.rc:1` | `uncrypt` | `/system/bin/uncrypt` | `` | `main` | `` | `` | class=main; socket=uncrypt stream 600 system system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/uncrypt.rc:7` | `setup-bcb` | `/system/bin/uncrypt --setup-bcb` | `` | `main` | `` | `` | class=main; socket=uncrypt stream 600 system system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/uncrypt.rc:13` | `clear-bcb` | `/system/bin/uncrypt --clear-bcb` | `` | `main` | `` | `` | class=main; socket=uncrypt stream 600 system system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `system/init/usbd.rc:1` | `usbd` | `/system/bin/usbd` | `stopped` | `late_start` | `root` | `root usb system` | class=late_start; user=root; group=root usb system; oneshot | ONESHOT_DECLARED |
| `system/init/vold.rc:1` | `vold` | `/system/bin/vold \\` | `running` | `core` | `` | `root reserved_disk` | class=core; group=root reserved_disk; ioprio be 2; writepid /dev/cpuset/foreground/tasks |  |
| `system/init/wait_for_keymaster.rc:1` | `wait_for_keymaster` | `/system/bin/wait_for_keymaster` | `` | `` | `root` | `root system` | user=root; group=root system; priority -20; ioprio rt 0 | SECURITY_SUBSTRATE |
| `system/init/wificond.rc:1` | `wificond` | `/system/bin/wificond` | `running` | `main` | `wifi` | `wifi net_raw net_admin` | class=main; user=wifi; group=wifi net_raw net_admin; capabilities=NET_RAW NET_ADMIN | CAPABILITY_SERVICE;CONNECTIVITY |
| `vendor/init/android.hardware.audio@5.0-service-mediatek.rc:4` | `vendor.audio-hal-2-0` | `/vendor/bin/hw/android.hardware.audio@5.0-service-mediatek` | `running` | `hal` | `audioserver` | `audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw` | class=hal; user=audioserver; group=audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw; capabilities=BLOCK_SUSPEND SYS_NICE; interface=android.hardware.audio@5.0::IDevicesFactory default; oneshot; ioprio rt 4; writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks | CAPABILITY_SERVICE;ONESHOT_DECLARED |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:1` | `bluetooth-1-0` | `/vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek` | `running` | `hal` | `bluetooth` | `bluetooth` | class=hal; user=bluetooth; group=bluetooth; capabilities=SYS_NICE; writepid /dev/stune/foreground/tasks | CAPABILITY_SERVICE;CONNECTIVITY |
| `vendor/init/android.hardware.cas@1.1-service.rc:1` | `vendor.cas-hal-1-1` | `/vendor/bin/hw/android.hardware.cas@1.1-service` | `running` | `hal` | `media` | `mediadrm drmrpc` | class=hal; user=media; group=mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| `vendor/init/android.hardware.configstore@1.1-service.rc:1` | `vendor.configstore-hal` | `/vendor/bin/hw/android.hardware.configstore@1.1-service` | `running` | `hal,animation` | `system` | `system` | class=hal,animation; user=system; group=system |  |
| `vendor/init/android.hardware.drm@1.0-service.rc:1` | `vendor.drm-hal-1-0` | `/vendor/bin/hw/android.hardware.drm@1.0-service` | `running` | `hal` | `media` | `mediadrm drmrpc` | class=hal; user=media; group=mediadrm drmrpc; interface=android.hardware.drm@1.0::ICryptoFactory default ; android.hardware.drm@1.0::IDrmFactory default; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.drm@1.2-service.clearkey.rc:1` | `vendor.drm-clearkey-hal-1-2` | `/vendor/bin/hw/android.hardware.drm@1.2-service.clearkey` | `running` | `hal` | `media` | `media mediadrm` | class=hal; user=media; group=media mediadrm; interface=android.hardware.drm@1.0::ICryptoFactory clearkey ; android.hardware.drm@1.0::IDrmFactory clearkey ; android.hardware.drm@1.1::ICryptoFactory clearkey ; android.hardware.drm@1.1::IDrmFactory clearkey ; android.hardware.drm@1.2::ICryptoFactory clearkey ; android.hardware.drm@1.2::IDrmFactory clearkey; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.drm@1.2-service.widevine.rc:5` | `vendor.move_data_sh` | `/system/bin/move_widevine_data.sh` | `` | `late_start` | `media` | `media mediadrm system` | class=late_start; user=media; group=media mediadrm system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/android.hardware.drm@1.2-service.widevine.rc:12` | `vendor.drm-widevine-hal-1-2` | `/vendor/bin/hw/android.hardware.drm@1.2-service.widevine` | `running` | `hal` | `media` | `media mediadrm drmrpc system` | class=hal; user=media; group=media mediadrm drmrpc system; interface=android.hardware.drm@1.0::ICryptoFactory widevine ; android.hardware.drm@1.0::IDrmFactory widevine ; android.hardware.drm@1.1::ICryptoFactory widevine ; android.hardware.drm@1.1::IDrmFactory widevine ; android.hardware.drm@1.2::ICryptoFactory widevine ; android.hardware.drm@1.2::IDrmFactory widevine ; android.hidl.base@1.0::IBase widevine; ioprio rt 4; writepid /dev/cpuset/foreground/tasks | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.gatekeeper@1.0-service.rc:1` | `vendor.gatekeeper-1-0` | `/vendor/bin/hw/android.hardware.gatekeeper@1.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.gnss@2.0-service-mediatek.rc:1` | `gnss_service` | `/vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek` | `running` | `hal` | `system` | `system gps` | class=hal; user=system; group=system gps | CONNECTIVITY |
| `vendor/init/android.hardware.graphics.allocator@2.0-service.rc:1` | `vendor.gralloc-2-0` | `/vendor/bin/hw/android.hardware.graphics.allocator@2.0-service` | `running` | `hal,animation` | `system` | `graphics drmrpc` | class=hal,animation; user=system; group=graphics drmrpc; capabilities=SYS_NICE; interface=android.hardware.graphics.allocator@2.0::IAllocator default; onrestart restart surfaceflinger | CAPABILITY_SERVICE |
| `vendor/init/android.hardware.graphics.composer@2.1-service.rc:1` | `vendor.hwcomposer-2-1` | `/vendor/bin/hw/android.hardware.graphics.composer@2.1-service` | `running` | `hal,animation` | `system` | `graphics drmrpc` | class=hal,animation; user=system; group=graphics drmrpc; capabilities=SYS_NICE; onrestart restart surfaceflinger; writepid /dev/cpuset/system-background/tasks | CAPABILITY_SERVICE |
| `vendor/init/android.hardware.health@2.0-service.rc:1` | `health-hal-2-0` | `/vendor/bin/hw/android.hardware.health@2.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system; capabilities=WAKE_ALARM | CAPABILITY_SERVICE |
| `vendor/init/android.hardware.keymaster@4.0-service.rc:1` | `vendor.keymaster-4-0` | `/vendor/bin/hw/android.hardware.keymaster@4.0-service` | `running` | `early_hal` | `system` | `system drmrpc` | class=early_hal; user=system; group=system drmrpc | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.light@2.0-service-mediatek.rc:1` | `light-hal-2-0` | `/vendor/bin/hw/android.hardware.light@2.0-service-mediatek` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system; interface=android.hardware.light@2.0::ILight default |  |
| `vendor/init/android.hardware.media.omx@1.0-service.rc:1` | `mediacodec` | `/vendor/bin/hw/android.hardware.media.omx@1.0-service` | `running` | `main` | `mediacodec` | `camera drmrpc mediadrm media_rw sdcard_rw media system audio inet net_bt net_bt_admin net_bw_acct sdcard_r` | class=main; user=mediacodec; group=camera drmrpc mediadrm media_rw sdcard_rw media system audio inet net_bt net_bt_admin net_bw_acct sdcard_r; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |  |
| `vendor/init/android.hardware.memtrack@1.0-service.rc:1` | `vendor.memtrack-hal-1-0` | `/vendor/bin/hw/android.hardware.memtrack@1.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system |  |
| `vendor/init/android.hardware.secure_element@1.0-service-mediatek.rc:1` | `mtk_secure_element_hal_service` | `/vendor/bin/hw/android.hardware.secure_element@1.0-service-mediatek` | `running` | `hal` | `secure_element` | `secure_element` | class=hal; user=secure_element; group=secure_element | SECURITY_SUBSTRATE |
| `vendor/init/android.hardware.sensors@2.0-service-mediatek.rc:1` | `vendor.sensors-hal-2-0` | `/vendor/bin/hw/android.hardware.sensors@2.0-service-mediatek` | `running` | `main` | `system` | `system` | class=main; user=system; group=system; rlimit rtprio 10 10 | SENSOR_SUBSTRATE |
| `vendor/init/android.hardware.thermal@1.0-service.rc:1` | `vendor.thermal-hal-1-0` | `/vendor/bin/hw/android.hardware.thermal@1.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system |  |
| `vendor/init/android.hardware.usb@1.1-service-mediatek.rc:16` | `vendor.usb-hal-1-1` | `/vendor/bin/hw/android.hardware.usb@1.1-service-mediatek` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system |  |
| `vendor/init/android.hardware.vibrator@1.0-service.rc:1` | `vendor.vibrator-1-0` | `/vendor/bin/hw/android.hardware.vibrator@1.0-service` | `running` | `hal` | `system` | `system` | class=hal; user=system; group=system |  |
| `vendor/init/android.hardware.wifi@1.0-service-lazy-mediatek.rc:1` | `vendor.wifi_hal_legacy` | `/vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek` | `running` | `hal` | `wifi` | `wifi gps` | class=hal; user=wifi; group=wifi gps; capabilities=NET_ADMIN NET_RAW SYS_MODULE; interface=android.hardware.wifi@1.0::IWifi default; disabled; oneshot | CAPABILITY_SERVICE;CONNECTIVITY;DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/atcid.rc:1` | `atcid-daemon-u` | `/vendor/bin/atcid` | `` | `hal,main` | `system` | `radio system media bluetooth wifi` | class=hal,main; user=system; group=radio system media bluetooth wifi; interface=vendor.mediatek.hardware.atci@1.0::IAtcid default; socket=adb_atci_socket stream 660 radio system; disabled; oneshot | DISABLED_DECLARED;ENGINEERING_DIAG;ONESHOT_DECLARED;SOCKET_SERVICE |
| `vendor/init/audiocmdservice_atci.rc:1` | `audio-daemon` | `/vendor/bin/audiocmdservice_atci` | `` | `main` | `system` | `system audio` | class=main; user=system; group=system audio; socket=atci-audio stream 666 system system; disabled; oneshot | DISABLED_DECLARED;ENGINEERING_DIAG;ONESHOT_DECLARED;SOCKET_SERVICE |
| `vendor/init/camerahalserver.rc:1` | `camerahalserver` | `/vendor/bin/hw/camerahalserver` | `running` | `main` | `cameraserver` | `audio camera input drmrpc sdcard_rw system media graphics` | class=main; user=cameraserver; group=audio camera input drmrpc sdcard_rw system media graphics; capabilities=SYS_NICE; ioprio rt 4; writepid /dev/cpuset/camera-daemon/tasks /dev/stune/top-app/tasks | CAPABILITY_SERVICE |
| `vendor/init/em_hidl_user.rc:1` | `emhidl_user` | `/vendor/bin/em_hidl` | `` | `hal` | `system` | `bluetooth` | class=hal; user=system; group=bluetooth; interface=vendor.mediatek.hardware.engineermode@1.2::IEmd EmHidlServer; disabled; oneshot | DISABLED_DECLARED;ENGINEERING_DIAG;ONESHOT_DECLARED |
| `vendor/init/fuelgauged_init.rc:1` | `fuelgauged` | `/vendor/bin/fuelgauged` | `running` | `core` | `system` | `system` | class=core; user=system; group=system |  |
| `vendor/init/fuelgauged_nvram_init.rc:1` | `fuelgauged_nvram` | `/vendor/bin/fuelgauged_nvram` | `stopped` | `main` | `system` | `system` | class=main; user=system; group=system; oneshot | ONESHOT_DECLARED |
| `vendor/init/gsm0710muxd.rc:1` | `vendor.gsm0710muxd` | `/vendor/bin/gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic` | `running` | `main` | `root` | `radio cache inet misc` | class=main; user=root; group=radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hostapd.android.rc:14` | `hostapd` | `/vendor/bin/hw/hostapd` | `` | `main` | `wifi` | `wifi net_raw net_admin` | class=main; user=wifi; group=wifi net_raw net_admin; capabilities=NET_ADMIN NET_RAW; interface=android.hardware.wifi.hostapd@1.0::IHostapd default ; android.hardware.wifi.hostapd@1.1::IHostapd default; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.connectivity.rc:34` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `` | `system` | `system` | user=system; group=system; oneshot | CONNECTIVITY;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.connectivity.rc:39` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `running` | `` | `system` | `system` | user=system; group=system | CONNECTIVITY;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.connectivity.rc:55` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `main` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet | CONNECTIVITY;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.project.rc:3` | `msensord` | `/system/bin/msensord` | `` | `` | `system` | `system` | user=system; group=system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;SENSOR_SUBSTRATE |
| `vendor/init/hw/factory_init.project.rc:8` | `akmd8963` | `/system/bin/akmd8963` | `` | `` | `system` | `system` | user=system; group=system; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.project.rc:21` | `spm_script` | `/vendor/bin/spm_loader` | `` | `` | `system` | `system` | user=system; group=system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:611` | `ueventd` | `/system/bin/ueventd` | `running` | `core` | `` | `` | class=core; u:r:ueventd:s0; critical | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.rc:616` | `console` | `/system/bin/sh` | `` | `core` | `shell` | `shell log readproc` | class=core; user=shell; group=shell log readproc; u:r:shell:s0; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.rc:634` | `adbd` | `/system/bin/adbd --root_seclabel=u:r:su:s0` | `running` | `core` | `` | `` | class=core; socket=adbd stream 660 system system; u:r:adbd:s0; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;SOCKET_SERVICE |
| `vendor/init/hw/factory_init.rc:644` | `thermal_manager` | `/vendor/bin/thermal_manager` | `stopped` | `` | `root` | `system` | user=root; group=system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:649` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/factory_init.rc:654` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `` | `system` | `radio system` | user=system; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/factory_init.rc:659` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/factory_init.rc:664` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `` | `system` | `radio system` | user=system; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/factory_init.rc:669` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/factory_init.rc:675` | `fuelgauged` | `/vendor/bin/fuelgauged` | `running` | `core` | `system` | `system` | class=core; user=system; group=system | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.rc:680` | `mdlogger` | `/system/bin/mdlogger` | `` | `main` | `shell` | `shell system radio sdcard_rw inet` | class=main; user=shell; group=shell system radio sdcard_rw inet; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:687` | `dualmdlogger` | `/system/bin/dualmdlogger` | `` | `main` | `shell` | `shell system radio sdcard_rw inet` | class=main; user=shell; group=shell system radio sdcard_rw inet; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:694` | `factory_no_image` | `/vendor/bin/factory` | `` | `` | `radio` | `radio system wifi media audio gps bluetooth wakelock` | user=radio; group=radio system wifi media audio gps bluetooth wakelock; capabilities=BLOCK_SUSPEND NET_RAW NET_ADMIN SYS_ADMIN SYS_BOOT | CAPABILITY_SERVICE;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/factory_init.rc:699` | `emdlogger1` | `/system/bin/emdlogger1` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:706` | `emdlogger2` | `/system/bin/emdlogger2` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:713` | `emdlogger3` | `/system/bin/emdlogger3` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:720` | `emdlogger5` | `/system/bin/emdlogger5` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:727` | `emdlogger6` | `/system/bin/emdlogger6` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/factory_init.rc:734` | `connsyslogger` | `/vendor/bin/connsyslogger` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/init.connectivity.rc:44` | `mnld` | `/vendor/bin/mnld` | `running` | `main` | `gps` | `gps inet misc sdcard_rw sdcard_r media_rw system radio wakelock` | class=main; user=gps; group=gps inet misc sdcard_rw sdcard_r media_rw system radio wakelock; socket=mnld stream 660 gps system | RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/hw/init.connectivity.rc:51` | `lbs_dbg` | `/system/bin/lbs_dbg` | `running` | `main` | `system` | `gps inet misc sdcard_rw sdcard_r media_rw system radio` | class=main; user=system; group=gps inet misc sdcard_rw sdcard_r media_rw system radio; socket=lbs_dbg stream 660 gps system | RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/hw/init.connectivity.rc:63` | `wpa_supplicant` | `/vendor/bin/hw/wpa_supplicant \\` | `` | `main` | `` | `` | class=main; interface=android.hardware.wifi.supplicant@1.0::ISupplicant default ; android.hardware.wifi.supplicant@1.1::ISupplicant default ; android.hardware.wifi.supplicant@1.2::ISupplicant default; socket=wpa_wlan0 dgram 660 wifi wifi; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;SOCKET_SERVICE |
| `vendor/init/hw/init.modem.rc:22` | `md_minilog_util` | `/vendor/bin/md_minilog_util` | `` | `` | `radio` | `` | user=radio; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.modem.rc:28` | `viarild` | `/vendor/bin/viarild -l /vendor/lib/libviatelecom-withuim-ril.so -- -d /dev/ttySDIO4` | `` | `core` | `` | `radio cache inet misc audio sdcard_r sdcard_rw log system` | class=core; group=radio cache inet misc audio sdcard_r sdcard_rw log system; socket=rild-via stream 660 radio radio ; msap_c2k_socket1 stream 660 radio radio ; msap_c2k_socket2 stream 660 radio radio ; msap_c2k_socket3 stream 660 radio radio ; msap_c2k_socket4 stream 660 radio radio ; rpc stream 660 radio system ; rildc-debug stream 660 radio system ; rild-ctclient stream 660 radio system ; rild-atci-c2k stream 660 radio system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/hw/init.modem.rc:47` | `permission_check` | `/vendor/bin/permission_check` | `` | `main` | `root` | `system radio` | class=main; user=root; group=system radio; oneshot | ENGINEERING_DIAG;ONESHOT_DECLARED |
| `vendor/init/hw/init.modem.rc:53` | `pppd_gprs` | `/system/bin/pppd_dt /dev/ttyACM0 debug noauth refuse-eap usepeerdns nodetach` | `` | `` | `root` | `radio cache inet misc` | user=root; group=radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.modem.rc:60` | `pppd` | `/system/bin/pppd file /data/misc/ppp/ppp_options` | `` | `` | `root` | `` | user=root; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6762.rc:960` | `bugreport` | `/system/bin/dumpstate -d -p -B -z \\` | `` | `late_start` | `` | `` | class=late_start; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6762.rc:1009` | `pppoe_wlan0` | `/vendor/bin/launchpppoe` | `` | `` | `root` | `radio cache inet misc` | user=root; group=radio cache inet misc; disabled; oneshot | CONNECTIVITY;DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6762.rc:1016` | `cmmb` | `/system/bin/cmmbsp` | `` | `main` | `root` | `system audio camera graphics inet net_bt net_bt_admin net_bw_acct media` | class=main; user=root; group=system audio camera graphics inet net_bt net_bt_admin net_bw_acct media; ioprio rt 4 |  |
| `vendor/init/hw/init.mt6762.rc:1076` | `meta_tst` | `/vendor/bin/meta_tst` | `` | `main` | `system` | `radio gps system wifi audio bluetooth media` | class=main; user=system; group=radio gps system wifi audio bluetooth media; capabilities=NET_RAW NET_ADMIN; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6762.rc:1086` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `` | `main` | `root` | `system` | class=main; user=root; group=system; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/init.mt6762.rc:1108` | `osi` | `/system/bin/osi` | `` | `main` | `root` | `` | class=main; user=root; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6765.rc:960` | `bugreport` | `/system/bin/dumpstate -d -p -B -z \\` | `` | `late_start` | `` | `` | class=late_start; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6765.rc:1009` | `pppoe_wlan0` | `/vendor/bin/launchpppoe` | `` | `` | `root` | `radio cache inet misc` | user=root; group=radio cache inet misc; disabled; oneshot | CONNECTIVITY;DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6765.rc:1016` | `cmmb` | `/system/bin/cmmbsp` | `` | `main` | `root` | `system audio camera graphics inet net_bt net_bt_admin net_bw_acct media` | class=main; user=root; group=system audio camera graphics inet net_bt net_bt_admin net_bw_acct media; ioprio rt 4 |  |
| `vendor/init/hw/init.mt6765.rc:1076` | `meta_tst` | `/vendor/bin/meta_tst` | `` | `main` | `system` | `radio gps system wifi audio bluetooth media` | class=main; user=system; group=radio gps system wifi audio bluetooth media; capabilities=NET_RAW NET_ADMIN; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/init.mt6765.rc:1086` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `` | `main` | `root` | `system` | class=main; user=root; group=system; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/init.mt6765.rc:1108` | `osi` | `/system/bin/osi` | `` | `main` | `root` | `` | class=main; user=root; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/init.project.rc:51` | `fuse_usbotg` | `/system/bin/sdcard -u 1023 -g 1023 -w 1023 -d /mnt/media_rw/usbotg /storage/usbotg` | `` | `late_start` | `` | `` | class=late_start; disabled | DISABLED_DECLARED |
| `vendor/init/hw/init.project.rc:55` | `spm_script` | `/vendor/bin/spm_loader` | `` | `core` | `system` | `system` | class=core; user=system; group=system; oneshot | ONESHOT_DECLARED |
| `vendor/init/hw/init.project.rc:65` | `remosaic_daemon` | `/vendor/bin/remosaic_daemon` | `` | `core` | `system` | `system` | class=core; user=system; group=system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/hw/meta_init.connectivity.rc:28` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `` | `system` | `system` | user=system; group=system; oneshot | CONNECTIVITY;FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/meta_init.connectivity.rc:33` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `running` | `` | `system` | `system` | user=system; group=system | CONNECTIVITY;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.connectivity.rc:49` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `main` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet | CONNECTIVITY;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.modem.rc:31` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/meta_init.modem.rc:36` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `` | `system` | `radio system` | user=system; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/meta_init.modem.rc:41` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/meta_init.modem.rc:46` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `` | `system` | `radio system` | user=system; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/meta_init.modem.rc:51` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/hw/meta_init.project.rc:10` | `spm_script` | `/vendor/bin/spm_loader` | `` | `` | `system` | `system` | user=system; group=system; oneshot | FACTORY_META_DIAGNOSTIC;ONESHOT_DECLARED |
| `vendor/init/hw/meta_init.rc:540` | `ueventd` | `/system/bin/ueventd` | `running` | `core` | `` | `` | class=core; u:r:ueventd:s0; critical | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:545` | `console` | `/system/bin/sh` | `` | `core` | `shell` | `shell log readproc` | class=core; user=shell; group=shell log readproc; u:r:shell:s0; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:564` | `adbd` | `/system/bin/adbd --root_seclabel=u:r:su:s0` | `running` | `core` | `` | `` | class=core; socket=adbd stream 660 system system; u:r:adbd:s0; disabled | DISABLED_DECLARED;FACTORY_META_DIAGNOSTIC;SOCKET_SERVICE |
| `vendor/init/hw/meta_init.rc:571` | `emdlogger1` | `/system/bin/emdlogger1` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:576` | `meta_tst` | `/vendor/bin/meta_tst` | `` | `` | `radio` | `radio system wifi audio bluetooth media gps` | user=radio; group=radio system wifi audio bluetooth media gps | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:580` | `connsyslogger` | `/system/bin/connsyslogger` | `` | `main` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:585` | `loghidlvendorservice` | `/vendor/bin/loghidlvendorservice` | `` | `main` | `shell` | `system shell radio` | class=main; user=shell; group=system shell radio | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:590` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `` | `main` | `shell` | `system shell radio` | class=main; user=shell; group=system shell radio | FACTORY_META_DIAGNOSTIC |
| `vendor/init/hw/meta_init.rc:596` | `fuelgauged` | `/vendor/bin/fuelgauged` | `running` | `core` | `system` | `system` | class=core; user=system; group=system | FACTORY_META_DIAGNOSTIC |
| `vendor/init/init.bip.rc:19` | `vendor.bip` | `/vendor/bin/bip` | `running` | `core` | `root` | `system log inet radio net_admin root` | class=core; user=root; group=system log inet radio net_admin root; socket=vendor.bip stream 660 root system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init.cccifsd.rc:22` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `core` | `radio` | `radio system root` | class=core; user=radio; group=radio system root; oneshot; priority -20; ioprio rt 4 | ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.cccifsd.rc:30` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `core` | `radio` | `radio system` | class=core; user=radio; group=radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.cccimdinit.rc:21` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `core` | `system` | `radio system` | class=core; user=system; group=radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.cccimdinit.rc:27` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `core` | `system` | `radio system` | class=core; user=system; group=radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.cccirpcd.rc:3` | `vendor.ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `running` | `core` | `radio` | `radio system` | class=core; user=radio; group=radio system; oneshot | ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.thermal.rc:1` | `thermal` | `/vendor/bin/thermal` | `running` | `main` | `system` | `system` | class=main; user=system; group=system |  |
| `vendor/init/init.thermal_manager.rc:1` | `thermal_manager` | `/vendor/bin/thermal_manager` | `stopped` | `main` | `root` | `system` | class=main; user=root; group=system; oneshot | ONESHOT_DECLARED |
| `vendor/init/init.thermalloadalgod.rc:1` | `thermalloadalgod` | `/vendor/bin/thermalloadalgod` | `running` | `main` | `system` | `system` | class=main; user=system; group=system |  |
| `vendor/init/init.volte_imcb.rc:3` | `vendor.volte_imcb` | `/vendor/bin/volte_imcb` | `running` | `main` | `root` | `system log inet radio` | class=main; user=root; group=system log inet radio; socket=volte_imsa1 stream 660 root radio ; volte_imsa2 stream 660 root radio ; volte_ut stream 660 root radio; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init.volte_imsm_93.rc:51` | `volte_imsm_93` | `/vendor/bin/volte_imsm_93` | `running` | `main` | `root` | `radio cache inet misc log system wifi` | class=main; user=root; group=radio cache inet misc log system wifi; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.volte_md_status.rc:8` | `volte_md_status` | `/system/vendor/bin/volte_md_status` | `running` | `core` | `root` | `radio log system` | class=core; user=root; group=radio log system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC |
| `vendor/init/init.volte_stack.rc:4` | `vendor.volte_stack` | `/vendor/bin/volte_stack` | `running` | `main` | `root` | `system log inet radio` | class=main; user=root; group=system log inet radio; socket=volte_stack stream 660 root system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init.volte_ua.rc:3` | `vendor.volte_ua` | `/vendor/bin/volte_ua` | `running` | `main` | `root` | `system log inet radio net_admin root` | class=main; user=root; group=system log inet radio net_admin root; socket=volte_ua stream 660 root system; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init.vtservice_hidl.rc:3` | `vtservice_hidl` | `/vendor/bin/hw/vtservice_hidl` | `running` | `hal` | `system` | `system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw` | class=hal; user=system; group=system camera graphics inet net_admin net_raw media sdcard_rw radio sdcard_r media_rw; socket=volte_vt stream 660 root system; ioprio rt 4 | SOCKET_SERVICE |
| `vendor/init/init.wfca.rc:5` | `wfca` | `/vendor/bin/wfca` | `running` | `core` | `root` | `system log inet radio net_admin root` | class=core; user=root; group=system log inet radio net_admin root; socket=wfca stream 660 root system ; wfca_rds stream 660 root system | RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init.wod.rc:26` | `vendor.epdg_wod` | `/vendor/bin/epdg_wod` | `running` | `late_start` | `root` | `root system log inet radio net_admin` | class=late_start; user=root; group=root system log inet radio net_admin; socket=wod_action stream 660 root system ; wod_sim stream 660 root system; disabled | DISABLED_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/init_connectivity.rc:10` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `early_hal` | `system` | `system` | class=early_hal; user=system; group=system; oneshot | CONNECTIVITY;ONESHOT_DECLARED |
| `vendor/init/init_connectivity.rc:16` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/ -o 1` | `running` | `early_hal` | `system` | `system` | class=early_hal; user=system; group=system | CONNECTIVITY |
| `vendor/init/init_connectivity.rc:39` | `stp_dump` | `/vendor/bin/stp_dump3` | `` | `core` | `system` | `system sdcard_rw misc` | class=core; user=system; group=system sdcard_rw misc; disabled | DISABLED_DECLARED;ENGINEERING_DIAG |
| `vendor/init/ipsec_mon.rc:1` | `ipsec_mon` | `/vendor/bin/ipsec_mon` | `running` | `main` | `system` | `net_admin net_raw system misc` | class=main; user=system; group=net_admin net_raw system misc; capabilities=NET_ADMIN NET_RAW | CAPABILITY_SERVICE;RADIO_IMS_WFC |
| `vendor/init/lbs_hidl_service.rc:1` | `lbs_hidl_service` | `/vendor/bin/lbs_hidl_service` | `running` | `hal` | `system` | `system gps radio inet sdcard_r sdcard_rw` | class=hal; user=system; group=system gps radio inet sdcard_r sdcard_rw | RADIO_IMS_WFC |
| `vendor/init/mtk_agpsd_p.rc:4` | `agpsd` | `/vendor/bin/mtk_agpsd` | `running` | `main` | `gps` | `gps radio inet sdcard_r sdcard_rw misc system` | class=main; user=gps; group=gps radio inet sdcard_r sdcard_rw misc system; socket=agpsd stream 660 gps system ; agpsd2 stream 660 gps inet ; agpsd3 stream 660 gps inet | RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/mtkrild.rc:5` | `vendor.ril-daemon-mtk` | `/vendor/bin/hw/mtkfusionrild -l libmtk-ril.so` | `running` | `main` | `radio` | `radio cache inet misc audio log readproc wakelock sdcard_r sdcard_rw system` | class=main; user=radio; group=radio cache inet misc audio log readproc wakelock sdcard_r sdcard_rw system; capabilities=BLOCK_SUSPEND NET_ADMIN NET_RAW; socket=sap_uim_socket1 stream 660 bluetooth bluetooth ; rild-oem stream 660 radio system ; volte_imsvt1 stream 660 root radio ; rild-atci stream 660 root radio; disabled; oneshot | CAPABILITY_SERVICE;DISABLED_DECLARED;ONESHOT_DECLARED;RADIO_IMS_WFC;SOCKET_SERVICE |
| `vendor/init/muxreport.rc:1` | `vendor.muxreport-daemon` | `/vendor/bin/muxreport` | `` | `core` | `radio` | `radio cache inet misc` | class=core; user=radio; group=radio cache inet misc; disabled; oneshot | DISABLED_DECLARED;ENGINEERING_DIAG;ONESHOT_DECLARED |
| `vendor/init/netdagent.rc:1` | `netdagent` | `/vendor/bin/netdagent` | `running` | `late_start` | `system` | `net_admin net_raw system misc` | class=late_start; user=system; group=net_admin net_raw system misc; capabilities=NET_ADMIN NET_RAW | CAPABILITY_SERVICE;ENGINEERING_DIAG |
| `vendor/init/nvram_daemon.rc:1` | `nvram_daemon` | `/vendor/bin/nvram_daemon` | `stopped` | `core` | `root` | `system radio wifi audio bluetooth media` | class=core; user=root; group=system radio wifi audio bluetooth media; oneshot | ONESHOT_DECLARED |
| `vendor/init/vendor.mediatek.hardware.gpu@1.0-service.rc:1` | `gpu-1-0` | `/vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service` | `running` | `hal` | `system` | `system graphics` | class=hal; user=system; group=system graphics |  |
| `vendor/init/vendor.mediatek.hardware.mms@1.3-service.rc:1` | `mms-1-3` | `/vendor/bin/hw/vendor.mediatek.hardware.mms@1.3-service` | `running` | `main` | `system` | `system graphics` | class=main; user=system; group=system graphics |  |
| `vendor/init/vendor.mediatek.hardware.mtkcodecservice@1.1-service.rc:1` | `mtkcodecservice-1-1` | `/vendor/bin/hw/vendor.mediatek.hardware.mtkcodecservice@1.1-service` | `stopped` | `hal` | `system` | `audio media sdcard_r` | class=hal; user=system; group=audio media sdcard_r; interface=vendor.mediatek.hardware.mtkcodecservice@1.1::IMtkCodecService default; disabled; oneshot | DISABLED_DECLARED;ONESHOT_DECLARED |
| `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc:1` | `power-hal-1-0` | `/vendor/bin/hw/vendor.mediatek.hardware.mtkpower@1.0-service` | `running` | `hal` | `system` | `system readproc wifi media media_rw camera audio` | class=hal; user=system; group=system readproc wifi media media_rw camera audio; capabilities=NET_ADMIN NET_RAW SYS_MODULE | CAPABILITY_SERVICE |
| `vendor/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc:1` | `nvram-hidl-1-1` | `/vendor/bin/hw/vendor.mediatek.hardware.nvram@1.1-service` | `running` | `hal` | `root` | `system bluetooth media audio radio` | class=hal; user=root; group=system bluetooth media audio radio |  |
| `vendor/init/vendor.mediatek.hardware.pq@2.2-service.rc:1` | `pq-2-2` | `/vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service` | `running` | `main` | `system` | `system graphics` | class=main; user=system; group=system graphics |  |
| `vendor/init/vndservicemanager.rc:1` | `vndservicemanager` | `/vendor/bin/vndservicemanager /dev/vndbinder` | `running` | `core` | `system` | `system readproc` | class=core; user=system; group=system readproc; writepid /dev/cpuset/system-background/tasks |  |
| `vendor/init/wlan_assistant.rc:1` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `main` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet | CONNECTIVITY |

################################################################################
# 1.9 — COMPLETE RUNTIME INIT.SVC STATE TABLE
################################################################################

| init.svc name | State | Matching source declaration |
|---|---|---|
| `adbd` | `running` | root/init.usb.rc:15; vendor/init/hw/factory_init.rc:634; vendor/init/hw/meta_init.rc:564 |
| `agpsd` | `running` | vendor/init/mtk_agpsd_p.rc:4 |
| `apexd` | `running` | system/init/apexd.rc:1 |
| `apexd-bootstrap` | `stopped` | system/init/apexd.rc:8 |
| `ashmemd` | `running` | system/init/ashmemd.rc:1 |
| `audioserver` | `running` | system/init/audioserver.rc:1 |
| `batterywarning` | `running` | system/init/batterywarning.rc:2 |
| `bluetooth-1-0` | `running` | vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:1 |
| `bootanim` | `stopped` | root/init.rc:855; system/init/mtkbootanim.rc:1 |
| `bootlogoupdater` | `stopped` | system/init/bootlogoupdater.rc:3 |
| `bpfloader` | `stopped` | system/init/bpfloader.rc:1 |
| `camerahalserver` | `running` | vendor/init/camerahalserver.rc:1 |
| `cameraserver` | `running` | system/init/cameraserver.rc:1 |
| `ccci3_mdinit` | `stopped` | vendor/init/hw/factory_init.rc:664; vendor/init/hw/meta_init.modem.rc:46; vendor/init/init.cccimdinit.rc:27 |
| `ccci_mdinit` | `running` | vendor/init/hw/factory_init.rc:654; vendor/init/hw/meta_init.modem.rc:36; vendor/init/init.cccimdinit.rc:21 |
| `drm` | `running` | system/init/drmserver.rc:1 |
| `flash_recovery` | `stopped` | root/init.rc:851 |
| `fuelgauged` | `running` | vendor/init/fuelgauged_init.rc:1; vendor/init/hw/factory_init.rc:675; vendor/init/hw/meta_init.rc:596 |
| `fuelgauged_nvram` | `stopped` | vendor/init/fuelgauged_nvram_init.rc:1 |
| `gatekeeperd` | `running` | system/init/gatekeeperd.rc:1 |
| `gnss_service` | `running` | vendor/init/android.hardware.gnss@2.0-service-mediatek.rc:1 |
| `gpu` | `running` | system/init/gpuservice.rc:1 |
| `gpu-1-0` | `running` | vendor/init/vendor.mediatek.hardware.gpu@1.0-service.rc:1 |
| `gsid` | `stopped` | system/init/gsid.rc:1 |
| `health-hal-2-0` | `running` | vendor/init/android.hardware.health@2.0-service.rc:1 |
| `hidl_memory` | `running` | system/init/android.hidl.allocator@1.0-service.rc:1 |
| `hwservicemanager` | `running` | system/init/hwservicemanager.rc:1 |
| `idmap2d` | `stopped` | system/init/idmap2d.rc:1 |
| `incidentd` | `running` | system/init/incidentd.rc:15 |
| `installd` | `running` | system/init/installd.rc:2 |
| `iorapd` | `stopped` | system/init/iorapd.rc:15 |
| `ipsec_mon` | `running` | vendor/init/ipsec_mon.rc:1 |
| `keystore` | `running` | system/init/keystore.rc:1 |
| `lbs_dbg` | `running` | vendor/init/hw/init.connectivity.rc:51 |
| `lbs_hidl_service` | `running` | vendor/init/lbs_hidl_service.rc:1 |
| `light-hal-2-0` | `running` | vendor/init/android.hardware.light@2.0-service-mediatek.rc:1 |
| `lmkd` | `running` | system/init/lmkd.rc:1 |
| `logd` | `running` | system/init/logd.rc:1 |
| `logd-auditctl` | `stopped` | system/init/logd.rc:20 |
| `logd-reinit` | `stopped` | system/init/logd.rc:12 |
| `mdnsd` | `running` | system/init/mdnsd.rc:1 |
| `media` | `running` | system/init/mediaserver.rc:1 |
| `media.swcodec` | `running` | <not in parsed source pack by exact service name> |
| `mediacodec` | `running` | vendor/init/android.hardware.media.omx@1.0-service.rc:1 |
| `mediadrm` | `running` | system/init/mediadrmserver.rc:1 |
| `mediaextractor` | `running` | system/init/mediaextractor.rc:1 |
| `mediametrics` | `running` | system/init/mediametrics.rc:1 |
| `mms-1-3` | `running` | vendor/init/vendor.mediatek.hardware.mms@1.3-service.rc:1 |
| `mnld` | `running` | vendor/init/hw/init.connectivity.rc:44 |
| `mobile_log_d` | `running` | system/init/mobile_log_d.rc:7 |
| `mtk_secure_element_hal_service` | `running` | vendor/init/android.hardware.secure_element@1.0-service-mediatek.rc:1 |
| `mtkcodecservice-1-1` | `stopped` | vendor/init/vendor.mediatek.hardware.mtkcodecservice@1.1-service.rc:1 |
| `netd` | `running` | system/init/netd.rc:1 |
| `netdagent` | `running` | vendor/init/netdagent.rc:1 |
| `netdiag` | `running` | system/init/netdiag.rc:27 |
| `nvram-hidl-1-1` | `running` | vendor/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc:1 |
| `nvram_daemon` | `stopped` | vendor/init/nvram_daemon.rc:1 |
| `power-hal-1-0` | `running` | vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc:1 |
| `pq-2-2` | `running` | vendor/init/vendor.mediatek.hardware.pq@2.2-service.rc:1 |
| `servicemanager` | `running` | system/init/servicemanager.rc:1 |
| `statsd` | `running` | system/init/statsd.rc:15 |
| `storaged` | `running` | system/init/storaged.rc:1 |
| `surfaceflinger` | `running` | system/init/surfaceflinger.rc:1 |
| `system_suspend` | `running` | system/init/android.system.suspend@1.0-service.rc:1 |
| `terservice` | `stopped` | system/init/terserver.rc:4 |
| `thermal` | `running` | vendor/init/init.thermal.rc:1 |
| `thermal_manager` | `stopped` | vendor/init/hw/factory_init.rc:644; vendor/init/init.thermal_manager.rc:1 |
| `thermald` | `running` | system/init/init.thermald.rc:1 |
| `thermalloadalgod` | `running` | vendor/init/init.thermalloadalgod.rc:1 |
| `tombstoned` | `running` | system/init/tombstoned.rc:1 |
| `ueventd` | `running` | root/init.rc:828; vendor/init/hw/factory_init.rc:611; vendor/init/hw/meta_init.rc:540 |
| `usbd` | `stopped` | system/init/usbd.rc:1 |
| `vendor.audio-hal-2-0` | `running` | vendor/init/android.hardware.audio@5.0-service-mediatek.rc:4 |
| `vendor.bip` | `running` | vendor/init/init.bip.rc:19 |
| `vendor.cas-hal-1-1` | `running` | vendor/init/android.hardware.cas@1.1-service.rc:1 |
| `vendor.ccci3_fsd` | `running` | vendor/init/hw/factory_init.rc:659; vendor/init/hw/meta_init.modem.rc:41; vendor/init/init.cccifsd.rc:30 |
| `vendor.ccci_fsd` | `running` | vendor/init/hw/factory_init.rc:649; vendor/init/hw/meta_init.modem.rc:31; vendor/init/init.cccifsd.rc:22 |
| `vendor.ccci_rpcd` | `running` | vendor/init/init.cccirpcd.rc:3 |
| `vendor.configstore-hal` | `running` | vendor/init/android.hardware.configstore@1.1-service.rc:1 |
| `vendor.drm-clearkey-hal-1-2` | `running` | vendor/init/android.hardware.drm@1.2-service.clearkey.rc:1 |
| `vendor.drm-hal-1-0` | `running` | vendor/init/android.hardware.drm@1.0-service.rc:1 |
| `vendor.drm-widevine-hal-1-2` | `running` | vendor/init/android.hardware.drm@1.2-service.widevine.rc:12 |
| `vendor.epdg_wod` | `running` | vendor/init/init.wod.rc:26 |
| `vendor.gatekeeper-1-0` | `running` | vendor/init/android.hardware.gatekeeper@1.0-service.rc:1 |
| `vendor.gralloc-2-0` | `running` | vendor/init/android.hardware.graphics.allocator@2.0-service.rc:1 |
| `vendor.gsm0710muxd` | `running` | vendor/init/gsm0710muxd.rc:1 |
| `vendor.hwcomposer-2-1` | `running` | vendor/init/android.hardware.graphics.composer@2.1-service.rc:1 |
| `vendor.keymaster-4-0` | `running` | vendor/init/android.hardware.keymaster@4.0-service.rc:1 |
| `vendor.memtrack-hal-1-0` | `running` | vendor/init/android.hardware.memtrack@1.0-service.rc:1 |
| `vendor.ril-daemon-mtk` | `running` | vendor/init/mtkrild.rc:5 |
| `vendor.sensors-hal-2-0` | `running` | vendor/init/android.hardware.sensors@2.0-service-mediatek.rc:1 |
| `vendor.thermal-hal-1-0` | `running` | vendor/init/android.hardware.thermal@1.0-service.rc:1 |
| `vendor.usb-hal-1-1` | `running` | vendor/init/android.hardware.usb@1.1-service-mediatek.rc:16 |
| `vendor.vibrator-1-0` | `running` | vendor/init/android.hardware.vibrator@1.0-service.rc:1 |
| `vendor.volte_imcb` | `running` | vendor/init/init.volte_imcb.rc:3 |
| `vendor.volte_stack` | `running` | vendor/init/init.volte_stack.rc:4 |
| `vendor.volte_ua` | `running` | vendor/init/init.volte_ua.rc:3 |
| `vendor.wifi_hal_legacy` | `running` | vendor/init/android.hardware.wifi@1.0-service-lazy-mediatek.rc:1 |
| `vndservicemanager` | `running` | vendor/init/vndservicemanager.rc:1 |
| `vold` | `running` | system/init/vold.rc:1 |
| `volte_imsm_93` | `running` | vendor/init/init.volte_imsm_93.rc:51 |
| `volte_md_status` | `running` | vendor/init/init.volte_md_status.rc:8 |
| `vtservice` | `running` | system/init/init.vtservice.rc:3 |
| `vtservice_hidl` | `running` | vendor/init/init.vtservice_hidl.rc:3 |
| `wfca` | `running` | vendor/init/init.wfca.rc:5 |
| `wificond` | `running` | system/init/wificond.rc:1 |
| `wlan_assistant` | `running` | vendor/init/hw/factory_init.connectivity.rc:55; vendor/init/hw/meta_init.connectivity.rc:49; vendor/init/wlan_assistant.rc:1 |
| `wmt_launcher` | `running` | vendor/init/hw/factory_init.connectivity.rc:39; vendor/init/hw/meta_init.connectivity.rc:33; vendor/init/init_connectivity.rc:16 |
| `wmt_loader` | `stopped` | vendor/init/hw/factory_init.connectivity.rc:34; vendor/init/hw/meta_init.connectivity.rc:28; vendor/init/init_connectivity.rc:10 |
| `zygote` | `running` | root/init.zygote32.rc:1; root/init.zygote64_32.rc:1 |
| `zygote_secondary` | `running` | root/init.zygote64_32.rc:17 |

################################################################################
# 1.10 — ACTION TRIGGER SUMMARY
################################################################################

| Trigger | Action block count | Command count |
|---|---:|---:|
| `boot` | 24 | 658 |
| `boot && property:persist.sys.usb.config=*` | 1 | 1 |
| `charger` | 6 | 101 |
| `defaultcrypto` | 3 | 3 |
| `early-boot` | 1 | 98 |
| `early-fs` | 3 | 3 |
| `early-init` | 10 | 56 |
| `early-init && property:ro.boot.boot_trace=1` | 3 | 5 |
| `encrypt` | 3 | 8 |
| `encrypt && property:ro.vendor.mtk_f2fs_enable=0` | 2 | 2 |
| `encrypt && property:ro.vendor.mtk_f2fs_enable=1` | 2 | 2 |
| `firmware_mounts_complete` | 3 | 3 |
| `fs` | 8 | 78 |
| `init` | 14 | 410 |
| `late-fs` | 3 | 18 |
| `late-init` | 6 | 216 |
| `load_persist_props_action` | 4 | 13 |
| `load_system_props_action` | 2 | 2 |
| `nonencrypted` | 3 | 6 |
| `post-fs` | 9 | 193 |
| `post-fs && property:ro.boot.bootreason=*` | 1 | 1 |
| `post-fs-data` | 34 | 1498 |
| `post-fs-data && property:init.svc.bootanim=running && property:ro.crypto.type=block` | 1 | 1 |
| `post-fs-data && property:ro.crypto.state=unencrypted` | 1 | 1 |
| `post-fs-data && property:ro.crypto.type=block` | 1 | 1 |
| `property:debug.atrace.user_initiated=1` | 1 | 1 |
| `property:init.svc.adbd=stopped` | 1 | 1 |
| `property:init.svc.mediadrm=running` | 1 | 2 |
| `property:init.svc.zygote=stopping` | 1 | 1 |
| `property:persist.debug.atrace.boottrace=1` | 1 | 1 |
| `property:persist.device_config.global_settings.sys_traced=0` | 1 | 1 |
| `property:persist.device_config.global_settings.sys_traced=1` | 1 | 1 |
| `property:persist.heapprofd.enable=0 && property:traced.lazy.heapprofd=0` | 1 | 1 |
| `property:persist.heapprofd.enable=1` | 1 | 1 |
| `property:persist.traced.enable=0` | 1 | 2 |
| `property:persist.traced.enable=1` | 1 | 3 |
| `property:persist.traced.enable=1 && property:debug.atrace.user_initiated=""` | 1 | 1 |
| `property:persist.vendor.connsys.coredump.mode=0` | 1 | 1 |
| `property:persist.vendor.connsys.coredump.mode=1` | 1 | 1 |
| `property:persist.vendor.connsys.coredump.mode=2` | 1 | 1 |
| `property:persist.vendor.mediatek.fg.disable=1` | 2 | 4 |
| `property:persist.vendor.mediatek.fg.force25c=0` | 2 | 4 |
| `property:persist.vendor.mediatek.fg.force25c=1` | 2 | 4 |
| `property:persist.vendor.mediatek.fg.log.enable=1` | 2 | 6 |
| `property:persist.vendor.operator.optr=OP12` | 1 | 1 |
| `property:persist.vendor.radio.port_index=""` | 1 | 5 |
| `property:persist.vendor.radio.port_index="1"` | 1 | 1 |
| `property:persist.vendor.radio.port_index="2"` | 1 | 1 |
| `property:persist.vendor.radio.port_index="3"` | 1 | 1 |
| `property:persist.vendor.radio.port_index="4"` | 1 | 1 |
| `property:persist.vendor.service.atci.atm_mode=1` | 1 | 0 |
| `property:persist.vendor.service.atci.autostart=1` | 1 | 0 |
| `property:persist.vendor.uartconsole.enable=*` | 2 | 2 |
| `property:persist.vendor.usb.printk=*` | 1 | 1 |
| `property:ro.board.platform=mt6753` | 2 | 6 |
| `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | 2 | 3 |
| `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | 1 | 6 |
| `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 && property:vendor.usb.feature=0` | 1 | 1 |
| `property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1` | 1 | 2 |
| `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | 1 | 2 |
| `property:ro.build.type=eng` | 1 | 1 |
| `property:ro.debuggable=1` | 3 | 7 |
| `property:ro.init.updatable_crashing=1` | 1 | 1 |
| `property:ro.iorapd.enable=false` | 1 | 1 |
| `property:ro.statsd.enable=false` | 1 | 1 |
| `property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting` | 1 | 2 |
| `property:ro.vendor.mtklog_internal=1` | 2 | 2 |
| `property:security.perf_harden=0` | 1 | 4 |
| `property:security.perf_harden=1` | 1 | 1 |
| `property:sys.boot_completed=1` | 12 | 73 |
| `property:sys.boot_completed=1 && property:ro.boot.atm=enable` | 2 | 6 |
| `property:sys.boot_completed=1 && property:sys.logbootcomplete=1` | 1 | 1 |
| `property:sys.boot_completed=1 && property:sys.wifitracing.started=0` | 1 | 25 |
| `property:sys.boot_completed=1 && property:wifi.interface=* && sys.wifitracing.started=1` | 1 | 1 |
| `property:sys.boot_from_charger_mode=1` | 3 | 6 |
| `property:sys.init_log_level=*` | 3 | 3 |
| `property:sys.lpdumpd=start` | 1 | 1 |
| `property:sys.lpdumpd=stop` | 1 | 1 |
| `property:sys.powerctl=*` | 1 | 1 |
| `property:sys.rss_hwm_reset.on=1` | 1 | 2 |
| `property:sys.sysctl.extra_free_kbytes=*` | 1 | 1 |
| `property:sys.sysctl.tcp_def_init_rwnd=*` | 1 | 1 |
| `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | 1 | 6 |
| `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | 2 | 6 |
| `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | 1 | 7 |
| `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | 2 | 3 |
| `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | 1 | 6 |
| `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | 2 | 7 |
| `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | 1 | 7 |
| `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | 2 | 3 |
| `property:sys.usb.config=acm_gs0` | 1 | 7 |
| `property:sys.usb.config=acm_gs0gs1` | 1 | 7 |
| `property:sys.usb.config=acm_gs0gs3` | 1 | 7 |
| `property:sys.usb.config=acm_gs1` | 1 | 7 |
| `property:sys.usb.config=acm_gs1gs3` | 1 | 7 |
| `property:sys.usb.config=acm_gs2` | 1 | 7 |
| `property:sys.usb.config=acm_gs3` | 1 | 7 |
| `property:sys.usb.config=acm_third` | 1 | 1 |
| `property:sys.usb.config=adb && property:sys.usb.configfs=0` | 1 | 7 |
| `property:sys.usb.config=adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | 1 | 3 |
| `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | 1 | 3 |
| `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | 1 | 6 |
| `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | 2 | 6 |
| `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | 1 | 7 |
| `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | 2 | 3 |
| `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | 1 | 7 |
| `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | 1 | 10 |
| `property:sys.usb.config=bicr,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | 1 | 7 |
| `property:sys.usb.config=charging_no` | 1 | 4 |
| `property:sys.usb.config=charging_yes` | 1 | 4 |
| `property:sys.usb.config=gs0gs1` | 1 | 1 |
| `property:sys.usb.config=gs0gs3` | 1 | 1 |
| `property:sys.usb.config=gs1gs3` | 2 | 11 |
| `property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1` | 1 | 2 |
| `property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1` | 1 | 2 |
| `property:sys.usb.config=gs3` | 1 | 1 |
| `property:sys.usb.config=gs3,dual_acm` | 1 | 1 |
| `property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1` | 1 | 2 |
| `property:sys.usb.config=hid && property:sys.usb.configfs=1` | 1 | 7 |
| `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | 1 | 5 |
| `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | 1 | 8 |
| `property:sys.usb.config=mass_storage,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=mass_storage,adb,acm` | 1 | 1 |
| `property:sys.usb.config=midi && property:sys.usb.configfs=1` | 2 | 5 |
| `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | 2 | 2 |
| `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | 1 | 4 |
| `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | 1 | 6 |
| `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | 1 | 9 |
| `property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=mtp,gs0gs1` | 1 | 1 |
| `property:sys.usb.config=mtp,gs0gs3` | 1 | 1 |
| `property:sys.usb.config=mtp,gs1gs3` | 1 | 1 |
| `property:sys.usb.config=mtp,gs3` | 1 | 1 |
| `property:sys.usb.config=mtp,gs3,dual_acm` | 1 | 1 |
| `property:sys.usb.config=none && property:sys.usb.configfs=0` | 1 | 4 |
| `property:sys.usb.config=none && property:sys.usb.configfs=1` | 2 | 15 |
| `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | 1 | 4 |
| `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | 1 | 5 |
| `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | 1 | 8 |
| `property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | 1 | 6 |
| `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | 1 | 9 |
| `property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=rndis,adb,gs0gs1` | 1 | 1 |
| `property:sys.usb.config=rndis,adb,gs0gs3` | 1 | 1 |
| `property:sys.usb.config=rndis,gs0gs1` | 1 | 1 |
| `property:sys.usb.config=rndis,gs0gs3` | 1 | 1 |
| `property:sys.usb.config=rndis,gs1gs3` | 1 | 1 |
| `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | 1 | 8 |
| `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | 1 | 9 |
| `property:sys.usb.config=via_bypass,adb && property:sys.usb.configfs=1` | 1 | 1 |
| `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | 1 |
| `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \\` | 1 | 2 |
| `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | 1 |
| `property:sys.usb.config=via_bypass,gs1gs3` | 1 | 1 |
| `property:sys.usb.ffs.ready=0` | 1 | 1 |
| `property:sys.usb.ffs.ready=1` | 1 | 1 |
| `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | 1 | 8 |
| `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | 1 | 8 |
| `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | 1 | 12 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | 1 | 6 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | 2 | 14 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | 1 | 4 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | 2 | 19 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | 1 | 9 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | 1 | 10 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | 1 | 10 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | 2 | 15 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | 2 | 17 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | 2 | 15 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | 1 | 5 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | 2 | 17 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | 1 | 6 |
| `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | 3 | 28 |
| `property:sys.usb.typec.data_role=device` | 1 | 2 |
| `property:sys.usb.typec.data_role=host` | 1 | 2 |
| `property:sys.usb.typec.mode=dfp` | 1 | 2 |
| `property:sys.usb.typec.mode=ufp` | 1 | 2 |
| `property:sys.usb.typec.power_role=sink` | 1 | 2 |
| `property:sys.usb.typec.power_role=source` | 1 | 2 |
| `property:traced.lazy.heapprofd=1` | 1 | 1 |
| `property:vendor.MB.control=copy` | 1 | 1 |
| `property:vendor.MB.ftrace.log=bsp` | 1 | 1 |
| `property:vendor.MB.ftrace.log=mmedia` | 1 | 1 |
| `property:vendor.MB.ftrace.log=off` | 1 | 1 |
| `property:vendor.MB.prdebug=0` | 1 | 1 |
| `property:vendor.MB.prdebug=1` | 1 | 2 |
| `property:vendor.MB.sublog_config=*` | 1 | 1 |
| `property:vendor.connsys.driver.ready=yes` | 11 | 17 |
| `property:vendor.connsys.driver.ready=yes && property:debug.vendor.connsys.suspend.alarm=*` | 1 | 1 |
| `property:vendor.connsys.driver.ready=yes && property:persist.vendor.connsys.coredump.mode=*` | 1 | 1 |
| `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | 2 | 12 |
| `property:vendor.em.usb.bypass.disable=0` | 1 | 1 |
| `property:vendor.em.usb.bypass.disable=1` | 1 | 1 |
| `property:vendor.em.usb.bypass.disable=2` | 1 | 1 |
| `property:vendor.em.usb.bypass.disable=3` | 1 | 1 |
| `property:vendor.em.usb.bypass.disable=4` | 1 | 1 |
| `property:vendor.em.usb.bypass.enable=0` | 1 | 1 |
| `property:vendor.em.usb.bypass.enable=1` | 1 | 1 |
| `property:vendor.em.usb.bypass.enable=2` | 1 | 1 |
| `property:vendor.em.usb.bypass.enable=3` | 1 | 1 |
| `property:vendor.em.usb.bypass.enable=4` | 1 | 1 |
| `property:vendor.em.usb.set=RG_USB20_HSTX_SRCTRL` | 1 | 1 |
| `property:vendor.em.usb.set=RG_USB20_INTR_EN` | 1 | 1 |
| `property:vendor.em.usb.set=RG_USB20_PHY_REV6` | 1 | 1 |
| `property:vendor.em.usb.set=RG_USB20_TERM_VREF_SEL` | 1 | 1 |
| `property:vendor.em.usb.set=RG_USB20_VRT_VREF_SEL` | 1 | 1 |
| `property:vendor.em.usb.set=USB_DRIVING_CAPABILITY` | 1 | 1 |
| `property:vendor.ril.mtk=1` | 1 | 1 |
| `property:vendor.ril.muxreport=1` | 1 | 1 |
| `property:vendor.usb.bicr=no` | 1 | 2 |
| `property:vendor.usb.bicr=yes` | 1 | 2 |
| `property:vendor.usb.charging=no` | 1 | 1 |
| `property:vendor.usb.charging=yes` | 1 | 1 |
| `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | 2 | 22 |
| `property:vendor.usb.config=*` | 1 | 1 |
| `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0 && \\` | 1 | 1 |
| `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0gs3 && \\` | 1 | 1 |
| `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | 1 | 13 |
| `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | 1 | 13 |
| `property:vendor.usb.port.mode=uart` | 1 | 1 |
| `property:vendor.usb.port.mode=usb` | 1 | 1 |
| `property:vendor.usb.printk=*` | 1 | 1 |
| `property:vendor.usb.test=*` | 1 | 2 |
| `property:vendor.volte_md_status=exception` | 3 | 10 |
| `property:vendor.volte_md_status=flightmode` | 3 | 10 |
| `property:vendor.volte_md_status=ready` | 2 | 5 |
| `property:vendor.volte_md_status=ready && property:persist.vendor.mtk_wfc_support=1` | 1 | 1 |
| `property:vendor.volte_md_status=reset` | 3 | 10 |
| `property:vendor.volte_md_status=stop` | 3 | 10 |
| `property:vold.decrypt=trigger_default_encryption` | 1 | 1 |
| `property:vold.decrypt=trigger_encryption` | 1 | 2 |
| `property:vold.decrypt=trigger_load_persist_props` | 3 | 11 |
| `property:vold.decrypt=trigger_post_fs_data` | 5 | 8 |
| `property:vold.decrypt=trigger_reset_main` | 5 | 9 |
| `property:vold.decrypt=trigger_restart_framework` | 6 | 20 |
| `property:vold.decrypt=trigger_restart_min_framework` | 5 | 10 |
| `property:vold.decrypt=trigger_shutdown_framework` | 6 | 37 |
| `property:vts.native_server.on=0` | 1 | 1 |
| `property:vts.native_server.on=0 && property:ro.build.type=eng` | 1 | 1 |
| `property:vts.native_server.on=0 && property:ro.build.type=userdebug` | 1 | 1 |
| `property:vts.native_server.on=1` | 1 | 1 |
| `property:vts.native_server.on=1 && property:ro.build.type=eng` | 1 | 1 |
| `property:vts.native_server.on=1 && property:ro.build.type=userdebug` | 1 | 1 |
| `zygote-start` | 1 | 1 |
| `zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file` | 3 | 12 |
| `zygote-start && property:ro.crypto.state=unencrypted` | 3 | 12 |
| `zygote-start && property:ro.crypto.state=unsupported` | 3 | 12 |

Action verb distribution across all parsed init action commands:

| Verb | Count |
|---|---:|
| `chown` | 1128 |
| `chmod` | 1025 |
| `write` | 676 |
| `mkdir` | 554 |
| `setprop` | 329 |
| `symlink` | 200 |
| `start` | 179 |
| `trigger` | 43 |
| `stop` | 43 |
| `mount` | 39 |
| `rm` | 35 |
| `class_start` | 31 |
| `restorecon_recursive` | 25 |
| `copy` | 24 |
| `exec_start` | 20 |
| `insmod` | 19 |
| `restorecon` | 18 |
| `exec` | 18 |
| `export` | 13 |
| `setrlimit` | 9 |
| `class_reset` | 8 |
| `load_persist_props` | 6 |
| `wait_for_prop` | 6 |
| `restart` | 6 |
| `exec_background` | 5 |
| `class_start_post_data` | 4 |
| `mount_all` | 4 |
| `class_stop` | 3 |
| `bootchart` | 3 |
| `ifup` | 3 |
| `hostname` | 3 |
| `domainname` | 3 |
| `loglevel` | 3 |
| `load_system_props` | 3 |
| `sysclktz` | 2 |
| `mark_post_data` | 2 |
| `installkey` | 2 |
| `enter_default_mount_ns` | 2 |
| `parse_apex_configs` | 2 |
| `init_user0` | 2 |
| `class_reset_post_data` | 2 |
| `rmdir` | 2 |
| `powerctl` | 1 |
| `swapon_all` | 1 |

################################################################################
# 1.11 — COMPLETE ACTION BLOCK CATALOG
################################################################################

| File:Line | Trigger | Command count | Command verbs |
|---|---|---:|---|
| `root/init.environ.rc:2` | `early-init` | 12 | export:12 |
| `root/init.rc:15` | `early-init` | 10 | mkdir:3, restorecon:2, write:1, chown:1, chmod:1, start:1, exec_start:1 |
| `root/init.rc:42` | `init` | 164 | chown:41, write:30, mkdir:29, chmod:29, copy:12, symlink:11, start:4, mount:3, setrlimit:2, sysclktz:1, restorecon_recursive:1, export:1 |
| `root/init.rc:306` | `property:sys.boot_from_charger_mode=1` | 2 | class_stop:1, trigger:1 |
| `root/init.rc:310` | `load_persist_props_action` | 3 | start:2, load_persist_props:1 |
| `root/init.rc:316` | `firmware_mounts_complete` | 1 | rm:1 |
| `root/init.rc:320` | `late-init` | 10 | trigger:10 |
| `root/init.rc:353` | `early-fs` | 1 | start:1 |
| `root/init.rc:357` | `post-fs` | 29 | chmod:8, mkdir:8, chown:6, mount:3, restorecon_recursive:2, exec:1, restorecon:1 |
| `root/init.rc:413` | `late-fs` | 2 | chmod:1, class_start:1 |
| `root/init.rc:421` | `post-fs-data` | 131 | mkdir:93, chmod:9, chown:8, copy:4, exec:3, start:2, restorecon:2, mark_post_data:1, installkey:1, bootchart:1, enter_default_mount_ns:1, write:1, symlink:1, wait_for_prop:1, parse_apex_configs:1, init_user0:1, setprop:1 |
| `root/init.rc:633` | `zygote-start && property:ro.crypto.state=unencrypted` | 4 | start:3, exec_start:1 |
| `root/init.rc:640` | `zygote-start && property:ro.crypto.state=unsupported` | 4 | start:3, exec_start:1 |
| `root/init.rc:647` | `zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file` | 4 | start:3, exec_start:1 |
| `root/init.rc:654` | `boot` | 75 | chown:47, chmod:16, write:6, class_start:2, ifup:1, hostname:1, domainname:1, setprop:1 |
| `root/init.rc:758` | `nonencrypted` | 2 | class_start:2 |
| `root/init.rc:762` | `property:sys.init_log_level=*` | 1 | loglevel:1 |
| `root/init.rc:765` | `charger` | 1 | class_start:1 |
| `root/init.rc:768` | `property:vold.decrypt=trigger_load_persist_props` | 3 | start:2, load_persist_props:1 |
| `root/init.rc:773` | `property:vold.decrypt=trigger_post_fs_data` | 2 | trigger:2 |
| `root/init.rc:777` | `property:vold.decrypt=trigger_restart_min_framework` | 4 | class_start_post_data:2, exec_start:1, class_start:1 |
| `root/init.rc:784` | `property:vold.decrypt=trigger_restart_framework` | 7 | class_start_post_data:2, class_start:2, exec_start:1, setprop:1, start:1 |
| `root/init.rc:794` | `property:vold.decrypt=trigger_shutdown_framework` | 4 | class_reset:2, class_reset_post_data:2 |
| `root/init.rc:800` | `property:sys.boot_completed=1` | 1 | bootchart:1 |
| `root/init.rc:806` | `property:sys.sysctl.extra_free_kbytes=*` | 1 | write:1 |
| `root/init.rc:810` | `property:sys.sysctl.tcp_def_init_rwnd=*` | 1 | write:1 |
| `root/init.rc:813` | `property:security.perf_harden=0` | 4 | write:4 |
| `root/init.rc:819` | `property:security.perf_harden=1` | 1 | write:1 |
| `root/init.rc:843` | `property:ro.debuggable=1` | 3 | chmod:2, start:1 |
| `root/init.usb.configfs.rc:1` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | 11 | write:4, rm:3, setprop:2, stop:1, rmdir:1 |
| `root/init.usb.configfs.rc:14` | `property:init.svc.adbd=stopped` | 1 | setprop:1 |
| `root/init.usb.configfs.rc:17` | `property:sys.usb.config=adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:20` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:26` | `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:32` | `property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:35` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:42` | `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:48` | `property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:51` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:58` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:64` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:67` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:74` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:80` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:83` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:90` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:97` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:100` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | 6 | symlink:3, write:2, setprop:1 |
| `root/init.usb.configfs.rc:108` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | 4 | write:2, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:114` | `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:117` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | 5 | write:2, symlink:2, setprop:1 |
| `root/init.usb.configfs.rc:124` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | 5 | write:2, mkdir:1, symlink:1, setprop:1 |
| `root/init.usb.configfs.rc:131` | `property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `root/init.usb.configfs.rc:134` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | 6 | write:2, symlink:2, mkdir:1, setprop:1 |
| `root/init.usb.rc:6` | `post-fs-data` | 6 | chown:2, chmod:2, mkdir:2 |
| `root/init.usb.rc:21` | `boot` | 1 | setprop:1 |
| `root/init.usb.rc:25` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | 4 | write:2, stop:1, setprop:1 |
| `root/init.usb.rc:34` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | 7 | write:5, start:1, setprop:1 |
| `root/init.usb.rc:44` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | 6 | write:5, setprop:1 |
| `root/init.usb.rc:53` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | 7 | write:5, start:1, setprop:1 |
| `root/init.usb.rc:63` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | 6 | write:5, setprop:1 |
| `root/init.usb.rc:72` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | 7 | write:5, start:1, setprop:1 |
| `root/init.usb.rc:82` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | 6 | write:5, setprop:1 |
| `root/init.usb.rc:91` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | 7 | write:5, start:1, setprop:1 |
| `root/init.usb.rc:102` | `boot && property:persist.sys.usb.config=*` | 1 | setprop:1 |
| `root/init.usb.rc:110` | `property:sys.usb.typec.mode=dfp` | 2 | write:1, setprop:1 |
| `root/init.usb.rc:114` | `property:sys.usb.typec.mode=ufp` | 2 | write:1, setprop:1 |
| `root/init.usb.rc:119` | `property:sys.usb.typec.data_role=device` | 2 | write:1, setprop:1 |
| `root/init.usb.rc:123` | `property:sys.usb.typec.data_role=host` | 2 | write:1, setprop:1 |
| `root/init.usb.rc:128` | `property:sys.usb.typec.power_role=source` | 2 | write:1, setprop:1 |
| `root/init.usb.rc:132` | `property:sys.usb.typec.power_role=sink` | 2 | write:1, setprop:1 |
| `system/init/art_apex_boot_integrity.rc:19` | `post-fs-data` | 1 | exec:1 |
| `system/init/atrace.rc:3` | `late-init` | 181 | chmod:178, write:3 |
| `system/init/atrace.rc:205` | `property:persist.debug.atrace.boottrace=1` | 1 | start:1 |
| `system/init/audioserver.rc:15` | `property:vold.decrypt=trigger_reset_main` | 1 | restart:1 |
| `system/init/audioserver.rc:17` | `property:vts.native_server.on=1` | 1 | stop:1 |
| `system/init/audioserver.rc:19` | `property:vts.native_server.on=0` | 1 | start:1 |
| `system/init/audioserver.rc:22` | `init` | 1 | mkdir:1 |
| `system/init/batterywarning.rc:8` | `property:sys.boot_completed=1` | 1 | start:1 |
| `system/init/bootstat.rc:6` | `post-fs && property:ro.boot.bootreason=*` | 1 | setprop:1 |
| `system/init/bootstat.rc:9` | `post-fs-data` | 30 | chown:29, mkdir:1 |
| `system/init/bootstat.rc:50` | `post-fs-data && property:init.svc.bootanim=running && property:ro.crypto.type=block` | 1 | exec_background:1 |
| `system/init/bootstat.rc:63` | `boot` | 1 | setprop:1 |
| `system/init/bootstat.rc:68` | `property:init.svc.zygote=stopping` | 1 | setprop:1 |
| `system/init/bootstat.rc:72` | `zygote-start` | 1 | exec_background:1 |
| `system/init/bootstat.rc:78` | `property:sys.boot_completed=1 && property:sys.logbootcomplete=1` | 1 | exec_background:1 |
| `system/init/dumpstate.rc:1` | `boot` | 2 | chown:1, chmod:1 |
| `system/init/flags_health_check.rc:1` | `load_persist_props_action` | 2 | mkdir:1, exec:1 |
| `system/init/flags_health_check.rc:7` | `property:sys.boot_completed=1` | 1 | setprop:1 |
| `system/init/flags_health_check.rc:10` | `property:ro.init.updatable_crashing=1` | 1 | exec:1 |
| `system/init/gsid.rc:6` | `post-fs` | 2 | mkdir:2 |
| `system/init/gsid.rc:10` | `post-fs-data` | 2 | mkdir:2 |
| `system/init/gsid.rc:14` | `boot` | 1 | exec_background:1 |
| `system/init/heapprofd.rc:29` | `property:persist.heapprofd.enable=1` | 1 | start:1 |
| `system/init/heapprofd.rc:32` | `property:traced.lazy.heapprofd=1` | 1 | start:1 |
| `system/init/heapprofd.rc:35` | `property:persist.heapprofd.enable=0 && property:traced.lazy.heapprofd=0` | 1 | stop:1 |
| `system/init/hw/vendor_init_as_system.rc:1` | `post-fs-data` | 135 | mkdir:96, chmod:9, chown:8, copy:4, exec:3, start:2, restorecon:2, setprop:2, mark_post_data:1, installkey:1, bootchart:1, enter_default_mount_ns:1, write:1, symlink:1, wait_for_prop:1, parse_apex_configs:1, init_user0:1 |
| `system/init/incidentd.rc:21` | `post-fs-data` | 1 | mkdir:1 |
| `system/init/init.connectivity.rc:3` | `post-fs-data` | 3 | mkdir:3 |
| `system/init/installd.rc:5` | `early-boot` | 98 | mkdir:98 |
| `system/init/iorapd.rc:24` | `property:ro.iorapd.enable=false` | 1 | stop:1 |
| `system/init/iorapd.rc:27` | `post-fs-data` | 1 | mkdir:1 |
| `system/init/kpoc_charger.rc:1` | `charger` | 1 | start:1 |
| `system/init/logd.rc:27` | `fs` | 3 | write:1, chown:1, chmod:1 |
| `system/init/logd.rc:33` | `property:sys.boot_completed=1` | 1 | start:1 |
| `system/init/lpdumpd.rc:31` | `property:sys.lpdumpd=start` | 1 | start:1 |
| `system/init/lpdumpd.rc:34` | `property:sys.lpdumpd=stop` | 1 | stop:1 |
| `system/init/mobile_log_d.rc:1` | `post-fs-data` | 3 | mkdir:3 |
| `system/init/mobile_log_d.rc:30` | `property:vendor.MB.control=copy` | 1 | start:1 |
| `system/init/mobile_log_d.rc:33` | `property:vendor.MB.sublog_config=*` | 1 | start:1 |
| `system/init/mobile_log_d.rc:37` | `property:vendor.MB.ftrace.log=mmedia` | 1 | start:1 |
| `system/init/mobile_log_d.rc:40` | `property:vendor.MB.ftrace.log=bsp` | 1 | start:1 |
| `system/init/mobile_log_d.rc:43` | `property:vendor.MB.ftrace.log=off` | 1 | start:1 |
| `system/init/mobile_log_d.rc:47` | `property:vendor.MB.prdebug=1` | 2 | write:2 |
| `system/init/mobile_log_d.rc:50` | `property:vendor.MB.prdebug=0` | 1 | write:1 |
| `system/init/netdiag.rc:1` | `boot` | 17 | chown:16, chmod:1 |
| `system/init/netdiag.rc:24` | `post-fs-data` | 1 | mkdir:1 |
| `system/init/perfetto.rc:38` | `property:persist.device_config.global_settings.sys_traced=1` | 1 | setprop:1 |
| `system/init/perfetto.rc:41` | `property:persist.device_config.global_settings.sys_traced=0` | 1 | setprop:1 |
| `system/init/perfetto.rc:44` | `property:debug.atrace.user_initiated=1` | 1 | stop:1 |
| `system/init/perfetto.rc:47` | `property:persist.traced.enable=1 && property:debug.atrace.user_initiated=""` | 1 | start:1 |
| `system/init/perfetto.rc:50` | `property:persist.traced.enable=1` | 3 | start:2, mkdir:1 |
| `system/init/perfetto.rc:62` | `property:persist.traced.enable=0` | 2 | stop:2 |
| `system/init/recovery-persist.rc:1` | `post-fs-data` | 2 | mkdir:1, exec_background:1 |
| `system/init/rss_hwm_reset.rc:24` | `property:sys.rss_hwm_reset.on=1` | 2 | start:1, setprop:1 |
| `system/init/statsd.rc:22` | `property:ro.statsd.enable=false` | 1 | stop:1 |
| `system/init/statsd.rc:25` | `post-fs-data` | 4 | mkdir:4 |
| `system/init/vdc.rc:2` | `defaultcrypto` | 1 | exec:1 |
| `system/init/vdc.rc:8` | `encrypt` | 2 | start:1, exec:1 |
| `system/init/wifi-events.rc:17` | `fs` | 1 | setprop:1 |
| `system/init/wifi-events.rc:20` | `property:sys.boot_completed=1 && property:sys.wifitracing.started=0` | 25 | write:16, chown:3, chmod:3, mkdir:1, restorecon_recursive:1, setprop:1 |
| `system/init/wifi-events.rc:62` | `property:sys.boot_completed=1 && property:wifi.interface=* && sys.wifitracing.started=1` | 1 | write:1 |
| `vendor/init/android.hardware.audio@5.0-service-mediatek.rc:1` | `post-fs-data` | 1 | mkdir:1 |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:8` | `property:vts.native_server.on=1 && property:ro.build.type=userdebug` | 1 | stop:1 |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:10` | `property:vts.native_server.on=1 && property:ro.build.type=eng` | 1 | stop:1 |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:12` | `property:vts.native_server.on=0 && property:ro.build.type=userdebug` | 1 | start:1 |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:14` | `property:vts.native_server.on=0 && property:ro.build.type=eng` | 1 | start:1 |
| `vendor/init/android.hardware.drm@1.2-service.widevine.rc:1` | `property:init.svc.mediadrm=running` | 2 | mkdir:1, start:1 |
| `vendor/init/android.hardware.usb@1.1-service-mediatek.rc:1` | `boot` | 12 | chown:6, chmod:6 |
| `vendor/init/atcid.rc:11` | `property:persist.vendor.service.atci.autostart=1` | 0 |  |
| `vendor/init/atcid.rc:14` | `property:persist.vendor.service.atci.atm_mode=1` | 0 |  |
| `vendor/init/atcid.rc:17` | `property:sys.boot_completed=1` | 0 |  |
| `vendor/init/bootperf.rc:2` | `early-init && property:ro.boot.boot_trace=1` | 3 | setprop:3 |
| `vendor/init/hostapd.android.rc:9` | `post-fs-data` | 3 | mkdir:3 |
| `vendor/init/hw/factory_init.connectivity.rc:1` | `post-fs-data` | 9 | mkdir:4, chmod:3, chown:2 |
| `vendor/init/hw/factory_init.connectivity.rc:28` | `boot` | 0 |  |
| `vendor/init/hw/factory_init.connectivity.rc:69` | `boot` | 1 | insmod:1 |
| `vendor/init/hw/factory_init.connectivity.rc:73` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/factory_init.connectivity.rc:77` | `property:vendor.connsys.driver.ready=yes` | 3 | insmod:2, start:1 |
| `vendor/init/hw/factory_init.connectivity.rc:83` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/factory_init.connectivity.rc:87` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/factory_init.project.rc:14` | `post-fs` | 2 | chmod:1, chown:1 |
| `vendor/init/hw/factory_init.project.rc:19` | `boot` | 0 |  |
| `vendor/init/hw/factory_init.project.rc:26` | `property:sys.usb.config=gs1gs3` | 10 | write:7, setprop:2, start:1 |
| `vendor/init/hw/factory_init.rc:29` | `early-init` | 8 | write:2, restorecon:2, start:1, exec_start:1, mount:1, chmod:1 |
| `vendor/init/hw/factory_init.rc:55` | `init` | 90 | write:22, mkdir:22, symlink:18, chown:10, chmod:7, mount:4, start:3, copy:2, restorecon_recursive:1, setrlimit:1 |
| `vendor/init/hw/factory_init.rc:197` | `property:sys.boot_from_charger_mode=1` | 2 | class_stop:1, trigger:1 |
| `vendor/init/hw/factory_init.rc:202` | `load_system_props_action` | 1 | load_system_props:1 |
| `vendor/init/hw/factory_init.rc:205` | `load_persist_props_action` | 3 | start:2, load_persist_props:1 |
| `vendor/init/hw/factory_init.rc:211` | `firmware_mounts_complete` | 1 | rm:1 |
| `vendor/init/hw/factory_init.rc:215` | `late-init` | 10 | trigger:10 |
| `vendor/init/hw/factory_init.rc:242` | `early-fs` | 1 | start:1 |
| `vendor/init/hw/factory_init.rc:247` | `fs` | 28 | mkdir:12, write:11, symlink:1, mount:1, wait_for_prop:1, start:1, mount_all:1 |
| `vendor/init/hw/factory_init.rc:282` | `post-fs` | 40 | chown:11, chmod:11, start:6, restorecon_recursive:4, mkdir:4, mount:3, symlink:1 |
| `vendor/init/hw/factory_init.rc:352` | `late-fs` | 8 | start:7, class_start:1 |
| `vendor/init/hw/factory_init.rc:365` | `post-fs-data` | 50 | chown:17, chmod:15, mkdir:5, write:2, start:2, rm:2, restorecon_recursive:2, symlink:1, exec:1, restorecon:1, setrlimit:1, setprop:1 |
| `vendor/init/hw/factory_init.rc:476` | `zygote-start && property:ro.crypto.state=unencrypted` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/factory_init.rc:483` | `zygote-start && property:ro.crypto.state=unsupported` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/factory_init.rc:490` | `zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/factory_init.rc:497` | `boot` | 23 | setprop:7, chmod:5, chown:5, class_start:3, ifup:1, hostname:1, domainname:1 |
| `vendor/init/hw/factory_init.rc:539` | `nonencrypted` | 2 | class_start:2 |
| `vendor/init/hw/factory_init.rc:543` | `property:vold.decrypt=trigger_default_encryption` | 1 | start:1 |
| `vendor/init/hw/factory_init.rc:546` | `property:vold.decrypt=trigger_encryption` | 2 | start:2 |
| `vendor/init/hw/factory_init.rc:550` | `property:sys.init_log_level=*` | 1 | loglevel:1 |
| `vendor/init/hw/factory_init.rc:553` | `property:vold.decrypt=trigger_reset_main` | 1 | class_reset:1 |
| `vendor/init/hw/factory_init.rc:556` | `property:vold.decrypt=trigger_load_persist_props` | 3 | start:2, load_persist_props:1 |
| `vendor/init/hw/factory_init.rc:561` | `property:vold.decrypt=trigger_post_fs_data` | 2 | trigger:2 |
| `vendor/init/hw/factory_init.rc:565` | `property:vold.decrypt=trigger_restart_min_framework` | 2 | exec_start:1, class_start:1 |
| `vendor/init/hw/factory_init.rc:570` | `property:vold.decrypt=trigger_restart_framework` | 5 | class_start:2, stop:1, start:1, exec_start:1 |
| `vendor/init/hw/factory_init.rc:578` | `property:vold.decrypt=trigger_shutdown_framework` | 2 | class_reset:2 |
| `vendor/init/hw/factory_init.rc:583` | `defaultcrypto` | 1 | exec:1 |
| `vendor/init/hw/factory_init.rc:592` | `encrypt && property:ro.vendor.mtk_f2fs_enable=0` | 1 | mount:1 |
| `vendor/init/hw/factory_init.rc:595` | `encrypt && property:ro.vendor.mtk_f2fs_enable=1` | 1 | mount:1 |
| `vendor/init/hw/factory_init.rc:598` | `encrypt` | 3 | setprop:2, trigger:1 |
| `vendor/init/hw/factory_init.rc:602` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | 6 | class_start:2, trigger:1, setprop:1, exec_start:1, start:1 |
| `vendor/init/hw/factory_init.rc:624` | `property:sys.powerctl=*` | 1 | powerctl:1 |
| `vendor/init/hw/factory_init.rc:627` | `property:ro.debuggable=1` | 2 | chmod:1, start:1 |
| `vendor/init/hw/factory_init.rc:742` | `property:ro.vendor.mtklog_internal=1` | 1 | setprop:1 |
| `vendor/init/hw/factory_init.rc:745` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | 10 | write:5, rm:4, setprop:1 |
| `vendor/init/hw/factory_init.rc:758` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | 1 | start:1 |
| `vendor/init/hw/factory_init.rc:761` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | 8 | write:5, symlink:3 |
| `vendor/init/hw/factory_init.rc:772` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | 6 | write:5, symlink:1 |
| `vendor/init/hw/factory_init.rc:781` | `property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1` | 2 | start:1, setprop:1 |
| `vendor/init/hw/factory_init.rc:785` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | 10 | write:4, symlink:4, setprop:2 |
| `vendor/init/hw/factory_init.rc:798` | `property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1` | 2 | start:1, setprop:1 |
| `vendor/init/hw/factory_init.rc:802` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | 10 | write:4, symlink:4, setprop:2 |
| `vendor/init/hw/factory_init.rc:815` | `property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1` | 2 | start:1, setprop:1 |
| `vendor/init/hw/factory_init.rc:819` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.aee.rc:1` | `init` | 1 | write:1 |
| `vendor/init/hw/init.ago.rc:3` | `init` | 1 | write:1 |
| `vendor/init/hw/init.ago.rc:6` | `post-fs-data && property:ro.crypto.type=block` | 1 | mkdir:1 |
| `vendor/init/hw/init.ago.rc:9` | `post-fs-data && property:ro.crypto.state=unencrypted` | 1 | mkdir:1 |
| `vendor/init/hw/init.ago.rc:12` | `post-fs-data` | 6 | write:5, swapon_all:1 |
| `vendor/init/hw/init.connectivity.rc:3` | `post-fs-data` | 11 | chmod:4, mkdir:4, chown:3 |
| `vendor/init/hw/init.connectivity.rc:38` | `boot` | 0 |  |
| `vendor/init/hw/init.modem.rc:7` | `early-init` | 6 | write:6 |
| `vendor/init/hw/init.modem.rc:15` | `post-fs-data` | 2 | write:1, setprop:1 |
| `vendor/init/hw/init.mt6762.rc:18` | `early-init` | 5 | write:3, mount:1, chmod:1 |
| `vendor/init/hw/init.mt6762.rc:26` | `init` | 21 | write:8, mkdir:7, chown:3, chmod:2, symlink:1 |
| `vendor/init/hw/init.mt6762.rc:69` | `late-init` | 2 | write:2 |
| `vendor/init/hw/init.mt6762.rc:77` | `charger` | 39 | write:12, chown:9, chmod:7, start:6, mount:2, exec:1, mkdir:1, restorecon_recursive:1 |
| `vendor/init/hw/init.mt6762.rc:122` | `fs` | 7 | write:4, wait_for_prop:1, start:1, mount_all:1 |
| `vendor/init/hw/init.mt6762.rc:131` | `fs` | 1 | mount:1 |
| `vendor/init/hw/init.mt6762.rc:135` | `post-fs` | 24 | chown:10, write:6, symlink:5, chmod:3 |
| `vendor/init/hw/init.mt6762.rc:173` | `post-fs-data` | 270 | chown:151, chmod:92, mkdir:12, write:11, restorecon_recursive:2, setrlimit:2 |
| `vendor/init/hw/init.mt6762.rc:630` | `boot` | 237 | chown:132, chmod:103, write:2 |
| `vendor/init/hw/init.mt6762.rc:905` | `property:ro.board.platform=mt6753` | 3 | chown:1, chmod:1, write:1 |
| `vendor/init/hw/init.mt6762.rc:910` | `property:vold.decrypt=trigger_reset_main` | 3 | setprop:2, write:1 |
| `vendor/init/hw/init.mt6762.rc:915` | `property:vold.decrypt=trigger_post_fs_data` | 1 | write:1 |
| `vendor/init/hw/init.mt6762.rc:918` | `property:vold.decrypt=trigger_restart_min_framework` | 1 | write:1 |
| `vendor/init/hw/init.mt6762.rc:921` | `property:vold.decrypt=trigger_restart_framework` | 1 | write:1 |
| `vendor/init/hw/init.mt6762.rc:924` | `property:vold.decrypt=trigger_shutdown_framework` | 14 | setprop:13, write:1 |
| `vendor/init/hw/init.mt6762.rc:946` | `early-init && property:ro.boot.boot_trace=1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6762.rc:950` | `property:sys.boot_completed=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6762.rc:955` | `property:persist.vendor.uartconsole.enable=*` | 1 | write:1 |
| `vendor/init/hw/init.mt6762.rc:968` | `property:sys.boot_completed=1` | 9 | write:9 |
| `vendor/init/hw/init.mt6762.rc:980` | `property:sys.boot_completed=1` | 23 | write:20, chown:3 |
| `vendor/init/hw/init.mt6762.rc:1058` | `property:persist.vendor.mediatek.fg.disable=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6762.rc:1062` | `property:persist.vendor.mediatek.fg.force25c=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6762.rc:1066` | `property:persist.vendor.mediatek.fg.force25c=0` | 2 | write:2 |
| `vendor/init/hw/init.mt6762.rc:1070` | `property:persist.vendor.mediatek.fg.log.enable=1` | 3 | write:3 |
| `vendor/init/hw/init.mt6762.rc:1093` | `property:sys.boot_completed=1 && property:ro.boot.atm=enable` | 3 | start:3 |
| `vendor/init/hw/init.mt6762.rc:1100` | `init` | 1 | symlink:1 |
| `vendor/init/hw/init.mt6762.rc:1104` | `boot` | 1 | insmod:1 |
| `vendor/init/hw/init.mt6765.rc:18` | `early-init` | 5 | write:3, mount:1, chmod:1 |
| `vendor/init/hw/init.mt6765.rc:26` | `init` | 21 | write:8, mkdir:7, chown:3, chmod:2, symlink:1 |
| `vendor/init/hw/init.mt6765.rc:69` | `late-init` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.rc:77` | `charger` | 39 | write:12, chown:9, chmod:7, start:6, mount:2, exec:1, mkdir:1, restorecon_recursive:1 |
| `vendor/init/hw/init.mt6765.rc:122` | `fs` | 7 | write:4, wait_for_prop:1, start:1, mount_all:1 |
| `vendor/init/hw/init.mt6765.rc:131` | `fs` | 1 | mount:1 |
| `vendor/init/hw/init.mt6765.rc:135` | `post-fs` | 24 | chown:10, write:6, symlink:5, chmod:3 |
| `vendor/init/hw/init.mt6765.rc:173` | `post-fs-data` | 270 | chown:151, chmod:92, mkdir:12, write:11, restorecon_recursive:2, setrlimit:2 |
| `vendor/init/hw/init.mt6765.rc:630` | `boot` | 237 | chown:132, chmod:103, write:2 |
| `vendor/init/hw/init.mt6765.rc:905` | `property:ro.board.platform=mt6753` | 3 | chown:1, chmod:1, write:1 |
| `vendor/init/hw/init.mt6765.rc:910` | `property:vold.decrypt=trigger_reset_main` | 3 | setprop:2, write:1 |
| `vendor/init/hw/init.mt6765.rc:915` | `property:vold.decrypt=trigger_post_fs_data` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.rc:918` | `property:vold.decrypt=trigger_restart_min_framework` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.rc:921` | `property:vold.decrypt=trigger_restart_framework` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.rc:924` | `property:vold.decrypt=trigger_shutdown_framework` | 14 | setprop:13, write:1 |
| `vendor/init/hw/init.mt6765.rc:946` | `early-init && property:ro.boot.boot_trace=1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.rc:950` | `property:sys.boot_completed=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.rc:955` | `property:persist.vendor.uartconsole.enable=*` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.rc:968` | `property:sys.boot_completed=1` | 9 | write:9 |
| `vendor/init/hw/init.mt6765.rc:980` | `property:sys.boot_completed=1` | 23 | write:20, chown:3 |
| `vendor/init/hw/init.mt6765.rc:1058` | `property:persist.vendor.mediatek.fg.disable=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.rc:1062` | `property:persist.vendor.mediatek.fg.force25c=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.rc:1066` | `property:persist.vendor.mediatek.fg.force25c=0` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.rc:1070` | `property:persist.vendor.mediatek.fg.log.enable=1` | 3 | write:3 |
| `vendor/init/hw/init.mt6765.rc:1093` | `property:sys.boot_completed=1 && property:ro.boot.atm=enable` | 3 | start:3 |
| `vendor/init/hw/init.mt6765.rc:1100` | `init` | 1 | symlink:1 |
| `vendor/init/hw/init.mt6765.rc:1104` | `boot` | 1 | insmod:1 |
| `vendor/init/hw/init.mt6765.usb.rc:1` | `early-init` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:4` | `post-fs` | 35 | mkdir:22, write:10, setprop:1, symlink:1, mount:1 |
| `vendor/init/hw/init.mt6765.usb.rc:41` | `boot` | 28 | chmod:10, chown:10, setprop:7, write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:72` | `charger` | 20 | write:7, setprop:7, mkdir:5, mount:1 |
| `vendor/init/hw/init.mt6765.usb.rc:95` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | 4 | write:2, rm:2 |
| `vendor/init/hw/init.mt6765.usb.rc:102` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | 7 | write:5, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:115` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:118` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:122` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:126` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:138` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | 5 | write:3, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:147` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:150` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:154` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:158` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | 9 | write:5, symlink:3, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:170` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | 6 | write:4, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:180` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:183` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:187` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:191` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | 10 | write:5, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:204` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | 7 | write:4, symlink:2, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:215` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:218` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:222` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:226` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | 8 | write:4, symlink:3, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:237` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | 5 | write:3, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:246` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:249` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:253` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:257` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:269` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | 6 | write:3, symlink:2, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:279` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:282` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:286` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:290` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | 9 | write:4, symlink:3, mkdir:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:302` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | 6 | write:3, mkdir:1, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:311` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:314` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:318` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:322` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | 10 | write:4, symlink:4, mkdir:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:335` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | 7 | write:3, symlink:2, mkdir:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:346` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:349` | `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:353` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:357` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:362` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:366` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:371` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:375` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:379` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:382` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:388` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:401` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | 8 | write:4, symlink:3, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:412` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:415` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:419` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:423` | `property:sys.usb.config=via_bypass,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `vendor/init/hw/init.mt6765.usb.rc:427` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | 10 | symlink:5, write:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:442` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:456` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:469` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:472` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:476` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:480` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | 8 | write:4, symlink:3, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:491` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | 5 | write:3, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:499` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:502` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:506` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:510` | `property:sys.usb.config=mass_storage,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `vendor/init/hw/init.mt6765.usb.rc:513` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | 9 | write:4, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:525` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | 6 | write:3, symlink:2, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:535` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:538` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:542` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:546` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | 10 | write:6, symlink:3, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:559` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | 7 | write:5, symlink:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:569` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=0 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:572` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \\` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:576` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=2 && \\` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:580` | `property:sys.usb.config=bicr,adb && property:sys.usb.configfs=1` | 1 | start:1 |
| `vendor/init/hw/init.mt6765.usb.rc:583` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | 11 | write:6, symlink:4, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:597` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | 8 | write:5, symlink:2, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:609` | `property:persist.vendor.radio.port_index=""` | 5 | setprop:5 |
| `vendor/init/hw/init.mt6765.usb.rc:616` | `property:persist.vendor.radio.port_index="1"` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:618` | `property:sys.usb.config=mass_storage,adb,acm` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:620` | `property:sys.usb.config=acm_gs0` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:629` | `property:persist.vendor.radio.port_index="2"` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:631` | `property:sys.usb.config=acm_gs1` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:640` | `property:persist.vendor.radio.port_index="3"` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:642` | `property:sys.usb.config=acm_third` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:644` | `property:sys.usb.config=acm_gs2` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:653` | `property:persist.vendor.radio.port_index="4"` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:655` | `property:sys.usb.config=gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:657` | `property:sys.usb.config=mtp,gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:659` | `property:sys.usb.config=acm_gs3` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:668` | `property:sys.usb.config=mtp,gs0gs1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:670` | `property:sys.usb.config=gs0gs1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:672` | `property:sys.usb.config=rndis,gs0gs1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:674` | `property:sys.usb.config=rndis,adb,gs0gs1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:676` | `property:sys.usb.config=acm_gs0gs1` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:685` | `property:sys.usb.config=gs3,dual_acm` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:687` | `property:sys.usb.config=mtp,gs3,dual_acm` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:689` | `property:sys.usb.config=mtp,gs0gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:691` | `property:sys.usb.config=gs0gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:693` | `property:sys.usb.config=rndis,gs0gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:695` | `property:sys.usb.config=rndis,adb,gs0gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:697` | `property:sys.usb.config=acm_gs0gs3` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:706` | `property:sys.usb.config=gs1gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:708` | `property:sys.usb.config=mtp,gs1gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:710` | `property:sys.usb.config=via_bypass,gs1gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:712` | `property:sys.usb.config=rndis,gs1gs3` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:714` | `property:sys.usb.config=acm_gs1gs3` | 7 | setprop:7 |
| `vendor/init/hw/init.mt6765.usb.rc:724` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | 12 | write:5, rm:5, rmdir:1, setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:739` | `property:sys.usb.ffs.ready=0` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:741` | `property:sys.usb.ffs.ready=1` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:744` | `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | 3 | setprop:2, stop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:749` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0 && \\` | 1 | start:1 |
| `vendor/init/hw/init.mt6765.usb.rc:753` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | 13 | setprop:6, write:4, symlink:3 |
| `vendor/init/hw/init.mt6765.usb.rc:771` | `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | 3 | setprop:2, stop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:776` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0gs3 && \\` | 1 | start:1 |
| `vendor/init/hw/init.mt6765.usb.rc:780` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | 13 | setprop:6, symlink:4, write:3 |
| `vendor/init/hw/init.mt6765.usb.rc:799` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | 7 | write:4, setprop:2, symlink:1 |
| `vendor/init/hw/init.mt6765.usb.rc:808` | `property:sys.usb.config=charging_yes` | 4 | setprop:4 |
| `vendor/init/hw/init.mt6765.usb.rc:814` | `property:sys.usb.config=charging_no` | 4 | setprop:4 |
| `vendor/init/hw/init.mt6765.usb.rc:820` | `property:vendor.usb.charging=yes` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:822` | `property:vendor.usb.charging=no` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:825` | `property:vendor.usb.bicr=yes` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:828` | `property:vendor.usb.bicr=no` | 2 | write:2 |
| `vendor/init/hw/init.mt6765.usb.rc:832` | `property:vendor.usb.port.mode=usb` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:834` | `property:vendor.usb.port.mode=uart` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:837` | `property:vendor.em.usb.set=USB_DRIVING_CAPABILITY` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:839` | `property:vendor.em.usb.set=RG_USB20_TERM_VREF_SEL` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:841` | `property:vendor.em.usb.set=RG_USB20_HSTX_SRCTRL` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:843` | `property:vendor.em.usb.set=RG_USB20_VRT_VREF_SEL` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:845` | `property:vendor.em.usb.set=RG_USB20_INTR_EN` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:847` | `property:vendor.em.usb.set=RG_USB20_PHY_REV6` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:850` | `property:vendor.em.usb.bypass.enable=0` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:852` | `property:vendor.em.usb.bypass.enable=1` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:854` | `property:vendor.em.usb.bypass.enable=2` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:856` | `property:vendor.em.usb.bypass.enable=3` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:858` | `property:vendor.em.usb.bypass.enable=4` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:860` | `property:vendor.em.usb.bypass.disable=0` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:862` | `property:vendor.em.usb.bypass.disable=1` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:864` | `property:vendor.em.usb.bypass.disable=2` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:866` | `property:vendor.em.usb.bypass.disable=3` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:868` | `property:vendor.em.usb.bypass.disable=4` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:872` | `property:vendor.usb.config=*` | 1 | setprop:1 |
| `vendor/init/hw/init.mt6765.usb.rc:877` | `property:vendor.usb.test=*` | 2 | setprop:2 |
| `vendor/init/hw/init.mt6765.usb.rc:882` | `property:vendor.usb.printk=*` | 1 | write:1 |
| `vendor/init/hw/init.mt6765.usb.rc:885` | `property:persist.vendor.usb.printk=*` | 1 | setprop:1 |
| `vendor/init/hw/init.project.rc:5` | `init` | 2 | mkdir:2 |
| `vendor/init/hw/init.project.rc:9` | `post-fs-data` | 18 | chmod:9, chown:9 |
| `vendor/init/hw/init.project.rc:41` | `init` | 0 |  |
| `vendor/init/hw/init.project.rc:62` | `property:sys.boot_completed=1` | 1 | start:1 |
| `vendor/init/hw/init.project.rc:72` | `charger` | 1 | load_system_props:1 |
| `vendor/init/hw/init.sensor_1_0.rc:2` | `post-fs-data` | 295 | chmod:148, chown:146, mkdir:1 |
| `vendor/init/hw/meta_init.connectivity.rc:2` | `post-fs-data` | 4 | chmod:2, chown:1, mkdir:1 |
| `vendor/init/hw/meta_init.connectivity.rc:22` | `boot` | 0 |  |
| `vendor/init/hw/meta_init.connectivity.rc:63` | `boot` | 1 | insmod:1 |
| `vendor/init/hw/meta_init.connectivity.rc:67` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/meta_init.connectivity.rc:71` | `property:vendor.connsys.driver.ready=yes` | 3 | insmod:2, start:1 |
| `vendor/init/hw/meta_init.connectivity.rc:77` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/meta_init.connectivity.rc:81` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/hw/meta_init.modem.rc:7` | `post-fs-data` | 10 | mkdir:4, restorecon_recursive:2, write:1, chown:1, restorecon:1, setprop:1 |
| `vendor/init/hw/meta_init.project.rc:2` | `post-fs` | 2 | chmod:1, chown:1 |
| `vendor/init/hw/meta_init.project.rc:8` | `boot` | 0 |  |
| `vendor/init/hw/meta_init.rc:29` | `early-init` | 9 | write:3, restorecon:2, start:1, exec_start:1, mount:1, chmod:1 |
| `vendor/init/hw/meta_init.rc:56` | `init` | 105 | mkdir:29, write:23, symlink:19, chown:14, chmod:8, mount:4, start:3, copy:2, sysclktz:1, restorecon_recursive:1, setrlimit:1 |
| `vendor/init/hw/meta_init.rc:216` | `property:sys.boot_from_charger_mode=1` | 2 | class_stop:1, trigger:1 |
| `vendor/init/hw/meta_init.rc:221` | `load_system_props_action` | 1 | load_system_props:1 |
| `vendor/init/hw/meta_init.rc:224` | `load_persist_props_action` | 5 | start:3, load_persist_props:1, stop:1 |
| `vendor/init/hw/meta_init.rc:234` | `firmware_mounts_complete` | 1 | rm:1 |
| `vendor/init/hw/meta_init.rc:238` | `late-init` | 11 | trigger:10, write:1 |
| `vendor/init/hw/meta_init.rc:275` | `early-fs` | 1 | start:1 |
| `vendor/init/hw/meta_init.rc:279` | `fs` | 30 | mkdir:13, write:12, symlink:1, setprop:1, wait_for_prop:1, start:1, mount_all:1 |
| `vendor/init/hw/meta_init.rc:315` | `post-fs` | 35 | chmod:10, chown:8, start:5, mount:4, restorecon_recursive:3, mkdir:2, write:1, restorecon:1, symlink:1 |
| `vendor/init/hw/meta_init.rc:376` | `late-fs` | 8 | start:7, class_start:1 |
| `vendor/init/hw/meta_init.rc:389` | `post-fs-data` | 13 | mkdir:4, chmod:3, start:2, chown:2, write:1, exec:1 |
| `vendor/init/hw/meta_init.rc:420` | `zygote-start && property:ro.crypto.state=unencrypted` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/meta_init.rc:427` | `zygote-start && property:ro.crypto.state=unsupported` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/meta_init.rc:434` | `zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file` | 4 | start:3, exec_start:1 |
| `vendor/init/hw/meta_init.rc:441` | `boot` | 14 | class_start:3, chmod:2, chown:2, setprop:2, write:1, ifup:1, hostname:1, domainname:1, mount:1 |
| `vendor/init/hw/meta_init.rc:469` | `nonencrypted` | 2 | class_start:2 |
| `vendor/init/hw/meta_init.rc:473` | `property:sys.init_log_level=*` | 1 | loglevel:1 |
| `vendor/init/hw/meta_init.rc:476` | `property:vold.decrypt=trigger_reset_main` | 1 | class_reset:1 |
| `vendor/init/hw/meta_init.rc:479` | `property:vold.decrypt=trigger_load_persist_props` | 5 | start:3, load_persist_props:1, stop:1 |
| `vendor/init/hw/meta_init.rc:488` | `property:vold.decrypt=trigger_post_fs_data` | 2 | trigger:2 |
| `vendor/init/hw/meta_init.rc:492` | `property:vold.decrypt=trigger_restart_min_framework` | 2 | exec_start:1, class_start:1 |
| `vendor/init/hw/meta_init.rc:497` | `property:vold.decrypt=trigger_restart_framework` | 5 | class_start:2, stop:1, start:1, exec_start:1 |
| `vendor/init/hw/meta_init.rc:505` | `property:vold.decrypt=trigger_shutdown_framework` | 2 | class_reset:2 |
| `vendor/init/hw/meta_init.rc:510` | `defaultcrypto` | 1 | exec:1 |
| `vendor/init/hw/meta_init.rc:519` | `encrypt && property:ro.vendor.mtk_f2fs_enable=0` | 1 | mount:1 |
| `vendor/init/hw/meta_init.rc:522` | `encrypt && property:ro.vendor.mtk_f2fs_enable=1` | 1 | mount:1 |
| `vendor/init/hw/meta_init.rc:525` | `encrypt` | 3 | setprop:2, trigger:1 |
| `vendor/init/hw/meta_init.rc:529` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | 6 | class_start:2, trigger:1, setprop:1, exec_start:1, start:1 |
| `vendor/init/hw/meta_init.rc:554` | `property:ro.vendor.mtklog_internal=1` | 1 | setprop:1 |
| `vendor/init/hw/meta_init.rc:557` | `property:ro.debuggable=1` | 2 | chmod:1, start:1 |
| `vendor/init/hw/meta_init.rc:602` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | 2 | write:1, start:1 |
| `vendor/init/hw/meta_init.rc:606` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | 8 | write:6, symlink:2 |
| `vendor/init/hw/meta_init.rc:618` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 && property:vendor.usb.feature=0` | 1 | write:1 |
| `vendor/init/hw/meta_init.rc:629` | `property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1` | 2 | write:1, setprop:1 |
| `vendor/init/hw/meta_init.rc:642` | `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | 2 | write:1, start:1 |
| `vendor/init/hw/meta_init.rc:646` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | 12 | write:7, symlink:4, setprop:1 |
| `vendor/init/init.bip.rc:4` | `property:vendor.volte_md_status=ready` | 1 | start:1 |
| `vendor/init/init.bip.rc:7` | `property:vendor.volte_md_status=reset` | 1 | stop:1 |
| `vendor/init/init.bip.rc:10` | `property:vendor.volte_md_status=stop` | 1 | stop:1 |
| `vendor/init/init.bip.rc:13` | `property:vendor.volte_md_status=flightmode` | 1 | stop:1 |
| `vendor/init/init.bip.rc:16` | `property:vendor.volte_md_status=exception` | 1 | stop:1 |
| `vendor/init/init.bt_drv.rc:2` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/init.cccifsd.rc:3` | `early-init` | 0 |  |
| `vendor/init/init.cccifsd.rc:5` | `post-fs-data` | 8 | mkdir:3, restorecon:3, restorecon_recursive:2 |
| `vendor/init/init.cccimdinit.rc:2` | `early-init` | 0 |  |
| `vendor/init/init.cccimdinit.rc:4` | `post-fs-data` | 6 | mkdir:4, chown:1, restorecon:1 |
| `vendor/init/init.gps_drv.rc:3` | `property:vendor.connsys.driver.ready=yes` | 1 | insmod:1 |
| `vendor/init/init.md_apps.rc:1` | `boot` | 5 | setprop:5 |
| `vendor/init/init.volte_imsm_93.rc:5` | `property:vendor.volte_md_status=ready` | 4 | restart:4 |
| `vendor/init/init.volte_imsm_93.rc:11` | `property:vendor.volte_md_status=reset` | 8 | rm:4, stop:4 |
| `vendor/init/init.volte_imsm_93.rc:21` | `property:vendor.volte_md_status=stop` | 8 | rm:4, stop:4 |
| `vendor/init/init.volte_imsm_93.rc:31` | `property:vendor.volte_md_status=flightmode` | 8 | rm:4, stop:4 |
| `vendor/init/init.volte_imsm_93.rc:41` | `property:vendor.volte_md_status=exception` | 8 | rm:4, stop:4 |
| `vendor/init/init.volte_md_status.rc:5` | `init` | 1 | restart:1 |
| `vendor/init/init.wlan_drv.rc:3` | `property:vendor.connsys.driver.ready=yes` | 3 | insmod:2, start:1 |
| `vendor/init/init.wmt_drv.rc:1` | `boot` | 1 | insmod:1 |
| `vendor/init/init.wod.rc:1` | `post-fs-data` | 3 | mkdir:3 |
| `vendor/init/init.wod.rc:6` | `property:vendor.volte_md_status=ready && property:persist.vendor.mtk_wfc_support=1` | 1 | start:1 |
| `vendor/init/init.wod.rc:9` | `property:vendor.volte_md_status=reset` | 1 | stop:1 |
| `vendor/init/init.wod.rc:12` | `property:vendor.volte_md_status=stop` | 1 | stop:1 |
| `vendor/init/init.wod.rc:15` | `property:vendor.volte_md_status=flightmode` | 1 | stop:1 |
| `vendor/init/init.wod.rc:18` | `property:vendor.volte_md_status=exception` | 1 | stop:1 |
| `vendor/init/init.wod.rc:22` | `property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting` | 2 | setprop:2 |
| `vendor/init/init_connectivity.rc:3` | `post-fs-data` | 2 | mkdir:2 |
| `vendor/init/init_connectivity.rc:7` | `boot` | 0 |  |
| `vendor/init/init_connectivity.rc:21` | `property:ro.build.type=eng` | 1 | write:1 |
| `vendor/init/init_connectivity.rc:24` | `property:persist.vendor.connsys.coredump.mode=1` | 1 | start:1 |
| `vendor/init/init_connectivity.rc:27` | `property:persist.vendor.connsys.coredump.mode=2` | 1 | start:1 |
| `vendor/init/init_connectivity.rc:30` | `property:persist.vendor.connsys.coredump.mode=0` | 1 | stop:1 |
| `vendor/init/init_connectivity.rc:33` | `property:vendor.connsys.driver.ready=yes && property:persist.vendor.connsys.coredump.mode=*` | 1 | write:1 |
| `vendor/init/init_connectivity.rc:36` | `property:vendor.connsys.driver.ready=yes && property:debug.vendor.connsys.suspend.alarm=*` | 1 | write:1 |
| `vendor/init/mtk_agpsd_p.rc:1` | `post-fs-data` | 1 | mkdir:1 |
| `vendor/init/mtkrild.rc:1` | `post-fs-data` | 1 | mkdir:1 |
| `vendor/init/mtkrild.rc:17` | `property:vendor.ril.mtk=1` | 1 | start:1 |
| `vendor/init/mtkrild.rc:20` | `property:vold.decrypt=trigger_restart_framework` | 1 | start:1 |
| `vendor/init/muxreport.rc:8` | `property:vendor.ril.muxreport=1` | 1 | start:1 |
| `vendor/init/netdagent.rc:8` | `init` | 1 | setprop:1 |
| `vendor/init/netdagent.rc:11` | `property:persist.vendor.operator.optr=OP12` | 1 | write:1 |
| `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:1` | `post-fs-data` | 195 | chown:97, chmod:97, mkdir:1 |
| `vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc:7` | `property:vold.decrypt=trigger_shutdown_framework` | 1 | setprop:1 |

################################################################################
# 1.12 — SECURITY-CRITICAL PRIVILEGE MUTATION SURFACE
################################################################################

The complete command ledger is carried in Appendix A. This section extracts the commands that
directly mutate permissions, ownership, kernel/module state, mount state, property state, boot
class state, service state, or hardware/sysfs/device-node accessibility.

| Verb | Count |
|---|---:|
| `chmod` | 1025 |
| `chown` | 1128 |
| `class_reset` | 8 |
| `class_start` | 31 |
| `class_stop` | 3 |
| `exec` | 18 |
| `exec_background` | 5 |
| `exec_start` | 20 |
| `insmod` | 19 |
| `mkdir` | 554 |
| `mount` | 39 |
| `restart` | 6 |
| `setprop` | 329 |
| `start` | 179 |
| `stop` | 43 |
| `symlink` | 200 |
| `trigger` | 43 |
| `write` | 676 |


| File:Line | Trigger | Command |
|---|---|---|
| `root/init.rc:288` | `init` | `chown system system /sys/power/wakeup_count` |
| `root/init.rc:291` | `init` | `chown radio wakelock /sys/power/wake_lock` |
| `root/init.rc:292` | `init` | `chown radio wakelock /sys/power/wake_unlock` |
| `root/init.rc:293` | `init` | `chmod 0660 /sys/power/wake_lock` |
| `root/init.rc:294` | `init` | `chmod 0660 /sys/power/wake_unlock` |
| `root/init.usb.configfs.rc:3` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `stop adbd` |
| `root/init.usb.configfs.rc:15` | `property:init.svc.adbd=stopped` | `setprop sys.usb.ffs.ready 0` |
| `root/init.usb.configfs.rc:18` | `property:sys.usb.config=adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:33` | `property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:49` | `property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:65` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:81` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:98` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:115` | `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:132` | `property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.rc:7` | `post-fs-data` | `chown system system /sys/class/android_usb/android0/f_mass_storage/lun/file` |
| `root/init.usb.rc:8` | `post-fs-data` | `chmod 0660 /sys/class/android_usb/android0/f_mass_storage/lun/file` |
| `root/init.usb.rc:26` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | `stop adbd` |
| `root/init.usb.rc:40` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:59` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:78` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:97` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `start adbd` |
| `system/init/hw/vendor_init_as_system.rc:199` | `post-fs-data` | `setprop vold.post_fs_data_done 1` |
| `vendor/init/hw/factory_init.connectivity.rc:7` | `post-fs-data` | `chmod 0660 /dev/ttyMT2` |
| `vendor/init/hw/factory_init.connectivity.rc:8` | `post-fs-data` | `chown system system /dev/ttyMT2` |
| `vendor/init/hw/factory_init.connectivity.rc:16` | `post-fs-data` | `chmod 666 /dev/gps_emi` |
| `vendor/init/hw/factory_init.project.rc:32` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/functions mass_storage,adb,acm` |
| `vendor/init/hw/factory_init.project.rc:34` | `property:sys.usb.config=gs1gs3` | `start adbd` |
| `vendor/init/hw/factory_init.rc:126` | `init` | `chown system system /sys/power/wakeup_count` |
| `vendor/init/hw/factory_init.rc:127` | `init` | `chown radio wakelock /sys/power/wake_lock` |
| `vendor/init/hw/factory_init.rc:128` | `init` | `chown radio wakelock /sys/power/wake_unlock` |
| `vendor/init/hw/factory_init.rc:130` | `init` | `chmod 0660 /sys/power/wake_lock` |
| `vendor/init/hw/factory_init.rc:131` | `init` | `chmod 0660 /sys/power/wake_unlock` |
| `vendor/init/hw/factory_init.rc:132` | `init` | `chmod 0660 /sys/power/wakeup_count` |
| `vendor/init/hw/factory_init.rc:261` | `fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/factory_init.rc:263` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/factory_init.rc:264` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/factory_init.rc:265` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/factory_init.rc:266` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/factory_init.rc:382` | `post-fs-data` | `chmod 0660 /dev/ttyMT4` |
| `vendor/init/hw/factory_init.rc:383` | `post-fs-data` | `chown radio radio /dev/ttyMT4` |
| `vendor/init/hw/factory_init.rc:460` | `post-fs-data` | `chmod 0666 /dev/exm0` |
| `vendor/init/hw/factory_init.rc:472` | `post-fs-data` | `setprop selinux.reload_policy 1` |
| `vendor/init/hw/factory_init.rc:503` | `boot` | `chmod 0660 /dev/ttyGS0` |
| `vendor/init/hw/factory_init.rc:504` | `boot` | `chown system radio /dev/ttyGS0` |
| `vendor/init/hw/factory_init.rc:505` | `boot` | `chmod 0660 /dev/ttyGS1` |
| `vendor/init/hw/factory_init.rc:506` | `boot` | `chown system radio /dev/ttyGS1` |
| `vendor/init/hw/factory_init.rc:508` | `boot` | `chown bluetooth radio /dev/ttyGS2` |
| `vendor/init/hw/factory_init.rc:509` | `boot` | `chmod 0660 /dev/ttyGS2` |
| `vendor/init/hw/factory_init.rc:511` | `boot` | `chown system radio /dev/ttyGS3` |
| `vendor/init/hw/factory_init.rc:512` | `boot` | `chmod 0660 /dev/ttyGS3` |
| `vendor/init/hw/factory_init.rc:600` | `encrypt` | `setprop vold.post_fs_data_done 0` |
| `vendor/init/hw/factory_init.rc:603` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `trigger load_persist_props_action` |
| `vendor/init/hw/factory_init.rc:605` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `setprop vendor.soter.teei.crypto.state unencrypted` |
| `vendor/init/hw/factory_init.rc:606` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `exec_start update_verifier` |
| `vendor/init/hw/factory_init.rc:607` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `class_start main` |
| `vendor/init/hw/factory_init.rc:608` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `class_start late_start` |
| `vendor/init/hw/factory_init.rc:609` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `start mount_all_storages` |
| `vendor/init/hw/factory_init.rc:759` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:766` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:768` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:777` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:782` | `property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:790` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:792` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:793` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:799` | `property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:808` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:809` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:810` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:816` | `property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:824` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:826` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:827` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.connectivity.rc:11` | `post-fs-data` | `chmod 0660 /dev/ttyMT2` |
| `vendor/init/hw/init.connectivity.rc:12` | `post-fs-data` | `chown system system /dev/ttyMT2` |
| `vendor/init/hw/init.connectivity.rc:15` | `post-fs-data` | `chmod 0660 /dev/ttyMT1` |
| `vendor/init/hw/init.connectivity.rc:16` | `post-fs-data` | `chown system system /dev/ttyMT1` |
| `vendor/init/hw/init.connectivity.rc:22` | `post-fs-data` | `chmod 666 /dev/gps_emi` |
| `vendor/init/hw/init.connectivity.rc:30` | `post-fs-data` | `chown bluetooth bluetooth /dev/ttyGS2` |
| `vendor/init/hw/init.connectivity.rc:31` | `post-fs-data` | `chmod 0660 /dev/ttyGS2` |
| `vendor/init/hw/init.modem.rc:20` | `post-fs-data` | `setprop vold.post_fs_data_done 1` |
| `vendor/init/hw/init.mt6762.rc:90` | `charger` | `chmod 0666 /dev/kmsg` |
| `vendor/init/hw/init.mt6762.rc:102` | `charger` | `chown system system /sys/power/wakeup_count` |
| `vendor/init/hw/init.mt6762.rc:103` | `charger` | `chown radio wakelock /sys/power/wake_lock` |
| `vendor/init/hw/init.mt6762.rc:104` | `charger` | `chown radio wakelock /sys/power/wake_unlock` |
| `vendor/init/hw/init.mt6762.rc:106` | `charger` | `chmod 0660 /sys/power/wake_lock` |
| `vendor/init/hw/init.mt6762.rc:107` | `charger` | `chmod 0660 /sys/power/wake_unlock` |
| `vendor/init/hw/init.mt6762.rc:108` | `charger` | `chmod 0660 /sys/power/wakeup_count` |
| `vendor/init/hw/init.mt6762.rc:133` | `fs` | `mount iso9660 loop@/vendor/etc/iAmCdRom.iso /mnt/cd-rom ro` |
| `vendor/init/hw/init.mt6762.rc:250` | `post-fs-data` | `chmod 0666 /dev/gps_emi` |
| `vendor/init/hw/init.mt6762.rc:273` | `post-fs-data` | `chmod 666 /dev/mali0` |
| `vendor/init/hw/init.mt6762.rc:341` | `post-fs-data` | `chmod 0666 /dev/goodix_fp` |
| `vendor/init/hw/init.mt6762.rc:432` | `post-fs-data` | `chown root diag /vendor/bin/baseband_regdump` |
| `vendor/init/hw/init.mt6762.rc:433` | `post-fs-data` | `chmod 4750 /vendor/bin/baseband_regdump` |
| `vendor/init/hw/init.mt6762.rc:535` | `post-fs-data` | `chmod 0666 /dev/ccu` |
| `vendor/init/hw/init.mt6762.rc:536` | `post-fs-data` | `chown system camera /dev/ccu` |
| `vendor/init/hw/init.mt6762.rc:603` | `post-fs-data` | `chmod 0666 /dev/uio0` |
| `vendor/init/hw/init.mt6762.rc:618` | `post-fs-data` | `chmod 0666 /dev/exm0` |
| `vendor/init/hw/init.mt6762.rc:872` | `boot` | `chown system radio /dev/ttyGS3` |
| `vendor/init/hw/init.mt6762.rc:873` | `boot` | `chmod 0660 /dev/ttyGS3` |
| `vendor/init/hw/init.mt6762.rc:876` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6762.rc:877` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6762.rc:878` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6762.rc:879` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6762.rc:880` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6762.rc:881` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6762.rc:882` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6762.rc:883` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6762.rc:884` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6762.rc:885` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6762.rc:886` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6762.rc:887` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6762.rc:888` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6762.rc:889` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6762.rc:890` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6762.rc:891` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6762.rc:892` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6762.rc:893` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6762.rc:896` | `boot` | `chmod 666 /dev/ttyS1` |
| `vendor/init/hw/init.mt6762.rc:897` | `boot` | `chown system system /dev/ttyS1` |
| `vendor/init/hw/init.mt6762.rc:900` | `boot` | `chmod 660 /dev/ttyGS0` |
| `vendor/init/hw/init.mt6762.rc:901` | `boot` | `chown system radio /dev/ttyGS0` |
| `vendor/init/hw/init.mt6762.rc:902` | `boot` | `chmod 660 /dev/ttyGS1` |
| `vendor/init/hw/init.mt6762.rc:903` | `boot` | `chown system radio /dev/ttyGS1` |
| `vendor/init/hw/init.mt6765.rc:90` | `charger` | `chmod 0666 /dev/kmsg` |
| `vendor/init/hw/init.mt6765.rc:102` | `charger` | `chown system system /sys/power/wakeup_count` |
| `vendor/init/hw/init.mt6765.rc:103` | `charger` | `chown radio wakelock /sys/power/wake_lock` |
| `vendor/init/hw/init.mt6765.rc:104` | `charger` | `chown radio wakelock /sys/power/wake_unlock` |
| `vendor/init/hw/init.mt6765.rc:106` | `charger` | `chmod 0660 /sys/power/wake_lock` |
| `vendor/init/hw/init.mt6765.rc:107` | `charger` | `chmod 0660 /sys/power/wake_unlock` |
| `vendor/init/hw/init.mt6765.rc:108` | `charger` | `chmod 0660 /sys/power/wakeup_count` |
| `vendor/init/hw/init.mt6765.rc:133` | `fs` | `mount iso9660 loop@/vendor/etc/iAmCdRom.iso /mnt/cd-rom ro` |
| `vendor/init/hw/init.mt6765.rc:250` | `post-fs-data` | `chmod 0666 /dev/gps_emi` |
| `vendor/init/hw/init.mt6765.rc:273` | `post-fs-data` | `chmod 666 /dev/mali0` |
| `vendor/init/hw/init.mt6765.rc:341` | `post-fs-data` | `chmod 0666 /dev/goodix_fp` |
| `vendor/init/hw/init.mt6765.rc:432` | `post-fs-data` | `chown root diag /vendor/bin/baseband_regdump` |
| `vendor/init/hw/init.mt6765.rc:433` | `post-fs-data` | `chmod 4750 /vendor/bin/baseband_regdump` |
| `vendor/init/hw/init.mt6765.rc:535` | `post-fs-data` | `chmod 0666 /dev/ccu` |
| `vendor/init/hw/init.mt6765.rc:536` | `post-fs-data` | `chown system camera /dev/ccu` |
| `vendor/init/hw/init.mt6765.rc:603` | `post-fs-data` | `chmod 0666 /dev/uio0` |
| `vendor/init/hw/init.mt6765.rc:618` | `post-fs-data` | `chmod 0666 /dev/exm0` |
| `vendor/init/hw/init.mt6765.rc:872` | `boot` | `chown system radio /dev/ttyGS3` |
| `vendor/init/hw/init.mt6765.rc:873` | `boot` | `chmod 0660 /dev/ttyGS3` |
| `vendor/init/hw/init.mt6765.rc:876` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6765.rc:877` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6765.rc:878` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6765.rc:879` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6765.rc:880` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6765.rc:881` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6765.rc:882` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6765.rc:883` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6765.rc:884` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6765.rc:885` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6765.rc:886` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6765.rc:887` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6765.rc:888` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6765.rc:889` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6765.rc:890` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6765.rc:891` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6765.rc:892` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6765.rc:893` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6765.rc:896` | `boot` | `chmod 666 /dev/ttyS1` |
| `vendor/init/hw/init.mt6765.rc:897` | `boot` | `chown system system /dev/ttyS1` |
| `vendor/init/hw/init.mt6765.rc:900` | `boot` | `chmod 660 /dev/ttyGS0` |
| `vendor/init/hw/init.mt6765.rc:901` | `boot` | `chown system radio /dev/ttyGS0` |
| `vendor/init/hw/init.mt6765.rc:902` | `boot` | `chmod 660 /dev/ttyGS1` |
| `vendor/init/hw/init.mt6765.rc:903` | `boot` | `chown system radio /dev/ttyGS1` |
| `vendor/init/hw/init.mt6765.usb.rc:24` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:25` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:26` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/init.mt6765.usb.rc:27` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:28` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/init.mt6765.usb.rc:63` | `boot` | `chown system radio /dev/ttyGS0` |
| `vendor/init/hw/init.mt6765.usb.rc:64` | `boot` | `chmod 0660 /dev/ttyGS0` |
| `vendor/init/hw/init.mt6765.usb.rc:65` | `boot` | `chown system radio /dev/ttyGS1` |
| `vendor/init/hw/init.mt6765.usb.rc:66` | `boot` | `chmod 0660 /dev/ttyGS1` |
| `vendor/init/hw/init.mt6765.usb.rc:67` | `boot` | `chown system radio /dev/ttyGS2` |
| `vendor/init/hw/init.mt6765.usb.rc:68` | `boot` | `chmod 0660 /dev/ttyGS2` |
| `vendor/init/hw/init.mt6765.usb.rc:69` | `boot` | `chown system radio /dev/ttyGS3` |
| `vendor/init/hw/init.mt6765.usb.rc:70` | `boot` | `chmod 0660 /dev/ttyGS3` |
| `vendor/init/hw/init.mt6765.usb.rc:131` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:133` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:134` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:165` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:166` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:199` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:200` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:232` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:233` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:264` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:265` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:297` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:298` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:330` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:331` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:396` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:424` | `property:sys.usb.config=via_bypass,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:437` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f5` |
| `vendor/init/hw/init.mt6765.usb.rc:450` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:451` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:471` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2002` |
| `vendor/init/hw/init.mt6765.usb.rc:474` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x200F` |
| `vendor/init/hw/init.mt6765.usb.rc:475` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:478` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202D` |
| `vendor/init/hw/init.mt6765.usb.rc:482` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:483` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:484` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:485` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:486` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:487` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:488` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:489` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:493` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums"` |
| `vendor/init/hw/init.mt6765.usb.rc:494` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:495` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:496` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:497` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:501` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2003` |
| `vendor/init/hw/init.mt6765.usb.rc:504` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2006` |
| `vendor/init/hw/init.mt6765.usb.rc:505` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:508` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2029` |
| `vendor/init/hw/init.mt6765.usb.rc:511` | `property:sys.usb.config=mass_storage,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:515` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:516` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:517` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:518` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:519` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:520` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:521` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:522` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:523` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:527` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:528` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:529` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:530` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:531` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:532` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:551` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:552` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:553` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:554` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:555` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:563` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:564` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:565` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:581` | `property:sys.usb.config=bicr,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:588` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:589` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:590` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:592` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:593` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:601` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:602` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:603` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:619` | `property:sys.usb.config=mass_storage,adb,acm` | `setprop sys.usb.config acm_gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:745` | `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | `stop adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:751` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0 && \\` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:764` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:772` | `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | `stop adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:778` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0gs3 && \\` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:790` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:791` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:826` | `property:vendor.usb.bicr=yes` | `write /sys/class/android_usb/android0/f_mass_storage/bicr 1` |
| `vendor/init/hw/init.mt6765.usb.rc:827` | `property:vendor.usb.bicr=yes` | `write /sys/class/android_usb/android0/f_mass_storage/lun/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:829` | `property:vendor.usb.bicr=no` | `write /sys/class/android_usb/android0/f_mass_storage/bicr 0` |
| `vendor/init/hw/init.mt6765.usb.rc:830` | `property:vendor.usb.bicr=no` | `write /sys/class/android_usb/android0/f_mass_storage/lun/file "off"` |
| `vendor/init/hw/meta_init.connectivity.rc:8` | `post-fs-data` | `chmod 0660 /dev/ttyMT2` |
| `vendor/init/hw/meta_init.connectivity.rc:9` | `post-fs-data` | `chown system system /dev/ttyMT2` |
| `vendor/init/hw/meta_init.connectivity.rc:15` | `post-fs-data` | `chmod 666 /dev/gps_emi` |
| `vendor/init/hw/meta_init.modem.rc:29` | `post-fs-data` | `setprop vold.post_fs_data_done 1` |
| `vendor/init/hw/meta_init.rc:134` | `init` | `chown system system /sys/power/wakeup_count` |
| `vendor/init/hw/meta_init.rc:135` | `init` | `chown radio wakelock /sys/power/wake_lock` |
| `vendor/init/hw/meta_init.rc:136` | `init` | `chown radio wakelock /sys/power/wake_unlock` |
| `vendor/init/hw/meta_init.rc:138` | `init` | `chmod 0660 /sys/power/wake_lock` |
| `vendor/init/hw/meta_init.rc:139` | `init` | `chmod 0660 /sys/power/wake_unlock` |
| `vendor/init/hw/meta_init.rc:293` | `fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/meta_init.rc:295` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/meta_init.rc:296` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/meta_init.rc:297` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/meta_init.rc:298` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/meta_init.rc:415` | `post-fs-data` | `chmod 0666 /dev/exm0` |
| `vendor/init/hw/meta_init.rc:464` | `boot` | `chown system radio /dev/ttyGS3` |
| `vendor/init/hw/meta_init.rc:465` | `boot` | `chmod 0660 /dev/ttyGS3` |
| `vendor/init/hw/meta_init.rc:527` | `encrypt` | `setprop vold.post_fs_data_done 0` |
| `vendor/init/hw/meta_init.rc:530` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `trigger load_persist_props_action` |
| `vendor/init/hw/meta_init.rc:532` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `setprop vendor.soter.teei.crypto.state unencrypted` |
| `vendor/init/hw/meta_init.rc:533` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `exec_start update_verifier` |
| `vendor/init/hw/meta_init.rc:534` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `class_start main` |
| `vendor/init/hw/meta_init.rc:535` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `class_start late_start` |
| `vendor/init/hw/meta_init.rc:536` | `property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1` | `start mount_all_storages` |
| `vendor/init/hw/meta_init.rc:603` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:start adbd"` |
| `vendor/init/hw/meta_init.rc:604` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/meta_init.rc:614` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/meta_init.rc:643` | `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:start adbd"` |
| `vendor/init/hw/meta_init.rc:644` | `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/meta_init.rc:653` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/meta_init.rc:655` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/meta_init.rc:656` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/init.bip.rc:5` | `property:vendor.volte_md_status=ready` | `start vendor.bip` |
| `vendor/init/init.bip.rc:8` | `property:vendor.volte_md_status=reset` | `stop vendor.bip` |
| `vendor/init/init.bip.rc:11` | `property:vendor.volte_md_status=stop` | `stop vendor.bip` |
| `vendor/init/init.bip.rc:14` | `property:vendor.volte_md_status=flightmode` | `stop vendor.bip` |
| `vendor/init/init.bip.rc:17` | `property:vendor.volte_md_status=exception` | `stop vendor.bip` |
| `vendor/init/init.volte_imsm_93.rc:6` | `property:vendor.volte_md_status=ready` | `restart volte_imsm_93` |
| `vendor/init/init.volte_imsm_93.rc:7` | `property:vendor.volte_md_status=ready` | `restart vendor.volte_stack` |
| `vendor/init/init.volte_imsm_93.rc:8` | `property:vendor.volte_md_status=ready` | `restart vendor.volte_ua` |
| `vendor/init/init.volte_imsm_93.rc:9` | `property:vendor.volte_md_status=ready` | `restart vendor.volte_imcb` |
| `vendor/init/init.volte_imsm_93.rc:16` | `property:vendor.volte_md_status=reset` | `stop vendor.volte_imcb` |
| `vendor/init/init.volte_imsm_93.rc:17` | `property:vendor.volte_md_status=reset` | `stop vendor.volte_ua` |
| `vendor/init/init.volte_imsm_93.rc:18` | `property:vendor.volte_md_status=reset` | `stop vendor.volte_stack` |
| `vendor/init/init.volte_imsm_93.rc:19` | `property:vendor.volte_md_status=reset` | `stop volte_imsm_93` |
| `vendor/init/init.volte_imsm_93.rc:26` | `property:vendor.volte_md_status=stop` | `stop vendor.volte_imcb` |
| `vendor/init/init.volte_imsm_93.rc:27` | `property:vendor.volte_md_status=stop` | `stop vendor.volte_ua` |
| `vendor/init/init.volte_imsm_93.rc:28` | `property:vendor.volte_md_status=stop` | `stop vendor.volte_stack` |
| `vendor/init/init.volte_imsm_93.rc:29` | `property:vendor.volte_md_status=stop` | `stop volte_imsm_93` |
| `vendor/init/init.volte_imsm_93.rc:36` | `property:vendor.volte_md_status=flightmode` | `stop vendor.volte_imcb` |
| `vendor/init/init.volte_imsm_93.rc:37` | `property:vendor.volte_md_status=flightmode` | `stop vendor.volte_ua` |
| `vendor/init/init.volte_imsm_93.rc:38` | `property:vendor.volte_md_status=flightmode` | `stop vendor.volte_stack` |
| `vendor/init/init.volte_imsm_93.rc:39` | `property:vendor.volte_md_status=flightmode` | `stop volte_imsm_93` |
| `vendor/init/init.volte_imsm_93.rc:46` | `property:vendor.volte_md_status=exception` | `stop vendor.volte_imcb` |
| `vendor/init/init.volte_imsm_93.rc:47` | `property:vendor.volte_md_status=exception` | `stop vendor.volte_ua` |
| `vendor/init/init.volte_imsm_93.rc:48` | `property:vendor.volte_md_status=exception` | `stop vendor.volte_stack` |
| `vendor/init/init.volte_imsm_93.rc:49` | `property:vendor.volte_md_status=exception` | `stop volte_imsm_93` |
| `vendor/init/init.volte_md_status.rc:6` | `init` | `restart volte_md_status` |
| `vendor/init/init.wod.rc:7` | `property:vendor.volte_md_status=ready && property:persist.vendor.mtk_wfc_support=1` | `start vendor.epdg_wod` |
| `vendor/init/init.wod.rc:10` | `property:vendor.volte_md_status=reset` | `stop vendor.epdg_wod` |
| `vendor/init/init.wod.rc:13` | `property:vendor.volte_md_status=stop` | `stop vendor.epdg_wod` |
| `vendor/init/init.wod.rc:16` | `property:vendor.volte_md_status=flightmode` | `stop vendor.epdg_wod` |
| `vendor/init/init.wod.rc:19` | `property:vendor.volte_md_status=exception` | `stop vendor.epdg_wod` |
| `vendor/init/init.wod.rc:23` | `property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting` | `setprop vendor.ril.mux.report.case 2` |
| `vendor/init/init.wod.rc:24` | `property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting` | `setprop vendor.ril.muxreport 1` |
| `vendor/init/mtkrild.rc:18` | `property:vendor.ril.mtk=1` | `start vendor.ril-daemon-mtk` |
| `vendor/init/mtkrild.rc:21` | `property:vold.decrypt=trigger_restart_framework` | `start vendor.ril-daemon-mtk` |

################################################################################
# 1.13 — WIITE / NRF / TTYS1 INIT BOUNDARY
################################################################################

Source-file authority:
- `init.mt6762.rc` and `init.mt6765.rc` both set nine `wiite_corp_ctrl` nodes to `system:system` and mode `0666`. [VERIFIED]
- `init.mt6762.rc` and `init.mt6765.rc` both set `/dev/ttyS1` to mode `666` and owner `system:system`. [VERIFIED]
- The setter lines occur under the `on boot` action block in both platform files. [VERIFIED]

Exact source lines:

| File:Line | Trigger | Command |
|---|---|---|
| `vendor/init/hw/init.mt6762.rc:876` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6762.rc:877` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6762.rc:878` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6762.rc:879` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6762.rc:880` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6762.rc:881` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6762.rc:882` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6762.rc:883` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6762.rc:884` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6762.rc:885` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6762.rc:886` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6762.rc:887` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6762.rc:888` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6762.rc:889` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6762.rc:890` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6762.rc:891` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6762.rc:892` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6762.rc:893` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6762.rc:896` | `boot` | `chmod 666 /dev/ttyS1` |
| `vendor/init/hw/init.mt6762.rc:897` | `boot` | `chown system system /dev/ttyS1` |
| `vendor/init/hw/init.mt6765.rc:876` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6765.rc:877` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op` |
| `vendor/init/hw/init.mt6765.rc:878` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6765.rc:879` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op` |
| `vendor/init/hw/init.mt6765.rc:880` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6765.rc:881` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc` |
| `vendor/init/hw/init.mt6765.rc:882` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6765.rc:883` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC` |
| `vendor/init/hw/init.mt6765.rc:884` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6765.rc:885` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm` |
| `vendor/init/hw/init.mt6765.rc:886` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6765.rc:887` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/command` |
| `vendor/init/hw/init.mt6765.rc:888` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6765.rc:889` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/version` |
| `vendor/init/hw/init.mt6765.rc:890` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6765.rc:891` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/config` |
| `vendor/init/hw/init.mt6765.rc:892` | `boot` | `chown system system /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6765.rc:893` | `boot` | `chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op` |
| `vendor/init/hw/init.mt6765.rc:896` | `boot` | `chmod 666 /dev/ttyS1` |
| `vendor/init/hw/init.mt6765.rc:897` | `boot` | `chown system system /dev/ttyS1` |

Runtime authority from the current snapshot:
- `/dev/ttyS1` is `crw-rw-rw- system system u:object_r:ttyS_device:s0 4,65`. [ACTIVE_RUNTIME]
- `/sys/class/tty/ttyS1` resolves to `/sys/devices/platform/11003000.serial/tty/ttyS1`. [ACTIVE_RUNTIME]
- `/sys/dev/char/4:65` resolves to the same UART sysfs path. [ACTIVE_RUNTIME]
- The tty uevent reports `MAJOR=4`, `MINOR=65`, `DEVNAME=ttyS1`. [ACTIVE_RUNTIME]
- `toybox lsof /dev/ttyS1` shows `com.wiite.devicedaemon:communication` PID 2144 holding FD 52u on `/dev/ttyS1`. [ACTIVE_RUNTIME]

Live Wiite sysfs nodes:

```text
/sys/devices/virtual/misc/wiite_con_ctrl:
total 0
drwxr-xr-x  3 root root u:object_r:sysfs:s0    0 2026-06-13 01:11 .
drwxr-xr-x 63 root root u:object_r:sysfs:s0    0 2026-06-13 01:11 ..
-r--r--r--  1 root root u:object_r:sysfs:s0 4096 2026-06-14 01:04 dev
-rw-r--r--  1 root root u:object_r:sysfs:s0 4096 2026-06-14 01:04 irq_test
-rw-r--r--  1 root root u:object_r:sysfs:s0 4096 2026-06-14 01:04 pin_set
drwxr-xr-x  2 root root u:object_r:sysfs:s0    0 2026-06-13 01:11 power
lrwxrwxrwx  1 root root u:object_r:sysfs:s0    0 2026-06-14 01:04 subsystem -> ../../../../class/misc
-rw-r--r--  1 root root u:object_r:sysfs:s0 4096 2026-06-13 01:11 uevent
-rw-r--r--  1 root root u:object_r:sysfs:s0 4096 2026-06-14 01:04 version

/sys/devices/virtual/misc/wiite_corp_ctrl:
total 0
drwxr-xr-x  3 root   root   u:object_r:sysfs:s0    0 2026-06-13 01:11 .
drwxr-xr-x 63 root   root   u:object_r:sysfs:s0    0 2026-06-13 01:11 ..
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 acc_dir
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 altitude_value
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 baro_enable
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 baro_value
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 bprs_simu
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 btMAC
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 chip_id
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-14 01:36 command
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 config
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 delay
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 dev
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 event_simu
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 hrs_simu
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 i2c_log
-rw-r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 i2c_test
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 lcm_type
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 local_rtc
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 longterm
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 23:50 nrf_rtc
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 nrf_sys_mode
drwxr-xr-x  2 root   root   u:object_r:sysfs:s0    0 2026-06-13 01:11 power
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 rst_op
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 spo2_simu
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 step_simu
lrwxrwxrwx  1 root   root   u:object_r:sysfs:s0    0 2026-06-14 01:04 subsystem -> ../../../../class/misc
-r--r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 tempearture_value
--w-------  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 tilt_simu
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-14 00:00 uart_op
-rw-r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-13 01:11 uevent
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 upgrade_op
--w--w----  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 user_command
-rw-r--r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 vdd18_con
-rw-rw-rw-  1 system system u:object_r:sysfs:s0 4096 2026-06-13 01:11 version
-rw-rw-r--  1 root   root   u:object_r:sysfs:s0 4096 2026-06-14 01:04 vibr_test
```

Interpretation: init itself exposes the Wiite/nRF sysfs and UART control boundary before
the APK layer is analyzed. The runtime snapshot proves that the UID-1000 Wiite communication
process owns the UART bridge at runtime. Protocol and APK behavior are carried to Section 13
and Section 16; the init-layer boundary is closed here.

################################################################################
# 1.14 — WIITE UID-1000 RUNTIME HANDOFF BOUNDARY
################################################################################

| SELinux label | User | PID | PPID | Process | Args |
|---|---|---:|---:|---|---|
| `u:r:system_app:s0` | `system` | 1262 | 512 | `com.wiite.WiiteCommon` | `com.wiite.WiiteCommon` |
| `u:r:system_app:s0` | `system` | 1881 | 512 | `com.wiite.devicedaemon` | `com.wiite.devicedaemon` |
| `u:r:system_app:s0` | `system` | 1929 | 512 | `com.wiite.cleantask` | `com.wiite.cleantask` |
| `u:r:system_app:s0` | `system` | 2144 | 512 | `com.wiite.devicedaemon:communication` | `com.wiite.devicedaemon:communication` |
| `u:r:system_app:s0` | `system` | 2519 | 512 | `com.wiite.wearhealthuart` | `com.wiite.wearhealthuart` |
| `u:r:system_app:s0` | `system` | 2532 | 512 | `com.wiite.wiite_heartrate` | `com.wiite.wiite_heartrate` |
| `u:r:system_app:s0` | `system` | 2570 | 512 | `com.wiite.wiite_sleep` | `com.wiite.wiite_sleep` |
| `u:r:system_app:s0` | `system` | 2584 | 512 | `com.wiite.wiite_bloodoxygen` | `com.wiite.wiite_bloodoxygen` |
| `u:r:system_app:s0` | `system` | 3850 | 512 | `com.wiite.power` | `com.wiite.power` |
| `u:r:system_app:s0` | `system` | 3881 | 512 | `com.wiite.wiitestore` | `com.wiite.wiitestore` |

Runtime handoff chain:

```text
init -> zygote/system_server -> persistent/system UID package start -> Wiite UID-1000 process cluster
     -> com.wiite.devicedaemon:communication -> /dev/ttyS1 -> MTK UART1 -> nRF / Wiite external control plane
```

Classification: [ACTIVE_RUNTIME] [ODM_CONTROL_BOUNDARY] [UART_BRIDGE] [SECURITY_CRITICAL].

################################################################################
# 1.15 — FACTORY / META / DIAGNOSTIC INIT PROFILES
################################################################################

The production vendor tree carries factory and meta boot profiles. They are not treated as
ordinary runtime HAL files. They are documented as factory/meta diagnostic surfaces because
they declare alternate boot actions, logging daemons, meta services, adbd starts, console
starts, modem/logger routes, and manufacturing/debug services.

| File | Bytes | Lines | SHA256 |
|---|---:|---:|---|
| `vendor/init/hw/factory_init.connectivity.rc` | 1943 | 93 | `7ffec7a83549f0795738446020ccbe858f5f8ae852deaedfc461dada059e1f06` |
| `vendor/init/hw/factory_init.project.rc` | 937 | 37 | `fec77385643179163bdf94c05f6b87c483a4922f8b5c5c9bb74816fd17db4df1` |
| `vendor/init/hw/factory_init.rc` | 27653 | 831 | `7b10e8a5235b96d4c24170109fa3aa7ba2c258967cef5865c7ddf0345f58dd76` |
| `vendor/init/hw/meta_init.connectivity.rc` | 1766 | 88 | `7fc55cfc7fbca1a1af8e901b456e2852c7f2313da5243d9389bb4855b2529a7e` |
| `vendor/init/hw/meta_init.modem.rc` | 1335 | 54 | `0a7e4330aa8e226c0fc5b1a60ff2a69b942c9161b5d415f13743b4d58a8ab579` |
| `vendor/init/hw/meta_init.project.rc` | 197 | 14 | `2777f0f39ef29d2cd5350820d8a8e2d5c0b361fd8aa31b241a80fdca000c991d` |
| `vendor/init/hw/meta_init.rc` | 23214 | 660 | `6d2f16a17f0c45719872deb65c167b932147207bf5479c9a09b299977658f6c5` |

Factory/meta service declarations:

| File:Line | Service | Command | User | Group | Options | Runtime |
|---|---|---|---|---|---|---|
| `vendor/init/hw/factory_init.connectivity.rc:34` | `wmt_loader` | `/vendor/bin/wmt_loader` | `system` | `system` | user=system; group=system; oneshot | `stopped` |
| `vendor/init/hw/factory_init.connectivity.rc:39` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `system` | `system` | user=system; group=system | `running` |
| `vendor/init/hw/factory_init.connectivity.rc:55` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet | `running` |
| `vendor/init/hw/factory_init.project.rc:3` | `msensord` | `/system/bin/msensord` | `system` | `system` | user=system; group=system; oneshot | `` |
| `vendor/init/hw/factory_init.project.rc:8` | `akmd8963` | `/system/bin/akmd8963` | `system` | `system` | user=system; group=system; disabled | `` |
| `vendor/init/hw/factory_init.project.rc:21` | `spm_script` | `/vendor/bin/spm_loader` | `system` | `system` | user=system; group=system; oneshot | `` |
| `vendor/init/hw/factory_init.rc:611` | `ueventd` | `/system/bin/ueventd` | `` | `` | class=core; u:r:ueventd:s0; critical | `running` |
| `vendor/init/hw/factory_init.rc:616` | `console` | `/system/bin/sh` | `shell` | `shell log readproc` | class=core; user=shell; group=shell log readproc; u:r:shell:s0; disabled | `` |
| `vendor/init/hw/factory_init.rc:634` | `adbd` | `/system/bin/adbd --root_seclabel=u:r:su:s0` | `` | `` | class=core; socket=adbd stream 660 system system; u:r:adbd:s0; disabled | `running` |
| `vendor/init/hw/factory_init.rc:644` | `thermal_manager` | `/vendor/bin/thermal_manager` | `root` | `system` | user=root; group=system; oneshot | `stopped` |
| `vendor/init/hw/factory_init.rc:649` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `running` |
| `vendor/init/hw/factory_init.rc:654` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `system` | `radio system` | user=system; group=radio system; oneshot | `running` |
| `vendor/init/hw/factory_init.rc:659` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `running` |
| `vendor/init/hw/factory_init.rc:664` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `system` | `radio system` | user=system; group=radio system; oneshot | `stopped` |
| `vendor/init/hw/factory_init.rc:669` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `` |
| `vendor/init/hw/factory_init.rc:675` | `fuelgauged` | `/vendor/bin/fuelgauged` | `system` | `system` | class=core; user=system; group=system | `running` |
| `vendor/init/hw/factory_init.rc:680` | `mdlogger` | `/system/bin/mdlogger` | `shell` | `shell system radio sdcard_rw inet` | class=main; user=shell; group=shell system radio sdcard_rw inet; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:687` | `dualmdlogger` | `/system/bin/dualmdlogger` | `shell` | `shell system radio sdcard_rw inet` | class=main; user=shell; group=shell system radio sdcard_rw inet; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:694` | `factory_no_image` | `/vendor/bin/factory` | `radio` | `radio system wifi media audio gps bluetooth wakelock` | user=radio; group=radio system wifi media audio gps bluetooth wakelock; capabilities=BLOCK_SUSPEND NET_RAW NET_ADMIN SYS_ADMIN SYS_BOOT | `` |
| `vendor/init/hw/factory_init.rc:699` | `emdlogger1` | `/system/bin/emdlogger1` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:706` | `emdlogger2` | `/system/bin/emdlogger2` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:713` | `emdlogger3` | `/system/bin/emdlogger3` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:720` | `emdlogger5` | `/system/bin/emdlogger5` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:727` | `emdlogger6` | `/system/bin/emdlogger6` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw; disabled; oneshot | `` |
| `vendor/init/hw/factory_init.rc:734` | `connsyslogger` | `/vendor/bin/connsyslogger` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | `` |
| `vendor/init/hw/init.mt6762.rc:1076` | `meta_tst` | `/vendor/bin/meta_tst` | `system` | `radio gps system wifi audio bluetooth media` | class=main; user=system; group=radio gps system wifi audio bluetooth media; capabilities=NET_RAW NET_ADMIN; disabled; oneshot | `` |
| `vendor/init/hw/init.mt6762.rc:1086` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `root` | `system` | class=main; user=root; group=system; disabled | `` |
| `vendor/init/hw/init.mt6765.rc:1076` | `meta_tst` | `/vendor/bin/meta_tst` | `system` | `radio gps system wifi audio bluetooth media` | class=main; user=system; group=radio gps system wifi audio bluetooth media; capabilities=NET_RAW NET_ADMIN; disabled; oneshot | `` |
| `vendor/init/hw/init.mt6765.rc:1086` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `root` | `system` | class=main; user=root; group=system; disabled | `` |
| `vendor/init/hw/meta_init.connectivity.rc:28` | `wmt_loader` | `/vendor/bin/wmt_loader` | `system` | `system` | user=system; group=system; oneshot | `stopped` |
| `vendor/init/hw/meta_init.connectivity.rc:33` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `system` | `system` | user=system; group=system | `running` |
| `vendor/init/hw/meta_init.connectivity.rc:49` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet | `running` |
| `vendor/init/hw/meta_init.modem.rc:31` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `running` |
| `vendor/init/hw/meta_init.modem.rc:36` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `system` | `radio system` | user=system; group=radio system; oneshot | `running` |
| `vendor/init/hw/meta_init.modem.rc:41` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `running` |
| `vendor/init/hw/meta_init.modem.rc:46` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `system` | `radio system` | user=system; group=radio system; oneshot | `stopped` |
| `vendor/init/hw/meta_init.modem.rc:51` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `radio` | `radio system` | user=radio; group=radio system; oneshot | `` |
| `vendor/init/hw/meta_init.project.rc:10` | `spm_script` | `/vendor/bin/spm_loader` | `system` | `system` | user=system; group=system; oneshot | `` |
| `vendor/init/hw/meta_init.rc:540` | `ueventd` | `/system/bin/ueventd` | `` | `` | class=core; u:r:ueventd:s0; critical | `running` |
| `vendor/init/hw/meta_init.rc:545` | `console` | `/system/bin/sh` | `shell` | `shell log readproc` | class=core; user=shell; group=shell log readproc; u:r:shell:s0; disabled | `` |
| `vendor/init/hw/meta_init.rc:564` | `adbd` | `/system/bin/adbd --root_seclabel=u:r:su:s0` | `` | `` | class=core; socket=adbd stream 660 system system; u:r:adbd:s0; disabled | `running` |
| `vendor/init/hw/meta_init.rc:571` | `emdlogger1` | `/system/bin/emdlogger1` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw | `` |
| `vendor/init/hw/meta_init.rc:576` | `meta_tst` | `/vendor/bin/meta_tst` | `radio` | `radio system wifi audio bluetooth media gps` | user=radio; group=radio system wifi audio bluetooth media gps | `` |
| `vendor/init/hw/meta_init.rc:580` | `connsyslogger` | `/system/bin/connsyslogger` | `shell` | `system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi` | class=main; user=shell; group=system shell radio sdcard_rw inet sdcard_r media_rw bluetooth gps wifi | `` |
| `vendor/init/hw/meta_init.rc:585` | `loghidlvendorservice` | `/vendor/bin/loghidlvendorservice` | `shell` | `system shell radio` | class=main; user=shell; group=system shell radio | `` |
| `vendor/init/hw/meta_init.rc:590` | `loghidlsysservice` | `/system/bin/loghidlsysservice` | `shell` | `system shell radio` | class=main; user=shell; group=system shell radio | `` |
| `vendor/init/hw/meta_init.rc:596` | `fuelgauged` | `/vendor/bin/fuelgauged` | `system` | `system` | class=core; user=system; group=system | `running` |

Interpretation: these profiles are shipped in the vendor image. A clean ROM should not carry
factory/meta profiles into the default user boot path unless a specific hardware dependency is
proven. They are `[STRIP_OR_ISOLATE]` candidates for Section 18.

################################################################################
# 1.16 — RADIO / IMS / WFC / EPDG / RIL INIT GRAPH
################################################################################

| File:Line | Service | Command | Runtime | User | Group | Caps / sockets / interfaces |
|---|---|---|---|---|---|---|
| `vendor/init/gsm0710muxd.rc:1` | `vendor.gsm0710muxd` | `/vendor/bin/gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic` | `running` | `root` | `radio cache inet misc` | class=main; user=root; group=radio cache inet misc; disabled; oneshot |
| `vendor/init/hw/factory_init.rc:649` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/hw/factory_init.rc:654` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `system` | `radio system` | user=system; group=radio system; oneshot |
| `vendor/init/hw/factory_init.rc:659` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/hw/factory_init.rc:664` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `system` | `radio system` | user=system; group=radio system; oneshot |
| `vendor/init/hw/factory_init.rc:669` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/hw/init.connectivity.rc:44` | `mnld` | `/vendor/bin/mnld` | `running` | `gps` | `gps inet misc sdcard_rw sdcard_r media_rw system radio wakelock` | class=main; user=gps; group=gps inet misc sdcard_rw sdcard_r media_rw system radio wakelock; socket=mnld stream 660 gps system |
| `vendor/init/hw/init.connectivity.rc:51` | `lbs_dbg` | `/system/bin/lbs_dbg` | `running` | `system` | `gps inet misc sdcard_rw sdcard_r media_rw system radio` | class=main; user=system; group=gps inet misc sdcard_rw sdcard_r media_rw system radio; socket=lbs_dbg stream 660 gps system |
| `vendor/init/hw/init.modem.rc:28` | `viarild` | `/vendor/bin/viarild -l /vendor/lib/libviatelecom-withuim-ril.so -- -d /dev/ttySDIO4` | `` | `` | `radio cache inet misc audio sdcard_r sdcard_rw log system` | class=core; group=radio cache inet misc audio sdcard_r sdcard_rw log system; socket=rild-via stream 660 radio radio ; msap_c2k_socket1 stream 660 radio radio ; msap_c2k_socket2 stream 660 radio radio ; msap_c2k_socket3 stream 660 radio radio ; msap_c2k_socket4 stream 660 radio radio ; rpc stream 660 radio system ; rildc-debug stream 660 radio system ; rild-ctclient stream 660 radio system ; rild-atci-c2k stream 660 radio system; disabled; oneshot |
| `vendor/init/hw/meta_init.modem.rc:31` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/hw/meta_init.modem.rc:36` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `system` | `radio system` | user=system; group=radio system; oneshot |
| `vendor/init/hw/meta_init.modem.rc:41` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/hw/meta_init.modem.rc:46` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `system` | `radio system` | user=system; group=radio system; oneshot |
| `vendor/init/hw/meta_init.modem.rc:51` | `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `` | `radio` | `radio system` | user=radio; group=radio system; oneshot |
| `vendor/init/init.bip.rc:19` | `vendor.bip` | `/vendor/bin/bip` | `running` | `root` | `system log inet radio net_admin root` | class=core; user=root; group=system log inet radio net_admin root; socket=vendor.bip stream 660 root system; disabled; oneshot |
| `vendor/init/init.cccifsd.rc:22` | `vendor.ccci_fsd` | `/vendor/bin/ccci_fsd 0` | `running` | `radio` | `radio system root` | class=core; user=radio; group=radio system root; oneshot; priority -20; ioprio rt 4 |
| `vendor/init/init.cccifsd.rc:30` | `vendor.ccci3_fsd` | `/vendor/bin/ccci_fsd 2` | `running` | `radio` | `radio system` | class=core; user=radio; group=radio system; oneshot |
| `vendor/init/init.cccimdinit.rc:21` | `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | `running` | `system` | `radio system` | class=core; user=system; group=radio system; oneshot |
| `vendor/init/init.cccimdinit.rc:27` | `ccci3_mdinit` | `/vendor/bin/ccci_mdinit 2` | `stopped` | `system` | `radio system` | class=core; user=system; group=radio system; oneshot |
| `vendor/init/init.cccirpcd.rc:3` | `vendor.ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | `running` | `radio` | `radio system` | class=core; user=radio; group=radio system; oneshot |
| `vendor/init/init.volte_imcb.rc:3` | `vendor.volte_imcb` | `/vendor/bin/volte_imcb` | `running` | `root` | `system log inet radio` | class=main; user=root; group=system log inet radio; socket=volte_imsa1 stream 660 root radio ; volte_imsa2 stream 660 root radio ; volte_ut stream 660 root radio; disabled; oneshot |
| `vendor/init/init.volte_imsm_93.rc:51` | `volte_imsm_93` | `/vendor/bin/volte_imsm_93` | `running` | `root` | `radio cache inet misc log system wifi` | class=main; user=root; group=radio cache inet misc log system wifi; disabled; oneshot |
| `vendor/init/init.volte_md_status.rc:8` | `volte_md_status` | `/system/vendor/bin/volte_md_status` | `running` | `root` | `radio log system` | class=core; user=root; group=radio log system; disabled; oneshot |
| `vendor/init/init.volte_stack.rc:4` | `vendor.volte_stack` | `/vendor/bin/volte_stack` | `running` | `root` | `system log inet radio` | class=main; user=root; group=system log inet radio; socket=volte_stack stream 660 root system; disabled; oneshot |
| `vendor/init/init.volte_ua.rc:3` | `vendor.volte_ua` | `/vendor/bin/volte_ua` | `running` | `root` | `system log inet radio net_admin root` | class=main; user=root; group=system log inet radio net_admin root; socket=volte_ua stream 660 root system; disabled; oneshot |
| `vendor/init/init.wfca.rc:5` | `wfca` | `/vendor/bin/wfca` | `running` | `root` | `system log inet radio net_admin root` | class=core; user=root; group=system log inet radio net_admin root; socket=wfca stream 660 root system ; wfca_rds stream 660 root system |
| `vendor/init/init.wod.rc:26` | `vendor.epdg_wod` | `/vendor/bin/epdg_wod` | `running` | `root` | `root system log inet radio net_admin` | class=late_start; user=root; group=root system log inet radio net_admin; socket=wod_action stream 660 root system ; wod_sim stream 660 root system; disabled |
| `vendor/init/ipsec_mon.rc:1` | `ipsec_mon` | `/vendor/bin/ipsec_mon` | `running` | `system` | `net_admin net_raw system misc` | class=main; user=system; group=net_admin net_raw system misc; capabilities=NET_ADMIN NET_RAW |
| `vendor/init/lbs_hidl_service.rc:1` | `lbs_hidl_service` | `/vendor/bin/lbs_hidl_service` | `running` | `system` | `system gps radio inet sdcard_r sdcard_rw` | class=hal; user=system; group=system gps radio inet sdcard_r sdcard_rw |
| `vendor/init/mtk_agpsd_p.rc:4` | `agpsd` | `/vendor/bin/mtk_agpsd` | `running` | `gps` | `gps radio inet sdcard_r sdcard_rw misc system` | class=main; user=gps; group=gps radio inet sdcard_r sdcard_rw misc system; socket=agpsd stream 660 gps system ; agpsd2 stream 660 gps inet ; agpsd3 stream 660 gps inet |
| `vendor/init/mtkrild.rc:5` | `vendor.ril-daemon-mtk` | `/vendor/bin/hw/mtkfusionrild -l libmtk-ril.so` | `running` | `radio` | `radio cache inet misc audio log readproc wakelock sdcard_r sdcard_rw system` | class=main; user=radio; group=radio cache inet misc audio log readproc wakelock sdcard_r sdcard_rw system; capabilities=BLOCK_SUSPEND NET_ADMIN NET_RAW; socket=sap_uim_socket1 stream 660 bluetooth bluetooth ; rild-oem stream 660 radio system ; volte_imsvt1 stream 660 root radio ; rild-atci stream 660 root radio; disabled; oneshot |

Runtime properties proving active radio/IMS/WFC/ePDG state include:
- vendor.ril.mtk                      : 1
- vendor.mtk.md1.status               : ready
- vendor.volte_md_status              : ready
- persist.vendor.volte_support        : 1
- persist.vendor.ims_support          : 1
- persist.vendor.mtk.volte.enable     : 1
- persist.vendor.mtk.wfc.enable       : 1
- vendor.net.ims.ipsec.version        : 2.0
- vendor.net.ims.volte.pid            : 823
- vendor.gsm.project.baseband         : TK_MD_BASIC(LWTG_6177M_R3_6762)
- vendor.rild.libpath                 : mtk-ril.so
- vendor.rild.libargs                 : -d /dev/ttyC0

Interpretation: Section 01 proves a live modem/RIL/IMS/WFC/ePDG init and runtime graph.
Endpoint behavior and data-flow analysis belong to Sections 08, 13, and 16.

################################################################################
# 1.17 — CONNECTIVITY, WMT, WIFI, BLUETOOTH, GNSS INIT GRAPH
################################################################################

| File:Line | Service | Command | Runtime | User | Group | Options |
|---|---|---|---|---|---|---|
| `system/init/wificond.rc:1` | `wificond` | `/system/bin/wificond` | `running` | `wifi` | `wifi net_raw net_admin` | class=main; user=wifi; group=wifi net_raw net_admin; capabilities=NET_RAW NET_ADMIN |
| `vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:1` | `bluetooth-1-0` | `/vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek` | `running` | `bluetooth` | `bluetooth` | class=hal; user=bluetooth; group=bluetooth; capabilities=SYS_NICE; writepid /dev/stune/foreground/tasks |
| `vendor/init/android.hardware.gnss@2.0-service-mediatek.rc:1` | `gnss_service` | `/vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek` | `running` | `system` | `system gps` | class=hal; user=system; group=system gps |
| `vendor/init/android.hardware.wifi@1.0-service-lazy-mediatek.rc:1` | `vendor.wifi_hal_legacy` | `/vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek` | `running` | `wifi` | `wifi gps` | class=hal; user=wifi; group=wifi gps; capabilities=NET_ADMIN NET_RAW SYS_MODULE; interface=android.hardware.wifi@1.0::IWifi default; disabled; oneshot |
| `vendor/init/hw/factory_init.connectivity.rc:34` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `system` | `system` | user=system; group=system; oneshot |
| `vendor/init/hw/factory_init.connectivity.rc:39` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `running` | `system` | `system` | user=system; group=system |
| `vendor/init/hw/factory_init.connectivity.rc:55` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet |
| `vendor/init/hw/init.connectivity.rc:51` | `lbs_dbg` | `/system/bin/lbs_dbg` | `running` | `system` | `gps inet misc sdcard_rw sdcard_r media_rw system radio` | class=main; user=system; group=gps inet misc sdcard_rw sdcard_r media_rw system radio; socket=lbs_dbg stream 660 gps system |
| `vendor/init/hw/init.mt6762.rc:1009` | `pppoe_wlan0` | `/vendor/bin/launchpppoe` | `` | `root` | `radio cache inet misc` | user=root; group=radio cache inet misc; disabled; oneshot |
| `vendor/init/hw/init.mt6765.rc:1009` | `pppoe_wlan0` | `/vendor/bin/launchpppoe` | `` | `root` | `radio cache inet misc` | user=root; group=radio cache inet misc; disabled; oneshot |
| `vendor/init/hw/meta_init.connectivity.rc:28` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `system` | `system` | user=system; group=system; oneshot |
| `vendor/init/hw/meta_init.connectivity.rc:33` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/` | `running` | `system` | `system` | user=system; group=system |
| `vendor/init/hw/meta_init.connectivity.rc:49` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet |
| `vendor/init/init_connectivity.rc:10` | `wmt_loader` | `/vendor/bin/wmt_loader` | `stopped` | `system` | `system` | class=early_hal; user=system; group=system; oneshot |
| `vendor/init/init_connectivity.rc:16` | `wmt_launcher` | `/vendor/bin/wmt_launcher -p /vendor/firmware/ -o 1` | `running` | `system` | `system` | class=early_hal; user=system; group=system |
| `vendor/init/lbs_hidl_service.rc:1` | `lbs_hidl_service` | `/vendor/bin/lbs_hidl_service` | `running` | `system` | `system gps radio inet sdcard_r sdcard_rw` | class=hal; user=system; group=system gps radio inet sdcard_r sdcard_rw |
| `vendor/init/mtk_agpsd_p.rc:4` | `agpsd` | `/vendor/bin/mtk_agpsd` | `running` | `gps` | `gps radio inet sdcard_r sdcard_rw misc system` | class=main; user=gps; group=gps radio inet sdcard_r sdcard_rw misc system; socket=agpsd stream 660 gps system ; agpsd2 stream 660 gps inet ; agpsd3 stream 660 gps inet |
| `vendor/init/wlan_assistant.rc:1` | `wlan_assistant` | `/vendor/bin/wlan_assistant` | `running` | `wifi` | `system gps inet` | class=main; user=wifi; group=system gps inet |

- vendor.connsys.driver.ready         : yes
- vendor.connsys.formeta.ready        : yes
- persist.vendor.connsys.chipid       : 0x6765
- persist.vendor.connsys.bt_fw_ver    : t-neptune-main-soc1_0e1-1916-CERVINO_SOC1_0_E1_ASIC-20200326221201
- persist.vendor.connsys.wifi_fw_ver  : 200326221201000
- persist.vendor.connsys.patch.version: 200326221201000

################################################################################
# 1.18 — SECURITY SUBSTRATE INIT GRAPH
################################################################################

| File:Line | Service | Command | Runtime | User | Group | Options |
|---|---|---|---|---|---|---|
| `system/init/drmserver.rc:1` | `drm` | `/system/bin/drmserver` | `running` | `drm` | `drm system inet drmrpc readproc sdcard_rw media_rw` | class=main; user=drm; group=drm system inet drmrpc readproc sdcard_rw media_rw; writepid /dev/cpuset/foreground/tasks |
| `system/init/gatekeeperd.rc:1` | `gatekeeperd` | `/system/bin/gatekeeperd /data/misc/gatekeeper` | `running` | `system` | `` | class=late_start; user=system; writepid /dev/cpuset/system-background/tasks |
| `system/init/mediadrmserver.rc:1` | `mediadrm` | `/system/bin/mediadrmserver` | `running` | `media` | `mediadrm drmrpc` | class=main; user=media; group=mediadrm drmrpc; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |
| `system/init/wait_for_keymaster.rc:1` | `wait_for_keymaster` | `/system/bin/wait_for_keymaster` | `` | `root` | `root system` | user=root; group=root system; priority -20; ioprio rt 0 |
| `vendor/init/android.hardware.drm@1.0-service.rc:1` | `vendor.drm-hal-1-0` | `/vendor/bin/hw/android.hardware.drm@1.0-service` | `running` | `media` | `mediadrm drmrpc` | class=hal; user=media; group=mediadrm drmrpc; interface=android.hardware.drm@1.0::ICryptoFactory default ; android.hardware.drm@1.0::IDrmFactory default; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |
| `vendor/init/android.hardware.drm@1.2-service.clearkey.rc:1` | `vendor.drm-clearkey-hal-1-2` | `/vendor/bin/hw/android.hardware.drm@1.2-service.clearkey` | `running` | `media` | `media mediadrm` | class=hal; user=media; group=media mediadrm; interface=android.hardware.drm@1.0::ICryptoFactory clearkey ; android.hardware.drm@1.0::IDrmFactory clearkey ; android.hardware.drm@1.1::ICryptoFactory clearkey ; android.hardware.drm@1.1::IDrmFactory clearkey ; android.hardware.drm@1.2::ICryptoFactory clearkey ; android.hardware.drm@1.2::IDrmFactory clearkey; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |
| `vendor/init/android.hardware.drm@1.2-service.widevine.rc:12` | `vendor.drm-widevine-hal-1-2` | `/vendor/bin/hw/android.hardware.drm@1.2-service.widevine` | `running` | `media` | `media mediadrm drmrpc system` | class=hal; user=media; group=media mediadrm drmrpc system; interface=android.hardware.drm@1.0::ICryptoFactory widevine ; android.hardware.drm@1.0::IDrmFactory widevine ; android.hardware.drm@1.1::ICryptoFactory widevine ; android.hardware.drm@1.1::IDrmFactory widevine ; android.hardware.drm@1.2::ICryptoFactory widevine ; android.hardware.drm@1.2::IDrmFactory widevine ; android.hidl.base@1.0::IBase widevine; ioprio rt 4; writepid /dev/cpuset/foreground/tasks |
| `vendor/init/android.hardware.gatekeeper@1.0-service.rc:1` | `vendor.gatekeeper-1-0` | `/vendor/bin/hw/android.hardware.gatekeeper@1.0-service` | `running` | `system` | `system` | class=hal; user=system; group=system |
| `vendor/init/android.hardware.keymaster@4.0-service.rc:1` | `vendor.keymaster-4-0` | `/vendor/bin/hw/android.hardware.keymaster@4.0-service` | `running` | `system` | `system drmrpc` | class=early_hal; user=system; group=system drmrpc |
| `vendor/init/android.hardware.secure_element@1.0-service-mediatek.rc:1` | `mtk_secure_element_hal_service` | `/vendor/bin/hw/android.hardware.secure_element@1.0-service-mediatek` | `running` | `secure_element` | `secure_element` | class=hal; user=secure_element; group=secure_element |

Ordering rule: `vendor.keymaster-4-0` is declared in class `early_hal` and is running in
the runtime snapshot. This is required before vold/FBE-dependent paths are considered stable.

################################################################################
# 1.19 — USB, ACM, ADB, MASS-STORAGE AND FACTORY GADGET GRAPH
################################################################################

| File:Line | Trigger | Command |
|---|---|---|
| `root/init.usb.configfs.rc:2` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `root/init.usb.configfs.rc:3` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `stop adbd` |
| `root/init.usb.configfs.rc:4` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `setprop sys.usb.ffs.ready 0` |
| `root/init.usb.configfs.rc:5` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceClass 0` |
| `root/init.usb.configfs.rc:6` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceSubClass 0` |
| `root/init.usb.configfs.rc:7` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceProtocol 0` |
| `root/init.usb.configfs.rc:8` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:9` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:10` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f3` |
| `root/init.usb.configfs.rc:11` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rmdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `root/init.usb.configfs.rc:12` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:15` | `property:init.svc.adbd=stopped` | `setprop sys.usb.ffs.ready 0` |
| `root/init.usb.configfs.rc:18` | `property:sys.usb.config=adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:21` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb"` |
| `root/init.usb.configfs.rc:22` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:23` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:24` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:27` | `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"` |
| `root/init.usb.configfs.rc:28` | `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:29` | `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:30` | `property:sys.usb.config=mtp && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:33` | `property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:36` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb"` |
| `root/init.usb.configfs.rc:37` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:38` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:39` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:40` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:43` | `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp"` |
| `root/init.usb.configfs.rc:44` | `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:45` | `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:46` | `property:sys.usb.config=ptp && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:49` | `property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:52` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb"` |
| `root/init.usb.configfs.rc:53` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:54` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:55` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:56` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:59` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory"` |
| `root/init.usb.configfs.rc:60` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:61` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:62` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:65` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:68` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_adb"` |
| `root/init.usb.configfs.rc:69` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:70` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:71` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:72` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:75` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "audiosource"` |
| `root/init.usb.configfs.rc:76` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:77` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:78` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:81` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:84` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "audiosource_adb"` |
| `root/init.usb.configfs.rc:85` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:86` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:87` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:88` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:91` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_audiosource"` |
| `root/init.usb.configfs.rc:92` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:93` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:94` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:95` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:98` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:101` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_audiosource_adb"` |
| `root/init.usb.configfs.rc:102` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:103` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:104` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f3` |
| `root/init.usb.configfs.rc:105` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:106` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:109` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "midi"` |
| `root/init.usb.configfs.rc:110` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/midi.gs5 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:111` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:112` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:115` | `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:118` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "midi_adb"` |
| `root/init.usb.configfs.rc:119` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/midi.gs5 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:120` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:121` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:122` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:125` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `root/init.usb.configfs.rc:126` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis"` |
| `root/init.usb.configfs.rc:127` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:128` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:129` | `property:sys.usb.config=rndis && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.configfs.rc:132` | `property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `start adbd` |
| `root/init.usb.configfs.rc:135` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `root/init.usb.configfs.rc:136` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"` |
| `root/init.usb.configfs.rc:137` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `root/init.usb.configfs.rc:138` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `root/init.usb.configfs.rc:139` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `root/init.usb.configfs.rc:140` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:7` | `post-fs-data` | `chown system system /sys/class/android_usb/android0/f_mass_storage/lun/file` |
| `root/init.usb.rc:8` | `post-fs-data` | `chmod 0660 /sys/class/android_usb/android0/f_mass_storage/lun/file` |
| `root/init.usb.rc:9` | `post-fs-data` | `chown system system /sys/class/android_usb/android0/f_rndis/ethaddr` |
| `root/init.usb.rc:10` | `post-fs-data` | `chmod 0660 /sys/class/android_usb/android0/f_rndis/ethaddr` |
| `root/init.usb.rc:22` | `boot` | `setprop sys.usb.configfs 0` |
| `root/init.usb.rc:26` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | `stop adbd` |
| `root/init.usb.rc:27` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:28` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/bDeviceClass 0` |
| `root/init.usb.rc:29` | `property:sys.usb.config=none && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:35` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:36` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:37` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 4EE7` |
| `root/init.usb.rc:38` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:39` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:40` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:41` | `property:sys.usb.config=adb && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:45` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:46` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:47` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d00` |
| `root/init.usb.rc:48` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:49` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:50` | `property:sys.usb.config=accessory && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:54` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:55` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:56` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d01` |
| `root/init.usb.rc:57` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:58` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:59` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:60` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:64` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:65` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:66` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d02` |
| `root/init.usb.rc:67` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:68` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:69` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:73` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:74` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:75` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d03` |
| `root/init.usb.rc:76` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:77` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:78` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:79` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:83` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:84` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:85` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d04` |
| `root/init.usb.rc:86` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:87` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:88` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:92` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 0` |
| `root/init.usb.rc:93` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idVendor 18d1` |
| `root/init.usb.rc:94` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/idProduct 2d05` |
| `root/init.usb.rc:95` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/functions ${sys.usb.config}` |
| `root/init.usb.rc:96` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `write /sys/class/android_usb/android0/enable 1` |
| `root/init.usb.rc:97` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `start adbd` |
| `root/init.usb.rc:98` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0` | `setprop sys.usb.state ${sys.usb.config}` |
| `root/init.usb.rc:103` | `boot && property:persist.sys.usb.config=*` | `setprop sys.usb.config ${persist.sys.usb.config}` |
| `root/init.usb.rc:111` | `property:sys.usb.typec.mode=dfp` | `write /sys/class/dual_role_usb/otg_default/mode ${sys.usb.typec.mode}` |
| `root/init.usb.rc:112` | `property:sys.usb.typec.mode=dfp` | `setprop sys.usb.typec.state ${sys.usb.typec.mode}` |
| `root/init.usb.rc:115` | `property:sys.usb.typec.mode=ufp` | `write /sys/class/dual_role_usb/otg_default/mode ${sys.usb.typec.mode}` |
| `root/init.usb.rc:116` | `property:sys.usb.typec.mode=ufp` | `setprop sys.usb.typec.state ${sys.usb.typec.mode}` |
| `root/init.usb.rc:120` | `property:sys.usb.typec.data_role=device` | `write /sys/class/dual_role_usb/otg_default/data_role ${sys.usb.typec.data_role}` |
| `root/init.usb.rc:121` | `property:sys.usb.typec.data_role=device` | `setprop sys.usb.typec.state ${sys.usb.typec.data_role}` |
| `root/init.usb.rc:124` | `property:sys.usb.typec.data_role=host` | `write /sys/class/dual_role_usb/otg_default/data_role ${sys.usb.typec.data_role}` |
| `root/init.usb.rc:125` | `property:sys.usb.typec.data_role=host` | `setprop sys.usb.typec.state ${sys.usb.typec.data_role}` |
| `root/init.usb.rc:129` | `property:sys.usb.typec.power_role=source` | `write /sys/class/dual_role_usb/otg_default/power_role ${sys.usb.typec.power_role}` |
| `root/init.usb.rc:130` | `property:sys.usb.typec.power_role=source` | `setprop sys.usb.typec.state ${sys.usb.typec.power_role}` |
| `root/init.usb.rc:133` | `property:sys.usb.typec.power_role=sink` | `write /sys/class/dual_role_usb/otg_default/power_role ${sys.usb.typec.power_role}` |
| `root/init.usb.rc:134` | `property:sys.usb.typec.power_role=sink` | `setprop sys.usb.typec.state ${sys.usb.typec.power_role}` |
| `vendor/init/hw/factory_init.project.rc:27` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/enable 0` |
| `vendor/init/hw/factory_init.project.rc:28` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/idVendor ${sys.usb.vid}` |
| `vendor/init/hw/factory_init.project.rc:29` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/idProduct 2029` |
| `vendor/init/hw/factory_init.project.rc:30` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/f_acm/port_index 2,4` |
| `vendor/init/hw/factory_init.project.rc:31` | `property:sys.usb.config=gs1gs3` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/factory_init.project.rc:32` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/functions mass_storage,adb,acm` |
| `vendor/init/hw/factory_init.project.rc:33` | `property:sys.usb.config=gs1gs3` | `write /sys/class/android_usb/android0/enable 1` |
| `vendor/init/hw/factory_init.project.rc:34` | `property:sys.usb.config=gs1gs3` | `start adbd` |
| `vendor/init/hw/factory_init.project.rc:35` | `property:sys.usb.config=gs1gs3` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/factory_init.project.rc:36` | `property:sys.usb.config=gs1gs3` | `setprop sys.usb.acm_idx 2,4` |
| `vendor/init/hw/factory_init.rc:251` | `fs` | `mkdir /config/usb_gadget/g1 0770 shell shell` |
| `vendor/init/hw/factory_init.rc:252` | `fs` | `write /config/usb_gadget/g1/idVendor 0x0E8D` |
| `vendor/init/hw/factory_init.rc:253` | `fs` | `write /config/usb_gadget/g1/bcdDevice 0xFFFF` |
| `vendor/init/hw/factory_init.rc:254` | `fs` | `write /config/usb_gadget/g1/bcdUSB 0x0200` |
| `vendor/init/hw/factory_init.rc:255` | `fs` | `mkdir /config/usb_gadget/g1/strings/0x409 0770` |
| `vendor/init/hw/factory_init.rc:256` | `fs` | `write /config/usb_gadget/g1/strings/0x409/manufacturer "Android"` |
| `vendor/init/hw/factory_init.rc:257` | `fs` | `write /config/usb_gadget/g1/strings/0x409/product "Android"` |
| `vendor/init/hw/factory_init.rc:258` | `fs` | `write /config/usb_gadget/g1/bDeviceClass 0` |
| `vendor/init/hw/factory_init.rc:259` | `fs` | `write /config/usb_gadget/g1/bDeviceSubClass 0` |
| `vendor/init/hw/factory_init.rc:260` | `fs` | `write /config/usb_gadget/g1/bDeviceProtocol 0` |
| `vendor/init/hw/factory_init.rc:261` | `fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/factory_init.rc:262` | `fs` | `mkdir /config/usb_gadget/g1/functions/ffs.adb` |
| `vendor/init/hw/factory_init.rc:263` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/factory_init.rc:264` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/factory_init.rc:265` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/factory_init.rc:266` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/factory_init.rc:267` | `fs` | `mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell` |
| `vendor/init/hw/factory_init.rc:268` | `fs` | `mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell` |
| `vendor/init/hw/factory_init.rc:269` | `fs` | `write /config/usb_gadget/g1/configs/b.1/MaxPower 500` |
| `vendor/init/hw/factory_init.rc:270` | `fs` | `symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1` |
| `vendor/init/hw/factory_init.rc:498` | `boot` | `setprop sys.usb.configfs 1` |
| `vendor/init/hw/factory_init.rc:499` | `boot` | `setprop sys.usb.ffs.aio_compat 1` |
| `vendor/init/hw/factory_init.rc:500` | `boot` | `setprop vendor.usb.clear boot` |
| `vendor/init/hw/factory_init.rc:746` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/factory_init.rc:747` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceClass 0` |
| `vendor/init/hw/factory_init.rc:748` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceSubClass 0` |
| `vendor/init/hw/factory_init.rc:749` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceProtocol 0` |
| `vendor/init/hw/factory_init.rc:750` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:751` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:752` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:753` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:754` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /sys/devices/platform/mt_usb/saving 0` |
| `vendor/init/hw/factory_init.rc:755` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 2` |
| `vendor/init/hw/factory_init.rc:759` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:762` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/factory_init.rc:763` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno` |
| `vendor/init/hw/factory_init.rc:764` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"` |
| `vendor/init/hw/factory_init.rc:765` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2006` |
| `vendor/init/hw/factory_init.rc:766` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:767` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:768` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:769` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "musb-hdrc"` |
| `vendor/init/hw/factory_init.rc:773` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/factory_init.rc:774` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "acm"` |
| `vendor/init/hw/factory_init.rc:775` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2007` |
| `vendor/init/hw/factory_init.rc:776` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceClass 02` |
| `vendor/init/hw/factory_init.rc:777` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:778` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "musb-hdrc"` |
| `vendor/init/hw/factory_init.rc:782` | `property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:783` | `property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 1` |
| `vendor/init/hw/factory_init.rc:787` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs3_dual_acm"` |
| `vendor/init/hw/factory_init.rc:788` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `write /config/usb_gadget/g1/idProduct 0x202E` |
| `vendor/init/hw/factory_init.rc:789` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/factory_init.rc:790` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:791` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:792` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:793` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:794` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `vendor/init/hw/factory_init.rc:795` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/factory_init.rc:796` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \\` | `setprop vendor.usb.clear 0` |
| `vendor/init/hw/factory_init.rc:799` | `property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:800` | `property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 1` |
| `vendor/init/hw/factory_init.rc:804` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs1gs3_dual_acm"` |
| `vendor/init/hw/factory_init.rc:805` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `write /config/usb_gadget/g1/idProduct 0x202F` |
| `vendor/init/hw/factory_init.rc:806` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/factory_init.rc:807` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:808` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:809` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:810` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:811` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `vendor/init/hw/factory_init.rc:812` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/factory_init.rc:813` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \\` | `setprop vendor.usb.clear 0` |
| `vendor/init/hw/factory_init.rc:816` | `property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/factory_init.rc:817` | `property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 1` |
| `vendor/init/hw/factory_init.rc:821` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs1gs3"` |
| `vendor/init/hw/factory_init.rc:822` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `write /config/usb_gadget/g1/idProduct 0x2029` |
| `vendor/init/hw/factory_init.rc:823` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/factory_init.rc:824` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/factory_init.rc:825` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/factory_init.rc:826` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/factory_init.rc:827` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/factory_init.rc:828` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `write /config/usb_gadget/g1/UDC ${sys.usb.controller}` |
| `vendor/init/hw/factory_init.rc:829` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \\` | `setprop vendor.usb.clear 0` |
| `vendor/init/hw/init.mt6762.rc:610` | `post-fs-data` | `chmod 0664 /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6762.rc:611` | `post-fs-data` | `chown root system /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6762.rc:612` | `post-fs-data` | `chmod 0664 /sys/class/udc/musb-hdrc/device/cmode` |
| `vendor/init/hw/init.mt6762.rc:613` | `post-fs-data` | `chown root system /sys/class/udc/musb-hdrc/device/cmode` |
| `vendor/init/hw/init.mt6765.rc:610` | `post-fs-data` | `chmod 0664 /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6765.rc:611` | `post-fs-data` | `chown root system /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6765.rc:612` | `post-fs-data` | `chmod 0664 /sys/class/udc/musb-hdrc/device/cmode` |
| `vendor/init/hw/init.mt6765.rc:613` | `post-fs-data` | `chown root system /sys/class/udc/musb-hdrc/device/cmode` |
| `vendor/init/hw/init.mt6765.usb.rc:5` | `post-fs` | `setprop vendor.usb.vid "0x0E8D"` |
| `vendor/init/hw/init.mt6765.usb.rc:8` | `post-fs` | `mkdir /config/usb_gadget/g1 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:9` | `post-fs` | `write /config/usb_gadget/g1/idVendor ${vendor.usb.vid}` |
| `vendor/init/hw/init.mt6765.usb.rc:10` | `post-fs` | `write /config/usb_gadget/g1/bcdDevice 0x0223` |
| `vendor/init/hw/init.mt6765.usb.rc:11` | `post-fs` | `write /config/usb_gadget/g1/bcdUSB 0x0200` |
| `vendor/init/hw/init.mt6765.usb.rc:12` | `post-fs` | `mkdir /config/usb_gadget/g1/strings/0x409 0770` |
| `vendor/init/hw/init.mt6765.usb.rc:13` | `post-fs` | `write /config/usb_gadget/g1/strings/0x409/serialnumber ${ro.serialno}` |
| `vendor/init/hw/init.mt6765.usb.rc:14` | `post-fs` | `write /config/usb_gadget/g1/strings/0x409/manufacturer ${ro.product.manufacturer}` |
| `vendor/init/hw/init.mt6765.usb.rc:15` | `post-fs` | `write /config/usb_gadget/g1/strings/0x409/product ${ro.product.model}` |
| `vendor/init/hw/init.mt6765.usb.rc:16` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/accessory.gs2` |
| `vendor/init/hw/init.mt6765.usb.rc:17` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/audio_source.gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:18` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/ffs.adb` |
| `vendor/init/hw/init.mt6765.usb.rc:19` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/mtp.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:20` | `post-fs` | `write /config/usb_gadget/g1/functions/mtp.gs0/os_desc/interface.MTP/compatible_id "MTP"` |
| `vendor/init/hw/init.mt6765.usb.rc:21` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/ptp.gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:22` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:23` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/midi.gs5` |
| `vendor/init/hw/init.mt6765.usb.rc:24` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:25` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:26` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/init.mt6765.usb.rc:27` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:28` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/init.mt6765.usb.rc:29` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/hid.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:30` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/via_modem.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:31` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/via_ets.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:32` | `post-fs` | `mkdir /config/usb_gadget/g1/functions/via_atc.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:33` | `post-fs` | `mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:34` | `post-fs` | `mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:35` | `post-fs` | `write /config/usb_gadget/g1/os_desc/b_vendor_code 0x1` |
| `vendor/init/hw/init.mt6765.usb.rc:36` | `post-fs` | `write /config/usb_gadget/g1/os_desc/qw_sign "MSFT100"` |
| `vendor/init/hw/init.mt6765.usb.rc:37` | `post-fs` | `write /config/usb_gadget/g1/configs/b.1/MaxPower 500` |
| `vendor/init/hw/init.mt6765.usb.rc:38` | `post-fs` | `symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1` |
| `vendor/init/hw/init.mt6765.usb.rc:42` | `boot` | `setprop sys.usb.configfs 1` |
| `vendor/init/hw/init.mt6765.usb.rc:43` | `boot` | `setprop sys.usb.ffs.aio_compat 1` |
| `vendor/init/hw/init.mt6765.usb.rc:44` | `boot` | `setprop vendor.usb.controller "musb-hdrc"` |
| `vendor/init/hw/init.mt6765.usb.rc:45` | `boot` | `setprop vendor.usb.acm_cnt 0` |
| `vendor/init/hw/init.mt6765.usb.rc:46` | `boot` | `setprop vendor.usb.acm_port0 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:47` | `boot` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:48` | `boot` | `setprop vendor.usb.acm_enable 0` |
| `vendor/init/hw/init.mt6765.usb.rc:49` | `boot` | `write /sys/class/android_usb/android0/f_mtp/cpu_mask 0xF0` |
| `vendor/init/hw/init.mt6765.usb.rc:50` | `boot` | `chmod 0664 /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6765.usb.rc:51` | `boot` | `chown root system /sys/class/android_usb/android0/iSerial` |
| `vendor/init/hw/init.mt6765.usb.rc:73` | `charger` | `mkdir /config/usb_gadget/g1 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:74` | `charger` | `write /config/usb_gadget/g1/idVendor 0x0E8D` |
| `vendor/init/hw/init.mt6765.usb.rc:75` | `charger` | `write /config/usb_gadget/g1/bcdDevice 0x0223` |
| `vendor/init/hw/init.mt6765.usb.rc:76` | `charger` | `write /config/usb_gadget/g1/bcdUSB 0x0200` |
| `vendor/init/hw/init.mt6765.usb.rc:77` | `charger` | `mkdir /config/usb_gadget/g1/strings/0x409 0770` |
| `vendor/init/hw/init.mt6765.usb.rc:78` | `charger` | `write /config/usb_gadget/g1/strings/0x409/serialnumber ${ro.serialno}` |
| `vendor/init/hw/init.mt6765.usb.rc:79` | `charger` | `write /config/usb_gadget/g1/strings/0x409/manufacturer ${ro.product.manufacturer}` |
| `vendor/init/hw/init.mt6765.usb.rc:80` | `charger` | `write /config/usb_gadget/g1/strings/0x409/product ${ro.product.model}` |
| `vendor/init/hw/init.mt6765.usb.rc:81` | `charger` | `mkdir /config/usb_gadget/g1/functions/hid.gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:82` | `charger` | `mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:83` | `charger` | `mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell` |
| `vendor/init/hw/init.mt6765.usb.rc:84` | `charger` | `write /config/usb_gadget/g1/configs/b.1/MaxPower 500` |
| `vendor/init/hw/init.mt6765.usb.rc:86` | `charger` | `setprop sys.usb.configfs 1` |
| `vendor/init/hw/init.mt6765.usb.rc:87` | `charger` | `setprop vendor.usb.controller "musb-hdrc"` |
| `vendor/init/hw/init.mt6765.usb.rc:88` | `charger` | `setprop vendor.usb.acm_cnt 0` |
| `vendor/init/hw/init.mt6765.usb.rc:89` | `charger` | `setprop vendor.usb.acm_port0 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:90` | `charger` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:91` | `charger` | `setprop vendor.usb.acm_enable 0` |
| `vendor/init/hw/init.mt6765.usb.rc:92` | `charger` | `setprop sys.usb.config hid` |
| `vendor/init/hw/init.mt6765.usb.rc:96` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor ${vendor.usb.vid}` |
| `vendor/init/hw/init.mt6765.usb.rc:97` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:98` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f5` |
| `vendor/init/hw/init.mt6765.usb.rc:99` | `property:sys.usb.config=none && property:sys.usb.configfs=1` | `write /sys/devices/platform/mt_usb/saving 0` |
| `vendor/init/hw/init.mt6765.usb.rc:103` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "HID"` |
| `vendor/init/hw/init.mt6765.usb.rc:104` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x20FF` |
| `vendor/init/hw/init.mt6765.usb.rc:105` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/functions/hid.gs0/protocol 0` |
| `vendor/init/hw/init.mt6765.usb.rc:106` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/functions/hid.gs0/subclass 0` |
| `vendor/init/hw/init.mt6765.usb.rc:109` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/hid.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:110` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:111` | `property:sys.usb.config=hid && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:117` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x201C` |
| `vendor/init/hw/init.mt6765.usb.rc:120` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2006` |
| `vendor/init/hw/init.mt6765.usb.rc:121` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:124` | `property:sys.usb.config=adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2029` |
| `vendor/init/hw/init.mt6765.usb.rc:128` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:129` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:130` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:131` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:132` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:133` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:134` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:135` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:136` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:140` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:141` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:142` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:143` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:144` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:149` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2008` |
| `vendor/init/hw/init.mt6765.usb.rc:152` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2012` |
| `vendor/init/hw/init.mt6765.usb.rc:153` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:156` | `property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202A` |
| `vendor/init/hw/init.mt6765.usb.rc:160` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:161` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:162` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/os_desc/use 1` |
| `vendor/init/hw/init.mt6765.usb.rc:163` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:164` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:165` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:166` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:167` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:168` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:172` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"` |
| `vendor/init/hw/init.mt6765.usb.rc:173` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:174` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/os_desc/use 1` |
| `vendor/init/hw/init.mt6765.usb.rc:175` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:176` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:177` | `property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:182` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x201D` |
| `vendor/init/hw/init.mt6765.usb.rc:185` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x200A` |
| `vendor/init/hw/init.mt6765.usb.rc:186` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:189` | `property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2026` |
| `vendor/init/hw/init.mt6765.usb.rc:193` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:194` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:195` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/os_desc/use 1` |
| `vendor/init/hw/init.mt6765.usb.rc:196` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:197` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:198` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:199` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:200` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:201` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:202` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:206` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:207` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:208` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/os_desc/use 1` |
| `vendor/init/hw/init.mt6765.usb.rc:209` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:210` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:211` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:212` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:217` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x200B` |
| `vendor/init/hw/init.mt6765.usb.rc:220` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2013` |
| `vendor/init/hw/init.mt6765.usb.rc:221` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:224` | `property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202B` |
| `vendor/init/hw/init.mt6765.usb.rc:228` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:229` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:230` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:231` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:232` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:233` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:234` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:235` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:239` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp"` |
| `vendor/init/hw/init.mt6765.usb.rc:240` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:241` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:242` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:243` | `property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:248` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x200C` |
| `vendor/init/hw/init.mt6765.usb.rc:251` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x200D` |
| `vendor/init/hw/init.mt6765.usb.rc:252` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:255` | `property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2027` |
| `vendor/init/hw/init.mt6765.usb.rc:259` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:260` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:261` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:262` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:263` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:264` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:265` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:266` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:267` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:271` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:272` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:273` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:274` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:275` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:276` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:281` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2004` |
| `vendor/init/hw/init.mt6765.usb.rc:284` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2011` |
| `vendor/init/hw/init.mt6765.usb.rc:285` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:288` | `property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202C` |
| `vendor/init/hw/init.mt6765.usb.rc:292` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:293` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:294` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:295` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:296` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:297` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:298` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:299` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:300` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:304` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:305` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis"` |
| `vendor/init/hw/init.mt6765.usb.rc:306` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:307` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:308` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:309` | `property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:313` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2005` |
| `vendor/init/hw/init.mt6765.usb.rc:316` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2010` |
| `vendor/init/hw/init.mt6765.usb.rc:317` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:320` | `property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2028` |
| `vendor/init/hw/init.mt6765.usb.rc:324` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:325` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:326` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:327` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:328` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:329` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:330` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:331` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:332` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:333` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:337` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `mkdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:338` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:339` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:340` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:341` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:342` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:343` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:347` | `property:sys.usb.config=midi && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2046` |
| `vendor/init/hw/init.mt6765.usb.rc:350` | `property:sys.usb.config=midi,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2048` |
| `vendor/init/hw/init.mt6765.usb.rc:354` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:355` | `property:sys.usb.config=accessory && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d00` |
| `vendor/init/hw/init.mt6765.usb.rc:358` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:359` | `property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d01` |
| `vendor/init/hw/init.mt6765.usb.rc:363` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:364` | `property:sys.usb.config=audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d02` |
| `vendor/init/hw/init.mt6765.usb.rc:367` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:368` | `property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d03` |
| `vendor/init/hw/init.mt6765.usb.rc:372` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:373` | `property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d04` |
| `vendor/init/hw/init.mt6765.usb.rc:376` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idVendor 0x18d1` |
| `vendor/init/hw/init.mt6765.usb.rc:377` | `property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2d05` |
| `vendor/init/hw/init.mt6765.usb.rc:381` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2031` |
| `vendor/init/hw/init.mt6765.usb.rc:384` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2033` |
| `vendor/init/hw/init.mt6765.usb.rc:385` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:390` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "via_bypass"` |
| `vendor/init/hw/init.mt6765.usb.rc:391` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:392` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:393` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:394` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:395` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:396` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:397` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:398` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:403` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "via_bypass"` |
| `vendor/init/hw/init.mt6765.usb.rc:404` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:405` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:406` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:407` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:408` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:409` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:410` | `property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:414` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2032` |
| `vendor/init/hw/init.mt6765.usb.rc:417` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2034` |
| `vendor/init/hw/init.mt6765.usb.rc:418` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:421` | `property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2050` |
| `vendor/init/hw/init.mt6765.usb.rc:424` | `property:sys.usb.config=via_bypass,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:430` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:431` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:432` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:433` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:434` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:435` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:436` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:437` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f5` |
| `vendor/init/hw/init.mt6765.usb.rc:438` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:439` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:445` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm2"` |
| `vendor/init/hw/init.mt6765.usb.rc:446` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:447` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:448` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:449` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:450` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:451` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:452` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:453` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:458` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:459` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:460` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:461` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:462` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:463` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:464` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:465` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:466` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:471` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2002` |
| `vendor/init/hw/init.mt6765.usb.rc:474` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x200F` |
| `vendor/init/hw/init.mt6765.usb.rc:475` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:478` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202D` |
| `vendor/init/hw/init.mt6765.usb.rc:482` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:483` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:484` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:485` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:486` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:487` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:488` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:489` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:493` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums"` |
| `vendor/init/hw/init.mt6765.usb.rc:494` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:495` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:496` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:497` | `property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:501` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2003` |
| `vendor/init/hw/init.mt6765.usb.rc:504` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2006` |
| `vendor/init/hw/init.mt6765.usb.rc:505` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:508` | `property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2029` |
| `vendor/init/hw/init.mt6765.usb.rc:511` | `property:sys.usb.config=mass_storage,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:515` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:516` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:517` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:518` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:519` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:520` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:521` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:522` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:523` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:527` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:528` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:529` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:530` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:531` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:532` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:537` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2002` |
| `vendor/init/hw/init.mt6765.usb.rc:540` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x200F` |
| `vendor/init/hw/init.mt6765.usb.rc:541` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:544` | `property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x202D` |
| `vendor/init/hw/init.mt6765.usb.rc:548` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:549` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:550` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:551` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:552` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:553` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:554` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:555` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:556` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:557` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:561` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums"` |
| `vendor/init/hw/init.mt6765.usb.rc:562` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:563` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:564` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:565` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:566` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:567` | `property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:571` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=0 && \\` | `setprop vendor.usb.pid 0x2003` |
| `vendor/init/hw/init.mt6765.usb.rc:574` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.pid 0x2006` |
| `vendor/init/hw/init.mt6765.usb.rc:575` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:578` | `property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=2 && \\` | `setprop vendor.usb.pid 0x2029` |
| `vendor/init/hw/init.mt6765.usb.rc:581` | `property:sys.usb.config=bicr,adb && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:585` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb_acm"` |
| `vendor/init/hw/init.mt6765.usb.rc:586` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:587` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:588` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:589` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:590` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:591` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:592` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:593` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:594` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:595` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:599` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb"` |
| `vendor/init/hw/init.mt6765.usb.rc:600` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}` |
| `vendor/init/hw/init.mt6765.usb.rc:601` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1` |
| `vendor/init/hw/init.mt6765.usb.rc:602` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:603` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:604` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:605` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:606` | `property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \\` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:610` | `property:persist.vendor.radio.port_index=""` | `setprop vendor.usb.acm_idx ""` |
| `vendor/init/hw/init.mt6765.usb.rc:611` | `property:persist.vendor.radio.port_index=""` | `setprop vendor.usb.acm_cnt 0` |
| `vendor/init/hw/init.mt6765.usb.rc:612` | `property:persist.vendor.radio.port_index=""` | `setprop vendor.usb.acm_enable 0` |
| `vendor/init/hw/init.mt6765.usb.rc:613` | `property:persist.vendor.radio.port_index=""` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:614` | `property:persist.vendor.radio.port_index=""` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:617` | `property:persist.vendor.radio.port_index="1"` | `setprop sys.usb.config acm_gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:619` | `property:sys.usb.config=mass_storage,adb,acm` | `setprop sys.usb.config acm_gs0` |
| `vendor/init/hw/init.mt6765.usb.rc:621` | `property:sys.usb.config=acm_gs0` | `setprop vendor.usb.acm_port0 0` |
| `vendor/init/hw/init.mt6765.usb.rc:622` | `property:sys.usb.config=acm_gs0` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:623` | `property:sys.usb.config=acm_gs0` | `setprop vendor.usb.acm_cnt 1` |
| `vendor/init/hw/init.mt6765.usb.rc:624` | `property:sys.usb.config=acm_gs0` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:625` | `property:sys.usb.config=acm_gs0` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:626` | `property:sys.usb.config=acm_gs0` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:627` | `property:sys.usb.config=acm_gs0` | `setprop vendor.usb.acm_idx "1"` |
| `vendor/init/hw/init.mt6765.usb.rc:630` | `property:persist.vendor.radio.port_index="2"` | `setprop sys.usb.config acm_gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:632` | `property:sys.usb.config=acm_gs1` | `setprop vendor.usb.acm_port0 1` |
| `vendor/init/hw/init.mt6765.usb.rc:633` | `property:sys.usb.config=acm_gs1` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:634` | `property:sys.usb.config=acm_gs1` | `setprop vendor.usb.acm_cnt 1` |
| `vendor/init/hw/init.mt6765.usb.rc:635` | `property:sys.usb.config=acm_gs1` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:636` | `property:sys.usb.config=acm_gs1` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:637` | `property:sys.usb.config=acm_gs1` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:638` | `property:sys.usb.config=acm_gs1` | `setprop vendor.usb.acm_idx "2"` |
| `vendor/init/hw/init.mt6765.usb.rc:641` | `property:persist.vendor.radio.port_index="3"` | `setprop sys.usb.config acm_gs2` |
| `vendor/init/hw/init.mt6765.usb.rc:643` | `property:sys.usb.config=acm_third` | `setprop sys.usb.config acm_gs2` |
| `vendor/init/hw/init.mt6765.usb.rc:645` | `property:sys.usb.config=acm_gs2` | `setprop vendor.usb.acm_port0 2` |
| `vendor/init/hw/init.mt6765.usb.rc:646` | `property:sys.usb.config=acm_gs2` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:647` | `property:sys.usb.config=acm_gs2` | `setprop vendor.usb.acm_cnt 1` |
| `vendor/init/hw/init.mt6765.usb.rc:648` | `property:sys.usb.config=acm_gs2` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:649` | `property:sys.usb.config=acm_gs2` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:650` | `property:sys.usb.config=acm_gs2` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:651` | `property:sys.usb.config=acm_gs2` | `setprop vendor.usb.acm_idx "3"` |
| `vendor/init/hw/init.mt6765.usb.rc:654` | `property:persist.vendor.radio.port_index="4"` | `setprop sys.usb.config acm_gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:656` | `property:sys.usb.config=gs3` | `setprop sys.usb.config acm_gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:658` | `property:sys.usb.config=mtp,gs3` | `setprop sys.usb.config acm_gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:660` | `property:sys.usb.config=acm_gs3` | `setprop vendor.usb.acm_port0 3` |
| `vendor/init/hw/init.mt6765.usb.rc:661` | `property:sys.usb.config=acm_gs3` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:662` | `property:sys.usb.config=acm_gs3` | `setprop vendor.usb.acm_cnt 1` |
| `vendor/init/hw/init.mt6765.usb.rc:663` | `property:sys.usb.config=acm_gs3` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:664` | `property:sys.usb.config=acm_gs3` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:665` | `property:sys.usb.config=acm_gs3` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:666` | `property:sys.usb.config=acm_gs3` | `setprop vendor.usb.acm_idx "4"` |
| `vendor/init/hw/init.mt6765.usb.rc:669` | `property:sys.usb.config=mtp,gs0gs1` | `setprop sys.usb.config acm_gs0gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:671` | `property:sys.usb.config=gs0gs1` | `setprop sys.usb.config acm_gs0gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:673` | `property:sys.usb.config=rndis,gs0gs1` | `setprop sys.usb.config acm_gs0gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:675` | `property:sys.usb.config=rndis,adb,gs0gs1` | `setprop sys.usb.config acm_gs0gs1` |
| `vendor/init/hw/init.mt6765.usb.rc:677` | `property:sys.usb.config=acm_gs0gs1` | `setprop vendor.usb.acm_port0 0` |
| `vendor/init/hw/init.mt6765.usb.rc:678` | `property:sys.usb.config=acm_gs0gs1` | `setprop vendor.usb.acm_port1 1` |
| `vendor/init/hw/init.mt6765.usb.rc:679` | `property:sys.usb.config=acm_gs0gs1` | `setprop vendor.usb.acm_cnt 2` |
| `vendor/init/hw/init.mt6765.usb.rc:680` | `property:sys.usb.config=acm_gs0gs1` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:681` | `property:sys.usb.config=acm_gs0gs1` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:682` | `property:sys.usb.config=acm_gs0gs1` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:683` | `property:sys.usb.config=acm_gs0gs1` | `setprop vendor.usb.acm_idx "1,2"` |
| `vendor/init/hw/init.mt6765.usb.rc:686` | `property:sys.usb.config=gs3,dual_acm` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:688` | `property:sys.usb.config=mtp,gs3,dual_acm` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:690` | `property:sys.usb.config=mtp,gs0gs3` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:692` | `property:sys.usb.config=gs0gs3` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:694` | `property:sys.usb.config=rndis,gs0gs3` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:696` | `property:sys.usb.config=rndis,adb,gs0gs3` | `setprop sys.usb.config acm_gs0gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:698` | `property:sys.usb.config=acm_gs0gs3` | `setprop vendor.usb.acm_port0 0` |
| `vendor/init/hw/init.mt6765.usb.rc:699` | `property:sys.usb.config=acm_gs0gs3` | `setprop vendor.usb.acm_port1 3` |
| `vendor/init/hw/init.mt6765.usb.rc:700` | `property:sys.usb.config=acm_gs0gs3` | `setprop vendor.usb.acm_cnt 2` |
| `vendor/init/hw/init.mt6765.usb.rc:701` | `property:sys.usb.config=acm_gs0gs3` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:702` | `property:sys.usb.config=acm_gs0gs3` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:703` | `property:sys.usb.config=acm_gs0gs3` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:704` | `property:sys.usb.config=acm_gs0gs3` | `setprop vendor.usb.acm_idx "1,4"` |
| `vendor/init/hw/init.mt6765.usb.rc:707` | `property:sys.usb.config=gs1gs3` | `setprop sys.usb.config acm_gs1gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:709` | `property:sys.usb.config=mtp,gs1gs3` | `setprop sys.usb.config acm_gs1gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:711` | `property:sys.usb.config=via_bypass,gs1gs3` | `setprop sys.usb.config acm_gs1gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:713` | `property:sys.usb.config=rndis,gs1gs3` | `setprop sys.usb.config acm_gs1gs3` |
| `vendor/init/hw/init.mt6765.usb.rc:715` | `property:sys.usb.config=acm_gs1gs3` | `setprop vendor.usb.acm_port0 1` |
| `vendor/init/hw/init.mt6765.usb.rc:716` | `property:sys.usb.config=acm_gs1gs3` | `setprop vendor.usb.acm_port1 3` |
| `vendor/init/hw/init.mt6765.usb.rc:717` | `property:sys.usb.config=acm_gs1gs3` | `setprop vendor.usb.acm_cnt 2` |
| `vendor/init/hw/init.mt6765.usb.rc:718` | `property:sys.usb.config=acm_gs1gs3` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:719` | `property:sys.usb.config=acm_gs1gs3` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:720` | `property:sys.usb.config=acm_gs1gs3` | `setprop sys.usb.config ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:721` | `property:sys.usb.config=acm_gs1gs3` | `setprop vendor.usb.acm_idx "2,4"` |
| `vendor/init/hw/init.mt6765.usb.rc:725` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/init.mt6765.usb.rc:726` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceClass 0` |
| `vendor/init/hw/init.mt6765.usb.rc:727` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceSubClass 0` |
| `vendor/init/hw/init.mt6765.usb.rc:728` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/bDeviceProtocol 0` |
| `vendor/init/hw/init.mt6765.usb.rc:729` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:730` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:731` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:732` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:733` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rm /config/usb_gadget/g1/configs/b.1/f5` |
| `vendor/init/hw/init.mt6765.usb.rc:734` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `rmdir /config/usb_gadget/g1/functions/rndis.gs4` |
| `vendor/init/hw/init.mt6765.usb.rc:735` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `write /sys/devices/platform/mt_usb/saving 0` |
| `vendor/init/hw/init.mt6765.usb.rc:736` | `property:vendor.usb.clear=1 && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 2` |
| `vendor/init/hw/init.mt6765.usb.rc:740` | `property:sys.usb.ffs.ready=0` | `setprop vendor.usb.ffs.ready 0` |
| `vendor/init/hw/init.mt6765.usb.rc:742` | `property:sys.usb.ffs.ready=1` | `setprop vendor.usb.ffs.ready 1` |
| `vendor/init/hw/init.mt6765.usb.rc:745` | `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | `stop adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:746` | `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | `setprop vendor.usb.ffs.ready 0` |
| `vendor/init/hw/init.mt6765.usb.rc:747` | `property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 1` |
| `vendor/init/hw/init.mt6765.usb.rc:751` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0 && \\` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:755` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.acm_port0 0` |
| `vendor/init/hw/init.mt6765.usb.rc:756` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.acm_port1 ""` |
| `vendor/init/hw/init.mt6765.usb.rc:757` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.acm_cnt 1` |
| `vendor/init/hw/init.mt6765.usb.rc:758` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:759` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `write /sys/class/udc/musb-hdrc/device/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:760` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "atm_gs0"` |
| `vendor/init/hw/init.mt6765.usb.rc:761` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `write /config/usb_gadget/g1/idProduct 0x200A` |
| `vendor/init/hw/init.mt6765.usb.rc:762` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:763` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:764` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:765` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:768` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.clear 0` |
| `vendor/init/hw/init.mt6765.usb.rc:769` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \\` | `setprop vendor.usb.acm_idx "1"` |
| `vendor/init/hw/init.mt6765.usb.rc:772` | `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | `stop adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:773` | `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | `setprop vendor.usb.ffs.ready 0` |
| `vendor/init/hw/init.mt6765.usb.rc:774` | `property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1` | `setprop vendor.usb.clear 1` |
| `vendor/init/hw/init.mt6765.usb.rc:778` | `property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0gs3 && \\` | `start adbd` |
| `vendor/init/hw/init.mt6765.usb.rc:782` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.acm_port0 0` |
| `vendor/init/hw/init.mt6765.usb.rc:783` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.acm_port1 3` |
| `vendor/init/hw/init.mt6765.usb.rc:784` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.acm_cnt 2` |
| `vendor/init/hw/init.mt6765.usb.rc:785` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.acm_enable 1` |
| `vendor/init/hw/init.mt6765.usb.rc:786` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `write /sys/class/udc/musb-hdrc/device/saving 1` |
| `vendor/init/hw/init.mt6765.usb.rc:787` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `write /config/usb_gadget/g1/idProduct 0x2026` |
| `vendor/init/hw/init.mt6765.usb.rc:788` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:789` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/init.mt6765.usb.rc:790` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/init.mt6765.usb.rc:791` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/init.mt6765.usb.rc:792` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:795` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.clear 0` |
| `vendor/init/hw/init.mt6765.usb.rc:796` | `property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \\` | `setprop vendor.usb.acm_idx "1,4"` |
| `vendor/init/hw/init.mt6765.usb.rc:800` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"` |
| `vendor/init/hw/init.mt6765.usb.rc:801` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2008` |
| `vendor/init/hw/init.mt6765.usb.rc:802` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/os_desc/use 1` |
| `vendor/init/hw/init.mt6765.usb.rc:803` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/init.mt6765.usb.rc:804` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC ${vendor.usb.controller}` |
| `vendor/init/hw/init.mt6765.usb.rc:805` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `setprop sys.usb.state ${sys.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:806` | `property:sys.usb.config=cdp && property:sys.usb.configfs=1` | `setprop vendor.usb.pid 0x2008` |
| `vendor/init/hw/init.mt6765.usb.rc:809` | `property:sys.usb.config=charging_yes` | `setprop vendor.usb.state ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:810` | `property:sys.usb.config=charging_yes` | `setprop vendor.usb.charging yes` |
| `vendor/init/hw/init.mt6765.usb.rc:811` | `property:sys.usb.config=charging_yes` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:812` | `property:sys.usb.config=charging_yes` | `setprop sys.usb.config ${vendor.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:815` | `property:sys.usb.config=charging_no` | `setprop vendor.usb.state ${sys.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:816` | `property:sys.usb.config=charging_no` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:817` | `property:sys.usb.config=charging_no` | `setprop sys.usb.config ${vendor.usb.state}` |
| `vendor/init/hw/init.mt6765.usb.rc:818` | `property:sys.usb.config=charging_no` | `setprop vendor.usb.charging no` |
| `vendor/init/hw/init.mt6765.usb.rc:821` | `property:vendor.usb.charging=yes` | `write /sys/class/udc/musb-hdrc/device/cmode 2` |
| `vendor/init/hw/init.mt6765.usb.rc:823` | `property:vendor.usb.charging=no` | `write /sys/class/udc/musb-hdrc/device/cmode 1` |
| `vendor/init/hw/init.mt6765.usb.rc:826` | `property:vendor.usb.bicr=yes` | `write /sys/class/android_usb/android0/f_mass_storage/bicr 1` |
| `vendor/init/hw/init.mt6765.usb.rc:827` | `property:vendor.usb.bicr=yes` | `write /sys/class/android_usb/android0/f_mass_storage/lun/file "/dev/block/loop0"` |
| `vendor/init/hw/init.mt6765.usb.rc:829` | `property:vendor.usb.bicr=no` | `write /sys/class/android_usb/android0/f_mass_storage/bicr 0` |
| `vendor/init/hw/init.mt6765.usb.rc:830` | `property:vendor.usb.bicr=no` | `write /sys/class/android_usb/android0/f_mass_storage/lun/file "off"` |
| `vendor/init/hw/init.mt6765.usb.rc:833` | `property:vendor.usb.port.mode=usb` | `write /sys/devices/platform/mt_usb/portmode 0` |
| `vendor/init/hw/init.mt6765.usb.rc:835` | `property:vendor.usb.port.mode=uart` | `write /sys/devices/platform/mt_usb/portmode 1` |
| `vendor/init/hw/init.mt6765.usb.rc:873` | `property:vendor.usb.config=*` | `setprop sys.usb.config ${vendor.usb.config}` |
| `vendor/init/hw/init.mt6765.usb.rc:878` | `property:vendor.usb.test=*` | `setprop sys.usb.config none` |
| `vendor/init/hw/init.mt6765.usb.rc:879` | `property:vendor.usb.test=*` | `setprop sys.usb.config ${vendor.usb.test}` |
| `vendor/init/hw/init.mt6765.usb.rc:883` | `property:vendor.usb.printk=*` | `write /proc/sys/kernel/printk ${vendor.usb.printk}` |
| `vendor/init/hw/init.mt6765.usb.rc:886` | `property:persist.vendor.usb.printk=*` | `setprop vendor.usb.printk ${persist.vendor.usb.printk}` |
| `vendor/init/hw/meta_init.rc:283` | `fs` | `mkdir /config/usb_gadget/g1 0770 shell shell` |
| `vendor/init/hw/meta_init.rc:284` | `fs` | `write /config/usb_gadget/g1/idVendor 0x0E8D` |
| `vendor/init/hw/meta_init.rc:285` | `fs` | `write /config/usb_gadget/g1/bcdDevice 0xFFFF` |
| `vendor/init/hw/meta_init.rc:286` | `fs` | `write /config/usb_gadget/g1/bcdUSB 0x0200` |
| `vendor/init/hw/meta_init.rc:287` | `fs` | `mkdir /config/usb_gadget/g1/strings/0x409 0770` |
| `vendor/init/hw/meta_init.rc:288` | `fs` | `write /config/usb_gadget/g1/strings/0x409/manufacturer "Android"` |
| `vendor/init/hw/meta_init.rc:289` | `fs` | `write /config/usb_gadget/g1/strings/0x409/product "Android"` |
| `vendor/init/hw/meta_init.rc:290` | `fs` | `write /config/usb_gadget/g1/bDeviceClass 0` |
| `vendor/init/hw/meta_init.rc:291` | `fs` | `write /config/usb_gadget/g1/bDeviceSubClass 0` |
| `vendor/init/hw/meta_init.rc:292` | `fs` | `write /config/usb_gadget/g1/bDeviceProtocol 0` |
| `vendor/init/hw/meta_init.rc:293` | `fs` | `mkdir /config/usb_gadget/g1/functions/mass_storage.usb0` |
| `vendor/init/hw/meta_init.rc:294` | `fs` | `mkdir /config/usb_gadget/g1/functions/ffs.adb` |
| `vendor/init/hw/meta_init.rc:295` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs0` |
| `vendor/init/hw/meta_init.rc:296` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs1` |
| `vendor/init/hw/meta_init.rc:297` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs2` |
| `vendor/init/hw/meta_init.rc:298` | `fs` | `mkdir /config/usb_gadget/g1/functions/acm.gs3` |
| `vendor/init/hw/meta_init.rc:299` | `fs` | `mkdir /config/usb_gadget/g1/functions/gser.gs0` |
| `vendor/init/hw/meta_init.rc:300` | `fs` | `mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell` |
| `vendor/init/hw/meta_init.rc:301` | `fs` | `mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell` |
| `vendor/init/hw/meta_init.rc:302` | `fs` | `write /config/usb_gadget/g1/configs/b.1/MaxPower 500` |
| `vendor/init/hw/meta_init.rc:303` | `fs` | `symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1` |
| `vendor/init/hw/meta_init.rc:304` | `fs` | `setprop vendor.usb.feature 0` |
| `vendor/init/hw/meta_init.rc:466` | `boot` | `setprop sys.usb.configfs 1` |
| `vendor/init/hw/meta_init.rc:467` | `boot` | `setprop sys.usb.ffs.aio_compat 1` |
| `vendor/init/hw/meta_init.rc:603` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:start adbd"` |
| `vendor/init/hw/meta_init.rc:604` | `property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/meta_init.rc:608` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /proc/bootprof "INIT:on property:ro.boot.usbconfig=0"` |
| `vendor/init/hw/meta_init.rc:609` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/meta_init.rc:610` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno` |
| `vendor/init/hw/meta_init.rc:611` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"` |
| `vendor/init/hw/meta_init.rc:612` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /config/usb_gadget/g1/idProduct 0x200E` |
| `vendor/init/hw/meta_init.rc:613` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/meta_init.rc:614` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/meta_init.rc:615` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \\` | `write /config/usb_gadget/g1/UDC "musb-hdrc"` |
| `vendor/init/hw/meta_init.rc:619` | `property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 && property:vendor.usb.feature=0` | `write /proc/bootprof "INIT:on property:ro.boot.usbconfig 1"` |
| `vendor/init/hw/meta_init.rc:630` | `property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:on property:ro.boot.usbconfig 2"` |
| `vendor/init/hw/meta_init.rc:631` | `property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1` | `setprop vendor.usb.acm_idx 1,4` |
| `vendor/init/hw/meta_init.rc:643` | `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:start adbd"` |
| `vendor/init/hw/meta_init.rc:644` | `property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `start adbd` |
| `vendor/init/hw/meta_init.rc:647` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /proc/bootprof "INIT:on property:ro.boot.usbconfig 3"` |
| `vendor/init/hw/meta_init.rc:648` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "none"` |
| `vendor/init/hw/meta_init.rc:649` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno` |
| `vendor/init/hw/meta_init.rc:650` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "meta_elt_adb"` |
| `vendor/init/hw/meta_init.rc:651` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/idProduct 0x2040` |
| `vendor/init/hw/meta_init.rc:652` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /sys/devices/platform/mt_usb/saving 1` |
| `vendor/init/hw/meta_init.rc:653` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1` |
| `vendor/init/hw/meta_init.rc:654` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2` |
| `vendor/init/hw/meta_init.rc:655` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3` |
| `vendor/init/hw/meta_init.rc:656` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4` |
| `vendor/init/hw/meta_init.rc:657` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `write /config/usb_gadget/g1/UDC "musb-hdrc"` |
| `vendor/init/hw/meta_init.rc:658` | `property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1` | `setprop vendor.usb.acm_idx 1,4` |

Runtime USB identity:
- vendor.usb.controller       : musb-hdrc
- vendor.usb.vid              : 0x0E8D
- vendor.usb.pid              : 0x201D
- vendor.usb.ffs.ready        : 1
- vendor.usb.acm_enable       : 0
- vendor.usb.acm_cnt          : 0
- vendor.usb.acm_port0        : 
- vendor.usb.acm_port1        : 

################################################################################
# 1.20 — BSP GHOSTS, DEAD IMPORTS, AND DECLARED-ABSENT ROUTES
################################################################################

| Item | Evidence | Status | ROM action |
|---|---|---|---|
| `/FWUpgradeInit.rc` | Imported by `init.mt6762.rc` and `init.mt6765.rc`; absent from source pack. | [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK] [OPEN_LEAD] | Do not carry unless ramdisk artifact is proven required. |
| `/system/etc/init/hw/init.aee.rc` | Imported by platform/factory/meta files; absent from source pack. | [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK] | Remove or replace with valid crash/AEE policy after Section 14. |
| `${ro.vendor.rc}init.volte.rc` | Imported by platform files; absent from source pack. Actual VoLTE services exist as flat RC files. | [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK] [CORRECT] | Use actual flat VoLTE RC service graph. |
| `${ro.vendor.rc}init.mal.rc` | Imported by platform files; absent from source pack. | [DECLARED_IMPORT] [ABSENT_IN_SOURCE_PACK] [BSP_GHOST] | Strip unless later binary dependency appears. |
| `/vendor/etc/iAmCdRom.iso` | Platform files mount it as iso9660 loop at `/mnt/cd-rom`; payload absent from live source pack. | [DECLARED] [ABSENT_IN_SOURCE_PACK] [OPEN_LEAD] | Do not carry; document as factory/BSP ghost unless payload is recovered. |
| `vendor.mediatek.hardware.nvram@1.1-sevice.rc` | Filename contains `sevice` typo while service binary is `vendor.mediatek.hardware.nvram@1.1-service`. | [VERIFIED] [ODM_TYPOSURFACE] | Preserve exact filename if carrying stock vendor service. |
| A/B preloader symlinks | Platform files symlink `preloader_a` and `preloader_b` from boot0/boot1. Device partition model is A-only. | [DECLARED] [BSP_GHOST] | Strip or neutralize in clean device tree after Section 00/17. |
| Legacy/absent hardware nodes | Sensor/HDMI/MATV/Goodix/Mali and other generic MTK nodes are permissioned by platform RC. | [DECLARED] [BSP_GHOST_CANDIDATE] | Keep only if live node/HAL dependency is proven. |

################################################################################
# 1.21 — RUNTIME PROCESS AND BINDER SERVICE SUMMARY
################################################################################

Runtime process entries captured: 494
Binder/framework service list count: 180

Critical runtime processes:
| Label | User | PID | PPID | Name | Args |
|---|---|---:|---:|---|---|
| `u:r:init:s0` | `root` | 1 | 0 | `init` | `init second_stage` |
| `u:r:vendor_init:s0` | `root` | 342 | 1 | `init` | `init subcontext u:r:vendor_init:s0 12` |
| `u:r:vendor_init:s0` | `root` | 343 | 1 | `init` | `init subcontext u:r:vendor_init:s0 13` |
| `u:r:servicemanager:s0` | `system` | 360 | 1 | `servicemanager` | `servicemanager` |
| `u:r:hwservicemanager:s0` | `system` | 361 | 1 | `hwservicemanager` | `hwservicemanager` |
| `u:r:vndservicemanager:s0` | `system` | 362 | 1 | `vndservicemanager` | `vndservicemanager /dev/vndbinder` |
| `u:r:vold:s0` | `root` | 372 | 1 | `vold` | `vold --blkid_context=u:r:blkid:s0 --blkid_untrusted_context=u:r:blkid_untrusted:s0 --fsck_context=u:r:fsck:s0 --fsck_untrusted_context=u:r:fsck_untrusted:s0` |
| `u:r:mtk_wmt_launcher:s0` | `system` | 415 | 1 | `wmt_launcher` | `wmt_launcher -p /vendor/firmware/ -o 1` |
| `u:r:fuelgauged:s0` | `system` | 552 | 1 | `fuelgauged` | `fuelgauged` |
| `u:r:ccci_fsd:s0` | `radio` | 553 | 1 | `ccci_fsd` | `ccci_fsd 0` |
| `u:r:ccci_fsd:s0` | `radio` | 556 | 1 | `ccci_fsd` | `ccci_fsd 2` |
| `u:r:ccci_mdinit:s0` | `system` | 558 | 1 | `ccci_mdinit` | `ccci_mdinit 0` |
| `u:r:ccci_rpcd:s0` | `radio` | 563 | 1 | `ccci_rpcd` | `ccci_rpcd 0` |
| `u:r:wfca:s0` | `root` | 565 | 1 | `wfca` | `wfca` |
| `u:r:mnld:s0` | `gps` | 638 | 1 | `mnld` | `mnld` |
| `u:r:lbs_dbg:s0` | `system` | 639 | 1 | `lbs_dbg` | `lbs_dbg` |
| `u:r:mtk_hal_sensors:s0` | `system` | 694 | 1 | `android.hardware.sensors@2.0-service-mediatek` | `android.hardware.sensors@2.0-service-mediatek` |
| `u:r:mtk_hal_camera:s0` | `cameraserver` | 696 | 1 | `camerahalserver` | `camerahalserver` |
| `u:r:ipsec_mon:s0` | `system` | 702 | 1 | `ipsec_mon` | `ipsec_mon` |
| `u:r:mtk_agpsd:s0` | `gps` | 703 | 1 | `mtk_agpsd` | `mtk_agpsd` |
| `u:r:netdagent:s0` | `system` | 721 | 1 | `netdagent` | `netdagent` |
| `u:r:gsm0710muxd:s0` | `radio` | 748 | 1 | `gsm0710muxd` | `gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic` |
| `u:r:bip:s0` | `root` | 816 | 1 | `bip` | `bip` |
| `u:r:volte_imsm_93:s0` | `root` | 820 | 1 | `volte_imsm_93` | `volte_imsm_93` |
| `u:r:volte_stack:s0` | `root` | 823 | 1 | `volte_stack` | `volte_stack` |
| `u:r:volte_ua:s0` | `root` | 827 | 1 | `volte_ua` | `volte_ua` |
| `u:r:volte_imcb:s0` | `system` | 835 | 1 | `volte_imcb` | `volte_imcb` |
| `u:r:epdg_wod:s0` | `root` | 836 | 1 | `epdg_wod` | `epdg_wod` |
| `u:r:rild:s0` | `radio` | 982 | 1 | `mtkfusionrild` | `mtkfusionrild -l libmtk-ril.so` |
| `u:r:system_server:s0` | `system` | 1075 | 512 | `system_server` | `system_server` |
| `u:r:mtk_hal_wifi:s0` | `wifi` | 1229 | 1 | `android.hardware.wifi@1.0-service-lazy-mediatek` | `android.hardware.wifi@1.0-service-lazy-mediatek` |
| `u:r:system_app:s0` | `system` | 1262 | 512 | `com.wiite.WiiteCommon` | `com.wiite.WiiteCommon` |
| `u:r:system_app:s0` | `system` | 1881 | 512 | `com.wiite.devicedaemon` | `com.wiite.devicedaemon` |
| `u:r:platform_app:s0:c512,c768` | `u0_a41` | 1906 | 512 | `com.adups.fota` | `com.adups.fota` |
| `u:r:system_app:s0` | `system` | 1929 | 512 | `com.wiite.cleantask` | `com.wiite.cleantask` |
| `u:r:system_app:s0` | `system` | 2144 | 512 | `com.wiite.devicedaemon:communication` | `com.wiite.devicedaemon:communication` |
| `u:r:system_app:s0` | `system` | 2519 | 512 | `com.wiite.wearhealthuart` | `com.wiite.wearhealthuart` |
| `u:r:system_app:s0` | `system` | 2532 | 512 | `com.wiite.wiite_heartrate` | `com.wiite.wiite_heartrate` |
| `u:r:system_app:s0` | `system` | 2570 | 512 | `com.wiite.wiite_sleep` | `com.wiite.wiite_sleep` |
| `u:r:system_app:s0` | `system` | 2584 | 512 | `com.wiite.wiite_bloodoxygen` | `com.wiite.wiite_bloodoxygen` |
| `u:r:system_app:s0` | `system` | 3850 | 512 | `com.wiite.power` | `com.wiite.power` |
| `u:r:system_app:s0` | `system` | 3881 | 512 | `com.wiite.wiitestore` | `com.wiite.wiitestore` |

################################################################################
# 1.22 — CLEAN-ROM INIT ACTION MATRIX
################################################################################

| Category | Items | Action | Reason |
|---|---|---|---|
| Core Android boot | root init, zygote, servicemanager, hwservicemanager, vndservicemanager, vold, netd, surfaceflinger | CARRY / ADAPT | Required for Android boot. |
| Keymaster/Gatekeeper/Secure Element/DRM | keymaster early_hal, gatekeeper, secure element, Widevine/Clearkey DRM | CARRY FIRST, HARDEN LATER | Required for FBE/security/media capability. |
| Vendor hardware HALs | audio, camera, sensors, GNSS, Bluetooth, Wi-Fi, USB, graphics, health, light, vibrator, power, thermal | CARRY FIRST | Required for hardware bring-up; reduce after boot validation. |
| Radio/IMS/WFC/ePDG | CCCI, RIL, VoLTE, WFC, ePDG, IPsec, BIP, GSM mux | CARRY FIRST, AUDIT | Required for phone/LTE/IMS; security-sensitive. |
| Wiite/nRF control path | `wiite_corp_ctrl` setters, `/dev/ttyS1`, UID-1000 Wiite process ownership | REPLACE / HARDEN | Required functionality must move to controlled replacement daemon; remove direct UID-1000 app access. |
| Factory/meta profiles | `factory_init*`, `meta_init*`, loggers, factory adbd/console/meta_tst | STRIP_OR_ISOLATE | Production user ROM should not expose manufacturing/debug profiles by default. |
| Dead imports | `/FWUpgradeInit.rc`, missing AEE, missing init.volte.rc/init.mal.rc | STRIP_OR_RESOLVE | Do not carry absent references unless recovered and justified. |
| CD-ROM/mass-storage factory routes | `iAmCdRom.iso`, mass_storage/ACM/adbd gadget actions | STRIP_OR_DISABLE | Factory/debug/USB attack surface; preserve only known-safe USB profile. |
| Generic BSP hardware ghosts | Mali, Goodix, MATV, HDMI/TV-out, absent sensors, legacy nodes | STRIP_AFTER_DEP_CHECK | Keep only if live node/HAL dependency is proven. |

################################################################################
# 1.23 — ORIGINAL DATABASE DELTA LEDGER
################################################################################

| Original Section 01 claim | Status | Resolution |
|---|---|---|
| Vendor RC inventory is 85 total, 68 flat + 17 hw. | [KEEP] | Source pack confirms exact vendor counts. |
| `init.wlan_drv.rc` is correct; `init.vlan_drv.rc` was a typo. | [KEEP] | Source pack contains `init.wlan_drv.rc` and no `init.vlan_drv.rc`. |
| `init.mt6762.rc` and `init.mt6765.rc` are identical. | [EXPAND] | Source pack confirms byte-for-byte identity with matching SHA256 and line count. |
| `${ro.vendor.rc}` resolves to vendor hw init path. | [KEEP] | Runtime property `ro.vendor.rc=/vendor/etc/init/hw/`. |
| `/FWUpgradeInit.rc` is imported. | [EXPAND] | Import exists; source pack lacks target. Classified declared-absent open lead. |
| `init.modem.rc` must remain last platform import. | [KEEP] | Platform import order confirms `init.modem.rc` last. |
| `iAmCdRom.iso` is mounted at `/mnt/cd-rom`. | [CORRECT] | Mount command exists; payload absent from source pack. Classified declared-absent open lead, not active payload. |
| Wiite/nRF control nodes are permissioned by init. | [EXPAND] | Exact setter lines found in both platform RC files and live runtime state matches. |
| `/dev/ttyS1` is made writable and assigned to system. | [EXPAND] | Exact setter lines found; live runtime shows `crw-rw-rw- system system`; Wiite communication process holds FD. |
| Factory/meta profiles exist. | [EXPAND] | Source pack confirms factory/meta RC files, services, triggers, and diagnostic surfaces. |
| Radio/VoLTE/WFC/ePDG graph is active. | [EXPAND] | Source and runtime confirm running RIL/CCCI/VoLTE/WFC/ePDG/IPsec services. |
| Missing/ghost MTK declarations exist. | [EXPAND] | Multiple absent imports and generic BSP permissioned nodes retained for Section 17. |

################################################################################
# 1.24 — CLOSURE VERDICT
################################################################################

Section 01 is closed for the init-layer software digital twin with the current source pack
and runtime snapshot. The following facts are absolute within the captured evidence boundary:

- Vendor init inventory is 85 RC files: 68 flat and 17 `hw/`.
- Product init contribution is zero in this capture.
- Root init/ueventd source pack files are present and hashed.
- System init inventory is 62 recursive RC files in this source pack.
- `init.mt6762.rc` and `init.mt6765.rc` are byte-for-byte identical.
- The MT6762 runtime / MT6765 BSP split is real and preserved.
- The platform import chain contains declared-absent targets.
- `init.modem.rc` is the last platform import and sets `vold.post_fs_data_done=1`.
- Wiite/nRF sysfs and `/dev/ttyS1` permissions are set by both platform RC files.
- `/dev/ttyS1` is live, system-owned, world-readable/writable, and opened by
  `com.wiite.devicedaemon:communication`.
- Factory/meta diagnostic profiles are shipped in the vendor init tree.
- Radio/IMS/WFC/ePDG/RIL/CCCI services are declared and active in runtime.
- Clean ROM work must replace the Wiite/nRF bridge with a controlled owner, strip or isolate
  factory/meta/debug USB profiles, and preserve required HAL/radio services until dependency
  reduction is proven.

Remaining work is moved, not missing:
- Section 13: Wiite/nRF protocol, APK/framework ownership, replacement daemon boundaries.
- Section 14: SELinux domains, file contexts, allow/neverallow cleanup.
- Section 16: security/threat analysis of UID-1000 packages, OTA/update flows, exfiltration
  candidates, and privileged receivers.
- Section 17: BSP ghosts, negative software register, and removable dead imports.
- Section 18: final ROM build gate and verification checklist.

################################################################################
# APPENDIX A — COMPLETE ACTION COMMAND LEDGER
################################################################################

Format: `file:line | trigger | command`

```text
root/init.environ.rc:3 | early-init | export ANDROID_BOOTLOGO 1
root/init.environ.rc:4 | early-init | export ANDROID_ROOT /system
root/init.environ.rc:5 | early-init | export ANDROID_ASSETS /system/app
root/init.environ.rc:6 | early-init | export ANDROID_DATA /data
root/init.environ.rc:7 | early-init | export ANDROID_STORAGE /storage
root/init.environ.rc:8 | early-init | export ANDROID_RUNTIME_ROOT /apex/com.android.runtime
root/init.environ.rc:9 | early-init | export ANDROID_TZDATA_ROOT /apex/com.android.tzdata
root/init.environ.rc:10 | early-init | export EXTERNAL_STORAGE /sdcard
root/init.environ.rc:11 | early-init | export ASEC_MOUNTPOINT /mnt/asec
root/init.environ.rc:12 | early-init | export BOOTCLASSPATH /apex/com.android.runtime/javalib/core-oj.jar:/apex/com.android.runtime/javalib/core-libart.jar:/apex/com.android.runtime/javalib/okhttp.jar:/apex/com.android.runtime/javalib/bouncycastle.jar:/apex/com.android.runtime/javalib/apache-xml.jar:/system/framework/framework.jar:/system/framework/ext.jar:/system/framework/telephony-common.jar:/system/framework/voip-common.jar:/system/framework/ims-common.jar:/system/framework/android.test.base.jar:/system/framework/mediatek-telephony-base.jar:/system/framework/mediatek-telephony-common.jar:/system/framework/mediatek-common.jar:/system/framework/mediatek-framework.jar:/system/framework/OPCommonTelephony.jar:/system/framework/mediatek-ims-common.jar:/system/framework/mediatek-ims-base.jar:/system/framework/mediatek-telecom-common.jar:/apex/com.android.conscrypt/javalib/conscrypt.jar:/apex/com.android.media/javalib/updatable-media.jar
root/init.environ.rc:13 | early-init | export DEX2OATBOOTCLASSPATH /apex/com.android.runtime/javalib/core-oj.jar:/apex/com.android.runtime/javalib/core-libart.jar:/apex/com.android.runtime/javalib/okhttp.jar:/apex/com.android.runtime/javalib/bouncycastle.jar:/apex/com.android.runtime/javalib/apache-xml.jar:/system/framework/framework.jar:/system/framework/ext.jar:/system/framework/telephony-common.jar:/system/framework/voip-common.jar:/system/framework/ims-common.jar:/system/framework/android.test.base.jar:/system/framework/mediatek-telephony-base.jar:/system/framework/mediatek-telephony-common.jar:/system/framework/mediatek-common.jar:/system/framework/mediatek-framework.jar:/system/framework/OPCommonTelephony.jar:/system/framework/mediatek-ims-common.jar:/system/framework/mediatek-ims-base.jar:/system/framework/mediatek-telecom-common.jar
root/init.environ.rc:14 | early-init | export SYSTEMSERVERCLASSPATH /system/framework/services.jar:/system/framework/ethernet-service.jar:/system/framework/wifi-service.jar:/system/framework/com.android.location.provider.jar
root/init.rc:17 | early-init | write /proc/sys/kernel/sysrq 0
root/init.rc:20 | early-init | restorecon /adb_keys
root/init.rc:23 | early-init | restorecon /postinstall
root/init.rc:25 | early-init | mkdir /acct/uid
root/init.rc:28 | early-init | chown root system /dev/memcg/memory.pressure_level
root/init.rc:29 | early-init | chmod 0040 /dev/memcg/memory.pressure_level
root/init.rc:31 | early-init | mkdir /dev/memcg/apps/ 0755 system system
root/init.rc:33 | early-init | mkdir /dev/memcg/system 0550 system system
root/init.rc:35 | early-init | start ueventd
root/init.rc:40 | early-init | exec_start apexd-bootstrap
root/init.rc:43 | init | sysclktz 0
root/init.rc:46 | init | copy /proc/cmdline /dev/urandom
root/init.rc:47 | init | copy /system/etc/prop.default /dev/urandom
root/init.rc:49 | init | symlink /proc/self/fd/0 /dev/stdin
root/init.rc:50 | init | symlink /proc/self/fd/1 /dev/stdout
root/init.rc:51 | init | symlink /proc/self/fd/2 /dev/stderr
root/init.rc:53 | init | symlink /system/bin /bin
root/init.rc:54 | init | symlink /system/etc /etc
root/init.rc:57 | init | symlink /sys/kernel/debug /d
root/init.rc:60 | init | symlink /system/vendor /vendor
root/init.rc:63 | init | mkdir /dev/stune/foreground
root/init.rc:64 | init | mkdir /dev/stune/background
root/init.rc:65 | init | mkdir /dev/stune/top-app
root/init.rc:66 | init | mkdir /dev/stune/rt
root/init.rc:67 | init | chown system system /dev/stune
root/init.rc:68 | init | chown system system /dev/stune/foreground
root/init.rc:69 | init | chown system system /dev/stune/background
root/init.rc:70 | init | chown system system /dev/stune/top-app
root/init.rc:71 | init | chown system system /dev/stune/rt
root/init.rc:72 | init | chown system system /dev/stune/tasks
root/init.rc:73 | init | chown system system /dev/stune/foreground/tasks
root/init.rc:74 | init | chown system system /dev/stune/background/tasks
root/init.rc:75 | init | chown system system /dev/stune/top-app/tasks
root/init.rc:76 | init | chown system system /dev/stune/rt/tasks
root/init.rc:77 | init | chmod 0664 /dev/stune/tasks
root/init.rc:78 | init | chmod 0664 /dev/stune/foreground/tasks
root/init.rc:79 | init | chmod 0664 /dev/stune/background/tasks
root/init.rc:80 | init | chmod 0664 /dev/stune/top-app/tasks
root/init.rc:81 | init | chmod 0664 /dev/stune/rt/tasks
root/init.rc:86 | init | mkdir /dev/blkio/background
root/init.rc:87 | init | chown system system /dev/blkio
root/init.rc:88 | init | chown system system /dev/blkio/background
root/init.rc:89 | init | chown system system /dev/blkio/tasks
root/init.rc:90 | init | chown system system /dev/blkio/background/tasks
root/init.rc:91 | init | chmod 0664 /dev/blkio/tasks
root/init.rc:92 | init | chmod 0664 /dev/blkio/background/tasks
root/init.rc:93 | init | write /dev/blkio/blkio.weight 1000
root/init.rc:94 | init | write /dev/blkio/background/blkio.weight 500
root/init.rc:95 | init | write /dev/blkio/blkio.group_idle 0
root/init.rc:96 | init | write /dev/blkio/background/blkio.group_idle 0
root/init.rc:98 | init | restorecon_recursive /mnt
root/init.rc:100 | init | mount configfs none /config nodev noexec nosuid
root/init.rc:101 | init | chmod 0770 /config/sdcardfs
root/init.rc:102 | init | chown system package_info /config/sdcardfs
root/init.rc:104 | init | mkdir /mnt/secure 0700 root root
root/init.rc:105 | init | mkdir /mnt/secure/asec 0700 root root
root/init.rc:106 | init | mkdir /mnt/asec 0755 root system
root/init.rc:107 | init | mkdir /mnt/obb 0755 root system
root/init.rc:108 | init | mkdir /mnt/media_rw 0750 root media_rw
root/init.rc:109 | init | mkdir /mnt/user 0755 root root
root/init.rc:110 | init | mkdir /mnt/user/0 0755 root root
root/init.rc:111 | init | mkdir /mnt/expand 0771 system system
root/init.rc:112 | init | mkdir /mnt/appfuse 0711 root root
root/init.rc:115 | init | mkdir /mnt/runtime 0700 root root
root/init.rc:116 | init | mkdir /mnt/runtime/default 0755 root root
root/init.rc:117 | init | mkdir /mnt/runtime/default/self 0755 root root
root/init.rc:118 | init | mkdir /mnt/runtime/read 0755 root root
root/init.rc:119 | init | mkdir /mnt/runtime/read/self 0755 root root
root/init.rc:120 | init | mkdir /mnt/runtime/write 0755 root root
root/init.rc:121 | init | mkdir /mnt/runtime/write/self 0755 root root
root/init.rc:122 | init | mkdir /mnt/runtime/full 0755 root root
root/init.rc:123 | init | mkdir /mnt/runtime/full/self 0755 root root
root/init.rc:126 | init | symlink /storage/self/primary /sdcard
root/init.rc:127 | init | symlink /storage/self/primary /mnt/sdcard
root/init.rc:128 | init | symlink /mnt/user/0/primary /mnt/runtime/default/self/primary
root/init.rc:130 | init | write /proc/sys/kernel/panic_on_oops 1
root/init.rc:131 | init | write /proc/sys/kernel/hung_task_timeout_secs 0
root/init.rc:132 | init | write /proc/cpu/alignment 4
root/init.rc:138 | init | write /proc/sys/kernel/sched_tunable_scaling 0
root/init.rc:139 | init | write /proc/sys/kernel/sched_latency_ns 10000000
root/init.rc:140 | init | write /proc/sys/kernel/sched_wakeup_granularity_ns 2000000
root/init.rc:141 | init | write /proc/sys/kernel/sched_child_runs_first 0
root/init.rc:143 | init | write /proc/sys/kernel/randomize_va_space 2
root/init.rc:144 | init | write /proc/sys/vm/mmap_min_addr 32768
root/init.rc:145 | init | write /proc/sys/net/ipv4/ping_group_range "0 2147483647"
root/init.rc:146 | init | write /proc/sys/net/unix/max_dgram_qlen 600
root/init.rc:147 | init | write /proc/sys/kernel/sched_rt_runtime_us 950000
root/init.rc:148 | init | write /proc/sys/kernel/sched_rt_period_us 1000000
root/init.rc:153 | init | write /proc/sys/net/core/rmem_max  262144
root/init.rc:154 | init | write /proc/sys/net/core/wmem_max  262144
root/init.rc:157 | init | write /proc/sys/net/ipv4/fwmark_reflect 1
root/init.rc:158 | init | write /proc/sys/net/ipv6/fwmark_reflect 1
root/init.rc:161 | init | write /proc/sys/net/ipv4/tcp_fwmark_accept 1
root/init.rc:164 | init | write /proc/sys/net/ipv4/conf/all/accept_redirects 0
root/init.rc:165 | init | write /proc/sys/net/ipv6/conf/all/accept_redirects 0
root/init.rc:168 | init | chmod 0400 /proc/net/fib_trie
root/init.rc:171 | init | chown system system /dev/cpuctl
root/init.rc:172 | init | chown system system /dev/cpuctl/tasks
root/init.rc:173 | init | chmod 0666 /dev/cpuctl/tasks
root/init.rc:174 | init | write /dev/cpuctl/cpu.rt_period_us 1000000
root/init.rc:175 | init | write /dev/cpuctl/cpu.rt_runtime_us 950000
root/init.rc:180 | init | mkdir /dev/cpuset/foreground
root/init.rc:181 | init | copy /dev/cpuset/cpus /dev/cpuset/foreground/cpus
root/init.rc:182 | init | copy /dev/cpuset/mems /dev/cpuset/foreground/mems
root/init.rc:183 | init | mkdir /dev/cpuset/background
root/init.rc:184 | init | copy /dev/cpuset/cpus /dev/cpuset/background/cpus
root/init.rc:185 | init | copy /dev/cpuset/mems /dev/cpuset/background/mems
root/init.rc:190 | init | mkdir /dev/cpuset/system-background
root/init.rc:191 | init | copy /dev/cpuset/cpus /dev/cpuset/system-background/cpus
root/init.rc:192 | init | copy /dev/cpuset/mems /dev/cpuset/system-background/mems
root/init.rc:196 | init | mkdir /dev/cpuset/restricted
root/init.rc:197 | init | copy /dev/cpuset/cpus /dev/cpuset/restricted/cpus
root/init.rc:198 | init | copy /dev/cpuset/mems /dev/cpuset/restricted/mems
root/init.rc:200 | init | mkdir /dev/cpuset/top-app
root/init.rc:201 | init | copy /dev/cpuset/cpus /dev/cpuset/top-app/cpus
root/init.rc:202 | init | copy /dev/cpuset/mems /dev/cpuset/top-app/mems
root/init.rc:205 | init | chown system system /dev/cpuset
root/init.rc:206 | init | chown system system /dev/cpuset/foreground
root/init.rc:207 | init | chown system system /dev/cpuset/background
root/init.rc:208 | init | chown system system /dev/cpuset/system-background
root/init.rc:209 | init | chown system system /dev/cpuset/top-app
root/init.rc:210 | init | chown system system /dev/cpuset/restricted
root/init.rc:211 | init | chown system system /dev/cpuset/tasks
root/init.rc:212 | init | chown system system /dev/cpuset/foreground/tasks
root/init.rc:213 | init | chown system system /dev/cpuset/background/tasks
root/init.rc:214 | init | chown system system /dev/cpuset/system-background/tasks
root/init.rc:215 | init | chown system system /dev/cpuset/top-app/tasks
root/init.rc:216 | init | chown system system /dev/cpuset/restricted/tasks
root/init.rc:219 | init | chmod 0775 /dev/cpuset/system-background
root/init.rc:221 | init | chmod 0664 /dev/cpuset/foreground/tasks
root/init.rc:222 | init | chmod 0664 /dev/cpuset/background/tasks
root/init.rc:223 | init | chmod 0664 /dev/cpuset/system-background/tasks
root/init.rc:224 | init | chmod 0664 /dev/cpuset/top-app/tasks
root/init.rc:225 | init | chmod 0664 /dev/cpuset/restricted/tasks
root/init.rc:226 | init | chmod 0664 /dev/cpuset/tasks
root/init.rc:229 | init | chown system system /proc/pressure/memory
root/init.rc:230 | init | chmod 0664 /proc/pressure/memory
root/init.rc:235 | init | chown root net_bw_acct /proc/net/xt_qtaguid/ctrl
root/init.rc:236 | init | chown root net_bw_stats /proc/net/xt_qtaguid/stats
root/init.rc:240 | init | chmod 0644 /dev/xt_qtaguid
root/init.rc:242 | init | chown root root /dev/cg2_bpf
root/init.rc:243 | init | chmod 0600 /dev/cg2_bpf
root/init.rc:244 | init | mount bpf bpf /sys/fs/bpf nodev noexec nosuid
root/init.rc:248 | init | mkdir /dev/fscklogs 0770 root system
root/init.rc:251 | init | mount pstore pstore /sys/fs/pstore nodev noexec nosuid
root/init.rc:252 | init | chown system log /sys/fs/pstore
root/init.rc:253 | init | chmod 0550 /sys/fs/pstore
root/init.rc:254 | init | chown system log /sys/fs/pstore/console-ramoops
root/init.rc:255 | init | chmod 0440 /sys/fs/pstore/console-ramoops
root/init.rc:256 | init | chown system log /sys/fs/pstore/console-ramoops-0
root/init.rc:257 | init | chmod 0440 /sys/fs/pstore/console-ramoops-0
root/init.rc:258 | init | chown system log /sys/fs/pstore/pmsg-ramoops-0
root/init.rc:259 | init | chmod 0440 /sys/fs/pstore/pmsg-ramoops-0
root/init.rc:262 | init | write /proc/sys/abi/swp 1
root/init.rc:266 | init | symlink /proc/self/fd /dev/fd
root/init.rc:268 | init | export DOWNLOAD_CACHE /data/cache
root/init.rc:271 | init | setrlimit nice 40 40
root/init.rc:274 | init | setrlimit nofile 32768 32768
root/init.rc:278 | init | write /sys/class/leds/vibrator/trigger "transient"
root/init.rc:281 | init | write /dev/cpu_variant:${ro.bionic.arch} ${ro.bionic.cpu_variant}
root/init.rc:282 | init | chmod 0444 /dev/cpu_variant:${ro.bionic.arch}
root/init.rc:283 | init | write /dev/cpu_variant:${ro.bionic.2nd_arch} ${ro.bionic.2nd_cpu_variant}
root/init.rc:284 | init | chmod 0444 /dev/cpu_variant:${ro.bionic.2nd_arch}
root/init.rc:287 | init | chown system system /sys/power/state
root/init.rc:288 | init | chown system system /sys/power/wakeup_count
root/init.rc:289 | init | chmod 0660 /sys/power/state
root/init.rc:291 | init | chown radio wakelock /sys/power/wake_lock
root/init.rc:292 | init | chown radio wakelock /sys/power/wake_unlock
root/init.rc:293 | init | chmod 0660 /sys/power/wake_lock
root/init.rc:294 | init | chmod 0660 /sys/power/wake_unlock
root/init.rc:297 | init | start logd
root/init.rc:300 | init | start servicemanager
root/init.rc:301 | init | start hwservicemanager
root/init.rc:302 | init | start vndservicemanager
root/init.rc:307 | property:sys.boot_from_charger_mode=1 | class_stop charger
root/init.rc:308 | property:sys.boot_from_charger_mode=1 | trigger late-init
root/init.rc:311 | load_persist_props_action | load_persist_props
root/init.rc:312 | load_persist_props_action | start logd
root/init.rc:313 | load_persist_props_action | start logd-reinit
root/init.rc:317 | firmware_mounts_complete | rm /dev/.booting
root/init.rc:321 | late-init | trigger early-fs
root/init.rc:327 | late-init | trigger fs
root/init.rc:328 | late-init | trigger post-fs
root/init.rc:335 | late-init | trigger late-fs
root/init.rc:339 | late-init | trigger post-fs-data
root/init.rc:342 | late-init | trigger load_persist_props_action
root/init.rc:345 | late-init | trigger zygote-start
root/init.rc:348 | late-init | trigger firmware_mounts_complete
root/init.rc:350 | late-init | trigger early-boot
root/init.rc:351 | late-init | trigger boot
root/init.rc:355 | early-fs | start vold
root/init.rc:358 | post-fs | exec - system system -- /system/bin/vdc checkpoint markBootAttempt
root/init.rc:362 | post-fs | mount rootfs rootfs / remount bind ro nodev
root/init.rc:364 | post-fs | mount none /mnt/runtime/default /storage bind rec
root/init.rc:365 | post-fs | mount none none /storage slave rec
root/init.rc:369 | post-fs | restorecon --recursive --cross-filesystems /sys/kernel/debug
root/init.rc:372 | post-fs | chown system cache /cache
root/init.rc:373 | post-fs | chmod 0770 /cache
root/init.rc:375 | post-fs | restorecon_recursive /cache
root/init.rc:379 | post-fs | mkdir /cache/recovery 0770 system cache
root/init.rc:382 | post-fs | mkdir /cache/backup_stage 0700 system system
root/init.rc:383 | post-fs | mkdir /cache/backup 0700 system system
root/init.rc:386 | post-fs | chown root log /proc/vmallocinfo
root/init.rc:387 | post-fs | chmod 0440 /proc/vmallocinfo
root/init.rc:389 | post-fs | chown root log /proc/slabinfo
root/init.rc:390 | post-fs | chmod 0440 /proc/slabinfo
root/init.rc:393 | post-fs | chown root system /proc/kmsg
root/init.rc:394 | post-fs | chmod 0440 /proc/kmsg
root/init.rc:395 | post-fs | chown root system /proc/sysrq-trigger
root/init.rc:396 | post-fs | chmod 0220 /proc/sysrq-trigger
root/init.rc:397 | post-fs | chown system log /proc/last_kmsg
root/init.rc:398 | post-fs | chmod 0440 /proc/last_kmsg
root/init.rc:401 | post-fs | chmod 0444 /sys/fs/selinux/policy
root/init.rc:404 | post-fs | mkdir /cache/lost+found 0770 root root
root/init.rc:406 | post-fs | restorecon_recursive /metadata
root/init.rc:407 | post-fs | mkdir /metadata/vold
root/init.rc:408 | post-fs | chmod 0700 /metadata/vold
root/init.rc:409 | post-fs | mkdir /metadata/password_slots 0771 root system
root/init.rc:411 | post-fs | mkdir /metadata/apex 0700 root system
root/init.rc:412 | post-fs | mkdir /metadata/apex/sessions 0700 root system
root/init.rc:416 | late-fs | chmod 0755 /sys/kernel/debug/tracing
root/init.rc:419 | late-fs | class_start early_hal
root/init.rc:422 | post-fs-data | mark_post_data
root/init.rc:425 | post-fs-data | start vold
root/init.rc:426 | post-fs-data | exec - system system -- /system/bin/vdc checkpoint prepareCheckpoint
root/init.rc:429 | post-fs-data | chown system system /data
root/init.rc:430 | post-fs-data | chmod 0771 /data
root/init.rc:432 | post-fs-data | restorecon /data
root/init.rc:435 | post-fs-data | installkey /data
root/init.rc:439 | post-fs-data | mkdir /data/bootchart 0755 shell shell
root/init.rc:440 | post-fs-data | bootchart start
root/init.rc:444 | post-fs-data | exec -- /system/bin/fsverity_init
root/init.rc:447 | post-fs-data | enter_default_mount_ns
root/init.rc:450 | post-fs-data | mkdir /data/apex 0750 root system
root/init.rc:451 | post-fs-data | mkdir /data/apex/active 0750 root system
root/init.rc:452 | post-fs-data | mkdir /data/apex/backup 0700 root system
root/init.rc:453 | post-fs-data | mkdir /data/apex/sessions 0700 root system
root/init.rc:454 | post-fs-data | mkdir /data/app-staging 0750 system system
root/init.rc:455 | post-fs-data | start apexd
root/init.rc:458 | post-fs-data | copy /data/system/entropy.dat /dev/urandom
root/init.rc:461 | post-fs-data | mkdir /data/misc 01771 system misc
root/init.rc:462 | post-fs-data | mkdir /data/misc/recovery 0770 system log
root/init.rc:463 | post-fs-data | copy /data/misc/recovery/ro.build.fingerprint /data/misc/recovery/ro.build.fingerprint.1
root/init.rc:464 | post-fs-data | chmod 0440 /data/misc/recovery/ro.build.fingerprint.1
root/init.rc:465 | post-fs-data | chown system log /data/misc/recovery/ro.build.fingerprint.1
root/init.rc:466 | post-fs-data | write /data/misc/recovery/ro.build.fingerprint ${ro.build.fingerprint}
root/init.rc:467 | post-fs-data | chmod 0440 /data/misc/recovery/ro.build.fingerprint
root/init.rc:468 | post-fs-data | chown system log /data/misc/recovery/ro.build.fingerprint
root/init.rc:469 | post-fs-data | mkdir /data/misc/recovery/proc 0770 system log
root/init.rc:470 | post-fs-data | copy /data/misc/recovery/proc/version /data/misc/recovery/proc/version.1
root/init.rc:471 | post-fs-data | chmod 0440 /data/misc/recovery/proc/version.1
root/init.rc:472 | post-fs-data | chown system log /data/misc/recovery/proc/version.1
root/init.rc:473 | post-fs-data | copy /proc/version /data/misc/recovery/proc/version
root/init.rc:474 | post-fs-data | chmod 0440 /data/misc/recovery/proc/version
root/init.rc:475 | post-fs-data | chown system log /data/misc/recovery/proc/version
root/init.rc:476 | post-fs-data | mkdir /data/misc/bluedroid 02770 bluetooth bluetooth
root/init.rc:478 | post-fs-data | chmod 0660 /data/misc/bluedroid/bt_config.conf
root/init.rc:479 | post-fs-data | chown bluetooth bluetooth /data/misc/bluedroid/bt_config.conf
root/init.rc:480 | post-fs-data | mkdir /data/misc/bluetooth 0770 bluetooth bluetooth
root/init.rc:481 | post-fs-data | mkdir /data/misc/bluetooth/logs 0770 bluetooth bluetooth
root/init.rc:482 | post-fs-data | mkdir /data/misc/keystore 0700 keystore keystore
root/init.rc:483 | post-fs-data | mkdir /data/misc/gatekeeper 0700 system system
root/init.rc:484 | post-fs-data | mkdir /data/misc/keychain 0771 system system
root/init.rc:485 | post-fs-data | mkdir /data/misc/net 0750 root shell
root/init.rc:486 | post-fs-data | mkdir /data/misc/radio 0770 system radio
root/init.rc:487 | post-fs-data | mkdir /data/misc/sms 0770 system radio
root/init.rc:488 | post-fs-data | mkdir /data/misc/carrierid 0770 system radio
root/init.rc:489 | post-fs-data | mkdir /data/misc/apns 0770 system radio
root/init.rc:490 | post-fs-data | mkdir /data/misc/zoneinfo 0775 system system
root/init.rc:491 | post-fs-data | mkdir /data/misc/network_watchlist 0774 system system
root/init.rc:492 | post-fs-data | mkdir /data/misc/textclassifier 0771 system system
root/init.rc:493 | post-fs-data | mkdir /data/misc/vpn 0770 system vpn
root/init.rc:494 | post-fs-data | mkdir /data/misc/shared_relro 0771 shared_relro shared_relro
root/init.rc:495 | post-fs-data | mkdir /data/misc/systemkeys 0700 system system
root/init.rc:496 | post-fs-data | mkdir /data/misc/wifi 0770 wifi wifi
root/init.rc:497 | post-fs-data | mkdir /data/misc/wifi/sockets 0770 wifi wifi
root/init.rc:498 | post-fs-data | mkdir /data/misc/wifi/wpa_supplicant 0770 wifi wifi
root/init.rc:499 | post-fs-data | mkdir /data/misc/ethernet 0770 system system
root/init.rc:500 | post-fs-data | mkdir /data/misc/dhcp 0770 dhcp dhcp
root/init.rc:501 | post-fs-data | mkdir /data/misc/user 0771 root root
root/init.rc:502 | post-fs-data | mkdir /data/misc/perfprofd 0775 root root
root/init.rc:504 | post-fs-data | chmod 0660 /data/misc/wifi/wpa_supplicant.conf
root/init.rc:505 | post-fs-data | mkdir /data/local 0751 root root
root/init.rc:506 | post-fs-data | mkdir /data/misc/media 0700 media media
root/init.rc:507 | post-fs-data | mkdir /data/misc/audioserver 0700 audioserver audioserver
root/init.rc:508 | post-fs-data | mkdir /data/misc/cameraserver 0700 cameraserver cameraserver
root/init.rc:509 | post-fs-data | mkdir /data/misc/vold 0700 root root
root/init.rc:510 | post-fs-data | mkdir /data/misc/boottrace 0771 system shell
root/init.rc:511 | post-fs-data | mkdir /data/misc/update_engine 0700 root root
root/init.rc:512 | post-fs-data | mkdir /data/misc/update_engine_log 02750 root log
root/init.rc:513 | post-fs-data | mkdir /data/misc/trace 0700 root root
root/init.rc:515 | post-fs-data | mkdir /data/misc/wmtrace 0700 system system
root/init.rc:517 | post-fs-data | mkdir /data/misc/profiles 0771 system system
root/init.rc:518 | post-fs-data | mkdir /data/misc/profiles/cur 0771 system system
root/init.rc:519 | post-fs-data | mkdir /data/misc/profiles/ref 0771 system system
root/init.rc:520 | post-fs-data | mkdir /data/misc/profman 0770 system shell
root/init.rc:521 | post-fs-data | mkdir /data/misc/gcov 0770 root root
root/init.rc:523 | post-fs-data | mkdir /data/preloads 0775 system system
root/init.rc:525 | post-fs-data | mkdir /data/vendor 0771 root root
root/init.rc:526 | post-fs-data | mkdir /data/vendor_ce 0771 root root
root/init.rc:527 | post-fs-data | mkdir /data/vendor_de 0771 root root
root/init.rc:528 | post-fs-data | mkdir /data/vendor/hardware 0771 root root
root/init.rc:532 | post-fs-data | mkdir /data/local/tmp 0771 shell shell
root/init.rc:533 | post-fs-data | mkdir /data/local/traces 0777 shell shell
root/init.rc:534 | post-fs-data | mkdir /data/data 0771 system system
root/init.rc:535 | post-fs-data | mkdir /data/app-private 0771 system system
root/init.rc:536 | post-fs-data | mkdir /data/app-ephemeral 0771 system system
root/init.rc:537 | post-fs-data | mkdir /data/app-asec 0700 root root
root/init.rc:538 | post-fs-data | mkdir /data/app-lib 0771 system system
root/init.rc:539 | post-fs-data | mkdir /data/app 0771 system system
root/init.rc:540 | post-fs-data | mkdir /data/property 0700 root root
root/init.rc:541 | post-fs-data | mkdir /data/tombstones 0771 system system
root/init.rc:542 | post-fs-data | mkdir /data/vendor/tombstones 0771 root root
root/init.rc:543 | post-fs-data | mkdir /data/vendor/tombstones/wifi 0771 wifi wifi
root/init.rc:546 | post-fs-data | mkdir /data/dalvik-cache 0771 root root
root/init.rc:548 | post-fs-data | mkdir /data/ota 0771 root root
root/init.rc:552 | post-fs-data | mkdir /data/ota_package 0770 system cache
root/init.rc:555 | post-fs-data | mkdir /data/resource-cache 0771 system system
root/init.rc:556 | post-fs-data | chown system system /data/resource-cache
root/init.rc:557 | post-fs-data | chmod 0771 /data/resource-cache
root/init.rc:560 | post-fs-data | mkdir /data/lost+found 0770 root root
root/init.rc:564 | post-fs-data | mkdir /data/drm 0770 drm drm
root/init.rc:568 | post-fs-data | mkdir /data/mediadrm 0770 mediadrm mediadrm
root/init.rc:570 | post-fs-data | mkdir /data/anr 0775 system system
root/init.rc:573 | post-fs-data | mkdir /data/nfc 0770 nfc nfc
root/init.rc:574 | post-fs-data | mkdir /data/nfc/param 0770 nfc nfc
root/init.rc:578 | post-fs-data | mkdir /data/backup 0700 system system
root/init.rc:579 | post-fs-data | mkdir /data/ss 0700 system system
root/init.rc:581 | post-fs-data | mkdir /data/system 0775 system system
root/init.rc:582 | post-fs-data | mkdir /data/system/dropbox 0700 system system
root/init.rc:583 | post-fs-data | mkdir /data/system/heapdump 0700 system system
root/init.rc:584 | post-fs-data | mkdir /data/system/users 0775 system system
root/init.rc:586 | post-fs-data | mkdir /data/system_de 0770 system system
root/init.rc:587 | post-fs-data | mkdir /data/system_ce 0770 system system
root/init.rc:589 | post-fs-data | mkdir /data/misc_de 01771 system misc
root/init.rc:590 | post-fs-data | mkdir /data/misc_ce 01771 system misc
root/init.rc:592 | post-fs-data | mkdir /data/user 0711 system system
root/init.rc:593 | post-fs-data | mkdir /data/user_de 0711 system system
root/init.rc:594 | post-fs-data | symlink /data/data /data/user/0
root/init.rc:596 | post-fs-data | mkdir /data/media 0770 media_rw media_rw
root/init.rc:597 | post-fs-data | mkdir /data/media/obb 0770 media_rw media_rw
root/init.rc:599 | post-fs-data | mkdir /data/cache 0770 system cache
root/init.rc:600 | post-fs-data | mkdir /data/cache/recovery 0770 system cache
root/init.rc:601 | post-fs-data | mkdir /data/cache/backup_stage 0700 system system
root/init.rc:602 | post-fs-data | mkdir /data/cache/backup 0700 system system
root/init.rc:605 | post-fs-data | wait_for_prop apexd.status ready
root/init.rc:606 | post-fs-data | parse_apex_configs
root/init.rc:608 | post-fs-data | init_user0
root/init.rc:611 | post-fs-data | restorecon --recursive --skip-ce /data
root/init.rc:614 | post-fs-data | exec - system system -- /system/bin/tzdatacheck /apex/com.android.runtime/etc/tz /data/misc/zoneinfo
root/init.rc:625 | post-fs-data | setprop sys.use_memfd false
root/init.rc:628 | post-fs-data | chown root system /dev/fscklogs/log
root/init.rc:629 | post-fs-data | chmod 0770 /dev/fscklogs/log
root/init.rc:635 | zygote-start && property:ro.crypto.state=unencrypted | exec_start update_verifier_nonencrypted
root/init.rc:636 | zygote-start && property:ro.crypto.state=unencrypted | start netd
root/init.rc:637 | zygote-start && property:ro.crypto.state=unencrypted | start zygote
root/init.rc:638 | zygote-start && property:ro.crypto.state=unencrypted | start zygote_secondary
root/init.rc:642 | zygote-start && property:ro.crypto.state=unsupported | exec_start update_verifier_nonencrypted
root/init.rc:643 | zygote-start && property:ro.crypto.state=unsupported | start netd
root/init.rc:644 | zygote-start && property:ro.crypto.state=unsupported | start zygote
root/init.rc:645 | zygote-start && property:ro.crypto.state=unsupported | start zygote_secondary
root/init.rc:649 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | exec_start update_verifier_nonencrypted
root/init.rc:650 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start netd
root/init.rc:651 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote
root/init.rc:652 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote_secondary
root/init.rc:656 | boot | ifup lo
root/init.rc:657 | boot | hostname localhost
root/init.rc:658 | boot | domainname localdomain
root/init.rc:661 | boot | write /proc/sys/net/core/xfrm_acq_expires 3600
root/init.rc:666 | boot | write /proc/sys/vm/overcommit_memory 1
root/init.rc:667 | boot | write /proc/sys/vm/min_free_order_shift 4
root/init.rc:668 | boot | chown root system /sys/module/lowmemorykiller/parameters/adj
root/init.rc:669 | boot | chmod 0664 /sys/module/lowmemorykiller/parameters/adj
root/init.rc:670 | boot | chown root system /sys/module/lowmemorykiller/parameters/minfree
root/init.rc:671 | boot | chmod 0664 /sys/module/lowmemorykiller/parameters/minfree
root/init.rc:674 | boot | chown root system /sys/block/zram0/idle
root/init.rc:675 | boot | chmod 0664 /sys/block/zram0/idle
root/init.rc:676 | boot | chown root system /sys/block/zram0/writeback
root/init.rc:677 | boot | chmod 0664 /sys/block/zram0/writeback
root/init.rc:680 | boot | write /proc/sys/vm/dirty_expire_centisecs 200
root/init.rc:681 | boot | write /proc/sys/vm/dirty_background_ratio  5
root/init.rc:687 | boot | write /sys/fs/f2fs/${dev.mnt.blk.data}/cp_interval 200
root/init.rc:690 | boot | chown radio system /sys/android_power/state
root/init.rc:691 | boot | chown radio system /sys/android_power/request_state
root/init.rc:692 | boot | chown radio system /sys/android_power/acquire_full_wake_lock
root/init.rc:693 | boot | chown radio system /sys/android_power/acquire_partial_wake_lock
root/init.rc:694 | boot | chown radio system /sys/android_power/release_wake_lock
root/init.rc:695 | boot | chown system system /sys/power/autosleep
root/init.rc:697 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/timer_rate
root/init.rc:698 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/timer_rate
root/init.rc:699 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/timer_slack
root/init.rc:700 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/timer_slack
root/init.rc:701 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/min_sample_time
root/init.rc:702 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/min_sample_time
root/init.rc:703 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
root/init.rc:704 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
root/init.rc:705 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/target_loads
root/init.rc:706 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/target_loads
root/init.rc:707 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/go_hispeed_load
root/init.rc:708 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/go_hispeed_load
root/init.rc:709 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/above_hispeed_delay
root/init.rc:710 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/above_hispeed_delay
root/init.rc:711 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/boost
root/init.rc:712 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/boost
root/init.rc:713 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/boostpulse
root/init.rc:714 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/input_boost
root/init.rc:715 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/input_boost
root/init.rc:716 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/boostpulse_duration
root/init.rc:717 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/boostpulse_duration
root/init.rc:718 | boot | chown system system /sys/devices/system/cpu/cpufreq/interactive/io_is_busy
root/init.rc:719 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/io_is_busy
root/init.rc:722 | boot | chown system system /sys/devices/system/cpu/cpu0/cpufreq/scaling_max_freq
root/init.rc:723 | boot | chmod 0660 /sys/devices/system/cpu/cpu0/cpufreq/scaling_max_freq
root/init.rc:725 | boot | chown system system /sys/class/leds/vibrator/trigger
root/init.rc:726 | boot | chown system system /sys/class/leds/vibrator/activate
root/init.rc:727 | boot | chown system system /sys/class/leds/vibrator/brightness
root/init.rc:728 | boot | chown system system /sys/class/leds/vibrator/duration
root/init.rc:729 | boot | chown system system /sys/class/leds/vibrator/state
root/init.rc:730 | boot | chown system system /sys/class/timed_output/vibrator/enable
root/init.rc:731 | boot | chown system system /sys/class/leds/keyboard-backlight/brightness
root/init.rc:732 | boot | chown system system /sys/class/leds/lcd-backlight/brightness
root/init.rc:733 | boot | chown system system /sys/class/leds/button-backlight/brightness
root/init.rc:734 | boot | chown system system /sys/class/leds/jogball-backlight/brightness
root/init.rc:735 | boot | chown system system /sys/class/leds/red/brightness
root/init.rc:736 | boot | chown system system /sys/class/leds/green/brightness
root/init.rc:737 | boot | chown system system /sys/class/leds/blue/brightness
root/init.rc:738 | boot | chown system system /sys/class/leds/red/device/grpfreq
root/init.rc:739 | boot | chown system system /sys/class/leds/red/device/grppwm
root/init.rc:740 | boot | chown system system /sys/class/leds/red/device/blink
root/init.rc:741 | boot | chown system system /sys/module/sco/parameters/disable_esco
root/init.rc:742 | boot | chown system system /sys/kernel/ipv4/tcp_wmem_min
root/init.rc:743 | boot | chown system system /sys/kernel/ipv4/tcp_wmem_def
root/init.rc:744 | boot | chown system system /sys/kernel/ipv4/tcp_wmem_max
root/init.rc:745 | boot | chown system system /sys/kernel/ipv4/tcp_rmem_min
root/init.rc:746 | boot | chown system system /sys/kernel/ipv4/tcp_rmem_def
root/init.rc:747 | boot | chown system system /sys/kernel/ipv4/tcp_rmem_max
root/init.rc:748 | boot | chown root radio /proc/cmdline
root/init.rc:751 | boot | setprop net.tcp.default_init_rwnd 60
root/init.rc:754 | boot | class_start hal
root/init.rc:756 | boot | class_start core
root/init.rc:759 | nonencrypted | class_start main
root/init.rc:760 | nonencrypted | class_start late_start
root/init.rc:763 | property:sys.init_log_level=* | loglevel ${sys.init_log_level}
root/init.rc:766 | charger | class_start charger
root/init.rc:769 | property:vold.decrypt=trigger_load_persist_props | load_persist_props
root/init.rc:770 | property:vold.decrypt=trigger_load_persist_props | start logd
root/init.rc:771 | property:vold.decrypt=trigger_load_persist_props | start logd-reinit
root/init.rc:774 | property:vold.decrypt=trigger_post_fs_data | trigger post-fs-data
root/init.rc:775 | property:vold.decrypt=trigger_post_fs_data | trigger zygote-start
root/init.rc:779 | property:vold.decrypt=trigger_restart_min_framework | exec_start update_verifier
root/init.rc:780 | property:vold.decrypt=trigger_restart_min_framework | class_start_post_data hal
root/init.rc:781 | property:vold.decrypt=trigger_restart_min_framework | class_start_post_data core
root/init.rc:782 | property:vold.decrypt=trigger_restart_min_framework | class_start main
root/init.rc:786 | property:vold.decrypt=trigger_restart_framework | exec_start update_verifier
root/init.rc:787 | property:vold.decrypt=trigger_restart_framework | class_start_post_data hal
root/init.rc:788 | property:vold.decrypt=trigger_restart_framework | class_start_post_data core
root/init.rc:789 | property:vold.decrypt=trigger_restart_framework | class_start main
root/init.rc:790 | property:vold.decrypt=trigger_restart_framework | class_start late_start
root/init.rc:791 | property:vold.decrypt=trigger_restart_framework | setprop service.bootanim.exit 0
root/init.rc:792 | property:vold.decrypt=trigger_restart_framework | start bootanim
root/init.rc:795 | property:vold.decrypt=trigger_shutdown_framework | class_reset late_start
root/init.rc:796 | property:vold.decrypt=trigger_shutdown_framework | class_reset main
root/init.rc:797 | property:vold.decrypt=trigger_shutdown_framework | class_reset_post_data core
root/init.rc:798 | property:vold.decrypt=trigger_shutdown_framework | class_reset_post_data hal
root/init.rc:801 | property:sys.boot_completed=1 | bootchart stop
root/init.rc:807 | property:sys.sysctl.extra_free_kbytes=* | write /proc/sys/vm/extra_free_kbytes ${sys.sysctl.extra_free_kbytes}
root/init.rc:811 | property:sys.sysctl.tcp_def_init_rwnd=* | write /proc/sys/net/ipv4/tcp_default_init_rwnd ${sys.sysctl.tcp_def_init_rwnd}
root/init.rc:814 | property:security.perf_harden=0 | write /proc/sys/kernel/perf_event_paranoid 1
root/init.rc:815 | property:security.perf_harden=0 | write /proc/sys/kernel/perf_event_max_sample_rate ${debug.perf_event_max_sample_rate:-100000}
root/init.rc:816 | property:security.perf_harden=0 | write /proc/sys/kernel/perf_cpu_time_max_percent ${debug.perf_cpu_time_max_percent:-25}
root/init.rc:817 | property:security.perf_harden=0 | write /proc/sys/kernel/perf_event_mlock_kb ${debug.perf_event_mlock_kb:-516}
root/init.rc:820 | property:security.perf_harden=1 | write /proc/sys/kernel/perf_event_paranoid 3
root/init.rc:846 | property:ro.debuggable=1 | chmod 0773 /data/misc/trace
root/init.rc:848 | property:ro.debuggable=1 | chmod 0775 /data/misc/wmtrace
root/init.rc:849 | property:ro.debuggable=1 | start console
root/init.usb.configfs.rc:2 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
root/init.usb.configfs.rc:3 | property:sys.usb.config=none && property:sys.usb.configfs=1 | stop adbd
root/init.usb.configfs.rc:4 | property:sys.usb.config=none && property:sys.usb.configfs=1 | setprop sys.usb.ffs.ready 0
root/init.usb.configfs.rc:5 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceClass 0
root/init.usb.configfs.rc:6 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceSubClass 0
root/init.usb.configfs.rc:7 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceProtocol 0
root/init.usb.configfs.rc:8 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:9 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:10 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f3
root/init.usb.configfs.rc:11 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rmdir /config/usb_gadget/g1/functions/rndis.gs4
root/init.usb.configfs.rc:12 | property:sys.usb.config=none && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:15 | property:init.svc.adbd=stopped | setprop sys.usb.ffs.ready 0
root/init.usb.configfs.rc:18 | property:sys.usb.config=adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:21 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb"
root/init.usb.configfs.rc:22 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:23 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:24 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:27 | property:sys.usb.config=mtp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"
root/init.usb.configfs.rc:28 | property:sys.usb.config=mtp && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:29 | property:sys.usb.config=mtp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:30 | property:sys.usb.config=mtp && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:33 | property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:36 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb"
root/init.usb.configfs.rc:37 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:38 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:39 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:40 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:43 | property:sys.usb.config=ptp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp"
root/init.usb.configfs.rc:44 | property:sys.usb.config=ptp && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:45 | property:sys.usb.config=ptp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:46 | property:sys.usb.config=ptp && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:49 | property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:52 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb"
root/init.usb.configfs.rc:53 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:54 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:55 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:56 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:59 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory"
root/init.usb.configfs.rc:60 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:61 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:62 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:65 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:68 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_adb"
root/init.usb.configfs.rc:69 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:70 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:71 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:72 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:75 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "audiosource"
root/init.usb.configfs.rc:76 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:77 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:78 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:81 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:84 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "audiosource_adb"
root/init.usb.configfs.rc:85 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:86 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:87 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:88 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:91 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_audiosource"
root/init.usb.configfs.rc:92 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:93 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:94 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:95 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:98 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:101 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "accessory_audiosource_adb"
root/init.usb.configfs.rc:102 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/accessory.gs2 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:103 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/audio_source.gs3 /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:104 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f3
root/init.usb.configfs.rc:105 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:106 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:109 | property:sys.usb.config=midi && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "midi"
root/init.usb.configfs.rc:110 | property:sys.usb.config=midi && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/midi.gs5 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:111 | property:sys.usb.config=midi && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:112 | property:sys.usb.config=midi && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:115 | property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:118 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "midi_adb"
root/init.usb.configfs.rc:119 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/midi.gs5 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:120 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:121 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:122 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:125 | property:sys.usb.config=rndis && property:sys.usb.configfs=1 | mkdir /config/usb_gadget/g1/functions/rndis.gs4
root/init.usb.configfs.rc:126 | property:sys.usb.config=rndis && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis"
root/init.usb.configfs.rc:127 | property:sys.usb.config=rndis && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:128 | property:sys.usb.config=rndis && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:129 | property:sys.usb.config=rndis && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.configfs.rc:132 | property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | start adbd
root/init.usb.configfs.rc:135 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | mkdir /config/usb_gadget/g1/functions/rndis.gs4
root/init.usb.configfs.rc:136 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"
root/init.usb.configfs.rc:137 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
root/init.usb.configfs.rc:138 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
root/init.usb.configfs.rc:139 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
root/init.usb.configfs.rc:140 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:7 | post-fs-data | chown system system /sys/class/android_usb/android0/f_mass_storage/lun/file
root/init.usb.rc:8 | post-fs-data | chmod 0660 /sys/class/android_usb/android0/f_mass_storage/lun/file
root/init.usb.rc:9 | post-fs-data | chown system system /sys/class/android_usb/android0/f_rndis/ethaddr
root/init.usb.rc:10 | post-fs-data | chmod 0660 /sys/class/android_usb/android0/f_rndis/ethaddr
root/init.usb.rc:11 | post-fs-data | mkdir /data/misc/adb 02750 system shell
root/init.usb.rc:12 | post-fs-data | mkdir /data/adb 0700 root root
root/init.usb.rc:22 | boot | setprop sys.usb.configfs 0
root/init.usb.rc:26 | property:sys.usb.config=none && property:sys.usb.configfs=0 | stop adbd
root/init.usb.rc:27 | property:sys.usb.config=none && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:28 | property:sys.usb.config=none && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/bDeviceClass 0
root/init.usb.rc:29 | property:sys.usb.config=none && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:35 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:36 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:37 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 4EE7
root/init.usb.rc:38 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:39 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:40 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | start adbd
root/init.usb.rc:41 | property:sys.usb.config=adb && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:45 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:46 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:47 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d00
root/init.usb.rc:48 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:49 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:50 | property:sys.usb.config=accessory && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:54 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:55 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:56 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d01
root/init.usb.rc:57 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:58 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:59 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | start adbd
root/init.usb.rc:60 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:64 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:65 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:66 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d02
root/init.usb.rc:67 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:68 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:69 | property:sys.usb.config=audio_source && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:73 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:74 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:75 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d03
root/init.usb.rc:76 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:77 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:78 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | start adbd
root/init.usb.rc:79 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:83 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:84 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:85 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d04
root/init.usb.rc:86 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:87 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:88 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:92 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 0
root/init.usb.rc:93 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idVendor 18d1
root/init.usb.rc:94 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/idProduct 2d05
root/init.usb.rc:95 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/functions ${sys.usb.config}
root/init.usb.rc:96 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | write /sys/class/android_usb/android0/enable 1
root/init.usb.rc:97 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | start adbd
root/init.usb.rc:98 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=0 | setprop sys.usb.state ${sys.usb.config}
root/init.usb.rc:103 | boot && property:persist.sys.usb.config=* | setprop sys.usb.config ${persist.sys.usb.config}
root/init.usb.rc:111 | property:sys.usb.typec.mode=dfp | write /sys/class/dual_role_usb/otg_default/mode ${sys.usb.typec.mode}
root/init.usb.rc:112 | property:sys.usb.typec.mode=dfp | setprop sys.usb.typec.state ${sys.usb.typec.mode}
root/init.usb.rc:115 | property:sys.usb.typec.mode=ufp | write /sys/class/dual_role_usb/otg_default/mode ${sys.usb.typec.mode}
root/init.usb.rc:116 | property:sys.usb.typec.mode=ufp | setprop sys.usb.typec.state ${sys.usb.typec.mode}
root/init.usb.rc:120 | property:sys.usb.typec.data_role=device | write /sys/class/dual_role_usb/otg_default/data_role ${sys.usb.typec.data_role}
root/init.usb.rc:121 | property:sys.usb.typec.data_role=device | setprop sys.usb.typec.state ${sys.usb.typec.data_role}
root/init.usb.rc:124 | property:sys.usb.typec.data_role=host | write /sys/class/dual_role_usb/otg_default/data_role ${sys.usb.typec.data_role}
root/init.usb.rc:125 | property:sys.usb.typec.data_role=host | setprop sys.usb.typec.state ${sys.usb.typec.data_role}
root/init.usb.rc:129 | property:sys.usb.typec.power_role=source | write /sys/class/dual_role_usb/otg_default/power_role ${sys.usb.typec.power_role}
root/init.usb.rc:130 | property:sys.usb.typec.power_role=source | setprop sys.usb.typec.state ${sys.usb.typec.power_role}
root/init.usb.rc:133 | property:sys.usb.typec.power_role=sink | write /sys/class/dual_role_usb/otg_default/power_role ${sys.usb.typec.power_role}
root/init.usb.rc:134 | property:sys.usb.typec.power_role=sink | setprop sys.usb.typec.state ${sys.usb.typec.power_role}
system/init/art_apex_boot_integrity.rc:21 | post-fs-data | exec - root -- /system/bin/art_apex_boot_integrity
system/init/atrace.rc:6 | late-init | chmod 0222 /sys/kernel/debug/tracing/trace_marker
system/init/atrace.rc:7 | late-init | chmod 0222 /sys/kernel/tracing/trace_marker
system/init/atrace.rc:10 | late-init | write /proc/sys/kernel/sched_schedstats 1
system/init/atrace.rc:14 | late-init | chmod 0666 /sys/kernel/debug/tracing/trace_clock
system/init/atrace.rc:15 | late-init | chmod 0666 /sys/kernel/tracing/trace_clock
system/init/atrace.rc:16 | late-init | chmod 0666 /sys/kernel/debug/tracing/buffer_size_kb
system/init/atrace.rc:17 | late-init | chmod 0666 /sys/kernel/tracing/buffer_size_kb
system/init/atrace.rc:18 | late-init | chmod 0666 /sys/kernel/debug/tracing/options/overwrite
system/init/atrace.rc:19 | late-init | chmod 0666 /sys/kernel/tracing/options/overwrite
system/init/atrace.rc:20 | late-init | chmod 0666 /sys/kernel/debug/tracing/options/print-tgid
system/init/atrace.rc:21 | late-init | chmod 0666 /sys/kernel/tracing/options/print-tgid
system/init/atrace.rc:22 | late-init | chmod 0666 /sys/kernel/debug/tracing/options/record-tgid
system/init/atrace.rc:23 | late-init | chmod 0666 /sys/kernel/tracing/options/record-tgid
system/init/atrace.rc:24 | late-init | chmod 0666 /sys/kernel/debug/tracing/saved_cmdlines_size
system/init/atrace.rc:25 | late-init | chmod 0666 /sys/kernel/tracing/saved_cmdlines_size
system/init/atrace.rc:26 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_switch/enable
system/init/atrace.rc:27 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_switch/enable
system/init/atrace.rc:28 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_wakeup/enable
system/init/atrace.rc:29 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_wakeup/enable
system/init/atrace.rc:30 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_blocked_reason/enable
system/init/atrace.rc:31 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_blocked_reason/enable
system/init/atrace.rc:32 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_cpu_hotplug/enable
system/init/atrace.rc:33 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_cpu_hotplug/enable
system/init/atrace.rc:34 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_pi_setprio/enable
system/init/atrace.rc:35 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_pi_setprio/enable
system/init/atrace.rc:36 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_process_exit/enable
system/init/atrace.rc:37 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_process_exit/enable
system/init/atrace.rc:38 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sched/sched_waking/enable
system/init/atrace.rc:39 | late-init | chmod 0666 /sys/kernel/tracing/events/sched/sched_waking/enable
system/init/atrace.rc:40 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/cgroup/enable
system/init/atrace.rc:41 | late-init | chmod 0666 /sys/kernel/tracing/events/cgroup/enable
system/init/atrace.rc:42 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/power/cpu_frequency/enable
system/init/atrace.rc:43 | late-init | chmod 0666 /sys/kernel/tracing/events/power/cpu_frequency/enable
system/init/atrace.rc:44 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/power/cpu_idle/enable
system/init/atrace.rc:45 | late-init | chmod 0666 /sys/kernel/tracing/events/power/cpu_idle/enable
system/init/atrace.rc:46 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/power/clock_set_rate/enable
system/init/atrace.rc:47 | late-init | chmod 0666 /sys/kernel/tracing/events/power/clock_set_rate/enable
system/init/atrace.rc:48 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/power/cpu_frequency_limits/enable
system/init/atrace.rc:49 | late-init | chmod 0666 /sys/kernel/tracing/events/power/cpu_frequency_limits/enable
system/init/atrace.rc:50 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/power/gpu_frequency/enable
system/init/atrace.rc:51 | late-init | chmod 0666 /sys/kernel/tracing/events/power/gpu_frequency/enable
system/init/atrace.rc:52 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/cpufreq_interactive/enable
system/init/atrace.rc:53 | late-init | chmod 0666 /sys/kernel/tracing/events/cpufreq_interactive/enable
system/init/atrace.rc:54 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/vmscan/mm_vmscan_direct_reclaim_begin/enable
system/init/atrace.rc:55 | late-init | chmod 0666 /sys/kernel/tracing/events/vmscan/mm_vmscan_direct_reclaim_begin/enable
system/init/atrace.rc:56 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/vmscan/mm_vmscan_direct_reclaim_end/enable
system/init/atrace.rc:57 | late-init | chmod 0666 /sys/kernel/tracing/events/vmscan/mm_vmscan_direct_reclaim_end/enable
system/init/atrace.rc:58 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/vmscan/mm_vmscan_kswapd_wake/enable
system/init/atrace.rc:59 | late-init | chmod 0666 /sys/kernel/tracing/events/vmscan/mm_vmscan_kswapd_wake/enable
system/init/atrace.rc:60 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/vmscan/mm_vmscan_kswapd_sleep/enable
system/init/atrace.rc:61 | late-init | chmod 0666 /sys/kernel/tracing/events/vmscan/mm_vmscan_kswapd_sleep/enable
system/init/atrace.rc:62 | late-init | chmod 0666 /sys/kernel/debug/tracing/tracing_on
system/init/atrace.rc:63 | late-init | chmod 0666 /sys/kernel/tracing/tracing_on
system/init/atrace.rc:64 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_transaction/enable
system/init/atrace.rc:65 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_transaction/enable
system/init/atrace.rc:66 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_transaction_received/enable
system/init/atrace.rc:67 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_transaction_received/enable
system/init/atrace.rc:68 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_transaction_alloc_buf/enable
system/init/atrace.rc:69 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_transaction_alloc_buf/enable
system/init/atrace.rc:70 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_lock/enable
system/init/atrace.rc:71 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_lock/enable
system/init/atrace.rc:72 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_locked/enable
system/init/atrace.rc:73 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_locked/enable
system/init/atrace.rc:74 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/binder/binder_unlock/enable
system/init/atrace.rc:75 | late-init | chmod 0666 /sys/kernel/tracing/events/binder/binder_unlock/enable
system/init/atrace.rc:76 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/enable
system/init/atrace.rc:77 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/enable
system/init/atrace.rc:78 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/i2c_read/enable
system/init/atrace.rc:79 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/i2c_read/enable
system/init/atrace.rc:80 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/i2c_write/enable
system/init/atrace.rc:81 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/i2c_write/enable
system/init/atrace.rc:82 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/i2c_result/enable
system/init/atrace.rc:83 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/i2c_result/enable
system/init/atrace.rc:84 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/i2c_reply/enable
system/init/atrace.rc:85 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/i2c_reply/enable
system/init/atrace.rc:86 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/smbus_read/enable
system/init/atrace.rc:87 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/smbus_read/enable
system/init/atrace.rc:88 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/smbus_write/enable
system/init/atrace.rc:89 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/smbus_write/enable
system/init/atrace.rc:90 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/smbus_result/enable
system/init/atrace.rc:91 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/smbus_result/enable
system/init/atrace.rc:92 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/i2c/smbus_reply/enable
system/init/atrace.rc:93 | late-init | chmod 0666 /sys/kernel/tracing/events/i2c/smbus_reply/enable
system/init/atrace.rc:94 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/lowmemorykiller/enable
system/init/atrace.rc:95 | late-init | chmod 0666 /sys/kernel/tracing/events/lowmemorykiller/enable
system/init/atrace.rc:96 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/sync/enable
system/init/atrace.rc:97 | late-init | chmod 0666 /sys/kernel/tracing/events/sync/enable
system/init/atrace.rc:98 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/fence/enable
system/init/atrace.rc:99 | late-init | chmod 0666 /sys/kernel/tracing/events/fence/enable
system/init/atrace.rc:100 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/dma_fence/enable
system/init/atrace.rc:101 | late-init | chmod 0666 /sys/kernel/tracing/events/dma_fence/enable
system/init/atrace.rc:102 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/kmem/rss_stat/enable
system/init/atrace.rc:103 | late-init | chmod 0666 /sys/kernel/tracing/events/kmem/rss_stat/enable
system/init/atrace.rc:104 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/kmem/ion_heap_grow/enable
system/init/atrace.rc:105 | late-init | chmod 0666 /sys/kernel/tracing/events/kmem/ion_heap_grow/enable
system/init/atrace.rc:106 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/kmem/ion_heap_shrink/enable
system/init/atrace.rc:107 | late-init | chmod 0666 /sys/kernel/tracing/events/kmem/ion_heap_shrink/enable
system/init/atrace.rc:108 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/signal/signal_generate/enable
system/init/atrace.rc:109 | late-init | chmod 0666 /sys/kernel/tracing/events/signal/signal_generate/enable
system/init/atrace.rc:110 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/signal/signal_deliver/enable
system/init/atrace.rc:111 | late-init | chmod 0666 /sys/kernel/tracing/events/signal/signal_deliver/enable
system/init/atrace.rc:112 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/mm_event/mm_event_record/enable
system/init/atrace.rc:113 | late-init | chmod 0666 /sys/kernel/tracing/events/mm_event/mm_event_record/enable
system/init/atrace.rc:114 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/lowmemorykiller/lowmemory_kill/enable
system/init/atrace.rc:115 | late-init | chmod 0666 /sys/kernel/tracing/events/lowmemorykiller/lowmemory_kill/enable
system/init/atrace.rc:116 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/oom/oom_score_adj_update/enable
system/init/atrace.rc:117 | late-init | chmod 0666 /sys/kernel/tracing/events/oom/oom_score_adj_update/enable
system/init/atrace.rc:118 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/task/task_rename/enable
system/init/atrace.rc:119 | late-init | chmod 0666 /sys/kernel/tracing/events/task/task_rename/enable
system/init/atrace.rc:120 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/task/task_newtask/enable
system/init/atrace.rc:121 | late-init | chmod 0666 /sys/kernel/tracing/events/task/task_newtask/enable
system/init/atrace.rc:124 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_get_data_block/enable
system/init/atrace.rc:125 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_get_data_block/enable
system/init/atrace.rc:126 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_iget/enable
system/init/atrace.rc:127 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_iget/enable
system/init/atrace.rc:128 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_sync_file_enter/enable
system/init/atrace.rc:129 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_sync_file_enter/enable
system/init/atrace.rc:130 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_sync_file_exit/enable
system/init/atrace.rc:131 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_sync_file_exit/enable
system/init/atrace.rc:132 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_write_begin/enable
system/init/atrace.rc:133 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_write_begin/enable
system/init/atrace.rc:134 | late-init | chmod 0666 /sys/kernel/tracing/events/f2fs/f2fs_write_end/enable
system/init/atrace.rc:135 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/f2fs/f2fs_write_end/enable
system/init/atrace.rc:136 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_da_write_begin/enable
system/init/atrace.rc:137 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_da_write_begin/enable
system/init/atrace.rc:138 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_da_write_end/enable
system/init/atrace.rc:139 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_da_write_end/enable
system/init/atrace.rc:140 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_es_lookup_extent_enter/enable
system/init/atrace.rc:141 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_es_lookup_extent_enter/enable
system/init/atrace.rc:142 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_es_lookup_extent_exit/enable
system/init/atrace.rc:143 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_es_lookup_extent_exit/enable
system/init/atrace.rc:144 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_load_inode/enable
system/init/atrace.rc:145 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_load_inode/enable
system/init/atrace.rc:146 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_sync_file_enter/enable
system/init/atrace.rc:147 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_sync_file_enter/enable
system/init/atrace.rc:148 | late-init | chmod 0666 /sys/kernel/tracing/events/ext4/ext4_sync_file_exit/enable
system/init/atrace.rc:149 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/ext4/ext4_sync_file_exit/enable
system/init/atrace.rc:150 | late-init | chmod 0666 /sys/kernel/tracing/events/block/block_rq_issue/enable
system/init/atrace.rc:151 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/block/block_rq_issue/enable
system/init/atrace.rc:152 | late-init | chmod 0666 /sys/kernel/tracing/events/block/block_rq_complete/enable
system/init/atrace.rc:153 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/block/block_rq_complete/enable
system/init/atrace.rc:156 | late-init | chmod 0666 /sys/kernel/tracing/events/filemap/mm_filemap_add_to_page_cache/enable
system/init/atrace.rc:157 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/filemap/mm_filemap_add_to_page_cache/enable
system/init/atrace.rc:158 | late-init | chmod 0666 /sys/kernel/tracing/events/filemap/mm_filemap_delete_from_page_cache/enable
system/init/atrace.rc:159 | late-init | chmod 0666 /sys/kernel/debug/tracing/events/filemap/mm_filemap_delete_from_page_cache/enable
system/init/atrace.rc:162 | late-init | write /sys/kernel/debug/tracing/tracing_on 0
system/init/atrace.rc:163 | late-init | write /sys/kernel/tracing/tracing_on 0
system/init/atrace.rc:166 | late-init | chmod 0666 /sys/kernel/debug/tracing/trace
system/init/atrace.rc:167 | late-init | chmod 0666 /sys/kernel/tracing/trace
system/init/atrace.rc:172 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu0/trace
system/init/atrace.rc:173 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu0/trace
system/init/atrace.rc:174 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu1/trace
system/init/atrace.rc:175 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu1/trace
system/init/atrace.rc:176 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu2/trace
system/init/atrace.rc:177 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu2/trace
system/init/atrace.rc:178 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu3/trace
system/init/atrace.rc:179 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu3/trace
system/init/atrace.rc:180 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu4/trace
system/init/atrace.rc:181 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu4/trace
system/init/atrace.rc:182 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu5/trace
system/init/atrace.rc:183 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu5/trace
system/init/atrace.rc:184 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu6/trace
system/init/atrace.rc:185 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu6/trace
system/init/atrace.rc:186 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu7/trace
system/init/atrace.rc:187 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu7/trace
system/init/atrace.rc:188 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu8/trace
system/init/atrace.rc:189 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu8/trace
system/init/atrace.rc:190 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu9/trace
system/init/atrace.rc:191 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu9/trace
system/init/atrace.rc:192 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu10/trace
system/init/atrace.rc:193 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu10/trace
system/init/atrace.rc:194 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu11/trace
system/init/atrace.rc:195 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu11/trace
system/init/atrace.rc:196 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu12/trace
system/init/atrace.rc:197 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu12/trace
system/init/atrace.rc:198 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu13/trace
system/init/atrace.rc:199 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu13/trace
system/init/atrace.rc:200 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu14/trace
system/init/atrace.rc:201 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu14/trace
system/init/atrace.rc:202 | late-init | chmod 0666 /sys/kernel/debug/tracing/per_cpu/cpu15/trace
system/init/atrace.rc:203 | late-init | chmod 0666 /sys/kernel/tracing/per_cpu/cpu15/trace
system/init/atrace.rc:206 | property:persist.debug.atrace.boottrace=1 | start boottrace
system/init/audioserver.rc:16 | property:vold.decrypt=trigger_reset_main | restart audioserver
system/init/audioserver.rc:18 | property:vts.native_server.on=1 | stop audioserver
system/init/audioserver.rc:20 | property:vts.native_server.on=0 | start audioserver
system/init/audioserver.rc:23 | init | mkdir /dev/socket/audioserver 0775 audioserver audioserver
system/init/batterywarning.rc:9 | property:sys.boot_completed=1 | start batterywarning
system/init/bootstat.rc:7 | post-fs && property:ro.boot.bootreason=* | setprop sys.boot.reason ${ro.boot.bootreason}
system/init/bootstat.rc:10 | post-fs-data | mkdir /data/misc/bootstat 0700 system log
system/init/bootstat.rc:13 | post-fs-data | chown system log /data/misc/bootstat/absolute_boot_time
system/init/bootstat.rc:14 | post-fs-data | chown system log /data/misc/bootstat/boot_complete
system/init/bootstat.rc:15 | post-fs-data | chown system log /data/misc/bootstat/boot_complete_no_encryption
system/init/bootstat.rc:16 | post-fs-data | chown system log /data/misc/bootstat/boot_reason
system/init/bootstat.rc:17 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.1BLE
system/init/bootstat.rc:18 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.1BLL
system/init/bootstat.rc:19 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.2BLE
system/init/bootstat.rc:20 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.2BLL
system/init/bootstat.rc:21 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.AVB
system/init/bootstat.rc:22 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.KD
system/init/bootstat.rc:23 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.KL
system/init/bootstat.rc:24 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.ODT
system/init/bootstat.rc:25 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.SW
system/init/bootstat.rc:26 | post-fs-data | chown system log /data/misc/bootstat/boottime.bootloader.total
system/init/bootstat.rc:27 | post-fs-data | chown system log /data/misc/bootstat/build_date
system/init/bootstat.rc:28 | post-fs-data | chown system log /data/misc/bootstat/factory_reset
system/init/bootstat.rc:29 | post-fs-data | chown system log /data/misc/bootstat/factory_reset_boot_complete
system/init/bootstat.rc:30 | post-fs-data | chown system log /data/misc/bootstat/factory_reset_boot_complete_no_encryption
system/init/bootstat.rc:31 | post-fs-data | chown system log /data/misc/bootstat/factory_reset_current_time
system/init/bootstat.rc:32 | post-fs-data | chown system log /data/misc/bootstat/factory_reset_record_value
system/init/bootstat.rc:33 | post-fs-data | chown system log /data/misc/bootstat/last_boot_time_utc
system/init/bootstat.rc:34 | post-fs-data | chown system log /data/misc/bootstat/ota_boot_complete
system/init/bootstat.rc:35 | post-fs-data | chown system log /data/misc/bootstat/ota_boot_complete_no_encryption
system/init/bootstat.rc:36 | post-fs-data | chown system log /data/misc/bootstat/post_decrypt_time_elapsed
system/init/bootstat.rc:37 | post-fs-data | chown system log /data/misc/bootstat/ro.boottime.init
system/init/bootstat.rc:38 | post-fs-data | chown system log /data/misc/bootstat/ro.boottime.init.cold_boot_wait
system/init/bootstat.rc:39 | post-fs-data | chown system log /data/misc/bootstat/ro.boottime.init.selinux
system/init/bootstat.rc:40 | post-fs-data | chown system log /data/misc/bootstat/time_since_factory_reset
system/init/bootstat.rc:41 | post-fs-data | chown system log /data/misc/bootstat/time_since_last_boot
system/init/bootstat.rc:51 | post-fs-data && property:init.svc.bootanim=running && property:ro.crypto.type=block | exec_background - system log -- /system/bin/bootstat -r post_decrypt_time_elapsed
system/init/bootstat.rc:64 | boot | setprop sys.logbootcomplete 1
system/init/bootstat.rc:69 | property:init.svc.zygote=stopping | setprop sys.logbootcomplete 0
system/init/bootstat.rc:75 | zygote-start | exec_background - system log -- /system/bin/bootstat --set_system_boot_reason
system/init/bootstat.rc:83 | property:sys.boot_completed=1 && property:sys.logbootcomplete=1 | exec_background - system log -- /system/bin/bootstat --record_boot_complete --record_boot_reason --record_time_since_factory_reset -l
system/init/dumpstate.rc:3 | boot | chown root mount /sys/kernel/debug/mmc0/mmc0:0001/ext_csd
system/init/dumpstate.rc:4 | boot | chmod 0440 /sys/kernel/debug/mmc0/mmc0:0001/ext_csd
system/init/flags_health_check.rc:4 | load_persist_props_action | mkdir /data/server_configurable_flags 0775 system system
system/init/flags_health_check.rc:5 | load_persist_props_action | exec - system system -- /system/bin/flags_health_check BOOT_FAILURE
system/init/flags_health_check.rc:8 | property:sys.boot_completed=1 | setprop persist.device_config.attempted_boot_count 0
system/init/flags_health_check.rc:11 | property:ro.init.updatable_crashing=1 | exec - system system -- /system/bin/flags_health_check UPDATABLE_CRASHING
system/init/gsid.rc:7 | post-fs | mkdir /metadata/gsi 0771 root system
system/init/gsid.rc:8 | post-fs | mkdir /metadata/gsi/dsu 0771 root system
system/init/gsid.rc:11 | post-fs-data | mkdir /data/gsi 0700 root root
system/init/gsid.rc:12 | post-fs-data | mkdir /data/gsi/dsu 0700 root root
system/init/gsid.rc:15 | boot | exec_background - root root -- /system/bin/gsid run-startup-tasks
system/init/heapprofd.rc:30 | property:persist.heapprofd.enable=1 | start heapprofd
system/init/heapprofd.rc:33 | property:traced.lazy.heapprofd=1 | start heapprofd
system/init/heapprofd.rc:36 | property:persist.heapprofd.enable=0 && property:traced.lazy.heapprofd=0 | stop heapprofd
system/init/hw/vendor_init_as_system.rc:2 | post-fs-data | mark_post_data
system/init/hw/vendor_init_as_system.rc:5 | post-fs-data | start vold
system/init/hw/vendor_init_as_system.rc:6 | post-fs-data | exec - system system -- /system/bin/vdc checkpoint prepareCheckpoint
system/init/hw/vendor_init_as_system.rc:9 | post-fs-data | chown system system /data
system/init/hw/vendor_init_as_system.rc:10 | post-fs-data | chmod 0771 /data
system/init/hw/vendor_init_as_system.rc:12 | post-fs-data | restorecon /data
system/init/hw/vendor_init_as_system.rc:15 | post-fs-data | installkey /data
system/init/hw/vendor_init_as_system.rc:19 | post-fs-data | mkdir /data/bootchart 0755 shell shell
system/init/hw/vendor_init_as_system.rc:20 | post-fs-data | bootchart start
system/init/hw/vendor_init_as_system.rc:24 | post-fs-data | exec -- /system/bin/fsverity_init
system/init/hw/vendor_init_as_system.rc:27 | post-fs-data | enter_default_mount_ns
system/init/hw/vendor_init_as_system.rc:30 | post-fs-data | mkdir /data/apex 0750 root system
system/init/hw/vendor_init_as_system.rc:31 | post-fs-data | mkdir /data/apex/active 0750 root system
system/init/hw/vendor_init_as_system.rc:32 | post-fs-data | mkdir /data/apex/backup 0700 root system
system/init/hw/vendor_init_as_system.rc:33 | post-fs-data | mkdir /data/apex/sessions 0700 root system
system/init/hw/vendor_init_as_system.rc:34 | post-fs-data | mkdir /data/app-staging 0750 system system
system/init/hw/vendor_init_as_system.rc:35 | post-fs-data | start apexd
system/init/hw/vendor_init_as_system.rc:38 | post-fs-data | copy /data/system/entropy.dat /dev/urandom
system/init/hw/vendor_init_as_system.rc:41 | post-fs-data | mkdir /data/misc 01771 system misc
system/init/hw/vendor_init_as_system.rc:42 | post-fs-data | mkdir /data/misc/recovery 0770 system log
system/init/hw/vendor_init_as_system.rc:43 | post-fs-data | copy /data/misc/recovery/ro.build.fingerprint /data/misc/recovery/ro.build.fingerprint.1
system/init/hw/vendor_init_as_system.rc:44 | post-fs-data | chmod 0440 /data/misc/recovery/ro.build.fingerprint.1
system/init/hw/vendor_init_as_system.rc:45 | post-fs-data | chown system log /data/misc/recovery/ro.build.fingerprint.1
system/init/hw/vendor_init_as_system.rc:46 | post-fs-data | write /data/misc/recovery/ro.build.fingerprint ${ro.build.fingerprint}
system/init/hw/vendor_init_as_system.rc:47 | post-fs-data | chmod 0440 /data/misc/recovery/ro.build.fingerprint
system/init/hw/vendor_init_as_system.rc:48 | post-fs-data | chown system log /data/misc/recovery/ro.build.fingerprint
system/init/hw/vendor_init_as_system.rc:49 | post-fs-data | mkdir /data/misc/recovery/proc 0770 system log
system/init/hw/vendor_init_as_system.rc:50 | post-fs-data | copy /data/misc/recovery/proc/version /data/misc/recovery/proc/version.1
system/init/hw/vendor_init_as_system.rc:51 | post-fs-data | chmod 0440 /data/misc/recovery/proc/version.1
system/init/hw/vendor_init_as_system.rc:52 | post-fs-data | chown system log /data/misc/recovery/proc/version.1
system/init/hw/vendor_init_as_system.rc:53 | post-fs-data | copy /proc/version /data/misc/recovery/proc/version
system/init/hw/vendor_init_as_system.rc:54 | post-fs-data | chmod 0440 /data/misc/recovery/proc/version
system/init/hw/vendor_init_as_system.rc:55 | post-fs-data | chown system log /data/misc/recovery/proc/version
system/init/hw/vendor_init_as_system.rc:56 | post-fs-data | mkdir /data/misc/bluedroid 02770 bluetooth bluetooth
system/init/hw/vendor_init_as_system.rc:58 | post-fs-data | chmod 0660 /data/misc/bluedroid/bt_config.conf
system/init/hw/vendor_init_as_system.rc:59 | post-fs-data | chown bluetooth bluetooth /data/misc/bluedroid/bt_config.conf
system/init/hw/vendor_init_as_system.rc:60 | post-fs-data | mkdir /data/misc/bluetooth 0770 bluetooth bluetooth
system/init/hw/vendor_init_as_system.rc:61 | post-fs-data | mkdir /data/misc/bluetooth/logs 0770 bluetooth bluetooth
system/init/hw/vendor_init_as_system.rc:62 | post-fs-data | mkdir /data/misc/keystore 0700 keystore keystore
system/init/hw/vendor_init_as_system.rc:63 | post-fs-data | mkdir /data/misc/gatekeeper 0700 system system
system/init/hw/vendor_init_as_system.rc:64 | post-fs-data | mkdir /data/misc/keychain 0771 system system
system/init/hw/vendor_init_as_system.rc:65 | post-fs-data | mkdir /data/misc/net 0750 root shell
system/init/hw/vendor_init_as_system.rc:66 | post-fs-data | mkdir /data/misc/radio 0770 system radio
system/init/hw/vendor_init_as_system.rc:67 | post-fs-data | mkdir /data/misc/sms 0770 system radio
system/init/hw/vendor_init_as_system.rc:68 | post-fs-data | mkdir /data/misc/carrierid 0770 system radio
system/init/hw/vendor_init_as_system.rc:69 | post-fs-data | mkdir /data/misc/apns 0770 system radio
system/init/hw/vendor_init_as_system.rc:70 | post-fs-data | mkdir /data/misc/zoneinfo 0775 system system
system/init/hw/vendor_init_as_system.rc:71 | post-fs-data | mkdir /data/misc/network_watchlist 0774 system system
system/init/hw/vendor_init_as_system.rc:72 | post-fs-data | mkdir /data/misc/textclassifier 0771 system system
system/init/hw/vendor_init_as_system.rc:73 | post-fs-data | mkdir /data/misc/vpn 0770 system vpn
system/init/hw/vendor_init_as_system.rc:74 | post-fs-data | mkdir /data/misc/shared_relro 0771 shared_relro shared_relro
system/init/hw/vendor_init_as_system.rc:75 | post-fs-data | mkdir /data/misc/systemkeys 0700 system system
system/init/hw/vendor_init_as_system.rc:76 | post-fs-data | mkdir /data/misc/wifi 0770 wifi wifi
system/init/hw/vendor_init_as_system.rc:77 | post-fs-data | mkdir /data/misc/wifi/sockets 0770 wifi wifi
system/init/hw/vendor_init_as_system.rc:78 | post-fs-data | mkdir /data/misc/wifi/wpa_supplicant 0770 wifi wifi
system/init/hw/vendor_init_as_system.rc:79 | post-fs-data | mkdir /data/misc/ethernet 0770 system system
system/init/hw/vendor_init_as_system.rc:80 | post-fs-data | mkdir /data/misc/dhcp 0770 dhcp dhcp
system/init/hw/vendor_init_as_system.rc:81 | post-fs-data | mkdir /data/misc/user 0771 root root
system/init/hw/vendor_init_as_system.rc:82 | post-fs-data | mkdir /data/misc/perfprofd 0775 root root
system/init/hw/vendor_init_as_system.rc:84 | post-fs-data | chmod 0660 /data/misc/wifi/wpa_supplicant.conf
system/init/hw/vendor_init_as_system.rc:85 | post-fs-data | mkdir /data/local 0751 root root
system/init/hw/vendor_init_as_system.rc:86 | post-fs-data | mkdir /data/misc/media 0700 media media
system/init/hw/vendor_init_as_system.rc:87 | post-fs-data | mkdir /data/misc/audioserver 0700 audioserver audioserver
system/init/hw/vendor_init_as_system.rc:88 | post-fs-data | mkdir /data/misc/cameraserver 0700 cameraserver cameraserver
system/init/hw/vendor_init_as_system.rc:89 | post-fs-data | mkdir /data/misc/vold 0700 root root
system/init/hw/vendor_init_as_system.rc:90 | post-fs-data | mkdir /data/misc/boottrace 0771 system shell
system/init/hw/vendor_init_as_system.rc:91 | post-fs-data | mkdir /data/misc/update_engine 0700 root root
system/init/hw/vendor_init_as_system.rc:92 | post-fs-data | mkdir /data/misc/update_engine_log 02750 root log
system/init/hw/vendor_init_as_system.rc:93 | post-fs-data | mkdir /data/misc/trace 0700 root root
system/init/hw/vendor_init_as_system.rc:95 | post-fs-data | mkdir /data/misc/wmtrace 0700 system system
system/init/hw/vendor_init_as_system.rc:97 | post-fs-data | mkdir /data/misc/profiles 0771 system system
system/init/hw/vendor_init_as_system.rc:98 | post-fs-data | mkdir /data/misc/profiles/cur 0771 system system
system/init/hw/vendor_init_as_system.rc:99 | post-fs-data | mkdir /data/misc/profiles/ref 0771 system system
system/init/hw/vendor_init_as_system.rc:100 | post-fs-data | mkdir /data/misc/profman 0770 system shell
system/init/hw/vendor_init_as_system.rc:101 | post-fs-data | mkdir /data/misc/gcov 0770 root root
system/init/hw/vendor_init_as_system.rc:103 | post-fs-data | mkdir /data/preloads 0775 system system
system/init/hw/vendor_init_as_system.rc:105 | post-fs-data | mkdir /data/vendor 0771 root root
system/init/hw/vendor_init_as_system.rc:106 | post-fs-data | mkdir /data/vendor_ce 0771 root root
system/init/hw/vendor_init_as_system.rc:107 | post-fs-data | mkdir /data/vendor_de 0771 root root
system/init/hw/vendor_init_as_system.rc:108 | post-fs-data | mkdir /data/vendor/hardware 0771 root root
system/init/hw/vendor_init_as_system.rc:112 | post-fs-data | mkdir /data/local/tmp 0771 shell shell
system/init/hw/vendor_init_as_system.rc:113 | post-fs-data | mkdir /data/local/traces 0777 shell shell
system/init/hw/vendor_init_as_system.rc:114 | post-fs-data | mkdir /data/data 0771 system system
system/init/hw/vendor_init_as_system.rc:115 | post-fs-data | mkdir /data/app-private 0771 system system
system/init/hw/vendor_init_as_system.rc:116 | post-fs-data | mkdir /data/app-ephemeral 0771 system system
system/init/hw/vendor_init_as_system.rc:117 | post-fs-data | mkdir /data/app-asec 0700 root root
system/init/hw/vendor_init_as_system.rc:118 | post-fs-data | mkdir /data/app-lib 0771 system system
system/init/hw/vendor_init_as_system.rc:119 | post-fs-data | mkdir /data/app 0771 system system
system/init/hw/vendor_init_as_system.rc:120 | post-fs-data | mkdir /data/property 0700 root root
system/init/hw/vendor_init_as_system.rc:121 | post-fs-data | mkdir /data/tombstones 0771 system system
system/init/hw/vendor_init_as_system.rc:122 | post-fs-data | mkdir /data/vendor/tombstones 0771 root root
system/init/hw/vendor_init_as_system.rc:123 | post-fs-data | mkdir /data/vendor/tombstones/wifi 0771 wifi wifi
system/init/hw/vendor_init_as_system.rc:126 | post-fs-data | mkdir /data/dalvik-cache 0771 root root
system/init/hw/vendor_init_as_system.rc:128 | post-fs-data | mkdir /data/ota 0771 root root
system/init/hw/vendor_init_as_system.rc:132 | post-fs-data | mkdir /data/ota_package 0770 system cache
system/init/hw/vendor_init_as_system.rc:135 | post-fs-data | mkdir /data/resource-cache 0771 system system
system/init/hw/vendor_init_as_system.rc:136 | post-fs-data | chown system system /data/resource-cache
system/init/hw/vendor_init_as_system.rc:137 | post-fs-data | chmod 0771 /data/resource-cache
system/init/hw/vendor_init_as_system.rc:140 | post-fs-data | mkdir /data/lost+found 0770 root root
system/init/hw/vendor_init_as_system.rc:144 | post-fs-data | mkdir /data/drm 0770 drm drm
system/init/hw/vendor_init_as_system.rc:148 | post-fs-data | mkdir /data/mediadrm 0770 mediadrm mediadrm
system/init/hw/vendor_init_as_system.rc:150 | post-fs-data | mkdir /data/anr 0775 system system
system/init/hw/vendor_init_as_system.rc:153 | post-fs-data | mkdir /data/nfc 0770 nfc nfc
system/init/hw/vendor_init_as_system.rc:154 | post-fs-data | mkdir /data/nfc/param 0770 nfc nfc
system/init/hw/vendor_init_as_system.rc:158 | post-fs-data | mkdir /data/backup 0700 system system
system/init/hw/vendor_init_as_system.rc:159 | post-fs-data | mkdir /data/ss 0700 system system
system/init/hw/vendor_init_as_system.rc:161 | post-fs-data | mkdir /data/system 0775 system system
system/init/hw/vendor_init_as_system.rc:162 | post-fs-data | mkdir /data/system/dropbox 0700 system system
system/init/hw/vendor_init_as_system.rc:163 | post-fs-data | mkdir /data/system/heapdump 0700 system system
system/init/hw/vendor_init_as_system.rc:164 | post-fs-data | mkdir /data/system/users 0775 system system
system/init/hw/vendor_init_as_system.rc:166 | post-fs-data | mkdir /data/system_de 0770 system system
system/init/hw/vendor_init_as_system.rc:167 | post-fs-data | mkdir /data/system_ce 0770 system system
system/init/hw/vendor_init_as_system.rc:169 | post-fs-data | mkdir /data/misc_de 01771 system misc
system/init/hw/vendor_init_as_system.rc:170 | post-fs-data | mkdir /data/misc_ce 01771 system misc
system/init/hw/vendor_init_as_system.rc:172 | post-fs-data | mkdir /data/user 0711 system system
system/init/hw/vendor_init_as_system.rc:173 | post-fs-data | mkdir /data/user_de 0711 system system
system/init/hw/vendor_init_as_system.rc:174 | post-fs-data | symlink /data/data /data/user/0
system/init/hw/vendor_init_as_system.rc:176 | post-fs-data | mkdir /data/media 0770 media_rw media_rw
system/init/hw/vendor_init_as_system.rc:178 | post-fs-data | mkdir /data/cache 0770 system cache
system/init/hw/vendor_init_as_system.rc:179 | post-fs-data | mkdir /data/cache/recovery 0770 system cache
system/init/hw/vendor_init_as_system.rc:180 | post-fs-data | mkdir /data/cache/backup_stage 0700 system system
system/init/hw/vendor_init_as_system.rc:181 | post-fs-data | mkdir /data/cache/backup 0700 system system
system/init/hw/vendor_init_as_system.rc:184 | post-fs-data | wait_for_prop apexd.status ready
system/init/hw/vendor_init_as_system.rc:185 | post-fs-data | parse_apex_configs
system/init/hw/vendor_init_as_system.rc:187 | post-fs-data | init_user0
system/init/hw/vendor_init_as_system.rc:190 | post-fs-data | restorecon --recursive --skip-ce /data
system/init/hw/vendor_init_as_system.rc:193 | post-fs-data | exec - system system -- /system/bin/tzdatacheck /apex/com.android.runtime/etc/tz /data/misc/zoneinfo
system/init/hw/vendor_init_as_system.rc:199 | post-fs-data | setprop vold.post_fs_data_done 1
system/init/hw/vendor_init_as_system.rc:204 | post-fs-data | setprop sys.use_memfd false
system/init/hw/vendor_init_as_system.rc:207 | post-fs-data | chown root system /dev/fscklogs/log
system/init/hw/vendor_init_as_system.rc:208 | post-fs-data | chmod 0770 /dev/fscklogs/log
system/init/hw/vendor_init_as_system.rc:214 | post-fs-data | mkdir /data/vendor_de/meta 0770 root root
system/init/hw/vendor_init_as_system.rc:215 | post-fs-data | mkdir /data/vendor_de/meta/mddb 0770 root root
system/init/hw/vendor_init_as_system.rc:218 | post-fs-data | mkdir /data/mdlog 0770 system system
system/init/hw/vendor_init_as_system.rc:219 | post-fs-data | mkdir /data/connsyslog 0770 system system
system/init/incidentd.rc:23 | post-fs-data | mkdir /data/misc/incidents 0770 incidentd incidentd
system/init/init.connectivity.rc:12 | post-fs-data | mkdir /data/debuglogger 0775 system system
system/init/init.connectivity.rc:13 | post-fs-data | mkdir /data/debuglogger/connsyslog 0775 shell log
system/init/init.connectivity.rc:14 | post-fs-data | mkdir /data/debuglogger/connsyslog/bthci 0775 shell log
system/init/installd.rc:6 | early-boot | mkdir /config/sdcardfs/extensions/1055
system/init/installd.rc:7 | early-boot | mkdir /config/sdcardfs/extensions/1056
system/init/installd.rc:8 | early-boot | mkdir /config/sdcardfs/extensions/1057
system/init/installd.rc:9 | early-boot | mkdir /config/sdcardfs/extensions/1056/3gpp
system/init/installd.rc:10 | early-boot | mkdir /config/sdcardfs/extensions/1056/3gp
system/init/installd.rc:11 | early-boot | mkdir /config/sdcardfs/extensions/1056/3gpp2
system/init/installd.rc:12 | early-boot | mkdir /config/sdcardfs/extensions/1056/3g2
system/init/installd.rc:13 | early-boot | mkdir /config/sdcardfs/extensions/1056/avi
system/init/installd.rc:14 | early-boot | mkdir /config/sdcardfs/extensions/1056/dl
system/init/installd.rc:15 | early-boot | mkdir /config/sdcardfs/extensions/1056/dif
system/init/installd.rc:16 | early-boot | mkdir /config/sdcardfs/extensions/1056/dv
system/init/installd.rc:17 | early-boot | mkdir /config/sdcardfs/extensions/1056/fli
system/init/installd.rc:18 | early-boot | mkdir /config/sdcardfs/extensions/1056/m4v
system/init/installd.rc:19 | early-boot | mkdir /config/sdcardfs/extensions/1056/ts
system/init/installd.rc:20 | early-boot | mkdir /config/sdcardfs/extensions/1056/mpeg
system/init/installd.rc:21 | early-boot | mkdir /config/sdcardfs/extensions/1056/mpg
system/init/installd.rc:22 | early-boot | mkdir /config/sdcardfs/extensions/1056/mpe
system/init/installd.rc:23 | early-boot | mkdir /config/sdcardfs/extensions/1056/mp4
system/init/installd.rc:24 | early-boot | mkdir /config/sdcardfs/extensions/1056/vob
system/init/installd.rc:25 | early-boot | mkdir /config/sdcardfs/extensions/1056/qt
system/init/installd.rc:26 | early-boot | mkdir /config/sdcardfs/extensions/1056/mov
system/init/installd.rc:27 | early-boot | mkdir /config/sdcardfs/extensions/1056/mxu
system/init/installd.rc:28 | early-boot | mkdir /config/sdcardfs/extensions/1056/webm
system/init/installd.rc:29 | early-boot | mkdir /config/sdcardfs/extensions/1056/lsf
system/init/installd.rc:30 | early-boot | mkdir /config/sdcardfs/extensions/1056/lsx
system/init/installd.rc:31 | early-boot | mkdir /config/sdcardfs/extensions/1056/mkv
system/init/installd.rc:32 | early-boot | mkdir /config/sdcardfs/extensions/1056/mng
system/init/installd.rc:33 | early-boot | mkdir /config/sdcardfs/extensions/1056/asf
system/init/installd.rc:34 | early-boot | mkdir /config/sdcardfs/extensions/1056/asx
system/init/installd.rc:35 | early-boot | mkdir /config/sdcardfs/extensions/1056/wm
system/init/installd.rc:36 | early-boot | mkdir /config/sdcardfs/extensions/1056/wmv
system/init/installd.rc:37 | early-boot | mkdir /config/sdcardfs/extensions/1056/wmx
system/init/installd.rc:38 | early-boot | mkdir /config/sdcardfs/extensions/1056/wvx
system/init/installd.rc:39 | early-boot | mkdir /config/sdcardfs/extensions/1056/movie
system/init/installd.rc:40 | early-boot | mkdir /config/sdcardfs/extensions/1056/wrf
system/init/installd.rc:41 | early-boot | mkdir /config/sdcardfs/extensions/1057/bmp
system/init/installd.rc:42 | early-boot | mkdir /config/sdcardfs/extensions/1057/gif
system/init/installd.rc:43 | early-boot | mkdir /config/sdcardfs/extensions/1057/jpg
system/init/installd.rc:44 | early-boot | mkdir /config/sdcardfs/extensions/1057/jpeg
system/init/installd.rc:45 | early-boot | mkdir /config/sdcardfs/extensions/1057/jpe
system/init/installd.rc:46 | early-boot | mkdir /config/sdcardfs/extensions/1057/pcx
system/init/installd.rc:47 | early-boot | mkdir /config/sdcardfs/extensions/1057/png
system/init/installd.rc:48 | early-boot | mkdir /config/sdcardfs/extensions/1057/svg
system/init/installd.rc:49 | early-boot | mkdir /config/sdcardfs/extensions/1057/svgz
system/init/installd.rc:50 | early-boot | mkdir /config/sdcardfs/extensions/1057/tiff
system/init/installd.rc:51 | early-boot | mkdir /config/sdcardfs/extensions/1057/tif
system/init/installd.rc:52 | early-boot | mkdir /config/sdcardfs/extensions/1057/wbmp
system/init/installd.rc:53 | early-boot | mkdir /config/sdcardfs/extensions/1057/webp
system/init/installd.rc:54 | early-boot | mkdir /config/sdcardfs/extensions/1057/dng
system/init/installd.rc:55 | early-boot | mkdir /config/sdcardfs/extensions/1057/cr2
system/init/installd.rc:56 | early-boot | mkdir /config/sdcardfs/extensions/1057/ras
system/init/installd.rc:57 | early-boot | mkdir /config/sdcardfs/extensions/1057/art
system/init/installd.rc:58 | early-boot | mkdir /config/sdcardfs/extensions/1057/jng
system/init/installd.rc:59 | early-boot | mkdir /config/sdcardfs/extensions/1057/nef
system/init/installd.rc:60 | early-boot | mkdir /config/sdcardfs/extensions/1057/nrw
system/init/installd.rc:61 | early-boot | mkdir /config/sdcardfs/extensions/1057/orf
system/init/installd.rc:62 | early-boot | mkdir /config/sdcardfs/extensions/1057/rw2
system/init/installd.rc:63 | early-boot | mkdir /config/sdcardfs/extensions/1057/pef
system/init/installd.rc:64 | early-boot | mkdir /config/sdcardfs/extensions/1057/psd
system/init/installd.rc:65 | early-boot | mkdir /config/sdcardfs/extensions/1057/pnm
system/init/installd.rc:66 | early-boot | mkdir /config/sdcardfs/extensions/1057/pbm
system/init/installd.rc:67 | early-boot | mkdir /config/sdcardfs/extensions/1057/pgm
system/init/installd.rc:68 | early-boot | mkdir /config/sdcardfs/extensions/1057/ppm
system/init/installd.rc:69 | early-boot | mkdir /config/sdcardfs/extensions/1057/srw
system/init/installd.rc:70 | early-boot | mkdir /config/sdcardfs/extensions/1057/arw
system/init/installd.rc:71 | early-boot | mkdir /config/sdcardfs/extensions/1057/rgb
system/init/installd.rc:72 | early-boot | mkdir /config/sdcardfs/extensions/1057/xbm
system/init/installd.rc:73 | early-boot | mkdir /config/sdcardfs/extensions/1057/xpm
system/init/installd.rc:74 | early-boot | mkdir /config/sdcardfs/extensions/1057/xwd
system/init/installd.rc:75 | early-boot | mkdir /config/sdcardfs/extensions/1055/aac
system/init/installd.rc:76 | early-boot | mkdir /config/sdcardfs/extensions/1055/aac
system/init/installd.rc:77 | early-boot | mkdir /config/sdcardfs/extensions/1055/amr
system/init/installd.rc:78 | early-boot | mkdir /config/sdcardfs/extensions/1055/awb
system/init/installd.rc:79 | early-boot | mkdir /config/sdcardfs/extensions/1055/snd
system/init/installd.rc:80 | early-boot | mkdir /config/sdcardfs/extensions/1055/flac
system/init/installd.rc:81 | early-boot | mkdir /config/sdcardfs/extensions/1055/flac
system/init/installd.rc:82 | early-boot | mkdir /config/sdcardfs/extensions/1055/mp3
system/init/installd.rc:83 | early-boot | mkdir /config/sdcardfs/extensions/1055/mpga
system/init/installd.rc:84 | early-boot | mkdir /config/sdcardfs/extensions/1055/mpega
system/init/installd.rc:85 | early-boot | mkdir /config/sdcardfs/extensions/1055/mp2
system/init/installd.rc:86 | early-boot | mkdir /config/sdcardfs/extensions/1055/m4a
system/init/installd.rc:87 | early-boot | mkdir /config/sdcardfs/extensions/1055/aif
system/init/installd.rc:88 | early-boot | mkdir /config/sdcardfs/extensions/1055/aiff
system/init/installd.rc:89 | early-boot | mkdir /config/sdcardfs/extensions/1055/aifc
system/init/installd.rc:90 | early-boot | mkdir /config/sdcardfs/extensions/1055/gsm
system/init/installd.rc:91 | early-boot | mkdir /config/sdcardfs/extensions/1055/mka
system/init/installd.rc:92 | early-boot | mkdir /config/sdcardfs/extensions/1055/m3u
system/init/installd.rc:93 | early-boot | mkdir /config/sdcardfs/extensions/1055/wma
system/init/installd.rc:94 | early-boot | mkdir /config/sdcardfs/extensions/1055/wax
system/init/installd.rc:95 | early-boot | mkdir /config/sdcardfs/extensions/1055/ra
system/init/installd.rc:96 | early-boot | mkdir /config/sdcardfs/extensions/1055/rm
system/init/installd.rc:97 | early-boot | mkdir /config/sdcardfs/extensions/1055/ram
system/init/installd.rc:98 | early-boot | mkdir /config/sdcardfs/extensions/1055/ra
system/init/installd.rc:99 | early-boot | mkdir /config/sdcardfs/extensions/1055/pls
system/init/installd.rc:100 | early-boot | mkdir /config/sdcardfs/extensions/1055/sd2
system/init/installd.rc:101 | early-boot | mkdir /config/sdcardfs/extensions/1055/wav
system/init/installd.rc:102 | early-boot | mkdir /config/sdcardfs/extensions/1055/ogg
system/init/installd.rc:103 | early-boot | mkdir /config/sdcardfs/extensions/1055/oga
system/init/iorapd.rc:25 | property:ro.iorapd.enable=false | stop iorapd
system/init/iorapd.rc:29 | post-fs-data | mkdir /data/misc/iorapd/ 0770 iorapd iorapd
system/init/kpoc_charger.rc:2 | charger | start kpoc_charger
system/init/logd.rc:28 | fs | write /dev/event-log-tags "# content owned by logd
system/init/logd.rc:30 | fs | chown logd logd /dev/event-log-tags
system/init/logd.rc:31 | fs | chmod 0644 /dev/event-log-tags
system/init/logd.rc:34 | property:sys.boot_completed=1 | start logd-auditctl
system/init/lpdumpd.rc:32 | property:sys.lpdumpd=start | start lpdumpd
system/init/lpdumpd.rc:35 | property:sys.lpdumpd=stop | stop lpdumpd
system/init/mobile_log_d.rc:2 | post-fs-data | mkdir /data/log_temp 0755 system system
system/init/mobile_log_d.rc:4 | post-fs-data | mkdir /data/misc/mblog 0755 system system
system/init/mobile_log_d.rc:5 | post-fs-data | mkdir /data/debuglogger 0770 shell log
system/init/mobile_log_d.rc:31 | property:vendor.MB.control=copy | start mobile_log_d_contorl
system/init/mobile_log_d.rc:34 | property:vendor.MB.sublog_config=* | start mobile_log_d_sublog_config
system/init/mobile_log_d.rc:38 | property:vendor.MB.ftrace.log=mmedia | start mmedialogstart
system/init/mobile_log_d.rc:41 | property:vendor.MB.ftrace.log=bsp | start bsplogstart
system/init/mobile_log_d.rc:44 | property:vendor.MB.ftrace.log=off | start trace_buf_off
system/init/mobile_log_d.rc:48 | property:vendor.MB.prdebug=1 | write /sys/kernel/debug/dynamic_debug/control "file *mediatek* +p"
system/init/mobile_log_d.rc:49 | property:vendor.MB.prdebug=1 | write /sys/kernel/debug/dynamic_debug/control "file *gpu* -p"
system/init/mobile_log_d.rc:51 | property:vendor.MB.prdebug=0 | write /sys/kernel/debug/dynamic_debug/control "file *mediatek* -p"
system/init/netdiag.rc:5 | boot | chown root radio /proc/net/vlan/config
system/init/netdiag.rc:6 | boot | chmod 0640 /proc/net/vlan/config
system/init/netdiag.rc:7 | boot | chown root radio /proc/net/ip6_tables_matches
system/init/netdiag.rc:8 | boot | chown root radio /proc/net/ip6_tables_names
system/init/netdiag.rc:9 | boot | chown root radio /proc/net/ip6_tables_targets
system/init/netdiag.rc:10 | boot | chown root radio /proc/net/arp_tables_targets
system/init/netdiag.rc:11 | boot | chown root radio /proc/net/arp_tables_matches
system/init/netdiag.rc:12 | boot | chown root radio /proc/net/arp_tables_names
system/init/netdiag.rc:13 | boot | chown root radio /proc/net/ip_tables_targets
system/init/netdiag.rc:14 | boot | chown root radio /proc/net/ip_tables_matches
system/init/netdiag.rc:15 | boot | chown root radio /proc/net/ip_tables_names
system/init/netdiag.rc:16 | boot | chown root radio /proc/net/ip_conntrack_expect
system/init/netdiag.rc:17 | boot | chown root radio /proc/net/ip_conntrack
system/init/netdiag.rc:18 | boot | chown root radio /proc/net/nf_conntrack
system/init/netdiag.rc:19 | boot | chown root radio /proc/net/nf_conntrack_expect
system/init/netdiag.rc:20 | boot | chown root radio /proc/net/netfilter/nfnetlink_log
system/init/netdiag.rc:21 | boot | chown root radio /proc/net/netfilter/nfnetlink_queue
system/init/netdiag.rc:25 | post-fs-data | mkdir /data/debuglogger 0770 shell log
system/init/perfetto.rc:39 | property:persist.device_config.global_settings.sys_traced=1 | setprop persist.traced.enable 1
system/init/perfetto.rc:42 | property:persist.device_config.global_settings.sys_traced=0 | setprop persist.traced.enable 0
system/init/perfetto.rc:45 | property:debug.atrace.user_initiated=1 | stop traced_probes
system/init/perfetto.rc:48 | property:persist.traced.enable=1 && property:debug.atrace.user_initiated="" | start traced_probes
system/init/perfetto.rc:57 | property:persist.traced.enable=1 | mkdir /data/misc/perfetto-traces 0773 root shell
system/init/perfetto.rc:59 | property:persist.traced.enable=1 | start traced
system/init/perfetto.rc:60 | property:persist.traced.enable=1 | start traced_probes
system/init/perfetto.rc:63 | property:persist.traced.enable=0 | stop traced
system/init/perfetto.rc:64 | property:persist.traced.enable=0 | stop traced_probes
system/init/recovery-persist.rc:2 | post-fs-data | mkdir /data/misc/recovery 0770 system log
system/init/recovery-persist.rc:3 | post-fs-data | exec_background - system log -- /system/bin/recovery-persist
system/init/rss_hwm_reset.rc:25 | property:sys.rss_hwm_reset.on=1 | start rss_hwm_reset
system/init/rss_hwm_reset.rc:26 | property:sys.rss_hwm_reset.on=1 | setprop sys.rss_hwm_reset.on 0
system/init/statsd.rc:23 | property:ro.statsd.enable=false | stop statsd
system/init/statsd.rc:27 | post-fs-data | mkdir /data/misc/stats-data/ 0770 statsd system
system/init/statsd.rc:28 | post-fs-data | mkdir /data/misc/stats-service/ 0770 statsd system
system/init/statsd.rc:29 | post-fs-data | mkdir /data/misc/stats-active-metric/ 0770 statsd system
system/init/statsd.rc:30 | post-fs-data | mkdir /data/misc/train-info/ 0770 statsd system
system/init/vdc.rc:3 | defaultcrypto | exec - root -- /system/bin/vdc --wait cryptfs mountdefaultencrypted
system/init/vdc.rc:9 | encrypt | start surfaceflinger
system/init/vdc.rc:10 | encrypt | exec - root -- /system/bin/vdc --wait cryptfs enablecrypto
system/init/wifi-events.rc:18 | fs | setprop sys.wifitracing.started 0
system/init/wifi-events.rc:22 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | mkdir /sys/kernel/debug/tracing/instances/wifi 711
system/init/wifi-events.rc:23 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | restorecon_recursive /sys/kernel/debug/tracing/instances/wifi
system/init/wifi-events.rc:24 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/tracing_on 0
system/init/wifi-events.rc:25 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/buffer_size_kb 1
system/init/wifi-events.rc:26 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/trace_options disable_on_free
system/init/wifi-events.rc:32 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/cfg80211_gtk_rekey_notify/enable 1
system/init/wifi-events.rc:33 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_add_key/enable 1
system/init/wifi-events.rc:34 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_assoc/enable 1
system/init/wifi-events.rc:35 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_auth/enable 1
system/init/wifi-events.rc:36 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_connect/enable 1
system/init/wifi-events.rc:37 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_set_default_key/enable 1
system/init/wifi-events.rc:38 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_set_default_mgmt_key/enable 1
system/init/wifi-events.rc:39 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/cfg80211/rdev_set_rekey_data/enable 1
system/init/wifi-events.rc:46 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/net/filter name==${wifi.interface:-wlan0}
system/init/wifi-events.rc:47 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/net/net_dev_queue/enable 1
system/init/wifi-events.rc:48 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/net/net_dev_xmit/enable 1
system/init/wifi-events.rc:49 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/net/netif_rx/enable 1
system/init/wifi-events.rc:50 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | write /sys/kernel/debug/tracing/instances/wifi/events/net/netif_receive_skb/enable 1
system/init/wifi-events.rc:54 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chown system /sys/kernel/debug/tracing/instances/wifi/tracing_on
system/init/wifi-events.rc:55 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chown system /sys/kernel/debug/tracing/instances/wifi/free_buffer
system/init/wifi-events.rc:56 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chown system /sys/kernel/debug/tracing/instances/wifi/trace
system/init/wifi-events.rc:57 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chmod 200 /sys/kernel/debug/tracing/instances/wifi/tracing_on
system/init/wifi-events.rc:58 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chmod 400 /sys/kernel/debug/tracing/instances/wifi/free_buffer
system/init/wifi-events.rc:59 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | chmod 600 /sys/kernel/debug/tracing/instances/wifi/trace
system/init/wifi-events.rc:60 | property:sys.boot_completed=1 && property:sys.wifitracing.started=0 | setprop sys.wifitracing.started 1
system/init/wifi-events.rc:64 | property:sys.boot_completed=1 && property:wifi.interface=* && sys.wifitracing.started=1 | write /sys/kernel/debug/tracing/instances/wifi/events/net/filter name==${wifi.interface}
vendor/init/android.hardware.audio@5.0-service-mediatek.rc:2 | post-fs-data | mkdir /data/vendor/audiohal 0771 system audio
vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:9 | property:vts.native_server.on=1 && property:ro.build.type=userdebug | stop bluetooth-1-0
vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:11 | property:vts.native_server.on=1 && property:ro.build.type=eng | stop bluetooth-1-0
vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:13 | property:vts.native_server.on=0 && property:ro.build.type=userdebug | start bluetooth-1-0
vendor/init/android.hardware.bluetooth@1.0-service-mediatek.rc:15 | property:vts.native_server.on=0 && property:ro.build.type=eng | start bluetooth-1-0
vendor/init/android.hardware.drm@1.2-service.widevine.rc:2 | property:init.svc.mediadrm=running | mkdir /data/vendor/mediadrm 0770 media mediadrm
vendor/init/android.hardware.drm@1.2-service.widevine.rc:3 | property:init.svc.mediadrm=running | start vendor.move_data_sh
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:2 | boot | chown root system /sys/class/dual_role_usb/dual-role-type_c_port0/mode
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:3 | boot | chown root system /sys/class/dual_role_usb/dual-role-type_c_port0/power_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:4 | boot | chown root system /sys/class/dual_role_usb/dual-role-type_c_port0/data_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:5 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-type_c_port0/mode
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:6 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-type_c_port0/power_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:7 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-type_c_port0/data_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:9 | boot | chown root system /sys/class/dual_role_usb/dual-role-usb20/mode
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:10 | boot | chown root system /sys/class/dual_role_usb/dual-role-usb20/power_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:11 | boot | chown root system /sys/class/dual_role_usb/dual-role-usb20/data_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:12 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-usb20/mode
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:13 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-usb20/power_role
vendor/init/android.hardware.usb@1.1-service-mediatek.rc:14 | boot | chmod 664 /sys/class/dual_role_usb/dual-role-usb20/data_role
vendor/init/bootperf.rc:3 | early-init && property:ro.boot.boot_trace=1 | setprop debug.atrace.tags.enableflags 0x1fffffe
vendor/init/bootperf.rc:6 | early-init && property:ro.boot.boot_trace=1 | setprop debug.atrace.app_number 1
vendor/init/bootperf.rc:7 | early-init && property:ro.boot.boot_trace=1 | setprop debug.atrace.app_0 "com.android.systemui"
vendor/init/hostapd.android.rc:10 | post-fs-data | mkdir /data/vendor/wifi 0770 wifi wifi
vendor/init/hostapd.android.rc:11 | post-fs-data | mkdir /data/vendor/wifi/hostapd 0770 wifi wifi
vendor/init/hostapd.android.rc:12 | post-fs-data | mkdir /data/vendor/wifi/hostapd/sockets 0770 wifi wifi
vendor/init/hw/factory_init.connectivity.rc:7 | post-fs-data | chmod 0660 /dev/ttyMT2
vendor/init/hw/factory_init.connectivity.rc:8 | post-fs-data | chown system system /dev/ttyMT2
vendor/init/hw/factory_init.connectivity.rc:11 | post-fs-data | mkdir /data/vendor/gps 0770 gps system
vendor/init/hw/factory_init.connectivity.rc:12 | post-fs-data | chown gps gps /dev/stpgps
vendor/init/hw/factory_init.connectivity.rc:13 | post-fs-data | chmod 0660 /dev/stpgps
vendor/init/hw/factory_init.connectivity.rc:16 | post-fs-data | chmod 666 /dev/gps_emi
vendor/init/hw/factory_init.connectivity.rc:19 | post-fs-data | mkdir /data/misc/wifi 0770 wifi wifi
vendor/init/hw/factory_init.connectivity.rc:20 | post-fs-data | mkdir /data/misc/wifi/sockets 0770 wifi wifi
vendor/init/hw/factory_init.connectivity.rc:21 | post-fs-data | mkdir /data/misc/wpa_supplicant 0770 wifi wifi
vendor/init/hw/factory_init.connectivity.rc:70 | boot | insmod /vendor/lib/modules/wmt_drv.ko
vendor/init/hw/factory_init.connectivity.rc:74 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/bt_drv.ko
vendor/init/hw/factory_init.connectivity.rc:78 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wmt_chrdev_wifi.ko
vendor/init/hw/factory_init.connectivity.rc:79 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wlan_drv_${ro.vendor.wlan.gen}.ko
vendor/init/hw/factory_init.connectivity.rc:80 | property:vendor.connsys.driver.ready=yes | start wlan_assistant
vendor/init/hw/factory_init.connectivity.rc:84 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/fmradio_drv.ko
vendor/init/hw/factory_init.connectivity.rc:88 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/gps_drv.ko
vendor/init/hw/factory_init.project.rc:16 | post-fs | chmod 0660 /dev/spm
vendor/init/hw/factory_init.project.rc:17 | post-fs | chown system system /dev/spm
vendor/init/hw/factory_init.project.rc:27 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/enable 0
vendor/init/hw/factory_init.project.rc:28 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/idVendor ${sys.usb.vid}
vendor/init/hw/factory_init.project.rc:29 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/idProduct 2029
vendor/init/hw/factory_init.project.rc:30 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/f_acm/port_index 2,4
vendor/init/hw/factory_init.project.rc:31 | property:sys.usb.config=gs1gs3 | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/factory_init.project.rc:32 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/functions mass_storage,adb,acm
vendor/init/hw/factory_init.project.rc:33 | property:sys.usb.config=gs1gs3 | write /sys/class/android_usb/android0/enable 1
vendor/init/hw/factory_init.project.rc:34 | property:sys.usb.config=gs1gs3 | start adbd
vendor/init/hw/factory_init.project.rc:35 | property:sys.usb.config=gs1gs3 | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/factory_init.project.rc:36 | property:sys.usb.config=gs1gs3 | setprop sys.usb.acm_idx 2,4
vendor/init/hw/factory_init.rc:31 | early-init | write /proc/1/oom_score_adj -1000
vendor/init/hw/factory_init.rc:34 | early-init | write /proc/sys/kernel/sysrq 0
vendor/init/hw/factory_init.rc:37 | early-init | restorecon /adb_keys
vendor/init/hw/factory_init.rc:40 | early-init | restorecon /postinstall
vendor/init/hw/factory_init.rc:42 | early-init | start ueventd
vendor/init/hw/factory_init.rc:48 | early-init | exec_start apexd-bootstrap
vendor/init/hw/factory_init.rc:51 | early-init | mount debugfs debugfs /sys/kernel/debug
vendor/init/hw/factory_init.rc:52 | early-init | chmod 0755 /sys/kernel/debug
vendor/init/hw/factory_init.rc:59 | init | start servicemanager
vendor/init/hw/factory_init.rc:60 | init | start hwservicemanager
vendor/init/hw/factory_init.rc:61 | init | start hidl_memory
vendor/init/hw/factory_init.rc:64 | init | copy /proc/cmdline /dev/urandom
vendor/init/hw/factory_init.rc:65 | init | copy /default.prop /dev/urandom
vendor/init/hw/factory_init.rc:67 | init | write /proc/bootprof "INIT: on init start"
vendor/init/hw/factory_init.rc:70 | init | symlink /system/etc /etc
vendor/init/hw/factory_init.rc:71 | init | symlink /sys/kernel/debug /d
vendor/init/hw/factory_init.rc:74 | init | symlink /dev/block/platform/bootdevice/by-name/boot /dev/bootimg
vendor/init/hw/factory_init.rc:75 | init | symlink /dev/block/platform/bootdevice/by-name/recovery /dev/recovery
vendor/init/hw/factory_init.rc:76 | init | symlink /dev/block/platform/bootdevice/by-name/secro  /dev/sec_ro
vendor/init/hw/factory_init.rc:77 | init | symlink /dev/block/platform/bootdevice/by-name/kb /dev/kb
vendor/init/hw/factory_init.rc:78 | init | symlink /dev/block/platform/bootdevice/by-name/dkb /dev/dkb
vendor/init/hw/factory_init.rc:79 | init | symlink /dev/block/platform/bootdevice/by-name/seccfg /dev/seccfg
vendor/init/hw/factory_init.rc:80 | init | symlink /dev/block/platform/bootdevice/by-name/proinfo /dev/pro_info
vendor/init/hw/factory_init.rc:81 | init | symlink /dev/block/platform/bootdevice/by-name/nvram /dev/nvram
vendor/init/hw/factory_init.rc:82 | init | symlink /dev/block/platform/bootdevice/by-name/para /dev/misc
vendor/init/hw/factory_init.rc:83 | init | symlink /dev/block/platform/bootdevice/by-name/logo /dev/logo
vendor/init/hw/factory_init.rc:87 | init | symlink /system/vendor /vendor
vendor/init/hw/factory_init.rc:90 | init | mount cgroup none /acct cpuacct
vendor/init/hw/factory_init.rc:91 | init | mkdir /acct/uid
vendor/init/hw/factory_init.rc:94 | init | mkdir /dev/stune
vendor/init/hw/factory_init.rc:95 | init | mount cgroup none /dev/stune schedtune
vendor/init/hw/factory_init.rc:96 | init | mkdir /dev/stune/foreground
vendor/init/hw/factory_init.rc:97 | init | chown system system /dev/stune
vendor/init/hw/factory_init.rc:98 | init | chown system system /dev/stune/foreground
vendor/init/hw/factory_init.rc:99 | init | chown system system /dev/stune/tasks
vendor/init/hw/factory_init.rc:100 | init | chown system system /dev/stune/foreground/tasks
vendor/init/hw/factory_init.rc:101 | init | chmod 0664 /dev/stune/tasks
vendor/init/hw/factory_init.rc:102 | init | chmod 0664 /dev/stune/foreground/tasks
vendor/init/hw/factory_init.rc:104 | init | restorecon_recursive /mnt
vendor/init/hw/factory_init.rc:106 | init | symlink /sdcard /mnt/sdcard
vendor/init/hw/factory_init.rc:108 | init | mount configfs none /config
vendor/init/hw/factory_init.rc:109 | init | chmod 0775 /config/sdcardfs
vendor/init/hw/factory_init.rc:110 | init | chown system package_info /config/sdcardfs
vendor/init/hw/factory_init.rc:112 | init | mkdir /mnt/secure 0700 root root
vendor/init/hw/factory_init.rc:113 | init | mkdir /mnt/secure/asec 0700 root root
vendor/init/hw/factory_init.rc:114 | init | mkdir /mnt/asec 0755 root system
vendor/init/hw/factory_init.rc:115 | init | mkdir /mnt/obb 0755 root system
vendor/init/hw/factory_init.rc:116 | init | mkdir /mnt/media_rw 0750 root media_rw
vendor/init/hw/factory_init.rc:117 | init | mkdir /mnt/user 0755 root root
vendor/init/hw/factory_init.rc:118 | init | mkdir /mnt/user/0 0755 root root
vendor/init/hw/factory_init.rc:119 | init | mkdir /mnt/expand 0771 system system
vendor/init/hw/factory_init.rc:120 | init | mkdir /mnt/appfuse 0711 root root
vendor/init/hw/factory_init.rc:124 | init | chown system system /sys/power/autosleep
vendor/init/hw/factory_init.rc:125 | init | chown system system /sys/power/state
vendor/init/hw/factory_init.rc:126 | init | chown system system /sys/power/wakeup_count
vendor/init/hw/factory_init.rc:127 | init | chown radio wakelock /sys/power/wake_lock
vendor/init/hw/factory_init.rc:128 | init | chown radio wakelock /sys/power/wake_unlock
vendor/init/hw/factory_init.rc:129 | init | chmod 0660 /sys/power/state
vendor/init/hw/factory_init.rc:130 | init | chmod 0660 /sys/power/wake_lock
vendor/init/hw/factory_init.rc:131 | init | chmod 0660 /sys/power/wake_unlock
vendor/init/hw/factory_init.rc:132 | init | chmod 0660 /sys/power/wakeup_count
vendor/init/hw/factory_init.rc:135 | init | mkdir /mnt/runtime 0700 root root
vendor/init/hw/factory_init.rc:136 | init | mkdir /mnt/runtime/default 0755 root root
vendor/init/hw/factory_init.rc:137 | init | mkdir /mnt/runtime/default/self 0755 root root
vendor/init/hw/factory_init.rc:138 | init | mkdir /mnt/runtime/read 0755 root root
vendor/init/hw/factory_init.rc:139 | init | mkdir /mnt/runtime/read/self 0755 root root
vendor/init/hw/factory_init.rc:140 | init | mkdir /mnt/runtime/write 0755 root root
vendor/init/hw/factory_init.rc:141 | init | mkdir /mnt/runtime/write/self 0755 root root
vendor/init/hw/factory_init.rc:144 | init | symlink /storage/self/primary /sdcard
vendor/init/hw/factory_init.rc:145 | init | symlink /storage/self/primary /mnt/sdcard
vendor/init/hw/factory_init.rc:146 | init | symlink /mnt/user/0/primary /mnt/runtime/default/self/primary
vendor/init/hw/factory_init.rc:149 | init | mkdir /dev/memcg 0700 root system
vendor/init/hw/factory_init.rc:150 | init | mount cgroup none /dev/memcg memory
vendor/init/hw/factory_init.rc:152 | init | mkdir /dev/memcg/apps/ 0755 system system
vendor/init/hw/factory_init.rc:154 | init | write /proc/sys/kernel/panic_on_oops 1
vendor/init/hw/factory_init.rc:155 | init | write /proc/sys/kernel/hung_task_timeout_secs 0
vendor/init/hw/factory_init.rc:156 | init | write /proc/cpu/alignment 4
vendor/init/hw/factory_init.rc:162 | init | write /proc/sys/kernel/sched_tunable_scaling 0
vendor/init/hw/factory_init.rc:163 | init | write /proc/sys/kernel/sched_latency_ns 10000000
vendor/init/hw/factory_init.rc:164 | init | write /proc/sys/kernel/sched_wakeup_granularity_ns 2000000
vendor/init/hw/factory_init.rc:165 | init | write /proc/sys/kernel/sched_child_runs_first 0
vendor/init/hw/factory_init.rc:166 | init | write /proc/sys/kernel/randomize_va_space 2
vendor/init/hw/factory_init.rc:167 | init | write /proc/sys/vm/mmap_min_addr 32768
vendor/init/hw/factory_init.rc:168 | init | write /proc/sys/net/ipv4/ping_group_range "0 2147483647"
vendor/init/hw/factory_init.rc:169 | init | write /proc/sys/net/unix/max_dgram_qlen 600
vendor/init/hw/factory_init.rc:170 | init | write /proc/sys/kernel/sched_rt_runtime_us 950000
vendor/init/hw/factory_init.rc:171 | init | write /proc/sys/kernel/sched_rt_period_us 1000000
vendor/init/hw/factory_init.rc:175 | init | write /proc/sys/net/core/rmem_max  262144
vendor/init/hw/factory_init.rc:176 | init | write /proc/sys/net/core/wmem_max  262144
vendor/init/hw/factory_init.rc:178 | init | write /proc/sys/net/ipv4/fwmark_reflect 1
vendor/init/hw/factory_init.rc:179 | init | write /proc/sys/net/ipv6/fwmark_reflect 1
vendor/init/hw/factory_init.rc:181 | init | write /proc/sys/net/ipv4/tcp_fwmark_accept 1
vendor/init/hw/factory_init.rc:183 | init | write /proc/sys/net/ipv4/conf/all/accept_redirects 0
vendor/init/hw/factory_init.rc:184 | init | write /proc/sys/net/ipv6/conf/all/accept_redirects 0
vendor/init/hw/factory_init.rc:186 | init | mkdir /mnt/cd-rom 0000 system system
vendor/init/hw/factory_init.rc:189 | init | write /proc/sys/abi/swp 1
vendor/init/hw/factory_init.rc:192 | init | symlink /proc/self/fd /dev/fd
vendor/init/hw/factory_init.rc:194 | init | setrlimit 13 40 40
vendor/init/hw/factory_init.rc:198 | property:sys.boot_from_charger_mode=1 | class_stop charger
vendor/init/hw/factory_init.rc:199 | property:sys.boot_from_charger_mode=1 | trigger late-init
vendor/init/hw/factory_init.rc:203 | load_system_props_action | load_system_props
vendor/init/hw/factory_init.rc:206 | load_persist_props_action | load_persist_props
vendor/init/hw/factory_init.rc:207 | load_persist_props_action | start logd
vendor/init/hw/factory_init.rc:208 | load_persist_props_action | start logd-reinit
vendor/init/hw/factory_init.rc:212 | firmware_mounts_complete | rm /dev/.booting
vendor/init/hw/factory_init.rc:216 | late-init | trigger early-fs
vendor/init/hw/factory_init.rc:217 | late-init | trigger fs
vendor/init/hw/factory_init.rc:218 | late-init | trigger post-fs
vendor/init/hw/factory_init.rc:223 | late-init | trigger load_system_props_action
vendor/init/hw/factory_init.rc:230 | late-init | trigger late-fs
vendor/init/hw/factory_init.rc:234 | late-init | trigger post-fs-data
vendor/init/hw/factory_init.rc:235 | late-init | trigger load_persist_props_action
vendor/init/hw/factory_init.rc:237 | late-init | trigger firmware_mounts_complete
vendor/init/hw/factory_init.rc:239 | late-init | trigger early-boot
vendor/init/hw/factory_init.rc:240 | late-init | trigger boot
vendor/init/hw/factory_init.rc:244 | early-fs | start vold
vendor/init/hw/factory_init.rc:249 | fs | mkdir /dev/usb-ffs 0770 shell shell
vendor/init/hw/factory_init.rc:250 | fs | mkdir /dev/usb-ffs/adb 0770 shell shell
vendor/init/hw/factory_init.rc:251 | fs | mkdir /config/usb_gadget/g1 0770 shell shell
vendor/init/hw/factory_init.rc:252 | fs | write /config/usb_gadget/g1/idVendor 0x0E8D
vendor/init/hw/factory_init.rc:253 | fs | write /config/usb_gadget/g1/bcdDevice 0xFFFF
vendor/init/hw/factory_init.rc:254 | fs | write /config/usb_gadget/g1/bcdUSB 0x0200
vendor/init/hw/factory_init.rc:255 | fs | mkdir /config/usb_gadget/g1/strings/0x409 0770
vendor/init/hw/factory_init.rc:256 | fs | write /config/usb_gadget/g1/strings/0x409/manufacturer "Android"
vendor/init/hw/factory_init.rc:257 | fs | write /config/usb_gadget/g1/strings/0x409/product "Android"
vendor/init/hw/factory_init.rc:258 | fs | write /config/usb_gadget/g1/bDeviceClass 0
vendor/init/hw/factory_init.rc:259 | fs | write /config/usb_gadget/g1/bDeviceSubClass 0
vendor/init/hw/factory_init.rc:260 | fs | write /config/usb_gadget/g1/bDeviceProtocol 0
vendor/init/hw/factory_init.rc:261 | fs | mkdir /config/usb_gadget/g1/functions/mass_storage.usb0
vendor/init/hw/factory_init.rc:262 | fs | mkdir /config/usb_gadget/g1/functions/ffs.adb
vendor/init/hw/factory_init.rc:263 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs0
vendor/init/hw/factory_init.rc:264 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs1
vendor/init/hw/factory_init.rc:265 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs2
vendor/init/hw/factory_init.rc:266 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs3
vendor/init/hw/factory_init.rc:267 | fs | mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell
vendor/init/hw/factory_init.rc:268 | fs | mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell
vendor/init/hw/factory_init.rc:269 | fs | write /config/usb_gadget/g1/configs/b.1/MaxPower 500
vendor/init/hw/factory_init.rc:270 | fs | symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1
vendor/init/hw/factory_init.rc:271 | fs | mount functionfs adb /dev/usb-ffs/adb uid=2000,gid=2000
vendor/init/hw/factory_init.rc:274 | fs | write /proc/bootprof "INIT:Mount_START"
vendor/init/hw/factory_init.rc:275 | fs | wait_for_prop hwservicemanager.ready "true"
vendor/init/hw/factory_init.rc:276 | fs | start vendor.boot-hal-1-0
vendor/init/hw/factory_init.rc:277 | fs | mount_all /vendor/etc/fstab.mt6765 ${ro.vendor.rc}multi_init.rc
vendor/init/hw/factory_init.rc:278 | fs | write /proc/bootprof "INIT:Mount_END"
vendor/init/hw/factory_init.rc:283 | post-fs | start logd
vendor/init/hw/factory_init.rc:284 | post-fs | start nfc_hal_service
vendor/init/hw/factory_init.rc:285 | post-fs | start light-hal-2-0
vendor/init/hw/factory_init.rc:286 | post-fs | start merged_hal_service
vendor/init/hw/factory_init.rc:287 | post-fs | start capi-2-0
vendor/init/hw/factory_init.rc:288 | post-fs | start power-hal-1-0
vendor/init/hw/factory_init.rc:290 | post-fs | mount rootfs rootfs / ro remount
vendor/init/hw/factory_init.rc:292 | post-fs | mount rootfs rootfs / shared rec
vendor/init/hw/factory_init.rc:294 | post-fs | mount none /mnt/runtime/default /storage slave bind rec
vendor/init/hw/factory_init.rc:297 | post-fs | restorecon_recursive /sys/kernel/debug
vendor/init/hw/factory_init.rc:300 | post-fs | symlink /sdcard /storage/sdcard0
vendor/init/hw/factory_init.rc:302 | post-fs | chown system system /system/data
vendor/init/hw/factory_init.rc:303 | post-fs | mkdir /system/cache 0770 system cache
vendor/init/hw/factory_init.rc:305 | post-fs | restorecon_recursive /cache
vendor/init/hw/factory_init.rc:308 | post-fs | chown system cache /cache
vendor/init/hw/factory_init.rc:309 | post-fs | chmod 0770 /cache
vendor/init/hw/factory_init.rc:312 | post-fs | chown system cache /cache/recovery
vendor/init/hw/factory_init.rc:313 | post-fs | chmod 0770 /cache/recovery
vendor/init/hw/factory_init.rc:316 | post-fs | chown root log /proc/vmallocinfo
vendor/init/hw/factory_init.rc:317 | post-fs | chmod 0440 /proc/vmallocinfo
vendor/init/hw/factory_init.rc:320 | post-fs | chown root system /proc/kmsg
vendor/init/hw/factory_init.rc:321 | post-fs | chmod 0440 /proc/kmsg
vendor/init/hw/factory_init.rc:322 | post-fs | chown root system /proc/sysrq-trigger
vendor/init/hw/factory_init.rc:323 | post-fs | chmod 0220 /proc/sysrq-trigger
vendor/init/hw/factory_init.rc:324 | post-fs | chown system log /proc/last_kmsg
vendor/init/hw/factory_init.rc:325 | post-fs | chmod 0440 /proc/last_kmsg
vendor/init/hw/factory_init.rc:328 | post-fs | chmod 0444 /sys/fs/selinux/policy
vendor/init/hw/factory_init.rc:331 | post-fs | mkdir /cache/lost+found 0770 root root
vendor/init/hw/factory_init.rc:334 | post-fs | mkdir /cache/recovery 0770 system system
vendor/init/hw/factory_init.rc:335 | post-fs | chown system system /mnt/vendor/protect_f
vendor/init/hw/factory_init.rc:336 | post-fs | chmod 0770 /mnt/vendor/protect_f
vendor/init/hw/factory_init.rc:338 | post-fs | chown system system /mnt/vendor/protect_s
vendor/init/hw/factory_init.rc:339 | post-fs | chmod 0770 /mnt/vendor/protect_s
vendor/init/hw/factory_init.rc:341 | post-fs | chown system system /mnt/vendor/nvcfg
vendor/init/hw/factory_init.rc:342 | post-fs | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/factory_init.rc:343 | post-fs | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/factory_init.rc:345 | post-fs | chown root system /mnt/vendor/nvdata
vendor/init/hw/factory_init.rc:346 | post-fs | chmod 0771 /mnt/vendor/nvdata
vendor/init/hw/factory_init.rc:347 | post-fs | mkdir /mnt/vendor/nvdata/media 0771 media audio
vendor/init/hw/factory_init.rc:348 | post-fs | restorecon_recursive /mnt/vendor/nvdata
vendor/init/hw/factory_init.rc:355 | late-fs | class_start early_hal
vendor/init/hw/factory_init.rc:357 | late-fs | start vendor.keymaster-3-0
vendor/init/hw/factory_init.rc:358 | late-fs | start vendor.keymaster-3-0-trustonic
vendor/init/hw/factory_init.rc:359 | late-fs | start vendor.keymaster-4-0
vendor/init/hw/factory_init.rc:360 | late-fs | start vendor.keymaster-4-0-trustonic
vendor/init/hw/factory_init.rc:361 | late-fs | start vendor.keymaster-4-0-beanpod
vendor/init/hw/factory_init.rc:362 | late-fs | start vendor.keymaster-4-0-trustkernel
vendor/init/hw/factory_init.rc:363 | late-fs | start keymaster_attestation-1-1
vendor/init/hw/factory_init.rc:366 | post-fs-data | write /proc/bootprof "INIT:post-fs-data"
vendor/init/hw/factory_init.rc:372 | post-fs-data | start debuggerd
vendor/init/hw/factory_init.rc:373 | post-fs-data | start debuggerd64
vendor/init/hw/factory_init.rc:376 | post-fs-data | rm /data/bugreports
vendor/init/hw/factory_init.rc:377 | post-fs-data | symlink /data/user_de/0/com.android.shell/files/bugreports /data/bugreports
vendor/init/hw/factory_init.rc:381 | post-fs-data | mkdir /mnt/vendor/nvdata/md_via 0770 root nvram
vendor/init/hw/factory_init.rc:382 | post-fs-data | chmod 0660 /dev/ttyMT4
vendor/init/hw/factory_init.rc:383 | post-fs-data | chown radio radio /dev/ttyMT4
vendor/init/hw/factory_init.rc:384 | post-fs-data | chmod 0660 /dev/vmodem
vendor/init/hw/factory_init.rc:385 | post-fs-data | chown radio radio /dev/vmodem
vendor/init/hw/factory_init.rc:391 | post-fs-data | chown root system /dev/md32
vendor/init/hw/factory_init.rc:392 | post-fs-data | chmod 0440 /dev/md32
vendor/init/hw/factory_init.rc:393 | post-fs-data | chown root system /sys/class/misc/md32/md32_mobile_log
vendor/init/hw/factory_init.rc:394 | post-fs-data | chmod 0660 /sys/class/misc/md32/md32_mobile_log
vendor/init/hw/factory_init.rc:395 | post-fs-data | write /sys/class/misc/md32/md32_boot 1
vendor/init/hw/factory_init.rc:398 | post-fs-data | chmod 0440 /dev/devmap
vendor/init/hw/factory_init.rc:399 | post-fs-data | chown root system /dev/devmap
vendor/init/hw/factory_init.rc:404 | post-fs-data | exec /vendor/etc/partition_permission.sh
vendor/init/hw/factory_init.rc:408 | post-fs-data | restorecon /data/ccci_cfg
vendor/init/hw/factory_init.rc:409 | post-fs-data | restorecon_recursive /mnt/vendor/protect_f
vendor/init/hw/factory_init.rc:410 | post-fs-data | restorecon_recursive /mnt/vendor/protect_s
vendor/init/hw/factory_init.rc:416 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_calmat
vendor/init/hw/factory_init.rc:417 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time
vendor/init/hw/factory_init.rc:418 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log
vendor/init/hw/factory_init.rc:419 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log_to_fs
vendor/init/hw/factory_init.rc:422 | post-fs-data | mkdir /data/misc/rtc 0770 system system
vendor/init/hw/factory_init.rc:425 | post-fs-data | mkdir /mnt/vendor/nvdata/md  0770 root system
vendor/init/hw/factory_init.rc:426 | post-fs-data | mkdir /mnt/vendor/nvdata/md2 0770 root system
vendor/init/hw/factory_init.rc:427 | post-fs-data | mkdir /mnt/vendor/nvdata/md3 0770 root system
vendor/init/hw/factory_init.rc:430 | post-fs-data | chown radio radio /sys/kernel/ccci/boot
vendor/init/hw/factory_init.rc:433 | post-fs-data | setrlimit 8 -1 -1
vendor/init/hw/factory_init.rc:436 | post-fs-data | chmod 0666 /dev/hwmsensor
vendor/init/hw/factory_init.rc:437 | post-fs-data | chmod 0666 /dev/gyroscope
vendor/init/hw/factory_init.rc:438 | post-fs-data | chmod 0666 /dev/msensor
vendor/init/hw/factory_init.rc:439 | post-fs-data | chmod 0666 /dev/gsensor
vendor/init/hw/factory_init.rc:440 | post-fs-data | chmod 0666 /dev/als_ps
vendor/init/hw/factory_init.rc:441 | post-fs-data | chmod 0666 /dev/humidity
vendor/init/hw/factory_init.rc:442 | post-fs-data | chmod 0666 /dev/barometer
vendor/init/hw/factory_init.rc:444 | post-fs-data | chown system system /dev/hwmsensor
vendor/init/hw/factory_init.rc:445 | post-fs-data | chown system system /dev/msensor
vendor/init/hw/factory_init.rc:446 | post-fs-data | chown system system /dev/gsensor
vendor/init/hw/factory_init.rc:447 | post-fs-data | chown radio system /dev/als_ps
vendor/init/hw/factory_init.rc:448 | post-fs-data | chown system system /dev/gyroscope
vendor/init/hw/factory_init.rc:449 | post-fs-data | chown system system /dev/barometer
vendor/init/hw/factory_init.rc:450 | post-fs-data | chown system system /dev/humidity
vendor/init/hw/factory_init.rc:452 | post-fs-data | chmod 0666 /dev/Vcodec
vendor/init/hw/factory_init.rc:458 | post-fs-data | chmod 0666 /dev/mtgpio
vendor/init/hw/factory_init.rc:460 | post-fs-data | chmod 0666 /dev/exm0
vendor/init/hw/factory_init.rc:463 | post-fs-data | rm /data/mtknfc_server
vendor/init/hw/factory_init.rc:472 | post-fs-data | setprop selinux.reload_policy 1
vendor/init/hw/factory_init.rc:478 | zygote-start && property:ro.crypto.state=unencrypted | exec_start update_verifier_nonencrypted
vendor/init/hw/factory_init.rc:479 | zygote-start && property:ro.crypto.state=unencrypted | start netd
vendor/init/hw/factory_init.rc:480 | zygote-start && property:ro.crypto.state=unencrypted | start zygote
vendor/init/hw/factory_init.rc:481 | zygote-start && property:ro.crypto.state=unencrypted | start zygote_secondary
vendor/init/hw/factory_init.rc:485 | zygote-start && property:ro.crypto.state=unsupported | exec_start update_verifier_nonencrypted
vendor/init/hw/factory_init.rc:486 | zygote-start && property:ro.crypto.state=unsupported | start netd
vendor/init/hw/factory_init.rc:487 | zygote-start && property:ro.crypto.state=unsupported | start zygote
vendor/init/hw/factory_init.rc:488 | zygote-start && property:ro.crypto.state=unsupported | start zygote_secondary
vendor/init/hw/factory_init.rc:492 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | exec_start update_verifier_nonencrypted
vendor/init/hw/factory_init.rc:493 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start netd
vendor/init/hw/factory_init.rc:494 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote
vendor/init/hw/factory_init.rc:495 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote_secondary
vendor/init/hw/factory_init.rc:498 | boot | setprop sys.usb.configfs 1
vendor/init/hw/factory_init.rc:499 | boot | setprop sys.usb.ffs.aio_compat 1
vendor/init/hw/factory_init.rc:500 | boot | setprop vendor.usb.clear boot
vendor/init/hw/factory_init.rc:503 | boot | chmod 0660 /dev/ttyGS0
vendor/init/hw/factory_init.rc:504 | boot | chown system radio /dev/ttyGS0
vendor/init/hw/factory_init.rc:505 | boot | chmod 0660 /dev/ttyGS1
vendor/init/hw/factory_init.rc:506 | boot | chown system radio /dev/ttyGS1
vendor/init/hw/factory_init.rc:508 | boot | chown bluetooth radio /dev/ttyGS2
vendor/init/hw/factory_init.rc:509 | boot | chmod 0660 /dev/ttyGS2
vendor/init/hw/factory_init.rc:511 | boot | chown system radio /dev/ttyGS3
vendor/init/hw/factory_init.rc:512 | boot | chmod 0660 /dev/ttyGS3
vendor/init/hw/factory_init.rc:515 | boot | setprop viatel.device.asci uart.4.ttyMT
vendor/init/hw/factory_init.rc:516 | boot | setprop viatel.device.fls  sdio.3.ttySDIO
vendor/init/hw/factory_init.rc:517 | boot | setprop viatel.device.at   sdio.4.ttySDIO
vendor/init/hw/factory_init.rc:518 | boot | setprop viatel.device.data sdio.1.ttySDIO
vendor/init/hw/factory_init.rc:521 | boot | chmod 0664 /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/factory_init.rc:522 | boot | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/factory_init.rc:525 | boot | ifup lo
vendor/init/hw/factory_init.rc:526 | boot | hostname localhost
vendor/init/hw/factory_init.rc:527 | boot | domainname localdomain
vendor/init/hw/factory_init.rc:531 | boot | class_start default
vendor/init/hw/factory_init.rc:535 | boot | class_start hal
vendor/init/hw/factory_init.rc:537 | boot | class_start core
vendor/init/hw/factory_init.rc:540 | nonencrypted | class_start main
vendor/init/hw/factory_init.rc:541 | nonencrypted | class_start late_start
vendor/init/hw/factory_init.rc:544 | property:vold.decrypt=trigger_default_encryption | start defaultcrypto
vendor/init/hw/factory_init.rc:547 | property:vold.decrypt=trigger_encryption | start surfaceflinger
vendor/init/hw/factory_init.rc:548 | property:vold.decrypt=trigger_encryption | start encrypt
vendor/init/hw/factory_init.rc:551 | property:sys.init_log_level=* | loglevel ${sys.init_log_level}
vendor/init/hw/factory_init.rc:554 | property:vold.decrypt=trigger_reset_main | class_reset main
vendor/init/hw/factory_init.rc:557 | property:vold.decrypt=trigger_load_persist_props | load_persist_props
vendor/init/hw/factory_init.rc:558 | property:vold.decrypt=trigger_load_persist_props | start logd
vendor/init/hw/factory_init.rc:559 | property:vold.decrypt=trigger_load_persist_props | start logd-reinit
vendor/init/hw/factory_init.rc:562 | property:vold.decrypt=trigger_post_fs_data | trigger post-fs-data
vendor/init/hw/factory_init.rc:563 | property:vold.decrypt=trigger_post_fs_data | trigger zygote-start
vendor/init/hw/factory_init.rc:567 | property:vold.decrypt=trigger_restart_min_framework | exec_start update_verifier
vendor/init/hw/factory_init.rc:568 | property:vold.decrypt=trigger_restart_min_framework | class_start main
vendor/init/hw/factory_init.rc:571 | property:vold.decrypt=trigger_restart_framework | stop surfaceflinger
vendor/init/hw/factory_init.rc:572 | property:vold.decrypt=trigger_restart_framework | start surfaceflinger
vendor/init/hw/factory_init.rc:574 | property:vold.decrypt=trigger_restart_framework | exec_start update_verifier
vendor/init/hw/factory_init.rc:575 | property:vold.decrypt=trigger_restart_framework | class_start main
vendor/init/hw/factory_init.rc:576 | property:vold.decrypt=trigger_restart_framework | class_start late_start
vendor/init/hw/factory_init.rc:579 | property:vold.decrypt=trigger_shutdown_framework | class_reset late_start
vendor/init/hw/factory_init.rc:580 | property:vold.decrypt=trigger_shutdown_framework | class_reset main
vendor/init/hw/factory_init.rc:584 | defaultcrypto | exec - root -- /system/bin/vdc --wait cryptfs mountdefaultencrypted
vendor/init/hw/factory_init.rc:593 | encrypt && property:ro.vendor.mtk_f2fs_enable=0 | mount ext4 /dev/block/platform/bootdevice/by-name/userdata /data noatime nosuid nodev noauto_da_alloc discard wait
vendor/init/hw/factory_init.rc:596 | encrypt && property:ro.vendor.mtk_f2fs_enable=1 | mount f2fs /dev/block/platform/bootdevice/by-name/userdata /data noatime nosuid nodev discard wait
vendor/init/hw/factory_init.rc:599 | encrypt | setprop vendor.crypto.fake_encrypt 1
vendor/init/hw/factory_init.rc:600 | encrypt | setprop vold.post_fs_data_done 0
vendor/init/hw/factory_init.rc:601 | encrypt | trigger post-fs-data
vendor/init/hw/factory_init.rc:603 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | trigger load_persist_props_action
vendor/init/hw/factory_init.rc:605 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | setprop vendor.soter.teei.crypto.state unencrypted
vendor/init/hw/factory_init.rc:606 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | exec_start update_verifier
vendor/init/hw/factory_init.rc:607 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | class_start main
vendor/init/hw/factory_init.rc:608 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | class_start late_start
vendor/init/hw/factory_init.rc:609 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | start mount_all_storages
vendor/init/hw/factory_init.rc:625 | property:sys.powerctl=* | powerctl ${sys.powerctl}
vendor/init/hw/factory_init.rc:630 | property:ro.debuggable=1 | chmod 0773 /data/misc/trace
vendor/init/hw/factory_init.rc:631 | property:ro.debuggable=1 | start console
vendor/init/hw/factory_init.rc:743 | property:ro.vendor.mtklog_internal=1 | setprop vendor.logmuch.value 10000
vendor/init/hw/factory_init.rc:746 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/factory_init.rc:747 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceClass 0
vendor/init/hw/factory_init.rc:748 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceSubClass 0
vendor/init/hw/factory_init.rc:749 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceProtocol 0
vendor/init/hw/factory_init.rc:750 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:751 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/factory_init.rc:752 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/factory_init.rc:753 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/factory_init.rc:754 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /sys/devices/platform/mt_usb/saving 0
vendor/init/hw/factory_init.rc:755 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | setprop vendor.usb.clear 2
vendor/init/hw/factory_init.rc:759 | property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/factory_init.rc:762 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/factory_init.rc:763 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno
vendor/init/hw/factory_init.rc:764 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"
vendor/init/hw/factory_init.rc:765 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2006
vendor/init/hw/factory_init.rc:766 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:767 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/factory_init.rc:768 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/factory_init.rc:769 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "musb-hdrc"
vendor/init/hw/factory_init.rc:773 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/factory_init.rc:774 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "acm"
vendor/init/hw/factory_init.rc:775 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2007
vendor/init/hw/factory_init.rc:776 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceClass 02
vendor/init/hw/factory_init.rc:777 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:778 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "musb-hdrc"
vendor/init/hw/factory_init.rc:782 | property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/factory_init.rc:783 | property:sys.usb.config=gs3,dual_acm && property:sys.usb.configfs=1 | setprop vendor.usb.clear 1
vendor/init/hw/factory_init.rc:787 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs3_dual_acm"
vendor/init/hw/factory_init.rc:788 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | write /config/usb_gadget/g1/idProduct 0x202E
vendor/init/hw/factory_init.rc:789 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/factory_init.rc:790 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:791 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/factory_init.rc:792 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/factory_init.rc:793 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/factory_init.rc:794 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
vendor/init/hw/factory_init.rc:795 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/factory_init.rc:796 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs3,dual_acm && \ | setprop vendor.usb.clear 0
vendor/init/hw/factory_init.rc:799 | property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/factory_init.rc:800 | property:sys.usb.config=gs1gs3,dual_acm && property:sys.usb.configfs=1 | setprop vendor.usb.clear 1
vendor/init/hw/factory_init.rc:804 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs1gs3_dual_acm"
vendor/init/hw/factory_init.rc:805 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | write /config/usb_gadget/g1/idProduct 0x202F
vendor/init/hw/factory_init.rc:806 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/factory_init.rc:807 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:808 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/factory_init.rc:809 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/factory_init.rc:810 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/factory_init.rc:811 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
vendor/init/hw/factory_init.rc:812 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/factory_init.rc:813 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3,dual_acm && \ | setprop vendor.usb.clear 0
vendor/init/hw/factory_init.rc:816 | property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/factory_init.rc:817 | property:sys.usb.config=gs1gs3 && property:sys.usb.configfs=1 | setprop vendor.usb.clear 1
vendor/init/hw/factory_init.rc:821 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "gs1gs3"
vendor/init/hw/factory_init.rc:822 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | write /config/usb_gadget/g1/idProduct 0x2029
vendor/init/hw/factory_init.rc:823 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/factory_init.rc:824 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/factory_init.rc:825 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/factory_init.rc:826 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | symlink /config/usb_gadget/g1/functions/acm.gs1 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/factory_init.rc:827 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/factory_init.rc:828 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | write /config/usb_gadget/g1/UDC ${sys.usb.controller}
vendor/init/hw/factory_init.rc:829 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=gs1gs3 && \ | setprop vendor.usb.clear 0
vendor/init/hw/init.aee.rc:2 | init | write /proc/aed/generate-oops aee0
vendor/init/hw/init.ago.rc:4 | init | write /sys/block/zram0/comp_algorithm lz4
vendor/init/hw/init.ago.rc:7 | post-fs-data && property:ro.crypto.type=block | mkdir /data/unencrypted
vendor/init/hw/init.ago.rc:10 | post-fs-data && property:ro.crypto.state=unencrypted | mkdir /data/unencrypted
vendor/init/hw/init.ago.rc:13 | post-fs-data | write /proc/sys/vm/page-cluster 0
vendor/init/hw/init.ago.rc:14 | post-fs-data | swapon_all /vendor/etc/fstab.enableswap
vendor/init/hw/init.ago.rc:15 | post-fs-data | write /dev/memcg/memory.swappiness 150
vendor/init/hw/init.ago.rc:16 | post-fs-data | write /dev/memcg/apps/memory.swappiness 150
vendor/init/hw/init.ago.rc:17 | post-fs-data | write /dev/memcg/system/memory.swappiness 150
vendor/init/hw/init.ago.rc:18 | post-fs-data | write /proc/sys/vm/swappiness 150
vendor/init/hw/init.connectivity.rc:11 | post-fs-data | chmod 0660 /dev/ttyMT2
vendor/init/hw/init.connectivity.rc:12 | post-fs-data | chown system system /dev/ttyMT2
vendor/init/hw/init.connectivity.rc:15 | post-fs-data | chmod 0660 /dev/ttyMT1
vendor/init/hw/init.connectivity.rc:16 | post-fs-data | chown system system /dev/ttyMT1
vendor/init/hw/init.connectivity.rc:19 | post-fs-data | mkdir /data/vendor/gps 0770 gps system
vendor/init/hw/init.connectivity.rc:22 | post-fs-data | chmod 666 /dev/gps_emi
vendor/init/hw/init.connectivity.rc:25 | post-fs-data | mkdir /data/vendor/wifi 0770 wifi wifi
vendor/init/hw/init.connectivity.rc:26 | post-fs-data | mkdir /data/vendor/wifi/wpa 0770 wifi wifi
vendor/init/hw/init.connectivity.rc:27 | post-fs-data | mkdir /data/vendor/wifi/wpa/sockets 0770 wifi wifi
vendor/init/hw/init.connectivity.rc:30 | post-fs-data | chown bluetooth bluetooth /dev/ttyGS2
vendor/init/hw/init.connectivity.rc:31 | post-fs-data | chmod 0660 /dev/ttyGS2
vendor/init/hw/init.modem.rc:8 | early-init | write /sys/class/net/ccmni0/queues/rx-0/rps_cpus "ff"
vendor/init/hw/init.modem.rc:9 | early-init | write /sys/class/net/ccmni1/queues/rx-0/rps_cpus "ff"
vendor/init/hw/init.modem.rc:10 | early-init | write /sys/class/net/ccmni2/queues/rx-0/rps_cpus "ff"
vendor/init/hw/init.modem.rc:11 | early-init | write /sys/class/net/ccmni0/queues/rx-0/rps_flow_cnt "1000"
vendor/init/hw/init.modem.rc:12 | early-init | write /sys/class/net/ccmni1/queues/rx-0/rps_flow_cnt "1000"
vendor/init/hw/init.modem.rc:13 | early-init | write /sys/class/net/ccmni2/queues/rx-0/rps_flow_cnt "1000"
vendor/init/hw/init.modem.rc:17 | post-fs-data | write /proc/bootprof "post-fs-data: on modem start"
vendor/init/hw/init.modem.rc:20 | post-fs-data | setprop vold.post_fs_data_done 1
vendor/init/hw/init.mt6762.rc:19 | early-init | write /proc/bootprof "INIT:early-init"
vendor/init/hw/init.mt6762.rc:21 | early-init | write /proc/perfmgr/boost_ctrl/cpu_ctrl/boot_freq "0 0 5 0"
vendor/init/hw/init.mt6762.rc:22 | early-init | write /sys/devices/platform/10012000.dvfsrc/helio-dvfsrc/dvfsrc_req_ddr_opp 0
vendor/init/hw/init.mt6762.rc:23 | early-init | mount debugfs debugfs /sys/kernel/debug
vendor/init/hw/init.mt6762.rc:24 | early-init | chmod 0755 /sys/kernel/debug
vendor/init/hw/init.mt6762.rc:28 | init | mkdir /dev/stune/io
vendor/init/hw/init.mt6762.rc:29 | init | chown root system /dev/stune/io
vendor/init/hw/init.mt6762.rc:30 | init | chown root system /dev/stune/io/tasks
vendor/init/hw/init.mt6762.rc:31 | init | chmod 0664 /dev/stune/io/tasks
vendor/init/hw/init.mt6762.rc:34 | init | symlink /sdcard /mnt/sdcard
vendor/init/hw/init.mt6762.rc:40 | init | mkdir /mnt/vendor/protect_f 0771 system system
vendor/init/hw/init.mt6762.rc:41 | init | mkdir /mnt/vendor/protect_s 0771 system system
vendor/init/hw/init.mt6762.rc:42 | init | mkdir /mnt/vendor/nvcfg 0771 system system
vendor/init/hw/init.mt6762.rc:45 | init | mkdir /mnt/vendor/nvdata 0771 system system
vendor/init/hw/init.mt6762.rc:47 | init | mkdir /custom
vendor/init/hw/init.mt6762.rc:49 | init | mkdir /mnt/cd-rom 0000 system system
vendor/init/hw/init.mt6762.rc:52 | init | chown root system /proc/lk_env
vendor/init/hw/init.mt6762.rc:53 | init | chmod 0660 /proc/lk_env
vendor/init/hw/init.mt6762.rc:56 | init | write /proc/sys/net/core/wmem_max 8388608
vendor/init/hw/init.mt6762.rc:57 | init | write /proc/sys/net/core/rmem_max 8388608
vendor/init/hw/init.mt6762.rc:60 | init | write /dev/cpuset/foreground/cpus 0-7
vendor/init/hw/init.mt6762.rc:61 | init | write /dev/cpuset/foreground/boost/cpus 0-7
vendor/init/hw/init.mt6762.rc:62 | init | write /dev/cpuset/background/cpus 4-7
vendor/init/hw/init.mt6762.rc:63 | init | write /dev/cpuset/system-background/cpus 2-7
vendor/init/hw/init.mt6762.rc:64 | init | write /dev/cpuset/top-app/cpus 0-7
vendor/init/hw/init.mt6762.rc:67 | init | write /dev/cpuctl/bg_non_interactive/cpu.rt_runtime_us 100000
vendor/init/hw/init.mt6762.rc:70 | late-init | write /proc/bootprof "INIT:late-init"
vendor/init/hw/init.mt6762.rc:73 | late-init | write /sys/kernel/rcu_expedited 1
vendor/init/hw/init.mt6762.rc:78 | charger | write /proc/hps/enabled 0
vendor/init/hw/init.mt6762.rc:79 | charger | write /sys/devices/system/cpu/cpu0/online 1
vendor/init/hw/init.mt6762.rc:80 | charger | write /sys/devices/system/cpu/cpu1/online 1
vendor/init/hw/init.mt6762.rc:81 | charger | write /sys/devices/system/cpu/cpu2/online 1
vendor/init/hw/init.mt6762.rc:82 | charger | write /sys/devices/system/cpu/cpu3/online 1
vendor/init/hw/init.mt6762.rc:83 | charger | write /sys/devices/system/cpu/cpu7/online 0
vendor/init/hw/init.mt6762.rc:84 | charger | write /sys/devices/system/cpu/cpu6/online 0
vendor/init/hw/init.mt6762.rc:85 | charger | write /sys/devices/system/cpu/cpu5/online 0
vendor/init/hw/init.mt6762.rc:86 | charger | write /sys/devices/system/cpu/cpu4/online 0
vendor/init/hw/init.mt6762.rc:88 | charger | exec /system/bin/e2fsck -f -p /dev/block/platform/bootdevice/by-name/cache
vendor/init/hw/init.mt6762.rc:89 | charger | mount ext4 /dev/block/platform/bootdevice/by-name/cache /cache rw wait
vendor/init/hw/init.mt6762.rc:90 | charger | chmod 0666 /dev/kmsg
vendor/init/hw/init.mt6762.rc:91 | charger | chmod 0775 /mnt/vendor
vendor/init/hw/init.mt6762.rc:92 | charger | mkdir /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:93 | charger | mount ext4 /dev/block/platform/bootdevice/by-name/nvcfg /mnt/vendor/nvcfg rw wait
vendor/init/hw/init.mt6762.rc:94 | charger | chown system system /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:95 | charger | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:96 | charger | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:97 | charger | write /sys/devices/platform/battery_meter/FG_daemon_log_level 7
vendor/init/hw/init.mt6762.rc:98 | charger | write /sys/bus/platform/devices/battery/FG_daemon_log_level 7
vendor/init/hw/init.mt6762.rc:100 | charger | chown system system /sys/power/autosleep
vendor/init/hw/init.mt6762.rc:101 | charger | chown system system /sys/power/state
vendor/init/hw/init.mt6762.rc:102 | charger | chown system system /sys/power/wakeup_count
vendor/init/hw/init.mt6762.rc:103 | charger | chown radio wakelock /sys/power/wake_lock
vendor/init/hw/init.mt6762.rc:104 | charger | chown radio wakelock /sys/power/wake_unlock
vendor/init/hw/init.mt6762.rc:105 | charger | chmod 0660 /sys/power/state
vendor/init/hw/init.mt6762.rc:106 | charger | chmod 0660 /sys/power/wake_lock
vendor/init/hw/init.mt6762.rc:107 | charger | chmod 0660 /sys/power/wake_unlock
vendor/init/hw/init.mt6762.rc:108 | charger | chmod 0660 /sys/power/wakeup_count
vendor/init/hw/init.mt6762.rc:109 | charger | write /sys/power/pm_async 0
vendor/init/hw/init.mt6762.rc:110 | charger | start fuelgauged
vendor/init/hw/init.mt6762.rc:111 | charger | start fuelgauged_nvram
vendor/init/hw/init.mt6762.rc:113 | charger | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6762.rc:114 | charger | chown system system /sys/class/leds/red/brightness
vendor/init/hw/init.mt6762.rc:115 | charger | chown system system /sys/class/leds/green/brightness
vendor/init/hw/init.mt6762.rc:117 | charger | start servicemanager
vendor/init/hw/init.mt6762.rc:118 | charger | start hwservicemanager
vendor/init/hw/init.mt6762.rc:119 | charger | start light-hal-2-0
vendor/init/hw/init.mt6762.rc:120 | charger | start health-hal-2-0
vendor/init/hw/init.mt6762.rc:123 | fs | write /proc/bootprof "INIT:Mount_START"
vendor/init/hw/init.mt6762.rc:124 | fs | write /sys/kernel/debug/tracing/trace_marker "B|1|Mount"
vendor/init/hw/init.mt6762.rc:125 | fs | wait_for_prop hwservicemanager.ready "true"
vendor/init/hw/init.mt6762.rc:126 | fs | start vendor.boot-hal-1-0
vendor/init/hw/init.mt6762.rc:127 | fs | mount_all /vendor/etc/fstab.mt6765
vendor/init/hw/init.mt6762.rc:128 | fs | write /sys/kernel/debug/tracing/trace_marker "E"
vendor/init/hw/init.mt6762.rc:129 | fs | write /proc/bootprof "INIT:Mount_END"
vendor/init/hw/init.mt6762.rc:133 | fs | mount iso9660 loop@/vendor/etc/iAmCdRom.iso /mnt/cd-rom ro
vendor/init/hw/init.mt6762.rc:136 | post-fs | write /proc/bootprof "INIT:post-fs"
vendor/init/hw/init.mt6762.rc:139 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "0 100"
vendor/init/hw/init.mt6762.rc:140 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "1 100"
vendor/init/hw/init.mt6762.rc:141 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "2 100"
vendor/init/hw/init.mt6762.rc:142 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "3 100"
vendor/init/hw/init.mt6762.rc:143 | post-fs | write /proc/sys/kernel/sched_stune_task_threshold 0
vendor/init/hw/init.mt6762.rc:146 | post-fs | symlink /sdcard /storage/sdcard0
vendor/init/hw/init.mt6762.rc:149 | post-fs | chown radio system /proc/freqhopping/dramc
vendor/init/hw/init.mt6762.rc:150 | post-fs | chown radio system /proc/freqhopping/dumpregs
vendor/init/hw/init.mt6762.rc:151 | post-fs | chown radio system /proc/freqhopping/freqhopping_debug
vendor/init/hw/init.mt6762.rc:152 | post-fs | chown radio system /proc/freqhopping/status
vendor/init/hw/init.mt6762.rc:153 | post-fs | chown radio system /proc/freqhopping/userdef
vendor/init/hw/init.mt6762.rc:156 | post-fs | symlink /dev/block/sda /dev/block/mmcblk0boot0
vendor/init/hw/init.mt6762.rc:157 | post-fs | symlink /dev/block/sdb /dev/block/mmcblk0boot1
vendor/init/hw/init.mt6762.rc:158 | post-fs | symlink /dev/block/mmcblk0boot0 /dev/block/platform/bootdevice/by-name/preloader_a
vendor/init/hw/init.mt6762.rc:159 | post-fs | symlink /dev/block/mmcblk0boot1 /dev/block/platform/bootdevice/by-name/preloader_b
vendor/init/hw/init.mt6762.rc:162 | post-fs | chown system system /proc/bootprof
vendor/init/hw/init.mt6762.rc:163 | post-fs | chmod 0664 /proc/bootprof
vendor/init/hw/init.mt6762.rc:164 | post-fs | chown root system /proc/mtprof/cputime
vendor/init/hw/init.mt6762.rc:165 | post-fs | chown root system /proc/mtprof/reboot_pid
vendor/init/hw/init.mt6762.rc:167 | post-fs | chown system system /mnt/vendor/protect_f
vendor/init/hw/init.mt6762.rc:168 | post-fs | chmod 0770 /mnt/vendor/protect_f
vendor/init/hw/init.mt6762.rc:170 | post-fs | chown system system /mnt/vendor/protect_s
vendor/init/hw/init.mt6762.rc:171 | post-fs | chmod 0770 /mnt/vendor/protect_s
vendor/init/hw/init.mt6762.rc:174 | post-fs-data | write /proc/bootprof "INIT:post-fs-data"
vendor/init/hw/init.mt6762.rc:177 | post-fs-data | write /sys/block/mmcblk0/queue/iostats 0
vendor/init/hw/init.mt6762.rc:178 | post-fs-data | write /sys/block/mmcblk0/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:179 | post-fs-data | write /sys/block/mmcblk0/queue/nr_requests 256
vendor/init/hw/init.mt6762.rc:180 | post-fs-data | write /sys/block/dm-0/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:181 | post-fs-data | write /sys/block/dm-1/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:182 | post-fs-data | write /sys/block/dm-2/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:183 | post-fs-data | write /sys/block/dm-3/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:184 | post-fs-data | write /sys/block/dm-4/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:185 | post-fs-data | write /sys/block/dm-5/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6762.rc:187 | post-fs-data | chown system system /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:188 | post-fs-data | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:189 | post-fs-data | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/init.mt6762.rc:190 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg
vendor/init/hw/init.mt6762.rc:191 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg/old_fg_data
vendor/init/hw/init.mt6762.rc:192 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg/car_tune_value
vendor/init/hw/init.mt6762.rc:198 | post-fs-data | chown root system /mnt/vendor/nvdata
vendor/init/hw/init.mt6762.rc:199 | post-fs-data | chmod 0771 /mnt/vendor/nvdata
vendor/init/hw/init.mt6762.rc:200 | post-fs-data | mkdir /mnt/vendor/nvdata/media 0771 media audio
vendor/init/hw/init.mt6762.rc:203 | post-fs-data | mkdir /mnt/vendor/cct
vendor/init/hw/init.mt6762.rc:204 | post-fs-data | chown root system /mnt/vendor/cct
vendor/init/hw/init.mt6762.rc:205 | post-fs-data | chmod 0771 /mnt/vendor/cct
vendor/init/hw/init.mt6762.rc:208 | post-fs-data | mkdir /data/vendor/flash
vendor/init/hw/init.mt6762.rc:209 | post-fs-data | chown root system /data/vendor/flash
vendor/init/hw/init.mt6762.rc:210 | post-fs-data | chmod 0771 /data/vendor/flash
vendor/init/hw/init.mt6762.rc:213 | post-fs-data | mkdir /data/vendor/shading
vendor/init/hw/init.mt6762.rc:214 | post-fs-data | chown root system /data/vendor/shading
vendor/init/hw/init.mt6762.rc:215 | post-fs-data | chmod 0771 /data/vendor/shading
vendor/init/hw/init.mt6762.rc:218 | post-fs-data | mkdir /data/vendor/shading_otp
vendor/init/hw/init.mt6762.rc:219 | post-fs-data | chown root system /data/vendor/shading_otp
vendor/init/hw/init.mt6762.rc:220 | post-fs-data | chmod 0771 /data/vendor/shading_otp
vendor/init/hw/init.mt6762.rc:223 | post-fs-data | mkdir /data/vendor/camera_dump
vendor/init/hw/init.mt6762.rc:224 | post-fs-data | chown root system /data/vendor/camera_dump
vendor/init/hw/init.mt6762.rc:225 | post-fs-data | chmod 0771 /data/vendor/camera_dump
vendor/init/hw/init.mt6762.rc:228 | post-fs-data | mkdir /data/vendor/dipdebug
vendor/init/hw/init.mt6762.rc:229 | post-fs-data | chown root system /data/vendor/dipdebug
vendor/init/hw/init.mt6762.rc:230 | post-fs-data | chmod 0771 /data/vendor/dipdebug
vendor/init/hw/init.mt6762.rc:233 | post-fs-data | mkdir /data/vendor/flicker
vendor/init/hw/init.mt6762.rc:234 | post-fs-data | chown root system /data/vendor/flicker
vendor/init/hw/init.mt6762.rc:235 | post-fs-data | chmod 0771 /data/vendor/flicker
vendor/init/hw/init.mt6762.rc:238 | post-fs-data | restorecon_recursive /mnt/vendor/nvdata
vendor/init/hw/init.mt6762.rc:242 | post-fs-data | chmod 0660 /dev/rfkill
vendor/init/hw/init.mt6762.rc:245 | post-fs-data | mkdir /dev/radio 0770 radio radio
vendor/init/hw/init.mt6762.rc:249 | post-fs-data | chmod 0775 /vendor/xbin/MPED
vendor/init/hw/init.mt6762.rc:250 | post-fs-data | chmod 0666 /dev/gps_emi
vendor/init/hw/init.mt6762.rc:255 | post-fs-data | chmod 0220 /sys/class/misc/scp/scp_mobile_log
vendor/init/hw/init.mt6762.rc:256 | post-fs-data | chown root system /sys/class/misc/scp/scp_mobile_log
vendor/init/hw/init.mt6762.rc:257 | post-fs-data | chmod 0220 /sys/class/misc/scp/scp_log_flush
vendor/init/hw/init.mt6762.rc:258 | post-fs-data | chown root system /sys/class/misc/scp/scp_log_flush
vendor/init/hw/init.mt6762.rc:259 | post-fs-data | chmod 0440 /dev/scp
vendor/init/hw/init.mt6762.rc:260 | post-fs-data | chown root system /dev/scp
vendor/init/hw/init.mt6762.rc:263 | post-fs-data | chmod 0620 /sys/class/misc/sspm/sspm_mobile_log
vendor/init/hw/init.mt6762.rc:264 | post-fs-data | chown root system /sys/class/misc/sspm/sspm_mobile_log
vendor/init/hw/init.mt6762.rc:265 | post-fs-data | chmod 0440 /dev/sspm
vendor/init/hw/init.mt6762.rc:266 | post-fs-data | chown root system /dev/sspm
vendor/init/hw/init.mt6762.rc:269 | post-fs-data | chmod 444 /etc/radvd/radvd.conf
vendor/init/hw/init.mt6762.rc:273 | post-fs-data | chmod 666 /dev/mali0
vendor/init/hw/init.mt6762.rc:278 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_calmat
vendor/init/hw/init.mt6762.rc:279 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time
vendor/init/hw/init.mt6762.rc:280 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_mode
vendor/init/hw/init.mt6762.rc:281 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time0
vendor/init/hw/init.mt6762.rc:282 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time1
vendor/init/hw/init.mt6762.rc:283 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_spl_num
vendor/init/hw/init.mt6762.rc:284 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_pressure_threshold
vendor/init/hw/init.mt6762.rc:285 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_auto_time_interval
vendor/init/hw/init.mt6762.rc:286 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_sample_cnt
vendor/init/hw/init.mt6762.rc:287 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_asamp
vendor/init/hw/init.mt6762.rc:288 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log
vendor/init/hw/init.mt6762.rc:289 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log_to_fs
vendor/init/hw/init.mt6762.rc:291 | post-fs-data | chmod 0666 /dev/pmem_multimedia
vendor/init/hw/init.mt6762.rc:297 | post-fs-data | chown root radio /dev/ptmx
vendor/init/hw/init.mt6762.rc:300 | post-fs-data | chmod 660 /dev/alarm
vendor/init/hw/init.mt6762.rc:301 | post-fs-data | chown root system /dev/alarm
vendor/init/hw/init.mt6762.rc:305 | post-fs-data | chmod 0444 /dev/m4u
vendor/init/hw/init.mt6762.rc:309 | post-fs-data | setrlimit 8 67108864 67108864
vendor/init/hw/init.mt6762.rc:312 | post-fs-data | setrlimit 8 -1 -1
vendor/init/hw/init.mt6762.rc:315 | post-fs-data | chmod 0644 /dev/mtk_cmdq
vendor/init/hw/init.mt6762.rc:316 | post-fs-data | chown system system /dev/mtk_cmdq
vendor/init/hw/init.mt6762.rc:319 | post-fs-data | chmod 0644 /dev/mdp_sync
vendor/init/hw/init.mt6762.rc:320 | post-fs-data | chown system system /dev/mdp_sync
vendor/init/hw/init.mt6762.rc:323 | post-fs-data | chmod 0640 /dev/offloadservice
vendor/init/hw/init.mt6762.rc:324 | post-fs-data | chown media media /dev/offloadservice
vendor/init/hw/init.mt6762.rc:327 | post-fs-data | chmod 0640 /dev/audio_ipi
vendor/init/hw/init.mt6762.rc:328 | post-fs-data | chown media media /dev/audio_ipi
vendor/init/hw/init.mt6762.rc:331 | post-fs-data | chmod 0640 /dev/audio_scp
vendor/init/hw/init.mt6762.rc:332 | post-fs-data | chown media media /dev/audio_scp
vendor/init/hw/init.mt6762.rc:333 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/rt5509_param.0/prop_param
vendor/init/hw/init.mt6762.rc:334 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/rt5509_param.1/prop_param
vendor/init/hw/init.mt6762.rc:335 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/mt6660-param.0/prop_params
vendor/init/hw/init.mt6762.rc:338 | post-fs-data | chown root radio /sys/devices/platform/Accdet_Driver/driver/accdet_call_state
vendor/init/hw/init.mt6762.rc:341 | post-fs-data | chmod 0666 /dev/goodix_fp
vendor/init/hw/init.mt6762.rc:345 | post-fs-data | chmod 0660 /dev/Vcodec
vendor/init/hw/init.mt6762.rc:346 | post-fs-data | chown media system /dev/Vcodec
vendor/init/hw/init.mt6762.rc:350 | post-fs-data | chmod 0660 /dev/MJC
vendor/init/hw/init.mt6762.rc:351 | post-fs-data | chown media system /dev/MJC
vendor/init/hw/init.mt6762.rc:354 | post-fs-data | chown radio radio /sys/devices/platform/mtk-kpd/driver/kpd_call_state
vendor/init/hw/init.mt6762.rc:357 | post-fs-data | chown radio radio /sys/devices/platform/battery/Charging_CallState
vendor/init/hw/init.mt6762.rc:360 | post-fs-data | chown radio system /sys/devices/platform/mt-pmic/low_battery_protect_stop
vendor/init/hw/init.mt6762.rc:361 | post-fs-data | chown radio system /sys/devices/platform/mt-pmic/low_battery_protect_ut
vendor/init/hw/init.mt6762.rc:364 | post-fs-data | mkdir /data/vendor_de/meta 0770 system system
vendor/init/hw/init.mt6762.rc:365 | post-fs-data | mkdir /data/vendor_de/meta/mddb 0770 system system
vendor/init/hw/init.mt6762.rc:368 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsopmode
vendor/init/hw/init.mt6762.rc:369 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsreptxy
vendor/init/hw/init.mt6762.rc:370 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsreptz
vendor/init/hw/init.mt6762.rc:371 | post-fs-data | chown system /sys/devices/platform/gsensor/driver/cpsopmode
vendor/init/hw/init.mt6762.rc:372 | post-fs-data | chown system /sys/devices/platform/gsensor/driver/cpsrange
vendor/init/hw/init.mt6762.rc:374 | post-fs-data | chown system /sys/class/input/input1/enable
vendor/init/hw/init.mt6762.rc:375 | post-fs-data | chown system /sys/class/input/input1/delay
vendor/init/hw/init.mt6762.rc:376 | post-fs-data | chown system /sys/class/input/input1/wake
vendor/init/hw/init.mt6762.rc:377 | post-fs-data | chown system /sys/class/input/input1/offsets
vendor/init/hw/init.mt6762.rc:378 | post-fs-data | chown system /sys/class/input/input2/enable
vendor/init/hw/init.mt6762.rc:379 | post-fs-data | chown system /sys/class/input/input2/delay
vendor/init/hw/init.mt6762.rc:380 | post-fs-data | chown system /sys/class/input/input2/wake
vendor/init/hw/init.mt6762.rc:381 | post-fs-data | chown system /sys/class/input/input2/offsets
vendor/init/hw/init.mt6762.rc:382 | post-fs-data | chown system /sys/class/input/input3/enable
vendor/init/hw/init.mt6762.rc:383 | post-fs-data | chown system /sys/class/input/input3/delay
vendor/init/hw/init.mt6762.rc:384 | post-fs-data | chown system /sys/class/input/input3/wake
vendor/init/hw/init.mt6762.rc:385 | post-fs-data | chown system /sys/class/input/input3/offsets
vendor/init/hw/init.mt6762.rc:386 | post-fs-data | chown system /sys/class/input/input4/enable
vendor/init/hw/init.mt6762.rc:387 | post-fs-data | chown system /sys/class/input/input4/delay
vendor/init/hw/init.mt6762.rc:388 | post-fs-data | chown system /sys/class/input/input4/wake
vendor/init/hw/init.mt6762.rc:389 | post-fs-data | chown system /sys/class/input/input4/offsets
vendor/init/hw/init.mt6762.rc:390 | post-fs-data | chown system /sys/class/input/input5/enable
vendor/init/hw/init.mt6762.rc:391 | post-fs-data | chown system /sys/class/input/input5/delay
vendor/init/hw/init.mt6762.rc:392 | post-fs-data | chown system /sys/class/input/input5/wake
vendor/init/hw/init.mt6762.rc:393 | post-fs-data | chown system /sys/class/input/input5/offsets
vendor/init/hw/init.mt6762.rc:394 | post-fs-data | chown system /sys/class/input/input6/enable
vendor/init/hw/init.mt6762.rc:395 | post-fs-data | chown system /sys/class/input/input6/delay
vendor/init/hw/init.mt6762.rc:396 | post-fs-data | chown system /sys/class/input/input6/wake
vendor/init/hw/init.mt6762.rc:397 | post-fs-data | chown system /sys/class/input/input6/offsets
vendor/init/hw/init.mt6762.rc:398 | post-fs-data | chown system /sys/class/input/input7/enable
vendor/init/hw/init.mt6762.rc:399 | post-fs-data | chown system /sys/class/input/input7/delay
vendor/init/hw/init.mt6762.rc:400 | post-fs-data | chown system /sys/class/input/input7/wake
vendor/init/hw/init.mt6762.rc:401 | post-fs-data | chown system /sys/class/input/input7/offsets
vendor/init/hw/init.mt6762.rc:402 | post-fs-data | chown system /sys/class/input/input8/enable
vendor/init/hw/init.mt6762.rc:403 | post-fs-data | chown system /sys/class/input/input8/delay
vendor/init/hw/init.mt6762.rc:404 | post-fs-data | chown system /sys/class/input/input8/wake
vendor/init/hw/init.mt6762.rc:405 | post-fs-data | chown system /sys/class/input/input8/offsets
vendor/init/hw/init.mt6762.rc:406 | post-fs-data | chown system /sys/class/input/input9/enable
vendor/init/hw/init.mt6762.rc:407 | post-fs-data | chown system /sys/class/input/input9/delay
vendor/init/hw/init.mt6762.rc:408 | post-fs-data | chown system /sys/class/input/input9/wake
vendor/init/hw/init.mt6762.rc:409 | post-fs-data | chown system /sys/class/input/input9/offsets
vendor/init/hw/init.mt6762.rc:410 | post-fs-data | chown system /sys/class/input/input10/enable
vendor/init/hw/init.mt6762.rc:411 | post-fs-data | chown system /sys/class/input/input10/delay
vendor/init/hw/init.mt6762.rc:412 | post-fs-data | chown system /sys/class/input/input10/wake
vendor/init/hw/init.mt6762.rc:413 | post-fs-data | chown system /sys/class/input/input10/offsets
vendor/init/hw/init.mt6762.rc:416 | post-fs-data | chown root diag /sys/bus/platform/drivers/eint/current_eint
vendor/init/hw/init.mt6762.rc:419 | post-fs-data | chmod 0664 /dev/graphics/fb0
vendor/init/hw/init.mt6762.rc:420 | post-fs-data | chown system graphics /dev/graphics/fb0
vendor/init/hw/init.mt6762.rc:422 | post-fs-data | chmod 0664 /dev/mtk_disp_mgr
vendor/init/hw/init.mt6762.rc:423 | post-fs-data | chown system graphics /dev/mtk_disp_mgr
vendor/init/hw/init.mt6762.rc:425 | post-fs-data | chmod 0660 /dev/mtkfb_vsync
vendor/init/hw/init.mt6762.rc:426 | post-fs-data | chown system graphics /dev/mtkfb_vsync
vendor/init/hw/init.mt6762.rc:428 | post-fs-data | chmod 0660 /dev/sw_sync
vendor/init/hw/init.mt6762.rc:429 | post-fs-data | chown system graphics /dev/sw_sync
vendor/init/hw/init.mt6762.rc:432 | post-fs-data | chown root diag /vendor/bin/baseband_regdump
vendor/init/hw/init.mt6762.rc:433 | post-fs-data | chmod 4750 /vendor/bin/baseband_regdump
vendor/init/hw/init.mt6762.rc:436 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access_bank0
vendor/init/hw/init.mt6762.rc:437 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access_bank1
vendor/init/hw/init.mt6762.rc:438 | post-fs-data | chown root radio /sys/devices/platform/mt6311-user/mt6311_access
vendor/init/hw/init.mt6762.rc:439 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access
vendor/init/hw/init.mt6762.rc:440 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access_bank0
vendor/init/hw/init.mt6762.rc:441 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access_bank1
vendor/init/hw/init.mt6762.rc:442 | post-fs-data | chmod 0664 /sys/devices/platform/mt6311-user/mt6311_access
vendor/init/hw/init.mt6762.rc:443 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access
vendor/init/hw/init.mt6762.rc:446 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_debug_mode
vendor/init/hw/init.mt6762.rc:447 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_mode
vendor/init/hw/init.mt6762.rc:448 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_period
vendor/init/hw/init.mt6762.rc:449 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_period_mode
vendor/init/hw/init.mt6762.rc:450 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_debug_mode
vendor/init/hw/init.mt6762.rc:451 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_mode
vendor/init/hw/init.mt6762.rc:452 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_period
vendor/init/hw/init.mt6762.rc:453 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_period_mode
vendor/init/hw/init.mt6762.rc:456 | post-fs-data | chown root diag /sys/bus/platform/drivers/eint/current_eint
vendor/init/hw/init.mt6762.rc:459 | post-fs-data | chmod 0660 /dev/sec
vendor/init/hw/init.mt6762.rc:460 | post-fs-data | chown root system /dev/sec
vendor/init/hw/init.mt6762.rc:463 | post-fs-data | chmod 0660 /dev/fm
vendor/init/hw/init.mt6762.rc:464 | post-fs-data | chown media media /dev/fm
vendor/init/hw/init.mt6762.rc:469 | post-fs-data | chmod 0440 /dev/devmap
vendor/init/hw/init.mt6762.rc:470 | post-fs-data | chown root system /dev/devmap
vendor/init/hw/init.mt6762.rc:473 | post-fs-data | chown bluetooth bluetooth /dev/hid-keyboard
vendor/init/hw/init.mt6762.rc:474 | post-fs-data | chmod 0660 /dev/hid-keyboard
vendor/init/hw/init.mt6762.rc:476 | post-fs-data | chown system net_bt_admin /dev/uinput
vendor/init/hw/init.mt6762.rc:479 | post-fs-data | chown system media /dev/uibc
vendor/init/hw/init.mt6762.rc:480 | post-fs-data | chmod 0660 /dev/uibc
vendor/init/hw/init.mt6762.rc:483 | post-fs-data | chmod 0664 /dev/TV-out
vendor/init/hw/init.mt6762.rc:486 | post-fs-data | chown media system /dev/hdmitx
vendor/init/hw/init.mt6762.rc:487 | post-fs-data | chmod 0664 /dev/hdmitx
vendor/init/hw/init.mt6762.rc:491 | post-fs-data | chmod 0664 /proc/mtk_jpeg
vendor/init/hw/init.mt6762.rc:494 | post-fs-data | chmod 0440 /dev/mtk_almk
vendor/init/hw/init.mt6762.rc:497 | post-fs-data | chmod 0444 /dev/mtk_disp
vendor/init/hw/init.mt6762.rc:500 | post-fs-data | chmod 0640 /dev/ancservice
vendor/init/hw/init.mt6762.rc:501 | post-fs-data | chown media media /dev/ancservice
vendor/init/hw/init.mt6762.rc:504 | post-fs-data | chmod 0660 /dev/camera-sysram
vendor/init/hw/init.mt6762.rc:505 | post-fs-data | chmod 0660 /dev/camera-isp
vendor/init/hw/init.mt6762.rc:506 | post-fs-data | chmod 0660 /dev/camera-tsf
vendor/init/hw/init.mt6762.rc:507 | post-fs-data | chmod 0660 /dev/camera-dpe
vendor/init/hw/init.mt6762.rc:508 | post-fs-data | chmod 0660 /dev/camera-fdvt
vendor/init/hw/init.mt6762.rc:509 | post-fs-data | chmod 0660 /dev/camera-pipemgr
vendor/init/hw/init.mt6762.rc:510 | post-fs-data | chmod 0660 /dev/kd_camera_hw
vendor/init/hw/init.mt6762.rc:511 | post-fs-data | chmod 0660 /dev/flashlight
vendor/init/hw/init.mt6762.rc:512 | post-fs-data | chmod 0660 /dev/kd_camera_hw_bus2
vendor/init/hw/init.mt6762.rc:513 | post-fs-data | chmod 0660 /dev/FM50AF
vendor/init/hw/init.mt6762.rc:514 | post-fs-data | chmod 0660 /dev/CAM_CAL_DRV
vendor/init/hw/init.mt6762.rc:515 | post-fs-data | chmod 0660 /dev/MAINAF
vendor/init/hw/init.mt6762.rc:516 | post-fs-data | chmod 0660 /dev/MAIN2AF
vendor/init/hw/init.mt6762.rc:517 | post-fs-data | chmod 0660 /dev/SUBAF
vendor/init/hw/init.mt6762.rc:519 | post-fs-data | chown system camera /dev/camera-sysram
vendor/init/hw/init.mt6762.rc:520 | post-fs-data | chown system camera /dev/camera-isp
vendor/init/hw/init.mt6762.rc:521 | post-fs-data | chown system camera /dev/camera-dpe
vendor/init/hw/init.mt6762.rc:522 | post-fs-data | chown system camera /dev/camera-tsf
vendor/init/hw/init.mt6762.rc:523 | post-fs-data | chown system camera /dev/camera-fdvt
vendor/init/hw/init.mt6762.rc:524 | post-fs-data | chown system camera /dev/camera-pipemgr
vendor/init/hw/init.mt6762.rc:525 | post-fs-data | chown system camera /dev/kd_camera_hw
vendor/init/hw/init.mt6762.rc:526 | post-fs-data | chown system camera /dev/flashlight
vendor/init/hw/init.mt6762.rc:527 | post-fs-data | chown system camera /dev/kd_camera_hw_bus2
vendor/init/hw/init.mt6762.rc:528 | post-fs-data | chown system camera /dev/FM50AF
vendor/init/hw/init.mt6762.rc:529 | post-fs-data | chown system camera /dev/CAM_CAL_DRV
vendor/init/hw/init.mt6762.rc:530 | post-fs-data | chown system camera /dev/MAINAF
vendor/init/hw/init.mt6762.rc:531 | post-fs-data | chown system camera /dev/MAIN2AF
vendor/init/hw/init.mt6762.rc:532 | post-fs-data | chown system camera /dev/SUBAF
vendor/init/hw/init.mt6762.rc:535 | post-fs-data | chmod 0666 /dev/ccu
vendor/init/hw/init.mt6762.rc:536 | post-fs-data | chown system camera /dev/ccu
vendor/init/hw/init.mt6762.rc:539 | post-fs-data | chmod 0640 /dev/vow
vendor/init/hw/init.mt6762.rc:540 | post-fs-data | chown media media /dev/vow
vendor/init/hw/init.mt6762.rc:543 | post-fs-data | chmod 0660 /dev/MATV
vendor/init/hw/init.mt6762.rc:544 | post-fs-data | chown system media /dev/MATV
vendor/init/hw/init.mt6762.rc:547 | post-fs-data | chown root radio /dev/mt_otg_test
vendor/init/hw/init.mt6762.rc:548 | post-fs-data | chmod 0660 /dev/mt_otg_test
vendor/init/hw/init.mt6762.rc:551 | post-fs-data | chmod 0660 /dev/mt-mdp
vendor/init/hw/init.mt6762.rc:552 | post-fs-data | chown system media /dev/mt-mdp
vendor/init/hw/init.mt6762.rc:555 | post-fs-data | chmod 0660 /dev/MTK_SMI
vendor/init/hw/init.mt6762.rc:556 | post-fs-data | chown media media /dev/MTK_SMI
vendor/init/hw/init.mt6762.rc:559 | post-fs-data | chmod 0660 /dev/mtk_rrc
vendor/init/hw/init.mt6762.rc:560 | post-fs-data | chown media system /dev/mtk_rrc
vendor/init/hw/init.mt6762.rc:563 | post-fs-data | chmod 0660 /dev/mtk_dfrc
vendor/init/hw/init.mt6762.rc:564 | post-fs-data | chown system graphics /dev/mtk_dfrc
vendor/init/hw/init.mt6762.rc:568 | post-fs-data | chmod 0640 /mtd@sec_ro
vendor/init/hw/init.mt6762.rc:569 | post-fs-data | chown root system /mtd@sec_ro
vendor/init/hw/init.mt6762.rc:571 | post-fs-data | chmod 0640 /mtd@preloader
vendor/init/hw/init.mt6762.rc:572 | post-fs-data | chown root system /mtd@preloader
vendor/init/hw/init.mt6762.rc:574 | post-fs-data | chmod 0660 /mtd@pro_info
vendor/init/hw/init.mt6762.rc:575 | post-fs-data | chown root system /mtd@pro_info
vendor/init/hw/init.mt6762.rc:577 | post-fs-data | chmod 0640 /mtd@bootimg
vendor/init/hw/init.mt6762.rc:578 | post-fs-data | chown root system /mtd@bootimg
vendor/init/hw/init.mt6762.rc:580 | post-fs-data | chmod 0640 /mtd@recovery
vendor/init/hw/init.mt6762.rc:581 | post-fs-data | chown root system /mtd@recovery
vendor/init/hw/init.mt6762.rc:583 | post-fs-data | chmod 0660 /mtd@nvram
vendor/init/hw/init.mt6762.rc:584 | post-fs-data | chown root system /mtd@nvram
vendor/init/hw/init.mt6762.rc:586 | post-fs-data | chmod 0660 /mtd@seccfg
vendor/init/hw/init.mt6762.rc:587 | post-fs-data | chown root system /mtd@seccfg
vendor/init/hw/init.mt6762.rc:589 | post-fs-data | chmod 0660 /mtd@misc
vendor/init/hw/init.mt6762.rc:590 | post-fs-data | chown root system /mtd@misc
vendor/init/hw/init.mt6762.rc:592 | post-fs-data | chmod 0664 /sys/bus/platform/drivers/emi_ctrl/concurrency_scenario
vendor/init/hw/init.mt6762.rc:593 | post-fs-data | chown media media /sys/bus/platform/drivers/emi_ctrl/concurrency_scenario
vendor/init/hw/init.mt6762.rc:596 | post-fs-data | chmod 0660 /dev/mtk_sched
vendor/init/hw/init.mt6762.rc:597 | post-fs-data | chown system media /dev/mtk_sched
vendor/init/hw/init.mt6762.rc:600 | post-fs-data | chmod 0660 /dev/ebc
vendor/init/hw/init.mt6762.rc:601 | post-fs-data | chown media media /dev/ebc
vendor/init/hw/init.mt6762.rc:603 | post-fs-data | chmod 0666 /dev/uio0
vendor/init/hw/init.mt6762.rc:606 | post-fs-data | chmod 0660 /proc/driver/otp
vendor/init/hw/init.mt6762.rc:607 | post-fs-data | chown root system /proc/driver/otp
vendor/init/hw/init.mt6762.rc:610 | post-fs-data | chmod 0664 /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6762.rc:611 | post-fs-data | chown root system /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6762.rc:612 | post-fs-data | chmod 0664 /sys/class/udc/musb-hdrc/device/cmode
vendor/init/hw/init.mt6762.rc:613 | post-fs-data | chown root system /sys/class/udc/musb-hdrc/device/cmode
vendor/init/hw/init.mt6762.rc:616 | post-fs-data | chown system system /proc/secmem0
vendor/init/hw/init.mt6762.rc:618 | post-fs-data | chmod 0666 /dev/exm0
vendor/init/hw/init.mt6762.rc:622 | post-fs-data | mkdir /data/vendor/.tp/ 0775 system system
vendor/init/hw/init.mt6762.rc:625 | post-fs-data | write /sys/power/pm_async 0
vendor/init/hw/init.mt6762.rc:631 | boot | write /proc/bootprof "INIT:boot"
vendor/init/hw/init.mt6762.rc:633 | boot | write /proc/sys/vm/dirty_writeback_centisecs 300
vendor/init/hw/init.mt6762.rc:634 | boot | chmod 0660 /sys/power/autosleep
vendor/init/hw/init.mt6762.rc:636 | boot | chmod 0664 /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6762.rc:637 | boot | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6762.rc:638 | boot | chmod 0664 /sys/class/leds/red/brightness
vendor/init/hw/init.mt6762.rc:639 | boot | chown system system /sys/class/leds/red/brightness
vendor/init/hw/init.mt6762.rc:640 | boot | chmod 0664 /sys/class/leds/green/brightness
vendor/init/hw/init.mt6762.rc:641 | boot | chown system system /sys/class/leds/green/brightness
vendor/init/hw/init.mt6762.rc:642 | boot | chmod 0664 /sys/class/leds/blue/brightness
vendor/init/hw/init.mt6762.rc:643 | boot | chown system system /sys/class/leds/blue/brightness
vendor/init/hw/init.mt6762.rc:645 | boot | chown system system /sys/devices/system/cpu/cpufreq/hotplug/cpu_num_base
vendor/init/hw/init.mt6762.rc:646 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/hotplug/cpu_num_base
vendor/init/hw/init.mt6762.rc:647 | boot | chown system system /sys/devices/system/cpu/cpufreq/policy0/scaling_governor
vendor/init/hw/init.mt6762.rc:648 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/policy0/scaling_governor
vendor/init/hw/init.mt6762.rc:649 | boot | chown system system /sys/devices/system/cpu/cpufreq/policy4/scaling_governor
vendor/init/hw/init.mt6762.rc:650 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/policy4/scaling_governor
vendor/init/hw/init.mt6762.rc:651 | boot | chown root   system /sys/kernel/debug/binder/transaction_log_enable
vendor/init/hw/init.mt6762.rc:652 | boot | chown root   system /sys/kernel/debug/binder/perf_evalue
vendor/init/hw/init.mt6762.rc:653 | boot | chown system system /sys/devices/system/cpu/rq-stats/htasks_thresh
vendor/init/hw/init.mt6762.rc:654 | boot | chmod 0660 /sys/devices/system/cpu/rq-stats/htasks_thresh
vendor/init/hw/init.mt6762.rc:655 | boot | chown system system /sys/devices/system/cpu/rq-stats/avg_htasks_thresh
vendor/init/hw/init.mt6762.rc:656 | boot | chmod 0660 /sys/devices/system/cpu/rq-stats/avg_htasks_thresh
vendor/init/hw/init.mt6762.rc:658 | boot | chown system /sys/module/mlog/parameters/do_mlog
vendor/init/hw/init.mt6762.rc:659 | boot | chown system /sys/module/mlog/parameters/timer_intval
vendor/init/hw/init.mt6762.rc:661 | boot | chown root   radio  /sys/class/leds/lcd-backlight/duty
vendor/init/hw/init.mt6762.rc:662 | boot | chown root   radio  /sys/class/leds/lcd-backlight/div
vendor/init/hw/init.mt6762.rc:663 | boot | chown system system /sys/class/leds/lcd-backlight/trigger
vendor/init/hw/init.mt6762.rc:664 | boot | chown system system /sys/class/leds/button-backlight/trigger
vendor/init/hw/init.mt6762.rc:665 | boot | chown system system /sys/class/leds/keyboard-backlight/trigger
vendor/init/hw/init.mt6762.rc:666 | boot | chown system system /sys/class/leds/jogball-backlight/trigger
vendor/init/hw/init.mt6762.rc:667 | boot | chown system system /sys/class/leds/red/trigger
vendor/init/hw/init.mt6762.rc:668 | boot | chown system system /sys/class/leds/green/trigger
vendor/init/hw/init.mt6762.rc:669 | boot | chown system system /sys/class/leds/blue/trigger
vendor/init/hw/init.mt6762.rc:672 | boot | chown radio system /sys/power/tuxonice
vendor/init/hw/init.mt6762.rc:673 | boot | chown radio system /sys/power/tuxonice/do_hibernate
vendor/init/hw/init.mt6762.rc:674 | boot | chmod 0770 /sys/power/tuxonice
vendor/init/hw/init.mt6762.rc:675 | boot | chmod 0220 /sys/power/tuxonice/do_hibernate
vendor/init/hw/init.mt6762.rc:679 | boot | chown system system /sys/power/sb_state
vendor/init/hw/init.mt6762.rc:680 | boot | chmod 0660 /sys/power/sb_state
vendor/init/hw/init.mt6762.rc:683 | boot | chown system system /proc/hps/num_base_perf_serv
vendor/init/hw/init.mt6762.rc:684 | boot | chmod 0660 /proc/hps/num_base_perf_serv
vendor/init/hw/init.mt6762.rc:685 | boot | chown system system /proc/hps/num_limit_power_serv
vendor/init/hw/init.mt6762.rc:686 | boot | chmod 0660 /proc/hps/num_limit_power_serv
vendor/init/hw/init.mt6762.rc:687 | boot | chown system system /proc/hps/num_limit_ultra_power_saving
vendor/init/hw/init.mt6762.rc:688 | boot | chmod 0660 /proc/hps/num_limit_ultra_power_saving
vendor/init/hw/init.mt6762.rc:689 | boot | chown system system /proc/hps/down_threshold
vendor/init/hw/init.mt6762.rc:690 | boot | chmod 0660 /proc/hps/down_threshold
vendor/init/hw/init.mt6762.rc:691 | boot | chown system system /proc/hps/up_threshold
vendor/init/hw/init.mt6762.rc:692 | boot | chmod 0660 /proc/hps/up_threshold
vendor/init/hw/init.mt6762.rc:693 | boot | chown system system /proc/hps/rush_boost_enabled
vendor/init/hw/init.mt6762.rc:694 | boot | chmod 0660 /proc/hps/rush_boost_enabled
vendor/init/hw/init.mt6762.rc:695 | boot | chown system system /proc/hps/heavy_task_enabled
vendor/init/hw/init.mt6762.rc:696 | boot | chmod 0660 /proc/hps/heavy_task_enabled
vendor/init/hw/init.mt6762.rc:697 | boot | chown system system /proc/hps/power_mode
vendor/init/hw/init.mt6762.rc:698 | boot | chmod 0660 /proc/hps/power_mode
vendor/init/hw/init.mt6762.rc:699 | boot | chown system system /proc/hps/up_times
vendor/init/hw/init.mt6762.rc:700 | boot | chmod 0660 /proc/hps/up_times
vendor/init/hw/init.mt6762.rc:701 | boot | chown system system /proc/hps/down_times
vendor/init/hw/init.mt6762.rc:702 | boot | chmod 0660 /proc/hps/down_times
vendor/init/hw/init.mt6762.rc:705 | boot | chown system system /proc/ppm/mode
vendor/init/hw/init.mt6762.rc:706 | boot | chmod 0660 /proc/ppm/mode
vendor/init/hw/init.mt6762.rc:707 | boot | chown system system /proc/ppm/policy/perfserv_min_perf_idx
vendor/init/hw/init.mt6762.rc:708 | boot | chmod 0440 /proc/ppm/policy/perfserv_min_perf_idx
vendor/init/hw/init.mt6762.rc:709 | boot | chown system system /proc/ppm/policy/perfserv_max_perf_idx
vendor/init/hw/init.mt6762.rc:710 | boot | chmod 0440 /proc/ppm/policy/perfserv_max_perf_idx
vendor/init/hw/init.mt6762.rc:711 | boot | chown system system /proc/ppm/policy/perfserv_perf_idx
vendor/init/hw/init.mt6762.rc:712 | boot | chmod 0660 /proc/ppm/policy/perfserv_perf_idx
vendor/init/hw/init.mt6762.rc:713 | boot | chown system system /proc/ppm/policy/userlimit_min_cpu_freq
vendor/init/hw/init.mt6762.rc:714 | boot | chmod 0660 /proc/ppm/policy/userlimit_min_cpu_freq
vendor/init/hw/init.mt6762.rc:715 | boot | chown system system /proc/ppm/policy/userlimit_max_cpu_freq
vendor/init/hw/init.mt6762.rc:716 | boot | chmod 0660 /proc/ppm/policy/userlimit_max_cpu_freq
vendor/init/hw/init.mt6762.rc:717 | boot | chown system system /proc/ppm/policy/userlimit_min_cpu_core
vendor/init/hw/init.mt6762.rc:718 | boot | chmod 0660 /proc/ppm/policy/userlimit_min_cpu_core
vendor/init/hw/init.mt6762.rc:719 | boot | chown system system /proc/ppm/policy/userlimit_max_cpu_core
vendor/init/hw/init.mt6762.rc:720 | boot | chmod 0660 /proc/ppm/policy/userlimit_max_cpu_core
vendor/init/hw/init.mt6762.rc:721 | boot | chown system system /proc/ppm/policy/userlimit_cpu_core
vendor/init/hw/init.mt6762.rc:722 | boot | chmod 0660 /proc/ppm/policy/userlimit_cpu_core
vendor/init/hw/init.mt6762.rc:723 | boot | chown system system /proc/ppm/policy/userlimit_cpu_freq
vendor/init/hw/init.mt6762.rc:724 | boot | chmod 0660 /proc/ppm/policy/userlimit_cpu_freq
vendor/init/hw/init.mt6762.rc:725 | boot | chown system system /proc/ppm/dump_cluster_0_dvfs_table
vendor/init/hw/init.mt6762.rc:726 | boot | chmod 0440 /proc/ppm/dump_cluster_0_dvfs_table
vendor/init/hw/init.mt6762.rc:727 | boot | chown system system /proc/ppm/dump_cluster_1_dvfs_table
vendor/init/hw/init.mt6762.rc:728 | boot | chmod 0440 /proc/ppm/dump_cluster_1_dvfs_table
vendor/init/hw/init.mt6762.rc:729 | boot | chown system system /proc/ppm/dump_cluster_2_dvfs_table
vendor/init/hw/init.mt6762.rc:730 | boot | chmod 0440 /proc/ppm/dump_cluster_2_dvfs_table
vendor/init/hw/init.mt6762.rc:731 | boot | chown system system /proc/ppm/root_cluster
vendor/init/hw/init.mt6762.rc:732 | boot | chmod 0660 /proc/ppm/root_cluster
vendor/init/hw/init.mt6762.rc:733 | boot | chown system system /proc/ppm/policy/hica_variant
vendor/init/hw/init.mt6762.rc:734 | boot | chmod 0660 /proc/ppm/policy/hica_variant
vendor/init/hw/init.mt6762.rc:735 | boot | chown system system /proc/ppm/policy/hica_settings/L_ONLY_to_4L_LL/loading_hold_time
vendor/init/hw/init.mt6762.rc:736 | boot | chmod 0660 /proc/ppm/policy/hica_settings/L_ONLY_to_4L_LL/loading_hold_time
vendor/init/hw/init.mt6762.rc:739 | boot | chown system system /proc/cpu_loading/onoff
vendor/init/hw/init.mt6762.rc:740 | boot | chmod 0664 /proc/cpu_loading/onoff
vendor/init/hw/init.mt6762.rc:741 | boot | chown system system /proc/cpu_loading/overThrhld
vendor/init/hw/init.mt6762.rc:742 | boot | chmod 0664 /proc/cpu_loading/overThrhld
vendor/init/hw/init.mt6762.rc:743 | boot | chown system system /proc/cpu_loading/poltime_nsecs
vendor/init/hw/init.mt6762.rc:744 | boot | chmod 0664 /proc/cpu_loading/poltime_nsecs
vendor/init/hw/init.mt6762.rc:745 | boot | chown system system /proc/cpu_loading/poltime_secs
vendor/init/hw/init.mt6762.rc:746 | boot | chmod 0664 /proc/cpu_loading/poltime_secs
vendor/init/hw/init.mt6762.rc:747 | boot | chown system system /proc/cpu_loading/uevent_enable
vendor/init/hw/init.mt6762.rc:748 | boot | chmod 0664 /proc/cpu_loading/uevent_enable
vendor/init/hw/init.mt6762.rc:751 | boot | chown system system /proc/cpufreq/cpufreq_limited_by_hevc
vendor/init/hw/init.mt6762.rc:752 | boot | chmod 0660 /proc/cpufreq/cpufreq_limited_by_hevc
vendor/init/hw/init.mt6762.rc:753 | boot | chown system system /proc/cpufreq/cpufreq_limited_max_freq_by_user
vendor/init/hw/init.mt6762.rc:754 | boot | chmod 0660 /proc/cpufreq/cpufreq_limited_max_freq_by_user
vendor/init/hw/init.mt6762.rc:755 | boot | chown system system /proc/cpufreq/cpufreq_power_mode
vendor/init/hw/init.mt6762.rc:756 | boot | chmod 0660 /proc/cpufreq/cpufreq_power_mode
vendor/init/hw/init.mt6762.rc:757 | boot | chown system system /proc/cpufreq/enable_hw_gov
vendor/init/hw/init.mt6762.rc:758 | boot | chmod 0660 /proc/cpufreq/enable_hw_gov
vendor/init/hw/init.mt6762.rc:761 | boot | chown system system /proc/fliperfs/cg_enable
vendor/init/hw/init.mt6762.rc:762 | boot | chmod 0664 /proc/fliperfs/cg_enable
vendor/init/hw/init.mt6762.rc:763 | boot | chown system system /proc/fliperfs/cg_threshold
vendor/init/hw/init.mt6762.rc:764 | boot | chmod 0664 /proc/fliperfs/cg_threshold
vendor/init/hw/init.mt6762.rc:765 | boot | chown system system /proc/fliperfs/cg_threshold_ddr3
vendor/init/hw/init.mt6762.rc:766 | boot | chmod 0664 /proc/fliperfs/cg_threshold_ddr3
vendor/init/hw/init.mt6762.rc:767 | boot | chown system system /proc/fliperfs/perf
vendor/init/hw/init.mt6762.rc:768 | boot | chmod 0664 /proc/fliperfs/perf
vendor/init/hw/init.mt6762.rc:771 | boot | chown system system /proc/perfmgr/smart/smart_uevent_enable
vendor/init/hw/init.mt6762.rc:772 | boot | chmod 0660 /proc/perfmgr/smart/smart_uevent_enable
vendor/init/hw/init.mt6762.rc:773 | boot | chown system system /proc/perfmgr/smart/smart_force_isolate
vendor/init/hw/init.mt6762.rc:774 | boot | chmod 0660 /proc/perfmgr/smart/smart_force_isolate
vendor/init/hw/init.mt6762.rc:775 | boot | chown system system /proc/perfmgr/smart/app_is_sports
vendor/init/hw/init.mt6762.rc:776 | boot | chmod 0660 /proc/perfmgr/smart/app_is_sports
vendor/init/hw/init.mt6762.rc:777 | boot | chown system system /proc/perfmgr/smart/app_is_running
vendor/init/hw/init.mt6762.rc:778 | boot | chmod 0660 /proc/perfmgr/smart/app_is_running
vendor/init/hw/init.mt6762.rc:779 | boot | chown system system /proc/perfmgr/boost/cpuset_pid
vendor/init/hw/init.mt6762.rc:780 | boot | chmod 0660 /proc/perfmgr/boost/cpuset_pid
vendor/init/hw/init.mt6762.rc:781 | boot | chown system system /proc/perfmgr/eas/perfserv_ta_boost
vendor/init/hw/init.mt6762.rc:782 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_ta_boost
vendor/init/hw/init.mt6762.rc:783 | boot | chown system system /proc/perfmgr/eas/perfserv_fg_boost
vendor/init/hw/init.mt6762.rc:784 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_fg_boost
vendor/init/hw/init.mt6762.rc:785 | boot | chown system system /proc/perfmgr/eas/perfserv_bg_boost
vendor/init/hw/init.mt6762.rc:786 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_bg_boost
vendor/init/hw/init.mt6762.rc:787 | boot | chown system system /sys/devices/system/cpu/eas/stune_task_thresh
vendor/init/hw/init.mt6762.rc:788 | boot | chmod 0660 /sys/devices/system/cpu/eas/stune_task_thresh
vendor/init/hw/init.mt6762.rc:789 | boot | chown system system /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/hw/init.mt6762.rc:790 | boot | chmod 0660 /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/hw/init.mt6762.rc:791 | boot | chown system system /sys/devices/system/cpu/eas/enable
vendor/init/hw/init.mt6762.rc:792 | boot | chmod 0660 /sys/devices/system/cpu/eas/enable
vendor/init/hw/init.mt6762.rc:793 | boot | chown system system /sys/module/ged/parameters/gx_top_app_pid
vendor/init/hw/init.mt6762.rc:794 | boot | chmod 0660 /sys/module/ged/parameters/gx_top_app_pid
vendor/init/hw/init.mt6762.rc:795 | boot | chown system system /proc/perfmgr/fbc
vendor/init/hw/init.mt6762.rc:796 | boot | chmod 0664 /proc/perfmgr/fbc
vendor/init/hw/init.mt6762.rc:797 | boot | chown system system /proc/perfmgr/legacy/perfserv_core
vendor/init/hw/init.mt6762.rc:798 | boot | chmod 0664 /proc/perfmgr/legacy/perfserv_core
vendor/init/hw/init.mt6762.rc:799 | boot | chown system system /proc/perfmgr/legacy/perfserv_freq
vendor/init/hw/init.mt6762.rc:800 | boot | chmod 0664 /proc/perfmgr/legacy/perfserv_freq
vendor/init/hw/init.mt6762.rc:801 | boot | chown system system /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/hw/init.mt6762.rc:802 | boot | chmod 0664 /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/hw/init.mt6762.rc:803 | boot | chown system system /dev/cpuset/user_space_global_cpuset
vendor/init/hw/init.mt6762.rc:804 | boot | chmod 0664 /dev/cpuset/user_space_global_cpuset
vendor/init/hw/init.mt6762.rc:805 | boot | chown system system /dev/cpuset/cpus
vendor/init/hw/init.mt6762.rc:806 | boot | chmod 0664 /dev/cpuset/cpus
vendor/init/hw/init.mt6762.rc:807 | boot | chown system system /dev/cpuset/top-app/cpus
vendor/init/hw/init.mt6762.rc:808 | boot | chmod 0664 /dev/cpuset/top-app/cpus
vendor/init/hw/init.mt6762.rc:809 | boot | chown system system /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6762.rc:810 | boot | chmod 0664 /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6762.rc:811 | boot | chown system system /sys/kernel/debug/fpsgo/common/fpsgo_game_enable
vendor/init/hw/init.mt6762.rc:812 | boot | chmod 0664 /sys/kernel/debug/fpsgo/common/fpsgo_game_enable
vendor/init/hw/init.mt6762.rc:813 | boot | chown system system /proc/fbt_cpu/switch_fbt
vendor/init/hw/init.mt6762.rc:814 | boot | chmod 0664 /proc/fbt_cpu/switch_fbt
vendor/init/hw/init.mt6762.rc:815 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_level
vendor/init/hw/init.mt6762.rc:816 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_level
vendor/init/hw/init.mt6762.rc:817 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_error_threshold
vendor/init/hw/init.mt6762.rc:818 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_error_threshold
vendor/init/hw/init.mt6762.rc:819 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_quantile
vendor/init/hw/init.mt6762.rc:820 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_quantile
vendor/init/hw/init.mt6762.rc:821 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_window_size
vendor/init/hw/init.mt6762.rc:822 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_window_size
vendor/init/hw/init.mt6762.rc:823 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_freq
vendor/init/hw/init.mt6762.rc:824 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_freq
vendor/init/hw/init.mt6762.rc:825 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_fps
vendor/init/hw/init.mt6762.rc:826 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_fps
vendor/init/hw/init.mt6762.rc:827 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_force_vag
vendor/init/hw/init.mt6762.rc:828 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_force_vag
vendor/init/hw/init.mt6762.rc:829 | boot | chown system system /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/hw/init.mt6762.rc:830 | boot | chmod 0664 /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/hw/init.mt6762.rc:831 | boot | chown system system /sys/module/fbt_cpu/parameters/kmin
vendor/init/hw/init.mt6762.rc:832 | boot | chmod 0664 /sys/module/fbt_cpu/parameters/kmin
vendor/init/hw/init.mt6762.rc:833 | boot | chown system system /proc/fbt_cpu/switch_cpuset
vendor/init/hw/init.mt6762.rc:834 | boot | chmod 0664 /proc/fbt_cpu/switch_cpuset
vendor/init/hw/init.mt6762.rc:835 | boot | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/hw/init.mt6762.rc:836 | boot | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/hw/init.mt6762.rc:837 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/hw/init.mt6762.rc:838 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/hw/init.mt6762.rc:839 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/hw/init.mt6762.rc:840 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/hw/init.mt6762.rc:841 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/hw/init.mt6762.rc:842 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/hw/init.mt6762.rc:843 | boot | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/ddr
vendor/init/hw/init.mt6762.rc:844 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/dram_ctrl/ddr
vendor/init/hw/init.mt6762.rc:849 | boot | chown root radio /proc/net/vlan/config
vendor/init/hw/init.mt6762.rc:850 | boot | chmod 0640 /proc/net/vlan/config
vendor/init/hw/init.mt6762.rc:851 | boot | chown root radio /proc/net/ip6_tables_matches
vendor/init/hw/init.mt6762.rc:852 | boot | chown root radio /proc/net/ip6_tables_names
vendor/init/hw/init.mt6762.rc:853 | boot | chown root radio /proc/net/ip6_tables_targets
vendor/init/hw/init.mt6762.rc:854 | boot | chown root radio /proc/net/arp_tables_targets
vendor/init/hw/init.mt6762.rc:855 | boot | chown root radio /proc/net/arp_tables_matches
vendor/init/hw/init.mt6762.rc:856 | boot | chown root radio /proc/net/arp_tables_names
vendor/init/hw/init.mt6762.rc:857 | boot | chown root radio /proc/net/ip_tables_targets
vendor/init/hw/init.mt6762.rc:858 | boot | chown root radio /proc/net/ip_tables_matches
vendor/init/hw/init.mt6762.rc:859 | boot | chown root radio /proc/net/ip_tables_names
vendor/init/hw/init.mt6762.rc:860 | boot | chown root radio /proc/net/ip_conntrack_expect
vendor/init/hw/init.mt6762.rc:861 | boot | chown root radio /proc/net/ip_conntrack
vendor/init/hw/init.mt6762.rc:862 | boot | chown root radio /proc/net/nf_conntrack
vendor/init/hw/init.mt6762.rc:863 | boot | chown root radio /proc/net/nf_conntrack_expect
vendor/init/hw/init.mt6762.rc:864 | boot | chown root radio /proc/net/netfilter/nfnetlink_log
vendor/init/hw/init.mt6762.rc:865 | boot | chown root radio /proc/net/netfilter/nfnetlink_queue
vendor/init/hw/init.mt6762.rc:868 | boot | chown shell system /proc/driver/wmt_dbg
vendor/init/hw/init.mt6762.rc:869 | boot | chown shell system /proc/driver/wmt_aee
vendor/init/hw/init.mt6762.rc:872 | boot | chown system radio /dev/ttyGS3
vendor/init/hw/init.mt6762.rc:873 | boot | chmod 0660 /dev/ttyGS3
vendor/init/hw/init.mt6762.rc:876 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/rst_op
vendor/init/hw/init.mt6762.rc:877 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op
vendor/init/hw/init.mt6762.rc:878 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op
vendor/init/hw/init.mt6762.rc:879 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op
vendor/init/hw/init.mt6762.rc:880 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc
vendor/init/hw/init.mt6762.rc:881 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc
vendor/init/hw/init.mt6762.rc:882 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/btMAC
vendor/init/hw/init.mt6762.rc:883 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC
vendor/init/hw/init.mt6762.rc:884 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/longterm
vendor/init/hw/init.mt6762.rc:885 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm
vendor/init/hw/init.mt6762.rc:886 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/command
vendor/init/hw/init.mt6762.rc:887 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/command
vendor/init/hw/init.mt6762.rc:888 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/version
vendor/init/hw/init.mt6762.rc:889 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/version
vendor/init/hw/init.mt6762.rc:890 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/config
vendor/init/hw/init.mt6762.rc:891 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/config
vendor/init/hw/init.mt6762.rc:892 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/uart_op
vendor/init/hw/init.mt6762.rc:893 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op
vendor/init/hw/init.mt6762.rc:896 | boot | chmod 666 /dev/ttyS1
vendor/init/hw/init.mt6762.rc:897 | boot | chown system system /dev/ttyS1
vendor/init/hw/init.mt6762.rc:900 | boot | chmod 660 /dev/ttyGS0
vendor/init/hw/init.mt6762.rc:901 | boot | chown system radio /dev/ttyGS0
vendor/init/hw/init.mt6762.rc:902 | boot | chmod 660 /dev/ttyGS1
vendor/init/hw/init.mt6762.rc:903 | boot | chown system radio /dev/ttyGS1
vendor/init/hw/init.mt6762.rc:906 | property:ro.board.platform=mt6753 | chown system system /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
vendor/init/hw/init.mt6762.rc:907 | property:ro.board.platform=mt6753 | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
vendor/init/hw/init.mt6762.rc:908 | property:ro.board.platform=mt6753 | write /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq 1300000
vendor/init/hw/init.mt6762.rc:911 | property:vold.decrypt=trigger_reset_main | write /proc/bootprof "INIT:vold.decrypt=trigger_reset_main"
vendor/init/hw/init.mt6762.rc:912 | property:vold.decrypt=trigger_reset_main | setprop sys.boot_completed 0
vendor/init/hw/init.mt6762.rc:913 | property:vold.decrypt=trigger_reset_main | setprop dev.bootcomplete 0
vendor/init/hw/init.mt6762.rc:916 | property:vold.decrypt=trigger_post_fs_data | write /proc/bootprof "INIT:vold.decrypt=trigger_post_fs_data"
vendor/init/hw/init.mt6762.rc:919 | property:vold.decrypt=trigger_restart_min_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_restart_min_framework"
vendor/init/hw/init.mt6762.rc:922 | property:vold.decrypt=trigger_restart_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_restart_framework"
vendor/init/hw/init.mt6762.rc:925 | property:vold.decrypt=trigger_shutdown_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_shutdown_framework"
vendor/init/hw/init.mt6762.rc:926 | property:vold.decrypt=trigger_shutdown_framework | setprop sys.boot_completed 0
vendor/init/hw/init.mt6762.rc:927 | property:vold.decrypt=trigger_shutdown_framework | setprop dev.bootcomplete 0
vendor/init/hw/init.mt6762.rc:932 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.default 4096,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6762.rc:933 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.wifi    524288,1048576,2097152,262144,524288,1048576
vendor/init/hw/init.mt6762.rc:934 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.lte     524288,1048576,2097152,262144,524288,1048576
vendor/init/hw/init.mt6762.rc:935 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.umts    4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6762.rc:936 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hspa    4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6762.rc:937 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hsupa   4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6762.rc:938 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hsdpa   4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6762.rc:939 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hspap   4094,87380,1220608,4096,16384,1220608
vendor/init/hw/init.mt6762.rc:940 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.edge    4093,26280,35040,4096,16384,35040
vendor/init/hw/init.mt6762.rc:941 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.gprs    4092,8760,11680,4096,8760,11680
vendor/init/hw/init.mt6762.rc:943 | property:vold.decrypt=trigger_shutdown_framework | setprop persist.sys.strictmode.disable 1
vendor/init/hw/init.mt6762.rc:947 | early-init && property:ro.boot.boot_trace=1 | setprop debug.atrace.tags.enableflags 0x1fffffe
vendor/init/hw/init.mt6762.rc:951 | property:sys.boot_completed=1 | write /proc/bootprof "0"
vendor/init/hw/init.mt6762.rc:952 | property:sys.boot_completed=1 | write /proc/mtprintk ${persist.vendor.uartconsole.enable}
vendor/init/hw/init.mt6762.rc:956 | property:persist.vendor.uartconsole.enable=* | write /proc/mtprintk ${persist.vendor.uartconsole.enable}
vendor/init/hw/init.mt6762.rc:969 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/iostats 1
vendor/init/hw/init.mt6762.rc:970 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/read_ahead_kb 512
vendor/init/hw/init.mt6762.rc:971 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/nr_requests 128
vendor/init/hw/init.mt6762.rc:972 | property:sys.boot_completed=1 | write /sys/block/dm-0/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:973 | property:sys.boot_completed=1 | write /sys/block/dm-1/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:974 | property:sys.boot_completed=1 | write /sys/block/dm-2/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:975 | property:sys.boot_completed=1 | write /sys/block/dm-3/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:976 | property:sys.boot_completed=1 | write /sys/block/dm-4/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:977 | property:sys.boot_completed=1 | write /sys/block/dm-5/queue/read_ahead_kb 128
vendor/init/hw/init.mt6762.rc:981 | property:sys.boot_completed=1 | write /dev/stune/schedtune.boost 0
vendor/init/hw/init.mt6762.rc:982 | property:sys.boot_completed=1 | write /dev/stune/foreground/schedtune.boost 0
vendor/init/hw/init.mt6762.rc:983 | property:sys.boot_completed=1 | write /dev/stune/background/schedtune.boost 0
vendor/init/hw/init.mt6762.rc:984 | property:sys.boot_completed=1 | write /dev/stune/top-app/schedtune.boost 0
vendor/init/hw/init.mt6762.rc:985 | property:sys.boot_completed=1 | write /dev/stune/rt/schedtune.boost 0
vendor/init/hw/init.mt6762.rc:986 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/eas/enable 2
vendor/init/hw/init.mt6762.rc:987 | property:sys.boot_completed=1 | write /proc/cpufreq/cpufreq_sched_disable 0
vendor/init/hw/init.mt6762.rc:988 | property:sys.boot_completed=1 | chown system system /dev/stune/foreground/schedtune.prefer_idle
vendor/init/hw/init.mt6762.rc:989 | property:sys.boot_completed=1 | chown system system /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6762.rc:990 | property:sys.boot_completed=1 | chown system system /dev/stune/rt/schedtune.prefer_idle
vendor/init/hw/init.mt6762.rc:991 | property:sys.boot_completed=1 | write /dev/stune/foreground/schedtune.prefer_idle 0
vendor/init/hw/init.mt6762.rc:992 | property:sys.boot_completed=1 | write /dev/stune/top-app/schedtune.prefer_idle 0
vendor/init/hw/init.mt6762.rc:993 | property:sys.boot_completed=1 | write /dev/stune/rt/schedtune.prefer_idle 0
vendor/init/hw/init.mt6762.rc:994 | property:sys.boot_completed=1 | write /proc/sys/kernel/sched_migration_cost_ns 200000
vendor/init/hw/init.mt6762.rc:997 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/cpufreq/policy0/scaling_governor "schedplus"
vendor/init/hw/init.mt6762.rc:998 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/cpufreq/policy4/scaling_governor "schedplus"
vendor/init/hw/init.mt6762.rc:1001 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "0 0"
vendor/init/hw/init.mt6762.rc:1002 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "1 0"
vendor/init/hw/init.mt6762.rc:1003 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "2 0"
vendor/init/hw/init.mt6762.rc:1004 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "3 0"
vendor/init/hw/init.mt6762.rc:1005 | property:sys.boot_completed=1 | write /proc/sys/kernel/sched_stune_task_threshold "-1"
vendor/init/hw/init.mt6762.rc:1006 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/cpu_ctrl/boot_freq "-1 -1 -1 -1"
vendor/init/hw/init.mt6762.rc:1007 | property:sys.boot_completed=1 | write /sys/devices/platform/10012000.dvfsrc/helio-dvfsrc/dvfsrc_req_ddr_opp "-1"
vendor/init/hw/init.mt6762.rc:1059 | property:persist.vendor.mediatek.fg.disable=1 | write /sys/devices/platform/battery_meter/FG_daemon_disable 1
vendor/init/hw/init.mt6762.rc:1060 | property:persist.vendor.mediatek.fg.disable=1 | write /sys/bus/platform/devices/battery/FG_daemon_disable 1
vendor/init/hw/init.mt6762.rc:1063 | property:persist.vendor.mediatek.fg.force25c=1 | write /sys/devices/platform/battery_meter/FG_drv_force25c 1
vendor/init/hw/init.mt6762.rc:1064 | property:persist.vendor.mediatek.fg.force25c=1 | write /sys/bus/platform/devices/battery/Battery_Temperature 25
vendor/init/hw/init.mt6762.rc:1067 | property:persist.vendor.mediatek.fg.force25c=0 | write /sys/devices/platform/battery_meter/FG_drv_force25c 0
vendor/init/hw/init.mt6762.rc:1068 | property:persist.vendor.mediatek.fg.force25c=0 | write /sys/bus/platform/devices/battery/Battery_Temperature 65535
vendor/init/hw/init.mt6762.rc:1071 | property:persist.vendor.mediatek.fg.log.enable=1 | write /sys/devices/platform/battery_meter/FG_daemon_log_level 7
vendor/init/hw/init.mt6762.rc:1072 | property:persist.vendor.mediatek.fg.log.enable=1 | write /sys/bus/platform/devices/battery/FG_daemon_log_level 7
vendor/init/hw/init.mt6762.rc:1073 | property:persist.vendor.mediatek.fg.log.enable=1 | write /proc/sys/kernel/printk 8
vendor/init/hw/init.mt6762.rc:1094 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start meta_tst
vendor/init/hw/init.mt6762.rc:1095 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start loghidlvendorservice
vendor/init/hw/init.mt6762.rc:1096 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start loghidlsysservice
vendor/init/hw/init.mt6762.rc:1101 | init | symlink /dev/block/platform/bootdevice /dev/block/bootdevice
vendor/init/hw/init.mt6762.rc:1105 | boot | insmod /vendor/lib/modules/fpsgo.ko
vendor/init/hw/init.mt6765.rc:19 | early-init | write /proc/bootprof "INIT:early-init"
vendor/init/hw/init.mt6765.rc:21 | early-init | write /proc/perfmgr/boost_ctrl/cpu_ctrl/boot_freq "0 0 5 0"
vendor/init/hw/init.mt6765.rc:22 | early-init | write /sys/devices/platform/10012000.dvfsrc/helio-dvfsrc/dvfsrc_req_ddr_opp 0
vendor/init/hw/init.mt6765.rc:23 | early-init | mount debugfs debugfs /sys/kernel/debug
vendor/init/hw/init.mt6765.rc:24 | early-init | chmod 0755 /sys/kernel/debug
vendor/init/hw/init.mt6765.rc:28 | init | mkdir /dev/stune/io
vendor/init/hw/init.mt6765.rc:29 | init | chown root system /dev/stune/io
vendor/init/hw/init.mt6765.rc:30 | init | chown root system /dev/stune/io/tasks
vendor/init/hw/init.mt6765.rc:31 | init | chmod 0664 /dev/stune/io/tasks
vendor/init/hw/init.mt6765.rc:34 | init | symlink /sdcard /mnt/sdcard
vendor/init/hw/init.mt6765.rc:40 | init | mkdir /mnt/vendor/protect_f 0771 system system
vendor/init/hw/init.mt6765.rc:41 | init | mkdir /mnt/vendor/protect_s 0771 system system
vendor/init/hw/init.mt6765.rc:42 | init | mkdir /mnt/vendor/nvcfg 0771 system system
vendor/init/hw/init.mt6765.rc:45 | init | mkdir /mnt/vendor/nvdata 0771 system system
vendor/init/hw/init.mt6765.rc:47 | init | mkdir /custom
vendor/init/hw/init.mt6765.rc:49 | init | mkdir /mnt/cd-rom 0000 system system
vendor/init/hw/init.mt6765.rc:52 | init | chown root system /proc/lk_env
vendor/init/hw/init.mt6765.rc:53 | init | chmod 0660 /proc/lk_env
vendor/init/hw/init.mt6765.rc:56 | init | write /proc/sys/net/core/wmem_max 8388608
vendor/init/hw/init.mt6765.rc:57 | init | write /proc/sys/net/core/rmem_max 8388608
vendor/init/hw/init.mt6765.rc:60 | init | write /dev/cpuset/foreground/cpus 0-7
vendor/init/hw/init.mt6765.rc:61 | init | write /dev/cpuset/foreground/boost/cpus 0-7
vendor/init/hw/init.mt6765.rc:62 | init | write /dev/cpuset/background/cpus 4-7
vendor/init/hw/init.mt6765.rc:63 | init | write /dev/cpuset/system-background/cpus 2-7
vendor/init/hw/init.mt6765.rc:64 | init | write /dev/cpuset/top-app/cpus 0-7
vendor/init/hw/init.mt6765.rc:67 | init | write /dev/cpuctl/bg_non_interactive/cpu.rt_runtime_us 100000
vendor/init/hw/init.mt6765.rc:70 | late-init | write /proc/bootprof "INIT:late-init"
vendor/init/hw/init.mt6765.rc:73 | late-init | write /sys/kernel/rcu_expedited 1
vendor/init/hw/init.mt6765.rc:78 | charger | write /proc/hps/enabled 0
vendor/init/hw/init.mt6765.rc:79 | charger | write /sys/devices/system/cpu/cpu0/online 1
vendor/init/hw/init.mt6765.rc:80 | charger | write /sys/devices/system/cpu/cpu1/online 1
vendor/init/hw/init.mt6765.rc:81 | charger | write /sys/devices/system/cpu/cpu2/online 1
vendor/init/hw/init.mt6765.rc:82 | charger | write /sys/devices/system/cpu/cpu3/online 1
vendor/init/hw/init.mt6765.rc:83 | charger | write /sys/devices/system/cpu/cpu7/online 0
vendor/init/hw/init.mt6765.rc:84 | charger | write /sys/devices/system/cpu/cpu6/online 0
vendor/init/hw/init.mt6765.rc:85 | charger | write /sys/devices/system/cpu/cpu5/online 0
vendor/init/hw/init.mt6765.rc:86 | charger | write /sys/devices/system/cpu/cpu4/online 0
vendor/init/hw/init.mt6765.rc:88 | charger | exec /system/bin/e2fsck -f -p /dev/block/platform/bootdevice/by-name/cache
vendor/init/hw/init.mt6765.rc:89 | charger | mount ext4 /dev/block/platform/bootdevice/by-name/cache /cache rw wait
vendor/init/hw/init.mt6765.rc:90 | charger | chmod 0666 /dev/kmsg
vendor/init/hw/init.mt6765.rc:91 | charger | chmod 0775 /mnt/vendor
vendor/init/hw/init.mt6765.rc:92 | charger | mkdir /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:93 | charger | mount ext4 /dev/block/platform/bootdevice/by-name/nvcfg /mnt/vendor/nvcfg rw wait
vendor/init/hw/init.mt6765.rc:94 | charger | chown system system /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:95 | charger | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:96 | charger | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:97 | charger | write /sys/devices/platform/battery_meter/FG_daemon_log_level 7
vendor/init/hw/init.mt6765.rc:98 | charger | write /sys/bus/platform/devices/battery/FG_daemon_log_level 7
vendor/init/hw/init.mt6765.rc:100 | charger | chown system system /sys/power/autosleep
vendor/init/hw/init.mt6765.rc:101 | charger | chown system system /sys/power/state
vendor/init/hw/init.mt6765.rc:102 | charger | chown system system /sys/power/wakeup_count
vendor/init/hw/init.mt6765.rc:103 | charger | chown radio wakelock /sys/power/wake_lock
vendor/init/hw/init.mt6765.rc:104 | charger | chown radio wakelock /sys/power/wake_unlock
vendor/init/hw/init.mt6765.rc:105 | charger | chmod 0660 /sys/power/state
vendor/init/hw/init.mt6765.rc:106 | charger | chmod 0660 /sys/power/wake_lock
vendor/init/hw/init.mt6765.rc:107 | charger | chmod 0660 /sys/power/wake_unlock
vendor/init/hw/init.mt6765.rc:108 | charger | chmod 0660 /sys/power/wakeup_count
vendor/init/hw/init.mt6765.rc:109 | charger | write /sys/power/pm_async 0
vendor/init/hw/init.mt6765.rc:110 | charger | start fuelgauged
vendor/init/hw/init.mt6765.rc:111 | charger | start fuelgauged_nvram
vendor/init/hw/init.mt6765.rc:113 | charger | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6765.rc:114 | charger | chown system system /sys/class/leds/red/brightness
vendor/init/hw/init.mt6765.rc:115 | charger | chown system system /sys/class/leds/green/brightness
vendor/init/hw/init.mt6765.rc:117 | charger | start servicemanager
vendor/init/hw/init.mt6765.rc:118 | charger | start hwservicemanager
vendor/init/hw/init.mt6765.rc:119 | charger | start light-hal-2-0
vendor/init/hw/init.mt6765.rc:120 | charger | start health-hal-2-0
vendor/init/hw/init.mt6765.rc:123 | fs | write /proc/bootprof "INIT:Mount_START"
vendor/init/hw/init.mt6765.rc:124 | fs | write /sys/kernel/debug/tracing/trace_marker "B|1|Mount"
vendor/init/hw/init.mt6765.rc:125 | fs | wait_for_prop hwservicemanager.ready "true"
vendor/init/hw/init.mt6765.rc:126 | fs | start vendor.boot-hal-1-0
vendor/init/hw/init.mt6765.rc:127 | fs | mount_all /vendor/etc/fstab.mt6765
vendor/init/hw/init.mt6765.rc:128 | fs | write /sys/kernel/debug/tracing/trace_marker "E"
vendor/init/hw/init.mt6765.rc:129 | fs | write /proc/bootprof "INIT:Mount_END"
vendor/init/hw/init.mt6765.rc:133 | fs | mount iso9660 loop@/vendor/etc/iAmCdRom.iso /mnt/cd-rom ro
vendor/init/hw/init.mt6765.rc:136 | post-fs | write /proc/bootprof "INIT:post-fs"
vendor/init/hw/init.mt6765.rc:139 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "0 100"
vendor/init/hw/init.mt6765.rc:140 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "1 100"
vendor/init/hw/init.mt6765.rc:141 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "2 100"
vendor/init/hw/init.mt6765.rc:142 | post-fs | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "3 100"
vendor/init/hw/init.mt6765.rc:143 | post-fs | write /proc/sys/kernel/sched_stune_task_threshold 0
vendor/init/hw/init.mt6765.rc:146 | post-fs | symlink /sdcard /storage/sdcard0
vendor/init/hw/init.mt6765.rc:149 | post-fs | chown radio system /proc/freqhopping/dramc
vendor/init/hw/init.mt6765.rc:150 | post-fs | chown radio system /proc/freqhopping/dumpregs
vendor/init/hw/init.mt6765.rc:151 | post-fs | chown radio system /proc/freqhopping/freqhopping_debug
vendor/init/hw/init.mt6765.rc:152 | post-fs | chown radio system /proc/freqhopping/status
vendor/init/hw/init.mt6765.rc:153 | post-fs | chown radio system /proc/freqhopping/userdef
vendor/init/hw/init.mt6765.rc:156 | post-fs | symlink /dev/block/sda /dev/block/mmcblk0boot0
vendor/init/hw/init.mt6765.rc:157 | post-fs | symlink /dev/block/sdb /dev/block/mmcblk0boot1
vendor/init/hw/init.mt6765.rc:158 | post-fs | symlink /dev/block/mmcblk0boot0 /dev/block/platform/bootdevice/by-name/preloader_a
vendor/init/hw/init.mt6765.rc:159 | post-fs | symlink /dev/block/mmcblk0boot1 /dev/block/platform/bootdevice/by-name/preloader_b
vendor/init/hw/init.mt6765.rc:162 | post-fs | chown system system /proc/bootprof
vendor/init/hw/init.mt6765.rc:163 | post-fs | chmod 0664 /proc/bootprof
vendor/init/hw/init.mt6765.rc:164 | post-fs | chown root system /proc/mtprof/cputime
vendor/init/hw/init.mt6765.rc:165 | post-fs | chown root system /proc/mtprof/reboot_pid
vendor/init/hw/init.mt6765.rc:167 | post-fs | chown system system /mnt/vendor/protect_f
vendor/init/hw/init.mt6765.rc:168 | post-fs | chmod 0770 /mnt/vendor/protect_f
vendor/init/hw/init.mt6765.rc:170 | post-fs | chown system system /mnt/vendor/protect_s
vendor/init/hw/init.mt6765.rc:171 | post-fs | chmod 0770 /mnt/vendor/protect_s
vendor/init/hw/init.mt6765.rc:174 | post-fs-data | write /proc/bootprof "INIT:post-fs-data"
vendor/init/hw/init.mt6765.rc:177 | post-fs-data | write /sys/block/mmcblk0/queue/iostats 0
vendor/init/hw/init.mt6765.rc:178 | post-fs-data | write /sys/block/mmcblk0/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:179 | post-fs-data | write /sys/block/mmcblk0/queue/nr_requests 256
vendor/init/hw/init.mt6765.rc:180 | post-fs-data | write /sys/block/dm-0/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:181 | post-fs-data | write /sys/block/dm-1/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:182 | post-fs-data | write /sys/block/dm-2/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:183 | post-fs-data | write /sys/block/dm-3/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:184 | post-fs-data | write /sys/block/dm-4/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:185 | post-fs-data | write /sys/block/dm-5/queue/read_ahead_kb 2048
vendor/init/hw/init.mt6765.rc:187 | post-fs-data | chown system system /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:188 | post-fs-data | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:189 | post-fs-data | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/init.mt6765.rc:190 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg
vendor/init/hw/init.mt6765.rc:191 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg/old_fg_data
vendor/init/hw/init.mt6765.rc:192 | post-fs-data | chown system system /mnt/vendor/nvcfg/fg/car_tune_value
vendor/init/hw/init.mt6765.rc:198 | post-fs-data | chown root system /mnt/vendor/nvdata
vendor/init/hw/init.mt6765.rc:199 | post-fs-data | chmod 0771 /mnt/vendor/nvdata
vendor/init/hw/init.mt6765.rc:200 | post-fs-data | mkdir /mnt/vendor/nvdata/media 0771 media audio
vendor/init/hw/init.mt6765.rc:203 | post-fs-data | mkdir /mnt/vendor/cct
vendor/init/hw/init.mt6765.rc:204 | post-fs-data | chown root system /mnt/vendor/cct
vendor/init/hw/init.mt6765.rc:205 | post-fs-data | chmod 0771 /mnt/vendor/cct
vendor/init/hw/init.mt6765.rc:208 | post-fs-data | mkdir /data/vendor/flash
vendor/init/hw/init.mt6765.rc:209 | post-fs-data | chown root system /data/vendor/flash
vendor/init/hw/init.mt6765.rc:210 | post-fs-data | chmod 0771 /data/vendor/flash
vendor/init/hw/init.mt6765.rc:213 | post-fs-data | mkdir /data/vendor/shading
vendor/init/hw/init.mt6765.rc:214 | post-fs-data | chown root system /data/vendor/shading
vendor/init/hw/init.mt6765.rc:215 | post-fs-data | chmod 0771 /data/vendor/shading
vendor/init/hw/init.mt6765.rc:218 | post-fs-data | mkdir /data/vendor/shading_otp
vendor/init/hw/init.mt6765.rc:219 | post-fs-data | chown root system /data/vendor/shading_otp
vendor/init/hw/init.mt6765.rc:220 | post-fs-data | chmod 0771 /data/vendor/shading_otp
vendor/init/hw/init.mt6765.rc:223 | post-fs-data | mkdir /data/vendor/camera_dump
vendor/init/hw/init.mt6765.rc:224 | post-fs-data | chown root system /data/vendor/camera_dump
vendor/init/hw/init.mt6765.rc:225 | post-fs-data | chmod 0771 /data/vendor/camera_dump
vendor/init/hw/init.mt6765.rc:228 | post-fs-data | mkdir /data/vendor/dipdebug
vendor/init/hw/init.mt6765.rc:229 | post-fs-data | chown root system /data/vendor/dipdebug
vendor/init/hw/init.mt6765.rc:230 | post-fs-data | chmod 0771 /data/vendor/dipdebug
vendor/init/hw/init.mt6765.rc:233 | post-fs-data | mkdir /data/vendor/flicker
vendor/init/hw/init.mt6765.rc:234 | post-fs-data | chown root system /data/vendor/flicker
vendor/init/hw/init.mt6765.rc:235 | post-fs-data | chmod 0771 /data/vendor/flicker
vendor/init/hw/init.mt6765.rc:238 | post-fs-data | restorecon_recursive /mnt/vendor/nvdata
vendor/init/hw/init.mt6765.rc:242 | post-fs-data | chmod 0660 /dev/rfkill
vendor/init/hw/init.mt6765.rc:245 | post-fs-data | mkdir /dev/radio 0770 radio radio
vendor/init/hw/init.mt6765.rc:249 | post-fs-data | chmod 0775 /vendor/xbin/MPED
vendor/init/hw/init.mt6765.rc:250 | post-fs-data | chmod 0666 /dev/gps_emi
vendor/init/hw/init.mt6765.rc:255 | post-fs-data | chmod 0220 /sys/class/misc/scp/scp_mobile_log
vendor/init/hw/init.mt6765.rc:256 | post-fs-data | chown root system /sys/class/misc/scp/scp_mobile_log
vendor/init/hw/init.mt6765.rc:257 | post-fs-data | chmod 0220 /sys/class/misc/scp/scp_log_flush
vendor/init/hw/init.mt6765.rc:258 | post-fs-data | chown root system /sys/class/misc/scp/scp_log_flush
vendor/init/hw/init.mt6765.rc:259 | post-fs-data | chmod 0440 /dev/scp
vendor/init/hw/init.mt6765.rc:260 | post-fs-data | chown root system /dev/scp
vendor/init/hw/init.mt6765.rc:263 | post-fs-data | chmod 0620 /sys/class/misc/sspm/sspm_mobile_log
vendor/init/hw/init.mt6765.rc:264 | post-fs-data | chown root system /sys/class/misc/sspm/sspm_mobile_log
vendor/init/hw/init.mt6765.rc:265 | post-fs-data | chmod 0440 /dev/sspm
vendor/init/hw/init.mt6765.rc:266 | post-fs-data | chown root system /dev/sspm
vendor/init/hw/init.mt6765.rc:269 | post-fs-data | chmod 444 /etc/radvd/radvd.conf
vendor/init/hw/init.mt6765.rc:273 | post-fs-data | chmod 666 /dev/mali0
vendor/init/hw/init.mt6765.rc:278 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_calmat
vendor/init/hw/init.mt6765.rc:279 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time
vendor/init/hw/init.mt6765.rc:280 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_mode
vendor/init/hw/init.mt6765.rc:281 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time0
vendor/init/hw/init.mt6765.rc:282 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_debounce_time1
vendor/init/hw/init.mt6765.rc:283 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_spl_num
vendor/init/hw/init.mt6765.rc:284 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_pressure_threshold
vendor/init/hw/init.mt6765.rc:285 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_auto_time_interval
vendor/init/hw/init.mt6765.rc:286 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_sample_cnt
vendor/init/hw/init.mt6765.rc:287 | post-fs-data | chown root diag /sys/module/tpd_setting/parameters/tpd_em_asamp
vendor/init/hw/init.mt6765.rc:288 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log
vendor/init/hw/init.mt6765.rc:289 | post-fs-data | chown root diag /sys/module/tpd_debug/parameters/tpd_em_log_to_fs
vendor/init/hw/init.mt6765.rc:291 | post-fs-data | chmod 0666 /dev/pmem_multimedia
vendor/init/hw/init.mt6765.rc:297 | post-fs-data | chown root radio /dev/ptmx
vendor/init/hw/init.mt6765.rc:300 | post-fs-data | chmod 660 /dev/alarm
vendor/init/hw/init.mt6765.rc:301 | post-fs-data | chown root system /dev/alarm
vendor/init/hw/init.mt6765.rc:305 | post-fs-data | chmod 0444 /dev/m4u
vendor/init/hw/init.mt6765.rc:309 | post-fs-data | setrlimit 8 67108864 67108864
vendor/init/hw/init.mt6765.rc:312 | post-fs-data | setrlimit 8 -1 -1
vendor/init/hw/init.mt6765.rc:315 | post-fs-data | chmod 0644 /dev/mtk_cmdq
vendor/init/hw/init.mt6765.rc:316 | post-fs-data | chown system system /dev/mtk_cmdq
vendor/init/hw/init.mt6765.rc:319 | post-fs-data | chmod 0644 /dev/mdp_sync
vendor/init/hw/init.mt6765.rc:320 | post-fs-data | chown system system /dev/mdp_sync
vendor/init/hw/init.mt6765.rc:323 | post-fs-data | chmod 0640 /dev/offloadservice
vendor/init/hw/init.mt6765.rc:324 | post-fs-data | chown media media /dev/offloadservice
vendor/init/hw/init.mt6765.rc:327 | post-fs-data | chmod 0640 /dev/audio_ipi
vendor/init/hw/init.mt6765.rc:328 | post-fs-data | chown media media /dev/audio_ipi
vendor/init/hw/init.mt6765.rc:331 | post-fs-data | chmod 0640 /dev/audio_scp
vendor/init/hw/init.mt6765.rc:332 | post-fs-data | chown media media /dev/audio_scp
vendor/init/hw/init.mt6765.rc:333 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/rt5509_param.0/prop_param
vendor/init/hw/init.mt6765.rc:334 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/rt5509_param.1/prop_param
vendor/init/hw/init.mt6765.rc:335 | post-fs-data | chown audioserver audio /sys/bus/platform/devices/mt6660-param.0/prop_params
vendor/init/hw/init.mt6765.rc:338 | post-fs-data | chown root radio /sys/devices/platform/Accdet_Driver/driver/accdet_call_state
vendor/init/hw/init.mt6765.rc:341 | post-fs-data | chmod 0666 /dev/goodix_fp
vendor/init/hw/init.mt6765.rc:345 | post-fs-data | chmod 0660 /dev/Vcodec
vendor/init/hw/init.mt6765.rc:346 | post-fs-data | chown media system /dev/Vcodec
vendor/init/hw/init.mt6765.rc:350 | post-fs-data | chmod 0660 /dev/MJC
vendor/init/hw/init.mt6765.rc:351 | post-fs-data | chown media system /dev/MJC
vendor/init/hw/init.mt6765.rc:354 | post-fs-data | chown radio radio /sys/devices/platform/mtk-kpd/driver/kpd_call_state
vendor/init/hw/init.mt6765.rc:357 | post-fs-data | chown radio radio /sys/devices/platform/battery/Charging_CallState
vendor/init/hw/init.mt6765.rc:360 | post-fs-data | chown radio system /sys/devices/platform/mt-pmic/low_battery_protect_stop
vendor/init/hw/init.mt6765.rc:361 | post-fs-data | chown radio system /sys/devices/platform/mt-pmic/low_battery_protect_ut
vendor/init/hw/init.mt6765.rc:364 | post-fs-data | mkdir /data/vendor_de/meta 0770 system system
vendor/init/hw/init.mt6765.rc:365 | post-fs-data | mkdir /data/vendor_de/meta/mddb 0770 system system
vendor/init/hw/init.mt6765.rc:368 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsopmode
vendor/init/hw/init.mt6765.rc:369 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsreptxy
vendor/init/hw/init.mt6765.rc:370 | post-fs-data | chown system /sys/devices/platform/msensor/driver/cpsreptz
vendor/init/hw/init.mt6765.rc:371 | post-fs-data | chown system /sys/devices/platform/gsensor/driver/cpsopmode
vendor/init/hw/init.mt6765.rc:372 | post-fs-data | chown system /sys/devices/platform/gsensor/driver/cpsrange
vendor/init/hw/init.mt6765.rc:374 | post-fs-data | chown system /sys/class/input/input1/enable
vendor/init/hw/init.mt6765.rc:375 | post-fs-data | chown system /sys/class/input/input1/delay
vendor/init/hw/init.mt6765.rc:376 | post-fs-data | chown system /sys/class/input/input1/wake
vendor/init/hw/init.mt6765.rc:377 | post-fs-data | chown system /sys/class/input/input1/offsets
vendor/init/hw/init.mt6765.rc:378 | post-fs-data | chown system /sys/class/input/input2/enable
vendor/init/hw/init.mt6765.rc:379 | post-fs-data | chown system /sys/class/input/input2/delay
vendor/init/hw/init.mt6765.rc:380 | post-fs-data | chown system /sys/class/input/input2/wake
vendor/init/hw/init.mt6765.rc:381 | post-fs-data | chown system /sys/class/input/input2/offsets
vendor/init/hw/init.mt6765.rc:382 | post-fs-data | chown system /sys/class/input/input3/enable
vendor/init/hw/init.mt6765.rc:383 | post-fs-data | chown system /sys/class/input/input3/delay
vendor/init/hw/init.mt6765.rc:384 | post-fs-data | chown system /sys/class/input/input3/wake
vendor/init/hw/init.mt6765.rc:385 | post-fs-data | chown system /sys/class/input/input3/offsets
vendor/init/hw/init.mt6765.rc:386 | post-fs-data | chown system /sys/class/input/input4/enable
vendor/init/hw/init.mt6765.rc:387 | post-fs-data | chown system /sys/class/input/input4/delay
vendor/init/hw/init.mt6765.rc:388 | post-fs-data | chown system /sys/class/input/input4/wake
vendor/init/hw/init.mt6765.rc:389 | post-fs-data | chown system /sys/class/input/input4/offsets
vendor/init/hw/init.mt6765.rc:390 | post-fs-data | chown system /sys/class/input/input5/enable
vendor/init/hw/init.mt6765.rc:391 | post-fs-data | chown system /sys/class/input/input5/delay
vendor/init/hw/init.mt6765.rc:392 | post-fs-data | chown system /sys/class/input/input5/wake
vendor/init/hw/init.mt6765.rc:393 | post-fs-data | chown system /sys/class/input/input5/offsets
vendor/init/hw/init.mt6765.rc:394 | post-fs-data | chown system /sys/class/input/input6/enable
vendor/init/hw/init.mt6765.rc:395 | post-fs-data | chown system /sys/class/input/input6/delay
vendor/init/hw/init.mt6765.rc:396 | post-fs-data | chown system /sys/class/input/input6/wake
vendor/init/hw/init.mt6765.rc:397 | post-fs-data | chown system /sys/class/input/input6/offsets
vendor/init/hw/init.mt6765.rc:398 | post-fs-data | chown system /sys/class/input/input7/enable
vendor/init/hw/init.mt6765.rc:399 | post-fs-data | chown system /sys/class/input/input7/delay
vendor/init/hw/init.mt6765.rc:400 | post-fs-data | chown system /sys/class/input/input7/wake
vendor/init/hw/init.mt6765.rc:401 | post-fs-data | chown system /sys/class/input/input7/offsets
vendor/init/hw/init.mt6765.rc:402 | post-fs-data | chown system /sys/class/input/input8/enable
vendor/init/hw/init.mt6765.rc:403 | post-fs-data | chown system /sys/class/input/input8/delay
vendor/init/hw/init.mt6765.rc:404 | post-fs-data | chown system /sys/class/input/input8/wake
vendor/init/hw/init.mt6765.rc:405 | post-fs-data | chown system /sys/class/input/input8/offsets
vendor/init/hw/init.mt6765.rc:406 | post-fs-data | chown system /sys/class/input/input9/enable
vendor/init/hw/init.mt6765.rc:407 | post-fs-data | chown system /sys/class/input/input9/delay
vendor/init/hw/init.mt6765.rc:408 | post-fs-data | chown system /sys/class/input/input9/wake
vendor/init/hw/init.mt6765.rc:409 | post-fs-data | chown system /sys/class/input/input9/offsets
vendor/init/hw/init.mt6765.rc:410 | post-fs-data | chown system /sys/class/input/input10/enable
vendor/init/hw/init.mt6765.rc:411 | post-fs-data | chown system /sys/class/input/input10/delay
vendor/init/hw/init.mt6765.rc:412 | post-fs-data | chown system /sys/class/input/input10/wake
vendor/init/hw/init.mt6765.rc:413 | post-fs-data | chown system /sys/class/input/input10/offsets
vendor/init/hw/init.mt6765.rc:416 | post-fs-data | chown root diag /sys/bus/platform/drivers/eint/current_eint
vendor/init/hw/init.mt6765.rc:419 | post-fs-data | chmod 0664 /dev/graphics/fb0
vendor/init/hw/init.mt6765.rc:420 | post-fs-data | chown system graphics /dev/graphics/fb0
vendor/init/hw/init.mt6765.rc:422 | post-fs-data | chmod 0664 /dev/mtk_disp_mgr
vendor/init/hw/init.mt6765.rc:423 | post-fs-data | chown system graphics /dev/mtk_disp_mgr
vendor/init/hw/init.mt6765.rc:425 | post-fs-data | chmod 0660 /dev/mtkfb_vsync
vendor/init/hw/init.mt6765.rc:426 | post-fs-data | chown system graphics /dev/mtkfb_vsync
vendor/init/hw/init.mt6765.rc:428 | post-fs-data | chmod 0660 /dev/sw_sync
vendor/init/hw/init.mt6765.rc:429 | post-fs-data | chown system graphics /dev/sw_sync
vendor/init/hw/init.mt6765.rc:432 | post-fs-data | chown root diag /vendor/bin/baseband_regdump
vendor/init/hw/init.mt6765.rc:433 | post-fs-data | chmod 4750 /vendor/bin/baseband_regdump
vendor/init/hw/init.mt6765.rc:436 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access_bank0
vendor/init/hw/init.mt6765.rc:437 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access_bank1
vendor/init/hw/init.mt6765.rc:438 | post-fs-data | chown root radio /sys/devices/platform/mt6311-user/mt6311_access
vendor/init/hw/init.mt6765.rc:439 | post-fs-data | chown root radio /sys/devices/platform/mt-pmic/pmic_access
vendor/init/hw/init.mt6765.rc:440 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access_bank0
vendor/init/hw/init.mt6765.rc:441 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access_bank1
vendor/init/hw/init.mt6765.rc:442 | post-fs-data | chmod 0664 /sys/devices/platform/mt6311-user/mt6311_access
vendor/init/hw/init.mt6765.rc:443 | post-fs-data | chmod 0664 /sys/devices/platform/mt-pmic/pmic_access
vendor/init/hw/init.mt6765.rc:446 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_debug_mode
vendor/init/hw/init.mt6765.rc:447 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_mode
vendor/init/hw/init.mt6765.rc:448 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_period
vendor/init/hw/init.mt6765.rc:449 | post-fs-data | chown root radio /proc/cpu_ss/cpu_ss_period_mode
vendor/init/hw/init.mt6765.rc:450 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_debug_mode
vendor/init/hw/init.mt6765.rc:451 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_mode
vendor/init/hw/init.mt6765.rc:452 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_period
vendor/init/hw/init.mt6765.rc:453 | post-fs-data | chmod 0664 /proc/cpu_ss/cpu_ss_period_mode
vendor/init/hw/init.mt6765.rc:456 | post-fs-data | chown root diag /sys/bus/platform/drivers/eint/current_eint
vendor/init/hw/init.mt6765.rc:459 | post-fs-data | chmod 0660 /dev/sec
vendor/init/hw/init.mt6765.rc:460 | post-fs-data | chown root system /dev/sec
vendor/init/hw/init.mt6765.rc:463 | post-fs-data | chmod 0660 /dev/fm
vendor/init/hw/init.mt6765.rc:464 | post-fs-data | chown media media /dev/fm
vendor/init/hw/init.mt6765.rc:469 | post-fs-data | chmod 0440 /dev/devmap
vendor/init/hw/init.mt6765.rc:470 | post-fs-data | chown root system /dev/devmap
vendor/init/hw/init.mt6765.rc:473 | post-fs-data | chown bluetooth bluetooth /dev/hid-keyboard
vendor/init/hw/init.mt6765.rc:474 | post-fs-data | chmod 0660 /dev/hid-keyboard
vendor/init/hw/init.mt6765.rc:476 | post-fs-data | chown system net_bt_admin /dev/uinput
vendor/init/hw/init.mt6765.rc:479 | post-fs-data | chown system media /dev/uibc
vendor/init/hw/init.mt6765.rc:480 | post-fs-data | chmod 0660 /dev/uibc
vendor/init/hw/init.mt6765.rc:483 | post-fs-data | chmod 0664 /dev/TV-out
vendor/init/hw/init.mt6765.rc:486 | post-fs-data | chown media system /dev/hdmitx
vendor/init/hw/init.mt6765.rc:487 | post-fs-data | chmod 0664 /dev/hdmitx
vendor/init/hw/init.mt6765.rc:491 | post-fs-data | chmod 0664 /proc/mtk_jpeg
vendor/init/hw/init.mt6765.rc:494 | post-fs-data | chmod 0440 /dev/mtk_almk
vendor/init/hw/init.mt6765.rc:497 | post-fs-data | chmod 0444 /dev/mtk_disp
vendor/init/hw/init.mt6765.rc:500 | post-fs-data | chmod 0640 /dev/ancservice
vendor/init/hw/init.mt6765.rc:501 | post-fs-data | chown media media /dev/ancservice
vendor/init/hw/init.mt6765.rc:504 | post-fs-data | chmod 0660 /dev/camera-sysram
vendor/init/hw/init.mt6765.rc:505 | post-fs-data | chmod 0660 /dev/camera-isp
vendor/init/hw/init.mt6765.rc:506 | post-fs-data | chmod 0660 /dev/camera-tsf
vendor/init/hw/init.mt6765.rc:507 | post-fs-data | chmod 0660 /dev/camera-dpe
vendor/init/hw/init.mt6765.rc:508 | post-fs-data | chmod 0660 /dev/camera-fdvt
vendor/init/hw/init.mt6765.rc:509 | post-fs-data | chmod 0660 /dev/camera-pipemgr
vendor/init/hw/init.mt6765.rc:510 | post-fs-data | chmod 0660 /dev/kd_camera_hw
vendor/init/hw/init.mt6765.rc:511 | post-fs-data | chmod 0660 /dev/flashlight
vendor/init/hw/init.mt6765.rc:512 | post-fs-data | chmod 0660 /dev/kd_camera_hw_bus2
vendor/init/hw/init.mt6765.rc:513 | post-fs-data | chmod 0660 /dev/FM50AF
vendor/init/hw/init.mt6765.rc:514 | post-fs-data | chmod 0660 /dev/CAM_CAL_DRV
vendor/init/hw/init.mt6765.rc:515 | post-fs-data | chmod 0660 /dev/MAINAF
vendor/init/hw/init.mt6765.rc:516 | post-fs-data | chmod 0660 /dev/MAIN2AF
vendor/init/hw/init.mt6765.rc:517 | post-fs-data | chmod 0660 /dev/SUBAF
vendor/init/hw/init.mt6765.rc:519 | post-fs-data | chown system camera /dev/camera-sysram
vendor/init/hw/init.mt6765.rc:520 | post-fs-data | chown system camera /dev/camera-isp
vendor/init/hw/init.mt6765.rc:521 | post-fs-data | chown system camera /dev/camera-dpe
vendor/init/hw/init.mt6765.rc:522 | post-fs-data | chown system camera /dev/camera-tsf
vendor/init/hw/init.mt6765.rc:523 | post-fs-data | chown system camera /dev/camera-fdvt
vendor/init/hw/init.mt6765.rc:524 | post-fs-data | chown system camera /dev/camera-pipemgr
vendor/init/hw/init.mt6765.rc:525 | post-fs-data | chown system camera /dev/kd_camera_hw
vendor/init/hw/init.mt6765.rc:526 | post-fs-data | chown system camera /dev/flashlight
vendor/init/hw/init.mt6765.rc:527 | post-fs-data | chown system camera /dev/kd_camera_hw_bus2
vendor/init/hw/init.mt6765.rc:528 | post-fs-data | chown system camera /dev/FM50AF
vendor/init/hw/init.mt6765.rc:529 | post-fs-data | chown system camera /dev/CAM_CAL_DRV
vendor/init/hw/init.mt6765.rc:530 | post-fs-data | chown system camera /dev/MAINAF
vendor/init/hw/init.mt6765.rc:531 | post-fs-data | chown system camera /dev/MAIN2AF
vendor/init/hw/init.mt6765.rc:532 | post-fs-data | chown system camera /dev/SUBAF
vendor/init/hw/init.mt6765.rc:535 | post-fs-data | chmod 0666 /dev/ccu
vendor/init/hw/init.mt6765.rc:536 | post-fs-data | chown system camera /dev/ccu
vendor/init/hw/init.mt6765.rc:539 | post-fs-data | chmod 0640 /dev/vow
vendor/init/hw/init.mt6765.rc:540 | post-fs-data | chown media media /dev/vow
vendor/init/hw/init.mt6765.rc:543 | post-fs-data | chmod 0660 /dev/MATV
vendor/init/hw/init.mt6765.rc:544 | post-fs-data | chown system media /dev/MATV
vendor/init/hw/init.mt6765.rc:547 | post-fs-data | chown root radio /dev/mt_otg_test
vendor/init/hw/init.mt6765.rc:548 | post-fs-data | chmod 0660 /dev/mt_otg_test
vendor/init/hw/init.mt6765.rc:551 | post-fs-data | chmod 0660 /dev/mt-mdp
vendor/init/hw/init.mt6765.rc:552 | post-fs-data | chown system media /dev/mt-mdp
vendor/init/hw/init.mt6765.rc:555 | post-fs-data | chmod 0660 /dev/MTK_SMI
vendor/init/hw/init.mt6765.rc:556 | post-fs-data | chown media media /dev/MTK_SMI
vendor/init/hw/init.mt6765.rc:559 | post-fs-data | chmod 0660 /dev/mtk_rrc
vendor/init/hw/init.mt6765.rc:560 | post-fs-data | chown media system /dev/mtk_rrc
vendor/init/hw/init.mt6765.rc:563 | post-fs-data | chmod 0660 /dev/mtk_dfrc
vendor/init/hw/init.mt6765.rc:564 | post-fs-data | chown system graphics /dev/mtk_dfrc
vendor/init/hw/init.mt6765.rc:568 | post-fs-data | chmod 0640 /mtd@sec_ro
vendor/init/hw/init.mt6765.rc:569 | post-fs-data | chown root system /mtd@sec_ro
vendor/init/hw/init.mt6765.rc:571 | post-fs-data | chmod 0640 /mtd@preloader
vendor/init/hw/init.mt6765.rc:572 | post-fs-data | chown root system /mtd@preloader
vendor/init/hw/init.mt6765.rc:574 | post-fs-data | chmod 0660 /mtd@pro_info
vendor/init/hw/init.mt6765.rc:575 | post-fs-data | chown root system /mtd@pro_info
vendor/init/hw/init.mt6765.rc:577 | post-fs-data | chmod 0640 /mtd@bootimg
vendor/init/hw/init.mt6765.rc:578 | post-fs-data | chown root system /mtd@bootimg
vendor/init/hw/init.mt6765.rc:580 | post-fs-data | chmod 0640 /mtd@recovery
vendor/init/hw/init.mt6765.rc:581 | post-fs-data | chown root system /mtd@recovery
vendor/init/hw/init.mt6765.rc:583 | post-fs-data | chmod 0660 /mtd@nvram
vendor/init/hw/init.mt6765.rc:584 | post-fs-data | chown root system /mtd@nvram
vendor/init/hw/init.mt6765.rc:586 | post-fs-data | chmod 0660 /mtd@seccfg
vendor/init/hw/init.mt6765.rc:587 | post-fs-data | chown root system /mtd@seccfg
vendor/init/hw/init.mt6765.rc:589 | post-fs-data | chmod 0660 /mtd@misc
vendor/init/hw/init.mt6765.rc:590 | post-fs-data | chown root system /mtd@misc
vendor/init/hw/init.mt6765.rc:592 | post-fs-data | chmod 0664 /sys/bus/platform/drivers/emi_ctrl/concurrency_scenario
vendor/init/hw/init.mt6765.rc:593 | post-fs-data | chown media media /sys/bus/platform/drivers/emi_ctrl/concurrency_scenario
vendor/init/hw/init.mt6765.rc:596 | post-fs-data | chmod 0660 /dev/mtk_sched
vendor/init/hw/init.mt6765.rc:597 | post-fs-data | chown system media /dev/mtk_sched
vendor/init/hw/init.mt6765.rc:600 | post-fs-data | chmod 0660 /dev/ebc
vendor/init/hw/init.mt6765.rc:601 | post-fs-data | chown media media /dev/ebc
vendor/init/hw/init.mt6765.rc:603 | post-fs-data | chmod 0666 /dev/uio0
vendor/init/hw/init.mt6765.rc:606 | post-fs-data | chmod 0660 /proc/driver/otp
vendor/init/hw/init.mt6765.rc:607 | post-fs-data | chown root system /proc/driver/otp
vendor/init/hw/init.mt6765.rc:610 | post-fs-data | chmod 0664 /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6765.rc:611 | post-fs-data | chown root system /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6765.rc:612 | post-fs-data | chmod 0664 /sys/class/udc/musb-hdrc/device/cmode
vendor/init/hw/init.mt6765.rc:613 | post-fs-data | chown root system /sys/class/udc/musb-hdrc/device/cmode
vendor/init/hw/init.mt6765.rc:616 | post-fs-data | chown system system /proc/secmem0
vendor/init/hw/init.mt6765.rc:618 | post-fs-data | chmod 0666 /dev/exm0
vendor/init/hw/init.mt6765.rc:622 | post-fs-data | mkdir /data/vendor/.tp/ 0775 system system
vendor/init/hw/init.mt6765.rc:625 | post-fs-data | write /sys/power/pm_async 0
vendor/init/hw/init.mt6765.rc:631 | boot | write /proc/bootprof "INIT:boot"
vendor/init/hw/init.mt6765.rc:633 | boot | write /proc/sys/vm/dirty_writeback_centisecs 300
vendor/init/hw/init.mt6765.rc:634 | boot | chmod 0660 /sys/power/autosleep
vendor/init/hw/init.mt6765.rc:636 | boot | chmod 0664 /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6765.rc:637 | boot | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/init.mt6765.rc:638 | boot | chmod 0664 /sys/class/leds/red/brightness
vendor/init/hw/init.mt6765.rc:639 | boot | chown system system /sys/class/leds/red/brightness
vendor/init/hw/init.mt6765.rc:640 | boot | chmod 0664 /sys/class/leds/green/brightness
vendor/init/hw/init.mt6765.rc:641 | boot | chown system system /sys/class/leds/green/brightness
vendor/init/hw/init.mt6765.rc:642 | boot | chmod 0664 /sys/class/leds/blue/brightness
vendor/init/hw/init.mt6765.rc:643 | boot | chown system system /sys/class/leds/blue/brightness
vendor/init/hw/init.mt6765.rc:645 | boot | chown system system /sys/devices/system/cpu/cpufreq/hotplug/cpu_num_base
vendor/init/hw/init.mt6765.rc:646 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/hotplug/cpu_num_base
vendor/init/hw/init.mt6765.rc:647 | boot | chown system system /sys/devices/system/cpu/cpufreq/policy0/scaling_governor
vendor/init/hw/init.mt6765.rc:648 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/policy0/scaling_governor
vendor/init/hw/init.mt6765.rc:649 | boot | chown system system /sys/devices/system/cpu/cpufreq/policy4/scaling_governor
vendor/init/hw/init.mt6765.rc:650 | boot | chmod 0660 /sys/devices/system/cpu/cpufreq/policy4/scaling_governor
vendor/init/hw/init.mt6765.rc:651 | boot | chown root   system /sys/kernel/debug/binder/transaction_log_enable
vendor/init/hw/init.mt6765.rc:652 | boot | chown root   system /sys/kernel/debug/binder/perf_evalue
vendor/init/hw/init.mt6765.rc:653 | boot | chown system system /sys/devices/system/cpu/rq-stats/htasks_thresh
vendor/init/hw/init.mt6765.rc:654 | boot | chmod 0660 /sys/devices/system/cpu/rq-stats/htasks_thresh
vendor/init/hw/init.mt6765.rc:655 | boot | chown system system /sys/devices/system/cpu/rq-stats/avg_htasks_thresh
vendor/init/hw/init.mt6765.rc:656 | boot | chmod 0660 /sys/devices/system/cpu/rq-stats/avg_htasks_thresh
vendor/init/hw/init.mt6765.rc:658 | boot | chown system /sys/module/mlog/parameters/do_mlog
vendor/init/hw/init.mt6765.rc:659 | boot | chown system /sys/module/mlog/parameters/timer_intval
vendor/init/hw/init.mt6765.rc:661 | boot | chown root   radio  /sys/class/leds/lcd-backlight/duty
vendor/init/hw/init.mt6765.rc:662 | boot | chown root   radio  /sys/class/leds/lcd-backlight/div
vendor/init/hw/init.mt6765.rc:663 | boot | chown system system /sys/class/leds/lcd-backlight/trigger
vendor/init/hw/init.mt6765.rc:664 | boot | chown system system /sys/class/leds/button-backlight/trigger
vendor/init/hw/init.mt6765.rc:665 | boot | chown system system /sys/class/leds/keyboard-backlight/trigger
vendor/init/hw/init.mt6765.rc:666 | boot | chown system system /sys/class/leds/jogball-backlight/trigger
vendor/init/hw/init.mt6765.rc:667 | boot | chown system system /sys/class/leds/red/trigger
vendor/init/hw/init.mt6765.rc:668 | boot | chown system system /sys/class/leds/green/trigger
vendor/init/hw/init.mt6765.rc:669 | boot | chown system system /sys/class/leds/blue/trigger
vendor/init/hw/init.mt6765.rc:672 | boot | chown radio system /sys/power/tuxonice
vendor/init/hw/init.mt6765.rc:673 | boot | chown radio system /sys/power/tuxonice/do_hibernate
vendor/init/hw/init.mt6765.rc:674 | boot | chmod 0770 /sys/power/tuxonice
vendor/init/hw/init.mt6765.rc:675 | boot | chmod 0220 /sys/power/tuxonice/do_hibernate
vendor/init/hw/init.mt6765.rc:679 | boot | chown system system /sys/power/sb_state
vendor/init/hw/init.mt6765.rc:680 | boot | chmod 0660 /sys/power/sb_state
vendor/init/hw/init.mt6765.rc:683 | boot | chown system system /proc/hps/num_base_perf_serv
vendor/init/hw/init.mt6765.rc:684 | boot | chmod 0660 /proc/hps/num_base_perf_serv
vendor/init/hw/init.mt6765.rc:685 | boot | chown system system /proc/hps/num_limit_power_serv
vendor/init/hw/init.mt6765.rc:686 | boot | chmod 0660 /proc/hps/num_limit_power_serv
vendor/init/hw/init.mt6765.rc:687 | boot | chown system system /proc/hps/num_limit_ultra_power_saving
vendor/init/hw/init.mt6765.rc:688 | boot | chmod 0660 /proc/hps/num_limit_ultra_power_saving
vendor/init/hw/init.mt6765.rc:689 | boot | chown system system /proc/hps/down_threshold
vendor/init/hw/init.mt6765.rc:690 | boot | chmod 0660 /proc/hps/down_threshold
vendor/init/hw/init.mt6765.rc:691 | boot | chown system system /proc/hps/up_threshold
vendor/init/hw/init.mt6765.rc:692 | boot | chmod 0660 /proc/hps/up_threshold
vendor/init/hw/init.mt6765.rc:693 | boot | chown system system /proc/hps/rush_boost_enabled
vendor/init/hw/init.mt6765.rc:694 | boot | chmod 0660 /proc/hps/rush_boost_enabled
vendor/init/hw/init.mt6765.rc:695 | boot | chown system system /proc/hps/heavy_task_enabled
vendor/init/hw/init.mt6765.rc:696 | boot | chmod 0660 /proc/hps/heavy_task_enabled
vendor/init/hw/init.mt6765.rc:697 | boot | chown system system /proc/hps/power_mode
vendor/init/hw/init.mt6765.rc:698 | boot | chmod 0660 /proc/hps/power_mode
vendor/init/hw/init.mt6765.rc:699 | boot | chown system system /proc/hps/up_times
vendor/init/hw/init.mt6765.rc:700 | boot | chmod 0660 /proc/hps/up_times
vendor/init/hw/init.mt6765.rc:701 | boot | chown system system /proc/hps/down_times
vendor/init/hw/init.mt6765.rc:702 | boot | chmod 0660 /proc/hps/down_times
vendor/init/hw/init.mt6765.rc:705 | boot | chown system system /proc/ppm/mode
vendor/init/hw/init.mt6765.rc:706 | boot | chmod 0660 /proc/ppm/mode
vendor/init/hw/init.mt6765.rc:707 | boot | chown system system /proc/ppm/policy/perfserv_min_perf_idx
vendor/init/hw/init.mt6765.rc:708 | boot | chmod 0440 /proc/ppm/policy/perfserv_min_perf_idx
vendor/init/hw/init.mt6765.rc:709 | boot | chown system system /proc/ppm/policy/perfserv_max_perf_idx
vendor/init/hw/init.mt6765.rc:710 | boot | chmod 0440 /proc/ppm/policy/perfserv_max_perf_idx
vendor/init/hw/init.mt6765.rc:711 | boot | chown system system /proc/ppm/policy/perfserv_perf_idx
vendor/init/hw/init.mt6765.rc:712 | boot | chmod 0660 /proc/ppm/policy/perfserv_perf_idx
vendor/init/hw/init.mt6765.rc:713 | boot | chown system system /proc/ppm/policy/userlimit_min_cpu_freq
vendor/init/hw/init.mt6765.rc:714 | boot | chmod 0660 /proc/ppm/policy/userlimit_min_cpu_freq
vendor/init/hw/init.mt6765.rc:715 | boot | chown system system /proc/ppm/policy/userlimit_max_cpu_freq
vendor/init/hw/init.mt6765.rc:716 | boot | chmod 0660 /proc/ppm/policy/userlimit_max_cpu_freq
vendor/init/hw/init.mt6765.rc:717 | boot | chown system system /proc/ppm/policy/userlimit_min_cpu_core
vendor/init/hw/init.mt6765.rc:718 | boot | chmod 0660 /proc/ppm/policy/userlimit_min_cpu_core
vendor/init/hw/init.mt6765.rc:719 | boot | chown system system /proc/ppm/policy/userlimit_max_cpu_core
vendor/init/hw/init.mt6765.rc:720 | boot | chmod 0660 /proc/ppm/policy/userlimit_max_cpu_core
vendor/init/hw/init.mt6765.rc:721 | boot | chown system system /proc/ppm/policy/userlimit_cpu_core
vendor/init/hw/init.mt6765.rc:722 | boot | chmod 0660 /proc/ppm/policy/userlimit_cpu_core
vendor/init/hw/init.mt6765.rc:723 | boot | chown system system /proc/ppm/policy/userlimit_cpu_freq
vendor/init/hw/init.mt6765.rc:724 | boot | chmod 0660 /proc/ppm/policy/userlimit_cpu_freq
vendor/init/hw/init.mt6765.rc:725 | boot | chown system system /proc/ppm/dump_cluster_0_dvfs_table
vendor/init/hw/init.mt6765.rc:726 | boot | chmod 0440 /proc/ppm/dump_cluster_0_dvfs_table
vendor/init/hw/init.mt6765.rc:727 | boot | chown system system /proc/ppm/dump_cluster_1_dvfs_table
vendor/init/hw/init.mt6765.rc:728 | boot | chmod 0440 /proc/ppm/dump_cluster_1_dvfs_table
vendor/init/hw/init.mt6765.rc:729 | boot | chown system system /proc/ppm/dump_cluster_2_dvfs_table
vendor/init/hw/init.mt6765.rc:730 | boot | chmod 0440 /proc/ppm/dump_cluster_2_dvfs_table
vendor/init/hw/init.mt6765.rc:731 | boot | chown system system /proc/ppm/root_cluster
vendor/init/hw/init.mt6765.rc:732 | boot | chmod 0660 /proc/ppm/root_cluster
vendor/init/hw/init.mt6765.rc:733 | boot | chown system system /proc/ppm/policy/hica_variant
vendor/init/hw/init.mt6765.rc:734 | boot | chmod 0660 /proc/ppm/policy/hica_variant
vendor/init/hw/init.mt6765.rc:735 | boot | chown system system /proc/ppm/policy/hica_settings/L_ONLY_to_4L_LL/loading_hold_time
vendor/init/hw/init.mt6765.rc:736 | boot | chmod 0660 /proc/ppm/policy/hica_settings/L_ONLY_to_4L_LL/loading_hold_time
vendor/init/hw/init.mt6765.rc:739 | boot | chown system system /proc/cpu_loading/onoff
vendor/init/hw/init.mt6765.rc:740 | boot | chmod 0664 /proc/cpu_loading/onoff
vendor/init/hw/init.mt6765.rc:741 | boot | chown system system /proc/cpu_loading/overThrhld
vendor/init/hw/init.mt6765.rc:742 | boot | chmod 0664 /proc/cpu_loading/overThrhld
vendor/init/hw/init.mt6765.rc:743 | boot | chown system system /proc/cpu_loading/poltime_nsecs
vendor/init/hw/init.mt6765.rc:744 | boot | chmod 0664 /proc/cpu_loading/poltime_nsecs
vendor/init/hw/init.mt6765.rc:745 | boot | chown system system /proc/cpu_loading/poltime_secs
vendor/init/hw/init.mt6765.rc:746 | boot | chmod 0664 /proc/cpu_loading/poltime_secs
vendor/init/hw/init.mt6765.rc:747 | boot | chown system system /proc/cpu_loading/uevent_enable
vendor/init/hw/init.mt6765.rc:748 | boot | chmod 0664 /proc/cpu_loading/uevent_enable
vendor/init/hw/init.mt6765.rc:751 | boot | chown system system /proc/cpufreq/cpufreq_limited_by_hevc
vendor/init/hw/init.mt6765.rc:752 | boot | chmod 0660 /proc/cpufreq/cpufreq_limited_by_hevc
vendor/init/hw/init.mt6765.rc:753 | boot | chown system system /proc/cpufreq/cpufreq_limited_max_freq_by_user
vendor/init/hw/init.mt6765.rc:754 | boot | chmod 0660 /proc/cpufreq/cpufreq_limited_max_freq_by_user
vendor/init/hw/init.mt6765.rc:755 | boot | chown system system /proc/cpufreq/cpufreq_power_mode
vendor/init/hw/init.mt6765.rc:756 | boot | chmod 0660 /proc/cpufreq/cpufreq_power_mode
vendor/init/hw/init.mt6765.rc:757 | boot | chown system system /proc/cpufreq/enable_hw_gov
vendor/init/hw/init.mt6765.rc:758 | boot | chmod 0660 /proc/cpufreq/enable_hw_gov
vendor/init/hw/init.mt6765.rc:761 | boot | chown system system /proc/fliperfs/cg_enable
vendor/init/hw/init.mt6765.rc:762 | boot | chmod 0664 /proc/fliperfs/cg_enable
vendor/init/hw/init.mt6765.rc:763 | boot | chown system system /proc/fliperfs/cg_threshold
vendor/init/hw/init.mt6765.rc:764 | boot | chmod 0664 /proc/fliperfs/cg_threshold
vendor/init/hw/init.mt6765.rc:765 | boot | chown system system /proc/fliperfs/cg_threshold_ddr3
vendor/init/hw/init.mt6765.rc:766 | boot | chmod 0664 /proc/fliperfs/cg_threshold_ddr3
vendor/init/hw/init.mt6765.rc:767 | boot | chown system system /proc/fliperfs/perf
vendor/init/hw/init.mt6765.rc:768 | boot | chmod 0664 /proc/fliperfs/perf
vendor/init/hw/init.mt6765.rc:771 | boot | chown system system /proc/perfmgr/smart/smart_uevent_enable
vendor/init/hw/init.mt6765.rc:772 | boot | chmod 0660 /proc/perfmgr/smart/smart_uevent_enable
vendor/init/hw/init.mt6765.rc:773 | boot | chown system system /proc/perfmgr/smart/smart_force_isolate
vendor/init/hw/init.mt6765.rc:774 | boot | chmod 0660 /proc/perfmgr/smart/smart_force_isolate
vendor/init/hw/init.mt6765.rc:775 | boot | chown system system /proc/perfmgr/smart/app_is_sports
vendor/init/hw/init.mt6765.rc:776 | boot | chmod 0660 /proc/perfmgr/smart/app_is_sports
vendor/init/hw/init.mt6765.rc:777 | boot | chown system system /proc/perfmgr/smart/app_is_running
vendor/init/hw/init.mt6765.rc:778 | boot | chmod 0660 /proc/perfmgr/smart/app_is_running
vendor/init/hw/init.mt6765.rc:779 | boot | chown system system /proc/perfmgr/boost/cpuset_pid
vendor/init/hw/init.mt6765.rc:780 | boot | chmod 0660 /proc/perfmgr/boost/cpuset_pid
vendor/init/hw/init.mt6765.rc:781 | boot | chown system system /proc/perfmgr/eas/perfserv_ta_boost
vendor/init/hw/init.mt6765.rc:782 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_ta_boost
vendor/init/hw/init.mt6765.rc:783 | boot | chown system system /proc/perfmgr/eas/perfserv_fg_boost
vendor/init/hw/init.mt6765.rc:784 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_fg_boost
vendor/init/hw/init.mt6765.rc:785 | boot | chown system system /proc/perfmgr/eas/perfserv_bg_boost
vendor/init/hw/init.mt6765.rc:786 | boot | chmod 0660 /proc/perfmgr/eas/perfserv_bg_boost
vendor/init/hw/init.mt6765.rc:787 | boot | chown system system /sys/devices/system/cpu/eas/stune_task_thresh
vendor/init/hw/init.mt6765.rc:788 | boot | chmod 0660 /sys/devices/system/cpu/eas/stune_task_thresh
vendor/init/hw/init.mt6765.rc:789 | boot | chown system system /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/hw/init.mt6765.rc:790 | boot | chmod 0660 /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/hw/init.mt6765.rc:791 | boot | chown system system /sys/devices/system/cpu/eas/enable
vendor/init/hw/init.mt6765.rc:792 | boot | chmod 0660 /sys/devices/system/cpu/eas/enable
vendor/init/hw/init.mt6765.rc:793 | boot | chown system system /sys/module/ged/parameters/gx_top_app_pid
vendor/init/hw/init.mt6765.rc:794 | boot | chmod 0660 /sys/module/ged/parameters/gx_top_app_pid
vendor/init/hw/init.mt6765.rc:795 | boot | chown system system /proc/perfmgr/fbc
vendor/init/hw/init.mt6765.rc:796 | boot | chmod 0664 /proc/perfmgr/fbc
vendor/init/hw/init.mt6765.rc:797 | boot | chown system system /proc/perfmgr/legacy/perfserv_core
vendor/init/hw/init.mt6765.rc:798 | boot | chmod 0664 /proc/perfmgr/legacy/perfserv_core
vendor/init/hw/init.mt6765.rc:799 | boot | chown system system /proc/perfmgr/legacy/perfserv_freq
vendor/init/hw/init.mt6765.rc:800 | boot | chmod 0664 /proc/perfmgr/legacy/perfserv_freq
vendor/init/hw/init.mt6765.rc:801 | boot | chown system system /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/hw/init.mt6765.rc:802 | boot | chmod 0664 /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/hw/init.mt6765.rc:803 | boot | chown system system /dev/cpuset/user_space_global_cpuset
vendor/init/hw/init.mt6765.rc:804 | boot | chmod 0664 /dev/cpuset/user_space_global_cpuset
vendor/init/hw/init.mt6765.rc:805 | boot | chown system system /dev/cpuset/cpus
vendor/init/hw/init.mt6765.rc:806 | boot | chmod 0664 /dev/cpuset/cpus
vendor/init/hw/init.mt6765.rc:807 | boot | chown system system /dev/cpuset/top-app/cpus
vendor/init/hw/init.mt6765.rc:808 | boot | chmod 0664 /dev/cpuset/top-app/cpus
vendor/init/hw/init.mt6765.rc:809 | boot | chown system system /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6765.rc:810 | boot | chmod 0664 /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6765.rc:811 | boot | chown system system /sys/kernel/debug/fpsgo/common/fpsgo_game_enable
vendor/init/hw/init.mt6765.rc:812 | boot | chmod 0664 /sys/kernel/debug/fpsgo/common/fpsgo_game_enable
vendor/init/hw/init.mt6765.rc:813 | boot | chown system system /proc/fbt_cpu/switch_fbt
vendor/init/hw/init.mt6765.rc:814 | boot | chmod 0664 /proc/fbt_cpu/switch_fbt
vendor/init/hw/init.mt6765.rc:815 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_level
vendor/init/hw/init.mt6765.rc:816 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_level
vendor/init/hw/init.mt6765.rc:817 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_error_threshold
vendor/init/hw/init.mt6765.rc:818 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_error_threshold
vendor/init/hw/init.mt6765.rc:819 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_quantile
vendor/init/hw/init.mt6765.rc:820 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_quantile
vendor/init/hw/init.mt6765.rc:821 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_window_size
vendor/init/hw/init.mt6765.rc:822 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_window_size
vendor/init/hw/init.mt6765.rc:823 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_freq
vendor/init/hw/init.mt6765.rc:824 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_freq
vendor/init/hw/init.mt6765.rc:825 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_fps
vendor/init/hw/init.mt6765.rc:826 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_lpp_fps
vendor/init/hw/init.mt6765.rc:827 | boot | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_force_vag
vendor/init/hw/init.mt6765.rc:828 | boot | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_force_vag
vendor/init/hw/init.mt6765.rc:829 | boot | chown system system /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/hw/init.mt6765.rc:830 | boot | chmod 0664 /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/hw/init.mt6765.rc:831 | boot | chown system system /sys/module/fbt_cpu/parameters/kmin
vendor/init/hw/init.mt6765.rc:832 | boot | chmod 0664 /sys/module/fbt_cpu/parameters/kmin
vendor/init/hw/init.mt6765.rc:833 | boot | chown system system /proc/fbt_cpu/switch_cpuset
vendor/init/hw/init.mt6765.rc:834 | boot | chmod 0664 /proc/fbt_cpu/switch_cpuset
vendor/init/hw/init.mt6765.rc:835 | boot | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/hw/init.mt6765.rc:836 | boot | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/hw/init.mt6765.rc:837 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/hw/init.mt6765.rc:838 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/hw/init.mt6765.rc:839 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/hw/init.mt6765.rc:840 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/hw/init.mt6765.rc:841 | boot | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/hw/init.mt6765.rc:842 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/hw/init.mt6765.rc:843 | boot | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/ddr
vendor/init/hw/init.mt6765.rc:844 | boot | chmod 0660 /proc/perfmgr/boost_ctrl/eas_ctrl/dram_ctrl/ddr
vendor/init/hw/init.mt6765.rc:849 | boot | chown root radio /proc/net/vlan/config
vendor/init/hw/init.mt6765.rc:850 | boot | chmod 0640 /proc/net/vlan/config
vendor/init/hw/init.mt6765.rc:851 | boot | chown root radio /proc/net/ip6_tables_matches
vendor/init/hw/init.mt6765.rc:852 | boot | chown root radio /proc/net/ip6_tables_names
vendor/init/hw/init.mt6765.rc:853 | boot | chown root radio /proc/net/ip6_tables_targets
vendor/init/hw/init.mt6765.rc:854 | boot | chown root radio /proc/net/arp_tables_targets
vendor/init/hw/init.mt6765.rc:855 | boot | chown root radio /proc/net/arp_tables_matches
vendor/init/hw/init.mt6765.rc:856 | boot | chown root radio /proc/net/arp_tables_names
vendor/init/hw/init.mt6765.rc:857 | boot | chown root radio /proc/net/ip_tables_targets
vendor/init/hw/init.mt6765.rc:858 | boot | chown root radio /proc/net/ip_tables_matches
vendor/init/hw/init.mt6765.rc:859 | boot | chown root radio /proc/net/ip_tables_names
vendor/init/hw/init.mt6765.rc:860 | boot | chown root radio /proc/net/ip_conntrack_expect
vendor/init/hw/init.mt6765.rc:861 | boot | chown root radio /proc/net/ip_conntrack
vendor/init/hw/init.mt6765.rc:862 | boot | chown root radio /proc/net/nf_conntrack
vendor/init/hw/init.mt6765.rc:863 | boot | chown root radio /proc/net/nf_conntrack_expect
vendor/init/hw/init.mt6765.rc:864 | boot | chown root radio /proc/net/netfilter/nfnetlink_log
vendor/init/hw/init.mt6765.rc:865 | boot | chown root radio /proc/net/netfilter/nfnetlink_queue
vendor/init/hw/init.mt6765.rc:868 | boot | chown shell system /proc/driver/wmt_dbg
vendor/init/hw/init.mt6765.rc:869 | boot | chown shell system /proc/driver/wmt_aee
vendor/init/hw/init.mt6765.rc:872 | boot | chown system radio /dev/ttyGS3
vendor/init/hw/init.mt6765.rc:873 | boot | chmod 0660 /dev/ttyGS3
vendor/init/hw/init.mt6765.rc:876 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/rst_op
vendor/init/hw/init.mt6765.rc:877 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/rst_op
vendor/init/hw/init.mt6765.rc:878 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/upgrade_op
vendor/init/hw/init.mt6765.rc:879 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/upgrade_op
vendor/init/hw/init.mt6765.rc:880 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/nrf_rtc
vendor/init/hw/init.mt6765.rc:881 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/nrf_rtc
vendor/init/hw/init.mt6765.rc:882 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/btMAC
vendor/init/hw/init.mt6765.rc:883 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/btMAC
vendor/init/hw/init.mt6765.rc:884 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/longterm
vendor/init/hw/init.mt6765.rc:885 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/longterm
vendor/init/hw/init.mt6765.rc:886 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/command
vendor/init/hw/init.mt6765.rc:887 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/command
vendor/init/hw/init.mt6765.rc:888 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/version
vendor/init/hw/init.mt6765.rc:889 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/version
vendor/init/hw/init.mt6765.rc:890 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/config
vendor/init/hw/init.mt6765.rc:891 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/config
vendor/init/hw/init.mt6765.rc:892 | boot | chown system system /sys/class/misc/wiite_corp_ctrl/uart_op
vendor/init/hw/init.mt6765.rc:893 | boot | chmod 0666 /sys/class/misc/wiite_corp_ctrl/uart_op
vendor/init/hw/init.mt6765.rc:896 | boot | chmod 666 /dev/ttyS1
vendor/init/hw/init.mt6765.rc:897 | boot | chown system system /dev/ttyS1
vendor/init/hw/init.mt6765.rc:900 | boot | chmod 660 /dev/ttyGS0
vendor/init/hw/init.mt6765.rc:901 | boot | chown system radio /dev/ttyGS0
vendor/init/hw/init.mt6765.rc:902 | boot | chmod 660 /dev/ttyGS1
vendor/init/hw/init.mt6765.rc:903 | boot | chown system radio /dev/ttyGS1
vendor/init/hw/init.mt6765.rc:906 | property:ro.board.platform=mt6753 | chown system system /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
vendor/init/hw/init.mt6765.rc:907 | property:ro.board.platform=mt6753 | chmod 0660 /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq
vendor/init/hw/init.mt6765.rc:908 | property:ro.board.platform=mt6753 | write /sys/devices/system/cpu/cpufreq/interactive/hispeed_freq 1300000
vendor/init/hw/init.mt6765.rc:911 | property:vold.decrypt=trigger_reset_main | write /proc/bootprof "INIT:vold.decrypt=trigger_reset_main"
vendor/init/hw/init.mt6765.rc:912 | property:vold.decrypt=trigger_reset_main | setprop sys.boot_completed 0
vendor/init/hw/init.mt6765.rc:913 | property:vold.decrypt=trigger_reset_main | setprop dev.bootcomplete 0
vendor/init/hw/init.mt6765.rc:916 | property:vold.decrypt=trigger_post_fs_data | write /proc/bootprof "INIT:vold.decrypt=trigger_post_fs_data"
vendor/init/hw/init.mt6765.rc:919 | property:vold.decrypt=trigger_restart_min_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_restart_min_framework"
vendor/init/hw/init.mt6765.rc:922 | property:vold.decrypt=trigger_restart_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_restart_framework"
vendor/init/hw/init.mt6765.rc:925 | property:vold.decrypt=trigger_shutdown_framework | write /proc/bootprof "INIT:vold.decrypt=trigger_shutdown_framework"
vendor/init/hw/init.mt6765.rc:926 | property:vold.decrypt=trigger_shutdown_framework | setprop sys.boot_completed 0
vendor/init/hw/init.mt6765.rc:927 | property:vold.decrypt=trigger_shutdown_framework | setprop dev.bootcomplete 0
vendor/init/hw/init.mt6765.rc:932 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.default 4096,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6765.rc:933 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.wifi    524288,1048576,2097152,262144,524288,1048576
vendor/init/hw/init.mt6765.rc:934 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.lte     524288,1048576,2097152,262144,524288,1048576
vendor/init/hw/init.mt6765.rc:935 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.umts    4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6765.rc:936 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hspa    4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6765.rc:937 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hsupa   4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6765.rc:938 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hsdpa   4094,87380,524288,4096,16384,524288
vendor/init/hw/init.mt6765.rc:939 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.hspap   4094,87380,1220608,4096,16384,1220608
vendor/init/hw/init.mt6765.rc:940 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.edge    4093,26280,35040,4096,16384,35040
vendor/init/hw/init.mt6765.rc:941 | property:vold.decrypt=trigger_shutdown_framework | setprop net.tcp.buffersize.gprs    4092,8760,11680,4096,8760,11680
vendor/init/hw/init.mt6765.rc:943 | property:vold.decrypt=trigger_shutdown_framework | setprop persist.sys.strictmode.disable 1
vendor/init/hw/init.mt6765.rc:947 | early-init && property:ro.boot.boot_trace=1 | setprop debug.atrace.tags.enableflags 0x1fffffe
vendor/init/hw/init.mt6765.rc:951 | property:sys.boot_completed=1 | write /proc/bootprof "0"
vendor/init/hw/init.mt6765.rc:952 | property:sys.boot_completed=1 | write /proc/mtprintk ${persist.vendor.uartconsole.enable}
vendor/init/hw/init.mt6765.rc:956 | property:persist.vendor.uartconsole.enable=* | write /proc/mtprintk ${persist.vendor.uartconsole.enable}
vendor/init/hw/init.mt6765.rc:969 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/iostats 1
vendor/init/hw/init.mt6765.rc:970 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/read_ahead_kb 512
vendor/init/hw/init.mt6765.rc:971 | property:sys.boot_completed=1 | write /sys/block/mmcblk0/queue/nr_requests 128
vendor/init/hw/init.mt6765.rc:972 | property:sys.boot_completed=1 | write /sys/block/dm-0/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:973 | property:sys.boot_completed=1 | write /sys/block/dm-1/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:974 | property:sys.boot_completed=1 | write /sys/block/dm-2/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:975 | property:sys.boot_completed=1 | write /sys/block/dm-3/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:976 | property:sys.boot_completed=1 | write /sys/block/dm-4/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:977 | property:sys.boot_completed=1 | write /sys/block/dm-5/queue/read_ahead_kb 128
vendor/init/hw/init.mt6765.rc:981 | property:sys.boot_completed=1 | write /dev/stune/schedtune.boost 0
vendor/init/hw/init.mt6765.rc:982 | property:sys.boot_completed=1 | write /dev/stune/foreground/schedtune.boost 0
vendor/init/hw/init.mt6765.rc:983 | property:sys.boot_completed=1 | write /dev/stune/background/schedtune.boost 0
vendor/init/hw/init.mt6765.rc:984 | property:sys.boot_completed=1 | write /dev/stune/top-app/schedtune.boost 0
vendor/init/hw/init.mt6765.rc:985 | property:sys.boot_completed=1 | write /dev/stune/rt/schedtune.boost 0
vendor/init/hw/init.mt6765.rc:986 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/eas/enable 2
vendor/init/hw/init.mt6765.rc:987 | property:sys.boot_completed=1 | write /proc/cpufreq/cpufreq_sched_disable 0
vendor/init/hw/init.mt6765.rc:988 | property:sys.boot_completed=1 | chown system system /dev/stune/foreground/schedtune.prefer_idle
vendor/init/hw/init.mt6765.rc:989 | property:sys.boot_completed=1 | chown system system /dev/stune/top-app/schedtune.prefer_idle
vendor/init/hw/init.mt6765.rc:990 | property:sys.boot_completed=1 | chown system system /dev/stune/rt/schedtune.prefer_idle
vendor/init/hw/init.mt6765.rc:991 | property:sys.boot_completed=1 | write /dev/stune/foreground/schedtune.prefer_idle 0
vendor/init/hw/init.mt6765.rc:992 | property:sys.boot_completed=1 | write /dev/stune/top-app/schedtune.prefer_idle 0
vendor/init/hw/init.mt6765.rc:993 | property:sys.boot_completed=1 | write /dev/stune/rt/schedtune.prefer_idle 0
vendor/init/hw/init.mt6765.rc:994 | property:sys.boot_completed=1 | write /proc/sys/kernel/sched_migration_cost_ns 200000
vendor/init/hw/init.mt6765.rc:997 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/cpufreq/policy0/scaling_governor "schedplus"
vendor/init/hw/init.mt6765.rc:998 | property:sys.boot_completed=1 | write /sys/devices/system/cpu/cpufreq/policy4/scaling_governor "schedplus"
vendor/init/hw/init.mt6765.rc:1001 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "0 0"
vendor/init/hw/init.mt6765.rc:1002 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "1 0"
vendor/init/hw/init.mt6765.rc:1003 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "2 0"
vendor/init/hw/init.mt6765.rc:1004 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/eas_ctrl/boot_boost "3 0"
vendor/init/hw/init.mt6765.rc:1005 | property:sys.boot_completed=1 | write /proc/sys/kernel/sched_stune_task_threshold "-1"
vendor/init/hw/init.mt6765.rc:1006 | property:sys.boot_completed=1 | write /proc/perfmgr/boost_ctrl/cpu_ctrl/boot_freq "-1 -1 -1 -1"
vendor/init/hw/init.mt6765.rc:1007 | property:sys.boot_completed=1 | write /sys/devices/platform/10012000.dvfsrc/helio-dvfsrc/dvfsrc_req_ddr_opp "-1"
vendor/init/hw/init.mt6765.rc:1059 | property:persist.vendor.mediatek.fg.disable=1 | write /sys/devices/platform/battery_meter/FG_daemon_disable 1
vendor/init/hw/init.mt6765.rc:1060 | property:persist.vendor.mediatek.fg.disable=1 | write /sys/bus/platform/devices/battery/FG_daemon_disable 1
vendor/init/hw/init.mt6765.rc:1063 | property:persist.vendor.mediatek.fg.force25c=1 | write /sys/devices/platform/battery_meter/FG_drv_force25c 1
vendor/init/hw/init.mt6765.rc:1064 | property:persist.vendor.mediatek.fg.force25c=1 | write /sys/bus/platform/devices/battery/Battery_Temperature 25
vendor/init/hw/init.mt6765.rc:1067 | property:persist.vendor.mediatek.fg.force25c=0 | write /sys/devices/platform/battery_meter/FG_drv_force25c 0
vendor/init/hw/init.mt6765.rc:1068 | property:persist.vendor.mediatek.fg.force25c=0 | write /sys/bus/platform/devices/battery/Battery_Temperature 65535
vendor/init/hw/init.mt6765.rc:1071 | property:persist.vendor.mediatek.fg.log.enable=1 | write /sys/devices/platform/battery_meter/FG_daemon_log_level 7
vendor/init/hw/init.mt6765.rc:1072 | property:persist.vendor.mediatek.fg.log.enable=1 | write /sys/bus/platform/devices/battery/FG_daemon_log_level 7
vendor/init/hw/init.mt6765.rc:1073 | property:persist.vendor.mediatek.fg.log.enable=1 | write /proc/sys/kernel/printk 8
vendor/init/hw/init.mt6765.rc:1094 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start meta_tst
vendor/init/hw/init.mt6765.rc:1095 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start loghidlvendorservice
vendor/init/hw/init.mt6765.rc:1096 | property:sys.boot_completed=1 && property:ro.boot.atm=enable | start loghidlsysservice
vendor/init/hw/init.mt6765.rc:1101 | init | symlink /dev/block/platform/bootdevice /dev/block/bootdevice
vendor/init/hw/init.mt6765.rc:1105 | boot | insmod /vendor/lib/modules/fpsgo.ko
vendor/init/hw/init.mt6765.usb.rc:2 | early-init | write /sys/module/musb_hdrc/parameters/kernel_init_done 1
vendor/init/hw/init.mt6765.usb.rc:5 | post-fs | setprop vendor.usb.vid "0x0E8D"
vendor/init/hw/init.mt6765.usb.rc:6 | post-fs | mkdir /dev/usb-ffs 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:7 | post-fs | mkdir /dev/usb-ffs/adb 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:8 | post-fs | mkdir /config/usb_gadget/g1 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:9 | post-fs | write /config/usb_gadget/g1/idVendor ${vendor.usb.vid}
vendor/init/hw/init.mt6765.usb.rc:10 | post-fs | write /config/usb_gadget/g1/bcdDevice 0x0223
vendor/init/hw/init.mt6765.usb.rc:11 | post-fs | write /config/usb_gadget/g1/bcdUSB 0x0200
vendor/init/hw/init.mt6765.usb.rc:12 | post-fs | mkdir /config/usb_gadget/g1/strings/0x409 0770
vendor/init/hw/init.mt6765.usb.rc:13 | post-fs | write /config/usb_gadget/g1/strings/0x409/serialnumber ${ro.serialno}
vendor/init/hw/init.mt6765.usb.rc:14 | post-fs | write /config/usb_gadget/g1/strings/0x409/manufacturer ${ro.product.manufacturer}
vendor/init/hw/init.mt6765.usb.rc:15 | post-fs | write /config/usb_gadget/g1/strings/0x409/product ${ro.product.model}
vendor/init/hw/init.mt6765.usb.rc:16 | post-fs | mkdir /config/usb_gadget/g1/functions/accessory.gs2
vendor/init/hw/init.mt6765.usb.rc:17 | post-fs | mkdir /config/usb_gadget/g1/functions/audio_source.gs3
vendor/init/hw/init.mt6765.usb.rc:18 | post-fs | mkdir /config/usb_gadget/g1/functions/ffs.adb
vendor/init/hw/init.mt6765.usb.rc:19 | post-fs | mkdir /config/usb_gadget/g1/functions/mtp.gs0
vendor/init/hw/init.mt6765.usb.rc:20 | post-fs | write /config/usb_gadget/g1/functions/mtp.gs0/os_desc/interface.MTP/compatible_id "MTP"
vendor/init/hw/init.mt6765.usb.rc:21 | post-fs | mkdir /config/usb_gadget/g1/functions/ptp.gs1
vendor/init/hw/init.mt6765.usb.rc:22 | post-fs | mkdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:23 | post-fs | mkdir /config/usb_gadget/g1/functions/midi.gs5
vendor/init/hw/init.mt6765.usb.rc:24 | post-fs | mkdir /config/usb_gadget/g1/functions/acm.gs0
vendor/init/hw/init.mt6765.usb.rc:25 | post-fs | mkdir /config/usb_gadget/g1/functions/acm.gs1
vendor/init/hw/init.mt6765.usb.rc:26 | post-fs | mkdir /config/usb_gadget/g1/functions/acm.gs2
vendor/init/hw/init.mt6765.usb.rc:27 | post-fs | mkdir /config/usb_gadget/g1/functions/acm.gs3
vendor/init/hw/init.mt6765.usb.rc:28 | post-fs | mkdir /config/usb_gadget/g1/functions/mass_storage.usb0
vendor/init/hw/init.mt6765.usb.rc:29 | post-fs | mkdir /config/usb_gadget/g1/functions/hid.gs0
vendor/init/hw/init.mt6765.usb.rc:30 | post-fs | mkdir /config/usb_gadget/g1/functions/via_modem.gs0
vendor/init/hw/init.mt6765.usb.rc:31 | post-fs | mkdir /config/usb_gadget/g1/functions/via_ets.gs0
vendor/init/hw/init.mt6765.usb.rc:32 | post-fs | mkdir /config/usb_gadget/g1/functions/via_atc.gs0
vendor/init/hw/init.mt6765.usb.rc:33 | post-fs | mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:34 | post-fs | mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:35 | post-fs | write /config/usb_gadget/g1/os_desc/b_vendor_code 0x1
vendor/init/hw/init.mt6765.usb.rc:36 | post-fs | write /config/usb_gadget/g1/os_desc/qw_sign "MSFT100"
vendor/init/hw/init.mt6765.usb.rc:37 | post-fs | write /config/usb_gadget/g1/configs/b.1/MaxPower 500
vendor/init/hw/init.mt6765.usb.rc:38 | post-fs | symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1
vendor/init/hw/init.mt6765.usb.rc:39 | post-fs | mount functionfs adb /dev/usb-ffs/adb uid=2000,gid=2000
vendor/init/hw/init.mt6765.usb.rc:42 | boot | setprop sys.usb.configfs 1
vendor/init/hw/init.mt6765.usb.rc:43 | boot | setprop sys.usb.ffs.aio_compat 1
vendor/init/hw/init.mt6765.usb.rc:44 | boot | setprop vendor.usb.controller "musb-hdrc"
vendor/init/hw/init.mt6765.usb.rc:45 | boot | setprop vendor.usb.acm_cnt 0
vendor/init/hw/init.mt6765.usb.rc:46 | boot | setprop vendor.usb.acm_port0 ""
vendor/init/hw/init.mt6765.usb.rc:47 | boot | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:48 | boot | setprop vendor.usb.acm_enable 0
vendor/init/hw/init.mt6765.usb.rc:49 | boot | write /sys/class/android_usb/android0/f_mtp/cpu_mask 0xF0
vendor/init/hw/init.mt6765.usb.rc:50 | boot | chmod 0664 /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6765.usb.rc:51 | boot | chown root system /sys/class/android_usb/android0/iSerial
vendor/init/hw/init.mt6765.usb.rc:53 | boot | chown radio system /sys/class/usb_rawbulk/data/enable
vendor/init/hw/init.mt6765.usb.rc:54 | boot | chmod 0660 /sys/class/usb_rawbulk/data/enable
vendor/init/hw/init.mt6765.usb.rc:55 | boot | chown radio system /sys/class/usb_rawbulk/ets/enable
vendor/init/hw/init.mt6765.usb.rc:56 | boot | chmod 0660 /sys/class/usb_rawbulk/ets/enable
vendor/init/hw/init.mt6765.usb.rc:57 | boot | chown radio system /sys/class/usb_rawbulk/atc/enable
vendor/init/hw/init.mt6765.usb.rc:58 | boot | chmod 0660 /sys/class/usb_rawbulk/atc/enable
vendor/init/hw/init.mt6765.usb.rc:59 | boot | chown radio system /sys/class/usb_rawbulk/pcv/enable
vendor/init/hw/init.mt6765.usb.rc:60 | boot | chmod 0660 /sys/class/usb_rawbulk/pcv/enable
vendor/init/hw/init.mt6765.usb.rc:61 | boot | chown radio system /sys/class/usb_rawbulk/gps/enable
vendor/init/hw/init.mt6765.usb.rc:62 | boot | chmod 0660 /sys/class/usb_rawbulk/gps/enable
vendor/init/hw/init.mt6765.usb.rc:63 | boot | chown system radio /dev/ttyGS0
vendor/init/hw/init.mt6765.usb.rc:64 | boot | chmod 0660 /dev/ttyGS0
vendor/init/hw/init.mt6765.usb.rc:65 | boot | chown system radio /dev/ttyGS1
vendor/init/hw/init.mt6765.usb.rc:66 | boot | chmod 0660 /dev/ttyGS1
vendor/init/hw/init.mt6765.usb.rc:67 | boot | chown system radio /dev/ttyGS2
vendor/init/hw/init.mt6765.usb.rc:68 | boot | chmod 0660 /dev/ttyGS2
vendor/init/hw/init.mt6765.usb.rc:69 | boot | chown system radio /dev/ttyGS3
vendor/init/hw/init.mt6765.usb.rc:70 | boot | chmod 0660 /dev/ttyGS3
vendor/init/hw/init.mt6765.usb.rc:73 | charger | mkdir /config/usb_gadget/g1 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:74 | charger | write /config/usb_gadget/g1/idVendor 0x0E8D
vendor/init/hw/init.mt6765.usb.rc:75 | charger | write /config/usb_gadget/g1/bcdDevice 0x0223
vendor/init/hw/init.mt6765.usb.rc:76 | charger | write /config/usb_gadget/g1/bcdUSB 0x0200
vendor/init/hw/init.mt6765.usb.rc:77 | charger | mkdir /config/usb_gadget/g1/strings/0x409 0770
vendor/init/hw/init.mt6765.usb.rc:78 | charger | write /config/usb_gadget/g1/strings/0x409/serialnumber ${ro.serialno}
vendor/init/hw/init.mt6765.usb.rc:79 | charger | write /config/usb_gadget/g1/strings/0x409/manufacturer ${ro.product.manufacturer}
vendor/init/hw/init.mt6765.usb.rc:80 | charger | write /config/usb_gadget/g1/strings/0x409/product ${ro.product.model}
vendor/init/hw/init.mt6765.usb.rc:81 | charger | mkdir /config/usb_gadget/g1/functions/hid.gs0
vendor/init/hw/init.mt6765.usb.rc:82 | charger | mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:83 | charger | mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell
vendor/init/hw/init.mt6765.usb.rc:84 | charger | write /config/usb_gadget/g1/configs/b.1/MaxPower 500
vendor/init/hw/init.mt6765.usb.rc:85 | charger | mount functionfs adb /dev/usb-ffs/adb uid=2000,gid=2000
vendor/init/hw/init.mt6765.usb.rc:86 | charger | setprop sys.usb.configfs 1
vendor/init/hw/init.mt6765.usb.rc:87 | charger | setprop vendor.usb.controller "musb-hdrc"
vendor/init/hw/init.mt6765.usb.rc:88 | charger | setprop vendor.usb.acm_cnt 0
vendor/init/hw/init.mt6765.usb.rc:89 | charger | setprop vendor.usb.acm_port0 ""
vendor/init/hw/init.mt6765.usb.rc:90 | charger | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:91 | charger | setprop vendor.usb.acm_enable 0
vendor/init/hw/init.mt6765.usb.rc:92 | charger | setprop sys.usb.config hid
vendor/init/hw/init.mt6765.usb.rc:96 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor ${vendor.usb.vid}
vendor/init/hw/init.mt6765.usb.rc:97 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:98 | property:sys.usb.config=none && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f5
vendor/init/hw/init.mt6765.usb.rc:99 | property:sys.usb.config=none && property:sys.usb.configfs=1 | write /sys/devices/platform/mt_usb/saving 0
vendor/init/hw/init.mt6765.usb.rc:103 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "HID"
vendor/init/hw/init.mt6765.usb.rc:104 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x20FF
vendor/init/hw/init.mt6765.usb.rc:105 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/functions/hid.gs0/protocol 0
vendor/init/hw/init.mt6765.usb.rc:106 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/functions/hid.gs0/subclass 0
vendor/init/hw/init.mt6765.usb.rc:109 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/hid.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:110 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:111 | property:sys.usb.config=hid && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:117 | property:sys.usb.config=adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x201C
vendor/init/hw/init.mt6765.usb.rc:120 | property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2006
vendor/init/hw/init.mt6765.usb.rc:121 | property:sys.usb.config=adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:124 | property:sys.usb.config=adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2029
vendor/init/hw/init.mt6765.usb.rc:128 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"
vendor/init/hw/init.mt6765.usb.rc:129 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:130 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:131 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:132 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:133 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:134 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:135 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:136 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:140 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb"
vendor/init/hw/init.mt6765.usb.rc:141 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:142 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:143 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:144 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:149 | property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2008
vendor/init/hw/init.mt6765.usb.rc:152 | property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2012
vendor/init/hw/init.mt6765.usb.rc:153 | property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:156 | property:sys.usb.config=mtp && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x202A
vendor/init/hw/init.mt6765.usb.rc:160 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_acm"
vendor/init/hw/init.mt6765.usb.rc:161 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:162 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/os_desc/use 1
vendor/init/hw/init.mt6765.usb.rc:163 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:164 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:165 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:166 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:167 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:168 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:172 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"
vendor/init/hw/init.mt6765.usb.rc:173 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:174 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/os_desc/use 1
vendor/init/hw/init.mt6765.usb.rc:175 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:176 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:177 | property:sys.usb.config=mtp && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:182 | property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x201D
vendor/init/hw/init.mt6765.usb.rc:185 | property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x200A
vendor/init/hw/init.mt6765.usb.rc:186 | property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:189 | property:sys.usb.config=mtp,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2026
vendor/init/hw/init.mt6765.usb.rc:193 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:194 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:195 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/os_desc/use 1
vendor/init/hw/init.mt6765.usb.rc:196 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:197 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:198 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:199 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:200 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:201 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:202 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:206 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp_adb"
vendor/init/hw/init.mt6765.usb.rc:207 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:208 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/os_desc/use 1
vendor/init/hw/init.mt6765.usb.rc:209 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:210 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:211 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:212 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mtp,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:217 | property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x200B
vendor/init/hw/init.mt6765.usb.rc:220 | property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2013
vendor/init/hw/init.mt6765.usb.rc:221 | property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:224 | property:sys.usb.config=ptp && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x202B
vendor/init/hw/init.mt6765.usb.rc:228 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_acm"
vendor/init/hw/init.mt6765.usb.rc:229 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:230 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:231 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:232 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:233 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:234 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:235 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:239 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp"
vendor/init/hw/init.mt6765.usb.rc:240 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:241 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:242 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:243 | property:sys.usb.config=ptp && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:248 | property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x200C
vendor/init/hw/init.mt6765.usb.rc:251 | property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x200D
vendor/init/hw/init.mt6765.usb.rc:252 | property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:255 | property:sys.usb.config=ptp,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2027
vendor/init/hw/init.mt6765.usb.rc:259 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:260 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:261 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:262 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:263 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:264 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:265 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:266 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:267 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:271 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ptp_adb"
vendor/init/hw/init.mt6765.usb.rc:272 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:273 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/ptp.gs1 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:274 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:275 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:276 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=ptp,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:281 | property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2004
vendor/init/hw/init.mt6765.usb.rc:284 | property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2011
vendor/init/hw/init.mt6765.usb.rc:285 | property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:288 | property:sys.usb.config=rndis && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x202C
vendor/init/hw/init.mt6765.usb.rc:292 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | mkdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:293 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"
vendor/init/hw/init.mt6765.usb.rc:294 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:295 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:296 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:297 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:298 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:299 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:300 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:304 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | mkdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:305 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis"
vendor/init/hw/init.mt6765.usb.rc:306 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:307 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:308 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:309 | property:sys.usb.config=rndis && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:313 | property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2005
vendor/init/hw/init.mt6765.usb.rc:316 | property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2010
vendor/init/hw/init.mt6765.usb.rc:317 | property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:320 | property:sys.usb.config=rndis,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2028
vendor/init/hw/init.mt6765.usb.rc:324 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | mkdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:325 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:326 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:327 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:328 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:329 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:330 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:331 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:332 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:333 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:337 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | mkdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:338 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"
vendor/init/hw/init.mt6765.usb.rc:339 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:340 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/rndis.gs4 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:341 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:342 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:343 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=rndis,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:347 | property:sys.usb.config=midi && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2046
vendor/init/hw/init.mt6765.usb.rc:350 | property:sys.usb.config=midi,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2048
vendor/init/hw/init.mt6765.usb.rc:354 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:355 | property:sys.usb.config=accessory && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d00
vendor/init/hw/init.mt6765.usb.rc:358 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:359 | property:sys.usb.config=accessory,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d01
vendor/init/hw/init.mt6765.usb.rc:363 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:364 | property:sys.usb.config=audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d02
vendor/init/hw/init.mt6765.usb.rc:367 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:368 | property:sys.usb.config=audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d03
vendor/init/hw/init.mt6765.usb.rc:372 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:373 | property:sys.usb.config=accessory,audio_source && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d04
vendor/init/hw/init.mt6765.usb.rc:376 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idVendor 0x18d1
vendor/init/hw/init.mt6765.usb.rc:377 | property:sys.usb.config=accessory,audio_source,adb && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2d05
vendor/init/hw/init.mt6765.usb.rc:381 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2031
vendor/init/hw/init.mt6765.usb.rc:384 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2033
vendor/init/hw/init.mt6765.usb.rc:385 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:390 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "via_bypass"
vendor/init/hw/init.mt6765.usb.rc:391 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:392 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:393 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:394 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:395 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:396 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:397 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:398 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:403 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "via_bypass"
vendor/init/hw/init.mt6765.usb.rc:404 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:405 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:406 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:407 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:408 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:409 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:410 | property:sys.usb.config=via_bypass && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:414 | property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2032
vendor/init/hw/init.mt6765.usb.rc:417 | property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2034
vendor/init/hw/init.mt6765.usb.rc:418 | property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:421 | property:sys.usb.config=via_bypass,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2050
vendor/init/hw/init.mt6765.usb.rc:424 | property:sys.usb.config=via_bypass,adb && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/init.mt6765.usb.rc:430 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:431 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:432 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:433 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:434 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:435 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:436 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:437 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f5
vendor/init/hw/init.mt6765.usb.rc:438 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:439 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:445 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb_acm2"
vendor/init/hw/init.mt6765.usb.rc:446 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:447 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:448 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:449 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:450 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:451 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:452 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:453 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:458 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "rndis_adb"
vendor/init/hw/init.mt6765.usb.rc:459 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:460 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:461 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_modem.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:462 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_ets.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:463 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/via_atc.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:464 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:465 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:466 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=via_bypass,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:471 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2002
vendor/init/hw/init.mt6765.usb.rc:474 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x200F
vendor/init/hw/init.mt6765.usb.rc:475 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:478 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x202D
vendor/init/hw/init.mt6765.usb.rc:482 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"
vendor/init/hw/init.mt6765.usb.rc:483 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:484 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:485 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:486 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:487 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:488 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:489 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:493 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums"
vendor/init/hw/init.mt6765.usb.rc:494 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:495 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:496 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:497 | property:sys.usb.config=mass_storage && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:501 | property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2003
vendor/init/hw/init.mt6765.usb.rc:504 | property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2006
vendor/init/hw/init.mt6765.usb.rc:505 | property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:508 | property:sys.usb.config=mass_storage,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2029
vendor/init/hw/init.mt6765.usb.rc:511 | property:sys.usb.config=mass_storage,adb && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/init.mt6765.usb.rc:515 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:516 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:517 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:518 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:519 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:520 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:521 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:522 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:523 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:527 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb"
vendor/init/hw/init.mt6765.usb.rc:528 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:529 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:530 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:531 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:532 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=mass_storage,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:537 | property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2002
vendor/init/hw/init.mt6765.usb.rc:540 | property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x200F
vendor/init/hw/init.mt6765.usb.rc:541 | property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:544 | property:sys.usb.config=bicr && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x202D
vendor/init/hw/init.mt6765.usb.rc:548 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_acm"
vendor/init/hw/init.mt6765.usb.rc:549 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:550 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:551 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1
vendor/init/hw/init.mt6765.usb.rc:552 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"
vendor/init/hw/init.mt6765.usb.rc:553 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:554 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:555 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:556 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:557 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=1 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:561 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums"
vendor/init/hw/init.mt6765.usb.rc:562 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:563 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1
vendor/init/hw/init.mt6765.usb.rc:564 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"
vendor/init/hw/init.mt6765.usb.rc:565 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:566 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:567 | property:sys.usb.config=bicr && property:vendor.usb.acm_enable=0 && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:571 | property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=0 && \ | setprop vendor.usb.pid 0x2003
vendor/init/hw/init.mt6765.usb.rc:574 | property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.pid 0x2006
vendor/init/hw/init.mt6765.usb.rc:575 | property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=1 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:578 | property:sys.usb.config=bicr,adb && property:vendor.usb.acm_cnt=2 && \ | setprop vendor.usb.pid 0x2029
vendor/init/hw/init.mt6765.usb.rc:581 | property:sys.usb.config=bicr,adb && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/init.mt6765.usb.rc:585 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb_acm"
vendor/init/hw/init.mt6765.usb.rc:586 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:587 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/init.mt6765.usb.rc:588 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1
vendor/init/hw/init.mt6765.usb.rc:589 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"
vendor/init/hw/init.mt6765.usb.rc:590 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:591 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:592 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port0} /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:593 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/acm.gs${vendor.usb.acm_port1} /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:594 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:595 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:599 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "ums_adb"
vendor/init/hw/init.mt6765.usb.rc:600 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/idProduct ${vendor.usb.pid}
vendor/init/hw/init.mt6765.usb.rc:601 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/cdrom 1
vendor/init/hw/init.mt6765.usb.rc:602 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/functions/mass_storage.usb0/lun.0/file "/dev/block/loop0"
vendor/init/hw/init.mt6765.usb.rc:603 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:604 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:605 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:606 | property:sys.usb.ffs.ready=1 && property:sys.usb.config=bicr,adb && \ | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:610 | property:persist.vendor.radio.port_index="" | setprop vendor.usb.acm_idx ""
vendor/init/hw/init.mt6765.usb.rc:611 | property:persist.vendor.radio.port_index="" | setprop vendor.usb.acm_cnt 0
vendor/init/hw/init.mt6765.usb.rc:612 | property:persist.vendor.radio.port_index="" | setprop vendor.usb.acm_enable 0
vendor/init/hw/init.mt6765.usb.rc:613 | property:persist.vendor.radio.port_index="" | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:614 | property:persist.vendor.radio.port_index="" | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:617 | property:persist.vendor.radio.port_index="1" | setprop sys.usb.config acm_gs0
vendor/init/hw/init.mt6765.usb.rc:619 | property:sys.usb.config=mass_storage,adb,acm | setprop sys.usb.config acm_gs0
vendor/init/hw/init.mt6765.usb.rc:621 | property:sys.usb.config=acm_gs0 | setprop vendor.usb.acm_port0 0
vendor/init/hw/init.mt6765.usb.rc:622 | property:sys.usb.config=acm_gs0 | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:623 | property:sys.usb.config=acm_gs0 | setprop vendor.usb.acm_cnt 1
vendor/init/hw/init.mt6765.usb.rc:624 | property:sys.usb.config=acm_gs0 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:625 | property:sys.usb.config=acm_gs0 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:626 | property:sys.usb.config=acm_gs0 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:627 | property:sys.usb.config=acm_gs0 | setprop vendor.usb.acm_idx "1"
vendor/init/hw/init.mt6765.usb.rc:630 | property:persist.vendor.radio.port_index="2" | setprop sys.usb.config acm_gs1
vendor/init/hw/init.mt6765.usb.rc:632 | property:sys.usb.config=acm_gs1 | setprop vendor.usb.acm_port0 1
vendor/init/hw/init.mt6765.usb.rc:633 | property:sys.usb.config=acm_gs1 | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:634 | property:sys.usb.config=acm_gs1 | setprop vendor.usb.acm_cnt 1
vendor/init/hw/init.mt6765.usb.rc:635 | property:sys.usb.config=acm_gs1 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:636 | property:sys.usb.config=acm_gs1 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:637 | property:sys.usb.config=acm_gs1 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:638 | property:sys.usb.config=acm_gs1 | setprop vendor.usb.acm_idx "2"
vendor/init/hw/init.mt6765.usb.rc:641 | property:persist.vendor.radio.port_index="3" | setprop sys.usb.config acm_gs2
vendor/init/hw/init.mt6765.usb.rc:643 | property:sys.usb.config=acm_third | setprop sys.usb.config acm_gs2
vendor/init/hw/init.mt6765.usb.rc:645 | property:sys.usb.config=acm_gs2 | setprop vendor.usb.acm_port0 2
vendor/init/hw/init.mt6765.usb.rc:646 | property:sys.usb.config=acm_gs2 | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:647 | property:sys.usb.config=acm_gs2 | setprop vendor.usb.acm_cnt 1
vendor/init/hw/init.mt6765.usb.rc:648 | property:sys.usb.config=acm_gs2 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:649 | property:sys.usb.config=acm_gs2 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:650 | property:sys.usb.config=acm_gs2 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:651 | property:sys.usb.config=acm_gs2 | setprop vendor.usb.acm_idx "3"
vendor/init/hw/init.mt6765.usb.rc:654 | property:persist.vendor.radio.port_index="4" | setprop sys.usb.config acm_gs3
vendor/init/hw/init.mt6765.usb.rc:656 | property:sys.usb.config=gs3 | setprop sys.usb.config acm_gs3
vendor/init/hw/init.mt6765.usb.rc:658 | property:sys.usb.config=mtp,gs3 | setprop sys.usb.config acm_gs3
vendor/init/hw/init.mt6765.usb.rc:660 | property:sys.usb.config=acm_gs3 | setprop vendor.usb.acm_port0 3
vendor/init/hw/init.mt6765.usb.rc:661 | property:sys.usb.config=acm_gs3 | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:662 | property:sys.usb.config=acm_gs3 | setprop vendor.usb.acm_cnt 1
vendor/init/hw/init.mt6765.usb.rc:663 | property:sys.usb.config=acm_gs3 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:664 | property:sys.usb.config=acm_gs3 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:665 | property:sys.usb.config=acm_gs3 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:666 | property:sys.usb.config=acm_gs3 | setprop vendor.usb.acm_idx "4"
vendor/init/hw/init.mt6765.usb.rc:669 | property:sys.usb.config=mtp,gs0gs1 | setprop sys.usb.config acm_gs0gs1
vendor/init/hw/init.mt6765.usb.rc:671 | property:sys.usb.config=gs0gs1 | setprop sys.usb.config acm_gs0gs1
vendor/init/hw/init.mt6765.usb.rc:673 | property:sys.usb.config=rndis,gs0gs1 | setprop sys.usb.config acm_gs0gs1
vendor/init/hw/init.mt6765.usb.rc:675 | property:sys.usb.config=rndis,adb,gs0gs1 | setprop sys.usb.config acm_gs0gs1
vendor/init/hw/init.mt6765.usb.rc:677 | property:sys.usb.config=acm_gs0gs1 | setprop vendor.usb.acm_port0 0
vendor/init/hw/init.mt6765.usb.rc:678 | property:sys.usb.config=acm_gs0gs1 | setprop vendor.usb.acm_port1 1
vendor/init/hw/init.mt6765.usb.rc:679 | property:sys.usb.config=acm_gs0gs1 | setprop vendor.usb.acm_cnt 2
vendor/init/hw/init.mt6765.usb.rc:680 | property:sys.usb.config=acm_gs0gs1 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:681 | property:sys.usb.config=acm_gs0gs1 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:682 | property:sys.usb.config=acm_gs0gs1 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:683 | property:sys.usb.config=acm_gs0gs1 | setprop vendor.usb.acm_idx "1,2"
vendor/init/hw/init.mt6765.usb.rc:686 | property:sys.usb.config=gs3,dual_acm | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:688 | property:sys.usb.config=mtp,gs3,dual_acm | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:690 | property:sys.usb.config=mtp,gs0gs3 | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:692 | property:sys.usb.config=gs0gs3 | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:694 | property:sys.usb.config=rndis,gs0gs3 | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:696 | property:sys.usb.config=rndis,adb,gs0gs3 | setprop sys.usb.config acm_gs0gs3
vendor/init/hw/init.mt6765.usb.rc:698 | property:sys.usb.config=acm_gs0gs3 | setprop vendor.usb.acm_port0 0
vendor/init/hw/init.mt6765.usb.rc:699 | property:sys.usb.config=acm_gs0gs3 | setprop vendor.usb.acm_port1 3
vendor/init/hw/init.mt6765.usb.rc:700 | property:sys.usb.config=acm_gs0gs3 | setprop vendor.usb.acm_cnt 2
vendor/init/hw/init.mt6765.usb.rc:701 | property:sys.usb.config=acm_gs0gs3 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:702 | property:sys.usb.config=acm_gs0gs3 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:703 | property:sys.usb.config=acm_gs0gs3 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:704 | property:sys.usb.config=acm_gs0gs3 | setprop vendor.usb.acm_idx "1,4"
vendor/init/hw/init.mt6765.usb.rc:707 | property:sys.usb.config=gs1gs3 | setprop sys.usb.config acm_gs1gs3
vendor/init/hw/init.mt6765.usb.rc:709 | property:sys.usb.config=mtp,gs1gs3 | setprop sys.usb.config acm_gs1gs3
vendor/init/hw/init.mt6765.usb.rc:711 | property:sys.usb.config=via_bypass,gs1gs3 | setprop sys.usb.config acm_gs1gs3
vendor/init/hw/init.mt6765.usb.rc:713 | property:sys.usb.config=rndis,gs1gs3 | setprop sys.usb.config acm_gs1gs3
vendor/init/hw/init.mt6765.usb.rc:715 | property:sys.usb.config=acm_gs1gs3 | setprop vendor.usb.acm_port0 1
vendor/init/hw/init.mt6765.usb.rc:716 | property:sys.usb.config=acm_gs1gs3 | setprop vendor.usb.acm_port1 3
vendor/init/hw/init.mt6765.usb.rc:717 | property:sys.usb.config=acm_gs1gs3 | setprop vendor.usb.acm_cnt 2
vendor/init/hw/init.mt6765.usb.rc:718 | property:sys.usb.config=acm_gs1gs3 | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:719 | property:sys.usb.config=acm_gs1gs3 | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:720 | property:sys.usb.config=acm_gs1gs3 | setprop sys.usb.config ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:721 | property:sys.usb.config=acm_gs1gs3 | setprop vendor.usb.acm_idx "2,4"
vendor/init/hw/init.mt6765.usb.rc:725 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/init.mt6765.usb.rc:726 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceClass 0
vendor/init/hw/init.mt6765.usb.rc:727 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceSubClass 0
vendor/init/hw/init.mt6765.usb.rc:728 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/bDeviceProtocol 0
vendor/init/hw/init.mt6765.usb.rc:729 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:730 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:731 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:732 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:733 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rm /config/usb_gadget/g1/configs/b.1/f5
vendor/init/hw/init.mt6765.usb.rc:734 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | rmdir /config/usb_gadget/g1/functions/rndis.gs4
vendor/init/hw/init.mt6765.usb.rc:735 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | write /sys/devices/platform/mt_usb/saving 0
vendor/init/hw/init.mt6765.usb.rc:736 | property:vendor.usb.clear=1 && property:sys.usb.configfs=1 | setprop vendor.usb.clear 2
vendor/init/hw/init.mt6765.usb.rc:740 | property:sys.usb.ffs.ready=0 | setprop vendor.usb.ffs.ready 0
vendor/init/hw/init.mt6765.usb.rc:742 | property:sys.usb.ffs.ready=1 | setprop vendor.usb.ffs.ready 1
vendor/init/hw/init.mt6765.usb.rc:745 | property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1 | stop adbd
vendor/init/hw/init.mt6765.usb.rc:746 | property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1 | setprop vendor.usb.ffs.ready 0
vendor/init/hw/init.mt6765.usb.rc:747 | property:sys.usb.config=atm_gs0 && property:sys.usb.configfs=1 | setprop vendor.usb.clear 1
vendor/init/hw/init.mt6765.usb.rc:751 | property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0 && \ | start adbd
vendor/init/hw/init.mt6765.usb.rc:755 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.acm_port0 0
vendor/init/hw/init.mt6765.usb.rc:756 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.acm_port1 ""
vendor/init/hw/init.mt6765.usb.rc:757 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.acm_cnt 1
vendor/init/hw/init.mt6765.usb.rc:758 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:759 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | write /sys/class/udc/musb-hdrc/device/saving 1
vendor/init/hw/init.mt6765.usb.rc:760 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "atm_gs0"
vendor/init/hw/init.mt6765.usb.rc:761 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | write /config/usb_gadget/g1/idProduct 0x200A
vendor/init/hw/init.mt6765.usb.rc:762 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:763 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:764 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:765 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:768 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.clear 0
vendor/init/hw/init.mt6765.usb.rc:769 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0 && \ | setprop vendor.usb.acm_idx "1"
vendor/init/hw/init.mt6765.usb.rc:772 | property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1 | stop adbd
vendor/init/hw/init.mt6765.usb.rc:773 | property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1 | setprop vendor.usb.ffs.ready 0
vendor/init/hw/init.mt6765.usb.rc:774 | property:sys.usb.config=atm_gs0gs3 && property:sys.usb.configfs=1 | setprop vendor.usb.clear 1
vendor/init/hw/init.mt6765.usb.rc:778 | property:vendor.usb.ffs.ready=0 && property:sys.usb.config=atm_gs0gs3 && \ | start adbd
vendor/init/hw/init.mt6765.usb.rc:782 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.acm_port0 0
vendor/init/hw/init.mt6765.usb.rc:783 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.acm_port1 3
vendor/init/hw/init.mt6765.usb.rc:784 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.acm_cnt 2
vendor/init/hw/init.mt6765.usb.rc:785 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.acm_enable 1
vendor/init/hw/init.mt6765.usb.rc:786 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | write /sys/class/udc/musb-hdrc/device/saving 1
vendor/init/hw/init.mt6765.usb.rc:787 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | write /config/usb_gadget/g1/idProduct 0x2026
vendor/init/hw/init.mt6765.usb.rc:788 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:789 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/init.mt6765.usb.rc:790 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/init.mt6765.usb.rc:791 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/init.mt6765.usb.rc:792 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:795 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.clear 0
vendor/init/hw/init.mt6765.usb.rc:796 | property:vendor.usb.ffs.ready=1 && property:sys.usb.config=atm_gs0gs3 && \ | setprop vendor.usb.acm_idx "1,4"
vendor/init/hw/init.mt6765.usb.rc:800 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "mtp"
vendor/init/hw/init.mt6765.usb.rc:801 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2008
vendor/init/hw/init.mt6765.usb.rc:802 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/os_desc/use 1
vendor/init/hw/init.mt6765.usb.rc:803 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/mtp.gs0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/init.mt6765.usb.rc:804 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC ${vendor.usb.controller}
vendor/init/hw/init.mt6765.usb.rc:805 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | setprop sys.usb.state ${sys.usb.config}
vendor/init/hw/init.mt6765.usb.rc:806 | property:sys.usb.config=cdp && property:sys.usb.configfs=1 | setprop vendor.usb.pid 0x2008
vendor/init/hw/init.mt6765.usb.rc:809 | property:sys.usb.config=charging_yes | setprop vendor.usb.state ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:810 | property:sys.usb.config=charging_yes | setprop vendor.usb.charging yes
vendor/init/hw/init.mt6765.usb.rc:811 | property:sys.usb.config=charging_yes | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:812 | property:sys.usb.config=charging_yes | setprop sys.usb.config ${vendor.usb.state}
vendor/init/hw/init.mt6765.usb.rc:815 | property:sys.usb.config=charging_no | setprop vendor.usb.state ${sys.usb.state}
vendor/init/hw/init.mt6765.usb.rc:816 | property:sys.usb.config=charging_no | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:817 | property:sys.usb.config=charging_no | setprop sys.usb.config ${vendor.usb.state}
vendor/init/hw/init.mt6765.usb.rc:818 | property:sys.usb.config=charging_no | setprop vendor.usb.charging no
vendor/init/hw/init.mt6765.usb.rc:821 | property:vendor.usb.charging=yes | write /sys/class/udc/musb-hdrc/device/cmode 2
vendor/init/hw/init.mt6765.usb.rc:823 | property:vendor.usb.charging=no | write /sys/class/udc/musb-hdrc/device/cmode 1
vendor/init/hw/init.mt6765.usb.rc:826 | property:vendor.usb.bicr=yes | write /sys/class/android_usb/android0/f_mass_storage/bicr 1
vendor/init/hw/init.mt6765.usb.rc:827 | property:vendor.usb.bicr=yes | write /sys/class/android_usb/android0/f_mass_storage/lun/file "/dev/block/loop0"
vendor/init/hw/init.mt6765.usb.rc:829 | property:vendor.usb.bicr=no | write /sys/class/android_usb/android0/f_mass_storage/bicr 0
vendor/init/hw/init.mt6765.usb.rc:830 | property:vendor.usb.bicr=no | write /sys/class/android_usb/android0/f_mass_storage/lun/file "off"
vendor/init/hw/init.mt6765.usb.rc:833 | property:vendor.usb.port.mode=usb | write /sys/devices/platform/mt_usb/portmode 0
vendor/init/hw/init.mt6765.usb.rc:835 | property:vendor.usb.port.mode=uart | write /sys/devices/platform/mt_usb/portmode 1
vendor/init/hw/init.mt6765.usb.rc:838 | property:vendor.em.usb.set=USB_DRIVING_CAPABILITY | write /sys/kernel/debug/usb20_phy/USB_DRIVING_CAPABILITY ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:840 | property:vendor.em.usb.set=RG_USB20_TERM_VREF_SEL | write /sys/kernel/debug/usb20_phy/RG_USB20_TERM_VREF_SEL ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:842 | property:vendor.em.usb.set=RG_USB20_HSTX_SRCTRL | write /sys/kernel/debug/usb20_phy/RG_USB20_HSTX_SRCTRL ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:844 | property:vendor.em.usb.set=RG_USB20_VRT_VREF_SEL | write /sys/kernel/debug/usb20_phy/RG_USB20_VRT_VREF_SEL ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:846 | property:vendor.em.usb.set=RG_USB20_INTR_EN | write /sys/kernel/debug/usb20_phy/RG_USB20_INTR_EN ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:848 | property:vendor.em.usb.set=RG_USB20_PHY_REV6 | write /sys/kernel/debug/usb20_phy/RG_USB20_PHY_REV6 ${mediatek.em.usb.value}
vendor/init/hw/init.mt6765.usb.rc:851 | property:vendor.em.usb.bypass.enable=0 | write /sys/class/usb_rawbulk/gps/enable 1
vendor/init/hw/init.mt6765.usb.rc:853 | property:vendor.em.usb.bypass.enable=1 | write /sys/class/usb_rawbulk/pcv/enable 1
vendor/init/hw/init.mt6765.usb.rc:855 | property:vendor.em.usb.bypass.enable=2 | write /sys/class/usb_rawbulk/atc/enable 1
vendor/init/hw/init.mt6765.usb.rc:857 | property:vendor.em.usb.bypass.enable=3 | write /sys/class/usb_rawbulk/ets/enable 1
vendor/init/hw/init.mt6765.usb.rc:859 | property:vendor.em.usb.bypass.enable=4 | write /sys/class/usb_rawbulk/data/enable 1
vendor/init/hw/init.mt6765.usb.rc:861 | property:vendor.em.usb.bypass.disable=0 | write /sys/class/usb_rawbulk/gps/enable 0
vendor/init/hw/init.mt6765.usb.rc:863 | property:vendor.em.usb.bypass.disable=1 | write /sys/class/usb_rawbulk/pcv/enable 0
vendor/init/hw/init.mt6765.usb.rc:865 | property:vendor.em.usb.bypass.disable=2 | write /sys/class/usb_rawbulk/atc/enable 0
vendor/init/hw/init.mt6765.usb.rc:867 | property:vendor.em.usb.bypass.disable=3 | write /sys/class/usb_rawbulk/ets/enable 0
vendor/init/hw/init.mt6765.usb.rc:869 | property:vendor.em.usb.bypass.disable=4 | write /sys/class/usb_rawbulk/data/enable 0
vendor/init/hw/init.mt6765.usb.rc:873 | property:vendor.usb.config=* | setprop sys.usb.config ${vendor.usb.config}
vendor/init/hw/init.mt6765.usb.rc:878 | property:vendor.usb.test=* | setprop sys.usb.config none
vendor/init/hw/init.mt6765.usb.rc:879 | property:vendor.usb.test=* | setprop sys.usb.config ${vendor.usb.test}
vendor/init/hw/init.mt6765.usb.rc:883 | property:vendor.usb.printk=* | write /proc/sys/kernel/printk ${vendor.usb.printk}
vendor/init/hw/init.mt6765.usb.rc:886 | property:persist.vendor.usb.printk=* | setprop vendor.usb.printk ${persist.vendor.usb.printk}
vendor/init/hw/init.project.rc:6 | init | mkdir /mnt/media_rw/usbotg 0700 media_rw media_rw
vendor/init/hw/init.project.rc:7 | init | mkdir /storage/usbotg 0700 root root
vendor/init/hw/init.project.rc:12 | post-fs-data | chmod 0660 /dev/MAINAF
vendor/init/hw/init.project.rc:13 | post-fs-data | chown system camera /dev/MAINAF
vendor/init/hw/init.project.rc:15 | post-fs-data | chmod 0660 /dev/MAINAF2
vendor/init/hw/init.project.rc:16 | post-fs-data | chown system camera /dev/MAINAF2
vendor/init/hw/init.project.rc:18 | post-fs-data | chmod 0660 /dev/SUBAF
vendor/init/hw/init.project.rc:19 | post-fs-data | chown system camera /dev/SUBAF
vendor/init/hw/init.project.rc:21 | post-fs-data | chmod 0660 /dev/GAF001AF
vendor/init/hw/init.project.rc:22 | post-fs-data | chown system camera /dev/GAF001AF
vendor/init/hw/init.project.rc:24 | post-fs-data | chmod 0660 /dev/DW9714AF
vendor/init/hw/init.project.rc:25 | post-fs-data | chown system camera /dev/DW9714AF
vendor/init/hw/init.project.rc:27 | post-fs-data | chmod 0660 /dev/LC898212AF
vendor/init/hw/init.project.rc:28 | post-fs-data | chown system camera /dev/LC898212AF
vendor/init/hw/init.project.rc:30 | post-fs-data | chmod 0660 /dev/BU64745GWZAF
vendor/init/hw/init.project.rc:31 | post-fs-data | chown system camera /dev/BU64745GWZAF
vendor/init/hw/init.project.rc:34 | post-fs-data | chown system system /proc/smb/ScreenComm
vendor/init/hw/init.project.rc:35 | post-fs-data | chmod 0660 /proc/smb/ScreenComm
vendor/init/hw/init.project.rc:37 | post-fs-data | chmod 0660 /dev/spm
vendor/init/hw/init.project.rc:38 | post-fs-data | chown system system /dev/spm
vendor/init/hw/init.project.rc:63 | property:sys.boot_completed=1 | start remosaic_daemon
vendor/init/hw/init.project.rc:73 | charger | load_system_props
vendor/init/hw/init.sensor_1_0.rc:4 | post-fs-data | mkdir /data/vendor/sensor 0774 system system
vendor/init/hw/init.sensor_1_0.rc:6 | post-fs-data | chmod 0660 /dev/hwmsensor
vendor/init/hw/init.sensor_1_0.rc:7 | post-fs-data | chmod 0660 /dev/msensor
vendor/init/hw/init.sensor_1_0.rc:8 | post-fs-data | chmod 0660 /dev/gsensor
vendor/init/hw/init.sensor_1_0.rc:9 | post-fs-data | chmod 0660 /dev/als_ps
vendor/init/hw/init.sensor_1_0.rc:10 | post-fs-data | chmod 0660 /dev/gyroscope
vendor/init/hw/init.sensor_1_0.rc:11 | post-fs-data | chmod 0660 /dev/aal_als
vendor/init/hw/init.sensor_1_0.rc:12 | post-fs-data | chmod 0660 /dev/humidity
vendor/init/hw/init.sensor_1_0.rc:13 | post-fs-data | chmod 0660 /dev/barometer
vendor/init/hw/init.sensor_1_0.rc:14 | post-fs-data | chmod 0660 /dev/m_als_misc
vendor/init/hw/init.sensor_1_0.rc:15 | post-fs-data | chmod 0660 /dev/m_hrs_misc
vendor/init/hw/init.sensor_1_0.rc:16 | post-fs-data | chmod 0660 /dev/m_spo2_misc
vendor/init/hw/init.sensor_1_0.rc:17 | post-fs-data | chmod 0660 /dev/m_bprs_misc
vendor/init/hw/init.sensor_1_0.rc:18 | post-fs-data | chmod 0660 /dev/m_temp_misc
vendor/init/hw/init.sensor_1_0.rc:19 | post-fs-data | chmod 0660 /dev/m_alti_misc
vendor/init/hw/init.sensor_1_0.rc:20 | post-fs-data | chmod 0660 /dev/m_ebus_misc
vendor/init/hw/init.sensor_1_0.rc:21 | post-fs-data | chmod 0660 /dev/m_baro_misc
vendor/init/hw/init.sensor_1_0.rc:22 | post-fs-data | chmod 0660 /dev/m_hmdy_misc
vendor/init/hw/init.sensor_1_0.rc:23 | post-fs-data | chmod 0660 /dev/m_ps_misc
vendor/init/hw/init.sensor_1_0.rc:24 | post-fs-data | chmod 0660 /dev/m_acc_misc
vendor/init/hw/init.sensor_1_0.rc:25 | post-fs-data | chmod 0660 /dev/m_tilt_misc
vendor/init/hw/init.sensor_1_0.rc:26 | post-fs-data | chmod 0660 /dev/m_mag_misc
vendor/init/hw/init.sensor_1_0.rc:27 | post-fs-data | chmod 0660 /dev/m_gyro_misc
vendor/init/hw/init.sensor_1_0.rc:28 | post-fs-data | chmod 0660 /dev/m_act_misc
vendor/init/hw/init.sensor_1_0.rc:29 | post-fs-data | chmod 0660 /dev/m_pedo_misc
vendor/init/hw/init.sensor_1_0.rc:30 | post-fs-data | chmod 0660 /dev/m_situ_misc
vendor/init/hw/init.sensor_1_0.rc:31 | post-fs-data | chmod 0660 /dev/m_step_c_misc
vendor/init/hw/init.sensor_1_0.rc:32 | post-fs-data | chmod 0660 /dev/m_fusion_misc
vendor/init/hw/init.sensor_1_0.rc:33 | post-fs-data | chmod 0660 /dev/m_bio_misc
vendor/init/hw/init.sensor_1_0.rc:34 | post-fs-data | chmod 0660 /dev/biometric
vendor/init/hw/init.sensor_1_0.rc:35 | post-fs-data | chmod 0660 /dev/sensorlist
vendor/init/hw/init.sensor_1_0.rc:37 | post-fs-data | chown system system /dev/hwmsensor
vendor/init/hw/init.sensor_1_0.rc:38 | post-fs-data | chown system system /dev/msensor
vendor/init/hw/init.sensor_1_0.rc:39 | post-fs-data | chown radio system /dev/gsensor
vendor/init/hw/init.sensor_1_0.rc:40 | post-fs-data | chown radio system /dev/als_ps
vendor/init/hw/init.sensor_1_0.rc:41 | post-fs-data | chown radio system /dev/gyroscope
vendor/init/hw/init.sensor_1_0.rc:42 | post-fs-data | chown system system /dev/aal_als
vendor/init/hw/init.sensor_1_0.rc:43 | post-fs-data | chown system system /dev/humidity
vendor/init/hw/init.sensor_1_0.rc:44 | post-fs-data | chown system system /dev/barometer
vendor/init/hw/init.sensor_1_0.rc:45 | post-fs-data | chown system system /dev/m_als_misc
vendor/init/hw/init.sensor_1_0.rc:46 | post-fs-data | chown system system /dev/m_hrs_misc
vendor/init/hw/init.sensor_1_0.rc:47 | post-fs-data | chown system system /dev/m_spo2_misc
vendor/init/hw/init.sensor_1_0.rc:48 | post-fs-data | chown system system /dev/m_bprs_misc
vendor/init/hw/init.sensor_1_0.rc:49 | post-fs-data | chown system system /dev/m_temp_misc
vendor/init/hw/init.sensor_1_0.rc:50 | post-fs-data | chown system system /dev/m_alti_misc
vendor/init/hw/init.sensor_1_0.rc:51 | post-fs-data | chown system system /dev/m_ebus_misc
vendor/init/hw/init.sensor_1_0.rc:52 | post-fs-data | chown system system /dev/m_baro_misc
vendor/init/hw/init.sensor_1_0.rc:53 | post-fs-data | chown system system /dev/m_hmdy_misc
vendor/init/hw/init.sensor_1_0.rc:54 | post-fs-data | chown system system /dev/m_ps_misc
vendor/init/hw/init.sensor_1_0.rc:55 | post-fs-data | chown system system /dev/m_acc_misc
vendor/init/hw/init.sensor_1_0.rc:56 | post-fs-data | chown system system /dev/m_tilt_misc
vendor/init/hw/init.sensor_1_0.rc:57 | post-fs-data | chown system system /dev/m_mag_misc
vendor/init/hw/init.sensor_1_0.rc:58 | post-fs-data | chown system system /dev/m_gyro_misc
vendor/init/hw/init.sensor_1_0.rc:59 | post-fs-data | chown system system /dev/m_act_misc
vendor/init/hw/init.sensor_1_0.rc:60 | post-fs-data | chown system system /dev/m_pedo_misc
vendor/init/hw/init.sensor_1_0.rc:61 | post-fs-data | chown system system /dev/m_situ_misc
vendor/init/hw/init.sensor_1_0.rc:62 | post-fs-data | chown system system /dev/m_step_c_misc
vendor/init/hw/init.sensor_1_0.rc:63 | post-fs-data | chown system system /dev/m_fusion_misc
vendor/init/hw/init.sensor_1_0.rc:64 | post-fs-data | chown system system /dev/m_bio_misc
vendor/init/hw/init.sensor_1_0.rc:65 | post-fs-data | chown system system /dev/biometric
vendor/init/hw/init.sensor_1_0.rc:66 | post-fs-data | chown system system /dev/sensorlist
vendor/init/hw/init.sensor_1_0.rc:68 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/accenablenodata
vendor/init/hw/init.sensor_1_0.rc:69 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/accactive
vendor/init/hw/init.sensor_1_0.rc:70 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/accdelay
vendor/init/hw/init.sensor_1_0.rc:71 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/accbatch
vendor/init/hw/init.sensor_1_0.rc:72 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/accflush
vendor/init/hw/init.sensor_1_0.rc:73 | post-fs-data | chmod 0660 /sys/class/sensor/m_acc_misc/acccali
vendor/init/hw/init.sensor_1_0.rc:74 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/accenablenodata
vendor/init/hw/init.sensor_1_0.rc:75 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/accactive
vendor/init/hw/init.sensor_1_0.rc:76 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/accdelay
vendor/init/hw/init.sensor_1_0.rc:77 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/accbatch
vendor/init/hw/init.sensor_1_0.rc:78 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/accflush
vendor/init/hw/init.sensor_1_0.rc:79 | post-fs-data | chown system system /sys/class/sensor/m_acc_misc/acccali
vendor/init/hw/init.sensor_1_0.rc:81 | post-fs-data | chmod 0660 /sys/class/sensor/m_tilt_misc/tilt_enablenodata
vendor/init/hw/init.sensor_1_0.rc:82 | post-fs-data | chmod 0660 /sys/class/sensor/m_tilt_misc/tilt_active
vendor/init/hw/init.sensor_1_0.rc:83 | post-fs-data | chmod 0660 /sys/class/sensor/m_tilt_misc/tilt_delay
vendor/init/hw/init.sensor_1_0.rc:84 | post-fs-data | chmod 0660 /sys/class/sensor/m_tilt_misc/tilt_batch
vendor/init/hw/init.sensor_1_0.rc:85 | post-fs-data | chmod 0660 /sys/class/sensor/m_tilt_misc/tilt_flush
vendor/init/hw/init.sensor_1_0.rc:86 | post-fs-data | chown system system /sys/class/sensor/m_tilt_misc/tilt_enablenodata
vendor/init/hw/init.sensor_1_0.rc:87 | post-fs-data | chown system system /sys/class/sensor/m_tilt_misc/tilt_active
vendor/init/hw/init.sensor_1_0.rc:88 | post-fs-data | chown system system /sys/class/sensor/m_tilt_misc/tilt_delay
vendor/init/hw/init.sensor_1_0.rc:89 | post-fs-data | chown system system /sys/class/sensor/m_tilt_misc/tilt_batch
vendor/init/hw/init.sensor_1_0.rc:90 | post-fs-data | chown system system /sys/class/sensor/m_tilt_misc/tilt_flush
vendor/init/hw/init.sensor_1_0.rc:92 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/magactive
vendor/init/hw/init.sensor_1_0.rc:93 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/magdelay
vendor/init/hw/init.sensor_1_0.rc:94 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/magbatch
vendor/init/hw/init.sensor_1_0.rc:95 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/magflush
vendor/init/hw/init.sensor_1_0.rc:96 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/magcali
vendor/init/hw/init.sensor_1_0.rc:97 | post-fs-data | chmod 0660 /sys/class/sensor/m_mag_misc/maglibinfo
vendor/init/hw/init.sensor_1_0.rc:98 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/magactive
vendor/init/hw/init.sensor_1_0.rc:99 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/magdelay
vendor/init/hw/init.sensor_1_0.rc:100 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/magbatch
vendor/init/hw/init.sensor_1_0.rc:101 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/magflush
vendor/init/hw/init.sensor_1_0.rc:102 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/magcali
vendor/init/hw/init.sensor_1_0.rc:103 | post-fs-data | chown system system /sys/class/sensor/m_mag_misc/maglibinfo
vendor/init/hw/init.sensor_1_0.rc:105 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyroenablenodata
vendor/init/hw/init.sensor_1_0.rc:106 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyroactive
vendor/init/hw/init.sensor_1_0.rc:107 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyrodelay
vendor/init/hw/init.sensor_1_0.rc:108 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyrobatch
vendor/init/hw/init.sensor_1_0.rc:109 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyroflush
vendor/init/hw/init.sensor_1_0.rc:110 | post-fs-data | chmod 0660 /sys/class/sensor/m_gyro_misc/gyrocali
vendor/init/hw/init.sensor_1_0.rc:111 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyroenablenodata
vendor/init/hw/init.sensor_1_0.rc:112 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyroactive
vendor/init/hw/init.sensor_1_0.rc:113 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyrodelay
vendor/init/hw/init.sensor_1_0.rc:114 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyrobatch
vendor/init/hw/init.sensor_1_0.rc:115 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyroflush
vendor/init/hw/init.sensor_1_0.rc:116 | post-fs-data | chown system system /sys/class/sensor/m_gyro_misc/gyrocali
vendor/init/hw/init.sensor_1_0.rc:118 | post-fs-data | chmod 0660 /sys/class/sensor/m_als_misc/alsactive
vendor/init/hw/init.sensor_1_0.rc:119 | post-fs-data | chmod 0660 /sys/class/sensor/m_als_misc/alsdelay
vendor/init/hw/init.sensor_1_0.rc:120 | post-fs-data | chmod 0660 /sys/class/sensor/m_als_misc/alsbatch
vendor/init/hw/init.sensor_1_0.rc:121 | post-fs-data | chmod 0660 /sys/class/sensor/m_als_misc/alsflush
vendor/init/hw/init.sensor_1_0.rc:122 | post-fs-data | chmod 0660 /sys/class/sensor/m_als_misc/alscali
vendor/init/hw/init.sensor_1_0.rc:123 | post-fs-data | chmod 0660 /sys/class/sensor/m_hrs_misc/hrs_active
vendor/init/hw/init.sensor_1_0.rc:124 | post-fs-data | chmod 0660 /sys/class/sensor/m_hrs_misc/hrs_delay
vendor/init/hw/init.sensor_1_0.rc:125 | post-fs-data | chmod 0660 /sys/class/sensor/m_hrs_misc/hrs_batch
vendor/init/hw/init.sensor_1_0.rc:126 | post-fs-data | chmod 0660 /sys/class/sensor/m_hrs_misc/hrs_flush
vendor/init/hw/init.sensor_1_0.rc:127 | post-fs-data | chmod 0660 /sys/class/sensor/m_hrs_misc/hrs_cali
vendor/init/hw/init.sensor_1_0.rc:128 | post-fs-data | chmod 0660 /sys/class/sensor/m_ps_misc/psactive
vendor/init/hw/init.sensor_1_0.rc:129 | post-fs-data | chmod 0660 /sys/class/sensor/m_ps_misc/psdelay
vendor/init/hw/init.sensor_1_0.rc:130 | post-fs-data | chmod 0660 /sys/class/sensor/m_ps_misc/psbatch
vendor/init/hw/init.sensor_1_0.rc:131 | post-fs-data | chmod 0660 /sys/class/sensor/m_ps_misc/psflush
vendor/init/hw/init.sensor_1_0.rc:132 | post-fs-data | chmod 0660 /sys/class/sensor/m_ps_misc/pscali
vendor/init/hw/init.sensor_1_0.rc:133 | post-fs-data | chown system system /sys/class/sensor/m_als_misc/alsactive
vendor/init/hw/init.sensor_1_0.rc:134 | post-fs-data | chown system system /sys/class/sensor/m_als_misc/alsdelay
vendor/init/hw/init.sensor_1_0.rc:135 | post-fs-data | chown system system /sys/class/sensor/m_als_misc/alsbatch
vendor/init/hw/init.sensor_1_0.rc:136 | post-fs-data | chown system system /sys/class/sensor/m_als_misc/alsflush
vendor/init/hw/init.sensor_1_0.rc:137 | post-fs-data | chown system system /sys/class/sensor/m_als_misc/alscali
vendor/init/hw/init.sensor_1_0.rc:138 | post-fs-data | chown system system /sys/class/sensor/m_hrs_misc/hrs_active
vendor/init/hw/init.sensor_1_0.rc:139 | post-fs-data | chown system system /sys/class/sensor/m_hrs_misc/hrs_delay
vendor/init/hw/init.sensor_1_0.rc:140 | post-fs-data | chown system system /sys/class/sensor/m_hrs_misc/hrs_batch
vendor/init/hw/init.sensor_1_0.rc:141 | post-fs-data | chown system system /sys/class/sensor/m_hrs_misc/hrs_flush
vendor/init/hw/init.sensor_1_0.rc:142 | post-fs-data | chown system system /sys/class/sensor/m_hrs_misc/hrs_cali
vendor/init/hw/init.sensor_1_0.rc:143 | post-fs-data | chown system system /sys/class/sensor/m_ps_misc/psactive
vendor/init/hw/init.sensor_1_0.rc:144 | post-fs-data | chown system system /sys/class/sensor/m_ps_misc/psdelay
vendor/init/hw/init.sensor_1_0.rc:145 | post-fs-data | chown system system /sys/class/sensor/m_ps_misc/psbatch
vendor/init/hw/init.sensor_1_0.rc:146 | post-fs-data | chown system system /sys/class/sensor/m_ps_misc/psflush
vendor/init/hw/init.sensor_1_0.rc:147 | post-fs-data | chown system system /sys/class/sensor/m_ps_misc/pscali
vendor/init/hw/init.sensor_1_0.rc:149 | post-fs-data | chmod 0660 /sys/class/sensor/m_spo2_misc/spo2_active
vendor/init/hw/init.sensor_1_0.rc:150 | post-fs-data | chmod 0660 /sys/class/sensor/m_spo2_misc/spo2_delay
vendor/init/hw/init.sensor_1_0.rc:151 | post-fs-data | chmod 0660 /sys/class/sensor/m_spo2_misc/spo2_batch
vendor/init/hw/init.sensor_1_0.rc:152 | post-fs-data | chmod 0660 /sys/class/sensor/m_spo2_misc/spo2_flush
vendor/init/hw/init.sensor_1_0.rc:153 | post-fs-data | chmod 0660 /sys/class/sensor/m_spo2_misc/spo2_name
vendor/init/hw/init.sensor_1_0.rc:154 | post-fs-data | chown system system /sys/class/sensor/m_spo2_misc/spo2_active
vendor/init/hw/init.sensor_1_0.rc:155 | post-fs-data | chown system system /sys/class/sensor/m_spo2_misc/spo2_delay
vendor/init/hw/init.sensor_1_0.rc:156 | post-fs-data | chown system system /sys/class/sensor/m_spo2_misc/spo2_batch
vendor/init/hw/init.sensor_1_0.rc:157 | post-fs-data | chown system system /sys/class/sensor/m_spo2_misc/spo2_flush
vendor/init/hw/init.sensor_1_0.rc:158 | post-fs-data | chown system system /sys/class/sensor/m_spo2_misc/spo2_name
vendor/init/hw/init.sensor_1_0.rc:160 | post-fs-data | chmod 0660 /sys/class/sensor/m_bprs_misc/bprs_active
vendor/init/hw/init.sensor_1_0.rc:161 | post-fs-data | chmod 0660 /sys/class/sensor/m_bprs_misc/bprs_delay
vendor/init/hw/init.sensor_1_0.rc:162 | post-fs-data | chmod 0660 /sys/class/sensor/m_bprs_misc/bprs_batch
vendor/init/hw/init.sensor_1_0.rc:163 | post-fs-data | chmod 0660 /sys/class/sensor/m_bprs_misc/bprs_flush
vendor/init/hw/init.sensor_1_0.rc:164 | post-fs-data | chmod 0660 /sys/class/sensor/m_bprs_misc/bprs_name
vendor/init/hw/init.sensor_1_0.rc:165 | post-fs-data | chown system system /sys/class/sensor/m_bprs_misc/bprs_active
vendor/init/hw/init.sensor_1_0.rc:166 | post-fs-data | chown system system /sys/class/sensor/m_bprs_misc/bprs_delay
vendor/init/hw/init.sensor_1_0.rc:167 | post-fs-data | chown system system /sys/class/sensor/m_bprs_misc/bprs_batch
vendor/init/hw/init.sensor_1_0.rc:168 | post-fs-data | chown system system /sys/class/sensor/m_bprs_misc/bprs_flush
vendor/init/hw/init.sensor_1_0.rc:169 | post-fs-data | chown system system /sys/class/sensor/m_bprs_misc/bprs_name
vendor/init/hw/init.sensor_1_0.rc:171 | post-fs-data | chmod 0660 /sys/class/sensor/m_temp_misc/tempenablenodata
vendor/init/hw/init.sensor_1_0.rc:172 | post-fs-data | chmod 0660 /sys/class/sensor/m_temp_misc/tempactive
vendor/init/hw/init.sensor_1_0.rc:173 | post-fs-data | chmod 0660 /sys/class/sensor/m_temp_misc/tempdelay
vendor/init/hw/init.sensor_1_0.rc:174 | post-fs-data | chmod 0660 /sys/class/sensor/m_temp_misc/tempbatch
vendor/init/hw/init.sensor_1_0.rc:175 | post-fs-data | chmod 0660 /sys/class/sensor/m_temp_misc/tempflush
vendor/init/hw/init.sensor_1_0.rc:176 | post-fs-data | chown system system /sys/class/sensor/m_temp_misc/tempenablenodata
vendor/init/hw/init.sensor_1_0.rc:177 | post-fs-data | chown system system /sys/class/sensor/m_temp_misc/tempactive
vendor/init/hw/init.sensor_1_0.rc:178 | post-fs-data | chown system system /sys/class/sensor/m_temp_misc/tempdelay
vendor/init/hw/init.sensor_1_0.rc:179 | post-fs-data | chown system system /sys/class/sensor/m_temp_misc/tempbatch
vendor/init/hw/init.sensor_1_0.rc:180 | post-fs-data | chown system system /sys/class/sensor/m_temp_misc/tempflush
vendor/init/hw/init.sensor_1_0.rc:182 | post-fs-data | chmod 0660 /sys/class/sensor/m_alti_misc/altienablenodata
vendor/init/hw/init.sensor_1_0.rc:183 | post-fs-data | chmod 0660 /sys/class/sensor/m_alti_misc/altiactive
vendor/init/hw/init.sensor_1_0.rc:184 | post-fs-data | chmod 0660 /sys/class/sensor/m_alti_misc/altidelay
vendor/init/hw/init.sensor_1_0.rc:185 | post-fs-data | chmod 0660 /sys/class/sensor/m_alti_misc/altibatch
vendor/init/hw/init.sensor_1_0.rc:186 | post-fs-data | chmod 0660 /sys/class/sensor/m_alti_misc/altiflush
vendor/init/hw/init.sensor_1_0.rc:187 | post-fs-data | chown system system /sys/class/sensor/m_alti_misc/altienablenodata
vendor/init/hw/init.sensor_1_0.rc:188 | post-fs-data | chown system system /sys/class/sensor/m_alti_misc/altiactive
vendor/init/hw/init.sensor_1_0.rc:189 | post-fs-data | chown system system /sys/class/sensor/m_alti_misc/altidelay
vendor/init/hw/init.sensor_1_0.rc:190 | post-fs-data | chown system system /sys/class/sensor/m_alti_misc/altibatch
vendor/init/hw/init.sensor_1_0.rc:191 | post-fs-data | chown system system /sys/class/sensor/m_alti_misc/altiflush
vendor/init/hw/init.sensor_1_0.rc:193 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_enablenodata
vendor/init/hw/init.sensor_1_0.rc:194 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_active
vendor/init/hw/init.sensor_1_0.rc:195 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_delay
vendor/init/hw/init.sensor_1_0.rc:196 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_batch
vendor/init/hw/init.sensor_1_0.rc:197 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_flush
vendor/init/hw/init.sensor_1_0.rc:198 | post-fs-data | chmod 0660 /sys/class/sensor/m_ebus_misc/ebus_devnum
vendor/init/hw/init.sensor_1_0.rc:199 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_enablenodata
vendor/init/hw/init.sensor_1_0.rc:200 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_active
vendor/init/hw/init.sensor_1_0.rc:201 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_delay
vendor/init/hw/init.sensor_1_0.rc:202 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_batch
vendor/init/hw/init.sensor_1_0.rc:203 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_flush
vendor/init/hw/init.sensor_1_0.rc:204 | post-fs-data | chown system system /sys/class/sensor/m_ebus_misc/ebus_devnum
vendor/init/hw/init.sensor_1_0.rc:206 | post-fs-data | chmod 0660 /sys/class/sensor/m_baro_misc/baroenablenodata
vendor/init/hw/init.sensor_1_0.rc:207 | post-fs-data | chmod 0660 /sys/class/sensor/m_baro_misc/baroactive
vendor/init/hw/init.sensor_1_0.rc:208 | post-fs-data | chmod 0660 /sys/class/sensor/m_baro_misc/barodelay
vendor/init/hw/init.sensor_1_0.rc:209 | post-fs-data | chmod 0660 /sys/class/sensor/m_baro_misc/barobatch
vendor/init/hw/init.sensor_1_0.rc:210 | post-fs-data | chmod 0660 /sys/class/sensor/m_baro_misc/baroflush
vendor/init/hw/init.sensor_1_0.rc:211 | post-fs-data | chown system system /sys/class/sensor/m_baro_misc/baroenablenodata
vendor/init/hw/init.sensor_1_0.rc:212 | post-fs-data | chown system system /sys/class/sensor/m_baro_misc/baroactive
vendor/init/hw/init.sensor_1_0.rc:213 | post-fs-data | chown system system /sys/class/sensor/m_baro_misc/barodelay
vendor/init/hw/init.sensor_1_0.rc:214 | post-fs-data | chown system system /sys/class/sensor/m_baro_misc/barobatch
vendor/init/hw/init.sensor_1_0.rc:215 | post-fs-data | chown system system /sys/class/sensor/m_baro_misc/baroflush
vendor/init/hw/init.sensor_1_0.rc:217 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/i32kP
vendor/init/hw/init.sensor_1_0.rc:218 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/temperature
vendor/init/hw/init.sensor_1_0.rc:219 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/pressure
vendor/init/hw/init.sensor_1_0.rc:220 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/i32kT
vendor/init/hw/init.sensor_1_0.rc:221 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/pressure
vendor/init/hw/init.sensor_1_0.rc:222 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/temperature
vendor/init/hw/init.sensor_1_0.rc:223 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_T_gain
vendor/init/hw/init.sensor_1_0.rc:224 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_T_dVbe
vendor/init/hw/init.sensor_1_0.rc:225 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_T_Vbe
vendor/init/hw/init.sensor_1_0.rc:226 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c0
vendor/init/hw/init.sensor_1_0.rc:227 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c00
vendor/init/hw/init.sensor_1_0.rc:228 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c1
vendor/init/hw/init.sensor_1_0.rc:229 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c01
vendor/init/hw/init.sensor_1_0.rc:230 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c02
vendor/init/hw/init.sensor_1_0.rc:231 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c10
vendor/init/hw/init.sensor_1_0.rc:232 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c11
vendor/init/hw/init.sensor_1_0.rc:233 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c12
vendor/init/hw/init.sensor_1_0.rc:234 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c20
vendor/init/hw/init.sensor_1_0.rc:235 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c21
vendor/init/hw/init.sensor_1_0.rc:236 | post-fs-data | chmod 0660 /sys/bus/platform/drivers/barometer/calib_param_c30
vendor/init/hw/init.sensor_1_0.rc:237 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/i32kP
vendor/init/hw/init.sensor_1_0.rc:238 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/i32kT
vendor/init/hw/init.sensor_1_0.rc:239 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/pressure
vendor/init/hw/init.sensor_1_0.rc:240 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/temperature
vendor/init/hw/init.sensor_1_0.rc:241 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_T_gain
vendor/init/hw/init.sensor_1_0.rc:242 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_T_dVbe
vendor/init/hw/init.sensor_1_0.rc:243 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_T_Vbe
vendor/init/hw/init.sensor_1_0.rc:244 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c0
vendor/init/hw/init.sensor_1_0.rc:245 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c1
vendor/init/hw/init.sensor_1_0.rc:246 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c00
vendor/init/hw/init.sensor_1_0.rc:247 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c01
vendor/init/hw/init.sensor_1_0.rc:248 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c02
vendor/init/hw/init.sensor_1_0.rc:249 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c10
vendor/init/hw/init.sensor_1_0.rc:250 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c11
vendor/init/hw/init.sensor_1_0.rc:251 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c12
vendor/init/hw/init.sensor_1_0.rc:252 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c20
vendor/init/hw/init.sensor_1_0.rc:253 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c21
vendor/init/hw/init.sensor_1_0.rc:254 | post-fs-data | chown system system /sys/bus/platform/drivers/barometer/calib_param_c30
vendor/init/hw/init.sensor_1_0.rc:256 | post-fs-data | chmod 0660 /sys/class/sensor/m_hmdy_misc/hmdyenablenodata
vendor/init/hw/init.sensor_1_0.rc:257 | post-fs-data | chmod 0660 /sys/class/sensor/m_hmdy_misc/hmdyactive
vendor/init/hw/init.sensor_1_0.rc:258 | post-fs-data | chmod 0660 /sys/class/sensor/m_hmdy_misc/hmdydelay
vendor/init/hw/init.sensor_1_0.rc:259 | post-fs-data | chmod 0660 /sys/class/sensor/m_hmdy_misc/hmdybatch
vendor/init/hw/init.sensor_1_0.rc:260 | post-fs-data | chmod 0660 /sys/class/sensor/m_hmdy_misc/hmdyflush
vendor/init/hw/init.sensor_1_0.rc:262 | post-fs-data | chown system system /sys/class/sensor/m_hmdy_misc/hmdyenablenodata
vendor/init/hw/init.sensor_1_0.rc:263 | post-fs-data | chown system system /sys/class/sensor/m_hmdy_misc/hmdyactive
vendor/init/hw/init.sensor_1_0.rc:264 | post-fs-data | chown system system /sys/class/sensor/m_hmdy_misc/hmdydelay
vendor/init/hw/init.sensor_1_0.rc:265 | post-fs-data | chown system system /sys/class/sensor/m_hmdy_misc/hmdybatch
vendor/init/hw/init.sensor_1_0.rc:266 | post-fs-data | chown system system /sys/class/sensor/m_hmdy_misc/hmdyflush
vendor/init/hw/init.sensor_1_0.rc:268 | post-fs-data | chmod 0660 /sys/class/sensor/m_pedo_misc/pedoactive
vendor/init/hw/init.sensor_1_0.rc:269 | post-fs-data | chmod 0660 /sys/class/sensor/m_pedo_misc/pedodelay
vendor/init/hw/init.sensor_1_0.rc:270 | post-fs-data | chmod 0660 /sys/class/sensor/m_pedo_misc/pedobatch
vendor/init/hw/init.sensor_1_0.rc:271 | post-fs-data | chmod 0660 /sys/class/sensor/m_pedo_misc/pedoflush
vendor/init/hw/init.sensor_1_0.rc:272 | post-fs-data | chown system system /sys/class/sensor/m_pedo_misc/pedoactive
vendor/init/hw/init.sensor_1_0.rc:273 | post-fs-data | chown system system /sys/class/sensor/m_pedo_misc/pedodelay
vendor/init/hw/init.sensor_1_0.rc:274 | post-fs-data | chown system system /sys/class/sensor/m_pedo_misc/pedobatch
vendor/init/hw/init.sensor_1_0.rc:275 | post-fs-data | chown system system /sys/class/sensor/m_pedo_misc/pedoflush
vendor/init/hw/init.sensor_1_0.rc:277 | post-fs-data | chmod 0660 /sys/class/sensor/m_situ_misc/situactive
vendor/init/hw/init.sensor_1_0.rc:278 | post-fs-data | chmod 0660 /sys/class/sensor/m_situ_misc/situdelay
vendor/init/hw/init.sensor_1_0.rc:279 | post-fs-data | chmod 0660 /sys/class/sensor/m_situ_misc/situbatch
vendor/init/hw/init.sensor_1_0.rc:280 | post-fs-data | chmod 0660 /sys/class/sensor/m_situ_misc/situflush
vendor/init/hw/init.sensor_1_0.rc:281 | post-fs-data | chown system system /sys/class/sensor/m_situ_misc/situactive
vendor/init/hw/init.sensor_1_0.rc:282 | post-fs-data | chown system system /sys/class/sensor/m_situ_misc/situdelay
vendor/init/hw/init.sensor_1_0.rc:283 | post-fs-data | chown system system /sys/class/sensor/m_situ_misc/situbatch
vendor/init/hw/init.sensor_1_0.rc:284 | post-fs-data | chown system system /sys/class/sensor/m_situ_misc/situflush
vendor/init/hw/init.sensor_1_0.rc:286 | post-fs-data | chmod 0660 /sys/class/sensor/m_act_misc/actactive
vendor/init/hw/init.sensor_1_0.rc:287 | post-fs-data | chmod 0660 /sys/class/sensor/m_act_misc/actdelay
vendor/init/hw/init.sensor_1_0.rc:288 | post-fs-data | chmod 0660 /sys/class/sensor/m_act_misc/actbatch
vendor/init/hw/init.sensor_1_0.rc:289 | post-fs-data | chmod 0660 /sys/class/sensor/m_act_misc/actflush
vendor/init/hw/init.sensor_1_0.rc:290 | post-fs-data | chown system system /sys/class/sensor/m_act_misc/actactive
vendor/init/hw/init.sensor_1_0.rc:291 | post-fs-data | chown system system /sys/class/sensor/m_act_misc/actdelay
vendor/init/hw/init.sensor_1_0.rc:292 | post-fs-data | chown system system /sys/class/sensor/m_act_misc/actbatch
vendor/init/hw/init.sensor_1_0.rc:293 | post-fs-data | chown system system /sys/class/sensor/m_act_misc/actflush
vendor/init/hw/init.sensor_1_0.rc:295 | post-fs-data | chmod 0660 /sys/class/sensor/m_step_c_misc/step_cactive
vendor/init/hw/init.sensor_1_0.rc:296 | post-fs-data | chmod 0660 /sys/class/sensor/m_step_c_misc/step_cdelay
vendor/init/hw/init.sensor_1_0.rc:297 | post-fs-data | chmod 0660 /sys/class/sensor/m_step_c_misc/step_cbatch
vendor/init/hw/init.sensor_1_0.rc:298 | post-fs-data | chmod 0660 /sys/class/sensor/m_step_c_misc/step_cflush
vendor/init/hw/init.sensor_1_0.rc:299 | post-fs-data | chown system system /sys/class/sensor/m_step_c_misc/step_cactive
vendor/init/hw/init.sensor_1_0.rc:300 | post-fs-data | chown system system /sys/class/sensor/m_step_c_misc/step_cdelay
vendor/init/hw/init.sensor_1_0.rc:301 | post-fs-data | chown system system /sys/class/sensor/m_step_c_misc/step_cbatch
vendor/init/hw/init.sensor_1_0.rc:302 | post-fs-data | chown system system /sys/class/sensor/m_step_c_misc/step_cflush
vendor/init/hw/init.sensor_1_0.rc:304 | post-fs-data | chmod 0660 /sys/class/sensor/m_fusion_misc/fusionactive
vendor/init/hw/init.sensor_1_0.rc:305 | post-fs-data | chmod 0660 /sys/class/sensor/m_fusion_misc/fusiondelay
vendor/init/hw/init.sensor_1_0.rc:306 | post-fs-data | chmod 0660 /sys/class/sensor/m_fusion_misc/fusionbatch
vendor/init/hw/init.sensor_1_0.rc:307 | post-fs-data | chmod 0660 /sys/class/sensor/m_fusion_misc/fusionflush
vendor/init/hw/init.sensor_1_0.rc:308 | post-fs-data | chown system system /sys/class/sensor/m_fusion_misc/fusionactive
vendor/init/hw/init.sensor_1_0.rc:309 | post-fs-data | chown system system /sys/class/sensor/m_fusion_misc/fusiondelay
vendor/init/hw/init.sensor_1_0.rc:310 | post-fs-data | chown system system /sys/class/sensor/m_fusion_misc/fusionbatch
vendor/init/hw/init.sensor_1_0.rc:311 | post-fs-data | chown system system /sys/class/sensor/m_fusion_misc/fusionflush
vendor/init/hw/init.sensor_1_0.rc:313 | post-fs-data | chmod 0660 /sys/class/sensor/m_bio_misc/bioactive
vendor/init/hw/init.sensor_1_0.rc:314 | post-fs-data | chmod 0660 /sys/class/sensor/m_bio_misc/biodelay
vendor/init/hw/init.sensor_1_0.rc:315 | post-fs-data | chmod 0660 /sys/class/sensor/m_bio_misc/biobatch
vendor/init/hw/init.sensor_1_0.rc:316 | post-fs-data | chmod 0660 /sys/class/sensor/m_bio_misc/bioflush
vendor/init/hw/init.sensor_1_0.rc:317 | post-fs-data | chown system system /sys/class/sensor/m_bio_misc/bioactive
vendor/init/hw/init.sensor_1_0.rc:318 | post-fs-data | chown system system /sys/class/sensor/m_bio_misc/biodelay
vendor/init/hw/init.sensor_1_0.rc:319 | post-fs-data | chown system system /sys/class/sensor/m_bio_misc/biobatch
vendor/init/hw/init.sensor_1_0.rc:320 | post-fs-data | chown system system /sys/class/sensor/m_bio_misc/bioflush
vendor/init/hw/meta_init.connectivity.rc:8 | post-fs-data | chmod 0660 /dev/ttyMT2
vendor/init/hw/meta_init.connectivity.rc:9 | post-fs-data | chown system system /dev/ttyMT2
vendor/init/hw/meta_init.connectivity.rc:12 | post-fs-data | mkdir /data/vendor/gps 0770 gps system
vendor/init/hw/meta_init.connectivity.rc:15 | post-fs-data | chmod 666 /dev/gps_emi
vendor/init/hw/meta_init.connectivity.rc:64 | boot | insmod /vendor/lib/modules/wmt_drv.ko
vendor/init/hw/meta_init.connectivity.rc:68 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/bt_drv.ko
vendor/init/hw/meta_init.connectivity.rc:72 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wmt_chrdev_wifi.ko
vendor/init/hw/meta_init.connectivity.rc:73 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wlan_drv_${ro.vendor.wlan.gen}.ko
vendor/init/hw/meta_init.connectivity.rc:74 | property:vendor.connsys.driver.ready=yes | start wlan_assistant
vendor/init/hw/meta_init.connectivity.rc:78 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/fmradio_drv.ko
vendor/init/hw/meta_init.connectivity.rc:82 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/gps_drv.ko
vendor/init/hw/meta_init.modem.rc:9 | post-fs-data | write /proc/bootprof "post-fs-data: on meta modem start"
vendor/init/hw/meta_init.modem.rc:12 | post-fs-data | mkdir /mnt/vendor/nvdata/md  0770 root system
vendor/init/hw/meta_init.modem.rc:13 | post-fs-data | mkdir /mnt/vendor/nvdata/md3  0770 root system
vendor/init/hw/meta_init.modem.rc:14 | post-fs-data | mkdir /data/md3	0770 root system
vendor/init/hw/meta_init.modem.rc:20 | post-fs-data | chown radio radio /sys/kernel/ccci/boot
vendor/init/hw/meta_init.modem.rc:23 | post-fs-data | mkdir /data/ccci_cfg 0770 system radio
vendor/init/hw/meta_init.modem.rc:24 | post-fs-data | restorecon /data/ccci_cfg
vendor/init/hw/meta_init.modem.rc:25 | post-fs-data | restorecon_recursive /mnt/vendor/protect_f
vendor/init/hw/meta_init.modem.rc:26 | post-fs-data | restorecon_recursive /mnt/vendor/protect_s
vendor/init/hw/meta_init.modem.rc:29 | post-fs-data | setprop vold.post_fs_data_done 1
vendor/init/hw/meta_init.project.rc:5 | post-fs | chmod 0660 /dev/spm
vendor/init/hw/meta_init.project.rc:6 | post-fs | chown system system /dev/spm
vendor/init/hw/meta_init.rc:30 | early-init | write /proc/bootprof "INIT:early-init"
vendor/init/hw/meta_init.rc:33 | early-init | write /proc/1/oom_score_adj -1000
vendor/init/hw/meta_init.rc:36 | early-init | write /proc/sys/kernel/sysrq 0
vendor/init/hw/meta_init.rc:39 | early-init | restorecon /adb_keys
vendor/init/hw/meta_init.rc:42 | early-init | restorecon /postinstall
vendor/init/hw/meta_init.rc:44 | early-init | start ueventd
vendor/init/hw/meta_init.rc:49 | early-init | exec_start apexd-bootstrap
vendor/init/hw/meta_init.rc:52 | early-init | mount debugfs debugfs /sys/kernel/debug
vendor/init/hw/meta_init.rc:53 | early-init | chmod 0755 /sys/kernel/debug
vendor/init/hw/meta_init.rc:57 | init | write /proc/bootprof "INIT:init"
vendor/init/hw/meta_init.rc:59 | init | sysclktz 0
vendor/init/hw/meta_init.rc:61 | init | start servicemanager
vendor/init/hw/meta_init.rc:62 | init | start hwservicemanager
vendor/init/hw/meta_init.rc:63 | init | start hidl_memory
vendor/init/hw/meta_init.rc:66 | init | copy /proc/cmdline /dev/urandom
vendor/init/hw/meta_init.rc:67 | init | copy /default.prop /dev/urandom
vendor/init/hw/meta_init.rc:69 | init | write /proc/bootprof "INIT: on init start"
vendor/init/hw/meta_init.rc:72 | init | symlink /system/etc /etc
vendor/init/hw/meta_init.rc:73 | init | symlink /sys/kernel/debug /d
vendor/init/hw/meta_init.rc:76 | init | symlink /system/vendor /vendor
vendor/init/hw/meta_init.rc:79 | init | symlink /dev/block/platform/bootdevice/by-name/boot /dev/bootimg
vendor/init/hw/meta_init.rc:80 | init | symlink /dev/block/platform/bootdevice/by-name/recovery /dev/recovery
vendor/init/hw/meta_init.rc:81 | init | symlink /dev/block/platform/bootdevice/by-name/secro  /dev/sec_ro
vendor/init/hw/meta_init.rc:82 | init | symlink /dev/block/platform/bootdevice/by-name/kb /dev/kb
vendor/init/hw/meta_init.rc:83 | init | symlink /dev/block/platform/bootdevice/by-name/dkb /dev/dkb
vendor/init/hw/meta_init.rc:84 | init | symlink /dev/block/platform/bootdevice/by-name/seccfg /dev/seccfg
vendor/init/hw/meta_init.rc:85 | init | symlink /dev/block/platform/bootdevice/by-name/proinfo /dev/pro_info
vendor/init/hw/meta_init.rc:86 | init | symlink /dev/block/platform/bootdevice/by-name/nvram /dev/nvram
vendor/init/hw/meta_init.rc:87 | init | symlink /dev/block/platform/bootdevice/by-name/para /dev/misc
vendor/init/hw/meta_init.rc:88 | init | symlink /dev/block/platform/bootdevice/by-name/logo /dev/logo
vendor/init/hw/meta_init.rc:91 | init | mount cgroup none /acct cpuacct
vendor/init/hw/meta_init.rc:92 | init | mkdir /acct/uid
vendor/init/hw/meta_init.rc:95 | init | mkdir /dev/stune
vendor/init/hw/meta_init.rc:96 | init | mount cgroup none /dev/stune schedtune
vendor/init/hw/meta_init.rc:97 | init | mkdir /dev/stune/foreground
vendor/init/hw/meta_init.rc:98 | init | mkdir /dev/stune/background
vendor/init/hw/meta_init.rc:99 | init | mkdir /dev/stune/top-app
vendor/init/hw/meta_init.rc:100 | init | chown system system /dev/stune
vendor/init/hw/meta_init.rc:101 | init | chown system system /dev/stune/foreground
vendor/init/hw/meta_init.rc:102 | init | chown system system /dev/stune/background
vendor/init/hw/meta_init.rc:103 | init | chown system system /dev/stune/top-app
vendor/init/hw/meta_init.rc:104 | init | chown system system /dev/stune/tasks
vendor/init/hw/meta_init.rc:105 | init | chown system system /dev/stune/foreground/tasks
vendor/init/hw/meta_init.rc:106 | init | chown system system /dev/stune/background/tasks
vendor/init/hw/meta_init.rc:107 | init | chown system system /dev/stune/top-app/tasks
vendor/init/hw/meta_init.rc:108 | init | chmod 0664 /dev/stune/tasks
vendor/init/hw/meta_init.rc:109 | init | chmod 0664 /dev/stune/foreground/tasks
vendor/init/hw/meta_init.rc:110 | init | chmod 0664 /dev/stune/background/tasks
vendor/init/hw/meta_init.rc:111 | init | chmod 0664 /dev/stune/top-app/tasks
vendor/init/hw/meta_init.rc:113 | init | restorecon_recursive /mnt
vendor/init/hw/meta_init.rc:115 | init | symlink /sdcard /mnt/sdcard
vendor/init/hw/meta_init.rc:117 | init | mount configfs none /config
vendor/init/hw/meta_init.rc:118 | init | chmod 0775 /config/sdcardfs
vendor/init/hw/meta_init.rc:119 | init | chown system package_info /config/sdcardfs
vendor/init/hw/meta_init.rc:121 | init | mkdir /mnt/secure 0700 root root
vendor/init/hw/meta_init.rc:122 | init | mkdir /mnt/secure/asec  0700 root root
vendor/init/hw/meta_init.rc:123 | init | mkdir /mnt/asec 0755 root system
vendor/init/hw/meta_init.rc:124 | init | mkdir /mnt/obb 0755 root system
vendor/init/hw/meta_init.rc:125 | init | mkdir /mnt/media_rw 0750 root media_rw
vendor/init/hw/meta_init.rc:126 | init | mkdir /mnt/user 0755 root root
vendor/init/hw/meta_init.rc:127 | init | mkdir /mnt/user/0 0755 root root
vendor/init/hw/meta_init.rc:128 | init | mkdir /mnt/expand 0771 system system
vendor/init/hw/meta_init.rc:129 | init | mkdir /mnt/appfuse 0711 root root
vendor/init/hw/meta_init.rc:132 | init | chown system system /sys/power/autosleep
vendor/init/hw/meta_init.rc:133 | init | chown system system /sys/power/state
vendor/init/hw/meta_init.rc:134 | init | chown system system /sys/power/wakeup_count
vendor/init/hw/meta_init.rc:135 | init | chown radio wakelock /sys/power/wake_lock
vendor/init/hw/meta_init.rc:136 | init | chown radio wakelock /sys/power/wake_unlock
vendor/init/hw/meta_init.rc:137 | init | chmod 0660 /sys/power/state
vendor/init/hw/meta_init.rc:138 | init | chmod 0660 /sys/power/wake_lock
vendor/init/hw/meta_init.rc:139 | init | chmod 0660 /sys/power/wake_unlock
vendor/init/hw/meta_init.rc:142 | init | mkdir /mnt/runtime 0700 root root
vendor/init/hw/meta_init.rc:143 | init | mkdir /mnt/runtime/default 0755 root root
vendor/init/hw/meta_init.rc:144 | init | mkdir /mnt/runtime/default/self 0755 root root
vendor/init/hw/meta_init.rc:145 | init | mkdir /mnt/runtime/read 0755 root root
vendor/init/hw/meta_init.rc:146 | init | mkdir /mnt/runtime/read/self 0755 root root
vendor/init/hw/meta_init.rc:147 | init | mkdir /mnt/runtime/write 0755 root root
vendor/init/hw/meta_init.rc:148 | init | mkdir /mnt/runtime/write/self 0755 root root
vendor/init/hw/meta_init.rc:151 | init | symlink /storage/self/primary /sdcard
vendor/init/hw/meta_init.rc:152 | init | symlink /storage/self/primary /mnt/sdcard
vendor/init/hw/meta_init.rc:153 | init | symlink /mnt/user/0/primary /mnt/runtime/default/self/primary
vendor/init/hw/meta_init.rc:156 | init | mkdir /dev/memcg 0700 root system
vendor/init/hw/meta_init.rc:157 | init | mount cgroup none /dev/memcg memory
vendor/init/hw/meta_init.rc:159 | init | mkdir /dev/memcg/apps/ 0755 system system
vendor/init/hw/meta_init.rc:161 | init | write /proc/sys/kernel/panic_on_oops 1
vendor/init/hw/meta_init.rc:162 | init | write /proc/sys/kernel/hung_task_timeout_secs 0
vendor/init/hw/meta_init.rc:163 | init | write /proc/cpu/alignment 4
vendor/init/hw/meta_init.rc:169 | init | write /proc/sys/kernel/sched_tunable_scaling 0
vendor/init/hw/meta_init.rc:170 | init | write /proc/sys/kernel/sched_latency_ns 10000000
vendor/init/hw/meta_init.rc:171 | init | write /proc/sys/kernel/sched_wakeup_granularity_ns 2000000
vendor/init/hw/meta_init.rc:172 | init | write /proc/sys/kernel/sched_child_runs_first 0
vendor/init/hw/meta_init.rc:173 | init | write /proc/sys/kernel/randomize_va_space 2
vendor/init/hw/meta_init.rc:174 | init | write /proc/sys/vm/mmap_min_addr 32768
vendor/init/hw/meta_init.rc:175 | init | write /proc/sys/net/ipv4/ping_group_range "0 2147483647"
vendor/init/hw/meta_init.rc:176 | init | write /proc/sys/net/unix/max_dgram_qlen 600
vendor/init/hw/meta_init.rc:177 | init | write /proc/sys/kernel/sched_rt_runtime_us 950000
vendor/init/hw/meta_init.rc:178 | init | write /proc/sys/kernel/sched_rt_period_us 1000000
vendor/init/hw/meta_init.rc:182 | init | write /proc/sys/net/core/rmem_max  262144
vendor/init/hw/meta_init.rc:183 | init | write /proc/sys/net/core/wmem_max  262144
vendor/init/hw/meta_init.rc:185 | init | write /proc/sys/net/ipv4/fwmark_reflect 1
vendor/init/hw/meta_init.rc:186 | init | write /proc/sys/net/ipv6/fwmark_reflect 1
vendor/init/hw/meta_init.rc:188 | init | write /proc/sys/net/ipv4/tcp_fwmark_accept 1
vendor/init/hw/meta_init.rc:190 | init | write /proc/sys/net/ipv4/conf/all/accept_redirects 0
vendor/init/hw/meta_init.rc:191 | init | write /proc/sys/net/ipv6/conf/all/accept_redirects 0
vendor/init/hw/meta_init.rc:194 | init | mkdir /mnt/vendor/protect_f 0771 system system
vendor/init/hw/meta_init.rc:195 | init | mkdir /mnt/vendor/protect_s 0771 system system
vendor/init/hw/meta_init.rc:196 | init | symlink /sdcard /mnt/sdcard
vendor/init/hw/meta_init.rc:198 | init | mkdir /mnt/vendor/nvdata 0771 system system
vendor/init/hw/meta_init.rc:199 | init | mkdir /mnt/vendor/nvcfg 0771 system system
vendor/init/hw/meta_init.rc:202 | init | mkdir /custom
vendor/init/hw/meta_init.rc:204 | init | mkdir /mnt/cd-rom 0000 system system
vendor/init/hw/meta_init.rc:208 | init | write /proc/sys/abi/swp 1
vendor/init/hw/meta_init.rc:211 | init | symlink /proc/self/fd /dev/fd
vendor/init/hw/meta_init.rc:213 | init | setrlimit 13 40 40
vendor/init/hw/meta_init.rc:217 | property:sys.boot_from_charger_mode=1 | class_stop charger
vendor/init/hw/meta_init.rc:218 | property:sys.boot_from_charger_mode=1 | trigger late-init
vendor/init/hw/meta_init.rc:222 | load_system_props_action | load_system_props
vendor/init/hw/meta_init.rc:225 | load_persist_props_action | load_persist_props
vendor/init/hw/meta_init.rc:226 | load_persist_props_action | start logd
vendor/init/hw/meta_init.rc:227 | load_persist_props_action | start logd-reinit
vendor/init/hw/meta_init.rc:230 | load_persist_props_action | stop keyinstall-1-0
vendor/init/hw/meta_init.rc:231 | load_persist_props_action | start keyinstall-1-0
vendor/init/hw/meta_init.rc:235 | firmware_mounts_complete | rm /dev/.booting
vendor/init/hw/meta_init.rc:239 | late-init | write /proc/bootprof "INIT:late-init"
vendor/init/hw/meta_init.rc:241 | late-init | trigger early-fs
vendor/init/hw/meta_init.rc:247 | late-init | trigger fs
vendor/init/hw/meta_init.rc:248 | late-init | trigger post-fs
vendor/init/hw/meta_init.rc:253 | late-init | trigger load_system_props_action
vendor/init/hw/meta_init.rc:260 | late-init | trigger late-fs
vendor/init/hw/meta_init.rc:264 | late-init | trigger post-fs-data
vendor/init/hw/meta_init.rc:267 | late-init | trigger load_persist_props_action
vendor/init/hw/meta_init.rc:270 | late-init | trigger firmware_mounts_complete
vendor/init/hw/meta_init.rc:272 | late-init | trigger early-boot
vendor/init/hw/meta_init.rc:273 | late-init | trigger boot
vendor/init/hw/meta_init.rc:277 | early-fs | start vold
vendor/init/hw/meta_init.rc:281 | fs | mkdir /dev/usb-ffs 0770 shell shell
vendor/init/hw/meta_init.rc:282 | fs | mkdir /dev/usb-ffs/adb 0770 shell shell
vendor/init/hw/meta_init.rc:283 | fs | mkdir /config/usb_gadget/g1 0770 shell shell
vendor/init/hw/meta_init.rc:284 | fs | write /config/usb_gadget/g1/idVendor 0x0E8D
vendor/init/hw/meta_init.rc:285 | fs | write /config/usb_gadget/g1/bcdDevice 0xFFFF
vendor/init/hw/meta_init.rc:286 | fs | write /config/usb_gadget/g1/bcdUSB 0x0200
vendor/init/hw/meta_init.rc:287 | fs | mkdir /config/usb_gadget/g1/strings/0x409 0770
vendor/init/hw/meta_init.rc:288 | fs | write /config/usb_gadget/g1/strings/0x409/manufacturer "Android"
vendor/init/hw/meta_init.rc:289 | fs | write /config/usb_gadget/g1/strings/0x409/product "Android"
vendor/init/hw/meta_init.rc:290 | fs | write /config/usb_gadget/g1/bDeviceClass 0
vendor/init/hw/meta_init.rc:291 | fs | write /config/usb_gadget/g1/bDeviceSubClass 0
vendor/init/hw/meta_init.rc:292 | fs | write /config/usb_gadget/g1/bDeviceProtocol 0
vendor/init/hw/meta_init.rc:293 | fs | mkdir /config/usb_gadget/g1/functions/mass_storage.usb0
vendor/init/hw/meta_init.rc:294 | fs | mkdir /config/usb_gadget/g1/functions/ffs.adb
vendor/init/hw/meta_init.rc:295 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs0
vendor/init/hw/meta_init.rc:296 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs1
vendor/init/hw/meta_init.rc:297 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs2
vendor/init/hw/meta_init.rc:298 | fs | mkdir /config/usb_gadget/g1/functions/acm.gs3
vendor/init/hw/meta_init.rc:299 | fs | mkdir /config/usb_gadget/g1/functions/gser.gs0
vendor/init/hw/meta_init.rc:300 | fs | mkdir /config/usb_gadget/g1/configs/b.1 0770 shell shell
vendor/init/hw/meta_init.rc:301 | fs | mkdir /config/usb_gadget/g1/configs/b.1/strings/0x409 0770 shell shell
vendor/init/hw/meta_init.rc:302 | fs | write /config/usb_gadget/g1/configs/b.1/MaxPower 500
vendor/init/hw/meta_init.rc:303 | fs | symlink /config/usb_gadget/g1/configs/b.1 /config/usb_gadget/g1/os_desc/b.1
vendor/init/hw/meta_init.rc:304 | fs | setprop vendor.usb.feature 0
vendor/init/hw/meta_init.rc:306 | fs | write /proc/bootprof "INIT:fs"
vendor/init/hw/meta_init.rc:308 | fs | write /proc/bootprof "INIT:Mount_START"
vendor/init/hw/meta_init.rc:309 | fs | wait_for_prop hwservicemanager.ready "true"
vendor/init/hw/meta_init.rc:310 | fs | start vendor.boot-hal-1-0
vendor/init/hw/meta_init.rc:311 | fs | mount_all /vendor/etc/fstab.mt6765 ${ro.vendor.rc}multi_init.rc
vendor/init/hw/meta_init.rc:312 | fs | write /proc/bootprof "INIT:Mount_END"
vendor/init/hw/meta_init.rc:316 | post-fs | write /proc/bootprof "INIT:post-fs"
vendor/init/hw/meta_init.rc:317 | post-fs | start logd
vendor/init/hw/meta_init.rc:318 | post-fs | start keyinstall-1-0
vendor/init/hw/meta_init.rc:319 | post-fs | start nfc_hal_service
vendor/init/hw/meta_init.rc:320 | post-fs | start light-hal-2-0
vendor/init/hw/meta_init.rc:321 | post-fs | start merged_hal_service
vendor/init/hw/meta_init.rc:323 | post-fs | mount rootfs rootfs / ro remount
vendor/init/hw/meta_init.rc:325 | post-fs | mount rootfs rootfs / shared rec
vendor/init/hw/meta_init.rc:327 | post-fs | mount none /mnt/runtime/default /storage bind rec
vendor/init/hw/meta_init.rc:328 | post-fs | mount none none /storage slave rec
vendor/init/hw/meta_init.rc:332 | post-fs | restorecon --recursive --cross-filesystems /sys/kernel/debug
vendor/init/hw/meta_init.rc:333 | post-fs | chmod 0755 /sys/kernel/debug/tracing
vendor/init/hw/meta_init.rc:336 | post-fs | symlink /sdcard /storage/sdcard0
vendor/init/hw/meta_init.rc:339 | post-fs | chown system cache /cache
vendor/init/hw/meta_init.rc:340 | post-fs | chmod 0770 /cache
vendor/init/hw/meta_init.rc:342 | post-fs | restorecon_recursive /cache
vendor/init/hw/meta_init.rc:345 | post-fs | chown root system /proc/kmsg
vendor/init/hw/meta_init.rc:346 | post-fs | chmod 0440 /proc/kmsg
vendor/init/hw/meta_init.rc:347 | post-fs | chown root system /proc/sysrq-trigger
vendor/init/hw/meta_init.rc:348 | post-fs | chmod 0220 /proc/sysrq-trigger
vendor/init/hw/meta_init.rc:349 | post-fs | chown system log /proc/last_kmsg
vendor/init/hw/meta_init.rc:350 | post-fs | chmod 0440 /proc/last_kmsg
vendor/init/hw/meta_init.rc:353 | post-fs | chmod 0444 /sys/fs/selinux/policy
vendor/init/hw/meta_init.rc:356 | post-fs | mkdir /cache/lost+found 0770 root root
vendor/init/hw/meta_init.rc:359 | post-fs | chown system system /mnt/vendor/protect_f
vendor/init/hw/meta_init.rc:360 | post-fs | chmod 0770 /mnt/vendor/protect_f
vendor/init/hw/meta_init.rc:362 | post-fs | chown system system /mnt/vendor/protect_s
vendor/init/hw/meta_init.rc:363 | post-fs | chmod 0770 /mnt/vendor/protect_s
vendor/init/hw/meta_init.rc:365 | post-fs | chown system system /mnt/vendor/nvcfg
vendor/init/hw/meta_init.rc:366 | post-fs | chmod 0771 /mnt/vendor/nvcfg
vendor/init/hw/meta_init.rc:367 | post-fs | restorecon_recursive /mnt/vendor/nvcfg
vendor/init/hw/meta_init.rc:369 | post-fs | chown root system /mnt/vendor/nvdata
vendor/init/hw/meta_init.rc:370 | post-fs | chmod 0771 /mnt/vendor/nvdata
vendor/init/hw/meta_init.rc:371 | post-fs | mkdir /mnt/vendor/nvdata/media 0771 media audio
vendor/init/hw/meta_init.rc:372 | post-fs | restorecon_recursive /mnt/vendor/nvdata
vendor/init/hw/meta_init.rc:379 | late-fs | class_start early_hal
vendor/init/hw/meta_init.rc:381 | late-fs | start vendor.keymaster-3-0
vendor/init/hw/meta_init.rc:382 | late-fs | start vendor.keymaster-3-0-trustonic
vendor/init/hw/meta_init.rc:383 | late-fs | start vendor.keymaster-4-0
vendor/init/hw/meta_init.rc:384 | late-fs | start vendor.keymaster-4-0-trustonic
vendor/init/hw/meta_init.rc:385 | late-fs | start vendor.keymaster-4-0-beanpod
vendor/init/hw/meta_init.rc:386 | late-fs | start vendor.keymaster-4-0-trustkernel
vendor/init/hw/meta_init.rc:387 | late-fs | start keymaster_attestation-1-1
vendor/init/hw/meta_init.rc:390 | post-fs-data | write /proc/bootprof "INIT:post-fs-data"
vendor/init/hw/meta_init.rc:393 | post-fs-data | start debuggerd
vendor/init/hw/meta_init.rc:394 | post-fs-data | start debuggerd64
vendor/init/hw/meta_init.rc:398 | post-fs-data | mkdir /data/misc/wifi 0770 system wifi
vendor/init/hw/meta_init.rc:399 | post-fs-data | mkdir /data/misc/wifi/sockets 0770 system wifi
vendor/init/hw/meta_init.rc:400 | post-fs-data | mkdir /data/misc/dhcp 0770 dhcp dhcp
vendor/init/hw/meta_init.rc:401 | post-fs-data | chown dhcp dhcp /data/misc/dhcp
vendor/init/hw/meta_init.rc:403 | post-fs-data | chmod 0660 /dev/rfkill
vendor/init/hw/meta_init.rc:406 | post-fs-data | mkdir /data/misc/rtc 0770 system system
vendor/init/hw/meta_init.rc:409 | post-fs-data | chmod 0660 /dev/sec
vendor/init/hw/meta_init.rc:410 | post-fs-data | chown root system /dev/sec
vendor/init/hw/meta_init.rc:413 | post-fs-data | exec /vendor/etc/partition_permission.sh
vendor/init/hw/meta_init.rc:415 | post-fs-data | chmod 0666 /dev/exm0
vendor/init/hw/meta_init.rc:422 | zygote-start && property:ro.crypto.state=unencrypted | exec_start update_verifier_nonencrypted
vendor/init/hw/meta_init.rc:423 | zygote-start && property:ro.crypto.state=unencrypted | start netd
vendor/init/hw/meta_init.rc:424 | zygote-start && property:ro.crypto.state=unencrypted | start zygote
vendor/init/hw/meta_init.rc:425 | zygote-start && property:ro.crypto.state=unencrypted | start zygote_secondary
vendor/init/hw/meta_init.rc:429 | zygote-start && property:ro.crypto.state=unsupported | exec_start update_verifier_nonencrypted
vendor/init/hw/meta_init.rc:430 | zygote-start && property:ro.crypto.state=unsupported | start netd
vendor/init/hw/meta_init.rc:431 | zygote-start && property:ro.crypto.state=unsupported | start zygote
vendor/init/hw/meta_init.rc:432 | zygote-start && property:ro.crypto.state=unsupported | start zygote_secondary
vendor/init/hw/meta_init.rc:436 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | exec_start update_verifier_nonencrypted
vendor/init/hw/meta_init.rc:437 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start netd
vendor/init/hw/meta_init.rc:438 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote
vendor/init/hw/meta_init.rc:439 | zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file | start zygote_secondary
vendor/init/hw/meta_init.rc:442 | boot | write /proc/bootprof "INIT:boot"
vendor/init/hw/meta_init.rc:445 | boot | chmod 0664 /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/meta_init.rc:446 | boot | chown system system /sys/class/leds/lcd-backlight/brightness
vendor/init/hw/meta_init.rc:449 | boot | ifup lo
vendor/init/hw/meta_init.rc:450 | boot | hostname localhost
vendor/init/hw/meta_init.rc:451 | boot | domainname localdomain
vendor/init/hw/meta_init.rc:454 | boot | class_start default
vendor/init/hw/meta_init.rc:458 | boot | class_start hal
vendor/init/hw/meta_init.rc:460 | boot | class_start core
vendor/init/hw/meta_init.rc:463 | boot | mount functionfs adb /dev/usb-ffs/adb uid=2000,gid=2000
vendor/init/hw/meta_init.rc:464 | boot | chown system radio /dev/ttyGS3
vendor/init/hw/meta_init.rc:465 | boot | chmod 0660 /dev/ttyGS3
vendor/init/hw/meta_init.rc:466 | boot | setprop sys.usb.configfs 1
vendor/init/hw/meta_init.rc:467 | boot | setprop sys.usb.ffs.aio_compat 1
vendor/init/hw/meta_init.rc:470 | nonencrypted | class_start main
vendor/init/hw/meta_init.rc:471 | nonencrypted | class_start late_start
vendor/init/hw/meta_init.rc:474 | property:sys.init_log_level=* | loglevel ${sys.init_log_level}
vendor/init/hw/meta_init.rc:477 | property:vold.decrypt=trigger_reset_main | class_reset main
vendor/init/hw/meta_init.rc:480 | property:vold.decrypt=trigger_load_persist_props | load_persist_props
vendor/init/hw/meta_init.rc:481 | property:vold.decrypt=trigger_load_persist_props | start logd
vendor/init/hw/meta_init.rc:482 | property:vold.decrypt=trigger_load_persist_props | start logd-reinit
vendor/init/hw/meta_init.rc:485 | property:vold.decrypt=trigger_load_persist_props | stop keyinstall-1-0
vendor/init/hw/meta_init.rc:486 | property:vold.decrypt=trigger_load_persist_props | start keyinstall-1-0
vendor/init/hw/meta_init.rc:489 | property:vold.decrypt=trigger_post_fs_data | trigger post-fs-data
vendor/init/hw/meta_init.rc:490 | property:vold.decrypt=trigger_post_fs_data | trigger zygote-start
vendor/init/hw/meta_init.rc:494 | property:vold.decrypt=trigger_restart_min_framework | exec_start update_verifier
vendor/init/hw/meta_init.rc:495 | property:vold.decrypt=trigger_restart_min_framework | class_start main
vendor/init/hw/meta_init.rc:498 | property:vold.decrypt=trigger_restart_framework | stop surfaceflinger
vendor/init/hw/meta_init.rc:499 | property:vold.decrypt=trigger_restart_framework | start surfaceflinger
vendor/init/hw/meta_init.rc:501 | property:vold.decrypt=trigger_restart_framework | exec_start update_verifier
vendor/init/hw/meta_init.rc:502 | property:vold.decrypt=trigger_restart_framework | class_start main
vendor/init/hw/meta_init.rc:503 | property:vold.decrypt=trigger_restart_framework | class_start late_start
vendor/init/hw/meta_init.rc:506 | property:vold.decrypt=trigger_shutdown_framework | class_reset late_start
vendor/init/hw/meta_init.rc:507 | property:vold.decrypt=trigger_shutdown_framework | class_reset main
vendor/init/hw/meta_init.rc:511 | defaultcrypto | exec - root -- /system/bin/vdc --wait cryptfs mountdefaultencrypted
vendor/init/hw/meta_init.rc:520 | encrypt && property:ro.vendor.mtk_f2fs_enable=0 | mount ext4 /dev/block/platform/bootdevice/by-name/userdata /data noatime nosuid nodev noauto_da_alloc discard wait
vendor/init/hw/meta_init.rc:523 | encrypt && property:ro.vendor.mtk_f2fs_enable=1 | mount f2fs /dev/block/platform/bootdevice/by-name/userdata /data noatime nosuid nodev discard wait
vendor/init/hw/meta_init.rc:526 | encrypt | setprop vendor.crypto.fake_encrypt 1
vendor/init/hw/meta_init.rc:527 | encrypt | setprop vold.post_fs_data_done 0
vendor/init/hw/meta_init.rc:528 | encrypt | trigger post-fs-data
vendor/init/hw/meta_init.rc:530 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | trigger load_persist_props_action
vendor/init/hw/meta_init.rc:532 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | setprop vendor.soter.teei.crypto.state unencrypted
vendor/init/hw/meta_init.rc:533 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | exec_start update_verifier
vendor/init/hw/meta_init.rc:534 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | class_start main
vendor/init/hw/meta_init.rc:535 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | class_start late_start
vendor/init/hw/meta_init.rc:536 | property:vendor.crypto.fake_encrypt=1 && property:vold.post_fs_data_done=1 | start mount_all_storages
vendor/init/hw/meta_init.rc:555 | property:ro.vendor.mtklog_internal=1 | setprop vendor.logmuch.value 10000
vendor/init/hw/meta_init.rc:560 | property:ro.debuggable=1 | chmod 0773 /data/misc/trace
vendor/init/hw/meta_init.rc:561 | property:ro.debuggable=1 | start console
vendor/init/hw/meta_init.rc:603 | property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | write /proc/bootprof "INIT:start adbd"
vendor/init/hw/meta_init.rc:604 | property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/meta_init.rc:608 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /proc/bootprof "INIT:on property:ro.boot.usbconfig=0"
vendor/init/hw/meta_init.rc:609 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/meta_init.rc:610 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno
vendor/init/hw/meta_init.rc:611 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "adb_acm"
vendor/init/hw/meta_init.rc:612 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /config/usb_gadget/g1/idProduct 0x200E
vendor/init/hw/meta_init.rc:613 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/meta_init.rc:614 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/meta_init.rc:615 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=0 && property:sys.usb.configfs=1 && \ | write /config/usb_gadget/g1/UDC "musb-hdrc"
vendor/init/hw/meta_init.rc:619 | property:ro.boot.usbconfig=1 && property:sys.usb.configfs=1 && property:vendor.usb.feature=0 | write /proc/bootprof "INIT:on property:ro.boot.usbconfig 1"
vendor/init/hw/meta_init.rc:630 | property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1 | write /proc/bootprof "INIT:on property:ro.boot.usbconfig 2"
vendor/init/hw/meta_init.rc:631 | property:ro.boot.usbconfig=2 && property:sys.usb.configfs=1 | setprop vendor.usb.acm_idx 1,4
vendor/init/hw/meta_init.rc:643 | property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /proc/bootprof "INIT:start adbd"
vendor/init/hw/meta_init.rc:644 | property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | start adbd
vendor/init/hw/meta_init.rc:647 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /proc/bootprof "INIT:on property:ro.boot.usbconfig 3"
vendor/init/hw/meta_init.rc:648 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "none"
vendor/init/hw/meta_init.rc:649 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/strings/0x409/serialnumber $ro.serialno
vendor/init/hw/meta_init.rc:650 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/configs/b.1/strings/0x409/configuration "meta_elt_adb"
vendor/init/hw/meta_init.rc:651 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/idProduct 0x2040
vendor/init/hw/meta_init.rc:652 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /sys/devices/platform/mt_usb/saving 1
vendor/init/hw/meta_init.rc:653 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/mass_storage.usb0 /config/usb_gadget/g1/configs/b.1/f1
vendor/init/hw/meta_init.rc:654 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/ffs.adb /config/usb_gadget/g1/configs/b.1/f2
vendor/init/hw/meta_init.rc:655 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/acm.gs0 /config/usb_gadget/g1/configs/b.1/f3
vendor/init/hw/meta_init.rc:656 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | symlink /config/usb_gadget/g1/functions/acm.gs3 /config/usb_gadget/g1/configs/b.1/f4
vendor/init/hw/meta_init.rc:657 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | write /config/usb_gadget/g1/UDC "musb-hdrc"
vendor/init/hw/meta_init.rc:658 | property:sys.usb.ffs.ready=1 && property:ro.boot.usbconfig=3 && property:sys.usb.configfs=1 | setprop vendor.usb.acm_idx 1,4
vendor/init/init.bip.rc:5 | property:vendor.volte_md_status=ready | start vendor.bip
vendor/init/init.bip.rc:8 | property:vendor.volte_md_status=reset | stop vendor.bip
vendor/init/init.bip.rc:11 | property:vendor.volte_md_status=stop | stop vendor.bip
vendor/init/init.bip.rc:14 | property:vendor.volte_md_status=flightmode | stop vendor.bip
vendor/init/init.bip.rc:17 | property:vendor.volte_md_status=exception | stop vendor.bip
vendor/init/init.bt_drv.rc:3 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/bt_drv.ko
vendor/init/init.cccifsd.rc:8 | post-fs-data | mkdir /data/vendor/ccci_cfg 0770 system radio
vendor/init/init.cccifsd.rc:9 | post-fs-data | restorecon /data/vendor/ccci_cfg
vendor/init/init.cccifsd.rc:10 | post-fs-data | mkdir /data/vendor/mdlpm 0770 system radio
vendor/init/init.cccifsd.rc:11 | post-fs-data | restorecon /data/vendor/mdlpm
vendor/init/init.cccifsd.rc:12 | post-fs-data | mkdir /data/vendor/md3 0770 system radio
vendor/init/init.cccifsd.rc:13 | post-fs-data | restorecon /data/vendor/md3
vendor/init/init.cccifsd.rc:15 | post-fs-data | restorecon_recursive /mnt/vendor/protect_f
vendor/init/init.cccifsd.rc:16 | post-fs-data | restorecon_recursive /mnt/vendor/protect_s
vendor/init/init.cccimdinit.rc:7 | post-fs-data | mkdir /mnt/vendor/nvdata/md  0770 root system
vendor/init/init.cccimdinit.rc:8 | post-fs-data | mkdir /mnt/vendor/nvdata/md3  0770 root system
vendor/init/init.cccimdinit.rc:9 | post-fs-data | mkdir /data/vendor/md3	0770 root system
vendor/init/init.cccimdinit.rc:15 | post-fs-data | chown radio radio /sys/kernel/ccci/boot
vendor/init/init.cccimdinit.rc:18 | post-fs-data | mkdir /data/vendor/ccci_cfg 0770 system radio
vendor/init/init.cccimdinit.rc:19 | post-fs-data | restorecon /data/vendor/ccci_cfg
vendor/init/init.gps_drv.rc:4 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/gps_drv.ko
vendor/init/init.md_apps.rc:2 | boot | setprop ro.vendor.md_apps.support 1
vendor/init/init.md_apps.rc:3 | boot | setprop ro.vendor.md_apps.load_type user
vendor/init/init.md_apps.rc:4 | boot | setprop ro.vendor.md_apps.load_verno "MOLY.LR12A.R3.MP.V107.4"
vendor/init/init.md_apps.rc:5 | boot | setprop ro.vendor.md_apps.load_gencfg "GEN93_USER"
vendor/init/init.md_apps.rc:6 | boot | setprop ro.vendor.md_apps.load_date "2021/01/20 17:32:43 GMT +08:00"
vendor/init/init.volte_imsm_93.rc:6 | property:vendor.volte_md_status=ready | restart volte_imsm_93
vendor/init/init.volte_imsm_93.rc:7 | property:vendor.volte_md_status=ready | restart vendor.volte_stack
vendor/init/init.volte_imsm_93.rc:8 | property:vendor.volte_md_status=ready | restart vendor.volte_ua
vendor/init/init.volte_imsm_93.rc:9 | property:vendor.volte_md_status=ready | restart vendor.volte_imcb
vendor/init/init.volte_imsm_93.rc:12 | property:vendor.volte_md_status=reset | rm /dev/radio/pttyims
vendor/init/init.volte_imsm_93.rc:13 | property:vendor.volte_md_status=reset | rm /dev/radio/ptty2ims
vendor/init/init.volte_imsm_93.rc:14 | property:vendor.volte_md_status=reset | rm /dev/radio/ptty3ims
vendor/init/init.volte_imsm_93.rc:15 | property:vendor.volte_md_status=reset | rm /dev/radio/ptty4ims
vendor/init/init.volte_imsm_93.rc:16 | property:vendor.volte_md_status=reset | stop vendor.volte_imcb
vendor/init/init.volte_imsm_93.rc:17 | property:vendor.volte_md_status=reset | stop vendor.volte_ua
vendor/init/init.volte_imsm_93.rc:18 | property:vendor.volte_md_status=reset | stop vendor.volte_stack
vendor/init/init.volte_imsm_93.rc:19 | property:vendor.volte_md_status=reset | stop volte_imsm_93
vendor/init/init.volte_imsm_93.rc:22 | property:vendor.volte_md_status=stop | rm /dev/radio/pttyims
vendor/init/init.volte_imsm_93.rc:23 | property:vendor.volte_md_status=stop | rm /dev/radio/ptty2ims
vendor/init/init.volte_imsm_93.rc:24 | property:vendor.volte_md_status=stop | rm /dev/radio/ptty3ims
vendor/init/init.volte_imsm_93.rc:25 | property:vendor.volte_md_status=stop | rm /dev/radio/ptty4ims
vendor/init/init.volte_imsm_93.rc:26 | property:vendor.volte_md_status=stop | stop vendor.volte_imcb
vendor/init/init.volte_imsm_93.rc:27 | property:vendor.volte_md_status=stop | stop vendor.volte_ua
vendor/init/init.volte_imsm_93.rc:28 | property:vendor.volte_md_status=stop | stop vendor.volte_stack
vendor/init/init.volte_imsm_93.rc:29 | property:vendor.volte_md_status=stop | stop volte_imsm_93
vendor/init/init.volte_imsm_93.rc:32 | property:vendor.volte_md_status=flightmode | rm /dev/radio/pttyims
vendor/init/init.volte_imsm_93.rc:33 | property:vendor.volte_md_status=flightmode | rm /dev/radio/ptty2ims
vendor/init/init.volte_imsm_93.rc:34 | property:vendor.volte_md_status=flightmode | rm /dev/radio/ptty3ims
vendor/init/init.volte_imsm_93.rc:35 | property:vendor.volte_md_status=flightmode | rm /dev/radio/ptty4ims
vendor/init/init.volte_imsm_93.rc:36 | property:vendor.volte_md_status=flightmode | stop vendor.volte_imcb
vendor/init/init.volte_imsm_93.rc:37 | property:vendor.volte_md_status=flightmode | stop vendor.volte_ua
vendor/init/init.volte_imsm_93.rc:38 | property:vendor.volte_md_status=flightmode | stop vendor.volte_stack
vendor/init/init.volte_imsm_93.rc:39 | property:vendor.volte_md_status=flightmode | stop volte_imsm_93
vendor/init/init.volte_imsm_93.rc:42 | property:vendor.volte_md_status=exception | rm /dev/radio/pttyims
vendor/init/init.volte_imsm_93.rc:43 | property:vendor.volte_md_status=exception | rm /dev/radio/ptty2ims
vendor/init/init.volte_imsm_93.rc:44 | property:vendor.volte_md_status=exception | rm /dev/radio/ptty3ims
vendor/init/init.volte_imsm_93.rc:45 | property:vendor.volte_md_status=exception | rm /dev/radio/ptty4ims
vendor/init/init.volte_imsm_93.rc:46 | property:vendor.volte_md_status=exception | stop vendor.volte_imcb
vendor/init/init.volte_imsm_93.rc:47 | property:vendor.volte_md_status=exception | stop vendor.volte_ua
vendor/init/init.volte_imsm_93.rc:48 | property:vendor.volte_md_status=exception | stop vendor.volte_stack
vendor/init/init.volte_imsm_93.rc:49 | property:vendor.volte_md_status=exception | stop volte_imsm_93
vendor/init/init.volte_md_status.rc:6 | init | restart volte_md_status
vendor/init/init.wlan_drv.rc:4 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wmt_chrdev_wifi.ko
vendor/init/init.wlan_drv.rc:5 | property:vendor.connsys.driver.ready=yes | insmod /vendor/lib/modules/wlan_drv_${ro.vendor.wlan.gen}.ko
vendor/init/init.wlan_drv.rc:6 | property:vendor.connsys.driver.ready=yes | start wlan_assistant
vendor/init/init.wmt_drv.rc:2 | boot | insmod /vendor/lib/modules/wmt_drv.ko
vendor/init/init.wod.rc:2 | post-fs-data | mkdir /data/vendor/ipsec 0700 root root
vendor/init/init.wod.rc:3 | post-fs-data | mkdir /data/vendor/ipsec/wo 0700 root root
vendor/init/init.wod.rc:4 | post-fs-data | mkdir /dev/vendor/net/ 0700 root root
vendor/init/init.wod.rc:7 | property:vendor.volte_md_status=ready && property:persist.vendor.mtk_wfc_support=1 | start vendor.epdg_wod
vendor/init/init.wod.rc:10 | property:vendor.volte_md_status=reset | stop vendor.epdg_wod
vendor/init/init.wod.rc:13 | property:vendor.volte_md_status=stop | stop vendor.epdg_wod
vendor/init/init.wod.rc:16 | property:vendor.volte_md_status=flightmode | stop vendor.epdg_wod
vendor/init/init.wod.rc:19 | property:vendor.volte_md_status=exception | stop vendor.epdg_wod
vendor/init/init.wod.rc:23 | property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting | setprop vendor.ril.mux.report.case 2
vendor/init/init.wod.rc:24 | property:ro.vendor.md_apps.load_type=user && property:init.svc.vendor.epdg_wod=restarting | setprop vendor.ril.muxreport 1
vendor/init/init_connectivity.rc:4 | post-fs-data | mkdir /data/vendor/stp_dump 0755 system system
vendor/init/init_connectivity.rc:5 | post-fs-data | mkdir /data/vendor/connsyslog 0755 system system
vendor/init/init_connectivity.rc:22 | property:ro.build.type=eng | write /sys/kernel/debug/tracing/tracing_on 1
vendor/init/init_connectivity.rc:25 | property:persist.vendor.connsys.coredump.mode=1 | start stp_dump
vendor/init/init_connectivity.rc:28 | property:persist.vendor.connsys.coredump.mode=2 | start stp_dump
vendor/init/init_connectivity.rc:31 | property:persist.vendor.connsys.coredump.mode=0 | stop stp_dump
vendor/init/init_connectivity.rc:34 | property:vendor.connsys.driver.ready=yes && property:persist.vendor.connsys.coredump.mode=* | write /proc/driver/wmt_dbg "0x15 ${persist.vendor.connsys.coredump.mode}"
vendor/init/init_connectivity.rc:37 | property:vendor.connsys.driver.ready=yes && property:debug.vendor.connsys.suspend.alarm=* | write /proc/driver/wmt_dbg "0x2e ${debug.vendor.connsys.suspend.alarm}"
vendor/init/mtk_agpsd_p.rc:2 | post-fs-data | mkdir /data/vendor/agps_supl 0771 gps system
vendor/init/mtkrild.rc:3 | post-fs-data | mkdir /data/vendor/radio 0771 radio radio
vendor/init/mtkrild.rc:18 | property:vendor.ril.mtk=1 | start vendor.ril-daemon-mtk
vendor/init/mtkrild.rc:21 | property:vold.decrypt=trigger_restart_framework | start vendor.ril-daemon-mtk
vendor/init/muxreport.rc:9 | property:vendor.ril.muxreport=1 | start vendor.muxreport-daemon
vendor/init/netdagent.rc:9 | init | setprop ro.vendor.net.upload.benchmark.default blocking
vendor/init/netdagent.rc:12 | property:persist.vendor.operator.optr=OP12 | write /proc/sys/net/optr 12
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:2 | post-fs-data | mkdir /data/vendor/powerhal 0775 system system
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:5 | post-fs-data | chown system system /dev/stune/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:6 | post-fs-data | chmod 0664 /dev/stune/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:7 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:8 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:9 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:10 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:11 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:12 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:13 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:14 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:15 | post-fs-data | chown system system /dev/stune/io/schedtune.uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:16 | post-fs-data | chmod 0664 /dev/stune/io/schedtune.uclamp_min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:17 | post-fs-data | chown system system /dev/stune/io/schedtune.util.min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:18 | post-fs-data | chmod 0664 /dev/stune/io/schedtune.util.min
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:19 | post-fs-data | chown system system /sys/devices/system/cpu/sched/idle_prefer
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:20 | post-fs-data | chmod 0664 /sys/devices/system/cpu/sched/idle_prefer
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:21 | post-fs-data | chown system system /dev/stune/top-app/schedtune.prefer_idle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:22 | post-fs-data | chmod 0664 /dev/stune/top-app/schedtune.prefer_idle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:23 | post-fs-data | chown system system /dev/stune/foreground/schedtune.prefer_idle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:24 | post-fs-data | chmod 0664 /dev/stune/foreground/schedtune.prefer_idle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:25 | post-fs-data | chown system system /dev/stune/io/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:26 | post-fs-data | chmod 0664 /dev/stune/io/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:27 | post-fs-data | chown system system /sys/devices/system/cpu/sched/sched_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:28 | post-fs-data | chmod 0664 /sys/devices/system/cpu/sched/sched_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:29 | post-fs-data | chown system system /dev/stune/background/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:30 | post-fs-data | chmod 0664 /dev/stune/background/schedtune.boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:31 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_down_throttle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:32 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_down_throttle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:33 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_up_throttle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:34 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_up_throttle
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:35 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_sync_flag
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:36 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_schedplus_sync_flag
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:37 | post-fs-data | chown system system /sys/devices/system/cpu/sched/cpu_prefer
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:38 | post-fs-data | chmod 0664 /sys/devices/system/cpu/sched/cpu_prefer
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:41 | post-fs-data | chown system system /proc/fliperfs/cg_enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:42 | post-fs-data | chmod 0664 /proc/fliperfs/cg_enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:43 | post-fs-data | chown system system /proc/fliperfs/cg_threshold
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:44 | post-fs-data | chmod 0664 /proc/fliperfs/cg_threshold
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:45 | post-fs-data | chown system system /proc/fliperfs/cg_threshold_ddr3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:46 | post-fs-data | chmod 0664 /proc/fliperfs/cg_threshold_ddr3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:47 | post-fs-data | chown system system /proc/fliperfs/perf
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:48 | post-fs-data | chmod 0664 /proc/fliperfs/perf
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:49 | post-fs-data | chown system system /proc/fliperfs/perf_ddr3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:50 | post-fs-data | chmod 0664 /proc/fliperfs/perf_ddr3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:51 | post-fs-data | chown system system /proc/fliperfs/emi
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:52 | post-fs-data | chmod 0664 /proc/fliperfs/emi
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:53 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/emi
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:54 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/dram_ctrl/emi
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:55 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/ddr
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:56 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/dram_ctrl/ddr
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:57 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/vcore
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:58 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/dram_ctrl/vcore
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:59 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/dram_ctrl/vcore_lp3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:60 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/dram_ctrl/vcore_lp3
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:63 | post-fs-data | chown system system /proc/cpufreq/cpufreq_cci_mode
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:64 | post-fs-data | chmod 0664 /proc/cpufreq/cpufreq_cci_mode
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:67 | post-fs-data | chown system system /proc/perfmgr/smart/app_is_sports
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:68 | post-fs-data | chmod 0660 /proc/perfmgr/smart/app_is_sports
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:69 | post-fs-data | chown system system /proc/perfmgr/legacy/perfserv_core
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:70 | post-fs-data | chmod 0664 /proc/perfmgr/legacy/perfserv_core
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:71 | post-fs-data | chown system system /proc/perfmgr/legacy/perfserv_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:72 | post-fs-data | chmod 0664 /proc/perfmgr/legacy/perfserv_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:73 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:74 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ta_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:75 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:76 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_fg_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:77 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:78 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_bg_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:79 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_core
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:80 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_core
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:81 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:82 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/perfserv_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:83 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ext_launch_mon
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:84 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/perfserv_ext_launch_mon
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:85 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/m_sched_migrate_cost_n
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:86 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/m_sched_migrate_cost_n
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:87 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/sched_big_task_rotation
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:88 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/sched_big_task_rotation
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:89 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/eas_ctrl/sched_stune_task_thresh
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:90 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/eas_ctrl/sched_stune_task_thresh
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:91 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/topo_ctrl/is_big_little
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:92 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/topo_ctrl/is_big_little
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:93 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/topo_ctrl/nr_clusters
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:94 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/topo_ctrl/nr_clusters
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:95 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/topo_ctrl/cpus_per_cluster
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:96 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/topo_ctrl/cpus_per_cluster
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:97 | post-fs-data | chown system system /proc/perfmgr/tchbst/user/usrtch
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:98 | post-fs-data | chmod 0664 /proc/perfmgr/tchbst/user/usrtch
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:99 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:100 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:101 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_polling_ms
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:102 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_polling_ms
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:103 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:104 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:105 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:106 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:107 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_time
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:108 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_time
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:109 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_time
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:110 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_time
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:111 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:112 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_up_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:113 | post-fs-data | chown system system /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:114 | post-fs-data | chmod 0664 /proc/perfmgr/boost_ctrl/cpu_ctrl/cfp_down_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:117 | post-fs-data | chown system system /sys/module/ged/parameters/gx_top_app_pid
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:118 | post-fs-data | chmod 0660 /sys/module/ged/parameters/gx_top_app_pid
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:119 | post-fs-data | chown system system /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:120 | post-fs-data | chmod 0664 /sys/module/ged/parameters/gx_3D_benchmark_on
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:121 | post-fs-data | chown system system /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:122 | post-fs-data | chmod 0660 /sys/module/ged/parameters/gx_force_cpu_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:123 | post-fs-data | chown system system /sys/kernel/debug/ged/hal/timer_base_dvfs_margin
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:124 | post-fs-data | chmod 0664 /sys/kernel/debug/ged/hal/timer_base_dvfs_margin
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:125 | post-fs-data | chown system system /sys/kernel/debug/ged/hal/loading_base_dvfs_step
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:126 | post-fs-data | chmod 0664 /sys/kernel/debug/ged/hal/loading_base_dvfs_step
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:127 | post-fs-data | chown system system /sys/kernel/debug/ged/hal/dvfs_margin_value
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:128 | post-fs-data | chmod 0664 /sys/kernel/debug/ged/hal/dvfs_margin_value
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:131 | post-fs-data | chown system system /proc/ppm/policy_status
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:132 | post-fs-data | chmod 0664 /proc/ppm/policy_status
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:133 | post-fs-data | chown system system /proc/ppm/policy/userlimit_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:134 | post-fs-data | chmod 0664 /proc/ppm/policy/userlimit_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:135 | post-fs-data | chown system system /proc/ppm/policy/hard_userlimit_cpu_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:136 | post-fs-data | chmod 0664 /proc/ppm/policy/hard_userlimit_cpu_freq
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:139 | post-fs-data | chown system system /sys/module/ca_drv/parameters/enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:140 | post-fs-data | chmod 0664 /sys/module/ca_drv/parameters/enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:143 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/bhr_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:144 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/bhr_opp
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:145 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:146 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/floor_bound
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:147 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/kmin
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:148 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/kmin
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:149 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_tune_dfps_ceiling
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:150 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_tune_dfps_ceiling
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:151 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_soft_level
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:152 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_soft_level
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:153 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_fps_list
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:154 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_fps_list
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:155 | post-fs-data | chown system system /proc/cm_mgr/dbg_cm_mgr
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:156 | post-fs-data | chmod 0664 /proc/cm_mgr/dbg_cm_mgr
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:157 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/fstb_fteh_list
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:158 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/fstb_fteh_list
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:159 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/eara/ai_bench
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:160 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/eara/ai_bench
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:161 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/common/gpu_block_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:162 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/common/gpu_block_boost
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:163 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fbt/boost_ta
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:164 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fbt/boost_ta
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:165 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/margin_mode
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:166 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/margin_mode
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:167 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/margin_mode_dbnc_a
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:168 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/margin_mode_dbnc_a
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:169 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fstb/margin_mode_dbnc_b
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:170 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fstb/margin_mode_dbnc_b
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:171 | post-fs-data | chown system system /sys/kernel/debug/eara_thermal/enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:172 | post-fs-data | chmod 0664 /sys/kernel/debug/eara_thermal/enable
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:173 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/rescue_percent
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:174 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/rescue_percent
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:175 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/rescue_enhance_f
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:176 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/rescue_enhance_f
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:177 | post-fs-data | chown system system /sys/kernel/debug/fpsgo/fbt/ultra_rescue
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:178 | post-fs-data | chmod 0664 /sys/kernel/debug/fpsgo/fbt/ultra_rescue
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:179 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/deqtime_bound
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:180 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/deqtime_bound
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:181 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/short_rescue_ns
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:182 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/short_rescue_ns
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:183 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/min_rescue_percent
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:184 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/min_rescue_percent
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:185 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/adjust_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:186 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/adjust_loading
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:187 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/loading_adj_cnt
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:188 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/loading_adj_cnt
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:189 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/loading_debnc_cnt
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:190 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/loading_debnc_cnt
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:191 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/loading_time_diff
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:192 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/loading_time_diff
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:193 | post-fs-data | chown system system /sys/module/fbt_cpu/parameters/loading_th
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:194 | post-fs-data | chmod 0664 /sys/module/fbt_cpu/parameters/loading_th
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:197 | post-fs-data | chown system system /sys/kernel/debug/mdla/power
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:198 | post-fs-data | chmod 0664 /sys/kernel/debug/mdla/power
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:199 | post-fs-data | chown system system /sys/kernel/debug/vpu/power
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:200 | post-fs-data | chmod 0664 /sys/kernel/debug/vpu/power
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:203 | post-fs-data | chown system system /proc/net/wlan/setCAM
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:204 | post-fs-data | chmod 0664 /proc/net/wlan/setCAM
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:207 | post-fs-data | chown system system /sys/fs/f2fs/sdc42/current_flush_merge
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:208 | post-fs-data | chmod 0664 /sys/fs/f2fs/sdc42/current_flush_merge
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:209 | post-fs-data | chown system system /sys/fs/f2fs/mmcblk0p42/current_flush_merge
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:210 | post-fs-data | chmod 0664 /sys/fs/f2fs/mmcblk0p42/current_flush_merge
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:211 | post-fs-data | chown system system /sys/fs/ext4/sdc42/temp_disable_barrier
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:212 | post-fs-data | chmod 0664 /sys/fs/ext4/sdc42/temp_disable_barrier
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:215 | post-fs-data | chown system system /sys/block/sdc/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:216 | post-fs-data | chmod 0664 /sys/block/sdc/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:217 | post-fs-data | chown system system /sys/block/dm-2/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:218 | post-fs-data | chmod 0664 /sys/block/dm-2/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:219 | post-fs-data | chown system system /sys/block/mmcblk0/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-init.rc:220 | post-fs-data | chmod 0664 /sys/block/mmcblk0/queue/read_ahead_kb
vendor/init/vendor.mediatek.hardware.mtkpower@1.0-service.rc:8 | property:vold.decrypt=trigger_shutdown_framework | setprop vendor.powerhal.init 0
```

################################################################################
# APPENDIX B — COMPLETE PROCESS SNAPSHOT
################################################################################

Format: `label | user | pid | ppid | name | args`

```text
u:r:init:s0 | root | 1 | 0 | init | init second_stage
u:r:kernel:s0 | root | 2 | 0 | [kthreadd] | [kthreadd]
u:r:kernel:s0 | root | 4 | 2 | [kworker/0:0H] | [kworker/0:0H]
u:r:kernel:s0 | root | 6 | 2 | [ksoftirqd/0] | [ksoftirqd/0]
u:r:kernel:s0 | root | 7 | 2 | [rcu_preempt] | [rcu_preempt]
u:r:kernel:s0 | root | 8 | 2 | [rcu_sched] | [rcu_sched]
u:r:kernel:s0 | root | 9 | 2 | [rcu_bh] | [rcu_bh]
u:r:kernel:s0 | root | 10 | 2 | [migration/0] | [migration/0]
u:r:kernel:s0 | root | 11 | 2 | [lru-add-drain] | [lru-add-drain]
u:r:kernel:s0 | root | 12 | 2 | [cpuhp/0] | [cpuhp/0]
u:r:kernel:s0 | root | 13 | 2 | [cpuhp/1] | [cpuhp/1]
u:r:kernel:s0 | root | 14 | 2 | [migration/1] | [migration/1]
u:r:kernel:s0 | root | 15 | 2 | [ksoftirqd/1] | [ksoftirqd/1]
u:r:kernel:s0 | root | 17 | 2 | [kworker/1:0H] | [kworker/1:0H]
u:r:kernel:s0 | root | 18 | 2 | [cpuhp/2] | [cpuhp/2]
u:r:kernel:s0 | root | 19 | 2 | [migration/2] | [migration/2]
u:r:kernel:s0 | root | 20 | 2 | [ksoftirqd/2] | [ksoftirqd/2]
u:r:kernel:s0 | root | 22 | 2 | [kworker/2:0H] | [kworker/2:0H]
u:r:kernel:s0 | root | 23 | 2 | [cpuhp/3] | [cpuhp/3]
u:r:kernel:s0 | root | 24 | 2 | [migration/3] | [migration/3]
u:r:kernel:s0 | root | 25 | 2 | [ksoftirqd/3] | [ksoftirqd/3]
u:r:kernel:s0 | root | 27 | 2 | [kworker/3:0H] | [kworker/3:0H]
u:r:kernel:s0 | root | 28 | 2 | [cpuhp/4] | [cpuhp/4]
u:r:kernel:s0 | root | 29 | 2 | [migration/4] | [migration/4]
u:r:kernel:s0 | root | 30 | 2 | [ksoftirqd/4] | [ksoftirqd/4]
u:r:kernel:s0 | root | 32 | 2 | [kworker/4:0H] | [kworker/4:0H]
u:r:kernel:s0 | root | 33 | 2 | [cpuhp/5] | [cpuhp/5]
u:r:kernel:s0 | root | 34 | 2 | [migration/5] | [migration/5]
u:r:kernel:s0 | root | 35 | 2 | [ksoftirqd/5] | [ksoftirqd/5]
u:r:kernel:s0 | root | 37 | 2 | [kworker/5:0H] | [kworker/5:0H]
u:r:kernel:s0 | root | 38 | 2 | [cpuhp/6] | [cpuhp/6]
u:r:kernel:s0 | root | 39 | 2 | [migration/6] | [migration/6]
u:r:kernel:s0 | root | 40 | 2 | [ksoftirqd/6] | [ksoftirqd/6]
u:r:kernel:s0 | root | 42 | 2 | [kworker/6:0H] | [kworker/6:0H]
u:r:kernel:s0 | root | 43 | 2 | [cpuhp/7] | [cpuhp/7]
u:r:kernel:s0 | root | 44 | 2 | [migration/7] | [migration/7]
u:r:kernel:s0 | root | 45 | 2 | [ksoftirqd/7] | [ksoftirqd/7]
u:r:kernel:s0 | root | 47 | 2 | [kworker/7:0H] | [kworker/7:0H]
u:r:kernel:s0 | root | 49 | 2 | [SSPM_WQ] | [SSPM_WQ]
u:r:kernel:s0 | root | 51 | 2 | [cmdq_buf_dump] | [cmdq_buf_dump]
u:r:kernel:s0 | root | 52 | 2 | [cmdq_timeout_ha] | [cmdq_timeout_ha]
u:r:kernel:s0 | root | 53 | 2 | [oom_reaper] | [oom_reaper]
u:r:kernel:s0 | root | 54 | 2 | [writeback] | [writeback]
u:r:kernel:s0 | root | 55 | 2 | [kcompactd0] | [kcompactd0]
u:r:kernel:s0 | root | 56 | 2 | [crypto] | [crypto]
u:r:kernel:s0 | root | 57 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 58 | 2 | [kblockd] | [kblockd]
u:r:kernel:s0 | root | 59 | 2 | [conn-md-thread] | [conn-md-thread]
u:r:kernel:s0 | root | 60 | 2 | [pool_reload_wor] | [pool_reload_wor]
u:r:kernel:s0 | root | 61 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 62 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 63 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 64 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 65 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 66 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 67 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 68 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 69 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 70 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 71 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 72 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 73 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 74 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 75 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 76 | 2 | [cmdq_flushq] | [cmdq_flushq]
u:r:kernel:s0 | root | 77 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 78 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 79 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 80 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 81 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 82 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 83 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 84 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 85 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 86 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 87 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 88 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 89 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 90 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 91 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 92 | 2 | [cmdq_auto_relea] | [cmdq_auto_relea]
u:r:kernel:s0 | root | 93 | 2 | [cmdq_resource] | [cmdq_resource]
u:r:kernel:s0 | root | 94 | 2 | [cmdq_mdp_task] | [cmdq_mdp_task]
u:r:kernel:s0 | root | 95 | 2 | [chgdet_thread] | [chgdet_thread]
u:r:kernel:s0 | root | 96 | 2 | [tcpc_power_off] | [tcpc_power_off]
u:r:kernel:s0 | root | 97 | 2 | [charger_in] | [charger_in]
u:r:kernel:s0 | root | 98 | 2 | [devfreq_wq] | [devfreq_wq]
u:r:kernel:s0 | root | 100 | 2 | [cfg80211] | [cfg80211]
u:r:kernel:s0 | root | 104 | 2 | [pmic_thread] | [pmic_thread]
u:r:kernel:s0 | root | 105 | 2 | [lbat_service] | [lbat_service]
u:r:kernel:s0 | root | 106 | 2 | [bat_percent_not] | [bat_percent_not]
u:r:kernel:s0 | root | 107 | 2 | [dlpt_notify_thr] | [dlpt_notify_thr]
u:r:kernel:s0 | root | 108 | 2 | [mdrt_thread] | [mdrt_thread]
u:r:kernel:s0 | root | 109 | 2 | [cfinteractive] | [cfinteractive]
u:r:kernel:s0 | root | 111 | 2 | [ion_mm_heap] | [ion_mm_heap]
u:r:kernel:s0 | root | 113 | 2 | [ion_mm_heap_for] | [ion_mm_heap_for]
u:r:kernel:s0 | root | 114 | 2 | [ion_comm_pool] | [ion_comm_pool]
u:r:kernel:s0 | root | 115 | 2 | [ion_mm_heap_for] | [ion_mm_heap_for]
u:r:kernel:s0 | root | 116 | 2 | [ion_comm_pool] | [ion_comm_pool]
u:r:kernel:s0 | root | 117 | 2 | [ion_mm_heap_for] | [ion_mm_heap_for]
u:r:kernel:s0 | root | 118 | 2 | [ion_history] | [ion_history]
u:r:kernel:s0 | root | 130 | 2 | [kswapd0] | [kswapd0]
u:r:kernel:s0 | root | 131 | 2 | [vmstat] | [vmstat]
u:r:kernel:s0 | root | 163 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 164 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 165 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 166 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 167 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 168 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 169 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 170 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 171 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 172 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 173 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 174 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 175 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 176 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 177 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 178 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 179 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 180 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 181 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 182 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 183 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 184 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 185 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 186 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 187 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 188 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 189 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 190 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 191 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 192 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 193 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 194 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 195 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 196 | 2 | [pbm] | [pbm]
u:r:kernel:s0 | root | 197 | 2 | [btif_rxd] | [btif_rxd]
u:r:kernel:s0 | root | 198 | 2 | [mtk_vibrator] | [mtk_vibrator]
u:r:kernel:s0 | root | 199 | 2 | [usb20_st_wq] | [usb20_st_wq]
u:r:kernel:s0 | root | 200 | 2 | [usb20_low_power] | [usb20_low_power]
u:r:kernel:s0 | root | 201 | 2 | [SCP_WQ] | [SCP_WQ]
u:r:kernel:s0 | root | 202 | 2 | [SCP_LOG_WQ] | [SCP_LOG_WQ]
u:r:kernel:s0 | root | 203 | 2 | [SCP_RESET_WQ] | [SCP_RESET_WQ]
u:r:kernel:s0 | root | 204 | 2 | [ged_notify_sw_v] | [ged_notify_sw_v]
u:r:kernel:s0 | root | 205 | 2 | [ged_dump_fw_log] | [ged_dump_fw_log]
u:r:kernel:s0 | root | 206 | 2 | [ged_kpi] | [ged_kpi]
u:r:kernel:s0 | root | 207 | 2 | [ccci_fsm1] | [ccci_fsm1]
u:r:kernel:s0 | root | 208 | 2 | [ccci_poll1] | [ccci_poll1]
u:r:kernel:s0 | root | 209 | 2 | [ccci_rpc_k] | [ccci_rpc_k]
u:r:kernel:s0 | root | 210 | 2 | [ccci_udc] | [ccci_udc]
u:r:kernel:s0 | root | 211 | 2 | [ccci_ipc_3] | [ccci_ipc_3]
u:r:kernel:s0 | root | 212 | 2 | [ccci_ipc_6] | [ccci_ipc_6]
u:r:kernel:s0 | root | 213 | 2 | [ccci_ipc_7] | [ccci_ipc_7]
u:r:kernel:s0 | root | 214 | 2 | [ccci_ipc_8] | [ccci_ipc_8]
u:r:kernel:s0 | root | 215 | 2 | [ccci_sys] | [ccci_sys]
u:r:kernel:s0 | root | 216 | 2 | [ccci_ctrl] | [ccci_ctrl]
u:r:kernel:s0 | root | 217 | 2 | [md1_cldma_worke] | [md1_cldma_worke]
u:r:kernel:s0 | root | 218 | 2 | [ccu-enque] | [ccu-enque]
u:r:kernel:s0 | root | 219 | 2 | [hang_detect] | [hang_detect]
u:r:kernel:s0 | root | 220 | 2 | [hang_detect1] | [hang_detect1]
u:r:kernel:s0 | root | 221 | 2 | [disp_switch_mod] | [disp_switch_mod]
u:r:kernel:s0 | root | 222 | 2 | [cpu_freq_wq] | [cpu_freq_wq]
u:r:kernel:s0 | root | 223 | 2 | [cpu_core_wq] | [cpu_core_wq]
u:r:kernel:s0 | root | 224 | 2 | [dram_vcore_wq] | [dram_vcore_wq]
u:r:kernel:s0 | root | 225 | 2 | [bcct_chrlmt_wor] | [bcct_chrlmt_wor]
u:r:kernel:s0 | root | 226 | 2 | [krtatm] | [krtatm]
u:r:kernel:s0 | root | 227 | 2 | [mt_usrtch__work] | [mt_usrtch__work]
u:r:kernel:s0 | root | 228 | 2 | [cpu_loading_wor] | [cpu_loading_wor]
u:r:kernel:s0 | root | 229 | 2 | [fpsgo_notifier_] | [fpsgo_notifier_]
u:r:kernel:s0 | root | 230 | 2 | [mt_fstb] | [mt_fstb]
u:r:kernel:s0 | root | 231 | 2 | [lt_wq] | [lt_wq]
u:r:kernel:s0 | root | 232 | 2 | [mem_thrash_dete] | [mem_thrash_dete]
u:r:kernel:s0 | root | 233 | 2 | [mtk_io_boost] | [mtk_io_boost]
u:r:kernel:s0 | root | 234 | 2 | [spi32766] | [spi32766]
u:r:kernel:s0 | root | 235 | 2 | [spi32765] | [spi32765]
u:r:kernel:s0 | root | 236 | 2 | [spi32764] | [spi32764]
u:r:kernel:s0 | root | 237 | 2 | [spi32763] | [spi32763]
u:r:kernel:s0 | root | 238 | 2 | [spi32762] | [spi32762]
u:r:kernel:s0 | root | 239 | 2 | [spi32761] | [spi32761]
u:r:kernel:s0 | root | 240 | 2 | [uether] | [uether]
u:r:kernel:s0 | root | 241 | 2 | [uether_rx1] | [uether_rx1]
u:r:kernel:s0 | root | 249 | 2 | [irq/27-WIITE_CO] | [irq/27-WIITE_CO]
u:r:kernel:s0 | root | 250 | 2 | [irq/25-WIITE_CO] | [irq/25-WIITE_CO]
u:r:kernel:s0 | root | 251 | 2 | [irq/30-mt6370_p] | [irq/30-mt6370_p]
u:r:kernel:s0 | root | 252 | 2 | [gauge_coulomb_t] | [gauge_coulomb_t]
u:r:kernel:s0 | root | 253 | 2 | [battery_thread] | [battery_thread]
u:r:kernel:s0 | root | 254 | 2 | [power_misc_thre] | [power_misc_thre]
u:r:kernel:s0 | root | 255 | 2 | [gauge_timer_thr] | [gauge_timer_thr]
u:r:kernel:s0 | root | 256 | 2 | [dm_bufio_cache] | [dm_bufio_cache]
u:r:kernel:s0 | root | 263 | 2 | [ipv6_addrconf] | [ipv6_addrconf]
u:r:kernel:s0 | root | 264 | 2 | [ddp_irq_log_kth] | [ddp_irq_log_kth]
u:r:kernel:s0 | root | 266 | 2 | [disp_idlemgr] | [disp_idlemgr]
u:r:kernel:s0 | root | 267 | 2 | [disp_check] | [disp_check]
u:r:kernel:s0 | root | 268 | 2 | [decouple_update] | [decouple_update]
u:r:kernel:s0 | root | 269 | 2 | [decouple_trigge] | [decouple_trigge]
u:r:kernel:s0 | root | 270 | 2 | [display_check_a] | [display_check_a]
u:r:kernel:s0 | root | 271 | 2 | [disp_delay_trig] | [disp_delay_trig]
u:r:kernel:s0 | root | 272 | 2 | [disp_od_trigger] | [disp_od_trigger]
u:r:kernel:s0 | root | 273 | 2 | [present_fence_w] | [present_fence_w]
u:r:kernel:s0 | root | 274 | 2 | [ion_fb_heap] | [ion_fb_heap]
u:r:kernel:s0 | root | 275 | 2 | [exe_cq/0] | [exe_cq/0]
u:r:kernel:s0 | root | 276 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 277 | 2 | [mmcqd/0] | [mmcqd/0]
u:r:kernel:s0 | root | 278 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 279 | 2 | [mmcqd/0boot0] | [mmcqd/0boot0]
u:r:kernel:s0 | root | 280 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 281 | 2 | [mmcqd/0boot1] | [mmcqd/0boot1]
u:r:kernel:s0 | root | 282 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 283 | 2 | [mmcqd/0rpmb] | [mmcqd/0rpmb]
u:r:kernel:s0 | root | 284 | 2 | [ipi_cpu_dvfs_rt] | [ipi_cpu_dvfs_rt]
u:r:kernel:s0 | root | 285 | 2 | [EEM_CTRL_L] | [EEM_CTRL_L]
u:r:kernel:s0 | root | 286 | 2 | [EEM_CTRL_2L] | [EEM_CTRL_2L]
u:r:kernel:s0 | root | 287 | 2 | [EEM_CTRL_CCI] | [EEM_CTRL_CCI]
u:r:kernel:s0 | root | 288 | 2 | [EEM_CTRL_L_HI] | [EEM_CTRL_L_HI]
u:r:kernel:s0 | root | 289 | 2 | [EEM_CTRL_2L_HI] | [EEM_CTRL_2L_HI]
u:r:kernel:s0 | root | 290 | 2 | [Init_1_Stress] | [Init_1_Stress]
u:r:kernel:s0 | root | 291 | 2 | [hps_main] | [hps_main]
u:r:kernel:s0 | root | 292 | 2 | [ksched_hint] | [ksched_hint]
u:r:kernel:s0 | root | 293 | 2 | [accel_polling] | [accel_polling]
u:r:kernel:s0 | root | 294 | 2 | [baro_polling] | [baro_polling]
u:r:kernel:s0 | root | 295 | 2 | [temp_polling] | [temp_polling]
u:r:kernel:s0 | root | 296 | 2 | [alti_polling] | [alti_polling]
u:r:kernel:s0 | root | 297 | 2 | [pvr_timer] | [pvr_timer]
u:r:kernel:s0 | root | 298 | 2 | [pvr_defer_free] | [pvr_defer_free]
u:r:kernel:s0 | root | 299 | 2 | [pvr_device_wdg] | [pvr_device_wdg]
u:r:kernel:s0 | root | 300 | 2 | [pvr_cacheop] | [pvr_cacheop]
u:r:kernel:s0 | root | 301 | 2 | [mwp] | [mwp]
u:r:kernel:s0 | root | 302 | 2 | [pvr_fce_status] | [pvr_fce_status]
u:r:kernel:s0 | root | 303 | 2 | [sys_timer_wq] | [sys_timer_wq]
u:r:kernel:s0 | root | 304 | 2 | [mtk-tpd] | [mtk-tpd]
u:r:kernel:s0 | root | 305 | 2 | [hyn_wq] | [hyn_wq]
u:r:kernel:s0 | root | 306 | 2 | [mtk-tpd] | [mtk-tpd]
u:r:kernel:s0 | root | 307 | 2 | [touch_resume] | [touch_resume]
u:r:kernel:s0 | root | 308 | 2 | [charger_thread] | [charger_thread]
u:r:kernel:s0 | root | 309 | 2 | [mt-wdk] | [mt-wdk]
u:r:kernel:s0 | root | 310 | 2 | [wdtk-0] | [wdtk-0]
u:r:kernel:s0 | root | 311 | 2 | [wdtk-1] | [wdtk-1]
u:r:kernel:s0 | root | 312 | 2 | [wdtk-2] | [wdtk-2]
u:r:kernel:s0 | root | 313 | 2 | [wdtk-3] | [wdtk-3]
u:r:kernel:s0 | root | 314 | 2 | [wdtk-4] | [wdtk-4]
u:r:kernel:s0 | root | 315 | 2 | [wdtk-5] | [wdtk-5]
u:r:kernel:s0 | root | 316 | 2 | [wdtk-6] | [wdtk-6]
u:r:kernel:s0 | root | 317 | 2 | [wdtk-7] | [wdtk-7]
u:r:kernel:s0 | root | 318 | 2 | [qos_recv] | [qos_recv]
u:r:kernel:s0 | root | 320 | 2 | [kworker/7:1H] | [kworker/7:1H]
u:r:kernel:s0 | root | 322 | 2 | [kdmflush] | [kdmflush]
u:r:kernel:s0 | root | 323 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 325 | 2 | [kdmflush] | [kdmflush]
u:r:kernel:s0 | root | 326 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 328 | 2 | [kdmflush] | [kdmflush]
u:r:kernel:s0 | root | 329 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 330 | 2 | [kworker/5:1H] | [kworker/5:1H]
u:r:kernel:s0 | root | 331 | 2 | [kworker/4:1H] | [kworker/4:1H]
u:r:kernel:s0 | root | 332 | 2 | [jbd2/mmcblk0p10] | [jbd2/mmcblk0p10]
u:r:kernel:s0 | root | 333 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 334 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 336 | 2 | [kworker/6:1H] | [kworker/6:1H]
u:r:kernel:s0 | root | 337 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 338 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:vendor_init:s0 | root | 342 | 1 | init | init subcontext u:r:vendor_init:s0 12
u:r:vendor_init:s0 | root | 343 | 1 | init | init subcontext u:r:vendor_init:s0 13
u:r:ueventd:s0 | root | 344 | 1 | ueventd | ueventd
u:r:kernel:s0 | root | 346 | 2 | [kworker/0:1H] | [kworker/0:1H]
u:r:kernel:s0 | root | 355 | 2 | [loop0] | [loop0]
u:r:kernel:s0 | root | 356 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 357 | 2 | [loop1] | [loop1]
u:r:kernel:s0 | root | 358 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:logd:s0 | logd | 359 | 1 | logd | logd
u:r:servicemanager:s0 | system | 360 | 1 | servicemanager | servicemanager
u:r:hwservicemanager:s0 | system | 361 | 1 | hwservicemanager | hwservicemanager
u:r:vndservicemanager:s0 | system | 362 | 1 | vndservicemanager | vndservicemanager /dev/vndbinder
u:r:kernel:s0 | root | 368 | 2 | [kauditd] | [kauditd]
u:r:volte_md_status:s0 | root | 369 | 1 | volte_md_status | volte_md_status
u:r:vold:s0 | root | 372 | 1 | vold | vold --blkid_context=u:r:blkid:s0 --blkid_untrusted_context=u:r:blkid_untrusted:s0 --fsck_context=u:r:fsck:s0 --fsck_untrusted_context=u:r:fsck_untrusted:s0
u:r:kernel:s0 | root | 383 | 2 | [jbd2/mmcblk0p36] | [jbd2/mmcblk0p36]
u:r:kernel:s0 | root | 384 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 388 | 2 | [jbd2/mmcblk0p35] | [jbd2/mmcblk0p35]
u:r:kernel:s0 | root | 389 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 393 | 2 | [jbd2/mmcblk0p12] | [jbd2/mmcblk0p12]
u:r:kernel:s0 | root | 394 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 398 | 2 | [jbd2/mmcblk0p13] | [jbd2/mmcblk0p13]
u:r:kernel:s0 | root | 399 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 403 | 2 | [jbd2/mmcblk0p9-] | [jbd2/mmcblk0p9-]
u:r:kernel:s0 | root | 404 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 408 | 2 | [jbd2/mmcblk0p8-] | [jbd2/mmcblk0p8-]
u:r:kernel:s0 | root | 409 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 411 | 2 | [f_mtp] | [f_mtp]
u:r:system_suspend:s0 | system | 412 | 1 | android.system.suspend@1.0-service | android.system.suspend@1.0-service
u:r:hal_keymaster_default:s0 | system | 413 | 1 | android.hardware.keymaster@4.0-service | android.hardware.keymaster@4.0-service
u:r:mtk_wmt_launcher:s0 | system | 415 | 1 | wmt_launcher | wmt_launcher -p /vendor/firmware/ -o 1
u:r:apexd:s0 | root | 436 | 1 | apexd | apexd
u:r:kernel:s0 | root | 437 | 2 | [irq/22-11240000] | [irq/22-11240000]
u:r:kernel:s0 | root | 438 | 2 | [loop2] | [loop2]
u:r:kernel:s0 | root | 439 | 2 | [kworker/1:2] | [kworker/1:2]
u:r:kernel:s0 | root | 441 | 2 | [kdmflush] | [kdmflush]
u:r:kernel:s0 | root | 442 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 443 | 2 | [kverityd] | [kverityd]
u:r:kernel:s0 | root | 444 | 2 | [bioset] | [bioset]
u:r:kernel:s0 | root | 445 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 446 | 2 | [loop3] | [loop3]
u:r:kernel:s0 | root | 447 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 448 | 2 | [loop4] | [loop4]
u:r:kernel:s0 | root | 449 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 450 | 2 | [loop5] | [loop5]
u:r:kernel:s0 | root | 451 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 452 | 2 | [loop6] | [loop6]
u:r:kernel:s0 | root | 453 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 454 | 2 | [loop7] | [loop7]
u:r:kernel:s0 | root | 455 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:kernel:s0 | root | 456 | 2 | [loop8] | [loop8]
u:r:kernel:s0 | root | 457 | 2 | [ext4-rsv-conver] | [ext4-rsv-conver]
u:r:magisk:s0 | root | 466 | 1 | magiskd | magiskd
u:r:kernel:s0 | root | 469 | 2 | [kworker/1:1H] | [kworker/1:1H]
u:r:netd:s0 | root | 511 | 1 | netd | netd
u:r:zygote:s0 | root | 512 | 1 | zygote64 | zygote64
u:r:zygote:s0 | root | 513 | 1 | zygote | zygote
u:r:hal_allocator_default:s0 | system | 515 | 1 | android.hidl.allocator@1.0-service | android.hidl.allocator@1.0-service
u:r:mtk_hal_audio:s0 | audioserver | 516 | 1 | android.hardware.audio@5.0-service-mediatek | android.hardware.audio@5.0-service-mediatek
u:r:mtk_hal_bluetooth:s0 | bluetooth | 517 | 1 | android.hardware.bluetooth@1.0-service-mediatek | android.hardware.bluetooth@1.0-service-mediatek
u:r:hal_cas_default:s0 | media | 518 | 1 | android.hardware.cas@1.1-service | android.hardware.cas@1.1-service
u:r:hal_configstore_default:s0 | system | 519 | 1 | android.hardware.configstore@1.1-service | android.hardware.configstore@1.1-service
u:r:hal_drm_default:s0 | media | 520 | 1 | android.hardware.drm@1.0-service | android.hardware.drm@1.0-service
u:r:hal_drm_clearkey:s0 | media | 521 | 1 | android.hardware.drm@1.2-service.clearkey | android.hardware.drm@1.2-service.clearkey
u:r:hal_drm_widevine:s0 | media | 522 | 1 | android.hardware.drm@1.2-service.widevine | android.hardware.drm@1.2-service.widevine
u:r:hal_gatekeeper_default:s0 | system | 523 | 1 | android.hardware.gatekeeper@1.0-service | android.hardware.gatekeeper@1.0-service
u:r:mtk_hal_gnss:s0 | system | 524 | 1 | android.hardware.gnss@2.0-service-mediatek | android.hardware.gnss@2.0-service-mediatek
u:r:hal_graphics_allocator_default:s0 | system | 525 | 1 | android.hardware.graphics.allocator@2.0-service | android.hardware.graphics.allocator@2.0-service
u:r:hal_graphics_composer_default:s0 | system | 526 | 1 | android.hardware.graphics.composer@2.1-service | android.hardware.graphics.composer@2.1-service
u:r:hal_health_default:s0 | system | 527 | 1 | android.hardware.health@2.0-service | android.hardware.health@2.0-service
u:r:mtk_hal_light:s0 | system | 528 | 1 | android.hardware.light@2.0-service-mediatek | android.hardware.light@2.0-service-mediatek
u:r:hal_memtrack_default:s0 | system | 529 | 1 | android.hardware.memtrack@1.0-service | android.hardware.memtrack@1.0-service
u:r:mtk_hal_secure_element:s0 | secure_element | 531 | 1 | android.hardware.secure_element@1.0-service-mediatek | android.hardware.secure_element@1.0-service-mediatek
u:r:hal_thermal_default:s0 | system | 532 | 1 | android.hardware.thermal@1.0-service | android.hardware.thermal@1.0-service
u:r:mtk_hal_usb:s0 | system | 534 | 1 | android.hardware.usb@1.1-service-mediatek | android.hardware.usb@1.1-service-mediatek
u:r:hal_vibrator_default:s0 | system | 536 | 1 | android.hardware.vibrator@1.0-service | android.hardware.vibrator@1.0-service
u:r:vtservice_hidl:s0 | system | 537 | 1 | vtservice_hidl | vtservice_hidl
u:r:lbs_hidl_service:s0 | system | 538 | 1 | lbs_hidl_service | lbs_hidl_service
u:r:mtk_hal_gpu:s0 | system | 539 | 1 | vendor.mediatek.hardware.gpu@1.0-service | vendor.mediatek.hardware.gpu@1.0-service
u:r:mtk_hal_power:s0 | system | 540 | 1 | vendor.mediatek.hardware.mtkpower@1.0-service | vendor.mediatek.hardware.mtkpower@1.0-service
u:r:nvram_agent_binder:s0 | root | 541 | 1 | vendor.mediatek.hardware.nvram@1.1-service | vendor.mediatek.hardware.nvram@1.1-service
u:r:ashmemd:s0 | nobody | 542 | 1 | ashmemd | ashmemd
u:r:audioserver:s0 | audioserver | 544 | 1 | audioserver | audioserver
u:r:gpuservice:s0 | gpu_service | 547 | 1 | gpuservice | gpuservice
u:r:lmkd:s0 | lmkd | 549 | 1 | lmkd | lmkd
u:r:surfaceflinger:s0 | system | 550 | 1 | surfaceflinger | surfaceflinger
u:r:fuelgauged:s0 | system | 552 | 1 | fuelgauged | fuelgauged
u:r:ccci_fsd:s0 | radio | 553 | 1 | ccci_fsd | ccci_fsd 0
u:r:ccci_fsd:s0 | radio | 556 | 1 | ccci_fsd | ccci_fsd 2
u:r:ccci_mdinit:s0 | system | 558 | 1 | ccci_mdinit | ccci_mdinit 0
u:r:ccci_rpcd:s0 | radio | 563 | 1 | ccci_rpcd | ccci_rpcd 0
u:r:wfca:s0 | root | 565 | 1 | wfca | wfca
u:r:kernel:s0 | root | 595 | 2 | [pvr_misr] | [pvr_misr]
u:r:kernel:s0 | root | 596 | 2 | [pvr_misr] | [pvr_misr]
u:r:kernel:s0 | root | 599 | 2 | [pvr_misr] | [pvr_misr]
u:r:kernel:s0 | root | 603 | 2 | [kworker/2:1H] | [kworker/2:1H]
u:r:kernel:s0 | root | 612 | 2 | [md1_tx0_worker] | [md1_tx0_worker]
u:r:kernel:s0 | root | 613 | 2 | [md1_tx1_worker] | [md1_tx1_worker]
u:r:kernel:s0 | root | 614 | 2 | [md1_tx2_worker] | [md1_tx2_worker]
u:r:kernel:s0 | root | 615 | 2 | [md1_tx3_worker] | [md1_tx3_worker]
u:r:kernel:s0 | root | 616 | 2 | [md1_rx0_worker] | [md1_rx0_worker]
u:r:kernel:s0 | root | 617 | 2 | [cldma_rxq0] | [cldma_rxq0]
u:r:kernel:s0 | root | 618 | 2 | [rx0_worker] | [rx0_worker]
u:r:kernel:s0 | root | 619 | 2 | [rx1_worker] | [rx1_worker]
u:r:kernel:s0 | root | 620 | 2 | [rx2_worker] | [rx2_worker]
u:r:kernel:s0 | root | 621 | 2 | [rx3_worker] | [rx3_worker]
u:r:kernel:s0 | root | 622 | 2 | [rx4_worker] | [rx4_worker]
u:r:kernel:s0 | root | 623 | 2 | [rx5_worker] | [rx5_worker]
u:r:kernel:s0 | root | 624 | 2 | [rx6_worker] | [rx6_worker]
u:r:kernel:s0 | root | 625 | 2 | [rx7_worker] | [rx7_worker]
u:r:mnld:s0 | gps | 638 | 1 | mnld | mnld
u:r:lbs_dbg:s0 | system | 639 | 1 | lbs_dbg | lbs_dbg
u:r:cameraserver:s0 | cameraserver | 647 | 1 | cameraserver | cameraserver
u:r:drmserver:s0 | drm | 649 | 1 | drmserver | drmserver
u:r:incidentd:s0 | incidentd | 651 | 1 | incidentd | incidentd
u:r:thermald:s0 | system | 652 | 1 | thermald | thermald
u:r:vtservice:s0 | system | 653 | 1 | vtservice | vtservice
u:r:installd:s0 | root | 654 | 1 | installd | installd
u:r:keystore:s0 | keystore | 655 | 1 | keystore | keystore /data/misc/keystore
u:r:kernel:s0 | root | 656 | 2 | [mtk_stp_psm] | [mtk_stp_psm]
u:r:mediadrmserver:s0 | media | 657 | 1 | mediadrmserver | mediadrmserver
u:r:kernel:s0 | root | 660 | 2 | [mtk_stp_btm] | [mtk_stp_btm]
u:r:mediaextractor:s0 | mediaex | 665 | 1 | media.extractor | media.extractor aextractor
u:r:kernel:s0 | root | 666 | 2 | [mtk_wmtd] | [mtk_wmtd]
u:r:mediametrics:s0 | media | 667 | 1 | media.metrics | media.metrics diametrics
u:r:mediaserver:s0 | media | 668 | 1 | mediaserver | mediaserver
u:r:kernel:s0 | root | 670 | 2 | [mtk_wmtd_worker] | [mtk_wmtd_worker]
u:r:mobile_log_d:s0 | system | 671 | 1 | mobile_log_d | mobile_log_d
u:r:netdiag:s0 | shell | 673 | 1 | netdiag | netdiag
u:r:statsd:s0 | statsd | 674 | 1 | statsd | statsd
u:r:storaged:s0 | root | 687 | 1 | storaged | storaged
u:r:wificond:s0 | wifi | 688 | 1 | wificond | wificond
u:r:mediacodec:s0 | mediacodec | 693 | 1 | media.codec | media.codec hw/android.hardware.media.omx@1.0-service
u:r:mtk_hal_sensors:s0 | system | 694 | 1 | android.hardware.sensors@2.0-service-mediatek | android.hardware.sensors@2.0-service-mediatek
u:r:mtk_hal_camera:s0 | cameraserver | 696 | 1 | camerahalserver | camerahalserver
u:r:thermal:s0 | system | 699 | 1 | thermal | thermal
u:r:thermalloadalgod:s0 | system | 701 | 1 | thermalloadalgod | thermalloadalgod
u:r:ipsec_mon:s0 | system | 702 | 1 | ipsec_mon | ipsec_mon
u:r:mtk_agpsd:s0 | gps | 703 | 1 | mtk_agpsd | mtk_agpsd
u:r:mtk_hal_mms:s0 | system | 704 | 1 | vendor.mediatek.hardware.mms@1.3-service | vendor.mediatek.hardware.mms@1.3-service
u:r:mtk_hal_pq:s0 | system | 706 | 1 | vendor.mediatek.hardware.pq@2.2-service | vendor.mediatek.hardware.pq@2.2-service
u:r:wlan_assistant:s0 | wifi | 708 | 1 | wlan_assistant | wlan_assistant
u:r:mediaswcodec:s0 | mediacodec | 709 | 1 | media.swcodec | media.swcodec oid.media.swcodec/bin/mediaswcodec
u:r:gatekeeperd:s0 | system | 711 | 1 | gatekeeperd | gatekeeperd /data/misc/gatekeeper
u:r:tombstoned:s0 | tombstoned | 718 | 1 | tombstoned | tombstoned
u:r:netdagent:s0 | system | 721 | 1 | netdagent | netdagent
u:r:netd:s0 | root | 728 | 511 | iptables-restore | iptables-restore --noflush -w -v
u:r:netd:s0 | root | 729 | 511 | ip6tables-restore | ip6tables-restore --noflush -w -v
u:r:gsm0710muxd:s0 | radio | 748 | 1 | gsm0710muxd | gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic
u:r:kernel:s0 | root | 749 | 2 | [kworker/3:1H] | [kworker/3:1H]
u:r:bip:s0 | root | 816 | 1 | bip | bip
u:r:volte_imsm_93:s0 | root | 820 | 1 | volte_imsm_93 | volte_imsm_93
u:r:volte_stack:s0 | root | 823 | 1 | volte_stack | volte_stack
u:r:volte_ua:s0 | root | 827 | 1 | volte_ua | volte_ua
u:r:volte_imcb:s0 | system | 835 | 1 | volte_imcb | volte_imcb
u:r:epdg_wod:s0 | root | 836 | 1 | epdg_wod | epdg_wod
u:r:rild:s0 | radio | 982 | 1 | mtkfusionrild | mtkfusionrild -l libmtk-ril.so
u:r:system_server:s0 | system | 1075 | 512 | system_server | system_server
u:r:mtk_hal_wifi:s0 | wifi | 1229 | 1 | android.hardware.wifi@1.0-service-lazy-mediatek | android.hardware.wifi@1.0-service-lazy-mediatek
u:r:platform_app:s0:c512,c768 | u0_a45 | 1238 | 512 | com.android.systemui | com.android.systemui
u:r:system_app:s0 | system | 1262 | 512 | com.wiite.WiiteCommon | com.wiite.WiiteCommon
u:r:webview_zygote:s0 | webview_zygote | 1330 | 513 | webview_zygote | webview_zygote
u:r:network_stack:s0:c49,c260,c512,c768 | network_stack | 1376 | 512 | com.android.networkstack.process | com.android.networkstack.process
u:r:radio:s0 | radio | 1587 | 512 | com.android.phone | com.android.phone
u:r:priv_app:s0:c512,c768 | u0_a13 | 1639 | 512 | com.google.android.ext.services | com.google.android.ext.services
u:r:batterywarning:s0 | system | 1747 | 1 | batterywarning | batterywarning
u:r:kernel:s0 | root | 1755 | 2 | [main_thread] | [main_thread]
u:r:kernel:s0 | root | 1756 | 2 | [hif_thread] | [hif_thread]
u:r:kernel:s0 | root | 1757 | 2 | [rx_thread] | [rx_thread]
u:r:adbd:s0 | shell | 1760 | 1 | adbd | adbd --root_seclabel=u:r:su:s0
u:r:priv_app:s0:c512,c768 | u0_a38 | 1855 | 512 | com.google.android.googlequicksearchbox:interactor | com.google.android.googlequicksearchbox:interactor
u:r:system_app:s0 | system | 1881 | 512 | com.wiite.devicedaemon | com.wiite.devicedaemon
u:r:platform_app:s0:c512,c768 | u0_a41 | 1906 | 512 | com.adups.fota | com.adups.fota
u:r:system_app:s0 | system | 1929 | 512 | com.wiite.cleantask | com.wiite.cleantask
u:r:secure_element:s0:c44,c260,c512,c768 | secure_element | 1940 | 512 | com.android.se | com.android.se
u:r:radio:s0 | radio | 2084 | 512 | com.mediatek.ims | com.mediatek.ims
u:r:system_app:s0 | system | 2144 | 512 | com.wiite.devicedaemon:communication | com.wiite.devicedaemon:communication
u:r:priv_app:s0:c512,c768 | u0_a46 | 2229 | 512 | com.google.process.gservices | com.google.process.gservices
u:r:priv_app:s0:c512,c768 | u0_a38 | 2494 | 512 | com.google.android.googlequicksearchbox:search | com.google.android.googlequicksearchbox:search
u:r:system_app:s0 | system | 2519 | 512 | com.wiite.wearhealthuart | com.wiite.wearhealthuart
u:r:system_app:s0 | system | 2532 | 512 | com.wiite.wiite_heartrate | com.wiite.wiite_heartrate
u:r:system_app:s0 | system | 2570 | 512 | com.wiite.wiite_sleep | com.wiite.wiite_sleep
u:r:system_app:s0 | system | 2584 | 512 | com.wiite.wiite_bloodoxygen | com.wiite.wiite_bloodoxygen
u:r:system_app:s0 | system | 3850 | 512 | com.wiite.power | com.wiite.power
u:r:system_app:s0 | system | 3881 | 512 | com.wiite.wiitestore | com.wiite.wiitestore
u:r:priv_app:s0:c512,c768 | u0_a46 | 4426 | 512 | com.google.process.gapps | com.google.process.gapps
u:r:kernel:s0 | root | 5406 | 2 | [fs_suspend_syss] | [fs_suspend_syss]
u:r:kernel:s0 | root | 5407 | 2 | [kworker/2:0] | [kworker/2:0]
u:r:kernel:s0 | root | 5726 | 2 | [kworker/3:0] | [kworker/3:0]
u:r:kernel:s0 | root | 5967 | 2 | [kworker/7:3] | [kworker/7:3]
u:r:kernel:s0 | root | 5970 | 2 | [kworker/2:3] | [kworker/2:3]
u:r:kernel:s0 | root | 6122 | 2 | [kworker/1:0] | [kworker/1:0]
u:r:kernel:s0 | root | 10399 | 2 | [kworker/u16:2] | [kworker/u16:2]
u:r:mediaprovider:s0:c512,c768 | u0_a18 | 12485 | 512 | android.process.media | android.process.media
u:r:untrusted_app:s0:c60,c256,c512,c768 | u0_a60 | 13148 | 512 | com.teslacoilsw.launcher | com.teslacoilsw.launcher
u:r:clatd:s0 | clat | 13240 | 511 | clatd-ccmni0 | clatd-ccmni0 -i ccmni0 -n 104 -m 0xf0068 -p 2607:7700:0:16:0:2:: -4 192.0.0.4 -6 2607:fb90:392b:4cdc:cf7d:7d18:947:2568 -t 59
u:r:kernel:s0 | root | 13604 | 2 | [kworker/0:2] | [kworker/0:2]
u:r:priv_app:s0:c512,c768 | u0_a38 | 13632 | 512 | com.google.android.googlequicksearchbox:googleapp | com.google.android.googlequicksearchbox:googleapp
u:r:kernel:s0 | root | 13839 | 2 | [kworker/7:0] | [kworker/7:0]
u:r:kernel:s0 | root | 13859 | 2 | [kworker/u16:9] | [kworker/u16:9]
u:r:kernel:s0 | root | 13951 | 2 | [kworker/4:0] | [kworker/4:0]
u:r:untrusted_app:s0:c55,c256,c512,c768 | u0_a55 | 14029 | 512 | com.google.android.apps.maps | com.google.android.apps.maps
u:r:kernel:s0 | root | 14048 | 2 | [kworker/3:1] | [kworker/3:1]
u:r:kernel:s0 | root | 14143 | 2 | [kworker/6:3] | [kworker/6:3]
u:r:priv_app:s0:c512,c768 | u0_a46 | 14145 | 512 | com.google.android.gms | com.google.android.gms
u:r:kernel:s0 | root | 14178 | 2 | [kworker/0:1] | [kworker/0:1]
u:r:priv_app:s0:c512,c768 | u0_a44 | 14186 | 512 | com.android.vending:instant_app_installer | com.android.vending:instant_app_installer
u:r:priv_app:s0:c512,c768 | u0_a44 | 14544 | 512 | com.android.vending:background | com.android.vending:background
u:r:kernel:s0 | root | 14708 | 2 | [kworker/1:1] | [kworker/1:1]
u:r:priv_app:s0:c512,c768 | u0_a46 | 14709 | 512 | com.google.android.gms.persistent | com.google.android.gms.persistent
u:r:mdnsd:s0 | mdnsr | 14825 | 1 | mdnsd | mdnsd
u:r:kernel:s0 | root | 15477 | 2 | [kworker/5:2] | [kworker/5:2]
u:r:kernel:s0 | root | 15499 | 2 | [kworker/6:0] | [kworker/6:0]
u:r:untrusted_app:s0:c56,c256,c512,c768 | u0_a56 | 15574 | 512 | com.topjohnwu.magisk | com.topjohnwu.magisk
u:r:untrusted_app:s0:c56,c256,c512,c768 | u0_a56 | 15606 | 15574 | su | su --mount-master
u:r:magisk:s0 | root | 15611 | 466 | libbusybox.so | libbusybox.so sh
u:r:magisk:s0 | root | 15617 | 15611 | com.topjohnwu.magisk:root:0 | com.topjohnwu.magisk:root:0
u:r:kernel:s0 | root | 16045 | 2 | [kworker/u17:2] | [kworker/u17:2]
u:r:kernel:s0 | root | 16069 | 2 | [kworker/u16:4] | [kworker/u16:4]
u:r:kernel:s0 | root | 16071 | 2 | [kworker/u16:6] | [kworker/u16:6]
u:r:kernel:s0 | root | 16088 | 2 | [kworker/4:2] | [kworker/4:2]
u:r:kernel:s0 | root | 16094 | 2 | [kworker/5:0] | [kworker/5:0]
u:r:priv_app:s0:c512,c768 | u0_a46 | 16243 | 512 | com.google.android.gms.unstable | com.google.android.gms.unstable
u:r:kernel:s0 | root | 16330 | 2 | [kworker/u17:0] | [kworker/u17:0]
u:r:kernel:s0 | root | 16370 | 2 | [kworker/u16:0] | [kworker/u16:0]
u:r:kernel:s0 | root | 16380 | 2 | [kworker/u17:1] | [kworker/u17:1]
u:r:system_app:s0 | system | 16382 | 512 | com.android.settings | com.android.settings
u:r:kernel:s0 | root | 16413 | 2 | [kworker/4:1] | [kworker/4:1]
u:r:shell:s0 | shell | 16448 | 1760 | sh | sh -c su -c "echo === SECTION01 RUNTIME SNAPSHOT ===; {?echo === GETPROP_INIT_SVC ===;?getprop | grep -E \"^\\[init\\.svc|^\\[ro\\.vendor\\.rc\\]|^\\[ro\\.hardware\\]|^\\[ro\\.boot\\.hardware\\]|^\\[ro\\.board\\.platform\\]|^\\[ro\\.vendor\\.mediatek\\.platform\\]|^\\[ro\\.build\\.type\\]|^\\[ro\\.boot\\.atm\\]|^\\[persist\\.vendor|^\\[vendor\\.\" | sort;?echo === PS_FULL ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z;?echo === SERVICE_LIST ===;?service list;?echo === LSOF_TTYS1 ===;?toybox lsof /dev/ttyS1 2>/dev/null;?echo === WIITE_CTRL_LS ===;?ls -laZ /sys/devices/virtual/misc/wiite_corp_ctrl /sys/devices/virtual/misc/wiite_con_ctrl 2>&1;?echo === TTYS1_STATE ===;?ls -laZ /dev/ttyS1 /sys/class/tty/ttyS1 /sys/dev/char/4:65 2>&1;?readlink -f /sys/class/tty/ttyS1 2>&1;?cat /sys/class/tty/ttyS1/uevent 2>&1;?} > /data/local/tmp/section01_runtime_snapshot.txt; sha256sum /data/local/tmp/section01_runtime_snapshot.txt; ls -l /data/local/tmp/section01_runtime_snapshot.txt"
u:r:shell:s0 | shell | 16450 | 16448 | su | su -c echo === SECTION01 RUNTIME SNAPSHOT ===; {?echo === GETPROP_INIT_SVC ===;?getprop | grep -E "^\[init\.svc|^\[ro\.vendor\.rc\]|^\[ro\.hardware\]|^\[ro\.boot\.hardware\]|^\[ro\.board\.platform\]|^\[ro\.vendor\.mediatek\.platform\]|^\[ro\.build\.type\]|^\[ro\.boot\.atm\]|^\[persist\.vendor|^\[vendor\." | sort;?echo === PS_FULL ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z;?echo === SERVICE_LIST ===;?service list;?echo === LSOF_TTYS1 ===;?toybox lsof /dev/ttyS1 2>/dev/null;?echo === WIITE_CTRL_LS ===;?ls -laZ /sys/devices/virtual/misc/wiite_corp_ctrl /sys/devices/virtual/misc/wiite_con_ctrl 2>&1;?echo === TTYS1_STATE ===;?ls -laZ /dev/ttyS1 /sys/class/tty/ttyS1 /sys/dev/char/4:65 2>&1;?readlink -f /sys/class/tty/ttyS1 2>&1;?cat /sys/class/tty/ttyS1/uevent 2>&1;?} > /data/local/tmp/section01_runtime_snapshot.txt; sha256sum /data/local/tmp/section01_runtime_snapshot.txt; ls -l /data/local/tmp/section01_runtime_snapshot.txt
u:r:magisk:s0 | root | 16452 | 1 | magiskd | magiskd
u:r:magisk:s0 | root | 16453 | 16452 | app_process | app_process /system/bin com.android.commands.content.Content call --uri content://com.topjohnwu.magisk.provider --user 0 --method log --extra from.uid:i:2000 --extra to.uid:i:0 --extra pid:i:16450 --extra policy:i:2 --extra target:i:-1 --extra context:s: --extra gids:s: --extra command:s:echo === SECTION01 RUNTIME SNAPSHOT ===; {?echo === GETPROP_INIT_SVC ===;?getprop | grep -E "^\[init\.svc|^\[ro\.vendor\.rc\]|^\[ro\.hardware\]|^\[ro\.boot\.hardware\]|^\[ro\.board\.platform\]|^\[ro\.vendor\.mediatek\.platform\]|^\[ro\.build\.type\]|^\[ro\.boot\.atm\]|^\[persist\.vendor|^\[vendor\." | sort;?echo === PS_FULL ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z;?echo === SERVICE_LIST ===;?service list;?echo === LSOF_TTYS1 ===;?toybox lsof /dev/ttyS1 2>/dev/null;?echo === WIITE_CTRL_LS ===;?ls -laZ /sys/devices/virtual/misc/wiite_corp_ctrl /sys/devices/virtual/misc/wiite_con_ctrl 2>&1;?echo === TTYS1_STATE ===;?ls -laZ /dev/ttyS1 /sys/class/tty/ttyS1 /sys/dev/char/4:65 2>&1;?readlink -f /sys/class/tty/ttyS1 2>&1;?cat /sys/class/tty/ttyS1/uevent 2>&1;?} > /data/local/tmp/section01_runtime_snapshot.txt; sha256sum /data/local/tmp/section01_runtime_snapshot.txt; ls -l /data/local/tmp/section01_runtime_snapshot.txt --extra notify:b:true
u:r:magisk:s0 | root | 16454 | 466 | sh | sh -c echo === SECTION01 RUNTIME SNAPSHOT ===; {?echo === GETPROP_INIT_SVC ===;?getprop | grep -E "^\[init\.svc|^\[ro\.vendor\.rc\]|^\[ro\.hardware\]|^\[ro\.boot\.hardware\]|^\[ro\.board\.platform\]|^\[ro\.vendor\.mediatek\.platform\]|^\[ro\.build\.type\]|^\[ro\.boot\.atm\]|^\[persist\.vendor|^\[vendor\." | sort;?echo === PS_FULL ===;?ps -A -o USER,PID,PPID,NAME,ARGS -Z;?echo === SERVICE_LIST ===;?service list;?echo === LSOF_TTYS1 ===;?toybox lsof /dev/ttyS1 2>/dev/null;?echo === WIITE_CTRL_LS ===;?ls -laZ /sys/devices/virtual/misc/wiite_corp_ctrl /sys/devices/virtual/misc/wiite_con_ctrl 2>&1;?echo === TTYS1_STATE ===;?ls -laZ /dev/ttyS1 /sys/class/tty/ttyS1 /sys/dev/char/4:65 2>&1;?readlink -f /sys/class/tty/ttyS1 2>&1;?cat /sys/class/tty/ttyS1/uevent 2>&1;?} > /data/local/tmp/section01_runtime_snapshot.txt; sha256sum /data/local/tmp/section01_runtime_snapshot.txt; ls -l /data/local/tmp/section01_runtime_snapshot.txt
u:r:magisk:s0 | root | 16458 | 16454 | ps | ps -A -o USER,PID,PPID,NAME,ARGS -Z
```

################################################################################
# APPENDIX C — BINDER SERVICE LIST SNAPSHOT
################################################################################

```text
Found 180 services:
0	capctrl: [com.mediatek.capctrl.aidl.IMtkCapCtrl]
1	gsiservice: []
2	mtkIms: [com.mediatek.ims.internal.IMtkImsService]
3	mwis: [com.mediatek.wfo.IMwiService]
4	secure_element: [android.se.omapi.ISecureElementService]
5	carrier_config: [com.android.internal.telephony.ICarrierConfigLoader]
6	phoneEx: [com.mediatek.internal.telephony.IMtkTelephonyEx]
7	phone: [com.android.internal.telephony.ITelephony]
8	imtksms: [com.mediatek.internal.telephony.IMtkSms]
9	iphonesubinfoEx: [com.mediatek.internal.telephony.IMtkPhoneSubInfoEx]
10	mtksimphonebook: [com.mediatek.internal.telephony.phb.IMtkIccPhoneBook]
11	isms: [com.android.internal.telephony.ISms]
12	iphonesubinfo: [com.android.internal.telephony.IPhoneSubInfo]
13	simphonebook: [com.android.internal.telephony.IIccPhoneBook]
14	ircs: [android.telephony.ims.aidl.IRcs]
15	telecom: [com.android.internal.telecom.ITelecomService]
16	isubstub: [com.mediatek.internal.telephony.IMtkSub]
17	network_stack: [android.net.INetworkStackConnector]
18	isub: [com.android.internal.telephony.ISub]
19	telephony.mtkregistry: [com.mediatek.internal.telephony.IMtkTelephonyRegistryEx]
20	mtk_telecom: [com.mediatek.internal.telecom.IMtkTelecomService]
21	contexthub: [android.hardware.location.IContextHubService]
22	mtkconnmetrics: [com.mediatek.net.connectivity.IMtkIpConnectivityMetrics]
23	netd_listener: [android.net.metrics.INetdEventListener]
24	connmetrics: [android.net.IIpConnectivityMetrics]
25	bluetooth_manager: [android.bluetooth.IBluetoothManager]
26	power_hal_mgr_service: [com.mediatek.powerhalmgr.IPowerHalMgr]
27	search_engine_service: [com.mediatek.search.ISearchEngineManagerService]
28	app_binding: []
29	clipboard: [android.content.IClipboard]
30	autofill: [android.view.autofill.IAutoFillManager]
31	imms: [com.android.internal.telephony.IMms]
32	incidentcompanion: [android.os.IIncidentCompanion]
33	statscompanion: [android.os.IStatsCompanionService]
34	media.camera.proxy: [android.hardware.ICameraServiceProxy]
35	slice: [android.app.slice.ISliceManager]
36	media_projection: [android.media.projection.IMediaProjectionManager]
37	crossprofileapps: [android.content.pm.ICrossProfileApps]
38	launcherapps: [android.content.pm.ILauncherApps]
39	shortcut: [android.content.pm.IShortcutService]
40	media_router: [android.media.IMediaRouterService]
41	media_resource_monitor: [android.media.IMediaResourceMonitor]
42	media_session: [android.media.session.ISessionManager]
43	restrictions: [android.content.IRestrictionsManager]
44	companiondevice: [android.companion.ICompanionDeviceManager]
45	print: [android.print.IPrintManager]
46	graphicsstats: [android.view.IGraphicsStats]
47	dreams: [android.service.dreams.IDreamManager]
48	network_time_update_service: []
49	runtime: []
50	diskstats: []
51	voiceinteraction: [com.android.internal.app.IVoiceInteractionManagerService]
52	role: [android.app.role.IRoleManager]
53	appwidget: [com.android.internal.appwidget.IAppWidgetService]
54	backup: [android.app.backup.IBackupManager]
55	trust: [android.app.trust.ITrustManager]
56	soundtrigger: [com.android.internal.app.ISoundTriggerService]
57	jobscheduler: [android.app.job.IJobScheduler]
58	color_display: [android.hardware.display.IColorDisplayManager]
59	hardware_properties: [android.os.IHardwarePropertiesManager]
60	serial: [android.hardware.ISerialManager]
61	usb: [android.hardware.usb.IUsbManager]
62	adb: [android.debug.IAdbManager]
63	midi: [android.media.midi.IMidiManager]
64	DockObserver: []
65	audio: [android.media.IAudioService]
66	wallpaper: [android.app.IWallpaperManager]
67	search: [android.app.ISearchManager]
68	time_detector: [android.app.timedetector.ITimeDetectorService]
69	country_detector: [android.location.ICountryDetector]
70	location: [android.location.ILocationManager]
71	devicestoragemonitor: []
72	notification: [android.app.INotificationManager]
73	updatelock: [android.os.IUpdateLock]
74	system_update: [android.os.ISystemUpdateManager]
75	servicediscovery: [android.net.nsd.INsdManager]
76	connectivity: [android.net.IConnectivityManager]
77	ethernet: [android.net.IEthernetManager]
78	wifip2p: [android.net.wifi.p2p.IWifiP2pManager]
79	wifiscanner: [android.net.wifi.IWifiScanner]
80	wifi: [android.net.wifi.IWifiManager]
81	persistent_data_block: [android.service.persistentdata.IPersistentDataBlockService]
82	netpolicy: [android.net.INetworkPolicyManager]
83	netstats: [android.net.INetworkStatsService]
84	network_score: [android.net.INetworkScoreService]
85	textclassification: [android.service.textclassifier.ITextClassifierService]
86	textservices: [com.android.internal.textservice.ITextServicesManager]
87	ipsec: [android.net.IIpSecService]
88	network_management: [android.os.INetworkManagementService]
89	content_suggestions: [android.app.contentsuggestions.IContentSuggestionsManager]
90	app_prediction: [android.app.prediction.IPredictionManager]
91	statusbar: [com.android.internal.statusbar.IStatusBarService]
92	device_policy: [android.app.admin.IDevicePolicyManager]
93	deviceidle: [android.os.IDeviceIdleController]
94	oem_lock: [android.service.oemlock.IOemLockService]
95	testharness: []
96	lock_settings: [com.android.internal.widget.ILockSettings]
97	uimode: [android.app.IUiModeManager]
98	storagestats: [android.app.usage.IStorageStatsManager]
99	mount: [android.os.storage.IStorageManager]
100	accessibility: [android.view.accessibility.IAccessibilityManager]
101	input_method: [com.android.internal.view.IInputMethodManager]
102	pinner: []
103	network_watchlist: [com.android.internal.net.INetworkWatchlistManager]
104	input: [android.hardware.input.IInputManager]
105	window: [android.view.IWindowManager]
106	inputflinger: [android.input.IInputFlinger]
107	alarm: [android.app.IAlarmManager]
108	dynamic_system: [android.os.image.IDynamicSystemService]
109	vibrator: [android.os.IVibratorService]
110	external_vibrator_service: [android.os.IExternalVibratorService]
111	dropbox: [com.android.internal.os.IDropBoxManagerService]
112	device_config: []
113	settings: []
114	content: [android.content.IContentService]
115	account: [android.accounts.IAccountManager]
116	telephony.registry: [com.android.internal.telephony.ITelephonyRegistry]
117	scheduling_policy: [android.os.ISchedulingPolicyService]
118	sec_key_att_app_id_provider: [android.security.keymaster.IKeyAttestationApplicationIdProvider]
119	bugreport: [android.os.IDumpstate]
120	rollback: [android.content.rollback.IRollbackManager]
121	looper_stats: []
122	binder_calls_stats: []
123	webviewupdate: [android.webkit.IWebViewUpdateService]
124	sensorservice: [android.gui.SensorServer]
125	usagestats: [android.app.usage.IUsageStatsManager]
126	media.sound_trigger_hw: [android.hardware.ISoundTriggerHwService]
127	batteryproperties: [android.os.IBatteryPropertiesRegistrar]
128	media.audio_policy: [android.media.IAudioPolicyService]
129	battery: []
130	media.camera: [android.hardware.ICameraService]
131	sensor_privacy: [android.hardware.ISensorPrivacyManager]
132	overlay: [android.content.om.IOverlayManager]
133	anrmanager: [android.app.IAnrManager]
134	processinfo: [android.os.IProcessInfoService]
135	permission: [android.os.IPermissionController]
136	cpuinfo: []
137	dbinfo: []
138	gfxinfo: []
139	meminfo: []
140	procstats: [com.android.internal.app.procstats.IProcessStats]
141	activity: [android.app.IActivityManager]
142	user: [android.os.IUserManager]
143	otadexopt: [android.content.pm.IOtaDexopt]
144	package_native: [android.content.pm.IPackageManagerNative]
145	package: [android.content.pm.IPackageManager]
146	display: [android.hardware.display.IDisplayManager]
147	recovery: [android.os.IRecoverySystem]
148	thermalservice: [android.os.IThermalService]
149	power: [android.os.IPowerManager]
150	appops: [com.android.internal.app.IAppOpsService]
151	batterystats: [com.android.internal.app.IBatteryStats]
152	activity_task: [android.app.IActivityTaskManager]
153	uri_grants: [android.app.IUriGrantsManager]
154	device_identifiers: [android.os.IDeviceIdentifiersPolicyService]
155	media.VTS: [android.hardware.IVTService]
156	netd: [android.net.INetd]
157	dnsresolver: [android.net.IDnsResolver]
158	media.extractor: [android.media.IMediaExtractorService]
159	media.resource_manager: [android.media.IResourceManagerService]
160	media.player: [android.media.IMediaPlayerService]
161	storaged_pri: [android.os.storaged.IStoragedPrivate]
162	SurfaceFlinger: [android.ui.ISurfaceComposer]
163	storaged: [android.os.IStoraged]
164	drm.drmManager: [drm.IDrmManagerService]
165	media.metrics: [android.media.IMediaAnalyticsService]
166	android.service.gatekeeper.IGateKeeperService: [android.service.gatekeeper.IGateKeeperService]
167	stats: [android.os.IStatsManager]
168	idmap: []
169	android.security.keystore: [android.security.keystore.IKeystoreService]
170	wificond: [android.net.wifi.IWificond]
171	media.drm: [android.media.IMediaDrmService]
172	installd: [android.os.IInstalld]
173	incident: [android.os.IIncidentManager]
174	media.audio_flinger: [android.media.IAudioFlinger]
175	gpu: [android.graphicsenv.IGpuService]
176	ashmem_device_service: [android.ashmemd.IAshmemDeviceService]
177	apexservice: [android.apex.IApexService]
178	suspend_control: [android.system.suspend.ISuspendControlService]
179	vold: [android.os.IVold]
```

