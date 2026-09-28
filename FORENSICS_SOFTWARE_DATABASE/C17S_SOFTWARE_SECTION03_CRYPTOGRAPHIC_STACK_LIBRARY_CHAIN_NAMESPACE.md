################################################################################
# C17S SOFTWARE SECTION 03 — CRYPTOGRAPHIC STACK, LIBRARY CHAIN & NAMESPACE ISOLATION
################################################################################
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 03 — CRYPTOGRAPHIC STACK, LIBRARY CHAIN & NAMESPACE ISOLATION
# Date        : 2026-06-14 
################################################################################
# SCOPE:
################################################################################

  This section is the authoritative software-database section for the C17S / LOKMAT
  5 MAX cryptographic runtime, library dependency graph, Android linker namespace
  requirements, and clean-ROM crypto validation gates.


Status:
  Ghidra/deep semantic comparison remains optional for vulnerability attribution and
  ODM divergence proof, but not required to understand or preserve the functional
  crypto/decryption path documented here.

Section doctrine:
  - Do not collapse TWRP recovery behavior into normal Android OS behavior.
  - Do not promote hardware security substrate presence into Android userspace
    crypto use unless process maps or binary dependencies prove that use.
  - Do not call a small HIDL service executable “the implementation” when the real
    implementation is reached through adapter, passthrough, or hw-module layers.
  - Do not add patchelf/RPATH targets to the normal ROM unless first-boot logs prove
    a specific linker/namespace failure.
  - Do not isolate bionic.
  - Preserve VNDK-29 compatibility for retained Android 10 vendor blobs.

################################################################################
# 03.0 SOURCE AUTHORITY, SCOPE, AND EVIDENCE MODEL
################################################################################

03.0.1 Section scope

This section closes the following required scope items:

  1. Complete OpenSSL / BoringSSL / libcrypto chain identification.
  2. ODM/vendor-modified and vendor-owned crypto/security library identification.
  3. Namespace isolation requirements and normal-ROM RPATH/patchelf decision table.
  4. Bionic exception set: libraries that must never be placed inside a private
     crypto bubble.
  5. StrongSwan/ePDG crypto island analysis:
       libcrypto-ss.so
       libssl-ss.so
       libcurl-ss.so
       libcharon-ss.so
       libstrongswan.so
       libhydra.so
       libsimaka.so
  6. Keymaster 4.0 chain: binderized frontend, vendor adapter, passthrough symbols,
     pure-software implementation path, VNDK-29 crypto resolution.
  7. Gatekeeper 1.0 chain: binderized frontend, HIDL_FETCH impl, hw_get_module_by_class,
     gatekeeper.default.so symlink, libSoftGatekeeper implementation, VNDK-29 crypto.
  8. FBE path: fscrypt v1 on kernel 4.9, legacy keyctl fallback, active key storage,
     current credential substrate, and PIN/credential risk gate.
  9. DXCC / TEE / RPMB hardware substrate versus Android userspace crypto path
     separation.
 10. Clean AOSP 11 ROM build consequences and validation gates.

03.0.2 Authority tiers used

  Tier 1 — live rooted device evidence:
    - getprop crypto state
    - /proc/mounts
    - stock fstab crypto flags
    - /data/unencrypted/key and credential storage listings
    - ps/service list state
    - /proc/<pid>/maps
    - live file labels, sizes, and SHA256 values

  Tier 2 — clean extracted filesystem evidence:
    - host-side readelf dependency analysis against super_unpacked source material
    - host-side file/symbol/string checks
    - host path resolution for system/vendor/VNDK files

  Tier 3 — static binary evidence:
    - readelf -d DT_NEEDED / SONAME / FLAGS
    - readelf -Ws exported/undefined symbol checks
    - strings checks for HIDL registration, passthrough, soft-keymaster, soft-gatekeeper,
      software root of trust, and StrongSwan crypto roles

  Tier 4 — prior project authority / cross-reference:
    - Section 00 partition and source-authority model
    - Section 01 init/service graph
    - Section 02 HAL/VINTF runtime and process authority
    - Hardware Section 11 security substrate: DXCC, RPMB, TEE, secure partitions
    - Existing fully decrypting TWRP result, referenced only as recovery-specific proof

03.0.3 Capture boundary

Current live state is a research-unlocked/rooted device state:

  bootloader state        : unlocked / orange research state
  SELinux                 : permissive research state
  Android base            : stock Android 10 / API 29 vendor stack
  target clean ROM        : AOSP 11
  TWRP                    : fully decrypting and closed

TWRP is no longer a build task for this section. It remains documented only as a
recovery-specific compatibility profile that proved the crypto stack can decrypt
when the right dependency isolation is recreated inside recovery.

################################################################################
# 03.1 HARD RULES FOR THIS SECTION
################################################################################

03.1.1 Runtime truth rules

  [RULE_CRYPTO_01]
    A file-present crypto library is not runtime-active unless a process map or
    dependency chain proves it is loaded or required.

  [RULE_CRYPTO_02]
    A HIDL service executable is not automatically the HAL implementation. The
    service may only register a binderized frontend and then delegate to adapter,
    passthrough, or hw-module libraries.

  [RULE_CRYPTO_03]
    The normal AOSP 11 ROM must reproduce Android linker/VNDK/APEX namespace
    behavior. It must not inherit a recovery-only /crypto_isolation solution unless
    a normal-ROM linker failure proves that exception necessary.

  [RULE_CRYPTO_04]
    Generic sonames such as libcrypto.so must be resolved by namespace context.
    The same soname may refer to different physical files depending on whether the
    loading process is system, vendor, VNDK, VNDK-SP, or APEX runtime.

  [RULE_CRYPTO_05]
    libc.so, libm.so, libdl.so, linker, and linker64 are bionic/APEX runtime
    objects. They are explicit non-isolation objects and must not be copied into a
    private crypto namespace.

  [RULE_CRYPTO_06]
    DXCC, TEE, secure partitions, and RPMB are hardware/security substrate facts,
    not evidence that Android Keymaster or Gatekeeper is hardware-backed.

  [RULE_CRYPTO_07]
    PIN/password/credential behavior is a clean-ROM blocking test. A ROM that boots
    but bootloops after PIN enrollment is not cryptographically correct.

03.1.2 Classification tags

  [BINDERIZED_FRONTEND]
  [PASSTHROUGH_ADAPTER]
  [HIDL_FETCH_IMPL]
  [HW_GET_MODULE_BY_CLASS]
  [PURE_SOFT_KEYMASTER]
  [SOFT_GATEKEEPER_HAL]
  [VNDK_29_CRYPTO_DEPENDENT]
  [VNDK_SP_29_COMMON_DEPENDENT]
  [SYSTEM_CRYPTO_DEPENDENT]
  [VENDOR_SS_CRYPTO_ISLAND]
  [BIONIC_EXCEPTION]
  [NO_HW_KEYMASTER_MODULE_FOUND]
  [GATEKEEPER_DEFAULT_SYMLINK_REQUIRED]
  [NO_TEE_RUNTIME_PROOF]
  [NO_RPMB_RUNTIME_PROOF]
  [NO_DXCC_USERSPACE_CRYPTO_PROOF]
  [TWRP_ONLY]
  [NORMAL_ROM_DO_NOT_IMPORT]
  [PATCH_ELF_EMPTY_UNTIL_PROVEN]
  [PIN_CREDENTIAL_CRITICAL]
  [OPEN_LEAD]
  [CLEAN_ROM_BLOCKING_TEST]

################################################################################
# 03.2 CURRENT CRYPTO PROPERTY, FSTAB, AND MOUNT STATE
################################################################################

03.2.1 Live crypto properties

  ro.crypto.state                  : encrypted
  ro.crypto.type                   : file
  ro.crypto.volume.filenames_mode  : aes-256-cts

Classification:
  [VERIFIED]
  [ACTIVE_RUNTIME]
  [FBE]

03.2.2 FBE fstab declaration

  Active /data encryption declaration:

    fileencryption=aes-256-xts

  The fstab declaration provides the contents encryption mode. Filename mode is
  provided separately by ro.crypto.volume.filenames_mode=aes-256-cts.

  Additional /data flags observed in the current stock path:

    checkpoint=block
    quota
    reservedsize=128M

Classification:
  [VERIFIED]
  [FBE_V1_COMPATIBILITY]
  [AOSP_5_COLUMN_FSTAB_STYLE_REQUIRED]

03.2.3 Active mount truth

  /metadata  : /dev/block/platform/bootdevice/by-name/md_udc
  /data      : /dev/block/platform/bootdevice/by-name/userdata
  /vendor    : dm-2
  /product   : dm-0
  /          : dm-1

The active metadata mount is md_udc, not the generic metadata partition. The generic
metadata partition remains a software/partition trap from the BSP template and must
not be used as the ROM /metadata source.

Classification:
  [VERIFIED]
  [MD_UDC_IS_REAL_METADATA]
  [GENERIC_METADATA_PARTITION_IS_GHOST]
  [ROM_BUILD_CRITICAL]

################################################################################
# 03.3 ACTIVE FBE KEY STORAGE AND CREDENTIAL SUBSTRATE
################################################################################

03.3.1 Active FBE key material path

  Active path:

    /data/unencrypted/key/

  Live contents:

    encrypted_key
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 92

    keymaster_key_blob
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 166

    keymaster_key_blob_upgraded
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 166
      mode        : rw-rw-rw-

    secdiscardable
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 16384

    stretching
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 10

    version
      owner/label : root:root u:object_r:unencrypted_data_file:s0
      size        : 1

Classification:
  [VERIFIED]
  [ACTIVE_FBE_KEY_PATH]
  [KEYMASTER_BLOB_PRESENT]
  [EMPTY_OR_LEGACY_CREDENTIAL_STATE]

03.3.2 Metadata-encryption directory state

  /metadata/vold/metadata_encryption : absent in current evidence

This device does not use the standard /metadata/vold/metadata_encryption path as the
active key-material authority in the current evidence set. The active key material
observed for this section is under /data/unencrypted/key and related vold/user key
locations described by the original database.

Classification:
  [VERIFIED]
  [NEGATIVE_PATH]

03.3.3 Lock settings and Gatekeeper storage state

  Binder services present:

    lock_settings: [com.android.internal.widget.ILockSettings]
    android.service.gatekeeper.IGateKeeperService
    android.security.keystore
    trust
    oem_lock
    persistent_data_block

  Current lock database:

    /data/system/locksettings.db
      owner/label : system:system u:object_r:system_data_file:s0
      size        : 20480

    /data/system/locksettings.db-wal : absent
    /data/system/locksettings.db-shm : absent

  Gatekeeper state:

    /data/system/gatekeeper : absent

    /data/misc/gatekeeper
      owner/label : system:system u:object_r:gatekeeper_data_file:s0
      contents    : .coldboot only

  Keystore state:

    /data/misc/keystore
      owner/label : keystore:keystore u:object_r:keystore_data_file:s0
      contents    : .metadata, timestamp, user_0/

Classification:
  [VERIFIED]
  [LOCK_SETTINGS_SERVICE_PRESENT]
  [GATEKEEPER_SERVICE_PRESENT]
  [KEYSTORE_SERVICE_PRESENT]
  [NO_NORMAL_ENROLLED_GATEKEEPER_HANDLE_OBSERVED]
  [PIN_CREDENTIAL_CRITICAL]

03.3.4 Vendor/wearable lock surface

  Packages observed in lock/face/keyguard search:

    /product/app/FaceUnlockLauncher/FaceUnlockLauncher.apk
      package: com.wiite.faceunlocklauncher

    /system/app/WearScreenLock/WearScreenLock.apk
      package: fdy.wiitter.com

    /system/priv-app/HeilsFaceUnlockDM101/HeilsFaceUnlockDM101.apk
      package: cn.heils.faceunlock

  The stock firmware exposes a nonstandard wearable/face-lock oriented surface rather
  than a normal phone-style lockscreen workflow. This is relevant to the historical
  temporary GSI PIN bootloop because a GSI may exercise framework credential paths
  that the stock wearable UX never normally uses.

Classification:
  [VERIFIED]
  [WEARABLE_LOCK_STACK]
  [FACE_UNLOCK_PRESENT]
  [PIN_BOOTLOOP_INFERENCE_CONTEXT]

03.3.5 Historical GSI PIN bootloop classification

  Historical test:

    - Android 14 GSI booted briefly.
    - Display, touch, Wi-Fi, LTE/4G, VoLTE, GPS, and major hardware paths were observed.
    - Dual sensor stack did not fully work.
    - Setting a PIN caused a bootloop.
    - GSI was removed and factory stock was restored months earlier.
    - No retained logs are available.

  This is not forensically provable from current retained logs. It is classified by
  architecture as an inferred failure class:

    LockSettingsService
      -> Synthetic Password / credential state
      -> Gatekeeper enrollment / verification
      -> Keystore / Keymaster auth-bound key behavior
      -> vold / fscrypt CE unlock
      -> /data/system_ce/0 availability after reboot

  If any wrapper, adapter, passthrough implementation, hw module, VNDK library, bionic
  runtime, or vold/fscrypt path is mismatched, a GSI can appear to boot normally until
  a PIN creates credential-backed state that must survive the next boot.

Classification:
  [OPEN_LEAD]
  [NO_RETAINED_LOG_PROOF]
  [INFERRED_FROM_CRYPTO_STACK]
  [CLEAN_ROM_BLOCKING_TEST]
  [PIN_CREDENTIAL_CRITICAL]

Clean ROM rule:
  Do not solve this by disabling PIN support. A correct clean ROM must pass PIN
  enrollment, reboot, credential verification, and CE unlock.

################################################################################
# 03.4 CRYPTO LIBRARY FAMILY MODEL
################################################################################

03.4.1 Three crypto families

The device carries three distinct crypto/library families that must not be collapsed:

  Family 1 — System / framework crypto

    Examples:
      /system/lib64/libcrypto.so
      /system/lib64/libssl.so
      /apex/com.android.runtime/lib64/libcrypto.so where applicable

    Use class:
      framework/system processes, keystore, gatekeeperd, vold, and system-side
      crypto consumers.

  Family 2 — VNDK-29 crypto

    Examples:
      /system/lib64/vndk-29/libcrypto.so
      /system/lib64/vndk-29/libkeymaster_portable.so
      /system/lib64/vndk-29/libkeymaster_messages.so
      /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
      /system/lib64/vndk-29/libgatekeeper.so

    Use class:
      Android 10 vendor/HAL chains retained inside the AOSP 11 clean ROM.

  Family 3 — Vendor StrongSwan/ePDG crypto island

    Examples:
      /vendor/lib64/libcrypto-ss.so
      /vendor/lib64/libssl-ss.so
      /vendor/lib64/libcurl-ss.so
      /vendor/lib64/libcharon-ss.so
      /vendor/lib64/libstrongswan.so
      /vendor/lib64/libhydra.so
      /vendor/lib64/libsimaka.so

    Use class:
      vendor IPsec, ePDG, Wi-Fi Calling, IMS/WFC support plane.

Classification:
  [THREE_CRYPTO_FAMILIES]
  [NAMESPACE_CRITICAL]
  [DO_NOT_COLLAPSE]

03.4.2 Critical namespace consequence

  Generic DT_NEEDED entries such as libcrypto.so are intentionally ambiguous until
  resolved by Android linker namespace context.

  Keymaster/Gatekeeper generic libcrypto.so:
    resolves to VNDK-29 libcrypto.so in vendor/HAL context.

  StrongSwan/ePDG crypto:
    does not use generic libcrypto.so for the vendor crypto island; it uses
    libcrypto-ss.so, libssl-ss.so, and libcurl-ss.so.

  System processes:
    use system/APEX/system-side libcrypto and framework libraries.

Classification:
  [NAMESPACE_ISOLATION_REQUIRED]
  [VNDK_29_REQUIRED]
  [VENDOR_SS_CRYPTO_ISLAND]

################################################################################
# 03.5 KEYMASTER 4.0 — COMPLETE LIBRARY CHAIN
################################################################################

03.5.1 Live service identity

  Service executable:
    /vendor/bin/hw/android.hardware.keymaster@4.0-service

  Live file identity:
    label    : u:object_r:hal_keymaster_default_exec:s0
    size     : 11848
    SHA256   : 712bab709c4bd7c0eda9aed83894876447f17e226d53d0cf240e5d93f058d4b8

  Runtime PID observed:
    413

  VINTF/HIDL role:
    android.hardware.keymaster@4.0::IKeymasterDevice/default

Classification:
  [VERIFIED]
  [BINDERIZED_FRONTEND]
  [EARLY_CRYPTO_SERVICE]
  [FBE_BOOT_CRITICAL]

03.5.2 Service frontend symbol/string evidence

  The service executable is stripped, but its symbols/strings show it is a frontend:

    IKeymasterDevice::registerAsService
    CreateKeymasterDevice(SecurityLevel)
    hardware/interfaces/keymaster/4.0/default/service.cpp
    Could not register service for Keymaster 4.0

Conclusion:
  The service binary registers the Keymaster HIDL service and invokes the device
  factory. It is not the full cryptographic implementation by itself.

Classification:
  [BINDERIZED_FRONTEND]
  [NOT_FULL_IMPLEMENTATION]

03.5.3 Keymaster vendor adapter layer

  Primary vendor adapter:
    /vendor/lib64/libkeymaster4.so

  Live identity:
    label    : u:object_r:vendor_file:s0
    size     : 91032
    SHA256   : 154557c43bbd6492203197c83f3ed8c808e15f5721ea3eb4c90b8edc265b48fd

  Host/static symbols and strings include:
    KeymasterPassthroughKeyFactory
    TKeymasterPassthroughEngine<keymaster1_device>
    TKeymasterPassthroughEngine<keymaster2_device>
    KeymasterPassthroughOperation
    AndroidKeymaster4Device
    PureSoftKeymasterContext reference

Classification:
  [VERIFIED]
  [VENDOR_LIB_ADAPTER]
  [PASSTHROUGH_ADAPTER]
  [KEYMASTER_PASSTHROUGH_SYMBOLS]

03.5.4 Keymaster support library

  Vendor support library:
    /vendor/lib64/libkeymaster4support.so

  Live identity:
    label    : u:object_r:vendor_file:s0
    size     : 165776
    SHA256   : a20193a3b6706d3e90d1822afbff0af31e8ec73f6a6dee8c1d706d16139e4090

  Note:
    A system-side /system/lib64/libkeymaster4support.so also exists with a different
    hash and is used by system processes such as vold/gatekeeperd/keystore. The
    Keymaster HAL service maps the vendor libkeymaster4support.so; the system services
    map the system libkeymaster4support.so.

Classification:
  [VERIFIED]
  [VENDOR_KEYMASTER_SUPPORT]
  [SYSTEM_VENDOR_SPLIT]

03.5.5 VNDK-29 pure-soft implementation chain

  /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
    size   : 45944
    SHA256 : 0f03768bf69e364c78f0bb93562cea5562540effe04e8038e224970ce5633097
    evidence strings/symbols:
      PureSoftKeymasterContext
      SoftKeymasterEnforcement
      softwareRootOfTrust
      SoftwareRandomSource
      Android Software Attestation Key
      Android Keystore Software Attestation Root
      Parsed an old softkeymaster key
      Parsed an old keymaster1 software key

  /system/lib64/vndk-29/libkeymaster_portable.so
    size   : 248912
    SHA256 : fbbb7c913eba10708b88e96f44e8535a8e49241dcecdfa1350bde89fc2a48dc3

  /system/lib64/vndk-29/libkeymaster_messages.so
    size   : 84024
    SHA256 : b2b26e32b164dfeb6ade1e3a228a1822229d3763c90d0945ca2419284fbcca23

  /system/lib64/vndk-29/libcrypto.so
    size   : 1149976
    SHA256 : 91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

Classification:
  [PURE_SOFT_KEYMASTER]
  [VNDK_29_CRYPTO_DEPENDENT]
  [SOFTWARE_ROOT_OF_TRUST]
  [SOFTWARE_ATTESTATION]

03.5.6 Keymaster DT_NEEDED chain

  /vendor/bin/hw/android.hardware.keymaster@4.0-service requires:

    android.hardware.keymaster@4.0.so
    libbase.so
    libcutils.so
    libhardware.so
    libhidlbase.so
    libhidltransport.so
    libkeymaster4.so
    liblog.so
    libutils.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  /vendor/lib64/libkeymaster4.so requires:

    libkeymaster_messages.so
    android.hardware.keymaster@4.0.so
    libcrypto.so
    libcutils.so
    libbase.so
    libhidlbase.so
    libhidltransport.so
    libkeymaster_portable.so
    libpuresoftkeymasterdevice.so
    liblog.so
    libutils.so
    libkeymaster4support.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  /vendor/lib64/libkeymaster4support.so requires:

    android.hardware.keymaster@3.0.so
    android.hardware.keymaster@4.0.so
    libbase.so
    libcrypto.so
    libhardware.so
    libhidlbase.so
    libhidltransport.so
    libutils.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  None of the checked keymaster binaries/libraries carried a required RPATH/RUNPATH
  in the captured readelf output. They rely on Android linker namespace resolution.

Classification:
  [DT_NEEDED_CLOSED]
  [NO_RUNPATH_REQUIRED_FOR_NORMAL_ROM]
  [VNDK_29_CRYPTO_DEPENDENT]

03.5.7 Keymaster dependency resolution result

  android.hardware.keymaster@4.0.so
    -> /system/lib64/vndk-29/android.hardware.keymaster@4.0.so
       SHA256 23269167ccbc0a57ccfd919876a93a53b4a2e3e0f9edbc44899aab2bb8787000

  android.hardware.keymaster@3.0.so
    -> /system/lib64/vndk-29/android.hardware.keymaster@3.0.so
       SHA256 5021a6d916dad6d8b437f3b2d95303d2aeb9e192657efdaa71eabb165ea9de4e

  libcrypto.so for Keymaster HAL chain
    -> /system/lib64/vndk-29/libcrypto.so
       SHA256 91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

  libkeymaster_portable.so
    -> /system/lib64/vndk-29/libkeymaster_portable.so
       SHA256 fbbb7c913eba10708b88e96f44e8535a8e49241dcecdfa1350bde89fc2a48dc3

  libkeymaster_messages.so
    -> /system/lib64/vndk-29/libkeymaster_messages.so
       SHA256 b2b26e32b164dfeb6ade1e3a228a1822229d3763c90d0945ca2419284fbcca23

  libpuresoftkeymasterdevice.so
    -> /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
       SHA256 0f03768bf69e364c78f0bb93562cea5562540effe04e8038e224970ce5633097

Classification:
  [RESOLUTION_CLOSED]
  [VNDK_29_REQUIRED]

03.5.8 Keymaster hw module search result

  Live paths checked:

    /vendor/lib/hw
    /vendor/lib64/hw
    /vendor/lib
    /vendor/lib64
    /system/lib/hw
    /system/lib64/hw

  Result:

    No /vendor/lib/hw/keymaster*.so found.
    No /vendor/lib64/hw/keymaster*.so found.
    No /vendor/lib/hw/keymaster.default.so found.
    No /vendor/lib64/hw/keymaster.default.so found.
    No /system/lib/hw/keymaster*.so found.
    No /system/lib64/hw/keymaster*.so found.

Conclusion:
  Keymaster does not use a separate hw/keymaster.default.so module on this device.
  It uses a binderized service frontend, vendor libkeymaster4 adapter, passthrough
  engine symbols, and VNDK-29 pure-soft keymaster implementation libraries.

Classification:
  [NO_HW_KEYMASTER_MODULE_FOUND]
  [PURE_SOFT_KEYMASTER]
  [VNDK_29_CRYPTO_DEPENDENT]

################################################################################
# 03.6 GATEKEEPER 1.0 — COMPLETE LIBRARY CHAIN
################################################################################

03.6.1 Live service identity

  Service executable:
    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service

  Live identity:
    label    : u:object_r:hal_gatekeeper_default_exec:s0
    size     : 11600
    SHA256   : 53fe83926ce938c8f6237c4faba7803df3490426fa9264391431b8f9398c474a

  Runtime PID observed:
    523

Classification:
  [VERIFIED]
  [BINDERIZED_FRONTEND]
  [PIN_CREDENTIAL_CRITICAL]

03.6.2 Service frontend evidence

  Symbols/strings:

    IGatekeeper::getService
    IGatekeeper::registerAsService
    android.hardware.gatekeeper@1.0-service

Conclusion:
  The gatekeeper service binary is a HIDL service frontend and does not contain the
  full gatekeeper implementation.

Classification:
  [BINDERIZED_FRONTEND]
  [NOT_FULL_IMPLEMENTATION]

03.6.3 HIDL_FETCH implementation layer

  64-bit implementation:
    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
      label  : u:object_r:vendor_file:s0
      size   : 20600
      SHA256 : 82a911dcfe40e0abbb843aa824fd25db979002279b3d6410cafbc80c2e37073c

  32-bit implementation:
    /vendor/lib/hw/android.hardware.gatekeeper@1.0-impl.so
      label  : u:object_r:vendor_file:s0
      size   : 19956
      SHA256 : a35914484d83cbd71e78360dd884a3451471f959bf4d98b08f4fa9e3ba24d17c

  Static evidence:

    HIDL_FETCH_IGatekeeper
    hw_get_module_by_class
    implementation::Gatekeeper::enroll
    implementation::Gatekeeper::verify
    implementation::Gatekeeper::deleteUser
    implementation::Gatekeeper::deleteAllUsers
    Unable to open GateKeeper HAL
    Unable to close GateKeeper HAL

Classification:
  [HIDL_FETCH_IMPL]
  [HW_GET_MODULE_BY_CLASS]
  [PASSTHROUGH_MODULE_LOADER]
  [PIN_CREDENTIAL_CRITICAL]

03.6.4 hw module and symlink target

  64-bit module symlink:
    /vendor/lib64/hw/gatekeeper.default.so -> libSoftGatekeeper.so
      symlink hash/target hash : a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a

  64-bit implementation target:
    /vendor/lib64/hw/libSoftGatekeeper.so
      label  : u:object_r:vendor_file:s0
      size   : 32536
      SHA256 : a48349adba6e54500fb39cd4604a8d0979057728b88b7268719d5a8464fc6f9a

  32-bit module symlink:
    /vendor/lib/hw/gatekeeper.default.so -> libSoftGatekeeper.so
      symlink hash/target hash : beef8d59dd10d54e8eb35941ba06e3dd5ef0268481e93c9c17eba3f1b78c5f46

  32-bit implementation target:
    /vendor/lib/hw/libSoftGatekeeper.so
      label  : u:object_r:vendor_file:s0
      size   : 37472
      SHA256 : beef8d59dd10d54e8eb35941ba06e3dd5ef0268481e93c9c17eba3f1b78c5f46

Classification:
  [GATEKEEPER_DEFAULT_SYMLINK_REQUIRED]
  [SOFT_GATEKEEPER_HAL]
  [32_64_ABI_PRESENT]

03.6.5 libSoftGatekeeper implementation evidence

  Static symbols/strings include:

    soft_gatekeeper_open
    soft_gatekeeper_close
    HalAdaptationLayer::enroll
    HalAdaptationLayer::verify
    GateKeeper::Enroll
    GateKeeper::Verify
    GateKeeper::DoVerify
    Soft Gatekeeper device created
    Opening Software Gatekeeper device
    Soft Gatekeeper HAL

Conclusion:
  Gatekeeper is a software Gatekeeper implementation behind a HIDL frontend and
  hw_get_module_by_class dispatch path.

Classification:
  [SOFT_GATEKEEPER_HAL]
  [ENROLL_VERIFY_IMPLEMENTATION]
  [PIN_CREDENTIAL_CRITICAL]

03.6.6 Gatekeeper DT_NEEDED chain

  /vendor/bin/hw/android.hardware.gatekeeper@1.0-service requires:

    android.hardware.gatekeeper@1.0.so
    libhardware.so
    libhidlbase.so
    libhidltransport.so
    libutils.so
    liblog.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so requires:

    android.hardware.gatekeeper@1.0.so
    libhardware.so
    libhidlbase.so
    libhidltransport.so
    libutils.so
    liblog.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  /vendor/lib64/hw/libSoftGatekeeper.so requires:

    libgatekeeper.so
    libhardware.so
    libbase.so
    libutils.so
    libcrypto.so
    liblog.so
    libc++.so
    libc.so
    libm.so
    libdl.so

Classification:
  [DT_NEEDED_CLOSED]
  [SOFT_GATEKEEPER_HAL]
  [VNDK_29_CRYPTO_DEPENDENT]

03.6.7 Gatekeeper dependency resolution result

  android.hardware.gatekeeper@1.0.so
    -> /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so
       SHA256 f97e190288ec175fc21c735920ce5359a9dbadda6660a271c26290287e29fef5

  libgatekeeper.so for vendor HAL chain
    -> /system/lib64/vndk-29/libgatekeeper.so
       SHA256 30179ec57751d3ddeeb1fbe3d1bb63e4c896d154b32eef7635f7b758ef51ac76

  libcrypto.so for vendor Gatekeeper HAL chain
    -> /system/lib64/vndk-29/libcrypto.so
       SHA256 91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

Classification:
  [RESOLUTION_CLOSED]
  [VNDK_29_REQUIRED]

################################################################################
# 03.7 SYSTEM GATEKEEPERD, KEYSTORE, AND VOLD CHAINS
################################################################################

03.7.1 gatekeeperd

  Binary:
    /system/bin/gatekeeperd

  Live identity:
    label  : u:object_r:gatekeeperd_exec:s0
    size   : 45336
    SHA256 : c4e828bda7bdf0a4fbcd329120e01c18cbc7ef0e3246d459db31aa763695e8e9

  Host readelf DT_NEEDED included:

    libbinder.so
    libgatekeeper.so
    libgsi.so
    liblog.so
    libhardware.so
    libbase.so
    libutils.so
    libcrypto.so
    libkeystore_aidl.so
    libkeystore_binder.so
    libhidlbase.so
    libhidltransport.so
    libhwbinder.so
    android.hardware.gatekeeper@1.0.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  Runtime maps prove gatekeeperd uses the system-side library chain, including:

    /system/lib64/libgatekeeper.so
    /system/lib64/libkeystore_aidl.so
    /system/lib64/libkeystore_binder.so
    /system/lib64/libkeystore_parcelables.so
    /system/lib64/libbinder.so
    /system/lib64/libhidlbase.so
    /system/lib64/libhidltransport.so
    /system/lib64/libhwbinder.so
    /system/lib64/libkeymaster4support.so
    /system/lib64/libcrypto.so via DT_NEEDED path

Classification:
  [SYSTEM_CREDENTIAL_SERVICE]
  [SYSTEM_CRYPTO_DEPENDENT]
  [PIN_CREDENTIAL_CRITICAL]

03.7.2 keystore

  Binary:
    /system/bin/keystore

  Live identity:
    label  : u:object_r:keystore_exec:s0
    size   : 477632
    SHA256 : 1ecbe5b77ff54515fb525d7837df8b243a1d23b1b3e6afef06bb4e893354cf43

  Note on host extraction:
    A host resolver previously found /super_unpacked/system/bin/keystore with a
    different size/hash. That path is treated as an extraction-layout artifact unless
    later reconciled. Live device path and hash above are authoritative for this
    section.

  Host readelf for a keystore binary showed dependencies including:

    android.hardware.confirmationui@1.0.so
    android.hardware.keymaster@3.0.so
    android.hardware.keymaster@4.0.so
    android.hardware.keymaster@4.1.so
    libbase.so
    libbinder.so
    libcrypto.so
    libcutils.so
    libhardware.so
    libhidlbase.so
    libkeymaster4support.so
    libkeymaster4_1support.so
    libkeymaster_messages.so
    libkeymaster_portable.so
    libkeystore-attestation-application-id.so
    libkeystore_aidl.so
    libkeystore_binder.so
    libkeystore_parcelables.so
    libprotobuf-cpp-lite.so
    libselinux.so
    libservices.so
    libsoftkeymasterdevice.so
    libutils.so
    libstatslog.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  Live maps prove system keystore maps:

    /system/lib64/libsoftkeymasterdevice.so
    /system/lib64/libkeymaster_portable.so
    /system/lib64/libkeymaster_messages.so
    /system/lib64/libkeymaster4support.so
    /system/lib64/libkeystore_aidl.so
    /system/lib64/libkeystore_binder.so
    /system/lib64/libkeystore_parcelables.so
    /system/lib64/libbinder.so
    /system/lib64/libselinux.so
    common system HIDL/Binder/support libraries

Classification:
  [SYSTEM_KEYSTORE]
  [SYSTEM_SOFTKEYMASTER_PRESENT]
  [AUTH_BOUND_KEY_CRITICAL]
  [PIN_CREDENTIAL_CRITICAL]

03.7.3 vold

  Binary:
    /system/bin/vold

  Live identity:
    label  : u:object_r:vold_exec:s0
    size   : 901584
    SHA256 : 39b04e7680f3fbf59d3eaf195dfa99d45eb1120d798b95b5140905a658da7b4e

  Host readelf DT_NEEDED included:

    android.hardware.keymaster@3.0.so
    android.hardware.keymaster@4.0.so
    android.hardware.boot@1.0.so
    libbase.so
    libbinder.so
    libcrypto.so
    libcrypto_utils.so
    libcutils.so
    libdiskconfig.so
    libext4_utils.so
    libf2fs_sparseblock.so
    libfscrypt.so
    libhardware.so
    libhardware_legacy.so
    libhidlbase.so
    libhwbinder.so
    libkeymaster4support.so
    libkeyutils.so
    liblog.so
    liblogwrap.so
    libselinux.so
    libsysutils.so
    libutils.so
    android.hardware.health.storage@1.0.so
    libhidltransport.so
    libc++.so
    libc.so
    libm.so
    libdl.so

  Critical live-mapped FBE support libraries:

    /system/lib64/libfscrypt.so
    /system/lib64/libkeyutils.so
    /system/lib64/libcrypto_utils.so
    /system/lib64/libkeymaster4support.so
    /system/lib64/liblogwrap.so
    /system/lib64/libselinux.so
    /system/lib64/libbinder.so
    /system/lib64/libhwbinder.so
    /system/lib64/libhidlbase.so
    /system/lib64/libhidltransport.so

Classification:
  [VOLD_FBE_OWNER]
  [FSCRYPT_V1_OWNER]
  [LEGACY_KEYCTL_REQUIRED]
  [CE_DE_UNLOCK_CRITICAL]

################################################################################
# 03.8 COMMON CRYPTO DEPENDENCY INVENTORY
################################################################################

03.8.1 Live common system dependency hashes

  /system/lib64/libbase.so
    size   : 83552
    SHA256 : cfd3b9dbc37bbf3dd21b30f0940c3e6fcfbae6b4d45a180efef91967cede39ef

  /system/lib64/libcutils.so
    size   : 77872
    SHA256 : ff2c1657014baf0205efc8a99f67fa5aa355e993fcc90e6edad6fcec2da2e25d

  /system/lib64/libutils.so
    size   : 117416
    SHA256 : 8eb7b70667df69684a4a17b511444805e0b8452fbd9fef09c6ea6e55ea860978

  /system/lib64/libhardware.so
    size   : 15280
    SHA256 : 655b29af09b86f40ddc70637cad5e97be025ba102908c50f001790b713371b6a

  /system/lib64/libhidlbase.so
    size   : 692152
    SHA256 : 4ca3742fb2a8b576af380c4ebc8c4a5e92010890351f602e7869d0dca4a183b0

  /system/lib64/libhidltransport.so
    size   : 14896
    SHA256 : 56ad32b30e6fbf6eef34d7ad1d8dd63f8538ffef3e84af49db834643afae6014

  /system/lib64/libhwbinder.so
    size   : 14896
    SHA256 : 0dd38ca10f7d0a00c1ebdf2148f12c1895e58651c6f5206b4a39f6df04cb50fb

  /system/lib64/libc++.so
    size   : 845928
    SHA256 : e3cdb7e15bb2b172d3dfada9ce5d171718ebe2ae10253cba5ac4d4ac71a21860

  /system/lib64/liblog.so
    size   : 92232
    SHA256 : 224e5d4179db0b4bb5681d15076f89e59545570e9c10050cafa06e736be7db55

  /system/lib64/libkeyutils.so
    size   : 14920
    SHA256 : a766e449e55b0f8229891fc241224789ffbc673bcb45f55c799dba1ab6f397b2

  /system/lib64/liblogwrap.so
    size   : 19800
    SHA256 : c712e8ae33d0acb62bdb2f652ebc57c47e602d048b536e3549bf2a51a990edd2

  /system/lib64/libcrypto_utils.so
    size   : 15056
    SHA256 : 4cfd0641a4f0e47819721e61ee0e669cb652e3de1a1fbe03d40aa91191afc6d0

  /system/lib64/libkeymaster4support.so
    size   : 165776
    SHA256 : 62615e23c17faed12087b305a7a90db39eb4fbc75ddcfb8a5466c5798007628e

  /system/lib64/libkeymaster4_1support.so
    state  : absent in live inventory

  /system/lib64/libkeymaster_messages.so
    size   : 101240
    SHA256 : 518ca208ef2fbf87beb3a9d33df5042782aa8659a6a9ab0407bf2f941d850693

  /system/lib64/libkeymaster_portable.so
    size   : 330536
    SHA256 : fa2f7614215593c3bf011be4c6bfbcf01e9699683f277276515670cf18b925e1

  /system/lib64/libsoftkeymasterdevice.so
    size   : 207808
    SHA256 : 168af4cdb3eeb40e5c9bf01d8a22d89ed7c7e9411825b9b88f642995bba18f60

  /system/lib64/libkeystore_aidl.so
    size   : 216816
    SHA256 : 1a6094e92a9360e3cf078b08c009a662f05ddd70a8c8dbaa4f71d0f3ef876fcf

  /system/lib64/libkeystore_binder.so
    size   : 119168
    SHA256 : 8070beb2e52e1d39ed2c4cf3b155df847e61826446a62c4eba7af015e2e495f8

  /system/lib64/libkeystore_parcelables.so
    size   : 54840
    SHA256 : ebf9e681827c827d0a74a8fac5cf5a51444f178fdeca4576525db0d897b3dcd8

  /system/lib64/libkeystore-engine.so
    size   : 76904
    SHA256 : f4853eac98ffbbb6cf1df158d0a57b60098ee66a10ea4dcd749d3c3080202b9d

Classification:
  [COMMON_DEPENDENCY_INVENTORY_CLOSED]
  [SYSTEM_SIDE_SUPPORT_SET]

03.8.2 VNDK-SP runtime observation

  Keymaster and Gatekeeper HAL service processes map several VNDK-SP-29 common
  support libraries at runtime, including:

    /system/lib64/vndk-sp-29/libutils.so
    /system/lib64/vndk-sp-29/libcutils.so
    /system/lib64/vndk-sp-29/libhidlbase.so
    /system/lib64/vndk-sp-29/libhidltransport.so
    /system/lib64/vndk-sp-29/libhwbinder.so
    /system/lib64/vndk-sp-29/libhardware.so
    /system/lib64/vndk-sp-29/libc++.so
    /system/lib64/vndk-sp-29/libbinderthreadstate.so
    /system/lib64/vndk-sp-29/libbase.so

This proves the normal OS path is not a flat /system/lib64 lookup. The HAL services
operate inside a vendor/VNDK/VNDK-SP namespace that must be preserved in AOSP 11.

Classification:
  [VNDK_SP_29_COMMON_DEPENDENT]
  [NAMESPACE_CRITICAL]

################################################################################
# 03.9 BIONIC AND LINKER EXCEPTION SET
################################################################################

03.9.1 Linker paths

  /system/bin/linker
    symlink -> /apex/com.android.runtime/bin/linker
    SHA256 : 3957a0241fbe9cce885705fd2691a34556ef32ed4871db5db750f551fe217b51

  /system/bin/linker64
    symlink -> /apex/com.android.runtime/bin/linker64
    SHA256 : 6aa0c8ec4568ce149acf755e97646fc5013c6c869d344a49ee8ec64262d55d67

  /apex/com.android.runtime/bin/linker
    size   : 1158240
    SHA256 : 3957a0241fbe9cce885705fd2691a34556ef32ed4871db5db750f551fe217b51

  /apex/com.android.runtime/bin/linker64
    size   : 1602480
    SHA256 : 6aa0c8ec4568ce149acf755e97646fc5013c6c869d344a49ee8ec64262d55d67

Classification:
  [BIONIC_EXCEPTION]
  [APEX_RUNTIME]
  [DO_NOT_ISOLATE]

03.9.2 bionic libc/libm/libdl paths

  32-bit:

    /system/lib/libc.so -> /apex/com.android.runtime/lib/bionic/libc.so
      SHA256 2c262ec168012c4529ebe1d3eac554ab65ed64060b58608d39a2dc88b225b3c8

    /system/lib/libm.so -> /apex/com.android.runtime/lib/bionic/libm.so
      SHA256 7403fc103c08191a912fd5494b41387891c2ad463a78bcde011b15ba77e6994a

    /system/lib/libdl.so -> /apex/com.android.runtime/lib/bionic/libdl.so
      SHA256 1bae8aa97d619e8c3965457b588f4a0495f913b0ec618f7a3346b54fb57a1578

  64-bit:

    /system/lib64/libc.so -> /apex/com.android.runtime/lib64/bionic/libc.so
      SHA256 a634a7b25c64d4d51b4a7ce20a6fe523f66ec53cea1e8c4971d3e47175121ee9

    /system/lib64/libm.so -> /apex/com.android.runtime/lib64/bionic/libm.so
      SHA256 78aa48ca8b154f4f43047a687c1259e4ed7e5f6e2bb686beff60c6978c428a88

    /system/lib64/libdl.so -> /apex/com.android.runtime/lib64/bionic/libdl.so
      SHA256 d49dba9e449f25ebfed4edfa2af6b811ba36bd1fe6683475cd8e5821fefe5acd

Classification:
  [BIONIC_EXCEPTION]
  [APEX_RUNTIME]
  [DO_NOT_ISOLATE]

03.9.3 Runtime map confirmation

  Runtime maps for the following processes show APEX bionic and linker64:

    android.hardware.keymaster@4.0-service
    android.hardware.gatekeeper@1.0-service
    gatekeeperd
    keystore
    vold

  Mapped runtime objects:

    /apex/com.android.runtime/lib64/bionic/libc.so
    /apex/com.android.runtime/lib64/bionic/libm.so
    /apex/com.android.runtime/lib64/bionic/libdl.so
    /apex/com.android.runtime/bin/linker64

Conclusion:
  libc.so, libm.so, libdl.so, linker, and linker64 are intentionally not resolved
  by the host dependency resolver search set. They are not missing dependencies.
  They are Android runtime/APEX bionic objects and must not be copied into or patched
  into a private crypto namespace.

Classification:
  [BIONIC_EXCEPTION_CLOSED]
  [NO_PATCH]
  [NO_PRIVATE_COPY]

################################################################################
# 03.10 STRONGSWAN / EPDG / WFC CRYPTO ISLAND
################################################################################

03.10.1 StrongSwan/ePDG role

The StrongSwan/ePDG/WFC library family is a vendor crypto island used by the radio,
Wi-Fi Calling, IMS, and IPsec plane. It is not the Android Keymaster/Gatekeeper/Vold
crypto path.

Classification:
  [VENDOR_SS_CRYPTO_ISLAND]
  [RADIO_WFC_CRYPTO]
  [DO_NOT_COLLIDE_WITH_ANDROID_LIBCRYPTO]

03.10.2 Primary files and hashes

  /vendor/bin/charon
    size   : 14240
    SHA256 : f5194e62ae17a4e1f308f37095833706f1483ac0607792a420adcb791d905afe

  /vendor/lib64/libcharon-ss.so
    size   : 739544
    SHA256 : f63c724129763d0a4aa6ef84ed0e231b3f2c483c826b7b6d3db352e01d098915

  /vendor/lib64/libcrypto-ss.so
    size   : 1787984
    SHA256 : 33e2fd0f928385c31639e5116494af66ab712137266b790c59a787a64c515465

  /vendor/lib64/libssl-ss.so
    size   : 447936
    SHA256 : e42a74843a7510ec4939745efc375c350516c84cb3807474304cbb2bf4bde2a0

  /vendor/lib64/libcurl-ss.so
    size   : 280312
    SHA256 : 30313b79ad1b5ae627db7620f33ff60d3c2984f4ee76d03306149831504cc1cb

  /vendor/lib64/libstrongswan.so
    size   : 562944
    SHA256 : ffa8647c5415ff5a6bbcb3a934236be4ab0db51f1dd22a3ee3d919f249f7879c

  /vendor/lib64/libhydra.so
    SHA256 : ed5a1c9fcf15783f3fe68e9b625b6a42bc03bd8a5343595d904eda2457ea5d88

  /vendor/lib64/libsimaka.so
    SHA256 : 7a517868aa9ff888f908de3083cf3264d60d7aa1818ad07a254630487ba469d7

Classification:
  [FILE_AUTHORITY]
  [VENDOR_SS_CRYPTO_ISLAND]

03.10.3 StrongSwan/ePDG dependency chain

  /vendor/bin/charon requires:

    libstrongswan.so
    libhydra.so
    libcharon-ss.so
    libc.so
    libm.so

  /vendor/lib64/libcharon-ss.so requires:

    libcutils.so
    libsimaka.so
    libstrongswan.so
    libhydra.so
    liblog.so
    libc.so
    libm.so

  /vendor/lib64/libstrongswan.so requires:

    libssl-ss.so
    libcurl-ss.so
    libcrypto-ss.so
    libcutils.so
    libdl.so
    libc.so
    libm.so

  /vendor/lib64/libssl-ss.so requires:

    libcrypto-ss.so
    libcutils.so
    liblog.so
    libc.so
    libm.so

  /vendor/lib64/libcurl-ss.so requires:

    libssl-ss.so
    libcrypto-ss.so
    libz.so
    libm.so
    liblog.so
    libc.so

  /vendor/lib64/libcrypto-ss.so requires:

    libcutils.so
    liblog.so
    libdl.so
    libc.so
    libm.so

Classification:
  [DT_NEEDED_CLOSED]
  [VENDOR_SS_CRYPTO_ISLAND]

03.10.4 StrongSwan/ePDG resolution result

  libstrongswan.so
    -> /vendor/lib64/libstrongswan.so
       SHA256 ffa8647c5415ff5a6bbcb3a934236be4ab0db51f1dd22a3ee3d919f249f7879c

  libhydra.so
    -> /vendor/lib64/libhydra.so
       SHA256 ed5a1c9fcf15783f3fe68e9b625b6a42bc03bd8a5343595d904eda2457ea5d88

  libcharon-ss.so
    -> /vendor/lib64/libcharon-ss.so
       SHA256 f63c724129763d0a4aa6ef84ed0e231b3f2c483c826b7b6d3db352e01d098915

  libssl-ss.so
    -> /vendor/lib64/libssl-ss.so
       SHA256 e42a74843a7510ec4939745efc375c350516c84cb3807474304cbb2bf4bde2a0

  libcurl-ss.so
    -> /vendor/lib64/libcurl-ss.so
       SHA256 30313b79ad1b5ae627db7620f33ff60d3c2984f4ee76d03306149831504cc1cb

  libcrypto-ss.so
    -> /vendor/lib64/libcrypto-ss.so
       SHA256 33e2fd0f928385c31639e5116494af66ab712137266b790c59a787a64c515465

  libsimaka.so
    -> /vendor/lib64/libsimaka.so
       SHA256 7a517868aa9ff888f908de3083cf3264d60d7aa1818ad07a254630487ba469d7

  libz.so for libcurl-ss.so
    -> /system/lib64/libz.so
       SHA256 3ee13916681349aafbb65e30b6ea34a50cf93c1b198377663d72d91350688fe9

Conclusion:
  StrongSwan/ePDG does not participate in the Keymaster/Gatekeeper/Vold FBE path.
  It is a separate vendor radio/WFC crypto island and must be preserved separately
  for IMS/WFC/ePDG completeness.

Classification:
  [RESOLUTION_CLOSED]
  [VENDOR_SS_CRYPTO_ISLAND]
  [RADIO_WFC_DEPENDENCY]

################################################################################
# 03.11 OPENSSL / BORINGSSL / LIBCRYPTO IDENTIFICATION
################################################################################

03.11.1 System libcrypto

  /system/lib64/libcrypto.so
    size   : 1264696
    SHA256 : ae1073ac09149912b48fff82336ca313d71ab2e74339b91fcb8aae204b015c29

Role:
  System/framework crypto library for system processes.

Classification:
  [SYSTEM_CRYPTO]

03.11.2 VNDK-29 libcrypto

  /system/lib64/vndk-29/libcrypto.so
    size   : 1149976
    SHA256 : 91254cfc5b1b3a93dd43b1d65674293da1b13309c1bb8d169431ab34d51f95ea

Role:
  Vendor/HAL-facing crypto library used by retained Android 10 vendor crypto HAL
  chains, including Keymaster and Gatekeeper.

Classification:
  [VNDK_29_CRYPTO]
  [KEYMASTER_GATEKEEPER_CRITICAL]

03.11.3 Vendor StrongSwan libcrypto

  /vendor/lib64/libcrypto-ss.so
    size   : 1787984
    SHA256 : 33e2fd0f928385c31639e5116494af66ab712137266b790c59a787a64c515465

Role:
  Vendor StrongSwan/ePDG/WFC crypto library. It is intentionally name-separated
  from Android libcrypto.so by the -ss suffix.

Classification:
  [VENDOR_SS_CRYPTO]
  [RADIO_WFC_CRYPTO]

03.11.4 Collision rule

  Do not replace all libcrypto.so occurrences with one file.
  Do not make StrongSwan use Android libcrypto.so.
  Do not make Keymaster/Gatekeeper use vendor libcrypto-ss.so.
  Do not make Keymaster/Gatekeeper resolve generic libcrypto.so to the wrong
  AOSP 11 system-side crypto library.

Classification:
  [DO_NOT_COLLIDE]
  [NAMESPACE_CRITICAL]

################################################################################
# 03.12 ODM / VENDOR MODIFIED LIBRARY IDENTIFICATION
################################################################################

03.12.1 Vendor-owned crypto/security implementation libraries

  Confirmed vendor-owned or vendor-carried crypto/security implementation files:

    /vendor/bin/hw/android.hardware.keymaster@4.0-service
    /vendor/lib64/libkeymaster4.so
    /vendor/lib64/libkeymaster4support.so

    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
    /vendor/lib64/hw/gatekeeper.default.so
    /vendor/lib64/hw/libSoftGatekeeper.so
    /vendor/lib/hw/android.hardware.gatekeeper@1.0-impl.so
    /vendor/lib/hw/gatekeeper.default.so
    /vendor/lib/hw/libSoftGatekeeper.so

    /vendor/bin/charon
    /vendor/lib64/libcharon-ss.so
    /vendor/lib64/libcrypto-ss.so
    /vendor/lib64/libssl-ss.so
    /vendor/lib64/libcurl-ss.so
    /vendor/lib64/libstrongswan.so
    /vendor/lib64/libhydra.so
    /vendor/lib64/libsimaka.so

Classification:
  [VENDOR_OWNED]
  [CARRY_FOR_FUNCTION]

03.12.2 Modified/common dependency warning inherited from prior TWRP work

  Prior recovery work identified a recovery-only crypto isolation problem involving
  modified or namespace-sensitive common libraries such as:

    libcrypto.so
    libhidlbase.so
    libutils.so

  That finding is preserved as a TWRP/recovery compatibility finding. It does not
  automatically create a normal-ROM patchelf target. The normal OS evidence shows
  Keymaster/Gatekeeper resolving through Android VNDK/VNDK-SP/system/APEX runtime
  namespaces, not through the TWRP /crypto_isolation bubble.

Classification:
  [TWRP_ONLY]
  [NORMAL_ROM_DO_NOT_IMPORT]
  [GHDIRA_OPTIONAL_FOR_ATTRIBUTION]

03.12.3 Ghidra status

  Ghidra/deep reverse engineering remains useful for vulnerability attribution and
  semantic divergence analysis of ODM/vendor libraries. It is not required for the
  functional ROM build decision because runtime maps, DT_NEEDED, symbol/string
  evidence, and dependency resolution already close the functional chain.

  Recommended optional Ghidra targets:

    /vendor/lib64/libkeymaster4.so
    /vendor/lib64/libkeymaster4support.so
    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
    /vendor/lib64/hw/libSoftGatekeeper.so
    /vendor/lib64/libcrypto-ss.so
    /vendor/lib64/libssl-ss.so
    /vendor/lib64/libcurl-ss.so
    /vendor/lib64/libcharon-ss.so
    /vendor/lib64/libstrongswan.so
    /vendor/lib64/libhydra.so
    /vendor/lib64/libsimaka.so

Classification:
  [OPTIONAL_DEEP_RE]
  [NOT_ROM_BLOCKING]

################################################################################
# 03.13 NAMESPACE ISOLATION REQUIREMENTS
################################################################################

03.13.1 Normal OS namespace model

  The normal OS runtime uses:

    - system namespace for framework/system processes
    - vendor namespace for vendor services
    - VNDK-29 libraries for retained Android 10 vendor blobs
    - VNDK-SP-29 common support libraries for vendor/HAL processes
    - APEX runtime/bionic for linker/libc/libm/libdl
    - vendor -ss libraries for StrongSwan/ePDG crypto

Classification:
  [NORMAL_ANDROID_NAMESPACE]
  [VNDK_29_REQUIRED]
  [VNDK_SP_29_REQUIRED]
  [APEX_RUNTIME_REQUIRED]

03.13.2 AOSP 11 build configuration consequence

  Required build policy:

    BOARD_VNDK_VERSION := current
    PRODUCT_EXTRA_VNDK_VERSIONS := 29
    PRODUCT_SHIPPING_API_LEVEL := 29

  The platform may be AOSP 11, but retained vendor blobs are Android 10/API29 blobs
  and must see VNDK-29-compatible dependencies.

Classification:
  [AOSP11_ROM_RULE]
  [API29_VENDOR_BLOBS]
  [VNDK_29_COMPATIBILITY_REQUIRED]

03.13.3 No normal-ROM crypto isolation bubble

  Normal AOSP 11 ROM default:

    /crypto_isolation : not used
    blanket patchelf  : not used
    LD_LIBRARY_PATH   : not used as crypto fix
    bionic copy       : forbidden

  The TWRP crypto isolation bubble remains recovery-only.

Classification:
  [TWRP_ONLY]
  [NORMAL_ROM_DO_NOT_IMPORT]

################################################################################
# 03.14 PATCH ELF / RPATH DECISION TABLE
################################################################################

03.14.1 Current normal-ROM patchelf target list

  Current AOSP 11 normal-ROM patchelf list:

    EMPTY

Reason:
  - Keymaster and Gatekeeper have no proven normal-ROM RPATH/RUNPATH requirement.
  - Keymaster and Gatekeeper resolve their critical generic libcrypto.so through
    VNDK-29 in the observed normal OS model.
  - Gatekeeper module discovery works through HIDL_FETCH_IGatekeeper and
    hw_get_module_by_class with gatekeeper.default.so -> libSoftGatekeeper.so.
  - StrongSwan/ePDG resolves through vendor -ss library names and does not need
    generic libcrypto.so collision handling.
  - bionic is provided by APEX/runtime and must not be patched into private paths.

Classification:
  [PATCH_ELF_EMPTY_UNTIL_PROVEN]
  [NO_NORMAL_ROM_PATCH_REQUIRED_CURRENTLY]

03.14.2 Exception rule

  A future ROM build may add a patchelf/RPATH target only if first-boot logs prove
  one of the following:

    CANNOT LINK EXECUTABLE
    library not found
    namespace access denied
    wrong ELF class
    wrong libcrypto selected
    HIDL_FETCH module cannot locate gatekeeper.default.so
    Keymaster service aborts before registration because libkeymaster4 chain fails
    vold fails CE/DE setup because keymaster/gatekeeper dependency resolution failed

  Any such exception must include:

    failing process
    exact missing library
    exact namespace denial or linker error
    exact source file path
    exact proposed patch
    post-patch /proc/<pid>/maps proof

Classification:
  [STRICT_EXCEPTION_ONLY]

################################################################################
# 03.15 FBE IMPLEMENTATION — FSCRYPT V1 AND LEGACY KEYCTL
################################################################################

03.15.1 FBE implementation summary

  Encryption type       : File-Based Encryption
  ro.crypto.type        : file
  ro.crypto.state       : encrypted
  contents mode         : aes-256-xts
  filenames mode        : aes-256-cts
  kernel family         : 4.9.190+
  fscrypt policy        : v1 / legacy policy path
  key install behavior  : legacy keyctl/add_key fallback expected on kernel 4.9

Classification:
  [FBE]
  [FSCRYPT_V1]
  [LEGACY_KEYCTL]

03.15.2 vold/FBE library evidence

  vold maps:

    /system/lib64/libfscrypt.so
    /system/lib64/libkeyutils.so
    /system/lib64/libcrypto_utils.so
    /system/lib64/libkeymaster4support.so
    /system/lib64/liblogwrap.so

  Important live identities:

    /system/lib64/libfscrypt.so
      SHA256 : 322938e09f58d90d42707330d30fb27203b071c25e4a1549fb390cc85b66504d

    /system/lib64/libkeyutils.so
      SHA256 : a766e449e55b0f8229891fc241224789ffbc673bcb45f55c799dba1ab6f397b2

    /system/lib64/libcrypto_utils.so
      SHA256 : 4cfd0641a4f0e47819721e61ee0e669cb652e3de1a1fbe03d40aa91191afc6d0

    /system/bin/vold
      SHA256 : 39b04e7680f3fbf59d3eaf195dfa99d45eb1120d798b95b5140905a658da7b4e

Classification:
  [VOLD_FBE_OWNER]
  [FSCRYPT_V1_OWNER]
  [LEGACY_KEYCTL_REQUIRED]

03.15.3 md_udc rule

  The clean ROM must mount /metadata from md_udc, not the generic metadata partition.

  Required active source:
    /dev/block/platform/bootdevice/by-name/md_udc

  Incorrect trap source:
    /dev/block/platform/bootdevice/by-name/metadata

Classification:
  [ROM_BUILD_CRITICAL]
  [MD_UDC_REQUIRED]

################################################################################
# 03.16 DXCC / TEE / RPMB BOUNDARY
################################################################################

03.16.1 Hardware substrate facts

  Hardware/security substrate exists on the platform and is tracked in the hardware
  database:

    DXCC / silicon key infrastructure
    RPMB
    secure partitions
    TEE/GZ/secure execution partitions
    secure boot/AVB-related partitions
    TRNG/security-related SoC blocks

03.16.2 Android userspace crypto path facts

  The current Android userspace crypto evidence shows:

    Keymaster runtime path is pure-soft.
    Gatekeeper runtime path is SoftGatekeeper.
    Keymaster maps no TEE client libraries in the captured runtime evidence.
    Gatekeeper maps no TEE client libraries in the captured runtime evidence.
    FBE/vold path uses Android fscrypt/keymaster/gatekeeper software chains.
    RPMB is not proven in the FBE key derivation path.
    DXCC is not proven as an Android userspace Keymaster/Gatekeeper backend.

Classification:
  [NO_TEE_RUNTIME_PROOF]
  [NO_RPMB_RUNTIME_PROOF]
  [NO_DXCC_USERSPACE_CRYPTO_PROOF]
  [HARDWARE_SUBSTRATE_SEPARATED_FROM_ANDROID_CRYPTO_PATH]

03.16.3 Boundary rule

  Do not write that the device lacks DXCC/TEE/RPMB. The hardware substrate exists.
  The correct statement is that the captured Android Keymaster/Gatekeeper/FBE
  userspace path does not use those components as its proven runtime backend.

Classification:
  [BOUNDARY_CORRECTED]

################################################################################
# 03.17 TWRP PROFILE — CLOSED RECOVERY-SPECIFIC REFERENCE
################################################################################

03.17.1 TWRP status

  TWRP is fully decrypting and operational. No additional TWRP work is required for
  this section.

Classification:
  [TWRP_CLOSED]
  [FULLY_DECRYPTING]

03.17.2 Why TWRP differed from normal OS

  Recovery does not reproduce the complete normal Android OS linker/VNDK/APEX/vendor
  namespace behavior. Because the Keymaster/Gatekeeper stack uses stripped binaries,
  generic sonames, passthrough adapters, and runtime-loaded modules, recovery required
  special handling to make those components resolve correctly.

  TWRP-specific mechanisms previously used:

    /crypto_isolation bubble
    RPATH correction inside recovery context
    bind/path handling for hw module discovery
    pure AOSP 5-column fstab format
    fscrypt v1 configuration

  These are recovery-specific. They are not the default design for the normal AOSP 11
  ROM.

Classification:
  [TWRP_ONLY]
  [NORMAL_ROM_DO_NOT_IMPORT]

################################################################################
# 03.18 CLEAN AOSP 11 ROM REQUIREMENTS
################################################################################

03.18.1 Required preserved components

  Keymaster preserve list:

    /vendor/bin/hw/android.hardware.keymaster@4.0-service
    /vendor/lib64/libkeymaster4.so
    /vendor/lib64/libkeymaster4support.so
    /system/lib64/vndk-29/android.hardware.keymaster@3.0.so
    /system/lib64/vndk-29/android.hardware.keymaster@4.0.so
    /system/lib64/vndk-29/libpuresoftkeymasterdevice.so
    /system/lib64/vndk-29/libkeymaster_portable.so
    /system/lib64/vndk-29/libkeymaster_messages.so
    /system/lib64/vndk-29/libcrypto.so
    VNDK-SP common support namespace
    APEX bionic/linker runtime

  Gatekeeper preserve list:

    /vendor/bin/hw/android.hardware.gatekeeper@1.0-service
    /vendor/lib64/hw/android.hardware.gatekeeper@1.0-impl.so
    /vendor/lib64/hw/gatekeeper.default.so -> libSoftGatekeeper.so
    /vendor/lib64/hw/libSoftGatekeeper.so
    /vendor/lib/hw/android.hardware.gatekeeper@1.0-impl.so
    /vendor/lib/hw/gatekeeper.default.so -> libSoftGatekeeper.so
    /vendor/lib/hw/libSoftGatekeeper.so
    /system/lib64/vndk-29/android.hardware.gatekeeper@1.0.so
    /system/lib64/vndk-29/libgatekeeper.so
    /system/lib64/vndk-29/libcrypto.so
    VNDK-SP common support namespace
    APEX bionic/linker runtime

  Vold/FBE preserve list:

    /system/bin/vold
    /system/lib64/libfscrypt.so
    /system/lib64/libkeyutils.so
    /system/lib64/libcrypto_utils.so
    /system/lib64/libkeymaster4support.so
    /system/lib64/liblogwrap.so
    correct /metadata mount from md_udc
    correct /data fstab fileencryption=aes-256-xts
    ro.crypto.volume.filenames_mode=aes-256-cts

  Keystore/Gatekeeperd preserve list:

    /system/bin/keystore
    /system/bin/gatekeeperd
    system libkeystore_* support libraries
    system libsoftkeymasterdevice.so
    system libkeymaster_portable.so
    system libkeymaster_messages.so
    system libgatekeeper.so
    Binder/HIDL support libraries

  StrongSwan/ePDG preserve list:

    /vendor/bin/charon
    /vendor/bin/epdg_wod
    /vendor/bin/ipsec_mon
    /vendor/bin/wfca
    /vendor/lib64/libcharon-ss.so
    /vendor/lib64/libcrypto-ss.so
    /vendor/lib64/libssl-ss.so
    /vendor/lib64/libcurl-ss.so
    /vendor/lib64/libstrongswan.so
    /vendor/lib64/libhydra.so
    /vendor/lib64/libsimaka.so

Classification:
  [CARRY_FOR_FUNCTION]
  [ROM_BUILD_REQUIRED]

03.18.2 Required exclusions / do-not-copy rules

  Do not copy APEX bionic into a private namespace:

    libc.so
    libm.so
    libdl.so
    linker
    linker64

  Do not import TWRP /crypto_isolation into normal ROM by default.

  Do not replace VNDK-29 libcrypto with AOSP 11 system libcrypto for vendor HAL
  processes.

  Do not collapse vendor libcrypto-ss.so into Android libcrypto.so.

  Do not remove gatekeeper.default.so symlink.

  Do not remove the 32-bit Gatekeeper module set unless a separate full 32-bit
  dependency audit proves it is unused.

Classification:
  [DO_NOT_COPY]
  [DO_NOT_COLLAPSE]
  [DO_NOT_STRIP]
  [BIONIC_EXCEPTION]

03.18.3 Clean ROM validation gates

  First boot crypto gate:

    keymaster service starts
    keymaster registers android.hardware.keymaster@4.0/default
    keymaster maps vendor libkeymaster4.so
    keymaster maps VNDK-29 libpuresoftkeymasterdevice.so
    keymaster maps VNDK-29 libcrypto.so
    gatekeeper service starts
    gatekeeper registers android.hardware.gatekeeper@1.0/default
    gatekeeper impl loads gatekeeper.default.so
    gatekeeper.default.so resolves to libSoftGatekeeper.so
    vold starts
    vold maps libfscrypt.so and libkeyutils.so
    /metadata mounts from md_udc
    /data mounts encrypted file-based
    user 0 DE storage available
    user 0 CE storage available after unlock path

  PIN credential gate:

    set PIN
    reboot
    no bootloop
    LockSettingsService survives
    gatekeeperd survives
    keystore survives
    keymaster survives
    vold survives
    CE unlock succeeds
    /data/system_ce/0 becomes available
    no synthetic password crash
    no fscrypt key-install failure
    no keymaster/gatekeeper auth-token failure
    no linker namespace denial

  Radio/WFC crypto gate:

    charon dependency chain resolves
    libstrongswan resolves vendor -ss libraries
    libssl-ss resolves libcrypto-ss
    libcurl-ss resolves libssl-ss/libcrypto-ss/libz
    no collision with Android libcrypto.so
    IMS/WFC/ePDG daemons do not crash from missing -ss libraries

Classification:
  [SECTION18_GATE]
  [CLEAN_ROM_BLOCKING_TEST]

################################################################################
# 03.19 FAILURE CLASSES AND DIAGNOSTIC STRINGS
################################################################################

03.19.1 Linker failure strings

  If clean ROM boot fails, search logs for:

    CANNOT LINK EXECUTABLE
    library not found
    namespace
    is not accessible for the namespace
    wrong ELF class
    dlopen failed
    HIDL_FETCH_IGatekeeper
    Unable to open GateKeeper HAL
    Unable to close GateKeeper HAL
    Could not register service for Keymaster 4.0

03.19.2 FBE/credential failure strings

  Search logs for:

    vold
    fscrypt
    keymaster
    gatekeeper
    gatekeeperd
    keystore
    synthetic password
    LockSettingsService
    spblob
    credential
    secdiscardable
    add_key
    keyctl
    Failed to install key
    Failed to read key
    user 0 CE

Classification:
  [DIAGNOSTIC_MAP]

################################################################################
# 03.20 FINAL RECONCILIATION LEDGER
################################################################################

03.20.1 Original database items carried forward

  Carried forward and corrected:

    - Three crypto families exist.
    - Keymaster is pure-soft at runtime.
    - Gatekeeper is SoftGatekeeper at runtime.
    - FBE is file-based, aes-256-xts contents, aes-256-cts filenames.
    - Kernel 4.9 requires fscrypt v1 / legacy keyctl behavior.
    - md_udc is the active metadata mount target.
    - TWRP crypto isolation is TWRP-only.
    - StrongSwan/ePDG has a separate -ss crypto family.

03.20.2 Corrected model items

  Corrected:

    - Keymaster service executable is a binderized registration frontend, not the
      full implementation.
    - Keymaster has no separate keymaster.default.so hw module in the live checked
      paths.
    - Keymaster uses libkeymaster4.so adapter and VNDK-29 pure-soft implementation.
    - Gatekeeper service executable is a binderized frontend.
    - Gatekeeper uses HIDL_FETCH_IGatekeeper, hw_get_module_by_class, and
      gatekeeper.default.so -> libSoftGatekeeper.so.
    - bionic unresolved entries in host dependency resolver are expected APEX runtime
      objects, not missing crypto files.

03.20.3 Open leads intentionally retained

  Retained open leads:

    [OPEN_LEAD] Historical GSI PIN bootloop exact cause
      Reason: no retained logs; inference only.
      Action: clean ROM must run PIN credential reboot gate.

    [OPEN_LEAD] Optional Ghidra semantic divergence of ODM/vendor crypto libraries
      Reason: not required for functional chain; useful for vulnerability attribution.
      Action: run only if Section 16/security attribution needs semantic proof.

    [OPEN_LEAD] Host duplicate keystore extraction path mismatch
      Reason: live /system/bin/keystore hash differs from one host-resolved extraction
      path; live device is authority for this section.
      Action: reconcile during Section 10 full blob inventory if required.

03.20.4 Closed items

  Closed:

    OpenSSL/BoringSSL/libcrypto family identification
    Keymaster runtime chain
    Gatekeeper runtime chain
    StrongSwan/ePDG crypto island
    Bionic exception set
    FBE key path
    md_udc metadata requirement
    normal-ROM patchelf default state
    TWRP/non-TWRP boundary
    DXCC/TEE/RPMB versus Android userspace crypto boundary

################################################################################
# 03.21 FINAL ROM DECISION SUMMARY
################################################################################

  The clean AOSP 11 ROM must preserve Android's normal linker namespace model with
  VNDK-29 and VNDK-SP-29 compatibility for retained Android 10 vendor blobs.

  The ROM must not import the TWRP /crypto_isolation architecture by default.

  The ROM must not patch Keymaster/Gatekeeper RPATHs unless a specific first-boot
  linker failure proves a required exception.

  The ROM must preserve the pure-soft Keymaster and SoftGatekeeper chains exactly
  enough for vold, keystore, gatekeeperd, LockSettingsService, and fscrypt to survive
  PIN enrollment and reboot.

  The ROM must preserve the vendor StrongSwan/ePDG -ss crypto island independently
  for radio/WFC/IMS behavior.

  The ROM must treat DXCC/TEE/RPMB as hardware substrate facts but must not claim
  Android Keymaster/Gatekeeper hardware backing without runtime proof.

Final classification:
  [SECTION03_COMPLETE]
  [AOSP11_CRYPTO_REQUIREMENTS_DEFINED]
  [NORMAL_ROM_NAMESPACE_MODEL_REQUIRED]
  [PATCH_ELF_EMPTY_UNTIL_PROVEN]
  [PIN_CREDENTIAL_GATE_REQUIRED]
  [READY_FOR_SECTION10_SECTION15_SECTION18_CONSUMPTION]

################################################################################
# 03.22 FINAL FILE CLOSURE
################################################################################

  Output filename:
    C17S_SOFTWARE_SECTION03_FINAL.md

  Final line count, byte count, and SHA256 are computed externally after file write.
  The SHA256 is not embedded here because embedding the digest would change the file.

################################################################################
# END OF SECTION 03
################################################################################
