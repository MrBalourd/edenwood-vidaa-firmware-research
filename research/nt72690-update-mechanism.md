# NT72690 update-chain analysis from public sources

Documentary, static analysis only. No TV was accessed, no script from the repository was executed, and no firmware was downloaded or flashed.

## 1. Repository provenance

| Field | Value |
| --- | --- |
| Repository | `https://github.com/opensource-hisense/SmartTV-SeriesVidaaU7_NT` |
| Branch | `main` |
| Commit analyzed | `006eb7d5ddd809ff0608edca91d62f86e032b388` ("Update README.md with new models", 2026-10-22 14:27:59 +0800) |
| Retrieved | 2026-10-24, shallow clone into `/tmp/vidaau7-nt-audit/repo` (outside the project folder) |
| Size | ~283,000 files, 5.3 GB; 149 directories under `oss/` |

Paths below are relative to the repository root. Line numbers refer to the commit above.

## 2. Inventory — what is actually in the repository

The `oss/` tree splits into `oss/part1` and `oss/part2`. Measured sizes of the largest components: `cobalt` (~2.1 GB), `boost` (~765 MB), `directfb` (~161 MB), `openssl` (~133 MB). Each component carries an `OSS_INFO.txt` metadata file, an `MTK_HOW_TO_INSTALL.txt`, a `Makefile`, and a `NOTICE.txt`.

### Critical negative finding: kernel and U-Boot sources are absent

- `oss/part2/kernel/` contains only a `Makefile` and `OSS_INFO.<version>.txt` files for versions 3.0, 3.4, 3.10, 3.18, 4.4, 4.9, 4.19. There is no kernel source (no `.c` files; directory is 32 KB).
- `oss/part2/uboot/` contains only `NOTICE.txt` and `OSS_INFO.txt` (24 KB, no source).
- `oss/part2/kernel/OSS_INFO.4.19.txt`: `NAME=linux kernel`, `VERSION=4.19`, `SOURCE=apollo/linux_core/kernel/linux-4.19`, `USAGE=Kernel`.
- `oss/part2/uboot/OSS_INFO.txt`: `NAME=u-boot-2011.12`, `USAGE=Static link`, `COMMENT= the uboot will load the linux kerenl to dram,and jump to run .`

So despite the repository being a firmware-source release, the boot-critical components are metadata-only entries pointing at Hisense-internal paths (`apollo/linux_core/...`). Their roles are documented in these metadata files and in code comments (below), but their implementation cannot be audited from this repository.

### Android-style verified-boot toolset — present with source

The following components are present with full source and are explicitly described as serving verified boot (`COMMENT=library and binary utils for android verify boot` in each `OSS_INFO.txt`):

- `oss/part2/avb` — libavb 2.0, from Android `external/avb` (`avb/libavb`, ~40 source files).
- `oss/part2/fs_mgr` — from Android `system/core/fs_mgr`, with `fs_mgr_avb.cpp`, `fs_mgr_avb_ops.cpp`, `fs_mgr_verity.cpp`.
- `oss/part2/libfec`, `oss/part2/ext4_utils`, `oss/part2/squashfs_util`, `oss/part2/libcrypto_utils`, `oss/part2/libcutils` — same "android verify boot" comment in their `OSS_INFO.txt`.
- `oss/part2/mdroid` — Android binder-based IPC, versions 2.3–8.0 (`COMMENT= Android binder using for inter process communication`).

### Novatek-specific modifications

`oss/part2/directfb` is Novatek-modified (files such as `inputdrivers/mikeypad/mikeypad.c`, `systems/mstartlb/mstartlb.c`, `systems/ion/ion_system.c` do not exist upstream). This is where the boot-time interaction with MBoot is visible.

## 3. Search for update-mechanism terms

Searched with `rg` across `oss/` (excluding the large unrelated `cobalt`/`boost` trees where noted):

| Term | Result |
| --- | --- |
| `usb_NOVATEK_DEV_EU.bin` | 0 hits |
| `NOVATEK_72690` | 0 hits |
| OTA / `ota_update` / `checkNewVersion` / `upgradeplc` / `ota-tv` | only unrelated generic hits (e.g. `oss/part2/libwebsockets/2.2.0/plugins/protocol_esp32_lws_ota.c` — an ESP32 example; Android UID constants in `mdroid`) |
| `mboot` (word, case-insensitive) | only in Novatek-modified DirectFB comments (§5) |
| `recovery` | no VIDAA recovery/updater implementation; only inherited Android utility code |
| `vbmeta`, `by-name`, `mmcblk` | present in `fs_mgr`/`avb` code and comments |

**Conclusion of the search:** the Hisense VIDAA updater application, the OTA client, and the USB upgrade handler are proprietary and absent. What the repository documents is the *mount-and-verify* layer that runs at boot after an image is already in eMMC, plus the per-project configuration mechanism.

## 4. Verified-boot evidence (directly observed code)

### AVB slot verification

- `oss/part2/avb/libavb/avb_slot_verify.h:164-171` — documents A/B slots: `|ab_suffix|` is `""`, `"_a"`, or `"_b"`; `vbmeta_images[0]` comes from the top-level `vbmeta` partition or `boot`.
- `oss/part2/avb/libavb/avb_slot_verify.h:185-192` — rollback-index handling: `avb_slot_verify()` never writes `stored_rollback_index`; the caller does.
- `oss/part2/avb/Makefile` — builds `libavb.a` (library only; no `avbtool` host tool is shipped here).

### Userspace mount-time verification

- `oss/part2/fs_mgr/fs_mgr/fs_mgr_avb.cpp:435` `hashtree_dm_verity_setup()`:
  1. opens `/dev/mapper/control`;
  2. creates a device-mapper verity device for the mount point;
  3. builds a verity table from the AVB `AvbHashtreeDescriptor` (salt, root digest);
  4. loads and resumes the verity table;
  5. **marks the underlying block device read-only** (`fs_mgr_set_blk_ro`, `fs_mgr_avb.cpp:490`).
- `oss/part2/fs_mgr/fs_mgr/fs_mgr_avb.cpp:194` `verify_vbmeta_digest()` — hashes all verified vbmeta images; the surrounding code (lines 211-232) matches `androidboot.vbmeta.hash_alg/size/digest` kernel-cmdline values.
- `oss/part2/fs_mgr/fs_mgr/fs_mgr_avb_ops.cpp:86` `read_from_partition()`; lines 168-175 build a by-name symlink map from an fstab (example in comments: `/dev/block/platform/soc.0/7824900.sdhci/by-name/system_a`), i.e. eMMC partitions addressed by name with A/B suffixes.
- `oss/part2/fs_mgr/fs_mgr/fs_mgr_verity.cpp:745` `fs_mgr_setup_verity()` — legacy path: `fec_open()` on the block device, `fec_verity_get_metadata()`, RSA signature check on the verity table (`verify_verity_signature`, using `crypto_utils/android_pubkey.h` and OpenSSL RSA/SHA — includes at `fs_mgr_verity.cpp:30-32`), default `VERITY_MODE_EIO`, and `fs_mgr_is_device_unlocked()` escape hatches.
- `oss/part2/squashfs_util/squashfs_utils/squashfs_utils.c:30-53` — parses the squashfs super block (`SQUASHFS_MAGIC 0x73717368`) for read-only squashfs system images.

### Who consumes this: `mtk_mount`

- `oss/part2/avb/MTK_HOW_TO_INSTALL.txt`: "only the mtk_util, mtk_mount is exported to the platform", with install order libavb → libcutils → ext4_utils → libcrypto_utils → squashfs_utils → libfec → fs_mgr → mtk_util.
- `mtk_util`/`mtk_mount` source is **not** in the repository (verified by search). The tool that actually invokes AVB/verity at mount time is proprietary.

## 5. MBoot and per-project configuration (directly observed)

From the Novatek-modified DirectFB sources:

- `oss/part2/directfb/directfb_1.4.2/1.4.2/inputdrivers/mikeypad/mikeypad.c:97-101`:
  ```c
  /// The path to read boot cmd from Mboot
  #define CMD_LINE_PATH               "/proc/cmdline"
  #define ENV_PROJECT_ID              "cusdata_projectid"
  #define DATA_CONFIG_INI_PATH        "/cusdata/config/dataIndex/"
  ```
- `mikeypad.c:692-720` `getKeypadCodeFilePath()`: parses `cusdata_projectid` out of `/proc/cmdline`, then loads `/cusdata/config/dataIndex/dataIndex_<projectid>.ini` (falling back to `dataIndex_1.ini` on odd values) and reads its `Keypad`→`m_pKeypad_File` entry.
- `oss/part2/directfb/directfb_1.4.2/1.4.2/src/misc/conf.h:392`: `mbootGOPIndex` — "gop index show logo in mboot"; `oss/part2/directfb/directfb_1.4.2/1.4.2/systems/mstartlb/mstartlb.c:248` and `oss/part2/directfb/directfb_1.4.2/1.4.2/systems/ion/ion_system.c:306`: comments "reset gop which initianlize in mboot".

What this establishes:

1. MBoot initializes the display (GOP, boot logo) and hands over to the Linux kernel.
2. MBoot injects a kernel command line containing `cusdata_projectid=<id>`; userland reads it from `/proc/cmdline`.
3. A writable `/cusdata` area holds per-project `config/dataIndex/*.ini` files; Project ID selects the runtime configuration.

## 6. Reconstructed chain (textual diagram)

Only the boxed stages are supported by code or metadata in this repository; everything else is inference labeled as such.

```
eMMC partitions (by-name, A/B suffixes _a/_b: vbmeta, boot, system, ...)
      |
Boot ROM -> MBoot (Novatek; inits eMMC + GOP logo; sets kernel cmdline
      |      incl. cusdata_projectid)          [metadata + comments only]
      v
U-Boot 2011.12 ("loads the linux kernel to dram, and jump to run")
      |                                       [metadata only]
      v
Linux kernel 4.19 (U7 release)               [metadata only]
      v
VIDAA userland, incl. mtk_util/mtk_mount    [proprietary, NOT in repo]
      |
      +-- avb_slot_verify() on vbmeta (_a/_b), RSA sigs, rollback indexes
      |         [code in oss/part2/avb]
      +-- hashtree dm-verity setup, block dev set read-only
      |         [code in oss/part2/fs_mgr]
      +-- mount squashfs / ext4(+fec) system images
      |         [code in squashfs_util / fs_mgr_verity]
      +-- read cusdata_projectid from /proc/cmdline -> /cusdata/config/...
                [code in oss/part2/directfb mikeypad.c]

OTA download / USB upgrade / package extraction / signature keys:
      NOT in this repository (proprietary)
```

## 7. Answers to the six questions

1. **Boot and loading:** MBoot → U-Boot 2011.12 → Linux 4.19. Roles documented in `OSS_INFO` metadata and DirectFB comments; sources absent, so the loading process cannot be audited here.
2. **Update download/apply components:** none in the repository. The VIDAA OTA client and the USB upgrade handler (the `usb_NOVATEK_DEV_EU.bin` flow from the Hisense NT72690 service manual, see `public-sources.md` §5) are proprietary.
3. **Roles:** MBoot owns early boot, eMMC access, and logo display; the eMMC stores A/B by-name partitions verified by AVB/dm-verity at mount time via the absent `mtk_mount`. No update service or script is present.
4. **Visible integrity steps:** AVB vbmeta RSA signature verification with rollback indexes (`avb_slot_verify.h:139-192`), device-mapper hashtree verity (`fs_mgr_avb.cpp:435-492`), read-only marking of verified block devices, legacy verity-table RSA verification (`fs_mgr_verity.cpp:745`+), squashfs super-block parsing. Unlocked-device escape hatches exist in the Hisense code (`fs_mgr_is_device_unlocked()`), but their activation on any given TV is unknown.
5. **Service-manual file names:** `NOVATEK_72690_EU_2K_...tar.gz` and `usb_NOVATEK_DEV_EU.bin` do not appear anywhere in the repository. The USB upgrade path is executed below/aside from this codebase (likely MBoot/factory tooling) and remains documented only by the manual.
6. **Necessarily unknown:** the OTA check request format, the package download/extraction pipeline, the signing keys, the exact USB `.bin` container format, the partition table layout, and everything Edenwood-specific.

### USB upgrade vs OTA — they are distinct

- **USB upgrade** (service manual): a manually supplied file (`usb_NOVATEK_DEV_EU.bin` fallback name) processed by boot-time/factory tooling. Not represented in this repository.
- **OTA** (VIDAA): network check + download + apply by a proprietary client. Also not represented here.
- **Shared (inferred):** both end up writing images into the eMMC partitions that `mtk_mount`+AVB/dm-verity then verify at mount. The repository documents this common verify layer for Hisense builds; applying it to Edenwood is a hypothesis below.

## 8. Relation to the Edenwood ED50EA03UHD-EL

| Observation | Status |
| --- | --- |
| `cusdata_projectid` selects per-project runtime config from `/cusdata/config/dataIndex/` | Fact observed in code (`mikeypad.c:97-101,692-720`, Hisense NT72690 build). Explains the semantics of the recap's "Factory Project ID 101" and supports the earlier finding (`firmware-candidates.md`) that Project ID alone is a config selector, not a unique model key. |
| MBoot injects cmdline; userland reads `/proc/cmdline` | Fact observed in code. On the target TV, reading `/proc/cmdline` (if a shell or service exists) would be a non-invasive diagnostic; not yet possible — access method unknown. |
| AVB/dm-verity verified mount of squashfs/ext4 images | Fact for the Hisense U7 NT72690 codebase. **Hypothesis** for Edenwood: same VIDAA-platform architecture, unverified. If true, a mismatched firmware package would fail verification or boot, reinforcing "do not flash candidates". |
| USB `.bin` format, OTA route, keys, partitions on the Edenwood | Unknown. |
| EL.NT72690 board family for Edenwood (32"/40" models) | Fact from external sources (`public-sources.md` §6); the 50" board reference is still unknown. |

The common NT72690+VIDAA platform makes these mechanisms plausible for the Edenwood, but the repository contains Hisense code and metadata only; no Edenwood-specific conclusion is justified beyond the platform level.

## 9. Open questions and next documentary steps

1. Where is the USB upgrade implemented (MBoot vs. a userland tool)? Best lead: the VIZIO `NT72690_FUR*` open-source releases (`public-sources.md` §4) — different OS, but full source tarballs that may include the Novatek upgrade stages.
2. What is inside a VIDAA firmware package (the `patch_package` ZIP structure already seen range-inspected on `VS0355UE2_0202`, `snapshot-audit.md`)? Mapping its members against the partition names found here (`vbmeta`, `boot`, system images) is a static, non-invasive next step.
3. Does the target TV expose `/proc/cmdline` or the `cusdata` tree through any documented interface? Depends on diagnostic access still to be established (open question 8 in `open-questions.md`).
4. Which partitions and A/B scheme does the Edenwood build actually use? Unknown until a trace, a service menu readout, or a package analysis.

## 10. Reproducibility

- Clone: `git clone --depth 1 https://github.com/opensource-hisense/SmartTV-SeriesVidaaU7_NT.git`
- Verify HEAD is `006eb7d5ddd809ff0608edca91d62f86e032b388` (2026-10-22).
- All searches were `rg`-based over the working tree; no scripts from the repository were executed, no dependencies installed, and no build was attempted.
