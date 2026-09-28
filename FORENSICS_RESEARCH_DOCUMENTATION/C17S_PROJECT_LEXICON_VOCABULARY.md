# Introduction - the entire projetc is authored by me for advanced developers and beginners alike. #
# the objective of this document is project understanding, terminology, and vocabulary.............# 

# C17S / LOKMAT 5 MAX FORENSIC ONTOLOGY & MASTER PROJECT LEXICON

**Project Title:** LOKMAT APPLLP 5 MAX (C17S) Full Device Reverse Engineering, Security Assessment & AOSP 11 Purification  (PojectBlackBox)
**Lead Investigator / Author:** Albert Pittman (beright1976) be.rt.pittman76@gmail.com / berite1976@outlook.com 
**Document Class:** Universal Technical Vocabulary & Forensic Methodology Specification  
**Standard of Verification:** Byte-exact verification, deterministic witness separation, zero assumptions, and hex level evidence absolution.


## SECTION I: Core Silicon, Mask ROM & Hardware Root Identity

### 1. MT6762V/WB (Physical Silicon Identity)
* **Domain / Layer:** Layer 0 (Physical Silicon / Base Wafer).
* **Forensic Definition:** The physical 28nm octa-core ARM Cortex-A53 System-on-Chip (SoC) manufactured by MediaTek (4x A53 @ 2.0 GHz + 4x A53 @ 1.5 GHz, PowerVR GE8320 GPU) present on the physical C17S board (`WP_C17S_PIX_TFT_D4`).
* **Project Context & Operational Impact:** Immutable silicon register reads (BROM HW Code `0x0766`) prove that the physical die is MT6762V/WB (Helio P22 class). Low-level driver binding and device tree compilation targets must respect physical silicon constraints while providing backward ABI compatibility for software BSP blobs.

### 2. MT6765 (Software Platform Masquerade / BSP Substrate)
* **Domain / Layer:** Layer 1 / Layer 5 (MediaTek Board Support Package / Android Vendor HALs).
* **Forensic Definition:** The higher-clocked sibling silicon identity under which MediaTek compiled the vendor binary blobs, VNDK-29 libraries, and HIDL HALs for Android 10 (API 29).
* **Project Context & Operational Impact:** Represents a "Franken-SoC" implementation split: the stock firmware declares `TARGET_BOARD_PLATFORM := mt6765` and `ro.board.platform=mt6765` to inherit MediaTek turn-key BSP frameworks, while `ro.hardware=mt6762` and `ro.boot.hardware=mt6762` select the active kernel hardware paths.

### 3. BROM (Boot ROM / Mask ROM)
* **Domain / Layer:** Immutable Silicon ROM (`0x00000000`–`0x0001FFFF`, 128 KiB).
* **Forensic Definition:** The primary mask ROM hardcoded onto the silicon die during wafer fabrication, executed by the primary ARM core immediately upon release from power-on reset.
* **Project Context & Operational Impact:** Analyzed via `brom_dump.bin` (SHA-256: `102847bbbaf36683df46ba7d5114257fcf66b8fdf630134a0d9c82f0aac3842c`). Contains the initial reset vector (`0x000000: b 0x28`), SRAM stack setup (`0x00102738`), eMMC `mmcblk0boot0` header parsing (`EMMC_BOOT` / `BRLYT`), and preloader handoff logic.

### 4. BROM Handshake & USBDL Protocol
* **Domain / Layer:** BROM Inter-Process Communication & Acquisition Endpoint.
* **Forensic Definition:** A low-level serial handshake protocol operating over USB virtual COM port (VID/PID `0e8d:0003`) executing prior to preloader validation.
* **Project Context & Operational Impact:** Uses byte sequences (`0xa0`, `0x0a`, `0x50`, `0x05`) to break hardware execution loops, allowing raw physical acquisition (mtkclient / Kamakiri) and unauthenticated SRAM payload execution when secure boot checks are disabled.

### 5. Hardware Identifiers: ME_ID, SOC_ID, eMMC CID
* **Domain / Layer:** Immutable Silicon Serialization & Fusing Substrate.
* **Forensic Definition:**
  * **HW Code (`0x0766`) / HW Subcode (`0x8a00`) / HW Version (`0xca00`):** Exact silicon revision markers read from hardware registers during reset.
  * **ME_ID (MediaTek Engine ID):** 128-bit/256-bit cryptographically unique silicon identifier (`4B5CF18EF37D514A6256199E0BBD5E6A`).
  * **SOC_ID:** 256-bit unique hash identifier (`6044C8E6000D6C384827B28853120093E04F3C815E8A50E6D20D6A6C24DF1B11`) binding the physical die to hardware crypto keys.
  * **eMMC CID:** Samsung eMMC card identification register (`150100335636434d42036c4bd6326559`).
* **Project Context & Operational Impact:** Serves as the device's physical cryptographic anchor, establishing the live physical hardware digital twin.

### 6. Hardware Security Fuses: SBC, SLA, DAA, SWJTAG
* **Domain / Layer:** Silicon eFuse Register Matrix (`SEC_CFG` blocks).
* **Forensic Definition:**
  * **SBC (Secure Boot Check):** Fused hardware enforcement requiring RSA/ECDSA public-key signature verification before loading preloader from eMMC.
  * **SLA (Serial Link Authentication):** Challenge-response authentication gate requiring OEM private keys over the BROM serial port.
  * **DAA (Download Agent Authentication):** Silicon-enforced signature verification on secondary DA binaries injected into SRAM.
  * **SWJTAG:** Software JTAG debug bus control fuses.
* **Project Context & Operational Impact:** Live BROM register checks confirmed all hardware security flags are `0x0` (DISABLED) on this physical board (`target_config = 0x0`, `flash.locked=0`, `verifiedbootstate=orange`). This factory configuration choices by the ODM establishes full low-level physical acquisition capability.


## SECTION II: Low-Level Boot Topography, Preloader & Bootloaders

### 7. eMMC Hardware Boot Regions (`mmcblk0boot0` / `mmcblk0boot1`)
* **Domain / Layer:** Non-Volatile Physical Storage Geometry.
* **Forensic Definition:** Dedicated physical block regions (4,096 KiB each) on the Samsung 3V6CMB 128 GB eMMC accessed via dedicated hardware commands outside the primary User Data Area (UDA / `mmcblk0`).
* **Project Context & Operational Impact:** Houses redundant hardware bootloader copies (`preloader_a` in `boot0` and `preloader_b` in `boot1`). BROM executes `preloader_a` directly from `mmcblk0boot0`.

### 8. GFH (Generic Firmware Header)
* **Domain / Layer:** MediaTek Proprietary Firmware Container Format.
* **Forensic Definition:** A chained binary header structure used across MediaTek bootloaders (`preloader`, `LK`, `ATF`, `SCP`, `SSPM`). Begins with magic `0x58881688` (or `0x58901690`) followed by chained sub-blocks (`FILE_INFO`, `SEC_KEY`, `SIGNATURE`, `ANTI_ROLLBACK`).
* **Project Context & Operational Impact:** In `preloader_boot1_gfh_object.bin`, the `FILE_INFO` block defines load base `0x00200F10` and jump offset `0xF0`, yielding execution entry at `0x00201000`. Understanding GFH alignment is mandatory to correctly slice binaries for disassemblers (Ghidra / Radare2) without corrupting vector tables.

### 9. BRLYT & EMMC_BOOT Anchors
* **Domain / Layer:** Raw Firmware Descriptors.
* **Forensic Definition:**
  * **EMMC_BOOT:** Magic string located at byte offset `0x000` of `mmcblk0boot0`.
  * **BRLYT (Boot Region Layout Table):** Magic string located at byte offset `0x200`.
* **Project Context & Operational Impact:** Parsed by BROM to validate eMMC clocking, bus width (4-bit/8-bit), and memory offset before executing the primary preloader binary.

### 10. Preloader (`preloader_a` / `preloader_b`)
* **Domain / Layer:** First-Stage Bootloader (FSBL) / Platform Initialization Firmware.
* **Forensic Definition:** Bare-metal ARM32 binary executed in SRAM that configures the clock tree, initializes the MT6357 PMIC, enables LPDDR DRAM controllers, parses GPT, and loads `LK` and `ATF BL31` into DRAM.
* **Project Context & Operational Impact:** Compiled by developer `dengjun` on `20250613-072526`. Loads `LK` container to DRAM address `0x47FFFE00` (link base `0x48000000`) and `tee1` (`ATF`) container to `0x47D803C0` (BL31 link base `0x47D80800`).

### 11. PLPL Boot-Tag Structure (`0x504C504C`)
* **Domain / Layer:** Bootloader Inter-Stage Register Handoff Protocol.
* **Forensic Definition:** The parameter block passed in register `r4` during execution transfer from Preloader to `LK` (and BROM to Preloader). Begins with ASCII magic `PLPL` (`0x504C504C`) followed by a contiguous 23,000-byte (`0x59D8`) payload or a chained tag list.
* **Project Context & Operational Impact:** Carries DRAM rank geometry (offsets `+0x50`, `+0x68`, `+0x80`, `+0x98`), battery boot status, and boot mode flags. Parsed by `LK` function `FUN_480224C` (saved at `0x48000020`).

### 12. LK / LK2 (Little Kernel Second-Stage Bootloader)
* **Domain / Layer:** Second-Stage Bootloader (SSBL) / Android Bootloader (`p26` / `p27`).
* **Forensic Definition:** ARM32 execution environment initialized at DRAM base `0x48000000` (`kmain` entry `0x4801E910`). Controls display splash (LCM panel ST7701S), USB fastboot mode, recovery key detection, partition integrity checks (AVB), and kernel/ramdisk handoff.
* **Project Context & Operational Impact:** Contains a 43-record partition table at carved `0x8EA74`. Merges `dtbo` overlay with base FDT to construct the 137,327-byte live kernel device tree at `0x47880000`.

### 13. ATF (ARM Trusted Firmware / BL31)
* **Domain / Layer:** ARM Exception Level 3 (EL3) Secure Monitor (`p31` `tee1` / `p32` `tee2`).
* **Forensic Definition:** 64-bit firmware executing in highest-privilege ARM EL3, providing Power State Coordination Interface (PSCI) and arbitrating Secure Monitor Calls (SMC) between Normal World (EL1/EL0 Linux Kernel) and Secure World (EL1).
* **Project Context & Operational Impact:** Loaded by Preloader to physical address `0x47D803C0` (BL31 entry `0x47D80800`). Occupies `mblock-4-atf-reserved` (`0x47D80000`–`0x47DCFFFF`, 320 KiB). Contains anomalous build tag `M10PRO_C15_rogbid_V1.2_20211230`.

---

## SECTION III: Coprocessors, Real-Time Subsystems & Dual-Chip Sensor Plane

### 14. SCP (System Companion Processor / CHRE Sensor Hub)
* **Domain / Layer:** Low-Power Coprocessor Domain (`p19` `scp1` / `p20` `scp2`).
* **Forensic Definition:** An embedded ARM Cortex-M4 subsystem running MediaTek's proprietary TinySys real-time operating system (`tinysys-scp-CM4_A`, 544,628 bytes) at 250 MHz.
* **Project Context & Operational Impact:** Manages I2C/SPI sensor buses, Context Hub Runtime Environment (CHRE), and sensor fusion offload directly. Mapped to reserved SRAM base `0x9F900000`. Communicates with Linux kernel via Inter-Processor Interrupts (IPI) and shared memory mailboxes.

### 15. SSPM (System Supervisor Processor / Power Manager)
* **Domain / Layer:** Power & Voltage Coprocessor Subsystem (`p21` `sspm_1` / `p22` `sspm_2`).
* **Forensic Definition:** ARM Cortex-M core running `tinysys-sspm` (428,656 bytes) managing dynamic system power policies: DVFS (Dynamic Voltage and Frequency Scaling), deep sleep states, and PMIC (`MT6357`) voltage rail modulation.
* **Project Context & Operational Impact:** Loaded to load base `0x7FF00018` (SSPM reserved region `0x7FF00000`, 768 KiB). Communicates via hardware mailboxes (`mbox0`–`mbox3`).

### 16. SPMFW (System Power Manager Firmware)
* **Domain / Layer:** Hardware Sleep Sequencer (`p18` `spmfw`).
* **Forensic Definition:** Low-level microcode (59,859 bytes) containing 5 PCM sub-images executed by the dedicated SPM hardware block to automate clock gating, power-well shutdown, and wake events. Loaded into SPM-reserved SRAM (`0x77FF0000`, 64 KiB).

### 17. nRF52832 Coprocessor Plane (Wearable Sensor Controller)
* **Domain / Layer:** External Dual-Chip Physical Coprocessor.
* **Forensic Definition:** Nordic Semiconductor 64 MHz ARM Cortex-M4F microcontroller connected to the main MT6762 SoC via I2C4, UART1 (`ttyS1` at `0x11003000`, IRQ 219), and GPIO interrupt lines (`WIITE_CORP0` / `WIITE_CORP1`).
* **Project Context & Operational Impact:** Controls step tracking, PPG heart rate (PAR2822 at I2C4 address `0x33`), and health telemetry. Stores active firmware in internal 512 KB flash; `/system/firmware/187_187.bin` (153,832 bytes) is an Android-side DFU reference image used by `WearDeviceDeamPix.apk` via `/sys/class/misc/wiite_corp_ctrl/upgrade_op`.

### 18. `wiite_bridge_daemon` / `/dev/ttyS1` Control Plane
* **Domain / Layer:** Serial Hardware Control Bridge & IPC Boundary.
* **Forensic Definition:**
  * **/dev/ttyS1:** Physical high-speed UART node exposed with world-writable (`0666`) permissions on stock vendor firmware, creating a critical local elevation-of-privilege surface.
  * **`wiite_bridge_daemon`:** Auditable C++ native vendor daemon built to replace the stock ODM privileged Java app (`DeviceDaemon` / `com.wiite.devicedaemon`), confining `ttyS1` and `wiite_corp_ctrl` sysfs access strictly to the `wiite_bridge` SELinux domain.

---

## SECTION IV: Storage Topography, Partition Topography, FBE & Android Kernel Base

### 19. Storage Topography & Partition Layout
* **Domain / Layer:** Physical eMMC Partition Topography.
* **Forensic Definition:** Samsung 3V6CMB 128 GB eMMC (`mmcblk0`, 116.5 GB usable) formatted with 38 physical GPT partitions (`mmcblk0p1`–`mmcblk0p38`), 3 eMMC hardware regions (`mmcblk0boot0`, `mmcblk0boot1`, `mmcblk0rpmb`), 1 mask ROM (`BROM`), and 42 total by-name block target aliases.

### 20. `super` Dynamic Partition Container (`mmcblk0p33`)
* **Domain / Layer:** Storage Geometry & Android Logical Volume Management.
* **Forensic Definition:** Single-slot ("A-only") 4 GiB container partition (`main` LP group, 4,289,724,416 bytes) employing Linux Device Mapper Linear (`dm-linear`) driven by LP v10.0 metadata.
* **Project Context & Operational Impact:** Encapsulates three ext4 logical filesystems: `system` (`dm-1`, 1.28 GB), `vendor` (`dm-2`, 291.2 MB), and `product` (`dm-0`, 1.17 GB), complete with dm-verity trees, FEC correction blocks, and embedded VBMeta descriptors.

### 21. `md_udc` vs. Ghost Metadata Partition (`p11`)
* **Domain / Layer:** Android Metadata Partition Binding.
* **Forensic Definition:**
  * **`md_udc` (`mmcblk0p10`, 23.7 MB):** The operational block device mounted at `/metadata` by the stock init chain and fstab.
  * **`metadata` (`mmcblk0p11`, 32 MB):** An unmounted physical partition created by generic MediaTek partition templates.
* **Project Context & Operational Impact:** Classified as a critical BSP Ghost trap. Mounting `p11` at `/metadata` causes silent File-Based Encryption (FBE) decryption failure; `/metadata` must map strictly to `md_udc` (`p10`).

### 22. Android Boot Image Header v2 (`p28` `boot`)
* **Domain / Layer:** Kernel/Ramdisk Packaging Standard.
* **Forensic Definition:** Android boot image format containing: Gzip kernel stream ( uncompresses to 25.8 MB AArch64 `Image`, link base `0xFFFFFF8008080000`), Gzip ramdisk (923.5 KB stream), Header v2 `dt_table` with embedded FDT (89,609 bytes), an undeclared 2,048-byte gap (`0x9C9800`), AVB `vbmeta` descriptor (`0x9CA000`), and a terminal AVB footer (`AVBf` magic at `0x1FFFFC0`).

### 23. DTBO (Device Tree Blob Overlay) & FDT Fragments (`p30` `dtbo`)
* **Domain / Layer:** Flattened Device Tree Kernel Configuration.
* **Forensic Definition:** 8 MiB partition containing an Android `dt_table` header (`0xD7B7AB1E`) and a 29,974-byte Flattened Device Tree. LK reads exactly 30,038 bytes (`0x7556`) into dynamic heap memory, merging overlays onto the base kernel tree.

### 24. File-Based Encryption (FBE) & Keymaster 4.0 Substrate
* **Domain / Layer:** Storage Encryption Substrate & Keystore Architecture.
* **Forensic Definition:** Per-file encryption enforced by Linux Kernel 4.9.190+ using fscrypt v1 API. Key derivation executes via software Keymaster 4.0 (`libpuresoftkeymasterdevice.so` / `libcrypto.so`) and software Gatekeeper over VNDK-29 without hardware TEE/RPMB dependencies. Key material resides in `/data/unencrypted/key/`.

---

## SECTION V: AOSP Purification, VNDK Compatibility & TWRP Build Protocol

### 25. TWRP Crypto Isolation Bubble
* **Domain / Layer:** Linker Architecture / Shared Library Namespace Engineering.
* **Forensic Definition:** An execution container created inside TWRP recovery using `patchelf` to modify binary dynamic headers (`DT_RPATH`, `DT_RUNPATH`, `DT_NEEDED`), forcing Keymaster/Gatekeeper shared libraries to link strictly against local BoringSSL/OpenSSL libraries without symbol collisions against Bionic C runtime libraries.
* **Project Context & Operational Impact:** Solves the classic MediaTek recovery decryption failure on non-standard architectures, achieving 100% operational FBE decryption of `/data` via VFS bind mounts.

### 26. VNDK-29 (Vendor Native Development Kit, API 29)
* **Domain / Layer:** Android Dynamic Linker Namespace Architecture.
* **Forensic Definition:** Versioned system library set in `/system/lib64/vndk-29/` guaranteed to remain stable, allowing Android 10 vendor blobs to execute cleanly on an AOSP 11 system runtime (`ro.vndk.version=29`).

### 27. De-ODM System Purification Strategy
* **Domain / Layer:** ROM Building & Security Remediation Doctrine.
* **Forensic Definition:** A systematic methodology for stripping hostile ODM packages while preserving core hardware enablement blobs, classified into six strict build dispositions:
  * **REQUIRED_FOR_BUILD:** Critical boot, decrypt, HAL, or hardware dependencies.
  * **REMOVE_BEFORE_BUILD:** Proven ODM spyware, UID 1000 backdoors, or unneeded tracking packages.
  * **REPLACE_IN_BUILD:** Unsafe stock components replaced by clean open-source daemons (e.g., `DeviceDaemon` -> `wiite_bridge_daemon`).
  * **VERIFY_BEFORE_BUILD:** Unclosed binaries requiring analysis prior to inclusion or removal.
  * **TEMPORARY_ONLY_IF_BLOCKING:** Allowed temporarily to unblock first-boot failures.
  * **NEVER_REMOVE:** Boot-critical or hardware-critical loader traps/firmware (e.g., Sony camera PDAF libs, Keymaster libs).

---

## SECTION VI: ODM Supply-Chain Security Assessment, Backdoors & Threat Analysis

### 28. Malicious-Capable Architecture (Fixed Severity Framing)
* **Domain / Layer:** Forensic Security Assessment Standard & Threat Modeling.
* **Forensic Definition:** A structural vulnerability classification defining an OS and hardware substrate engineered with the technical capability, permissions, and covert pathways necessary to execute surveillance, arbitrary code execution, and data exfiltration, without asserting legal intent.
* **Project Context & Operational Impact:** Mandatory phrasing used throughout CVE filings (CVE Request 2025814) and CISA disclosures: *"Malicious-capable architecture"*—strictly avoiding unprovable assertions of "confirmed espionage".

### 29. Shared UID 1000 Privilege Cluster
* **Domain / Layer:** Android Framework Security / Application Sandbox Subversion.
* **Forensic Definition:** System privilege level (`android.uid.system`) assigned to 15+ pre-installed ODM applications via platform signing keys, collapsing per-application isolation.
* **Project Context & Operational Impact:** Signed packages (`WearDevicemsg.apk`, `Stopwatch.apk`, `AdupsFota.apk`, `WearAppDialStore.apk`, `WearCommon.apk`) share UID 1000, possessing raw access to serial devices, kernel sysfs parameters, hidden telephony interfaces, and silent package installation mechanisms.

### 30. Confirmed Hostile & Surveillance Packages
* **Domain / Layer:** Software Malware Classification.
* **Forensic Definition:**
  * **`WearDevicemsg.apk` (`/product/app/WearDevicemsg/`):** UID 1000, autostarts via priority 1000 `BOOT_COMPLETED` receiver, initializes an obfuscated third-party SDK (`com.nasa.cook` / `com.nasa.memory`), harvests device hardware identifiers (IMEI, MAC, serials), stores data in hidden encrypted directory `.honor`, executes `chmod 777` on local storage, and exfiltrates JSON telemetry via HTTP POST. Confirmed malware flag by Google Drive scanners.
  * **`Stopwatch.apk` (`/vendor/operator/app/Stopwatch/`):** UID 1000 backdoor. Performs IMEI/IMSI harvesting, WiFi SSID/BSSID collection, shell execution, silent APK installation, and remote command execution.
  * **`AdupsFota.apk` (`/product/priv-app/AdupsFota/`):** Persistent ADUPS OTA agent with `WRITE_SECURE_SETTINGS` and `REBOOT` permissions.
  * **`WearAppDialStore.apk` (`/product/app/WearAppDialStore/`):** UID 1000 dialer/store application holding full surveillance permissions (`READ_SMS`, `SEND_SMS`, `RECORD_AUDIO`, `CAMERA`, `ACCESS_FINE_LOCATION`, `BODY_SENSORS`) and HTTP APK installation capabilities.

### 31. Framework Injections & Backdoor Registries
* **Domain / Layer:** AOSP Framework Contamination.
* **Forensic Definition:**
  * **`pms_sysapp_grant_permission_list.txt` (`/system/etc/permissions/`):** A hardcoded backdoor permission registry read by `WiitePackageManagerUtil`, silently granting all requested runtime permissions (including `INSTALL_PACKAGES`) to listed ODM APKs without user prompts.
  * **`Configuration.isSpecialApp()`:** Custom Java patch injected into `framework.jar` and `services.jar`, shielding ODM telemetry daemons from Android OS memory killers, battery optimizations, and permission restrictions.

---

## SECTION VII: Forensic Epistemology, Evidence Discipline & State Ledger Mechanics

### 32. Absolution (The Doctrine of Zero Inference)
* **Domain / Layer:** Engineering Methodology & Research Epistemology.
* **Forensic Definition:** An absolute scientific operating paradigm rejecting all assumptions, generalizations, and manufacturer claims. A condition or value is asserted as fact only when verified by byte-level binary analysis or direct live hardware observation.
* **Project Context & Operational Impact:** Core governing philosophy: *"Tool output is a lead; the raw binary is the proof; the live attached device outranks both."*

### 33. Five-Tier Authority Hierarchy
* **Domain / Layer:** Evidence Prioritization Matrix.
* **Forensic Definition:**
  1. **Tier 1: Live Rooted Physical Device:** Dynamic runtime observation (`/proc`, `/sys`, `/dev`, ADB root shell under `unshare -m`).
  2. **Tier 2: Clean Binary / Raw Partition Extraction:** Direct byte-level extractions (`super_unpacked`, `entire42.bin`, BROM pulls).
  3. **Tier 3: Static Reverse Engineering:** Disassembly, decompilation (Ghidra / Radare2), and hex header analysis.
  4. **Tier 4: Hardware Digital Twin Cross-Reference:** Prevents software declarations from being promoted to physical truth.
  5. **Tier 5: Databases & Prior Documents:** Output-only ledgers, never verification anchors.

### 34. Controlled Status Vocabulary & Ledger Mechanics
* **Domain / Layer:** Target Closure Ledger Mechanics (`*_closure_log.md`).
* **Forensic Definition:**
  * **[VERIFIED]:** Grounded in byte-level proof or verified runtime capture.
  * **[ACTIVE_RUNTIME]:** Confirmed actively executing, registered, or mounted in live userspace.
  * **[BSP_GHOST]:** Present in vendor software/manifests, but confirmed physically unpopulated or dead on this hardware.
  * **[LOADER_REQUIRED]:** Hardware absent, but scaffolding blobs must remain to satisfy HAL/loader init traps.
  * **[OPEN_BOUNDARY]:** An unresolved research lead defined with: (1) what is known, (2) what is unknown, and (3) the exact command/offset required to close it.
  * **[RETIRED]:** Further analysis abandoned because evidence proves it cannot affect build, security, or remediation scope.
  * **[SUPERSEDED]:** Prior valid finding refined or replaced by superior evidence.
  * **[CORRECTED]:** A prior erroneous entry formally corrected with arithmetic/evidentiary proof.

---

## SECTION VIII: AI-Augmented Forensic Methodology, Multi-Model Swarm & Tooling

### 35. Human-in-the-Loop Deterministic Verification Gate
* **Domain / Layer:** AI Swarm Governance Architecture.
* **Forensic Definition:** Operational rule dictating that AI models (LLMs) operate strictly as search, calculation, and structural drafting assistants under direct human command. No AI generation is accepted as device truth without raw byte-level validation and physical verification by the lead engineer (Albert Pittman / `beright1976`).

### 36. Multi-Model Swarm Architecture (Gemini, ChatGPT, Claude)
* **Domain / Layer:** Multi-Agent Analytical Pipeline.
* **Forensic Definition:** Deploying disparate Large Language Model architectures in parallel to independently audit decompiled code, disassembly scripts, and linker dependencies, utilizing discrepancies between models to expose hallucination, unverified assumptions, and toolchain defects.

### 37. Execution Context Pollution & Namespace Poisoning
* **Domain / Layer:** Forensic Toolchain Threat Vector.
* **Forensic Definition:** The silent corruption of forensic data caused by running diagnostic commands within an active Android userspace contaminated by Magisk mount namespaces (`mnt_namespace`), `overlayfs`, or `su -c` environment variable stripping.
* **Remediation Protocol:** Mandating `unshare -m` namespace isolation or out-of-band BROM-level extraction (`entire42.bin`) to capture unmodified hardware truth.

### 38. 8-Gate Controlled Ghidra Restart Sequence
* **Domain / Layer:** Headless Disassembly Automation Framework.
* **Forensic Definition:** A strict 8-step rerun procedure for raw bare-metal binaries lacking ELF headers (such as `brom_dump.bin`), enforcing explicit pre-seeding of processor context registers (`TMode=0` for ARM32, `TMode=1` for Thumb16) before triggering auto-analysis to eliminate disassembly corruption, fake function boundaries, and truncated control-flow graphs.

################################################################################
################################################################################

================================================================================
C17S / LOKMAT 5 MAX PROJECT VOCABULARY
Plain-Language Terminology Reference
================================================================================

Author: Albert Pittman (beright1976)
Purpose: This is a plain-language reference. It exists so that a reader who
is not a developer, not a security researcher, and not familiar with Android
internals can look up any term used across this project and understand what
it means, without needing to already know the field. Every entry aims to be
correct and complete first, and simple second -- accuracy is never traded
away for simplicity. Readers who want the full technical depth behind a term
should consult the project's forensic ontology and the hardware and software
databases, where the same objects are documented at byte-level precision.

Entries are alphabetical. Where a term has more than one meaning depending on
context, both meanings are given.

================================================================================
NUMBERS & SYMBOLS
================================================================================

3G, 4G, 5G -- The third, fourth, and fifth generations of cellular network
technology. Each generation is a jump in speed, capacity, and the underlying
radio standard. The same numbering style is sometimes borrowed informally
for other wireless technologies, such as Wi-Fi or Bluetooth versions.

A/B (Partitions) -- A device design where two full copies of the system
exist side by side ("slot A" and "slot B"). Updates are installed to the
inactive slot while the active slot keeps running; if the update fails, the
device can fall back to the previously working slot. The C17S uses a
single-slot ("A-only") design instead, which does not have this fallback
mechanism.

================================================================================
A
================================================================================

ADB (Android Debug Bridge) -- The standard command-line tool for
communicating with an Android device over USB or network, used to install
apps, pull files, run shell commands, and view logs.

AGP (Android Gradle Plugin) -- The build tool plugin that compiles Android
app source code into an installable app package. Not used directly for
device firmware work, but part of standard Android app development.

AIDL (Android Interface Definition Language) -- A language used to define
how one piece of Android software can call functions in another, across
process boundaries. Used heavily in the hardware abstraction layer to
describe what a driver or service exposes to the rest of the system.

AOSP (Android Open Source Project) -- The open-source version of Android
published by Google, with no manufacturer additions. A "clean" or
"purified" build in this project means one built as close as possible to
AOSP, with manufacturer-added software removed or replaced.

APEX -- A packaging format Android uses to update core system components
(such as parts of the runtime or media libraries) independently of a full
system update.

APK -- The installable package format for Android applications. Every app
on the device, including the manufacturer's own privileged apps, ships as
an APK.

ARM -- The processor architecture used by nearly all mobile devices,
including the C17S. ARM designs the instruction set; companies like
MediaTek license it and build their own chips around it.

ART (Android Runtime) -- The system that runs installed Android apps on
the device, replacing the older Dalvik runtime starting with Android 5.

ASEC (Android Secure Container) -- An older Android mechanism for
encrypting app data stored on removable storage. Largely obsolete on
modern Android.

ATF (ARM Trusted Firmware) -- Firmware that runs at the highest privilege
level the processor supports, below the operating system itself. It
manages secure/non-secure world switching and power-state transitions
before Linux ever starts. On the C17S, this is the BL31 component loaded
from the tee1/tee2 partitions.

AVB (Android Verified Boot) -- The mechanism Android uses to cryptographically
verify that the bootloader, kernel, and system partitions have not been
tampered with before the device finishes booting. A device with AVB
actively enforcing will refuse to boot modified partitions; a device in
"orange" unlocked state (like the C17S in its research configuration) has
this enforcement relaxed.

AVC (Access Vector Cache) -- The part of SELinux that records permission
denials. When something is blocked by SELinux policy, the denial shows up
as an "avc: denied" entry in the kernel log.

================================================================================
B
================================================================================

Baseband -- The subsystem, usually its own dedicated processor and firmware,
that handles the actual cellular radio connection (calls, data, SMS)
separately from the main Android processor. On the C17S this is the MD1
modem.

Binder -- Android's core mechanism for letting one process call functions
in another process. Almost all communication between apps, system
services, and hardware drivers on Android happens over Binder.

Bionic -- Android's own version of the C standard library, smaller and
built specifically for mobile devices rather than borrowed directly from
desktop Linux.

Blob (Binary Blob) -- A file, usually a driver or firmware component,
supplied only as compiled binary code with no accompanying source code.
Blobs are common in Android vendor software because chip manufacturers
frequently do not release the source for proprietary hardware drivers.

BL31 -- See ATF.

Bootloader -- The software that runs before the operating system, responsible
for loading and starting the next stage (on MediaTek devices, this chain
runs BROM, then Preloader, then LK, then the kernel). "Unlocking the
bootloader" removes a manufacturer restriction that would otherwise prevent
flashing unsigned or custom software.

BROM (Boot ROM / Mask ROM) -- The very first code that runs on the chip,
permanently written into the silicon at the factory and unchangeable by
software. It cannot be updated or erased because it is not stored in
flash memory -- it is physically part of the chip. It is responsible for
finding and starting the next piece of boot firmware (the Preloader).

BSD (Berkeley Software Distribution) -- A family of Unix-like operating
systems, historically important and technically related to but distinct
from Linux.

BSP (Board Support Package) -- The set of drivers, configuration files, and
low-level software a chip manufacturer provides to make its chip work with
Android on a specific circuit board. The C17S's BSP is built for the MT6765
chip family even though the physical chip installed is a different but
closely related part (MT6762).

BSP Ghost -- A term used throughout this project for something (a driver
reference, a device tree entry, a permission, a sysfs node) that exists in
the manufacturer's software because it came bundled with the board support
package, but has no actual hardware behind it on this specific device. A
"ghost" entry looks real in the software but corresponds to nothing
physically present.

Build.prop -- A configuration file on Android devices containing key-value
system properties (device model, Android version, hardware identifiers,
and many manufacturer-specific settings) that both the OS and apps can
read.

Byte-Exact Verification -- This project's standard for proving a technical
claim: a finding is only accepted once it has been confirmed against the
actual bytes of a file, a register, or a live memory location, rather than
being accepted because it matched what was expected or what a tool
reported without independent confirmation.

================================================================================
C
================================================================================

C -- A foundational programming language, in continuous use since the
1970s, that much of the Android operating system and most Linux software
is written in or built on top of.

C++ -- An extension of the C language that adds object-oriented
programming features. Widely used for performance-sensitive Android
system code and native libraries.

C# -- A Microsoft programming language, unrelated to this project's core
work but included here as a general reference term.

CHRE (Context Hub Runtime Environment) -- A framework for running
sensor-processing code on a low-power coprocessor rather than the main
application processor, so continuous sensor tasks (like step counting)
do not have to keep the main chip awake. On the C17S this runs on the SCP.

Chroot -- A Linux technique for changing the apparent root directory a
process sees, effectively isolating it into its own filesystem view.

Clock Gating -- A power-saving technique where the electrical clock signal
to an unused part of the chip is switched off, so that portion of the
chip consumes no power until it is needed again.

Closure Log -- A permanent, append-only record this project keeps for
every research target, tracking the current status of every finding about
that target using a fixed set of labels (verified, open boundary,
superseded, corrected, retired, and so on) so that nothing is silently
lost or rewritten without a trace.

Compatible String -- A text label inside a device tree entry that
identifies which driver should be matched to a piece of hardware. Its
presence in software does not by itself prove the hardware is physically
there -- see BSP Ghost.

Cooling Device -- An entry in the Android/Linux thermal management system
representing something the system can throttle or shut down to bring the
device's temperature back under control (for example, reducing CPU speed).

CTS (Compatibility Test Suite) -- Google's official test suite that a
device must pass to be certified for Google Mobile Services.

================================================================================
D
================================================================================

DA (Download Agent) -- A secondary bootloader-stage program used by
MediaTek's flashing tools to read from and write to a device's storage
at a low level, used during firmware recovery and forensic extraction.

DAC (Discretionary Access Control) -- The traditional Unix/Linux
permission system (user, group, read/write/execute), which relies on the
resource owner to set restrictions. Contrast with MAC (Mandatory Access
Control), which is enforced regardless of the owner's choices.

Dalvik -- The original Android app runtime, used before Android 5.0
introduced ART as its replacement.

Dart -- The programming language used to write apps with Google's Flutter
toolkit.

Data-at-Rest Encryption -- Encryption that protects stored data while the
device is powered off or locked, so the contents of storage cannot be read
without the correct key. See FBE and FDE for the two Android approaches.

Device Tree (DT / DTB / DTBO / DTS / FDT) -- A structured description of
a device's hardware -- what components exist, their memory addresses,
their interrupt lines, and how they connect -- that the Linux kernel reads
at boot to configure itself for that specific board. DTS is the
human-readable source form; DTB is the compiled binary form the kernel
actually reads; DTBO is an overlay that can be merged onto a base device
tree to add or modify entries; FDT (Flattened Device Tree) refers to the
binary format itself.

Digital Twin -- This project's term for a structured, evidence-based
model of the device: a record of what physically exists, what it does,
how it is wired together, and what depends on it, built entirely from
verified observation of the real hardware and software rather than from
manufacturer claims or assumptions.

DMA (Direct Memory Access) -- A method that lets hardware components move
data directly into or out of memory without routing every byte through
the main processor, improving performance for things like storage and
audio.

dm-verity -- A Linux kernel feature, part of Android Verified Boot, that
checks blocks of a filesystem against a cryptographic hash tree as they
are read, detecting any modification to system partitions at runtime.

DRM (Digital Rights Management) -- Technology used to control access to
copyrighted content, such as licensed video streaming. Widevine is the
DRM system used on Android.

DTB, DTBO, DTS -- See Device Tree.

DVFS (Dynamic Voltage and Frequency Scaling) -- A power management
technique where the processor's clock speed and voltage are adjusted in
real time based on workload, trading performance for power savings when
full speed is not needed.

================================================================================
E
================================================================================

eBPF (extended Berkeley Packet Filter) -- A Linux kernel technology that
allows small, safety-checked programs to run inside the kernel itself,
commonly used for monitoring, filtering, and tracing.

eFuse -- A microscopic fuse physically built into a chip that can be
permanently "blown" (set) exactly once during or after manufacturing to
lock in a setting, such as disabling debug access or enforcing secure
boot. Once set, an eFuse cannot be reset.

EL0 - EL3 (Exception Levels) -- The privilege levels defined by the ARM
processor architecture, from EL0 (normal applications, least privileged)
up to EL3 (secure firmware such as ATF, most privileged). The Android
operating system kernel typically runs at EL1.

ELF (Executable and Linkable Format) -- The standard file format for
executables, shared libraries, and object code on Linux and Android.

eMMC (embedded MultiMediaCard) -- A type of flash storage chip soldered
directly onto a device's circuit board, functioning similarly to an SD
card but built in. The C17S uses a Samsung eMMC chip as its primary
storage.

================================================================================
F
================================================================================

Fastboot -- A protocol and command-line tool for communicating with a
device's bootloader, used to flash partitions, unlock the bootloader, and
reboot into different modes, when the device is not fully booted into
Android.

FBE (File-Based Encryption) -- Android's modern storage encryption
approach, where individual files (rather than the entire storage volume)
are encrypted, allowing different files to be unlocked under different
conditions -- for example, some data can become available before the user
unlocks the device at all.

FDE (Full-Disk Encryption) -- An older Android storage encryption approach
where the entire data partition is encrypted as a single unit and nothing
on it is accessible until the whole thing is unlocked. Superseded by FBE
on modern Android.

FDT (Flattened Device Tree) -- See Device Tree.

FEC (Forward Error Correction) -- Extra data stored alongside a partition
that allows small amounts of corruption to be detected and automatically
repaired without needing a backup copy.

Firmware -- Software that is closely tied to specific hardware and
typically runs at a lower level than the main operating system --
bootloaders, coprocessor programs, and chip-management code are all
examples of firmware.

Flutter -- Google's toolkit for building apps that run from a single
codebase across mobile, desktop, web, and Fuchsia.

Framework (Android Framework) -- The layer of Android's operating system,
written mostly in Java, that provides the APIs apps use and manages
system-wide services such as activity management, package management, and
permissions. Delivered primarily through framework.jar and services.jar.

FRP (Factory Reset Protection) -- An Android feature that requires the
previous Google account credentials to be entered after a factory reset,
intended to deter theft.

Fuchsia -- An alternate operating system from Google, unrelated to Linux,
that some newer Google hardware and Flutter's cross-platform support can
target.

================================================================================
G
================================================================================

GAPP (Google Apps) -- The bundle of Google-made applications and services
(Play Store, Gmail, Maps, and so on) that are not part of AOSP and must be
separately licensed and installed by a device manufacturer.

Gatekeeper -- The Android component responsible for verifying lock-screen
credentials (PIN, pattern, password) and working with Keymaster to bind
that verification to the device's encryption keys. Can be implemented
purely in software or backed by a hardware security module; the C17S uses
a pure-software implementation.

GFH (Generic Firmware Header) -- A MediaTek-specific header format used to
wrap and chain together the various firmware images (Preloader, LK, ATF,
SCP, SSPM) loaded during boot, containing signing and integrity
information for each stage.

Ghidra -- A free reverse-engineering tool, originally developed by the
U.S. National Security Agency and later released publicly, used to
disassemble and analyze compiled binary code without having its original
source.

GMS (Google Mobile Services) -- The underlying code that supports Google's
app ecosystem (Play Store, push notifications, location services, and
more) on a device.

GNSS (Global Navigation Satellite System) -- The general term for
satellite-based positioning, covering GPS (United States), GLONASS
(Russia), BeiDou (China), and Galileo (European Union) together, all of
which the C17S can use.

GPIO (General-Purpose Input/Output) -- Individual physical pins on a chip
that software can directly set high, set low, or read, used for simple
signaling and control between the main chip and other components.

GPT (GUID Partition Table) -- The standard format for dividing a storage
device into partitions on modern hardware, used to lay out the C17S's 38
partitions on its eMMC storage.

GPU (Graphics Processing Unit) -- A processor core specialized for
rendering images and graphics, separate from the main CPU. The C17S uses
a PowerVR GE8320 GPU.

GSI (Generic System Image) -- A version of Android's system partition
built to be device-independent, used to test whether a device's vendor
software is properly separated from the OS itself under Project Treble.

================================================================================
H
================================================================================

HAL (Hardware Abstraction Layer) -- The layer of software that sits
between Android's framework and the actual hardware drivers, giving
Android a standard way to talk to different manufacturers' hardware
without needing to know its specific implementation details.

Hash / SHA-256 -- A hash is a fixed-length string of characters generated
from a file or block of data using a mathematical function; changing even
one byte of the original data produces a completely different hash.
SHA-256 is the specific hashing algorithm used throughout this project to
prove that two files are byte-for-byte identical. A matching hash proves
identical content -- it does not, by itself, explain what that content
does.

HIDL (HAL Interface Definition Language) -- The language Android
originally used to formally define the interface between the framework
and hardware drivers under Project Treble. It has since been largely
superseded by AIDL for newer hardware interfaces, though HIDL interfaces
remain present and in use on Android 10 devices like the C17S.

Hex Editor -- A tool for viewing and editing the raw bytes of a file
directly, shown in hexadecimal, rather than interpreting the file's
contents as text or a particular format.

================================================================================
I
================================================================================

I2C (Inter-Integrated Circuit) -- A simple two-wire communication bus
commonly used to connect a main chip to nearby low-speed peripheral
chips, such as sensors and power-management components.

IMEI (International Mobile Equipment Identity) -- A unique number
assigned to a cellular device's radio hardware, used to identify the
device on a mobile network.

IMS (IP Multimedia Subsystem) -- The framework that lets voice calls and
messaging run over a data connection rather than traditional circuit-
switched cellular service, underlying features like VoLTE and Wi-Fi
Calling.

IMSI (International Mobile Subscriber Identity) -- A unique number stored
on a SIM card that identifies the subscriber (not the device) to a mobile
network.

Intent -- An Android message object representing a request to perform an
action or a notification that something has happened, used heavily for
communication between app components and system services.

IPC (Inter-Process Communication) -- Any mechanism that allows separate,
isolated running programs to exchange data or requests with each other.
On Android, this is primarily done through Binder.

IRQ (Interrupt Request) -- A signal a piece of hardware sends to the
processor to indicate it needs immediate attention, interrupting whatever
the processor was doing so it can respond.

================================================================================
J
================================================================================

Java -- An object-oriented programming language, in use since 1995, that
is the original and still most common language for writing Android
applications and much of the Android framework itself.

JetPack -- A collection of modern Android development libraries
(packaged under androidx.*) that consolidate and replace many older,
scattered Android APIs.

JNI (Java Native Interface) -- The mechanism that allows Java code running
on Android to call into compiled native code written in C or C++, and
vice versa.

================================================================================
K
================================================================================

Kamakiri -- An exploitation technique used against MediaTek's BROM stage
to gain unauthenticated code execution during the earliest part of the
boot process, used by tools like mtkclient for low-level device access.

Keymaster -- The Android component responsible for generating, storing,
and using cryptographic keys, particularly the keys that protect
encrypted storage. Like Gatekeeper, it can be backed by a dedicated
hardware security module or implemented purely in software; the C17S
uses a pure-software Keymaster implementation.

Kernel -- The core of the operating system, running with the highest
privilege among general-purpose software, responsible for managing
memory, processes, and hardware access for everything running above it.
Android is built on top of the Linux kernel.

Kotlin -- A newer programming language, now Google's preferred language
for Android app development, interoperable with existing Java code.

================================================================================
L
================================================================================

LDO (Low-Dropout Regulator) -- A type of voltage regulator that converts
one voltage to a slightly lower, stable voltage with minimal loss, used
throughout a device's power circuitry to feed individual components the
exact voltage they need.

LineageOS -- A community-maintained project that builds enhanced,
AOSP-based Android images for a wide range of devices, independent of the
original manufacturer's software.

Little Kernel (LK / LK2) -- The second-stage bootloader used on MediaTek
devices, running after the Preloader and before the Linux kernel. It
handles tasks like displaying the boot splash screen, detecting recovery
key combinations, verifying partitions, and finally loading the kernel
and ramdisk into memory to start Android (or a recovery environment such
as TWRP).

Logcat -- Android's system logging tool and log format, used to capture
messages from apps, the framework, and the kernel for debugging and
analysis.

LP Metadata (Logical Partition Metadata) -- The metadata format Android
uses to describe how a single physical "super" partition is subdivided
into several logical partitions (such as system, vendor, and product)
that can be resized without repartitioning the underlying physical
storage.

LSM (Linux Security Modules) -- A framework built into the Linux kernel
that allows different mandatory access control systems, such as SELinux,
to be plugged in.

LTTng (Linux Trace Toolkit: next generation) -- A Linux and Android
tracing framework used to record detailed, low-overhead logs of system
behavior for performance and debugging analysis.

================================================================================
M
================================================================================

MAC (Mandatory Access Control) -- A security model, such as SELinux, where
the operating system itself enforces access rules that cannot be
overridden by the owner of a resource, in addition to any ordinary
permission settings. Not to be confused with MAC address, below.

MAC Address (Media Access Control Address) -- A unique hardware identifier
assigned to a network interface (Wi-Fi, Bluetooth), used to identify that
specific interface on a local network.

Macro -- In programming, a single named instruction or shorthand that
automatically expands into a larger set of instructions when it is used.

Magisk -- A widely used Android rooting tool that grants privileged
("root") access to selected apps while modifying the system in a way
designed to leave the original boot and system partitions themselves
unchanged, aiming to stay concealed from apps that check for tampering.

Malicious-Capable Architecture -- This project's formal severity
classification for a piece of software or hardware design that has the
technical capability, permissions, and hidden pathways necessary to carry
out surveillance, unauthorized code execution, or data exfiltration --
without asserting that this capability has been used with malicious
intent, which would require separate evidence to prove.

Mali -- A family of GPU designs owned by ARM, commonly used in
Android devices generally. The C17S does not use a Mali GPU; it uses a
PowerVR GE8320.

Mask ROM -- See BROM.

MediaTek -- A Taiwanese semiconductor company that designs and
manufactures the system-on-chip used in the C17S, among many other
mobile and IoT devices.

ME_ID (MediaTek Engine ID) -- A unique identifier permanently fused into
a MediaTek chip during manufacturing, used as a cryptographic anchor
tied to that specific physical piece of silicon.

Metadata -- In its general computing sense, metadata is data that
describes other data (for example, a file's size, creation date, or
owner, rather than the file's actual contents). In this project it also
refers specifically to the Android /metadata partition, a small partition
used to store encryption-related metadata needed to decrypt user data at
boot -- and to a specific, documented trap on the C17S, where a
similarly-named but inactive physical partition must not be confused with
the one Android actually uses (see md_udc in the hardware database).

Modem -- The hardware and firmware responsible for a device's cellular
radio functions. On the C17S this runs as a separate processor with its
own firmware image, communicating with the main Android system rather
than running as part of it.

================================================================================
N
================================================================================

NAN (Neighborhood Aware Networking) -- A Wi-Fi standard that allows
nearby devices to discover and communicate with each other directly,
without a traditional Wi-Fi access point.

NDK (Native Development Kit) -- Google's toolkit for writing Android app
components in C or C++ rather than Java or Kotlin, typically used for
performance-critical code.

*Nix -- An informal blanket term covering Unix, Linux, and similar
operating systems that share common design roots.

nRF52832 -- The specific Nordic Semiconductor coprocessor chip used as
the C17S's secondary, wearable-focused controller, separate from the main
MediaTek chip. It handles sensor and step-tracking tasks and communicates
with the main system over a serial connection.

NSA (National Security Agency) -- The United States intelligence agency
that originally developed and later publicly released the Ghidra
reverse-engineering tool used in this project.

================================================================================
O
================================================================================

OAT -- A native, precompiled code format Android generates ahead of time
from an app's DEX bytecode, used to speed up app execution. OAT files are
themselves in the standard ELF format.

OBB (Opaque Binary Blob) -- A large auxiliary data file, separate from an
app's main APK, commonly used to hold large assets like game data.

ODEX (Optimized DEX) -- A precompiled or preprocessed form of an Android
app's DEX bytecode.

ODM (Original Design Manufacturer) -- A company that designs and builds a
device on behalf of a brand that will sell it under its own name. On the
C17S, Wiite (also associated with the Topwise/Linswear family of
manufacturers) is the ODM responsible for the device-specific software
found in this research.

OEM (Original Equipment Manufacturer) -- The company whose brand name
appears on the finished product. Depending on the arrangement, an OEM may
design its own hardware or have an ODM design it on their behalf.

OOP (Object-Oriented Programming) -- A programming style organized around
"objects" that bundle data and behavior together, used by Java, Kotlin,
C++, and many other languages.

Open Boundary -- This project's term for a research question that is
explicitly acknowledged as unresolved, recorded along with exactly what is
already known, what remains unknown, and what specific evidence would be
needed to close the question -- rather than being left as a vague,
unstated gap.

OPP (Operating Performance Point) -- A defined combination of clock speed
and voltage that a processor or GPU can run at. DVFS switches between
different OPPs depending on workload.

OTA (Over-the-Air) Update -- A software update delivered to a device over
a network connection rather than through a physical connection to a
computer.

================================================================================
P
================================================================================

Partition -- A defined section of a storage device, treated by the system
as if it were its own separate disk, used to organize different types of
data (bootloader, operating system, user data, and so on) separately from
each other.

Patchelf -- A tool for modifying certain low-level properties of already-
compiled ELF binaries, such as which shared libraries they load and where
they look for them, without needing to recompile the program from source.

PID (Process ID) -- A number the operating system assigns to a running
program, used to identify and manage that specific running instance.

PMIC (Power Management Integrated Circuit) -- A dedicated chip responsible
for generating and distributing the various regulated voltages a device's
components need, and for managing battery charging. The C17S's main PMIC
is the MediaTek MT6357.

Preloader -- The first-stage bootloader on a MediaTek device, loaded and
run by BROM. It sets up the memory controller, initializes the PMIC,
reads the partition table, and loads the next boot stage (LK) into
memory.

Procfs (/proc) -- A virtual filesystem on Linux and Android that exposes
live information about the running kernel and processes as if it were a
set of ordinary files, without that information actually being stored on
disk.

PSCI (Power State Coordination Interface) -- A standard interface, used by
ARM Trusted Firmware, for managing processor power states such as putting
CPU cores to sleep or waking them up.

Python -- A widely used, general-purpose programming language known for
readable syntax, commonly used in this project for scripting analysis and
verification tasks against extracted device data.

================================================================================
R
================================================================================

Radare2 -- A free, open-source reverse-engineering framework used to
disassemble, analyze, and debug compiled binary code, used in this
project alongside Ghidra for independent cross-verification.

Recovery -- A minimal, separate operating environment on an Android
device, launched instead of the main OS, used for tasks like installing
updates, wiping data, and (with a custom recovery such as TWRP) advanced
tasks like decrypting and backing up user data.

Regex (Regular Expression) -- A compact pattern-matching syntax used to
search for, validate, or extract specific text patterns within data.

RIL (Radio Interface Layer) -- The Android subsystem that sits between
the telephony framework and the actual baseband/modem hardware,
translating standard Android telephony requests into the specific
commands a given modem understands.

Rollback Index -- A value used by Android Verified Boot to prevent a
device from being downgraded to an older, less secure version of its
software, since older versions may contain known, fixed vulnerabilities.
This is a distinct concept from RIL, above, despite the similar-sounding
name.

Root / Rooting -- Gaining unrestricted administrative access to an
Android device, beyond what the manufacturer normally permits. Rooting
the C17S in this project was done using Magisk, one of several available
methods.

RPC (Remote Procedure Call) -- Calling a function that runs in a different
process, or on a different machine, as if it were a normal local function
call. On Android this is typically implemented over Binder.

RPMB (Replay Protected Memory Block) -- A specially protected region of
eMMC storage designed to resist tampering and replay attacks, commonly
used to store security-sensitive data tied to hardware-backed encryption.

Rust -- A systems programming language designed to provide strong memory-
safety guarantees at compile time, increasingly used in newer Android
components.

================================================================================
S
================================================================================

SBC (Secure Boot Check) -- A hardware-enforced setting on a chip that
requires each stage of the boot chain to be cryptographically signed and
verified before it is allowed to run. On the C17S's current research
configuration, this enforcement has been disabled.

SCP (System Companion Processor) -- A low-power coprocessor built into
the MediaTek chip, separate from the main ARM cores, used to handle
sensor management and similar always-on tasks without needing to keep the
main processor awake.

SELinux (Security-Enhanced Linux) -- A mandatory access control system
built into the Linux kernel that Android uses to restrict what each
process is allowed to do, even if the process is otherwise running with
elevated permissions. It can run in "enforcing" mode (violations are
blocked) or "permissive" mode (violations are only logged, not blocked).

SELinux Policy -- The specific, compiled set of rules that SELinux
enforces on a given device, defining exactly what each type of process is
and is not permitted to do.

Service (Android) -- An app component designed to run operations in the
background, without a visible user interface.

SGL (Simple Graphics Library) -- A software layer that sits above lower-
level graphics APIs such as OpenGL or Vulkan.

SIM / UICC -- The physical (or embedded) card that identifies a
subscriber to a cellular network and securely stores subscriber
credentials. UICC (Universal Integrated Circuit Card) is the formal name
for the chip technology that SIM cards are built on.

SLA (Serial Link Authentication) -- A MediaTek BROM-stage security
check requiring a manufacturer's private key to authenticate a connection
over the serial download interface before certain low-level operations
are permitted.

SoC (System on a Chip) -- A single chip that integrates the processor,
graphics, memory controller, and many other functions that would once
have been separate components. Used in essentially all mobile devices.

SPI (Serial Peripheral Interface) -- A high-speed communication bus used
to connect a main chip to nearby peripheral components, similar in
purpose to I2C but typically faster and requiring more wires.

SPMFW (System Power Manager Firmware) -- Low-level microcode running on a
dedicated hardware block that automates fine-grained power sequencing
tasks like clock gating and wake events, operating independently of the
main processor.

SQLite -- A lightweight, file-based database engine built directly into
most mobile operating systems, including Android, and used by many apps
and system components to store structured data locally.

SSPM (System Supervisor Processor / Power Manager) -- A coprocessor
responsible for managing dynamic power policy across the chip, including
voltage scaling and deep sleep transitions, working closely with the
PMIC.

StrongBox -- A specification for a dedicated, physically separate
hardware security module that can back Android's Keymaster and
Gatekeeper functions. The C17S does not implement StrongBox; its
Keymaster and Gatekeeper run entirely in software.

Sysfs (/sys) -- A virtual filesystem on Linux and Android that exposes
kernel and driver settings and status as if they were ordinary files,
allowing both reading device state and, in many cases, writing to control
hardware behavior directly.

/system -- The main partition on an Android device containing the core
operating system files.

================================================================================
T
================================================================================

TEE (Trusted Execution Environment) -- An isolated, protected processing
area on a chip, separate from the main operating system, designed to run
sensitive operations (such as cryptographic key handling) where even a
compromised main OS cannot interfere. ARM's specific implementation of
this concept is called TrustZone.

Thermal Throttling -- Automatically reducing a device's performance (most
commonly CPU or GPU clock speed) in response to rising temperature, to
prevent overheating.

TinySys -- MediaTek's proprietary lightweight real-time operating system
used on its coprocessors, such as the SCP and SSPM.

Treble -- A major Android architectural change that formally separated
the Android operating system framework from vendor-specific hardware
drivers, using standardized interfaces (HIDL/AIDL and VINTF) so that
devices could receive OS updates without waiting for the hardware vendor
to update its drivers.

TrustZone -- ARM's specific hardware implementation of a Trusted
Execution Environment, dividing the processor into a "secure world" and
a "normal world."

TWRP (Team Win Recovery Project) -- A widely used open-source custom
recovery for Android devices, providing a touch interface for backups,
restores, installing files, and (when properly built for a device)
decrypting encrypted user data.

================================================================================
U
================================================================================

UART (Universal Asynchronous Receiver-Transmitter) -- A simple, common
serial communication interface used to connect two components with a
minimal two-wire connection. On the C17S, UART1 is the physical link
between the main chip and the nRF52832 coprocessor.

UICC -- See SIM / UICC.

UID (User ID) -- A number the operating system assigns to identify a
specific user account or, on Android, a specific app's sandboxed
identity. Some privileged system apps on Android share UID 1000, the same
identity used by the core system itself, which removes the normal
isolation between those apps.

USBDL (USB Download Mode) -- A low-level mode MediaTek chips can enter,
communicating over a USB virtual serial connection, used for firmware
flashing, recovery, and forensic extraction at the earliest stage of the
boot process, before the operating system or even the Preloader has
started.

================================================================================
V
================================================================================

vbmeta (Verified Boot Metadata) -- A partition and data structure holding
the cryptographic information Android Verified Boot uses to check the
integrity of other partitions.

/vendor -- The partition on an Android device containing manufacturer-
and chip-specific drivers and configuration, kept separate from /system
under Project Treble.

VINTF (Vendor Interface) -- The Android framework that declares and
checks which hardware interfaces a device's framework and vendor software
each provide and expect, ensuring the two sides are compatible.

VNDK (Vendor Native Development Kit) -- A defined, versioned set of
system libraries that vendor software is allowed to depend on, kept
stable across OS updates so that a manufacturer's compiled drivers keep
working even when the OS above them changes.

Vulkan -- A modern, lower-level graphics API, offering more direct
hardware control than OpenGL, used for rendering graphics.

================================================================================
W
================================================================================

Widevine -- Google's DRM system for protecting licensed video content on
Android, available in different security levels (L1 being hardware-backed
and most secure, L3 being software-only).

WMT (Wireless Management/Connectivity Driver) -- MediaTek's internal
driver layer that manages the chip's combined Wi-Fi, Bluetooth, and GNSS
radio hardware as a group.

================================================================================
X
================================================================================

XDA (XDA Developers) -- A long-running community forum for mobile device
development, modification, and technical discussion, and the intended
public home for this project's release thread.

================================================================================
Z
================================================================================

Zygote -- A special, privileged Android process that starts early in boot
and keeps a pre-warmed copy of the Android runtime in memory. Every new
app process is created by copying ("forking") from Zygote rather than
starting from scratch, which is what makes Android app launches fast.

================================================================================
END OF VOCABULARY
================================================================================


