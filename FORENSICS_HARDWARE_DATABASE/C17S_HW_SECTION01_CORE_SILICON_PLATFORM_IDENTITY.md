################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 1 — CORE SILICON & PLATFORM IDENTITY
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 1 — CORE SILICON / PLATFORM IDENTITY ONLY
#
# SCOPE:
# This section defines the immutable root identity of the C17S platform:
# physical SoC, board/product identity, CPU architecture, base memory identity,
# core SoC register map, kernel platform identity, and firmware execution-domain
# identity at the summary level.
#
# Full storage geometry, partition maps, boot-image field decode, DTBO fragment
# decode, I2C/GPIO peripheral topology, power rails, security substrate, GPU
# runtime stack, modem/radio, sensors, and negative/BSP ghosts belong to late
# sections of the hardware digital twin.
#
# SOURCE AUTHORITY:
# TIER 1 : Live physical device evidence
# TIER 2 : BROM-level extraction and raw binary decode from this exact device
# TIER 3 : Working boot/decrypt/build behavior on this exact device
# TIER 4 : APLog, bootprof, pl_lk, scp_log, atf_log, kernel_log, and closed
# documents as evidence records only
# FINAL  : Beright / Albert acceptance
#################################################################################
# 1.1 — DEVICE, PRODUCT & BOARD IDENTITY
################################################################################

Brand / commercial identity:
Commercial brand              : LOKMAT
Commercial model              : APPLLP 5 MAX
Device family                 : C17S
Board name                    : WP_C17S_PIX_TFT_D4
Product name                  : full_WP_C17S_PIX_TFT_D4

Live Android product identity:
ro.product.board              : WP_C17S_PIX_TFT_D4
ro.product.device             : WP_C17S_PIX_TFT_D4
ro.product.name               : full_WP_C17S_PIX_TFT_D4
ro.product.model              : LOKMAT APPLLP 5 MAX
ro.product.brand              : SPRD
ro.product.manufacturer       : SPRD

Resolved product-source order:
ro.product.property_source_order : odm,vendor,product,system

Layered Android product identity:
ro.product.system.brand        : SPRD
ro.product.system.device       : WP_C17S_PIX_TFT_D4
ro.product.system.manufacturer : SPRD
ro.product.system.model        : LOKMAT APPLLP 5 MAX
ro.product.system.name         : full_WP_C17S_PIX_TFT_D4

ro.product.product.brand       : SPRD
ro.product.product.device      : WP_C17S_PIX_TFT_D4
ro.product.product.manufacturer: SPRD
ro.product.product.model       : LOKMAT APPLLP 5 MAX
ro.product.product.name        : full_WP_C17S_PIX_TFT_D4

ro.product.vendor.brand        : SPRD
ro.product.vendor.device       : WP_C17S_PIX_TFT_D4
ro.product.vendor.manufacturer : SPRD
ro.product.vendor.model        : LOKMAT APPLLP 5 MAX
ro.product.vendor.name         : full_WP_C17S_PIX_TFT_D4

ro.product.odm.brand           : SPRD
ro.product.odm.device          : WP_C17S_PIX_TFT_D4
ro.product.odm.manufacturer    : SPRD
ro.product.odm.model           : LOKMAT APPLLP 5 MAX
ro.product.odm.name            : full_WP_C17S_PIX_TFT_D4

Build target aliases:
ro.build.product               : WP_C17S_PIX_TFT_D4
ro.build.flavor                : full_WP_C17S_PIX_TFT_D4-user

ODM / platform lineage:
ODM family                     : Topwise / Linswear / Wiite
ODM identity string            : wiiteer6762_10.0
FOTA OEM platform              : MTK6762_10.0
MTK branch                     : alps-mp-q0.mp1
MTK release                    : alps-mp-q0.mp1-V9.48
Kernel source baseline         : alps/kernel-4.9

Project configuration identity:
NRF_DEVICE                     : C17S
PRODUCT_MODEL_CUSTOM           : LOKMAT APPLLP 5 MAX
PRODUCT_WIFI_NAME              : APPLLP_5_MAX
PRODUCT_BT_NAME                : APPLLP_5_MAX
CUSTOM_BUILD_VERNO             : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613
LINUX_KERNEL_VERSION           : kernel-4.9
MTK_PLATFORM                   : MT6765

Build/product fingerprint identity:
Android release                : 10
API level                      : 29
Build ID                       : QP1A.190711.020
Build variant                  : user
Signing state                  : test-keys
Build timestamp                : 1749799430
Build display ID               : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613
Build fingerprint              : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749799430:user/test-keys
Build description              : full_WP_C17S_PIX_TFT_D4-user 10 QP1A.190711.020 1749799430 test-keys

Layered build timestamp / fingerprint identity:
Primary build layer            : system / product / odm / ro.build
Primary build date             : Fri Jun 13 07:23:14 UTC 2025
Primary build date.utc         : 1749799394
Primary build incremental      : 1749799430
Primary build fingerprint      : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749799430:user/test-keys

Vendor build layer             : vendor / bootimage
Vendor build date              : Fri Jun 13 08:11:06 UTC 2025
Vendor build date.utc          : 1749802266
Vendor build incremental       : 1749802324
Vendor build fingerprint       : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749802324:user/test-keys
Bootimage build date           : Fri Jun 13 08:11:06 UTC 2025
Bootimage build date.utc       : 1749802266
Bootimage build fingerprint    : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749802324:user/test-keys

Static property source files:
system build.prop              : /system/build.prop
system build.prop sha256       : 4a42eee761f91257f0ff61ba54aad7d37822a6a9e69167fee788f42982e27018
product build.prop             : /product/build.prop
product build.prop sha256      : 7d4528245ef134bedc2b318e60268c72d82b28927882228d6c0d3e899f65218b
odm build.prop                 : /vendor/odm/etc/build.prop
odm build.prop sha256          : 87a67e1288a5458d1f9e31a20e9715414a5d2f13d320e21624c5e548805a5d5f
vendor build.prop              : /vendor/build.prop
vendor build.prop sha256       : fbfa2961ae9dc4acfcd8d1b2594c76f93ae43c7e282a9de587c6876c4da6911e

Static project configuration source:
ProjectConfig path             : /vendor/data/misc/ProjectConfig.mk
ProjectConfig size             : 22,032 bytes
ProjectConfig lines            : 564
ProjectConfig sha256           : a94dede9f36229b9022ea8ed5fa3ccc7914becc924b65f0c8edc9bd530b53b15

Identity-layer note:
The primary build identity and the vendor / bootimage build identity are both
real exposed layers on the device. The primary system/product/odm build layer
uses incremental 1749799430. The vendor and bootimage layer uses incremental
1749802324.These values must not be collapsed into a single timestamp.



################################################################################
# 1.2 — PHYSICAL SILICON IDENTITY
################################################################################

Primary application processor:
Physical SoC                  : MediaTek MT6762V/WB
SoC family                    : MT6762
Market family                 : Helio P22 WB-class silicon
Physical proof class          : BROM / Preloader target-info / live kernel silicon register evidence
Software-facing BSP layer     : MT6765 identity belongs to software/platform layer, not physical SoC identity

Live CPU silicon presentation:
Source path                   : /proc/cpuinfo
Hardware field                : MT6762V/WB
CPU count observed            : 8
CPU implementer               : 0x41
CPU architecture              : 8
CPU variant                   : 0x0
CPU part                      : 0xd03
CPU revision                  : 4
Classification                : LIVE_PROVEN_PHYSICAL_SOC_PRESENTATION

BROM-level hardware identifiers:
HW Code                       : 0x766
HW Subcode                    : 0x8a00
HW Version                    : 0xca00
ME_ID                         : 4B5CF18EF37D514A6256199E0BBD5E6A
SOC_ID                        : 6044C8E6000D6C384827B28853120093E04F3C815E8A50E6D20D6A6C24DF1B11
ME_ID source class            : BROM / Preloader silicon acquisition identity
SOC_ID source class           : BROM / Preloader silicon acquisition identity
Linux named ME_ID export      : not exposed in searched live /proc or /sys names
Linux named SOC_ID export     : not exposed in searched live /proc or /sys names
Classification                : STATIC_SILICON_ACQUISITION_IDENTITY

Live /proc/chip silicon register identity:
Source path                   : /proc/chip
SELinux context               : u:object_r:proc_chip:s0
hw_code                       : 0766
hw_subcode                    : 8A00
hw_ver                        : CA00
sw_ver                        : 0000
code_func                     : 0001
code_date_raw                 : 004A
code_date_decoded             : 201310
code_proj                     : 0000
code_fab                      : 0002
reg                           : 0766 8A00 CA00 0000
Classification                : LIVE_PROVEN_SILICON_REGISTER_IDENTITY

Live chipid platform declaration:
Live node                     : /sys/devices/platform/8000000.chipid
Live node context             : u:object_r:sysfs:s0
Driver link                   : NO_DRIVER_LINK
OF node link                  : ../../../firmware/devicetree/base/chipid@08000000
OF_NAME                       : chipid
OF_FULLNAME                   : /chipid@08000000
OF_COMPATIBLE_0               : mediatek,chipid
MODALIAS                      : of:NchipidT<NULL>Cmediatek,chipid
Classification                : LIVE_PROVEN_CHIPID_PLATFORM_DECLARATION

Live chipid devicetree register declaration:
DT node                       : /sys/firmware/devicetree/base/chipid@08000000
DT node context               : u:object_r:sysfs:s0
name                          : chipid<NUL>
compatible                    : mediatek,chipid<NUL>
#address-cells                : 00000002
#size-cells                   : 00000002
reg[0] address                : 0x0000000008000000
reg[0] size                   : 0x0000000000000004
reg[1] address                : 0x0000000008000004
reg[1] size                   : 0x0000000000000004
reg[2] address                : 0x0000000008000008
reg[2] size                   : 0x0000000000000004
reg[3] address                : 0x000000000800000c
reg[3] size                   : 0x0000000000000004
Classification                : LIVE_PROVEN_CHIPID_REGISTER_DECLARATION

Storage identity coupled to silicon extraction:
Block device                  : /sys/block/mmcblk0
Device link                   : /sys/block/mmcblk0/device -> ../../../mmc0:0001
Boot partitions               : /sys/block/mmcblk0boot0, /sys/block/mmcblk0boot1
RPMB partition                : /sys/block/mmcblk0rpmb
Storage type                  : MMC
Storage name                  : 3V6CMB
eMMC CID                      : 150100335636434d42036c4bd6326559
eMMC CSD                      : d02701320f5903fff6dbffef8e40400d
CID source path               : /sys/block/mmcblk0/device/cid
CID file mode                 : -r--r--r--
CID file owner                : root:root
CID file size                 : 4096
Classification                : LIVE_PROVEN_STORAGE_IDENTITY

Preloader / SegCode silicon register identity:
FAB0 Register                 : 0x6762
FAB_CODE                      : 0x2
Segment Code                  : 0x8
PROJECT_CODE                  : 0x0
RW_STA                        : 0x0
CTL                           : 0x0
DCM                           : 0x4
DATE_CODE_YY                  : 0x1
DATE_CODE_WW                  : 0xA
HT22 Data                     : 2, 0x1, 0x1 done
Classification                : STATIC_PRELOADER_SEGCODE_SILICON_IDENTITY

Silicon / security register declaration surfaces:
Node                          : /sys/devices/platform/10004000.efuse_dbg
DT node                       : /sys/firmware/devicetree/base/efuse_dbg@10004000
Compatible                    : mediatek,efuse_dbg
Reg base                      : 0x10004000
Reg size                      : 0x00001000
Driver link                   : NO_DRIVER_LINK
Classification                : LIVE_PROVEN_EFUSE_DEBUG_REGISTER_DECLARATION

Node                          : /sys/devices/platform/11c50000.efusec
DT node                       : /sys/firmware/devicetree/base/efusec@11c50000
Compatible                    : mediatek,efusec
Reg base                      : 0x11c50000
Reg size                      : 0x00010000
Driver link                   : NO_DRIVER_LINK
Classification                : LIVE_PROVEN_EFUSE_CONTROLLER_REGISTER_DECLARATION

Node                          : /sys/devices/platform/1000a000.hacc
DT node                       : /sys/firmware/devicetree/base/hacc@1000a000
Compatible                    : mediatek,hacc
Reg base                      : 0x1000a000
Reg size                      : 0x00001000
Interrupt cells               : 00000000 000000bd 00000008
Driver                        : masp
Driver link                   : ../../../bus/platform/drivers/masp
Classification                : LIVE_PROVEN_BOUND_HACC_SECURITY_BLOCK

Node                          : /sys/devices/platform/10210000.dxcc_sec
DT node                       : /sys/firmware/devicetree/base/dxcc_sec@10210000
Compatible                    : mediatek,dxcc_sec
Reg base                      : 0x10210000
Reg size                      : 0x00001000
Interrupt cells               : 00000000 000000a2 00000004
Driver link                   : NO_DRIVER_LINK
Classification                : LIVE_PROVEN_DXCC_SECURITY_BLOCK_DECLARATION

Section 1.2 cross-section anomaly leads:
Anomaly label                 : WLAN_EFUSE_PROC_INTERFACE
Primary section               : Section 9 — Baseband, Radio, RF & Connectivity
Canonical path                : /proc/net/wlan/efuse_dump
Projected paths               : /proc/<PID>/net/wlan/efuse_dump
Canonical inode               : 4026533669
Mode                          : -rw-rw-r--
Owner                         : shell:wifi
SELinux context               : u:object_r:proc_net:s0
Classification                : ANOMALOUS_SECURITY_RELEVANT_CONNECTIVITY_SURFACE
Section 1.2 handling          : cross-reference only
Comment                       : Not a multiplicity anomaly; /proc/<PID>/net projections resolve to the same canonical object.

Anomaly label                 : PMIC_VEFUSE_WRITABLE_SYSFS_STATE
Primary section               : Section 6 — Power Management, Thermal & Charging
Status path                   : /sys/devices/platform/mt-pmic/ldo_vefuse_status
Status value                  : 0
Voltage path                  : /sys/devices/platform/mt-pmic/ldo_vefuse_voltage
Voltage value                 : 1800
DT node                       : /sys/firmware/devicetree/base/mt-pmic/ldo_regulators/ldo_vefuse
Regulator name                : vefuse<NUL>
Regulator min microvolt       : 1200000
Regulator max microvolt       : 3300000
Regulator enable ramp delay   : 264
Phandle                       : 0x0000005e
Classification                : UNRESOLVED_SECURITY_RELEVANT_POWER_SURFACE
Section 1.2 handling          : cross-reference only
Comment                       : eFuse-related PMIC regulator surface exists; primary analysis belongs in Section 6.

Canonical silicon conclusion:
Physical application processor : MT6762V/WB
BROM / preloader identity      : 0x766 / 0x8a00 / 0xca00 with ME_ID and SOC_ID above
Live Linux silicon identity    : MT6762V/WB plus /proc/chip register identity above
Software BSP identity          : MT6765 layer only; not physical SoC identity
Section 1.2 status             : CLOSED_FOR_CURRENT_REVERIFICATION_PASS


################################################################################
# 1.3 — CROSS-LAYER PLATFORM IDENTITY
################################################################################


Canonical platform split:
Physical silicon identity       : MT6762V/WB
Software BSP / HAL identity     : MT6765
Split classification            : REAL_LAYER_SPLIT

Layer rule:
MT6762 / MT6762V/WB identifies the physical silicon, kernel-visible hardware lineage, and ro.hardware runtime layer.
MT6765 / MT6765 identifies the ODM-selected software BSP, HAL ABI surface, fstab template lineage, and vendor platform layer.
Both identities are valid only in their correct layers.


Physical / kernel-visible identity:
/proc/cpuinfo Hardware          : MT6762V/WB
/proc/cpuinfo Processor         : AArch64 Processor rev 4 (aarch64)
uname -a                        : Linux localhost 4.9.190+ #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025 aarch64
/proc/version                   : Linux version 4.9.190+ (nobody@android-build) (Android (5484270 based on r353983c) clang version 9.0.3 (https://android.googlesource.com/toolchain/clang 745b335211bb9eadfa6aa6301f84715cee4b37c5) (https://android.googlesource.com/toolchain/llvm 60cf23e54e46c807513f7a36d0a7b777920b5881) (based on LLVM 9.0.3svn)) #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025
Classification                  : LIVE_KERNEL_HARDWARE_PRESENTATION


Device-tree root identity:
DT primary path                 : /sys/firmware/devicetree/base
DT alias path                   : /proc/device-tree

/sys/firmware/devicetree/base/model:
SELinux context                 : u:object_r:sysfs_device_tree_model:s0
Size                            : 11 bytes
Hex payload                     : 4d 54 36 37 36 32 56 2f 57 42 00
Decoded value                   : MT6762V/WB<NUL>
Classification                  : LIVE_DT_PHYSICAL_MODEL_IDENTITY

/sys/firmware/devicetree/base/compatible:
SELinux context                 : u:object_r:sysfs:s0
Size                            : 16 bytes
Hex payload                     : 6d 65 64 69 61 74 65 6b 2c 4d 54 36 37 36 35 00
Decoded value                   : mediatek,MT6765<NUL>
Classification                  : LIVE_DT_SOFTWARE_COMPATIBLE_IDENTITY

/proc/device-tree/model:
SELinux context                 : u:object_r:sysfs_device_tree_model:s0
Size                            : 11 bytes
Decoded value                   : MT6762V/WB<NUL>
Classification                  : LIVE_DT_ALIAS_PHYSICAL_MODEL_IDENTITY

/proc/device-tree/compatible:
SELinux context                 : u:object_r:sysfs:s0
Size                            : 16 bytes
Decoded value                   : mediatek,MT6765<NUL>
Classification                  : LIVE_DT_ALIAS_SOFTWARE_COMPATIBLE_IDENTITY

DT root scalar fields:
/sys/firmware/devicetree/base/name             : 00 / NUL-only / size 1 / context u:object_r:sysfs:s0
/sys/firmware/devicetree/base/#address-cells   : 00000002 / decoded 2 / size 4 / context u:object_r:sysfs:s0
/sys/firmware/devicetree/base/#size-cells      : 00000002 / decoded 2 / size 4 / context u:object_r:sysfs:s0
/sys/firmware/devicetree/base/interrupt-parent : 00000001 / decoded phandle 1 / size 4 / context u:object_r:sysfs:s0

Device-tree split conclusion:
DT model                         : MT6762V/WB
DT compatible                    : mediatek,MT6765
Classification                   : DEVICE_TREE_LAYER_SPLIT_CONFIRMED


Bootargs / cmdline platform identity:
/proc/cmdline context            : u:object_r:proc_cmdline:s0
/proc/bootconfig                 : ABSENT / No such file or directory
DT chosen bootargs path          : /sys/firmware/devicetree/base/chosen/bootargs
DT chosen bootargs size          : 671 bytes
DT chosen bootargs context       : u:object_r:sysfs:s0
DT chosen bootargs termination   : NUL-terminated / final byte 00

Bootargs / cmdline selected values:
androidboot.hardware             : mt6765
maxcpus                          : 8
loop.max_part                    : 7
firmware_class.path              : /vendor/firmware
androidboot.boot_devices         : bootdevice,11230000.mmc
root                             : /dev/ram
androidboot.verifiedbootstate    : orange
bootopt                          : 64S3,32N2,64N2
buildvariant                     : user
androidboot.atm                  : disabled
androidboot.meta_log_disable     : 0
printk.disable_uart              : 0
androidboot.serialno             : MTK0002507301608400
androidboot.bootreason           : PowerKey
gpt                              : 1
usb2jtag_mode                    : 0
mrdump_ddrsv                     : yes
mrdump_cb                        : 0x10e800,0x1400
androidboot.dtb_idx              : 0
androidboot.dtbo_idx             : 0

Bootargs / runtime property split:
DT chosen bootargs androidboot.hardware : mt6765
/proc/cmdline androidboot.hardware      : mt6765
getprop ro.boot.hardware                : mt6762
getprop ro.hardware                     : mt6762
Classification                          : BOOTARG_RUNTIME_HARDWARE_TOKEN_SPLIT_CONFIRMED


Canonical live/platform properties:
androidboot.hardware             : mt6765
ro.boot.hardware                 : mt6762
ro.hardware                      : mt6762
ro.board.platform                : mt6765
ro.vendor.mediatek.platform      : MT6765
ro.vendor.wlan.gen               : gen4m
ro.fota.oem                      : wiiteer6762_10.0
ro.fota.platform                 : MTK6762_10.0
ro.fota.version                  : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613_20250613-0759
ro.zygote                        : zygote64_32
ro.vndk.version                  : 29

Expanded live cross-layer property namespace:
ro.bionic.2nd_arch               : arm
ro.bionic.2nd_cpu_variant        : cortex-a53
ro.bionic.arch                   : arm64
ro.bionic.cpu_variant            : cortex-a53
ro.board.platform                : mt6765
ro.boot.hardware                 : mt6762
ro.bootimage.build.fingerprint   : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749802324:user/test-keys
ro.fota.app                      : 5
ro.fota.battery                  : 30
ro.fota.device                   : LOKMAT APPLLP 5 MAX
ro.fota.oem                      : wiiteer6762_10.0
ro.fota.platform                 : MTK6762_10.0
ro.fota.type                     : phone
ro.fota.version                  : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613_20250613-0759
ro.hardware                      : mt6762
ro.hardware.egl                  : mtk
ro.product.cpu.abi               : arm64-v8a
ro.product.cpu.abilist           : arm64-v8a,armeabi-v7a,armeabi
ro.product.cpu.abilist32         : armeabi-v7a,armeabi
ro.product.cpu.abilist64         : arm64-v8a
ro.vendor.mediatek.platform      : MT6765
ro.vendor.product.cpu.abilist    : arm64-v8a,armeabi-v7a,armeabi
ro.vendor.product.cpu.abilist32  : armeabi-v7a,armeabi
ro.vendor.product.cpu.abilist64  : arm64-v8a
ro.vndk.version                  : 29
ro.zygote                        : zygote64_32
ro.zygote.preload.enable         : 0

Property namespace classification:
ro.hardware / ro.boot.hardware   : mt6762 runtime hardware selector layer
ro.board.platform                : mt6765 board/platform software layer
ro.vendor.mediatek.platform      : MT6765 vendor BSP layer
ro.product.cpu.*                 : 64-bit primary / 32-bit secondary ABI layer
ro.vendor.product.cpu.*          : vendor ABI exposure layer
ro.bionic.*                      : runtime architecture selector layer
ro.zygote                        : zygote selector layer
ro.vndk.version                  : vendor-interface API layer
ro.hardware.egl                  : graphics/EGL hardware token; display/GPU details belong to later section


Property source-file map:
/default.prop                    : symlink to system/etc/prop.default
/default.prop context            : u:object_r:rootfs:s0
/system/etc/prop.default         : size 1458 / mode -rw------- / context u:object_r:system_file:s0
/system/build.prop               : size 3582 / mode -rw------- / context u:object_r:system_file:s0
/product/build.prop              : size 1046 / mode -rw------- / context u:object_r:system_file:s0
/vendor/build.prop               : size 14385 / mode -rw------- / context u:object_r:vendor_file:s0
/vendor/default.prop             : size 556 / mode -rw------- / context u:object_r:vendor_file:s0
/vendor/ro.prop                  : size 146 / mode -rw-r--r-- / context u:object_r:vendor_file:s0
/vendor/rw.prop                  : size 0 / mode -rw-r--r-- / context u:object_r:vendor_file:s0

/system/build.prop sourced values:
/system/build.prop:43            : ro.product.cpu.abi=arm64-v8a
/system/build.prop:44            : ro.product.cpu.abilist=arm64-v8a,armeabi-v7a,armeabi
/system/build.prop:45            : ro.product.cpu.abilist32=armeabi-v7a,armeabi
/system/build.prop:46            : ro.product.cpu.abilist64=arm64-v8a
/system/build.prop:100           : ro.zygote.preload.enable=0
/system/build.prop:134           : ro.fota.platform=MTK6762_10.0
/system/build.prop:138           : ro.fota.oem=wiiteer6762_10.0
/system/build.prop:140           : ro.fota.version=APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613_20250613-0759

/vendor/build.prop sourced values:
/vendor/build.prop:9             : ro.board.platform=mt6765
/vendor/build.prop:53            : ro.vendor.mediatek.platform=MT6765
/vendor/build.prop:110           : ro.vendor.mediatek.version.branch=alps-mp-q0.mp1
/vendor/build.prop:111           : ro.vendor.mediatek.version.release=alps-mp-q0.mp1-V9.48
/vendor/build.prop:158           : ro.vendor.wlan.gen=gen4m

/default.prop / system prop.default sourced values:
/default.prop:35                 : ro.mediatek.version.branch=alps-mp-q0.mp1
/default.prop:36                 : ro.mediatek.version.release=alps-mp-q0.mp1-V9.48

/vendor/default.prop sourced values:
/vendor/default.prop:4           : ro.vndk.version=29
/vendor/default.prop:5           : ro.zygote=zygote64_32
/vendor/default.prop:13          : ro.bionic.arch=arm64
/vendor/default.prop:14          : ro.bionic.cpu_variant=cortex-a53
/vendor/default.prop:15          : ro.bionic.2nd_arch=arm
/vendor/default.prop:16          : ro.bionic.2nd_cpu_variant=cortex-a53

Open property-source boundary:
ro.hardware=mt6762 source assignment       : not found in checked live property/init source files
ro.boot.hardware=mt6762 source assignment  : not found in checked live property/init source files
Checked literal source tokens              : mt6762 / MT6762 in known live property and init source files produced no return
Classification                             : LIVE_RUNTIME_PROPERTY_CONFIRMED_SOURCE_MECHANISM_OPEN


Project configuration identity:
ProjectConfig path              : /vendor/data/misc/ProjectConfig.mk
ProjectConfig mode/context      : -rw-r--r-- root:root u:object_r:vendor_file:s0
ProjectConfig size              : 22032 bytes
ProjectConfig line count        : 564
ProjectConfig timestamp         : 2008-12-31 18:00
ProjectConfig sha256            : a94dede9f36229b9022ea8ed5fa3ccc7914becc924b65f0c8edc9bd530b53b15

ProjectConfig verified lines:
/vendor/data/misc/ProjectConfig.mk:69  : CUSTOM_MODEM=C17S_LWTG_20210120
/vendor/data/misc/ProjectConfig.mk:346 : MTK_PLATFORM = MT6765
/vendor/data/misc/ProjectConfig.mk:347 : MTK_PLATFORM_OPTIMIZE = yes
/vendor/data/misc/ProjectConfig.mk:535 : NRF_DEVICE = C17S
/vendor/data/misc/ProjectConfig.mk:561 : PRODUCT_WIFI_NAME = APPLLP_5_MAX
/vendor/data/misc/ProjectConfig.mk:563 : PRODUCT_MODEL_CUSTOM = LOKMAT APPLLP 5 MAX
/vendor/data/misc/ProjectConfig.mk:564 : CUSTOM_BUILD_VERNO = APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613
/vendor/data/misc/ProjectConfig.mk:565 : PRODUCT_BT_NAME = APPLLP_5_MAX

ProjectConfig classification:
MTK_PLATFORM                    : MT6765 software BSP/platform identity
CUSTOM_MODEM                    : C17S modem/product-family identity; modem/radio details belong to later section
NRF_DEVICE                      : C17S product-family identity
PRODUCT_MODEL_CUSTOM            : LOKMAT APPLLP 5 MAX commercial model identity
CUSTOM_BUILD_VERNO              : C17S product build-version identity

Excluded from this hardware subsection:
TARGET_BOARD_PLATFORM           : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_BOOTLOADER_BOARD_NAME    : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_CPU_ABI                  : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_CPU_ABI2                 : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_CPU_VARIANT              : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_ARCH_VARIANT             : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_2ND_ARCH                 : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3
TARGET_2ND_CPU_VARIANT          : ROM build / BoardConfig target flag; excluded from Hardware DB 1.3


Init selector / platform split:
/init.rc mode/context           : -rwxr-x--- root:shell u:object_r:rootfs:s0
/init.rc size                   : 33780 bytes
/init.rc sha256                 : e87ae6ccbc537cded9af4cb7790b17dfaa01188217ebca18f52752bb7a9fc21b

/init.rc import selectors:
/init.rc:7                      : import /init.environ.rc
/init.rc:8                      : import /init.usb.rc
/init.rc:9                      : import /init.${ro.hardware}.rc
/init.rc:10                     : import /vendor/etc/init/hw/init.${ro.hardware}.rc
/init.rc:11                     : import /init.usb.configfs.rc
/init.rc:12                     : import /init.${ro.zygote}.rc

Live selector resolution:
ro.hardware                     : mt6762
Resolved root hardware import   : /init.mt6762.rc
Root hardware import status     : ABSENT / No such file or directory
Resolved vendor hardware import : /vendor/etc/init/hw/init.mt6762.rc
Vendor hardware import status   : PRESENT
ro.zygote                       : zygote64_32
Resolved zygote import          : /init.zygote64_32.rc
Zygote import status            : PRESENT

Root init namespace:
/init                           : symlink to /system/bin/init / context u:object_r:init_exec:s0
/init.environ.rc                : present / rootfs rc
/init.rc                        : present / rootfs rc
/init.usb.configfs.rc           : present / rootfs rc
/init.usb.rc                    : present / rootfs rc
/init.zygote32.rc               : present / rootfs rc
/init.zygote64_32.rc            : present / rootfs rc
/init.mt6762.rc                 : absent
/init.mt6765.rc                 : absent

PID 1 identity:
/proc/1/cmdline hex             : 2f 73 79 73 74 65 6d 2f 62 69 6e 2f 69 6e 69 74 00 73 65 63 6f 6e 64 5f 73 74 61 67 65 00
/proc/1/cmdline decoded         : /system/bin/init<NUL>second_stage<NUL>
/proc/1/exe                     : /system/bin/init
/proc/1/exe context display     : u:r:init:s0
Classification                  : LIVE_PID1_INIT_SECOND_STAGE

/system/bin/init identity:
/system/bin/init mode/context   : -rwxr-xr-x root:shell u:object_r:init_exec:s0
/system/bin/init size           : 734296 bytes
/system/bin/init sha256         : f02c418a06e4a2220615609924b647dddacd476756e74d4221f2bfe73a08bef3

Init selector anomaly:
Root hardware init selector exists in /init.rc, but /init.${ro.hardware}.rc resolves to absent /init.mt6762.rc.
Vendor hardware init selector exists in /init.rc, and /vendor/etc/init/hw/init.${ro.hardware}.rc resolves to present /vendor/etc/init/hw/init.mt6762.rc.
Classification                  : LIVE_INIT_SELECTOR_SPLIT_CONFIRMED


Vendor platform init and fstab identity:
ro.vendor.rc                    : /vendor/etc/init/hw/

/vendor/etc/init/hw/init.mt6762.rc:
Mode/context                    : -rw-r--r-- root:root u:object_r:vendor_configs_file:s0
Size                            : 39950 bytes
sha256                          : a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9
Line 4 import                   : import /vendor/etc/init/hw/init.mt6765.usb.rc
Line 127 mount_all              : mount_all /vendor/etc/fstab.mt6765

/vendor/etc/init/hw/init.mt6765.rc:
Mode/context                    : -rw-r--r-- root:root u:object_r:vendor_configs_file:s0
Size                            : 39950 bytes
sha256                          : a9dea430b3cb08d0319c6da4cb477c2c83b260a7f23eb2774d544e4b0fcb54f9
Line 4 import                   : import /vendor/etc/init/hw/init.mt6765.usb.rc
Line 127 mount_all              : mount_all /vendor/etc/fstab.mt6765

/vendor/etc/init/hw/init.mt6765.usb.rc:
Mode/context                    : -rw-r--r-- root:root u:object_r:vendor_configs_file:s0
Size                            : 43547 bytes
sha256                          : e4eb8039f5238c17a49ca3c38c62feb8c6d66eb426f07793595e8ffa66b934d0

Vendor init equivalence:
init.mt6762.rc and init.mt6765.rc have identical size and identical sha256.
Both files mount /vendor/etc/fstab.mt6765.
Both files import init.mt6765.usb.rc.
Classification                  : LIVE_VENDOR_INIT_MT6762_MT6765_EQUIVALENCE_CONFIRMED

/vendor/etc/fstab.mt6762:
Mode/context                    : -rw-r--r-- root:root u:object_r:vendor_configs_file:s0
Size                            : 4169 bytes
sha256                          : 6d033c2a7debc697b036874e552c05530ab26d45891a19200cb854efb2104055
Source marker                   : vendor/mediatek/proprietary/hardware/fstab/mt6765/fstab.in.mt6765

/vendor/etc/fstab.mt6765:
Mode/context                    : -rw-r--r-- root:root u:object_r:vendor_configs_file:s0
Size                            : 4169 bytes
sha256                          : 6d033c2a7debc697b036874e552c05530ab26d45891a19200cb854efb2104055
Source marker                   : vendor/mediatek/proprietary/hardware/fstab/mt6765/fstab.in.mt6765

Fstab logical mount declarations:
Line 9                          : system /system ext4 ro wait,avb=vbmeta_system,logical,first_stage_mount,avb_keys=/avb/q-gsi.avbpubkey:/avb/r-gsi.avbpubkey:/avb/s-gsi.avbpubkey,
Line 11                         : vendor /vendor ext4 ro wait,avb,logical,first_stage_mount,
Line 14                         : product /product ext4 ro wait,avb,logical,first_stage_mount,
Line 16                         : /dev/block/platform/bootdevice/by-name/md_udc /metadata ext4 noatime,nosuid,nodev,discard wait,formattable,first_stage_mount

Fstab / init conclusion:
init.mt6762.rc uses the MT6765 fstab path.
init.mt6765.rc uses the MT6765 fstab path.
fstab.mt6762 and fstab.mt6765 are byte-identical by sha256.
The storage/init selector layer preserves physical/kernel selector name mt6762 while mounting MT6765 fstab content.
Classification                  : FSTAB_INIT_CROSS_LAYER_SPLIT_CONFIRMED


Zygote / ABI runtime selector identity:
ro.zygote                       : zygote64_32
ro.zygote source                : /vendor/default.prop:5
init.svc.zygote                 : running
init.svc.zygote_secondary       : running

/init.zygote64_32.rc:
Mode/context                    : -rwxr-x--- root:shell u:object_r:rootfs:s0
Size                            : 981 bytes
sha256                          : dffaf27b5859fc37d2623bbd65cc7d5b7a7fc81eefc9ffeade1e784bd92cfcc0
Primary zygote service          : /system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server --socket-name=zygote
Secondary zygote service        : /system/bin/app_process32 -Xzygote /system/bin --zygote --socket-name=zygote_secondary --enable-lazy-preload
Classification                  : SELECTED_ZYGOTE64_32_RUNTIME

/init.zygote32.rc:
Mode/context                    : -rwxr-x--- root:shell u:object_r:rootfs:s0
Size                            : 563 bytes
sha256                          : 99b41ae737cfc340d672f0bbd2c9dea3ed1ed73a3187cd61ab2c3fef0f3f90e3
Zygote service                  : /system/bin/app_process -Xzygote /system/bin --zygote --start-system-server
Selection status                : PRESENT_NOT_SELECTED_BY_ro.zygote

/system/bin/app_process:
Type                            : symlink to app_process64
Context                         : u:object_r:system_file:s0
sha256                          : f80d0a7dc34060e513c61e06ca81c85f64241173cc1cd2b0670cfc1f12ea2d4b
Hash note                       : sha256sum dereferenced symlink; hash equals app_process64

/system/bin/app_process64:
Mode/context                    : -rwxr-xr-x root:shell u:object_r:zygote_exec:s0
Size                            : 33424 bytes
sha256                          : f80d0a7dc34060e513c61e06ca81c85f64241173cc1cd2b0670cfc1f12ea2d4b

/system/bin/app_process32:
Mode/context                    : -rwxr-xr-x root:shell u:object_r:zygote_exec:s0
Size                            : 25120 bytes
sha256                          : a0673d8d56758f7c3af838dd9469fea4699ca4689ad47a8f3b489ac040483879

ABI / zygote conclusion:
Primary runtime ABI             : arm64-v8a / arm64
Secondary runtime ABI           : armeabi-v7a,armeabi / arm
CPU variant                     : cortex-a53
Zygote mode                     : zygote64_32
Classification                  : LIVE_64_32_RUNTIME_SELECTOR_CONFIRMED


Runtime mount / dm platform identity:
/system directory metadata:
Path                            : /system
Mode/context                    : drwxr-xr-x root:root u:object_r:system_file:s0
Size                            : 4096 bytes
Inode                           : 53
Link count                      : 16
Mount status                    : not a separate /system mount in captured /proc/mounts; system image mounted as root /

Primary platform mounts:
/dev/block/dm-1                 : mounted at / / ext4 / ro,seclabel,relatime
/dev/block/dm-2                 : mounted at /vendor / ext4 / ro,seclabel,relatime
/dev/block/dm-0                 : mounted at /product / ext4 / ro,seclabel,relatime

/proc/self/mountinfo primary lines:
18 17 253:1 / / ro,relatime shared:1 - ext4 /dev/block/dm-1 ro,seclabel
42 18 253:2 / /vendor ro,relatime shared:10 - ext4 /dev/block/dm-2 ro,seclabel
43 18 253:0 / /product ro,relatime shared:11 - ext4 /dev/block/dm-0 ro,seclabel

/system symlink layer:
/system/vendor                  : symlink to /vendor / context u:object_r:vendor_file:s0
/system/product                 : symlink to /product / context u:object_r:system_file:s0
/system/vendor dereference      : /vendor directory / inode 2 / owner 0:2000 / mode 755 / links 14
/system/product dereference     : /product directory / inode 2 / owner 0:0 / mode 755 / links 11

/dev/block/mapper symlinks:
/dev/block/mapper/product       : symlink to /dev/block/dm-0 / context u:object_r:block_device:s0
/dev/block/mapper/system        : symlink to /dev/block/dm-1 / context u:object_r:block_device:s0
/dev/block/mapper/vendor        : symlink to /dev/block/dm-2 / context u:object_r:block_device:s0

Device-mapper primary identities:
dm-0 name                       : product
dm-0 dev                        : 253:0
dm-0 size sectors               : 2298328
dm-0 size bytes                 : 1176743936

dm-1 name                       : system
dm-1 dev                        : 253:1
dm-1 size sectors               : 2516312
dm-1 size bytes                 : 1288351744

dm-2 name                       : vendor
dm-2 dev                        : 253:2
dm-2 size sectors               : 568848
dm-2 size bytes                 : 291250176

Device-mapper uuid observation:
dm-0 uuid readable payload       : 0a / newline-only
dm-1 uuid readable payload       : 0a / newline-only
dm-2 uuid readable payload       : 0a / newline-only
dm-3 uuid readable payload       : 0a / newline-only
dm-4 uuid readable payload       : 0a / newline-only
UUID classification              : uuid files do not expose UUID strings in readable payload for dm-0 through dm-4 in this capture

Runtime mount conclusion:
/system is represented by dm-1 mounted at root /.
/vendor is represented by dm-2 mounted at /vendor.
/product is represented by dm-0 mounted at /product.
Classification                  : LIVE_DYNAMIC_PARTITION_PLATFORM_MOUNT_IDENTITY_CONFIRMED


Research-state boundary metadata:
Known user-introduced research-state changes from stock:
Bootloader state                : unlocked
Root state                      : Magisk root active
AVB / verity state              : disabled / verified boot state orange
SELinux state                   : permissive

Live research-state evidence:
ro.boot.verifiedbootstate       : orange
ro.boot.flash.locked            : 0
getenforce                      : Permissive
/sys/fs/selinux/enforce mode    : -rw-r--r-- root:root u:object_r:selinuxfs:s0
/sys/fs/selinux/enforce payload : 30 / decoded 0
/sys/fs/selinux/enforce wc -c   : 1
resetprop path                  : /sbin/resetprop
Magisk process state            : magiskd and Magisk app/root processes observed active

Research-state interpretation boundary:
Magisk/root is research-state tooling on the live device.
No Magisk module/script property override was found in checked /data/adb module/script locations.
Do not infer arbitrary stock-device modifications from Magisk presence alone.
Do not treat live boot/init observations as stock-closed where Magisk boot/init effects are possible.
Classification                  : LIVE_RESEARCH_STATE_BOUNDARY_DECLARED


Brief anomaly / cross-section comments:
Bootimage fingerprint anomaly   : ro.bootimage.build.fingerprint begins with SPRD while platform evidence is MediaTek / MTK; primary build identity belongs to Section 1.1 and boot-image decode belongs to later boot-image section.
Boot hardware token split       : bootargs and /proc/cmdline expose androidboot.hardware=mt6765 while ro.boot.hardware and ro.hardware expose mt6762.
Root init selector anomaly      : /init.${ro.hardware}.rc resolves to absent /init.mt6762.rc; vendor selector resolves to present /vendor/etc/init/hw/init.mt6762.rc.
DM cross-section lead           : dm-3 name is bow; mounted as /data in captured mountinfo; storage details belong to Section 2.
APEX cross-section lead         : dm-4 name is com.android.tzdata@298901000; mounted at /apex/com.android.tzdata@298901000 and /apex/com.android.tzdata; APEX/runtime details belong to software/runtime sections.
Device-tree later-section leads : nfc / irq_nfc, fingerprint, i2c7 / i2c8 / i2c9, utos / utos_tester / tkcore / mobicore, wiite_con / wiite_corp observed in DT root listing; not expanded in Section 1.3.


Canonical 1.3 conclusion:
Physical / kernel lineage       : MT6762V/WB / mt6762
Device-tree model lineage       : MT6762V/WB
Runtime hardware selector       : ro.hardware=mt6762 / ro.boot.hardware=mt6762
Bootargs hardware token         : androidboot.hardware=mt6765
Device-tree compatible layer    : mediatek,MT6765
Vendor BSP platform layer       : ro.board.platform=mt6765 / ro.vendor.mediatek.platform=MT6765 / MTK_PLATFORM=MT6765
Init/fstab layer                : ro.hardware selects mt6762 vendor init name; init.mt6762.rc mounts MT6765 fstab path; init.mt6762.rc and init.mt6765.rc are byte-identical
Runtime ABI layer               : arm64 primary / arm secondary / zygote64_32
Mount layer                     : system/vendor/product exposed as dm-1/dm-2/dm-0 with system mounted as root /

Section 1.3 final classification:
The C17S platform identity is a verified cross-layer split, not a conflict.
MT6762V/WB / mt6762 must remain represented for physical silicon, kernel-visible hardware, and runtime hardware selector identity.
MT6765 / MT6765 must remain represented for software BSP, HAL ABI surface, DT compatible, vendor platform, fstab template lineage, and storage/init platform layer.
BoardConfig TARGET_* flags are intentionally excluded from this hardware database subsection.


TARGET_BOARD_PLATFORM=mt6765 remains required for HAL and vendor ABI parity.
ro.hardware=mt6762 remains required for physical/kernel hardware identity.
Both identities must remain represented in their correct layers.

################################################################################
# 1.4 — CPU ARCHITECTURE
################################################################################

CPU core architecture:
ISA                           : ARMv8-A
Execution state                : AArch64 primary, AArch32 secondary supported
Core count                     : 8
Core type                      : ARM Cortex-A53
CPU implementer                : 0x41
CPU architecture field         : 8
CPU variant                    : 0x0
CPU part                       : 0xd03
CPU revision                   : 4
BogoMIPS                       : 26.00 per core

Live processor identity:
/proc/cpuinfo Processor        : AArch64 Processor rev 4 (aarch64)
/proc/cpuinfo Hardware         : MT6762V/WB
uname -m                       : aarch64

CPU core records:

| CPU | BogoMIPS | Features | Implementer | Architecture | Variant | Part | Revision |
|---:|---:|---|---|---:|---|---|---:|
| 0 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 1 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 2 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 3 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 4 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 5 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 6 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |
| 7 | 26.00 | fp asimd evtstrm aes pmull sha1 sha2 crc32 | 0x41 | 8 | 0x0 | 0xd03 | 4 |

CPU feature flags:
Feature 0                      : fp
Feature 1                      : asimd
Feature 2                      : evtstrm
Feature 3                      : aes
Feature 4                      : pmull
Feature 5                      : sha1
Feature 6                      : sha2
Feature 7                      : crc32

CPU availability state:
/sys/devices/system/cpu/present  : 0-7
/sys/devices/system/cpu/possible : 0-7
/sys/devices/system/cpu/online   : 0-7

CPU evidence path contexts:

| Path | SELinux context |
|---|---|
| /proc/cpuinfo | u:object_r:proc_cpuinfo:s0 |
| /sys/devices/system/cpu/present | u:object_r:sysfs_devices_system_cpu:s0 |
| /sys/devices/system/cpu/possible | u:object_r:sysfs_devices_system_cpu:s0 |
| /sys/devices/system/cpu/online | u:object_r:sysfs_devices_system_cpu:s0 |

Cluster topology:
Cluster count                  : 2
Cluster 0                      : cores 0-3
Cluster 1                      : cores 4-7
Core type across both clusters : Cortex-A53
Topology class                 : split DVFS clusters, not heterogeneous big.LITTLE
SMT exposure                   : none

CPU topology records:

| CPU | physical_package_id | core_id | core_siblings_list | thread_siblings_list |
|---:|---:|---:|---|---|
| 0 | 0 | 0 | 0-3 | 0 |
| 1 | 0 | 1 | 0-3 | 1 |
| 2 | 0 | 2 | 0-3 | 2 |
| 3 | 0 | 3 | 0-3 | 3 |
| 4 | 1 | 0 | 4-7 | 4 |
| 5 | 1 | 1 | 4-7 | 5 |
| 6 | 1 | 2 | 4-7 | 6 |
| 7 | 1 | 3 | 4-7 | 7 |

ATF PSCI MPIDR topology:
Cluster 0 cores 0-3            : MPIDR 0x000 through 0x003
Cluster 1 cores 0-3            : MPIDR 0x100 through 0x103

Device-tree CPU topology records:

| Device-tree CPU node | DT reg value | MPIDR class | CPU-map cluster | CPU-map core | CPU-map phandle | CPU phandle | linux,phandle |
|---|---:|---|---:|---:|---:|---:|---:|
| cpu@000 | 0x000 | cluster 0 / core 0 | 0 | 0 | 0x09 | 0x09 | 0x09 |
| cpu@001 | 0x001 | cluster 0 / core 1 | 0 | 1 | 0x0a | 0x0a | 0x0a |
| cpu@002 | 0x002 | cluster 0 / core 2 | 0 | 2 | 0x0b | 0x0b | 0x0b |
| cpu@003 | 0x003 | cluster 0 / core 3 | 0 | 3 | 0x0c | 0x0c | 0x0c |
| cpu@100 | 0x100 | cluster 1 / core 0 | 1 | 0 | 0x0d | 0x0d | 0x0d |
| cpu@101 | 0x101 | cluster 1 / core 1 | 1 | 1 | 0x0e | 0x0e | 0x0e |
| cpu@102 | 0x102 | cluster 1 / core 2 | 1 | 2 | 0x0f | 0x0f | 0x0f |
| cpu@103 | 0x103 | cluster 1 / core 3 | 1 | 3 | 0x10 | 0x10 | 0x10 |

Device-tree CPU root:
/sys/firmware/devicetree/base/cpus/#address-cells : 0x00000001
/sys/firmware/devicetree/base/cpus/#size-cells    : 0x00000000
/sys/firmware/devicetree/base/cpus/name           : cpus<NUL>
/sys/firmware/devicetree/base/cpus SELinux        : u:object_r:sysfs:s0

Device-tree CPU node identity:

| CPU node | compatible | device_type | enable-method | clock-frequency raw | clock-frequency decoded |
|---|---|---|---|---:|---:|
| cpu@000 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x89267940 | 2,301,000,000 Hz |
| cpu@001 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x89267940 | 2,301,000,000 Hz |
| cpu@002 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x89267940 | 2,301,000,000 Hz |
| cpu@003 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x89267940 | 2,301,000,000 Hz |
| cpu@100 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x6b49d200 | 1,800,000,000 Hz |
| cpu@101 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x6b49d200 | 1,800,000,000 Hz |
| cpu@102 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x6b49d200 | 1,800,000,000 Hz |
| cpu@103 | arm,cortex-a53<NUL> | cpu<NUL> | psci<NUL> | 0x6b49d200 | 1,800,000,000 Hz |

Device-tree CPU clock-frequency layer:
Static CPU-node metadata        : Device-tree clock-frequency
Kernel DVFS policy exposure     : Live cpufreq policy maxima
Layer handling                  : separate evidence layers; values are not collapsed into one value

PSCI provider:
/sys/firmware/devicetree/base/psci/name       : psci<NUL>
/sys/firmware/devicetree/base/psci/compatible : arm,psci-1.0<NUL>
/sys/firmware/devicetree/base/psci/method     : smc<NUL>
/sys/firmware/devicetree/base/psci/method raw : 0x736d6300
/sys/firmware/devicetree/base/psci SELinux    : u:object_r:sysfs:s0

CPU idle-state linkage:
/sys/firmware/devicetree/base/cpus/idle-states/entry-method : arm,psci<NUL>

CPU idle-state phandles:

| Idle-state phandle | Idle-state node |
|---:|---|
| 0x02 | standby |
| 0x03 | mcdi-cpu |
| 0x04 | mcdi-cluster |
| 0x05 | sodi |
| 0x06 | sodi3 |
| 0x07 | dpidle |
| 0x08 | suspend |

CPU idle-state link records:

| CPU node | cpu-idle-states phandle list |
|---|---|
| cpu@000 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@001 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@002 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@003 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@100 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@101 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@102 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |
| cpu@103 | 0x02 0x03 0x04 0x05 0x06 0x07 0x08 |

Secondary CPU entrypoint:
Address                        : 0x47d810d8
Device-origin source            : /dev/block/by-name/expdb
Source partition identity       : expdb / mmcblk0p4
Evidence class                 : ATF runtime log record
ATF function                   : plat_affinst_on()
CPU scope                      : secondary CPU bring-up for MPIDR 0x1, 0x2, 0x3, 0x100, 0x101, 0x102, 0x103
Region                         : atf-reserved
ATF reserved base              : 0x47d80000
ATF reserved size              : 0x50000
ATF reserved inclusive end      : 0x47dcffff
Entrypoint offset in region     : 0x10d8
Range containment              : 0x47d810d8 lies inside 0x47d80000-0x47dcffff
Live DT release property       : not exposed
Primary boot CPU handling       : primary boot CPU is not listed in these ATF secondary bring-up records
Record scope                   : PSCI/ATF activation of secondary CPUs only

Live expdb partition identity for entrypoint source:
/dev/block/by-name/expdb resolved path                : /dev/block/mmcblk0p4
/dev/block/platform/bootdevice/by-name/expdb resolved : /dev/block/mmcblk0p4

Live expdb partition fields:

| Field | Value |
|---|---|
| MAJOR | 179 |
| MINOR | 4 |
| DEVNAME | mmcblk0p4 |
| DEVTYPE | partition |
| PARTN | 4 |
| PARTNAME | expdb |
| start sector | 68672 |
| size sectors | 40960 |
| sector size | 512 bytes |
| exact byte offset | 35,160,064 bytes |
| exact byte size | 20,971,520 bytes |
| by-name symlink context | u:object_r:block_device:s0 |
| platform symlink context | u:object_r:block_device:s0 |
| block device context | u:object_r:expdb_block_device:s0 |

Entrypoint ATF runtime records from live expdb:
Extraction source command       : adb shell 'su -c "dd if=/dev/block/by-name/expdb bs=1048576 count=20 2>/dev/null"' | strings -a | grep -n -C 2 "sec_entrypoint"

First observed ATF secondary bring-up sequence:

| strings line | ATF timestamp | MPIDR | sec_entrypoint | afflvl | state | adjacent PSCI / SPMC record |
|---:|---:|---:|---:|---:|---:|---|
| 2863 | 13.964884 | 0x1 | 0x47d810d8 | 1 | 1 | core 0.1 bring-up follows |
| 2864 | 13.965030 | 0x1 | 0x47d810d8 | 0 | 1 | spmc power on core 0.1 |
| 2868 | 13.992925 | 0x2 | 0x47d810d8 | 1 | 1 | core 0.2 bring-up follows |
| 2869 | 13.993067 | 0x2 | 0x47d810d8 | 0 | 1 | spmc power on core 0.2 |
| 2873 | 14.020980 | 0x3 | 0x47d810d8 | 1 | 1 | core 0.3 bring-up follows |
| 2874 | 14.021121 | 0x3 | 0x47d810d8 | 0 | 1 | spmc power on core 0.3 |
| 2878 | 14.049045 | 0x100 | 0x47d810d8 | 1 | 1 | cluster 1 bring-up follows |
| 2881 | 14.049347 | 0x100 | 0x47d810d8 | 0 | 1 | core 1.0 bring-up follows |
| 2886 | 14.077116 | 0x101 | 0x47d810d8 | 1 | 1 | core 1.1 bring-up follows |
| 2887 | 14.077259 | 0x101 | 0x47d810d8 | 0 | 1 | spmc power on core 1.1 |
| 2891 | 14.105181 | 0x102 | 0x47d810d8 | 1 | 1 | core 1.2 bring-up follows |
| 2892 | 14.105322 | 0x102 | 0x47d810d8 | 0 | 1 | spmc power on core 1.2 |
| 2896 | 14.133248 | 0x103 | 0x47d810d8 | 1 | 1 | core 1.3 bring-up follows |
| 2897 | 14.133390 | 0x103 | 0x47d810d8 | 0 | 1 | spmc power on core 1.3 |

Second observed ATF secondary bring-up sequence:

| strings line | ATF timestamp | MPIDR | sec_entrypoint | afflvl | state | adjacent PSCI / SPMC record |
|---:|---:|---:|---:|---:|---:|---|
| 11729 | 15.452225 | 0x1 | 0x47d810d8 | 1 | 1 | core 0.1 bring-up follows |
| 11730 | 15.452371 | 0x1 | 0x47d810d8 | 0 | 1 | spmc power on core 0.1 |
| 11734 | 15.480268 | 0x2 | 0x47d810d8 | 1 | 1 | core 0.2 bring-up follows |
| 11735 | 15.480408 | 0x2 | 0x47d810d8 | 0 | 1 | spmc power on core 0.2 |
| 11739 | 15.508324 | 0x3 | 0x47d810d8 | 1 | 1 | core 0.3 bring-up follows |
| 11740 | 15.508465 | 0x3 | 0x47d810d8 | 0 | 1 | spmc power on core 0.3 |
| 11744 | 15.536390 | 0x100 | 0x47d810d8 | 1 | 1 | cluster 1 bring-up follows |
| 11747 | 15.536692 | 0x100 | 0x47d810d8 | 0 | 1 | core 1.0 bring-up follows |
| 11752 | 15.564459 | 0x101 | 0x47d810d8 | 1 | 1 | core 1.1 bring-up follows |
| 11753 | 15.564601 | 0x101 | 0x47d810d8 | 0 | 1 | spmc power on core 1.1 |
| 11757 | 15.592523 | 0x102 | 0x47d810d8 | 1 | 1 | core 1.2 bring-up follows |
| 11758 | 15.592665 | 0x102 | 0x47d810d8 | 0 | 1 | spmc power on core 1.2 |
| 11762 | 15.620591 | 0x103 | 0x47d810d8 | 1 | 1 | core 1.3 bring-up follows |
| 11763 | 15.620734 | 0x103 | 0x47d810d8 | 0 | 1 | spmc power on core 1.3 |

Live DT search result for entrypoint/release properties:
Search scope                    : /sys/firmware/devicetree/base
Search class                    : CPU release / secondary entrypoint / boot address property names
Matched entrypoint property     : none
Matched cpu-release property    : none
Matched release-addr property   : none
Matched secondary property      : none
Matched boot-addr property      : none
Entrypoint verification source  : live expdb ATF runtime records
DT CPU release property source  : not present

Reserved memory: ATF runtime region:
reserved-memory/#address-cells                         : 0x00000002
reserved-memory/#size-cells                            : 0x00000002
mblock-4-atf-reserved/name                             : mblock-4-atf-reserved<NUL>
mblock-4-atf-reserved/compatible                       : mediatek,atf-reserved<NUL>
mblock-4-atf-reserved/reg                              : 00000000 47d80000 00000000 00050000
mblock-4-atf-reserved base                             : 0x47d80000
mblock-4-atf-reserved size                             : 0x00050000
mblock-4-atf-reserved no-map                           : present
mblock-4-atf-reserved SELinux                          : u:object_r:sysfs:s0

Reserved memory: ATF log region:
mblock-5-atf-log-reserved/name                         : mblock-5-atf-log-reserved<NUL>
mblock-5-atf-log-reserved/compatible                   : mediatek,atf-log-reserved<NUL>
mblock-5-atf-log-reserved/reg                          : 00000000 bffbf000 00000000 00040000
mblock-5-atf-log-reserved base                         : 0xbffbf000
mblock-5-atf-log-reserved size                         : 0x00040000
mblock-5-atf-log-reserved no-map                       : present
mblock-5-atf-log-reserved SELinux                      : u:object_r:sysfs:s0

ATF logger node:
/sys/firmware/devicetree/base/atf_logger/name          : atf_logger<NUL>
/sys/firmware/devicetree/base/atf_logger/compatible    : mediatek,atf_logger<NUL>
/sys/firmware/devicetree/base/atf_logger/interrupts    : 00000000 000000f7 00000001
/sys/firmware/devicetree/base/atf_logger SELinux       : u:object_r:sysfs:s0

CPU frequency / DVFS domains:
/proc/cpufreq/MT_CPU_DVFS_LL/  : lower-frequency CPU DVFS domain
/proc/cpufreq/MT_CPU_DVFS_L/   : higher-frequency CPU DVFS domain
/proc/cpufreq/MT_CPU_DVFS_CCI/ : CCI/interconnect DVFS domain

/proc/cpufreq inventory:
/proc/cpufreq SELinux context   : u:object_r:proc_cpufreq:s0
Global proc entries             : cpufreq_debug, cpufreq_dvfs_time_profile, cpufreq_power_mode, cpufreq_sched_disable, cpufreq_stress_test
Per-domain proc entries         : cpufreq_freq, cpufreq_volt, cpufreq_oppidx, cpufreq_turbo_mode
Detected domains                : MT_CPU_DVFS_L, MT_CPU_DVFS_LL, MT_CPU_DVFS_CCI

cpufreq sysfs policy domains:

| cpufreq policy | related_cpus | affected_cpus | cpuinfo_min_freq | cpuinfo_max_freq | scaling_min_freq | scaling_max_freq | scaling_cur_freq | governor | available governors |
|---|---|---|---:|---:|---:|---:|---:|---|---|
| policy0 | 0 1 2 3 | 0 1 2 3 | 900000 | 1750000 | 900000 | 1750000 | 1750000 | schedplus | schedplus ondemand userspace powersave interactive performance |
| policy4 | 4 5 6 7 | 4 5 6 7 | 400000 | 1050000 | 400000 | 1050000 | 643000 | schedplus | schedplus ondemand userspace powersave interactive performance |

DVFS domain mapping:
MT_CPU_DVFS_L/0                : policy0 -> CPUs 0-3 -> higher-frequency CPU cluster
MT_CPU_DVFS_LL/4               : policy4 -> CPUs 4-7 -> lower-frequency CPU cluster
MT_CPU_DVFS_CCI/10             : CCI/interconnect DVFS domain

Captured live DVFS state — prior sample:
L cluster frequency            : 899,500 KHz
L cluster Vproc                : 662,500 µV
L cluster Vsram                : 850,000 µV
LL cluster frequency           : 541,750 KHz
LL cluster Vproc               : 675,000 µV
LL cluster Vsram               : 850,000 µV
CCI frequency                  : 591,750 KHz

Captured live DVFS state — verification sample:

| Domain | Domain token | Current frequency | Vproc | Vsram | Current OPP index | Turbo mode |
|---|---|---:|---:|---:|---:|---|
| MT_CPU_DVFS_L | MT_CPU_DVFS_L/0 | 1,749,000 KHz | 862,500 µV | 962,500 µV | 8 | support=0, disable=0, loc_opt=0 |
| MT_CPU_DVFS_LL | MT_CPU_DVFS_LL/4 | 744,750 KHz | 862,500 µV | 962,500 µV | 12 | support=0, disable=0, loc_opt=0 |
| MT_CPU_DVFS_CCI | MT_CPU_DVFS_CCI/10 | 762,500 KHz | 862,500 µV | 962,500 µV | 8 | support=0, disable=0, loc_opt=0 |

Captured live DVFS value boundary:
Captured live DVFS values       : runtime state at capture time
Frequency/voltage drift         : different capture-time frequencies and voltages are not contradictions

MT_CPU_DVFS_L OPP table:

| OPP | Frequency KHz | Encoded voltage field |
|---:|---:|---:|
| 0 | 1750000 | 100000 |
| 1 | 1750000 | 100000 |
| 2 | 1750000 | 100000 |
| 3 | 1750000 | 100000 |
| 4 | 1750000 | 100000 |
| 5 | 1750000 | 97500 |
| 6 | 1750000 | 93750 |
| 7 | 1750000 | 90000 |
| 8 | 1750000 | 86250 |
| 9 | 1617000 | 83125 |
| 10 | 1484000 | 78750 |
| 11 | 1351000 | 75625 |
| 12 | 1218000 | 71250 |
| 13 | 1085000 | 67500 |
| 14 | 979000 | 64375 |
| 15 | 900000 | 61875 |

MT_CPU_DVFS_LL OPP table:

| OPP | Frequency KHz | Encoded voltage field |
|---:|---:|---:|
| 0 | 1050000 | 87500 |
| 1 | 1050000 | 87500 |
| 2 | 1050000 | 87500 |
| 3 | 1050000 | 87500 |
| 4 | 1050000 | 87500 |
| 5 | 1050000 | 87500 |
| 6 | 1050000 | 87500 |
| 7 | 1050000 | 87500 |
| 8 | 1050000 | 86250 |
| 9 | 948000 | 83125 |
| 10 | 846000 | 79375 |
| 11 | 745000 | 75625 |
| 12 | 643000 | 71875 |
| 13 | 542000 | 68125 |
| 14 | 501000 | 66250 |
| 15 | 400000 | 63125 |

MT_CPU_DVFS_CCI OPP table:

| OPP | Frequency KHz | Encoded voltage field |
|---:|---:|---:|
| 0 | 1048000 | 111875 |
| 1 | 997000 | 106875 |
| 2 | 953000 | 102500 |
| 3 | 915000 | 98750 |
| 4 | 877000 | 95000 |
| 5 | 839000 | 91250 |
| 6 | 801000 | 87500 |
| 7 | 763000 | 83750 |
| 8 | 724000 | 80000 |
| 9 | 658000 | 76875 |
| 10 | 592000 | 73750 |
| 11 | 525000 | 70625 |
| 12 | 459000 | 67500 |
| 13 | 392000 | 64375 |
| 14 | 339000 | 61875 |
| 15 | 300000 | 60000 |

Final CPU architecture classification:
CPU architecture class          : ARMv8-A / AArch64 / AArch32-capable
Core count                      : 8
Core implementation             : ARM Cortex-A53 across all CPU nodes
Cluster topology                : 2 clusters, 4 cores each
Cluster heterogeneity           : no heterogeneous big.LITTLE core-type split observed
Cluster asymmetry               : DVFS and static DT clock-frequency asymmetry observed
CPU0-CPU3                       : Cortex-A53, MPIDR 0x000-0x003, policy0, MT_CPU_DVFS_L/0
CPU4-CPU7                       : Cortex-A53, MPIDR 0x100-0x103, policy4, MT_CPU_DVFS_LL/4
PSCI provider                   : arm,psci-1.0 via smc
CPU enable method               : psci
CPU idle entry method           : arm,psci
Secondary CPU entrypoint        : 0x47d810d8
Entrypoint source               : live expdb ATF runtime log records from /dev/block/by-name/expdb
Entrypoint ATF region           : mediatek,atf-reserved at 0x47d80000 size 0x50000

Scope note:
Full power-management behavior  : belongs to the power-management section
Thermal policy                  : belongs to the power-management section
Scheduler tuning                : belongs to the power-management section
Long-duration DVFS behavior     : belongs to the power-management section
Section 1.4 record scope        : CPU architecture, topology, PSCI/ATF CPU bring-up evidence, and directly observed DVFS domain identity

################################################################################
################################################################################
# 1.5 — CORE GPU / GRAPHICS SILICON IDENTITY
################################################################################

GPU silicon identity:
GPU                            : PowerVR GE8320
Architecture                   : Rogue
Vendor                         : Imagination Technologies
Codename / stack name           : MEOW
Renderer string                : PowerVR Rogue GE8320
Runtime renderer vendor         : Imagination Technologies
Runtime proof source            : live SurfaceFlinger EGL/GLES report
Classification                 : LIVE_PROVEN_RUNTIME_GPU_RENDERER_IDENTITY

PVR / RGX runtime hardware identity:
Runtime GPU device name         : RGX
PVR driver family               : Rogue_DDK_Android
PVR driver version              : rogueddk 1.11@5425693
PVR driver build class          : release
PVR driver build options        : 0x00002010 mtk_android
PVR MTK version string          : blank
Rogue hardware version          : 22.87.104.18 (HW)
PVR firmware version            : 1.11 @ 5425693
PVR firmware build class        : release
PVR firmware build options      : 0x80002010 mtk_android
PVR driver status               : OK
PVR firmware status             : OK
Runtime proof source            : /sys/kernel/debug/pvr/version and /sys/kernel/debug/pvr/status
Classification                 : LIVE_PROVEN_RUNTIME_PVR_RGX_IDENTITY

GPU kernel / platform binding:
Live platform device            : /sys/devices/platform/13000000.mfg_doma
Bound kernel driver             : pvrsrvkm
Driver path                     : /sys/bus/platform/drivers/pvrsrvkm
Device-tree node                : /sys/firmware/devicetree/base/mfg_doma@13000000
OF_NAME                         : mfg_doma
OF_FULLNAME                     : /mfg_doma@13000000
OF_COMPATIBLE_0                 : mediatek,doma
MODALIAS from uevent            : of:Nmfg_domaT<NULL>Cmediatek,doma
modalias file output            : blank
Classification                 : LIVE_PROVEN_GPU_PLATFORM_DRIVER_BINDING

GPU device-tree register identity:
Parent #address-cells           : 00000002
Parent #size-cells              : 00000002
interrupt-parent                : 00000001
Interrupt controller node       : interrupt-controller@0c000000
DT node compatible raw          : 6d 65 64 69 61 74 65 6b 2c 64 6f 6d 61 00
DT node compatible decoded      : mediatek,doma\0
DT node name raw                : 6d 66 67 5f 64 6f 6d 61 00
DT node name decoded            : mfg_doma\0
DT interrupt-name raw           : 52 47 58 00
DT interrupt-name decoded       : RGX\0
DT reg raw cells                : 00000000 13000000 00000000 00080000
DT reg decoded base             : 0x13000000
DT reg decoded size             : 0x00080000
DT interrupts raw cells         : 00000000 00000103 00000008
DT clock-frequency raw          : 21f98280
Classification                 : LIVE_RUNTIME_EXPOSED_DEVICE_TREE_GPU_DOMAIN

DRM / DRI runtime binding identity:
DRM class node                  : /sys/class/drm/card0
DRM class node target           : ../../devices/platform/13000000.mfg_doma/drm/card0
DRM control node                : /sys/class/drm/controlD64
DRM control node target         : ../../devices/platform/13000000.mfg_doma/drm/controlD64
DRM render node                 : /sys/class/drm/renderD128
DRM render node target          : ../../devices/platform/13000000.mfg_doma/drm/renderD128
DRM version                     : drm 1.1.0 20060810
DRI debug entry                 : /sys/kernel/debug/dri/0
DRI debug entry files           : clients, name
DRI control entry               : /sys/kernel/debug/dri/64
DRI control entry files         : clients, name
DRI render entry                : /sys/kernel/debug/dri/128
DRI render entry files          : clients, name
Classification                 : LIVE_PROVEN_DRM_DRI_RUNTIME_BINDING

Graphics API runtime identity:
EGL HAL property                : ro.hardware.egl=mtk
EGL implementation              : 1.4 build 1.11@5425693
GLES vendor                     : Imagination Technologies
GLES renderer                   : PowerVR Rogue GE8320
GLES version                    : OpenGL ES 3.2 build 1.11@5425693
ro.opengles.version             : 196610
ro.opengles.version decoded     : 0x00030002 = OpenGL ES 3.2
Vulkan                          : 1.1
Vulkan version field            : 4198400
OpenCL                          : stock OpenCL libraries present in vendor 32-bit and 64-bit stacks
Classification                 : LIVE_RUNTIME_API_IDENTITY_PLUS_STOCK_VENDOR_API_STACK

Stock GPU firmware identity:
Firmware path                   : /vendor/firmware/rgx.fw
Stock source path               : super_unpacked/vendor/firmware/rgx.fw
Type                            : regular file
Mode                            : -rw-r--r--
UID                             : 0
GID                             : 0
Size                            : 118,784 bytes
Links                           : 1
SELinux context                 : u:object_r:vendor_file:s0
SHA256                          : af317093b7f374d9b223eb69a3fb22db894a324e09e1391aa2c07ed1cea968f0
File type                       : ELF 32-bit LSB executable, MIPS, MIPS32 rel2 version 1 (SYSV), statically linked, stripped
Classification                 : STOCK_VENDOR_FIRMWARE_IDENTITY

Stock core graphics service executables:
/vendor/bin/hw/android.hardware.graphics.allocator@2.0-service
  type                          : regular file
  mode                          : -rwxr-xr-x
  uid                           : 0
  gid                           : 2000
  size                          : 11,584 bytes
  inode                         : 29742
  links                         : 1
  context                       : u:object_r:hal_graphics_allocator_default_exec:s0
  sha256                        : de14c988341b1bd421ad97191b9d45ec176bcd468feb42704e72f9b97fae774c

/vendor/bin/hw/android.hardware.graphics.composer@2.1-service
  type                          : regular file
  mode                          : -rwxr-xr-x
  uid                           : 0
  gid                           : 2000
  size                          : 11,672 bytes
  inode                         : 29743
  links                         : 1
  context                       : u:object_r:hal_graphics_composer_default_exec:s0
  sha256                        : 9a718498f8f8effc1bbcca7c1c9b92a2b6d0ea22b4eb7529c8b54e33fab28b89

/vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service
  type                          : regular file
  mode                          : -rwxr-xr-x
  uid                           : 0
  gid                           : 2000
  size                          : 29,976 bytes
  inode                         : 29760
  links                         : 1
  context                       : u:object_r:mtk_hal_gpu_exec:s0
  sha256                        : 6dd788d13979d080066ea915342579b187f2df4e637e7c8d3d7509aea4b4afe8

Core graphics HAL identity:
64-bit allocator HAL            : /vendor/lib64/hw/android.hardware.graphics.allocator@2.0-impl.so
64-bit allocator size           : 24,904 bytes
64-bit allocator context        : u:object_r:vendor_file:s0
64-bit allocator sha256         : 8a8f18a08c0bebc96c1f936ca971dd16b06f02c0db7b4fb5bd3c8a5f4960aa65

64-bit composer HAL             : /vendor/lib64/hw/android.hardware.graphics.composer@2.1-impl.so
64-bit composer size            : 104,224 bytes
64-bit composer context         : u:object_r:vendor_file:s0
64-bit composer sha256          : a3ace507225480020f0ae9e36be72f22a83714575b844f666841418fe3b0c98e

64-bit mapper HAL               : /vendor/lib64/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so
64-bit mapper size              : 38,696 bytes
64-bit mapper context           : u:object_r:same_process_hal_file:s0
64-bit mapper sha256            : 2d6c882b22326ed01a578f1d8545a73838c903ffb568b986665f7b30f47ed9c0

64-bit gralloc HAL              : /vendor/lib64/hw/gralloc.mt6765.so
64-bit gralloc size             : 616,632 bytes
64-bit gralloc context          : u:object_r:same_process_hal_file:s0
64-bit gralloc sha256           : 89ec41a1a401551d88422d8c22df1ea523b2153e00fd5fc1c8a2ff1cacc60570

64-bit hwcomposer HAL           : /vendor/lib64/hw/hwcomposer.mt6765.so
64-bit hwcomposer size          : 682,224 bytes
64-bit hwcomposer context       : u:object_r:vendor_file:s0
64-bit hwcomposer sha256        : 4887675a3d5f4b85496c4e677f402be62806302a928927b95f54e412c4cd7694

64-bit Vulkan HAL               : /vendor/lib64/hw/vulkan.mt6765.so
64-bit Vulkan HAL size          : 683,024 bytes
64-bit Vulkan HAL context       : u:object_r:same_process_hal_file:s0
64-bit Vulkan HAL sha256        : a0aec798ba07930dae09b6092b32e05e05af8326e6edd7e8683e50caf73acbb5

32-bit allocator HAL            : /vendor/lib/hw/android.hardware.graphics.allocator@2.0-impl.so
32-bit allocator size           : 24,580 bytes
32-bit allocator context        : u:object_r:vendor_file:s0
32-bit allocator sha256         : e08fd77637989d8b2df4c602a204cced8861832ac4832419f3e89cc07c6e2e7b

32-bit composer HAL             : /vendor/lib/hw/android.hardware.graphics.composer@2.1-impl.so
32-bit composer size            : 79,400 bytes
32-bit composer context         : u:object_r:vendor_file:s0
32-bit composer sha256          : 8f4221d1c37d21d755629564e6b1ffedd5d4fa93b5c2836a583c8da89de8c999

32-bit mapper HAL               : /vendor/lib/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so
32-bit mapper size              : 29,980 bytes
32-bit mapper context           : u:object_r:same_process_hal_file:s0
32-bit mapper sha256            : 79dba7c0136c21a627cfd0c92fe9e247b0694c791d62e47ee7124fdb00b87b9c

32-bit gralloc default HAL      : /vendor/lib/hw/gralloc.default.so
32-bit gralloc default size     : 28,788 bytes
32-bit gralloc default context  : u:object_r:same_process_hal_file:s0
32-bit gralloc default sha256   : bb60cb89dcff1b310035433b4712f5002cbb267c08ffacfc92b09574942bb8e3

32-bit gralloc HAL              : /vendor/lib/hw/gralloc.mt6765.so
32-bit gralloc size             : 552,832 bytes
32-bit gralloc context          : u:object_r:same_process_hal_file:s0
32-bit gralloc sha256           : b7a3ec5bc130e5c6d9fe3c24f0f9d85c61981da0c306c2c167521580ba8e90e7

32-bit Vulkan HAL               : /vendor/lib/hw/vulkan.mt6765.so
32-bit Vulkan HAL size          : 543,016 bytes
32-bit Vulkan HAL context       : u:object_r:same_process_hal_file:s0
32-bit Vulkan HAL sha256        : d174bf26d577768fb1e34f2853192a4856a1752f20e623ca0a80add4bcafafd1

EGL / GLES / MEOW stock stack identity:
64-bit EGL vendor library       : /vendor/lib64/egl/libEGL_mtk.so
64-bit EGL size                 : 14,000 bytes
64-bit EGL sha256               : c1e17bb6f92d90170573a84724483e0ffe574503aeba3716591964eeb6680276

64-bit MEOW GLES library        : /vendor/lib64/egl/libGLES_meow.so
64-bit MEOW GLES size           : 1,999,944 bytes
64-bit MEOW GLES sha256         : 32fa3f4d9044a72251c2e310c000b038867f1dabd091ac7da67e9f206b7803e8

64-bit GLESv1 CM library        : /vendor/lib64/egl/libGLESv1_CM_mtk.so
64-bit GLESv1 CM size           : 706,528 bytes
64-bit GLESv1 CM sha256         : 745912f6219393130a38a528a82473cdf76b703d60c3e21f1e39bdc65d01bb28

64-bit GLESv2 library           : /vendor/lib64/egl/libGLESv2_mtk.so
64-bit GLESv2 size              : 1,591,296 bytes
64-bit GLESv2 sha256            : d4714f8a2e3f519f51b8cc0e796be19ce7d5c5d7c8dd06f62ed8f98c7a859d22

64-bit MEOW trace library       : /vendor/lib64/egl/libMEOW_trace.so
64-bit MEOW trace size          : 535,640 bytes
64-bit MEOW trace sha256        : a1e0566272e1153d0a9a9e4b07c7aadde9277b7c1ba4f2b7510c01d9198b0843

32-bit EGL vendor library       : /vendor/lib/egl/libEGL_mtk.so
32-bit EGL size                 : 17,604 bytes
32-bit EGL sha256               : 771e33b95776024b15e897aacc8bea58e0db99392dc6eee622a1adc4549ae473

32-bit MEOW GLES library        : /vendor/lib/egl/libGLES_meow.so
32-bit MEOW GLES size           : 1,252,340 bytes
32-bit MEOW GLES sha256         : 0152c3186e298907d4852515a1bd4f1076351ef493577df246660e582675bc4f

32-bit GLESv1 CM library        : /vendor/lib/egl/libGLESv1_CM_mtk.so
32-bit GLESv1 CM size           : 554,392 bytes
32-bit GLESv1 CM sha256         : ed4128824cdbb5b1be0a717fbefac831cb49184bf71129cdf823a6d12d48ad17

32-bit GLESv2 library           : /vendor/lib/egl/libGLESv2_mtk.so
32-bit GLESv2 size              : 1,246,648 bytes
32-bit GLESv2 sha256            : 924255a78a51b85160876ed625991324dee1b772d67bcd62129ace12ca9b5317

32-bit MEOW trace library       : /vendor/lib/egl/libMEOW_trace.so
32-bit MEOW trace size          : 370,644 bytes
32-bit MEOW trace sha256        : 01448f3479ae13bdc2d63a8dfec589bc04e907a94841546f06234025630d24ce

OpenCL stock library identity:
64-bit OpenCL library           : /vendor/lib64/libOpenCL.so
64-bit OpenCL size              : 85,456 bytes
64-bit OpenCL context           : u:object_r:same_process_hal_file:s0
64-bit OpenCL sha256            : 3feb689544917c67e5a3ecef82639386dd25c9139e618af2bcb705c6aa2dc553

32-bit OpenCL library           : /vendor/lib/libOpenCL.so
32-bit OpenCL size              : 65,456 bytes
32-bit OpenCL context           : u:object_r:same_process_hal_file:s0
32-bit OpenCL sha256            : 557462a70eeff80729fd69a2e981b3491d367618c39286cf2223c42490a18dce

EGL configuration identity:
Config path                     : /vendor/lib/egl/egl.cfg
Type                            : regular file
Mode                            : -rw-r--r--
UID                             : 0
GID                             : 0
Size                            : 76 bytes
Inode                           : 144516
Links                           : 1
Context                         : u:object_r:same_process_hal_file:s0
SHA256                          : 1606ef2f0ad0dfd812c8ec4ab7b7d9ae5bff3554191c7012530ca883612c70e1
Content line 1                  : # NOTICE: This is an autogenerated file, do not edit\n
Content line 2                  : 0 0 android
Content line 3                  : 0 1 mali
Classification                 : STOCK_VENDOR_EGL_CONFIG_WITH_GRAPHICS_STACK_ANOMALY

Graphics HAL runtime registration:
Allocator HAL                   : android.hardware.graphics.allocator@2.0::IAllocator/default
Composer HAL                    : android.hardware.graphics.composer@2.1::IComposer/default
Mapper HAL                      : android.hardware.graphics.mapper@2.1::IMapper/default
MTK GPU extension HAL           : vendor.mediatek.hardware.gpu@1.0::IGraphicExt/default
Classification                 : LIVE_RUNTIME_HAL_REGISTRATION

Graphics process runtime identity:
Allocator process               : android.hardware.graphics.allocator@2.0-service
Allocator SELinux domain        : u:r:hal_graphics_allocator_default:s0
Allocator user                  : system
Composer process                : android.hardware.graphics.composer@2.1-service
Composer SELinux domain         : u:r:hal_graphics_composer_default:s0
Composer user                   : system
MTK GPU HAL process             : vendor.mediatek.hardware.gpu@1.0-service
MTK GPU HAL SELinux domain      : u:r:mtk_hal_gpu:s0
MTK GPU HAL user                : system
GPU framework service           : gpuservice
GPU framework SELinux domain    : u:r:gpuservice:s0
GPU framework user              : gpu_service
SurfaceFlinger process          : surfaceflinger
SurfaceFlinger SELinux domain   : u:r:surfaceflinger:s0
SurfaceFlinger user             : system
Classification                 : LIVE_RUNTIME_PROCESS_IDENTITY

Runtime GPU kernel threads:
GED threads                     : ged_notify_sw_v, ged_dump_fw_log, ged_kpi
PVR threads                     : pvr_timer, pvr_defer_free, pvr_device_wdg, pvr_cacheop, pvr_fce_status, pvr_misr
Classification                 : LIVE_RUNTIME_GPU_KERNEL_THREAD_EVIDENCE

Anomaly notes:
egl.cfg anomaly                 : egl.cfg declares android and mali entries while runtime renderer is PowerVR Rogue GE8320 and stock vendor graphics stack contains MTK / MEOW / RGX components.
modalias anomaly                : uevent exposes MODALIAS=of:Nmfg_domaT<NULL>Cmediatek,doma while the modalias file read returned blank.
research-state boundary         : live metadata affected by root, Magisk, unlocked bootloader, SELinux permissive state, mounting, or runtime session is not used as stock filesystem truth in this subsection.

GPU identity conclusion:
The C17S core graphics processor is PowerVR Rogue GE8320.
The active runtime GPU driver stack is PVR / RGX through pvrsrvkm on the
mfg_doma@13000000 platform node. The verified Rogue hardware version is
22.87.104.18 (HW), with Rogue_DDK_Android rogueddk 1.11@5425693 runtime driver
and matching 1.11 @ 5425693 RGX firmware runtime report. Stock vendor evidence
contains rgx.fw, MTK graphics HAL modules, Vulkan HAL modules, MEOW EGL/GLES
libraries, and 32-bit / 64-bit OpenCL libraries. Deep display pipeline,
OPP/frequency behavior, GPU daemon dependency mapping, PQ/display tuning,
partition-wide hidden-surface analysis, and BSP ghost cleanup belong outside
Section 1.5.

################################################################################

# 1.6 — RAM & BASE MEMORY IDENTITY

################################################################################

RAM identity:
RAM type                        : LPDDR4
Live MemTotal                   : 6,031,948 kB
Usable memory class             : ~5.75 GiB usable to Linux
NUMA topology                   : single node
CmaTotal (live)                 : 0 kB
CmaFree (live)                  : 0 kB

Live /proc/meminfo full capture:
MemFree                          : 1,047,520 kB
MemAvailable                     : 4,983,248 kB
Buffers                          : 60,700 kB
Cached                           : 3,888,028 kB
SwapCached                       : 58,320 kB
Active                           : 1,367,508 kB
Inactive                         : 3,135,708 kB
Active(anon)                     : 286,168 kB
Inactive(anon)                   : 277,064 kB
Active(file)                     : 1,081,340 kB
Inactive(file)                   : 2,858,644 kB
Unevictable                      : 1,284 kB
Mlocked                          : 1,284 kB
SwapTotal                        : 3,317,564 kB
SwapFree                         : 2,942,176 kB
Dirty                            : 0 kB
Writeback                        : 0 kB
AnonPages                        : 553,616 kB
Mapped                           : 1,115,384 kB
Shmem                            : 7,524 kB
Slab                             : 172,904 kB
SReclaimable                     : 71,256 kB
SUnreclaim                       : 101,648 kB
KernelStack                      : 28,736 kB
PageTables                       : 44,612 kB
CommitLimit                      : 6,333,536 kB
Committed_AS                     : 55,033,620 kB
VmallocTotal                     : 263,061,440 kB
VmallocUsed                      : 0 kB
VmallocChunk                     : 0 kB

Core physical memory base:
System RAM base                 : 0x40000000
Kernel code range                : 0x40080000 — 0x4126ffff
Kernel data range                : 0x41780000 — 0x41f35fff

Live /proc/iomem System RAM blocks (physical, non-contiguous):
Block 1                          : 0x40000000 — 0x47c7ffff
Block 2                          : 0x47dd0000 — 0x77feffff
Block 3                          : 0x79600000 — 0x7fadffff
Block 4                          : 0x7ffc0000 — 0x8cffffff
Block 5                          : 0x8d300000 — 0x9f3effff
Block 6                          : 0x9ff00000 — 0xbdffffff
Block 7                          : 0xbe400000 — 0xbeffffff
Block 8                          : 0xbf300000 — 0xbffbefff
Block 9                          : 0xc0000000 — 0x1bfffefff

Preloader memory-block identity:
mblock magic                    : 0x99999999
mblock version                  : 0x2
Rank count field                : 2
Rank 0 start                    : 0x40000000
Rank 0 captured size field      : 0x07c80000
Rank 1 start                    : 0x47d80000
Rank 1 captured size field      : 0x0

Reserved memory anchors (byte-decoded from live devicetree reg fields):
ram_console                     : 0x47c80000, size 0x10000
pstore                          : 0x47c90000, size 0xe0000
minirdump                       : 0x47d70000, size 0x10000
atf-reserved                    : 0x47d80000, size 0x50000
atf-log-reserved                : 0xbffbf000, size 0x40000
pl-bootarg                      : 0x47dd0000, size 0x200000
framebuffer                     : 0x7fae0000, size 0x420000
SSPM-reserved                   : 0x7ff00000, size 0xc0000
SPM-reserved                    : 0x77ff0000, size 0x10000
SCP-reserved                    : 0x9f900000, size 0x600000
ccci modem memory               : 0x8e000000, size 0x10000000
ccci modem2 memory              : 0x78000000, size 0x1600000
ccci_tag_mem                    : 0x9fff0000, size 0x10000
md_smem_ncache                  : 0x7a000000, size 0x170000
log_store                       : 0x7ffc0000, size 0x40000
dramc-rk0                       : 0xbffff000, size 0x1000
dramc-rk1                       : 0x1bffff000, size 0x1000

Dynamic/CMA-style reserved memory nodes (alignment + alloc-ranges, no fixed reg):
consys-reserve-memory alignment/alloc-ranges : captured live, not fully decoded this pass
reserve-memory-scp_share size               : 0x300000
reserve-memory-scp_share alignment          : 0x1000000
reserve-memory-scp_share alloc-ranges        : base 0x40000000, size 0x50000000
reserve-memory-sspm_share size               : 0x510000
reserve-memory-sspm_share alignment          : 0x10000
reserve-memory-sspm_share alloc-ranges       : base 0x40000000, size 0x60000000
reserve-memory-sspm_share status             : okay
wifi-reserve-memory size                     : 0x300000
wifi-reserve-memory alignment                : 0x1000000
wifi-reserve-memory alloc-ranges             : base 0x40000000, size 0x80000000
zmc-default compatible                       : mediatek,zone_movable_cma
zmc-default size                             : 0xffc00000
zmc-default alignment                        : 0x10000000
zmc-default alloc-ranges                     : base 0xc0000000, size 0x180000000
zmc-default status                           : disabled
zone-movable-cma-memory compatible           : mediatek,zone_movable_cma
zone-movable-cma-memory size                 : 0xffc00000
zone-movable-cma-memory alignment            : 0x10000000
zone-movable-cma-memory alloc-ranges         : base 0xc0000000, size 0x180000000
zone-movable-cma-memory status               : no status property present — OPEN_BOUNDARY

Persistent RAM range — resolved contradiction:
Prior database value (superseded)     : 0x47c80000 — 0x47d5ffff
Live /proc/iomem "persistent_ram" span : 0x47c90000 — 0x47d6ffff
pstore DT node span                    : 0x47c90000 — 0x47d6ffff
Corrected Persistent RAM range         : 0x47c90000 — 0x47d6ffff (= pstore node exactly)
Classification                        : CONTRADICTION_RESOLVED

EMI (External Memory Interface) register identity:
Node path                       : /sys/devices/platform/10219000.emi
Bound driver                    : emi_ctrl
DT compatible                   : mediatek,emi
DT name                         : emi
Modalias                        : of:NemiT<NULL>Cmediatek,emi
driver_override                 : (null)
Reg window 1                    : 0x10219000 — 0x10219fff
Reg window 2                    : 0x10226000 — 0x10226fff
Reg window 3                    : 0x1022d000 — 0x1022dfff
Reg window 4                    : 0x10235000 — 0x10235fff
emi_info,ch_num                 : 2
emi_info,rk_num                 : 2
emi_info,dram_type (raw)        : 01 00 00 03 — undecoded, no reference table
emi_info,rank_size (raw)        : rank0=0x10, rank1=0x20 — undecoded, no reference table
interrupts                      : type=0, num=0x90, flags=0x08
CONFIG_MTK_EMI                  : y

EMI /proc/iomem coalescing anomaly:
Windows 1+2 iomem span           : 10219000-10226fff (coalesced, not two discrete entries)
Windows 3+4 iomem                : individually present, matching DT exactly
Comparison control               : DRAMC's 8 windows (below) do NOT coalesce — rules out generic iomem-merge behavior
Classification                   : OPEN_BOUNDARY

DRAMC (DRAM Controller) register identity:
Node path                       : /sys/devices/platform/1022a000.dramc
Bound driver                    : emi_clk_test
DT compatible                   : mediatek,dramc
DT name                         : dramc
Reg window 1                    : 0x1022a000 — 0x1022bfff
Reg window 2                    : 0x10232000 — 0x10233fff
Reg window 3                    : 0x1022c000 — 0x1022cfff
Reg window 4                    : 0x10234000 — 0x10234fff
Reg window 5                    : 0x10228000 — 0x10229fff
Reg window 6                    : 0x10230000 — 0x10231fff
Reg window 7                    : 0x1022e000 — 0x1022efff
Reg window 8                    : 0x10236000 — 0x10236fff
Live iomem cross-check          : all 8 windows individually confirmed, no coalescing
read_dram_data_rate (live)      : 1534 (unit not stated by driver — undecoded)
CONFIG_MTK_DRAMC                : y
kallsyms dramc symbols          : dcm_dramc_ao(T), dramc_tx_tracking(t), mt_dramc_chn_base_get(T), mt_dramc_nao_chn_base_get(T), mt_dramc_chn_get(T), mt_dramc_chp_get(T), mt_dramc_rankbase_get(T), mt_dramc_ta_support_ranks(T)
Config vs kallsyms               : consistent, no contradiction

DRAMC driver-name anomaly:
DRAMC node bound to driver named : emi_clk_test (not dramc-named)
Modalias confirms genuine OF match : yes
Classification                    : OPEN_BOUNDARY (cosmetic/organizational)

EMI/DRAMC security-relevant writable driver surfaces:
concurrency_scenario mode/owner  : -rw-rw-r-- media:media
mpu_config mode/owner            : -rw-r--r-- root:root
emi_clk_mem_test mode/owner      : -rw-rw-r-- root:root
read_dram_data_rate mode/owner   : -rw-rw-r-- root:root
Classification                   : ANOMALOUS_MEMORY_CONTROLLER_WRITABLE_SURFACE

Live EMI MPU region table — FULL 26 REGIONS (R0–R25), parsed programmatically from raw live capture, byte-exact against source. C1-C16 = 16 raw permission cells per region in original capture order (lines 1-2 = block A, lines 3-4 = block B). No domain/master labels are asserted for these columns — none were provided by the driver output.

| Region | Base | End | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | C9 | C10 | C11 | C12 | C13 | C14 | C15 | C16 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| R0 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R1 | 0x47d80000 | 0x47dcffff | S_RW | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R2 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R3 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R4 | 0x9f3f0000 | 0x9f8fffff | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R5 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R6 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R7 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R8 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R9 | 0x8e000000 | 0x8f22ffff | S_R_NS_R | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R10 | 0x95000000 | 0x956fffff | S_R_NS_R | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R11 | 0x95700000 | 0x95dfffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R12 | 0x92800000 | 0x9280ffff | S_R_NS_R | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R13 | 0x91bd0000 | 0x9264ffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R14 | 0x90800000 | 0x91bcffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R15 | 0x8fc00000 | 0x9001ffff | S_R_NS_R | S_R_NS_R | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R16 | 0x95000000 | 0x95dfffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R17 | 0x92800000 | 0x9280ffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R18 | 0x92660000 | 0x9266ffff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R19 | 0x92680000 | 0x926affff | S_R_NS_R | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R20 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R21 | 0x40000000 | 0x4000ffff | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| R22 | 0x78000000 | 0x795fffff | No | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R23 | 0x7a000000 | 0x7a16ffff | No | No | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R24 | 0xbe140000 | 0xbe26ffff | FORBIDDEN | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN |
| R25 | 0xbe000000 | 0xbe3fffff | No | FORBIDDEN | No | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | FORBIDDEN | CAPTURE_TRUNCATED |

R25/C16 root-cause finding (sysfs page-size truncation, not a capture error):
Driver output byte length (measured, R0 through point of prompt collision) : 4,095 bytes
Standard kernel sysfs page size                                             : 4,096 bytes
Delta                                                                        : 1 byte short of page boundary
Reproducibility                                                              : identical truncation point on two independent live captures, run at different times
Classification                                                               : LIVE_PROVEN_SYSFS_PAGE_SIZE_TRUNCATION — driver-level defect, not a terminal/paste artifact
Recoverability                                                               : CLOSED — CONFIRMED PERMANENTLY UNRECOVERABLE VIA THIS SYSFS NODE. Live byte-offset read (dd skip=4090 count=40) returned only 5 bytes total ("DEN, ", the tail of R25/C15's own "FORBIDDEN, " value) before EOF, proving the kernel buffer is exactly 4,095 bytes and terminates immediately after C15. C16 was never written into the buffer by the driver — this is not a display or capture limitation, it is an absence in the source data itself. Recovery would require direct review of the ODM's mpu_config show() function source code to determine the coded value; not obtainable from the live device by any read method.

Cross-reference findings, full table vs. reserved-memory nodes:
R1  = atf-reserved exactly (0x47d80000–0x47dcffff)
R9  = partial overlap with ccci modem memory; MPU boundary (0x8f22ffff) short of full DT reservation end (0x9dffffff)
R22 = ccci modem2 memory exactly (0x78000000–0x795fffff)
R23 = md_smem_ncache exactly (0x7a000000–0x7a16ffff)
R4  = adjacent to but not overlapping SCP-reserved (0x9f3f0000–0x9f8fffff vs. 0x9f900000–...)
R10 = R16 duplicate range declaration (0x95000000–0x95dfffff), near-identical permission pattern (differs at C2/C7)
R12 = R17 duplicate range declaration (0x92800000–0x9280ffff), identical permission pattern
R9, R10–R19 : contiguous modem-adjacent cluster (0x8e000000–0x9dffffff span), no matching entry among the 22 reserved-memory DT nodes except R9's partial ccci overlap
R24, R25 : both fall in the physical gap between /proc/iomem System RAM Block 6 (ends 0xbdffffff) and Block 7 (starts 0xbe400000); unmatched to any named DT node
R0, R2, R3, R5, R6, R7, R8, R20, R21 : identical placeholder range (0x40000000–0x4000ffff) with all-No permissions — consistent with unused/inactive MPU region table slots

EMI BWL (Bandwidth Limiter) live scenario state:
Active scenario                  : BWL_SCN_UI = 0x1
Inactive scenarios               : BWL_SCN_VPWFD=0, BWL_SCN_VR4K=0, BWL_SCN_ICFP=0
Registers captured               : CEN 0x100–0x138; CH0/CH1 0x48, 0x80, 0x88, 0x140, 0x144, 0x158, 0x15c

Kernel config vs. live kernel symbol table — EMI_MPU / EMI_BWL contradiction:
CONFIG_MTK_EMI_MPU                : not set
CONFIG_MTK_EMI_BWL                : not set
CONFIG_MTK_COMMON_EMI             : not set
Live kallsyms EMI_MPU symbols (T)  : mt_emi_mpu_base_get, emi_mpu_set_protection, emi_mpu_set_single_permission, emi_mpu_clear_protection, emi_mpu_check_register, sspm_lock_emi_mpu, sspm_set_emi_mpu
Live kallsyms EMI_BWL symbols (T)  : decode_bwl_env, acquire_bwl_ctrl, release_bwl_ctrl, bwl_ctrl, bwl_init, mm_qos_update_larb_bwl, set_force_bwl, get_force_bwl
/proc/modules match                : none (not loadable modules)
/sys/module match                  : none (consistent with built-in status)
Classification                     : CONTRADICTION_CONFIRMED — root cause OPEN_BOUNDARY, requires raw image/ODM source comparison

Debugfs memory-controller node:
Path                              : /sys/kernel/debug/emi_mbw
Type                               : directory
Contents                           : dump_buf
dump_buf mode/owner/context        : -r--r--r-- root:root u:object_r:debugfs_emi_mbw_buf:s0
dump_buf content                   : not read this pass — OPEN_BOUNDARY
/sys/kernel/debug/emi_mpu          : absent
/proc/emi_mpu                      : absent
/proc/emi_info                     : absent

ZRAM identity:
Device                           : /dev/block/zram0
Sysfs disksize                   : 3,397,189,632 bytes
/proc/partitions size            : 3,317,568 kB
SwapTotal                        : 3,317,564 kB
Active compression algorithm     : lz4
Available algorithms             : lzo, lz4, deflate
Swap priority                    : -1
Symlink target                   : ../devices/virtual/block/zram0
Symlink SELinux context          : u:object_r:sysfs:s0
Symlink timestamp                : 2025-06-13 02:34

Live ZRAM mm_stat (capture-time):
orig_data_size                   : 466,931,712 bytes
compr_data_size                  : 116,541,570 bytes
mem_used_total                   : 139,157,504 bytes
mem_limit                        : 0
mem_used_max                     : 154,288,128 bytes
same_pages                       : 7,871
pages_compacted                  : 1,864
Compression ratio (capture-time) : ~4.007:1

vm.swappiness                    : 60

Kernel zone state:
/proc/zoneinfo zones             : DMA, Normal (Node 0)
DMA start_pfn                    : 262,144
Normal start_pfn                 : 1,048,576
/proc/pagetypeinfo zones         : DMA, Normal (consistent)
/sys/devices/system/memory       : absent
/sys/devices/system/node         : absent

Swap/partition memory-relevant cross-reference:
/proc/swaps                      : zram0 only, matches SwapTotal exactly
ram0–ram15                       : 4096 blocks each
Full partition table             : primary section = Storage/Partition Geometry (cross-reference only, not expanded here)

Memory policy anchors:
vmalloc cmdline                  : 400M
dalvik.vm.heapgrowthlimit        : 256m
dalvik.vm.heapsize               : 512m
ro.lmk.downgrade_pressure        : 70


################################################################################

Open boundaries — Section 1.6:

1. DUPLICATE_CMA_NODE_DECLARATION — zmc-default / zone-movable-cma-memory identical compatible/size/alignment/alloc-ranges; only status differs (disabled vs. no status property).
2. DRAMC_RESERVED_RANK_REGIONS_PURPOSE — dramc-rk0 (0xbffff000) / dramc-rk1 (0x1bffff000) purpose unconfirmed; distinct from preloader Rank 0/Rank 1 start fields.
3. EMI_IOMEM_COALESCING — EMI windows 1+2 coalesce in /proc/iomem; DRAMC's 8 windows do not.
4. EMI_MPU_R9_PARTIAL_COVERAGE — R9 MPU boundary (0x8f22ffff) short of full ccci DT reservation end (0x9dffffff).
5. EMI_MPU_BWL_CONFIG_CONTRADICTION_ROOT_CAUSE — contradiction proven via kallsyms; root cause requires raw image/ODM source.
6. DRAMC_DRIVER_NAME_MISMATCH — DRAMC node bound to driver named emi_clk_test.
7. EMI_MBW_DUMP_BUF_CONTENT_UNREAD — content not yet captured.
8. RESERVED_MEMORY_NODE_COUNT_RECONCILIATION — 22 nodes documented this pass vs. 24 referenced in prior session context; unreconciled.
9. PL_BOOTARG_NOT_REVERIFIED — retained from prior database; not independently re-touched live this pass.
10. EMI_MPU_R25_C16_VALUE_UNKNOWN — sysfs page-size truncation confirmed via byte-offset read; C16 value for R25 was never written to the driver's buffer and does not exist on the live device. Requires ODM driver source review to determine, not a live-device-resolvable item.
11. EMI_MPU_DUPLICATE_REGION_R10_R16 — identical range 0x95000000–0x95dfffff declared twice (R10, R16), near-identical permission pattern (differs C2/C7).
12. EMI_MPU_DUPLICATE_REGION_R12_R17 — identical range 0x92800000–0x9280ffff declared twice (R12, R17), identical permission pattern.
13. EMI_MPU_UNMATCHED_MODEM_CLUSTER — R9, R10–R19 span 0x8e000000–0x9dffffff with no matching reserved-memory DT node except R9's partial ccci overlap.
14. EMI_MPU_UNMATCHED_GAP_REGIONS_R24_R25 — both fall in the physical gap between iomem System RAM Blocks 6 and 7 (0xbe000000–0xbe3fffff range); no matching DT node.
15. EMI_MPU_R4_ADJACENCY — R4 (0x9f3f0000–0x9f8fffff) sits immediately adjacent to but does not overlap SCP-reserved (0x9f900000–...).

################################################################################

# 1.7 — KERNEL PLATFORM IDENTITY

################################################################################
#
# SCOPE : Kernel build identity, kernel command line, devicetree platform identity,
#         boot/LK/base-DTB/DTBO/live-FDT stage mapping, compiled kernel config,
#         kernel modules, firmware-class payloads, filesystem/crypto/DM runtime,
#         debugfs/tracefs/pstore/log/keyring exposure, sysctl/SELinux kernel
#         policy surfaces, and kernel-influence surfaces below userspace ELF level.
#
# DATABASE CLASS : HARDWARE DATABASE / KERNEL PLATFORM IDENTITY
# STATUS         : CLOSED_FOR_CURRENT_EVIDENCE_SET
#
# WITNESS TAGS (per field):
#   LIVE    = live rooted device capture where runtime state is the authority
#   CFG     = /proc/config.gz preserved kernel-config witness
#   DT      = /proc/device-tree -> /sys/firmware/devicetree/base live-applied FDT view
#   FS      = on-disk vendor filesystem witness (/vendor/lib/modules, /vendor/firmware)
#   BOOT    = raw boot.bin / boot.img binary-phase witness
#   BDTB    = boot.bin embedded DT_TABLE entry0 FDT / decompiled base DTS witness
#   DTBO    = raw dtbo.bin entry0 FDT / decompiled overlay DTS witness
#   DTC     = device-tree-compiler output/warning witness; warnings preserved as data
#   LK      = lk.bin bootloader binary witness and corrected LK evidence directory
#   LKFDT   = LK-embedded lk_main_dtb payload carved from lk.bin
#   AFDT    = host-applied FDT generated by applying dtbo_entry0.dtb onto boot base FDT with fdtoverlay
#   LFDT    = live kernel-applied FDT copied from /sys/firmware/fdt
#   KRUN    = preserved kernel runtime pseudo-files: /proc/config.gz, /proc/modules,
#             /proc/filesystems, /proc/crypto, /proc/self/mountinfo, sysctl/tunables
#   KMOD    = vendor module binary files under /vendor/lib/modules
#   KLOG    = /dev/kmsg, /proc/kmsg, and dmesg runtime exposure
#   KEYRING = /proc/keys, /proc/key-users, /proc/sys/kernel/keys runtime exposure
#
# CLASSIFICATION TAGS:
#   PER_BOOT_VARIANT                 : value regenerated/repopulated each boot
#   NOT_CONFIRMED_ACTIVE             : compiled capability; runtime activation not proven
#   CONFIRMED_ABSENT                 : proven absent by enumeration or byte test
#   OPEN_BOUNDARY                    : semantic/cause boundary requiring structure definitions,
#                                      binary control-flow proof, or endpoint-owner reconciliation
#   OPEN_OPTIONAL                    : retained semantic boundary not blocking closure
#   CLOSED_AS_STATIC_ONLY            : true for raw boot-FDT/static source stage only
#   CLOSED_BY_DTBO                   : static/open item resolved by dtbo entry0 overlay content
#   CLOSED_AS_STANDALONE_OVERLAY_ONLY: standalone DTBO warning/parser/fixup class; base application closed
#   TARGETED_NEGATIVE_LEAD           : explicitly queried and absent, with correct alternate node/path recorded
#   LIVE_RESEARCH_STATE              : value affected by current unlocked/rooted/permissive research state
#   XREF:n                           : evidence owned by / corroborating Section n
#
# NOTE ON DT INTEGER CELLS: FDT numeric cells are big-endian by spec. Raw bytes are
# recorded verbatim; integer interpretation is stated when applied.
################################################################################

################################################################################
# KERNEL BUILD IDENTITY
################################################################################

Kernel build identity:
Kernel version                  : Linux 4.9.190+
Kernel build instance           : #1
Kernel config class             : SMP PREEMPT
Kernel architecture             : aarch64
Kernel build date               : Fri Jun 13 07:31:05 UTC 2025
Build compiler                  : Clang 9.0.3
Android LLVM base               : 5484270 based on r353983c
LLVM base                       : LLVM 9.0.3svn
Clang toolchain commit          : 745b335211bb9eadfa6aa6301f84715cee4b37c5
LLVM toolchain commit           : 60cf23e54e46c807513f7a36d0a7b777920b5881
Kernel compile user@host        : nobody@android-build          # /proc/version compile string; distinct from ro.build.user
Cross-compile prefix            : aarch64-linux-gnu-            # CFG CONFIG_CROSS_COMPILE
Kernel localversion             : "" (empty)                    # CFG CONFIG_LOCALVERSION
Default hostname                : (none)                        # CFG CONFIG_DEFAULT_HOSTNAME

Kernel version cross-witnesses (three independent sources, all agree):
uname -a                        : Linux localhost 4.9.190+ #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025 aarch64   # LIVE
/proc/version                   : Linux version 4.9.190+ (nobody@android-build) (clang 9.0.3 ...) #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025   # LIVE
/proc/sys/kernel/osrelease      : 4.9.190+                      # LIVE
/proc/sys/kernel/version        : #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025   # LIVE

Android build identity (distinct witnesses; do not collapse with kernel compile string):
ro.build.user                   : dengjun                       # LIVE getprop
ro.build.host                   : wtsv                          # LIVE getprop
ro.build.date                   : Fri Jun 13 07:23:14 UTC 2025  # LIVE getprop
ro.build.date.utc               : 1749799394                    # LIVE getprop; = ro.build.date (math-verified: epoch 1749799394 = Fri Jun 13 07:23:14 UTC 2025)
ro.vendor.build.date            : Fri Jun 13 08:11:06 UTC 2025  # LIVE getprop

Build-stage timestamp spread (three distinct build stages, each an independent witness):
System build date               : Fri Jun 13 07:23:14 UTC 2025  # ro.build.date
Kernel build date               : Fri Jun 13 07:31:05 UTC 2025  # /proc/version   (+8m from system)
Vendor build date               : Fri Jun 13 08:11:06 UTC 2025  # ro.vendor.build.date (+48m from system)


################################################################################
# KERNEL COMMAND LINE
################################################################################
# LIVE /proc/cmdline byte-verified via od -c (622 bytes, \n-terminated).
# DT SOURCE: /proc/device-tree/chosen/bootargs (670 bytes, \0-terminated) is
# byte-identical to /proc/cmdline for all token content this boot — the running
# kernel command line is devicetree /chosen-sourced. XREF:3 (boot.img header diff).

Kernel command-line identity values:
console                         : tty0
console (serial)                : ttyS0,921600n1
vmalloc                         : 400M
slub_debug                      : OFZPU
page_owner                      : on
swiotlb                         : noforce
androidboot.hardware            : mt6765                        # silicon-ID split witness (see 1.7.3)
maxcpus                         : 8
loop.max_part                   : 7
firmware_class.path             : /vendor/firmware              # module firmware-request path; populated (see 1.7.9)
has_battery_removed             : 0                             # present live; was absent from prior 1.7 draft
androidboot.boot_devices        : bootdevice,11230000.mmc
root                            : /dev/ram                      # byte-confirmed double space (0x20 0x20) follows before next token
androidboot.verifiedbootstate   : orange
bootopt                         : 64S3,32N2,64N2
buildvariant                    : user
androidboot.atm                 : disabled
androidboot.meta_log_disable    : 0
printk.disable_uart             : 0
bootprof.pl_t                   : 1130                          # PER_BOOT_VARIANT (see below)
bootprof.lk_t                   : 8086                          # PER_BOOT_VARIANT
bootprof.logo_t                 : 999                           # PER_BOOT_VARIANT
androidboot.serialno            : MTK0002507301608400
androidboot.bootreason          : PowerKey
gpt                             : 1
usb2jtag_mode                   : 0
mrdump_ddrsv                    : yes
mrdump_cb                       : 0x10e800,0x1400
androidboot.dtb_idx             : 0
androidboot.dtbo_idx            : 0                             # dtbo overlay selection active

bootprof timing — per-boot-variant proof (three separate boot captures):
Prior database capture          : pl_t=1074  lk_t=8057  logo_t=999
Prior 1.7 draft                 : pl_t=1128  lk_t=8071  logo_t=998
This live capture               : pl_t=1130  lk_t=8086  logo_t=999
Classification                  : PER_BOOT_VARIANT — three distinct captures; bootprof counters are boot-instance values, not fixed identity

androidboot.usbcontroller — correction (source misattribution):
Prior 1.7 draft value           : musb-hdrc  (listed as a cmdline token)
/proc/cmdline (plain + od -c)    : token ABSENT — "usbcontroller" not present at byte level
getprop androidboot.usbcontroller: empty (property not set)
Real source (LIVE)              : /sys/class/udc -> musb-hdrc
udc symlink target              : ../../devices/platform/mt_usb/musb-hdrc/udc/musb-hdrc
Classification                  : value real; source is the mt_usb platform UDC node, NOT the kernel command line

Command-line byte anomalies:
Double space                    : between root=/dev/ram and androidboot.verifiedbootstate (0x20 0x20) — byte-confirmed od -c; cause OPEN_BOUNDARY


################################################################################
# DEVICETREE PLATFORM IDENTITY
################################################################################
# DT = /proc/device-tree (symlink -> /sys/firmware/devicetree/base, LIVE)

Devicetree root identity:
/proc/device-tree symlink       : -> /sys/firmware/devicetree/base
dt.compatible                   : mediatek,MT6765               # od -c 16B: "mediatek,MT6765"\0 — single \0-terminated string
dt.model                        : MT6762V/WB                    # od -c 11B: "MT6762V/WB"\0 — single \0-terminated string
dt.#address-cells               : 2                             # od -c BE=0x00000002 — root uses 2-cell (64-bit) addresses
dt.#size-cells                  : 2                             # od -c BE=0x00000002 — root uses 2-cell (64-bit) sizes
dt.root name property           : empty                         # cat returned empty (root node name conventionally empty)
dt.chosen sysfs-listing count   : 38                            # /proc/device-tree/chosen listing witness
dt.chosen FDT real-property count: boot base 4 ; host-applied 11 ; live FDT 36
dt.root sysfs-listing count     : 370                           # /proc/device-tree top-level listing witness
dt.root FDT subnode count        : boot base 366 ; host-applied 366 ; live FDT 368

Silicon-ID layer split — six live witnesses (structural, by design; XREF:1.2 physical MT6762V/WB vs BSP MT6765):
androidboot.hardware (cmdline)  : mt6765                        # BSP/platform layer
ro.boot.hardware (getprop)      : mt6762                        # physical/boot layer
ro.hardware (getprop)           : mt6762                        # physical/boot layer
ro.board.platform (getprop)     : mt6765                        # BSP/platform layer
dt.compatible                   : mediatek,MT6765               # DT binding = BSP platform
dt.model                        : MT6762V/WB                    # DT model = physical silicon
Split pattern                   : hardware/boot layer reports MT6762 (physical); platform/BSP/DT-binding layer reports MT6765; both IDs coexist in one devicetree (model vs compatible)


################################################################################
# DEVICETREE /chosen NODE  (38 properties — kernel-consumed boot params)
################################################################################
# Mixed population: /chosen contains static-template, DTBO-applied, and live bootloader/runtime fields.
# Source-class correction: live sysfs pseudo-file metadata is not stock-static unless raw/static source proves it.
# All values byte-captured via od -c. DT integer cells interpreted big-endian.
# atag struct field semantics require MTK preloader/LK struct defs — OPEN (see boundaries).

chosen.bootargs                 : 670B — byte-identical to /proc/cmdline
chosen.atag,devinfo             : 804B live FDT `/chosen` payload — contains >=3 16-byte high-entropy fields, dominant repeating word 0x00000766, 0xc8 header candidate; stock-static source class not asserted; full struct decode OPEN (XREF:1.2/1.11)
chosen.atag,masp                : 88B — MTK security atag; ASCII hex ID field below
chosen.atag,masp hex_id         : 1A52A367CB12C458              # od -c, 16 ASCII hex chars (64-bit); NOT ME_ID, NOT SOC_ID (both REVERIFY_REQUIRED, not valid anchors); semantic OPEN (XREF:1.2/1.11)
chosen.atag,mem                 : 48B — ASCII "BT" x2, word 0x40000000 (=1 GiB); field struct OPEN (XREF:1.6)
chosen.atag,boot                : 12B — 03 00 00 00 | 02 08 00 41 | 00 00 00 00 ; struct OPEN
chosen.atag,boot_voltage        : 1B  — "0" (0x30)
chosen.atag,mdinfo              : 12B — 03 00 00 00 | 06 08 00 41 | 00 00 00 00 ; struct OPEN (XREF:9)
chosen.atag,ptp                 : 24B — 06 00 00 00 | 08 00 41 54 ("AT") | 16x 00 ; body zero; struct OPEN (XREF:6 SVS/DVFS calib)
chosen.atag,fg_swocv_i          : 5B  — prior ASCII "-1396"; latest raw ASCII "-1298", no NUL terminator; `fdtget -t s` returns `Unterminated string`; PER_BOOT_VARIANT (XREF:6)
chosen.atag,fg_swocv_v          : 5B  — prior ASCII "41755"; latest raw ASCII "41937", no NUL terminator; `fdtget -t s` returns `Unterminated string`; PER_BOOT_VARIANT (XREF:6)
chosen.atag,imix_r              : 4B  — prior raw `cb 00 00 00`; latest raw `db 00 00 00`; PER_BOOT_VARIANT / semantic OPEN (XREF:6)
chosen.atag,shutdown_time       : 1B  — "0" (0x30)
chosen.atag,two_sec_reboot      : 1B  — "0" (0x30)
chosen.atag,videolfb            : 52B — hdr words then ASCII "lk_st7701S_V3_480X640_dsi_vdo"\0 (XREF:8)
chosen.atag,videolfb-lcmname    : 36B — "nt35695B_fhd_dsi_cmd_auo_rt5081_drv"\0 (XREF:8)
chosen.atag,videolfb-fb_base_h  : 4B  — BE 0x00000000 (framebuffer base high word)
chosen.atag,videolfb-fb_base_l  : 4B  — BE 0x5e605000 (framebuffer base low word; combined 64b base 0x000000005e605000) (XREF:8)
chosen.atag,videolfb-vramSize   : 4B  — BE 0x017bb000 = 24,887,296 bytes (23.73 MiB) (XREF:8)
chosen.atag,videolfb-fps        : 4B  — BE 0x00001770 = 6000 (scaling unstated; XREF:8)
chosen.atag,videolfb-islcm_inited : 4B — BE 0x00000000 (LCM inited flag = 0)
chosen.atag,videolfb-islcmfound : 4B  — BE 0x00000001 (LCM found flag = 1)
chosen.ccci,modem_info_v2       : 48B — 00 00 ff 9f | 00 00 00 00 | 80 0c 00 00 | 00...| 03 00 00 00 | 1b 00 00 00 | 01 00 00 00 | zero-fill; struct OPEN (XREF:9); two-witness confirmed
chosen.dfd,base_addr            : 8B  — 00 00 00 00 | 40 10 00 00 ; MTK DFD debug facility; struct OPEN
chosen.dfd,base_addr_msb        : 4B  — 7a 10 00 00 ; struct OPEN
chosen.kaslr-seed               : 8B  — all 0x00 (see KASLR resolution in 1.7.7)
chosen.linux,initrd-start       : 4B  — 51 b0 00 00 (BE 0x51b00000 initrd start)
chosen.linux,initrd-end         : 4B  — 51 be 17 cd (BE 0x51be17cd initrd end; BE size = 0xe17cd = 922,061 bytes)
chosen.linux,phandle            : 4B  — BE 0x0000004b (75)
chosen.phandle                  : 4B  — BE 0x0000004b (75)  — matches linux,phandle
chosen.log_store                : 8B  — 00 e7 10 00 | 00 01 00 00 ; addr/size candidate; struct OPEN
chosen.non_secure_sram          : 8B  — 00 dc 10 00 | 00 44 00 00 ; addr/size candidate; struct OPEN
chosen.ram_console              : 16B — 00 dc 10 00 | 00 08 00 00 | 01 00 00 00 | c0 0a 00 00 ; struct OPEN
chosen.plat_dbg_info,base       : 12B — 00 10 e4 0c | 00 10 e4 58 | 00 10 e4 70 ; three addr candidates; struct OPEN
chosen.plat_dbg_info,key        : 12B — 00 00 d8 a3 | 00 00 e3 1c | 00 00 db 45 ; struct OPEN
chosen.plat_dbg_info,size       : 12B — BE 0x4c, 0x18, 0x10 (76, 24, 16) ; struct OPEN
chosen.plat_dbg_info,max        : 4B  — BE 0x00000003 (3)
chosen.name                     : "chosen"


################################################################################
# DEVICETREE ROOT NODE INVENTORY  (370 top-level nodes; identity subset)
################################################################################
# Full FDT node namespace bound by the kernel. Peripheral nodes XREF to owning
# sections; only kernel/platform-identity and anomaly-bearing nodes tabled here.

Kernel/platform-identity root nodes (1.7-native):
compatible, model, chosen, cpus, memory, reserved-memory, psci, timer, clocks,
#address-cells, #size-cells, __symbols__, interrupt-parent,
interrupt-controller@0c000000, dynamic_options, firmware, memory-ssmr-features

cpus node (CPU topology binding; XREF:1.4 full topology):
cpus children                   : #address-cells, #size-cells, cpu-map, idle-states, name
cpu nodes                       : cpu@000 cpu@001 cpu@002 cpu@003 cpu@100 cpu@101 cpu@102 cpu@103
Cluster split                   : cluster0 = cpu@000..003 ; cluster1 = cpu@100..103 (8 cores, 2 clusters)

firmware node:
firmware children               : android, name                # XREF:1.11 (AVB/verifiedboot)
firmware/android/compatible     : android,firmware              # live FDT closeout; supersedes earlier empty current capture
firmware/android/hardware       : mt6762
firmware/android/mode           : normal
firmware/android/serialno       : MTK0002507301608400

Silicon/security identity nodes (XREF:1.2 / 1.11):
chipid@08000000, efuse_dbg@10004000, efusec@11c50000, dxcc_sec@10210000,
security_ao@1001a000, trng@1020f000, hacc@1000a000, aes_top0@10016000,
sramrom@10214000, sspm@10440000, scp@10500000, mobicore, utos, utos_tester, tkcore

ODM / non-standard root nodes (NOT standard MTK BSP node names):
wiite_con                       : DT children — compatible, linux,phandle, name, phandle, pinctrl-0, pinctrl-1, pinctrl-names, wiite,con1, wiite,con2 ; content -> Section 7 (XREF:7). OPERATOR_HYPOTHESIS: UART/I2C bridge to nRF/Android; structurally consistent (pinctrl + two custom endpoints); NOT asserted
wiite_corp                      : DT children — compatible, linux,phandle, name, phandle, status, vhrs-supply ; content -> Section 7 (XREF:7)
heart                           : non-standard node; purpose OPEN_BOUNDARY
gse_1                           : non-standard node; purpose OPEN_BOUNDARY
amms_control                    : non-standard node; purpose OPEN_BOUNDARY
radio_md_cfg                    : non-standard node (XREF:9)
chr_stat, lk_charger, pd_adapter, rt9465_slave_chr : charger/power ODM nodes (XREF:6)

DT node-name / duplicate anomalies (byte-confirmed from ls; cause OPEN):
ddrphy@10015000 + ddrphy@1001e000            : two ddrphy nodes (XREF:1.6)
dramc0@1001d000 + dramc@1022a000 + dramc_nao@1021d000 : three dramc nodes (XREF:1.6)
dvfsp@00110800 + dvfsp@10227000              : two dvfsp nodes, different base
i2c0..i2c6 (addressed) + i2c7 / i2c8 / i2c9 (no address) : i2c7-9 unaddressed (XREF:4)
syscfg_pctl_1@0x10002000 .. syscfg_pctl_7    : literal "0x" prefix inside node name (7 nodes); syscfg_pctl_0@10005000 has none (XREF:4/5)
iocfg_0..5 + io_cfg_bl/lb/lm/lt/rb/rr/rt     : two parallel iocfg naming schemes (XREF:4)
msdc@11230000/11240000/11250000/11260000 + msdc0_top/msdc1_top/pad_macro : (XREF:2)
dbg_apmcu_mp0@0d4-0d7 (16) + dbg_apmcu_mp1@0d8-0db (16) : 32 CPU debug-APB nodes (XREF:1.3)


################################################################################
# COMPILED KERNEL CONFIG ANCHORS
################################################################################
# CFG source = frozen artifact c17s_config_live.txt (sha256 5d96ffc7...ecccddf).
# Line numbers reference the frozen 4660-line artifact.

Kernel encryption capability anchors (compiled capability; not runtime-activation proof):
CONFIG_EXT4_ENCRYPTION          : y          # L4030
CONFIG_EXT4_FS_ENCRYPTION       : y          # L4031
CONFIG_FS_ENCRYPTION            : y          # L4049
CONFIG_DM_CRYPT                 : y          # L1867
CONFIG_HIE                      : y          # L4440 (MTK hardware inline encryption)
CONFIG_MTK_HW_FDE               : y          # L1883 — NOT_CONFIRMED_ACTIVE (runtime mode is FBE, see 1.7.7)
CONFIG_DM_BOW                   : y          # L1886
CONFIG_CRYPTO_AES_ARM64_CE      : y          # L4591
CONFIG_CRYPTO_AES_ARM64_CE_BLK  : y          # L4593
CONFIG_CRYPTO_SHA2_ARM64_CE     : y          # L4589
CONFIG_RD_GZIP                  : y          # L160
CONFIG_RD_LZ4                   : not set    # L165
CONFIG_CRYPTO_ADIANTUM          : not set    # L4503

dm-verity / AVB compiled state (XREF:1.11 AVB null-state):
CONFIG_DM_VERITY                : y          # L1879
CONFIG_DM_VERITY_FEC            : y          # L1880
CONFIG_DM_VERITY_AVB            : not set    # L1884

Kernel KASLR / relocation (compiled):
CONFIG_ARCH_HAS_ELF_RANDOMIZE       : y
CONFIG_RANDOMIZE_BASE               : y      # kernel KASLR compiled in
CONFIG_RANDOMIZE_MODULE_REGION_FULL : y
CONFIG_ARM64_RANDOMIZE_TEXT_OFFSET  : not set
CONFIG_RELOCATABLE                  : y
CONFIG_ARM64_MODULE_PLTS            : y

Module subsystem (compiled):
CONFIG_MODULES                  : y
CONFIG_MODULE_FORCE_LOAD        : not set
CONFIG_MODULE_FORCE_UNLOAD      : not set
CONFIG_MODULE_SIG               : not set    # modules NOT signature-enforced

MTK-namespace config scale:
CONFIG_MTK* / CONFIG_HIE lines  : 398        # count in frozen artifact (matches live)

ext2/ext3 registration explanation (resolves /proc/filesystems vs config):
CONFIG_EXT2_FS                  : not set
CONFIG_EXT3_FS                  : not set
CONFIG_EXT4_FS                  : y
CONFIG_EXT4_USE_FOR_EXT2        : y          # ext4 driver registers under ext2/ext3 filesystem names


################################################################################
# RUNTIME KERNEL STATE
################################################################################

Runtime encryption mode (FBE active; distinct evidence class from compiled CONFIG_* above):
ro.crypto.state                 : encrypted                     # LIVE getprop (enumerated)
ro.crypto.type                  : file                          # LIVE getprop — FBE (File-Based Encryption), not FDE
FBE contents mode               : aes-256-xts                   # LIVE /data/unencrypted/mode bytes (left of pair)
FBE filenames mode              : aes-256-cts                   # LIVE getprop + mode-file (two witnesses, right of pair)
/data/unencrypted/mode          : "aes-256-xts:aes-256-cts"     # LIVE od -c, 23 bytes
ro.crypto.volume.filenames_mode : aes-256-cts                   # LIVE getprop (present)
ro.crypto.volume.contents_mode  : CONFIRMED_ABSENT (property)   # value present in storage layer only (mode file); property not exposed. OPEN_BOUNDARY: AOSP-normal vs ODM-omission
ro.crypto.fs_crypto_blkdev      : CONFIRMED_ABSENT (property)   # FDE-specific; absence consistent with FBE
ro.crypto.fde_algorithm         : CONFIRMED_ABSENT (property)   # FDE-specific; absence consistent with FBE

Kernel tunables / policy identity nodes (LIVE /proc/sys/kernel):
kptr_restrict                   : 2                             # confirmed cause of zeroed module + kallsyms addresses
tainted                         : 4609                          # = bits 0+9+12 (arith-verified 1+512+4096=4609)
tainted bit 0                   : (P) proprietary module loaded
tainted bit 9                   : (W) kernel issued a warning since boot  # KERNEL_WARN_EMITTED; dmesg cause OPEN
tainted bit 12                  : (O) out-of-tree module loaded
osrelease                       : 4.9.190+
version                         : #1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025
arch (sysctl)                   : CONFIRMED_ABSENT              # does not exist on 4.9 kernels (expected, not anomaly)
modules_disabled                : 0                             # runtime module loading ENABLED
panic                           : 1                             # panic reboots after 1 second
panic_on_oops                   : 1                             # oops escalates to panic
printk                          : 7 4 1 7                       # console=7 default=4 minimum=1 boot-default=7 (tab-separated, byte-verified)
randomize_va_space              : 2                             # full userspace ASLR active (stack+heap+mmap+brk)
perf_event_paranoid             : 3                             # most restrictive perf gating
random/boot_id                  : ad521cce-c647-434d-8081-9533057c1c71  # PER_BOOT_VARIANT

Kernel KASLR resolved state:
Compiled                        : CONFIG_RANDOMIZE_BASE=y + CONFIG_RELOCATABLE=y (KASLR built-in, capable)
DT seed                         : chosen/kaslr-seed = 0x0 (all-zero, byte-confirmed)
Classification                  : KASLR_COMPILED_ACTIVE_CAPABLE + DT_SEED_ZERO_AT_READ — zero DT seed does not by itself prove KASLR inactive (may be post-consumption cleared presentation); resolving (bootloader-supplied-zero vs consumed-then-zeroed) requires boot-log KASLR offset or kernel-image analysis — OPEN

Module-load security posture (factual capability statement):
CONFIG_MODULE_SIG=not set + modules_disabled=0 + no FORCE_LOAD/UNLOAD : kernel accepts unsigned module loads; loading not runtime-locked (consistent with loaded out-of-tree (O)/(PO) modules)

Symbol table:
kallsyms names                  : VISIBLE (T/t types present; e.g. _stext, do_undefinstr, gic_handle_irq, vectors)   # LIVE head
kallsyms addresses              : 0x0000000000000000 (all zeroed by kptr_restrict=2) — real addresses require kernel-binary extraction (Ghidra/nm)
kallsyms anomaly                : gic_handle_irq appears twice (both type t) — byte-confirmed; cause OPEN_BOUNDARY


################################################################################
# KERNEL MODULES
################################################################################

Loaded vendor kernel modules at capture (LIVE /proc/modules; load addresses zeroed by kptr_restrict=2):
wlan_drv_gen4m                  : loaded ; 1994752B ; refs=0 ; taint=(O)
wmt_chrdev_wifi                 : loaded ; 28672B  ; refs=1 [wlan_drv_gen4m] ; taint=(O)
gps_drv                         : loaded ; 65536B  ; refs=0 ; taint=(O)
bt_drv                          : loaded ; 28672B  ; refs=0 ; taint=(O)
wmt_drv                         : loaded ; 1224704B ; refs=5 [wlan_drv_gen4m,wmt_chrdev_wifi,gps_drv,bt_drv] ; taint=(O)
fpsgo                           : loaded ; 811008B ; refs=0 ; taint=(PO)

On-disk vendor kernel modules (FS /vendor/lib/modules ; SELinux u:object_r:vendor_file:s0 ; all ts 2008-12-31 18:00):
bt_drv.ko                       : 52216B
fpsgo.ko                        : 34608B
gps_drv.ko                      : 72192B
met.ko                          : 901856B — present on disk, not loaded at capture (absent from /proc/modules, confirmed)
wlan_drv_gen4m.ko               : 5158760B
wmt_chrdev_wifi.ko              : 57720B
wmt_drv.ko                      : 2133064B
/system/lib/modules             : CONFIRMED_ABSENT (directory does not exist)

Module metadata:
Timestamp anomaly               : all 7 .ko carry identical stripped timestamp 2008-12-31 18:00 (confirmed under -la and -laZ); cause OPEN_BOUNDARY
Taint flag semantics            : (O)=out-of-tree ; (P)=proprietary ; (PO)=proprietary+out-of-tree (standard kernel taint semantics)
Global taint cross-witness      : tainted=4609 (P+W+O) consistent with per-module (O)x5 + (PO)x1
Module modinfo string surface   : CLOSED_ABSENT_BY_TARGETED_GREP — no matched vermagic/name/version/license/author/description/depends/intree/retpoline/srcversion/alias strings across all seven `.ko` files
Module ELF ABI extraction        : OPEN_OPTIONAL — only required if build tooling needs section/vermagic metadata


################################################################################
# FIRMWARE-CLASS PAYLOADS
################################################################################
# FS /vendor/firmware — target of cmdline firmware_class.path=/vendor/firmware.
# All files ts 2008-12-31 18:00 (same stripped-timestamp class as modules).

BT_FW.cfg                       : 410B      (XREF:9 Bluetooth FW config)
MNL.bin                         : 128B — ALL 0x00 (zero-filled, byte-confirmed od -c) ; purpose OPEN_BOUNDARY
WIFI_RAM_CODE_soc1_0_1_1.bin    : 612652B   (XREF:9 WiFi RAM firmware)
WMT_SOC.cfg                     : 121B      (XREF:9 MTK WMT combo-chip SoC config)
gt1151_default_firmware2.img    : 63616B    (XREF:8 Goodix GT1151 touch controller FW)
rgx.fw                          : 118784B   (XREF:8 Imagination PowerVR RGX GPU FW)
soc1_0_patch_mcu_1_1_hdr.bin    : 26350B    (XREF:9 combo MCU patch)
soc1_0_ram_bt_1_1_hdr.bin       : 570765B   (XREF:9 combo BT RAM FW)
soc1_0_ram_mcu_1_1_hdr.bin      : 104324B   (XREF:9 combo MCU RAM FW)
soc1_0_ram_wifi_1_1_hdr.bin     : 195228B   (XREF:9 combo WiFi RAM FW)


################################################################################
# FILESYSTEM SUPPORT
################################################################################
# LIVE /proc/filesystems ; (*) = also confirmed active mount in /proc/mounts.

nodev filesystems:
sysfs (*) , rootfs , ramfs , bdev , proc (*) , cpuset (*) , cgroup (*) ,
cgroup2 (*) , tmpfs (*) , configfs (*) , debugfs (*) , tracefs (*) , sockfs ,
bpf (*) , pipefs , devpts (*) , sdcardfs (*) , fuse , fusectl , overlay ,
pstore (*) , selinuxfs (*) , functionfs (*)

block filesystems:
ext3 , ext2 , ext4 (*) , vfat , msdos , iso9660 , fuseblk

Prior 1.7 draft anchor list (all retained; the live enumeration above is the complete superset):
ext4, ext3, ext2, vfat, msdos, overlay, fuse, sdcardfs, pstore, configfs, selinuxfs, functionfs, tmpfs, ramfs, rootfs
Note                            : ext2/ext3 are registered names backed by the ext4 driver (CONFIG_EXT4_USE_FOR_EXT2=y); not independently mounted


################################################################################
# DEEPER KERNEL-INFLUENCE SURFACES
################################################################################
# Kernel binding/instruction surfaces below userspace ELF inspection
# (readelf/strings/strace/rpath cannot observe these). Status per live evidence.

vector.devicetree_bootargs      : CONFIRMED_ACTIVE — DT /chosen/bootargs carries the kernel command line (670B == /proc/cmdline); dtb is a separate partition, dtbo-overlayable
vector.dtbo_overlay             : ACTIVE_CLOSED — androidboot.dtbo_idx=0; raw dtbo.bin entry0 extracted/decompiled/classified; host `fdtoverlay` base+DTBO application returned status `<0>`; live FDT cross-check retained
vector.atag_injection           : ACTIVE — /chosen atags are DT-injected, kernel-consumed boot params (see 1.7.4)
vector.firmware_class           : POPULATED — /vendor/firmware, 10 files (see 1.7.9); request_firmware content not yet enumerated
vector.ebpf                     : POPULATED_STOCK — /sys/fs/bpf holds 19 pinned objects, all standard Android netd/clatd (11 maps + 8 progs); no non-standard object at capture
vector.module_runtime_load      : ENABLED — modules_disabled=0, CONFIG_MODULE_SIG not set
vector.kernel_image_internals   : OPEN — requires boot.img kernel-segment extraction (Ghidra/binwalk)
vector.stock_cmdline_diff       : CLOSED_AS_DIVERGENCE — boot.img header cmdline contains only bootopt/buildvariant; raw boot FDT /chosen bootargs contains baseline 9-token template; live /proc/cmdline contains bootloader-augmented final cmdline
vector.preloader_lk_stage       : OPEN — XREF:3 (pre-kernel handoff)

/sys/fs/bpf pinned-object inventory (LIVE; all stock netd/clatd):
maps  : map_clatd_clat_ingress_map, map_netd_app_uid_stats_map,
        map_netd_configuration_map, map_netd_cookie_tag_map,
        map_netd_iface_index_name_map, map_netd_iface_stats_map,
        map_netd_stats_map_A, map_netd_stats_map_B,
        map_netd_uid_counterset_map, map_netd_uid_owner_map,
        map_netd_uid_permission_map
progs : prog_clatd_schedcls_ingress_clat_ether, prog_clatd_schedcls_ingress_clat_rawip,
        prog_netd_cgroupskb_egress_stats, prog_netd_cgroupskb_ingress_stats,
        prog_netd_skfilter_blacklist_xtbpf, prog_netd_skfilter_egress_xtbpf,
        prog_netd_skfilter_ingress_xtbpf, prog_netd_skfilter_whitelist_xtbpf


################################################################################
# BINARY-PHASE WITNESS IDENTITY  (boot.bin / embedded DTB / dtbo.bin)
################################################################################

Binary-phase artifact identity:

| Object | Value |
| --- | --- |
| boot.bin source path | `/home/beright1976/Downloads/boot_evidence/00_frozen_source/boot.bin` |
| boot.bin source cross-witness | `/home/beright1976/lokmat_5_max_stock/boot.bin` byte-identical |
| boot.bin size | 33,554,432 bytes |
| boot.bin SHA256 | `fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851` |
| dtbo.bin source path | `/home/beright1976/lokmat_5_max_stock/dtbo.bin` |
| dtbo.bin size | 8,388,608 bytes |
| dtbo.bin SHA256 | `7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b` |
| unpack_bootimg kernel output | `/home/beright1976/Downloads/boot_evidence/02_unpack_bootimg_output/kernel` |
| unpack_bootimg kernel size | 9,246,082 bytes |
| unpack_bootimg kernel SHA256 | `e76555df4ce2dbc5dea2113468548f1597c49d1a5871d0d38dea199477d19522` |
| unpack_bootimg ramdisk output | `/home/beright1976/Downloads/boot_evidence/02_unpack_bootimg_output/ramdisk` |
| unpack_bootimg ramdisk size | 923,597 bytes |
| unpack_bootimg ramdisk SHA256 | `0a14137bcfff4de968f5c4ac09c04c55ccc97dc70b3e10421f4a10dfb74911ad` |
| unpack_bootimg dtb region output | `/home/beright1976/Downloads/boot_evidence/02_unpack_bootimg_output/dtb` |
| unpack_bootimg dtb region size | 89,609 bytes |
| unpack_bootimg dtb region SHA256 | `41115a867807ddabf54707acc5b16d8c93534026533bb109c4faedca4aa4a3af` |
| libufdt extract_dtb entry output | `/home/beright1976/Downloads/boot_evidence/03_dtb_extract_output/entry` |
| libufdt extract_dtb entry size | 89,545 bytes |
| libufdt extract_dtb entry SHA256 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| boot embedded base DTS output | `/home/beright1976/Downloads/boot_evidence/04_dts_decompiled/boot_embedded_dtb.dts` |
| boot embedded base DTS SHA256 | `14fb72004bc6c194678966e706509888da8a7a0c7a582d137ce9b81dc6d598a3` |
| boot embedded base DTS line count | 3,892 |
| dtbo entry0 extracted FDT output | `/home/beright1976/Downloads/boot_evidence/05_dtbo_decompiled/dtbo_entry0.dtb` |
| dtbo entry0 extracted FDT size | 29,974 bytes |
| dtbo entry0 extracted FDT SHA256 | `2dc23aa3b77db4e644000769fa22b41c007706f85538705183b6f0c2f1c26d84` |
| dtbo entry0 DTS output | `/home/beright1976/Downloads/boot_evidence/05_dtbo_decompiled/dtbo_entry0.dts` |
| dtbo entry0 DTS line count | 1,318 |
| dtbo entry0 DTS byte count | 34,954 bytes |
| dtbo entry0 repeat decompile compare | `cmp` exit 0; deterministic output |
| dtbo entry0 dtc exit | 0 |
| dtbo entry0 dtc warnings | 37 lines; classified in 1.7.18 |

Boot image header v2 decode:

| Field | Value |
| --- | --- |
| magic | `ANDROID!` at offset 0 |
| kernel_size | 9,246,082 |
| kernel_addr | `0x40080000` |
| ramdisk_size | 923,597 |
| ramdisk_addr | `0x51b00000` |
| second_size | 0 |
| second_addr | `0x40f00000` |
| tags_addr | `0x47880000` |
| page_size | 2,048 |
| header_version | 2 |
| os_version | Android 10.0.0 |
| patch level | 2024-03 |
| name[16] | CONFIRMED_ABSENT; all zero |
| cmdline[512] | `bootopt=64S3,32N2,64N2 buildvariant=user` |
| id[8] / 32-byte field | `c6ccf000f6513dafd9a2018c87d79992234887c` + 12 zero bytes; SHA1-shaped digest; not independently reproduced |
| extra_cmdline[1024] | CONFIRMED_ABSENT; all zero |
| recovery_dtbo_size | 0 |
| recovery_dtbo_offset | 0 |
| header_size | 1,660 |
| dtb_size | 89,609 |
| dtb_addr | `0x47880000` |
| dtb_addr vs tags_addr | `0x47880000 == 0x47880000`; OPEN_BOUNDARY / header-field collision |

Corrected boot.img offset math:

| Segment | Absolute offset / math |
| --- | --- |
| kernel_offset | 2,048 |
| ramdisk_offset | 9,248,768 |
| dtb_offset | 10,172,416; embedded nested DT_TABLE_MAGIC container start |
| dtb entry0 FDT start | 10,172,480 = dtb_offset + dt_offset 64 |

Boot image embedded DTB-region structure:

| Object | Value |
| --- | --- |
| boot header `dtb_size` region type | DT_TABLE_MAGIC container, not bare FDT |
| DT table magic | `0xD7B7AB1E` |
| DT table total_size | 89,609; exact match to boot.img header dtb_size |
| dt_entry_count | 1 |
| entry0 dt_size | 89,545 |
| entry0 dt_offset | 64 |
| entry0 FDT magic | `0xD00DFEED` |
| entry0 FDT absolute offset | 10,172,480 |
| whole-file FDT magic scan | exactly one match, consistent with corrected math |
| entry0 model | `MT6765` in raw boot FDT decompile context |
| entry0 compatible | `mediatek,MT6765` |
| boot.bin vs recovery.bin DTB | Same extracted DTB SHA256 `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6`; confirms shared embedded base DTB object |

DTBO container decode:

| Field | Value |
| --- | --- |
| dtbo.bin DT table magic | `0xD7B7AB1E` |
| total_size | 30,038 |
| header_size | 32 |
| dt_entry_size | 32 |
| dt_entry_count | 1 |
| dt_entries_offset | 32 |
| page_size | 2,048 |
| version | 0 |
| entry0 dt_size | 29,974 |
| entry0 dt_offset | 64 |
| entry0 id | 0 |
| entry0 rev | 0 |
| size math | 32 + 32 + 29,974 = 30,038; closed-consistent |
| nested FDT internal offset | 64 |
| nested FDT magic | `0xD00DFEED` |
| nested FDT totalsize | 29,974 |
| nested FDT off_dt_struct | 56 |
| nested FDT off_dt_strings | 26,664 |
| nested FDT status | Valid, standard, internally consistent; content classified below |

################################################################################
# STATIC BOOT FDT vs LIVE APPLIED CMDLINE / DT PRESENTATION
################################################################################

Static raw boot FDT `/chosen/bootargs` template:

```text
console=tty0 console=ttyS0,921600n1 vmalloc=400M slub_debug=OFZPU page_owner=on swiotlb=noforce androidboot.hardware=mt6765 maxcpus=8 loop.max_part=7 firmware_class.path=/vendor/firmware
```

Static boot image header cmdline:

```text
bootopt=64S3,32N2,64N2 buildvariant=user
```

Live `/proc/cmdline` / live `/proc/device-tree/chosen/bootargs` final command-line class:

| Class | Status |
| --- | --- |
| Baseline tokens shared with raw static boot FDT | console, serial console, vmalloc, slub_debug, page_owner, swiotlb, androidboot.hardware, maxcpus, loop.max_part, firmware_class.path |
| Boot image header-only field group | bootopt/buildvariant carried in boot.img header and present in live final cmdline |
| Bootloader/appended live-only field group | has_battery_removed, androidboot.boot_devices, root, androidboot.verifiedbootstate, androidboot.atm, androidboot.meta_log_disable, printk.disable_uart, bootprof.pl_t/lk_t/logo_t, androidboot.serialno, androidboot.bootreason, gpt, usb2jtag_mode, mrdump_ddrsv, mrdump_cb, androidboot.dtb_idx, androidboot.dtbo_idx |
| Resolution | STATIC_BOOT_FDT_TEMPLATE + BOOT_HEADER_CMDLINE + BOOTLOADER_AUGMENTATION produce final live `/proc/cmdline` |
| Prior OB16 STOCK_CMDLINE_DIFF | CLOSED_AS_DIVERGENCE; not an error; mechanism is pre-kernel augmentation/rewrite |

################################################################################
# RAW BOOT FDT PLATFORM CONTENT FINDINGS
################################################################################

Raw boot FDT memory declaration contradiction:

| Node/property | Raw value | Interpreted value | Classification |
| --- | --- | --- | --- |
| `/memory/reg` | `<0x00 0x40000000 0x00 0x20000000>` | base `0x40000000`, size `0x20000000` = 536,870,912 bytes / 512 MiB | OPEN_BOUNDARY |
| `/reserved-memory/zmc-default/size` | `<0x00 0xffc00000>` | 4,290,772,992 bytes / ~4092 MiB | OPEN_BOUNDARY |
| contradiction | reserved-memory size is ~8x static `/memory` size in same raw FDT | physically impossible if both are final runtime map | RECONCILE_WITH_SECTION_1_6_LIVE_RAM |

Raw boot FDT register/topology anomalies:

| Node | Raw property / structure | Classification | XREF |
| --- | --- | --- | --- |
| `apcldmain@1021b000` | `reg=<0x00 0x1021b000 0x00 0x00>` | ZERO_SIZE_REG_OPEN | 4 / 9 |
| `apcldmaout@1021b400` | `reg=<0x00 0x1021b400 0x00 0x00>` | ZERO_SIZE_REG_OPEN | 4 / 9 |
| `mdcldma@10014000` | composite `reg` spans four physical blocks: `0x10014000/0x1000`, `0x1021b000/0x1000`, `0x10209000/0x1000`, `0x1020a000/0x1000` | COMPOSITE_MODEM_DMA_REGISTER_NODE_CLOSED | 4 / 9 |
| `mdcldma@10014000` linked standalone nodes | `apcldmain_ao@10014000`, `apcldmain@1021b000`, `ap_ccif0@10209000`, `md_ccif0@1020a000` | REGISTER_OVERLAP_TOPOLOGY_CLOSED | 4 / 9 |
| `mdcldma@10014000` clock names | `scp-sys-md1-main`, infra-ccif-ap/md pairs | MODEM_CLDMA_ROLE_CONFIRMED | 9 |
| `msdc@11250000` | compatible `mediatek,msdc2`; reg base `0x11250000`; status disable | STATIC_DISABLED_NODE | 2 / 4 |
| `msdc@11260000` | compatible `mediatek,msdc3`; node path implies `0x11260000`; reg property repeats `0x11250000`; status disable | NAME_VS_REG_MISMATCH_OPEN | 2 / 4 |
| `__symbols__.msdc2` | `/msdc@11250000` | confirms msdc2 path | 2 / 4 |
| `__symbols__.msdc3` | `/msdc@11260000` | symbol/path conflicts with msdc3 reg property | 2 / 4 |
| `odm` simple-bus raw boot FDT | `compatible="simple-bus"`; phandle `0xa8`; no children rendered in static boot FDT | CLOSED_AS_STATIC_ONLY_BY_DTBO | 12 |

Raw boot FDT Wiite node-body resolution:

| Node | Raw boot FDT body | DTBO-applied resolution | Classification |
| --- | --- | --- | --- |
| `wiite_con` | compatible `wiite,wiite_con`; phandle `0x98`; no `reg`, no `interrupts`, no `clocks`; has pinctrl refs and Wiite custom properties in live/root inventory | DTBO fragment@11 adds `wiite,con1`, `wiite,con2`, and pwr I2C/UART pinctrl refs | CLOSED_BY_DTBO |
| `wiite_corp` | compatible `wiite,wiite_corp`; phandle `0x99`; static raw node `status="disabled"`; no `reg`, no `interrupts`, no `clocks` | DTBO fragment@13 sets `status="okay"` and `vhrs-supply` | CLOSED_AS_STATIC_ONLY_BY_DTBO |

Security/register identity nodes confirmed from raw boot FDT:

| Node | Register base | Size | Interrupt | Classification |
| --- | ---: | ---: | --- | --- |
| `efuse_dbg@10004000` | `0x10004000` | `0x1000` | none in node | REGISTER_CONFIRMED |
| `security_ao@1001a000` | `0x1001a000` | `0x1000` | none in node | REGISTER_CONFIRMED |
| `dxcc_sec@10210000` | `0x10210000` | `0x1000` | `<0 0xa2 4>` | REGISTER_CONFIRMED_INTERRUPT_PRESENT |
| `efusec@11c50000` | `0x11c50000` | `0x10000` | none in node | REGISTER_CONFIRMED_SIZE_ASYMMETRY |

Repeated-value static-FDT anomaly:

| Property | Raw bytes | Interpreted value | Classification |
| --- | --- | ---: | --- |
| `mt6370_pmu_dts/ldo/mt6370_ldo/regulator-max-microvolt` | `00 3d 09 00` | 4,000,000 | COHERENT_VOLTAGE_BOUND |
| `mt6370_pmu_dts/dsv/mt6370_dsvp/regulator-min-microvolt` | `00 3d 09 00` | 4,000,000 | COHERENT_VOLTAGE_BOUND |
| `mt6370_pmu_dts/dsv/mt6370_dsvn/regulator-min-microvolt` | `00 3d 09 00` | 4,000,000 | COHERENT_VOLTAGE_BOUND |
| `mt6370_pmu_dts/charger/dc_wdt` | `00 3d 09 00` | 4,000,000 | TIMER_SEMANTIC_MISMATCH_OPEN |
| DTC rendering note | `00 3d 09 00` may render as string-like `"\0=\t"` | raw-byte verification required for garbled properties | METHOD_APPLIED |


##  DTBO ENTRY0 OVERLAY OBJECT MAP

| Fragment | Resolved target symbol from __fixups__ | Has __local_fixups__ |
| --- | --- | --- |
| fragment@0 | chosen |  |
| fragment@1 | odm |  |
| fragment@2 | touch | YES |
| fragment@3 | pio |  |
| fragment@4 | pio |  |
| fragment@5 | consys | YES |
| fragment@6 | audgpio | YES |
| fragment@7 | pio |  |
| fragment@8 | mt6370_pmu |  |
| fragment@9 | i2c3 |  |
| fragment@10 | i2c4 |  |
| fragment@11 | wiite_con | YES |
| fragment@12 | pio |  |
| fragment@13 | wiite_corp |  |
| fragment@14 | auxadc |  |
| fragment@15 | pmic_clock_buffer_ctrl |  |
| fragment@16 | i2c0 |  |
| fragment@17 | i2c1 |  |
| fragment@18 | i2c2 |  |
| fragment@19 | i2c3 |  |
| fragment@20 | i2c4 |  |
| fragment@21 | i2c5 |  |
| fragment@22 | i2c6 |  |
| fragment@23 | i2c7 |  |
| fragment@24 | i2c8 |  |
| fragment@25 | i2c9 |  |
| fragment@26 | gpio_usage_mapping |  |
| fragment@27 | gpio |  |
| fragment@28 | touch |  |
| fragment@29 | subpmic_pmu_eint |  |
| fragment@30 | dsi_te |  |
| fragment@31 | main_pmic |  |
| fragment@32 | mt_pmic_vcama_ldo_reg |  |
| fragment@33 | mt_pmic_vsim1_ldo_reg |  |
| fragment@34 | mt_pmic_vsim2_ldo_reg |  |
| fragment@35 | mt_pmic_vcamd_ldo_reg |  |
| fragment@36 | mt_pmic_vcamio_ldo_reg |  |
| fragment@37 | mt_pmic_vldo28_ldo_reg |  |
| fragment@38 | kd_camera_hw1 |  |
| fragment@39 | touch |  |
| fragment@40 | keypad |  |
| fragment@41 | pio |  |
| fragment@42 | kd_camera_hw1 | YES |

## DTBO fragment object classification

| Fragment | Target | Overlay data | Object class | Route | Limited comment |
| --- | --- | --- | --- | --- | --- |
| fragment@0 | chosen | atag,videolfb-* overlay: fb_base_h=0, fb_base_l standalone DTS rendered string '^`P' but host-applied/live `fdtget` closes raw value as `5e605000`, islcmfound=1, islcm_inited=0, fps=0x1770, vramSize=0x17bb000, lcmname=nt35695B_fhd_dsi_cmd_auo_rt5081_drv | KERNEL_BOOT_PARAM_OVERLAY | INCLUDE_1_7 | PANEL_ID_DIVERGENCE remains anomaly: overlay lcmname=nt35695B while active display evidence elsewhere favors ST7701S. |
| fragment@1 | odm | Adds led@0..led@6 and vibrator@0 under odm target: red/green/blue/jogball/keyboard/button/lcd-backlight plus vibrator | ODM_NODE_POPULATED_BY_DTBO | INCLUDE_1_7 | Closes static-only empty odm node: raw boot FDT empty, DTBO populates it. |
| fragment@2 | touch | Touch config: resolution 480x640, 3 virtual keys, max-touch=5, filter enabled, pinctrl references 0x01..0x06, status=okay | TOUCH_PLATFORM_OVERLAY | XREF_ONLY | Kernel-platform includes target/status/pinctrl; endpoint detail belongs to touch/display hardware mapping. |
| fragment@3 | pio | Touch EINT/RST pinctrl states: eint0default, eint@0, eintoutput0/1, rstoutput0/1; pinmux 0x200 and 0xae00 | PINCTRL_TOUCH_STATES | XREF_ONLY | Unit-address warning on eint@0. |
| fragment@4 | pio | ConSys pinctrl states: consys_default; gpslna@0/1/2 pinmux 0x5b00; wcnvcn33@0/1/2 pinmux 0xb200 | PINCTRL_CONSYS_GPS_WCN | XREF_ONLY | Unit-address warnings on gpslna and wcnvcn33 nodes. |
| fragment@5 | consys | Applies consys pinctrl-names default, gps_lna states, wcn_vcn33 states; status=okay | CONSYS_PLATFORM_ENABLE | INCLUDE_1_7 | Kernel platform enables consys pinctrl path. |
| fragment@6 | audgpio | Audio GPIO pinctrl references for I2S/MOSI/MISO/extamp; status=okay | AUDIO_GPIO_PLATFORM_ENABLE | XREF_ONLY | Endpoint detail belongs to audio hardware mapping. |
| fragment@7 | pio | Audio pinctrl child states: aud_clk_mosi/miso off/on, aud_dat_mosi/miso off/on, extamp high/low; pinmux 0x8800/0x8801/0x8c00/0x8c01/0x8a00/0x8a01/0x8b00/0x8b01/0x8e00/0x8e01/0x8f00/0x8f01/0x9f00 | PINCTRL_AUDIO_STATES | XREF_ONLY | Pinctrl data is hardware endpoint support, not 1.7-native except target linkage. |
| fragment@8 | mt6370_pmu | MT6370 PMU overlay: intr_gpio_num=0x0b; intr_gpio=pio GPIO 0x0b; charger chg_en_gpio=pio GPIO 0x9a; ieoc=0xc350 | PMU_CHARGER_PLATFORM_OVERLAY | INCLUDE_1_7 | GPIO properties use 0xffffffff fixups; validate after overlay application. |
| fragment@9 | i2c3 | Adds ext_buck_lp4@57/ext_buck_lp4x@50 regulator nodes: ext_buck_vdd2/ext_buck_vddq, min=0x493e0, max=0x13d620, vsel_pin 0/1 | I2C3_REGULATOR_OVERLAY | INCLUDE_1_7 | Unit-address warnings because no reg property despite @57/@50. |
| fragment@10 | i2c4 | Adds wiite_cp@50: compatible=wiite,cp; reg=0x50; irq-gpio=pio GPIO 0x08; irq-gpio_1=pio GPIO 0x06; status=okay | WIITE_NRF_PRIMARY_I2C_OVERLAY | INCLUDE_1_7 | Critical kernel-platform identity item; raw static wiite node body incomplete without DTBO. |
| fragment@11 | wiite_con | Adds wiite,con1=pio GPIO 0x0a; wiite,con2=pio GPIO 0x04; pinctrl-names pwr_i2c_uart_pin_af/gpio; pinctrl 0x18/0x19 | WIITE_CON_CONTROL_OVERLAY | INCLUDE_1_7 | Critical kernel-platform identity item; fills static wiite_con function surface. |
| fragment@12 | pio | Adds pwr_i2c_uart_pin_af/gpio states: SCL pinmux 0x6901/0x6900, SDA 0x6a01/0x6a00, UTX 0x01/0x00, URX 0x101/0x100 | PINCTRL_WIITE_PWR_I2C_UART | INCLUDE_1_7 | Critical for Wiite/nRF bridge pin identity. |
| fragment@13 | wiite_corp | Sets vhrs-supply=<mt_pmic_vmch_ldo_reg>; status=okay | WIITE_CORP_ENABLE_OVERLAY | INCLUDE_1_7 | Closes static disabled state: DTBO enables wiite_corp. |
| fragment@14 | auxadc | Adds adc_channel@ compatible mediatek,adc_channel; temperature0=0, temperature1=1, adc_fdd_rf_params_dynamic_custom_ch=2; status=okay | AUXADC_CHANNEL_OVERLAY | INCLUDE_1_7 | Node name adc_channel@ has no address suffix; no dtc warning observed. |
| fragment@15 | pmic_clock_buffer_ctrl | Clock buffer config: quantity=7; config=<2 1 1 2 0 0 0>; driving-current seven 1s; status=okay | PMIC_CLOCK_BUFFER_OVERLAY | INCLUDE_1_7 | Clock-buffer kernel platform config. |
| fragment@16 | i2c0 | I2C0 bus config clock-frequency=0x61a80; open-drain; adds cap_touch@5d compatible mediatek,cap_touch reg=0x5d status=okay | I2C0_TOUCH_OVERLAY | INCLUDE_1_7 | Kernel-visible bus endpoint. |
| fragment@17 | i2c1 | I2C1 bus config only: #address-cells=1; #size-cells=0; clock-frequency=0x61a80; open-drain | I2C_BUS_CONFIG_ONLY | INCLUDE_1_7 | No child endpoint in overlay. |
| fragment@18 | i2c2 | I2C2 camera bus: sub@2d, sub_eeprom@52, main@1a, main_eeprom@50, main_af@0c, ccu_sensor_i2c_2_hw@10 all status=okay | I2C2_CAMERA_OVERLAY | INCLUDE_1_7 | camera_main_af@0c has leading-zero unit-address warning. |
| fragment@19 | i2c3 | I2C3 bus config only: #address-cells=1; #size-cells=0; clock-frequency=0x61a80; open-drain | I2C_BUS_CONFIG_ONLY | INCLUDE_1_7 | Pairs with fragment@9 ext buck nodes. |
| fragment@20 | i2c4 | I2C4 bus: heartrate@33 compatible mediatek,heartrate; ccu_sensor_i2c_4_hw@11; both status=okay | I2C4_HEARTRATE_CCU_OVERLAY | INCLUDE_1_7 | Critical because same bus as wiite_cp@50; confirms I2C4 multi-node topology. |
| fragment@21 | i2c5 | I2C5 bus: clock-frequency=0x33e140; use-push-pull; subpmic_pmu@34 compatible mediatek,subpmic_pmu status=okay | I2C5_SUBPMIC_OVERLAY | INCLUDE_1_7 | 3.4 MHz bus config. |
| fragment@22 | i2c6 | I2C6 camera-main-two bus: camera_main_two@2d, eeprom@50, af@0c status=okay | I2C6_CAMERA_OVERLAY | INCLUDE_1_7 | camera_main_two_af@0c leading-zero warning. |
| fragment@23 | i2c7 | I2C7 bus config only: #address-cells=1; #size-cells=0; clock-frequency=0x61a80; open-drain | I2C_BUS_CONFIG_ONLY | INCLUDE_1_7 | No child endpoint in overlay. |
| fragment@24 | i2c8 | I2C8 bus config only: #address-cells=1; #size-cells=0; clock-frequency=0x61a80; open-drain | I2C_BUS_CONFIG_ONLY | INCLUDE_1_7 | No child endpoint in overlay. |
| fragment@25 | i2c9 | I2C9 bus config only: #address-cells=1; #size-cells=0; clock-frequency=0x61a80; open-drain | I2C_BUS_CONFIG_ONLY | INCLUDE_1_7 | No child endpoint in overlay. |
| fragment@26 | gpio_usage_mapping | Defines GPIO_SIM1_SCLK GPIO 0x26, SIM1_SRST 0x27, SIM1_SIO 0x28, SIM2_HOT_PLUG 0x2e | GPIO_SIM_MAPPING_OVERLAY | INCLUDE_1_7 | Kernel platform GPIO mapping. |
| fragment@27 | gpio | Large gpio_init_default table covering GPIO indexes 0x00..0xb3; raw cell table retained, not semantically decoded here | GPIO_INIT_TABLE_OVERLAY | INCLUDE_1_7 | Requires dedicated decode if GPIO table semantics are needed; raw table is kernel platform data. |
| fragment@28 | touch | Touch interrupt overlay: interrupt-parent=<pio>; interrupts=<0x02 0x08 0x02 0x00>; status=okay | TOUCH_INTERRUPT_OVERLAY | INCLUDE_1_7 | Standalone dtc reports invalid phandle until base fixup applied. |
| fragment@29 | subpmic_pmu_eint | SubPMIC EINT overlay: interrupt-parent=<pio>; interrupts=<0x0b 0x02 0x0b 0x00>; status=okay | SUBPMIC_INTERRUPT_OVERLAY | INCLUDE_1_7 | Standalone dtc reports invalid phandle until base fixup applied. |
| fragment@30 | dsi_te | DSI TE interrupt overlay: interrupt-parent=<pio>; interrupts=<0x2c 0x01 0x2c 0x01>; status=okay | DSI_TE_INTERRUPT_OVERLAY | INCLUDE_1_7 | Standalone dtc reports invalid phandle until base fixup applied. |
| fragment@31 | main_pmic | Main PMIC interrupt overlay: interrupt-parent=<pio>; interrupts=<0x90 0x04 0x90 0x01>; status=okay | MAIN_PMIC_INTERRUPT_OVERLAY | INCLUDE_1_7 | Standalone dtc reports invalid phandle until base fixup applied. |
| fragment@32 | mt_pmic_vcama_ldo_reg | Regulator vcama default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | Supply source for camera fragments. |
| fragment@33 | mt_pmic_vsim1_ldo_reg | Regulator vsim1 default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | SIM power platform data. |
| fragment@34 | mt_pmic_vsim2_ldo_reg | Regulator vsim2 default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | SIM power platform data. |
| fragment@35 | mt_pmic_vcamd_ldo_reg | Regulator vcamd default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | Camera digital power platform data. |
| fragment@36 | mt_pmic_vcamio_ldo_reg | Regulator vcamio default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | Camera IO power platform data. |
| fragment@37 | mt_pmic_vldo28_ldo_reg | Regulator vldo28 default-on=1 status=okay | REGULATOR_ENABLE_OVERLAY | INCLUDE_1_7 | Used for vcamaf/vtouch supplies. |
| fragment@38 | kd_camera_hw1 | Camera supply binding: vcama, vcamd_main2, vcamio, vcamio_sub, vcamio_main2, vcamaf, vcamaf_sub, vcamaf_main2; status=okay | CAMERA_SUPPLY_OVERLAY | INCLUDE_1_7 | Target binding for camera power. |
| fragment@39 | touch | Touch supply binding: vtouch-supply=<mt_pmic_vldo28_ldo_reg>; status=okay | TOUCH_SUPPLY_OVERLAY | INCLUDE_1_7 | Touch power supply platform data. |
| fragment@40 | keypad | Keypad config: debounce=0x400; sw pwrkey=0x74; hw pwrkey=0x08; sw rstkey=0x73; hw rstkey=0x11; hw map num=0x48; recovery/download key config; status=okay | KEYPAD_PLATFORM_OVERLAY | INCLUDE_1_7 | Kernel input key platform identity. |
| fragment@41 | pio | Camera pinctrl states: cam0/cam1 mclk on/off, reset/pdn low/high with pinmux 0x64/0x63/0x66/0x62/0x65/0x61 families | PINCTRL_CAMERA_STATES | INCLUDE_1_7 | Several unit-address warnings on cam0/cam1 child nodes. |
| fragment@42 | kd_camera_hw1 | Camera pinctrl bindings and camera supplies for cam0/cam1: 13 pinctrl refs; vcama/vcamd/vcamio supplies; status=okay | CAMERA_PINCTRL_SUPPLY_BINDING | INCLUDE_1_7 | Local fixups for pinctrl-0..12. |

## __fixups__ target / property linkage

| Fixup key | Reference count | References |
| --- | --- | --- |
| chosen | 1 | /fragment@0:target:0 |
| odm | 1 | /fragment@1:target:0 |
| touch | 3 | /fragment@2:target:0<br>/fragment@28:target:0<br>/fragment@39:target:0 |
| pio | 19 | /fragment@3:target:0<br>/fragment@4:target:0<br>/fragment@7:target:0<br>/fragment@8/__overlay__:mt6370,intr_gpio:0<br>/fragment@8/__overlay__/charger:mt6370,chg_en_gpio:0<br>/fragment@10/__overlay__/wiite_cp@50:irq-gpio:0<br>/fragment@10/__overlay__/wiite_cp@50:irq-gpio_1:0<br>/fragment@11/__overlay__:wiite,con1:0<br>/fragment@11/__overlay__:wiite,con2:0<br>/fragment@12:target:0<br>/fragment@26/__overlay__:GPIO_SIM1_SCLK:0<br>/fragment@26/__overlay__:GPIO_SIM1_SRST:0<br>/fragment@26/__overlay__:GPIO_SIM1_SIO:0<br>/fragment@26/__overlay__:GPIO_SIM2_HOT_PLUG:0<br>/fragment@28/__overlay__:interrupt-parent:0<br>/fragment@29/__overlay__:interrupt-parent:0<br>/fragment@30/__overlay__:interrupt-parent:0<br>/fragment@31/__overlay__:interrupt-parent:0<br>/fragment@41:target:0 |
| consys | 1 | /fragment@5:target:0 |
| audgpio | 1 | /fragment@6:target:0 |
| mt6370_pmu | 1 | /fragment@8:target:0 |
| i2c3 | 2 | /fragment@9:target:0<br>/fragment@19:target:0 |
| i2c4 | 2 | /fragment@10:target:0<br>/fragment@20:target:0 |
| wiite_con | 1 | /fragment@11:target:0 |
| wiite_corp | 1 | /fragment@13:target:0 |
| mt_pmic_vmch_ldo_reg | 1 | /fragment@13/__overlay__:vhrs-supply:0 |
| auxadc | 1 | /fragment@14:target:0 |
| pmic_clock_buffer_ctrl | 1 | /fragment@15:target:0 |
| i2c0 | 1 | /fragment@16:target:0 |
| i2c1 | 1 | /fragment@17:target:0 |
| i2c2 | 1 | /fragment@18:target:0 |
| i2c5 | 1 | /fragment@21:target:0 |
| i2c6 | 1 | /fragment@22:target:0 |
| i2c7 | 1 | /fragment@23:target:0 |
| i2c8 | 1 | /fragment@24:target:0 |
| i2c9 | 1 | /fragment@25:target:0 |
| gpio_usage_mapping | 1 | /fragment@26:target:0 |
| gpio | 1 | /fragment@27:target:0 |
| subpmic_pmu_eint | 1 | /fragment@29:target:0 |
| dsi_te | 1 | /fragment@30:target:0 |
| main_pmic | 1 | /fragment@31:target:0 |
| mt_pmic_vcama_ldo_reg | 4 | /fragment@32:target:0<br>/fragment@38/__overlay__:vcama-supply:0<br>/fragment@42/__overlay__:cam0_vcama-supply:0<br>/fragment@42/__overlay__:cam1_vcama-supply:0 |
| mt_pmic_vsim1_ldo_reg | 1 | /fragment@33:target:0 |
| mt_pmic_vsim2_ldo_reg | 1 | /fragment@34:target:0 |
| mt_pmic_vcamd_ldo_reg | 4 | /fragment@35:target:0<br>/fragment@38/__overlay__:vcamd_main2-supply:0<br>/fragment@42/__overlay__:cam0_vcamd-supply:0<br>/fragment@42/__overlay__:cam1_vcamd-supply:0 |
| mt_pmic_vcamio_ldo_reg | 6 | /fragment@36:target:0<br>/fragment@38/__overlay__:vcamio-supply:0<br>/fragment@38/__overlay__:vcamio_sub-supply:0<br>/fragment@38/__overlay__:vcamio_main2-supply:0<br>/fragment@42/__overlay__:cam0_vcamio-supply:0<br>/fragment@42/__overlay__:cam1_vcamio-supply:0 |
| mt_pmic_vldo28_ldo_reg | 5 | /fragment@37:target:0<br>/fragment@38/__overlay__:vcamaf-supply:0<br>/fragment@38/__overlay__:vcamaf_sub-supply:0<br>/fragment@38/__overlay__:vcamaf_main2-supply:0<br>/fragment@39/__overlay__:vtouch-supply:0 |
| kd_camera_hw1 | 2 | /fragment@38:target:0<br>/fragment@42:target:0 |
| keypad | 1 | /fragment@40:target:0 |

## __symbols__ exported overlay objects

| Symbol | Overlay path | DTS line |
| --- | --- | --- |
| led0 | /fragment@1/__overlay__/led@0 | 1149 |
| led1 | /fragment@1/__overlay__/led@1 | 1150 |
| led2 | /fragment@1/__overlay__/led@2 | 1151 |
| led3 | /fragment@1/__overlay__/led@3 | 1152 |
| led4 | /fragment@1/__overlay__/led@4 | 1153 |
| led5 | /fragment@1/__overlay__/led@5 | 1154 |
| led6 | /fragment@1/__overlay__/led@6 | 1155 |
| vibrator0 | /fragment@1/__overlay__/vibrator@0 | 1156 |
| ctp_pins_default | /fragment@3/__overlay__/eint0default | 1157 |
| ctp_pins_eint_as_int | /fragment@3/__overlay__/eint@0 | 1158 |
| ctp_pins_eint_output0 | /fragment@3/__overlay__/eintoutput0 | 1159 |
| ctp_pins_eint_output1 | /fragment@3/__overlay__/eintoutput1 | 1160 |
| ctp_pins_rst_output0 | /fragment@3/__overlay__/rstoutput0 | 1161 |
| ctp_pins_rst_output1 | /fragment@3/__overlay__/rstoutput1 | 1162 |
| consys_pins_default | /fragment@4/__overlay__/consys_default | 1163 |
| gpslna_pins_init | /fragment@4/__overlay__/gpslna@0 | 1164 |
| gpslna_pins_oh | /fragment@4/__overlay__/gpslna@1 | 1165 |
| gpslna_pins_ol | /fragment@4/__overlay__/gpslna@2 | 1166 |
| wcn_vcn33_pins_init | /fragment@4/__overlay__/wcnvcn33@0 | 1167 |
| wcn_vcn33_pins_oh | /fragment@4/__overlay__/wcnvcn33@1 | 1168 |
| wcn_vcn33_pins_ol | /fragment@4/__overlay__/wcnvcn33@2 | 1169 |
| aud_clk_mosi_off | /fragment@7/__overlay__/aud_clk_mosi_off | 1170 |
| aud_clk_mosi_on | /fragment@7/__overlay__/aud_clk_mosi_on | 1171 |
| aud_clk_miso_off | /fragment@7/__overlay__/aud_clk_miso_off | 1172 |
| aud_clk_miso_on | /fragment@7/__overlay__/aud_clk_miso_on | 1173 |
| aud_dat_mosi_off | /fragment@7/__overlay__/aud_dat_mosi_off | 1174 |
| aud_dat_mosi_on | /fragment@7/__overlay__/aud_dat_mosi_on | 1175 |
| aud_dat_miso_off | /fragment@7/__overlay__/aud_dat_miso_off | 1176 |
| aud_dat_miso_on | /fragment@7/__overlay__/aud_dat_miso_on | 1177 |
| aud_pins_extamp_high | /fragment@7/__overlay__/audexamphigh | 1178 |
| aud_pins_extamp_low | /fragment@7/__overlay__/audexamplow | 1179 |
| pwr_i2c_uart_pin_af | /fragment@12/__overlay__/pwr_i2c_uart_pin_af | 1180 |
| pwr_i2c_uart_pin_gpio | /fragment@12/__overlay__/pwr_i2c_uart_pin_gpio | 1181 |
| cap_touch_mtk | /fragment@16/__overlay__/cap_touch@5d | 1182 |
| camera_sub_mtk | /fragment@18/__overlay__/camera_sub@2d | 1183 |
| camera_sub_eeprom_mtk | /fragment@18/__overlay__/camera_sub_eeprom@52 | 1184 |
| camera_main_mtk | /fragment@18/__overlay__/camera_main@1a | 1185 |
| camera_main_eeprom_mtk | /fragment@18/__overlay__/camera_main_eeprom@50 | 1186 |
| camera_main_af_mtk | /fragment@18/__overlay__/camera_main_af@0c | 1187 |
| ccu_sensor_i2c_2_hw_mtk | /fragment@18/__overlay__/ccu_sensor_i2c_2_hw@10 | 1188 |
| heartrate_mtk | /fragment@20/__overlay__/heartrate@33 | 1189 |
| ccu_sensor_i2c_4_hw_mtk | /fragment@20/__overlay__/ccu_sensor_i2c_4_hw@11 | 1190 |
| subpmic_pmu_mtk | /fragment@21/__overlay__/subpmic_pmu@34 | 1191 |
| camera_main_two_mtk | /fragment@22/__overlay__/camera_main_two@2d | 1192 |
| camera_main_two_eeprom_mtk | /fragment@22/__overlay__/camera_main_two_eeprom@50 | 1193 |
| camera_main_two_af_mtk | /fragment@22/__overlay__/camera_main_two_af@0c | 1194 |
| camera_pins_cam0_mclk_on | /fragment@41/__overlay__/camera_pins_cam0_mclk_on | 1195 |
| camera_pins_cam0_mclk_off | /fragment@41/__overlay__/camera_pins_cam0_mclk_off | 1196 |
| camera_pins_cam1_mclk_on | /fragment@41/__overlay__/camera_pins_cam1_mclk_on | 1197 |
| camera_pins_cam1_mclk_off | /fragment@41/__overlay__/camera_pins_cam1_mclk_off | 1198 |
| camera_pins_cam0_rst0 | /fragment@41/__overlay__/cam0@0 | 1199 |
| camera_pins_cam0_rst1 | /fragment@41/__overlay__/cam0@1 | 1200 |
| camera_pins_cam0_pnd0 | /fragment@41/__overlay__/cam0@2 | 1201 |
| camera_pins_cam0_pnd1 | /fragment@41/__overlay__/cam0@3 | 1202 |
| camera_pins_cam1_rst0 | /fragment@41/__overlay__/cam1@0 | 1203 |
| camera_pins_cam1_rst1 | /fragment@41/__overlay__/cam1@1 | 1204 |
| camera_pins_cam1_pnd0 | /fragment@41/__overlay__/cam1@2 | 1205 |
| camera_pins_cam1_pnd1 | /fragment@41/__overlay__/cam1@3 | 1206 |
| camera_pins_default | /fragment@41/__overlay__/camdefault | 1207 |

## __local_fixups__ Local Phandle Patching

| Fragment | Property | DTS line |
| --- | --- | --- |
| 2 | pinctrl-0 | 1253 |
| 2 | pinctrl-1 | 1254 |
| 2 | pinctrl-2 | 1255 |
| 2 | pinctrl-3 | 1256 |
| 2 | pinctrl-4 | 1257 |
| 2 | pinctrl-5 | 1258 |
| 5 | pinctrl-0 | 1265 |
| 5 | pinctrl-1 | 1266 |
| 5 | pinctrl-2 | 1267 |
| 5 | pinctrl-3 | 1268 |
| 5 | pinctrl-4 | 1269 |
| 5 | pinctrl-5 | 1270 |
| 5 | pinctrl-6 | 1271 |
| 6 | pinctrl-0 | 1278 |
| 6 | pinctrl-1 | 1279 |
| 6 | pinctrl-2 | 1280 |
| 6 | pinctrl-3 | 1281 |
| 6 | pinctrl-4 | 1282 |
| 6 | pinctrl-5 | 1283 |
| 6 | pinctrl-6 | 1284 |
| 6 | pinctrl-7 | 1285 |
| 6 | pinctrl-8 | 1286 |
| 6 | pinctrl-9 | 1287 |
| 11 | pinctrl-0 | 1294 |
| 11 | pinctrl-1 | 1295 |
| 42 | pinctrl-0 | 1302 |
| 42 | pinctrl-1 | 1303 |
| 42 | pinctrl-2 | 1304 |
| 42 | pinctrl-3 | 1305 |
| 42 | pinctrl-4 | 1306 |
| 42 | pinctrl-5 | 1307 |
| 42 | pinctrl-6 | 1308 |
| 42 | pinctrl-7 | 1309 |
| 42 | pinctrl-8 | 1310 |
| 42 | pinctrl-9 | 1311 |
| 42 | pinctrl-10 | 1312 |
| 42 | pinctrl-11 | 1313 |
| 42 | pinctrl-12 | 1314 |

## DTBO DTC WARNING CLASSIFICATION

| Warning type | Count |
| --- | --- |
| gpios_property | 2 |
| interrupts_property | 8 |
| unit_address_format | 2 |
| unit_address_vs_reg | 25 |

## DTC warning class summary

| Classification | Count |
| --- | --- |
| BASE_PHANDLE_DEPENDENCY | 9 |
| LINT_UNIT_ADDRESS_FORMAT | 2 |
| LINT_UNIT_ADDRESS_NO_REG | 25 |
| OVERLAY_FIXUP_STRING_LINT | 1 |

## DTC warning classification — individual lines

| # | dtc warning | Object path / message root | Classification | Handling |
| --- | --- | --- | --- | --- |
| 1 | unit_address_vs_reg | /fragment@1/__overlay__/led@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 2 | unit_address_vs_reg | /fragment@1/__overlay__/led@1 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 3 | unit_address_vs_reg | /fragment@1/__overlay__/led@2 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 4 | unit_address_vs_reg | /fragment@1/__overlay__/led@3 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 5 | unit_address_vs_reg | /fragment@1/__overlay__/led@4 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 6 | unit_address_vs_reg | /fragment@1/__overlay__/led@5 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 7 | unit_address_vs_reg | /fragment@1/__overlay__/led@6 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 8 | unit_address_vs_reg | /fragment@1/__overlay__/vibrator@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 9 | unit_address_vs_reg | /fragment@3/__overlay__/eint@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 10 | unit_address_vs_reg | /fragment@4/__overlay__/gpslna@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 11 | unit_address_vs_reg | /fragment@4/__overlay__/gpslna@1 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 12 | unit_address_vs_reg | /fragment@4/__overlay__/gpslna@2 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 13 | unit_address_vs_reg | /fragment@4/__overlay__/wcnvcn33@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 14 | unit_address_vs_reg | /fragment@4/__overlay__/wcnvcn33@1 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 15 | unit_address_vs_reg | /fragment@4/__overlay__/wcnvcn33@2 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 16 | unit_address_vs_reg | /fragment@9/__overlay__/ext_buck_lp4@57 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 17 | unit_address_vs_reg | /fragment@9/__overlay__/ext_buck_lp4x@50 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 18 | unit_address_vs_reg | /fragment@41/__overlay__/cam0@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 19 | unit_address_vs_reg | /fragment@41/__overlay__/cam0@1 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 20 | unit_address_vs_reg | /fragment@41/__overlay__/cam0@2 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 21 | unit_address_vs_reg | /fragment@41/__overlay__/cam0@3 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 22 | unit_address_vs_reg | /fragment@41/__overlay__/cam1@0 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 23 | unit_address_vs_reg | /fragment@41/__overlay__/cam1@1 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 24 | unit_address_vs_reg | /fragment@41/__overlay__/cam1@2 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 25 | unit_address_vs_reg | /fragment@41/__overlay__/cam1@3 | LINT_UNIT_ADDRESS_NO_REG | Record path; non-blocking unless node is expected to be addressable. Node is overlay child/pinctrl/LED logical node. |
| 26 | unit_address_format | /fragment@18/__overlay__/camera_main_af@0c | LINT_UNIT_ADDRESS_FORMAT | Record exact node name; non-blocking address formatting defect. |
| 27 | unit_address_format | /fragment@22/__overlay__/camera_main_two_af@0c | LINT_UNIT_ADDRESS_FORMAT | Record exact node name; non-blocking address formatting defect. |
| 28 | gpios_property | /fragment@10/__overlay__/wiite_cp@50 | BASE_PHANDLE_DEPENDENCY | irq-gpio uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 29 | gpios_property | /__fixups__ | OVERLAY_FIXUP_STRING_LINT | Standalone overlay fixup table parsed by dtc as gpio-like property; validate by overlay application against base DTB. |
| 30 | interrupts_property | /fragment@28/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 31 | interrupts_property | /fragment@28/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 32 | interrupts_property | /fragment@29/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 33 | interrupts_property | /fragment@29/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 34 | interrupts_property | /fragment@30/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 35 | interrupts_property | /fragment@30/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 36 | interrupts_property | /fragment@31/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |
| 37 | interrupts_property | /fragment@31/__overlay__ | BASE_PHANDLE_DEPENDENCY | interrupt-parent uses unresolved 0xffffffff phandle in standalone overlay; validate by overlay application against base DTB/live applied DT. |

## KERNEL-PLATFORM RESOLUTION MATRIX

| Insert object | Data |
| --- | --- |
| DTBO container identity | dtbo.bin entry0 FDT is valid and decompiles deterministically: 29,974-byte FDT; DTS 1,318 lines; dtc exit 0; deterministic cmp exit 0; warning lines 37 classified above. |
| DTBO overlay selected | Live cmdline already records androidboot.dtbo_idx=0. Raw dtbo entry0 content is therefore the selected overlay candidate for applied platform topology. |
| Overlay target count | 43 fragments target 35 base symbols through __fixups__; 59 overlay symbols exported; local fixups patch pinctrl arrays in fragments 2, 5, 6, 11, and 42. |
| Static/raw vs applied topology | Raw boot FDT can be a template. DTBO supplies board-specific applied topology for odm, Wiite, I2C, regulators, pinctrl, touch, camera, keypad, PMU, and GPIO. |
| odm node resolution | Raw boot FDT empty odm node is static-only. DTBO fragment@1 targets odm and adds LED/vibrator child nodes. |
| Wiite/nRF resolution | DTBO fragment@10 adds wiite_cp@50 on i2c4 with irq GPIOs 8 and 6; fragment@11 completes wiite_con GPIOs 10 and 4 plus pinctrl; fragment@13 enables wiite_corp with vhrs-supply; fragment@20 adds heartrate@33 and ccu_sensor_i2c_4_hw@11 on i2c4. |
| Warning handling | DTC warnings do not invalidate extraction/decompile. Standalone phandle/fixup warnings are closed as standalone-overlay warning class after successful host base+DTBO application; applied/live DTC warning surfaces are preserved separately. |

## DTBO / FDT APPLICATION CLOSEOUT

| Former boundary | Status | Resolution |
| --- | --- | --- |
| DTBO_BASE_APPLICATION_VALIDATION | CLOSED | `fdtoverlay` applied `dtbo_entry0.dtb` against boot base FDT with status `<0>`; host-applied FDT generated and hashed. |
| BASE_PHANDLE_DEPENDENCY warnings | CLOSED_AS_STANDALONE_OVERLAY_ONLY | Standalone overlay warnings are retained as parser/fixup evidence; base application succeeded. |
| DTC_GPIO_FIXUP_WARNING | CLOSED_AS_STANDALONE_OVERLAY_ONLY | `__fixups__` parser warning no longer blocks 1.7 closure after base application. |
| DTC_INTERRUPT_PHANDLE_WARNINGS | CLOSED_AS_STANDALONE_OVERLAY_ONLY | Fragments 28-31 interrupt-parent placeholder warnings are standalone-overlay artifacts; applied/live warning surfaces are retained separately. |
| VIDEOLFB_FB_BASE_L_RENDERING | CLOSED | Host-applied and live `fdtget` return `5e605000`; combined framebuffer base is `0x000000005e605000`. |
| FIRMWARE_ANDROID_COMPATIBLE_EMPTY | CLOSED/SUPERSEDED | Live FDT `/firmware/android compatible=android,firmware`, `hardware=mt6762`, `mode=normal`, `serialno=MTK0002507301608400`. |
| RAW_BOOT_MEMORY_ZMC_CONTRADICTION | CLOSED_AS_STATIC_TEMPLATE_VS_LIVE_REWRITE | Raw boot base memory is a 512 MiB static template; live FDT memory is the final 6 GiB map with live reserved-memory mblock injection. |
| MODULE_VERMAGIC_ABI | CLOSED_FOR_STRING_SURFACE / OPTIONAL_ELF_OPEN | Targeted grep found no modinfo string surface across all seven `.ko` files; ELF-level extraction remains optional if build tooling needs it. |

## kernel-platform relevance matrix

| Object family | 1.7 relevance | Classification basis |
| --- | --- | --- |
| chosen/video atag overlay | YES | Boot parameter / chosen-node platform payload; panel anomaly retained. |
| odm target population | YES | Changes root DT topology from static empty to applied populated. |
| Wiite/nRF nodes | YES | Board-specific coprocessor/control-plane kernel binding. |
| I2C bus configs and endpoints | YES | Kernel-visible bus topology; endpoint details may route elsewhere after 1.7 records platform facts. |
| PMU/regulators/supplies | YES | Kernel platform power dependency graph. |
| Pinctrl/GPIO/interrupt fixups | YES | Kernel applied board pin/interrupt topology. |
| Touch/camera/audio/consys/keypad endpoint details | PARTIAL | 1.7 records DTBO linkage/status/target; endpoint operation details belong outside 1.7. |
| DTC lint warnings | YES | Kernel platform source quality/anomaly record. |
| Full raw DTS duplication | NO | Source evidence file is retained separately; assessment stores classified objects and routing. |

##  DTBO final status

| Item | Status |
| --- | --- |
| DTBO extraction | CLOSED |
| DTBO decompile determinism | CLOSED |
| DTBO content inventory | CLOSED_FOR_CURRENT_FILES |
| DTC warnings | CLOSED_WITH_WARNINGS_PRESERVED |
| OB15 DTBO_OVERLAY_CONTENT | CLOSED_AS_CONTENT; BASE_APPLICATION_VALIDATION_CLOSED |
| Empty odm node finding | CLOSED_AS_STATIC_ONLY; DTBO_POPULATES_ODM |
| Static wiite_corp disabled finding | CLOSED_AS_STATIC_ONLY; DTBO_ENABLES_WIITE_CORP |
| Static wiite_con incomplete body | CLOSED_BY_DTBO_CONTENT |
| Kernel platform identity closure | CLOSED_FOR_CURRENT_EVIDENCE_SET |


################################################################################
# CURRENT-FILE SUPERSESSION MATRIX
################################################################################

| Current file item | Current value / status | Correct closeout value / status |
| --- | --- | --- |
| `DTBO_BASE_APPLICATION_VALIDATION` | OPEN | CLOSED. `fdtoverlay` applied `dtbo_entry0.dtb` to boot base FDT with status `<0>`. |
| `BASE_PHANDLE_PENDING` DTBO warnings | Open pending base application | Closed as standalone-overlay warning class; host-applied DTB generated successfully. Applied-DTS warnings remain separate DTC data. |
| `VIDEOLFB_FB_BASE_L_RENDERING` | OPEN because standalone DTBO DTS rendered `atag,videolfb-fb_base_l` as `"^`P"` | CLOSED by `fdtget` on host-applied and live FDT: `5e605000`; combined framebuffer base remains `0x000000005e605000`. |
| `FIRMWARE_ANDROID_COMPATIBLE_EMPTY` | OPEN / empty current capture | CLOSED/SUPERSEDED by live FDT: `/firmware/android compatible=android,firmware`, `hardware=mt6762`, `mode=normal`, `serialno=MTK0002507301608400`. |
| `/chosen` property count | 38 by `/proc/device-tree/chosen` listing | Preserve as sysfs-listing witness. Add FDT real-property counts: boot base `4`, host-applied `11`, live FDT `36`. |
| `dt.root node count` | 370 by `/proc/device-tree` listing | Preserve as sysfs-listing witness. Add FDT root subnode counts: boot base `366`, host-applied `366`, live FDT `368`. |
| `chosen.atag,fg_swocv_i` | `-1396` | PER_BOOT_VARIANT. Latest raw ASCII value: `-1298`, no NUL terminator; `fdtget -t s` returns `Unterminated string`. |
| `chosen.atag,fg_swocv_v` | `41755` | PER_BOOT_VARIANT. Latest raw ASCII value: `41937`, no NUL terminator; `fdtget -t s` returns `Unterminated string`. |
| `chosen.atag,imix_r` | raw `cb 00 00 00` | PER_BOOT_VARIANT. Latest raw `db 00 00 00`. Preserve raw bytes; do not force semantic decode. |
| `chosen.atag,devinfo STATIC` | STATIC | Source-class correction: live sysfs pseudo-file metadata must not be called stock-static unless raw/static source proves it. Current evidence class is live FDT `/chosen` payload, 804B, semantic struct open. |
| `MODULE_VERMAGIC_ABI` | OPEN requires `modinfo/readelf` | Modinfo string surface closed as absent by targeted grep on all seven `.ko` files. ELF ABI/vermagic remains optional if required by build tooling. |
| `Kernel platform identity closure` in 1.7.19.3 | NOT CLOSED until base application/live check | CLOSED for 1.7 after host `fdtoverlay` application and live FDT cross-check. |


################################################################################
# LK / BOOT BASE FDT / DTBO / LIVE FDT CLOSEOUT
################################################################################

Binary witnesses:

| Object | Bytes | SHA256 | Status |
| --- | ---: | --- | --- |
| `lk.bin` | 1,048,576 | `3860c8857554284a03cc844b7c8435c873355a7f175a36ae96d94890c702aa41` | CLOSED |
| boot base DTB entry | 89,545 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` | CLOSED |
| LK embedded FDT at `0xB1850` / `lk_main_dtb` | 89,545 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` | CLOSED |
| DTBO entry0 FDT | 29,974 | `2dc23aa3b77db4e644000769fa22b41c007706f85538705183b6f0c2f1c26d84` | CLOSED |
| host base+DTBO applied FDT | 111,829 | `4d5392842722fb9bbd59af5f04bae9235bc34391253dc66eeec826384808421f` | CLOSED |
| live kernel-applied FDT copied from `/sys/firmware/fdt` | 137,327 | `709de2ce167c51d82823ef2be59310e86d92d0cc6c0e90f12fa2163389e85b40` | CLOSED |

LK evidence directory corrected ZIP:

| Object | Value |
| --- | --- |
| zip path | `/mnt/data/lk_bin_evidence_directory.zip` |
| zip bytes | 2,926,164 |
| zip sha256 | `cc517c2f3f777dd511641bc4c0a83f09ec0ee2a63d42398beb38130e35e929db` |
| zip test | PASS |
| entries | 51 total; 43 files; 8 directories |
| uncompressed total | 11,243,523 bytes |
| SHA256SUMS verification | PASS |
| carved-segment reconstruction | concatenated carved segments == `lk.bin`; reconstructed length 1,048,576 bytes |
| zero gaps | `gap_1_pre_cert1_a.bin` 4B zero; `gap_2_pre_cert2_a.bin` 3B zero; `gap_3_pre_lk_main_dtb.bin` 3B zero; `gap_4_pre_cert1_b.bin` 7B zero; `gap_5_pre_cert2_b.bin` 3B zero; `tail_final_zero.bin` 228,211B zero |

LK-main-DTB relationship correction:

| Prior ZIP text | Correct 1.7 status |
| --- | --- |
| README / STRUCTURE_MAP still treats LK-main-DTB vs boot/kernel DTB as an open lead. | CLOSED: LK `lk_main_dtb` payload SHA256 equals boot DTB entry SHA256 and prior `cmp` status was 0. |
| `lk_main_dtb` SHA256 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| boot DTB entry SHA256 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| classification | `LK_MAIN_DTB_BYTE_IDENTICAL_TO_BOOT_BASE_DTB` |

Host applied DTS:

| Object | Value |
| --- | --- |
| `fdtoverlay` status | `<0>` |
| applied DTS lines | 4,771 |
| applied DTS bytes | 132,892 |
| applied DTS SHA256 | `146e5f26f794c63a43f92f8caef9e1b628025c8230c43c402808fd9e46c3332c` |
| applied warning lines | 138 |
| applied warning bytes | 26,369 |
| applied warning SHA256 | `4630480dfac279e728ab96a639d38e7b85a36ccb4020e79e57073489ccf4e235` |
| classification | `APPLIED_DTS_DECOMPILE_CLOSED_WITH_WARNINGS` |

Live FDT decompile:

| Object | Value |
| --- | --- |
| live FDT DTS lines | 4,861 |
| live FDT DTS bytes | 166,614 |
| live FDT DTC warning lines | 156 |
| live FDT DTC warning bytes | 30,288 |
| indexed warning file bytes | 30,804 |
| indexed warning SHA256 | `613e3be9affbc29bf471e4512de3e045a1a64e3fb8d7b2752e5aa12b5e6a509a` |
| live DTC status | `<0>` |
| live vs host-applied `cmp` | `<1>` |
| live vs boot-entry `cmp` | `<1>` |

Live DTC warning classes:

| Warning class | Count |
| --- | ---: |
| `status_is_string` | 2 |
| `unit_address_vs_reg` | 56 |
| `unit_address_format` | 60 |
| `simple_bus_reg` | 8 |
| `avoid_unnecessary_addr_size` | 1 |
| `unique_unit_address` | 23 |
| `gpios_property` | 1 |
| `interrupt_provider` | 5 |

Known live warning anchors:

| Warning class | Object / message |
| --- | --- |
| `status_is_string` | `/reserved-memory/reserve-memory-sspm_share:status: property is not a string` |
| `status_is_string` | `/scp@10500000:status: property is not a string` |
| `unit_address_vs_reg` | live `mblock-*` reserved-memory nodes |
| `simple_bus_reg` | `/odm/led@0..6`, `/odm/vibrator@0` |
| `gpios_property` | `/__symbols__:gpio: property size (15) is invalid, expected multiple of 4` |
| `interrupt_provider` | `/intpol-controller@10200a80`, `/pinctrl@1000b000`, `/mt-pmic`, `/mt6370_pmu_dts` |


################################################################################
# FDT STAGE COMPARISON / MUTATION MAP
################################################################################

FDT stage identity:

| Witness | Model | Compatible | `/memory reg` | Reserved-memory nodes | `/chosen` properties | SHA256 |
| --- | --- | --- | --- | ---: | ---: | --- |
| Boot base DTB / LK main DTB | `MT6765` | `mediatek,MT6765` | `0 40000000 0 20000000` | 5 | 4 | `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| Host base+DTBO applied FDT | `MT6765` | `mediatek,MT6765` | `0 40000000 0 20000000` | 5 | 11 | `4d5392842722fb9bbd59af5f04bae9235bc34391253dc66eeec826384808421f` |
| Live kernel-applied FDT | `MT6762V/WB` | `mediatek,MT6765` | `0 40000000 1 80000000` | 22 | 36 | `709de2ce167c51d82823ef2be59310e86d92d0cc6c0e90f12fa2163389e85b40` |

Root subnode counts:

| Witness | Lines | Bytes | SHA256 |
| --- | ---: | ---: | --- |
| Live FDT root subnode list | 368 | 6,376 | `38a41296a6174bca4633f7dbc4a6bbbedfa547416f741d24237d6b2022e3a49f` |
| Host base+DTBO root subnode list | 366 | 6,354 | `09d4e4c81bc8d10460e47d7b1aaa3734ea2c3b8c7dad462e1291a230cdebee7e` |
| Boot base entry root subnode list | 366 | 6,354 | `09d4e4c81bc8d10460e47d7b1aaa3734ea2c3b8c7dad462e1291a230cdebee7e` |

Live-only root additions relative to host-applied FDT:

| Live-only root node | Status |
| --- | --- |
| `firmware` | live FDT only relative to host-applied base+DTBO |
| `md_attr_node` | live FDT only relative to host-applied base+DTBO |

Live `/firmware` node:

| Path/property | Value / status |
| --- | --- |
| `/firmware` subnode | `android` |
| `/firmware/android` properties | `mode`, `serialno`, `hardware`, `compatible` |
| `/firmware/android/mode` | `normal` |
| `/firmware/android/serialno` | `MTK0002507301608400` |
| `/firmware/android/hardware` | `mt6762` |
| `/firmware/android/compatible` | `android,firmware` |

Live `/md_attr_node`:

| Property | Value |
| --- | --- |
| `compatible` | `mediatek,md_attr_node` |
| `mediatek,md_product_name_model_id` | `f0f0f0f` |
| `mediatek,md_drdi_rf_set_idx` | `f0f0f0f` |

Source-stage classification:

| Mutation | Classification |
| --- | --- |
| root model `MT6765` -> live `MT6762V/WB` | live FDT finalization / bootloader-runtime mutation; preserve as stage split |
| memory size `0x20000000` -> live `0x180000000` | live FDT memory rewrite from static 512 MiB template to 6 GiB final map |
| reserved-memory 5 -> 22 nodes | live reserved-memory mblock injection |
| `/chosen` 4 -> 11 -> 36 properties | boot base template + DTBO chosen overlay + live bootloader/runtime expansion |
| root additions `firmware`, `md_attr_node` | live bootloader/runtime FDT insertion |


################################################################################
# /chosen CLOSEOUT / LIVE EXPANSION
################################################################################

`/chosen` property-count witnesses:

| Witness | Properties | Bytes | SHA256 |
| --- | ---: | ---: | --- |
| Boot base DTB `/chosen` | 4 | 42 | `e69f897383d7c0374d622a580296b400fc224b9c39ae4f4389e9996027f4a814` |
| Host base+DTBO `/chosen` | 11 | 205 | `a761c83570cac76879d3526959465d43345d21cfb167dd072add986051654a40` |
| Live FDT `/chosen` | 36 | 582 | `d694cdabcaeeb76226010c00acb676c191b162f98999ea778bead8673002a99a` |

Live-only `/chosen` properties relative to host base+DTBO:

```text
atag,boot
atag,boot_voltage
atag,devinfo
atag,fg_swocv_i
atag,fg_swocv_v
atag,imix_r
atag,masp
atag,mdinfo
atag,mem
atag,ptp
atag,shutdown_time
atag,two_sec_reboot
atag,videolfb
ccci,modem_info_v2
dfd,base_addr
dfd,base_addr_msb
linux,initrd-end
linux,initrd-start
log_store
non_secure_sram
plat_dbg_info,base
plat_dbg_info,key
plat_dbg_info,max
plat_dbg_info,size
ram_console
```

Bootargs stage split:

| Witness | Bootargs class |
| --- | --- |
| Boot base DTB | short template: `console=tty0 console=ttyS0,921600n1 vmalloc=400M slub_debug=OFZPU page_owner=on swiotlb=noforce androidboot.hardware=mt6765 maxcpus=8 loop.max_part=7 firmware_class.path=/vendor/firmware` |
| Host base+DTBO | same short template |
| Live FDT | full final bootargs including boot header values and bootloader-appended values |
| `/proc/cmdline` | byte-equivalent token content to live FDT `/chosen/bootargs` |

Latest live bootprof values:

| Token | Value | Classification |
| --- | --- | --- |
| `bootprof.pl_t` | 1134 | PER_BOOT_VARIANT |
| `bootprof.lk_t` | 8043 | PER_BOOT_VARIANT |
| `bootprof.logo_t` | 999 | PER_BOOT_VARIANT |

Bootprof observed sequence:

| Capture class | `pl_t` | `lk_t` | `logo_t` |
| --- | ---: | ---: | ---: |
| Prior database capture | 1074 | 8057 | 999 |
| Prior 1.7 draft | 1128 | 8071 | 998 |
| Current live capture | 1130 | 8086 | 999 |
| Latest live FDT DTS | 1134 | 8043 | 999 |

`kaslr-seed` stage comparison:

| Witness | Raw value |
| --- | --- |
| Boot base DTB | `0 0 0 0 0 0 0 0` |
| Host base+DTBO | `0 0 0 0 0 0 0 0` |
| Live FDT | `0 0 0 0 0 0 0 0` |
| Classification | KASLR compiled capable; DT seed zero at read; inactive vs consumed-then-zeroed remains semantic open. |

Latest live raw `/chosen` updates:

| Property | Latest raw value / status | Classification |
| --- | --- | --- |
| `atag,fg_swocv_i` | ASCII bytes `2d 31 32 39 38` = `-1298`; no NUL terminator; `fdtget -t s` -> `Unterminated string` | PER_BOOT_VARIANT |
| `atag,fg_swocv_v` | ASCII bytes `34 31 39 33 37` = `41937`; no NUL terminator; `fdtget -t s` -> `Unterminated string` | PER_BOOT_VARIANT |
| `atag,imix_r` | raw `db 00 00 00` | PER_BOOT_VARIANT / semantic open |
| `atag,videolfb` | includes ASCII `lk_st7701S_V3_480X640_dsi_vdo` | panel identity anomaly retained |
| `atag,videolfb-lcmname` | `nt35695B_fhd_dsi_cmd_auo_rt5081_drv` | panel identity anomaly retained |
| `atag,videolfb-fb_base_l` | `5e605000`; combined framebuffer base `0x000000005e605000` | CLOSED |
| `atag,videolfb-vramSize` | `0x017bb000` = 24,887,296 bytes | CLOSED |
| `atag,videolfb-fps` | `0x00001770` = 6000 | CLOSED |
| `linux,initrd-start` | BE `0x51b00000` | CLOSED |
| `linux,initrd-end` | BE `0x51be17cd`; size `0xe17cd` = 922,061 bytes | CLOSED |
| `reserve-memory-sspm_share/status` | raw `6f 6b 61 79`; string-like `okay` without NUL | DTC `status_is_string` warning preserved |
| `/scp@10500000/status` | raw `6f 6b 61 79`; string-like `okay` without NUL | DTC `status_is_string` warning preserved |


################################################################################
# RESERVED-MEMORY NORMALIZATION CLOSEOUT
################################################################################

Reserved-memory subnode counts:

| Witness | Nodes | Lines | Bytes | SHA256 |
| --- | ---: | ---: | ---: | --- |
| Boot base DTB | 5 | 5 | 105 | `f2fbd4b67a5f5dc11339b17907de77227df2014fd7f9352e4ba1fd6c03ff2a99` |
| Host base+DTBO | 5 | 5 | 105 | `f2fbd4b67a5f5dc11339b17907de77227df2014fd7f9352e4ba1fd6c03ff2a99` |
| Live FDT | 22 | 22 | 459 | `db73b0fd9d7fd9d6f96ddea58f2dde3c74f62ac988bf1031b28f1a9e9f292f93` |

Live-only reserved-memory additions relative to host-applied FDT:

```text
mblock-10-framebuffer
mblock-11-SPM-reserved
mblock-12-SCP-reserved
mblock-13-ccci_tag_mem
mblock-14-ccci
mblock-15-md_smem_ncache
mblock-16-ccci
mblock-1-dramc-rk0
mblock-2-dramc-rk1
mblock-3-log_store
mblock-4-atf-reserved
mblock-5-atf-log-reserved
mblock-6-SSPM-reserved
mblock-7-ram_console
mblock-8-pstore
mblock-9-minirdump
zone-movable-cma-memory
```

Live reserved-memory normalized table:

| Node | Compatible | Reg | Size | Alignment | Alloc-ranges | Status raw | no-map |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `mblock-16-ccci` | `mediatek,ccci` | `0 78000000 0 1600000` | absent | absent | absent | absent | yes |
| `mblock-15-md_smem_ncache` | `mediatek,md_smem_ncache` | `0 7a000000 0 170000` | absent | absent | absent | absent | no |
| `mblock-14-ccci` | `mediatek,ccci` | `0 8e000000 0 10000000` | absent | absent | absent | absent | no |
| `mblock-13-ccci_tag_mem` | `mediatek,ccci_tag_mem` | `0 9fff0000 0 10000` | absent | absent | absent | absent | no |
| `mblock-12-SCP-reserved` | `mediatek,SCP-reserved` | `0 9f900000 0 600000` | absent | absent | absent | absent | yes |
| `mblock-11-SPM-reserved` | `mediatek,SPM-reserved` | `0 77ff0000 0 10000` | absent | absent | absent | absent | yes |
| `mblock-10-framebuffer` | `mediatek,framebuffer` | `0 7fae0000 0 420000` | absent | absent | absent | absent | yes |
| `mblock-9-minirdump` | `mediatek,minirdump` | `0 47d70000 0 10000` | absent | absent | absent | absent | yes |
| `mblock-8-pstore` | `mediatek,pstore` | `0 47c90000 0 e0000` | absent | absent | absent | absent | yes |
| `mblock-7-ram_console` | `mediatek,ram_console` | `0 47c80000 0 10000` | absent | absent | absent | absent | yes |
| `mblock-6-SSPM-reserved` | `mediatek,SSPM-reserved` | `0 7ff00000 0 c0000` | absent | absent | absent | absent | yes |
| `mblock-5-atf-log-reserved` | `mediatek,atf-log-reserved` | `0 bffbf000 0 40000` | absent | absent | absent | absent | yes |
| `mblock-4-atf-reserved` | `mediatek,atf-reserved` | `0 47d80000 0 50000` | absent | absent | absent | absent | yes |
| `mblock-3-log_store` | `mediatek,log_store` | `0 7ffc0000 0 40000` | absent | absent | absent | absent | no |
| `mblock-2-dramc-rk1` | `mediatek,dramc-rk1` | `1 bffff000 0 1000` | absent | absent | absent | absent | yes |
| `mblock-1-dramc-rk0` | `mediatek,dramc-rk0` | `0 bffff000 0 1000` | absent | absent | absent | absent | yes |
| `zone-movable-cma-memory` | `mediatek,zone_movable_cma` | absent | `0 ffc00000` | `0 10000000` | `0 c0000000 1 80000000` | absent | no |
| `zmc-default` | `mediatek,zone_movable_cma` | absent | `0 ffc00000` | `0 10000000` | `0 c0000000 1 80000000` | `64 69 73 61 62 6c 65 64 00` (`disabled\0`) | no |
| `reserve-memory-sspm_share` | `mediatek,reserve-memory-sspm_share` | absent | `0 510000` | `0 10000` | `0 40000000 0 60000000` | `6f 6b 61 79` (`okay` without NUL) | yes |
| `reserve-memory-scp_share` | `mediatek,reserve-memory-scp_share` | absent | `0 300000` | `0 1000000` | `0 40000000 0 50000000` | absent | yes |
| `consys-reserve-memory` | `mediatek,consys-reserve-memory` | absent | `0 400000` | `0 1000000` | `0 40000000 0 80000000` | absent | yes |
| `wifi-reserve-memory` | `mediatek,wifi-reserve-memory` | absent | `0 300000` | `0 1000000` | `0 40000000 0 80000000` | absent | yes |

Targeted negative lead:

| Queried node | Result | Classification |
| --- | --- | --- |
| `/reserved-memory/scp_share` | `FDT_ERR_NOTFOUND` for compatible, reg, size, alignment, alloc-ranges, status, no-map grep | `TARGETED_NEGATIVE_LEAD`; correct existing node is `/reserved-memory/reserve-memory-scp_share` |


################################################################################
# LK STRING ANCHOR SURFACE
################################################################################

LK string hits:

| String | Offset(s) in `lk.bin` |
| --- | --- |
| `md_attr_node` | 510756, 510845 |
| `mediatek,md_attr_node` | 510836 |
| `md_product_name_model_id` | 510809 |
| `md_drdi_rf_set_idx` | 510781 |
| `android,firmware` | 385956 |
| `serialno` | 385944, 494256 |
| `bootreason` | 491308, 491340, 491376, 491412, 491444, 491476, 491512, 491556, 491592, 491632, 491672, 491708, 491748, 491784, 491812, 491840, 491880, 491908, 491944, 491972, 492012, 492044, 492076, 492116, 492148 |
| `mblock-` | 515988, 516075, 516167, 516263, 516347, 516408, 518500, 518552 |
| `zone-movable-cma-memory` | 518788, 518816, 518856 |
| `reserve-memory-sspm_share` | 477809, 730940, 730989 |
| `reserve-memory-scp_share` | 731124, 731173 |
| `consys-reserve-memory` | 731288, 731333 |
| `wifi-reserve-memory` | 731444, 731485 |
| `linux,initrd-start` | 493016 |
| `linux,initrd-end` | 493036 |
| `dfd,base_addr` | 474132, 474148 |
| `ccci,modem_info_v2` | 510860 |
| `atag,devinfo` | 493700 |
| `atag,masp` | 493860 |
| `atag,videolfb` | 377100, 377124, 377148, 493716 |
| `plat_dbg_info` | 519572, 519592, 519612, 519632 |

Classification:

| Item | Status |
| --- | --- |
| LK string anchor surface | PRESENT |
| Control-flow proof | NOT PROVEN by string search alone |
| Database use | record as `LK_STRING_ANCHOR_SURFACE_PRESENT`; do not assign writer/execution role without LK control-flow review |


################################################################################
# KERNEL RUNTIME EVIDENCE PRESERVATION
################################################################################

Primary kernel runtime artifact directory:

| Host path | `/home/beright1976
| --- | --- |

| File | Bytes | SHA256 |
| --- | ---: | --- |
| `proc_config.gz` | 28,345 | `9346c47bbcdffad6576364b4e3386c7d5f50a7961f99261c767a119525b8398d` |
| `proc_crypto.txt` | 10,280 | `6a4ad45137b72abd514efb984eaf4f0c9d783c56d1567016ad67fe6fdc51b189` |
| `proc_filesystems.txt` | 352 | `b5de60b5ade93ab2b5b3dc98943b6dfd6ba98c1a58e33529f5c03e79fa1a1cde` |
| `proc_modules.txt` | 353 | `b5e2959cb9bba372d8e287869e47f062ccdc852afe905b8945117890ad6d6b67` |
| `proc_self_mountinfo.txt` | 5,701 | `8ba0a150b2c9105d80494e0ad508abb6685c2ba44ddf521bbf055f9656061c92` |

Runtime tunable artifact directory:

| Host path | `/home/beright1976/Downloads/boot_evidence/07_kernel_runtime/runtime_2` |
| --- | --- |

| File | Bytes | SHA256 |
| --- | ---: | --- |
| `dmesg_restrict.txt` | 2 | `9a271f2a916b0b6ee6cecb2426f0b3206ef074578be55d9bc94f6f3fe3ab86aa` |
| `firmware_class_path.txt` | 17 | `2b586817c80a78f54d94af2f8780e1fbeabaf2d94738823827a529f214df3414` |
| `kptr_restrict.txt` | 2 | `53c234e5e8472b6ac51c1ae1cab3fe06fad053beb8ebfd8977b010655bfdd3c3` |
| `modules_disabled.txt` | 2 | `9a271f2a916b0b6ee6cecb2426f0b3206ef074578be55d9bc94f6f3fe3ab86aa` |
| `perf_event_paranoid.txt` | 2 | `1121cfccd5913f0a63fec40a6ffd44ea64f9dc135c66634ba001d10bcf4302a2` |
| `randomize_va_space.txt` | 2 | `53c234e5e8472b6ac51c1ae1cab3fe06fad053beb8ebfd8977b010655bfdd3c3` |
| `sysrq.txt` | 2 | `9a271f2a916b0b6ee6cecb2426f0b3206ef074578be55d9bc94f6f3fe3ab86aa` |
| `tainted.txt` | 5 | `a6e6c65a43863d033e37ced4e600ef52e9f358519c1d23042055e0b9ee2a8715` |


################################################################################
# KERNEL CONFIG CLOSEOUT
################################################################################

`/proc/config.gz` live pseudo-file:

| Field | Value |
| --- | --- |
| path | `/proc/config.gz` |
| mode | `0444/-r--r--r--` |
| owner | `root:root` |
| SELinux label | `u:object_r:config_gz:s0` |
| live size | 28,345 bytes |
| preserved host SHA256 | `9346c47bbcdffad6576364b4e3386c7d5f50a7961f99261c767a119525b8398d` |

Key config line recheck:

| Symbol | Value |
| --- | --- |
| `CONFIG_ARM64` | `y` |
| `CONFIG_SMP` | `y` |
| `CONFIG_AUDIT` | `y` |
| `CONFIG_IKCONFIG` | `y` |
| `CONFIG_IKCONFIG_PROC` | `y` |
| `CONFIG_KALLSYMS` | `y` |
| `CONFIG_KALLSYMS_ALL` | not set |
| `CONFIG_KPROBES` | not set |
| `CONFIG_MODULES` | `y` |
| `CONFIG_MODULE_UNLOAD` | `y` |
| `CONFIG_PREEMPT` | `y` |
| `CONFIG_DEVTMPFS` | not set |
| `CONFIG_FW_LOADER` | `y` |
| `CONFIG_OF` | `y` |
| `CONFIG_OF_FLATTREE` | `y` |
| `CONFIG_OF_OVERLAY` | not set |
| `CONFIG_DM_CRYPT` | `y` |
| `CONFIG_DM_VERITY` | `y` |
| `CONFIG_EXT4_FS` | `y` |
| `CONFIG_F2FS_FS` | not set |
| `CONFIG_OVERLAY_FS` | `y` |
| `CONFIG_VFAT_FS` | `y` |
| `CONFIG_TMPFS` | `y` |
| `CONFIG_SQUASHFS` | not set |
| `CONFIG_DEBUG_FS` | `y` |
| `CONFIG_KEYS` | `y` |
| `CONFIG_ENCRYPTED_KEYS` | not set |
| `CONFIG_SECURITY` | `y` |
| `CONFIG_SECURITY_SELINUX` | `y` |
| `CONFIG_CRYPTO` | `y` |
| `CONFIG_CRYPTO_SHA256` | `y` |
| `CONFIG_CRYPTO_AES` | `y` |
| host grep status | `<0>` |

Non-emitted exact-query symbols:

| Symbol | Handling |
| --- | --- |
| `CONFIG_EROFS_FS` | no line emitted by exact grep; do not record as `y` or `not set` from this query |
| `CONFIG_TRUSTED_KEYS` | no line emitted by exact grep; do not record as `y` or `not set` from this query |

Malformed earlier shell status:

| Field | Value |
| --- | --- |
| `CONFIG_GREP_STATUS` | empty field in adb shell capture |
| Classification | shell-context status formatting anomaly; later host recheck returned `HOST_CONFIG_GREP_STATUS=<0>` |


################################################################################
# MODULE INVENTORY CLOSEOUT
################################################################################

Module directories:

| Directory | Status |
| --- | --- |
| `/vendor/lib/modules` | present |
| `/vendor_dlkm/lib/modules` | absent |
| `/system/lib/modules` | absent |
| `/lib/modules` | absent |

All vendor module binary identities:

| Module file | Bytes | SHA256 | Load state |
| --- | ---: | --- | --- |
| `/vendor/lib/modules/bt_drv.ko` | 52,216 | `cb8de1956ff35f2d62b1a09313c1cd86d053f3bb0a3d075df5f6bcc99e27b0be` | loaded |
| `/vendor/lib/modules/fpsgo.ko` | 34,608 | `7dce5fbbaa537c7513cc1e7a2f9db060d468b3cff77c33beadc580d736a382bf` | loaded |
| `/vendor/lib/modules/gps_drv.ko` | 72,192 | `2e7e750e4db4debef2cb238c0ccabac9abc3627506c030d872f4473d3b846ff8` | loaded |
| `/vendor/lib/modules/met.ko` | 901,856 | `39b066ea41161046b5298dbecb659c48602bd1109ed4a541dbb7ba1fe148c266` | present-not-loaded |
| `/vendor/lib/modules/wlan_drv_gen4m.ko` | 5,158,760 | `f19c5675b02c6feb8e73c5d6ad31c23968c9e0931fc7a48229a72b76c2805175` | loaded |
| `/vendor/lib/modules/wmt_chrdev_wifi.ko` | 57,720 | `e01a7e1f3feae836bd6027f261d722eb08ebec89cd79078ec5b4b11988e7f594` | loaded |
| `/vendor/lib/modules/wmt_drv.ko` | 2,133,064 | `2726de3e349b3cecfa533978bc5583c4c0e817c76e88c8a28bd409f7c121a785` | loaded |

Common file metadata class:

| Field | Value |
| --- | --- |
| mode | `0644/-rw-r--r--` |
| owner | `root:root` |
| SELinux label | `u:object_r:vendor_file:s0` |
| modify time | `2008-12-31 18:00:00.000000000 -0600` |
| change time | `2008-12-31 18:00:00.000000000 -0600` |
| classification | stripped/static vendor timestamp class; cause open |

Loaded runtime modules:

| Module | `/proc/modules` row |
| --- | --- |
| `wlan_drv_gen4m` | `wlan_drv_gen4m 1994752 0 - Live 0x0000000000000000 (O)` |
| `wmt_chrdev_wifi` | `wmt_chrdev_wifi 28672 1 wlan_drv_gen4m, Live 0x0000000000000000 (O)` |
| `gps_drv` | `gps_drv 65536 0 - Live 0x0000000000000000 (O)` |
| `bt_drv` | `bt_drv 28672 0 - Live 0x0000000000000000 (O)` |
| `wmt_drv` | `wmt_drv 1224704 5 wlan_drv_gen4m,wmt_chrdev_wifi,gps_drv,bt_drv, Live 0x0000000000000000 (O)` |
| `fpsgo` | `fpsgo 811008 0 - Live 0x0000000000000000 (PO)` |

Loaded module sysfs state:

| Module | initstate | refcnt | taint | holders |
| --- | --- | ---: | --- | --- |
| `wlan_drv_gen4m` | live | 0 | `O` | empty |
| `wmt_chrdev_wifi` | live | 1 | `O` | `wlan_drv_gen4m` |
| `gps_drv` | live | 0 | `O` | empty |
| `bt_drv` | live | 0 | `O` | empty |
| `wmt_drv` | live | 5 | `O` | `bt_drv gps_drv wlan_drv_gen4m wmt_chrdev_wifi` |
| `fpsgo` | live | 0 | `PO` | empty |

`met.ko` classification:

| Probe | Result |
| --- | --- |
| `/vendor/lib/modules/met.ko` | present |
| `grep -w "^met" /proc/modules` | status `<1>` |
| `ls -laZ /sys/module/met` | no such file or directory; status `<1>` |
| classification | `PRESENT_VENDOR_MODULE_NOT_LOADED` |

Module modinfo string surface:

| Module | Targeted modinfo grep status |
| --- | ---: |
| `bt_drv.ko` | 1 |
| `fpsgo.ko` | 1 |
| `gps_drv.ko` | 1 |
| `met.ko` | 1 |
| `wlan_drv_gen4m.ko` | 1 |
| `wmt_chrdev_wifi.ko` | 1 |
| `wmt_drv.ko` | 1 |
| classification | no matched `vermagic=`, `name=`, `version=`, `license=`, `author=`, `description=`, `depends=`, `intree=`, `retpoline=`, `srcversion=`, or `alias=` strings by targeted grep |

`/sys/module` sorted namespace captured:

```text
8021q
8250
aed
alarm_dev
binder
binder_alloc
block
bootprof
brd
bridge
bt_drv
c2k_usb
cfg80211
charger_class
configfs
cpuidle
cryptomgr
dm_bufio
dm_mod
dm_verity
drm
drm_kms_helper
dynamic_debug
engine_request
fb
fbt_cpu
fbt_fteh
firmware_class
fpsgo
fscrypto
fuse
ged
gps_drv
hid
hid_apple
hid_magicmouse
i2c_algo_bit
ip6_tunnel
ipip
ipv6
kernel
l2tp_core
l2tp_ppp
libcomposite
loop
lowmem_hint
lowmemorykiller
mlog_logger
mmcblk
mmdvfs_pmqos
module
mrdump
mt6370_pmu_bled
mt6370_pmu_charger
mt6370_pmu_core
mt6370_pmu_fled
mt6370_pmu_i2c
mt6370_pmu_ldo
mtk_cmdq_mailbox
mtk_gauge_class
mtk_leds_drv
mtk_power_gs
mtk_rtc_common
mtk_rtc_hal
mtk_sleep
musb_hdrc
nf_conntrack
nf_conntrack_amanda
nf_conntrack_ftp
nf_conntrack_h323
nf_conntrack_ipv4
nf_conntrack_irc
nf_conntrack_netbios_ns
nf_conntrack_sane
nf_conntrack_tftp
overlay
ppp_generic
ppp_mppe
printk
pstore
pvrsrvkm
r8152
ramoops
random
rcupdate
rcutree
rt5738_regulator
rt_flashlight
rt_regmap
rtfled
sch_htb
scsi_mod
sec
sit
smi_legacy
snd
snd_pcm
snd_rawmidi
snd_seq
snd_seq_dummy
snd_seq_midi
snd_seq_oss
snd_soc_rt5509
snd_timer
snd_usb_audio
soundcore
spurious
sysrq
tcp_bic
tcp_cubic
tpd_debug
tpd_misc
tpd_setting
u_ether
uinput
upmu
upmu_debugfs
upmu_regulator
usb20
usb20_host
usb_boost
usb_f_mtp
usb_f_rndis
usb_storage
usbcore
usbhid
wlan_drv_gen4m
wmt_chrdev_wifi
wmt_drv
workqueue
xgf
xt_quota2
xt_recent
```


################################################################################
# FILESYSTEM / CRYPTO / DM CLOSEOUT
################################################################################

`/proc/filesystems`:

| Class | Filesystems |
| --- | --- |
| nodev | `sysfs`, `rootfs`, `ramfs`, `bdev`, `proc`, `cpuset`, `cgroup`, `cgroup2`, `tmpfs`, `configfs`, `debugfs`, `tracefs`, `sockfs`, `bpf`, `pipefs`, `devpts`, `sdcardfs`, `fuse`, `fusectl`, `overlay`, `pstore`, `selinuxfs`, `functionfs` |
| block | `ext3`, `ext2`, `ext4`, `vfat`, `msdos`, `iso9660`, `fuseblk` |
| not listed | `f2fs` |

`/proc/crypto` full inventory counts:

| Row type | Count |
| --- | ---: |
| `name` | 45 |
| `driver` | 45 |
| `type` | 45 |

Crypto type counts:

| Type | Count |
| --- | ---: |
| `ablkcipher` | 6 |
| `blkcipher` | 6 |
| `cipher` | 8 |
| `compression` | 4 |
| `rng` | 9 |
| `shash` | 11 |
| `skcipher` | 1 |

Crypto names sorted:

```text
aes
aes
arc4
blowfish
cbc(aes)
__cbc-aes-ce
__cbc-aes-ce
cipher_null
compress_null
crc32c
ctr(aes)
__ctr-aes-ce
cts(cbc(aes))
deflate
des
des3_ede
digest_null
ecb(aes)
__ecb-aes-ce
__ecb-aes-ce
ecb(arc4)
ecb(cipher_null)
ghash
jitterentropy_rng
lz4
lzo
md5
sha1
sha224
sha224
sha256
sha256
sha384
sha512
stdrng
stdrng
stdrng
stdrng
stdrng
stdrng
stdrng
stdrng
twofish
xts(aes)
__xts-aes-ce
```

Device-mapper / crypto runtime:

| Node | Status |
| --- | --- |
| `/sys/module/dm_crypt` | absent |
| `/sys/module/dm_mod` | present |
| `/sys/module/dm_verity` | present |
| `/sys/module/fscrypto` | present |
| `/sys/block/dm-0` | name `product`, uuid `1`, ro `1` |
| `/sys/block/dm-1` | name `system`, uuid `1`, ro `1` |
| `/sys/block/dm-2` | name `vendor`, uuid `1`, ro `1` |
| `/sys/block/dm-3` | name `com.android.tzdata@299200000`, uuid `1`, ro `1` |

Classification:

| Item | Status |
| --- | --- |
| full crypto API inventory | CLOSED |
| dm runtime surface | CLOSED |
| `dm_crypt` module directory absence | record exact absence; compiled `CONFIG_DM_CRYPT=y` remains separate from visible `/sys/module/dm_crypt` runtime directory |


################################################################################
# DEBUGFS / TRACEFS / PSTORE / LOG / KEYRING CLOSEOUT
################################################################################

Kernel pseudo-fs mounts:

| Filesystem | Mount |
| --- | --- |
| `selinuxfs` | `/sys/fs/selinux` rw |
| `debugfs` | `/sys/kernel/debug` rw,seclabel |
| `tracefs` | `/sys/kernel/debug/tracing` rw,seclabel |
| `configfs` | `/config` rw,nosuid,nodev,noexec |
| `pstore` | `/sys/fs/pstore` rw,nosuid,nodev,noexec,seclabel |

Debugfs top-level names:

```text
asoc
bdi
binder
blockio
blocktag
clk
cpuidle
debug_enabled
devinfo
disp
displowpower
dispsys
dma_buf
dmlog
dri
dynamic_debug
emi_mbw
extfrag
fault_around_bytes
fbconfig
fpsgo
ged
gpio
hid
ieee80211
iio
ion
irq_domain_mapping
keypad
m4u
memblock
mlog
mlog_fmt
mmc0
mmc1
mmprofile
mtk_pmic
mtk_rtc
mtkfb
mtksocanaaudio
mtksocaudio
musb
pinctrl
pm_qos
pvr
regmap
regulator
rt-regmap
sched_features
sleep_time
static_power
suspend_stats
sync
sys_timer
tracing
usb
usb20_phy
wakeup_sources
```

Tracefs exposure:

| Node | Mode / label |
| --- | --- |
| `/sys/kernel/debug/tracing/trace` | `rw-rw-rw-`, `u:object_r:debugfs_tracing:s0` |
| `/sys/kernel/debug/tracing/trace_marker` | `--w--w--w-`, `u:object_r:debugfs_trace_marker:s0` |
| `/sys/kernel/debug/tracing/tracing_on` | `rw-rw-rw-`, `u:object_r:debugfs_tracing:s0` |
| `/sys/kernel/tracing` | exists as empty sysfs directory |
| classification | tracefs mounted under debugfs; writable trace surfaces exposed in live research state |

Pstore:

| Node | Status |
| --- | --- |
| `/sys/fs/pstore` | present; `dr-xr-x--- system:log u:object_r:pstorefs:s0` |
| contents in capture | empty |

Kernel log surface:

| Node | Mode / label |
| --- | --- |
| `/dev/kmsg` | `crw------- root:root u:object_r:kmsg_device:s0` |
| `/proc/kmsg` | `-r--r----- root:system u:object_r:proc_kmsg:s0` |
| `dmesg` under `su` | readable; status `<0>` |
| notable dmesg live anchor | repeated `[WIITE_CON_CTRL]Try to set pin(2) to state(0/1)\x0a`; MT6370 charger/PMU logs also visible |

Keyring surface:

| Node | Status |
| --- | --- |
| `/proc/keys` | present, `u:object_r:proc_keys:s0` |
| `/proc/key-users` | present |
| `/proc/sys/kernel/keys` | present |
| `/proc/sys/kernel/keys/root_maxkeys` | `1000000` |
| `/proc/sys/kernel/keys/root_maxbytes` | `25000000` |

`/proc/key-users`:

```text
0:    16 15/15 13/1000000 930/25000000
```

`/proc/keys` visible head:

```text
011609f1 I--Q---     1 perm 3d010000     0     0 logon     ext4:c38416f0aac882df: 72
02d18fbb I--Q---     1 perm 3d010000     0     0 logon     f2fs:14e26f60b11e583e: 72
04fc1225 I--Q---     1 perm 3d010000     0     0 logon     f2fs:2e2d8176aa254dd1: 72
065f7121 I--Q---  4603 perm 3d010000     0     0 logon     fscrypt:2e2d8176aa254dd1: 72
135d2c1b I--Q---     1 perm 3d010000     0     0 logon     f2fs:c38416f0aac882df: 72
163986ea I--Q---  2123 perm 3d010000     0     0 logon     fscrypt:14e26f60b11e583e: 72
1a4944e4 I--Q---   935 perm 3d010000     0     0 logon     fscrypt:c38416f0aac882df: 72
1c649a64 I--Q---     1 perm 3d010000     0     0 logon     ext4:2e2d8176aa254dd1: 72
20da4719 I--Q---     2 perm 1f3f0000     0 65534 keyring   _uid.0: empty
2673d2b0 I--Q---     1 perm 3f010000     0     0 keyring   fscrypt: 9
2f98fbd4 I--Q---   459 perm 3f030000     0     0 keyring   _ses: 1
36aa4795 I--Q---     1 perm 3d010000     0     0 logon     ext4:14e26f60b11e583e: 72
3dee6693 I--Q---     1 perm 1f3f0000     0 65534 keyring   _uid_ses.0: 1
```

Classification:

| Surface | Status |
| --- | --- |
| debugfs / tracefs / pstore | CLOSED |
| kernel log exposure | CLOSED |
| keyring visibility | CLOSED |


################################################################################
# KERNEL POLICY TUNABLES / SELINUX / FIRMWARE CLASS
################################################################################

Kernel tunables:

| Path | Value | SELinux label |
| --- | --- | --- |
| `/proc/sys/kernel/kptr_restrict` | `2` | `u:object_r:proc_security:s0` |
| `/proc/sys/kernel/perf_event_paranoid` | `3` | `u:object_r:proc_perf:s0` |
| `/proc/sys/kernel/dmesg_restrict` | `0` | `u:object_r:proc_security:s0` |
| `/proc/sys/kernel/modules_disabled` | `0` | `u:object_r:proc_security:s0` |
| `/proc/sys/kernel/randomize_va_space` | `2` | `u:object_r:proc_security:s0` |
| `/proc/sys/kernel/sysrq` | `0` | `u:object_r:proc_sysrq:s0` |
| `/proc/sys/kernel/panic` | `1` | `u:object_r:proc:s0` |
| `/proc/sys/kernel/panic_on_oops` | `1` | `u:object_r:proc_panic:s0` |
| `/proc/sys/kernel/printk` | `7 4 1 7` | `u:object_r:proc:s0` |
| `/proc/sys/kernel/tainted` | `4609` | `u:object_r:proc:s0` |
| `/proc/sys/kernel/keys/root_maxkeys` | `1000000` | `u:object_r:proc:s0` |
| `/proc/sys/kernel/keys/root_maxbytes` | `25000000` | `u:object_r:proc:s0` |

SELinux kernel surface:

| Node | Value / data |
| --- | --- |
| `/sys/fs/selinux/enforce` | `0` |
| `/sys/fs/selinux/policyvers` | `30` |
| `/sys/fs/selinux/mls` | `1` |
| `/sys/fs/selinux/checkreqprot` | `0` |
| `/sys/fs/selinux/deny_unknown` | `1` |
| `/sys/fs/selinux/status` stat size | `0` |
| `/sys/fs/selinux/status` read bytes | `01 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 01 00 00 00` |
| classification | `SELINUXFS_ZERO_STAT_SIZE_PSEUDOFILE_WITH_20_BYTE_READ`; `enforce=0` is `LIVE_RESEARCH_STATE` |

Firmware class runtime:

| Node | Value |
| --- | --- |
| `/sys/module/firmware_class` | present |
| `/sys/module/firmware_class/parameters/path` | `/vendor/firmware` |
| `/sys/module/firmware_class/parameters/ignore_sysfs_fallback` | absent |
| command-line cross-witness | `firmware_class.path=/vendor/firmware` |


################################################################################
# CLOSEOUT MATRIX FOR FORMER OPEN ITEMS
################################################################################

| Former boundary | New status | Resolution |
| --- | --- | --- |
| `DTBO_BASE_APPLICATION_VALIDATION` | CLOSED | `fdtoverlay` applied `dtbo_entry0.dtb` against boot base FDT; output FDT created, SHA256 captured. |
| `BASE_PHANDLE_DEPENDENCY` warnings | CLOSED_AS_STANDALONE_OVERLAY_ONLY | Standalone DTBO warnings are expected unresolved fixup placeholders; base application succeeded. |
| `DTC_INTERRUPT_PHANDLE_WARNINGS` | CLOSED_AS_STANDALONE_OVERLAY_ONLY | Same closure as above. Applied/live DTC warning surfaces are preserved separately. |
| `DTC_GPIO_FIXUP_WARNING` | CLOSED_AS_STANDALONE_OVERLAY_ONLY | `__fixups__` parser warning retained but no longer blocks 1.7 closure. |
| `VIDEOLFB_FB_BASE_L_RENDERING` | CLOSED | host-applied/live `fdtget` reads `5e605000`; standalone DTS string rendering is DTC representation anomaly. |
| `FIRMWARE_ANDROID_COMPATIBLE_EMPTY` | CLOSED/SUPERSEDED | live FDT `/firmware/android compatible=android,firmware`. |
| `EMPTY_STATIC_ODM_NODE` | CLOSED_AS_STATIC_ONLY | raw base `odm` empty; DTBO fragment@1 populates `led@0..6` and `vibrator@0`; live applied tree contains ODM population. |
| `STATIC_WIITE_CORP_DISABLED` | CLOSED_AS_STATIC_ONLY | raw base disabled; DTBO fragment@13 enables with `status=okay` and `vhrs-supply`; live applied tree confirms. |
| `STATIC_WIITE_CON_BODY_INCOMPLETE` | CLOSED_BY_DTBO | DTBO fragment@11 adds `wiite,con1`, `wiite,con2`, pinctrl names/refs; live applied tree confirms. |
| `RAW_BOOT_MEMORY_ZMC_CONTRADICTION` | CLOSED_AS_STATIC_TEMPLATE_VS_LIVE_REWRITE | raw boot base memory is 512 MiB template; live FDT memory is 6 GiB and live reserved mblocks are injected. Keep static contradiction as source-stage data, not active runtime map. |
| `MODULE_VERMAGIC_ABI` | CLOSED_FOR_STRING_SURFACE / OPTIONAL_ELF_OPEN | targeted grep found no modinfo strings on all seven modules. ELF-level ABI extraction remains optional if build tooling needs it. |
| `KERNEL_WARN_SOURCE` | PARTIAL_CLOSED | `dmesg` is readable and shows active warning/log surface; exact origin of taint bit W remains open unless log scan is required. |
| `MNL_BIN_ZERO_FILLED` | UNCHANGED_OPEN | no new consumer/path proof in this session. |
| `ATAG_* STRUCTS` | UNCHANGED_OPEN | raw payloads preserved; full semantic decode requires MTK LK/preloader/BSP struct mapping. |
| `KASLR_SEED_ZERO` | UNCHANGED_OPEN | all three FDT witnesses show zero seed; inactive vs consumed-then-zeroed unresolved. |
| `CMDLINE_DOUBLE_SPACE` | UNCHANGED_OPEN | byte anomaly preserved; cause unresolved. |
| `PANEL_ID_DIVERGENCE` | UNCHANGED_OPEN_ANOMALY | `lk_st7701S...` vs `nt35695B...` retained for display-section reconciliation. |
| `GPIO_INIT_DEFAULT_DECODE` | UNCHANGED_OPEN_OPTIONAL | raw table retained; semantic decode requires MTK GPIO table format. |
| `MSDC2_MSDC3_NAME_REG_MISMATCH` | UNCHANGED_OPEN | no new live/applied resolution in this session. |
| `ZERO_SIZE_REG_PROPERTIES` | UNCHANGED_OPEN | no new driver-source binding proof in this session. |
| `MT6370_DC_WDT_REPEATED_VALUE` | UNCHANGED_OPEN | no new semantic proof. |
| `DTB_ADDR_TAGS_ADDR_COLLISION` | UNCHANGED_OPEN | no new boot-header semantic proof. |
| `BOOTIMG_ID_SHA1_REPRODUCTION` | UNCHANGED_OPEN | no new hash reproduction proof. |


################################################################################
# OPEN — REMAINING OPEN BOUNDARIES AFTER CLOSEOUT
################################################################################

| # | Boundary | Status | Required closure data / reason retained |
| ---: | --- | --- | --- |
| 1 | `ATAG_DEVINFO_STRUCT_DECODE` | OPEN | MTK devinfo struct definition from preloader/LK/BSP headers or reverse-engineered binary struct mapping. Current payload retained as 804B live `/chosen` value; do not call stock-static without raw/static proof. |
| 2 | `ATAG_MASP_SEMANTIC` | OPEN | MTK MASP atag definition; current data: 88B, ASCII hex id `1A52A367CB12C458`, not string-equal to ME_ID/SOC_ID anchors. |
| 3 | `ATAG_STRUCTS_UNDECODED` | OPEN | Structs for `atag,mem`, `atag,boot`, `atag,mdinfo`, `atag,ptp`, `ccci,modem_info_v2`; raw bytes retained. |
| 4 | `MNL_BIN_ZERO_FILLED` | OPEN | Consumer/path proof for `/vendor/firmware/MNL.bin`; current data: 128B all zero. |
| 5 | `MODULE_FIRMWARE_TIMESTAMP_STRIPPED` | OPEN | Source/cause of identical `2008-12-31 18:00` timestamps across modules and firmware. |
| 6 | `KALLSYMS_ADDRESSES_SUPPRESSED` | OPEN | Live names visible, all addresses zeroed by `kptr_restrict=2`; real addresses require kernel-binary extraction. |
| 7 | `KALLSYMS_DUPLICATE_SYMBOL` | OPEN | Binary/symbol-table analysis for duplicate `gic_handle_irq`. |
| 8 | `CRYPTO_CONTENTS_MODE_PROPERTY_NON_EXPOSURE` | OPEN | Contents mode present in `/data/unencrypted/mode`; absent from `ro.crypto.volume.contents_mode`; AOSP-normal vs ODM omission unresolved. |
| 9 | `KERNEL_KASLR_SEED_ZERO` | OPEN | KASLR compiled; all observed FDT seeds zero; inactive vs consumed-then-zeroed unresolved. |
| 10 | `CMDLINE_DOUBLE_SPACE` | OPEN | Byte-confirmed `0x20 0x20` between `root=/dev/ram` and `androidboot.verifiedbootstate`; cause unresolved. |
| 11 | `PANEL_ID_DIVERGENCE` | OPEN_ANOMALY | `atag,videolfb` includes `lk_st7701S_V3_480X640_dsi_vdo`; lcmname/DTBO carry `nt35695B_fhd_dsi_cmd_auo_rt5081_drv`. |
| 12 | `GPIO_INIT_DEFAULT_DECODE` | OPEN_OPTIONAL | Raw DTBO `gpio_init_default` table present; semantic decode requires MTK GPIO init table format. |
| 13 | `I2C7_I2C8_I2C9_EMPTY_BUS_CONFIG` | CLOSED_AS_DECLARED / OPEN_AS_FUNCTION | DTBO declares configured childless buses; do not promote endpoints without endpoint evidence. |
| 14 | `ZERO_SIZE_REG_PROPERTIES` | OPEN | `apcldmain@1021b000` and `apcldmaout@1021b400` zero-size reg fields; needs driver binding/source if endpoint section requires. |
| 15 | `MSDC2_MSDC3_NAME_REG_MISMATCH` | OPEN | `msdc@11260000` path/symbol vs `reg=<0x11250000>` mismatch; static-disabled; endpoint owner can resolve if needed. |
| 16 | `MT6370_DC_WDT_REPEATED_VALUE` | OPEN | `dc_wdt=4,000,000` shares raw value with regulator voltage bounds; semantic cause unresolved. |
| 17 | `DTB_ADDR_TAGS_ADDR_COLLISION` | OPEN | boot.img v2 `dtb_addr == tags_addr == 0x47880000`; MTK convention vs anomaly unresolved. |
| 18 | `BOOTIMG_ID_SHA1_REPRODUCTION` | OPEN | boot.img id field SHA1-shaped but not independently reproduced. |
| 19 | `KERNEL_WARN_SOURCE` | OPEN_OPTIONAL | `tainted=4609` includes W bit; `dmesg` readable and contains active kernel log anchors, but exact warning source not isolated. |
| 20 | `MODULE_ELF_ABI_EXTRACTION` | OPEN_OPTIONAL | String modinfo surface absent by targeted grep; ELF-level readelf/modinfo extraction only needed if build tooling requires vermagic/section metadata. |

Closed boundaries to remove from active-open table:

```text
DTBO_BASE_APPLICATION_VALIDATION
BASE_PHANDLE_DEPENDENCY
DTC_GPIO_FIXUP_WARNING
DTC_INTERRUPT_PHANDLE_WARNINGS
VIDEOLFB_FB_BASE_L_RENDERING
FIRMWARE_ANDROID_COMPATIBLE_EMPTY
RAW_BOOT_MEMORY_ZMC_CONTRADICTION
MODULE_VERMAGIC_ABI as a required blocker
```


################################################################################
# FINAL — CLOSE STATUS
################################################################################

| Close item | Status |
| --- | --- |
| kernel build identity | CLOSED |
| Android build/kernel timestamp split | CLOSED |
| cmdline / bootargs | CLOSED |
| bootargs per-boot variants | CLOSED |
| boot/LK base DTB equivalence | CLOSED |
| corrected LK evidence ZIP structural audit | CLOSED |
| DTBO application | CLOSED |
| live FDT mutation map | CLOSED |
| root model / compatible split | CLOSED |
| memory map rewrite | CLOSED |
| `/chosen` live payload expansion | CLOSED |
| reserved-memory live expansion | CLOSED |
| DTC warning surface | CLOSED_WITH_WARNINGS_PRESERVED |
| LK string anchor surface | CLOSED_AS_STRING_SURFACE_ONLY |
| kernel config | CLOSED |
| module inventory | CLOSED |
| loaded module state | CLOSED |
| present-not-loaded `met.ko` | CLOSED |
| module modinfo string surface | CLOSED_ABSENT_BY_TARGETED_GREP |
| filesystems | CLOSED |
| crypto API inventory | CLOSED |
| dm / fscrypto / verity surface | CLOSED |
| debugfs / tracefs / pstore | CLOSED |
| sysctl policy tunables | CLOSED |
| SELinux kernel surface | CLOSED_WITH_RESEARCH_STATE_ENFORCE_0 |
| firmware_class path | CLOSED |
| kernel log exposure | CLOSED |
| keyring visibility | CLOSED |


################################################################################
# APPENDIX-A — RAW DTBO ENTRY0 DTS WITNESS  (verbatim decompiled data)
################################################################################

```dts
/dts-v1/;

/ {

	fragment@0 {
		target = <0xffffffff>;

		__overlay__ {
			atag,videolfb-fb_base_h = <0x00>;
			atag,videolfb-fb_base_l = "^`P";
			atag,videolfb-islcmfound = <0x01>;
			atag,videolfb-islcm_inited = <0x00>;
			atag,videolfb-fps = <0x1770>;
			atag,videolfb-vramSize = <0x17bb000>;
			atag,videolfb-lcmname = "nt35695B_fhd_dsi_cmd_auo_rt5081_drv";
		};
	};

	fragment@1 {
		target = <0xffffffff>;

		__overlay__ {

			led@0 {
				compatible = "mediatek,red";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x27>;
				phandle = <0x27>;
			};

			led@1 {
				compatible = "mediatek,green";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x28>;
				phandle = <0x28>;
			};

			led@2 {
				compatible = "mediatek,blue";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x29>;
				phandle = <0x29>;
			};

			led@3 {
				compatible = "mediatek,jogball-backlight";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x2a>;
				phandle = <0x2a>;
			};

			led@4 {
				compatible = "mediatek,keyboard-backlight";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x2b>;
				phandle = <0x2b>;
			};

			led@5 {
				compatible = "mediatek,button-backlight";
				led_mode = <0x00>;
				data = <0x01>;
				pwm_config = <0x00 0x00 0x00 0x00 0x00>;
				linux,phandle = <0x2c>;
				phandle = <0x2c>;
			};

			led@6 {
				compatible = "mediatek,lcd-backlight";
				led_mode = <0x05>;
				data = <0x01>;
				pwm_config = <0x00 0x03 0x00 0x00 0x00>;
				linux,phandle = <0x2d>;
				phandle = <0x2d>;
			};

			vibrator@0 {
				compatible = "mediatek,vibrator";
				vib_timer = <0x19>;
				vib_limit = <0x09>;
				vib_vol = <0x09>;
				linux,phandle = <0x2e>;
				phandle = <0x2e>;
			};
		};
	};

	fragment@2 {
		target = <0xffffffff>;

		__overlay__ {
			tpd-resolution = <0x1e0 0x280>;
			use-tpd-button = <0x00>;
			tpd-key-num = <0x03>;
			tpd-key-local = <0x8b 0xac 0x9e 0x00>;
			tpd-key-dim-local = <0x5a 0x373 0x64 0x28 0xe6 0x373 0x64 0x28 0x172 0x373 0x64 0x28 0x00 0x00 0x00 0x00>;
			tpd-max-touch-num = <0x05>;
			tpd-filter-enable = <0x01>;
			tpd-filter-pixel-density = <0x92>;
			tpd-filter-custom-prameters = <0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00>;
			tpd-filter-custom-speed = <0x00 0x00 0x00>;
			pinctrl-names = "default\0state_eint_as_int\0state_eint_output0\0state_eint_output1\0state_rst_output0\0state_rst_output1";
			pinctrl-0 = <0x01>;
			pinctrl-1 = <0x02>;
			pinctrl-2 = <0x03>;
			pinctrl-3 = <0x04>;
			pinctrl-4 = <0x05>;
			pinctrl-5 = <0x06>;
			pos-swap = <0x00>;
			posx-reverse = <0x00>;
			posy-reverse = <0x00>;
			status = "okay";
		};
	};

	fragment@3 {
		target = <0xffffffff>;

		__overlay__ {

			eint0default {
				linux,phandle = <0x01>;
				phandle = <0x01>;
			};

			eint@0 {
				linux,phandle = <0x02>;
				phandle = <0x02>;

				pins_cmd_dat {
					pinmux = <0x200>;
					slew-rate = <0x00>;
					bias-disable;
				};
			};

			eintoutput0 {
				linux,phandle = <0x03>;
				phandle = <0x03>;

				pins_cmd_dat {
					pinmux = <0x200>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			eintoutput1 {
				linux,phandle = <0x04>;
				phandle = <0x04>;

				pins_cmd_dat {
					pinmux = <0x200>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			rstoutput0 {
				linux,phandle = <0x05>;
				phandle = <0x05>;

				pins_cmd_dat {
					pinmux = <0xae00>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			rstoutput1 {
				linux,phandle = <0x06>;
				phandle = <0x06>;

				pins_cmd_dat {
					pinmux = <0xae00>;
					slew-rate = <0x01>;
					output-high;
				};
			};
		};
	};

	fragment@4 {
		target = <0xffffffff>;

		__overlay__ {

			consys_default {
				linux,phandle = <0x07>;
				phandle = <0x07>;
			};

			gpslna@0 {
				linux,phandle = <0x08>;
				phandle = <0x08>;

				pins_cmd_dat {
					pinmux = <0x5b00>;
					slew-rate = <0x00>;
					bias-disable;
					output-low;
				};
			};

			gpslna@1 {
				linux,phandle = <0x09>;
				phandle = <0x09>;

				pins_cmd_dat {
					pinmux = <0x5b00>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			gpslna@2 {
				linux,phandle = <0x0a>;
				phandle = <0x0a>;

				pins_cmd_dat {
					pinmux = <0x5b00>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			wcnvcn33@0 {
				linux,phandle = <0x0b>;
				phandle = <0x0b>;

				pins_cmd_dat {
					pinmux = <0xb200>;
					slew-rate = <0x00>;
					bias-disable;
					output-low;
				};
			};

			wcnvcn33@1 {
				linux,phandle = <0x0c>;
				phandle = <0x0c>;

				pins_cmd_dat {
					pinmux = <0xb200>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			wcnvcn33@2 {
				linux,phandle = <0x0d>;
				phandle = <0x0d>;

				pins_cmd_dat {
					pinmux = <0xb200>;
					slew-rate = <0x01>;
					output-low;
				};
			};
		};
	};

	fragment@5 {
		target = <0xffffffff>;

		__overlay__ {
			pinctrl-names = "default\0gps_lna_state_init\0gps_lna_state_oh\0gps_lna_state_ol\0wcn_vcn33_state_init\0wcn_vcn33_state_oh\0wcn_vcn33_state_ol";
			pinctrl-0 = <0x07>;
			pinctrl-1 = <0x08>;
			pinctrl-2 = <0x09>;
			pinctrl-3 = <0x0a>;
			pinctrl-4 = <0x0b>;
			pinctrl-5 = <0x0c>;
			pinctrl-6 = <0x0d>;
			status = "okay";
		};
	};

	fragment@6 {
		target = <0xffffffff>;

		__overlay__ {
			pinctrl-names = "aud_clk_mosi_off\0aud_clk_mosi_on\0aud_clk_miso_off\0aud_clk_miso_on\0aud_dat_mosi_off\0aud_dat_mosi_on\0aud_dat_miso_off\0aud_dat_miso_on\0extamp-pullhigh\0extamp-pulllow";
			pinctrl-0 = <0x0e>;
			pinctrl-1 = <0x0f>;
			pinctrl-2 = <0x10>;
			pinctrl-3 = <0x11>;
			pinctrl-4 = <0x12>;
			pinctrl-5 = <0x13>;
			pinctrl-6 = <0x14>;
			pinctrl-7 = <0x15>;
			pinctrl-8 = <0x16>;
			pinctrl-9 = <0x17>;
			status = "okay";
		};
	};

	fragment@7 {
		target = <0xffffffff>;

		__overlay__ {

			aud_clk_mosi_off {
				linux,phandle = <0x0e>;
				phandle = <0x0e>;

				pins_cmd0_dat {
					pinmux = <0x8800>;
				};
			};

			aud_clk_mosi_on {
				linux,phandle = <0x0f>;
				phandle = <0x0f>;

				pins_cmd0_dat {
					pinmux = <0x8801>;
				};
			};

			aud_clk_miso_off {
				linux,phandle = <0x10>;
				phandle = <0x10>;

				pins_cmd0_dat {
					pinmux = <0x8c00>;
				};
			};

			aud_clk_miso_on {
				linux,phandle = <0x11>;
				phandle = <0x11>;

				pins_cmd0_dat {
					pinmux = <0x8c01>;
				};
			};

			aud_dat_mosi_off {
				linux,phandle = <0x12>;
				phandle = <0x12>;

				pins_cmd1_dat {
					pinmux = <0x8a00>;
					input-enable;
					slew-rate = <0x00>;
					bias-disable;
				};

				pins_cmd2_dat {
					pinmux = <0x8b00>;
					input-enable;
					slew-rate = <0x00>;
					bias-disable;
				};
			};

			aud_dat_mosi_on {
				linux,phandle = <0x13>;
				phandle = <0x13>;

				pins_cmd1_dat {
					pinmux = <0x8a01>;
				};

				pins_cmd2_dat {
					pinmux = <0x8b01>;
				};
			};

			aud_dat_miso_off {
				linux,phandle = <0x14>;
				phandle = <0x14>;

				pins_cmd1_dat {
					pinmux = <0x8e00>;
					input-enable;
					slew-rate = <0x00>;
					bias-disable;
				};

				pins_cmd2_dat {
					pinmux = <0x8f00>;
					input-enable;
					slew-rate = <0x00>;
					bias-disable;
				};
			};

			aud_dat_miso_on {
				linux,phandle = <0x15>;
				phandle = <0x15>;

				pins_cmd1_dat {
					pinmux = <0x8e01>;
				};

				pins_cmd2_dat {
					pinmux = <0x8f01>;
				};
			};

			audexamphigh {
				linux,phandle = <0x16>;
				phandle = <0x16>;

				pins_cmd_dat {
					pinmux = <0x9f00>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			audexamplow {
				linux,phandle = <0x17>;
				phandle = <0x17>;

				pins_cmd_dat {
					pinmux = <0x9f00>;
					slew-rate = <0x01>;
					output-low;
				};
			};
		};
	};

	fragment@8 {
		target = <0xffffffff>;

		__overlay__ {
			mt6370,intr_gpio_num = <0x0b>;
			mt6370,intr_gpio = <0xffffffff 0x0b 0x00>;

			charger {
				mt6370,chg_en_gpio = <0xffffffff 0x9a 0x00>;
				ieoc = <0xc350>;
			};
		};
	};

	fragment@9 {
		target = <0xffffffff>;

		__overlay__ {

			ext_buck_lp4@57 {
				regulator-name = "ext_buck_vdd2";
				regulator-min-microvolt = <0x493e0>;
				regulator-max-microvolt = <0x13d620>;
				vsel_pin = <0x00>;
			};

			ext_buck_lp4x@50 {
				regulator-name = "ext_buck_vddq";
				regulator-min-microvolt = <0x493e0>;
				regulator-max-microvolt = <0x13d620>;
				vsel_pin = <0x01>;
			};
		};
	};

	fragment@10 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;

			wiite_cp@50 {
				compatible = "wiite,cp";
				reg = <0x50>;
				irq-gpio = <0xffffffff 0x08 0x00>;
				irq-gpio_1 = <0xffffffff 0x06 0x00>;
				status = "okay";
			};
		};
	};

	fragment@11 {
		target = <0xffffffff>;

		__overlay__ {
			wiite,con1 = <0xffffffff 0x0a 0x00>;
			wiite,con2 = <0xffffffff 0x04 0x00>;
			pinctrl-names = "pwr_i2c_uart_pin_af\0pwr_i2c_uart_pin_gpio";
			pinctrl-0 = <0x18>;
			pinctrl-1 = <0x19>;
		};
	};

	fragment@12 {
		target = <0xffffffff>;

		__overlay__ {

			pwr_i2c_uart_pin_af {
				linux,phandle = <0x18>;
				phandle = <0x18>;

				pins_scl_dat {
					pinmux = <0x6901>;
					input-enable;
					slew-rate = <0x00>;
					bias-pull-up;
				};

				pins_sda_dat {
					pinmux = <0x6a01>;
					input-enable;
					slew-rate = <0x00>;
					bias-pull-up;
				};

				pins_utx_dat {
					pinmux = <0x01>;
					input-enable;
					slew-rate = <0x00>;
					bias-disable;
				};

				pins_urx_dat {
					pinmux = <0x101>;
					input-enable;
					slew-rate = <0x00>;
					bias-pull-up;
				};
			};

			pwr_i2c_uart_pin_gpio {
				linux,phandle = <0x19>;
				phandle = <0x19>;

				pins_scl_dat {
					pinmux = <0x6900>;
					output-low;
					bias-disable;
				};

				pins_sda_dat {
					pinmux = <0x6a00>;
					output-low;
					bias-disable;
				};

				pins_utx_dat {
					pinmux = <0x00>;
					output-low;
					bias-disable;
				};

				pins_urx_dat {
					pinmux = <0x100>;
					output-low;
					bias-disable;
				};
			};
		};
	};

	fragment@13 {
		target = <0xffffffff>;

		__overlay__ {
			vhrs-supply = <0xffffffff>;
			status = "okay";
		};
	};

	fragment@14 {
		target = <0xffffffff>;

		__overlay__ {

			adc_channel@ {
				compatible = "mediatek,adc_channel";
				mediatek,temperature0 = <0x00>;
				mediatek,temperature1 = <0x01>;
				mediatek,adc_fdd_rf_params_dynamic_custom_ch = <0x02>;
				status = "okay";
			};
		};
	};

	fragment@15 {
		target = <0xffffffff>;

		__overlay__ {
			mediatek,clkbuf-quantity = <0x07>;
			mediatek,clkbuf-config = <0x02 0x01 0x01 0x02 0x00 0x00 0x00>;
			mediatek,clkbuf-driving-current = <0x01 0x01 0x01 0x01 0x01 0x01 0x01>;
			status = "okay";
		};
	};

	fragment@16 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;

			cap_touch@5d {
				compatible = "mediatek,cap_touch";
				reg = <0x5d>;
				status = "okay";
				linux,phandle = <0x2f>;
				phandle = <0x2f>;
			};
		};
	};

	fragment@17 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;
		};
	};

	fragment@18 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;

			camera_sub@2d {
				compatible = "mediatek,camera_sub";
				reg = <0x2d>;
				status = "okay";
				linux,phandle = <0x30>;
				phandle = <0x30>;
			};

			camera_sub_eeprom@52 {
				compatible = "mediatek,camera_sub_eeprom";
				reg = <0x52>;
				status = "okay";
				linux,phandle = <0x31>;
				phandle = <0x31>;
			};

			camera_main@1a {
				compatible = "mediatek,camera_main";
				reg = <0x1a>;
				status = "okay";
				linux,phandle = <0x32>;
				phandle = <0x32>;
			};

			camera_main_eeprom@50 {
				compatible = "mediatek,camera_main_eeprom";
				reg = <0x50>;
				status = "okay";
				linux,phandle = <0x33>;
				phandle = <0x33>;
			};

			camera_main_af@0c {
				compatible = "mediatek,camera_main_af";
				reg = <0x0c>;
				status = "okay";
				linux,phandle = <0x34>;
				phandle = <0x34>;
			};

			ccu_sensor_i2c_2_hw@10 {
				compatible = "mediatek,ccu_sensor_i2c_2_hw";
				reg = <0x10>;
				status = "okay";
				linux,phandle = <0x35>;
				phandle = <0x35>;
			};
		};
	};

	fragment@19 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;
		};
	};

	fragment@20 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;

			heartrate@33 {
				compatible = "mediatek,heartrate";
				reg = <0x33>;
				status = "okay";
				linux,phandle = <0x36>;
				phandle = <0x36>;
			};

			ccu_sensor_i2c_4_hw@11 {
				compatible = "mediatek,ccu_sensor_i2c_4_hw";
				reg = <0x11>;
				status = "okay";
				linux,phandle = <0x37>;
				phandle = <0x37>;
			};
		};
	};

	fragment@21 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x33e140>;
			mediatek,use-push-pull;

			subpmic_pmu@34 {
				compatible = "mediatek,subpmic_pmu";
				reg = <0x34>;
				status = "okay";
				linux,phandle = <0x38>;
				phandle = <0x38>;
			};
		};
	};

	fragment@22 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;

			camera_main_two@2d {
				compatible = "mediatek,camera_main_two";
				reg = <0x2d>;
				status = "okay";
				linux,phandle = <0x39>;
				phandle = <0x39>;
			};

			camera_main_two_eeprom@50 {
				compatible = "mediatek,camera_main_two_eeprom";
				reg = <0x50>;
				status = "okay";
				linux,phandle = <0x3a>;
				phandle = <0x3a>;
			};

			camera_main_two_af@0c {
				compatible = "mediatek,camera_main_two_af";
				reg = <0x0c>;
				status = "okay";
				linux,phandle = <0x3b>;
				phandle = <0x3b>;
			};
		};
	};

	fragment@23 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;
		};
	};

	fragment@24 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;
		};
	};

	fragment@25 {
		target = <0xffffffff>;

		__overlay__ {
			#address-cells = <0x01>;
			#size-cells = <0x00>;
			clock-frequency = <0x61a80>;
			mediatek,use-open-drain;
		};
	};

	fragment@26 {
		target = <0xffffffff>;

		__overlay__ {
			GPIO_SIM1_SCLK = <0xffffffff 0x26 0x00>;
			GPIO_SIM1_SRST = <0xffffffff 0x27 0x00>;
			GPIO_SIM1_SIO = <0xffffffff 0x28 0x00>;
			GPIO_SIM2_HOT_PLUG = <0xffffffff 0x2e 0x00>;
		};
	};

	fragment@27 {
		target = <0xffffffff>;

		__overlay__ {
			gpio_init_default = <0x00 0x01 0x01 0x00 0x00 0x00 0x01 0x01 0x01 0x00 0x00 0x01 0x01 0x01 0x02 0x00 0x00 0x00 0x01 0x01 0x01 0x03 0x00 0x00 0x00 0x01 0x00 0x01 0x04 0x00 0x01 0x00 0x00 0x00 0x00 0x05 0x00 0x00 0x00 0x01 0x00 0x00 0x06 0x00 0x00 0x00 0x01 0x01 0x00 0x07 0x00 0x01 0x00 0x00 0x00 0x00 0x08 0x00 0x00 0x00 0x01 0x01 0x00 0x09 0x00 0x00 0x00 0x01 0x00 0x00 0x0a 0x00 0x01 0x00 0x00 0x00 0x00 0x0b 0x00 0x00 0x00 0x01 0x01 0x00 0x0c 0x00 0x00 0x00 0x01 0x00 0x00 0x0d 0x00 0x00 0x00 0x01 0x00 0x00 0x0e 0x00 0x00 0x00 0x01 0x00 0x00 0x0f 0x00 0x00 0x00 0x01 0x00 0x00 0x10 0x00 0x00 0x00 0x01 0x00 0x00 0x11 0x00 0x00 0x00 0x01 0x00 0x00 0x12 0x00 0x00 0x00 0x01 0x00 0x00 0x13 0x00 0x00 0x00 0x01 0x00 0x00 0x14 0x00 0x00 0x00 0x01 0x00 0x00 0x15 0x00 0x00 0x00 0x01 0x00 0x00 0x16 0x00 0x00 0x00 0x01 0x00 0x00 0x17 0x00 0x00 0x00 0x01 0x00 0x00 0x18 0x00 0x00 0x00 0x01 0x00 0x00 0x19 0x00 0x00 0x00 0x01 0x00 0x00 0x1a 0x00 0x00 0x00 0x01 0x00 0x00 0x1b 0x00 0x00 0x00 0x01 0x00 0x00 0x1c 0x00 0x00 0x00 0x01 0x00 0x00 0x1d 0x00 0x00 0x00 0x01 0x00 0x01 0x1e 0x00 0x00 0x00 0x01 0x00 0x01 0x1f 0x00 0x00 0x00 0x01 0x00 0x01 0x20 0x00 0x00 0x00 0x01 0x00 0x01 0x21 0x00 0x00 0x00 0x01 0x00 0x01 0x22 0x00 0x00 0x00 0x01 0x00 0x01 0x23 0x00 0x00 0x00 0x01 0x00 0x00 0x24 0x00 0x00 0x00 0x01 0x00 0x00 0x25 0x00 0x00 0x00 0x01 0x00 0x00 0x26 0x01 0x01 0x00 0x00 0x00 0x00 0x27 0x01 0x01 0x00 0x00 0x00 0x00 0x28 0x01 0x00 0x00 0x01 0x01 0x00 0x29 0x00 0x00 0x00 0x01 0x00 0x00 0x2a 0x04 0x01 0x00 0x00 0x00 0x00 0x2b 0x01 0x01 0x00 0x00 0x00 0x00 0x2c 0x01 0x00 0x00 0x01 0x00 0x00 0x2d 0x01 0x01 0x01 0x00 0x00 0x00 0x2e 0x01 0x00 0x00 0x01 0x01 0x00 0x2f 0x00 0x00 0x00 0x01 0x00 0x00 0x30 0x01 0x00 0x00 0x01 0x01 0x01 0x31 0x01 0x00 0x00 0x01 0x01 0x01 0x32 0x01 0x00 0x00 0x01 0x01 0x01 0x33 0x01 0x00 0x00 0x01 0x01 0x01 0x34 0x00 0x00 0x00 0x01 0x00 0x00 0x35 0x00 0x00 0x00 0x01 0x00 0x00 0x36 0x00 0x00 0x00 0x01 0x00 0x00 0x37 0x00 0x00 0x00 0x01 0x00 0x00 0x38 0x00 0x00 0x00 0x01 0x00 0x00 0x39 0x00 0x00 0x00 0x01 0x00 0x00 0x3a 0x00 0x00 0x00 0x01 0x00 0x00 0x3b 0x01 0x00 0x00 0x01 0x00 0x00 0x3c 0x01 0x00 0x00 0x01 0x00 0x00 0x3d 0x01 0x00 0x00 0x01 0x00 0x00 0x3e 0x01 0x00 0x00 0x01 0x00 0x00 0x3f 0x01 0x00 0x00 0x01 0x00 0x00 0x40 0x01 0x00 0x00 0x01 0x00 0x00 0x41 0x00 0x00 0x00 0x01 0x00 0x00 0x42 0x00 0x00 0x00 0x01 0x00 0x00 0x43 0x00 0x00 0x00 0x01 0x00 0x00 0x44 0x00 0x00 0x00 0x01 0x00 0x00 0x45 0x00 0x00 0x00 0x01 0x00 0x00 0x46 0x00 0x00 0x00 0x01 0x00 0x00 0x47 0x00 0x00 0x00 0x01 0x00 0x00 0x48 0x00 0x00 0x00 0x01 0x00 0x00 0x49 0x00 0x00 0x00 0x01 0x00 0x00 0x4a 0x01 0x01 0x00 0x01 0x00 0x00 0x4b 0x01 0x01 0x00 0x01 0x00 0x00 0x4c 0x01 0x01 0x00 0x01 0x00 0x00 0x4d 0x00 0x00 0x00 0x01 0x00 0x00 0x4e 0x00 0x00 0x00 0x01 0x00 0x00 0x4f 0x00 0x00 0x00 0x01 0x00 0x00 0x50 0x00 0x00 0x00 0x01 0x00 0x00 0x51 0x01 0x00 0x00 0x01 0x01 0x01 0x52 0x01 0x00 0x00 0x01 0x01 0x01 0x53 0x01 0x00 0x00 0x01 0x01 0x01 0x54 0x01 0x00 0x00 0x01 0x01 0x01 0x55 0x01 0x01 0x00 0x01 0x00 0x00 0x56 0x01 0x01 0x00 0x01 0x00 0x00 0x57 0x00 0x00 0x00 0x01 0x00 0x00 0x58 0x00 0x00 0x00 0x01 0x00 0x00 0x59 0x00 0x00 0x00 0x01 0x00 0x00 0x5a 0x00 0x00 0x00 0x01 0x00 0x00 0x5b 0x00 0x01 0x00 0x01 0x00 0x00 0x5c 0x01 0x01 0x00 0x00 0x00 0x00 0x5d 0x01 0x00 0x00 0x01 0x01 0x00 0x5e 0x01 0x00 0x00 0x01 0x01 0x00 0x5f 0x01 0x00 0x00 0x01 0x01 0x00 0x60 0x01 0x01 0x00 0x00 0x00 0x00 0x61 0x00 0x01 0x00 0x00 0x00 0x00 0x62 0x00 0x01 0x00 0x00 0x00 0x00 0x63 0x01 0x01 0x00 0x00 0x00 0x00 0x64 0x00 0x00 0x00 0x01 0x00 0x00 0x65 0x00 0x01 0x01 0x00 0x00 0x00 0x66 0x00 0x01 0x01 0x00 0x00 0x00 0x67 0x01 0x00 0x00 0x01 0x01 0x01 0x68 0x01 0x00 0x00 0x01 0x01 0x01 0x69 0x01 0x00 0x00 0x01 0x01 0x01 0x6a 0x01 0x00 0x00 0x01 0x01 0x01 0x6b 0x00 0x00 0x00 0x01 0x00 0x00 0x6c 0x00 0x00 0x00 0x01 0x00 0x00 0x6d 0x00 0x00 0x00 0x01 0x00 0x00 0x6e 0x00 0x00 0x00 0x01 0x00 0x00 0x6f 0x00 0x00 0x00 0x01 0x00 0x00 0x70 0x00 0x00 0x00 0x01 0x00 0x00 0x71 0x00 0x00 0x00 0x01 0x00 0x00 0x72 0x00 0x00 0x00 0x01 0x00 0x00 0x73 0x00 0x00 0x00 0x01 0x00 0x00 0x74 0x00 0x00 0x00 0x01 0x00 0x00 0x75 0x00 0x00 0x00 0x01 0x00 0x00 0x76 0x00 0x00 0x00 0x01 0x00 0x00 0x77 0x00 0x00 0x00 0x01 0x00 0x00 0x78 0x00 0x00 0x00 0x01 0x00 0x00 0x79 0x00 0x00 0x00 0x01 0x00 0x00 0x7a 0x01 0x00 0x00 0x01 0x01 0x01 0x7b 0x01 0x00 0x00 0x01 0x01 0x01 0x7c 0x01 0x01 0x00 0x00 0x00 0x01 0x7d 0x01 0x00 0x00 0x01 0x01 0x01 0x7e 0x01 0x00 0x00 0x01 0x01 0x01 0x7f 0x01 0x00 0x00 0x01 0x01 0x01 0x80 0x01 0x00 0x00 0x01 0x01 0x01 0x81 0x01 0x00 0x00 0x01 0x01 0x01 0x82 0x01 0x00 0x00 0x01 0x01 0x01 0x83 0x01 0x00 0x00 0x01 0x00 0x01 0x84 0x01 0x00 0x00 0x01 0x01 0x01 0x85 0x01 0x01 0x01 0x00 0x00 0x01 0x86 0x01 0x00 0x00 0x01 0x00 0x01 0x87 0x01 0x01 0x00 0x00 0x00 0x00 0x88 0x01 0x01 0x00 0x00 0x00 0x00 0x89 0x01 0x01 0x00 0x00 0x00 0x00 0x8a 0x01 0x01 0x00 0x00 0x00 0x00 0x8b 0x01 0x01 0x00 0x00 0x00 0x00 0x8c 0x01 0x00 0x00 0x01 0x00 0x00 0x8d 0x01 0x00 0x00 0x01 0x00 0x00 0x8e 0x01 0x00 0x00 0x01 0x00 0x00 0x8f 0x01 0x00 0x00 0x01 0x00 0x00 0x90 0x01 0x00 0x00 0x01 0x00 0x00 0x91 0x01 0x01 0x01 0x00 0x00 0x00 0x92 0x01 0x00 0x00 0x01 0x00 0x00 0x93 0x01 0x01 0x00 0x00 0x00 0x00 0x94 0x01 0x01 0x00 0x00 0x00 0x00 0x95 0x01 0x01 0x00 0x00 0x00 0x00 0x96 0x00 0x00 0x00 0x01 0x00 0x00 0x97 0x00 0x00 0x00 0x01 0x00 0x00 0x98 0x00 0x00 0x00 0x01 0x00 0x00 0x99 0x00 0x00 0x00 0x01 0x00 0x00 0x9a 0x00 0x01 0x00 0x00 0x00 0x00 0x9b 0x00 0x00 0x00 0x01 0x00 0x00 0x9c 0x00 0x00 0x00 0x01 0x00 0x00 0x9d 0x00 0x00 0x00 0x01 0x00 0x00 0x9e 0x00 0x00 0x00 0x01 0x00 0x00 0x9f 0x00 0x01 0x00 0x00 0x00 0x00 0xa0 0x00 0x00 0x00 0x01 0x00 0x00 0xa1 0x00 0x00 0x00 0x01 0x00 0x01 0xa2 0x00 0x00 0x00 0x01 0x00 0x01 0xa3 0x00 0x00 0x00 0x01 0x00 0x00 0xa4 0x00 0x00 0x00 0x01 0x00 0x00 0xa5 0x00 0x00 0x00 0x01 0x00 0x00 0xa6 0x00 0x00 0x00 0x01 0x00 0x00 0xa7 0x00 0x00 0x00 0x01 0x00 0x00 0xa8 0x00 0x00 0x00 0x01 0x00 0x00 0xa9 0x00 0x00 0x00 0x01 0x00 0x00 0xaa 0x00 0x00 0x00 0x01 0x00 0x00 0xab 0x00 0x00 0x00 0x01 0x00 0x00 0xac 0x00 0x00 0x00 0x01 0x00 0x00 0xad 0x00 0x00 0x00 0x01 0x00 0x00 0xae 0x00 0x01 0x01 0x00 0x00 0x00 0xaf 0x00 0x00 0x00 0x01 0x00 0x00 0xb0 0x00 0x00 0x00 0x01 0x00 0x00 0xb1 0x00 0x00 0x00 0x01 0x00 0x00 0xb2 0x00 0x01 0x00 0x00 0x00 0x00 0xb3 0x00 0x00 0x00 0x01 0x00 0x00>;
		};
	};

	fragment@28 {
		target = <0xffffffff>;

		__overlay__ {
			interrupt-parent = <0xffffffff>;
			interrupts = <0x02 0x08 0x02 0x00>;
			status = "okay";
		};
	};

	fragment@29 {
		target = <0xffffffff>;

		__overlay__ {
			interrupt-parent = <0xffffffff>;
			interrupts = <0x0b 0x02 0x0b 0x00>;
			status = "okay";
		};
	};

	fragment@30 {
		target = <0xffffffff>;

		__overlay__ {
			interrupt-parent = <0xffffffff>;
			interrupts = <0x2c 0x01 0x2c 0x01>;
			status = "okay";
		};
	};

	fragment@31 {
		target = <0xffffffff>;

		__overlay__ {
			interrupt-parent = <0xffffffff>;
			interrupts = <0x90 0x04 0x90 0x01>;
			status = "okay";
		};
	};

	fragment@32 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vcama";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@33 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vsim1";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@34 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vsim2";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@35 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vcamd";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@36 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vcamio";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@37 {
		target = <0xffffffff>;

		__overlay__ {
			regulator-name = "vldo28";
			regulator-default-on = <0x01>;
			status = "okay";
		};
	};

	fragment@38 {
		target = <0xffffffff>;

		__overlay__ {
			vcama-supply = <0xffffffff>;
			vcamd_main2-supply = <0xffffffff>;
			vcamio-supply = <0xffffffff>;
			vcamio_sub-supply = <0xffffffff>;
			vcamio_main2-supply = <0xffffffff>;
			vcamaf-supply = <0xffffffff>;
			vcamaf_sub-supply = <0xffffffff>;
			vcamaf_main2-supply = <0xffffffff>;
			status = "okay";
		};
	};

	fragment@39 {
		target = <0xffffffff>;

		__overlay__ {
			vtouch-supply = <0xffffffff>;
			status = "okay";
		};
	};

	fragment@40 {
		target = <0xffffffff>;

		__overlay__ {
			mediatek,kpd-key-debounce = <0x400>;
			mediatek,kpd-sw-pwrkey = <0x74>;
			mediatek,kpd-hw-pwrkey = <0x08>;
			mediatek,kpd-sw-rstkey = <0x73>;
			mediatek,kpd-hw-rstkey = <0x11>;
			mediatek,kpd-use-extend-type = <0x00>;
			mediatek,kpd-hw-map-num = <0x48>;
			mediatek,kpd-hw-init-map = <0x9e 0xd4 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x1e 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00 0x00>;
			mediatek,kpd-pwrkey-eint-gpio = <0x00>;
			mediatek,kpd-pwkey-gpio-din = <0x00>;
			mediatek,kpd-hw-dl-key0 = <0x11>;
			mediatek,kpd-hw-dl-key2 = <0x08>;
			mediatek,kpd-hw-recovery-key = <0x11>;
			status = "okay";
		};
	};

	fragment@41 {
		target = <0xffffffff>;

		__overlay__ {

			camera_pins_cam0_mclk_on {
				linux,phandle = <0x23>;
				phandle = <0x23>;

				pins_cmd_dat {
					pinmux = <0x6401>;
				};
			};

			camera_pins_cam0_mclk_off {
				linux,phandle = <0x24>;
				phandle = <0x24>;

				pins_cmd_dat {
					pinmux = <0x6400>;
				};
			};

			camera_pins_cam1_mclk_on {
				linux,phandle = <0x25>;
				phandle = <0x25>;

				pins_cmd_dat {
					pinmux = <0x6301>;
				};
			};

			camera_pins_cam1_mclk_off {
				linux,phandle = <0x26>;
				phandle = <0x26>;

				pins_cmd_dat {
					pinmux = <0x6300>;
				};
			};

			cam0@0 {
				linux,phandle = <0x1b>;
				phandle = <0x1b>;

				pins_cmd_dat {
					pinmux = <0x6600>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			cam0@1 {
				linux,phandle = <0x1c>;
				phandle = <0x1c>;

				pins_cmd_dat {
					pinmux = <0x6600>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			cam0@2 {
				linux,phandle = <0x1d>;
				phandle = <0x1d>;

				pins_cmd_dat {
					pinmux = <0x6200>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			cam0@3 {
				linux,phandle = <0x1e>;
				phandle = <0x1e>;

				pins_cmd_dat {
					pinmux = <0x6200>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			cam1@0 {
				linux,phandle = <0x1f>;
				phandle = <0x1f>;

				pins_cmd_dat {
					pinmux = <0x6500>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			cam1@1 {
				linux,phandle = <0x20>;
				phandle = <0x20>;

				pins_cmd_dat {
					pinmux = <0x6500>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			cam1@2 {
				linux,phandle = <0x21>;
				phandle = <0x21>;

				pins_cmd_dat {
					pinmux = <0x6100>;
					slew-rate = <0x01>;
					output-low;
				};
			};

			cam1@3 {
				linux,phandle = <0x22>;
				phandle = <0x22>;

				pins_cmd_dat {
					pinmux = <0x6100>;
					slew-rate = <0x01>;
					output-high;
				};
			};

			camdefault {
				linux,phandle = <0x1a>;
				phandle = <0x1a>;
			};
		};
	};

	fragment@42 {
		target = <0xffffffff>;

		__overlay__ {
			pinctrl-names = "default\0cam0_rst0\0cam0_rst1\0cam0_pnd0\0cam0_pnd1\0cam1_rst0\0cam1_rst1\0cam1_pnd0\0cam1_pnd1\0cam0_mclk_on\0cam0_mclk_off\0cam1_mclk_on\0cam1_mclk_off";
			pinctrl-0 = <0x1a>;
			pinctrl-1 = <0x1b>;
			pinctrl-2 = <0x1c>;
			pinctrl-3 = <0x1d>;
			pinctrl-4 = <0x1e>;
			pinctrl-5 = <0x1f>;
			pinctrl-6 = <0x20>;
			pinctrl-7 = <0x21>;
			pinctrl-8 = <0x22>;
			pinctrl-9 = <0x23>;
			pinctrl-10 = <0x24>;
			pinctrl-11 = <0x25>;
			pinctrl-12 = <0x26>;
			cam0_vcama-supply = <0xffffffff>;
			cam0_vcamd-supply = <0xffffffff>;
			cam0_vcamio-supply = <0xffffffff>;
			cam1_vcama-supply = <0xffffffff>;
			cam1_vcamd-supply = <0xffffffff>;
			cam1_vcamio-supply = <0xffffffff>;
			status = "okay";
		};
	};

	__symbols__ {
		led0 = "/fragment@1/__overlay__/led@0";
		led1 = "/fragment@1/__overlay__/led@1";
		led2 = "/fragment@1/__overlay__/led@2";
		led3 = "/fragment@1/__overlay__/led@3";
		led4 = "/fragment@1/__overlay__/led@4";
		led5 = "/fragment@1/__overlay__/led@5";
		led6 = "/fragment@1/__overlay__/led@6";
		vibrator0 = "/fragment@1/__overlay__/vibrator@0";
		ctp_pins_default = "/fragment@3/__overlay__/eint0default";
		ctp_pins_eint_as_int = "/fragment@3/__overlay__/eint@0";
		ctp_pins_eint_output0 = "/fragment@3/__overlay__/eintoutput0";
		ctp_pins_eint_output1 = "/fragment@3/__overlay__/eintoutput1";
		ctp_pins_rst_output0 = "/fragment@3/__overlay__/rstoutput0";
		ctp_pins_rst_output1 = "/fragment@3/__overlay__/rstoutput1";
		consys_pins_default = "/fragment@4/__overlay__/consys_default";
		gpslna_pins_init = "/fragment@4/__overlay__/gpslna@0";
		gpslna_pins_oh = "/fragment@4/__overlay__/gpslna@1";
		gpslna_pins_ol = "/fragment@4/__overlay__/gpslna@2";
		wcn_vcn33_pins_init = "/fragment@4/__overlay__/wcnvcn33@0";
		wcn_vcn33_pins_oh = "/fragment@4/__overlay__/wcnvcn33@1";
		wcn_vcn33_pins_ol = "/fragment@4/__overlay__/wcnvcn33@2";
		aud_clk_mosi_off = "/fragment@7/__overlay__/aud_clk_mosi_off";
		aud_clk_mosi_on = "/fragment@7/__overlay__/aud_clk_mosi_on";
		aud_clk_miso_off = "/fragment@7/__overlay__/aud_clk_miso_off";
		aud_clk_miso_on = "/fragment@7/__overlay__/aud_clk_miso_on";
		aud_dat_mosi_off = "/fragment@7/__overlay__/aud_dat_mosi_off";
		aud_dat_mosi_on = "/fragment@7/__overlay__/aud_dat_mosi_on";
		aud_dat_miso_off = "/fragment@7/__overlay__/aud_dat_miso_off";
		aud_dat_miso_on = "/fragment@7/__overlay__/aud_dat_miso_on";
		aud_pins_extamp_high = "/fragment@7/__overlay__/audexamphigh";
		aud_pins_extamp_low = "/fragment@7/__overlay__/audexamplow";
		pwr_i2c_uart_pin_af = "/fragment@12/__overlay__/pwr_i2c_uart_pin_af";
		pwr_i2c_uart_pin_gpio = "/fragment@12/__overlay__/pwr_i2c_uart_pin_gpio";
		cap_touch_mtk = "/fragment@16/__overlay__/cap_touch@5d";
		camera_sub_mtk = "/fragment@18/__overlay__/camera_sub@2d";
		camera_sub_eeprom_mtk = "/fragment@18/__overlay__/camera_sub_eeprom@52";
		camera_main_mtk = "/fragment@18/__overlay__/camera_main@1a";
		camera_main_eeprom_mtk = "/fragment@18/__overlay__/camera_main_eeprom@50";
		camera_main_af_mtk = "/fragment@18/__overlay__/camera_main_af@0c";
		ccu_sensor_i2c_2_hw_mtk = "/fragment@18/__overlay__/ccu_sensor_i2c_2_hw@10";
		heartrate_mtk = "/fragment@20/__overlay__/heartrate@33";
		ccu_sensor_i2c_4_hw_mtk = "/fragment@20/__overlay__/ccu_sensor_i2c_4_hw@11";
		subpmic_pmu_mtk = "/fragment@21/__overlay__/subpmic_pmu@34";
		camera_main_two_mtk = "/fragment@22/__overlay__/camera_main_two@2d";
		camera_main_two_eeprom_mtk = "/fragment@22/__overlay__/camera_main_two_eeprom@50";
		camera_main_two_af_mtk = "/fragment@22/__overlay__/camera_main_two_af@0c";
		camera_pins_cam0_mclk_on = "/fragment@41/__overlay__/camera_pins_cam0_mclk_on";
		camera_pins_cam0_mclk_off = "/fragment@41/__overlay__/camera_pins_cam0_mclk_off";
		camera_pins_cam1_mclk_on = "/fragment@41/__overlay__/camera_pins_cam1_mclk_on";
		camera_pins_cam1_mclk_off = "/fragment@41/__overlay__/camera_pins_cam1_mclk_off";
		camera_pins_cam0_rst0 = "/fragment@41/__overlay__/cam0@0";
		camera_pins_cam0_rst1 = "/fragment@41/__overlay__/cam0@1";
		camera_pins_cam0_pnd0 = "/fragment@41/__overlay__/cam0@2";
		camera_pins_cam0_pnd1 = "/fragment@41/__overlay__/cam0@3";
		camera_pins_cam1_rst0 = "/fragment@41/__overlay__/cam1@0";
		camera_pins_cam1_rst1 = "/fragment@41/__overlay__/cam1@1";
		camera_pins_cam1_pnd0 = "/fragment@41/__overlay__/cam1@2";
		camera_pins_cam1_pnd1 = "/fragment@41/__overlay__/cam1@3";
		camera_pins_default = "/fragment@41/__overlay__/camdefault";
	};

	__fixups__ {
		chosen = "/fragment@0:target:0";
		odm = "/fragment@1:target:0";
		touch = "/fragment@2:target:0\0/fragment@28:target:0\0/fragment@39:target:0";
		pio = "/fragment@3:target:0\0/fragment@4:target:0\0/fragment@7:target:0\0/fragment@8/__overlay__:mt6370,intr_gpio:0\0/fragment@8/__overlay__/charger:mt6370,chg_en_gpio:0\0/fragment@10/__overlay__/wiite_cp@50:irq-gpio:0\0/fragment@10/__overlay__/wiite_cp@50:irq-gpio_1:0\0/fragment@11/__overlay__:wiite,con1:0\0/fragment@11/__overlay__:wiite,con2:0\0/fragment@12:target:0\0/fragment@26/__overlay__:GPIO_SIM1_SCLK:0\0/fragment@26/__overlay__:GPIO_SIM1_SRST:0\0/fragment@26/__overlay__:GPIO_SIM1_SIO:0\0/fragment@26/__overlay__:GPIO_SIM2_HOT_PLUG:0\0/fragment@28/__overlay__:interrupt-parent:0\0/fragment@29/__overlay__:interrupt-parent:0\0/fragment@30/__overlay__:interrupt-parent:0\0/fragment@31/__overlay__:interrupt-parent:0\0/fragment@41:target:0";
		consys = "/fragment@5:target:0";
		audgpio = "/fragment@6:target:0";
		mt6370_pmu = "/fragment@8:target:0";
		i2c3 = "/fragment@9:target:0\0/fragment@19:target:0";
		i2c4 = "/fragment@10:target:0\0/fragment@20:target:0";
		wiite_con = "/fragment@11:target:0";
		wiite_corp = "/fragment@13:target:0";
		mt_pmic_vmch_ldo_reg = "/fragment@13/__overlay__:vhrs-supply:0";
		auxadc = "/fragment@14:target:0";
		pmic_clock_buffer_ctrl = "/fragment@15:target:0";
		i2c0 = "/fragment@16:target:0";
		i2c1 = "/fragment@17:target:0";
		i2c2 = "/fragment@18:target:0";
		i2c5 = "/fragment@21:target:0";
		i2c6 = "/fragment@22:target:0";
		i2c7 = "/fragment@23:target:0";
		i2c8 = "/fragment@24:target:0";
		i2c9 = "/fragment@25:target:0";
		gpio_usage_mapping = "/fragment@26:target:0";
		gpio = "/fragment@27:target:0";
		subpmic_pmu_eint = "/fragment@29:target:0";
		dsi_te = "/fragment@30:target:0";
		main_pmic = "/fragment@31:target:0";
		mt_pmic_vcama_ldo_reg = "/fragment@32:target:0\0/fragment@38/__overlay__:vcama-supply:0\0/fragment@42/__overlay__:cam0_vcama-supply:0\0/fragment@42/__overlay__:cam1_vcama-supply:0";
		mt_pmic_vsim1_ldo_reg = "/fragment@33:target:0";
		mt_pmic_vsim2_ldo_reg = "/fragment@34:target:0";
		mt_pmic_vcamd_ldo_reg = "/fragment@35:target:0\0/fragment@38/__overlay__:vcamd_main2-supply:0\0/fragment@42/__overlay__:cam0_vcamd-supply:0\0/fragment@42/__overlay__:cam1_vcamd-supply:0";
		mt_pmic_vcamio_ldo_reg = "/fragment@36:target:0\0/fragment@38/__overlay__:vcamio-supply:0\0/fragment@38/__overlay__:vcamio_sub-supply:0\0/fragment@38/__overlay__:vcamio_main2-supply:0\0/fragment@42/__overlay__:cam0_vcamio-supply:0\0/fragment@42/__overlay__:cam1_vcamio-supply:0";
		mt_pmic_vldo28_ldo_reg = "/fragment@37:target:0\0/fragment@38/__overlay__:vcamaf-supply:0\0/fragment@38/__overlay__:vcamaf_sub-supply:0\0/fragment@38/__overlay__:vcamaf_main2-supply:0\0/fragment@39/__overlay__:vtouch-supply:0";
		kd_camera_hw1 = "/fragment@38:target:0\0/fragment@42:target:0";
		keypad = "/fragment@40:target:0";
	};

	__local_fixups__ {

		fragment@2 {

			__overlay__ {
				pinctrl-0 = <0x00>;
				pinctrl-1 = <0x00>;
				pinctrl-2 = <0x00>;
				pinctrl-3 = <0x00>;
				pinctrl-4 = <0x00>;
				pinctrl-5 = <0x00>;
			};
		};

		fragment@5 {

			__overlay__ {
				pinctrl-0 = <0x00>;
				pinctrl-1 = <0x00>;
				pinctrl-2 = <0x00>;
				pinctrl-3 = <0x00>;
				pinctrl-4 = <0x00>;
				pinctrl-5 = <0x00>;
				pinctrl-6 = <0x00>;
			};
		};

		fragment@6 {

			__overlay__ {
				pinctrl-0 = <0x00>;
				pinctrl-1 = <0x00>;
				pinctrl-2 = <0x00>;
				pinctrl-3 = <0x00>;
				pinctrl-4 = <0x00>;
				pinctrl-5 = <0x00>;
				pinctrl-6 = <0x00>;
				pinctrl-7 = <0x00>;
				pinctrl-8 = <0x00>;
				pinctrl-9 = <0x00>;
			};
		};

		fragment@11 {

			__overlay__ {
				pinctrl-0 = <0x00>;
				pinctrl-1 = <0x00>;
			};
		};

		fragment@42 {

			__overlay__ {
				pinctrl-0 = <0x00>;
				pinctrl-1 = <0x00>;
				pinctrl-2 = <0x00>;
				pinctrl-3 = <0x00>;
				pinctrl-4 = <0x00>;
				pinctrl-5 = <0x00>;
				pinctrl-6 = <0x00>;
				pinctrl-7 = <0x00>;
				pinctrl-8 = <0x00>;
				pinctrl-9 = <0x00>;
				pinctrl-10 = <0x00>;
				pinctrl-11 = <0x00>;
				pinctrl-12 = <0x00>;
			};
		};
	};
};
```

################################################################################
# 1.8 — CORE SOC REGISTER MAP
################################################################################

Live register-map boundary:
Capture state                   : bootloader unlocked; Magisk root active; AVB/verity disabled/orange; SELinux permissive; Android live runtime
Register declaration authority  : /sys/firmware/devicetree/base
Platform population authority   : /sys/bus/platform/devices and /sys/devices/platform
Driver binding authority        : /sys/bus/platform/drivers
Kernel resource authority       : /proc/iomem
Boot-device anchor authority    : /proc/cmdline
Power-state authority           : /sys/devices/platform/*/power/*
Stock metadata scope            : not included in this live register-map capture
Raw DTB equivalence             : OPEN_BOUNDARY

Device-tree root register encoding:
#address-cells                  : 0x00000002
#size-cells                     : 0x00000002
Register cell format            : 64-bit address + 64-bit size pairs
interrupt-parent                : 0x00000001
interrupt-parent target         : /intpol-controller@10200a80
reserved-memory/ranges          : present, 0-byte property
reserved-memory/ranges payload  : empty
reserved-memory/ranges status   : command status 0

Core register-map facts:
| Field | Value | Layer | Classification | Comment |
|---|---|---|---|---|
| DT root #address-cells | 0x00000002 | DT root property | CONFIRMED | Root register cells decode as 64-bit address + 64-bit size pairs |
| DT root #size-cells | 0x00000002 | DT root property | CONFIRMED | Required for all decoded register windows in this register-map capture |
| DT root interrupt-parent | 0x00000001 | DT root property | CONFIRMED | Resolves to /intpol-controller@10200a80 |
| reserved-memory/ranges | 0-byte file | DT property | CONFIRMED_EMPTY_PROPERTY | Present file; size 0; xxd payload empty; command status 0 |
| boot device anchor | bootdevice,11230000.mmc | boot cmdline / platform alias | CONFIRMED | /sys/bus/platform/devices/11230000.msdc aliases to /sys/devices/platform/bootdevice |
| primary eMMC/MSDC controller | /msdc@11230000 / driver mtk-msdc | platform binding | CONFIRMED_ALIAS_BINDING | Bus alias 11230000.msdc -> bootdevice |
| USB controller alias | /sys/bus/platform/devices/11200000.usb0 -> /sys/devices/platform/mt_usb | platform alias | CONFIRMED_ALIAS_BINDING | Direct path /sys/devices/platform/11200000.usb0 is absent; live alias resolves to /sys/devices/platform/mt_usb |
| USB driver | mt_usb | platform driver | CONFIRMED | OF node /usb0@11200000; compatible mediatek,mt6765-usb20 |
| DVFSRC register topology | 0x10012000 size 0x1000 + 0x00110780 size 0x80 | DT + iomem + platform | DUAL_REGISTER_WINDOW_CONFIRMED | Both windows appear in /proc/iomem as /dvfsrc@10012000 |
| DVFSRC driver | helio-dvfsrc | runtime binding | CONFIRMED | Platform device 10012000.dvfsrc |
| TOPRGU/watchdog driver | mtk-wdt | runtime binding | CONFIRMED | DT node /toprgu@10007000 |
| SCP register topology | 0x10500000 size 0x40000; 0x105c0000 size 0x3000; 0x105c4000 size 0x1000; 0x105d4000 size 0x6000 | DT + iomem + platform | CONFIRMED | Driver scp |
| EMI register topology | 0x10219000; 0x10226000; 0x1022d000; 0x10235000, each size 0x1000 | DT + iomem + platform | CONFIRMED | Driver emi_ctrl |
| DRAMC register topology | eight DT windows from 0x10228000 through 0x10236000 | DT + iomem + platform | CONFIRMED | Driver emi_clk_test |
| GCE mailbox register topology | 0x10238000 size 0x4000 | DT + platform | CONFIRMED | Driver mtk_cmdq_mbox |
| GCE mailbox interrupts | <0x0 0x9a 0x8>, <0x0 0x9b 0x8> | DT property | CONFIRMED | 24-byte interrupts payload |
| UART0 actual register base | 0x11002000 size 0x1000 | DT reg/platform | CONFIRMED_WITH_UNIT_ADDRESS_ANOMALY | DT node name is serial@11020000 |
| UART1 actual register base | 0x11003000 size 0x1000 | DT reg/platform | CONFIRMED_WITH_UNIT_ADDRESS_ANOMALY | DT node name is serial@11030000 |
| UART runtime /proc/iomem spans | 0x11002000-0x1100201f; 0x11003000-0x1100301f | iomem | CONFIRMED_LAYER_SPECIFIC | DT reg size 0x1000; iomem claimed span 0x20 |
| UART driver | mt6577-uart | runtime binding | CONFIRMED | Compatible list: mediatek,mt6758-uart / mediatek,mt6577-uart |
| I2C controller count | I2C0 through I2C6 core live controllers | DT + iomem | CONFIRMED | Each controller has main register window plus secondary low-offset window |
| SPI controller count | SPI0 through SPI5 | DT + platform + driver | CONFIRMED_EXPANDED | Live controller set is SPI0 through SPI5 |
| SPI driver | mtk-spi for SPI0-SPI5 | runtime binding | CONFIRMED | Compatible mediatek,mt6765-spi |
| interrupt phandle 0x01 | /intpol-controller@10200a80 | phandle map | CONFIRMED | Root interrupt-parent target |
| interrupt phandle 0x11 | /interrupt-controller@0c000000 | phandle map | CONFIRMED | Parent of intpol-controller; arm,gic-v3 |
| clock phandle 0x24 | /infracfg_ao@10001000 | phandle map | CONFIRMED | #clock-cells = 0x1; platform populated; no direct driver link |
| clock phandle 0x25 | /scpsys@10001000 | phandle map | CONFIRMED | #clock-cells = 0x1; platform populated; driver scpsys |
| modem phandle 0x1c | /mdcldma@10014000 | phandle map | CONFIRMED | Driver_modem binds only this composite node |
| DFD debug feature state | mediatek,enabled = 0x1 | DT private property | CONFIRMED | chain_length 0xc350; rg_dfd_timeout 0xa0 |
| runtime power surface pattern | power/control auto; runtime_status unsupported; runtime_enabled absent | platform power sysfs | CONFIRMED_FOR_SAMPLED_NODES | Applies to mdcldma, AO CLDMA subnodes, GIC auxiliary nodes, sys_cirq |

Layered register records:
| Object / field | Layer | Device path | Raw value | Decoded value | Classification | Comment |
|---|---|---|---|---|---|---|
| DVFSRC DT reg | DT reg | /dvfsrc@10012000/reg | 00000000 10012000 00000000 00001000; 00000000 00110780 00000000 00000080 | 0x10012000/0x1000 + 0x00110780/0x80 | CONFIRMED | Dual-window node |
| DVFSRC iomem | kernel resource claim | /proc/iomem | 00110780-001107ff : /dvfsrc@10012000; 10012000-10012fff : /dvfsrc@10012000 | same | AGREES | Matches DT windows |
| infracfg_ao DT reg | DT reg | /infracfg_ao@10001000/reg | 0x10001000 size 0x1000 | 0x10001000/0x1000 | CONFIRMED | Same base as scpsys reg[0] |
| scpsys DT reg | DT reg | /scpsys@10001000/reg | 6 windows | 0x10001000; 0x10006000; 0x14002000; 0x1020e000; 0x18004000; 0x18002000, all size 0x1000 | CONFIRMED | Multi-window power/syscon node |
| 0x10001000 iomem owner | kernel resource claim | /proc/iomem | 10001000-10001fff : /scpsys@10001000 | scpsys owns iomem label | LAYER_OWNER_SPLIT | infracfg_ao shares same base in DT/platform |
| mcucfg DT reg | DT reg | /mcucfg@10200000/reg | 0x10200000 size 0x1000 | same | CONFIRMED | Platform populated; no driver link |
| mcucfg_mp0_counter DT reg | DT reg | /mcucfg_mp0_counter@10200000/reg | 0x10200000 size 0x4000 | same | CONFIRMED | Same base, larger span |
| DFD DT reg | DT reg | /dfd@10200b00/reg | 0x10200b00 size 0x10000 | same | CONFIRMED | Broad debug span; platform populated; no driver link |
| intpol-controller DT reg | DT reg | /intpol-controller@10200a80/reg | 0x10200a80 size 0x50 | same | CONFIRMED | No matching platform device found by discovery; included inside GIC reg[2] |
| intpol-controller marker | DT marker property | /intpol-controller@10200a80/interrupt-controller | size 0 | present marker | CONFIRMED_ZERO_BYTE_MARKER | #interrupt-cells = 0x3 |
| interrupt-controller DT reg | DT reg | /interrupt-controller@0c000000/reg | 0x0c000000/0x40000; 0x0c100000/0x200000; 0x10200a80/0x50 | GIC v3 composite + intpol offset | CONFIRMED | phandle 0x11 |
| gic500 DT reg | DT reg | /gic500@0c000000/reg | 0x0c000000 size 0x1000 | same | CONFIRMED_AUXILIARY_NODE | Platform populated unbound |
| gic_cpu DT reg | DT reg | /gic_cpu@0c400000/reg | 0x0c400000 size 0x40000 | same | CONFIRMED_AUXILIARY_NODE | Platform populated unbound |
| interrupt iomem gap | kernel resource claim | /proc/iomem grep | no rows for 0c000000/0c400000/10200a80/10204000/gic/interrupt/sys_cirq/intpol | no iomem claim visible | IOMEM_GAP | Not absence; DT/platform witnesses populate nodes |
| devapc DT reg | DT reg | /devapc@10207000/reg | 0x10207000/0x1000 + 0x1000e000/0x1000 | dual window | CONFIRMED | Driver devapc |
| bus_dbg DT reg | DT reg | /bus_dbg@10208000/reg | 0x10208000/0x1000 + 0x10001000/0x1000 | dual window | CONFIRMED | Driver systracker; reuses scpsys/infracfg base |
| trng DT/platform | DT + platform | /trng@1020f000; /sys/devices/platform/1020f000.trng | driver absent | 0x1020f000/0x1000 | POPULATED_UNBOUND | IRQ present in DT |
| dxcc_sec DT/platform | DT + platform | /dxcc_sec@10210000; /sys/devices/platform/10210000.dxcc_sec | driver absent | 0x10210000/0x1000 | POPULATED_UNBOUND | IRQ present in DT |
| cq_dma DT reg | DT reg | /cq_dma@10212000/reg | 0x10212000/0x80 + 0x10212080/0x80 | dual 0x80 windows | CONFIRMED | Driver cqdma; /proc/iomem matches both |
| cq_dma private config | DT private properties | /cq_dma@10212000/* | keep_clock_ao="yes"; nr_channel=0x2; clocks=<0x24 0x43> | clock provider infracfg_ao index 0x43 | CONFIRMED | clock-names=cqdma |
| mdcldma DT reg | DT reg | /mdcldma@10014000/reg | 0x10014000; 0x1021b000; 0x10209000; 0x1020a000 each size 0x1000 | composite MD1 CLDMA/CCIF root | CONFIRMED | phandle 0x1c |
| mdcldma driver binding | runtime binding | /sys/bus/platform/drivers/driver_modem | only bound link 10014000.mdcldma | composite driver binding | CONFIRMED | Subnodes are populated but unbound |
| AO CLDMA DT properties | DT property set | /apcldmain_ao@10014000 etc. | name, compatible, reg only | status/phandle/interrupts absent | CONFIRMED | Six AO nodes, each size 0x400 |
| AO CLDMA platform state | platform sysfs | /sys/devices/platform/10014xxx / 10015xxx | driver absent; modalias/of_node/uevent present; runtime_status unsupported | platform-populated unbound | CONFIRMED | driver_override=(null); control=auto |

Declaration, population and binding state:
| Object | Declared by | Live platform object | Runtime state | Physical evidence | Classification | Comment |
|---|---|---|---|---|---|---|
| /dvfsrc@10012000 | Live device tree | /sys/devices/platform/10012000.dvfsrc | driver helio-dvfsrc; /proc/iomem claims both windows | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Dual-window confirmed |
| /toprgu@10007000 | Live device tree | /sys/devices/platform/10007000.toprgu | driver mtk-wdt | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Watchdog/TOPRGU |
| /scp@10500000 | Live device tree | /sys/devices/platform/10500000.scp | driver scp | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Four register windows |
| /emi@10219000 | Live device tree | /sys/devices/platform/10219000.emi | driver emi_ctrl | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Four register windows |
| /dramc@1022a000 | Live device tree | /sys/devices/platform/1022a000.dramc | driver emi_clk_test | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Eight register windows |
| /gce_mbox@10238000 | Live device tree | /sys/devices/platform/10238000.gce_mbox | driver mtk_cmdq_mbox | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | GCE mailbox |
| /infracfg_ao@10001000 | Live device tree | /sys/devices/platform/10001000.infracfg_ao | no driver link; uevent present | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_PROVIDER | phandle 0x24; #clock-cells 1 |
| /scpsys@10001000 | Live device tree | /sys/devices/platform/10001000.scpsys | driver scpsys | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND_PROVIDER | phandle 0x25; #clock-cells 1 |
| /mcucfg@10200000 | Live device tree | /sys/devices/platform/10200000.mcucfg | no driver link | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND | Same base as mcucfg_mp0_counter |
| /mcucfg_mp0_counter@10200000 | Live device tree | /sys/devices/platform/10200000.mcucfg_mp0_counter | no driver link | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND | Same base; larger size 0x4000 |
| /dfd@10200b00 | Live device tree | /sys/devices/platform/10200b00.dfd | no driver link | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_DEBUG | mediatek,enabled=1 |
| /intpol-controller@10200a80 | Live device tree | no platform device found by discovery | interrupt-controller marker present | NOT_TESTED_THIS_PASS | DECLARED_NOT_PLATFORM_MATCHED | phandle 0x1; parent 0x11 |
| /sys_cirq@10204000 | Live device tree | /sys/devices/platform/10204000.sys_cirq | no driver link; cirq driver exists | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND | cirq_num=209; spi_start_offset=72 |
| /interrupt-controller@0c000000 | Live device tree | kernel interrupt-controller DT node; no specific platform device path captured for this exact node | GIC v3 marker and phandle 0x11 | NOT_TESTED_THIS_PASS | DECLARED_INTERRUPT_CONTROLLER | Parent self-reference 0x11 |
| /gic500@0c000000 | Live device tree | /sys/devices/platform/c000000.gic500 | driver absent; modalias/uevent present | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_AUXILIARY | mediatek,gic500 |
| /gic_cpu@0c400000 | Live device tree | /sys/devices/platform/c400000.gic_cpu | driver absent; modalias/uevent present | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_AUXILIARY | mediatek,gic_cpu |
| /devapc@10207000 | Live device tree | /sys/devices/platform/10207000.devapc | driver devapc | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | Dual-window; clock provider 0x24 index 0x28 |
| /bus_dbg@10208000 | Live device tree | /sys/devices/platform/10208000.bus_dbg | driver systracker | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | bus_dbg_con_offset 0x2fc |
| /trng@1020f000 | Live device tree | /sys/devices/platform/1020f000.trng | no driver link | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND | IRQ present |
| /dxcc_sec@10210000 | Live device tree | /sys/devices/platform/10210000.dxcc_sec | no driver link | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND | IRQ present |
| /cq_dma@10212000 | Live device tree | /sys/devices/platform/10212000.cq_dma | driver cqdma | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND | 2 channels; keep_clock_ao yes |
| /mdcldma@10014000 | Live device tree | /sys/devices/platform/10014000.mdcldma | driver driver_modem | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND_COMPOSITE | MD1 root/composite modem node |
| AO CLDMA six nodes | Live device tree | /sys/devices/platform/10014000.apcldmain_ao etc. | no driver link; runtime_status unsupported | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_SUBNODES | Each 0x400; no status/phandle/interrupts |
| AP/MD CCIF0-3 nodes | Live device tree | /sys/devices/platform/10209xxx/1020xxxx/1023xxxx | no direct driver link; included in platform namespace | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_UNBOUND_SUBNODES | Composite mdcldma owns CCIF0 AP/MD windows in reg list |
| /usb0@11200000 | Live device tree | /sys/devices/platform/mt_usb via alias 11200000.usb0 | driver mt_usb | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND_ALIAS | Two windows: 0x11200000 and 0x11cc0000 |
| /msdc@11230000 | Live device tree | /sys/devices/platform/bootdevice via alias 11230000.msdc | driver mtk-msdc | NOT_TESTED_THIS_PASS | DECLARED_POPULATED_BOUND_ALIAS | eMMC bootdevice |

Register-map sysfs objects:
| Path | Type | Mode | UID:GID | SELinux context | Size bytes | SHA256 | Symlink target | Classification | Comment |
|---|---|---|---|---|---|---|---|---|---|
| /sys/firmware/devicetree/base/reserved-memory/ranges | DT property file | -r--r--r-- | root:root | u:object_r:sysfs:s0 | 0 | NOT_APPLICABLE_SYSFS | NOT_APPLICABLE | CONFIRMED_EMPTY_PROPERTY | xxd payload empty; wc -c 0 |
| /sys/bus/platform/devices/11200000.usb0 | sysfs symlink | lrwxrwxrwx | root:root | u:object_r:sysfs:s0 | 0 | NOT_APPLICABLE_SYSFS | ../../../devices/platform/mt_usb | CONFIRMED_ALIAS | Real device mt_usb |
| /sys/bus/platform/devices/11230000.msdc | sysfs symlink | lrwxrwxrwx | root:root | u:object_r:sysfs:s0 | 0 | NOT_APPLICABLE_SYSFS | ../../../devices/platform/bootdevice | CONFIRMED_ALIAS | Real device bootdevice |
| /sys/bus/platform/devices/c000000.gic500 | sysfs symlink | lrwxrwxrwx | root:root | u:object_r:sysfs:s0 | 0 | NOT_APPLICABLE_SYSFS | ../../../devices/platform/c000000.gic500 | CONFIRMED_PLATFORM_ALIAS | GIC auxiliary platform node |
| /sys/bus/platform/devices/c400000.gic_cpu | sysfs symlink | lrwxrwxrwx | root:root | u:object_r:sysfs:s0 | 0 | NOT_APPLICABLE_SYSFS | ../../../devices/platform/c400000.gic_cpu | CONFIRMED_PLATFORM_ALIAS | GIC CPU auxiliary node |

Live platform binding state:
| Platform object | Binding type | Driver | State | Domain / context | Classification | Comment |
|---|---|---|---|---|---|---|
| /sys/devices/platform/10012000.dvfsrc | platform driver | helio-dvfsrc | bound | sysfs | BOUND | OF node /dvfsrc@10012000 |
| /sys/devices/platform/10007000.toprgu | platform driver | mtk-wdt | bound | sysfs | BOUND | OF node /toprgu@10007000 |
| /sys/devices/platform/10500000.scp | platform driver | scp | bound | sysfs | BOUND | OF node /scp@10500000 |
| /sys/devices/platform/10219000.emi | platform driver | emi_ctrl | bound | sysfs | BOUND | OF node /emi@10219000 |
| /sys/devices/platform/1022a000.dramc | platform driver | emi_clk_test | bound | sysfs | BOUND_WITH_NONSTANDARD_DRIVER_NAME | Driver name retained exactly |
| /sys/devices/platform/10238000.gce_mbox | platform driver | mtk_cmdq_mbox | bound | sysfs | BOUND | GCE mailbox |
| /sys/devices/platform/11002000.serial | platform driver | mt6577-uart | bound | sysfs | BOUND_WITH_DT_NODE_NAME_MISMATCH | of_node serial@11020000 |
| /sys/devices/platform/11003000.serial | platform driver | mt6577-uart | bound | sysfs | BOUND_WITH_DT_NODE_NAME_MISMATCH | of_node serial@11030000 |
| /sys/devices/platform/mt_usb | platform driver | mt_usb | bound | sysfs | BOUND_ALIAS_TARGET | bus alias 11200000.usb0 |
| /sys/devices/platform/bootdevice | platform driver | mtk-msdc | bound | sysfs | BOUND_ALIAS_TARGET | bus alias 11230000.msdc |
| /sys/devices/platform/10207000.devapc | platform driver | devapc | bound | sysfs | BOUND | DEVAPC dual-window node |
| /sys/devices/platform/10208000.bus_dbg | platform driver | systracker | bound | sysfs | BOUND | bus_dbg-v2 |
| /sys/devices/platform/10212000.cq_dma | platform driver | cqdma | bound | sysfs | BOUND | cqdma-v1 |
| /sys/devices/platform/10014000.mdcldma | platform driver | driver_modem | bound | sysfs | BOUND_COMPOSITE_MODEM_ROOT | Only bound link in driver_modem |
| /sys/devices/platform/10001000.scpsys | platform driver | scpsys | bound | sysfs | BOUND_PROVIDER | Same base as infracfg_ao |
| /sys/devices/platform/10001000.infracfg_ao | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND_PROVIDER | clock provider phandle 0x24 |
| /sys/devices/platform/1020f000.trng | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND | IRQ present in DT |
| /sys/devices/platform/10210000.dxcc_sec | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND | IRQ present in DT |
| /sys/devices/platform/c000000.gic500 | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND_AUXILIARY | runtime_status unsupported |
| /sys/devices/platform/c400000.gic_cpu | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND_AUXILIARY | runtime_status unsupported |
| /sys/devices/platform/10204000.sys_cirq | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND | cirq driver namespace exists |
| AO CLDMA subnodes | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND_RUNTIME_PM_UNSUPPORTED | power/control auto; runtime_status unsupported |
| AP/MD CCIF0-3 subnodes | platform driver | DRIVER_LINK_ABSENT | populated unbound | sysfs | POPULATED_UNBOUND_SUBNODES | resource files absent |

Core SoC register topology:
| Register block | Address / index | Size / range | Domain | Device-tree object | Driver / binding | Classification | Comment |
|---|---|---|---|---|---|---|---|
| DVFSRC main | 0x10012000 | 0x1000 | core SoC | dvfsrc | helio-dvfsrc | CONFIRMED | DT and iomem agree |
| DVFSRC secondary | 0x00110780 | 0x80 | core SoC | dvfsrc | helio-dvfsrc | CONFIRMED | Low-address DVFSRC window |
| TOPRGU/watchdog | 0x10007000 | 0x1000 | core SoC | toprgu | mtk-wdt | CONFIRMED | interrupts <0 0x8b 0> |
| SCP sysctrl | 0x10500000 | 0x40000 | core SoC | scp | scp | CONFIRMED | interrupt <0 0xc0 0x4> |
| SCP aux0 | 0x105c0000 | 0x3000 | scp | scp | scp | CONFIRMED | From DT/iomem |
| SCP aux1 | 0x105c4000 | 0x1000 | scp | scp | scp | CONFIRMED | From DT/iomem |
| SCP aux2 | 0x105d4000 | 0x6000 | scp | scp | scp | CONFIRMED | From DT/iomem |
| EMI window 0 | 0x10219000 | 0x1000 | memory subsystem | emi | emi_ctrl | CONFIRMED | interrupt <0 0x90 0x8> |
| EMI window 1 | 0x10226000 | 0x1000 | memory subsystem | emi | emi_ctrl | CONFIRMED | — |
| EMI window 2 | 0x1022d000 | 0x1000 | memory subsystem | emi | emi_ctrl | CONFIRMED | — |
| EMI window 3 | 0x10235000 | 0x1000 | memory subsystem | emi | emi_ctrl | CONFIRMED | — |
| DRAMC window 0 | 0x1022a000 | 0x2000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 1 | 0x10232000 | 0x2000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 2 | 0x1022c000 | 0x1000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 3 | 0x10234000 | 0x1000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 4 | 0x10228000 | 0x2000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 5 | 0x10230000 | 0x2000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 6 | 0x1022e000 | 0x1000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| DRAMC window 7 | 0x10236000 | 0x1000 | memory subsystem | dramc | emi_clk_test | CONFIRMED | — |
| GCE mailbox | 0x10238000 | 0x4000 | GCE/CMDQ | gce_mbox | mtk_cmdq_mbox | CONFIRMED | interrupts 0x9a/0x9b |
| AXI-BUS primary | 0x18000000 | 0x100000 | AXI | AXI-BUS | not bound by this pass | CONFIRMED_IOMEM | /proc/iomem: 18000000-180fffff |
| AXI-BUS secondary | 0xbf000000 | 0x300000 | AXI | AXI-BUS | not bound by this pass | CONFIRMED_IOMEM | /proc/iomem: bf000000-bf2fffff |
| USB controller main | 0x11200000 | 0x10000 | USB | usb0 | mt_usb | CONFIRMED_ALIAS_BOUND | compatible mediatek,mt6765-usb20 |
| USB PHY/secondary | 0x11cc0000 | 0x10000 | USB | usb0 | mt_usb | CONFIRMED | Same DT node as usb0 |
| MSDC bootdevice | 0x11230000 | 0x10000 | storage | msdc | mtk-msdc | CONFIRMED_ALIAS_BOUND | /sys/devices/platform/bootdevice |
| UART0 DT node | serial@11020000 | reg base 0x11002000 size 0x1000 | UART | ttyS0/serial | mt6577-uart | UNIT_ADDRESS_MISMATCH | iomem span 0x20 |
| UART1 DT node | serial@11030000 | reg base 0x11003000 size 0x1000 | UART | ttyS1/serial | mt6577-uart | UNIT_ADDRESS_MISMATCH | iomem span 0x20 |
| I2C0 main | 0x11007000 | 0x1000 | I2C | i2c0 | mt-i2c | CONFIRMED | secondary 0x11000080/0x80 |
| I2C1 main | 0x11008000 | 0x1000 | I2C | i2c1 | mt-i2c | CONFIRMED | secondary 0x11000100/0x80 |
| I2C2 main | 0x11009000 | 0x1000 | I2C | i2c2 | mt-i2c | CONFIRMED | secondary 0x11000180/0x180 |
| I2C3 main | 0x1100f000 | 0x1000 | I2C | i2c3 | mt-i2c | CONFIRMED | secondary 0x11000300/0x100 |
| I2C4 main | 0x11011000 | 0x1000 | I2C | i2c4 | mt-i2c | CONFIRMED | secondary 0x11000400/0x180 |
| I2C5 main | 0x11016000 | 0x1000 | I2C | i2c5 | mt-i2c | CONFIRMED | secondary 0x11000580/0x80 |
| I2C6 main | 0x1100d000 | 0x1000 | I2C | i2c6 | mt-i2c | CONFIRMED | secondary 0x11000600/0x80 |
| SPI0 | 0x1100a000 | 0x1000 | SPI | spi@1100a000 | mtk-spi | CONFIRMED | IRQ 0x76 |
| SPI1 | 0x11010000 | 0x1000 | SPI | spi@11010000 | mtk-spi | CONFIRMED | IRQ 0x7a |
| SPI2 | 0x11012000 | 0x1000 | SPI | spi@11012000 | mtk-spi | CONFIRMED | IRQ 0x80 |
| SPI3 | 0x11013000 | 0x1000 | SPI | spi@11013000 | mtk-spi | CONFIRMED | IRQ 0x81 |
| SPI4 | 0x11014000 | 0x1000 | SPI | spi@11014000 | mtk-spi | CONFIRMED | IRQ 0x5f |
| SPI5 | 0x11015000 | 0x1000 | SPI | spi@11015000 | mtk-spi | CONFIRMED | IRQ 0x60 |
| GIC v3 distributor | 0x0c000000 | 0x40000 | interrupt-controller | interrupt-controller@0c000000 | kernel interrupt controller | CONFIRMED | arm,gic-v3 |
| GIC v3 redistributor | 0x0c100000 | 0x200000 | interrupt-controller | interrupt-controller@0c000000 | kernel interrupt controller | CONFIRMED | #redistributor-regions=1 |
| GIC intpol window | 0x10200a80 | 0x50 | interrupt-controller | interrupt-controller@0c000000 / intpol-controller@10200a80 | kernel interrupt topology | CONFIRMED_OVERLAP | Same range as intpol-controller DT node |
| GIC500 auxiliary | 0x0c000000 | 0x1000 | interrupt-controller | gic500@0c000000 | driver absent | POPULATED_UNBOUND_AUXILIARY | overlaps GIC distributor |
| GIC CPU auxiliary | 0x0c400000 | 0x40000 | interrupt-controller | gic_cpu@0c400000 | driver absent | POPULATED_UNBOUND_AUXILIARY | no phandle/status |
| CIRQ | 0x10204000 | 0x1000 | interrupt subsystem | sys_cirq@10204000 | driver absent; cirq driver namespace exists | POPULATED_UNBOUND | cirq_num=209 |
| DEVAPC main | 0x10207000 | 0x1000 | security/access control | devapc | devapc | CONFIRMED_BOUND | clock <0x24 0x28> |
| DEVAPC secondary | 0x1000e000 | 0x1000 | security/access control | devapc | devapc | CONFIRMED | secondary window not separately claimed in iomem grep |
| bus_dbg main | 0x10208000 | 0x1000 | debug/systracker | bus_dbg | systracker | CONFIRMED_BOUND | offset 0x2fc |
| bus_dbg secondary | 0x10001000 | 0x1000 | debug/systracker | bus_dbg | systracker | CROSS_NODE_REGISTER_REUSE | same base as scpsys/infracfg_ao |
| TRNG | 0x1020f000 | 0x1000 | security/random | trng | driver absent | POPULATED_UNBOUND | interrupt <0 0xa1 0x8> |
| DXCC secure | 0x10210000 | 0x1000 | security/crypto | dxcc_sec | driver absent | POPULATED_UNBOUND | interrupt <0 0xa2 0x4> |
| CQDMA window 0 | 0x10212000 | 0x80 | DMA | cq_dma | cqdma | CONFIRMED_BOUND | interrupt 0x71 |
| CQDMA window 1 | 0x10212080 | 0x80 | DMA | cq_dma | cqdma | CONFIRMED_BOUND | interrupt 0x72 |
| MDCLDMA composite AO window | 0x10014000 | 0x1000 | modem/CLDMA | mdcldma | driver_modem | CONFIRMED_COMPOSITE | part of mdcldma reg |
| MDCLDMA AP CLDMA window | 0x1021b000 | 0x1000 | modem/CLDMA | mdcldma | driver_modem | CONFIRMED_COMPOSITE | contains zero-size AP subnodes |
| MDCLDMA AP CCIF0 window | 0x10209000 | 0x1000 | modem/CCIF | mdcldma/ap_ccif0 | driver_modem composite | CONFIRMED_COMPOSITE | AP CCIF0 subnode populated unbound |
| MDCLDMA MD CCIF0 window | 0x1020a000 | 0x1000 | modem/CCIF | mdcldma/md_ccif0 | driver_modem composite | CONFIRMED_COMPOSITE | MD CCIF0 subnode populated unbound |
| AO AP CLDMA in | 0x10014000 | 0x400 | modem/CLDMA AO | apcldmain_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AO AP CLDMA out | 0x10014400 | 0x400 | modem/CLDMA AO | apcldmaout_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AO AP CLDMA misc | 0x10014800 | 0x400 | modem/CLDMA AO | apcldmamisc_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AO MD CLDMA in | 0x10015000 | 0x400 | modem/CLDMA AO | mdcldmain_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AO MD CLDMA out | 0x10015400 | 0x400 | modem/CLDMA AO | mdcldmaout_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AO MD CLDMA misc | 0x10015800 | 0x400 | modem/CLDMA AO | mdcldmamisc_ao | unbound | POPULATED_UNBOUND_SUBNODE | no status/phandle/interrupts |
| AP CLDMA in subnode | 0x1021b000 | 0x0 | modem/CLDMA | apcldmain | unbound | ZERO_SIZE_SUBREG_NODE | composite mdcldma owns 0x1021b000/0x1000 |
| AP CLDMA out subnode | 0x1021b400 | 0x0 | modem/CLDMA | apcldmaout | unbound | ZERO_SIZE_SUBREG_NODE | no driver link |
| AP CLDMA misc subnode | 0x1021b800 | 0x0 | modem/CLDMA | apcldmamisc | unbound | ZERO_SIZE_SUBREG_NODE_WITH_IRQ | interrupt <0 0xa5 0x4> |
| MD CLDMA in subnode | 0x1021c000 | 0x400 | modem/CLDMA | mdcldmain | unbound | POPULATED_UNBOUND_SUBNODE | — |
| MD CLDMA out subnode | 0x1021c400 | 0x400 | modem/CLDMA | mdcldmaout | unbound | POPULATED_UNBOUND_SUBNODE | — |
| MD CLDMA misc subnode | 0x1021c800 | 0x1000 | modem/CLDMA | mdcldmamisc | unbound | POPULATED_UNBOUND_SUBNODE | — |

Clock, interrupt and composite dependencies:
| Provider | Consumer | Interface | Runtime state | Classification | Comment |
|---|---|---|---|---|---|
| /infracfg_ao@10001000 phandle 0x24 | /devapc@10207000 | clocks <0x24 0x28> | YES_DT_RUNTIME | CONFIRMED_CLOCK_DEPENDENCY | clock-names devapc-infra-clock |
| /infracfg_ao@10001000 phandle 0x24 | /cq_dma@10212000 | clocks <0x24 0x43> | YES_DT_RUNTIME | CONFIRMED_CLOCK_DEPENDENCY | clock-names cqdma |
| /scpsys@10001000 phandle 0x25 | /mdcldma@10014000 | clocks <0x25 0x00> | YES_DT_RUNTIME | CONFIRMED_CLOCK_DEPENDENCY | clock-name scp-sys-md1-main |
| /infracfg_ao@10001000 phandle 0x24 | /mdcldma@10014000 | clocks <0x24 0x32>, <0x24 0x29>, <0x24 0x2c>, <0x24 0x23>, <0x24 0x24>, <0x24 0x51>, <0x24 0x52> | YES_DT_RUNTIME | CONFIRMED_CLOCK_DEPENDENCY | CLDMA/CCIF infra clocks |
| /intpol-controller@10200a80 phandle 0x01 | DT root | interrupt-parent | YES_DT_RUNTIME | CONFIRMED_INTERRUPT_PARENT | root interrupt-parent = 0x01 |
| /interrupt-controller@0c000000 phandle 0x11 | /intpol-controller@10200a80 | interrupt-parent | YES_DT_RUNTIME | CONFIRMED_INTERRUPT_PARENT | intpol parent = GIC v3 node |
| /interrupt-controller@0c000000 phandle 0x11 | /interrupt-controller@0c000000 | interrupt-parent self-reference | YES_DT_RUNTIME | CONFIRMED_SELF_REFERENCE | GIC interrupt-parent = 0x11 |
| driver_modem | /mdcldma@10014000 | platform driver binding | YES | COMPOSITE_DRIVER_DEPENDENCY | driver_modem binds only 10014000.mdcldma |
| /mdcldma@10014000 | AO/AP/MD CLDMA and CCIF subnodes | composite register ownership | PARTIAL_DT_RUNTIME | COMPOSITE_OWNER_DEPENDENCY | subnodes populated but unbound |

Register-map anomalies:
| Anomaly | Evidence A | Evidence B | Exact condition | Status | Classification | Comment |
|---|---|---|---|---|---|---|
| DVFSRC low-address secondary window | DT reg includes 0x00110780/0x80 | Live device tree and /proc/iomem expose 0x10012000/0x1000 and 0x00110780/0x80 | Secondary low-address DVFSRC window is independently declared and claimed | RESOLVED_CURRENT_PASS | PRIOR_MAP_INCOMPLETE | Both windows retained |
| UART unit-address mismatch | DT node serial@11020000 and serial@11030000 | DT reg/platform/iomem bases 0x11002000 and 0x11003000 | Node unit-address text does not match reg base | OPEN_RETAIN | UNIT_ADDRESS_TEXT_DOES_NOT_MATCH_REG_BASE | Raw DTB/decompiled source confirmation optional |
| UART DT size vs iomem size | DT size 0x1000 | /proc/iomem span 0x20 | Register declaration span differs from claimed runtime resource span | OPEN_RETAIN | LAYER_SPECIFIC_SIZE_DIFFERENCE | No collapse |
| SPI count expansion | Live device tree, platform namespace, and driver binding expose SPI0-SPI5 | Live DT/platform/driver confirm SPI0-SPI5 | Complete captured controller set is retained | RESOLVED_CURRENT_PASS | PRIOR_MAP_INCOMPLETE | SPI0-SPI5 retained |
| I2C secondary windows omitted | Live device tree and /proc/iomem expose main and secondary I2C windows | DT/iomem confirm secondary low-offset windows | Complete captured controller set is retained | RESOLVED_CURRENT_PASS | PRIOR_MAP_INCOMPLETE | Main and secondary windows retained |
| USB direct path false absence | /sys/devices/platform/11200000.usb0 absent | bus alias points to /sys/devices/platform/mt_usb | Direct platform path invalid for live alias | RESOLVED_CURRENT_PASS | PLATFORM_ALIAS_NOT_ABSENCE | Alias and resolved platform path retained |
| MSDC direct path false absence | /sys/devices/platform/11230000.msdc absent | bus alias points to /sys/devices/platform/bootdevice | Direct platform path invalid for live alias | RESOLVED_CURRENT_PASS | PLATFORM_ALIAS_NOT_ABSENCE | Alias and resolved platform path retained |
| infracfg/scpsys same base | infracfg_ao reg 0x10001000/0x1000 | scpsys reg[0] 0x10001000/0x1000; /proc/iomem labels scpsys | Same base with different property and driver surface | OPEN_RETAIN | SAME_BASE_MULTI_NODE_WITH_DRIVER_OWNER_SPLIT | Preserve both nodes |
| mcucfg same-base different-size | mcucfg 0x10200000/0x1000 | mcucfg_mp0_counter 0x10200000/0x4000 | Same base, different declared size | OPEN_RETAIN | SAME_BASE_DIFFERENT_SIZE_DT_OVERLAP | Preserve both nodes |
| DFD broad overlap | dfd 0x10200b00/0x10000 | Nearby 1020xxxx control/security nodes | Broad debug span crosses neighborhoods | OPEN_RETAIN | BROAD_DEBUG_REGISTER_SPAN | Further raw DT/kernel source may explain ownership |
| intpol platform gap | DT node /intpol-controller@10200a80 | no matching platform device found by discovery | Declared interrupt-controller has no expected platform device | OPEN_RETAIN | DT_DECLARED_NO_MATCHING_PLATFORM_DEVICE_FOUND | Leave as DT-only interrupt-controller node |
| devapc secondary window | devapc reg[1] 0x1000e000/0x1000 | no iomem row returned in targeted grep | DT/platform binding exists, iomem silent | OPEN_RETAIN | IOMEM_GAP | No absence inference |
| bus_dbg secondary base reuse | bus_dbg reg[1] 0x10001000/0x1000 | infracfg/scpsys share 0x10001000 | Debug/systracker reuses same base | OPEN_RETAIN | CROSS_NODE_REGISTER_REUSE | Preserve exact reuse |
| GIC auxiliary overlap | interrupt-controller reg includes 0x0c000000/0x40000 | gic500 reg 0x0c000000/0x1000 | Auxiliary node overlaps main GIC distributor range | OPEN_RETAIN | OVERLAPPING_INTERRUPT_CONTROLLER_AUX_NODE | Preserve both nodes |
| GIC/intpol overlap | interrupt-controller reg[2] 0x10200a80/0x50 | intpol-controller reg 0x10200a80/0x50 | Same register range represented under both nodes | RESOLVED_CURRENT_PASS | CONFIRMED_OVERLAP | Phandle chain explains relationship |
| GIC/sys_cirq iomem silence | DT/platform nodes populated | /proc/iomem grep returned no rows | Runtime resource not visible in /proc/iomem | OPEN_RETAIN | IOMEM_GAP | Keep DT/platform as authority for declaration/population |
| CLDMA composite/subnode asymmetry | driver_modem binds mdcldma root | AO/AP/MD CLDMA and CCIF subnodes populated unbound | Composite driver owns root only | OPEN_RETAIN | COMPOSITE_DRIVER_WITH_UNBOUND_SUBNODES | Expand in radio/modem section if needed |
| AP CLDMA zero-size subnodes | apcldmain/apcldmaout/apcldmamisc sizes 0x0 | mdcldma composite owns 0x1021b000/0x1000 | Subnodes are zero-size but platform-populated | OPEN_RETAIN | ZERO_SIZE_SUBREG_NODES | Preserve raw sizes |
| resource file absence pattern | sampled platform devices have no resource file | DT reg/platform/driver entries exist | sysfs resource files absent across sampled nodes | OPEN_RETAIN | SYSFS_RESOURCE_NOT_EXPOSED | Do not infer no registers |
| populated unbound security nodes | trng/dxcc_sec platform devices populated | driver links absent | Security nodes declared and populated without direct driver binding | OPEN_RETAIN | POPULATED_UNBOUND_SECURITY_NODE | hardware database1 owns security interpretation |

Associated device data retained at register-anchor level:
| Device object / fact | Register-map handling | Classification | Comment |
|---|---|---|---|
| Camera/CCU I2C endpoints under I2C2/I2C4/I2C6 | Register anchor only retained here | REGISTER_ANCHOR_ONLY | Endpoint sensor/CCU topology belongs to camera section |
| Touch endpoint cap_touch@5d under I2C0 | Register anchor only retained here | REGISTER_ANCHOR_ONLY | Touch endpoint topology is peripheral-specific |
| Heart-rate / sub-PMIC / Wiite CP I2C endpoints | Register anchor only retained here | REGISTER_ANCHOR_ONLY | Endpoint function classification not core register-map scope |
| Modem service behavior above CLDMA/CCIF register declaration | Composite register map retained here | REGISTER_ANCHOR_ONLY | Runtime radio/modem services and protocols belong to radio/modem section |
| TRNG/DXCC/HACC/keymaster security semantics | Register node retained here | REGISTER_ANCHOR_ONLY | Security-substrate interpretation belongs to security section |
| GPIO pin function and mux detail | Count boundary retained here | REGISTER_ANCHOR_ONLY | Full GPIO ownership/function mapping is not core register-map scope |
| USB role/mode/PHY operational behavior | Register anchor and alias retained here | REGISTER_ANCHOR_ONLY | Connectivity behavior belongs to connectivity section |
| Storage geometry and partition layout | MSDC bootdevice anchor retained here | REGISTER_ANCHOR_ONLY | Geometry and partitions are not core SoC register-map scope |

Open register-map boundaries:
| Boundary | Known | OPEN_BOUNDARY | Reason | Closure evidence | Classification |
|---|---|---|---|---|---|
| Full 1.8 register universe completeness | Expanded set now includes core, interrupt, clock, DMA, CLDMA/CCIF, USB/MSDC/UART/I2C/SPI anchors captured this pass | Whether every top-level DT reg node belonging in Core SoC register map has been enumerated | This pass targeted discovered families, not a complete mechanically decoded all-reg-node inventory | Exhaustive DT reg inventory with domain classification for every top-level reg node | SECTION_OPEN_COMPLETENESS_BOUNDARY |
| Raw stock DTB equivalence | Live DT property files are decoded | Raw stock boot/DTB byte-for-byte equivalence for each node is not proven in this register-map capture pass | Live rooted runtime DT can reflect selected DTBO/boot runtime, though hardware exposure remains valid | Raw boot/dtb/dtbo extraction cross-check against live DT paths | RAW_IMAGE_EQUIVALENCE_OPEN |
| /proc/iomem silence semantics | Many DT/platform nodes are populated while /proc/iomem is silent | Kernel reason for each silent resource is not decoded | /proc/iomem is a resource-claim witness, not complete DT declaration witness | Kernel source/resource registration audit or debugfs resource tree if available | IOMEM_GAP_OPEN |
| UART unit-address anomaly origin | serial node names mismatch reg bases; platform/iomem agree with reg bases | Whether mismatch originated in vendor DTS typo, address formatting convention, or overlay tooling is not proven | Live DT cannot alone explain authoring origin | Raw decompiled DTS comparison from stock boot image | DT_AUTHORING_ORIGIN_OPEN |
| CLDMA/CCIF full modem register ownership | mdcldma composite driver owns root; subnodes populated unbound; clock/dependency data decoded | Exact kernel-internal ownership semantics for each subnode are not proven | Platform sysfs lacks direct driver links/resource files for subnodes | driver_modem source/symbol analysis or runtime modem driver debug output | COMPOSITE_DRIVER_OWNERSHIP_OPEN |
| Security node active use | TRNG and DXCC are declared/populated with interrupts but no direct driver link | Whether secure-world, TrustZone, or indirect kernel consumers use these nodes at runtime is not proven here | Core SoC register map records register map only | hardware database1 secure-substrate runtime/static analysis | TRANSFERRED_SECURITY_BOUNDARY |
| GIC auxiliary node function | gic500 and gic_cpu are populated unbound auxiliary nodes | Whether they are consumed by early kernel/GIC code before platform driver binding is not proven | No direct platform driver link/resource file exists | Kernel boot log/GIC init trace or source audit | INTERRUPT_AUXILIARY_USE_OPEN |
| Clock index semantic names | Clock phandles and numeric indices are decoded | Symbolic names for clock indices 0x23/0x24/0x28/0x29/0x2c/0x32/0x43/0x51/0x52 are not decoded | clock-output-names absent for infracfg_ao/scpsys in live DT | Kernel clock driver table/source or full clock summary | CLOCK_INDEX_NAME_OPEN |
| HACC / EFUSE / CHIPID inclusion | CHIPID, HACC, EFUSE debug, and EFUSE controller register nodes are verified device objects | Exact CHIPID, HACC, EFUSE debug, and EFUSE controller register rows are not included in the current register table | Register-map ownership for these security/silicon anchors remains unresolved | Determine final register-map inclusion and add exact verified register rows | OWNERSHIP_BOUNDARY |
| Complete SMI/LARB/media/consys treatment | SMI/CONSYS/media register blocks were observed in earlier discovery | Only core-relevant anchors are partially represented here; full media/connectivity register map not expanded | These families may be owned by display/camera/connectivity sections | Cross-section owner decision and transfer rows or inclusion rows | CROSS_SECTION_SCOPE_BOUNDARY |

Core SoC register-map status:
Register-map state              : OPEN
Open boundary count             : 10
Recorded anomaly count          : 20
Associated anchor-only items    : 8
Current-pass conclusion         : Live device-tree declarations, platform-device population, driver binding, interrupt topology, clock dependencies, and /proc/iomem claims are retained as separate device layers.

################################################################################
# 1.9 — CORE BOOT IMAGE IDENTITY ANCHORS
################################################################################


Boot partition namespaces:

| Path | Type | Mode | Owner | SELinux context | Runtime size | Runtime timestamp |
|---|---|---|---|---|---:|---|
| /dev/block/by-name | directory | drwxr-xr-x | root:root | u:object_r:block_device:s0 | 880 bytes | 2026-07-07 23:21 |
| /dev/block/platform/bootdevice/by-name | directory | drwxr-xr-x | root:root | u:object_r:block_device:s0 | 840 bytes | 2026-07-07 23:21 |

Boot-related by-name entries:
Generic namespace             : /dev/block/by-name/boot, /dev/block/by-name/boot_para
Platform namespace            : /dev/block/platform/bootdevice/by-name/boot, /dev/block/platform/bootdevice/by-name/boot_para

A/B slot properties:
ro.boot.slot_suffix           : <empty>
ro.boot.slot                  : <empty>
A/B slot identity             : OPEN_BOUNDARY
Comment                       : Empty property values do not independently prove a non-A/B partition layout.

Boot partition namespace identity:

| Path | Object type | Symlink target | Mode | Owner | SELinux context | Inode |
|---|---|---|---|---|---|---:|
| /dev/block/by-name/boot | symbolic link | /dev/block/mmcblk0p28 | lrwxrwxrwx | root:root | u:object_r:block_device:s0 | 256 |
| /dev/block/platform/bootdevice/by-name/boot | symbolic link | /dev/block/mmcblk0p28 | lrwxrwxrwx | root:root | u:object_r:boot_block_device:s0 | 255 |
| /dev/block/mmcblk0p28 | block device | — | brw------- | root:root | u:object_r:boot_block_device:s0 | 254 |

Generic symlink size          : 21 bytes
Platform symlink size         : 21 bytes
Generic symlink access time   : 2026-07-10 01:32:32.280010769 -0500
Platform symlink access time  : 2026-07-10 01:32:42.840010769 -0500
Symlink modify time           : 2026-07-07 23:21:13.076000000 -0500
Symlink change time           : 2026-07-07 23:21:13.076000000 -0500
Block-device access time      : 2026-07-07 23:21:13.076000000 -0500
Block-device modify time      : 2026-07-07 23:21:13.076000000 -0500
Block-device change time      : 2026-07-07 23:21:16.604000000 -0500

Namespace relationship:
Common resolved partition     : /dev/block/mmcblk0p28
Symlink-object identity       : separate objects
Resolved block-device identity: common object
SELinux layer difference      : generic symlink uses block_device; platform symlink and target use boot_block_device

Boot partition block identity:
Partition name                : boot
Partition device              : /dev/block/mmcblk0p28
Sysfs target                  : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001/block/mmcblk0/mmcblk0p28
Device type                   : partition
Partition number              : 28
Block-device major:minor      : 179:28
Read-only state               : 1
Start sector                  : 933888
Sector count                  : 65536
Logical block size            : 512 bytes
Physical block size           : 512 bytes
Start byte offset             : 478,150,656 bytes
Partition size                : 33,554,432 bytes
Partition size                : 32 MiB
Final sector                  : 999423
End byte offset, exclusive    : 511,705,088 bytes

Boot image header identity:
Header magic                  : ANDROID!
Header version                : 2
Header size                   : 1,660 bytes
Header size hex               : 0x0000067c
Page size                     : 2,048 bytes
Kernel binary size            : 9,246,082 bytes
Kernel binary size hex        : 0x008d1582
Kernel base                   : 0x40000000
Kernel load address           : 0x40080000
Kernel offset                 : 0x00080000
Ramdisk size                  : 923,597 bytes
Ramdisk size hex              : 0x000e17cd
Ramdisk load address          : 0x51b00000
Ramdisk offset                : 0x11b00000
Second-stage size             : 0 bytes
Second-stage load address     : 0x40f00000
Second-stage offset           : 0x00f00000
Second-stage state            : not populated
Tags load address             : 0x47880000
Tags offset                   : 0x07880000
DTB size                      : 89,609 bytes
DTB size hex                  : 0x00015e09
DTB load address              : 0x0000000047880000
DTB offset                    : 0x07880000
Recovery-DTBO size            : 0 bytes
Recovery-DTBO offset          : 0x0000000000000000
Recovery-DTBO state           : not populated
Board-name field              : 16 zero bytes
Board-name value              : <empty>
OS version raw                : 0x14000183
OS version encoded            : 10.0.0
OS patch level encoded        : 2024-03
Boot image SHA1 field         : c6ccf000f6513dafd9a2018c87d799922348887c
Boot image ID padding         : 12 zero bytes

Boot header command line:
Primary cmdline field size    : 512 bytes
Primary cmdline value         : bootopt=64S3,32N2,64N2 buildvariant=user
Primary cmdline length        : 40 bytes
Primary cmdline terminator    : NUL at image offset 0x00000068
Primary cmdline trailing bytes: 471 zero bytes
Extra cmdline field size      : 1,024 bytes
Extra cmdline value           : <empty>
Extra cmdline raw state       : 1,024 zero bytes

Boot image component layout:

| Component | Start offset | Exact size | Exact end | Page-aligned end | Padding size | Padding SHA256 | Padding state |
|---|---:|---:|---:|---:|---:|---|---|
| Header | 0x00000000 | 1,660 bytes | 0x0000067c | 0x00000800 | 388 bytes | 8de76ee7eb6b32c307d4a46a43ac55bc15b917e2a24d36c3d001878a97fd39d6 | zero-filled |
| Kernel field | 0x00000800 | 9,246,082 bytes | 0x008d1d82 | 0x008d2000 | 638 bytes | 72e5b1f5ddd379c04a98a022643ab85ff0c27dbd474f02d88dd2dcae138d0786 | zero-filled |
| Ramdisk | 0x008d2000 | 923,597 bytes | 0x009b37cd | 0x009b3800 | 51 bytes | 8e8fe47e4a33b178bf0433d8050cb0ad7ec323fbdeeab3ecfd857b4ce1805b7a | zero-filled |
| Android DT table | 0x009b3800 | 89,609 bytes | 0x009c9609 | 0x009c9800 | 503 bytes | 561dc0533eb1201eb4bdc0a0f90a89929c8ab2ba5fd38d82474b18d3cdaec5fb | zero-filled |

Header-described image size:
Page-aligned image end        : 0x009c9800
Exact byte size               : 10,262,528 bytes
SHA256                        : 3b6ff0a39be25e7f7c18996e52e3c600e34d7c2bc740d4d6988370b73ce4a526

Boot component hashes:

| Object | Exact range or size | SHA256 |
|---|---|---|
| Header page | 0x00000000-0x000007ff / 2,048 bytes | 0a2d1479f5dac642ebe3104b9fcf04262e86c72143fd61bc4ac2f738f6c3c7bb |
| Kernel field | 0x00000800-0x008d1d81 / 9,246,082 bytes | e76555df4ce2dbc5dea2113468548f1597c49d1a5871d0d38dea199477d19522 |
| Kernel gzip stream | 9,156,537 bytes | dadfdd23fcd2897f9602aa3def8f381cf0e323424720cc193764ba48f2b7242e |
| Kernel-appended DTB | 89,545 bytes | 16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6 |
| Ramdisk gzip stream | 923,597 bytes | 0a14137bcfff4de968f5c4ac09c04c55ccc97dc70b3e10421f4a10dfb74911ad |
| Android DT table | 89,609 bytes | 41115a867807ddabf54707acc5b16d8c93534026533bb109c4faedca4aa4a3af |
| DT-table embedded DTB | 89,545 bytes | 16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6 |
| VBMeta object | 1,472 bytes | 2e2ec8c3939f75182f4084730d1a364827af22dccbb3d868cb0024ae16397a5d |
| AVB public key | 520 bytes | 75521791cbf8e91b4ab790ea29c551776092e9ed76d9b5722d590fb493d3db1d |
| AVB footer | 64 bytes | a89ebf879001b529f74403525f6de65499cb2212d897f4cbbe06f6a808bd3c88 |
| Complete boot partition | 33,554,432 bytes | fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851 |

Android boot image ID verification:
Stored SHA-1                 : c6ccf000f6513dafd9a2018c87d799922348887c
Recomputed SHA-1             : c6ccf000f6513dafd9a2018c87d799922348887c
Verification result          : exact match

Kernel field composition:
Kernel field start            : 0x00000800
Kernel gzip stream size       : 9,156,537 bytes
Kernel gzip stream size hex   : 0x008bb7b9
Kernel-appended DTB start     : 0x008bbfb9
Kernel-appended DTB size      : 89,545 bytes
Kernel-appended DTB size hex  : 0x00015dc9
Kernel field exact end        : 0x008d1d82
Composition                   : gzip-compressed ARM64 kernel followed by raw flattened device tree
Size equation                 : 9,156,537 + 89,545 = 9,246,082 bytes

Kernel gzip identity:
Gzip header bytes             : 1f 8b 08 00 00 00 00 00 02 03
Compression method            : deflate
Gzip timestamp                : 0
Gzip extra flags              : 0x02
Gzip OS field                 : 0x03
Gzip trailer offset           : 0x008bbfb1
CRC32 raw bytes               : 91 af 27 e9
CRC32 decoded                 : 0xe927af91
ISIZE raw bytes               : 14 b8 8a 01
ISIZE decoded                 : 25,868,308 bytes
Decompressed size             : 25,868,308 bytes
Decompressed SHA256           : ba8bdc71e1ef8936772529ac91d73448883d2e7b8d7fb7562363bbbabf4f116b
Gzip integrity state          : CRC32 and ISIZE accepted by decompressor

Decompressed kernel image identity:
Payload format                : Linux ARM64 Image
Initial 64-byte header        : 00 c0 47 14 00 00 00 00 00 00 08 00 00 00 00 00 00 60 eb 01 00 00 00 00 0a 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 41 52 4d 64 00 00 00 00
code0                        : 0x1447c000
code1                        : 0x00000000
text_offset                  : 0x0000000000080000
image_size                   : 0x0000000001eb6000
image_size                   : 32,202,752 bytes
flags                        : 0x000000000000000a
reserved fields              : zero
Image magic                  : ARMd
Image magic numeric          : 0x644d5241

Kernel terminal strings:
Terminal string 0             : options
Terminal string 1             : mt6370_pmu
Terminal string 2             : mt6370_typec

Ramdisk gzip identity:
Gzip header bytes             : 1f 8b 08 00 00 00 00 00 02 ff
Compression method            : deflate
Gzip timestamp                : 0
Gzip extra flags              : 0x02
Gzip OS field                 : 0xff
Gzip trailer offset           : 0x009b37c5
CRC32 raw bytes               : 2d b8 7c 41
CRC32 decoded                 : 0x417cb82d
ISIZE raw bytes               : a0 17 10 00
ISIZE decoded                 : 1,054,624 bytes
Decompressed size             : 1,054,624 bytes
Decompressed SHA256           : 772a762bc3bb2ae302642fa6be64408a5ae0133dcd5a38ea842ed792f8d3fc59
Gzip integrity state          : CRC32 and ISIZE accepted by decompressor

Decompressed ramdisk identity:
Payload format                : ASCII cpio newc archive
Archive magic                 : 070701
Initial 64-byte header        : 30 37 30 37 30 31 30 30 30 34 39 33 65 30 30 30 30 30 34 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30 30

Android DT table identity:
Table start                   : 0x009b3800
Table magic                   : 0xd7b7ab1e
Table total size              : 89,609 bytes
Table total size hex          : 0x00015e09
Table header size             : 32 bytes
Table entry size              : 32 bytes
Table entry count             : 1
Table entries offset          : 0x00000020
Table page size               : 2,048 bytes
Table version                 : 0

Android DT table entry:
Entry start                   : 0x009b3820
DTB offset                    : 0x00000040
DTB size                      : 89,545 bytes
DTB size hex                  : 0x00015dc9
Entry ID                      : 0
Entry revision                : 0
Entry custom field 0          : 0
Entry custom field 1          : 0
Entry custom field 2          : 0
Entry custom field 3          : 0

Embedded flattened device tree:
DTB start                     : 0x009b3840
FDT magic                     : 0xd00dfeed
FDT total size                : 89,545 bytes
FDT total size hex            : 0x00015dc9
Structure block offset        : 0x00000038
Strings block offset          : 0x00013488
Memory reservation offset     : 0x00000028
FDT version                   : 17
Last compatible version       : 16
Boot CPU ID                   : 0
FDT exact end                 : 0x009c9609

Device-tree duplication:
Kernel-appended DTB start     : 0x008bbfb9
DT-table embedded DTB start   : 0x009b3840
Kernel-appended DTB SHA256    : 16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6
DT-table embedded DTB SHA256  : 16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6
Byte identity                 : exact match
Representation 0              : raw FDT appended to the kernel field
Representation 1              : same raw FDT inside a one-entry Android DT table

Embedded DTB terminal strings:
Terminal string 0             : dynamic_options
Terminal string 1             : mt6370_pmu
Terminal string 2             : mt6370_typec

AVB footer identity:
Footer offset                 : 0x01ffffc0
Footer size                   : 64 bytes
Footer magic                  : AVBf
Footer version major          : 1
Footer version minor          : 0
Original image size           : 0x009c9800
Original image size           : 10,262,528 bytes
VBMeta offset                 : 0x009ca000
VBMeta size                   : 0x000005c0
VBMeta size                   : 1,472 bytes
Reserved footer bytes         : zero

AVB layout:
Header-described image end    : 0x009c9800
VBMeta start                  : 0x009ca000
Inter-object gap              : 2,048 bytes
Inter-object gap SHA256       : e5a00aa9991ac8a5ee3109844d84a55583bd20572ad3ffcd42792f3c36b183ad
Inter-object gap state        : zero-filled
VBMeta exact end              : 0x009ca5c0
AVB footer start              : 0x01ffffc0
Post-VBMeta region size       : 23,288,320 bytes
Post-VBMeta region SHA256     : bb34bb62aad5a0c16e28e37f79b314c5c85b15ae00895e722c7ae297986e33ca
Post-VBMeta region state      : zero-filled

VBMeta header identity:
VBMeta magic                  : AVB0
Required libavb major version : 1
Required libavb minor version : 0
Header size                   : 256 bytes
Authentication block size     : 320 bytes
Auxiliary block size          : 896 bytes
Algorithm type                : 1
Algorithm                     : SHA256_RSA2048
Rollback index                : 0
Flags                         : 0
Rollback-index location       : 0
Release string                : avbtool 1.1.0

VBMeta authentication block:
Authentication block start    : 0x009ca100
Hash offset                   : 0x00000000
Hash size                     : 32 bytes
Signature offset              : 0x00000020
Signature size                : 256 bytes
Stored authentication hash    : bc3bbf9d4d89559bff7fd999a973acfefa76fb994f0784bf81367d787a70337f
Recomputed authentication hash: bc3bbf9d4d89559bff7fd999a973acfefa76fb994f0784bf81367d787a70337f
Authentication hash result    : exact match
RSA signature verification    : OPEN_BOUNDARY

VBMeta auxiliary block:
Auxiliary block start         : 0x009ca240
Descriptor offset             : 0x00000000
Descriptor size               : 360 bytes
Public-key offset             : 0x00000168
Public-key size               : 520 bytes
Public-key absolute start     : 0x009ca3a8
Public-key SHA256             : 75521791cbf8e91b4ab790ea29c551776092e9ed76d9b5722d590fb493d3db1d
Public-key metadata offset    : 0x00000370
Public-key metadata size      : 0 bytes

AVB hash descriptor:
Descriptor tag                : 2
Descriptor bytes following    : 184
Descriptor total size         : 200 bytes
Protected image size          : 0x009aa800
Protected image size          : 10,135,552 bytes
Hash algorithm                : sha256
Partition name                : boot
Partition-name length         : 4
Salt length                   : 32 bytes
Digest length                 : 32 bytes
Descriptor flags              : 0
Salt                          : e72556c30e53cd12e555f26883ebf923790b28e54cebd8114110b7d9a3cce1b0
Stored digest                 : 70067c1a25dd5b13d46b2f80eaad0a59319d1c0fc8e3a85233dde522dd20ecdd
Recomputed declared-range digest: 2710688ef8745b475ff3930176a3814d4bb8ebb4da5dc5fb5b2b42c9c60c5769
Recomputed full-image digest  : 61ec0d26624604cbcf64a8b1823af9b2f3258c7eefb1b2e64643daacca71bd05
Stored digest result          : mismatch with both recomputed ranges

AVB property descriptors:

| Property | Value |
|---|---|
| com.android.build.boot.os_version | 10 |
| com.android.build.boot.security_patch | 2019-06-06 |

Boot version identity layers:
Boot-header OS version        : 10.0.0
AVB OS-version property       : 10
Boot-header patch level       : 2024-03
AVB security-patch property   : 2019-06-06
Patch-level relationship      : distinct encoded layers

AVB protected-range boundary:
Protected-range end           : 0x009aa800
Boundary alignment            : 2,048-byte page boundary
Boundary location             : inside compressed ramdisk data
Offset into ramdisk           : 886,784 bytes
Ramdisk bytes after boundary  : 36,813 bytes
Bytes preceding boundary      : fc 93 07 59 3e 7c 49 71 6c 30 1c aa 57 77 d3 cf 6b 43 f6 12 8f dd 32 94 ec c8 da 22 ba e4 1c b3 a8 4f e3 8b b2 55 f4 cd 0d bc 63 8e 46 7d 5b 85 99 13 6f 4e 10 10 90 7e fa de 87 21 5a 64 b3 f3
Bytes following boundary      : e0 8d 9d 6d 52 be 23 8b b3 42 69 33 f5 ca 5c 64 24 c3 1c 9d 04 e3 a1 d4 8c bb 04 fc 79 56 86 67 75 ce 31 59 27 0a c6 b3 9e 73 26 4e d0 47 c7 1d ee 0c ca 1b f0 59 7e 0c dd a8 83 c8 59 40 96 0b
Boundary data state          : nonzero compressed data on both sides

AVB protected-range discrepancy:
Protected image size          : 10,135,552 bytes
Footer original image size    : 10,262,528 bytes
Difference                    : 126,976 bytes
Remaining ramdisk data        : 36,813 bytes
Ramdisk padding               : 51 bytes
Android DT table              : 89,609 bytes
DT-table padding              : 503 bytes
Difference equation           : 36,813 + 51 + 89,609 + 503 = 126,976 bytes
Mismatch cause                : OPEN_BOUNDARY

Canonical build constants:
BOARD_BOOTIMG_HEADER_VERSION  : 2
BOARD_KERNEL_BASE             : 0x40000000
BOARD_KERNEL_OFFSET           : 0x00080000
BOARD_RAMDISK_OFFSET          : 0x11b00000
BOARD_TAGS_OFFSET             : 0x07880000
BOARD_DTB_OFFSET              : 0x07880000
BOARD_KERNEL_PAGESIZE         : 2048
BOARD_FLASH_BLOCK_SIZE        : 131072
BOARD_OS_VERSION              : 10.0.0
BOARD_OS_PATCH_LEVEL          : 2024-03

Open boundaries:

| Boundary | Known state | Unresolved state |
|---|---|---|
| A/B slot identity | ro.boot.slot_suffix and ro.boot.slot are empty | Partition-layout classification is not closed by empty properties alone |
| AVB hash descriptor | Stored digest differs from both the current declared-range digest and current full-image digest; declared boundary cuts active ramdisk data | Origin and intended protected-byte set |
| AVB RSA signature | VBMeta authentication hash is internally consistent and the embedded public key is identified | Independent RSA signature verification |
Current mismatch mechanism          : RESOLVED — boot ramdisk/init was patched by Magisk
Original stock descriptor validity  : OPEN — requires preserved pre-Magisk boot evidence


################################################################################
# 1.10 — FIRMWARE EXECUTION DOMAIN IDENTITY
################################################################################

Subsection control:
Database class                 : HARDWARE DATABASE / FIRMWARE EXECUTION-DOMAIN IDENTITY
Device                         : LOKMAT APPLLP 5 MAX (C17S)
Board                          : WP_C17S_PIX_TFT_D4
Physical SoC                   : MediaTek MT6762V/WB
Software BSP                   : MT6765
Scope                          : Root execution-domain identity, firmware location, processor class, privilege level, runtime binding, memory identity, IPC identity, and firmware-container identity
Excluded                       : Full firmware disassembly, full command/protocol decode, replacement/build procedure, update procedure, and userspace implementation design
Status                         : CURRENT EVIDENCE SET INTEGRATED — OPEN BOUNDARIES RETAINED

Capture-state boundary:
Live privilege context         : uid=0(root) gid=0(root) groups=0(root) context=u:r:magisk:s0
Bootloader state               : unlocked
Root state                     : Magisk active
AVB / verity state             : disabled / verified boot state orange
SELinux state                  : permissive
Research-state handling        : Runtime binding and live activity are valid live witnesses; stock filesystem metadata is not inferred from research-state pseudo-files

Source authority used in this subsection:
Physical silicon identity      : BROM / preloader acquisition identity and live silicon presentation
Boot-stage identity            : raw partition identity, preserved boot logs, and exact binary-header decode
Runtime platform identity      : live devicetree, platform bus, driver links, procfs, sysfs, /proc/interrupts, and /proc/iomem
Firmware binary identity       : exact live block-device reads, SHA256, cmp, byte offsets, and embedded strings
Android property identity      : live properties plus literal property-source lines
External coprocessor identity  : live AP-side control interface plus exact Android-side firmware artifact identity

Execution-domain map:

| Domain | Processor / execution class | Privilege / world | Firmware location | Current classification |
|---|---|---|---|---|
| BROM | MediaTek silicon boot ROM | immutable on-silicon boot domain | physical SoC ROM | VERIFIED_STATIC_SILICON_DOMAIN |
| Preloader | MediaTek preloader | pre-kernel boot firmware | eMMC boot areas mmcblk0boot0 / mmcblk0boot1 | VERIFIED_BOOT_FIRMWARE_DOMAIN |
| LK / LK2 | Little Kernel, AArch32 | bootloader runtime | redundant LK / LK2 partitions | VERIFIED_BOOTLOADER_DOMAIN |
| ATF BL31 | ARM Trusted Firmware | AArch64 EL3 | ATF reserved runtime region | VERIFIED_SECURE_MONITOR_DOMAIN |
| MTEE | MediaTek MTEE kernel/container | AArch64 secure-world EL1 | tee1 / tee2 partitions plus secure runtime memory | VERIFIED_SECURE_WORLD_DOMAIN |
| SCP | MediaTek TinySys CM4_A | secondary low-power coprocessor | scp1 / scp2 partitions; SCP reserved memory | DECLARED_POPULATED_ACTIVE |
| SSPM | MediaTek TinySys SSPM | secondary low-power power-management processor | sspm_1 / sspm_2 partitions; SSPM reserved memory | DECLARED_POPULATED_ACTIVE |
| Linux kernel | ARM64 Linux 4.9.190+ | AArch64 non-secure EL1 | boot partition kernel payload | VERIFIED_PRIMARY_OS_KERNEL_DOMAIN |
| nRF52832 | Nordic ARM Cortex-M4F | independent external coprocessor | nRF internal flash | VERIFIED_EXTERNAL_COPROCESSOR_DOMAIN |

################################################################################
# BROM EXECUTION DOMAIN
################################################################################

BROM identity:
Domain                         : MediaTek Boot ROM
Storage                        : immutable on-silicon ROM
Physical SoC                   : MT6762V/WB
HW Code                        : 0x0766
HW Subcode                     : 0x8a00
HW Version                     : 0xca00
ME_ID                          : 4B5CF18EF37D514A6256199E0BBD5E6A
SOC_ID                         : 6044C8E6000D6C384827B28853120093E04F3C815E8A50E6D20D6A6C24DF1B11
Execution position             : first immutable firmware stage
Classification                 : VERIFIED_STATIC_SILICON_DOMAIN

BROM / live silicon cross-witness:
/proc/cpuinfo Hardware         : MT6762V/WB
/proc/chip hw_code             : 0766
/proc/chip hw_subcode          : 8A00
/proc/chip hw_ver              : CA00
Cross-witness result           : BROM acquisition identity agrees with live silicon register identity

################################################################################
# PRELOADER EXECUTION DOMAIN
################################################################################

Preloader identity:
Domain                         : MediaTek preloader
Primary location               : /dev/block/mmcblk0boot0
Redundant location             : /dev/block/mmcblk0boot1
GPT partition                  : none
Storage class                  : eMMC hardware boot areas
Execution position             : after BROM, before LK
Classification                 : VERIFIED_BOOT_FIRMWARE_DOMAIN

Preloader silicon / segment anchors:
FAB0 Register                  : 0x6762
FAB_CODE                       : 0x2
Segment Code                   : 0x8
PROJECT_CODE                   : 0x0
RW_STA                         : 0x0
CTL                            : 0x0
DCM                            : 0x4
DATE_CODE_YY                   : 0x1
DATE_CODE_WW                   : 0xA
HT22 Data                      : 2, 0x1, 0x1 done

Bootprof timing records:

| Capture | Preloader time | LK time | Logo time | Classification |
|---|---:|---:|---:|---|
| Prior database capture | 1,074 ms | 8,057 ms | 999 ms | PER_BOOT_VARIANT |
| Retained 1,128 ms sample / prior 1.7 draft | 1,128 ms | 8,071 ms | 998 ms | PER_BOOT_VARIANT |
| Current Section 1 live capture | 1,130 ms | 8,086 ms | 999 ms | PER_BOOT_VARIANT |

Timing boundary:
Bootprof values                : boot-instance measurements
Fixed firmware identity use    : none

################################################################################
# LK / LK2 EXECUTION DOMAIN
################################################################################

LK identity:
Domain                         : Little Kernel bootloader
Version                        : 1.2.3
Architecture                   : AArch32
Load-region base               : 0x48000000
Load-region size               : 0x00400000
Load-region inclusive end      : 0x483fffff
Partition model                : redundant LK and LK2 partitions
Execution position             : after preloader, before ATF/Linux handoff
Classification                 : VERIFIED_BOOTLOADER_DOMAIN

LK platform handoff identity:
Boot-image DT selection        : androidboot.dtb_idx=0
DTBO selection                 : androidboot.dtbo_idx=0
Boot hardware token            : androidboot.hardware=mt6765
Physical runtime token         : ro.boot.hardware=mt6762 / ro.hardware=mt6762
Layer classification           : BOOTARG_RUNTIME_HARDWARE_TOKEN_SPLIT_CONFIRMED

################################################################################
# ATF BL31 EXECUTION DOMAIN
################################################################################

ATF identity:
Domain                         : ARM Trusted Firmware BL31
Execution level                : AArch64 EL3
Role                           : secure monitor, PSCI provider, secondary CPU bring-up
PSCI compatible                : arm,psci-1.0
PSCI method                    : smc
CPU topology                   : 2 clusters / 8 cores
Secondary CPU entrypoint       : 0x47d810d8
Entrypoint source              : ATF runtime records preserved in expdb
Classification                 : VERIFIED_SECURE_MONITOR_DOMAIN

ATF secondary CPU bring-up scope:
Primary boot CPU               : not represented in secondary bring-up records
Secondary MPIDRs               : 0x001, 0x002, 0x003, 0x100, 0x101, 0x102, 0x103
Entrypoint for all listed CPUs : 0x47d810d8

ATF reserved runtime memory:
DT node                        : /reserved-memory/mblock-4-atf-reserved
Compatible                     : mediatek,atf-reserved<NUL>
Base                           : 0x47d80000
Size                           : 0x00050000
Inclusive end                  : 0x47dcffff
no-map                         : present
Entrypoint offset              : 0x000010d8
Entrypoint containment         : 0x47d810d8 is inside 0x47d80000-0x47dcffff

ATF log memory:
DT node                        : /reserved-memory/mblock-5-atf-log-reserved
Compatible                     : mediatek,atf-log-reserved<NUL>
Base                           : 0xbffbf000
Size                           : 0x00040000
Inclusive end                  : 0xbfffefff
no-map                         : present

ATF logger declaration:
DT node                        : /atf_logger
Compatible                     : mediatek,atf_logger<NUL>
Interrupt cells                : 00000000 000000f7 00000001

################################################################################
# TEE / MTEE EXECUTION DOMAIN
################################################################################

MTEE identity:
TEE type                       : MediaTek MTEE
Confirmation string            : sapu-mtee-shm
Component model                : ATF BL31 + MTEE kernel
TEE architecture               : AArch64 secure-world EL1 runtime
tee1 partition size            : 5 MiB
tee2 partition size            : 11 MiB
Classification                 : VERIFIED_SECURE_WORLD_DOMAIN

Secure-world declaration surface:
DT root nodes                  : mobicore, utos, utos_tester, tkcore
Layer handling                 : declarations retained as secure-world interface identity; individual service behavior is outside this subsection

################################################################################
# LINUX KERNEL EXECUTION DOMAIN
################################################################################

Linux kernel identity:
Kernel                         : Linux 4.9.190+
Build instance                 : #1 SMP PREEMPT
Architecture                   : AArch64
Android base                   : Android 10 / API 29
Kernel load address            : 0x40080000
Kernel image format            : Linux ARM64 Image
Decompressed image size        : 32,202,752 bytes
Primary execution role         : Android non-secure operating-system kernel
Classification                 : VERIFIED_PRIMARY_OS_KERNEL_DOMAIN

Kernel firmware-class path:
Command-line value             : firmware_class.path=/vendor/firmware
SCP / SSPM source relationship : dedicated block partitions are separately verified; filename matches under /vendor/firmware are not promoted to SCP/SSPM firmware

################################################################################
# MEDIATEK TINYSYS FRAMEWORK IDENTITY
################################################################################

Compiled TinySys support:
CONFIG_MTK_TINYSYS_SCP_SUPPORT : y
CONFIG_MTK_TINYSYS_SSPM_SUPPORT: y
CONFIG_MTK_TINYSYS_SSPM_PLT_SUPPORT : not set
CONFIG_MTK_AUDIO_SCP_SPKPROTECT_SUPPORT : not set
CONFIG_MTK_CMDQ_MBOX           : y
Classification                 : MTK_TINYSYS_FRAMEWORK_COMPILED

Generic Remoteproc / RPMsg config block:
Remoteproc menu header         : present
Rpmsg menu header              : present
CONFIG_STE_MODEM_RPROC         : not set
CONFIG_REMOTEPROC assignment   : not found in complete text search
CONFIG_RPMSG assignment        : not found in complete text search
Subordinate RPMsg assignments  : not found in complete text search
Classification                 : NO_GENERIC_REMOTEPROC_RPMSG_CONFIG_ASSIGNMENT_FOUND

Standard live framework-path search:

| Path | Result | Command status | Classification |
|---|---|---:|---|
| /sys/class/remoteproc | No such file or directory | 1 | ABSENT_IN_TESTED_PATH |
| /sys/class/rpmsg | No such file or directory | 1 | ABSENT_IN_TESTED_PATH |
| /sys/kernel/debug/remoteproc | No such file or directory | 1 | ABSENT_IN_TESTED_PATH |
| /sys/kernel/debug top-level grep scp/sspm/tinysys/ipi/mbox | no match | 1 | NO_MATCH_IN_TESTED_NAMESPACE |

Framework boundary:
Generic Remoteproc/RPMsg path  : not exposed in tested live namespaces
Active implementation path     : MediaTek-specific platform drivers, TinySys partitions, IPI/mailbox interrupts, reserved memory, and procfs control surfaces
Compile-out claim              : not asserted beyond the exact config/path evidence above

Matching platform devices discovered:
10001000.scpsys
10211000.mcupm_sram2
10213000.mcupm_sram3
10216000.mcupm_reg
10217000.mcupm_sram0
10218000.mcupm_sram1
10440000.sspm
10500000.scp
mt-scpdvfs
mt_soc_pcm_dl1_scp_spk
mt_soc_pcm_voice_scp
scp_dvfs

Matching platform drivers discovered:
scp
scpdvfs
scpsys
sspm

################################################################################
# SCP EXECUTION DOMAIN
################################################################################

SCP canonical identity:
Domain                         : MediaTek Sensor / Control Processor
Firmware framework             : MediaTek TinySys
Processor identity             : CM4_A
Processor architecture proof   : FreeRTOS ARM_CM4F source-path string
RTOS                           : FreeRTOS
Platform device                : 10500000.scp
Bound driver                   : scp
DT node                        : /scp@10500000
Primary role                   : secondary low-power sensor/control execution domain
Classification                 : DECLARED_POPULATED_ACTIVE

SCP runtime binding:
Driver link                    : ../../../bus/platform/drivers/scp
OF-node link                   : ../../../firmware/devicetree/base/scp@10500000
uevent DRIVER                  : scp
uevent OF_NAME                 : scp
uevent OF_FULLNAME             : /scp@10500000
uevent OF_COMPATIBLE_0         : mediatek,scp
uevent OF_COMPATIBLE_N         : 1
uevent MODALIAS                : of:NscpT<NULL>Cmediatek,scp

SCP platform target directory:
Path                           : /sys/devices/platform/10500000.scp
Child objects                  : driver, driver_override, modalias, of_node, power, subsystem, uevent
Driver-specific root attrs     : none observed beyond standard platform-device objects

SCP driver directory:
Path                           : /sys/bus/platform/drivers/scp
Bound-device link              : 10500000.scp -> ../../../../devices/platform/10500000.scp
Control files                  : bind, uevent, unbind
Control-file mode              : --w------- root:root

SCP devicetree string properties:
compatible raw                 : 6d 65 64 69 61 74 65 6b 2c 73 63 70 00
compatible decoded             : mediatek,scp<NUL>
core_1 raw                     : 65 6e 61 62 6c 65 00
core_1 decoded                 : enable<NUL>
status raw                     : 6f 6b 61 79
status decoded                 : okay
status length                  : 4 bytes
status NUL termination         : absent
Classification                 : SCP_STATUS_STRING_NON_NUL_TERMINATED

SCP register windows:

| Index | Raw address cells | Raw size cells | Base | Size | Inclusive end |
|---:|---|---|---:|---:|---:|
| 0 | 00000000 10500000 | 00000000 00040000 | 0x10500000 | 0x00040000 | 0x1053ffff |
| 1 | 00000000 105c0000 | 00000000 00003000 | 0x105c0000 | 0x00003000 | 0x105c2fff |
| 2 | 00000000 105c4000 | 00000000 00001000 | 0x105c4000 | 0x00001000 | 0x105c4fff |
| 3 | 00000000 105d4000 | 00000000 00006000 | 0x105d4000 | 0x00006000 | 0x105d9fff |

SCP local SRAM declaration:
Property                       : scp_sramSize
Raw value                      : 00040000
Decoded size                   : 0x00040000 / 262,144 bytes

SCP interrupt declaration:
Raw cells                      : 00000000 000000c0 00000004
Raw type                       : 0
Raw hardware number            : 192
Raw flags                      : 4
Runtime Linux IRQ              : 183
Runtime label                  : SCP A IPC2HOST
Per-CPU counters               : 3 1 0 0 1 1 0 1
Runtime activity               : nonzero
Classification                 : DECLARED_AND_RUNTIME_REGISTERED

SCP /proc/iomem reconciliation:
10001000-10001fff              : /scpsys@10001000
10500000-1053ffff              : /scp@10500000
105c0000-105c2fff              : /scp@10500000
105c4000-105c4fff              : /scp@10500000
105d4000-105d9fff              : /scp@10500000
DT / iomem relationship        : all four SCP register windows match exactly

SCP fixed reserved-memory identity:
DT node                        : /reserved-memory/mblock-12-SCP-reserved
compatible raw                 : mediatek,SCP-reserved<NUL>
name raw                       : mblock-12-SCP-reserved<NUL>
Base                           : 0x9f900000
Size                           : 0x00600000 / 6,291,456 bytes
Inclusive end                  : 0x9fefffff
no-map                         : present
Classification                 : VERIFIED_FIXED_RESERVED_MEMORY

SCP firmware load anchor:
Firmware load address          : 0x9f900000
Retained firmware-size anchor  : 0x0003b800 / 243,712 bytes
Size-source state              : retained verified subsection value; principal SCP record length was not re-read directly in the current container walk

SCP dynamic shared-memory declaration:
DT node                        : /reserved-memory/reserve-memory-scp_share
compatible                     : mediatek,reserve-memory-scp_share<NUL>
name                           : reserve-memory-scp_share<NUL>
Requested size                 : 0x00300000 / 3,145,728 bytes
Alignment                      : 0x01000000 / 16,777,216 bytes
Allocation-range base          : 0x40000000
Allocation-range span          : 0x50000000
Allocation window              : 0x40000000-0x8fffffff
no-map                         : present
status property                : absent
Final allocated runtime base   : OPEN_BOUNDARY
Classification                 : DYNAMIC_RESERVED_MEMORY_DECLARATION

SCP procfs control surface:
Top-level matching procfs entry: scp_dvfs
Other top-level scp/sspm/tinysys/ipi/mbox matches : none
Directory                      : /proc/scp_dvfs
Directory context              : u:object_r:proc:s0

| File | Mode | Owner | Read result |
|---|---|---|---|
| pmicw_mode_debug | -rw-rw-r-- | root:root | pmicw_mode_debug_flag is not configured by command yet. |
| scp_dvfs_ctrl | -rw-rw-r-- | root:root | DVFS state and feature/sensor table emitted |
| scp_dvfs_sleep | -rw-rw-r-- | root:root | SCP sleep is not configured by command yet. |
| scp_dvfs_state | -rw-rw-r-- | root:root | scp status: sleep mode |

SCP live power / frequency state:
SCP reported state             : sleep mode
SCP DVFS                       : ON
Current frequency              : 250 MHz
Expected frequency             : 250 MHz
Current / expected relation    : exact match
Forced sleep command state     : not configured
PMIC wrapper debug command     : not configured

SCP feature table:

| Feature ID | Frequency field | Enable | Semantic classification |
|---:|---:|---:|---|
| 0 | 75 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 1 | 356 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 2 | 0 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 3 | 47 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 4 | 26 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 5 | 0 | 1 | ANOMALY_RETAINED / STRUCT_OPEN |
| 6 | 200 | 0 | STRUCT_OPEN / UNIT_OPEN |
| 7 | 0 | 0 | STRUCT_OPEN / UNIT_OPEN |

SCP emitted sensor records before trailing default slots:

| Emission order | Sensor ID | Frequency field | Enable |
|---:|---:|---:|---:|
| 1 | 0 | 2 | 0 |
| 2 | 1 | 2 | 0 |
| 3 | 2 | 2 | 0 |
| 4 | 3 | 2 | 0 |
| 5 | 4 | 2 | 0 |
| 6 | 7 | 2 | 0 |
| 7 | 5 | 2 | 0 |
| 8 | 18 | 2 | 0 |
| 9 | 16 | 2 | 0 |
| 10 | 17 | 2 | 0 |
| 11 | 23 | 2 | 0 |
| 12 | 43 | 3 | 0 |
| 13 | 41 | 2 | 0 |
| 14 | 28 | 2 | 0 |
| 15 | 29 | 2 | 0 |
| 16 | 35 | 3 | 0 |
| 17 | 41 | 2 | 0 |
| 18 | 26 | 2 | 0 |
| 19 | 36 | 3 | 0 |

SCP sensor-table anomalies:
Duplicate emitted sensor ID    : 41 appears twice with identical freq=2, enable=0
Trailing default records       : repeated sensor id=0, freq=0, enable=0 rows emitted for remaining table slots
Physical-sensor interpretation : not assigned from this table alone
Frequency-field unit           : UNIT_OPEN
Sensor-ID semantic map         : STRUCT_OPEN

SCP firmware partitions:

| Partition | By-name path | Block node | GPT index | Start sector | Sector count | Byte offset | Byte size | Inclusive byte range |
|---|---|---|---:|---:|---:|---:|---:|---|
| scp1 | /dev/block/by-name/scp1 | /dev/block/mmcblk0p19 | 19 | 724,992 | 2,048 | 371,195,904 / 0x16200000 | 1,048,576 / 0x00100000 | 0x16200000-0x162fffff |
| scp2 | /dev/block/by-name/scp2 | /dev/block/mmcblk0p20 | 20 | 727,040 | 2,048 | 372,244,480 / 0x16300000 | 1,048,576 / 0x00100000 | 0x16300000-0x163fffff |

SCP block-object metadata:
By-name object type            : symbolic link
Block-node type                : block device
Block-node mode                : brw-------
Owner                          : root:root
SELinux context                : u:object_r:block_device:s0
Parent logical block size      : 512 bytes
Parent physical block size     : 512 bytes
Parent minimum I/O size        : 512 bytes
Parent optimal I/O size        : 0
Per-partition queue path       : absent for mmcblk0p19-mmcblk0p22; parent mmcblk0 queue is the geometry authority

SCP redundant-pair identity:
scp1 SHA256                    : d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374
scp2 SHA256                    : d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374
cmp result                     : status 0
Pair relationship              : byte-for-byte identical
Active selected copy           : OPEN_BOUNDARY

SCP TinySys outer record header:
Primary magic raw              : 88 16 88 58
Primary magic LE               : 0x58881688
Record-0 payload length raw    : 38 04 00 00
Record-0 payload length        : 0x00000438 / 1,080 bytes
Record-0 name                  : tinysys-loader-CM4_A<NUL>
Secondary marker raw           : 89 16 89 58
Secondary marker LE            : 0x58891689
Payload-offset raw             : 00 02 00 00
Payload offset                 : 0x00000200 / 512 bytes

SCP container record map observed:

| Record | Record start | Name offset | Name | Declared payload length | Payload start | Payload end exclusive | Next aligned record |
|---:|---:|---:|---|---:|---:|---:|---:|
| 0 | 0x00000000 | 0x00000008 | tinysys-loader-CM4_A | 0x00000438 | 0x00000200 | 0x00000638 | 0x00000640 |
| 1 | 0x00000640 | 0x00000648 | cert1 | 0x000006ad | 0x00000840 | 0x00000eed | 0x00000ef0 |
| 2 | 0x00000ef0 | 0x00000ef8 | cert2 | 0x000003bd | 0x000010f0 | 0x000014ad | 0x000014b0 |
| 3 | derived 0x000014b0 | observed 0x000014b8 | tinysys-scp-CM4_A | retained payload size 0x0003b800 | not re-read this pass | not re-read this pass | OPEN_BOUNDARY |

SCP record alignment evidence:
Record 0 payload end           : 0x638
Record 1 header start          : 0x640
Padding                        : 8 bytes
Record 1 payload end           : 0xeed
Record 2 header start          : 0xef0
Padding                        : 3 bytes
Record 2 payload end           : 0x14ad
Next observed name offset      : 0x14b8
Derived next header start      : 0x14b0
Derived padding                : 3 bytes

SCP loader payload prefix at partition offset 0x200:
Raw first words                : 00000538 000001e1 00000261 00000261
Repeated word                  : 0x00000261 across subsequent captured entries
Semantic interpretation        : STRUCT_OPEN

SCP certificate identity:
cert1 payload start            : 0x00000840
cert1 first bytes              : 30 82 06 a9 30 82 05 58 a0 03 02 01 02 02 09 00
DER sequence encoded length    : 0x06a9 plus 4-byte TLV header = 0x06ad
Record-length agreement        : exact
cert1 serial bytes observed    : 8d cc 21 f6 bc c0 52 5c
cert1 country field observed   : AU
cert1 classification           : DER_X509_SHAPED_CERTIFICATE_OBJECT_CONFIRMED
cert2 payload format           : not read in current pass

SCP embedded identity strings and exact offsets:

| Partition offset | String |
|---:|---|
| 0x00000008 | tinysys-loader-CM4_A |
| 0x000014b8 | tinysys-scp-CM4_A |
| 0x0001eec4 | MPE_CAL_A_VER_18082801 |
| 0x00020d9d | kernel/FreeRTOS/Source/list.c |
| 0x00020dbb | kernel/FreeRTOS/Source/portable/GCC/ARM_CM4F/port.c |
| 0x00020e08 | kernel/FreeRTOS/Source/portable/MemMang/heap_4.c |
| 0x00020e39 | kernel/FreeRTOS/Source/queue.c |
| 0x00020e58 | kernel/FreeRTOS/Source/tasks.c |
| 0x00020ecb | kernel/FreeRTOS/Source/timers.c |
| 0x00023677 | FreeRTOS %s(build:%s %s) |
| 0x00023c0b | ?[MPEKlib]: %s |
| 0x00023c1b | [MPEKlib]: Acc_run_calibration data input error |
| 0x00023c4b | [MPEKlib]: Gyro_run_calibration data input error |
| 0x00023ce6 | Lib version: 778c288c |
| 0x00023e12 | Gesture lib version: eec60996 |

Additional SCP source-path identity strings:
drivers/CM4_A/mt6765/dvfs/src/dvfs.c
drivers/CM4_A/mt6765/dvfs/src/resource_req.c
drivers/CM4_A/mt6765/dvfs/src/sleep.c
drivers/CM4_A/mt6765/i2c/src/i2cchre-plat.c
drivers/CM4_A/mt6765/spi/src/spichre-plat.c
drivers/common/ipi/v02/src/scp_ipi.c
drivers/common/logger/v01/src/scp_logger.c
project/CM4_A/mt6765/platform/src/interrupt.c
project/CM4_A/mt6765/platform/src/main.c
project/CM4_A/mt6765/platform/src/scp_scpctl.c

SCP embedded identity resolution:
Processor class                : ARM Cortex-M4F / CM4_A
RTOS                           : FreeRTOS
BSP source lineage             : MT6765
AP communication               : MediaTek IPI
Sensor framework evidence      : CHRE/SCP strings and sensor-table runtime surface
MPEKlib version                : MPE_CAL_A_VER_18082801
Library version                : 778c288c
Gesture-library version        : eec60996
Build-time literal             : format string only; actual substituted build time not recovered

################################################################################
# SSPM EXECUTION DOMAIN
################################################################################

SSPM canonical identity:
Domain                         : MediaTek System Power-Management Processor
Firmware framework             : MediaTek TinySys
Platform device                : 10440000.sspm
Bound driver                   : sspm
DT node                        : /sspm@10440000
Primary role                   : secondary low-power system power-management execution domain
Classification                 : DECLARED_POPULATED_ACTIVE

SSPM runtime binding:
Driver link                    : ../../../bus/platform/drivers/sspm
OF-node link                   : ../../../firmware/devicetree/base/sspm@10440000
uevent DRIVER                  : sspm
uevent OF_NAME                 : sspm
uevent OF_FULLNAME             : /sspm@10440000
uevent OF_COMPATIBLE_0         : mediatek,sspm
uevent OF_COMPATIBLE_N         : 1
uevent MODALIAS                : of:NsspmT<NULL>Cmediatek,sspm

SSPM platform target directory:
Path                           : /sys/devices/platform/10440000.sspm
Child objects                  : driver, driver_override, modalias, of_node, power, subsystem, uevent
Driver-specific root attrs     : none observed beyond standard platform-device objects

SSPM driver directory:
Path                           : /sys/bus/platform/drivers/sspm
Bound-device link              : 10440000.sspm -> ../../../../devices/platform/10440000.sspm
Control files                  : bind, uevent, unbind
Control-file mode              : --w------- root:root

SSPM devicetree string properties:
compatible raw                 : 6d 65 64 69 61 74 65 6b 2c 73 73 70 6d 00
compatible decoded             : mediatek,sspm<NUL>
status property                : absent
Runtime driver binding         : present
Classification                 : STATIC_STATUS_PROPERTY_ABSENT_RUNTIME_DRIVER_BOUND

SSPM register-name order:
1                              : cfgreg
2                              : mbox0_base
3                              : mbox0_ctrl
4                              : mbox1_base
5                              : mbox1_ctrl
6                              : mbox2_base
7                              : mbox2_ctrl
8                              : mbox3_base
9                              : mbox3_ctrl
10                             : mbox4_base
11                             : mbox4_ctrl

SSPM register windows:

| Name | Raw address cells | Raw size cells | Base | Size | Inclusive end |
|---|---|---|---:|---:|---:|
| cfgreg | 00000000 10440000 | 00000000 00010000 | 0x10440000 | 0x00010000 | 0x1044ffff |
| mbox0_base | 00000000 10450000 | 00000000 00000100 | 0x10450000 | 0x00000100 | 0x104500ff |
| mbox0_ctrl | 00000000 10451000 | 00000000 00000008 | 0x10451000 | 0x00000008 | 0x10451007 |
| mbox1_base | 00000000 10460000 | 00000000 00000100 | 0x10460000 | 0x00000100 | 0x104600ff |
| mbox1_ctrl | 00000000 10461000 | 00000000 00000008 | 0x10461000 | 0x00000008 | 0x10461007 |
| mbox2_base | 00000000 10470000 | 00000000 00000100 | 0x10470000 | 0x00000100 | 0x104700ff |
| mbox2_ctrl | 00000000 10471000 | 00000000 00000008 | 0x10471000 | 0x00000008 | 0x10471007 |
| mbox3_base | 00000000 10480000 | 00000000 00000100 | 0x10480000 | 0x00000100 | 0x104800ff |
| mbox3_ctrl | 00000000 10481000 | 00000000 00000008 | 0x10481000 | 0x00000008 | 0x10481007 |
| mbox4_base | 00000000 10490000 | 00000000 00000100 | 0x10490000 | 0x00000100 | 0x104900ff |
| mbox4_ctrl | 00000000 10491000 | 00000000 00000008 | 0x10491000 | 0x00000008 | 0x10491007 |

SSPM interrupt-name order:
1                              : ipc
2                              : mbox0
3                              : mbox1
4                              : mbox2
5                              : mbox3
6                              : mbox4

SSPM interrupt declaration and runtime mapping:

| DT name | Raw cells | Hardware IRQ | Runtime Linux IRQ | Runtime label | Per-CPU counters | Classification |
|---|---|---:|---:|---|---|---|
| ipc | 00000000 000000ca 00000004 | 202 | not found | — | — | DECLARED_NOT_FOUND_IN_CURRENT_PROC_INTERRUPTS |
| mbox0 | 00000000 000000cd 00000004 | 205 | 206 | SSPM_MBOX | 11 4 11 9 18 11 13 11 | RUNTIME_ACTIVE |
| mbox1 | 00000000 000000ce 00000004 | 206 | 207 | SSPM_MBOX | 53137 30783 3255 51068 18913 16485 10791 8490 | RUNTIME_ACTIVE |
| mbox2 | 00000000 000000cf 00000004 | 207 | 208 | SSPM_MBOX | 22026 8060 4835 7199 29322 21741 14299 10448 | RUNTIME_ACTIVE |
| mbox3 | 00000000 000000d0 00000004 | 208 | 209 | SSPM_MBOX | 0 0 0 0 0 0 0 0 | RUNTIME_REGISTERED_ZERO_COUNT |
| mbox4 | 00000000 000000d1 00000004 | 209 | not found | — | — | DECLARED_NOT_FOUND_IN_CURRENT_PROC_INTERRUPTS |

SSPM /proc/iomem reconciliation:
DT register tuples             : eleven windows declared
Named SSPM rows in /proc/iomem : none returned by exact scp/sspm name search
Runtime driver binding         : present
Classification                 : DT_RUNTIME_BINDING_WITH_IOMEM_NAMED_ROW_GAP

SSPM fixed reserved-memory identity:
DT node                        : /reserved-memory/mblock-6-SSPM-reserved
compatible raw                 : mediatek,SSPM-reserved<NUL>
name raw                       : mblock-6-SSPM-reserved<NUL>
Base                           : 0x7ff00000
Size                           : 0x000c0000 / 786,432 bytes
Inclusive end                  : 0x7ffbffff
no-map                         : present
Classification                 : VERIFIED_FIXED_RESERVED_MEMORY

SSPM dynamic shared-memory declaration:
DT node                        : /reserved-memory/reserve-memory-sspm_share
compatible                     : mediatek,reserve-memory-sspm_share<NUL>
name                           : reserve-memory-sspm_share<NUL>
Requested size                 : 0x00510000 / 5,308,416 bytes
Alignment                      : 0x00010000 / 65,536 bytes
Allocation-range base          : 0x40000000
Allocation-range span          : 0x60000000
Allocation window              : 0x40000000-0x9fffffff
no-map                         : present
status raw                     : 6f 6b 61 79
status decoded                 : okay
status length                  : 4 bytes
status NUL termination         : absent
Final allocated runtime base   : OPEN_BOUNDARY
Classification                 : DYNAMIC_RESERVED_MEMORY_DECLARATION_WITH_NON_NUL_STATUS

SSPM firmware partitions:

| Partition | By-name path | Block node | GPT index | Start sector | Sector count | Byte offset | Byte size | Inclusive byte range |
|---|---|---|---:|---:|---:|---:|---:|---|
| sspm_1 | /dev/block/by-name/sspm_1 | /dev/block/mmcblk0p21 | 21 | 729,088 | 2,048 | 373,293,056 / 0x16400000 | 1,048,576 / 0x00100000 | 0x16400000-0x164fffff |
| sspm_2 | /dev/block/by-name/sspm_2 | /dev/block/mmcblk0p22 | 22 | 731,136 | 2,048 | 374,341,632 / 0x16500000 | 1,048,576 / 0x00100000 | 0x16500000-0x165fffff |

SSPM redundant-pair identity:
sspm_1 SHA256                  : 25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81
sspm_2 SHA256                  : 25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81
cmp result                     : status 0
Pair relationship              : byte-for-byte identical
Active selected copy           : OPEN_BOUNDARY

Combined contiguous TinySys partition window:
Start                          : 0x16200000
End                            : 0x165fffff
Size                           : 0x00400000 / 4,194,304 bytes
Order                          : scp1, scp2, sspm_1, sspm_2
Adjacency                      : exact, with no sector gaps between the four 1 MiB partitions

SSPM TinySys outer record header:
Primary magic raw              : 88 16 88 58
Primary magic LE               : 0x58881688
Record-0 payload length raw    : 70 8a 06 00
Record-0 payload length        : 0x00068a70 / 428,656 bytes
Record-0 name                  : tinysys-sspm<NUL>
Secondary marker raw           : 89 16 89 58
Secondary marker LE            : 0x58891689
Payload offset                 : 0x00000200 / 512 bytes

SSPM container record map observed:

| Record | Record start | Name offset | Name | Declared payload length | Payload start | Payload end exclusive | Next aligned record |
|---:|---:|---:|---|---:|---:|---:|---:|
| 0 | 0x00000000 | 0x00000008 | tinysys-sspm | 0x00068a70 | 0x00000200 | 0x00068c70 | 0x00068c70 |
| 1 | 0x00068c70 | 0x00068c78 | cert1 | 0x000006ad | 0x00068e70 | 0x0006951d | 0x00069520 |

SSPM payload-boundary proof:
Payload start                  : 0x00000200
Declared length                : 0x00068a70
Calculated end exclusive       : 0x00068c70
Observed next-record header    : 0x00068c70
Result                         : exact payload-length closure

SSPM payload prefix at partition offset 0x200:
Primary payload marker raw     : 90 16 90 58
Primary payload marker LE      : 0x58901690
Following raw words            : 00000018 00001070 00000004 00000000
Embedded diagnostic strings    : dcm: read fail: addr=0x%08x; vproc; vsram_proc; [FH] DDS stable timeout; [slp]>1.5s

SSPM certificate identity:
cert1 payload start            : 0x00068e70
cert1 first 64 bytes           : identical to the captured SCP cert1 first 64 bytes
DER sequence encoded length    : 0x06a9 plus 4-byte TLV header = 0x06ad
Record-length agreement        : exact
Full SCP/SSPM cert equality    : not asserted without full-payload hash/cmp

SSPM embedded identity strings and exact offsets:

| Partition offset | String |
|---:|---|
| 0x00000008 | tinysys-sspm |
| 0x00000a54 | Build time: %s %s |
| 0x00068c4c | ENDalps-mp-q0.mp1-V9.48_k62v1.64.bsp |

SSPM release string boundary:
String start                   : 0x00068c4c
String byte length             : 0x24 / 36 bytes
Payload end / next record      : 0x00068c70
String termination             : ends exactly at firmware-payload boundary; no NUL byte before next record header
Leading token                  : END
Release body                   : alps-mp-q0.mp1-V9.48_k62v1.64.bsp
Build-time literal             : format string only; actual substituted build time not recovered

################################################################################
# CROSS-LAYER MTK RELEASE IDENTITY
################################################################################

Runtime MTK release properties:
ro.vendor.mediatek.version.branch : alps-mp-q0.mp1
ro.vendor.mediatek.version.release: alps-mp-q0.mp1-V9.48
ro.mediatek.version.branch        : alps-mp-q0.mp1
ro.mediatek.version.release       : alps-mp-q0.mp1-V9.48

Literal property-source lines:
/vendor/build.prop:110         : ro.vendor.mediatek.version.branch=alps-mp-q0.mp1
/vendor/build.prop:111         : ro.vendor.mediatek.version.release=alps-mp-q0.mp1-V9.48
/default.prop:35               : ro.mediatek.version.branch=alps-mp-q0.mp1
/default.prop:36               : ro.mediatek.version.release=alps-mp-q0.mp1-V9.48

SSPM embedded BSP identity:
Embedded string                : ENDalps-mp-q0.mp1-V9.48_k62v1.64.bsp
Common exact release prefix    : alps-mp-q0.mp1-V9.48
Relationship                   : EXACT_RELEASE_PREFIX_MATCH
Meaning                        : Android vendor properties and SSPM firmware expose the same MTK Q0 MP1 V9.48 release lineage
Build-instance equality        : not proven
SSPM-specific suffix           : _k62v1.64.bsp

################################################################################
# FIRMWARE FILESYSTEM NAME-SEARCH BOUNDARY
################################################################################

/vendor/firmware filename matches:
/vendor/firmware/soc1_0_ram_mcu_1_1_hdr.bin
/vendor/firmware/soc1_0_patch_mcu_1_1_hdr.bin

Classification                 : NAME_MATCH_ONLY
SCP/SSPM attribution           : not proven
Dedicated SCP/SSPM source      : partition-resident TinySys containers independently verified

/system/firmware generic search:
Search terms                   : scp|sspm|tinysys|freertos|mcu
Result                         : no matching filename
Command status                 : 1
Boundary                       : does not negate the numeric nRF artifact /system/firmware/187_187.bin

Current dmesg string search:
Search terms                   : scp|sspm|tinysys|freertos|mpek
Result                         : no matching line in current dmesg buffer
Command status                 : 1
Interpretation                 : current-buffer negative only; boot-time absence not asserted

################################################################################
# NRF52832 EXTERNAL EXECUTION DOMAIN
################################################################################

nRF52832 canonical identity:
Domain                         : independent external wearable/sensor-control coprocessor
Vendor                         : Nordic Semiconductor
Processor                      : ARM Cortex-M4F
Internal flash                 : 512 KB class
Internal RAM                   : 64 KB class
BLE class                      : BLE 5.0 class
Active firmware location       : nRF internal flash
Relationship to SCP / SSPM     : separate physical firmware domain; must not be conflated
Classification                 : VERIFIED_EXTERNAL_COPROCESSOR_DOMAIN

Live nRF identity values:
chip_id                        : 0x70
firmware version               : 48059
Bluetooth MAC                  : ca:77:87:c7:16:00
nrf_sys_mode                   : 0
Interface source               : AP-side wiite_corp control interface

Android-side nRF firmware artifact:
Path                           : /system/firmware/187_187.bin
Type                           : regular file
Mode                           : -rw-r--r--
Owner                          : root:root
SELinux context                : u:object_r:system_file:s0
Size                           : 153,832 bytes
Timestamp                      : 2008-12-31 18:00
SHA256                         : 56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c
Artifact format                : raw ARM Cortex-M flat binary
Vector-table offset            : 0x00000000
Initial stack pointer          : 0x20014228
Reset vector raw               : 0x10015469
Reset handler                  : 0x10015468 after Thumb-bit clearing
Mapped flash base              : 0x10010000
Mapped flash end               : 0x100358e7
Artifact / active-flash rule   : eMMC artifact identity does not prove that it is the currently programmed nRF internal-flash image

AP-to-nRF control paths:
Primary I2C path               : I2C4 address 0x50 / wiite_cp / wiite_corp
Runtime I2C node               : /sys/bus/i2c/devices/4-0050
DT compatible                  : wiite,cp
DT status                      : okay
Runtime driver                 : wiite_corp
Wakeup                         : enabled
Primary IRQ                    : GPIO/EINT 8 -> WIITE_CORP0 -> Linux IRQ 27
Secondary IRQ                  : GPIO/EINT 6 -> WIITE_CORP1 -> Linux IRQ 25
UART path                      : UART1 / ttyS1 / register base 0x11003000
Primary control surface        : wiite_corp_ctrl / misc major 10 minor 48
Secondary control surface      : wiite_con_ctrl / misc major 10 minor 50

Wiite control pinmux identity:
SCL alternate / GPIO           : 0x6901 / 0x6900
SDA alternate / GPIO           : 0x6a01 / 0x6a00
UTX alternate / GPIO           : 0x0001 / 0x0000
URX alternate / GPIO           : 0x0101 / 0x0100
wiite_con GPIO endpoints       : GPIO 10 and GPIO 4
wiite_corp DT state            : enabled / status okay
wiite_corp supply              : vhrs-supply

Adjacent PixArt / health-sensor path:
Sensor identity                : PixArt PAR2822 optical health-sensor plane
AP-visible proxy node          : I2C4 address 0x33 / heartrate@33
Runtime I2C node               : /sys/bus/i2c/devices/4-0033
Compatible                     : mediatek,heartrate
DT status                      : okay
Bound Linux driver             : not proven in the captured node state
Ownership boundary             : nRF-adjacent/proxy path retained; exact physical/protocol ownership remains open

Additional I2C4 control object:
DT node                        : ccu_sensor_i2c_4_hw@11
Address                        : 0x11
Compatible                     : mediatek,ccu_sensor_i2c_4_hw
DT status                      : okay
Operational role               : declared control/trigger object; exact role not expanded here

################################################################################
# DECLARATION / POPULATION / ACTIVATION MATRIX
################################################################################

| Domain | Declared | Firmware populated | Driver/runtime bound | Runtime activity | Final classification |
|---|---|---|---|---|---|
| BROM | silicon identity | immutable ROM | not Linux-bound | executes before all mutable stages | VERIFIED_STATIC_SILICON_DOMAIN |
| Preloader | boot-area identity | mmcblk0boot0/1 | not Linux-bound | bootprof evidence | VERIFIED_BOOT_FIRMWARE_DOMAIN |
| LK / LK2 | partition/load identity | redundant bootloader payloads | not Linux-bound | boot handoff evidence | VERIFIED_BOOTLOADER_DOMAIN |
| ATF BL31 | PSCI/ATF logs and reserved memory | secure runtime image | secure monitor active | secondary CPU bring-up records | VERIFIED_SECURE_MONITOR_DOMAIN |
| MTEE | secure-world strings/partitions/nodes | tee1/tee2 | secure-world runtime | secure interfaces present | VERIFIED_SECURE_WORLD_DOMAIN |
| SCP | DT + platform | scp1/scp2 TinySys containers | driver scp bound | IRQ, procfs, DVFS, 250 MHz state | DECLARED_POPULATED_ACTIVE |
| SSPM | DT + platform | sspm_1/sspm_2 TinySys containers | driver sspm bound | SSPM_MBOX IRQ activity | DECLARED_POPULATED_ACTIVE |
| Linux kernel | boot image | kernel payload | active primary kernel | complete Android runtime | VERIFIED_PRIMARY_OS_KERNEL_DOMAIN |
| nRF52832 | live control interface + board topology | active internal flash; eMMC DFU artifact present | wiite_corp AP bridge bound | live identity values and IRQ paths | VERIFIED_EXTERNAL_COPROCESSOR_DOMAIN |

################################################################################
# ANOMALY / CONTRADICTION REGISTER
################################################################################

| Label | Evidence | Classification | Current handling |
|---|---|---|---|
| GENERIC_REMOTEPROC_RPMSG_SURFACE_ABSENT | standard remoteproc/rpmsg sysfs/debugfs paths absent; no generic config assignments found | ANOMALY_RETAINED / SOURCE_MECHANISM_OPEN | retain MTK-specific TinySys path as active mechanism |
| SCP_STATUS_NON_NUL | /scp@10500000/status is raw four-byte `okay` without NUL | ANOMALY_RETAINED | preserve raw bytes and decoded string-like value |
| SSPM_SHARE_STATUS_NON_NUL | reserve-memory-sspm_share/status is raw four-byte `okay` without NUL | ANOMALY_RETAINED | preserve raw bytes and decoded string-like value |
| SSPM_NODE_STATUS_ABSENT | /sspm@10440000 has no status property but driver is bound | LAYER_SPLIT_CONFIRMED | declaration/property absence does not negate runtime binding |
| SSPM_IOMEM_NAMED_ROW_GAP | eleven SSPM DT register tuples; no SSPM-named /proc/iomem rows | WITNESS_DIVERGENCE | retain DT and runtime binding as separate witnesses |
| SSPM_DECLARED_IRQ_GAPS | ipc hwirq202 and mbox4 hwirq209 absent from current /proc/interrupts | OPEN_BOUNDARY | do not classify as physically absent |
| SCP_SENSOR_ID_41_DUPLICATE | sensor ID 41 emitted twice with identical values | ANOMALY_RETAINED | do not infer duplicate physical sensor |
| SCP_FEATURE5_ZERO_FREQ_ENABLED | feature=5, freq=0, enable=1 | STRUCT_OPEN / UNIT_OPEN | retain without semantic assignment |
| TINYSYS_CERT_OBJECTS | cert1 DER object present in SCP and SSPM; SCP cert2 present | VERIFIED_CONTAINER_OBJECTS | full certificate-chain semantics not assigned |
| SSPM_END_PREFIX | terminal payload string begins with literal `END` and ends exactly at record boundary | ANOMALY_RETAINED | preserve exact string and offset |
| REDUNDANT_PAIR_EQUALITY | both SCP copies and both SSPM copies are byte-identical | VERIFIED_CURRENT_CAPTURE | active-selection mechanism remains open |
| NRF_ARTIFACT_ACTIVE_FLASH_SPLIT | /system/firmware/187_187.bin exists, active execution is from nRF internal flash | LAYER_SPLIT_CONFIRMED | do not equate stored artifact with programmed active image |

################################################################################
# OPEN BOUNDARIES
################################################################################

1. GENERIC_REMOTEPROC_RPMSG_KCONFIG_SEMANTICS
   - No generic CONFIG_REMOTEPROC or CONFIG_RPMSG assignment was found.
   - Standard live class/debug paths were absent.
   - Final Kconfig dependency/source interpretation remains open.

2. SCP_SSPM_ACTIVE_COPY_SELECTION
   - scp1 equals scp2 byte-for-byte.
   - sspm_1 equals sspm_2 byte-for-byte.
   - The boot-stage selection mechanism and active copy are not exposed by the current live evidence.

3. SCP_COMPLETE_CONTAINER_ENUMERATION
   - Loader, cert1, cert2, and principal tinysys-scp-CM4_A name are located.
   - The principal record header and all later records were not fully re-read in this pass.

4. SCP_MAIN_RECORD_SIZE_REVERIFICATION
   - Existing firmware-size anchor 0x3b800 is retained.
   - Direct current-pass header-field recapture for the principal SCP record remains open.

5. TINYSYS_CERTIFICATE_CHAIN_SEMANTICS
   - SCP/SSPM cert1 DER structure is confirmed.
   - Full certificate hashes, issuer/subject decode, signatures, chain roles, and cert2 semantics remain open.

6. SSPM_RUNTIME_IPC_COMPLETENESS
   - mbox0-mbox3 are registered; mbox0-mbox2 are active.
   - ipc hwirq202 and mbox4 hwirq209 were not found in the current interrupt table.

7. SSPM_IOMEM_RESOURCE_PRESENTATION
   - SSPM is DT-declared and driver-bound.
   - SSPM register windows did not appear under SSPM names in the current /proc/iomem search.

8. SCP_SSPM_DYNAMIC_SHARED_MEMORY_ALLOCATION
   - Size, alignment, allocation windows, and no-map declarations are known.
   - Final allocated physical bases are not captured.

9. CURRENT_DMESG_BOOT_IDENTITY
   - No SCP/SSPM/TinySys strings were present in the current dmesg buffer.
   - Original boot-time firmware logs may have aged out or be routed elsewhere.

10. VENDOR_FIRMWARE_MCU_FILE_OWNERSHIP
    - Two /vendor/firmware MCU filenames matched generic search terms.
    - Their relationship to connectivity MCU firmware is not collapsed into SCP or SSPM identity.

11. NRF_ACTIVE_INTERNAL_FLASH_EQUIVALENCE
    - The Android-side DFU/reference artifact is exactly identified.
    - Equality with currently programmed nRF internal flash is not proven.

12. PAR2822_EXACT_PROTOCOL_OWNERSHIP
    - The 0x33 heartrate proxy path is present.
    - Exact Linux-driver, nRF-internal bus, and command/frame ownership remain open.

################################################################################
# FINAL SUBSECTION CLASSIFICATION
################################################################################

Canonical execution chain:
BROM                           : immutable MediaTek silicon ROM
Preloader                      : eMMC boot-area firmware
LK / LK2                       : redundant AArch32 Little Kernel bootloader domain
ATF BL31                       : AArch64 EL3 secure monitor / PSCI domain
MTEE                           : AArch64 secure-world EL1 domain
SCP                            : MTK TinySys CM4_A / FreeRTOS secondary sensor-control domain
SSPM                           : MTK TinySys secondary system power-management/mailbox domain
Linux kernel                   : Linux 4.9.190+ AArch64 primary Android kernel domain
nRF52832                       : independent external ARM Cortex-M4F wearable/sensor coprocessor domain

Canonical SCP identity:
Firmware partitions            : scp1 / scp2, each 1 MiB, byte-identical
Firmware framework             : MediaTek TinySys
Principal firmware object      : tinysys-scp-CM4_A
Loader object                  : tinysys-loader-CM4_A
RTOS                           : FreeRTOS
Processor class                : ARM CM4F
BSP source lineage             : MT6765
Fixed reserved memory          : 0x9f900000-0x9fefffff
Dynamic shared-memory request  : 0x00300000 bytes
Runtime driver                 : scp
Runtime IRQ                    : Linux IRQ 183 / hardware IRQ 192 / SCP A IPC2HOST
Runtime state                  : DVFS ON; 250 MHz current and expected; sleep mode at capture
MPEKlib identity               : MPE_CAL_A_VER_18082801
Library identity               : 778c288c
Gesture-library identity       : eec60996

Canonical SSPM identity:
Firmware partitions            : sspm_1 / sspm_2, each 1 MiB, byte-identical
Firmware framework             : MediaTek TinySys
Principal firmware object      : tinysys-sspm
Fixed reserved memory          : 0x7ff00000-0x7ffbffff
Dynamic shared-memory request  : 0x00510000 bytes
Runtime driver                 : sspm
Mailbox register groups        : mbox0 through mbox4
Declared interrupt set         : ipc plus mbox0 through mbox4
Runtime registered set         : mbox0 through mbox3
Embedded BSP release           : alps-mp-q0.mp1-V9.48_k62v1.64.bsp
Android/SSPM release relation  : exact alps-mp-q0.mp1-V9.48 prefix match

Canonical nRF identity:
Processor                      : Nordic nRF52832 / ARM Cortex-M4F
Active firmware location       : internal flash
Runtime identity               : chip_id 0x70; version 48059; MAC ca:77:87:c7:16:00; mode 0
Android-side artifact          : /system/firmware/187_187.bin
Artifact size                  : 153,832 bytes
Artifact SHA256                : 56b9c5256d5e4a3da34fd710be7c6a3e63fe7f8de433d3700d59e35d06577b0c
Primary AP bridge              : I2C4 address 0x50 / wiite_corp
Secondary AP bridge            : UART1 / ttyS1
Interrupt paths                : WIITE_CORP0 IRQ 27; WIITE_CORP1 IRQ 25
Adjacent health path           : PixArt PAR2822 / heartrate proxy at I2C4 address 0x33

Subsection status:
CURRENT_EVIDENCE_SET_INTEGRATED
OPEN_BOUNDARIES_RETAINED
NO_EXECUTION_DOMAIN_CONFLATION
NO_EXTERNAL_DOCUMENT_DEPENDENCY




################################################################################
# 1.11 — SECTION 1 CLOSURE SUMMARY
################################################################################

Canonical Section 1 root facts:
Device                         : LOKMAT APPLLP 5 MAX
Board                          : C17S / WP_C17S_PIX_TFT_D4
Product                        : full_WP_C17S_PIX_TFT_D4
Physical SoC                   : MediaTek MT6762V/WB
Software BSP                   : MT6765
BROM HW Code                   : 0x766
ME_ID                          : 4B5CF18EF37D514A6256199E0BBD5E6A
SOC_ID                         : 6044C8E6000D6C384827B28853120093E04F3C815E8A50E6D20D6A6C24DF1B11
eMMC CID                       : 150100335636434d42036c4bd6326559
CPU                            : 8x ARM Cortex-A53, ARMv8-A, AArch64
GPU                            : PowerVR GE8320 / Rogue / MEOW
RAM                            : LPDDR4, 6,031,948 kB live MemTotal
System RAM base                : 0x40000000
Kernel                         : Linux 4.9.190+
Android base                   : Android 10 / API 29 / VNDK-29
Build ID                       : QP1A.190711.020
Signing                        : test-keys
androidboot.hardware           : mt6765
ro.boot.hardware               : mt6762
ro.hardware                    : mt6762
ro.board.platform              : mt6765
ro.vendor.mediatek.platform    : MT6765
Boot image header version      : 2
BOARD_KERNEL_BASE              : 0x40000000
BOARD_KERNEL_OFFSET            : 0x00080000
BOARD_RAMDISK_OFFSET           : 0x11b00000
BOARD_TAGS_OFFSET              : 0x07880000
BOARD_DTB_OFFSET               : 0x07880000
BOARD_KERNEL_PAGESIZE          : 2048
Kernel binary size             : 9,246,082 bytes
Ramdisk size                   : 923,597 bytes
Live GPIO registered count      : 180 pins, GPIO0-GPIO179
DTBO GPIO initialized entries   : 179 entries, GPIO0-GPIO178
Core I2C controller count       : I2C0 through I2C6 present in live platform map
Firmware domains               : BROM, Preloader, LK, MTEE, ATF, SCP, SSPM,
Linux kernel, nRF52832

Section 1 status:
COMPLETE as root silicon and platform identity.
Peripheral topology continues in later hardware-digital-twin sections.
################################################################################

# END SECTION 1 — CORE SILICON & PLATFORM IDENTITY

################################################################################

