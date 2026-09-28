################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 3 — FIRMWARE EXECUTION DOMAINS
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 3 — FIRMWARE EXECUTION DOMAINS
#
# PURPOSE:
# This section defines the complete MTK firmware execution-domain chain for
# the C17S platform from immutable BootROM through Linux/init handoff.
#
# The goal is not only forensic description.
# The goal is build reconstruction.
#
# If a future build, recovery, AOSP ROM, kernel, driver stack, firmware
# replacement, or full custom OS effort needs a firmware-domain value to
# preserve, emulate, replace, interface with, or deliberately remove a
# component, that value belongs here.
#
# SCOPE:
# Included:
# - BROM / immutable silicon ROM identity
# - eMMC boot regions / preloader_a / preloader_b
# - Preloader execution transcript
# - LK / LK2 execution domain
# - LK environment behavior
# - LK display/logo handoff
# - ATF / TEE / MTEE execution domain
# - ATF runtime proc-surface behavior
# - GZ / GeniZone reserved-domain state
# - SSPM firmware domain
# - SPMFW firmware domain
# - SCP firmware domain
# - SCP CHRE / tinysys / FreeRTOS sensor-hub findings
# - SCP IPI / I2C / SPI / MEMS / audio integration evidence
# - SCP runtime DVFS state
# - MD1 modem firmware domain
# - Android boot image / kernel handoff
# - Recovery image / AVB footer relationship
# - Known-good TWRP 11 recovery validation anchor
# - VBMeta / AVB execution state
# - DTBO / DTB handoff
# - Kernel command line, verbatim
# - Bootprof timing spine
# - pstore / ramoops artifact identity
# - Init / first-stage mount / FBE handoff boundary
# - Firmware preservation / replacement policy
# - Build constants derived from firmware-domain truth
#
# Excluded:
# - nRF52832 firmware and nRF protocol analysis.
# That belongs to the dedicated nRF / external sensor-controller section.
#
# - Android APK behavior and ODM application control plane.
# That belongs to APK behavior / security / purification sections.
#
# - Full HAL inventory.
# That belongs to the HAL inventory section.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : BROM / DXCC / MTKClient physical silicon evidence
# TIER 3 : Raw partition hashes and firmware headers from this exact device
# TIER 4 : /proc/pl_lk, /proc/bootprof, /proc/cmdline, /proc/scp_dvfs,
# /sys, dmesg, pstore
# TIER 5 : Proven working TWRP 11 recovery behavior
# TIER 6 : Closed DOC00–DOC21 corpus
# FINAL  : Beright / Albert acceptance
#
# RULES:
# No domain is called active unless execution is proven by boot transcript,
# runtime surface, partition content, load evidence, or hardware behavior.
#
# No reserved firmware partition is treated as active just because it exists.
#
# Full partition SHA256 identifies static firmware payloads.
#
# Proc nodes are runtime interfaces, not static files. If a proc reader blocks,
# returns zero bytes, or exposes dynamic content, that behavior is recorded as
# runtime truth.
#
# Clean AOSP bring-up preserves all MTK firmware execution domains unless a
# full matched replacement path exists for firmware + kernel driver + ABI +
# userspace dependency.
#
# SCP is NOT classified as a generic low-power helper in this database.
# SCP is classified as an active MediaTek tinysys / CHRE / FreeRTOS
# sensor-hub firmware domain with IPI, bus-master, sensor, audio, and DVFS
# surfaces.
################################################################################
################################################################################
# 3.1 — CANONICAL EXECUTION ORDER
################################################################################

Verified C17S MTK firmware execution-domain chain:

Power button / reset
↓
MediaTek BROM / BootROM
↓
eMMC boot hardware region
↓
Preloader
↓
LK / LK2 Little Kernel bootloader
↓
ATF / TEE / MTEE secure firmware handoff
↓
GZ / GeniZone reserved-domain check
↓
SSPM firmware load
↓
SPMFW firmware load
↓
SCP tinysys / CHRE / sensor-hub firmware load
↓
MD1 modem firmware load / CCCI memory setup
↓
Android boot image
↓
Linux kernel 4.9.190+
↓
DTB / DTBO handoff
↓
AVB / vbmeta state injection
↓
kernel command line
↓
first-stage init
↓
dynamic-super mapper creation
↓
/metadata mount from md_udc
↓
vold / FBE v1 setup
↓
second-stage init
↓
HAL/native daemon startup
↓
zygote / system_server / Android userspace

Canonical interpretation:
Section 3 governs the firmware execution path and handoff contracts required
for boot, recovery, clean AOSP, kernel work, and future full source-level
reconstruction.

This section does not claim all firmware is understood internally.
It records what is known, what executes, where it loads from, how it hands off,
and what must be preserved or decoded before replacement.

################################################################################

# 3.2 — DOMAIN SUMMARY MATRIX

################################################################################

Domain:
BROM
Storage / runtime source:
immutable silicon ROM
Active state:
active before eMMC
Build action:
cannot replace

Domain:
Preloader
Storage / runtime source:
mmcblk0boot0 / mmcblk0boot1
Active state:
active
Build action:
preserve

Domain:
LK / LK2
Storage / runtime source:
lk / lk2
Active state:
active
Build action:
preserve for AOSP bring-up

Domain:
ATF / TEE / MTEE
Storage / runtime source:
tee1 / tee2
Active state:
tee1 active load confirmed
Build action:
preserve

Domain:
GZ / GeniZone
Storage / runtime source:
gz1 / gz2
Active state:
reserved, zero-filled
Build action:
preserve reserved state; do not invent payload

Domain:
SSPM
Storage / runtime source:
sspm_1 / sspm_2
Active state:
active load confirmed
Build action:
preserve

Domain:
SPMFW
Storage / runtime source:
spmfw
Active state:
active load confirmed
Build action:
preserve

Domain:
SCP
Storage / runtime source:
scp1 / scp2
Active state:
active load confirmed
Corrected classification:
MediaTek tinysys / CHRE / FreeRTOS sensor-hub firmware domain
Build action:
preserve exactly; decode before any replacement

Domain:
MD1 modem
Storage / runtime source:
md1img
Active state:
active modem load confirmed
Build action:
preserve with CCCI/RIL/IMS stack

Domain:
Boot image
Storage / runtime source:
boot
Active state:
active kernel/ramdisk source
Build action:
build target

Domain:
Recovery image
Storage / runtime source:
recovery
Active state:
dedicated recovery partition
Build action:
known-good custom TWRP already proven; flash only intentionally

Domain:
VBMeta
Storage / runtime source:
vbmeta / vbmeta_system / vbmeta_vendor
Active state:
AVB disabled/orange
Build action:
keep disabled for current clean AOSP track

Domain:
DTBO
Storage / runtime source:
dtbo
Active state:
active index 0
Build action:
preserve initially

Domain:
pstore / ramoops
Storage / runtime source:
/sys/fs/pstore/console-ramoops
Active state:
runtime preserved console buffer
Build action:
preserve debugging capability

Domain:
First-stage mount / FBE
Storage / runtime source:
fstab.mt6765 + md_udc + userdata
Active state:
active
Build action:
reproduce exactly

################################################################################

# 3.3 — BROM / IMMUTABLE SILICON ROM DOMAIN

################################################################################

Domain:
Name                           : MediaTek BROM / BootROM
Execution stage                : first code after reset
Storage source                 : immutable SoC mask ROM
Replaceable                    : no
Flash target                   : none
ROM action                     : cannot be replaced; must be obeyed

Physical silicon identity:
Physical SoC                   : MediaTek MT6762V/WB
Software BSP                   : MT6765
Board                          : WP_C17S_PIX_TFT_D4
BROM HW Code                   : 0x766
HW Subcode                     : 0x8a00
HW Version                     : 0xca00
ME_ID                          : 4B5CF18EF37D514A6256199E0BBD5E6A
SOC_ID                         : 6044C8E6000D6C384827B28853120093E04F3C815E8A50E6D20D6A6C24DF1B11
eMMC CID                       : 150100335636434d42036c4bd6326559

Boot media selected by platform:
Boot device                    : eMMC
Preloader source               : eMMC boot hardware partition
Android GPT user area          : /dev/block/mmcblk0

BROM is immutable.
BROM identity proves the physical silicon is MT6762V/WB even though the
Android BSP/platform stack presents as MT6765.
Device tree and BoardConfig must respect the MT6765 software substrate while
preserving MT6762V/WB as physical silicon truth.
Any future full boot-chain replacement must account for BROM behavior and
eMMC boot hardware-region selection.

################################################################################

# 3.4 — EMMC BOOT REGIONS / PRELOADER STORAGE DOMAIN

################################################################################

eMMC boot region A:
By-name alias                  : preloader_a
Block device                   : /dev/block/mmcblk0boot0
Size                           : 4,096 KiB
SHA256                         : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
Content class                  : preloader hardware boot region
ROM action                     : NEVER TOUCH in normal ROM bring-up

eMMC boot region B:
By-name alias                  : preloader_b
Block device                   : /dev/block/mmcblk0boot1
Size                           : 4,096 KiB
SHA256                         : 9cccea6d32cede5e77f1f0ce214c8c0fae40ba51745baf6e3ba4ac7bcc75d14b
Content class                  : preloader hardware boot region
ROM action                     : NEVER TOUCH in normal ROM bring-up

Relationship:
preloader_a and preloader_b are byte-identical by SHA256.
Both are eMMC hardware boot partitions.
Neither is a GPT partition.
The GPT partition table does not contain preloader entries.

Preserve both eMMC boot hardware regions.
Do not include preloader in normal AOSP flash scripts.
Do not alter eMMC boot0/boot1 during clean ROM bring-up.
Any future preloader replacement is a separate boot-chain project requiring
BROM-level recovery and DRAM/EMI compatibility.

################################################################################

# 3.5 — PRELOADER EXECUTION DOMAIN

################################################################################

Domain:
Name                           : Preloader
Execution stage                : after BROM, before LK
Storage source                 : eMMC boot hardware region
Active evidence                : /proc/pl_lk boot transcript + bootprof
ROM action                     : preserve

Bootprof timing:
preloader                      : 1074 ms

/proc/pl_lk preloader transcript facts:
Log buffer signature           : 0xcdab3412
Preloader log size             : 0x5001
LK log size                    : 0x210c7
Build time                     : 20250613-072526
chip_hw_ver                    : CA00
chip_sw_ver                    : 0
Boot device                    : eMMC45
eMMC CID words                 : 15010033,5636434D,42036C4B,D6326559
GPT                            : valid primary GPT found
SEC CFG                        : v4 exists
SEC CFG lock state             : 3
Tool connection                : unlocked
Boot reason                    : power key boot
RTC mode                       : 32k-less mode
DRAM type trap                 : 0x6, 0x2
EMI MDL index                  : 13

Security / boot state implications:
Bootloader/tooling state       : unlocked
Verified boot runtime state    : orange
AVB enforcement                : disabled for current ROM track

Preloader configures early memory/boot media state and hands off to LK.
Clean ROM must preserve preloader and its expected memory map.
Do not alter preloader, seccfg, proinfo, protect, nvram, or RPMB during ROM
bring-up.
Future boot-chain replacement must reproduce preloader-compatible DRAM/EMI,
GPT, boot-device, and LK handoff behavior.

################################################################################

# 3.6 — LK / LK2 STORAGE DOMAIN

################################################################################

LK partition:
Name                           : lk
Block device                   : /dev/block/mmcblk0p26
Size                           : 1,048,576 bytes
SHA256                         : 3860c8857554284a03cc844b7c8435c873355a7f175a36ae96d94890c702aa41
Fstab alias                    : /bootloader
ROM action                     : NEVER TOUCH in normal ROM bring-up

LK2 partition:
Name                           : lk2
Block device                   : /dev/block/mmcblk0p27
Size                           : 1,048,576 bytes
SHA256                         : 3860c8857554284a03cc844b7c8435c873355a7f175a36ae96d94890c702aa41
Fstab alias                    : /bootloader2
ROM action                     : NEVER TOUCH in normal ROM bring-up

Relationship:
lk and lk2 are byte-identical by SHA256.
Both slots exist.
The device is Android A-only and does not expose Android A/B slot suffixing.

Header / boot transcript identity:
Active source                  : lk
lk active bit                  : 0
lk2 active bit                 : 0
Image name                     : lk
Header magic                   : 0x58881688
Payload size                   : 722,396 bytes
Device source offset           : 0x000000001C600200
Load address                   : 0x48000000
Runtime size                   : 0xB1450

LK version:
LK_VER_TAG                     : WP_C17S_PIX_TFT_D4-a1685973d-20250430100520-20250613073129

LK heap:
Heap base                      : 0x48157e54
Heap size                      : 2,785,708 bytes

Bootprof timing:
lk                             : 8057 ms
logo_t                         : 999 ms

LK owns boot image selection, bootargs injection, DTB/DTBO handoff, AVB state
injection, display/logo init, modem memory preparation, SCP/SPMFW handoff, and
Linux kernel entry.
Preserve LK/LK2 for AOSP 11 bring-up.
Future LK replacement requires reproducing DTBO, AVB, display, modem, SCP,
SPMFW, memory reservation, bootprof, and cmdline behavior.

################################################################################

# 3.7 — LK ENVIRONMENT DOMAIN

################################################################################

Runtime surface:
Node                           : /proc/lk_env
Original mode                  : -rw-rw---- root system
Mode after chmod test          : -rw-rw-rw- root system
SELinux label                  : u:object_r:proc_lk_env:s0
stat size                      : 0
Root read behavior             : returns empty / 0 bytes
dd read behavior               : 0+0 records, 0 bytes copied
system read behavior           : Permission denied
chmod result                   : mode changes possible, but no readable payload
Interpretation                 : proc node exists but exposes no finite readable
LK environment payload in this live Android session

Useful LK environment source:
Primary readable source        : /proc/pl_lk boot transcript

/proc/pl_lk LK environment values:
LK_ENV area 0                  : initialized successfully
LK_ENV area 1                  : signature wrong
Active env area                : 0

md1_ccb_cap_gear               : 1
off-mode-charge                : 1
md1_ccb_gear_list              : 1(2,20);2(2,10);3(0,0);11(2,2)

usb2jtag current setting       : 0
scp current setting            : 1

/proc/lk_env is not useful as a readable data source on live Android.
/proc/pl_lk contains the build-relevant LK environment values.
off-mode-charge=1 must be preserved behaviorally.
usb2jtag_mode=0 appears in both LK environment and kernel cmdline.
scp=1 explains why LK loads SCP firmware.
md1_ccb_* values affect modem capability gearing and must be preserved unless
modem firmware and CCCI behavior are replaced as a unit.

################################################################################

# 3.8 — LK DISPLAY / LOGO HANDOFF DOMAIN

################################################################################

LK display initialization:
LCM name                       : lk_st7701S_V3_480X640_dsi_vdo
Resolution                     : 480 x 640
Interface                      : DSI
DSI mode                       : BURST_VDO_MODE
Lane count                     : 1
PLL_CLOCK                      : 320
Framebuffer size               : 4,325,376 bytes
Framebuffer address            : 0x7fae0000
Logo DB address                : 0x7eae0000
LCM read 0x0A                  : 156
LCM connected                  : yes
Bootprof logo time             : 999 ms

LK initializes the panel/logo path before Linux.
Kernel/display stack must match the LK-initialized DSI panel assumptions.
Clean AOSP framework replacement must not assume generic phone display.
Display is 480x640 portrait hardware.
LK panel name is build-relevant for future kernel/display-driver work.

################################################################################

# 3.9 — ATF / TEE / MTEE STORAGE AND EXECUTION DOMAIN

################################################################################

TEE partition 1:
Name                           : tee1
Block device                   : /dev/block/mmcblk0p31
Size                           : 5,242,880 bytes
SHA256                         : 834a8c4332ee6de87a96594dbefd225f9a8d81dd95c5c5a55ca00f58cd6b7933
Fstab alias                    : /tee1
ROM action                     : NEVER TOUCH in normal ROM bring-up

TEE partition 2:
Name                           : tee2
Block device                   : /dev/block/mmcblk0p32
Size                           : 11,534,336 bytes
SHA256                         : e74a9c635ff82c9786e3aa950338e1d7aa1611c4b9b4d51714d6ead02cbad860
Fstab alias                    : /tee2
ROM action                     : NEVER TOUCH in normal ROM bring-up

Relationship:
tee1 and tee2 are asymmetric.
They are not identical.
tee1 is the partition loaded in the captured boot transcript.

Boot transcript identity:
Active loaded partition        : tee1
Image name                     : atf
Header magic                   : 0x58881688
Header/payload size field      : 120,832 bytes
Device source offset           : 0x000000001F800200
Load address                   : 0x47D805C0
BLDR reported ATF size         : 0x1E670
Verification time              : 0 ms

Reserved memory:
ATF reserved memory            : 0x47d80000 size 0x50000
ATF log reserved memory        : 0xbffbf000 size 0x40000

Runtime proc surface:
Directory                      : /proc/atf_log
Node                           : /proc/atf_log/atf_log
Directory mode                 : dr-xr-xr-x root root
Node mode                      : -r--r--r-- root root
Listed node size               : 0
Read behavior                  : blocking / non-terminating proc read
Timeout proof command          : timeout 5s adb shell su -c 'cat /proc/atf_log/atf_log'
Timeout result                 : 124
Manual read result             : adb shell su -c 'cat /proc/atf_log/atf_log' required Ctrl-C
Hash rule                      : not hashable as a static object; no EOF-bound byte stream

ATF/TEE/MTEE is active as part of the boot chain.
The runtime ATF log surface exists but cannot be treated as a finite readable
static file.
The primary ATF evidence is tee1 hash/header/load record plus /proc/pl_lk.
Preserve tee1 and tee2 for AOSP 11 bring-up.
Do not rely on TEE for stock FBE keymaster/gatekeeper behavior; stock recovery
decryption proof uses the resolved software/keymaster/gatekeeper path.
Future TEE replacement is out of scope for clean Android userspace bring-up.

################################################################################

# 3.10 — GZ / GENIZONE RESERVED DOMAIN

################################################################################

GZ partition 1:
Name                           : gz1
Block device                   : /dev/block/mmcblk0p23
Size                           : 16,777,216 bytes
SHA256                         : 080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e
Content state                  : full-partition zero-filled
ROM action                     : NEVER TOUCH

GZ partition 2:
Name                           : gz2
Block device                   : /dev/block/mmcblk0p24
Size                           : 16,777,216 bytes
SHA256                         : 080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e
Content state                  : full-partition zero-filled
ROM action                     : NEVER TOUCH

Relationship:
gz1 and gz2 are byte-identical by SHA256.
Both match the full 16 MiB all-zero hash.
They are reserved slots, not active firmware payloads.

GZ/GeniZone partition names exist in GPT.
No active GZ payload is present in gz1/gz2.
Do not invent a GZ firmware dependency for the clean ROM.
Preserve the partitions as reserved state.
Do not flash, format, or repurpose them.

################################################################################

# 3.11 — SSPM FIRMWARE EXECUTION DOMAIN

################################################################################

SSPM partition 1:
Name                           : sspm_1
Block device                   : /dev/block/mmcblk0p21
Size                           : 1,048,576 bytes
SHA256                         : 25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81
Fstab alias                    : /sspm_1
ROM action                     : NEVER TOUCH

SSPM partition 2:
Name                           : sspm_2
Block device                   : /dev/block/mmcblk0p22
Size                           : 1,048,576 bytes
SHA256                         : 25a6fd81a7961d184c938f74abb60fb23ddaf49ac585b10fc547f9e7a68d0a81
Fstab alias                    : /sspm_2
ROM action                     : NEVER TOUCH

Relationship:
sspm_1 and sspm_2 are byte-identical by SHA256.
Their first 64 bytes are byte-identical.

Raw header, sspm_1:
00000000: 88 16 88 58 70 8a 06 00 74 69 6e 79 73 79 73 2d  ...Xp...tinysys-
00000010: 73 73 70 6d 00 00 00 00 00 00 00 00 00 00 00 00  sspm............
00000020: 00 00 00 00 00 00 00 00 ff ff ff ff ff ff ff ff  ................
00000030: 89 16 89 58 00 02 00 00 01 00 00 00 00 00 00 00  ...X............

Raw header, sspm_2:
00000000: 88 16 88 58 70 8a 06 00 74 69 6e 79 73 79 73 2d  ...Xp...tinysys-
00000010: 73 73 70 6d 00 00 00 00 00 00 00 00 00 00 00 00  sspm............
00000020: 00 00 00 00 00 00 00 00 ff ff ff ff ff ff ff ff  ................
00000030: 89 16 89 58 00 02 00 00 01 00 00 00 00 00 00 00  ...X............

Decoded header:
Header magic                   : 0x58881688
Payload size                   : 0x00068a70 = 428,656 bytes
Image name                     : tinysys-sspm
Secondary marker               : 0x58891689

Boot transcript:
Partition loaded               : sspm_1
Image name                     : tinysys-sspm
Header magic                   : 0x58881688
Payload size                   : 428,656 bytes
Device source offset           : 0x0000000016400200
Load address                   : 0x7FF00018
Reserved memory                : 0x7ff00000 size 0xc0000
Verification time              : 0 ms
Runtime note                   : SSPM Start without DDR reserved mode

Live platform surfaces:
/sys/devices/platform/10440000.sspm
/sys/bus/platform/devices/10440000.sspm
/sys/bus/platform/drivers/sspm
/sys/class/misc/sspm
/sys/devices/virtual/misc/sspm
/sys/kernel/debug/clk/sspm_ck
/sys/kernel/debug/clk/sspm_sel
/proc/irq/206/SSPM_MBOX
/proc/irq/207/SSPM_MBOX
/proc/irq/208/SSPM_MBOX
/proc/irq/209/SSPM_MBOX

SSPM is an active firmware execution domain.
Partition size is 1 MiB, but executable payload size is 428,656 bytes.
Preserve sspm_1 and sspm_2.
Do not treat identical backup slots as removable.
Future replacement requires matching kernel power/thermal/PMIC integration.

################################################################################

# 3.12 — SPMFW EXECUTION DOMAIN

################################################################################

Partition:
Name                           : spmfw
Block device                   : /dev/block/mmcblk0p18
Size                           : 1,048,576 bytes
SHA256                         : 0e38b211d1c713460000e632d8b866becbc31492213f90fdb3135313dffb31ac
Fstab alias                    : /spmfw
ROM action                     : NEVER TOUCH

Raw header:
00000000: 88 16 88 58 d3 e9 00 00 73 70 6d 66 77 00 00 00  ...X....spmfw...
00000010: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000020: 00 00 00 00 00 00 00 00 ff ff ff ff ff ff ff ff  ................
00000030: 89 16 89 58 00 02 00 00 01 00 00 00 00 00 00 00  ...X............

Decoded header:
Header magic                   : 0x58881688
Payload size                   : 0x0000e9d3 = 59,859 bytes
Image name                     : spmfw
Secondary marker               : 0x58891689

Boot transcript:
Image name                     : spmfw
Header magic                   : 0x58881688
Payload size                   : 59,859 bytes
Image size                     : 59,859 bytes
Reserved memory                : 0x77ff0000 size 0x10000
Handoff                        : before smc call / after smc call
Load time                      : 65 ms

Live platform surfaces:
/sys/power/spm/vcore_dvfs_ctrl

SPMFW is active and loaded during LK/secure firmware handoff.
Partition size is 1 MiB, executable payload is 59,859 bytes.
Preserve stock spmfw for AOSP 11 bring-up.
Future replacement requires matching SPM/SMC/kernel low-power behavior.

################################################################################

# 3.13 — SCP FIRMWARE EXECUTION DOMAIN

################################################################################

SCP partition 1:
Name                           : scp1
Block device                   : /dev/block/mmcblk0p19
Size                           : 1,048,576 bytes
SHA256                         : d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374
Fstab alias                    : /scp1
ROM action                     : NEVER TOUCH

SCP partition 2:
Name                           : scp2
Block device                   : /dev/block/mmcblk0p20
Size                           : 1,048,576 bytes
SHA256                         : d8a06460fec3f24b0ab27a2b95dcd2f6d833fbdf1f190db19f3bc431fbdfa374
Fstab alias                    : /scp2
ROM action                     : NEVER TOUCH

Relationship:
scp1 and scp2 are byte-identical by SHA256.
LK_ENV scp setting is 1.
No active bit marked; LK loads scp1.

Boot transcript:
LK_ENV scp setting             : 1
Reserved memory                : 0x9f900000 size 0x600000
Active partition logic         : no active bit marked; loads scp1
SRAM size                      : 262,144 bytes
Loader image                   : tinysys-loader-CM4_A
Loader size                    : 1,080 bytes
Firmware image                 : tinysys-scp-CM4_A
Firmware size                  : 544,628 bytes
DRAM image                     : tinysys-scp-CM4_A_dram absent / magic error
Overlay firmware size          : 243,712 bytes
ap_loader_start                : 0x9f900000
ap_loader_size                 : 0x438
ap_firmware_start              : 0x9f900800
ap_firmware_size               : 0x3b800
ap_dram_start                  : 0x9f980000
Load result                    : load_scp(): done
Load time                      : 138 ms

Corrected classification:
SCP is not merely a generic low-power/DVFS helper.
SCP is an active MediaTek tinysys / CHRE / FreeRTOS sensor-hub firmware
execution domain.

Firmware string evidence:
Loader                         : tinysys-loader-CM4_A
Firmware                       : tinysys-scp-CM4_A
Runtime framework              : CHRE
OS/runtime                     : FreeRTOS / ARM_CM4F
IPI                            : chre_ipi_init, chre_scp_rx, chre_fw_ipi,
chre_ipi, scp_ipi.c
Logger                         : scp_logger.c, logger_update, Logger init done
Shared memory                  : __blProgramShared, __blEraseShared,
osWriteShared
Bus capability                 : SCP-side I2C and SPI master paths
I2C path                       : drivers/CM4_A/mt6765/i2c/src/i2cchre-plat.c
SPI path                       : drivers/CM4_A/mt6765/spi/src/spichre-plat.c
DVFS path                      : drivers/CM4_A/mt6765/dvfs/src/dvfs.c
Resource request path          : drivers/CM4_A/mt6765/dvfs/src/resource_req.c
Sleep path                     : drivers/CM4_A/mt6765/dvfs/src/sleep.c
MEMS framework path            : middleware/contexthub/MEMS_Driver/accGyro/accGyro.c

Confirmed firmware capability strings:
CHRE
chre_ipi_init
chre_scp_rx
chre_fw_ipi
contextHubHandleIpiRxEvent cannot find cmd
ERR: contextHubIpiRxAck failed
scp_ipi_send failed, enable a oneshot timer
send ready IPI
i2cMasterTxRxSync
i2cMasterTxRxRetryDelay
I2C0
i2c0
SPI-SCP
IPI_DVFS_DEBUG
IPI_DVFS_SLEEP
IPI_DVFS_SET_FREQ
SCP DVFS
No sleep reasons
no sleep by sleep busy
sleep: cnt=%d, total=%d
polling SPM2SCP_GPR[0]==0 timeout
Accelerometer
Gyroscope
accGyro
sensorFirmwareGyro
sensorFirmwareAcc
sensorCfgGyro
sensorCfgAcc
sensorCaliGyro
sensorCaliAcc
sensorFlushGyro
sensorFlushAcc
sensorRateGyro
sensorRateAcc
sensorPowerGyro
sensorPowerAcc
alsps
sensorRateRGBW
sensorCfgPs
sensorRatePs
sensorRateAls
sensorPowerRGBW
sensorPowerAls
sensorPowerPs
sensorPowerBaro
sensorPowerMag
LiftDetectPower
liftpdDetectPower
STEP_RECOGNITION
WAKEUP: SUBSCRIBE LIFT SENSOR DONE
noMotionPower
anyMotionPower
use legacy-wakeup
acc: enable done
acc: disable done
acc: cali done
acc: cfg done
acc: selftest done
acc: ratechg done
gyro: enable done
gyro: disable done
gyro: cali done
gyro: cfg done
gyro: selftest done
gyro: ratechg done

Runtime proc surface:
Directory                      : /proc/scp_dvfs
Directory mode                 : dr-xr-xr-x root root
Files:
/proc/scp_dvfs/pmicw_mode_debug
/proc/scp_dvfs/scp_dvfs_ctrl
/proc/scp_dvfs/scp_dvfs_sleep
/proc/scp_dvfs/scp_dvfs_state

Runtime SCP DVFS state:
SCP DVFS                       : ON
Current SCP frequency          : 250 MHz
Expected SCP frequency         : 250 MHz
SCP sleep state                : sleep mode
SCP sleep command config       : not configured by command yet
PMICW debug flag               : not configured by command yet

Feature DVFS table:
feature 0                      : freq 75, enable 0
feature 1                      : freq 356, enable 0
feature 2                      : freq 0, enable 0
feature 3                      : freq 47, enable 0
feature 4                      : freq 26, enable 0
feature 5                      : freq 0, enable 1
feature 6                      : freq 200, enable 0
feature 7                      : freq 0, enable 0

Sensor DVFS table observed:
sensor id 0                    : freq 2, enable 0
sensor id 1                    : freq 2, enable 0
sensor id 2                    : freq 2, enable 0
sensor id 3                    : freq 2, enable 0
sensor id 4                    : freq 2, enable 0
sensor id 7                    : freq 2, enable 0
sensor id 5                    : freq 2, enable 0
sensor id 18                   : freq 2, enable 0
sensor id 16                   : freq 2, enable 0
sensor id 17                   : freq 2, enable 0
sensor id 23                   : freq 2, enable 0
sensor id 43                   : freq 3, enable 0
sensor id 41                   : freq 2, enable 0
sensor id 28                   : freq 2, enable 0
sensor id 29                   : freq 2, enable 0
sensor id 35                   : freq 3, enable 0
sensor id 26                   : freq 2, enable 0
sensor id 36                   : freq 3, enable 0
trailing empty sensor slots    : sensor id 0, freq 0, enable 0 repeated

Live kernel / proc / sysfs surfaces:
/proc/aed/generate-scp
/proc/aed/generate-md32
/proc/irq/183/SCP A IPC2HOST
/proc/scp_dvfs
/sys/devices/platform/10500000.scp
/sys/devices/platform/scp_dvfs
/sys/devices/platform/mt-scpdvfs
/sys/devices/platform/10001000.scpsys
/sys/class/misc/scp
/sys/class/misc/scp_vcore_req
/sys/devices/virtual/misc/scp
/sys/devices/virtual/misc/scp_vcore_req
/sys/kernel/debug/clk/scp_ck
/sys/kernel/debug/clk/scp_sel
/sys/kernel/debug/pm_qos/scp_vcore_req
/sys/firmware/devicetree/base/scp@10500000
/sys/firmware/devicetree/base/scp_dvfs
/sys/bus/platform/devices/10500000.scp
/sys/bus/platform/devices/scp_dvfs
/sys/bus/platform/drivers/scp
/sys/bus/platform/drivers/scpdvfs

Audio-SCP live surfaces:
/sys/devices/platform/mt_soc_pcm_voice_scp
/sys/devices/platform/mt_soc_pcm_dl1_scp_spk
/sys/devices/virtual/misc/audio_ipi
/sys/class/misc/audio_ipi
/sys/firmware/devicetree/base/mt_soc_pcm_voice_scp
/sys/firmware/devicetree/base/mt_soc_pcm_dl1_scp_spk

Current dmesg limitation:
Current dmesg ring buffer did not retain early SCP boot lines.
Current filtered dmesg is dominated by late modem, WLAN, fuelgauge, and init
property activity.
SCP execution proof therefore comes from:
- /proc/pl_lk boot transcript
- scp1/scp2 full SHA256
- scp1 firmware strings
- /proc/scp_dvfs runtime state
- live /proc, /sys, IRQ, misc, devicetree, and platform nodes

SCP is active and required.
SCP is not removable vendor bloat.
SCP is not merely a DVFS helper.
SCP is an active tinysys / CHRE / FreeRTOS sensor-hub firmware domain.
SCP may own or mediate motion sensors, sensor batching, wake gestures,
context-hub behavior, IPI command flow, audio IPI paths, and low-power
firmware messaging.
Exact command map and exact physical sensor ownership remain undecoded.
Preserve scp1/scp2 exactly for clean AOSP 11 bring-up.
Preserve kernel SCP, IPI, CHRE/contexthub, DVFS, audio_ipi, and sensor HAL
interfaces unless replacing SCP firmware and kernel driver ABI together.

Database classification:
SCP_STATUS                     : ACTIVE_REQUIRED_CHRE_SENSORHUB_UNDECODED_COMMAND_MAP

################################################################################

# 3.14 — MD1 MODEM FIRMWARE EXECUTION DOMAIN

################################################################################

Partition:
Name                           : md1img
Block device                   : /dev/block/mmcblk0p17
Size                           : 104,857,600 bytes
SHA256                         : e78c1691a36ff9e08a335822645e9b801e14f4c4e8d9cf6ce383d8060bb170b6
Fstab alias                    : /md1img
ROM action                     : NEVER TOUCH in normal ROM bring-up

Raw first container header:
00000000: 88 16 88 58 64 6b 22 01 6d 64 31 72 6f 6d 00 00  ...Xdk".md1rom..
00000010: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000020: 00 00 00 00 00 00 00 00 00 00 00 00 ff ff ff ff  ................
00000030: 89 16 89 58 00 02 00 00 01 00 00 00 00 00 00 01  ...X............

Decoded header:
Header magic                   : 0x58881688
First payload size             : 0x01226b64 = 19,032,932 bytes
First image name               : md1rom
Secondary marker               : 0x58891689

Boot transcript:
Modem load path                : load_modem_image v2.0
ccci_tag_mem                   : 0x9fff0000 size 0x10000
CCCI modem memory              : 0x8e000000 size 0x10000000
md_smem_ncache                 : 0x7a000000 size 0x170000
CCCI modem2 reserve            : 0x78000000 size 0x1600000
MD1 baseband version           : MOLY.LR12A.R3.MP.V107.

LK environment modem values:
md1_ccb_cap_gear               : 1
md1_ccb_gear_list              : 1(2,20);2(2,10);3(0,0);11(2,2)

MD1 modem firmware is active and loaded through the MTK CCCI path.
md1img is a 100 MiB partition, but the first md1rom payload header identifies
a 19,032,932-byte payload.
Preserve md1img, CCCI memory map, modem properties, radio stack, RIL, IMS,
and VoLTE dependencies for AOSP 11 bring-up.
Future modem replacement is out of scope unless the entire modem/CCCI/RIL
stack is replaced and carrier function is revalidated.

################################################################################

# 3.15 — ANDROID BOOT IMAGE / KERNEL EXECUTION DOMAIN

################################################################################

Boot partition:
Name                           : boot
Block device                   : /dev/block/mmcblk0p28
Size                           : 33,554,432 bytes
SHA256                         : fc7d52cda504683b332ca1bf4e0c0492fcc54498244f8a863e15a9465ad62851
Fstab alias                    : /boot
ROM action                     : ROM build target / flash per build

Boot image header:
Header magic                   : ANDROID!
Header version                 : 2
Page size                      : 2048 bytes
Kernel size                    : 9,246,082 bytes
Kernel load address            : 0x40080000
Kernel base                    : 0x40000000
Kernel offset                  : 0x00080000
Ramdisk size                   : 923,597 bytes
Ramdisk load address           : 0x51b00000
Ramdisk offset                 : 0x11b00000
Tags load address              : 0x47880000
Tags offset                    : 0x07880000
DTB offset                     : 0x07880000
OS version                     : 10.0.0
OS patch level                 : 2024-03
Boot image SHA1 field          : c6ccf000f6513dafd9a2018c87d799922348887c

LK handoff:
DTB kernel address             : 0x47880000 size 0x80000
Kernel address                 : 0x40080000 size 0x7800000
Kernel real size               : 0x008d1582 = 9,246,082 bytes
Ramdisk address                : 0x51b00000 size 0x4000000

BoardConfig constants:
BOARD_BOOTIMG_HEADER_VERSION   : 2
BOARD_KERNEL_BASE              : 0x40000000
BOARD_KERNEL_OFFSET            : 0x00080000
BOARD_RAMDISK_OFFSET           : 0x11b00000
BOARD_TAGS_OFFSET              : 0x07880000
BOARD_DTB_OFFSET               : 0x07880000
BOARD_KERNEL_PAGESIZE          : 2048
BOARD_FLASH_BLOCK_SIZE         : 131072

boot.img is the clean ROM kernel/ramdisk target.
Current clean AOSP 11 track preserves stock kernel initially.
Any rebuilt boot image must preserve header version, page size, load offsets,
ramdisk placement, DTB offset, and kernel cmdline policy.
The stock boot image SHA1 field must not be copied into a modified boot image
unless the rebuilt payload is byte-identical.

################################################################################

# 3.16 — KNOWN-GOOD TWRP 11 RECOVERY VALIDATION ANCHOR

################################################################################

Known-good milestone:
A fully functioning TWRP 11 recovery has already been built for this C17S
device.

TWRP status:
Base                           : TWRP 11 / Android 11 recovery base
Device                         : LOKMAT APPLLP 5 MAX / C17S
Recovery partition             : dedicated recovery partition
Boot status                    : boots successfully
Recovery log state             : zero recovery log errors
FBE decryption                 : working
Userdata decryption            : working
md_udc metadata target         : proven correct
metadata p11 trap              : proven not active /metadata
fstab parsing                  : corrected
crypto stack                   : solved
keymaster/gatekeeper path      : solved through recovery compatibility work
vendor namespace issue         : solved through crypto isolation/bind path
splash/freeze issue            : solved

TWRP engineering truths proven by execution:
Boot image constants are operational.
Recovery partition is usable.
bootimg header v2 path is operational.
Kernel base and offsets are operational.
Ramdisk offset is operational.
Tags/DTB offset 0x07880000 is operational.
Page size 2048 is operational.
md_udc is the correct /metadata target.
userdata FBE v1 can be decrypted when the ramdisk and crypto stack are correct.
Stock firmware domains can support custom recovery boot.
Clean AOSP ROM bring-up is not starting from theoretical bootability.

TWRP is a proof anchor for Section 3.
Boot/recovery/FBE constants are validated by execution, not just decoded from
headers.
Clean AOSP work should reuse the proven boot/recovery/crypto truths and
preserve firmware domains while replacing the ODM Android framework/control
plane.

################################################################################

# 3.17 — KERNEL COMMAND LINE DOMAIN

################################################################################

Live command:
adb shell su -c 'cat /proc/cmdline'

Verbatim live /proc/cmdline:
console=tty0 console=ttyS0,921600n1 vmalloc=400M slub_debug=OFZPU page_owner=on swiotlb=noforce androidboot.hardware=mt6765 maxcpus=8 loop.max_part=7 firmware_class.path=/vendor/firmware has_battery_removed=0 androidboot.boot_devices=bootdevice,11230000.mmc root=/dev/ram  androidboot.verifiedbootstate=orange bootopt=64S3,32N2,64N2 buildvariant=user androidboot.atm=disabled androidboot.meta_log_disable=0 printk.disable_uart=0 bootprof.pl_t=1074 bootprof.lk_t=8057 bootprof.logo_t=999 androidboot.serialno=MTK0002507301608400 androidboot.bootreason=PowerKey gpt=1 usb2jtag_mode=0 mrdump_ddrsv=yes mrdump_cb=0x10e800,0x1400 androidboot.dtb_idx=0 androidboot.dtbo_idx=0

Static cmdline values to preserve / understand:
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

Dynamic LK-injected values:
bootprof.pl_t=1074
bootprof.lk_t=8057
bootprof.logo_t=999
androidboot.serialno=MTK0002507301608400
androidboot.bootreason=PowerKey
androidboot.dtb_idx=0
androidboot.dtbo_idx=0

Authoring rule:
Do not hardcode dynamic LK-injected values into product/vendor/system
properties.
Do not author ro.boot.* properties in product/vendor/system property files.
androidboot.hardware=mt6765 is cmdline-derived even though the project tracks
MT6762V/WB as physical silicon.

The cmdline is the kernel handoff contract.
Clean boot.img must preserve the static command-line behavior unless a kernel
replacement intentionally changes it with matching driver/device-tree updates.
Dynamic bootprof, serial, bootreason, dtb_idx, and dtbo_idx values are runtime
injection products and not authored static properties.

################################################################################

# 3.18 — DTB / DTBO HANDOFF DOMAIN

################################################################################

DTBO partition:
Name                           : dtbo
Block device                   : /dev/block/mmcblk0p30
Size                           : 8,388,608 bytes
SHA256                         : 7a6723402020bedeed3d91572e64c7beea56ac5f18c11c76aa754820e47bde4b
Active DTBO table SHA256       : 2f41472d1eb39926f38b7a551d4efad438cc3f4e5e4b0b55955eb4841a24523c
Fstab alias                    : /dtbo
ROM action                     : preserve stock DTBO initially

DTBO table:
Magic                          : 0xd7b7ab1e
Total table size               : 0x00007556 = 30,038 bytes
Header size                    : 32 bytes
Entry size                     : 32 bytes
Entry count                    : 1
Entry table offset             : 32 bytes
Page size                      : 2048 bytes
Version                        : 0

DTBO entry 0:
DTB payload size               : 0x00007516 = 29,974 bytes
DTB payload offset             : 0x00000040 = 64 bytes

FDT payload:
FDT magic                      : 0xd00dfeed
FDT total size                 : 29,974 bytes
off_dt_struct                  : 0x38
off_dt_strings                 : 0x6828
size_dt_strings                : 0x0cee
size_dt_struct                 : 0x67f0

LK / cmdline handoff:
lk_main_dtb size               : 89,545 bytes
PCB_ID                         : SEVB (MTK0963)
dtb_idx                        : 0
dtbo_idx                       : 0
dtbo_size                      : 29,974 bytes
load_dtbo_buffer               : 6 ms
dtb_overlay                    : 7 ms
lk_dtb_init                    : 39 ms

DTBO contains exactly one overlay entry.
androidboot.dtbo_idx=0 selects the only overlay.
androidboot.dtb_idx=0 selects DTB index 0.
Clean AOSP 11 must preserve stock DTBO initially.
Future kernel/device-tree rebuild must reproduce this DTB/DTBO relationship.

################################################################################

# 3.19 — AVB / VBMETA EXECUTION STATE DOMAIN

################################################################################

vbmeta:
Partition                      : /dev/block/mmcblk0p34
Size                           : 8,388,608 bytes
SHA256                         : 21571196f5497effb81ba3f1e81e9bf922696ad1193b72ab5495a0dea73d7256
Content state                  : null vbmeta
Flags                          : 0x00000003
Runtime boot state             : orange
ROM action                     : flash null vbmeta when intentionally keeping AVB disabled

vbmeta_system:
Partition                      : /dev/block/mmcblk0p5
Size                           : 8,388,608 bytes
SHA256                         : 210d8b3ba839f9997677e1bbc7340ac2f7dc09b139c6bb26f9e9868fdf145a26

vbmeta_vendor:
Partition                      : /dev/block/mmcblk0p6
Size                           : 8,388,608 bytes
SHA256                         : 7f47526430e63774503bda0dcec8b3d72de92b80924db4798a7b9cbbbf625d97

LK AVB transcript:
AVB memory reserve             : 0xb9bbf000 size 0x6400000
Initial vbmeta verify          : HASH_MISMATCH
boot/recovery verify time      : 620 ms
avb_ret                        : 0
ret                            : 0
Boot state                     : orange
Root of trust send             : failed, 0xffffffff

Runtime command-line state:
androidboot.verifiedbootstate  : orange

AVB is disabled/orange for the current clean AOSP track.
BOARD_AVB_ENABLE := false.
Do not declare android.software.verified_boot while AVB remains disabled/orange.
Do not attempt AVB re-enable during first clean AOSP bring-up.
Production hardening may revisit AVB only after functional ROM closure.

################################################################################

# 3.20 — RECOVERY IMAGE / AVB FOOTER DOMAIN

################################################################################

Recovery partition:
Name                           : recovery
Block device                   : /dev/block/mmcblk0p2
Size                           : 33,554,432 bytes
SHA256                         : f5a00d0570883ddd41d838b26b3cbb5314b96a66b4b929628f2da89a898770b1
Fstab alias                    : /recovery
ROM action                     : flash only when intentionally updating recovery

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

Recovery is dedicated and separate from boot.
recovery-as-boot is false.
TWRP/recovery development targets the recovery partition independently.
Recovery AVB footer geometry must be preserved or intentionally regenerated.

################################################################################

# 3.21 — BOOTPROF TIMING SPINE

################################################################################

Live command:
adb shell su -c 'cat /proc/bootprof'

Bootprof header:
BOOT PROF (unit:msec)

Core firmware timings:
preloader                      : 1074 ms
lk                             : 8057 ms
logo time                      : 999 ms

Early kernel timing entries observed:
ON                             : 34.317845 ms
init_menu                      : 294.058846 ms, 16.019384 ms duration
of_platform_default_populate   : 329.836692 ms, 29.824846 ms duration
pm_sysrq_init                  : 358.162538 ms, 15.420308 ms duration
ccci_util_init                 : 389.004461 ms, 27.099923 ms duration
event_trace_init               : 422.060923 ms, 18.645615 ms duration
inet_init                      : 458.740615 ms, 20.100154 ms duration
mt6577-uart probe              : 940.763232 ms, 451.984924 ms duration
second mt6577-uart probe       : 964.086847 ms, 21.701923 ms duration
mtk8250_platform_driver_init   : 966.012540 ms, 477.434232 ms duration
__mt_gpufreq_init              : 1000.348386 ms, 15.696846 ms duration
musb-hdrc probe                : 1070.508386 ms, 38.357154 ms duration
musb_init                      : 1070.552309 ms, 39.835000 ms duration

Known mount/FBE spine:
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

Bootprof is the timing spine for future bring-up comparison.
AOSP 11 bring-up should compare preloader/LK/kernel/mount/FBE milestones
against this known-good spine.
Large timing drift in preloader/LK/SCP/modem/init path indicates firmware or
mount-flow regression.

################################################################################

# 3.22 — PSTORE / RAMOOPS DOMAIN

################################################################################

Runtime artifact:
Path                           : /sys/fs/pstore/console-ramoops
Size                           : 238,984 bytes
Device SHA256                  : cb5cea377fd266bf97548ec5605fbc8a9b992f186069e7f1ef7679abd80c2785
Host pull SHA256               : cb5cea377fd266bf97548ec5605fbc8a9b992f186069e7f1ef7679abd80c2785
Ownership                      : system log
Mode                           : -r--r-----
Content state                  : readable pstore console buffer; noisy/corrupted text
Evidence role                  : persistent early-kernel / previous-console buffer
Interpretation rule            : auxiliary boot-domain evidence only; do not treat
corrupted bytes as clean log text

Reserved memory relationship:
ram_console                    : 0x47c80000 size 0x10000
pstore                         : 0x47c90000 size 0xe0000

pstore/ramoops is available and must be kept functional for bring-up debugging.
pstore evidence is valuable for panics, reboot loops, and early-kernel crash
analysis.
The console-ramoops hash identifies the complete captured byte stream without
requiring the full noisy buffer to be embedded in normal database prose.

################################################################################

# 3.23 — INIT / FIRST-STAGE MOUNT / FBE HANDOFF BOUNDARY

################################################################################

Boundary:
Firmware-domain execution ends at Linux kernel / first-stage init handoff.
Android storage/mount behavior begins at first-stage init, but is included here
because firmware cmdline, DTBO, dynamic partition flags, and boot image layout
directly determine whether init can proceed.

Critical facts:
Device is A-only.
Device has no vendor_boot.
Device has no init_boot.
Device has no vendor_kernel_boot.
Device is non-SAR hybrid.
system logical partition mounts at /.
BOARD_BUILD_SYSTEM_ROOT_IMAGE must not be set.
Dynamic partition group is main.
Logical partitions inside super are system, vendor, product.
/metadata mounts from md_udc / mmcblk0p10.
by-name/metadata / mmcblk0p11 is a ghost trap and must not be used.
FBE is v1.
fstab fileencryption flag is fileencryption=aes-256-xts only.
Filename mode is supplied by ro.crypto.volume.filenames_mode=aes-256-cts.
Do not add filename-mode suffix in fstab.

Firmware and kernel handoff must preserve the same dynamic partition, fstab,
md_udc, and FBE ordering.
A clean ROM that boots but breaks /metadata or FBE is not a valid bring-up.
md_udc is non-negotiable.
The metadata-named partition p11 must not be substituted for md_udc.

################################################################################

# 3.24 — FIRMWARE PRESERVATION / REPLACEMENT POLICY MATRIX

################################################################################

BROM:
Current track action           : cannot replace
Future replacement requirement : none; immutable silicon

preloader_a / preloader_b:
Current track action           : preserve
Future replacement requirement : BROM recovery path + DRAM/EMI compatibility

lk / lk2:
Current track action           : preserve
Future replacement requirement : reproduce DTBO, AVB, display, modem, SCP,
SPMFW, cmdline, memory handoff

tee1 / tee2:
Current track action           : preserve
Future replacement requirement : secure-world replacement project only

gz1 / gz2:
Current track action           : preserve reserved zero state
Future replacement requirement : do not invent active firmware

sspm_1 / sspm_2:
Current track action           : preserve
Future replacement requirement : matching kernel power/thermal/PMIC behavior

spmfw:
Current track action           : preserve
Future replacement requirement : matching SPM/SMC/kernel low-power behavior

scp1 / scp2:
Current track action           : preserve exactly
Current classification         : ACTIVE_REQUIRED_CHRE_SENSORHUB_UNDECODED_COMMAND_MAP
Future replacement requirement : SCP firmware + SCP kernel driver + IPI ABI +
CHRE/context hub path + DVFS + audio_ipi +
sensor HAL dependencies + reserved memory

md1img:
Current track action           : preserve
Future replacement requirement : full modem/CCCI/RIL/IMS/VoLTE replacement and
carrier validation

boot:
Current track action           : build target
Future replacement requirement : preserve header constants and cmdline policy

recovery:
Current track action           : known-good TWRP path proven; flash intentionally
Future replacement requirement : preserve AVB footer geometry or regenerate
deliberately

vbmeta / vbmeta_system / vbmeta_vendor:
Current track action           : keep disabled/orange for current track
Future replacement requirement : re-enable only after stable clean ROM closure

dtbo:
Current track action           : preserve initially
Future replacement requirement : replace only with verified DTB/DTBO source parity

pstore / ramoops:
Current track action           : preserve debug functionality
Future replacement requirement : required for bring-up diagnostics

Clean AOSP 11 rule:
Preserve all stock MTK firmware domains.
Replace Android framework/userspace ODM control plane first.
Do not replace firmware during the same step as ODM framework removal.
One major execution-domain variable changed per boot test cycle.

################################################################################

# 3.25 — BUILD CONSTANTS DERIVED FROM SECTION 3

################################################################################

Boot image:
BOARD_BOOTIMG_HEADER_VERSION         := 2
BOARD_KERNEL_BASE                    := 0x40000000
BOARD_KERNEL_OFFSET                  := 0x00080000
BOARD_RAMDISK_OFFSET                 := 0x11b00000
BOARD_TAGS_OFFSET                    := 0x07880000
BOARD_DTB_OFFSET                     := 0x07880000
BOARD_KERNEL_PAGESIZE                := 2048
BOARD_FLASH_BLOCK_SIZE               := 131072
BOARD_BOOTIMAGE_PARTITION_SIZE       := 33554432
BOARD_RECOVERYIMAGE_PARTITION_SIZE   := 33554432
BOARD_DTBOIMG_PARTITION_SIZE         := 8388608

AVB:
BOARD_AVB_ENABLE                     := false
android.software.verified_boot       := do not declare while orange/disabled

Boot layout:
AB_OTA_UPDATER                       := false
BOARD_USES_RECOVERY_AS_BOOT          := false
vendor_boot                          := absent
init_boot                            := absent
vendor_kernel_boot                   := absent

Dynamic partitions:
PRODUCT_USE_DYNAMIC_PARTITIONS       := true
BOARD_SUPER_PARTITION_GROUPS         := main
BOARD_MAIN_PARTITION_LIST            := system vendor product
BOARD_DYNAMIC_PARTITION_LIST         := system vendor product

System root:
BOARD_BUILD_SYSTEM_ROOT_IMAGE        := not set

Metadata/FBE:
BOARD_USES_METADATA_PARTITION        := true
Active metadata target               := md_udc / mmcblk0p10
fileencryption                       := aes-256-xts
filename mode                        := ro.crypto.volume.filenames_mode=aes-256-cts

SCP:
Preserve scp1/scp2                   := true
Treat SCP as CHRE sensor-hub domain   := true
Replace SCP without kernel ABI match  := false
Remove SCP as bloat                   := false

################################################################################

# 3.26 — VERIFICATION COMMANDS

################################################################################

Platform identity:
adb shell getprop ro.hardware
adb shell getprop ro.boot.hardware
adb shell getprop ro.board.platform
adb shell getprop ro.vendor.mediatek.platform
adb shell getprop ro.product.board

Firmware partition hashes:
adb shell su -c 'sha256sum /dev/block/mmcblk0boot0'
adb shell su -c 'sha256sum /dev/block/mmcblk0boot1'
adb shell su -c 'sha256sum /dev/block/by-name/lk'
adb shell su -c 'sha256sum /dev/block/by-name/lk2'
adb shell su -c 'sha256sum /dev/block/by-name/tee1'
adb shell su -c 'sha256sum /dev/block/by-name/tee2'
adb shell su -c 'sha256sum /dev/block/by-name/gz1'
adb shell su -c 'sha256sum /dev/block/by-name/gz2'
adb shell su -c 'sha256sum /dev/block/by-name/scp1'
adb shell su -c 'sha256sum /dev/block/by-name/scp2'
adb shell su -c 'sha256sum /dev/block/by-name/sspm_1'
adb shell su -c 'sha256sum /dev/block/by-name/sspm_2'
adb shell su -c 'sha256sum /dev/block/by-name/spmfw'
adb shell su -c 'sha256sum /dev/block/by-name/md1img'
adb shell su -c 'sha256sum /dev/block/by-name/boot'
adb shell su -c 'sha256sum /dev/block/by-name/recovery'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta_system'
adb shell su -c 'sha256sum /dev/block/by-name/vbmeta_vendor'
adb shell su -c 'sha256sum /dev/block/by-name/dtbo'

Firmware headers:
adb shell su -c 'dd if=/dev/block/mmcblk0boot0 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/mmcblk0boot1 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/lk bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/lk2 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/tee1 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/tee2 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/scp1 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/scp2 bs=1 count=256 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/sspm_1 bs=1 count=64 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/sspm_2 bs=1 count=64 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/spmfw bs=1 count=64 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/md1img bs=1 count=64 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/boot bs=1 count=2048 2>/dev/null | xxd -g1'
adb shell su -c 'dd if=/dev/block/by-name/dtbo bs=1 count=128 2>/dev/null | xxd -g1'

Runtime boot surfaces:
adb shell su -c 'cat /proc/cmdline'
adb shell su -c 'cat /proc/bootprof'
adb shell su -c 'cat /proc/pl_lk'
adb shell su -c 'ls -la /proc/lk_env'
adb shell su -c 'cat /proc/lk_env'
adb shell su -c 'dd if=/proc/lk_env bs=1 count=4096 2>&1 | xxd -g1'

ATF runtime surface:
adb shell su -c 'ls -la /proc/atf_log'
adb shell su -c 'ls -la /proc/atf_log/atf_log'
timeout 5s adb shell su -c 'cat /proc/atf_log/atf_log'

SCP runtime surface:
adb shell su -c 'ls -la /proc/scp_dvfs'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_ctrl'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_sleep'
adb shell su -c 'cat /proc/scp_dvfs/scp_dvfs_state'
adb shell su -c 'cat /proc/scp_dvfs/pmicw_mode_debug'

SCP firmware string classification:
adb shell su -c 'strings -a /dev/block/by-name/scp1 | grep -Ei "tinysys|scp|sensor|acc|gyro|als|prox|baro|hr|spo|wiite|nrf|i2c|uart|dvfs|sleep|cmd|debug|log|ipi|share|md32|cm4" | head -200'
adb shell su -c 'strings -a /dev/block/by-name/scp1 | grep -Ei "feature|sensor id|scp_dvfs|pmicw|ipi|vow|audio|mdla|apu|ccu|cam|thermal|power|wakeup|rtc|watchdog" | head -200'
adb shell su -c 'find /proc /sys -maxdepth 4 2>/dev/null | grep -Ei "scp|tinysys|ipi|sspm|spmfw|dvfs|vow|md32" | head -300'

pstore / ramoops:
adb shell su -c 'ls -la /sys/fs/pstore'
adb shell su -c 'wc -c /sys/fs/pstore/console-ramoops'
adb shell su -c 'sha256sum /sys/fs/pstore/console-ramoops'

AVB / properties:
adb shell su -c 'getprop | grep -Ei "ro.boot|androidboot|verifiedboot|verity|vbmeta|slot|bootreason|hardware|dtbo|dtb|selinux|build.system_root_image|dynamic_partitions"'
adb shell su -c 'which avbctl; avbctl get-verity; avbctl get-verification'

Modem:
adb shell su -c 'dmesg | grep -Ei "ccci|modem|md1|MOLY"'
adb shell su -c 'getprop vendor.mtk.md1.status'
adb shell su -c 'getprop vendor.volte_md_status'

################################################################################

# 3.27 — SECTION 3 CLOSURE SUMMARY

################################################################################

Closed firmware execution-domain facts:
Physical SoC                   : MT6762V/WB
Software BSP                   : MT6765
BROM HW Code                   : 0x766
Board                          : WP_C17S_PIX_TFT_D4
Boot storage                   : Samsung eMMC 3V6CMB
Preloader regions              : mmcblk0boot0 / mmcblk0boot1
Preloader hashes               : identical
LK partitions                  : lk / lk2
LK hashes                      : identical
Active LK source               : lk
LK load address                : 0x48000000
LK version tag                 : WP_C17S_PIX_TFT_D4-a1685973d-20250430100520-20250613073129
LK display panel               : lk_st7701S_V3_480X640_dsi_vdo
Known-good recovery            : fully decrypting TWRP 11 recovery already built
TWRP validation                : recovery boot, FBE decrypt, md_udc, boot constants proven
ATF active source              : tee1
ATF load address               : 0x47D805C0
ATF reserved memory            : 0x47d80000 size 0x50000
ATF log reserved memory        : 0xbffbf000 size 0x40000
/proc/atf_log behavior         : exists, read blocks, timeout 124
GZ partitions                  : gz1 / gz2 zero-filled reserved slots
SSPM active source             : sspm_1
SSPM payload                   : tinysys-sspm, 428,656 bytes
SSPM load address              : 0x7FF00018
SPMFW payload                  : spmfw, 59,859 bytes
SPMFW reserved memory          : 0x77ff0000 size 0x10000
SCP active source              : scp1
SCP loader                     : tinysys-loader-CM4_A
SCP firmware                   : tinysys-scp-CM4_A
SCP classification             : active CHRE / tinysys / FreeRTOS sensor-hub domain
SCP capabilities              : IPI, I2C, SPI, MEMS, acc/gyro, ALS/PS, baro,
mag, lift, step, wakeup, audio_ipi, DVFS
SCP current frequency          : 250 MHz
SCP runtime state              : sleep mode
SCP command map                : not fully decoded
MD1 first payload              : md1rom, 19,032,932 bytes
MD1 baseband                   : MOLY.LR12A.R3.MP.V107.
Boot image header              : v2, page size 2048
Kernel base                    : 0x40000000
Kernel offset                  : 0x00080000
Ramdisk offset                 : 0x11b00000
Tags / DTB offset              : 0x07880000
Kernel size                    : 9,246,082 bytes
Ramdisk size                   : 923,597 bytes
DTBO active table              : 30,038 bytes
DTBO payload                   : 29,974 bytes
DTBO overlay count             : 1
dtb_idx                        : 0
dtbo_idx                       : 0
AVB state                      : disabled / orange
vbmeta flags                   : 0x00000003
pstore console-ramoops         : present, 238,984 bytes
pstore SHA256                  : cb5cea377fd266bf97548ec5605fbc8a9b992f186069e7f1ef7679abd80c2785
Boot layout                    : A-only
vendor_boot                    : absent
init_boot                      : absent
vendor_kernel_boot             : absent
recovery-as-boot               : false
Active /metadata               : md_udc / mmcblk0p10
FBE                            : v1, aes-256-xts contents, aes-256-cts filenames

The clean AOSP 11 ROM preserves all MTK firmware execution domains and
replaces the Android framework/userspace ODM control plane first.
SCP is not removable bloat and not a generic DVFS-only blob; it is an active
CHRE/tinysys sensor-hub firmware domain requiring preservation and later
command-map decoding.
Firmware replacement is not part of the initial clean ROM track.
Future source-level replacement of kernel, LK, TEE, SCP, SSPM, SPMFW, or
modem firmware requires this section as the compatibility contract.

Section 3 status:
COMPLETE as corrected MTK firmware execution-domain map for C17S.
################################################################################

# END SECTION 3 — FIRMWARE EXECUTION DOMAINS

################################################################################

