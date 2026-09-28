################################################################################
# C17S SOFTWARE DIGITAL TWIN
# EXPANSION WORKFLOW, SECTION PROTOCOL & AGENT HANDOFF
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Purpose     : Repeatable workflow for expanding C17S_SOFTWARE_Forensics.md
# Status      : WORKFLOW AUTHORITY DOCUMENT — USE BEFORE EVERY NEW SECTION
################################################################################

################################################################################
# 0 — PURPOSE OF THIS WORKFLOW DOCUMENT
################################################################################

This document tells any future chat session, agent, or developer how to expand
and verify the C17S Software Digital Twin section by section without requiring
Beright / Albert to re-explain the project every time.

The goal is not to create a ROM build document, note, a summary, or a casual research log.
The goal is to build a complete software digital twin database of the LOKMAT APPLLP 5 MAX
(C17S) software stack: a database-quality replacement for having the device in
hand.

When the database is complete, a capable Android/MediaTek developer should be
able to read the hardware twin plus this software twin and reproduce the device
bring-up trail, understand what is active, what is declared, what is required,
what is dangerous, what is leftover BSP residue, and what must not be carried
into a clean AOSP build. The developer needs to be able to do these things not 
software digital twin the digitaital twin is the evidence.

Expected final scale:
- Original software database: approximately 11,000+ lines.
- Expanded software digital twin target: likely 30,000+ lines. but theres no limit or minimum.
- Work will span multiple chat sessions.
- Each section must be independently resumable from this workflow document.

################################################################################
# 1 — ABSOLUTE DOCTRINE
################################################################################

No stale values.
No borrowed values from similar MediaTek devices.
No assumption promoted to software truth.
No software declaration promoted to hardware truth.
No fstab entry promoted to active mount truth.
No APK permission promoted to behavior truth without static/runtime evidence.
No device node promoted to active use without mount/process/HAL/sysfs evidence.
No partition name promoted to active mount source without live /proc/mounts proof.
No framework string promoted to execution truth without call path or runtime proof.
No vendor blob promoted to required status without dependency, dlopen, HAL, init,
service, or boot-stability evidence.

Every fact must end in one of these states:
- [VERIFIED]
- [ACTIVE_RUNTIME]
- [FILE_AUTHORITY]
- [DECLARED]
- [RAW_PARTITION]
- [DYNAMIC_CONTAINER]
- [REQUIRED]
- [LOADER_REQUIRED]
- [REPLACE]
- [STRIP]
- [BSP_GHOST]
- [NEGATIVE]
- [OPEN_LEAD]
- [CONFLICT_RESOLVED]
- [MOVED_TO_SECTION_XX]

A section is not complete just because it is long. A section is complete only
when every entry inside its stated scope is classified and every classification
is backed by an evidence source.

################################################################################
# 2 — PRIMARY SOURCE SET
################################################################################

Each new session must load or be given the following source set.

A. Section directory / scope authority
   - C17S_SOFTWARE_SECTIONS_DIRECTORY.md
   - Defines Sections 00 through 18.
   - Defines scope, dependencies, cross-references, and section status.

B. Original software database / seed authority
   - C17S_SOFTWARE_Forensics.md
   - This is the original software digital twin seed.
   - It must be read section-by-section before rewriting or expanding.
   - Do not skip it and do not replace it from memory.

C. Expanded hardware database / physical truth boundary
   - C17S_LOKMAT5MAX_HARDWARE_DATABASE.md
   - Hardware sections define actual physical topology.
   - Software declarations must be cross-checked against this database before
     being treated as real hardware.

D. Current completed software sections
   - Example: C17S_SOFTWARE_SECTION00_FINAL.md
   - Completed sections become upstream authority for later sections.
   - Do not regress corrected terminology from completed sections.

E. ROM/build document set, when relevant
   - C17S_ROM_BUILD_DOCUMENT.md
   - DOC00-DOC21 drive documents when supplied.
   - These are supporting evidence and workflow records, not replacements for
     live verification.

F. Live rooted device evidence
   - adb shell through Magisk su.
   - Runtime authority for /proc, /sys, /dev, getprop, service state, mounts,
     lshal, dumpsys, ps, logcat, and runtime behavior.

G. Clean TWRP partition pulls
   - super_unpacked/system/
   - super_unpacked/vendor/
   - super_unpacked/product/
   - system.img, vendor.img, product.img
   - Authority for file-sourced data.

Known local host root used by Beright during Section 00:
~/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/

Known super unpack path:
~/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked/

################################################################################
# 3 — AUTHORITY TIERS
################################################################################

TIER 1 — Live rooted C17S device
- adb shell
- su through Magisk
- /proc, /sys, /dev, getprop, lpdump, service, dumpsys, lshal, ps, logcat
- Authority for runtime state.

TIER 2 — Clean TWRP-extracted partition pulls
- system/, vendor/, product/
- system.img, vendor.img, product.img
- Authority for file presence, file contents, hashes, sizes, XML, RC, manifests,
  APKs, binaries, libraries, and firmware blobs.

TIER 3 — Static and binary reverse engineering
- apktool, JADX, Ghidra, strings, xxd, readelf, objdump, file, sha256sum
- Authority only for the actual artifact analyzed.

TIER 4 — Hardware digital twin
- Confirms physical presence, absence, bus ownership, sensor identity, radio,
  storage, display, GPU, camera, audio, security substrate, and negative hardware.
- Prevents software leftovers from being promoted to hardware truth.

FINAL — Beright / Albert acceptance
- Final correction gate.
- If Beright has already corrected a value by live evidence, do not regress.

################################################################################
# 4 — SESSION START PROTOCOL
################################################################################

At the beginning of a new section or new chat session, the agent must do this:

1. Identify the target section number and exact title from
   C17S_SOFTWARE_SECTIONS_DIRECTORY.md.

2. Read the section scope, status, dependencies, and hardware cross-reference
   from the directory.

3. Read the matching original section in C17S_SOFTWARE_Forensics.md completely.
   Do not rely on snippets or memory.

4. Read any relevant hardware database section listed in the cross-reference map.

5. Read the latest completed prior software section if it is upstream of the
   current section.

6. Create a section delta ledger before writing the final section:
   - KEEP
   - CORRECT
   - EXPAND
   - DEMOTE
   - MOVE_TO_SECTION_XX
   - OPEN_LEAD
   - NEEDS_LIVE_VERIFICATION
   - NEEDS_LOCAL_FILE_VERIFICATION
   - NEEDS_BINARY_ANALYSIS

7. Only then request live commands or host commands.

8. Do not ask for broad scripts unless the user explicitly approves them.

################################################################################
# 5 — SECTION EXPANSION WORKFLOW
################################################################################

Every section must follow this order.

PHASE 1 — Scope lock
- Copy the section title and scope from the section directory.
- State what belongs in the section.
- State what does not belong and where it moves.
- Identify upstream dependencies and downstream consumers.

PHASE 2 — Source read pass
- Read the old software database section line by line.
- Identify every value, path, service, binary, package, HAL, property, sysfs node,
  partition, mount, permission, or behavior claim.
- Do not summarize yet.

PHASE 3 — Delta ledger
- For every original claim, classify it:
  KEEP, CORRECT, EXPAND, DEMOTE, MOVE, OPEN_LEAD, or VERIFY.
- Contradictions are not erased. They are resolved by evidence layer.
- If two values are both true at different layers, preserve both.

PHASE 4 — Evidence collection plan
- Ask only for commands that close real gaps.
- Keep commands small and direct.
- Prefer deterministic commands whose failure is obvious.
- Avoid long pipelines inside remote su.
- Avoid broad scripts that hide which step failed.

PHASE 5 — Live verification
- Use live device only for runtime truth.
- Capture command and output.
- Mark capture-specific fields separately from immutable facts.
- Examples of capture-specific fields: bootreason, bootprof timing, timestamps,
  process IDs, mount times, temporary loop assignment if not stable.

PHASE 6 — Host clean-pull verification
- Use local extracted partition trees for file data.
- Hash important images/blobs/APKs/libraries.
- Count files only from the clean pull, not from the modified live device.
- Treat tree apparent size separately from raw image size.

PHASE 7 — Cross-reference to hardware twin
- If software declares hardware, check hardware database before accepting it.
- If hardware is absent but software declares it, classify as BSP_GHOST,
  DECLARED_ONLY, LOADER_REQUIRED, or OPEN_LEAD depending on evidence.

PHASE 8 — Final section composition
- Write the section as a standalone database section.
- Use clean hardware-database style and formatting.
- Include authority block, scope, rules, verified evidence, tables, conclusions,
  negative routes, and cross-references.
- Avoid narrative fluff.
- Include exact command outputs or distilled evidence where useful.

PHASE 9 — Closure gate
- Every item in the section scope must be closed, moved, or marked OPEN_LEAD.
- No unclassified claims.
- No stale terminology.
- No unresolved contradiction hidden in prose.
- Section status becomes COMPLETE only after Beright acceptance.

################################################################################
# 6 — COMMAND DISCIPLINE
################################################################################

The C17S is not a normal generic Android target. Do not throw normal Android
helper scripts at it and assume the result is meaningful.

Rules: (VERY STRICT)

1. Use small command batches.
   Good:
     adb shell 'su -c "cat /proc/mounts"'
     adb shell 'su -c "getprop ro.hardware"'

2. Avoid large blind scripts.
   Bad:
     huge all-in-one collection scripts that make failures hard to isolate.

3. Prefer direct commands over complex remote shell logic.
   Good:
     adb shell 'su -c "echo p1; cat /sys/block/mmcblk0/mmcblk0p1/start"'

4. If loops are used, prefer host-expanded loops or fully escaped variables.
   The remote shell and local shell can eat $variables before su runs.

5. Avoid unquoted regex pipes inside remote su.
   Bad:
     adb shell 'su -c grep -nE "a|b|c" /file'
     This can fail if the pipe characters are interpreted by the wrong shell.

   Safer:
     adb shell 'su -c "cat /vendor/etc/fstab.mt6762"' | grep -nE 'a|b|c'

6. Capture failures as evidence.
   If a command fails, do not silently skip it.
   Record whether the failure proves absence, permission boundary, missing tool,
   wrong namespace, or bad command syntax.

7. Do not use destructive commands.
   Forbidden unless explicitly requested and understood:
   - dd writes
   - format
   - mount -o rw,remount
   - rm/rmdir on device
   - chmod/chown on device
   - setprop changes
   - service stop/start unless approved
   - flashing commands

8. Do not treat tool availability as device truth.
   Example: dmsetup missing does not mean device-mapper is absent.
   It means dmsetup is absent. Use dmctl, /dev/block/mapper, /proc/partitions,
   and lpdump if available.

9. Use exact namespace paths.
   /dev/block/by-name is not always identical to
   /dev/block/platform/bootdevice/by-name.

10. Keep command output tied to section purpose.
    Do not collect unrelated logs unless the section requires them.

################################################################################
# 7 — COMMAND TEMPLATE STYLE
################################################################################

Use commands like this in chat so Beright can paste them safely.
We walk through the section to understand the device as well as 
populate the section.

Single direct live command:
```bash
adb shell 'su -c "getprop ro.hardware"'
```

Multiple small direct commands:
```bash
adb shell 'su -c "getprop ro.product.device"'
adb shell 'su -c "getprop ro.board.platform"'
adb shell 'su -c "getprop ro.hardware"'
```

Safe grep of remote file using host-side grep:
```bash
adb shell 'su -c "cat /vendor/etc/fstab.mt6762"' | grep -nE 'md_udc|metadata|userdata'
```

Partition sysfs verification, direct form:
```bash
adb shell 'su -c "echo p1; cat /sys/block/mmcblk0/mmcblk0p1/start; cat /sys/block/mmcblk0/mmcblk0p1/size; cat /sys/block/mmcblk0/mmcblk0p1/uevent"'
```

Host clean-pull verification:
```bash
cd ~/Downloads/LOKMAT_5_MAX_C17S_RESEARCH_DRIVE/LOKMAT_C17S_Forensics/super_unpacked
find system -type f | wc -l
find vendor -type f | wc -l
find product -type f | wc -l
sha256sum system.img vendor.img product.img
```

################################################################################
# 8 — REQUIRED SECTION FORMAT
################################################################################

Use the same clean style as the expanded hardware database and the completed
Section 00.

Each final section should generally follow this shape:

################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION XX — SECTION TITLE
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL FINAL SECTION XX — SHORT STATUS
# Date        : live-verification integration pass
################################################################################

################################################################################
# SCOPE
################################################################################

State exactly what the section covers and what it does not cover.

################################################################################
# SOURCE AUTHORITY
################################################################################

List the sources actually used for this section:
- live device commands
- clean partition paths
- static-analysis tools
- hardware DB sections
- prior software sections

################################################################################
# RULES
################################################################################

List section-specific rules.
Example for fstab/mounts:
- fstab declaration is not mount truth.
- partition name is not active mount source.

################################################################################
# X.1 — FIRST SUBSECTION
################################################################################

Use aligned key/value fields where possible:
Field name                    : value                                  [STATE]

Use tables for large inventories.
Use exact paths.
Use exact hashes where relevant.
Use exact version strings.
Use exact command output when necessary.

################################################################################
# X.N — SECTION CONCLUSION
################################################################################

Close with:
- What is active.
- What is required.
- What is declared only.
- What is ghost/dead residue.
- What is moved to another section.
- What remains open.

################################################################################
# SECTION XX CLOSURE STATUS
################################################################################

Scope item                    : CLOSED / MOVED / OPEN_LEAD / NEEDS_FUTURE_RE
Final section status           : COMPLETE only after Beright acceptance

################################################################################
# END SECTION XX
################################################################################

################################################################################
# 9 — CLASSIFICATION VOCABULARY
################################################################################

[VERIFIED]
A fact proven by live output, clean pull, hash, binary analysis, or cited source.

[ACTIVE_RUNTIME]
Present and participating in the current live runtime.
Examples: mounted filesystem, running service, registered HAL, live sysfs node
with active driver, binder service.

[FILE_AUTHORITY]
Proven from clean extracted system/vendor/product tree or raw image.

[DECLARED]
Present in config, fstab, manifest, XML, RC, properties, APK manifest, or code.
This does not prove runtime use.

[RAW_PARTITION]
An addressable block partition. It may not be mounted as a filesystem.

[DYNAMIC_CONTAINER]
A raw partition used as dynamic partition backing, such as super.

[REQUIRED]
Needed for correct boot, HAL registration, decryption, radio, camera, sensors,
audio, connectivity, or other verified behavior.

[LOADER_REQUIRED]
Possibly not physical hardware, but must be carried because a loader/HAL/service
expects it and fails without it.
Example class: camera scaffold/placeholder blobs.

[REPLACE]
Function required, but vendor implementation should be replaced by clean AOSP or
clean custom implementation.

[STRIP]
Remove from clean ROM because it is spyware, FOTA risk, factory backdoor,
unneeded ODM control surface, dead package, or unsafe framework patch.

[BSP_GHOST]
Declared by the MediaTek/ODM BSP but absent from actual live hardware/runtime.

[NEGATIVE]
Confirmed absent or not physically present after evidence review.

[OPEN_LEAD]
Not proven active, not proven absent, and requires later reverse engineering or
additional capture. Must not be promoted to truth.

[CONFLICT_RESOLVED]
Former contradiction resolved by layer split, stale-value correction, or direct
live evidence.

[MOVED_TO_SECTION_XX]
Relevant, but belongs in a later section.

################################################################################
# 10 — CONTRADICTION HANDLING
################################################################################

Do not erase contradictions. Resolve them.

If two values differ, ask:
1. Are they from different layers?
   Example: physical SoC MT6762V/WB versus software BSP MT6765.
   If yes, both are true and must be preserved as layer-specific.

2. Are they from different namespaces?
   Example: /dev/block/by-name has 42 symlinks, while
   /dev/block/platform/bootdevice/by-name has 40 symlinks.
   If yes, both are true and must be documented separately.

3. Are they capture-variable values?
   Example: bootreason, bootprof timings, process IDs, mount timestamps.
   If yes, preserve the capture but do not treat it as immutable.

4. Is one value stale?
   If a later live capture or corrected closed doc supersedes an older claim,
   mark the old value corrected and explain why.

5. Is one value file-source and another runtime-source?
   Example: fstab says /system, but /proc/mounts shows dm-1 mounted at /.
   Preserve declaration and runtime resolution separately.

################################################################################
# 11 — ACTIVE VS LEFTOVER DECISION MODEL
################################################################################

The entire software database expansion exists to answer this question:

What is actually used, and what is only leftover?

Use this ladder:

1. FILE PRESENT
   The file exists in clean pull. This proves presence only.

2. DECLARED
   The file/path/service/HAL/partition is referenced by config/manifest/RC/fstab.
   This proves declaration only.

3. LOADABLE
   The binary/library/blob can be loaded or appears in dependency output.
   This proves potential use, not active use.

4. LOADER REQUIRED
   Removing it breaks a verified loader/service/HAL path.
   This proves build/runtime requirement even if physical hardware is absent.

5. ACTIVE_RUNTIME
   The item is mounted, running, registered, opened, mapped, serving binder/HIDL,
   producing sysfs output, or otherwise active in live runtime.

6. PHYSICAL CONFIRMED
   Hardware database proves a physical endpoint.

7. BSP_GHOST / NEGATIVE
   Declared in software but absent from live runtime and absent from hardware.

Do not skip levels.

################################################################################
# 12 — SECTION-SPECIFIC SOURCE PRIORITIES
################################################################################

Section 00 — Document Control, Partition Architecture & Source Authority
- Live: /proc/partitions, /sys/block, /dev/block, /proc/mounts, getprop, lpdump.
- Host: super_unpacked counts, image hashes.
- Hardware DB: storage geometry and physical eMMC identity.

Section 01 — Init System, RC Files & Service Graph
- Clean pull: init*.rc, /vendor/etc/init, /system/etc/init, /product/etc/init.
- Live: init.svc.*, getprop triggers, ps, boot timing when needed.
- Classify services: active, disabled, oneshot, ghost, ODM, critical.

Section 02 — HAL Layer, HIDL/VINTF & Interface Inventory
- Clean pull: /vendor/etc/vintf/manifest.xml, compatibility matrices, hwservice
  declarations, vendor/bin/hw, vendor/lib*/hw.
- Live: lshal, service list, hwservicemanager state, ps.
- Cross-check every HAL with physical hardware section.

Section 03 — Cryptographic Stack, Library Chain & Namespace Isolation
- Clean pull: keymaster/gatekeeper binaries, libcrypto/libssl/libkeymaster,
  ld.config, VNDK, ODM crypto libs.
- Live: process maps, vold/keymaster/gatekeeper state, FBE mount behavior.
- Preserve TWRP-only isolation as TWRP-only; do not impose it on normal ROM
  unless evidence requires.

Section 04 — Sensor Stack, SCP, nRF Bridge & SensorService
- Clean pull: sensor HAL, Wiite bridge binaries, SCP overlays, sensor XML/libs.
- Live: dumpsys sensorservice, sysfs wiite_corp_ctrl, ttyS1, I2C nodes, ps.
- Hardware DB: nRF52832, PAR2822, absent sensors.

Section 05 — Display, GPU & Graphics Stack
- Clean pull: gralloc, hwcomposer, EGL/GLES/Vulkan/OpenCL blobs, egl.cfg,
  SurfaceFlinger configs.
- Live: dumpsys SurfaceFlinger, GPU nodes, loaded modules, properties.
- Hardware DB: ST7701S, PowerVR GE8320, negative Mali residue.

Section 06 — Camera Stack
- Clean pull: camera HAL, sensor libs, EEPROM/PDAF/CCU blobs, camera configs.
- Live: dumpsys media.camera, camera provider service, I2C nodes, logs.
- Hardware DB: GC2385, GC030A, BF2553L loader trap/open lead, SENINF.

Section 07 — Audio Stack
- Clean pull: audio policy XML, audio HAL, soundfx, audio_param files,
  Aurisys configs.
- Live: dumpsys media.audio_flinger, audio_policy, properties, route state.
- Hardware DB: speaker/mic, absent earpiece/headset/HDMI routes.

Section 08 — Radio, Telephony & Modem Stack
- Clean pull: modem firmware, RIL libs, IMS/VoLTE daemons, APDB/APN configs.
- Live: service list, ps, getprop gsm/ril/vendor, ccci nodes, network iface.
- Hardware DB: baseband/RF/antenna/SIM truth.

Section 09 — Connectivity Stack
- Clean pull: WMT, Wi-Fi, BT, GNSS firmware/configs/daemons/modules.
- Live: modules, /dev nodes, ps, getprop, dmesg/logcat where needed.
- Hardware DB: WMT/connsys, GNSS, BT/Wi-Fi physical truth.

Section 10 — VNDK and Vendor Blob Inventory
- Clean pull: full vendor tree, VNDK/VNDK-SP libs, dependency maps.
- Tools: readelf, strings, Ghidra, sha256sum.
- Classify required/optional/ghost/ODM-modified.

Section 11 — Firmware Blob Catalog
- Clean pull: /vendor/firmware, /system/firmware, calibration files.
- Tools: sha256sum, strings, file, xxd.
- Hardware DB: map every firmware blob to physical owner or classify ghost.

Section 12 — APK Inventory
- Clean pull/live: pm list packages, APK paths, dumpsys package, manifests.
- Tools: apktool/JADX for suspicious packages.
- Classify KEEP/STRIP/REPLACE/ANALYZE for all 127 packages.

Section 13 — ODM Control Layer
- Clean pull: Wiite framework, DeviceDaemon, WiiteCommon, health apps, bridge.
- Live: sysfs wiite_corp_ctrl, wiite_con, ttyS1, process/service state.
- Hardware DB: nRF52832 control plane.

Section 14 — SELinux Policy Map
- Clean pull: precompiled_sepolicy, file_contexts, service_contexts,
  hwservice_contexts, seapp_contexts.
- Live: getenforce, AVC denials, labels, ps -Z, ls -Z.
- Goal: minimum clean policy, not ODM permissive replication.

Section 15 — Build Specification
- Consume completed Sections 00-14 and hardware Section 1.
- Build constants must trace back to verified evidence.
- No GMS spoof or ODM risk components in clean base unless explicitly required.

Section 16 — Security & Threat Analysis
- Clean pull: suspicious APKs, framework jars, pms grants, FOTA, Wiite hooks.
- Tools: JADX/apktool/Ghidra/strings/network endpoint extraction.
- Claims must separate capability, behavior, intent, and proof.

Section 17 — Software Negative Register
- Consolidates all declared-but-absent software routes.
- Must cross-reference hardware Section 12.
- Prevents BSP residue from being carried into clean ROM by accident.

Section 18 — ROM Build Gate & Verification Checklist
- Consumes all sections.
- Nothing builds until all required gates close.
- Tracks pre-build and post-build verification.

################################################################################
# 13 — COMPLETION MATRIX REQUIREMENT
################################################################################

Before writing a final section, produce or internally maintain a completion
matrix like this:

Scope item                    : CLOSED / OPEN / MOVED / NEEDS_VERIFICATION
Evidence source               : live / clean pull / binary / hardware DB / prior section
Classification                : VERIFIED / ACTIVE / DECLARED / GHOST / OPEN_LEAD
Action                        : keep / correct / expand / move / remove

No final section should be produced until every scope item is represented in the
matrix.

################################################################################
# 14 — WRITING STYLE REQUIREMENTS
################################################################################

Use the expanded hardware database style:
- Large ################################################################################ separators.
- Clear section/subsection numbering.
- Aligned key/value fields.
- Exact file paths.
- Exact hashes.
- Exact property names.
- Exact package names.
- Exact service names.
- Exact partition names and block devices.
- Exact command outputs where they are central evidence.

Avoid:
- Casual prose.
- Marketing language.
- Overclaiming.
- “Probably,” unless marked OPEN_LEAD.
- “Standard Android,” unless verified on this device.
- “Looks like,” unless followed by an evidence class.
- Generic MediaTek assumptions.

When explaining a finding, use this pattern:

Evidence:
  What was observed.

Interpretation:
  What the evidence proves.

Boundary:
  What it does not prove.

ROM/database consequence:
  KEEP, STRIP, REPLACE, REQUIRED, GHOST, or OPEN_LEAD.

################################################################################
# 15 — COMMON PITFALLS FROM SECTION 00
################################################################################

1. Namespace collapse
   /dev/block/by-name and /dev/block/platform/bootdevice/by-name were different.
   Do not collapse paths that look similar.

2. Size-unit confusion
   /sys/block/*/size is sectors, usually 512-byte sectors.
   /proc/partitions size is 1K blocks.
   lpdump extents are sectors.
   du -sb is host tree apparent bytes.
   Raw image size is raw file bytes.

3. Mount declaration versus runtime mount
   fstab declared system /system.
   Runtime mounted dm-1 at /.
   Both are true at different layers.

4. Partition name trap
   metadata exists as p11.
   Active /metadata mount source is md_udc p10.
   Do not infer from name.

5. Ghost entry trap
   fstab entries md1dsp, md1arm7, md3img, odmdtbo are declared but absent from
   live partition truth.

6. Capture-variable trap
   bootreason and bootprof timing values can change between boots.
   Preserve exact capture, but do not make them immutable constants.

7. Software declaration trap
   A config route for SD/USB/CD-ROM does not prove physical storage exists or is
   mounted.

################################################################################
# 16 — FINAL OUTPUT REQUIREMENT
################################################################################

For each completed section, produce:

1. A standalone markdown file:
   C17S_SOFTWARE_SECTIONXX_FINAL.md

2. The section must include:
   - scope
   - source authority
   - rules
   - verified evidence
   - complete inventories/tables within scope
   - active/runtime classification
   - declared-only classification
   - ghost/negative/open-lead classification
   - cross-references
   - closure status

3. Include file metadata in the chat response:
   - filename
   - line count
   - byte size
   - SHA256

4. The final answer must include a download link to the generated file.

################################################################################
# 17 — AGENT HANDOFF SUMMARY
################################################################################

You are helping expand the C17S Software Digital Twin for the LOKMAT APPLLP 5 MAX.
This is a forensic engineering database, not a general explanation or ROM guide.
The user has already completed a deeply expanded hardware database and a final
Section 00-03 of the software database. Continue in the same style.

Do not assume normal Android behavior. This device is a cross-layer MT6762V/WB
physical silicon platform running an MT6765 software BSP/HAL stack, with ODM
Wiite/Topwise/Linswear modifications, system-UID packages, factory/control
surfaces, BSP ghosts, and a dual-chip sensor/control plane.

Read the section directory, read the original software database section, read the
relevant hardware section, create a delta ledger, collect only missing evidence,
then write a canonical final section.

The central question for every section is:

WHAT IS ACTUALLY USED, AND WHAT IS ONLY LEFTOVER?

################################################################################

# 18 — ROM LANGUAGE CONTAINMENT RULE

################################################################################

This software digital twin is not a ROM build document.

ROM/build relevance may appear only in one small bounded note near the end of a
section, titled:

ROM / BUILD RELEVANCE NOTE

That note must be short and must only summarize why the database finding matters
for later bring-up. The body of the section must remain a software evidence
database.

Do not turn section findings into:

* build instructions
* BoardConfig planning
* device tree planning
* implementation strategy
* patch plans
* feature wish lists
* speculative replacement architecture

Those belong in Section 15, Section 18, or the separate ROM build document set.

Each software section must answer:

What exists?
What runs?
What is declared?
What owns it?
What opens it?
What depends on it?
What is required?
What is ghost residue?
What remains open?

Not:

How do we build the ROM?

################################################################################

# 19 — STRICT LIVE COMMAND SIZE LIMIT

################################################################################

Live commands must be targeted and small.

Default command rule:

* One command should answer one evidence question.
* A command may contain a few directly related reads from the same subsystem.
* Do not combine unrelated subsystems into one large capture.
* Do not run broad sweeps unless Beright explicitly requests a broad sweep.
* Do not hide many checks inside one remote shell script.
* Prefer multiple small pasteable commands over one long command.

Acceptable:

* Check one sysfs control surface.
* Check one process owner.
* Check one device node owner.
* Check one service state.
* Check one HAL registration.
* Check one package path.
* Check one targeted log transition.

Not acceptable by default:

* all-in-one runtime dumps
* broad process/property/sysfs/log/radio sweeps
* giant loops across unrelated subsystems
* commands that make failures hard to isolate
* commands that collect data outside the current section scope

If a broad capture is genuinely useful, explain why and ask before giving it.

################################################################################

# 20 — SESSION START PROMPT TEMPLATE

################################################################################

Use this prompt at the start of a new chat or new section:

You are working on the C17S Software Digital Twin for the LOKMAT APPLLP 5 MAX.
This is a forensic software database, not a ROM build guide.

Before writing anything:

1. Read C17S_SOFTWARE_DATABASE_EXPANSION_WORKFLOW.md.
2. Read C17S_SOFTWARE_SECTIONS_DIRECTORY.md.
3. Identify the exact target section number and title.
4. Read the matching original section in C17S_SOFTWARE_Forensics.md.
5. Read relevant completed software sections.
6. Read relevant hardware database sections.
7. Create a delta ledger:
   KEEP / CORRECT / EXPAND / DEMOTE / MOVE / OPEN_LEAD / NEEDS_VERIFICATION.
8. Ask only for small direct commands that close real evidence gaps.
9. Do not use broad scripts unless I explicitly approve them.
10. Keep ROM/build language out of the section body. Only one small ROM/build
    relevance note may appear at the end.

The section must be written as a standalone database-quality document with exact
paths, exact services, exact packages, exact hashes where relevant, evidence
classification tags, negative/ghost/open-lead handling, and closure status.

Do not assume generic Android or generic MediaTek behavior. Use only C17S
evidence.


################################################################################
# END C17S SOFTWARE DATABASE EXPANSION WORKFLOW
################################################################################
