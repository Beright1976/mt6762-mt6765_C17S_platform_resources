################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 11 — HARDWARE SECURITY SUBSTRATE
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 11 — HARDWARE SECURITY SUBSTRATE
# Closure     : CLOSED WITH STOCK-VERSUS-RESEARCH CAPTURE BOUNDARIES
#
# PURPOSE:
# This section defines the hardware-backed and hardware-adjacent security
# substrate of the C17S platform: silicon security blocks, eFuse/DXCC/TRNG,
# RPMB, /dev/sec, TEE/ATF/Mobicore declarations, security partitions, AVB
# metadata, boot-lock state, FBE metadata path, Keymaster/Gatekeeper HAL
# implementation path, DRM/Widevine/CAS security boundary, Secure Element SIM1
# boundary, and the signed-stock privileged-code admission surface where it
# intersects hardware trust.
#
# This is a hardware digital-twin section. It is not an APK behavior report,
# not a traffic-forensics report, not a legal threat conclusion, and not a
# secret-extraction record. Software appears here only when it determines which
# process, HAL, service, binder surface, signed package, or Android framework
# component is allowed to consume hardware security substrates.
#
# If a future TWRP build, clean AOSP 11 bring-up, device tree, kernel rebuild,
# SELinux policy, AVB/vbmeta policy, FBE repair, RPMB handling, Keymaster or
# Gatekeeper replacement, DRM/Widevine audit, Secure Element/UICC isolation,
# or ODM-control-plane removal workflow needs to preserve, emulate, replace,
# restrict, or deliberately exclude a security substrate behavior, that
# information belongs here.
#
# SCOPE:
# Included:
# - MediaTek silicon security blocks declared by devicetree: DXCC, EFUSEC,
#   SECURITY_AO, TRNG, Trustonic/Mobicore, TEE reserved memory, GCE secure
#   thread/gcpu references
# - RPMB block device and RPMB character device boundary, size, major/minor,
#   sysfs class, and public handling rule
# - /dev/sec character device and sec module capture-state parameters
# - Security partitions: vbmeta, vbmeta_system, vbmeta_vendor, tee1, tee2,
#   gz1, gz2, seccfg, sec1, proinfo, frp, otp, nvram, nvdata, nvcfg,
#   protect1, protect2, md_udc, metadata
# - AVB/vbmeta headers, partition sizes, SHA256 hashes, current capture flags,
#   lock-state and verified-boot properties
# - TEE/ATF/GZ/Mobicore execution-domain evidence and absence/presence limits
# - Keymaster 4.0 HAL process, service, binary, library linkage, and backend
#   classification
# - Gatekeeper 1.0 HAL process, service, binary, symlink, library linkage, and
#   backend classification
# - Keystore, vold, lock_settings, trust, oem_lock, persistent_data_block, and
#   FBE hardware-adjacent path
# - Widevine/DRM/CAS/Clearkey HAL service state, binary hashes, and current
#   security-level evidence
# - Secure Element SIM1 / UICC hardware-adjacent boundary and NFC non-promotion
# - UID1000 signed-stock package admission surface only as a hardware trust
#   boundary; individual APK behavior remains owned by software-security docs
# - Stock-versus-research capture boundary for bootloader, SELinux, AVB/verity,
#   and Widevine level
#
# Excluded:
# - Raw RPMB reads/writes, RPMB key extraction, replay-counter extraction, or
#   any device-unique secret extraction
# - DXCC-derived key extraction or attempts to recover hardware unique keys
# - Widevine provisioning blobs, device-unique DRM secrets, or private
#   provisioning identifiers
# - Full APK behavior analysis for Stopwatch, WearDevicemsg, AdupsFota, Wiite
#   Store, WiiteCommon, or Devicedaemon. Those belong to software/APK security
#   sections and are referenced here only as trust-admission surfaces.
# - Full baseband/RF/SIM behavior. Section 9 owns baseband/radio/RF. Section 11
#   records Secure Element SIM1 and UICC trust boundaries only.
# - Full coprocessor/sensor protocol behavior. Section 7 owns nRF/PAR/Pixart
#   protocol truth. Section 11 records only security-boundary implications.
# - Full external interface proof. Section 10 owns external board boundaries.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S evidence supplied from adb shell su -c captures
# TIER 2 : /sys/firmware/devicetree/base, /sys/devices/platform, /sys/class,
#          /dev, /proc, mount, fstab, getprop, service list, lshal, ps -A -Z
# TIER 3 : Full-partition SHA256 hashes and first-header hexdumps captured from
#          live block devices
# TIER 4 : Closed Sections 1-10 as cross-section authorities for SoC, storage,
#          firmware execution, bus fabric, power, coprocessor, radio, visual,
#          and board-boundary facts
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# - Stock factory posture and current research-capture posture are separate.
# - Current Magisk root, SELinux permissive, unlocked bootloader, orange state,
#   and AVB/verity-disabled behavior are owner-created research conditions and
#   must not be written as shipped-stock facts.
# - Stock locked/enforcing state does not disprove ODM control. Signed stock
#   images can admit ODM UID1000 packages and privileged services inside the
#   trusted boot envelope.
# - A security partition is not proof of an active secure-world payload.
# - A devicetree security node is hardware declaration proof; it is not proof
#   that a userspace HAL is hardware-backed.
# - A running Keymaster or Gatekeeper HAL is not proof of TEE backing. Backend
#   linkage and strings determine implementation classification.
# - RPMB existence is hardware boundary proof. RPMB contents and secrets must
#   not be extracted for the public database.
# - Widevine L3 is the current research-capture state only unless locked-stock
#   evidence proves the same level. Stock Widevine may have changed after
#   bootloader unlock; therefore stock level remains unconfirmed.
# - SIM identifiers, ICCID, IMSI, serial numbers, subscriber values, operator
#   private identifiers, DRM identifiers, and device-unique security values are
#   PUBLIC_REDACT.
################################################################################

################################################################################
# 11.0 — SECTION CLASSIFICATION LEGEND
################################################################################

CONFIRMED_DT_SECURITY_HARDWARE      : Security block declared by live devicetree with compatible/reg evidence.
CONFIRMED_PLATFORM_DEVICE           : /sys/devices/platform runtime device exists.
CONFIRMED_SECURITY_PARTITION        : Named partition exists with block mapping and size.
CONFIRMED_SECURITY_HEADER           : Security partition has recognized header/magic.
CONFIRMED_SECURITY_HASH             : Full block-device SHA256 captured.
CONFIRMED_RPMB_BOUNDARY             : RPMB block or character device exists; no content extraction performed.
CONFIRMED_SEC_DEVICE                : /dev/sec and sec sysfs class/module surface exist.
CONFIRMED_TEE_DECLARATION           : TEE/Mobicore/Trustonic declared in DT or libraries.
CONFIRMED_TEE_RESERVED_MEMORY       : TEE memory reservation exists in DT.
NO_TEE_USERSPACE_NODE_CAPTURED      : No matching /dev/tee*, /dev/tz*, /dev/mtee*, /dev/trust* node found in capture.
CONFIRMED_HAL_RUNNING               : HAL process/service is running in live Android capture.
CONFIRMED_FRAMEWORK_SERVICE         : Binder/framework service is present.
SOFTWARE_BACKED_SECURITY_PATH       : Backend linkage proves software/pure-soft/default implementation.
PASSTHROUGH_SECURITY_PATH           : Backend linkage proves passthrough/default HAL path.
NOT_CONFIRMED_TEE_BACKED            : No evidence proves TEE-backed implementation for this path.
CURRENT_RESEARCH_CAPTURE_STATE      : State is current owner-modified research state, not stock-shipping claim.
STOCK_STATE_USER_ATTESTED           : User states stock factory behavior existed before unlock; retain as owner context.
STOCK_STATE_UNCONFIRMED_BY_CAPTURE  : Plausible stock state lacks preserved locked-stock capture evidence.
AVB_PRESENT                         : AVB/vbmeta infrastructure exists.
AVB_CURRENTLY_NON_ENFORCING         : Current capture reports orange/unlocked/disable flags.
FBE_ACTIVE                          : File-based encryption active in current Android state.
PUBLIC_REDACT                       : Value must be removed or generalized in public copies.
TRUSTED_CODE_ADMISSION_SURFACE      : Signed-stock system/privileged code admitted into hardware trust boundary.
DO_NOT_EXTRACT_SECRET               : Evidence surface must be identified but not dumped for public database.
OPEN_LEAD                           : Needs preserved stock capture, kernel source, teardown, or additional proof.
################################################################################

################################################################################
# 11.1 — CANONICAL SECTION 11 SUMMARY
################################################################################

Device security substrate summary:

Physical SoC security family       : MediaTek MT6762V/WB silicon under MT6765 BSP identity
Android base                       : Android 10 / API 29 stock base; AOSP 11 target project
Kernel                             : 4.9.190+
Current root context               : uid=0, context u:r:magisk:s0
Current SELinux state              : Permissive, /sys/fs/selinux/enforce=0
Stock SELinux state                : STOCK_STATE_USER_ATTESTED enforcing before owner research modifications
Current bootloader state           : ro.boot.flash.locked=0
Stock bootloader state             : STOCK_STATE_USER_ATTESTED locked before owner unlock
Current verified boot state        : ro.boot.verifiedbootstate=orange
AVB version property               : ro.boot.avb_version=1.1
Current vbmeta flags               : 0x00000003 observed in vbmeta/vbmeta_system/vbmeta_vendor headers
Current AVB/verity state           : AVB_PRESENT / AVB_CURRENTLY_NON_ENFORCING in research capture
Stock AVB/verity state             : STOCK_STATE_USER_ATTESTED locked/enforcing before owner unlock
FBE state                          : ro.crypto.state=encrypted; ro.crypto.type=file
FBE filename mode                  : ro.crypto.volume.filenames_mode=aes-256-cts
Userdata                           : /dev/block/mmcblk0p36, 119,706,467,840 bytes, mounted /data
Active metadata partition           : md_udc, /dev/block/mmcblk0p10, 23,699,456 bytes, mounted /metadata
Ghost metadata partition            : metadata, /dev/block/mmcblk0p11, 33,554,432 bytes, not mounted as /metadata in capture
RPMB block device                  : /dev/block/mmcblk0rpmb, major 179 minor 96, 32,768 sectors, 512-byte sectors, 16,777,216 bytes
RPMB char device                   : /dev/rpmb0, major 227 minor 0
Security char device               : /dev/sec, major 182 minor 0
DXCC DT block                      : dxcc_sec@10210000, compatible mediatek,dxcc_sec, base 0x10210000, size 0x00100000
EFUSEC DT block                    : efusec@11c50000, compatible mediatek,efusec, base 0x11c50000, size 0x00000100
SECURITY_AO DT block               : security_ao@1001a000, compatible mediatek,security_ao, base 0x1001a000, size 0x00100000
TRNG DT block                      : trng@1020f000, compatible mediatek,trng, base 0x1020f000, size 0x00100000
Trustonic/Mobicore DT declaration  : /sys/firmware/devicetree/base/mobicore, compatible trustonic,mobicore
TEE reserved memory                : /sys/firmware/devicetree/base/memory/tee_reserved_mem present
TEE1 partition                     : /dev/block/mmcblk0p31, 5,242,880 bytes, header identifies atf
TEE2 partition                     : /dev/block/mmcblk0p32, 11,534,336 bytes, header identifies atf
GZ1/GZ2 partitions                 : /dev/block/mmcblk0p23 and p24, both 16,777,216 bytes, first 128 bytes zero, identical SHA256 in capture
Keymaster HAL                      : android.hardware.keymaster@4.0-service running, u:r:hal_keymaster_default:s0
Keymaster backend classification   : PURE_SOFT / PASSTHROUGH path confirmed by libkeymaster4 strings and libpuresoftkeymasterdevice dependency
Gatekeeper HAL                     : android.hardware.gatekeeper@1.0-service running, u:r:hal_gatekeeper_default:s0
Gatekeeper backend classification  : SOFTWARE Gatekeeper confirmed; gatekeeper.default.so -> libSoftGatekeeper.so
Keystore service                   : android.security.keystore binder service present; keystore process running
DRM/Widevine/CAS                   : DRM, MediaDRM, CAS, Clearkey, default DRM, Widevine HALs running
Current Widevine level             : CURRENT_RESEARCH_CAPTURE_STATE L3 via ro.vendor.mtk_widevine_drm_l3_support=1
Stock Widevine level               : STOCK_STATE_UNCONFIRMED_BY_CAPTURE; may have differed before unlock
Secure Element HAL                 : android.hardware.secure_element@1.0::ISecureElement/SIM1 running
Secure Element boundary            : SIM1 / UICC; no NFC hardware feature declared in capture
OEM lock service                   : binder oem_lock present
Persistent data block              : binder persistent_data_block present; frp partition exists
FRP partition                      : /dev/block/mmcblk0p7, 1,048,576 bytes
OTP partition                      : /dev/block/mmcblk0p37, 45,088,768 bytes
Trusted code admission             : 50+ UID1000 packages exist in signed system/product/vendor image; hardware-facing Wiite packages run as system UID
Public redaction                   : serial, SIM identifiers, ICCID, IMSI, operator private values, DRM IDs, RPMB contents, key material
################################################################################

################################################################################
# 11.2 — STOCK VERSUS RESEARCH CAPTURE BOUNDARY
################################################################################

This section contains two intentionally separated security states.

11.2.1 — Stock factory state, owner-attested

The owner states the device was stock locked and SELinux enforcing before the
bootloader was unlocked and research modifications were applied. This state is
recorded as STOCK_STATE_USER_ATTESTED, not as a current live capture.

Stock-attested properties:
- Bootloader locked before owner unlock.
- SELinux enforcing before owner research modifications.
- Verified boot / verity behaved as a normal stock Android shipping state.
- Current Magisk, permissive, orange, and AVB/verity-disabled state was created
  by the owner for research, TWRP, decryption, and ROM bring-up.

Important interpretation:
- A locked bootloader and enforcing SELinux protect the signed stock image from
  user modification.
- They do not prevent ODM-signed system/product/vendor UID1000 packages from
  executing inside the trusted stock envelope.
- Therefore the ODM privileged software plane is not explained by rooting. It
  predates owner unlock when present in stock partitions.

11.2.2 — Current research capture state, live-proven

Live capture values:
- Root identity: uid=0(root), gid=0(root), context u:r:magisk:s0
- SELinux: getenforce=Permissive
- SELinux enforce node: /sys/fs/selinux/enforce = 0
- ro.boot.flash.locked=0
- ro.boot.verifiedbootstate=orange
- ro.boot.avb_version=1.1
- ro.secure=1
- ro.adb.secure=1
- ro.debuggable=0
- ro.crypto.state=encrypted
- ro.crypto.type=file
- ro.crypto.volume.filenames_mode=aes-256-cts
- ro.vendor.mtk_widevine_drm_l3_support=1

Classification:
- CURRENT_RESEARCH_CAPTURE_STATE
- AVB_PRESENT / AVB_CURRENTLY_NON_ENFORCING
- FBE_ACTIVE
- CURRENT_CAPTURE_WIDEVINE_L3
- DO_NOT_WRITE_AS_STOCK_FACTORY_POSTURE
################################################################################

################################################################################
# 11.3 — SILICON SECURITY BLOCKS / DEVICETREE REGISTER MAP
################################################################################

The C17S exposes MediaTek security blocks through live devicetree and platform
runtime devices. These are hardware-substrate facts. Their existence does not
prove that each Android HAL path is hardware-backed.

11.3.1 — DXCC security block

DT node:
- /sys/firmware/devicetree/base/dxcc_sec@10210000

Properties:
- name       : dxcc_sec
- compatible : mediatek,dxcc_sec
- reg cells  : 00000000 00002110 00000000 00100000
- decoded    : base 0x10210000, size 0x00100000
- interrupts : 00000000 a2000000 04000000

Runtime platform surface:
- /sys/devices/platform/10210000.dxcc_sec
- /sys/devices/platform/10210000.dxcc_sec/of_node
- /sys/devices/platform/10210000.dxcc_sec/uevent
- /sys/devices/platform/10210000.dxcc_sec/power

Classification:
- CONFIRMED_DT_SECURITY_HARDWARE
- CONFIRMED_PLATFORM_DEVICE
- DO_NOT_EXTRACT_SECRET

Notes:
- DXCC is the correct hardware-substrate anchor for derived-key discussion.
- Section 11 records the block, address, and trust-boundary role only.
- No HUK, derived key, wrapped key, DRM secret, RPMB key, or device-unique
  secret is extracted or requested.

11.3.2 — EFUSE controller

DT node:
- /sys/firmware/devicetree/base/efusec@11c50000

Properties:
- name       : efusec
- compatible : mediatek,efusec
- reg cells  : 00000000 0000c511 00000000 00000100
- decoded    : base 0x11c50000, size 0x00000100

Runtime platform surfaces:
- /sys/devices/platform/11c50000.efusec
- /sys/devices/platform/10004000.efuse_dbg
- /sys/devices/platform/mt-pmic/ldo_vefuse_status
- /sys/devices/platform/mt-pmic/ldo_vefuse_voltage

Classification:
- CONFIRMED_DT_SECURITY_HARDWARE
- CONFIRMED_PLATFORM_DEVICE
- DO_NOT_EXTRACT_SECRET

11.3.3 — SECURITY_AO block

DT node:
- /sys/firmware/devicetree/base/security_ao@1001a000

Properties:
- name       : security_ao
- compatible : mediatek,security_ao
- reg cells  : 00000000 00a00110 00000000 00100000
- decoded    : base 0x1001a000, size 0x00100000

Runtime platform surface:
- /sys/devices/platform/1001a000.security_ao

Classification:
- CONFIRMED_DT_SECURITY_HARDWARE
- CONFIRMED_PLATFORM_DEVICE

11.3.4 — TRNG block

DT node:
- /sys/firmware/devicetree/base/trng@1020f000

Properties:
- name       : trng
- compatible : mediatek,trng
- reg cells  : 00000000 00f02010 00000000 00100000
- decoded    : base 0x1020f000, size 0x00100000
- interrupts : 00000000 a1000000 08000000

Runtime platform surface:
- /sys/devices/platform/1020f000.trng

Runtime random devices:
- /dev/random  major 1 minor 8  label u:object_r:random_device:s0
- /dev/urandom major 1 minor 9  label u:object_r:random_device:s0

Classification:
- CONFIRMED_DT_SECURITY_HARDWARE
- CONFIRMED_PLATFORM_DEVICE
- RUNTIME_RANDOM_DEVICE_PRESENT

11.3.5 — Trustonic / Mobicore declaration

DT node:
- /sys/firmware/devicetree/base/mobicore

Properties:
- name       : mobicore
- compatible : trustonic,mobicore
- interrupts : 00000000 0f010000 01000000

Runtime platform surface:
- /sys/devices/platform/mobicore

System libraries present:
- /system/lib/libteeservice_client.trustonic.so
- /system/lib/vendor.trustonic.teeregistry@1.0.so
- /system/lib64/libteeservice_client.trustonic.so
- /system/lib64/vendor.trustonic.teeregistry@1.0.so

Device-node capture result:
- No /dev/tee*, /dev/teei*, /dev/tz*, /dev/mtee*, /dev/trust*, /dev/trustonic*, /dev/t-base-tui, or /dev/gud node was found by the Section 11 capture glob.

Classification:
- CONFIRMED_TEE_DECLARATION
- CONFIRMED_PLATFORM_DEVICE
- NO_TEE_USERSPACE_NODE_CAPTURED
- NOT_CONFIRMED_TEE_BACKED for Android Keymaster/Gatekeeper userspace paths

11.3.6 — TEE reserved memory and GCE secure references

DT paths observed:
- /sys/firmware/devicetree/base/memory/tee_reserved_mem
- /sys/firmware/devicetree/base/memory-ssmr-features/sdsp-tee-sharedmem-size
- /sys/firmware/devicetree/base/gce@10238000/secure_thread
- /sys/firmware/devicetree/base/gce@10238000/gcpu_base
- /sys/firmware/devicetree/base/chosen/non_secure_sram
- /sys/firmware/devicetree/base/chosen/plat_dbg_info,key

Classification:
- CONFIRMED_TEE_RESERVED_MEMORY
- CONFIRMED_SECURE_GCE_DECLARATION
- SECURITY_DEBUG_CONTEXT_PRESENT
- DO_NOT_EXTRACT_SECRET for plat_dbg_info,key
################################################################################

################################################################################
# 11.4 — RPMB AND /DEV/SEC HARDWARE BOUNDARY
################################################################################

11.4.1 — RPMB character device

Live node:
- /dev/rpmb0

Attributes:
- Type       : character device
- Owner      : root:root
- Mode       : crw-------
- Label      : u:object_r:teei_rpmb_device:s0
- Major/minor: 227:0

Sysfs:
- /sys/class/rpmb/rpmb0 -> ../../devices/virtual/rpmb/rpmb0
- /sys/devices/virtual/rpmb/rpmb0/dev = 227:0
- /sys/devices/virtual/rpmb/rpmb0/uevent:
  - MAJOR=227
  - MINOR=0
  - DEVNAME=rpmb0

Classification:
- CONFIRMED_RPMB_BOUNDARY
- DO_NOT_EXTRACT_SECRET

11.4.2 — RPMB block device

Live node:
- /dev/block/mmcblk0rpmb

Attributes:
- Type       : block device
- Owner      : root:root
- Mode       : brw-------
- Label      : u:object_r:rpmb_block_device:s0
- Major/minor: 179:96

Block sysfs:
- /sys/class/block/mmcblk0rpmb/size = 32768
- /sys/class/block/mmcblk0rpmb/queue/logical_block_size = 512
- /sys/class/block/mmcblk0rpmb/queue/physical_block_size = 512
- /sys/class/block/mmcblk0rpmb/dev = 179:96
- /sys/class/block/mmcblk0rpmb/uevent:
  - MAJOR=179
  - MINOR=96
  - DEVNAME=mmcblk0rpmb
  - DEVTYPE=disk

Decoded size:
- 32768 sectors * 512 bytes = 16,777,216 bytes
- Canonical RPMB size: 16 MiB

Classification:
- CONFIRMED_RPMB_BOUNDARY
- HARDWARE_STORAGE_SECURITY_BOUNDARY
- DO_NOT_READ_PUBLICLY
- DO_NOT_WRITE

11.4.3 — /dev/sec character device

Live node:
- /dev/sec

Attributes:
- Type       : character device
- Owner      : root:system
- Mode       : crw-rw----
- Label      : u:object_r:sec_device:s0
- Major/minor: 182:0

Sysfs:
- /sys/class/sec/sec -> ../../devices/virtual/sec/sec
- /sys/devices/virtual/sec/sec/dev = 182:0
- /sys/devices/virtual/sec/sec/uevent:
  - MAJOR=182
  - MINOR=0
  - DEVNAME=sec

Module sysfs:
- /sys/module/sec
- /sys/module/sec/parameters/lks = 0
- /sys/module/sec/parameters/recovery_done = 0

Classification:
- CONFIRMED_SEC_DEVICE
- SECURITY_CHAR_DEVICE
- CURRENT_RESEARCH_CAPTURE_STATE for lks/recovery_done values
- OPEN_LEAD unless kernel source maps parameter semantics exactly
################################################################################

################################################################################
# 11.5 — SECURITY PARTITION REGISTER
################################################################################

11.5.1 — AVB/vbmeta partition chain

vbmeta:
- by-name      : /dev/block/by-name/vbmeta
- target       : /dev/block/mmcblk0p34
- size         : 8,388,608 bytes
- SHA256       : 21571196f5497effb81ba3f1e81e9bf922696ad1193b72ab5495a0dea73d7256
- header magic : 41 56 42 30 (AVB0)
- header flags : 0x00000003 observed in current capture

vbmeta_system:
- by-name      : /dev/block/by-name/vbmeta_system
- target       : /dev/block/mmcblk0p5
- size         : 8,388,608 bytes
- SHA256       : 210d8b3ba839f9997677e1bbc7340ac2f7dc09b139c6bb26f9e9868fdf145a26
- header magic : AVB0
- header flags : 0x00000003 observed in current capture

vbmeta_vendor:
- by-name      : /dev/block/by-name/vbmeta_vendor
- target       : /dev/block/mmcblk0p6
- size         : 8,388,608 bytes
- SHA256       : 7f47526430e63774503bda0dcec8b3d72de92b80924db4798a7b9cbbbf625d97
- header magic : AVB0
- header flags : 0x00000003 observed in current capture

Runtime AVB/lock properties:
- ro.boot.avb_version=1.1
- ro.boot.flash.locked=0
- ro.boot.verifiedbootstate=orange
- sys.oem_unlock_allowed=1
- ro.oem_unlock_supported=1
- ro.frp.pst=/dev/block/platform/bootdevice/by-name/frp

Classification:
- AVB_PRESENT
- AVB_CURRENTLY_NON_ENFORCING
- CURRENT_RESEARCH_CAPTURE_STATE
- DO_NOT_WRITE_AS_STOCK_FACTORY_STATE

11.5.2 — TEE / ATF partitions

tee1:
- by-name      : /dev/block/by-name/tee1
- target       : /dev/block/mmcblk0p31
- size         : 5,242,880 bytes
- SHA256       : 834a8c4332ee6de87a96594dbefd225f9a8d81dd95c5c5a55ca00f58cd6b7933
- header       : MediaTek image header observed
- image string : atf

tee2:
- by-name      : /dev/block/by-name/tee2
- target       : /dev/block/mmcblk0p32
- size         : 11,534,336 bytes
- SHA256       : e74a9c635ff82c9786e3aa950338e1d7aa1611c4b9b4d51714d6ead02cbad860
- header       : MediaTek image header observed
- image string : atf

Classification:
- CONFIRMED_SECURITY_PARTITION
- CONFIRMED_SECURITY_HEADER
- ATF/TEE_EXECUTION_DOMAIN_PARTITION
- NOT_PROOF_OF_ANDROID_KEYMASTER_TEE_BACKING

11.5.3 — GZ / GenieZone partitions

gz1:
- by-name      : /dev/block/by-name/gz1
- target       : /dev/block/mmcblk0p23
- size         : 16,777,216 bytes
- SHA256       : 080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e
- first 128 B  : all zero in capture

gz2:
- by-name      : /dev/block/by-name/gz2
- target       : /dev/block/mmcblk0p24
- size         : 16,777,216 bytes
- SHA256       : 080acf35a507ac9849cfcba47dc2ad83e01b75663a516279c8b9d243b719643e
- first 128 B  : all zero in capture

Classification:
- CONFIRMED_SECURITY_PARTITION
- GZ_RESERVED_PARTITION
- ZERO_HEADER_CURRENT_CAPTURE
- IDENTICAL_HASH_PAIR_CURRENT_CAPTURE
- NO_ACTIVE_PAYLOAD_PROVEN

11.5.4 — seccfg / sec1 / proinfo / FRP / OTP

seccfg:
- by-name      : /dev/block/by-name/seccfg
- target       : /dev/block/mmcblk0p14
- size         : 8,388,608 bytes
- SHA256       : 1452b719457a466d689cb3bf43acd9689b3b25a65229ba6e3a4b01dfefe9a381
- header       : starts with 4d 4d 4d 4d ... 45 45 45 45 in capture

sec1:
- by-name      : /dev/block/by-name/sec1
- target       : /dev/block/mmcblk0p15
- size         : 2,097,152 bytes
- SHA256       : 5647f05ec18958947d32874eeb788fa396a05d0bab7c1b71f112ceb7e9b31eee
- first 128 B  : all zero in capture

proinfo:
- by-name      : /dev/block/by-name/proinfo
- target       : /dev/block/mmcblk0p16
- size         : 3,145,728 bytes

frp:
- by-name      : /dev/block/by-name/frp
- target       : /dev/block/mmcblk0p7
- size         : 1,048,576 bytes
- ro.frp.pst   : /dev/block/platform/bootdevice/by-name/frp

otp:
- by-name      : /dev/block/by-name/otp
- target       : /dev/block/mmcblk0p37
- size         : 45,088,768 bytes

Classification:
- CONFIRMED_SECURITY_PARTITION
- FRP_BOUNDARY_PRESENT
- OEM_LOCK_AND_PERSISTENT_DATA_BLOCK_ADJACENT
- DO_NOT_EXTRACT_DEVICE_UNIQUE_VALUES

11.5.5 — NVRAM / NVDATA / NVCFG / PROTECT partitions

nvram:
- by-name      : /dev/block/by-name/nvram
- target       : /dev/block/mmcblk0p25
- size         : 67,108,864 bytes

nvdata:
- by-name      : /dev/block/by-name/nvdata
- target       : /dev/block/mmcblk0p9
- size         : 67,108,864 bytes

nvcfg:
- by-name      : /dev/block/by-name/nvcfg
- target       : /dev/block/mmcblk0p8
- size         : 33,554,432 bytes

protect1:
- by-name      : /dev/block/by-name/protect1
- target       : /dev/block/mmcblk0p12
- size         : 8,388,608 bytes
- fstab mount  : /mnt/vendor/protect_f

protect2:
- by-name      : /dev/block/by-name/protect2
- target       : /dev/block/mmcblk0p13
- size         : 11,395,072 bytes
- fstab mount  : /mnt/vendor/protect_s

Classification:
- CONFIRMED_SECURITY_ADJACENT_PARTITION
- MODEM/RADIO/CALIBRATION/PROVISIONING_BOUNDARY
- PUBLIC_REDACT for private provisioning contents

11.5.6 — FBE metadata and userdata partitions

userdata:
- by-name      : /dev/block/by-name/userdata
- target       : /dev/block/mmcblk0p36
- size         : 119,706,467,840 bytes
- mount        : /dev/block/mmcblk0p36 on /data type ext4

md_udc:
- by-name      : /dev/block/by-name/md_udc
- target       : /dev/block/mmcblk0p10
- size         : 23,699,456 bytes
- label        : u:object_r:metadata_block_device:s0
- mount        : /dev/block/mmcblk0p10 on /metadata type ext4
- fstab        : first_stage_mount, formattable

metadata:
- by-name      : /dev/block/by-name/metadata
- target       : /dev/block/mmcblk0p11
- size         : 33,554,432 bytes
- label        : u:object_r:block_device:s0
- mount        : not mounted as /metadata in capture

Classification:
- FBE_ACTIVE
- MD_UDC_IS_ACTIVE_METADATA
- METADATA_PARTITION_IS_GHOST_OR_INACTIVE_IN_CAPTURE
################################################################################

################################################################################
# 11.6 — KEYMASTER 4.0 HARDWARE-ADJACENT PATH
################################################################################

11.6.1 — Runtime services and process

Init service:
- init.svc.vendor.keymaster-4-0=running

HIDL service:
- android.hardware.keymaster@4.0::IKeymasterDevice/default

Process:
- user/domain : system / u:r:hal_keymaster_default:s0
- process     : android.hardware.keymaster@4.0-service
- observed PID: 413 in capture

Binary:
- /vendor/bin/hw/android.hardware.keymaster@4.0-service
- owner/mode : root:shell, -rwxr-xr-x
- label      : u:object_r:hal_keymaster_default_exec:s0
- size       : 11,848 bytes
- SHA256     : 712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8

11.6.2 — Keymaster libraries and hashes

Vendor:
- /vendor/lib64/libkeymaster4.so
  - size   : 91,032 bytes
  - SHA256 : 154557c43bbd6492203197c83f3ed8c808e15f5721ea3eb4c90b8edc265b48fd

- /vendor/lib64/libkeymaster4support.so
  - size   : 165,776 bytes
  - SHA256 : a20193a3b6706d3e90d1822afbff0af31e8ec73f6a6dee8c1d706d16139e4090

System support:
- /system/lib64/libsoftkeymasterdevice.so
  - size   : 207,808 bytes
  - SHA256 : 168af4cdb3eeb40e5c9bf01d8a22d89ed7c7e9411825b9b88f642995bba18f60

- /system/lib64/libkeymaster_portable.so
  - size   : 330,536 bytes
  - SHA256 : fa2f7614215593c3bf011be4c6bfbcf01e9699683f277276515670cf18b925e1

- /system/lib64/libkeymaster_messages.so
  - size   : 101,240 bytes
  - SHA256 : 518ca208ef2fbf87beb3a9d33df5042782aa8659a6a9ab0407bf2f941d850693

Additional framework libraries:
- /system/lib/android.hardware.keymaster@3.0.so
- /system/lib/android.hardware.keymaster@4.0.so
- /system/lib/libkeymaster4support.so
- /system/lib64/android.hardware.keymaster@3.0.so
- /system/lib64/android.hardware.keymaster@4.0.so
- /system/lib64/libkeymaster4support.so

11.6.3 — Backend strings and classification

Key strings observed in /vendor/lib64/libkeymaster4.so:
- libpuresoftkeymasterdevice.so
- libkeymaster_portable.so
- KeymasterPassthroughKeyFactory
- KeymasterPassthroughEngine
- KeymasterPassthroughOperation
- AndroidKeymaster4Device
- CreateKeymasterDevice(SecurityLevel)
- PureSoftKeymasterContext

Key strings observed in /system/lib64/libsoftkeymasterdevice.so:
- libsoftkeymasterdevice.so
- SoftwareKeyBlobMaker
- Keymaster0Engine
- Keymaster1Engine
- Software key factory paths

Classification:
- CONFIRMED_HAL_RUNNING
- SOFTWARE_BACKED_SECURITY_PATH
- PASSTHROUGH_SECURITY_PATH
- NOT_CONFIRMED_TEE_BACKED
- NOT_PROOF_OF_ABSENT_DXCC_SILICON

Canonical statement:
The C17S exposes a running Android Keymaster 4.0 HAL, but the captured
userspace implementation is pure-soft/passthrough at the HAL/library layer.
This does not erase the DXCC silicon block. It means the Android Keymaster path
observed in stock userspace is not proven to use a TEE-backed or hardware-backed
Keymaster implementation.
################################################################################

################################################################################
# 11.7 — GATEKEEPER 1.0 HARDWARE-ADJACENT PATH
################################################################################

11.7.1 — Runtime services and process

Init services:
- init.svc.gatekeeperd=running
- init.svc.vendor.gatekeeper-1-0=running

HIDL service:
- android.hardware.gatekeeper@1.0::IGatekeeper/default

Processes:
- gatekeeperd
  - user/domain : system / u:r:gatekeeperd:s0
  - process     : gatekeeperd /data/misc/gatekeeper

- HAL process
  - user/domain : system / u:r:hal_gatekeeper_default:s0
  - process     : android.hardware.gatekeeper@1.0-service
  - observed PID: 523 in capture

Binary:
- /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
- owner/mode : root:shell, -rwxr-xr-x
- label      : u:object_r:hal_gatekeeper_default_exec:s0
- size       : 11,600 bytes
- SHA256     : 53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a

11.7.2 — Gatekeeper software backend proof

64-bit HAL path:
- /vendor/lib64/hw/gatekeeper.default.so -> libSoftGatekeeper.so
- SHA256 target: a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a

32-bit HAL path:
- /vendor/lib/hw/gatekeeper.default.so -> libSoftGatekeeper.so
- SHA256 target: beef8d59dd10d54e8eb35941ba06e3dd5ef0268481e93c9c17eba3f1b78c5f46

Backend strings:
- SoftGatekeeper
- Soft Gatekeeper HAL
- Soft Gatekeeper device created
- Opening Software Gatekeeper device
- soft_gatekeeper_open
- soft_gatekeeper_close

Classification:
- CONFIRMED_HAL_RUNNING
- SOFTWARE_BACKED_SECURITY_PATH
- NOT_CONFIRMED_TEE_BACKED

Canonical statement:
The C17S has a running Android Gatekeeper 1.0 HAL and gatekeeperd service, but
the implementation path is confirmed software Gatekeeper. It must not be written
as hardware Gatekeeper or TEE Gatekeeper.
################################################################################

################################################################################
# 11.8 — KEYSTORE / VOLD / FBE / LOCK SETTINGS TRUST PATH
################################################################################

11.8.1 — Binder services

Security-relevant binder services present:
- android.security.keystore        : android.security.keystore.IKeystoreService
- lock_settings                    : com.android.internal.widget.ILockSettings
- trust                            : android.app.trust.ITrustManager
- vold                             : android.os.IVold
- mount                            : android.os.storage.IStorageManager
- oem_lock                         : android.service.oemlock.IOemLockService
- persistent_data_block            : android.service.persistentdata.IPersistentDataBlockService
- sec_key_att_app_id_provider      : android.security.keymaster.IKeyAttestationApplicationIdProvider

Classification:
- CONFIRMED_FRAMEWORK_SERVICE
- HARDWARE_SECURITY_ADJACENT_FRAMEWORK_PATH

11.8.2 — FBE runtime properties

Properties:
- ro.crypto.state=encrypted
- ro.crypto.type=file
- ro.crypto.volume.filenames_mode=aes-256-cts
- vold.has_adoptable=1
- vold.has_quota=1
- vold.has_reserved=1
- vold.post_fs_data_done=1

Mounted filesystems:
- /dev/block/mmcblk0p36 on /data type ext4
- /dev/block/mmcblk0p10 on /metadata type ext4
- /dev/block/dm-1 on / type ext4
- /dev/block/dm-2 on /vendor type ext4
- /dev/block/dm-0 on /product type ext4
- /dev/block/dm-3 on /apex/com.android.tzdata

Fstab lines:
- /dev/block/platform/bootdevice/by-name/md_udc /metadata ext4 ... first_stage_mount
- /dev/block/platform/bootdevice/by-name/userdata /data ext4 ... quota,reservedsize=128M,formattable,resize,checkpoint=block,fileencryption=aes-256-xts
- system /system ext4 ro wait,avb=vbmeta_system,logical,first_stage_mount,avb_keys=/avb/q-gsi.avbpubkey:/avb/r-gsi.avbpubkey:/avb/s-gsi.avbpubkey
- vendor /vendor ext4 ro wait,avb,logical,first_stage_mount
- product /product ext4 ro wait,avb,logical,first_stage_mount

Classification:
- FBE_ACTIVE
- MD_UDC_METADATA_PATH_CONFIRMED
- USERDATA_FBE_V1_STYLE_PATH
- AVB_DECLARED_IN_FSTAB_BUT_CURRENTLY_NON_ENFORCING_BY_CAPTURE_STATE

11.8.3 — Lock and persistent-data partitions

FRP:
- ro.frp.pst=/dev/block/platform/bootdevice/by-name/frp
- /dev/block/by-name/frp -> /dev/block/mmcblk0p7
- size 1,048,576 bytes

OEM lock:
- binder service oem_lock present
- ro.oem_unlock_supported=1
- sys.oem_unlock_allowed=1 in current capture
- ro.boot.flash.locked=0 in current capture

Persistent data block:
- binder service persistent_data_block present

Classification:
- FRP_BOUNDARY_PRESENT
- OEM_LOCK_FRAMEWORK_BOUNDARY_PRESENT
- CURRENT_RESEARCH_CAPTURE_UNLOCKED
################################################################################

################################################################################
# 11.9 — DRM / WIDEVINE / CAS / CLEARKEY SECURITY BOUNDARY
################################################################################

11.9.1 — Runtime properties

Properties:
- drm.service.enabled=true
- init.svc.drm=running
- init.svc.mediadrm=running
- init.svc.vendor.cas-hal-1-1=running
- init.svc.vendor.drm-clearkey-hal-1-2=running
- init.svc.vendor.drm-hal-1-0=running
- init.svc.vendor.drm-widevine-hal-1-2=running
- ro.vendor.mtk_widevine_drm_l3_support=1
- ro.build.version.security_patch=2024-03-05
- ro.vendor.build.security_patch=2020-04-05

Current Widevine classification:
- CURRENT_CAPTURE_WIDEVINE_L3
- STOCK_WIDEVINE_LEVEL_UNCONFIRMED_BY_CAPTURE

Important note:
The current L3 value must not be written as a locked-stock fact. Bootloader
unlock, orange state, and research modifications may change Widevine trust
level. If a preserved locked-stock capture proves L1, this section must be
updated to record stock L1 and current research L3 as separate states.

11.9.2 — DRM/CAS/Widevine processes

Processes:
- drmserver
  - domain : u:r:drmserver:s0
  - user   : drm

- mediadrmserver
  - domain : u:r:mediadrmserver:s0
  - user   : media

- android.hardware.cas@1.1-service
  - domain : u:r:hal_cas_default:s0
  - user   : media

- android.hardware.drm@1.0-service
  - domain : u:r:hal_drm_default:s0
  - user   : media

- android.hardware.drm@1.2-service.clearkey
  - domain : u:r:hal_drm_clearkey:s0
  - user   : media

- android.hardware.drm@1.2-service.widevine
  - domain : u:r:hal_drm_widevine:s0
  - user   : media

11.9.3 — DRM/CAS/Widevine HAL binaries

Widevine:
- /vendor/bin/hw/android.hardware.drm@1.2-service.widevine
- label  : u:object_r:hal_drm_widevine_exec:s0
- size   : 24,604 bytes
- SHA256 : 36aa79155c6f737d12d1380ce3c89fbbedc5efca61fc03c9ac128c3d391f9abd
- strings include: WidevineHidlService, registerAsService("widevine"), vendor/widevine/libwvdrmengine/src_hidl/service.cpp

Clearkey:
- /vendor/bin/hw/android.hardware.drm@1.2-service.clearkey
- label  : u:object_r:hal_drm_clearkey_exec:s0
- size   : 152,336 bytes
- SHA256 : b8aca481ed3ab1d6723fb20ffeff9ddaffdd30ef8a974cecd012119d2826ade3

Default DRM:
- /vendor/bin/hw/android.hardware.drm@1.0-service
- label  : u:object_r:hal_drm_default_exec:s0
- size   : 11,120 bytes
- SHA256 : ae17cace2c9482931740abe88db93d1217fb456c244c8f1c1e955e2b643f2b8b

CAS:
- /vendor/bin/hw/android.hardware.cas@1.1-service
- label  : u:object_r:hal_cas_default_exec:s0
- size   : 39,696 bytes
- SHA256 : 2a71aa05330efe843583c4f1069680356b5516eb0b93ab923e480e65a746cdc9

Classification:
- DRM_STACK_PRESENT
- WIDEVINE_HAL_RUNNING
- CAS_HAL_RUNNING
- CURRENT_CAPTURE_WIDEVINE_L3
- STOCK_WIDEVINE_LEVEL_OPEN
- DO_NOT_EXTRACT_DRM_SECRET
################################################################################

################################################################################
# 11.10 — SECURE ELEMENT / SIM1 / UICC TRUST BOUNDARY
################################################################################

HAL declaration:
- android.hardware.secure_element@1.0::ISecureElement/SIM1

Init service:
- init.svc.mtk_secure_element_hal_service=running

Process:
- android.hardware.secure_element@1.0-service-mediatek
  - user   : secure_element
  - domain : u:r:mtk_hal_secure_element:s0

Framework app/process:
- com.android.se
  - user   : secure_element
  - domain : u:r:secure_element:s0:c44,c260,c512,c768

SIM/UICC properties observed:
- ro.telephony.sim.count=1
- persist.vendor.radio.msimmode=ss
- ro.vendor.radio.max.multisim=ss
- vendor.gsm.ril.uicctype=USIM
- vendor.gsm.ril.fulluicctype=USIM
- gsm.sim.state=LOADED

Public redaction required:
- ICCID
- IMSI status values if device-specific
- operator numeric/private values
- SIM retry counters if publishing outside private database
- subscriber/operator identifiers

NFC boundary:
- pm feature list does not declare android.hardware.nfc
- Secure Element is therefore recorded as SIM1/UICC boundary, not NFC/eSE hardware proof.

Classification:
- CONFIRMED_HAL_RUNNING
- SECURE_ELEMENT_SIM1_BOUNDARY
- UICC_SECURITY_BOUNDARY
- NFC_NOT_PROVEN
- PUBLIC_REDACT
################################################################################

################################################################################
# 11.11 — TRUSTED CODE ADMISSION SURFACE / UID1000 HARDWARE TRUST CROSSOVER
################################################################################

This subsection is intentionally limited. It does not analyze APK behavior.
It records the signed-stock privileged-code admission surface because those
packages run as Android system UID and can therefore consume hardware-facing
services, sysfs nodes, binder services, sensors, radio, storage, and security
framework APIs inside the stock trust envelope.

11.11.1 — Package-manager UID1000 evidence

System/product/vendor packages observed with uid:1000 include:

Product app packages:
- /product/app/WearAgingTest/WearAgingTest.apk = com.wiite.agingtest
- /product/app/WearAppDialStore/WearAppDialStore.apk = com.wiite.wiitestore
- /product/app/WearAppFreeze/WearAppFreeze.apk = com.wiite.freezeapp
- /product/app/WearBreath/WearBreath.apk = com.wiite.breathtraining
- /product/app/WearCalendar/WearCalendar.apk = com.wiite.mycalender
- /product/app/WearCamera/WearCamera.apk = com.wiite.camera
- /product/app/WearCleanTaskPro/WearCleanTaskPro.apk = com.wiite.cleantask
- /product/app/WearClock/WearClock.apk = com.android.deskclock
- /product/app/WearConfig/WearConfig.apk = com.wiite.dev
- /product/app/WearDevicemsg/WearDevicemsg.apk = com.wiite.devicemsg
- /product/app/WearDialerContacts/WearDialerContacts.apk = com.wiite.dbf.androidp_contacts
- /product/app/WearFileManager/WearFileManager.apk = com.wiite.filemanager
- /product/app/WearFitnessUart/WearFitnessUart.apk = com.sprots.wiiteer.fitness
- /product/app/WearGallerySquare/WearGallerySquare.apk = com.android.gallery3d
- /product/app/WearHealthStepUart/WearHealthStepUart.apk = com.wiite.wearhealthuart
- /product/app/WearHeartRateUart/WearHeartRateUart.apk = com.wiite.wiite_heartrate
- /product/app/WearHomeSettings/WearHomeSettings.apk = com.wiite.dbf.wiitedesktopsettings
- /product/app/WearHomeSportDM101/WearHomeSportDM101.apk = com.wiite.home
- /product/app/WearInChargingUi/WearInChargingUi.apk = com.wiite.power
- /product/app/WearLanguageSettings/WearLanguageSettings.apk = com.wiite.wearlanguagesettings
- /product/app/WearMms/WearMms.apk = com.wiite.dbf.wiitemms
- /product/app/WearMusic/WearMusic.apk = com.android.music
- /product/app/WearRecorder/WearRecorder.apk = com.wiite.wear.dy.recorder
- /product/app/WearReliableTest/WearReliableTest.apk = com.wiite.reliabletest
- /product/app/WearScreenRecord/WearScreenRecord.apk = com.wiite.screenrecorder
- /product/app/WearSdkUart/WearSdkUart.apk = com.wiite.wearuart
- /product/app/WearSleepUart/WearSleepUart.apk = com.wiite.wiite_sleep
- /product/app/WearSpoUart/WearSpoUart.apk = com.wiite.wiite_bloodoxygen
- /product/app/WearSyncContact/WearSyncContact.apk = com.wiite.bluetoothvcard
- /product/app/WearThirdPartAppAdapter/WearThirdPartAppAdapter.apk = com.wiite.wearthirdappadater
- /product/app/WearWeather/WearWeather.apk = com.android.watchweather

Product priv-app packages:
- /product/priv-app/Omacp/Omacp.apk = com.mediatek.omacp
- /product/priv-app/WearCommon/WearCommon.apk = com.wiite.WiiteCommon
- /product/priv-app/WearDeviceDeamPix/WearDeviceDeamPix.apk = com.wiite.devicedaemon
- /product/priv-app/WearOOBE/WearOOBE.apk = com.ss.oobe
- /product/priv-app/WearSettings/WearSettings.apk = com.android.settings
- /product/priv-app/WearSystemMode/WearSystemMode.apk = com.wiite.systemmode

System packages:
- /system/app/BatteryWarning/BatteryWarning.apk = com.mediatek.batterywarning
- /system/app/KeyChain/KeyChain.apk = com.android.keychain
- /system/app/WearFactoryMode/WearFactoryMode.apk = com.signal.main
- /system/app/WearScreenLock/WearScreenLock.apk = fdy.wiitter.com
- /system/framework/framework-res.apk = android
- /system/framework/mediatek-res.apk = com.mediatek
- /system/priv-app/DynamicSystemInstallationService/DynamicSystemInstallationService.apk = com.android.dynsystem
- /system/priv-app/FusedLocation/FusedLocation.apk = com.android.location.fused
- /system/priv-app/InputDevices/InputDevices.apk = com.android.inputdevices
- /system/priv-app/LPPeService/LPPeService.apk = com.mediatek.location.lppe.main
- /system/priv-app/WapiCertManager/WapiCertManager.apk = com.wapi.wapicertmanager
- /system/priv-app/WearSettingsProvider/WearSettingsProvider.apk = com.android.providers.settings
- /system/priv-app/WearTelecom/WearTelecom.apk = com.android.server.telecom

Vendor package:
- /vendor/operator/app/Stopwatch/Stopwatch.apk = com.wiite.wiite_stopwatch

11.11.2 — Live system UID hardware-facing processes

Hardware/security-relevant system UID processes observed live include:
- android.hardware.keymaster@4.0-service
- android.hardware.gatekeeper@1.0-service
- android.hardware.gnss@2.0-service-mediatek
- android.hardware.health@2.0-service
- android.hardware.light@2.0-service-mediatek
- android.hardware.sensors@2.0-service-mediatek
- android.hardware.thermal@1.0-service
- android.hardware.usb@1.1-service-mediatek
- android.hardware.vibrator@1.0-service
- wmt_launcher
- ccci_mdinit
- netdagent
- volte_imcb
- system_server
- com.wiite.WiiteCommon
- com.wiite.devicedaemon
- com.wiite.devicedaemon:communication
- com.wiite.wearhealthuart
- com.wiite.wiite_heartrate
- com.wiite.wiite_sleep
- com.wiite.wiite_bloodoxygen
- com.wiite.wiitestore
- com.wiite.dbf.androidp_contacts
- com.wiite.power
- com.android.settings

Classification:
- TRUSTED_CODE_ADMISSION_SURFACE
- HARDWARE_TRUST_CROSSOVER
- NOT_A_HARDWARE_COMPONENT
- CROSS_REFERENCE_TO_SOFTWARE_SECURITY_DOCS

Canonical statement:
The hardware security substrate must be evaluated both as a boot-chain state and
as a privileged-code admission model. Stock locked/enforcing status can still
admit ODM UID1000 packages because those packages are part of the signed stock
image. Section 11 records this as a trust-boundary fact only; APK behavior and
network conclusions remain outside this hardware section.
################################################################################

################################################################################
# 11.12 — SECURITY FEATURE DECLARATION REGISTER
################################################################################

Security-relevant Android features declared:
- android.software.device_admin
- android.software.managed_users
- android.software.secure_lock_screen
- android.software.securely_removes_users
- android.software.verified_boot

Hardware feature declarations relevant to adjacent trust surfaces:
- android.hardware.telephony
- android.hardware.telephony.gsm
- android.hardware.telephony.ims
- android.hardware.bluetooth
- android.hardware.bluetooth_le
- android.hardware.location.gps
- android.hardware.usb.accessory
- android.hardware.usb.host
- android.hardware.wifi

Security features not declared in capture:
- android.hardware.nfc not present in feature capture
- StrongBox not declared in feature/HAL capture
- Weaver not declared in HIDL capture
- AuthSecret not declared in HIDL capture
- ConfirmationUI not declared in HIDL capture

Classification:
- VERIFIED_BOOT_FEATURE_DECLARED
- SECURE_LOCK_SCREEN_DECLARED
- NO_STRONGBOX_CAPTURED
- NO_WEAVER_CAPTURED
- NO_AUTHSECRET_CAPTURED
- NO_CONFIRMATIONUI_CAPTURED
- NO_NFC_FEATURE_CAPTURED
################################################################################

################################################################################
# 11.13 — PUBLIC REDACTION AND NON-EXTRACTION POLICY
################################################################################

Values to redact from public Section 11:
- androidboot.serialno
- SIM ICCID
- IMSI or IMSI-status values if device-specific
- SIM operator private identifiers and subscriber values
- DRM/Widevine provisioning identifiers
- RPMB contents, replay counters, and authentication material
- DXCC-derived secrets or hardware unique key material
- FRP contents
- OTP contents
- NVRAM/NVDATA/NVCFG/protect partition private payloads
- Gatekeeper credential handles
- Keystore blobs and user credential material
- TEE/Mobicore private logs or key outputs

Allowed public facts:
- Partition names, sizes, block targets, and non-secret SHA256 hashes of public
  image partitions already included in the forensic set
- Device-node major/minor values
- DT node names, compatible strings, MMIO base/size, and IRQ cell presence
- HAL service names, SELinux domains, process names, binary paths, file sizes,
  and binary hashes
- Current capture-state properties once clearly labeled as research state
- Stock state only when labeled owner-attested or backed by preserved stock logs
################################################################################

################################################################################
# 11.14 — CLEAN AOSP 11 BRING-UP IMPLICATIONS
################################################################################

Required preservation or replacement facts:
- Preserve md_udc as active /metadata. Do not switch to p11 metadata without a
  deliberate migration plan.
- Preserve userdata p36 size: 119,706,467,840 bytes.
- Preserve FBE fileencryption=aes-256-xts and filename mode handling.
- Preserve RPMB device boundaries but do not attempt to initialize, erase, or
  write RPMB in bring-up experiments.
- Treat /dev/sec as security-adjacent; do not grant broad write access in clean
  SELinux policy.
- Treat DXCC, EFUSEC, SECURITY_AO, TRNG, and Mobicore DT nodes as hardware
  facts that should remain represented in the device tree unless deliberately
  proven unused by clean kernel bring-up.
- Do not claim hardware Keymaster/Gatekeeper unless a future implementation
  actually routes to TEE/hardware and proves that path.
- For immediate clean ROM, software/passthrough Keymaster and software
  Gatekeeper may be sufficient for FBE bring-up, matching the observed stock
  userspace behavior.
- If Widevine stock L1 is later proven, keep L1 as stock-only and L3 as current
  unlocked capture unless re-lock/provisioning tests prove otherwise.
- Secure Element SIM1 should be kept only if SIM/UICC stack is retained. Do not
  add NFC/eSE claims.
- Remove or replace ODM UID1000 packages as software policy, but keep hardware
  paths they own only when needed: nRF/PAR health UART, Wiite bridge, sensors,
  telephony, radio, power, charging, and input paths are owned by Sections 7,
  9, 10, and 6 respectively.

SELinux bring-up implication:
- Stock enforcing is owner-attested, but current capture is permissive. New
  policy must be rebuilt from desired clean hardware ownership, not copied from
  the permissive research state.

AVB implication:
- Current vbmeta flags and orange state are research state. Clean ROM can keep
  AVB disabled for development, but public database must separate that from
  stock locked state.
################################################################################

################################################################################
# 11.15 — OPEN LEADS / DO NOT OVERCLAIM REGISTER
################################################################################

OPEN_LEAD_11_A — Locked-stock Widevine level
- Current capture proves L3.
- User notes stock may have been L1 and may have downgraded after unlock.
- Required closure: preserved locked-stock getprop/dumpsys media.drm/Widevine
  diagnostic, or re-lock/provisioning test if safe and reversible.
- Current database status: STOCK_WIDEVINE_LEVEL_UNCONFIRMED_BY_CAPTURE.

OPEN_LEAD_11_B — TEE userspace node naming
- DT declares Trustonic/Mobicore and TEE reserved memory.
- Capture did not find /dev/tee*, /dev/tz*, /dev/mtee*, /dev/trust*, or similar
  userspace nodes.
- Required closure: kernel config/source, init rc, vendor library strace, or
  full /dev and sysfs trustonic-specific scan.

OPEN_LEAD_11_C — DXCC consumer path
- DXCC block exists at 0x10210000.
- Android Keymaster path appears pure-soft/passthrough.
- Required closure: kernel source, vendor libraries, TEE driver proof, or
  controlled non-secret tracing to identify DXCC consumers.
- Do not attempt to extract derived secrets.

OPEN_LEAD_11_D — /dev/sec ioctl semantics
- /dev/sec exists and sec module parameters are visible.
- Required closure: kernel source or symbol mapping.
- Do not brute-force ioctl calls on production device.

OPEN_LEAD_11_E — Stock locked/enforcing evidence archive
- User attests stock was locked/enforcing.
- If old stock logs exist, add them as preserved stock-state evidence.
- Current live capture remains owner-modified research state.

CLOSED_11_A — RPMB physical boundary
- /dev/rpmb0, /dev/block/mmcblk0rpmb, sysfs class, major/minor, and 16 MiB size
  are closed.

CLOSED_11_B — Gatekeeper backend
- Software Gatekeeper path is closed by symlink and strings.

CLOSED_11_C — Keymaster userspace backend
- Pure-soft/passthrough Keymaster path is closed for captured userspace by
  library linkage and strings.

CLOSED_11_D — Active metadata path
- md_udc p10 is active /metadata; p11 metadata is inactive/ghost in capture.

CLOSED_11_E — Secure Element boundary
- SIM1/UICC Secure Element is present; NFC is not promoted.
################################################################################

################################################################################
# 11.16 — FINAL SECTION VERDICT
################################################################################

The C17S contains a real MediaTek hardware security substrate: DXCC, EFUSEC,
SECURITY_AO, TRNG, RPMB, /dev/sec, TEE/ATF partitions, Trustonic/Mobicore DT
binding, TEE reserved memory, AVB/vbmeta partitions, FRP/OTP/provisioning
partitions, FBE metadata, Secure Element SIM1, and a full Android security
service stack.

The captured Android userspace does not expose a hardware-backed Keymaster or
hardware-backed Gatekeeper path. Keymaster 4.0 is running but resolves through
pure-soft/passthrough components. Gatekeeper 1.0 is running but resolves through
SoftGatekeeper. Widevine is present and running, but the current research
capture reports L3; locked-stock level remains unconfirmed because unlock may
change DRM trust level.

The current live state is an owner-created research state: Magisk root,
SELinux permissive, bootloader unlocked, orange verified boot, and non-enforcing
AVB/verity behavior. These must not be described as factory-stock facts. The
stock device is owner-attested as locked and enforcing before unlock.

The critical security architecture truth is that boot-chain enforcement and
privileged-code admission are separate. Stock locked/enforcing state can still
admit ODM UID1000 product/vendor/system packages because those packages are part
of the signed stock image. For the hardware database, this is recorded only as a
trusted-code admission surface crossing into hardware ownership; APK behavior
belongs to software security documents.

Section 11 is closed for the current evidence set with explicit stock-versus-
research boundaries and no secret extraction.
################################################################################
