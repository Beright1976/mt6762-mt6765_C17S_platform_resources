# MT6762 / MT6765 C17S Platform Resources

## LOKMAT APPLLP 5 MAX — Hardware and Software Research & Development

**Author:** [Albert Pittman / beright1976](https://github.com/Beright1976)  
**Project status:** Active, ongoing research and development  
**Scope:** Device reverse engineering, forensic evidence, security analysis, and the methodology supporting device reconstruction and remediation

This repository publishes the hardware and software forensic databases and research methodology developed through independent investigation of the **LOKMAT APPLLP 5 MAX (C17S)**. It brings together the device's physical architecture, firmware execution domains, Android software stack, dependencies, trust boundaries, and research history in a structured evidence resource.

The governing principles are **truth and logic**. Every technical conclusion must be supported by the evidence appropriate to that conclusion. Names identify the objects they describe. Contradictions remain visible. Unresolved questions retain explicit boundaries.

The purpose is practical: understand the device deeply enough to correct its behavior, preserve its useful capabilities, and develop a trustworthy standalone system. The research record and the methods used to produce it are published so that developers, security researchers, and readers learning these techniques can inspect the work and follow its evidence.

## Why this project exists

The C17S is a full Android wearable with communications, cameras, location services, radios, storage, and health-related hardware. Those capabilities make trust in the underlying platform essential. A device intended to hold personal information and run sensitive applications needs a security foundation that its owner can understand and evaluate.

Work that began with recovery development expanded into investigation of the entire platform: what the hardware actually is, how the firmware starts it, which software controls it, and which privileged interfaces remain exposed in the shipped software.

The security investigation documents factory, engineering, repair, and firmware-update functionality within the device's production firmware, including privileged Wiite applications, hidden Android component entry points, and hardware-control paths. The concern is the combination of privilege, reachability, persistence, and access to sensitive subsystems.

A concrete example from the associated research is the Nordic DFU service within `com.wiite.devicedaemon`. A service-start test without a firmware payload reached the DFU handling code, caused the persistent UID 1000 daemon to crash, and was followed by an Android restart of that daemon. The documented test established service reachability and crash behavior. No firmware payload was supplied, no firmware transfer was attempted, and no nRF firmware modification was performed.

The project's security framing is **malicious-capable architecture**: evidence of capabilities and weakened trust boundaries must be assessed at the strength actually demonstrated. Confirmed espionage, malicious exfiltration, remote exploitation in the field, manufacturer intent, and identical exposure across other products require their own evidence. Broader risks arising from reused Android board support packages are research questions whose applicability must be established for each device.

The engineering objective is to preserve legitimate device functions while correcting unsafe behavior and constraining privileged access. The databases support that work by documenting mechanisms, ownership, dependencies, and the boundaries that a replacement implementation must respect.

## Device and platform scope

| Attribute | Research subject |
|---|---|
| Commercial device | LOKMAT APPLLP 5 MAX |
| Device family | C17S |
| Board | `WP_C17S_PIX_TFT_D4` |
| Physical SoC identity | MediaTek `MT6762V/WB` |
| Software platform / BSP identity | `MT6765` |
| Stock Android baseline | Android 10 |
| Associated development | TWRP recovery and clean AOSP 11 research and development |

The physical silicon identity and software-platform identity are separate recorded facts. Each database section defines its own scope, evidence sources, capture state, and open boundaries. Observations from a rooted, unlocked, Magisk-modified, recovery, or GSI environment must be read in that recorded context; they do not automatically describe an untouched stock device.

## Start here

| To understand… | Read… |
|---|---|
| How the research was conducted and verified | [Controlled AI-assisted research methodology](FORENSICS_RESEARCH_DOCUMENTATION/C17S_AI_METHODOLOGY.md) |
| How evidence records are structured and classified | [Android Hardware Forensic Database Standard](FORENSICS_RESEARCH_DOCUMENTATION/ANDROID_HARDWARE_FORENSIC_DATABASE_STANDARD.md) |
| Project terminology and device-specific vocabulary | [Project lexicon](FORENSICS_RESEARCH_DOCUMENTATION/C17S_PROJECT_LEXICON_VOCABULARY.md) |
| Hardware identity and physical organization | [Hardware Section 01](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION01_CORE_SILICON_PLATFORM_IDENTITY.md) and the hardware section map below |
| Software provenance, partitions, and capture boundaries | [Software Section 00](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION00_PARTITION_ARCHITECTURE_SOURCE_AUTHORITY.md) |
| How the software database is expanded | [Software database expansion workflow](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_DATABASE_EXPANSION_WORKFLOW.md) |
| The earlier research record | [Historical resources](C17S_HISTORICAL_RESOURCES/) |

## Repository organization


mt6762-mt6765_C17S_platform_resources/
├── README.md
├── LICENSE
├── C17S_HISTORICAL_RESOURCES/
├── FORENSICS_HARDWARE_DATABASE/
├── FORENSICS_RESEARCH_DOCUMENTATION/
└── FORENSICS_SOFTWARE_DATABASE/


### Hardware forensic database

The hardware digital twin records physical identity, topology, firmware domains, interfaces, runtime bindings, and the distinction between populated hardware and inherited BSP declarations.

| Section | Subject |
|---|---|
| [01](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION01_CORE_SILICON_PLATFORM_IDENTITY.md) | Core silicon and platform identity |
| [02](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION02_GEOMETRY&BOOT_TOPOGRAPHY.md) | Storage geometry and boot topography |
| [03](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION03_FIRMWARE_EXECUTION_DOMAIN.md) | Firmware execution domains |
| [04](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION04_HARDWARE_INTERCONNECTS_BUSES&DMA.md) | Hardware interconnects, buses, and DMA |
| [05](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION05_CLOCK_RESET_IRQ_WAKE_TOPOLOGY.md) | Clock, reset, IRQ, and wake topology |
| [06](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION06_POWER_MANAGEMENT_THERMAL_CHARGING.md) | Power management, thermal behavior, and charging |
| [07](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION07_DUAL_CHIP_COPROCESSOR_SENSOR_PLANE.md) | Dual-chip coprocessor and sensor plane |
| [08](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION08_VISUAL_AUDIO_INPUT_PIPELINES.md) | Visual, audio, and input pipelines |
| [09](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION09_BASEBAND_RADIO_RF_CONNECTIVITY.md) | Baseband, radio, RF, and connectivity |
| [10](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION10_EXTERNAL_PHYSICAL_INTERFACES_BOARD_LEVEL_BOUNDARIES.md) | External physical interfaces and board-level boundaries |
| [11](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION11_HARDWARE_SECURITY_SUBSTRATE.md) | Hardware security substrate |
| [12](FORENSICS_HARDWARE_DATABASE/C17S_HW_SECTION12_NEGATIVE_HARDWARE_BSP_GHOSTS_OPEN_PHYSICAL_LEADS.md) | Negative hardware, BSP ghosts, and open physical leads |

The supplied [hardware section directory](FORENSICS_HARDWARE_DATABASE/C17S_HARDWARE_SECTIONS_DIRECTORY.md) also records the section organization.

### Software forensic database

The software digital twin maps the Android environment through file provenance, init actions, service ownership, HAL declarations and registration, library dependencies, runtime observations, and subsystem relationships.

| Section | Subject |
|---|---|
| [00](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION00_PARTITION_ARCHITECTURE_SOURCE_AUTHORITY.md) | Document control, partition architecture, and source authority |
| [01](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION01_INIT_SYSTEM_RC-FILES_SERVICE_GRAPH.md) | Init system, RC files, and service graph |
| [02](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION02_HAL_LAYER_HIDL_VINTF_MANIFEST_INTERFACE_INVENTORY.md) | HAL layer, HIDL/VINTF manifests, and interface inventory |
| [03](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION03_CRYPTOGRAPHIC_STACK_LIBRARY_CHAIN_NAMESPACE.md) | Cryptographic stack, library chain, and namespace isolation |
| [04](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION04_SCP_nRFBRIDGE_SENSORSERVICE.md) | Sensor stack, SCP, nRF bridge, and Android SensorService |
| [05](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION05_DISPLAY_GPU_GRAPHICS_STACK.md) | Display, GPU, and graphics stack |
| [06](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION06_CAMERA_HAL_BLOB_CCU_AUTHORITY_MAP.md) | Camera stack, HAL, blobs, sensor identities, and CCU |
| [07](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION07_AUDIO_POLICY_HAL_PARAMETER_ROUTES.md) | Audio stack, policy, HAL, parameters, and routes |
| [08](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION08_RADIO_TELEPHONY_MODEM.md) | Radio, telephony, and modem software stack |
| [09](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION09_CONNECTIVITY_SOFTWARE_STACK_WI-FI_BLUETOOTH_GNSS_WMT.md) | Wi-Fi, Bluetooth, GNSS, and WMT connectivity stack |
| [10](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTION10_VNDK-29_VNDK-SP-29_VENDOR_BLOB_INVENTORY.md) | VNDK-29, VNDK-SP-29, and vendor blob inventory |

The [software section directory](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_SECTIONS_DIRECTORY.md) describes the broader section architecture, including the continuing expansion beyond the section files currently supplied here. The [expansion workflow](FORENSICS_SOFTWARE_DATABASE/C17S_SOFTWARE_DATABASE_EXPANSION_WORKFLOW.md) documents acquisition, classification, contradiction handling, and verification procedures.

### Research methodology and historical resources

[`FORENSICS_RESEARCH_DOCUMENTATION/`](FORENSICS_RESEARCH_DOCUMENTATION/) contains the database standard, the AI-assisted research methodology, and the project lexicon.

[`C17S_HISTORICAL_RESOURCES/`](C17S_HISTORICAL_RESOURCES/) preserves the earlier research and methodology records:

- [Historical AI methodology](C17S_HISTORICAL_RESOURCES/AI_METHODOLOGY.txt)
- [Software forensics](C17S_HISTORICAL_RESOURCES/C17S_SOFTWARE_FORENSICS.md)
- [Forensic master database](C17S_HISTORICAL_RESOURCES/LOKMAT_5_MAX_FORENSICS_REVERSE_ENGINNEERING_DATABASE.md)

These records preserve the development of the investigation. Their conclusions must be read with their original scope, capture context, and any subsequent correction or reconciliation recorded in the expanded sections.

## What “digital twin” means in this project

A digital twin here is a structured forensic representation of the device: what exists, what it does, how it operates, what controls it, and what depends on it. Its purpose is to make the device's architecture inspectable and support reproducible engineering from documented evidence.

The record connects physical components and firmware to kernel interfaces, services, libraries, Android declarations, runtime consumers, and observed behavior. It also records absent hardware, inactive declarations, unresolved mechanisms, and contradictory witnesses.

The database remains an output of research. A recorded conclusion is checked against its underlying evidence; its presence in a document does not independently prove it.

## Evidence and verification discipline

The methodology makes the following distinctions explicit:

- **Source authority follows the question.** Live captures establish observed runtime state; raw images establish captured bytes and image metadata; extracted trees and static analysis are tied to their specific source objects.
- **Declaration, presence, and activity are different states.** A manifest entry, a file on disk, an active service, and physically populated hardware each require their own evidence.
- **Capture state is part of the finding.** Stock artifacts, current runtime observations, and research-induced changes retain their separate boundaries.
- **Mechanisms must be described.** An identifier, filename, or hash alone cannot explain a component's function, dependencies, or control flow.
- **Contradictions and open boundaries remain visible.** A disagreement is preserved until evidence resolves it. An open boundary states what is known, what remains unknown, and what would establish closure.
- **Expansion includes fresh discovery.** Earlier records provide context, while a new investigation examines the actual scope and reconciles its findings against the prior record.
- **Human review controls acceptance.** Albert Pittman directs the research, reviews execution and evidence, and accepts or rejects conclusions.

AI assistance is documented openly in the [methodology paper](FORENSICS_RESEARCH_DOCUMENTATION/C17S_AI_METHODOLOGY.md). Models assist with analysis, correlation, calculation, and drafting under operator control. Generated text and agreement between models do not substitute for device evidence.

## Living project and document status

This is an ongoing research and development project with no fixed final endpoint. Sections expand as additional evidence is acquired, mechanisms are resolved, and engineering work produces new observations.

Labels such as `FINAL`, `CANONICAL`, or `CLOSED` belong to the scope and verification state recorded by the individual document. They do not mean the entire project is finished, that every adjacent question is resolved, or that the document can never be extended. A section may explicitly retain named open leads alongside closed findings.

When reading or referencing a result, retain its section, evidence classification, capture state, and unresolved boundary. For a reproducible reference, identify the repository revision and the specific file and finding or subsection.

## Related project repositories

| Resource | Role in the wider project |
|---|---|
| [TWRP Build Control Protocol](https://github.com/Beright1976/twrp-build-protocol) | Evidence-driven recovery build methodology and validation gates |
| [C17S TWRP device repository](https://github.com/Beright1976/Lokmat5MAX_wiite-c17s_mt6765) | Device-specific TWRP implementation and recovery engineering whitepaper |
| [Wiite ODM security research](https://github.com/Beright1976/wiite-odm-backdoor-research) | Security findings, supporting analyses, ROM dissection, and clean-ROM research |

The planned XDA research and development thread will serve as the public release and discussion home connecting this evidence resource with the related repositories. 

## Reporting issues and contribution policy

This repository is single-author work. All commits, edits, and section expansions are made by Albert Pittman (beright1976) directly, against the evidence standards recorded in this repository's methodology documents. Pull requests and unsolicited edits are not the intended path for engaging with this material and will not be merged.

If you believe a specific finding is wrong, a mechanism is mischaracterized, or a piece of evidence contradicts what is recorded — contact the author directly through the GitHub profile linked below. Direct correction or challenge to the work is welcome and will be evaluated against the same evidence standard the rest of the repository is held to.

Public discussion, questions, and community engagement with this research have their home on the associated XDA thread, not in this repository's issue tracker. This repository is the evidence record; the XDA thread is the discussion venue.

This is separate from vulnerability disclosure for the device itself. Findings concerning the C17S / LOKMAT 5 MAX platform are handled through the CVE and CISA disclosure channels referenced in the security assessment, not through this repository.

## Authorship and publication scope

This research is authored and directed by **Albert Pittman (beright1976)**. The publication scope of this repository is the original research documentation, forensic databases, and methodology. Proprietary binaries and firmware or partition images are excluded from this repository's release scope. References to those artifacts identify the objects studied and the evidence sources used in the research.

## License

This repository's original research documentation, forensic databases, and methodology are released under the **Creative Commons Attribution 4.0 International License (CC BY 4.0)**. Full terms are in [`LICENSE`](LICENSE).

In practice: this work may be read, shared, cited, and built upon, including commercially, provided Albert Pittman (beright1976) is credited as the original author and any changes made to the material are indicated. This license does not extend to proprietary binaries, firmware, or partition images, which are excluded from this repository's release scope as stated above.

[GitHub profile](https://github.com/Beright1976) · [Professional profile](https://beright1976.github.io/Beright1976/)
