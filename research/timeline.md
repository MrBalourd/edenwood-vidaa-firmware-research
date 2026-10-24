# Timeline

## 2026-10-23 — Initial passive exploration

- Recorded the Edenwood retail model, internal VIDAA model/build, SoC, region, and factory Project ID from the supplied recap.
- Identified the public OTA listing host and the NT72690 OPKG feed.
- Established naming patterns for firmware objects and found nearby family/build-name candidates.
- Determined that the exact OTA check route was still unknown; the recap proposed a packet capture during a real check.

## 2026-10-23 — Pasted transcript and pagination claims

- The supplied transcript reports a cache-busted enumeration with 5,050 production names and 15,356 test names, then reports no exact `VS0350UF2_0208` hit.
- It also identifies UF3 packages and P1120 filename matches as non-matches.
- These values are preserved as historical transcript claims, not independently confirmed current catalog totals.

## 2026-10-24 — Reconciliation and launcher analysis

- Later observations found stale CDN responses, ignored or inconsistent prefix/marker behavior, and a saved test snapshot with 5,256 distinct names. The pagination datasets do not reconcile.
- The production listing was not established as complete; no definitive absence claim is made.
- The public OPKG index was inspected and the latest launcher package was hash-checked against its feed.
- Static analysis found a browser canary API path, not the TV firmware OTA check path.
- The user deferred providing a manual-check packet capture. No installation or flashing was attempted.

## 2026-10-24 — Targeted API follow-up

- Queried the production and test object-list APIs with unique, non-secret cache-busters and exact/broad model prefixes. Responses were HTTP 200 but often failed to echo the requested prefix.
- On the test host, page 2 returned five names that all overlapped page 1 despite a new cache-buster; continuation is not reliable.
- No exact UF2 match appeared in the responses, but the stale and unfiltered results cannot prove that the object is absent.
- No credential values were sent or saved.

## 2026-10-24 — Snapshot reconciliation and bounded package inspection

- Reconciled saved page totals: production 5,050 rows / 5,000 distinct names; test 15,356 rows / 5,256 distinct names. Repeated pages account for the extra rows.
- Found the additional `VS0355UE2_0202` OEM basic package in the test snapshot. A bounded Range inspection of its ZIP metadata and `basic.ini` found version `V0000.08.00X.O1120`, with no target model or Project ID in that file.
- The candidate is a different 0355/UE2/0202 variant. No full firmware package was downloaded or installed; no exact target match has been confirmed.

## 2026-10-24 — External documentary research

- The built-in WebSearch tool was unavailable (HTTP 403); searches were run read-only against Brave Search via `curl`. All sources, URLs, and consultation dates are recorded in `public-sources.md`.
- Confirmed the retail model's VIDAA system and QLED specs from a public spec sheet (lcd-compare.com).
- Found a Hisense service manual for the NT72690 chassis documenting USB upgrade (including the `usb_NOVATEK_DEV_EU.bin` fallback), serial upgrading, Mboot, and eMMC at platform level.
- Found the Hisense open-source release for VIDAA U7 TVs on NT72690 (kernel, U-Boot, AVB components), and VIZIO NT72690 source releases. The Hisense README confirms the `VS####XG#_####` internal-name scheme; it contains no `VS0350` or Edenwood entry.
- Found independent board-level evidence that Edenwood TVs use `EL.NT72690` mainboards (32"/40" models via resale listings); the 50" target board suffix remains unknown.
- Noted the `weinzii/vidaa-edge` toolkit as evidence of VIDAA app-level APIs (`Hisense_installApp`, `Appinfo.json` writes, `Hisense_FileRead`); not used, since installation methods write to the TV.
- No new firmware candidate was found; nothing was downloaded, installed, or flashed.

## 2026-10-24 — EL.NT72690VU-FG95 board and firmware investigation

- Resale listings directly tied the target model family to the `EL.NT72690VU-FG95` mainboard: Edenwood `EED50EA03UHD-EL` (50", spelling variant of `ED50EA03UHD-EL`) and Edenwood `ED55EA05UHD-EL` (55", panel ST5461D12-4). Recorded in `fg95-board-firmware.md`.
- Mapped the FG95 board across brands: HQS50X6500/HQS55X6500 (Argentina), Allview 50iPlay6200-U (EU, VIDAA 6.0, DVB), Essentielb QLED306/307/308 (France, VIDAA, near-identical spec sheet to the target), plus the Edenwood units. KANJI/PANORAMIC "FG95" boards are EL.MT9602PRO (MediaTek) and were excluded as a suffix coincidence.
- Found FG95 firmware offers, all paywalled: newtodofirmware HQS50X6500 package (`usb_NOVATEK_72690_AM_OEM_4K_1080P.bin` + `FACBURN.CTV` + `CV72690CustomerFile`), mundotecnico `USB_Upgrade_EL.NT72690VU-FG95_4K.zip` (premium), and a remont-aud Allview eMMC dump (device-specific keys; excluded as a firmware substitute). Nothing was purchased or downloaded.
- Concluded that board identity does not establish firmware compatibility (region AM vs EU_A, panel config, Project ID, branding). The best static-comparison candidate is the mundotecnico board-exact package, gated behind a premium account.
