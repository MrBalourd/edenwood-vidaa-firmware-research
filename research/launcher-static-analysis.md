# Launcher package analysis

## Source and integrity

The latest public OPKG feed package analyzed was `tv.vidaa.app.operationui_9.00.02-b20260428s_all.ipk`. Its SHA-256 was checked against the feed metadata and matched:

`286b5a5169b0b7606c20b1e654c5d8549d320a5c3ff270cf0988b0eec5811d89`

The package binary and extracted tree are intentionally not committed.

## Findings

- Static string inspection found a `upgrade-plc-tv` route reference used in the browser canary flow.
- The request path found was `vidaa/canary/api/strategy/match`; its model and chip-platform values are used to select a browser canary configuration.
- Scans of this launcher package did not find literal `ota-tv`, `upgrade-cus`, or `checkNewVersion` strings, nor the target `VS0350UF2_0208` or Project ID 101.
- This is limited negative evidence for the inspected package, not proof that the TV firmware updater has no such routes. The focused semantic analysis of the large minified `app.js` bundle did not finish due to memory exhaustion.

## Related feed

The `nt72690/Packages` index returned eight application package entries. It exposes launcher and supporting apps, but no separate system firmware updater package was identified in the inspected index.
