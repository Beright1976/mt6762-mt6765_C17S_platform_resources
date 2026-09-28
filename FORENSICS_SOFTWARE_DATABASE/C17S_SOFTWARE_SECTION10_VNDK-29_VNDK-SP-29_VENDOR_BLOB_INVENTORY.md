################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 10 — VNDK-29, VNDK-SP-29 & VENDOR BLOB INVENTORY
#
# Device      : LOKMAT 5 MAX / C17S
# Board       : WP_C17S_PIX_TFT_D4
# Platform    : MT6762/MT6765-class MediaTek Android 10 vendor stack
# Author      : beright1976
# Draft Role  : FULL-SCOPE SECTION 10 DATABASE DRAFT — NOT CLOSED
# Status      : PARTIALLY POPULATED — requires complete inventory, dependency map, and Ghidra/dlopen closure
# Date        : 2026-06-19 evidence consolidation draft
################################################################################

################################################################################
# SECTION 10 CANONICAL SCOPE — CONTROLLING SCOPE
################################################################################

Section 10 — VNDK-29, VNDK-SP-29 & Vendor Blob Inventory

Canonical scope:
  - Complete VNDK-29 library list: 64-bit and 32-bit sets
  - Complete VNDK-SP-29 library list: 39 files + hw/ subdirectory
  - ODM-modified VNDK libraries identified (different symbol tables)
  - Vendor blob classification: required / optional / ghost
  - Blob dependency map — all 1,468 vendor files
  - dlopen call targets identified via Ghidra — status per blob
  - patchelf rpath correction targets
  - Library namespace declarations in /vendor/etc/ld.config.*.txt

Current section status:
  [PARTIALLY_POPULATED]
  [NOT_CLOSED]
  [FULL_SCOPE_RESTORED]
  [VNDK_INVENTORY_PENDING_FULL_LIST]
  [VNDK_SP_INVENTORY_PENDING_FULL_LIST]
  [VENDOR_BLOB_DEPENDENCY_MAP_PENDING_1468]
  [GHIDRA_DLOPEN_STATUS_PENDING]
  [PATCHELF_RPATH_STATUS_PENDING]
  [LINKER_NAMESPACE_MODEL_PARTIALLY_PROVEN]
  [CRYPTO_SECURITY_CHAIN_PARTIALLY_PROVEN]
  [RUNTIME_PROVIDER_MAPS_PARTIALLY_PROVEN]
  [B1E4_VNDK_29_COMPLETE_LIST_CLOSED_IN_DATABASE_FORM]
  [B1E4_VNDK_SP_29_COMPLETE_LIST_CLOSED_IN_DATABASE_FORM]
  [B1E4_VNDK_LISTS_SELF_CONTAINED_IN_SECTION10]

Scope doctrine:
  This section is a database section first. It is not a ROM build guide and it is
  not a threat-report narrative. ROM work, AOSP compatibility, and security
  assessment are consumers of this section; they do not redefine this section.

  Crypto resolution under AOSP is one required use-case of this database, but it
  is not the whole section. The whole section is the VNDK/VNDK-SP/vendor-blob
  inventory and dependency authority for the device.

  Nothing is removed from scope. New evidence is added, classified, and linked
  back to the canonical scope.

################################################################################
# 10.0 — SOURCE AUTHORITY, WRITE POLICY, AND DATABASE IDENTITY RULES
################################################################################

10.0.1 Source authority

  Primary source authorities consumed by this draft:
    - C17S_SOFTWARE_SECTIONS_DIRECTORY.md
    - C17S_SOFTWARE_DATABASE_EXPANSION_WORKFLOW.md
    - C17S_SOFTWARE_Forensics.md
    - C17S_ROM_BUILD_DOCUMENT.md
    - C17S_LOKMAT5MAX_HARDWARE_DATABASE.md
    - Completed Software Sections 00 through 09
    - Live rooted C17S adb/su captures provided by the user
    - Static stock super_unpacked tree on the user's host, read-only reference only
    - Article/repo-level ODM-security findings only where they inform Section 10
      classification or known control-plane cross-references

  Evidence authority order:
    1. Live rooted device runtime evidence
    2. Static stock partition extraction evidence
    3. Completed canonical section evidence
    4. ROM build document as downstream-consumer/supporting evidence
    5. Threat/reverse-engineering article/repo as cross-reference evidence only

10.0.2 Host artifact and research-drive write policy

  [RESEARCH_DRIVE_READ_ONLY_REFERENCE]
  The research drive is a source authority only. The assistant must not direct
  generated output files, temporary files, command-output artifacts, patched
  binaries, extracted APKs, jadx outputs, Ghidra exports, or generated section
  drafts into the research drive.

  [DOWNLOADS_ONLY_GENERATED_OUTPUT]
  Host-side generated outputs must be written only under ~/Downloads, preferably
  an explicit work directory such as:

    ~/Downloads/C17S_SECTION10_WORK/

  [USER_CONTROLS_RESEARCH_TREE]
  The user is the only actor who changes files inside the research directory.

10.0.3 File identity rule

  [PATH_NAMESPACE_ABI_HASH_IDENTITY_RULE]
  No VNDK, VNDK-SP, vendor, system, or product library is identified by basename
  alone. No file is identified by size alone. No file is identified by SONAME
  alone. No host extraction path is assumed to be the runtime provider.

  Required identity tuple for any library/blob in Section 10:

    path
    + partition/source layer
    + linker namespace
    + ABI class
    + file size
    + SHA256
    + SONAME when ELF
    + DT_NEEDED when ELF
    + RPATH/RUNPATH when ELF
    + Build ID when ELF note exists
    + runtime mapped provider when active
    + owner process/service when active
    + persistent state path when state-consuming
    + classification: required / optional / ghost / cross-reference / pending

10.0.4 Classification vocabulary

  [REQUIRED]
    Required for boot, HAL registration, framework service operation, crypto,
    security, storage, radio, connectivity, media, graphics, or another proven
    active runtime chain.

  [OPTIONAL]
    Feature-dependent, test-only, debug-only, product-feature-only, or conditionally
    activated. Optional does not mean safe to delete; it means not yet proven
    universally required.

  [GHOST]
    Present because of BSP carryover or unused declaration, disproven hardware,
    stale feature, unused config, or non-runtime path. Ghost entries remain in the
    database and are not silently removed.

  [CROSS_REFERENCE]
    Belongs primarily to another section but must be referenced here because it
    affects blob loading, namespace behavior, dependency closure, runtime security,
    or vendor-blob classification.

  [PENDING]
    Known in scope, but not yet closed.

################################################################################
# 10.1 — PLATFORM, PARTITION, AND RUNTIME LAYER MODEL
################################################################################

10.1.1 Device identity anchors

  [DEVICE_IDENTITY_ANCHOR]
  Device class:
    LOKMAT 5 MAX / C17S rectangular watch-class Android device with full phone
    stack and product/priv-app ODM layer.

  [BOARD_IDENTITY_ANCHOR]
  Board:
    WP_C17S_PIX_TFT_D4

  [SOC_IDENTITY_BOUNDARY]
  Hardware/software identity split:
    - getprop surfaces mt6762 / mt6765-family identifiers.
    - ro.board.platform / vendor platform surfaces MT6765-class BSP identity.
    - Device is treated as MT6762/MT6765-class MediaTek Android 10 vendor stack.

  [ANDROID_VENDOR_API_BOUNDARY]
  Vendor stack:
    Android 10 / API 29 vendor/VNDK design.

10.1.2 Static stock extraction layers

  [STATIC_SUPER_LAYER]
  Static extracted stock root contains:
    - system.img extraction
    - vendor.img extraction
    - product.img extraction

  Important host extraction path correction:
    Android /system content resides under host system/system/.

  Observed crypto/security layer families:
    - system/system/lib64 and system/system/lib
    - system/system/lib64/vndk-29 and system/system/lib/vndk-29
    - system/system/lib64/vndk-sp-29 and system/system/lib/vndk-sp-29
    - system/system/vendor/lib64 and system/system/vendor/lib
    - vendor/lib64 and vendor/lib
    - product app/priv-app layers

10.1.3 Live runtime layers not represented by super

  [LIVE_RUNTIME_LAYER_REQUIRED]
  The following live layers are not fully represented by static super extraction:
    - /data/vendor
    - /data/misc
    - /metadata
    - /mnt/vendor/nvdata
    - /mnt/vendor/nvcfg
    - /mnt/vendor/protect_f
    - /mnt/vendor/protect_s
    - /apex tmpfs/runtime view
    - procfs, sysfs, debugfs
    - sockets and binder/hwbinder registrations
    - process memory maps
    - generated runtime config/state

  [RUNTIME_MOUNT_AUTHORITY_CAPTURED]
  Live mount/path observations include:
    - /vendor mounted from dm-2
    - /product mounted from dm-0
    - /metadata mounted from mmcblk0p10
    - /data mounted from mmcblk0p36
    - /mnt/vendor/protect_f mounted from mmcblk0p12
    - /mnt/vendor/protect_s mounted from mmcblk0p13
    - /mnt/vendor/nvdata mounted from mmcblk0p9
    - /mnt/vendor/nvcfg mounted from mmcblk0p8
    - /system/vendor resolves to /vendor at runtime
    - /odm exists as vendor-labeled directory but no active ODM partition was proven

################################################################################
# 10.2 — LINKER NAMESPACE DECLARATIONS AND RESOLUTION MODEL
################################################################################

10.2.1 ld.config ownership

  [LINKER_NAMESPACE_DECLARATION_SCOPE]
  Section 10 owns library namespace declarations in:
    - /vendor/etc/ld.config.*.txt
    - system linker configuration copies that affect vendor/framework resolution
    - any extracted static equivalents under system/system/etc or vendor/etc

  Required final state:
    Every ld.config.* file must be listed with:
      path
      size
      SHA256
      namespace sections
      search paths
      permitted paths
      namespace links
      shared_libs link allowlists
      allow_all_shared_libs declarations
      double-load risk notes

10.2.2 Captured ld.config.29 namespace model

  [STOCK_LINKER_NAMESPACE_AUTHORITY_PARTIAL]
  Captured namespace model includes:

    [system]
      default namespace isolated/visible
      search paths include /system/${LIB}, /product/${LIB}
      additional namespaces include runtime, conscrypt, media, resolv, sphal,
      vndk, and rs.

    [system] sphal namespace
      searches /odm/${LIB}, /vendor/${LIB}, /vendor/${LIB}/hw
      permits /odm/${LIB}, /vendor/${LIB}, /system/vendor/${LIB}
      links to rs, default, and vndk
      includes VNDK-SP support libraries in shared_libs link set.

    [system] vndk namespace
      used for VNDK-SP libraries
      searches /odm/${LIB}/vndk-sp, /vendor/${LIB}/vndk-sp,
      /system/${LIB}/vndk-sp-29
      links default and sphal
      namespace.vndk.link.sphal.allow_all_shared_libs = true

    [vendor]
      additional namespaces include runtime, system, vndk
      default namespace searches /odm/${LIB}, /vendor/${LIB}
      permitted paths include /odm, /vendor, /system/vendor
      links to system and vndk
      default-to-vndk link includes VNDK libraries required by vendor processes.

    [vendor] vndk namespace
      searches /odm/${LIB}/vndk, /odm/${LIB}/vndk-sp,
      /vendor/${LIB}/vndk, /vendor/${LIB}/vndk-sp,
      /system/${LIB}/vndk-sp-29, /system/${LIB}/vndk-29
      links system and default
      namespace.vndk.link.default.allow_all_shared_libs = true

  [VENDOR_DEFAULT_LOCAL_FIRST]
  Vendor processes resolve local vendor paths before linked VNDK/system providers
  where namespace rules allow it.

  [DOUBLE_LOAD_RISK_DOCUMENTED]
  Same basename may map separately in system and vendor/VNDK namespaces. Global
  state must not be assumed shared across namespace-selected copies.

10.2.3 Namespace consequence for Section 10

  [GENERIC_DT_NEEDED_NOT_ENOUGH]
  A DT_NEEDED entry such as libcrypto.so does not identify the provider. It must
  be resolved through:
    - process class
    - ld.config section
    - namespace search path
    - linked namespace allowlist
    - live /proc/<pid>/maps proof

################################################################################
# 10.3 — COMPLETE VNDK-29 INVENTORY STATUS
################################################################################

10.3.1 Canonical requirement

  [VNDK_29_COMPLETE_LIST_REQUIRED]
  Final Section 10 must contain the complete VNDK-29 library list for:
    - 64-bit set: /system/lib64/vndk-29/*.so
    - 32-bit set: /system/lib/vndk-29/*.so

  Required per-file fields:
    - path
    - ABI
    - size
    - SHA256
    - SONAME
    - DT_NEEDED list
    - RPATH/RUNPATH
    - Build ID
    - symbol-table divergence status against same-name system/vendor copies
    - required/optional/ghost/cross-reference classification
    - owning runtime process when mapped

10.3.2 Current VNDK-29 evidence already captured

  [VNDK_29_CRYPTO_SECURITY_SUBSET_CAPTURED]
  Known VNDK-29 crypto/security and service-provider subset includes:

    /system/lib64/vndk-29/libcrypto.so
      bytes=1149976
      sha256=91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

    /system/lib64/vndk-29/libgatekeeper.so
      bytes=28072
      sha256=30179ec57751d3ddeeb1fbe3d1bb63e4c896d154b32eef7635f7b758ef51ac76

    /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
      bytes=45944
      sha256=0f03768bf69e364c78f0bb93562cea5562540effe04e8038e224970ce5633097

    /system/lib/vndk-29/libpuresoftkeymasterdevice.so
      bytes=37312
      sha256=7b0aca84a7fb34c5f3ac80a1f7739aae4cde5419074b702455042213b6594479

    /system/lib64/vndk-29/libkeymaster_messages.so
      bytes=84024
      sha256=b2b26e32b164dfeb6ade1e3a228a1822229d3763c90d0945ca2419284fbcca23

    /system/lib64/vndk-29/libkeymaster_portable.so
      bytes=248912
      sha256=fbbb7c913eba10708b88e96f44e8535a8e49241dcecdfa1350bde89fc2a48dc3

    /system/lib64/vndk-29/android.hardware.keymaster@3.0.so
      bytes=140880
      sha256=5021a6d916dad6d8b437f3b2d95303d2aeb9e192657efdaa71eabb165ea9de4e

    /system/lib64/vndk-29/android.hardware.keymaster@4.0.so
      bytes=158504
      sha256=23269167ccbc0a57ccfd919876a93a53b4a2e3e0f9edbc44899aab2bb8787000

    /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so
      bytes=92856
      sha256=f97e190288ec175fc21c735920ce5359a9dbadda6660a271c26290287e29fef5

  Additional VNDK-29 families already observed in live maps:
    - android.hardware.drm@1.0.so
    - android.hardware.drm@1.1.so
    - android.hardware.drm@1.2.so
    - android.hardware.cas@1.0.so
    - android.hardware.cas@1.1.so
    - android.hardware.keymaster@3.0.so
    - android.hardware.keymaster@4.0.so
    - android.hardware.gatekeeper@1.0.so
    - libcrypto.so
    - libgatekeeper.so
    - libkeymaster_messages.so
    - libkeymaster_portable.so
    - libpuresoftkeymasterdevice.so

10.3.3 Current VNDK-29 closure status

  [VNDK_29_FULL_LIST_PENDING]
  Complete 64-bit and 32-bit VNDK-29 library lists are not yet embedded in this
  draft. This is a blocking open item for final Section 10 closure.

  [VNDK_29_SYMBOL_DELTA_PENDING]
  ODM-modified VNDK libraries require symbol-table comparison against the same
  basename in normal system/vendor/other namespace copies and/or baseline AOSP/MTK
  references. This remains pending except where same-name stock copies were
  already proven divergent by SHA256/size.


################################################################################
# 10.3.4 — B1E4 COMPLETE VNDK-29 INVENTORY — SELF-CONTAINED DATABASE TABLES
################################################################################

10.3.4.1 Closure authority and evidence boundary

  [B1E4_VNDK_29_COMPLETE_LIST_EMBEDDED]
  [B1E4_VNDK_29_DATABASE_FORM_CLOSED]
  [CANONICAL_VNDK_29_LIST_SELF_CONTAINED]

  This B1E4 insertion closes the complete VNDK-29 list requirement for the
  C17S captured software image. The complete 64-bit and 32-bit VNDK-29 sets are
  embedded directly in this Section 10 database document. No sidecar file,
  external report, build note, or generated artifact is required to recover the
  VNDK-29 inventory.

  Evidence source used to render the embedded rows:
    SECTION10_GATE_B1E3D_VNDK_STATIC_LIVE_EQUIVALENCE_504.tsv
      sha256=6800548720af5b045fb580499e7af239499d9c28a2411794afcdfc3c8ab7a4d7

  Draft source before B1E4 insertion:
    C17S_SOFTWARE_SECTION10_DRAFT_FULL_SCOPE.md
      sha256=45b6c889181f2576a55198bc7ea032d03f1f89e3942a436b2ec13cd82e38d804

  VNDK-29 closure counts:
    - /system/lib64/vndk-29: 216 ELF64 files.
    - /system/lib/vndk-29: 216 ELF32 files.
    - VNDK-29 total: 432 files.
    - Static/live size match: 432 / 432.
    - Static/live SHA256 match: 432 / 432.
    - Live SELinux context: u:object_r:system_lib_file:s0 for all 432 rows.
    - RPATH/RUNPATH: NONE for all 432 rows.
    - Classification applied in this inventory: REQUIRED_SYSTEM_VNDK.

  Closure boundary:
    This closes the canonical VNDK-29 file list and static/live byte-equivalence
    for the stock extraction and live mounted runtime paths captured in this
    evidence set. This does not close ODM symbol-table delta analysis; that
    remains owned by Section 10.15.

10.3.4.2 Field design for the embedded VNDK-29 tables

  The VNDK-29 evidence is split into three table classes per ABI so the database
  remains readable and usable:

    - Identity and equivalence table:
        one row per library, carrying size, SHA256, ELF identity, Build ID,
        SONAME, RPATH/RUNPATH status, static/live equivalence, ownership,
        SELinux label, and classification.

    - Path provenance table:
        one row per library, carrying the live runtime path and the static stock
        extraction path used for byte-equivalence proof.

    - DT_NEEDED table:
        one row per library, carrying the complete DT_NEEDED dependency list.

  These three table classes together form the complete VNDK-29 library list.

10.3.5 Complete VNDK-29 library list — 64-bit set

  Runtime directory:
    /system/lib64/vndk-29

  Row count:
    216

  Table use:
    The local ID column is stable inside this ABI subsection. Identity, path
    provenance, and DT_NEEDED rows share the same local ID. SHA256 is the
    static/live-matched payload hash. Static/live equivalence is carried per row.

Identity and equivalence table:

| ID | Scope | Library | Size | SHA256 | ELF | Machine | Build ID | SONAME | RPATH/RUNPATH | Static/live equivalence | Mode / UID:GID | SELinux context | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | top | android.frameworks.cameraservice.common@2.0.so | 15064 | 1113f440b8db1b5327397d04d1c8acec420807ae2fbe5e6f2fc8d0882a058c6b | ELF64 | ARM aarch64 | e6cc225b8e2d103e16aa652a4aea6df8 | android.frameworks.cameraservice.common@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 2 | top | android.frameworks.cameraservice.device@2.0.so | 195400 | ad0e8a64a02e839f43a944fa59482cd66beef5bfd825f066bed4f877f46f2388 | ELF64 | ARM aarch64 | 2a38a304e557359b7d930daa4420df59 | android.frameworks.cameraservice.device@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 3 | top | android.frameworks.cameraservice.service@2.0.so | 147744 | 673ffa33f53a5276dce1578dd119c732e7576767cdbd3de1434a788e89c8d182 | ELF64 | ARM aarch64 | 184c106b4701c3bd2bdc351c1314f8b5 | android.frameworks.cameraservice.service@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 4 | top | android.frameworks.displayservice@1.0.so | 167336 | 80767f2bd64f80e23adb33bcc70e70e4fe480c745deab9a4ad9058d85913511a | ELF64 | ARM aarch64 | 1cc4f78facaed827881fe548157e264f | android.frameworks.displayservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 5 | top | android.frameworks.schedulerservice@1.0.so | 83688 | b20677c7b5a936aa0c38f1e16553b01ae1b9f0db4e7c6b518274a544676cf48a | ELF64 | ARM aarch64 | d5acb165e5daa3d1f5da03c9e6515751 | android.frameworks.schedulerservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 6 | top | android.frameworks.sensorservice@1.0.so | 223328 | ee9b03956ccada5ed681341098692d31cc30d3946e7e2d86051f036b4e16bece | ELF64 | ARM aarch64 | ba7124f76b269e1832f3c128bc7460cb | android.frameworks.sensorservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 7 | top | android.frameworks.stats@1.0.so | 118728 | 8e46ef3ab5229ba0646e3e21c5388f56e43e5de8e7806798c37aeb7e8ec84e33 | ELF64 | ARM aarch64 | 086d73660ef7ef4c33615adc092512f2 | android.frameworks.stats@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 8 | top | android.frameworks.vr.composer@1.0.so | 120704 | 142a3c3cb26c88888e76586d32aa4ec601410dc0ffe7eb4f0f1a39096caa774f | ELF64 | ARM aarch64 | e9620bbb65143cdde7ef2ac2bab1a8f1 | android.frameworks.vr.composer@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 9 | top | android.hardware.atrace@1.0.so | 88136 | 927e808dadcdcc1b6c716235cd9d4fd00fd817c3fbf695a19e888493439beb71 | ELF64 | ARM aarch64 | 49000d24d0bc06007882867e81bd1ea2 | android.hardware.atrace@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 10 | top | android.hardware.audio.common@2.0.so | 15040 | 43eddecefb92fffa38437e6245b25c2f0fc77d7e17be73bceb4f8275b8091776 | ELF64 | ARM aarch64 | 1c14220a57654079b0be8d78a125b40b | android.hardware.audio.common@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 11 | top | android.hardware.audio.common@4.0.so | 15040 | d3bb31dac7e17b24e4f06e6cb40ceebe55ade0889819104cb62c249d0a0c0b94 | ELF64 | ARM aarch64 | d4cf7c51fd2c011aa11c4b4560525635 | android.hardware.audio.common@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 12 | top | android.hardware.audio.common@5.0.so | 27560 | f9df3c731e8a725440bd77cbfe72136282d5e44bb78024bd7a79247631c503a1 | ELF64 | ARM aarch64 | 1cc6185a7e8f3bc79e3176d69cacdd5a | android.hardware.audio.common@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 13 | top | android.hardware.audio.effect@2.0.so | 1283480 | 6b0cfdb1945851a5ece78c4ab35c77681a7f7fd847602f34476349828c368ce7 | ELF64 | ARM aarch64 | a7545dbce164073977adc5650c5f99c8 | android.hardware.audio.effect@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 14 | top | android.hardware.audio.effect@4.0.so | 1275160 | 65e2ce98048344e0db4ea3f14093515ad721ddb33ef1d980e0af5b3603731e0d | ELF64 | ARM aarch64 | 9744ab45a19031ccfc5ee45f03b93d17 | android.hardware.audio.effect@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 15 | top | android.hardware.audio.effect@5.0.so | 1275216 | cf6f897a309b1978b36103abb1da54d37b3a65ca34225eb95efb171b66f10e90 | ELF64 | ARM aarch64 | d62c824dc5d27c9e6ed546418457679a | android.hardware.audio.effect@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 16 | top | android.hardware.audio@2.0.so | 625832 | a64190185596807bccafef3e131643bbde6dfd2031c6300706892036896f802d | ELF64 | ARM aarch64 | c470e4fc28f5e22544142deea5d9152d | android.hardware.audio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 17 | top | android.hardware.audio@4.0.so | 664704 | bec26e72b595941c7bf3579a0214537d3af589d6b4e86751b9ce0d0d269a2db9 | ELF64 | ARM aarch64 | 406682eedbb38273e3d518d0145394e8 | android.hardware.audio@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 18 | top | android.hardware.audio@5.0.so | 673048 | 4400b1fd37b62cb9863074e58257f273e0527eff8b7a07a2ca8f3c4c2ffe066b | ELF64 | ARM aarch64 | 651e54790b4edb991127e6bc4dec2133 | android.hardware.audio@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 19 | top | android.hardware.authsecret@1.0.so | 79616 | 155ca5cdae8dfdd608648ebdd2dd5e8437a96a36a92acfb1c5a145c5b911a1ea | ELF64 | ARM aarch64 | 330bb5c41da5a880dd02dc6117193d99 | android.hardware.authsecret@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 20 | top | android.hardware.automotive.audiocontrol@1.0.so | 84072 | fa53c4c47fca5e91b16f6cde8ca5e6e9bbfb48c9331e23f7cbf17adb83b816da | ELF64 | ARM aarch64 | f0f028b174b5733ea079803e5aaf9434 | android.hardware.automotive.audiocontrol@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 21 | top | android.hardware.automotive.evs@1.0.so | 247768 | 1cd091cc22a443c007694fc6a79b1e42e184573f1679e4244e798a0f3f8b136d | ELF64 | ARM aarch64 | d0c0d98a563ee2f63ba64a67b1ab032a | android.hardware.automotive.evs@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 22 | top | android.hardware.automotive.vehicle@2.0.so | 156376 | a2c6b1b9549d45b5e11676b2fe5ea66c5e6dfb93e6782d8667da1d1e118d95e6 | ELF64 | ARM aarch64 | 432a5524c8ea1c2bba814ff3f8d6ea35 | android.hardware.automotive.vehicle@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 23 | top | android.hardware.biometrics.face@1.0.so | 186096 | b7a2e6c54871ede74fdb3bbcf08f9e91cd55425a36ebfa36e5973793d3d4329f | ELF64 | ARM aarch64 | 47b9f4662eed917a03e6f4266286cde7 | android.hardware.biometrics.face@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 24 | top | android.hardware.biometrics.fingerprint@2.1.so | 168288 | c8c07ef0e48653ba3eac6d436b480039af2f7f9e37aa190c9c906ff5e4c974a1 | ELF64 | ARM aarch64 | 8e0d864c3a4868b51b7c388c837ce282 | android.hardware.biometrics.fingerprint@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 25 | top | android.hardware.bluetooth.a2dp@1.0.so | 134616 | 0d675e4da6eb0c2b7d63cd8a815e10f1086e443a59bcf21267e4abe52033a8ea | ELF64 | ARM aarch64 | d8e7735aa719f40bc622d70e7270aa46 | android.hardware.bluetooth.a2dp@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 26 | top | android.hardware.bluetooth.audio@2.0.so | 222280 | 69b1810ccb9353b45f65a67233203ad95f9beb766cb4a577dc4a0f7d00c6f683 | ELF64 | ARM aarch64 | 550ce447ecd042ea23053789293a6a24 | android.hardware.bluetooth.audio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 27 | top | android.hardware.bluetooth@1.0.so | 129784 | cf9f4983d8d1220a828942b153aea5cf2487755cd4c06a70a947a8fc09d6212b | ELF64 | ARM aarch64 | 0cfa255f0c9928c537a8359324897d18 | android.hardware.bluetooth@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 28 | top | android.hardware.boot@1.0.so | 101376 | e1c5c993322444e4519402c9898270d691a82a6b6106a282d04fcd12d9e83fa2 | ELF64 | ARM aarch64 | 6b1bc178e9d68247a2ea2681a66ce8ea | android.hardware.boot@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 29 | top | android.hardware.broadcastradio@1.0.so | 253360 | da0028f1cf7cbf09ac72677c1539ff8cbced34b801157cc02e95621a3872c499 | ELF64 | ARM aarch64 | e09a5cdb14087438014f754a7491f7fb | android.hardware.broadcastradio@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 30 | top | android.hardware.broadcastradio@1.1.so | 286904 | 60ec3767a1e8feacd443084b78c8d2cf4aa9cca2da9fc60a772be304490a83a9 | ELF64 | ARM aarch64 | 068fe185431f268c3888cee3a3d1f525 | android.hardware.broadcastradio@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 31 | top | android.hardware.broadcastradio@2.0.so | 321000 | 438d1b12ce53efc4bbc5ec364054a9581f3a66b5c4f829172531de9353237ad4 | ELF64 | ARM aarch64 | f17c29c262e53b264cb61f2f2b82865e | android.hardware.broadcastradio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 32 | top | android.hardware.camera.common@1.0.so | 15048 | 5d41b0f7dcff57977ff039a76b5f4ea46d0d71cec4a4a0233c3e3f008e170ab1 | ELF64 | ARM aarch64 | 1ce6eb262750073d0f59e505408b10ed | android.hardware.camera.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 33 | top | android.hardware.camera.device@1.0.so | 278416 | f0814480600d62ecd822036de62f8f9802bd23c7ea918ac6171719fed62c99e7 | ELF64 | ARM aarch64 | 0c3b7f733db95568d947ee314399e055 | android.hardware.camera.device@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 34 | top | android.hardware.camera.device@3.2.so | 202952 | 4c28c958c1f689b3f21512b6fad294e7d585983c2a0ff0070e6ae6b54fcaf32d | ELF64 | ARM aarch64 | 22f08f75b2286c530fe382af0419ec6e | android.hardware.camera.device@3.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 35 | top | android.hardware.camera.device@3.3.so | 98088 | 6ef249ce8d696b75a4f168012b822be9738149efdbbcb113e0909bc91d2ef1ef | ELF64 | ARM aarch64 | 59722f9c26784e08f2042633681269b1 | android.hardware.camera.device@3.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 36 | top | android.hardware.camera.device@3.4.so | 162112 | a07227489b91f2872604b5d8cdd2c54193f64017796f14e95fe374e1cbe5ba53 | ELF64 | ARM aarch64 | 37760b7be507d0291ece897e12af4185 | android.hardware.camera.device@3.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 37 | top | android.hardware.camera.device@3.5.so | 244992 | 4a2365ee3f3599c16cd5aa5324c529a4dabc97f623fa49723094f0c95833e5c2 | ELF64 | ARM aarch64 | 525e8c39b93373035855d0ff28130370 | android.hardware.camera.device@3.5.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 38 | top | android.hardware.camera.metadata@3.2.so | 14984 | edc467a79388f773a4145886e92ea33e62b1ab8e8be72eedf0d004024dff2499 | ELF64 | ARM aarch64 | a76bc8c1f5f441f1efca58bab12dc69f | android.hardware.camera.metadata@3.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 39 | top | android.hardware.camera.metadata@3.3.so | 15000 | 201af7c20ea5bfc7da6b429bc168f5abcfb9714e9fb550da2db119771684a91f | ELF64 | ARM aarch64 | 092ad47efec64f4b2d4fdb7deeaec2aa | android.hardware.camera.metadata@3.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 40 | top | android.hardware.camera.metadata@3.4.so | 15016 | b7af173e954857b5816574531be939b741e50a4d64e5feb898a35cff5f7c453f | ELF64 | ARM aarch64 | 38a4c049e5a3da5fd8361ece597a1b98 | android.hardware.camera.metadata@3.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 41 | top | android.hardware.camera.provider@2.4.so | 147928 | a871349ba0c6ab2ee31ff169244096296bfbe3016459da20eec32affcf8262de | ELF64 | ARM aarch64 | f1502ed105141784d5534bfebf826862 | android.hardware.camera.provider@2.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 42 | top | android.hardware.camera.provider@2.5.so | 93784 | b361dc7611d739d12c45154b5ea9e05cf0af0fcc6ce30e627aafa08771604cfe | ELF64 | ARM aarch64 | 89907676b1738845acbf08b1426e5885 | android.hardware.camera.provider@2.5.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 43 | top | android.hardware.cas.native@1.0.so | 93184 | 9caf0b9d090d25ffb0e44e0ecc37570b9ea2df28c0b13df912964ab5578918a2 | ELF64 | ARM aarch64 | 0fd15ae6df57bf669182235a093fd0b2 | android.hardware.cas.native@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 44 | top | android.hardware.cas@1.0.so | 234616 | 99bd8a5264b1ca5d15737ddeea8c99f2cd950b6de558709de41f5fba686051a5 | ELF64 | ARM aarch64 | 4eb12bf95f4b43e7201de1c72b1525f1 | android.hardware.cas@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 45 | top | android.hardware.cas@1.1.so | 186448 | d27837c229b638d37f061b3daf9e493f6216ecfc1a94164cce1b1f632a8fbfbc | ELF64 | ARM aarch64 | 973330c19c32c22433bf09bce5c96fe7 | android.hardware.cas@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 46 | top | android.hardware.configstore-utils.so | 15512 | 40d7e9f9e9cb55738085ec87f58501a31edd1429c3bca359b47141588c2c3957 | ELF64 | ARM aarch64 | 29b13033fec256e1293625e3bacbc64d | android.hardware.configstore-utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 47 | top | android.hardware.configstore@1.0.so | 123632 | e6d1df9e02cb389d3ffde2d78434ca8749e1477c318dd7b27ba341c8bab797b2 | ELF64 | ARM aarch64 | 484c10fe764fc2985d9ddfd25ddd2e3b | android.hardware.configstore@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 48 | top | android.hardware.configstore@1.1.so | 102576 | c86bd9125984c05f970e8069be320b0c1dfbf94806037ebcd9dec12370ed7e2f | ELF64 | ARM aarch64 | d35bf3613e7334cc863ee06eaeaea758 | android.hardware.configstore@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 49 | top | android.hardware.confirmationui-support-lib.so | 14904 | bbb933230bd7bd9ca479f718b8f1f26c36e9605096d24061f149bf20e258ab28 | ELF64 | ARM aarch64 | 476a45887445b76cda23bdcf8ec4f855 | android.hardware.confirmationui-support-lib.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 50 | top | android.hardware.confirmationui@1.0.so | 125448 | 9704cc80ddfe8b3e8712e47df03e561eef1adcbdbb25d3c54b0ff9b237601f2c | ELF64 | ARM aarch64 | 74d96f3169b08c5b47cfb81dcc7ce1fd | android.hardware.confirmationui@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 51 | top | android.hardware.contexthub@1.0.so | 151120 | cfb69012dd13c6aa345c2608170ab4b1f458d2a605b6e1667b44479c63b1e75c | ELF64 | ARM aarch64 | 4a00b70fae213548dafb914ec8fd036c | android.hardware.contexthub@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 52 | top | android.hardware.drm@1.0.so | 368568 | 8dcace34538dd902d4418709147869404d5a668df8c88c6bf62cde2f3a37018e | ELF64 | ARM aarch64 | bb91988bc55d53d84bac095f14b01a47 | android.hardware.drm@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 53 | top | android.hardware.drm@1.1.so | 253584 | 9b17305779dbdd831fbe50127f0d4d05f9e0eca1053f56c8b2048ddce494137a | ELF64 | ARM aarch64 | 31a1d41027c6a38221d289e4fc9dcc72 | android.hardware.drm@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 54 | top | android.hardware.drm@1.2.so | 383896 | ef2c0d81437df5619fe796cdebac76e18fe2efc888ae832d178b0f931252c9c2 | ELF64 | ARM aarch64 | d28f7267efc56a9bd4441ec05c2d6ae0 | android.hardware.drm@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 55 | top | android.hardware.dumpstate@1.0.so | 75376 | f7c62f9be8dcedea190607ad6b15d456e80dc9c2c1376f6ea4f4d92a6a989fbc | ELF64 | ARM aarch64 | ffd498e5756c281f9aef5aa266340b1f | android.hardware.dumpstate@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 56 | top | android.hardware.fastboot@1.0.so | 93512 | 64912a09994ed8036d8ee515a9ef8a1eafb38fe67be920be50fcbe45ed511ec6 | ELF64 | ARM aarch64 | 99eaf5e3cb033a4c90de82038628ef3c | android.hardware.fastboot@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 57 | top | android.hardware.gatekeeper@1.0.so | 92856 | f97e190288ec175fc21c735920ce5359a9dbadda6660a271c26290287e29fef5 | ELF64 | ARM aarch64 | 40575e1e9804fe2897d3cf95910711a2 | android.hardware.gatekeeper@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 58 | top | android.hardware.gnss.measurement_corrections@1.0.so | 125352 | e080d150e9d070f50252b04bbf784a27909589352b2bac7547a014d770a1450a | ELF64 | ARM aarch64 | 6ebbb5ef596b8d394f503450906828cd | android.hardware.gnss.measurement_corrections@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 59 | top | android.hardware.gnss.visibility_control@1.0.so | 125384 | 5039ff5c058fdcba40e47283092e3a1b7efd35c610d6528453c0ff452a1c51f6 | ELF64 | ARM aarch64 | 64ea091f6303ed5274756de73c804d0c | android.hardware.gnss.visibility_control@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 60 | top | android.hardware.gnss@1.0.so | 962664 | 07100dceb0b7052bd7f4e6a58c0b4d0d0950a9843338919eb1b2bc6a306e2391 | ELF64 | ARM aarch64 | f768b292863eb1e0dd4933bf9c1edaac | android.hardware.gnss@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 61 | top | android.hardware.gnss@1.1.so | 326056 | 56fe381b8fa5c53f304ee74a2b052248ab05f5db4667da48c60b1861a92fc7d6 | ELF64 | ARM aarch64 | ee569c65e9113688c4b2862b07ae6c51 | android.hardware.gnss@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 62 | top | android.hardware.gnss@2.0.so | 657168 | fdb11a3c7bca640f5fe34d312b73877ec8116a88e05db464883e7c9add80472b | ELF64 | ARM aarch64 | 1cb623e9b93e8c5f6c98bfdaae9dc062 | android.hardware.gnss@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 63 | top | android.hardware.graphics.allocator@2.0.so | 84328 | f9ec9351b351351ab4a87b035d3dbeede376357d88c270b5e2ff4170eefb3c2d | ELF64 | ARM aarch64 | 86005c834372c11ac74418ed33c52365 | android.hardware.graphics.allocator@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 64 | top | android.hardware.graphics.allocator@3.0.so | 84368 | d9f44402162160bec51dea8ddb8dd826ff273b3dd5df03d35ebaee2827840e63 | ELF64 | ARM aarch64 | 3ca40611ae6eb6384896560965aec1d0 | android.hardware.graphics.allocator@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 65 | top | android.hardware.graphics.bufferqueue@1.0.so | 204312 | b41e9d58d171e36a91db9ee962ba3bf44b275612a23938d46eb456c002bfc978 | ELF64 | ARM aarch64 | 258da5941643a32ca38b437c52804835 | android.hardware.graphics.bufferqueue@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 66 | top | android.hardware.graphics.bufferqueue@2.0.so | 182632 | ac1b9569d053b2349ca47a6fa5e29e41fa36ee5c486ece18962b3cc1d31394e0 | ELF64 | ARM aarch64 | 1b45e0ca70a2f883245cd3c16ac1f78c | android.hardware.graphics.bufferqueue@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 67 | top | android.hardware.graphics.composer@2.1.so | 255424 | 8468a67c98cc91b0fc97f6ceb785fe7ac38ca7aeb6e302838e1d997741cf7937 | ELF64 | ARM aarch64 | ef0977c8b764daef84be2c33ab49932f | android.hardware.graphics.composer@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 68 | top | android.hardware.graphics.composer@2.2.so | 211480 | 83fb017933aaf595db2af44cd3a996643f1e046d7f5764f23aba38a04d46dee8 | ELF64 | ARM aarch64 | fb18be75b5d94f79c440efdae8e9d28f | android.hardware.graphics.composer@2.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 69 | top | android.hardware.graphics.composer@2.3.so | 265232 | b5e4cd85d750dd91af1e03fc49e8757b69ad82152ff01b165a954bc193ec6b0a | ELF64 | ARM aarch64 | d1d22a8766294b9782b3e0fe32d187b5 | android.hardware.graphics.composer@2.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 70 | top | android.hardware.health.storage@1.0.so | 117256 | 2288c102790a4ea81f3443f147e3e5b49209a24d92af96409006177b3af198b4 | ELF64 | ARM aarch64 | 1c4d18f85c2134566c8a3e6b77bfcec9 | android.hardware.health.storage@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 71 | top | android.hardware.health@1.0.so | 88784 | 4e06cae125b65eaa41055680b79cfda203f58205e97ffb1e0a6cdcd4b3cc417b | ELF64 | ARM aarch64 | 309ac90b56870b11a612bb2197e7542f | android.hardware.health@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 72 | top | android.hardware.health@2.0.so | 161464 | 55cf1a1d1b7a09799c2877c4f9103853130a1ced27def15a1caed6c7cce8e171 | ELF64 | ARM aarch64 | f025045e3bbd69e26ee432efacf09dd8 | android.hardware.health@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 73 | top | android.hardware.input.classifier@1.0.so | 83784 | 8798b06d3e59dac1c8935935aba2e316dc09f61a59d792a8fed887d3e080ea2e | ELF64 | ARM aarch64 | eac110e30a61a92f5640e2938dd3c2d3 | android.hardware.input.classifier@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 74 | top | android.hardware.input.common@1.0.so | 15024 | bf52c8e85852aa01ebd23234c704b3de432441bf74024325005936e75763b11a | ELF64 | ARM aarch64 | 4bfa51e5167cff059d3adb5549a5213b | android.hardware.input.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 75 | top | android.hardware.ir@1.0.so | 79936 | a9850baa88d196e4bfc8dde408b807255f42f2e016f27e231053cd6915000a3e | ELF64 | ARM aarch64 | e98199b93ec8cf137cb49b526a608bb8 | android.hardware.ir@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 76 | top | android.hardware.keymaster@3.0.so | 140880 | 5021a6d916dad6d8b437f3b2d95303d2aeb9e192657efdaa71eabb165ea9de4e | ELF64 | ARM aarch64 | d92832c4aade590e8d556af640a10bc7 | android.hardware.keymaster@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 77 | top | android.hardware.keymaster@4.0.so | 158504 | 23269167ccbc0a57ccfd919876a93a53b4a2e3e0f9edbc44899aab2bb8787000 | ELF64 | ARM aarch64 | d1ccf53473ecb07d8ac0a712be162fe3 | android.hardware.keymaster@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 78 | top | android.hardware.light@2.0.so | 79808 | a69651f0aef6eda88001cd227174fba0da593b6c45bc913442cac7c0d02aad9c | ELF64 | ARM aarch64 | 82c4e1babbd8be48d2e7403b5c05e704 | android.hardware.light@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 79 | top | android.hardware.media.bufferpool@1.0.so | 163824 | 6163db5b19bc7616d18d2b23f7aa7074360b4ba30606003ffc20a739f77db168 | ELF64 | ARM aarch64 | a5967c60dbaaab92b93c0d0b5e692b0e | android.hardware.media.bufferpool@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 80 | top | android.hardware.media.bufferpool@2.0.so | 201544 | 04b3ad1a093857b885a2281b14b323a5c358698d9a5c5c022a8db172cf8014e6 | ELF64 | ARM aarch64 | c374448e1593ea117e8c90ccf0eec67d | android.hardware.media.bufferpool@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 81 | top | android.hardware.media.c2@1.0.so | 530696 | 9c21b77d2debe92071bc7d7aff18118ccb7f03c2a2bab1cf6a7316e1be08630a | ELF64 | ARM aarch64 | 5ecfacfbd9010bb0698948ecbe1c1559 | android.hardware.media.c2@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 82 | top | android.hardware.media.omx@1.0.so | 401992 | 330cf6b3de10a63c6c6c0af49a8b57b797907a2eea0f97ebb03e74489ee7e264 | ELF64 | ARM aarch64 | c81df9268ca4c1eb07dd5d3262a379fe | android.hardware.media.omx@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 83 | top | android.hardware.media@1.0.so | 15024 | 3b22f28cd351d240621f6fa92d94884d74c3229dd32f4818ad95b8c5191badd7 | ELF64 | ARM aarch64 | b473a222635fa41ba2885a0f543ea2e7 | android.hardware.media@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 84 | top | android.hardware.memtrack@1.0.so | 79888 | 860f47e935284466b4ecf1300de3936e88b288b50f60e9ec4636452e5f742003 | ELF64 | ARM aarch64 | 0fe848502d92eb701bb797ddc8ebafc8 | android.hardware.memtrack@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 85 | top | android.hardware.neuralnetworks@1.0.so | 213584 | 2e3e3e2a9a33407aa5c7254c49c187ce6a65fdcf48d220bff148cbaf65f7bc96 | ELF64 | ARM aarch64 | cffd0b5f37957753f7fcfcf32a7e3b07 | android.hardware.neuralnetworks@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 86 | top | android.hardware.neuralnetworks@1.1.so | 101960 | 1476cbd157805e9c94e49a8699511c71660a24e48bb34034cfad7a0235e8bdab | ELF64 | ARM aarch64 | b0936c5cb97fd8707077b1bb5b98d3f6 | android.hardware.neuralnetworks@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 87 | top | android.hardware.neuralnetworks@1.2.so | 398280 | 8a149a6b2fae76854656523bd07870eb720f3289d31f644967da72f7aea3fcf3 | ELF64 | ARM aarch64 | bd61c18b8c04e4edb8216952a3a16c52 | android.hardware.neuralnetworks@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 88 | top | android.hardware.nfc@1.0.so | 133856 | fb1fbe1fc92c98232c3f54886e646af827145587fb92593eef960aba3e19a2a6 | ELF64 | ARM aarch64 | e43ef4c29b5784f67b94af036a57b97b | android.hardware.nfc@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 89 | top | android.hardware.nfc@1.1.so | 144056 | 12c0efc9549ecdaaac93de5c085f3c872bc2b45fe20e59a3b23ef57d3a86ed45 | ELF64 | ARM aarch64 | 4f2828b236792c53483af2d97c7ae221 | android.hardware.nfc@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 90 | top | android.hardware.nfc@1.2.so | 102576 | 71cd1e00733e7d51293553c0c793bad0cbf5e1367b5c93260af33e8ec6853a45 | ELF64 | ARM aarch64 | a7c17c014821e56892b6f24c039fb83d | android.hardware.nfc@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 91 | top | android.hardware.oemlock@1.0.so | 92968 | a792f14b7a188847eaf3175286778ca2b225c8367289398db112c479c3d39df6 | ELF64 | ARM aarch64 | 3c146c48dc3dc3d779c71eb04dd3fe9a | android.hardware.oemlock@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 92 | top | android.hardware.power.stats@1.0.so | 102056 | 3fb9c580e62fa0d6a8fd7e477fee3465f7ce9d7374d2ec6b2ff121ac698d3ba7 | ELF64 | ARM aarch64 | 588f4219509bdcb2ca459f6eb4456c63 | android.hardware.power.stats@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 93 | top | android.hardware.power@1.0.so | 88376 | 4cd6869f59d0972998c0bbc5b1b4acf8249cf50aa26baee5ab8f6204217d9f8d | ELF64 | ARM aarch64 | 40e2778fbb8d019f7d0ede93a4769fd6 | android.hardware.power@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 94 | top | android.hardware.power@1.1.so | 93480 | 50407c253270b5a088b62216d78f792f777a9f0eb496db47e414a0175b3f7e03 | ELF64 | ARM aarch64 | b078b735129ff3074c9b4648d75ddb6f | android.hardware.power@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 95 | top | android.hardware.power@1.2.so | 94056 | 5777631c386a3f16fe85c3e7273f3f0c18f31b9ee7858436f10c524d83b6bf6e | ELF64 | ARM aarch64 | 7ae4b91482524781a763d94300304e13 | android.hardware.power@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 96 | top | android.hardware.power@1.3.so | 99016 | 94b251688e11f4596e742dd05da6c0b9257c7e43e80c23b434526946e8a2c6be | ELF64 | ARM aarch64 | 997bcc37a6bf03deed6c31e5ad9875d1 | android.hardware.power@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 97 | top | android.hardware.radio.config@1.0.so | 171936 | 20171a0f9a5f34ece37a2131dc009fe8b00691a89a0ac2221d7e5bc3f2d2fab7 | ELF64 | ARM aarch64 | 36afa0e9c609419f2084e9d0dfd1e9e2 | android.hardware.radio.config@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 98 | top | android.hardware.radio.config@1.1.so | 204048 | e0b42f0f33911d59abad870f48bab35c2272413016419bfac9eb57162ef1f6bf | ELF64 | ARM aarch64 | 410889be4fa77977e3f90c6810d22452 | android.hardware.radio.config@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 99 | top | android.hardware.radio.config@1.2.so | 158208 | c7ecd05e2daa8431a78ceaf56372d932af4604fd50387f3ca127289e43d9e181 | ELF64 | ARM aarch64 | 90c8b174af1da1a866a9f503bf3476ea | android.hardware.radio.config@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 100 | top | android.hardware.radio.deprecated@1.0.so | 171912 | f0c63fd34c8f7dd08cdce84ebecc0a49111657c08d0da887b6e5e935fbae7231 | ELF64 | ARM aarch64 | 6f7bb706cc2edf4e8cbcfa6244589193 | android.hardware.radio.deprecated@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 101 | top | android.hardware.radio@1.0.so | 1359632 | 4a24e0ffd3824e9f1a3d1b19e4770c59b74234a8ad9ed64fd800e0c87b43003f | ELF64 | ARM aarch64 | 0090b2d2df4c18dc77fd7609ba935ac6 | android.hardware.radio@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 102 | top | android.hardware.radio@1.1.so | 969856 | 3cf8eba25423f31895c8d84ac00548495f08de956ff9e6fe29c42bacb4f870fa | ELF64 | ARM aarch64 | 58231e5def3b02dde9b3fa8d37d7a599 | android.hardware.radio@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 103 | top | android.hardware.radio@1.2.so | 1076416 | f5bfcf30ddfb1a899338179a90de66eabb3beb3c62e4b9b15621f1b11bdf3901 | ELF64 | ARM aarch64 | f2f6c10f9e1ef30f6371b3c39d012be6 | android.hardware.radio@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 104 | top | android.hardware.radio@1.3.so | 1019400 | 3218e3a66d844cac2e80de99bdf8dfb951921222609bee647bc5da17418bb743 | ELF64 | ARM aarch64 | 4c30cb8d2e040e81dd3606869388ff1a | android.hardware.radio@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 105 | top | android.hardware.radio@1.4.so | 1189328 | e1298db7c606d9f9f1247e757ed1883f84165fba9c09bab4873503e660285b20 | ELF64 | ARM aarch64 | 09f35aeb8ad8360f61d7207b3e33c3a4 | android.hardware.radio@1.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 106 | top | android.hardware.secure_element@1.0.so | 139080 | e966913510abeb9f43b81b5961e020ff294aa5803dc3b166d6b24e295968aaac | ELF64 | ARM aarch64 | a43b5acb176d5fa2b951452a12001dde | android.hardware.secure_element@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 107 | top | android.hardware.secure_element@1.1.so | 139760 | 3e71558aec4a862fe76fa013d1e59ad050b3a54e7440fdb6cab5ec03839b9d90 | ELF64 | ARM aarch64 | c7c1d17b5cc580974646a4068ec5124c | android.hardware.secure_element@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 108 | top | android.hardware.sensors@1.0.so | 105984 | a8e941cfb2f5bae1786896ed1c4c1b968091fbb917251d78b9dce0cd8a15edad | ELF64 | ARM aarch64 | a3ce77fe09ca1eafa4cfb2b9f78a1c92 | android.hardware.sensors@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 109 | top | android.hardware.sensors@2.0.so | 155984 | 7e5f7170223f1b6468a51a4ff2c9d279c44dc7d58aed95a3b4d996185ca49e34 | ELF64 | ARM aarch64 | 13e444cc537a4d448cccb75286249b68 | android.hardware.sensors@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 110 | top | android.hardware.soundtrigger@2.0-core.so | 36808 | d215359aef1c4b275240dc6756720d2a4ddda0de09d38f7794707be843d337ca | ELF64 | ARM aarch64 | a2f8fb213c2fcedc91c10213542cecfa | android.hardware.soundtrigger@2.0-core.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 111 | top | android.hardware.soundtrigger@2.0.so | 151432 | 05ca0a40ed7659d4a47ef535b84ef360f3a9869bbeec29f44afbf8d11c844c29 | ELF64 | ARM aarch64 | eee6eadb0b243030e87e26b5f95917ff | android.hardware.soundtrigger@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 112 | top | android.hardware.soundtrigger@2.1.so | 165528 | 47e32ac5c00a9db4163d84a75c0e070fe75a4a2f5aa1a3733080120115010df4 | ELF64 | ARM aarch64 | d3b66362bc5a5856a4cb3cad3604a545 | android.hardware.soundtrigger@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 113 | top | android.hardware.soundtrigger@2.2.so | 102680 | 0c6e43d2561d825b1a4157871a594074632ad414e21855522ca631b39d0072c1 | ELF64 | ARM aarch64 | 01f97f23230e36a66ecef48f027e2597 | android.hardware.soundtrigger@2.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 114 | top | android.hardware.tetheroffload.config@1.0.so | 79896 | a0e904c65cefcf2c8afd0b70f01417c06055a134409a09ee09e08a2227b9e3f0 | ELF64 | ARM aarch64 | 839402c7e87e4be654ebfbcb53082d83 | android.hardware.tetheroffload.config@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 115 | top | android.hardware.tetheroffload.control@1.0.so | 160896 | 9302500238630a897e23cda47057f79d24c929aeb3795ca5b10a020e56b72f2a | ELF64 | ARM aarch64 | 4bccab79ccb80d297887bce315d0eb25 | android.hardware.tetheroffload.control@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 116 | top | android.hardware.thermal@1.0.so | 88688 | a55bf022ae30091cf9faeba7bcb6d98b4046c0c0345765f2e474bbf1085860b4 | ELF64 | ARM aarch64 | 182b27301e61863a0eee9c11ecb52180 | android.hardware.thermal@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 117 | top | android.hardware.thermal@1.1.so | 126496 | 716d052c1cd4381026ae48d2876932d4a9022cdafcbe7012ad3e211b5f3437c2 | ELF64 | ARM aarch64 | 67d23eeee554a2259bab26a2043c50e8 | android.hardware.thermal@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 118 | top | android.hardware.thermal@2.0.so | 148592 | 7a009fb1e6797522b0bb32195ee3f2aa6ccd8f3a6cc5172603096717bc64025d | ELF64 | ARM aarch64 | 25a335726c9f916ea03ee73f6d9d28d9 | android.hardware.thermal@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 119 | top | android.hardware.tv.cec@1.0.so | 151704 | f925835834fb61b55ce16515bb119d36b388b25786ae02ad9af2c61685829842 | ELF64 | ARM aarch64 | b9594edd2d4d4101773062bbd60ebd3b | android.hardware.tv.cec@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 120 | top | android.hardware.tv.cec@2.0.so | 164440 | 41c33ce74821e8ca8ec8eb5da94362013444d8b401d9c458227b9a84fd92d92f | ELF64 | ARM aarch64 | b8f9d9144ff9da229ff3ad4c012a6d5f | android.hardware.tv.cec@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 121 | top | android.hardware.tv.input@1.0.so | 130232 | 456910baa698243302a7af94e5d6e4e19e7978b28db1e165e2da3c8f763f887c | ELF64 | ARM aarch64 | 1c361dc89bd401a3611958d6e9aeb185 | android.hardware.tv.input@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 122 | top | android.hardware.usb.gadget@1.0.so | 121768 | 2420371d9fbe68e6030aecb8746b067a8c0db1316ce0510415be02f304613204 | ELF64 | ARM aarch64 | b5e5f47e1ec9efd80da502f81ce3ebe1 | android.hardware.usb.gadget@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 123 | top | android.hardware.usb@1.0.so | 126272 | 4944fed54a9f04503b5ab0c6f11ef53b22e26402393ce58a00117091199dd3b0 | ELF64 | ARM aarch64 | c4073a1f9d4fea836c31f67c4c6ef298 | android.hardware.usb@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 124 | top | android.hardware.usb@1.1.so | 131784 | 91acbdc20d88148f0103b5a2b1bd4a3f86b3ee56d263d9a943b58ee72372589f | ELF64 | ARM aarch64 | 04151a343eb044a5fce32dedbd8cd18d | android.hardware.usb@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 125 | top | android.hardware.usb@1.2.so | 150040 | 25bdcad3fc37c83a33be6925912dfb65dd5062ee11d90faa8b822d199e2ea5bb | ELF64 | ARM aarch64 | a5128c9b462f43ea257d4f343842265f | android.hardware.usb@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 126 | top | android.hardware.vibrator@1.0.so | 88368 | ad5fd3a6926d80e8e13deffa12f88aa251fdf3226243696d6620594abebf6dbc | ELF64 | ARM aarch64 | 9d3e9ebd45c782411f84866fdb8aea24 | android.hardware.vibrator@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 127 | top | android.hardware.vibrator@1.1.so | 89152 | 87f379a51e5ba547534d5975082ba1f424c79574cc78e3e99ac6069a6d62a1cd | ELF64 | ARM aarch64 | ae043f7f5dc40bd0fc26efb37e810870 | android.hardware.vibrator@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 128 | top | android.hardware.vibrator@1.2.so | 94032 | 9e6055a408495e0a0c348dc58c4293d947ad6188b1b06e6213b63fbe2b2c4f9a | ELF64 | ARM aarch64 | 96653d7ac6a772c9aa4965be21f647bb | android.hardware.vibrator@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 129 | top | android.hardware.vibrator@1.3.so | 103160 | d45bf6f33b041793816e1513eb4dbd868210fe5deb8dc3d0a1ffff4b9af2f434 | ELF64 | ARM aarch64 | 01141072c0f9f1f7429fafa990e4c0a7 | android.hardware.vibrator@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 130 | top | android.hardware.vr@1.0.so | 79464 | 88c1a979a958d3d4c7af5b808f605e3b272b8963f4ffdab1dfa340e16a236d5f | ELF64 | ARM aarch64 | fe82d43264764beef9a6b15ef9bb32eb | android.hardware.vr@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 131 | top | android.hardware.weaver@1.0.so | 84336 | 926265c7effcd030ff591d956f1b267f68b90e7d7ec5405056f666dd675c3886 | ELF64 | ARM aarch64 | d0fefb5b95f03a44c9d62d8f9071940b | android.hardware.weaver@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 132 | top | android.hardware.wifi.hostapd@1.0.so | 88496 | 8293bea882910d8fcfa482de5f23c1c270c61e4fa49b7d7e267a823b1a410827 | ELF64 | ARM aarch64 | d4af7edafc4c1cce0a84efef7667bad1 | android.hardware.wifi.hostapd@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 133 | top | android.hardware.wifi.hostapd@1.1.so | 135344 | e262b4965ff847e48015edb83c41aa1f2b72efd27c023ae801edb9177db847e1 | ELF64 | ARM aarch64 | 96f938982899c029c2c6b5ed4c3f5079 | android.hardware.wifi.hostapd@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 134 | top | android.hardware.wifi.offload@1.0.so | 143256 | 2301a528d2e17e212f787e588bd31e392170ce2ddf30beb373298e2a4426a650 | ELF64 | ARM aarch64 | f74853d133befc96edf809239698773f | android.hardware.wifi.offload@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 135 | top | android.hardware.wifi.supplicant@1.0.so | 1357600 | 616310766a3b7bb3776f68cc599897aa84325e4565233562500078f6fe0fa2dd | ELF64 | ARM aarch64 | 2f88dfbcbaa34d13bab82a35d5031d41 | android.hardware.wifi.supplicant@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 136 | top | android.hardware.wifi.supplicant@1.1.so | 502056 | 8891a582a975710391205691f7d540a5218e21f9a65434fe38826bbfff070c33 | ELF64 | ARM aarch64 | cdad3160961a7428531673d37877365c | android.hardware.wifi.supplicant@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 137 | top | android.hardware.wifi.supplicant@1.2.so | 747280 | a484a90d8e98b11e559007756f513980561022eaef436c449d1a954c6d7cd8fb | ELF64 | ARM aarch64 | 3496ca65bb9dc006cf41250e94c0b24a | android.hardware.wifi.supplicant@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 138 | top | android.hardware.wifi@1.0.so | 1056352 | 1e54e6ec23c19e232a64a669af48862005958b21312157e6142dc12b9dafbf34 | ELF64 | ARM aarch64 | 19ba29d41aebf0ac9d844eeab8c754d8 | android.hardware.wifi@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 139 | top | android.hardware.wifi@1.1.so | 199048 | 239161a9440f4b965fcd2c5416c41406febfa854bc80d13eb0916a1644158209 | ELF64 | ARM aarch64 | 402f4ee295d08f18c83f3dcf12761b63 | android.hardware.wifi@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 140 | top | android.hardware.wifi@1.2.so | 566192 | 8565ae41c958a525e995369a6e8c17eb47d8d0390507d4d51d05f893afecfe4c | ELF64 | ARM aarch64 | d363abc776f1af11522e3211c2b92572 | android.hardware.wifi@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 141 | top | android.hardware.wifi@1.3.so | 333560 | 65e19c4d533885419464bdbf60e97bac017b865d5d2672129a3b9261c783ee6c | ELF64 | ARM aarch64 | 4422283c817dcc3fd0c0e8aa1a50a843 | android.hardware.wifi@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 142 | top | android.hidl.allocator@1.0.so | 80176 | 52c87ca126fac23e534342de88a827bc9e64d7eb5148a8756ee444e450adfb66 | ELF64 | ARM aarch64 | 689b79c57069fb2bbe120ab5dc3293dc | android.hidl.allocator@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 143 | top | android.hidl.memory.block@1.0.so | 15008 | c21d1020b1f6956b8fb081c15bdd4ca9c0511fbc37a238e9c047d01a91d17c3f | ELF64 | ARM aarch64 | def14cb1bdd16386891909c095ea3bce | android.hidl.memory.block@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 144 | top | android.hidl.token@1.0-utils.so | 20512 | 4979c2263ff15aa595ec72a305a50348281a6fd1d5fff2dcfc164fb5eb68516d | ELF64 | ARM aarch64 | 37f432fbbd598322b322abbaa9081386 | android.hidl.token@1.0-utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 145 | top | android.hidl.token@1.0.so | 88160 | 92bb33abe5804e932e8db9f747cea946099323cc5f986a654f4779607dbe5e61 | ELF64 | ARM aarch64 | 48cfe508ad9267694573b981b6f26d2a | android.hidl.token@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 146 | top | android.system.net.netd@1.0.so | 79928 | 26d6f45b312835dc1466e937630b1980f381ec64fd15410fab2aab191a819c4d | ELF64 | ARM aarch64 | 72ef258c40c936c1a46de0a2952bf44d | android.system.net.netd@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 147 | top | android.system.net.netd@1.1.so | 97248 | 11eec4a61229ffad2d65a16e7496ff9db4f48d2f7a2b2cb33bf0d7ede1d7c3eb | ELF64 | ARM aarch64 | 347e65552addecdde0a530715d273aea | android.system.net.netd@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 148 | top | android.system.suspend@1.0.so | 117104 | 013f906469aa120b0162bf5d5dd018a9ec4d2e6f3db580e778c5cc43be0705c4 | ELF64 | ARM aarch64 | f6b0c090517b791f8c6b7f47124ad144 | android.system.suspend@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 149 | top | android.system.wifi.keystore@1.0.so | 88656 | 7cdee669abccd3bf7efb7b1a30b84d2fbb277328bdd67841e95b5387156ed300 | ELF64 | ARM aarch64 | 751b043a14319974a14a7e9821674809 | android.system.wifi.keystore@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 150 | top | libadf.so | 23576 | 48b8044d40b3ed598689f35c73d8da8fae4ad90dc32b652ab7542c795bf9064b | ELF64 | ARM aarch64 | 0c2cf92d2d14c3b145f1d1dbeb0958b9 | libadf.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 151 | top | libaudioroute.so | 23680 | bd91024a32dc8ae52ecbe349d015bbbc45d7f4f4b2ca106ca94e73067fdadcdc | ELF64 | ARM aarch64 | b9c5b4ac80a4c07baaab3be688f743b4 | libaudioroute.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 152 | top | libaudioutils.so | 87376 | 701db8edeb4ff2bd68c3ae1b58395aa137019a293f736f561339294981ddf6df | ELF64 | ARM aarch64 | d12336db4f424ea11bf4a40e35a52dce | libaudioutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 153 | top | libbinder.so | 497936 | 82c4aa29448ced9630478ee75d79e16baec820216d52c53c158d1f83c72f449d | ELF64 | ARM aarch64 | 723b78bbfec359343c9662dbcfd3e1bc | libbinder.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 154 | top | libcamera_metadata.so | 41448 | a62b911993cd717fc2eedf79c9eadb37e9382f230cdb5e5e1811f2eda90bac1c | ELF64 | ARM aarch64 | ee9d5193a5c3a730eafd4a1f9c8b3469 | libcamera_metadata.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 155 | top | libcap.so | 27824 | f7974e8b90d2588629824db5c2715c640958571aacbe2acc37791d1cdf1af483 | ELF64 | ARM aarch64 | 71a512190c8cb0805136b1dee6d18da3 | libcap.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 156 | top | libclang_rt.scudo-aarch64-android.so | 669376 | f547b6688c32d24f6367ad2d1dd24da1fcbf1f23dd305105485d952b9643961d | ELF64 | ARM aarch64 | 452b79a9acc33c850ac06039fb154d768e3b24d8 | libclang_rt.scudo-aarch64-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 157 | top | libclang_rt.scudo_minimal-aarch64-android.so | 325720 | 0cb15911e3e5d7e5119eb4a83bd85197f9420e6540baf6f61421fc0cac8990b1 | ELF64 | ARM aarch64 | adcd1947903882f3b748dd90cf0e1856246a2e6f | libclang_rt.scudo_minimal-aarch64-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 158 | top | libclang_rt.ubsan_standalone-aarch64-android.so | 1048024 | b0423a9bf163c97f049fd3b5abfa8deef700f230e7a377771a24ca1ae65bef2c | ELF64 | ARM aarch64 | da05dbaeac45ce417e178d1de96a6b979ef2e967 | libclang_rt.ubsan_standalone-aarch64-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 159 | top | libcn-cbor.so | 19176 | f1d9601279f515875d2bb3e73b1f99887c7f3e002bac6f19d50065a98c7f313b | ELF64 | ARM aarch64 | 0432b8cd15c0bcf94a4688f39db523b4 | libcn-cbor.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 160 | top | libcodec2.so | 14896 | a7896124d24ddacfcf5e121a5973e177108a57e6e4fca9b5e199bf8d39a7e4c3 | ELF64 | ARM aarch64 | 64f95ab910af48db1bf93f04b8fefcc4 | libcodec2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 161 | top | libcrypto.so | 1149976 | 91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea | ELF64 | ARM aarch64 | 08d886a04c168aa8e7a5248a738934a7 | libcrypto.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 162 | top | libcrypto_utils.so | 15056 | da88643576d6983fb20e78fa06e179b15f0b1333e29450756350bee294b22629 | ELF64 | ARM aarch64 | ff436c671313a30fb8d81a007011d826 | libcrypto_utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 163 | top | libcurl.so | 386592 | 76a6075aebfb46e9f5ce3adc202f13d5730112e2b50aaee4e080a97369bf1024 | ELF64 | ARM aarch64 | 8a5daaa19e25c9939f6292a7159c0d46 | libcurl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 164 | top | libdiskconfig.so | 23640 | cf799a96c2cc9e30895f9130609a260c5ba7e0dd319c907edad91e8e1e33a1a1 | ELF64 | ARM aarch64 | e6425da8aa7997c538f038194bdc2e65 | libdiskconfig.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 165 | top | libdumpstateutil.so | 36840 | e5d8b39e5fcbfcd97a4635da8ad936448531b8c154b5b3b840713151c6047d9d | ELF64 | ARM aarch64 | e70ab526d55b4ce082720888472e625d | libdumpstateutil.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 166 | top | libevent.so | 312208 | 77c1d977c7bfb95166975d23471231f710e75f2564bb57cb07756838cb58445b | ELF64 | ARM aarch64 | c5e92633ec4e6250e12aa0aaea094560 | libevent.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 167 | top | libexif.so | 228272 | 5bde00a8de196950574194bea79f3c9a358bb54196174a582797218ca39d9ac2 | ELF64 | ARM aarch64 | b84743f7e373da3e2e36a1b190e3ff6c | libexif.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 168 | top | libexpat.so | 151504 | 285763179aa360dbbfc50dca60fb8f80099b89856020820ca38c58988246c621 | ELF64 | ARM aarch64 | f4d2a6c3ebf91e586e9539392c9a6f79 | libexpat.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 169 | top | libfmq.so | 19704 | 6cf6c435552e9ccce5c4d81b3e9eaff62b6df550ec91c06ab91b6a16e48469ea | ELF64 | ARM aarch64 | 31e733698b3835d814ba685c620073be | libfmq.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 170 | top | libgatekeeper.so | 28072 | 30179ec57751d3ddeeb1fbe3d1bb63e4c896d154b32eef7635f7b758ef51ac76 | ELF64 | ARM aarch64 | f84f9020c2348ba4cfb1a6d34df87e26 | libgatekeeper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 171 | top | libgui.so | 943096 | 62e6955211e022f2027d0431ad8737e9d0daaf822d25e274920bd266aef5adc3 | ELF64 | ARM aarch64 | f0cb2516e6a7777790f3af7faca429fb | libgui.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 172 | top | libhardware_legacy.so | 24256 | ccf84db8fc1ea0738d1c012d4ccca6920585869ac3eb0474cb5f73d6d487c59f | ELF64 | ARM aarch64 | 0e703428a2aaf909dc70faedd29c4017 | libhardware_legacy.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 173 | top | libhidlallocatorutils.so | 15144 | 931facd7ce41b1097d089d0dc5f40885f9da22c5df54d19f80aaea1d30f2a1a3 | ELF64 | ARM aarch64 | ed16331dfc94f0691b544376a290c4be | libhidlallocatorutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 174 | top | libhidlcache.so | 50008 | 567e0ebe083c243a4e51d81cc36c0ec517ab9bd763d306c66cb34cc1cc381548 | ELF64 | ARM aarch64 | 2f8320fc6bcf40edfae790feb9015f02 | libhidlcache.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 175 | top | libjpeg.so | 323432 | 5173cd8aa81d4bdac0e221cafa1859f1de8084e1173734e43d52aaf88b3c4e2c | ELF64 | ARM aarch64 | b4444aa8ea38f11b98a1ddd8d257bdac | libjpeg.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 176 | top | libkeymaster_messages.so | 84024 | b2b26e32b164dfeb6ade1e3a228a1822229d3763c90d0945ca2419284fbcca23 | ELF64 | ARM aarch64 | b58e288b2f04a9b30d2dc66a2e63c782 | libkeymaster_messages.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 177 | top | libkeymaster_portable.so | 248912 | fbbb7c913eba10708b88e96f44e8535a8e49241dcecdfa1350bde89fc2a48dc3 | ELF64 | ARM aarch64 | 43165b66ae73c692a1290173b46924e4 | libkeymaster_portable.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 178 | top | libldacBT_abr.so | 15048 | 490caccf5de7bc8afbd5b502e66d4289cd25c488ed6786c89ec36a2b6d192f62 | ELF64 | ARM aarch64 | 244b9f0959e9cce0b6819d89ae7c7945 | libldacBT_abr.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 179 | top | libldacBT_enc.so | 52736 | 3cafccf775ad8448348188efda1d634e783d31ea2ade2a7cb5a87006794fde2f | ELF64 | ARM aarch64 | d1a69380a436d81e75854f205000f8e7 | libldacBT_enc.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 180 | top | liblz4.so | 130512 | ad5727ec8a6563c63440769160e50c77b07f0a507f0c281a67016b0f2c1179bb | ELF64 | ARM aarch64 | b1493e36406c5f8ff0857674aa07bb17 | liblz4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 181 | top | libmedia_helper.so | 100688 | 516801e1468d67d3ef35a44e0d76405f290824ac25f2cf8aa7594593cd45bb6d | ELF64 | ARM aarch64 | 21f63db73ced1844b2c9462c6e7e21ef | libmedia_helper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 182 | top | libmedia_omx.so | 239776 | a15046a50b081547ea129bd7dddc88854d1cbb28befa2360e6646843f0b408e3 | ELF64 | ARM aarch64 | 123eecb94a1e6a1681a615052543dc82 | libmedia_omx.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 183 | top | libmemtrack.so | 16072 | fd9c6fc12cfa935594937eb7b05ae0a84e656d6cb0177972678729438139a60e | ELF64 | ARM aarch64 | ff930549609a09fbb6c9b65d6cf62b2a | libmemtrack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 184 | top | libminijail.so | 133000 | 704937b5b0f05ff2171ecd6e5c7955d4ab7f2d2d264d33b0b4b677d804220b4b | ELF64 | ARM aarch64 | 5aebb909e19b03eded6f79bccba25644 | libminijail.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 185 | top | libmkbootimg_abi_check.so | 14888 | 43ae9e3ab278014d8c4c2ac33161d26ab8c90a22b88ab57f367a42f5868f3ef9 | ELF64 | ARM aarch64 | e17c0bb54c5f93f4105aa7a9156c5624 | libmkbootimg_abi_check.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 186 | top | libnetutils.so | 36496 | 8ddd81366381e3a42b5da53343433b626b9808da538fa9391fe9f720075f903d | ELF64 | ARM aarch64 | 4c48b773ddded96135bf82a00fcea672 | libnetutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 187 | top | libnl.so | 137688 | 03052267dec9e7dba5f8936b62f32daabb599d6403c59144509abab0fc15f305 | ELF64 | ARM aarch64 | ebc39810ae7444a4bd6d9bdf2ea8ea9c | libnl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 188 | top | libpcre2.so | 315832 | 28a8b51302ad8968f412ee9a0baa0fd4b856825fb2071f1b20ccccc1fc5c00b5 | ELF64 | ARM aarch64 | d24ad5dc73550f9cd9a938366e74dd86 | libpcre2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 189 | top | libpiex.so | 104600 | 3e5a4ed9468276cca7ff045fecc1818fb1a6298f1f361643eb4e55564a58cda7 | ELF64 | ARM aarch64 | 9404643cef277b77cf9ae731a7d3ced4 | libpiex.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 190 | top | libpng.so | 214832 | 4b84cc29e4bd7e35dc885765bf9460774c21f0f3d221286a3ceedc1acf2d7867 | ELF64 | ARM aarch64 | 14e79c04ce8f90aba9b5e9a8018344a6 | libpng.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 191 | top | libpower.so | 24168 | 74d642dd3ab0b15a38e627683b700ac9c404cc3482e7892351fbe0aab75598ee | ELF64 | ARM aarch64 | cc258bb80d436386410e0622d32080f4 | libpower.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 192 | top | libprocinfo.so | 19848 | 5b20f73d2e1688252fb36dc45cfaf301b036d77ac2a090ae155da42cf468b8e5 | ELF64 | ARM aarch64 | 67aa5285b8d13e07c5220a4fcef5d813 | libprocinfo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 193 | top | libprotobuf-cpp-full.so | 1910072 | ca73719f9e8c437a0bf6a8024ff01bbefad452caa2af50b332ed6c81f0497c06 | ELF64 | ARM aarch64 | 12ab036521c51c3ffcf08da86447678b | libprotobuf-cpp-full.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 194 | top | libprotobuf-cpp-lite.so | 279768 | d670a2fdd8ccc7d45abd9fe277b732374950a4b6200db4f265f4e8e8c7a21404 | ELF64 | ARM aarch64 | d0101752c1e151dea815f86076f5696b | libprotobuf-cpp-lite.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 195 | top | libpuresoftkeymasterdevice.so | 45944 | 0f03768bf69e364c78f0bb93562cea5562540effe04e8038e224970ce5633097 | ELF64 | ARM aarch64 | e213fe6498e29882b1964b3d537cb646 | libpuresoftkeymasterdevice.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 196 | top | libradio_metadata.so | 15488 | b1065827509c25e5d882fcfacd84ab6b199726cf57cdc4b233c26fa4132cade6 | ELF64 | ARM aarch64 | 1899cca6886e31496d54b028fedb7a2d | libradio_metadata.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 197 | top | libselinux.so | 62992 | 33f39765619864dc82f6c74823cd50f3d5daba11352bd76f4fdd82d3e3598d6b | ELF64 | ARM aarch64 | 7d55f75c2bd143d6eaf3b5ad3c520aa3 | libselinux.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 198 | top | libsoftkeymasterdevice.so | 181264 | 6ea62b388c3afce96977cd24a976680464b1b71d220c3f39381111412f9094f3 | ELF64 | ARM aarch64 | 7b46146d6a04bdce9be5458d39fac7d8 | libsoftkeymasterdevice.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 199 | top | libspeexresampler.so | 24208 | 881f931dfff9d345e8379827a8e20c3012067e85485e4a020dccdb39ed265d1f | ELF64 | ARM aarch64 | ada928e9a10e068cfd033e496d330db2 | libspeexresampler.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 200 | top | libsqlite.so | 1146440 | 6fe5f7c9f7150a8bec57f8706afefe65cbab702d35a6b772724ba13c8d3ab008 | ELF64 | ARM aarch64 | 519d7a7df6563489b62b0b00618683f5 | libsqlite.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 201 | top | libssl.so | 338912 | 4d0a10b3b6936420a8a1ae4de1f95bfc01fdf96240737798746f5e662c7338e4 | ELF64 | ARM aarch64 | 77bb63a6972c865debe561183a8dad5c | libssl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 202 | top | libstagefright_bufferpool@2.0.so | 178968 | 2636d5a4272eeac18787393a8090b5db78669556fa41a9ba2d9d602229e29a72 | ELF64 | ARM aarch64 | f83cc0dfaa1cfbb66ce584878a338873 | libstagefright_bufferpool@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 203 | top | libstagefright_bufferqueue_helper.so | 560992 | ad40d8c3ab0dc245c9b67be6b4f4da49d1af95fd8831de30eb80d86465079495 | ELF64 | ARM aarch64 | 44ef0df742d942bbd48dc2e92ab3e6c2 | libstagefright_bufferqueue_helper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 204 | top | libstagefright_foundation.so | 180504 | c2e069f6d8e49712b06e00d7715cb6576056859ec57f91cf19f5e835e788136e | ELF64 | ARM aarch64 | 59490f715cda6b496134bd3ae17a9a31 | libstagefright_foundation.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 205 | top | libstagefright_omx.so | 269152 | 08d1ef7f7739d36ef6a5960ef90e942a477dcd3ce0865f6b23b6fd9361e6dec0 | ELF64 | ARM aarch64 | f6720feac17277c78c2d24b4eabdd625 | libstagefright_omx.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 206 | top | libstagefright_omx_utils.so | 23976 | bb42f6103680d05319abab0be54727734559ffc2ea1018df56d112d3854a2720 | ELF64 | ARM aarch64 | c0f3746f3b6b064fedc694eb3b0ce96c | libstagefright_omx_utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 207 | top | libstagefright_xmlparser.so | 103472 | 483bc22bb32bfba527070847075caab19bf520f81d5a2547301d0664ef0e58dd | ELF64 | ARM aarch64 | e2ee2d65e8c5166df79b5c65e686f3bc | libstagefright_xmlparser.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 208 | top | libsysutils.so | 53512 | e18b174decfa3753d7e6eb90298ec081423e2484e2469f4c8b0ce213a400700a | ELF64 | ARM aarch64 | b02981788eef5c6979d3dbe59d679c73 | libsysutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 209 | top | libtinyalsa.so | 36512 | 7ce647e20f33dee0d122972b0de769c0130d976f8bee03f458c75120672df4b9 | ELF64 | ARM aarch64 | 8b3f9c797e7692e0127ae89955915cb6 | libtinyalsa.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 210 | top | libtinyxml2.so | 79760 | b6d77142b7b477b58e6c26b3b9af7e89563984648d26728c46c1c5924435aed4 | ELF64 | ARM aarch64 | 74387bac24515ffa57db8cd831d5f409 | libtinyxml2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 211 | top | libui.so | 173936 | a53c0727cecf95c208db4251fcdcd96d0505d4c32cb7ee8ec1f8d124fd2a0cd0 | ELF64 | ARM aarch64 | 6d6fd4f1242ecfc91cb731afcbbca91c | libui.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 212 | top | libusbhost.so | 23776 | 0e83a02534292280b83bea82bec57ff3a44821bdc5740494d98c63305a2e1d10 | ELF64 | ARM aarch64 | abb6bcb29d6f7f0e81fed658e9f3500f | libusbhost.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 213 | top | libwifi-system-iface.so | 24064 | a5870c46db934161b828dfb46476c7d59edbdfe92e1c5a7f6e684fe7d2f32f92 | ELF64 | ARM aarch64 | fe26c23c99391d2f4e834515fa93f284 | libwifi-system-iface.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 214 | top | libxml2.so | 1320184 | 2d03a3b35c1e184d49352473fed58da80804681f3dc598a12b122e095b5193f6 | ELF64 | ARM aarch64 | 7d13d0b59a1b00178e009c31eae5bc7c | libxml2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 215 | top | libyuv.so | 299536 | 3724eaff1a7abe5e1d3281ba1528f590bccfa457924ca4d1337a3d04ce253dff | ELF64 | ARM aarch64 | 09c826d674c6bc990a2caa07c9f6e7fe | libyuv.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 216 | top | libziparchive.so | 54104 | 8d1541006d958a2ab734f6b014d00e1c2dc64203b33cfc9d0035f21b9cf4c041 | ELF64 | ARM aarch64 | 93e7946d04aa4b19fa962653488a323f | libziparchive.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |

Path provenance table:

| ID | Library | Live runtime path | Static stock source path |
| --- | --- | --- | --- |
| 1 | android.frameworks.cameraservice.common@2.0.so | /system/lib64/vndk-29/android.frameworks.cameraservice.common@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.cameraservice.common@2.0.so |
| 2 | android.frameworks.cameraservice.device@2.0.so | /system/lib64/vndk-29/android.frameworks.cameraservice.device@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.cameraservice.device@2.0.so |
| 3 | android.frameworks.cameraservice.service@2.0.so | /system/lib64/vndk-29/android.frameworks.cameraservice.service@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.cameraservice.service@2.0.so |
| 4 | android.frameworks.displayservice@1.0.so | /system/lib64/vndk-29/android.frameworks.displayservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.displayservice@1.0.so |
| 5 | android.frameworks.schedulerservice@1.0.so | /system/lib64/vndk-29/android.frameworks.schedulerservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.schedulerservice@1.0.so |
| 6 | android.frameworks.sensorservice@1.0.so | /system/lib64/vndk-29/android.frameworks.sensorservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.sensorservice@1.0.so |
| 7 | android.frameworks.stats@1.0.so | /system/lib64/vndk-29/android.frameworks.stats@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.stats@1.0.so |
| 8 | android.frameworks.vr.composer@1.0.so | /system/lib64/vndk-29/android.frameworks.vr.composer@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.frameworks.vr.composer@1.0.so |
| 9 | android.hardware.atrace@1.0.so | /system/lib64/vndk-29/android.hardware.atrace@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.atrace@1.0.so |
| 10 | android.hardware.audio.common@2.0.so | /system/lib64/vndk-29/android.hardware.audio.common@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.common@2.0.so |
| 11 | android.hardware.audio.common@4.0.so | /system/lib64/vndk-29/android.hardware.audio.common@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.common@4.0.so |
| 12 | android.hardware.audio.common@5.0.so | /system/lib64/vndk-29/android.hardware.audio.common@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.common@5.0.so |
| 13 | android.hardware.audio.effect@2.0.so | /system/lib64/vndk-29/android.hardware.audio.effect@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.effect@2.0.so |
| 14 | android.hardware.audio.effect@4.0.so | /system/lib64/vndk-29/android.hardware.audio.effect@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.effect@4.0.so |
| 15 | android.hardware.audio.effect@5.0.so | /system/lib64/vndk-29/android.hardware.audio.effect@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio.effect@5.0.so |
| 16 | android.hardware.audio@2.0.so | /system/lib64/vndk-29/android.hardware.audio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio@2.0.so |
| 17 | android.hardware.audio@4.0.so | /system/lib64/vndk-29/android.hardware.audio@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio@4.0.so |
| 18 | android.hardware.audio@5.0.so | /system/lib64/vndk-29/android.hardware.audio@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.audio@5.0.so |
| 19 | android.hardware.authsecret@1.0.so | /system/lib64/vndk-29/android.hardware.authsecret@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.authsecret@1.0.so |
| 20 | android.hardware.automotive.audiocontrol@1.0.so | /system/lib64/vndk-29/android.hardware.automotive.audiocontrol@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.automotive.audiocontrol@1.0.so |
| 21 | android.hardware.automotive.evs@1.0.so | /system/lib64/vndk-29/android.hardware.automotive.evs@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.automotive.evs@1.0.so |
| 22 | android.hardware.automotive.vehicle@2.0.so | /system/lib64/vndk-29/android.hardware.automotive.vehicle@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.automotive.vehicle@2.0.so |
| 23 | android.hardware.biometrics.face@1.0.so | /system/lib64/vndk-29/android.hardware.biometrics.face@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.biometrics.face@1.0.so |
| 24 | android.hardware.biometrics.fingerprint@2.1.so | /system/lib64/vndk-29/android.hardware.biometrics.fingerprint@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.biometrics.fingerprint@2.1.so |
| 25 | android.hardware.bluetooth.a2dp@1.0.so | /system/lib64/vndk-29/android.hardware.bluetooth.a2dp@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.bluetooth.a2dp@1.0.so |
| 26 | android.hardware.bluetooth.audio@2.0.so | /system/lib64/vndk-29/android.hardware.bluetooth.audio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.bluetooth.audio@2.0.so |
| 27 | android.hardware.bluetooth@1.0.so | /system/lib64/vndk-29/android.hardware.bluetooth@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.bluetooth@1.0.so |
| 28 | android.hardware.boot@1.0.so | /system/lib64/vndk-29/android.hardware.boot@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.boot@1.0.so |
| 29 | android.hardware.broadcastradio@1.0.so | /system/lib64/vndk-29/android.hardware.broadcastradio@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.broadcastradio@1.0.so |
| 30 | android.hardware.broadcastradio@1.1.so | /system/lib64/vndk-29/android.hardware.broadcastradio@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.broadcastradio@1.1.so |
| 31 | android.hardware.broadcastradio@2.0.so | /system/lib64/vndk-29/android.hardware.broadcastradio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.broadcastradio@2.0.so |
| 32 | android.hardware.camera.common@1.0.so | /system/lib64/vndk-29/android.hardware.camera.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.common@1.0.so |
| 33 | android.hardware.camera.device@1.0.so | /system/lib64/vndk-29/android.hardware.camera.device@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.device@1.0.so |
| 34 | android.hardware.camera.device@3.2.so | /system/lib64/vndk-29/android.hardware.camera.device@3.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.device@3.2.so |
| 35 | android.hardware.camera.device@3.3.so | /system/lib64/vndk-29/android.hardware.camera.device@3.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.device@3.3.so |
| 36 | android.hardware.camera.device@3.4.so | /system/lib64/vndk-29/android.hardware.camera.device@3.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.device@3.4.so |
| 37 | android.hardware.camera.device@3.5.so | /system/lib64/vndk-29/android.hardware.camera.device@3.5.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.device@3.5.so |
| 38 | android.hardware.camera.metadata@3.2.so | /system/lib64/vndk-29/android.hardware.camera.metadata@3.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.metadata@3.2.so |
| 39 | android.hardware.camera.metadata@3.3.so | /system/lib64/vndk-29/android.hardware.camera.metadata@3.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.metadata@3.3.so |
| 40 | android.hardware.camera.metadata@3.4.so | /system/lib64/vndk-29/android.hardware.camera.metadata@3.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.metadata@3.4.so |
| 41 | android.hardware.camera.provider@2.4.so | /system/lib64/vndk-29/android.hardware.camera.provider@2.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.provider@2.4.so |
| 42 | android.hardware.camera.provider@2.5.so | /system/lib64/vndk-29/android.hardware.camera.provider@2.5.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.camera.provider@2.5.so |
| 43 | android.hardware.cas.native@1.0.so | /system/lib64/vndk-29/android.hardware.cas.native@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.cas.native@1.0.so |
| 44 | android.hardware.cas@1.0.so | /system/lib64/vndk-29/android.hardware.cas@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.cas@1.0.so |
| 45 | android.hardware.cas@1.1.so | /system/lib64/vndk-29/android.hardware.cas@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.cas@1.1.so |
| 46 | android.hardware.configstore-utils.so | /system/lib64/vndk-29/android.hardware.configstore-utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.configstore-utils.so |
| 47 | android.hardware.configstore@1.0.so | /system/lib64/vndk-29/android.hardware.configstore@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.configstore@1.0.so |
| 48 | android.hardware.configstore@1.1.so | /system/lib64/vndk-29/android.hardware.configstore@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.configstore@1.1.so |
| 49 | android.hardware.confirmationui-support-lib.so | /system/lib64/vndk-29/android.hardware.confirmationui-support-lib.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.confirmationui-support-lib.so |
| 50 | android.hardware.confirmationui@1.0.so | /system/lib64/vndk-29/android.hardware.confirmationui@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.confirmationui@1.0.so |
| 51 | android.hardware.contexthub@1.0.so | /system/lib64/vndk-29/android.hardware.contexthub@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.contexthub@1.0.so |
| 52 | android.hardware.drm@1.0.so | /system/lib64/vndk-29/android.hardware.drm@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.drm@1.0.so |
| 53 | android.hardware.drm@1.1.so | /system/lib64/vndk-29/android.hardware.drm@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.drm@1.1.so |
| 54 | android.hardware.drm@1.2.so | /system/lib64/vndk-29/android.hardware.drm@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.drm@1.2.so |
| 55 | android.hardware.dumpstate@1.0.so | /system/lib64/vndk-29/android.hardware.dumpstate@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.dumpstate@1.0.so |
| 56 | android.hardware.fastboot@1.0.so | /system/lib64/vndk-29/android.hardware.fastboot@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.fastboot@1.0.so |
| 57 | android.hardware.gatekeeper@1.0.so | /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so |
| 58 | android.hardware.gnss.measurement_corrections@1.0.so | /system/lib64/vndk-29/android.hardware.gnss.measurement_corrections@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gnss.measurement_corrections@1.0.so |
| 59 | android.hardware.gnss.visibility_control@1.0.so | /system/lib64/vndk-29/android.hardware.gnss.visibility_control@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gnss.visibility_control@1.0.so |
| 60 | android.hardware.gnss@1.0.so | /system/lib64/vndk-29/android.hardware.gnss@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gnss@1.0.so |
| 61 | android.hardware.gnss@1.1.so | /system/lib64/vndk-29/android.hardware.gnss@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gnss@1.1.so |
| 62 | android.hardware.gnss@2.0.so | /system/lib64/vndk-29/android.hardware.gnss@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.gnss@2.0.so |
| 63 | android.hardware.graphics.allocator@2.0.so | /system/lib64/vndk-29/android.hardware.graphics.allocator@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.allocator@2.0.so |
| 64 | android.hardware.graphics.allocator@3.0.so | /system/lib64/vndk-29/android.hardware.graphics.allocator@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.allocator@3.0.so |
| 65 | android.hardware.graphics.bufferqueue@1.0.so | /system/lib64/vndk-29/android.hardware.graphics.bufferqueue@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.bufferqueue@1.0.so |
| 66 | android.hardware.graphics.bufferqueue@2.0.so | /system/lib64/vndk-29/android.hardware.graphics.bufferqueue@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.bufferqueue@2.0.so |
| 67 | android.hardware.graphics.composer@2.1.so | /system/lib64/vndk-29/android.hardware.graphics.composer@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.composer@2.1.so |
| 68 | android.hardware.graphics.composer@2.2.so | /system/lib64/vndk-29/android.hardware.graphics.composer@2.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.composer@2.2.so |
| 69 | android.hardware.graphics.composer@2.3.so | /system/lib64/vndk-29/android.hardware.graphics.composer@2.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.graphics.composer@2.3.so |
| 70 | android.hardware.health.storage@1.0.so | /system/lib64/vndk-29/android.hardware.health.storage@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.health.storage@1.0.so |
| 71 | android.hardware.health@1.0.so | /system/lib64/vndk-29/android.hardware.health@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.health@1.0.so |
| 72 | android.hardware.health@2.0.so | /system/lib64/vndk-29/android.hardware.health@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.health@2.0.so |
| 73 | android.hardware.input.classifier@1.0.so | /system/lib64/vndk-29/android.hardware.input.classifier@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.input.classifier@1.0.so |
| 74 | android.hardware.input.common@1.0.so | /system/lib64/vndk-29/android.hardware.input.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.input.common@1.0.so |
| 75 | android.hardware.ir@1.0.so | /system/lib64/vndk-29/android.hardware.ir@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.ir@1.0.so |
| 76 | android.hardware.keymaster@3.0.so | /system/lib64/vndk-29/android.hardware.keymaster@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.keymaster@3.0.so |
| 77 | android.hardware.keymaster@4.0.so | /system/lib64/vndk-29/android.hardware.keymaster@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.keymaster@4.0.so |
| 78 | android.hardware.light@2.0.so | /system/lib64/vndk-29/android.hardware.light@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.light@2.0.so |
| 79 | android.hardware.media.bufferpool@1.0.so | /system/lib64/vndk-29/android.hardware.media.bufferpool@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.media.bufferpool@1.0.so |
| 80 | android.hardware.media.bufferpool@2.0.so | /system/lib64/vndk-29/android.hardware.media.bufferpool@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.media.bufferpool@2.0.so |
| 81 | android.hardware.media.c2@1.0.so | /system/lib64/vndk-29/android.hardware.media.c2@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.media.c2@1.0.so |
| 82 | android.hardware.media.omx@1.0.so | /system/lib64/vndk-29/android.hardware.media.omx@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.media.omx@1.0.so |
| 83 | android.hardware.media@1.0.so | /system/lib64/vndk-29/android.hardware.media@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.media@1.0.so |
| 84 | android.hardware.memtrack@1.0.so | /system/lib64/vndk-29/android.hardware.memtrack@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.memtrack@1.0.so |
| 85 | android.hardware.neuralnetworks@1.0.so | /system/lib64/vndk-29/android.hardware.neuralnetworks@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.neuralnetworks@1.0.so |
| 86 | android.hardware.neuralnetworks@1.1.so | /system/lib64/vndk-29/android.hardware.neuralnetworks@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.neuralnetworks@1.1.so |
| 87 | android.hardware.neuralnetworks@1.2.so | /system/lib64/vndk-29/android.hardware.neuralnetworks@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.neuralnetworks@1.2.so |
| 88 | android.hardware.nfc@1.0.so | /system/lib64/vndk-29/android.hardware.nfc@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.nfc@1.0.so |
| 89 | android.hardware.nfc@1.1.so | /system/lib64/vndk-29/android.hardware.nfc@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.nfc@1.1.so |
| 90 | android.hardware.nfc@1.2.so | /system/lib64/vndk-29/android.hardware.nfc@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.nfc@1.2.so |
| 91 | android.hardware.oemlock@1.0.so | /system/lib64/vndk-29/android.hardware.oemlock@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.oemlock@1.0.so |
| 92 | android.hardware.power.stats@1.0.so | /system/lib64/vndk-29/android.hardware.power.stats@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.power.stats@1.0.so |
| 93 | android.hardware.power@1.0.so | /system/lib64/vndk-29/android.hardware.power@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.power@1.0.so |
| 94 | android.hardware.power@1.1.so | /system/lib64/vndk-29/android.hardware.power@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.power@1.1.so |
| 95 | android.hardware.power@1.2.so | /system/lib64/vndk-29/android.hardware.power@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.power@1.2.so |
| 96 | android.hardware.power@1.3.so | /system/lib64/vndk-29/android.hardware.power@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.power@1.3.so |
| 97 | android.hardware.radio.config@1.0.so | /system/lib64/vndk-29/android.hardware.radio.config@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio.config@1.0.so |
| 98 | android.hardware.radio.config@1.1.so | /system/lib64/vndk-29/android.hardware.radio.config@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio.config@1.1.so |
| 99 | android.hardware.radio.config@1.2.so | /system/lib64/vndk-29/android.hardware.radio.config@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio.config@1.2.so |
| 100 | android.hardware.radio.deprecated@1.0.so | /system/lib64/vndk-29/android.hardware.radio.deprecated@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio.deprecated@1.0.so |
| 101 | android.hardware.radio@1.0.so | /system/lib64/vndk-29/android.hardware.radio@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio@1.0.so |
| 102 | android.hardware.radio@1.1.so | /system/lib64/vndk-29/android.hardware.radio@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio@1.1.so |
| 103 | android.hardware.radio@1.2.so | /system/lib64/vndk-29/android.hardware.radio@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio@1.2.so |
| 104 | android.hardware.radio@1.3.so | /system/lib64/vndk-29/android.hardware.radio@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio@1.3.so |
| 105 | android.hardware.radio@1.4.so | /system/lib64/vndk-29/android.hardware.radio@1.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.radio@1.4.so |
| 106 | android.hardware.secure_element@1.0.so | /system/lib64/vndk-29/android.hardware.secure_element@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.secure_element@1.0.so |
| 107 | android.hardware.secure_element@1.1.so | /system/lib64/vndk-29/android.hardware.secure_element@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.secure_element@1.1.so |
| 108 | android.hardware.sensors@1.0.so | /system/lib64/vndk-29/android.hardware.sensors@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.sensors@1.0.so |
| 109 | android.hardware.sensors@2.0.so | /system/lib64/vndk-29/android.hardware.sensors@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.sensors@2.0.so |
| 110 | android.hardware.soundtrigger@2.0-core.so | /system/lib64/vndk-29/android.hardware.soundtrigger@2.0-core.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.soundtrigger@2.0-core.so |
| 111 | android.hardware.soundtrigger@2.0.so | /system/lib64/vndk-29/android.hardware.soundtrigger@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.soundtrigger@2.0.so |
| 112 | android.hardware.soundtrigger@2.1.so | /system/lib64/vndk-29/android.hardware.soundtrigger@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.soundtrigger@2.1.so |
| 113 | android.hardware.soundtrigger@2.2.so | /system/lib64/vndk-29/android.hardware.soundtrigger@2.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.soundtrigger@2.2.so |
| 114 | android.hardware.tetheroffload.config@1.0.so | /system/lib64/vndk-29/android.hardware.tetheroffload.config@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.tetheroffload.config@1.0.so |
| 115 | android.hardware.tetheroffload.control@1.0.so | /system/lib64/vndk-29/android.hardware.tetheroffload.control@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.tetheroffload.control@1.0.so |
| 116 | android.hardware.thermal@1.0.so | /system/lib64/vndk-29/android.hardware.thermal@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.thermal@1.0.so |
| 117 | android.hardware.thermal@1.1.so | /system/lib64/vndk-29/android.hardware.thermal@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.thermal@1.1.so |
| 118 | android.hardware.thermal@2.0.so | /system/lib64/vndk-29/android.hardware.thermal@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.thermal@2.0.so |
| 119 | android.hardware.tv.cec@1.0.so | /system/lib64/vndk-29/android.hardware.tv.cec@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.tv.cec@1.0.so |
| 120 | android.hardware.tv.cec@2.0.so | /system/lib64/vndk-29/android.hardware.tv.cec@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.tv.cec@2.0.so |
| 121 | android.hardware.tv.input@1.0.so | /system/lib64/vndk-29/android.hardware.tv.input@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.tv.input@1.0.so |
| 122 | android.hardware.usb.gadget@1.0.so | /system/lib64/vndk-29/android.hardware.usb.gadget@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.usb.gadget@1.0.so |
| 123 | android.hardware.usb@1.0.so | /system/lib64/vndk-29/android.hardware.usb@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.usb@1.0.so |
| 124 | android.hardware.usb@1.1.so | /system/lib64/vndk-29/android.hardware.usb@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.usb@1.1.so |
| 125 | android.hardware.usb@1.2.so | /system/lib64/vndk-29/android.hardware.usb@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.usb@1.2.so |
| 126 | android.hardware.vibrator@1.0.so | /system/lib64/vndk-29/android.hardware.vibrator@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.vibrator@1.0.so |
| 127 | android.hardware.vibrator@1.1.so | /system/lib64/vndk-29/android.hardware.vibrator@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.vibrator@1.1.so |
| 128 | android.hardware.vibrator@1.2.so | /system/lib64/vndk-29/android.hardware.vibrator@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.vibrator@1.2.so |
| 129 | android.hardware.vibrator@1.3.so | /system/lib64/vndk-29/android.hardware.vibrator@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.vibrator@1.3.so |
| 130 | android.hardware.vr@1.0.so | /system/lib64/vndk-29/android.hardware.vr@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.vr@1.0.so |
| 131 | android.hardware.weaver@1.0.so | /system/lib64/vndk-29/android.hardware.weaver@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.weaver@1.0.so |
| 132 | android.hardware.wifi.hostapd@1.0.so | /system/lib64/vndk-29/android.hardware.wifi.hostapd@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.hostapd@1.0.so |
| 133 | android.hardware.wifi.hostapd@1.1.so | /system/lib64/vndk-29/android.hardware.wifi.hostapd@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.hostapd@1.1.so |
| 134 | android.hardware.wifi.offload@1.0.so | /system/lib64/vndk-29/android.hardware.wifi.offload@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.offload@1.0.so |
| 135 | android.hardware.wifi.supplicant@1.0.so | /system/lib64/vndk-29/android.hardware.wifi.supplicant@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.supplicant@1.0.so |
| 136 | android.hardware.wifi.supplicant@1.1.so | /system/lib64/vndk-29/android.hardware.wifi.supplicant@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.supplicant@1.1.so |
| 137 | android.hardware.wifi.supplicant@1.2.so | /system/lib64/vndk-29/android.hardware.wifi.supplicant@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi.supplicant@1.2.so |
| 138 | android.hardware.wifi@1.0.so | /system/lib64/vndk-29/android.hardware.wifi@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi@1.0.so |
| 139 | android.hardware.wifi@1.1.so | /system/lib64/vndk-29/android.hardware.wifi@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi@1.1.so |
| 140 | android.hardware.wifi@1.2.so | /system/lib64/vndk-29/android.hardware.wifi@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi@1.2.so |
| 141 | android.hardware.wifi@1.3.so | /system/lib64/vndk-29/android.hardware.wifi@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hardware.wifi@1.3.so |
| 142 | android.hidl.allocator@1.0.so | /system/lib64/vndk-29/android.hidl.allocator@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hidl.allocator@1.0.so |
| 143 | android.hidl.memory.block@1.0.so | /system/lib64/vndk-29/android.hidl.memory.block@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hidl.memory.block@1.0.so |
| 144 | android.hidl.token@1.0-utils.so | /system/lib64/vndk-29/android.hidl.token@1.0-utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hidl.token@1.0-utils.so |
| 145 | android.hidl.token@1.0.so | /system/lib64/vndk-29/android.hidl.token@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.hidl.token@1.0.so |
| 146 | android.system.net.netd@1.0.so | /system/lib64/vndk-29/android.system.net.netd@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.system.net.netd@1.0.so |
| 147 | android.system.net.netd@1.1.so | /system/lib64/vndk-29/android.system.net.netd@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.system.net.netd@1.1.so |
| 148 | android.system.suspend@1.0.so | /system/lib64/vndk-29/android.system.suspend@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.system.suspend@1.0.so |
| 149 | android.system.wifi.keystore@1.0.so | /system/lib64/vndk-29/android.system.wifi.keystore@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/android.system.wifi.keystore@1.0.so |
| 150 | libadf.so | /system/lib64/vndk-29/libadf.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libadf.so |
| 151 | libaudioroute.so | /system/lib64/vndk-29/libaudioroute.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libaudioroute.so |
| 152 | libaudioutils.so | /system/lib64/vndk-29/libaudioutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libaudioutils.so |
| 153 | libbinder.so | /system/lib64/vndk-29/libbinder.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libbinder.so |
| 154 | libcamera_metadata.so | /system/lib64/vndk-29/libcamera_metadata.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcamera_metadata.so |
| 155 | libcap.so | /system/lib64/vndk-29/libcap.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcap.so |
| 156 | libclang_rt.scudo-aarch64-android.so | /system/lib64/vndk-29/libclang_rt.scudo-aarch64-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libclang_rt.scudo-aarch64-android.so |
| 157 | libclang_rt.scudo_minimal-aarch64-android.so | /system/lib64/vndk-29/libclang_rt.scudo_minimal-aarch64-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libclang_rt.scudo_minimal-aarch64-android.so |
| 158 | libclang_rt.ubsan_standalone-aarch64-android.so | /system/lib64/vndk-29/libclang_rt.ubsan_standalone-aarch64-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libclang_rt.ubsan_standalone-aarch64-android.so |
| 159 | libcn-cbor.so | /system/lib64/vndk-29/libcn-cbor.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcn-cbor.so |
| 160 | libcodec2.so | /system/lib64/vndk-29/libcodec2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcodec2.so |
| 161 | libcrypto.so | /system/lib64/vndk-29/libcrypto.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcrypto.so |
| 162 | libcrypto_utils.so | /system/lib64/vndk-29/libcrypto_utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcrypto_utils.so |
| 163 | libcurl.so | /system/lib64/vndk-29/libcurl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libcurl.so |
| 164 | libdiskconfig.so | /system/lib64/vndk-29/libdiskconfig.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libdiskconfig.so |
| 165 | libdumpstateutil.so | /system/lib64/vndk-29/libdumpstateutil.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libdumpstateutil.so |
| 166 | libevent.so | /system/lib64/vndk-29/libevent.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libevent.so |
| 167 | libexif.so | /system/lib64/vndk-29/libexif.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libexif.so |
| 168 | libexpat.so | /system/lib64/vndk-29/libexpat.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libexpat.so |
| 169 | libfmq.so | /system/lib64/vndk-29/libfmq.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libfmq.so |
| 170 | libgatekeeper.so | /system/lib64/vndk-29/libgatekeeper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libgatekeeper.so |
| 171 | libgui.so | /system/lib64/vndk-29/libgui.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libgui.so |
| 172 | libhardware_legacy.so | /system/lib64/vndk-29/libhardware_legacy.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libhardware_legacy.so |
| 173 | libhidlallocatorutils.so | /system/lib64/vndk-29/libhidlallocatorutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libhidlallocatorutils.so |
| 174 | libhidlcache.so | /system/lib64/vndk-29/libhidlcache.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libhidlcache.so |
| 175 | libjpeg.so | /system/lib64/vndk-29/libjpeg.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libjpeg.so |
| 176 | libkeymaster_messages.so | /system/lib64/vndk-29/libkeymaster_messages.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libkeymaster_messages.so |
| 177 | libkeymaster_portable.so | /system/lib64/vndk-29/libkeymaster_portable.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libkeymaster_portable.so |
| 178 | libldacBT_abr.so | /system/lib64/vndk-29/libldacBT_abr.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libldacBT_abr.so |
| 179 | libldacBT_enc.so | /system/lib64/vndk-29/libldacBT_enc.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libldacBT_enc.so |
| 180 | liblz4.so | /system/lib64/vndk-29/liblz4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/liblz4.so |
| 181 | libmedia_helper.so | /system/lib64/vndk-29/libmedia_helper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libmedia_helper.so |
| 182 | libmedia_omx.so | /system/lib64/vndk-29/libmedia_omx.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libmedia_omx.so |
| 183 | libmemtrack.so | /system/lib64/vndk-29/libmemtrack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libmemtrack.so |
| 184 | libminijail.so | /system/lib64/vndk-29/libminijail.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libminijail.so |
| 185 | libmkbootimg_abi_check.so | /system/lib64/vndk-29/libmkbootimg_abi_check.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libmkbootimg_abi_check.so |
| 186 | libnetutils.so | /system/lib64/vndk-29/libnetutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libnetutils.so |
| 187 | libnl.so | /system/lib64/vndk-29/libnl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libnl.so |
| 188 | libpcre2.so | /system/lib64/vndk-29/libpcre2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libpcre2.so |
| 189 | libpiex.so | /system/lib64/vndk-29/libpiex.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libpiex.so |
| 190 | libpng.so | /system/lib64/vndk-29/libpng.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libpng.so |
| 191 | libpower.so | /system/lib64/vndk-29/libpower.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libpower.so |
| 192 | libprocinfo.so | /system/lib64/vndk-29/libprocinfo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libprocinfo.so |
| 193 | libprotobuf-cpp-full.so | /system/lib64/vndk-29/libprotobuf-cpp-full.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libprotobuf-cpp-full.so |
| 194 | libprotobuf-cpp-lite.so | /system/lib64/vndk-29/libprotobuf-cpp-lite.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libprotobuf-cpp-lite.so |
| 195 | libpuresoftkeymasterdevice.so | /system/lib64/vndk-29/libpuresoftkeymasterdevice.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libpuresoftkeymasterdevice.so |
| 196 | libradio_metadata.so | /system/lib64/vndk-29/libradio_metadata.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libradio_metadata.so |
| 197 | libselinux.so | /system/lib64/vndk-29/libselinux.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libselinux.so |
| 198 | libsoftkeymasterdevice.so | /system/lib64/vndk-29/libsoftkeymasterdevice.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libsoftkeymasterdevice.so |
| 199 | libspeexresampler.so | /system/lib64/vndk-29/libspeexresampler.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libspeexresampler.so |
| 200 | libsqlite.so | /system/lib64/vndk-29/libsqlite.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libsqlite.so |
| 201 | libssl.so | /system/lib64/vndk-29/libssl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libssl.so |
| 202 | libstagefright_bufferpool@2.0.so | /system/lib64/vndk-29/libstagefright_bufferpool@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_bufferpool@2.0.so |
| 203 | libstagefright_bufferqueue_helper.so | /system/lib64/vndk-29/libstagefright_bufferqueue_helper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_bufferqueue_helper.so |
| 204 | libstagefright_foundation.so | /system/lib64/vndk-29/libstagefright_foundation.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_foundation.so |
| 205 | libstagefright_omx.so | /system/lib64/vndk-29/libstagefright_omx.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_omx.so |
| 206 | libstagefright_omx_utils.so | /system/lib64/vndk-29/libstagefright_omx_utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_omx_utils.so |
| 207 | libstagefright_xmlparser.so | /system/lib64/vndk-29/libstagefright_xmlparser.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libstagefright_xmlparser.so |
| 208 | libsysutils.so | /system/lib64/vndk-29/libsysutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libsysutils.so |
| 209 | libtinyalsa.so | /system/lib64/vndk-29/libtinyalsa.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libtinyalsa.so |
| 210 | libtinyxml2.so | /system/lib64/vndk-29/libtinyxml2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libtinyxml2.so |
| 211 | libui.so | /system/lib64/vndk-29/libui.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libui.so |
| 212 | libusbhost.so | /system/lib64/vndk-29/libusbhost.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libusbhost.so |
| 213 | libwifi-system-iface.so | /system/lib64/vndk-29/libwifi-system-iface.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libwifi-system-iface.so |
| 214 | libxml2.so | /system/lib64/vndk-29/libxml2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libxml2.so |
| 215 | libyuv.so | /system/lib64/vndk-29/libyuv.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libyuv.so |
| 216 | libziparchive.so | /system/lib64/vndk-29/libziparchive.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-29/libziparchive.so |

DT_NEEDED table:

| ID | Library | DT_NEEDED |
| --- | --- | --- |
| 1 | android.frameworks.cameraservice.common@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 2 | android.frameworks.cameraservice.device@2.0.so | android.frameworks.cameraservice.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 3 | android.frameworks.cameraservice.service@2.0.so | android.frameworks.cameraservice.common@2.0.so<br>android.frameworks.cameraservice.device@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 4 | android.frameworks.displayservice@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 5 | android.frameworks.schedulerservice@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 6 | android.frameworks.sensorservice@1.0.so | android.hardware.sensors@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 7 | android.frameworks.stats@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 8 | android.frameworks.vr.composer@1.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.composer@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 9 | android.hardware.atrace@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 10 | android.hardware.audio.common@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 11 | android.hardware.audio.common@4.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 12 | android.hardware.audio.common@5.0.so | android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 13 | android.hardware.audio.effect@2.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 14 | android.hardware.audio.effect@4.0.so | android.hardware.audio.common@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 15 | android.hardware.audio.effect@5.0.so | android.hardware.audio.common@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 16 | android.hardware.audio@2.0.so | android.hardware.audio.common@2.0.so<br>android.hardware.audio.effect@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 17 | android.hardware.audio@4.0.so | android.hardware.audio.common@4.0.so<br>android.hardware.audio.effect@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 18 | android.hardware.audio@5.0.so | android.hardware.audio.common@5.0.so<br>android.hardware.audio.effect@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 19 | android.hardware.authsecret@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 20 | android.hardware.automotive.audiocontrol@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 21 | android.hardware.automotive.evs@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 22 | android.hardware.automotive.vehicle@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 23 | android.hardware.biometrics.face@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 24 | android.hardware.biometrics.fingerprint@2.1.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 25 | android.hardware.bluetooth.a2dp@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 26 | android.hardware.bluetooth.audio@2.0.so | android.hardware.audio.common@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 27 | android.hardware.bluetooth@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 28 | android.hardware.boot@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 29 | android.hardware.broadcastradio@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 30 | android.hardware.broadcastradio@1.1.so | android.hardware.broadcastradio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 31 | android.hardware.broadcastradio@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 32 | android.hardware.camera.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 33 | android.hardware.camera.device@1.0.so | android.hardware.camera.common@1.0.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 34 | android.hardware.camera.device@3.2.so | android.hardware.camera.common@1.0.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 35 | android.hardware.camera.device@3.3.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 36 | android.hardware.camera.device@3.4.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.device@3.3.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 37 | android.hardware.camera.device@3.5.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.device@3.3.so<br>android.hardware.camera.device@3.4.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 38 | android.hardware.camera.metadata@3.2.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 39 | android.hardware.camera.metadata@3.3.so | android.hardware.camera.metadata@3.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 40 | android.hardware.camera.metadata@3.4.so | android.hardware.camera.metadata@3.2.so<br>android.hardware.camera.metadata@3.3.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 41 | android.hardware.camera.provider@2.4.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@1.0.so<br>android.hardware.camera.device@3.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 42 | android.hardware.camera.provider@2.5.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.provider@2.4.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 43 | android.hardware.cas.native@1.0.so | android.hardware.cas@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 44 | android.hardware.cas@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 45 | android.hardware.cas@1.1.so | android.hardware.cas@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 46 | android.hardware.configstore-utils.so | android.hardware.configstore@1.0.so<br>android.hardware.configstore@1.1.so<br>libbase.so<br>libhidlbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 47 | android.hardware.configstore@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 48 | android.hardware.configstore@1.1.so | android.hardware.configstore@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 49 | android.hardware.confirmationui-support-lib.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 50 | android.hardware.confirmationui@1.0.so | android.hardware.keymaster@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 51 | android.hardware.contexthub@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 52 | android.hardware.drm@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 53 | android.hardware.drm@1.1.so | android.hardware.drm@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 54 | android.hardware.drm@1.2.so | android.hardware.drm@1.0.so<br>android.hardware.drm@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 55 | android.hardware.dumpstate@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 56 | android.hardware.fastboot@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 57 | android.hardware.gatekeeper@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 58 | android.hardware.gnss.measurement_corrections@1.0.so | android.hardware.gnss@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 59 | android.hardware.gnss.visibility_control@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 60 | android.hardware.gnss@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 61 | android.hardware.gnss@1.1.so | android.hardware.gnss@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 62 | android.hardware.gnss@2.0.so | android.hardware.gnss.measurement_corrections@1.0.so<br>android.hardware.gnss.visibility_control@1.0.so<br>android.hardware.gnss@1.0.so<br>android.hardware.gnss@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 63 | android.hardware.graphics.allocator@2.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.mapper@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 64 | android.hardware.graphics.allocator@3.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.mapper@3.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 65 | android.hardware.graphics.bufferqueue@1.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.media@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 66 | android.hardware.graphics.bufferqueue@2.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 67 | android.hardware.graphics.composer@2.1.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 68 | android.hardware.graphics.composer@2.2.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.composer@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 69 | android.hardware.graphics.composer@2.3.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.composer@2.1.so<br>android.hardware.graphics.composer@2.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 70 | android.hardware.health.storage@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 71 | android.hardware.health@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 72 | android.hardware.health@2.0.so | android.hardware.health@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 73 | android.hardware.input.classifier@1.0.so | android.hardware.input.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 74 | android.hardware.input.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 75 | android.hardware.ir@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 76 | android.hardware.keymaster@3.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 77 | android.hardware.keymaster@4.0.so | android.hardware.keymaster@3.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 78 | android.hardware.light@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 79 | android.hardware.media.bufferpool@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 80 | android.hardware.media.bufferpool@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 81 | android.hardware.media.c2@1.0.so | android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.media.bufferpool@2.0.so<br>android.hardware.media.omx@1.0.so<br>android.hardware.media@1.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 82 | android.hardware.media.omx@1.0.so | android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.common@1.0.so<br>android.hardware.media@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 83 | android.hardware.media@1.0.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 84 | android.hardware.memtrack@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 85 | android.hardware.neuralnetworks@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 86 | android.hardware.neuralnetworks@1.1.so | android.hardware.neuralnetworks@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 87 | android.hardware.neuralnetworks@1.2.so | android.hardware.neuralnetworks@1.0.so<br>android.hardware.neuralnetworks@1.1.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 88 | android.hardware.nfc@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 89 | android.hardware.nfc@1.1.so | android.hardware.nfc@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 90 | android.hardware.nfc@1.2.so | android.hardware.nfc@1.0.so<br>android.hardware.nfc@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 91 | android.hardware.oemlock@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 92 | android.hardware.power.stats@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 93 | android.hardware.power@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 94 | android.hardware.power@1.1.so | android.hardware.power@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 95 | android.hardware.power@1.2.so | android.hardware.power@1.0.so<br>android.hardware.power@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 96 | android.hardware.power@1.3.so | android.hardware.power@1.0.so<br>android.hardware.power@1.1.so<br>android.hardware.power@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 97 | android.hardware.radio.config@1.0.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 98 | android.hardware.radio.config@1.1.so | android.hardware.radio.config@1.0.so<br>android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 99 | android.hardware.radio.config@1.2.so | android.hardware.radio.config@1.0.so<br>android.hardware.radio.config@1.1.so<br>android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 100 | android.hardware.radio.deprecated@1.0.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 101 | android.hardware.radio@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 102 | android.hardware.radio@1.1.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 103 | android.hardware.radio@1.2.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 104 | android.hardware.radio@1.3.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>android.hardware.radio@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 105 | android.hardware.radio@1.4.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>android.hardware.radio@1.2.so<br>android.hardware.radio@1.3.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 106 | android.hardware.secure_element@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 107 | android.hardware.secure_element@1.1.so | android.hardware.secure_element@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 108 | android.hardware.sensors@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 109 | android.hardware.sensors@2.0.so | android.hardware.sensors@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 110 | android.hardware.soundtrigger@2.0-core.so | libhidlbase.so<br>libhidltransport.so<br>liblog.so<br>libutils.so<br>libhardware.so<br>android.hardware.soundtrigger@2.0.so<br>android.hardware.audio.common@2.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 111 | android.hardware.soundtrigger@2.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 112 | android.hardware.soundtrigger@2.1.so | android.hardware.audio.common@2.0.so<br>android.hardware.soundtrigger@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 113 | android.hardware.soundtrigger@2.2.so | android.hardware.audio.common@2.0.so<br>android.hardware.soundtrigger@2.0.so<br>android.hardware.soundtrigger@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 114 | android.hardware.tetheroffload.config@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 115 | android.hardware.tetheroffload.control@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 116 | android.hardware.thermal@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 117 | android.hardware.thermal@1.1.so | android.hardware.thermal@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 118 | android.hardware.thermal@2.0.so | android.hardware.thermal@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 119 | android.hardware.tv.cec@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 120 | android.hardware.tv.cec@2.0.so | android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 121 | android.hardware.tv.input@1.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 122 | android.hardware.usb.gadget@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 123 | android.hardware.usb@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 124 | android.hardware.usb@1.1.so | android.hardware.usb@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 125 | android.hardware.usb@1.2.so | android.hardware.usb@1.0.so<br>android.hardware.usb@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 126 | android.hardware.vibrator@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 127 | android.hardware.vibrator@1.1.so | android.hardware.vibrator@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 128 | android.hardware.vibrator@1.2.so | android.hardware.vibrator@1.0.so<br>android.hardware.vibrator@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 129 | android.hardware.vibrator@1.3.so | android.hardware.vibrator@1.0.so<br>android.hardware.vibrator@1.1.so<br>android.hardware.vibrator@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 130 | android.hardware.vr@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 131 | android.hardware.weaver@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 132 | android.hardware.wifi.hostapd@1.0.so | android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 133 | android.hardware.wifi.hostapd@1.1.so | android.hardware.wifi.hostapd@1.0.so<br>android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 134 | android.hardware.wifi.offload@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 135 | android.hardware.wifi.supplicant@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 136 | android.hardware.wifi.supplicant@1.1.so | android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 137 | android.hardware.wifi.supplicant@1.2.so | android.hardware.wifi.supplicant@1.0.so<br>android.hardware.wifi.supplicant@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 138 | android.hardware.wifi@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 139 | android.hardware.wifi@1.1.so | android.hardware.wifi@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 140 | android.hardware.wifi@1.2.so | android.hardware.wifi@1.0.so<br>android.hardware.wifi@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 141 | android.hardware.wifi@1.3.so | android.hardware.wifi@1.0.so<br>android.hardware.wifi@1.1.so<br>android.hardware.wifi@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 142 | android.hidl.allocator@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 143 | android.hidl.memory.block@1.0.so | android.hidl.memory.token@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 144 | android.hidl.token@1.0-utils.so | android.hidl.token@1.0.so<br>libbinder.so<br>libhidlbase.so<br>liblog.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 145 | android.hidl.token@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 146 | android.system.net.netd@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 147 | android.system.net.netd@1.1.so | android.system.net.netd@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 148 | android.system.suspend@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 149 | android.system.wifi.keystore@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 150 | libadf.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 151 | libaudioroute.so | liblog.so<br>libcutils.so<br>libutils.so<br>libexpat.so<br>libtinyalsa.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 152 | libaudioutils.so | libcutils.so<br>liblog.so<br>libspeexresampler.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 153 | libbinder.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 154 | libcamera_metadata.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 155 | libcap.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 156 | libclang_rt.scudo-aarch64-android.so | libc.so<br>libdl.so<br>liblog.so |
| 157 | libclang_rt.scudo_minimal-aarch64-android.so | libc.so<br>libdl.so<br>liblog.so |
| 158 | libclang_rt.ubsan_standalone-aarch64-android.so | libc.so<br>libdl.so<br>liblog.so |
| 159 | libcn-cbor.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 160 | libcodec2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 161 | libcrypto.so | libc.so<br>libm.so<br>libdl.so |
| 162 | libcrypto_utils.so | libcrypto.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 163 | libcurl.so | libcrypto.so<br>libssl.so<br>libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 164 | libdiskconfig.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 165 | libdumpstateutil.so | libbase.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 166 | libevent.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 167 | libexif.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 168 | libexpat.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 169 | libfmq.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 170 | libgatekeeper.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 171 | libgui.so | libhardware.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hidl.token@1.0-utils.so<br>libbase.so<br>libbinder.so<br>libcutils.so<br>libEGL.so<br>libGLESv2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libnativewindow.so<br>libsync.so<br>libui.so<br>libutils.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 172 | libhardware_legacy.so | libbase.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>android.system.suspend@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 173 | libhidlallocatorutils.so | libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 174 | libhidlcache.so | libbase.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhwbinder.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>android.hidl.memory.block@1.0.so<br>android.hidl.memory.token@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 175 | libjpeg.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 176 | libkeymaster_messages.so | libc.so<br>libm.so<br>libdl.so |
| 177 | libkeymaster_portable.so | libcrypto.so<br>libc.so<br>libm.so<br>libdl.so |
| 178 | libldacBT_abr.so | libldacBT_enc.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 179 | libldacBT_enc.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 180 | liblz4.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 181 | libmedia_helper.so | libutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 182 | libmedia_omx.so | android.hidl.token@1.0-utils.so<br>android.hardware.media.omx@1.0.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>liblog.so<br>libstagefright_foundation.so<br>libui.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 183 | libmemtrack.so | libhardware.so<br>liblog.so<br>libbase.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>libutils.so<br>android.hardware.memtrack@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 184 | libminijail.so | libcap.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 185 | libmkbootimg_abi_check.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 186 | libnetutils.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 187 | libnl.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 188 | libpcre2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 189 | libpiex.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 190 | libpng.so | libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 191 | libpower.so | libbase.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>android.system.suspend@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 192 | libprocinfo.so | libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 193 | libprotobuf-cpp-full.so | libz.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 194 | libprotobuf-cpp-lite.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 195 | libpuresoftkeymasterdevice.so | libkeymaster_messages.so<br>libkeymaster_portable.so<br>liblog.so<br>libcrypto.so<br>libcutils.so<br>libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 196 | libradio_metadata.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 197 | libselinux.so | libcrypto.so<br>liblog.so<br>libpcre2.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 198 | libsoftkeymasterdevice.so | libkeymaster_messages.so<br>libkeymaster_portable.so<br>liblog.so<br>libbase.so<br>libcrypto.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 199 | libspeexresampler.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 200 | libsqlite.so | liblog.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 201 | libssl.so | libcrypto.so<br>libc.so<br>libm.so<br>libdl.so |
| 202 | libstagefright_bufferpool@2.0.so | libcutils.so<br>libfmq.so<br>libhidlbase.so<br>libhwbinder.so<br>libhidltransport.so<br>liblog.so<br>libutils.so<br>android.hardware.media.bufferpool@2.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 203 | libstagefright_bufferqueue_helper.so | libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhidltransport.so<br>liblog.so<br>libstagefright_foundation.so<br>libui.so<br>libutils.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hidl.token@1.0-utils.so<br>libbase.so<br>libEGL.so<br>libhwbinder.so<br>libnativewindow.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 204 | libstagefright_foundation.so | libbinder.so<br>libutils.so<br>libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 205 | libstagefright_omx.so | libbase.so<br>libbinder.so<br>libmedia_omx.so<br>libutils.so<br>liblog.so<br>libui.so<br>libcutils.so<br>libstagefright_foundation.so<br>libstagefright_bufferqueue_helper.so<br>libstagefright_xmlparser.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhidltransport.so<br>libvndksupport.so<br>android.hardware.media.omx@1.0.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 206 | libstagefright_omx_utils.so | libmedia_omx.so<br>libstagefright_foundation.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 207 | libstagefright_xmlparser.so | libbase.so<br>libexpat.so<br>liblog.so<br>libstagefright_omx_utils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 208 | libsysutils.so | libbase.so<br>libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 209 | libtinyalsa.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 210 | libtinyxml2.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 211 | libui.so | android.hardware.graphics.allocator@2.0.so<br>android.hardware.graphics.allocator@3.0.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.mapper@2.0.so<br>android.hardware.graphics.mapper@2.1.so<br>android.hardware.graphics.mapper@3.0.so<br>libbase.so<br>libcutils.so<br>libhardware.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>libsync.so<br>libutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 212 | libusbhost.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 213 | libwifi-system-iface.so | libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 214 | libxml2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 215 | libyuv.so | libjpeg.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 216 | libziparchive.so | liblog.so<br>libbase.so<br>libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |

10.3.6 Complete VNDK-29 library list — 32-bit set

  Runtime directory:
    /system/lib/vndk-29

  Row count:
    216

  Table use:
    The local ID column is stable inside this ABI subsection. Identity, path
    provenance, and DT_NEEDED rows share the same local ID. SHA256 is the
    static/live-matched payload hash. Static/live equivalence is carried per row.

Identity and equivalence table:

| ID | Scope | Library | Size | SHA256 | ELF | Machine | Build ID | SONAME | RPATH/RUNPATH | Static/live equivalence | Mode / UID:GID | SELinux context | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | top | android.frameworks.cameraservice.common@2.0.so | 15068 | f9d05746a0a8ca506f5a012345adaad980a02d0886f44f40fb484674f5593500 | ELF32 | ARM | 7c476c2b2336d64756cb7a5069208010 | android.frameworks.cameraservice.common@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 2 | top | android.frameworks.cameraservice.device@2.0.so | 146388 | 20cede0da9b4a63622b90451101e90ebf5a618db1c8e38736159334b3b6d72dc | ELF32 | ARM | f1620d62aaeac003ecdfb914a397bef4 | android.frameworks.cameraservice.device@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 3 | top | android.frameworks.cameraservice.service@2.0.so | 111380 | f3a741cd4ae0b42cfec69f3b114124102dc29936566f3f5087cd7c30715a808b | ELF32 | ARM | 4a006a2ea14a7c18bd24161cd5c73101 | android.frameworks.cameraservice.service@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 4 | top | android.frameworks.displayservice@1.0.so | 125268 | 91f1af9db8c36d939da6241647faf35c9536643ed9c6fb92335d1273168fdf9a | ELF32 | ARM | aa3bc2a641e0054e8884eeb3c7dc020a | android.frameworks.displayservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 5 | top | android.frameworks.schedulerservice@1.0.so | 61204 | 8d496bfae8526eda05a4547db276abedca5accb17af75e37ef931c0adc639bb9 | ELF32 | ARM | 21a275034985357236e58f4c3cc28b80 | android.frameworks.schedulerservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 6 | top | android.frameworks.sensorservice@1.0.so | 163088 | ea4267c57821c17a2b21fd7d5eb8e086cbb2a10ab4fa16be9515f3bf7ee261e1 | ELF32 | ARM | e206935e3477de0345acab66567cb646 | android.frameworks.sensorservice@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 7 | top | android.frameworks.stats@1.0.so | 87892 | 7488ddb3fe51df4cec746723a92d72ff3095dfdf086dcde3334fecde51478c09 | ELF32 | ARM | f395da287fabf63784deb9d47a6ab3d3 | android.frameworks.stats@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 8 | top | android.frameworks.vr.composer@1.0.so | 93084 | cdb641473759d087d5112bd45de15b9091fa74f90ca44fc24bf21cd0d02d6794 | ELF32 | ARM | d7bbe3b56e8cd7cb3872fde3837b2557 | android.frameworks.vr.composer@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 9 | top | android.hardware.atrace@1.0.so | 65652 | e14cd7d27cbb8c4dbc1ed34c82c2c10123f6621b43aa6b479ebad78d693661c6 | ELF32 | ARM | d25791cc02522f6fe3b377f58bb8768b | android.hardware.atrace@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 10 | top | android.hardware.audio.common@2.0.so | 14968 | 024d66a2f3260bf42f2e4dda03dc90f3503d3cecf4846ef3639d4837f2e3cdc7 | ELF32 | ARM | f3be3ecc3805572b7ab2e03e6ea30efd | android.hardware.audio.common@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 11 | top | android.hardware.audio.common@4.0.so | 14968 | 557b7cd17f571f55e3acc69c0d7a341b2ff8fe6fd62e1c760735329405a810ee | ELF32 | ARM | 8ab1f54186f09a7748cd1f4a2d719188 | android.hardware.audio.common@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 12 | top | android.hardware.audio.common@5.0.so | 19492 | e88afc1270226c66479767ae72182cf6d009a1b6950b2fb88d6c32f29ecfef61 | ELF32 | ARM | 07d287ff4f647bc640db8160737bd056 | android.hardware.audio.common@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 13 | top | android.hardware.audio.effect@2.0.so | 912108 | 36701856e8de3de7006138c5ca6bad90111819245e776d93bbe05ac272f701ed | ELF32 | ARM | 450c3cc474bdb9178505a84a8f8b5e78 | android.hardware.audio.effect@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 14 | top | android.hardware.audio.effect@4.0.so | 907696 | 9c459c411f6dc7636f5c71e653c521946df3689f1c03441c0998ec74a3ba583f | ELF32 | ARM | 79b772ab0d1de24bc8f0bac7a8186341 | android.hardware.audio.effect@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 15 | top | android.hardware.audio.effect@5.0.so | 907716 | 57b4f821c292ce1f71f3baff81c9d11ff7b0c3fca1648c8adc394e6b34af05b9 | ELF32 | ARM | 22c05e60c820e8027c50cc81ae3f4e1f | android.hardware.audio.effect@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 16 | top | android.hardware.audio@2.0.so | 446200 | ed51d1ffeeb5cb82c6c15b8273bab866bf6a4bd56b95109a7c44cc472d610fdb | ELF32 | ARM | 593214c54bbd03cbb9fe28432798bedb | android.hardware.audio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 17 | top | android.hardware.audio@4.0.so | 472592 | 51adc6f48117a975746e24d9937a48c16e1fc191e929cf92dfd58b7c8b109fc6 | ELF32 | ARM | 6dbc6e996dbd7127c2574c6bf23a195a | android.hardware.audio@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 18 | top | android.hardware.audio@5.0.so | 480836 | a68d75f53a53816500dabf236f9b3dbf74335b405871c2a82d0256dc4102230c | ELF32 | ARM | 47dc8ed1fe1059bb109affcc8289aff8 | android.hardware.audio@5.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 19 | top | android.hardware.authsecret@1.0.so | 61228 | dcfbd2bf53ab0f91ccfe42ea84e81431a3d2be0a89ac64600f5ff81e9a89ae99 | ELF32 | ARM | f71938cf533786173d5ab65167780251 | android.hardware.authsecret@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 20 | top | android.hardware.automotive.audiocontrol@1.0.so | 61508 | d3d36c5c60d3078298aaebfc82de1b6e3d5cd8ff18f9cb784e2ebcd048575f62 | ELF32 | ARM | d93355713880e5c2c8ccc3b415df4680 | android.hardware.automotive.audiocontrol@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 21 | top | android.hardware.automotive.evs@1.0.so | 179448 | 22dc2bd694047253b5b35c9e5a3e45c27d7a33be1922678b8e461eca42d5e9dd | ELF32 | ARM | 0dd159703a078327e5d556a8799c6e45 | android.hardware.automotive.evs@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 22 | top | android.hardware.automotive.vehicle@2.0.so | 115992 | 203965c32cfca640fced5bd1e4042f628dcd2f4a59ffda4ce61abcdac13f05dd | ELF32 | ARM | 17dbf927473f18219b01d86d418c87f2 | android.hardware.automotive.vehicle@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 23 | top | android.hardware.biometrics.face@1.0.so | 137152 | dbff1d9ad12146d924e4413c0bcd0ce4a37a08d26f75b726dae2f9a1411fe063 | ELF32 | ARM | a91a19d5d4c4087d66e7653acb043220 | android.hardware.biometrics.face@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 24 | top | android.hardware.biometrics.fingerprint@2.1.so | 123516 | 91118a5187156a1f0562bab7f1d1f66173e9b5ef08bf5b6be93ebf4c9c282b60 | ELF32 | ARM | 6efe2acb2191e211537f6f6d75741836 | android.hardware.biometrics.fingerprint@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 25 | top | android.hardware.bluetooth.a2dp@1.0.so | 102272 | fa666b63d2adae243817057da198f1cd80527d815e4a18fe6bec6898133de21f | ELF32 | ARM | 5033c4e9b2aeef9ba80d47c1a18eb49a | android.hardware.bluetooth.a2dp@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 26 | top | android.hardware.bluetooth.audio@2.0.so | 159912 | 31f649e710d49e74ecdd959aca70c65030df11863b1e4544dab9e1d66003f111 | ELF32 | ARM | 85bab59bd23089dba32d4b623729468e | android.hardware.bluetooth.audio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 27 | top | android.hardware.bluetooth@1.0.so | 101608 | 25f821a400916b05f15f16994e3fdca7516f0e5b8d97de347eb9ce401ef140d9 | ELF32 | ARM | 1f4ad3b07092c37321df62d414da7d00 | android.hardware.bluetooth@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 28 | top | android.hardware.boot@1.0.so | 74644 | 75d5fc38714c6c9dc13cdaf5ff2b80c072d2cf042c569953e7c4a8a958333135 | ELF32 | ARM | 7b887a50a6be8eb5ee74bd75bf269ca8 | android.hardware.boot@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 29 | top | android.hardware.broadcastradio@1.0.so | 185016 | 72896bf1e31bf8850a5062347bf4fa25974b4232e9b70e41075da0ad32ac794f | ELF32 | ARM | d983f0f1e5941b33736a7edfeeb728af | android.hardware.broadcastradio@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 30 | top | android.hardware.broadcastradio@1.1.so | 208864 | bf609833295f3838b2ae655633b277a91bf63b1c8b04049622b02f6d5fed56ce | ELF32 | ARM | 5eb91c7bda60c0d1549e027f8e8efb6e | android.hardware.broadcastradio@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 31 | top | android.hardware.broadcastradio@2.0.so | 230536 | 3ab9aa1f2bbbada1a34dd883d3307e5f7b340d2ff694e64e13d1b3d638e7f6ad | ELF32 | ARM | fa95834bb7c250e701db4e1b70a03082 | android.hardware.broadcastradio@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 32 | top | android.hardware.camera.common@1.0.so | 15060 | 924e77d77338f766f31e887732a7c0aeac622b26a97fe25f906a0573ca773a4a | ELF32 | ARM | b93496866c71c9d61484bc4a150dfda6 | android.hardware.camera.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 33 | top | android.hardware.camera.device@1.0.so | 207080 | c7e32da48d2ce4ef9a8e74c7a49f4e76673ee28ad1d408a4c454cc7269304511 | ELF32 | ARM | 8b25ff5bdf68a88a6c79073a9ac2fcb9 | android.hardware.camera.device@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 34 | top | android.hardware.camera.device@3.2.so | 152508 | 892afc7965915a6c65223a39c0b6f24accc456f50b277aa8b3e4e49e5a3e510c | ELF32 | ARM | 1c5eefde3607d66bdccb4586b6157a44 | android.hardware.camera.device@3.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 35 | top | android.hardware.camera.device@3.3.so | 75096 | d5262c49f725d81aabd0f215bed00ea879be80a3f63f242f3f13b9326209848b | ELF32 | ARM | a0c56cc8c514135aa61567eed337119a | android.hardware.camera.device@3.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 36 | top | android.hardware.camera.device@3.4.so | 120644 | e96757660f985e87bf78743dac895132bd13db9cd4e6f9c4b0923c406ff1d002 | ELF32 | ARM | c59b280de2b83c877a9b25a93102b589 | android.hardware.camera.device@3.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 37 | top | android.hardware.camera.device@3.5.so | 180424 | fab30b50a326e643d60163e98a77f607d1d4e279b95ccee5f32e9a153a15b9c4 | ELF32 | ARM | 1342e3e0e296ffbb690407ebeeedbc93 | android.hardware.camera.device@3.5.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 38 | top | android.hardware.camera.metadata@3.2.so | 14916 | a51a700f716a005127687f941e687e3f16f62f102483901ac44caa65195e8e4d | ELF32 | ARM | cf98e91725d4d9a6e131db3a15e3980f | android.hardware.camera.metadata@3.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 39 | top | android.hardware.camera.metadata@3.3.so | 14928 | e33f65240ae99715eb2d5714fe139a434d9696c70abeced4049b1693374024ff | ELF32 | ARM | 72b4a4a668c89b3226f5bb38ce885911 | android.hardware.camera.metadata@3.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 40 | top | android.hardware.camera.metadata@3.4.so | 14936 | cd0340e141b899b6588e8197c9514d78e43cee3e7068c1b5bb579af2376aa805 | ELF32 | ARM | 18b609529f24857651d4bce406f986e4 | android.hardware.camera.metadata@3.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 41 | top | android.hardware.camera.provider@2.4.so | 111340 | aa0201158ec4c66beab0d2c2d6e4355ed552ba509d2b0fbe6ae918ce0f90aa46 | ELF32 | ARM | e13ce2f3c644a008d2b14a736e83f1af | android.hardware.camera.provider@2.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 42 | top | android.hardware.camera.provider@2.5.so | 74840 | ad6080a3c416083d3a8ea812663cdf38f2b11c5094fbcd849513262d440aa322 | ELF32 | ARM | 0ca91ae9900a1755e1a67c7b79546d01 | android.hardware.camera.provider@2.5.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 43 | top | android.hardware.cas.native@1.0.so | 70352 | a521cbc056bfdec0cfd8f5da130a79656c1b588a975525b0f59ca7de8dd6b57c | ELF32 | ARM | f341e827d3d7d9e21e7bd25b793d3dd8 | android.hardware.cas.native@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 44 | top | android.hardware.cas@1.0.so | 170444 | 6197e0f82784043fb089bfe5d94697229f57c339b0608f9fe6f90d917537dab9 | ELF32 | ARM | 5f15322a22479aa3b85b530fe23bae56 | android.hardware.cas@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 45 | top | android.hardware.cas@1.1.so | 139148 | 605acca164e4de90ce90d506c2833e7b745fe044a4e4e56e9aec261500fa561b | ELF32 | ARM | a9b85c23a6ff14af3816a07525dfcce9 | android.hardware.cas@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 46 | top | android.hardware.configstore-utils.so | 15184 | aedbd5c76313884e73ed07c7c0a82bb616d650b10a7f8d18112cfec322bb4735 | ELF32 | ARM | 13469ce17915f52f2f413a936404ec7b | android.hardware.configstore-utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 47 | top | android.hardware.configstore@1.0.so | 92432 | 815b59e61d1e6e7b653baefd117194e232c8685016681404eac23eb57a765091 | ELF32 | ARM | c09626836fc4bf5d1ec9d1d0a30411fb | android.hardware.configstore@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 48 | top | android.hardware.configstore@1.1.so | 83540 | 15c3a07396da964acc0c0f1aaa36eb6e120f21982ff50fe853a6f134db2e94c1 | ELF32 | ARM | 5d19e1a3c99559c5c4c98a62866d13d8 | android.hardware.configstore@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 49 | top | android.hardware.confirmationui-support-lib.so | 14916 | bbb535916ae45832e0d29284007f07c953b493eac273b9d6703b5bb7822c3acc | ELF32 | ARM | 0bd9917c430df49ad103848b33cfb798 | android.hardware.confirmationui-support-lib.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 50 | top | android.hardware.confirmationui@1.0.so | 93228 | 042afa9a50bbae45f78f75da82cec48183fdb27346096fa3279194c6a11c0eb8 | ELF32 | ARM | dd99382e2a5a3b11dc2ae14786762c50 | android.hardware.confirmationui@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 51 | top | android.hardware.contexthub@1.0.so | 114736 | 6b0eec71f683aecad550957ccf7892ccfa919112bf4ca405b13ca682fff9e87f | ELF32 | ARM | b3b18421d9a3c23c1d643e364cccf41d | android.hardware.contexthub@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 52 | top | android.hardware.drm@1.0.so | 265456 | 26737962c1b75d3e25926a0e211d3804d818f427d259f21995397c9a89a2a60f | ELF32 | ARM | 16d4b21c643730478be2994891f6038a | android.hardware.drm@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 53 | top | android.hardware.drm@1.1.so | 184960 | 235e46dc359d01e926be08da75b0a02472847969c0b03cc3f9523d4495008c47 | ELF32 | ARM | 464906d933d46815bcac3112865d046f | android.hardware.drm@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 54 | top | android.hardware.drm@1.2.so | 277424 | 2eb511662a648c3e08317cd6723dd56452022a347174cb8641e1832d1c3a28dc | ELF32 | ARM | 452fe7701837f32bfa0a1ce49ddabd87 | android.hardware.drm@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 55 | top | android.hardware.dumpstate@1.0.so | 61076 | 95ae1f099c5fa7e5a90967e4d05f57a47f58d7406bcf1412a84cf43dddefba6f | ELF32 | ARM | d7a775e3561e655e2ae84f0c297f9792 | android.hardware.dumpstate@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 56 | top | android.hardware.fastboot@1.0.so | 74932 | 39910863c3412ebdf8a883e0d9ff76ab4c3116db830c55fe8c01df65f1546735 | ELF32 | ARM | 5d70475d504dea7131e37b31f7161696 | android.hardware.fastboot@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 57 | top | android.hardware.gatekeeper@1.0.so | 70268 | 0155df4db251c5d318b62d00b9f8325c9779c7ced39e4d8b2620b94935a9e253 | ELF32 | ARM | 63422fd51abe960c796006baf46d2bd5 | android.hardware.gatekeeper@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 58 | top | android.hardware.gnss.measurement_corrections@1.0.so | 93124 | b58cfe31dc9530fda747e74054f2b4eb18cba8d16ba0e0bb753262ed4d1b29d7 | ELF32 | ARM | f06c304b243d35a05cb551d099f48d83 | android.hardware.gnss.measurement_corrections@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 59 | top | android.hardware.gnss.visibility_control@1.0.so | 97320 | 09f9b1c33a7e09f6a28b8f8183ed0eaa4b2d815801ac1eab5751f7515f13cee3 | ELF32 | ARM | b7f6b8a7fcbe7e807f7108a727d64dd3 | android.hardware.gnss.visibility_control@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 60 | top | android.hardware.gnss@1.0.so | 675212 | d00e6b15ae5d53bd9970ce8ec4f108b52c5ef4f74d460a3ab0a692baec02d0ce | ELF32 | ARM | 3cb8ae24c3a3ed7f2e574b4d6bf2b146 | android.hardware.gnss@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 61 | top | android.hardware.gnss@1.1.so | 237560 | 534d9e49e81a08c48cf57a9118dbaf428394f8dcbe0b9b166cc767441f71d9d4 | ELF32 | ARM | f8903589fb1afef165e7517d5035edd4 | android.hardware.gnss@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 62 | top | android.hardware.gnss@2.0.so | 474012 | 772e76a20ed4f5b6072d1978b12c7f9bea97ba33068ad838ae57e6d34a85aba0 | ELF32 | ARM | 6f66017db49bdb168721c3cb04c6c65e | android.hardware.gnss@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 63 | top | android.hardware.graphics.allocator@2.0.so | 65836 | c71acd386f8a72de24a3c9cbd2430a6173d33878303dce3a0d711b79bbee3d6f | ELF32 | ARM | b195e4256abb45685276c2a338f8ee97 | android.hardware.graphics.allocator@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 64 | top | android.hardware.graphics.allocator@3.0.so | 65852 | f69942179d0f5e0fdf821cd00982a2e89f80cce43c6c1538299a9f6a19fefdd8 | ELF32 | ARM | dcd9901c60c970a8259a1f35fdedc054 | android.hardware.graphics.allocator@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 65 | top | android.hardware.graphics.bufferqueue@1.0.so | 155084 | b06749379015840a31684245efc004336692379a9759e8dc7a0d91e3b13538ea | ELF32 | ARM | dd9b5de322d3c4e00367f8cc9fd4f8b5 | android.hardware.graphics.bufferqueue@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 66 | top | android.hardware.graphics.bufferqueue@2.0.so | 137592 | 35be1009924dd78ae0dfd9a29d72d50a38268ae81c28c3e6da297ae678d27140 | ELF32 | ARM | 079171236ff3a6b109b391e07f162cd5 | android.hardware.graphics.bufferqueue@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 67 | top | android.hardware.graphics.composer@2.1.so | 188048 | 31db7b50da39306f9674f3ef964d670c7c3633175b446fc02a2b99a7b21125bd | ELF32 | ARM | afbbf41cb3324c25e135c07d8e36b34c | android.hardware.graphics.composer@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 68 | top | android.hardware.graphics.composer@2.2.so | 156900 | 662c2b796fcde599a6e372d96c8e762dad11699be0c3dc6ee5598e7ce5726253 | ELF32 | ARM | d1fa6c2edbbba5747bfcb5bceffe15ec | android.hardware.graphics.composer@2.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 69 | top | android.hardware.graphics.composer@2.3.so | 197200 | b8c91ba607b895f434984c2bf9e1bf3788d40a3a4553ca042f1311b3b6ad5eb9 | ELF32 | ARM | 4c6a45881fb8322b8c62a120463a7fd0 | android.hardware.graphics.composer@2.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 70 | top | android.hardware.health.storage@1.0.so | 89168 | 09d67cf20a84e444c7b1b0333f2bb3bda58b4891c52ff05ef6f33159133cc67a | ELF32 | ARM | 0019964d487751bbff10598ed3dfc6f3 | android.hardware.health.storage@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 71 | top | android.hardware.health@1.0.so | 66200 | e3c78ee8b252aa565377b0a9b7b57fa9faaed3e3a491c2ab8e8f90b9b746ae3e | ELF32 | ARM | 6ee66ff45d63041717da7338da8b1f1f | android.hardware.health@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 72 | top | android.hardware.health@2.0.so | 120732 | 329684b07fc02e44b720f9b8adf98153d1afc65e55d92fb1b4d992a23aa8ad35 | ELF32 | ARM | 15856baa97eba7046ab068724449f1b9 | android.hardware.health@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 73 | top | android.hardware.input.classifier@1.0.so | 61248 | 618e2b2add607ba63a6c3211176758e60da355540f93303ceffb3e15634d8cb7 | ELF32 | ARM | 8174e37b8d9bf8f3b3b2412e52245223 | android.hardware.input.classifier@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 74 | top | android.hardware.input.common@1.0.so | 14980 | 721652fa36085a998e0a3e3a0c86e02619d85c3daa81cb96502345cdae120e90 | ELF32 | ARM | 5da3b747eeb751370e86b5ef8d5e44ec | android.hardware.input.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 75 | top | android.hardware.ir@1.0.so | 61472 | 14f0e2358aa87bea35f6ee8e97abacdf222f083ae9d8237b1ac5c2d829555f91 | ELF32 | ARM | 25515d1bce6817838774ac750da17b5e | android.hardware.ir@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 76 | top | android.hardware.keymaster@3.0.so | 105620 | 620b79b07a1118b1025202443f90bb06e35788dd50ff15966a9af283c87448dd | ELF32 | ARM | 5514f2dd8562379548a48fb0b618dce5 | android.hardware.keymaster@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 77 | top | android.hardware.keymaster@4.0.so | 118980 | 8785b9b992628344cb9bc6721bcb4d88502eab935c1ed29f43cfb931c2e9bbae | ELF32 | ARM | 05dcb7ce011a81c70e8d0ad1706c8310 | android.hardware.keymaster@4.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 78 | top | android.hardware.light@2.0.so | 61380 | 2b7dad0f43792f281a59e1bd159065e66a1c417949220d95d6b7e71c71cbf157 | ELF32 | ARM | b15a9288fe219e65bb958b850fbf45c1 | android.hardware.light@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 79 | top | android.hardware.media.bufferpool@1.0.so | 121744 | 9ce83e68aa1f0c1f39b48adcbdd03a428508ce08088e88740a50e6273ca67b71 | ELF32 | ARM | 8eb23c37f58a3de1ff85f1ca6cdcdb7d | android.hardware.media.bufferpool@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 80 | top | android.hardware.media.bufferpool@2.0.so | 145612 | 962f36af8bc535c9ba1e571ad4f4a10596b95c07c84ad970144241e572324410 | ELF32 | ARM | 214760fe2f3df2c3e7c65c0577b3408a | android.hardware.media.bufferpool@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 81 | top | android.hardware.media.c2@1.0.so | 378076 | 0658c5c34639e34909ba01365f55fc0b7458abf3c98e4fbfc3647c51e5383dea | ELF32 | ARM | 21e185222ad97419116de659a3d3ca43 | android.hardware.media.c2@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 82 | top | android.hardware.media.omx@1.0.so | 289092 | 686b7c55701150d040335f7a0256e4aed24c85212de7eea7f222b556fdaf6d67 | ELF32 | ARM | 17afb2d6531b562eef3b3a1a9296774e | android.hardware.media.omx@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 83 | top | android.hardware.media@1.0.so | 15028 | 0863c9cfae7e79a3cf2ec225e16cb6cb66f3b00c3de3c0790b4adce5d4a2c0a4 | ELF32 | ARM | 20ef6a4ba2b5a60645adf4ca85e44835 | android.hardware.media@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 84 | top | android.hardware.memtrack@1.0.so | 61444 | d280d2619baa50cf9e7e0c0253fab7ef51c94c8ad7804f332f151c2ee2c198ad | ELF32 | ARM | b290f96366385be7c5ebd84c4c7bfe88 | android.hardware.memtrack@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 85 | top | android.hardware.neuralnetworks@1.0.so | 153600 | 3be8fc45f0bd2d5ec4f35f3b38b2aff75763b9b7d13956c0667cd2eaed2a983f | ELF32 | ARM | 2b2eec8f11fd8dface7a001603f64e99 | android.hardware.neuralnetworks@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 86 | top | android.hardware.neuralnetworks@1.1.so | 79056 | 05e98e3e98ccb155414c1785394a0f3d3ae1fe3bca37f4c2ab7ae18dcf166bbe | ELF32 | ARM | 6f097412873e48b4e08c49714f5360e7 | android.hardware.neuralnetworks@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 87 | top | android.hardware.neuralnetworks@1.2.so | 288708 | 5f0b717cdf842beae1f428e2960ee02876d823c7db5880263eefcd7c4c4486d7 | ELF32 | ARM | 07efbaeee365fa8d8dd885e0a8e12021 | android.hardware.neuralnetworks@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 88 | top | android.hardware.nfc@1.0.so | 97516 | 9c8b937dc4fc59b89a89fded68837db707afc64bd88e958f2b909a2aa5330021 | ELF32 | ARM | d4410b1a8174dad0ecbd6d01e1ae0d1d | android.hardware.nfc@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 89 | top | android.hardware.nfc@1.1.so | 107024 | afc9023a10a1e6311a82f942bc27c424a23f5a49b2ec6304b2dc9efb1cdfdf3c | ELF32 | ARM | ccfaa0aebb3a0cb1128fb2d316cad85a | android.hardware.nfc@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 90 | top | android.hardware.nfc@1.2.so | 79172 | 8695cfdb97b77520edb1a5919b86e018650a6f64f3b022199b2b760f6c9477e9 | ELF32 | ARM | 42d6427e5e1669087f01930925c54076 | android.hardware.nfc@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 91 | top | android.hardware.oemlock@1.0.so | 70296 | a462b59c24cd397710dac220c7b2ef933b6b5858274d33eb7f0a1ec95b10625c | ELF32 | ARM | 9c2f6bdd9a3e19ff9121990ac92fb167 | android.hardware.oemlock@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 92 | top | android.hardware.power.stats@1.0.so | 79392 | 42734871100164a0bec4df13fe1934b795c1f98777c4c367c63a4dec91b77bd6 | ELF32 | ARM | da90271dbd003ff03eff7586fba3dce8 | android.hardware.power.stats@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 93 | top | android.hardware.power@1.0.so | 65856 | 410d4efdf8e0a4cb91d614c6842ac472284cbe1392bcd0b3c3d78f9cdc97b893 | ELF32 | ARM | e4d3305dc1916bb2861baf568ee2e3ec | android.hardware.power@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 94 | top | android.hardware.power@1.1.so | 70600 | 4c702aaba2a4ddd4f108bf0423412e88ed882be06ff366aab190ab5dc0adba4c | ELF32 | ARM | f60072e351475cbe158c35072033c30a | android.hardware.power@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 95 | top | android.hardware.power@1.2.so | 70804 | df5dece99761764f40ba0bdb688261607e7018946ccf5a89605fccc20e364b72 | ELF32 | ARM | 81057588fbddfadae338cffaae804d79 | android.hardware.power@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 96 | top | android.hardware.power@1.3.so | 75424 | e5069d1e8c465ec3995c224bdf78ee593591ad782903cf7f4938ad1d704f6ba7 | ELF32 | ARM | fffecddfb847be4b2f7e98df53df837d | android.hardware.power@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 97 | top | android.hardware.radio.config@1.0.so | 125876 | d3d9acaf3414280be2c716a9922af11f31753ff82491265edce48c96e2ccb6f3 | ELF32 | ARM | ddf4571a6ffe1d966d2c4513592e8698 | android.hardware.radio.config@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 98 | top | android.hardware.radio.config@1.1.so | 148716 | 7323d61c1e76c6d5427acd059edf66db4b5731eb41b35897ea5974de6bfa1fc9 | ELF32 | ARM | d0cad2dc811adba7fa2cff881c2290ec | android.hardware.radio.config@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 99 | top | android.hardware.radio.config@1.2.so | 116652 | ce36ccb021f90a0384fa4d496958b472b49ec31cfdb8d96b611720726f8e8fa6 | ELF32 | ARM | 0842d91006c2fd062d0275d0e445caa4 | android.hardware.radio.config@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 100 | top | android.hardware.radio.deprecated@1.0.so | 125792 | 2d759de5d9daf46fd8314fadc3b7d4c5557f2b948552599bb4b11917a604a1ba | ELF32 | ARM | 7db8adf603fe9952953f69f792fbf636 | android.hardware.radio.deprecated@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 101 | top | android.hardware.radio@1.0.so | 939424 | 4290fda38d35a19e619592967b0d7afde8f9e83916270c257e90376aa4e6d896 | ELF32 | ARM | 40c03bc0ab562f2197e1992623df0273 | android.hardware.radio@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 102 | top | android.hardware.radio@1.1.so | 672344 | f16d2d25523ca0e621fcdca16cf32ea8f506e0a17db906057a2c1d41221fdd5f | ELF32 | ARM | 83e45726570aa2e9ee3ec876cac09056 | android.hardware.radio@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 103 | top | android.hardware.radio@1.2.so | 746576 | 9639122eb94d052945d925e8173fe03667148ba1a9a12936ef6223594b7e107c | ELF32 | ARM | 1253985277a3a88c87f70198cc56f783 | android.hardware.radio@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 104 | top | android.hardware.radio@1.3.so | 708440 | c9bc42d2db20996e0843649c0733436f2de4832b0326f4e8384a29b32cf40c96 | ELF32 | ARM | 3c94f53655b320d0ec393b7eef5913cb | android.hardware.radio@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 105 | top | android.hardware.radio@1.4.so | 821028 | 3ea305a361eda3c208caae7387a2c9f7df971dcce3f296f4da2660ac3e799fef | ELF32 | ARM | e43f68d85b7e334fd5adf674989d79e8 | android.hardware.radio@1.4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 106 | top | android.hardware.secure_element@1.0.so | 102664 | 49622cbce9b895d5774aec8c6314d7659a7c3fb884d572d693277d499b5ee4a7 | ELF32 | ARM | 77d9f8c32964a67a8a8a70cd70a18c15 | android.hardware.secure_element@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 107 | top | android.hardware.secure_element@1.1.so | 102724 | d9639d1e37ec4bbbe3c278490ae3ecaa8aa9e69995bffc1950a119d43aa13ba5 | ELF32 | ARM | df013b42220f3cc635462f6b411cec49 | android.hardware.secure_element@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 108 | top | android.hardware.sensors@1.0.so | 79288 | 2e026dfc2444da36c7269a7af77487f0e1b49340dd1e13d747f080ecd90cb5a0 | ELF32 | ARM | ed96bcef8714397a85fa94f73b32a9d7 | android.hardware.sensors@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 109 | top | android.hardware.sensors@2.0.so | 111408 | 64fe084c6085cf360fd96c6c5da199752cac63b43a21d458384752b910afd43c | ELF32 | ARM | 4bed8a3f791e8ae29398e32d5109c600 | android.hardware.sensors@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 110 | top | android.hardware.soundtrigger@2.0-core.so | 28288 | 61eec3cc8a1a14d3adb8c81e2adec8c04504f90db7813374143f264fdf0fd3d0 | ELF32 | ARM | faf32dc52711da9276716fd2d99894d7 | android.hardware.soundtrigger@2.0-core.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 111 | top | android.hardware.soundtrigger@2.0.so | 115104 | b9a28f0fd81cde9ee4af7f637fd24eca48d0414e1cadf2120b956108dbcd3062 | ELF32 | ARM | ad17c31112ed7afa21e370714fb86266 | android.hardware.soundtrigger@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 112 | top | android.hardware.soundtrigger@2.1.so | 124272 | b9610c0180042dcb34edc331311d3bccc7d4f58b58debf34835398dddd66f212 | ELF32 | ARM | 36a32cde42b91a014797e43df8800423 | android.hardware.soundtrigger@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 113 | top | android.hardware.soundtrigger@2.2.so | 83428 | 1573fdc5264b09bb31c757e31d6f80d4236d495bf06f5f983d22e9c802fb624f | ELF32 | ARM | 87e5f2900bbf3f85ae78da03c3ff8097 | android.hardware.soundtrigger@2.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 114 | top | android.hardware.tetheroffload.config@1.0.so | 61428 | e1ca8875d10f0353a3494ad96be67818f92e88854ade0ef21756274e8cf1777e | ELF32 | ARM | 2cce15f86f64c2c998a3613210c45fbe | android.hardware.tetheroffload.config@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 115 | top | android.hardware.tetheroffload.control@1.0.so | 116144 | d93290ac8448bd75732c2eb198805595f620e479193fcd8fc730ca3ce919cbd6 | ELF32 | ARM | 45597fe9f1cfd999e665b29c2d552646 | android.hardware.tetheroffload.control@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 116 | top | android.hardware.thermal@1.0.so | 66120 | 95245b9b65392ed419f7bf623c26ca71c835c595b402fc3f7343b11920347853 | ELF32 | ARM | ed92cb76e8884400ff7404fbc0589f74 | android.hardware.thermal@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 117 | top | android.hardware.thermal@1.1.so | 93964 | e6fa45a13aa53ec135497b660df46298642c996bc29f72aa9aa95b682857e6e8 | ELF32 | ARM | c80b6aeace2c7f93c5708073e368f53b | android.hardware.thermal@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 118 | top | android.hardware.thermal@2.0.so | 111860 | e42d2c8f67de6f51d1f5466667e0259cad7fefba5af873cad44af489384ea7db | ELF32 | ARM | d67efc85373446f8ea588200a0854168 | android.hardware.thermal@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 119 | top | android.hardware.tv.cec@1.0.so | 111180 | f9ff99a2812444345b03d87774b33f22eb2fc5eae6e430df789cb5295f53dcfe | ELF32 | ARM | c9bdfb4d15dc4803159f974bfe95fa93 | android.hardware.tv.cec@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 120 | top | android.hardware.tv.cec@2.0.so | 119764 | 2b9d6ac5221f593c8455a2c15e141c56bf6bf9ca3ad813511dd9cdc9e65d0656 | ELF32 | ARM | f34cf4818c57115f80edd3722dc13de9 | android.hardware.tv.cec@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 121 | top | android.hardware.tv.input@1.0.so | 93864 | eb81a4505575b89dbafd942c4ed2ad0ddf49df519bfee3cae1289fed15ae7bed | ELF32 | ARM | d23d212a5110077dd9d7f66337c1789a | android.hardware.tv.input@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 122 | top | android.hardware.usb.gadget@1.0.so | 93640 | b79046cec85d49ac585e6c69b39c0012e8c84a8cf07a6988cd09f1148704ca68 | ELF32 | ARM | e0ae8b0b47e24d733a167c338fc3fd1f | android.hardware.usb.gadget@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 123 | top | android.hardware.usb@1.0.so | 94020 | 7d648c0f1d7b7ceb5b2894022e933aa039b59e2a7302dde9f6163983bedb3527 | ELF32 | ARM | 0cec06a98f4bf7a8db63516b06c4f0db | android.hardware.usb@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 124 | top | android.hardware.usb@1.1.so | 94956 | f3606fb924c0bbd2cb54f2fb6243e24f8e10ec0eba49782c888751ded259f001 | ELF32 | ARM | 872b3ca5ee0a03a3d0a1e8f041cf50b6 | android.hardware.usb@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 125 | top | android.hardware.usb@1.2.so | 112612 | 606eaceeb91fbf0bd832ba09721f58df0052cc676680693f73c3ebea9adc37e0 | ELF32 | ARM | 89955182178a4640107832a14dae7855 | android.hardware.usb@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 126 | top | android.hardware.vibrator@1.0.so | 65800 | 57aae4f21ddda5f984e0520c54a9946084850d03a5a823c39b1a37913efdd4e7 | ELF32 | ARM | 66b1eee2e6c3ef79c7c725039a99519c | android.hardware.vibrator@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 127 | top | android.hardware.vibrator@1.1.so | 70332 | 127fba58d9522cc0518db10bfa36e24ddfafc6b5b24900773d2846c742ea2839 | ELF32 | ARM | 00c448b854e491f0a2ea9a5e9601c8f7 | android.hardware.vibrator@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 128 | top | android.hardware.vibrator@1.2.so | 70776 | 2d78ad16a510d2b493df97959d0d2b36d9f90e679ee98cb14b196333ed9f9863 | ELF32 | ARM | 2b77d580df114bfab8181796ffadd91d | android.hardware.vibrator@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 129 | top | android.hardware.vibrator@1.3.so | 79576 | 74ea2ec2094e23913ffd6695e6d8b42bd656c013fef215346b26de00bbea1e17 | ELF32 | ARM | 13c307fa7b0d08f0e3448b5dc662f9e8 | android.hardware.vibrator@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 130 | top | android.hardware.vr@1.0.so | 56984 | 791b8f49cc8355ac67d2c4b668ccc102f18fcb7e2f4917dec5e57641ca8ab986 | ELF32 | ARM | ce1f9f9cb622708eac92b05d060f0b35 | android.hardware.vr@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 131 | top | android.hardware.weaver@1.0.so | 65888 | 8eb0d8691701b126256864c3e27e99bd454c5a66a0fd544c83f5a94818b2c876 | ELF32 | ARM | 4bc610b80c6c0519676b9b88988353d8 | android.hardware.weaver@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 132 | top | android.hardware.wifi.hostapd@1.0.so | 65980 | e0026ad9a3e35275919400f8720de42b4a0e1c2f087f173678726b9a3f118ae1 | ELF32 | ARM | 0a309375cb081c33409829334637dc0d | android.hardware.wifi.hostapd@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 133 | top | android.hardware.wifi.hostapd@1.1.so | 98696 | f3d8445d01796eee9ba4ed3c5a4a0fe0612d9780b84a2b0ef648b509993c6a1c | ELF32 | ARM | fb0e667e022f73b918601cfa971c2b12 | android.hardware.wifi.hostapd@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 134 | top | android.hardware.wifi.offload@1.0.so | 102888 | 90d67e029542fa7a72b6113d05622001168106e92cda985568efb24ef1874144 | ELF32 | ARM | fb3e8596d11cd2f56acbe29bd31a5735 | android.hardware.wifi.offload@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 135 | top | android.hardware.wifi.supplicant@1.0.so | 967760 | b26721cfede1a5dcba31f95e8ccde0a874f65bf984aaa2e4c4aaf833061919a3 | ELF32 | ARM | 2660dd52368b8630a57fee3df94c4356 | android.hardware.wifi.supplicant@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 136 | top | android.hardware.wifi.supplicant@1.1.so | 370276 | e2e787e873368807d9d2bd373f02cedcbd048d3efe20e6bd782d667c0b202e7f | ELF32 | ARM | fcba11003920e87266fbf2bedba312f4 | android.hardware.wifi.supplicant@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 137 | top | android.hardware.wifi.supplicant@1.2.so | 547048 | 51c98137130705875dbdeb8b6e1ef5ca5568b9d1b7f29da5adb7e996ce3e28e4 | ELF32 | ARM | 46253d21ff25bd009d85a00751453d42 | android.hardware.wifi.supplicant@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 138 | top | android.hardware.wifi@1.0.so | 743276 | d8b7cdab45b3d6e4f50f731ba133dcf1c292a8d20383133cd2253c4569720c71 | ELF32 | ARM | c4717bb4fffec56952a333b853d5f79c | android.hardware.wifi@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 139 | top | android.hardware.wifi@1.1.so | 144416 | 2f44d0efcfdae49ed19ec4681ad06f4eaf0fc0b11b16ad20a167c8a483b6f83b | ELF32 | ARM | 170ed5c8ccc54d430d3bbedb21453542 | android.hardware.wifi@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 140 | top | android.hardware.wifi@1.2.so | 401992 | 4a99a8159d24efb54d14b528fa1bd99ca6ac70ab995d4d8d0916965802dc4b57 | ELF32 | ARM | 20f0c9f40f0b0cdd8544e373f519265d | android.hardware.wifi@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 141 | top | android.hardware.wifi@1.3.so | 245256 | 4441ffb474b4b8e9c89eb1a747c57fcfbfe45a660c7e6b43926523ca981ba60d | ELF32 | ARM | df396d9ffa21c6fd2b45652042b4df21 | android.hardware.wifi@1.3.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 142 | top | android.hidl.allocator@1.0.so | 61728 | 269559a37e49a033b64a6ee7d11cd780cb8654981aaad7fc2bf9909534afd167 | ELF32 | ARM | 9740b34e9cc7404df4ec897d1d21e192 | android.hidl.allocator@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 143 | top | android.hidl.memory.block@1.0.so | 14928 | bd0775c97637b93cbb87864532f6e98aa5974808d2269a35474429ed36c3d0e5 | ELF32 | ARM | a868be7a9bae7f6d066f6a84f323a0af | android.hidl.memory.block@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 144 | top | android.hidl.token@1.0-utils.so | 15976 | 6e7fea4ba47363e0459382de46c61aff331952bbec1766a25b0499d27e0648fd | ELF32 | ARM | 64a11aac2f56790ed3f1e50291fc5819 | android.hidl.token@1.0-utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 145 | top | android.hidl.token@1.0.so | 65608 | 499bc5cc8dd228fd11d652670761283fa5db712c48ccea175273218ba7fc94cd | ELF32 | ARM | 74241f18c86222a6aea32ab4496fc7d6 | android.hidl.token@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 146 | top | android.system.net.netd@1.0.so | 61508 | 29df9fe000dd5117383ae5e85f96beeac123fd72d5c833fd4d98af29820952e4 | ELF32 | ARM | dc3bec80802bba84e085d59cd58d9b82 | android.system.net.netd@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 147 | top | android.system.net.netd@1.1.so | 74352 | e647d71e15032020a4f85431077adea4833e9adfe6350b2e3119b96072fd3f98 | ELF32 | ARM | 27267abf3b12fb682093225d4a011002 | android.system.net.netd@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 148 | top | android.system.suspend@1.0.so | 84896 | f2c3ecbbbd2f2e330c1da3b7d659a3ccc7b0bad5de2a1abe4fc6364dcac093d8 | ELF32 | ARM | 6400bdbf939fe23131be18be88ce0be8 | android.system.suspend@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 149 | top | android.system.wifi.keystore@1.0.so | 66052 | 475e5d6c1d4904ea4410cec17ee40e3f711b390a5e20ed2ba01852f627bc76fb | ELF32 | ARM | 549d753ce0969fd73a6f1b4f238efd5a | android.system.wifi.keystore@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 150 | top | libadf.so | 19612 | 8c0cd8183ece7c1caa3501cdb23be82ad0f2b5e2e5b198b1fb5c494d92543edd | ELF32 | ARM | 2ceecf4f4c9c7fff3d2cdc13e72dde75 | libadf.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 151 | top | libaudioroute.so | 19460 | 34d150ec1e54ca50c2c7ea51dbd591489781d13c70a5013dfd6c3c8d5b16b996 | ELF32 | ARM | c0c9e6e570e1f53b4c1a46520e4613c9 | libaudioroute.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 152 | top | libaudioutils.so | 71704 | e3d4198627a0f20f1aa6614295d8ccf5c945d95bda656de1236b911baec16f19 | ELF32 | ARM | 27b01c980072e629b82eeffd4f4126c2 | libaudioutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 153 | top | libbinder.so | 345708 | 84475baf1cb1391084c4f92412eeb8eaf436f2408670a66fa697ddf6a2133447 | ELF32 | ARM | 368731f5b7fef55cd36a0f42d79efa53 | libbinder.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 154 | top | libcamera_metadata.so | 36640 | 48a47aa8f8af9b03719874fbc4b700626676db854ec92d3576d38da68a44c00c | ELF32 | ARM | ce15c2ed26136f315dee67c9cf309234 | libcamera_metadata.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 155 | top | libcap.so | 19596 | a7a9f15541adf9541ce6c321c4ae85ad3d12b1050d65e743ff9714cdcd6a1bbc | ELF32 | ARM | c7aba04da6c19d5f667d655ca1da4109 | libcap.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 156 | top | libclang_rt.scudo-arm-android.so | 548932 | 02de76198a52e594a0c7c57d43dbfd3d4f0df7e91165bb3b8dd6ed155ebcfb90 | ELF32 | ARM | 2849301e17a2cdfbec5261d487ca4357f56638c1 | libclang_rt.scudo-arm-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 157 | top | libclang_rt.scudo_minimal-arm-android.so | 322044 | e9fd615fd35abcc464fafdac83e8711ac78f807ba8c29a3af6bb6de40a2c4fa9 | ELF32 | ARM | b99cb4906b6a6a6384cdd52622d3d4833d96bcc0 | libclang_rt.scudo_minimal-arm-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 158 | top | libclang_rt.ubsan_standalone-arm-android.so | 486604 | 7d6447b0c85b381e0011b24014e5607e5d8bf7c7123b657d2293d455ab716a01 | ELF32 | ARM | d335d0819997bd24dfb8b250b2b5cf5fde3c3786 | libclang_rt.ubsan_standalone-arm-android.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 159 | top | libcn-cbor.so | 15152 | 2bccd4656c90f5f0f440c9eaf07974781d168955f6058ec4c5e1f16259d43bcb | ELF32 | ARM | 3059631ddb7ca39c75e976fb350c419d | libcn-cbor.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 160 | top | libcodec2.so | 14868 | bc8e8601583623272a009fead8b36a8d8c5f414fef5eb646c40ca5b0a4242013 | ELF32 | ARM | 1079af5684c0b6e27a35c90e927119b3 | libcodec2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 161 | top | libcrypto.so | 959964 | 31eba623105b3492539e426e25abed0a305a9004535cbbd48a80182fe205d183 | ELF32 | ARM | a9265f7b457523779d520e62ed38f126 | libcrypto.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 162 | top | libcrypto_utils.so | 14996 | 320db4877c17dbc545560b841dc00a5f55d11f29d4178598c42b072f39eb59c9 | ELF32 | ARM | 5a6ef31bcbd1ecbc6f2b341fc5b358ef | libcrypto_utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 163 | top | libcurl.so | 271780 | 6bd739aeedada2d3e0e04100cfbc484deec806f4e035ce65b563980e3c9d5005 | ELF32 | ARM | 307b18eb9cbd04baa6b7d3617a2b36d4 | libcurl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 164 | top | libdiskconfig.so | 23736 | e1e71f2f9b6845edbb82e1c72ab215b2667af05c20e214455ae55799d5d26a50 | ELF32 | ARM | 296f79da453cc7d70b5e3a308829f6d8 | libdiskconfig.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 165 | top | libdumpstateutil.so | 32448 | 43d2f0041afd8cb00552a1133f527a7584710a26ccefd08b334476e646415f24 | ELF32 | ARM | 92214f92da9314a638ab1cfe6762edfb | libdumpstateutil.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 166 | top | libevent.so | 285800 | 02d3254fe04639bf6b77ee9b282409888802d32474aa9dcc1106834b388c96f4 | ELF32 | ARM | c6fb241cedeeae385fdaafe7aedd562f | libevent.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 167 | top | libexif.so | 170660 | 6d6accba6e40592bcb16cf55523c1c2d597fe5aa15665b17495dd189f2973d72 | ELF32 | ARM | cc49554fdbc0c314dcd00e8a8dcb5064 | libexif.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 168 | top | libexpat.so | 105712 | c1200eda7a35b6e0134c71633ba96719f4e3e8d0d04c280546a3aa59201416f4 | ELF32 | ARM | 7e5949b8032bd219540fd23a9a4d35f8 | libexpat.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 169 | top | libfmq.so | 23596 | 4b6188e0c1dca3ab7739f770a71801322ba6ea377b1a99bf9a7aea98c9bb277b | ELF32 | ARM | d4ea4598113ee04c4cfd04fdca96d1a2 | libfmq.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 170 | top | libgatekeeper.so | 23776 | d1b0587285f60c1133bd0fa069ceff2d1ddca949e1850e38598831bbff9297f8 | ELF32 | ARM | c39a32166d5fea129dda8fbfb3d76139 | libgatekeeper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 171 | top | libgui.so | 657192 | 467e8bdd304e6b6babc0dd144a2118f6d2ee232bfc9856c56a23ca26a059a92c | ELF32 | ARM | 75029f3a8f747e446826842fb8b83386 | libgui.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 172 | top | libhardware_legacy.so | 23872 | a3f20b8ba4750e1f95a373eb3ac12c256af3ae37869baa11d8e586439cd67019 | ELF32 | ARM | ba0c516d94376696b241c0d9c8282ec0 | libhardware_legacy.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 173 | top | libhidlallocatorutils.so | 15024 | f93c66cdecb49fe03f7acd462a285996dafcd22a2ae2bbd1f8108605575a4574 | ELF32 | ARM | 31a3d822ab92f3a135154f11159bae74 | libhidlallocatorutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 174 | top | libhidlcache.so | 39816 | af117fdd963747c8932163b4699900156b9272423e2c426ec42e9689ab22d0a5 | ELF32 | ARM | 3080d1dc9b625c30637bd6a6dc2d2638 | libhidlcache.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 175 | top | libjpeg.so | 295628 | 0dbe54a375bfbf0324a2d5873f0c7402f0e0ef4fa9be5bfe7231ac018341ca95 | ELF32 | ARM | 1084952af11ca441cd63eec03eab8e1c | libjpeg.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 176 | top | libkeymaster_messages.so | 63848 | 826a244d7b920bc2921554d3ff556d7047497111b59a9f4ed2869c02b6932b2a | ELF32 | ARM | 394bc6bd3451f928160de7f414b956f5 | libkeymaster_messages.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 177 | top | libkeymaster_portable.so | 184856 | 84b19da01a006056b7e164e1f347a84d83982960071ff8ff901dc59595058007 | ELF32 | ARM | f09e02d27d549cd330b29fd741b79f79 | libkeymaster_portable.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 178 | top | libldacBT_abr.so | 15028 | f228094c0605f8593f554f07763dcef4043a770af89382f2f4e5ec0ac16c2a35 | ELF32 | ARM | 95b6751007c431413eca97f8d5e5cd2b | libldacBT_abr.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 179 | top | libldacBT_enc.so | 48640 | 1085235f032bc01cb1464c289020bd35ffc00ca7a4c9f64d41255e73e26d320e | ELF32 | ARM | 73419b504150cf6255d2513e91262fdc | libldacBT_enc.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 180 | top | liblz4.so | 102592 | 889d4634bed503297d64f697785bb707108971d6c914342c6c88940794de6327 | ELF32 | ARM | 1a209967562f0362dc97d076dc96fff2 | liblz4.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 181 | top | libmedia_helper.so | 77220 | bd10d847e8466920bddbeb3a0d3e9d1ce7e5f4af1da7eaf5faaa33efe6f168ff | ELF32 | ARM | 1996ec5088dd84a8853503db09bb823c | libmedia_helper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 182 | top | libmedia_omx.so | 163388 | 31227e1598741811e890d12f068e929542ec6baa9388ffe6d1a6819d42eed156 | ELF32 | ARM | 44a019c079ae9dc855363d7b610179e7 | libmedia_omx.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 183 | top | libmemtrack.so | 15720 | 937bddced143b6491a074a71fbfed0c766737d6d8c9c44f7c6dbf6987a64a893 | ELF32 | ARM | 395a85f4f427ef26abf952bf201f417b | libmemtrack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 184 | top | libminijail.so | 102496 | c844fe5252c467210a60b07e69e44680ffb0e9b0bfb680e07f4459798a1cd938 | ELF32 | ARM | d0d7c81a9323200d08d035387903b917 | libminijail.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 185 | top | libmkbootimg_abi_check.so | 14876 | 7d73889c9b4f4f98ef95c47d66e5181a7a4a297b3e6ebb9fb4416bd1126d11ed | ELF32 | ARM | 79b872f2b3bc12605b5b04b7a5ea6adf | libmkbootimg_abi_check.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 186 | top | libnetutils.so | 32460 | 33716daa412b661e421631ad11c4cb17513b884c7edf0ed01edce4087af50d80 | ELF32 | ARM | b7d9a0d4e6057d9bdb27de1d72b22049 | libnetutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 187 | top | libnl.so | 96204 | da466c5b739000e018c27174d5d57870a1ecf1ea824611e75f9bc3c428845ca8 | ELF32 | ARM | c52442c1effa67fe923da154d7cc7535 | libnl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 188 | top | libpcre2.so | 266820 | 4c8ae1f7ca40f9060fa2a42a113bafdcd575a075729ab1e8468777e357125ea0 | ELF32 | ARM | 9f2049c0b1bc0e437321b9ce7eec0d68 | libpcre2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 189 | top | libpiex.so | 76104 | a2664bfe76c7b7d14fb48b9564f24494d5eaa39758e1dbd11a8c45b4c7deaf4d | ELF32 | ARM | f091aae9139d04e8fc0fbd329d207288 | libpiex.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 190 | top | libpng.so | 163272 | 8431b6a3aa7bb5f0fd6abf5b7523bd4e57f71d7f585eb612952f9ddb96be5443 | ELF32 | ARM | 615b56b8289204743214c8764fd20d3d | libpng.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 191 | top | libpower.so | 19668 | 1a4dd916f28d09821bb8e350e3d97ef494648b4aa043d81c3e8d87b03c21426d | ELF32 | ARM | cabef4d441a35b6cefc262fe6c007d48 | libpower.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 192 | top | libprocinfo.so | 15436 | c9b4c544164756b5b9c77f36571a1c1581a8b92f48a4b7846b62b20ea97dd969 | ELF32 | ARM | dd7841f14ad4d2bd30875740c6532e64 | libprocinfo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 193 | top | libprotobuf-cpp-full.so | 1374724 | 2814a57060a6b9ac3ce6773bbcfc62b750b66b3c2e036e3a62e44086bf5d17cc | ELF32 | ARM | 91932ca585590bdd325ab5b220e0d8b2 | libprotobuf-cpp-full.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 194 | top | libprotobuf-cpp-lite.so | 207868 | f76868a1647b81503727ba79703c734473f771c1c044ab75266c348e416ead83 | ELF32 | ARM | 040cb211640c32437238991b5c1a43b2 | libprotobuf-cpp-lite.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 195 | top | libpuresoftkeymasterdevice.so | 37312 | 7b0aca84a7fb34c5f3ac80a1f7739aae4cde5419074b702455042213b6594479 | ELF32 | ARM | 8f96a267bd975567722d215776cf87e8 | libpuresoftkeymasterdevice.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 196 | top | libradio_metadata.so | 15288 | b68a7a2999fdd3cb6a56fcfa05717ce3cda1636300059c05e9d11c05f365438a | ELF32 | ARM | 7c118dfba29facffe8692a1c2e2a69e9 | libradio_metadata.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 197 | top | libselinux.so | 51424 | aed0cdf6798a984d237369ed76396d8f50f787d34e5adfa4284c851110307dfc | ELF32 | ARM | a4b4fdc14e45222ea2a76ed2ff53d17e | libselinux.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 198 | top | libsoftkeymasterdevice.so | 138724 | fab8e2da6aa6a897d4a27d160a31d6605ba92728f65c8d0257be3a6b548417a5 | ELF32 | ARM | 2ae5fc17475ba00269790df0126ad7ba | libsoftkeymasterdevice.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 199 | top | libspeexresampler.so | 24088 | 3b2b278102f42720370ac916792f117b4fb2941888211b088ceb20bcd3588758 | ELF32 | ARM | 582a95f8bd3f1bc099b362bf9256580f | libspeexresampler.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 200 | top | libsqlite.so | 820840 | 80700004433d967b057d3a885ba4bdafe19742f3ba41eafbd02754d9f49fdc4f | ELF32 | ARM | 6048c25f8399f010a38f6249fc7b260c | libsqlite.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 201 | top | libssl.so | 343932 | be34fa75c4b72d1db3ccffa7dc35e491ac6137c839a0cb0b19fd32962d1445a6 | ELF32 | ARM | 8dffeebff4dbca6b6d7588470885b8b9 | libssl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 202 | top | libstagefright_bufferpool@2.0.so | 133012 | 67a54a4976eb13e9bb37d5dcd793d872abaf748bb66d114f1f996dec3316b8e1 | ELF32 | ARM | 0994a00285f522e17f1d47badf181026 | libstagefright_bufferpool@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 203 | top | libstagefright_bufferqueue_helper.so | 393868 | a734c458454491f79a4e0e7e9f20f6720df6090a75de186a8403e0893cd178ed | ELF32 | ARM | 272ca7696ee04c22a16819fc614c5c28 | libstagefright_bufferqueue_helper.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 204 | top | libstagefright_foundation.so | 131908 | 57f8e868b9fe88c9c5f20ee989979f10034f7356ec385bd71e93d99979c059f4 | ELF32 | ARM | 0a988ab80774438e90d599c5dae0cdfe | libstagefright_foundation.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 205 | top | libstagefright_omx.so | 208444 | 985941600fa0ecd24f818ff9e8d64f1be5e952ec6dce248637a44866826d5f33 | ELF32 | ARM | d17f34794cd0dd866e0f8d3d945910dd | libstagefright_omx.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 206 | top | libstagefright_omx_utils.so | 19584 | 5b56eec34ca4df6f3ccddd501e19a1f3a8a27080c6de1609f5b58ebeb6d25c51 | ELF32 | ARM | 8033809be4cdf4f661ba7fc4b05dfcdd | libstagefright_omx_utils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 207 | top | libstagefright_xmlparser.so | 79100 | 319bb868ebc3d4db4c8ca836a9054df4a70b36af5bd0781f466e1ab8021142be | ELF32 | ARM | 324b54037a6bf906563eb729250b6f38 | libstagefright_xmlparser.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 208 | top | libsysutils.so | 41228 | 0b02a487e7a3a142b035c918f44024e67cf56649e8d058149b8172ef6ad8e7cb | ELF32 | ARM | 665a00c577fa1c6aabbdaad11af5ad19 | libsysutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 209 | top | libtinyalsa.so | 28252 | 6daac3a5ca1999f835e8126042b87303d975346d7a092f976ba612b62e0c8331 | ELF32 | ARM | 911fa3da769350ea67a240bf4c8759a1 | libtinyalsa.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 210 | top | libtinyxml2.so | 55252 | 356e0d0a391df633ff8a91c74640b10b78f476176eb7cfe1b14195aff5bb4922 | ELF32 | ARM | 08fe9877d141aba51c5f32b8583312f0 | libtinyxml2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 211 | top | libui.so | 133952 | 586579a28d566e4dd23c76fdc7d32e2f890a06d37a953b78e418ce91cd0c0cb4 | ELF32 | ARM | 8d5ef499772316b9450360aa34a9b1c1 | libui.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 212 | top | libusbhost.so | 23704 | 1d7f096e0afc354da7cfee2f5822b0650310b3ec17eaf1be97501dd36eea0380 | ELF32 | ARM | 161df488ca01537b933132d0fcf9df17 | libusbhost.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 213 | top | libwifi-system-iface.so | 15524 | 1e80f4cddde02513ee7ccd5c016698f506d401a1eb53f98e69f63968033c6775 | ELF32 | ARM | d3f44ef5518de913976a9f6be0dce51c | libwifi-system-iface.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 214 | top | libxml2.so | 942092 | 16675bdb3d9f49d641fa2837dc23626c25e1a739a3d7bbfeb5d21a1045f8c7c6 | ELF32 | ARM | 0a769ed042b9b1f6aba2eeb9622511fe | libxml2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 215 | top | libyuv.so | 215080 | 65722180bdfa256e7c37649f58b87e5128dbef4f4a9960e8e253554a2fea4296 | ELF32 | ARM | ed833d2c8e9b64df2664ff870f677d6a | libyuv.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |
| 216 | top | libziparchive.so | 45700 | ff2b710ed06407a55be55e49a6bcca5d7259745d013c398e5bf368cdcbf7affc | ELF32 | ARM | b658a25d72bdc590bc0909486cf90f2d | libziparchive.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK |

Path provenance table:

| ID | Library | Live runtime path | Static stock source path |
| --- | --- | --- | --- |
| 1 | android.frameworks.cameraservice.common@2.0.so | /system/lib/vndk-29/android.frameworks.cameraservice.common@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.cameraservice.common@2.0.so |
| 2 | android.frameworks.cameraservice.device@2.0.so | /system/lib/vndk-29/android.frameworks.cameraservice.device@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.cameraservice.device@2.0.so |
| 3 | android.frameworks.cameraservice.service@2.0.so | /system/lib/vndk-29/android.frameworks.cameraservice.service@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.cameraservice.service@2.0.so |
| 4 | android.frameworks.displayservice@1.0.so | /system/lib/vndk-29/android.frameworks.displayservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.displayservice@1.0.so |
| 5 | android.frameworks.schedulerservice@1.0.so | /system/lib/vndk-29/android.frameworks.schedulerservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.schedulerservice@1.0.so |
| 6 | android.frameworks.sensorservice@1.0.so | /system/lib/vndk-29/android.frameworks.sensorservice@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.sensorservice@1.0.so |
| 7 | android.frameworks.stats@1.0.so | /system/lib/vndk-29/android.frameworks.stats@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.stats@1.0.so |
| 8 | android.frameworks.vr.composer@1.0.so | /system/lib/vndk-29/android.frameworks.vr.composer@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.frameworks.vr.composer@1.0.so |
| 9 | android.hardware.atrace@1.0.so | /system/lib/vndk-29/android.hardware.atrace@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.atrace@1.0.so |
| 10 | android.hardware.audio.common@2.0.so | /system/lib/vndk-29/android.hardware.audio.common@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.common@2.0.so |
| 11 | android.hardware.audio.common@4.0.so | /system/lib/vndk-29/android.hardware.audio.common@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.common@4.0.so |
| 12 | android.hardware.audio.common@5.0.so | /system/lib/vndk-29/android.hardware.audio.common@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.common@5.0.so |
| 13 | android.hardware.audio.effect@2.0.so | /system/lib/vndk-29/android.hardware.audio.effect@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.effect@2.0.so |
| 14 | android.hardware.audio.effect@4.0.so | /system/lib/vndk-29/android.hardware.audio.effect@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.effect@4.0.so |
| 15 | android.hardware.audio.effect@5.0.so | /system/lib/vndk-29/android.hardware.audio.effect@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio.effect@5.0.so |
| 16 | android.hardware.audio@2.0.so | /system/lib/vndk-29/android.hardware.audio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio@2.0.so |
| 17 | android.hardware.audio@4.0.so | /system/lib/vndk-29/android.hardware.audio@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio@4.0.so |
| 18 | android.hardware.audio@5.0.so | /system/lib/vndk-29/android.hardware.audio@5.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.audio@5.0.so |
| 19 | android.hardware.authsecret@1.0.so | /system/lib/vndk-29/android.hardware.authsecret@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.authsecret@1.0.so |
| 20 | android.hardware.automotive.audiocontrol@1.0.so | /system/lib/vndk-29/android.hardware.automotive.audiocontrol@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.automotive.audiocontrol@1.0.so |
| 21 | android.hardware.automotive.evs@1.0.so | /system/lib/vndk-29/android.hardware.automotive.evs@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.automotive.evs@1.0.so |
| 22 | android.hardware.automotive.vehicle@2.0.so | /system/lib/vndk-29/android.hardware.automotive.vehicle@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.automotive.vehicle@2.0.so |
| 23 | android.hardware.biometrics.face@1.0.so | /system/lib/vndk-29/android.hardware.biometrics.face@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.biometrics.face@1.0.so |
| 24 | android.hardware.biometrics.fingerprint@2.1.so | /system/lib/vndk-29/android.hardware.biometrics.fingerprint@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.biometrics.fingerprint@2.1.so |
| 25 | android.hardware.bluetooth.a2dp@1.0.so | /system/lib/vndk-29/android.hardware.bluetooth.a2dp@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.bluetooth.a2dp@1.0.so |
| 26 | android.hardware.bluetooth.audio@2.0.so | /system/lib/vndk-29/android.hardware.bluetooth.audio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.bluetooth.audio@2.0.so |
| 27 | android.hardware.bluetooth@1.0.so | /system/lib/vndk-29/android.hardware.bluetooth@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.bluetooth@1.0.so |
| 28 | android.hardware.boot@1.0.so | /system/lib/vndk-29/android.hardware.boot@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.boot@1.0.so |
| 29 | android.hardware.broadcastradio@1.0.so | /system/lib/vndk-29/android.hardware.broadcastradio@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.broadcastradio@1.0.so |
| 30 | android.hardware.broadcastradio@1.1.so | /system/lib/vndk-29/android.hardware.broadcastradio@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.broadcastradio@1.1.so |
| 31 | android.hardware.broadcastradio@2.0.so | /system/lib/vndk-29/android.hardware.broadcastradio@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.broadcastradio@2.0.so |
| 32 | android.hardware.camera.common@1.0.so | /system/lib/vndk-29/android.hardware.camera.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.common@1.0.so |
| 33 | android.hardware.camera.device@1.0.so | /system/lib/vndk-29/android.hardware.camera.device@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.device@1.0.so |
| 34 | android.hardware.camera.device@3.2.so | /system/lib/vndk-29/android.hardware.camera.device@3.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.device@3.2.so |
| 35 | android.hardware.camera.device@3.3.so | /system/lib/vndk-29/android.hardware.camera.device@3.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.device@3.3.so |
| 36 | android.hardware.camera.device@3.4.so | /system/lib/vndk-29/android.hardware.camera.device@3.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.device@3.4.so |
| 37 | android.hardware.camera.device@3.5.so | /system/lib/vndk-29/android.hardware.camera.device@3.5.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.device@3.5.so |
| 38 | android.hardware.camera.metadata@3.2.so | /system/lib/vndk-29/android.hardware.camera.metadata@3.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.metadata@3.2.so |
| 39 | android.hardware.camera.metadata@3.3.so | /system/lib/vndk-29/android.hardware.camera.metadata@3.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.metadata@3.3.so |
| 40 | android.hardware.camera.metadata@3.4.so | /system/lib/vndk-29/android.hardware.camera.metadata@3.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.metadata@3.4.so |
| 41 | android.hardware.camera.provider@2.4.so | /system/lib/vndk-29/android.hardware.camera.provider@2.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.provider@2.4.so |
| 42 | android.hardware.camera.provider@2.5.so | /system/lib/vndk-29/android.hardware.camera.provider@2.5.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.camera.provider@2.5.so |
| 43 | android.hardware.cas.native@1.0.so | /system/lib/vndk-29/android.hardware.cas.native@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.cas.native@1.0.so |
| 44 | android.hardware.cas@1.0.so | /system/lib/vndk-29/android.hardware.cas@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.cas@1.0.so |
| 45 | android.hardware.cas@1.1.so | /system/lib/vndk-29/android.hardware.cas@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.cas@1.1.so |
| 46 | android.hardware.configstore-utils.so | /system/lib/vndk-29/android.hardware.configstore-utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.configstore-utils.so |
| 47 | android.hardware.configstore@1.0.so | /system/lib/vndk-29/android.hardware.configstore@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.configstore@1.0.so |
| 48 | android.hardware.configstore@1.1.so | /system/lib/vndk-29/android.hardware.configstore@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.configstore@1.1.so |
| 49 | android.hardware.confirmationui-support-lib.so | /system/lib/vndk-29/android.hardware.confirmationui-support-lib.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.confirmationui-support-lib.so |
| 50 | android.hardware.confirmationui@1.0.so | /system/lib/vndk-29/android.hardware.confirmationui@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.confirmationui@1.0.so |
| 51 | android.hardware.contexthub@1.0.so | /system/lib/vndk-29/android.hardware.contexthub@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.contexthub@1.0.so |
| 52 | android.hardware.drm@1.0.so | /system/lib/vndk-29/android.hardware.drm@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.drm@1.0.so |
| 53 | android.hardware.drm@1.1.so | /system/lib/vndk-29/android.hardware.drm@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.drm@1.1.so |
| 54 | android.hardware.drm@1.2.so | /system/lib/vndk-29/android.hardware.drm@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.drm@1.2.so |
| 55 | android.hardware.dumpstate@1.0.so | /system/lib/vndk-29/android.hardware.dumpstate@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.dumpstate@1.0.so |
| 56 | android.hardware.fastboot@1.0.so | /system/lib/vndk-29/android.hardware.fastboot@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.fastboot@1.0.so |
| 57 | android.hardware.gatekeeper@1.0.so | /system/lib/vndk-29/android.hardware.gatekeeper@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gatekeeper@1.0.so |
| 58 | android.hardware.gnss.measurement_corrections@1.0.so | /system/lib/vndk-29/android.hardware.gnss.measurement_corrections@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gnss.measurement_corrections@1.0.so |
| 59 | android.hardware.gnss.visibility_control@1.0.so | /system/lib/vndk-29/android.hardware.gnss.visibility_control@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gnss.visibility_control@1.0.so |
| 60 | android.hardware.gnss@1.0.so | /system/lib/vndk-29/android.hardware.gnss@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gnss@1.0.so |
| 61 | android.hardware.gnss@1.1.so | /system/lib/vndk-29/android.hardware.gnss@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gnss@1.1.so |
| 62 | android.hardware.gnss@2.0.so | /system/lib/vndk-29/android.hardware.gnss@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.gnss@2.0.so |
| 63 | android.hardware.graphics.allocator@2.0.so | /system/lib/vndk-29/android.hardware.graphics.allocator@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.allocator@2.0.so |
| 64 | android.hardware.graphics.allocator@3.0.so | /system/lib/vndk-29/android.hardware.graphics.allocator@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.allocator@3.0.so |
| 65 | android.hardware.graphics.bufferqueue@1.0.so | /system/lib/vndk-29/android.hardware.graphics.bufferqueue@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.bufferqueue@1.0.so |
| 66 | android.hardware.graphics.bufferqueue@2.0.so | /system/lib/vndk-29/android.hardware.graphics.bufferqueue@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.bufferqueue@2.0.so |
| 67 | android.hardware.graphics.composer@2.1.so | /system/lib/vndk-29/android.hardware.graphics.composer@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.composer@2.1.so |
| 68 | android.hardware.graphics.composer@2.2.so | /system/lib/vndk-29/android.hardware.graphics.composer@2.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.composer@2.2.so |
| 69 | android.hardware.graphics.composer@2.3.so | /system/lib/vndk-29/android.hardware.graphics.composer@2.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.graphics.composer@2.3.so |
| 70 | android.hardware.health.storage@1.0.so | /system/lib/vndk-29/android.hardware.health.storage@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.health.storage@1.0.so |
| 71 | android.hardware.health@1.0.so | /system/lib/vndk-29/android.hardware.health@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.health@1.0.so |
| 72 | android.hardware.health@2.0.so | /system/lib/vndk-29/android.hardware.health@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.health@2.0.so |
| 73 | android.hardware.input.classifier@1.0.so | /system/lib/vndk-29/android.hardware.input.classifier@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.input.classifier@1.0.so |
| 74 | android.hardware.input.common@1.0.so | /system/lib/vndk-29/android.hardware.input.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.input.common@1.0.so |
| 75 | android.hardware.ir@1.0.so | /system/lib/vndk-29/android.hardware.ir@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.ir@1.0.so |
| 76 | android.hardware.keymaster@3.0.so | /system/lib/vndk-29/android.hardware.keymaster@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.keymaster@3.0.so |
| 77 | android.hardware.keymaster@4.0.so | /system/lib/vndk-29/android.hardware.keymaster@4.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.keymaster@4.0.so |
| 78 | android.hardware.light@2.0.so | /system/lib/vndk-29/android.hardware.light@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.light@2.0.so |
| 79 | android.hardware.media.bufferpool@1.0.so | /system/lib/vndk-29/android.hardware.media.bufferpool@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.media.bufferpool@1.0.so |
| 80 | android.hardware.media.bufferpool@2.0.so | /system/lib/vndk-29/android.hardware.media.bufferpool@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.media.bufferpool@2.0.so |
| 81 | android.hardware.media.c2@1.0.so | /system/lib/vndk-29/android.hardware.media.c2@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.media.c2@1.0.so |
| 82 | android.hardware.media.omx@1.0.so | /system/lib/vndk-29/android.hardware.media.omx@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.media.omx@1.0.so |
| 83 | android.hardware.media@1.0.so | /system/lib/vndk-29/android.hardware.media@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.media@1.0.so |
| 84 | android.hardware.memtrack@1.0.so | /system/lib/vndk-29/android.hardware.memtrack@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.memtrack@1.0.so |
| 85 | android.hardware.neuralnetworks@1.0.so | /system/lib/vndk-29/android.hardware.neuralnetworks@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.neuralnetworks@1.0.so |
| 86 | android.hardware.neuralnetworks@1.1.so | /system/lib/vndk-29/android.hardware.neuralnetworks@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.neuralnetworks@1.1.so |
| 87 | android.hardware.neuralnetworks@1.2.so | /system/lib/vndk-29/android.hardware.neuralnetworks@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.neuralnetworks@1.2.so |
| 88 | android.hardware.nfc@1.0.so | /system/lib/vndk-29/android.hardware.nfc@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.nfc@1.0.so |
| 89 | android.hardware.nfc@1.1.so | /system/lib/vndk-29/android.hardware.nfc@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.nfc@1.1.so |
| 90 | android.hardware.nfc@1.2.so | /system/lib/vndk-29/android.hardware.nfc@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.nfc@1.2.so |
| 91 | android.hardware.oemlock@1.0.so | /system/lib/vndk-29/android.hardware.oemlock@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.oemlock@1.0.so |
| 92 | android.hardware.power.stats@1.0.so | /system/lib/vndk-29/android.hardware.power.stats@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.power.stats@1.0.so |
| 93 | android.hardware.power@1.0.so | /system/lib/vndk-29/android.hardware.power@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.power@1.0.so |
| 94 | android.hardware.power@1.1.so | /system/lib/vndk-29/android.hardware.power@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.power@1.1.so |
| 95 | android.hardware.power@1.2.so | /system/lib/vndk-29/android.hardware.power@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.power@1.2.so |
| 96 | android.hardware.power@1.3.so | /system/lib/vndk-29/android.hardware.power@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.power@1.3.so |
| 97 | android.hardware.radio.config@1.0.so | /system/lib/vndk-29/android.hardware.radio.config@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio.config@1.0.so |
| 98 | android.hardware.radio.config@1.1.so | /system/lib/vndk-29/android.hardware.radio.config@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio.config@1.1.so |
| 99 | android.hardware.radio.config@1.2.so | /system/lib/vndk-29/android.hardware.radio.config@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio.config@1.2.so |
| 100 | android.hardware.radio.deprecated@1.0.so | /system/lib/vndk-29/android.hardware.radio.deprecated@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio.deprecated@1.0.so |
| 101 | android.hardware.radio@1.0.so | /system/lib/vndk-29/android.hardware.radio@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio@1.0.so |
| 102 | android.hardware.radio@1.1.so | /system/lib/vndk-29/android.hardware.radio@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio@1.1.so |
| 103 | android.hardware.radio@1.2.so | /system/lib/vndk-29/android.hardware.radio@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio@1.2.so |
| 104 | android.hardware.radio@1.3.so | /system/lib/vndk-29/android.hardware.radio@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio@1.3.so |
| 105 | android.hardware.radio@1.4.so | /system/lib/vndk-29/android.hardware.radio@1.4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.radio@1.4.so |
| 106 | android.hardware.secure_element@1.0.so | /system/lib/vndk-29/android.hardware.secure_element@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.secure_element@1.0.so |
| 107 | android.hardware.secure_element@1.1.so | /system/lib/vndk-29/android.hardware.secure_element@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.secure_element@1.1.so |
| 108 | android.hardware.sensors@1.0.so | /system/lib/vndk-29/android.hardware.sensors@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.sensors@1.0.so |
| 109 | android.hardware.sensors@2.0.so | /system/lib/vndk-29/android.hardware.sensors@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.sensors@2.0.so |
| 110 | android.hardware.soundtrigger@2.0-core.so | /system/lib/vndk-29/android.hardware.soundtrigger@2.0-core.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.soundtrigger@2.0-core.so |
| 111 | android.hardware.soundtrigger@2.0.so | /system/lib/vndk-29/android.hardware.soundtrigger@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.soundtrigger@2.0.so |
| 112 | android.hardware.soundtrigger@2.1.so | /system/lib/vndk-29/android.hardware.soundtrigger@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.soundtrigger@2.1.so |
| 113 | android.hardware.soundtrigger@2.2.so | /system/lib/vndk-29/android.hardware.soundtrigger@2.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.soundtrigger@2.2.so |
| 114 | android.hardware.tetheroffload.config@1.0.so | /system/lib/vndk-29/android.hardware.tetheroffload.config@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.tetheroffload.config@1.0.so |
| 115 | android.hardware.tetheroffload.control@1.0.so | /system/lib/vndk-29/android.hardware.tetheroffload.control@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.tetheroffload.control@1.0.so |
| 116 | android.hardware.thermal@1.0.so | /system/lib/vndk-29/android.hardware.thermal@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.thermal@1.0.so |
| 117 | android.hardware.thermal@1.1.so | /system/lib/vndk-29/android.hardware.thermal@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.thermal@1.1.so |
| 118 | android.hardware.thermal@2.0.so | /system/lib/vndk-29/android.hardware.thermal@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.thermal@2.0.so |
| 119 | android.hardware.tv.cec@1.0.so | /system/lib/vndk-29/android.hardware.tv.cec@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.tv.cec@1.0.so |
| 120 | android.hardware.tv.cec@2.0.so | /system/lib/vndk-29/android.hardware.tv.cec@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.tv.cec@2.0.so |
| 121 | android.hardware.tv.input@1.0.so | /system/lib/vndk-29/android.hardware.tv.input@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.tv.input@1.0.so |
| 122 | android.hardware.usb.gadget@1.0.so | /system/lib/vndk-29/android.hardware.usb.gadget@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.usb.gadget@1.0.so |
| 123 | android.hardware.usb@1.0.so | /system/lib/vndk-29/android.hardware.usb@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.usb@1.0.so |
| 124 | android.hardware.usb@1.1.so | /system/lib/vndk-29/android.hardware.usb@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.usb@1.1.so |
| 125 | android.hardware.usb@1.2.so | /system/lib/vndk-29/android.hardware.usb@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.usb@1.2.so |
| 126 | android.hardware.vibrator@1.0.so | /system/lib/vndk-29/android.hardware.vibrator@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.vibrator@1.0.so |
| 127 | android.hardware.vibrator@1.1.so | /system/lib/vndk-29/android.hardware.vibrator@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.vibrator@1.1.so |
| 128 | android.hardware.vibrator@1.2.so | /system/lib/vndk-29/android.hardware.vibrator@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.vibrator@1.2.so |
| 129 | android.hardware.vibrator@1.3.so | /system/lib/vndk-29/android.hardware.vibrator@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.vibrator@1.3.so |
| 130 | android.hardware.vr@1.0.so | /system/lib/vndk-29/android.hardware.vr@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.vr@1.0.so |
| 131 | android.hardware.weaver@1.0.so | /system/lib/vndk-29/android.hardware.weaver@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.weaver@1.0.so |
| 132 | android.hardware.wifi.hostapd@1.0.so | /system/lib/vndk-29/android.hardware.wifi.hostapd@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.hostapd@1.0.so |
| 133 | android.hardware.wifi.hostapd@1.1.so | /system/lib/vndk-29/android.hardware.wifi.hostapd@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.hostapd@1.1.so |
| 134 | android.hardware.wifi.offload@1.0.so | /system/lib/vndk-29/android.hardware.wifi.offload@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.offload@1.0.so |
| 135 | android.hardware.wifi.supplicant@1.0.so | /system/lib/vndk-29/android.hardware.wifi.supplicant@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.supplicant@1.0.so |
| 136 | android.hardware.wifi.supplicant@1.1.so | /system/lib/vndk-29/android.hardware.wifi.supplicant@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.supplicant@1.1.so |
| 137 | android.hardware.wifi.supplicant@1.2.so | /system/lib/vndk-29/android.hardware.wifi.supplicant@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi.supplicant@1.2.so |
| 138 | android.hardware.wifi@1.0.so | /system/lib/vndk-29/android.hardware.wifi@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi@1.0.so |
| 139 | android.hardware.wifi@1.1.so | /system/lib/vndk-29/android.hardware.wifi@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi@1.1.so |
| 140 | android.hardware.wifi@1.2.so | /system/lib/vndk-29/android.hardware.wifi@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi@1.2.so |
| 141 | android.hardware.wifi@1.3.so | /system/lib/vndk-29/android.hardware.wifi@1.3.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hardware.wifi@1.3.so |
| 142 | android.hidl.allocator@1.0.so | /system/lib/vndk-29/android.hidl.allocator@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hidl.allocator@1.0.so |
| 143 | android.hidl.memory.block@1.0.so | /system/lib/vndk-29/android.hidl.memory.block@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hidl.memory.block@1.0.so |
| 144 | android.hidl.token@1.0-utils.so | /system/lib/vndk-29/android.hidl.token@1.0-utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hidl.token@1.0-utils.so |
| 145 | android.hidl.token@1.0.so | /system/lib/vndk-29/android.hidl.token@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.hidl.token@1.0.so |
| 146 | android.system.net.netd@1.0.so | /system/lib/vndk-29/android.system.net.netd@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.system.net.netd@1.0.so |
| 147 | android.system.net.netd@1.1.so | /system/lib/vndk-29/android.system.net.netd@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.system.net.netd@1.1.so |
| 148 | android.system.suspend@1.0.so | /system/lib/vndk-29/android.system.suspend@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.system.suspend@1.0.so |
| 149 | android.system.wifi.keystore@1.0.so | /system/lib/vndk-29/android.system.wifi.keystore@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/android.system.wifi.keystore@1.0.so |
| 150 | libadf.so | /system/lib/vndk-29/libadf.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libadf.so |
| 151 | libaudioroute.so | /system/lib/vndk-29/libaudioroute.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libaudioroute.so |
| 152 | libaudioutils.so | /system/lib/vndk-29/libaudioutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libaudioutils.so |
| 153 | libbinder.so | /system/lib/vndk-29/libbinder.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libbinder.so |
| 154 | libcamera_metadata.so | /system/lib/vndk-29/libcamera_metadata.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcamera_metadata.so |
| 155 | libcap.so | /system/lib/vndk-29/libcap.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcap.so |
| 156 | libclang_rt.scudo-arm-android.so | /system/lib/vndk-29/libclang_rt.scudo-arm-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libclang_rt.scudo-arm-android.so |
| 157 | libclang_rt.scudo_minimal-arm-android.so | /system/lib/vndk-29/libclang_rt.scudo_minimal-arm-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libclang_rt.scudo_minimal-arm-android.so |
| 158 | libclang_rt.ubsan_standalone-arm-android.so | /system/lib/vndk-29/libclang_rt.ubsan_standalone-arm-android.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libclang_rt.ubsan_standalone-arm-android.so |
| 159 | libcn-cbor.so | /system/lib/vndk-29/libcn-cbor.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcn-cbor.so |
| 160 | libcodec2.so | /system/lib/vndk-29/libcodec2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcodec2.so |
| 161 | libcrypto.so | /system/lib/vndk-29/libcrypto.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcrypto.so |
| 162 | libcrypto_utils.so | /system/lib/vndk-29/libcrypto_utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcrypto_utils.so |
| 163 | libcurl.so | /system/lib/vndk-29/libcurl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libcurl.so |
| 164 | libdiskconfig.so | /system/lib/vndk-29/libdiskconfig.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libdiskconfig.so |
| 165 | libdumpstateutil.so | /system/lib/vndk-29/libdumpstateutil.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libdumpstateutil.so |
| 166 | libevent.so | /system/lib/vndk-29/libevent.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libevent.so |
| 167 | libexif.so | /system/lib/vndk-29/libexif.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libexif.so |
| 168 | libexpat.so | /system/lib/vndk-29/libexpat.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libexpat.so |
| 169 | libfmq.so | /system/lib/vndk-29/libfmq.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libfmq.so |
| 170 | libgatekeeper.so | /system/lib/vndk-29/libgatekeeper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libgatekeeper.so |
| 171 | libgui.so | /system/lib/vndk-29/libgui.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libgui.so |
| 172 | libhardware_legacy.so | /system/lib/vndk-29/libhardware_legacy.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libhardware_legacy.so |
| 173 | libhidlallocatorutils.so | /system/lib/vndk-29/libhidlallocatorutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libhidlallocatorutils.so |
| 174 | libhidlcache.so | /system/lib/vndk-29/libhidlcache.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libhidlcache.so |
| 175 | libjpeg.so | /system/lib/vndk-29/libjpeg.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libjpeg.so |
| 176 | libkeymaster_messages.so | /system/lib/vndk-29/libkeymaster_messages.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libkeymaster_messages.so |
| 177 | libkeymaster_portable.so | /system/lib/vndk-29/libkeymaster_portable.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libkeymaster_portable.so |
| 178 | libldacBT_abr.so | /system/lib/vndk-29/libldacBT_abr.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libldacBT_abr.so |
| 179 | libldacBT_enc.so | /system/lib/vndk-29/libldacBT_enc.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libldacBT_enc.so |
| 180 | liblz4.so | /system/lib/vndk-29/liblz4.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/liblz4.so |
| 181 | libmedia_helper.so | /system/lib/vndk-29/libmedia_helper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libmedia_helper.so |
| 182 | libmedia_omx.so | /system/lib/vndk-29/libmedia_omx.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libmedia_omx.so |
| 183 | libmemtrack.so | /system/lib/vndk-29/libmemtrack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libmemtrack.so |
| 184 | libminijail.so | /system/lib/vndk-29/libminijail.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libminijail.so |
| 185 | libmkbootimg_abi_check.so | /system/lib/vndk-29/libmkbootimg_abi_check.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libmkbootimg_abi_check.so |
| 186 | libnetutils.so | /system/lib/vndk-29/libnetutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libnetutils.so |
| 187 | libnl.so | /system/lib/vndk-29/libnl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libnl.so |
| 188 | libpcre2.so | /system/lib/vndk-29/libpcre2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libpcre2.so |
| 189 | libpiex.so | /system/lib/vndk-29/libpiex.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libpiex.so |
| 190 | libpng.so | /system/lib/vndk-29/libpng.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libpng.so |
| 191 | libpower.so | /system/lib/vndk-29/libpower.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libpower.so |
| 192 | libprocinfo.so | /system/lib/vndk-29/libprocinfo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libprocinfo.so |
| 193 | libprotobuf-cpp-full.so | /system/lib/vndk-29/libprotobuf-cpp-full.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libprotobuf-cpp-full.so |
| 194 | libprotobuf-cpp-lite.so | /system/lib/vndk-29/libprotobuf-cpp-lite.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libprotobuf-cpp-lite.so |
| 195 | libpuresoftkeymasterdevice.so | /system/lib/vndk-29/libpuresoftkeymasterdevice.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libpuresoftkeymasterdevice.so |
| 196 | libradio_metadata.so | /system/lib/vndk-29/libradio_metadata.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libradio_metadata.so |
| 197 | libselinux.so | /system/lib/vndk-29/libselinux.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libselinux.so |
| 198 | libsoftkeymasterdevice.so | /system/lib/vndk-29/libsoftkeymasterdevice.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libsoftkeymasterdevice.so |
| 199 | libspeexresampler.so | /system/lib/vndk-29/libspeexresampler.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libspeexresampler.so |
| 200 | libsqlite.so | /system/lib/vndk-29/libsqlite.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libsqlite.so |
| 201 | libssl.so | /system/lib/vndk-29/libssl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libssl.so |
| 202 | libstagefright_bufferpool@2.0.so | /system/lib/vndk-29/libstagefright_bufferpool@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_bufferpool@2.0.so |
| 203 | libstagefright_bufferqueue_helper.so | /system/lib/vndk-29/libstagefright_bufferqueue_helper.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_bufferqueue_helper.so |
| 204 | libstagefright_foundation.so | /system/lib/vndk-29/libstagefright_foundation.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_foundation.so |
| 205 | libstagefright_omx.so | /system/lib/vndk-29/libstagefright_omx.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_omx.so |
| 206 | libstagefright_omx_utils.so | /system/lib/vndk-29/libstagefright_omx_utils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_omx_utils.so |
| 207 | libstagefright_xmlparser.so | /system/lib/vndk-29/libstagefright_xmlparser.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libstagefright_xmlparser.so |
| 208 | libsysutils.so | /system/lib/vndk-29/libsysutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libsysutils.so |
| 209 | libtinyalsa.so | /system/lib/vndk-29/libtinyalsa.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libtinyalsa.so |
| 210 | libtinyxml2.so | /system/lib/vndk-29/libtinyxml2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libtinyxml2.so |
| 211 | libui.so | /system/lib/vndk-29/libui.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libui.so |
| 212 | libusbhost.so | /system/lib/vndk-29/libusbhost.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libusbhost.so |
| 213 | libwifi-system-iface.so | /system/lib/vndk-29/libwifi-system-iface.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libwifi-system-iface.so |
| 214 | libxml2.so | /system/lib/vndk-29/libxml2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libxml2.so |
| 215 | libyuv.so | /system/lib/vndk-29/libyuv.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libyuv.so |
| 216 | libziparchive.so | /system/lib/vndk-29/libziparchive.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-29/libziparchive.so |

DT_NEEDED table:

| ID | Library | DT_NEEDED |
| --- | --- | --- |
| 1 | android.frameworks.cameraservice.common@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 2 | android.frameworks.cameraservice.device@2.0.so | android.frameworks.cameraservice.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 3 | android.frameworks.cameraservice.service@2.0.so | android.frameworks.cameraservice.common@2.0.so<br>android.frameworks.cameraservice.device@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 4 | android.frameworks.displayservice@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 5 | android.frameworks.schedulerservice@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 6 | android.frameworks.sensorservice@1.0.so | android.hardware.sensors@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 7 | android.frameworks.stats@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 8 | android.frameworks.vr.composer@1.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.composer@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 9 | android.hardware.atrace@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 10 | android.hardware.audio.common@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 11 | android.hardware.audio.common@4.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 12 | android.hardware.audio.common@5.0.so | android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 13 | android.hardware.audio.effect@2.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 14 | android.hardware.audio.effect@4.0.so | android.hardware.audio.common@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 15 | android.hardware.audio.effect@5.0.so | android.hardware.audio.common@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 16 | android.hardware.audio@2.0.so | android.hardware.audio.common@2.0.so<br>android.hardware.audio.effect@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 17 | android.hardware.audio@4.0.so | android.hardware.audio.common@4.0.so<br>android.hardware.audio.effect@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 18 | android.hardware.audio@5.0.so | android.hardware.audio.common@5.0.so<br>android.hardware.audio.effect@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 19 | android.hardware.authsecret@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 20 | android.hardware.automotive.audiocontrol@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 21 | android.hardware.automotive.evs@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 22 | android.hardware.automotive.vehicle@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 23 | android.hardware.biometrics.face@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 24 | android.hardware.biometrics.fingerprint@2.1.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 25 | android.hardware.bluetooth.a2dp@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 26 | android.hardware.bluetooth.audio@2.0.so | android.hardware.audio.common@5.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 27 | android.hardware.bluetooth@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 28 | android.hardware.boot@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 29 | android.hardware.broadcastradio@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 30 | android.hardware.broadcastradio@1.1.so | android.hardware.broadcastradio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 31 | android.hardware.broadcastradio@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 32 | android.hardware.camera.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 33 | android.hardware.camera.device@1.0.so | android.hardware.camera.common@1.0.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 34 | android.hardware.camera.device@3.2.so | android.hardware.camera.common@1.0.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 35 | android.hardware.camera.device@3.3.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 36 | android.hardware.camera.device@3.4.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.device@3.3.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 37 | android.hardware.camera.device@3.5.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.device@3.3.so<br>android.hardware.camera.device@3.4.so<br>android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 38 | android.hardware.camera.metadata@3.2.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 39 | android.hardware.camera.metadata@3.3.so | android.hardware.camera.metadata@3.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 40 | android.hardware.camera.metadata@3.4.so | android.hardware.camera.metadata@3.2.so<br>android.hardware.camera.metadata@3.3.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 41 | android.hardware.camera.provider@2.4.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@1.0.so<br>android.hardware.camera.device@3.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 42 | android.hardware.camera.provider@2.5.so | android.hardware.camera.common@1.0.so<br>android.hardware.camera.device@1.0.so<br>android.hardware.camera.device@3.2.so<br>android.hardware.camera.provider@2.4.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 43 | android.hardware.cas.native@1.0.so | android.hardware.cas@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 44 | android.hardware.cas@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 45 | android.hardware.cas@1.1.so | android.hardware.cas@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 46 | android.hardware.configstore-utils.so | android.hardware.configstore@1.0.so<br>android.hardware.configstore@1.1.so<br>libbase.so<br>libhidlbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 47 | android.hardware.configstore@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 48 | android.hardware.configstore@1.1.so | android.hardware.configstore@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 49 | android.hardware.confirmationui-support-lib.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 50 | android.hardware.confirmationui@1.0.so | android.hardware.keymaster@4.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 51 | android.hardware.contexthub@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 52 | android.hardware.drm@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 53 | android.hardware.drm@1.1.so | android.hardware.drm@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 54 | android.hardware.drm@1.2.so | android.hardware.drm@1.0.so<br>android.hardware.drm@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 55 | android.hardware.dumpstate@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 56 | android.hardware.fastboot@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 57 | android.hardware.gatekeeper@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 58 | android.hardware.gnss.measurement_corrections@1.0.so | android.hardware.gnss@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 59 | android.hardware.gnss.visibility_control@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 60 | android.hardware.gnss@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 61 | android.hardware.gnss@1.1.so | android.hardware.gnss@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 62 | android.hardware.gnss@2.0.so | android.hardware.gnss.measurement_corrections@1.0.so<br>android.hardware.gnss.visibility_control@1.0.so<br>android.hardware.gnss@1.0.so<br>android.hardware.gnss@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 63 | android.hardware.graphics.allocator@2.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.mapper@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 64 | android.hardware.graphics.allocator@3.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.mapper@3.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 65 | android.hardware.graphics.bufferqueue@1.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.media@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 66 | android.hardware.graphics.bufferqueue@2.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 67 | android.hardware.graphics.composer@2.1.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 68 | android.hardware.graphics.composer@2.2.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.composer@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 69 | android.hardware.graphics.composer@2.3.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.composer@2.1.so<br>android.hardware.graphics.composer@2.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 70 | android.hardware.health.storage@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 71 | android.hardware.health@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 72 | android.hardware.health@2.0.so | android.hardware.health@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 73 | android.hardware.input.classifier@1.0.so | android.hardware.input.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 74 | android.hardware.input.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 75 | android.hardware.ir@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 76 | android.hardware.keymaster@3.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 77 | android.hardware.keymaster@4.0.so | android.hardware.keymaster@3.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 78 | android.hardware.light@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 79 | android.hardware.media.bufferpool@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 80 | android.hardware.media.bufferpool@2.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 81 | android.hardware.media.c2@1.0.so | android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.media.bufferpool@2.0.so<br>android.hardware.media.omx@1.0.so<br>android.hardware.media@1.0.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 82 | android.hardware.media.omx@1.0.so | android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.common@1.0.so<br>android.hardware.media@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 83 | android.hardware.media@1.0.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 84 | android.hardware.memtrack@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 85 | android.hardware.neuralnetworks@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 86 | android.hardware.neuralnetworks@1.1.so | android.hardware.neuralnetworks@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 87 | android.hardware.neuralnetworks@1.2.so | android.hardware.neuralnetworks@1.0.so<br>android.hardware.neuralnetworks@1.1.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 88 | android.hardware.nfc@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 89 | android.hardware.nfc@1.1.so | android.hardware.nfc@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 90 | android.hardware.nfc@1.2.so | android.hardware.nfc@1.0.so<br>android.hardware.nfc@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 91 | android.hardware.oemlock@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 92 | android.hardware.power.stats@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 93 | android.hardware.power@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 94 | android.hardware.power@1.1.so | android.hardware.power@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 95 | android.hardware.power@1.2.so | android.hardware.power@1.0.so<br>android.hardware.power@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 96 | android.hardware.power@1.3.so | android.hardware.power@1.0.so<br>android.hardware.power@1.1.so<br>android.hardware.power@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 97 | android.hardware.radio.config@1.0.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 98 | android.hardware.radio.config@1.1.so | android.hardware.radio.config@1.0.so<br>android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 99 | android.hardware.radio.config@1.2.so | android.hardware.radio.config@1.0.so<br>android.hardware.radio.config@1.1.so<br>android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 100 | android.hardware.radio.deprecated@1.0.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 101 | android.hardware.radio@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 102 | android.hardware.radio@1.1.so | android.hardware.radio@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 103 | android.hardware.radio@1.2.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 104 | android.hardware.radio@1.3.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>android.hardware.radio@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 105 | android.hardware.radio@1.4.so | android.hardware.radio@1.0.so<br>android.hardware.radio@1.1.so<br>android.hardware.radio@1.2.so<br>android.hardware.radio@1.3.so<br>android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 106 | android.hardware.secure_element@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 107 | android.hardware.secure_element@1.1.so | android.hardware.secure_element@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 108 | android.hardware.sensors@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 109 | android.hardware.sensors@2.0.so | android.hardware.sensors@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 110 | android.hardware.soundtrigger@2.0-core.so | libhidlbase.so<br>libhidltransport.so<br>liblog.so<br>libutils.so<br>libhardware.so<br>android.hardware.soundtrigger@2.0.so<br>android.hardware.audio.common@2.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 111 | android.hardware.soundtrigger@2.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 112 | android.hardware.soundtrigger@2.1.so | android.hardware.audio.common@2.0.so<br>android.hardware.soundtrigger@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 113 | android.hardware.soundtrigger@2.2.so | android.hardware.audio.common@2.0.so<br>android.hardware.soundtrigger@2.0.so<br>android.hardware.soundtrigger@2.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 114 | android.hardware.tetheroffload.config@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 115 | android.hardware.tetheroffload.control@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 116 | android.hardware.thermal@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 117 | android.hardware.thermal@1.1.so | android.hardware.thermal@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 118 | android.hardware.thermal@2.0.so | android.hardware.thermal@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 119 | android.hardware.tv.cec@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 120 | android.hardware.tv.cec@2.0.so | android.hidl.safe_union@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 121 | android.hardware.tv.input@1.0.so | android.hardware.audio.common@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 122 | android.hardware.usb.gadget@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 123 | android.hardware.usb@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 124 | android.hardware.usb@1.1.so | android.hardware.usb@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 125 | android.hardware.usb@1.2.so | android.hardware.usb@1.0.so<br>android.hardware.usb@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 126 | android.hardware.vibrator@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 127 | android.hardware.vibrator@1.1.so | android.hardware.vibrator@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 128 | android.hardware.vibrator@1.2.so | android.hardware.vibrator@1.0.so<br>android.hardware.vibrator@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 129 | android.hardware.vibrator@1.3.so | android.hardware.vibrator@1.0.so<br>android.hardware.vibrator@1.1.so<br>android.hardware.vibrator@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 130 | android.hardware.vr@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 131 | android.hardware.weaver@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 132 | android.hardware.wifi.hostapd@1.0.so | android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 133 | android.hardware.wifi.hostapd@1.1.so | android.hardware.wifi.hostapd@1.0.so<br>android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 134 | android.hardware.wifi.offload@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 135 | android.hardware.wifi.supplicant@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 136 | android.hardware.wifi.supplicant@1.1.so | android.hardware.wifi.supplicant@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 137 | android.hardware.wifi.supplicant@1.2.so | android.hardware.wifi.supplicant@1.0.so<br>android.hardware.wifi.supplicant@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 138 | android.hardware.wifi@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 139 | android.hardware.wifi@1.1.so | android.hardware.wifi@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 140 | android.hardware.wifi@1.2.so | android.hardware.wifi@1.0.so<br>android.hardware.wifi@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 141 | android.hardware.wifi@1.3.so | android.hardware.wifi@1.0.so<br>android.hardware.wifi@1.1.so<br>android.hardware.wifi@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 142 | android.hidl.allocator@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 143 | android.hidl.memory.block@1.0.so | android.hidl.memory.token@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 144 | android.hidl.token@1.0-utils.so | android.hidl.token@1.0.so<br>libbinder.so<br>libhidlbase.so<br>liblog.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 145 | android.hidl.token@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 146 | android.system.net.netd@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 147 | android.system.net.netd@1.1.so | android.system.net.netd@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 148 | android.system.suspend@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 149 | android.system.wifi.keystore@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 150 | libadf.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 151 | libaudioroute.so | liblog.so<br>libcutils.so<br>libutils.so<br>libexpat.so<br>libtinyalsa.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 152 | libaudioutils.so | libcutils.so<br>liblog.so<br>libspeexresampler.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 153 | libbinder.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 154 | libcamera_metadata.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 155 | libcap.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 156 | libclang_rt.scudo-arm-android.so | libc.so<br>libdl.so<br>liblog.so<br>libc++.so |
| 157 | libclang_rt.scudo_minimal-arm-android.so | libc.so<br>libdl.so<br>liblog.so |
| 158 | libclang_rt.ubsan_standalone-arm-android.so | libc++.so<br>libc.so<br>libdl.so<br>liblog.so |
| 159 | libcn-cbor.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 160 | libcodec2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 161 | libcrypto.so | libc.so<br>libm.so<br>libdl.so |
| 162 | libcrypto_utils.so | libcrypto.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 163 | libcurl.so | libcrypto.so<br>libssl.so<br>libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 164 | libdiskconfig.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 165 | libdumpstateutil.so | libbase.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 166 | libevent.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 167 | libexif.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 168 | libexpat.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 169 | libfmq.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 170 | libgatekeeper.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 171 | libgui.so | libhardware.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>android.hidl.token@1.0-utils.so<br>libbase.so<br>libbinder.so<br>libcutils.so<br>libEGL.so<br>libGLESv2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libnativewindow.so<br>libsync.so<br>libui.so<br>libutils.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 172 | libhardware_legacy.so | libbase.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>android.system.suspend@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 173 | libhidlallocatorutils.so | libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 174 | libhidlcache.so | libbase.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhwbinder.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>android.hidl.memory.block@1.0.so<br>android.hidl.memory.token@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 175 | libjpeg.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 176 | libkeymaster_messages.so | libc.so<br>libm.so<br>libdl.so |
| 177 | libkeymaster_portable.so | libcrypto.so<br>libc.so<br>libm.so<br>libdl.so |
| 178 | libldacBT_abr.so | libldacBT_enc.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 179 | libldacBT_enc.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 180 | liblz4.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 181 | libmedia_helper.so | libutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 182 | libmedia_omx.so | android.hidl.token@1.0-utils.so<br>android.hardware.media.omx@1.0.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>liblog.so<br>libstagefright_foundation.so<br>libui.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 183 | libmemtrack.so | libhardware.so<br>liblog.so<br>libbase.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>libutils.so<br>android.hardware.memtrack@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 184 | libminijail.so | libcap.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 185 | libmkbootimg_abi_check.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 186 | libnetutils.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 187 | libnl.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 188 | libpcre2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 189 | libpiex.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 190 | libpng.so | libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 191 | libpower.so | libbase.so<br>libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>android.system.suspend@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 192 | libprocinfo.so | libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 193 | libprotobuf-cpp-full.so | libz.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 194 | libprotobuf-cpp-lite.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 195 | libpuresoftkeymasterdevice.so | libkeymaster_messages.so<br>libkeymaster_portable.so<br>liblog.so<br>libcrypto.so<br>libcutils.so<br>libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 196 | libradio_metadata.so | libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 197 | libselinux.so | libcrypto.so<br>liblog.so<br>libpcre2.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 198 | libsoftkeymasterdevice.so | libkeymaster_messages.so<br>libkeymaster_portable.so<br>liblog.so<br>libbase.so<br>libcrypto.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 199 | libspeexresampler.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 200 | libsqlite.so | liblog.so<br>libutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 201 | libssl.so | libcrypto.so<br>libc.so<br>libm.so<br>libdl.so |
| 202 | libstagefright_bufferpool@2.0.so | libcutils.so<br>libfmq.so<br>libhidlbase.so<br>libhwbinder.so<br>libhidltransport.so<br>liblog.so<br>libutils.so<br>android.hardware.media.bufferpool@2.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 203 | libstagefright_bufferqueue_helper.so | libbinder.so<br>libcutils.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhidltransport.so<br>liblog.so<br>libstagefright_foundation.so<br>libui.so<br>libutils.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>android.hardware.graphics.bufferqueue@2.0.so<br>android.hidl.token@1.0-utils.so<br>libbase.so<br>libEGL.so<br>libhwbinder.so<br>libnativewindow.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 204 | libstagefright_foundation.so | libbinder.so<br>libutils.so<br>libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 205 | libstagefright_omx.so | libbase.so<br>libbinder.so<br>libmedia_omx.so<br>libutils.so<br>liblog.so<br>libui.so<br>libcutils.so<br>libstagefright_foundation.so<br>libstagefright_bufferqueue_helper.so<br>libstagefright_xmlparser.so<br>libhidlbase.so<br>libhidlmemory.so<br>libhidltransport.so<br>libvndksupport.so<br>android.hardware.media.omx@1.0.so<br>android.hardware.graphics.bufferqueue@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 206 | libstagefright_omx_utils.so | libmedia_omx.so<br>libstagefright_foundation.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 207 | libstagefright_xmlparser.so | libbase.so<br>libexpat.so<br>liblog.so<br>libstagefright_omx_utils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 208 | libsysutils.so | libbase.so<br>libcutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 209 | libtinyalsa.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 210 | libtinyxml2.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 211 | libui.so | android.hardware.graphics.allocator@2.0.so<br>android.hardware.graphics.allocator@3.0.so<br>android.hardware.graphics.common@1.2.so<br>android.hardware.graphics.mapper@2.0.so<br>android.hardware.graphics.mapper@2.1.so<br>android.hardware.graphics.mapper@3.0.so<br>libbase.so<br>libcutils.so<br>libhardware.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>libsync.so<br>libutils.so<br>liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 212 | libusbhost.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 213 | libwifi-system-iface.so | libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 214 | libxml2.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 215 | libyuv.so | libjpeg.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 216 | libziparchive.so | liblog.so<br>libbase.so<br>libz.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |

################################################################################
# 10.4 — COMPLETE VNDK-SP-29 INVENTORY STATUS
################################################################################

10.4.1 Canonical requirement

  [VNDK_SP_29_COMPLETE_LIST_REQUIRED]
  Final Section 10 must contain the complete VNDK-SP-29 library list:
    - 39 files plus hw/ subdirectory according to section scope
    - 64-bit and 32-bit where present
    - all support libraries required by SP-HAL and vendor namespaces

  Required per-file fields:
    - path
    - ABI
    - size
    - SHA256
    - SONAME
    - DT_NEEDED
    - RPATH/RUNPATH
    - Build ID
    - normal-system copy comparison
    - runtime mapped process list if active

10.4.2 VNDK-SP-29 support-substrate divergence already proven

  [STANDARD_SUPPORT_SUBSTRATE_DIVERGENT_64BIT]
  Normal system and VNDK-SP-29 copies are different stock payloads for all checked
  support libraries below. Same basename does not imply identity.

    libbase.so
      /system/lib64/libbase.so
        bytes=83552
        sha256=cfd3b9dbc37bbf3dd21b30f0940c3e6fcfbae6b4d45a180efef91967cede39ef
      /system/lib64/vndk-sp-29/libbase.so
        bytes=83552
        sha256=59e91b20dd637b980f89c4b888d2212c6cc11d369c0db277f9ea8b90575ea912

    libcutils.so
      /system/lib64/libcutils.so
        bytes=77872
        sha256=ff2c1657014baf0205efc8a99f67fa5aa355e993fcc90e6edad6fcec2da2e25d
      /system/lib64/vndk-sp-29/libcutils.so
        bytes=73704
        sha256=f8937d3b135d9a6820c870e1e93ba6d57fba0c8ac39cdfc1ba077acdeea0d9dc

    libutils.so
      /system/lib64/libutils.so
        bytes=117416
        sha256=8eb7b70667df69684a4a17b511444805e0b8452fbd9fef09c6ea6e55ea860978
      /system/lib64/vndk-sp-29/libutils.so
        bytes=117416
        sha256=64375d969cb77d08c70ac2fb69be58148f93bc710233f82b3cae4b0b8334f2a7

    libhardware.so
      /system/lib64/libhardware.so
        bytes=15280
        sha256=655b29af09b86f40ddc70637cad5e97be025ba102908c50f001790b713371b6a
      /system/lib64/vndk-sp-29/libhardware.so
        bytes=15256
        sha256=ad4257c0aa2620daf23938a9930d5a969b3f00d1e020616861405a93e5b960e8

    libhidlbase.so
      /system/lib64/libhidlbase.so
        bytes=692152
        sha256=4ca3742fb2a8b576af380c4ebc8c4a5e92010890351f602e7869d0dca4a183b0
      /system/lib64/vndk-sp-29/libhidlbase.so
        bytes=692184
        sha256=f7ded83f6c9716ee0466cf1972375d6a2983e99329b1d627b19493e44c610720

    libhidltransport.so
      /system/lib64/libhidltransport.so
        bytes=14896
        sha256=56ad32b30e6fbf6eef34d7ad1d8dd63f8538ffef3e84af49db834643afae6014
      /system/lib64/vndk-sp-29/libhidltransport.so
        bytes=14896
        sha256=c52ecde24197c9b6367b2f56609aab75d9c65649742577da428cc8eb29f22f06

    libc++.so
      /system/lib64/libc++.so
        bytes=845928
        sha256=e3cdb7e15bb2b172d3dfada9ce5d171718ebe2ae10253cba5ac4d4ac71a21860
      /system/lib64/vndk-sp-29/libc++.so
        bytes=845936
        sha256=91ea46ffe9ee01dd5572d75c5456aba6e3f351999f378452074026b615e6574e

  [NO_STANDARD_LIB_TRUST_SHORTCUT]
  These files are standard Android basenames but cannot be treated as generic
  interchangeable AOSP copies. They are namespace-qualified stock C17S payloads.

10.4.3 Current VNDK-SP-29 closure status

  [VNDK_SP_29_FULL_LIST_PENDING]
  Complete 39-file VNDK-SP-29 list plus hw/ subdirectory is not yet embedded in
  this draft. This is a blocking open item.

  [VNDK_SP_29_HW_SUBDIR_PENDING]
  hw/ subdirectory identity, hash, DT_NEEDED, and runtime mapping remain pending.


################################################################################
# 10.4.4 — B1E4 COMPLETE VNDK-SP-29 INVENTORY — SELF-CONTAINED DATABASE TABLES
################################################################################

10.4.4.1 Closure authority and scope-count resolution

  [B1E4_VNDK_SP_29_COMPLETE_LIST_EMBEDDED]
  [B1E4_VNDK_SP_29_DATABASE_FORM_CLOSED]
  [CANONICAL_VNDK_SP_29_LIST_SELF_CONTAINED]
  [ORIGINAL_39_FILE_SCOPE_LEAD_SUPERSEDED_BY_DEVICE_EVIDENCE]

  This B1E4 insertion closes the complete VNDK-SP-29 list requirement for the
  C17S captured software image. The complete 64-bit and 32-bit VNDK-SP-29 sets,
  including the hw/ implementation subdirectory, are embedded directly in this
  Section 10 database document. No sidecar file, external report, build note, or
  generated artifact is required to recover the VNDK-SP-29 inventory.

  Evidence source used to render the embedded rows:
    SECTION10_GATE_B1E3D_VNDK_STATIC_LIVE_EQUIVALENCE_504.tsv
      sha256=6800548720af5b045fb580499e7af239499d9c28a2411794afcdfc3c8ab7a4d7

  Original scope wording:
    Complete VNDK-SP-29 library list: 39 files + hw/ subdirectory.

  Resolved C17S evidence:
    - /system/lib64/vndk-sp-29: 35 top-level ELF64 files.
    - /system/lib64/vndk-sp-29/hw: 1 ELF64 hw implementation.
    - /system/lib/vndk-sp-29: 35 top-level ELF32 files.
    - /system/lib/vndk-sp-29/hw: 1 ELF32 hw implementation.
    - VNDK-SP-29 recursive total: 36 files per ABI, 72 files across both ABIs.
    - Static/live size match: 72 / 72.
    - Static/live SHA256 match: 72 / 72.
    - Live SELinux context: u:object_r:system_lib_file:s0 for all 72 rows.
    - RPATH/RUNPATH: NONE for all 72 rows.
    - Classification applied in this inventory: REQUIRED_SYSTEM_VNDK_SP.

  Count classification:
    The original “39 files + hw/ subdirectory” wording is retained as a
    pre-resolution scope lead. Device-specific static/live evidence supersedes
    it. No four-file deficit exists inside the canonical C17S VNDK-SP-29
    directories captured here.

  Closure boundary:
    This closes the canonical VNDK-SP-29 file list and static/live
    byte-equivalence for the stock extraction and live mounted runtime paths
    captured in this evidence set. It does not claim that all crypto,
    keymaster, gatekeeper, DRM, or vendor HAL runtime behavior is contained
    inside VNDK-SP-29. Those chains remain resolved under the dependency,
    namespace, vendor blob, and dlopen/Ghidra closure gates later in Section 10.

10.4.4.2 Field design for the embedded VNDK-SP-29 tables

  The VNDK-SP-29 evidence is split into three table classes per ABI so the
  database remains readable and usable:

    - Identity and equivalence table:
        one row per library, carrying size, SHA256, ELF identity, Build ID,
        SONAME, RPATH/RUNPATH status, static/live equivalence, ownership,
        SELinux label, and classification.

    - Path provenance table:
        one row per library, carrying the live runtime path and the static stock
        extraction path used for byte-equivalence proof.

    - DT_NEEDED table:
        one row per library, carrying the complete DT_NEEDED dependency list.

  These three table classes together form the complete VNDK-SP-29 library list.

10.4.5 Complete VNDK-SP-29 library list — 64-bit set including hw/

  Runtime directory:
    /system/lib64/vndk-sp-29

  Row count:
    36

  Table use:
    The local ID column is stable inside this ABI subsection. Identity, path
    provenance, and DT_NEEDED rows share the same local ID. SHA256 is the
    static/live-matched payload hash. Static/live equivalence is carried per row.

Identity and equivalence table:

| ID | Scope | Library | Size | SHA256 | ELF | Machine | Build ID | SONAME | RPATH/RUNPATH | Static/live equivalence | Mode / UID:GID | SELinux context | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | top | android.hardware.graphics.common@1.0.so | 14984 | c3a0828cec8053bb95192e444484ea06befce6d1d094a58a8d0e95696a768392 | ELF64 | ARM aarch64 | fec0b96cd1ad3446e6f8e62e80ba8cc7 | android.hardware.graphics.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 2 | top | android.hardware.graphics.common@1.1.so | 15000 | 4116d4688c2f87461649f27b557e0f24cbfa6adcb80a3de304788d0c612fbc97 | ELF64 | ARM aarch64 | 5dd36c2bfe40139480ab9f2c788a8b6c | android.hardware.graphics.common@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 3 | top | android.hardware.graphics.common@1.2.so | 15040 | a3b4fca4862e03aba39ad3507a71901d969786af9a8bb94c9781d1e31fc794f8 | ELF64 | ARM aarch64 | d6e8fe820a139b28957a6e11acb89385 | android.hardware.graphics.common@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 4 | top | android.hardware.graphics.mapper@2.0.so | 93184 | 8c6664640032cd5615de409a64df96dba2e8918d5d843e89d64c75b7c018985a | ELF64 | ARM aarch64 | 52ee4f22843839004fac80b2d5a24879 | android.hardware.graphics.mapper@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 5 | top | android.hardware.graphics.mapper@2.1.so | 98216 | 9af5657524c070f09c904134c99b9d79cfbd8aa3b6c163c5829963bb15f5a962 | ELF64 | ARM aarch64 | 0b0dbcc45eb8c7eb434bbc8385307369 | android.hardware.graphics.mapper@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 6 | top | android.hardware.graphics.mapper@3.0.so | 102200 | d51ed7a4e407e7a2d4b56503f0b86af875bfd8e6149381f3b04ac19c1aa9cdf3 | ELF64 | ARM aarch64 | 1c9a9dfdbc89b9fafa1740f25be454e0 | android.hardware.graphics.mapper@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 7 | top | android.hardware.renderscript@1.0.so | 325888 | d356d9959c7fa1798e9de90f03f5125b4b96ad5d2ce25f4c35bc671721cd01e6 | ELF64 | ARM aarch64 | cfde859f0eddb9f9714b95635ec06106 | android.hardware.renderscript@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 8 | top | android.hidl.memory.token@1.0.so | 79744 | edc0786cc5abe37434bf300bb80071d343eb20bf78c19fbdf2d3c602386db72e | ELF64 | ARM aarch64 | b09198785d98f7d99bf5212eb14034c4 | android.hidl.memory.token@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 9 | top | android.hidl.memory@1.0.so | 125624 | 42651045801baadd720a988d8d9a2f99104c6356e8ca90f78ca25f0ab9aae6d2 | ELF64 | ARM aarch64 | 4a8870f0fe2aa24b5db8333f494038a8 | android.hidl.memory@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 10 | top | android.hidl.safe_union@1.0.so | 14984 | cac93f4c1b222f979ea3e1f4f71b228c58e295662ec760ac065df0b0c40a957c | ELF64 | ARM aarch64 | e5565e3b0a26def5807e1c4f0d7c38cd | android.hidl.safe_union@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 11 | top | libRSCpuRef.so | 500200 | 7251735d479867616a51ad58a73ae93f63479d5f7c2daecd144c8ab19e7334d9 | ELF64 | ARM aarch64 | c2cd52fa963556e394fd2f50fe11904a | libRSCpuRef.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 12 | top | libRSDriver.so | 211296 | 9e33c7646c2effcf9f96782e7059be52f78cb772e6570818efc5929c42406018 | ELF64 | ARM aarch64 | 651d51af9fa0a2b1b86e66ae21a2dc72 | libRSDriver.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 13 | top | libRS_internal.so | 322744 | 24e3e2bce5690203dffeb14ddda2ac3692ecc2a63f4a1641cf24896cc890eb09 | ELF64 | ARM aarch64 | 34138807567f6d49224bc905442e60a3 | libRS_internal.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 14 | top | libbacktrace.so | 109648 | deec26c3e36a51c19480bb48813a1f40233d58db87a147ab7da0e62ab50c37af | ELF64 | ARM aarch64 | 376d0bbdeccaf53143c748580e399221 | libbacktrace.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 15 | top | libbase.so | 83552 | 59e91b20dd637b980f89c4b888d2212c6cc11d369c0db277f9ea8b90575ea912 | ELF64 | ARM aarch64 | 6ae5a8f80fb5bfdb7922758e5168b93d | libbase.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 16 | top | libbcinfo.so | 1154472 | 472fbc848768f92ce9c013a037292770211995a9441d5fedbf9b8c5df4fdcb97 | ELF64 | ARM aarch64 | c6785dc255abf918da42fa8c255c5d6b | libbcinfo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 17 | top | libbinderthreadstate.so | 23896 | c7a0db9edb894123f2f0c8b46cbe667374a85d9dd55e7def5f8da1541f9e1919 | ELF64 | ARM aarch64 | 05c6ead9bc45946f14b095182e6b1cb3 | libbinderthreadstate.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 18 | top | libblas.so | 1185264 | 944798f67dcb60e846bc449b0ca655f2e0e04114a08073143bf20b190f58d90c | ELF64 | ARM aarch64 | 7178959e571d5d854bf3a5f6e268a1f5 | libblas.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 19 | top | libc++.so | 845936 | 91ea46ffe9ee01dd5572d75c5456aba6e3f351999f378452074026b615e6574e | ELF64 | ARM aarch64 | e4cde0271b46ab929a056901322632cd | libc++.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 20 | top | libcompiler_rt.so | 111152 | e87d35526e0ce6e2804fbf371247fe5811ce8b80115ec3b05cea6c8586d2481d | ELF64 | ARM aarch64 | 2a18102af4ed32727fc3a060c19dd184 | libcompiler_rt.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 21 | top | libcutils.so | 73704 | f8937d3b135d9a6820c870e1e93ba6d57fba0c8ac39cdfc1ba077acdeea0d9dc | ELF64 | ARM aarch64 | 97f1f4183d49bbf88dc7720dede7e5ff | libcutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 22 | top | libhardware.so | 15256 | ad4257c0aa2620daf23938a9930d5a969b3f00d1e020616861405a93e5b960e8 | ELF64 | ARM aarch64 | dba23eb2bfdcd7670f87fc0bcd1269c1 | libhardware.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 23 | top | libhidlbase.so | 692184 | f7ded83f6c9716ee0466cf1972375d6a2983e99329b1d627b19493e44c610720 | ELF64 | ARM aarch64 | a9d7172724be7902f3adff1ab08f98f0 | libhidlbase.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 24 | top | libhidlmemory.so | 29680 | 11b7a294a67fa20711c29fb563b5790398d117da49f32c5402b1d090dce44af0 | ELF64 | ARM aarch64 | 2867addca38026a5351c4331f8e77ec9 | libhidlmemory.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 25 | top | libhidltransport.so | 14896 | c52ecde24197c9b6367b2f56609aab75d9c65649742577da428cc8eb29f22f06 | ELF64 | ARM aarch64 | 0c63c81123ff81fa7b434f1fbac7f4cd | libhidltransport.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 26 | top | libhwbinder.so | 14896 | 17b2bb3a8ea0c27a18098e3b0ef819cc545bd0a6b0bc00a56f35f349377caac0 | ELF64 | ARM aarch64 | 12e3ab14daaf2e0f3e3746439b150add | libhwbinder.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 27 | top | libhwbinder_noltopgo.so | 166664 | 66062c80566a8cda4a90594cb47251fedf72e115bc6459edfe5d1fa1bb266ad9 | ELF64 | ARM aarch64 | 2d8859b1bd07cb6f61e5366eefb05806 | libhwbinder_noltopgo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 28 | top | libion.so | 15184 | 56a67cd746a5de134457e0545711c860914b574974efe491479c0d2cb94ac1ce | ELF64 | ARM aarch64 | 391fedfb20b9c0142b6a9694bd4b41d7 | libion.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 29 | top | libjsoncpp.so | 132320 | 291e1b4f232cf1525f6cfb18e8c68878ea48531a7702f15f1060d1906434191f | ELF64 | ARM aarch64 | a0de9cb5fec1109f8811de9a0fdec227 | libjsoncpp.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 30 | top | liblzma.so | 152672 | 799741cba58fa892198259c2be9432f0f8506c5e9e687c9855a46ded0c2a61c0 | ELF64 | ARM aarch64 | c1bc8b7a439dea0b53c7eb88bf1338c2 | liblzma.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 31 | top | libprocessgroup.so | 251208 | cb9f706e6282742de36c62816f093065d0d8642c70e5c402428845ffc3c644a3 | ELF64 | ARM aarch64 | c644809bb326fa07f1e95a822fdec025 | libprocessgroup.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 32 | top | libunwindstack.so | 373464 | 27de153d9eb32944be726f39da245dbd6a88c17f0bf5a8839f8813fb36c42039 | ELF64 | ARM aarch64 | ca4172edec0136dd92953dfce9fdc02b | libunwindstack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 33 | top | libutils.so | 117416 | 64375d969cb77d08c70ac2fb69be58148f93bc710233f82b3cae4b0b8334f2a7 | ELF64 | ARM aarch64 | 6cb544e68e35030534ebeec37bc37743 | libutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 34 | top | libutilscallstack.so | 28520 | f25883513820a12b6491748c3e13e81bd7171aeff6d22ecaaa3778562af1ca29 | ELF64 | ARM aarch64 | ddf99051ade9eb455e55cbb74402b8ff | libutilscallstack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 35 | top | libz.so | 102344 | b10d8b327becce3028eac8db8cb7f0f7e9d7957dbcad15a3c21e5efab0d28e4d | ELF64 | ARM aarch64 | cfb0a70fc145448d4bc0444b6da80d0e | libz.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 36 | hw | hw/android.hidl.memory@1.0-impl.so | 25824 | bc6cc230f1354c8695bd458b0d65211ec9c2b6f8c8f8884ae95a61fd60d8a3d4 | ELF64 | ARM aarch64 | 141edd08b9925326b0e763a7a581f668 | android.hidl.memory@1.0-impl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |

Path provenance table:

| ID | Library | Live runtime path | Static stock source path |
| --- | --- | --- | --- |
| 1 | android.hardware.graphics.common@1.0.so | /system/lib64/vndk-sp-29/android.hardware.graphics.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.common@1.0.so |
| 2 | android.hardware.graphics.common@1.1.so | /system/lib64/vndk-sp-29/android.hardware.graphics.common@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.common@1.1.so |
| 3 | android.hardware.graphics.common@1.2.so | /system/lib64/vndk-sp-29/android.hardware.graphics.common@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.common@1.2.so |
| 4 | android.hardware.graphics.mapper@2.0.so | /system/lib64/vndk-sp-29/android.hardware.graphics.mapper@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.mapper@2.0.so |
| 5 | android.hardware.graphics.mapper@2.1.so | /system/lib64/vndk-sp-29/android.hardware.graphics.mapper@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.mapper@2.1.so |
| 6 | android.hardware.graphics.mapper@3.0.so | /system/lib64/vndk-sp-29/android.hardware.graphics.mapper@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.graphics.mapper@3.0.so |
| 7 | android.hardware.renderscript@1.0.so | /system/lib64/vndk-sp-29/android.hardware.renderscript@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hardware.renderscript@1.0.so |
| 8 | android.hidl.memory.token@1.0.so | /system/lib64/vndk-sp-29/android.hidl.memory.token@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hidl.memory.token@1.0.so |
| 9 | android.hidl.memory@1.0.so | /system/lib64/vndk-sp-29/android.hidl.memory@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hidl.memory@1.0.so |
| 10 | android.hidl.safe_union@1.0.so | /system/lib64/vndk-sp-29/android.hidl.safe_union@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/android.hidl.safe_union@1.0.so |
| 11 | libRSCpuRef.so | /system/lib64/vndk-sp-29/libRSCpuRef.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libRSCpuRef.so |
| 12 | libRSDriver.so | /system/lib64/vndk-sp-29/libRSDriver.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libRSDriver.so |
| 13 | libRS_internal.so | /system/lib64/vndk-sp-29/libRS_internal.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libRS_internal.so |
| 14 | libbacktrace.so | /system/lib64/vndk-sp-29/libbacktrace.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libbacktrace.so |
| 15 | libbase.so | /system/lib64/vndk-sp-29/libbase.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libbase.so |
| 16 | libbcinfo.so | /system/lib64/vndk-sp-29/libbcinfo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libbcinfo.so |
| 17 | libbinderthreadstate.so | /system/lib64/vndk-sp-29/libbinderthreadstate.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libbinderthreadstate.so |
| 18 | libblas.so | /system/lib64/vndk-sp-29/libblas.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libblas.so |
| 19 | libc++.so | /system/lib64/vndk-sp-29/libc++.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libc++.so |
| 20 | libcompiler_rt.so | /system/lib64/vndk-sp-29/libcompiler_rt.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libcompiler_rt.so |
| 21 | libcutils.so | /system/lib64/vndk-sp-29/libcutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libcutils.so |
| 22 | libhardware.so | /system/lib64/vndk-sp-29/libhardware.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhardware.so |
| 23 | libhidlbase.so | /system/lib64/vndk-sp-29/libhidlbase.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhidlbase.so |
| 24 | libhidlmemory.so | /system/lib64/vndk-sp-29/libhidlmemory.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhidlmemory.so |
| 25 | libhidltransport.so | /system/lib64/vndk-sp-29/libhidltransport.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhidltransport.so |
| 26 | libhwbinder.so | /system/lib64/vndk-sp-29/libhwbinder.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhwbinder.so |
| 27 | libhwbinder_noltopgo.so | /system/lib64/vndk-sp-29/libhwbinder_noltopgo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libhwbinder_noltopgo.so |
| 28 | libion.so | /system/lib64/vndk-sp-29/libion.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libion.so |
| 29 | libjsoncpp.so | /system/lib64/vndk-sp-29/libjsoncpp.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libjsoncpp.so |
| 30 | liblzma.so | /system/lib64/vndk-sp-29/liblzma.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/liblzma.so |
| 31 | libprocessgroup.so | /system/lib64/vndk-sp-29/libprocessgroup.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libprocessgroup.so |
| 32 | libunwindstack.so | /system/lib64/vndk-sp-29/libunwindstack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libunwindstack.so |
| 33 | libutils.so | /system/lib64/vndk-sp-29/libutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libutils.so |
| 34 | libutilscallstack.so | /system/lib64/vndk-sp-29/libutilscallstack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libutilscallstack.so |
| 35 | libz.so | /system/lib64/vndk-sp-29/libz.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/libz.so |
| 36 | hw/android.hidl.memory@1.0-impl.so | /system/lib64/vndk-sp-29/hw/android.hidl.memory@1.0-impl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib64/vndk-sp-29/hw/android.hidl.memory@1.0-impl.so |

DT_NEEDED table:

| ID | Library | DT_NEEDED |
| --- | --- | --- |
| 1 | android.hardware.graphics.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 2 | android.hardware.graphics.common@1.1.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 3 | android.hardware.graphics.common@1.2.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 4 | android.hardware.graphics.mapper@2.0.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 5 | android.hardware.graphics.mapper@2.1.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.mapper@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 6 | android.hardware.graphics.mapper@3.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 7 | android.hardware.renderscript@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 8 | android.hidl.memory.token@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 9 | android.hidl.memory@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 10 | android.hidl.safe_union@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 11 | libRSCpuRef.so | libRS_internal.so<br>liblog.so<br>libz.so<br>libbcinfo.so<br>libblas.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 12 | libRSDriver.so | libRS_internal.so<br>libRSCpuRef.so<br>liblog.so<br>libEGL.so<br>libGLESv1_CM.so<br>libGLESv2.so<br>libnativewindow.so<br>libbcinfo.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 13 | libRS_internal.so | liblog.so<br>libutilscallstack.so<br>libutils.so<br>libEGL.so<br>libGLESv1_CM.so<br>libGLESv2.so<br>libnativewindow.so<br>libft2.so<br>libbcinfo.so<br>libmediandk.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 14 | libbacktrace.so | libbase.so<br>liblog.so<br>libunwindstack.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 15 | libbase.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 16 | libbcinfo.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 17 | libbinderthreadstate.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 18 | libblas.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 19 | libc++.so | libc.so<br>libm.so<br>libdl.so |
| 20 | libcompiler_rt.so | liblog.so<br>libc.so<br>libm.so<br>libdl.so |
| 21 | libcutils.so | liblog.so<br>libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 22 | libhardware.so | libcutils.so<br>liblog.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 23 | libhidlbase.so | libhwbinder.so<br>libvndksupport.so<br>libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 24 | libhidlmemory.so | libbase.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>android.hidl.memory.token@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 25 | libhidltransport.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 26 | libhwbinder.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 27 | libhwbinder_noltopgo.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 28 | libion.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 29 | libjsoncpp.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 30 | liblzma.so | libc.so<br>libm.so<br>libdl.so |
| 31 | libprocessgroup.so | libbase.so<br>libcgrouprc.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 32 | libunwindstack.so | libbase.so<br>liblog.so<br>liblzma.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 33 | libutils.so | libcutils.so<br>liblog.so<br>libprocessgroup.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 34 | libutilscallstack.so | libcutils.so<br>liblog.so<br>libutils.so<br>libbacktrace.so<br>libprocessgroup.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 35 | libz.so | libc.so<br>libm.so<br>libdl.so |
| 36 | hw/android.hidl.memory@1.0-impl.so | libcutils.so<br>libhwbinder.so<br>libbase.so<br>liblog.so<br>libutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |

10.4.6 Complete VNDK-SP-29 library list — 32-bit set including hw/

  Runtime directory:
    /system/lib/vndk-sp-29

  Row count:
    36

  Table use:
    The local ID column is stable inside this ABI subsection. Identity, path
    provenance, and DT_NEEDED rows share the same local ID. SHA256 is the
    static/live-matched payload hash. Static/live equivalence is carried per row.

Identity and equivalence table:

| ID | Scope | Library | Size | SHA256 | ELF | Machine | Build ID | SONAME | RPATH/RUNPATH | Static/live equivalence | Mode / UID:GID | SELinux context | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | top | android.hardware.graphics.common@1.0.so | 14916 | 313380826ca7d6ffe2fe53de9c25d010eb2c0ea10ca19348a5b2cf3733406367 | ELF32 | ARM | 9316ecd08fbcaf8f3923b4264f186ce4 | android.hardware.graphics.common@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 2 | top | android.hardware.graphics.common@1.1.so | 14928 | 47ab778d0f364bc6e9c4138759c6b6535f6384ea5b45f23db85b0fdee93ae4f5 | ELF32 | ARM | 0c4f6430ff25fde0776053955e7a8055 | android.hardware.graphics.common@1.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 3 | top | android.hardware.graphics.common@1.2.so | 15036 | 36d967bc4bb15fa0eb79113d224d32f12335040605804875eb400fbe4c70d933 | ELF32 | ARM | 6b7cb196a9af0c0efd36c37af3ade05c | android.hardware.graphics.common@1.2.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 4 | top | android.hardware.graphics.mapper@2.0.so | 70572 | 522a99ea314cc76c93f0e5fed4796a420b144f2f87b9c88c9acf740c6aceb997 | ELF32 | ARM | 98127391739b59c50d3379f8dbdbb582 | android.hardware.graphics.mapper@2.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 5 | top | android.hardware.graphics.mapper@2.1.so | 75196 | f9fc46f5c33fa6ae317996ed1ee100570a9eea2b50f7c6c936d589a7bbb417e6 | ELF32 | ARM | 45d401dee7744a03d44254ddd11392e7 | android.hardware.graphics.mapper@2.1.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 6 | top | android.hardware.graphics.mapper@3.0.so | 75352 | fa388df25fbd623f02a4dd4120f208494e9976d5a2e029e8e28ae47dc4d47f89 | ELF32 | ARM | 695843d2bca1787d37398e2872e9c33b | android.hardware.graphics.mapper@3.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 7 | top | android.hardware.renderscript@1.0.so | 239360 | c8f2c7cce2c9f75e11cca7f1c1ac1672c3b21cf6518b7e9aba1cb91520351f12 | ELF32 | ARM | ba67135ad8166127d4349eadb5e1be9a | android.hardware.renderscript@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 8 | top | android.hidl.memory.token@1.0.so | 61340 | 8e0b5cb712ad9306aec6a505a9f130b6432742661a65f6c0205e205a461c2d40 | ELF32 | ARM | 1f8fa6c76b8507dd8f10d5b123315d67 | android.hidl.memory.token@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 9 | top | android.hidl.memory@1.0.so | 97516 | d3ca2d6e24e7fdf1309951eacd47ba89a6fa2261c5bf8a3032f357180fd315f9 | ELF32 | ARM | d287f332cedf60d68b0f1f3befdbbad7 | android.hidl.memory@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 10 | top | android.hidl.safe_union@1.0.so | 14916 | c645654bcec6e2dd6ce3d453a4f35df0ffeeb4156a4c7804c70eb9fb29e28643 | ELF32 | ARM | 950cc4685cb8d50d4642108c9aad89e4 | android.hidl.safe_union@1.0.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 11 | top | libRSCpuRef.so | 395748 | b055ea2187ae597da582ceada41944cefc40c26a561d8a4d22b216ad7a8dfffb | ELF32 | ARM | d0742beb374d595afaa73c14a51e0e71 | libRSCpuRef.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 12 | top | libRSDriver.so | 168156 | 98c6e805599fc45d89dcc900f21b7e817c762740bb4114af3e41f7e32cf5ba03 | ELF32 | ARM | f68e32b176be4411101f9fe7ef1f8917 | libRSDriver.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 13 | top | libRS_internal.so | 239936 | 63cfc080ff33a5977f65c4fd102f822d48f0976345b6aa3e5326740ce63c427b | ELF32 | ARM | b460070ddc7741dedcb788a47efd2598 | libRS_internal.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 14 | top | libbacktrace.so | 84812 | 5c9c1f9362d80cc293ee8040d22cb47b62d514d2717d0a1ac65ceb350e0575c2 | ELF32 | ARM | b151675c5b6088ac37a88209b6a31adb | libbacktrace.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 15 | top | libbase.so | 63220 | abc9f466802e6511316af32b32a59e63f46d47b29923b051a93dfe2b16d7dee6 | ELF32 | ARM | a941bbb4fc801b984eb61c0086ffc347 | libbase.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 16 | top | libbcinfo.so | 872352 | 6ea0955ce3376fc8ef314f5cd045fdb57df785c123eb8a5d85dd02faf7810658 | ELF32 | ARM | 08763da7bf93746a2848c4edf3eaab30 | libbcinfo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 17 | top | libbinderthreadstate.so | 15476 | efef808d23f3a900ce0143e73e937c25a71b4d9ebf03f7658333a1a7afe4d5fc | ELF32 | ARM | e8863c8d7df47847b667c7265674bf09 | libbinderthreadstate.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 18 | top | libblas.so | 839492 | 39d9a86d4b0e7835ed93512f3779930546644874a1493ffe571f499bc834b1e9 | ELF32 | ARM | a01240c89be1e4e9ead603a3f95703ce | libblas.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 19 | top | libc++.so | 580768 | 8da1e8d70c296a821a70b29cbfc610081f2bbf091e15bf457972ac292ab15fec | ELF32 | ARM | 4565fe79ac07a1878495faa042400298 | libc++.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 20 | top | libcompiler_rt.so | 40848 | d250952e5d7da52a84d9a94825214ef59284394a5cd0a49ca59e7edb1f6fd2d8 | ELF32 | ARM | 1fe1edc61072adb71924697d5a4d9536 | libcompiler_rt.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 21 | top | libcutils.so | 57872 | 659c5c5a7df50b5781d041291c989c80df6e4f3c7dcc24ed5660db8fb534b0ef | ELF32 | ARM | 96c4284e5756d2d503648d16d7e394e0 | libcutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 22 | top | libhardware.so | 15268 | 0aa6aab2bd9465b826e897120b1b9ac07e496c370e3ec3fc706485f2d4078dfb | ELF32 | ARM | f3bafba5bb3485ab8bf32da1e1c99d1b | libhardware.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 23 | top | libhidlbase.so | 491488 | 398e12aeab008c3bb0a80d2323f82d8de4d5f098619578c4fb463c48030da64f | ELF32 | ARM | db47eb3f1ceb3442c4d88ce793be3dc9 | libhidlbase.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 24 | top | libhidlmemory.so | 24720 | 14da1c0608abb9bb2cb7c13d5d01f5a11a3a4a25b17d454caf77e11655427f16 | ELF32 | ARM | 45f152d5957ae42be1bc6b5911441777 | libhidlmemory.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 25 | top | libhidltransport.so | 14868 | b117ba80d3431764e947cc82ae3c0013c91500a040ba928e179e9c63c48083d5 | ELF32 | ARM | d12b78064eef55df2e756c4d5c0a4a57 | libhidltransport.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 26 | top | libhwbinder.so | 14868 | 1286819e82eda563ffd40f54280a003037a9ac59497ade6a9dd2a6a89d6bc318 | ELF32 | ARM | 5d3d6b2e4baebf299787fe3772104719 | libhwbinder.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 27 | top | libhwbinder_noltopgo.so | 121076 | 327bec07050d55812abfab0d2d8dcc87f2a1e57ddbe705aa47b32ecea4a2d58f | ELF32 | ARM | 304904f9564e09db8b9f196a870e290f | libhwbinder_noltopgo.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 28 | top | libion.so | 15168 | 0b8a830792f2cd4772ae71b095fcc1dc8a2becb0413efe9b34b18b34c8fbf72c | ELF32 | ARM | 32770f1dbe03cec1368f50dc7d8b7832 | libion.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 29 | top | libjsoncpp.so | 100456 | 581773f2701b9fb8a45dee2493d3ea70bada0ce3feaad5b0b9d5643f4f03fe8d | ELF32 | ARM | 25b05ee9db3e0637c512bbbff1474bec | libjsoncpp.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 30 | top | liblzma.so | 121808 | 0df2cdf3a9d2b217e565a11493eb46cd020c0bedde27ff8ec93b73821e953c0d | ELF32 | ARM | bc91fe79531e8ad5b79100cbd674d4e3 | liblzma.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 31 | top | libprocessgroup.so | 186404 | 8c8bd2f9f80943557f05325b212002862b8f14d7be5822ea707d622328ab463b | ELF32 | ARM | 9c4ce378855ed39e7bd90edafe8262a4 | libprocessgroup.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 32 | top | libunwindstack.so | 282484 | a66c486e7b19b24284f5ddb89c1681a00a407fbb9601532154858678f8282d54 | ELF32 | ARM | 88713dd3505d6a1385e6b34cb56c27be | libunwindstack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 33 | top | libutils.so | 89284 | 1267ebf5ae00a160b45499addbcd41bc6b05c5e4923e9cfa2c51a031659838ee | ELF32 | ARM | 010e2bd9a7b1ad0c105e6e493e3c8905 | libutils.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 34 | top | libutilscallstack.so | 24276 | efd8f5a9b018f2b3179a65ee5370af47cb422a9c7b297e87211a96dbb80403d6 | ELF32 | ARM | da4886700184cbc0119fb2f3b13a36c1 | libutilscallstack.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 35 | top | libz.so | 111296 | 18870c9d911e2f613573e9a96141bbff87e6f8b95326a87590c5e6724383661c | ELF32 | ARM | cd79a5af3106eb0b3328ba0c874076e9 | libz.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |
| 36 | hw | hw/android.hidl.memory@1.0-impl.so | 24772 | 674cfce14260fb918726463c26c4c85be2a1e32f9d49d9c602710d7d8eaa5478 | ELF32 | ARM | 126ef88ffd1965ba894fc9a925b2ca26 | android.hidl.memory@1.0-impl.so | NONE | size=MATCH; sha256=MATCH; overall=MATCH | 644 / 0:0 | u:object_r:system_lib_file:s0 | REQUIRED_SYSTEM_VNDK_SP |

Path provenance table:

| ID | Library | Live runtime path | Static stock source path |
| --- | --- | --- | --- |
| 1 | android.hardware.graphics.common@1.0.so | /system/lib/vndk-sp-29/android.hardware.graphics.common@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.common@1.0.so |
| 2 | android.hardware.graphics.common@1.1.so | /system/lib/vndk-sp-29/android.hardware.graphics.common@1.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.common@1.1.so |
| 3 | android.hardware.graphics.common@1.2.so | /system/lib/vndk-sp-29/android.hardware.graphics.common@1.2.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.common@1.2.so |
| 4 | android.hardware.graphics.mapper@2.0.so | /system/lib/vndk-sp-29/android.hardware.graphics.mapper@2.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.mapper@2.0.so |
| 5 | android.hardware.graphics.mapper@2.1.so | /system/lib/vndk-sp-29/android.hardware.graphics.mapper@2.1.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.mapper@2.1.so |
| 6 | android.hardware.graphics.mapper@3.0.so | /system/lib/vndk-sp-29/android.hardware.graphics.mapper@3.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.graphics.mapper@3.0.so |
| 7 | android.hardware.renderscript@1.0.so | /system/lib/vndk-sp-29/android.hardware.renderscript@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hardware.renderscript@1.0.so |
| 8 | android.hidl.memory.token@1.0.so | /system/lib/vndk-sp-29/android.hidl.memory.token@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hidl.memory.token@1.0.so |
| 9 | android.hidl.memory@1.0.so | /system/lib/vndk-sp-29/android.hidl.memory@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hidl.memory@1.0.so |
| 10 | android.hidl.safe_union@1.0.so | /system/lib/vndk-sp-29/android.hidl.safe_union@1.0.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/android.hidl.safe_union@1.0.so |
| 11 | libRSCpuRef.so | /system/lib/vndk-sp-29/libRSCpuRef.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libRSCpuRef.so |
| 12 | libRSDriver.so | /system/lib/vndk-sp-29/libRSDriver.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libRSDriver.so |
| 13 | libRS_internal.so | /system/lib/vndk-sp-29/libRS_internal.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libRS_internal.so |
| 14 | libbacktrace.so | /system/lib/vndk-sp-29/libbacktrace.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libbacktrace.so |
| 15 | libbase.so | /system/lib/vndk-sp-29/libbase.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libbase.so |
| 16 | libbcinfo.so | /system/lib/vndk-sp-29/libbcinfo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libbcinfo.so |
| 17 | libbinderthreadstate.so | /system/lib/vndk-sp-29/libbinderthreadstate.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libbinderthreadstate.so |
| 18 | libblas.so | /system/lib/vndk-sp-29/libblas.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libblas.so |
| 19 | libc++.so | /system/lib/vndk-sp-29/libc++.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libc++.so |
| 20 | libcompiler_rt.so | /system/lib/vndk-sp-29/libcompiler_rt.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libcompiler_rt.so |
| 21 | libcutils.so | /system/lib/vndk-sp-29/libcutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libcutils.so |
| 22 | libhardware.so | /system/lib/vndk-sp-29/libhardware.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhardware.so |
| 23 | libhidlbase.so | /system/lib/vndk-sp-29/libhidlbase.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhidlbase.so |
| 24 | libhidlmemory.so | /system/lib/vndk-sp-29/libhidlmemory.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhidlmemory.so |
| 25 | libhidltransport.so | /system/lib/vndk-sp-29/libhidltransport.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhidltransport.so |
| 26 | libhwbinder.so | /system/lib/vndk-sp-29/libhwbinder.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhwbinder.so |
| 27 | libhwbinder_noltopgo.so | /system/lib/vndk-sp-29/libhwbinder_noltopgo.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libhwbinder_noltopgo.so |
| 28 | libion.so | /system/lib/vndk-sp-29/libion.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libion.so |
| 29 | libjsoncpp.so | /system/lib/vndk-sp-29/libjsoncpp.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libjsoncpp.so |
| 30 | liblzma.so | /system/lib/vndk-sp-29/liblzma.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/liblzma.so |
| 31 | libprocessgroup.so | /system/lib/vndk-sp-29/libprocessgroup.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libprocessgroup.so |
| 32 | libunwindstack.so | /system/lib/vndk-sp-29/libunwindstack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libunwindstack.so |
| 33 | libutils.so | /system/lib/vndk-sp-29/libutils.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libutils.so |
| 34 | libutilscallstack.so | /system/lib/vndk-sp-29/libutilscallstack.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libutilscallstack.so |
| 35 | libz.so | /system/lib/vndk-sp-29/libz.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/libz.so |
| 36 | hw/android.hidl.memory@1.0-impl.so | /system/lib/vndk-sp-29/hw/android.hidl.memory@1.0-impl.so | /home/beright1976/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/system/system/lib/vndk-sp-29/hw/android.hidl.memory@1.0-impl.so |

DT_NEEDED table:

| ID | Library | DT_NEEDED |
| --- | --- | --- |
| 1 | android.hardware.graphics.common@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 2 | android.hardware.graphics.common@1.1.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 3 | android.hardware.graphics.common@1.2.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 4 | android.hardware.graphics.mapper@2.0.so | android.hardware.graphics.common@1.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 5 | android.hardware.graphics.mapper@2.1.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.mapper@2.0.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 6 | android.hardware.graphics.mapper@3.0.so | android.hardware.graphics.common@1.0.so<br>android.hardware.graphics.common@1.1.so<br>android.hardware.graphics.common@1.2.so<br>libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 7 | android.hardware.renderscript@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 8 | android.hidl.memory.token@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 9 | android.hidl.memory@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 10 | android.hidl.safe_union@1.0.so | libhidlbase.so<br>libhidltransport.so<br>libhwbinder.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 11 | libRSCpuRef.so | libRS_internal.so<br>liblog.so<br>libz.so<br>libbcinfo.so<br>libblas.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 12 | libRSDriver.so | libRS_internal.so<br>libRSCpuRef.so<br>liblog.so<br>libEGL.so<br>libGLESv1_CM.so<br>libGLESv2.so<br>libnativewindow.so<br>libbcinfo.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 13 | libRS_internal.so | liblog.so<br>libutilscallstack.so<br>libutils.so<br>libEGL.so<br>libGLESv1_CM.so<br>libGLESv2.so<br>libnativewindow.so<br>libft2.so<br>libbcinfo.so<br>libmediandk.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 14 | libbacktrace.so | libbase.so<br>liblog.so<br>libunwindstack.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 15 | libbase.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 16 | libbcinfo.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 17 | libbinderthreadstate.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 18 | libblas.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 19 | libc++.so | libc.so<br>libm.so<br>libdl.so |
| 20 | libcompiler_rt.so | liblog.so<br>libc.so<br>libm.so<br>libdl.so |
| 21 | libcutils.so | liblog.so<br>libbase.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 22 | libhardware.so | libcutils.so<br>liblog.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 23 | libhidlbase.so | libhwbinder.so<br>libvndksupport.so<br>libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 24 | libhidlmemory.so | libbase.so<br>liblog.so<br>libutils.so<br>libcutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>android.hidl.memory.token@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 25 | libhidltransport.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 26 | libhwbinder.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 27 | libhwbinder_noltopgo.so | libbase.so<br>liblog.so<br>libcutils.so<br>libutils.so<br>libbinderthreadstate.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 28 | libion.so | liblog.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 29 | libjsoncpp.so | libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 30 | liblzma.so | libc.so<br>libm.so<br>libdl.so |
| 31 | libprocessgroup.so | libbase.so<br>libcgrouprc.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 32 | libunwindstack.so | libbase.so<br>liblog.so<br>liblzma.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 33 | libutils.so | libcutils.so<br>liblog.so<br>libprocessgroup.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 34 | libutilscallstack.so | libcutils.so<br>liblog.so<br>libutils.so<br>libbacktrace.so<br>libprocessgroup.so<br>libvndksupport.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |
| 35 | libz.so | libc.so<br>libm.so<br>libdl.so |
| 36 | hw/android.hidl.memory@1.0-impl.so | libcutils.so<br>libhwbinder.so<br>libbase.so<br>liblog.so<br>libutils.so<br>libhidlbase.so<br>libhidltransport.so<br>android.hidl.memory@1.0.so<br>libc++.so<br>libc.so<br>libm.so<br>libdl.so |

################################################################################
# 10.5 — SYSTEM / VNDK / VENDOR CRYPTO PROVIDER COLLISION MATRIX
################################################################################

10.5.1 Known provider layers

  [CRYPTO_PROVIDER_LAYERS_CAPTURED]
  Static locate output showed at least these provider families:

    system/system/lib64/libcrypto.so
    system/system/lib64/libssl.so
    system/system/lib64/vndk-29/libcrypto.so
    system/system/lib64/vndk-29/libssl.so
    system/system/lib64/vndk-29/libpuresoftkeymasterdevice.so
    system/system/lib/libcrypto.so
    system/system/lib/libssl.so
    system/system/lib/vndk-29/libcrypto.so
    system/system/lib/vndk-29/libssl.so
    system/system/lib/vndk-29/libpuresoftkeymasterdevice.so
    system/system/vendor/lib64/libcrypto-ss.so
    system/system/vendor/lib64/libssl-ss.so
    vendor/lib64/libcrypto-ss.so
    vendor/lib64/libssl-ss.so
    vendor/lib/libcrypto-ss.so
    vendor/lib/libssl-ss.so

  [CRYPTO_STACK_IS_NOT_SINGLE_LIBRARY]
  C17S has at least three crypto-provider families:
    - normal system libcrypto/libssl
    - VNDK-29 libcrypto/libssl
    - vendor StrongSwan libcrypto-ss/libssl-ss/libcurl-ss

10.5.2 Normal-system versus VNDK-29 divergence proven

  [SYSTEM_NORMAL_VS_VNDK29_DIVERGENT_64BIT]
  Same-name 64-bit normal system and VNDK-29 copies differ for checked crypto and
  keymaster/gatekeeper support files.

    libcrypto.so:
      /system/lib64/libcrypto.so
        bytes=1264696
        sha256=ae1073ac09149912b48fff82336ca313d71ab2e74339b91fcb8aae204b015c29
      /system/lib64/vndk-29/libcrypto.so
        bytes=1149976
        sha256=91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

    libgatekeeper.so:
      /system/lib64/libgatekeeper.so
        bytes=36344
        sha256=8a5cb343fb50f9e4e8ab26818afcdde9fec4297b17a5bdcc8f8333e7903b927e
      /system/lib64/vndk-29/libgatekeeper.so
        bytes=28072
        sha256=30179ec57751d3ddeeb1fbe3d1bb63e4c896d154b32eef7635f7b758ef51ac76

    android.hardware.gatekeeper@1.0.so:
      /system/lib64/android.hardware.gatekeeper@1.0.so
        bytes=92856
        sha256=af7b0b07295cfaa5e3f36553bf11a768808140153dcdbaa2d7c14242d622d099
      /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so
        bytes=92856
        sha256=f97e190288ec175fc21c735920ce5359a9dbadda6660a271c26290287e29fef5

    android.hardware.keymaster@3.0.so:
      /system/lib64/android.hardware.keymaster@3.0.so
        bytes=140880
        sha256=94f8c933b7283256fe10b14cd51a5f98b819be34f0f05209e6e7a3f445b987f3
      /system/lib64/vndk-29/android.hardware.keymaster@3.0.so
        bytes=140880
        sha256=5021a6d916dad6d8b437f3b2d95303d2aeb9e192657efdaa71eabb165ea9de4e

    android.hardware.keymaster@4.0.so:
      /system/lib64/android.hardware.keymaster@4.0.so
        bytes=158504
        sha256=d22bc211c675a4e5c5617094e19d38e908f865a5d2bffcf03d18ccdaa9c038f4
      /system/lib64/vndk-29/android.hardware.keymaster@4.0.so
        bytes=158504
        sha256=23269167ccbc0a57ccfd919876a93a53b4a2e3e0f9edbc44899aab2bb8787000

    libkeymaster_messages.so:
      /system/lib64/libkeymaster_messages.so
        bytes=101240
        sha256=518ca208ef2fbf87beb3a9d33df5042782aa8659a6a9ab0407bf2f941d850693
      /system/lib64/vndk-29/libkeymaster_messages.so
        bytes=84024
        sha256=b2b26e32b164dfeb6ade1e3a228a1822229d3763c90d0945ca2419284fbcca23

    libkeymaster_portable.so:
      /system/lib64/libkeymaster_portable.so
        bytes=330536
        sha256=fa2f7614215593c3bf011be4c6bfbcf01e9699683f277276515670cf18b925e1
      /system/lib64/vndk-29/libkeymaster_portable.so
        bytes=248912
        sha256=fbbb7c913eba10708b88e96f44e8535a8e49241dcecdfa1350bde89fc2a48dc3

  [SAME_BASENAME_SAME_SIZE_DIFFERENT_PAYLOAD]
  At least android.hardware.gatekeeper@1.0.so, android.hardware.keymaster@3.0.so,
  and android.hardware.keymaster@4.0.so have same basename and size across
  normal-system and VNDK-29 locations but different SHA256 payloads.

10.5.3 Vendor/system same-size divergence

  [SAME_BASENAME_SAME_SIZE_DIFFERENT_PAYLOAD_CONFIRMED_LIVE]
  /system/lib64/libkeymaster4support.so and /vendor/lib64/libkeymaster4support.so
  are same-size copies with different hashes:

    /system/lib64/libkeymaster4support.so
      bytes=165776
      sha256=62615e23c17faed12087b305a7a90db39eb4fbc75ddcfb8a5466c5798007628e

    /vendor/lib64/libkeymaster4support.so
      bytes=165776
      sha256=a20193a3b6706d3e90d1822afbff0af31e8ec73f6a6dee8c1d706d16139e4090

################################################################################
# 10.6 — VENDOR BLOB INVENTORY AND CLASSIFICATION MODEL
################################################################################

10.6.1 Canonical requirement

  [VENDOR_BLOB_INVENTORY_REQUIRED]
  Final Section 10 must classify all 1,468 vendor files and every vendor ELF blob
  into required / optional / ghost / cross-reference / pending.

  Required final fields per vendor file:
    - file path
    - file type
    - size
    - SHA256
    - ELF identity when ELF
    - owner subsystem
    - DT_NEEDED provider list
    - dlopen target status
    - RPATH/RUNPATH status
    - namespace provider status
    - runtime active/inactive/unknown
    - classification
    - evidence source

10.6.2 Current required vendor/security blob anchors

  [REQUIRED_VENDOR_SECURITY_BLOBS_PARTIAL]
  Already proven or strongly anchored required security/crypto blobs include:

    /vendor/bin/hw/android.hardware.keymaster@4.0-service
      bytes=11848
      sha256=712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8
      classification: required, live runtime service

    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
      bytes=11600
      sha256=53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a
      classification: required, live runtime service

    /vendor/lib64/libkeymaster4.so
      bytes=91032
      sha256=154557c43bbd6492203197c83f3ed8c808e15f5721ea3eb4c90b8edc265b48fd
      classification: required for keymaster HAL chain

    /vendor/lib64/libkeymaster4support.so
      bytes=165776
      sha256=a20193a3b6706d3e90d1822afbff0af31e8ec73f6a6dee8c1d706d16139e4090
      classification: required for vendor keymaster chain

    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
      bytes=20600
      sha256=82a911dcfe40e0abbb843aa824fd25db979002279b3d6410cafbc80c2e37073c
      classification: required for gatekeeper HAL chain

    /vendor/lib64/hw/libSoftGatekeeper.so
      bytes=32536
      sha256=a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a
      classification: required by live software gatekeeper chain

    /vendor/lib64/hw/gatekeeper.default.so
      bytes=32536
      sha256=a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a
      classification: same payload as libSoftGatekeeper; vendor gatekeeper module alias/copy

10.6.3 Current DRM/CAS/Widevine vendor blob anchors

  [REQUIRED_DRM_CAS_WIDEVINE_BLOBS_PARTIAL]

    /vendor/bin/hw/android.hardware.cas@1.1-service
      bytes=39696
      sha256=2a71aa05330efe843583c4f1069680356b5516eb0b93ab923e480e65a746cdc9

    /vendor/bin/hw/android.hardware.drm@1.0-service
      bytes=11120
      sha256=ae17cace2c9482931740abe88db93d1217fb456c244c8f1c1e955e2b643f2b8b

    /vendor/bin/hw/android.hardware.drm@1.2-service.clearkey
      bytes=152336
      sha256=b8aca481ed3ab1d6723fb20ffeff9ddaffdd30ef8a974cecd012119d2826ade3

    /vendor/bin/hw/android.hardware.drm@1.2-service.widevine
      bytes=24604
      sha256=36aa79155c6f737d12d1380ce3c89fbbedc5efca61fc03c9ac128c3d391f9abd

    /vendor/lib/hw/android.hardware.drm@1.0-impl.so
      bytes=74452
      sha256=5c0750704fee32542a46759c65c34fc7ea822260578e153e0ae1525f8084694c

    /vendor/lib/mediadrm/libwvdrmengine.so
      bytes=2602656
      sha256=365dc743406008835b5ee908cc7f45dad3c1340b761a61fa494a8ea869662781

    /vendor/lib/mediadrm/libmockdrmcryptoplugin.so
      bytes=45368
      sha256=5149b460612e1f370e4baa0766fda29ac7880d727cf1bff26ce821b1c4347439

    /vendor/lib/mediadrm/libdrmclearkeyplugin.so
      bytes=47212
      sha256=23bc0ecd437b2d34e4aa9c7220b5156c6256fcba7aad6c0ad57857595139cca1

    /vendor/lib/libwvhidl.so
      bytes=2696544
      sha256=7fdc3adffd0a3271a4613ad7e781398ea8e2a8bd40907f992af47e03a3c9a5a2

10.6.4 Current StrongSwan/WFC/IPsec vendor blob anchors

  [REQUIRED_STRONGSWAN_WFC_IPSEC_BLOBS_PARTIAL]

    /vendor/bin/starter
      bytes=80240
      sha256=c98a70b761bffcb5ad5c726ba2fd02c587cc370d58d40cc0911ae283d30ffc22

    /vendor/bin/charon
      bytes=14240
      sha256=f5194e62ae17a4e1f308f37095833706f1483ac0607792a420adcb791d905afe

    /vendor/bin/ipsec_mon
      bytes=50264
      sha256=20e9049844291dea861052a8ed755aac74393a750c3676a8014b45fd51a7ec40

    /vendor/bin/epdg_wod
      bytes=137200
      sha256=4b471aa9c72132d600c0d135b7fee0da4795c26e67c0edad64fc8904f1821d16

    /vendor/bin/wfca
      bytes=71232
      sha256=0aaef1c50d6d6d78cc394f40a97d5efe2424a4674bb8e17a192e8329088c7c1b

    /vendor/lib64/libcrypto-ss.so
      bytes=1787984
      sha256=33e2fd0f928385c31639e5116494af66ab712137266b790c59a787a64c515465

    /vendor/lib64/libssl-ss.so
      bytes=447936
      sha256=e42a74843a7510ec4939745efc375c350516c84cb3807474304cbb2bf4bde2a0

    /vendor/lib64/libcurl-ss.so
      bytes=280312
      sha256=30313b79ad1b5ae627db7620f33ff60d3c2984f4ee76d03306149831504cc1cb

    /vendor/lib64/libhydra.so
      bytes=93248
      sha256=ed5a1c9fcf15783f3fe68e9b625b6a42bc03bd8a5343595d904eda2457ea5d88

    /vendor/lib64/libstrongswan.so
      bytes=562944
      sha256=ffa8647c5415ff5a6bbcb3a934236be4ab0db51f1dd22a3ee3d919f249f7879c

    /vendor/lib64/libcharon-ss.so
      bytes=739544
      sha256=f63c724129763d0a4aa6ef84ed0e231b3f2c483c826b7b6d3db352e01d098915

    /vendor/lib64/libsimaka.so
      bytes=30792
      sha256=7a517868aa9ff888f908de3083cf3264d60d7aa1818ad07a254630487ba469d7

    /vendor/lib64/libwo.so
      bytes=54912
      sha256=d42122dee2f2083592efce4268fbf5026247d5f4df6cd5672b98d2506a16afbb

10.6.5 Current subsystem cross-reference vendor blobs

  [VENDOR_SUBSYSTEM_BLOB_CROSS_REFERENCE]
  Vendor blob classification must consume finalized evidence from earlier sections:

    Section 05 graphics/display:
      gralloc.mt6765.so
      hwcomposer.mt6765.so
      GPU HAL services
      PowerVR/RGX/GE8320 libraries and firmware cross-reference

    Section 06 camera:
      camerahalserver
      provider@2.4 impl
      libcamalgo.*
      lib3a.ccu.dm / lib3a.ccu.pm
      camera bgservice/lomoeffect libraries

    Section 07 audio:
      audio HAL, policy, routes, parameter blobs

    Section 08 radio/telephony/modem:
      mtkfusionrild
      libmtk-ril.so
      mtk radio HAL / RadioEx / RadioOp
      CCCI daemons
      IMS/VoLTE/WFC services
      RemoteSimlock/modem-security adjacency when dependency evidence applies

    Section 09 connectivity:
      wmt_drv.ko
      wlan_drv_gen4m.ko
      bt_drv.ko
      gps_drv.ko
      wpa_supplicant
      hostapd
      mnld
      mtk_agpsd
      WMT/Wi-Fi/BT/GNSS firmware cross-references

  These items are not closed in Section 10 until their vendor ELF dependency
  chains, runtime maps where active, and required/optional/ghost status are
  entered in the vendor blob inventory.

################################################################################
# 10.7 — BLOB DEPENDENCY MAP — ALL 1,468 VENDOR FILES
################################################################################

10.7.1 Canonical requirement

  [BLOB_DEPENDENCY_MAP_1468_REQUIRED]
  Section 10 final must include or reference a complete dependency map for all
  1,468 vendor files. The map must include every ELF dependency edge and every
  non-ELF config/data/firmware owner relationship required for classification.

10.7.2 Required dependency edge fields

  For each ELF:
    - file path
    - SHA256
    - ELF class / machine / type
    - SONAME
    - DT_NEEDED list
    - RPATH/RUNPATH
    - Build ID
    - provider resolution for each DT_NEEDED item
    - unresolved dependency status
    - dlopen target status
    - runtime mapped status
    - owning service/init/hwservice if known

  For each non-ELF vendor file:
    - owner subsystem
    - consumer process/library
    - hash
    - runtime read/write status if known
    - classification

10.7.3 Current dependency-map status

  [DEPENDENCY_MAP_PARTIAL]
  Key crypto/security chains have partial static and live maps. Full 1,468-file
  vendor dependency graph is not yet complete.

  [FULL_VENDOR_ELF_DYNAMIC_MAP_PENDING]
  A complete readelf/sha256 dynamic map for vendor and system/system/vendor ELF
  files remains a blocking open item.

################################################################################
# 10.8 — KEYMASTER 4.0 DEPENDENCY AND RUNTIME MAP
################################################################################

10.8.1 Static identity

  [KEYMASTER_VENDOR_SERVICE_STATIC_ID]
    /vendor/bin/hw/android.hardware.keymaster@4.0-service
      bytes=11848
      sha256=712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8

  [KEYMASTER_VENDOR_LIBS_STATIC_ID]
    /vendor/lib64/libkeymaster4.so
      bytes=91032
      sha256=154557c43bbd6492203197c83f3ed8c808e15f5721ea3eb4c90b8edc265b48fd

    /vendor/lib64/libkeymaster4support.so
      bytes=165776
      sha256=a20193a3b6706d3e90d1822afbff0af31e8ec73f6a6dee8c1d706d16139e4090

10.8.2 DT_NEEDED chain

  [KEYMASTER_STATIC_DT_NEEDED_CAPTURED]
  /vendor/bin/hw/android.hardware.keymaster@4.0-service needs:
    - android.hardware.keymaster@4.0.so
    - libbase.so
    - libcutils.so
    - libhardware.so
    - libhidlbase.so
    - libhidltransport.so
    - libkeymaster4.so
    - liblog.so
    - libutils.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

  /vendor/lib64/libkeymaster4.so needs:
    - libkeymaster_messages.so
    - android.hardware.keymaster@4.0.so
    - libcrypto.so
    - libcutils.so
    - libbase.so
    - libhidlbase.so
    - libhidltransport.so
    - libkeymaster_portable.so
    - libpuresoftkeymasterdevice.so
    - liblog.so
    - libutils.so
    - libkeymaster4support.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

  /vendor/lib64/libkeymaster4support.so needs:
    - android.hardware.keymaster@3.0.so
    - android.hardware.keymaster@4.0.so
    - libbase.so
    - libcrypto.so
    - libhardware.so
    - libhidlbase.so
    - libhidltransport.so
    - libutils.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

10.8.3 Runtime map

  [LIVE_KEYMASTER_RUNTIME_MAP_CAPTURED]
  Process:
    /vendor/bin/hw/android.hardware.keymaster@4.0-service
    PID observed: 413
    domain: u:r:hal_keymaster_default:s0

  Live mapped providers include:
    - /vendor/bin/hw/android.hardware.keymaster@4.0-service
    - /vendor/lib64/libkeymaster4.so
    - /vendor/lib64/libkeymaster4support.so
    - /system/lib64/vndk-29/android.hardware.keymaster@3.0.so
    - /system/lib64/vndk-29/android.hardware.keymaster@4.0.so
    - /system/lib64/vndk-29/libcrypto.so
    - /system/lib64/vndk-29/libkeymaster_messages.so
    - /system/lib64/vndk-29/libkeymaster_portable.so
    - /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
    - /system/lib64/vndk-sp-29 support libraries

  [KEYMASTER_SOFTWARE_IMPL_CONFIRMED_STATIC]
  The live/static chain resolves through libpuresoftkeymasterdevice.so. No vendor
  hardware-specific keymaster HAL module path was proven in this capture.

################################################################################
# 10.9 — GATEKEEPER 1.0 DEPENDENCY AND RUNTIME MAP
################################################################################

10.9.1 Static identity

  [GATEKEEPER_VENDOR_SERVICE_STATIC_ID]
    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
      bytes=11600
      sha256=53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a

  [GATEKEEPER_VENDOR_IMPL_STATIC_ID]
    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
      bytes=20600
      sha256=82a911dcfe40e0abbb843aa824fd25db979002279b3d6410cafbc80c2e37073c

    /vendor/lib64/hw/gatekeeper.default.so
      bytes=32536
      sha256=a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a
      SONAME: libSoftGatekeeper.so

    /vendor/lib64/hw/libSoftGatekeeper.so
      bytes=32536
      sha256=a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a
      SONAME: libSoftGatekeeper.so

  [GATEKEEPER_DEFAULT_EQUALS_SOFTGATEKEEPER]
  gatekeeper.default.so and libSoftGatekeeper.so are byte-identical in the checked
  64-bit vendor path.

10.9.2 DT_NEEDED chain

  [GATEKEEPER_STATIC_DT_NEEDED_CAPTURED]
  /vendor/bin/hw/android.hardware.gatekeeper@1.0-service needs:
    - android.hardware.gatekeeper@1.0.so
    - libhardware.so
    - libhidlbase.so
    - libhidltransport.so
    - libutils.so
    - liblog.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

  /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so needs:
    - android.hardware.gatekeeper@1.0.so
    - libhardware.so
    - libhidlbase.so
    - libhidltransport.so
    - libutils.so
    - liblog.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

  /vendor/lib64/hw/libSoftGatekeeper.so and gatekeeper.default.so need:
    - libgatekeeper.so
    - libhardware.so
    - libbase.so
    - libutils.so
    - libcrypto.so
    - liblog.so
    - libc++.so
    - libc.so
    - libm.so
    - libdl.so

10.9.3 Runtime map

  [LIVE_GATEKEEPER_RUNTIME_MAP_CAPTURED]
  Process:
    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
    PID observed: 523
    domain: u:r:hal_gatekeeper_default:s0

  Live mapped providers include:
    - /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
    - /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
    - /vendor/lib64/hw/libSoftGatekeeper.so
    - /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so
    - /system/lib64/vndk-29/libcrypto.so
    - /system/lib64/vndk-29/libgatekeeper.so
    - VNDK-SP support substrate

  [GATEKEEPER_SOFTWARE_IMPL_CONFIRMED_STATIC]
  The chain resolves through software gatekeeper payloads in vendor/hw and VNDK
  support providers.

################################################################################
# 10.10 — SYSTEM KEYSTORE, GATEKEEPERD, VOLD, AND FBE RUNTIME MAP
################################################################################

10.10.1 System keystore

  [KEYSTORE_RUNTIME_MAP_CAPTURED]
  Process:
    /system/bin/keystore
    PID observed: 655
    domain: u:r:keystore:s0

  Live mapped providers include:
    - /system/bin/keystore
    - /system/lib64/libcrypto.so
    - /system/lib64/libsoftkeymasterdevice.so
    - /system/lib64/libkeymaster4support.so
    - /system/lib64/libkeymaster_messages.so
    - /system/lib64/libkeymaster_portable.so
    - /system/lib64/android.hardware.keymaster@3.0.so
    - /system/lib64/android.hardware.keymaster@4.0.so

  [SYSTEM_KEYSTORE_PROVIDER_HASHES_CAPTURED]
    /system/bin/keystore
      bytes=477632
      sha256=1ecbe5b77ff54515fb525d7837df8b243a1d23b1b3e6afef06bb4e893354cf43

    /system/lib64/libsoftkeymasterdevice.so
      bytes=207808
      sha256=168af4cdb3eeb40e5c9bf01d8a22d89ed7c7e9411825b9b88f642995bba18f60

10.10.2 System gatekeeperd

  [GATEKEEPERD_RUNTIME_MAP_CAPTURED]
  Process:
    /system/bin/gatekeeperd
    PID observed: 711
    domain: u:r:gatekeeperd:s0

  Live mapped providers include:
    - /system/bin/gatekeeperd
    - /system/lib64/libcrypto.so
    - /system/lib64/libgatekeeper.so
    - /system/lib64/libkeymaster4support.so
    - /system/lib64/android.hardware.gatekeeper@1.0.so
    - /system/lib64/android.hardware.keymaster@3.0.so
    - /system/lib64/android.hardware.keymaster@4.0.so

  [GATEKEEPERD_PROVIDER_HASH_CAPTURED]
    /system/bin/gatekeeperd
      bytes=45336
      sha256=c4e828bda7bdf0a4fbcd329120e01c18cbc7ef0e3246d459db31aa763695e8e9

10.10.3 Vold and FBE

  [VOLD_RUNTIME_MAP_CAPTURED]
  Process:
    /system/bin/vold
    PID observed: 372
    domain: u:r:vold:s0

  Live mapped providers include:
    - /system/bin/vold
    - /system/lib64/libfscrypt.so
    - /system/lib64/libcrypto.so
    - /system/lib64/libkeymaster4support.so
    - /system/lib64/android.hardware.keymaster@3.0.so
    - /system/lib64/android.hardware.keymaster@4.0.so
    - normal-system support libraries

  [VOLD_PROVIDER_HASH_CAPTURED]
    /system/bin/vold
      bytes=901584
      sha256=39b04e7680f3fbf59d3eaf195dfa99d45eb1120d798b95b5140905a658da7b4e

10.10.4 FBE runtime state

  [FBE_RUNTIME_KEY_MATERIAL_CONFIRMED]
  Live FBE runtime key material paths observed:
    - /data/misc/vold/user_keys/ce/0/current/stretching
    - /data/misc/vold/user_keys/ce/0/current/encrypted_key
    - /data/misc/vold/user_keys/ce/0/current/keymaster_key_blob
    - /data/misc/vold/user_keys/ce/0/current/keymaster_key_blob_upgraded
    - /data/misc/vold/user_keys/ce/0/current/secdiscardable
    - /data/misc/vold/user_keys/ce/0/current/version
    - /data/misc/vold/user_keys/de/0/current/stretching
    - /data/misc/vold/user_keys/de/0/current/encrypted_key
    - /data/misc/vold/user_keys/de/0/current/keymaster_key_blob
    - /data/misc/vold/user_keys/de/0/current/keymaster_key_blob_upgraded
    - /data/misc/vold/user_keys/de/0/current/secdiscardable
    - /data/misc/vold/user_keys/de/0/current/version

  [PERMISSION_ANOMALY]
  keymaster_key_blob_upgraded appeared mode 666 in both CE and DE user key paths
  in prior runtime file capture. This remains a Section 10 security-relevant
  runtime fact and cross-references Section 03/16.

################################################################################
# 10.11 — DRM, CAS, WIDEVINE, AND MEDIA CRYPTO MAP
################################################################################

10.11.1 Runtime services

  [LIVE_DRM_CAS_WIDEVINE_SERVICES_CAPTURED]
  Live running services/processes include:
    - android.hardware.cas@1.1-service
    - android.hardware.drm@1.0-service
    - android.hardware.drm@1.2-service.clearkey
    - android.hardware.drm@1.2-service.widevine
    - drmserver
    - mediadrmserver

10.11.2 Runtime mapped providers

  [LIVE_DRM_CAS_WIDEVINE_IPSEC_MAPS_CLEAN_CAPTURED]
  CAS process maps VNDK-29 CAS libraries and VNDK-SP support.

  DRM default process maps:
    - /vendor/lib/mediadrm/libwvdrmengine.so
    - /vendor/lib/mediadrm/libmockdrmcryptoplugin.so
    - /vendor/lib/mediadrm/libdrmclearkeyplugin.so
    - /vendor/lib/hw/android.hardware.drm@1.0-impl.so
    - /system/lib/vndk-29/libcrypto.so

  ClearKey process maps:
    - DRM 1.0/1.1/1.2 VNDK providers
    - VNDK-29 libcrypto
    - VNDK-SP support

  Widevine process maps:
    - /vendor/lib/libwvhidl.so
    - DRM 1.0/1.1/1.2 VNDK providers
    - VNDK-SP support

10.11.3 Runtime state

  [WIDEVINE_L3_RUNTIME_STATE_PRESENT]
  Runtime state observed:
    /data/vendor/mediadrm/IDM1013/L3

  Files observed include:
    - ay64.dat
    - ay64.dat5
    - ay64.dat6
    - usgtable.bin
    - cert*.bin series

  [CURRENT_RESEARCH_CAPTURE_ONLY]
  Widevine L3 is current research-capture state. Stock-locked device state may
  differ and remains boundary-marked.

################################################################################
# 10.12 — STRONGSWAN, ePDG, WFC, WOD, AND IPSEC MAP
################################################################################

10.12.1 Runtime processes

  [LIVE_STRONGSWAN_WFC_IPSEC_PROCESSES_CAPTURED]
  Live processes include:
    - /vendor/bin/starter
    - /vendor/bin/charon --use-syslog
    - /vendor/bin/ipsec_mon
    - /vendor/bin/epdg_wod
    - /vendor/bin/wfca

10.12.2 Runtime providers

  [STRONGSWAN_IPSEC_PROVIDER_HASHED]
  StrongSwan/WFC/IPsec chain maps:
    - /vendor/lib64/libcrypto-ss.so
    - /vendor/lib64/libssl-ss.so
    - /vendor/lib64/libcurl-ss.so
    - /vendor/lib64/libhydra.so
    - /vendor/lib64/libstrongswan.so
    - /vendor/lib64/libcharon-ss.so
    - /vendor/lib64/libsimaka.so
    - /vendor/lib64/libwo.so

10.12.3 Runtime sockets/state

  [WFC_IPSEC_SOCKET_FANOUT_CONFIRMED]
  Relevant sockets and owners:
    - /data/vendor/ipsec/charon.ctl -> /vendor/bin/charon, domain u:r:ipsec:s0
    - @wod_ipsec -> /vendor/bin/epdg_wod, domain u:r:epdg_wod:s0
    - @wod_dns -> /vendor/bin/epdg_wod, domain u:r:epdg_wod:s0
    - @MDM_rtp_OTA_msg_ctrl -> /vendor/bin/wfca, domain u:r:wfca:s0

  [VENDOR_IPSEC_RUNTIME_STATE_PRESENT]
  Runtime state includes:
    - /data/vendor/ipsec/charon.pid
    - /data/vendor/ipsec/starter.charon.pid
    - /data/vendor/ipsec/wo_apn.conf
    - /data/vendor/ipsec/wo/pdn_0_24.conf

################################################################################
# 10.13 — DLOPEN TARGETS AND GHIDRA STATUS
################################################################################

10.13.1 Canonical requirement

  [DLOPEN_TARGETS_REQUIRED]
  Each vendor blob must receive a dlopen status:
    - [NO_DLOPEN_FOUND]
    - [DLOPEN_STATIC_STRING_FOUND]
    - [DLOPEN_DYNAMIC_TARGET_UNRESOLVED]
    - [DLOPEN_TARGET_RESOLVED]
    - [GHIDRA_REQUIRED]
    - [GHIDRA_COMPLETE]

10.13.2 Current status

  [GHIDRA_DLOPEN_STATUS_PENDING]
  Full Ghidra analysis is not complete. This is a blocking final closure item.

  [STATIC_STRINGS_NOT_ENOUGH]
  Strings-only target discovery is useful lead generation but does not close
  dlopen/call-path status for stripped or dynamically constructed target names.

10.13.3 Priority dlopen/call-path targets

  Priority closure list:
    - keymaster/gatekeeper vendor services and libraries
    - libkeymaster4.so
    - libkeymaster4support.so
    - libSoftGatekeeper.so
    - DRM/Widevine/CAS service and plugins
    - libwvhidl.so
    - libwvdrmengine.so
    - libcrypto-ss/libssl-ss/libcurl-ss/libcharon-ss/libstrongswan
    - epdg_wod / wfca / libwo.so
    - mtkfusionrild / RadioEx / RadioOp / RemoteSimlock / capctrl adjacency
    - vendor HALs with dlopen/hw_get_module patterns
    - graphics/camera/audio/connectivity blobs from Sections 05-09

################################################################################
# 10.14 — PATCHELF, RPATH, RUNPATH, AND CORRECTION TARGETS
################################################################################

10.14.1 Canonical requirement

  [PATCHELF_RPATH_TARGETS_REQUIRED]
  Section 10 final must identify every blob that requires:
    - RPATH correction
    - RUNPATH correction
    - DT_NEEDED rewrite
    - SONAME handling
    - namespace relocation avoidance
    - no-patch / preserve-as-stock marking

10.14.2 Current status

  [NO_RPATH_EMITTED_IN_CHECKED_SECURITY_OBJECTS]
  Checked keymaster/gatekeeper objects did not emit RPATH/RUNPATH in the captured
  readelf output.

  [FULL_RPATH_RUNPATH_SCAN_PENDING]
  This is not global closure. Full vendor ELF scan remains required.

10.14.3 Correction doctrine

  [PRESERVE_STOCK_PROVIDER_WHEN_NAMESPACE_PROVEN]
  If a vendor blob resolves cleanly through stock ld.config namespaces and live
  runtime maps, patchelf is not applied just because a clean AOSP build is a
  downstream consumer. Patching is a last resort after provider mismatch is proven.

################################################################################
# 10.15 — ODM-MODIFIED VNDK LIBRARIES AND SYMBOL-TABLE DELTA
################################################################################

10.15.1 Canonical requirement

  [ODM_MODIFIED_VNDK_IDENTIFICATION_REQUIRED]
  The section must identify ODM-modified VNDK libraries with different symbol
  tables. This requires per-library symbol extraction and comparison.

10.15.2 Current evidence

  [PAYLOAD_DIVERGENCE_PROVEN_NOT_SYMBOL_DELTA_COMPLETE]
  Multiple same-name libraries were proven divergent by SHA256 and/or size across
  normal system and VNDK/VNDK-SP namespaces. This is not identical to full symbol
  delta closure.

  Proven divergence examples include:
    - libcrypto.so normal system vs VNDK-29
    - libgatekeeper.so normal system vs VNDK-29
    - android.hardware.keymaster@3.0.so normal system vs VNDK-29
    - android.hardware.keymaster@4.0.so normal system vs VNDK-29
    - android.hardware.gatekeeper@1.0.so normal system vs VNDK-29
    - libbase.so normal system vs VNDK-SP-29
    - libcutils.so normal system vs VNDK-SP-29
    - libutils.so normal system vs VNDK-SP-29
    - libhardware.so normal system vs VNDK-SP-29
    - libhidlbase.so normal system vs VNDK-SP-29
    - libhidltransport.so normal system vs VNDK-SP-29
    - libc++.so normal system vs VNDK-SP-29

10.15.3 Open symbol gates

  [SYMBOL_TABLE_DELTA_PENDING]
  Required final tools/outputs:
    - readelf -Ws per candidate
    - nm -D per candidate where applicable
    - exported symbol count
    - imported symbol count
    - added/removed symbol deltas
    - Build ID comparison
    - AOSP/MTK baseline comparison where available

################################################################################
# 10.16 — RUNTIME SERVICE, BINDER, HWBINDER, AND SOCKET CROSS-REFERENCE
################################################################################

10.16.1 Live security services

  [LIVE_HWBINDER_SECURITY_SERVICE_REGISTRATION_CAPTURED]
  Live registered HIDL services include:
    - android.hardware.cas@1.0::IMediaCasService/default
    - android.hardware.cas@1.1::IMediaCasService/default
    - android.hardware.drm@1.0/1.1/1.2 ICryptoFactory/IDrmFactory clearkey/default/widevine
    - android.hardware.gatekeeper@1.0::IGatekeeper/default
    - android.hardware.keymaster@4.0::IKeymasterDevice/default
    - android.hardware.secure_element@1.0::ISecureElement/SIM1

  [LIVE_BINDER_SECURITY_SERVICE_REGISTRATION_CAPTURED]
  Binder services include:
    - persistent_data_block
    - oem_lock
    - lock_settings
    - sec_key_att_app_id_provider
    - drm.drmManager
    - android.service.gatekeeper.IGateKeeperService
    - android.security.keystore
    - media.drm
    - vold

10.16.2 Security-adjacent sockets

  [SOCKET_OWNER_RESOLVED_PARTIAL]
  Resolved socket surfaces relevant to Section 10 classification:
    - /data/system/ndebugsocket -> system_server
    - /data/vendor/ipsec/charon.ctl -> charon
    - @wod_ipsec -> epdg_wod
    - @wod_dns -> epdg_wod
    - @MDM_rtp_OTA_msg_ctrl -> wfca
    - @mobilelogd -> mobile_log_d
    - @bthostlogd -> com.android.bluetooth
    - @netdiag -> netdiag
    - @spa-socket -> thermalloadalgod, inferred from later FD map

  These sockets do not replace the vendor blob inventory. They classify runtime
  activity and owner processes for existing vendor/system services.

10.16.3 GNSS/LBS socket cross-reference

  [GNSS_LBS_SOCKET_OWNER_MAP_CONFIRMED]
  GNSS/LBS socket owners:
    - mnld owns mnld_gps_control_socket
    - mnld owns mnld_epo_download_socket
    - mnld owns mnld_qepo_download_socket
    - mnld owns mnld_mtknav_download_socket
    - mnld owns mtk_hal2mnl
    - mnld owns mtk_agps2mnl
    - GNSS HAL owns mtk_mnl2hal
    - mtk_agpsd owns mtk_mnl2agps
    - mtk_agpsd owns mtk_agps_soc_tls
    - mtk_agpsd owns mtk_agps_soc_supl

  Classification:
    Cross-reference to Sections 09 and 16. Retained in Section 10 only because
    socket ownership helps classify runtime vendor blobs and security-adjacent
    network/GNSS surfaces.

################################################################################
# 10.17 — WIITE / ODM CONTROL PLANE CROSS-REFERENCE BOUNDARY
################################################################################

10.17.1 Boundary

  [WIITE_CONTROL_PLANE_CROSS_REFERENCE_ONLY]
  Wiite/ODM APK behavior is not the core of Section 10. Full behavior belongs to
  Sections 12, 13, and 16. Section 10 retains only facts that affect:
    - system UID runtime provider mapping
    - vendor/framework dependency loading
    - security/crypto chain adjacency
    - hardware/control FDs that influence blob classification
    - ttyS1/nRF/DFU adjacency where it crosses vendor blob/security mapping

10.17.2 Section 10-relevant Wiite facts already proven

  [WIITE_UID1000_RUNTIME_CONTROL_PLANE_CONFIRMED]
  Live UID1000/system_app processes include:
    - com.wiite.WiiteCommon
    - com.wiite.devicedaemon
    - com.wiite.devicedaemon:communication
    - com.wiite.cleantask
    - com.wiite.home
    - com.wiite.wearhealthuart
    - com.wiite.wiite_sleep
    - com.wiite.wiite_bloodoxygen
    - com.wiite.power
    - com.wiite.wiitestore

  Common framework loads include:
    - framework.jar
    - ext.jar
    - telephony-common.jar
    - voip-common.jar
    - ims-common.jar
    - mediatek-telephony-base.jar
    - mediatek-telephony-common.jar
    - mediatek-common.jar
    - mediatek-framework.jar
    - OPCommonTelephony.jar
    - mediatek-ims-common.jar
    - mediatek-ims-base.jar
    - mediatek-telecom-common.jar

  Runtime FDs observed include:
    - /dev/binder
    - /dev/hwbinder in selected processes
    - /dev/dri/renderD128 in selected processes
    - /dev/ion in selected processes
    - /dev/pvr_sync in selected processes
    - /proc/ged in selected processes
    - /proc/perfmgr/perf_ioctl in selected processes
    - /sys/kernel/debug/tracing/trace_marker
    - /dev/ttyS1 opened by com.wiite.devicedaemon:communication

10.17.3 ttyS1 cross-reference

  [TTYS1_WORLD_WRITABLE_CONFIRMED]
  /dev/ttyS1:
    mode: crw-rw-rw-
    owner: system:system
    label: u:object_r:ttyS_device:s0

  [TTYS1_WIITE_COMMUNICATION_PROCESS_CONFIRMED]
  /dev/ttyS1 live owner:
    PID=2144
    domain=u:r:system_app:s0
    cmd=com.wiite.devicedaemon:communication
    FD=52

10.17.4 Wiite package identity snapshot

  [WIITE_UID1000_PRODUCT_APP_IDENTITY_CAPTURED]
  Captured APK/hash identities include:

    /product/priv-app/WearCommon/WearCommon.apk
      sha256=93e7235b7ac8a13c9cf21e5cc17e81fe7d707b2081370c18d965a4084b361b23

    /product/priv-app/WearCommon/oat/arm64/WearCommon.odex
      sha256=fff399e5e6fa640ed8103b4ce744e9d8ceeff5ec323502de12f45ef8dc2405db

    /product/priv-app/WearCommon/oat/arm64/WearCommon.vdex
      sha256=e7efbe28c1fefbf829eb8ddd1ae02961b7e8950eb9a8136288b4c9992bbc8d89

    /product/app/WearCleanTaskPro/WearCleanTaskPro.apk
      sha256=794bde9d636f94e77a89d8e7a64e069bb676c31922cb430f0762342f8ab02be0

    /product/app/WearHomeSportDM101/WearHomeSportDM101.apk
      sha256=856fe9c65ad231852fb99b0c570b7a166d109f139eb0c241e978bdf966d39487

    /product/app/WearHealthStepUart/WearHealthStepUart.apk
      sha256=1cc4e184ef6a2442699424a22f99adeb9dd57bb0c9af788aa7c8c47565dbd781

    /product/app/WearSleepUart/WearSleepUart.apk
      sha256=c29ea8dd02a3c167d1f0490334f03f0cb1189f7f1eba1f88b1a4b155c1042a7e

    /product/app/WearSpoUart/WearSpoUart.apk
      sha256=7e73b2ce152503007bd79ea72967cc0f71f54a927b3cd38fc9dc8e2a54ac20ee

    /product/app/WearSpoUart/oat/arm64/WearSpoUart.odex
      sha256=ef8717233e281e786bc1eb3530d1b3a8cf7c32c57999123c4bf97c2fe591ed2d

    /product/app/WearSpoUart/oat/arm64/WearSpoUart.vdex
      sha256=2922d3fdf95d292c552392a62897d368e7abfa96b9d46914c65f01b832f607b3

    /product/app/WearInChargingUi/WearInChargingUi.apk
      sha256=e751afc5344077b54dd43aa5e5aa7b4a2e2a7fbff51fe93ad417d909d9ad7bb7

    /product/app/WearAppDialStore/WearAppDialStore.apk
      sha256=8ac0787bb1a7289166f413a11f3b21d64570636fea26e125493287483e597dd4

  [NO_OAT_VDEX_AT_CHECKED_PATH]
  Some product app OAT/VDEX paths produced no file at the checked oat/arm64
  location. This is recorded as checked-path absence, not global absence.

10.17.5 Component and permission cross-reference

  [WIITE_COMPONENT_SURFACE_CAPTURED]
  Section 10-relevant component surfaces include:
    - com.wiite.devicedaemon InitActivity with MAIN/DEFAULT/HOME/SETUP_WIZARD
    - com.wiite.devicedaemon DfuActivity
    - com.wiite.devicedaemon DataTestActivity
    - com.wiite.devicedaemon RepairDeviceActivity
    - com.wiite.devicedaemon SecretCodeReceiver for android.provider.Telephony.SECRET_CODE
    - com.wiite.devicedaemon DfuService for no.nordicsemi.android.action.DFU_UPLOAD
    - Wiite clean task boot receiver
    - Wiite home watchface/wallpaper service
    - Wiite health UART service/provider
    - sleep/bloodoxygen messenger services/providers
    - power receiver with power and custom com.wiite.xxx actions
    - Wiite store boot receiver and downloader/cache surfaces

  [UID1000_EFFECTIVE_PERMISSION_SURFACE_CONFIRMED]
  UID1000/system package authority exposes broad granted permission capability.
  Final Section 10 must normalize requested permissions separately from effective
  UID1000 grant wall. This is not to be pasted raw as APK behavior.

################################################################################
# 10.18 — RUNTIME DATA AND STATE REGISTER
################################################################################

10.18.1 Metadata/vold

  [VOLD_METADATA_CHECKPOINT_HASHED]
  /metadata/vold/checkpoint:
    bytes=1
    sha256=d4735e3a265e16eee03f59718b9b5d03019c07d8b6c51f90da3a666eec13ab35

  [PASSWORD_SLOT_DIR_PRESENT]
  /metadata/password_slots exists but no visible slot file was listed in current
  capture.

10.18.2 Keystore/gatekeeper runtime state

  [KEYSTORE_USER0_METADATA_HASHED]
  /data/misc/keystore/.metadata:
    bytes=4
    sha256=67abdd721024f0ff4e0b3f4c2fc13bc5bad42d0b7851d456d88d203d15aaa450

  /data/misc/keystore/timestamp:
    bytes=0
    sha256=e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

  [GATEKEEPER_COLDBOOT_EMPTY_HASHED]
  /data/misc/gatekeeper/.coldboot:
    bytes=0
    sha256=e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

  [KEYSTORE_USER_OBJECTS_PRESENT_REDACT]
  /data/misc/keystore/user_0 contains app/user key/cert objects. The database
  records structure and ownership; public-facing output must not expose personal
  alias strings.

10.18.3 Widevine runtime state

  [WIDEVINE_L3_RUNTIME_STATE_HASHED]
  /data/vendor/mediadrm/IDM1013/L3 contains:
    - ay64.dat
    - ay64.dat5
    - ay64.dat6
    - cert*.bin
    - usgtable.bin

  Known hashes:
    ay64.dat sha256=b36d5f980c8da4927f4423d464ded33dee162a6768ea268f70ff97b1cb3650be
    ay64.dat5 sha256=ef1131aac1b5374a77033527fc4ddf9df16172259b6b1bfe4f5ef55f6fc7b36e
    ay64.dat6 sha256=f8badebc9c027716cf55f3ddd47602d1223a1a36e351eb6e105c32bf8dab9aa1
    usgtable.bin sha256=19a6491b39f77b0bef58639a4e52372fea7b85fd742730cae2d6b8ba78ed06bc

10.18.4 IPsec/WOD runtime state

  [WOD_IPSEC_RUNTIME_CONFIG_HASHED]
  /data/vendor/ipsec runtime files include:
    - charon.pid
    - starter.charon.pid
    - wo_apn.conf
    - wo/pdn_0_24.conf

10.18.5 GPS runtime data anomaly

  [RUNTIME_EXEC_PERMISSION_ANOMALY]
  /data/vendor/gps/MTKNAV.MD5:
    mode=-rwxrw-r--
    owner=gps:gps
    label=u:object_r:gps_data_file:s0
    bytes=37

  This is likely not a runnable binary, but the execute bit is still recorded as
  a runtime permission anomaly under vendor GPS state.

################################################################################
# 10.19 — REQUIRED / OPTIONAL / GHOST CLASSIFICATION — CURRENT DRAFT STATE
################################################################################

10.19.1 Required — currently proven or strongly anchored

  [REQUIRED_PARTIAL]
  Required security/runtime chains currently include:
    - keymaster HAL service and vendor libkeymaster4 chain
    - gatekeeper HAL service and software gatekeeper chain
    - system keystore and softkeymaster chain
    - system gatekeeperd chain
    - system vold/fscrypt/FBE chain
    - DRM/CAS/Widevine/ClearKey services and provider plugins
    - StrongSwan/ePDG/WFC/IPsec chain
    - VNDK-29 providers mapped by those services
    - VNDK-SP-29 support libraries mapped by those services
    - standard support libraries where namespace-specific copy is proven active

10.19.2 Optional / feature-dependent — currently partial

  [OPTIONAL_PARTIAL]
  Feature-dependent surfaces include:
    - Widevine/CAS/DRM depending on media/DRM use-case, but services are live
    - WFC/ePDG/IPsec depending on carrier/SIM/network configuration, but daemons
      are live and sockets/state exist
    - GNSS/LBS sockets depending on location state, but services and sockets are
      live
    - Wiite app store/cache/download surfaces depending on user/app interaction
    - DFU/OTA component surfaces, requiring controlled trigger/call-path analysis

10.19.3 Ghost / do-not-promote — currently partial

  [GHOST_PARTIAL]
  Ghost classification is not complete in Section 10. Known ghost candidates from
  broader project evidence include features declared by BSP but physically absent
  or not connected, such as NFC/HDMI/IR where relevant to software blobs/configs.
  Final Section 10 must not remove these; it must classify them.

################################################################################
# 10.20 — INVALID CAPTURE REGISTER
################################################################################

  [INVALID_CAPTURE_RETAINED]
  Invalid command captures are retained as failed gates, not evidence:
    - APK hash command where $f expanded too early and ls listed root
    - GNSS/LBS socket command where variables expanded too early
    - Wiite package summary command where variables expanded too early
    - ttyS1 command where /cmdline path was produced by bad expansion
    - executable-file command where $root expanded too early
    - recent runtime writes command using unsupported/suppressed find -printf

  These are not used as evidence of absence or presence except to document
  command failure mode.

################################################################################
# 10.21 — OPEN ITEM REGISTER — BLOCKERS FOR FINAL SECTION 10 CLOSURE
################################################################################

10.21.1 Full inventory blockers

  [BLOCKER_FULL_VNDK_29_LIST]
    Complete /system/lib64/vndk-29 and /system/lib/vndk-29 file list with hash,
    SONAME, DT_NEEDED, Build ID, and classification.

  [BLOCKER_FULL_VNDK_SP_29_LIST]
    Complete VNDK-SP-29 39-file list plus hw/ subdirectory with hash, SONAME,
    DT_NEEDED, Build ID, and classification.

  [BLOCKER_VENDOR_1468_DEPENDENCY_MAP]
    Complete vendor file inventory and dependency map for all 1,468 vendor files.

  [BLOCKER_FULL_VENDOR_ELF_DYNAMIC_ID]
    Complete readelf dynamic identity for vendor/system-system-vendor ELF files.

10.21.1A B1E4 inventory blocker closure update

  [BLOCKER_FULL_VNDK_29_LIST_CLOSED_BY_B1E4]
  The complete VNDK-29 64-bit and 32-bit library lists are now embedded in
  Sections 10.3.4 through 10.3.6 of this database document. This closes the
  complete-list blocker only. Symbol-table delta analysis remains open under
  Section 10.15.

  [BLOCKER_FULL_VNDK_SP_29_LIST_CLOSED_BY_B1E4]
  The complete VNDK-SP-29 64-bit and 32-bit library lists, including hw/, are
  now embedded in Sections 10.4.4 through 10.4.6 of this database document.
  This closes the complete-list blocker only. Runtime dependency behavior,
  namespace reachability, and symbol-table delta analysis remain owned by later
  Section 10 gates.

10.21.2 Analysis blockers

  [BLOCKER_DLOPEN_GHIDRA_STATUS_PER_BLOB]
    Every vendor blob requires dlopen status. Ghidra analysis completion required.

  [BLOCKER_SYMBOL_TABLE_DELTA]
    ODM-modified VNDK libraries must be identified by symbol table delta, not only
    SHA256 divergence.

  [BLOCKER_PATCHELF_RPATH_MATRIX]
    RPATH/RUNPATH/patchelf correction targets must be globally scanned and
    classified.

10.21.3 Runtime blockers

  [BLOCKER_RUNTIME_MAP_COVERAGE]
    Runtime maps exist for key security chains but not for every active vendor
    blob/service in Section 10 scope.

  [BLOCKER_STATE_DEPENDENT_RETEST]
    Some runtime surfaces must be rechecked across state changes:
      - clean boot idle
      - SIM registered/LTE active
      - Wi-Fi connected
      - Bluetooth enabled
      - GNSS active
      - health sensor active
      - charging event
      - WFC/ePDG active if carrier permits
      - app store/watchface interaction
      - controlled DFU/OTA component analysis

################################################################################
# 10.22 — DOWNLOADS-ONLY FUTURE GATE POLICY
################################################################################

  [FUTURE_HOST_GATES_DOWNLOADS_ONLY]
  Any future host-generated Section 10 outputs must follow this pattern:

    mkdir -p ~/Downloads/C17S_SECTION10_WORK
    SRC=~/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked
    OUT=~/Downloads/C17S_SECTION10_WORK/<OUTPUT_NAME>.txt
    cd "$SRC"
    : > "$OUT"

  The source tree is read only. The output is under Downloads.

  [NO_RESEARCH_DRIVE_ARTIFACTS]
  No generated output files belong inside the research drive.

################################################################################
# 10.23 — DOWNSTREAM AOSP / ROM CONSUMER NOTE
################################################################################

  [ROM_CONSUMER_NOTE_ONLY]
  A clean AOSP ROM is a downstream consumer of this database. The ROM needs this
  section to know which VNDK/VNDK-SP libraries, vendor blobs, namespace rules,
  security providers, and runtime state paths must resolve. This note does not
  redefine Section 10 as a ROM build document.

  Minimal AOSP consequence:
    - Preserve namespace-correct VNDK-29/VNDK-SP-29 providers unless replaced with
      proven-compatible equivalents.
    - Do not collapse normal-system and VNDK providers by basename.
    - Preserve vendor keymaster/gatekeeper/DRM/CAS/WFC/IPsec provider chains until
      replacement implementations are proven.
    - Treat runtime FBE/keystore/gatekeeper/DRM/IPsec state paths as compatibility
      and security boundaries.

################################################################################
# 10.24 — CURRENT CLOSURE STATEMENT
################################################################################

  [SECTION10_NOT_CLOSED]
  Section 10 is not closed.

  [DATABASE_SCOPE_RESTORED]
  This draft restores the full canonical scope: VNDK-29, VNDK-SP-29, vendor blob
  inventory, 1,468-file dependency mapping, dlopen/Ghidra status, patchelf/RPATH
  targets, and ld.config namespace authority.

  [WHAT_IS_ALREADY_STRONG]
  Strongly populated evidence now exists for:
    - linker namespace model
    - normal-system vs VNDK/VNDK-SP duplicate-provider divergence
    - keymaster service/static/live chain
    - gatekeeper service/static/live chain
    - keystore/gatekeeperd/vold runtime chain
    - DRM/CAS/Widevine runtime providers
    - StrongSwan/WFC/ePDG/IPsec providers
    - runtime state paths under /data/vendor, /data/misc, and /metadata
    - security-adjacent socket owners
    - Section 10-relevant Wiite UID1000 runtime cross-reference

  [WHAT_REMAINS_OPEN]
  Final closure still requires:
    - complete VNDK-29 file list
    - complete VNDK-SP-29 file list including hw/
    - symbol-table delta analysis
    - complete vendor file inventory and dependency map for all 1,468 files
    - per-blob dlopen/Ghidra status
    - global patchelf/RPATH/RUNPATH target matrix
    - final required/optional/ghost classification for every vendor blob


################################################################################
# 10.25 — B1E4 CLOSURE ADDENDUM — VNDK/VNDK-SP COMPLETE LISTS
################################################################################

  [B1E4_COMPLETE_LISTS_EMBEDDED_IN_DATABASE]
  The complete VNDK-29 and VNDK-SP-29 library lists are now embedded directly
  in this Section 10 document as self-contained database tables. These tables
  carry identity, path provenance, DT_NEEDED dependencies, RPATH/RUNPATH status,
  static/live equivalence, live ownership/mode/context, Build ID, SONAME, and
  classification.

  [B1E4_CLOSED_SCOPE_ITEMS]
    - Complete VNDK-29 library list: 64-bit and 32-bit sets.
    - Complete VNDK-SP-29 library list including hw/ subdirectory.

  [B1E4_STILL_OPEN_SCOPE_ITEMS]
    - ODM-modified VNDK libraries identified by symbol-table deltas.
    - Vendor blob classification: required / optional / ghost.
    - Blob dependency map — all 1,468 vendor files.
    - dlopen call targets identified via Ghidra — status per blob.
    - patchelf rpath correction targets.
    - Library namespace declarations in /vendor/etc/ld.config.*.txt.

  [B1E4_DATABASE_BOUNDARY]
  This addendum is evidence in document form. It is not a ROM build note and it
  does not rely on external evidence files for the two completed list items.

################################################################################
# END SECTION 10 DRAFT — FULL SCOPE RESTORED
################################################################################
