# Firmware candidate comparison

No candidate below is verified for the target `VS0350UF2_0208`, Project ID 101, EU_A, software build `S0343.V0200.UE2.00.09.09B.P1120`.

| Candidate | Why it appeared relevant | Mismatch or limitation | Status |
| --- | --- | --- | --- |
| `EM-OTA/VS0350UF3_0100446patch_package_entire_20260127.zip` | Shares the `VS0350` family prefix | Package metadata reports ProjectID `016`, model `VS0350UF3_0100`, and BasicmodelName `VS0343UE3_0100`; target is UF2 / Project 101 / UE2 | Reject as target match; do not flash |
| `EU-OTA/VS0343UE2_0200_patch_package_entire_OEMBASICPKG_20240702.zip` | Matches the `S0343.V0200.UE2` naming family | Local `basic.ini` described generic software metadata with no TV-model relationship; reports U8 / O0702. Exact target, region, and Project ID are not established | Family-level lead only |
| `EU-OTA/VS0355UE2_0202_patch_package_entire_OEMBASICPKG_20241120.zip` | Closest additional UE2/03xx family name in the saved test snapshot | Code is 0355 / UE2 / 0202, not target 0350 / UF2 / 0208. A bounded HTTP Range inspection of `patch_package/basic.ini` found version `V0000.08.00X.O1120` but no target model or Project ID | Sibling lead only; not verified |
| `EM-OTA/55Q60SAVT01_OTA_diff_P1113_to_P1120.zip` | Filename contains P1120 | Names another model | False positive |
| `NA-OTA/P1120-1128.zip` | Filename contains P1120 | NA path; no evidence it maps to this TV | False positive |

A Project ID alone does not establish a match: a separate observed Project ID 101 package maps to `VS0624FE2_0202`.

The saved test snapshot also contains `VS0350UF3` (January 2026), `VS0355UF3`, and `VS0365UF3` packages. These are UF3 variants and do not establish a match for the target UF2 model. The P1120 filename matches remain unrelated model/region entries.

The 678,550,126-byte `VS0355UE2_0202` archive was not downloaded in full or saved locally. Only HTTP headers, the ZIP directory, and the small `basic.ini` member were read with byte-range requests; no firmware was installed or flashed.

## Current answer

No exact model package has been confirmed. Filename similarity, software-family similarity, or a shared Project ID is insufficient. Do not install or flash any of these candidates.
