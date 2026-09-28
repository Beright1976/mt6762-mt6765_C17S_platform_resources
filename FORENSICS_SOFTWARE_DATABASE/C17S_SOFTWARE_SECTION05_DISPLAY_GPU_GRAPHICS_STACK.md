################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 05 — DISPLAY, GPU & GRAPHICS STACK
# 
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 05 — DISPLAY / GPU / GRAPHICS AUTHORITY MAP
# Closure     : CLOSED WITH RUNTIME-EXPOSED DSI PARAMETERS CLOSED AND PRIVATE PANEL-INIT ARRAYS NOT RUNTIME-EXPOSED
# Date        : 2026-06-16 live SurfaceFlinger, HWC, PVR, GED, backlight, DRM, HAL, and blob-manifest integration pass
################################################################################

################################################################################
# SCOPE
################################################################################
This section defines the software digital-twin authority for the C17S display,
GPU, EGL/GLES, Vulkan, OpenCL, graphics allocator, mapper, composer, SurfaceFlinger,
hardware composer, framebuffer, DRM/PVR kernel surface, GED DVFS surface, MTK
picture-quality layer, and LCD backlight control path.

Included:
- LCM/panel identity and correction of superseded NT35695B references.
- LK/DSI boot parameters, timing fields, lane count, framebuffer geometry, and
  live Linux fbdev state.
- LCD backlight and MT6370 PMU BLED software-visible path.
- PowerVR GE8320 / Rogue / MEOW graphics stack.
- PVR kernel-driver identity, debugfs status, firmware status, and memory statistics.
- GED / MFG DVFS, OPP, frequency, and clock surfaces.
- EGL/GLES, Vulkan, OpenCL, IMG/PVR, MTK GPU, and MTK PQ library inventory.
- Graphics HAL chain: allocator, mapper, gralloc, composer, hwcomposer, gpuservice,
  SurfaceFlinger, and vendor MTK GPU/PQ extension HALs.
- SurfaceFlinger configuration, color capability, sync/fence configuration, native
  orientation, and HWC runtime binding.
- HWUI and framework graphics properties.
- Negative/BSP residue treatment for Mali references.

Excluded:
- Camera preview pipeline and camera ISP tuning: Section 06.
- Touch/input controller identity: Hardware Section 08 and software Section 04/07
  cross-reference only.
- Audio/video codec display consumers: Sections 06, 07, and media sections.
- Threat conclusions about ODM packages: Section 16.
- ROM build decisions beyond a small carry/strip relevance note at the end.

################################################################################
# SOURCE AUTHORITY
################################################################################
Source authority order:
TIER 1 : Live rooted C17S device output from adb shell under su.
TIER 2 : Clean TWRP-extracted system/vendor/product file authority.
TIER 3 : Completed software Sections 00 through 04.
TIER 4 : Hardware database Section 08 and hardware database core GPU identity.
TIER 5 : Targeted blob manifest generated from the live device.

Completed-section fingerprints consumed:
| Section | File | SHA256 |
|---|---|---|
| 00 | C17S_SOFTWARE_SECTION00_FINAL.md | 74f9979548846e2b76140fcb217cf8fb271a27b01c8e1b779380c93cce66a29f |
| 01 | C17S_SOFTWARE_SECTION01_FINAL.md | 6caa50c8a4bf13551efd7c7c226f46634ec1d1ebbfa2a698523cfc2a066f91df |
| 02 | C17S_SOFTWARE_SECTION02_FINAL.md | d7f1e17aef801e79ac8186390d6d3f24ef571c377f83006d0f3c04c4b904e704 |
| 03 | C17S_SOFTWARE_SECTION03_FINAL.md | e1d02ec03cd3c709c5991bd17af4fdd4af76af89de0dbf62124a23a2cdf69f76 |
| 04 | C17S_SOFTWARE_SECTION04_FINAL.md | 6be7d405e45e4000fce5d1d9c32a879f92b9f4c573994dd024a82da7a543dd99 |

Current Section 05 closure artifacts:
| Artifact | SHA256 | Role |
|---|---|---|
| SECTION05_GRAPHICS_BLOB_MANIFEST.tsv | 12ab0ff00657ffc594bb9abd3fd390cf1b984db849d7a2349ba10c22c0bf97d0 | targeted graphics/display/PVR/PQ/framework blob manifest |
| PASS01 live graphics runtime | inline terminal capture | properties, lshal, Binder services, process domains, module negative |
| PASS02 live display/framebuffer/backlight | inline terminal capture | LCM, fb0, debugfs, LED/BLED, device nodes |
| PASS03 live process maps/blob inventory | inline terminal capture + manifest | active userspace library chain |
| PASS04 live SurfaceFlinger/DRM/devfreq/HWUI | inline terminal capture | SF config, pvrsrvkm DRM, GED/MFG paths, features/properties |
| PASS05 live PVR/GED/MFG/BLED/negative close | inline terminal capture | PVR version/status, GED DVFS, MFG clocks, BLED uevent, Mali negative |

################################################################################
# RULES
################################################################################
Rules for this section:
1. No panel, GPU, EGL, HWC, or backlight value is accepted unless it is live-captured
   or file-authority captured.
2. The active panel is the live ST7701S value. NT35695B is retained only as a
   superseded prior inference.
3. `egl.cfg` string content is not silicon proof. The `mali` selector in egl.cfg is
   treated as config residue/name selection because DRM, PVR debugfs, process maps,
   and vendor blobs prove the active stack is PVR/IMG/Rogue/MEOW.
4. Absence from /proc/modules is not absence of a driver if sysfs/DRM/debugfs exposes
   a bound built-in or platform driver.
5. DSI private initialization command arrays are not invented. Runtime-exposed LK/DSI
   parameters are recorded; private panel vendor command arrays are classified as
   NOT_RUNTIME_EXPOSED unless source/DTB/LK disassembly exposes them.
6. File-present libraries are not automatically active. Active runtime is separately
   proven through init state, lshal, process maps, file descriptors, SurfaceFlinger,
   DRM, PVR, and GED evidence.
7. Search-hit false positives from the broad blob manifest are not promoted to graphics
   authority. They are demoted in this section rather than silently dropped.

################################################################################
# 05.0 — CAPTURE BOUNDARY
################################################################################
Capture identity:
| Field | Value | Classification |
|---|---|---|
| Device serial visible to adb | MTK0002507301608400 | LIVE_DEVICE |
| ro.hardware | mt6762 | LIVE_PROPERTY |
| ro.boot.hardware | mt6762 | LIVE_PROPERTY |
| ro.board.platform | mt6765 | LIVE_PROPERTY |
| ro.vendor.mediatek.platform | MT6765 | LIVE_PROPERTY |
| ro.build.display.id | APPLLP_5_MAX_LMT_WTC17S_EN_480_640_V1.5_20250613 | LIVE_PROPERTY |
| ro.sf.lcd_density | 160 | LIVE_PROPERTY |
| ro.hardware.egl | mtk | LIVE_PROPERTY |
| ro.opengles.version | 196610 / 0x00030002 / OpenGL ES 3.2 | LIVE_PROPERTY |
| ro.hwui.use_vulkan | empty | LIVE_PROPERTY / HWUI_VULKAN_NOT_FORCED |
| ro.surface_flinger.primary_display_orientation | 0 | LIVE_PROPERTY |
| vendor.debug.sf.hwc_pid | 526 | LIVE_PROPERTY / HWC_PID_CONFIRMED |
| vendor.debug.sf.hdr_enable | 0 | LIVE_PROPERTY / HDR_DISABLED |
| debug.sf.disable_backpressure | 1 | LIVE_PROPERTY |
| vendor.debug.sf.latch_unsignaled | 1 | LIVE_PROPERTY |
| ro.vendor.mtk_pq_support | 2 | LIVE_PROPERTY / PQ_SUPPORTED |
| ro.vendor.mtk_pq_color_mode | 1 | LIVE_PROPERTY / PQ_COLOR_MODE_PRESENT |

################################################################################
# 05.1 — COMPLETION MATRIX
################################################################################
| Scope item | Final state | Evidence / treatment |
|---|---|---|
| LCM/panel identity | CLOSED | ST7701S live from /proc/driver/lcm_info; NT35695B superseded |
| DSI / LK timing | CLOSED | LK register supplies mode, 1 lane, PLL clock, timing config, framebuffer pitch/format |
| Prior command-mode wording | CORRECTED | Runtime/LK authority says BURST_VDO_MODE, not active CMD-mode truth |
| Framebuffer | CLOSED | mtkfb, /dev/graphics/fb0, 480x640 mode, 480x1920 virtual, 32 bpp, stride 1920 |
| Backlight | CLOSED SOFTWARE PATH | LED-class lcd-backlight + MT6370 PMU BLED at I2C5 5-0034 |
| PowerVR GPU identity | CLOSED | GE8320/Rogue/MEOW from hardware DB plus PVR debugfs RGX/Rogue DDK |
| PVR kernel driver | CLOSED | DRM card/render/control bound to pvrsrvkm at 13000000.mfg_doma |
| PVR firmware/runtime status | CLOSED | PVR debugfs: Driver OK, Firmware OK, Rogue DDK 1.11@5425693 |
| GED / GPU DVFS | CLOSED | GED debugfs exposes utilization, current/previous freq, freq-level count, FPS bound |
| MFG clocks | CLOSED | mfgpll/mfg_ck/mfg_sel/pg_mfg clocks captured |
| HWC/allocator/mapper | CLOSED | lshal, init, process maps, FDs, and blobs captured |
| SurfaceFlinger | CLOSED | SF config, color model, sync config, VSYNC, binder service captured |
| HWUI | CLOSED | libhwui hashes, ro.hwui.use_vulkan empty, system animation settings captured |
| EGL config | CLOSED | /vendor/lib/egl/egl.cfg hash and content captured |
| MEOW | CLOSED | libGLES_meow and libMEOW_trace present in 32-bit and 64-bit vendor EGL dirs |
| Vulkan | CLOSED | feature declaration + vendor vulkan.mt6765.so 32/64 present with hashes |
| OpenCL | CLOSED | libOpenCL + libPVROCL 32/64 present with hashes |
| Mali negative | CLOSED | no Mali module files/nodes; /proc/modules only fpsgo matched; egl.cfg mali string demoted |
| Complete blob inventory | CLOSED | targeted manifest embedded with hashes, sizes, labels, UID/GID, modes |
| Private DSI panel init arrays | NOT_RUNTIME_EXPOSED | not invented; runtime-exposed DSI/LK parameters closed |
################################################################################
# 05.2 — HIGH-LEVEL ARCHITECTURE
################################################################################
Software/render path:
App / framework renderer
  → system EGL/GLES loader libraries
  → vendor MTK EGL/GLES MEOW implementation
  → PowerVR Rogue / RGX / GE8320 userspace stack
  → gralloc.mt6765.so buffer allocation
  → mapper@2.1 passthrough implementation
  → ION / DMA-BUF / sync_file / pvr_sync
  → SurfaceFlinger
  → HWC2 composer service
  → hwcomposer.mt6765.so
  → MTK display manager / CMDQ / MDP sync / display pipeline
  → mtkfb / DSI0 / ST7701S panel

Kernel/runtime GPU path:
DRM card0/renderD128/controlD64
  → platform device 13000000.mfg_doma
  → driver pvrsrvkm
  → /sys/kernel/debug/pvr
  → /sys/kernel/debug/ged
  → MFG clocks and GED DVFS

Backlight path:
Android light service / framework brightness setting
  → leds-mt65xx lcd-backlight class surface
  → MT6370 PMU BLED at I2C5 address 5-0034
  → display PWM / BLED output

################################################################################
# 05.3 — LCM / PANEL IDENTITY
################################################################################
Live panel identity:
```text
/proc/driver/lcm_info
Name: lk_st7701S_V3_480X640_dsi_vdo
Reso: 480 x 640
```

Final panel authority:
| Field | Value |
|---|---|
| Active LCM name | lk_st7701S_V3_480X640_dsi_vdo |
| Panel driver family | ST7701S |
| Active resolution | 480 x 640 |
| Source | /proc/driver/lcm_info and hardware Section 08 /proc/pl_lk register |
| NT35695B treatment | superseded prior inference, not active panel truth |
| Active NT35695B runtime proof | none captured |
| Active ST7701S runtime proof | closed |

The prior NT35695B display entry must not be carried as the active display identity.
It may remain only in negative/reconciliation registers as a superseded BSP or
database inference. Section 05 active truth is ST7701S.

################################################################################
# 05.4 — LK / DSI BOOT PARAMETERS
################################################################################
The hardware database Section 08 preserves /proc/pl_lk display probe fields for
the active ST7701S panel. These are the authoritative runtime-exposed DSI/LK
parameters available in the current capture.

| Field | Value |
|---|---|
| LCM name | lk_st7701S_V3_480X640_dsi_vdo |
| Resolution | 480 x 640 |
| Type | DSI |
| DSI mode | BURST_VDO_MODE |
| Lane count | 1 |
| data_format | (0,2,0,0) |
| vact | 4 |
| vbp | 20 |
| vfp | 20 |
| vact_line | 640 |
| hact | 5 |
| hbp | 30 |
| hfp | 30 |
| hactive | 480 |
| PLL_CLOCK | 320 |
| DSI PHY pll | 320 |
| DSI PHY cycle time | 13 ns |
| DSI PHY unit interval | 2 ns |
| HS_TRAIL | 6 |
| HS_ZERO | 11 |
| HS_PRPR | 5 |
| LPX | 6 |
| TA_GET | 30 |
| TA_SURE | 9 |
| TA_GO | 24 |
| CLK_TRAIL | 8 |
| CLK_ZERO | 30 |
| CLK_HS_PRPR | 4 |
| DSI_PHY_TIMCONFIG | 0x060b0506,0x0c1e0918,0x081e0100,0x000c0f04 |
| DSI read packet_type | 0x21 |
| LCM register read | 0x0A = 156 |
| LCM connected | yes |
| dpmgr scenario | primary_disp |
| dst module | dsi0 |
| mutex | mutex 0 |
| connect path | ovl0 to dsi0 |
| Framebuffer pitch | 1920 |
| Framebuffer format | eBGRA8888 |
| Layer dst rectangle | 0,0,480,640 |
| MTK_LCM_PHYSICAL_ROTATION | 0 |
| LCM_HEIGHT | 307 |
| LCM_WIDTH | 546 |
| videolfb islcmfound | 1 |
| videolfb lcmname | lk_st7701S_V3_480X640_dsi_vdo |

Correction:
The outline phrase "DSI command mode parameters" is superseded by the captured
LK display register showing DSI Mode = BURST_VDO_MODE. Section 05 records the
live boot value and does not promote command-mode wording as active truth.

Private panel initialization command arrays:
No live sysfs/debugfs/proc output exposed the raw vendor panel init command table.
That table is classified as NOT_RUNTIME_EXPOSED, not unknown hardware. It can only
be closed by LK/kernel source, DTB/LK extraction, or disassembly if needed later.

################################################################################
# 05.5 — FRAMEBUFFER AND DISPLAY DEBUGFS
################################################################################
Live framebuffer authority:
| Field | Value |
|---|---|
| /proc/fb | 0 mtkfb |
| fb0 name | mtkfb |
| fb0 mode | U:480x640p-0 |
| fb0 virtual_size | 480,1920 |
| fb0 bits_per_pixel | 32 |
| fb0 stride | 1920 |
| fb0 blank | empty return |
| /dev/graphics/fb0 | crw-rw-r-- system:graphics u:object_r:graphics_device:s0 29,0 |
| /dev/graphics directory | u:object_r:graphics_device:s0 |

Interpretation:
The visible panel is 480 x 640. The virtual framebuffer is 480 x 1920, which is
three times the physical panel height and matches the SurfaceFlinger build config
of NUM_FRAMEBUFFER_SURFACE_BUFFERS=3. The stride is 1920 bytes, consistent with
480 pixels × 4 bytes per pixel.

Display debugfs:
| Path | SELinux | Returned value |
|---|---|---|
| /sys/kernel/debug/disp | u:object_r:debugfs_fb:s0 | directory present |
| /d/disp | u:object_r:debugfs_fb:s0 | directory present |
| /sys/kernel/debug/disp/dump | u:object_r:debugfs_fb:s0 | idlemgr disable mtcmos now, all the regs may 0x00000000 |
| /sys/kernel/debug/disp/lowpowermode | u:object_r:debugfs_fb:s0 | unknown mode(n) |

Display device nodes:
| Node | Label | Major:Minor | Mode | Owner |
|---|---|---:|---:|---|
| /dev/graphics/fb0 | u:object_r:graphics_device:s0 | 29:0 | 664 | system:graphics |
| /dev/mtk_disp_mgr | u:object_r:graphics_device:s0 | 234:0 | 664 | system:graphics |
| /dev/disp_freq | u:object_r:device:s0 | 10:24 | 600 | root:root |
| /dev/mtk_cmdq | u:object_r:mtk_cmdq_device:s0 | 248:0 | 644 | system:system |
| /dev/ion | u:object_r:ion_device:s0 | 10:62 | 664 | system:graphics |

################################################################################
# 05.6 — BACKLIGHT / MT6370 PMU BLED
################################################################################
Normal Android backlight class result:
`/sys/class/backlight/*` did not resolve to a normal backlight device in the live
capture. The display brightness path is LED-class based.

LED-class backlight authority:
| LED node | Resolved path | Max | Brightness | Driver / uevent |
|---|---|---:|---:|---|
| /sys/class/leds/lcd-backlight | /sys/devices/platform/leds-mt65xx/leds/lcd-backlight | 255 | 0 | DRIVER=leds-mt65xx; MODALIAS=platform:leds-mt65xx |
| /sys/class/leds/mt6370_pmu_bled | /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_bled/leds/mt6370_pmu_bled | 255 | 255 | DRIVER=mt6370_pmu_bled; OF_NAME=bled; OF_COMPATIBLE_0=mediatek,mt6370_pmu_bled |

MT6370 BLED authority:
| Field | Value |
|---|---|
| PMU / LED IC surface | MT6370 PMU BLED |
| I2C controller | 11016000.i2c5 |
| I2C address | 5-0034 |
| Device path | /sys/devices/platform/11016000.i2c5/i2c-5/5-0034/mt6370_pmu_bled |
| OpenFirmware node | /mt6370_pmu_dts/bled |
| Compatible | mediatek,mt6370_pmu_bled |
| Class | CONFIRMED_SOFTWARE_BACKLIGHT_PATH |

Dmesg backlight evidence:
```text
light@2.0-servi [PWM] disp_pwm_set_backlight_cmdq: id=0x1, level_1024=449, old=0
light@2.0-servi [PWM] disp_pwm_backlight_status: backlight is on (449), power:(1), pwm id:(0)
light@2.0-servi [PWM] disp_pwm_set_backlight_cmdq: id=0x1, level_1024=40, old=449
light@2.0-servi [PWM] disp_pwm_set_backlight_cmdq: id=0x1, level_1024=0, old=40
HwBinder:526_2 [PWM] disp_pwm_backlight_status: backlight is off, power:(0), pwm id:(0)
```

The display software path is therefore closed as MTK light service / PWM /
leds-mt65xx / MT6370 PMU BLED. Board-level optical trace routing remains hardware
Section 08/06 ownership and does not block Section 05 software closure.

################################################################################
# 05.7 — RUNTIME HAL / HIDL / BINDER SURFACE
################################################################################
Init service states:
| Service | State |
|---|---|
| surfaceflinger | running |
| gpu | running |
| vendor.gralloc-2-0 | running |
| vendor.hwcomposer-2-1 | running |
| gpu-1-0 | running |
| pq-2-2 | running |

lshal graphics/display registrations:
| Interface | Registration class |
|---|---|
| android.frameworks.displayservice@1.0::IDisplayService/default | framework manifest |
| android.hardware.configstore@1.0::ISurfaceFlingerConfigs/default | device manifest |
| android.hardware.configstore@1.1::ISurfaceFlingerConfigs/default | device/FC |
| android.hardware.graphics.allocator@2.0::IAllocator/default | device/FC active, PID 525 |
| android.hardware.graphics.composer@2.1::IComposer/default | device/FC active, PID 526 |
| android.hardware.graphics.mapper@2.1::IMapper/default | passthrough/client-loaded, wide client set |
| vendor.mediatek.hardware.gpu@1.0::IGraphicExt/default | device manifest, service active |
| vendor.mediatek.hardware.pq@2.0::IPictureQuality/default | declared |
| vendor.mediatek.hardware.pq@2.1::IPictureQuality/default | declared |
| vendor.mediatek.hardware.pq@2.2::IPictureQuality/default | declared |
| vendor.mediatek.hardware.pq@2.3::IPictureQuality/default | active through pq PID 706 / impl mapped |

Binder services:
| Binder service | Interface |
|---|---|
| graphicsstats | android.view.IGraphicsStats |
| color_display | android.hardware.display.IColorDisplayManager |
| display | android.hardware.display.IDisplayManager |
| SurfaceFlinger | android.ui.ISurfaceComposer |
| gpu | android.graphicsenv.IGpuService |

################################################################################
# 05.8 — PROCESS DOMAIN / SERVICE MAP
################################################################################
| PID | Command | SELinux domain | User/group | Section 05 role |
|---|---|---|---|---|
| 515 | android.hidl.allocator@1.0-service | u:r:hal_allocator_default:s0 | system | ashmem allocator service |
| 525 | android.hardware.graphics.allocator@2.0-service | u:r:hal_graphics_allocator_default:s0 | system:graphics | active graphics allocator HAL |
| 526 | android.hardware.graphics.composer@2.1-service | u:r:hal_graphics_composer_default:s0 | system:graphics | active HWC2 composer HAL |
| 539 | vendor.mediatek.hardware.gpu@1.0-service | u:r:mtk_hal_gpu:s0 | system:system | active MTK GPU extension HAL |
| 547 | gpuservice | u:r:gpuservice:s0 | gpu_service:graphics | Android graphicsenv GPU service |
| 550 | surfaceflinger | u:r:surfaceflinger:s0 | system:graphics | active compositor |
| 706 | vendor.mediatek.hardware.pq@2.2-service | u:r:mtk_hal_pq:s0 | system:system | active MTK picture-quality HAL |

Process file-descriptor authority:
| PID | Process | Confirmed graphics FDs |
|---:|---|---|
| 525 | graphics allocator | /proc/ged, /dev/dri/renderD128, /dev/ion |
| 526 | graphics composer | /proc/ged, /dev/mtk_disp_mgr, /dev/mdp_sync, /dev/mtk_cmdq, /dev/dri/renderD128, /dev/ion, dmabuf, sync_file, gralloc_extra |
| 539 | MTK GPU HAL | /proc/ged |
| 547 | gpuservice | /dev/binder; no direct GPU node in captured FD subset |
| 550 | SurfaceFlinger | /dev/dri/renderD128, /dev/ion, /proc/ged, /dev/pvr_sync, dmabuf, sync_file, gralloc_extra |
| 706 | MTK PQ HAL | /dev/mtk_disp_mgr |

Active library-map authority:
| PID | Process | Key mapped Section 05 libraries |
|---:|---|---|
| 525 | graphics allocator | gralloc.mt6765.so, allocator@2.0-impl.so, libgralloc_extra.so, libmpvr.so, libion_mtk.so, libion_ulit.so, vendor.mediatek.hardware.gpu@1.0.so |
| 526 | graphics composer | hwcomposer.mt6765.so, composer@2.1-impl.so, gralloc.mt6765.so, mapper@2.0-impl-2.1.so, libmpvr.so, libpq_cust_base.so, libpq_prot.so, vendor.mediatek.hardware.gpu@1.0.so, vendor.mediatek.hardware.pq@2.0.so |
| 539 | MTK GPU HAL | vendor.mediatek.hardware.gpu@1.0.so, graphics mapper/allocator interface libs, libui |
| 547 | gpuservice | libvulkan.so, libgpud_sys.so, libgralloc_extra_sys.so, libui, graphics mapper/allocator interface libs |
| 550 | SurfaceFlinger | libEGL.so, libGLESv1_CM.so, libGLESv2.so, libEGL_mtk.so, libGLESv1_CM_mtk.so, libGLESv2_mtk.so, libIMGegl.so, libmpvr.so, libgpu_aux.so, libgpud.so, gralloc.mt6765.so, mapper@2.0-impl-2.1.so, libsurfaceflinger.so |
| 706 | MTK PQ HAL | pq@2.3-impl.so, vendor.mediatek.hardware.pq@2.0/2.1/2.2/2.3.so, libpq_cust_base.so, libpq_prot.so, vendor.mediatek.hardware.gpu@1.0.so, mapper/allocator libs |

################################################################################
# 05.9 — DRM / PVR / GPU DEVICE NODES
################################################################################
DRM sysfs authority:
| DRM node | Device path | dev | Driver | OF node | Compatible |
|---|---|---:|---|---|---|
| /sys/class/drm/card0 | ../../devices/platform/13000000.mfg_doma/drm/card0 | 226:0 | pvrsrvkm | /mfg_doma@13000000 | mediatek,doma |
| /sys/class/drm/renderD128 | ../../devices/platform/13000000.mfg_doma/drm/renderD128 | 226:128 | pvrsrvkm | /mfg_doma@13000000 | mediatek,doma |
| /sys/class/drm/controlD64 | ../../devices/platform/13000000.mfg_doma/drm/controlD64 | 226:64 | pvrsrvkm | /mfg_doma@13000000 | mediatek,doma |

/dev node authority:
| Node | Label | Major:Minor | Mode | Owner |
|---|---|---:|---:|---|
| /dev/dri/card0 | u:object_r:dri_device:s0 | 226:0 | 666 | root:graphics |
| /dev/dri/controlD64 | u:object_r:gpu_device:s0 | 226:64 | 666 | root:graphics |
| /dev/dri/renderD128 | u:object_r:gpu_device:s0 | 226:128 | 666 | root:graphics |
| /dev/pvr_sync | u:object_r:gpu_device:s0 | 10:13 | 666 | root:root |
| /dev/gpu_memory_bandwidth | u:object_r:device:s0 | 10:35 | 600 | root:root |
| /dev/ion | u:object_r:ion_device:s0 | 10:62 | 664 | system:graphics |
| /dev/MTK_SMI | u:object_r:MTK_SMI_device:s0 | 228:0 | 660 | media:media |

PVR kernel-driver conclusion:
The active kernel GPU driver surface is pvrsrvkm, bound to platform node
13000000.mfg_doma. The absence of a visible `pvr` or `pvrsrv` row in /proc/modules
is not absence of the driver; DRM/sysfs/debugfs prove the driver is present and
active, likely built-in or not represented as a loadable module entry in this capture.

################################################################################
# 05.10 — POWERVR / ROGUE / MEOW IDENTITY
################################################################################
GPU identity:
| Field | Value |
|---|---|
| GPU silicon | PowerVR GE8320 |
| Architecture | Rogue |
| Vendor family | Imagination Technologies / IMG / PVR |
| Renderer family | PowerVR Rogue GE8320 |
| Driver family | Rogue DDK Android |
| MEOW codename | confirmed by libGLES_meow.so and libMEOW_trace.so |
| Kernel DRM driver | pvrsrvkm |
| DRM platform | 13000000.mfg_doma |
| PVR debugfs device name | RGX |
| PVR hardware version | Rogue Version 22.87.104.18 (HW) |
| PVR firmware version | 1.11 @ 5425693 release, build options 0x80002010 mtk_android |
| PVR driver version | Rogue_DDK_Android rogueddk 1.11@5425693 release, build options 0x00002010 mtk_android |
| Firmware file | /vendor/firmware/rgx.fw |
| Firmware size | 118,784 bytes |

PVR debugfs status:
| Field | Value |
|---|---|
| Driver Status | OK |
| Firmware Status | OK |
| HWR Event Count | 0 |
| CRR Event Count | 0 |
| FWF Event Count | 0 |
| APM Event Count | 59219 |
| GPU Utilisation | 6% at capture time |

PVR debugfs build/apphint:
| Field | Value |
|---|---|
| HWPerfClientBufferSize | 0x000c0000 |
| HWRDebugDumpLimit | 0x00000001 |
| TimeCorrClock | mono |
| HWPerfClientFilter_Vulkan | 0x00000000 |
| HWPerfClientFilter_OpenCL | 0x00000000 |
| HWPerfClientFilter_OpenGLES | 0x00000000 |
| HWPerfClientFilter_EGL | 0x00000000 |
| HWPerfClientFilter_Services | 0x00000000 |

PVR memory statistics:
| Field | Value |
|---|---:|
| MemoryUsageKMalloc | 2,141,120 |
| MemoryUsageKMallocMax | 4,054,336 |
| MemoryUsageVMalloc | 69,632 |
| MemoryUsageVMallocMax | 102,400 |
| MemoryUsageAllocPTMemoryUMA | 958,464 |
| MemoryUsageAllocPTMemoryUMAMax | 1,822,720 |
| MemoryUsageAllocGPUMemUMA | 83,443,712 |
| MemoryUsageAllocGPUMemUMAMax | 173,109,248 |
| MemoryUsageAllocGPUMemUMAPool | 18,182,144 |
| MemoryUsageAllocGPUMemUMAPoolMax | 42,876,928 |
| MemoryUsageMappedGPUMemUMA/LMA | 138,117,120 |
| MemoryUsageMappedGPUMemUMA/LMAMax | 487,407,616 |

PVR memtrack notable Section 05 PIDs:
| PID | Role | AllocGPUMemUMA |
|---:|---|---:|
| 550 | SurfaceFlinger | 6,127,616 |
| 526 | HWC composer | 4,096 |
| 525 | graphics allocator | 217,088 |

################################################################################
# 05.11 — GED / GPU DVFS / OPP / MFG CLOCKS
################################################################################
GED runtime surfaces are present under /sys/kernel/debug/ged. These close the
GPU DVFS and OPP runtime side for Section 05.

GED values:
| Field | Value |
|---|---|
| integration_report GPU Utilization fp | 0000000083db37a9 |
| integration_report GPU DVFS idx commit fp | 00000000de02b0d1 |
| GPU clock notify on | 1 |
| GPU clock notify off | 1 |
| opp_logs | 168641, 107868, 6602716, \| 227262096294 |
| gpu_utilization | 0 0 100 |
| current_freqency | 0 650000 |
| previous_freqency | 1 500000 |
| total_gpu_freq_level_count | 3 |
| gpu_boost_level | -1 |
| fps_upper_bound | 60 |
| custom_upbound_gpu_freq | 0 |
| custom_boost_gpu_freq | 2 |
| custom_dvfs_mode | 0 |
| vsync_offset_level | 8300000 |
| gpu_tuner global feature | 00000000 |

MFG clock values:
| Clock | clk_rate | enable_count | prepare_count | flags | accuracy | phase |
|---|---:|---:|---:|---|---:|---:|
| pg_mfg | 0 | 0 | 0 | 0x00000008 | 0 | 0 |
| pg_mfg_core0 | 0 | 0 | 0 | 0x00000008 | 0 | 0 |
| pg_mfg_async | 0 | 0 | 0 | 0x00000008 | 0 | 0 |
| mfgpll_ck | 459999970 | 0 | 0 | 0x00000024 | 0 | 0 |
| mfg_ck | 26000000 | 0 | 0 | 0x00000024 | 0 | 0 |
| mfg_sel | 26000000 | 0 | 0 | 0x00000004 | 0 | 0 |
| mfgpll | 459999970 | 0 | 0 | 0x00000000 | 0 | 0 |

devfreq class:
| Path | Captured value |
|---|---|
| /sys/class/devfreq/10012000.dvfsrc | current value 0; governor helio_dvfsrc |

Conclusion:
No standalone GPU devfreq class was needed to close the GPU OPP side because GED
exposes the active GPU frequency state and total level count. Current capture shows
three GPU frequency levels, current index/frequency 0 / 650000, previous 1 / 500000,
and FPS upper bound 60.

################################################################################
# 05.12 — SURFACEFLINGER CONFIGURATION
################################################################################
SurfaceFlinger runtime:
| Field | Value |
|---|---|
| Display ID data | Display 0 / HWC display 0: no identification data |
| Wide color built-in display | 0 |
| Color management | 0 |
| DisplayColorSetting | Managed |
| Color modes | ColorMode::NATIVE (0) only |
| Current color mode | ColorMode::NATIVE (0) |
| Sync configuration | EGL_ANDROID_native_fence_sync, EGL_KHR_wait_sync |
| VSYNC period | 16,666,667 ns / 60 Hz |
| app phase | 8,300,000 ns |
| SF phase | 8,300,000 ns |
| early app phase | 8,300,000 ns |
| early SF phase | 8,300,000 ns |
| GL early app phase | 8,300,000 ns |
| GL early SF phase | 8,300,000 ns |
| present offset | 0 ns |
| Scheduler | enabled |
| Smart 90 video detection | off |
| Visible layers at capture | 64 |
| GraphicBufferProducers | 8 |
| max GraphicBufferProducers | 4096 |
| Total missed frame count | 34748 |
| HWC missed frame count | 34717 |
| GPU missed frame count | 0 |

SurfaceFlinger build configuration:
```text
sf PRESENT_TIME_OFFSET=0
FORCE_HWC_FOR_RBG_TO_YUV=1
MAX_VIRT_DISPLAY_DIM=0
RUNNING_WITHOUT_SYNC_FRAMEWORK=0
NUM_FRAMEBUFFER_SURFACE_BUFFERS=3
```

Framework/display settings:
| Setting/property | Value |
|---|---|
| animator_duration_scale | 0.0 |
| transition_animation_scale | 0.0 |
| window_animation_scale | 0.0 |
| wifi_display_on | 0 |
| wifi_display_max_resolution | 0 |
| wifi_display_portrait_resolution | 0 |
| wifi_display_power_saving_option | 1 |
| screen_brightness | 112 |
| screen_brightness_for_vr | 86 |
| screen_brightness_mode | 0 |
| screen_off_timeout | 30000 |
| user_rotation | 1 |
| accelerometer_rotation | 0 |
| is_round_screen_system | true |

Interpretation:
Native panel orientation remains ro.surface_flinger.primary_display_orientation=0.
The current user setting user_rotation=1 is runtime UI state, not panel identity.
The system advertises round-screen UI behavior even though the physical active LCM
is 480 x 640 rectangular ST7701S.

################################################################################
# 05.13 — HARDWARE COMPOSER / ALLOCATOR / MAPPER CHAIN
################################################################################
HWC active chain:
| Layer | Authority |
|---|---|
| Service | vendor.hwcomposer-2-1 running |
| Binary | /vendor/bin/hw/android.hardware.graphics.composer@2.1-service |
| PID | 526 |
| Domain | u:r:hal_graphics_composer_default:s0 |
| User/group | system:graphics |
| HIDL | android.hardware.graphics.composer@2.1::IComposer/default |
| Runtime implementation | /vendor/lib64/hw/hwcomposer.mt6765.so |
| Adapter implementation | /vendor/lib64/hw/android.hardware.graphics.composer@2.1-impl.so |
| Mapper implementation | /vendor/lib64/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so |
| Gralloc implementation | /vendor/lib64/hw/gralloc.mt6765.so |
| HWC PID property | vendor.debug.sf.hwc_pid=526 |

Allocator active chain:
| Layer | Authority |
|---|---|
| Service | vendor.gralloc-2-0 running |
| Binary | /vendor/bin/hw/android.hardware.graphics.allocator@2.0-service |
| PID | 525 |
| Domain | u:r:hal_graphics_allocator_default:s0 |
| User/group | system:graphics |
| HIDL | android.hardware.graphics.allocator@2.0::IAllocator/default |
| Runtime implementation | /vendor/lib64/hw/android.hardware.graphics.allocator@2.0-impl.so |
| Gralloc | /vendor/lib64/hw/gralloc.mt6765.so |
| Opens | /dev/dri/renderD128, /dev/ion, /proc/ged |

Mapper:
The active mapper interface is advertised as android.hardware.graphics.mapper@2.1
and loaded as passthrough by many clients. The vendor implementation file name is
android.hardware.graphics.mapper@2.0-impl-2.1.so, meaning a mapper 2.0 base
implementation with 2.1 compatibility/shim behavior. This is a normal MTK vendor
stack pattern and must be carried as-is unless replaced by a fully validated stack.

################################################################################
# 05.14 — EGL / GLES / MEOW CONFIGURATION
################################################################################
EGL property:
| Property | Value |
|---|---|
| ro.hardware.egl | mtk |

EGL config:
```text
Path   : /vendor/lib/egl/egl.cfg
SHA256 : 1606ef2f0ad0dfd812c8ec4ab7b7d9ae5bff3554191c7012530ca883612c70e1
Label  : u:object_r:same_process_hal_file:s0
Size   : 76 bytes

# NOTICE: This is an autogenerated file, do not edit\n
0 0 android
0 1 mali
```

EGL/GLES library directories:
| Directory | Contents |
|---|---|
| /system/lib/egl | libGLES_android.so |
| /system/lib64/egl | libGLES_android.so |
| /vendor/lib/egl | egl.cfg, libEGL_mtk.so, libGLES_meow.so, libGLESv1_CM_mtk.so, libGLESv2_mtk.so, libMEOW_trace.so |
| /vendor/lib64/egl | libEGL_mtk.so, libGLES_meow.so, libGLESv1_CM_mtk.so, libGLESv2_mtk.so, libMEOW_trace.so |

MEOW closure:
MEOW is confirmed by actual vendor files, not naming speculation:
| Path | Size | SHA256 |
|---|---:|---|
| /vendor/lib/egl/libGLES_meow.so | 1252340 | 0152c3186e298907d4852515a1bd4f1076351ef493577df246660e582675bc4f |
| /vendor/lib64/egl/libGLES_meow.so | 1999944 | 32fa3f4d9044a72251c2e310c000b038867f1dabd091ac7da67e9f206b7803e8 |
| /vendor/lib/egl/libMEOW_trace.so | 370644 | 01448f3479ae13bdc2d63a8dfec589bc04e907a94841546f06234025630d24ce |
| /vendor/lib64/egl/libMEOW_trace.so | 535640 | a1e0566272e1153d0a9a9e4b07c7aadde9277b7c1ba4f2b7510c01d9198b0843 |

Mali treatment:
The string `0 1 mali` in egl.cfg is not physical Mali proof. It is demoted to
EGL selector residue because the active kernel/sysfs/debugfs/process evidence
proves pvrsrvkm / PVR / IMG / Rogue / MEOW.

################################################################################
# 05.15 — VULKAN / OPENCL / FEATURE DECLARATIONS
################################################################################
Package-manager feature declarations:
| Feature | Captured value |
|---|---|
| android.hardware.opengles.aep | present |
| android.hardware.vulkan.compute | present |
| android.hardware.vulkan.level | present |
| android.hardware.vulkan.version | 4198400 |
| android.software.activities_on_secondary_displays | present |
| android.software.picture_in_picture | present |

Vulkan authority:
| Path | Size | SHA256 |
|---|---:|---|
| /vendor/lib/hw/vulkan.mt6765.so | 543016 | d174bf26d577768fb1e34f2853192a4856a1752f20e623ca0a80add4bcafafd1 |
| /vendor/lib64/hw/vulkan.mt6765.so | 683024 | a0aec798ba07930dae09b6092b32e05e05af8326e6edd7e8683e50caf73acbb5 |
| /system/lib/libvulkan.so | 130272 | d4988826d8770ada756823c3a081dc3afa9af5c135e626f5a6881d31a4b5982d |
| /system/lib64/libvulkan.so | 176904 | 960229d0d1e276cf5e32b173068289c6c23b042eaf31f3a24e50e0f127e4b635 |

OpenCL authority:
| Path | Size | SHA256 |
|---|---:|---|
| /vendor/lib/libOpenCL.so | 65456 | 557462a70eeff80729fd69a2e981b3491d367618c39286cf2223c42490a18dce |
| /vendor/lib64/libOpenCL.so | 85456 | 3feb689544917c67e5a3ecef82639386dd25c9139e618af2bcb705c6aa2dc553 |
| /vendor/lib/libPVROCL.so | 25265440 | d323ef2ab3a8c9f6599113d2608db2e273ec4d6d2abe00ccc681dbf9cbfe1c85 |
| /vendor/lib64/libPVROCL.so | 34741696 | 2d10c57e0052c12bd30b9f8f6a76a4291e17e23cb660274b7c74e01f8d349809 |

HWUI:
| Field | Value |
|---|---|
| ro.hwui.use_vulkan | empty |
| /system/lib/libhwui.so | 6231444 bytes / 631243c64cc0eb604a2bf3e6aff17921b85542465bcdd646eb08b8166de73a2e |
| /system/lib64/libhwui.so | 8309472 bytes / 1f5e809f7cb4c7c50841d20986690eb8a82ccc825d62025be8704e56e1310e66 |

Conclusion:
The device advertises Vulkan and ships vendor Vulkan/OpenCL/PVR libraries, but HWUI
is not forced to Vulkan by property in this capture.

################################################################################
# 05.16 — PICTURE QUALITY / MTK DISPLAY EXTENSION
################################################################################
MTK PQ runtime:
| Field | Value |
|---|---|
| Service | pq-2-2 running |
| Binary | /vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service |
| PID | 706 |
| Domain | u:r:mtk_hal_pq:s0 |
| Active FD | /dev/mtk_disp_mgr |
| Active implementation | /vendor/lib64/hw/vendor.mediatek.hardware.pq@2.3-impl.so |
| HIDL declarations | pq@2.0, pq@2.1, pq@2.2, pq@2.3 |
| ro.vendor.mtk_pq_support | 2 |
| ro.vendor.mtk_pq_color_mode | 1 |

PQ process maps confirm vendor.mediatek.hardware.pq@2.0.so, @2.1.so, @2.2.so,
@2.3.so, libpq_cust_base.so, libpq_prot.so, libion_mtk.so, libion_ulit.so, and
vendor.mediatek.hardware.gpu@1.0.so. This is a live display-quality extension layer,
not a dead blob group.

################################################################################
# 05.17 — MALI NEGATIVE / BSP RESIDUE REGISTER
################################################################################
Mali negative evidence:
| Probe | Result |
|---|---|
| /proc/modules grep mali/pvrsrv/pvr/ged/gpu/fpsgo | fpsgo only |
| module file grep /vendor/lib/modules and /system/lib/modules | NO_MODULE_FILE_MATCH |
| /dev, /sys/class, /sys/kernel/debug mali node grep | no Mali node output |
| EGL config | contains `0 1 mali` selector line |
| DRM driver | pvrsrvkm |
| PVR debugfs | present |
| GED debugfs | present |
| SurfaceFlinger maps | IMG/PVR/MEOW/MTK stack |
| vendor EGL libs | libGLES_meow, libMEOW_trace, libIMGegl, libmpvr |

Final Mali classification:
- `mali` in egl.cfg: BSP/EGL selector residue.
- `mali.ko`: not loaded in current capture; no module file match captured.
- Physical/active GPU: PowerVR GE8320 / Rogue / RGX / pvrsrvkm.
- Do not carry Mali as hardware truth.

################################################################################
# 05.18 — CORE GRAPHICS BLOB MANIFEST
################################################################################
The following table is the curated Section 05 core manifest generated from the targeted live-device blob manifest. Full SHA256 values are preserved.
| class | path | size | mode | uid | gid | label | sha256 |
|---|---|---|---|---|---|---|---|
| ACTIVE_FRAMEWORK_SERVICE | /system/bin/gpuservice | 267520 | 755 | 0 | 2000 | u:object_r:gpuservice_exec:s0 | 22ead82b0cd6a5a0bf310b32fa36dbf4a4ecd30e4d528f0c68fc9c23e97a239d |
| ACTIVE_FRAMEWORK_SERVICE | /system/bin/surfaceflinger | 32976 | 755 | 1000 | 1003 | u:object_r:surfaceflinger_exec:s0 | 4a049e731828b901123205aa7f61b74ddafbba6c411b725c8de497b6ca5f1e00 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/android.hardware.graphics.allocator@2.0-service | 11584 | 755 | 0 | 2000 | u:object_r:hal_graphics_allocator_default_exec:s0 | de14c988341b1bd421ad97191b9d45ec176bcd468feb42704e72f9b97fae774c |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/android.hardware.graphics.composer@2.1-service | 11672 | 755 | 0 | 2000 | u:object_r:hal_graphics_composer_default_exec:s0 | 9a718498f8f8effc1bbcca7c1c9b92a2b6d0ea22b4eb7529c8b54e33fab28b89 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service | 29976 | 755 | 0 | 2000 | u:object_r:mtk_hal_gpu_exec:s0 | 6dd788d13979d080066ea915342579b187f2df4e637e7c8d3d7509aea4b4afe8 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service | 37448 | 755 | 0 | 2000 | u:object_r:mtk_hal_pq_exec:s0 | 859928c744e3eec38623b2024a960bef3faa27a9ab7842d9b2a12c82c053c0b1 |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/bin/mtkbootanimation | 25248 | 755 | 0 | 2000 | u:object_r:mtkbootanimation_exec:s0 | 02ae43b4af1517b1013559d91a8d08080add91ead560823280927da03c4a4102 |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/lib/libmtkbootanimation.so | 76208 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3427ded2fbccbb22517bf07b7f31a567a4df4ca15c442ce47a6f15d50d8c4fac |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/lib64/libmtkbootanimation.so | 93448 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | aca72f970131253a5dc2287ab6461ce07a81354f8d7575f83809e7379594deab |
| PQ_DISPLAY_ADJACENT | /system/lib/libPQDCjni.so | 24804 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f6d7b504f14365caf4532e0760e2078585f3f054e9461c349d1774790e2af408 |
| PQ_DISPLAY_ADJACENT | /system/lib/libPQjni.so | 20056 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 057859cdd836593c3bf11cbf79097daaa8403440bb77d0f4ff2f3b84664b4af1 |
| PQ_DISPLAY_ADJACENT | /system/lib/libjni_pq.so | 46940 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 5b1aa30b505f4ba5645707c19e8ba3babb5930b5efca31528e170c2b0e688a75 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libPQDCjni.so | 33072 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 0e18d8c9bd3219812ae96a4d606dd2bdc251aa12e85cd0e2807189a36d7a28c9 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libPQjni.so | 20328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bf2285cd757ea725636e9b6e7d79adcc496f4d5b6d3d6b8d2d7cbba9086a0dd4 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libjni_pq.so | 67568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 65bb2ccd7be020b41831dec8b123a0a3d26ed5bbd3bbf89b47295544e6642832 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/android.frameworks.vr.composer@1.0.so | 93080 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d3e52a1cf0506aa1256241772171a9b40d39aad9af772e62070a3ce787208fd5 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/android.hidl.allocator@1.0.so | 61728 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 95971c8824cb871c2c19494b126837a893fae8f530c6e378190c1c172266fa0e |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libion.so | 15168 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | cb8d1c51c87b95bf7e0cf9d887c1bd56194314377db8a819eb0fcc478dfbff6f |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libmediatek_exceptionlog.so | 15264 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 8c95fa1d5f3703100c3f5578286000c74bda2c145e8e45889aa60456079f03eb |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libpostalgo_grallocutils.so | 42704 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 05ab85fd9f362fa4cf75bd5d387349ec6e28c1b82f3ffc77220fd48bf6db009f |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/android.frameworks.vr.composer@1.0.so | 120704 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d9a357840a6c84863a15dc4b837b5b84fa811f994632c1c1f49f87958a022a00 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/android.hidl.allocator@1.0.so | 80176 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bcf6d57d2344d1a4828cf7598e6416b716083912cfc1f0986dfb94c7ba236f42 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libion.so | 15184 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 4317a7f5bb843674751ae7817010cd23b8fd14af2c437841b85bd4c083a749f1 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libmediatek_exceptionlog.so | 15400 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 10d30761da0b3816339ddce3fd0754aa20dce76964dea970a8d8e1e5dca132ac |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libpostalgo_grallocutils.so | 51120 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 519341fb41875d83e4b17e10d4d4a73fe22bf30b80a3bab727344c24a7156df6 |
| SYSTEM_FALLBACK_EGL | /system/lib/egl/libGLES_android.so | 105464 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b0818c1cbba3826b91091e78260ae67fab059e6dc80ff4827d7c734c76cc1d2b |
| SYSTEM_FALLBACK_EGL | /system/lib64/egl/libGLES_android.so | 133688 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | dd3d53e429eca357ce18ad0b7166278228745d3eac7793330d75dfcacbeefcac |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.allocator@2.0.so | 65840 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 86bf8e81b1833238cc6b57cd7a670f261f785b7410211b08f7ddc23ff7a3a408 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.allocator@3.0.so | 65848 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f8f98a8617dde9be70319dc53cf1cd795640b6a6650000f7cec25bdea35d62d8 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.1.so | 188052 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1ba66900d26e2444b7cb938c27fb53b3b0344f477abfd8e78958cc4f67568eaf |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.2.so | 156900 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9ffe9a663896f98508c1edd814b585969a02fe47b85fe6c855ab59bdaab6c204 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.3.so | 197192 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b0a452f0b94da77c337e62aea53a7eae99720db23727f97443969d1426945de8 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@2.0.so | 70576 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 23da339812b7773a73d83059fdd74af5de599cfdfda8a987ad500f709d462c3f |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@2.1.so | 75196 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c472879aee75a66f1e3e1e22473bcdcf32561caa0b0497a3ff89776762a51cbd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@3.0.so | 75352 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 4d66842e682fc94f3c204b1b60d05353cb2b6924ee1ee359c27668be1a833fd5 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libEGL.so | 152436 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c3a1dd7850e0cca1a7e80a5f309a2ff2771885bd370e3a85f996c0a25d3bb2bc |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv1_CM.so | 31900 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 70ecbb6a22c467ea999b53c7cbd0ae11a283c86af321cc05d56acf27917580ff |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv2.so | 77568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 65f3e4efa0ec1c64bf7c9fe68f2eab652c3bf9f080eb6510194f5556e715a5b5 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv3.so | 77568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 51e3f2a8ecb953dea4436adb5f86fcc452c68aa8633506eb921b01ae7fc0b302 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libSurfaceFlingerProp.so | 92040 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9ff7e0fb0f12bdbe96f118dc33bd37d884210296903982ac5071834f36ee4f55 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libgpud_sys.so | 63984 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 194471894bb06a63b850408959a7a03bd131e9fa4c9297974ba149b8b1f45b2e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libgralloc_extra_sys.so | 19640 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bf8fda1d0be5cc8d7826290438a7acd09fc48938f36060ff2d6ca91b1f9b94dd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libhwui.so | 6231444 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 631243c64cc0eb604a2bf3e6aff17921b85542465bcdd646eb08b8166de73a2e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libsurfaceflinger.so | 1150652 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ee128a9a775ccb3024cbff6edd472fe9b4b79d4a2757370276c258a94876dc45 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libvulkan.so | 130272 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d4988826d8770ada756823c3a081dc3afa9af5c135e626f5a6881d31a4b5982d |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.gpu@1.0.so | 123844 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | a900db381d82db9487769248b36bee58c09a58b63c35e2b25c3eba8f7ac26afa |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.0.so | 157580 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 63b03af701f7e61f02dc83ec5f76b36e2c439120f5f8060a35ad0f67302eff1f |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.1.so | 114612 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c848a731986be093c60a466122387fc572feea8ae5b0140e171fd74a9ae11694 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.2.so | 115316 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9b342cd2ffbab9fef1e7d148738fe1325a16b878e6ea6b42e88a9dbb82ae5aca |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.3.so | 145936 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 976438d66a249ff467b5db8cd27184167ac665d442e359a51fe98852ac054481 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.allocator@2.0.so | 84328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 147fae699ac02c3e56d7923d4f152abe321db17ef7bd81e406afc0bd2dce8230 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.allocator@3.0.so | 84368 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b2832794a427bfa757ff196c569ca611e6952d49944b3d3a4e453381dbb46b09 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.1.so | 255424 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3b898553596f3869938c1799dea0de950f29ec00ddbad07edab8de0e11759596 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.2.so | 211472 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7a5a8522418eaf7f8ec89045637cc04aa612acdcf4a415ac7f0a16ba70b04d3a |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.3.so | 265232 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f635c12bb8be56b50a3beefaea96a48675880346aa49c2d3eb0318874f526d99 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@2.0.so | 93184 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 674672272ad7a5aebc6b8c7f78e8306488bfc076ec3f5c0503cd7406d624346e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@2.1.so | 98208 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | da43e00d2c1c8c71b421025b59687ef1c9a120e3cb41a1844acc04ddcd5b427e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@3.0.so | 102192 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ce23e15e7720d3f01ebbe710cd1006faf096282e2f45d7e83283b24459d3fc7c |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libEGL.so | 216920 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f0db2ae3f41a10b6029c12b35764124bb9fc0055aa27305cde0ddee65a087786 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv1_CM.so | 39744 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 8865b82184ed9cdb685efa2e238d55eef02612361844261ee6ec5cbaf025fd86 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv2.so | 109200 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d219bc0be29643555292a89398bce438a563e01c8b9d3dd62754b6d16c9ce1fd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv3.so | 109200 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 95cd55ef165bbeafed285a86dcffd645f12a49e49b0405caf0e60173ea509e25 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libSurfaceFlingerProp.so | 109280 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | eda0446c93f1b2dead3d81cb6b297dfe0a3a1b7f0ca375a60e41dc6d5cd446d2 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libgpud_sys.so | 69792 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ef65275e90dc31a5f87c763348c8670b7f9713261a0070b205a4733555ec0289 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libgralloc_extra_sys.so | 24008 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 357b0ff18fd0a89950469560e5c04859f07d54e56a87b3a9d9cb8f4cc17087be |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libhwui.so | 8309472 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1f5e809f7cb4c7c50841d20986690eb8a82ccc825d62025be8704e56e1310e66 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libsurfaceflinger.so | 1636960 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 14e709039f4c1dd63116ad13df0d9d482ddf9bc66bda827d32d3f21aae9b5c2b |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libvulkan.so | 176904 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 960229d0d1e276cf5e32b173068289c6c23b042eaf31f3a24e50e0f127e4b635 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.gpu@1.0.so | 164488 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | afbc19635e613d247977a65756e4c8c089c0a8a1ef1babb31bd4e1e58d298e12 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.0.so | 214008 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ec3182c357bef60731884c9272c25e4cf1a5546f8044f57f8090a3bccdbcc369 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.1.so | 146888 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 502223c60dd2da535fd77a647648d1157e55a71a8e227c351f700d9df9ae8483 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.2.so | 156328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 6b4312e0f72882d4c8a713bde7c42d81f2a62543b93003f4d5094780308690c3 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.3.so | 191936 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7e6d9d80abc7e416c7f414127fb4372026108f4ffddce079b62801bcc19386fe |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/egl.cfg | 76 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 1606ef2f0ad0dfd812c8ec4ab7b7d9ae5bff3554191c7012530ca883612c70e1 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libEGL_mtk.so | 17604 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 771e33b95776024b15e897aacc8bea58e0db99392dc6eee622a1adc4549ae473 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLES_meow.so | 1252340 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 0152c3186e298907d4852515a1bd4f1076351ef493577df246660e582675bc4f |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLESv1_CM_mtk.so | 554392 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | ed4128824cdbb5b1be0a717fbefac831cb49184bf71129cdf823a6d12d48ad17 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLESv2_mtk.so | 1246648 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 924255a78a51b85160876ed625991324dee1b772d67bcd62129ace12ca9b5317 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libMEOW_trace.so | 370644 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 01448f3479ae13bdc2d63a8dfec589bc04e907a94841546f06234025630d24ce |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libEGL_mtk.so | 14000 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c1e17bb6f92d90170573a84724483e0ffe574503aeba3716591964eeb6680276 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLES_meow.so | 1999944 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 32fa3f4d9044a72251c2e310c000b038867f1dabd091ac7da67e9f206b7803e8 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLESv1_CM_mtk.so | 706528 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 745912f6219393130a38a528a82473cdf76b703d60c3e21f1e39bdc65d01bb28 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLESv2_mtk.so | 1591296 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d4714f8a2e3f519f51b8cc0e796be19ce7d5c5d7c8dd06f62ed8f98c7a859d22 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libMEOW_trace.so | 535640 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a1e0566272e1153d0a9a9e4b07c7aadde9277b7c1ba4f2b7510c01d9198b0843 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libIMGegl.so | 284056 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 1a1c0f144ea9d312ac1549231aeb3fb0ec5dffe594cec21961534db78cf8f3a1 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libOpenCL.so | 65456 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 557462a70eeff80729fd69a2e981b3491d367618c39286cf2223c42490a18dce |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libPVROCL.so | 25265440 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d323ef2ab3a8c9f6599113d2608db2e273ec4d6d2abe00ccc681dbf9cbfe1c85 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libPVRScopeServices.so | 112016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 5f352b257e99d724857d9d19992cb509a23a1335d4ebf331917c4fae312e806b |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libdpframework.so | 788880 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c9633ac03ea683e81b9fd171ed5188a902e97fdba17529955cfb47120a8b5c17 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgpu_aux.so | 78068 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a6687f6601986757b788962456629f9293010abd1eac70a974e9eee5a9dea8a9 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgpud.so | 63976 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b40686aeffdd5b5977c67e8ed8214462d025e5d9135fcbc71dbca11b80924011 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgralloc_extra.so | 19640 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 52017263d40d2d480a6378cb0ce4120216c47a2816c1de9e1c92df1f156ccd02 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libion_mtk.so | 15156 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a120f5023a98dd12a60651616bffad75cc9903959da4799a03d705e23756482b |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libion_ulit.so | 14996 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4d9d08edf2b58fd63bb16fe2ce40dff5a815d1dc1c3b8b68e30687df7dafd519 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libmpvr.so | 13556 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b115de41227fa51b720c908b8879d0d02aecebdce72d6e95318f167086bdf1e9 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libpq_cust_base.so | 114252 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4cdee8e3cc6328a3564ab9ad1179a2e558c166c88d93609c29f5f667b297f6fe |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libpq_prot.so | 174256 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | e380ac2dd91a873ea8ba5810e0404e5b7f18c84cadbd1814c6e0efd4f901376c |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.gpu@1.0.so | 123848 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2e67edc2ce12ff478296b60b72f9e96f16cebd31e8b7a72d4d38191eeed7ccfd |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.0.so | 157588 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 30878f6c763ccb2ab472720ed5debc3c6215b0cdd985f572d10aca77d25ec8e0 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.1.so | 114604 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 72a34b575f5c00d22066f959f9b49204f3964e1ece3d89adf846ffa60af52e78 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.2.so | 115316 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 0fcfb84c24e9d9ff4ce5536b5c100dcb93576a5b4764e25b2c89415c6b7fbbfe |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.3.so | 145936 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4ba90407ee2bd4555086683668919fca3ee91f0ec00f9e0b18930aa54caac078 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libIMGegl.so | 337888 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 929c7a9b124a7e91b8af29aac7ac4aba2ff814ccf7c26959703402923e569071 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libOpenCL.so | 85456 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 3feb689544917c67e5a3ecef82639386dd25c9139e618af2bcb705c6aa2dc553 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libPVROCL.so | 34741696 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2d10c57e0052c12bd30b9f8f6a76a4291e17e23cb660274b7c74e01f8d349809 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libPVRScopeServices.so | 133088 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 33d1c4b43b7db9caabd57cdafaeb88d5fe5f9111a6778c8db0c9c0e577940a72 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libdpframework.so | 1041672 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a3a3d74969ec8d2abb8341a501406f5cbdb1b9057102be16cc58bdf81fba2033 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgpu_aux.so | 89320 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 36e466a01c259e2f3c9dde6ef2a8abe2c6a58f83553333400e5fb8aadbc0e0dd |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgpud.so | 69776 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 63f8e4acc38567e6e65b3752170ec3a416b8863154b05a8a965808f242fdf040 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgralloc_extra.so | 24016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | e1605c7911757612d1eba1f2a0a791b0007994c5712f18934e3e033a24eaf53d |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libion_mtk.so | 15160 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 967fa4db28e4e600f2f5d50ac364d20b994f8e9ba6306c8d629c41bd8f4c72ad |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libion_ulit.so | 15080 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 3b30b53ff2497976912a4c918c827fd8a7d52ea740c1fa39ccf089b2b1b5dc48 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libmpvr.so | 5880 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 498cf3a7d57eceb4efdde278017044e223905f9c10cdfd5019a292946312992d |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libpq_cust_base.so | 105280 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 78a13871437e01bdfe62d22754868b28934b0b7744119931a07855e0d11ebc57 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libpq_prot.so | 212384 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | f809db6cdafa29d34946f2807a87610636f090298a8fcf18b9590c27b07773dc |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.gpu@1.0.so | 164488 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 03485b9903f5b6c2dc9a2290aadc3ee47b9d4f011f6ea6a3d82607aa73836048 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.0.so | 214016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d612cabb77d4f8892278d3a145ed00b848f750576a63346fea4f11a3fa1d12be |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.1.so | 146888 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2cc4e78e0a64d65a4646c03186efad365e77a4d14537d47d9ced5e7658a56c30 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.2.so | 156336 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 33f515cd41716ad8a8475f5b3595921c4bf3b6da7a304a0ccaa0f019d808e527 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.3.so | 191936 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c758a979c2b224d961eb4a8f2444576947c5c3ef91346860a5b3d46474db14b1 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.allocator@2.0-impl.so | 24580 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | e08fd77637989d8b2df4c602a204cced8861832ac4832419f3e89cc07c6e2e7b |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.composer@2.1-impl.so | 79400 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 8f4221d1c37d21d755629564e6b1ffedd5d4fa93b5c2836a583c8da89de8c999 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 29980 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 79dba7c0136c21a627cfd0c92fe9e247b0694c791d62e47ee7124fdb00b87b9c |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/gralloc.default.so | 28788 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | bb60cb89dcff1b310035433b4712f5002cbb267c08ffacfc92b09574942bb8e3 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/gralloc.mt6765.so | 552832 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b7a3ec5bc130e5c6d9fe3c24f0f9d85c61981da0c306c2c167521580ba8e90e7 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/vendor.mediatek.hardware.pq@2.3-impl.so | 131772 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 6b028a1e5a9cf765577a44e1b3ee983b026ab25f20f04596c4265e9284b7be3f |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/vulkan.mt6765.so | 543016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d174bf26d577768fb1e34f2853192a4856a1752f20e623ca0a80add4bcafafd1 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.allocator@2.0-impl.so | 24904 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 8a8f18a08c0bebc96c1f936ca971dd16b06f02c0db7b4fb5bd3c8a5f4960aa65 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.composer@2.1-impl.so | 104224 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | a3ace507225480020f0ae9e36be72f22a83714575b844f666841418fe3b0c98e |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 38696 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2d6c882b22326ed01a578f1d8545a73838c903ffb568b986665f7b30f47ed9c0 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/gralloc.default.so | 23824 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d6bfcd749ea6f386e981fcc9b3c35c9bc89d2cde2da43b6aef73a896cae568aa |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/gralloc.mt6765.so | 616632 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 89ec41a1a401551d88422d8c22df1ea523b2153e00fd5fc1c8a2ff1cacc60570 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/hwcomposer.mt6765.so | 682224 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 4887675a3d5f4b85496c4e677f402be62806302a928927b95f54e412c4cd7694 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/vendor.mediatek.hardware.pq@2.3-impl.so | 177208 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | fecfc33bf5e43a75bba53c08a2f4ca65f49081abfb427dc05710a05bb2ea0190 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/vulkan.mt6765.so | 683024 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a0aec798ba07930dae09b6092b32e05e05af8326e6edd7e8683e50caf73acbb5 |
################################################################################
# 05.19 — TARGETED SEARCH DEMOTION REGISTER
################################################################################
The targeted manifest intentionally used broad patterns such as `ion`, `img`, and
`composer` to avoid missing graphics dependencies. That broad search also catches
non-Section-05-dominant files such as camera imgbuf libraries, GNSS measurement
correction interfaces, audio compensation filters, clang runtime libraries, and
RIL/fusion strings. These are not silently dropped; they are demoted here so the
database remains exact.

| path | size | label | sha256 |
|---|---|---|---|
| /system/bin/ionice | 6 | u:object_r:system_file:s0 | 44377bc01a96443b9cacfb9a995fd2904b359f4d8f92ae7e2e10c82e196f5c8d |
| /system/lib/android.hardware.gnss.measurement_corrections@1.0.so | 93124 | u:object_r:system_lib_file:s0 | b045cf88c9bfb468c1fa7c85b2a891eb6677042fb4ae5c1707b553620158c7f6 |
| /system/lib/android.hidl.safe_union@1.0.so | 14916 | u:object_r:system_lib_file:s0 | 7a046fe5a21c42ed397feeb04309a6eec25439f3380cbd01595a617cb860963c |
| /system/lib/libaudiocompensationfilter.so | 19240 | u:object_r:system_lib_file:s0 | a6594909d22f1dd6ee8d321670b0e7688e143425057866dddedb585794c1f3ea |
| /system/lib/libclang_rt.asan-arm-android.so | 2049748 | u:object_r:system_lib_file:s0 | 12344ed00194d3e1fd1a6c566ad1f7852065cd706db1a12c6970576f8aa2175e |
| /system/lib/libclcore.bc | 297820 | u:object_r:system_lib_file:s0 | 22f36ddab71cf26313a29be083bf0857b68f64b94cbefeb8a5083e6be80fc2a2 |
| /system/lib/libclcore_debug.bc | 323452 | u:object_r:system_lib_file:s0 | 7ce7ecaee8d8e0344b0bf3c42daa86f9013f4449d9cca5374fb97d383509d695 |
| /system/lib/libclcore_debug_g.bc | 941408 | u:object_r:system_lib_file:s0 | fa997728ed71d4ed5b8f7794c36ba95b25cd2453458970d8a515491193de200d |
| /system/lib/libclcore_g.bc | 1067372 | u:object_r:system_lib_file:s0 | 545efcc346acb476ecedb1ade95908b630a50c3462fff880cd24e56fb45d2e50 |
| /system/lib/libclcore_neon.bc | 301196 | u:object_r:system_lib_file:s0 | 1c576b222fb9a16c9d0d94f2353ae6b75afa9ef153e05b70c3607b66562bda5c |
| /system/lib/libhidlallocatorutils.so | 15024 | u:object_r:system_lib_file:s0 | 976c3f96964528920421d4c8fabfeb7e6ae18c0be3e3626692552a73d8ea7895 |
| /system/lib/libimg_utils.so | 63400 | u:object_r:system_lib_file:s0 | 383c74ef351fbadccb4910cc83f97ecacc193ad26be525cddeb0a01f0ac1f8f5 |
| /system/lib/libpostalgo_imgbuf.so | 117472 | u:object_r:system_lib_file:s0 | bd228a41d3a19c2ce90300c18ad36845aa8305b3988bbff4fe219564938fd40b |
| /system/lib/libstagefright_foundation.so | 131908 | u:object_r:system_lib_file:s0 | a7df898d44743eb71e1dbe505e3ec7248f10e32b1ecaa54d86d7ac9daff67224 |
| /system/lib64/android.hardware.confirmationui@1.0.so | 125448 | u:object_r:system_lib_file:s0 | cd98d93a4a89bb3a0efcc9684baaf0d004543e9ebd00c5a5c03fe00304084287 |
| /system/lib64/android.hardware.gnss.measurement_corrections@1.0.so | 125352 | u:object_r:system_lib_file:s0 | fc4b6f58923212edf1c540101390e1591206fe475919a39614b8ea0596cd29c5 |
| /system/lib64/android.hidl.safe_union@1.0.so | 14984 | u:object_r:system_lib_file:s0 | 1653f5c07f57fcb61b34695fa24d4311a03de928b363855bdb7a612593de3500 |
| /system/lib64/libaudiocompensationfilter.so | 23608 | u:object_r:system_lib_file:s0 | 752d0078329ddfa9c47b19e628cee81e3b3c90f1b888dbf535a343331cdcd5e0 |
| /system/lib64/libaudiopolicycustomextensions.so | 448264 | u:object_r:system_lib_file:s0 | 6e2b2d9b5cec0e6295a86a86691c4b32760f62bce8fbf75632649d31e2525386 |
| /system/lib64/libclang_rt.asan-aarch64-android.so | 2262928 | u:object_r:system_lib_file:s0 | 2ed663691556b606d97aab184d21b1d40f537d30de64f098976a9f7f644b15b9 |
| /system/lib64/libclang_rt.hwasan-aarch64-android.so | 776496 | u:object_r:system_lib_file:s0 | 8e4b2db02603c76ba38ddd108e608655667973e84a8127d63b5825aa97a10823 |
| /system/lib64/libclang_rt.ubsan_standalone-aarch64-android.so | 1048024 | u:object_r:system_lib_file:s0 | b0423a9bf163c97f049fd3b5abfa8deef700f230e7a377771a24ca1ae65bef2c |
| /system/lib64/libclcore.bc | 330432 | u:object_r:system_lib_file:s0 | cf5e66e49c3ec8e0535da0676e1fe841d3624171451fe06362f446d1a52200b5 |
| /system/lib64/libclcore_debug.bc | 353076 | u:object_r:system_lib_file:s0 | 29a78789cb2a859aede75390c4b4c23eb3ee86ca3c00d252f1cb7cda3c708d7e |
| /system/lib64/libclcore_debug_g.bc | 1077160 | u:object_r:system_lib_file:s0 | cef914434e995ad93411486c2eb9c1f2904a7d587559062c1ad5ec6e5f39a8c6 |
| /system/lib64/libclcore_g.bc | 1124700 | u:object_r:system_lib_file:s0 | 3c6a81d55a685e04e5fe9403f7b2be148266bc01b3a824c4a20da6702702cb9a |
| /system/lib64/libhidlallocatorutils.so | 15144 | u:object_r:system_lib_file:s0 | 322c9517812e05666b75c3902c4e7736636d572e6d89d465dc6a4cad5a992c1c |
| /system/lib64/libimg_utils.so | 122736 | u:object_r:system_lib_file:s0 | a6b81991e3d6d0c8be4a3b58bcfae540cf8da4a1c23072d380a5ed1ed62c63cc |
| /system/lib64/libpostalgo_imgbuf.so | 171640 | u:object_r:system_lib_file:s0 | 3faf972a6f9acaaa6c31779c20c7df407577ae2255b1bfff00f570a432b9ec3d |
| /system/lib64/libstagefright_foundation.so | 211552 | u:object_r:system_lib_file:s0 | 21bbf68a7ce6baa641430794ae77f65cec5f9468bef5d0c749984305896f70b1 |
| /vendor/bin/hw/mtkfusionrild | 20168 | u:object_r:rild_exec:s0 | de07d5b07985746f475e02e048d5423b0138f4b44e5a27bf0b89521a9204df04 |
| /vendor/lib/libaudiocompensationfilter_vendor.so | 32556 | u:object_r:vendor_file:s0 | b3c85b534b2a609a45d9347c593fb325671150bf3e16231ccc5e42dcbfe2ca17 |
| /vendor/lib/libaudiocompensationfilterc.so | 36652 | u:object_r:vendor_file:s0 | 6f0693f475e39090b0914d7e3551703732b2888f7cb541f1b0f9ff5ab7e38ec8 |
| /vendor/lib/libbluetooth_audio_session_mediatek.so | 71276 | u:object_r:vendor_file:s0 | 7dd67cd5585c6742906302ee6ba7ac8fe076202056fa7af22d7f7ee4b5e6c166 |
| /vendor/lib64/libaudiocompensationfilter_vendor.so | 27776 | u:object_r:vendor_file:s0 | ab0e692b3d42f41ffc97617a78136e7fb64b9621f0d38d718f55d282e12fe439 |
| /vendor/lib64/libaudiocompensationfilterc.so | 31928 | u:object_r:vendor_file:s0 | f7978df4d482a681da7afaeb305d32b6140ebc378aace498bebd6f3acdd3f287 |
| /vendor/lib64/libbluetooth_audio_session_mediatek.so | 83760 | u:object_r:vendor_file:s0 | 9d45ffa34e85faa425c756c3067c24a60ad09a18b1b3ce031836ab5d9c9e5ded |
| /vendor/lib64/libmtkcam_calibration_convertor.so | 15464 | u:object_r:vendor_file:s0 | f075e8fd75b8489d6cb4f3a6afad85bb9766fbabb0f1b96594059c55a78c558d |
| /vendor/lib64/libmtkcam_calibration_provider.so | 168896 | u:object_r:vendor_file:s0 | 0fb1339d740a40379b9ac30973d140c742ce51841bb1fe8ac3b5cdc893b25232 |
| /vendor/lib64/libmtkcam_grallocutils.so | 59488 | u:object_r:vendor_file:s0 | 23c7f7dc074b4ca5eefe45558b33a1af819903b9382dd5d3b2e6878bc362043a |
| /vendor/lib64/libmtkcam_imgbuf.so | 394368 | u:object_r:vendor_file:s0 | 2321ccf8b920c54489ac595e74297f8e1bdb53028cea498b619b0bce8c27507c |
| /vendor/lib64/libmtkcam_pipelinemodel_session.so | 415040 | u:object_r:vendor_file:s0 | 7bc3464174329a1d2543f44a2fdb4462b3e998442ee35c43b48502c991e1ac17 |
| /vendor/lib64/librilfusion.so | 845416 | u:object_r:vendor_file:s0 | 51a7ab29b2475531abc610a763050ce79a26ab6cf2a6b433877fb24177dd935f |
################################################################################
# 05.20 — COMPLETE RAW TARGETED BLOB MANIFEST
################################################################################
Raw manifest class legend:
- ACTIVE_FRAMEWORK_SERVICE: running framework graphics service binary.
- ACTIVE_VENDOR_HAL_SERVICE: running vendor graphics/PQ/GPU HAL service binary.
- VENDOR_GRAPHICS_HAL_IMPL: vendor HAL implementation selected by graphics stack.
- VENDOR_EGL_GLES_MEOW: vendor EGL/GLES/MEOW implementation.
- VENDOR_GPU_PQ_MEMORY_STACK: vendor PVR/IMG/OpenCL/PQ/ION/gralloc/GPU support stack.
- SYSTEM_GRAPHICS_FRAMEWORK: AOSP/system-side graphics framework and interface libraries.
- SYSTEM_FALLBACK_EGL: Android fallback GLES implementation.
- DISPLAY_BOOTANIMATION_ADJACENT: visual boot path, not GPU silicon proof.
- PQ_DISPLAY_ADJACENT: picture-quality/display-adjacent library.
- SHARED_OR_SECTION05_REVIEWED: shared graphics/memory/display dependency kept under review.
- SEARCH_HIT_NON_SECTION05_DOMINANT: broad regex hit demoted from Section 05 authority.

| class | path | size | mode | uid | gid | label | sha256 |
|---|---|---|---|---|---|---|---|
| ACTIVE_FRAMEWORK_SERVICE | /system/bin/gpuservice | 267520 | 755 | 0 | 2000 | u:object_r:gpuservice_exec:s0 | 22ead82b0cd6a5a0bf310b32fa36dbf4a4ecd30e4d528f0c68fc9c23e97a239d |
| ACTIVE_FRAMEWORK_SERVICE | /system/bin/surfaceflinger | 32976 | 755 | 1000 | 1003 | u:object_r:surfaceflinger_exec:s0 | 4a049e731828b901123205aa7f61b74ddafbba6c411b725c8de497b6ca5f1e00 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/android.hardware.graphics.allocator@2.0-service | 11584 | 755 | 0 | 2000 | u:object_r:hal_graphics_allocator_default_exec:s0 | de14c988341b1bd421ad97191b9d45ec176bcd468feb42704e72f9b97fae774c |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/android.hardware.graphics.composer@2.1-service | 11672 | 755 | 0 | 2000 | u:object_r:hal_graphics_composer_default_exec:s0 | 9a718498f8f8effc1bbcca7c1c9b92a2b6d0ea22b4eb7529c8b54e33fab28b89 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/vendor.mediatek.hardware.gpu@1.0-service | 29976 | 755 | 0 | 2000 | u:object_r:mtk_hal_gpu_exec:s0 | 6dd788d13979d080066ea915342579b187f2df4e637e7c8d3d7509aea4b4afe8 |
| ACTIVE_VENDOR_HAL_SERVICE | /vendor/bin/hw/vendor.mediatek.hardware.pq@2.2-service | 37448 | 755 | 0 | 2000 | u:object_r:mtk_hal_pq_exec:s0 | 859928c744e3eec38623b2024a960bef3faa27a9ab7842d9b2a12c82c053c0b1 |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/bin/mtkbootanimation | 25248 | 755 | 0 | 2000 | u:object_r:mtkbootanimation_exec:s0 | 02ae43b4af1517b1013559d91a8d08080add91ead560823280927da03c4a4102 |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/lib/libmtkbootanimation.so | 76208 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3427ded2fbccbb22517bf07b7f31a567a4df4ca15c442ce47a6f15d50d8c4fac |
| DISPLAY_BOOTANIMATION_ADJACENT | /system/lib64/libmtkbootanimation.so | 93448 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | aca72f970131253a5dc2287ab6461ce07a81354f8d7575f83809e7379594deab |
| PQ_DISPLAY_ADJACENT | /system/lib/libPQDCjni.so | 24804 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f6d7b504f14365caf4532e0760e2078585f3f054e9461c349d1774790e2af408 |
| PQ_DISPLAY_ADJACENT | /system/lib/libPQjni.so | 20056 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 057859cdd836593c3bf11cbf79097daaa8403440bb77d0f4ff2f3b84664b4af1 |
| PQ_DISPLAY_ADJACENT | /system/lib/libjni_pq.so | 46940 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 5b1aa30b505f4ba5645707c19e8ba3babb5930b5efca31528e170c2b0e688a75 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libPQDCjni.so | 33072 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 0e18d8c9bd3219812ae96a4d606dd2bdc251aa12e85cd0e2807189a36d7a28c9 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libPQjni.so | 20328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bf2285cd757ea725636e9b6e7d79adcc496f4d5b6d3d6b8d2d7cbba9086a0dd4 |
| PQ_DISPLAY_ADJACENT | /system/lib64/libjni_pq.so | 67568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 65bb2ccd7be020b41831dec8b123a0a3d26ed5bbd3bbf89b47295544e6642832 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/bin/ionice | 6 | 755 | 0 | 2000 | u:object_r:system_file:s0 | 44377bc01a96443b9cacfb9a995fd2904b359f4d8f92ae7e2e10c82e196f5c8d |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/android.hardware.gnss.measurement_corrections@1.0.so | 93124 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b045cf88c9bfb468c1fa7c85b2a891eb6677042fb4ae5c1707b553620158c7f6 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/android.hidl.safe_union@1.0.so | 14916 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7a046fe5a21c42ed397feeb04309a6eec25439f3380cbd01595a617cb860963c |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libaudiocompensationfilter.so | 19240 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | a6594909d22f1dd6ee8d321670b0e7688e143425057866dddedb585794c1f3ea |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclang_rt.asan-arm-android.so | 2049748 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 12344ed00194d3e1fd1a6c566ad1f7852065cd706db1a12c6970576f8aa2175e |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclcore.bc | 297820 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 22f36ddab71cf26313a29be083bf0857b68f64b94cbefeb8a5083e6be80fc2a2 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclcore_debug.bc | 323452 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7ce7ecaee8d8e0344b0bf3c42daa86f9013f4449d9cca5374fb97d383509d695 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclcore_debug_g.bc | 941408 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | fa997728ed71d4ed5b8f7794c36ba95b25cd2453458970d8a515491193de200d |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclcore_g.bc | 1067372 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 545efcc346acb476ecedb1ade95908b630a50c3462fff880cd24e56fb45d2e50 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libclcore_neon.bc | 301196 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1c576b222fb9a16c9d0d94f2353ae6b75afa9ef153e05b70c3607b66562bda5c |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libhidlallocatorutils.so | 15024 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 976c3f96964528920421d4c8fabfeb7e6ae18c0be3e3626692552a73d8ea7895 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libimg_utils.so | 63400 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 383c74ef351fbadccb4910cc83f97ecacc193ad26be525cddeb0a01f0ac1f8f5 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libpostalgo_imgbuf.so | 117472 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bd228a41d3a19c2ce90300c18ad36845aa8305b3988bbff4fe219564938fd40b |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib/libstagefright_foundation.so | 131908 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | a7df898d44743eb71e1dbe505e3ec7248f10e32b1ecaa54d86d7ac9daff67224 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/android.hardware.confirmationui@1.0.so | 125448 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | cd98d93a4a89bb3a0efcc9684baaf0d004543e9ebd00c5a5c03fe00304084287 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/android.hardware.gnss.measurement_corrections@1.0.so | 125352 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | fc4b6f58923212edf1c540101390e1591206fe475919a39614b8ea0596cd29c5 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/android.hidl.safe_union@1.0.so | 14984 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1653f5c07f57fcb61b34695fa24d4311a03de928b363855bdb7a612593de3500 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libaudiocompensationfilter.so | 23608 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 752d0078329ddfa9c47b19e628cee81e3b3c90f1b888dbf535a343331cdcd5e0 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libaudiopolicycustomextensions.so | 448264 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 6e2b2d9b5cec0e6295a86a86691c4b32760f62bce8fbf75632649d31e2525386 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclang_rt.asan-aarch64-android.so | 2262928 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 2ed663691556b606d97aab184d21b1d40f537d30de64f098976a9f7f644b15b9 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclang_rt.hwasan-aarch64-android.so | 776496 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 8e4b2db02603c76ba38ddd108e608655667973e84a8127d63b5825aa97a10823 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclang_rt.ubsan_standalone-aarch64-android.so | 1048024 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b0423a9bf163c97f049fd3b5abfa8deef700f230e7a377771a24ca1ae65bef2c |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclcore.bc | 330432 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | cf5e66e49c3ec8e0535da0676e1fe841d3624171451fe06362f446d1a52200b5 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclcore_debug.bc | 353076 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 29a78789cb2a859aede75390c4b4c23eb3ee86ca3c00d252f1cb7cda3c708d7e |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclcore_debug_g.bc | 1077160 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | cef914434e995ad93411486c2eb9c1f2904a7d587559062c1ad5ec6e5f39a8c6 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libclcore_g.bc | 1124700 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3c6a81d55a685e04e5fe9403f7b2be148266bc01b3a824c4a20da6702702cb9a |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libhidlallocatorutils.so | 15144 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 322c9517812e05666b75c3902c4e7736636d572e6d89d465dc6a4cad5a992c1c |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libimg_utils.so | 122736 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | a6b81991e3d6d0c8be4a3b58bcfae540cf8da4a1c23072d380a5ed1ed62c63cc |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libpostalgo_imgbuf.so | 171640 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3faf972a6f9acaaa6c31779c20c7df407577ae2255b1bfff00f570a432b9ec3d |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /system/lib64/libstagefright_foundation.so | 211552 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 21bbf68a7ce6baa641430794ae77f65cec5f9468bef5d0c749984305896f70b1 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/bin/hw/mtkfusionrild | 20168 | 755 | 0 | 2000 | u:object_r:rild_exec:s0 | de07d5b07985746f475e02e048d5423b0138f4b44e5a27bf0b89521a9204df04 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib/libaudiocompensationfilter_vendor.so | 32556 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | b3c85b534b2a609a45d9347c593fb325671150bf3e16231ccc5e42dcbfe2ca17 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib/libaudiocompensationfilterc.so | 36652 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 6f0693f475e39090b0914d7e3551703732b2888f7cb541f1b0f9ff5ab7e38ec8 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib/libbluetooth_audio_session_mediatek.so | 71276 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 7dd67cd5585c6742906302ee6ba7ac8fe076202056fa7af22d7f7ee4b5e6c166 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libaudiocompensationfilter_vendor.so | 27776 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | ab0e692b3d42f41ffc97617a78136e7fb64b9621f0d38d718f55d282e12fe439 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libaudiocompensationfilterc.so | 31928 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | f7978df4d482a681da7afaeb305d32b6140ebc378aace498bebd6f3acdd3f287 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libbluetooth_audio_session_mediatek.so | 83760 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 9d45ffa34e85faa425c756c3067c24a60ad09a18b1b3ce031836ab5d9c9e5ded |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libmtkcam_calibration_convertor.so | 15464 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | f075e8fd75b8489d6cb4f3a6afad85bb9766fbabb0f1b96594059c55a78c558d |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libmtkcam_calibration_provider.so | 168896 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 0fb1339d740a40379b9ac30973d140c742ce51841bb1fe8ac3b5cdc893b25232 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libmtkcam_grallocutils.so | 59488 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 23c7f7dc074b4ca5eefe45558b33a1af819903b9382dd5d3b2e6878bc362043a |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libmtkcam_imgbuf.so | 394368 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 2321ccf8b920c54489ac595e74297f8e1bdb53028cea498b619b0bce8c27507c |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/libmtkcam_pipelinemodel_session.so | 415040 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 7bc3464174329a1d2543f44a2fdb4462b3e998442ee35c43b48502c991e1ac17 |
| SEARCH_HIT_NON_SECTION05_DOMINANT | /vendor/lib64/librilfusion.so | 845416 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 51a7ab29b2475531abc610a763050ce79a26ab6cf2a6b433877fb24177dd935f |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/android.frameworks.vr.composer@1.0.so | 93080 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d3e52a1cf0506aa1256241772171a9b40d39aad9af772e62070a3ce787208fd5 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/android.hidl.allocator@1.0.so | 61728 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 95971c8824cb871c2c19494b126837a893fae8f530c6e378190c1c172266fa0e |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libion.so | 15168 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | cb8d1c51c87b95bf7e0cf9d887c1bd56194314377db8a819eb0fcc478dfbff6f |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libmediatek_exceptionlog.so | 15264 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 8c95fa1d5f3703100c3f5578286000c74bda2c145e8e45889aa60456079f03eb |
| SHARED_OR_SECTION05_REVIEWED | /system/lib/libpostalgo_grallocutils.so | 42704 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 05ab85fd9f362fa4cf75bd5d387349ec6e28c1b82f3ffc77220fd48bf6db009f |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/android.frameworks.vr.composer@1.0.so | 120704 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d9a357840a6c84863a15dc4b837b5b84fa811f994632c1c1f49f87958a022a00 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/android.hidl.allocator@1.0.so | 80176 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bcf6d57d2344d1a4828cf7598e6416b716083912cfc1f0986dfb94c7ba236f42 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libion.so | 15184 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 4317a7f5bb843674751ae7817010cd23b8fd14af2c437841b85bd4c083a749f1 |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libmediatek_exceptionlog.so | 15400 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 10d30761da0b3816339ddce3fd0754aa20dce76964dea970a8d8e1e5dca132ac |
| SHARED_OR_SECTION05_REVIEWED | /system/lib64/libpostalgo_grallocutils.so | 51120 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 519341fb41875d83e4b17e10d4d4a73fe22bf30b80a3bab727344c24a7156df6 |
| SYSTEM_FALLBACK_EGL | /system/lib/egl/libGLES_android.so | 105464 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b0818c1cbba3826b91091e78260ae67fab059e6dc80ff4827d7c734c76cc1d2b |
| SYSTEM_FALLBACK_EGL | /system/lib64/egl/libGLES_android.so | 133688 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | dd3d53e429eca357ce18ad0b7166278228745d3eac7793330d75dfcacbeefcac |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.allocator@2.0.so | 65840 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 86bf8e81b1833238cc6b57cd7a670f261f785b7410211b08f7ddc23ff7a3a408 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.allocator@3.0.so | 65848 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f8f98a8617dde9be70319dc53cf1cd795640b6a6650000f7cec25bdea35d62d8 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.1.so | 188052 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1ba66900d26e2444b7cb938c27fb53b3b0344f477abfd8e78958cc4f67568eaf |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.2.so | 156900 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9ffe9a663896f98508c1edd814b585969a02fe47b85fe6c855ab59bdaab6c204 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.composer@2.3.so | 197192 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b0a452f0b94da77c337e62aea53a7eae99720db23727f97443969d1426945de8 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@2.0.so | 70576 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 23da339812b7773a73d83059fdd74af5de599cfdfda8a987ad500f709d462c3f |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@2.1.so | 75196 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c472879aee75a66f1e3e1e22473bcdcf32561caa0b0497a3ff89776762a51cbd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/android.hardware.graphics.mapper@3.0.so | 75352 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 4d66842e682fc94f3c204b1b60d05353cb2b6924ee1ee359c27668be1a833fd5 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libEGL.so | 152436 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c3a1dd7850e0cca1a7e80a5f309a2ff2771885bd370e3a85f996c0a25d3bb2bc |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv1_CM.so | 31900 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 70ecbb6a22c467ea999b53c7cbd0ae11a283c86af321cc05d56acf27917580ff |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv2.so | 77568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 65f3e4efa0ec1c64bf7c9fe68f2eab652c3bf9f080eb6510194f5556e715a5b5 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libGLESv3.so | 77568 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 51e3f2a8ecb953dea4436adb5f86fcc452c68aa8633506eb921b01ae7fc0b302 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libSurfaceFlingerProp.so | 92040 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9ff7e0fb0f12bdbe96f118dc33bd37d884210296903982ac5071834f36ee4f55 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libgpud_sys.so | 63984 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 194471894bb06a63b850408959a7a03bd131e9fa4c9297974ba149b8b1f45b2e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libgralloc_extra_sys.so | 19640 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | bf8fda1d0be5cc8d7826290438a7acd09fc48938f36060ff2d6ca91b1f9b94dd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libhwui.so | 6231444 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 631243c64cc0eb604a2bf3e6aff17921b85542465bcdd646eb08b8166de73a2e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libsurfaceflinger.so | 1150652 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ee128a9a775ccb3024cbff6edd472fe9b4b79d4a2757370276c258a94876dc45 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/libvulkan.so | 130272 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d4988826d8770ada756823c3a081dc3afa9af5c135e626f5a6881d31a4b5982d |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.gpu@1.0.so | 123844 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | a900db381d82db9487769248b36bee58c09a58b63c35e2b25c3eba8f7ac26afa |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.0.so | 157580 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 63b03af701f7e61f02dc83ec5f76b36e2c439120f5f8060a35ad0f67302eff1f |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.1.so | 114612 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | c848a731986be093c60a466122387fc572feea8ae5b0140e171fd74a9ae11694 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.2.so | 115316 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 9b342cd2ffbab9fef1e7d148738fe1325a16b878e6ea6b42e88a9dbb82ae5aca |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib/vendor.mediatek.hardware.pq@2.3.so | 145936 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 976438d66a249ff467b5db8cd27184167ac665d442e359a51fe98852ac054481 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.allocator@2.0.so | 84328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 147fae699ac02c3e56d7923d4f152abe321db17ef7bd81e406afc0bd2dce8230 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.allocator@3.0.so | 84368 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | b2832794a427bfa757ff196c569ca611e6952d49944b3d3a4e453381dbb46b09 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.1.so | 255424 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 3b898553596f3869938c1799dea0de950f29ec00ddbad07edab8de0e11759596 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.2.so | 211472 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7a5a8522418eaf7f8ec89045637cc04aa612acdcf4a415ac7f0a16ba70b04d3a |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.composer@2.3.so | 265232 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f635c12bb8be56b50a3beefaea96a48675880346aa49c2d3eb0318874f526d99 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@2.0.so | 93184 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 674672272ad7a5aebc6b8c7f78e8306488bfc076ec3f5c0503cd7406d624346e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@2.1.so | 98208 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | da43e00d2c1c8c71b421025b59687ef1c9a120e3cb41a1844acc04ddcd5b427e |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/android.hardware.graphics.mapper@3.0.so | 102192 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ce23e15e7720d3f01ebbe710cd1006faf096282e2f45d7e83283b24459d3fc7c |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libEGL.so | 216920 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | f0db2ae3f41a10b6029c12b35764124bb9fc0055aa27305cde0ddee65a087786 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv1_CM.so | 39744 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 8865b82184ed9cdb685efa2e238d55eef02612361844261ee6ec5cbaf025fd86 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv2.so | 109200 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | d219bc0be29643555292a89398bce438a563e01c8b9d3dd62754b6d16c9ce1fd |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libGLESv3.so | 109200 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 95cd55ef165bbeafed285a86dcffd645f12a49e49b0405caf0e60173ea509e25 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libSurfaceFlingerProp.so | 109280 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | eda0446c93f1b2dead3d81cb6b297dfe0a3a1b7f0ca375a60e41dc6d5cd446d2 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libgpud_sys.so | 69792 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ef65275e90dc31a5f87c763348c8670b7f9713261a0070b205a4733555ec0289 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libgralloc_extra_sys.so | 24008 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 357b0ff18fd0a89950469560e5c04859f07d54e56a87b3a9d9cb8f4cc17087be |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libhwui.so | 8309472 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 1f5e809f7cb4c7c50841d20986690eb8a82ccc825d62025be8704e56e1310e66 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libsurfaceflinger.so | 1636960 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 14e709039f4c1dd63116ad13df0d9d482ddf9bc66bda827d32d3f21aae9b5c2b |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/libvulkan.so | 176904 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 960229d0d1e276cf5e32b173068289c6c23b042eaf31f3a24e50e0f127e4b635 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.gpu@1.0.so | 164488 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | afbc19635e613d247977a65756e4c8c089c0a8a1ef1babb31bd4e1e58d298e12 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.0.so | 214008 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | ec3182c357bef60731884c9272c25e4cf1a5546f8044f57f8090a3bccdbcc369 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.1.so | 146888 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 502223c60dd2da535fd77a647648d1157e55a71a8e227c351f700d9df9ae8483 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.2.so | 156328 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 6b4312e0f72882d4c8a713bde7c42d81f2a62543b93003f4d5094780308690c3 |
| SYSTEM_GRAPHICS_FRAMEWORK | /system/lib64/vendor.mediatek.hardware.pq@2.3.so | 191936 | 644 | 0 | 0 | u:object_r:system_lib_file:s0 | 7e6d9d80abc7e416c7f414127fb4372026108f4ffddce079b62801bcc19386fe |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/egl.cfg | 76 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 1606ef2f0ad0dfd812c8ec4ab7b7d9ae5bff3554191c7012530ca883612c70e1 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libEGL_mtk.so | 17604 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 771e33b95776024b15e897aacc8bea58e0db99392dc6eee622a1adc4549ae473 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLES_meow.so | 1252340 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 0152c3186e298907d4852515a1bd4f1076351ef493577df246660e582675bc4f |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLESv1_CM_mtk.so | 554392 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | ed4128824cdbb5b1be0a717fbefac831cb49184bf71129cdf823a6d12d48ad17 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libGLESv2_mtk.so | 1246648 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 924255a78a51b85160876ed625991324dee1b772d67bcd62129ace12ca9b5317 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib/egl/libMEOW_trace.so | 370644 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 01448f3479ae13bdc2d63a8dfec589bc04e907a94841546f06234025630d24ce |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libEGL_mtk.so | 14000 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c1e17bb6f92d90170573a84724483e0ffe574503aeba3716591964eeb6680276 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLES_meow.so | 1999944 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 32fa3f4d9044a72251c2e310c000b038867f1dabd091ac7da67e9f206b7803e8 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLESv1_CM_mtk.so | 706528 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 745912f6219393130a38a528a82473cdf76b703d60c3e21f1e39bdc65d01bb28 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libGLESv2_mtk.so | 1591296 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d4714f8a2e3f519f51b8cc0e796be19ce7d5c5d7c8dd06f62ed8f98c7a859d22 |
| VENDOR_EGL_GLES_MEOW | /vendor/lib64/egl/libMEOW_trace.so | 535640 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a1e0566272e1153d0a9a9e4b07c7aadde9277b7c1ba4f2b7510c01d9198b0843 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libIMGegl.so | 284056 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 1a1c0f144ea9d312ac1549231aeb3fb0ec5dffe594cec21961534db78cf8f3a1 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libOpenCL.so | 65456 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 557462a70eeff80729fd69a2e981b3491d367618c39286cf2223c42490a18dce |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libPVROCL.so | 25265440 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d323ef2ab3a8c9f6599113d2608db2e273ec4d6d2abe00ccc681dbf9cbfe1c85 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libPVRScopeServices.so | 112016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 5f352b257e99d724857d9d19992cb509a23a1335d4ebf331917c4fae312e806b |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libdpframework.so | 788880 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c9633ac03ea683e81b9fd171ed5188a902e97fdba17529955cfb47120a8b5c17 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgpu_aux.so | 78068 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a6687f6601986757b788962456629f9293010abd1eac70a974e9eee5a9dea8a9 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgpud.so | 63976 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b40686aeffdd5b5977c67e8ed8214462d025e5d9135fcbc71dbca11b80924011 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libgralloc_extra.so | 19640 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 52017263d40d2d480a6378cb0ce4120216c47a2816c1de9e1c92df1f156ccd02 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libion_mtk.so | 15156 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a120f5023a98dd12a60651616bffad75cc9903959da4799a03d705e23756482b |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libion_ulit.so | 14996 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4d9d08edf2b58fd63bb16fe2ce40dff5a815d1dc1c3b8b68e30687df7dafd519 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libmpvr.so | 13556 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b115de41227fa51b720c908b8879d0d02aecebdce72d6e95318f167086bdf1e9 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libpq_cust_base.so | 114252 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4cdee8e3cc6328a3564ab9ad1179a2e558c166c88d93609c29f5f667b297f6fe |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/libpq_prot.so | 174256 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | e380ac2dd91a873ea8ba5810e0404e5b7f18c84cadbd1814c6e0efd4f901376c |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.gpu@1.0.so | 123848 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2e67edc2ce12ff478296b60b72f9e96f16cebd31e8b7a72d4d38191eeed7ccfd |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.0.so | 157588 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 30878f6c763ccb2ab472720ed5debc3c6215b0cdd985f572d10aca77d25ec8e0 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.1.so | 114604 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 72a34b575f5c00d22066f959f9b49204f3964e1ece3d89adf846ffa60af52e78 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.2.so | 115316 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 0fcfb84c24e9d9ff4ce5536b5c100dcb93576a5b4764e25b2c89415c6b7fbbfe |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib/vendor.mediatek.hardware.pq@2.3.so | 145936 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 4ba90407ee2bd4555086683668919fca3ee91f0ec00f9e0b18930aa54caac078 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libIMGegl.so | 337888 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 929c7a9b124a7e91b8af29aac7ac4aba2ff814ccf7c26959703402923e569071 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libOpenCL.so | 85456 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 3feb689544917c67e5a3ecef82639386dd25c9139e618af2bcb705c6aa2dc553 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libPVROCL.so | 34741696 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2d10c57e0052c12bd30b9f8f6a76a4291e17e23cb660274b7c74e01f8d349809 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libPVRScopeServices.so | 133088 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 33d1c4b43b7db9caabd57cdafaeb88d5fe5f9111a6778c8db0c9c0e577940a72 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libdpframework.so | 1041672 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a3a3d74969ec8d2abb8341a501406f5cbdb1b9057102be16cc58bdf81fba2033 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgpu_aux.so | 89320 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 36e466a01c259e2f3c9dde6ef2a8abe2c6a58f83553333400e5fb8aadbc0e0dd |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgpud.so | 69776 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 63f8e4acc38567e6e65b3752170ec3a416b8863154b05a8a965808f242fdf040 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libgralloc_extra.so | 24016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | e1605c7911757612d1eba1f2a0a791b0007994c5712f18934e3e033a24eaf53d |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libion_mtk.so | 15160 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 967fa4db28e4e600f2f5d50ac364d20b994f8e9ba6306c8d629c41bd8f4c72ad |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libion_ulit.so | 15080 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 3b30b53ff2497976912a4c918c827fd8a7d52ea740c1fa39ccf089b2b1b5dc48 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libmpvr.so | 5880 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 498cf3a7d57eceb4efdde278017044e223905f9c10cdfd5019a292946312992d |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libpq_cust_base.so | 105280 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 78a13871437e01bdfe62d22754868b28934b0b7744119931a07855e0d11ebc57 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/libpq_prot.so | 212384 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | f809db6cdafa29d34946f2807a87610636f090298a8fcf18b9590c27b07773dc |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.gpu@1.0.so | 164488 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 03485b9903f5b6c2dc9a2290aadc3ee47b9d4f011f6ea6a3d82607aa73836048 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.0.so | 214016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d612cabb77d4f8892278d3a145ed00b848f750576a63346fea4f11a3fa1d12be |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.1.so | 146888 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2cc4e78e0a64d65a4646c03186efad365e77a4d14537d47d9ced5e7658a56c30 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.2.so | 156336 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 33f515cd41716ad8a8475f5b3595921c4bf3b6da7a304a0ccaa0f019d808e527 |
| VENDOR_GPU_PQ_MEMORY_STACK | /vendor/lib64/vendor.mediatek.hardware.pq@2.3.so | 191936 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | c758a979c2b224d961eb4a8f2444576947c5c3ef91346860a5b3d46474db14b1 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.allocator@2.0-impl.so | 24580 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | e08fd77637989d8b2df4c602a204cced8861832ac4832419f3e89cc07c6e2e7b |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.composer@2.1-impl.so | 79400 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 8f4221d1c37d21d755629564e6b1ffedd5d4fa93b5c2836a583c8da89de8c999 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 29980 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 79dba7c0136c21a627cfd0c92fe9e247b0694c791d62e47ee7124fdb00b87b9c |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/gralloc.default.so | 28788 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | bb60cb89dcff1b310035433b4712f5002cbb267c08ffacfc92b09574942bb8e3 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/gralloc.mt6765.so | 552832 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | b7a3ec5bc130e5c6d9fe3c24f0f9d85c61981da0c306c2c167521580ba8e90e7 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/vendor.mediatek.hardware.pq@2.3-impl.so | 131772 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 6b028a1e5a9cf765577a44e1b3ee983b026ab25f20f04596c4265e9284b7be3f |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib/hw/vulkan.mt6765.so | 543016 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d174bf26d577768fb1e34f2853192a4856a1752f20e623ca0a80add4bcafafd1 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.allocator@2.0-impl.so | 24904 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 8a8f18a08c0bebc96c1f936ca971dd16b06f02c0db7b4fb5bd3c8a5f4960aa65 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.composer@2.1-impl.so | 104224 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | a3ace507225480020f0ae9e36be72f22a83714575b844f666841418fe3b0c98e |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/android.hardware.graphics.mapper@2.0-impl-2.1.so | 38696 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 2d6c882b22326ed01a578f1d8545a73838c903ffb568b986665f7b30f47ed9c0 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/gralloc.default.so | 23824 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | d6bfcd749ea6f386e981fcc9b3c35c9bc89d2cde2da43b6aef73a896cae568aa |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/gralloc.mt6765.so | 616632 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | 89ec41a1a401551d88422d8c22df1ea523b2153e00fd5fc1c8a2ff1cacc60570 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/hwcomposer.mt6765.so | 682224 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | 4887675a3d5f4b85496c4e677f402be62806302a928927b95f54e412c4cd7694 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/vendor.mediatek.hardware.pq@2.3-impl.so | 177208 | 644 | 0 | 0 | u:object_r:vendor_file:s0 | fecfc33bf5e43a75bba53c08a2f4ca65f49081abfb427dc05710a05bb2ea0190 |
| VENDOR_GRAPHICS_HAL_IMPL | /vendor/lib64/hw/vulkan.mt6765.so | 683024 | 644 | 0 | 0 | u:object_r:same_process_hal_file:s0 | a0aec798ba07930dae09b6092b32e05e05af8326e6edd7e8683e50caf73acbb5 |
################################################################################
# 05.21 — SOFTWARE DATABASE DISPOSITION REGISTER
################################################################################
| Entry | Disposition | Reason |
|---|---|---|
| ST7701S LCM | KEEP | active panel identity |
| NT35695B references | DEMOTE | superseded prior inference; not active truth |
| /proc/pl_lk DSI fields | KEEP | boot display timing/register authority |
| DSI private init command arrays | NOT_RUNTIME_EXPOSED | do not invent; source/disassembly only |
| mtkfb / fb0 | KEEP | active framebuffer |
| /dev/mtk_disp_mgr | KEEP | active composer/PQ display manager node |
| /dev/dri/card0/controlD64/renderD128 | KEEP | active PVR DRM nodes |
| /dev/pvr_sync | KEEP | active PVR sync node |
| /proc/ged and /sys/kernel/debug/ged | KEEP | active GED/DVFS interface |
| /sys/kernel/debug/pvr | KEEP | active PVR driver/runtime authority |
| leds-mt65xx lcd-backlight | KEEP | framework-visible backlight class |
| mt6370_pmu_bled | KEEP | MT6370 BLED path |
| gralloc.mt6765.so | KEEP | active allocator/HWC/SF mapped blob |
| hwcomposer.mt6765.so | KEEP | active HWC implementation |
| android.hardware.graphics.mapper@2.0-impl-2.1.so | KEEP | active mapper implementation |
| libEGL_mtk / libGLES*_mtk | KEEP | active vendor EGL/GLES |
| libGLES_meow / libMEOW_trace | KEEP | MEOW/PVR GLES authority |
| libIMGegl / libmpvr | KEEP | active IMG/PVR userspace |
| vulkan.mt6765.so | KEEP | vendor Vulkan HAL |
| libOpenCL / libPVROCL | KEEP | PowerVR OpenCL stack |
| vendor.mediatek.hardware.gpu@1.0-service | KEEP | active MTK GPU extension HAL |
| vendor.mediatek.hardware.pq@2.2-service and pq@2.3 impl | KEEP | active picture-quality HAL |
| Mali selector string in egl.cfg | DEMOTE | config residue/name selector only |
| Mali module/device nodes | NEGATIVE | no live module/node/module file evidence |
| secondary display feature | DECLARED_SOFTWARE_ONLY | no external physical display node in this section |
| Wi-Fi display settings | OFF | software setting only; no physical display proof |
################################################################################
# 05.22 — OPEN LEADS
################################################################################
Section 05 blocking open leads:
None.

Narrow non-runtime-exposed item:
| Item | Classification | Impact |
|---|---|---|
| Raw ST7701S panel vendor init command array | NOT_RUNTIME_EXPOSED | Does not block Section 05; LK/DSI mode/timing/fb/panel identity are closed. Only source/LK/DTB/disassembly can expose the private array. |

Cross-section ownership notes:
| Surface | Section 05 treatment | Owning section |
|---|---|---|
| MT6370 physical rail routing | software path closed; physical trace not owned here | Hardware Section 08 / Camera-PMIC rail sections |
| Camera preview / ISP graphics consumers | not Section 05 authority | Section 06 |
| Touch coordinate confirmation 480x640 | cross-reference only | Hardware Section 08 / software input sections |
| External HDMI/display absence | no physical node in Section 05; negative belongs elsewhere | Negative hardware/software sections |

################################################################################
# 05.23 — ORIGINAL CLAIM RECONCILIATION LEDGER
################################################################################
| Original/outlined item | Resolution | Evidence |
|---|---|---|
| LCM/panel identity ST7701S confirmed | CONFIRMED | /proc/driver/lcm_info and /proc/pl_lk |
| NT35695B superseded | CONFIRMED | no active runtime proof; superseded by ST7701S |
| DSI command mode | CORRECTED | runtime boot register says BURST_VDO_MODE |
| 480x640 framebuffer | CONFIRMED | fb0 mode U:480x640p-0 |
| virtual framebuffer | CONFIRMED | 480,1920 / three buffers |
| MT6370 BLED | CONFIRMED | I2C5 5-0034 mt6370_pmu_bled |
| PowerVR GE8320 | CONFIRMED | hardware DB + PVR/Rogue/MEOW runtime evidence |
| MEOW codename | CONFIRMED | libGLES_meow.so and libMEOW_trace.so |
| GPU OPP/DVFS | CONFIRMED | GED total_gpu_freq_level_count=3, current/previous frequency |
| SurfaceFlinger config | CONFIRMED | dumpsys SurfaceFlinger |
| HWC2 composer | CONFIRMED | lshal active + PID 526 + hwcomposer.mt6765.so mapped |
| HWUI config | CONFIRMED | ro.hwui.use_vulkan empty + libhwui hashes |
| mali.ko not loaded | CONFIRMED | /proc/modules shows only fpsgo match; no module files/nodes |
| egl.cfg verified | CONFIRMED | path, hash, label, content captured |
| gralloc.mt6765.so present | CONFIRMED | 32/64 files, hashes, mapped in active processes |
| hwcomposer.mt6765.so present | CONFIRMED | 64-bit file hash, mapped in HWC process |
| All targeted graphics blobs | CONFIRMED | manifest embedded with classification |
################################################################################
# 05.24 — FINAL SECTION 05 CLOSURE STATEMENT
################################################################################
Section 05 is closed.

The C17S active display/GPU stack is:
ST7701S 480 x 640 LCM
  → DSI0 / LK BURST_VDO_MODE / 1 lane / 320 MHz PLL register set
  → mtkfb / fb0 / 32 bpp / 480x1920 virtual framebuffer
  → MTK display manager / CMDQ / ION / DMA-BUF / sync
  → SurfaceFlinger + HWC2
  → gralloc.mt6765.so / hwcomposer.mt6765.so / mapper@2.1
  → PowerVR GE8320 / Rogue / RGX
  → pvrsrvkm at 13000000.mfg_doma
  → PVR debugfs + GED DVFS + MFG clocks
  → MTK/IMG/PVR/MEOW EGL/GLES, Vulkan, and OpenCL vendor stack

The active backlight software path is:
Android light/brightness framework
  → lcd-backlight LED class
  → MT6370 PMU BLED at I2C5 address 5-0034
  → display PWM / BLED output.

Mali is not the active hardware. Its only captured Section 05 relevance is the
`0 1 mali` selector line in /vendor/lib/egl/egl.cfg, which is demoted as residue
because all live kernel, userspace, and debugfs evidence proves the PVR/IMG/MEOW
stack.

No Section 05 runtime/file-scope item remains unresolved. The only not-runtime-
exposed item is the raw private ST7701S vendor panel initialization command array,
which is not inventable and is not required to close the runtime display/GPU
software authority.

################################################################################
# 05.25 — SMALL BUILD RELEVANCE NOTE
################################################################################
For clean AOSP bring-up, Section 05 implies:
- Carry vendor graphics HAL services and RC wiring for allocator@2.0, composer@2.1,
  vendor.mediatek.hardware.gpu@1.0, and vendor.mediatek.hardware.pq@2.2.
- Carry gralloc.mt6765.so, hwcomposer.mt6765.so, mapper@2.0-impl-2.1.so,
  allocator/composer impl libraries, vendor EGL/GLES MEOW libraries, libIMGegl,
  libmpvr, libPVROCL, libOpenCL, vulkan.mt6765.so, MTK GPU/PQ libs, and rgx.fw.
- Preserve /dev/dri, /dev/pvr_sync, /dev/ion, /proc/ged, /sys/kernel/debug/pvr,
  /sys/kernel/debug/ged, /dev/mtk_disp_mgr, /dev/mtk_cmdq, and relevant SELinux
  labels/permissions.
- Preserve MT6370 BLED/light HAL behavior for panel brightness.
- Do not introduce Mali assumptions. Do not replace PVR/IMG/MEOW with a Mali stack.
- Treat NT35695B as superseded and ST7701S as the active panel.
- Use ro.opengles.version=196610 / GLES 3.2 and vendor Vulkan/OpenCL feature
  declarations consistently.

################################################################################
# END SECTION 05 — DISPLAY, GPU & GRAPHICS STACK
################################################################################
