################################################################################
# C17S SOFTWARE DIGITAL TWIN
# SECTION 07 — AUDIO STACK — POLICY, HAL, PARAMETERS & ROUTES
#
# Device      : LOKMAT APPLLP 5 MAX (C17S)
# Board       : WP_C17S_PIX_TFT_D4
# Author      : beright1976
# Status      : CANONICAL SECTION 07 — AUDIO / POLICY / HAL / PARAMETER / ROUTE AUTHORITY MAP
# Closure     : CLOSED WITH PHYSICAL 3.5 MM NEGATIVE, USB/POGO ACCESSORY MAPPING LEAD OPEN, AND LAYERED ROUTE MODEL RESOLVED
# Date        : 2026-06-17 live audio policy, HAL, ALSA, parameter, Aurisys, process-map, ACCDET, ATCI, DAPM, and device-tree integration pass
################################################################################

################################################################################
# SCOPE
################################################################################

This section documents the C17S audio stack as a software digital-twin database
section. It records the Android audio policy layer, MTK audio HAL layer,
AudioFlinger / AudioPolicy runtime layer, ALSA kernel PCM layer, audio parameter
layer, Aurisys tuning layer, effects chain, accessory-detect surfaces, cellular
voice audio interface, Bluetooth/USB/FM/remote-submix route surfaces, and the
physical-versus-declared endpoint boundary.

This section covers:
- Primary audio HAL service, process, SELinux domain, UID/GID, group set, and
  binder/hwbinder publication.
- Audio policy configuration files, legacy policy residue, audio-device routing
  XML, effects XML, volume XML, USB/Bluetooth/A2DP/r_submix included policy files.
- Primary audio policy module topology: mix ports, device ports, attached devices,
  default output, and route graph.
- Runtime AudioPolicyManager and AudioFlinger route state.
- ALSA card, /dev/snd device nodes, /proc/asound PCM inventory, and platform audio
  route surfaces.
- Audio HAL process maps and file descriptors proving ALSA control and modem audio
  channels.
- Complete vendor audio HAL/effect blob carry set with ABI split, sizes, and SHA256.
- Audio tuning and parameter files: strict /vendor/etc/audio_param count and wider
  audio tuning XML boundary.
- Aurisys configuration and speech parameter file boundary.
- ACCDET / PMIC accessory-detect software presence and physical 3.5 mm negative.
- ATCI audio diagnostic surface and socket declaration.
- Route classification for speaker, earpiece, wired headset/headphones/mic, USB,
  Bluetooth SCO, Bluetooth A2DP, hearing aid, remote submix, FM, ANC, telephony,
  voice call, dock, AUX/HDMI, and back microphone surfaces.

This section does not perform a physical board trace. Physical observations are
recorded only as owner-supplied hardware boundary facts and are used to prevent
software declarations from being promoted to physical endpoints. Clean-ROM notes
are limited to preservation and risk boundaries; this is not a ROM build document.

################################################################################
# SOURCE AUTHORITY
################################################################################

Source authority order:
TIER 1 : Live rooted C17S runtime evidence from adb shell under su.
TIER 2 : Clean extracted super_unpacked filesystem authority.
TIER 3 : Completed software Sections 00 through 06.
TIER 4 : Hardware digital-twin database cross-reference and owner physical
         observation.
FINAL  : Beright / Albert live correction gate.

Primary live captures consumed:
- Gate 07A: runtime properties, init service state, audio processes, binder
  services, hwbinder registrations.
- Gate 07B: audio configuration existence conflict close.
- Gate 07C: policy/device/effects/Aurisys XML topology, line counts, sizes, SHA256.
- Gate 07D: strict /vendor/etc/audio_param inventory and category counts.
- Gate 07E: AudioPolicyManager, AudioFlinger, and AudioService route-state dump.
- Gate 07F: /dev/snd, /proc/asound, /sys/class/sound, ACCDET, and tinymix boundary.
- Gate 07G: 96 versus 99 parameter-bucket closure.
- Gate 07H: synchronized runtime drift snapshot.
- Gate 07I: audioserver and audio HAL maps/fds.
- Gate 07J: vendor audio HAL/effects/service blob inventory.
- Gate 07K: ACCDET/headset/dock/USB/pogo software route scan.
- Gate 07L: audio ATCI diagnostic surface.
- Gate 07M: DAPM/codec route detail for suspicious audio surfaces.
- Gate 07N: device-tree audio node detail.

################################################################################
# RULES
################################################################################

No stale values.
No borrowed values from similar MediaTek devices.
No software declaration promoted to physical hardware truth.
No Android policy route promoted to active route without runtime evidence.
No ALSA PCM name promoted to physical endpoint without hardware evidence.
No ACCDET/HP_IMPEDANCE route promoted to a 3.5 mm jack without visible physical
jack or insertion-state evidence.
No USB/headset/dock/pogo mapping asserted without a route-state trigger or
physical pin trace.
No ROM carry decision is allowed to replace database classification.

Classification vocabulary used here:
- [ACTIVE_RUNTIME] — live process, service, file descriptor, route, or HAL state.
- [FILE_AUTHORITY] — static file exists with captured size/hash/label.
- [DECLARED] — declared by policy, XML, init, DT, or VINTF but not proven active.
- [VERIFIED] — verified by live runtime, static file, or hardware boundary evidence.
- [PHYSICAL_CONFIRMED] — confirmed by physical observation or hardware database.
- [PHYSICAL_NEGATIVE] — absent physically for this device.
- [BSP_GHOST] — inherited/unpopulated software route candidate.
- [OPEN_LEAD] — retained because evidence is incomplete but route may be functional.
- [REQUIRED] — required to preserve current stock function or first-boot stability.
- [CONFLICT_RESOLVED] — earlier conflicting data is resolved by later gate evidence.

################################################################################
# 07.0 — CAPTURE BOUNDARY
################################################################################

Capture state:
- Device state is the current research state, not pristine locked stock.
- Audio services are live under a rooted permissive research environment.
- Boot duration at Gate 07H was 4 days, 11:51.
- Gate 07H timestamp: Wed Jun 17 13:03:02 CDT 2026.
- `audioserver` PID in capture: 544.
- `android.hardware.audio@5.0-service-mediatek` PID in capture: 516.
- `ro.boottime.audioserver`: 15909099462.
- `ro.boottime.vendor.audio-hal-2-0`: 15785459923.

Important boundary:
Runtime route fields, selected devices, active patch counts, and force-use values
are moment-capture facts. Static file topology, hashes, and line counts are stable
file-authority facts. Kernel PCM nodes are kernel-exposed route surfaces. Physical
endpoint classification is controlled by hardware evidence and physical inspection.

################################################################################
# 07.1 — COMPLETION MATRIX
################################################################################
| Scope item | Status | Closure result |
|---|---|---|
| Primary audio HAL service/process | CLOSED | vendor.audio-hal-2-0 running; PID 516; domain u:r:mtk_hal_audio:s0; hwbinder interfaces registered. |
| Audioserver process | CLOSED | /system/bin/audioserver running; PID 544; domain u:r:audioserver:s0. |
| VINTF/HIDL audio interfaces | CLOSED | audio@5.0 IDevicesFactory, audio.effect@5.0 IEffectsFactory, bluetooth.audio@2.0 providers registered through PID 516. |
| Policy config source | CLOSED | Runtime AudioPolicyManager config source is /vendor/etc/audio_policy_configuration.xml. |
| Policy XML topology | CLOSED | Primary module, mix ports, device ports, routes, attached devices, default output captured. |
| Legacy policy conflict | CLOSED | audio_policy.conf exists and is retained as legacy residue; not assumed absent. |
| audio_device.xml route controls | CLOSED | Speaker/headphone/headset/mic/voice/ext speaker route names captured. |
| Effects XML and soundfx ABI set | CLOSED | audio_effects.xml and all 32/64 soundfx libraries captured with sizes/hash. |
| Audio parameter inventory | CLOSED | Strict audio_param count 96; wider audio tuning XML set 99. |
| Aurisys boundary | CLOSED | /vendor/etc/aurisys_config.xml and /vendor/etc/aurisys_param/Speech_AudioParam.xml captured. |
| ALSA card and PCM inventory | CLOSED | card0 mtsndcard and PCM 00-00 through 00-31 captured. |
| HAL fd ownership | CLOSED | HAL opens /dev/snd/controlC0, /dev/ccci_aud, /dev/ccci_raw_audio. |
| ACCDET / headset physical boundary | CLOSED | pmic-accdet DT node exists; no visible 3.5 mm jack; /sys/class/switch absent. |
| USB audio route family | CLOSED | USB policy module and Voice_USB PCM surfaces captured; physical external-headset mapping remains open. |
| Bluetooth route family | CLOSED | BT SCO/A2DP/hearing aid policy and BT/CCCI/BTCVSD surfaces captured. |
| FM route family | CLOSED WITH OPEN RF LEAD | FM audio policy/PCM surfaces exist; RF path is not proven in this section. |
| ANC/back mic physical mapping | CLOSED WITH OPEN MAPPING LEAD | ANC/back mic software surfaces exist; exact physical microphone directionality remains open. |
| ATCI audio diagnostic surface | CLOSED | audiocmdservice_atci.rc declares disabled oneshot audio-daemon and atci-audio socket; no live audio-daemon process in capture. |

################################################################################
# 07.2 — HIGH-LEVEL AUDIO ARCHITECTURE
################################################################################

C17S audio is not a single clean watch-form-factor policy. It is a layered MTK
phone-class audio stack carried into a watch-class board. The live device exposes:

1. Android framework audio layer:
   - `audioserver`
   - AudioFlinger
   - AudioPolicyService
   - AAudio service
   - framework audio policy manager and volume curves

2. MTK vendor HAL layer:
   - `/vendor/bin/hw/android.hardware.audio@5.0-service-mediatek`
   - `audio.primary.mt6765.so`
   - `audio.usb.mt6765.so`
   - `audio.r_submix.mt6765.so`
   - Bluetooth audio implementation
   - vendor audio effects implementation
   - vendor speech/BES/audio-parameter parser libraries

3. Policy/configuration layer:
   - modern XML policy
   - legacy policy.conf residue
   - audio_device route-control XML
   - effects XML
   - USB, Bluetooth, A2DP, remote-submix policy modules
   - volume curve XMLs

4. Tuning/parameter layer:
   - strict `/vendor/etc/audio_param` parameter directory
   - `audio_em.xml`
   - Aurisys config and speech parameter file

5. Kernel/platform layer:
   - ALSA card `mtsndcard`
   - `/dev/snd` device nodes
   - `/proc/asound/pcm` route surfaces
   - `11220000.audio` platform nodes
   - `audio_snd_card`, `audio@11220000`, `audio_sram@11221000`, and
     `mtk-btcvsd-snd@18050000` DT surfaces

6. Physical endpoint boundary:
   - Speaker is the active physical output family.
   - Built-in microphone and back-mic policy surfaces exist.
   - No visible physical 3.5 mm headset jack is present.
   - USB-C/pogo/accessory routing remains the plausible location for any
     headset-class semantics, but this section does not assert that mapping.

################################################################################
# 07.3 — INIT, SERVICE, HIDL, BINDER, AND RUNTIME IDENTITY
################################################################################

Runtime services:
| Service / process | State | PID | Domain | User / groups | Classification |
|---|---|---:|---|---|---|
| `audioserver` | running | 544 | `u:r:audioserver:s0` | UID 1041, GID 1005, groups 1000 1006 1013 1015 1026 1031 3001 3002 3003 3007 3010 | [ACTIVE_RUNTIME] |
| `vendor.audio-hal-2-0` / `android.hardware.audio@5.0-service-mediatek` | running | 516 | `u:r:mtk_hal_audio:s0` | UID 1041, GID 1005, same group set | [ACTIVE_RUNTIME] |

Binder services captured:
- `soundtrigger`: `com.android.internal.app.ISoundTriggerService`
- `audio`: `android.media.IAudioService`
- `media.sound_trigger_hw`: `android.hardware.ISoundTriggerHwService`
- `media.audio_policy`: `android.media.IAudioPolicyService`
- `media.audio_flinger`: `android.media.IAudioFlinger`

Hwbinder registrations captured through audio HAL PID 516:
- `android.hardware.audio@5.0::IDevicesFactory/default`
- `android.hardware.audio.effect@5.0::IEffectsFactory/default`
- `android.hardware.bluetooth.audio@2.0::IBluetoothAudioProvidersFactory/default`

Runtime properties:
```text
[init.svc.audioserver]: [running]
[init.svc.vendor.audio-hal-2-0]: [running]
[init.svc.media]: [running]
[log.tag.APM_AudioPolicyManager]: [D]
[persist.log.tag.RpAudioControl]: [I]
[persist.vendor.audiohal.besloudness_state]: [1]
[persist.vendor.audiohal.bt_headset_nrec_on]: [0]
[persist.vendor.audiohal.firstboot]: [0]
[persist.vendor.bluetooth.a2dpstandbytime]: [500]
[ro.audio.silent]: [0]
[ro.audio.usb.period_us]: [16000]
[ro.vendor.mtk_audio_alac_support]: [1]
[ro.vendor.mtk_audio_ape_support]: [1]
[ro.vendor.mtk_audio_tuning_tool_ver]: [V2.2]
[ro.vendor.mtk_besloudness_support]: [1]
[vendor.audiohal.speech.shm_init]: [1]
```

################################################################################
# 07.4 — INIT RC AUTHORITY
################################################################################

Vendor audio HAL service declaration:
```rc
on post-fs-data
    mkdir /data/vendor/audiohal 0771 system audio

service vendor.audio-hal-2-0 /vendor/bin/hw/android.hardware.audio@5.0-service-mediatek
    class hal
    user audioserver
    group audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw
    capabilities BLOCK_SUSPEND SYS_NICE
    ioprio rt 4
    writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks
    oneshot
    interface android.hardware.audio@5.0::IDevicesFactory default
```

Audioserver service declaration:
```rc
service audioserver /system/bin/audioserver
    class core
    user audioserver
    group audio camera drmrpc inet media mediadrm net_bt net_bt_admin net_bw_acct wakelock system sdcard_rw
    capabilities BLOCK_SUSPEND
    ioprio rt 4
    writepid /dev/cpuset/foreground/tasks /dev/stune/foreground/tasks
    onrestart restart vendor.audio-hal-2-0
    onrestart restart audio-hal-2-0
```

Audio ATCI diagnostic service declaration:
```rc
service audio-daemon /vendor/bin/audiocmdservice_atci
    class main
    socket atci-audio stream 666 system system
    user system
    group system audio
    disabled
    oneshot
```

Classification:
- `vendor.audio-hal-2-0` is active runtime audio HAL. [ACTIVE_RUNTIME]
- `audioserver` is active runtime framework audio service. [ACTIVE_RUNTIME]
- `audio-daemon` / `audiocmdservice_atci` is declared diagnostic/ATCI surface;
  no live matching process was present in Gate 07L. [DECLARED]
- `rild-atci` socket and `vendor.mtk.atci.boot_completed=1` exist, so ATCI is not
  removed from the database boundary. [OPEN_LEAD]

################################################################################
# 07.5 — FILE AUTHORITY REGISTER: AUDIO CONFIGURATION XML AND POLICY FILES
################################################################################
| Path | Lines | Bytes | SHA256 | Classification note |
|---|---|---|---|---|
| /vendor/etc/audio_policy_configuration.xml | 274 | 17636 | 15eb86cb9c7a7a28ec216c6d44b6132dbf83dc2d73c9ce13b31e98d96fae299d | primary modern policy source |
| /vendor/etc/audio_policy.conf | 186 | 6435 | d5dd11d234f0a6a5244d0f7227d3076ff4ebf62b1310c58923fc299cd02c01f6 | legacy policy residue still present |
| /vendor/etc/audio_device.xml | 278 | 12583 | 44373295a89786c24b060b79be77f524514e9b242c2ec5fc61616ca55aa4866d | MTK route/control XML |
| /vendor/etc/audio_effects.xml | 106 | 6235 | f1867f84bb71cbc16d4b405450e3c3eba14e2b2000defbc6c13eef81b1984fd2 | effects declaration XML |
| /vendor/etc/audio_effects.conf | ABSENT | ABSENT | ABSENT | legacy conf absent |
| /vendor/etc/aurisys_config.xml | 331 | 16772 | a985b9507a2c207b3316189aead214c2ca6be949428657f4f13dcc0fb60314fe | Aurisys scenario config |
| /vendor/etc/aurisys_param/Speech_AudioParam.xml | 400 | 57003 | a90382b952ff692f68b20911dc91f91523182bdc0f877d7fab677131d986ed8d | Aurisys speech parameter file |
| /vendor/etc/audio_em.xml | 55 | 2702 | 94ded9c8c3f250cbdd35b736b45968e2434a61f6597a3ebc1c950257f4c13bb7 | engineering/audio tuning XML |
| /vendor/etc/bluetooth_audio_policy_configuration.xml | 44 | 2099 | 877d8745fe542a2e1070d773d64c7c6b3d03168679d3d8355dd8ee283c5ec884 | Bluetooth Audio HAL 2.0 policy |
| /vendor/etc/a2dp_audio_policy_configuration.xml | 44 | 2034 | f8de61bf7552d1e2d4ffdd8cc0dcf33c0794aa38f20700b05b546c95526b2b77 | legacy A2DP policy |
| /vendor/etc/a2dp_in_audio_policy_configuration.xml | 22 | 904 | 3ccbeb59afade7c19e891ab4cd515bc77b0baf02ec785e48d14f834639d4aa30 | A2DP input policy |
| /vendor/etc/usb_audio_policy_configuration.xml | 47 | 2233 | f0190ceaf22c50f2c38a866740694d9c017ae79311bac4bcc39eda8e8bf7241e | USB audio policy |
| /vendor/etc/r_submix_audio_policy_configuration.xml | 47 | 2105 | 44aa04e6a3fe674bf22e1e87fee602ca4d42e701740bf96f0101fe6c3b2abe09 | remote submix policy |
| /vendor/etc/audio_policy_configuration_bluetooth_legacy_hal.xml | 237 | 14794 | 720c734a9ac2086ed577038565fbac05873cdd1c8f3b349b735748d15202a0de | legacy Bluetooth/audio policy variant |
| /vendor/etc/audio_policy_volumes.xml | 179 | 10439 | 2dac8b16c0841fdaafe1e6d0986a5035c4fe2c66e6178eabe24f2711900b0630 | volume curves |
| /vendor/etc/default_volume_tables.xml | 70 | 2617 | 8d9025527a672bca499f6276c44ceba4b977470de09c6b1eb60fa03509658c21 | default volume table refs |

Conflict resolved:
Earlier source material stated or implied that `audio_device.xml` and
`audio_policy.conf` were absent. Live Gate 07B/07C closed that conflict. Both
files exist and are required database facts. `audio_effects.conf` is absent; the
active declared effects file is `audio_effects.xml`.

################################################################################
# 07.6 — PRIMARY POLICY CONFIGURATION TOPOLOGY
################################################################################

Runtime config source:
```text
Config source: /vendor/etc/audio_policy_configuration.xml
```

Primary policy file:
- Path: `/vendor/etc/audio_policy_configuration.xml`
- Lines: 274
- Bytes: 17636
- SHA256: `15eb86cb9c7a7a28ec216c6d44b6132dbf83dc2d73c9ce13b31e98d96fae299d`
- Global configuration: `speaker_drc_enabled="false"`
- Primary module: `primary`
- Primary HAL version: `3.0`
- Default output device: `Speaker`

Attached primary devices:
- `Speaker`
- `Earpiece`
- `Built-In Mic`
- `Built-In Back Mic`
- `FM Tuner In`
- `Voice Call In`
- `Telephony Tx`

Primary mix ports:
| Mix port | Role | Flags / limits | Classification |
|---|---|---|---|
| `primary output` | source | `AUDIO_OUTPUT_FLAG_PRIMARY|AUDIO_OUTPUT_FLAG_FAST` | [DECLARED] [ACTIVE_RUNTIME speaker-selected] |
| `deep_buffer` | source | `AUDIO_OUTPUT_FLAG_DEEP_BUFFER` | [DECLARED] |
| `primary input` | sink | `maxOpenCount=1`, `maxActiveCount=1` | [DECLARED] |
| `incall_music_uplink` | source | `AUDIO_OUTPUT_FLAG_INCALL_MUSIC` | [DECLARED] |
| `voice tx` | sink | `maxOpenCount=1`, `maxActiveCount=1` | [DECLARED] |
| `FM Tuner input` | sink | `maxOpenCount=1`, `maxActiveCount=1` | [DECLARED] |

Primary output device ports:
- `Earpiece` — `AUDIO_DEVICE_OUT_EARPIECE`
- `Speaker` — `AUDIO_DEVICE_OUT_SPEAKER`
- `Wired Headset` — `AUDIO_DEVICE_OUT_WIRED_HEADSET`
- `Wired Headphones` — `AUDIO_DEVICE_OUT_WIRED_HEADPHONE`
- `AUX Digital Out` — `AUDIO_DEVICE_OUT_AUX_DIGITAL|AUDIO_DEVICE_OUT_HDMI`
- `BT SCO` — `AUDIO_DEVICE_OUT_BLUETOOTH_SCO`
- `BT SCO Headset` — `AUDIO_DEVICE_OUT_BLUETOOTH_SCO_HEADSET`
- `BT SCO Car Kit` — `AUDIO_DEVICE_OUT_BLUETOOTH_SCO_CARKIT`
- `Analog Dock Headset` — `AUDIO_DEVICE_OUT_ANLG_DOCK_HEADSET`
- `Digital Dock Headset` — `AUDIO_DEVICE_OUT_DGTL_DOCK_HEADSET`
- `FM Tuner Out` — `AUDIO_DEVICE_OUT_FM`
- `USB Primary Out` — `AUDIO_DEVICE_OUT_BUS`, address `usb_phone_call`
- `Telephony Tx` — `AUDIO_DEVICE_OUT_TELEPHONY_TX`

Primary input device ports:
- `Built-In Mic` — `AUDIO_DEVICE_IN_BUILTIN_MIC`
- `Built-In Back Mic` — `AUDIO_DEVICE_IN_BACK_MIC`
- `Wired Headset Mic` — `AUDIO_DEVICE_IN_WIRED_HEADSET`
- `BT SCO Headset Mic` — `AUDIO_DEVICE_IN_BLUETOOTH_SCO_HEADSET|AUDIO_DEVICE_IN_ALL_SCO`
- `AUX Digital In` — `AUDIO_DEVICE_IN_AUX_DIGITAL|AUDIO_DEVICE_IN_HDMI`
- `FM Tuner In` — `AUDIO_DEVICE_IN_FM_TUNER`
- `Echo Ref In` — `AUDIO_DEVICE_IN_ECHO_REFERENCE`
- `USB Primary In` — `AUDIO_DEVICE_IN_BUS`, address `usb_phone_call`
- `Voice Call In` — `AUDIO_DEVICE_IN_TELEPHONY_RX|AUDIO_DEVICE_IN_VOICE_CALL`

Primary route graph:
| Sink | Sources | Classification |
|---|---|---|
| `Earpiece` | `primary output`, `deep_buffer`, `Voice Call In` | logical voice/earpiece route; physical receiver not proven |
| `Speaker` | `primary output`, `deep_buffer`, `FM Tuner In`, `Voice Call In` | active speaker route |
| `Wired Headset` | `primary output`, `deep_buffer`, `FM Tuner In`, `Voice Call In` | no physical 3.5 mm jack; possible accessory/USB/pogo or inherited route |
| `Wired Headphones` | `primary output`, `deep_buffer`, `FM Tuner In`, `Voice Call In` | no physical 3.5 mm jack; possible accessory/USB/pogo or inherited route |
| `AUX Digital Out` | `primary output` | HDMI/AUX software route; no HDMI hardware confirmed |
| `BT SCO` | `primary output`, `Voice Call In` | Bluetooth SCO call path surface |
| `BT SCO Headset` | `primary output`, `Voice Call In` | Bluetooth SCO headset surface |
| `BT SCO Car Kit` | `primary output`, `Voice Call In` | Bluetooth SCO car-kit surface |
| `Analog Dock Headset` | `primary output` | dock route; force-use analog dock present; physical endpoint open |
| `Digital Dock Headset` | `primary output` | dock route; physical endpoint open |
| `FM Tuner Out` | `primary output` | FM audio route; RF path open |
| `USB Primary Out` | `primary output`, `Voice Call In` | USB call/audio bus route |
| `Telephony Tx` | built-in/back/wired/BT/USB mics plus incall source | modem uplink route |
| `primary input` | built-in/back/wired/BT/AUX/USB input sources | primary capture surface |
| `voice tx` | `Voice Call In` | voice-call internal route |
| `FM Tuner input` | `FM Tuner In`, `Echo Ref In` | FM/echo-ref capture route |

################################################################################
# 07.7 — INCLUDED / PARALLEL AUDIO POLICY MODULES
################################################################################

Bluetooth Audio HAL policy:
- File: `/vendor/etc/bluetooth_audio_policy_configuration.xml`
- Module: `bluetooth`, HAL 2.0.
- Mix ports: `a2dp output`, `hearing aid output`.
- Device ports: `BT A2DP Out`, `BT A2DP Headphones`, `BT A2DP Speaker`,
  `BT Hearing Aid Out`.
- Routes: A2DP/hearing-aid outputs to Bluetooth device ports.

Legacy A2DP policy:
- File: `/vendor/etc/a2dp_audio_policy_configuration.xml`
- Module: `a2dp`, HAL 2.0.
- Mix ports: `a2dp output`, `a2dp input`.
- Device ports: `BT A2DP Out`, `BT A2DP Headphones`, `BT A2DP Speaker`,
  `BT A2DP In`.

A2DP input policy:
- File: `/vendor/etc/a2dp_in_audio_policy_configuration.xml`
- Module: `a2dp`, HAL 2.0.
- Mix port: `a2dp input`.
- Device port: `BT A2DP In`.

USB audio policy:
- File: `/vendor/etc/usb_audio_policy_configuration.xml`
- Module: `usb`, HAL 2.0.
- Mix ports: `usb_accessory output`, `usb_device output`, `usb_device input`.
- Device ports: `USB Host Out`, `USB Device Out`, `USB Headset Out`,
  `USB Device In`, `USB Headset In`.
- Routes: host out, device out, headset out, USB device/headset input.

Remote submix policy:
- File: `/vendor/etc/r_submix_audio_policy_configuration.xml`
- Module: `r_submix`, HAL 2.0.
- Routes: `Remote Submix Out` and `r_submix input`.

Legacy Bluetooth policy variant:
- File: `/vendor/etc/audio_policy_configuration_bluetooth_legacy_hal.xml`
- Preserves a legacy-style policy family with similar primary, A2DP, and USB
  routes. It is retained as file authority and not promoted above the runtime
  config source.

Legacy `audio_policy.conf`:
- File exists and carries inherited route declarations including earpiece,
  headset/headphone, dock, HDMI, A2DP, USB, MATV-style/ambient-style inputs,
  voice-call, back-mic, all-SCO, and FM tuner families.
- It is documented as inherited legacy policy residue and possible fallback
  authority for vendor code paths.

################################################################################
# 07.8 — RUNTIME AUDIO POLICY / AUDIOFLINGER STATE
################################################################################

Gate 07E / 07H runtime facts:
- Force use for dock: `8`.
- AudioService event log records `setForceUse(FOR_DOCK, FORCE_ANALOG_DOCK)` from
  persisted dock settings.
- HDMI system audio force-use is `0`; `mHdmiSystemAudioSupported=false`.
- `mBluetoothName=null` in the capture.
- `mSafeUsbMediaVolumeIndex=20`.
- `mSafeUsbMediaVolumeDbfs=-37.0`.
- `mFixedVolumeDevices=0x2c1c00`.
- Global active counts in the captured policy dump were 0 at the checked points.

Available output devices in runtime dump:
- `Earpiece` — `AUDIO_DEVICE_OUT_EARPIECE`
- `Speaker` — `AUDIO_DEVICE_OUT_SPEAKER`
- `Telephony Tx` — `AUDIO_DEVICE_OUT_TELEPHONY_TX`

Available input devices in runtime dump:
- `Built-In Mic` — `AUDIO_DEVICE_IN_BUILTIN_MIC`
- `Built-In Back Mic` — `AUDIO_DEVICE_IN_BACK_MIC`
- `FM Tuner In` — `AUDIO_DEVICE_IN_FM_TUNER`
- `Echo Ref In` — `AUDIO_DEVICE_IN_ECHO_REFERENCE`
- `Voice Call In` — `AUDIO_DEVICE_IN_TELEPHONY_RX|AUDIO_DEVICE_IN_VOICE_CALL`
- `Remote Submix In` — `AUDIO_DEVICE_IN_REMOTE_SUBMIX`

Audio patches in captured dump:
- Speaker sink patch: `AUDIO_DEVICE_OUT_SPEAKER`.
- Second speaker sink patch: `AUDIO_DEVICE_OUT_SPEAKER`.
- Telephony sink patch: `AUDIO_DEVICE_OUT_TELEPHONY_TX`.

AudioFlinger output threads:
| Thread | Type | Sample rate | HAL format | HAL frame count | Output device | Classification |
|---|---|---:|---|---:|---|---|
| `AudioOut_D` | MIXER | 48000 Hz | `AUDIO_FORMAT_PCM_32_BIT` | 256 | `AUDIO_DEVICE_OUT_SPEAKER` | active speaker-capable mixer |
| `AudioOut_15` | MIXER | 48000 Hz | `AUDIO_FORMAT_PCM_32_BIT` | 2048 | `AUDIO_DEVICE_OUT_SPEAKER` | speaker/deep-buffer family |
| `AudioOut_1D` | MIXER | 48000 Hz | `AUDIO_FORMAT_PCM_32_BIT` | 1024 | `AUDIO_DEVICE_OUT_TELEPHONY_TX` | modem/telephony TX route |

Selected-device behavior:
- `AUDIO_STREAM_VOICE_CALL` selected `AUDIO_DEVICE_OUT_EARPIECE` in policy
  strategy even though physical receiver separation is not proven.
- Most media, alarm, notification, accessibility, game, assistant, and speaker
  transmitted strategies selected `AUDIO_DEVICE_OUT_SPEAKER`.
- Bluetooth SCO stream volume lists earpiece and BT SCO headset categories, but
  no connected Bluetooth device was active in the captured state.

Interpretation:
The runtime state does not contradict the static route graph. It shows the loaded
policy graph plus current connection/force-use state. The divergence is layer-
specific: static policy declares many routes; runtime currently selects speaker,
earpiece-class voice/SCO, and telephony TX surfaces; hardware confirms physical
speaker and denies visible 3.5 mm jack.

################################################################################
# 07.9 — AUDIO DEVICE XML ROUTE-CONTROL SURFACE
################################################################################

File:
- `/vendor/etc/audio_device.xml`
- Lines: 278
- Bytes: 12583
- SHA256: `44373295a89786c24b060b79be77f524514e9b242c2ec5fc61616ca55aa4866d`

Captured route/control names:
- `Audio_Speaker_class_Switch` = `CLASSAB`
- `headphone_output` turn-on / turn-off paths
- `Voice_Amp_Switch` On / Off
- `two_in_one_speaker_output` toggles `Speaker_Amp_Switch` and
  `Ext_Speaker_Amp_Switch`
- `speaker_output` toggles `Speaker_Amp_Switch`
- `headphoneSpeaker_output` toggles `Headset_Speaker_Amp_Switch`
- `maxim_speaker_output`
- `maxim_headphoneSpeaker_output`
- `ext_speaker_output` toggles `Ext_Speaker_Amp_Switch`
- `builtin_Mic_Mic1`
- `builtin_Mic_Mic1_Inverse`
- `builtin_Mic_Mic2`
- `builtin_Mic_Mic2_Inverse`
- `builtin_Mic_SingleMic`
- `builtin_Mic_DualMic`
- `builtin_Mic_BackMic`
- `builtin_Mic_BackMic_Inverse`
- `headset_mic_input`
- `Mic1TypeDCCECMDIFFMode`
- `Mic2TypeDCCECMDIFFMode`
- `Mic3TypeDCCECMDIFFMode`
- `Mic4TypeDCCECMDIFFMode`

Classification:
- Speaker route-control names are relevant and retained. [REQUIRED]
- Headphone/headset route-control names are retained as accessory/BSP route
  surfaces, not as physical 3.5 mm proof. [DECLARED] [OPEN_LEAD]
- Back-mic and dual-mic route-control names are retained; exact physical mapping
  remains open. [OPEN_LEAD]
- Voice amp route-control names remain relevant to call-audio behavior. [REQUIRED]

################################################################################
# 07.10 — EFFECTS CHAIN AND SOUNDFX INVENTORY
################################################################################

Effects declaration file:
- `/vendor/etc/audio_effects.xml`
- Lines: 106
- Bytes: 6235
- SHA256: `f1867f84bb71cbc16d4b405450e3c3eba14e2b2000defbc6c13eef81b1984fd2`

Declared libraries:
- `proxy` — `libeffectproxy.so`
- `some_fx_sw` — `lib_some_fx_sw.so`
- `some_fx_hw` — `lib_some_fx_hw.so`
- `pre_processing` — `libaudiopreprocessing.so`
- `bundle` — `libbundlewrapper.so`
- `reverb` — `libreverbwrapper.so`
- `visualizer` — `libvisualizer.so`
- `downmix` — `libdownmix.so`
- `loudness_enhancer` — `libldnhncr.so`
- `dynamics_processing` — `libdynproc.so`

Declared effect names:
- `agc`
- `aec`
- `ns`
- `bassboost`
- `virtualizer`
- `equalizer`
- `volume`
- `reverb_env_aux`
- `reverb_env_ins`
- `reverb_pre_aux`
- `reverb_pre_ins`
- `visualizer`
- `downmix`
- `loudness_enhancer`
- `dynamics_processing`
- repeated `aec`, `ns`, `agc` post/pre-processing entries

Pre/post-processing routing:
- Source `voice_communication` declares pre-processing effects.
- Stream `voice_call` declares post/pre effect handling.
- This makes the effects chain relevant to speakerphone, call, VoIP, and recorder
  behavior.

Vendor 32-bit soundfx library register:
| Path | Bytes | SHA256 |
|---|---|---|
| /vendor/lib/soundfx/libaudiopreprocessing.so | 24672 | fced404492f325b6e0dc98b874c445161fa2f457ed365a969082f546611322e7 |
| /vendor/lib/soundfx/libbundlewrapper.so | 71660 | 5af290892bf987adae79c41cdc5ce0557aba9e82f5e01f5949fce49d138680e5 |
| /vendor/lib/soundfx/libdownmix.so | 15440 | 1640ec8768ea8aa341408cb4cde3c5a1e959d56070ae03b5a2825c0349377e1e |
| /vendor/lib/soundfx/libdynproc.so | 46560 | 7ad4a0dcdfc0c076eebe82de5898f07362e37bb87722eef26b8b0dcb18306cd0 |
| /vendor/lib/soundfx/libeffectproxy.so | 15248 | 3fb529903563f7cefcb9029e66dbd69f956acc8709d003c2b5092ea04c643925 |
| /vendor/lib/soundfx/libldnhncr.so | 15576 | 036e87613c99c011a4dfaef723d949fdb15e8ef0b47fcf1db3e00cc88675f993 |
| /vendor/lib/soundfx/libreverbwrapper.so | 36944 | 752c073518a4ae0f7403b27c8e237ee11adaa37e9e63b899cb9de6c30bca26f3 |
| /vendor/lib/soundfx/libvisualizer.so | 28420 | 7ba7adfe12dcf8c295dae7efa30fec415c4c3e343213f035516168918cd44f02 |

Vendor 64-bit soundfx library register:
| Path | Bytes | SHA256 |
|---|---|---|
| /vendor/lib64/soundfx/libaudiopreprocessing.so | 37224 | dcf0da3275820b8f9bdde9612c5ca3d2371748002a14a08b86dd9cccc18ebbba |
| /vendor/lib64/soundfx/libbundlewrapper.so | 78768 | 819462341f19591081ae456fe1a16a6d75c42e58c42788085559b0296d242909 |
| /vendor/lib64/soundfx/libdownmix.so | 23624 | 20bb06c6a84dc8e63a7fc0dc537dd54bc48fb2c51f43f0ad66de343cb6ca4091 |
| /vendor/lib64/soundfx/libdynproc.so | 66528 | e4d665a0af772246d75bf3683d8c45c3197f63ed10c5c9ea0643f50789f36012 |
| /vendor/lib64/soundfx/libeffectproxy.so | 19544 | 22eb7fb783fd1cf11dfda56fdcdfae0a00b7568e09aee08aaafcacb9108ac5f4 |
| /vendor/lib64/soundfx/libldnhncr.so | 19640 | 06fb99c95d342150b314663788e6add06df7aacb71fbfc6a0d114727f1274858 |
| /vendor/lib64/soundfx/libreverbwrapper.so | 36848 | 6113e9e67505b477ca8f878e1a93502b0ef39af423690cde591eea4a11ce59d6 |
| /vendor/lib64/soundfx/libvisualizer.so | 19536 | e1dfad1719c8ec2b10c1cb8dc5e2d7ab5a3841f3e5b0a59805d0758fe2b05d0d |

Runtime process-map note:
The audio HAL process mapped 32-bit vendor soundfx libraries during Gate 07I,
including `libaudiopreprocessing.so`, `libdynproc.so`, `libldnhncr.so`,
`libdownmix.so`, `libvisualizer.so`, `libreverbwrapper.so`, and
`libbundlewrapper.so`. This confirms that the effects library set is not only
static inventory; it participates in the active HAL process address space.

################################################################################
# 07.11 — HAL / SOUNDFX / SERVICE BLOB INVENTORY
################################################################################

Vendor 32-bit HAL and sound-trigger libraries:
| Path | Bytes | SHA256 |
|---|---|---|
| /vendor/lib/hw/audio.bluetooth.default.so | 71584 | 3ae20675ed7cdbd54fa28875d3ac2c8bad46bf160a5ec5fb859727c9767a0d6c |
| /vendor/lib/hw/audio.primary.default.so | 19820 | ab623abe82f1b1f9d8424a0172f88a0f9a780cac585f2049bbd5fcb59572fca9 |
| /vendor/lib/hw/audio.primary.mt6765.so | 1948368 | d02514e529059095c1a39aa0bf0af9fe71fce44f11360f1654e4e16bfe86cc10 |
| /vendor/lib/hw/audio.r_submix.default.so | 37788 | da077262c3d2efc3c2b0ab5498399c42c535e10a25774e5d7fd1d665c4607ec5 |
| /vendor/lib/hw/audio.r_submix.mt6765.so | 55048 | 4e73b45a18f2812e035a383d3300eafd2fb0f81f31b24846f3eeff93038b97c1 |
| /vendor/lib/hw/audio.usb.default.so | 24328 | 0a536723eca21757ed96588f1bb0f1f13619517d3789bb7363eb42a805a4726a |
| /vendor/lib/hw/audio.usb.mt6765.so | 63488 | 947f0475c725f0610c87c3a720c4295c3db76eb472e371594bbd419ac08b7de3 |
| /vendor/lib/hw/audio_policy.stub.so | 15416 | 758dc1a49fd20483a174decc47536a6eac2cce65533dc5c8c2064e770eef5b75 |
| /vendor/lib/hw/sound_trigger.primary.mt6765.so | 54400 | 580edfeb9412da6c18246763402e803f0d63608b151955ddc8358e578bf4b92c |

Vendor 64-bit HAL and sound-trigger libraries:
| Path | Bytes | SHA256 |
|---|---|---|
| /vendor/lib64/hw/audio.bluetooth.default.so | 92864 | c8f5e6bdc5e18a1b7c4c39394ff98ee16143e2ecc67b9b2458b29d3af59d8101 |
| /vendor/lib64/hw/audio.primary.default.so | 15584 | 303b8d7f6494ee865a7ea7699348ffbc5bb0922fb78be25fdff4681ead5546ef |
| /vendor/lib64/hw/audio.primary.mt6765.so | 2042304 | 2343e25a161103c2bd1c90b711461a7ee7843de4c54447eb21b086a2f0bd0bc5 |
| /vendor/lib64/hw/audio.r_submix.default.so | 41960 | 98d9815f90d5d3859c8103091e9a1c4dcf2581fb27571c42e68f56599cff0bf6 |
| /vendor/lib64/hw/audio.r_submix.mt6765.so | 50176 | 9892838d224a5757a93bea27d517adfedb0fd8872878d4c931e8572e7294c388 |
| /vendor/lib64/hw/audio.usb.default.so | 28416 | 6a70ec052af08504a6a08ad0677b231c8f8557b931c2b6a4870bc65c3a6ca498 |
| /vendor/lib64/hw/audio.usb.mt6765.so | 62968 | b6754ae89ca8bcb80378d80c06ba166ac54c142671c5e3c01454cde5faf41c7b |
| /vendor/lib64/hw/audio_policy.stub.so | 15424 | 774cc0909307949816794df94848929c6a10592f2850224dee954ec753abc159 |
| /vendor/lib64/hw/sound_trigger.primary.mt6765.so | 49512 | a648dfeab026c76f612b35cf6b9559ff5e62fc23f3442c393d4f52b036753aa6 |

Audio service binary and init RC files:
| Path | Lines | Bytes | SHA256 | Classification |
|---|---:|---:|---|---|
| `/vendor/bin/hw/android.hardware.audio@5.0-service-mediatek` | 30 | 15472 | `4b9217f439be7f77d6887d84a008de749da96e64a5ba8ce71f033a655d244da2` | active HAL service binary |
| `/vendor/etc/init/android.hardware.audio@5.0-service-mediatek.rc` | 15 | 788 | `b162ea7de962ced35584459e49daba6bebcbaa0d0ad510bc546ac9c5f5b9b26e` | HAL init declaration |
| `/vendor/etc/init/audiocmdservice_atci.rc` | 8 | 181 | `4c38c958052e71915cb5e35037faf3b44c13a5332ecf65f2975d5f48c3621750` | audio ATCI declaration |

################################################################################
# 07.12 — AUDIO HAL PROCESS MAP AND MODEM AUDIO FILE DESCRIPTORS
################################################################################

Audioserver PID 544:
- Command: `/system/bin/audioserver`
- Domain: `u:r:audioserver:s0`
- UID: 1041
- GID: 1005
- Threads: 21
- Open fds include `/dev/hwbinder`, `/dev/binder`, `/dev/pmsg0`, ashmem, sockets,
  and foreground stune task control.
- Maps framework audio policy, audioflinger, audio HAL client libraries, AAudio,
  effects config, HIDL audio interface libraries, and MTK system-side audio
  processing libraries such as:
  - `/system/lib64/libbessound_hd_mtk.so`
  - `/system/lib64/libmtklimiter.so`
  - `/system/lib64/libmtkshifter.so`
  - `/system/lib64/libaudiocompensationfilter.so`
  - `/system/lib64/libaudio_param_parser-sys.so`
  - `/system/lib64/vendor.mediatek.hardware.audio@5.1.so`

Audio HAL PID 516:
- Command: `/vendor/bin/hw/android.hardware.audio@5.0-service-mediatek`
- Domain: `u:r:mtk_hal_audio:s0`
- UID: 1041
- GID: 1005
- Threads: 12
- Open fds include:
  - `/dev/snd/controlC0`
  - `/dev/ccci_aud`
  - `/dev/ccci_raw_audio`
  - `/dev/vndbinder`
  - `/dev/hwbinder`
  - `/dev/pmsg0`
  - ashmem
  - foreground stune task control

Runtime-mapped vendor HAL family:
- `/vendor/lib/hw/audio.primary.mt6765.so`
- `/vendor/lib/hw/audio.usb.mt6765.so`
- `/vendor/lib/hw/audio.r_submix.mt6765.so`
- `/vendor/lib/hw/audio.bluetooth.default.so`
- `/vendor/lib/hw/android.hardware.audio@5.0-impl-mediatek.so`
- `/vendor/lib/hw/android.hardware.audio.effect@5.0-impl.so`
- `/vendor/lib/hw/android.hardware.bluetooth.audio@2.0-impl-mediatek.so`

Runtime-mapped vendor audio support family:
- `/vendor/lib/libaudio_param_parser-vnd.so`
- `/vendor/lib/libaudioprimarydevicehalifclient.so`
- `/vendor/lib/libaudiocustparam_vendor.so`
- `/vendor/lib/libaudiocomponentengine_vendor.so`
- `/vendor/lib/libaudiocompensationfilter_vendor.so`
- `/vendor/lib/libaudiocompensationfilterc.so`
- `/vendor/lib/libaudiodcrflt_vendor.so`
- `/vendor/lib/libaudioloudc.so`
- `/vendor/lib/libaudiofmtconv.so`
- `/vendor/lib/libaudiotoolkit_vendor.so`
- `/vendor/lib/libalsautils.so`
- `/vendor/lib/libbessound_hd_mtk_vendor.so`
- `/vendor/lib/lib_speech_enh.so`
- `/vendor/lib/libMtkSpeechEnh.so`
- `/vendor/lib/libbluetooth_audio_session_mediatek.so`
- `/vendor/lib/libmsbc_mtk.so`
- `/vendor/lib/libcvsd_mtk.so`
- `/vendor/lib/libwebrtc_audio_preprocessing.so`

Classification:
- `/dev/snd/controlC0` fd proves the live HAL owns ALSA control. [ACTIVE_RUNTIME]
- `/dev/ccci_aud` and `/dev/ccci_raw_audio` fds prove live modem/audio channel
  integration. [ACTIVE_RUNTIME]
- `ccci_raw_audio` is also mapped shared memory in the HAL process. [ACTIVE_RUNTIME]
- Cellular voice audio is therefore not merely policy-declared; it is live HAL ↔
  CCCI integration. [VERIFIED]

################################################################################
# 07.13 — ALSA CARD, /DEV/SND, AND PCM INVENTORY
################################################################################

ALSA card:
```text
0 [mtsndcard      ]: mt-snd-card - mt-snd-card
                      mt-snd-card
```

ALSA driver version:
```text
Advanced Linux Sound Architecture Driver Version k4.9.190+.
```

`/dev/snd` device-node boundary:
- Directory label: `u:object_r:audio_device:s0`
- Device-node owner/group: `system:audio`
- Major/minor family: ALSA major 116 plus sequencer/timer nodes.
- `controlC0`, PCM playback/capture nodes, sequencer nodes, and timer were
  present in Gate 07F.

PCM inventory:
| PCM | Name | Direction |
|---|---|---|
| 00-00 | MultiMedia1_PLayback | playback |
| 00-01 | MultiMedia1_Capture | capture |
| 00-02 | Voice_MD1_PLayback | playback+capture |
| 00-03 | ULDL_Loopback | playback+capture |
| 00-04 | I2S0_PLayback | playback |
| 00-05 | MRGRX_PLayback | playback+capture |
| 00-06 | MRGRX_CAPTURE | playback+capture |
| 00-07 | I2S0DL1_PLayback | playback |
| 00-08 | Deep_Buffer_PLayback | playback |
| 00-09 | DL1_AWB_Record | capture |
| 00-10 | Voice_MD1_BT_Playback | playback |
| 00-11 | VOIP_Call_BT_Playback | playback |
| 00-12 | VOIP_Call_BT_Capture | capture |
| 00-13 | TDM_Debug_Record | capture |
| 00-14 | FM_MRGTX_Playback | playback |
| 00-15 | MultiMediaData2_Capture | capture |
| 00-16 | I2S0AWB_Capture | capture |
| 00-17 | Voice_MD2_PLayback | playback+capture |
| 00-18 | MultiMedia_Routing | playback+capture |
| 00-19 | Voice_MD2_BT_Playback | playback |
| 00-20 | HP_IMPEDANCE_Playback | playback |
| 00-21 | FM_I2S_Playback | playback |
| 00-22 | FM_I2S_Capture | capture |
| 00-23 | MultiMedia2_PLayback | playback |
| 00-24 | MultiMedia3_PLayback | playback |
| 00-25 | ANC_Record | capture |
| 00-26 | Voice_USB_PLayback | playback+capture |
| 00-27 | Voice_USB_EchoRef | playback |
| 00-28 | BTCVSD | playback+capture |
| 00-29 | Headphone_PLayback | playback+capture |
| 00-30 | Speaker_PLayback | playback+capture |
| 00-31 | I2S2ADC2_Capture | capture |

Kernel PCM classification notes:
- `Speaker_PLayback` and `Headphone_PLayback` are present in `/proc/asound/pcm`.
- Gate 07M did not find literal platform directories named
  `/sys/devices/platform/11220000.audio/Speaker_PLayback` or
  `/sys/devices/platform/11220000.audio/Headphone_PLayback`; corresponding sysfs
  surfaces appear under `ext_Speaker_Multimedia` and `ext_Headphone_Multimedia`.
- PCM names are kernel route-surface names, not standalone proof of physical
  endpoints.

################################################################################
# 07.14 — PLATFORM AUDIO SYSFS AND DAPM SURFACES
################################################################################

Platform roots captured:
- `/sys/devices/platform/audio_snd_card`
- `/sys/devices/platform/11220000.audio`
- `/sys/devices/platform/11221000.audio_sram`
- `/sys/devices/platform/18050000.mtk-btcvsd-snd`
- `/sys/devices/platform/snd-soc-dummy`
- `/sys/devices/platform/accdet`

Selected `11220000.audio` route surfaces captured:
- `HP_IMPEDANCE`
- `MRGRX`
- `MRGRXCAPTURE`
- `MultiMedia1`
- `MultiMedia_DL2`
- `MultiMedia_DL3`
- `I2S0OUTPUT`
- `I2S0DL1OUTPUT`
- `I2S0_AWB_CAPTURE`
- `I2S1_AWB_CAPTURE`
- `Voice_MD1`
- `Voice_MD2`
- `Voice_MD1_BT`
- `Voice_MD2_BT`
- `Voice_USB`
- `VOIP_CALL_BT_PLAYBACK`
- `BTCVSD`
- `FM_I2S_RX_Playback`
- `FM_MRG_TX`
- `ANC_RECORD`
- `PLATOFRM_CONTROL`
- `ext_Speaker_Multimedia`
- `ext_Headphone_Multimedia`

Gate 07M DAPM/codec observations:
| Surface | Presence | dapm_widget | codec_reg | pmdown_time | power/runtime_status | Classification |
|---|---|---|---|---:|---|---|
| `ext_Speaker_Multimedia` | present | empty | empty | 5000 | unsupported | external/speaker route surface |
| `ext_Headphone_Multimedia` | present | empty | empty | 5000 | unsupported | accessory/headphone-class surface, no 3.5 mm proof |
| `HP_IMPEDANCE` | present | `PM State: Standby` | empty | 5000 | unsupported | headphone-impedance surface, no 3.5 mm proof |
| `ANC_RECORD` | present | `PM State: Standby` | empty | 5000 | unsupported | ANC/record capture surface, physical mapping open |
| `FM_I2S_RX_Playback` | present | `PM State: Standby` | empty | 5000 | unsupported | FM route surface, RF path open |
| `FM_MRG_TX` | present | `PM State: Standby` | empty | 5000 | unsupported | FM/MRG route surface, RF path open |
| `Voice_USB` | present | `PM State: Standby` | empty | 5000 | unsupported | USB voice route surface |
| `Voice_MD1` | present | `PM State: Standby` | empty | 5000 | unsupported | modem voice route surface |
| `Voice_MD2` | present | `PM State: Standby` | empty | 5000 | unsupported | secondary modem route surface |
| `Voice_MD1_BT` | present | `PM State: Standby` | empty | 5000 | unsupported | BT voice route surface |
| `Voice_MD2_BT` | present | `PM State: Standby` | empty | 5000 | unsupported | BT voice route surface |
| `VOIP_CALL_BT_PLAYBACK` | present | `PM State: Standby` | empty | 5000 | unsupported | VoIP BT route surface |
| `BTCVSD` | present | `PM State: Standby` | empty | 5000 | unsupported | BT CVSD audio route surface |
| `MultiMedia1` / `MultiMedia_DL2` / `MultiMedia_DL3` | present | `PM State: Standby` | empty | 5000 | unsupported | multimedia route surfaces |
| `I2S0OUTPUT` / `I2S0DL1OUTPUT` | present | `PM State: Standby` | empty | 5000 | unsupported | I2S playback surfaces |
| `I2S0_AWB_CAPTURE` | present | `PM State: Standby` | empty | 5000 | unsupported | capture surface |
| `I2S1_AWB_CAPTURE` | present | empty | empty | 5000 | unsupported | capture surface |
| `MRGRX` / `MRGRXCAPTURE` | present | `PM State: Standby` | empty | 5000 | unsupported | MRG route surfaces |
| `PLATOFRM_CONTROL` | present | `PM State: Standby` | empty | 5000 | unsupported | platform control route surface |

################################################################################
# 07.15 — DEVICE TREE AUDIO NODES
################################################################################

Device-tree audio nodes captured:
| Node | Compatible | Name | Classification |
|---|---|---|---|
| `/proc/device-tree/accdet` | `mediatek,pmic-accdet` | `accdet` | PMIC accessory detect node |
| `/proc/device-tree/audio_snd_card` | `mediatek,audio_snd_card` | `audio_snd_card` | ALSA card declaration |
| `/proc/device-tree/audio@11220000` | `mediatek,audio syscon` | `audio` | audio syscon root; includes `mediatek,btcvsd_snd` reference |
| `/proc/device-tree/audio_sram@11221000` | `mediatek,audio_sram` | `audio_sram` | audio SRAM block |
| `/proc/device-tree/mtk-btcvsd-snd@18050000` | `mediatek,mtk-btcvsd-snd` | `mtk-btcvsd-snd` | Bluetooth CVSD audio engine |

Device-tree interpretation:
- DT confirms board/BSP audio root, audio SRAM, sound card, PMIC ACCDET, and
  BTCVSD engine surfaces.
- DT does not expose `mediatek,audio-routing` in the captured nodes.
- DT does not by itself prove physical headphone, dock, or headset hardware.

################################################################################
# 07.16 — ACCDET, HEADSET, DOCK, USB, AND 3.5 MM PHYSICAL BOUNDARY
################################################################################

ACCDET live sysfs:
```text
/sys/devices/platform/accdet
/sys/bus/platform/devices/accdet -> ../../../devices/platform/accdet
OF_NAME=accdet
OF_FULLNAME=/accdet
OF_COMPATIBLE_0=mediatek,pmic-accdet
MODALIAS=of:NaccdetT<NULL>Cmediatek,pmic-accdet
```

Physical observation boundary:
- Owner physical inspection: no visible physical 3.5 mm headset jack is present
  on the device.
- Gate 07F/07H: `/sys/class/switch` is absent.
- Gate 07K: ACCDET exposes a platform node and DT compatibility, not a visible
  switch-class headset insertion endpoint.

Classification:
| Surface | Status | Classification |
|---|---|---|
| `ACCDET` / `pmic-accdet` | present DT/platform node | [VERIFIED] BSP/PMIC accessory-detect infrastructure |
| 3.5 mm TRRS headset jack | no visible physical jack | [PHYSICAL_NEGATIVE] |
| `AUDIO_DEVICE_OUT_WIRED_HEADSET` | policy-declared | [DECLARED] [OPEN_LEAD] no 3.5 mm proof |
| `AUDIO_DEVICE_OUT_WIRED_HEADPHONE` | policy-declared | [DECLARED] [OPEN_LEAD] no 3.5 mm proof |
| `AUDIO_DEVICE_IN_WIRED_HEADSET` | policy-declared | [DECLARED] [OPEN_LEAD] no 3.5 mm proof |
| `HP_IMPEDANCE` | PCM/sysfs/param surface | [DECLARED] [OPEN_LEAD] accessory/headphone-class route surface |
| `ext_Headphone_Multimedia` | sysfs route surface | [DECLARED] [OPEN_LEAD] possible inherited or accessory route |
| `Analog Dock Headset` | policy-declared; force-use analog dock persisted | [DECLARED] [OPEN_LEAD] |
| `Digital Dock Headset` | policy-declared | [DECLARED] [OPEN_LEAD] |
| USB headset/device routes | policy-declared and USB PCM surfaces exist | [DECLARED] [OPEN_LEAD] likely external route family |

Resolution statement:
The C17S does not have a visible physical 3.5 mm jack. Headset/headphone/mic
policy routes are therefore not documented as a TRRS jack. They are retained as
inherited MTK accessory semantics that may be unused BSP residue, USB-C audio,
pogo/accessory cradle behavior, factory/diagnostic harness routing, or internal
board-level accessory mapping. The software routes must not be deleted from the
database simply because the physical jack is absent.

################################################################################
# 07.17 — AUDIO PARAMETER AND AURISYS INVENTORY
################################################################################

Count boundary:
- Strict `/vendor/etc/audio_param` direct-file count: 96.
- Wider audio tuning/config XML set count: 99.
- The 99-count set equals:
  - 96 direct files under `/vendor/etc/audio_param/`
  - `/vendor/etc/audio_em.xml`
  - `/vendor/etc/aurisys_config.xml`
  - `/vendor/etc/aurisys_param/Speech_AudioParam.xml`

This resolves the 99-versus-96 conflict. Both values are true at different
bucket boundaries.

Strict audio_param category counts:
| Prefix/category | Direct files |
|---|---|
| Playback | 15 |
| Record | 13 |
| Speech | 19 |
| VoIP | 22 |
| VoIPv2 | 9 |
| Volume | 6 |
| Ringback | 4 |
| BtInfo | 2 |
| HpImpedance | 2 |
| MicInfo | 2 |
| SoundEnhancement | 2 |
| USBCall | 2 |
| USBDevice | 2 |
| UplinkConfiguration | 2 |
| VOW | 2 |

Important count note:
`AudioParamOptions.xml` is a global option file inside `/vendor/etc/audio_param`.
It is included in the strict 96-file directory count and the wider 99-file tuning
set. It is not a prefix category in the category-count loop.

Audio parameter roster by category:

Playback:
- `/vendor/etc/audio_param/AudioParamOptions.xml`
- `/vendor/etc/audio_param/PlaybackACF_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackACF_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/PlaybackDRC_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackDRC_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/PlaybackHCF_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackHCF_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/PlaybackVolAna_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackVolAna_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/PlaybackVolDigi_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackVolDigi_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/PlaybackVolUI_AudioParam.xml`
- `/vendor/etc/audio_param/PlaybackVolUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/Playback_AudioParam.xml`
- `/vendor/etc/audio_param/Playback_ParamTreeView.xml`
- `/vendor/etc/audio_param/Playback_ParamUnitDesc.xml`

Record:
- `/vendor/etc/audio_param/RecordDMNR_AudioParam.xml`
- `/vendor/etc/audio_param/RecordDMNR_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/RecordFIR_AudioParam.xml`
- `/vendor/etc/audio_param/RecordFIR_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/RecordUI_AudioParam.xml`
- `/vendor/etc/audio_param/RecordUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/RecordVolUI_AudioParam.xml`
- `/vendor/etc/audio_param/RecordVolUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/RecordVol_AudioParam.xml`
- `/vendor/etc/audio_param/RecordVol_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/Record_AudioParam.xml`
- `/vendor/etc/audio_param/Record_ParamTreeView.xml`
- `/vendor/etc/audio_param/Record_ParamUnitDesc.xml`

Speech:
- `/vendor/etc/audio_param/SpeechDMNR_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechDMNR_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechEchoRef_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechEchoRef_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechGeneral_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechGeneral_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechMagiClarity_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechMagiClarity_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechNetwork_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechNetwork_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechUI_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechVolUI_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechVolUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/SpeechVol_AudioParam.xml`
- `/vendor/etc/audio_param/SpeechVol_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/Speech_AudioParam.xml`
- `/vendor/etc/audio_param/Speech_ParamTreeView.xml`
- `/vendor/etc/audio_param/Speech_ParamUnitDesc.xml`

VoIP:
- `/vendor/etc/audio_param/VoIPDMNR_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPDMNR_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPGeneral_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPGeneral_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPUI_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPVolUI_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPVolUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPVol_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPVol_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIP_AudioParam.xml`
- `/vendor/etc/audio_param/VoIP_ParamTreeView.xml`
- `/vendor/etc/audio_param/VoIP_ParamUnitDesc.xml`

VoIPv2:
- `/vendor/etc/audio_param/VoIPv2DMNR_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPv2DMNR_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPv2General_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPv2General_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPv2UI_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPv2UI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VoIPv2_AudioParam.xml`
- `/vendor/etc/audio_param/VoIPv2_ParamTreeView.xml`
- `/vendor/etc/audio_param/VoIPv2_ParamUnitDesc.xml`

Volume:
- `/vendor/etc/audio_param/VolumeGainMapUL_AudioParam.xml`
- `/vendor/etc/audio_param/VolumeGainMapUL_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/VolumeGainMap_AudioParam.xml`
- `/vendor/etc/audio_param/VolumeGainMap_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/Volume_AudioParam.xml`
- `/vendor/etc/audio_param/Volume_ParamUnitDesc.xml`

Ringback:
- `/vendor/etc/audio_param/RingbackVolUI_AudioParam.xml`
- `/vendor/etc/audio_param/RingbackVolUI_ParamUnitDesc.xml`
- `/vendor/etc/audio_param/RingbackVol_AudioParam.xml`
- `/vendor/etc/audio_param/RingbackVol_ParamUnitDesc.xml`

BtInfo:
- `/vendor/etc/audio_param/BtInfo_AudioParam.xml`
- `/vendor/etc/audio_param/BtInfo_ParamUnitDesc.xml`

HpImpedance:
- `/vendor/etc/audio_param/HpImpedance_AudioParam.xml`
- `/vendor/etc/audio_param/HpImpedance_ParamUnitDesc.xml`

MicInfo:
- `/vendor/etc/audio_param/MicInfo_AudioParam.xml`
- `/vendor/etc/audio_param/MicInfo_ParamUnitDesc.xml`

SoundEnhancement:
- `/vendor/etc/audio_param/SoundEnhancement_AudioParam.xml`
- `/vendor/etc/audio_param/SoundEnhancement_ParamUnitDesc.xml`

USBCall:
- `/vendor/etc/audio_param/USBCall_AudioParam.xml`
- `/vendor/etc/audio_param/USBCall_ParamUnitDesc.xml`

USBDevice:
- `/vendor/etc/audio_param/USBDevice_AudioParam.xml`
- `/vendor/etc/audio_param/USBDevice_ParamUnitDesc.xml`

UplinkConfiguration:
- `/vendor/etc/audio_param/UplinkConfiguration_AudioParam.xml`
- `/vendor/etc/audio_param/UplinkConfiguration_ParamUnitDesc.xml`

VOW:
- `/vendor/etc/audio_param/VOW_AudioParam.xml`
- `/vendor/etc/audio_param/VOW_ParamUnitDesc.xml`

Wider audio tuning files outside strict audio_param:
| Path | Lines | Bytes | SHA256 | Classification |
|---|---:|---:|---|---|
| `/vendor/etc/audio_em.xml` | 55 | 2702 | `94ded9c8c3f250cbdd35b736b45968e2434a61f6597a3ebc1c950257f4c13bb7` | engineering/audio tuning XML |
| `/vendor/etc/aurisys_config.xml` | 331 | 16772 | `a985b9507a2c207b3316189aead214c2ca6be949428657f4f13dcc0fb60314fe` | Aurisys scenario configuration |
| `/vendor/etc/aurisys_param/Speech_AudioParam.xml` | 400 | 57003 | `a90382b952ff692f68b20911dc91f91523182bdc0f877d7fab677131d986ed8d` | Aurisys speech parameter file |

Aurisys scenario/library boundary:
- `mtk_bessound`
- `mtk_speech_enh`
- `mtk_iir`
- normal record/playback scenarios
- `AURISYS_SCENARIO_VOIP`
- `AURISYS_SCENARIO_VOIP_WITHOUT_AEC`

Largest known audio_param file from capture:
- `/vendor/etc/audio_param/VoIPv2DMNR_ParamUnitDesc.xml`
- Bytes: 162277
- Lines: 896
- SHA256: `0d1749be67c5ac6e3c073f7169cb3b3be8b8f2ac7088a9ce2b995f3784fd5434`

Speech parameter duality:
- `/vendor/etc/audio_param/Speech_AudioParam.xml`
  - Bytes: 46795
  - Lines: 401
  - SHA256: `5b13758fec69ab7dbb2bc6cc1fc9d756b8682c119286b49f3408967fa3f4a325`
- `/vendor/etc/aurisys_param/Speech_AudioParam.xml`
  - Bytes: 57003
  - Lines: 400
  - SHA256: `a90382b952ff692f68b20911dc91f91523182bdc0f877d7fab677131d986ed8d`

These are separate files and must not be collapsed.

################################################################################
# 07.18 — ENDPOINT CLASSIFICATION TABLE
################################################################################
| Endpoint / route | Evidence | Classification | Section result |
|---|---|---|---|
| Speaker | policy default output; runtime selected; AudioFlinger speaker threads; ext speaker route; physical speaker present | [PHYSICAL_CONFIRMED] [ACTIVE_RUNTIME] | Primary user audible output. |
| Earpiece | policy attached and selected for voice-call strategy; no separate visible receiver confirmed | [DECLARED] [OPEN_LEAD] | Likely logical earpiece/voice route; do not assert independent receiver. |
| Built-In Mic | policy attached input; primary input source; parameter/mic route names exist | [DECLARED] [REQUIRED] | Physical mic expected; exact mapping not separated from back mic here. |
| Built-In Back Mic | policy attached input; back-mic route-control and MicInfo params exist | [DECLARED] [OPEN_LEAD] | Physical directionality and exact capsule mapping open. |
| Wired Headset | policy output route; no visible 3.5 mm jack | [DECLARED] [PHYSICAL_NEGATIVE for 3.5 mm] [OPEN_LEAD] | May be inherited BSP, USB-C/pogo/accessory, or unused fallback. |
| Wired Headphones | policy output route; no visible 3.5 mm jack | [DECLARED] [PHYSICAL_NEGATIVE for 3.5 mm] [OPEN_LEAD] | Same boundary as wired headset. |
| Wired Headset Mic | policy input route; no visible 3.5 mm jack | [DECLARED] [PHYSICAL_NEGATIVE for 3.5 mm] [OPEN_LEAD] | May map to accessory/USB/pogo or be BSP residue. |
| USB Device/Headset Out/In | USB policy module; Voice_USB PCM; USB period property | [DECLARED] [OPEN_LEAD] | Most plausible external headset route family, but not proven as wired-headset alias. |
| BT SCO / SCO Headset / SCO Car Kit | policy routes; BTCVSD/BT voice PCMs; BT audio HAL registered | [DECLARED] [REQUIRED] | Required for call/headset audio behavior. |
| BT A2DP Out/In | Bluetooth/A2DP policy files and HAL providers registered | [DECLARED] [REQUIRED] | Required for media Bluetooth route family. |
| BT Hearing Aid | Bluetooth Audio HAL policy route | [DECLARED] | No active connected device in capture. |
| Telephony Tx | policy output; AudioFlinger output thread; HAL fd to CCCI devices | [ACTIVE_RUNTIME] [REQUIRED] | Modem uplink path is live. |
| Voice Call In | policy input; voice route source; internal telephony RX/call path | [DECLARED] [REQUIRED] | Call audio path surface. |
| Echo Ref In | policy input; FM tuner input source; voice USB echo-ref PCM exists | [DECLARED] [OPEN_LEAD] | Echo-ref route surface; use-case validation needed. |
| FM Tuner In/Out | policy routes and FM PCM surfaces exist | [DECLARED] [OPEN_LEAD] | RF path not proven here. |
| ANC_Record | PCM and sysfs surface exists | [DECLARED] [OPEN_LEAD] | Physical ANC mic/function not proven. |
| AUX Digital / HDMI | policy/legacy declarations | [BSP_GHOST] [PHYSICAL_NEGATIVE HDMI] | No HDMI hardware on device. |
| Analog/Digital Dock | policy routes; force-use analog dock persists | [DECLARED] [OPEN_LEAD] | Could be pogo/accessory/legacy route; physical mapping open. |
| Remote Submix | r_submix policy/HAL routes | [DECLARED] | Virtual route, not physical hardware. |

################################################################################
# 07.19 — LAYER-DIVERGENCE MODEL AND CONFLICT RESOLUTION
################################################################################

The audio stack produces apparently different results because each command reads
a different layer. The final database model is:

| Layer | Example source | What it proves | What it does not prove |
|---|---|---|---|
| Static policy | `audio_policy_configuration.xml` | Declared modules, ports, routes, attached devices | Current selected route or physical endpoint |
| Legacy policy | `audio_policy.conf` | Inherited route family retained in vendor image | Active policy source by itself |
| audio_device XML | `audio_device.xml` | MTK route-control naming and kcontrol-style path names | Physical population of every path |
| Runtime policy | `dumpsys media.audio_policy` | Loaded policy source, available devices, selected devices, active patches | Physical endpoint if software route is a ghost |
| AudioFlinger | `dumpsys media.audio_flinger` | Output/input threads, HAL format, selected output device | Static config completeness |
| ALSA | `/proc/asound/pcm`, `/dev/snd` | Kernel PCM route surface and ALSA node exposure | Physical jack/speaker/mic proof by name alone |
| HAL maps/fds | `/proc/<pid>/maps`, `/proc/<pid>/fd` | Loaded blobs and open device/modem nodes | Complete user-visible behavior |
| DT/sysfs | `/proc/device-tree`, `/sys/devices/platform` | Board/BSP node presence | Physical external connector mapping unless linked |
| Physical observation | device inspection | Absence/presence of visible physical endpoints | Internal electrical reuse without trace/test |

Resolved conflicts:
1. `audio_device.xml` and `audio_policy.conf` are present, not absent.
2. `audio_effects.conf` is absent; `audio_effects.xml` is present.
3. Strict audio_param count is 96; wider audio tuning set is 99.
4. Wired-headset routes exist, but no visible physical 3.5 mm jack exists.
5. ACCDET exists, but does not prove a TRRS jack.
6. Earpiece is runtime-selected for voice-call strategy, but independent physical
   receiver hardware is not asserted.
7. Dock force-use exists, but physical dock/pogo mapping remains open.

################################################################################
# 07.20 — REQUIRED DATABASE CARRY / STRIP / OPEN DISPOSITION
################################################################################

This is a database disposition table, not a build recipe.

| Item family | Disposition | Reason |
|---|---|---|
| `android.hardware.audio@5.0-service-mediatek` service binary and RC | KEEP / REQUIRED | Primary active audio HAL service. |
| `audio.primary.mt6765.so` | KEEP / REQUIRED | Primary active vendor audio HAL. |
| `audio.usb.mt6765.so` / USB policy | KEEP / REQUIRED | USB audio/voice route surfaces exist. |
| `audio.r_submix.mt6765.so` / r_submix policy | KEEP unless virtual-route policy is deliberately rewritten | Virtual route family present. |
| `audio.bluetooth.default.so` / Bluetooth Audio HAL impl | KEEP / REQUIRED | BT SCO/A2DP/BT audio provider surface. |
| `sound_trigger.primary.mt6765.so` | KEEP pending sound-trigger validation | Present in HAL blob set; soundtrigger service exists. |
| `/vendor/etc/audio_policy_configuration.xml` | KEEP / REQUIRED | Runtime AudioPolicyManager config source. |
| `/vendor/etc/audio_policy.conf` | KEEP / REVIEW | Legacy residue, possible vendor fallback; not absent. |
| `/vendor/etc/audio_device.xml` | KEEP / REQUIRED | MTK route-control paths. |
| `/vendor/etc/audio_effects.xml` and soundfx libs | KEEP / REQUIRED | Effects chain and mapped HAL libraries. |
| `/vendor/etc/audio_param` 96 files | KEEP / REQUIRED | MTK parameter/tuning set. |
| `audio_em.xml`, `aurisys_config.xml`, `aurisys_param/Speech_AudioParam.xml` | KEEP / REQUIRED | Wider 99-file audio tuning set. |
| Wired-headset/headphone policy routes | KEEP / OPEN | No 3.5 mm jack, but possible USB-C/pogo/accessory or inherited route. |
| HDMI/AUX digital routes | MARK BSP_GHOST unless later proof appears | No HDMI hardware confirmed. |
| FM routes | KEEP / OPEN | Audio route exists; RF path not proven. |
| ANC/back mic routes | KEEP / OPEN | Capture surfaces exist; physical mapping open. |
| ATCI audio daemon | KEEP / CONTROLLED | Declared disabled oneshot diagnostic socket surface. |

################################################################################
# 07.21 — OPEN LEADS
################################################################################

Open leads preserved by this section:

1. USB-C / pogo / accessory mapping for headset-class routes.
   - No visible 3.5 mm jack exists.
   - Wired-headset/headphone/mic policy routes exist.
   - USB headset/device routes exist.
   - ACCDET and dock force-use exist.
   - Exact external mapping requires connection-state testing or board trace.

2. Earpiece physical implementation.
   - Earpiece is policy-declared and runtime-selected for voice-call strategy.
   - No independent physical receiver is asserted by this section.
   - It may be a logical route into the speaker path or an unobserved internal
     receiver path; physical proof is not captured.

3. Back microphone / dual microphone / ANC mapping.
   - Built-in back mic, mic inverse paths, dual-mic paths, and ANC_Record exist.
   - Exact physical capsule and directionality remain unproven.

4. FM RF path.
   - FM audio policy, FM PCM, and FM route surfaces exist.
   - This section does not prove FM antenna/RF functionality.

5. Dock route physical target.
   - Force-use analog dock is present from persisted settings.
   - Analog and digital dock headset routes are declared.
   - Physical dock/pogo/accessory target remains open.

6. ATCI audio diagnostic activation path.
   - RC declares disabled oneshot audio-daemon with atci-audio socket.
   - No audio-daemon process was live in capture.
   - rild-atci and ATCI boot-complete property exist.

################################################################################
# 07.22 — FINAL SECTION 07 CLOSURE STATEMENT
################################################################################

Section 07 is closed as the canonical software audio authority for the current
C17S software digital twin.

The active framework audio service is `audioserver`, and the active vendor HAL is
`android.hardware.audio@5.0-service-mediatek` registered as
`vendor.audio-hal-2-0`. The live HAL publishes audio, audio effects, and
Bluetooth audio HIDL services through PID 516 and opens ALSA control plus CCCI
modem audio devices. The loaded runtime policy source is
`/vendor/etc/audio_policy_configuration.xml`. The modern policy XML, legacy
`audio_policy.conf`, `audio_device.xml`, effects XML, volume XMLs, included USB,
Bluetooth, A2DP, and remote-submix policies, strict 96-file audio_param directory,
and wider 99-file audio tuning set are captured and classified.

The ALSA substrate is card0 `mtsndcard`, exposing PCM 00-00 through 00-31 and the
expected MTK multimedia, modem voice, BT, USB, FM, ANC, speaker, and headphone
route surfaces. Speaker output is active and physical. Cellular voice integration
is active through Telephony Tx and the HAL file descriptors to `/dev/ccci_aud`
and `/dev/ccci_raw_audio`.

The C17S has no visible physical 3.5 mm headset jack. Wired headset/headphone/mic
routes remain declared software/accessory routes, not physical TRRS proof. They
are retained because they may represent inherited MTK accessory semantics, USB-C
or pogo/accessory routing, factory/diagnostic harness behavior, or unused BSP
fallback. HDMI/AUX digital routes are BSP ghost candidates because no HDMI
hardware is confirmed. FM, ANC, back-mic, earpiece physical implementation, dock,
and USB/pogo headset mapping are preserved as classified open leads rather than
unverified assertions.

All Section 07 scope items are closed to the database standard: every route,
file family, runtime process, HAL surface, parameter bucket, kernel PCM surface,
and physical boundary is classified in its correct evidence layer.

################################################################################
# END SECTION 07 — AUDIO STACK — POLICY, HAL, PARAMETERS & ROUTES
################################################################################
