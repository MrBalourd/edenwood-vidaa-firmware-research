# Saved listing snapshot audit

The local page XML was audited without committing the raw listings. These counts describe saved captures only; they are not a claim that the live production catalog was exhausted.

| Saved listing | Raw name rows | Distinct names | Duplicate rows | Page observations |
| --- | ---: | ---: | ---: | --- |
| Production | 5,050 | 5,000 | 50 | Page 1 had 5,000 names; page 2 repeated 50 from page 1; page 3 was empty |
| Test | 15,356 | 5,256 | 10,100 | Pages 3 and 5 repeated page 1; page 4 repeated page 2; page 6 added 256 distinct names |

## Target search in distinct names

- Neither snapshot contains the exact internal identifier `VS0350UF2_0208` or retail string `ED50`; the test snapshot has P1120-only false positives listed below.
- The test snapshot contained `VS0350UF3` packages (January 2026), `VS0355UF3` packages, and `VS0365UF3` packages. All are UF3 variants.
- It also contained `EU-OTA/VS0355UE2_0202_patch_package_entire_OEMBASICPKG_20241120.zip`, a close family-name lead but not the target variant.
- `P1120` also appeared in two unrelated filenames: an EM-OTA diff for `55Q60SAVT01` and a NA-OTA `P1120-1128` package.

## Bounded inspection of the VS0355UE2 package

The test CDN returned HTTP 200 for a header-only request and advertised byte ranges for the 678,550,126-byte ZIP. Inspection then fetched only the ZIP tail, its 23,394-byte central directory, and the 406-byte compressed `patch_package/basic.ini` member. The file's version field is `V0000.08.00X.O1120`; the inspected file does not identify the target model or Project ID.

The full archive was not downloaded or retained. The package remains unverified and must not be flashed to the target TV.

## Interpretation

The duplicate pages explain why raw counts appeared much larger than the number of distinct names. The test snapshot is not the production catalog, and both live listing endpoints have shown stale or ignored-prefix/marker responses. The exact target package is therefore still unconfirmed; the snapshots do not prove that it is absent from every current production object.
