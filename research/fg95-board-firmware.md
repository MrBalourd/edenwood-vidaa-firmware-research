# EL.NT72690VU-FG95 board and firmware leads

Investigation from 2026-10-24, follow-up to `public-sources.md` §6 (which covered the FG48/FG75 boards of smaller Edenwood models). Documentary research only: no archive was downloaded, no firmware installed, and nothing was written to any TV.

## 1. The FG95 board and the target TV

Two resale listings directly tie the target's model family to the `EL.NT72690VU-FG95` mainboard:

| Model | Board | Panel | Source (consulted 2026-10-24) |
| --- | --- | --- | --- |
| Edenwood **EED50EA03UHD-EL** | EL.NT72690VU-FG95 | not shown | eBay item 800656535600 via `https://picclick.fr/Carte-m%C3%A8re-TV-Edenwood-EED50EA03UHD-EL-ELNT72690VU-FG95-800656535600.html` (EUR 63.10, seller lopdem-0, Maubeuge, used) |
| Edenwood **ED55EA05UHD-EL** | EL.NT72690VU-FG95 | ST5461D12-4 | eBay item 366317858942 via `https://picclick.fr/%85-Carte-M%C3%A8re-pour-TV-Edenwood-ED55EA05UHD-EL-ELNT72690VU-FG95-366317858942.html` (EUR 83.80, seller hifibar42, tested, panel ST5461D12-4 with T-CON EL.FITI-OCPW) |

**Name reconciliation (open question 4):** `EED50EA03UHD-EL` appears only in the eBay listing title; the retail name everywhere else (Electro Dépot, lcd-compare) is `ED50EA03UHD-EL`. Treated as a spelling variant of the same 50" QLED model, not a separate product. `ED55EA05UHD-EL` is the 55" sibling of the same EA QLED line — same mainboard, different size and panel (ST5461D12-4). No source claims a different SoC or board for the 55".

Caveats: both are informal resale listings, not manufacturer documentation. The board reference was written by the sellers on physical boards; the 50" listing shows no panel, so the target's panel reference is still unconfirmed.

## 2. Every TV model found on EL.NT72690VU-FG95

| Model | Brand / market | Notes | Source |
| --- | --- | --- | --- |
| HQS50X6500 | HQS, Argentina | 50", panel CX500DLEDM, T-CON DCBDM-X296C_08 | `https://www.newtodofirmware.com/hqs/hqs-hqs50x6500/` |
| HQS55X6500 | HQS, Argentina | 55", chassis NT72690VU | `https://www.newtodofirmware.com/hqs/hqs-hqs55x6500/` |
| 50iPlay6200-U (QL50iPlay6200-U) | Allview, Romania/EU | 50", panel CX500DLEDM = CV500U2-T01 Rev.02; **VIDAA 6.0**; Cortex A53 quad; 4 GB flash / 1.5 GB RAM; DVB-T/T2/C/S2; HDR10; 60 Hz | `https://www.schemetv.ro/placi-de-baza/el-nt72690vu-fg95-pozitia-gg729.html`, `https://www.allview.pl/50iplay6200-u.html`, `https://remont-aud.net/dump/lcd_pdp_tv/prochee/allview_ql50iplay6200_u_shassi_main_board_el_nt72690vu_fg95/248-1-0-168058` |
| 43QLED306W / Essentielb QLED306-308 | Essentielb (Boulanger), France | 43QLED306 board listing; 50QLED307 is the 50" 2024 sibling: VIDAA, QLED, 4K, 60 Hz, HDR10, 20 W, class E — near-identical spec sheet to the Edenwood ED50EA03UHD-EL | `https://www.leboncoin.fr/ad/photo_audio_video/3247651943` (43QLED306W + FG95), seller hifibar42 related-listings note, `https://www.lcd-compare.com/televiseur-ESS50QLED307-ESSENTIELB-50QLED307.htm` |
| EED50EA03UHD-EL / ED55EA05UHD-EL | Edenwood (Electro Dépot), France | §1 above | §1 |

Not on the Novatek FG95 despite the name: KANJI KJ-50ST005-2Q and PANORAMIC PAN-50ST005-2 use **EL.MT9602PRO-FG95** (MediaTek; `install.img` USB format) — the FG95 suffix is a board-numbering coincidence across ODM board generations, not the same platform (`https://www.newtodofirmware.com/kanji-2/kanji-kj-50st005-2q/`, `https://www.newtodofirmware.com/panoramic/pan-50st005-2/`).

Reading: the board appears to be one ODM platform (EL. = TPV-style board coding) sold across EU budget brands (Edenwood, Essentielb, Allview) and Latin-American brands (HQS) with different panels, tuners, and branding. The 50" EU units found (Edenwood, Essentielb, Allview) share the CX500DLEDM/CV500U2 panel family (two of three confirmed).

## 3. Firmware availability for FG95 (all paywalled or registration-gated)

| Offer | Contents | Conditions | Source |
| --- | --- | --- | --- |
| newtodofirmware "HQS HQS50X6500" | `usb_NOVATEK_72690_AM_OEM_4K_1080P.bin`, `FACBURN.CTV`, folder `CV72690CustomerFile`; plus separate `EMMC_HQS50X6500.zip` | Annual subscription (`https://www.newtodofirmware.com/producto/suscripcion-anual/`); version and date not stated; provenance = repair-community upload, no manufacturer link | `https://www.newtodofirmware.com/hqs/hqs-hqs50x6500/` |
| mundotecnico "EL.NT72690VU-FG95" board topic | `USB_Upgrade_EL.NT72690VU-FG95_4K.zip` attached to the forced-install post (post `p=107946`, board topic `t=28124`); service-menu and USB-update procedures documented | Forum premium account ("Acceda a una cuenta Premium para desbloquear este contenido") | `https://www.mundotecnico.info/viewtopic.php?t=28124`, model topic `t=26539` |
| remont-aud Allview dump | Full eMMC image (Toshiba THGBMJG6C1LBAIL, 8 GB, GPT, 40 partitions) of one QL50iPlay6200-U unit, ~968 MB RAR | Registration + site test; uploader notes it contains that unit's serial, MAC, HDCP/Widevine keys | `https://remont-aud.net/dump/lcd_pdp_tv/prochee/allview_ql50iplay6200_u_shassi_main_board_el_nt72690vu_fg95/248-1-0-168058` |

Per the project's safety rules, the Allview eMMC dump must **not** be treated as a firmware substitute: it is a raw per-device backup with device-bound keys. None of the three offers was purchased or downloaded.

Naming observations: `usb_NOVATEK_72690_AM_OEM_4K_1080P.bin` follows the `usb_NOVATEK_*` pattern of the Hisense NT72690 service manual (`usb_NOVATEK_DEV_EU.bin`, `public-sources.md` §5), consistent with an MBoot-level USB upgrade path; `AM` suggests an Americas region build (HQS is Argentine) and `OEM` a white-label build; `CV72690CustomerFile` matches the customer-configuration concept. This is interpretation of file names, not verified content.

## 4. Board identity vs firmware compatibility

An identical board does not establish firmware compatibility. Deltas to resolve before any candidate could be considered (none is resolved today):

1. **Region/tuner:** target recap says region `EU_A` (DVB tuners). HQS is Argentine — the `AM` build likely carries ISDB-Tb tuner configuration. Allview/Essentielb/Edenwood are EU (DVB), so an EU FG95 build would be the right region family.
2. **Panel:** FG95 ships with different panels (CX500DLEDM/CV500U2 family on 50" HQS/Allview; ST5461D12-4 on the 55" Edenwood). USB packages carry panel/customer configuration (the `CV72690CustomerFile` folder in the HQS package). The Edenwood 50" panel reference is unknown.
3. **Project/model identity:** the target's `VS0350UF2_0208` / Project ID 101 / build `S0343.V0200.UE2.00.09.09B.P1120` must appear in or match the package's own metadata — the same discipline as `firmware-candidates.md`. None of the three offers publishes this metadata publicly.
4. **Branding/VIDAA version:** Allview advertises VIDAA 6.0; the target's VIDAA version is not directly recorded (launcher OPKG package 9.00.02 observed earlier).

## 5. Best candidate for static comparison (no installation)

- **First choice:** `USB_Upgrade_EL.NT72690VU-FG95_4K.zip` (mundotecnico, premium) — board-exact, and the name suggests a chassis-level package rather than a single-brand build. Requires a premium account to obtain.
- **Second choice:** the HQS50X6500 package (newtodofirmware, subscription) — board-exact but an Americas (`AM`) build; useful to compare structure (bin + FACBURN.CTV + CustomerFile folder), not to flash.
- For either, the static comparison is the same as the bounded Range inspections already done for `VS0355UE2_0202` (`snapshot-audit.md`): list ZIP members, read small INI/metadata members, look for the target's `VS0350UF2_0208` / Project ID 101 / EU region markers, and check whether a per-panel/customer config distinguishes builds. No purchase, download, or installation was performed for this report.

## 6. Open questions added

1. What panel reference does the Edenwood ED50EA03UHD-EL actually use (CX500DLEDM family or other)? A board-label photo or factory-menu readout would settle it.
2. Does the Edenwood EA line share the Allview/Essentielb EU build family (VS03xx naming, EU region config) closely enough that an EU FG95 firmware exists for it in VIDAA's own OTA store? The OTA listing problem (`api-observations.md`) is unchanged; the FG95 knowledge narrows which family names to look for.
3. What exactly is inside `CV72690CustomerFile` (panel/tuner/region selection?) — answerable only after obtaining one of the paywalled packages.
