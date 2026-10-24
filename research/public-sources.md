# Public sources consulted

External documentary research from 2026-10-24. All sources are legally accessible public pages. No TV was modified, no firmware was downloaded or installed, and no credentials were used.

Search tooling note: the WebSearch tool was unavailable in this environment (HTTP 403 for the organization). Searches were run read-only against Brave Search via `curl`; DuckDuckGo, Mojeek, and Ecosia blocked automated requests, and Bing returned unrelated localized results. URLs below were all consulted on 2026-10-24.

## Sources

### 1. Retail model spec sheet — lcd-compare.com

- URL: `https://www.lcd-compare.com/televiseur-EDEED50EA03UHDEL-EDENWOOD-ED50EA03UHD-EL.htm`
- Consulted: 2026-10-24.
- Demonstrates: retail specifications for the Edenwood ED50EA03UHD-EL — 127 cm (50") QLED, 3840x2160, 50/60 Hz panel, HDR10, 2.0 audio 20 W, Smart TV with the VIDAA system, Wi-Fi, TNT/Satellite/Cable tuner. Confirms the retail model runs VIDAA, matching the target device recap.
- Limits: no motherboard, firmware, or internal identifier information.

### 2. User manual existence — manualslib.fr

- URL: `https://www.manualslib.fr/products/Edenwood-Ed50A03Uhd-El-13415977.html` (manual page: `https://www.manualslib.fr/manual/913164/Edenwood-Ed50A03Uhd-El.html`)
- Consulted: 2026-10-24.
- Demonstrates: a 71-page French user guide "Guide D'utilisation" exists for an "Edenwood ED50A03UHD-EL".
- Limits: the manualslib product name is `ED50A03UHD-EL`, not `ED50EA03UHD-EL`. This may be the same product with a dropped letter, or a different (non-QLED) variant. Not yet verified against the target; do not treat as the exact manual without checking.

### 3. Hisense VIDAA U7 NT72690 open-source release — GitHub

- URL: `https://github.com/opensource-hisense/SmartTV-SeriesVidaaU7_NT`
- Consulted: 2026-10-24.
- Demonstrates: Hisense publishes an open-source drop for VIDAA U7-series TVs on the NT72690 platform. The `oss/` tree contains the kernel, U-Boot, `avb` (Android Verified Boot components), `squashfs_util`, `fs_mgr`, DirectFB, and many libraries. This is primary source material for the same SoC and the same VIDAA OS family as the target.
- The README maps Hisense mainboard part numbers (e.g. `50A60LXVT(0002)`, `40Q50QEVS`) to retail models, and shows VIDAA internal model identifiers in the same `VS####XG#_####` scheme as the target `VS0350UF2_0208` (examples: `VS0655UG2_0600`, `VS0332FG2_0109`). Confirms the internal-name structure observed in the OTA listings.
- Limits: Hisense boards only. No `VS0350` and no Edenwood entry in this mapping, so the target is not one of these Hisense models. No OTA/updater application code is present in the OSS parts inspected (listing level only).

### 4. VIZIO NT72690 open-source releases — GitHub

- URLs: `https://github.com/opensource-vizio/NT72690_FUR8.1_OpenSourceSoftware` (firmwares 90.810.X), `https://github.com/opensource-vizio/NT72690_FUR7.2_OpenSourceSoftware` (firmwares 90.720.X), plus an FUR7.1.1 repo.
- Consulted: 2026-10-24.
- Demonstrates: VIZIO also ships NT72690-based TVs and publishes source tarballs. Same SoC family as the target; useful for low-level platform understanding.
- Limits: VIZIO SmartCast, not VIDAA. Different software stack; any extrapolation to the target is at the SoC level only.

### 5. Hisense NT72690 service manual — manualslib.com

- URL: `https://www.manualslib.com/manual/3624622/Hisense-Nt72690.html`
- Consulted: 2026-10-24.
- Demonstrates: a Hisense service manual (V1.0, 2023-01-29) for the NT72690 chassis, using the 32A33EXVT as example model. Documents: main board layout (RSAG2.908.12913-01), factory/service OSD menus, USB software upgrading (fallback rename to `usb_NOVATEK_DEV_EU.bin`), serial upgrading via a USB-serial cable with the UART option enabled in Factory → Options, an Mboot bootloader stage, and eMMC flash. Firmware file naming example: `NOVATEK_72690_EU_2K_NOVATEK_ICCOPY_emmc_HD2K-HIS_20230129.tar.gz`.
- Limits: documents the Hisense implementation of the platform. The Edenwood implementation may share the Novatek upgrade stages but can differ in file naming and validation. Nothing here was applied to the target TV.

### 6. Edenwood/SABA NT72690 mainboard family — resale listings

- URLs (examples): `https://picclick.fr/Carte-MERE-ELNT72690-FG48-POUR-TV-EDENWOOD-267352968723.html` (Edenwood ED32A09HD-EL, board `EL.NT72690-FG48`), `https://picclick.fr/Motherboard-Carte-M%C3%A8re-TV-SABA-SB32HDS2345-ELNT72690-FG48ST3151A08-4-326852491939.html` (SABA SB32HDS2345), plus variants `EL.NT72690-FG75` (Edenwood ED40A02FHD-EL) and TPV boards `TPD.NT72690.PC762/PB767` (Philips).
- Consulted: 2026-10-24.
- Demonstrates: Edenwood TVs are built on `EL.NT72690` mainboards, a board family also used by SABA, with TPV-made `TPD.NT72690` boards appearing in Philips sets. Confirms the recap's NT72690 identification with independent board-level evidence.
- Limits: evidence covers 32"/40" Edenwood models; the exact board suffix for the 50" QLED target (`ED50EA03UHD-EL`) is still unknown. Resale listings are informal sources — board names are credible but not manufacturer documentation.

### 7. VIDAA NT72690 browser user-agent — user-agents.net

- URL: `https://user-agents.net/string/mozilla-5-0-x11-linux-aarch64-applewebkit-537-36-khtml-like-gecko-chrome-111-0-5563-146-odin-111-5563-5-1-safari-537-36-model-vidaa-nt72690`
- Consulted: 2026-10-24.
- Demonstrates: VIDAA NT72690 TVs use a Linux aarch64 "Odin" browser (Chromium-based, Chrome 111 lineage) whose user-agent contains `model-vidaa-nt72690`. Useful for recognizing the TV's browser traffic on an authorized network and for interpreting the launcher canary API's chip-platform fields.
- Limits: a UA string, not documentation of the OTA path.

### 8. VIDAA web-app installation toolkit — GitHub

- URL: `https://github.com/weinzii/vidaa-edge` (MIT, experimental; credits BananaMafia's Hisense analysis)
- Consulted: 2026-10-24.
- Demonstrates: community knowledge of VIDAA app-level APIs: a legacy `Hisense_installApp` function and a newer method writing `websdk/Appinfo.json` via `HiUtils_createRequest` with a `fileWrite` command, activated by DNS-pointing `vidaahub.com` at a local host. Documents the existence of a file-system read API (`Hisense_FileRead`) usable for read-only diagnostics.
- Limits: not used here — the install methods write to the TV, so they are out of scope for passive research. Any use on the target requires an explicit decision.

## What this research adds

- Independent confirmation of the NT72690 platform at board level for the Edenwood brand.
- Confirmation of the VIDAA internal model-name scheme from a manufacturer source (Hisense OSS README).
- Manufacturer-grade documentation of the NT72690 USB/serial upgrade stages (Hisense service manual), answering part of open question 5 at platform level.
- Identified a same-platform, same-OS open-source corpus (Hisense U7 NT72690) as the best public reference for future low-level questions.

## What it does not add

- No new candidate firmware package for `VS0350UF2_0208`.
- No identification of the Edenwood 50" board reference or the ODM behind the VS0350 family.
- No evidence of the exact OTA check route used by the TV.
