################################################################################
# ANDROID HARDWARE FORENSIC DATABASE STANDARD
# Scope: universal subsection/section structure for Android hardware digital-twin databases
# Purpose: make every hardware section look, read, classify, and close the same way
# Applicability: any Android-derived device, any SoC vendor, any Android version,
#                any partition scheme, any hardware population state
################################################################################

################################################################################
# 0 — CONTROLLING RULE
################################################################################

Every hardware database subsection shall use the same structural skeleton.

The subsection topic changes.
The evidence class changes.
The tables used inside evidence blocks change.
The order, control fields, classification language, closure logic, and style do not change.

A subsection is not a narrative report.
A subsection is not a ROM plan.
A subsection is not a build document.
A subsection is not a cleanup list.
A subsection is a closed or open evidence container.

The database records only:
- what physically exists
- what is declared
- what is populated on disk
- what is exposed by kernel/devicetree/firmware
- what is mounted or mapped
- what is bound to a driver
- what is registered to Android
- what runs
- what depends on what
- what contradicts another witness
- what remains open and why

No subsection may change style because the evidence is complicated.

################################################################################
# 1 — DOCUMENT STYLE STANDARD
################################################################################

1.1 Fixed visual style

Use only these structural forms:

################################################################################
# N.N — SUBSECTION TITLE
################################################################################

BLOCK TITLE:
Aligned key/value rows.

| Tables | For repeated records only |
|---|---|

Do not mix unrelated heading systems inside the same database:
- Do not use Markdown "##" subsection headings inside a subsection.
- Do not use prose mini-essays.
- Do not use bullet explanations where rows or tables are possible.
- Do not use code fences for normal database rows.
- Use raw capture blocks only for byte-exact evidence that cannot be represented safely in a table.

1.2 Key/value row alignment

All scalar fields use aligned labels.

Required form:
Field name                     : exact value
Field name source              : exact witness/source
Field classification           : CLASSIFICATION_TOKEN

1.3 Table rule

Use tables only for repeated records where a key/value list would lose alignment or create duplication.

Every table row must include a classification column unless the table is a pure byte/field decode table that is immediately covered by a classification row below it.

1.4 Raw capture rule

Use raw capture blocks only when preserving byte-exact output matters.

Required form:

RAW_CAPTURE_START: CAPTURE_ID
source                         : exact source path/object
capture method                 : exact method class, not a shell transcript unless the command is part of the evidence
payload:
<verbatim bytes/text>
RAW_CAPTURE_END: CAPTURE_ID

Raw captures do not replace structured records. They support structured records.

################################################################################
# 2 — UNIVERSAL SECTION STRUCTURE
################################################################################

Every hardware database section shall use this top-level order.

################################################################################
# SECTION N — SECTION TITLE
################################################################################

SECTION CONTROL:
Section ID                     : N
Section title                  : exact title
Database type                  : HARDWARE_DIGITAL_TWIN
Device scope                   : exact device/project scope
Section status                 : SECTION_OPEN / SECTION_CLOSED_CURRENT_PASS / SECTION_CLOSED_FINAL
Last verification pass         : exact date/time or NOT_RECORDED
Primary evidence class         : LIVE / RAW_IMAGE / EXTRACTED_TREE / STATIC_BINARY / LOG / MIXED
Primary closure authority      : exact authority model

SECTION SCOPE:
Included scope                 : concise list of what belongs here
Excluded scope                 : concise list of what does not belong here
Cross-section dependencies     : section IDs only, no prose expansion

SOURCE AUTHORITY:
Tier 1                         : source class and exact authority meaning
Tier 2                         : source class and exact authority meaning
Tier 3                         : source class and exact authority meaning
Tier 4                         : source class and exact authority meaning
Final acceptance               : named final acceptance rule if applicable

CAPTURE BOUNDARY:
Live state                     : stock / rooted / unlocked / recovery / GSI / unknown
Research-state changes         : exact known changes, or NONE_RECORDED
Stock metadata authority       : exact source class for stock file metadata
Runtime authority              : exact source class for runtime behavior
Boundary rule                  : live runtime data is not stock metadata unless proven

SECTION CLASSIFICATION VOCABULARY:
Use the universal classification vocabulary in Section 6 of this standard.
Add section-local tokens only if the universal vocabulary cannot represent a required state.
Every section-local token must be defined before first use.

SUBSECTION MAP:
| Subsection | Title | Status | Primary evidence class | Closure state |
|---|---|---|---|---|

Then write each subsection using the universal subsection skeleton in Section 3.

SECTION CLOSURE SUMMARY:
Canonical closed facts          : table or aligned key/value rows
Open boundaries carried forward : table
Section status                  : exact final section status

################################################################################
# 3 — UNIVERSAL SUBSECTION SKELETON
################################################################################

Every subsection shall use this exact block order.

################################################################################
# N.N — SUBSECTION TITLE
################################################################################

SUBSECTION CONTROL:
Section ID                     : N
Subsection ID                  : N.N
Subsection title               : exact title
Database type                  : HARDWARE_DIGITAL_TWIN
Record domain                  : one or more tokens from Section 5
Primary object class           : physical_component / controller / bus / register_block / firmware_domain / storage_object / kernel_binding / runtime_binding / mixed
Status                         : SUBSECTION_OPEN / SUBSECTION_BLOCKED / SUBSECTION_CLOSED_CURRENT_PASS / SUBSECTION_CLOSED_FINAL
Closure target                 : what this subsection must prove to close

SCOPE:
Included                       : exact evidence and object classes included here
Excluded                       : exact evidence and object classes excluded here
Cross-section ownership         : section IDs that own out-of-scope records
Comment allowance              : NONE / ANOMALY_ONLY / SCOPE_ONLY

WITNESS AUTHORITY MAP:
| Witness ID | Witness class | Authority meaning | Exact source object/path | Capture state | Notes |
|---|---|---|---|---|---|

CAPTURE STATE BOUNDARY:
Live capture state             : exact state
Stock file metadata authority  : exact source or NOT_IN_SCOPE
Runtime behavior authority     : exact source or NOT_IN_SCOPE
Raw-image authority            : exact source or NOT_IN_SCOPE
Extraction authority           : exact source or NOT_IN_SCOPE
Boundary classification         : exact classification token

EVIDENCE GATE STATUS:
Command/input clean             : YES / NO / NOT_APPLICABLE
Output complete                 : YES / NO / NOT_APPLICABLE
Destination/file valid          : YES / NO / NOT_APPLICABLE
Witness agreement state         : AGREES / DISAGREES / SINGLE_WITNESS / NOT_TESTED
Contradiction state             : NONE / PRESENT / RESOLVED / OPEN
Closure permitted               : YES / NO

CANONICAL RESOLVED FACTS:
| Fact ID | Fact key | Canonical value | Canonical layer | Primary witness | Classification | Notes |
|---|---|---|---|---|---|---|

LAYERED WITNESS RECORDS:
| Record ID | Fact key | Witness ID | Layer | Source object/path | Raw value | Normalized value | Classification | Notes |
|---|---|---|---|---|---|---|---|---|

DECLARATION / POPULATION / ACTIVATION MATRIX:
| Object ID | Object name/path | Declared by | Populated where | Runtime active evidence | Physical evidence | Classification | Notes |
|---|---|---|---|---|---|---|---|

STATIC ARTIFACT INVENTORY:
| Artifact ID | Path/object | Type | Mode | UID:GID | SELinux context | Size bytes | Symlink target | Witness ID | Classification | Notes |
|---|---|---|---|---|---|---:|---|---|---|---|---|

RUNTIME BINDING / OPERATIONAL STATE:
| Runtime ID | Runtime object | Binding type | Driver/service/process/HAL | State | UID/domain/context | Source | Classification | Notes |
|---|---|---|---|---|---|---|---|---|

REGISTER / BUS / TOPOLOGY RECORDS:
| Topology ID | Object | Address/bus/index | Size/range | Parent | Child/endpoint | Driver/binding | Source | Classification | Notes |
|---|---|---|---|---|---|---|---|---|---|

DEPENDENCY / CONSUMER MAP:
| Dependency ID | Provider object | Consumer object | Interface | Evidence source | Runtime proven | Classification | Notes |
|---|---|---|---|---|---|---|---|

ANOMALY / CONTRADICTION REGISTER:
| Anomaly ID | Label | Evidence A | Evidence B | Exact discrepancy | Status | Owner section | Classification | Required resolution |
|---|---|---|---|---|---|---|---|---|

CROSS-SECTION TRANSFER REGISTER:
| Transfer ID | Object/fact | Current subsection handling | Owning section | Reason for transfer | Classification |
|---|---|---|---|---|---|

OPEN BOUNDARIES:
| Boundary ID | Boundary label | What is known | What is not known | Why it remains open | Required evidence to close | Classification |
|---|---|---|---|---|---|---|

FINAL SUBSECTION CLASSIFICATION:
Canonical conclusion           : one-line factual conclusion only
Closure state                  : exact status token
Open boundary count            : integer
Contradiction count            : integer
Transferred item count         : integer
Database action                : RETAIN / REPLACE_PRIOR_VALUE / ADD_NEW_RECORDS / BLOCK_CLOSURE

################################################################################
# 4 — REQUIRED BLOCK BEHAVIOR
################################################################################

4.1 SUBSECTION CONTROL

This block prevents a subsection from becoming a different document style.

Required:
- section ID
- subsection ID
- title
- database type
- record domain
- primary object class
- status
- closure target

Forbidden:
- project motivation prose
- ROM goals
- build recommendations
- security opinions
- generic summary language

4.2 SCOPE

SCOPE is the only place where a short paragraph-like sentence is allowed.

The scope must define:
- what belongs here
- what is explicitly excluded
- which other section owns excluded material

The subsection may not later expand outside its own scope unless a cross-section transfer row is created.

4.3 WITNESS AUTHORITY MAP

Every subsection must name its witnesses before using them.

Universal witness classes:
LIVE_DEVICE
RAW_PARTITION_IMAGE
RAW_BOOT_IMAGE
RAW_DTB_DTBO
EXTRACTED_FILESYSTEM_TREE
STATIC_FILE
STATIC_BINARY_DECODE
KERNEL_LOG
BOOTLOADER_LOG
FIRMWARE_LOG
ANDROID_PROPERTY
ANDROID_SERVICE_REGISTRY
ANDROID_PACKAGE_REGISTRY
VINTF_MANIFEST
KERNEL_CONFIG
KERNEL_PROCFS
KERNEL_SYSFS
DEVICE_TREE
DEBUGFS
BLOCK_LAYER
EXTERNAL_TEST_RESULT

4.4 CAPTURE STATE BOUNDARY

Every subsection must state whether live data is stock truth, runtime truth, or research-state truth.

Required distinction:
- runtime presentation
- stock file metadata
- raw image inode/bytes
- extracted content convenience
- current rooted/Magisk/permissive/recovery/GSI state

4.5 EVIDENCE GATE STATUS

This block prevents dirty evidence from being written as closure.

If any field is NO:
- Closure permitted must be NO.
- The failed capture must be recorded as invalid or open.
- No final fact may depend on that capture.

4.6 CANONICAL RESOLVED FACTS

Only facts that survive witness classification belong here.

Every row must include:
- Fact ID
- Fact key
- canonical value
- canonical layer
- primary witness
- classification
- notes

Canonical value does not mean single value.
Layered devices may have multiple canonical values if each belongs to a different layer.

4.7 LAYERED WITNESS RECORDS

This is where conflicting values are preserved.

Do not collapse:
- physical SoC vs BSP platform
- devicetree model vs compatible
- live runtime vs raw image
- declaration vs activation
- symlink object vs dereferenced target
- size vs apparent size
- file presence vs service use
- HAL declaration vs hardware population
- stock image state vs rooted live state

4.8 DECLARATION / POPULATION / ACTIVATION MATRIX

This matrix is mandatory for all Android hardware work because Android often declares hardware that is not populated or active.

Definitions:
Declared                       : exists in DT, fstab, VINTF, XML, property, init, rc, config, manifest, feature XML, kernel config, or source file
Populated                      : physical component, file artifact, partition, node, firmware, binary, or module exists
Activated                      : runtime binding, mounted state, loaded module, running service, active HAL, open device node, traffic, interrupt, or operational behavior is proven
Physical evidence              : silicon ID, bus probe, register block, schematic/PCB observation, log probe, sensor response, electrical test, or equivalent hardware evidence

No row may promote declared to populated.
No row may promote populated to active.
No row may promote active software route to physical hardware.

4.9 STATIC ARTIFACT INVENTORY

Use for:
- firmware blobs
- kernel modules
- HAL libraries
- init files
- property files
- config files
- APKs when the hardware section needs only hardware-facing APK evidence
- binary dumps
- DTB/DTBO images
- partition images

Required metadata where applicable:
- path/object
- type
- mode
- UID:GID
- SELinux context
- size bytes
- symlink target
- witness ID
- classification

If metadata is unavailable, record why:
- NOT_EXPOSED_BY_SOURCE
- NOT_CAPTURED_THIS_PASS
- INVALID_CAPTURE
- NOT_APPLICABLE_TO_OBJECT_CLASS

4.10 RUNTIME BINDING / OPERATIONAL STATE

Use for:
- loaded kernel modules
- platform driver binding
- I2C/SPI/USB/PCI/SDIO/MMC device binding
- HAL registration
- service process
- interrupt activity
- active mount
- active block mapping
- active power supply
- active sensor stream
- active radio/modem state
- runtime firmware request

Every row must separate:
- object exists
- object is bound
- object is active
- object is merely declared

4.11 REGISTER / BUS / TOPOLOGY RECORDS

Use for:
- memory-mapped registers
- devicetree reg cells
- /proc/iomem spans
- bus numbers and addresses
- GPIO/pinctrl
- interrupts
- clocks
- regulators
- power domains
- firmware domains
- reserved memory
- CPU topology
- display pipeline
- camera pipeline
- modem/shared-memory regions

Numeric decode rule:
- raw bytes must be preserved when byte order matters
- decoded value must state endian/units
- unknown unit must be marked UNIT_OPEN
- unknown field semantics must be marked STRUCT_OPEN

4.12 DEPENDENCY / CONSUMER MAP

Use for:
- firmware consumed by module/driver
- HAL consumed by framework
- kernel node consumed by daemon
- regulator consumed by peripheral
- clock/reset/pinctrl dependency
- partition consumed by firmware loader
- shared memory consumed by modem/TEE/SCP/etc.
- sysfs/proc node consumed by APK/service

4.13 ANOMALY / CONTRADICTION REGISTER

Every contradiction stays.

Contradiction examples:
- same path, different bytes
- same file name/size, different hash
- declaration with no physical object
- physical object with no Android declaration
- driver bound to unexpected node
- kernel config says not set, symbols prove built-in
- devicetree says one panel, bootloader atag says another
- property layer says one platform, silicon register says another
- live metadata differs from stock image metadata
- output truncates at kernel/sysfs page boundary

An anomaly row must include:
- exact discrepancy
- both evidence sources
- status
- owner section
- required evidence to close

4.14 CROSS-SECTION TRANSFER REGISTER

Use this to prevent scope creep while preserving leads.

A transferred fact is not deleted.
It is retained as a cross-reference with an owner section.

4.15 OPEN BOUNDARIES

Open boundaries are not failures.

They are controlled unresolved states.

Required:
- known facts
- unknown part
- why it remains open
- exact evidence needed to close
- classification

Forbidden:
- vague "needs investigation"
- generic "check later"
- unresolved facts buried in comments

4.16 FINAL SUBSECTION CLASSIFICATION

This block must be short and structured.

Allowed:
- canonical conclusion
- closure state
- counts
- database action

Forbidden:
- persuasive language
- security narrative
- build plan
- cleanup recommendation
- generalized lessons

################################################################################
# 5 — UNIVERSAL RECORD DOMAINS
################################################################################

Use these tokens in SUBSECTION CONTROL / Record domain.

IDENTITY_DOMAIN                : brand/model/board/product/build/platform identity
SILICON_DOMAIN                 : SoC, chip ID, die/fuse/security identity
CPU_DOMAIN                     : CPU architecture/topology/PSCI/DVFS identity
GPU_DISPLAY_DOMAIN             : GPU, display controller, panel, graphics stack
MEMORY_DOMAIN                  : RAM, reserved memory, EMI/DRAMC, ZRAM, memory policy
STORAGE_DOMAIN                 : eMMC/UFS/NAND, GPT, dynamic partitions, block devices
BOOT_DOMAIN                    : boot image, bootloader, DTB/DTBO, boot args, initrd
SECURITY_DOMAIN                : AVB, TEE, RPMB, keymaster, gatekeeper, fuses, crypto
RADIO_DOMAIN                   : modem, RIL, IMS, SIM, RF, baseband, GNSS if coupled
CONNECTIVITY_DOMAIN            : Wi-Fi, BT, NFC, USB, Ethernet, WMT/Combo chips
SENSOR_DOMAIN                  : IMU, health sensors, coprocesors, sensor HALs
CAMERA_DOMAIN                  : sensors, ISP, CCU, flashlight, calibration
AUDIO_DOMAIN                   : codec, DSP, speaker, mic, routing
INPUT_DOMAIN                   : touch, keypad, keyboard, haptics, pen, gestures
POWER_THERMAL_DOMAIN           : PMIC, charger, battery, regulators, thermal zones
BUS_GPIO_PINCTRL_DOMAIN        : I2C, SPI, UART, SDIO, GPIO, pinctrl, interrupts
FIRMWARE_DOMAIN                : SCP, SSPM, ADSP, modem firmware, external MCU firmware
VENDOR_HAL_DOMAIN              : vendor HALs tied directly to hardware behavior
BSP_GHOST_DOMAIN               : declared/remnant hardware routes not populated/active
MIXED_DOMAIN                   : only when a subsection intentionally spans domains

################################################################################
# 6 — UNIVERSAL CLASSIFICATION VOCABULARY
################################################################################

6.1 Evidence state

VERIFIED                       : directly proven by clean evidence
REVERIFY_REQUIRED              : prior value exists but current pass did not reverify it
INVALID_CAPTURE                : command/output/file was malformed or incomplete
PARTIAL_CAPTURE                : evidence is usable only within stated boundary
SINGLE_WITNESS                 : only one valid witness exists
MULTI_WITNESS_CONFIRMED        : multiple independent witnesses agree
WITNESS_CONTRADICTION          : witnesses disagree and contradiction is open

6.2 Runtime/static state

LIVE_RUNTIME_AUTHORITY         : live system is authority for active behavior
RAW_IMAGE_AUTHORITY            : raw image is authority for stock bytes/metadata
EXTRACTED_TREE_CONVENIENCE     : extracted tree is convenience source only
STOCK_FILE_AUTHORITY           : stock-preserved file source is authority
STATIC_DECLARATION             : declaration exists but activity/population not proven
STATIC_FILE_PRESENT            : file exists but runtime use not proven
RUNTIME_ACTIVE                 : active behavior is proven
RUNTIME_INACTIVE               : runtime path exists but inactive at capture
PER_BOOT_VARIANT               : value changes per boot and is not fixed identity
RESEARCH_STATE_ONLY            : value belongs to modified/rooted/recovery/GSI state

6.3 Hardware population state

PHYSICAL_PRESENT               : physical hardware presence proven
PHYSICAL_ABSENT_IN_SCOPE       : absence proven within explicit search scope
POPULATED_ON_DISK              : file/firmware/blob/module exists
DECLARED_ONLY                  : declared but no population/activation proof
DECLARED_AND_POPULATED         : declaration and artifact/hardware population both proven
DECLARED_POPULATED_ACTIVE      : declaration, population, and runtime activation proven
POPULATED_NOT_DECLARED         : object exists without standard Android declaration
ACTIVE_WITHOUT_STATIC_SOURCE   : runtime active but static source not found
BSP_GHOST                      : BSP declaration/remnant without matching device reality
VARIANT_REMNANT                : leftover from another product/platform variant
VIRTUAL_OR_EMULATED            : software-exposed virtual hardware/object

6.4 Layering state

LAYER_SPLIT_CONFIRMED          : different values are correct in different layers
SOURCE_MECHANISM_OPEN          : value is real but origin mechanism not found
STRUCT_OPEN                    : raw structure captured but fields not decoded
UNIT_OPEN                      : value captured but units/scaling unknown
ENDIAN_DECLARED                : byte order explicitly stated
SYMLINK_OBJECT                 : symlink itself is the object
SYMLINK_DEREFERENCED_TARGET    : target content was measured, not symlink identity

6.5 Contradiction/anomaly state

CONTRADICTION_CONFIRMED        : discrepancy proven
CONTRADICTION_RESOLVED         : discrepancy resolved with correct layer/authority
ANOMALY_RETAINED               : unusual fact retained without forced explanation
OPEN_BOUNDARY                  : unresolved but controlled boundary
UNRESOLVABLE_FROM_LIVE         : live device cannot expose the missing value
REQUIRES_RAW_IMAGE             : closure requires raw partition/image
REQUIRES_BINARY_DECODE         : closure requires binary reverse engineering
REQUIRES_SOURCE_CODE           : closure requires source code not present
REQUIRES_PHYSICAL_INSPECTION   : closure requires board/electrical inspection

6.6 Closure state

SUBSECTION_OPEN
SUBSECTION_BLOCKED
SUBSECTION_CLOSED_CURRENT_PASS
SUBSECTION_CLOSED_FINAL
SECTION_OPEN
SECTION_CLOSED_CURRENT_PASS
SECTION_CLOSED_FINAL

################################################################################
# 7 — UNIVERSAL RECORD TABLE DEFINITIONS
################################################################################

7.1 Canonical fact table

Required columns:
| Fact ID | Fact key | Canonical value | Canonical layer | Primary witness | Classification | Notes |

Use this table in every subsection.

7.2 Layered witness table

Required columns:
| Record ID | Fact key | Witness ID | Layer | Source object/path | Raw value | Normalized value | Classification | Notes |

Use this table in every subsection.

7.3 Static artifact table

Required columns:
| Artifact ID | Path/object | Type | Mode | UID:GID | SELinux context | Size bytes | Symlink target | Witness ID | Classification | Notes |

Use only when static artifacts are in scope.
If static artifacts are not in scope, write:
Static artifact inventory      : NOT_APPLICABLE_BY_SCOPE

7.4 Runtime binding table

Required columns:
| Runtime ID | Runtime object | Binding type | Driver/service/process/HAL | State | UID/domain/context | Source | Classification | Notes |

Use only when runtime binding is in scope.
If runtime binding is not in scope, write:
Runtime binding inventory      : NOT_APPLICABLE_BY_SCOPE

7.5 Register/topology table

Required columns:
| Topology ID | Object | Address/bus/index | Size/range | Parent | Child/endpoint | Driver/binding | Source | Classification | Notes |

Use only when register, bus, or topology facts are in scope.
If not in scope, write:
Register/topology records      : NOT_APPLICABLE_BY_SCOPE

7.6 Declaration/population/activation matrix

Required columns:
| Object ID | Object name/path | Declared by | Populated where | Runtime active evidence | Physical evidence | Classification | Notes |

Use in every subsection where Android may declare hardware. That includes almost all Android hardware sections.

7.7 Anomaly register

Required columns:
| Anomaly ID | Label | Evidence A | Evidence B | Exact discrepancy | Status | Owner section | Classification | Required resolution |

Use in every subsection.
If none:
Anomaly register               : NONE_RECORDED_THIS_PASS

7.8 Open boundary table

Required columns:
| Boundary ID | Boundary label | What is known | What is not known | Why it remains open | Required evidence to close | Classification |

Use in every subsection.
If none:
Open boundaries                : NONE_RECORDED_THIS_PASS

################################################################################
# 8 — SECTION AND SUBSECTION CLOSURE RULES
################################################################################

8.1 A subsection may close only when all required gates are true.

Required gates:
- scope is declared
- witnesses are named
- capture state is declared
- all canonical facts have evidence rows
- all contradictions are either resolved or moved to open boundaries
- all transferred facts have owner sections
- all static artifacts in scope have metadata or explicit metadata boundary
- all runtime claims have runtime evidence
- no declared-only item is written as physical hardware
- no active software route is written as physical hardware
- no research-state value is written as stock truth
- no command/output/file capture is malformed

8.2 Closure labels

Use:
SUBSECTION_CLOSED_CURRENT_PASS
when the subsection is internally consistent for the current evidence set but may expand later.

Use:
SUBSECTION_CLOSED_FINAL
only when the project authority accepts there are no remaining in-scope open boundaries.

Use:
SUBSECTION_BLOCKED
when closure requires evidence not currently available.

Use:
SUBSECTION_OPEN
when work is still underway.

8.3 Contradiction closure

A contradiction is resolved only when one of these is true:
- the two values belong to different layers and both are retained
- one capture is proven invalid and marked invalid
- one source is proven non-authoritative for that field type
- a newer clean capture replaces a stale value and the stale value is retained as superseded
- the contradiction is moved to an open boundary and closure is downgraded accordingly

################################################################################
# 9 — STANDARDIZATION RULES FOR AGENTS
################################################################################

An agent writing or rewriting a hardware database subsection must do this in order:

1. Identify the subsection scope.
2. Assign the subsection record domain.
3. Name witness sources.
4. Declare capture-state boundaries.
5. Convert every existing fact into one of the standard record tables.
6. Preserve every anomaly, contradiction, open item, and transferred lead.
7. Remove ROM/build/action language from the database subsection.
8. Replace prose conclusions with final classification rows.
9. Ensure every canonical fact has a supporting witness row.
10. Ensure no declaration is promoted to physical hardware.
11. Ensure no runtime state is promoted to stock metadata.
12. Ensure every row has a classification.
13. Produce the subsection in the universal skeleton order.

Agent must not:
- invent a section-local structure
- omit blocks because they feel unnecessary
- summarize a complex subsection into prose
- hide anomalies in paragraphs
- convert open boundaries into conclusions
- write recommendations inside the database
- use "likely", "probably", or "expected" as database facts
- remove facts because they are inconvenient or duplicate-looking
- collapse same-name/same-size objects without hash proof
- collapse path identity and byte identity
- collapse symlink identity and target identity

################################################################################
# 10 — UNIVERSAL SUBSECTION TEMPLATE
################################################################################

################################################################################
# N.N — SUBSECTION TITLE
################################################################################

SUBSECTION CONTROL:
Section ID                     : 
Subsection ID                  : 
Subsection title               : 
Database type                  : HARDWARE_DIGITAL_TWIN
Record domain                  : 
Primary object class           : 
Status                         : 
Closure target                 : 

SCOPE:
Included                       : 
Excluded                       : 
Cross-section ownership         : 
Comment allowance              : 

WITNESS AUTHORITY MAP:
| Witness ID | Witness class | Authority meaning | Exact source object/path | Capture state | Notes |
|---|---|---|---|---|---|

CAPTURE STATE BOUNDARY:
Live capture state             : 
Stock file metadata authority  : 
Runtime behavior authority     : 
Raw-image authority            : 
Extraction authority           : 
Boundary classification         : 

EVIDENCE GATE STATUS:
Command/input clean             : 
Output complete                 : 
Destination/file valid          : 
Witness agreement state         : 
Contradiction state             : 
Closure permitted               : 

CANONICAL RESOLVED FACTS:
| Fact ID | Fact key | Canonical value | Canonical layer | Primary witness | Classification | Notes |
|---|---|---|---|---|---|---|

LAYERED WITNESS RECORDS:
| Record ID | Fact key | Witness ID | Layer | Source object/path | Raw value | Normalized value | Classification | Notes |
|---|---|---|---|---|---|---|---|---|

DECLARATION / POPULATION / ACTIVATION MATRIX:
| Object ID | Object name/path | Declared by | Populated where | Runtime active evidence | Physical evidence | Classification | Notes |
|---|---|---|---|---|---|---|---|

STATIC ARTIFACT INVENTORY:
| Artifact ID | Path/object | Type | Mode | UID:GID | SELinux context | Size bytes | Symlink target | Witness ID | Classification | Notes |
|---|---|---|---|---|---|---:|---|---|---|---|---|

RUNTIME BINDING / OPERATIONAL STATE:
| Runtime ID | Runtime object | Binding type | Driver/service/process/HAL | State | UID/domain/context | Source | Classification | Notes |
|---|---|---|---|---|---|---|---|---|

REGISTER / BUS / TOPOLOGY RECORDS:
| Topology ID | Object | Address/bus/index | Size/range | Parent | Child/endpoint | Driver/binding | Source | Classification | Notes |
|---|---|---|---|---|---|---|---|---|---|

DEPENDENCY / CONSUMER MAP:
| Dependency ID | Provider object | Consumer object | Interface | Evidence source | Runtime proven | Classification | Notes |
|---|---|---|---|---|---|---|---|

ANOMALY / CONTRADICTION REGISTER:
| Anomaly ID | Label | Evidence A | Evidence B | Exact discrepancy | Status | Owner section | Classification | Required resolution |
|---|---|---|---|---|---|---|---|---|

CROSS-SECTION TRANSFER REGISTER:
| Transfer ID | Object/fact | Current subsection handling | Owning section | Reason for transfer | Classification |
|---|---|---|---|---|---|

OPEN BOUNDARIES:
| Boundary ID | Boundary label | What is known | What is not known | Why it remains open | Required evidence to close | Classification |
|---|---|---|---|---|---|---|

FINAL SUBSECTION CLASSIFICATION:
Canonical conclusion           : 
Closure state                  : 
Open boundary count            : 
Contradiction count            : 
Transferred item count         : 
Database action                : 

################################################################################
# 11 — NORMALIZATION RULES FOR EXISTING INCONSISTENT SUBSECTIONS
################################################################################

When normalizing older subsections, do not rewrite meaning first.
Normalize structure first.

11.1 Existing scalar groups

Move scalar groups into:
- CANONICAL RESOLVED FACTS
- LAYERED WITNESS RECORDS
- STATIC ARTIFACT INVENTORY
- REGISTER / BUS / TOPOLOGY RECORDS

11.2 Existing prose notes

Move prose notes into one of:
- SCOPE
- CAPTURE STATE BOUNDARY
- ANOMALY / CONTRADICTION REGISTER
- OPEN BOUNDARIES
- FINAL SUBSECTION CLASSIFICATION

If a prose note contains more than one fact, split it into records.

11.3 Existing Markdown subheadings

Convert internal "##" headings into block titles from the universal skeleton.

11.4 Existing code fences

Convert code-fenced key/value data into aligned rows or tables.
Retain code fences only for raw capture payloads.

11.5 Existing conclusion paragraphs

Convert to:
- canonical conclusion
- closure state
- open boundary count
- contradiction count
- database action

11.6 Existing open item lists

Convert to OPEN BOUNDARIES table.
Each open item gets:
- known facts
- unknown part
- required evidence
- classification

11.7 Existing anomaly comments

Convert to ANOMALY / CONTRADICTION REGISTER unless they are pure scope comments.

11.8 Existing cross-section references

Convert to CROSS-SECTION TRANSFER REGISTER.
Do not expand transferred material inside the current subsection.

################################################################################
# 12 — DATABASE-WIDE HARDWARE OUTCOME COVERAGE
################################################################################

This standard supports every Android hardware outcome by forcing each object through the same matrix:

Declared?                       : YES / NO / NOT_TESTED / NOT_APPLICABLE
Populated?                      : YES / NO / NOT_TESTED / NOT_APPLICABLE
Active?                         : YES / NO / NOT_TESTED / NOT_APPLICABLE
Physical?                       : YES / NO / NOT_TESTED / NOT_APPLICABLE
Layer split?                    : YES / NO
Contradiction?                  : YES / NO
Research-state affected?        : YES / NO / UNKNOWN
Closure                         : CLOSED / OPEN / BLOCKED

Examples of supported outcomes:
- physical hardware present and active
- physical hardware present but inactive
- physical hardware present with no Android declaration
- Android declaration with no hardware
- file/blob present but unused
- HAL registered without physical hardware
- driver bound to unexpected node
- devicetree node present but unbound
- kernel config enabled but runtime inactive
- runtime active with no static source found
- BSP remnant from another variant
- virtual/emulated Android hardware
- symlink projection / alias namespace
- per-boot counter/value
- stock image vs live rooted contradiction
- raw image vs extracted tree contradiction
- malformed or truncated capture
- value not exposed by live device
- value requiring binary/source/physical inspection

No separate subsection format is needed for any of these outcomes.
Only classifications and row values change.

################################################################################
# END STANDARD
################################################################################
