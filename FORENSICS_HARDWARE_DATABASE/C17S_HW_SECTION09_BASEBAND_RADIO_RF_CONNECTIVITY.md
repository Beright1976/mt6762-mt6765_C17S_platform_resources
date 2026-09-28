################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 9 — BASEBAND, RADIO, RF & CONNECTIVITY
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 9 — BASEBAND / RADIO / RF / CONNECTIVITY
# Closure     : CLOSED WITH PUBLIC-REDACTION AND PHYSICAL-RF OPEN LEADS
#
# PURPOSE:
# This section defines the baseband, modem firmware, cellular transport,
# radio-HAL, SIM/UICC, IMS/VoLTE/WFC, Wi-Fi, Bluetooth, GNSS, WMT/connsys,
# USB-networking, network-interface, and connectivity-control topology of the
# C17S platform.
#
# This is a hardware digital-twin section. It is not an app behavior report.
# It is not a ROM bring-up essay. It is not a threat conclusion. It records the
# physical and runtime connectivity surfaces exposed by this device and the
# exact classification of each surface.
#
# If a future recovery, clean AOSP ROM, GSI validation, device tree, kernel
# module set, SELinux policy, vendor-blob extraction, firmware audit, or public
# security report needs to preserve, emulate, restrict, replace, classify, or
# deliberately remove a baseband/RF/connectivity behavior, that information
# belongs here.
#
# SCOPE:
# Included:
# - MD1 modem firmware partition identity, geometry, and hash
# - radio-adjacent storage partitions: nvram, nvdata, nvcfg, protect1,
#   protect2, proinfo, seccfg
# - explicit absent modem partitions: md1dsp, md1arm7, md3img
# - /sys/kernel/ccci state, modem-count proof, LK modem enable state,
#   md1 postfix, CCCI version, and CCCI channel surface
# - CCCI, ttyC, PPP, radio, IMS, VoLTE, modem-status, raw modem, and raw USB
#   device nodes
# - RIL, MTK RadioEx, RadioOp, radio config, telephony binder, and HIDL/VINTF
#   exposure
# - SIM/UICC/SecureElement SIM1 exposure and single-SIM runtime state
# - LTE / IWLAN / VoLTE / WFC property and service state
# - IMS / SIP / VoLTE socket surfaces and process association boundary
# - MTK WMT/connsys parent plane
# - Wi-Fi gen4m driver, firmware, interface, supplicant, hostapd, p2p, and
#   SoftAP surfaces
# - Bluetooth WMT-dependent child path, kernel module, BTIF/STP device nodes,
#   HAL, framework manager, enabled runtime, profiles, and process ownership
# - GNSS/GPS WMT-dependent child path, kernel module, MNL firmware boundary,
#   GNSS HAL, LBS HAL, mnld, mtk_agpsd, and live GNSS fix evidence
# - USB HAL, MTP/ADB state, ttyGS gadget nodes, usb-ffs, accessory node, and
#   tethering boundary
# - APDB, APN, carrier-config, IMS-permission, Wi-Fi, Bluetooth, and AGPS
#   config artifacts
# - network interfaces: ccmni, wlan0, p2p0, ap0, ccmni-lan, ppp, tun, usb
#   related nodes
# - declared features, negative hardware declarations, BSP ghosts, open leads,
#   and public-redaction requirements
#
# Excluded:
# - Full visual/audio/input route mapping.
#   Bluetooth audio policy routes are cross-referenced only; Section 8 owns
#   audio-route classification.
#
# - Full nRF52832 wearable-sensor BLE command protocol.
#   Section 7 owns the nRF52832 coprocessor, UART1 bridge, I2C4 Wiite control
#   plane, and sensor-plane command protocol. This section records only the
#   MediaTek Android Bluetooth stack and the boundary with Wiite connectivity
#   control when it appears in connectivity logs.
#
# - Full APK permission or spyware-grade behavior mapping.
#   APK behavior belongs to DOC09 / software security maps. This section only
#   records connectivity services, HALs, sockets, device nodes, and runtime
#   control planes.
#
# - Full carrier/privacy data publication.
#   Subscriber identifiers, precise network identifiers, local IPs, Wi-Fi SSID,
#   Wi-Fi BSSID, Bluetooth peer addresses, and GNSS coordinates are captured in
#   raw private evidence but redacted from the public section body.
#
# - Full physical RF antenna and board trace proof.
#   RF chains are classified from live runtime, driver, HAL, and service proof.
#   Antenna routing remains a physical-board open lead unless separately proven.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S adb shell / su / Magisk root captures
# TIER 2 : /dev/block/by-name, resolved mmcblk0 partition nodes, block geometry
# TIER 3 : dd-streamed partition SHA256 hashes from resolved block nodes
# TIER 4 : /dev device-node inventory, /dev/socket inventory, /sys/kernel/ccci
# TIER 5 : /proc/modules, /vendor/lib/modules, module strings, firmware hashes
# TIER 6 : init rc files, service states, binder service list, lshal, VINTF
# TIER 7 : dumpsys phone, telephony.registry, wifi, bluetooth_manager, location
# TIER 8 : /sys/class, /sys/bus/platform, /sys/devices/platform connectivity tree
# TIER 9 : Closed C17S Sections 1-8 and C17S ROM/forensics documents
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# A partition exists only if it is present in /dev/block/by-name and resolves to
# a live block node.
#
# A modem exists only when firmware partition, CCCI state, runtime service, radio
# HAL, telephony framework, and network interface evidence agree.
#
# A CCCI MD2/MD3 status node is not proof of a physical second/third modem when
# /sys/kernel/ccci reports modem num:1 and the GPT contains only md1img.
#
# A HIDL/VINTF declaration is not physical RF proof by itself.
#
# A Wi-Fi, Bluetooth, or GPS feature is confirmed only when the module, device
# node, HAL/service, and runtime framework state are classified separately.
#
# A Wi-Fi P2P or SoftAP interface is not an active AP/client unless its interface
# state is up and carrier/route proof exists.
#
# A Bluetooth bonded device proves framework/profile behavior, not board-level
# peer hardware identity. Peer MACs are private and redacted.
#
# A GNSS fix proves GNSS runtime operation, not antenna trace topology.
#
# A socket on port 5060, 50006, or 50036 is classified as IMS/VoLTE-associated
# only when correlated with active VoLTE/IMS processes and radio state. Exact
# owner remains open unless ss/lsof exposes PID ownership.
#
# No stale values.
# No generic MTK reference values promoted to C17S truth.
# No ODM declaration is treated as physical truth without live proof.
# No suspicion is recorded as behavior.
# No subscriber/private identifiers are published unredacted.
################################################################################

################################################################################

# 9.0 — SECTION AUTHORITY / CLOSURE STATE

################################################################################

Section status:
Status                         : CLOSED WITH PUBLIC-REDACTION AND PHYSICAL-RF OPEN LEADS
Closure class                  : Evidence-backed baseband / radio / RF / connectivity map
Open-lead class                : physical RF antenna routing / exact IMS socket owner / optional FM RF proof
Hardware topology blockers     : none remaining for AP-mode Android connectivity mapping

Capture basis:
Device                         : LOKMAT APPLLP 5 MAX
Board                          : WP_C17S_PIX_TFT_D4
Kernel                         : Linux 4.9.190+
Android base                   : Android 10 / MTK Q BSP
Platform BSP identity          : mt6765
Physical AP silicon identity   : MT6762V/WB
Research state                 : rooted live stock device / Magisk true-root / SELinux permissive
Radio stack class              : full phone-class MTK radio/connectivity stack
Form factor                    : watch body / phone-class AP connectivity platform

Primary live surfaces used:
/dev/block/by-name
/dev/block/mmcblk0p8
/dev/block/mmcblk0p9
/dev/block/mmcblk0p12
/dev/block/mmcblk0p13
/dev/block/mmcblk0p14
/dev/block/mmcblk0p16
/dev/block/mmcblk0p17
/dev/block/mmcblk0p25
/sys/class/block/mmcblk0p*
/sys/kernel/ccci
/dev/ccci_*
/dev/ttyC*
/dev/radio
/dev/stpwmt
/dev/stpbt
/dev/stpgps
/dev/wmtWifi
/dev/wmtdetect
/dev/btif
/dev/gps_emi
/dev/fw_log_wmt
/dev/fw_log_wifi
/dev/fw_log_bt
/dev/fw_log_gps
/sys/class/stpwmt/stpwmt
/sys/class/stpbt/stpbt
/sys/class/stpgps/stpgps
/sys/class/wmtWifi/wmtWifi
/sys/class/wmtdetect/wmtdetect
/sys/class/btif/btif
/sys/class/fw_log_wmt/fw_log_wmt
/sys/class/fw_log_wifi/fw_log_wifi
/sys/class/fw_log_bt/fw_log_bt
/sys/class/gpsfwlog/fw_log_gps
/sys/class/gpsemi/gps_emi
/sys/class/net/ccmni0
/sys/class/net/ccmni1
/sys/class/net/ccmni2
/sys/class/net/ccmni-lan
/sys/class/net/wlan0
/sys/class/net/p2p0
/sys/class/net/ap0
/sys/bus/platform/devices/1100c000.btif
/sys/bus/platform/devices/18000000.wifi
/sys/devices/platform/CONNAC
/sys/devices/platform/CONNAC/net/wlan0
/proc/modules
/proc/net/dev
ip link / ip addr / ip route
getprop
service list
lshal
/vendor/etc/vintf/manifest.xml
/vendor/etc/init/*.rc
/vendor/etc/init/hw/init.modem.rc
/vendor/etc/init/hw/meta_init.modem.rc
/vendor/lib/modules
/vendor/firmware
/vendor/etc/apdb
/vendor/etc/wifi
/system/etc/bluetooth
/system/etc/apns-conf.xml
/vendor/etc/vendor-apns-conf.xml
dumpsys telephony.registry
dumpsys phone
dumpsys wifi
dumpsys bluetooth_manager
dumpsys location
ss -tunap
ps -A

Closure meaning:
The C17S baseband, cellular, Wi-Fi, Bluetooth, GNSS, WMT, USB, and Android
connectivity exposure is complete enough to understand the device six months
later without this chat history. Remaining open leads are physical-board RF
routing and process-owner granularity where the live command did not expose PID.

Not claimed by closure:
Every RF antenna trace is mapped.
Every modem NV item is decoded.
Every IMS socket has a PID owner.
Every carrier provisioning behavior is clean or dirty.
Every Wiite connectivity-control pin is net-named.
FM RF reception is proven.
5G/NR hardware exists.
A second physical modem exists.

################################################################################

# 9.1 — EVIDENCE CLASS LEGEND

################################################################################

CONFIRMED_PARTITION:
  A named partition exists in /dev/block/by-name and resolves to a block node.

CONFIRMED_GEOMETRY:
  Start sector, sector count, byte size, node, major/minor, and SELinux label are
  captured from the live block device.

CONFIRMED_HASH:
  SHA256 was captured from a resolved node or file path.

CONFIRMED_LIVE_RUNTIME:
  The service, HAL, process, framework, or interface is running/active in the
  captured Android runtime.

CONFIRMED_ENABLED_RUNTIME:
  The subsystem was explicitly enabled and framework state reports ON/active.

CONFIRMED_DEVICE_NODE:
  A /dev node exists with owner, group, major/minor, and SELinux context.

CONFIRMED_SYSFS:
  A /sys node or class path exists and links the runtime surface to a kernel or
  platform object.

CONFIRMED_KERNEL_MODULE:
  A kernel module appears in /proc/modules and/or exists as a vendor .ko file.

CONFIRMED_FILE:
  A firmware/config/binary file exists with path, size, SELinux label, and hash
  where captured.

HAL_DECLARED:
  A HAL is declared in VINTF or shown by lshal but is not necessarily physical
  proof by itself.

HAL_RUNNING:
  A HAL service process or init service is active.

BINDER_DECLARED:
  A framework binder service is published.

FRAMEWORK_DECLARED:
  Android framework properties or pm features declare capability.

NETWORK_INTERFACE:
  A Linux network interface exists. Carrier/up/down/routing are separate fields.

ACTIVE_NETWORK_INTERFACE:
  Interface state, carrier, route, or IP provisioning proves active network use.

WMT_CHILD_PATH:
  Wi-Fi, Bluetooth, or GPS path depends on the WMT/connsys parent plane.

BSP_GHOST_CANDIDATE:
  A node, service, route, or status surface exists due to MTK BSP expansion but
  does not prove populated physical hardware.

DECLARED_ONLY:
  Present only as a declaration/config/route with no live physical proof.

ABSENT_FROM_GPT:
  Named partition absent from /dev/block/by-name / GPT.

ABSENT_IN_CAPTURE:
  Not present in the live command capture.

OPEN_PHYSICAL_LEAD:
  Runtime exposure exists, but board trace, antenna, populated component, or
  physical endpoint remains unmapped.

REDACT_PUBLIC:
  Raw private value exists in evidence but must not be published unmasked.

CAPTURE_ARTIFACT:
  Command-output artifact not part of hardware truth, such as Magisk su usage
  banner caused by multi-line command quoting.

################################################################################

# 9.2 — CANONICAL CONNECTIVITY ARCHITECTURE SUMMARY

################################################################################

Application processor          : MediaTek MT6762V/WB
BSP/software identity          : MT6765
Baseband class                 : integrated MediaTek MD1 modem domain
Modem firmware partition       : md1img
Modem transport                : CCCI / ttyC / ccmni / modem RPC / modem FS
Cellular runtime               : single-SIM phone-class LTE stack
SIM count                      : 1
SIM runtime state              : LOADED in capture
Carrier class observed         : US GSM/LTE carrier, T-Mobile in private capture
Baseband version               : MOLY.LR12A.R3.MP.V107.4
RIL implementation prop        : android reference-ril 1.0
Vendor RIL daemon              : mtkfusionrild -l libmtk-ril.so
Radio HAL                      : android.hardware.radio family through 1.4
MTK radio extension            : vendor.mediatek.hardware.mtkradioex through 1.5
RadioOp                        : vendor.mediatek.hardware.radio_op@2.0
Radio config                   : android.hardware.radio.config@1.1
IMS/VoLTE                      : present / running / active services
WFC/IWLAN                      : declared and enabled in captured props
Cellular data interface        : ccmni0 active in capture

WMT/connsys parent             : wmt_drv.ko
Wi-Fi child module             : wmt_chrdev_wifi.ko + wlan_drv_gen4m.ko
Bluetooth child module         : bt_drv.ko
GPS/GNSS child module          : gps_drv.ko
WMT child-dependency trigger   : vendor.connsys.driver.ready=yes
Wi-Fi active interface         : wlan0
Wi-Fi direct interface         : p2p0
SoftAP/tethering interface     : ap0
Bluetooth Android stack        : MediaTek HCI HAL + framework bluetooth_manager
GNSS runtime                   : GNSS HAL + MTK LBS + mnld + mtk_agpsd
USB connectivity               : musb-hdrc / MTP+ADB / USB HAL 1.1 / ttyGS nodes
Secure element exposure        : SIM1 secure-element HAL declared

Canonical classification:
The C17S is not a reduced watch-only connectivity stack. It exposes a full
phone-class MTK radio/connectivity subsystem inside a watch-body product.

################################################################################

# 9.3 — MODEM FIRMWARE PARTITION REGISTER

################################################################################

Primary modem firmware partition:
Partition name                 : md1img
Classification                 : CONFIRMED_PARTITION / CONFIRMED_GEOMETRY / CONFIRMED_HASH
By-name path                   : /dev/block/by-name/md1img
Resolved node                  : /dev/block/mmcblk0p17
Block type                     : eMMC user-area GPT partition
SELinux label                  : u:object_r:md_block_device:s0
Owner/group                    : root:system
Permissions                    : brw-r-----
Major/minor decimal            : 179,17
Start sector                   : 518144
Sector count                   : 204800
Sector size                    : 512 bytes
Byte size                      : 104,857,600
Hex size                       : 0x06400000
SHA256                         : e78c1691a36ff9e08a335822645e9b801e14f4c4e8d9cf6ce383d8060bb170b6
Hash method                    : adb exec-out su -c 'dd if=/dev/block/mmcblk0p17 bs=1048576 2>/dev/null' | sha256sum

Absent modem partitions:
md1dsp                         : ABSENT_FROM_GPT
md1arm7                        : ABSENT_FROM_GPT
md3img                         : ABSENT_FROM_GPT

Interpretation boundary:
The modem firmware partition model is single-md1img for this GPT. No separate
md1dsp, md1arm7, or md3img partition is present. Do not import MTK reference BSP
partition assumptions unless a live GPT entry exists.

################################################################################

# 9.4 — RADIO-ADJACENT STORAGE PARTITION REGISTER

################################################################################

| Name     | Classification | Node | SELinux label | Major,minor | Start | Sectors | Bytes | SHA256 |
|----------|----------------|------|---------------|-------------|-------|---------|-------|--------|
| nvram    | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p25 | u:object_r:nvram_device:s0 | 179,25 | 798720 | 131072 | 67108864 | 5266ee901675a21eb2655aff0e84943917fd52e9b282f5aced4bf04ff9a7ee55 |
| nvdata   | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p9  | u:object_r:nvdata_device:s0 | 179,9  | 209984 | 131072 | 67108864 | fcd08fd1c1303c92241ef39dd478d8912f1a15481277a866a5ba90fb31912d1a |
| nvcfg    | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p8  | u:object_r:nvcfg_block_device:s0 | 179,8 | 144448 | 65536 | 33554432 | 5fe1127d5b291164e80df1985846b549853a4afc753e6ed39785ed663f6e840e |
| protect1 | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p12 | u:object_r:protect1_block_device:s0 | 179,12 | 452880 | 16384 | 8388608 | ab80b444ed334e9044bf251db8050c32927e2f1a180d242c2a11455274cbb10c |
| protect2 | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p13 | u:object_r:protect2_block_device:s0 | 179,13 | 469264 | 22256 | 11395072 | 80110e0c0aed8017bec5d82e09d65f474b85d64d3caf78abe86113ad78c236d0 |
| proinfo  | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p16 | u:object_r:nvram_device:s0 | 179,16 | 512000 | 6144 | 3145728 | d34d3939155d3db8f994e8c68b0fbefd86f7f847ae4b0b4b36691d0633403de3 |
| seccfg   | CONFIRMED_GEOMETRY / CONFIRMED_HASH | /dev/block/mmcblk0p14 | u:object_r:seccfg_block_device:s0 | 179,14 | 491520 | 16384 | 8388608 | 1452b719457a466d689cb3bf43acd9689b3b25a65229ba6e3a4b01dfefe9a381 |

Absent storage names in capture:
persist                         : ABSENT_FROM_GPT
secro                           : ABSENT_FROM_GPT

Storage boundary:
These partitions are radio-adjacent because modem, carrier, calibration,
identity, lock-state, NVRAM daemon, APDB schema, and telephony services consume
or protect values in this storage class. The exact private contents are not
published in this section.

################################################################################

# 9.5 — CCCI / MODEM KERNEL STATE REGISTER

################################################################################

/sys/kernel/ccci:
Classification                 : CONFIRMED_SYSFS / CONFIRMED_LIVE_RUNTIME
Path                           : /sys/kernel/ccci
Permissions                    : drwxr-xr-x root root

Captured files:
boot                           : writable by radio:radio
version                        : 6
kcfg_setting                   : [modem num]:1
lk_md                          : LK Load MD:[Enabled](0x00000001)
md1_postfix                    : 1_ulwtg_n
ft_info                        : ft_inf_ver:1
md_chn                         : live CCCI channel log data present
md_en                          : present
mdsys1                         : present child directory

Canonical interpretation:
CCCI version                   : 6
Modem count                    : 1
LK modem load                  : enabled
Active modem domain            : MD1
MD1 postfix                    : 1_ulwtg_n

Contradiction resolution:
CCCI exposes md1/md2/md3 status-style nodes, but /sys/kernel/ccci reports
modem num:1 and GPT exposes only md1img. Therefore md2/md3 status nodes are
classified as CCCI framework/BSP expansion surfaces, not proof of physical
second/third modem firmware partitions.

################################################################################

# 9.6 — CCCI DEVICE NODE REGISTER

################################################################################

CCCI core device nodes:
| Node | Owner | Group | SELinux label | Major,minor | Classification |
|------|-------|-------|---------------|-------------|----------------|
| /dev/ccci_aud | audio | audio | u:object_r:ccci_device:s0 | 239,1 | modem-audio CCCI node |
| /dev/ccci_fs | radio | radio | u:object_r:ccci_device:s0 | 239,4 | modem filesystem channel |
| /dev/ccci_rpc | radio | radio | u:object_r:ccci_device:s0 | 239,20 | modem RPC channel |
| /dev/ccci_md_log_ctrl | radio | radio | u:object_r:ccci_device:s0 | 239,2 | modem logging control |
| /dev/ccci_mdl_monitor | radio | radio | u:object_r:ccci_device:s0 | 239,19 | modem monitor |
| /dev/ccci_monitor | radio | radio | u:object_r:ccci_monitor_device:s0 | 240,0 | CCCI monitor |
| /dev/ccci_ioctl0 | radio | radio | u:object_r:ccci_device:s0 | 239,12 | CCCI ioctl |
| /dev/ccci_ioctl1 | radio | radio | u:object_r:ccci_device:s0 | 239,13 | CCCI ioctl |
| /dev/ccci_ioctl2 | radio | radio | u:object_r:ccci_device:s0 | 239,14 | CCCI ioctl |
| /dev/ccci_ioctl3 | radio | radio | u:object_r:ccci_device:s0 | 239,15 | CCCI ioctl |
| /dev/ccci_ioctl4 | radio | radio | u:object_r:ccci_device:s0 | 239,16 | CCCI ioctl |

IMS / VoLTE / modem-service CCCI nodes:
| Node | Major,minor | Classification |
|------|-------------|----------------|
| /dev/ccci_imsv | 239,8 | IMS voice / VoLTE channel surface |
| /dev/ccci_imsc | 239,9 | IMS control channel surface |
| /dev/ccci_imsa | 239,10 | IMS application channel surface |
| /dev/ccci_imsdc | 239,11 | IMS data/control channel surface |
| /dev/ccci_imsem | 239,21 | IMS emergency / extension channel surface |
| /dev/ccci_imsm | 239,22 | IMS modem manager channel surface |
| /dev/ccci_woa | 239,23 | WOA / Wi-Fi-offload-assist style channel surface |
| /dev/ccci_ss_xcap | 239,26 | supplementary service / XCAP channel surface |

C2K / legacy / BSP-expanded nodes:
| Node | Major,minor | Classification |
|------|-------------|----------------|
| /dev/ccci_c2k_ppp | 239,24 | legacy C2K PPP surface / BSP-expanded |
| /dev/ccci_c2k_agps | 239,25 | legacy C2K AGPS surface / BSP-expanded |
| /dev/ccci_bip | 239,27 | bearer-independent-protocol surface |

IPC / CCB / raw nodes:
| Node | Major,minor | Classification |
|------|-------------|----------------|
| /dev/ccci_ipc_1220_0 | 239,100 | CCCI IPC surface |
| /dev/ccci_ipc_2 | 239,102 | CCCI IPC surface |
| /dev/ccci_ipc_4 | 239,104 | CCCI IPC surface |
| /dev/ccci_ipc_5 | 239,105 | CCCI IPC surface |
| /dev/ccci_ipc_9 | 239,109 | CCCI IPC surface |
| /dev/ccci_ccb_dhl | 239,151 | CCB/DHL diagnostic/log channel |
| /dev/ccci_ccb_md_monitor | 239,152 | CCB modem monitor |
| /dev/ccci_ccb_meta | 239,153 | CCB META channel |
| /dev/ccci_ccb_ctrl | 239,154 | CCB control |
| /dev/ccci_raw_dhl | 239,155 | raw DHL diagnostic/log |
| /dev/ccci_raw_mdm | 239,156 | raw modem |
| /dev/ccci_raw_netd | 239,157 | raw netd modem channel |
| /dev/ccci_raw_usb | 239,158 | raw USB modem channel |
| /dev/ccci_raw_audio | 239,159 | raw audio modem channel |

Modem status nodes:
| Node | Major,minor | Classification |
|------|-------------|----------------|
| /dev/ccci_mdx_sta | 250,0 | generic modem status surface |
| /dev/ccci_md1_sta | 250,1 | MD1 status surface; active modem path |
| /dev/ccci_md2_sta | 250,2 | BSP-expanded status node; not physical MD2 proof |
| /dev/ccci_md3_sta | 250,3 | BSP-expanded status node; not physical MD3 proof |

TTY / mux / AGPS / USB nodes:
| Node | Owner | Group | SELinux label | Major,minor | Classification |
|------|-------|-------|---------------|-------------|----------------|
| /dev/ttyC0 | radio | radio | u:object_r:gsm0710muxd_device:s0 | 239,3 | GSM 0710 mux / RIL serial path |
| /dev/ttyC1 | radio | radio | u:object_r:mdlog_device:s0 | 239,7 | modem log tty |
| /dev/ttyC2 | radio | radio | u:object_r:agps_device:s0 | 239,5 | AGPS tty path |
| /dev/ttyC3 | radio | radio | u:object_r:icusb_device:s0 | 239,6 | modem ICUSB tty path |
| /dev/ttyC5 | radio | radio | u:object_r:nwkopt_device:s0 | 239,28 | network option tty path |
| /dev/ttyC6 | radio | radio | u:object_r:nlop_device:s0 | 239,29 | NLOP tty path |

Other related character nodes:
/dev/ppp                        : ppp_device, radio:vpn, major 108 minor 0
/dev/radio                      : radio-owned directory, device context
/dev/tun                        : tun_device, system:vpn, major 10 minor 200
/dev/ttyGS0-ttyGS3              : USB gadget serial nodes, system:radio

################################################################################

# 9.7 — MODEM INIT / SERVICE OWNERSHIP REGISTER

################################################################################

init.modem.rc:
Classification                 : CONFIRMED_FILE / INIT_OWNERSHIP
Path                           : /vendor/etc/init/hw/init.modem.rc
SHA256                         : f587b9a3f7900de9828cc19ac5774c8195a96ed541ce33788560437ab0c03cbf
Primary actions:
- early-init writes RPS CPU masks to ccmni0/ccmni1/ccmni2
- early-init writes RPS flow counts to ccmni0/ccmni1/ccmni2
- post-fs-data writes modem bootprof marker
- sets vold.post_fs_data_done=1

Services declared:
md_minilog_util                 : /vendor/bin/md_minilog_util; user radio; disabled; oneshot
viarild                         : /vendor/bin/viarild; disabled; oneshot; VIA/C2K legacy path
permission_check                : /vendor/bin/permission_check; user root; group system radio; oneshot
pppd_gprs                       : /system/bin/pppd_dt /dev/ttyACM0; disabled; oneshot
pppd                            : /system/bin/pppd file /data/misc/ppp/ppp_options; disabled; oneshot

meta_init.modem.rc:
Classification                 : CONFIRMED_FILE / INIT_OWNERSHIP
Path                           : /vendor/etc/init/hw/meta_init.modem.rc
SHA256                         : 0a7e4330aa8e226c0fc5b1a60ff2a69b942c9161b5d415f13743b4d58a8ab579
Primary actions:
- mkdir /mnt/vendor/nvdata/md 0770 root system
- mkdir /mnt/vendor/nvdata/md3 0770 root system
- mkdir /data/md3 0770 root system
- chown radio radio /sys/kernel/ccci/boot
- mkdir /data/ccci_cfg 0770 system radio
- restorecon /data/ccci_cfg
- restorecon_recursive /mnt/vendor/protect_f
- restorecon_recursive /mnt/vendor/protect_s
- setprop vold.post_fs_data_done=1

Services declared:
vendor.ccci_fsd                 : /vendor/bin/ccci_fsd 0; user radio; group radio system; oneshot
ccci_mdinit                     : /vendor/bin/ccci_mdinit 0; user system; group radio system; oneshot
vendor.ccci3_fsd                : /vendor/bin/ccci_fsd 2; user radio; group radio system; oneshot
ccci3_mdinit                    : /vendor/bin/ccci_mdinit 2; user system; group radio system; oneshot
ccci_rpcd                       : /vendor/bin/ccci_rpcd 0; user radio; group radio system; oneshot

Runtime service state:
init.svc.vendor.ccci_fsd        : running
init.svc.ccci_mdinit            : running
init.svc.vendor.ccci3_fsd       : running
init.svc.ccci3_mdinit           : stopped
init.svc.vendor.ccci_rpcd       : running
init.svc.vendor.ril-daemon-mtk  : running

Classification note:
ccci3_fsd is present/running and /mnt/vendor/nvdata/md3 is created, but the GPT
contains no md3img and /sys/kernel/ccci reports modem num:1. Therefore the MD3
userspace/storage scaffolding is BSP-expanded and must not be promoted to a
confirmed second modem.

################################################################################

# 9.8 — RADIO / RIL / TELEPHONY PROPERTY REGISTER

################################################################################

Radio identity:
Baseband                       : MOLY.LR12A.R3.MP.V107.4
RIL implementation             : android reference-ril 1.0
Vendor RIL active MD           : vendor.ril.active.md=10
MTK RIL flag                   : vendor.ril.mtk=1
World mode active mode         : vendor.ril.nw.worldmode.activemode=1
Packet switch RAT              : persist.vendor.radio.mtk_ps2_rat=L/W/G
Secondary RAT declaration      : persist.vendor.radio.mtk_ps3_rat=G
Default network                : ro.telephony.default_network=9,9,9,9

SIM / subscription state:
ro.telephony.sim.count         : 1
persist.radio.multisim.config  : ss
persist.vendor.radio.msimmode  : ss
persist.vendor.radio.sim.mode  : 1
persist.vendor.radio.simswitch : 1
gsm.sim.state                  : LOADED
gsm.current.phone-type         : 1
gsm.sim.operator.alpha         : T-Mobile in private capture
gsm.sim.operator.iso-country   : us
gsm.sim.operator.numeric       : 310260
vendor.ril.imsi.status.sim1    : 1
vendor.ril.sim.onoff.state1    : 11
vendor.ril.sim.onoff.support   : 1

Data / LTE / IWLAN:
gsm.defaultpdpcontext.active   : true
gsm.network.type               : IWLAN in capture
persist.vendor.radio.default.data.selected : 1
persist.vendor.radio.rilj_nw_type1         : 19
vendor.ril.data.allow_data_slot            : 0
vendor.ril.data.preferred_data_mode        : 1
vendor.ril.data.ssc.mode                   : 3
vendor.ril.nw.signalstrength.lte.1         : captured; REDACT_PUBLIC boundary

VoLTE / WFC / IWLAN:
persist.vendor.radio.volte_state           : 1
persist.vendor.radio.wfc_state             : 1
persist.vendor.mtk.viwifi.enable           : 1
persist.vendor.viwifi_support              : 1
ro.telephony.iwlan_operation_mode          : default
persist.vendor.radio.cfu_over_ims          : 0

Emergency / ECC:
ril.ecclist                    : 911,112
vendor.ril.ecc.service.category.mcc : 310

Private values captured but redacted:
ICCID                           : captured in raw evidence; REDACT_PUBLIC
IMSI-derived data               : captured in raw evidence; REDACT_PUBLIC
XUI/UT fields                   : captured in raw evidence; REDACT_PUBLIC
GID/PNN carrier blobs           : captured in raw evidence; REDACT_PUBLIC
Exact LTE signal tuple          : captured in raw evidence; may publish only generalized signal state

################################################################################

# 9.9 — RADIO HAL / HIDL / VINTF REGISTER

################################################################################

Android radio HAL:
android.hardware.radio          : HAL_DECLARED / HAL_RUNNING
IRadio instances                : slot1, imsAospSlot1, em1, se1
Declared versions observed      : 1.2 and 1.4 in manifest; broader 1.0-1.4 family visible in lshal capture
Radio config                    : android.hardware.radio.config@1.1::IRadioConfig/default

MTK RadioEx:
vendor.mediatek.hardware.mtkradioex : HAL_DECLARED
Version family                  : through 1.5
Instances                       : imsSlot1, mtkAssist1, mtkCap1, mtkEm1, mtkRcs1, mtkSe1, mtkSlot1

MTK RadioOp:
vendor.mediatek.hardware.radio_op@2.0 : HAL_DECLARED
Instances                       : OpImsRILd1, slot1

MTK network agent:
vendor.mediatek.hardware.netdagent@1.0::INetdagent/default : HAL_DECLARED / service running

NVRAM HAL:
vendor.mediatek.hardware.nvram@1.1::INvram/default : HAL_DECLARED
RC service                      : nvram-hidl-1-1
Binary                          : /vendor/bin/hw/vendor.mediatek.hardware.nvram@1.1-service

Secure element:
android.hardware.secure_element : HAL_DECLARED
SIM1 exposure                   : present in live HAL context
Classification                  : SIM/UICC secure-element exposure; not NFC proof

Wi-Fi HAL family:
android.hardware.wifi@1.3::IWifi/default                 : HAL_DECLARED
android.hardware.wifi.hostapd@1.1::IHostapd/default       : HAL_DECLARED
android.hardware.wifi.supplicant@1.2::ISupplicant/default : HAL_DECLARED
vendor.mediatek.hardware.wifi.supplicant@2.0::ISupplicant/default : HAL_DECLARED

Bluetooth HAL family:
android.hardware.bluetooth@1.0::IBluetoothHci/default : HAL_DECLARED / HAL_RUNNING
android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default : HAL_DECLARED / HAL_RUNNING

GNSS / LBS HAL family:
android.hardware.gnss@1.1::IGnss/default : HAL_DECLARED
android.hardware.gnss@2.0::IGnss/default : HAL_DECLARED / HAL_RUNNING
vendor.mediatek.hardware.lbs@1.0::ILbs/* : HAL_DECLARED
LBS instances include:
- AgpsDebugInterface
- AgpsInterface
- mtk_agps2framework
- mtk_framework2agps
- mtk_lbs_log_v2s
- mtk_lppe_socket_agps
- mtk_lppe_socket_bt
- mtk_lppe_socket_ipaddr
- mtk_lppe_socket_lbs
- mtk_lppe_socket_network
- mtk_lppe_socket_sensor
- mtk_lppe_socket_wlan
- mtk_meta2mnld
- mtk_debugService2mnld
- mtk_mnld2debugService
- mtk_mnld2mtklogger
- mtk_mnld2nlputils
- mtk_mtklogger2mnld

USB HAL:
android.hardware.usb@1.1::IUsb/default : HAL_DECLARED / vendor.usb-hal-1-1 running

################################################################################

# 9.10 — BINDER SERVICE REGISTER

################################################################################

Telephony / IMS binder services:
mtkIms                         : com.mediatek.ims.internal.IMtkImsService
carrier_config                 : com.android.internal.telephony.ICarrierConfigLoader
phoneEx                        : com.mediatek.internal.telephony.IMtkTelephonyEx
phone                          : com.android.internal.telephony.ITelephony
imtksms                        : com.mediatek.internal.telephony.IMtkSms
iphonesubinfoEx                : com.mediatek.internal.telephony.IMtkPhoneSubInfoEx
mtksimphonebook                : com.mediatek.internal.telephony.phb.IMtkIccPhoneBook
isms                           : com.android.internal.telephony.ISms
iphonesubinfo                  : com.android.internal.telephony.IPhoneSubInfo
simphonebook                   : com.android.internal.telephony.IIccPhoneBook
ircs                           : android.telephony.ims.aidl.IRcs
isubstub                       : com.mediatek.internal.telephony.IMtkSub
isub                           : com.android.internal.telephony.ISub
telephony.mtkregistry          : com.mediatek.internal.telephony.IMtkTelephonyRegistryEx
telephony.registry             : com.android.internal.telephony.ITelephonyRegistry
imms                           : com.android.internal.telephony.IMms

Connectivity binder services:
mtkconnmetrics                 : com.mediatek.net.connectivity.IMtkIpConnectivityMetrics
netd_listener                  : android.net.metrics.INetdEventListener
connmetrics                    : android.net.IIpConnectivityMetrics
connectivity                   : android.net.IConnectivityManager
netd                           : android.net.INetd

Wi-Fi binder services:
wifi                           : android.net.wifi.IWifiManager
wifiscanner                    : android.net.wifi.IWifiScanner
wificond                       : android.net.wifi.IWificond
wifip2p                        : android.net.wifi.p2p.IWifiP2pManager

Bluetooth binder services:
bluetooth_manager              : android.bluetooth.IBluetoothManager

Location/GNSS binder services:
location                       : android.location.ILocationManager
country_detector               : android.location.ICountryDetector
contexthub                     : android.hardware.location.IContextHubService

USB binder service:
usb                            : android.hardware.usb.IUsbManager

################################################################################

# 9.11 — WMT / CONNSYS PARENT PLANE

################################################################################

Parent module:
Module                         : wmt_drv
Runtime classification          : CONFIRMED_KERNEL_MODULE / WMT_PARENT
/proc/modules entry             : wmt_drv 1224704 5 wlan_drv_gen4m,wmt_chrdev_wifi,gps_drv,bt_drv, Live ... (O)
File path                       : /vendor/lib/modules/wmt_drv.ko
File size                       : 2,133,064 bytes
File label                      : u:object_r:vendor_file:s0
File SHA256                     : 2726de3e349b3cecfa533978bc5583c4c0e817c76e88c8a28bd409f7c121a785

WMT strings / behavior evidence:
- WMT-DETECT GPIO output/input strings present
- WIFI_EINT / BGF_EINT / PMU / RST pin configuration strings present
- combo chip detection strings present
- WMT power control strings present

WMT init:
RC file                         : /vendor/etc/init/init.wmt_drv.rc
Action                          : on boot insmod /vendor/lib/modules/wmt_drv.ko
Runtime services                : wmt_launcher running; wmt_loader stopped in captured service state
Binaries                        : /vendor/bin/wmt_launcher, /vendor/bin/wmt_loader

WMT child dependency rule:
Wi-Fi, Bluetooth, and GPS drivers load only after vendor.connsys.driver.ready=yes.

WMT device nodes:
/dev/stpwmt                     : system:system, u:object_r:stpwmt_device:s0, major 190 minor 0, hex major be
/dev/wmtWifi                    : wifi:wifi, u:object_r:wmtWifi_device:s0, major 153 minor 0, hex major 99
/dev/wmtdetect                  : system:system, u:object_r:wmtdetect_device:s0, major 154 minor 0, hex major 9a
/dev/fw_log_wmt                 : system:system, u:object_r:fw_log_wmt_device:s0, major 223 minor 0, hex major df

WMT sysfs/class topology:
/sys/class/stpwmt/stpwmt
/sys/class/wmtWifi/wmtWifi
/sys/class/wmtdetect/wmtdetect
/sys/class/fw_log_wmt/fw_log_wmt
/sys/bus/platform/drivers/mtk_wmt
/sys/devices/platform/CONNAC

Connectivity-control adjacent nodes:
/dev/wiite_con_ctrl             : root:root, u:object_r:device:s0, major 10 minor 50
/dev/wiite_corp_ctrl            : root:root, u:object_r:device:s0, major 10 minor 48

Observed Wiite connectivity-control log line:
[WIITE_CON_CTRL] Try to set pin(2) to state(1/0)

Classification:
WIITE_CON_CTRL activity is recorded as an observed board-control surface during
connectivity runtime logging. It is not classified as malicious behavior here.
Exact physical net name for pin(2) remains open.

################################################################################

# 9.12 — WI-FI SUBSYSTEM REGISTER

################################################################################

Wi-Fi module chain:
Parent                          : wmt_drv.ko
WMT Wi-Fi char module           : wmt_chrdev_wifi.ko
Wi-Fi driver module             : wlan_drv_gen4m.ko
Generation property             : ro.vendor.wlan.gen=gen4m

wmt_chrdev_wifi.ko:
Classification                  : CONFIRMED_KERNEL_MODULE / WMT_CHILD_PATH
File path                       : /vendor/lib/modules/wmt_chrdev_wifi.ko
Size                            : 57,720 bytes
SHA256                          : e01a7e1f3feae836bd6027f261d722eb08ebec89cd79078ec5b4b11988e7f594
vermagic                        : 4.9.190+ SMP preempt mod_unload modversions aarch64
license                         : Dual BSD/GPL
depends                         : wmt_drv
Strings                         : WMT turn on WIFI success/fail; wlan0; wmtWifi; mtk_wcn_wmt_func_on/off

wlan_drv_gen4m.ko:
Classification                  : CONFIRMED_KERNEL_MODULE / WMT_CHILD_PATH
File path                       : /vendor/lib/modules/wlan_drv_gen4m.ko
Size                            : 5,158,760 bytes
SHA256                          : f19c5675b02c6feb8e73c5d6ad31c23968c9e0931fc7a48229a72b76c2805175
Build path string               : WP_C17S_PIX_TFT_D4 / mt6765 / wlan_drv_gen4m.ko_intermediates

Wi-Fi init:
RC file                         : /vendor/etc/init/init.wlan_drv.rc
Trigger                         : on property:vendor.connsys.driver.ready=yes
Actions                         : insmod wmt_chrdev_wifi.ko; insmod wlan_drv_${ro.vendor.wlan.gen}.ko; start wlan_assistant
wlan_assistant RC               : /vendor/etc/init/wlan_assistant.rc
wlan_assistant service          : /vendor/bin/wlan_assistant; user wifi; group system gps inet

Wi-Fi HAL/service state:
init.svc.vendor.wifi_hal_legacy : running
init.svc.wificond               : running
init.svc.wlan_assistant         : running
init.svc.wpa_supplicant         : running
wificond binder                 : published
wifi binder                     : published
wifiscanner binder              : published
wifip2p binder                  : published

Wi-Fi properties:
wifi.interface                  : wlan0
wifi.active.interface           : wlan0
wifi.direct.interface           : p2p0
wifi.tethering.interface        : ap0
ro.vendor.wifi.sap.interface    : ap0
ro.mediatek.wlan.p2p            : 1
ro.mediatek.wlan.wsc            : 1
ro.default_wifi_hotspot         : APPLLP_5_MAX
wlan.driver.status              : ok
vendor.wlan.driver.version      : 1_0_1-20250613073152
vendor.wlan.firmware.version    : t-neptune-main-soc1_0e1-1916-CERVINO_SOC1_0_E1_ASIC-20200326221201
persist.vendor.connsys.wifi_fw_ver : 200326221201000

Wi-Fi interfaces:
wlan0                           : ACTIVE_NETWORK_INTERFACE
  state                         : UP / LOWER_UP
  mtu                           : 1500
  qdisc                         : mq
  IP address                    : captured; REDACT_PUBLIC
  IPv6 link-local               : captured; REDACT_PUBLIC
  active route                  : yes, default route captured through local gateway; REDACT_PUBLIC
  supplicant state              : COMPLETED
  framework state               : ConnectedState
  Wi-Fi mode                    : ClientModeActiveState
  RSSI                          : captured around -68 dBm in live sample
  link speed                    : 78 Mbps in framework sample; kernel station sample also reported higher PHY link value
  frequency                     : 2412 MHz
  country code                  : US

p2p0                            : NETWORK_INTERFACE / DECLARED_RUNTIME
  state                         : UP / NO-CARRIER / DOWN
  role                          : Wi-Fi Direct interface
  classification                : present, not active at capture

ap0                             : NETWORK_INTERFACE / DECLARED_RUNTIME
  state                         : DOWN
  role                          : SoftAP/tethering interface
  classification                : present, not active at capture

Wi-Fi sysfs topology:
/sys/class/net/wlan0
/sys/bus/platform/devices/18000000.wifi
/sys/bus/platform/drivers/wlan
/sys/devices/platform/CONNAC/net/wlan0
Wi-Fi regulator path            : mt-pmic / vcn33_wifi observed in sysfs search

Wi-Fi device/log nodes:
/dev/wmtWifi                    : wifi:wifi, u:object_r:wmtWifi_device:s0, 153,0
/dev/fw_log_wifi                : wifi:wifi, u:object_r:fw_log_wifi_device:s0, 219,0

Wi-Fi config / firmware files:
/vendor/firmware/WIFI_RAM_CODE_soc1_0_1_1.bin : size 612,652; SHA256 bffe8f712bf16666b5ccec1d7eb1149334e6b8f66652a68e4ebfb63347634566
/vendor/firmware/soc1_0_ram_wifi_1_1_hdr.bin  : size 195,228; SHA256 f99d7a212e4c4d00bf33f8241ff5bd4b8b93315558646e448c25a05762cc9191
/vendor/firmware/WMT_SOC.cfg                  : size 121; SHA256 6845e0f30219d5aedaf3791b8318eaaf7ae22d0087702268a815c8724243e2e8
/vendor/etc/wifi/wpa_supplicant.conf          : size 318; SHA256 20795c73c5f4b0a6a1ed33ea7bcd39ed7df21c255d5d7cd145922de29914a7cc
/vendor/etc/wifi/wpa_supplicant_overlay.conf  : size 127; SHA256 d5cc6c93e3c268d4ea58dc2f841e2bb63b4cdf47786379fcc828b71811706b77
/vendor/etc/wifi/p2p_supplicant_overlay.conf  : size 135; SHA256 b053c6ad5f23feec0b657aab58712f5375b803b9a93f489feede5931007c161e

Wi-Fi binaries:
/vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek : size 427,424; mtk_hal_wifi_exec
/vendor/bin/hostapd                                           : size 940,776; hal_wifi_hostapd_default_exec
/vendor/bin/wpa_supplicant                                    : size 2,594,800; hal_wifi_supplicant_default_exec
/vendor/bin/wpa_cli                                           : size 162,032
/vendor/bin/wlan_assistant                                    : size 15,848

Private Wi-Fi values captured but redacted:
SSID, BSSID, local MAC, randomized MAC, local IPv4, IPv6 address, gateway, DNS,
and local domain are not published in this section.

################################################################################

# 9.13 — BLUETOOTH SUBSYSTEM REGISTER

################################################################################

Bluetooth module:
Module                         : bt_drv
Classification                 : CONFIRMED_KERNEL_MODULE / WMT_CHILD_PATH / CONFIRMED_ENABLED_RUNTIME
/proc/modules entry             : bt_drv 28672 0 - Live ... (O)
Parent                          : wmt_drv
File path                       : /vendor/lib/modules/bt_drv.ko
Size                            : 52,216 bytes
SHA256                          : cb8de1956ff35f2d62b1a09313c1cd86d053f3bb0a3d075df5f6bcc99e27b0be
vermagic                        : 4.9.190+ SMP preempt mod_unload modversions aarch64
license                         : Dual BSD/GPL
depends                         : wmt_drv
Build path string               : WP_C17S_PIX_TFT_D4 / bt_drv.ko_intermediates / stp_chrdev_bt.c
Behavior strings                : WMT turn on BT OK/fail; STP ready/not ready; MTK Bluetooth Mode; HCI command timeout

Bluetooth init:
RC file                         : /vendor/etc/init/init.bt_drv.rc
Trigger                         : on property:vendor.connsys.driver.ready=yes
Action                          : insmod /vendor/lib/modules/bt_drv.ko
HAL RC                          : /vendor/etc/init/android.hardware.bluetooth@1.0-service-mediatek.rc
HAL service                     : bluetooth-1-0 /vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek
Service user/group              : bluetooth:bluetooth
Capability                      : SYS_NICE
Runtime service                 : init.svc.bluetooth-1-0=running

Bluetooth HAL / binder:
android.hardware.bluetooth@1.0::IBluetoothHci/default : HAL_RUNNING
android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default : HAL_RUNNING
Binder                          : bluetooth_manager published
Process                         : android.hardware.bluetooth@1.0-service-mediatek, PID 517 in capture
Framework process               : com.android.bluetooth, PID 24880 in capture
Kernel thread                   : [btif_rxd]

Bluetooth enabled runtime:
persist.vendor.bluetooth.state  : 1
Bluetooth manager enabled       : true
Bluetooth manager state         : ON
Adapter state                   : OnState
Scan mode                       : SCAN_MODE_CONNECTABLE
BLE started                     : yes
BR/EDR started                  : yes
Bluetooth crashed               : 0 times in capture
A2DP source                     : Enabled
A2DP sink                       : Disabled
Headset profile                 : created/started
PAN profile                     : present; tether off
GATT scanner                    : registered scanner present
AVRCP target                    : present
MAP/PBAP/OPP/SAP/HID profiles  : framework services exposed

Bluetooth device/log nodes:
/dev/btif                       : system:system, u:object_r:btif_device:s0, decimal 244,0, hex f4:0
/dev/stpbt                      : bluetooth:bluetooth, u:object_r:stpbt_device:s0, decimal 192,0, hex c0:0
/dev/fw_log_bt                  : bluetooth:bluetooth, u:object_r:fw_log_bt_device:s0, decimal 222,0, hex de:0

Bluetooth sysfs/class topology:
/sys/class/btif/btif
/sys/class/stpbt/stpbt
/sys/class/fw_log_bt/fw_log_bt
/sys/bus/platform/devices/1100c000.btif
/sys/bus/platform/drivers/mtk_btif

Bluetooth firmware/config:
/vendor/firmware/BT_FW.cfg                  : size 410; SHA256 e018d7b8153af454d7e5c51626d584582904ffc5ba2bd8edb5d2a1ed27124b67
/vendor/firmware/soc1_0_ram_bt_1_1_hdr.bin  : size 570,765; SHA256 36fcbd3e272fbd496c330f31f90ba31c07ee5a9d48888fefa2d27c352966ed19
/system/etc/bluetooth/mtk_bt_fw.conf        : size 1,475; SHA256 9c7d0a8f5edd4795c4a82242b9f488fd28fc1bad8030ed9f072726b2f3b94804
/system/etc/bluetooth/mtk_bt_stack.conf     : size 1,680; SHA256 2e1a86ea5bbb7ecfb6967914a8e0d7758871da3cf466dbda5022a11b6b524eb1
/system/etc/bluetooth/bt_did.conf           : present in inventory
/system/etc/bluetooth/bt_stack.conf         : present in inventory

Privacy boundary:
Adapter address, bonded-peer MAC, peer device name, and media-controller details
were visible in live output. They are private runtime values and are not needed
for hardware topology publication.

################################################################################

# 9.14 — GNSS / GPS / LBS SUBSYSTEM REGISTER

################################################################################

GNSS module:
Module                         : gps_drv
Classification                 : CONFIRMED_KERNEL_MODULE / WMT_CHILD_PATH / CONFIRMED_LIVE_RUNTIME
/proc/modules entry             : gps_drv 65536 0 - Live ... (O)
Parent                          : wmt_drv
File path                       : /vendor/lib/modules/gps_drv.ko
Size                            : 72,192 bytes
SHA256                          : 2e7e750e4db4debef2cb238c0ccabac9abc3627506c030d872f4473d3b846ff8
vermagic                        : 4.9.190+ SMP preempt mod_unload modversions aarch64
license                         : GPL
author strings                  : Heiping Lei / Chaoran Zhang / MediaTek
Description strings             : GPS EMI Driver; GPS FW log driver
Build path string               : WP_C17S_PIX_TFT_D4 / gps_drv.ko_intermediates / stp_chrdev_gps.c
Behavior strings                : GPS wakelock, GPS FW log, gps lna pin, get hw/fw version, COMBO_IOC_GPS_LISTEN_RST_EVT

GNSS init:
RC file                         : /vendor/etc/init/init.gps_drv.rc
Trigger                         : on property:vendor.connsys.driver.ready=yes
Action                          : insmod /vendor/lib/modules/gps_drv.ko
HAL RC                          : /vendor/etc/init/android.hardware.gnss@2.0-service-mediatek.rc
HAL service                     : gnss_service /vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek
Service user/group              : system:system gps
AGPS RC                         : /vendor/etc/init/mtk_agpsd_p.rc
AGPS service                    : agpsd /vendor/bin/mtk_agpsd; user gps; group gps radio inet sdcard_r sdcard_rw misc system
MNLD service                    : mnld running in process capture

GNSS HAL / framework:
android.hardware.gnss@2.0::IGnss/default : HAL_RUNNING
vendor.mediatek.hardware.lbs@1.0          : HAL_DECLARED
Location framework providers              : gps, network, fused, passive present in capture

GNSS properties:
vendor.gps.clock.type           : 21
vendor.gps.gps.version          : 0x6765
Feature declaration             : android.hardware.location.gps

GNSS device/log nodes:
/dev/stpgps                     : gps:gps, u:object_r:mnld_device:s0, decimal 191,0, hex bf:0
/dev/gps_emi                    : root:root, u:object_r:gps_emi_device:s0, decimal 221,0, hex dd:0
/dev/fw_log_gps                 : gps:gps, u:object_r:fw_log_gps_device:s0, decimal 220,0, hex dc:0
/dev/ttyC2                      : radio:radio, u:object_r:agps_device:s0, decimal 239,5

GNSS sysfs/class topology:
/sys/class/stpgps/stpgps
/sys/class/gpsemi/gps_emi
/sys/class/gpsfwlog/fw_log_gps

GNSS firmware/config:
/vendor/firmware/MNL.bin        : present in closed firmware evidence; SHA256 38723a2e5e8a17aa7950dc008209944e898f69a7bd10a23c839d341e935fd5ca
/system/etc/gps_debug.conf      : size 1,585; SHA256 336f1c49c575c018e1d07b107afd5db25cb1e8a22a7d4f5afa0a41c1c25220e7
/vendor/etc/agps_profiles_conf2.xml : size 27,012; SHA256 5d37714fbc3015728bcf358f48fc316c4d27a7ac3e211bdace0a5d918f362d36
MNL_AT.bin                      : absent in closed later evidence; do not list as present unless rediscovered

Live GNSS proof:
GNSS live classification         : CONFIRMED_LIVE_RUNTIME
Used-in-fix constellations       : GPS, GLONASS, BEIDOU, GALILEO
Satellite data block             : 32 satellites reported in native internal state
GNSS top HAL capabilities        : 0x746
Capability text in capture       : MSB, MSA, MEASUREMENTS, LOW_POWER_MODE, SATELLITE_BLACKLIST, MEASUREMENT_CORRECTIONS
SubHAL measurement corrections   : capabilities 0x7; LOS_SATS, EXCESS_PATH_LENGTH, REFLECTING_PLANE
Coordinates                      : captured; REDACT_PUBLIC
Altitude/accuracy                : captured; REDACT_PUBLIC

Physical boundary:
GNSS runtime is proven. Antenna trace, LNA net, and exact board routing remain
physical RF open leads unless proven by schematic/board probing or direct kernel
pin evidence. Section 6/older hardware map references GPIO91 as GNSS LNA control;
this section records it as a cross-reference, not a newly re-proven value.

################################################################################

# 9.15 — IMS / VoLTE / WFC / RADIO SOCKET REGISTER

################################################################################

VoLTE services:
volte_md_status                 : running; process root / volte_md_status
volte_imsm_93                   : running; process root / volte_imsm_93
vendor.volte_stack              : running; process root / volte_stack
vendor.volte_ua                 : running; process root / volte_ua
vendor.volte_imcb               : running; process system / volte_imcb in ps capture
com.mediatek.ims                : running under radio UID in process capture
com.android.phone               : running under radio UID in process capture

VoLTE RC files and hashes:
/vendor/etc/init/init.volte_imcb.rc      : SHA256 2b1a08e2e5e0eab9a78b62d366312e7c750af535083cec1a114b0b42ef1cf54e
/vendor/etc/init/init.volte_imsm_93.rc   : SHA256 ba22f7dfc06ec1f45529a2ee8ffeadb61a1092fcae1d1b08ef603f6e4cdbcf51
/vendor/etc/init/init.volte_md_status.rc : SHA256 21b8a8728d2291f216abe397799f936d8a2b2c127d9e6908b336dc86c0505bc2
/vendor/etc/init/init.volte_stack.rc     : SHA256 726bb222414b7af15ffa55148848e16c9dd5cab99bb2dbaa6894175b1b658f23
/vendor/etc/init/init.volte_ua.rc        : SHA256 1bd8bab39fa9c5c22ce035c6174b4dbd257d66e948279e2cf5463041df9a3266

VoLTE sockets:
/dev/socket/volte_imsa1          : root:radio, u:object_r:volte_imsa1_socket:s0
/dev/socket/volte_imsa2          : root:radio, u:object_r:socket_device:s0
/dev/socket/volte_imsvt1         : root:radio, u:object_r:volte_imsvt1_socket:s0
/dev/socket/volte_stack          : root:system, u:object_r:volte_stack_socket:s0
/dev/socket/volte_ua             : root:system, u:object_r:volte_ua_socket:s0
/dev/socket/volte_ut             : root:radio, u:object_r:socket_device:s0
/dev/socket/volte_vt             : root:system, u:object_r:volte_vt_socket:s0

Network socket surfaces:
Port 5060                       : UDP UNCONN and TCP LISTEN on device IPv6 address; IMS/SIP-associated surface
Port 50006                      : UDP UNCONN and TCP LISTEN on device IPv6 address; IMS/VoLTE-associated surface
Port 50036                      : UDP UNCONN and TCP ESTAB to private IPv6 IMS-side address; IMS/WFC-associated surface
Remote/private addresses         : REDACT_PUBLIC
Exact PID ownership              : OPEN_LEAD; ss did not expose process owner

Classification:
The IMS/VoLTE process set and port set correlate strongly, but exact socket PID
ownership is not claimed without a PID-bearing socket capture.

################################################################################

# 9.16 — MODEM / RADIO PROCESS REGISTER

################################################################################

Kernel threads:
[ccci_fsm1]                     : root kernel thread
[ccci_poll1]                    : root kernel thread
[ccci_rpc_k]                    : root kernel thread
[ccci_udc]                      : root kernel thread
[ccci_ipc_3]                    : root kernel thread
[ccci_ipc_6]                    : root kernel thread
[ccci_ipc_7]                    : root kernel thread
[ccci_ipc_8]                    : root kernel thread
[ccci_sys]                      : root kernel thread
[ccci_ctrl]                     : root kernel thread

Native processes:
volte_md_status                 : root, parent 1
netd                            : root, parent 1
ccci_fsd 0                      : radio, parent 1
ccci_fsd 2                      : radio, parent 1
ccci_mdinit 0                   : system, parent 1
ccci_rpcd 0                     : radio, parent 1
mnld                            : gps, parent 1
netdiag                         : shell, parent 1
mtk_agpsd                       : gps, parent 1
netdagent                       : system, parent 1
gsm0710muxd                     : radio, parent 1, args -s /dev/ttyC0 -f 512 -n 8 -m basic
volte_imsm_93                   : root, parent 1
volte_stack                     : root, parent 1
volte_ua                        : root, parent 1
volte_imcb                      : system, parent 1
mtkfusionrild                   : radio, parent 1, args -l libmtk-ril.so

Framework/app processes:
com.android.phone               : radio UID
com.mediatek.ims                : radio UID
com.android.bluetooth           : bluetooth UID when Bluetooth enabled

################################################################################

# 9.17 — NETWORK INTERFACE REGISTER

################################################################################

Cellular interfaces:
ccmni0                          : ACTIVE_NETWORK_INTERFACE
  sysfs                         : /sys/class/net/ccmni0 -> ../../devices/virtual/net/ccmni0
  address                       : captured; REDACT_PUBLIC
  carrier                       : 1
  mtu                           : 1280
  flags                         : 0x81
  ifindex                       : 2
  type                          : 520
  uevent                        : INTERFACE=ccmni0
  classification                : active cellular packet-data interface

ccmni1                          : NETWORK_INTERFACE
  sysfs                         : /sys/class/net/ccmni1
  carrier                       : empty
  mtu                           : 1500
  flags                         : 0x80
  operstate                     : down
  type                          : 520
  classification                : present but inactive cellular/modem interface

ccmni2                          : NETWORK_INTERFACE
  sysfs                         : /sys/class/net/ccmni2
  carrier                       : empty
  mtu                           : 1500
  flags                         : 0x80
  operstate                     : down
  type                          : 520
  classification                : present but inactive cellular/modem interface

ccmni3-ccmni20                  : NETWORK_INTERFACE / CCCI_EXPANDED
  classification                : CCCI modem interface expansion; not individually active in captured summary

ccmni-lan                       : NETWORK_INTERFACE
  sysfs                         : /sys/class/net/ccmni-lan
  mtu                           : 1500
  flags                         : 0x1002
  operstate                     : down
  type                          : 1
  classification                : present but inactive modem-LAN style interface

Wi-Fi interfaces:
wlan0                           : ACTIVE_NETWORK_INTERFACE
p2p0                            : present, down/no-carrier
ap0                             : present, down

Tunnels / virtual / gadget:
tun                             : /dev/tun present; system:vpn
ppp                             : /dev/ppp present; radio:vpn
usb-ffs                         : /dev/usb-ffs present
ttyGS0-ttyGS3                   : USB gadget serial nodes, system:radio
usb_accessory                   : /dev/usb_accessory present

Routing summary:
Both cellular (`ccmni0`) and Wi-Fi (`wlan0`) network surfaces exist. Wi-Fi had
active DHCP and default route in the Wi-Fi capture. Cellular `ccmni0` had carrier
and LTE/IWLAN service state. Exact addresses/routes are private and redacted.

################################################################################

# 9.18 — USB CONNECTIVITY REGISTER

################################################################################

USB properties:
sys.usb.config                  : mtp,adb
sys.usb.state                   : mtp,adb
sys.usb.configfs                : 1
sys.usb.controller              : musb-hdrc
sys.usb.ffs.ready               : 1
sys.usb.ffs.aio_compat          : 1

USB services:
init.svc.vendor.usb-hal-1-1     : running
init.svc.usbd                   : stopped

USB HAL:
android.hardware.usb@1.1::IUsb/default : HAL_DECLARED / HAL_RUNNING
Binary                          : /vendor/bin/hw/android.hardware.usb@1.1-service-mediatek
Binary size                     : 38,832 bytes
SELinux label                   : u:object_r:mtk_hal_usb_exec:s0

USB device nodes:
/dev/usb_accessory              : root:usb, u:object_r:usbaccessory_device:s0, 10,11
/dev/usb-ffs                    : shell:shell directory
/dev/ttyGS0                     : system:radio, u:object_r:ttyGS_device:s0, 232,0
/dev/ttyGS1                     : system:radio, u:object_r:ttyGS_device:s0, 232,1
/dev/ttyGS2                     : system:radio, u:object_r:ttyGS_device:s0, 232,2
/dev/ttyGS3                     : system:radio, u:object_r:ttyGS_device:s0, 232,3
/dev/mtp_usb                    : root:mtp, u:object_r:mtp_device:s0, 10,10

Tethering / RNDIS classification:
USB MTP/ADB is confirmed. USB HAL is confirmed. RNDIS/tethering is not confirmed
active in the captured state. Do not classify USB tethering as active without a
separate RNDIS/network interface capture.

################################################################################

# 9.19 — FIRMWARE / CONFIG / DATABASE ARTIFACT REGISTER

################################################################################

Modem / APDB / carrier files:
| Path | Size | SHA256 | Classification |
|------|------|--------|----------------|
| /vendor/etc/apdb/APDB_MT6765_S01__W2014 | 193425 | 7fcf0dd7ea42291d90d707036ee87e8ae3fb90a806a407604241a008bad8d5d7 | APDB modem database |
| /vendor/etc/apdb/APDB_MT6765_S01__W2014_ENUM | 20797 | 16b0c1e32cb3b4804a1cb16f7cf7bd395027c41773b6a8fc5e63c5c193d4fa51 | APDB enum database |
| /system/etc/apns-conf.xml | 557029 | 6d46ca610cfb3d91578fecf553b56c5fdc5281220ce12d0e4664802796430b17 | APN database |
| /vendor/etc/vendor-apns-conf.xml | 7409 | 60aeb604d2977913ba2681a3422faa262149cc581e9f62d1ddefc83044abcfe8 | vendor APN database |
| /vendor/etc/virtual-spn-conf-by-imsi.xml | 196 | e9bffdd5b881b518e35767cbe6856361b0f9415a95d130e8433a94c00dafd30e | virtual SPN by IMSI config |
| /product/etc/permissions/com.android.carrierconfig.xml | 869 | 248978a11956f56667e3b4708603c811defa4eea2e069a7459d03473fbb2ea95 | carrier config permission |
| /vendor/etc/permissions/android.hardware.telephony.ims.xml | 821 | 6f0d8f1e1dc0657e39ff63129b1633fdd9971417dcc9982271f089024666dadd | IMS feature declaration |
| /system/etc/permissions/com.mediatek.op.ims.common.xml | 826 | ee8445b511ac2fc5fd3c47b92c5d038f232b7adcd171160902192c9a20470c2f | MTK IMS common permission |

NVRAM / init configs:
| Path | Size | SHA256 | Classification |
|------|------|--------|----------------|
| /vendor/etc/init/fuelgauged_nvram_init.rc | 115 | 2bc23468174e2bea32e54c6a382c4f97cce17ca6255b83756e309c66a12514b8 | NVRAM-adjacent init |
| /vendor/etc/init/nvram_daemon.rc | 136 | a6cd1a716873790eb49e1abaf1b5f9ebf91509abc9cfdd2c68abf451deaae23a | nvram_daemon init |
| /vendor/etc/init/vendor.mediatek.hardware.nvram@1.1-sevice.rc | 153 | b75822d2a39f1bf11d1086eca6fdc120322232c85f6914921817810e0430ece6 | NVRAM HIDL init |

Wi-Fi / Bluetooth / GNSS files:
| Path | Size | SHA256 | Classification |
|------|------|--------|----------------|
| /vendor/firmware/BT_FW.cfg | 410 | e018d7b8153af454d7e5c51626d584582904ffc5ba2bd8edb5d2a1ed27124b67 | Bluetooth firmware config |
| /vendor/firmware/WIFI_RAM_CODE_soc1_0_1_1.bin | 612652 | bffe8f712bf16666b5ccec1d7eb1149334e6b8f66652a68e4ebfb63347634566 | Wi-Fi firmware |
| /vendor/firmware/WMT_SOC.cfg | 121 | 6845e0f30219d5aedaf3791b8318eaaf7ae22d0087702268a815c8724243e2e8 | WMT SOC config |
| /vendor/firmware/soc1_0_ram_bt_1_1_hdr.bin | 570765 | 36fcbd3e272fbd496c330f31f90ba31c07ee5a9d48888fefa2d27c352966ed19 | Bluetooth firmware blob |
| /vendor/firmware/soc1_0_ram_wifi_1_1_hdr.bin | 195228 | f99d7a212e4c4d00bf33f8241ff5bd4b8b93315558646e448c25a05762cc9191 | Wi-Fi firmware header/blob |
| /system/etc/bluetooth/mtk_bt_fw.conf | 1475 | 9c7d0a8f5edd4795c4a82242b9f488fd28fc1bad8030ed9f072726b2f3b94804 | MTK BT firmware config |
| /system/etc/bluetooth/mtk_bt_stack.conf | 1680 | 2e1a86ea5bbb7ecfb6967914a8e0d7758871da3cf466dbda5022a11b6b524eb1 | MTK BT stack config |
| /system/etc/gps_debug.conf | 1585 | 336f1c49c575c018e1d07b107afd5db25cb1e8a22a7d4f5afa0a41c1c25220e7 | GPS debug config |
| /vendor/etc/agps_profiles_conf2.xml | 27012 | 5d37714fbc3015728bcf358f48fc316c4d27a7ac3e211bdace0a5d918f362d36 | AGPS profiles config |
| /vendor/etc/wifi/wpa_supplicant.conf | 318 | 20795c73c5f4b0a6a1ed33ea7bcd39ed7df21c255d5d7cd145922de29914a7cc | Wi-Fi supplicant base config |
| /vendor/etc/wifi/wpa_supplicant_overlay.conf | 127 | d5cc6c93e3c268d4ea58dc2f841e2bb63b4cdf47786379fcc828b71811706b77 | Wi-Fi supplicant overlay |
| /vendor/etc/wifi/p2p_supplicant_overlay.conf | 135 | b053c6ad5f23feec0b657aab58712f5375b803b9a93f489feede5931007c161e | P2P supplicant overlay |

Additional present config files from inventory:
/system/etc/bluetooth/bt_did.conf
/system/etc/bluetooth/bt_stack.conf
/system/etc/bluetooth_audio_policy_configuration.xml
/system/etc/audio_policy_configuration_bluetooth_legacy_hal.xml
/system/etc/init/wifi-events.rc
/system/etc/init/wificond.rc
/vendor/etc/audio_param/BtInfo_AudioParam.xml
/vendor/etc/audio_param/BtInfo_ParamUnitDesc.xml
/vendor/etc/bluetooth_audio_policy_configuration.xml
/vendor/etc/audio_policy_configuration_bluetooth_legacy_hal.xml
/vendor/etc/permissions/android.hardware.bluetooth.xml
/vendor/etc/permissions/android.hardware.bluetooth_le.xml
/vendor/etc/permissions/android.hardware.location.gps.xml
/vendor/etc/permissions/android.hardware.wifi.xml
/vendor/etc/permissions/android.hardware.wifi.direct.xml
/vendor/etc/permissions/android.hardware.wifi.passpoint.xml

################################################################################

# 9.20 — CONNECTIVITY BINARY REGISTER

################################################################################

Vendor/system connectivity binaries:
| Path | Size | SELinux label / class | Classification |
|------|------|-----------------------|----------------|
| /vendor/bin/wlan_assistant | 15848 | vendor binary | Wi-Fi assistant service |
| /vendor/bin/wmt_launcher | 37104 | u:object_r:mtk_wmt_launcher_exec:s0 | WMT launcher |
| /vendor/bin/wmt_loader | 15672 | u:object_r:wmt_loader_exec:s0 | WMT loader |
| /vendor/bin/wpa_cli | 162032 | vendor_file | Wi-Fi CLI helper |
| /vendor/bin/hw/android.hardware.bluetooth@1.0-service-mediatek | 11512 | u:object_r:mtk_hal_bluetooth_exec:s0 | Bluetooth HAL |
| /vendor/bin/hw/android.hardware.gnss@2.0-service-mediatek | 11536 | u:object_r:mtk_hal_gnss_exec:s0 | GNSS HAL |
| /vendor/bin/hw/android.hardware.usb@1.1-service-mediatek | 38832 | u:object_r:mtk_hal_usb_exec:s0 | USB HAL |
| /vendor/bin/hw/android.hardware.wifi@1.0-service-lazy-mediatek | 427424 | u:object_r:mtk_hal_wifi_exec:s0 | Wi-Fi HAL |
| /vendor/bin/hostapd | 940776 | u:object_r:hal_wifi_hostapd_default_exec:s0 | SoftAP / hostapd |
| /vendor/bin/mtkfusionrild | 20168 | u:object_r:rild_exec:s0 | MTK RIL daemon |
| /vendor/bin/wpa_supplicant | 2594800 | u:object_r:hal_wifi_supplicant_default_exec:s0 | Wi-Fi supplicant |
| /vendor/bin/mtk_agpsd | captured path | gps service | AGPS daemon |
| /vendor/bin/ccci_fsd | captured path | radio service | CCCI filesystem daemon |
| /vendor/bin/ccci_mdinit | captured path | system/radio service | modem init daemon |
| /vendor/bin/ccci_rpcd | captured path | radio service | CCCI RPC daemon |
| /vendor/bin/volte_imsm_93 | captured path | root service | VoLTE IMSM daemon |
| /vendor/bin/volte_stack | captured path | root service | VoLTE stack |
| /vendor/bin/volte_ua | captured path | root service | VoLTE UA |
| /vendor/bin/volte_imcb | captured path | system/root declared | VoLTE IMCB |

################################################################################

# 9.21 — FEATURE DECLARATION REGISTER

################################################################################

Declared Android features:
android.hardware.bluetooth       : present
android.hardware.bluetooth_le    : present
android.hardware.location        : present
android.hardware.location.gps    : present
android.hardware.location.network: present
android.hardware.telephony       : present
android.hardware.telephony.gsm   : present
android.hardware.telephony.ims   : present
android.hardware.usb.accessory   : present
android.hardware.usb.host        : present
android.hardware.wifi            : present
android.hardware.wifi.direct     : present
android.hardware.wifi.passpoint  : present

Not declared in captured feature list:
android.hardware.nfc             : ABSENT_IN_CAPTURE
android.hardware.nfc.any         : ABSENT_IN_CAPTURE
android.hardware.uwb             : ABSENT_IN_CAPTURE
android.hardware.ethernet        : ABSENT_IN_CAPTURE
android.hardware.telephony.cdma  : ABSENT_IN_CAPTURE
android.hardware.telephony.euicc : ABSENT_IN_CAPTURE
android.hardware.telephony.5g    : ABSENT_IN_CAPTURE
android.hardware.radio.fm        : ABSENT_IN_CAPTURE

Feature boundary:
The captured feature list proves phone-class GSM/LTE/IMS, Wi-Fi, Wi-Fi Direct,
Passpoint, Bluetooth, BLE, GPS, network location, USB host, and USB accessory.
It does not prove NFC, UWB, Ethernet, eSIM, 5G/NR, CDMA feature publication, or
FM RF capability.

################################################################################

# 9.22 — NEGATIVE / BSP-GHOST / DECLARED-ONLY REGISTER

################################################################################

Absent modem partitions:
md1dsp                          : ABSENT_FROM_GPT
md1arm7                         : ABSENT_FROM_GPT
md3img                          : ABSENT_FROM_GPT

BSP-expanded modem surfaces:
/dev/ccci_md2_sta               : BSP_GHOST_CANDIDATE / status node only
/dev/ccci_md3_sta               : BSP_GHOST_CANDIDATE / status node only
vendor.ccci3_fsd                : BSP-expanded CCCI3 filesystem daemon; running
ccci3_mdinit                    : stopped in capture
/mnt/vendor/nvdata/md3           : created by init; not proof of md3img partition
/data/md3                        : created by init; not proof of md3img partition

Legacy/C2K surfaces:
viarild                         : declared disabled/oneshot
/dev/ccci_c2k_ppp               : legacy C2K surface; no CDMA feature declaration
/dev/ccci_c2k_agps              : legacy C2K surface; no CDMA feature declaration
persist/vendor CDMA props       : BSP/legacy property class unless supported by feature/runtime proof

FM boundary:
FM audio PCM routes are known from audio topology in other sections, but Section
9 did not capture an FM feature declaration, FM kernel module, FM service, or FM
RF tuning proof. Therefore FM remains OPEN_PHYSICAL_LEAD / DECLARED_AUDIO_ROUTE_ONLY.

NFC boundary:
No NFC feature declaration captured. SecureElement SIM1 is UICC/SIM exposure and
must not be reclassified as NFC.

5G / NR boundary:
No 5G/NR feature declaration captured. Radio stack is LTE/GSM/IMS-class in the
captured state.

Dual-SIM / eSIM boundary:
ro.telephony.sim.count=1 and persist.radio.multisim.config=ss. No eSIM feature
captured. Dual-SIM is not confirmed.

Ethernet / RNDIS boundary:
No Ethernet feature declaration captured. USB MTP/ADB and USB HAL are confirmed;
RNDIS/tethering is not confirmed active.

################################################################################

# 9.23 — PRIVACY / PUBLIC REDACTION REGISTER

################################################################################

Do not publish unmasked:
- ICCID
- IMSI or IMSI-derived fields
- SIM XUI / UT private blobs
- GID / PNN / carrier-private identifiers
- exact LTE signal/cell identifiers if tied to user location
- Wi-Fi SSID
- Wi-Fi BSSID
- Wi-Fi local MAC / randomized MAC
- local IPv4 / IPv6 addresses
- local gateway / DNS / domain
- Bluetooth adapter address
- Bluetooth bonded-peer MAC
- Bluetooth peer device name unless user explicitly wants it public
- media-controller content from Bluetooth AVRCP dumpsys
- GNSS coordinates
- GNSS altitude / exact fix time tied to location
- private IPv6 IMS/WFC endpoint addresses

Publication rule:
The section may publish capability, topology, interface names, service names,
partition geometry, hashes, HALs, modules, and classification. It must not
publish subscriber, location, peer, or home-network identifiers unmasked.

################################################################################

# 9.24 — CROSS-SECTION OWNERSHIP MAP

################################################################################

Section 1 consumes:
- physical SoC identity MT6762V/WB
- BSP platform identity MT6765

Section 2 consumes:
- GPT partition identity
- by-name and mmcblk0 partition map
- absent md1dsp/md1arm7/md3img/persist/secro truth

Section 3 consumes:
- firmware execution domain order
- MD1 baseband firmware domain
- WMT/connsys firmware domain
- GNSS firmware-domain boundary

Section 4 consumes:
- bus/controller ownership
- USB/musb-hdrc
- UART/tty paths
- DMA/GCE/platform interconnects

Section 5 consumes:
- IRQ/wake ownership for WMT, CCCI, BTIF, Wi-Fi, GPS, USB, and network wakes

Section 6 consumes:
- regulators, PMIC rails, Wi-Fi/BT/GPS power, VCN33 Wi-Fi path, and RF power rails

Section 7 owns:
- nRF52832 BLE/sensor coprocessor
- UART1 / ttyS1 Wiite bridge
- I2C4 wiite_corp_ctrl / wiite_con_ctrl sensor plane
- wearable low-power/sport-mode coprocessor behavior

Section 8 owns:
- Bluetooth audio policy routes
- audio PCM routes including FM audio-only surfaces
- headset/ACCDET audio boundaries

Section 9 owns:
- MediaTek Android Bluetooth stack
- MediaTek WMT/connsys Wi-Fi/BT/GPS parent plane
- modem/RIL/IMS/radio/HAL/SIM/connectivity/networking surfaces

Section 12 will consume:
- negative hardware, BSP ghosts, public redaction, and unresolved physical leads

################################################################################

# 9.25 — FINAL CLASSIFICATION TABLE

################################################################################

| Subsystem | Classification | Closure |
|-----------|----------------|---------|
| MD1 modem firmware | CONFIRMED_PARTITION / CONFIRMED_HASH | closed |
| md1dsp | ABSENT_FROM_GPT | closed negative |
| md1arm7 | ABSENT_FROM_GPT | closed negative |
| md3img | ABSENT_FROM_GPT | closed negative |
| nvram/nvdata/nvcfg/protect/proinfo/seccfg | CONFIRMED_GEOMETRY / CONFIRMED_HASH | closed |
| CCCI core | CONFIRMED_DEVICE_NODE / CONFIRMED_SYSFS / CONFIRMED_LIVE_RUNTIME | closed |
| CCCI MD2/MD3 status nodes | BSP_GHOST_CANDIDATE | closed classification |
| single SIM | CONFIRMED_LIVE_RUNTIME / FRAMEWORK_DECLARED | closed with redaction |
| LTE/GSM radio | CONFIRMED_LIVE_RUNTIME | closed with RF physical lead |
| IMS/VoLTE | CONFIRMED_LIVE_RUNTIME | closed with socket-owner open lead |
| WFC/IWLAN | FRAMEWORK_DECLARED / SERVICE_RUNTIME | closed with redaction |
| Wi-Fi wlan0 | CONFIRMED_ENABLED_RUNTIME / ACTIVE_NETWORK_INTERFACE | closed with redaction |
| Wi-Fi p2p0 | NETWORK_INTERFACE / DECLARED_RUNTIME | present, inactive at capture |
| Wi-Fi ap0 | NETWORK_INTERFACE / DECLARED_RUNTIME | present, inactive at capture |
| WMT parent | CONFIRMED_KERNEL_MODULE / CONFIRMED_DEVICE_NODE / CONFIRMED_SYSFS | closed |
| Bluetooth | CONFIRMED_ENABLED_RUNTIME / HAL_RUNNING / WMT_CHILD_PATH | closed with redaction |
| GNSS/GPS | CONFIRMED_LIVE_RUNTIME / HAL_RUNNING / WMT_CHILD_PATH | closed with coordinate redaction |
| USB MTP/ADB | CONFIRMED_LIVE_RUNTIME | closed |
| USB tether/RNDIS | ABSENT_IN_ACTIVE_CAPTURE | not claimed |
| SecureElement SIM1 | HAL_DECLARED / SIM-UICC boundary | closed classification |
| NFC | ABSENT_IN_FEATURE_CAPTURE | negative |
| UWB | ABSENT_IN_FEATURE_CAPTURE | negative |
| Ethernet | ABSENT_IN_FEATURE_CAPTURE | negative |
| 5G/NR | ABSENT_IN_FEATURE_CAPTURE | negative |
| eSIM | ABSENT_IN_FEATURE_CAPTURE | negative |
| FM RF | OPEN_PHYSICAL_LEAD / audio-route-only elsewhere | not proven |
| Wiite pin2 connectivity-control toggles | OPEN_PHYSICAL_LEAD | net name unresolved |

################################################################################

# 9.26 — OPEN LEAD REGISTER

################################################################################

S09-OPEN-001 — Exact IMS/VoLTE socket PID ownership
Status                         : OPEN
Evidence                       : ports 5060, 50006, 50036 visible; VoLTE/IMS processes active
Missing                        : PID/program owner from ss/lsof/netstat with process info
Impact                         : not a topology blocker
Classification                 : process attribution refinement

S09-OPEN-002 — Physical RF antenna routing
Status                         : OPEN
Evidence                       : LTE/Wi-Fi/BT/GNSS runtime confirmed
Missing                        : board trace / antenna feed / RF switch / matching-network map
Impact                         : not an Android connectivity topology blocker
Classification                 : physical-board mapping lead

S09-OPEN-003 — FM RF capability
Status                         : OPEN
Evidence                       : audio PCM FM routes exist in audio topology; no Section 9 FM feature/module/service proof
Missing                        : FM driver/service/tuner/antenna proof
Impact                         : not a phone/Wi-Fi/BT/GNSS connectivity blocker
Classification                 : optional feature lead

S09-OPEN-004 — Wiite connectivity-control pin(2) net name
Status                         : OPEN
Evidence                       : WIITE_CON_CTRL pin(2) toggles observed during Wi-Fi/runtime logs
Missing                        : exact board net/function assignment
Impact                         : preserve as observed control surface until mapped
Classification                 : board-control lead

S09-OPEN-005 — SecureElement SIM1 physical/use boundary
Status                         : OPEN_CLASSIFICATION
Evidence                       : secure_element SIM1 HAL declared/exposed
Missing                        : complete OMAPI/UICC use-case mapping
Impact                         : not NFC proof; not a blocker
Classification                 : UICC/secure-element boundary lead

################################################################################

# 9.27 — CLOSED NEGATIVE REGISTER

################################################################################

Closed negatives:
md1dsp partition                : absent
md1arm7 partition               : absent
md3img partition                : absent
persist partition               : absent
secro partition                 : absent
NFC feature                     : absent in pm feature capture
UWB feature                     : absent in pm feature capture
Ethernet feature                : absent in pm feature capture
5G/NR feature                   : absent in pm feature capture
eSIM feature                    : absent in pm feature capture
Dual-SIM runtime                : not supported by captured single-SIM properties
Bluetooth absent                : false; Bluetooth is confirmed live enabled
Wi-Fi absent                    : false; Wi-Fi is confirmed live connected
GNSS absent                     : false; GNSS is confirmed live
Phone stack fake                : false; modem/RIL/SIM/LTE/IMS stack is live

################################################################################

# 9.28 — CAPTURE ARTIFACT REGISTER

################################################################################

Artifact:
MagiskSU usage banner appeared before several command outputs.

Cause:
Multi-line `adb shell su -c ' ... '` command form caused `su: option requires an argument -- c` in the host transcript.

Effect:
The command body still executed after the banner in multiple captures. The
banner itself is not hardware evidence and must not be copied as a device fact.

Correct command form:
adb shell 'su -c "echo test; id; getenforce"'

Verified root context:
uid=0(root) gid=0(root) groups=0(root) context=u:r:magisk:s0
getenforce: Permissive

Classification:
CAPTURE_ARTIFACT only.

################################################################################

# 9.29 — SECTION 9 FINAL VERDICT

################################################################################

Verdict:
The C17S baseband/radio/RF/connectivity plane is a full MTK phone-class stack,
not a minimal watch connectivity stack.

Confirmed core truths:
- The modem firmware model is single md1img.
- md1dsp, md1arm7, and md3img are absent from the live GPT.
- /sys/kernel/ccci reports one modem.
- LK modem loading is enabled.
- MD1 postfix is 1_ulwtg_n.
- CCCI exposes a large modem/IMS/raw diagnostic/control node surface.
- Single-SIM telephony is live.
- LTE/GSM/IMS/WFC/IWLAN capability is present in the runtime state.
- ccmni0 is the active cellular packet-data interface in capture.
- WMT/connsys is the parent plane for Wi-Fi, Bluetooth, and GPS.
- Wi-Fi gen4m is live and connected through wlan0.
- Bluetooth is live enabled through MTK HCI HAL and framework manager.
- GNSS is live, with GPS/GLONASS/BEIDOU/GALILEO used-in-fix evidence.
- USB MTP/ADB and USB HAL are live.
- NFC, UWB, Ethernet, eSIM, 5G/NR, and FM RF are not proven by the captured
  feature/runtime evidence.

Closure statement:
Section 9 is complete as a hardware/connectivity database section. Remaining
items are explicitly named physical or attribution open leads and do not block
understanding of the C17S baseband, radio, RF, Wi-Fi, Bluetooth, GNSS, USB, or
networking architecture.

################################################################################
# END SECTION 9 — BASEBAND, RADIO, RF & CONNECTIVITY
################################################################################
