################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 08 — RADIO, TELEPHONY & MODEM SOFTWARE STACK
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 08 — RADIO / TELEPHONY / MODEM / IMS / WFC AUTHORITY MAP
# Closure     : CLOSED WITH FULL RADIO SOFTWARE INCLUSION, MTK RIL/CCCI/IMS/WFC STACK, WIITE UID1000 TELEPHONY AUTHORITY, FOTA RADIO-ADJACENCY, AND BSP GHOSTS CLASSIFIED
# Date        : 2026-06-17 live radio, modem, RIL, CCCI, IMS, WFC, ePDG, APN, package, binder, property, device-node, and network-state closure pass
################################################################################

################################################################################
# SCOPE
################################################################################

This section documents the C17S radio, telephony, modem, IMS, VoLTE, WFC,
IWLAN, APN, carrier, and modem-transport software stack as a software digital
twin database section.

This section is not a ROM build guide. It is the software truth map a competent
developer can use to understand how the stock device is actually constructed.
Build relevance is recorded only where the database entry affects downstream
bring-up, compatibility, hardening, stripping, or replacement decisions.

This section includes every software object in scope, including active runtime
components, required binaries, framework packages, APN/SPN/carrier files, modem
database files, CCCI device nodes, pseudo-terminal fanout, HIDL/Binder services,
Wiite/ODM UID1000 telephony authority, FOTA radio-adjacent software, unused or
ghost BSP surfaces, and failed capture gates. Classification preserves each
object in the database. Classification never removes an object.

This section covers:
- MD1 modem firmware identity and baseband properties.
- MTK RIL daemon, RIL library path, RIL arguments, and runtime service state.
- CCCI device-node inventory, modem status nodes, and CCCI daemon ownership.
- GSM0710 mux daemon, `/dev/ttyC0`, and `/dev/radio` pseudo-terminal fanout.
- Android telephony framework package set and `android.uid.phone` shared UID.
- MTK IMS, WFO, VoLTE, RadioEx, RadioOp, VideoTelephony, and Netdagent layers.
- VoLTE daemon set and WFC/ePDG/IPSec/StrongSwan daemon/config stack.
- APN, vendor APN, SPN, virtual SPN, carrierconfig, permission XML, and init RC identities.
- APDB modem database and shipped firmware artifacts within the radio/connectivity boundary.
- Active radio properties, SIM/UICC/carrier state, and privacy-classified subscriber identifiers.
- Live cellular/IWLAN network interfaces, routes, and ccmni fanout.
- Wiite UID1000/product framework-level radio authority and direct-FD boundary.
- Adups/FOTA radio-adjacent package identity and classification.
- Negative/BSP ghost register for C2K, inactive multisim, inactive MD2/MD3, unused ccmni ports, and failed gates.

This section does not perform Java decompilation, native reverse engineering,
SELinux policy decompilation, carrier policy interpretation, or live carrier
network diagnosis. Those belong to Sections 10, 13, 14, 16, and 18. When such
items are encountered in this section, they are retained here and classified.

################################################################################
# SOURCE AUTHORITY
################################################################################

TIER 1 : Live rooted C17S runtime capture
         - Authority for `getprop`, `init.svc.*`, `ps`, `/proc/<pid>`,
           `/dev`, `/dev/radio`, `/sys/class/net`, `ip route`, `service list`,
           `lshal`, and active process state.

TIER 2 : Live stock filesystem identity
         - Authority for `/system`, `/vendor`, `/product`, `/system/vendor`
           file paths, labels, sizes, and SHA-256 values.

TIER 3 : Prior closed canonical sections
         - Section 01: init/service graph.
         - Section 02: VINTF/HIDL service graph.
         - Section 03: crypto / StrongSwan / namespace isolation.
         - Section 04: Wiite/nRF/ODM process context where radio-adjacent.
         - Section 10 and later sections consume this section for VNDK/blob and
           ROM build mapping, but do not replace this section.

TIER 4 : Hardware digital twin
         - Used only to prevent software declarations from being promoted to
           physical hardware truth.

FINAL  : Beright / Albert acceptance after live re-verification.

################################################################################
# CLASSIFICATION LEGEND
################################################################################

[VERIFIED]                         Confirmed by live device or file identity capture.
[ACTIVE_RUNTIME]                   Observed running or present in active runtime state.
[REQUIRED_STOCK_STACK]             Stock software layer required by stock radio behavior.
[LOWER_MODEM_TRANSPORT]            Raw modem / CCCI / ttyC / mux transport layer.
[FRAMEWORK_TELEPHONY_AUTHORITY]    Android framework-level telephony permission/service authority.
[MTK_PHONE_UID]                    `android.uid.phone` / UID 1001 telephony authority set.
[WIITE_UID1000]                    Wiite/ODM package running under `android.uid.system` / UID 1000.
[ODM_RADIO_ADJACENT]               ODM package with radio/phone/SMS/APN/carrier-adjacent permissions or paths.
[FOTA_RADIO_ADJACENT]              FOTA package with radio identity/network-adjacent authority.
[IMS_WFC_CONTROL_PLANE]            IMS, VoLTE, ViLTE, WFC, IWLAN, ePDG, WFO, IPsec surface.
[STRONGSWAN_ISLAND]                Vendor StrongSwan/ePDG crypto/config stack.
[CONFIG_SOURCE]                    File supplies APN/SPN/carrier/init/permission config.
[FIRMWARE_OR_DATABASE]             Firmware, APDB, MD database, or modem-related database artifact.
[BSP_GHOST]                        Software declaration or node shipped by BSP; not proof of physical hardware.
[NEGATIVE_RUNTIME]                 Explicitly absent, stopped, unused, or non-matching in this capture.
[OPEN_LEAD]                        Preserved unresolved item requiring future specialized analysis.
[SECURITY_REVIEW]                  Security-relevant entry preserved for Section 16.
[CROSS_SECTION]                    Entry belongs here and is also consumed by another section.
[FAILED_GATE]                      Capture command failed or was malformed; not a negative device conclusion.
[PRIVACY_REDACTED]                 Private subscriber value captured in evidence but redacted in sharable section.

################################################################################
# SECTION 08 COMPLETION MATRIX
################################################################################

| Scope item | Closure status | Classification |
|---|---:|---|
| MD1/baseband identity | CLOSED | [VERIFIED] [ACTIVE_RUNTIME] |
| RIL daemon and library path | CLOSED | [VERIFIED] [LOWER_MODEM_TRANSPORT] |
| CCCI node inventory | CLOSED | [VERIFIED] [LOWER_MODEM_TRANSPORT] |
| ttyC/GSM0710 mux path | CLOSED | [VERIFIED] [LOWER_MODEM_TRANSPORT] |
| `/dev/radio` pty fanout | CLOSED | [VERIFIED] [LOWER_MODEM_TRANSPORT] |
| VoLTE/IMS/WFC/ePDG runtime daemons | CLOSED | [VERIFIED] [IMS_WFC_CONTROL_PLANE] |
| StrongSwan/ePDG/IPsec config | CLOSED | [VERIFIED] [STRONGSWAN_ISLAND] |
| APN/SPN/carrier/init/permission files | CLOSED | [VERIFIED] [CONFIG_SOURCE] |
| APDB / firmware identity | CLOSED | [VERIFIED] [FIRMWARE_OR_DATABASE] |
| Android/MTK phone UID packages | CLOSED | [VERIFIED] [MTK_PHONE_UID] |
| Wiite UID1000 telephony-adjacent packages | CLOSED | [VERIFIED] [WIITE_UID1000] |
| Adups/FOTA radio-adjacent package | CLOSED | [VERIFIED] [FOTA_RADIO_ADJACENT] [SECURITY_REVIEW] |
| Binder service surface | CLOSED | [VERIFIED] [FRAMEWORK_TELEPHONY_AUTHORITY] |
| HIDL radio/MTK service surface | CLOSED | [VERIFIED] [MTK_RADIO_HAL] |
| Cellular/IWLAN network state | CLOSED | [VERIFIED] [ACTIVE_RUNTIME] |
| Subscriber identifiers | CLOSED AS REDACTED | [VERIFIED] [PRIVACY_REDACTED] |
| C2K/MD2/MD3/multisim/unused nodes | CLOSED AS CLASSIFIED | [BSP_GHOST] [NEGATIVE_RUNTIME] |
| Failed early gates | CLOSED AS FAILED CAPTURE | [FAILED_GATE] |

################################################################################
# 08.1 — RADIO STACK SUMMARY
################################################################################

The C17S stock radio software stack is a single-SIM MTK LWTG modem stack built
around an MTK MD1 modem, MTK RIL fusion daemon, CCCI transport nodes, GSM0710
muxing over `/dev/ttyC0`, Android telephony framework services, MTK telephony
extensions, MTK IMS/WFO services, VoLTE daemons, WFC/ePDG/IPsec daemons, APN/SPN
configuration files, APDB modem database files, and Wiite UID1000 framework
packages with system-level phone/SMS/APN/WFC permissions.

The lower stack is MTK-owned and CCCI-backed. The Wiite layer does not need raw
CCCI file-descriptor ownership to be in the radio software boundary. The captured
truth is that Wiite/ODM packages occupy the system UID framework authority layer
and inherit privileged phone/SMS/APN/WFC authority through `android.uid.system`.
The raw modem transport remains owned by MTK radio daemons and CCCI services.

Primary architecture:

```text
SIM / UICC
  -> MD1 modem firmware / MOLY.LR12A.R3.MP.V107.4
    -> CCCI device nodes and ttyC channels
      -> gsm0710muxd on /dev/ttyC0
        -> mtkfusionrild -l libmtk-ril.so
          -> android.hardware.radio + vendor.mediatek.hardware.mtkradioex + radio_op
            -> android.uid.phone packages
              -> IMS / WFO / VoLTE / WFC framework services
                -> Wiite UID1000 system/product applications with phone/SMS/APN authority
```

################################################################################
# 08.2 — MODEM / BASEBAND / RIL IDENTITY
################################################################################

| Field | Value | Classification |
|---|---|---|
| Baseband | `MOLY.LR12A.R3.MP.V107.4` | [VERIFIED] [ACTIVE_RUNTIME] |
| MD apps load verno | `MOLY.LR12A.R3.MP.V107.4` | [VERIFIED] |
| MD apps load gencfg | `GEN93_USER` | [VERIFIED] |
| MD apps load date | `2021/01/20 17:32:43 GMT +08:00` | [VERIFIED] |
| Project baseband | `TK_MD_BASIC(LWTG_6177M_R3_6762)` | [VERIFIED] |
| Active modem status | `vendor.mtk.md1.status=ready` | [VERIFIED] [ACTIVE_RUNTIME] |
| VoLTE modem status | `vendor.volte_md_status=ready` | [VERIFIED] [ACTIVE_RUNTIME] |
| Active MD selector | `vendor.ril.active.md=10` | [VERIFIED] |
| RIL enabled flag | `vendor.ril.mtk=1` | [VERIFIED] |
| RIL daemon | `vendor.ril-daemon-mtk` | [VERIFIED] [ACTIVE_RUNTIME] |
| RIL binary | `/vendor/bin/hw/mtkfusionrild` / `/system/vendor/bin/hw/mtkfusionrild` | [VERIFIED] |
| RIL library path | `vendor.rild.libpath=mtk-ril.so` | [VERIFIED] |
| RIL args property | `vendor.rild.libargs=-d /dev/ttyC0` | [VERIFIED] |
| Runtime RIL command line | `/vendor/bin/hw/mtkfusionrild -l libmtk-ril.so` | [VERIFIED] |
| RIL mode | `ro.vendor.mtk_ril_mode=c6m_1rild` | [VERIFIED] |
| RIL implementation string | `gsm.version.ril-impl=android reference-ril 1.0` | [VERIFIED] |
| Single-bin modem support | `ro.vendor.mtk_single_bin_modem_support=1` | [VERIFIED] |
| LTE support | `ro.vendor.mtk_lte_support=1` | [VERIFIED] |
| C2K LTE mode | `ro.vendor.mtk_c2k_lte_mode=0` | [VERIFIED] [NEGATIVE_RUNTIME] |
| ECCCI C2K declaration | `ro.vendor.mtk_eccci_c2k=1` | [VERIFIED] [BSP_GHOST] |
| Protocol RAT config | `Lf/Lt/W/T/G` | [VERIFIED] |
| PS2 RAT config | `L/W/G` | [VERIFIED] |
| PS3 RAT config | `G` | [VERIFIED] |
| RAF1 | `213002` | [VERIFIED] |
| Default network | `9,9,9,9` | [VERIFIED] [BSP_EXPANDED] |
| Multi-SIM config | `persist.radio.multisim.config=ss` | [VERIFIED] |
| Max multisim | `ro.vendor.radio.max.multisim=ss` | [VERIFIED] |
| SIM count | `ro.telephony.sim.count=1` | [VERIFIED] |
| Radio baseband prop | `ro.baseband=unknown` | [VERIFIED] [ANDROID_GENERIC_GHOST] |

################################################################################
# 08.3 — SIM / UICC / CARRIER STATE
################################################################################

| Field | Value | Classification |
|---|---|---|
| SIM state | `gsm.sim.state=LOADED` | [VERIFIED] [ACTIVE_RUNTIME] |
| UICC type | `vendor.gsm.ril.uicctype=USIM` | [VERIFIED] |
| Full UICC type | `vendor.gsm.ril.fulluicctype=USIM` | [VERIFIED] |
| SIM operator alpha | `T-Mobile` | [VERIFIED] |
| SIM MCC/MNC | `310260` | [VERIFIED] |
| SIM country | `us` | [VERIFIED] |
| Runtime operator numeric | empty | [VERIFIED] [ACTIVE_IWLAN_CAPTURE] |
| Runtime operator alpha | empty | [VERIFIED] [ACTIVE_IWLAN_CAPTURE] |
| Current phone type | `1` | [VERIFIED] |
| SIM PIN retries | PIN `3`, PIN2 `10`, PUK `10`, PUK2 `10` | [VERIFIED] |
| PHB ready | `vendor.gsm.sim.ril.phbready=true` | [VERIFIED] |
| Test SIM flag | `vendor.gsm.sim.ril.testsim=0` | [VERIFIED] |
| Slot lock state | `vendor.gsm.sim.slot.lock.state=1` | [VERIFIED] |
| Slot lock card valid | `0` | [VERIFIED] |
| SIM on/off state | `vendor.ril.sim.onoff.state1=11` | [VERIFIED] |
| SIM on/off support | `vendor.ril.sim.onoff.support=1` | [VERIFIED] |
| IMSI status | `vendor.ril.imsi.status.sim1=1` | [VERIFIED] |
| ICCID raw property | present in live capture | [VERIFIED] [PRIVACY_REDACTED] |
| GSM IMSI property | present in live capture | [VERIFIED] [PRIVACY_REDACTED] |
| Hashed last ICCID | `7f562cd58bb777374ef5918d86439501` | [VERIFIED] |
| CFU ICCID base64 property | present in live capture | [VERIFIED] [PRIVACY_REDACTED] |
| UT IMSI / XUI obfuscated properties | present | [VERIFIED] [PRIVACY_REDACTED] |
| ECC list | `ril.ecclist=911,112` | [VERIFIED] |
| ECC service MCC | `310` | [VERIFIED] |
| Special ECC list | empty | [VERIFIED] |

Private subscriber identifiers are not deleted from the database. Their keys,
presence, persistence behavior, and classification are retained. Full raw values
are redacted in this sharable section and remain recoverable from the original
local evidence gate if needed.

################################################################################
# 08.4 — CCCI DEVICE NODE REGISTER
################################################################################

CCCI nodes are the lower modem transport surface. They are software-visible
device nodes and are included even when a node represents an inactive or BSP-wide
capability rather than an active physical modem endpoint.

| Node | Owner:group | Label | Major,minor | Classification |
|---|---|---|---:|---|
| `/dev/ccci_aud` | audio:audio | `u:object_r:ccci_device:s0` | 239,1 | [VERIFIED] [MODEM_AUDIO] |
| `/dev/ccci_md_log_ctrl` | radio:radio | `u:object_r:ccci_device:s0` | 239,2 | [VERIFIED] [MODEM_LOG] |
| `/dev/ccci_fs` | radio:radio | `u:object_r:ccci_device:s0` | 239,4 | [VERIFIED] [CCCI_FS] |
| `/dev/ccci_imsv` | radio:radio | `u:object_r:ccci_device:s0` | 239,8 | [VERIFIED] [IMS_VIDEO] |
| `/dev/ccci_imsc` | radio:radio | `u:object_r:ccci_device:s0` | 239,9 | [VERIFIED] [IMS_CONTROL] |
| `/dev/ccci_imsa` | radio:radio | `u:object_r:ccci_device:s0` | 239,10 | [VERIFIED] [IMS_WFC_CONTROL_PLANE] |
| `/dev/ccci_imsdc` | radio:radio | `u:object_r:ccci_device:s0` | 239,11 | [VERIFIED] [IMS_DATA_CHANNEL] |
| `/dev/ccci_ioctl0` | radio:radio | `u:object_r:ccci_device:s0` | 239,12 | [VERIFIED] [MODEM_IOCTL] |
| `/dev/ccci_ioctl1` | radio:radio | `u:object_r:ccci_device:s0` | 239,13 | [VERIFIED] [MODEM_IOCTL] |
| `/dev/ccci_ioctl2` | radio:radio | `u:object_r:ccci_device:s0` | 239,14 | [VERIFIED] [MODEM_IOCTL] |
| `/dev/ccci_ioctl3` | radio:radio | `u:object_r:ccci_device:s0` | 239,15 | [VERIFIED] [MODEM_IOCTL] |
| `/dev/ccci_ioctl4` | radio:radio | `u:object_r:ccci_device:s0` | 239,16 | [VERIFIED] [MODEM_IOCTL] |
| `/dev/ccci_it` | radio:radio | `u:object_r:ccci_device:s0` | 239,17 | [VERIFIED] |
| `/dev/ccci_lb_it` | radio:radio | `u:object_r:ccci_device:s0` | 239,18 | [VERIFIED] |
| `/dev/ccci_mdl_monitor` | radio:radio | `u:object_r:ccci_device:s0` | 239,19 | [VERIFIED] [MODEM_MONITOR] |
| `/dev/ccci_rpc` | radio:radio | `u:object_r:ccci_device:s0` | 239,20 | [VERIFIED] [CCCI_RPC] |
| `/dev/ccci_imsem` | radio:radio | `u:object_r:ccci_device:s0` | 239,21 | [VERIFIED] [IMS_EMERGENCY] |
| `/dev/ccci_imsm` | radio:radio | `u:object_r:ccci_device:s0` | 239,22 | [VERIFIED] [IMS_MANAGER] |
| `/dev/ccci_woa` | radio:radio | `u:object_r:ccci_device:s0` | 239,23 | [VERIFIED] [WFC_EPDG] |
| `/dev/ccci_c2k_ppp` | radio:radio | `u:object_r:ccci_device:s0` | 239,24 | [VERIFIED] [BSP_GHOST] [C2K] |
| `/dev/ccci_c2k_agps` | radio:radio | `u:object_r:ccci_device:s0` | 239,25 | [VERIFIED] [BSP_GHOST] [C2K] |
| `/dev/ccci_ss_xcap` | radio:radio | `u:object_r:ccci_device:s0` | 239,26 | [VERIFIED] [XCAP] |
| `/dev/ccci_bip` | radio:radio | `u:object_r:ccci_device:s0` | 239,27 | [VERIFIED] [BIP] |
| `/dev/ccci_ipc_1220_0` | radio:radio | `u:object_r:ccci_device:s0` | 239,100 | [VERIFIED] [CCCI_IPC] |
| `/dev/ccci_ipc_2` | radio:radio | `u:object_r:ccci_device:s0` | 239,102 | [VERIFIED] [CCCI_IPC] |
| `/dev/ccci_ipc_4` | radio:radio | `u:object_r:ccci_device:s0` | 239,104 | [VERIFIED] [CCCI_IPC] |
| `/dev/ccci_ipc_5` | radio:radio | `u:object_r:ccci_device:s0` | 239,105 | [VERIFIED] [CCCI_IPC] |
| `/dev/ccci_ipc_9` | radio:radio | `u:object_r:ccci_device:s0` | 239,109 | [VERIFIED] [CCCI_IPC] |
| `/dev/ccci_ccb_dhl` | radio:radio | `u:object_r:ccci_device:s0` | 239,151 | [VERIFIED] [DHL_DIAGNOSTIC] |
| `/dev/ccci_ccb_md_monitor` | radio:radio | `u:object_r:ccci_device:s0` | 239,152 | [VERIFIED] [MODEM_MONITOR] |
| `/dev/ccci_ccb_meta` | radio:radio | `u:object_r:ccci_device:s0` | 239,153 | [VERIFIED] [META] |
| `/dev/ccci_ccb_ctrl` | radio:radio | `u:object_r:ccci_device:s0` | 239,154 | [VERIFIED] [CCB_CONTROL] |
| `/dev/ccci_raw_dhl` | radio:radio | `u:object_r:ccci_device:s0` | 239,155 | [VERIFIED] [RAW_DIAGNOSTIC] |
| `/dev/ccci_raw_mdm` | radio:radio | `u:object_r:ccci_device:s0` | 239,156 | [VERIFIED] [RAW_MODEM] |
| `/dev/ccci_raw_netd` | radio:radio | `u:object_r:ccci_device:s0` | 239,157 | [VERIFIED] [RAW_NETD] |
| `/dev/ccci_raw_usb` | radio:radio | `u:object_r:ccci_device:s0` | 239,158 | [VERIFIED] [RAW_USB_MODEM] |
| `/dev/ccci_raw_audio` | audio:audio | `u:object_r:ccci_device:s0` | 239,159 | [VERIFIED] [RAW_MODEM_AUDIO] |
| `/dev/ccci_mdx_sta` | radio:radio | `u:object_r:ccci_device:s0` | 250,0 | [VERIFIED] [GENERIC_MODEM_STATUS] |
| `/dev/ccci_md1_sta` | radio:radio | `u:object_r:ccci_device:s0` | 250,1 | [VERIFIED] [ACTIVE_MD1_STATUS] |
| `/dev/ccci_md2_sta` | radio:radio | `u:object_r:ccci_device:s0` | 250,2 | [VERIFIED] [BSP_GHOST] |
| `/dev/ccci_md3_sta` | radio:radio | `u:object_r:ccci_device:s0` | 250,3 | [VERIFIED] [BSP_GHOST] |
| `/dev/ccci_monitor` | radio:radio | `u:object_r:ccci_monitor_device:s0` | 240,0 | [VERIFIED] [MODEM_MONITOR] |

################################################################################
# 08.5 — TTYC / GSM0710 / RADIO PSEUDOTERMINAL FANOUT
################################################################################

| Node | Owner:group | Label | Major,minor | Function | Classification |
|---|---|---|---:|---|---|
| `/dev/ttyC0` | radio:radio | `u:object_r:gsm0710muxd_device:s0` | 239,3 | GSM0710 mux / primary RIL serial path | [VERIFIED] [LOWER_MODEM_TRANSPORT] |
| `/dev/ttyC1` | radio:radio | `u:object_r:mdlog_device:s0` | 239,7 | modem log tty | [VERIFIED] |
| `/dev/ttyC2` | radio:radio | `u:object_r:agps_device:s0` | 239,5 | AGPS tty path | [VERIFIED] |
| `/dev/ttyC3` | radio:radio | `u:object_r:icusb_device:s0` | 239,6 | ICUSB modem tty | [VERIFIED] |
| `/dev/ttyC5` | radio:radio | `u:object_r:nwkopt_device:s0` | 239,28 | network-option tty | [VERIFIED] |
| `/dev/ttyC6` | radio:radio | `u:object_r:nlop_device:s0` | 239,29 | NLOP tty | [VERIFIED] |

Runtime mux daemon:

| Field | Value |
|---|---|
| Service | `vendor.gsm0710muxd` |
| Runtime command | `/vendor/bin/gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic` |
| Runtime UID/GID | UID 1001 radio / GID 1001 radio |
| Open FD 0 | `/dev/ttyC0` |
| PTMX fanout | FDs 5-28 to `/dev/ptmx` |
| Classification | [VERIFIED] [LOWER_MODEM_TRANSPORT] [PTY_FANOUT] |

Radio pseudo-terminal directory:

| Path | Resolved target | Classification |
|---|---|---|
| `/dev/radio/atci1` | `/dev/pts/9` | [VERIFIED] [ATCI] |
| `/dev/radio/atci2` | `/dev/pts/20` | [VERIFIED] [ATCI] |
| `/dev/radio/pttyims` | `/dev/pts/0` | [VERIFIED] [IMS] |
| `/dev/radio/ptty2ims` | `/dev/pts/1` | [VERIFIED] [IMS] |
| `/dev/radio/ptty3ims` | `/dev/pts/2` | [VERIFIED] [IMS] |
| `/dev/radio/ptty4ims` | `/dev/pts/3` | [VERIFIED] [IMS] |
| `/dev/radio/pttynoti` | `/dev/pts/5` | [VERIFIED] [NOTIFICATION] |
| `/dev/radio/ptty2noti` | `/dev/pts/16` | [VERIFIED] [NOTIFICATION] |
| `/dev/radio/pttynwcmd` | `/dev/pts/26` | [VERIFIED] [NETWORK_COMMAND] |
| `/dev/radio/pttynwurc` | `/dev/pts/27` | [VERIFIED] [NETWORK_URC] |
| `/dev/radio/pttycmd1` | `/dev/pts/6` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd2` | `/dev/pts/7` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd3` | `/dev/pts/8` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd4` | `/dev/pts/4` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd7` | `/dev/pts/10` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd8` | `/dev/pts/11` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd9` | `/dev/pts/12` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd10` | `/dev/pts/13` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/pttycmd11` | `/dev/pts/14` | [VERIFIED] [RIL_COMMAND] |
| `/dev/radio/ptty2cmd1` | `/dev/pts/17` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd2` | `/dev/pts/18` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd3` | `/dev/pts/19` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd4` | `/dev/pts/15` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd7` | `/dev/pts/21` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd8` | `/dev/pts/22` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd9` | `/dev/pts/23` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd10` | `/dev/pts/24` | [VERIFIED] [SECONDARY_COMMAND] |
| `/dev/radio/ptty2cmd11` | `/dev/pts/25` | [VERIFIED] [SECONDARY_COMMAND] |

Directory label and ownership: `/dev/radio` is `drwxrwx---`, owner `radio`, group
`radio`, label `u:object_r:device:s0`. Individual IMS ptys are system-owned
symlinks while command/ATCI ptys are root:radio symlinks.

################################################################################
# 08.6 — DAEMON BINARY IDENTITY REGISTER
################################################################################

| Binary | Label | Size | SHA-256 | Classification |
|---|---|---:|---|---|
| `/system/bin/vtservice` | `u:object_r:vtservice_exec:s0` | 11060 | `2806ca092db758520e2a7842efc8597f4b1ca040dd7b8a28966e9d31cede595d` | [VERIFIED] [IMS_VIDEO] |
| `/system/vendor/bin/bip` | `u:object_r:bip_exec:s0` | 157640 | `c63d234a5bfc55125598b678b05d082c857a947227a18e1c3c75db6b6c996caa` | [VERIFIED] [BIP] |
| `/system/vendor/bin/ccci_fsd` | `u:object_r:ccci_fsd_exec:s0` | 156512 | `c0da9b3508a1f4d0ce92b19e5c0fbc4a80f0b88327572047582a128b6a1c5634` | [VERIFIED] [CCCI_FS] |
| `/system/vendor/bin/ccci_mdinit` | `u:object_r:ccci_mdinit_exec:s0` | 91636 | `98d7ab98c4abf5139d01b84ac9eb4dc9bcec275789857e77087f5b486589cf19` | [VERIFIED] [MODEM_INIT] |
| `/system/vendor/bin/ccci_rpcd` | `u:object_r:ccci_rpcd_exec:s0` | 36312 | `9132abb93a9d0fb9136e49d1fbb9dea6659d0b6cbd80ab337a11e9cb7c6cb031` | [VERIFIED] [CCCI_RPC] |
| `/system/vendor/bin/charon` | `u:object_r:charon_exec:s0` | 14240 | `f5194e62ae17a4e1f308f37095833706f1483ac0607792a420adcb791d905afe` | [VERIFIED] [STRONGSWAN_ISLAND] |
| `/system/vendor/bin/epdg_wod` | `u:object_r:epdg_wod_exec:s0` | 137200 | `4b471aa9c72132d600c0d135b7fee0da4795c26e67c0edad64fc8904f1821d16` | [VERIFIED] [EPDG_WOD] |
| `/system/vendor/bin/gsm0710muxd` | `u:object_r:gsm0710muxd_exec:s0` | 87308 | `2888face4c7b23a597e0f12dc7c43470591a60cb30e906f7fd5f6e3a4966d833` | [VERIFIED] [GSM0710_MUX] |
| `/system/vendor/bin/hw/mtkfusionrild` | `u:object_r:rild_exec:s0` | 20168 | `de07d5b07985746f475e02e048d5423b0138f4b44e5a27bf0b89521a9204df04` | [VERIFIED] [RIL] |
| `/system/vendor/bin/hw/vtservice_hidl` | `u:object_r:vtservice_hidl_exec:s0` | 11120 | `e815435af1dbaf0af9ce0e9a2eda39965faa2e9b8c5e69b34bd8fc3614f916f6` | [VERIFIED] [IMS_VIDEO_HIDL] |
| `/system/vendor/bin/ipsec_mon` | `u:object_r:ipsec_mon_exec:s0` | 50264 | `20e9049844291dea861052a8ed755aac74393a750c3676a8014b45fd51a7ec40` | [VERIFIED] [IPSEC_MON] |
| `/system/vendor/bin/netdagent` | `u:object_r:netdagent_exec:s0` | 56048 | `401b4ddb824642185e6bd2196c96658c5ddaadffd4f7bcb5f4cba70f3a3792da` | [VERIFIED] [NETDAGENT] |
| `/system/vendor/bin/starter` | `u:object_r:starter_exec:s0` | 80240 | `c98a70b761bffcb5ad5c726ba2fd02c587cc370d58d40cc0911ae283d30ffc22` | [VERIFIED] [STRONGSWAN_ISLAND] |
| `/system/vendor/bin/stroke` | `u:object_r:stroke_exec:s0` | 18408 | `b18050d77a84ca99f72da78966f9088c816a897c1fb0e92d9776b43a44231d4a` | [VERIFIED] [STRONGSWAN_ISLAND] |
| `/system/vendor/bin/volte_imcb` | `u:object_r:volte_imcb_exec:s0` | 680412 | `dc09ac7eb6b8ebdc564b18bbaf8a211d19f2ac3fb0d1aa6b7c66aa99c7eb5708` | [VERIFIED] [VOLTE] |
| `/system/vendor/bin/volte_imsm_93` | `u:object_r:volte_imsm_93_exec:s0` | 165440 | `ed86775e6a67580f6bfc3c22353d8a99e916bc456548df49de42cc6911ff4b94` | [VERIFIED] [VOLTE] |
| `/system/vendor/bin/volte_md_status` | `u:object_r:volte_md_status_exec:s0` | 18120 | `640ce5f28e2ca9a0df2af54fd07f095f078855a07184feebf1dea29a8d6f0dc9` | [VERIFIED] [MODEM_STATUS] |
| `/system/vendor/bin/volte_stack` | `u:object_r:volte_stack_exec:s0` | 755376 | `d38ccfc5a59984183ce85b801e9d5e0479b293ac0128ea5826d03b8c045315ab` | [VERIFIED] [VOLTE] |
| `/system/vendor/bin/volte_ua` | `u:object_r:volte_ua_exec:s0` | 1525552 | `b1c00d8dea0f77a3ae884019f0f314e12405d40f62dab6a23f36b9f7a4bb31b0` | [VERIFIED] [VOLTE] |
| `/system/vendor/bin/wfca` | `u:object_r:wfca_exec:s0` | 71232 | `0aaef1c50d6d6d78cc394f40a97d5efe2424a4674bb8e17a192e8329088c7c1b` | [VERIFIED] [WFC] |

################################################################################
# 08.7 — ACTIVE DAEMON RUNTIME REGISTER
################################################################################

| Process | Runtime command | UID/GID | Key open devices / sockets | Classification |
|---|---|---|---|---|
| `mtkfusionrild` | `/vendor/bin/hw/mtkfusionrild -l libmtk-ril.so` | radio:radio | `/dev/vndbinder`, `/dev/hwbinder`, multiple `/dev/pts/*`, `/dev/ccci_ss_xcap`, `/dev/ccci_mdx_sta`, sockets | [VERIFIED] [RIL] [LOWER_MODEM_TRANSPORT] |
| `gsm0710muxd` | `/vendor/bin/gsm0710muxd -s /dev/ttyC0 -f 512 -n 8 -m basic` | radio:radio | `/dev/ttyC0`, many `/dev/ptmx` | [VERIFIED] [GSM0710_MUX] |
| `ccci_fsd 2` | `/vendor/bin/ccci_fsd 2` | radio:radio | `/dev/ccci_md3_sta` | [VERIFIED] [BSP_GHOST] [MD3_STATUS] |
| `ccci_fsd 0` | `/vendor/bin/ccci_fsd 0` | radio:radio | `/dev/ccci_md1_sta`, `/dev/ccci_fs`, wake lock files | [VERIFIED] [ACTIVE_MD1] |
| `ccci_rpcd` | `/vendor/bin/ccci_rpcd 0` | radio:radio | `/dev/ccci_rpc` | [VERIFIED] [CCCI_RPC] |
| `ccci_mdinit` | `/vendor/bin/ccci_mdinit 0` | system:radio | `/proc/bootprof`, `/dev/ccci_monitor`, `/dev/ccci_ipc_5`, `/dev/ccci_md1_sta`, `/dev/ccci_mdx_sta` | [VERIFIED] [MODEM_INIT] |
| `bip` | `/vendor/bin/bip` | root:system | `/dev/ccci_bip`, sockets | [VERIFIED] [BIP] |
| `volte_md_status` | `/system/vendor/bin/volte_md_status` | root:radio | `/dev/ccci_mdx_sta`, socket | [VERIFIED] [VOLTE_STATUS] |
| `volte_imsm_93` | `/vendor/bin/volte_imsm_93` | root:radio | `/dev/ccci_imsm`, sockets | [VERIFIED] [IMS_MANAGER] |
| `volte_stack` | `/vendor/bin/volte_stack` | root:system | sockets/pipes, `libvolte_core_shr.so`, `libvolte_xdmc_shr.so`, `libipsec_ims_shr.so` | [VERIFIED] [VOLTE_STACK] |
| `volte_ua` | `/vendor/bin/volte_ua` | root:system | sockets/pipes, `libvolte_core_shr.so`, `libvolte_xdmc_shr.so` | [VERIFIED] [VOLTE_UA] |
| `volte_imcb` | `/vendor/bin/volte_imcb` | system:system | `/dev/ccci_imsc`, `/dev/ptmx`, sockets | [VERIFIED] [IMS_CONTROL_BRIDGE] |
| `wfca` | `/vendor/bin/wfca` | root:system | `/dev/ccci_imsa`, wake locks, sockets | [VERIFIED] [WFC_CONTROL] |
| `epdg_wod` | `/vendor/bin/epdg_wod` | root:root | `/dev/ccci_woa`, sockets | [VERIFIED] [EPDG_WOD] |
| `ipsec_mon` | `/vendor/bin/ipsec_mon` | system:net_admin | sockets, `libifcutils_mtk.so` | [VERIFIED] [IPSEC_MON] |
| `charon` | `/vendor/bin/charon --use-syslog` | root:root | `/data/vendor/ipsec/charon.pid`, `/dev/random`, `/dev/urandom`, sockets, wake locks | [VERIFIED] [STRONGSWAN_ISLAND] |
| `starter` | `/vendor/bin/starter --daemon charon` | root:root | minimal stdio FDs, `libstrongswan.so` mapped | [VERIFIED] [STRONGSWAN_ISLAND] |
| `vtservice` | `/system/bin/vtservice` | system:system | `/dev/binder`, `/dev/hwbinder`, `/dev/ccci_imsdc`, ashmem | [VERIFIED] [IMS_VIDEO] |
| `vtservice_hidl` | `/vendor/bin/hw/vtservice_hidl` | system:system | `/dev/hwbinder`, ashmem, sockets | [VERIFIED] [IMS_VIDEO_HIDL] |
| `netdagent` | `/vendor/bin/netdagent` | system:net_admin | `/dev/hwbinder`, socket, `libifcutils_mtk.so`, `vendor.mediatek.hardware.netdagent@1.0.so` | [VERIFIED] [NETDAGENT] |

################################################################################
# 08.8 — RIL / RADIO / IMS LIBRARY CHAIN
################################################################################

The `mtkfusionrild` process maps the MTK RIL fusion stack, Android radio VNDK
interfaces, MTK RadioEx interfaces, MTK RadioOp interfaces, carrier config,
XCAP/curl support, and MTK property contexts.

Required mapped libraries and interfaces include:

| Library / interface | Classification |
|---|---|
| `/vendor/lib64/libmtk-ril.so` | [VERIFIED] [RIL_CORE] |
| `/vendor/lib64/libmtk-rilop.so` | [VERIFIED] [RIL_OP] |
| `/vendor/lib64/librilfusion.so` | [VERIFIED] [RIL_FUSION] |
| `/vendor/lib64/libmtkrilutils.so` | [VERIFIED] |
| `/vendor/lib64/libmtkrillog.so` | [VERIFIED] |
| `/vendor/lib64/libccci_util.so` | [VERIFIED] [CCCI] |
| `/vendor/lib64/libcarrierconfig.so` | [VERIFIED] [CARRIER_CONFIG] |
| `/vendor/lib64/libcurl_xcap_md.so` | [VERIFIED] [XCAP] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.0.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.1.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.2.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.3.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.4.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.mtkradioex@1.5.so` | [VERIFIED] [MTK_RADIOEX] |
| `/vendor/lib64/vendor.mediatek.hardware.radio_op@1.1.so` | [VERIFIED] [RADIO_OP] |
| `/vendor/lib64/vendor.mediatek.hardware.radio_op@1.2.so` | [VERIFIED] [RADIO_OP] |
| `/vendor/lib64/vendor.mediatek.hardware.radio_op@1.3.so` | [VERIFIED] [RADIO_OP] |
| `/vendor/lib64/vendor.mediatek.hardware.radio_op@1.4.so` | [VERIFIED] [RADIO_OP] |
| `/vendor/lib64/vendor.mediatek.hardware.radio_op@2.0.so` | [VERIFIED] [RADIO_OP] |
| `/system/lib64/vndk-29/android.hardware.radio@1.0.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio@1.1.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio@1.2.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio@1.3.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio@1.4.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio.config@1.0.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio.config@1.1.so` | [VERIFIED] [VNDK_29] |
| `/system/lib64/vndk-29/android.hardware.radio.config@1.2.so` | [VERIFIED] [VNDK_29] |
| `/vendor/lib/libvolte_core_shr.so` | [VERIFIED] [VOLTE] |
| `/vendor/lib/libvolte_xdmc_shr.so` | [VERIFIED] [VOLTE] |
| `/vendor/lib/libipsec_ims_shr.so` | [VERIFIED] [IMS_IPSEC] |
| `/system/lib/libimsma.so` | [VERIFIED] [IMS_VIDEO] |
| `/system/lib/libimsma_adapt.so` | [VERIFIED] [IMS_VIDEO] |
| `/system/lib/libimsma_rtp.so` | [VERIFIED] [IMS_VIDEO] |
| `/system/lib/libimsma_socketwrapper.so` | [VERIFIED] [IMS_VIDEO] |
| `/system/lib/libmtk_vt_service.so` | [VERIFIED] [IMS_VIDEO] |
| `/vendor/lib64/vendor.mediatek.hardware.netdagent@1.0.so` | [VERIFIED] [NETDAGENT] |

Property context mappings observed inside radio daemons include:
`radio_prop`, `vendor_radio_prop`, `exported3_radio_prop`, `mtk_ims_prop`,
`mtk_wfc_prop`, `mtk_wfc_support_prop`, `mtk_volte_prop`,
`mtk_volte_support_prop`, `mtk_vilte_prop`, `mtk_vilte_support_prop`,
`mtk_viwifi_prop`, `mtk_viwifi_support_prop`, `mtk_mims_prop`,
`mtk_dynims_prop`, `mtk_ct_volte_prop`, `mtk_rat_config_prop`,
`mtk_md_version_prop`, `mtk_md_prop`, `mtk_md_status_prop`,
`mtk_telephony_sensitive_prop`, `mtk_smsformat_prop`, `mtk_extsim_prop`,
`mtk_vsim_prop`, `mtk_disable_c2k_cap_prop`, `ril_mux_report_case_prop`,
`gsm0710muxd_prop`, `mtk_em_hidl_prop`, and `mtk_nfc_uicc_clf_prop`.

`mtk_nfc_uicc_clf_prop` is a software property-context mapping only. It is not
physical NFC proof.

################################################################################
# 08.9 — RADIO / IMS / WFC HIDL AND BINDER SERVICE SURFACE
################################################################################

Init service state:

| Service | State | Classification |
|---|---|---|
| `ccci_mdinit` | running | [VERIFIED] |
| `ccci3_mdinit` | stopped | [VERIFIED] [NEGATIVE_RUNTIME] [BSP_GHOST] |
| `ipsec_mon` | running | [VERIFIED] |
| `netdagent` | running | [VERIFIED] |
| `vendor.bip` | running | [VERIFIED] |
| `vendor.ccci3_fsd` | running | [VERIFIED] [BSP_GHOST] |
| `vendor.ccci_fsd` | running | [VERIFIED] |
| `vendor.ccci_rpcd` | running | [VERIFIED] |
| `vendor.epdg_wod` | running | [VERIFIED] |
| `vendor.gsm0710muxd` | running | [VERIFIED] |
| `vendor.ril-daemon-mtk` | running | [VERIFIED] |
| `vendor.volte_imcb` | running | [VERIFIED] |
| `vendor.volte_stack` | running | [VERIFIED] |
| `vendor.volte_ua` | running | [VERIFIED] |
| `volte_imsm_93` | running | [VERIFIED] |
| `volte_md_status` | running | [VERIFIED] |
| `vtservice` | running | [VERIFIED] |
| `vtservice_hidl` | running | [VERIFIED] |
| `wfca` | running | [VERIFIED] |

Binder service surface:

| Service | Interface | Classification |
|---|---|---|
| `capctrl` | `com.mediatek.capctrl.aidl.IMtkCapCtrl` | [VERIFIED] |
| `mtksimphonebook` | `com.mediatek.internal.telephony.phb.IMtkIccPhoneBook` | [VERIFIED] |
| `isms` | `com.android.internal.telephony.ISms` | [VERIFIED] |
| `telephony.registry` | `com.android.internal.telephony.ITelephonyRegistry` | [VERIFIED] |
| `iphonesubinfo` | `com.android.internal.telephony.IPhoneSubInfo` | [VERIFIED] |
| `simphonebook` | `com.android.internal.telephony.IIccPhoneBook` | [VERIFIED] |
| `ircs` | `android.telephony.ims.aidl.IRcs` | [VERIFIED] |
| `telecom` | `com.android.internal.telecom.ITelecomService` | [VERIFIED] |
| `isubstub` | `com.mediatek.internal.telephony.IMtkSub` | [VERIFIED] |
| `isub` | `com.android.internal.telephony.ISub` | [VERIFIED] |
| `telephony.mtkregistry` | `com.mediatek.internal.telephony.IMtkTelephonyRegistryEx` | [VERIFIED] |
| `mtkIms` | `com.mediatek.ims.internal.IMtkImsService` | [VERIFIED] |
| `mtk_telecom` | `com.mediatek.internal.telecom.IMtkTelecomService` | [VERIFIED] |
| `mtkconnmetrics` | `com.mediatek.net.connectivity.IMtkIpConnectivityMetrics` | [VERIFIED] |
| `mwis` | `com.mediatek.wfo.IMwiService` | [VERIFIED] [WFO] |
| `imms` | `com.android.internal.telephony.IMms` | [VERIFIED] |
| `carrier_config` | `com.android.internal.telephony.ICarrierConfigLoader` | [VERIFIED] |
| `phoneEx` | `com.mediatek.internal.telephony.IMtkTelephonyEx` | [VERIFIED] |
| `phone` | `com.android.internal.telephony.ITelephony` | [VERIFIED] |
| `imtksms` | `com.mediatek.internal.telephony.IMtkSms` | [VERIFIED] |
| `iphonesubinfoEx` | `com.mediatek.internal.telephony.IMtkPhoneSubInfoEx` | [VERIFIED] |

HIDL surface:

| Interface | Instances | Classification |
|---|---|---|
| `android.hardware.radio.config@1.0::IRadioConfig` | `default` | [VERIFIED] |
| `android.hardware.radio.config@1.1::IRadioConfig` | `default` | [VERIFIED] |
| `android.hardware.radio@1.0::IRadio` | `em1`, `imsAospSlot1`, `se1`, `slot1` | [VERIFIED] |
| `android.hardware.radio@1.1::IRadio` | `em1`, `imsAospSlot1`, `se1`, `slot1` | [VERIFIED] |
| `android.hardware.radio@1.2::IRadio` | `em1`, `imsAospSlot1`, `se1`, `slot1` | [VERIFIED] |
| `android.hardware.radio@1.3::IRadio` | `em1`, `imsAospSlot1`, `se1`, `slot1` | [VERIFIED] |
| `android.hardware.radio@1.4::IRadio` | `em1`, `imsAospSlot1`, `se1`, `slot1` | [VERIFIED] |
| `android.hardware.radio@1.0::ISap` | `slot1` | [VERIFIED] |
| `android.hardware.radio@1.1::ISap` | `slot1` | [VERIFIED] |
| `android.hardware.radio@1.2::ISap` | `slot1` | [VERIFIED] |
| `android.hardware.secure_element@1.0::ISecureElement` | `SIM1` | [VERIFIED] [SIM_SECURE_ELEMENT] |
| `vendor.mediatek.hardware.mtkradioex@1.0-1.5::IMtkRadioEx` | `imsSlot1`, `mtkAssist1`, `mtkCap1`, `mtkEm1`, `mtkRcs1`, `mtkSe1`, `mtkSlot1` | [VERIFIED] |
| `vendor.mediatek.hardware.radio_op@2.0::IRadioOp` | `OpImsRILd1`, `slot1` | [VERIFIED] |
| `vendor.mediatek.hardware.netdagent@1.0::INetdagent` | `default` | [VERIFIED] |
| `vendor.mediatek.hardware.videotelephony@1.0::IVideoTelephony` | `default`, PID 537 | [VERIFIED] |
| `android.hidl.base@1.0::IBase` | `OpImsRILd1`, `imsAospSlot1`, `imsSlot1` | [VERIFIED] |

################################################################################
# 08.10 — IMS / VOLTE / WFC / IWLAN STATE
################################################################################

| Field | Value | Classification |
|---|---|---|
| Active network type | `gsm.network.type=IWLAN` | [VERIFIED] [ACTIVE_RUNTIME] |
| IMS support | `persist.vendor.ims_support=1` | [VERIFIED] |
| MIMS support | `persist.vendor.mims_support=2` | [VERIFIED] |
| MD MIMS support | `ro.vendor.md_mims_support=2` | [VERIFIED] |
| Auto IMS setup | `ro.vendor.md_auto_setup_ims=1` | [VERIFIED] |
| Dynamic IMS switch | `persist.vendor.mtk_dynamic_ims_switch=1` | [VERIFIED] |
| VoLTE support | `persist.vendor.volte_support=1` | [VERIFIED] |
| MTK VoLTE enable | `persist.vendor.mtk.volte.enable=1` | [VERIFIED] |
| VoLTE state | `persist.vendor.radio.volte_state=1` | [VERIFIED] |
| CT VoLTE support | `persist.vendor.mtk_ct_volte_support=1` | [VERIFIED] |
| ViLTE support | `persist.vendor.vilte_support=1` | [VERIFIED] |
| MTK ViLTE enable | `persist.vendor.mtk.vilte.enable=1` | [VERIFIED] |
| ViWiFi support | `persist.vendor.viwifi_support=1` | [VERIFIED] |
| MTK ViWiFi enable | `persist.vendor.mtk.viwifi.enable=1` | [VERIFIED] |
| WFC system support | `persist.vendor.wfc.sys_wfc_support=1` | [VERIFIED] |
| MTK WFC support | `persist.vendor.mtk_wfc_support=1` | [VERIFIED] |
| MTK WFC enable | `persist.vendor.mtk.wfc.enable=1` | [VERIFIED] |
| WFC state | `persist.vendor.radio.wfc_state=1` | [VERIFIED] |
| IWLAN operation mode | `ro.telephony.iwlan_operation_mode=default` | [VERIFIED] |
| IMS PDN info | `vendor.ims.eims.pdn.info=ims_0_0,` | [VERIFIED] |
| Data PDN info | `vendor.ril.data.pdn_info0=default_2,` | [VERIFIED] |
| IMS IPsec version | `vendor.net.ims.ipsec.version=2.0` | [VERIFIED] |
| VoLTE process PID prop | `vendor.net.ims.volte.pid=17881` | [VERIFIED] |
| LTE signal strength | `vendor.ril.nw.signalstrength.lte.1=-113,8` / capture-variant `-106,12` | [VERIFIED] [CAPTURE_VARIANT] |
| CFU over IMS | `persist.vendor.radio.cfu_over_ims=0` | [VERIFIED] |
| Terminal CLIR m/n | `m=4`, `n=0` | [VERIFIED] |
| Terminal CW | `disabled_tbcw` | [VERIFIED] |
| Fast dormancy counters | `150/50` and R8 `150/50` | [VERIFIED] |

################################################################################
# 08.11 — STRONGSWAN / EPDG / IPSEC CONFIG REGISTER
################################################################################

The WFC/ePDG stack uses a vendor StrongSwan crypto/config island. It is separate
from Android Keymaster/Gatekeeper/Vold crypto. It belongs in Section 08 because it
is active WFC/ePDG/IPsec radio software, and it cross-references Section 03 for
crypto isolation.

| File | Size | SHA-256 | Classification |
|---|---:|---|---|
| `/vendor/etc/ipsec/ipsec.conf` | 218 | `457273daf3e70a6e069dde468e081564aaa3ab00d5eb28f606dcdebed163612e` | [VERIFIED] [STRONGSWAN_ISLAND] |
| `/vendor/etc/ipsec/strongswan.conf` | 1282 | `2e06f61267b005798d7f16ea4e42186c95eadcf0339f8db37d3974f76c73aa93` | [VERIFIED] |
| `/vendor/etc/ipsec/ssl/openssl.cnf` | 0 | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | [VERIFIED] [EMPTY_CONFIG] |
| `/data/vendor/ipsec/charon.pid` | 6 | `2c6f6aee11e774c6b3fd5f81d656a48cf7a13b1f3237d743ee0ddfbfe44c046b` | [VERIFIED] [RUNTIME_STATE] |
| `/data/vendor/ipsec/starter.charon.pid` | 6 | `703bca8b83fa452cfe964e8918b213d8f992518c45c9d19ae35418a481f73e59` | [VERIFIED] [RUNTIME_STATE] |
| `/data/vendor/ipsec/wo/pdn_0_16.conf` | 1262 | `d4572cf131bf424a7b43ef26b0a41161be3a4d062889b4fab2a729746601519e` | [VERIFIED] [WFC_RUNTIME_APN] |
| `/data/vendor/ipsec/wo_apn.conf` | 45 | `aea4a3a434fbb583142198063650948a6bc4e1e9874d5b2d303c13ec53730b43` | [VERIFIED] [WFC_RUNTIME_APN] |

Trusted CA certificates shipped under `/vendor/etc/ipsec/ipsec.d/cacerts` and
mirrored under `/system/vendor/etc/ipsec/ipsec.d/cacerts`:

| Certificate | Size | SHA-256 |
|---|---:|---|
| `DigiCertGlobalRootCA.crt` | 947 | `4348a0e9444c78cb265e058d5e8944b4d84f9662bd26db257f8934a443c70161` |
| `DigiCertGlobalRootG2.crt` | 914 | `cb3ccbb76031e5e0138f8dd39a23f9de47ffc35e43c1144cea27d46a5ab1cb5f` |
| `DigiCertGlobalRootG3.crt` | 579 | `31ad6648f8104138c738f39ea4320133393e3a18cc02296ef97c2ac9ef6731d0` |
| `Entrust.net_Certification_Authority_2048.cer` | 1500 | `893299f422d50dfc38fa4fef23d7e833a164d98066d58b4fc7489b49e775d207` |
| `GeoTrust_PCA_G3_Root.der` | 1026 | `b478b812250df878635c2aa7ec7d155eaa625ee82916e2cd294361886cd1fbd4` |
| `GeoTrust_Primary_CA.der` | 896 | `37d51006c512eaab626421f1ec8c92013fc5f82ae98ee533eb4619b8deb4d06c` |
| `GeoTrust_Primary_CA_G2_ECC.der` | 690 | `5edb7ac43b82a06a8761e8d7be4979ebf2611f7dd79bf91c1c6b566a219ed766` |
| `Root-R3.cer` | 867 | `cbb522d7b7f127ad6a0113865bdf1cd4102e7d0759af635a7cf4720dc963c53b` |
| `Root_CA_1003.crt` | 1586 | `8bb5f9d84dfb0bae3702ea5f54b626330e2e4472f0236f8233a2e44356b3948a` |
| `VeriSignClass3G4.der` | 683 | `53dfdfa4e297fcfe07594e8c62d5b8ab06b32c7549f38a163094fd6429d5da43` |
| `VeriSignClass3G5.der` | 1239 | `9acfab7e43c8d880d06b262a94deeee4b4659989c3d0caf19baf6405e41ab7df` |
| `VeriSignUniversalRootCertification.der` | 1213 | `2399561127a57125de8cefea610ddf2fa078b5c8067f4e828290bfb860e84b3c` |
| `gold.cer` | 1476 | `40f60f2e2f83fb6c63ddefeba7939a7852b2d468183ea939cc4dcac8fe4cc87d` |
| `thawte.der` | 1060 | `8d722f81a9c113c0791df136a2966db26c950a971db46b4199f4ea54b78bfb9f` |

################################################################################
# 08.12 — CONFIG FILE IDENTITY REGISTER
################################################################################

| File | Label | Size | SHA-256 | Classification |
|---|---|---:|---|---|
| `/system/etc/apns-conf.xml` | `u:object_r:system_file:s0` | 557029 | `6d46ca610cfb3d91578fecf553b56c5fdc5281220ce12d0e4664802796430b17` | [VERIFIED] [APN] |
| `/vendor/etc/vendor-apns-conf.xml` | `u:object_r:vendor_configs_file:s0` | 7409 | `60aeb604d2977913ba2681a3422faa262149cc581e9f62d1ddefc83044abcfe8` | [VERIFIED] [VENDOR_APN] |
| `/system/etc/spn-conf.xml` | `u:object_r:system_file:s0` | 57035 | `c385d8741c3e723c9a8d85640101e0d16246e7f5bcf8d2558040944d4b1e9b7c` | [VERIFIED] [SPN] |
| `/vendor/etc/spn-conf-op09.xml` | `u:object_r:vendor_configs_file:s0` | 57412 | `9bc5ce157c1db88afdfba466edb5ae8fe5a9fa1a1ab09b981628df130bff0b53` | [VERIFIED] [OP09_SPn] |
| `/vendor/etc/virtual-spn-conf-by-efgid1.xml` | `u:object_r:vendor_configs_file:s0` | 186 | `707d105983e96505ff429070b0718d57fcac6a31f403068c3ff6389dd1a70ec4` | [VERIFIED] |
| `/vendor/etc/virtual-spn-conf-by-efpnn.xml` | `u:object_r:vendor_configs_file:s0` | 241 | `46a12da86eddfefa1dcb436b21100cc7348f7afa00595a430fca9aff70dd5b79` | [VERIFIED] |
| `/vendor/etc/virtual-spn-conf-by-efspn.xml` | `u:object_r:vendor_configs_file:s0` | 3265 | `02eec5bf31b3d9da0471536c1441cb8a6f616855790cb704c1263c567104dbd1` | [VERIFIED] |
| `/vendor/etc/virtual-spn-conf-by-imsi.xml` | `u:object_r:vendor_configs_file:s0` | 196 | `e9bffdd5b881b518e35767cbe6856361b0f9415a95d130e8433a94c00dafd30e` | [VERIFIED] [PRIVACY_REDACTED] |
| `/product/etc/permissions/com.android.carrierconfig.xml` | `u:object_r:system_file:s0` | 869 | `248978a11956f56667e3b4708603c811defa4eea2e069a7459d03473fbb2ea95` | [VERIFIED] [CARRIER_CONFIG_PERMISSION] |
| `/system/etc/permissions/com.mediatek.op.ims.common.xml` | `u:object_r:system_file:s0` | 826 | `ee8445b511ac2fc5fd3c47b92c5d038f232b7adcd171160902192c9a20470c2f` | [VERIFIED] [IMS_PERMISSION] |
| `/vendor/etc/permissions/android.hardware.telephony.gsm.xml` | `u:object_r:vendor_configs_file:s0` | 881 | `1e93627316d371ebaf1e5aad14b247530587780f3034b1ba136cbd66fc42dd48` | [VERIFIED] |
| `/vendor/etc/permissions/android.hardware.telephony.ims.xml` | `u:object_r:vendor_configs_file:s0` | 821 | `6f0d8f1e1dc0657e39ff63129b1633fdd9971417dcc9982271f089024666dadd` | [VERIFIED] |
| `/vendor/etc/init/init.volte_imcb.rc` | `u:object_r:vendor_configs_file:s0` | 447 | `2b1a08e2e5e0eab9a78b62d366312e7c750af535083cec1a114b0b42ef1cf54e` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.volte_imsm_93.rc` | `u:object_r:vendor_configs_file:s0` | 1393 | `ba22f7dfc06ec1f45529a2ee8ffeadb61a1092fcae1d1b08ef603f6e4cdbcf51` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.volte_md_status.rc` | `u:object_r:vendor_configs_file:s0` | 247 | `21b8a8728d2291f216abe397799f936d8a2b2c127d9e6908b336dc86c0505bc2` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.volte_stack.rc` | `u:object_r:vendor_configs_file:s0` | 233 | `726bb222414b7af15ffa55148848e16c9dd5cab99bb2dbaa6894175b1b658f23` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.volte_ua.rc` | `u:object_r:vendor_configs_file:s0` | 239 | `1bd8bab39fa9c5c22ce035c6174b4dbd257d66e948279e2cf5463041df9a3266` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.wfca.rc` | `u:object_r:vendor_configs_file:s0` | 263 | `50dd34078681cc2f5c5287890bd32da1d45583348c7097e38a3576af022ff29c` | [VERIFIED] [INIT_RC] |
| `/vendor/etc/init/init.wod.rc` | `u:object_r:vendor_configs_file:s0` | 970 | `0ae0b156bd52250961640bef4bffd4beebd7be41895c1c2d8aafa92f4b0ffb94` | [VERIFIED] [EPDG_INIT] |
| `/vendor/etc/init/ipsec_mon.rc` | `u:object_r:vendor_configs_file:s0` | 182 | `6cca4735245bbcda52f8853f4d4a8e299cfdb334479d450ceb94b7b4f7a4e4d1` | [VERIFIED] [IPSEC_INIT] |
| `/vendor/etc/init/mtkrild.rc` | `u:object_r:vendor_configs_file:s0` | 691 | `8c1767987ae47dd9f10f27add33dac0aa79c03e2fbcb8f80a82a9f013afeeb67` | [VERIFIED] [RIL_INIT] |
| `/vendor/etc/init/gsm0710muxd.rc` | `u:object_r:vendor_configs_file:s0` | 173 | `98cc2496b883e337bcb9e7bc9ceac8a606e2317cc5da3ec8a9a9474353201094` | [VERIFIED] [MUX_INIT] |
| `/vendor/etc/init/init.cccifsd.rc` | `u:object_r:vendor_configs_file:s0` | 682 | `b5810a6bbfa691310b13345d7e842542c5a5a8f3ebee9052bbfdf33bdafa6ab0` | [VERIFIED] [CCCI_INIT] |
| `/vendor/etc/init/init.cccimdinit.rc` | `u:object_r:vendor_configs_file:s0` | 767 | `81c46acff6f8b2235a41e637b37fc335c63d629bac93c76814172c8f62041cbf` | [VERIFIED] [CCCI_INIT] |
| `/vendor/etc/init/init.cccirpcd.rc` | `u:object_r:vendor_configs_file:s0` | 118 | `946fd9b36b3c3d26036c923388025b06ca86364491c5ffbba865eb2fd16221de` | [VERIFIED] [CCCI_INIT] |
| `/vendor/etc/init/init.bip.rc` | `u:object_r:vendor_configs_file:s0` | 574 | `6ed6befc0580e34c7eab24c97c3c3a25d14c37cea96d189fbc277fac86755cf6` | [VERIFIED] [BIP_INIT] |

################################################################################
# 08.13 — APDB / MODEM DATABASE / FIRMWARE REGISTER
################################################################################

| File | Label | Size | SHA-256 | Classification |
|---|---|---:|---|---|
| `/vendor/etc/apdb/APDB_MT6765_S01__W2014` | `u:object_r:vendor_configs_file:s0` | 193425 | `7fcf0dd7ea42291d90d707036ee87e8ae3fb90a806a407604241a008bad8d5d7` | [VERIFIED] [APDB] |
| `/vendor/etc/apdb/APDB_MT6765_S01__W2014_ENUM` | `u:object_r:vendor_configs_file:s0` | 20797 | `16b0c1e32cb3b4804a1cb16f7cf7bd395027c41773b6a8fc5e63c5c193d4fa51` | [VERIFIED] [APDB_ENUM] |
| `/system/vendor/etc/apdb/APDB_MT6765_S01__W2014` | `u:object_r:vendor_configs_file:s0` | 193425 | `7fcf0dd7ea42291d90d707036ee87e8ae3fb90a806a407604241a008bad8d5d7` | [VERIFIED] [MIRROR_PATH] |
| `/system/vendor/etc/apdb/APDB_MT6765_S01__W2014_ENUM` | `u:object_r:vendor_configs_file:s0` | 20797 | `16b0c1e32cb3b4804a1cb16f7cf7bd395027c41773b6a8fc5e63c5c193d4fa51` | [VERIFIED] [MIRROR_PATH] |

`/vendor/etc/mddb` and `/system/vendor/etc/mddb` were present as checked
candidate directories but did not produce matched MDDB files in the capture.

Firmware candidates under radio/connectivity scope:

| File | Label | Size | SHA-256 | Classification |
|---|---|---:|---|---|
| `/vendor/firmware/MNL.bin` | `u:object_r:vendor_file:s0` | 128 | `38723a2e5e8a17aa7950dc008209944e898f69a7bd10a23c839d341e935fd5ca` | [VERIFIED] [GNSS_MNL] |
| `/vendor/firmware/WIFI_RAM_CODE_soc1_0_1_1.bin` | `u:object_r:vendor_file:s0` | 612652 | `bffe8f712bf16666b5ccec1d7eb1149334e6b8f66652a68e4ebfb63347634566` | [VERIFIED] [CONNECTIVITY_FIRMWARE] |
| `/vendor/firmware/gt1151_default_firmware2.img` | `u:object_r:vendor_file:s0` | 63616 | `d2a6e1c2e1e350d940fad41991900fcfc4c81b3e786068560fe4f554602c3688` | [VERIFIED] [CROSS_SECTION_TOUCH] |
| `/vendor/firmware/soc1_0_patch_mcu_1_1_hdr.bin` | `u:object_r:vendor_file:s0` | 26350 | `f90816944375a023801af68bb5cf1cb163c76d0cb3a52b79b34084cc8aaee91e` | [VERIFIED] [CONNECTIVITY_FIRMWARE] |
| `/vendor/firmware/soc1_0_ram_bt_1_1_hdr.bin` | `u:object_r:vendor_file:s0` | 570765 | `36fcbd3e272fbd496c330f31f90ba31c07ee5a9d48888fefa2d27c352966ed19` | [VERIFIED] [BT_FIRMWARE] |
| `/vendor/firmware/soc1_0_ram_mcu_1_1_hdr.bin` | `u:object_r:vendor_file:s0` | 104324 | `47a349e4ddae156404fa4f65c824ad3da91d1027c9cbdfe0bb3ef16d47ca1600` | [VERIFIED] [CONNECTIVITY_MCU] |
| `/vendor/firmware/soc1_0_ram_wifi_1_1_hdr.bin` | `u:object_r:vendor_file:s0` | 195228 | `f99d7a212e4c4d00bf33f8241ff5bd4b8b93315558646e448c25a05762cc9191` | [VERIFIED] [WIFI_FIRMWARE] |

The same firmware paths are visible under `/system/vendor/firmware` with identical
hashes and labels. These are mirror paths caused by the stock mount/symlink
layout and are retained as software-visible paths.

################################################################################
# 08.14 — PHONE UID / MTK TELEPHONY PACKAGE REGISTER
################################################################################

| Package | UID / shared UID | Code path | APK size | SHA-256 | Classification |
|---|---|---|---:|---|---|
| `com.android.phone` | UID 1001 / `android.uid.phone` | `/system/priv-app/MtkTeleService` | 10318535 | `5b2d88a17df55f453e5d60d8e87d67260b212237fa6e2c83cb55df431e9b4623` | [VERIFIED] [MTK_PHONE_UID] |
| `com.mediatek.ims` | UID 1001 / `android.uid.phone` | `/system/priv-app/ImsService` | 6524791 | `d6f68b64e9495c451df3cdc31f11cf0ccd611a7a0d8578cf74d6e40b796fcbf1` | [VERIFIED] [IMS_SERVICE] |
| `com.mediatek.telephony` | UID 1001 / `android.uid.phone` | `/system/priv-app/MtkTelephonyAssist` | 53662 | `17c1aed965c4b325b674a117f7414c7e3de3ec7fdd983f464c0a7bbe0ef3c948` | [VERIFIED] [MTK_TELEPHONY_ASSIST] |
| `com.android.providers.telephony` | UID 1001 / `android.uid.phone` | `/system/priv-app/MtkTelephonyProvider` | 570583 | `5658d29625d482ae129fbb6a30185edcfcc3a74e9e43d5f7345c946af1e58fd3` | [VERIFIED] [TELEPHONY_PROVIDER] |
| `com.android.carrierconfig` | UID 10037 | `/product/priv-app/MtkCarrierConfig` | 258566 | `ddecc2bc588161562374cbd88321e386f5ed0bf0bef9f37e24a3877ad96e935a` | [VERIFIED] [CARRIER_CONFIG] |
| `com.android.server.telecom` | UID 1000 / `android.uid.system` | `/system/priv-app/WearTelecom` | 6430525 | `49b04ff8db1ec75cee1917dc764015ed3f2c98dc766d3fb631a0b3df67ca6af8` | [VERIFIED] [TELECOM] [WIITE_SYSTEM_STYLE] |
| `com.android.mms` | UID 10015 / `android.uid.mms` | `/system/priv-app/WearMtkMms` | 10887720 | `602470497765e8d5d97202e730187989039eb6eab1df547a67c30084cc9d2b61` | [VERIFIED] [MMS] |
| `com.mediatek.mms.appservice` | UID 1001 / `android.uid.phone` | `/system/priv-app/MtkMmsAppService` | 311146 | `80fb867545484c6be33cf485f0420704161f3d7ea7f1c4ec8ac0de7fb748b685` | [VERIFIED] [MTK_MMS_APP_SERVICE] |
| `com.android.mms.service` | UID 1001 / `android.uid.phone` | `/system/priv-app/MtkMmsService` | 98786 | `1f24bc686860c3063380ff989c226cfdc4a1f96fc09cd9fad31808ac191bb558` | [VERIFIED] [MMS_SERVICE] |

Phone UID packages share privileged grants:
`BIND_CARRIER_SERVICES`, `BROADCAST_SMS`, `CALL_PRIVILEGED`,
`READ_PRECISE_PHONE_STATE`, `TETHER_PRIVILEGED`,
`READ_PRIVILEGED_PHONE_STATE`, `ACCESS_IMS_CALL_SERVICE`, `WRITE_SMS`,
`WRITE_APN_SETTINGS`, `BIND_CARRIER_MESSAGING_SERVICE`, `BIND_EUICC_SERVICE`,
`REGISTER_SIM_SUBSCRIPTION`, `BIND_TELEPHONY_DATA_SERVICE`, `BIND_IMS_SERVICE`,
and `MODIFY_PHONE_STATE`.

`com.android.phone` exposes or contains telephony actions/services including
SIM contacts, EUICC privileged actions, VoLTE state setting action
`com.dmyk.android.telephony.action.VOLTE_STATE_SETTING`, carrier config change
receivers, SIM state receivers, cellular data service, MTK IWLAN data service,
MTK qualified networks service, cellular network service, MTK IWLAN network
service, and telephony debug service.

`com.mediatek.ims` provides `android.telephony.ims.ImsService`, MTK dynamic IMS
service, IMS config provider, and signature/privileged IMS config read/write
permissions.

`com.android.providers.telephony` exposes telephony, SMS, MMS, WAP push, RCS,
carrier ID, carrier information, service state, cell broadcast, and MWI provider
authorities.

`com.android.mms` includes MTK/Wiite-integrated MMS/SMS actions, WAP push
delivery, SIM full receiver, SMS deliver receiver, OMACP receiver, SMS/MMS
compose schemes, and a `com.wiite.mms.remove.notif` action. It also includes
IMS config provider permissions.

################################################################################
# 08.15 — WIITE UID1000 RADIO-ADJACENT PACKAGE REGISTER
################################################################################

These entries are not removed because they are ODM-specific. They are software
truth. Their classification records the authority they hold.

All listed UID1000 Wiite packages inherit the `android.uid.system` shared UID
grant set containing:
`CALL_PRIVILEGED`, `TETHER_PRIVILEGED`, `READ_PRIVILEGED_PHONE_STATE`,
`WRITE_SMS`, `WRITE_APN_SETTINGS`, `com.mediatek.permission.version.wfc`,
`MODIFY_PHONE_STATE`, and default/system-fixed grants for `READ_SMS`,
`SEND_SMS`, `RECEIVE_SMS`, `READ_PHONE_STATE`, `READ_PHONE_NUMBERS`,
`CALL_PHONE`, `ACCESS_FINE_LOCATION`, and `ACCESS_COARSE_LOCATION`.

| Package | UID / shared UID | Code path | APK size | SHA-256 | Classification |
|---|---|---|---:|---|---|
| `com.wiite.WiiteCommon` | 1000 / `android.uid.system` | `/product/priv-app/WearCommon` | 2134345 | `93e7235b7ac8a13c9cf21e5cc17e81fe7d707b2081370c18d965a4084b361b23` | [VERIFIED] [WIITE_UID1000] [FRAMEWORK_TELEPHONY_AUTHORITY] |
| `com.wiite.devicedaemon` | 1000 / `android.uid.system` | `/product/priv-app/WearDeviceDeamPix` | 3088919 | `c5adad35b18a6c52bd96e1cccb60ad747c58d9143f7b9ec119750e720326e96a` | [VERIFIED] [WIITE_UID1000] [SECRET_CODE_SURFACE] |
| `com.wiite.wearhealthuart` | 1000 / `android.uid.system` | `/product/app/WearHealthStepUart` | 389950 | `1cc4e184ef6a2442699424a22f99adeb9dd57bb0c9af788aa7c8c47565dbd781` | [VERIFIED] [WIITE_UID1000] [CROSS_SECTION_SENSOR] |
| `com.wiite.wiite_heartrate` | 1000 / `android.uid.system` | `/product/app/WearHeartRateUart` | 4569178 | `f1f1c2ee7b1572ff72492e43b65a6975fb73b156501b6e61da02d70aa18752cf` | [VERIFIED] [WIITE_UID1000] [CROSS_SECTION_SENSOR] |
| `com.wiite.wiite_bloodoxygen` | 1000 / `android.uid.system` | `/product/app/WearSpoUart` | 9537511 | `7e73b2ce152503007bd79ea72967cc0f71f54a927b3cd38fc9dc8e2a54ac20ee` | [VERIFIED] [WIITE_UID1000] [CROSS_SECTION_SENSOR] |
| `com.wiite.wiite_sleep` | 1000 / `android.uid.system` | `/product/app/WearSleepUart` | 4396687 | `c29ea8dd02a3c167d1f0490334f03f0cb1189f7f1eba1f88b1a4b155c1042a7e` | [VERIFIED] [WIITE_UID1000] |
| `com.wiite.wiitestore` | 1000 / `android.uid.system` | `/product/app/WearAppDialStore` | 7270858 | `8ac0787bb1a7289166f413a11f3b21d64570636fea26e125493287483e597dd4` | [VERIFIED] [WIITE_UID1000] |
| `com.wiite.power` | 1000 / `android.uid.system` | `/product/app/WearInChargingUi` | 1820317 | `e751afc5344077b54dd43aa5e5aa7b4a2e2a7fbff51fe93ad417d909d9ad7bb7` | [VERIFIED] [WIITE_UID1000] |
| `com.wiite.home` | 1000 / `android.uid.system` | `/product/app/WearHomeSportDM101` | 16031362 | `856fe9c65ad231852fb99b0c570b7a166d109f139eb0c241e978bdf966d39487` | [VERIFIED] [WIITE_UID1000] |
| `com.wiite.devicemsg` | 1000 / `android.uid.system` | `/product/app/WearDevicemsg` | 8172512 | `4a67d6186fbe54c487d816ab9000788722376722149a543f69a1969d05dfccb0` | [VERIFIED] [WIITE_UID1000] |
| `com.wiite.systemtool` | 10048 | `/product/app/WearSystemTool` | 2873531 | `ee0d1af1dbaedd8f49ad7655a241c947740a2874d8618615dea3120e2cce1313` | [VERIFIED] [NON_UID1000_EXCEPTION] |
| `com.wiite.dbf.androidp_contacts` | 1000 / `android.uid.system` | `/product/app/WearDialerContacts` | 3844760 | `f5b5097ea56e078e75b8d1e46bd7e47be6e910386de2a8fed47114744d2f68bf` | [VERIFIED] [WIITE_UID1000] [DIALER_CONTACTS] |
| `com.wiite.dbf.wiitemms` | 1000 / `android.uid.system` | `/product/app/WearMms` | 3306049 | `4e2772b3330526346f45ee89b9a5ca91fd322b15378aa99d7027cfe82f311935` | [VERIFIED] [WIITE_UID1000] [MMS_SMS] |
| `com.wiite.camera` | 1000 / `android.uid.system` | `/product/app/WearCamera` | 5737265 | `e2ecaf8e4131cab2fd898c96e123b11844e149fa03d9b42b0f5d3b978c11a844` | [VERIFIED] [WIITE_UID1000] [CROSS_SECTION_CAMERA] |

`com.wiite.devicedaemon` includes an `android.provider.Telephony.SECRET_CODE`
surface using scheme `android_secret_code`. This is recorded here because it is
inside the telephony software surface; behavior analysis belongs to Section 13
and Section 16.

`com.wiite.dbf.wiitemms` includes SMS/MMS scheme handlers, WAP push delivery,
SMS deliver/received receivers, and SMS service surfaces. It is both a Section 08
telephony/MMS software entry and a Section 16 security-review entry.

`com.wiite.systemtool` is not removed. It is classified as the exception in this
capture: UID 10048, product app, no radio-adjacent privilege match in this gate.

################################################################################
# 08.16 — FOTA / ADUPS RADIO-ADJACENT REGISTER
################################################################################

| Package | UID | Code path | APK size | SHA-256 | Classification |
|---|---:|---|---:|---|---|
| `com.adups.fota` | 10041 | `/product/priv-app/AdupsFota` | 7367820 | `619790f00d84e2993ceca569c946e7dc0ba9e347e12ec32c2d18cf1ca5abbcba` | [VERIFIED] [FOTA_RADIO_ADJACENT] [SECURITY_REVIEW] |
| `com.adups.fota.sysoper` | not present / no match | N/A | N/A | N/A | [VERIFIED] [NEGATIVE_RUNTIME] |
| `com.mediatek.mms.appservice` | 1001 / `android.uid.phone` | `/system/priv-app/MtkMmsAppService` | 311146 | `80fb867545484c6be33cf485f0420704161f3d7ea7f1c4ec8ac0de7fb748b685` | [VERIFIED] [MTK_PHONE_UID] |
| `com.android.mms.service` | 1001 / `android.uid.phone` | `/system/priv-app/MtkMmsService` | 98786 | `1f24bc686860c3063380ff989c226cfdc4a1f96fc09cd9fad31808ac191bb558` | [VERIFIED] [MTK_PHONE_UID] |

`com.adups.fota` is not excluded. It is included because it is product-privileged
and holds `READ_PRIVILEGED_PHONE_STATE`. Its malicious or unwanted behavior
classification is owned by Section 16, but the package identity and radio-adjacent
phone-state authority belong in this Section 08 database.

################################################################################
# 08.17 — CELLULAR / IWLAN NETWORK INTERFACE STATE
################################################################################

Active interfaces captured:

| Interface | State | Addressing | Classification |
|---|---|---|---|
| `ccmni0` | `UP, LOWER_UP` | IPv6 global `2607:fc20:fa69:14ba:*` and link-local | [VERIFIED] [CELLULAR_DATA] |
| `ccmni2` | capture-variant active in one run / down in another | IPv6 global `2607:fb90:*` in active capture | [VERIFIED] [CAPTURE_VARIANT] |
| `v4-ccmni2` | active in earlier capture | IPv4 `192.0.0.4/32` | [VERIFIED] [CELLULAR_IPV4_TRANSLATION] |
| `wlan0` | `UP, LOWER_UP` | IPv4 `192.168.1.115/24`, link-local IPv6 | [VERIFIED] [WIFI_TRANSPORT] |
| `ccmni1`, `ccmni3`-`ccmni20` | present, mostly DOWN | generated CCMNI fanout | [VERIFIED] [BSP_EXPANDED] [NEGATIVE_RUNTIME] |
| `ccmni-lan` | DOWN | LAN-style CCMNI endpoint | [VERIFIED] [BSP_EXPANDED] |
| `ifb0`, `ifb1` | DOWN | intermediate functional block devices | [VERIFIED] [TRAFFIC_CONTROL] |
| `tunl0`, `ip_vti0`, `ip6_vti0`, `sit0`, `ip6tnl0` | DOWN | tunnel interfaces | [VERIFIED] [IPSEC_TUNNEL_CAPABILITY] |
| `p2p0`, `ap0` | DOWN | Wi-Fi direct/AP endpoints | [VERIFIED] [CROSS_SECTION_CONNECTIVITY] |

Route state includes:
- Default route via `wlan0` table `wlan0`.
- Default route via `ccmni0` table `2454`.
- Default route via `ccmni0` table `ccmni0`.
- IPv6 route for `2607:fc20:fa69:14ba::/64` via `ccmni0`.
- Capture-variant IPv6 route for `ccmni2`.
- Numerous unreachable default routes in unused network tables.

The presence of many ccmni interfaces is software-visible truth and is retained.
Only active data state is classified separately.

################################################################################
# 08.18 — PROPERTY REGISTER SUMMARY
################################################################################

The radio property surface is large. The following groups are confirmed and must
be treated as part of Section 08:

| Group | Representative properties | Classification |
|---|---|---|
| GSM runtime | `gsm.network.type`, `gsm.sim.state`, `gsm.version.baseband`, `gsm.version.ril-impl` | [VERIFIED] |
| SIM/UICC | `vendor.gsm.ril.uicctype`, `vendor.gsm.ril.fulluicctype`, `vendor.gsm.sim.*` | [VERIFIED] |
| RIL core | `vendor.ril.mtk`, `vendor.rild.libpath`, `vendor.rild.libargs`, `ro.vendor.mtk_ril_mode` | [VERIFIED] |
| MD/baseband | `vendor.mtk.md1.status`, `ro.vendor.md_apps.load_verno`, `vendor.gsm.project.baseband` | [VERIFIED] |
| VoLTE/IMS | `persist.vendor.ims_support`, `persist.vendor.volte_support`, `persist.vendor.mtk.volte.enable`, `vendor.volte_md_status` | [VERIFIED] |
| WFC/IWLAN | `persist.vendor.mtk.wfc.enable`, `persist.vendor.radio.wfc_state`, `ro.telephony.iwlan_operation_mode`, `vendor.net.ims.ipsec.version` | [VERIFIED] |
| ViLTE/ViWiFi | `persist.vendor.vilte_support`, `persist.vendor.viwifi_support`, `persist.vendor.mtk.vilte.enable`, `persist.vendor.mtk.viwifi.enable` | [VERIFIED] |
| RAT/network | `ro.vendor.mtk_protocol1_rat_config`, `persist.vendor.radio.mtk_ps2_rat`, `persist.vendor.radio.raf1`, `vendor.ril.nw.worldmode.activemode` | [VERIFIED] |
| Fast dormancy | `persist.vendor.radio.fd.*` | [VERIFIED] |
| Supplementary service | `persist.vendor.radio.terminal-based.*`, `persist.vendor.radio.cfu.*` | [VERIFIED] |
| Privacy-sensitive identifiers | ICCID, IMSI, XUI, CFU ICCID properties | [VERIFIED] [PRIVACY_REDACTED] |
| Logging tags | numerous `persist.log.tag.*` RIL/IMS/SMS/telephony tags | [VERIFIED] [DIAGNOSTIC] |
| BSP ghosts | C2K/CDMA tags, external SIM, VSIM, NFC-UICC property contexts | [VERIFIED] [BSP_GHOST] |

################################################################################
# 08.19 — NEGATIVE / GHOST / BSP-EXPANDED REGISTER
################################################################################

| Item | Evidence | Classification |
|---|---|---|
| `ccci3_mdinit` stopped | `init.svc.ccci3_mdinit=stopped` | [VERIFIED] [NEGATIVE_RUNTIME] |
| `vendor.ccci3_fsd` running with `/dev/ccci_md3_sta` | CCCI FSD PID 556 opens MD3 status node | [VERIFIED] [BSP_GHOST] |
| `/dev/ccci_md2_sta` | node exists, no active MD2 proof | [VERIFIED] [BSP_GHOST] |
| `/dev/ccci_md3_sta` | node exists and ccci_fsd instance opens it, but no physical MD3 proof | [VERIFIED] [BSP_GHOST] |
| C2K properties/log tags | `ro.vendor.mtk_c2k_lte_mode=0` while C2K nodes/properties exist | [VERIFIED] [BSP_GHOST] |
| CDMA log tags | many CDMA/C2K tags enabled at `I` | [VERIFIED] [BSP_GHOST] |
| External SIM/VSIM property contexts | mapped in RIL process | [VERIFIED] [BSP_GHOST] |
| NFC UICC property context | mapped in RIL process | [VERIFIED] [BSP_GHOST] [NOT_PHYSICAL_NFC_PROOF] |
| ccmni1 and ccmni3-ccmni20 | present but down | [VERIFIED] [BSP_EXPANDED] |
| `com.adups.fota.sysoper` | no package match | [VERIFIED] [NEGATIVE_RUNTIME] |
| Early Gate F/H failures | quoting failure | [FAILED_GATE] |
| Gate Q first attempt | command quoting failure | [FAILED_GATE] |

################################################################################
# 08.20 — DIRECT WIITE MODEM OWNERSHIP BOUNDARY
################################################################################

The capture set proves Wiite/ODM framework-level radio authority, not direct raw
modem file-descriptor ownership by Wiite UID1000 processes.

Confirmed:
- Wiite packages run as UID1000 or product privileged apps.
- Wiite UID1000 packages inherit phone/SMS/APN/WFC privileged grants through
  `android.uid.system`.
- Wiite processes map telephony and MTK IMS framework artifacts in process memory.
- Wiite packages include SMS/MMS/dialer/secret-code/phone-state surfaces.
- `com.wiite.devicedaemon` exposes a telephony secret-code receiver.
- Wiite packages are product/system software truth and must be included.

Not proven by current gates:
- Direct Wiite open FD ownership of `/dev/ccci*`.
- Direct Wiite open FD ownership of `/dev/ttyC*`.
- Direct Wiite open FD ownership of `/dev/radio/*`.
- Direct Wiite TCP/UDP socket ownership tied to RIL/IMS/ePDG endpoints.

Classification:
`[VERIFIED] [WIITE_UID1000] [FRAMEWORK_TELEPHONY_AUTHORITY] [NO_DIRECT_CCCI_FD_PROOF]`.

################################################################################
# 08.21 — RECONCILIATION WITH PRIOR SECTIONS
################################################################################

Section 01 provides init authority for:
- `mtkrild.rc`
- `gsm0710muxd.rc`
- `init.cccifsd.rc`
- `init.cccimdinit.rc`
- `init.cccirpcd.rc`
- `init.bip.rc`
- `init.volte_imcb.rc`
- `init.volte_imsm_93.rc`
- `init.volte_md_status.rc`
- `init.volte_stack.rc`
- `init.volte_ua.rc`
- `init.wfca.rc`
- `init.wod.rc`
- `ipsec_mon.rc`

Section 02 provides manifest/HIDL authority for:
- Android radio HAL 1.0-1.4.
- Radio config 1.0/1.1.
- MTK RadioEx 1.0-1.5.
- MTK RadioOp 2.0.
- VideoTelephony 1.0.
- Netdagent 1.0.
- Secure Element SIM1.

Section 03 provides crypto isolation authority for:
- Vendor StrongSwan/ePDG crypto island.
- `charon`, `starter`, `stroke`.
- `libcharon-ss.so`, `libstrongswan.so`, `libcrypto-ss.so`, `libssl-ss.so`,
  `libcurl-ss.so`, `libhydra.so`, `libsimaka.so`.

Section 04 / Section 13 consume:
- Wiite UID1000 process set.
- DeviceDaemon and UART/sensor bridge relations.
- Wiite telephony-adjacent authority where it crosses ODM control-plane behavior.

Section 16 consumes:
- Adups/FOTA radio-adjacent authority.
- UID1000 phone/SMS/APN/WFC authority.
- Telephony secret-code surfaces.
- SMS/MMS/WAP push delivery surfaces.
- Private subscriber identifier persistence.

Section 18 consumes:
- C2K/MD2/MD3/VSIM/external SIM/NFC-UICC ghosts.
- Unused ccmni interface fanout.
- Failed capture gates and command-construction failures.

################################################################################
# 08.22 — FINAL SECTION 08 DATABASE CONCLUSION
################################################################################

The C17S radio software stack is fully represented by a layered MTK/Android/ODM
stack rather than by a single generic Android radio layer.

The active lower transport is MTK MD1 + CCCI + ttyC + GSM0710 mux + RIL fusion.
The active framework surface is Android telephony + MTK telephony + MTK IMS/WFO.
The active IMS/WFC surface is VoLTE daemon set + WFC/ePDG + IPsec/StrongSwan.
The active carrier/config surface is APN/SPN/vendor APN/virtual SPN/carrierconfig
plus APDB database files. The active ODM layer is Wiite UID1000 framework-level
telephony authority with phone/SMS/APN/WFC privileges. Adups/FOTA is retained as
a product-privileged radio-adjacent package with `READ_PRIVILEGED_PHONE_STATE`.

All active components, inactive ghost surfaces, security-relevant packages,
runtime properties, file identities, daemon mappings, and failed gates are
included and classified. No in-scope object is removed from this database section.

################################################################################
# END OF SECTION 08
################################################################################
