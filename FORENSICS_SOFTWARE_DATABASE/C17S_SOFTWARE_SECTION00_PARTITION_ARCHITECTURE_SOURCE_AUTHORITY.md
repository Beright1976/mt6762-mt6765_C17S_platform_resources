################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 00 — DOCUMENT CONTROL, PARTITION ARCHITECTURE & SOURCE AUTHORITY
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 00 — SOFTWARE AUTHORITY ROOT
# Date        : 2026-06-12 live-verification integration pass
################################################################################

################################################################################
# SCOPE
################################################################################

This section establishes the software-source authority root for the full C17S
software digital twin. Every later software section consumes this section for
partition topology, extraction provenance, runtime mount truth, fstab truth,
source-file authority, and capture-state boundaries.

This section covers:
- Authoritative source hierarchy and document-control rules.
- Clean extraction provenance and file counts for system, vendor, and product.
- Complete eMMC/GPT partition architecture.
- Complete 38-entry GPT table from live sysfs.
- eMMC hardware-region aliasing and by-name namespace differences.
- Dynamic partition metadata and logical partition mapping.
- fstab.mt6762 / fstab.mt6765 identity, hash, source template, and ghost entries.
- Live /proc/mounts state.
- Kernel command line from live device.
- Current research security state versus owner-attested stock boundary.
- Cross-reference map to software Sections 01-18 and hardware database sections.

This section does not decide whether every declared storage route is physically
populated. It records declaration, runtime activity, absence, and open leads in
their correct evidence class. Full negative classification is carried forward to
Software Section 17.

################################################################################
# SOURCE AUTHORITY
################################################################################

TIER 1 : Live rooted C17S device
         - adb shell
         - su through Magisk
         - live /proc, /sys, /dev, getprop, lpdump, mount-state data
         - authority for runtime-sourced data

TIER 2 : Clean TWRP-extracted partition pulls
         - system/  from clean super_unpacked/system/
         - vendor/  from clean super_unpacked/vendor/
         - product/ from clean super_unpacked/product/
         - raw logical partition images: system.img, vendor.img, product.img
         - authority for file-sourced data

TIER 3 : Binary/static analysis
         - apktool, JADX, Ghidra, strings, xxd, sha256sum, file inventories
         - authority only for the artifacts actually analyzed

TIER 4 : Hardware digital twin cross-reference
         - confirms physical topology and negative hardware boundaries
         - prevents software declarations from being promoted to physical truth

FINAL  : Beright / Albert acceptance after live re-verification

################################################################################
# RULES
################################################################################

No stale values.
No borrowed values from similar MTK devices.
No assumption promoted to software truth.
No software declaration promoted to hardware truth.
No fstab entry promoted to active mount truth.
No device node promoted to active runtime use without mount/process/HAL evidence.
No partition name promoted to active mount source without live /proc/mounts proof.
All layer-specific truths must remain layer-specific.

Classification vocabulary used in this section:

[VERIFIED]             Proven by live device output, clean pull hash/count, or
                       cited source artifact.
[ACTIVE_RUNTIME]       Present and active in current live runtime.
[DECLARED]             Present in fstab/config/source but not necessarily active.
[FILE_AUTHORITY]       Sourced from clean extracted partition pull.
[RAW_PARTITION]        Addressable partition/raw block object; not mounted as fs.
[DYNAMIC_CONTAINER]    Raw partition used as LP metadata/logical-partition backing.
[ALIAS_NAMESPACE]      Symlink namespace, not independent storage.
[BSP_GHOST]            Declared by BSP but absent from live partition/device truth.
[OPEN_LEAD]            Plausible nonstandard route requiring later section analysis.
[STOCK_ATTESTED]       Owner-attested historical stock state, not current capture.
[CURRENT_CAPTURE]      Current research-state capture.

################################################################################
# 0.1 — DOCUMENT CONTROL & CAPTURE BOUNDARY
################################################################################

Canonical device identity:
Commercial device             : LOKMAT APPLLP 5 MAX
Device family                 : C17S
Board/device string           : WP_C17S_PIX_TFT_D4
Physical SoC                  : MT6762V/WB
Software BSP / HAL platform   : MT6765
Android release               : 10
VNDK version                  : 29
Build display ID              : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613
Build fingerprint             : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749799430:user/test-keys

Live property identity, current capture:
ro.product.device             : WP_C17S_PIX_TFT_D4                    [VERIFIED]
ro.product.board              : WP_C17S_PIX_TFT_D4                    [VERIFIED]
ro.board.platform             : mt6765                                [VERIFIED]
ro.hardware                   : mt6762                                [VERIFIED]
ro.boot.hardware              : mt6762                                [VERIFIED]
ro.vendor.mediatek.platform   : MT6765                                [VERIFIED]
ro.build.display.id           : APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613 [VERIFIED]
ro.build.fingerprint          : SPRD/full_WP_C17S_PIX_TFT_D4/WP_C17S_PIX_TFT_D4:10/QP1A.190711.020/1749799430:user/test-keys [VERIFIED]

Interpretation:
The device exposes a real cross-layer identity split. MT6762/MT6762V-WB is the
physical/kernel hardware identity. MT6765 is the software BSP/HAL/storage-template
identity. Both values are correct only in their own layers.

################################################################################
# 0.2 — STOCK STATE VERSUS CURRENT RESEARCH CAPTURE
################################################################################

0.2.1 — Stock factory state boundary

Classification: STOCK_ATTESTED

The owner states the device was stock locked and SELinux enforcing before
bootloader unlock, Magisk, TWRP, and live research modifications. This is not the
current live capture state. It is retained as historical context only.

Stock-attested boundary:
- Bootloader was locked before owner unlock.
- SELinux was enforcing before research modifications.
- Current Magisk/root/permissive/orange state was created by the owner for
  forensic access, TWRP decryption, and clean ROM bring-up.

Important interpretation:
- Owner unlock/root explains the current research access state.
- Owner unlock/root does not explain ODM-signed privileged packages or vendor
  framework modifications that were present in the extracted stock image.
- Stock and current-capture states must never be merged.

0.2.2 — Current research capture state

Classification: CURRENT_CAPTURE

adb root identity              : uid=0(root), gid=0(root), context=u:r:magisk:s0 [VERIFIED]
SELinux getenforce             : Permissive                            [VERIFIED]
/sys/fs/selinux/enforce        : 0                                     [VERIFIED]
ro.boot.verifiedbootstate      : orange                                [VERIFIED]
ro.boot.flash.locked           : 0                                     [VERIFIED]
ro.debuggable                  : 0                                     [VERIFIED]
ro.secure                      : 1                                     [VERIFIED]
ro.adb.secure                  : 1                                     [VERIFIED]
ro.boot.dynamic_partitions     : true                                  [VERIFIED]
ro.build.version.release       : 10                                    [VERIFIED]
ro.vndk.version                : 29                                    [VERIFIED]

Security-state interpretation:
- Current live capture is rooted through Magisk.
- SELinux is permissive in the current capture.
- Verified boot state is orange.
- Bootloader is unlocked.
- adb/root state is a research boundary and must not be written as stock factory posture.

################################################################################
# 0.3 — CLEAN SOURCE PULL AUTHORITY
################################################################################

Clean partition-pull root on host:
~/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/

Host directory contents:
product/
product.img
system/
system.img
vendor/
vendor.img

File count authority:
system/                       : 8,859 files                            [VERIFIED]
vendor/                       : 1,468 files                            [VERIFIED]
product/                      : 286 files                              [VERIFIED]

Raw logical partition image hashes:
system.img SHA256             : 57840c8aa2dd30d97cb34e312381aee26a2dc49ae66eabd00555c348c7fd3987 [VERIFIED]
vendor.img SHA256             : 8c3b36bf8f3db6064dccf398dff18a8de7732452e1d87ace94922e09b9f36baf [VERIFIED]
product.img SHA256            : dd634b2112d1c1ff94416e69d313dd1e8d155759062d5f53ea7bf59292c02fad [VERIFIED]

Raw logical partition image sizes:
system.img                    : 1,288,351,744 bytes                    [VERIFIED]
vendor.img                    :   291,250,176 bytes                    [VERIFIED]
product.img                   : 1,176,743,936 bytes                    [VERIFIED]

Extracted tree apparent byte sizes, host filesystem measurement:
system/                       : 2,976,934,946 bytes                    [VERIFIED]
vendor/                       :   373,154,732 bytes                    [VERIFIED]
product/                      : 1,156,087,583 bytes                    [VERIFIED]

Important interpretation:
- Raw image size comes from the logical partition image file and lpdump extent math.
- Extracted tree apparent size comes from host filesystem du -sb.
- These are different measurements and must not be collapsed.
- File-sourced software inventory must use the clean extracted trees and their hashes/counts as authority.

################################################################################
# 0.4 — eMMC DEVICE GEOMETRY
################################################################################

Primary block device           : /dev/block/mmcblk0                    [VERIFIED]
/sys/block/mmcblk0/size        : 244,277,248 sectors                   [VERIFIED]
Logical block size             : 512 bytes                             [VERIFIED]
Physical block size            : 512 bytes                             [VERIFIED]
Hardware sector size           : 512 bytes                             [VERIFIED]
Decoded user-area size         : 244,277,248 * 512 = 125,069,950,976 bytes [VERIFIED]
Human size                     : ~116.5 GiB                            [VERIFIED]

Stored eMMC identity from prior Section 00 / hardware database authority:
Manufacturer ID                : 0x000015 Samsung
OEM ID                         : 0x0100
Product name                   : 3V6CMB
Hardware revision              : 0x0
Firmware revision              : 0x0300000000000000
Serial number                  : 0x6c4bd632
CID                            : 150100335636434d42036c4bd6326559
CSD                            : d02701320f5903fff6dbffef8e40400d
Manufacture date               : 06/2018
Preferred erase size           : 4,194,304 bytes
Erase size                     : 524,288 bytes
RPMB size multiplier           : 0x80
Pre-EOL info                   : 0x01 normal

################################################################################
# 0.5 — STORAGE OBJECT COUNTS AND BY-NAME NAMESPACES
################################################################################

This device exposes multiple storage namespaces. They are not interchangeable.

0.5.1 — GPT partition count

GPT named partitions           : 38                                    [VERIFIED]
GPT partition numbers          : mmcblk0p1 through mmcblk0p38           [VERIFIED]

0.5.2 — Kernel eMMC object exposure

/proc/partitions eMMC objects  : 42                                    [VERIFIED]

Count composition:
- mmcblk0                      : whole eMMC user area
- mmcblk0p1 .. mmcblk0p38      : 38 GPT partitions
- mmcblk0boot0                 : eMMC boot hardware region
- mmcblk0boot1                 : eMMC boot hardware region
- mmcblk0rpmb                  : Replay Protected Memory Block

Formula:
1 whole user area + 38 GPT partitions + 2 boot regions + 1 RPMB = 42

0.5.3 — /dev/block/by-name namespace

/dev/block/by-name symlinks    : 42                                    [VERIFIED]

/dev/block/by-name contains:
- 38 GPT partition aliases
- mmcblk0                      -> /dev/block/mmcblk0
- mmcblk0boot0                 -> /dev/block/mmcblk0boot0
- mmcblk0boot1                 -> /dev/block/mmcblk0boot1
- mmcblk0rpmb                  -> /dev/block/mmcblk0rpmb

Classification:
- ALIAS_NAMESPACE
- FULL_ROOT_BY_NAME_NAMESPACE

0.5.4 — /dev/block/platform/bootdevice/by-name namespace

/dev/block/platform/bootdevice/by-name symlinks : 40                   [VERIFIED]

/dev/block/platform/bootdevice/by-name contains:
- 38 GPT partition aliases
- preloader_a                  -> /dev/block/mmcblk0boot0
- preloader_b                  -> /dev/block/mmcblk0boot1

It does not expose the root by-name raw aliases:
- mmcblk0
- mmcblk0boot0
- mmcblk0boot1
- mmcblk0rpmb

Classification:
- ALIAS_NAMESPACE
- PLATFORM_BOOTDEVICE_NAMESPACE

Canonical storage count rule:
38 = GPT named partitions
42 = /dev/block/by-name symlinks
42 = kernel-exposed eMMC block objects in /proc/partitions
40 = /dev/block/platform/bootdevice/by-name symlinks

################################################################################
# 0.6 — COMPLETE GPT PARTITION TABLE
################################################################################

All entries below were freshly verified from live sysfs using:
/sys/block/mmcblk0/mmcblk0pN/start
/sys/block/mmcblk0/mmcblk0pN/size
/sys/block/mmcblk0/mmcblk0pN/uevent

Size sectors are 512-byte sectors.

| # | Partition | Block device | Major:Minor | Start sector | Size sectors | Exact bytes | Runtime / software classification |
|---:|---|---|---:|---:|---:|---:|---|
| 1 | boot_para | mmcblk0p1 | 179:1 | 64 | 2,048 | 1,048,576 | RAW_PARTITION; fstab raw /boot_para declaration |
| 2 | recovery | mmcblk0p2 | 179:2 | 2,112 | 65,536 | 33,554,432 | RAW_PARTITION; dedicated recovery partition |
| 3 | para | mmcblk0p3 | 179:3 | 67,648 | 1,024 | 524,288 | RAW_PARTITION; fstab /misc declaration |
| 4 | expdb | mmcblk0p4 | 179:4 | 68,672 | 40,960 | 20,971,520 | RAW_PARTITION; exception/debug partition |
| 5 | vbmeta_system | mmcblk0p5 | 179:5 | 109,632 | 16,384 | 8,388,608 | RAW_PARTITION; AVB chain artifact |
| 6 | vbmeta_vendor | mmcblk0p6 | 179:6 | 126,016 | 16,384 | 8,388,608 | RAW_PARTITION; AVB chain artifact |
| 7 | frp | mmcblk0p7 | 179:7 | 142,400 | 2,048 | 1,048,576 | RAW_PARTITION; fstab /persistent declaration |
| 8 | nvcfg | mmcblk0p8 | 179:8 | 144,448 | 65,536 | 33,554,432 | ACTIVE_RUNTIME; mounted /mnt/vendor/nvcfg |
| 9 | nvdata | mmcblk0p9 | 179:9 | 209,984 | 131,072 | 67,108,864 | ACTIVE_RUNTIME; mounted /mnt/vendor/nvdata |
| 10 | md_udc | mmcblk0p10 | 179:10 | 341,056 | 46,288 | 23,699,456 | ACTIVE_RUNTIME; mounted /metadata |
| 11 | metadata | mmcblk0p11 | 179:11 | 387,344 | 65,536 | 33,554,432 | PRESENT_BUT_NOT_ACTIVE_METADATA_SOURCE; not mounted as /metadata |
| 12 | protect1 | mmcblk0p12 | 179:12 | 452,880 | 16,384 | 8,388,608 | ACTIVE_RUNTIME; mounted /mnt/vendor/protect_f |
| 13 | protect2 | mmcblk0p13 | 179:13 | 469,264 | 22,256 | 11,395,072 | ACTIVE_RUNTIME; mounted /mnt/vendor/protect_s |
| 14 | seccfg | mmcblk0p14 | 179:14 | 491,520 | 16,384 | 8,388,608 | RAW_PARTITION; security config partition |
| 15 | sec1 | mmcblk0p15 | 179:15 | 507,904 | 4,096 | 2,097,152 | RAW_PARTITION; security partition |
| 16 | proinfo | mmcblk0p16 | 179:16 | 512,000 | 6,144 | 3,145,728 | RAW_PARTITION; fstab /proinfo declaration |
| 17 | md1img | mmcblk0p17 | 179:17 | 518,144 | 204,800 | 104,857,600 | RAW_PARTITION; modem image partition |
| 18 | spmfw | mmcblk0p18 | 179:18 | 722,944 | 2,048 | 1,048,576 | RAW_PARTITION; SPM firmware partition |
| 19 | scp1 | mmcblk0p19 | 179:19 | 724,992 | 2,048 | 1,048,576 | RAW_PARTITION; SCP firmware slot 1 |
| 20 | scp2 | mmcblk0p20 | 179:20 | 727,040 | 2,048 | 1,048,576 | RAW_PARTITION; SCP firmware slot 2 |
| 21 | sspm_1 | mmcblk0p21 | 179:21 | 729,088 | 2,048 | 1,048,576 | RAW_PARTITION; SSPM firmware slot 1 |
| 22 | sspm_2 | mmcblk0p22 | 179:22 | 731,136 | 2,048 | 1,048,576 | RAW_PARTITION; SSPM firmware slot 2 |
| 23 | gz1 | mmcblk0p23 | 179:23 | 733,184 | 32,768 | 16,777,216 | RAW_PARTITION; GZ/secure firmware slot 1 |
| 24 | gz2 | mmcblk0p24 | 179:24 | 765,952 | 32,768 | 16,777,216 | RAW_PARTITION; GZ/secure firmware slot 2 |
| 25 | nvram | mmcblk0p25 | 179:25 | 798,720 | 131,072 | 67,108,864 | RAW_PARTITION; fstab /nvram declaration |
| 26 | lk | mmcblk0p26 | 179:26 | 929,792 | 2,048 | 1,048,576 | RAW_PARTITION; Little Kernel slot 1 |
| 27 | lk2 | mmcblk0p27 | 179:27 | 931,840 | 2,048 | 1,048,576 | RAW_PARTITION; Little Kernel slot 2 |
| 28 | boot | mmcblk0p28 | 179:28 | 933,888 | 65,536 | 33,554,432 | RAW_PARTITION; boot image partition |
| 29 | logo | mmcblk0p29 | 179:29 | 999,424 | 16,384 | 8,388,608 | RAW_PARTITION; logo partition |
| 30 | dtbo | mmcblk0p30 | 179:30 | 1,015,808 | 16,384 | 8,388,608 | RAW_PARTITION; DTBO partition |
| 31 | tee1 | mmcblk0p31 | 179:31 | 1,032,192 | 10,240 | 5,242,880 | RAW_PARTITION; TEE slot 1 |
| 32 | tee2 | mmcblk0p32 | 259:0 | 1,042,432 | 22,528 | 11,534,336 | RAW_PARTITION; TEE slot 2 |
| 33 | super | mmcblk0p33 | 259:1 | 1,064,960 | 8,388,608 | 4,294,967,296 | DYNAMIC_CONTAINER; backs system/vendor/product |
| 34 | vbmeta | mmcblk0p34 | 259:2 | 9,453,568 | 16,384 | 8,388,608 | RAW_PARTITION; vbmeta root partition |
| 35 | cache | mmcblk0p35 | 259:3 | 9,469,952 | 884,736 | 452,984,832 | ACTIVE_RUNTIME; mounted /cache |
| 36 | userdata | mmcblk0p36 | 259:4 | 10,354,688 | 233,801,695 | 119,706,467,840 | ACTIVE_RUNTIME; mounted /data; backs /data/media emulated storage |
| 37 | otp | mmcblk0p37 | 259:5 | 244,156,383 | 88,064 | 45,088,768 | RAW_PARTITION; OTP partition |
| 38 | flashinfo | mmcblk0p38 | 259:6 | 244,244,447 | 32,768 | 16,777,216 | RAW_PARTITION; flashinfo partition |

Important exactness note:
userdata has an odd sector count: 233,801,695 sectors.
Decoded exact size: 233,801,695 * 512 = 119,706,467,840 bytes.
Do not force userdata into an even-KB alignment in the canonical table.

################################################################################
# 0.7 — NON-GPT eMMC HARDWARE REGIONS
################################################################################

These objects are exposed by the eMMC/kernel block layer but are not GPT
partitions.

mmcblk0                        : whole eMMC user area                   [VERIFIED]
mmcblk0boot0                   : eMMC boot hardware region, 4 MB        [VERIFIED]
mmcblk0boot1                   : eMMC boot hardware region, 4 MB        [VERIFIED]
mmcblk0rpmb                    : Replay Protected Memory Block, 16 MB   [VERIFIED]

Root by-name aliases:
/dev/block/by-name/mmcblk0     -> /dev/block/mmcblk0                   [VERIFIED]
/dev/block/by-name/mmcblk0boot0 -> /dev/block/mmcblk0boot0              [VERIFIED]
/dev/block/by-name/mmcblk0boot1 -> /dev/block/mmcblk0boot1              [VERIFIED]
/dev/block/by-name/mmcblk0rpmb -> /dev/block/mmcblk0rpmb                [VERIFIED]

Platform bootdevice aliases:
/dev/block/platform/bootdevice/by-name/preloader_a -> /dev/block/mmcblk0boot0 [VERIFIED]
/dev/block/platform/bootdevice/by-name/preloader_b -> /dev/block/mmcblk0boot1 [VERIFIED]

Classification:
- eMMC boot0/boot1 are hardware boot regions, not GPT partitions.
- RPMB is a protected hardware security storage region.
- Do not read, dump, publish, modify, or derive secret material from RPMB.

################################################################################
# 0.8 — DYNAMIC PARTITION MAP
################################################################################

lpdump live output:
Metadata version               : 10.0                                  [VERIFIED]
Metadata size                  : 516 bytes                             [VERIFIED]
Metadata max size              : 65,536 bytes                          [VERIFIED]
Metadata slot count            : 2                                     [VERIFIED]

Block device table:
Partition name                 : super                                 [VERIFIED]
First sector                   : 2,048                                 [VERIFIED]
Size                           : 4,294,967,296 bytes                   [VERIFIED]
Flags                          : none                                  [VERIFIED]

Group table:
default maximum size           : 0 bytes                               [VERIFIED]
main maximum size              : 4,292,870,144 bytes                   [VERIFIED]
main flags                     : none                                  [VERIFIED]

Logical partitions:

| Logical partition | Group | Attributes | Extent mapping | Decoded size | Runtime dm |
|---|---|---|---|---:|---|
| product | main | readonly | 0 .. 2,298,327 linear super 2,048 | 1,176,743,936 bytes | dm-0 |
| system | main | readonly | 0 .. 2,516,311 linear super 2,301,952 | 1,288,351,744 bytes | dm-1 |
| vendor | main | readonly | 0 .. 568,847 linear super 4,818,944 | 291,250,176 bytes | dm-2 |

Device mapper namespace:
/dev/block/mapper/product                 -> /dev/block/dm-0           [VERIFIED]
/dev/block/mapper/system                  -> /dev/block/dm-1           [VERIFIED]
/dev/block/mapper/vendor                  -> /dev/block/dm-2           [VERIFIED]
/dev/block/mapper/com.android.tzdata@297903000 -> /dev/block/dm-3      [VERIFIED]

Runtime mount resolution:
/dev/block/dm-1                : mounted at /                          [ACTIVE_RUNTIME]
/dev/block/dm-2                : mounted at /vendor                    [ACTIVE_RUNTIME]
/dev/block/dm-0                : mounted at /product                   [ACTIVE_RUNTIME]
/dev/block/dm-3                : mounted as com.android.tzdata APEX    [ACTIVE_RUNTIME]

Important interpretation:
- system is declared as /system in fstab but resolves at runtime as dm-1 mounted at /.
- /system is therefore not a separate live mount in /proc/mounts.
- vendor and product are live logical partition mounts.
- dm-3 is an APEX backing device, not a named logical partition in super.

################################################################################
# 0.9 — ROOT ARCHITECTURE
################################################################################

Root architecture              : dm-1 mounted at /                     [VERIFIED]
/system mount entry             : absent from /proc/mounts              [VERIFIED]
/system directory               : real directory inside dm-1 root fs     [VERIFIED]
/system/vendor                  : symlink bridge to /vendor             [ORIGINAL_DB_VERIFIED]
/system/product                 : symlink bridge to /product            [ORIGINAL_DB_VERIFIED]

Canonical interpretation:
The device uses an ODM hybrid root layout. It is not conventional modern
system-as-root in the clean AOSP sense, and it is not a simple pre-dynamic
physical /system partition. The system logical partition is mapped through dm-1
and mounted at /. The /system subtree exists inside that root filesystem.

This layout is a critical ROM-bringup boundary. Build rules must preserve the
verified mount topology, not generic Android assumptions.

################################################################################
# 0.10 — FSTAB IDENTITY AND ANALYSIS
################################################################################

Fstab file locations:
/vendor/etc/fstab.mt6762                                           [VERIFIED]
/vendor/etc/fstab.mt6765                                           [VERIFIED]

File metadata:
Size, fstab.mt6762              : 4,169 bytes                       [VERIFIED]
Size, fstab.mt6765              : 4,169 bytes                       [VERIFIED]
SELinux label                   : u:object_r:vendor_configs_file:s0 [VERIFIED]
SHA256, both files              : 6d033c2a7debc697b036874e552c05530ab26d45891a19200cb854efb2104055 [VERIFIED]

Source template embedded in file:
vendor/mediatek/proprietary/hardware/fstab/mt6765/fstab.in.mt6765 [VERIFIED]

Canonical interpretation:
- fstab.mt6762 and fstab.mt6765 are byte-identical.
- Both are generated from the MT6765 BSP fstab template.
- The mt6762-named file is not an independent physical-MT6762 fstab.
- This is a software-layer proof of the MT6762 physical / MT6765 BSP split.

0.10.1 — Logical partition fstab declarations

system   /system   ext4 ro wait,avb=vbmeta_system,logical,first_stage_mount,avb_keys=/avb/q-gsi.avbpubkey:/avb/r-gsi.avbpubkey:/avb/s-gsi.avbpubkey [DECLARED]
vendor   /vendor   ext4 ro wait,avb,logical,first_stage_mount [DECLARED]
product  /product  ext4 ro wait,avb,logical,first_stage_mount [DECLARED]

Runtime distinction:
- fstab declares system at /system.
- live runtime mounts dm-1 at /.
- vendor and product declarations match live mount points.

0.10.2 — Physical ext4 fstab declarations

/dev/block/platform/bootdevice/by-name/md_udc   /metadata              [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/userdata /data                  [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/cache    /cache                 [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/protect1 /mnt/vendor/protect_f  [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/protect2 /mnt/vendor/protect_s  [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/nvdata   /mnt/vendor/nvdata     [DECLARED][ACTIVE_RUNTIME]
/dev/block/platform/bootdevice/by-name/nvcfg    /mnt/vendor/nvcfg      [DECLARED][ACTIVE_RUNTIME]

0.10.3 — Raw eMMC fstab declarations

/dev/block/platform/bootdevice/by-name/frp            /persistent      [DECLARED]
/dev/block/platform/bootdevice/by-name/nvram          /nvram           [DECLARED]
/dev/block/platform/bootdevice/by-name/proinfo        /proinfo         [DECLARED]
/dev/block/platform/bootdevice/by-name/lk             /bootloader      [DECLARED]
/dev/block/platform/bootdevice/by-name/lk2            /bootloader2     [DECLARED]
/dev/block/platform/bootdevice/by-name/para           /misc            [DECLARED]
/dev/block/platform/bootdevice/by-name/boot           /boot            [DECLARED]
/dev/block/platform/bootdevice/by-name/recovery       /recovery        [DECLARED]
/dev/block/platform/bootdevice/by-name/vbmeta_vendor  /vbmeta_vendor   [DECLARED]
/dev/block/platform/bootdevice/by-name/vbmeta_system  /vbmeta_system   [DECLARED]
/dev/block/platform/bootdevice/by-name/logo           /logo            [DECLARED]
/dev/block/platform/bootdevice/by-name/expdb          /expdb           [DECLARED]
/dev/block/platform/bootdevice/by-name/seccfg         /seccfg          [DECLARED]
/dev/block/platform/bootdevice/by-name/tee1           /tee1            [DECLARED]
/dev/block/platform/bootdevice/by-name/tee2           /tee2            [DECLARED]
/dev/block/platform/bootdevice/by-name/scp1           /scp1            [DECLARED]
/dev/block/platform/bootdevice/by-name/scp2           /scp2            [DECLARED]
/dev/block/platform/bootdevice/by-name/sspm_1         /sspm_1          [DECLARED]
/dev/block/platform/bootdevice/by-name/sspm_2         /sspm_2          [DECLARED]
/dev/block/platform/bootdevice/by-name/md1img         /md1img          [DECLARED]
/dev/block/platform/bootdevice/by-name/spmfw          /spmfw           [DECLARED]
/dev/block/platform/bootdevice/by-name/boot_para      /boot_para       [DECLARED]
/dev/block/platform/bootdevice/by-name/dtbo           /dtbo            [DECLARED]
/dev/block/platform/bootdevice/by-name/vbmeta         /vbmeta          [DECLARED]

Important interpretation:
These raw eMMC declarations do not mean the paths are mounted as filesystems in
current /proc/mounts. They are fstab raw-device declarations and boot-chain
addressability records.

0.10.4 — External-storage fstab declarations

/devices/platform/externdevice* auto auto defaults voldmanaged=sdcard1:auto,encryptable=userdata [DECLARED]
/devices/platform/mt_usb*       auto vfat defaults voldmanaged=usbotg:auto [DECLARED]

Current capture state:
- /storage exists as tmpfs.                                           [VERIFIED]
- /storage/emulated is backed by /data/media through sdcardfs.          [VERIFIED]
- /mnt/media_rw/usbotg exists.                                         [VERIFIED]
- No active usbotg mount is present in /proc/mounts.                    [VERIFIED]
- No active sdcard1 mount is present in /proc/mounts.                   [VERIFIED]
- msdc1 platform nodes exist and are carried as OPEN_LEAD.              [OPEN_LEAD]

Classification:
- External routes are declared by fstab.
- Current capture proves emulated storage active.
- Current capture does not prove active physical SD or USB mass-storage mount.
- Physical-population conclusion belongs to hardware Section 12 and software Section 17.

0.10.5 — Fstab ghost entries

Declared in fstab but absent from live by-name and /proc/partitions:
/dev/block/platform/bootdevice/by-name/md1dsp    /md1dsp               [BSP_GHOST]
/dev/block/platform/bootdevice/by-name/md1arm7   /md1arm7              [BSP_GHOST]
/dev/block/platform/bootdevice/by-name/md3img    /md3img               [BSP_GHOST]
/dev/block/platform/bootdevice/by-name/odmdtbo   /odmdtbo              [BSP_GHOST]

Verification state:
- Present in fstab.mt6762.                                             [VERIFIED]
- Present in fstab.mt6765.                                             [VERIFIED]
- Absent from /dev/block/platform/bootdevice/by-name.                   [VERIFIED]
- Absent from /dev/block/by-name.                                      [VERIFIED]
- Absent from /proc/partitions.                                        [VERIFIED]

Classification formula:
[DECLARED_IN_FSTAB] + [ABSENT_BY_NAME] + [ABSENT_KERNEL_PARTITION] = BSP_GHOST_STORAGE_ROUTE

################################################################################
# 0.11 — md_udc VERSUS metadata PARTITION RULE
################################################################################

Live block identity:
/dev/block/platform/bootdevice/by-name/md_udc   -> /dev/block/mmcblk0p10 [VERIFIED]
/dev/block/platform/bootdevice/by-name/metadata -> /dev/block/mmcblk0p11 [VERIFIED]

Live sysfs identity:
mmcblk0p10 PARTNAME             : md_udc                                [VERIFIED]
mmcblk0p11 PARTNAME             : metadata                              [VERIFIED]

Live mount truth:
/dev/block/platform/bootdevice/by-name/md_udc /metadata ext4 rw,seclabel,nosuid,nodev,noatime,discard,data=ordered [ACTIVE_RUNTIME]

Canonical rule:
- md_udc is the active /metadata mount source.
- metadata exists as a named physical partition but is not the live /metadata mount source.
- Do not infer active metadata use from the partition name metadata.
- Do not mount mmcblk0p11 as /metadata.

FBE/key-material wording correction:
- Older shorthand describing md_udc as “FBE key material” is too broad.
- Correct wording: md_udc backs the active /metadata operational mount.
- Active CE/DE FBE key files are under /data/unencrypted/key/ per later closed crypto/fstab correction records.
- /metadata/vold metadata-encryption semantics must not be assumed for this device.

################################################################################
# 0.12 — LIVE /proc/mounts TABLE
################################################################################

Current live mount capture:

/dev/block/dm-1 / ext4 ro,seclabel,relatime 0 0
tmpfs /dev tmpfs rw,seclabel,nosuid,relatime,mode=755 0 0
devpts /dev/pts devpts rw,seclabel,relatime,mode=600,ptmxmode=000 0 0
proc /proc proc rw,relatime,gid=3009,hidepid=2 0 0
sysfs /sys sysfs rw,seclabel,relatime 0 0
selinuxfs /sys/fs/selinux selinuxfs rw,relatime 0 0
tmpfs /mnt tmpfs rw,seclabel,nosuid,nodev,noexec,relatime,mode=755,gid=1000 0 0
tmpfs /apex tmpfs rw,seclabel,nosuid,nodev,noexec,relatime,mode=755 0 0
/dev/block/platform/bootdevice/by-name/md_udc /metadata ext4 rw,seclabel,nosuid,nodev,noatime,discard,data=ordered 0 0
/dev/block/dm-2 /vendor ext4 ro,seclabel,relatime 0 0
/dev/block/dm-0 /product ext4 ro,seclabel,relatime 0 0
magisk /sbin tmpfs rw,seclabel,relatime,mode=755 0 0
devpts /sbin/.magisk/pts devpts rw,seclabel,nosuid,noexec,relatime,mode=600,ptmxmode=000 0 0
none /dev/cg2_bpf cgroup2 rw,nosuid,nodev,noexec,relatime 0 0
none /dev/cpuctl cgroup rw,nosuid,nodev,noexec,relatime,cpu 0 0
none /acct cgroup rw,nosuid,nodev,noexec,relatime,cpuacct 0 0
none /dev/cpuset cgroup rw,nosuid,nodev,noexec,relatime,cpuset,noprefix,release_agent=/sbin/cpuset_release_agent 0 0
none /dev/stune cgroup rw,nosuid,nodev,noexec,relatime,schedtune 0 0
debugfs /sys/kernel/debug debugfs rw,seclabel,relatime 0 0
tracefs /sys/kernel/debug/tracing tracefs rw,seclabel,relatime 0 0
none /config configfs rw,nosuid,nodev,noexec,relatime 0 0
bpf /sys/fs/bpf bpf rw,nosuid,nodev,noexec,relatime 0 0
pstore /sys/fs/pstore pstore rw,seclabel,nosuid,nodev,noexec,relatime 0 0
/dev/block/platform/bootdevice/by-name/userdata /data ext4 rw,seclabel,nosuid,nodev,noatime,noauto_da_alloc,resgid=1065,errors=panic,data=ordered 0 0
/dev/block/platform/bootdevice/by-name/cache /cache ext4 rw,seclabel,nosuid,nodev,noatime,discard,noauto_da_alloc,data=ordered 0 0
/dev/block/platform/bootdevice/by-name/protect1 /mnt/vendor/protect_f ext4 rw,seclabel,nosuid,nodev,noatime,nodelalloc,noauto_da_alloc,commit=1,data=ordered 0 0
/dev/block/platform/bootdevice/by-name/protect2 /mnt/vendor/protect_s ext4 rw,seclabel,nosuid,nodev,noatime,nodelalloc,noauto_da_alloc,commit=1,data=ordered 0 0
/dev/block/platform/bootdevice/by-name/nvdata /mnt/vendor/nvdata ext4 rw,seclabel,nosuid,nodev,noatime,discard,noauto_da_alloc,data=ordered 0 0
/dev/block/platform/bootdevice/by-name/nvcfg /mnt/vendor/nvcfg ext4 rw,seclabel,nosuid,nodev,noatime,nodelalloc,noauto_da_alloc,commit=1,data=ordered 0 0
tmpfs /storage tmpfs rw,seclabel,nosuid,nodev,noexec,relatime,mode=755,gid=1000 0 0
adb /dev/usb-ffs/adb functionfs rw,relatime 0 0
/dev/block/dm-3 /apex/com.android.tzdata@297903000 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/dm-3 /apex/com.android.tzdata ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop3 /apex/com.android.media@300900700 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop3 /apex/com.android.media ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop4 /apex/com.android.runtime@1 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop4 /apex/com.android.runtime ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop5 /apex/com.android.apex.cts.shim@1 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop5 /apex/com.android.apex.cts.shim ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop6 /apex/com.android.resolv@300900700 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop6 /apex/com.android.resolv ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop7 /apex/com.android.conscrypt@291900801 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop7 /apex/com.android.conscrypt ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop8 /apex/com.android.media.swcodec@300900700 ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/dev/block/loop8 /apex/com.android.media.swcodec ext4 ro,dirsync,seclabel,nodev,noatime 0 0
/data/media /mnt/runtime/default/emulated sdcardfs rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal 0 0
/data/media /storage/emulated sdcardfs rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=1015,multiuser,mask=6,derive_gid,default_normal 0 0
/data/media /mnt/runtime/read/emulated sdcardfs rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=23,derive_gid,default_normal 0 0
/data/media /mnt/runtime/write/emulated sdcardfs rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal 0 0
/data/media /mnt/runtime/full/emulated sdcardfs rw,nosuid,nodev,noexec,noatime,fsuid=1023,fsgid=1023,gid=9997,multiuser,mask=7,derive_gid,default_normal 0 0

Mount interpretation:
- / is dm-1 and read-only.
- /vendor is dm-2 and read-only.
- /product is dm-0 and read-only.
- /metadata is md_udc and read-write.
- /data is userdata and read-write.
- /cache, protect1, protect2, nvdata, and nvcfg are active ext4 mounts.
- /storage is tmpfs with emulated storage backed by /data/media through sdcardfs.
- No active physical sdcard1 or usbotg filesystem is mounted in this capture.
- Magisk /sbin is a current research artifact, not stock posture.

################################################################################
# 0.13 — APEX / LOOP / DM RUNTIME SURFACES
################################################################################

APEX dm mount:
/dev/block/dm-3 -> /apex/com.android.tzdata@297903000                 [ACTIVE_RUNTIME]
/dev/block/dm-3 -> /apex/com.android.tzdata                           [ACTIVE_RUNTIME]

APEX loop mounts:
/dev/block/loop3 -> /apex/com.android.media@300900700                 [ACTIVE_RUNTIME]
/dev/block/loop3 -> /apex/com.android.media                           [ACTIVE_RUNTIME]
/dev/block/loop4 -> /apex/com.android.runtime@1                       [ACTIVE_RUNTIME]
/dev/block/loop4 -> /apex/com.android.runtime                         [ACTIVE_RUNTIME]
/dev/block/loop5 -> /apex/com.android.apex.cts.shim@1                 [ACTIVE_RUNTIME]
/dev/block/loop5 -> /apex/com.android.apex.cts.shim                   [ACTIVE_RUNTIME]
/dev/block/loop6 -> /apex/com.android.resolv@300900700                [ACTIVE_RUNTIME]
/dev/block/loop6 -> /apex/com.android.resolv                          [ACTIVE_RUNTIME]
/dev/block/loop7 -> /apex/com.android.conscrypt@291900801             [ACTIVE_RUNTIME]
/dev/block/loop7 -> /apex/com.android.conscrypt                       [ACTIVE_RUNTIME]
/dev/block/loop8 -> /apex/com.android.media.swcodec@300900700         [ACTIVE_RUNTIME]
/dev/block/loop8 -> /apex/com.android.media.swcodec                   [ACTIVE_RUNTIME]

Loop-node note:
/dev/block exposes loop0 through loop15 in the current capture, but only loop3
through loop8 are mounted as APEX filesystems. Existence of loop block nodes does
not prove active mount use.

################################################################################
# 0.14 — EMULATED, EXTERNAL, USB, AND CD-ROM STORAGE SURFACES
################################################################################

Active user-facing storage:
/data/media -> /storage/emulated                                      [ACTIVE_RUNTIME]
/data/media -> /mnt/runtime/default/emulated                          [ACTIVE_RUNTIME]
/data/media -> /mnt/runtime/read/emulated                             [ACTIVE_RUNTIME]
/data/media -> /mnt/runtime/write/emulated                            [ACTIVE_RUNTIME]
/data/media -> /mnt/runtime/full/emulated                             [ACTIVE_RUNTIME]

Storage directories:
/storage/emulated                                                      [VERIFIED]
/storage/sdcard0 -> /sdcard                                            [VERIFIED]
/storage/self                                                          [VERIFIED]
/mnt/media_rw/usbotg                                                   [VERIFIED]
/mnt/cd-rom                                                            [VERIFIED]

Current external/USB state:
- fstab declares sdcard1 route through /devices/platform/externdevice*. [DECLARED]
- fstab declares usbotg route through /devices/platform/mt_usb*.         [DECLARED]
- /mnt/media_rw/usbotg directory exists.                                [VERIFIED]
- /mnt/cd-rom directory exists with restricted mode.                     [VERIFIED]
- No physical sdcard1 mount is present in /proc/mounts.                 [VERIFIED]
- No USB storage filesystem is mounted in /proc/mounts.                 [VERIFIED]
- /dev/usb-ffs/adb functionfs is active for adb.                        [ACTIVE_RUNTIME]

Platform nodes relevant to open storage leads:
11210000.usb1p_sif                                                     [VERIFIED]
11210000.usb_sif                                                       [VERIFIED]
11c90000.msdc1_pad_macro                                               [VERIFIED]
11c90000.msdc1_top                                                     [VERIFIED]
11cd0000.msdc0_pad_macro                                               [VERIFIED]
11cd0000.msdc0_top                                                     [VERIFIED]
msdc1_ins@0                                                            [VERIFIED]
mt_usb                                                                 [VERIFIED]
usb_phy_generic.0.auto                                                 [VERIFIED]

Properties relevant to storage/USB:
init.svc.storaged             : running                                [VERIFIED]
init.svc.vold                 : running                                [VERIFIED]
init.svc.usbd                 : stopped                                [VERIFIED]
init.svc.vendor.usb-hal-1-1   : running                                [VERIFIED]
persist.sys.isolated_storage  : true                                   [VERIFIED]
sys.isolated_storage_snapshot : true                                   [VERIFIED]
persist.sys.usb.config        : adb                                    [VERIFIED]
sys.usb.config                : mtp,adb                                [VERIFIED]
sys.usb.controller            : musb-hdrc                              [VERIFIED]
sys.usb.state                 : mtp,adb                                [VERIFIED]
vendor.usb.pid                : 0x201D                                 [VERIFIED]
vendor.usb.vid                : 0x0E8D                                 [VERIFIED]
vold.has_adoptable            : 1                                      [VERIFIED]
vold.has_quota                : 1                                      [VERIFIED]
vold.has_reserved             : 1                                      [VERIFIED]
vold.post_fs_data_done        : 1                                      [VERIFIED]

Classification:
- Emulated storage is active and backed by userdata.
- USB/SD external storage routes are declared but not mounted in this capture.
- msdc1 / mt_usb surfaces remain OPEN_LEAD for Section 17 and hardware Section 10/12 correlation.
- /mnt/cd-rom exists but is not active-mounted in this capture.

################################################################################
# 0.15 — KERNEL COMMAND LINE
################################################################################

Live /proc/cmdline current capture:

console=tty0 console=ttyS0,921600n1 vmalloc=400M slub_debug=OFZPU page_owner=on swiotlb=noforce androidboot.hardware=mt6765 maxcpus=8 loop.max_part=7 firmware_class.path=/vendor/firmware has_battery_removed=0 androidboot.boot_devices=bootdevice,11230000.mmc root=/dev/ram  androidboot.verifiedbootstate=orange bootopt=64S3,32N2,64N2 buildvariant=user androidboot.atm=disabled androidboot.meta_log_disable=0 printk.disable_uart=0 bootprof.pl_t=1074 bootprof.lk_t=8057 bootprof.logo_t=999 androidboot.serialno=MTK0002507301608400 androidboot.bootreason=PowerKey gpt=1 usb2jtag_mode=0 mrdump_ddrsv=yes mrdump_cb=0x10e800,0x1400 androidboot.dtb_idx=0 androidboot.dtbo_idx=0

Stable boot-architecture fields:
console=tty0
console=ttyS0,921600n1
vmalloc=400M
slub_debug=OFZPU
page_owner=on
swiotlb=noforce
androidboot.hardware=mt6765
maxcpus=8
loop.max_part=7
firmware_class.path=/vendor/firmware
has_battery_removed=0
androidboot.boot_devices=bootdevice,11230000.mmc
root=/dev/ram
androidboot.verifiedbootstate=orange
bootopt=64S3,32N2,64N2
buildvariant=user
androidboot.atm=disabled
androidboot.meta_log_disable=0
printk.disable_uart=0
gpt=1
usb2jtag_mode=0
mrdump_ddrsv=yes
mrdump_cb=0x10e800,0x1400
androidboot.dtb_idx=0
androidboot.dtbo_idx=0

Capture-variable fields:
bootprof.pl_t=1074
bootprof.lk_t=8057
bootprof.logo_t=999
androidboot.serialno=MTK0002507301608400
androidboot.bootreason=PowerKey

Important correction:
Older captures may contain different bootprof values and bootreason values such
as reboot. Those are not storage-architecture contradictions. They are
boot-session variables and must not be treated as immutable BoardConfig constants.

Public-redaction rule:
androidboot.serialno is retained here for private forensic reproducibility. It
should be redacted before public release if the published document set requires
serial-number redaction.

################################################################################
# 0.16 — COMPLETION MATRIX
################################################################################

| Scope item | Status | Authority |
|---|---|---|
| Section purpose and document-control rules | CLOSED | Original software DB + expanded section directory |
| Source authority tiers | CLOSED | Original software DB + expanded section directory |
| Clean system/vendor/product file counts | CLOSED | Host clean pull verification |
| system.img/vendor.img/product.img SHA256 | CLOSED | Host sha256sum |
| eMMC geometry | CLOSED | Live /sys/block/mmcblk0 |
| 38-entry GPT table | CLOSED | Live sysfs p1-p38 start/size/uevent |
| /proc/partitions eMMC object count | CLOSED | Live /proc/partitions |
| /dev/block/by-name count and contents | CLOSED | Live ls/find |
| /dev/block/platform/bootdevice/by-name count and contents | CLOSED | Live ls/find |
| Dynamic partition metadata | CLOSED | Live lpdump |
| dm mapper symlinks | CLOSED | Live /dev/block/mapper |
| fstab.mt6762 and fstab.mt6765 identity | CLOSED | Live ls -lZ + sha256sum |
| fstab logical/physical/raw declarations | CLOSED | Live cat/grep of fstab |
| fstab ghost entries | CLOSED | Live fstab + absent by-name + absent /proc/partitions |
| md_udc versus metadata | CLOSED | Live by-name + uevent + /proc/mounts |
| live /proc/mounts table | CLOSED | Full live /proc/mounts capture |
| emulated storage status | CLOSED | Live /proc/mounts + /storage + /mnt/runtime |
| USB/SD/cd-rom declared versus active state | CLOSED_FOR_SECTION_00 | Detailed negative/physical conclusion carried to Section 17/HW Section 12 |
| kernel cmdline | CLOSED | Live /proc/cmdline |
| stock versus research capture boundary | CLOSED | Owner attestation + live security properties |
| cross-reference map | CLOSED | Expanded software directory + hardware database mapping |

################################################################################
# 0.17 — CROSS-REFERENCE MAP
################################################################################

Software section dependency map:
Section 00 -> consumed by all software sections.
Section 01 -> consumes fstab/init/import/source boundary for RC and service graph.
Section 02 -> consumes dynamic partition/vendor source boundary for HAL/VINTF inventory.
Section 03 -> consumes /metadata, userdata, crypto, and current security-state boundary.
Section 04 -> consumes source and live runtime authority for sensor/nRF paths.
Section 05 -> consumes product/vendor/system mount and blob-source authority.
Section 06 -> consumes vendor/product/system file-source authority for camera HAL/blobs.
Section 07 -> consumes vendor/etc and vendor/lib authority for audio policy and routes.
Section 08 -> consumes modem/NVRAM/protect partition authority and vendor radio blobs.
Section 09 -> consumes vendor/firmware, WMT, Wi-Fi, Bluetooth, GNSS paths.
Section 10 -> consumes clean vendor/system VNDK source authority.
Section 11 -> consumes firmware partition and vendor/firmware source authority.
Section 12 -> consumes all APK locations across system/vendor/product/data boundaries.
Section 13 -> consumes live sysfs, tty, /metadata, userdata, and ODM control source rules.
Section 14 -> consumes SELinux current-capture boundary and policy source paths.
Section 15 -> consumes all partition, mount, fstab, cmdline, and property constants.
Section 16 -> consumes stock/current boundary and privileged software source authority.
Section 17 -> consumes all ghost/dead-route declarations from Section 00.
Section 18 -> consumes Section 00 as the root verification gate for ROM build parity.

Hardware database cross-reference:
SW Section 00 <- HW Section 2, partition map authority.
SW Section 00 also cross-checks HW Section 1 for silicon identity and HW Section 12
for negative hardware/open physical leads.

################################################################################
# 0.18 — FINAL SECTION 00 CONCLUSION
################################################################################

Section 00 is now canonical for software source authority and partition
architecture.

The C17S software storage architecture is not a generic Android layout. It is a
hybrid MTK/ODM topology with:
- physical MT6762 hardware identity,
- MT6765 BSP/fstab/software identity,
- native dynamic partitions,
- dm-1 mounted at /,
- vendor and product mounted separately,
- md_udc as the active /metadata mount source,
- metadata as a present but non-active metadata-source partition,
- 38 GPT partitions,
- 42 root by-name symlinks,
- 40 platform bootdevice by-name symlinks,
- 42 kernel-exposed eMMC storage objects,
- active emulated storage through /data/media,
- declared but inactive external-storage routes in the current capture,
- fstab-declared BSP ghosts with no live partition backing.

This section can be used as the source-of-truth entry gate for later software
digital-twin sections and for clean ROM bring-up planning. Later sections must
not override this section with generic MTK assumptions.

################################################################################
# END SECTION 00
################################################################################
