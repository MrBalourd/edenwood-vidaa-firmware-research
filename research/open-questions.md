# Open questions

1. Which exact package object, if any, matches `VS0350UF2_0208`, Project ID 101, region EU_A, and the installed P1120 build?
2. Can the public OTA listing be queried with fresh, reproducible pagination such that prefix and continuation behavior can be validated independently of CDN cache?
3. What request path and model fields does the TV itself use for a manual OTA check? A user-captured trace during a check was deferred; no capture is included.
4. Is `EL.NT72690VU-FG95` the target's actual mainboard? A resale listing for the spelling variant `EED50EA03UHD-EL` reports this board, but it has not been confirmed from the TV or a board-label photo.
5. What is the exact USB package validation format? Partial at platform level: a Hisense NT72690 service manual documents USB and serial upgrade stages, including a `usb_NOVATEK_DEV_EU.bin` fallback name and an Mboot/eMMC flow (see `public-sources.md`). Edenwood-specific naming and validation are unverified.
6. Does the `VS0355UE2_0202` base package have any documented relationship to the target `VS0350UF2_0208`? The inspected `basic.ini` supplies only a different version string.
7. Who is the ODM behind the `VS0350` internal-name family? Hisense's own NT72690 mapping contains no `VS0350`, and TPV-built `TPD.NT72690` boards exist for Philips sets, so the target may come from a different ODM.
8. Do the VIDAA app-level APIs evidenced by `weinzii/vidaa-edge` (`Hisense_FileRead`, `Hisense_installApp`) exist on the target firmware, and could `Hisense_FileRead` serve as a read-only diagnostic channel? Requires on-TV testing; not attempted.
9. What panel reference does the Edenwood ED50EA03UHD-EL use? FG95 listings show CX500DLEDM/CV500U2 for 50-inch HQS/Allview units and ST5461D12-4 for a 55-inch Edenwood; the target's panel remains unconfirmed.
10. Is the target part of the EU FG95 build family in VIDAA's own OTA store, under a VS03xx-family name not yet enumerated? The paywalled `USB_Upgrade_EL.NT72690VU-FG95_4K.zip` metadata could clarify model and region identifiers.

Until these are resolved, keep all package candidates unverified and do not flash them.
