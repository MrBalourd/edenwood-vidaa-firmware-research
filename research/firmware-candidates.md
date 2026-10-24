# Firmware candidate comparison

No candidate below is verified for the target `VS0350UF2_0208`, Project ID 101, EU_A, software build `S0343.V0200.UE2.00.09.09B.P1120`.

| Candidate | Why it appeared relevant | Mismatch or limitation | Status |
| --- | --- | --- | --- |
| `EM-OTA/VS0350UF3_0100446patch_package_entire_20260127.zip` | Shares the `VS0350` family prefix | Package metadata reports ProjectID `016`, model `VS0350UF3_0100`, and BasicmodelName `VS0343UE3_0100`; target is UF2 / Project 101 / UE2 | Reject as target match; do not flash |
| `EU-OTA/VS0343UE2_0200_patch_package_entire_OEMBASICPKG_20240702.zip` | Matches the `S0343.V0200.UE2` naming family | Local `basic.ini` described generic software metadata with no TV-model relationship; reports U8 / O0702. Exact target, region, and Project ID are not established | Family-level lead only |
| `EM-OTA/55Q60SAVT01_OTA_diff_P1113_to_P1120.zip` | Filename contains P1120 | Names another model | False positive |
| `NA-OTA/P1120-1128.zip` | Filename contains P1120 | NA path; no evidence it maps to this TV | False positive |

A Project ID alone does not establish a match: a separate observed Project ID 101 package maps to `VS0624FE2_0202`.

## Current answer

No exact model package has been confirmed. Filename similarity, software-family similarity, or a shared Project ID is insufficient. Do not install or flash any of these candidates.
