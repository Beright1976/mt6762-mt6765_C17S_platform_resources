#######################################################################################################################################################
#    LOKMAT 5 MAX  C17S_Forensic_Hardware_Database_Directory
#  
# 
# 
# Core Silicon & Platform Identity
# Storage Geometry & Boot Topography
# Firmware Execution Domains
# Hardware Interconnects, Buses & DMA
# Clock, Reset, IRQ & Wake Topology
# Power Management, Thermal & Charging
# Dual-Chip Coprocessor & Sensor Plane
# Visual, Audio & Input Pipelines
# Baseband, Radio, RF & Connectivity
# External Physical Interfaces & Board-Level Boundaries
# Hardware Security Substrate
# Negative Hardware, BSP Ghosts & Open Physical Leads




**Section 1 — Core Silicon & Platform Identity** — MT6762V/WB silicon, MT6765 BSP, BROM identifiers, ME_ID, SOC_ID, CPU cluster topology, GPU. All present. ✓

**Section 2 — Storage Geometry & Boot Topography** — Samsung 3V6CMB eMMC CID, full 38-partition GPT with byte offsets, super partition lpdump map, fstab, dm-0/1/2 mount topology. All present. ✓

**Section 3 — Firmware Execution Domains** — BROM → Preloader → LK/LK2 → TEE/MTEE → GZ (GeniZone) → SCP → SSPM → Kernel chain. Boot image header values, AVB footer hex, kernel cmdline verbatim. All present. ✓

**Section 4 — Hardware Interconnects, Buses & DMA** — iomem register map: all 7 I2C buses, 3 SPI buses, UART0/UART1, USB controller, AXI bus, EMI, DRAMC, GCE mailbox. All present. ✓

**Section 5 — Clock, Reset, IRQ & Wake Topology** — DVFSRC, watchdog/toprgu address, CPU DVFS proc nodes, CCI frequency, nRF52832 IRQ 27 confirmed. Partial — IRQ full table may need a live pull. Flag this.

**Section 6 — Power Management, Thermal & Charging** — MT6357 PMIC, MT6370 charger/PMU, fuel gauge, battery profile, thermal zones, CPU/GPU OPP voltage tables, ZRAM config. All present. ✓

**Section 7 — Dual-Chip Coprocessor & Sensor Plane** — nRF52832 silicon confirmed, UART1/ttyS1 bridge, 34-attribute wiite_corp_ctrl interface, IRQ 27 timing, sensor IC inventory, SCP RTOS, SSPM. All present. ✓

**Section 8 — Visual, Audio & Input Pipelines** — NT35695B panel, DSI command mode, 480×640, framebuffer nodes, Hynitron touch I2C 0x5D, audio HAL, vibrator PMIC path. Display and input present. Audio detail should be checked.

**Section 9 — Baseband, Radio, RF & Connectivity** — md1img modem, wmt_drv/wlan gen4m/bt_drv/gps_drv kernel modules, HAL manifest radio entries, LTE profile. Present. ✓

**Section 10 — External Physical Interfaces & Board-Level Boundaries** — USB controller 0x11200000, VID/PID 0x0E8D/0x201D, SD card/OTG fstab entries, physical camera ports, UART console. Present. ✓

**Section 11 — Hardware Security Substrate** — DXCC derived keys, RPMB, TEE/GZ/SCP execution domains, Keymaster 4.0, Gatekeeper software path, Widevine L3, AVB null state. All present. ✓

**Section 12 — Negative Hardware, BSP Ghosts & Open Physical Leads** — This is critical and the database already has entries for absent hardware (no gyroscope, no NFC, no proximity sensor, no ambient light, no wireless charging) and BSP ghost components. This section is where that all lives cleanly. ✓

**One gap I see:** The full IRQ table for the MT6762 is referenced but I did not see a complete enumerated IRQ-to-peripheral assignment table in what I read. That is the one area under Section 5 I cannot confirm is complete without you telling me or a live pull.

**One addition to consider:** The APEX anomaly — com.android.tzdata on dm-3 versus loop devices — is a storage/firmware boundary question. It fits in Section 3 or Section 2 depending on how you want to file it. It is documented in the database. You just need to decide which section owns it.

Otherwise: yes, your 12 sections encompass the entire physical hardware structure of this device. Nothing falls outside those boundaries. Ready when you start Section 1.
