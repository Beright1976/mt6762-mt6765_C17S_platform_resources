################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 2 — STORAGE GEOMETRY & BOOT TOPOGRAPHY
# 
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 2 — STORAGE / BOOT TOPOGRAPHY
# 
# SCOPE:
# This section defines the complete storage and boot-topology layer of the
# C17S hardware digital twin:
# 
# - eMMC identity
# - eMMC hardware regions
# - RPMB protected region
# - whole-device block geometry
# - GPT state
# - by-name topology
# - all 38 GPT physical partitions
# - every known partition role
# - every known partition size / offset / sector count
# - every known full-object SHA256
# - every known filesystem UUID
# - boot-chain storage layout
# - boot/recovery/vbmeta/dtbo artifact geometry
# - dynamic super LP metadata
# - runtime device-mapper topology
# - non-SAR mount topology
# - APEX runtime dm clarification
# - md_udc / metadata / userdata topology
# - static fstab storage surfaces
# - absent boot/storage surfaces
# - storage-derived build constants
# 
# Security policy, APK behavior, HAL ownership, SELinux, sensor topology,
# nRF52832 command maps, GPIO/I2C peripheral decode, power rails, modem
# runtime behavior, and framework divergence belong to later sections.
# 
# SOURCE AUTHORITY:
# TIER 1 : Live physical device evidence
# TIER 2 : BROM-level extraction and raw binary decode from this exact device
# TIER 3 : Working boot/decrypt/build behavior on this exact device
# TIER 4 : APLog, bootprof, pl_lk, kernel logs, closed documents as evidence
# records only
# FINAL  : Beright / Albert acceptance
# 
# RULES:
# No stale values.
# No alternate values unless the device exposes real layer-specific truths.
# No borrowed values from similar devices.
# No first-sector-only content claims promoted to full-partition truth.
# No assumption promoted to storage truth.
# Full-partition SHA256 governs content identity where readable.
# Live mutable partitions require capture-state language.
# Protected regions require access-limitation language.
################################################################################
################################################################################
# 2.1 — STORAGE MEDIUM IDENTITY
################################################################################
################################################################################

Primary storage medium:
Device node                    : /dev/block/mmcblk0
Kernel block name              : mmcblk0
Kernel bus device              : mmc0:0001
MMC host                       : mmc0
Resolved device path           : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001
Storage class                  : MMC / eMMC
Manufacturer                   : Samsung
Manufacturer ID                : 0x000015
OEM ID                         : 0x0100
Product name                   : 3V6CMB
Hardware revision              : 0x0
Firmware revision              : 0x0300000000000000
Product revision               : 0x3
Serial number                  : 0x6c4bd632
Manufacture date               : 06/2018
Device removable               : 0
Device read-only               : 0
Classification                 : LIVE_PROVEN_EMMC_STORAGE_IDENTITY

Primary block-device identity:
/dev/block/mmcblk0 type         : block device
/dev/block/mmcblk0 mode         : brw-rw----
/dev/block/mmcblk0 owner        : root:system
/dev/block/mmcblk0 context      : u:object_r:bootdevice_block_device:s0
/dev/block/mmcblk0 major:minor  : 179:0
/sys/block/mmcblk0 context      : u:object_r:sysfs_mmcblk:s0

MMC bus uevent:
DRIVER                          : mmcblk
MMC_TYPE                        : MMC
MMC_NAME                        : 3V6CMB
MODALIAS                        : mmc:block

Block-device uevent:
MAJOR                           : 179
MINOR                           : 0
DEVNAME                         : mmcblk0
DEVTYPE                         : disk

CID / CSD identity:
CID                             : 150100335636434d42036c4bd6326559
CSD                             : d02701320f5903fff6dbffef8e40400d
OCR                             : 0x00100000
PRV                             : 0x3
REV                             : 0x8
DSR                             : 0x404

Primary identity source paths:
/sys/block/mmcblk0/device       : symlink to ../../../mmc0:0001
Resolved identity source       : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001
CID source                     : /sys/block/mmcblk0/device/cid
CSD source                     : /sys/block/mmcblk0/device/csd
EXT_CSD source                 : /sys/kernel/debug/mmc0/mmc0:0001/ext_csd

Whole-device capacity:
Total sectors                  : 244,277,248
Total sectors hex              : 0x0e8f6000
Sector unit                    : 512 bytes
Total bytes                    : 125,069,950,976 bytes
Total bytes hex                : 0x1d1ec00000
Binary capacity                : 116.48046875 GiB
Decimal capacity               : 125.069950976 GB

Whole-device capacity cross-witness:
Live sysfs size                : 244,277,248 sectors
EXT_CSD SEC_COUNT[212:215] raw : 00 60 8f 0e
EXT_CSD SEC_COUNT little-endian: 0x0e8f6000
EXT_CSD SEC_COUNT decimal      : 244,277,248 sectors
Capacity equivalence           : 244,277,248 × 512 = 125,069,950,976 bytes
Classification                 : LIVE_SYSFS_EXT_CSD_CAPACITY_EQUIVALENCE_CONFIRMED

Block / I/O geometry:
Logical block size             : 512 bytes
Physical block size            : 512 bytes
Hardware sector size           : 512 bytes
Minimum I/O size               : 512 bytes
Optimal I/O size               : 0 bytes
Discard granularity            : 4,194,304 bytes
Erase size                     : 524,288 bytes
Preferred erase size           : 4,194,304 bytes
Write-protect group size       : 8,388,608 bytes

Block namespace parameters:
mmcblk0 range                   : 32
mmcblk0 ext_range               : 256

eMMC hardware-region exposure:
/dev/block/mmcblk0             : primary eMMC user area
/dev/block/mmcblk0boot0        : eMMC boot hardware partition 0
/dev/block/mmcblk0boot1        : eMMC boot hardware partition 1
/dev/block/mmcblk0rpmb         : RPMB hardware region

eMMC boot hardware partition 0:
Block device                   : /dev/block/mmcblk0boot0
Major:minor                    : 179:32
Size sectors                   : 8,192
Sector unit                    : 512 bytes
Size bytes                     : 4,194,304 bytes
Size KiB                       : 4,096 KiB

eMMC boot hardware partition 1:
Block device                   : /dev/block/mmcblk0boot1
Major:minor                    : 179:64
Size sectors                   : 8,192
Sector unit                    : 512 bytes
Size bytes                     : 4,194,304 bytes
Size KiB                       : 4,096 KiB

RPMB hardware region:
Block device                   : /dev/block/mmcblk0rpmb
Major:minor                    : 179:96
Size sectors                   : 32,768
Sector unit                    : 512 bytes
Size bytes                     : 16,777,216 bytes
Size KiB                       : 16,384 KiB
EXT_CSD RPMB_SIZE_MULT[168]    : 0x80

EXT_CSD live evidence identity:
Source path                    : /sys/kernel/debug/mmc0/mmc0:0001/ext_csd
Source mode                    : -r--r-----
Source owner                   : root:mount
Source context                 : u:object_r:debugfs_mmc:s0
Text export length             : 1,025 bytes
Hex payload length             : 1,024 characters
Trailing terminator            : newline
Represented register length    : 512 bytes
Byte presentation order        : EXT_CSD[0] through EXT_CSD[511]
Ordering proof byte            : EXT_CSD_REV[192] = 0x08
Ordering proof char offset     : 384
Reverse-order candidate offset : 638
Reverse-order candidate value  : 0x00
Text-export SHA256             : 835f1ead5e0c2979175ee29f3bd1fbe1927889ffffef8e56cec6b84ba625c621
SHA256 scope                   : live 1,025-byte debugfs textual export including trailing newline
Classification                 : LIVE_PROVEN_EXT_CSD_TEXTUAL_REGISTER_EXPORT

Selected EXT_CSD identity / capability fields:
EXT_CSD_REV[192]               : 0x08
CARD_TYPE[196]                 : 0x57
BOOT_SIZE_MULT[226]            : 0x20
RPMB_SIZE_MULT[168]            : 0x80
REL_WR_SEC_C[222]              : 0x01
HC_WP_GRP_SIZE[221]            : 0x10
HC_ERASE_GRP_SIZE[224]         : 0x01
CACHE_SIZE[249:252] raw        : 00 00 01 00
CACHE_SIZE little-endian value : 0x00010000
CMDQ_DEPTH[307]                : 0x0f
CMDQ_SUPPORT[308]              : 0x01
BKOPS_SUPPORT[502]             : 0x01
HPI_FEATURES[503]              : 0x01
FFU capable                    : 1

eMMC health fields:
PRE_EOL_INFO sysfs             : 01
PRE_EOL_INFO[267]              : 0x01
DEVICE_LIFE_TIME_EST_TYP_A[268]: 0x01
DEVICE_LIFE_TIME_EST_TYP_B[269]: 0x01
sysfs life_time                : 0x01 0x01
Health-field equivalence       : sysfs and raw EXT_CSD values agree
Classification                 : LIVE_PROVEN_RAW_EMMC_HEALTH_FIELD_IDENTITY

eMMC partitioning / enhanced-area fields:
PARTITION_SETTING_COMPLETED[155]: 0x00
PARTITIONS_ATTRIBUTE[156]      : 0x00
PARTITIONING_SUPPORT[160]      : 0x07
ENH_START_ADDR[136:139] raw    : 00 00 00 00
ENH_START_ADDR value           : 0
ENH_SIZE_MULT[140:142] raw     : 00 00 00
ENH_SIZE_MULT value            : 0
MAX_ENH_SIZE_MULT[157:159] raw : 69 13 00
MAX_ENH_SIZE_MULT little-endian: 0x001369
MAX_ENH_SIZE_MULT decimal      : 4,969

Enhanced-area sysfs state:
enhanced_area_offset raw       : 18446744073709551594
enhanced_area_size raw         : 4294967274
enhanced_area_offset arithmetic: 2^64 - 22
enhanced_area_size arithmetic  : 2^32 - 22
Kernel ABI inactive sentinel   : -EINVAL / -22
Raw EXT_CSD enhanced start     : 0
Raw EXT_CSD enhanced size mult : 0
Active enhanced area           : not enabled
Classification                 : INACTIVE_ENHANCED_AREA_WITH_UNSIGNED_SYSFS_SENTINEL_REPRESENTATION

Live MMC interface state:
Host clock                     : 200,000,000 Hz
VDD selector                   : 20
VDD range                      : 3.2 ~ 3.3 V
Bus mode                       : 2 / push-pull
Chip select                    : 0 / don't care
Power mode                     : 2 / on
Bus width                      : 3 / 8 bits
Timing specification           : 10 / mmc HS400
Signal voltage                 : 1 / 1.80 V
Driver type                    : 0 / driver type B
Clock debugfs value            : 200000000
Classification                 : LIVE_RUNTIME_MMC_INTERFACE_STATE

Live MMC diagnostic raw state:
Debugfs state                  : 0x00001005
Debugfs status                 : 00000900
Classification                 : LIVE_RAW_MMC_DIAGNOSTIC_STATE

Storage-medium region classification:
Primary user area              : /dev/block/mmcblk0
Boot hardware region 0         : /dev/block/mmcblk0boot0
Boot hardware region 1         : /dev/block/mmcblk0boot1
RPMB protected region          : /dev/block/mmcblk0rpmb
GPT-bearing region             : eMMC user area / mmcblk0
Non-GPT hardware regions       : mmcblk0boot0, mmcblk0boot1, mmcblk0rpmb

Canonical storage-medium identity:
Manufacturer                   : Samsung
Product                        : 3V6CMB
Storage technology             : eMMC
Primary block device           : /dev/block/mmcblk0
Exact user-area capacity       : 125,069,950,976 bytes
Exact user-area sectors        : 244,277,248
Logical / physical block size  : 512 / 512 bytes
Boot hardware regions          : 2 × 4,194,304 bytes
RPMB hardware region           : 16,777,216 bytes
Current MMC timing             : HS400
Current MMC bus width          : 8 bits
Current MMC signal voltage     : 1.80 V
Section 2.1 status             : CLOSED_FOR_CURRENT_REVERIFICATION_PASS

################################################################################
################################################################################
# 2.2 — EMMC HARDWARE REGION TOPOLOGY
################################################################################
################################################################################

Subsection control:
Subsection                     : 2.2
Title                          : eMMC Hardware Region Topology
Scope                          : eMMC user area, boot hardware regions, RPMB
                                 hardware region, region block objects, aliases,
                                 active boot-region selection, region protection
                                 state, content equivalence, and RPMB interfaces
Status                         : CLOSED_WITH_BOUNDED_LAYER_DIVERGENCES

Canonical eMMC hardware-region map:

| Hardware region | Primary live object | Kernel object class | Major:minor | Exact sectors | Exact bytes |
|---|---|---|---:|---:|---:|
| User area | /dev/block/mmcblk0 | disk | 179:0 | 244,277,248 | 125,069,950,976 |
| Boot hardware region 0 | /dev/block/mmcblk0boot0 | disk | 179:32 | 8,192 | 4,194,304 |
| Boot hardware region 1 | /dev/block/mmcblk0boot1 | disk | 179:64 | 8,192 | 4,194,304 |
| RPMB hardware region | /dev/block/mmcblk0rpmb | disk | 179:96 | 32,768 | 16,777,216 |

Hardware-region parent topology:
Physical MMC card object       : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001
Primary user-area block object : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001/block/mmcblk0
Boot region 0 block object     : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001/block/mmcblk0/mmcblk0boot0
Boot region 1 block object     : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001/block/mmcblk0/mmcblk0boot1
RPMB block object              : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001/block/mmcblk0/mmcblk0rpmb
Kernel hierarchy               : all four hardware regions belong to one physical
                                 MMC card object; only the user area contains GPT
                                 child partitions
Classification                 : MULTI_WITNESS_CONFIRMED_EMMC_HARDWARE_REGION_TOPOLOGY


eMMC user area:
Block device                   : /dev/block/mmcblk0
Kernel object class            : disk
Major:minor                    : 179:0
Node type                      : block device
Node mode                      : brw-rw----
Node owner                     : root:system
Node context                   : u:object_r:bootdevice_block_device:s0
Role                           : GPT-bearing Android/MTK user area
Contains                       : 38 GPT partitions
Total sectors                  : 244,277,248
Sector unit                    : 512 bytes
Total bytes                    : 125,069,950,976
GPT relationship               : sole GPT-bearing eMMC hardware region
Classification                 : LIVE_PROVEN_GPT_BEARING_EMMC_USER_AREA


eMMC boot hardware partition 0:
Block device                   : /dev/block/mmcblk0boot0
Kernel object class            : disk
Major:minor                    : 179:32
Node type                      : block device
Node mode                      : brw-rw----
Node owner                     : root:system
Node context                   : u:object_r:preloader_block_device:s0
By-name alias                  : preloader_a
Alias path                     : /dev/block/platform/bootdevice/by-name/preloader_a
Alias target                   : /dev/block/mmcblk0boot0
Alias object context           : u:object_r:block_device:s0
Size sectors                   : 8,192
Sector unit                    : 512 bytes
Size                           : 4,096 KiB
Size bytes                     : 4,194,304
Full SHA256                    : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
Role                           : Preloader hardware boot region A
Content relationship           : identical to mmcblk0boot1 by SHA256
Full-object comparison         : byte-for-byte identical to mmcblk0boot1
Comparison status              : cmp status 0
First-sector magic bytes       : 45 4d 4d 43 5f 42 4f 4f 54
First-sector magic ASCII       : EMMC_BOOT
First-sector raw bytes 0x0c-0x0f: 01 00 00 00
First-sector raw bytes 0x10-0x13: 00 02 00 00
First-sector fill 0x14-0x1ff   : ff
Current software force_ro      : 1
Current block-device ro        : 1
ro_lock_until_next_power_on    : 1
Current hardware boot source   : selected
Hardware power-on WP status    : active
Hardware permanent WP status   : inactive
Bad-block interface payload    : empty
Bad-block read status          : 0
Removable                      : 0
ROM action                     : NEVER TOUCH in normal ROM bring-up
Classification                 : ACTIVE_EMMC_BOOT_REGION_0
                                 LIVE_PROVEN_SOFTWARE_READ_ONLY
                                 LIVE_PROVEN_POWER_ON_WRITE_PROTECTED
                                 FULL_OBJECT_EQUIVALENCE_CONFIRMED


eMMC boot hardware partition 1:
Block device                   : /dev/block/mmcblk0boot1
Kernel object class            : disk
Major:minor                    : 179:64
Node type                      : block device
Node mode                      : brw-rw----
Node owner                     : root:system
Node context                   : u:object_r:preloader_block_device:s0
By-name alias                  : preloader_b
Alias path                     : /dev/block/platform/bootdevice/by-name/preloader_b
Alias target                   : /dev/block/mmcblk0boot1
Alias object context           : u:object_r:block_device:s0
Size sectors                   : 8,192
Sector unit                    : 512 bytes
Size                           : 4,096 KiB
Size bytes                     : 4,194,304
Full SHA256                    : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
Role                           : Preloader hardware boot region B
Content relationship           : identical to mmcblk0boot0 by SHA256
Full-object comparison         : byte-for-byte identical to mmcblk0boot0
Comparison status              : cmp status 0
First-sector magic bytes       : 45 4d 4d 43 5f 42 4f 4f 54
First-sector magic ASCII       : EMMC_BOOT
First-sector raw bytes 0x0c-0x0f: 01 00 00 00
First-sector raw bytes 0x10-0x13: 00 02 00 00
First-sector fill 0x14-0x1ff   : ff
Current software force_ro      : 1
Current block-device ro        : 1
ro_lock_until_next_power_on    : 1
Current hardware boot source   : not selected
Hardware power-on WP status    : inactive
Hardware permanent WP status   : inactive
Bad-block interface payload    : empty
Bad-block read status          : 0
Removable                      : 0
ROM action                     : NEVER TOUCH in normal ROM bring-up
Classification                 : REDUNDANT_EMMC_BOOT_REGION_1
                                 LIVE_PROVEN_SOFTWARE_READ_ONLY
                                 FULL_OBJECT_EQUIVALENCE_CONFIRMED


Boot-region EXT_CSD control state:
EXT_CSD source                 : /sys/kernel/debug/mmc0/mmc0:0001/ext_csd
EXT_CSD register length        : 512 bytes
BOOT_WP[173]                   : 0x81
BOOT_WP_STATUS[174]            : 0x01
BOOT_BUS_CONDITIONS[177]       : 0x02
BOOT_CONFIG_PROT[178]          : 0x00
PARTITION_CONFIG[179]          : 0x48

PARTITION_CONFIG[179] decode:
Boot acknowledgement           : enabled
Selected boot hardware area    : boot area 1 in eMMC register terminology
Linux block-object mapping     : boot area 1 -> mmcblk0boot0
Android alias mapping          : mmcblk0boot0 -> preloader_a
Partition access selection     : user area
Canonical active boot source   : /dev/block/mmcblk0boot0 / preloader_a

BOOT_BUS_CONDITIONS[177] decode:
Boot bus width                 : x8
Post-boot width behavior       : reset to x1
Boot transfer mode             : SDR backward-compatible mode

BOOT_WP[173] control decode:
Per-boot-area selection mode   : enabled
Power-on write-protect enable  : enabled
Permanent write-protect enable : disabled
Area identity authority        : BOOT_WP_STATUS[174], not BOOT_WP[173] alone

BOOT_WP_STATUS[174] decode:
Boot hardware region 0         : power-on write-protected
Boot hardware region 1         : not power-on write-protected
Boot region 0 permanent lock   : not indicated
Boot region 1 permanent lock   : not indicated
Classification                 : LIVE_PROVEN_EXT_CSD_BOOT_SELECTION
                                 LIVE_PROVEN_AREA_SPECIFIC_BOOT_WP_STATUS


Boot-region block-interface equivalence:

| Attribute | mmcblk0boot0 | mmcblk0boot1 | Relationship |
|---|---:|---:|---|
| alignment_offset | 0 | 0 | identical |
| capability | 250 | 250 | identical |
| discard_alignment | 0 | 0 | identical |
| ext_range | 256 | 256 | identical |
| force_ro | 1 | 1 | identical |
| inflight | 0 0 | 0 0 | identical |
| range | 32 | 32 | identical |
| removable | 0 | 0 | identical |
| ro | 1 | 1 | identical |
| ro_lock_until_next_power_on | 1 | 1 | identical sysfs presentation |
| size sectors | 8,192 | 8,192 | identical |
| stat | eleven zero fields | eleven zero fields | identical |
| badblocks | empty | empty | identical successful empty result |

Boot-region queue equivalence:

| Queue attribute | mmcblk0boot0 | mmcblk0boot1 |
|---|---:|---:|
| add_random | 0 | 0 |
| dax | 0 | 0 |
| discard_granularity | 4,194,304 | 4,194,304 |
| discard_max_bytes | 2,199,023,255,040 | 2,199,023,255,040 |
| discard_max_hw_bytes | 2,199,023,255,040 | 2,199,023,255,040 |
| discard_zeroes_data | 0 | 0 |
| hw_sector_size | 512 | 512 |
| io_poll | 0 | 0 |
| iostats | 1 | 1 |
| logical_block_size | 512 | 512 |
| max_hw_sectors_kb | 512 | 512 |
| max_integrity_segments | 0 | 0 |
| max_sectors_kb | 512 | 512 |
| max_segment_size | 4,194,304 | 4,194,304 |
| max_segments | 1,024 | 1,024 |
| minimum_io_size | 512 | 512 |
| nomerges | 0 | 0 |
| nr_requests | 128 | 128 |
| optimal_io_size | 0 | 0 |
| physical_block_size | 512 | 512 |
| read_ahead_kb | 128 | 128 |
| rotational | 0 | 0 |
| rq_affinity | 1 | 1 |
| scheduler | noop deadline [cfq] | noop deadline [cfq] |
| write_cache | write back | write back |
| write_same_max_bytes | 0 | 0 |

Boot-region redundancy classification:
Physical redundancy            : two distinct eMMC boot hardware regions
Content redundancy             : exact full-object duplication
Android slot implication       : none
Android A/B conclusion         : preloader_a/preloader_b aliases do not establish
                                 Android A/B partitioning
Current selected hardware area : mmcblk0boot0 / preloader_a
Standby duplicate area         : mmcblk0boot1 / preloader_b
Classification                 : DUAL_HARDWARE_BOOT_REGION
                                 EXACT_DUPLICATE_PRELOADER_CONTENT
                                 NON_AB_ANDROID_SLOT_SEMANTICS


RPMB protected region:
Block device                   : /dev/block/mmcblk0rpmb
Kernel object class            : disk
Major:minor                    : 179:96
Node type                      : block device
Node mode                      : brw-------
Node owner                     : root:root
Node context                   : u:object_r:rpmb_block_device:s0
Size sectors                   : 32,768
Sector unit                    : 512 bytes
Size                           : 16,384 KiB
Size bytes                     : 16,777,216
EXT_CSD RPMB_SIZE_MULT[168]    : 0x80
Current software force_ro      : 0
Current block-device ro        : 0
ro_lock_until_next_power_on    : interface absent
Bad-block interface payload    : empty
Bad-block read status          : 0
Removable                      : 0
Live SHA256 attempt            : permission denied
Current ordinary block read    : read error: I/O error
Current ordinary-read status   : 1
Successful ordinary bytes read : none demonstrated
Role                           : Replay Protected Memory Block
Content state                  : protected; not hash-readable by normal block-device read
ROM action                     : NEVER TOUCH
Classification                 : HARDWARE_PROTECTED_RPMB_REGION
                                 ORDINARY_BLOCK_READ_REJECTED
                                 DO_NOT_HASH_AS_NORMAL_BLOCK_STORAGE
                                 DO_NOT_WRITE


RPMB block aliases:
Primary block node             : /dev/block/mmcblk0rpmb
Generic by-name alias          : /dev/block/by-name/mmcblk0rpmb
Generic alias target           : /dev/block/mmcblk0rpmb
Platform alias                 : /dev/block/platform/bootdevice/mmcblk0rpmb
Platform alias target          : /dev/block/mmcblk0rpmb
Alias object mode              : lrwxrwxrwx
Alias object owner             : root:root
Alias object context           : u:object_r:block_device:s0
Platform by-name directory     : no RPMB entry in
                                 /dev/block/platform/bootdevice/by-name/
Classification                 : CONFIRMED_RPMB_BLOCK_ALIAS_TOPOLOGY


RPMB protocol character interface:
Character device               : /dev/rpmb0
Node type                      : character device
Major:minor                    : 227:0
Node mode                      : crw-------
Node owner                     : root:root
Node context                   : u:object_r:teei_rpmb_device:s0
Kernel character major         : 227
/proc/devices registration     : 227 rpmb
Sysfs class                    : /sys/class/rpmb
Sysfs class object             : /sys/class/rpmb/rpmb0
Resolved sysfs object          : /sys/devices/virtual/rpmb/rpmb0
Sysfs dev value                : 227:0
Sysfs uevent MAJOR             : 227
Sysfs uevent MINOR             : 0
Sysfs uevent DEVNAME           : rpmb0
Block/character distinction    : /dev/rpmb0 is not a symlink and is not the
                                 mmcblk0rpmb block object
Role boundary                  : separate RPMB protocol/security interface
Deeper protocol ownership      : Section 11.4 — RPMB and /dev/sec hardware boundary
Classification                 : CONFIRMED_RPMB_CHARACTER_INTERFACE
                                 CROSS_SECTION_CONFIRMED_SECURITY_BOUNDARY


RPMB block-interface queue state:

| Queue attribute | mmcblk0rpmb |
|---|---:|
| add_random | 0 |
| dax | 0 |
| discard_granularity | 4,194,304 |
| discard_max_bytes | 2,199,023,255,040 |
| discard_max_hw_bytes | 2,199,023,255,040 |
| discard_zeroes_data | 0 |
| hw_sector_size | 512 |
| io_poll | 0 |
| iostats | 1 |
| logical_block_size | 512 |
| max_hw_sectors_kb | 512 |
| max_integrity_segments | 0 |
| max_sectors_kb | 512 |
| max_segment_size | 4,194,304 |
| max_segments | 1,024 |
| minimum_io_size | 512 |
| nomerges | 0 |
| nr_requests | 128 |
| optimal_io_size | 0 |
| physical_block_size | 512 |
| read_ahead_kb | 128 |
| rotational | 0 |
| rq_affinity | 1 |
| scheduler | noop deadline [cfq] |
| write_cache | write back |
| write_same_max_bytes | 0 |

RPMB block-interface scalar state:
alignment_offset               : 0
capability                     : 250
discard_alignment              : 0
ext_range                      : 256
force_ro                       : 0
inflight                       : 0 0
range                          : 32
removable                      : 0
ro                             : 0
size sectors                   : 32,768
stat                           : eleven zero fields
badblocks                      : empty successful result


Layered divergence register:

Divergence 2.2-D01:
Layer A                        : boot0 sysfs ro_lock_until_next_power_on = 1
Layer B                        : boot1 sysfs ro_lock_until_next_power_on = 1
Layer C                        : EXT_CSD BOOT_WP_STATUS[174] = 0x01
Resolved hardware status       : boot region 0 power-on protected;
                                 boot region 1 not power-on protected
Kernel presentation mechanism  : OPEN_BOUNDARY
Database treatment             : retain sysfs values and area-specific EXT_CSD
                                 status side by side; do not promote the shared
                                 sysfs presentation over the hardware status byte
Classification                 : LAYERED_INTERFACE_DIVERGENCE

Divergence 2.2-D02:
Earlier operation              : SHA256 attempt
Earlier result                 : permission denied
Current operation              : one-sector ordinary block read
Current result                 : read error: I/O error
Current status                 : 1
Earlier execution provenance   : OPEN_BOUNDARY
Database treatment             : preserve both verified access results; canonical
                                 functional conclusion is that normal block-style
                                 content acquisition is unavailable
Classification                 : ACCESS_RESULT_DIVERGENCE
                                 FUNCTIONAL_CONCLUSION_CLOSED


Topology rule:
mmcblk0boot0, mmcblk0boot1, and mmcblk0rpmb are eMMC hardware regions.
They are not GPT partitions.
preloader_a and preloader_b by-name aliases resolve to eMMC boot hardware
partitions, not mmcblk0p* nodes.
The user area / mmcblk0 is the sole GPT-bearing eMMC hardware region.
The two boot hardware regions contain exact duplicate EMMC_BOOT-formatted
preloader content.
PARTITION_CONFIG selects mmcblk0boot0 / preloader_a as the active hardware
boot source.
RPMB is exposed through both a protected block object and a separate RPMB
character interface; neither is a GPT partition.

Final subsection classification:
User area                     : VERIFIED_GPT_BEARING_EMMC_USER_AREA
Boot hardware region 0        : VERIFIED_ACTIVE_EMMC_BOOT_REGION
Boot hardware region 1        : VERIFIED_REDUNDANT_DUPLICATE_BOOT_REGION
Boot-region equivalence       : MULTI_WITNESS_CONFIRMED_FULL_OBJECT_EQUIVALENCE
Boot selection                : VERIFIED_EXT_CSD_BOOT_REGION_0_SELECTION
Boot-region write protection  : VERIFIED_AREA_SPECIFIC_POWER_ON_WP_STATUS
RPMB physical region          : VERIFIED_HARDWARE_PROTECTED_RPMB_REGION
RPMB block interface          : VERIFIED_ORDINARY_BLOCK_READ_REJECTED
RPMB character interface      : VERIFIED_RPMB_PROTOCOL_CHARACTER_INTERFACE
GPT relationship              : VERIFIED_NON_GPT_HARDWARE_REGIONS
Subsection 2.2 status         : CLOSED_WITH_BOUNDED_LAYER_DIVERGENCES

################################################################################
################################################################################

# 2.3 — GPT STATE

################################################################################
################################################################################

Subsection scope:
Included                       : PMBR bytes; primary GPT header and entry-array decode;
                                  all 128 entry slots; all 38 active entries; usable-LBA
                                  topology; tail metadata; CRC validation; kernel partition
                                  realization; GPT-facing preloader and LK static capability
Excluded                       : partition payload contents; filesystems; by-name aliases;
                                  dynamic-super metadata; AVB payloads; boot-image contents;
                                  partition-role analysis beyond GPT names; build, repair,
                                  or modification actions
Cross-section ownership        : 1.7, 1.10, 2.2, 2.4, 2.5

Whole-device geometry:
Whole-device sectors           : 244,277,248
Logical block size             : 512 bytes
Physical block size            : 512 bytes
Hardware sector size           : 512 bytes
Final physical LBA             : 244,277,247
Classification                 : MULTI_WITNESS_CONFIRMED

Protective MBR — LBA 0:
Partition entry offset          : bytes 0x01BE-0x01CD
Boot indicator                  : 0x00
Partition type                  : 0xEE
Starting CHS                    : 00 00 00
Starting LBA                    : 1
Sector count                    : 244,277,247
Ending CHS                      : 00 00 00
MBR signature                   : 55 AA at bytes 0x01FE-0x01FF
Additional MBR entries (2-4)    : zero-filled
Hybrid MBR entries              : none present
Classification                  : VERIFIED_PROTECTIVE_MBR

Primary GPT header — LBA 1:
Signature                       : EFI PART
Revision                        : 0x00010000
Header size                     : 92 bytes
Stored header CRC32             : 0x56FCDD37
Calculated header CRC32         : 0x56FCDD37
Header CRC match                : YES
Current LBA (embedded)          : 1
Alternate LBA (embedded)        : 244,277,247
First usable LBA                : 34
Last usable LBA                 : 244,277,214
Disk GUID                       : 00000000-0000-0000-0000-000000000000
Disk GUID source mechanism      : OPEN_BOUNDARY
Partition-entry start LBA       : 2
Partition-entry count           : 128
Partition-entry size            : 128 bytes
Partition-array byte length     : 16,384 bytes
Partition-array sector length   : 32 sectors
Stored partition-array CRC32    : 0x3F3C3F6A
Calculated partition-array CRC32: 0x3F3C3F6A
Array CRC match                 : YES
Header padding (bytes 0x5C-0x1FF): zero-filled
Classification                  : VERIFIED_PRIMARY_GPT_HEADER_AND_ARRAY

Primary partition-entry array — LBA 2-33:
Declared entry count            : 128
Populated entries               : 38
Populated indices               : 1-38 (contiguous)
Unused indices                  : 39-128 (entirely zero-filled)
Active entry bytes               : 4,864
Active sector footprint          : LBA 2-10 (entries 1-36), LBA 11 bytes 0x000-0x0FF
                                   (entries 37-38)
Zero-filled remainder            : LBA 11 bytes 0x100-0x1FF, LBA 12-33
Entry ordering                   : ascending by start LBA
Overlaps                         : 0
Out-of-bounds entries            : 0
Duplicate unique GUIDs           : 0
Zero type/unique GUIDs           : 0
Common type GUID (all 38 entries): ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
Common type GUID raw bytes       : a2a0d0ebe5b9334487c068b6b72699c7
Type-GUID assignment mechanism   : OPEN_BOUNDARY
All entry attribute fields       : 0x0000000000000000
Leading usable gap                : LBA 34-63 (30 sectors, unallocated)
Trailing usable gap               : none
Terminal contiguity                : userdata -> otp -> flashinfo -> last usable LBA (exact)
Classification                     : VERIFIED_38_ENTRY_TOPOLOGY

GPT entry on-disk identity vs. kernel realization:
Geometry (start LBA, end LBA, sector count, size) is owned by Section 2.5 and is not
repeated here. This table records GPT metadata identity (unique GUID) against the live
kernel object it realizes.

# Idx  Name             Unique GUID (GPT entry)                Kernel Object    Name Match  PARTN Match  DEVTYPE
────────────────────────────────────────────────────────────────────────────────────────────────────────────────
1     boot_para        f57ad330-39c2-4488-9bb0-00cb43c9ccd4    mmcblk0p1        YES         YES          partition
2     recovery         fe686d97-3544-4a41-be21-167e25b61b6f    mmcblk0p2        YES         YES          partition
3     para             1cb143a8-b1a8-4b57-b251-945c5119e8fe    mmcblk0p3        YES         YES          partition
4     expdb            3b9e343b-cdc8-4d7f-9fa6-b6812e50ab62    mmcblk0p4        YES         YES          partition
5     vbmeta_system    5f6a2c79-6617-4b85-ac02-c2975a14d2d7    mmcblk0p5        YES         YES          partition
6     vbmeta_vendor    4ae2050b-5db5-4ff7-aad3-5730534be63d    mmcblk0p6        YES         YES          partition
7     frp              1f9b0939-e16b-4bc9-a5bc-dc2ee969d801    mmcblk0p7        YES         YES          partition
8     nvcfg            d722c721-0dee-4cb8-8a83-2c63cd1393c7    mmcblk0p8        YES         YES          partition
9     nvdata           e02179a8-ceb5-48a9-8831-4f1c9c5a8695    mmcblk0p9        YES         YES          partition
10    md_udc           84b09a81-fad2-41ac-890e-407c24975e74    mmcblk0p10       YES         YES          partition
11    metadata         e8f0a5ef-8d1b-42ea-9c2a-835cd77de363    mmcblk0p11       YES         YES          partition
12    protect1         d5f0e175-a6e1-4db7-94c0-f82ad032950b    mmcblk0p12       YES         YES          partition
13    protect2         1d9056e1-e139-4fca-8c0b-b75fd74d81c6    mmcblk0p13       YES         YES          partition
14    seccfg           7792210b-b6a8-45d5-ad91-3361ed14c608    mmcblk0p14       YES         YES          partition
15    sec1             138a6db9-1032-451d-91e9-0fa38ff94fbb    mmcblk0p15       YES         YES          partition
16    proinfo          756d934c-50e3-4c91-af46-02d824169ca7    mmcblk0p16       YES         YES          partition
17    md1img           a3f3c267-5521-42dd-a724-3bdec20c7c6f    mmcblk0p17       YES         YES          partition
18    spmfw            8c68cd2a-ccc9-4c5d-8b57-34ae9b2dd481    mmcblk0p18       YES         YES          partition
19    scp1             6a5cebf8-54a7-4b89-8d1d-c5eb140b095b    mmcblk0p19       YES         YES          partition
20    scp2             a0d65bf8-e8de-4107-9434-1d318c843d37    mmcblk0p20       YES         YES          partition
21    sspm_1           46f0c0bb-f227-4eb6-b82f-66408e13e36d    mmcblk0p21       YES         YES          partition
22    sspm_2           fbc2c131-6392-4217-b51e-548a6edb03d0    mmcblk0p22       YES         YES          partition
23    gz1              e195a981-e285-4734-8025-ec323e9589d9    mmcblk0p23       YES         YES          partition
24    gz2              e29052f8-5d3a-4e97-adb5-5f312ce6610a    mmcblk0p24       YES         YES          partition
25    nvram            9c3cabd7-a35d-4b45-8c57-b80775426b35    mmcblk0p25       YES         YES          partition
26    lk               e7099731-95a6-45a6-a1e5-1b6aba032cf1    mmcblk0p26       YES         YES          partition
27    lk2              8273e1ab-846f-4468-b999-ee2ea8e50a16    mmcblk0p27       YES         YES          partition
28    boot             d26472f1-9ebc-421d-ba14-311296457c90    mmcblk0p28       YES         YES          partition
29    logo             b72ccbe9-2055-46f4-a167-4a069c201738    mmcblk0p29       YES         YES          partition
30    dtbo             9c1520f3-c2c5-4b89-8242-fe4c61208a9e    mmcblk0p30       YES         YES          partition
31    tee1             902d5f3f-434a-4de7-8988-321e88c9b8aa    mmcblk0p31       YES         YES          partition
32    tee2             bece74c8-d8e2-4863-9bfe-5b0b66bb920f    mmcblk0p32       YES         YES          partition
33    super            ff1342cf-b7be-44d5-a25e-a435addd2702    mmcblk0p33       YES         YES          partition
34    vbmeta           a4da8f1b-fe07-433b-95cb-84a5f23e477b    mmcblk0p34       YES         YES          partition
35    cache            c2635e15-61aa-454e-9c40-ebe1bdf19b9b    mmcblk0p35       YES         YES          partition
36    userdata         4d2d1290-36a3-4f5d-afb4-319f8ab6dcd8    mmcblk0p36       YES         YES          partition
37    otp              fdce12f0-a7eb-40f7-8350-960972e6cb57    mmcblk0p37       YES         YES          partition
38    flashinfo        0fbbafa2-4aa9-4490-8983-5329328505fd    mmcblk0p38       YES         YES          partition

GPT-to-kernel realization summary:
GPT active entries               : 38
Kernel uevent records             : 38
Missing kernel indices              : none
Extra kernel indices                  : none
Name / PARTNAME matches                : 38 of 38
Index / PARTN matches                    : 38 of 38
DEVTYPE=partition records                  : 38 of 38
Kernel PARTUUID field present                : 0 of 38
by-partuuid /dev symlink search                : none found (find status 0, zero matches)
PARTUUID suppression/omission mechanism          : OPEN_BOUNDARY
Kernel start-LBA / sysfs match                     : 38 of 38
Kernel sector-count / sysfs match                    : 38 of 38
Kernel metadata copy consumed (primary LBA1 block
or relocated tail clone)                               : OPEN_BOUNDARY — the two 33-sector
                                                          copies are byte-identical, so
                                                          geometry realization alone cannot
                                                          distinguish which physical copy
                                                          was read
Classification                                            : MULTI_WITNESS_CONFIRMED

Tail region — LBA 244,277,215-244,277,247:
Byte range                      : exact copy of LBA 1-33 (33 sectors, 16,896 bytes)
Full comparison result          : all 16,896 bytes identical (cmp status 0)
Embedded my_lba in tail header  : 1 (does not describe the tail's own physical location)
Embedded alternate LBA in tail  : 244,277,247 (unchanged from primary)
Standard backup GPT header      : ABSENT
Standard backup entry array     : ABSENT
Final physical LBA (244,277,247): zero-filled
Penultimate LBA (244,277,246)   : zero-filled
Classification                  : EXACT_RELOCATED_PRIMARY_GPT_METADATA_CLONE
Tail-clone producer mechanism   : OPEN_BOUNDARY
Tail-clone consumer mechanism   : OPEN_BOUNDARY

Kernel partition-parser configuration:
CONFIG_BLOCK                    : y
CONFIG_MSDOS_PARTITION          : y
CONFIG_EFI_PARTITION            : y
CONFIG_PARTITION_ADVANCED       : not set
Classification                  : VERIFIED_KERNEL_PARTITION_PARSER_CONFIGURATION

Kernel symbol exposure:
efi_partition                   : exposed, global text symbol, address presented as zero
read_lba                        : exposed, local text symbol, address presented as zero
is_gpt_valid                    : exposed, local text symbol, address presented as zero
find_valid_gpt                  : not found by exact name
compare_gpts                    : not found by exact name
alloc_read_gpt_entries           : not found by exact name
read_partitions                  : not found by exact name
Symbol address masking mechanism : OPEN_BOUNDARY
Absence-of-name implication       : does not prove absence of equivalent logic; functions
                                     may be inlined, renamed, static, or optimized without an
                                     exposed kallsyms row

Preloader (mmcblk0boot0, active boot region) — GPT capability surface:
File size                        : 4,194,304 bytes
Leading signature                : EMMC_BOOT
Host file-type recognition       : data (no recognized container by host `file` database)
Static GPT capability strings    : get_gpt_header, parse_gpt_header, header signature/CRC/
                                    my_lba validators, entry-array CRC validator, valid-GPT
                                    success/failure paths
Classification                   : STATIC_BINARY_CAPABILITY_PRESENT
Runtime execution proof          : OPEN_BOUNDARY
Selected GPT copy (primary/tail) : OPEN_BOUNDARY

LK partition (mmcblk0p26 / by-name/lk) — GPT capability surface:
File size                        : 1,048,576 bytes
Leading bytes                    : 88 16 88 58 dc 05 0b 00, ASCII "lk" at offset 0x08
Host file-type recognition       : data (no recognized container by host `file` database)
Static GPT capability strings    : pgpt, sgpt, parse_gpt_header, read_gpt, write_primary_gpt,
                                    write_secondary_gpt, write pmbr/pheader/pentries/sheader,
                                    partition UUID parser and name/start/count/uuid
                                    diagnostic output
Classification                   : STATIC_BINARY_CAPABILITY_PRESENT (parse AND write,
                                    primary AND secondary)
Runtime update execution         : OPEN_BOUNDARY
Tail-clone attribution to LK     : OPEN_BOUNDARY

Divergence register (still-open items only):

Divergence 2.3-D01 — tail metadata clone:
Physical tail location           : LBA 244,277,215-244,277,247
Physical content                 : exact 16,896-byte clone of primary LBA 1-33, in primary
                                    order (header then entries), not standard alternate-GPT
                                    order (entries then final-LBA header)
Embedded my_lba                  : 1 (matches primary's location, not the tail's own)
Declared alternate LBA (244,277,247) actual content : zero-filled, not a backup header
Producer / consumer mechanism    : OPEN_BOUNDARY
Classification                   : ANOMALY_RETAINED / SOURCE_MECHANISM_OPEN

Divergence 2.3-D02 — GUID population asymmetry:
Disk GUID                        : all-zero
Active-entry unique GUIDs        : 38 nonzero, all distinct
Active-entry type GUID           : one value shared across all 38 entries despite
                                    heterogeneous partition roles
Producer rationale for both      : OPEN_BOUNDARY
Classification                   : ANOMALY_RETAINED / SOURCE_MECHANISM_OPEN

Divergence 2.3-D03 — on-disk GUID vs. kernel projection:
On-disk unique GUIDs             : 38 nonzero, verified
Kernel PARTUUID exposure         : absent from all 38 captured uevents
/dev by-partuuid alias           : none found
Suppression/omission mechanism   : OPEN_BOUNDARY
Classification                   : ANOMALY_RETAINED / SOURCE_MECHANISM_OPEN

Divergence 2.3-D04 — structural validation conflict:
Tail-header embedded my_lba      : 1, mismatched against its physical LBA 244,277,215
Preloader my_lba validator       : proven present (static string: check header,
                                    err(my_lba 0x%llx!=0x%llx))
LK my_lba validator              : proven present (same check, static string confirmed)
Acceptance/rejection outcome     : OPEN_BOUNDARY — requires control-flow recovery, not
                                    further string search
Classification                   : STRUCTURAL_VALIDATION_CONFLICT

Unresolved lexical collision:
Strings observed                 : apxgpt@10008000, mediatek,apxgpt (found inside the LK
                                    binary string dump)
Object class                     : NOT DECODED
Relation to storage GPT          : NOT ESTABLISHED — the substring "gpt" alone does not
                                    prove membership in GUID partition-table handling; this
                                    is not merged with any storage GPT record
Classification                   : TERM_COLLISION_OPEN
Transfer owner                   : Section 1.7 (pending object-class decode)

Cross-section transfers (not owned by this subsection):
EMMC_REGION_TOPOLOGY             : boot0/boot1/RPMB region relationship -> owned by 2.2
BY_NAME_ALIASES                  : partition by-name symlink map -> owned by 2.4
PARTITION_CONTENTS               : filesystem/image/payload identity -> owned by 2.5
BOOTLOADER_PLATFORM              : preloader/LK content beyond GPT-facing capability strings
                                    -> owned by 1.10
FINAL_USABLE_PAYLOAD             : LBA 244,277,214 zero-fill is inside flashinfo partition
                                    payload, not GPT metadata -> owned by 2.5
PRELOADER_HEADER_STRUCTURE       : EMMC_BOOT leading-byte container structure -> owned by
                                    1.10
LK_HEADER_STRUCTURE               : LK leading-byte container structure -> owned by 1.10
APXGPT_TERM                       : apxgpt@10008000 / mediatek,apxgpt object class -> owned
                                     by 1.7

Open boundaries carried out of this subsection:
1. Type-GUID assignment mechanism — why all 38 entries share one type GUID
2. Disk GUID source mechanism — why the disk GUID is left all-zero
3. Tail-clone producer mechanism — what wrote the relocated primary-metadata copy, and when
4. Tail-clone consumer mechanism — whether anything currently reads or relies on the tail
   copy
5. Kernel physical-copy selection — whether the parser consumed the LBA1 block, the tail
   clone, or has no distinguishing behavior between them
6. Kernel kallsyms address-masking mechanism — why efi_partition/read_lba/is_gpt_valid
   addresses are presented as zero
7. Kernel PARTUUID suppression/omission mechanism — why on-disk unique GUIDs do not project
   into the kernel uevent interface or a by-partuuid alias
8. Preloader and LK runtime GPT-parsing execution proof — static capability is proven;
   which code path actually executed on this device is not
9. Preloader's selected GPT copy when both primary and tail are structurally valid
10. Whether LK's proven GPT-write code path (write_primary_gpt / write_secondary_gpt) has
    ever executed against this device
11. Structural-validation-conflict outcome — both bootloaders contain proven my_lba
    mismatch-check code; whether either one flags, rejects, or silently accepts the tail
    clone's non-matching embedded my_lba is unresolved
12. apxgpt / mediatek,apxgpt object-class identity and its relation, or non-relation, to
    storage GPT metadata

Subsection closure:
Canonical conclusion             : The mmcblk0 user area contains a protective MBR, a
                                    CRC-valid primary GPT at LBA 1-33 with 38 contiguous
                                    active entries exactly realized by the live kernel, and
                                    an exact primary-metadata clone occupying the final 33
                                    LBAs. A standard alternate GPT is absent.
Open boundary count               : 12
Subsection 2.3 status             : CLOSED_WITH_BOUNDED_OPEN_ITEMS

################################################################################
################################################################################
# 2.4 — BY-NAME TOPOLOGY
################################################################################
################################################################################

Subsection control:
Title                          : By-Name Topology
Scope                          : physical block-alias namespaces, alternate namespace
                                 paths, GPT PARTNAME aliases, eMMC DEVNAME aliases,
                                 platform-direct aliases, preloader aliases, alias
                                 generation, live object metadata, and bounded
                                 namespace-label divergences
Status                         : CLOSED_WITH_BOUNDED_OPEN_ITEMS

Canonical namespace topology:
Physical by-name directories   : 2
Root by-name directory          : /dev/block/by-name
Root directory inode            : 239
Root directory mode             : drwxr-xr-x
Root directory owner            : root:root
Root directory context          : u:object_r:block_device:s0
Platform by-name directory      : /dev/block/platform/bootdevice/by-name
Platform directory inode        : 237
Platform directory mode         : drwxr-xr-x
Platform directory owner        : root:root
Platform directory context      : u:object_r:block_device:s0
Alternate platform path         : /dev/block/bootdevice/by-name
Alternate path inode            : 237
Alternate path relationship     : same directory object as
                                 /dev/block/platform/bootdevice/by-name
Parent namespace alias          : /dev/block/bootdevice
Parent alias target             : /dev/block/platform/bootdevice
Parent alias mode               : lrwxrwxrwx
Parent alias owner              : root:root
Parent alias context            : u:object_r:block_device:s0
Physical /dev platform path     : /dev/block/platform/bootdevice
Address-form platform path      : /dev/block/platform/11230000.mmc absent
Classification                 : MULTI_WITNESS_CONFIRMED_BLOCK_ALIAS_NAMESPACE_TOPOLOGY

Boot-device controller binding:
Boot-device property            : bootdevice,11230000.mmc
ro.boot.bootdevice              : empty
ro.boot.slot_suffix             : empty
ro.boot.dynamic_partitions      : true
Controller sysfs object         : /sys/devices/platform/bootdevice
Controller driver               : mtk-msdc
Device-tree node                : /msdc@11230000
Device-tree compatible          : mediatek,msdc
MMC card path                   : /sys/devices/platform/bootdevice/mmc_host/mmc0/mmc0:0001
Classification                 : LIVE_PROVEN_BOOTDEVICE_CONTROLLER_BINDING

Namespace composition:

| Namespace | GPT PARTNAME aliases | eMMC DEVNAME aliases | Init-created aliases | Total aliases | Unique targets |
|---|---:|---:|---:|---:|---:|
| /dev/block/by-name | 38 | 4 | 0 | 42 | 42 |
| /dev/block/platform/bootdevice/by-name | 38 | 0 | 2 | 40 | 40 |
| /dev/block/platform/bootdevice/mmcblk0* | 38 partition-node aliases | 4 disk-node aliases | 0 | 42 | 42 |

Namespace set relationships:
Common root/platform aliases    : 38
Common alias class              : GPT PARTNAME
Common target mismatches        : 0
Common readlink errors          : 0
Root-only aliases               : 4
Root-only names                 : mmcblk0, mmcblk0boot0, mmcblk0boot1, mmcblk0rpmb
Platform-only aliases           : 2
Platform-only names             : preloader_a, preloader_b
Hidden root entries             : 0
Hidden platform entries         : 0
Classification                 : COMPLETE_NAMESPACE_SET_RECONCILIATION_CONFIRMED

Alias integrity:

| Namespace | Objects tested | Non-symlink objects | Broken aliases | Non-block targets | Read errors |
|---|---:|---:|---:|---:|---:|
| /dev/block/by-name | 42 | 0 | 0 | 0 | 0 |
| /dev/block/platform/bootdevice/by-name | 40 | 0 | 0 | 0 | 0 |
| /dev/block/platform/bootdevice/mmcblk0* | 42 | 0 | 0 | 0 | 0 |

Count equations:
Root by-name                  : 38 GPT PARTNAME + 4 eMMC DEVNAME = 42
Platform by-name              : 38 GPT PARTNAME + 2 preloader aliases = 40
Platform direct               : 38 partition nodes + 4 disk nodes = 42
Root equation result           : MATCH
Platform equation result       : MATCH
Platform-direct result         : MATCH
Canonical unresolved records   : 0
Canonical record errors        : 0
Classification                 : COMPLETE_TARGET_INTEGRITY_CONFIRMED

Canonical GPT PARTNAME alias map:

| PARTN | Alias | Target | Major:minor | Root alias context | Platform alias context | Target mode / owner | Target context |
|---:|---|---|---:|---|---|---|---|
| 1 | boot_para | /dev/block/mmcblk0p1 | 179:1 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:boot_para_block_device:s0 |
| 2 | recovery | /dev/block/mmcblk0p2 | 179:2 | u:object_r:block_device:s0 | u:object_r:recovery_block_device:s0 | brw------- / root:root | u:object_r:recovery_block_device:s0 |
| 3 | para | /dev/block/mmcblk0p3 | 179:3 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:para_block_device:s0 |
| 4 | expdb | /dev/block/mmcblk0p4 | 179:4 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:expdb_block_device:s0 |
| 5 | vbmeta_system | /dev/block/mmcblk0p5 | 179:5 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | brw------- / root:root | u:object_r:vbmeta_block_device:s0 |
| 6 | vbmeta_vendor | /dev/block/mmcblk0p6 | 179:6 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | brw------- / root:root | u:object_r:vbmeta_block_device:s0 |
| 7 | frp | /dev/block/mmcblk0p7 | 179:7 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:frp_block_device:s0 |
| 8 | nvcfg | /dev/block/mmcblk0p8 | 179:8 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:nvcfg_block_device:s0 |
| 9 | nvdata | /dev/block/mmcblk0p9 | 179:9 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:nvdata_device:s0 |
| 10 | md_udc | /dev/block/mmcblk0p10 | 179:10 | u:object_r:metadata_block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:metadata_block_device:s0 |
| 11 | metadata | /dev/block/mmcblk0p11 | 179:11 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:metadata_block_device:s0 |
| 12 | protect1 | /dev/block/mmcblk0p12 | 179:12 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:protect1_block_device:s0 |
| 13 | protect2 | /dev/block/mmcblk0p13 | 179:13 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:protect2_block_device:s0 |
| 14 | seccfg | /dev/block/mmcblk0p14 | 179:14 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:seccfg_block_device:s0 |
| 15 | sec1 | /dev/block/mmcblk0p15 | 179:15 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:sec1_block_device:s0 |
| 16 | proinfo | /dev/block/mmcblk0p16 | 179:16 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:nvram_device:s0 |
| 17 | md1img | /dev/block/mmcblk0p17 | 179:17 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-r----- / root:system | u:object_r:md_block_device:s0 |
| 18 | spmfw | /dev/block/mmcblk0p18 | 179:18 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:spmfw_block_device:s0 |
| 19 | scp1 | /dev/block/mmcblk0p19 | 179:19 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 20 | scp2 | /dev/block/mmcblk0p20 | 179:20 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 21 | sspm_1 | /dev/block/mmcblk0p21 | 179:21 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 22 | sspm_2 | /dev/block/mmcblk0p22 | 179:22 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 23 | gz1 | /dev/block/mmcblk0p23 | 179:23 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 24 | gz2 | /dev/block/mmcblk0p24 | 179:24 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 25 | nvram | /dev/block/mmcblk0p25 | 179:25 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:nvram_device:s0 |
| 26 | lk | /dev/block/mmcblk0p26 | 179:26 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:lk_block_device:s0 |
| 27 | lk2 | /dev/block/mmcblk0p27 | 179:27 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |
| 28 | boot | /dev/block/mmcblk0p28 | 179:28 | u:object_r:block_device:s0 | u:object_r:boot_block_device:s0 | brw------- / root:root | u:object_r:boot_block_device:s0 |
| 29 | logo | /dev/block/mmcblk0p29 | 179:29 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:logo_block_device:s0 |
| 30 | dtbo | /dev/block/mmcblk0p30 | 179:30 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:dtbo_block_device:s0 |
| 31 | tee1 | /dev/block/mmcblk0p31 | 179:31 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:tee_block_device:s0 |
| 32 | tee2 | /dev/block/mmcblk0p32 | 259:0 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:tee_block_device:s0 |
| 33 | super | /dev/block/mmcblk0p33 | 259:1 | u:object_r:block_device:s0 | u:object_r:super_block_device:s0 | brw------- / root:root | u:object_r:super_block_device:s0 |
| 34 | vbmeta | /dev/block/mmcblk0p34 | 259:2 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | brw------- / root:root | u:object_r:vbmeta_block_device:s0 |
| 35 | cache | /dev/block/mmcblk0p35 | 259:3 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:cache_block_device:s0 |
| 36 | userdata | /dev/block/mmcblk0p36 | 259:4 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:userdata_block_device:s0 |
| 37 | otp | /dev/block/mmcblk0p37 | 259:5 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:otp_part_block_device:s0 |
| 38 | flashinfo | /dev/block/mmcblk0p38 | 259:6 | u:object_r:block_device:s0 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:block_device:s0 |

GPT alias correlation:
GPT active partition names      : 38
Root PARTNAME matches           : 38 of 38
Platform PARTNAME matches       : 38 of 38
Root target matches             : 38 of 38
Platform target matches         : 38 of 38
Missing aliases                 : 0
Target mismatches               : 0
Uevent read errors              : 0
Readlink errors                 : 0
Classification                 : KERNEL_PARTNAME_TO_ALIAS_EQUIVALENCE_CONFIRMED

Root-only eMMC DEVNAME aliases:

| Alias | Target | DEVTYPE | Major:minor | Alias context | Target mode / owner | Target context |
|---|---|---|---:|---|---|---|
| mmcblk0 | /dev/block/mmcblk0 | disk | 179:0 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:bootdevice_block_device:s0 |
| mmcblk0boot0 | /dev/block/mmcblk0boot0 | disk | 179:32 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:preloader_block_device:s0 |
| mmcblk0boot1 | /dev/block/mmcblk0boot1 | disk | 179:64 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:preloader_block_device:s0 |
| mmcblk0rpmb | /dev/block/mmcblk0rpmb | disk | 179:96 | u:object_r:block_device:s0 | brw------- / root:root | u:object_r:rpmb_block_device:s0 |

Root eMMC DEVNAME correlation:
Kernel disk objects             : 4
DEVNAME matches                 : 4 of 4
Alias target matches            : 4 of 4
Missing aliases                 : 0
Target mismatches               : 0
Read errors                     : 0
Classification                 : KERNEL_DEVNAME_TO_ALIAS_EQUIVALENCE_CONFIRMED

Platform-only preloader aliases:

| Alias | Target | Major:minor | Alias context | Target mode / owner | Target context | Creation file | Trigger |
|---|---|---:|---|---|---|---|---|
| preloader_a | /dev/block/mmcblk0boot0 | 179:32 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:preloader_block_device:s0 | /vendor/etc/init/hw/init.mt6762.rc | on post-fs |
| preloader_b | /dev/block/mmcblk0boot1 | 179:64 | u:object_r:block_device:s0 | brw-rw---- / root:system | u:object_r:preloader_block_device:s0 | /vendor/etc/init/hw/init.mt6762.rc | on post-fs |

Preloader alias classification : ACTIVE_INIT_GENERATED_EMMC_BOOT_REGION_ALIASES

Platform-direct block aliases:

| Alias object | Target | Major:minor | Target class | Alias context |
|---|---|---:|---|---|
| /dev/block/platform/bootdevice/mmcblk0 | /dev/block/mmcblk0 | 179:0 | disk | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0boot0 | /dev/block/mmcblk0boot0 | 179:32 | disk | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0boot1 | /dev/block/mmcblk0boot1 | 179:64 | disk | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0rpmb | /dev/block/mmcblk0rpmb | 179:96 | disk | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p1 | /dev/block/mmcblk0p1 | 179:1 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p2 | /dev/block/mmcblk0p2 | 179:2 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p3 | /dev/block/mmcblk0p3 | 179:3 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p4 | /dev/block/mmcblk0p4 | 179:4 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p5 | /dev/block/mmcblk0p5 | 179:5 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p6 | /dev/block/mmcblk0p6 | 179:6 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p7 | /dev/block/mmcblk0p7 | 179:7 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p8 | /dev/block/mmcblk0p8 | 179:8 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p9 | /dev/block/mmcblk0p9 | 179:9 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p10 | /dev/block/mmcblk0p10 | 179:10 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p11 | /dev/block/mmcblk0p11 | 179:11 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p12 | /dev/block/mmcblk0p12 | 179:12 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p13 | /dev/block/mmcblk0p13 | 179:13 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p14 | /dev/block/mmcblk0p14 | 179:14 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p15 | /dev/block/mmcblk0p15 | 179:15 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p16 | /dev/block/mmcblk0p16 | 179:16 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p17 | /dev/block/mmcblk0p17 | 179:17 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p18 | /dev/block/mmcblk0p18 | 179:18 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p19 | /dev/block/mmcblk0p19 | 179:19 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p20 | /dev/block/mmcblk0p20 | 179:20 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p21 | /dev/block/mmcblk0p21 | 179:21 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p22 | /dev/block/mmcblk0p22 | 179:22 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p23 | /dev/block/mmcblk0p23 | 179:23 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p24 | /dev/block/mmcblk0p24 | 179:24 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p25 | /dev/block/mmcblk0p25 | 179:25 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p26 | /dev/block/mmcblk0p26 | 179:26 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p27 | /dev/block/mmcblk0p27 | 179:27 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p28 | /dev/block/mmcblk0p28 | 179:28 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p29 | /dev/block/mmcblk0p29 | 179:29 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p30 | /dev/block/mmcblk0p30 | 179:30 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p31 | /dev/block/mmcblk0p31 | 179:31 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p32 | /dev/block/mmcblk0p32 | 259:0 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p33 | /dev/block/mmcblk0p33 | 259:1 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p34 | /dev/block/mmcblk0p34 | 259:2 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p35 | /dev/block/mmcblk0p35 | 259:3 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p36 | /dev/block/mmcblk0p36 | 259:4 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p37 | /dev/block/mmcblk0p37 | 259:5 | partition | u:object_r:block_device:s0 |
| /dev/block/platform/bootdevice/mmcblk0p38 | /dev/block/mmcblk0p38 | 259:6 | partition | u:object_r:block_device:s0 |

Platform-direct correlation:
Direct aliases                 : 42
Exact target-string matches     : 42 of 42
Non-symlink objects             : 0
Broken aliases                 : 0
Non-block targets              : 0
Target mismatches              : 0
Read errors                    : 0
Classification                 : COMPLETE_PLATFORM_DIRECT_ALIAS_MAP_CONFIRMED

Alias-generation mechanism:
Running daemon name             : /system/bin/ueventd
Running executable              : /system/bin/init
/system/bin/ueventd             : symlink to init
/init                           : symlink to /system/bin/init
Kernel partition inputs         : DEVNAME, DEVTYPE, PARTN, PARTNAME, MAJOR, MINOR
Native binary strings           : PARTN=, PARTNAME=, MAJOR=, /dev/block/by-name/,
                                 /bus/platform, Linking partition, /by-name/
Native string offsets           : 0x0000da4e, 0x0000f379, 0x000105da,
                                 0x000107c4, 0x00011d81, 0x00011d8f, 0x00011da3
Root GPT alias generation       : native init/ueventd block-event handling
Platform GPT alias generation   : native init/ueventd block-event handling
Root eMMC alias generation      : native init/ueventd DEVNAME handling
Platform-direct generation      : native init/ueventd block-device handling
Exact internal function         : OPEN_BOUNDARY
Classification                 : NATIVE_INIT_UEVENTD_ALIAS_GENERATION_CONFIRMED

Active init selection:
ro.hardware                     : mt6762
ro.boot.hardware                : mt6762
Boot mode                       : normal
/init.rc import                 : /vendor/etc/init/hw/init.${ro.hardware}.rc
Resolved hardware file          : /vendor/etc/init/hw/init.mt6762.rc
/init.mt6762.rc                 : absent
/init.mt6765.rc                 : absent
/vendor/etc/init/hw/init.mt6762.rc: present, 39,950 bytes
/vendor/etc/init/hw/init.mt6765.rc: present, 39,950 bytes
Active parent alias command     : symlink /dev/block/platform/bootdevice
                                 /dev/block/bootdevice
Parent alias trigger            : on init
Active preloader commands       : symlink /dev/block/mmcblk0boot0
                                 /dev/block/platform/bootdevice/by-name/preloader_a
                                 symlink /dev/block/mmcblk0boot1
                                 /dev/block/platform/bootdevice/by-name/preloader_b
Preloader alias trigger         : on post-fs
Classification                 : ACTIVE_MT6762_INIT_ALIAS_PATH_CONFIRMED

Static alias declarations and live realization:

| Declaration | Trigger / mode | Live result | Classification |
|---|---|---|---|
| `/dev/block/bootdevice -> /dev/block/platform/bootdevice` | `on init`, active mt6762 file | populated | DECLARED_POPULATED_ACTIVE |
| `preloader_a -> /dev/block/mmcblk0boot0` | `on post-fs`, active mt6762 file | populated | DECLARED_POPULATED_ACTIVE |
| `preloader_b -> /dev/block/mmcblk0boot1` | `on post-fs`, active mt6762 file | populated | DECLARED_POPULATED_ACTIVE |
| `symlink /dev/block/sda /dev/block/mmcblk0boot0` | `on post-fs`, active mt6762 file | `/dev/block/sda` absent; link path is an actual block node | STATIC_DECLARATION_NOT_REALIZED |
| `symlink /dev/block/sdb /dev/block/mmcblk0boot1` | `on post-fs`, active mt6762 file | `/dev/block/sdb` absent; link path is an actual block node | STATIC_DECLARATION_NOT_REALIZED |
| mt6765 bootdevice and preloader directives | mt6765 hardware file | file present; not selected by `ro.hardware=mt6762` | STATIC_DECLARATION_RUNTIME_INACTIVE |
| `/dev/bootimg`, `/dev/recovery`, `/dev/sec_ro`, `/dev/kb`, `/dev/dkb`, `/dev/seccfg`, `/dev/pro_info`, `/dev/nvram`, `/dev/misc`, `/dev/logo` | meta and factory init files | absent in captured normal runtime | ALTERNATE_MODE_DECLARATIONS_NOT_POPULATED |

Static by-name consumers:
Root super alias               : /system/etc/init/lpdumpd.rc
Platform cache alias           : e2fsck and ext4 mount source in init.mt6762.rc
Platform nvcfg alias           : ext4 mount source in init.mt6762.rc
Platform userdata alias        : data mount source in meta_init.rc and factory_init.rc
Classification                 : STATIC_ALIAS_CONSUMER_DECLARATIONS_PRESENT

Live SELinux alias topology:
Root directory context          : u:object_r:block_device:s0
Platform directory context      : u:object_r:block_device:s0
Platform-direct alias context   : u:object_r:block_device:s0 for 42 of 42
Root generic aliases            : 41
Root specialized alias          : md_udc
Root md_udc context             : u:object_r:metadata_block_device:s0
Platform specialized aliases    : boot, recovery, super, vbmeta,
                                 vbmeta_system, vbmeta_vendor
Platform generic aliases        : 34
Common aliases with different root/platform contexts: 7
Classification                 : MULTI_LAYER_ALIAS_LABEL_TOPOLOGY_CONFIRMED

Common-alias context asymmetry:

| Alias | Target | Root alias context | Platform alias context | Target context |
|---|---|---|---|---|
| boot | /dev/block/mmcblk0p28 | u:object_r:block_device:s0 | u:object_r:boot_block_device:s0 | u:object_r:boot_block_device:s0 |
| md_udc | /dev/block/mmcblk0p10 | u:object_r:metadata_block_device:s0 | u:object_r:block_device:s0 | u:object_r:metadata_block_device:s0 |
| recovery | /dev/block/mmcblk0p2 | u:object_r:block_device:s0 | u:object_r:recovery_block_device:s0 | u:object_r:recovery_block_device:s0 |
| super | /dev/block/mmcblk0p33 | u:object_r:block_device:s0 | u:object_r:super_block_device:s0 | u:object_r:super_block_device:s0 |
| vbmeta | /dev/block/mmcblk0p34 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | u:object_r:vbmeta_block_device:s0 |
| vbmeta_system | /dev/block/mmcblk0p5 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | u:object_r:vbmeta_block_device:s0 |
| vbmeta_vendor | /dev/block/mmcblk0p6 | u:object_r:block_device:s0 | u:object_r:vbmeta_block_device:s0 | u:object_r:vbmeta_block_device:s0 |

Platform alias active-default divergence:
Live generic platform aliases  : 23
Active default specialized paths: 23
Dry-run modification mode       : no changes performed
Restorecon status               : 0 for all 23 paths

| Alias | Live context | Active default context | Target context |
|---|---|---|---|
| boot_para | u:object_r:block_device:s0 | u:object_r:boot_para_block_device:s0 | u:object_r:boot_para_block_device:s0 |
| cache | u:object_r:block_device:s0 | u:object_r:cache_block_device:s0 | u:object_r:cache_block_device:s0 |
| dtbo | u:object_r:block_device:s0 | u:object_r:dtbo_block_device:s0 | u:object_r:dtbo_block_device:s0 |
| expdb | u:object_r:block_device:s0 | u:object_r:expdb_block_device:s0 | u:object_r:expdb_block_device:s0 |
| frp | u:object_r:block_device:s0 | u:object_r:frp_block_device:s0 | u:object_r:frp_block_device:s0 |
| lk | u:object_r:block_device:s0 | u:object_r:lk_block_device:s0 | u:object_r:lk_block_device:s0 |
| logo | u:object_r:block_device:s0 | u:object_r:logo_block_device:s0 | u:object_r:logo_block_device:s0 |
| md1img | u:object_r:block_device:s0 | u:object_r:md_block_device:s0 | u:object_r:md_block_device:s0 |
| metadata | u:object_r:block_device:s0 | u:object_r:metadata_block_device:s0 | u:object_r:metadata_block_device:s0 |
| nvcfg | u:object_r:block_device:s0 | u:object_r:nvcfg_block_device:s0 | u:object_r:nvcfg_block_device:s0 |
| nvdata | u:object_r:block_device:s0 | u:object_r:nvdata_device:s0 | u:object_r:nvdata_device:s0 |
| nvram | u:object_r:block_device:s0 | u:object_r:nvram_device:s0 | u:object_r:nvram_device:s0 |
| otp | u:object_r:block_device:s0 | u:object_r:otp_part_block_device:s0 | u:object_r:otp_part_block_device:s0 |
| para | u:object_r:block_device:s0 | u:object_r:para_block_device:s0 | u:object_r:para_block_device:s0 |
| proinfo | u:object_r:block_device:s0 | u:object_r:nvram_device:s0 | u:object_r:nvram_device:s0 |
| protect1 | u:object_r:block_device:s0 | u:object_r:protect1_block_device:s0 | u:object_r:protect1_block_device:s0 |
| protect2 | u:object_r:block_device:s0 | u:object_r:protect2_block_device:s0 | u:object_r:protect2_block_device:s0 |
| sec1 | u:object_r:block_device:s0 | u:object_r:sec1_block_device:s0 | u:object_r:sec1_block_device:s0 |
| seccfg | u:object_r:block_device:s0 | u:object_r:seccfg_block_device:s0 | u:object_r:seccfg_block_device:s0 |
| spmfw | u:object_r:block_device:s0 | u:object_r:spmfw_block_device:s0 | u:object_r:spmfw_block_device:s0 |
| tee1 | u:object_r:block_device:s0 | u:object_r:tee_block_device:s0 | u:object_r:tee_block_device:s0 |
| tee2 | u:object_r:block_device:s0 | u:object_r:tee_block_device:s0 | u:object_r:tee_block_device:s0 |
| userdata | u:object_r:block_device:s0 | u:object_r:userdata_block_device:s0 | u:object_r:userdata_block_device:s0 |

Platform aliases with no proposed relabel:
Specialized live aliases        : boot, recovery, super, vbmeta, vbmeta_system,
                                 vbmeta_vendor
Generic live aliases            : flashinfo, gz1, gz2, lk2, md_udc, preloader_a,
                                 preloader_b, scp1, scp2, sspm_1, sspm_2
No-change count                 : 17
Classification                 : ACTIVE_DEFAULT_CONTEXT_RESULTS_FULLY_ENUMERATED

Underlying block-node label topology:
Specialized live nodes          : 30
Literal-path active default     : u:object_r:block_device:s0 for all 30
Dry-run restorecon result       : proposed specialized-to-generic relabel
No-change block objects         : 12
Specialized-to-generic names    : boot_para, recovery, para, expdb, vbmeta_system, vbmeta_vendor, frp, nvcfg, nvdata, md_udc, metadata, protect1, protect2, seccfg, sec1, proinfo, md1img, spmfw, nvram, lk, boot, logo, dtbo, tee1, tee2, super, vbmeta, cache, userdata, otp
No-change names                 : mmcblk0, mmcblk0boot0, mmcblk0boot1, mmcblk0rpmb, scp1, scp2, sspm_1, sspm_2, gz1, gz2, lk2, flashinfo
Classification                 : CREATION_LABEL_AND_LITERAL_PATH_DEFAULT_SPLIT_CONFIRMED

Vendor file-context realization:
Relevant policy file            : /vendor/etc/selinux/vendor_file_contexts
Root exact specialized rule     : /dev/block/by-name/md_udc
Root exact rule result          : metadata_block_device, realized live
Platform specialized rules realized live: boot, recovery, super, vbmeta,
                                 vbmeta_system, vbmeta_vendor
Platform specialized rules not realized live: boot_para, cache, dtbo, expdb,
                                 frp, lk, logo, md1img, metadata, nvcfg, nvdata,
                                 nvram, otp, para, proinfo, protect1, protect2,
                                 sec1, seccfg, spmfw, tee1, tee2, userdata
Naming-form divergence          : lk(_[ab])? does not match lk2
Naming-form divergence          : scp(_[ab])? does not match scp1 or scp2
Naming-form divergence          : sspm(_[ab])? does not match sspm_1 or sspm_2
No captured specialized platform rule: flashinfo, gz1, gz2, md_udc,
                                 preloader_a, preloader_b
Classification                 : STATIC_POLICY_AND_LIVE_REALIZATION_SPLIT_CONFIRMED

Vendor ueventd permission declarations:

| Object | Static declaration | Live target-node state | Relationship |
|---|---|---|---|
| mmcblk0 | 0660 root:system | 0660 root:system | MATCH |
| mmcblk0boot0 | 0660 root:system | 0660 root:system | MATCH |
| mmcblk0boot1 | 0660 root:system | 0660 root:system | MATCH |
| boot | 0640 root:system | 0600 root:root | DIVERGES |
| recovery | 0640 root:system | 0600 root:root | DIVERGES |
| seccfg | 0660 root:system | 0660 root:system | MATCH |
| proinfo | 0660 root:system | 0660 root:system | MATCH |
| nvram | 0660 root:system | 0660 root:system | MATCH |
| para | 0660 root:system | 0660 root:system | MATCH |
| logo | 0660 root:system | 0660 root:system | MATCH |
| frp | 0660 root:system | 0660 root:system | MATCH |
| md1img | 0640 root:system | 0640 root:system | MATCH |
| otp | 0660 root:system | 0660 root:system | MATCH |

Static names without live by-name objects:
Vendor ueventd names            : misc2, secro, md1img_a, md1img_b, md1dsp
Meta/factory source names       : secro, kb, dkb
Live GPT PARTNAME matches       : none for the listed absent names
Classification                 : STATIC_DECLARATIONS_NOT_POPULATED_IN_NORMAL_RUNTIME

Resolved layer divergences:
1. `/dev/block/by-name` and `/dev/block/platform/bootdevice/by-name` are
   independent directories with different populations; the alternate
   `/dev/block/bootdevice/by-name` path resolves to the platform directory.
2. The same 38 GPT aliases resolve to identical targets in both physical
   namespaces while seven alias names carry different symlink-object contexts.
3. `md_udc` is specialized in the root namespace and generic in the platform
   namespace; boot, recovery, super, vbmeta, vbmeta_system, and vbmeta_vendor
   show the opposite namespace pattern.
4. `preloader_a` and `preloader_b` are not GPT aliases; they are post-fs init
   aliases to the two eMMC boot hardware regions.
5. `ro.boot.boot_devices` exposes `bootdevice,11230000.mmc`, while only the
   `bootdevice` platform directory is materialized under `/dev/block/platform`.

Open boundaries carried out of this subsection:
1. Exact native init/ueventd function names, branch predicates, and path-
   construction sequence for the root, platform, and platform-direct aliases
2. Exact boot-stage mechanism producing the 23 platform alias live/default
   SELinux context divergences
3. Exact creation-time lookup path producing specialized live contexts on 30
   underlying block nodes whose literal-path default is generic block_device
4. Exact reason the boot and recovery ueventd permission declarations are not
   reflected in the captured target-node mode and ownership
5. Exact native handling of the `11230000.mmc` boot-device token without a
   matching `/dev/block/platform/11230000.mmc` directory
6. Exact init command result for the unrealized `/dev/block/sda` and
   `/dev/block/sdb` compatibility declarations

Subsection closure:
Physical by-name directories    : 2
Alternate platform path         : 1
Canonical root aliases          : 42
Canonical platform aliases      : 40
Canonical platform-direct aliases: 42
Common GPT aliases              : 38
Root-only eMMC aliases          : 4
Platform-only preloader aliases : 2
Broken canonical aliases        : 0
Non-block canonical targets     : 0
Target mismatches               : 0
Unresolved canonical records    : 0
Open boundary count             : 6
Canonical conclusion            : Two independent physical by-name directories expose
                                  the complete live storage alias topology. The root
                                  namespace contains 38 GPT PARTNAME aliases and four
                                  eMMC DEVNAME aliases. The platform namespace contains
                                  the same 38 GPT aliases plus two init-created preloader
                                  aliases. The platform parent additionally contains 42
                                  direct mmcblk0* aliases. Every canonical alias resolves
                                  to one populated block target with no integrity errors.
Subsection 2.4 status            : CLOSED_WITH_BOUNDED_OPEN_ITEMS

################################################################################
################################################################################
# 2.5 — COMPLETE PHYSICAL PARTITION MAP
################################################################################
################################################################################

Subsection control:
Title                                      : Complete Physical Partition Map
Scope                                      : mmcblk0 GPT physical-partition geometry, raw GPT entry identity,
                                             live kernel partition realization, exact byte extents, block-object
                                             attributes, holder topology, continuity, and closure
Status                                     : CLOSED

Unit and arithmetic rules:
Logical sector unit                        : 512 bytes
Inclusive end-LBA rule                     : start LBA + sector count - 1
Start-byte-offset rule                     : start LBA × 512
Exact-byte-size rule                       : sector count × 512
Inclusive end-byte rule                    : (end LBA × 512) + 511
Exclusive end-byte rule                    : (end LBA + 1) × 512
Hexadecimal representation                 : exact base-16 representation of verified integers

Evidence-class declaration:
Raw GPT entry fields                       : RAW_DEVICE_CAPTURE
GPT type GUIDs                             : RAW_DEVICE_CAPTURE
GPT unique partition GUIDs                 : RAW_DEVICE_CAPTURE
GPT attribute bitfields                    : RAW_DEVICE_CAPTURE
GPT first and last LBAs                    : RAW_DEVICE_CAPTURE
Kernel partition numbers                   : LIVE_SYSFS_CAPTURE
Kernel partition names                     : LIVE_SYSFS_CAPTURE
Kernel start sectors                       : LIVE_SYSFS_CAPTURE
Kernel sector counts                       : LIVE_SYSFS_CAPTURE
Major:minor values                         : LIVE_SYSFS_CAPTURE
Alignment offsets                          : LIVE_SYSFS_CAPTURE
Discard alignments                         : LIVE_SYSFS_CAPTURE
Kernel read-only states                    : LIVE_SYSFS_CAPTURE
Holder counts and names                    : LIVE_SYSFS_CAPTURE
Holder major:minor values                  : LIVE_SYSFS_CAPTURE
Holder sector counts                       : LIVE_SYSFS_CAPTURE
Holder slave relationships                 : LIVE_SYSFS_CAPTURE
Partition byte geometry                    : EXACT_INTEGER_DERIVATION_FROM_VERIFIED_512_BYTE_SECTOR_GEOMETRY
Holder exact byte sizes                    : EXACT_INTEGER_DERIVATION_FROM_LIVE_SECTOR_COUNTS

Live kernel acquisition surfaces:
Raw GPT source object                      : /dev/block/mmcblk0
Raw GPT entry-array LBA range              : 2–33
Raw GPT entry-array byte length            : 16,384
Raw GPT entry-array capture status         : 0
Partition object range                     : /sys/block/mmcblk0/mmcblk0p1 through
                                             /sys/block/mmcblk0/mmcblk0p38
Major:minor attribute                      : dev
Partition-number attribute                 : partition
Partition-name attribute                   : uevent PARTNAME
Start-sector attribute                     : start
Sector-count attribute                     : size
Alignment-offset attribute                 : alignment_offset
Discard-alignment attribute                : discard_alignment
Read-only-state attribute                  : ro
Holder-directory attribute                 : holders
Holder block objects                       : /sys/block/dm-0, /sys/block/dm-1, /sys/block/dm-2
Holder slave directories                   : /sys/block/dm-0/slaves, /sys/block/dm-1/slaves,
                                             /sys/block/dm-2/slaves
Partition objects captured                 : 38 of 38
Partition scalar reads                     : 266 of 266 successful
Partition-name capture coverage            : 38 of 38
Missing partition objects                  : 0
Duplicate partition objects                : 0

Canonical map geometry:
Parent block device                        : /dev/block/mmcblk0
Parent device name                         : mmcblk0
Parent device type                         : disk
Parent major:minor                         : 179:0
Parent sector count                        : 244,277,248
Logical sector size                        : 512 bytes
Parent exact byte size                     : 125,069,950,976
Parent first byte offset                   : 0
Parent last byte offset                    : 125,069,950,975
Parent first byte offset hex               : 0x0000000000000000
Parent last byte offset hex                : 0x0000001D1EBFFFFF
Physical partition count                   : 38
Physical partition number range            : 1–38
Physical partition object range            : mmcblk0p1–mmcblk0p38
Partition number sequence                  : contiguous
Partition name count                       : 38
Duplicate partition name count             : 0
Parent holder count                        : 0
Parent slave count                         : 0

GPT partition-entry structure:
Partition-entry capacity                   : 128
Partition-entry size                       : 128 bytes
Partition-entry array size                 : 16,384 bytes
Populated partition-entry count            : 38
First populated partition entry            : 1
Last populated partition entry             : 38
Zero-filled partition-entry count          : 90
First zero-filled partition entry          : 39
Last zero-filled partition entry           : 128
Partially populated unused-entry count     : 0
Partition-entry type-GUID count            : 1
Partition-entry type GUID                  : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
Unique partition-GUID count                : 38
Duplicate unique partition-GUID count      : 0
Zero unique partition-GUID count           : 0
Populated-entry attribute bitfield         : 0x0000000000000000
Entries with nonzero attribute bitfields   : 0
Decoded UTF-16LE partition-name count      : 38
Empty populated partition-name count       : 0
Malformed UTF-16LE partition-name count    : 0
Nonzero name-field data after terminator   : 0
Stored partition-entry-array CRC32         : 0x3F3C3F6A
Calculated partition-entry-array CRC32     : 0x3F3C3F6A
Partition-entry-array CRC32 state          : MATCH

Physical extent geometry:
Leading non-partition structural LBA range : 0–33
Leading non-partition structural sector count: 34
Leading non-partition structural byte range: 0–17,407
Leading non-partition structural byte count: 17,408
First usable LBA                           : 34
First physical partition LBA               : 64
Leading unallocated usable LBA range       : 34–63
Leading unallocated usable sector count    : 30
Leading unallocated usable byte range      : 17,408–32,767
Leading unallocated usable byte count      : 15,360
Physical partition-span LBA range          : 64–244,277,214
Physical partition-span sector count       : 244,277,151
Physical partition-span byte range         : 32,768–125,069,934,079
Physical partition-span exact byte size    : 125,069,901,312
Physical partition-span first-byte offset hex: 0x0000000000008000
Physical partition-span last-byte offset hex: 0x0000001D1EBFBDFF
Sum of partition sector counts             : 244,277,151
Internal gap count                         : 0
Internal gap sector count                  : 0
Overlap count                              : 0
Last physical partition LBA                : 244,277,214
Last usable LBA                            : 244,277,214
Trailing unallocated usable sector count   : 0
Trailing non-partition LBA range           : 244,277,215–244,277,247
Trailing non-partition sector count        : 33
Trailing non-partition byte range          : 125,069,934,080–125,069,950,975
Trailing non-partition byte count          : 16,896
Total non-partition sector count           : 97
Total non-partition byte count             : 49,664

Kernel block-object geometry:
Kernel partition-object count              : 38
Kernel device type                         : partition
Major 179 partition range                  : mmcblk0p1–mmcblk0p31
Major 179 minor range                      : 1–31
Major 259 partition range                  : mmcblk0p32–mmcblk0p38
Major 259 minor range                      : 0–6
Partition alignment-offset value           : 0 bytes on all 38 partitions
Partitions with nonzero alignment offset   : 0
Kernel read-only-state 0 range             : mmcblk0p1–mmcblk0p13, mmcblk0p34–mmcblk0p38
Kernel read-only-state 1 range             : mmcblk0p14–mmcblk0p33
Partitions with holders                    : 1
Partition with holders                     : mmcblk0p33
Total holder count                         : 3

Exact size representation:
Exact geometry unit                        : 512-byte sectors
/proc/partitions reporting unit            : 1,024-byte blocks
Partitions with odd 512-byte sector count  : 1
Odd-sector partition                       : userdata
userdata exact sector count                : 233,801,695
userdata exact byte size                   : 119,706,467,840
userdata /proc/partitions block count      : 116,900,847
userdata /proc/partitions represented byte count: 119,706,467,328
userdata representation remainder          : 512 bytes

Kernel-field evidentiary closure:
Major:minor field class                    : LIVE_SYSFS_CAPTURE
Major:minor field coverage                 : 38 of 38 partitions
Alignment-offset field class               : LIVE_SYSFS_CAPTURE
Alignment-offset field coverage            : 38 of 38 partitions
Discard-alignment field class              : LIVE_SYSFS_CAPTURE
Discard-alignment field coverage           : 38 of 38 partitions
Discard-alignment acquisition derivation   : NO
Discard-alignment formula-match coverage   : 38 of 38 partitions
Discard granularity used for formula check : 4,194,304 bytes
Holder field class                         : LIVE_SYSFS_CAPTURE
Holder field coverage                      : 38 of 38 partitions
Partitions with holders                    : 1
Partition with holders                     : mmcblk0p33
Total holders                              : 3
Unresolved evidentiary-class fields        : 0

Canonical physical partition map:

| PARTN | PARTNAME | Block object | Major:minor | Start LBA | End LBA inclusive | Sectors | Exact bytes | GPT unique GUID |
|---:|---|---|---:|---:|---:|---:|---:|---|
| 1 | boot_para | mmcblk0p1 | 179:1 | 64 | 2,111 | 2,048 | 1,048,576 | f57ad330-39c2-4488-9bb0-00cb43c9ccd4 |
| 2 | recovery | mmcblk0p2 | 179:2 | 2,112 | 67,647 | 65,536 | 33,554,432 | fe686d97-3544-4a41-be21-167e25b61b6f |
| 3 | para | mmcblk0p3 | 179:3 | 67,648 | 68,671 | 1,024 | 524,288 | 1cb143a8-b1a8-4b57-b251-945c5119e8fe |
| 4 | expdb | mmcblk0p4 | 179:4 | 68,672 | 109,631 | 40,960 | 20,971,520 | 3b9e343b-cdc8-4d7f-9fa6-b6812e50ab62 |
| 5 | vbmeta_system | mmcblk0p5 | 179:5 | 109,632 | 126,015 | 16,384 | 8,388,608 | 5f6a2c79-6617-4b85-ac02-c2975a14d2d7 |
| 6 | vbmeta_vendor | mmcblk0p6 | 179:6 | 126,016 | 142,399 | 16,384 | 8,388,608 | 4ae2050b-5db5-4ff7-aad3-5730534be63d |
| 7 | frp | mmcblk0p7 | 179:7 | 142,400 | 144,447 | 2,048 | 1,048,576 | 1f9b0939-e16b-4bc9-a5bc-dc2ee969d801 |
| 8 | nvcfg | mmcblk0p8 | 179:8 | 144,448 | 209,983 | 65,536 | 33,554,432 | d722c721-0dee-4cb8-8a83-2c63cd1393c7 |
| 9 | nvdata | mmcblk0p9 | 179:9 | 209,984 | 341,055 | 131,072 | 67,108,864 | e02179a8-ceb5-48a9-8831-4f1c9c5a8695 |
| 10 | md_udc | mmcblk0p10 | 179:10 | 341,056 | 387,343 | 46,288 | 23,699,456 | 84b09a81-fad2-41ac-890e-407c24975e74 |
| 11 | metadata | mmcblk0p11 | 179:11 | 387,344 | 452,879 | 65,536 | 33,554,432 | e8f0a5ef-8d1b-42ea-9c2a-835cd77de363 |
| 12 | protect1 | mmcblk0p12 | 179:12 | 452,880 | 469,263 | 16,384 | 8,388,608 | d5f0e175-a6e1-4db7-94c0-f82ad032950b |
| 13 | protect2 | mmcblk0p13 | 179:13 | 469,264 | 491,519 | 22,256 | 11,395,072 | 1d9056e1-e139-4fca-8c0b-b75fd74d81c6 |
| 14 | seccfg | mmcblk0p14 | 179:14 | 491,520 | 507,903 | 16,384 | 8,388,608 | 7792210b-b6a8-45d5-ad91-3361ed14c608 |
| 15 | sec1 | mmcblk0p15 | 179:15 | 507,904 | 511,999 | 4,096 | 2,097,152 | 138a6db9-1032-451d-91e9-0fa38ff94fbb |
| 16 | proinfo | mmcblk0p16 | 179:16 | 512,000 | 518,143 | 6,144 | 3,145,728 | 756d934c-50e3-4c91-af46-02d824169ca7 |
| 17 | md1img | mmcblk0p17 | 179:17 | 518,144 | 722,943 | 204,800 | 104,857,600 | a3f3c267-5521-42dd-a724-3bdec20c7c6f |
| 18 | spmfw | mmcblk0p18 | 179:18 | 722,944 | 724,991 | 2,048 | 1,048,576 | 8c68cd2a-ccc9-4c5d-8b57-34ae9b2dd481 |
| 19 | scp1 | mmcblk0p19 | 179:19 | 724,992 | 727,039 | 2,048 | 1,048,576 | 6a5cebf8-54a7-4b89-8d1d-c5eb140b095b |
| 20 | scp2 | mmcblk0p20 | 179:20 | 727,040 | 729,087 | 2,048 | 1,048,576 | a0d65bf8-e8de-4107-9434-1d318c843d37 |
| 21 | sspm_1 | mmcblk0p21 | 179:21 | 729,088 | 731,135 | 2,048 | 1,048,576 | 46f0c0bb-f227-4eb6-b82f-66408e13e36d |
| 22 | sspm_2 | mmcblk0p22 | 179:22 | 731,136 | 733,183 | 2,048 | 1,048,576 | fbc2c131-6392-4217-b51e-548a6edb03d0 |
| 23 | gz1 | mmcblk0p23 | 179:23 | 733,184 | 765,951 | 32,768 | 16,777,216 | e195a981-e285-4734-8025-ec323e9589d9 |
| 24 | gz2 | mmcblk0p24 | 179:24 | 765,952 | 798,719 | 32,768 | 16,777,216 | e29052f8-5d3a-4e97-adb5-5f312ce6610a |
| 25 | nvram | mmcblk0p25 | 179:25 | 798,720 | 929,791 | 131,072 | 67,108,864 | 9c3cabd7-a35d-4b45-8c57-b80775426b35 |
| 26 | lk | mmcblk0p26 | 179:26 | 929,792 | 931,839 | 2,048 | 1,048,576 | e7099731-95a6-45a6-a1e5-1b6aba032cf1 |
| 27 | lk2 | mmcblk0p27 | 179:27 | 931,840 | 933,887 | 2,048 | 1,048,576 | 8273e1ab-846f-4468-b999-ee2ea8e50a16 |
| 28 | boot | mmcblk0p28 | 179:28 | 933,888 | 999,423 | 65,536 | 33,554,432 | d26472f1-9ebc-421d-ba14-311296457c90 |
| 29 | logo | mmcblk0p29 | 179:29 | 999,424 | 1,015,807 | 16,384 | 8,388,608 | b72ccbe9-2055-46f4-a167-4a069c201738 |
| 30 | dtbo | mmcblk0p30 | 179:30 | 1,015,808 | 1,032,191 | 16,384 | 8,388,608 | 9c1520f3-c2c5-4b89-8242-fe4c61208a9e |
| 31 | tee1 | mmcblk0p31 | 179:31 | 1,032,192 | 1,042,431 | 10,240 | 5,242,880 | 902d5f3f-434a-4de7-8988-321e88c9b8aa |
| 32 | tee2 | mmcblk0p32 | 259:0 | 1,042,432 | 1,064,959 | 22,528 | 11,534,336 | bece74c8-d8e2-4863-9bfe-5b0b66bb920f |
| 33 | super | mmcblk0p33 | 259:1 | 1,064,960 | 9,453,567 | 8,388,608 | 4,294,967,296 | ff1342cf-b7be-44d5-a25e-a435addd2702 |
| 34 | vbmeta | mmcblk0p34 | 259:2 | 9,453,568 | 9,469,951 | 16,384 | 8,388,608 | a4da8f1b-fe07-433b-95cb-84a5f23e477b |
| 35 | cache | mmcblk0p35 | 259:3 | 9,469,952 | 10,354,687 | 884,736 | 452,984,832 | c2635e15-61aa-454e-9c40-ebe1bdf19b9b |
| 36 | userdata | mmcblk0p36 | 259:4 | 10,354,688 | 244,156,382 | 233,801,695 | 119,706,467,840 | 4d2d1290-36a3-4f5d-afb4-319f8ab6dcd8 |
| 37 | otp | mmcblk0p37 | 259:5 | 244,156,383 | 244,244,446 | 88,064 | 45,088,768 | fdce12f0-a7eb-40f7-8350-960972e6cb57 |
| 38 | flashinfo | mmcblk0p38 | 259:6 | 244,244,447 | 244,277,214 | 32,768 | 16,777,216 | 0fbbafa2-4aa9-4490-8983-5329328505fd |

boot_para physical partition:
Partition number                           : 1
GPT entry number                           : 1
Partition name                             : boot_para
Block device                               : /dev/block/mmcblk0p1
Device name                                : mmcblk0p1
Device type                                : partition
Major:minor                                : 179:1
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 1
Kernel partition name                      : boot_para
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : f57ad330-39c2-4488-9bb0-00cb43c9ccd4
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 64
Start LBA hex                              : 0x40
End LBA inclusive                          : 2,111
End LBA inclusive hex                      : 0x83F
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 32,768
Start byte offset hex                      : 0x0000000000008000
End byte offset inclusive                  : 1,081,343
End byte offset inclusive hex              : 0x0000000000107FFF
End byte offset exclusive                  : 1,081,344
End byte offset exclusive hex              : 0x0000000000108000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 4,161,536 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

recovery physical partition:
Partition number                           : 2
GPT entry number                           : 2
Partition name                             : recovery
Block device                               : /dev/block/mmcblk0p2
Device name                                : mmcblk0p2
Device type                                : partition
Major:minor                                : 179:2
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 2
Kernel partition name                      : recovery
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : fe686d97-3544-4a41-be21-167e25b61b6f
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 2,112
Start LBA hex                              : 0x840
End LBA inclusive                          : 67,647
End LBA inclusive hex                      : 0x1083F
Sector count                               : 65,536
Sector count hex                           : 0x10000
Start byte offset                          : 1,081,344
Start byte offset hex                      : 0x0000000000108000
End byte offset inclusive                  : 34,635,775
End byte offset inclusive hex              : 0x0000000002107FFF
End byte offset exclusive                  : 34,635,776
End byte offset exclusive hex              : 0x0000000002108000
Exact byte size                            : 33,554,432
Exact byte size hex                        : 0x0000000002000000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,112,960 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

para physical partition:
Partition number                           : 3
GPT entry number                           : 3
Partition name                             : para
Block device                               : /dev/block/mmcblk0p3
Device name                                : mmcblk0p3
Device type                                : partition
Major:minor                                : 179:3
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 3
Kernel partition name                      : para
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 1cb143a8-b1a8-4b57-b251-945c5119e8fe
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 67,648
Start LBA hex                              : 0x10840
End LBA inclusive                          : 68,671
End LBA inclusive hex                      : 0x10C3F
Sector count                               : 1,024
Sector count hex                           : 0x400
Start byte offset                          : 34,635,776
Start byte offset hex                      : 0x0000000002108000
End byte offset inclusive                  : 35,160,063
End byte offset inclusive hex              : 0x0000000002187FFF
End byte offset exclusive                  : 35,160,064
End byte offset exclusive hex              : 0x0000000002188000
Exact byte size                            : 524,288
Exact byte size hex                        : 0x0000000000080000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,112,960 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

expdb physical partition:
Partition number                           : 4
GPT entry number                           : 4
Partition name                             : expdb
Block device                               : /dev/block/mmcblk0p4
Device name                                : mmcblk0p4
Device type                                : partition
Major:minor                                : 179:4
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 4
Kernel partition name                      : expdb
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 3b9e343b-cdc8-4d7f-9fa6-b6812e50ab62
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 68,672
Start LBA hex                              : 0x10C40
End LBA inclusive                          : 109,631
End LBA inclusive hex                      : 0x1AC3F
Sector count                               : 40,960
Sector count hex                           : 0xA000
Start byte offset                          : 35,160,064
Start byte offset hex                      : 0x0000000002188000
End byte offset inclusive                  : 56,131,583
End byte offset inclusive hex              : 0x0000000003587FFF
End byte offset exclusive                  : 56,131,584
End byte offset exclusive hex              : 0x0000000003588000
Exact byte size                            : 20,971,520
Exact byte size hex                        : 0x0000000001400000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,588,672 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

vbmeta_system physical partition:
Partition number                           : 5
GPT entry number                           : 5
Partition name                             : vbmeta_system
Block device                               : /dev/block/mmcblk0p5
Device name                                : mmcblk0p5
Device type                                : partition
Major:minor                                : 179:5
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 5
Kernel partition name                      : vbmeta_system
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 5f6a2c79-6617-4b85-ac02-c2975a14d2d7
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 109,632
Start LBA hex                              : 0x1AC40
End LBA inclusive                          : 126,015
End LBA inclusive hex                      : 0x1EC3F
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 56,131,584
Start byte offset hex                      : 0x0000000003588000
End byte offset inclusive                  : 64,520,191
End byte offset inclusive hex              : 0x0000000003D87FFF
End byte offset exclusive                  : 64,520,192
End byte offset exclusive hex              : 0x0000000003D88000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,588,672 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

vbmeta_vendor physical partition:
Partition number                           : 6
GPT entry number                           : 6
Partition name                             : vbmeta_vendor
Block device                               : /dev/block/mmcblk0p6
Device name                                : mmcblk0p6
Device type                                : partition
Major:minor                                : 179:6
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 6
Kernel partition name                      : vbmeta_vendor
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 4ae2050b-5db5-4ff7-aad3-5730534be63d
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 126,016
Start LBA hex                              : 0x1EC40
End LBA inclusive                          : 142,399
End LBA inclusive hex                      : 0x22C3F
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 64,520,192
Start byte offset hex                      : 0x0000000003D88000
End byte offset inclusive                  : 72,908,799
End byte offset inclusive hex              : 0x0000000004587FFF
End byte offset exclusive                  : 72,908,800
End byte offset exclusive hex              : 0x0000000004588000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,588,672 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

frp physical partition:
Partition number                           : 7
GPT entry number                           : 7
Partition name                             : frp
Block device                               : /dev/block/mmcblk0p7
Device name                                : mmcblk0p7
Device type                                : partition
Major:minor                                : 179:7
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 7
Kernel partition name                      : frp
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 1f9b0939-e16b-4bc9-a5bc-dc2ee969d801
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 142,400
Start LBA hex                              : 0x22C40
End LBA inclusive                          : 144,447
End LBA inclusive hex                      : 0x2343F
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 72,908,800
Start byte offset hex                      : 0x0000000004588000
End byte offset inclusive                  : 73,957,375
End byte offset inclusive hex              : 0x0000000004687FFF
End byte offset exclusive                  : 73,957,376
End byte offset exclusive hex              : 0x0000000004688000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,588,672 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

nvcfg physical partition:
Partition number                           : 8
GPT entry number                           : 8
Partition name                             : nvcfg
Block device                               : /dev/block/mmcblk0p8
Device name                                : mmcblk0p8
Device type                                : partition
Major:minor                                : 179:8
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 8
Kernel partition name                      : nvcfg
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : d722c721-0dee-4cb8-8a83-2c63cd1393c7
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 144,448
Start LBA hex                              : 0x23440
End LBA inclusive                          : 209,983
End LBA inclusive hex                      : 0x3343F
Sector count                               : 65,536
Sector count hex                           : 0x10000
Start byte offset                          : 73,957,376
Start byte offset hex                      : 0x0000000004688000
End byte offset inclusive                  : 107,511,807
End byte offset inclusive hex              : 0x0000000006687FFF
End byte offset exclusive                  : 107,511,808
End byte offset exclusive hex              : 0x0000000006688000
Exact byte size                            : 33,554,432
Exact byte size hex                        : 0x0000000002000000
Alignment offset                           : 0 bytes
Discard alignment                          : 1,540,096 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

nvdata physical partition:
Partition number                           : 9
GPT entry number                           : 9
Partition name                             : nvdata
Block device                               : /dev/block/mmcblk0p9
Device name                                : mmcblk0p9
Device type                                : partition
Major:minor                                : 179:9
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 9
Kernel partition name                      : nvdata
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : e02179a8-ceb5-48a9-8831-4f1c9c5a8695
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 209,984
Start LBA hex                              : 0x33440
End LBA inclusive                          : 341,055
End LBA inclusive hex                      : 0x5343F
Sector count                               : 131,072
Sector count hex                           : 0x20000
Start byte offset                          : 107,511,808
Start byte offset hex                      : 0x0000000006688000
End byte offset inclusive                  : 174,620,671
End byte offset inclusive hex              : 0x000000000A687FFF
End byte offset exclusive                  : 174,620,672
End byte offset exclusive hex              : 0x000000000A688000
Exact byte size                            : 67,108,864
Exact byte size hex                        : 0x0000000004000000
Alignment offset                           : 0 bytes
Discard alignment                          : 1,540,096 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

md_udc physical partition:
Partition number                           : 10
GPT entry number                           : 10
Partition name                             : md_udc
Block device                               : /dev/block/mmcblk0p10
Device name                                : mmcblk0p10
Device type                                : partition
Major:minor                                : 179:10
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 10
Kernel partition name                      : md_udc
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 84b09a81-fad2-41ac-890e-407c24975e74
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 341,056
Start LBA hex                              : 0x53440
End LBA inclusive                          : 387,343
End LBA inclusive hex                      : 0x5E90F
Sector count                               : 46,288
Sector count hex                           : 0xB4D0
Start byte offset                          : 174,620,672
Start byte offset hex                      : 0x000000000A688000
End byte offset inclusive                  : 198,320,127
End byte offset inclusive hex              : 0x000000000BD21FFF
End byte offset exclusive                  : 198,320,128
End byte offset exclusive hex              : 0x000000000BD22000
Exact byte size                            : 23,699,456
Exact byte size hex                        : 0x000000000169A000
Alignment offset                           : 0 bytes
Discard alignment                          : 1,540,096 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

metadata physical partition:
Partition number                           : 11
GPT entry number                           : 11
Partition name                             : metadata
Block device                               : /dev/block/mmcblk0p11
Device name                                : mmcblk0p11
Device type                                : partition
Major:minor                                : 179:11
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 11
Kernel partition name                      : metadata
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : e8f0a5ef-8d1b-42ea-9c2a-835cd77de363
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 387,344
Start LBA hex                              : 0x5E910
End LBA inclusive                          : 452,879
End LBA inclusive hex                      : 0x6E90F
Sector count                               : 65,536
Sector count hex                           : 0x10000
Start byte offset                          : 198,320,128
Start byte offset hex                      : 0x000000000BD22000
End byte offset inclusive                  : 231,874,559
End byte offset inclusive hex              : 0x000000000DD21FFF
End byte offset exclusive                  : 231,874,560
End byte offset exclusive hex              : 0x000000000DD22000
Exact byte size                            : 33,554,432
Exact byte size hex                        : 0x0000000002000000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,006,464 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

protect1 physical partition:
Partition number                           : 12
GPT entry number                           : 12
Partition name                             : protect1
Block device                               : /dev/block/mmcblk0p12
Device name                                : mmcblk0p12
Device type                                : partition
Major:minor                                : 179:12
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 12
Kernel partition name                      : protect1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : d5f0e175-a6e1-4db7-94c0-f82ad032950b
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 452,880
Start LBA hex                              : 0x6E910
End LBA inclusive                          : 469,263
End LBA inclusive hex                      : 0x7290F
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 231,874,560
Start byte offset hex                      : 0x000000000DD22000
End byte offset inclusive                  : 240,263,167
End byte offset inclusive hex              : 0x000000000E521FFF
End byte offset exclusive                  : 240,263,168
End byte offset exclusive hex              : 0x000000000E522000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,006,464 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

protect2 physical partition:
Partition number                           : 13
GPT entry number                           : 13
Partition name                             : protect2
Block device                               : /dev/block/mmcblk0p13
Device name                                : mmcblk0p13
Device type                                : partition
Major:minor                                : 179:13
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 13
Kernel partition name                      : protect2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 1d9056e1-e139-4fca-8c0b-b75fd74d81c6
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 469,264
Start LBA hex                              : 0x72910
End LBA inclusive                          : 491,519
End LBA inclusive hex                      : 0x77FFF
Sector count                               : 22,256
Sector count hex                           : 0x56F0
Start byte offset                          : 240,263,168
Start byte offset hex                      : 0x000000000E522000
End byte offset inclusive                  : 251,658,239
End byte offset inclusive hex              : 0x000000000EFFFFFF
End byte offset exclusive                  : 251,658,240
End byte offset exclusive hex              : 0x000000000F000000
Exact byte size                            : 11,395,072
Exact byte size hex                        : 0x0000000000ADE000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,006,464 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

seccfg physical partition:
Partition number                           : 14
GPT entry number                           : 14
Partition name                             : seccfg
Block device                               : /dev/block/mmcblk0p14
Device name                                : mmcblk0p14
Device type                                : partition
Major:minor                                : 179:14
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 14
Kernel partition name                      : seccfg
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 7792210b-b6a8-45d5-ad91-3361ed14c608
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 491,520
Start LBA hex                              : 0x78000
End LBA inclusive                          : 507,903
End LBA inclusive hex                      : 0x7BFFF
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 251,658,240
Start byte offset hex                      : 0x000000000F000000
End byte offset inclusive                  : 260,046,847
End byte offset inclusive hex              : 0x000000000F7FFFFF
End byte offset exclusive                  : 260,046,848
End byte offset exclusive hex              : 0x000000000F800000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

sec1 physical partition:
Partition number                           : 15
GPT entry number                           : 15
Partition name                             : sec1
Block device                               : /dev/block/mmcblk0p15
Device name                                : mmcblk0p15
Device type                                : partition
Major:minor                                : 179:15
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 15
Kernel partition name                      : sec1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 138a6db9-1032-451d-91e9-0fa38ff94fbb
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 507,904
Start LBA hex                              : 0x7C000
End LBA inclusive                          : 511,999
End LBA inclusive hex                      : 0x7CFFF
Sector count                               : 4,096
Sector count hex                           : 0x1000
Start byte offset                          : 260,046,848
Start byte offset hex                      : 0x000000000F800000
End byte offset inclusive                  : 262,143,999
End byte offset inclusive hex              : 0x000000000F9FFFFF
End byte offset exclusive                  : 262,144,000
End byte offset exclusive hex              : 0x000000000FA00000
Exact byte size                            : 2,097,152
Exact byte size hex                        : 0x0000000000200000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

proinfo physical partition:
Partition number                           : 16
GPT entry number                           : 16
Partition name                             : proinfo
Block device                               : /dev/block/mmcblk0p16
Device name                                : mmcblk0p16
Device type                                : partition
Major:minor                                : 179:16
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 16
Kernel partition name                      : proinfo
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 756d934c-50e3-4c91-af46-02d824169ca7
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 512,000
Start LBA hex                              : 0x7D000
End LBA inclusive                          : 518,143
End LBA inclusive hex                      : 0x7E7FF
Sector count                               : 6,144
Sector count hex                           : 0x1800
Start byte offset                          : 262,144,000
Start byte offset hex                      : 0x000000000FA00000
End byte offset inclusive                  : 265,289,727
End byte offset inclusive hex              : 0x000000000FCFFFFF
End byte offset exclusive                  : 265,289,728
End byte offset exclusive hex              : 0x000000000FD00000
Exact byte size                            : 3,145,728
Exact byte size hex                        : 0x0000000000300000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

md1img physical partition:
Partition number                           : 17
GPT entry number                           : 17
Partition name                             : md1img
Block device                               : /dev/block/mmcblk0p17
Device name                                : mmcblk0p17
Device type                                : partition
Major:minor                                : 179:17
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 17
Kernel partition name                      : md1img
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : a3f3c267-5521-42dd-a724-3bdec20c7c6f
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 518,144
Start LBA hex                              : 0x7E800
End LBA inclusive                          : 722,943
End LBA inclusive hex                      : 0xB07FF
Sector count                               : 204,800
Sector count hex                           : 0x32000
Start byte offset                          : 265,289,728
Start byte offset hex                      : 0x000000000FD00000
End byte offset inclusive                  : 370,147,327
End byte offset inclusive hex              : 0x00000000160FFFFF
End byte offset exclusive                  : 370,147,328
End byte offset exclusive hex              : 0x0000000016100000
Exact byte size                            : 104,857,600
Exact byte size hex                        : 0x0000000006400000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,145,728 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

spmfw physical partition:
Partition number                           : 18
GPT entry number                           : 18
Partition name                             : spmfw
Block device                               : /dev/block/mmcblk0p18
Device name                                : mmcblk0p18
Device type                                : partition
Major:minor                                : 179:18
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 18
Kernel partition name                      : spmfw
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 8c68cd2a-ccc9-4c5d-8b57-34ae9b2dd481
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 722,944
Start LBA hex                              : 0xB0800
End LBA inclusive                          : 724,991
End LBA inclusive hex                      : 0xB0FFF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 370,147,328
Start byte offset hex                      : 0x0000000016100000
End byte offset inclusive                  : 371,195,903
End byte offset inclusive hex              : 0x00000000161FFFFF
End byte offset exclusive                  : 371,195,904
End byte offset exclusive hex              : 0x0000000016200000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,145,728 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

scp1 physical partition:
Partition number                           : 19
GPT entry number                           : 19
Partition name                             : scp1
Block device                               : /dev/block/mmcblk0p19
Device name                                : mmcblk0p19
Device type                                : partition
Major:minor                                : 179:19
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 19
Kernel partition name                      : scp1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 6a5cebf8-54a7-4b89-8d1d-c5eb140b095b
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 724,992
Start LBA hex                              : 0xB1000
End LBA inclusive                          : 727,039
End LBA inclusive hex                      : 0xB17FF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 371,195,904
Start byte offset hex                      : 0x0000000016200000
End byte offset inclusive                  : 372,244,479
End byte offset inclusive hex              : 0x00000000162FFFFF
End byte offset exclusive                  : 372,244,480
End byte offset exclusive hex              : 0x0000000016300000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

scp2 physical partition:
Partition number                           : 20
GPT entry number                           : 20
Partition name                             : scp2
Block device                               : /dev/block/mmcblk0p20
Device name                                : mmcblk0p20
Device type                                : partition
Major:minor                                : 179:20
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 20
Kernel partition name                      : scp2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : a0d65bf8-e8de-4107-9434-1d318c843d37
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 727,040
Start LBA hex                              : 0xB1800
End LBA inclusive                          : 729,087
End LBA inclusive hex                      : 0xB1FFF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 372,244,480
Start byte offset hex                      : 0x0000000016300000
End byte offset inclusive                  : 373,293,055
End byte offset inclusive hex              : 0x00000000163FFFFF
End byte offset exclusive                  : 373,293,056
End byte offset exclusive hex              : 0x0000000016400000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 1,048,576 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

sspm_1 physical partition:
Partition number                           : 21
GPT entry number                           : 21
Partition name                             : sspm_1
Block device                               : /dev/block/mmcblk0p21
Device name                                : mmcblk0p21
Device type                                : partition
Major:minor                                : 179:21
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 21
Kernel partition name                      : sspm_1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 46f0c0bb-f227-4eb6-b82f-66408e13e36d
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 729,088
Start LBA hex                              : 0xB2000
End LBA inclusive                          : 731,135
End LBA inclusive hex                      : 0xB27FF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 373,293,056
Start byte offset hex                      : 0x0000000016400000
End byte offset inclusive                  : 374,341,631
End byte offset inclusive hex              : 0x00000000164FFFFF
End byte offset exclusive                  : 374,341,632
End byte offset exclusive hex              : 0x0000000016500000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

sspm_2 physical partition:
Partition number                           : 22
GPT entry number                           : 22
Partition name                             : sspm_2
Block device                               : /dev/block/mmcblk0p22
Device name                                : mmcblk0p22
Device type                                : partition
Major:minor                                : 179:22
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 22
Kernel partition name                      : sspm_2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : fbc2c131-6392-4217-b51e-548a6edb03d0
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 731,136
Start LBA hex                              : 0xB2800
End LBA inclusive                          : 733,183
End LBA inclusive hex                      : 0xB2FFF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 374,341,632
Start byte offset hex                      : 0x0000000016500000
End byte offset inclusive                  : 375,390,207
End byte offset inclusive hex              : 0x00000000165FFFFF
End byte offset exclusive                  : 375,390,208
End byte offset exclusive hex              : 0x0000000016600000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,145,728 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

gz1 physical partition:
Partition number                           : 23
GPT entry number                           : 23
Partition name                             : gz1
Block device                               : /dev/block/mmcblk0p23
Device name                                : mmcblk0p23
Device type                                : partition
Major:minor                                : 179:23
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 23
Kernel partition name                      : gz1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : e195a981-e285-4734-8025-ec323e9589d9
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 733,184
Start LBA hex                              : 0xB3000
End LBA inclusive                          : 765,951
End LBA inclusive hex                      : 0xBAFFF
Sector count                               : 32,768
Sector count hex                           : 0x8000
Start byte offset                          : 375,390,208
Start byte offset hex                      : 0x0000000016600000
End byte offset inclusive                  : 392,167,423
End byte offset inclusive hex              : 0x00000000175FFFFF
End byte offset exclusive                  : 392,167,424
End byte offset exclusive hex              : 0x0000000017600000
Exact byte size                            : 16,777,216
Exact byte size hex                        : 0x0000000001000000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

gz2 physical partition:
Partition number                           : 24
GPT entry number                           : 24
Partition name                             : gz2
Block device                               : /dev/block/mmcblk0p24
Device name                                : mmcblk0p24
Device type                                : partition
Major:minor                                : 179:24
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 24
Kernel partition name                      : gz2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : e29052f8-5d3a-4e97-adb5-5f312ce6610a
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 765,952
Start LBA hex                              : 0xBB000
End LBA inclusive                          : 798,719
End LBA inclusive hex                      : 0xC2FFF
Sector count                               : 32,768
Sector count hex                           : 0x8000
Start byte offset                          : 392,167,424
Start byte offset hex                      : 0x0000000017600000
End byte offset inclusive                  : 408,944,639
End byte offset inclusive hex              : 0x00000000185FFFFF
End byte offset exclusive                  : 408,944,640
End byte offset exclusive hex              : 0x0000000018600000
Exact byte size                            : 16,777,216
Exact byte size hex                        : 0x0000000001000000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

nvram physical partition:
Partition number                           : 25
GPT entry number                           : 25
Partition name                             : nvram
Block device                               : /dev/block/mmcblk0p25
Device name                                : mmcblk0p25
Device type                                : partition
Major:minor                                : 179:25
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 25
Kernel partition name                      : nvram
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 9c3cabd7-a35d-4b45-8c57-b80775426b35
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 798,720
Start LBA hex                              : 0xC3000
End LBA inclusive                          : 929,791
End LBA inclusive hex                      : 0xE2FFF
Sector count                               : 131,072
Sector count hex                           : 0x20000
Start byte offset                          : 408,944,640
Start byte offset hex                      : 0x0000000018600000
End byte offset inclusive                  : 476,053,503
End byte offset inclusive hex              : 0x000000001C5FFFFF
End byte offset exclusive                  : 476,053,504
End byte offset exclusive hex              : 0x000000001C600000
Exact byte size                            : 67,108,864
Exact byte size hex                        : 0x0000000004000000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

lk physical partition:
Partition number                           : 26
GPT entry number                           : 26
Partition name                             : lk
Block device                               : /dev/block/mmcblk0p26
Device name                                : mmcblk0p26
Device type                                : partition
Major:minor                                : 179:26
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 26
Kernel partition name                      : lk
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : e7099731-95a6-45a6-a1e5-1b6aba032cf1
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 929,792
Start LBA hex                              : 0xE3000
End LBA inclusive                          : 931,839
End LBA inclusive hex                      : 0xE37FF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 476,053,504
Start byte offset hex                      : 0x000000001C600000
End byte offset inclusive                  : 477,102,079
End byte offset inclusive hex              : 0x000000001C6FFFFF
End byte offset exclusive                  : 477,102,080
End byte offset exclusive hex              : 0x000000001C700000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 2,097,152 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

lk2 physical partition:
Partition number                           : 27
GPT entry number                           : 27
Partition name                             : lk2
Block device                               : /dev/block/mmcblk0p27
Device name                                : mmcblk0p27
Device type                                : partition
Major:minor                                : 179:27
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 27
Kernel partition name                      : lk2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 8273e1ab-846f-4468-b999-ee2ea8e50a16
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 931,840
Start LBA hex                              : 0xE3800
End LBA inclusive                          : 933,887
End LBA inclusive hex                      : 0xE3FFF
Sector count                               : 2,048
Sector count hex                           : 0x800
Start byte offset                          : 477,102,080
Start byte offset hex                      : 0x000000001C700000
End byte offset inclusive                  : 478,150,655
End byte offset inclusive hex              : 0x000000001C7FFFFF
End byte offset exclusive                  : 478,150,656
End byte offset exclusive hex              : 0x000000001C800000
Exact byte size                            : 1,048,576
Exact byte size hex                        : 0x0000000000100000
Alignment offset                           : 0 bytes
Discard alignment                          : 1,048,576 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

boot physical partition:
Partition number                           : 28
GPT entry number                           : 28
Partition name                             : boot
Block device                               : /dev/block/mmcblk0p28
Device name                                : mmcblk0p28
Device type                                : partition
Major:minor                                : 179:28
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 28
Kernel partition name                      : boot
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : d26472f1-9ebc-421d-ba14-311296457c90
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 933,888
Start LBA hex                              : 0xE4000
End LBA inclusive                          : 999,423
End LBA inclusive hex                      : 0xF3FFF
Sector count                               : 65,536
Sector count hex                           : 0x10000
Start byte offset                          : 478,150,656
Start byte offset hex                      : 0x000000001C800000
End byte offset inclusive                  : 511,705,087
End byte offset inclusive hex              : 0x000000001E7FFFFF
End byte offset exclusive                  : 511,705,088
End byte offset exclusive hex              : 0x000000001E800000
Exact byte size                            : 33,554,432
Exact byte size hex                        : 0x0000000002000000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

logo physical partition:
Partition number                           : 29
GPT entry number                           : 29
Partition name                             : logo
Block device                               : /dev/block/mmcblk0p29
Device name                                : mmcblk0p29
Device type                                : partition
Major:minor                                : 179:29
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 29
Kernel partition name                      : logo
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : b72ccbe9-2055-46f4-a167-4a069c201738
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 999,424
Start LBA hex                              : 0xF4000
End LBA inclusive                          : 1,015,807
End LBA inclusive hex                      : 0xF7FFF
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 511,705,088
Start byte offset hex                      : 0x000000001E800000
End byte offset inclusive                  : 520,093,695
End byte offset inclusive hex              : 0x000000001EFFFFFF
End byte offset exclusive                  : 520,093,696
End byte offset exclusive hex              : 0x000000001F000000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

dtbo physical partition:
Partition number                           : 30
GPT entry number                           : 30
Partition name                             : dtbo
Block device                               : /dev/block/mmcblk0p30
Device name                                : mmcblk0p30
Device type                                : partition
Major:minor                                : 179:30
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 30
Kernel partition name                      : dtbo
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 9c1520f3-c2c5-4b89-8242-fe4c61208a9e
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 1,015,808
Start LBA hex                              : 0xF8000
End LBA inclusive                          : 1,032,191
End LBA inclusive hex                      : 0xFBFFF
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 520,093,696
Start byte offset hex                      : 0x000000001F000000
End byte offset inclusive                  : 528,482,303
End byte offset inclusive hex              : 0x000000001F7FFFFF
End byte offset exclusive                  : 528,482,304
End byte offset exclusive hex              : 0x000000001F800000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

tee1 physical partition:
Partition number                           : 31
GPT entry number                           : 31
Partition name                             : tee1
Block device                               : /dev/block/mmcblk0p31
Device name                                : mmcblk0p31
Device type                                : partition
Major:minor                                : 179:31
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 31
Kernel partition name                      : tee1
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 902d5f3f-434a-4de7-8988-321e88c9b8aa
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 1,032,192
Start LBA hex                              : 0xFC000
End LBA inclusive                          : 1,042,431
End LBA inclusive hex                      : 0xFE7FF
Sector count                               : 10,240
Sector count hex                           : 0x2800
Start byte offset                          : 528,482,304
Start byte offset hex                      : 0x000000001F800000
End byte offset inclusive                  : 533,725,183
End byte offset inclusive hex              : 0x000000001FCFFFFF
End byte offset exclusive                  : 533,725,184
End byte offset exclusive hex              : 0x000000001FD00000
Exact byte size                            : 5,242,880
Exact byte size hex                        : 0x0000000000500000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

tee2 physical partition:
Partition number                           : 32
GPT entry number                           : 32
Partition name                             : tee2
Block device                               : /dev/block/mmcblk0p32
Device name                                : mmcblk0p32
Device type                                : partition
Major:minor                                : 259:0
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 32
Kernel partition name                      : tee2
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : bece74c8-d8e2-4863-9bfe-5b0b66bb920f
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 1,042,432
Start LBA hex                              : 0xFE800
End LBA inclusive                          : 1,064,959
End LBA inclusive hex                      : 0x103FFF
Sector count                               : 22,528
Sector count hex                           : 0x5800
Start byte offset                          : 533,725,184
Start byte offset hex                      : 0x000000001FD00000
End byte offset inclusive                  : 545,259,519
End byte offset inclusive hex              : 0x00000000207FFFFF
End byte offset exclusive                  : 545,259,520
End byte offset exclusive hex              : 0x0000000020800000
Exact byte size                            : 11,534,336
Exact byte size hex                        : 0x0000000000B00000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,145,728 bytes
Kernel read-only state                     : 1
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

super physical partition:
Partition number                           : 33
GPT entry number                           : 33
Partition name                             : super
Block device                               : /dev/block/mmcblk0p33
Device name                                : mmcblk0p33
Device type                                : partition
Major:minor                                : 259:1
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 33
Kernel partition name                      : super
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : ff1342cf-b7be-44d5-a25e-a435addd2702
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 1,064,960
Start LBA hex                              : 0x104000
End LBA inclusive                          : 9,453,567
End LBA inclusive hex                      : 0x903FFF
Sector count                               : 8,388,608
Sector count hex                           : 0x800000
Start byte offset                          : 545,259,520
Start byte offset hex                      : 0x0000000020800000
End byte offset inclusive                  : 4,840,226,815
End byte offset inclusive hex              : 0x00000001207FFFFF
End byte offset exclusive                  : 4,840,226,816
End byte offset exclusive hex              : 0x0000000120800000
Exact byte size                            : 4,294,967,296
Exact byte size hex                        : 0x0000000100000000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 1
Holder count                               : 3
Holders                                    : dm-0, dm-1, dm-2
dm-0 major:minor                           : 253:0
dm-0 sector count                          : 2,298,328
dm-0 exact byte size                       : 1,176,743,936
dm-0 slave block device                    : /dev/block/mmcblk0p33
dm-1 major:minor                           : 253:1
dm-1 sector count                          : 2,516,312
dm-1 exact byte size                       : 1,288,351,744
dm-1 slave block device                    : /dev/block/mmcblk0p33
dm-2 major:minor                           : 253:2
dm-2 sector count                          : 568,848
dm-2 exact byte size                       : 291,250,176
dm-2 slave block device                    : /dev/block/mmcblk0p33
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

vbmeta physical partition:
Partition number                           : 34
GPT entry number                           : 34
Partition name                             : vbmeta
Block device                               : /dev/block/mmcblk0p34
Device name                                : mmcblk0p34
Device type                                : partition
Major:minor                                : 259:2
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 34
Kernel partition name                      : vbmeta
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : a4da8f1b-fe07-433b-95cb-84a5f23e477b
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 9,453,568
Start LBA hex                              : 0x904000
End LBA inclusive                          : 9,469,951
End LBA inclusive hex                      : 0x907FFF
Sector count                               : 16,384
Sector count hex                           : 0x4000
Start byte offset                          : 4,840,226,816
Start byte offset hex                      : 0x0000000120800000
End byte offset inclusive                  : 4,848,615,423
End byte offset inclusive hex              : 0x0000000120FFFFFF
End byte offset exclusive                  : 4,848,615,424
End byte offset exclusive hex              : 0x0000000121000000
Exact byte size                            : 8,388,608
Exact byte size hex                        : 0x0000000000800000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

cache physical partition:
Partition number                           : 35
GPT entry number                           : 35
Partition name                             : cache
Block device                               : /dev/block/mmcblk0p35
Device name                                : mmcblk0p35
Device type                                : partition
Major:minor                                : 259:3
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 35
Kernel partition name                      : cache
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : c2635e15-61aa-454e-9c40-ebe1bdf19b9b
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 9,469,952
Start LBA hex                              : 0x908000
End LBA inclusive                          : 10,354,687
End LBA inclusive hex                      : 0x9DFFFF
Sector count                               : 884,736
Sector count hex                           : 0xD8000
Start byte offset                          : 4,848,615,424
Start byte offset hex                      : 0x0000000121000000
End byte offset inclusive                  : 5,301,600,255
End byte offset inclusive hex              : 0x000000013BFFFFFF
End byte offset exclusive                  : 5,301,600,256
End byte offset exclusive hex              : 0x000000013C000000
Exact byte size                            : 452,984,832
Exact byte size hex                        : 0x000000001B000000
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

userdata physical partition:
Partition number                           : 36
GPT entry number                           : 36
Partition name                             : userdata
Block device                               : /dev/block/mmcblk0p36
Device name                                : mmcblk0p36
Device type                                : partition
Major:minor                                : 259:4
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 36
Kernel partition name                      : userdata
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 4d2d1290-36a3-4f5d-afb4-319f8ab6dcd8
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 10,354,688
Start LBA hex                              : 0x9E0000
End LBA inclusive                          : 244,156,382
End LBA inclusive hex                      : 0xE8D87DE
Sector count                               : 233,801,695
Sector count hex                           : 0xDEF87DF
Start byte offset                          : 5,301,600,256
Start byte offset hex                      : 0x000000013C000000
End byte offset inclusive                  : 125,008,068,095
End byte offset inclusive hex              : 0x0000001D1B0FBDFF
End byte offset exclusive                  : 125,008,068,096
End byte offset exclusive hex              : 0x0000001D1B0FBE00
Exact byte size                            : 119,706,467,840
Exact byte size hex                        : 0x0000001BDF0FBE00
Alignment offset                           : 0 bytes
Discard alignment                          : 0 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

otp physical partition:
Partition number                           : 37
GPT entry number                           : 37
Partition name                             : otp
Block device                               : /dev/block/mmcblk0p37
Device name                                : mmcblk0p37
Device type                                : partition
Major:minor                                : 259:5
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 37
Kernel partition name                      : otp
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : fdce12f0-a7eb-40f7-8350-960972e6cb57
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 244,156,383
Start LBA hex                              : 0xE8D87DF
End LBA inclusive                          : 244,244,446
End LBA inclusive hex                      : 0xE8EDFDE
Sector count                               : 88,064
Sector count hex                           : 0x15800
Start byte offset                          : 125,008,068,096
Start byte offset hex                      : 0x0000001D1B0FBE00
End byte offset inclusive                  : 125,053,156,863
End byte offset inclusive hex              : 0x0000001D1DBFBDFF
End byte offset exclusive                  : 125,053,156,864
End byte offset exclusive hex              : 0x0000001D1DBFBE00
Exact byte size                            : 45,088,768
Exact byte size hex                        : 0x0000000002B00000
Alignment offset                           : 0 bytes
Discard alignment                          : 3,162,624 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

flashinfo physical partition:
Partition number                           : 38
GPT entry number                           : 38
Partition name                             : flashinfo
Block device                               : /dev/block/mmcblk0p38
Device name                                : mmcblk0p38
Device type                                : partition
Major:minor                                : 259:6
Parent block device                        : /dev/block/mmcblk0
Kernel partition number                    : 38
Kernel partition name                      : flashinfo
GPT type GUID                              : ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
GPT unique partition GUID                  : 0fbbafa2-4aa9-4490-8983-5329328505fd
GPT attribute bitfield                     : 0x0000000000000000
Start LBA                                  : 244,244,447
Start LBA hex                              : 0xE8EDFDF
End LBA inclusive                          : 244,277,214
End LBA inclusive hex                      : 0xE8F5FDE
Sector count                               : 32,768
Sector count hex                           : 0x8000
Start byte offset                          : 125,053,156,864
Start byte offset hex                      : 0x0000001D1DBFBE00
End byte offset inclusive                  : 125,069,934,079
End byte offset inclusive hex              : 0x0000001D1EBFBDFF
End byte offset exclusive                  : 125,069,934,080
End byte offset exclusive hex              : 0x0000001D1EBFBE00
Exact byte size                            : 16,777,216
Exact byte size hex                        : 0x0000000001000000
Alignment offset                           : 0 bytes
Discard alignment                          : 16,896 bytes
Kernel read-only state                     : 0
Holder count                               : 0
Holders                                    : none
Geometry reconciliation                    : RAW_GPT_AND_LIVE_SYSFS_MATCH
Classification                             : VERIFIED_PHYSICAL_GPT_PARTITION

Physical partition map closure:
Physical partition count                   : 38
Populated GPT-entry count                  : 38
Kernel-exposed partition-object count      : 38
Partition-number reconciliation            : MATCH
Partition-name reconciliation              : MATCH
Start-LBA reconciliation                   : MATCH
End-LBA reconciliation                     : MATCH
Sector-count reconciliation                : MATCH
Major:minor reconciliation                 : MATCH
Unique partition-GUID reconciliation       : 38 unique nonzero values
GPT-attribute reconciliation               : all populated entries equal 0x0000000000000000
Internal extent continuity                 : continuous
Internal gap count                         : 0
Overlap count                              : 0
Final partition end LBA                    : 244,277,214
Final usable LBA                           : 244,277,214
Unresolved physical-partition-map fields   : 0
Major:minor source class                   : LIVE_SYSFS_CAPTURE
Alignment-offset source class              : LIVE_SYSFS_CAPTURE
Discard-alignment source class             : LIVE_SYSFS_CAPTURE
Holder-topology source class               : LIVE_SYSFS_CAPTURE
Derived partition geometry class           : EXACT_INTEGER_DERIVATION_FROM_VERIFIED_SECTOR_GEOMETRY
Open boundary count                        : 0
Subsection Status                          : CLOSED

###############################################################################
###############################################################################
# 2.6 — GPT PARTITION CONTENT MANIFEST 
###############################################################################
###############################################################################

## p1 — `boot_para`
| p1 | `boot_para` | 1,048,576 | Unrecognized fixed-layout binary parameter data; 72-byte preamble; three 376-byte records; no recognized filesystem, container, embedded signature, or semantic text | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `dc018e0d194642a7e860d9eedff530d643ebb343a18b7a6466e96b96fd4f46d4` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/boot_para.bin` |
| Object size | 1,048,576 bytes |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE |
| Binary content extent | `0x000000–0x0004AF`; 1,200 bytes |
| Last nonzero byte | `0x0004AD` |
| Zero-padding extent | `0x0004B0–0x000FFFFF`; 1,047,376 bytes |
| Preamble | `0x000000–0x000047`; 72 bytes |
| Initial raw bytes | `17 46 67 02` |
| Initial little-endian word | `0x02674617` |
| Initial-word identification | `OPEN_BOUNDARY` |
| Fixed-span record count | 3 |
| Record 1 | `0x000048–0x0001BF`; 376 bytes; initial LE32 value `0x00002C9B` |
| Record 2 | `0x0001C0–0x000337`; 376 bytes; initial LE32 value `0x000038BF` |
| Record 3 | `0x000338–0x0004AF`; 376 bytes; initial LE32 value `0x00006EF3` |
| Semantic text | NONE |
| Internal field semantics | `OPEN_BOUNDARY` |
| Content classification | `UNRECOGNIZED_FIXED_LAYOUT_BINARY_PARAMETER_DATA` |

## p2 — `recovery`
| p2 | `recovery` | 33,554,432 | Android boot image v2 containing kernel, Android recovery ramdisk, recovery-DTBO table, DTB table, embedded VBMeta, padding, and AVB footer | `STOCK_RECOVERY_AT_CAPTURE`; TWRP installed after capture | `CLOSED` | `89d85239f4c749c6123f6afe483854cbd4b032a7f92fa98bda255ca19fb4807c` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/recovery.bin` |
| Object size | 33,554,432 bytes; `0x02000000` |
| Container | Android boot image header version 2 |
| Page size | 2,048 bytes |
| Header OS version | Android `10.0.0` |
| Header security patch | `2024-03` |
| Kernel command line | `bootopt=64S3,32N2,64N2 buildvariant=user` |
| Kernel component | Header size 9,246,082 bytes; gzip stream `0x00000800–0x008BBFB8`, 9,156,537 bytes; decompressed size 25,868,308 bytes |
| Kernel-appended FDT | FDT version 17; `0x008BBFB9–0x008D1D81`; 89,545 bytes |
| Recovery ramdisk | Gzip stream `0x008D2000–0x00EF2733`; 6,424,372 bytes; decompressed CPIO size 15,922,944 bytes; `cpio` report 31,100 blocks |
| Recovery ramdisk content | Android recovery and fastbootd executables, recovery UI resources, fstab data, supporting libraries, and recovery runtime files; no TWRP content observed |
| Recovery-DTBO table | Begins `0x00EF2800`; total size 30,038 bytes; one overlay FDT of 29,974 bytes at `0x00EF2840` |
| Boot DTB table | Begins `0x00EFA000`; total size 89,609 bytes; one FDT of 89,545 bytes at `0x00EFA040` |
| Original-image extent | `0x00000000–0x00F0FFFF`; 15,794,176 bytes |
| Embedded VBMeta | Offset `0x00F10000`; size 1,344 bytes |
| Post-VBMeta padding | `0x00F10540–0x01FFFFBF` |
| AVB footer | Final 64 bytes; footer version 1.0 |
| AVB algorithm | `SHA256_RSA2048` |
| AVB rollback index | 0 |
| AVB flags | 0 |
| AVB release string | `avbtool 1.1.0` |
| AVB descriptor scope | `recovery`; descriptor image size 15,794,176 bytes |
| Capture classification | `STOCK_RECOVERY_AT_CAPTURE`; TWRP built and installed after this dump was acquired |

## p3 — `para`
| p3 | `para` | 524,288 | Sparse MTK parameter/state object containing `bootonce-bootloader`, an `ENV_v1` variable block, and one unidentified fixed binary record | `PHYSICAL_DUMP_CAPTURE`; active captured state retained | `CLOSED` | `3ded1607a5d6d333c3bc5693d29012528b75c0ce4e6906fe6adc1adb730454ef` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/para.bin` |
| Object size | 524,288 bytes; `0x00080000` |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE |
| Region 1 | `0x000000–0x000013`; NUL-terminated ASCII `bootonce-bootloader` |
| Region 2 | `0x020000–0x023FFF`; `ENV_v1` variable block |
| `ENV_v1` variables | `md1_ccb_cap_gear=1`; `off-mode-charge=1`; `md1_ccb_gear_list=1(2,20);2(2,10);3(0,0);11(2,2)` |
| Terminal environment marker | Second `ENV_v1` at `0x023FF4`; terminal bytes `01 19 00 00` at `0x023FFC–0x023FFF` |
| Region 3 | Fixed binary record begins `0x040000`; last nonzero byte `0x040042` |
| Fixed-record field semantics | `OPEN_BOUNDARY` |
| Post-content state | `0x040043–0x07FFFF` is zero-filled |
| Content classification | `SPARSE_MTK_PARAMETER_AND_BOOT_STATE_DATA` |

## p4 — `expdb`
| p4 | `expdb` | 20,971,520 | MediaTek exception/crash diagnostic database containing mixed ASCII boot/DRAM/RAM-console logs, ten aligned AFTA-tagged binary record sectors, terminal binary fields, and zero-filled regions | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `2729d6f15a21767ff930f39ecea42c37b2ed705cf3b71b4ebd5e7f1c1527e65a` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/expdb.bin` |
| Object size | 20,971,520 bytes; `0x01400000` |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned no entries |
| Leading zero-filled region | `0x00000000–0x011FFFFF`; 18,874,368 bytes; independently verified against `/dev/zero` |
| Diagnostic tail region | `0x01200000–0x013FFFFF`; 2,097,152 bytes |
| First nonzero byte | `0x01200000` |
| Last nonzero byte | `0x013FFE17` |
| Bounded content extent | `0x01200000–0x013FFE17`; 2,096,664 bytes |
| Trailing zero-filled region | `0x013FFE18–0x013FFFFF`; 488 bytes; independently verified against `/dev/zero` |
| Full-partition nonzero-byte count | 2,084,890 |
| ASCII string candidates | 36,688 strings; 15,689 unique strings; length range 4–222 bytes |
| ASCII text structure | Mixed diagnostic records containing 2,011,352 printable ASCII bytes, 158 tab bytes, 36,291 carriage returns, and 36,918 line feeds |
| Line termination structure | 36,171 CRLF pairs; 120 bare CR bytes; 747 bare LF bytes |
| UTF-16LE text | NONE; zero strings at minimum length 4 |
| Observed diagnostic content | DRAM type and PMIC-trap reporting; DRAM fatal-error flag; RAM-console initialization and watchdog state; DRAM-buffer and MSDC-pool addresses; keypad initialization; eFuse-writer status; DRAM-size reporting; LastDRAMC register reporting |
| NUL structure | 116 contiguous NUL runs within the diagnostic tail; 12,262 NUL bytes; run lengths 1–507 bytes |
| Other binary bytes | 171 bytes outside NUL, tab, CR, LF, and printable ASCII classes |
| AFTA-tagged record count | 10 |
| AFTA marker position | Record-relative offset `0x2C` in every identified record sector |
| AFTA record sector 1 | `0x012B4A00–0x012B4BFF`; marker `0x012B4A2C` |
| AFTA record sector 2 | `0x012D0200–0x012D03FF`; marker `0x012D022C` |
| AFTA record sector 3 | `0x012EBC00–0x012EBDFF`; marker `0x012EBC2C` |
| AFTA record sector 4 | `0x01312000–0x013121FF`; marker `0x0131202C` |
| AFTA record sector 5 | `0x01336E00–0x01336FFF`; marker `0x01336E2C` |
| AFTA record sector 6 | `0x0135BC00–0x0135BDFF`; marker `0x0135BC2C` |
| AFTA record sector 7 | `0x01379400–0x013795FF`; marker `0x0137942C` |
| AFTA record sector 8 | `0x01396C00–0x01396DFF`; marker `0x01396C2C` |
| AFTA record sector 9 | `0x013B4200–0x013B43FF`; marker `0x013B422C` |
| AFTA record sector 10 | `0x013CB800–0x013CB9FF`; marker `0x013CB82C` |
| AFTA internal field semantics | `OPEN_BOUNDARY` |
| Terminal binary field block | `0x013FFE00–0x013FFE17`; 24 bytes |
| Terminal LE32 words | `0x785690EF`; `0x001CC200`; `0x00000000`; `0x00000000`; `0x00000000`; `0x0D15AB1E` |
| Terminal-field semantics | `OPEN_BOUNDARY` |
| Specific crash/reset-event attribution | `OPEN_BOUNDARY` |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_EXCEPTION_CRASH_DIAGNOSTIC_DATABASE_WITH_MIXED_TEXT_AND_FIXED_BINARY_RECORDS` |

## p5 — `vbmeta_system`
| p5 | `vbmeta_system` | 8,388,608 | Signed AVB metadata containing four build properties and dm-verity hashtree descriptors for `product` and `system` | `CURRENT_RESEARCH_CAPTURE`; AVB flags `3` | `CLOSED` | `210d8b3ba839f9997677e1bbc7340ac2f7dc09b139c6bb26f9e9868fdf145a26` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/vbmeta_system.bin` |
| Object size | 8,388,608 bytes; `0x00800000` |
| Recognized filesystem | NONE |
| AVB object extent | `0x000000–0x0007BF`; 1,984 bytes |
| AVB header block | 256 bytes |
| AVB authentication block | 320 bytes |
| AVB auxiliary block | 1,408 bytes |
| Last nonzero byte | `0x000787` |
| AVB alignment padding | `0x000788–0x0007BF`; 56 bytes |
| Zero-filled remainder | `0x0007C0–0x7FFFFF`; independently scanned nonzero-byte count 0 |
| Minimum libavb version | 1.0 |
| AVB algorithm | `SHA256_RSA2048` |
| AVB rollback index | 0 |
| AVB flags | 3; hashtree disabled and verification disabled |
| AVB release string | `avbtool 1.1.0` |
| Product OS property | `com.android.build.product.os_version=10` |
| Product patch property | `com.android.build.product.security_patch=2024-03-05` |
| System OS property | `com.android.build.system.os_version=10` |
| System patch property | `com.android.build.system.security_patch=2024-03-05` |
| Product hashtree | dm-verity version 1; image size 1,158,090,752 bytes; tree offset 1,158,090,752; tree size 9,125,888 bytes; data block 4,096 bytes; hash block 4,096 bytes; two FEC roots; FEC offset 1,167,216,640; FEC size 9,232,384 bytes; algorithm `sha256`; descriptor flags 0 |
| System hashtree | dm-verity version 1; image size 1,267,933,184 bytes; tree offset 1,267,933,184; tree size 9,990,144 bytes; data block 4,096 bytes; hash block 4,096 bytes; two FEC roots; FEC offset 1,277,923,328; FEC size 10,108,928 bytes; algorithm `sha256`; descriptor flags 0 |
| Capture classification | `CURRENT_RESEARCH_CAPTURE` |

## p6 — `vbmeta_vendor`
| p6 | `vbmeta_vendor` | 8,388,608 | Signed AVB metadata containing two build properties and one dm-verity hashtree descriptor for `vendor` | `CURRENT_RESEARCH_CAPTURE`; AVB flags `3` | `CLOSED` | `7f47526430e63774503bda0dcec8b3d72de92b80924db4798a7b9cbbbf625d97` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/vbmeta_vendor.bin` |
| Object size | 8,388,608 bytes; `0x00800000` |
| Recognized filesystem | NONE |
| AVB object extent | `0x000000–0x0005FF`; 1,536 bytes |
| AVB header block | 256 bytes |
| AVB authentication block | 320 bytes |
| AVB auxiliary block | 960 bytes |
| Last nonzero byte | `0x0005E7` |
| AVB alignment padding | `0x0005E8–0x0005FF`; 24 bytes |
| Zero-filled remainder | `0x000600–0x7FFFFF`; independently scanned nonzero-byte count 0 |
| Minimum libavb version | 1.0 |
| AVB algorithm | `SHA256_RSA2048` |
| AVB rollback index | 0 |
| AVB flags | 3; hashtree disabled and verification disabled |
| AVB release string | `avbtool 1.1.0` |
| Vendor OS property | `com.android.build.vendor.os_version=10` |
| Vendor patch property | `com.android.build.vendor.security_patch=2020-04-05` |
| Vendor hashtree | dm-verity version 1; image size 286,564,352 bytes; tree offset 286,564,352; tree size 2,265,088 bytes; data block 4,096 bytes; hash block 4,096 bytes; two FEC roots; FEC offset 288,829,440; FEC size 2,285,568 bytes; algorithm `sha256`; descriptor flags 0 |
| Capture classification | `CURRENT_RESEARCH_CAPTURE` |

## p7 — `frp`
| p7 | `frp` | 1,048,576 | Standard Android persistent-data-block/FRP layout containing a stored 32-byte digest field, partition marker, 77-byte Protobuf-compatible structured payload, zero-filled body, and terminal OEM-unlock flag | `CURRENT_RESEARCH_CAPTURE`; OEM-unlock flag `1`; payload factory-relative state `OPEN_BOUNDARY` | `CLOSED` | `7f42e19cf672ea84a2ad800bb2aad3f6bc168236169181d7c19f06054c283a8d` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/frp.bin` |
| Object size | 1,048,576 bytes; `0x00100000` |
| Recognized filesystem | NONE |
| Container / fixed layout | Android persistent-data-block / FRP fixed layout |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned no entries |
| Structured block extent | `0x000000–0x000074`; 117 bytes |
| Stored digest field | `0x000000–0x00001F`; 32 bytes |
| Stored digest bytes | `48 25 1D E1 E1 A5 3C B7 60 60 DA 93 84 87 3D 49 35 6A 39 12 3C 2E 09 D9 58 16 15 3D 49 CF D5 A8` |
| Partition-type marker | `0x000020–0x000023`; raw bytes `19 90 18 73`; big-endian value `0x19901873` |
| Payload-length field | `0x000024–0x000027`; raw bytes `00 00 00 4D`; big-endian value 77 bytes |
| Structured payload extent | `0x000028–0x000074`; 77 bytes |
| Payload encoding | Protobuf-compatible wire structure |
| Outer payload field | Field 1; wire type 2; tag `0x0A` at `0x000028`; encoded length 75 at `0x000029` |
| Nested-message extent | `0x00002A–0x000074`; 75 bytes |
| Nested field 1 | Field 1; wire type 0; tag `0x08` at `0x00002A`; varint bytes `BE 4E` at `0x00002B–0x00002C`; decoded value 10,046 |
| Nested field 2 | Field 2; wire type 0; tag `0x10` at `0x00002D`; value `0x20` at `0x00002E`; decoded value 32 |
| Nested field 3 | Field 3; wire type 2; tag `0x1A` at `0x00002F`; length 32 at `0x000030`; data extent `0x000031–0x000050` |
| Nested field 3 data | `8E 3E 7E EF 66 D0 1D FE 9E 24 3E 11 34 EA 7B 1B 40 62 47 0A 61 8C 34 F7 12 E3 D6 82 07 DE 05 36` |
| Nested field 4 | Field 4; wire type 2; tag `0x22` at `0x000051`; length 32 at `0x000052`; data extent `0x000053–0x000072` |
| Nested field 4 data | `CD EC 91 42 AF 7B B1 80 01 48 DD A6 3B B1 F2 87 CE B9 E7 47 F6 0B C1 1A BF 99 FA A3 58 D7 DB 6E` |
| Nested field 5 | Field 5; wire type 0; tag `0x28` at `0x000073`; value `0x01` at `0x000074`; decoded value 1 |
| Protobuf schema and field semantics | `OPEN_BOUNDARY` |
| Zero-filled region | `0x000075–0x0FFFFE`; 1,048,458 bytes; independently verified against `/dev/zero` |
| Terminal flag extent | `0x0FFFFF`; 1 byte |
| Terminal flag value | `0x01` |
| Terminal flag classification | OEM-unlock enabled flag; `CURRENT_RESEARCH_CAPTURE` |
| Full-partition nonzero-byte count | 115 |
| ASCII text | One incidental five-byte ASCII sequence `=I5j9` at `0x00000E–0x000012` inside the stored digest field; no standalone semantic text structure |
| UTF-16LE text | NONE; zero strings at minimum length 4 |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; bootloader-unlocked research state; structured-payload factory-relative state `OPEN_BOUNDARY` |
| Content classification | `ANDROID_PERSISTENT_DATA_BLOCK_WITH_FRP_PAYLOAD_AND_OEM_UNLOCK_FLAG` |

## p8 — `nvcfg`
| p8 | `nvcfg` | 33,554,432 | Clean ext4 vendor-configuration filesystem containing two live fuel-gauge state files, an inactive 4 MiB journal, empty sensor storage, and 1,015 unallocated residual fuel-gauge records | `CURRENT_RESEARCH_CAPTURE`; residual-record chronology and internal field semantics `OPEN_BOUNDARY` | `CLOSED` | `8fd092c584add9420a115b1c6bdb1b3ebaf11eb22121edf14cf80e73e6145d25` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/nvcfg.bin` |
| Object size | 33,554,432 bytes; `0x02000000` |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision |
| Filesystem extent | Entire evidence object, `0x00000000–0x01FFFFFF` |
| Filesystem UUID | `f892fcba-a0c9-4878-b29c-74faae323b1f` |
| Filesystem volume name | NONE |
| Last-mounted path | `/mnt/vendor/nvcfg` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 8,192 blocks; 8,192 inodes; one block group |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Filesystem flags | `unsigned_directory_hash` |
| Default mount options | `user_xattr acl` |
| Filesystem state | Clean |
| Read-only integrity check | `e2fsck -f -n` completed all five passes without error; 15/8,192 inodes and 1,297/8,192 blocks in use; 0.0% non-contiguous |
| Filesystem creation time | Thu Dec 31 18:00:28 2009 |
| Last mount time | Wed Apr 8 03:59:48 2026 |
| Last write time | Wed Apr 8 04:03:25 2026 |
| Last checked | Tue Apr 7 17:51:43 2026 |
| Mount count | 5; maximum mount count disabled |
| Lifetime writes | 998 MB |
| Inode geometry | First ordinary inode 11; inode size 128 bytes; 8,177 free inodes |
| Allocated blocks | Blocks `0–1294`, `3072`, and `3584`; 1,297 blocks total |
| Free blocks | Blocks `1295–3071`, `3073–3583`, and `3585–8191`; 6,895 blocks total |
| Primary filesystem structures | Superblock in block 0 at `0x00000400`; group descriptor block 1; reserved-GDT block 2; block bitmap block 3; inode bitmap block 4; inode table blocks `5–260` |
| Resize inode | Inode 7; indirect block 2 and double-indirect block 266 |
| Journal | Inode 8; 4,194,304 bytes; 1,024 blocks; physical blocks `267–1290`, `0x0010B000–0x0050AFFF` |
| Journal state | `journal_incompat_revoke`; sequence `0x000059DD`; journal start block 0; `logdump` reported transaction 23005 and no active transaction records |
| Root directory | Inode 2; mode `0771`; UID/GID `1000:1000`; physical block 261; entries `lost+found`, `fg`, and `sensor` |
| Root security attributes | `security.selinux="u:object_r:nvcfg_file:s0\0"`; `security.sehash=0c 4f 57 ef 06 73 b6 04 77 36 7b fd af f6 0a 19 c1 fe 8c 68` |
| `/lost+found` | Inode 11; directory; mode `0700`; UID/GID `0:0`; blocks `262–265`; no allocated child entries |
| `/fg` | Inode 12; directory; mode `0700`; UID/GID `1000:1000`; physical block 1294; contains `old_fg_data` and `car_tune_value` |
| `/sensor` | Inode 14; directory; mode `0700`; UID/GID `1000:1000`; physical block 1291; empty |
| Live regular-file count | 2 |
| `/fg/old_fg_data` | Inode 13; mode `0700`; UID/GID `1000:1000`; size 216 bytes; extent block 3584; SELinux context `u:object_r:nvcfg_file:s0` |
| `old_fg_data` physical extent | File data `0x00E00000–0x00E000D7`; independently verified zero slack `0x00E000D8–0x00E00FFF`, 3,880 bytes |
| `old_fg_data` structure | 54 little-endian 32-bit words; header words `0x00000056, 0x00000298, 0x000000D8, 0x00000001`; 37 nonzero words and 17 zero words |
| `old_fg_data` complete LE32 vector | `00000056 00000298 000000D8 00000001 0000001C 00008797 0000879A 00002328 00002710 00002710 0000002D 00001658 00001491 00009795 00001658 000098AE 000013E8 00000000 0000001B FFFFFFB0 00002A82 00000000 00000064 FFFFFE39 00009D9E 0000001C 0000001C 0000001B 000014CF 00001630 00000034 000031E3 0000376E 000031D3 0000375D 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000008 00000000 00000000 00000000 00000000 FFFFF18A 000096F5 0000185A 00000000` |
| `/fg/car_tune_value` | Inode 15; mode `0777`; UID/GID `1000:1000`; size 8 bytes; extent block 3072; SELinux context `u:object_r:nvcfg_file:s0` |
| `car_tune_value` content | Raw bytes `00 00 00 00 00 08 00 00`; two little-endian words `0x00000000, 0x00000800` |
| `car_tune_value` physical extent | File data `0x00C00000–0x00C00007`; independently verified zero slack `0x00C00008–0x00C00FFF`, 4,088 bytes |
| Unallocated residual-record count | 1,015 populated free blocks, each containing one 216-byte record at relative offsets `0x000–0x0D7` |
| Residual-record common header | All 1,015 records begin with LE32 words `0x00000056, 0x00000298, 0x000000D8, 0x00000001`, matching the live `old_fg_data` record structure |
| Residual-record logical content | 219,240 bytes total; 75,710 nonzero bytes |
| Residual-record block slack | No nonzero data after relative offset `0x0D7`; 3,938,200 bytes of verified zero slack across the 1,015 populated blocks |
| Residual-record nonzero distribution | 62–84 nonzero bytes per record; last nonzero at relative `0x0CD` for 2 records, `0x0D0` for 23 records, and `0x0D1` for 990 records |
| Residual run 1 | Blocks `1536–1908`; 373 records; first record `0x00600000–0x006000D7`; final record `0x00774000–0x007740D7` |
| Residual run 2 | Blocks `2048–2401`; 354 records; first record `0x00800000–0x008000D7`; final record `0x00961000–0x009610D7` |
| Residual run 3 | Blocks `2560–2592`; 33 records; first record `0x00A00000–0x00A000D7`; final record `0x00A20000–0x00A200D7` |
| Residual run 4 | Blocks `3073–3260`; 188 records; first record `0x00C01000–0x00C010D7`; final record `0x00CBC000–0x00CBC0D7` |
| Residual run 5 | Blocks `3585–3612`; 28 records; first record `0x00E01000–0x00E010D7`; final record `0x00E1C000–0x00E1C0D7` |
| Residual run 6 | Blocks `4096–4124`; 29 records; first record `0x01000000–0x010000D7`; final record `0x0101C000–0x0101C0D7` |
| Residual run 7 | Blocks `4608–4615`; 8 records; first record `0x01200000–0x012000D7`; final record `0x01207000–0x012070D7` |
| Residual run 8 | Blocks `5120–5121`; 2 records; first record `0x01400000–0x014000D7`; final record `0x01401000–0x014010D7` |
| Residual-record diversity | 1,014 byte-distinct payloads; one duplicate class containing two records; maximum identical multiplicity 2 |
| Live/residual comparison | Zero residual records are byte-identical to the captured live `/fg/old_fg_data` |
| Residual-record chronology | `OPEN_BOUNDARY`; physical order is recorded, but chronological order is not asserted |
| Fuel-gauge field semantics | `OPEN_BOUNDARY`; raw words are preserved without unsupported field-name assignments |
| ASCII strings | 158 occurrences at minimum length 4: 139 `/mnt/vendor/nvcfg`, two `jselinux`, seven single filesystem-name/xattr fragments, and ten incidental binary coincidences |
| UTF-16LE strings | NONE at minimum length 4 |
| Payload semantic text | NONE; observed meaningful ASCII is restricted to filesystem metadata, paths, directory entries, and security/xattr material |
| Initial zero area | `0x00000000–0x000003FF`; 1,024 bytes |
| First nonzero byte | `0x00000401` |
| Partition nonzero-byte count | 603,810 |
| Last nonzero byte | `0x014010D1` |
| Terminal zero-filled free space | `0x014010D2–0x01FFFFFF`; 12,578,606 bytes; independently verified |
| Completely zero free blocks | 5,880 of 6,895 free blocks |
| Embedded-signature scan | Outer Linux EXT filesystem signature only; no additional embedded container or signature identified |
| Recognizer discrepancy | Binwalk auxiliary 8,388,608-byte size and malformed UUID tail conflict with `stat`, `blkid`, `dumpe2fs`, raw superblock decoding, and full block geometry; those auxiliary values are excluded |
| Content classification | `EXT4_NVCFG_WITH_LIVE_FUEL_GAUGE_STATE_AND_UNALLOCATED_RESIDUAL_RECORDS` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE` |

## p9 — `nvdata`
| p9 | `nvdata` | 67,108,864 | Clean ext4 vendor NVRAM filesystem containing 296 regular files, configuration/calibration/modem record trees, an exact 159-file aggregate with a corresponding fixed-record map, an inactive 4 MiB journal, and completely zero-filled unallocated blocks | `CURRENT_RESEARCH_CAPTURE`; opaque NVRAM field semantics `OPEN_BOUNDARY` | `CLOSED` | `20bc94b5a4be4514421a4e67c5f94275b73a817e0a8994120335891524230057`|

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/nvdata.bin` |
| Object size | 67,108,864 bytes; `0x04000000` |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision |
| Filesystem extent | Entire evidence object, `0x00000000–0x03FFFFFF` |
| Filesystem UUID | `7db3fff2-83d3-4461-a6a7-965a0fff7478` |
| Filesystem volume name | NONE |
| Last-mounted path | `/mnt/vendor/nvdata` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 16,384 blocks; 16,384 inodes; one block group |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Filesystem flags | `unsigned_directory_hash` |
| Default mount options | `user_xattr acl` |
| Filesystem state | Clean |
| Read-only integrity check | `e2fsck -f -n` completed all five passes without error; 320/16,384 allocated inodes; 2,080/16,384 allocated blocks; 2.5% non-contiguous |
| Filesystem creation time | Wed Feb 4 10:36:33 2026 |
| Last mount time | Wed Apr 8 17:59:48 2026 |
| Last write time | Wed Apr 8 18:03:25 2026 |
| Last checked | Mon Apr 6 17:12:09 2026 |
| Mount count | 7; maximum mount count disabled |
| Lifetime writes | 325 MB |
| Inode geometry | First ordinary inode 11; inode size 128 bytes; 16,064 free inodes |
| Namespace population | 296 regular files and 15 directories, including the root directory |
| Allocated reserved non-directory inodes | 9 |
| Namespace symlinks and special files | NONE |
| Allocated blocks | 2,080 blocks; 8,519,680 bytes |
| Free blocks | 14,304 blocks; 58,589,184 bytes |
| Free-space fragmentation | 93 free extents; minimum 4 KiB; maximum 16,372 KiB; average 612 KiB |
| Free-block ranges | `1921–2048, 2050, 2053, 2056, 2064, 2066, 2068–2069, 2071, 2073, 2075, 2078, 2080–2559, 2561–2562, 2566, 2569, 2571, 2574, 2576–2577, 2582, 2584, 2586, 2589, 2592–2593, 2595–3073, 3075, 3077–3078, 3080–3081, 3083, 3085–3091, 3098–3583, 3585, 3587–3588, 3590–3594, 3596–3597, 3600–3602, 3606, 3608–3609, 3611, 3614–4095, 4097, 4099, 4101–4102, 4110, 4112, 4114–4115, 4117, 4122, 4124–4127, 4129–4608, 4610, 4612–4613, 4615–4617, 4619, 4621, 4623, 4626, 4628, 4630, 4634, 4639, 4641–4642, 4644, 4647, 4649, 4652–5120, 5124, 5127–5632, 5634, 5637, 5642, 5645–5647, 5649–5651, 5653, 5655, 5660, 5663, 5665, 5669, 5671–6146, 6149–6658, 6660–7167, 7169–7171, 7173–7679, 7681–7682, 7684–8191, 8196, 8198–8703, 8708, 8710–9215, 9218–9727, 9730–10752, 10754–12289, 12291–16383` |
| Unallocated free-space state | All 14,304 free blocks are zero-filled; complete `debugfs dump_unused` scan emitted no nonzero unused block |
| Unallocated residual records | NONE |
| Primary filesystem structures | Superblock in block 0 at `0x00000400`; group descriptor block 1; reserved-GDT blocks `2–4`; block bitmap block 5; inode bitmap block 6; inode table blocks `7–518` |
| Block-group state | Group 0 spans blocks `0–16383`; `ITABLE_ZEROED`; checksum `0x289C` |
| Resize inode | Inode 7; three reserved GDT blocks |
| Journal | Inode 8; 4,194,304 bytes; 1,024 blocks; physical blocks `525–1548`, `0x0020D000–0x0060CFFF` |
| Journal state | `journal_incompat_revoke`; sequence `0x0000414E`; journal start block 0; `logdump` reported transaction 16,718 and emitted no active transaction records |
| Root directory | Inode 2; mode `0771`; UID/GID `0:1000`; physical block 519 |
| Root entries | `lost+found`, `media`, `md`, `md3`, `AllFile`, `AllMap`, `APCFG`, `RestoreFlag`, `md_cmn` |
| Root security attributes | `security.selinux="u:object_r:nvdata_file:s0\0"`; `security.sehash=b1 ed 5d f8 ca 7e ea 7c dd 66 55 5d 61 fe 79 07 84 ee 54 b6` |
| Root extended-attribute block | Block 1551 |
| Shared namespace extended-attribute block | Block 1552 |
| Empty directories | `/lost+found`, `/md3`, `/md_cmn`, `/md/NVRAM/BACKUP`, `/md/NVRAM/NVD_CORE` |
| Regular-file logical-byte total | 1,133,466 bytes across 296 files |
| `/AllFile` | Inode 15; mode `0660`; UID/GID `0:1000`; size 441,975 bytes; physical blocks `1561–1668` |
| `/AllFile` physical content extent | `0x00619000–0x00684E76`; 441,975 bytes |
| `/AllFile` allocated-block slack | `0x00684E77–0x00684FFF`; 393 bytes; independently verified zero-filled |
| `/AllFile` structure | Exact byte-for-byte concatenation of 159 regular files from `APCFG/APRDEB`, `APCFG/APRDCL`, `md/NVRAM/NVD_IMEI`, and `md/NVRAM/CALIBRAT` |
| `/AllFile` source-byte reconciliation | `8,198 + 660 + 433,117 = 441,975` bytes; complete generated source stream compared equal with status 0 |
| `/AllMap` | Inode 16; mode `0660`; UID/GID `0:1000`; size 22,312 bytes; physical blocks `1669–1674` |
| `/AllMap` physical content extent | `0x00685000–0x0068A727`; 22,312 bytes |
| `/AllMap` allocated-block slack | `0x0068A728–0x0068AFFF`; 2,264 bytes; independently verified zero-filled |
| `/AllMap` structure | 52-byte header followed by 159 contiguous fixed-size records of 140 bytes each; `52 + (159 × 140) = 22,312` |
| `/AllMap` header extent | Logical `0x0000–0x0033`; 52 bytes |
| `/AllMap` header LE32 vector | `D38E45C6 00000033 0000001B 00040004 00930004 00000000 00000000 00000000 00000000 00000000 00000000 0000018A 00000000` |
| `/AllMap` header field semantics | `OPEN_BOUNDARY` |
| `/AllMap` record extent | Logical `0x0034–0x5727`; 22,260 bytes |
| `/AllMap` record leading fields | LE32 state value, `/AllFile` logical offset, and record byte length |
| `/AllMap` state values | All 159 records contain state value `7` |
| `/AllMap` offset continuity | Zero discontinuities; first offset 0; final record ends exactly at byte 441,975 |
| `/AllMap` path population | 159 absolute paths rooted at `/mnt/vendor/nvdata`; exact population used for successful full `/AllFile` byte comparison |
| `/AllMap` first record | Logical `0x0034–0x00BF`; `/mnt/vendor/nvdata/APCFG/APRDEB/WIFI_CUSTOM`; `/AllFile` offset 0; length 6 |
| `/AllMap` final record | Logical `0x569C–0x5727`; `/mnt/vendor/nvdata/md/NVRAM/CALIBRAT/ML0B_000`; `/AllFile` offset 441,863; length 112 |
| `/AllMap` repeated control text | `is_support_ecci_c2k 1\n`; `rt property get fail : \n` |
| `/AllMap` internal control-field semantics | `OPEN_BOUNDARY` |
| `/AllMap` recognizer discrepancy | Host `file` labeled the object an OpenPGP public key; fixed-record geometry, complete absolute-path population, and exact `/AllFile` mapping establish a vendor NVRAM map object instead |
| `/RestoreFlag` | Inode 182; mode `0660`; UID/GID `0:1000`; size 4 bytes; physical block 1885 |
| `/RestoreFlag` content | Raw bytes `78 56 34 12`; little-endian value `0x12345678` |
| `/RestoreFlag` field semantics | `OPEN_BOUNDARY` |
| `/media/Audio_ver1_Vol_custom` | Inode 197; mode `0660`; UID/GID `1041:1005`; size 583 bytes; physical block 2051; binary audio-level/calibration table |
| Audio-record field semantics | `OPEN_BOUNDARY` |
| `APCFG/APRDEB` population | Four files; 2,222 logical bytes; `WIFI_CUSTOM=6`, `GPS=54`, `WIFI=2,050`, `BT_Addr=112` |
| `APCFG/APRDEB` metadata | Mode `0660`; UID/GID `0:1000`, except `BT_Addr` GID `1002` |
| `APCFG/APRDCL` population | Four files; 5,976 logical bytes; `AUXADC=78`, `FILE_VER=5,846`, `FG=26`, `MD_SBP=26` |
| `APCFG/APRDCL` metadata | UID/GID `0:1000`; mode `0660`, except `FILE_VER` mode `0664` |
| `FILE_VER` structure | 79 fixed 74-byte records; exact size `79 × 74 = 5,846` bytes |
| `FILE_VER` observed registry | `NVRAM_VER_INFO`, `FILE_VER`, `BT_Addr`, `AUXADC`, camera, audio, Wi-Fi, hardware-monitor, modem-type, SDIO, product-information, smart-amplifier, and related configuration labels |
| `FILE_VER` record-field semantics | `OPEN_BOUNDARY` |
| `md/NVRAM/NVD_IMEI` population | Four files; 660 logical bytes; `LD0B_001=384`, `FILELIST=36`, `NV01_000=96`, `NV0S_000=144` |
| `md/NVRAM/NVD_IMEI` metadata | Mode `0660`; UID/GID `0:1000` |
| `md/NVRAM/CALIBRAT` population | 147 regular files; 433,117 logical bytes; sizes 76–127,048 bytes; mode `0660`; UID/GID `0:1000` |
| `md/NVRAM/CALIBRAT` complete inventory | `YS03_000=148; LA0N_002=88; UL1Z_000=168; HL13_001=530; LA0U_000=316; ULBY_000=3540; ML0D_000=16458; ULC1_000=3540; HL1G_000=144; UL16_001=530; LA26_000=4184; HL12_001=530; EL71_000=192; UL22_000=168; UL25_000=168; FILELIST=1323; HL18_001=530; UL13_001=530; UL20_000=168; EL03_001=122; HL1B_000=144; TD2B_000=76; HL15_001=530; EL76_000=1032; LA05_002=332; ML09_001=144; ULBW_000=3540; ULBU_000=3540; LA0L_002=88; EL74_000=192; LA08_002=736; LA0D_002=736; HL11_001=530; LA0I_002=80; UL12_001=530; ULB3_000=780; UL1O_000=172; UL1X_000=168; LA0C_002=736; UL1J_000=780; ULBT_000=3540; ULBV_000=3540; LA0E_002=136; EL91_001=688; LA0K_006=536; LA0P_003=104; LA0B_002=736; LA0A_002=736; EL73_000=192; LA0T_000=316; ULB0_001=530; LA0M_002=88; EA10_001=30528; EA40_001=121198; UL1U_000=172; UL1I_000=780; HL1H_000=144; EL70_000=192; HL1E_000=144; YS00_000=332; HL1A_000=144; LA0G_002=136; LA0V_002=397; ML5A_000=104; EA50_001=8722; EL04_001=472; ULB2_000=780; ULBR_000=3540; UL1P_000=172; ULB9_000=144; ULBZ_000=3540; HL19_001=530; EL90_001=25856; UL1B_000=780; LA1V_000=76; LA0Q_002=76; UL1A_001=530; UL1D_000=780; UL18_001=530; EL78_000=1032; UL1C_000=780; UL1W_000=168; ULB6_001=530; LA1U_000=88; ULBC_000=80; UL1Y_000=168; HL14_001=530; UL1S_000=172; UL1E_000=780; ULBQ_000=3540; UL23_000=168; LA0J_004=120; ULB5_000=172; HL1F_000=144; ULB7_001=530; ULB8_000=144; ULC0_000=3540; HL1I_000=144; UL15_001=530; ULB1_001=530; EL72_000=192; UL1V_000=172; UL1Q_000=172; ML4A_000=88; UL17_001=530; EL93_001=8764; LA0O_002=88; ML0C_000=240; UL1N_000=172; UL1T_000=172; LA0H_002=136; HL16_001=530; ULBB_000=168; EL77_000=1032; ULBA_000=168; EL75_000=1032; UL1G_000=780; ULBX_000=3540; UL21_000=168; EA03_001=80; UL1F_000=780; ULBD_000=76; UL1H_000=780; UL19_001=530; HL1D_000=144; UL24_000=168; HL10_001=530; UL1M_000=172; UL1K_000=780; HL17_001=530; UL1R_000=172; YS01_000=397; LA09_002=736; TD6Z_000=76; ULBS_000=3540; LA06_002=736; UL11_001=530; UL10_000=88; HL1J_000=144; LA07_002=736; EL92_001=127048; ULB4_000=172; EL79_000=1032; LA0F_002=136; UL14_001=530; HL1C_000=144; ML0B_000=112` |
| `/md/NVRAM/INFO_FILE` | Inode 187; mode `0660`; UID/GID `1001:1001`; size 1,628 bytes; physical block 10,753; begins with ASCII `INFO` |
| `INFO_FILE` internal semantics | `OPEN_BOUNDARY` |
| `md/NVRAM/NVD_DATA` population | 132 regular files; 224,989 logical bytes; sizes 73–18,164 bytes; mode `0660`; UID/GID `1001:1001` |
| `md/NVRAM/NVD_DATA` complete inventory | `SP82_000=160; IM83_026=376; IM03_001=128; ML2A_000=248; ML0A_001=132; EL01_001=572; IM90_008=140; EL0K_000=672; ML1A_000=272; MT6K_004=384; EL5C_000=672; ML1B_000=122; EL02_001=1372; ML3A_000=75; EL0L_000=488; ML5B_000=152; EL0S_000=136; NA84_000=1376; ML6B_000=392; EL5D_000=196; EL5G_000=94; ML0E_000=316; EL0R_000=74; ML7A_000=124; EL0X_000=74; ULC7_000=88; LA3S_000=76; EL0T_000=136; ULC8_000=84; EL0U_000=124; EL0W_000=136; EL0C_000=74; LA0R_000=92; LA0S_000=92; LA9W_000=868; LA3K_000=3560; LA3M_000=3560; LA3O_000=3560; LA3Q_000=3560; LA9X_000=148; LA9Y_000=18164; LA9Z_000=252; MT29_000=1168; YS04_000=120; TL10_001=584; TL11_000=3642; TL14_000=1172; TL15_000=1564; TL16_000=2012; UL03_000=1440; ULM1_000=2888; ULM2_000=2888; ULM3_000=2888; ULM4_000=2888; ULM5_000=2888; ULMQ_000=1296; ULMR_000=1296; ULMS_000=1296; ULMT_000=1296; ULMU_000=1296; ULML_000=264; ULMM_000=264; ULMN_000=264; ULMO_000=264; ULMP_000=264; ULMG_000=1976; ULMI_000=1976; ULMJ_000=1976; ULMK_000=1976; ULMB_000=360; ULMC_000=360; ULMD_000=360; ULME_000=360; ULMF_000=360; ULMV_000=88; ULMW_000=88; ULMX_000=88; ULMY_000=88; ULMZ_000=88; ULN1_000=200; ULN2_000=392; ULP1_000=78; ULP2_000=612; ULP3_000=432; ULP4_000=1032; ULP5_000=13672; ULP6_000=1032; ULP7_000=13672; ULP8_000=1032; ULP9_000=13672; ULPH_000=232; ULN5_000=73; ULN6_000=73; ULN7_000=73; ULN8_000=73; ULN9_000=73; LD6S_014=100; ELB8_002=116; ELB9_002=86; ELD4_000=664; IM79_077=5480; NA87_000=77; LD35_000=73; LD6G_003=160; LD4A_000=144; SD61_001=3664; MT91_000=128; SD62_001=2640; SD63_002=2128; LD4C_002=84; LD4G_002=8024; LD49_000=82; LD6H_003=11248; LD44_000=204; LD41_001=232; LD43_000=256; LD46_003=640; LD42_002=112; EC76_008=132; D2A0_000=1880; UM06_005=4572; SS1A_011=10072; SS11_000=4080; ER1A_001=1240; ER1E_001=1168; LTEF_005=84; LTEH_001=6608; ER1B_023=7104; ER1D_002=6496; EX04_000=11076; EC75_002=232; LD32_000=73` |
| NVRAM record encoding | Predominantly opaque fixed binary structures with directory-entry identifiers and bounded logical sizes |
| NVRAM record internal semantics | `OPEN_BOUNDARY` |
| ASCII strings | 2,509 occurrences at minimum length 4; 924 unique strings; length range 4–45 bytes |
| UTF-16LE strings | 732 occurrences at minimum length 4; 57 unique strings; length range 4–9 code units |
| Semantic text classes | Filesystem paths and metadata; `/AllMap` control text; `FILE_VER` labels; NVRAM record identifiers; incidental binary coincidences |
| Initial zero-filled region | `0x00000000–0x00000400`; 1,025 bytes; independently verified against `/dev/zero` |
| First nonzero byte | `0x00000401` |
| Partition nonzero-byte count | 671,540 |
| Last nonzero byte | `0x03002048`; decimal 50,339,912; allocated filesystem block 12,290 |
| Terminal zero-filled region | `0x03002049–0x03FFFFFF`; 16,768,951 bytes; independently verified against `/dev/zero` |
| Embedded-signature recognition | Outer ext4 filesystem confirmed; no additional embedded container established |
| Capture classification |`CURRENT_RESEARCH_CAPTURE`; mutable vendor NVRAM state acquired after bootloader unlock and Magisk-root research-state changes|
| Content classification | `EXT4_NVDATA_WITH_CONFIGURATION_CALIBRATION_MODEM_RECORDS_AND_EXACT_AGGREGATE_INDEX` |

## p10 — `md_udc`
| p10 | `md_udc` | 23,699,456 | Clean Android `/metadata` ext4 filesystem containing seven live directories beneath root, no live regular files, an inactive 4 MiB journal, and deleted `/metadata/vold/checkpoint` residue | `CURRENT_RESEARCH_CAPTURE`; deleted UDC checkpoint control-file residue; original checkpoint value and exact lifecycle outcome `OPEN_BOUNDARY` | `CLOSED` | `f63e29883f197f2198f89c1cc6caea5be7c551940daef25a73c5c1c88b3e2099` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/md_udc.bin` |
| Object size | 23,699,456 bytes; `0x0169A000` |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision |
| Filesystem extent | Entire evidence object, `0x00000000–0x01699FFF`; no wrapper or trailing bytes |
| Filesystem UUID | `2fdda847-b389-4139-9dbc-f7829dcfb030` |
| Filesystem volume name | NONE |
| Last-mounted path | `/metadata` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 5,786 blocks; 5,792 inodes; one block group |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Filesystem flags | `unsigned_directory_hash` |
| Default mount options | `user_xattr acl` |
| Default directory hash | `half_md4` |
| Directory hash seed | `fa7f35ff-d072-4d8a-b5fa-6e04b9addaf4` |
| Filesystem state | Clean |
| Error behavior | Continue |
| Read-only integrity check | `e2fsck -f -n` completed all five passes without error; exit status 0; 17/5,792 inodes and 1,228/5,786 blocks in use; 0.0% non-contiguous |
| Filesystem creation time | Fri Jan 1 09:00:22 2010 |
| Last mount time | Wed Apr 8 17:59:44 2026 |
| Last write time | Wed Apr 8 18:03:25 2026 |
| Last checked | Fri Jan 1 09:00:22 2010 |
| Mount count | 47; maximum mount count disabled |
| Lifetime writes | 69 MB |
| Reserved blocks | 57; reserved UID/GID `0:0` |
| Inode geometry | First ordinary inode 11; inode size 128 bytes; 5,775 free inodes |
| Directory count | Eight allocated directories including root |
| Live ordinary inodes | Inodes `11–17`; all seven are directories |
| Live regular-file count | 0 |
| Allocated blocks | Blocks `0–1215`, `5760–5761`, and `5776–5785`; 1,228 blocks total |
| Free blocks | Blocks `1216–5759` and `5762–5775`; 4,558 blocks total; 78.8% |
| Free-space extents | Two extents: 4,544 blocks and 14 blocks |
| Primary filesystem structures | Superblock in block 0 at `0x00000400`; group descriptor block 1; reserved-GDT block 2; block bitmap block 3; inode bitmap block 4; inode table blocks `5–185` |
| Resize inode | Inode 7; double-indirect block 191 and indirect block 2 |
| Journal | Inode 8; mode `0600`; 4,194,304 bytes; 1,024 blocks; physical blocks `192–1215`, `0x000C0000–0x004BFFFF` |
| Journal configuration | No journal feature flags; sequence `0x00000075`; journal start block 0 |
| Journal state | Inactive and clean; `logdump` reported transaction 117 and no active transaction records |
| Journal residual extent | Nonzero historical journal material is confined to journal logical blocks `0–19`, physical blocks `192–211`; first byte `0x000C0000`, last nonzero byte `0x000D303A` |
| Journal residual volume | 11,511 nonzero bytes within the 4,194,304-byte journal |
| Journal residual structures | JBD2 superblock plus historical descriptor, data, and commit blocks; transaction 115 records inode-table block 5, while transaction 28 records `/vold` directory, inode-table, reserved-GDT, bitmap, group-descriptor, and xattr metadata |
| Journal residual text | Contains `/metadata`, `checkpoint`, SELinux attribute names, `u:object_r:vold_metadata_file:s0`, and `u:object_r:gsi_metadata_file:s0`; no recoverable regular-file payload |
| Root directory | Inode 2; mode `0755`; UID/GID `0:0`; size 4,096 bytes; block 186, `0x000BA000–0x000BAFFF` |
| Root entries | `lost+found`, `vold`, `password_slots`, `apex`, and `gsi` |
| Root security attributes | `security.selinux="u:object_r:metadata_file:s0\0"`; `security.sehash=d2 83 7d bf 62 6f 7b e5 ea 38 ec cf d1 96 23 ac 13 77 f0 af` |
| Root xattr block | Block 5785; reference count 1 |
| `/lost+found` | Inode 11; directory; mode `0700`; UID/GID `0:0`; size 16,384 bytes; blocks `187–190`; no allocated child entries |
| `/lost+found` security attributes | `security.selinux="u:object_r:metadata_file:s0\0"`; `security.sehash=c3 0f fb ad 8d b7 7f 54 5d 71 81 86 40 2d 08 73 4b 25 26 b8` |
| `/lost+found` xattr block | Block 5784; reference count 1 |
| `/vold` | Inode 12; directory; mode `0700`; UID/GID `0:0`; size 4,096 bytes; block 5777, `0x01691000–0x01691FFF`; logically empty |
| `/vold` timestamps | ctime Wed Apr 8 17:59:48 2026; mtime Fri Mar 20 17:16:22 2026 |
| `/vold` security attribute | `security.selinux="u:object_r:vold_metadata_file:s0\0"` |
| `/vold` xattr block | Block 5776; active reference count 1 |
| `/password_slots` | Inode 13; directory; mode `0771`; UID/GID `0:1000`; size 4,096 bytes; block 5779, `0x01693000–0x01693FFF`; empty |
| `/password_slots` security attribute | `security.selinux="u:object_r:password_slot_metadata_file:s0\0"` |
| `/password_slots` xattr block | Block 5778; reference count 1 |
| `/apex` | Inode 14; directory; mode `0700`; UID/GID `0:1000`; size 4,096 bytes; block 5781, `0x01695000–0x01695FFF`; contains `sessions` |
| `/apex/sessions` | Inode 15; directory; mode `0700`; UID/GID `0:1000`; size 4,096 bytes; block 5782, `0x01696000–0x01696FFF`; empty |
| APEX security attribute | Both directories carry `security.selinux="u:object_r:apex_metadata_file:s0\0"` |
| APEX shared xattr block | Block 5780; reference count 2 |
| `/gsi` | Inode 16; directory; mode `0771`; UID/GID `0:1000`; size 4,096 bytes; block 5760, `0x01680000–0x01680FFF`; contains `dsu` |
| `/gsi/dsu` | Inode 17; directory; mode `0771`; UID/GID `0:1000`; size 4,096 bytes; block 5761, `0x01681000–0x01681FFF`; empty |
| GSI security attribute | Both directories carry `security.selinux="u:object_r:gsi_metadata_file:s0\0"` |
| GSI shared xattr block | Block 5783; reference count 2 |
| External-xattr blocks | Blocks `5776`, `5778`, `5780`, `5783`, `5784`, and `5785` |
| Xattr reference accounting | Reference counts respectively `1, 1, 2, 2, 1, 1`; shared-block ownership is fully accounted for |
| Deleted directory entry | `/vold` slack preserves inode 18, type regular file, name `checkpoint`; record begins at physical offset `0x01691018`, with name bytes at `0x01691020–0x01691029` |
| Deleted-entry state | Not live: the preceding `..` entry has record length `0x0FF4` and logically consumes the remainder of the directory block |
| Deleted inode | Inode 18; inode bitmap marks it free; preserved inode-table record at `0x00005880–0x000058FF` |
| Deleted inode type | Regular file; mode `0600`; UID/GID `0:0`; zero links; size 0; no data extents |
| Deleted inode time | dtime, ctime, and mtime Fri Mar 20 17:16:22 2026; atime Fri Mar 20 12:38:39 2026 |
| Deleted inode generation | `1702752565`; `0x657DF135` |
| Deleted inode stale xattr pointer | Block 5776, the currently allocated `/vold` SELinux xattr block; its on-disk reference count remains correctly 1 because inode 18 is free |
| Deleted inode security residue | `security.selinux="u:object_r:vold_metadata_file:s0\0"` |
| Deleted checkpoint content | NONE recoverable; inode size and block count are both zero |
| Checkpoint-string occurrences | Exactly two: journal offset `0x000C8020` and `/vold` directory-slack offset `0x01691020` |
| Checkpoint semantic resolution | AOSP hard-codes `/metadata/vold/checkpoint` as the User Data Checkpoint retry/slot control file, writes it when starting a checkpoint, and removes it after commit; the captured residue matches that path and purpose |
| Checkpoint state conclusion | No live `/metadata/vold/checkpoint` exists in this capture; a particular retry value, boot-slot value, or checkpoint success/failure result cannot be reconstructed |
| Checkpoint semantic source | [AOSP `Checkpoint.cpp`](https://android.googlesource.com/platform/system/vold/+/master/Checkpoint.cpp) |
| Metadata-partition role source | [Android User Data Checkpoint documentation](https://source.android.com/docs/core/ota/user-data-checkpoint) identifies `/metadata` as the early-mounted ext4 partition used for UDC retry state and keys |
| SELinux role corroboration | [AOSP SEPolicy type definitions](https://android.googlesource.com/platform/system/sepolicy/+/refs/heads/android-t-preview-1/public/file.te) identify `vold_metadata_file`, `gsi_metadata_file`, `password_slot_metadata_file`, and `apex_metadata_file`; the password-slot type is specifically described as shared Weaver-slot information |
| Free-block residual scan | `debugfs dump_unused` produced no residual blocks |
| Free-block zero verification | All 4,558 free blocks are byte-for-byte zero; 18,669,568 verified zero bytes |
| Large zero extent | Blocks `1216–5759`; 18,612,224 zero bytes |
| Secondary zero extent | Blocks `5762–5775`; 57,344 zero bytes |
| Completely zero free blocks | 4,558 of 4,558 |
| Directory-slack result | The deleted `checkpoint` entry is the only non-live pathname recovered from live directory blocks |
| ASCII strings | 35 occurrences at minimum length 4 |
| ASCII interpretation | Meaningful text is restricted to filesystem metadata, live directory names, SELinux attributes, `/metadata`, and the deleted `checkpoint` name; remaining short strings are incidental binary coincidences |
| UTF-16LE strings | NONE at minimum length 4 |
| Live payload semantic text | NONE; the filesystem contains no live regular-file data |
| Initial zero area | `0x00000000–0x000003FF`; 1,024 bytes |
| First nonzero byte | `0x00000400` |
| Partition nonzero-byte count | 19,459 |
| Partition zero-byte count | 23,679,997 |
| Last nonzero byte | `0x01699FFE`; part of the root SELinux xattr value in block 5785 |
| Terminal zero area | `0x01699FFF`; one byte |
| Embedded-signature scan | No ELF, ZIP, gzip, PNG, SQLite, Android boot-image, AVB, or FDT signature identified; outer ext4 filesystem only |
| Content classification | `EXT4_ANDROID_METADATA_DIRECTORY_SCAFFOLD_WITH_DELETED_UDC_CHECKPOINT_RESIDUE` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE` |
| Open boundary | Original deleted checkpoint contents and exact checkpoint lifecycle outcome are unrecoverable from this zero-length inode; no unsupported value or event outcome is asserted |

## p11 — `metadata`
| p11 | `metadata` | 33,554,432 | Completely zero-filled raw partition image containing no filesystem, container, header, allocation structure, text, key material, marker, or residual payload | `CURRENT_RESEARCH_CAPTURE`; inactive zero-filled metadata-named partition; original provisioning reason `OPEN_BOUNDARY` | `CLOSED` | `83ee47245398adee79bd9c0a8bc57b821e92aba10f5f9ade8a5d1fae4d8c4302` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/metadata.bin` |
| GPT partition | p11; `metadata` |
| Physical block device | `/dev/block/mmcblk0p11` |
| By-name mapping | `/dev/block/by-name/metadata` → `/dev/block/mmcblk0p11` |
| Major:minor | `179:11` |
| Device-node mode and ownership | `brw-------`; `root:root` |
| Observed device-node SELinux context | `u:object_r:block_device:s0` |
| GPT start LBA | 387,344 |
| GPT end LBA | 452,879 inclusive |
| GPT sector count | 65,536 sectors |
| GPT partition size | 33,554,432 bytes; 32 MiB |
| GPT unique partition GUID | `e8f0a5ef-8d1b-42ea-9c2a-835cd77de363` |
| Object size | 33,554,432 bytes; `0x02000000` |
| Evidence-object extent | `0x00000000–0x01FFFFFF` |
| Size reconciliation | 65,536 × 512-byte GPT sectors = 33,554,432 bytes; exact |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| `file -k` classification | `data` |
| `blkid` signature | NONE |
| ext2/ext3/ext4 probe | No valid superblock or ext-family magic; the object is not classified as a damaged ext filesystem |
| Internal partition table | NONE |
| Allocation structures | NONE |
| Directory or inode structures | NONE |
| Journal | NONE |
| Extended attributes | NONE |
| Distinct byte values | One: `0x00` |
| Nonzero-byte count | 0 |
| Zero-byte count | 33,554,432 |
| Zero-filled extent | Entire evidence object, `0x00000000–0x01FFFFFF` |
| First nonzero byte | NONE |
| Last nonzero byte | NONE |
| Initial zero area | Entire evidence object |
| Terminal zero area | Entire evidence object |
| Shannon entropy | `0.000000000` bits per byte |
| 512-byte sector accounting | 65,536 total sectors; 65,536 completely zero sectors; 0 nonzero sectors |
| 4,096-byte block accounting | 8,192 total blocks; 8,192 completely zero blocks; 0 nonzero blocks |
| Independent segmented verification | Eight separately compared 4 MiB ranges were all byte-for-byte identical to `/dev/zero` |
| Independent whole-object verification | All 33,554,432 bytes compared identical to `/dev/zero`; zero differences |
| Independent byte-removal verification | Removing every `0x00` byte left 0 bytes |
| ASCII strings | NONE at minimum length 4 |
| UTF-16LE strings | NONE at minimum length 4 |
| Embedded-signature scan | No ELF, ZIP, gzip, PNG, SQLite, Android boot-image, AVB, FDT, or ext-family signature |
| Structured payload | NONE |
| Cryptographic or FBE key material | NONE present in this capture |
| Persistent state | NONE encoded |
| Recoverable deleted content | NONE |
| Residual-data classification | NONE; no nonzero residual bytes exist |
| Operational witness separation | The GPT label `metadata` identifies p11, but the confirmed stock mount topology uses p10 `md_udc` as the `/metadata` filesystem source |
| Active `/metadata` source | Not p11; p10 `md_udc` is the populated ext4 filesystem associated with `/metadata` |
| p11 operational state | Present as a physical GPT partition and block device but empty and not the active `/metadata` source in the confirmed stock witness |
| Naming limitation | The GPT label alone does not establish filesystem content or active mount use |
| Original provisioning reason | `OPEN_BOUNDARY`; the all-zero image contains no evidence establishing why the metadata-named GPT entry was created or retained |
| Content classification | `FULLY_ZERO_FILLED_RAW_PARTITION_IMAGE` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` |

## p12 — `protect1`
| p12 | `protect1` | 8,388,608 | Clean ext4 `protect_f` modem-NVRAM filesystem containing seven live `LDI` record files, an inactive 4 MiB journal, zeroed extent slack, and two unallocated historical MT00 record copies | `CURRENT_RESEARCH_CAPTURE`; proprietary LID field semantics, stored-record overhead, and residual chronology `OPEN_BOUNDARY` | `CLOSED` | `232005b9a824ccf38d7eee090171c538de5f8a5598d6002a0ef940eaed720e3a` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/protect1.bin` |
| GPT partition | p12; `protect1` |
| Physical block device | `/dev/block/mmcblk0p12`; major:minor `179:12` |
| By-name mapping | `/dev/block/by-name/protect1` → `/dev/block/mmcblk0p12` |
| GPT start LBA | 452,880 |
| GPT end LBA | 469,263 inclusive |
| GPT sector count | 16,384 sectors |
| GPT unique partition GUID | `d5f0e175-a6e1-4db7-94c0-f82ad032950b` |
| Object size | 8,388,608 bytes; `0x00800000`; 8 MiB |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision |
| Filesystem extent | Entire evidence object, `0x00000000–0x007FFFFF` |
| Filesystem UUID | `9743e6fe-50d3-4276-ae5b-bb4d5b3b9d89` |
| Filesystem volume name | NONE |
| Last-mounted path | `/mnt/vendor/protect_f` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 2,048 blocks; 2,048 inodes; one block group |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Filesystem flags | `unsigned_directory_hash` |
| Default mount options | `user_xattr acl` |
| Default directory hash | `half_md4` |
| Directory hash seed | `dbb8e570-51cc-490b-b294-ef1b35e4eb4d` |
| Filesystem state | Clean |
| Error behavior | Continue |
| Read-only integrity check | `e2fsck -f -n` completed all five passes without error; exit status 0; 19/2,048 inodes and 1,115/2,048 blocks in use; 5.3% non-contiguous |
| Filesystem creation time | Thu Dec 31 18:00:28 2009 |
| Last mount time | Wed Apr 8 03:59:48 2026 |
| Last write time | Wed Apr 8 04:03:25 2026 |
| Last checked | Mon Apr 6 03:12:09 2026 |
| Mount count | 7; maximum mount count disabled |
| Lifetime writes | 207 MB |
| Reserved blocks | 20; reserved UID/GID `0:0` |
| Inode geometry | First ordinary inode 11; inode size 128 bytes; 2,029 free inodes |
| Allocated ordinary inodes | Inodes `11–19`; two directories and seven regular files |
| Directory count | Three including root |
| Live regular-file count | Seven |
| Allocated blocks | Blocks `0–1104` and `1538–1547`; 1,115 blocks total |
| Free blocks | Blocks `1105–1537` and `1548–2047`; 933 blocks total |
| Primary filesystem structures | Superblock in block 0 at `0x00000400`; group descriptor block 1; block bitmap block 2; inode bitmap block 3; inode table blocks `4–67` |
| Resize inode | Inode 7; double-indirect block 73 |
| Journal | Inode 8; mode `0600`; 4,194,304 bytes; 1,024 blocks; physical blocks `74–1097`, `0x0004A000–0x00449FFF` |
| Journal configuration | No journal feature flags; sequence `0x00003804`; journal start block 0 |
| Journal state | Inactive and clean; `logdump` reported transaction 14,340 and no active transaction records |
| Journal nonzero extent | All journal blocks contain historical metadata bytes; first nonzero `0x0004A000`, last nonzero `0x0044903B` |
| Journal byte accounting | 117,455 nonzero bytes and 4,076,849 zero bytes |
| Journal payload search | No `LDI` magic, MOLY version string, live filename, or live LDI payload identified |
| Journal semantic text | One historical `/mnt/vendor/protect_f` occurrence; remaining printable sequences are filesystem-metadata coincidences |
| Root directory | Inode 2; mode `0770`; UID/GID `1000:1000`; size 4,096 bytes; block 68, `0x00044000–0x00044FFF` |
| Root entries | `lost+found` and `md` |
| Root security attributes | `security.selinux="u:object_r:protect_f_data_file:s0\0"`; `security.sehash=0f 34 1a 3e a8 5f c6 ef 5b 48 7d 0f b8 c0 4d 45 22 80 df 9a` |
| Root xattr block | Block 1099; reference count 1 |
| `/lost+found` | Inode 11; directory; mode `0700`; UID/GID `0:0`; size 16,384 bytes; blocks `69–72`; no allocated child entries |
| `/lost+found` security attributes | `security.selinux="u:object_r:protect_f_data_file:s0\0"`; `security.sehash=fb d0 d0 e9 5e 00 c1 d7 14 8d 78 20 58 5c a8 74 48 48 2f 3f` |
| `/lost+found` xattr block | Block 1098; reference count 1 |
| `/md` | Inode 12; directory; mode `0770`; UID/GID `1001:1001`; size 4,096 bytes; block 1101, `0x0044D000–0x0044DFFF` |
| `/md` entries | `MT00A001`, `MT00B001`, `LD40_001`, `ST33_004`, `ST34_001`, `NA77_005`, and `MTFW_000` |
| Live-file ownership | All seven files have mode `0660`, UID/GID `1001:1001`, and one link |
| Shared security attribute | `/md` and all seven files carry `security.selinux="u:object_r:protect_f_data_file:s0\0"` |
| Shared xattr block | Block 1100; reference count 8 |
| Deleted inode scan | NONE; `lsdel` reported zero deleted inodes |
| Directory-slack scan | No deleted or unallocated pathname identified |
| Common live-file envelope | Every live file begins with a 64-byte header containing magic `LDI\0` |
| LDI header count/identifier field | Little-endian word at `0x04`; upper 16 bits match the stored-record count and lower 16 bits identify the LID |
| LDI declared-record field | Little-endian 32-bit value at `0x08`; matches the empirically resolved logical record size |
| Stored-record geometry | `(file size − 64) ÷ record count` resolves the physical stride exactly for all seven files |
| LDI identifier semantics | Proprietary per-LID functional assignments remain `OPEN_BOUNDARY`; filenames and raw identifiers are preserved without unsupported schema labels |
| Live logical-file bytes | 32,938 bytes total |
| Live logical-file nonzero bytes | 11,289 |
| Live logical-file zero bytes | 21,649 |
| Live file-data allocation | 14 filesystem blocks; 57,344 allocated bytes |
| Aggregate live extent slack | 24,406 bytes; independently verified completely zero |
| `/md/MT00A001` | Inode 13; 232 bytes; block 1104, `0x00450000–0x00450FFF`; 127 nonzero bytes; entropy 4.071292 bits/byte |
| `MT00A001` LDI geometry | LID `0xF000`; four records; declared size 34 bytes; stored stride 42 bytes; eight extra bytes per record |
| `MT00A001` record 0 | Payload `MOLY.LR12A.R3.MP.V107.4`; trailer `b8 61 52 b5 43 bf 62 66` |
| `MT00A001` record 1 | Payload `LR12A.R3.MP TK_MD_BASIC`; trailer `83 d4 25 5b 75 c5 ac 73` |
| `MT00A001` record 2 | Payload `LWTG_6177M_R3_6762`; trailer `29 e4 7d de 22 98 19 b9` |
| `MT00A001` record 3 | Payload `2021/01/20 17:26`; trailer `bb 1c 66 e6 32 c1 f8 5d` |
| `MT00A001` extent slack | `0x004500E8–0x00450FFF`; 3,864 bytes; all zero |
| `/md/MT00B001` | Inode 14; 232 bytes; block 1547, `0x0060B000–0x0060BFFF`; 127 nonzero bytes; entropy 4.071292 bits/byte |
| `MT00B001` LDI geometry | LID `0xF000`; four records; declared size 34 bytes; stored stride 42 bytes; eight extra bytes per record |
| `MT00B001` record content | All four logical payloads and trailers are byte-identical to `MT00A001` |
| Live MT00 comparison | The complete files differ at only two byte positions: header offset `0x2A` is `0xF7` in A and `0xF9` in B; offset `0x2C` is `0x01` in A and `0x02` in B |
| `MT00B001` extent slack | `0x0060B0E8–0x0060BFFF`; 3,864 bytes; all zero |
| `/md/LD40_001` | Inode 15; 19,264 bytes; extents block 1102 followed by blocks `1538–1541`; 1,715 nonzero bytes; entropy 0.666444 bits/byte |
| `LD40_001` LDI geometry | LID `0xEF09`; 100 records; declared size 184 bytes; stored stride 192 bytes; eight extra bytes per record |
| `LD40_001` record diversity | All 100 complete 192-byte records are byte-identical |
| `LD40_001` exact record structure | Byte 0 `FE`; bytes `1–175` zero; bytes `176–183` eight `FF` bytes; bytes `184–191` trailer `e3 dd 49 49 a7 44 c3 17` |
| `LD40_001` record byte accounting | 17 nonzero bytes and 175 zero bytes per stored record |
| `LD40_001` content conclusion | Not byte-empty: it contains 100 structurally populated low-information records; whether those values represent defaults, unset calibration, or another state remains `OPEN_BOUNDARY` |
| `LD40_001` physical layout | Logical block 0 at physical block 1102; logical blocks `1–4` at physical blocks `1538–1541` |
| `LD40_001` final extent slack | Final block 1541; 1,216 bytes; all zero |
| `/md/ST33_004` | Inode 16; 2,784 bytes; block 1103, `0x0044F000–0x0044FFFF`; 2,706 nonzero bytes; entropy 7.274733 bits/byte |
| `ST33_004` LDI geometry | LID `0xEF11`; ten records; declared size 260 bytes; stored stride 272 bytes; 12 extra bytes per record |
| `ST33_004` record diversity | All ten complete 272-byte stored records are byte-identical |
| `ST33_004` record composition | Each record has 269 nonzero bytes, three zero bytes, and 176 distinct byte values |
| `ST33_004` record boundaries | First record `0x40–0x14F`; subsequent records begin every `0x110` bytes through record 9 at `0x9D0` |
| `ST33_004` record anchors | First 16 bytes `64 54 56 59 ca eb ca f9 7d e0 b4 b7 dd 4b b1 98`; final 16 bytes `fc 7e 58 8f b7 04 fd 5c 76 d9 ae 3b b1 76 49 6b` |
| `ST33_004` interpretation boundary | High byte diversity does not independently establish calibration validity, encryption, or measurement provenance; specific field semantics remain `OPEN_BOUNDARY` |
| `ST33_004` extent slack | 1,312 bytes; all zero |
| `/md/ST34_001` | Inode 17; 832 bytes; block 1542, `0x00606000–0x00606FFF`; 742 nonzero bytes; entropy 5.104079 bits/byte |
| `ST34_001` LDI geometry | LID `0xEF12`; two records; declared size 362 bytes; stored stride 384 bytes; 22 extra bytes per record |
| `ST34_001` record diversity | The two complete 384-byte records are byte-identical |
| `ST34_001` internal units | Each record contains 24 consecutive 16-byte units and six distinct unit values |
| `ST34_001` dominant unit | `23 cd 4a 2b c6 e8 ce 46 16 49 fa d4 00 30 75 70`; occurs 19 times per record |
| `ST34_001` remaining units | Five additional 16-byte values occur once each: `3c42db552233c4e4f6f9562f83a44f7f`, `d0d2db8a0035c480bff5a9d46e3c65b7`, `c5fbb408c2e5d82d2c2471d1d5124734`, `dd868c437e09ffcd2600874a1448b282`, and `e60a185ce6135465f84fa4f60e7d8613` |
| `ST34_001` extent slack | 3,264 bytes; all zero |
| `/md/NA77_005` | Inode 18; 9,488 bytes; blocks `1543–1545`, `0x00607000–0x00609FFF`; 5,833 nonzero bytes; entropy 1.685417 bits/byte |
| `NA77_005` LDI geometry | LID `0x0480`; two records; declared size 4,704 bytes; stored stride 4,712 bytes; eight extra bytes per record |
| `NA77_005` record diversity | Two byte-distinct 4,712-byte stored records |
| `NA77_005` record 0 | 3,120 nonzero and 1,592 zero bytes; 56 distinct byte values; dominant values `FF:2487`, `00:1592`, `0F:240`, `01:59`, `03:30`; trailer `02 a3 95 ac 72 c7 6e 0c` |
| `NA77_005` record 1 | 2,695 nonzero and 2,017 zero bytes; 26 distinct byte values; dominant values `FF:2464`, `00:2017`, `0F:156`, `07:25`, `B4:7`; trailer `d9 0b 32 b3 be 33 4a ec` |
| `NA77_005` interpretation boundary | Exact table bytes and distributions are preserved; proprietary field assignments remain `OPEN_BOUNDARY` |
| `NA77_005` final extent slack | Final block 1545; 2,800 bytes; all zero |
| `/md/MTFW_000` | Inode 19; 106 bytes; block 1546, `0x0060A000–0x0060AFFF`; 39 nonzero bytes; entropy 2.600115 bits/byte |
| `MTFW_000` LDI geometry | LID `0xF202`; one record; declared size 34 bytes; stored stride 42 bytes; eight extra bytes |
| `MTFW_000` content | Payload `2021/01/20 17:26`; trailer `bb 1c 66 e6 32 c1 f8 5d` |
| `MTFW_000` relationship | Its logical payload and trailer are byte-identical to record 3 in both live MT00 files |
| `MTFW_000` extent slack | 3,990 bytes; all zero |
| Total LDI-envelope count | Nine |
| Live LDI offsets | `0x0044E000`, `0x0044F000`, `0x00450000`, `0x00606000`, `0x00607000`, `0x0060A000`, and `0x0060B000` |
| Unallocated LDI offsets | `0x00600000` and `0x00601000`, free blocks 1536 and 1537 |
| Residual block 1536 | Former 232-byte MT00A-form record followed by 3,864 zero bytes; 126 nonzero bytes |
| Residual block 1537 | Former 232-byte MT00B-form record followed by 3,864 zero bytes; 126 nonzero bytes |
| Residual-to-live MT00A comparison | Two differing bytes, offsets `0x2A–0x2B`: residual little-endian value `0x00D6`; live value `0x04F7`; all other 230 bytes identical |
| Residual-to-live MT00B comparison | Two differing bytes, offsets `0x2A–0x2B`: residual little-endian value `0x00D7`; live value `0x04F9`; all other 230 bytes identical |
| Residual logical volume | 464 bytes across two free blocks; 252 nonzero bytes |
| Residual block slack | 7,728 bytes; independently verified zero |
| Residual inode association | NONE recoverable; no deleted inode or deleted pathname points to either free block |
| Residual chronology | `OPEN_BOUNDARY`; the two-byte header changes establish distinct historical/current states but do not establish their wall-clock order or field meaning |
| Completely zero free blocks | 931 of 933 |
| Completely zero free-block volume | 3,813,376 bytes |
| Nonzero free blocks | Two: blocks 1536 and 1537 |
| Free-space nonzero-byte count | 252 |
| ASCII strings | 1,320 occurrences at minimum length 4; the majority are incidental journal/filesystem sequences |
| Meaningful ASCII | MOLY modem release `MOLY.LR12A.R3.MP.V107.4`; branch `LR12A.R3.MP TK_MD_BASIC`; platform string `LWTG_6177M_R3_6762`; timestamp `2021/01/20 17:26`; filesystem paths, filenames, and SELinux attributes |
| UTF-16LE strings | NONE at minimum length 4 |
| Initial zero area | `0x00000000–0x00000400`; 1,025 bytes |
| First nonzero byte | `0x00000401` |
| Partition nonzero-byte count | 137,734 |
| Partition zero-byte count | 8,250,874 |
| Last nonzero byte | `0x0060B0E7`; final byte of live `/md/MT00B001` |
| Terminal zero-filled area | `0x0060B0E8–0x007FFFFF`; 2,051,864 bytes |
| Embedded-signature scan | Outer ext4 filesystem and internal `LDI` record envelopes only; no ELF, ZIP, gzip, PNG, SQLite, Android boot-image, AVB, or FDT container identified |
| Recognizer correction | An initial newline-sensitive `grep` split the PNG signature and counted 26 isolated `0x1A` bytes; exact eight-byte PNG-signature comparison returned zero matches |
| Content classification | `EXT4_PROTECT_F_WITH_LIVE_MODEM_NVRAM_LDI_RECORDS_AND_TWO_UNALLOCATED_MT00_RESIDUALS` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE` |
| Open boundaries | Proprietary LID field meanings, variable per-record stored overhead, trailer algorithms, and unallocated-record chronology remain unresolved; raw structures are fully bounded without unsupported semantic assignments |

## p13 — `protect2`
| p13 | `protect2` | 11,395,072 | Clean ext4 `protect_s` modem-NVRAM filesystem containing five live `LDI` record files, an inactive 4 MiB journal with historical JBD2 metadata, completely zero-filled unallocated blocks, and no recoverable deleted or residual payload | `CURRENT_RESEARCH_CAPTURE`; proprietary LID field semantics, record-value meanings, stored-record overhead, and trailer algorithms `OPEN_BOUNDARY` | `CLOSED` | `5ff316b5f522d87c17957b48ea540b533d2da36e6d9a35d214a899d3b5a56633` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/protect2.bin` |
| GPT partition | p13; `protect2` |
| Physical block device | `/dev/block/mmcblk0p13`; major:minor `179:13` |
| By-name mapping | `/dev/block/by-name/protect2` → `/dev/block/mmcblk0p13` |
| GPT start LBA | 469,264 |
| GPT end LBA | 491,519 inclusive |
| GPT sector count | 22,256 sectors |
| GPT unique partition GUID | `1d9056e1-e139-4fca-8c0b-b75fd74d81c6` |
| Object size | 11,395,072 bytes; `0x00ADE000` |
| Evidence-object extent | `0x00000000–0x00ADDFFF` |
| Size reconciliation | 22,256 × 512-byte GPT sectors = 11,395,072 bytes; 2,782 × 4,096-byte filesystem blocks = 11,395,072 bytes; exact |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision |
| Filesystem extent | Entire evidence object, `0x00000000–0x00ADDFFF`; no wrapper or trailing bytes |
| Filesystem UUID | `666202e1-6301-42a1-8f86-9de9905284eb` |
| Filesystem volume name | NONE |
| Last-mounted path | `/mnt/vendor/protect_s` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 2,782 blocks; 2,784 inodes; one block group |
| Block-group state | Group 0 spans blocks `0–2781`; `ITABLE_ZEROED`; checksum `0x4C6E` |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Filesystem flags | `unsigned_directory_hash` |
| Default mount options | `user_xattr acl` |
| Default directory hash | `half_md4` |
| Directory hash seed | `9aa6c74b-e7ed-4b52-81e0-ff91947e9070` |
| Filesystem state | Clean |
| Error behavior | Continue |
| Read-only integrity check | `e2fsck -f -n` completed all five passes without error; exit status 0; 17/2,784 inodes and 1,135/2,782 blocks in use; 5.9% non-contiguous |
| Filesystem creation time | Thu Dec 31 18:00:28 2009 |
| Last mount time | Wed Apr 8 03:59:48 2026 |
| Last write time | Wed Apr 8 04:03:25 2026 |
| Last checked | Mon Apr 6 03:12:09 2026 |
| Mount count | 7; maximum mount count disabled |
| Lifetime writes | 206 MB |
| Reserved blocks | 27; reserved UID/GID `0:0` |
| Inode geometry | First ordinary inode 11; inode size 128 bytes; 2,767 free inodes |
| Allocated ordinary inodes | Inodes `11–17`; two directories and five regular files |
| Allocated reserved non-directory inodes | Nine |
| Directory count | Three including root |
| Live regular-file count | Five |
| Namespace symlinks and special files | NONE |
| Allocated blocks | Blocks `0–1123`, `1536–1537`, and `2048–2056`; 1,135 blocks; 4,648,960 bytes |
| Free blocks | Blocks `1124–1535`, `1538–2047`, and `2057–2781`; 1,647 blocks; 6,746,112 bytes |
| Free-space extents | Three extents containing respectively 412, 510, and 725 blocks |
| Primary filesystem structures | Superblock in block 0 at `0x00000400`; group descriptor block 1; block bitmap block 2; inode bitmap block 3; inode table blocks `4–90` |
| Resize inode | Inode 7; double-indirect block 96 |
| Journal | Inode 8; mode `0600`; 4,194,304 bytes; 1,024 blocks; physical blocks `97–1120`, `0x00061000–0x00460FFF` |
| Journal configuration | No journal feature flags; sequence `0x00003796`; journal start block 0 |
| Journal state | Inactive and clean; `logdump` reported transaction 14,230 and emitted no active transaction records |
| Journal nonzero extent | Every journal block contains historical data; first nonzero byte `0x00061000`; last nonzero byte `0x0046003B` |
| Journal byte accounting | 97,423 nonzero bytes and 4,096,881 zero bytes |
| Journal JBD2 header accounting | 761 block-aligned JBD2 headers: one type-4 superblock, 263 type-1 descriptor blocks, and 497 type-2 commit blocks |
| Journal sequence population | Historical descriptor sequences `13,256–14,227`; historical commit sequences `13,254–14,228` |
| Journal payload search | No `LDI\0` magic, live filename, MT00 filename, MOLY release string, or live MTFW timestamp identified |
| Journal semantic text | One historical `/mnt/vendor/protect_s` occurrence; remaining printable sequences are filesystem-metadata or binary coincidences |
| Root directory | Inode 2; mode `0770`; UID/GID `1000:1000`; size 4,096 bytes; block 91, `0x0005B000–0x0005BFFF` |
| Root entries | `lost+found` and `md` |
| Root security attributes | `security.selinux="u:object_r:protect_s_data_file:s0\0"`; `security.sehash=ab fc 6e 39 84 10 c7 a7 7c fd 65 55 58 28 00 83 b9 45 6d b9` |
| Root xattr block | Block 1122; physical extent `0x00462000–0x00462FFF`; reference count 2 |
| `/lost+found` | Inode 11; directory; mode `0700`; UID/GID `0:0`; size 16,384 bytes; blocks `92–95`, `0x0005C000–0x0005FFFF`; no allocated child entries |
| `/lost+found` security attributes | `security.selinux="u:object_r:protect_s_data_file:s0\0"`; `security.sehash=ab fc 6e 39 84 10 c7 a7 7c fd 65 55 58 28 00 83 b9 45 6d b9` |
| `/lost+found` xattr relationship | Shares xattr block 1122 with root; on-disk reference count 2 exactly accounts for both owners |
| `/md` | Inode 12; directory; mode `0770`; UID/GID `1001:1001`; size 4,096 bytes; block 1123, `0x00463000–0x00463FFF` |
| `/md` entries | `LD40_001`, `ST33_004`, `ST34_001`, `NA77_005`, and `MTFW_000` |
| Live-file ownership | All five files have mode `0660`, UID/GID `1001:1001`, and one link |
| Shared security attribute | `/md` and all five files carry `security.selinux="u:object_r:protect_s_data_file:s0\0"` |
| Shared xattr block | Block 1121; physical extent `0x00461000–0x00461FFF`; reference count 6 exactly accounts for `/md` and the five files |
| Deleted inode scan | NONE; `lsdel` reported zero deleted inodes |
| Directory-slack scan | Root, all four `lost+found` blocks, and `/md` contain no nonzero slack bytes, deleted directory entry, or unallocated pathname |
| Common live-file envelope | Every live file begins with a 64-byte header containing magic `LDI\0` |
| LDI count/identifier field | Little-endian word at header offset `0x04`; upper 16 bits equal the stored-record count and lower 16 bits identify the LID |
| LDI declared-record field | Little-endian 32-bit value at header offset `0x08`; matches the empirically resolved logical record size |
| Stored-record geometry | `(file size − 64) ÷ record count` resolves the physical stride exactly for all five files |
| Remaining LDI-header fields | `OPEN_BOUNDARY`; raw bytes are retained without unsupported assignments |
| LDI identifier semantics | Proprietary per-LID functional assignments remain `OPEN_BOUNDARY`; filenames and identifiers are preserved without unsupported schema labels |
| Live logical-file bytes | 32,474 bytes total |
| Live logical-file nonzero bytes | 11,035 |
| Live logical-file zero bytes | 21,439 |
| Live file-data allocation | 11 filesystem blocks; 45,056 allocated bytes |
| Aggregate live extent slack | 12,582 bytes; independently verified completely zero |
| Raw-extent reconciliation | Every extracted logical file compared byte-for-byte equal to the data reconstructed directly from its physical filesystem extents |
| `/md/LD40_001` | Inode 13; 19,264 bytes; logical block 0 at physical block 1536 and logical blocks `1–4` at physical blocks `2048–2051`; 1,715 nonzero bytes; entropy 0.665868471 bits/byte |
| `LD40_001` physical extents | `0x00600000–0x00600FFF` and `0x00800000–0x00803FFF` |
| `LD40_001` LDI geometry | LID `0xEF09`; 100 records; declared size 184 bytes; stored stride 192 bytes; eight extra bytes per record |
| `LD40_001` record diversity | All 100 complete 192-byte records are byte-identical |
| `LD40_001` exact record structure | Byte 0 `FE`; bytes `1–175` zero; bytes `176–183` eight `FF` bytes; bytes `184–191` trailer `e3 dd 49 49 a7 44 c3 17` |
| `LD40_001` record byte accounting | 17 nonzero bytes and 175 zero bytes per stored record |
| `LD40_001` content conclusion | Not byte-empty: it contains 100 structurally populated low-information records; whether the values represent defaults, unset calibration, or another state remains `OPEN_BOUNDARY` |
| `LD40_001` final extent slack | `0x00803B40–0x00803FFF`; 1,216 bytes; all zero |
| `/md/ST33_004` | Inode 14; 2,784 bytes; block 2052, `0x00804000–0x00804FFF`; 2,706 nonzero bytes; entropy 7.275104893 bits/byte |
| `ST33_004` LDI geometry | LID `0xEF11`; ten records; declared size 260 bytes; stored stride 272 bytes; 12 extra bytes per record |
| `ST33_004` record diversity | All ten complete 272-byte stored records are byte-identical |
| `ST33_004` record composition | Each record contains 269 nonzero bytes, three zero bytes, and 176 distinct byte values |
| `ST33_004` record boundaries | First record at file-relative `0x0040–0x014F`; subsequent records begin every `0x0110` bytes through record 9 at `0x09D0` |
| `ST33_004` record anchors | First 16 bytes `64 54 56 59 ca eb ca f9 7d e0 b4 b7 dd 4b b1 98`; final 16 bytes `fc 7e 58 8f b7 04 fd 5c 76 d9 ae 3b b1 76 49 6b` |
| `ST33_004` interpretation boundary | High byte diversity does not independently establish calibration validity, encryption, or measurement provenance; specific field semantics remain `OPEN_BOUNDARY` |
| `ST33_004` extent slack | `0x00804AE0–0x00804FFF`; 1,312 bytes; all zero |
| `/md/ST34_001` | Inode 15; 832 bytes; block 1537, `0x00601000–0x00601FFF`; 742 nonzero bytes; entropy 5.104078766 bits/byte |
| `ST34_001` LDI geometry | LID `0xEF12`; two records; declared size 362 bytes; stored stride 384 bytes; 22 extra bytes per record |
| `ST34_001` record diversity | The two complete 384-byte records are byte-identical |
| `ST34_001` record boundaries | File-relative `0x0040–0x01BF` and `0x01C0–0x033F` |
| `ST34_001` record composition | Each record contains 363 nonzero bytes, 21 zero bytes, 81 distinct byte values, and 24 consecutive 16-byte units |
| `ST34_001` dominant unit | `23 cd 4a 2b c6 e8 ce 46 16 49 fa d4 00 30 75 70`; occurs 19 times per record |
| `ST34_001` remaining units | Five additional 16-byte values occur once each: `3c42db552233c4e4f6f9562f83a44f7f`, `c5fbb408c2e5d82d2c2471d1d5124734`, `d0d2db8a0035c480bff5a9d46e3c65b7`, `dd868c437e09ffcd2600874a1448b282`, and `e60a185ce6135465f84fa4f60e7d8613` |
| `ST34_001` interpretation boundary | Unit meanings, stored-record overhead, and trailer construction remain `OPEN_BOUNDARY` |
| `ST34_001` extent slack | `0x00601340–0x00601FFF`; 3,264 bytes; all zero |
| `/md/NA77_005` | Inode 16; 9,488 bytes; blocks `2053–2055`, `0x00805000–0x00807FFF`; 5,833 nonzero bytes; entropy 1.684880644 bits/byte |
| `NA77_005` LDI geometry | LID `0x0480`; two records; declared size 4,704 bytes; stored stride 4,712 bytes; eight extra bytes per record |
| `NA77_005` record boundaries | File-relative `0x0040–0x12A7` and `0x12A8–0x250F` |
| `NA77_005` record diversity | Two byte-distinct 4,712-byte stored records |
| `NA77_005` record 0 | 3,120 nonzero and 1,592 zero bytes; 56 distinct byte values; dominant values `FF:2487`, `00:1592`, `0F:240`, `01:59`, `03:30`; trailer `02 a3 95 ac 72 c7 6e 0c` |
| `NA77_005` record 1 | 2,695 nonzero and 2,017 zero bytes; 26 distinct byte values; dominant values `FF:2464`, `00:2017`, `0F:156`, `07:25`, `3C:7`, `B4:7`; trailer `d9 0b 32 b3 be 33 4a ec` |
| `NA77_005` interpretation boundary | Exact table bytes and distributions are preserved; proprietary field assignments, value meanings, and validity state remain `OPEN_BOUNDARY` |
| `NA77_005` final extent slack | `0x00807510–0x00807FFF`; 2,800 bytes; all zero |
| `/md/MTFW_000` | Inode 17; 106 bytes; block 2056, `0x00808000–0x00808FFF`; 39 nonzero bytes; entropy 2.600114877 bits/byte |
| `MTFW_000` LDI geometry | LID `0xF202`; one record; declared size 34 bytes; stored stride 42 bytes; eight extra bytes |
| `MTFW_000` content | Payload `2021/01/20 17:26`; remaining declared payload bytes zero; trailer `bb 1c 66 e6 32 c1 f8 5d` |
| `MTFW_000` extent slack | `0x0080806A–0x00808FFF`; 3,990 bytes; all zero |
| Total live LDI-envelope count | Five |
| Live LDI offsets | `0x00600000`, `0x00601000`, `0x00804000`, `0x00805000`, and `0x00808000` |
| Unallocated LDI envelopes | NONE |
| Unallocated residual records | NONE |
| Free-space verification method | Complete block-bitmap-driven scan plus three independent extent comparisons against `/dev/zero` |
| Free-space extent 1 | Blocks `1124–1535`; `0x00464000–0x005FFFFF`; 1,687,552 bytes; completely zero |
| Free-space extent 2 | Blocks `1538–2047`; `0x00602000–0x007FFFFF`; 2,088,960 bytes; completely zero |
| Free-space extent 3 | Blocks `2057–2781`; `0x00809000–0x00ADDFFF`; 2,969,600 bytes; completely zero |
| Completely zero free blocks | 1,647 of 1,647 |
| Completely zero free-block volume | 6,746,112 bytes |
| Free-space nonzero-byte count | 0 |
| Recoverable deleted content | NONE; no deleted inode, pathname, nonzero free block, or journal-resident LDI payload identified |
| ASCII strings | 508 occurrences at minimum length 4; 136 unique strings; length range 4–33 bytes |
| Meaningful ASCII | `/mnt/vendor/protect_s`; `lost+found`; the five live filenames; `security.selinux`; `security.sehash`; `u:object_r:protect_s_data_file:s0`; timestamp `2021/01/20 17:26` |
| ASCII interpretation | Remaining printable sequences are journal/filesystem metadata or binary coincidences inside opaque LDI records |
| UTF-16LE strings | NONE at minimum length 4 |
| Initial zero area | `0x00000000–0x000003FF`; 1,024 bytes |
| First nonzero byte | `0x00000400` |
| Partition nonzero-byte count | 116,828 |
| Partition zero-byte count | 11,278,244 |
| Distinct byte values | 256 |
| Last nonzero byte | `0x00808069`; final byte of the live `/md/MTFW_000` trailer |
| Terminal zero-filled area | `0x0080806A–0x00ADDFFF`; 2,973,590 bytes |
| Embedded-signature scan | Outer ext4 filesystem plus five internal `LDI` envelopes; no ELF, ZIP, gzip, PNG, SQLite, Android boot-image, AVB, FDT, DTBO-table, or Android sparse-image signature identified |
| Historical ext-magic occurrence | A second `0xEF53` occurrence at `0x00066438` belongs to a journaled historical superblock image and does not establish a nested filesystem |
| Content classification | `EXT4_PROTECT_S_WITH_FIVE_LIVE_MODEM_NVRAM_LDI_RECORDS_AND_ZERO_FILLED_UNALLOCATED_SPACE` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; mutable `protect_s` state acquired after bootloader unlock and Magisk-root research-state changes |
| Open boundaries | Proprietary LID assignments, remaining LDI-header fields, per-record overhead meanings, trailer algorithms, record-value semantics, calibration validity, and operational use remain unresolved; all physical structures and byte extents are bounded without unsupported semantic assignments |

## p14 — `seccfg`
| p14 | `seccfg` | 8,388,608 | Fixed-layout binary header (`MMMM`/`EEEE` magic markers, five LE32 fields, one 32-byte high-entropy field) at offset 0; isolated 8-byte record at offset `0x200`; no recognized filesystem, container, or embedded signature; solid 65,024-byte `0xFF` fill block; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `1452b719457a466d689cb3bf43acd9689b3b25a65229ba6e3a4b01dfefe9a381` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/seccfg.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p14 seccfg` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p14; `seccfg` |
| Physical block device | `/dev/block/mmcblk0p14`; major:minor `179:14` |
| By-name mapping | `/dev/block/by-name/seccfg` -> `/dev/block/mmcblk0p14` |
| GPT start LBA | 491,520 |
| GPT end LBA | 507,903 inclusive |
| GPT sector count | 16,384 |
| GPT unique partition GUID | `7792210b-b6a8-45d5-ad91-3361ed14c608` |
| Object size | 8,388,608 bytes; `0x00800000`; 8 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned zero entries |
| Binary content extent | `0x000000–0x01FFFF`; 131,072 bytes (128 KiB) |
| Terminal zero-filled region | `0x020000–0x7FFFFF`; 8,257,536 bytes; independently verified byte-for-byte zero |
| Header block extent | `0x000000–0x00003B`; 60 bytes |
| Header leading magic | `0x000000–0x000003`; ASCII `MMMM`; raw bytes `4D 4D 4D 4D` |
| Header field @0x004 (LE32) | `0x00000004` (4) |
| Header field @0x008 (LE32) | `0x0000003C` (60); numerically equal to the exact byte length of the populated header block `0x000000–0x00003B` |
| Header field @0x00C (LE32) | `0x00000003` (3) |
| Header field @0x010 (LE32) | `0x00000001` (1) |
| Header field @0x014 (LE32) | `0x00000000` (0) |
| Header secondary magic | `0x000018–0x00001B`; ASCII `EEEE`; raw bytes `45 45 45 45` |
| Header trailing field | `0x00001C–0x00003B`; 32 bytes; raw bytes `0B C0 B2 52 37 D6 06 FC 7D 78 6D F2 BD 47 F4 28 4D 19 F6 9D 8A 78 51 1B CC C1 60 98 48 5A 3D A9` |
| Header trailing field entropy | 4.9375 bits/byte; 31 of 32 bytes distinct |
| Header trailing field hash-candidacy test | Tested against SHA256 and SHA1 of six candidate preceding-byte ranges (`0x00–0x17`, `0x00–0x1B`, `0x00–0x1F`, `0x04–0x17`, `0x00–0x3B`, `0x00–0x207`); zero matches — not a hash of any tested preceding span |
| Header-to-secondary-record gap | `0x00003C–0x0001FF`; 452 bytes; independently verified byte-for-byte zero |
| Secondary record extent | `0x000200–0x000207`; 8 bytes |
| Secondary record raw bytes | `90 79 09 22 00 00 00 01` |
| Secondary record LE32 @0x200 | `0x22097990` (571,046,288) |
| Secondary record LE32 @0x204 | `0x01000000` (16,777,216) |
| Secondary-record-to-fill-block gap | `0x000208–0x0101FF`; 65,528 bytes; independently verified byte-for-byte zero |
| Fill-block extent | `0x010200–0x01FFFF`; 65,024 bytes |
| Fill-block content | Solid single-byte fill, value `0xFF`; independently verified byte-for-byte against a generated equal-length `0xFF` buffer; zero-byte count within block: 0 |
| Fill-block entropy | 0.000000 bits/byte (1 distinct byte value) |
| Full-partition nonzero-byte count | 65,073 |
| Full-partition zero-byte count | 8,323,535 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x01FFFF` |
| Byte-accounting closure | 60 + 452 + 8 + 65,528 + 65,024 + 8,257,536 = 8,388,608; exact, no unaccounted bytes |
| ASCII strings (min length 4) | Two: `MMMM` at offset `0x000000`; `EEEE` at offset `0x000018` |
| UTF-16LE strings (min length 4) | NONE |
| Semantic text | NONE beyond the two four-byte magic markers |
| Internal field semantics (header fields, secondary record, 32-byte field, fill-block provenance) | `OPEN_BOUNDARY` |
| Existing database cross-reference | Section 2.6 currently lists `p14 seccfg` at summary depth only ("security configuration partition"); Section 2.7 lists fstab alias `/seccfg`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SECCFG_FIXED_HEADER_WITH_ISOLATED_SECONDARY_RECORD_AND_SOLID_0xFF_FILL_BLOCK` |

## p15 — `sec1`
| p15 | `sec1` | 2,097,152 | Completely zero-filled raw partition image containing no filesystem, container, header, allocation structure, text, cryptographic material, marker, or residual payload | `CURRENT_RESEARCH_CAPTURE`; factory-relative content state, original provisioning reason, and intended security-domain use `OPEN_BOUNDARY` | `CLOSED` | `5647f05ec18958947d32874eeb788fa396a05d0bab7c1b71f112ceb7e9b31eee` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/sec1.bin` |
| GPT partition | p15; `sec1` |
| Physical block device | `/dev/block/mmcblk0p15` |
| By-name mapping | `/dev/block/by-name/sec1` → `/dev/block/mmcblk0p15` |
| Major:minor | `179:15` |
| Device-node mode and ownership | `brw-------`; `root:root` |
| Device-node SELinux context | `u:object_r:sec1_block_device:s0` |
| GPT start LBA | 507,904 |
| GPT end LBA | 511,999 inclusive |
| GPT sector count | 4,096 sectors |
| GPT unique partition GUID | `138a6db9-1032-451d-91e9-0fa38ff94fbb` |
| Object size | 2,097,152 bytes; `0x00200000`; 2 MiB |
| Evidence-object extent | `0x00000000–0x001FFFFF` |
| Size reconciliation | 4,096 × 512-byte GPT sectors = 2,097,152 bytes; exact |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| `file -k` classification | `data` |
| `blkid` signature | NONE |
| ext2/ext3/ext4 probe | No valid superblock or ext-family magic; the object is not classified as a damaged ext filesystem |
| Internal partition table | NONE |
| Allocation structures | NONE |
| Directory or inode structures | NONE |
| Journal | NONE |
| Extended attributes | NONE |
| Distinct byte values | One: `0x00` |
| Nonzero-byte count | 0 |
| Zero-byte count | 2,097,152 |
| Zero-filled extent | Entire evidence object, `0x00000000–0x001FFFFF` |
| First nonzero byte | NONE |
| Last nonzero byte | NONE |
| Initial zero area | Entire evidence object |
| Terminal zero area | Entire evidence object |
| Shannon entropy | `0.000000000` bits per byte |
| 512-byte sector accounting | 4,096 total sectors; 4,096 completely zero sectors; 0 nonzero sectors |
| 4,096-byte block accounting | 512 total blocks; 512 completely zero blocks; 0 nonzero blocks |
| 524,288-byte segment accounting | Four total segments; four completely zero segments; 0 nonzero segments |
| 1,048,576-byte region accounting | Two total regions; two completely zero regions; 0 nonzero regions |
| Independent segmented verification | Four separately compared 524,288-byte ranges were all byte-for-byte identical to `/dev/zero` |
| Independent whole-object verification | All 2,097,152 bytes compared byte-for-byte identical to `/dev/zero`; zero differences |
| Independent byte-removal verification | Removing every `0x00` byte left 0 bytes |
| ASCII strings | NONE at minimum length 4 |
| UTF-16LE strings | NONE at minimum length 4 |
| Embedded-signature scan | No ext-family magic, ELF, ZIP, gzip, PNG, SQLite, Android boot-image, AVB header, AVB footer, FDT, DTBO-table, Android sparse-image, or `LDI` signature |
| Structured payload | NONE |
| Cryptographic or key material | NONE present in this capture |
| Persistent state | NONE encoded |
| Recoverable deleted content | NONE |
| Residual-data classification | NONE; no nonzero residual bytes exist |
| Naming limitation | The GPT label `sec1` identifies the physical partition but does not establish populated security content, an active consumer, or internal semantics |
| Original provisioning reason | `OPEN_BOUNDARY`; the all-zero image contains no evidence establishing why the GPT entry was created or retained |
| Intended security-domain function | `OPEN_BOUNDARY`; no payload, header, marker, key, state field, or allocation structure exists from which a specific function can be resolved |
| Factory-relative content state | `OPEN_BOUNDARY`; the capture proves complete zero-fill at acquisition but does not establish whether the partition was always empty, deliberately erased, or reserved for conditional provisioning |
| Content classification | `FULLY_ZERO_FILLED_RAW_SECURITY_NAMED_PARTITION_IMAGE` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` |
| Open boundaries | Original provisioning reason, intended security-domain consumer, factory-relative history, and whether the partition was reserved or deliberately cleared remain unresolved; the complete captured byte state is closed |

## p16 — `proinfo`
| p16 | `proinfo` | 3,145,728 | Sparse production-information partition containing one 24-byte ASCII string (`MTK000250730160840000565`) at offset 0, an 8-byte hex field at `0x40`, a 12-byte hex field at `0x68`, three isolated single-byte flags (`0xAA`, `0xB0`, `0xB2`, each value `0x01`), and one isolated terminal byte (`0x142` = `0x1D`); no recognized filesystem, container, or embedded signature; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `d34d3939155d3db8f994e8c68b0fbefd86f7f847ae4b0b4b36691d0633403de3` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/proinfo.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p16 proinfo` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p16; `proinfo` |
| Physical block device | `/dev/block/mmcblk0p16`; major:minor `179:16` |
| By-name mapping | `/dev/block/by-name/proinfo` -> `/dev/block/mmcblk0p16` |
| GPT start LBA | 512,000 |
| GPT end LBA | 518,143 inclusive |
| GPT sector count | 6,144 |
| GPT unique partition GUID | `756d934c-50e3-4c91-af46-02d824169ca7` |
| Object size | 3,145,728 bytes; `0x00300000`; 3 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned zero entries |
| Full-partition nonzero-byte count | 48 |
| Full-partition zero-byte count | 3,145,680 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x000142` |
| Populated content extent | `0x000000–0x000142`; entire remainder of the partition is zero-filled |
| ASCII string field | `0x000000–0x000017`; 24 bytes; ASCII `MTK000250730160840000565` |
| Gap 1 | `0x000018–0x00003F`; 40 bytes; independently verified byte-for-byte zero |
| Hex field 1 | `0x000040–0x000047`; 8 bytes; raw bytes `68 10 32 94 11 81 99 F7` |
| Hex field 1 decode | LE32@0x40 `0x94321068`; LE32@0x44 `0xF7998111`; LE64@0x40 `0xF799811194321068`; BE64@0x40 `0x68103294118199F7` |
| Gap 2 | `0x000048–0x000067`; 32 bytes; independently verified byte-for-byte zero |
| Hex field 2 | `0x000068–0x000073`; 12 bytes; raw bytes `12 34 56 A0 19 99 20 72 0D 01 99 9E` |
| Hex field 2 decode | LE32@0x68 `0xA0563412`; LE32@0x6C `0x72209919`; LE32@0x70 `0x9E99010D` |
| Gap 3 | `0x000074–0x0000A9`; 54 bytes; independently verified byte-for-byte zero |
| Isolated flag byte 1 | `0x0000AA`; value `0x01` |
| Gap 4 | `0x0000AB–0x0000AF`; 5 bytes; independently verified byte-for-byte zero |
| Isolated flag byte 2 | `0x0000B0`; value `0x01` |
| Isolated zero byte | `0x0000B1`; value `0x00` |
| Isolated flag byte 3 | `0x0000B2`; value `0x01` |
| Gap 5 | `0x0000B3–0x000141`; 143 bytes; independently verified byte-for-byte zero |
| Isolated terminal byte | `0x000142`; value `0x1D` (29) |
| Terminal zero-filled region | `0x000143–0x2FFFFF`; 3,145,405 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 24 + 40 + 8 + 32 + 12 + 54 + 1 + 5 + 1 + 1 + 1 + 143 + 1 + 3,145,405 = 3,145,728; exact, no unaccounted bytes |
| ASCII strings (min length 4) | One: `MTK000250730160840000565` at offset `0x000000` |
| UTF-16LE strings (min length 4) | NONE |
| Semantic text | The single ASCII string only; no other semantic text structure |
| Nonzero-byte entropy (all 48 populated bytes) | 4.403107 bits/byte |
| Internal field semantics (hex field 1, hex field 2, all three flag bytes, terminal byte `0x1D`) | `OPEN_BOUNDARY` |
| Existing database cross-reference | Section 2.6 currently lists `p16 proinfo` at summary depth only ("production information partition"); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/proinfo`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_PRODUCTION_INFO_PARTITION_WITH_SERIAL_STRING_AND_SPARSE_BINARY_FIELDS` |

## p17 — `md1img`
| p17 | `md1img` | 104,857,600 | MTK modem (MD1) multi-image container: content-populated region `0x000000–0x303144B` (50,533,452 bytes, ~48.2 MiB) containing two `MMM`/`FILE_INFO`-tagged sub-image headers (Primary-MAUI, declared length 19,031,168 bytes; and a second untagged/unsigned sub-image, declared length 6,914,048 bytes), six embedded X.509v3 DER certificates, two LZMA-compressed streams, one gzip-compressed stream, a genuine embedded AES S-box, two genuine embedded SHA-256 constant tables, a genuine Base64 alphabet table, and a genuine perfect-square lookup table; remainder of the 100 MiB partition (`0x303144C–0x63FFFFF`, 54,324,148 bytes) is zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `e78c1691a36ff9e08a335822645e9b801e14f4c4e8d9cf6ce383d8060bb170b6` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/md1img.bin` |
| Transfer completeness note | Uploaded object size confirmed 104,857,600 bytes on arrival in this sandbox — exact match to declared GPT/Section 2.5 size; no truncation observed in this session |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p17 md1img` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p17; `md1img` |
| Physical block device | `/dev/block/mmcblk0p17`; major:minor `179:17` |
| Device-node mode / ownership (from prior project record) | `brw-r-----` / `root:system` |
| By-name mapping | `/dev/block/by-name/md1img` -> `/dev/block/mmcblk0p17` |
| GPT start LBA | 518,144 |
| GPT end LBA | 722,943 inclusive |
| GPT sector count | 204,800 |
| GPT unique partition GUID | `a3f3c267-5521-42dd-a724-3bdec20c7c6f` |
| Object size | 104,857,600 bytes; `0x06400000`; 100 MiB |
| Recognized filesystem | NONE |
| Recognized container (outer) | NONE; multiple internal recognized structures (see below) |
| Full-partition nonzero-byte count | 45,657,051 |
| Full-partition zero-byte count | 59,200,549 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x0303144B` |
| Content-populated region | `0x000000–0x0303144B`; 50,533,452 bytes |
| Terminal zero-filled region | `0x0303144C–0x063FFFFF`; 54,324,148 bytes; independently verified byte-for-byte zero (whole-region numpy comparison, zero differences) |
| Byte-accounting closure | 50,533,452 + 54,324,148 = 104,857,600; exact, no unaccounted bytes |
| Primary sub-image header magic | `0x000600–0x000603`; raw bytes `4D 4D 4D 01` (ASCII `MMM` + type byte `0x01`) |
| Primary sub-image header size field (LE32 @+0x04) | `0x00000038` (56) |
| Primary sub-image identify string | `0x000608–0x000610`; ASCII `FILE_INFO` + null |
| Primary sub-image binwalk-parsed fields | File Type: Primary-MAUI; Flash Type: NOR; Signature Type: SINGLE; Load Address `0x400` (independently confirmed at raw offset `0x01A`, LE32 value `0x00000400`); File Length 19,031,168 bytes (independently confirmed at raw offset `0x01E`, LE32 value `0x01226480`); Maximum Size `-1` (`0xFFFFFFFF`); Content Offset `0x430`; Signature Length 256; Jump Offset 1072; flags `POST_BUILD_DONE`, `XIP` |
| Second sub-header magic (chained) | `0x000636–0x000639`; raw bytes `4D 4D 4D 02` (type byte `0x02`), immediately following the first sub-header's declared 56-byte extent |
| Embedded build-identifier string | `0x000640–0x00067F`; ASCII `TK_MD_BASIC_MDBIN_PCB01_MT6765_S00.MOLY_LR12A_R3_MP_V107_4.bin` |
| Embedded release-tag string | `0x0006C0–0x0006D6`; ASCII `MOLY.LR12A.R3.MP.V107.4` |
| Release-tag cross-reference | Matches the `MOLY.LR12A.R3.MP.V107.4` / `LWTG_6177M_R3_6762` modem release strings already recorded in this project's `protect1`/`protect2` (`/md`) closed manifest records — same modem release identified from an independent partition |
| Early identifier string | `0x000008–0x00000D` (approx.); ASCII `md1rom` |
| Second sub-image header magic | `0x1229A80–0x1229A83`; raw bytes `4D 4D 4D 01` (ASCII `MMM` + type byte `0x01`) |
| Second sub-image identify string | `0x1229A88–0x1229A90`; ASCII `FILE_INFO` + null |
| Second sub-image binwalk-parsed fields | Flash Type: NONE; No Signature; Load Address `-1`; File Length 6,914,048 bytes; Maximum Size `-1`; Content Offset `0x118`; Signature Length 0; Jump Offset 0; flag `POST_BUILD_DONE` |
| Second sub-image declared payload span (approximate) | `0x1229B98–0x18C1B97` (header start `0x1229A80` + content offset `0x118`, extent 6,914,048 bytes); the next certificate anchor (`cert5` at `0x18C1DA0`) begins 520 bytes after this declared span ends — close but not exact; precise reconciliation of the 520-byte gap `OPEN_BOUNDARY` |
| Certificate 1 | `0x1226F70`; Binwalk-recognized X.509v3, header length 4 + sequence length 1,749 = 1,753 bytes; DER SEQUENCE tag confirmed (`30 82 06 D5 30 82 05 84`) |
| Certificate 2 | `0x1227850`; header length 4 + sequence length 953 = 957 bytes; DER SEQUENCE tag confirmed (`30 82 03 B9 30 82 02 68`) |
| Certificate 3 | `0x1228C10`; header length 4 + sequence length 1,705 = 1,709 bytes; DER SEQUENCE tag confirmed (`30 82 06 A9 30 82 05 58`) |
| Certificate 4 | `0x12294C0`; header length 4 + sequence length 953 = 957 bytes; DER SEQUENCE tag confirmed, identical leading 8 bytes to Certificate 2 but NOT byte-identical across the full 957-byte span |
| Certificate 5 | `0x18C1DA0`; header length 4 + sequence length 1,705 = 1,709 bytes; DER SEQUENCE tag confirmed; full 1,709-byte span byte-for-byte identical to Certificate 3 |
| Certificate 6 | `0x18C2650`; header length 4 + sequence length 953 = 957 bytes; DER SEQUENCE tag confirmed, identical leading 8 bytes to Certificates 2 and 4 but NOT byte-identical to either across the full span |
| Certificate identity summary | 6 DER certificates total across two clusters (4 near `0x122xxxx`, 2 near `0x18Cxxxx`); Certificate 3 and Certificate 5 are proven byte-identical; Certificates 2, 4, and 6 share identical ASN.1 length headers (same certificate type/size class) but differ in full content; Certificate 1 is unique (SHA256 `23f3ea71de3b54123e576978d544adcd031e1e0650d214ad987944b0c4405b0d`); full ASN.1 field decode and signature validation `OPEN_BOUNDARY` for all six |
| LZMA stream 1 | `0x18D2380`; properties byte `0x5D` confirmed; dictionary size 16,777,216 bytes; declared uncompressed size 3,920,926 bytes; LZMA magic/header bytes independently confirmed (`5D 00 00 00 01 1E D4 3B 00 00 00 00 00`) |
| LZMA stream 2 | `0x19CEE10`; properties byte `0x5D` confirmed; dictionary size 16,777,216 bytes; declared uncompressed size 3,219,136 bytes; header bytes independently confirmed (`5D 00 00 00 01 C0 1E 31 00 00 00 00 00`) |
| gzip stream | `0x1AC2590`; magic independently confirmed (`1F 8B 08 00 00 00 00 00 00 03`); Unix origin, null modification timestamp |
| Compressed-stream payload content | Not decompressed this pass; likely DSP coefficient tables, NVRAM defaults, or auxiliary modem data given placement after the two `FILE_INFO`-tagged sub-images and preceding the densest region of the file; exact content `OPEN_BOUNDARY` |
| Content-region density map (1 MiB granularity) | `0x000000–0x0FFFFFF`: 92–96% nonzero throughout (dense code/data, includes Primary-MAUI header and declared payload); `0x1000000–0x18FFFFF`: fluctuating 39–73% nonzero (includes second `FILE_INFO` header, all six certificates); `0x1900000–0x28FFFFF` (16 MiB): 99% nonzero (includes both LZMA streams and gzip stream); `0x2900000–0x2FFFFFF` (7 MiB): 100% nonzero, no zero bytes at all; `0x3000000–0x30FFFFF`: 19% nonzero (tail of content before zero-fill) |
| Confirmed genuine SHA-256 round-constant table (copy 1) | `0x105DAB0`; first six LE32 words exactly match the canonical SHA-256 constants `K[0]-K[5]` (`0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b, 0x59f111f1`); mathematically verified byte-for-byte against the published constant sequence; Binwalk's raw label ("SHA256 hash constants") was structurally correct — this is a genuine embedded cryptographic constant table, not noise |
| Confirmed genuine SHA-256 round-constant table (copy 2) | `0x10A6C8C`; full 256-byte (64-word) table verified exact match to the same canonical SHA-256 constants |
| Confirmed genuine SHA-256 round-constant table (copy 3) | `0x10A6DAC`; full 256-byte table byte-for-byte identical to the copy at `0x10A6C8C` — the complete constant table is embedded at least twice more beyond the first instance, consistent with two separate compiled crypto-library instances |
| Confirmed genuine AES S-box | `0x1140C94`; first 16 bytes exact match to the canonical Rijndael/AES substitution box (`63 7C 77 7B F2 6B 6F C5 30 01 67 2B FE D7 AB 76...`); Binwalk's raw label ("AES S-Box") was structurally correct — this is a genuine embedded AES implementation constant, not noise |
| Confirmed genuine Base64 standard alphabet table | `0x105D3DC`; exact 64-byte match to the standard Base64 alphabet `A-Za-z0-9+/`; Binwalk's raw label ("Base64 standard index table") was structurally correct |
| Confirmed genuine perfect-square lookup table | `0x1169D04`; exact byte match to `i²` for `i=0..15` as single bytes (`00 01 04 09 10 19 24 31 40 51 64 79 90 A9 C4 E1` = 0,1,4,9,16,25,36,49,64,81,100,121,144,169,196,225); purpose `OPEN_BOUNDARY` (possible fast-square/CRC/signal-processing lookup use); located 4 bytes after Binwalk's reported "Nagra Constant_KEY" offset, which was not itself confirmed as a real cipher-key structure |
| Genuine RF-calibration flag strings (Binwalk mislabeled as Adobe Flash SWF) | `0x108E994`; real ASCII content: `EL1D_RX_FWS_EN`, `EL1D_RX_TBMIMO_FORCE_EN`, `EL1D_RX_FIX_AFC_EN`; no SWF header structure present — Binwalk's container label was a false match on incidental header bytes, but the underlying text is genuine and modem-relevant |
| Genuine DVFS power-management strings (Binwalk mislabeled as CSYS header) | `0x1111164`; real ASCII content: `..._PWR_DMCSYS`, `ForceALLPWR`, `DVFS_MCUPLL_HIGHEST`, `DVFS_TXPLL_HIGHEST`; no CSYS container structure present — Binwalk's label was a false match on the substring `CSYS` within `DMCSYS`, but the underlying text is genuine and modem-relevant |
| Structured table, unresolved purpose (Binwalk mislabeled as MPEG transport stream) | `0x3D6F7A`; entropy 5.949 bits/byte over a 400-byte sample (lower than surrounding code); confirmed repeating 6-byte record pattern with an incrementing index byte (`...a3 46 c0 00 f3 / ...a3 47 c0 00 f3 / ...a3 48 c0 00 f3...`), transitioning to a different byte pattern after several repetitions; not an actual MPEG-TS stream (no valid 188-byte-interval sync structure); real structured table, semantic purpose `OPEN_BOUNDARY` |
| Structured table, unresolved purpose (Binwalk mislabeled as ARJ archive) | `0x109C484`; confirmed repeating small-integer sequence (`0,1,2,2,2,3,3,3,4,4,4,4,4,5,6,7,7,7...`, repeating with variation); consistent with a quantization/code-length table used in PHY-layer channel coding; not an ARJ archive; semantic purpose `OPEN_BOUNDARY` |
| Sparse filler table, unresolved purpose (Binwalk mislabeled as MySQL ISAM) | `0x10AB399`; confirmed 402 of 512 sampled bytes (78%) equal filler value `0xFE`, with sparse `0x02`/`0x03`/`0x06`/`0xFF` markers interspersed; not a MySQL ISAM file; semantic purpose `OPEN_BOUNDARY` |
| Sparse marker table, unresolved purpose (Binwalk mislabeled as VxWorks symbol table) | `0x113F368`; confirmed 387 of 512 sampled bytes (76%) equal zero, with 54 occurrences of marker byte `0x05` at roughly regular intervals; no symbol-name strings, addresses, or type bytes present — not an actual VxWorks symbol table; semantic purpose `OPEN_BOUNDARY` |
| Dense unresolved regions, individually checked (Binwalk labeled as mcrypt encrypted data, ×5) | `0x1DD2F4`, `0xA4FF3E`, `0xD103BE`, `0xDE9BC8`, `0xF0D144`; measured Shannon entropy 6.66–7.00 bits/byte across 2,048-byte samples at each offset; statistically consistent with a confirmed dense-code reference region sampled elsewhere in this same file (entropy 6.93 bits/byte at `0x2000`); no header, magic, or cryptographic container structure identified at any of the five offsets; consistent with ordinary compiled ARM/DSP code or coefficient data rather than an actual encrypted container; classification is an entropy-based comparison, not a dismissal — each offset was individually inspected and none showed structure beyond dense code/data |
| ASCII strings (min length 6) | 163,142 total; semantic classes observed: `md1rom`/`FILE_INFO` header identifiers, build/release strings, RF-calibration and DVFS flag strings noted above, extensive modem-stack source-path and symbol strings (e.g. `common/driver/devdrv/pmic_wrap/src/pmic_wrap_v2.c`, `common/service/flc2_v2/...`), IMS/SMS/RRC configuration-key strings (`video_over_ps_support`, `sdm_profile_enable_sms_over_ims`, etc.), and thousands of incidental byte-range coincidences inside dense compiled/compressed data; not individually enumerated at this scale |
| UTF-16LE strings (min length 6) | 785 total; not individually characterized this pass; likely incidental given the ASCII-dominant nature of the surrounding content |
| Internal field semantics (GFH/`MMM` sub-header type-byte meanings beyond FILE_INFO, internal flag-byte decode, exact sub-image count and boundaries beyond the two headers located, compressed-stream payload content, DER internal ASN.1 field decode/signature validation, purpose of each unresolved structured table listed above, code/data segmentation within each dense region) | `OPEN_BOUNDARY` |
| Existing database cross-reference | Section 2.6 currently lists `p17 md1img` at summary depth only ("modem firmware image"); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/md1img`, ROM action "NEVER TOUCH in normal ROM bring-up"; this record supersedes the summary-only entry with full structural closure at the depth appropriate to a 100 MiB multi-image container (structural classification with explicit open boundaries, per closure standard — full semantic reverse engineering of the modem stack is not required for closure) |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_MODEM_MD1_MULTI_IMAGE_CONTAINER_WITH_MMM_FILE_INFO_HEADERS_SIX_X509_CERTIFICATES_COMPRESSED_SUBBLOBS_AND_EMBEDDED_CRYPTO_CONSTANT_TABLES` |

## p18 — `spmfw`
| p18 | `spmfw` | 1,048,576 | MTK GFH-style firmware container: 512-byte main GFH header (name `spmfw`); five sequential `2MPS`-tagged PCM (Power Control Microcode) sub-images for LPDDR3/LPDDR4 variants, each with its own 66-byte header and embedded version tag; one `END` marker; two nested GFH-wrapped X.509v3 signing certificates (`cert1`, `cert2`) confirmed by Binwalk DER signature scan; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `0e38b211d1c713460000e632d8b866becbc31492213f90fdb3135313dffb31ac` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/spmfw.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p18 spmfw` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p18; `spmfw` |
| Physical block device | `/dev/block/mmcblk0p18`; major:minor `179:18` |
| By-name mapping | `/dev/block/by-name/spmfw` -> `/dev/block/mmcblk0p18` |
| GPT start LBA | 722,944 |
| GPT end LBA | 724,991 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `8c68cd2a-ccc9-4c5d-8b57-34ae9b2dd481` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE, at outer level; two internal recognized containers (X.509v3 DER certificates) confirmed by Binwalk signature scan at `0x00EDE0` (header length 4, sequence length 1,705) and `0x00F690` (header length 4, sequence length 953) |
| Full-partition nonzero-byte count | 52,014 |
| Full-partition zero-byte count | 996,562 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x00FA4C` |
| Main GFH header extent | `0x000000–0x0001FF`; 512 bytes |
| Main header magic 1 | `0x000000–0x000003`; raw bytes `88 16 88 58` |
| Main header length field (LE32 @0x004) | `0x0000E9D3` (59,859) |
| Main header name field | `0x000008–0x00000F`; ASCII `spmfw` + 3 zero bytes |
| Main header zero gap | `0x000010–0x000027`; 24 bytes; independently verified byte-for-byte zero |
| Main header placeholder | `0x000028–0x00002B`; raw bytes `FF FF FF FF` |
| Main header FF-fill 1 | `0x000028–0x00002F`; 8 bytes; independently verified byte-for-byte `0xFF` |
| Main header magic 2 | `0x000030–0x000033`; raw bytes `89 16 89 58` |
| Main header secondary fields | `0x000034–0x00004F`; raw bytes `00 02 00 00 01 00 00 00 00 00 00 00 00 00 00 00 10 00 00 00 00 00 00 00 00 00 00 00` |
| Main header FF-fill 2 | `0x000050–0x0001FF`; 432 bytes; independently verified byte-for-byte `0xFF` |
| PCM sub-block 0 | Magic `2MPS` @`0x000200`; header `0x000200–0x000241` (66 bytes); length field `11,782` (`0x2E06`); filename `pcm_allinone_lp3_1866.bin`; payload `0x000242–0x003047` (11,782 bytes); embedded version tag `pcm_allinone_lp3_1866_v7.2.4` observed near payload tail (offset `0x003028`) |
| PCM sub-block 1 | Magic `2MPS` @`0x003048`; header `0x003048–0x003089` (66 bytes); length field `11,934` (`0x2E9E`); filename `pcm_allinone_lp4_2400.bin`; payload `0x00308A–0x005F27` (11,934 bytes); embedded version tag `pcm_allinone_lp4_2400_v7.2.4` (offset `0x005F08`) |
| PCM sub-block 2 | Magic `2MPS` @`0x005F28`; header `0x005F28–0x005F69` (66 bytes); length field `11,934` (`0x2E9E`); filename `pcm_allinone_lp4_3200.bin`; payload `0x005F6A–0x008E07` (11,934 bytes); embedded version tag `pcm_allinone_lp4_3200_v7.2.4` (offset `0x008DE8`) |
| PCM sub-block 3 | Magic `2MPS` @`0x008E08`; header `0x008E08–0x008E49` (66 bytes); length field `11,938` (`0x2EA2`); filename `pcm_allinone_lp4_term_3200.bin`; payload `0x008E4A–0x00BCEB` (11,938 bytes); embedded version tag `pcm_allinone_lp4_term_3200_v7.2.4` (offset `0x00BCC8`) |
| PCM sub-block 4 | Magic `2MPS` @`0x00BCEC`; header `0x00BCEC–0x00BD2D` (66 bytes); length field `11,938` (`0x2EA2`); filename `pcm_allinone_lp4_term_2400.bin`; payload `0x00BD2E–0x00EBCF` (11,938 bytes); embedded version tag `pcm_allinone_lp4_term_2400_v7.2.4` (offset `0x00EBAC`) |
| PCM sub-block boundary verification | Each sub-block's declared header(66)+length exactly equals the byte distance to the next sub-block's magic for all five blocks; zero discrepancy |
| PCM filename field structure | 58-byte field per sub-block (`header 66 − magic 4 − length 4 = 58`); null-terminated ASCII, zero-padded remainder |
| END marker | `0x00EBD0–0x00EBD2`; ASCII `END` |
| END-marker zero pad | `0x00EBD3–0x00EBDF`; 13 bytes; independently verified byte-for-byte zero |
| cert1 GFH wrapper extent | `0x00EBE0–0x00F48C`; 2,221 bytes |
| cert1 magic 1 | `0x00EBE0–0x00EBE3`; raw bytes `88 16 88 58` |
| cert1 length field (LE32 @+0x04) | `0x000006AD` (1,709) |
| cert1 name field | `0x00EBE8–0x00EBEF`; ASCII `cert1` + 3 zero bytes |
| cert1 zero gap | `0x00EBF0–0x00EC07`; 24 bytes; independently verified byte-for-byte zero |
| cert1 placeholder | `0x00EC08–0x00EC0B`; raw bytes `78 56 34 12` |
| cert1 FF-fill 1 | `0x00EC0C–0x00EC0F`; 4 bytes; independently verified byte-for-byte `0xFF` |
| cert1 magic 2 | `0x00EC10–0x00EC13`; raw bytes `89 16 89 58` |
| cert1 secondary fields | `0x00EC14–0x00EC2B`; raw bytes `00 02 00 00 01 00 00 00 00 00 00 02 00 00 00 00 10 00 00 00 00 00 00 00` |
| cert1 placeholder 2 | `0x00EC2C–0x00EC2F`; raw bytes `78 56 34 22` |
| cert1 FF-fill 2 | `0x00EC30–0x00EDDF`; 432 bytes; independently verified byte-for-byte `0xFF` |
| cert1 DER payload | `0x00EDE0–0x00F48C`; 1,709 bytes; Binwalk-recognized X.509v3 certificate, header length 4 + sequence length 1,705 = 1,709 exact match to container length field |
| cert1 observed ASCII fields (unparsed) | Two occurrences each of `Some-State1` and `Mediatek0`; validity strings `150604031444Z` and `350604031444Z` (2015-06-04 03:14:44 UTC to 2035-06-04 03:14:44 UTC, 20-year span) consistent with a self-signed issuer/subject pair |
| Gap after cert1 | `0x00F48D–0x00F48F`; 3 bytes; independently verified byte-for-byte zero |
| cert2 GFH wrapper extent | `0x00F490–0x00FA4C`; 1,469 bytes |
| cert2 magic 1 | `0x00F490–0x00F493`; raw bytes `88 16 88 58` |
| cert2 length field (LE32 @+0x04) | `0x000003BD` (957) |
| cert2 name field | `0x00F498–0x00F49F`; ASCII `cert2` + 3 zero bytes |
| cert2 internal structure | Same sub-structure as cert1 (zero gap, placeholder, magic 2, secondary fields, placeholder 2); FF-fill region `0x00F4E0–0x00F68F` (432 bytes) independently verified byte-for-byte `0xFF` |
| cert2 DER payload | `0x00F690–0x00FA4C`; 957 bytes; Binwalk-recognized X.509v3 certificate, header length 4 + sequence length 953 = 957 exact match to container length field |
| cert2 observed ASCII fields (unparsed) | Same pattern as cert1: two occurrences each of `Some-State1` and `Mediatek0`; identical validity window `150604031444Z`–`350604031444Z` |
| Terminal zero-filled region | `0x00FA4D–0x0FFFFF`; 984,499 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 11,848 + 12,000 + 12,000 + 12,004 + 12,004 + 16 + 2,221 + 3 + 1,469 + 984,499 = 1,048,576; exact, no unaccounted bytes |
| ASCII strings (min length 4) | 54 total; semantic classes: 5 PCM filenames, 5 embedded version tags, `cert1`/`cert2` labels, `Some-State1`/`Mediatek0`/validity-date X.509 fields (×2 certs); remainder are incidental byte-range coincidences inside the binary PCM payloads and DER signature blobs |
| UTF-16LE strings (min length 4) | NONE |
| Internal field semantics (main/cert1/cert2 length-field precise role, placeholder values `0x12345678`/`0x22345678`, secondary-field values, PCM microcode opcode content, DER certificate internal ASN.1 field decode and signature validation) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Main and both certificate wrapper headers share the identical magic-pair structure (`88 16 88 58` / `89 16 89 58`) already present in this project's Section 2.3 record for the `lk` partition leading bytes; internal field semantics remain independently `OPEN_BOUNDARY` in both locations |
| Existing database cross-reference | Section 2.6 currently lists `p18 spmfw` at summary depth only ("SPM firmware"); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/spmfw`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SPM_FIRMWARE_CONTAINER_WITH_FIVE_PCM_SUBIMAGES_AND_TWO_X509_SIGNING_CERTIFICATES` |

## p19 — `scp1`
| p19 | `scp1` | 1,048,576 | MTK GFH-style dual-image SCP (tinysys) container: 512-byte loader header (`tinysys-loader-CM4_A`, 1,080-byte payload) with its own GFH-wrapped cert1/cert2 X.509v3 pair; 512-byte main-firmware header (`tinysys-scp-CM4_A`, declared 544,628-byte payload) containing compiled ARM Cortex-M4 FreeRTOS/CHRE sensor-hub code and strings, with a 370,311-byte internal zero-filled gap, followed by its own GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/scp1.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p19 scp1` entry and Section 2.5/2.7 size exactly; existing database already records `scp2` as byte-identical to `scp1` by SHA256 — not re-verified this pass (out of scope; `scp2` is a separate partition/record) |
| GPT partition | p19; `scp1` |
| Physical block device | `/dev/block/mmcblk0p19`; major:minor `179:19` |
| By-name mapping | `/dev/block/by-name/scp1` -> `/dev/block/mmcblk0p19` |
| GPT start LBA | 724,992 |
| GPT end LBA | 727,039 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `6a5cebf8-54a7-4b89-8d1d-c5eb140b095b` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; four internal recognized containers (X.509v3 DER certificates) confirmed by Binwalk signature scan at `0x000840` (header length 4, sequence length 1,705), `0x0010F0` (header length 4, sequence length 953), `0x086830` (header length 4, sequence length 1,705), `0x0870E0` (header length 4, sequence length 953) |
| Full-partition nonzero-byte count | 158,937 |
| Full-partition zero-byte count | 889,639 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x08749C` |
| Loader GFH header extent | `0x000000–0x0001FF`; 512 bytes |
| Loader header magic 1 | `0x000000–0x000003`; raw bytes `88 16 88 58` |
| Loader header length field (LE32 @0x004) | `0x00000438` (1,080) |
| Loader header name field | `0x000008–0x00001B`; ASCII `tinysys-loader-CM4_A`, zero-padded to `0x000027` |
| Loader header FF-fill 1 | `0x000028–0x00002F`; 8 bytes; independently verified byte-for-byte `0xFF` |
| Loader header magic 2 | `0x000030–0x000033`; raw bytes `89 16 89 58` |
| Loader header secondary fields | `0x000034–0x00004F`; raw bytes `00 02 00 00 01 00 00 00 00 00 00 00 00 00 00 00 10 00 00 00 00 00 00 00 00 00 00 00` |
| Loader header FF-fill 2 | `0x000050–0x0001FF`; 432 bytes; independently verified byte-for-byte `0xFF` |
| Loader payload | `0x000200–0x000637`; 1,080 bytes; dense binary content, no recognized sub-container |
| Gap 1 | `0x000638–0x00063F`; 8 bytes; independently verified byte-for-byte zero |
| cert1(loader) GFH wrapper extent | `0x000640–0x000EEC`; 2,221 bytes |
| cert1(loader) length field | `0x000006AD` (1,709); name field `cert1` |
| cert1(loader) placeholder/magic2/fields | Identical layout to Section 2.6/p18 `spmfw` cert wrapper convention: placeholder `78 56 34 12`, magic2 `89 16 89 58`, secondary fields, placeholder2 `78 56 34 22`, FF-fill to `0x1FF` |
| cert1(loader) DER payload | `0x000840–0x000EEC`; 1,709 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap 2 | `0x000EED–0x000EEF`; 3 bytes; independently verified byte-for-byte zero |
| cert2(loader) GFH wrapper extent | `0x000EF0–0x0014AC`; 1,469 bytes |
| cert2(loader) length field | `0x000003BD` (957); name field `cert2` |
| cert2(loader) DER payload | `0x0010F0–0x0014AC`; 957 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 953 = 957 exact match |
| Gap 3 | `0x0014AD–0x0014AF`; 3 bytes; independently verified byte-for-byte zero |
| Main-firmware GFH header extent | `0x0014B0–0x0016AF`; 512 bytes |
| Main-firmware header name field | `0x0014B8–0x0014C9`; ASCII `tinysys-scp-CM4_A`, zero-padded |
| Main-firmware header length field (LE32 @+0x04) | `0x00084F74` (544,628) |
| Main-firmware header internal layout | Same magic1/FF-fill/magic2/fields/FF-fill structure as loader header, byte-for-byte pattern-identical aside from name and length field |
| Main-firmware declared payload extent | `0x0016B0–0x086623`; 544,628 bytes (exactly matches header length field; independently confirmed by exact distance to the next GFH magic at `0x086630` minus a 12-byte trailing gap) |
| Main-firmware payload region A | `0x0016B0–0x026828`; 151,929 bytes; dense; contains compiled ARM Cortex-M4 Thumb code and extensive ASCII debug/string content (FreeRTOS kernel, CHRE/contexthub sensor framework, build source paths, printf-style format strings) |
| Main-firmware internal zero-filled gap | `0x026829–0x080EAF`; 370,311 bytes; independently verified byte-for-byte zero; purpose (reserved/BSS data region vs. build artifact) `OPEN_BOUNDARY` |
| Main-firmware payload region B | `0x080EB0–0x086623`; 22,388 bytes; dense; contains sensor-driver-specific strings (`lis2hh12`, `akm09918`, `cm36558`, `bmp280`, `fakeAls`, `fakePs`) and overlay-mechanism strings (`ovl select`, `_ovly_table`), consistent with an overlay-loaded sensor-driver code segment; precise overlay boundary/table location `OPEN_BOUNDARY` |
| Gap 4 | `0x086624–0x08662F`; 12 bytes; independently verified byte-for-byte zero |
| cert1b(mainfw) GFH wrapper extent | `0x086630–0x086EDC`; 2,221 bytes; name field `cert1` |
| cert1b(mainfw) DER payload | `0x086830–0x086EDC`; 1,709 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 1,705 = 1,709 exact match |
| cert1 cross-instance identity | The complete 2,221-byte cert1(loader) wrapper (`0x000640` span) and the complete 2,221-byte cert1b(mainfw) wrapper (`0x086630` span) are byte-for-byte identical in full, including both headers and both DER payloads |
| Gap 5 | `0x086EDD–0x086EDF`; 3 bytes; independently verified byte-for-byte zero |
| cert2b(mainfw) GFH wrapper extent | `0x086EE0–0x08749C`; 1,469 bytes; name field `cert2` |
| cert2b(mainfw) DER payload | `0x0870E0–0x08749C`; 957 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 953 = 957 exact match |
| cert2 cross-instance comparison | The 1,469-byte cert2(loader) wrapper and 1,469-byte cert2b(mainfw) wrapper differ at 320 of 1,469 byte positions, including differences within both DER payloads (distinct SHA256 per DER); the two `cert1` instances are identical while the two `cert2` instances are distinct certificates |
| Observed X.509 ASCII fields (both cert pairs, unparsed) | Each of the four DER blobs contains two occurrences each of `Some-State1` and `Mediatek0`, and validity strings `150604031444Z`/`350604031444Z` (2015-06-04 03:14:44 UTC to 2035-06-04 03:14:44 UTC); full ASN.1 field decode and signature validation `OPEN_BOUNDARY` |
| Observed build/version strings (unparsed) | `FreeRTOS V10.0.1`, build date/time strings `Jun 13 2025` / `07:25:23`, `Lib version: 778c288c`, `Gesture lib version: eec60996`, `MPE_CAL_A_VER_18082801`, `STAMP_K_VER_18041204` |
| Terminal zero-filled region | `0x08749D–0x0FFFFF`; 494,435 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 1,080 + 8 + 2,221 + 3 + 1,469 + 3 + 512 + 151,929 + 370,311 + 22,388 + 12 + 2,221 + 3 + 1,469 + 494,435 = 1,048,576; exact, no unaccounted bytes |
| ASCII strings (min length 4) | 2,236 total; semantic classes: loader/main-firmware GFH names, `cert1`/`cert2` labels, X.509 fields (×4 DER blobs), FreeRTOS/CHRE/build-path/format strings, sensor-driver identifiers; remainder are incidental byte-range coincidences inside dense ARM Thumb code |
| UTF-16LE strings (min length 4) | 4 incidental matches only; no genuine UTF-16LE text content |
| Internal field semantics (all GFH length/secondary-field roles, placeholder values, internal zero-gap purpose, overlay-table mechanism, ARM Thumb code content, DER internal ASN.1 decode/signature validation) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Loader, main-firmware, and both certificate wrapper headers share the identical magic-pair structure (`88 16 88 58` / `89 16 89 58`) already recorded in this manifest for `p18 spmfw` and in Section 2.3 for the `lk` partition leading bytes; internal field semantics remain independently `OPEN_BOUNDARY` in all locations |
| Existing database cross-reference | Section 2.6 currently lists `p19 scp1` at summary depth only ("SCP firmware slot 1"), cross-referenced to `p20 scp2` as SHA256-identical; Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/scp1`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SCP_TINYSYS_DUAL_GFH_CONTAINER_WITH_LOADER_AND_MAIN_FREERTOS_CHRE_FIRMWARE_AND_FOUR_X509_CERTIFICATES` |

## p20 — `scp2`
| p20 | `scp2` | 1,048,576 | Byte-for-byte identical to `p19 scp1` (independently confirmed via full-file `cmp`, not merely SHA256): MTK GFH-style dual-image SCP (tinysys) container — 512-byte loader header (`tinysys-loader-CM4_A`, 1,080-byte payload) with its own GFH-wrapped cert1/cert2 X.509v3 pair; 512-byte main-firmware header (`tinysys-scp-CM4_A`, declared 544,628-byte payload) containing compiled ARM Cortex-M4 FreeRTOS/CHRE sensor-hub code and strings, with a 370,311-byte internal zero-filled gap, followed by its own GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/scp2.bin` |
| Content identity verification | Full-file `cmp` against `scp1.bin` performed this pass: zero byte differences across all 1,048,576 bytes; SHA256 also matches exactly. This upgrades the existing Section 2.6 "identical to scp1 by SHA256" claim from single-hash equivalence to independently confirmed full byte-for-byte identity |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p20 scp2` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p20; `scp2` |
| Physical block device | `/dev/block/mmcblk0p20`; major:minor `179:20` |
| By-name mapping | `/dev/block/by-name/scp2` -> `/dev/block/mmcblk0p20` |
| GPT start LBA | 727,040 |
| GPT end LBA | 729,087 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `a0d65bf8-e8de-4107-9434-1d318c843d37` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; four internal recognized containers (X.509v3 DER certificates), same offsets as `p19 scp1`: `0x000840` (header length 4, sequence length 1,705), `0x0010F0` (header length 4, sequence length 953), `0x086830` (header length 4, sequence length 1,705), `0x0870E0` (header length 4, sequence length 953) |
| Full-partition nonzero-byte count | 158,937 (identical to p19) |
| Full-partition zero-byte count | 889,639 (identical to p19) |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x08749C` |
| Loader GFH header extent | `0x000000–0x0001FF`; 512 bytes; magic1 `88 16 88 58`, length field `0x00000438` (1,080), name `tinysys-loader-CM4_A`, magic2 `89 16 89 58`, FF-fill regions independently re-verified byte-for-byte `0xFF` |
| Loader payload | `0x000200–0x000637`; 1,080 bytes |
| Gap 1 | `0x000638–0x00063F`; 8 bytes; independently verified byte-for-byte zero |
| cert1(loader) GFH wrapper extent | `0x000640–0x000EEC`; 2,221 bytes; length field `0x000006AD` (1,709); name `cert1` |
| cert1(loader) DER payload | `0x000840–0x000EEC`; 1,709 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap 2 | `0x000EED–0x000EEF`; 3 bytes; independently verified byte-for-byte zero |
| cert2(loader) GFH wrapper extent | `0x000EF0–0x0014AC`; 1,469 bytes; length field `0x000003BD` (957); name `cert2` |
| cert2(loader) DER payload | `0x0010F0–0x0014AC`; 957 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 953 = 957 exact match |
| Gap 3 | `0x0014AD–0x0014AF`; 3 bytes; independently verified byte-for-byte zero |
| Main-firmware GFH header extent | `0x0014B0–0x0016AF`; 512 bytes; name `tinysys-scp-CM4_A`; length field `0x00084F74` (544,628) |
| Main-firmware declared payload extent | `0x0016B0–0x086623`; 544,628 bytes |
| Main-firmware payload region A | `0x0016B0–0x026828`; 151,929 bytes; dense ARM Cortex-M4 Thumb code and FreeRTOS/CHRE debug strings |
| Main-firmware internal zero-filled gap | `0x026829–0x080EAF`; 370,311 bytes; independently verified byte-for-byte zero; purpose `OPEN_BOUNDARY` |
| Main-firmware payload region B | `0x080EB0–0x086623`; 22,388 bytes; dense; sensor-driver and overlay-mechanism strings (`lis2hh12`, `akm09918`, `cm36558`, `bmp280`, `ovl select`, `_ovly_table`) |
| Gap 4 | `0x086624–0x08662F`; 12 bytes; independently verified byte-for-byte zero |
| cert1b(mainfw) GFH wrapper extent | `0x086630–0x086EDC`; 2,221 bytes; name `cert1`; DER payload `0x086830–0x086EDC` (1,709 bytes) |
| cert1 cross-instance identity | As in p19: the full 2,221-byte cert1(loader) and cert1b(mainfw) wrappers are byte-for-byte identical, including both DER payloads |
| Gap 5 | `0x086EDD–0x086EDF`; 3 bytes; independently verified byte-for-byte zero |
| cert2b(mainfw) GFH wrapper extent | `0x086EE0–0x08749C`; 1,469 bytes; name `cert2`; DER payload `0x0870E0–0x08749C` (957 bytes) |
| cert2 cross-instance comparison | As in p19: the two `cert2` wrappers differ at 320 of 1,469 byte positions, including distinct DER content (distinct SHA256 per DER) |
| Observed X.509 ASCII fields (unparsed) | Same as p19: two occurrences each of `Some-State1` and `Mediatek0` per DER blob; validity strings `150604031444Z`/`350604031444Z` (2015-06-04 to 2035-06-04 UTC); full ASN.1 decode `OPEN_BOUNDARY` |
| Observed build/version strings (unparsed) | Same as p19: `FreeRTOS V10.0.1`, `Jun 13 2025` / `07:25:23`, `Lib version: 778c288c`, `Gesture lib version: eec60996`, `MPE_CAL_A_VER_18082801`, `STAMP_K_VER_18041204` |
| Terminal zero-filled region | `0x08749D–0x0FFFFF`; 494,435 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 1,080 + 8 + 2,221 + 3 + 1,469 + 3 + 512 + 151,929 + 370,311 + 22,388 + 12 + 2,221 + 3 + 1,469 + 494,435 = 1,048,576; exact, no unaccounted bytes |
| ASCII strings (min length 4) | 2,236 total (identical to p19); same semantic classes |
| UTF-16LE strings (min length 4) | 4 incidental matches only; no genuine UTF-16LE text content |
| Internal field semantics (all GFH length/secondary-field roles, placeholder values, internal zero-gap purpose, overlay-table mechanism, ARM Thumb code content, DER internal ASN.1 decode/signature validation) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Same magic-pair structure (`88 16 88 58` / `89 16 89 58`) as `p18 spmfw`, `p19 scp1`, and Section 2.3 `lk` partition leading bytes |
| Existing database cross-reference | Section 2.6 currently lists `p20 scp2` at summary depth only ("SCP firmware slot 2", identical to scp1 by SHA256); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/scp2`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure, inherited from the independently-verified p19 analysis via confirmed byte-for-byte file identity |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SCP_TINYSYS_DUAL_GFH_CONTAINER_WITH_LOADER_AND_MAIN_FREERTOS_CHRE_FIRMWARE_AND_FOUR_X509_CERTIFICATES` |

## p21 — `sspm_1`
| p21 | `sspm_1` | 1,048,576 | MTK GFH-style single-image firmware container: 512-byte header (`tinysys-sspm`, declared 428,656-byte payload) containing compiled ARM Cortex-M power-management firmware (DCM, MCDI, DVFS/regulator, SODI/DPIDLE sleep-state code and strings), immediately followed with zero gap by a GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/sspm_1.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p21 sspm_1` entry and Section 2.5/2.7 size exactly; existing database records `sspm_2` as byte-identical to `sspm_1` by SHA256 — not re-verified this pass (separate partition/record) |
| GPT partition | p21; `sspm_1` |
| Physical block device | `/dev/block/mmcblk0p21`; major:minor `179:21` |
| By-name mapping | `/dev/block/by-name/sspm_1` -> `/dev/block/mmcblk0p21` |
| GPT start LBA | 729,088 |
| GPT end LBA | 731,135 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `46f0c0bb-f227-4eb6-b82f-66408e13e36d` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; two internal recognized containers (X.509v3 DER certificates) confirmed by Binwalk signature scan at `0x068E70` (header length 4, sequence length 1,705) and `0x069720` (header length 4, sequence length 953) |
| Full-partition nonzero-byte count | 425,751 |
| Full-partition zero-byte count | 622,825 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x069ADC` |
| Main GFH header extent | `0x000000–0x0001FF`; 512 bytes |
| Main header magic 1 | `0x000000–0x000003`; raw bytes `88 16 88 58` |
| Main header length field (LE32 @0x004) | `0x00068A70` (428,656) |
| Main header name field | `0x000008–0x000013`; ASCII `tinysys-sspm`, zero-padded to `0x000027` |
| Main header FF-fill 1 | `0x000028–0x00002F`; 8 bytes; independently verified byte-for-byte `0xFF` |
| Main header magic 2 | `0x000030–0x000033`; raw bytes `89 16 89 58` |
| Main header secondary fields | `0x000034–0x00004F`; raw bytes `00 02 00 00 01 00 00 00 00 00 00 00 00 00 00 00 10 00 00 00 00 00 00 00 00 00 00 00` |
| Main header FF-fill 2 | `0x000050–0x0001FF`; 432 bytes; independently verified byte-for-byte `0xFF` |
| Main payload extent | `0x000200–0x068C6F`; 428,656 bytes; exactly matches header length field, with zero gap (0 bytes) to the next GFH magic at `0x068C70` |
| Main payload density | 421,997 of 428,656 bytes nonzero; no internal zero run exceeding 100 bytes detected (dense throughout, unlike `p19 scp1`'s main-firmware region) |
| Main payload observed content | Compiled ARM Cortex-M firmware and extensive ASCII debug/string content: DCM (`dcm: read fail`), frequency-hopping (`[FH] DDS stable timeout`, `[FH] NS MEMPLL`), MCDI (`mcdi_read_bus`, `mcdi_write_bus`), regulator/PMU (`vproc`, `vsram_proc`, `vsram_others`, `[PMU]mt6357 regulator`), sleep-state transition strings (`SUSPEND enter/leave CMD again`, `DPIDLE enter/leave CMD again`, `SODI enter/leave CMD again`, `sspm doesn't leave SLP`), watchdog (`WDT T.O from 0x%x`), mailbox/IPI (`mbox %u init error`, `SLP IPI fail`) |
| Observed build string (unparsed) | `Build time: %s %s` format string (offset `0x000A54`) immediately followed by build date literal `Apr  1 2020` (offset `0x000A68`) |
| cert1 GFH wrapper extent | `0x068C70–0x06951C`; 2,221 bytes |
| cert1 length field (LE32 @+0x04) | `0x000006AD` (1,709); name field `cert1` |
| cert1 internal layout | Same magic1/zero-gap/placeholder/magic2/fields/placeholder2/FF-fill convention as `p18 spmfw` and `p19 scp1` cert wrappers |
| cert1 DER payload | `0x068E70–0x06951C`; 1,709 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap after cert1 | `0x06951D–0x06951F`; 3 bytes; independently verified byte-for-byte zero |
| cert2 GFH wrapper extent | `0x069520–0x069ADC`; 1,469 bytes |
| cert2 length field (LE32 @+0x04) | `0x000003BD` (957); name field `cert2` |
| cert2 DER payload | `0x069720–0x069ADC`; 957 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 953 = 957 exact match |
| Observed X.509 ASCII fields (both certs, unparsed) | Two occurrences each of `Some-State1` and `Mediatek0`; validity strings `150604031444Z`/`350604031444Z` (2015-06-04 03:14:44 UTC to 2035-06-04 03:14:44 UTC), same validity window observed in `p18 spmfw` and `p19 scp1`/`p20 scp2` certificates; full ASN.1 field decode and signature validation `OPEN_BOUNDARY` |
| Terminal zero-filled region | `0x069ADD–0x0FFFFF`; 615,715 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 428,656 + 0 + 2,221 + 3 + 1,469 + 615,715 = 1,048,576; exact, no unaccounted bytes |
| ASCII strings (min length 4) | 4,872 total; semantic classes: header name, build-time string, extensive power-management/regulator/sleep-state debug strings, `cert1`/`cert2` labels, X.509 fields (×2 DER blobs); remainder are incidental byte-range coincidences inside compiled machine code |
| UTF-16LE strings (min length 4) | 1 incidental match only; no genuine UTF-16LE text content |
| Internal field semantics (header length/secondary-field roles, placeholder values, DER internal ASN.1 decode/signature validation, firmware machine-code content) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Header and both certificate wrappers share the identical magic-pair structure (`88 16 88 58` / `89 16 89 58`) already recorded in this manifest for `p18 spmfw` and `p19 scp1`/`p20 scp2`, and in Section 2.3 for the `lk` partition leading bytes; internal field semantics remain independently `OPEN_BOUNDARY` in all locations |
| Existing database cross-reference | Section 2.6 currently lists `p21 sspm_1` at summary depth only ("SSPM firmware slot 1"), cross-referenced to `p22 sspm_2` as SHA256-identical; Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/sspm_1`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SSPM_TINYSYS_GFH_CONTAINER_WITH_POWER_MANAGEMENT_FIRMWARE_AND_TWO_X509_SIGNING_CERTIFICATES` |

## p22 — `sspm_2`
| p22 | `sspm_2` | 1,048,576 | Byte-for-byte identical to `p21 sspm_1` (independently confirmed via full-file `cmp`, not merely SHA256): MTK GFH-style single-image firmware container — 512-byte header (`tinysys-sspm`, declared 428,656-byte payload) containing compiled ARM Cortex-M power-management firmware (DCM, MCDI, DVFS/regulator, SODI/DPIDLE sleep-state code and strings), immediately followed with zero gap by a GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/sspm_2.bin` |
| Content identity verification | Full-file `cmp` against `sspm_1.bin` performed this pass: zero byte differences across all 1,048,576 bytes; SHA256 also matches exactly. Upgrades the existing Section 2.6 "identical to sspm_1 by SHA256" claim to independently confirmed full byte-for-byte identity |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p22 sspm_2` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p22; `sspm_2` |
| Physical block device | `/dev/block/mmcblk0p22`; major:minor `179:22` |
| By-name mapping | `/dev/block/by-name/sspm_2` -> `/dev/block/mmcblk0p22` |
| GPT start LBA | 731,136 |
| GPT end LBA | 733,183 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `fbc2c131-6392-4217-b51e-548a6edb03d0` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; two internal recognized containers (X.509v3 DER certificates), same offsets as `p21 sspm_1`: `0x068E70` (header length 4, sequence length 1,705) and `0x069720` (header length 4, sequence length 953) |
| Full-partition nonzero-byte count | 425,751 (identical to p21) |
| Full-partition zero-byte count | 622,825 (identical to p21) |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x069ADC` |
| Main GFH header extent | `0x000000–0x0001FF`; 512 bytes; magic1 `88 16 88 58`, length field `0x00068A70` (428,656), name `tinysys-sspm`, magic2 `89 16 89 58`, FF-fill regions independently re-verified byte-for-byte `0xFF` |
| Main payload extent | `0x000200–0x068C6F`; 428,656 bytes; exactly matches header length field, zero gap to next GFH magic at `0x068C70`; 421,997 of 428,656 bytes nonzero, no internal zero run exceeding 100 bytes |
| Main payload observed content | Same as p21: DCM, frequency-hopping (`[FH]...`), MCDI, regulator/PMU (`vproc`, `vsram_proc`, `vsram_others`, `mt6357 regulator`), sleep-state transitions (`SUSPEND`/`DPIDLE`/`SODI` enter/leave, `sspm doesn't leave SLP`), watchdog, mailbox/IPI strings |
| Observed build string (unparsed) | `Build time: %s %s` (offset `0x000A54`) followed by build date literal `Apr  1 2020` (offset `0x000A68`) — identical to p21 |
| cert1 GFH wrapper extent | `0x068C70–0x06951C`; 2,221 bytes; length field `0x000006AD` (1,709); name `cert1`; DER payload `0x068E70–0x06951C` (1,709 bytes), Binwalk header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap after cert1 | `0x06951D–0x06951F`; 3 bytes; independently verified byte-for-byte zero |
| cert2 GFH wrapper extent | `0x069520–0x069ADC`; 1,469 bytes; length field `0x000003BD` (957); name `cert2`; DER payload `0x069720–0x069ADC` (957 bytes), Binwalk header length 4 + sequence length 953 = 957 exact match |
| Observed X.509 ASCII fields (unparsed) | Same as p21: two occurrences each of `Some-State1` and `Mediatek0`; validity strings `150604031444Z`/`350604031444Z` (2015-06-04 to 2035-06-04 UTC); full ASN.1 decode `OPEN_BOUNDARY` |
| Terminal zero-filled region | `0x069ADD–0x0FFFFF`; 615,715 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 428,656 + 0 + 2,221 + 3 + 1,469 + 615,715 = 1,048,576; exact, no unaccounted bytes |
| ASCII strings (min length 4) | 4,872 total (identical to p21); same semantic classes |
| UTF-16LE strings (min length 4) | 1 incidental match only; no genuine UTF-16LE text content |
| Internal field semantics (header length/secondary-field roles, placeholder values, DER internal ASN.1 decode/signature validation, firmware machine-code content) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Same magic-pair structure (`88 16 88 58` / `89 16 89 58`) as `p18 spmfw`, `p19 scp1`/`p20 scp2`, `p21 sspm_1`, and Section 2.3 `lk` partition leading bytes |
| Existing database cross-reference | Section 2.6 currently lists `p22 sspm_2` at summary depth only ("SSPM firmware slot 2", identical to sspm_1 by SHA256); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/sspm_2`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure, inherited from the independently-verified p21 analysis via confirmed byte-for-byte file identity |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_SSPM_TINYSYS_GFH_CONTAINER_WITH_POWER_MANAGEMENT_FIRMWARE_AND_TWO_X509_SIGNING_CERTIFICATES` |

## p23 — `gz1`
| p23 | `gz1` | 16,777,216 | Completely zero-filled raw partition image containing no filesystem, container, header, allocation structure, text, key material, marker, or residual payload | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` | `CLOSED` | `080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/gz1.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p23 gz1` entry and Section 2.5/2.7 size exactly; existing database records `gz2` as byte-identical to `gz1` by SHA256 — not re-verified this pass (separate partition/record) |
| GPT partition | p23; `gz1` |
| Physical block device | `/dev/block/mmcblk0p23`; major:minor `179:23` |
| By-name mapping | `/dev/block/by-name/gz1` -> `/dev/block/mmcblk0p23` |
| GPT start LBA | 733,184 |
| GPT end LBA | 765,951 inclusive |
| GPT sector count | 32,768 |
| GPT unique partition GUID | `e195a981-e285-4734-8025-ec323e9589d9` |
| Object size | 16,777,216 bytes; `0x01000000`; 16 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned zero entries |
| Distinct byte values | One: `0x00` |
| Nonzero-byte count | 0 |
| Zero-byte count | 16,777,216 |
| Zero-filled extent | Entire evidence object, `0x000000–0xFFFFFF` |
| Independent whole-object verification | Full 16,777,216-byte object compared identical to a generated zero buffer of equal length; zero differences |
| Independent segmented verification | Eight separately compared 2,097,152-byte (2 MiB) ranges spanning `0x000000–0x1FFFFF` through `0xE00000–0xFFFFFF`; all eight byte-for-byte identical to zero |
| First nonzero byte | NONE |
| Last nonzero byte | NONE |
| Shannon entropy | 0.000000000 bits per byte |
| ASCII strings (min length 4) | NONE |
| UTF-16LE strings (min length 4) | NONE |
| Structured payload | NONE |
| Recoverable deleted content | NONE |
| Original provisioning reason | `OPEN_BOUNDARY`; the all-zero image contains no evidence establishing why the GeniZone-named GPT entry is populated entirely with zero |
| Existing database cross-reference | Section 2.6 currently lists `p23 gz1` at this same conclusion ("zero-filled GeniZone reserved slot 1", full-partition all-zero hash confirmed); Section 2.5 confirms geometry; Section 2.7 lists role "GeniZone reserved slot 1", ROM action `NEVER TOUCH`; this record formalizes the existing summary into full structural closure with independent re-verification |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` |
| Content classification | `FULLY_ZERO_FILLED_RAW_PARTITION_IMAGE` |

## p24 — `gz2`
| p24 | `gz2` | 16,777,216 | Completely zero-filled raw partition image containing no filesystem, container, header, allocation structure, text, key material, marker, or residual payload; byte-for-byte identical to `p23 gz1` | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` | `CLOSED` | `080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/gz2.bin` |
| Content identity verification | Full-file `cmp` against `gz1.bin` performed this pass: zero byte differences across all 16,777,216 bytes; SHA256 also matches exactly. Additionally re-verified independently on its own: full-file comparison against a generated zero buffer matches; distinct byte values = 1 (`0x00`); nonzero-byte count = 0 |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p24 gz2` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p24; `gz2` |
| Physical block device | `/dev/block/mmcblk0p24`; major:minor `179:24` |
| By-name mapping | `/dev/block/by-name/gz2` -> `/dev/block/mmcblk0p24` |
| GPT start LBA | 765,952 |
| GPT end LBA | 798,719 inclusive |
| GPT sector count | 32,768 |
| GPT unique partition GUID | `e29052f8-5d3a-4e97-adb5-5f312ce6610a` |
| Object size | 16,777,216 bytes; `0x01000000`; 16 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| Recognized embedded signature | NONE; complete Binwalk signature scan returned zero entries |
| Distinct byte values | One: `0x00` |
| Nonzero-byte count | 0 |
| Zero-byte count | 16,777,216 |
| Zero-filled extent | Entire evidence object, `0x000000–0xFFFFFF` |
| First nonzero byte | NONE |
| Last nonzero byte | NONE |
| Shannon entropy | 0.000000000 bits per byte |
| ASCII strings (min length 4) | NONE |
| UTF-16LE strings (min length 4) | NONE |
| Structured payload | NONE |
| Recoverable deleted content | NONE |
| Original provisioning reason | `OPEN_BOUNDARY`; the all-zero image contains no evidence establishing why the GeniZone-named GPT entry is populated entirely with zero |
| Existing database cross-reference | Section 2.6 currently lists `p24 gz2` at this same conclusion ("zero-filled GeniZone reserved slot 2", identical to gz1 by SHA256, full-partition all-zero hash confirmed); Section 2.5 confirms geometry; Section 2.7 lists role "GeniZone reserved slot 2", ROM action `NEVER TOUCH`; this record formalizes the existing summary into full structural closure with independent re-verification |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` |
| Content classification | `FULLY_ZERO_FILLED_RAW_PARTITION_IMAGE` |

## p25 — `nvram`
| p25 | `nvram` | 67,108,864 | Raw vendor NVRAM binary-region image containing a 12-byte length prefix, a complete 159-record fixed map, a 441,975-byte mapped aggregate, bounded unreferenced residual data, primary and backup `0xFF` fill, an exact prefix-plus-map backup copy, four operation-log slots, and a 16-byte control record | `CURRENT_RESEARCH_CAPTURE`; NVRAM payload fields, residual ownership, backup format, timestamps, and control-field semantics `OPEN_BOUNDARY` | `CLOSED` | `5266ee901675a21eb2655aff0e84943917fd52e9b282f5aced4bf04ff9a7ee55` |

| Field | Verified value |
|---|---|
| Evidence object | `nvram.bin` |
| Object size | 67,108,864 bytes; `0x04000000` |
| Acquisition completeness | Expected from GPT: 67,108,864 bytes; received: 67,108,864 bytes; difference: 0 |
| Partition identity | p25; `nvram`; `/dev/block/mmcblk0p25`; major:minor `179:25` |
| GPT geometry | Start LBA 798,720; end LBA 929,791 inclusive; 131,072 sectors |
| GPT unique GUID | `9c3cabd7-a35d-4b45-8c57-b80775426b35` |
| Device-node metadata | `brw-rw----`; owner/group `root:system`; both supplied SELinux-context columns `u:object_r:block_device:s0` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Manual signature scan | No EXT magic, ELF, ZIP, gzip, PNG, SQLite, Android boot, AVB, FDT, DTBO, Android sparse, MediaTek GFH, or GPT signature established |
| Complete top-level layout | Primary region `0x00000000–0x0009FFFF`; zero span `0x000A0000–0x03F3FFFF`; backup-related region `0x03F40000–0x03F5FFFF`; zero span `0x03F60000–0x03FBFFFF`; operation log `0x03FC0000–0x03FC07FF`; zero span `0x03FC0800–0x03FDFFFF`; control record `0x03FE0000–0x03FE000F`; terminal zeros `0x03FE0010–0x03FFFFFF` |
| Layout reconciliation | All extents are contiguous and nonoverlapping; summed extent 67,108,864 bytes, exactly equal to the evidence-object size |
| Primary prefix | `0x00000000–0x0000000B`; 12 bytes |
| Primary prefix raw bytes | `28 57 00 00 77 BE 06 00 FF FF FF FF` |
| Primary prefix LE32 vector | Map length `0x00005728` = 22,312; aggregate length `0x0006BE77` = 441,975; third value `0xFFFFFFFF` |
| Primary prefix field semantics | The first two LE32 values exactly bound the following map and aggregate; any additional producer-specific meaning remains `OPEN_BOUNDARY` |
| Map extent | `0x0000000C–0x00005733`; exactly 22,312 bytes |
| Map structure | 52-byte header followed by 159 contiguous 140-byte records; `52 + (159 × 140) = 22,312`, remainder 0 |
| Map header extent | Map-relative `0x0000–0x0033`; absolute `0x0000000C–0x0000003F` |
| Map header LE32 vector | `D38E45C6 00000033 0000001B 00040004 00930004 00000000 00000000 00000000 00000000 00000000 00000000 0000018A 00000000` |
| Map header field semantics | `OPEN_BOUNDARY` |
| Map record leading fields | LE32 state at record `+0x00`; aggregate offset at `+0x04`; byte length at `+0x08`; NUL-terminated absolute path beginning at `+0x0C` |
| Map record state | All 159 state fields equal `7` |
| Map offset continuity | Zero discontinuities; first aggregate offset 0; every record begins at the preceding record’s end |
| Map terminal reconciliation | Final record ends exactly at aggregate byte 441,975 |
| Map path population | 159 unique ASCII paths; all absolute and rooted at `/mnt/vendor/nvdata/`; path lengths 34–45 bytes |
| Map repeated control text | Every record contains `is_support_ecci_c2k 1\n` and `rt property get fail : \n`; 159 occurrences of each in the primary map |
| Map internal-field semantics | Fields beyond the proven state, offset, length, and path relationship remain `OPEN_BOUNDARY` |
| First map record | `/mnt/vendor/nvdata/APCFG/APRDEB/WIFI_CUSTOM`; aggregate offset 0; length 6 |
| Final map record | `/mnt/vendor/nvdata/md/NVRAM/CALIBRAT/ML0B_000`; aggregate offset 441,863; length 112 |
| Map SHA-256 | `0fa3935521711b223f8ae1963dda5a6a19b1e5c30f68f415fe154a62155e5ada` |
| Aggregate extent | `0x00005734–0x000715AA`; exactly 441,975 bytes |
| Aggregate relationship | All 159 map records address continuous, nonoverlapping byte ranges inside this aggregate; aggregate offsets and lengths reconcile exactly |
| Aggregate group reconciliation | `APCFG/APRDEB`: 4 records, 2,222 bytes; `APCFG/APRDCL`: 4 records, 5,976 bytes; `md/NVRAM/NVD_IMEI`: 4 records, 660 bytes; `md/NVRAM/CALIBRAT`: 147 records, 433,117 bytes; total 159 records and 441,975 bytes |
| Aggregate byte state | 62,626 nonzero bytes; 379,349 zero bytes; 17,095 `0xFF` bytes; all 256 byte values represented |
| Mapped-record population | 38 records contain at least one nonzero byte; 121 records are completely zero-filled; two records are completely `0xFF` |
| Population by group | `APCFG/APRDEB`: 3 populated, 1 zero; `APCFG/APRDCL`: 0 populated, 4 zero; `md/NVRAM/NVD_IMEI`: 0 populated, 4 zero; `md/NVRAM/CALIBRAT`: 35 populated, 112 zero |
| Fully `0xFF` mapped records | `WIFI_CUSTOM`: 6/6 bytes `0xFF`; `GPS`: 54/54 bytes `0xFF` |
| Partially `0xFF` Wi-Fi record | `WIFI`: 2,050 logical bytes; exactly 144 nonzero bytes, all `0xFF`; remaining 1,906 bytes zero |
| `FILE_VER` state | Mapped length 5,846 bytes; exact `79 × 74` geometry, but all 5,846 bytes are zero in this p25 aggregate; live-file registry labels are therefore not attributed to this capture |
| Populated-record notation | Entries below use `logical-size/nonzero-byte-count`; every mapped record absent from this list is exactly zero-filled |
| Complete populated-record list | `WIFI_CUSTOM=6/6; GPS=54/54; WIFI=2050/144; EA40_001=121198/20397; UL1U_000=172/59; UL1I_000=780/72; HL1H_000=144/23; EL70_000=192/27; HL1E_000=144/9; YS00_000=332/4; LA0V_002=397/16; ML5A_000=104/2; EA50_001=8722/787; EL04_001=472/14; ULB2_000=780/76; ULBR_000=3540/242; ULBZ_000=3540/158; HL19_001=530/20; EL90_001=25856/730; UL1B_000=780/2; EL78_000=1032/2; UL1E_000=780/2; ULBQ_000=3540/2; ULC0_000=3540/2; UL15_001=530/2; EL93_001=8764/6; EL77_000=1032/2; ULBX_000=3540/4; HL10_001=530/2; TD6Z_000=76/2; ULBS_000=3540/2; EL92_001=127048/38313; ULB4_000=172/148; EL79_000=1032/635; LA0F_002=136/37; UL14_001=530/415; HL1C_000=144/125; ML0B_000=112/83` |
| `APCFG/APRDEB` complete inventory | `WIFI_CUSTOM=6; GPS=54; WIFI=2050; BT_Addr=112` |
| `APCFG/APRDCL` complete inventory | `AUXADC=78; FILE_VER=5846; FG=26; MD_SBP=26` |
| `md/NVRAM/NVD_IMEI` complete inventory | `LD0B_001=384; FILELIST=36; NV01_000=96; NV0S_000=144` |
| `md/NVRAM/CALIBRAT` complete inventory | `YS03_000=148; LA0N_002=88; UL1Z_000=168; HL13_001=530; LA0U_000=316; ULBY_000=3540; ML0D_000=16458; ULC1_000=3540; HL1G_000=144; UL16_001=530; LA26_000=4184; HL12_001=530; EL71_000=192; UL22_000=168; UL25_000=168; FILELIST=1323; HL18_001=530; UL13_001=530; UL20_000=168; EL03_001=122; HL1B_000=144; TD2B_000=76; HL15_001=530; EL76_000=1032; LA05_002=332; ML09_001=144; ULBW_000=3540; ULBU_000=3540; LA0L_002=88; EL74_000=192; LA08_002=736; LA0D_002=736; HL11_001=530; LA0I_002=80; UL12_001=530; ULB3_000=780; UL1O_000=172; UL1X_000=168; LA0C_002=736; UL1J_000=780; ULBT_000=3540; ULBV_000=3540; LA0E_002=136; EL91_001=688; LA0K_006=536; LA0P_003=104; LA0B_002=736; LA0A_002=736; EL73_000=192; LA0T_000=316; ULB0_001=530; LA0M_002=88; EA10_001=30528; EA40_001=121198; UL1U_000=172; UL1I_000=780; HL1H_000=144; EL70_000=192; HL1E_000=144; YS00_000=332; HL1A_000=144; LA0G_002=136; LA0V_002=397; ML5A_000=104; EA50_001=8722; EL04_001=472; ULB2_000=780; ULBR_000=3540; UL1P_000=172; ULB9_000=144; ULBZ_000=3540; HL19_001=530; EL90_001=25856; UL1B_000=780; LA1V_000=76; LA0Q_002=76; UL1A_001=530; UL1D_000=780; UL18_001=530; EL78_000=1032; UL1C_000=780; UL1W_000=168; ULB6_001=530; LA1U_000=88; ULBC_000=80; UL1Y_000=168; HL14_001=530; UL1S_000=172; UL1E_000=780; ULBQ_000=3540; UL23_000=168; LA0J_004=120; ULB5_000=172; HL1F_000=144; ULB7_001=530; ULB8_000=144; ULC0_000=3540; HL1I_000=144; UL15_001=530; ULB1_001=530; EL72_000=192; UL1V_000=172; UL1Q_000=172; ML4A_000=88; UL17_001=530; EL93_001=8764; LA0O_002=88; ML0C_000=240; UL1N_000=172; UL1T_000=172; LA0H_002=136; HL16_001=530; ULBB_000=168; EL77_000=1032; ULBA_000=168; EL75_000=1032; UL1G_000=780; ULBX_000=3540; UL21_000=168; EA03_001=80; UL1F_000=780; ULBD_000=76; UL1H_000=780; UL19_001=530; HL1D_000=144; UL24_000=168; HL10_001=530; UL1M_000=172; UL1K_000=780; HL17_001=530; UL1R_000=172; YS01_000=397; LA09_002=736; TD6Z_000=76; ULBS_000=3540; LA06_002=736; UL11_001=530; UL10_000=88; HL1J_000=144; LA07_002=736; EL92_001=127048; ULB4_000=172; EL79_000=1032; LA0F_002=136; UL14_001=530; HL1C_000=144; ML0B_000=112` |
| `LDI\0` signature census | 149 total occurrences: 56 internal to mapped `EA40_001`; 87 internal to mapped `EL92_001`; six in unreferenced residual C; none begin one of the 159 mapped payloads |
| Aggregate SHA-256 | `5d91622f883dcd4451348b274a0e23bf9b5a83a91816ba41dc4f4f889b176c0c` |
| Unreferenced primary residual | `0x000715AB–0x0008BE76`; 108,748 bytes; 33,632 nonzero and 75,116 zero bytes |
| Residual A | `0x000715AB–0x0007BE24`; 43,130 bytes; 33,261 nonzero; dense repeated numeric/binary material |
| Residual B | `0x0007BE25–0x0008B620`; 63,484 bytes; 63,460 zero bytes and exactly 24 nonzero bytes, forming twelve `EF CD` pairs spaced `0x1314` bytes apart |
| Residual C | `0x0008B621–0x0008BE76`; 2,134 bytes; 347 nonzero bytes; contains six `LDI\0` sequences at `0x0008B629`, `0x0008B6D5`, `0x0008BADD`, `0x0008BB65`, `0x0008BD77`, and `0x0008BE07` |
| Residual classification | Not referenced by any map record; contains calibration-like repetition and structured fragments, but exact ownership and lifecycle state remain `OPEN_BOUNDARY` |
| Primary `0xFF` fill | `0x0008BE77–0x0009FFFF`; 82,313 bytes; continuous `0xFF`; independently verified |
| Main zero span | `0x000A0000–0x03F3FFFF`; 65,667,072 bytes; completely zero-filled |
| Backup-related region | `0x03F40000–0x03F5FFFF`; exactly 131,072 bytes |
| Backup pre-marker zeros | `0x03F40000–0x03F405F0`; 1,521 zero bytes |
| Backup marker | `0x03F405F1–0x03F405F4`; raw `DD CC BB AA` |
| Backup structural copy | `0x03F405F5–0x03F45D28`; 22,324 bytes; exact byte-for-byte copy of primary `0x00000000–0x00005733`, comprising the 12-byte prefix and complete map |
| Backup raw-match extension | Raw equality with the primary continues through 22,528 bytes, ending at backup `0x03F45DF4`, because the first 204 aggregate bytes are also `0xFF`; this does not extend the proven structural copy beyond the map |
| Backup `0xFF` fill | `0x03F45D29–0x03F5FFFF`; 107,223 bytes; continuous `0xFF`; independently verified |
| Backup format semantics | Marker meaning, record lifecycle, and whether any fill bytes represent intended aggregate content remain `OPEN_BOUNDARY` |
| Post-backup zero span | `0x03F60000–0x03FBFFFF`; 393,216 bytes; completely zero-filled |
| Operation-log allocation | `0x03FC0000–0x03FC07FF`; four fixed 512-byte slots; 2,048 bytes |
| Operation-log common structure | Magic `7B 7B 5A 5A` (`{{ZZ`); LE32 field `1` at `+0x04`; indices `0–3` at `+0x08`; common name at `+0x14`: `FileOp_BackupToBinRegion_All_Exx` |
| Operation-log record 0 | `0x03FC0000–0x03FC01FF`; index 0; LE32 `0x00000925`; message length 55; `2025-07-30 16:10:13.777:Enter FileOp_BackupToBinRegion_`; last nonzero at slot `+0x82` |
| Operation-log record 1 | `0x03FC0200–0x03FC03FF`; index 1; LE32 `0x00000B3E`; message length 44; `(null):Leave FileOp_BackupToBinRegion_All_Ex`; last nonzero at slot `+0x77` |
| Operation-log record 2 | `0x03FC0400–0x03FC05FF`; index 2; LE32 `0x00000925`; message length 55; `2011-01-11 12:52:53.557:Enter FileOp_BackupToBinRegion_`; last nonzero at slot `+0x82` |
| Operation-log record 3 | `0x03FC0600–0x03FC07FF`; index 3; LE32 `0x00000B3E`; message length 44; `(null):Leave FileOp_BackupToBinRegion_All_Ex`; last nonzero at slot `+0x77` |
| Operation-log chronology | Literal 2025 and 2011 timestamp strings are preserved as observed; clock validity, record chronology, and the meaning of the two numeric fields remain `OPEN_BOUNDARY` |
| Operation-log SHA-256 | `5fa42fcb8007f6410a5c8e4be034fbff18b2a6be05bb7fbfcdfcd73ffd0b4051` |
| Post-log zero span | `0x03FC0800–0x03FDFFFF`; 129,024 bytes; completely zero-filled |
| Terminal control record | `0x03FE0000–0x03FE000F`; raw `7B 7B 5A 5A 00 00 00 00 04 00 00 00 25 E2 2B 4D` |
| Terminal control LE32 vector | `5A5A7B7B 00000000 00000004 4D2BE225` |
| Terminal control semantics | Shares the operation-log magic; meanings of fields `0`, `4`, and `0x4D2BE225` remain `OPEN_BOUNDARY` |
| Terminal control SHA-256 | `0ec7f9067cdc0b331fbc738221eb053d5188ae9119d8b848945eaeb9a81dc83d` |
| Terminal zero span | `0x03FE0010–0x03FFFFFF`; 131,056 bytes; completely zero-filled |
| First nonzero byte | `0x00000000` |
| Last nonzero byte | `0x03FE000F` |
| Partition nonzero-byte count | 322,148 |
| Partition zero-byte count | 66,786,716 |
| Partition `0xFF` count | 214,732 |
| Byte-alphabet coverage | All 256 possible byte values occur |
| Populated 4 KiB runs | `0x00000000–0x00005FFF`; `0x00020000–0x00023FFF`; `0x00027000–0x0007BFFF`; `0x0007D000–0x00081FFF`; `0x00083000–0x00088FFF`; `0x0008A000–0x0009FFFF`; `0x03F40000–0x03F5FFFF`; `0x03FC0000–0x03FC0FFF`; `0x03FE0000–0x03FE0FFF` |
| ASCII strings | 1,374 occurrences at minimum length 4; 489 unique strings; length range 4–55 bytes |
| UTF-16LE strings | 364 occurrences at minimum length 4; 53 unique strings; length range 4–9 code units |
| Semantic text classes | Map paths and repeated map control text; NVRAM identifiers; backup-operation names and messages; incidental binary coincidences |
| Independent fill verification | All reported long zero and `0xFF` ranges passed both direct byte-count validation and independent `cmp` comparison streams |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `5266ee901675a21eb2655aff0e84943917fd52e9b282f5aced4bf04ff9a7ee55` |
| Content classification | `RAW_NVRAM_BINREGION_WITH_LENGTH_PREFIXED_MAP_MAPPED_AGGREGATE_RESIDUAL_BACKUP_LOG_AND_CONTROL` |


## p26 — `lk`
| p26 | `lk` | 1,048,576 | MTK GFH-style dual-image bootloader container: 512-byte header (`lk`, declared 722,396-byte payload) containing the compiled Little Kernel bootloader (PMIC/MT6357 init, fastboot, GPIO/DTB parsing, SoC-variant detection code and strings) with its own GFH-wrapped cert1/cert2 X.509v3 pair; followed by a 512-byte `lk_main_dtb` header (declared 89,545-byte FDT payload, version 17) with its own GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `3860c8857554284a03cc844b7c8435c873355a7f175a36ae96d94890c702aa41` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/lk.bin` |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p26 lk` entry and Section 2.5/2.7 size exactly; existing database records `lk2` as byte-identical to `lk` by SHA256 — not re-verified this pass (separate partition/record); leading bytes `88 16 88 58` and ASCII `lk` at offset `0x08` already noted in Section 2.3 GPT-capability-surface record — confirmed again here at full structural depth |
| GPT partition | p26; `lk` |
| Physical block device | `/dev/block/mmcblk0p26`; major:minor `179:26` |
| By-name mapping | `/dev/block/by-name/lk` -> `/dev/block/mmcblk0p26` |
| GPT start LBA | 929,792 |
| GPT end LBA | 931,839 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `e7099731-95a6-45a6-a1e5-1b6aba032cf1` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; multiple internal recognized structures (see below) |
| Full-partition nonzero-byte count | 596,552 |
| Full-partition zero-byte count | 452,024 |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x0C848C` |
| Main `lk` GFH header extent | `0x000000–0x0001FF`; 512 bytes |
| Main header magic 1 | `0x000000–0x000003`; raw bytes `88 16 88 58` |
| Main header length field (LE32 @0x004) | `0x000B05DC` (722,396) |
| Main header name field | `0x000008–0x000009`; ASCII `lk`, zero-padded to `0x000027` |
| Main header magic 2 | `0x000030–0x000033`; raw bytes `89 16 89 58` (matches Section 2.3's previously-recorded leading-byte capture) |
| Main `lk` payload extent | `0x000200–0x0B07DB`; 722,396 bytes; exactly matches header length field |
| Gap 1 | `0x0B07DC–0x0B07DF`; 4 bytes; independently verified byte-for-byte zero |
| cert1 GFH wrapper extent | `0x0B07E0–0x0B108C`; 2,221 bytes; length field `0x000006AD` (1,709); name `cert1` |
| cert1 DER payload | `0x0B09E0–0x0B108C`; 1,709 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap 2 | `0x0B108D–0x0B108F`; 3 bytes; independently verified byte-for-byte zero |
| cert2 GFH wrapper extent | `0x0B1090–0x0B164C`; 1,469 bytes; length field `0x000003BD` (957); name `cert2` |
| cert2 DER payload | `0x0B1290–0x0B164C`; 957 bytes; Binwalk-recognized X.509v3, header length 4 + sequence length 953 = 957 exact match |
| Gap 3 | `0x0B164D–0x0B164F`; 3 bytes; independently verified byte-for-byte zero |
| `lk_main_dtb` GFH header extent | `0x0B1650–0x0B184F`; 512 bytes; length field `0x00015DC9` (89,545); name `lk_main_dtb` |
| `lk_main_dtb` FDT payload | `0x0B1850–0x0C7618`; 89,545 bytes; Binwalk-recognized Flattened Device Tree, version 17, size 89,545 bytes — exact match to the container's declared length field |
| FDT size cross-reference | The 89,545-byte FDT payload size here is identical to the "Kernel-appended FDT" size already recorded in this project's boot-image geometry analysis for the `boot`/`recovery` partitions (Section 2 boot topology records); byte-identity between this FDT and those has not been tested this pass (source files not present in this session) — flagged as a size correspondence only, not a content-identity claim |
| Gap 4 | `0x0C7619–0x0C761F`; 7 bytes; independently verified byte-for-byte zero |
| cert1b GFH wrapper extent | `0x0C7620–0x0C7ECC`; 2,221 bytes; name `cert1`; DER payload `0x0C7820–0x0C7ECC` (1,709 bytes), Binwalk header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap 5 | `0x0C7ECD–0x0C7ECF`; 3 bytes; independently verified byte-for-byte zero |
| cert2b GFH wrapper extent | `0x0C7ED0–0x0C848C`; 1,469 bytes; name `cert2`; DER payload `0x0C80D0–0x0C848C` (957 bytes), Binwalk header length 4 + sequence length 953 = 957 exact match |
| Terminal zero-filled region | `0x0C848D–0x0FFFFF`; 228,211 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 722,396 + 4 + 2,221 + 3 + 1,469 + 3 + 512 + 89,545 + 7 + 2,221 + 3 + 1,469 + 228,211 = 1,048,576; exact, no unaccounted bytes |
| Confirmed genuine CRC32 lookup table | `0x07F9CC`; first 8 LE32 words exactly match the canonical IEEE 802.3 CRC-32 polynomial table (`0x00000000, 0x77073096, 0xEE0E612C, 0x990951BA, 0x076DC419, 0x706AF48F, 0xE963A535, 0x9E6495A3`); mathematically verified; Binwalk's label was structurally correct |
| Confirmed genuine SHA-256 round-constant table (copy 1) | `0x08BCB4`; first six LE32 words exactly match canonical SHA-256 constants `K[0]-K[5]`; mathematically verified |
| Confirmed genuine SHA-256 round-constant table (copy 2) | `0x08BF54`; full 256-byte table byte-for-byte identical to the copy at `0x08BCB4` |
| "Android bootimg"-labeled hit, individually verified | `0x05D8F8`; the literal 8-byte ASCII string `ANDROID!` genuinely exists at this offset as its own null-terminated string (extent `0x05D8F8–0x05D8FF`), immediately preceded by a separate null-terminated debug string `> boot image size = 0x%x\n` (extent `0x05D8DA–0x05D8F7`); Binwalk's boot-image-header parser incorrectly interpreted the bytes following the literal string as `boot_img_hdr` fields (reported kernel size 0, implausible ramdisk size, garbage "product name"), which is a confirmed false container interpretation — but the underlying `ANDROID!` string itself is genuine LK source code, consistent with a boot-image magic-comparison routine used when validating a loaded Android boot image; not a real embedded boot image container |
| Observed LK source/debug strings (unparsed, genuine) | `platform/mt6765/mt_disp_drv.c`, `platform/mt6765/platform.c`, `[GPIO] Failed to find /gpio@10005000 in dtb`, ` => FASTBOOT mode...`, `pmic_init`, `[FASTBOOT] reboot to boot loader`, `[pmic_init] LK Start..................`, `[pmic_init] MT6357 CHIP Code = 0x%x`, extensive `PMIC_RG_...`/`PMIC_AUXADC_...` register-name strings |
| Observed SoC-variant detection strings (unparsed, genuine) | `MT6765V/WB`, `MT6765T`, `MT6765V/CB`, `MT6765V/CA`, `MT6765`, `MT6765V/WA` |
| Observed build/mode strings (unparsed, genuine) | `eng build`, `[FACTORY]`, `%s Forbidden`, `%s Check  boot menu`; presence of an `eng build` string is a literal debug-log string found in the binary and is not by itself proof that this specific compiled image is an engineering (non-production) build — asserting that would be an unsupported inference; flagged as observed text only |
| ASCII strings (min length 6) | 7,740 total; semantic classes: GFH/cert/dtb names, CRC32/SHA-256 constant tables (confirmed genuine), extensive PMIC/fastboot/GPIO/platform source-path and debug strings, SoC-variant strings, `ANDROID!` magic-comparison string; remainder incidental byte-range coincidences inside compiled ARM code |
| UTF-16LE strings (min length 6) | 1 incidental match only; no genuine UTF-16LE text content |
| Content-region density map (64 KiB granularity) | `0x000000–0x08FFFF`: 81–93% nonzero (dense LK bootloader code and strings, includes CRC32/SHA-256 tables and `ANDROID!` string); `0x090000–0x0AFFFF`: 18–22% nonzero (sparser region, includes tail of `lk` payload and first cert pair); `0x0B0000–0x0BFFFF`: 49% nonzero (includes `lk_main_dtb` header and FDT payload start); `0x0C0000–0x0CFFFF`: 37% nonzero (FDT payload continuation and second cert pair); `0x0D0000–0x0FFFFF`: 0% nonzero (terminal zero-fill) |
| Internal field semantics (GFH length/secondary-field roles beyond confirmed values, FDT internal node/property decode, DER internal ASN.1 field decode/signature validation, LK machine-code control flow) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Same magic-pair structure (`88 16 88 58` / `89 16 89 58`) as `p18 spmfw`, `p19 scp1`/`p20 scp2`, `p21 sspm_1`/`p22 sspm_2`, and this project's own Section 2.3 record of the `lk` partition's leading bytes (`88 16 88 58`, ASCII `lk` at offset `0x08`) — this record confirms and extends that prior observation to full structural closure |
| Existing database cross-reference | Section 2.6 currently lists `p26 lk` at summary depth only ("Little Kernel bootloader slot 1"), cross-referenced to `p27 lk2` as SHA256-identical; Section 2.3 previously recorded the leading-byte GPT capability surface (static strings for GPT parse/write); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/bootloader`, ROM action `NEVER TOUCH`; this record supersedes both the Section 2.3 partial capture and the Section 2.6 summary entry with full structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_LK_BOOTLOADER_GFH_CONTAINER_WITH_MAIN_IMAGE_DEVICE_TREE_AND_FOUR_X509_SIGNING_CERTIFICATES_PLUS_CONFIRMED_CRC32_AND_SHA256_CONSTANT_TABLES` |

## p27 — `lk2`
| p27 | `lk2` | 1,048,576 | Byte-for-byte identical to `p26 lk` (independently confirmed via full-file `cmp`, not assumed): MTK GFH-style dual-image bootloader container — 512-byte header (`lk`, declared 722,396-byte payload) containing the compiled Little Kernel bootloader with its own GFH-wrapped cert1/cert2 X.509v3 pair; followed by a 512-byte `lk_main_dtb` header (declared 89,545-byte FDT payload, version 17) with its own GFH-wrapped cert1/cert2 X.509v3 pair; remainder zero-filled | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `3860c8857554284a03cc844b7c8435c873355a7f175a36ae96d94890c702aa41` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/lk2.bin` |
| Content identity verification | Full-file `cmp` against `lk.bin` performed this pass, not assumed from naming convention: zero byte differences across all 1,048,576 bytes; SHA256 also matches exactly |
| Prior database cross-check | Full SHA256 matches existing Section 2.6 `p27 lk2` entry and Section 2.5/2.7 size exactly; no contradiction |
| GPT partition | p27; `lk2` |
| Physical block device | `/dev/block/mmcblk0p27`; major:minor `179:27` |
| By-name mapping | `/dev/block/by-name/lk2` -> `/dev/block/mmcblk0p27` |
| GPT start LBA | 931,840 |
| GPT end LBA | 933,887 inclusive |
| GPT sector count | 2,048 |
| GPT unique partition GUID | `8273e1ab-846f-4468-b999-ee2ea8e50a16` |
| Object size | 1,048,576 bytes; `0x00100000`; 1 MiB |
| Recognized filesystem | NONE |
| Recognized container | NONE at outer level; same internal structures as `p26 lk` at identical offsets (see below) |
| Full-partition nonzero-byte count | 596,552 (identical to p26) |
| Full-partition zero-byte count | 452,024 (identical to p26) |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x0C848C` |
| Main `lk` GFH header extent | `0x000000–0x0001FF`; 512 bytes; magic1 `88 16 88 58`, length field `0x000B05DC` (722,396), name `lk`, magic2 `89 16 89 58` |
| Main `lk` payload extent | `0x000200–0x0B07DB`; 722,396 bytes; exactly matches header length field |
| Gap 1 | `0x0B07DC–0x0B07DF`; 4 bytes; independently verified byte-for-byte zero |
| cert1 GFH wrapper extent | `0x0B07E0–0x0B108C`; 2,221 bytes; length field `0x000006AD` (1,709); name `cert1`; DER payload `0x0B09E0–0x0B108C` (1,709 bytes), Binwalk header length 4 + sequence length 1,705 = 1,709 exact match |
| Gap 2 | `0x0B108D–0x0B108F`; 3 bytes; independently verified byte-for-byte zero |
| cert2 GFH wrapper extent | `0x0B1090–0x0B164C`; 1,469 bytes; length field `0x000003BD` (957); name `cert2`; DER payload `0x0B1290–0x0B164C` (957 bytes), Binwalk header length 4 + sequence length 953 = 957 exact match |
| Gap 3 | `0x0B164D–0x0B164F`; 3 bytes; independently verified byte-for-byte zero |
| `lk_main_dtb` GFH header extent | `0x0B1650–0x0B184F`; 512 bytes; length field `0x00015DC9` (89,545); name `lk_main_dtb` |
| `lk_main_dtb` FDT payload | `0x0B1850–0x0C7618`; 89,545 bytes; Binwalk-recognized Flattened Device Tree, version 17 |
| Gap 4 | `0x0C7619–0x0C761F`; 7 bytes; independently verified byte-for-byte zero |
| cert1b GFH wrapper extent | `0x0C7620–0x0C7ECC`; 2,221 bytes; name `cert1`; DER payload `0x0C7820–0x0C7ECC` (1,709 bytes) |
| Gap 5 | `0x0C7ECD–0x0C7ECF`; 3 bytes; independently verified byte-for-byte zero |
| cert2b GFH wrapper extent | `0x0C7ED0–0x0C848C`; 1,469 bytes; name `cert2`; DER payload `0x0C80D0–0x0C848C` (957 bytes) |
| Terminal zero-filled region | `0x0C848D–0x0FFFFF`; 228,211 bytes; independently verified byte-for-byte zero |
| Byte-accounting closure | 512 + 722,396 + 4 + 2,221 + 3 + 1,469 + 3 + 512 + 89,545 + 7 + 2,221 + 3 + 1,469 + 228,211 = 1,048,576; exact, no unaccounted bytes |
| Confirmed genuine CRC32 lookup table | `0x07F9CC`; exact match to canonical IEEE 802.3 CRC-32 polynomial table (same as p26) |
| Confirmed genuine SHA-256 round-constant table (copy 1) | `0x08BCB4`; exact match to canonical SHA-256 `K[0]-K[5]` (same as p26) |
| Confirmed genuine SHA-256 round-constant table (copy 2) | `0x08BF54`; byte-identical to copy 1 (same as p26) |
| "Android bootimg"-labeled hit, individually verified | `0x05D8F8`; same as p26: genuine standalone `ANDROID!` string used by LK's own boot-image magic-check code; Binwalk's container interpretation is a confirmed false positive, the string itself is genuine |
| Observed LK source/debug strings (unparsed, genuine) | Same as p26: `platform/mt6765/mt_disp_drv.c`, `platform/mt6765/platform.c`, PMIC/MT6357/fastboot/GPIO strings, SoC-variant strings (`MT6765V/WB`, `MT6765T`, etc.), `eng build`/`[FACTORY]` mode strings |
| ASCII strings (min length 6) | 7,740 total (identical to p26); same semantic classes |
| UTF-16LE strings (min length 6) | 1 incidental match only; no genuine UTF-16LE text content |
| Internal field semantics (GFH length/secondary-field roles beyond confirmed values, FDT internal node/property decode, DER internal ASN.1 field decode/signature validation, LK machine-code control flow) | `OPEN_BOUNDARY` |
| Cross-reference to existing project GFH pattern | Same magic-pair structure (`88 16 88 58` / `89 16 89 58`) as `p18 spmfw`, `p19 scp1`/`p20 scp2`, `p21 sspm_1`/`p22 sspm_2`, `p26 lk`, and Section 2.3's leading-byte record |
| Existing database cross-reference | Section 2.6 currently lists `p27 lk2` at summary depth only ("Little Kernel bootloader slot 2", identical to lk by SHA256); Section 2.5 confirms geometry; Section 2.7 lists fstab alias `/bootloader2`, ROM action `NEVER TOUCH`; this record supersedes the summary-only entry with full structural closure, inherited from the independently-verified p26 analysis via confirmed byte-for-byte file identity |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_LK_BOOTLOADER_GFH_CONTAINER_WITH_MAIN_IMAGE_DEVICE_TREE_AND_FOUR_X509_SIGNING_CERTIFICATES_PLUS_CONFIRMED_CRC32_AND_SHA256_CONSTANT_TABLES` |

## p28 — `boot`
| p28 | `boot` | 33,554,432 | Android boot image v2 containing a gzip-compressed AArch64 Linux 4.9.190 kernel with appended FDT, a Magisk 30.6-patched gzip/newc ramdisk with a recoverable backed-up Android `init`, a second byte-identical FDT in an Android DT table, a cryptographically self-valid embedded VBMeta object whose signed boot hash descriptor does not match the current patched payload, exact zero padding, and an AVB footer | `CURRENT_RESEARCH_CAPTURE`; owner-research Magisk modification directly confirmed; stored pre-patch `SHA1=` target and device-wide AVB enforcement outside this evidence object `OPEN_BOUNDARY` | `CLOSED` | `fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851` |

| Field | Verified value |
|---|---|
| Evidence object | `boot(1).bin` |
| Object size | 33,554,432 bytes; `0x02000000` |
| Acquisition completeness | Expected from GPT: 33,554,432 bytes; received: 33,554,432 bytes; difference: 0 |
| Partition identity | p28; `boot`; `/dev/block/mmcblk0p28`; major:minor `179:28` |
| GPT geometry | Start LBA 933,888; end LBA 999,423 inclusive; 65,536 sectors |
| GPT byte geometry | Start byte `0x1C800000`; end byte inclusive `0x1E7FFFFF`; end byte exclusive `0x1E800000` |
| GPT unique GUID | `d26472f1-9ebc-421d-ba14-311296457c90` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0x0000000000000000` |
| Device-node metadata | Mode `brw-------`; owner/group `root:root`; raw partition-node context `u:object_r:block_device:s0`; by-name `boot` context and policy-expected context `u:object_r:boot_block_device:s0` |
| Aliases | By-name alias `boot`; platform alias `/dev/block/platform/bootdevice/mmcblk0p28`; fstab alias `/boot`, type `emmc`, options `first_stage_mount,nofail` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: Android boot image with kernel address `0x40080000`, ramdisk address `0x51B00000`, 2,048-byte page size, and recorded cmdline; `blkid -p`: no recognized filesystem signature, status 2 |
| Primary container | Android boot image magic `ANDROID!`; header version 2 |
| Structural-signature census | `ANDROID!` occurs exactly at `0x00000000`; Android DT-table magic `D7 B7 AB 1E` exactly at `0x009B3800`; FDT magic `D0 0D FE ED` exactly at `0x008BBFB9` and `0x009B3840`; `AVB0` exactly at `0x009CA000`; `AVBf` exactly at `0x01FFFFC0` |
| Gzip-signature handling | Three raw `1F 8B 08` occurrences exist: table-defined streams at `0x00000800` and `0x008D2000`, plus one incidental occurrence at `0x005BF5E6` inside compressed kernel data; only the two boundary-defined streams are promoted to objects |
| Complete top-level layout | Header page `0x00000000–0x000007FF`; kernel gzip stream `0x00000800–0x008BBFB8`; kernel-appended FDT `0x008BBFB9–0x008D1D81`; kernel padding `0x008D1D82–0x008D1FFF`; ramdisk gzip `0x008D2000–0x009B37CC`; ramdisk padding `0x009B37CD–0x009B37FF`; DT table `0x009B3800–0x009C9608`; DT padding `0x009C9609–0x009C97FF`; pre-VBMeta zeros `0x009C9800–0x009C9FFF`; embedded VBMeta `0x009CA000–0x009CA5BF`; post-VBMeta zeros `0x009CA5C0–0x01FFFFBF`; AVB footer `0x01FFFFC0–0x01FFFFFF` |
| Structural extent ledger | 2,048 + 9,156,537 + 89,545 + 638 + 923,597 + 51 + 89,609 + 503 + 2,048 + 1,472 + 23,288,320 + 64 = 33,554,432 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| Header-page extent | `0x00000000–0x000007FF`; 2,048 bytes; 93 nonzero and 1,955 zero bytes; SHA-256 `0a2d1479f5dac642ebe3104b9fcf04262e86c72143fd61bc4ac2f738f6c3c7bb` |
| Header size | Structured header size 1,660 bytes; allocation/page size 2,048 bytes |
| Header component sizes | Kernel 9,246,082 bytes; ramdisk 923,597 bytes; second stage 0 bytes; recovery-DTBO 0 bytes; DTB table 89,609 bytes |
| Header load addresses | Kernel `0x40080000`; ramdisk `0x51B00000`; second stage `0x40F00000`; tags `0x47880000`; DTB `0x47880000` |
| Derived build constants | Kernel base `0x40000000`; kernel offset `0x00080000`; ramdisk offset `0x11B00000`; tags/DTB offset `0x07880000`; page size `0x800` |
| Header OS field | Raw `0x14000183`; decoded Android `10.0.0`; decoded security patch `2024-03` |
| Header name | Empty, all 16 name bytes zero |
| Kernel command line | `bootopt=64S3,32N2,64N2 buildvariant=user` |
| Header image-ID field | First 20 bytes `c6ccf000f6513dafd9a2018c87d799922348887c`; remaining 12 bytes zero |
| Header image-ID verification | SHA-1 over the v2 ordered component sequence—kernel bytes plus LE32 size, ramdisk bytes plus LE32 size, zero-length second stage plus zero size, zero-length recovery-DTBO plus zero size, and DTB-table bytes plus LE32 size—recomputes exactly to `c6ccf000f6513dafd9a2018c87d799922348887c` |
| Header image-ID attribution | The field authenticates the components in this current Magisk-patched image and is not a stock-image identifier; the current patched ramdisk participates directly in the verified digest |
| Kernel component extent | `0x00000800–0x008D1D81`; header-declared 9,246,082 bytes; comprises a 9,156,537-byte gzip stream followed immediately by an 89,545-byte FDT |
| Kernel gzip stream | `0x00000800–0x008BBFB8`; gzip header has MTIME 0, XFL 2, OS 3; decompressor reaches exact EOF after 9,156,537 bytes and leaves exactly the appended FDT as unused input |
| Kernel gzip SHA-256 | `dadfdd23fcd2897f9602aa3def8f381cf0e323424720cc193764ba48f2b7242e` |
| Decompressed kernel | 25,868,308 bytes; SHA-256 `ba8bdc71e1ef8936772529ac91d73448883d2e7b8d7fb7562363bbbabf4f116b` |
| AArch64 Image header | Magic `ARMd`; text offset `0x00080000`; declared in-memory image size `0x01EB6000` = 32,202,752 bytes; flags `0xA` |
| Kernel version | `Linux version 4.9.190+`; build identity `nobody@android-build`; `#1 SMP PREEMPT Fri Jun 13 07:31:05 UTC 2025` |
| Kernel compiler | Android clang 9.0.3, build 5484270 based on `r353983c`; embedded LLVM/toolchain commit identifiers retained in the kernel version string |
| Kernel factory-relative boundary | No separate stock kernel evidence object was supplied for this comparison; byte identity or modification state relative to factory kernel bytes remains `OPEN_BOUNDARY` |
| Kernel-appended FDT | `0x008BBFB9–0x008D1D81`; exactly 89,545 bytes; version 17; SHA-256 `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| Kernel-component SHA-256 | Header-declared combined gzip-plus-FDT component: `e76555df4ce2dbc5dea2113468548f1597c49d1a5871d0d38dea199477d19522` |
| Kernel padding | `0x008D1D82–0x008D1FFF`; 638 zero bytes; independently verified |
| Ramdisk compressed extent | `0x008D2000–0x009B37CC`; 923,597 bytes; gzip stream consumes the declared extent exactly, reaches EOF, and has no unused or unconsumed bytes |
| Ramdisk compressed SHA-256 | `0a14137bcfff4de968f5c4ac09c04c55ccc97dc70b3e10421f4a10dfb74911ad` |
| Ramdisk decoded archive | ASCII-hex `070701` newc CPIO; exactly 1,054,624 bytes; 2,060 512-byte blocks after ceiling-rounding; SHA-256 `772a762bc3bb2ae302642fa6be64408a5ae0133dcd5a38ea842ed792f8d3fc59` |
| CPIO closure | 22 real members plus one `TRAILER!!!` record; 12 files and 10 directories; all names unique; parser ends exactly at decoded byte 1,054,624 with zero trailing bytes |
| CPIO ownership/timestamps | Every member has UID 0, GID 0, link count 1, and mtime 0 |
| CPIO logical-byte reconciliation | Twelve file payloads total 1,051,752 bytes; newc headers, names, alignment, and trailer total 2,872 bytes; sum equals the 1,054,624-byte decoded archive |
| Complete directory inventory | `.backup` mode `0000`; `apex`, `avb`, `debug_ramdisk`, `dev`, `mnt`, `proc`, and `sys` mode `0755`; `overlay.d` and `overlay.d/sbin` mode `0750` |
| Complete file inventory, part 1 | `.backup/.magisk` mode `0000`, 143 bytes; `.backup/.rmlist` mode `0000`, 99; `.backup/init.xz` mode `0750`, 597,432; `avb/q-gsi.avbpubkey` mode `0644`, 1,032; `avb/r-gsi.avbpubkey` mode `0644`, 1,032; `avb/s-gsi.avbpubkey` mode `0644`, 1,032 |
| Complete file inventory, part 2 | `fstab.mt6762` mode `0640`, 4,169 bytes; `fstab.mt6765` mode `0640`, 4,169; `init` mode `0750`, 254,584; `overlay.d/sbin/init-ld.xz` mode `0644`, 1,556; `overlay.d/sbin/magisk.xz` mode `0644`, 160,216; `overlay.d/sbin/stub.xz` mode `0644`, 26,288 |
| File SHA-256, part 1 | `.backup/.magisk=5199403dd8387697cfa7fcea85cb8bdf7fd3450a3e1c5ee422715b8a0927244a; .backup/.rmlist=8a752f56beefef45991ca8d719ccf3f1e10a924f9e31424cd1f5cc577a0458f7; .backup/init.xz=a9a14d513494bfe2f2f3460f6ee062a91bc4c194b4954163a8399b3ea374d4f4; q-gsi.avbpubkey=07b37325316fd483765d474aafcaca908b4de4773e16ed87aed415c15f71fb98; r-gsi.avbpubkey=4706b8d2ffd46c5759d9431b8bf95f5c0544f4f6c87bbe35ff591325eb1783f5; s-gsi.avbpubkey=ec60e13d92331e2c2d5d1372a5d7f2131861dc602815fd87c0637d14790b4538` |
| File SHA-256, part 2 | `fstab.mt6762=6d033c2a7debc697b036874e552c05530ab26d45891a19200cb854efb2104055; fstab.mt6765=6d033c2a7debc697b036874e552c05530ab26d45891a19200cb854efb2104055; init=a3ce9397456cbe260e9d918eb0d62a7d3b4553dd14082538e7bfe19da0326652; init-ld.xz=acb5c3258521752f20626a8a1c8ddfaaa7b3b13d66c9736c85d40cd32365dd07; magisk.xz=0c7367c3ce5474b53e47cb478673e8f2573e03923bf3f53cbd67eeaef33bc2e4; stub.xz=4459fbca8480d3d74f0213f82df0a0df3f810e8469c6e22ad1037a2b8197add5` |
| Magisk configuration | `.backup/.magisk` contains `KEEPVERITY=true`, `KEEPFORCEENCRYPT=true`, `RECOVERYMODE=false`, `VENDORBOOT=false`, `PREINITDEVICE=userdata`, and `SHA1=27cf4756457d154fadbc89c1e7c037e57de646a0` |
| Magisk removal list | `.backup/.rmlist` is a NUL-delimited list containing `overlay.d`, `overlay.d/sbin`, `overlay.d/sbin/init-ld.xz`, `overlay.d/sbin/magisk.xz`, and `overlay.d/sbin/stub.xz` |
| Active `init` classification | 254,584-byte static AArch64 ELF64 ET_EXEC with no PT_INTERP or PT_DYNAMIC; GNU build ID `9667d0b3b218f2f31bcea112e91234fd494ad81d`; direct strings include `magiskinit`, Magisk SELinux paths, and injected `--post-fs-data`, `--service`, `--zygote-restart`, and `--boot-complete` actions |
| Backed-up Android `init` | `.backup/init.xz` is a complete CRC32-checked XZ stream; it decompresses to a 1,842,080-byte static AArch64 ELF64 ET_EXEC with no PT_INTERP or PT_DYNAMIC; SHA-256 `57afd29669c0f481a942732f503579dba1eef27539906e70919af6bdca6ad906`; GNU build ID `4f7df3718d0ac0f9915af6fc844a7a5b`; Android-init and first-stage-mount strings directly confirmed |
| `init-ld.xz` payload | Complete CRC32-checked XZ stream; decompresses to 5,208-byte AArch64 ELF64 ET_DYN; SHA-256 `54ec98f3f93473e51252267a8997cd12878dd463263f3dc2a32c14ab7951f9f0`; GNU build ID `48bc1d2b2a7c69762e96639e72233259ea3a477f` |
| `magisk.xz` payload | Complete CRC32-checked XZ stream; decompresses to 400,568-byte AArch64 ELF64 ET_DYN using `/system/bin/linker64`; SHA-256 `70558e6d6199fa5a961b7bafeb8f96d8157cc63810db8df2a3cded1881763697`; GNU build ID `5bf2cb770123f6eda022f37298b35bd428341008` |
| `stub.xz` payload | Complete CRC32-checked XZ stream; decompresses to a 33,149-byte ZIP/APK; SHA-256 `5a6fc51f1f74647e0b31b50a7e8694f2c73e82edf45355c1d60836667752f2e3` |
| Magisk version proof | Stub APK ZIP comment is exactly `version=30.6`, `versionCode=30600`, `stubVersion=40`; the Magisk executable independently contains `30.6:MAGISKSU` and `30.6:MAGISK:R` markers |
| Version disambiguation | Embedded `21.0.0` strings identify the LLD/Clang compiler toolchain and do not identify the Magisk release; the payload's directly encoded Magisk version is 30.6 |
| Stub APK inventory | Five ZIP members: `AndroidManifest.xml` 7,344 bytes; `META-INF/CERT.RSA` 1,184; `META-INF/CERT.SF` 266; `META-INF/MANIFEST.MF` 161; `classes.dex` 36,176; all CRC checks pass |
| Stub APK identity/signing | Binary manifest contains package identifier `com.topjohnwu.magisk`; one PKCS#7 signing certificate, self-issued `CN=John Wu,L=Taipei,C=TW`, serial `0x50514879`, SHA-256 fingerprint `b4cb83b4dad99f997dbe872f013aa16c14eec41d167021f371f7e1330f273ee6` |
| Stored SHA-1 namespace separation | Current whole-partition SHA-1 `d29b72f5eaf7950f9bca2b758fe279863c5ac411`, verified current v2 header component-ID `c6ccf000f6513dafd9a2018c87d799922348887c`, and `.backup/.magisk` value `27cf4756457d154fadbc89c1e7c037e57de646a0` are three distinct values and must not be conflated |
| Stored Magisk SHA-1 boundary | The object hashed by `.backup/.magisk`'s `SHA1=` field cannot be independently reproduced without the pre-patch boot evidence object or the exact producing tool state; its target identity remains `OPEN_BOUNDARY` |
| Fstab relationship | `fstab.mt6762` and `fstab.mt6765` are byte-for-byte identical; both identify source template `fstab.in.mt6765` and contain the same 4,169 bytes |
| Fstab boot-relevant content | `system`, `vendor`, and `product` are logical first-stage ext4 mounts with AVB controls; `/metadata` maps to `md_udc`; `/data` uses `checkpoint=block` and `fileencryption=aes-256-xts`; boot, recovery, vbmeta, DTBO, firmware, calibration, and security partitions are declared by-name |
| GSI AVB public keys | q, r, and s key files each reconcile as an 8-byte AVB key header plus 4096-bit modulus and 4096-bit Montgomery-RR value; all three keys are distinct |
| GSI key modulus SHA-256 | `q=460403f1540ab23025e5acbd04d6f858aa160979d428fb4f35c1496137d8e8ec; r=6bd793f8c026fbef5b66fedf1cfcb9ab9e9b4e3d6c4aa5ff26d71b78b6eeb655; s=cd16d6f1ce2059539fd3b0900605da015d28c897a7bf055e79615a0388138ebf` |
| Ramdisk padding | `0x009B37CD–0x009B37FF`; 51 zero bytes; independently verified |
| DT-table extent | `0x009B3800–0x009C9608`; exactly 89,609 bytes; SHA-256 `41115a867807ddabf54707acc5b16d8c93534026533bb109c4faedca4aa4a3af` |
| DT-table header | Big-endian magic `0xD7B7AB1E`; total size `0x15E09` = 89,609; header size 32; entry size 32; entry count 1; entries offset 32; page size 2,048; version 0 |
| DT-table entry 0 | DT size `0x15DC9` = 89,545; DT offset `0x40`; ID 0; revision 0; all four custom fields 0; entry end equals table end exactly |
| FDT header | Magic `0xD00DFEED`; total size `0x15DC9` = 89,545; version 17; last compatible version 16; structure offset `0x38`; strings offset `0x13488`; memory-reservation offset `0x28`; boot CPU ID 0 |
| FDT internal reconciliation | Structure size `0x13450` = 78,928; strings size `0x2941` = 10,561; all declared blocks remain inside the 89,545-byte FDT; reservation map contains only its zero terminator; structure reaches its `FDT_END` token exactly |
| FDT population | 504 nodes; 2,379 properties; zero NOP tokens; maximum parsed depth 4 |
| FDT identity | Root `model=MT6765`; root `compatible=mediatek,MT6765`; root address and size cell counts both 2 |
| FDT chosen bootargs | `console=tty0 console=ttyS0,921600n1 vmalloc=400M slub_debug=OFZPU page_owner=on swiotlb=noforce androidboot.hardware=mt6765 maxcpus=8 loop.max_part=7 firmware_class.path=/vendor/firmware` |
| In-image FDT equality | Kernel-appended FDT and DT-table entry 0 are byte-for-byte identical and independently produce SHA-256 `16249ee8f473d94c13222256bad4299b5194759e6b0a6b33ecf7104ac90dc0b6` |
| Cross-partition FDT boundary | Equality to an FDT in `lk` is not entered as a direct p28 finding because the `lk` evidence object was not part of this analysis set; cross-partition byte comparison remains `OPEN_BOUNDARY` here |
| DT padding | `0x009C9609–0x009C97FF`; 503 zero bytes; independently verified |
| Footer original-image extent | `0x00000000–0x009C97FF`; 10,262,528 bytes; exactly the page-aligned end of the DT-table component; SHA-256 `3b6ff0a39be25e7f7c18996e52e3c600e34d7c2bc740d4d6988370b73ce4a526` |
| Pre-VBMeta zero gap | `0x009C9800–0x009C9FFF`; 2,048 zero bytes; independently verified |
| Embedded VBMeta extent | `0x009CA000–0x009CA5BF`; 1,472 bytes; SHA-256 `2e2ec8c3939f75182f4084730d1a364827af22dccbb3d868cb0024ae16397a5d` |
| VBMeta header geometry | 256-byte `AVB0` header; required libavb 1.0; 320-byte authentication block; 896-byte auxiliary block; exact total `256 + 320 + 896 = 1,472` |
| VBMeta algorithm/state | Algorithm type 1, `SHA256_RSA2048`; rollback index 0; flags 0; rollback-index location 0; release string `avbtool 1.1.0` |
| VBMeta auxiliary geometry | Descriptor region offset 0, size 360; public key offset 360, size 520; public-key metadata offset 880, size 0; all auxiliary extents reconcile exactly |
| VBMeta authentication geometry | SHA-256 field offset 0, size 32; RSA signature offset 32, size 256; remaining 32 authentication-block bytes zero |
| VBMeta authentication hash | Stored `bc3bbf9d4d89559bff7fd999a973acfefa76fb994f0784bf81367d787a70337f` exactly equals SHA-256 of the 256-byte VBMeta header concatenated with the 896-byte auxiliary block |
| VBMeta public key | AVB-encoded RSA 2,048-bit key; exponent 65,537; modulus SHA-256 `ef57169073f7c615d9d4a01b7554f1565bbecf69cd2606cb7f649163ccda3468`; reconstructed SPKI SHA-256 `572717aa57a7f811d388e9130bd8f911f757e09e8eb74ebc156f9097a524549c` |
| VBMeta RSA verification | The 256-byte signature verifies exactly as PKCS#1 v1.5 with SHA-256 under the embedded RSA public key; the embedded VBMeta metadata object is internally cryptographically valid |
| Descriptor inventory | Three descriptors occupy exactly 360 bytes: one 200-byte hash descriptor for partition `boot`; one 72-byte property descriptor; one 88-byte property descriptor |
| Boot hash descriptor | Algorithm `sha256`; partition `boot`; image size `0x009AA800` = 10,135,552 bytes; 32-byte salt `e72556c30e53cd12e555f26883ebf923790b28e54cebd8114110b7d9a3cce1b0`; flags 0 |
| Stored boot digest | `70067c1a25dd5b13d46b2f80eaad0a59319d1c0fc8e3a85233dde522dd20ecdd` |
| Current covered-byte digest | SHA-256 of the descriptor salt followed by current bytes `0x00000000–0x009AA7FF` is `2710688ef8745b475ff3930176a3814d4bb8ebb4da5dc5fb5b2b42c9c60c5769`; it does not equal the stored digest |
| Descriptor coverage mismatch | Descriptor image size is 126,976 bytes smaller than the footer original-image size and ends 36,813 bytes before the current compressed ramdisk ends; the final 36,813 ramdisk bytes, 51-byte ramdisk pad, 89,609-byte DT table, and 503-byte DT pad are outside its signed hash scope |
| AVB integrity conclusion | VBMeta's own authentication hash and RSA signature are valid, but its signed `boot` hash descriptor rejects the bytes in this current Magisk-patched boot image; metadata authenticity and payload-hash validity are separate and must not be conflated |
| VBMeta property 1 | `com.android.build.boot.os_version=10` |
| VBMeta property 2 | `com.android.build.boot.security_patch=2019-06-06` |
| Version-layer mismatch | Header patch level is `2024-03`; preserved signed VBMeta property is `2019-06-06`; both are recorded as distinct layer values without normalizing either one |
| AVB enforcement boundary | This boot-local VBMeta header has flags 0. Device-wide disable flags recorded for the separate p34 `vbmeta` partition are not bytes in this p28 object; final boot-chain enforcement behavior therefore requires that separate witness and remains outside the direct p28 content claim |
| Post-VBMeta zero span | `0x009CA5C0–0x01FFFFBF`; 23,288,320 zero bytes; SHA-256 `bb34bb62aad5a0c16e28e37f79b314c5c85b15ae00895e722c7ae297986e33ca`; independently verified |
| AVB footer | `0x01FFFFC0–0x01FFFFFF`; 64 bytes; magic `AVBf`; version 1.0; reserved 28 bytes all zero; SHA-256 `a89ebf879001b529f74403525f6de65499cb2212d897f4cbbe06f6a808bd3c88` |
| Footer fields | Original image size `0x009C9800` = 10,262,528; embedded VBMeta offset `0x009CA000`; embedded VBMeta size `0x5C0` = 1,472; every field reconciles to the observed extents |
| Capture-state classification | `CURRENT_RESEARCH_CAPTURE`; Magisk modification is directly proven by ramdisk bytes; this is not classified as a stock boot partition |
| First nonzero byte | `0x00000000` |
| Last nonzero byte | `0x01FFFFE3`; the footer's final 28 reserved bytes are zero |
| Partition nonzero-byte count | 10,149,412 |
| Partition zero-byte count | 23,405,020 |
| Partition `0xFF` count | 64,311 |
| Byte-alphabet coverage | All 256 possible byte values occur |
| Populated 4 KiB blocks | 2,508 blocks in two runs: `0x00000000–0x009CAFFF` and `0x01FFF000–0x01FFFFFF` |
| ASCII strings | 106,578 occurrences at minimum length 4; 103,931 unique strings; length range 4–187 bytes; most occurrences are compressed-data coincidences |
| UTF-16LE strings | One minimum-length-4 occurrence at `0x0089A0B0`, eleven characters consisting of `+` and spaces; classified as an incidental compressed-data coincidence |
| Independent zero verification | Kernel padding, ramdisk padding, DT padding, the 2,048-byte pre-VBMeta gap, and the 23,288,320-byte post-VBMeta span each passed direct byte-count assertions and independent `cmp` comparison against `/dev/zero` |
| Closure assertions | Exact header/component math, both gzip EOF states, CPIO end/trailer, all four XZ EOF/check states, stub ZIP inventory/comment, duplicate-FDT equality, v2 header-ID recomputation, all zero extents, footer geometry, VBMeta authentication hash, RSA signature, and current payload-hash mismatch passed one deterministic assertion suite |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851` |
| Content classification | `ANDROID_BOOT_V2_WITH_MAGISK_30_6_RAMDISK_DUPLICATE_FDT_AND_SELF_VALID_BUT_PAYLOAD_MISMATCHED_AVB_METADATA` |

## p29 — `logo`
| p29 | `logo` | 8,388,608 | Structurally complete MediaTek GFH-wrapped logo package containing a 42-entry zlib-compressed RGBX8888 raster archive, 20 full-screen 480×640 assets, 22 glyph/strip assets, a directly verified two-stage RSA-PSS certificate relationship binding the GFH header and aligned archive payload, and a fully zero-filled terminal region | `CURRENT_RESEARCH_CAPTURE`; undocumented runtime symbolic asset names and proprietary non-hash GFH/certificate-field semantics `OPEN_BOUNDARY` | `CLOSED` | `3d00b415d1d8cad27abe92176ff01ab225dca4154b6422794aa152921ffe7d0a` |

| Field | Verified value |
|---|---|
| Evidence object | `logo.bin` |
| Object size | 8,388,608 bytes; `0x00800000` |
| Acquisition completeness | Expected from GPT: 8,388,608 bytes; received: 8,388,608 bytes; difference: 0 |
| Partition identity | p29; `logo`; `/dev/block/mmcblk0p29`; major:minor `179:29` |
| GPT geometry | Start LBA 999,424; end LBA 1,015,807 inclusive; 16,384 sectors |
| GPT unique GUID | `b72ccbe9-2055-46f4-a167-4a069c201738` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0` |
| Device-node metadata | `brw-rw----`; owner/group `root:system`; both supplied SELinux-context columns `u:object_r:block_device:s0` |
| SELinux policy comparison | Supplied/observed context: `u:object_r:block_device:s0`; policy-specific expected context: `u:object_r:logo_block_device:s0`; discrepancy preserved as evidence and not silently normalized |
| Aliases | By-name alias `logo`; fstab alias `/logo`; fstab type `emmc` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Standard-signature scan | No PNG, JPEG, GIF, RIFF/WEBP, ELF, ZIP, Android boot, AVB, FDT, DTBO, Android sparse, GPT, or recognized filesystem outer signature established |
| Incidental-signature handling | Short `BM`, `53 EF`, and `1F 8B` byte pairs occur inside compressed material but not at table-defined object boundaries; none was promoted to an embedded object. Of 74 `78 DA` occurrences, exactly 42 are table-defined zlib starts and 32 are internal compressed-data coincidences |
| MediaTek GFH signature census | Primary magic `88 16 88 58` occurs exactly at `0x000000`, `0x2DE250`, and `0x2DEB00`; secondary magic `89 16 89 58` occurs exactly at `0x000030`, `0x2DE280`, and `0x2DEB30` |
| Complete top-level layout | Logo GFH header `0x000000–0x0001FF`; archive table `0x000200–0x0002AF`; 42 zlib streams `0x0002B0–0x2DE246`; logo-alignment zeros `0x2DE247–0x2DE24F`; cert1 GFH header `0x2DE250–0x2DE44F`; cert1 DER `0x2DE450–0x2DEAFC`; cert1-alignment zeros `0x2DEAFD–0x2DEAFF`; cert2 GFH header `0x2DEB00–0x2DECFF`; cert2 DER `0x2DED00–0x2DF0BC`; terminal zeros `0x2DF0BD–0x7FFFFF` |
| Structural extent ledger | 512 + 176 + 3,006,359 + 9 + 512 + 1,709 + 3 + 512 + 957 + 5,377,859 = 8,388,608 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| Logo GFH header | `0x000000–0x0001FF`; 512 bytes; name `logo`; primary magic bytes `88 16 88 58`; declared payload `0x002DE047` = 3,006,535 bytes |
| Logo GFH control words | LE32 at header `+0x28` = `0xFFFFFFFF`; LE32 at `+0x2C` = `0xFFFFFFFF` |
| Logo GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 00000000 00000000 00000010 00000000 00000000` |
| Logo GFH reconciliation | The declared 3,006,535-byte payload begins at `0x000200` and equals the 176-byte archive table plus 3,006,359 bytes of zlib stream data; the next nine bytes are alignment zeros outside the declared payload |
| Logo GFH header SHA-256 | `132f38845606bad06a1ff2545a0dc87d5653c76e1931d0af580d9c34603b562b` |
| Archive table extent | `0x000200–0x0002AF`; exactly 176 bytes |
| Archive table header | LE32 asset count `42`; LE32 payload length `0x002DE047` = 3,006,535; followed by 42 LE32 stream offsets |
| Archive table geometry | `8 + (42 × 4) = 176` bytes; remainder 0; first stream offset `0x000000B0` begins exactly after the table |
| Offset-table validation | All 42 offsets are unique and strictly increasing; every absolute stream start equals `0x000200 + table_offset`; final offset is `0x002C3439` |
| Archive terminal reconciliation | Stream 41 ends at `0x2DE246`, exactly `0x000200 + 0x002DE047 - 1`; there are no gaps or overlaps in the table-defined stream region |
| Archive table SHA-256 | `74689b7c2064a3898669f471811e71c1b253cacede528e502a35c799aee25005` |
| Zlib stream validation | All 42 streams begin `78 DA`; each reaches zlib end-of-stream exactly at the next table boundary or declared archive end; every compressed span is consumed completely with zero unused or unconsumed bytes; all 42 Adler-32 checks pass |
| Compressed aggregate | `0x0002B0–0x2DE246`; 3,006,359 bytes; SHA-256 `6f0db81b6a2b75dfb7aadbba5b496f22cbd41c96ee81feaf5045deff98a4b19e` |
| Decoded aggregate | 24,707,352 bytes; 6,176,838 four-byte pixels; compressed/decoded ratio 12.167872%; expansion factor 8.218364× |
| Decoded uniqueness | All 42 decoded SHA-256 values are distinct |
| Pixel encoding | Every fourth decoded byte is exactly `0xFF`; decoding the other three bytes as R, G, and B yields coherent images, establishing opaque `R,G,B,0xFF` / RGBX8888 raster storage |
| Independently corroborated display geometry | Device display evidence records 480×640, 32 bpp, and 1,920-byte stride; each 1,228,800-byte decoded entry equals `480 × 640 × 4` exactly |
| Decoded-size groups | 1,228,800 bytes: entries `0–3`, `25–35`, and `37–41` = 20 full-screen assets; 5,160 bytes: entries `4–13` = ten 30×43 glyphs; 5,904 bytes: entry `14` = one 36×41 glyph; 7,344 bytes: entries `15–24` = ten 102×18 strips; 408 bytes: entry `36` = one 102×1 strip |
| Full-screen asset inventory | Entry 0: brown/orange LOKMAT splash, one rotation; 1: charging/battery graphic on a gold/green luminous background; 2: horizontal empty battery with blue-lit left end; 3: charger-warning screen containing `Error! Please plug out charger!`; 25–34: ten horizontal battery-fill animation frames progressing from low to high orange fill; 35: empty horizontal battery housing; 37: filled/nearly filled horizontal battery with `100%`; 38: brown/orange LOKMAT splash rotated opposite entry 0; 39: vertical rectangular battery with bright-blue lightning; 40: vertical rectangular battery with dark-blue lightning; 41: upright cylindrical battery on a pedestal with blue lightning |
| Glyph and strip inventory | Entries `4–13` are the digit glyphs `0–9` in direct numeric order; entry 14 is `%`; entries `15–24` are ten distinct orange/black curved fill strips; entry 36 is a one-row orange line strip |
| Visual-classification boundary | The recorded visual descriptions are direct raster observations. Producer/runtime symbolic names, UI state mappings, timing, and exact animation sequencing are not encoded in the archive and remain `OPEN_BOUNDARY` |
| Complete stream inventory, entries 0–6 | `0=0x000002B0–0x00055C70,350657→1228800; 1=0x00055C71–0x000B515E,390382→1228800; 2=0x000B515F–0x000D2EE2,122244→1228800; 3=0x000D2EE3–0x000D94E4,26114→1228800; 4=0x000D94E5–0x000D984A,870→5160; 5=0x000D984B–0x000D992F,229→5160; 6=0x000D9930–0x000D9C76,839→5160` |
| Complete stream inventory, entries 7–13 | `7=0x000D9C77–0x000DA04E,984→5160; 8=0x000DA04F–0x000DA2A1,595→5160; 9=0x000DA2A2–0x000DA642,929→5160; 10=0x000DA643–0x000DAA21,991→5160; 11=0x000DAA22–0x000DAC78,599→5160; 12=0x000DAC79–0x000DB08C,1044→5160; 13=0x000DB08D–0x000DB45C,976→5160` |
| Complete stream inventory, entries 14–20 | `14=0x000DB45D–0x000DB94C,1264→5904; 15=0x000DB94D–0x000DC130,2020→7344; 16=0x000DC131–0x000DC985,2133→7344; 17=0x000DC986–0x000DD1A8,2083→7344; 18=0x000DD1A9–0x000DD94F,1959→7344; 19=0x000DD950–0x000DE061,1810→7344; 20=0x000DE062–0x000DE706,1701→7344` |
| Complete stream inventory, entries 21–27 | `21=0x000DE707–0x000DEE76,1904→7344; 22=0x000DEE77–0x000DF681,2059→7344; 23=0x000DF682–0x000DFEB0,2095→7344; 24=0x000DFEB1–0x000E06DC,2092→7344; 25=0x000E06DD–0x000FD0D9,117245→1228800; 26=0x000FD0DA–0x0011E988,137391→1228800; 27=0x0011E989–0x001418CF,143175→1228800` |
| Complete stream inventory, entries 28–34 | `28=0x001418D0–0x001634A0,138193→1228800; 29=0x001634A1–0x001860F1,142417→1228800; 30=0x001860F2–0x001A6D9E,134317→1228800; 31=0x001A6D9F–0x001C808B,135917→1228800; 32=0x001C808C–0x001ECC89,150526→1228800; 33=0x001ECC8A–0x00212D07,155774→1228800; 34=0x00212D08–0x00236417,145168→1228800` |
| Complete stream inventory, entries 35–41 | `35=0x00236418–0x0023EF2E,35607→1228800; 36=0x0023EF2F–0x0023F072,324→408; 37=0x0023F073–0x0026253F,144589→1228800; 38=0x00262540–0x002B815F,351264→1228800; 39=0x002B8160–0x002BDD83,23588→1228800; 40=0x002BDD84–0x002C3638,22709→1228800; 41=0x002C3639–0x002DE246,109582→1228800` |
| Decoded SHA-256, entries 0–5 | `0=b7c86dc254d43506ac8f96a60f8432a75375f783c70ce1c90298dc147ee4ef10; 1=037303e74e82500b11db12b72759ebacd7ee950e5cdb0cc23a174c1b9d2a0071; 2=0c6d21ef75c8f8edb9394f7db39167eff1b61ccd0aa4d2018777397fd11fa5d6; 3=fc7ca64af1297253b2a74ca82d871e418b11f4370a9bada2e66b9188bc1bf29b; 4=af7ad83d59c919ee919aad51e8dd50a30dbbbae8a2b7f319bbec48edc04ef12a; 5=62ba5f4ed5a8738802c78e1a08c73b0bd89f174c113d16d7838bad72e2485b89` |
| Decoded SHA-256, entries 6–11 | `6=6adb9e3a22ceec723fe6e62807a36488226306db817f055280632cf017559d63; 7=6c0454d4336d5caa96f6115ee9b4478f86db0944ea34ee17bd3e369614149795; 8=a1573437301279eb180288ec74d6df0189fef85e1a56a34497fbb8e92f9cf856; 9=32e7ea988ca64bddea41decf0ba35b257f67bf9ca9c42a78876f737c415b16c3; 10=8bc995dedd3fc813f91f8d4f13d4e77dcf64ea4f2bc7de9d2885ee5e19aa82ec; 11=e16081d86917e7b80cd639d50f0938a9010f0af644a3f7158a9bb245148dbaf1` |
| Decoded SHA-256, entries 12–17 | `12=15c29954652a0e8f3ca6bb80c3db11b6e64c3cf0a4baf40f49422568c47c86a1; 13=97886caa69d2868ee613238f6f3a2d78faac9f02d42bff34b41084d7007392bd; 14=cd0c282bd365feab8d9c0d3789aa44dfc67bfc98768215a3064a32f10c88e641; 15=99969dbe7951c450a6de9c0b495a2afcc3db58bdcd8ab367f59ce8320a8c2ee2; 16=0b58f074ca06d1c66a03f0205b4883d4afc3b544e4b18da4b4b1808e1007718f; 17=a16bde9e93bed1350148e7ab3a3e9c02e49d69b62e40cb36a23c0820cb107d30` |
| Decoded SHA-256, entries 18–23 | `18=140ec3cf6de06b96c9d7817aedd54eade715b3bc2aac1a893b1a89fca5af85de; 19=b087a02a1376752172698891411540c6f27b19e37f907e047c5652a6acc3e30c; 20=899c5dede7a78cc2005f0e4b7afe1253471fe72e1c1d6e3a333d0628c45c6466; 21=c9d40b71906ca72e7ca572a4c43f9b392a8c4852f55f5904d139ae8aca3bb940; 22=701f5aaaa59b61d41c89a089e2968463bb591b52c22e2d9d869680a5a686a060; 23=aa734fa78421c3f7d033626eb8e83eb324bc307c027755a673199cf2c3e315d7` |
| Decoded SHA-256, entries 24–29 | `24=8b436742385067ea7eccd435be504ee5bea89bd6503ccf89f95bedf34c0d37f4; 25=bcc41b2644238cac38b4989156c18a9a4b7cf49267e20e70b087122a3a2fa75a; 26=674e735da93630bc10483cccb7ba911aec084094062aaba0e88c6e4a2d3309c2; 27=23c286a55468a2560ccff9539b2c49a480b8d762dde61e8bc4a2831d7fc0d4d3; 28=e323e048905cda098b32aa7bb5000a03647db4359c9330ce10a7ab7471dd0d32; 29=1a82ded0dc9ba8f98021fc02b91b9a3ebbed00a0fb6d2a83374f49217db20560` |
| Decoded SHA-256, entries 30–35 | `30=cf3d5b18ae814ddd5b30ec169758a2220a3527b36f76de4423ddac918e38acf7; 31=0b28bd0d6b8d3a96a602b55f3b34772338dd538267829536677989b3cd35c68b; 32=2a8104bd347777eb7350c95fd56f3547f876598a3276b5dd17a876f5b49d26e3; 33=41169e3520ad5c3946b005ea108ef9d238d7bbaab0e99e7c060406909749a39e; 34=336f5bef07cb9878ae4e878e990e729d87285331176802bfec0cf220e88b2a34; 35=24595c58138194a058443d4223dc48f3f9bffcff20c6cbaeedb3465ef1b9c81c` |
| Decoded SHA-256, entries 36–41 | `36=97d1ab42c96dabdc6a100381f851bf5426b696f078804d0cbc07e4631988deee; 37=3a4a635db3a4d74aee55be68d136b200d9f948f333a7940ed9b4cfed820ab10b; 38=eba105d9a83de0674db1a76a1e359a734348569619ec16c1d9fb342a651bb407; 39=551130573eb94fa01e063b3bf84d1ba59c67d61820b4a0ad2f7bbcefba9ba612; 40=a01a218bc11b34bf708533c8ce2237f7168a3647b0fb1950add7ea1d0d331430; 41=d1162aae6dd5c0eae03798cd8c0f8cc21a76d9d87e71ca3fb15bb3ea090aa55f` |
| Logo alignment span | `0x2DE247–0x2DE24F`; 9 zero bytes; SHA-256 `3e7077fd2f66d689e0cee6a7cf5b37bf2dca7c979af356d0a31cbc5c85605c7d` |
| cert1 GFH header | `0x2DE250–0x2DE44F`; 512 bytes; name `cert1`; primary magic bytes `88 16 88 58`; declared payload `0x000006AD` = 1,709 bytes |
| cert1 GFH control words | LE32 at header `+0x28` = `0x12345678`; LE32 at `+0x2C` = `0xFFFFFFFF` |
| cert1 GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 02000000 00000000 00000010 00000000 22345678` |
| cert1 GFH header SHA-256 | `6f28bbc5a2b6eebf4c5cc0e482fcad897e0020caa398e10e518d4588e4005eee` |
| cert1 DER extent | `0x2DE450–0x2DEAFC`; exactly 1,709 bytes; SHA-256 `f6d4f6dda11a739002e1c2db92f5119780294c21ce8fcb6b5c0754ec2f8eb63b` |
| cert1 DER structure | Top-level DER SEQUENCE consumes the declared payload exactly; principal children are a 1,372-byte-content TBS-like SEQUENCE, a 72-byte RSA-PSS algorithm SEQUENCE, and a 256-byte signature BIT STRING |
| cert1 alignment span | `0x2DEAFD–0x2DEAFF`; 3 zero bytes; SHA-256 `709e80c88487a2411e1ee4dfb9f22a861492d20c4765150c0c794abd70f8147c` |
| cert2 GFH header | `0x2DEB00–0x2DECFF`; 512 bytes; name `cert2`; primary magic bytes `88 16 88 58`; declared payload `0x000003BD` = 957 bytes |
| cert2 GFH control words | LE32 at header `+0x28` = `0x12345678`; LE32 at `+0x2C` = `0xFFFFFFFF` |
| cert2 GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 02000002 00000001 00000010 00000000 22345678` |
| cert2 GFH header SHA-256 | `5abaa4c19345a5f05b1c8a1b70cd56eb041b0ee17692212e4b49c39e5a219ec6` |
| cert2 DER extent | `0x2DED00–0x2DF0BC`; exactly 957 bytes; SHA-256 `a7420c1131b1ad9ae1edbcb8e9d4e715030d009f21bc72a81b6e585c27637861` |
| cert2 DER structure | Top-level DER SEQUENCE consumes the declared payload exactly; principal children are a 620-byte-content TBS-like SEQUENCE, a 72-byte RSA-PSS algorithm SEQUENCE, and a 256-byte signature BIT STRING |
| Certificate parser result | OpenSSL `asn1parse` consumes each DER payload exactly with status 0 and no trailing data; OpenSSL's X.509 loader rejects them because these are MediaTek custom ASN.1 certificate structures, not ordinary standards-conforming X.509 certificates; parser rejection is not evidence of truncation |
| Shared certificate identity fields | Serial `8DCC21F6BCC0525C`; issuer and subject `C=AU, ST=Some-State, O=Mediatek`; encoded UTCTIME pair `150604031444Z` and `350604031444Z` |
| Signature algorithm | RSA-PSS with SHA-256, MGF1-SHA256, 32-byte salt, and trailer field 1 |
| cert1 signed-body evidence | TBS-like sequence SHA-256 `b7d4d49b397fe96359518f3345644aebe111372afeb91f86f3e4a4be5a10a947`; signature-byte SHA-256 `b9fd5f325f2b5487ff313b3a60185d9faded946aa6c3b2f9802db8fb70a5a458` |
| cert2 signed-body evidence | TBS-like sequence SHA-256 `46672132f3f5086ff7afc196bee6c2ea60cc3a61bcffc4182072935d401a7b70`; signature-byte SHA-256 `0619ea8a3f29894116c5a750716dfcd1be8be1531bf0ee37acf4394b1cd75593` |
| cert1 primary public key | RSA 2,048-bit; exponent 65,537; SPKI SHA-256 `edf343d880317d38c6cd9ba2ab77505156045024cd6cfdb0a7addda997bebe68`; modulus SHA-256 `9af8a28bf1096b58751ab5ef5f696c5df3050124bb9fec51bd9bf190d04c618f` |
| cert1 secondary public key | RSA 2,048-bit; exponent 65,537; SPKI SHA-256 `051e3c5a1d927b9c756a339e62ed04124d9e3f54246c66fb3202ecffeae26a87`; modulus SHA-256 `952a0d4b2ce11a057b3da1ba63105da1e4b5e52efab0cb16557ed267370e9506` |
| cert2 primary public key | Byte-for-byte equal to the cert1 secondary public key; identical SPKI and modulus hashes |
| Direct signature verification | cert1 RSA-PSS signature verifies with the cert1 primary key and not the alternate tested key; cert2 RSA-PSS signature verifies with the cert1 secondary/cert2 primary key |
| cert2 logo-payload binding | Proprietary field associated with OID `2.16.886.2454.2.1` contains `eeb0816935d7125ab2c4789dc671e8b8401321683c49e8773a33e7f90daedf83`, exactly equal to SHA-256 of `0x000200–0x2DE24F` = the 3,006,535-byte declared logo payload plus its 9 alignment-zero bytes |
| cert2 logo-header binding | Proprietary field associated with OID `2.16.886.2454.2.4` contains `132f38845606bad06a1ff2545a0dc87d5653c76e1931d0af580d9c34603b562b`, exactly equal to SHA-256 of the 512-byte logo GFH header `0x000000–0x0001FF` |
| Custom OID census | cert1 includes `2.16.886.2454.3.1`, `.1.2`, `.1.1`, `.2.2`, `.2.5`, `.2.3`, and `.3.2`; cert2 includes `.3.1`, `.2.1`, `.2.2`, `.2.4`, `.2.6`, `.3.2`, and `.2.7`, all under base `2.16.886.2454` |
| Certificate semantic boundary | Hash equality, key equality, and RSA-PSS verification are directly established. Meanings of proprietary OIDs other than the two direct hash bindings, certificate policy, trust-anchor deployment, and bootloader enforcement behavior remain `OPEN_BOUNDARY` |
| GFH semantic boundary | Magic, 512-byte header size, embedded names, declared payload lengths, and boundary reconciliation are established; undocumented producer-specific meanings of the remaining GFH words remain `OPEN_BOUNDARY` |
| Terminal zero span | `0x2DF0BD–0x7FFFFF`; 5,377,859 bytes; completely zero-filled; SHA-256 `6e7846f59dd5f615f356deb1760bed5f6fb380a581b02e1f7cc11d3866080da2` |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x2DF0BC` |
| Used extent through last nonzero | `0x000000–0x2DF0BC`; 3,010,749 bytes |
| Partition nonzero-byte count | 2,997,603 |
| Partition zero-byte count | 5,391,005 |
| Partition `0xFF` count | 18,558 |
| Byte-alphabet coverage | All 256 possible byte values occur |
| Populated 4 KiB blocks | 736 blocks in one contiguous run, `0x000000–0x2DFFFF`; exact terminal-zero boundary begins inside the final populated block at `0x2DF0BD` |
| ASCII strings | 30,369 occurrences at minimum length 4; 29,882 unique strings; length range 4–900 bytes; most are compressed-data coincidences |
| UTF-16LE strings | Zero occurrences at minimum length 4 |
| Semantic cleartext | GFH names `logo`, `cert1`, and `cert2`; certificate strings `AU`, `Some-State`, and `Mediatek`; encoded times. Text visible in images is rasterized pixel content and is not stored as equivalent cleartext |
| Independent fill verification | The 9-byte and 3-byte alignment spans and the 5,377,859-byte terminal span each passed direct byte-count validation and independent comparison against zero streams |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `3d00b415d1d8cad27abe92176ff01ab225dca4154b6422794aa152921ffe7d0a` |
| Content classification | `MTK_GFH_LOGO_ARCHIVE_WITH_42_ZLIB_RGBX8888_ASSETS_AND_RSA_PSS_CERTIFICATE_CHAIN` |

## p30 — `dtbo`
| p30 | `dtbo` | 8,388,608 | Android DTBO v0 table containing one structurally complete FDT v17 overlay, followed by an exactly reconciled MediaTek GFH-wrapped two-certificate RSA-PSS relationship, a footer-declared unsigned 512-byte VBMeta object whose single salted `dtbo` hash descriptor validates the complete 33,744-byte original image, sparse zero padding, and a terminal AVB footer | `CURRENT_RESEARCH_CAPTURE`; overlay declarations do not independently establish runtime binding or physical population, AVB authentication/enforcement state and undocumented non-hash GFH/certificate-field semantics `OPEN_BOUNDARY` | `CLOSED` | `7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b` |

| Field | Verified value |
|---|---|
| Evidence object | `dtbo.bin` |
| Object size | 8,388,608 bytes; `0x00800000` |
| Acquisition completeness | Expected from GPT: 8,388,608 bytes; received: 8,388,608 bytes; difference: 0 |
| Partition identity | p30; `dtbo`; `/dev/block/mmcblk0p30`; major:minor `179:30` |
| GPT geometry | Start LBA 1,015,808 (`0xF8000`); end LBA 1,032,191 (`0xFBFFF`) inclusive; 16,384 sectors; byte extent on parent device `0x1F000000–0x1F7FFFFF` |
| GPT unique GUID | `9c1520f3-c2c5-4b89-8242-fe4c61208a9e` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0` |
| Kernel block state | Read-only state `1`; holder count 0; raw GPT and live sysfs geometry match |
| Device-node metadata | `brw-------`; owner/group `root:root`; both supplied SELinux-context columns `u:object_r:block_device:s0` |
| SELinux policy comparison | Supplied/observed context: `u:object_r:block_device:s0`; policy-specific expected context: `u:object_r:dtbo_block_device:s0`; discrepancy preserved as evidence and not silently normalized |
| Aliases | By-name alias `dtbo`; fstab alias `/dtbo`; fstab type `emmc` |
| Research-state context | Physical partition dump acquired with bootloader unlocked, Magisk root present, AVB/verity disabled, and SELinux permissive; factory-relative modification state `OPEN_BOUNDARY` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Deterministic signature census | Android DTBO magic `D7 B7 AB 1E` exactly once at `0x000000`; FDT magic `D0 0D FE ED` exactly once at `0x000040`; MediaTek GFH primary magic `88 16 88 58` exactly at `0x007560` and `0x007E10`; GFH secondary magic `89 16 89 58` exactly at `0x007590` and `0x007E40`; DER sequence anchors exactly at `0x007760` and `0x008010`; `AVB0` exactly once at `0x009000`; `AVBf` exactly once at `0x7FFFC0` |
| Negative standard-signature census | No ELF, ZIP, gzip, PNG, SQLite, Android boot-image, Android sparse-image, or ext-family magic was established anywhere in the partition |
| Complete top-level layout | DTBO table/image `0x000000–0x007555`; DTBO-alignment zeros `0x007556–0x00755F`; cert1 GFH header `0x007560–0x00775F`; cert1 DER `0x007760–0x007E0C`; cert1-alignment zeros `0x007E0D–0x007E0F`; cert2 GFH header `0x007E10–0x00800F`; cert2 DER `0x008010–0x0083CC`; cert2-alignment zeros `0x0083CD–0x0083CF`; pre-VBMeta zeros `0x0083D0–0x008FFF`; embedded VBMeta `0x009000–0x0091FF`; post-VBMeta zeros `0x009200–0x7FFFBF`; AVB footer `0x7FFFC0–0x7FFFFF` |
| Structural extent ledger | 30,038 + 10 + 512 + 1,709 + 3 + 512 + 957 + 3 + 3,120 + 512 + 8,351,168 + 64 = 8,388,608 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| DTBO header extent | `0x000000–0x00001F`; 32 bytes |
| DTBO raw header | `d7 b7 ab 1e 00 00 75 56 00 00 00 20 00 00 00 20 00 00 00 01 00 00 00 20 00 00 08 00 00 00 00 00` |
| DTBO header decode | Magic `0xD7B7AB1E`; total size `0x7556` = 30,038; header size `0x20` = 32; entry size `0x20` = 32; entry count 1; entries offset `0x20` = 32; page size `0x800` = 2,048; version 0 |
| DTBO entry 0 | Entry header `0x000020–0x00003F`; `dt_size=0x7516` = 29,974; `dt_offset=0x40` = 64; ID 0; revision 0; all four custom fields 0 |
| DTBO table reconciliation | `32-byte header + (1 × 32-byte entry) + 29,974-byte FDT = 30,038 bytes`; exact equality with header `total_size`; no internal gap or overlap |
| FDT payload extent | DTBO absolute `0x000040–0x007555`; exactly 29,974 bytes |
| FDT header | Magic `0xD00DFEED`; total size `0x7516` = 29,974; `off_dt_struct=0x38`; `off_dt_strings=0x6828`; `off_mem_rsvmap=0x28`; version 17; last compatible version 16; `boot_cpuid_phys=0`; `size_dt_strings=0xCEE` = 3,310; `size_dt_struct=0x67F0` = 26,608 |
| FDT internal reconciliation | `0x38 + 0x67F0 = 0x6828`; `0x6828 + 0xCEE = 0x7516`; structure and strings blocks exactly tile the declared payload after the header/reserve-map region |
| FDT memory-reservation map | Zero reservation entries; terminating `(address=0,size=0)` pair occupies FDT-relative `0x28–0x37` |
| FDT parser closure | Balanced token stream: 209 nodes, 690 properties, zero `FDT_NOP` tokens, maximum depth 4; `FDT_END` at FDT-relative `0x6824`; only three required zero alignment bytes remain through the declared structure-block end `0x6827`; strings block contains 204 NUL-terminated property-name strings and ends exactly at relative `0x7515` |
| Overlay topology | 43 fragments (`fragment@0` through `fragment@42`) plus `__symbols__`, `__fixups__`, and `__local_fixups__`; every fragment target placeholder is `0xFFFFFFFF` and is resolved by an exact external-fixup entry |
| Fragment target map, 0–14 | `0→chosen; 1→odm; 2→touch; 3→pio; 4→pio; 5→consys; 6→audgpio; 7→pio; 8→mt6370_pmu; 9→i2c3; 10→i2c4; 11→wiite_con; 12→pio; 13→wiite_corp; 14→auxadc` |
| Fragment target map, 15–29 | `15→pmic_clock_buffer_ctrl; 16→i2c0; 17→i2c1; 18→i2c2; 19→i2c3; 20→i2c4; 21→i2c5; 22→i2c6; 23→i2c7; 24→i2c8; 25→i2c9; 26→gpio_usage_mapping; 27→gpio; 28→touch; 29→subpmic_pmu_eint` |
| Fragment target map, 30–42 | `30→dsi_te; 31→main_pmic; 32→mt_pmic_vcama_ldo_reg; 33→mt_pmic_vsim1_ldo_reg; 34→mt_pmic_vsim2_ldo_reg; 35→mt_pmic_vcamd_ldo_reg; 36→mt_pmic_vcamio_ldo_reg; 37→mt_pmic_vldo28_ldo_reg; 38→kd_camera_hw1; 39→touch; 40→keypad; 41→pio; 42→kd_camera_hw1` |
| External-fixup closure | 35 external-fixup labels provide an encoded relocation for every fragment target plus GPIO, interrupt-parent, regulator-supply, and camera-supply placeholder; successful application still depends on the corresponding labels existing in the external base tree |
| Exported-symbol census | 59 `__symbols__` properties: 7 LED plus 1 vibrator symbol; 6 touch-pinctrl symbols; 7 connectivity-pinctrl symbols; 10 audio-pinctrl symbols; 2 power-I²C/UART symbols; 1 capacitive-touch symbol; 6 first-camera-bus symbols; 2 heartrate/CCU symbols; 1 sub-PMIC symbol; 3 second-camera-bus symbols; and 13 camera-pinctrl symbols |
| Local-fixup closure | 38 local-fixup properties: fragment 2 = 6, fragment 5 = 7, fragment 6 = 10, fragment 11 = 2, fragment 42 = 13; all refer to local pinctrl phandles |
| Boot-selection evidence | Independent boot evidence records `androidboot.dtbo_idx=0`; entry index 0 is the only overlay in this table, so the recorded selector is in range and unambiguous |
| `chosen` video-framebuffer declaration | `fb_base_h=0`; `fb_base_l=0x5E605000`; `islcmfound=1`; `islcm_inited=0`; `fps=0x1770` = 6,000; `vramSize=0x017BB000` = 24,883,200; literal LCM name `nt35695B_fhd_dsi_cmd_auo_rt5081_drv`; producer-specific unit/selection semantics beyond the literal values `OPEN_BOUNDARY` |
| LED declarations | Seven nodes: `mediatek,red`, `green`, `blue`, `jogball-backlight`, `keyboard-backlight`, `button-backlight`, and `lcd-backlight`; the first six encode `led_mode=0`, `data=1`, and five zero PWM cells; LCD backlight encodes `led_mode=5`, `data=1`, PWM cells `[0,3,0,0,0]` |
| Vibrator declaration | `mediatek,vibrator`; `vib_timer=25`, `vib_limit=9`, `vib_vol=9`; producer-specific units `OPEN_BOUNDARY` |
| Touch declaration | `tpd-resolution=[480,640]`; `use-tpd-button=0`; `tpd-key-num=3`; key map `[139,172,158,0]`; key-dimension rows `[90,883,100,40]`, `[230,883,100,40]`, `[370,883,100,40]`, and one all-zero row; maximum touches 5; filter enabled 1; pixel density 146; position swap/X reverse/Y reverse all 0; status `okay` |
| Touch pinctrl states | `default`, `state_eint_as_int`, `state_eint_output0`, `state_eint_output1`, `state_rst_output0`, `state_rst_output1`; corresponding pinctrl definitions are supplied in fragment 3 |
| Connectivity/audio pinctrl declarations | Connectivity states include `consys_default`, GPS-LNA init/high/low, and WCN-VCN33 init/high/low; audio states include clock/data MOSI/MISO on/off plus external-amplifier high/low; power I²C/UART alternate-function and GPIO-low states are also defined; exact pinmux cells are structurally present, while board-pad electrical interpretation remains `OPEN_BOUNDARY` |
| MT6370 declaration | Interrupt GPIO number 11 with encoded GPIO tuple `[pio,11,0]`; charger-enable tuple `[pio,154,0]`; `ieoc=50000`; unit and runtime-binding semantics `OPEN_BOUNDARY` |
| External-buck declarations | Under target `i2c3`: node `ext_buck_lp4@57`, regulator name `ext_buck_vdd2`, range 300,000–1,300,000, `vsel_pin=0`; node `ext_buck_lp4x@50`, regulator name `ext_buck_vddq`, same range, `vsel_pin=1`; node-name unit addresses are literal declarations, not independent probe evidence |
| Wiite declaration | `wiite_cp@50`: compatible `wiite,cp`, `reg=0x50`, IRQ GPIO tuples `[pio,8,0]` and `[pio,6,0]`, status `okay`; connection tuples `[pio,10,0]` and `[pio,4,0]` with alternate-function/GPIO pinctrl states |
| ADC/clock-buffer declarations | ADC node compatible `mediatek,adc_channel`, temperature channels 0 and 1, dynamic-custom channel 2, status `okay`; PMIC clock-buffer quantity 7, config `[2,1,1,2,0,0,0]`, drive-current vector of seven `1` values, status `okay` |
| I²C controller declarations | Fragments targeting `i2c0`, `i2c1`, `i2c2`, `i2c3`, `i2c4`, `i2c6`, `i2c7`, `i2c8`, and `i2c9` declare 400,000 clock frequency and open-drain operation; `i2c5` declares 3,400,000 and push-pull operation; each declares one address cell and zero size cells |
| I²C child inventory | `i2c0`: `cap_touch@5d` (`reg=0x5D`); `i2c2`: `camera_sub@2d`, `camera_sub_eeprom@52`, `camera_main@1a`, `camera_main_eeprom@50`, `camera_main_af@0c`, `ccu_sensor_i2c_2_hw@10`; `i2c4`: `heartrate@33`, `ccu_sensor_i2c_4_hw@11`; `i2c5`: `subpmic_pmu@34`; `i2c6`: `camera_main_two@2d`, `camera_main_two_eeprom@50`, `camera_main_two_af@0c`; every listed child has the matching encoded `reg` value and status `okay` |
| GPIO usage declaration | `GPIO_SIM1_SCLK`, `GPIO_SIM1_SRST`, `GPIO_SIM1_SIO`, and `GPIO_SIM2_HOT_PLUG` are external GPIO references resolved through the `pio` fixup |
| GPIO initialization matrix | `gpio_init_default` is exactly 5,040 bytes = 1,260 BE32 cells = 180 seven-cell rows; row cell 0 is the exact sequential index `0–179`; distributions for remaining columns are `c1:{0×113,1×66,4×1}`, `c2:{0×143,1×37}`, `c3:{0×174,1×6}`, `c4:{0×31,1×149}`, `c5:{0×149,1×31}`, `c6:{0×143,1×37}`; undocumented per-column meanings `OPEN_BOUNDARY` |
| Interrupt declarations | Touch `[2,8,2,0]`; sub-PMIC EINT `[11,2,11,0]`; DSI TE `[44,1,44,1]`; main PMIC `[144,4,144,1]`; all four use the `pio` interrupt-parent fixup and status `okay`; tuple-field semantics beyond literal values `OPEN_BOUNDARY` |
| Regulator declarations | `vcama`, `vsim1`, `vsim2`, `vcamd`, `vcamio`, and `vldo28`; each encodes `regulator-default-on=1` and status `okay`; camera and touch supply references resolve through the corresponding external regulator fixups |
| Camera supply/pinctrl declarations | `kd_camera_hw1` receives eight encoded camera supply references plus status `okay`; 13 pinctrl states cover default, camera 0/1 reset low/high, power-down low/high, and MCLK on/off; fragment 42 supplies camera 0/1 VCAMA/VCAMD/VCAMIO references and status `okay` |
| Keypad declaration | Debounce 1,024; software/hardware power keys 116/8; software/hardware reset keys 115/17; extend type 0; map count 72; the 72-cell initialization map has nonzero cells only at indices `0=158`, `1=212`, and `19=30`; power-key GPIO fields both 0; download keys 17 and 8; recovery key 17; status `okay` |
| Compatible/status census | 23 compatible-string properties: 7 LED classes, vibrator, `wiite,cp`, ADC, capacitive touch, six first-camera/CCU classes, heartrate plus second CCU, sub-PMIC, and three second-camera classes; exactly 34 status properties, all literal `okay`, with no `disabled` status encoded |
| Overlay declaration boundary | Node names, `compatible`, `reg`, status, GPIO, regulator, pinctrl, and framebuffer values are compile-time overlay declarations. They establish the binary's intended patch content but do not independently prove component population, successful probe, applied-runtime state, or current physical hardware identity; those runtime/population questions remain `OPEN_BOUNDARY` unless separately corroborated |
| MediaTek GFH certificate census | Two 512-byte GFH headers; primary magic at `0x007560`/`0x007E10`, secondary magic 48 bytes later in each; names `cert1` and `cert2`; each FF-fill tail from header-relative `0x50–0x1FF` is exactly 432 bytes of `0xFF` |
| cert1 GFH header | `0x007560–0x00775F`; declared DER length `0x000006AD` = 1,709; control words `0x12345678` and `0xFFFFFFFF`; secondary vector `58891689 00000200 00000001 02000000 00000000 00000010 00000000 22345678` |
| cert1 DER structure | `0x007760–0x007E0C`; exactly 1,709 bytes; top-level SEQUENCE consumes the declared length exactly; principal children are a 1,372-byte encoded TBS-like SEQUENCE (1,368-byte content), a 72-byte encoded RSA-PSS algorithm SEQUENCE, and a BIT STRING containing a one-byte unused-bits count plus a 256-byte signature |
| cert2 GFH header | `0x007E10–0x00800F`; declared DER length `0x000003BD` = 957; control words `0x12345678` and `0xFFFFFFFF`; secondary vector `58891689 00000200 00000001 02000002 00000001 00000010 00000000 22345678` |
| cert2 DER structure | `0x008010–0x0083CC`; exactly 957 bytes; top-level SEQUENCE consumes the declared length exactly; principal children are a 620-byte encoded TBS-like SEQUENCE (616-byte content), a 72-byte encoded RSA-PSS algorithm SEQUENCE, and a BIT STRING containing a one-byte unused-bits count plus a 256-byte signature |
| Certificate parser result | OpenSSL `asn1parse` consumes each DER payload exactly with status 0 and no trailing data; OpenSSL's ordinary X.509 loader rejects both because they are MediaTek custom ASN.1 certificate structures rather than standards-conforming X.509 certificates; loader rejection is not evidence of truncation |
| Shared certificate identity fields | Serial `8DCC21F6BCC0525C`; issuer and subject `C=AU, ST=Some-State, O=Mediatek`; encoded UTCTIME pair `150604031444Z` and `350604031444Z` |
| Signature algorithm | RSA-PSS with SHA-256, MGF1-SHA256, 32-byte salt, and trailer field 1 |
| Certificate public keys | cert1 contains two RSA 2,048-bit public keys, both exponent 65,537; cert2's primary public key is byte-for-byte equal to cert1's secondary public key |
| Direct signature verification | cert1's RSA-PSS signature verifies against cert1's primary key and fails against the alternate tested cert1 secondary key; cert2's RSA-PSS signature verifies against the cert1-secondary/cert2-primary key |
| cert2 DTBO payload binding | Proprietary field associated with OID `2.16.886.2454.2.1` contains `6a22bbd8d01af4e23eaaae102220282235e29777a4e2e4d41819c710e2e132b0`, exactly equal to SHA-256 of `0x000020–0x00755F` = the 32-byte entry record plus the complete 29,974-byte FDT and 10 alignment-zero bytes |
| cert2 DTBO-header binding | Proprietary field associated with OID `2.16.886.2454.2.4` contains `b97df360ab7e507d28f4d53c3d5485d20f07411820fe2624d2cabe5426967529`, exactly equal to SHA-256 of the 32-byte DTBO header `0x000000–0x00001F` |
| Certificate coverage reconciliation | The two cert2 hash-bound spans are adjacent and together cover `0x000000–0x00755F` = the complete 30,038-byte DTBO table plus its 10-byte alignment span; key equality and direct RSA-PSS verification bind those fields to the cert1 secondary key, while external trust-anchor deployment and enforcement remain `OPEN_BOUNDARY` |
| Custom OID census | cert1 includes `2.16.886.2454.3.1`, `.1.2`, `.1.1`, `.2.2`, `.2.5`, `.2.3`, and `.3.2`; cert2 includes `.3.1`, `.2.1`, `.2.2`, `.2.4`, `.2.6`, `.3.2`, and `.2.7`, all under base `2.16.886.2454` |
| GFH/certificate semantic boundary | Magic, header sizes, names, DER lengths, exact ASN.1 consumption, direct hash equalities, key equality, and RSA-PSS verification are established. Undocumented meanings of remaining GFH words/proprietary OIDs, certificate policy, trust-anchor provenance/deployment, and bootloader enforcement behavior remain `OPEN_BOUNDARY` |
| AVB footer | `0x7FFFC0–0x7FFFFF`; 64 bytes; magic `AVBf`; footer version 1.0; original image size `0x83D0` = 33,744; VBMeta offset `0x9000` = 36,864; VBMeta size `0x200` = 512; all reserved footer bytes zero |
| Footer original-image reconciliation | Footer-declared original image is exactly `0x000000–0x0083CF`: complete DTBO table/alignment plus both GFH/DER certificate wrappers and final three alignment zeros; the boundary lands exactly before the 3,120-byte pre-VBMeta zero span |
| Embedded VBMeta header | `0x009000–0x0090FF`; minimum libavb 1.0; authentication block 0 bytes; auxiliary block 256 bytes; algorithm type 0 (`NONE`); hash/signature/public-key/public-key-metadata sizes all 0; descriptor offset 0; descriptor size 200; rollback index 0; flags 0; rollback-index location 0; release string `avbtool 1.1.0`; all 80 reserved header bytes zero |
| Embedded AVB descriptor | One tag-2 hash descriptor, exactly 200 bytes at `0x009100–0x0091C7`; image size 33,744; algorithm `sha256`; partition `dtbo`; partition-name length 4; salt length 32; digest length 32; flags 0; no descriptor padding; remaining auxiliary bytes `0x0091C8–0x0091FF` are 56 zeros |
| Embedded AVB salt | `f0b811c76a5dd838af23cc5fcd69405d5ec71be8ac01354723f02895b8c8edec` |
| Embedded AVB digest | `34df441cacdc9d852b72cbe166abe44f22c0eae684c2f64cd3d85f998ffb500f` |
| Direct AVB hash validation | Recomputed SHA-256 over the 32-byte salt concatenated with `partition[0:33744]` equals the encoded digest byte-for-byte; the descriptor's `image_size` exactly equals the footer's `original_image_size` |
| AVB authentication boundary | The embedded descriptor is internally self-consistent, but its enclosing VBMeta uses algorithm `NONE` with zero authentication block, zero signature, and zero public key; it therefore supplies corruption detection for the declared bytes but does not itself authenticate them. External/root VBMeta policy and actual bootloader enforcement remain `OPEN_BOUNDARY` |
| DTBO alignment span | `0x007556–0x00755F`; 10 bytes; completely zero-filled |
| Certificate alignment spans | `0x007E0D–0x007E0F` and `0x0083CD–0x0083CF`; 3 bytes each; completely zero-filled |
| Pre-VBMeta zero span | `0x0083D0–0x008FFF`; 3,120 bytes; completely zero-filled |
| Post-VBMeta zero span | `0x009200–0x7FFFBF`; 8,351,168 bytes; completely zero-filled |
| First/last nonzero byte | First `0x000000`; last `0x7FFFE2`; the 29 bytes `0x7FFFE3–0x7FFFFF` are zero-reserved footer tail bytes |
| Partition byte counts | 18,670 nonzero bytes; 8,369,938 zero bytes; 1,168 bytes equal `0xFF`; all 256 possible byte values occur |
| Populated 4 KiB blocks | 11 blocks: one contiguous ten-block run at block indices `0–9` (`0x000000–0x009FFF`) plus terminal block 2,047 (`0x7FF000–0x7FFFFF`); blocks `10–2046` are completely zero-filled |
| ASCII strings | 697 occurrences at minimum length 4; 531 unique; length range 4–52 bytes; semantic classes are FDT node/property/fixup/symbol names and values, GFH names, certificate identity/time fields, `avbtool 1.1.0`, `sha256`, and `dtbo`; remaining short occurrences inside key/signature material are incidental |
| UTF-16LE strings | Zero occurrences at minimum length 4 |
| Independent fill verification | The 10-byte and both 3-byte alignment spans, 3,120-byte pre-VBMeta span, and 8,351,168-byte post-VBMeta span each passed direct byte-count validation and independent comparison against a zero stream |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b` only after structural closure |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `ANDROID_DTBO_V0_SINGLE_FDT_V17_OVERLAY_WITH_MTK_RSA_PSS_CERTIFICATE_BINDING_AND_UNSIGNED_AVB_HASH_FOOTER` |

## p31 — `tee1`
| p31 | `tee1` | 5,242,880 | MediaTek GFH-wrapped, certificate-bound ARM Trusted Firmware BL31 v1.4 debug image for MT6765, containing a 576-byte `EET KTM ` inner header, a 120,256-byte raw AArch64 EL3 firmware body, a directly verified cert1/cert2 RSA-PSS relationship binding the GFH header and complete ATF payload, an exactly reconciled bootloader load package, and terminal zero fill | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state, proprietary inner-header fields, root-of-trust deployment, and separate BL32/MTEE payload identity `OPEN_BOUNDARY` | `CLOSED` | `834a8c4332ee6de87a96594dbefd225f9a8d81dd95c5c5a55ca00f58cd6b7933` |

| Field | Verified value |
|---|---|
| Evidence object | `tee1.bin` |
| Object size | 5,242,880 bytes; `0x00500000` |
| Acquisition completeness | Expected from GPT: 5,242,880 bytes; received: 5,242,880 bytes; difference: 0 |
| Partition identity | p31; `tee1`; `/dev/block/mmcblk0p31`; major:minor `179:31` |
| GPT geometry | Start LBA 1,032,192; end LBA 1,042,431 inclusive; 10,240 sectors |
| GPT byte geometry | Start byte `0x1F800000`; end byte inclusive `0x1FCFFFFF`; end byte exclusive `0x1FD00000` |
| GPT unique GUID | `902d5f3f-434a-4de7-8988-321e88c9b8aa` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0x0000000000000000` |
| Device-node metadata | Mode `brw-------`; owner/group `root:root`; supplied partition and by-name contexts `u:object_r:block_device:s0`; policy-expected context `u:object_r:tee_block_device:s0` |
| Aliases | By-name alias `tee1`; fstab alias `/tee1`; fstab type `emmc` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Standard-signature scan | No Android boot image, ELF, FDT, Android DT table, ZIP, gzip, XZ, LZ4 frame, AVB header/footer, Android sparse image, GPT, or PNG signature occurs in the raw partition |
| MediaTek GFH signature census | Primary magic `88 16 88 58` occurs exactly at `0x000000`, `0x01DA00`, and `0x01E2B0`; secondary magic `89 16 89 58` occurs exactly at `0x000030`, `0x01DA30`, and `0x01E2E0` |
| Complete top-level layout | ATF GFH header `0x000000–0x0001FF`; inner ATF header `0x000200–0x00043F`; raw AArch64 ATF body `0x000440–0x01D9FF`; cert1 GFH header `0x01DA00–0x01DBFF`; cert1 DER `0x01DC00–0x01E2AC`; cert1 alignment zeros `0x01E2AD–0x01E2AF`; cert2 GFH header `0x01E2B0–0x01E4AF`; cert2 DER `0x01E4B0–0x01E86C`; package-alignment zeros `0x01E86D–0x01E86F`; terminal zeros `0x01E870–0x4FFFFF` |
| Structural extent ledger | 512 + 576 + 120,256 + 512 + 1,709 + 3 + 512 + 957 + 3 + 5,117,840 = 5,242,880 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| ATF GFH header | `0x000000–0x0001FF`; 512 bytes; name `atf`; primary magic bytes `88 16 88 58`; declared payload `0x0001D800` = 120,832 bytes |
| ATF GFH control words | LE32 at header `+0x28` = `0xFFFFFFFF`; LE32 at `+0x2C` = `0x00000000`; bytes `+0x50–+0x1FF` are 432 consecutive `0xFF` bytes |
| ATF GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 00000000 00000000 00000010 00000000 00000000` |
| ATF GFH reconciliation | The declared payload begins exactly at `0x000200` and ends at `0x01D9FF`; the next object, cert1, begins immediately at `0x01DA00` with no intervening byte |
| ATF GFH header SHA-256 | `cd70068a721924f75a5e9418d3097cd208a75f57f01a6fba9ecfc9ea24de27b1` |
| ATF payload extent | `0x000200–0x01D9FF`; exactly 120,832 bytes; 96,399 nonzero, 24,433 zero, and 1,837 `0xFF` bytes; all 256 byte values represented |
| ATF payload SHA-256 | `5d78b1bc44017349771f9e4be05d0455dfb0b3f0475435207c9013904f6019e6` |
| Inner-header extent | `0x000200–0x00043F`; exactly `0x240` = 576 bytes |
| Inner-header raw magic | Bytes `45 45 54 20 4B 54 4D 20`; literal ASCII `EET KTM `; producer-specific interpretation remains `OPEN_BOUNDARY` |
| Inner-header leading LE32 vector | `20544545 204D544B 00000240 30010001 00000001 0001D5C0 0001D5C0 00000000` |
| Inner-header proven size fields | Value at inner `+0x08` equals the 576-byte inner-header size; values at `+0x14` and `+0x18` both equal `0x1D5C0` = 120,256, exactly the following body size; `0x240 + 0x1D5C0 = 0x1D800`, exactly the outer GFH payload length |
| Inner-header reserved-zero span | Inner `+0x1C–+0x13B`, absolute `0x00021C–0x00033B`; 288 zero bytes; independently verified |
| Inner-header opaque field | Inner `+0x13C–+0x23B`, absolute `0x00033C–0x00043B`; 256 bytes; 164 distinct byte values; SHA-256 `71b2848952f32c9f0a8e6e64bde78ef381f0132a3e5fd4a017814cd48bb85ac3` |
| Opaque-field verification boundary | The 256-byte field does not verify with the cert1 primary or secondary public keys under tested RSA PKCS#1 v1.5 or RSA-PSS SHA-1/SHA-256 combinations over the body and common header/body candidates; exact producer role remains `OPEN_BOUNDARY` |
| Inner-header terminal word | LE32 `0xFFFFFFFF` at inner `+0x23C`; immediately followed by the raw firmware body |
| Inner-header SHA-256 | `85c6402cf91e00851cc7669a9b795f6068c079f2c54da1efab4672287c5385a0` |
| Firmware-body extent | `0x000440–0x01D9FF`; exactly `0x1D5C0` = 120,256 bytes; 96,120 nonzero, 24,136 zero, and 1,833 `0xFF` bytes; all 256 byte values represented |
| Firmware-body format | Raw position-dependent/linked AArch64 firmware image; no ELF header, program-header table, section table, compression wrapper, or independently encoded linker-section map is present |
| Firmware entry bytes | Body begins `3E 40 1C D5 F1 4D 00 94 1F 20 03 D5`; the instruction encoding, `aarch64` source paths, and EL3-system-register strings independently identify AArch64 secure-firmware code |
| Firmware-body SHA-256 | `f3513dfa6ce424262e79e47abbbc9ae9febfb372c35b51fad0bb6851d416e118` |
| Firmware-body terminal state | Last nonzero byte at `0x01D853`; body-relative `0x1D413`; final 428 body bytes `0x01D854–0x01D9FF` are zero and remain inside the declared body length |
| Firmware identity string | `v1.4(debug):M10PRO_C15_rogbid_V1.2_20211230_2021-12-30@19-02-15-179-ga1685973d-dirty` at `0x015AC8` |
| Firmware build string | `Built : 07:29:53, Jun 13 2025` at `0x015B20` |
| Firmware platform attribution | Direct source-path strings identify `plat/mediatek/mt6765`; direct runtime strings identify `BL31`, MTK SIP services, and ATF logging; classification is MediaTek MT6765 ARM Trusted Firmware BL31 |
| Source-path population | 36 ASCII source paths ending in `.c` or `.S`, including MediaTek MT6765 platform, common BL31, GIC, PSCI, CPU, translation-table, power-management, security, and memory-protection sources |
| BL31/EL3 functionality | Direct strings cover BL31 early/platform/architecture setup, runtime-service initialization, BL32 initialization handoff, secure/non-secure context management, EL3 exception/panic state, MTK SIP calls, and 32-bit/64-bit kernel entry preparation |
| Power and interrupt functionality | Direct strings cover PSCI CPU/cluster on, off, and suspend; system reset/off; SPM/SPMC; GICv2/GICv3; secure PPI/SGI; watchdog/FIQ handling; and system timer state |
| Security and memory functionality | Direct strings cover DEVAPC/DAPC, EMI MPU policy, protected/shared/secure memory types, RPMB HMAC/init/uninit, AMMS modem-memory controls, ATF/AEE crash logging, and secure/non-secure memory-zone configuration |
| Reserved-memory labels | `atf-reserved`, `tee-reserved`, `sapu-mtee-shm`, and `gz-tee-static-shm` occur directly; policy labels include `PROT_SHARED`, `MD_SHARED`, `SECURE_OS`, `SECURE_MEM`, `TRUSTED_UI`, and `SECURE_WFD` |
| TEE/MTEE classification boundary | The payload directly identifies itself as ATF BL31 and contains one `sapu-mtee-shm` label plus `BL31: Initializing BL32`; it contains zero `iTrustee`, `Trustonic`, or `Mobicore` strings. These bytes prove secure-world handoff and MTEE shared-memory support labels, not that this payload itself is an MTEE/iTrustee secure-OS image |
| Keymaster/gatekeeper boundary | RPMB and secure-memory service strings establish ATF capabilities only; they do not by themselves establish the device's keymaster or gatekeeper backend |
| Boot-transcript selection | Captured boot transcript selects `tee1` as the loaded partition and reports image name `atf` |
| Boot-transcript source | Absolute source offset `0x1F800200`, exactly GPT partition start `0x1F800000 + 0x200`; the source begins at the inner ATF header after the outer GFH header |
| Boot-transcript load geometry | Reported load address `0x47D805C0`; reported ATF size `0x1E670` = 124,528 bytes; reported verification time 0 ms |
| Loaded-size reconciliation | `0x01E870 - 0x000200 = 0x1E670`; the reported size equals ATF payload 120,832 + cert1 GFH 512 + cert1 DER 1,709 + pad 3 + cert2 GFH 512 + cert2 DER 957 + pad 3, with zero-byte remainder |
| Derived body load address | Package load `0x47D805C0 + 0x240` inner-header size = `0x47D80800`, a 2,048-byte-aligned start for the raw AArch64 body |
| Reserved-memory reconciliation | ATF reserved region `0x47D80000`, size `0x50000`, ends exclusive `0x47DD0000`; loaded package ends exclusive `0x47D9EC30` and remains fully inside the reserved region |
| cert1 GFH header | `0x01DA00–0x01DBFF`; 512 bytes; name `cert1`; declared payload `0x000006AD` = 1,709 bytes |
| cert1 GFH control words | LE32 at header `+0x28` = `0x12345678`; LE32 at `+0x2C` = `0xFFFFFFFF` |
| cert1 GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 02000000 00000000 00000010 00000000 22345678` |
| cert1 GFH header SHA-256 | `6f28bbc5a2b6eebf4c5cc0e482fcad897e0020caa398e10e518d4588e4005eee` |
| cert1 DER extent | `0x01DC00–0x01E2AC`; exactly 1,709 bytes; SHA-256 `f6d4f6dda11a739002e1c2db92f5119780294c21ce8fcb6b5c0754ec2f8eb63b` |
| cert1 DER structure | Top-level DER SEQUENCE consumes the declared payload exactly; principal children are a 1,372-byte total TBS-like SEQUENCE, a 72-byte RSA-PSS algorithm SEQUENCE, and a 256-byte signature BIT STRING |
| cert1 alignment | `0x01E2AD–0x01E2AF`; 3 zero bytes; independently verified |
| cert2 GFH header | `0x01E2B0–0x01E4AF`; 512 bytes; name `cert2`; declared payload `0x000003BD` = 957 bytes |
| cert2 GFH control words | LE32 at header `+0x28` = `0x12345678`; LE32 at `+0x2C` = `0xFFFFFFFF` |
| cert2 GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 02000002 00000001 00000010 00000000 22345678` |
| cert2 GFH header SHA-256 | `5abaa4c19345a5f05b1c8a1b70cd56eb041b0ee17692212e4b49c39e5a219ec6` |
| cert2 DER extent | `0x01E4B0–0x01E86C`; exactly 957 bytes; SHA-256 `a3b0146044d327efc5e10f868f50c379ee7e10bdeebd808abb55b800dbba7cca` |
| cert2 DER structure | Top-level DER SEQUENCE consumes the declared payload exactly; principal children are a 620-byte total TBS-like SEQUENCE, a 72-byte RSA-PSS algorithm SEQUENCE, and a 256-byte signature BIT STRING |
| Certificate parser result | OpenSSL `asn1parse` consumes both DER objects exactly with status 0 and no trailing data; OpenSSL's X.509 loader rejects them because their MediaTek proprietary ASN.1 fields do not form ordinary standards-conforming X.509 certificates; rejection is not truncation evidence |
| Shared certificate identity fields | Serial `8DCC21F6BCC0525C`; issuer and subject `C=AU, ST=Some-State, O=Mediatek`; encoded UTCTIME pair `150604031444Z` and `350604031444Z` |
| Certificate signature algorithm | RSA-PSS with SHA-256, MGF1-SHA256, 32-byte salt, and trailer field 1 |
| cert1 signed-body evidence | TBS-like DER child SHA-256 `b7d4d49b397fe96359518f3345644aebe111372afeb91f86f3e4a4be5a10a947`; signature-byte SHA-256 `b9fd5f325f2b5487ff313b3a60185d9faded946aa6c3b2f9802db8fb70a5a458` |
| cert2 signed-body evidence | TBS-like DER child SHA-256 `aa12fcfa845932e9f46283b9c09506daa106da1f9f96a4b01d873a82f5d3401b`; signature-byte SHA-256 `9859661aab93dcdd5f69b077b5f3df5f25d0027682d94acf4465cf902e7e69f8` |
| cert1 primary public key | RSA 2,048-bit; exponent 65,537; SPKI SHA-256 `edf343d880317d38c6cd9ba2ab77505156045024cd6cfdb0a7addda997bebe68`; modulus SHA-256 `9af8a28bf1096b58751ab5ef5f696c5df3050124bb9fec51bd9bf190d04c618f` |
| cert1 secondary public key | RSA 2,048-bit; exponent 65,537; SPKI SHA-256 `051e3c5a1d927b9c756a339e62ed04124d9e3f54246c66fb3202ecffeae26a87`; modulus SHA-256 `952a0d4b2ce11a057b3da1ba63105da1e4b5e52efab0cb16557ed267370e9506` |
| cert2 primary public key | Byte-for-byte equal to the cert1 secondary public key; identical SPKI and modulus hashes |
| Direct certificate-signature verification | cert1 RSA-PSS signature verifies with the cert1 primary key; cert2 RSA-PSS signature verifies with the cert1 secondary/cert2 primary key; alternate tested key relationships fail |
| cert2 ATF-payload binding | Proprietary field associated with OID `2.16.886.2454.2.1` contains `5d78b1bc44017349771f9e4be05d0455dfb0b3f0475435207c9013904f6019e6`, exactly equal to SHA-256 of the complete 120,832-byte ATF payload `0x000200–0x01D9FF` |
| cert2 ATF-header binding | Proprietary field associated with OID `2.16.886.2454.2.4` contains `cd70068a721924f75a5e9418d3097cd208a75f57f01a6fba9ecfc9ea24de27b1`, exactly equal to SHA-256 of the 512-byte ATF GFH header `0x000000–0x0001FF` |
| Custom OID census | cert1 includes `2.16.886.2454.3.1`, `.1.2`, `.1.1`, `.2.2`, `.2.5`, `.2.3`, and `.3.2`; cert2 includes `.3.1`, `.2.1`, `.2.2`, `.2.4`, `.2.6`, `.3.2`, and `.2.7`, all under base `2.16.886.2454` |
| Certificate trust boundary | Hash equality, public-key equality, and both RSA-PSS relationships are directly established; external root-key provisioning, boot-ROM/preloader trust-anchor selection, revocation policy, and enforcement behavior remain `OPEN_BOUNDARY` |
| GFH semantic boundary | Magic, 512-byte header size, embedded names, declared payload lengths, and boundary reconciliation are established; undocumented meanings of remaining producer-specific GFH words remain `OPEN_BOUNDARY` |
| Package-alignment span | `0x01E86D–0x01E86F`; 3 zero bytes; brings the bootloader load package to exact size `0x1E670`; independently verified |
| Terminal zero span | `0x01E870–0x4FFFFF`; 5,117,840 bytes; completely zero-filled; SHA-256 `6683aa761017914e966c8da7f3d552509332154ff5aa563f7c2b12fa648e4636` |
| First nonzero byte | `0x000000` |
| Last nonzero byte | `0x01E86C` |
| Used extent through last nonzero | `0x000000–0x01E86C`; 125,037 bytes |
| Structurally allocated package extent | `0x000000–0x01E86F`; 125,040 bytes including final alignment zeros |
| Partition nonzero-byte count | 100,141 |
| Partition zero-byte count | 5,142,739 |
| Partition `0xFF` count | 3,149 |
| Byte-alphabet coverage | All 256 possible byte values occur |
| Populated 4 KiB blocks | 31 blocks in one contiguous run, `0x000000–0x01EFFF`; exact terminal-zero boundary begins inside the final populated block at `0x01E870` |
| ASCII strings | 1,008 occurrences at minimum length 4; 957 unique strings; length range 4–168 bytes |
| Firmware-body ASCII classes | 964 occurrences, 931 unique; build identity; BL31/EL3/PSCI and MediaTek services; assertions/errors/log formats; source paths; CPU/register names; security and memory-policy labels |
| UTF-16LE strings | Three minimum-length-4 candidates, two unique, at `0x0199DA`, `0x01A508`, and `0x01A5D8`; all are punctuation/arithmetic character sequences produced by binary word patterns and are classified as incidental coincidences |
| Independent fill verification | Inner-header reserved zeros, 428-byte body tail, both 3-byte certificate/package alignment spans, and the 5,117,840-byte terminal span passed direct byte-count assertions and independent `cmp` comparisons against `/dev/zero` |
| Closure assertions | Exact GFH counts and boundaries, inner-header/body math, all zero extents, bootloader load-size reconciliation, both DER lengths, public-key equality, both RSA-PSS signatures, both cert2 content hashes, and unique GFH magic positions passed one deterministic assertion suite |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `834a8c4332ee6de87a96594dbefd225f9a8d81dd95c5c5a55ca00f58cd6b7933` |
| Content classification | `MTK_GFH_WRAPPED_ATF_BL31_AARCH64_FIRMWARE_WITH_RSA_PSS_CERTIFICATE_CHAIN` |

## p32 — `tee2`
| p32 | `tee2` | 11,534,336 | Structurally complete MediaTek GFH-wrapped AArch64 ARM Trusted Firmware BL31 image for MT6765: a self-reconciling 576-byte `EET KTM` inner header, 120,256-byte compiled secure-monitor image with a 16-slot AArch64 exception-vector table, literal ATF/build identity strings, PSCI/EL3/GIC/SPM/RPMB/TRNG/MPU functionality, a directly verified two-stage RSA-PSS certificate relationship binding the complete GFH header and payload, and a fully zero-filled terminal region | `PHYSICAL_DUMP_CAPTURE`; inner-header proprietary fields, external trust-anchor/enforcement behavior, slot-selection policy, and factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `e74a9c635ff82c9786e3aa950338e1d7aa1611c4b9b4d51714d6ead02cbad860` |

| Field | Verified value |
|---|---|
| Evidence object | `tee2.bin` |
| Object size | 11,534,336 bytes; `0x00B00000` |
| Acquisition completeness | Expected from GPT: 11,534,336 bytes; received: 11,534,336 bytes; difference: 0 |
| Partition identity | p32; `tee2`; `/dev/block/mmcblk0p32`; major:minor `259:0` |
| GPT geometry | Start LBA 1,042,432 (`0xFE800`); end LBA 1,064,959 (`0x103FFF`) inclusive; 22,528 sectors; byte extent on parent device `0x1FD00000–0x207FFFFF` |
| GPT unique GUID | `bece74c8-d8e2-4863-9bfe-5b0b66bb920f` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0` |
| Kernel block state | Read-only state `1`; alignment offset 0; discard alignment 3,145,728 bytes; holder count 0; raw GPT and live sysfs geometry match |
| Device-node metadata | `brw-------`; owner/group `root:root`; both supplied SELinux-context columns `u:object_r:block_device:s0` |
| SELinux policy comparison | Supplied/observed context: `u:object_r:block_device:s0`; policy-specific expected context: `u:object_r:tee_block_device:s0`; discrepancy preserved as evidence and not silently normalized |
| Aliases | By-name alias `tee2`; fstab alias `/tee2`; fstab type `emmc` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Deterministic container census | MediaTek GFH primary magic `88 16 88 58` exactly at `0x000000`, `0x01DA00`, and `0x01E2B0`; GFH secondary magic `89 16 89 58` exactly at `0x000030`, `0x01DA30`, and `0x01E2E0`; DER sequence anchors exactly at `0x01DC00` and `0x01E4B0`; literal inner magic `EET KTM ` exactly at `0x000200` |
| Negative standard-signature census | No ELF, ZIP, gzip, XZ, Zstandard, LZ4-frame, bzip2, PNG, SQLite, Android boot-image, Android sparse-image, AVB, FDT, DTBO, PEM, or recognized filesystem signature was established anywhere in the partition |
| Incidental-signature handling | Four `78 01` byte pairs occur inside AArch64 code/data at `0x00A763`, `0x00A793`, `0x00ECCD`, and `0x013EBC`; direct zlib decompression fails immediately at all four, so none was promoted to a compressed stream |
| Complete top-level layout | Main `atf` GFH header `0x000000–0x0001FF`; declared ATF payload `0x000200–0x01D9FF`; cert1 GFH header `0x01DA00–0x01DBFF`; cert1 DER `0x01DC00–0x01E2AC`; cert1-alignment zeros `0x01E2AD–0x01E2AF`; cert2 GFH header `0x01E2B0–0x01E4AF`; cert2 DER `0x01E4B0–0x01E86C`; terminal zeros `0x01E86D–0xAFFFFF` |
| Structural extent ledger | 512 + 120,832 + 512 + 1,709 + 3 + 512 + 957 + 11,409,299 = 11,534,336 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| MediaTek GFH signature census | Three primary and three secondary magic instances only; no additional GFH-wrapped image exists beyond the main `atf`, `cert1`, and `cert2` objects |
| Main GFH header | `0x000000–0x0001FF`; 512 bytes; name `atf`; primary magic bytes `88 16 88 58`; declared payload length `0x0001D800` = 120,832 bytes |
| Main GFH control words | LE32 at header `+0x28` = `0xFFFFFFFF`; LE32 at `+0x2C` = `0x00000000` |
| Main GFH secondary vector | At header `+0x30`: `58891689 00000200 00000001 00000000 00000000 00000010 00000000 00000000` |
| Main GFH FF-fill | Header-relative `0x50–0x1FF`; 432 bytes; completely `0xFF`-filled |
| Main GFH reconciliation | Declared payload begins immediately at `0x000200`, contains exactly 120,832 bytes, and ends at `0x01D9FF`; cert1 begins immediately at `0x01DA00`, with zero gap and zero overlap |
| Inner-header extent | `0x000200–0x00043F`; `0x240` = 576 bytes |
| Inner-header leading bytes | `45 45 54 20 4B 54 4D 20 40 02 00 00 01 00 01 30 01 00 00 00 C0 D5 01 00 C0 D5 01 00`; literal ASCII magic `EET KTM ` |
| Inner-header field decode | LE32 at inner `+0x08` = `0x00000240` = 576; `+0x0C` raw bytes `01 00 01 30`, LE32 `0x30010001`; `+0x10` = 1; `+0x14` = `0x0001D5C0` = 120,256; `+0x18` = the same 120,256 |
| Inner-header length reconciliation | The `+0x08` value exactly reaches the first AArch64 vector instruction; each duplicate 120,256-byte field exactly equals the remaining image extent `0x000440–0x01D9FF`; `576 + 120,256 = 120,832`, exactly equal to the outer GFH payload length |
| Inner-header reserved-zero region | `0x00021C–0x00033B`; exactly 288 bytes; completely zero-filled |
| Inner-header opaque field | `0x00033C–0x00043B`; exactly 256 bytes; entropy 7.186160693 bits/byte; 255 nonzero bytes, one zero byte, no `0xFF`; byte-distinct from both external DER signature fields; no encoded signed-span/algorithm/key relationship establishes its role, so semantics remain `OPEN_BOUNDARY` |
| Inner-header terminal marker | `0x00043C–0x00043F`; exactly four `0xFF` bytes |
| Inner proprietary-field boundary | The length relationships above are directly established. Interpreting `0x30010001` as a semantic version, assigning meaning to the `+0x10` field, or labeling the 256-byte opaque field as a signature would exceed the bytes; those meanings remain `OPEN_BOUNDARY` |
| Declared inner image | `0x000440–0x01D9FF`; exactly `0x1D5C0` = 120,256 bytes; raw AArch64 code plus read-only data, strings, tables, and internal zero padding; no ELF wrapper or section table |
| AArch64 exception-vector table | `0x000440–0x000C3F`; exactly 2,048 bytes = 16 slots × 128 bytes; slot starts occur at `0x440 + n×0x80` for `n=0..15` |
| Vector-slot evidence | Slots 0–7 each begin with LE instruction words `0xD51C403E`, a slot-specific `BL` word in the `0x94004Dxx` range, and `0xD503201F` (`NOP` encoding), followed by per-slot zero padding; slots 8–15 contain corresponding AArch64 exception-entry instruction sequences at the same 128-byte cadence |
| Architecture classification | `AARCH64` established independently by the complete 16-slot vector geometry, aligned 32-bit A64 instruction stream, EL3 system-register encodings, and embedded paths `lib/cpus/aarch64/cpu_helpers.S`, `lib/aarch64/misc_helpers.S`, and `lib/xlat_tables_v2/aarch64/xlat_tables_arch.c` |
| Post-vector code/data extent | `0x000C40–0x01D9FF`; compiled BL31 implementation, platform code, constant/read-only data, diagnostic strings, memory-policy labels, internal fill, and terminal image padding |
| Literal firmware identity | `v1.4(debug):M10PRO_C15_rogbid_V1.2_20211230_2021-12-30@19-02-15-179-ga1685973d-dirty` at `0x015AC8` |
| Literal build stamp | `Built : 07:29:53, Jun 13 2025` at `0x015B20`; string is preserved literally; build-host clock validity, timezone, and provenance remain `OPEN_BOUNDARY` |
| Platform identity | Multiple source paths name `plat/mediatek/mt6765`, including `bl31_plat_setup.c`, `plat_mt_gic.c`, `plat_pm.c`, `scu.c`, and `drivers/sec/rpmb_hmac.c`; this directly identifies the compiled platform target as MediaTek MT6765 |
| BL31 identity evidence | Repeated `BL31`/`bl31` strings and paths include `bl31_main.c`, `bl31_context_mgmt.c`, `bl31_prepare_next_image_entry`, `bl31_platform_setup`, `bl31_early_platform_setup`, `BL31_BASE`, `BL31_TZRAM_SIZE`, and `BL31: Initializing runtime services` |
| Secure-monitor/SMC content | MediaTek SIP handler and platform SIP handler; secure/non-secure SMC dispatch; LK and kernel-stage SMC paths; FIQ/IPI handling; EL3 context management and exception diagnostics; exact runtime call coverage remains dependent on the booted system |
| PSCI/power content | PSCI CPU on/off/suspend, system suspend/reset/off, affinity/power-domain state coordination, coherent bakery locks, MediaTek cluster/core power paths, SPMC controls, RTC and PMIC-wrapper handling |
| Interrupt-controller content | Both GICv2 and GICv3 helper code/strings are compiled, including secure PPI/SGI setup, redistributor handling, group configuration, and CPU-interface paths; which hardware path executes at runtime remains `OPEN_BOUNDARY` |
| SPM firmware interface | Embedded filenames `pcm_allinone_lp4_term_3200.bin`, `pcm_allinone_lp4_3200.bin`, `pcm_allinone_lp3_1866.bin`, `pcm_allinone_lp4_term_2400.bin`, and `pcm_allinone_lp4_2400.bin`; functions/strings for loading the separate `spmfw` partition and configuring suspend firmware |
| Security-service content | RPMB HMAC initialization/uninitialization and parameter checks; TRNG request/error handling; EMI MPU/MPU-zone controls; DAPC/DEVAPC and modem-memory AMMS policy; compilation establishes capability, not successful runtime invocation |
| Memory-policy labels | `atf-reserved`, `tee-reserved`, `sapu-mtee-shm`, `gz-tee-static-shm`, `PROT_SHARED`, `MD_SHARED`, `SECURE_OS`, `SECURE_MEM`, `TRUSTED_UI`, and `SECURE_WFD` occur as literal data/policy labels |
| TEE-OS object boundary | No second GFH image, ELF, or other bounded secure-OS payload follows the BL31 image inside the declared main payload; `sapu-mtee-shm` is one reserved-memory label at `0x01BB28`, not an embedded MTEE image. `OP-TEE`, `OPTEE`, `Trusty`, `iTrustee`, and `TEEI` markers are absent |
| BL32 string boundary | `BL31: Initializing BL32` is one compiled diagnostic string; no separately bounded BL32 binary is present in this partition image, so the identity/location of any runtime BL32 payload remains `OPEN_BOUNDARY` |
| CPU-family strings | Cortex-A53 and Cortex-A57 errata/helper names are both compiled; these are code-coverage artifacts and do not by themselves prove physical CPU population |
| Large internal zero span | `0x01C514–0x01CC3F`; 1,836 bytes; completely zero-filled inside the declared image; exact section/BSS/alignment purpose `OPEN_BOUNDARY` |
| Secondary internal zero span | `0x01D101–0x01D207`; 263 bytes; completely zero-filled inside the declared image; exact purpose `OPEN_BOUNDARY` |
| Inner-image terminal zeros | `0x01D854–0x01D9FF`; 428 bytes; completely zero-filled but included in both duplicate inner size fields and the outer GFH payload length |
| Cryptographic-table census | No exact full AES S-box, SHA-256 64-word constant table in either endian order, standard Base64 alphabet, or URL-safe Base64 alphabet occurs in the partition; absence does not exclude hardware-backed or non-table crypto implementations |
| cert1 GFH header | `0x01DA00–0x01DBFF`; 512 bytes; name `cert1`; declared DER length `0x000006AD` = 1,709; control words `0x12345678` and `0xFFFFFFFF`; secondary vector `58891689 00000200 00000001 02000000 00000000 00000010 00000000 22345678`; relative `0x50–0x1FF` is 432 bytes of `0xFF` |
| cert1 DER structure | `0x01DC00–0x01E2AC`; exactly 1,709 bytes; top-level SEQUENCE consumes the declared length exactly; principal children are a 1,372-byte encoded TBS-like SEQUENCE (1,368-byte content), a 72-byte encoded RSA-PSS algorithm SEQUENCE, and a BIT STRING containing a one-byte unused-bits count plus a 256-byte signature |
| cert1 alignment span | `0x01E2AD–0x01E2AF`; exactly 3 bytes; completely zero-filled |
| cert2 GFH header | `0x01E2B0–0x01E4AF`; 512 bytes; name `cert2`; declared DER length `0x000003BD` = 957; control words `0x12345678` and `0xFFFFFFFF`; secondary vector `58891689 00000200 00000001 02000002 00000001 00000010 00000000 22345678`; relative `0x50–0x1FF` is 432 bytes of `0xFF` |
| cert2 DER structure | `0x01E4B0–0x01E86C`; exactly 957 bytes; top-level SEQUENCE consumes the declared length exactly; principal children are a 620-byte encoded TBS-like SEQUENCE (616-byte content), a 72-byte encoded RSA-PSS algorithm SEQUENCE, and a BIT STRING containing a one-byte unused-bits count plus a 256-byte signature |
| Certificate parser result | OpenSSL `asn1parse` consumes each DER payload exactly with status 0 and no trailing data; OpenSSL's ordinary X.509 loader rejects them because they are MediaTek custom ASN.1 certificate structures rather than standards-conforming X.509 certificates; loader rejection is not evidence of truncation |
| Shared certificate identity fields | Serial `8DCC21F6BCC0525C`; issuer and subject `C=AU, ST=Some-State, O=Mediatek`; encoded UTCTIME pair `150604031444Z` and `350604031444Z` |
| Signature algorithm | RSA-PSS with SHA-256, MGF1-SHA256, 32-byte salt, and trailer field 1 |
| Certificate public keys | cert1 contains two RSA 2,048-bit public keys, both exponent 65,537; cert2's primary public key is byte-for-byte equal to cert1's secondary public key |
| Direct signature verification | cert1's RSA-PSS signature verifies against cert1's primary key and fails against the alternate tested cert1 secondary key; cert2's RSA-PSS signature verifies against the cert1-secondary/cert2-primary key |
| cert2 ATF-payload binding | Proprietary field associated with OID `2.16.886.2454.2.1` contains `5d78b1bc44017349771f9e4be05d0455dfb0b3f0475435207c9013904f6019e6`, exactly equal to SHA-256 of the entire declared 120,832-byte main payload `0x000200–0x01D9FF` |
| cert2 ATF-header binding | Proprietary field associated with OID `2.16.886.2454.2.4` contains `cd70068a721924f75a5e9418d3097cd208a75f57f01a6fba9ecfc9ea24de27b1`, exactly equal to SHA-256 of the 512-byte main GFH header `0x000000–0x0001FF` |
| Certificate coverage reconciliation | The two cert2 hash-bound spans are adjacent and together cover `0x000000–0x01D9FF` = the complete main GFH header and declared payload; key equality and direct RSA-PSS verification bind both fields to the cert1 secondary key, while external trust-anchor deployment and bootloader enforcement remain `OPEN_BOUNDARY` |
| Custom OID census | cert1 includes `2.16.886.2454.3.1`, `.1.2`, `.1.1`, `.2.2`, `.2.5`, `.2.3`, and `.3.2`; cert2 includes `.3.1`, `.2.1`, `.2.2`, `.2.4`, `.2.6`, `.3.2`, and `.2.7`, all under base `2.16.886.2454` |
| GFH/certificate semantic boundary | Magic, header sizes, names, DER lengths, exact ASN.1 consumption, direct hash equalities, key equality, and RSA-PSS verification are established. Undocumented meanings of remaining GFH words/proprietary OIDs, certificate policy, trust-anchor provenance/deployment, and loader enforcement behavior remain `OPEN_BOUNDARY` |
| Terminal zero span | `0x01E86D–0xAFFFFF`; 11,409,299 bytes; completely zero-filled |
| First/last nonzero byte | First `0x000000`; last `0x01E86C` |
| Used extent through last nonzero | `0x000000–0x01E86C`; 125,037 bytes |
| Partition byte counts | 100,141 nonzero bytes; 11,434,195 zero bytes; 3,149 bytes equal `0xFF`; all 256 possible byte values occur |
| Populated 4 KiB blocks | 31 blocks in one contiguous run, indices `0–30` (`0x000000–0x01EFFF`); exact terminal-zero boundary begins inside block 30 at `0x01E86D`; blocks `31–2815` are completely zero-filled |
| ASCII strings | 1,002 occurrences at minimum length 4; 951 unique; length range 4–168 bytes; semantic classes are GFH/certificate fields, firmware/build identity, MediaTek/BL31 source paths, function/assertion/diagnostic text, PSCI/GIC/SPM/RPMB/TRNG/MPU labels, and memory-policy names; remaining early short occurrences are machine-code/data coincidences |
| UTF-16LE strings | Three minimum-length-4 detections at `0x0199DA`, `0x01A508`, and `0x01A5D8`; all are monotonic punctuation/alphanumeric table patterns inside compiled data rather than semantic prose |
| Independent fill verification | All three 432-byte GFH FF-fill spans passed direct comparison against `0xFF`; the 288-byte inner reserved region, 428-byte inner-image tail, 3-byte certificate alignment, and 11,409,299-byte terminal span each passed direct byte-count validation and independent comparison against a zero stream |
| Slot-comparison boundary | No `tee1.bin` evidence object was supplied for this pass; full-partition or active-content equality between p31 and p32 is therefore not asserted here and remains `OPEN_BOUNDARY` pending direct byte comparison |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `e74a9c635ff82c9786e3aa950338e1d7aa1611c4b9b4d51714d6ead02cbad860` only after structural closure |
| Capture classification | `PHYSICAL_DUMP_CAPTURE`; factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `MTK_GFH_AARCH64_ARM_TRUSTED_FIRMWARE_BL31_WITH_RSA_PSS_CERTIFICATE_CHAIN` |

## p33 — `super`
| p33 | `super` | 4,294,967,296 | Structurally complete Android LP v10.0 dynamic-partition container with duplicate geometry blocks, two byte-identical metadata slots plus byte-identical backups, one populated `main` group, three read-only ext4 logical partitions, and a complete per-child sequence of SHA-256 dm-verity tree, two-root FEC data, unsigned embedded VBMeta descriptors, verified zero alignment, terminal AVB footer, and exact physical-gap accounting | `CURRENT_RESEARCH_CAPTURE`; full logical-filesystem namespace census, independent dm-verity-tree/root-digest and FEC recomputation, runtime mapping/activation, bootloader enforcement, and factory-relative modification state `OPEN_BOUNDARY` | `CLOSED` | `247e84069af75a4fc248c398338db06a8bbf80373f2b8f8e225dafccb443cd97` |

| Field | Verified value |
|---|---|
| Evidence object | `super.bin` |
| Object size | 4,294,967,296 bytes; `0x0000000100000000`; exactly 4 GiB |
| Acquisition completeness | Expected from GPT: 4,294,967,296 bytes; received: 4,294,967,296 bytes; difference: 0 |
| Partition identity | p33; `super`; `/dev/block/mmcblk0p33`; major:minor `259:1` |
| GPT geometry | Start LBA 1,064,960 (`0x104000`); end LBA 9,453,567 (`0x903FFF`) inclusive; 8,388,608 sectors |
| GPT byte geometry | Parent-device extent `0x0000000020800000–0x00000001207FFFFF`; end exclusive `0x0000000120800000` |
| GPT unique GUID | `ff1342cf-b7be-44d5-a25e-a435addd2702` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0x0000000000000000` |
| Kernel block state | Read-only state `1`; alignment offset 0; discard alignment 0; holder count 3; holders `dm-0`, `dm-1`, and `dm-2` |
| Device-node metadata | `brw-------`; owner/group `root:root`; raw target context `u:object_r:super_block_device:s0` |
| Alias contexts | Root by-name alias context `u:object_r:block_device:s0`; platform by-name alias and target context `u:object_r:super_block_device:s0` |
| Aliases | By-name alias `super`; platform alias `/dev/block/platform/bootdevice/mmcblk0p33` |
| Recognized outer filesystem | NONE |
| Outer recognition | `file -k`: `data`, status 0; `blkid -p`: no recognized outer filesystem signature, status 2 |
| Primary container | Android Logical Partitions metadata format; geometry magic LE32 `0x616C4467`; metadata-header magic LE32 `0x414C5030` |
| Confirmed structural anchors | LP geometry at `0x00001000` and `0x00002000`; metadata headers at `0x00003000`, `0x00013000`, `0x00023000`, and `0x00033000`; ext4 superblock magic in all three logical children; embedded `AVB0` objects at `0x462F3000`, `0x9305D000`, and `0xA46A1000`; terminal `AVBf` footers at `0x4633AFC0`, `0x930AAFC0`, and `0xA46C1FC0` |
| Complete top-level physical layout | Leading reserved zeros `0x00000000–0x00000FFF`; primary geometry block `0x00001000–0x00001FFF`; backup geometry block `0x00002000–0x00002FFF`; primary metadata slot 0 `0x00003000–0x00012FFF`; primary metadata slot 1 `0x00013000–0x00022FFF`; backup metadata slot 0 `0x00023000–0x00032FFF`; backup metadata slot 1 `0x00033000–0x00042FFF`; pre-data reserved zeros `0x00043000–0x000FFFFF`; `product` extent `0x00100000–0x4633AFFF`; zero gap `0x4633B000–0x463FFFFF`; `system` extent `0x46400000–0x930AAFFF`; zero gap `0x930AB000–0x930FFFFF`; `vendor` extent `0x93100000–0xA46C1FFF`; terminal zeros `0xA46C2000–0xFFFFFFFF` |
| Top-level structural extent ledger | 4,096 + 4,096 + 4,096 + 65,536 + 65,536 + 65,536 + 65,536 + 774,144 + 1,176,743,936 + 806,912 + 1,288,351,744 + 348,160 + 291,250,176 + 1,536,417,792 = 4,294,967,296 bytes; all regions are contiguous and nonoverlapping, with no unassigned physical byte |
| First/last nonzero boundary | Leading `0x00000000–0x00000FFF` is zero; first nonzero byte occurs at geometry magic offset `0x00001000`; the final nonzero byte established by the vendor footer occurs at `0xA46C1FE3`; `0xA46C1FE4–0xFFFFFFFF` is zero-filled |
| Leading reserved span | `0x00000000–0x00000FFF`; 4,096 bytes; completely zero-filled |
| Primary geometry structure | `0x00001000–0x00001033`; 52 bytes; magic `67 44 6C 61`; structure size 52; metadata maximum size 65,536; metadata slot count 2; logical-block size 4,096 |
| Backup geometry structure | `0x00002000–0x00002033`; byte-for-byte equal to the primary geometry structure; the remainder of each 4,096-byte geometry block is zero-filled |
| Geometry encoded checksum | `4e31cf64275442f40e25c772a18d1fccd8b1291232e593f65e522fc7ac07df03`; stored in both geometry copies; independent checksum recomputation was not captured and remains `OPEN_BOUNDARY` |
| Metadata-slot geometry | Two 65,536-byte primary slots followed by two 65,536-byte backup slots; slot starts `0x00003000`, `0x00013000`, `0x00023000`, and `0x00033000` |
| Metadata-copy equality | Direct 65,536-byte comparisons establish primary slot 0 = backup slot 0, primary slot 1 = backup slot 1, and primary slot 0 = primary slot 1; therefore all four formal metadata slots are byte-identical |
| Active metadata extent per slot | 516 bytes from each slot start: 128-byte header plus 388-byte table area; formal reserved slot capacity after the active structure is 65,020 bytes per slot |
| Metadata version/header | Major 10; minor 0; header size 128 bytes; table size 388 bytes; total active metadata size 516 bytes |
| Encoded metadata-header checksum | `132ae38367df966fa64b50b0f9084e32eefeb898c3570d4a2c81c329749602bc`; independent recomputation `OPEN_BOUNDARY` |
| Encoded metadata-tables checksum | `253002e86e085d50053b6ec56ec3d0c9001407447b1bf0e8ed4f23fe5cc6dedc`; independent recomputation `OPEN_BOUNDARY` |
| Partition-table descriptor | Table-relative offset 0; 3 entries; 52 bytes per entry; 156 bytes total |
| Extent-table descriptor | Table-relative offset 156; 3 entries; 24 bytes per entry; 72 bytes total |
| Group-table descriptor | Table-relative offset 228; 2 entries; 48 bytes per entry; 96 bytes total |
| Block-device-table descriptor | Table-relative offset 324; 1 entry; 64 bytes total; table area ends exactly at relative byte 387 |
| LP partition entries | `product`, `system`, and `vendor`; each has attributes `0x00000001` (`readonly`), one linear extent, and group index 1 (`main`) |
| LP extent entry 0 | `product`; 2,298,328 sectors (`0x2311D8`); linear target type 0; physical target sector 2,048 (`0x800`); target source 0 (`super`) |
| LP extent entry 1 | `system`; 2,516,312 sectors (`0x266558`); linear target type 0; physical target sector 2,301,952 (`0x232000`); target source 0 (`super`) |
| LP extent entry 2 | `vendor`; 568,848 sectors (`0x8AE10`); linear target type 0; physical target sector 4,818,944 (`0x498800`); target source 0 (`super`) |
| LP group `default` | Flags 0; maximum size 0; contains no logical partition |
| LP group `main` | Flags 0; maximum size 4,292,870,144 bytes (`0xFFE00000`); contains `product`, `system`, and `vendor` |
| LP block-device entry | Name `super`; first logical sector 2,048; alignment 1,048,576 bytes; alignment offset 0; block-device size 4,294,967,296 bytes; flags 0 |
| Pre-data reserved zero span | `0x00043000–0x000FFFFF`; 774,144 bytes; completely zero-filled by direct full-span comparison |
| `product` logical mapping | Logical bytes `0x00000000–0x4623AFFF`; physical bytes `0x00100000–0x4633AFFF`; 2,298,328 sectors; 1,176,743,936 bytes; one linear extent; read-only; group `main` |
| `system` logical mapping | Logical bytes `0x00000000–0x4CCAAFFF`; physical bytes `0x46400000–0x930AAFFF`; 2,516,312 sectors; 1,288,351,744 bytes; one linear extent; read-only; group `main` |
| `vendor` logical mapping | Logical bytes `0x00000000–0x115C1FFF`; physical bytes `0x93100000–0xA46C1FFF`; 568,848 sectors; 291,250,176 bytes; one linear extent; read-only; group `main` |
| Inter-extent zero spans | `0x4633B000–0x463FFFFF`, 806,912 bytes, and `0x930AB000–0x930FFFFF`, 348,160 bytes; both completely zero-filled by direct full-span comparison |
| Terminal unused physical span | `0xA46C2000–0xFFFFFFFF`; 1,536,417,792 bytes; completely zero-filled by direct full-span comparison; independently confirmed at the terminal 16 KiB boundary |
| Logical filesystem class | All three logical extents begin with clean ext4 revision-1 filesystems using 4,096-byte blocks and 256-byte inodes; none advertises `has_journal` |
| Shared ext4 features | `ext_attr resize_inode dir_index filetype extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize`; filesystem flag `signed_directory_hash`; default mount options `user_xattr acl`; error behavior `Continue`; OS type `Linux` |
| Shared ext4 timestamp/mount state | Creation, last-write, and last-check values render as `Wed Dec 31 18:00:00 2008`; last mount time `n/a`; mount count 0; maximum mount count -1; check interval 0; lifetime writes 1 kB |
| `product` ext4 identity | Volume `product`; last-mounted path `/product`; UUID `453968ff-b43c-54b7-a506-a6a88682c128`; filesystem state `clean` |
| `product` ext4 geometry | 432 inodes; 282,737 blocks; 846 free blocks; 8 free inodes; 0 reserved blocks; 32,768 blocks per group; 48 inodes per group; 3 inode blocks per group; 69 reserved GDT blocks |
| `product` ext4 extent | Logical `0x00000000–0x45070FFF`; physical `0x00100000–0x45170FFF`; 1,158,090,752 bytes; exactly 282,737 × 4,096 bytes |
| `system` ext4 identity | Volume `/`; last-mounted path `/`; UUID `4729639d-b5f2-5cc1-a120-9ac5f788683c`; filesystem state `clean` |
| `system` ext4 geometry | 4,320 inodes; 309,554 blocks; 943 free blocks; 17 free inodes; 0 reserved blocks; 32,768 blocks per group; 432 inodes per group; 27 inode blocks per group; 75 reserved GDT blocks |
| `system` ext4 extent | Logical `0x00000000–0x4B931FFF`; physical `0x46400000–0x91D31FFF`; 1,267,933,184 bytes; exactly 309,554 × 4,096 bytes |
| `vendor` ext4 identity | Volume `vendor`; last-mounted path `/vendor`; UUID `2b96c597-1e2f-5ee1-9851-c4a9fa9de36e`; filesystem state `clean` |
| `vendor` ext4 geometry | 1,584 inodes; 69,962 blocks; 215 free blocks; 48 free inodes; 0 reserved blocks; 32,768 blocks per group; 528 inodes per group; 33 inode blocks per group; 17 reserved GDT blocks |
| `vendor` ext4 extent | Logical `0x00000000–0x11149FFF`; physical `0x93100000–0xA4249FFF`; 286,564,352 bytes; exactly 69,962 × 4,096 bytes |
| ext4 namespace boundary | The raw filesystems and allocation geometry are structurally established; a complete inode/path/payload/ownership/SELinux namespace census of the three large filesystems was not performed in this partition-record pass and remains `OPEN_BOUNDARY` |
| Shared appended-AVB layout | Each logical extent contains, in exact order after its ext4 image: SHA-256 dm-verity tree, two-root FEC data, 704-byte unsigned embedded VBMeta object, verified zero alignment, and 64-byte AVB footer |
| Shared embedded-VBMeta header | 256-byte header; required libavb 1.0; authentication block size 0; auxiliary block size 448; algorithm type 0 (`NONE`); hash/signature/public-key/public-key-metadata sizes 0; rollback index 0; flags 0; rollback-index location 0; release string `avbtool 1.1.0` |
| Shared embedded-VBMeta descriptor census | Three descriptors consume 416 bytes: one 256-byte tag-1 hashtree descriptor followed by 72-byte and 88-byte tag-0 property descriptors; the remaining 32 auxiliary bytes are zero alignment |
| Shared hashtree parameters | dm-verity version 1; data-block size 4,096; hash-block size 4,096; FEC root count 2; hash algorithm `sha256`; hashtree flags 0; salt length 32; root-digest length 32 |
| Embedded-VBMeta authentication boundary | All three embedded VBMeta objects use algorithm `NONE`, contain no authentication block, signature, or public key, and therefore do not independently authenticate their descriptors; external chained-VBMeta trust and bootloader enforcement remain `OPEN_BOUNDARY` |
| `product` complete logical layout | ext4 `0x00000000–0x45070FFF`; hashtree `0x45071000–0x45924FFF`; FEC `0x45925000–0x461F2FFF`; embedded VBMeta `0x461F3000–0x461F32BF`; zero alignment `0x461F32C0–0x4623AFBF`; AVB footer `0x4623AFC0–0x4623AFFF` |
| `product` logical extent ledger | 1,158,090,752 + 9,125,888 + 9,232,384 + 704 + 294,144 + 64 = 1,176,743,936 bytes; exact, contiguous, and nonoverlapping |
| `product` hashtree descriptor | Image size/tree offset `0x45071000`; tree size `0x008B4000`; FEC offset `0x45925000`; FEC size `0x008CE000`; partition `product`; name length 7 |
| `product` AVB salt | `29d8c4136ad5e0f7117ad8255f48cf905e4332924c4bbd546e2b08f715ab85df` |
| `product` encoded root digest | `9bbc62db4d1adda320bf1319305532b5fb5c7b4a22ef7946d88af1ef8f8fd75b`; independent hashtree/root-digest recomputation `OPEN_BOUNDARY` |
| `product` AVB properties | `com.android.build.product.os_version=10`; `com.android.build.product.security_patch=2024-03-05` |
| `product` AVB footer | Logical `0x4623AFC0–0x4623AFFF`; physical `0x4633AFC0–0x4633AFFF`; magic `AVBf`; version 1.0; original-image size `0x45071000`; VBMeta offset `0x461F3000`; VBMeta size `0x2C0`; 28 reserved bytes zero |
| `product` AVB alignment | Logical `0x461F32C0–0x4623AFBF`; 294,144 bytes; completely zero-filled by direct full-span comparison |
| `system` complete logical layout | ext4 `0x00000000–0x4B931FFF`; hashtree `0x4B932000–0x4C2B8FFF`; FEC `0x4C2B9000–0x4CC5CFFF`; embedded VBMeta `0x4CC5D000–0x4CC5D2BF`; zero alignment `0x4CC5D2C0–0x4CCAAFBF`; AVB footer `0x4CCAAFC0–0x4CCAAFFF` |
| `system` logical extent ledger | 1,267,933,184 + 9,990,144 + 10,108,928 + 704 + 318,720 + 64 = 1,288,351,744 bytes; exact, contiguous, and nonoverlapping |
| `system` hashtree descriptor | Image size/tree offset `0x4B932000`; tree size `0x00987000`; FEC offset `0x4C2B9000`; FEC size `0x009A4000`; partition `system`; name length 6 |
| `system` AVB salt | `ea1ae6ff4590b39129e0320ce6b653c88b2699b72a58fd8c5a194ae949222f35` |
| `system` encoded root digest | `59ed1e411a8d2490384aaed4dac2c7f09045257e895c8e9aab8f4ae14680d53d`; independent hashtree/root-digest recomputation `OPEN_BOUNDARY` |
| `system` AVB properties | `com.android.build.system.os_version=10`; `com.android.build.system.security_patch=2024-03-05` |
| `system` AVB footer | Logical `0x4CCAAFC0–0x4CCAAFFF`; physical `0x930AAFC0–0x930AAFFF`; magic `AVBf`; version 1.0; original-image size `0x4B932000`; VBMeta offset `0x4CC5D000`; VBMeta size `0x2C0`; 28 reserved bytes zero |
| `system` AVB alignment | Logical `0x4CC5D2C0–0x4CCAAFBF`; 318,720 bytes; completely zero-filled by direct full-span comparison |
| `vendor` complete logical layout | ext4 `0x00000000–0x11149FFF`; hashtree `0x1114A000–0x11372FFF`; FEC `0x11373000–0x115A0FFF`; embedded VBMeta `0x115A1000–0x115A12BF`; zero alignment `0x115A12C0–0x115C1FBF`; AVB footer `0x115C1FC0–0x115C1FFF` |
| `vendor` logical extent ledger | 286,564,352 + 2,265,088 + 2,285,568 + 704 + 134,400 + 64 = 291,250,176 bytes; exact, contiguous, and nonoverlapping |
| `vendor` hashtree descriptor | Image size/tree offset `0x1114A000`; tree size `0x00229000`; FEC offset `0x11373000`; FEC size `0x0022E000`; partition `vendor`; name length 6 |
| `vendor` AVB salt | `b5431e41fdce7b719a00798fed1ea74efe6edef9654a632a5d7d8904404fa107` |
| `vendor` encoded root digest | `0765a6ff730b03b8bea3c3670ca44134a99f3b691e90290ecadd192f1e8cd1db`; independent hashtree/root-digest recomputation `OPEN_BOUNDARY` |
| `vendor` AVB properties | `com.android.build.vendor.os_version=10`; `com.android.build.vendor.security_patch=2020-04-05` |
| `vendor` AVB footer | Logical `0x115C1FC0–0x115C1FFF`; physical `0xA46C1FC0–0xA46C1FFF`; magic `AVBf`; version 1.0; original-image size `0x1114A000`; VBMeta offset `0x115A1000`; VBMeta size `0x2C0`; 28 reserved bytes zero |
| `vendor` AVB alignment | Logical `0x115A12C0–0x115C1FBF`; 134,400 bytes; completely zero-filled by direct full-span comparison |
| AVB extent reconciliation | For every child, footer `original_image_size` equals the exact ext4 block count multiplied by 4,096; descriptor `image_size` and `tree_offset` equal the same value; tree end equals FEC offset; FEC end equals embedded-VBMeta offset; the 704-byte VBMeta, verified zero alignment, and 64-byte footer end exactly at the LP extent boundary |
| Cryptographic-verification boundary | Encoded salts, root digests, tree/FEC sizes, offsets, and algorithms are structurally decoded. The complete dm-verity trees and FEC regions were not independently recomputed against their ext4 data during this pass, so cryptographic equality and correction capability remain `OPEN_BOUNDARY` |
| Runtime-activation boundary | The raw container establishes three populated read-only logical partitions and their physical mappings. This object alone does not prove which device-mapper targets were instantiated or which filesystems were mounted during a particular boot; runtime activation remains outside raw-image authority |
| Semantic ASCII content | Directly bounded structural text includes `product`, `system`, `vendor`, `default`, `main`, `super`, `avbtool 1.1.0`, `sha256`, the three AVB partition names, and six Android build-property keys/values; filesystem namespace text is contained inside the ext4 data regions |
| Whole-partition ASCII census | Full occurrence/uniqueness census was not captured for this 4 GiB object; `OPEN_BOUNDARY` |
| Whole-partition UTF-16LE census | Not captured for this 4 GiB object; `OPEN_BOUNDARY` |
| Zero-fill verification | Leading reserved block, geometry padding, pre-data reserved span, both inter-extent gaps, all three AVB alignment spans, and the complete 1,536,417,792-byte terminal span are directly established as zero-filled within their stated boundaries |
| Byte-accounting closure | The physical-container ledger equals 4,294,967,296 bytes exactly; each of the three child ledgers independently equals its LP extent size exactly; no physical or logical byte remains unassigned |
| SHA-256 verification | `sha256sum` produced `247e84069af75a4fc248c398338db06a8bbf80373f2b8f8e225dafccb443cd97` only after structural closure; command status 0 |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; raw `super.bin` is authority for the recorded container bytes; runtime use and factory-relative modification state `OPEN_BOUNDARY` |
| Content classification | `ANDROID_LP_V10_DYNAMIC_SUPER_CONTAINER_WITH_THREE_READONLY_EXT4_SHA256_HASHTREE_FEC_UNSIGNED_AVB_LOGICAL_PARTITIONS` |

## p34 — `vbmeta`
| p34 | `vbmeta` | 8,388,608 | Structurally complete AVB root-metadata image containing four chain-partition descriptors and one directly validated salted `dtbo` hash descriptor; the captured flags word `3` is a proven post-signing mutation because the captured header fails its stored digest and signature while restoring only that four-byte word to `0` validates both exactly | `CURRENT_RESEARCH_CAPTURE`; encoded hashtree-disable and verification-disable flags; external root-key trust, chained-target key/signature matches, actual bootloader enforcement, mutation actor/time/method, and factory-source provenance `OPEN_BOUNDARY` | `CLOSED` | `21571196f5497effb81ba3f1e81e9bf922696ad1193b72ab5495a0dea73d7256` |

| Field | Verified value |
|---|---|
| Evidence object | `vbmeta.bin` |
| Object size | 8,388,608 bytes; `0x00800000` |
| Acquisition completeness | Expected from GPT: 8,388,608 bytes; received: 8,388,608 bytes; difference: 0 |
| Partition identity | p34; `vbmeta`; `/dev/block/mmcblk0p34`; major:minor `259:2` |
| GPT geometry | Start LBA 9,453,568 (`0x904000`); end LBA 9,469,951 (`0x907FFF`) inclusive; 16,384 sectors; byte extent on parent device `0x0000000120800000–0x0000000120FFFFFF` |
| GPT unique GUID | `a4da8f1b-fe07-433b-95cb-84a5f23e477b` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0` |
| Kernel block state | Read-only state `0`; alignment offset 0; discard alignment 0; holder count 0; raw GPT and live sysfs geometry match |
| Device-node metadata | `brw-------`; owner/group `root:root`; target context `u:object_r:vbmeta_block_device:s0` |
| Alias contexts | Root by-name alias context `u:object_r:block_device:s0`; platform by-name alias context `u:object_r:vbmeta_block_device:s0`; the platform-specialized alias is realized live |
| Aliases | By-name alias `vbmeta`; fstab alias `/vbmeta`; fstab type `emmc` |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| AVB magic census | Literal `AVB0` occurs exactly once at `0x000000`; no `AVBf` footer occurs anywhere in the partition |
| Negative standard-signature census | No ELF, ZIP, gzip, XZ, Zstandard, LZ4-frame, bzip2, PNG, SQLite, Android boot-image, Android sparse-image, FDT, DTBO-container, or recognized filesystem signature was established anywhere in the partition |
| Complete top-level layout | AVB header `0x000000–0x0000FF`; authentication block `0x000100–0x00023F`; auxiliary block `0x000240–0x000EFF`; terminal zero fill `0x000F00–0x7FFFFF` |
| Structural extent ledger | 256-byte header + 320-byte authentication block + 3,264-byte auxiliary block + 8,384,768-byte terminal zero fill = 8,388,608 bytes; all extents are contiguous and nonoverlapping, with no unassigned byte |
| AVB object extent | `0x000000–0x000EFF`; exactly `0xF00` = 3,840 bytes, including required block padding |
| AVB header extent | `0x000000–0x0000FF`; exactly 256 bytes |
| AVB serialization | Header, descriptor, and serialized-key integer fields decode consistently as big-endian/network byte order |
| Required libavb version | Major 1; minor 0 |
| AVB algorithm | Header algorithm type 1; `SHA256_RSA2048`; 32-byte digest and 256-byte RSA signature extents reconcile exactly |
| Header-declared block sizes | Authentication block `0x140` = 320 bytes; auxiliary block `0xCC0` = 3,264 bytes; `256 + 320 + 3,264 = 3,840` bytes |
| Authentication offsets | Hash offset 0, size 32; signature offset 32, size 256, both relative to authentication-block start `0x000100`; remaining 32 bytes are alignment fill |
| Auxiliary offsets | Descriptor offset 0, size `0xA90` = 2,704; root public-key offset `0xA90`, size `0x208` = 520; public-key-metadata offset `0xC98`, size 0; all offsets are relative to auxiliary-block start `0x000240` |
| Rollback fields | Rollback index 0; top-level rollback-index location 0 |
| Captured AVB flags | `0x00000003` at header `0x000078–0x00007B`; bit 0 encodes hashtree disabled and bit 1 encodes verification disabled |
| Release string | Literal `avbtool 1.1.0` at `0x000080`; remaining bytes in the 48-byte release-string field are zero |
| Header reserved field | `0x0000B0–0x0000FF`; exactly 80 bytes; completely zero-filled |
| Stored authentication digest | `07532328ee067be97428d9a476b796ae1257705f7264f437b0fbd0b8391ed7f6` at `0x000100–0x00011F` |
| Captured-object digest result | SHA-256 over the captured 256-byte header concatenated with the complete 3,264-byte auxiliary block is `c3816c8445ade8839df6e487c8a37d0e1372eae9522a9474d3d00ac385023ec5`, which does not equal the stored authentication digest; current self-authentication result is therefore `HASH_MISMATCH` |
| Four-byte restoration test | Replacing only header bytes `0x000078–0x00007B` from `00 00 00 03` with `00 00 00 00`, while leaving every other header and auxiliary byte unchanged, produces SHA-256 `07532328ee067be97428d9a476b796ae1257705f7264f437b0fbd0b8391ed7f6`, exactly equal to the stored digest |
| Captured signature result | Direct RSA PKCS#1 v1.5 / SHA-256 verification over the captured header plus auxiliary block fails, consistent with the digest mismatch |
| Restored-header signature result | The same 256-byte signature verifies against the embedded root RSA key when only the four-byte flags word is restored to zero; independent raw RSA decoding yields a correct SHA-256 DigestInfo carrying the stored digest |
| Post-signing mutation finding | The authenticated form binds flags `0`; the captured form contains flags `3`. Exact digest equality and direct RSA verification after the single four-byte restoration establish a post-signing `0 → 3` flags mutation. No resigning accompanied the change |
| Mutation provenance boundary | The bytes establish what changed relative to the signed form, but do not identify the actor, utility, time, host, or acquisition-stage event responsible; those remain `OPEN_BOUNDARY` |
| Enforcement boundary | Flags `3` encode disabled hashtree and verification behavior, but this image alone cannot establish how the device bootloader handled the unauthenticated modified header during any specific boot; actual enforcement behavior remains `OPEN_BOUNDARY` |
| Authentication-block layout | Stored digest `0x000100–0x00011F`; RSA signature `0x000120–0x00021F`; zero alignment `0x000220–0x00023F` |
| Authentication alignment | `0x000220–0x00023F`; exactly 32 bytes; completely zero-filled |
| Root public-key extent | `0x000CD0–0x000ED7`; exactly 520 bytes in AVB serialized RSA-key format |
| Root public-key structure | 2,048-bit modulus; implicit exponent 65,537; encoded `n0inv` `0xC9D87D7B`; modulus has exact 2,048-bit length |
| Root public-key arithmetic | Encoded `n0inv` equals `−n⁻¹ mod 2³²`; encoded 256-byte `rr` equals `2⁴⁰⁹⁶ mod n`; both internal Montgomery values validate exactly |
| Root trust boundary | The embedded key validates this object's stored signature after flags restoration, but no independently trusted bootloader key store or fused trust-anchor evidence was supplied; external trust of this key remains `OPEN_BOUNDARY` |
| Descriptor census | Exactly five descriptors consume the declared 2,704-byte descriptor area: four tag-4 chain-partition descriptors followed by one tag-2 hash descriptor; no unknown tag or residual descriptor byte remains |
| Descriptor extent ledger | 624 + 632 + 632 + 616 + 200 = 2,704 bytes; descriptor spans are contiguous from `0x000240` through `0x000CCF` |
| Chain descriptor 1 | `0x000240–0x0004AF`; 624 bytes; partition `recovery`; rollback-index location 1; name length 8; public-key length 520; descriptor flags 0; 60 reserved zero bytes; 4 terminal padding zeros |
| Chain descriptor 2 | `0x0004B0–0x000727`; 632 bytes; partition `vbmeta_system`; rollback-index location 2; name length 13; public-key length 520; descriptor flags 0; 60 reserved zero bytes; 7 terminal padding zeros |
| Chain descriptor 3 | `0x000728–0x00099F`; 632 bytes; partition `vbmeta_vendor`; rollback-index location 4; name length 13; public-key length 520; descriptor flags 0; 60 reserved zero bytes; 7 terminal padding zeros |
| Chain descriptor 4 | `0x0009A0–0x000C07`; 616 bytes; partition `boot`; rollback-index location 3; name length 4; public-key length 520; descriptor flags 0; 60 reserved zero bytes; no terminal padding |
| Delegated public keys | Each chain descriptor contains one structurally valid 520-byte AVB RSA-2048 key with exponent 65,537; all four encoded `n0inv` and `rr` values pass direct arithmetic validation |
| Key relationship census | The four delegated serialized keys are mutually distinct and each is distinct from the embedded root key; equality was tested directly on complete 520-byte key blobs, without relying on fingerprints |
| Chained-target verification boundary | `recovery`, `boot`, `vbmeta_system`, and `vbmeta_vendor` evidence objects were not supplied for this pass; their embedded signing-key equality, object signatures, rollback values, and descriptor payloads therefore remain `OPEN_BOUNDARY` |
| Direct hash descriptor | `0x000C08–0x000CCF`; 200 bytes; tag 2; 184 bytes following the common descriptor header; image size 33,744 bytes; algorithm literal `sha256`; partition `dtbo`; name length 4; salt length 32; digest length 32; flags 0; 60 reserved zero bytes; no terminal padding |
| `dtbo` descriptor salt | `f0b811c76a5dd838af23cc5fcd69405d5ec71be8ac01354723f02895b8c8edec` |
| `dtbo` descriptor digest | `34df441cacdc9d852b72cbe166abe44f22c0eae684c2f64cd3d85f998ffb500f` |
| Direct `dtbo` validation | Independent SHA-256 over the descriptor's 32-byte salt followed by exactly bytes `0x000000–0x0083CF` of the supplied `dtbo.bin` produces the encoded digest exactly; bytes after the declared 33,744-byte image span are not covered by this descriptor |
| `dtbo` cross-record reconciliation | Descriptor image size 33,744 bytes exactly equals p30's AVB-declared original-image size; the verified span contains p30's DTBO table and its included pre-AVB padding |
| Descriptor authentication scope | In the restored-flags form, the stored digest and valid RSA signature cover the entire header and auxiliary block, including all five descriptors and all five serialized public keys |
| Public-key metadata | Declared size 0; no metadata payload is present |
| Auxiliary alignment | `0x000ED8–0x000EFF`; exactly 40 bytes; completely zero-filled |
| Terminal zero span | `0x000F00–0x7FFFFF`; exactly 8,384,768 bytes; completely zero-filled |
| First/last nonzero byte | First `0x000000`; last `0x000ED7` |
| Used extent through last nonzero | `0x000000–0x000ED7`; exactly 3,800 bytes; the final 40 bytes of the formal AVB object are zero alignment |
| Partition byte counts | 3,041 nonzero bytes; 8,385,567 zero bytes; 5 bytes equal `0xFF`; all 256 possible byte values occur |
| Populated 4 KiB blocks | One block only, index 0 (`0x000000–0x000FFF`); the exact terminal-zero boundary begins inside that block at `0x000F00`; blocks 1–2047 are completely zero-filled |
| ASCII strings | 44 occurrences at minimum length 4; 44 unique; length range 4–13 bytes. Semantic cleartext is `AVB0`, `avbtool 1.1.0`, `recovery`, `vbmeta_system`, `vbmeta_vendor`, `boot`, `sha256`, and `dtbo`; the other short runs occur inside cryptographic fields |
| UTF-16LE strings | Zero occurrences at minimum length 4 |
| Prior `null vbmeta` label reconciliation | The supplied object is neither empty nor unsigned: it contains a SHA256_RSA2048 authentication block, a root key, four delegated keys, four chain descriptors, and one hash descriptor. `NULL_VBMETA` is rejected as a literal content classification; only the captured disable-flags state is preserved |
| Independent fill verification | The 80-byte header-reserved field, 32-byte authentication alignment, 40-byte auxiliary alignment, and 8,384,768-byte terminal span each passed direct byte-count validation and independent comparison against a zero stream |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `21571196f5497effb81ba3f1e81e9bf922696ad1193b72ab5495a0dea73d7256` only after structural and authentication-state closure |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; directly established post-signing flags mutation; factory-source provenance `OPEN_BOUNDARY` |
| Content classification | `AVB_ROOT_VBMETA_SHA256_RSA2048_WITH_FOUR_CHAIN_DESCRIPTORS_ONE_VALIDATED_DTBO_HASH_AND_POST_SIGNING_FLAGS_0_TO_3_MUTATION` |

## p35 — `cache`
| p35 | `cache` | 452,984,832 | Structurally consistent ext4 `/cache` filesystem containing five live directories, two 833-byte Magisk research logs, one zero-length recovery-log placeholder, a committed and fully checkpointed journal-recovery set, five deleted zero-length recovery-file namespace records preserved in directory slack and stale free inodes, and 421,605,376 bytes of completely zero-filled free space | `CURRENT_RESEARCH_CAPTURE`; Magisk and Substratum log content is owner-research state; deleted file payloads, historical journal-slot ownership where descriptors were overwritten, and factory-relative cache history `OPEN_BOUNDARY` | `CLOSED` | `acd28ef933a6eb09f681b6324c546efbebe562be23315cb6bdc44aff210fb671` |

| Field | Verified value |
|---|---|
| Evidence object | `cache.bin` |
| Acquisition completeness | Expected from GPT: 452,984,832 bytes; received: 452,984,832 bytes; difference: 0 |
| Received-file allocation | 884,736 allocated 512-byte host blocks = 452,984,832 bytes; the received object is not sparse |
| GPT partition | p35; `cache` |
| Physical block device | `/dev/block/mmcblk0p35`; major:minor `259:3` |
| By-name mapping | `/dev/block/by-name/cache` → `/dev/block/mmcblk0p35` |
| Device-node mode and ownership | `brw-------`; `root:root` |
| Observed partition-node SELinux context | `u:object_r:block_device:s0` |
| Observed by-name SELinux context | `u:object_r:block_device:s0` |
| Static-policy context for the cache alias | `u:object_r:cache_block_device:s0`; specialized policy label was not realized on the captured live alias |
| GPT start LBA | 9,469,952 |
| GPT end LBA | 10,354,687 inclusive |
| GPT sector count | 884,736 sectors |
| GPT partition size | 452,984,832 bytes; 432 MiB |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT unique partition GUID | `c2635e15-61aa-454e-9c40-ebe1bdf19b9b` |
| GPT attributes | `0x0000000000000000` |
| Object size | 452,984,832 bytes; `0x1B000000`; 432 MiB |
| Evidence-object extent | `0x00000000–0x1AFFFFFF` |
| Size reconciliation | 884,736 × 512-byte GPT sectors = 452,984,832 bytes; exact |
| Recognized filesystem | Linux ext4 revision 1; dynamic revision; entire evidence object |
| `file -k` classification | Linux ext4 filesystem data; `needs journal recovery`; extents; large-file and huge-file support |
| `blkid -p` classification | ext4; probe status 0; filesystem size exactly 452,984,832 bytes |
| Filesystem UUID | `e85e6a3d-0f1c-473e-8757-5ffc1e2decef` |
| Filesystem volume name | NONE |
| Last-mounted path | `/cache` |
| Confirmed runtime role | Active physical ext4 mount at `/cache` |
| Captured mount options | `rw,seclabel,nosuid,nodev,noatime,discard,noauto_da_alloc,data=ordered` |
| Filesystem magic | `0xEF53` |
| Block and fragment size | 4,096 bytes |
| Filesystem geometry | 110,592 blocks; four block groups; 32,768 blocks per full group; final group 12,288 blocks |
| Inode geometry | 110,592 inodes; 27,648 inodes per group; 128-byte inode records; first ordinary inode 11 |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype needs_recovery extent sparse_super large_file huge_file uninit_bg dir_nlink extra_isize` |
| Default mount features | `user_xattr acl`; unsigned directory hashes; `half_md4` directory hashing |
| Superblock state split | Superblock state field reports `clean`, while the ext4 incompatibility feature set includes `needs_recovery` and the JBD2 journal start is nonzero |
| Read-only consistency check | `e2fsck -f -n` skipped journal replay, completed all five passes with status 0, and reported no inode, block, directory, connectivity, reference-count, or group-summary error |
| Mount and write counters | Mount count 1; maximum mount count disabled; lifetime writes 403 MB |
| Used inodes | 17 total: nine reserved non-namespace inodes plus five directories and three regular files |
| Free inodes | 110,575 |
| Used blocks | 7,661 blocks; 31,379,456 bytes; 6.93% |
| Free blocks | 102,931 blocks; 421,605,376 bytes; 93.07% |
| Reserved blocks | 1,105 blocks; reserved UID/GID `0:0` |
| Used-block reconciliation | 3,549 filesystem-structure/resize-inode blocks + 4,096 journal blocks + 8 directory blocks + 2 live regular-file data blocks + 6 external-xattr blocks = 7,661 blocks; exact |
| Group 0 structures | Blocks `0–893` contain the primary superblock, group descriptors, 26 reserved-GDT blocks, block bitmap 28, inode bitmap 29, and inode table `30–893`; resize inode double-indirect block 899 |
| Group 1 structures | Backup superblock 32768; backup group-descriptor block 32769; reserved-GDT blocks `32770–32795`; block bitmap 32796; inode bitmap 32797; inode table `32798–33661` |
| Group 2 structures | Block bitmap 65536; inode bitmap 65537; inode table `65538–66401`; no sparse-super backup in this group |
| Group 3 structures | Backup superblock 98304; backup group-descriptor block 98305; reserved-GDT blocks `98306–98331`; block bitmap 98332; inode bitmap 98333; inode table `98334–99197` |
| Resize inode | Inode 7; double-indirect block 899; indirect blocks `2–27`; references the 26 reserved-GDT blocks in groups 1 and 3; block accounting closes exactly |
| Free-block extent 1 | Blocks `903–1023`; `0x00387000–0x003FFFFF`; 495,616 bytes; completely zero |
| Free-block extent 2 | Blocks `1025–1535`; `0x00401000–0x005FFFFF`; 2,093,056 bytes; completely zero |
| Free-block extent 3 | Blocks `1537–32767`; `0x00601000–0x07FFFFFF`; 127,922,176 bytes; completely zero |
| Free-block extent 4 | Blocks `33664–65535`; `0x08380000–0x0FFFFFFF`; 130,547,712 bytes; completely zero |
| Free-block extent 5 | Blocks `70502–98303`; `0x11366000–0x17FFFFFF`; 113,876,992 bytes; completely zero |
| Free-block extent 6 | Blocks `99198–110591`; `0x1837E000–0x1AFFFFFF`; 46,669,824 bytes; completely zero |
| Free-space verification | Complete group-bitmap-driven scan accounted for all 102,931 free blocks; every free block is byte-for-byte zero; free-space nonzero-byte count 0 |
| Completely zero physical blocks | 110,480 of 110,592: 102,931 free blocks plus 7,549 allocated-but-zero blocks |
| Nonzero physical blocks | 112 mixed-content blocks; no completely `0xFF` block |
| Nonzero 4 KiB run 1 | Blocks `0–30`; `0x00000000–0x0001EFFF` |
| Nonzero 4 KiB run 2 | Blocks `894–902`; `0x0037E000–0x00386FFF` |
| Nonzero 4 KiB runs 3–4 | Block 1024, `0x00400000–0x00400FFF`; block 1536, `0x00600000–0x00600FFF` |
| Nonzero 4 KiB runs 5–8 | Blocks `32768–32769`, `32796–32798`, `33662–33663`, and `65536–65538` |
| Nonzero 4 KiB runs 9–12 | Blocks `66402–66454`, `70498–70501`, `98304–98305`, and block 98332 |
| Root directory | Inode 2; mode `0770`; UID/GID `1000:2001` (`system:cache`); block 894, `0x0037E000–0x0037EFFF` |
| Root security attributes | `security.selinux="u:object_r:cache_file:s0\0"`; 20-byte `security.sehash`; external xattr block 901 |
| Root entries | `lost+found`, `recovery`, `backup_stage`, `backup`, `magisk.log`, and `magisk.log.bak` |
| `/lost+found` | Inode 11; directory; mode `0770`; UID/GID `0:0`; blocks `895–898`; no live child entry |
| `/lost+found` security attributes | `u:object_r:cache_file:s0`; 20-byte `security.sehash`; external xattr block 900 |
| `/recovery` | Inode 27649; directory; mode `0770`; UID/GID `1000:2001` (`system:cache`); block 33663, `0x0837F000–0x0837FFFF` |
| `/recovery` security attribute | `u:object_r:cache_recovery_file:s0`; external xattr block 33662 |
| `/recovery/last_log.gz` | Inode 27654; regular file; mode `0640`; UID/GID `0:0`; logical size 0; no data extent; no gzip header or payload |
| `/backup_stage` | Inode 55297; empty directory; mode `0700`; UID/GID `1000:1000` (`system:system`); block 70499; SELinux context `u:object_r:cache_backup_file:s0`; external xattr block 70498 |
| `/backup` | Inode 55298; empty directory; mode `0700`; UID/GID `1000:1000` (`system:system`); block 70501; SELinux context `u:object_r:cache_private_backup_file:s0`; external xattr block 70500 |
| Live regular-file count | Three: two populated Magisk logs and zero-length `/recovery/last_log.gz` |
| Live regular-file logical bytes | 1,666 bytes total |
| `/magisk.log.bak` | Inode 12; mode `0666`; UID/GID `0:0`; size 833 bytes; block 1024, `0x00400000–0x00400FFF`; SELinux context `u:object_r:cache_file:s0` |
| `/magisk.log` | Inode 13; mode `0666`; UID/GID `0:0`; size 833 bytes; block 1536, `0x00600000–0x00600FFF`; SELinux context `u:object_r:cache_file:s0` |
| Magisk-log xattr relationship | Both log files share external xattr block 902; on-disk reference count 2 exactly accounts for both owners |
| Magisk-log allocated slack | 3,263 bytes per allocated block; 6,526 bytes total; independently verified completely zero |
| Magisk-log structure | Each file contains 12 newline-terminated ASCII log records and no binary or NUL byte; the files differ at 136 byte positions |
| Magisk-log semantic comparison | Removing the date/time and PID prefixes yields the same ordered 12-message sequence in both files |
| Magisk-log messages | Magisk `30.6(30600)` start; API level 29; post-fs-data; environment initialization; post-fs-data.d and module scripts; `projekt.substratum.lite.helper` module loading; late-start service mode; service.d and module scripts; boot-complete; preinit directory `/data/unencrypted/magisk` |
| Research-state attribution | The two Magisk logs and Substratum helper reference are current owner-research modifications and are not assigned stock or factory provenance |
| Directory-slack scan | Eight live directory blocks contain 32,488 slack bytes; 32,415 are zero and 73 are nonzero bytes belonging exclusively to five deleted `/recovery` directory entries |
| Deleted recovery entry 1 | `.version`; directory-block relative offset `0x18`, absolute `0x0837F018`; stale inode 27650; mode `0666`; UID/GID `0:0`; zero links; size 0; no data extent |
| Deleted recovery entry 2 | `recovery.fstab`; relative offset `0x28`, absolute `0x0837F028`; stale inode 27651; mode `0644`; UID/GID `0:0`; zero links; size 0; no data extent |
| Deleted recovery entry 3 | `storage.fstab`; relative offset `0x40`, absolute `0x0837F040`; stale inode 27652; mode `0666`; UID/GID `0:0`; zero links; size 0; no data extent |
| Deleted recovery entry 4 | `log.gz`; relative offset `0x58`, absolute `0x0837F058`; stale inode 27653; mode `0600`; UID/GID `1000:1000`; zero links; size 0; no data extent |
| Deleted recovery entry 5 | `intent`; relative offset `0x7C`, absolute `0x0837F07C`; stale inode 27655; mode `0666`; UID/GID `0:0`; zero links; size 0; no data extent |
| Deleted-entry logical state | `.version`, `recovery.fstab`, `storage.fstab`, and `log.gz` are consumed by the live `..` entry's `rec_len=0x005C`; `intent` is consumed by live `last_log.gz` with `rec_len=0x0F98`; none is live |
| Free-inode scan | Complete inode-bitmap/table scan found exactly five nonzero unallocated inode records—27650, 27651, 27652, 27653, and 27655—matching the five deleted directory entries; all other 110,570 free inode records are zero |
| Deleted-inode tool split | `debugfs lsdel` emitted zero entries, but the independent bitmap/table scan and directory-slack decode resolve the five stale unallocated records directly; `lsdel` output is not used to erase that evidence |
| Deleted-inode xattr residue | All five stale inodes retain external-xattr pointer 33662 and decode to `u:object_r:cache_recovery_file:s0`; because the inodes are free, the current xattr-block reference count correctly remains 2 for `/recovery` and live `last_log.gz` only |
| Recoverable deleted payload | NONE; all five stale inodes have size 0, zero data blocks, and no extent, while every bitmap-free block is zero; only filenames and inode metadata survive |
| Journal | JBD2 inode 8; 16,777,216 bytes; 4,096 blocks; physical blocks `66402–70497`, `0x10362000–0x11361FFF` |
| Journal state | ext4 `needs_recovery`; JBD2 feature set empty; journal sequence `0x000001CA`; journal start block 1 |
| Active journal transactions | Three complete committed transactions: 458, 459, and 460; descriptor/commit blocks `1/11`, `12/14`, and `15/17` respectively |
| Active journal data | Transaction 458 logs nine data blocks; transactions 459 and 460 each log one later version of filesystem block 30; 11 logged data blocks address nine distinct filesystem targets |
| Active journal targets | Filesystem blocks 0, 1, 28, 29, 30, 894, 902, 32798, and 65538 |
| Journal checkpoint verification | Every current filesystem target equals the byte-for-byte latest committed journal version; all nine latest-target comparisons pass, so replay introduces no namespace or metadata change beyond clearing recovery state |
| Journal historical headers | Residual descriptor and/or commit headers remain for sequences 228, 229, 274, 275, 276, 283, 302, and 420 |
| Journal nonzero extent | Journal logical blocks `0–52`, physical `0x10362000–0x10396FFF`; 53 nonzero blocks containing 5,799 nonzero bytes |
| Journal terminal zero extent | Journal logical blocks `53–4095`, physical `0x10397000–0x11361FFF`; 4,043 blocks; completely zero |
| Historical recovery namespace witness | Residual journal logical block 45 at `0x1038F000` preserves a prior ext4 directory image in which `.version`, `recovery.fstab`, `storage.fstab`, `log.gz`, `last_log.gz`, and `intent` are all live entries |
| Historical recovery-allocation witness | Residual journal logical block 42 preserves inode-bitmap allocation for inodes 27649–27655, and logical block 44 preserves external-xattr block 33662 with reference count 7; these values exactly account for the directory plus six files before five deletions |
| Historical directory transition | The current and historical recovery-directory blocks differ at only five record-length bytes; the current lengths merge the five deleted entries into live-entry slack without destroying their pathname bytes |
| Historical journal-slot boundary | The descriptor mapping for the journal data cluster containing logical blocks 42–46 has been overwritten; its directory, inode-bitmap, and xattr relationships are structurally identified, but exact transaction ownership is `OPEN_BOUNDARY` |
| Journal-resident file payload | NONE identified; journal strings and decoded structures are filesystem metadata, xattrs, and current/historical directory names, not regular-file content |
| Full-partition zero-byte count | 452,970,219 |
| Full-partition nonzero-byte count | 14,613 |
| Full-partition `0xFF`-byte count | 8,041; all are filesystem-metadata bytes |
| Distinct byte values | 215 |
| Shannon entropy | `0.000654851430` bits per byte |
| First nonzero byte | `0x00000401`; inside the primary ext4 superblock |
| Last nonzero byte | `0x1801CFFF`; final byte of group 3's block bitmap |
| Terminal zero-filled extent | `0x1801D000–0x1AFFFFFF`; 50,212,864 bytes |
| ASCII strings | 82 occurrences; 48 unique strings; length range 4–87 bytes |
| Meaningful ASCII | `/cache`; live and deleted pathnames; SELinux attribute names and contexts; two 12-line Magisk logs; JBD2 copies of filesystem metadata |
| UTF-16LE strings | NONE at minimum length 4 |
| Embedded-signature scan | Outer ext4 and JBD2 structures only; no ELF, ZIP, gzip payload, PNG, SQLite, Android boot-image, AVB, FDT, DTBO-table, Android sparse-image, GPT, or `LDI` signature |
| Internal partition table | NONE |
| Source-to-upload identity | The received object is exactly GPT-sized, non-sparse, and internally closed; a source-host SHA-256 witness was not supplied, so identity with the host-side source file remains `OPEN_BOUNDARY` |
| Timestamp treatment | Captured 2026 filesystem and Magisk-log times are research-session state and are not used to infer stock chronology; other stale inode time fields are preserved in the image but not assigned unsupported lifecycle meaning |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `acd28ef933a6eb09f681b6324c546efbebe562be23315cb6bdc44aff210fb671` |
| Content classification | `EXT4_ACTIVE_CACHE_WITH_TWO_MAGISK_LOGS_ZEROED_FREE_SPACE_DELETED_ZERO_LENGTH_RECOVERY_NAMESPACE_AND_CHECKPOINTED_PENDING_JOURNAL` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `OWNER_RESEARCH_MODIFIED_CACHE_STATE`; `NEEDS_RECOVERY_WITH_LATEST_TRANSACTIONS_FULLY_CHECKPOINTED` |
| Closure statement | The complete 452,984,832-byte object, filesystem geometry, live namespace, allocation, journal, free space, file slack, directory slack, stale inode records, and deleted-name residue are closed; unavailable deleted payload history, factory-relative chronology, overwritten historical journal descriptors, and source-host identity remain explicit `OPEN_BOUNDARY` items |

## p36 — `userdata`
| p36 | `userdata` | 119,706,467,840 | Clean-state ext4 `/data` filesystem, FBE v1 (AES-256-XTS contents / AES-256-CTS filenames) with 7,307,264 inodes and 4,788 directories; top-level namespace census complete (44 root entries, quota inodes 3/4, journal inode 8, `/unencrypted` FBE key-management tree, `/rollback` session, `/system_ce/0` CE root); all Android-managed subtrees (`/system`, `/misc`, `/adb`, `/app`, `/backup`, `/anr`, `/property`) filename-encrypted at the ext4 layer, closure limited to structural metadata (mode/UID:GID/size/mtime/encrypted-name-length) | `CURRENT_RESEARCH_CAPTURE`; `OWNER_RESEARCH_MODIFIED` (live Magisk preinit/sepolicy staging present under `/unencrypted/magisk`); `CONTRADICTION_CONFIRMED` — this session's SHA-256 does not match the pre-existing database "archived" SHA-256 for the identical-size, identical-mtime file; source mechanism `OPEN_BOUNDARY` | `CLOSED` | `00baa4a99e47cdcab5aeffbf7ac1e9351d8da64d06ad7bb9024f3b280f2e8a9b` |

| Field | Verified value |
|---|---|
| Evidence object | `/home/beright1976/Downloads/FULL_FORENSIC_DUMP_C17S/userdata.bin` |
| GPT partition | p36; `userdata` |
| Physical block device | `/dev/block/mmcblk0p36`; major:minor `259:4` |
| By-name mapping | `/dev/block/by-name/userdata` → `/dev/block/mmcblk0p36` |
| GPT start LBA | 10,354,688 |
| GPT end LBA | 244,156,382 inclusive |
| GPT sector count | 233,801,695 |
| GPT unique partition GUID | `4d2d1290-36a3-4f5d-afb4-319f8ab6dcd8` |
| Object size | 119,706,467,840 bytes; 233,801,695 × 512-byte GPT sectors, exact |
| Host file allocation | 233,801,744 host blocks (`stat`); `du --apparent-size` = 119,706,467,840; `du` (actual) = 119,706,492,928 — allocated slightly above apparent size, consistent with host filesystem block rounding, not partition content |
| Recognized filesystem | Linux ext4 revision 1 (dynamic); entire evidence object |
| `file -k` classification | ext4, needs journal recovery, extents, large files, huge files |
| `blkid -p` classification | ext4; VERSION 1.0; BLOCK_SIZE 4096 |
| Filesystem UUID | `893837c4-5e10-4b63-a4e8-1aafe6a6901d` — matches existing database value; cross-witness confirmed via raw superblock bytes at absolute offset `1104` (superblock `+0x150`) |
| Last-mounted path | `/data` — confirmed both by `dumpe2fs` and raw ASCII `2f 64 61 74 61 00` at superblock `+0x168` |
| Block size | 4,096 bytes; `s_log_block_size=2` confirmed raw at superblock `+0x18` |
| Filesystem geometry | 7,307,264 inodes; 29,225,211 blocks; 32,768 blocks/group; 8,192 inodes/group |
| Filesystem features | `has_journal ext_attr resize_inode dir_index filetype needs_recovery extent encrypt sparse_super large_file huge_file uninit_bg dir_nlink extra_isize quota` |
| Encryption identity | `s_encrypt_algos[0]=0x01` (AES-256-XTS, contents); `s_encrypt_algos[1]=0x04` (AES-256-CTS, filenames); `s_encrypt_algos[2..3]=0x00`; `s_encrypt_pw_salt`=all-zero; raw bytes confirmed at absolute offset `1620` (superblock `+0x254`) — reconciles exactly with pre-existing database "FBE v1 / aes-256-xts / aes-256-cts" entry; `CONTRADICTION_RESOLVED` |
| 64-bit block-count extension | `s_blocks_count_hi=0`; `s_r_blocks_count_hi=0`; `s_free_blocks_count_hi=0` — confirmed raw at absolute offset `1360` (superblock `+0x150`); proves 29,225,211 is the complete, non-extended block count |
| Extra-isize fields | `s_min_extra_isize=32`; `s_want_extra_isize=32`; `s_flags=0x00000002` (`unsigned_directory_hash`) — confirmed raw at offset `1360`, matches `dumpe2fs` exactly |
| Reserved/quota UID:GID | Reserved blocks: 32,768; reserved UID/GID `0:1065` (`1065` = unknown group locally) |
| Mount/write state | Filesystem state clean; mount count 94; max mount count disabled; lifetime writes 80 MB; created Thu Dec 31 18:00:22 2009 (build-epoch marker, not live chronology); last mount/write Wed Apr 8 03:59:48 2026 |
| Journal inode | Inode 8; regular, mode `0600`; size 536,870,912 bytes (512 MiB); 6 extents; matches `dumpe2fs` "Total journal size: 512M / 131,072 blocks" exactly |
| Journal state | `journal_incompat_revoke`; sequence `0x00062370`; journal start block 1; `needs_recovery` flag set at capture |
| Journal replay/payload census | Not performed this pass — proportionate-closure boundary; historical transaction content, descriptor/commit inventory, and payload recovery remain `OPEN_BOUNDARY`, consistent with the depth already accepted for `super`'s dm-verity/FEC recomputation boundary |
| User quota inode | Inode 3; regular, mode `0600`; size 23,552 bytes; 6 sparse extents (blocks 1545–1546, 2049, 4523030, 4526626, 16690738) |
| User quota identity | Raw LE32 magic `0xD9C01F11` at block 1545 (quota v2 USRQUOTA format); version 1; header fields `0x00093A80`×2 (605,824), `0x17` (23), `0x14` (20); confirmed at absolute offset `6,328,320` |
| Group quota inode | Inode 4; regular, mode `0600`; size 37,888 bytes; 10 sparse extents |
| Group quota identity | Raw LE32 magic `0xD9C01927` at block 1547 (quota v2 GRPQUOTA format); version 1; header fields `0x00093A80`×2 (605,824), `0x25` (37), `0x1D` (29); confirmed at absolute offset `6,336,512`; both magics directly confirm `dumpe2fs` "User quota inode: 3 / Group quota inode: 4" at the byte level |
| Quota internal record-table semantics | `OPEN_BOUNDARY` — header structure and magic confirmed; per-record accounting fields not decoded |
| Root directory (`/data`) | Inode 2; mode `0771`; UID/GID `1000:1000`; size 4,096; links 47; SELinux `u:object_r:system_data_file:s0`; single extent block 1539 |
| Root directory census | 44 total entries (`.`, `..`, `lost+found`, plus 41 named children); complete list captured — `unencrypted`, `bootchart`, `apex`, `app-staging`, `misc`, `local`, `preloads`, `vendor`, `vendor_ce`, `vendor_de`, `data`, `app-private`, `app-ephemeral`, `app-asec`, `app-lib`, `app`, `property`, `tombstones`, `dalvik-cache`, `ota`, `ota_package`, `resource-cache`, `drm`, `mediadrm`, `anr`, `nfc`, `backup`, `ss`, `system`, `system_de`, `system_ce`, `misc_de`, `misc_ce`, `user`, `user_de`, `media`, `cache`, `adb`, `gsi`, `debuglogger`, `log_temp`, `server_configurable_flags`, `rollback-observer`, `.layout_version`, `rollback` |
| `/lost+found` | Inode 11; directory; mode `0770`; root:root; SELinux `u:object_r:system_data_file:s0`; size 16,384; single extent block 1540; genuinely empty — no live child entries beyond `.`/`..` |
| `/.layout_version` | Inode 12; regular, mode `0600`; size 2 bytes; SELinux `u:object_r:install_data_file:s0`; single extent block 2066; crtime=mtime=ctime=atime all Thu Dec 31 18:00:30 2009 (never touched since factory provisioning) |
| `/.layout_version` content | Raw bytes `33 00`; ASCII `3` + one NUL terminator; confirmed directly via `dd` at absolute offset `8,462,336` after `debugfs cat` produced no output across all attempts (classified `INVALID_CAPTURE` for the debugfs tool path specifically, not for the underlying evidence) |
| `/unencrypted` | Inode 1,130,497; directory; mode `0700`; root:root; SELinux `u:object_r:unencrypted_data_file:s0`; crtime Thu Dec 31 18:00:27 2009; single extent block 4522498; 4 children: `mode` (23B), `key` (dir), `ref` (8B), `magisk` (dir) |
| `/unencrypted/key` | Inode 1,130,498; directory; mode `0700`; root:root; SELinux `u:object_r:unencrypted_data_file:s0`; single extent block 4522499; 5 children |
| `/unencrypted/key` contents | `version` (1B), `secdiscardable` (16,384B), `stretching` (10B), `keymaster_key_blob` (166B), `encrypted_key` (92B) — matches the stock AOSP vold synthetic-password/CE-key-wrap directory structure exactly by name and cardinality |
| `/unencrypted/key` internal field semantics | `OPEN_BOUNDARY` — file presence/size/naming confirmed; byte-level key material not extracted or decoded this pass |
| `/unencrypted/magisk` | Inode 1,130,506; directory; mode `0700`; root:root; mtime Wed Mar 18 2026 19:41; 1 child: `sepolicy.rule` (inode 1,130,507, 0 bytes, mode `0644`, mtime Wed Apr 8 2026 02:41) |
| `/unencrypted/magisk` classification | Live Magisk sepolicy-injection staging path; empty at capture time; `OWNER_RESEARCH_MODIFIED`, not stock |
| `/rollback` | Inode 5,160,961; directory; mode `0700`; system:system (`1000:1000`); SELinux `u:object_r:system_data_file:s0`; mtime Thu Mar 19 2026 22:38; single extent block 20644354; 1 child: `583413911` |
| `/rollback/583413911` | Inode 5,160,962; directory; mode `0700`; 1000:1000; 2 children: `com.google.android.tzdata` (dir) and `rollback.json` (5,160,965; 535 bytes; mtime Tue Apr 7 2026 02:09) — package-rollback session for a tzdata module |
| `/system_ce` | Inode 6,234,113; directory; mode `0770`; system:system; SELinux `u:object_r:system_data_file:s0`; single extent, 1 child: `0` (inode 6,234,114; per-user CE storage root for user 0; not walked further — encrypted namespace boundary) |
| `/system` | Inode 4,161,537; directory; mode `0775`; system:system; 58 children total, all names filename-encrypted (`<encrypted (N)>`); complete structural metadata (mode/UID:GID/size/mtime/encrypted-name-length) captured per entry |
| `/misc` | Inode 1,064,961; directory; mode `1771`; group `9998`; 42 children, all filename-encrypted; distinct owner UID/GID pairs visible structurally per entry (identity not resolvable without live device) |
| `/property` | Inode 4,931,585; directory; mode `0700`; root:root; 1 child, filename-encrypted (24-byte encrypted name), 4,013 bytes, mtime Wed Apr 8 2026 04:03 |
| `/adb` | Inode 2,940,929; directory; mode `0700`; root:root; 5 children — 1 file + 4 directories, all filename-encrypted |
| `/adb` anomalous child | Inode 2,940,930; regular file; **mode `0000`** (zero permission bits — anomalous relative to every other sampled object in this session, all of which carry nonzero mode bits); size 40,960 bytes; links 1; 10 extents across 4 discontiguous block regions; ctime=mtime Wed Apr 8 2026 04:00:11; atime=crtime Tue Dec 31 2024 10:00:03 |
| `/adb` anomalous-child xattr | Extended attribute name `c` (28 bytes): `01 7f 04 00 14 e2 6f 60 b1 1e 58 3e 81 cc ef e2 27 bd 91 7f a9 f4 65 a8 76 91 f3 af`; SELinux `u:object_r:adb_data_file:s0` |
| `/adb` anomalous-child structural note | 28-byte xattr length matches the documented `fscrypt_context_v1` structure size (version + contents-mode + filenames-mode + flags + 8-byte key descriptor + 16-byte nonce); field-by-field byte decode not asserted — `OPEN_BOUNDARY` |
| `/adb` anomalous-child cause | `OPEN_BOUNDARY`; zero-permission regular file under `/adb` with `adb_data_file` context — provisioning mechanism, intended consumer, and whether the zero mode is a corruption artifact or a deliberate ADB-managed state are not established from structure alone |
| `/app-staging` | Inode 819,201; directory; mode `0750`; system:system; mtime Fri Mar 20 2026 03:16; empty (`.`/`..` only) |
| `/backup` | Inode 5,505,025; directory; mode `0700`; root:root; 7 children, all filename-encrypted |
| `/anr` | Inode 1,196,033; directory; mode `0775`; system:system; 3 children, all filename-encrypted: two files (91,151B and 325,290B, both mtime Mon Mar 23 2026 09:58) and one subdirectory |
| `/app` | Inode 4,833,281; directory; mode `0771`; system:system; 33 children, all directories, all filename-encrypted, uniform mode `0775`, uniform owner `1000:1000` — consistent with 33 installed non-system application package directories |
| Encrypted-namespace closure boundary | `/system`, `/misc`, `/property`, `/adb`, `/backup`, `/anr`, `/app` all confirmed under ext4 filename-encryption policy at capture; individual file/package identity is cryptographically unresolvable from this raw offline image; classified `REQUIRES_LIVE_DEVICE_FBE_UNLOCK`, distinct from an unresolved discovery gap |
| Physical extent ledger | ext4-accounted content: 29,225,211 blocks × 4,096 bytes = 119,706,464,256 bytes; terminal gap: 3,584 bytes; sum = 119,706,467,840 bytes; exact |
| Terminal gap | Absolute offset `119,706,464,256`–`119,706,467,839`; 3,584 bytes; independently verified completely zero-filled via direct `dd` extraction |
| Terminal gap producer mechanism | `OPEN_BOUNDARY` — gap sits between ext4's own accounted geometry and the raw GPT/object size; not decoded to a specific reserved structure this pass |
| Leading bytes | Absolute offset `0`–`15`: completely zero (bootblock region); full 1,024-byte reserved bootblock span (`0`–`1023`) not exhaustively scanned this pass — `NOT_CAPTURED_THIS_PASS` beyond the sampled 16 bytes |
| Full-partition nonzero-byte count / entropy / ASCII census | Not performed this pass — proportionate-closure boundary at 119.7 GB scale, consistent with the precedent already accepted for `super` (4 GiB, byte census explicitly deferred) and `md1img`'s scale-appropriate closure |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `OWNER_RESEARCH_MODIFIED` (live Magisk preinit/sepolicy artifacts present) |
| Content classification | `EXT4_ANDROID_DATA_FBE_ENCRYPTED_USER_PARTITION_WITH_UNENCRYPTED_KEY_MANAGEMENT_TREE_QUOTA_ACCOUNTING_AND_PRIOR_CAPTURE_HASH_DIVERGENCE` |
| Open boundaries | (1) SHA-256 divergence from pre-existing database "archived" value — `CONTRADICTION_CONFIRMED`, `SOURCE_MECHANISM_OPEN`; (2) `/adb` zero-permission-mode file cause; (3) `fscrypt_context_v1`-sized xattr internal field decode; (4) terminal 3,584-byte gap producer mechanism; (5) journal historical transaction/payload content; (6) `/unencrypted/key` byte-level key material; (7) encrypted-namespace file/package identity for `/system`, `/misc`, `/property`, `/adb`, `/backup`, `/anr`, `/app`; (8) full-partition byte census (nonzero count, entropy, ASCII/UTF-16LE strings) |

## p37 — `otp`
| p37 | `otp` | 45,088,768 | Completely zero-filled raw OTP-named partition image containing no filesystem, container, header, allocation structure, text, cryptographic material, provisioning record, marker, or residual payload | `CURRENT_RESEARCH_CAPTURE`; captured partition-byte content closed; factory-relative state, original provisioning reason, intended consumer, and relationship to any hardware OTP/eFuse mechanism `OPEN_BOUNDARY` | `CLOSED` | `39f7c6d38af574fe2c90ef400dfaba8ef8edccd11bdac998a3f8143a86837331` |

| Field | Verified value |
|---|---|
| Evidence object | `otp.bin` |
| Acquisition completeness | Expected from GPT: 45,088,768 bytes; received: 45,088,768 bytes; difference: 0 |
| GPT partition | p37; `otp` |
| Physical block device | `/dev/block/mmcblk0p37`; major:minor `259:5` |
| By-name mapping | `/dev/block/by-name/otp` → `/dev/block/mmcblk0p37` |
| Device-node mode and ownership | `brw-rw----`; `root:system` |
| Observed partition-node SELinux context | `u:object_r:block_device:s0` |
| Observed by-name SELinux context | `u:object_r:block_device:s0` |
| Static-policy context for the OTP alias | `u:object_r:otp_part_block_device:s0`; specialized policy label was not realized on the captured live alias |
| GPT start LBA | 244,156,383 |
| GPT end LBA | 244,244,446 inclusive |
| GPT sector count | 88,064 sectors |
| GPT partition size | 45,088,768 bytes; 43 MiB |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT unique partition GUID | `fdce12f0-a7eb-40f7-8350-960972e6cb57` |
| GPT attributes | `0x0000000000000000` |
| Object size | 45,088,768 bytes; `0x02B00000`; 43 MiB |
| Evidence-object extent | `0x00000000–0x02AFFFFF` |
| Size reconciliation | 88,064 × 512-byte GPT sectors = 45,088,768 bytes; exact |
| Received-file allocation | 88,064 allocated 512-byte host blocks = 45,088,768 bytes; the received object is not sparse |
| Recognized filesystem | NONE |
| Recognized container | NONE |
| `file -k` classification | `data` |
| `blkid -p` signature | NONE; probe status 2 |
| ext2/ext3/ext4 probe | No superblock or ext-family magic exists; the object is not classified as a damaged ext filesystem |
| Internal partition table | NONE |
| Allocation structures | NONE |
| Directory or inode structures | NONE |
| Journal | NONE |
| Extended attributes | NONE |
| Distinct byte values | One: `0x00` |
| Nonzero-byte count | 0 |
| Zero-byte count | 45,088,768 |
| `0xFF`-byte count | 0 |
| Zero-filled extent | Entire evidence object, `0x00000000–0x02AFFFFF` |
| First nonzero byte | NONE |
| Last nonzero byte | NONE |
| Initial zero area | Entire evidence object |
| Terminal zero area | Entire evidence object |
| Shannon entropy | `0.000000000` bits per byte |
| 512-byte sector accounting | 88,064 total sectors; 88,064 completely zero sectors; 0 nonzero sectors |
| 4,096-byte block accounting | 11,008 total blocks; 11,008 completely zero blocks; 0 nonzero blocks |
| 65,536-byte region accounting | 688 total regions; 688 completely zero regions; 0 nonzero regions |
| 1,048,576-byte region accounting | 43 total regions; 43 completely zero regions; 0 nonzero regions |
| Constant-run census | One maximal run: `0x00000000–0x02AFFFFF`; 45,088,768 consecutive `0x00` bytes |
| Independent segmented verification | All 43 separately hashed 1 MiB regions matched the SHA-256 of a generated 1 MiB all-zero region |
| Independent whole-object verification | All 45,088,768 bytes compared byte-for-byte identical to `/dev/zero`; zero differences |
| Independent generated-stream verification | SHA-256 of an independently generated 45,088,768-byte zero stream equals the evidence-object SHA-256 |
| Independent byte-removal verification | Removing every `0x00` byte left 0 bytes |
| ASCII strings | NONE at minimum length 4 |
| UTF-16LE strings | NONE at minimum length 4 |
| Embedded-signature scan | No ext-family magic, ELF, ZIP, gzip, PNG, SQLite, Android boot-image header, AVB header/footer, FDT, DTBO-table, Android sparse-image, MediaTek GFH, GPT, or `LDI` signature |
| Structured payload | NONE |
| Partition-resident OTP provisioning record | NONE present in this capture |
| Cryptographic or key material | NONE present in this capture |
| Persistent state | NONE encoded in the captured partition bytes |
| Recoverable deleted content | NONE; no filesystem, allocation metadata, or nonzero residual byte exists |
| Residual-data classification | NONE; no nonzero residual bytes exist |
| GPT-label limitation | The name `otp` identifies this eMMC user-area GPT partition; the name alone does not establish its producer, consumer, record format, or lifecycle |
| Partition-versus-hardware boundary | Complete zero-fill of p37 does not establish that SoC OTP/eFuse banks or any other hardware-backed one-time-programmable store are blank, programmed, readable, or related to this partition |
| `/proc/driver/otp` witness separation | Captured init-policy evidence assigns `/proc/driver/otp` mode `0660` and owner/group `root:system`; no byte-level evidence establishes that proc node as a reader or writer of p37, so the relationship remains `OPEN_BOUNDARY` |
| Original provisioning reason | `OPEN_BOUNDARY`; the all-zero image contains no evidence establishing why this GPT entry was created or retained |
| Intended consumer and function | `OPEN_BOUNDARY`; no payload, header, marker, state field, or allocation structure exists from which a specific consumer or OTP workflow can be resolved |
| Factory-relative content state | `OPEN_BOUNDARY`; this capture proves complete zero-fill at acquisition but cannot distinguish never populated, deliberately cleared, reserved, placeholder, or conditional-provisioning states |
| Source-to-upload identity | The received object is exactly GPT-sized and internally closed; a source-host SHA-256 witness was not supplied, so identity with the host-side source file remains `OPEN_BOUNDARY` |
| Operational safety | An all-zero capture does not establish that p37 is safe to initialize or overwrite; preserve it and exclude it from normal ROM flashing unless a separately verified device-specific procedure requires otherwise |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `39f7c6d38af574fe2c90ef400dfaba8ef8edccd11bdac998a3f8143a86837331` |
| Content classification | `FULLY_ZERO_FILLED_RAW_OTP_NAMED_PARTITION_IMAGE` |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; `ALL_ZERO` |
| Closure statement | The complete 45,088,768-byte captured object is structurally closed as one all-zero extent; provisioning history, intended use, producer/consumer identity, hardware-OTP relationship, and source-host identity remain explicit `OPEN_BOUNDARY` items |

## p38 — `flashinfo`
| p38 | `flashinfo` | 16,777,216 | Structurally complete raw download-information and download-history ledger: one `DOWNLOAD INFORMATION!!` v1.0 area, a 21-entry ordinal/value table, 24 fixed string slots, one `#DOWNLOAD_HISTORY_RECORD#` area containing two fixed-stride records and 82 fixed-width operation entries, and directly validated additive-16 flash-check values for the supplied `dtbo`, `tee2`, and authenticated-flags-zero `vbmeta` objects | `CURRENT_RESEARCH_CAPTURE`; generating utility identity, record chronology, undocumented control-word meanings, unsupplied-target checksum validation, operation provenance, and factory-relative state `OPEN_BOUNDARY` | `CLOSED` | `8144ef811e8f7465c5bb8d57add630b7c8a2f10c2c99b5fc0778033ea106e910` |

| Field | Verified value |
|---|---|
| Evidence object | `flashinfo.bin` |
| Object size | 16,777,216 bytes; `0x01000000` |
| Acquisition completeness | Expected from GPT: 16,777,216 bytes; received: 16,777,216 bytes; difference: 0 |
| Partition identity | p38; `flashinfo`; `/dev/block/mmcblk0p38`; major:minor `259:6` |
| GPT geometry | Start LBA 244,244,447 (`0x0E8EDFDF`); end LBA 244,277,214 (`0x0E8F5FDE`) inclusive; 32,768 sectors; byte extent on parent device `0x0000001D1DBFBE00–0x0000001D1EBFBDFF` |
| Terminal GPT position | p38 follows p37 `otp` contiguously and ends exactly at GPT last usable LBA 244,277,214; all terminal zero fill described below is inside p38, not outside the partition |
| GPT unique GUID | `0fbbafa2-4aa9-4490-8983-5329328505fd` |
| GPT type GUID | `ebd0a0a2-b9e5-4433-87c0-68b6b72699c7` |
| GPT attributes | `0` |
| Kernel block state | Read-only state `0`; alignment offset 0; discard alignment 16,896 bytes; holder count 0; raw GPT and live sysfs geometry match |
| Device-node metadata | `brw-------`; owner/group `root:root`; target context `u:object_r:block_device:s0` |
| Alias realization | By-name alias `flashinfo`; root and platform alias contexts both `u:object_r:block_device:s0`; no captured specialized platform rule |
| Recognized filesystem | NONE |
| Outer recognition | `file -k`: `data`; `blkid -p`: no recognized signature, status 2 |
| Proprietary marker census | Literal `DOWNLOAD INFORMATION!!` occurs exactly once at `0x000000`; literal `#DOWNLOAD_HISTORY_RECORD#` occurs exactly once at `0x004000` |
| Negative standard-signature census | No ELF, ZIP, gzip, XZ, Zstandard, LZ4-frame, bzip2, PNG, SQLite, Android boot-image, Android sparse-image, AVB, FDT, DTBO-container, or recognized filesystem signature was established anywhere in the partition |
| Complete top-level layout | Download-information area `0x000000–0x003FFF`; download-history area `0x004000–0x007FFF`; reserved `0xFF` area `0x008000–0x013FFF`; terminal zeros `0x014000–0xFFFFFF` |
| Structural extent ledger | 16,384 + 16,384 + 49,152 + 16,695,296 = 16,777,216 bytes; all top-level extents are contiguous and nonoverlapping, with no unassigned byte |
| Download-information extent | `0x000000–0x003FFF`; exactly `0x4000` = 16,384 bytes |
| Information-area ledger | 32-byte signature/version header + 20-byte control/fill area + 420-byte indexed-value table + 2,120-byte `0xFF` gap + 384-byte string-slot table + 13,408-byte `0xFF` tail = 16,384 bytes |
| Information signature | `0x000000–0x000015`: literal `DOWNLOAD INFORMATION!!`; followed by `00 FF` at `0x000016–0x000017` |
| Information version | `0x000018–0x00001B`: literal `V1.0`; followed by `00 FF FF FF` at `0x00001C–0x00001F` |
| Information control/fill | `0x000020–0x00002B`: 12 zero bytes; `0x00002C–0x000033`: eight `0xFF` bytes |
| Indexed-value table | `0x000034–0x0001D7`; exactly 21 entries × 20 bytes. Each entry is a sequential LE32 ordinal, one LE64 value, and eight terminal `0xFF` bytes |
| Indexed ordinals | Sequential values 1–21 with no missing, duplicated, or out-of-order ordinal |
| Indexed LE64 values | 1 `0x0000`; 2 `0x2183`; 3 `0x2051`; 4 `0xE961`; 5 `0x2480`; 6 `0x2EF0`; 7 `0x8000`; 8 `0x8000`; 9 `0xFB2E`; 10 `0xFB2E`; 11 `0x7D08`; 12 `0x7D08`; 13 `0x94AD`; 14 `0xCFB2`; 15 `0x32CB`; 16 `0xC17A`; 17 `0xC17A`; 18 `0xF42E`; 19 `0xD432`; 20 `0x4236`; 21 `0xF427` |
| Indexed-table relationship | The complete 21-value sequence equals history record B's `preloader` value followed by its `recovery` through `userdata` values, in physical order; the separately present `preloader_backu` row is the sole history-B target omitted from this sequence |
| Information-table `0xFF` gap | `0x0001D8–0x000A1F`; exactly 2,120 bytes; completely `0xFF`-filled |
| Information string slots | `0x000A20–0x000B9F`; exactly 24 slots × 16 bytes |
| Information slot 0 | `0x000A20–0x000A2F`: literal `DRAM_PASS`, one NUL, and six `0xFF` bytes |
| Information slot 1 | `0x000A30–0x000A3F`: raw bytes `44 4C 5F 44 4F 4E 45 00 00 41 52 54 00 FF FF FF`; this contains literal `DL_DONE`, followed by `00 41 52 54 00 FF FF FF`. Meaning of the secondary `ART` byte sequence remains `OPEN_BOUNDARY` and is not normalized to an inferred word |
| Information target slots | Slots 2–23 contain, in order: `preloader`, `preloader_backu`, `recovery`, `vbmeta_system`, `vbmeta_vendor`, `md1img`, `spmfw`, `scp1`, `scp2`, `sspm_1`, `sspm_2`, `lk`, `lk2`, `boot`, `logo`, `dtbo`, `tee1`, `tee2`, `super`, `vbmeta`, `cache`, `userdata` |
| Truncated target literal | `preloader_backu` is the exact 15-byte stored label in the information and history areas; an unencoded final character is not supplied or silently restored |
| Information-area terminal fill | `0x000BA0–0x003FFF`; exactly 13,408 bytes; completely `0xFF`-filled |
| Download-history extent | `0x004000–0x007FFF`; exactly `0x4000` = 16,384 bytes |
| History-area ledger | 32-byte global marker + 7,580-byte record A + 7,580-byte record B + 4-byte zero alignment + 1,188-byte `0xFF` tail = 16,384 bytes |
| History global marker | `0x004000–0x004018`: literal `#DOWNLOAD_HISTORY_RECORD#`; followed by one NUL and six `0xFF` bytes through `0x00401F` |
| History record geometry | Record A `0x004020–0x005DBB`; record B `0x005DBC–0x007B57`; each is exactly `0x1D9C` = 7,580 bytes; corresponding internal fields are separated by the same 7,580-byte stride |
| Common record preamble | Each record begins with three LE32 words, 12 zero bytes, four repetitions of raw marker `FE FF FF EA`, four `0xBB` bytes, LE32 value 56, 360 zero bytes, and literal `RsltOK` plus two NUL bytes; fixed table begins at record-relative `0x1A0` |
| Record-control words | Record A first three LE32 words: `1, 1, 8`; record B: `0, 2, 8`. Their exact values and order are established; undocumented semantic meanings remain `OPEN_BOUNDARY` |
| Record marker words | Record-relative `0x18–0x27`: four raw `FE FF FF EA` repetitions; `0x28–0x2B`: `BB BB BB BB`; `0x2C–0x2F`: LE32 56. Assigning status, sentinel, or version meanings beyond the encoded values remains `OPEN_BOUNDARY` |
| History entry format | Exactly 56 bytes per entry: 16-byte target label; 8-byte operation field; 8-byte result field; 8-byte field containing `EOK`; 8-byte field containing `FlsCK?N`; final LE64 numeric value |
| Literal result boundary | Every one of the 82 entries contains `RsltOK`, `EOK`, and `FlsCK?N`; `FORMAT_` or `DL.Done` occupies the operation field. These are stored result declarations, not independent proof that an external flashing utility completed each declared operation |
| Record A entry table | `0x0041C0–0x0047A7`; 27 entries × 56 bytes = 1,512 bytes |
| Record A entry classes | Three identical `format` / `FORMAT_` entries with value 0; `SGPT` and `PGPT` download entries; then the complete 22-target sequence from `preloader` through `userdata`, including `preloader_backu` |
| Record A numeric ledger | `SGPT 0x4695`; `PGPT 0x4932`; `preloader 0x0000`; `preloader_backu 0x0000`; `recovery 0x2CA2`; `vbmeta_system 0x2EF3`; `vbmeta_vendor 0xE47B`; `md1img 0x11A6`; `spmfw 0x340C`; `scp1 0x8117`; `scp2 0x8117`; `sspm_1 0xF8F2`; `sspm_2 0xF8F2`; `lk 0x2F20`; `lk2 0x2F20`; `boot 0xE5A6`; `logo 0x5F47`; `dtbo 0xF749`; `tee1 0xC0B6`; `tee2 0xC0B6`; `super 0x4908`; `vbmeta 0xC958`; `cache 0x3C50`; `userdata 0x0C09` |
| Record A terminal reserve | `0x0047A8–0x005DBB`; exactly 5,652 bytes; completely zero-filled |
| Record B entry table | `0x005F5C–0x006B63`; 55 entries × 56 bytes = 3,080 bytes |
| Record B entry classes | 33 byte-for-byte identical `format` / `FORMAT_` entries with value 0, followed by the complete 22-target sequence from `preloader` through `userdata`, including `preloader_backu` |
| Record B numeric ledger | `preloader 0x0000`; `preloader_backu 0x0000`; `recovery 0x2183`; `vbmeta_system 0x2051`; `vbmeta_vendor 0xE961`; `md1img 0x2480`; `spmfw 0x2EF0`; `scp1 0x8000`; `scp2 0x8000`; `sspm_1 0xFB2E`; `sspm_2 0xFB2E`; `lk 0x7D08`; `lk2 0x7D08`; `boot 0x94AD`; `logo 0xCFB2`; `dtbo 0x32CB`; `tee1 0xC17A`; `tee2 0xC17A`; `super 0xF42E`; `vbmeta 0xD432`; `cache 0x4236`; `userdata 0xF427` |
| Record B terminal reserve | `0x006B64–0x007B57`; exactly 4,084 bytes; completely zero-filled |
| Record-set distinction | Records A and B contain different numeric values for all three supplied comparable targets: `dtbo`, `tee2`, and `vbmeta`; physical ordering is established, but chronological ordering, acquisition event, and generating-host identity remain `OPEN_BOUNDARY` |
| Additive-check algorithm | For all three supplied comparison objects, record B's final numeric field equals the complete-object sum of unsigned byte values modulo 65,536. The field is stored as LE64 but only its low 16 bits are nonzero in every populated ledger entry |
| Direct `dtbo` comparison | Complete `dtbo.bin` additive-16 value is `0x32CB`, exactly equal to record B `dtbo 0x32CB`; record A contains the different value `0xF749` |
| Direct `tee2` comparison | Complete `tee2.bin` additive-16 value is `0xC17A`, exactly equal to record B `tee2 0xC17A`; record A contains the different value `0xC0B6` |
| Direct `vbmeta` comparison | Captured flags-3 `vbmeta.bin` additive-16 value is `0xD435`; restoring only its authenticated flags word from `3` to `0` produces `0xD432`, exactly equal to record B `vbmeta 0xD432`; record A contains the different value `0xC958` |
| p34 corroboration | The record-B `vbmeta` checksum independently agrees with p34's RSA-authenticated flags-zero form and differs from the captured flags-3 form by exactly 3, corroborating the directly established post-signing flags `0 → 3` mutation |
| Current-object relationship | Record B's fields equal the complete-object additive-16 values of supplied `dtbo` and `tee2` and the authenticated form of supplied `vbmeta`; record A differs for all three. This weak checksum equality validates the field algorithm and ledger relationship but does not independently prove byte identity |
| Unsupplied-target boundary | No comparison objects for the other record targets were supplied in this pass. Extending the validated additive-16 equality to `recovery`, `vbmeta_system`, `vbmeta_vendor`, `md1img`, `spmfw`, `scp1`, `scp2`, `sspm_1`, `sspm_2`, `lk`, `lk2`, `boot`, `logo`, `tee1`, `super`, `cache`, `userdata`, `SGPT`, or `PGPT` remains `OPEN_BOUNDARY` |
| Checksum-purpose boundary | Direct arithmetic establishes the additive-16 algorithm for the three tested record-B rows. Whether the utility labels this value a flash checksum, download checksum, verification checksum, or another proprietary term is not explicitly encoded; literal `FlsCK?N` is preserved without expansion |
| History-area alignment | `0x007B58–0x007B5B`; exactly four bytes; completely zero-filled |
| History-area `0xFF` tail | `0x007B5C–0x007FFF`; exactly 1,188 bytes; completely `0xFF`-filled |
| Reserved `0xFF` area | `0x008000–0x013FFF`; exactly 49,152 bytes; completely `0xFF`-filled |
| Terminal zero span | `0x014000–0xFFFFFF`; exactly 16,695,296 bytes; completely zero-filled |
| First/last nonzero byte | First `0x000000`; last `0x013FFF` |
| Used extent through last nonzero | `0x000000–0x013FFF`; exactly 81,920 bytes; this includes formal `0xFF` reserve through the last nonzero byte |
| Structured-table endpoint | Last fixed-width history entry ends at `0x006B63`; remaining bytes through `0x007B57` are record-local zero reserve, followed by alignment and `0xFF` reserve |
| Partition byte counts | 69,050 nonzero bytes; 16,708,166 zero bytes; 66,272 bytes equal `0xFF`; 118 distinct byte values occur |
| Populated 4 KiB blocks | 20 blocks in one contiguous run, indices 0–19 (`0x000000–0x013FFF`); blocks 20–4095 are completely zero-filled. Blocks 1–3 and 8–19 are wholly `0xFF` |
| ASCII strings | 351 occurrences at minimum length 4; 32 unique; length range 4–25 bytes; every detected string belongs to the two proprietary record structures rather than compressed or executable data |
| ASCII frequency anchors | `RsltOK` 84; `FlsCK?N` 82; `DL.Done` 46; `FORMAT_` 36; `format` 36; each of the 20 target labels with length at least 4 occurs three times; `lk` and `lk2` are separately present in fixed fields but fall below this scan threshold; the two top-level markers and `V1.0`, `DRAM_PASS`, `DL_DONE`, `SGPT`, and `PGPT` occur once each |
| UTF-16LE strings | Zero occurrences at minimum length 4 |
| Utility-identity boundary | No literal `SP Flash`, `MediaTek`, or `MTK` identifier is encoded. Exact utility product/version, host, command, and execution time remain `OPEN_BOUNDARY`; `V1.0` is only the encoded information-record version |
| Prior flash-health label reconciliation | No literal eMMC/NAND health, wear, lifetime, PRE_EOL, CID, CSD, JEDEC, bad-block, or erase-count field was found. The byte structure directly establishes download information and download history, so `FLASH_HEALTH_PARTITION` is rejected as a content classification unless separate evidence later establishes an additional role |
| Independent fill verification | Both record-local zero tails, the four-byte history alignment, and the 16,695,296-byte terminal span passed independent zero-stream comparison; the 2,120-byte and 13,408-byte information fills and the combined 50,340-byte history/reserve fill passed independent `0xFF`-stream comparison |
| SHA-256 verification | `sha256sum` and OpenSSL independently produced `8144ef811e8f7465c5bb8d57add630b7c8a2f10c2c99b5fc0778033ea106e910` only after structural closure |
| Capture classification | `CURRENT_RESEARCH_CAPTURE`; mutable download-history provenance, generating event, and factory-relative state `OPEN_BOUNDARY` |
| Content classification | `DOWNLOAD_INFORMATION_V1_0_WITH_TWO_FIXED_DOWNLOAD_HISTORY_RECORDS_AND_VALIDATED_ADDITIVE16_FLASH_CHECKSUM_LEDGER` |







################################################################################

# 2.7 — COMPLETE PHYSICAL PARTITION ROLE CLASSIFICATION

################################################################################

eMMC hardware regions:
preloader_a / mmcblk0boot0:
Role                         : preloader boot region A
Size                         : 4,096 KiB
Hash                         : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
ROM action                   : NEVER TOUCH

preloader_b / mmcblk0boot1:
Role                         : preloader boot region B
Size                         : 4,096 KiB
Hash                         : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
ROM action                   : NEVER TOUCH

mmcblk0rpmb:
Role                         : RPMB protected storage
Size                         : 16,384 KiB
Hash                         : not available through normal block-device read
ROM action                   : NEVER TOUCH

GPT physical partitions:
p1 boot_para:
Block device                 : /dev/block/mmcblk0p1
Size                         : 1,048,576 bytes
By-name alias                : boot_para
Fstab alias                  : /boot_para
Fstab type                   : emmc
Role                         : MTK boot parameter block; GFH structured data
Content state                : active structured content
ROM action                   : NEVER TOUCH

p2 recovery:
Block device                 : /dev/block/mmcblk0p2
Size                         : 33,554,432 bytes
By-name alias                : recovery
Fstab alias                  : /recovery
Fstab type                   : emmc
Role                         : dedicated recovery image
Content state                : Android recovery image with AVB footer
ROM action                   : flash only when intentionally updating recovery

p3 para:
Block device                 : /dev/block/mmcblk0p3
Size                         : 524,288 bytes
Start sector                 : 67,648
Sector count                 : 1,024
By-name alias                : para
Fstab alias                  : /misc
Fstab type                   : emmc
Role                         : MTK misc/para partition; boot mode and OTA update flags
Content state                : first sector all-zero; full partition not zero-filled
ROM action                   : preserve; do not wipe

p4 expdb:
Block device                 : /dev/block/mmcblk0p4
Size                         : 20,971,520 bytes
Start sector                 : 68,672
Sector count                 : 40,960
By-name alias                : expdb
Fstab alias                  : /expdb
Fstab type                   : emmc
Role                         : MTK exception database / crash log storage
Content state                : first sector all-zero; full partition not zero-filled
ROM action                   : preserve unless deliberately clearing crash history

p5 vbmeta_system:
Block device                 : /dev/block/mmcblk0p5
Size                         : 8,388,608 bytes
By-name alias                : vbmeta_system
Fstab alias                  : /vbmeta_system
Fstab type                   : emmc
Role                         : AVB chain descriptor for system logical partition
Content state                : AVB metadata partition
ROM action                   : flash with vbmeta set when intentionally changing AVB metadata

p6 vbmeta_vendor:
Block device                 : /dev/block/mmcblk0p6
Size                         : 8,388,608 bytes
By-name alias                : vbmeta_vendor
Fstab alias                  : /vbmeta_vendor
Fstab type                   : emmc
Role                         : AVB chain descriptor for vendor logical partition
Content state                : AVB metadata partition
ROM action                   : flash with vbmeta set when intentionally changing AVB metadata

p7 frp:
Block device                 : /dev/block/mmcblk0p7
Size                         : 1,048,576 bytes
By-name alias                : frp
Fstab alias                  : /persistent
Fstab type                   : emmc
Role                         : Factory Reset Protection binding data
Content state                : active non-zero structured FRP data
ROM action                   : do not wipe unless deliberately clearing FRP state

p8 nvcfg:
Block device                 : /dev/block/mmcblk0p8
Size                         : 33,554,432 bytes
By-name alias                : nvcfg
Mount point                  : /mnt/vendor/nvcfg
Filesystem                   : ext4
UUID                         : f892fcba-a0c9-4878-b29c-74faae323b1f
Role                         : NVRAM configuration filesystem
ROM action                   : preserve

p9 nvdata:
Block device                 : /dev/block/mmcblk0p9
Size                         : 67,108,864 bytes
By-name alias                : nvdata
Mount point                  : /mnt/vendor/nvdata
Filesystem                   : ext4
UUID                         : 7db3fff2-83d3-4461-a6a7-965a0fff7478
Role                         : runtime NVRAM data filesystem
ROM action                   : preserve

p10 md_udc:
Block device                 : /dev/block/mmcblk0p10
Size                         : 23,699,456 bytes
By-name alias                : md_udc
Mount point                  : /metadata
Filesystem                   : ext4
UUID                         : 2fdda847-b389-4139-9dbc-f7829dcfb030
Role                         : active /metadata mount target
ROM action                   : preserve as /metadata target

p11 metadata:
Block device                 : /dev/block/mmcblk0p11
Size                         : 33,554,432 bytes
By-name alias                : metadata
Filesystem                   : ext4
UUID                         : 933d2e5d-4696-4d2e-8582-cb61000d4bde
Role                         : inactive metadata-named ghost partition
Content state                : physical partition exists but is not active /metadata
ROM action                   : NEVER USE as /metadata target

p12 protect1:
Block device                 : /dev/block/mmcblk0p12
Size                         : 8,388,608 bytes
By-name alias                : protect1
Mount point                  : /mnt/vendor/protect_f
Filesystem                   : ext4
UUID                         : 9743e6fe-50d3-4276-ae5b-bb4d5b3b9d89
Role                         : NVRAM protect_f / factory calibration storage
ROM action                   : READ-ONLY / NEVER FORMAT / NEVER WIPE

p13 protect2:
Block device                 : /dev/block/mmcblk0p13
Size                         : 11,395,072 bytes
By-name alias                : protect2
Mount point                  : /mnt/vendor/protect_s
Filesystem                   : ext4
UUID                         : 666202e1-6301-42a1-8f86-9de9905284eb
Role                         : NVRAM protect_s / factory calibration storage
ROM action                   : READ-ONLY / NEVER FORMAT / NEVER WIPE

p14 seccfg:
Block device                 : /dev/block/mmcblk0p14
Size                         : 8,388,608 bytes
By-name alias                : seccfg
Fstab alias                  : /seccfg
Fstab type                   : emmc
Role                         : security configuration
ROM action                   : NEVER TOUCH

p15 sec1:
Block device                 : /dev/block/mmcblk0p15
Size                         : 2,097,152 bytes
By-name alias                : sec1
Role                         : security partition
Content state                : full-partition zero-filled
ROM action                   : NEVER TOUCH

p16 proinfo:
Block device                 : /dev/block/mmcblk0p16
Size                         : 3,145,728 bytes
By-name alias                : proinfo
Fstab alias                  : /proinfo
Fstab type                   : emmc
Role                         : production information
ROM action                   : NEVER TOUCH

p17 md1img:
Block device                 : /dev/block/mmcblk0p17
Size                         : 104,857,600 bytes
By-name alias                : md1img
Fstab alias                  : /md1img
Fstab type                   : emmc
Role                         : modem firmware image
ROM action                   : NEVER TOUCH in normal ROM bring-up

p18 spmfw:
Block device                 : /dev/block/mmcblk0p18
Size                         : 1,048,576 bytes
By-name alias                : spmfw
Fstab alias                  : /spmfw
Fstab type                   : emmc
Role                         : SPM firmware
ROM action                   : NEVER TOUCH

p19 scp1:
Block device                 : /dev/block/mmcblk0p19
Size                         : 1,048,576 bytes
By-name alias                : scp1
Fstab alias                  : /scp1
Fstab type                   : emmc
Role                         : SCP firmware slot 1
ROM action                   : NEVER TOUCH

p20 scp2:
Block device                 : /dev/block/mmcblk0p20
Size                         : 1,048,576 bytes
By-name alias                : scp2
Fstab alias                  : /scp2
Fstab type                   : emmc
Role                         : SCP firmware slot 2
ROM action                   : NEVER TOUCH

p21 sspm_1:
Block device                 : /dev/block/mmcblk0p21
Size                         : 1,048,576 bytes
By-name alias                : sspm_1
Fstab alias                  : /sspm_1
Fstab type                   : emmc
Role                         : SSPM firmware slot 1
ROM action                   : NEVER TOUCH

p22 sspm_2:
Block device                 : /dev/block/mmcblk0p22
Size                         : 1,048,576 bytes
By-name alias                : sspm_2
Fstab alias                  : /sspm_2
Fstab type                   : emmc
Role                         : SSPM firmware slot 2
ROM action                   : NEVER TOUCH

p23 gz1:
Block device                 : /dev/block/mmcblk0p23
Size                         : 16,777,216 bytes
By-name alias                : gz1
Role                         : GeniZone reserved slot 1
Content state                : full-partition zero-filled
ROM action                   : NEVER TOUCH

p24 gz2:
Block device                 : /dev/block/mmcblk0p24
Size                         : 16,777,216 bytes
By-name alias                : gz2
Role                         : GeniZone reserved slot 2
Content state                : full-partition zero-filled
ROM action                   : NEVER TOUCH

p25 nvram:
Block device                 : /dev/block/mmcblk0p25
Size                         : 67,108,864 bytes
By-name alias                : nvram
Fstab alias                  : /nvram
Fstab type                   : emmc
Role                         : primary NVRAM / calibration store
ROM action                   : NEVER TOUCH

p26 lk:
Block device                 : /dev/block/mmcblk0p26
Size                         : 1,048,576 bytes
By-name alias                : lk
Fstab alias                  : /bootloader
Fstab type                   : emmc
Role                         : Little Kernel bootloader slot 1
ROM action                   : NEVER TOUCH

p27 lk2:
Block device                 : /dev/block/mmcblk0p27
Size                         : 1,048,576 bytes
By-name alias                : lk2
Fstab alias                  : /bootloader2
Fstab type                   : emmc
Role                         : Little Kernel bootloader slot 2
ROM action                   : NEVER TOUCH

p28 boot:
Block device                 : /dev/block/mmcblk0p28
Size                         : 33,554,432 bytes
By-name alias                : boot
Fstab alias                  : /boot
Fstab type                   : emmc
Role                         : Linux kernel + Android ramdisk boot image
ROM action                   : ROM target / flash per build

p29 logo:
Block device                 : /dev/block/mmcblk0p29
Size                         : 8,388,608 bytes
By-name alias                : logo
Fstab alias                  : /logo
Fstab type                   : emmc
Role                         : MTK boot logo / splash image
ROM action                   : preserve unless intentionally replacing splash

p30 dtbo:
Block device                 : /dev/block/mmcblk0p30
Size                         : 8,388,608 bytes
By-name alias                : dtbo
Fstab alias                  : /dtbo
Fstab type                   : emmc
Role                         : DTBO table / device-tree overlay payload
ROM action                   : preserve or intentionally flash known-good DTBO

p31 tee1:
Block device                 : /dev/block/mmcblk0p31
Size                         : 5,242,880 bytes
By-name alias                : tee1
Fstab alias                  : /tee1
Fstab type                   : emmc
Role                         : TEE firmware slot 1
ROM action                   : NEVER TOUCH

p32 tee2:
Block device                 : /dev/block/mmcblk0p32
Size                         : 11,534,336 bytes
By-name alias                : tee2
Fstab alias                  : /tee2
Fstab type                   : emmc
Role                         : TEE firmware slot 2
ROM action                   : NEVER TOUCH

p33 super:
Block device                 : /dev/block/mmcblk0p33
Size                         : 4,294,967,296 bytes
By-name alias                : super
Role                         : dynamic partition container
Logical children             : product, system, vendor
ROM action                   : flash only with correctly built super image

p34 vbmeta:
Block device                 : /dev/block/mmcblk0p34
Size                         : 8,388,608 bytes
By-name alias                : vbmeta
Fstab alias                  : /vbmeta
Fstab type                   : emmc
Role                         : AVB root metadata
Content state                : null vbmeta; flags 0x00000003
ROM action                   : flash null vbmeta when intentionally keeping AVB disabled

p35 cache:
Block device                 : /dev/block/mmcblk0p35
Size                         : 452,984,832 bytes
By-name alias                : cache
Mount point                  : /cache
Filesystem                   : ext4
UUID                         : e85e6a3d-0f1c-473e-8757-5ffc1e2decef
Role                         : Android cache / OTA staging / system cache
ROM action                   : safe to wipe intentionally; reformats on first boot

p36 userdata:
Block device                 : /dev/block/mmcblk0p36
Size                         : 119,706,467,840 bytes
By-name alias                : userdata
Mount point                  : /data
Filesystem                   : ext4
UUID                         : 893837c4-5e10-4b63-a4e8-1aafe6a6901d
Role                         : FBE /data filesystem
ROM action                   : preserve unless deliberate wipe/test cycle

p37 otp:
Block device                 : /dev/block/mmcblk0p37
Size                         : 45,088,768 bytes
By-name alias                : otp
Role                         : reserved OTP partition
Content state                : full-partition zero-filled
ROM action                   : NEVER TOUCH

p38 flashinfo:
Block device                 : /dev/block/mmcblk0p38
Size                         : 16,777,216 bytes
By-name alias                : flashinfo
Role                         : flash information / flash-health partition
Content state                : non-zero full-partition hash
ROM action                   : NEVER TOUCH

################################################################################

# 2.8 — BOOT-CHAIN STORAGE TOPOLOGY

################################################################################

Boot-chain partitions and regions:
preloader_a                   : /dev/block/mmcblk0boot0
preloader_b                   : /dev/block/mmcblk0boot1
lk                            : /dev/block/mmcblk0p26
lk2                           : /dev/block/mmcblk0p27
boot                          : /dev/block/mmcblk0p28
recovery                      : /dev/block/mmcblk0p2
logo                          : /dev/block/mmcblk0p29
dtbo                          : /dev/block/mmcblk0p30
tee1                          : /dev/block/mmcblk0p31
tee2                          : /dev/block/mmcblk0p32
vbmeta                        : /dev/block/mmcblk0p34
vbmeta_system                 : /dev/block/mmcblk0p5
vbmeta_vendor                 : /dev/block/mmcblk0p6
scp1                          : /dev/block/mmcblk0p19
scp2                          : /dev/block/mmcblk0p20
sspm_1                        : /dev/block/mmcblk0p21
sspm_2                        : /dev/block/mmcblk0p22
spmfw                         : /dev/block/mmcblk0p18
md1img                        : /dev/block/mmcblk0p17

Boot layout:
Slot scheme                    : A-only
Recovery partition             : dedicated recovery partition exists
recovery-as-boot               : false
vendor_boot                    : absent
init_boot                      : absent
vendor_kernel_boot             : absent
dedicated dtb partition         : absent
DTBO partition                 : present
DTBO selected index            : androidboot.dtbo_idx=0
DTB selected index             : androidboot.dtb_idx=0

################################################################################

# 2.9 — BOOT IMAGE GEOMETRY ANCHORS

################################################################################

Boot image partition:
Partition name                : boot
Block device                  : /dev/block/mmcblk0p28
Partition size                : 33,554,432 bytes
Full SHA256                   : fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851

Boot image header anchors:
Header magic                  : ANDROID!
Header version                : 2
Page size                     : 2048 bytes
Kernel size                   : 9,246,082 bytes
Kernel load address           : 0x40080000
Kernel base                   : 0x40000000
Kernel offset                 : 0x00080000
Ramdisk size                  : 923,597 bytes
Ramdisk load address          : 0x51b00000
Ramdisk offset                : 0x11b00000
Tags load address             : 0x47880000
Tags offset                   : 0x07880000
DTB offset                    : 0x07880000
OS version                    : 10.0.0
OS patch level                : 2024-03
Boot image SHA1 field         : c6ccf000f6513dafd9a2018c87d799922348887c

Canonical boot build constants:
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

Boot image integrity note:
The stock boot image header contains SHA1 field:
c6ccf000f6513dafd9a2018c87d799922348887c

This field belongs to stock boot image identity.
Any custom boot image rebuild must recompute the boot-image header hash through
the boot-image construction toolchain.
Do not copy the stock SHA1 field into a rebuilt boot image unless the rebuilt
boot payload is byte-identical to stock.

################################################################################

# 2.10 — RECOVERY IMAGE / AVB FOOTER GEOMETRY

################################################################################

Recovery partition:
Partition name                 : recovery
Block device                   : /dev/block/mmcblk0p2
Partition size                 : 33,554,432 bytes
Full SHA256                    : f5a00d0570883ddd41d838b26b3cbb5314b96a66b4b929628f2da89a898770b1

Recovery AVB footer:
Footer offset                  : 0x01ffffc0
Footer magic                   : AVBf
Footer version                 : 1.0
Original image size            : 0x00f10000 = 15,794,176 bytes
Embedded vbmeta offset         : 0x00f10000
Embedded vbmeta size           : 0x00000540 = 1,344 bytes

Recovery byte layout:
0x00000000 – 0x00f0ffff        : recovery image data
0x00f10000 – 0x00f1053f        : embedded vbmeta blob
0x00f10540 – 0x01ffffbf        : null padding
0x01ffffc0 – 0x01ffffff        : AVB footer

################################################################################

# 2.11 — DTBO BOOT ARTIFACT TOPOGRAPHY

################################################################################

Physical partition:
Partition name                 : dtbo
Block device                   : /dev/block/mmcblk0p30
By-name path                   : /dev/block/by-name/dtbo
Partition size                 : 8,388,608 bytes
Boot selection index           : androidboot.dtbo_idx=0
Full partition SHA256          : 7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b
Active DTBO table SHA256       : 2f41472d1eb39926f38b7a551d4efad438cc3f4e5e4b0b55955eb4841a24523c
ROM action                     : flash only when intentionally restoring or replacing DTBO

DTBO table header:
Header offset                  : 0x00000000
Raw header bytes               : d7 b7 ab 1e 00 00 75 56 00 00 00 20 00 00 00 20
00 00 00 01 00 00 00 20 00 00 08 00 00 00 00 00
Magic                          : 0xd7b7ab1e
Total table size               : 0x00007556 = 30,038 bytes
Live device confirmation       : bytes 4-7 = 00 00 75 56 confirmed 2026-06-08
Header size                    : 0x00000020 = 32 bytes
Entry size                     : 0x00000020 = 32 bytes
Entry count                    : 0x00000001 = 1 overlay entry
Entry table offset             : 0x00000020 = 32 bytes
Page size                      : 0x00000800 = 2048 bytes
Version                        : 0x00000000 = 0

DTBO entry 0:
Entry offset                   : 0x00000020
DTB payload size               : 0x00007516 = 29,974 bytes
DTB payload offset             : 0x00000040 = 64 bytes
ID                             : 0x00000000
Revision                       : 0x00000000
Custom field 0                 : 0x00000000
Custom field 1                 : 0x00000000
Custom field 2                 : 0x00000000
Custom field 3                 : 0x00000000

DTBO table math:
Header size                    : 0x20
Entry table size               : 0x20
DTB payload size               : 0x7516
Active used bytes              : 0x20 + 0x20 + 0x7516 = 0x7556
Active used bytes decimal      : 30,038 bytes
Partition size                 : 0x800000 = 8,388,608 bytes
Remaining partition bytes      : 0x7f8aaa = 8,358,570 bytes

FDT payload header:
FDT payload absolute offset    : 0x00000040
FDT magic                      : 0xd00dfeed
FDT total size                 : 0x00007516 = 29,974 bytes
off_dt_struct                  : 0x00000038 = 56 bytes
off_dt_strings                 : 0x00006828 = 26,664 bytes
off_mem_rsvmap                 : 0x00000028 = 40 bytes
FDT version                    : 0x00000011 = 17
FDT last compatible version    : 0x00000010 = 16
boot_cpuid_phys                : 0x00000000
size_dt_strings                : 0x00000cee = 3,310 bytes
size_dt_struct                 : 0x000067f0 = 26,608 bytes

FDT internal math:
off_dt_struct + size_dt_struct : 0x38 + 0x67f0 = 0x6828
off_dt_strings + size_dt_strings: 0x6828 + 0x0cee = 0x7516
FDT total size verified        : 0x7516 = 29,974 bytes

Extraction geometry:
Extracted DTB payload offset   : 64 bytes
Extracted DTB payload size     : 29,974 bytes
Extraction command             : dd if=dtbo.bin of=dtbo_extracted.dtb bs=1 skip=64 count=29974
Decode command                 : dtc -I dtb -O dts -o dtbo.dts dtbo_extracted.dtb

Topology conclusion:
DTBO contains exactly one overlay entry.
The active DTB payload begins at byte offset 64.
The active DTBO table occupies 30,038 bytes of an 8 MiB partition.
androidboot.dtbo_idx=0 selects the only overlay entry.
This supports A-only / single-overlay boot topology.

################################################################################

# 2.12 — AVB / VBMETA STORAGE TOPOLOGY

################################################################################

vbmeta:
Partition                      : /dev/block/mmcblk0p34
Size                           : 8,388,608 bytes
SHA256                         : 21571196f5497effb81ba3f1e81e9bf922696ad1193b72ab5495a0dea73d7256
Role                           : AVB root metadata
Content state                  : null vbmeta
Flags                          : 0x00000003
Runtime AVB state              : androidboot.verifiedbootstate=orange

vbmeta_system:
Partition                      : /dev/block/mmcblk0p5
Size                           : 8,388,608 bytes
SHA256                         : 210d8b3ba839f9997677e1bbc7340ac2f7dc09b139c6bb26f9e9868fdf145a26
Role                           : AVB chain descriptor for system logical partition

vbmeta_vendor:
Partition                      : /dev/block/mmcblk0p6
Size                           : 8,388,608 bytes
SHA256                         : 7f47526430e63774503bda0dcec8b3d72de92b80924db4798a7b9cbbbf625d97
Role                           : AVB chain descriptor for vendor logical partition

################################################################################

# 2.13 — DYNAMIC SUPER LP METADATA

################################################################################

Physical dynamic partition:
Partition name                 : super
Block device                   : /dev/block/mmcblk0p33
By-name path                   : /dev/block/by-name/super
Size                           : 4,294,967,296 bytes
SHA256                         : 91db6aefd636ace9061c8eac715c0244c0a0c3298f504be888a2c21555afbf46

Live LP metadata:
Metadata version               : 10.0
Metadata size                  : 516 bytes
Metadata max size              : 65,536 bytes
Metadata slot count            : 2

LP block-device table:
Partition name                 : super
First sector                   : 2048
Size                           : 4,294,967,296 bytes
Flags                          : none

LP group table:
Group                          : default
Maximum size                   : 0 bytes
Flags                          : none

Default group clarification:
The default group is an LP metadata structural artifact present in the
metadata table. On this device it carries zero maximum size and contains no
logical partitions. The only active dynamic partition group is main.

Group                          : main
Maximum size                   : 4,292,870,144 bytes
Flags                          : none

Canonical dynamic group:
Name                           : main
Logical partition scope        : product, system, vendor

Logical partitions from LP metadata:
product:
Group                        : main
Attributes                   : readonly
Extent                       : 0 .. 2298327 linear super 2048
Sector count                 : 2,298,328
Size                         : 1,176,743,936 bytes

system:
Group                        : main
Attributes                   : readonly
Extent                       : 0 .. 2516311 linear super 2301952
Sector count                 : 2,516,312
Size                         : 1,288,351,744 bytes

vendor:
Group                        : main
Attributes                   : readonly
Extent                       : 0 .. 568847 linear super 4818944
Sector count                 : 568,848
Size                         : 291,250,176 bytes

Dynamic partition closure:
The only active dynamic partition group is main.
product, system, and vendor are readonly logical partitions in group main.
The default group exists in LP metadata but has maximum size 0 bytes and
contains no logical partitions.

################################################################################

# 2.14 — RUNTIME DEVICE-MAPPER TOPOLOGY

################################################################################

Dynamic-super runtime realization:
/dev/block/mapper/product      -> /dev/block/dm-0
/dev/block/mapper/system       -> /dev/block/dm-1
/dev/block/mapper/vendor       -> /dev/block/dm-2

Device-mapper names:
dm-0                           : product
dm-1                           : system
dm-2                           : vendor

Runtime mount topology:
/dev/block/dm-1                : mounted at /
/dev/block/dm-2                : mounted at /vendor
/dev/block/dm-0                : mounted at /product

Runtime filesystem flags:
/                               : ext4, ro, seclabel, relatime
/vendor                         : ext4, ro, seclabel, relatime
/product                        : ext4, ro, seclabel, relatime

Canonical interpretation:
super is the physical GPT partition.
product, system, and vendor are logical partitions defined by LP metadata
inside super.
Android first-stage mount materializes these logical partitions as dm-linear
block devices.
Runtime mount sources are /dev/block/dm-* nodes exposed through
/dev/block/mapper symlinks.

################################################################################

# 2.15 — NON-SAR ROOT MOUNT TOPOLOGY

################################################################################

Root mount:
system logical partition        : /dev/block/dm-1
Mount point                     : /
Filesystem                      : ext4
Flags                           : ro,seclabel,relatime

Vendor mount:
vendor logical partition        : /dev/block/dm-2
Mount point                     : /vendor
Filesystem                      : ext4
Flags                           : ro,seclabel,relatime

Product mount:
product logical partition       : /dev/block/dm-0
Mount point                     : /product
Filesystem                      : ext4
Flags                           : ro,seclabel,relatime

Non-SAR conclusion:
system is mounted at root (/).
/system is a directory inside the root filesystem from dm-1.
There is no separate physical /system GPT partition.
BOARD_BUILD_SYSTEM_ROOT_IMAGE must not be set for this device.

################################################################################

# 2.16 — APEX RUNTIME DM CLARIFICATION

################################################################################

Runtime non-super dm device:
Mapper name                    : com.android.tzdata@297903000
Runtime device                 : /dev/block/dm-3
Mapper path                    : /dev/block/mapper/com.android.tzdata@297903000
dm name                        : com.android.tzdata@297903000

Mounts:
/dev/block/dm-3                : /apex/com.android.tzdata@297903000
/dev/block/dm-3                : /apex/com.android.tzdata

Filesystem:
Type                           : ext4
Flags                          : ro,dirsync,seclabel,nodev,noatime

Canonical interpretation:
dm-3 is created by apexd for Android tzdata APEX activation.
dm-3 is not a super logical partition.
dm-3 is not listed in lpdump.
dm-3 is not physical storage geometry.
dm-3 is runtime Android userspace module topology only.

APEX directory state:
/apex/com.android.tzdata             : active canonical mount path
/apex/com.android.tzdata@297903000   : active versioned mount path
/apex/com.android.tzdata@292500002   : inactive/staged directory state observed

################################################################################

# 2.17 — FILESYSTEM PARTITIONS, UUIDS & MOUNT ROLES

################################################################################

Mounted ext4 physical partitions:
cache:
Block device                 : /dev/block/by-name/cache
UUID                         : e85e6a3d-0f1c-473e-8757-5ffc1e2decef
Type                         : ext4
Mount point                  : /cache
SHA256                       : 1e4df5e601c83d1ecbe5efc4ac3632eca03970a530b55bca39e785d552029f9c
Mutability                   : mutable / capture-time hash

md_udc:
Block device                 : /dev/block/by-name/md_udc
UUID                         : 2fdda847-b389-4139-9dbc-f7829dcfb030
Type                         : ext4
Mount point                  : /metadata
SHA256                       : b4243b9cb034d91c4c694c5e62f696372f976c79316aa996c9481bfeac6f2e3d
Mutability                   : mutable / capture-time hash

metadata:
Block device                 : /dev/block/by-name/metadata
UUID                         : 933d2e5d-4696-4d2e-8582-cb61000d4bde
Type                         : ext4
Mount point                  : none in stock runtime
SHA256                       : b3ad8c912c2a135b427ebb03c8d8e67c55fcdb79c32c698dd6f39cbbeff06a5e
Mutability                   : inactive ghost filesystem state

nvcfg:
Block device                 : /dev/block/by-name/nvcfg
UUID                         : f892fcba-a0c9-4878-b29c-74faae323b1f
Type                         : ext4
Mount point                  : /mnt/vendor/nvcfg
SHA256                       : f92bf804164a0041fe0c3ee33883f95d2c75d9bf4b5b0c7c39409d724d41e2e6
Mutability                   : mutable calibration/config state

nvdata:
Block device                 : /dev/block/by-name/nvdata
UUID                         : 7db3fff2-83d3-4461-a6a7-965a0fff7478
Type                         : ext4
Mount point                  : /mnt/vendor/nvdata
SHA256                       : 39c3615b76276f65c590598f5c167cdd058ba52864ab4be5cd6582f6b1532c1f
Mutability                   : mutable NVRAM data state

protect1:
Block device                 : /dev/block/by-name/protect1
UUID                         : 9743e6fe-50d3-4276-ae5b-bb4d5b3b9d89
Type                         : ext4
Mount point                  : /mnt/vendor/protect_f
SHA256                       : 159b93f2ae89a1b626f0ff7dc9e851090a00270a7d7883e56b8ac2ea5d2d2bff
Mutability                   : calibration/factory state; preserve

protect2:
Block device                 : /dev/block/by-name/protect2
UUID                         : 666202e1-6301-42a1-8f86-9de9905284eb
Type                         : ext4
Mount point                  : /mnt/vendor/protect_s
SHA256                       : a1618cc5d7ff5dd8d413140e6a657fec510132a2a745d017aedb355e2b8d5f9d
Mutability                   : calibration/factory state; preserve

userdata:
Block device                 : /dev/block/by-name/userdata
UUID                         : 893837c4-5e10-4b63-a4e8-1aafe6a6901d
Type                         : ext4
Mount point                  : /data
Archived dump SHA256         : eabb9232de92bac953d7b1fbe96c7b0024626d497235ae9f1e53b50b4902e4cb
Mutability                   : live mounted FBE data; live hash not canonical

################################################################################

# 2.18 — METADATA, USERDATA & FBE MOUNT TOPOLOGY

################################################################################

Real /metadata mount:
Partition name                 : md_udc
Block device                   : /dev/block/mmcblk0p10
By-name path                   : /dev/block/by-name/md_udc
Size                           : 23,699,456 bytes
Mount point                    : /metadata
Filesystem                     : ext4
UUID                           : 2fdda847-b389-4139-9dbc-f7829dcfb030
Flags                          : rw,seclabel,nosuid,nodev,noatime,discard,data=ordered
Role                           : required early mount point for vold/FBE ordering

Inactive metadata-named physical partition:
Partition name                 : metadata
Block device                   : /dev/block/mmcblk0p11
By-name path                   : /dev/block/by-name/metadata
Size                           : 33,554,432 bytes
Filesystem                     : ext4
UUID                           : 933d2e5d-4696-4d2e-8582-cb61000d4bde
Mount point                    : none in stock runtime
Role in stock FBE              : none

Metadata topology rule:
/metadata is md_udc / mmcblk0p10.
metadata / mmcblk0p11 is a physical partition but is not the active
/metadata mount target.

Metadata contents observed:
/metadata
/metadata/apex
/metadata/apex/sessions
/metadata/lost+found
/metadata/password_slots
/metadata/gsi
/metadata/gsi/dsu
/metadata/vold

Metadata encryption state:
/metadata/vold/metadata_encryption does not exist.
No metadata encryption key directory is present.
/metadata is required for mount ordering and vold operational state, not
because this device uses metadata encryption keys.

Userdata:
Partition name                 : userdata
Block device                   : /dev/block/mmcblk0p36
By-name path                   : /dev/block/by-name/userdata
Start offset                   : 0x1_3c00_0000
Size                           : 119,706,467,840 bytes
Mount point                    : /data
Filesystem                     : ext4
UUID                           : 893837c4-5e10-4b63-a4e8-1aafe6a6901d
Archived dump SHA256           : eabb9232de92bac953d7b1fbe96c7b0024626d497235ae9f1e53b50b4902e4cb
Encryption model               : FBE v1
Contents mode                  : aes-256-xts
Filename mode                  : aes-256-cts

Mount/decrypt ordering:
first_stage_mount begins       : 14143 ms
first_stage_mount ends         : 14785 ms
/metadata mounted              : during first_stage_mount
vold fbeEnable starts          : 15026 ms
key retrieval starts           : 15029 ms
key retrieval ends             : 15054 ms
fbeEnable ends                 : 15065 ms
initUser0 starts               : 15877 ms
initUser0 ends                 : 15954 ms
post-fs-data starts            : 16260 ms

Ordering rule:
/metadata must mount before /data.
md_udc is the only valid /metadata target.
/data FBE mount completes before post-fs-data.

################################################################################

# 2.19 — STATIC FSTAB STORAGE SURFACES

################################################################################

Fstab identity:
/vendor/etc/fstab.mt6765       : primary platform fstab
/vendor/etc/fstab.mt6762       : byte-for-byte identical to fstab.mt6765
fstab source lineage           : MT6765 fstab source
Dynamic partition state        : enabled
A/B state                      : A-only
System-as-root image flag      : false

First-stage logical partitions:
system logical                 : first_stage_mount, logical
vendor logical                 : first_stage_mount, logical
product logical                : first_stage_mount, logical

Crypto-critical partitions:
md_udc                         : /metadata, ext4, first_stage_mount
userdata                       : /data, ext4, FBE

Standard mounted physical partitions:
cache                          : /cache
protect1                       : /mnt/vendor/protect_f
protect2                       : /mnt/vendor/protect_s
nvdata                         : /mnt/vendor/nvdata
nvcfg                          : /mnt/vendor/nvcfg

Raw/emmc fstab surfaces:
frp                            : /persistent
nvram                          : /nvram
proinfo                        : /proinfo
lk                             : /bootloader
lk2                            : /bootloader2
para                           : /misc
boot                           : /boot
recovery                       : /recovery
vbmeta_vendor                  : /vbmeta_vendor
vbmeta_system                  : /vbmeta_system
logo                           : /logo
expdb                          : /expdb
seccfg                         : /seccfg
tee1                           : /tee1
tee2                           : /tee2
scp1                           : /scp1
scp2                           : /scp2
sspm_1                         : /sspm_1
sspm_2                         : /sspm_2
md1img                         : /md1img
spmfw                          : /spmfw
boot_para                      : /boot_para
dtbo                           : /dtbo
vbmeta                         : /vbmeta

spmfw fstab confirmation:
Source                         : /vendor/etc/fstab.mt6765
Line                           : 87
Entry                          : /dev/block/platform/bootdevice/by-name/spmfw /spmfw emmc defaults defaults
Role                           : SPM firmware partition alias
Runtime action                 : raw emmc reference only; not a normal filesystem mount
ROM action                     : preserve stock partition; never include in normal flash script

Removable storage:
external SD pattern            : /devices/platform/externdevice*
USB OTG pattern                : /devices/platform/mt_usb*
ZRAM swap config               : /dev/block/zram0 none swap defaults zramsize=55%

################################################################################

# 2.20 — ABSENT BOOT / STORAGE SURFACES

################################################################################

Absent GPT/by-name partitions:
md1dsp                         : absent
md1arm7                        : absent
md3img                         : absent
odmdtbo                        : absent
vendor_boot                    : absent
init_boot                      : absent
vendor_kernel_boot             : absent
dedicated dtb partition         : absent

Boot slot topology:
Slot scheme                    : A-only
ro.boot.slot_suffix             : empty
ro.boot.slot                    : empty
vendor_boot                    : not present
init_boot                      : not present
vendor_kernel_boot             : not present

DTBO topology:
DTBO partition                 : /dev/block/mmcblk0p30
DTBO partition size            : 8,388,608 bytes
DTBO index                     : androidboot.dtbo_idx=0
DTBO overlay count             : 1

DTB topology:
DTB index                      : androidboot.dtb_idx=0
Dedicated dtb partition         : absent

################################################################################

# 2.21 — STORAGE-DERIVED BUILD CONSTANTS

################################################################################

Physical partition constants:
BOARD_BOOTIMAGE_PARTITION_SIZE       : 33554432
BOARD_RECOVERYIMAGE_PARTITION_SIZE   : 33554432
BOARD_DTBOIMG_PARTITION_SIZE         : 8388608
BOARD_SUPER_PARTITION_SIZE           : 4294967296
BOARD_USERDATAIMAGE_PARTITION_SIZE   : 119706467840
BOARD_CACHEIMAGE_PARTITION_SIZE      : 452984832
BOARD_FLASH_BLOCK_SIZE               : 131072

Dynamic partition constants:
BOARD_SUPER_PARTITION_GROUPS         : main
BOARD_MAIN_SIZE                      : 4292870144
BOARD_MAIN_PARTITION_LIST            : system vendor product
BOARD_DYNAMIC_PARTITION_LIST         : system vendor product

Boot image constants:
BOARD_BOOTIMG_HEADER_VERSION         : 2
BOARD_KERNEL_BASE                    : 0x40000000
BOARD_KERNEL_OFFSET                  : 0x00080000
BOARD_RAMDISK_OFFSET                 : 0x11b00000
BOARD_TAGS_OFFSET                    : 0x07880000
BOARD_DTB_OFFSET                     : 0x07880000
BOARD_KERNEL_PAGESIZE                : 2048

Mount/build topology constants:
BOARD_BUILD_SYSTEM_ROOT_IMAGE        : not set
BOARD_USES_METADATA_PARTITION        : true
BOARD_USES_RECOVERY_AS_BOOT          : false
AB_OTA_UPDATER                       : false

################################################################################

# 2.22 — VERIFICATION COMMANDS

################################################################################

Storage medium:
adb shell su -c 'cat /sys/block/mmcblk0/size'
adb shell su -c 'cat /sys/block/mmcblk0/queue/logical_block_size'
adb shell su -c 'cat /sys/block/mmcblk0/queue/physical_block_size'
adb shell su -c 'cat /sys/block/mmcblk0/device/name'
adb shell su -c 'cat /sys/block/mmcblk0/device/cid'
adb shell su -c 'cat /sys/block/mmcblk0/device/csd'

GPT raw sectors:
adb shell su -c 'dd if=/dev/block/mmcblk0 bs=512 skip=0 count=1 2>/dev/null | xxd -g1 -l 64'
adb shell su -c 'dd if=/dev/block/mmcblk0 bs=512 skip=1 count=1 2>/dev/null | xxd -g1 -l 128'
adb shell su -c 'dd if=/dev/block/mmcblk0 bs=512 skip=244277247 count=1 2>/dev/null | xxd -g1 -l 64'

Physical partitions:
adb shell su -c 'ls -la /dev/block/by-name'
adb shell su -c 'cat /proc/partitions'
adb shell su -c 'cat /sys/block/mmcblk0/mmcblk0p33/start; cat /sys/block/mmcblk0/mmcblk0p33/size'
adb shell su -c 'cat /sys/block/mmcblk0/mmcblk0p36/start; cat /sys/block/mmcblk0/mmcblk0p36/size'
adb shell su -c 'cat /sys/block/mmcblk0/mmcblk0p38/start; cat /sys/block/mmcblk0/mmcblk0p38/size'

Full-object hashes:
adb shell su -c 'sha256sum /dev/block/mmcblk0boot0'
adb shell su -c 'sha256sum /dev/block/mmcblk0boot1'
adb shell su -c 'ls -l /dev/block/mmcblk0rpmb; sha256sum /dev/block/mmcblk0rpmb'
adb shell su -c 'sha256sum /dev/block/by-name/boot_para'
adb shell su -c 'sha256sum /dev/block/by-name/recovery'
adb shell su -c 'sha256sum /dev/block/by-name/para'
adb shell su -c 'sha256sum /dev/block/by-name/expdb'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta_system'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta_vendor'
adb shell su -c 'sha256sum /dev/block/by-name/frp'
adb shell su -c 'sha256sum /dev/block/by-name/nvcfg'
adb shell su -c 'sha256sum /dev/block/by-name/nvdata'
adb shell su -c 'sha256sum /dev/block/by-name/md_udc'
adb shell su -c 'sha256sum /dev/block/by-name/metadata'
adb shell su -c 'sha256sum /dev/block/by-name/protect1'
adb shell su -c 'sha256sum /dev/block/by-name/protect2'
adb shell su -c 'sha256sum /dev/block/by-name/seccfg'
adb shell su -c 'sha256sum /dev/block/by-name/sec1'
adb shell su -c 'sha256sum /dev/block/by-name/proinfo'
adb shell su -c 'sha256sum /dev/block/by-name/md1img'
adb shell su -c 'sha256sum /dev/block/by-name/spmfw'
adb shell su -c 'sha256sum /dev/block/by-name/scp1'
adb shell su -c 'sha256sum /dev/block/by-name/scp2'
adb shell su -c 'sha256sum /dev/block/by-name/sspm_1'
adb shell su -c 'sha256sum /dev/block/by-name/sspm_2'
adb shell su -c 'sha256sum /dev/block/by-name/gz1'
adb shell su -c 'sha256sum /dev/block/by-name/gz2'
adb shell su -c 'sha256sum /dev/block/by-name/nvram'
adb shell su -c 'sha256sum /dev/block/by-name/lk'
adb shell su -c 'sha256sum /dev/block/by-name/lk2'
adb shell su -c 'sha256sum /dev/block/by-name/boot'
adb shell su -c 'sha256sum /dev/block/by-name/logo'
adb shell su -c 'sha256sum /dev/block/by-name/dtbo'
adb shell su -c 'sha256sum /dev/block/by-name/tee1'
adb shell su -c 'sha256sum /dev/block/by-name/tee2'
adb shell su -c 'sha256sum /dev/block/by-name/super'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta'
adb shell su -c 'sha256sum /dev/block/by-name/cache'
adb shell su -c 'sha256sum /dev/block/by-name/otp'
adb shell su -c 'sha256sum /dev/block/by-name/flashinfo'

Filesystem identity:
adb shell su -c 'blkid /dev/block/by-name/* 2>/dev/null'

Dynamic super:
adb shell su -c 'lpdump'

Runtime mapper:
adb shell su -c 'ls -la /dev/block/mapper'
adb shell su -c 'readlink -f /dev/block/mapper/product'
adb shell su -c 'readlink -f /dev/block/mapper/system'
adb shell su -c 'readlink -f /dev/block/mapper/vendor'
adb shell su -c 'cat /sys/block/dm-0/dm/name'
adb shell su -c 'cat /sys/block/dm-1/dm/name'
adb shell su -c 'cat /sys/block/dm-2/dm/name'
adb shell su -c 'cat /proc/mounts | grep -E " / | /product | /vendor "'

APEX dm:
adb shell su -c 'cat /sys/block/dm-3/dm/name'
adb shell su -c 'cat /proc/mounts | grep -i tzdata'
adb shell su -c 'ls -la /apex | grep tzdata'

DTBO:
adb shell su -c 'dd if=/dev/block/by-name/dtbo bs=1 count=8 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/dtbo bs=1 count=128 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/dtbo bs=1 skip=64 count=4 2>/dev/null | xxd -g1'
adb shell su -c 'sha256sum /dev/block/by-name/dtbo'
adb shell su -c 'dd if=/dev/block/by-name/dtbo bs=1 count=30038 2>/dev/null | sha256sum'

Metadata:
adb shell su -c 'readlink -f /dev/block/by-name/md_udc'
adb shell su -c 'readlink -f /dev/block/by-name/metadata'
adb shell su -c 'cat /proc/mounts | grep metadata'
adb shell su -c 'find /metadata -maxdepth 4 -xdev'

Fstab:
adb shell su -c 'grep -n "spmfw" /vendor/etc/fstab.mt6765'
adb shell su -c 'grep -n "dtbo" /vendor/etc/fstab.mt6765'

################################################################################

# 2.23 — SECTION 2 CLOSURE SUMMARY

################################################################################

Canonical storage facts:
Storage medium                 : Samsung eMMC 3V6CMB
CID                            : 150100335636434d42036c4bd6326559
Total sectors                  : 244,277,248
Sector size                    : 512 bytes
Total bytes                    : 125,069,950,976 bytes
GPT partitions                 : 38
By-name symlinks               : 40 total, including preloader_a/preloader_b
Primary GPT                    : valid at LBA1
LBA0                           : zeroed
Final LBA                      : zeroed
Last usable LBA                : 244,277,214
p38 end LBA                    : 244,277,214
preloader_a SHA256             : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
preloader_b SHA256             : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
RPMB                           : present, protected, normal block SHA256 denied
boot_para                      : active structured GFH content, magic 0x02674617
para                           : first sector all-zero; not fully zero-filled
expdb                          : first sector all-zero; not fully zero-filled
frp                            : active non-zero structured FRP data present
sec1                           : full-partition zero-filled
gz1                            : full-partition zero-filled
gz2                            : full-partition zero-filled
otp                            : full-partition zero-filled
flashinfo                      : non-zero flash information partition
Dynamic physical partition     : super / mmcblk0p33
Super SHA256                   : 91db6aefd636ace9061c8eac715c0244c0a0c3298f504be888a2c21555afbf46
Super size                     : 4,294,967,296 bytes
LP metadata version            : 10.0
LP metadata size               : 516 bytes
LP metadata max size           : 65,536 bytes
LP metadata slot count         : 2
Dynamic group                  : main
Main group max size            : 4,292,870,144 bytes
Default group                  : present, maximum size 0 bytes, no logical partitions
Logical partitions             : product, system, vendor
product runtime device         : dm-0
system runtime device          : dm-1
vendor runtime device          : dm-2
Root mount                     : dm-1 mounted at /
Vendor mount                   : dm-2 mounted at /vendor
Product mount                  : dm-0 mounted at /product
Runtime APEX dm device         : dm-3 = com.android.tzdata@297903000
Active /metadata target        : md_udc / mmcblk0p10
metadata partition p11         : physical partition, not active /metadata
Userdata exact size            : 119,706,467,840 bytes
Userdata filesystem UUID       : 893837c4-5e10-4b63-a4e8-1aafe6a6901d
Userdata archived dump SHA256  : eabb9232de92bac953d7b1fbe96c7b0024626d497235ae9f1e53b50b4902e4cb
DTBO partition size            : 8,388,608 bytes
DTBO full partition SHA256     : 7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b
DTBO active table SHA256       : 2f41472d1eb39926f38b7a551d4efad438cc3f4e5e4b0b55955eb4841a24523c
DTBO active table size         : 30,038 bytes
DTBO payload size              : 29,974 bytes
DTBO overlay count             : 1
Boot layout                    : A-only
vendor_boot/init_boot          : absent
vendor_kernel_boot             : absent
Non-SAR topology               : system logical partition mounted at /
spmfw fstab line               : /dev/block/platform/bootdevice/by-name/spmfw /spmfw emmc defaults defaults

Section 2 status:
COMPLETE as storage geometry and boot topography.
################################################################################

# END SECTION 2 — STORAGE GEOMETRY & BOOT TOPOGRAPHY

################################################################################

