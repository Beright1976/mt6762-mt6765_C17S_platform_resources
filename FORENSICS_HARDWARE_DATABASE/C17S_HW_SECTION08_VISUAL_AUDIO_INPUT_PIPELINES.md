################################################################################
# C17S HARDWARE DIGITAL TWIN
# SECTION 8 — VISUAL, AUDIO & INPUT PIPELINES
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 8 — DATABASE FORM
# Closure     : CLOSED WITH NAMED OPEN PHYSICAL-MAPPING LEADS
#
# PURPOSE:
# This section records the hardware-visible and runtime-visible visual, audio,
# input, haptic, LED, flashlight, framebuffer, display, graphics, accessory-
# detect, ALSA, audio-policy, and user-facing endpoint surfaces of the C17S.
#
# This is a hardware database section.
# It is not a ROM build plan.
# It is not an ODM product-mode design note.
# It is not a framework behavior essay.
#
# SCOPE:
# Included:
# - Active live LCM identity
# - Display boot parameters exposed through /proc/pl_lk
# - Linux display platform nodes
# - Display procfs/debugfs/clock/debug surfaces
# - fbdev /dev/graphics/fb0 runtime geometry
# - SurfaceFlinger / HWC display state only as runtime display evidence
# - Graphics HAL service and file inventory
# - Hynitron physical touch path
# - MTK TPD virtual touch aggregation path
# - MTK keypad path and advertised key capabilities
# - Input keylayout / keychar / IDC inventory state
# - Backlight, BLED, FLED, flashlight, and vibrator class surfaces
# - Vibrator HAL and Android haptic-service state
# - ACCDET / PMIC accessory-detect node
# - ALSA card, PCM devices, /dev/snd, /sys/class/sound
# - Audio HAL services and binary inventory
# - Audio policy declared output/input devices
# - Physical-versus-declared endpoint classifications
# - Stale / superseded / conflicting database-derived entries inside this scope
#
# Excluded:
# - Bus-controller ownership belongs to Section 4.
# - Clock/reset/IRQ/wake ownership belongs to Section 5.
# - PMIC/regulator/thermal ownership belongs to Section 6.
# - nRF/PAR2822/sensor-coprocessor ownership belongs to Section 7.
# - Full camera sensor truth belongs to camera/hardware component sections.
# - Full radio / Wi-Fi / Bluetooth / GPS topology belongs to Section 9.
# - ROM policy and ODM removal plans belong to ROM/build documents.
#
# SOURCE AUTHORITY:
# TIER 1 : Live rooted C17S device evidence
# TIER 2 : /proc/driver/lcm_info and /proc/pl_lk
# TIER 3 : /dev, /proc, /sys runtime device surfaces
# TIER 4 : lshal, getprop, dumpsys SurfaceFlinger, dumpsys media.audio_policy
# TIER 5 : Vendor/system filesystem inventory from this exact device
# TIER 6 : Closed Sections 1–7 as cross-reference owners only
# FINAL  : Beright / Albert acceptance
#
# EVIDENCE RULES:
# Controller presence, platform-node presence, class-device exposure, HAL
# registration, framework publication, route declaration, physical endpoint proof,
# and database inference are separate evidence classes.
#
# A declared route is not physical hardware proof.
# A class node is not physical placement proof.
# A virtual input device is not a physical controller.
# A stale database entry is not discarded if it originated from this device.
# A stale database entry is demoted only when stronger live evidence supersedes it.
# No borrowed values from similar devices.
# No assumption promoted to hardware truth.
# No ROM policy promoted to hardware truth.
################################################################################

################################################################################
# 8.0 — SECTION AUTHORITY / CLOSURE STATE
################################################################################

Section status:
Status                         : CLOSED
Document form                  : DATABASE
Closure class                  : Endpoint inventory complete with named open leads
Open-lead class                : physical mapping / board routing / interrupted DT search
Hardware topology blockers     : none identified inside Section 8 scope

Capture basis:
Device                         : LOKMAT APPLLP 5 MAX
Board                          : WP_C17S_PIX_TFT_D4
Kernel                         : Linux 4.9.190+
Android base                   : Android 10 / MTK Q BSP
Platform BSP identity          : mt6765
Physical AP silicon identity   : MT6762V/WB
Root state                     : adb shell su available

Primary captured surfaces:
/proc/driver/lcm_info
/proc/pl_lk
/dev/graphics/fb0
/sys/class/graphics/fb0
/sys/devices/platform/mtkfb@0
/sys/devices/platform/*disp*
/sys/devices/platform/*dsi*
/sys/kernel/debug/disp
/sys/kernel/debug/mtkfb
/sys/kernel/debug/dispsys
/sys/kernel/debug/clk/mm_dsi0
/sys/kernel/debug/clk/mm_disp_*
dumpsys SurfaceFlinger
lshal
getprop init.svc.*
/vendor/lib/hw
/vendor/lib64/hw
/proc/bus/input/devices
/dev/input/event0
/dev/input/event1
/dev/input/event2
/sys/bus/i2c/devices/0-005d
getevent -lp
getevent -i
/system/usr/keylayout
/system/usr/keychars
/system/usr/idc
/sys/class/leds
/sys/class/flashlight
/sys/devices/platform/accdet
/proc/asound/cards
/proc/asound/devices
/proc/asound/pcm
/proc/asound/card0
/dev/snd
/sys/class/sound
dumpsys media.audio_policy
/vendor/etc/audio_policy_configuration.xml
/vendor/etc/audio_policy.conf
/vendor/etc/audio_param

Closure meaning:
The visual/audio/input endpoint database is complete enough to identify every
known Section 8 endpoint class, every confirmed live surface, every declared
route class, every virtual aggregation layer, and every open physical-mapping
lead presently known from the captured evidence.

Not closed:
DTBO string search was interrupted.
Board-level optical/physical mapping of all FLED/BLED paths is not closed.
Physical population of every audio-policy route is not closed.
Physical mapping of all advertised keypad/touch sideband keycodes is not closed.

################################################################################
# 8.1 — EVIDENCE CLASS LEGEND
################################################################################

CONFIRMED_PHYSICAL             : physical endpoint directly verified by device behavior or owner confirmation
CONFIRMED_LIVE_RUNTIME         : live node / runtime value / running HAL / active service on this device
CONFIRMED_BOOT_RUNTIME         : bootloader/LK/proc boot evidence on this device
CONFIRMED_PLATFORM_NODE        : live platform or devicetree-backed runtime node
CONFIRMED_CLASS_DEVICE         : /sys/class or /dev class endpoint exists live
VIRTUAL_AGGREGATION_LAYER      : Android/kernel virtual input or route aggregation surface
DECLARED_ROUTE_ONLY            : audio/input/display route declared but not physical-proof complete
BSP_GHOST_CANDIDATE            : inherited MTK/ODM route likely not populated on this watch form factor
SUPERSEDED_DATABASE_INFERENCE  : prior database-derived entry superseded by stronger live evidence
OPEN_PHYSICAL_LEAD             : endpoint requires physical/functional/board confirmation
CROSS_REFERENCE_ONLY           : endpoint owned by another hardware section
ABSENT_IN_CAPTURE              : searched surface returned no matching result in current capture
INTERRUPTED_SEARCH             : command interrupted; not usable as negative evidence

################################################################################
# 8.2 — CANONICAL ENDPOINT INDEX
################################################################################

| Endpoint class | Canonical endpoint | Primary path / value | Evidence class | Physical status |
|---|---|---|---|---|
| LCM | lk_st7701S_V3_480X640_dsi_vdo | /proc/driver/lcm_info, /proc/pl_lk | CONFIRMED_BOOT_RUNTIME + CONFIRMED_LIVE_RUNTIME | CONFIRMED display endpoint |
| Resolution | 480 x 640 | /proc/driver/lcm_info, LK, fb0, input axes | CONFIRMED_LIVE_RUNTIME | CONFIRMED |
| DSI | dsi0 | /sys/devices/platform/14014000.dsi0 | CONFIRMED_PLATFORM_NODE | CONFIRMED display path |
| Display PWM | disp_pwm | /sys/devices/platform/1100e000.disp_pwm | CONFIRMED_PLATFORM_NODE | CONFIRMED display path |
| Framebuffer | fb0 / mtkfb | /dev/graphics/fb0, /sys/class/graphics/fb0 | CONFIRMED_CLASS_DEVICE | CONFIRMED runtime surface |
| SurfaceFlinger display | Display 0 / Internal display | dumpsys SurfaceFlinger | CONFIRMED_LIVE_RUNTIME | CONFIRMED runtime surface |
| HWC | composer 2.1 | android.hardware.graphics.composer@2.1 | CONFIRMED_LIVE_RUNTIME | Runtime HAL |
| Gralloc | allocator 2.0 / gralloc.mt6765.so | /vendor/lib*/hw | CONFIRMED_LIVE_RUNTIME | Runtime HAL |
| Vulkan | vulkan.mt6765.so | /vendor/lib*/hw | CONFIRMED_FILE_SURFACE | Runtime graphics blob |
| Backlight | lcd-backlight | /sys/class/leds/lcd-backlight | CONFIRMED_CLASS_DEVICE | User-visible class surface |
| BLED | mt6370_pmu_bled | /sys/class/leds and /sys/class/flashlight | CONFIRMED_CLASS_DEVICE | OPEN_PHYSICAL_LEAD mapping |
| FLED0 | mt-flash-led1 | /sys/class/flashlight/mt-flash-led1 | CONFIRMED_CLASS_DEVICE | OPEN_PHYSICAL_LEAD mapping |
| FLED1 | mt-flash-led2 | /sys/class/flashlight/mt-flash-led2 | CONFIRMED_CLASS_DEVICE | OPEN_PHYSICAL_LEAD mapping |
| Touch controller | hyn_ts | I2C0 0x5d / /dev/input/event1 | CONFIRMED_PHYSICAL/DRIVER PATH | CONFIRMED touch endpoint |
| Touch aggregation | mtk-tpd | /dev/input/event2 | VIRTUAL_AGGREGATION_LAYER | Not physical chip |
| Keypad | mtk-kpd | /dev/input/event0 | CONFIRMED_CLASS_DEVICE | Physical mapping partly open |
| Vibrator | vibrator LED + HAL | /sys/class/leds/vibrator, vibrator HAL | CONFIRMED_LIVE_RUNTIME | CONFIRMED haptics path |
| ACCDET | pmic-accdet | /sys/devices/platform/accdet | CONFIRMED_PLATFORM_NODE | Jack physical status open |
| ALSA card | mtsndcard | /proc/asound/cards, /sys/class/sound/card0 | CONFIRMED_CLASS_DEVICE | Audio runtime path |
| Audio HAL | audio@5.0 | lshal, init.svc.vendor.audio-hal-2-0 | CONFIRMED_LIVE_RUNTIME | Runtime HAL |
| Audio policy | phone-class route table | dumpsys media.audio_policy | CONFIRMED_LIVE_RUNTIME | Routes split below |

################################################################################
# 8.3 — PANEL IDENTITY REGISTER
################################################################################

Active live LCM:
Name                           : lk_st7701S_V3_480X640_dsi_vdo
Resolution                     : 480 x 640
Live proc source               : /proc/driver/lcm_info
Bootloader/proc source         : /proc/pl_lk
Evidence class                 : CONFIRMED_BOOT_RUNTIME + CONFIRMED_LIVE_RUNTIME

Superseded panel inference:
Prior database value           : NT35695B / AUO / nt35695B_fhd_dsi_cmd_auo_rt5081_drv
Origin class                   : device-derived prior database inference
Current class                  : SUPERSEDED_DATABASE_INFERENCE
Reason                         : live /proc/driver/lcm_info and /proc/pl_lk identify ST7701S path
Deletion status                : retained in contradiction register, not erased
Active-hardware status         : not promoted to active hardware truth

Panel identity register:
| Identifier | Source class | Current evidence | Database status |
|---|---|---|---|
| lk_st7701S_V3_480X640_dsi_vdo | /proc/driver/lcm_info + /proc/pl_lk | active live value | ACTIVE_LIVE_LCM_IDENTITY |
| 480 x 640 | proc/LK/fb/input | repeated live geometry | ACTIVE_LIVE_GEOMETRY |
| NT35695B | older database inference | not present in current active runtime outputs | SUPERSEDED_DATABASE_INFERENCE |
| nt35695B_fhd_dsi_cmd_auo_rt5081_drv | older database inference | not present in current active runtime outputs | SUPERSEDED_DATABASE_INFERENCE |

################################################################################
# 8.4 — LK DISPLAY / DSI BOOT REGISTER
################################################################################

/proc/pl_lk display probe:
LCM name                       : lk_st7701S_V3_480X640_dsi_vdo
Resolution                     : 480 x 640
Physical size                  : 0 x 0
lcm_if                         : 0
cmd_if                         : 0
Interface string               : unknown
Type                           : DSI
DSI Mode                       : BURST_VDO_MODE
LANE_NUM                       : 1
data_format                    : (0,2,0,0)
vact                           : 4
vbp                            : 20
vfp                            : 20
vact_line                      : 640
hact                           : 5
hbp                            : 30
hfp                            : 30
hblank                         : 0
hactive                        : 480
pll_select                     : 0
pll_div1                       : 0
pll_div2                       : 0
fbk_div                        : 0
fbk_sel                        : 0
rg_bir                         : 0
rg_bic                         : 0
rg_bp                          : 0
PLL_CLOCK                      : 320
dsi_clock                      : 0
ssc_range                      : 0
ssc_disable                    : 1
compatibility_for_nvk          : 0
cont_clock                     : 0
lcm_ext_te_enable              : 0
noncont_clock                  : 0
noncont_clock_period           : 0
LCM handle after probe         : 0x480b431c
DISP_GetFBRamSize              : 3,686,400 bytes
dpmgr scenario                 : primary_disp
dpmgr handle                   : 0x480b3710
dst module                     : dsi0
mutex                          : mutex 0
mutex value                    : 0x90380
SOF                            : dsi0
EOF                            : dsi0
connect path                   : ovl0 to dsi0
DSI PHY pll                    : 320
DSI PHY cycle time             : 13 ns
DSI PHY unit interval          : 2 ns
DSI PHY lane count             : 1
HS_TRAIL                       : 6
HS_ZERO                        : 11
HS_PRPR                        : 5
LPX                            : 6
TA_GET                         : 30
TA_SURE                        : 9
TA_GO                          : 24
CLK_TRAIL                      : 8
CLK_ZERO                       : 30
CLK_HS_PRPR                    : 4
DSI_PHY_TIMCONFIG              : 0x060b0506,0x0c1e0918,0x081e0100,0x000c0f04
DSI read packet_type           : 0x21
LCM register read              : 0x0A = 156
LCM connected                  : yes
Layer 0 framebuffer addr       : 0x7fae0000
Layer 1 framebuffer addr       : 0x7fc0c000
Framebuffer pitch              : 1920
Framebuffer format             : eBGRA8888
Layer dst rectangle            : 0,0,480,640
MTK_LCM_PHYSICAL_ROTATION      : 0
LCM_HEIGHT                     : 307
LCM_WIDTH                      : 546
videolfb islcmfound            : 1
videolfb lcmname               : lk_st7701S_V3_480X640_dsi_vdo

################################################################################
# 8.5 — LINUX DISPLAY PLATFORM NODE REGISTER
################################################################################

Primary root:
/sys/devices/platform/mtkfb@0
  driver                        : ../../../bus/platform/drivers/mtkfb
  of_node                       : ../../../firmware/devicetree/base/mtkfb@0
  modalias                      : present
  uevent                        : present
  SELinux                       : u:object_r:sysfs:s0
  Class                         : CONFIRMED_PLATFORM_NODE

Display platform nodes:
| Node | Register/name role | Section owner |
|---|---|---|
| /sys/devices/platform/1100e000.disp_pwm | display PWM | Section 8 endpoint / Section 5 clock cross-ref |
| /sys/devices/platform/14001000.disp_mutex0 | display mutex | Section 8 endpoint / Section 4 fabric cross-ref |
| /sys/devices/platform/1400b000.disp_ovl0 | display overlay 0 | Section 8 endpoint |
| /sys/devices/platform/1400c000.disp_ovl0_2l | display overlay 2-layer | Section 8 endpoint |
| /sys/devices/platform/1400d000.disp_rdma0 | display RDMA0 | Section 8 endpoint / Section 4 DMA cross-ref |
| /sys/devices/platform/1400e000.disp_wdma0 | display WDMA0 | Section 8 endpoint / Section 4 DMA cross-ref |
| /sys/devices/platform/1400f000.disp_color0 | display color | Section 8 endpoint |
| /sys/devices/platform/14010000.disp_ccorr0 | display color correction | Section 8 endpoint |
| /sys/devices/platform/14011000.disp_aal0 | adaptive ambient light/display block | Section 8 endpoint |
| /sys/devices/platform/14012000.disp_gamma0 | gamma | Section 8 endpoint |
| /sys/devices/platform/14013000.disp_dither0 | dither | Section 8 endpoint |
| /sys/devices/platform/14014000.dsi0 | DSI0 | Section 8 endpoint / Section 4 DSI cross-ref |
| /sys/devices/platform/14015000.disp_rsz0 | display resizer | Section 8 endpoint |
| /sys/devices/platform/dispsys | display system | Section 8 endpoint |
| /sys/devices/platform/dsi_te | DSI tearing-effect node | Section 8 endpoint |
| /sys/devices/platform/mtk_disp_mgr.0 | MTK display manager | Section 8 endpoint |
| /sys/devices/platform/mtkfb@0 | framebuffer root | Section 8 endpoint |

################################################################################
# 8.6 — DISPLAY PROCFS / DEBUGFS / CLOCK SURFACES
################################################################################

Procfs/debugfs display surfaces:
/proc/driver/lcm_info
/proc/driver/thermal/clabcct_lcmoff
/proc/irq/267/dsi0
/proc/mtkcooler/abcct_lcmoff
/proc/ppm/policy/lcmoff_min_freq
/sys/kernel/debug/disp
/sys/kernel/debug/displowpower
/sys/kernel/debug/dispsys
/sys/kernel/debug/ion/clients/disp_decouple-0
/sys/kernel/debug/ion/clients/display-0
/sys/kernel/debug/mtkfb
/sys/kernel/debug/pm_qos/disp_freq

Display clock debug surfaces:
/sys/kernel/debug/clk/disp_pwm_sel
/sys/kernel/debug/clk/f_fdisp_pwm_ck
/sys/kernel/debug/clk/ifr_disp_pwm
/sys/kernel/debug/clk/mm_dig_dsi_ck
/sys/kernel/debug/clk/mm_disp_aal0
/sys/kernel/debug/clk/mm_disp_ccorr0
/sys/kernel/debug/clk/mm_disp_color0
/sys/kernel/debug/clk/mm_disp_dither0
/sys/kernel/debug/clk/mm_disp_gamma0
/sys/kernel/debug/clk/mm_disp_ovl0
/sys/kernel/debug/clk/mm_disp_ovl0_2l
/sys/kernel/debug/clk/mm_disp_rdma0
/sys/kernel/debug/clk/mm_disp_rsz0
/sys/kernel/debug/clk/mm_disp_wdma0
/sys/kernel/debug/clk/mm_dsi0

Debug file returns captured:
/sys/kernel/debug/disp/dump         : idlemgr disable mtcmos now, all the regs may 0x00000000
/sys/kernel/debug/disp/lowpowermode : unknown mode(n)

################################################################################
# 8.7 — FRAMEBUFFER REGISTER
################################################################################

/dev node:
Path                           : /dev/graphics/fb0
Type                           : character device
Permissions                    : crw-rw-r--
Owner                          : system
Group                          : graphics
SELinux                        : u:object_r:graphics_device:s0
Major/minor                    : 29,0
Class                          : CONFIRMED_CLASS_DEVICE

/dev/graphics directory:
Path                           : /dev/graphics
SELinux                        : u:object_r:graphics_device:s0
Entries                         : fb0

/sys class:
Path                           : /sys/class/graphics/fb0
Target                         : ../../devices/platform/mtkfb@0/graphics/fb0
SELinux                        : u:object_r:sysfs:s0

fb0 attributes:
bits_per_pixel                 : 32
blank                          : empty return
console                        : empty return
cursor                         : empty return
dev                            : 29:0
mode                           : empty return
modes                          : U:480x640p-0
name                           : mtkfb
pan                            : 0,1280
rotate                         : 0
state                          : 0
stride                         : 1920
uevent                         : MAJOR=29 / MINOR=0 / DEVNAME=fb0
virtual_size                   : 480,1920

################################################################################
# 8.8 — SURFACEFLINGER / DISPLAY RUNTIME REGISTER
################################################################################

SurfaceFlinger build/runtime:
Build configuration            : sf PRESENT_TIME_OFFSET=0
FORCE_HWC_FOR_RBG_TO_YUV       : 1
MAX_VIRT_DISPLAY_DIM           : 0
RUNNING_WITHOUT_SYNC_FRAMEWORK : 0
NUM_FRAMEBUFFER_SURFACE_BUFFERS: 3
Display identification data    : no identification data
Wide-color built-in display    : 0
DisplayColorSetting            : Managed
Display 0 color mode           : ColorMode::NATIVE (0)
Current color mode             : ColorMode::NATIVE (0)

DisplayDevice:
Entry count                    : 1
Display                        : DisplayDevice{0, primary, "Internal display"}
powerMode                      : 0
activeConfig                   : 0
numLayers                      : 0
isEnabled                      : false
isSecure                       : true
layerStack                     : 4294967295
layerStackInternal             : true
colorMode                      : NATIVE (0)
renderIntent                   : COLORIMETRIC (0)
dataspace                      : UNKNOWN (0)
colorTransform                 : 0
wideColorGamut                 : false
hdr10plus                      : false
hdr10                          : false
hlg                            : false
dv                             : false
metadata                       : 0
orientation                    : 1
isPoweredOn                    : 0
refresh-rate                   : 59.999999 fps

Observed active buffer geometries:
480 x 640 RGBA_8888
640 x 480 RGBA_8888
1024 x 1243 RGBA_8888
1 x 640 RGBA_8888

################################################################################
# 8.9 — GRAPHICS HAL / FILE REGISTER
################################################################################

Service state:
init.svc.vendor.hwcomposer-2-1 : running
init.svc.vendor.gralloc-2-0    : running

lshal graphics registrations:
android.hardware.graphics.allocator@2.0::IAllocator/default
android.hardware.graphics.composer@2.1::IComposer/default
android.hardware.graphics.mapper@2.1::IMapper/default

64-bit graphics HAL files:
| File | Size bytes | SELinux label |
|---|---:|---|
| /vendor/lib64/hw/android.hardware.graphics.allocator@2.0-impl.so | 24904 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/android.hardware.graphics.composer@2.1-impl.so | 104224 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 38696 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib64/hw/gralloc.default.so | 23824 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib64/hw/gralloc.mt6765.so | 616632 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib64/hw/hwcomposer.mt6765.so | 682224 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/vulkan.mt6765.so | 683024 | u:object_r:same_process_hal_file:s0 |

32-bit graphics HAL files:
| File | Size bytes | SELinux label |
|---|---:|---|
| /vendor/lib/hw/android.hardware.graphics.allocator@2.0-impl.so | 24580 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/android.hardware.graphics.composer@2.1-impl.so | 79400 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 29980 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib/hw/gralloc.default.so | 28788 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib/hw/gralloc.mt6765.so | 552832 | u:object_r:same_process_hal_file:s0 |
| /vendor/lib/hw/vulkan.mt6765.so | 543016 | u:object_r:same_process_hal_file:s0 |

Vendor display string surfaces:
vendor.mediatek.hardware.pq@2.3-impl.so : PictureQuality getLcmIndexOfGamma / LCM index / DISP_IOCTL_GET_LCMINDEX
hwcomposer.mt6765.so                    : lcm_color_mode / lcm_degree
libacdk.so                              : width / height / mLCMOrientation
libvcodecdrv.so                         : h264_enc get LCM info fail
meta_tst                                : FT_UTILCMD_LCD_COLOR_TEST / FT_UTILCMD_MAIN_SUB_LCD_LIGHT_LEVEL

################################################################################
# 8.10 — LIGHT / BACKLIGHT / FLASHLIGHT REGISTER
################################################################################

/sys/class/leds inventory:
| Name | Target | Brightness | Max | Trigger | Class |
|---|---|---:|---:|---|---|
| lcd-backlight | ../../devices/platform/leds-mt65xx/leds/lcd-backlight | 0 | 255 | [none] charger-online ac-online usb-online battery-charging-or-full battery-charging battery-full battery-charging-blink-full-solid mmc0 timer mmc1 | CONFIRMED_CLASS_DEVICE |
| mt6370_pmu_bled | ../../devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_bled/leds/mt6370_pmu_bled | 255 | 255 | [none] charger-online ac-online usb-online battery-charging-or-full battery-charging battery-full battery-charging-blink-full-solid mmc0 timer mmc1 | CONFIRMED_CLASS_DEVICE / OPEN_PHYSICAL_LEAD |
| vibrator | ../../devices/platform/odm/odm:vibrator@0/leds/vibrator | 0 | 255 | [none] charger-online ac-online usb-online battery-charging-or-full battery-charging battery-full battery-charging-blink-full-solid mmc0 timer mmc1 | CONFIRMED_CLASS_DEVICE |

/sys/class/flashlight inventory:
| Name | Target | Mode | Torch brightness | Torch max | Strobe brightness | Strobe max | Timeout | Driver name | Type | Class |
|---|---|---|---:|---:|---:|---:|---:|---|---|---|
| mt-flash-led1 | /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_fled.0/rt-flash-led.0/flashlight/mt-flash-led1 | Off | 0 | 31 | 0 | 256 | 0 | mt6370-fled | LED | CONFIRMED_CLASS_DEVICE / OPEN_PHYSICAL_LEAD |
| mt-flash-led2 | /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_fled.1/rt-flash-led.1/flashlight/mt-flash-led2 | Off | 0 | 31 | 0 | 256 | 0 | mt6370-fled | LED | CONFIRMED_CLASS_DEVICE / OPEN_PHYSICAL_LEAD |
| mt6370_pmu_bled | /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_bled/rt-flash-led.2/flashlight/mt6370_pmu_bled | Off | 128 | 255 | 255 | 255 | 208 | mt6370_pmu_bled | LED | CONFIRMED_CLASS_DEVICE / OPEN_PHYSICAL_LEAD |

PMIC bus cross-reference:
MT6370 PMU bus                 : I2C5 / 5-0034
Bus ownership                  : Section 4
Power/LED rail ownership       : Section 6
Endpoint class ownership       : Section 8

################################################################################
# 8.11 — TOUCH / INPUT DEVICE REGISTER
################################################################################

/proc/bus/input/devices:
| Event | Name | Bus | Vendor | Product | Version | Sysfs | Handlers | Class |
|---|---|---|---|---|---|---|---|---|
| event0 | mtk-kpd | 0019 | 2454 | 6500 | 0010 | /devices/platform/10010000.kp/input/input0 | event0 | CONFIRMED_CLASS_DEVICE |
| event1 | hyn_ts | 0018 | 0000 | 0000 | 0000 | /devices/platform/11007000.i2c0/i2c-0/0-005d/input/input2 | event1 | CONFIRMED_PHYSICAL_TOUCH_PATH |
| event2 | mtk-tpd | 0000 | 0000 | 0000 | 0000 | /devices/virtual/input/input1 | event2 | VIRTUAL_AGGREGATION_LAYER |

/dev/input nodes:
| Node | Type | Owner | Group | SELinux | Major/minor |
|---|---|---|---|---|---|
| /dev/input/event0 | char | root | input | u:object_r:input_device:s0 | 13,64 |
| /dev/input/event1 | char | root | input | u:object_r:input_device:s0 | 13,65 |
| /dev/input/event2 | char | root | input | u:object_r:input_device:s0 | 13,66 |

################################################################################
# 8.12 — HYNITRON TOUCH CONTROLLER REGISTER
################################################################################

I2C child path:
Path                           : /sys/bus/i2c/devices/0-005d
Resolved path                  : /sys/devices/platform/11007000.i2c0/i2c-0/0-005d
Bus                            : I2C0
Address                        : 0x5d
Device name                    : cap_touch
modalias                       : i2c:cap_touch
Driver                         : hyn_ts
Driver path                    : /sys/bus/i2c/drivers/hyn_ts
OF_NAME                        : cap_touch
OF_FULLNAME                    : /i2c0@11007000/cap_touch@5d
OF_COMPATIBLE_0                : mediatek,cap_touch
OF_COMPATIBLE_N                : 1
Evidence class                 : CONFIRMED_PHYSICAL_TOUCH_PATH

hyn_ts event1 decoded capabilities:
KEY                            : KEY_W KEY_E KEY_U KEY_O KEY_S KEY_L KEY_Z KEY_C KEY_V KEY_M KEY_UP KEY_LEFT KEY_RIGHT KEY_DOWN KEY_POWER BTN_TOOL_FINGER BTN_TOUCH
ABS_MT_SLOT                    : min 0 / max 4
ABS_MT_TOUCH_MAJOR             : min 0 / max 255
ABS_MT_WIDTH_MAJOR             : min 0 / max 200
ABS_MT_POSITION_X              : min 0 / max 480
ABS_MT_POSITION_Y              : min 0 / max 640
ABS_MT_TRACKING_ID             : min 0 / max 5
ABS_MT_PRESSURE                : min 0 / max 255
Input prop                     : INPUT_PROP_DIRECT

hyn_ts raw event codes:
KEY codes                      : 0011 0012 0016 0018 001f 0026 002c 002e 002f 0032 0067 0069 006a 006c 0074 0145 014a
ABS codes                      : 002f 0030 0032 0035 0036 0039 003a

################################################################################
# 8.13 — MTK TPD VIRTUAL INPUT REGISTER
################################################################################

mtk-tpd event2:
Path                           : /dev/input/event2
Sysfs                          : /sys/devices/virtual/input/input1/event2
Name                           : mtk-tpd
Bus                            : 0000
Vendor                         : 0000
Product                        : 0000
Version                        : 0000
Class                          : VIRTUAL_AGGREGATION_LAYER

Decoded capabilities:
KEY                            : BTN_TOUCH
ABS_X                          : min 0 / max 480 / resolution 480
ABS_Y                          : min 0 / max 640 / resolution 640
ABS_PRESSURE                   : min 0 / max 255
ABS_MT_TOUCH_MAJOR             : min 0 / max 100
ABS_MT_TOUCH_MINOR             : min 0 / max 100
ABS_MT_POSITION_X              : min 0 / max 480
ABS_MT_POSITION_Y              : min 0 / max 640
ABS_MT_TRACKING_ID             : min 0 / max 10
Input prop                     : INPUT_PROP_DIRECT

################################################################################
# 8.14 — MTK KEYPAD REGISTER
################################################################################

mtk-kpd event0:
Path                           : /dev/input/event0
Sysfs                          : /sys/devices/platform/10010000.kp/input/input0/event0
Name                           : mtk-kpd
Bus                            : 0019
Vendor                         : 2454
Product                        : 6500
Version                        : 0010
Class                          : CONFIRMED_CLASS_DEVICE
Physical mapping               : OPEN_PHYSICAL_LEAD per keycode

Decoded key capabilities:
KEY_A                          : declared by kernel input device
KEY_VOLUMEUP                   : declared by kernel input device
KEY_POWER                      : declared by kernel input device
KEY_BACK                       : declared by kernel input device
KEY_CAMERA                     : declared by kernel input device
KEY_RESTART                    : declared by kernel input device

Raw key codes:
001e
0073
0074
009e
00d4
0198

################################################################################
# 8.15 — INPUT CONFIGURATION FILE REGISTER
################################################################################

Device-specific search terms:
mtk-kpd
hyn_ts
mtk-tpd
cap_touch
2454
6500

Search roots:
/system/usr
/vendor/usr
/product/usr

Search result:
Device-specific matches        : none returned
Class                          : ABSENT_IN_CAPTURE

Observed system input config inventory:
/system/usr/idc/AVRCP.idc
/system/usr/idc/qwerty.idc
/system/usr/idc/qwerty2.idc
/system/usr/idc/rotary-encoder.idc
/system/usr/keychars/Generic.kcm
/system/usr/keychars/Vendor_18d1_Product_5018.kcm
/system/usr/keychars/Virtual.kcm
/system/usr/keychars/qwerty.kcm
/system/usr/keychars/qwerty2.kcm
/system/usr/keylayout/AVRCP.kl
/system/usr/keylayout/Generic.kl
/system/usr/keylayout/qwerty.kl
/system/usr/keylayout/Vendor_0079_Product_0011.kl
/system/usr/keylayout/Vendor_045e_Product_028e.kl
/system/usr/keylayout/Vendor_045e_Product_02d1.kl
/system/usr/keylayout/Vendor_045e_Product_02e0.kl
/system/usr/keylayout/Vendor_045e_Product_02e3.kl
/system/usr/keylayout/Vendor_045e_Product_02ea.kl
/system/usr/keylayout/Vendor_045e_Product_02fd.kl
/system/usr/keylayout/Vendor_046d_Product_b501.kl
/system/usr/keylayout/Vendor_046d_Product_c216.kl
/system/usr/keylayout/Vendor_046d_Product_c219.kl
/system/usr/keylayout/Vendor_046d_Product_c21d.kl
/system/usr/keylayout/Vendor_046d_Product_c21f.kl
/system/usr/keylayout/Vendor_046d_Product_c294.kl
/system/usr/keylayout/Vendor_046d_Product_c299.kl
/system/usr/keylayout/Vendor_046d_Product_c532.kl
/system/usr/keylayout/Vendor_054c_Product_0268.kl
/system/usr/keylayout/Vendor_054c_Product_0268_Version_8000.kl
/system/usr/keylayout/Vendor_054c_Product_0268_Version_8100.kl
/system/usr/keylayout/Vendor_054c_Product_0268_Version_8111.kl
/system/usr/keylayout/Vendor_054c_Product_05c4.kl
/system/usr/keylayout/Vendor_054c_Product_05c4_Version_8000.kl
/system/usr/keylayout/Vendor_054c_Product_05c4_Version_8100.kl
/system/usr/keylayout/Vendor_054c_Product_05c4_Version_8111.kl
/system/usr/keylayout/Vendor_054c_Product_09cc.kl
/system/usr/keylayout/Vendor_054c_Product_09cc_Version_8000.kl
/system/usr/keylayout/Vendor_054c_Product_09cc_Version_8100.kl
/system/usr/keylayout/Vendor_054c_Product_09cc_Version_8111.kl
/system/usr/keylayout/Vendor_054c_Product_0ba0.kl
/system/usr/keylayout/Vendor_054c_Product_0ba0_Version_8111.kl
/system/usr/keylayout/Vendor_057e_Product_2009.kl
/system/usr/keylayout/Vendor_0583_Product_2060.kl
/system/usr/keylayout/Vendor_05ac_Product_0239.kl
/system/usr/keylayout/Vendor_0b05_Product_4500.kl
/system/usr/keylayout/Vendor_0e6f_Product_02a4.kl
/system/usr/keylayout/Vendor_1038_Product_1412.kl
/system/usr/keylayout/Vendor_12bd_Product_d015.kl
/system/usr/keylayout/Vendor_1532_Product_0900.kl
/system/usr/keylayout/Vendor_1689_Product_fd00.kl
/system/usr/keylayout/Vendor_1689_Product_fd01.kl
/system/usr/keylayout/Vendor_1689_Product_fe00.kl
/system/usr/keylayout/Vendor_18d1_Product_2c40.kl
/system/usr/keylayout/Vendor_18d1_Product_5018.kl
/system/usr/keylayout/Vendor_1949_Product_0401.kl
/system/usr/keylayout/Vendor_1bad_Product_f016.kl
/system/usr/keylayout/Vendor_1bad_Product_f023.kl
/system/usr/keylayout/Vendor_1bad_Product_f027.kl
/system/usr/keylayout/Vendor_1bad_Product_f036.kl
/system/usr/keylayout/Vendor_1d79_Product_0009.kl
/system/usr/keylayout/Vendor_22b8_Product_093d.kl
/system/usr/keylayout/Vendor_2378_Product_1008.kl
/system/usr/keylayout/Vendor_2378_Product_100a.kl

################################################################################
# 8.16 — VIBRATOR / HAPTICS REGISTER
################################################################################

HAL registration:
android.hardware.vibrator@1.0::IVibrator/default
init.svc.vendor.vibrator-1-0   : running
Class                          : CONFIRMED_LIVE_RUNTIME

Sysfs node:
Path                           : /sys/class/leds/vibrator
Target                         : ../../devices/platform/odm/odm:vibrator@0/leds/vibrator
SELinux                        : u:object_r:sysfs:s0
brightness                     : 0
max_brightness                 : 255
trigger                        : [none] charger-online ac-online usb-online battery-charging-or-full battery-charging battery-full battery-charging-blink-full-solid mmc0 timer mmc1
uevent                         : empty return
Class                          : CONFIRMED_CLASS_DEVICE

Framework service state:
mCurrentVibration              : null
mCurrentExternalVibration      : null
mVibratorUnderExternalControl  : false
mLowPowerMode                  : false
mHapticFeedbackIntensity       : 2
mNotificationIntensity         : 2
mRingIntensity                 : 2

Observed historical haptic clients:
com.android.mms
com.touchtype.swiftkey
com.brave.browser
android
com.teslacoilsw.launcher
com.android.systemui

Observed haptic reason examples:
Notification delayed
Power - Long Press - Global Actions
keyboard / launcher / browser touch haptics

################################################################################
# 8.17 — ACCDET / JACK-DETECT REGISTER
################################################################################

ACCDET platform node:
Path                           : /sys/devices/platform/accdet
SELinux                        : u:object_r:sysfs:s0
of_node                        : ../../../firmware/devicetree/base/accdet
modalias                       : of:NaccdetT<NULL>Cmediatek,pmic-accdet
OF_NAME                        : accdet
OF_FULLNAME                    : /accdet
OF_COMPATIBLE_0                : mediatek,pmic-accdet
OF_COMPATIBLE_N                : 1
MODALIAS                       : of:NaccdetT<NULL>Cmediatek,pmic-accdet
driver_override                : (null)
Class                          : CONFIRMED_PLATFORM_NODE
Physical jack status           : OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE

AUXADC ACCDET readings:
in_voltage9_ACCDET_input/raw   : 562 / 1389

Related platform surfaces found:
/sys/devices/platform/11210000.usb1p_sif
/sys/devices/platform/11210000.usb_sif
/sys/devices/platform/11220000.audio/Voice_USB
/sys/devices/platform/11220000.audio/Voice_USB_ECHOREF
/sys/devices/platform/mt-pmic/ldo_vusb33_status
/sys/devices/platform/mt-pmic/ldo_vusb33_voltage
/sys/devices/platform/mt-pmic/mt-pmic:mt635x-auxadc/iio:device0/in_voltage9_ACCDET_input
/sys/devices/platform/mt-pmic/mt-pmic:mt635x-auxadc/iio:device0/in_voltage9_ACCDET_raw
/sys/devices/platform/mt-pmic/regulator/regulator.32/mt-pmic-vusb33
/sys/devices/platform/mt_charger/power_supply/usb
/sys/devices/platform/mt_soc_pcm_voice_usb
/sys/devices/platform/mt_soc_pcm_voice_usb_echoref
/sys/devices/platform/mt_usb
/sys/devices/platform/mt_usb/musb-hdrc
/sys/devices/platform/mt_usb/musb-hdrc/dual_role_usb
/sys/devices/platform/mt_usb/musb-hdrc/dual_role_usb/dual-role-usb20
/sys/devices/platform/mt_usb/musb-hdrc/udc/musb-hdrc
/sys/devices/platform/mt_usb/musb-hdrc/usb1
/sys/devices/platform/reg-dummy/regulator/regulator.0/musb-hdrc-va12
/sys/devices/platform/reg-dummy/regulator/regulator.0/musb-hdrc-vusb
/sys/devices/platform/reg-dummy/regulator/regulator.0/usb_phy_generic.0.auto-vcc
/sys/devices/platform/usb_phy_generic.0.auto

################################################################################
# 8.18 — AUDIO SERVICE / HAL FILE REGISTER
################################################################################

Service state:
init.svc.audioserver           : running
init.svc.vendor.audio-hal-2-0  : running

lshal registrations:
android.hardware.audio.effect@5.0::IEffectsFactory/default
android.hardware.audio@5.0::IDevicesFactory/default
android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default

64-bit audio HAL files:
| File | Size bytes | SELinux label |
|---|---:|---|
| /vendor/lib64/hw/audio.bluetooth.default.so | 92864 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.primary.default.so | 15584 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.primary.mt6765.so | 2042304 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.r_submix.default.so | 41960 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.r_submix.mt6765.so | 50176 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.usb.default.so | 28416 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio.usb.mt6765.so | 62968 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/audio_policy.stub.so | 15424 | u:object_r:vendor_file:s0 |
| /vendor/lib64/hw/sound_trigger.primary.mt6765.so | 49512 | u:object_r:vendor_file:s0 |

32-bit audio HAL files:
| File | Size bytes | SELinux label |
|---|---:|---|
| /vendor/lib/hw/audio.bluetooth.default.so | 71584 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.primary.default.so | 19820 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.primary.mt6765.so | 1948368 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.r_submix.default.so | 37788 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.r_submix.mt6765.so | 55048 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.usb.default.so | 24328 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio.usb.mt6765.so | 63488 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/audio_policy.stub.so | 15416 | u:object_r:vendor_file:s0 |
| /vendor/lib/hw/sound_trigger.primary.mt6765.so | 54400 | u:object_r:vendor_file:s0 |

Audio properties:
persist.vendor.audiohal.besloudness_state : 1
persist.vendor.audiohal.bt_headset_nrec_on : 0
persist.vendor.audiohal.firstboot          : 0
persist.vendor.bluetooth.a2dpstandbytime   : 500
ro.audio.silent                            : 0
ro.audio.usb.period_us                     : 16000
ro.boottime.audioserver                    : 15863973154
ro.boottime.vendor.audio-hal-2-0           : 15729867385
ro.vendor.mtk_audio_alac_support           : 1
ro.vendor.mtk_audio_ape_support            : 1
ro.vendor.mtk_audio_tuning_tool_ver        : V2.2
vendor.audiohal.speech.shm_init            : 1
log.tag.APM_AudioPolicyManager             : D
persist.log.tag.RpAudioControl             : I

################################################################################
# 8.19 — ALSA / PCM REGISTER
################################################################################

ALSA card:
/proc/asound/cards             : 0 [mtsndcard] mt-snd-card - mt-snd-card
/proc/asound/card0/id          : mtsndcard
Platform sound root            : /sys/devices/platform/11220000.audio/sound/card0
Class                          : CONFIRMED_CLASS_DEVICE

/dev/snd:
Directory SELinux              : u:object_r:audio_device:s0
Owner/group for PCM/control    : system / audio
Major                          : 116
Sequencer major                : 14
Nodes present                  : controlC0, pcmC0D0p..pcmC0D31c/p as listed, seq, sequencer, sequencer2, timer

PCM inventory:
| PCM | Name | Direction |
|---|---|---|
| 00-00 | MultiMedia1_PLayback mt-soc-codec-tx-dai-0 | playback |
| 00-01 | MultiMedia1_Capture mt-soc-codec-rx-dai-1 | capture |
| 00-02 | Voice_MD1_PLayback mt-soc-codec-voicemd1-dai-2 | playback + capture |
| 00-03 | ULDL_Loopback mt-soc-codec-uldlloopback-dai-3 | playback + capture |
| 00-04 | I2S0_PLayback mt-soc-i2s0-dummy-dai-codec-4 | playback |
| 00-05 | MRGRX_PLayback mt-soc-mrgrx-dai-codec-5 | playback + capture |
| 00-06 | MRGRX_CAPTURE mt-soc-mrgrx-dummy-dai-codec-6 | playback + capture |
| 00-07 | I2S0DL1_PLayback mt-soc-codec-I2s0tx-dai-7 | playback |
| 00-08 | Deep_Buffer_PLayback mt-soc-codec-deepbuffer-tx-dai-8 | playback |
| 00-09 | DL1_AWB_Record mt-soc-codec-dl1awb-dai-9 | capture |
| 00-10 | Voice_MD1_BT_Playback mt-soc-codec-voicemd1-bt-dai-10 | playback |
| 00-11 | VOIP_Call_BT_Playback mt-soc-codec-voipcall-btout-dai-11 | playback |
| 00-12 | VOIP_Call_BT_Capture mt-soc-codec-voipcall-btin-dai-12 | capture |
| 00-13 | TDM_Debug_Record mt-soc-tdmrx-dai-codec-13 | capture |
| 00-14 | FM_MRGTX_Playback mt-soc-fmmrg2tx-dummy-dai-codec-14 | playback |
| 00-15 | MultiMediaData2_Capture mt-soc-codec-rx-dai2-15 | capture |
| 00-16 | I2S0AWB_Capture mt-soc-codec-i2s0awb-dai-16 | capture |
| 00-17 | Voice_MD2_PLayback mt-soc-codec-voicemd2-dai-17 | playback + capture |
| 00-18 | MultiMedia_Routing mt-soc-dummy-dai-codec-18 | playback + capture |
| 00-19 | Voice_MD2_BT_Playback mt-soc-codec-voicemd2-bt-dai-19 | playback |
| 00-20 | HP_IMPEDANCE_Playback mt-soc-codec-hp-impedance-dai-20 | playback |
| 00-21 | FM_I2S_Playback mt-soc-fm-i2s-dai-codec-21 | playback |
| 00-22 | FM_I2S_Capture mt-soc-fm-i2s-dummy-dai-codec-22 | capture |
| 00-23 | MultiMedia2_PLayback mt-soc-codec-tx-dai2-23 | playback |
| 00-24 | MultiMedia3_PLayback mt-soc-codec-offload-dai-24 | playback |
| 00-25 | ANC_Record mt-soc-dummy-dai-codec-25 | capture |
| 00-26 | Voice_USB_PLayback mt-soc-codec-voiceusb-dai-26 | playback + capture |
| 00-27 | Voice_USB_EchoRef mt-soc-codec-voiceusb-echoref-dai-27 | playback |
| 00-28 | BTCVSD mt-soc-codec-btcvsd-dai-28 | playback + capture |
| 00-29 | Headphone_PLayback snd-soc-dummy-dai-29 | playback + capture |
| 00-30 | Speaker_PLayback snd-soc-dummy-dai-30 | playback + capture |
| 00-31 | I2S2ADC2_Capture snd-soc-dummy-dai-31 | capture |

################################################################################
# 8.20 — AUDIO CONFIG FILE REGISTER
################################################################################

System audio config files:
/system/etc/a2dp_audio_policy_configuration.xml
/system/etc/audio_effects.conf
/system/etc/audio_policy_configuration.xml
/system/etc/audio_policy_configuration_bluetooth_legacy_hal.xml
/system/etc/audio_policy_configuration_stub.xml
/system/etc/audio_policy_volumes.xml
/system/etc/bluetooth_audio_policy_configuration.xml
/system/etc/hearing_aid_audio_policy_configuration.xml
/system/etc/r_submix_audio_policy_configuration.xml
/system/etc/usb_audio_policy_configuration.xml

Vendor audio config files:
/vendor/etc/a2dp_audio_policy_configuration.xml
/vendor/etc/a2dp_in_audio_policy_configuration.xml
/vendor/etc/audio_device.xml
/vendor/etc/audio_effects.xml
/vendor/etc/audio_policy.conf
/vendor/etc/audio_policy_configuration.xml
/vendor/etc/audio_policy_configuration_bluetooth_legacy_hal.xml
/vendor/etc/audio_policy_volumes.xml
/vendor/etc/bluetooth_audio_policy_configuration.xml
/vendor/etc/r_submix_audio_policy_configuration.xml
/vendor/etc/usb_audio_policy_configuration.xml

Vendor audio_param files:
/vendor/etc/audio_param/USBDevice_AudioParam.xml
/vendor/etc/audio_param/USBDevice_ParamUnitDesc.xml
/vendor/etc/audio_param/UplinkConfiguration_AudioParam.xml
/vendor/etc/audio_param/UplinkConfiguration_ParamUnitDesc.xml
/vendor/etc/audio_param/VolumeGainMapUL_AudioParam.xml
/vendor/etc/audio_param/VolumeGainMapUL_ParamUnitDesc.xml
/vendor/etc/audio_param/VolumeGainMap_AudioParam.xml
/vendor/etc/audio_param/VolumeGainMap_ParamUnitDesc.xml
/vendor/etc/audio_param/Volume_AudioParam.xml
/vendor/etc/audio_param/Volume_ParamUnitDesc.xml

################################################################################
# 8.21 — AUDIO POLICY DECLARED DEVICE REGISTER
################################################################################

Primary module available output devices:
| Tag | Type | Address | Class |
|---|---|---|---|
| Earpiece | AUDIO_DEVICE_OUT_EARPIECE | none | DECLARED_ROUTE_ONLY / BSP_GHOST_CANDIDATE |
| Speaker | AUDIO_DEVICE_OUT_SPEAKER | none | DECLARED_ROUTE_ONLY + known user-facing route |
| Telephony Tx | AUDIO_DEVICE_OUT_TELEPHONY_TX | none | DECLARED_ROUTE_ONLY |

Primary module available input devices:
| Tag | Type | Address | Class |
|---|---|---|---|
| Built-In Mic | AUDIO_DEVICE_IN_BUILTIN_MIC | bottom | DECLARED_ROUTE_ONLY + likely physical endpoint |
| Built-In Back Mic | AUDIO_DEVICE_IN_BACK_MIC | back | DECLARED_ROUTE_ONLY / OPEN_PHYSICAL_LEAD |
| FM Tuner In | AUDIO_DEVICE_IN_FM_TUNER | none | DECLARED_ROUTE_ONLY / BSP_GHOST_CANDIDATE |
| Echo Ref In | AUDIO_DEVICE_IN_ECHO_REFERENCE | none | DECLARED_ROUTE_ONLY |
| Voice Call In | AUDIO_DEVICE_IN_TELEPHONY_RX + AUDIO_DEVICE_IN_VOICE_CALL | none | DECLARED_ROUTE_ONLY |
| Remote Submix In | AUDIO_DEVICE_IN_REMOTE_SUBMIX | 0 | VIRTUAL_AUDIO_ROUTE |

Primary output route supported devices:
Earpiece
Speaker
Wired Headset
Wired Headphones
AUX Digital Out / HDMI
BT SCO
BT SCO Headset
BT SCO Car Kit
Analog Dock Headset
Digital Dock Headset
FM Tuner Out
USB Primary Out / address usb_phone_call

Deep buffer supported devices:
Earpiece
Speaker
Wired Headset
Wired Headphones

Incall music output:
Telephony Tx

Primary input supported devices:
Built-In Mic / address bottom
Built-In Back Mic / address back
Wired Headset Mic
BT SCO Headset Mic
AUX Digital In / HDMI
USB Primary In / address usb_phone_call

Additional declared modules:
| Module route group | Declared devices | Class |
|---|---|---|
| Bluetooth A2DP output | BT A2DP Out, BT A2DP Headphones, BT A2DP Speaker | DECLARED_ROUTE_ONLY |
| Hearing aid output | BT Hearing Aid Out | DECLARED_ROUTE_ONLY |
| Bluetooth A2DP input | BT A2DP In | DECLARED_ROUTE_ONLY |
| USB output | USB Host Out, USB Device Out, USB Headset Out | DECLARED_ROUTE_ONLY |
| USB input | USB Device In, USB Headset In | DECLARED_ROUTE_ONLY |
| Remote submix | Remote Submix Out address 0, Remote Submix In address 0 | VIRTUAL_AUDIO_ROUTE |

################################################################################
# 8.22 — AUDIO ENDPOINT CLASSIFICATION TABLE
################################################################################

| Endpoint | Evidence | Classification | Physical status |
|---|---|---|---|
| Speaker | audio policy + PCM 00-30 Speaker_PLayback + owner physical observation | CONFIRMED_PHYSICAL + CONFIRMED_RUNTIME_ROUTE | confirmed user-facing endpoint |
| Built-In Mic | audio policy address bottom + capture PCM paths | DECLARED_ROUTE_ONLY + likely physical | physical confirmation should remain owner/evidence based |
| Built-In Back Mic | audio policy address back | OPEN_PHYSICAL_LEAD | not closed |
| Earpiece | audio policy declared | BSP_GHOST_CANDIDATE | not physically confirmed |
| Wired Headset | audio policy declared + ACCDET node | BSP_GHOST_CANDIDATE / OPEN_PHYSICAL_LEAD | physical jack not proven |
| Wired Headphones | audio policy declared + ACCDET node | BSP_GHOST_CANDIDATE / OPEN_PHYSICAL_LEAD | physical jack not proven |
| Wired Headset Mic | audio policy declared | BSP_GHOST_CANDIDATE / OPEN_PHYSICAL_LEAD | physical jack not proven |
| AUX Digital / HDMI Out | audio policy declared | BSP_GHOST_CANDIDATE | no physical HDMI proof in Section 8 |
| AUX Digital / HDMI In | audio policy declared | BSP_GHOST_CANDIDATE | no physical HDMI proof in Section 8 |
| Analog Dock Headset | audio policy declared | BSP_GHOST_CANDIDATE | no physical dock proof |
| Digital Dock Headset | audio policy declared | BSP_GHOST_CANDIDATE | no physical dock proof |
| FM Tuner Out | audio policy declared + FM PCM | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | RF/antenna/use not closed |
| FM Tuner In | audio policy declared + FM PCM | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | RF/antenna/use not closed |
| Echo Ref In | audio policy declared | DECLARED_ROUTE_ONLY | internal route |
| Voice Call In | audio policy declared | DECLARED_ROUTE_ONLY | telephony route |
| Telephony Tx | audio policy declared | DECLARED_ROUTE_ONLY | telephony route |
| BT SCO | audio policy declared | DECLARED_ROUTE_ONLY | depends Bluetooth stack |
| BT A2DP | audio policy declared | DECLARED_ROUTE_ONLY | depends Bluetooth stack |
| BT Hearing Aid | audio policy declared | DECLARED_ROUTE_ONLY | depends Bluetooth stack |
| USB audio | audio policy + USB voice PCM + USB platform nodes | DECLARED_ROUTE_ONLY / CONFIRMED_USB_ROUTE_SURFACE | physical use depends USB mode |
| Remote Submix | audio policy address 0 | VIRTUAL_AUDIO_ROUTE | not physical hardware |
| ANC_Record | PCM 00-25 | OPEN_PHYSICAL_LEAD | ANC mic not proven |
| Headphone_PLayback PCM | PCM 00-29 | DECLARED_ROUTE_ONLY / BSP_GHOST_CANDIDATE | physical headphone path not proven |

################################################################################
# 8.23 — FACTORY / TEST STRING SURFACES INSIDE SECTION 8 SCOPE
################################################################################

/vendor/bin/meta_tst strings observed:
FT_UTILCMD_LCD_COLOR_TEST
FT_UTILCMD_MAIN_SUB_LCD_LIGHT_LEVEL
FT_UTILCMD_VIBRATOR_ONOFF
FT_UTILCMD_KEYPAD_LED_ONOFF
FT_UTILCMD_SIGNAL_INDICATOR_ONOFF
FT_UTILCMD_CHECK_IF_FUNC_EXIST
FT_UTILCMD_SWITCH_WIFI_USB
FT_UTILCMD_QUERY_WCNDRIVER_READY
FT_UTILCMD_SDIO_AUTO_CALIBRATION

Classification:
meta_tst                       : FACTORY_TEST_SURFACE
LCD color test                 : Section 8 visual test surface
LCD light level                : Section 8 backlight/light test surface
Vibrator on/off                : Section 8 haptics test surface
Keypad LED on/off              : Section 8 input/light declared test surface
Signal indicator on/off        : Section 8 light/radio-adjacent test surface

################################################################################
# 8.24 — CONTRADICTION / OPEN-LEAD REGISTER
################################################################################

| ID | Item | Current database class | Blocking? | Required future proof |
|---|---|---|---|---|
| S08-OPEN-001 | NT35695B prior panel identity | SUPERSEDED_DATABASE_INFERENCE | no | DTBO/kernel/source/physical evidence if promoted again |
| S08-OPEN-002 | DTBO panel string search | INTERRUPTED_SEARCH | no | targeted non-hanging DTBO extraction/string search |
| S08-OPEN-003 | FLED1 physical mapping | OPEN_PHYSICAL_LEAD | no | physical flash test or board trace |
| S08-OPEN-004 | FLED2 physical mapping | OPEN_PHYSICAL_LEAD | no | physical flash test or board trace |
| S08-OPEN-005 | BLED physical mapping | OPEN_PHYSICAL_LEAD | no | display/backlight/board mapping proof |
| S08-OPEN-006 | ACCDET physical jack | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | no | physical jack/pogo/accessory path proof |
| S08-OPEN-007 | Earpiece route | BSP_GHOST_CANDIDATE | no | physical earpiece proof or removal validation |
| S08-OPEN-008 | Back mic route | OPEN_PHYSICAL_LEAD | no | microphone functional/directional test or board proof |
| S08-OPEN-009 | FM route | OPEN_PHYSICAL_LEAD / BSP_GHOST_CANDIDATE | no | RF/FM functional test or board proof |
| S08-OPEN-010 | ANC_Record | OPEN_PHYSICAL_LEAD | no | ANC mic/proc/route proof |
| S08-OPEN-011 | KEY_CAMERA physical button | OPEN_PHYSICAL_LEAD | no | getevent physical-button test |
| S08-OPEN-012 | KEY_RESTART physical button | OPEN_PHYSICAL_LEAD | no | getevent physical-button test |
| S08-OPEN-013 | hyn_ts gesture keycodes | OPEN_PHYSICAL_LEAD | no | controlled getevent gesture test |

################################################################################
# 8.25 — NEGATIVE / DEMOTION REGISTER
################################################################################

| Entry | Prior or declared source | Current Section 8 treatment |
|---|---|---|
| NT35695B | prior database inference | retained as superseded inference, not active panel truth |
| Physical headphone jack | ACCDET + audio policy | not confirmed; classify open/BSP ghost |
| HDMI audio | audio policy | declared route only; no Section 8 physical proof |
| Analog dock | audio policy | declared route only; no Section 8 physical proof |
| Digital dock | audio policy | declared route only; no Section 8 physical proof |
| Earpiece | audio policy | declared route only; BSP ghost candidate |
| FM tuner | audio policy + PCM | declared/runtime route; RF physical/function open |
| ANC mic | PCM ANC_Record | open physical lead |
| Device-specific keylayout for mtk-kpd/hyn_ts/mtk-tpd | grep search | absent in capture |
| /proc or /sys NT35695B active string | grep search | absent in capture |
| /proc or /sys ST7701 active grep | grep search | no grep hits beyond lcm_info path command; /proc/pl_lk direct source confirms ST7701 |

################################################################################
# 8.26 — CROSS-SECTION OWNERSHIP REGISTER
################################################################################

| Surface | Section 8 role | Owning/cross-reference section |
|---|---|---|
| I2C0 0x5d cap_touch | endpoint owner | Section 4 owns I2C controller fabric |
| I2C5 5-0034 MT6370 LEDs | endpoint owner | Section 4 owns I2C5; Section 6 owns MT6370/rails |
| DSI0 | endpoint owner | Section 4 owns MIPI DSI MMIO fabric; Section 5 owns IRQ/clock |
| Display clocks | endpoint list only | Section 5 owns clock topology |
| IRQ 267 dsi0 | endpoint list only | Section 5 owns IRQ topology |
| TOUCH_PANEL-eint | endpoint dependency | Section 5 owns IRQ topology |
| Afe_ISR_Handle / BTCVSD_ISR_Handle | endpoint dependency | Section 5 owns IRQ topology |
| vibrator regulator | haptic endpoint | Section 6 owns regulator/power truth |
| MT6370 BLED/FLED rails | light endpoint | Section 6 owns MT6370 power/charger/LED rail truth |
| nRF/PAR2822 sport/health plane | excluded from Section 8 | Section 7 owns coprocessor/sensor plane |
| Camera sensor identity | preview/flash adjacency only | Camera hardware/software docs own sensors |
| Bluetooth radio | audio route declaration only | Section 9 owns connectivity/radio topology |
| USB controller | USB audio route adjacency only | Section 4 owns USB/MUSB/UDC fabric |

################################################################################
# 8.27 — COMMAND / EVIDENCE LEDGER
################################################################################

Batch 01 captured:
/dev/graphics/fb0
/sys/class/graphics/fb0
/sys/class/leds/lcd-backlight
/sys/class/leds/mt6370_pmu_bled
/proc/bus/input/devices
/dev/input/event0
/dev/input/event1
/dev/input/event2
/sys/bus/i2c/devices/0-005d
/proc/asound/cards
/proc/asound/devices
/proc/asound/pcm
getprop audio-related properties
lshal audio/vibrator/graphics subset
dumpsys vibrator

Batch 02 captured:
init.svc.audioserver
init.svc.vendor.audio-hal-2-0
init.svc.vendor.vibrator-1-0
init.svc.vendor.hwcomposer-2-1
init.svc.vendor.gralloc-2-0
/vendor/lib*/hw graphics HAL files
dumpsys SurfaceFlinger targeted display state
getevent -lp /dev/input/event0..2
vendor/system audio config file inventory
vendor audio HAL file inventory
dumpsys media.audio_policy targeted route output

Batch 03 captured:
/sys/devices/platform/mtkfb@0
/sys/devices/platform display/DSI nodes
/proc and debugfs display surfaces
/system/usr keylayout/keychar/IDC inventory
getevent -i /dev/input/event0..2
ACCDET / accessory-detect platform node
/sys/class/flashlight full inventory
/dev/snd
/sys/class/sound
/proc/asound/card0 id and PCM info
dumpsys media.audio_policy declared-device continuation

Batch 04 captured:
/proc/driver/lcm_info
/proc/pl_lk LCM/DSI/display boot evidence
/proc and /sys active ST7701/NT35695 grep search
/vendor/lib*/vendor/bin display-related string search

Interrupted command:
DTBO / device-tree panel string search  : interrupted by ^C
Classification                          : INTERRUPTED_SEARCH
Negative-evidence status                : not usable as absence proof

################################################################################
# 8.28 — SECTION 8 CLOSURE SUMMARY
################################################################################

Closed endpoint groups:
LCM active identity              : closed
LK DSI boot parameters           : closed
Linux display platform nodes     : closed
fbdev runtime geometry           : closed
SurfaceFlinger display state     : closed
Graphics HAL file inventory      : closed
Backlight/LED/flashlight classes : closed as class devices, physical mapping open
Touch physical controller path   : closed
MTK TPD virtual path             : closed
MTK keypad advertised keys       : closed as input capabilities, physical mapping open
Input config search state        : closed for current capture
Vibrator HAL/sysfs/framework     : closed
ACCDET platform node             : closed as platform node, physical jack open
ALSA card/PCM inventory          : closed
Audio HAL file inventory         : closed
Audio policy declared devices    : closed
Audio physical route map         : closed with named open physical leads
NT35695B contradiction           : closed as superseded database inference, not active truth

Final database authority:
Active display LCM              : lk_st7701S_V3_480X640_dsi_vdo
Active display resolution       : 480 x 640
Active framebuffer              : mtkfb / fb0 / 32 bpp / stride 1920 / virtual 480x1920
Physical touch path             : I2C0 0x5d cap_touch / hyn_ts / event1
Virtual touch path              : mtk-tpd / event2
Keypad path                     : mtk-kpd / event0
Haptics path                    : vibrator HAL + /sys/class/leds/vibrator
Audio card                      : mt-snd-card / mtsndcard
Primary audio HAL               : audio.primary.mt6765.so
Graphics HAL                    : gralloc.mt6765.so / hwcomposer.mt6765.so / vulkan.mt6765.so

################################################################################
# END SECTION 8 — VISUAL, AUDIO & INPUT PIPELINES
################################################################################
