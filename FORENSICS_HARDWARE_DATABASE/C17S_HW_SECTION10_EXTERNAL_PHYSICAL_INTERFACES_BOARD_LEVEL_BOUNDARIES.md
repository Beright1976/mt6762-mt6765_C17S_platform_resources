################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 10 — EXTERNAL PHYSICAL INTERFACES & BOARD-LEVEL BOUNDARIES
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 10 — EXTERNAL PHYSICAL / BOARD BOUNDARIES
# Closure     : CLOSED WITH OWNER-CONFIRMED NEGATIVE PHYSICAL LEADS
#
# PURPOSE:
# This section defines the external physical interface, board-edge, gadget,
# removable-media declaration, UART/serial, camera-port, GPIO/pinctrl, and
# Wiite board-control boundary topology of the C17S platform.
#
# This is a hardware digital-twin section. It is not a ROM build recipe,
# not an APK behavior report, and not a threat conclusion. It records which
# physical-edge and board-control surfaces are actually exposed, which are
# software declarations only, which are BSP leftovers, and which require
# privacy redaction before public release.
#
# If a future recovery, clean AOSP ROM, GSI validation, device tree, kernel
# rebuild, SELinux policy, USB bring-up, camera bring-up, UART replacement,
# external-interface audit, or ODM control-plane removal workflow needs to
# preserve, emulate, restrict, replace, classify, or deliberately remove a
# physical-edge behavior, that information belongs here.
#
# SCOPE:
# Included:
# - USB controller, USB PHY, UDC, configfs gadget, Android USB, FunctionFS, MTP, ADB, accessory, ttyGS, and USB power/dual-role state
# - USB VID/PID and public-redaction rules for USB serial identity
# - FSTAB-declared sdcard1 and usbotg entries, and runtime storage-volume proof
# - MMC host boundary: boot eMMC host versus declared external MMC host
# - UART / serial boundary: ttyS0, ttyS1, ttyC modem channels, ttyGS USB gadget serial, console
# - Physical camera-board boundary: kd_camera_hw, SENINF1-4, camera calibration, autofocus, flashlight, MT6370 FLED/BLED paths
# - Wiite board-control plane: wiite_con, wiite_corp, wiite_cp@50, misc control nodes, I2C4 binding, pinctrl, GPIO, UART operation, regulator crossover
# - Board-control / security-relevant edge nodes: gpiochip0, sec, rpmb0, usb_accessory, wiite_con_ctrl, wiite_corp_ctrl
# - Negative physical interface register: no removable storage slot, no HDMI, no NFC, no IR, no UWB, no Ethernet in captured physical/runtime evidence
# - BSP-leftover classification for stale declarations that are not physically populated on this device
# - Public-redaction policy for serial numbers, MAC addresses, local RTC artifacts, and private peripheral identifiers
#
# Excluded:
# - Full baseband, Wi-Fi, Bluetooth, GNSS, modem, IMS, WMT, and RF stack. Section 9 owns those stacks. Section 10 records only their physical-edge overlaps such as USB raw bulk, ttyGS, USB, and board-control boundaries.
# - Full nRF52832/PAR2822 protocol decoding. Section 7 owns wearable-coprocessor and sensor-plane semantics. Section 10 records board-boundary control exposure only.
# - Full I2C/SPI/UART controller inventory. Section 4 owns bus-controller topology. Section 10 consumes only controllers that terminate at external or board-boundary surfaces.
# - Full clock/reset/IRQ/wake topology. Section 5 owns clock and wake proof. Section 10 cross-references UART, USB, Wiite, GPIO, and camera boundary values only.
# - Full power/charger/thermal rail proof. Section 6 owns PMIC and power. Section 10 records USB power, MT6370 flashlight boundary, and Wiite regulator crossover only as boundary facts.
# - Full camera optical sensor identity. Section 8 and hardware component maps own optical/camera behavior. Section 10 owns the board ports and camera fabric endpoints.
# - APK permissions, UID1000 behavior, OTA/FOTA, and policy-level security conclusions. Those belong to software security documents.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S evidence supplied from adb shell su -c captures
# TIER 2 : /sys, /dev, /proc, /config, /vendor/etc/fstab*, service list, lshal, getprop
# TIER 3 : /sys/firmware/devicetree/base and live driver binding paths
# TIER 4 : Closed Sections 4, 5, 7, 8, and 9 as cross-section boundary authorities
# TIER 5 : Owner physical inspection for populated-versus-BSP-leftover external interfaces
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# - Controller presence, devicetree declaration, sysfs node, character device, driver binding, Android feature declaration, runtime service, mounted volume, and physical port population are separate evidence classes.
# - A fstab vold entry is not proof of a physical removable slot.
# - An Android feature declaration is not proof of a populated external device.
# - A devicetree leftover is retained as evidence, but not promoted to physical hardware without corroborating physical/runtime proof.
# - Owner physical inspection can close a negative physical lead when software declarations are already consistent with BSP residue.
# - A board-control misc node is recorded as a board boundary even if its exact downstream electrical net is still open.
# - USB serial, Bluetooth/MAC-like identifiers, local RTC artifacts, and unique peripheral identifiers must be redacted from public versions.
################################################################################

################################################################################
# 10.0 — SECTION CLASSIFICATION LEGEND
################################################################################

CONFIRMED_PHYSICAL_BOUNDARY     : Hardware/runtime boundary present as live node, driver, bus, or physical port class.
CONFIRMED_RUNTIME               : Runtime state active at capture.
CONFIRMED_PLATFORM_DEVICE       : /sys/devices/platform or /sys/bus/platform binding exists.
CONFIRMED_I2C_BINDING           : I2C child exists and reports a bound driver or compatible uevent.
CONFIRMED_MISC_DEVICE           : /dev or /sys/class/misc control device exists.
CONFIRMED_ANDROID_FEATURE       : pm list features exposes the Android hardware feature.
DECLARED_ONLY                   : Config/fstab/DT/property exists but no runtime physical proof captured.
BSP_LEFTOVER                    : Reference BSP residue present but not physically populated on this C17S unit.
OWNER_CONFIRMED_ABSENT          : Owner physical inspection confirms no external physical hardware/port.
NO_RUNTIME_VOLUME               : No mounted removable/media volume in capture.
SECURITY_RELEVANT_BOUNDARY      : Surface can change hardware state, board control, serial, reset, upgrade, or external interface behavior.
PUBLIC_REDACT                   : Value must be redacted in public copies.
OPEN_PHYSICAL_MAPPING           : Electrical net/function requires teardown/schematic/logic capture to fully name.
################################################################################
# 10.1 — CANONICAL SECTION 10 SUMMARY
################################################################################

USB controller                  : 11200000.usb0 / mt_usb / musb-hdrc
USB UDC                         : /sys/class/udc/musb-hdrc
USB controller property         : sys.usb.controller=musb-hdrc / vendor.usb.controller=musb-hdrc
USB runtime config              : mtp,adb
USB configfs                    : enabled, /config/usb_gadget/g1 present
USB VID/PID                     : 0x0E8D / 0x201D
USB product string              : LOKMAT APPLLP 5 MAX
USB manufacturer string         : SPRD
USB serial                      : PUBLIC_REDACT
USB role at capture             : device / UFP / sink
USB supported role              : ufp + dfp
USB speed                       : high-speed
USB power                       : online, 500000 uA, 5000000 uV
USB HAL                         : android.hardware.usb@1.0 and @1.1 declared; vendor.usb-hal-1-1 running
MTP node                        : /dev/mtp_usb major 10 minor 10
USB accessory node              : /dev/usb_accessory major 10 minor 11
USB gadget serial               : /dev/ttyGS0 through /dev/ttyGS3 major 232
ADB FunctionFS                  : /dev/usb-ffs/adb
External storage fstab          : externdevice* -> sdcard1:auto; mt_usb* -> usbotg:auto
Physical removable storage      : OWNER_CONFIRMED_ABSENT
MMC hosts                       : mmc0 bootdevice; mmc1 externdevice declaration/host boundary
Runtime external volume         : none; only private and emulated mounted
UART0                           : 0x11002000 / ttyS0 / IRQ 218 / idle in capture
UART1                           : 0x11003000 / ttyS1 / IRQ 219 / active RX/TX counters
Modem serial boundary           : ttyC0, ttyC1, ttyC2, ttyC3, ttyC5, ttyC6
Console                         : /dev/console major 5 minor 1
Camera fabric                   : kd_camera_hw1 plus SENINF1-4
Camera dev nodes                : kd_camera_hw, CAM_CAL_DRV, MAINAF, camera-dpe, camera-fdvt, camera-isp, flashlight
Camera HAL/services             : cameraserver, camerahalserver, media.camera, ICameraProvider/internal/0
Flashlight boundary             : MT6370 FLED/BLED rt-flash-led paths plus flashlight_core/flashlights_mt6370
Wiite platform devices          : wiite_con and wiite_corp
Wiite misc devices              : /dev/wiite_con_ctrl major 10 minor 50; /dev/wiite_corp_ctrl major 10 minor 48
Wiite I2C device                : I2C4 4-0050, name=cp, DRIVER=wiite_corp, OF_NAME=wiite_cp
Wiite DT nodes                  : wiite_con, wiite_corp, i2c4@11011000/wiite_cp@50
Wiite control plane span        : platform + misc + I2C + pinctrl/GPIO + UART control + regulator debug
HDMI                            : OWNER_CONFIRMED_ABSENT / BSP_LEFTOVER
NFC                             : OWNER_CONFIRMED_ABSENT / BSP_LEFTOVER
IR                              : OWNER_CONFIRMED_ABSENT / BSP_LEFTOVER
UWB                             : not feature-declared; no live device node
Ethernet                        : not feature-declared; no live interface/device node
################################################################################
# 10.2 — USB CONTROLLER / PLATFORM TOPOLOGY REGISTER
################################################################################

Canonical USB controller evidence:
- /sys/bus/platform/devices/11200000.usb0
- /sys/bus/platform/devices/musb-hdrc
- /sys/devices/platform/mt_usb
- /sys/devices/platform/mt_usb/musb-hdrc
- /sys/devices/platform/mt_usb/musb-hdrc/usb1
- /sys/class/udc/musb-hdrc
- /sys/class/android_usb
- /sys/devices/virtual/android_usb
- /sys/class/dual_role_usb/dual-role-usb20
- /sys/devices/platform/otg_iddig
- /sys/class/power_supply/usb
- /sys/devices/platform/mt_charger/power_supply/usb
- /sys/devices/platform/mt-pmic/ldo_vusb33_status
- /sys/devices/platform/mt-pmic/ldo_vusb33_voltage
- /sys/devices/platform/mt-pmic/regulator/regulator.32/mt-pmic-vusb33
- /sys/devices/platform/usb_phy_generic.0.auto
- /sys/class/usb_boost
- /sys/class/usb_rawbulk
- /sys/devices/virtual/usb_rawbulk
- /sys/devices/virtual/ccci_node/ccci_raw_usb

USB property register:
init.svc.adbd                       : running
init.svc.usbd                       : stopped
init.svc.vendor.usb-hal-1-1         : running
persist.sys.usb.config              : adb
ro.adb.secure                       : 1
ro.audio.usb.period_us              : 16000
ro.sys.usb.bicr                     : no
ro.sys.usb.charging.only            : yes
ro.sys.usb.mtp.whql.enable          : 0
ro.sys.usb.storage.type             : mtp
sys.usb.config                      : mtp,adb
sys.usb.configfs                    : 1
sys.usb.controller                  : musb-hdrc
sys.usb.ffs.aio_compat              : 1
sys.usb.ffs.ready                   : 1
sys.usb.state                       : mtp,adb
vendor.usb.acm_cnt                  : 0
vendor.usb.acm_enable               : 0
vendor.usb.acm_port0                : 
vendor.usb.acm_port1                : 
vendor.usb.controller               : musb-hdrc
vendor.usb.ffs.ready                : 1
vendor.usb.pid                      : 0x201D
vendor.usb.vid                      : 0x0E8D

USB classification:
- CONFIRMED_PHYSICAL_BOUNDARY
- CONFIRMED_PLATFORM_DEVICE
- CONFIRMED_RUNTIME
- CONFIRMED_ANDROID_FEATURE
- SECURITY_RELEVANT_BOUNDARY
- PUBLIC_REDACT for serial identity
################################################################################
# 10.3 — USB GADGET / CONFIGFS IDENTITY REGISTER
################################################################################

/sys/class/android_usb/android0/state                         : CONFIGURED
/sys/class/android_usb/android0/iSerial                       : empty in legacy android_usb node
/config/usb_gadget/g1/idVendor                                : 0x0e8d
/config/usb_gadget/g1/idProduct                               : 0x201d
/config/usb_gadget/g1/UDC                                     : musb-hdrc
/config/usb_gadget/g1/strings/0x409/manufacturer              : SPRD
/config/usb_gadget/g1/strings/0x409/product                   : LOKMAT APPLLP 5 MAX
/config/usb_gadget/g1/strings/0x409/serialnumber              : [PUBLIC_REDACT_USB_SERIAL]

Important interpretation:
- The VID/PID pair is MediaTek-style 0x0E8D:0x201D and is confirmed both by properties and configfs.
- The product string reflects the marketed device name while the manufacturer string reports SPRD, which is a retained identity contradiction and should not be silently normalized.
- USB serial is a unique identifier and is redacted in this canonical public section body.
- Current runtime state is configured MTP+ADB gadget mode, not mass-storage mode.
- `ro.sys.usb.bicr=no` and stock software evidence records the iAmCdRom/BICR path as inactive/no image on this unit.
################################################################################
# 10.4 — USB DEVICE NODES / HAL / SERVICE REGISTER
################################################################################

/dev/mtp_usb            : crw-rw---- root:mtp u:object_r:mtp_device:s0 major 10 minor 10
/dev/usb_accessory      : crw-rw---- root:usb u:object_r:usbaccessory_device:s0 major 10 minor 11
/dev/ttyGS0             : crw-rw---- system:radio u:object_r:ttyGS_device:s0 major 232 minor 0
/dev/ttyGS1             : crw-rw---- system:radio u:object_r:ttyGS_device:s0 major 232 minor 1
/dev/ttyGS2             : crw-rw---- system:radio u:object_r:ttyGS_device:s0 major 232 minor 2
/dev/ttyGS3             : crw-rw---- system:radio u:object_r:ttyGS_device:s0 major 232 minor 3
/dev/bus/usb/001        : present under u:object_r:usb_device:s0
/dev/usb-ffs/adb        : FunctionFS ADB endpoint mounted under u:object_r:functionfs:s0
/dev/socket/adbd        : u:object_r:adbd_socket:s0

USB HAL / binder service register:
- android.hardware.usb@1.0::IUsb/default
- android.hardware.usb@1.1::IUsb/default
- vendor.usb-hal-1-1 running
- service usb: android.hardware.usb.IUsbManager
- service adb: android.debug.IAdbManager

USB HAL binary / adjacent audio USB artifacts:
- /vendor/bin/hw/android.hardware.usb@1.1-service-mediatek
- /vendor/lib/hw/audio.usb.default.so
- /vendor/lib/hw/audio.usb.mt6765.so
- /vendor/lib64/hw/audio.usb.default.so
- /vendor/lib64/hw/audio.usb.mt6765.so
################################################################################
# 10.5 — USB DUAL-ROLE / OTG / POWER STATE REGISTER
################################################################################

/sys/class/dual_role_usb/dual-role-usb20/data_role                  : device
/sys/class/dual_role_usb/dual-role-usb20/mode                       : ufp
/sys/class/dual_role_usb/dual-role-usb20/power_role                 : sink
/sys/class/dual_role_usb/dual-role-usb20/supported_modes            : ufp dfp
/sys/class/udc/musb-hdrc/current_speed                              : high-speed
/sys/class/udc/musb-hdrc/maximum_speed                              : high-speed
/sys/class/udc/musb-hdrc/is_otg                                     : 1
/sys/class/udc/musb-hdrc/is_selfpowered                             : 0
/sys/class/udc/musb-hdrc/state                                      : configured
/sys/class/udc/musb-hdrc/uevent                                     : DRIVER=configfs-gadget
/sys/devices/platform/mt_usb/musb-hdrc/cmode                        : 1
/sys/devices/platform/mt_usb/musb-hdrc/modalias                     : platform:musb-hdrc
/sys/devices/platform/mt_usb/musb-hdrc/uevent                       : DRIVER=musb-hdrc
/sys/devices/platform/otg_iddig/modalias                            : of:Notg_iddigT<NULL>Cmediatek,usb_iddig_bi_eint
/sys/class/power_supply/usb/online                                  : 1
/sys/class/power_supply/usb/type                                    : USB
/sys/class/power_supply/usb/current_max                             : 500000
/sys/class/power_supply/usb/voltage_max                             : 5000000

Classification:
- USB dual-role support is confirmed because supported_modes exposes both UFP and DFP.
- The live capture is device/sink/UFP mode, not host/source/DFP mode.
- OTG electrical/logical support is exposed by is_otg=1 and otg_iddig platform node.
- No attached OTG storage device was mounted in this capture.
- The device is USB 2.0 high-speed in the captured state and power limit is standard 500 mA at 5 V.
################################################################################
# 10.6 — FSTAB EXTERNAL STORAGE / OTG DECLARATION REGISTER
################################################################################

FSTAB files with repeated external declarations:
- /vendor/etc/fstab.mt6762
- /vendor/etc/fstab.mt6765
- /system/vendor/etc/fstab.mt6762
- /system/vendor/etc/fstab.mt6765

Repeated external lines:
/devices/platform/externdevice* auto auto defaults voldmanaged=sdcard1:auto,encryptable=userdata
/devices/platform/mt_usb*       auto vfat defaults voldmanaged=usbotg:auto

Classification of fstab entries:
- `sdcard1:auto` is a BSP/storage-template declaration on this device, not proof of a physical removable card slot.
- `usbotg:auto` is a USB OTG media declaration. OTG capability exists, but no OTG volume was attached in the captured runtime state.
- Owner physical inspection confirms there is no removable storage slot on the physical unit.
- Therefore removable SD is classified as FSTAB_DECLARED_BSP_LEFTOVER / OWNER_CONFIRMED_ABSENT_PHYSICAL_SLOT.
- The fstab entries are retained as evidence because they define the vendor storage policy and explain vold/media_rw paths.
################################################################################
# 10.7 — MMC / SD HOST / RUNTIME STORAGE REGISTER
################################################################################

MMC host inventory:
- /sys/class/mmc_host/mmc0 -> /sys/devices/platform/bootdevice/mmc_host/mmc0
- /sys/class/mmc_host/mmc1 -> /sys/devices/platform/externdevice/mmc_host/mmc1

Block node boundary:
- /dev/block/mmcblk0: boot eMMC user area, u:object_r:bootdevice_block_device:s0
- /dev/block/mmcblk0boot0: eMMC boot0/preloader area
- /dev/block/mmcblk0boot1: eMMC boot1/preloader area
- /dev/block/mmcblk0rpmb: eMMC RPMB
- /dev/block/mmcblk0p1-p38: fixed eMMC partitions

Runtime storage mounts captured:
- tmpfs on /storage
- adb on /dev/usb-ffs/adb type functionfs
- /data/media on /mnt/runtime/default/emulated
- /data/media on /storage/emulated
- /data/media on /mnt/runtime/read/emulated
- /data/media on /mnt/runtime/write/emulated
- /data/media on /mnt/runtime/full/emulated

Storage-manager result:
private mounted null
emulated mounted null
No `sm list-disks` output was captured.

Media directories:
- /mnt/media_rw/usbotg exists as a directory but no OTG block volume is mounted there
- /storage/sdcard0 -> /sdcard symlink exists
- /storage/emulated is mounted from /data/media
- /dev/block/vold exists but contains no runtime removable block node in capture

Final removable-storage truth:
The C17S has a declared external MMC host and fstab sdcard1 line, but owner physical inspection confirms no removable storage slot. Treat as BSP leftover / unpopulated board boundary, not as real removable storage hardware.
################################################################################
# 10.8 — UART / SERIAL / CONSOLE BOUNDARY REGISTER
################################################################################

TTY device nodes:
/dev/console      : crw------- root:root u:object_r:console_device:s0 major 5 minor 1
/dev/ttyS0        : crw------- root:root u:object_r:ttyS_device:s0 major 4 minor 64
/dev/ttyS1        : crw-rw-rw- system:system u:object_r:ttyS_device:s0 major 4 minor 65
/dev/ttyC0        : crw-rw---- radio:radio u:object_r:gsm0710muxd_device:s0 major 239 minor 3
/dev/ttyC1        : crw-rw---- radio:radio u:object_r:mdlog_device:s0 major 239 minor 7
/dev/ttyC2        : crw-rw---- radio:radio u:object_r:agps_device:s0 major 239 minor 5
/dev/ttyC3        : crw-rw---- radio:radio u:object_r:icusb_device:s0 major 239 minor 6
/dev/ttyC5        : crw-rw---- radio:radio u:object_r:nwkopt_device:s0 major 239 minor 28
/dev/ttyC6        : crw-rw---- radio:radio u:object_r:nlop_device:s0 major 239 minor 29
/dev/ttyGS0-3     : USB gadget serial ports, major 232 minors 0-3

/proc/tty/drivers evidence:
g_serial             /dev/ttyGS    232 0-7 serial
serial               /dev/ttyS       4 64-65 serial

/proc/tty/driver/serial evidence:
0: uart:ST16650V2 mmio:0x11002000 irq:218 tx:0  rx:0    RTS|DTR|DSR|CD|RI
1: uart:ST16650V2 mmio:0x11003000 irq:219 tx:56 rx:1606 RTS|DTR|DSR|CD|RI

Serial sysfs/platform paths:
- /sys/bus/platform/devices/11002000.serial
- /sys/bus/platform/devices/11003000.serial
- /sys/devices/platform/11002000.serial/tty/ttyS0
- /sys/devices/platform/11003000.serial/tty/ttyS1
- /sys/devices/virtual/ccci_node/ttyC0
- /sys/devices/virtual/ccci_node/ttyC1
- /sys/devices/virtual/ccci_node/ttyC2
- /sys/devices/virtual/ccci_node/ttyC3
- /sys/devices/virtual/ccci_node/ttyC5
- /sys/devices/virtual/ccci_node/ttyC6
- /sys/devices/virtual/tty/ttyGS0
- /sys/devices/virtual/tty/ttyGS1
- /sys/devices/virtual/tty/ttyGS2
- /sys/devices/virtual/tty/ttyGS3
- /sys/devices/virtual/misc/wiite_corp_ctrl/uart_op

UART classification:
- UART0 is instantiated and exposed as ttyS0, with zero tx/rx in this capture; treat as present but idle.
- UART1 is instantiated and exposed as ttyS1, world-writable in stock, and active with nonzero RX/TX counters; treat as real active board boundary, not BSP residue.
- ttyC* nodes are CCCI/modem virtual serial channels and belong primarily to Section 9, but are recorded here as board-external serial surfaces.
- ttyGS* nodes are USB gadget serial ports and belong to the USB boundary.
- `wiite_corp_ctrl/uart_op` ties the Wiite board-control plane to UART operation.
################################################################################
# 10.9 — CAMERA PHYSICAL PORT / BOARD FABRIC BOUNDARY
################################################################################

Camera device nodes:
/dev/kd_camera_hw   : u:object_r:kd_camera_hw_device:s0 major 242 minor 0
/dev/CAM_CAL_DRV    : u:object_r:CAM_CAL_DRV_device:s0 major 241 minor 0
/dev/MAINAF         : u:object_r:MAINAF_device:s0 major 231 minor 0
/dev/camera-dpe     : u:object_r:camera_dpe_device:s0 major 236 minor 0
/dev/camera-fdvt    : u:object_r:camera_fdvt_device:s0 major 235 minor 0
/dev/camera-isp     : u:object_r:camera_isp_device:s0 major 237 minor 0
/dev/flashlight     : u:object_r:flashlight_device:s0 major 233 minor 0

Clean camera platform targets:
- /sys/bus/platform/devices/1a040000.kd_camera_hw1
- /sys/bus/platform/devices/1a040000.seninf1
- /sys/bus/platform/devices/1a041000.seninf2
- /sys/bus/platform/devices/1a042000.seninf3
- /sys/bus/platform/devices/1a043000.seninf4
- /sys/bus/platform/devices/CAM_CAL_DRV.0
- /sys/bus/platform/devices/flashlight_core
- /sys/bus/platform/devices/flashlights_mt6370
- /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_bled/rt-flash-led.2/flashlight
- /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_fled.0/rt-flash-led.0/flashlight
- /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_fled.1/rt-flash-led.1/flashlight
- /sys/devices/platform/reg-dummy/regulator/regulator.0/MAINAF-vcamaf

Camera service / HAL evidence:
- init.svc.camerahalserver=running
- init.svc.cameraserver=running
- service media.camera.proxy
- service media.camera
- android.frameworks.cameraservice.service@2.0::ICameraService/default
- android.hardware.camera.provider@2.4::ICameraProvider/internal/0
- vendor.mediatek.hardware.camera.bgservice@1.0::IBGService/internal/0 declared
- vendor.mediatek.hardware.camera.bgservice@1.1::IBGService/internal/0 declared

Camera property evidence:
- camera.disable_zsl_mode=1
- persist.sys.force.no.flashlight=false
- persist.sys.rotation.camrera.back=0
- persist.sys.rotation.camrera.font=0
- ro.vendor.mtk_camera_app_version=3
- vendor.camera.mdp.cz.enable=1
- vendor.camera.mdp.dre.enable=0

Camera boundary interpretation:
- Four SENINF platform endpoints are exposed: seninf1, seninf2, seninf3, seninf4.
- Section 10 records port/fabric exposure only. It does not decide optical sensor truth beyond camera board-boundary endpoints.
- The earlier broad search returned /proc/*/net/wlan/setCAM noise; that noise is intentionally excluded from this canonical register.
- MT6370 flashlight paths tie camera-visible flash/BLED/FLED to the I2C5 PMIC/sub-PMIC boundary.
################################################################################
# 10.10 — WIITE BOARD-CONTROL PLANE MASTER REGISTER
################################################################################

Primary conclusion:
Wiite control is not isolated to one APK or one sysfs file. It is a board-control plane spanning platform devices, misc character devices, I2C4, devicetree nodes, pinctrl/GPIO tuples, UART operation, and regulator/debug surfaces.

Live Wiite control roots:
- /dev/wiite_con_ctrl
- /dev/wiite_corp_ctrl
- /sys/class/misc/wiite_con_ctrl
- /sys/class/misc/wiite_corp_ctrl
- /sys/devices/platform/wiite_con
- /sys/devices/platform/wiite_corp
- /sys/bus/platform/drivers/wiite_con
- /sys/bus/platform/drivers/wiite_corp
- /sys/bus/i2c/drivers/wiite_corp
- /sys/bus/i2c/devices/4-0050
- /sys/firmware/devicetree/base/wiite_con
- /sys/firmware/devicetree/base/wiite_corp
- /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50
- /sys/devices/virtual/misc/wiite_corp_ctrl/uart_op
- /sys/kernel/debug/regulator/vmch/wiite_corp-vhrs

Misc character device identity:
/dev/wiite_con_ctrl   : crw------- root:root u:object_r:device:s0 major 10 minor 50
/dev/wiite_corp_ctrl  : crw------- root:root u:object_r:device:s0 major 10 minor 48
/sys/class/misc/wiite_con_ctrl/dev  = 10:50
/sys/class/misc/wiite_corp_ctrl/dev = 10:48

wiite_con_ctrl attribute register:
- dev=10:50
- irq_test=empty
- pin_set=empty
- uevent=MAJOR=10
- version=driver vreion:(1.0 20200104)

wiite_corp_ctrl attribute/value register — redacted where unique/private:
acc_dir               : Sensor Direction:0x0
altitude_value        : Altitude value: 0
baro_enable           : DISABLED
baro_value            : Baro value: 0
bprs_simu             : empty
btMAC                 : [PUBLIC_REDACT_NRF_BT_MAC]
chip_id               : 0x70
command               : 0
config                : start ...
delay                 : pix_delay:50
dev                   : 10:48
event_simu            : empty
hrs_simu              : empty
i2c_log               : i2c_log_en:0
i2c_test              : empty
lcm_type              : Unsupport
local_rtc             : [PUBLIC_REDACT_ODM_RTC_ARTIFACT]
longterm              : NO SUPPORT
nrf_rtc               : [PUBLIC_REDACT_NRF_RTC_TIMESTAMP]
nrf_sys_mode          : 0
rst_op                : unknown
spo2_simu             : empty
step_simu             : empty
tempearture_value     : Temperature value: 0
tilt_simu             : empty
uart_op               : uart enable:0
uevent                : MAJOR=10
upgrade_op            : unknown
user_command          : empty
vdd18_con             : empty
version               : 48059
vibr_test             : NO SUPPORT

Classification:
- CONFIRMED_BOARD_CONTROL_PLANE
- CONFIRMED_PLATFORM_DEVICE
- CONFIRMED_MISC_DEVICE
- CONFIRMED_I2C_BINDING
- CONFIRMED_DT_SYMBOL
- CONFIRMED_GPIO_BOUNDARY
- CONFIRMED_UART_CONTROL_SURFACE
- CONFIRMED_REGULATOR_DEBUG_CROSSOVER
- SECURITY_RELEVANT_BOUNDARY
- OPEN_PHYSICAL_MAPPING for exact external pin/net names
################################################################################
# 10.11 — WIITE PLATFORM / DEVICETREE / I2C BINDING DETAILS
################################################################################

Platform device bindings:
/sys/devices/platform/wiite_con/modalias                : of:Nwiite_conT<NULL>Cwiite,wiite_con
/sys/devices/platform/wiite_con/uevent                  : DRIVER=wiite_con
/sys/devices/platform/wiite_con/of_node                 : /sys/firmware/devicetree/base/wiite_con
/sys/devices/platform/wiite_corp/modalias               : of:Nwiite_corpT<NULL>Cwiite,wiite_corp
/sys/devices/platform/wiite_corp/uevent                 : DRIVER=wiite_corp
/sys/devices/platform/wiite_corp/of_node                : /sys/firmware/devicetree/base/wiite_corp
/sys/bus/platform/drivers/wiite_con/wiite_con           : bound platform instance
/sys/bus/platform/drivers/wiite_corp/wiite_corp         : bound platform instance

Devicetree values:
- wiite_con compatible = wiite,wiite_con
- wiite_con name = wiite_con
- wiite_con phandle/linux,phandle = 0x98
- wiite_con pinctrl-names = pwr_i2c_uart_pin_af, pwr_i2c_uart_pin_gpio
- wiite_con wiite,con1 raw tuple = <0x3c 0x0a 0x00>
- wiite_con wiite,con2 raw tuple = <0x3c 0x04 0x00>
- wiite_corp compatible = wiite,wiite_corp
- wiite_corp name = wiite_corp
- wiite_corp phandle/linux,phandle = 0x99
- wiite_corp status = okay
- wiite_corp vhrs-supply = phandle 0x6e
- wiite_cp@50 compatible = wiite,cp
- wiite_cp@50 name = wiite_cp
- wiite_cp@50 reg = 0x50
- wiite_cp@50 status = okay
- wiite_cp@50 irq-gpio raw tuple = <0x3c 0x08 0x00>
- wiite_cp@50 irq-gpio_1 raw tuple = <0x3c 0x06 0x00>

I2C4 live child inventory:
/sys/bus/i2c/devices/i2c-4        : name=mt-i2c; OF_NAME=i2c4; OF_FULLNAME=/i2c4@11011000; compatible mediatek,i2c
/sys/bus/i2c/devices/4-0011       : name=ccu_sensor_i2c_4_hw; DRIVER=ccu_i2c_4_hwtrg; compatible mediatek,ccu_sensor_i2c_4_hw
/sys/bus/i2c/devices/4-0033       : name=heartrate; compatible mediatek,heartrate; no bound DRIVER line in capture
/sys/bus/i2c/devices/4-0050       : name=cp; DRIVER=wiite_corp; OF_NAME=wiite_cp; OF_FULLNAME=/i2c4@11011000/wiite_cp@50; compatible wiite,cp

Interpretation:
- I2C4 address 0x50 is the confirmed Wiite CP endpoint and is driver-bound to wiite_corp.
- I2C4 address 0x33 is the heartrate node and remains part of the sensor/control I2C4 cluster.
- I2C4 address 0x11 is CCU sensor I2C hardware trigger, tying camera/CCU fabric into the same bus cluster.
- The Wiite CP node is status okay and exposes GPIO interrupt tuples through the pinctrl parent phandle 0x3c.
################################################################################
# 10.12 — GPIO / PINCTRL / BOARD-CONTROL EDGE REGISTER
################################################################################

Board-control device nodes:
- /dev/gpiochip0: crw------- root:root u:object_r:device:s0 major 253 minor 0
- /dev/sec: crw-rw---- root:system u:object_r:sec_device:s0 major 182 minor 0
- /dev/rpmb0: crw------- root:root u:object_r:teei_rpmb_device:s0 major 227 minor 0
- /dev/usb_accessory: crw-rw---- root:usb u:object_r:usbaccessory_device:s0 major 10 minor 11
- /dev/wiite_con_ctrl: major 10 minor 50
- /dev/wiite_corp_ctrl: major 10 minor 48

GPIO / pinctrl / control paths observed:
- /sys/bus/gpio
- /sys/bus/gpio/devices/gpiochip0
- /sys/bus/platform/devices/10005000.gpio
- /sys/bus/platform/devices/gpio
- /sys/devices/platform/10005000.gpio
- /sys/devices/platform/1000b000.pinctrl/gpiochip0
- /sys/devices/platform/1000b000.pinctrl/mt_gpio
- /sys/firmware/devicetree/base/gpio
- /sys/firmware/devicetree/base/gpio@10005000
- /sys/firmware/devicetree/base/gpio@10005000/gpio_init_default
- /sys/firmware/devicetree/base/pinctrl@1000b000/gpio-controller
- /sys/firmware/devicetree/base/pinctrl@1000b000/pwr_i2c_uart_pin_gpio
- /sys/kernel/debug/gpio
- /sys/kernel/debug/pinctrl/1000b000.pinctrl/gpio-ranges
- /sys/kernel/debug/tracing/events/gpio
- /sys/module/mtk_sleep/parameters/slp_dump_gpio

Device-tree GPIO artifacts relevant to Section 10:
- GPIO_SIM1_SCLK / GPIO_SIM1_SIO / GPIO_SIM1_SRST / GPIO_SIM2_HOT_PLUG
- msdc@11240000/cd-gpios
- type_c_port0/mt6370pd,intr_gpio and intr_gpio_num
- mt6370_pmu_dts/mt6370,intr_gpio and intr_gpio_num
- nfc/gpio-irq, nfc/gpio-irq-std, nfc/gpio-rst, nfc/gpio-rst-std — BSP leftover, no physical NFC hardware on this unit
- wiite_con/wiite,con1 and wiite,con2
- wiite_cp@50 irq-gpio and irq-gpio_1

Classification:
- GPIO/pinctrl surfaces are confirmed board-control boundaries.
- GPIO lines associated with Wiite and type-C/USB are live-relevant.
- NFC GPIO declarations are retained as BSP leftovers because owner physical inspection confirms no NFC hardware and Android does not declare NFC feature support.
################################################################################
# 10.13 — NEGATIVE EXTERNAL FEATURE / BSP LEFTOVER REGISTER
################################################################################

Removable SD / physical card slot   : OWNER_CONFIRMED_ABSENT; fstab sdcard1 and mmc1 host are BSP/unpopulated boundary evidence, not real removable slot proof.
USB OTG media                       : USB dual-role/OTG support exists; no OTG media was attached or mounted in captured state.
HDMI                                : OWNER_CONFIRMED_ABSENT; property-context/BSP residue only; no Android hardware feature, no device node, no physical port.
NFC                                 : OWNER_CONFIRMED_ABSENT; devicetree/property-context leftovers only; no Android hardware feature, no confirmed NFC device node, no physical NFC hardware.
IR / Consumer IR                    : OWNER_CONFIRMED_ABSENT; property-context residue only; no Android hardware feature, no device node, no physical IR hardware.
UWB                                 : Not feature-declared; no live device/class node captured.
Ethernet                            : Not feature-declared; no eth interface/device/class node captured.
Extra USB ACM ports                 : vendor.usb.acm_cnt=0, vendor.usb.acm_enable=0, acm_port0/1 empty.

pm feature sweep positive external features:
- feature:android.hardware.bluetooth
- feature:android.hardware.bluetooth_le
- feature:android.hardware.camera
- feature:android.hardware.camera.any
- feature:android.hardware.camera.autofocus
- feature:android.hardware.camera.capability.manual_post_processing
- feature:android.hardware.camera.capability.manual_sensor
- feature:android.hardware.camera.capability.raw
- feature:android.hardware.camera.flash
- feature:android.hardware.camera.front
- feature:android.hardware.camera.level.full
- feature:android.hardware.usb.accessory
- feature:android.hardware.usb.host
- feature:android.hardware.wifi
- feature:android.hardware.wifi.direct
- feature:android.hardware.wifi.passpoint

pm feature sweep absent from captured filtered list:
- android.hardware.nfc
- android.hardware.uwb
- android.hardware.ethernet
- android.hardware.hdmi*
- android.hardware.consumerir

Final negative-boundary statement:
The C17S software tree contains stale external-interface residue from broader MTK/ODM BSPs, but the physical unit has no removable SD slot, no HDMI, no NFC, and no IR. These entries are not removed from the database; they are classified as BSP leftovers / owner-confirmed absent physical interfaces.
################################################################################
# 10.14 — EXTERNAL INTERFACE PRIVACY / REDACTION REGISTER
################################################################################

USB serial number                 : redact in public copies
nRF / Wiite BT MAC                : redact in public copies
Bluetooth peer addresses          : redact if repeated from Section 9
Wi-Fi SSID/BSSID/MAC/IP           : redact if a storage/USB capture includes network context
GNSS coordinates                  : redact if cross-captured from location dumps
Local RTC garbage/high-entropy values: redact unless needed for private debugging
SIM/carrier private IDs           : not Section 10 owned; redact if incidental
Host computer paths/usernames     : not hardware evidence; remove from public artifact where practical

Redaction already applied in this canonical public section body:
- configfs USB serial replaced with [PUBLIC_REDACT_USB_SERIAL]
- wiite_corp_ctrl/btMAC replaced with [PUBLIC_REDACT_NRF_BT_MAC]
- wiite_corp_ctrl/local_rtc replaced with [PUBLIC_REDACT_ODM_RTC_ARTIFACT]
- wiite_corp_ctrl/nrf_rtc replaced with [PUBLIC_REDACT_NRF_RTC_TIMESTAMP]
################################################################################
# 10.15 — CROSS-SECTION OWNERSHIP MAP
################################################################################

Section 4   : Owns full bus/interconnect topology: I2C count, SPI count, UART count, USB UDC, MIPI CSI/DSI, DMA, GCE/CMDQ, CLDMA.
Section 5   : Owns clock/reset/IRQ/wake: UART1 IRQ 219, I2C4 wake, Wiite IRQs, dual-role-usb20 wake, MT6370 wake, PMIC interrupts.
Section 6   : Owns PMIC, charger, regulator, thermal, USB power and MT6370 power rails in full depth.
Section 7   : Owns nRF52832/PAR2822/sensor-plane semantics and Wiite protocol leads.
Section 8   : Owns visual/audio/input endpoints, camera preview/flashlight UX, and audio USB route declarations.
Section 9   : Owns baseband/RF/connectivity, WMT, Bluetooth/Wi-Fi/GNSS, USB network/tethering boundary.
Section 10  : Owns board-edge and external physical boundary classification only.
Section 11  : Will own security substrate/trust surface classification where secure/RPMB/TEE/USB/board-control security implications must be evaluated.
Section 12  : Will own negative hardware/BSP ghost consolidation and final open physical leads.
################################################################################
# 10.16 — COMMAND LEDGER
################################################################################

01. SECTION10 USB PLATFORM TOPOLOGY
02. SECTION10 USB GADGET IDENTITY
03. SECTION10 USB DEVICE NODES HALS SERVICES
04. SECTION10 FSTAB EXTERNAL STORAGE OTG ENTRIES
05. SECTION10 MMC / SD HOST PHYSICAL STORAGE BOUNDARY
06. SECTION10 RUNTIME STORAGE VOLUMES
07. SECTION10 UART SERIAL CONSOLE BOUNDARY
08. SECTION10 CAMERA PHYSICAL PORT BOUNDARY
09. SECTION10 BOARD CONTROL / EXTERNAL CONTROL NODES
10. SECTION10 NEGATIVE EXTERNAL FEATURE SWEEP
11. SECTION10 WIITE CONTROL ATTRIBUTES
12. SECTION10 WIITE DEVICE TREE VALUES
13. SECTION10 WIITE I2C AND DRIVER BINDING
14. SECTION10 USB DUAL ROLE / OTG STATE
15. SECTION10 CLEAN CAMERA PLATFORM TARGETS

Command-form correction carried from Section 9:
Use single-line `adb shell 'su -c "..."'` and escape device-side loop variables as `\$var` to avoid host-side expansion or Magisk usage-banner pollution.
################################################################################
# 10.17 — OPEN LEADS
################################################################################

WIITE_CON exact pin names         : wiite,con1 and wiite,con2 raw GPIO tuples are known, but exact external electrical net names remain open without schematic/teardown/logic capture.
WIITE_CP downstream protocol      : I2C4 cp@50 and UART control surface are confirmed; full command decoding belongs to Section 7 / firmware RE, not Section 10.
USB DFP/host live test            : USB supports DFP but current capture is UFP/device/sink. A controlled OTG-host test with an attached low-risk device could record host mode if desired.
External MMC host population      : mmc1/externdevice exists as a declared host boundary, but owner confirms no removable slot. Electrical pad population remains a board-inspection question only.
Camera port electrical population : SENINF1-4 and camera fabric are exposed. Exact MIPI lane wiring / sensor-port assignment belongs to camera-specific physical verification.
Type-C naming residue             : type_c_port0 devicetree interrupt fields exist, but observed USB class exposes micro/dual-role USB 2.0 style musb-hdrc behavior. Physical connector type/pinout belongs to teardown notes.
################################################################################
# 10.18 — FINAL SECTION 10 VERDICT
################################################################################

USB boundary                      : COMPLETE — musb-hdrc / 11200000.usb0 / configfs g1 / VID 0x0E8D / PID 0x201D / MTP+ADB / accessory / ttyGS / dual-role support recorded.
Storage boundary                  : COMPLETE — fstab sdcard1/usbotg declarations recorded; runtime shows no removable volume; owner confirms no removable slot.
UART boundary                     : COMPLETE — ttyS0 idle, ttyS1 active, ttyC modem channels, ttyGS USB serial, console all recorded.
Camera boundary                   : COMPLETE — camera fabric, SENINF1-4, camera dev nodes, HAL/services, MT6370 flashlight endpoints recorded.
Wiite board-control boundary      : COMPLETE — wiite_con, wiite_corp, wiite_cp@50, misc devs, I2C4 binding, pinctrl, GPIO, UART operation, regulator crossover recorded.
Negative external features        : COMPLETE — no removable storage slot, no HDMI, no NFC, no IR; UWB/Ethernet absent from captured features/nodes.
Public-redaction                  : COMPLETE — unique USB serial, nRF/Wiite MAC, RTC-style private artifacts redacted in this public section body.

Section 10 status:
CLOSED as external physical interface and board-level boundary database section.
Remaining open leads are optional physical-mapping refinements and are not blockers for clean AOSP ROM planning or digital-twin continuity.

################################################################################
# APPENDIX 10.C — PER-INTERFACE CANONICAL CLASSIFICATION MATRIX
################################################################################

| Endpoint | Classification | Evidence / Interpretation |
| :--- | :--- | :--- |
| USB controller 11200000.usb0 | CONFIRMED_PLATFORM_DEVICE | Primary SoC USB controller register-domain path present in sysfs find. |
| musb-hdrc | CONFIRMED_RUNTIME | Runtime USB controller, UDC, platform driver, and vendor property all converge on musb-hdrc. |
| configfs g1 gadget | CONFIRMED_RUNTIME | idVendor/idProduct/UDC/string nodes present under /config/usb_gadget/g1. |
| MTP | CONFIRMED_RUNTIME | sys.usb.state includes mtp; /dev/mtp_usb present; storage type mtp. |
| ADB | CONFIRMED_RUNTIME | adbd running, /dev/usb-ffs/adb mounted, adbd socket present. |
| USB accessory | CONFIRMED_NODE | /dev/usb_accessory and Android usb.accessory feature present. |
| USB host / OTG | DECLARED_AND_CAPABLE | Android feature and dual_role supported_modes show host/DFP support; live state is UFP/device/sink. |
| USB RNDIS | NOT_ACTIVE_IN_CAPTURE | No active rndis config in sys.usb.state; not a physical absence claim. |
| USB ACM | DISABLED_IN_CAPTURE | vendor.usb.acm_cnt=0 and vendor.usb.acm_enable=0. |
| USB mass storage / BICR | BSP/INACTIVE | ro.sys.usb.bicr=no and storage type mtp; no mass_storage image active. |
| External sdcard1 | BSP_LEFTOVER | Fstab declares sdcard1:auto; no runtime volume; owner confirms no removable slot. |
| mmc1 externdevice | DECLARED_HOST_BOUNDARY | mmc1 host path exists under externdevice; physical slot absent. |
| usbotg | DECLARED_AND_CAPABLE | Fstab declares usbotg:auto and USB dual role is present; no attached OTG volume in capture. |
| ttyS0 | CONFIRMED_IDLE_UART | ST16650V2 at 0x11002000 IRQ218, exposed as ttyS0, zero counters in capture. |
| ttyS1 | CONFIRMED_ACTIVE_UART | ST16650V2 at 0x11003000 IRQ219, exposed as ttyS1, nonzero counters and Wiite-control correlation. |
| ttyC0-ttyC6 | CONFIRMED_MODEM_SERIAL_BOUNDARY | CCCI virtual serial channels present; radio ownership is Section 9. |
| ttyGS0-ttyGS3 | CONFIRMED_USB_GADGET_SERIAL | USB gadget serial nodes present with ttyGS major 232. |
| console | CONFIRMED_NODE | /dev/console present and labeled console_device. |
| Camera SENINF1-4 | CONFIRMED_PLATFORM_DEVICE | Four SENINF platform devices present at 1a040000-1a043000 family. |
| kd_camera_hw1 | CONFIRMED_PLATFORM_DEVICE | Camera hardware platform node present. |
| CAM_CAL_DRV.0 | CONFIRMED_PLATFORM_DEVICE | Camera calibration platform node and /dev/CAM_CAL_DRV present. |
| MAINAF-vcamaf | CONFIRMED_REGULATOR_ENDPOINT | Autofocus regulator endpoint present under reg-dummy. |
| MT6370 flashlight | CONFIRMED_POWERED_ENDPOINT | FLED/BLED RT flashlight paths present under I2C5 MT6370 PMU. |
| wiite_con | CONFIRMED_PLATFORM_DEVICE | Platform device, driver binding, devicetree compatible, misc control node present. |
| wiite_corp | CONFIRMED_PLATFORM_DEVICE | Platform device, driver binding, devicetree compatible, misc control node present. |
| wiite_cp@50 | CONFIRMED_I2C_BINDING | I2C4 address 0x50, driver wiite_corp, compatible wiite,cp, status okay. |
| wiite_con_ctrl | SECURITY_RELEVANT_BOUNDARY | Pin/IRQ control misc surface major 10 minor 50. |
| wiite_corp_ctrl | SECURITY_RELEVANT_BOUNDARY | nRF/CP control surface major 10 minor 48, exposes reset/upgrade/uart/config/control fields. |
| NFC | OWNER_CONFIRMED_ABSENT | DT/property leftovers only; no feature, no confirmed node, no physical hardware. |
| HDMI | OWNER_CONFIRMED_ABSENT | BSP property context residue only; no feature/node/port. |
| IR | OWNER_CONFIRMED_ABSENT | BSP property context residue only; no feature/node/port. |
| UWB | ABSENT_IN_CAPTURE | No Android hardware feature and no device/class node captured. |
| Ethernet | ABSENT_IN_CAPTURE | No Android hardware feature and no eth device/interface captured. |

################################################################################
# APPENDIX 10.D — DETAILED USB PRESERVATION / REJECTION REGISTER
################################################################################

| Rule | Item | Reason |
| :--- | :--- | :--- |
| PRESERVE | /sys/devices/platform/mt_usb/musb-hdrc | Required USB controller runtime path. |
| PRESERVE | /sys/class/udc/musb-hdrc | Required UDC identity for configfs gadget. |
| PRESERVE | /config/usb_gadget/g1 | Current Android USB gadget model. |
| PRESERVE | vendor.usb.vid=0x0E8D | Host-side ADB/MTP association value. |
| PRESERVE | vendor.usb.pid=0x201D | Host-side ADB/MTP association value. |
| PRESERVE | /dev/usb-ffs/adb | ADB FunctionFS endpoint. |
| PRESERVE | /dev/mtp_usb | MTP endpoint. |
| PRESERVE | /dev/usb_accessory | Accessory mode boundary and declared Android feature. |
| PRESERVE | /dev/ttyGS0-3 | USB gadget serial boundary; classify, do not assume unused. |
| PRESERVE | android.hardware.usb@1.1-service-mediatek | USB HAL runtime owner. |
| DO_NOT_OVERCLAIM | USB host live media | DFP is supported, but no host media was mounted during the capture. |
| DO_NOT_OVERCLAIM | RNDIS | Not active in sys.usb.state during this capture. |
| DO_NOT_OVERCLAIM | mass_storage/BICR | BICR disabled; storage type MTP. |
| REDACT | /config/usb_gadget/g1/strings/0x409/serialnumber | Unique device serial. |

################################################################################
# APPENDIX 10.E — DETAILED WIITE BOARD-CONTROL PRESERVATION / RISK REGISTER
################################################################################

| Rule | Item | Reason |
| :--- | :--- | :--- |
| PRESERVE_AS_EVIDENCE | /sys/class/misc/wiite_corp_ctrl/chip_id | 0x70 identifies the nRF/CP-side control silicon lineage. |
| PRESERVE_AS_EVIDENCE | /sys/class/misc/wiite_corp_ctrl/version | 48059 firmware/control-plane version observed. |
| PRESERVE_AS_EVIDENCE | /sys/class/misc/wiite_corp_ctrl/nrf_sys_mode | 0 = normal operation at capture. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/uart_op | Board-control plane owns/controls UART operation. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/upgrade_op | Firmware/control upgrade surface; security-relevant boundary. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/rst_op | Reset-operation surface; security-relevant boundary. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/command | Command surface; classify separately from user_command. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/user_command | User command channel; write-only style in earlier section evidence. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/i2c_test | I2C diagnostic/test path. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/i2c_log | I2C logging enable path. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_corp_ctrl/vdd18_con | 1.8V control lead; exact electrical net open. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_con_ctrl/pin_set | Secondary pin-control path. |
| PRESERVE_AS_BOUNDARY | /sys/class/misc/wiite_con_ctrl/irq_test | Secondary IRQ-test path. |
| PRESERVE_AS_DT | /sys/firmware/devicetree/base/wiite_con/pinctrl-names | Shows pwr_i2c_uart_pin_af and pwr_i2c_uart_pin_gpio states. |
| PRESERVE_AS_DT | /sys/firmware/devicetree/base/i2c4@11011000/wiite_cp@50/reg | I2C address 0x50. |
| REDACT | btMAC | Unique nRF/BT address. |
| REDACT | local_rtc / nrf_rtc raw values | Private/live runtime timestamp artifacts. |
| OPEN | wiite,con1 / wiite,con2 net names | Raw GPIO tuples known; exact schematic net names open. |

################################################################################
# APPENDIX 10.F — RAW EVIDENCE VALUES NORMALIZED FOR DATABASE USE
################################################################################

USB normalized values:
controller              : musb-hdrc
platform_primary        : 11200000.usb0
usb_phy                 : usb_phy_generic.0.auto
otg_iddig               : mediatek,usb_iddig_bi_eint
udc                     : musb-hdrc
configfs_gadget         : g1
vid                     : 0x0E8D
pid                     : 0x201D
runtime_functions       : mtp,adb
role                    : data=device / mode=ufp / power=sink
speed                   : high-speed
power                   : 500 mA @ 5 V
usb_hal                 : android.hardware.usb@1.1-service-mediatek

Storage normalized values:
mmc0                    : bootdevice eMMC host
mmc1                    : externdevice declared host
vold sdcard1            : declared; physical removable slot absent
vold usbotg             : declared; no attached volume in capture
runtime volumes         : private mounted null; emulated mounted null
/mnt/media_rw/usbotg    : directory exists; no mounted device

Serial normalized values:
UART0                   : 0x11002000 / IRQ218 / ttyS0 / tx0 rx0
UART1                   : 0x11003000 / IRQ219 / ttyS1 / tx56 rx1606 in Section10 capture
g_serial                : /dev/ttyGS major 232 minors 0-7
ttyGS captured          : ttyGS0-ttyGS3
CCCI tty captured       : ttyC0 ttyC1 ttyC2 ttyC3 ttyC5 ttyC6

Camera normalized values:
camera_hw               : 1a040000.kd_camera_hw1
seninf                  : seninf1 seninf2 seninf3 seninf4
calibration             : CAM_CAL_DRV.0 and /dev/CAM_CAL_DRV
autofocus               : MAINAF and MAINAF-vcamaf
flashlight              : flashlight_core / flashlights_mt6370 / rt-flash-led.0/.1/.2
services                : cameraserver + camerahalserver + media.camera
HAL                     : android.hardware.camera.provider@2.4 internal/0

Wiite normalized values:
wiite_con               : compatible wiite,wiite_con / phandle 0x98
wiite_corp              : compatible wiite,wiite_corp / status okay / phandle 0x99
wiite_cp                : i2c4 address 0x50 / compatible wiite,cp / status okay
wiite_cp driver         : DRIVER=wiite_corp
wiite_con_ctrl          : major 10 minor 50 / pin_set / irq_test
wiite_corp_ctrl         : major 10 minor 48 / control + sensor + UART + upgrade attributes
pinctrl states          : pwr_i2c_uart_pin_af and pwr_i2c_uart_pin_gpio
irq tuples              : <0x3c 0x08 0x00> and <0x3c 0x06 0x00> for wiite_cp
vhrs                    : wiite_corp-vhrs debug regulator crossover

################################################################################
# APPENDIX 10.G — FINAL NEGATIVE-HARDWARE SENTENCES FOR SECTION 12 CARRY-FORWARD
################################################################################

- The fstab `sdcard1:auto` declaration is retained as a BSP leftover because the physical C17S unit has no removable storage slot.
- The `externdevice` MMC host path is retained as an unpopulated/declaration boundary, not promoted to removable-media hardware.
- The `usbotg:auto` declaration is valid as a USB OTG storage policy, but no OTG volume was attached or mounted in the capture.
- HDMI is absent as physical hardware. Any HDMI property-context or BSP residue must remain classified as HDMI_BSP_LEFTOVER.
- NFC is absent as physical hardware. Devicetree `nfc` GPIO nodes and NFC property contexts remain NFC_BSP_LEFTOVER / OWNER_CONFIRMED_ABSENT.
- IR / Consumer IR is absent as physical hardware. Property-context residue remains IR_BSP_LEFTOVER / OWNER_CONFIRMED_ABSENT.
- UWB and Ethernet have no Android feature declaration and no live device/class node in this capture.
- Wiite control is real board-control hardware exposure and must not be grouped with the negative BSP leftovers.

################################################################################
# END SECTION 10 — EXTERNAL PHYSICAL INTERFACES & BOARD-LEVEL BOUNDARIES
################################################################################
################################################################################
# APPENDIX 10.A — CANONICAL NODE TABLES
################################################################################

USB node table:
| Path | Major:Minor | Owner | SELinux label | Role |
| :--- | :--- | :--- | :--- | :--- |
| /dev/mtp_usb | 10:10 | root:mtp | u:object_r:mtp_device:s0 | MTP gadget node |
| /dev/usb_accessory | 10:11 | root:usb | u:object_r:usbaccessory_device:s0 | Android Open Accessory boundary |
| /dev/ttyGS0 | 232:0 | system:radio | u:object_r:ttyGS_device:s0 | USB gadget serial 0 |
| /dev/ttyGS1 | 232:1 | system:radio | u:object_r:ttyGS_device:s0 | USB gadget serial 1 |
| /dev/ttyGS2 | 232:2 | system:radio | u:object_r:ttyGS_device:s0 | USB gadget serial 2 |
| /dev/ttyGS3 | 232:3 | system:radio | u:object_r:ttyGS_device:s0 | USB gadget serial 3 |

Serial node table:
| Path | Major:Minor | Owner | SELinux label | Role |
| :--- | :--- | :--- | :--- | :--- |
| /dev/console | 5:1 | root:root | u:object_r:console_device:s0 | console |
| /dev/ttyS0 | 4:64 | root:root | u:object_r:ttyS_device:s0 | UART0 0x11002000 |
| /dev/ttyS1 | 4:65 | system:system | u:object_r:ttyS_device:s0 | UART1 0x11003000 |
| /dev/ttyC0 | 239:3 | radio:radio | u:object_r:gsm0710muxd_device:s0 | CCCI modem serial |
| /dev/ttyC1 | 239:7 | radio:radio | u:object_r:mdlog_device:s0 | CCCI modem log |
| /dev/ttyC2 | 239:5 | radio:radio | u:object_r:agps_device:s0 | AGPS serial |
| /dev/ttyC3 | 239:6 | radio:radio | u:object_r:icusb_device:s0 | ICUSB boundary |
| /dev/ttyC5 | 239:28 | radio:radio | u:object_r:nwkopt_device:s0 | Network option |
| /dev/ttyC6 | 239:29 | radio:radio | u:object_r:nlop_device:s0 | NLOP boundary |

Camera node table:
| Path | Major:Minor | Owner | SELinux label | Role |
| :--- | :--- | :--- | :--- | :--- |
| /dev/kd_camera_hw | 242:0 | system:camera | u:object_r:kd_camera_hw_device:s0 | camera HW control |
| /dev/CAM_CAL_DRV | 241:0 | system:camera | u:object_r:CAM_CAL_DRV_device:s0 | camera calibration |
| /dev/MAINAF | 231:0 | system:camera | u:object_r:MAINAF_device:s0 | autofocus |
| /dev/camera-dpe | 236:0 | system:camera | u:object_r:camera_dpe_device:s0 | camera DPE |
| /dev/camera-fdvt | 235:0 | system:camera | u:object_r:camera_fdvt_device:s0 | camera FDVT |
| /dev/camera-isp | 237:0 | system:camera | u:object_r:camera_isp_device:s0 | camera ISP |
| /dev/flashlight | 233:0 | system:camera | u:object_r:flashlight_device:s0 | flashlight |

Wiite/control node table:
| Path | Major:Minor | Owner | SELinux label | Role |
| :--- | :--- | :--- | :--- | :--- |
| /dev/wiite_corp_ctrl | 10:48 | root:root | u:object_r:device:s0 | primary Wiite/nRF control |
| /dev/wiite_con_ctrl | 10:50 | root:root | u:object_r:device:s0 | secondary Wiite pin/IRQ control |
| /dev/gpiochip0 | 253:0 | root:root | u:object_r:device:s0 | GPIO chip boundary |
| /dev/sec | 182:0 | root:system | u:object_r:sec_device:s0 | security device boundary |
| /dev/rpmb0 | 227:0 | root:root | u:object_r:teei_rpmb_device:s0 | RPMB boundary |

################################################################################
# APPENDIX 10.B — BUILD-ADJACENT PRESERVATION NOTES
################################################################################

These are not ROM instructions. They are database constraints that prevent accidental removal of physical-boundary evidence:
- Preserve USB VID/PID 0x0E8D/0x201D if ADB/MTP host continuity matters.
- Preserve configfs gadget g1 and musb-hdrc UDC assumptions unless kernel/device-tree changes intentionally replace USB gadget architecture.
- Do not model the external storage fstab entry as a real SD slot. It is declared, but the physical slot is absent.
- Do not delete USB OTG support merely because no OTG volume was mounted in the capture; dual-role/DFP support is declared and UDC reports OTG capability.
- Do not model ttyS1 as dead residue. It is active UART1 hardware at 0x11003000 / IRQ 219 and intersects the Wiite/sensor/control plane.
- Do not flatten Wiite control into one sysfs path. It spans wiite_con, wiite_corp, I2C4 cp@50, pinctrl, GPIO, UART operation, and regulator/debug paths.
- Do not treat NFC/HDMI/IR leftovers as physical hardware. They are retained only as BSP residue/negative boundaries.
- Do not publish USB serial or nRF BT MAC in public artifacts.
