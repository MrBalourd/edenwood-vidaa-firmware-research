# API observations

All endpoints below are public service names and routes observed in read-only research. No credentials, authorization headers, signed query values, cookies, or request dumps are included.

## Firmware object listing

- Listing host: `https://ota-dl.vidaahub.com/`
- Container path: `/` (container `ota`)
- Query parameters used: `restype=container`, `comp=list`, optional `prefix`, `maxresults`, and a cache-busting `_cb` value.
- Purpose: enumerate object names and metadata returned by the public listing endpoint.

### Observed behavior and caveats

- Earlier transcript notes report a cache-busted run returning 5,050 production names and 15,356 test names. These counts are historical claims in the supplied transcript, not a reproducible current baseline.
- A later saved test listing contains 5,256 distinct names. Several saved pages repeat prior results; the saved snapshot and earlier reported counts do not reconcile.
- The live CDN has returned stale or prefix/marker-inconsistent XML responses in subsequent checks. The saved production snapshot does not establish a complete catalog: its first page contains 5,000 mostly manual objects and continuation was not reliable.
- A direct request to the Azure Blob origin returned HTTP 409 (`Public access is not permitted`). No access-control bypass was attempted.
- The test listing's `include=metadata` response was HTTP 200 but returned stale-looking XML with `Prefix=EM-OTA`, `MaxResults=5`, and five blobs. Treat it as cache-affected.

**Conclusion:** neither current listing behavior nor saved snapshots prove that the exact target package is absent from every production object.

## OPKG package feed

- Index: `https://app-dl.vidaahub.com/nt72690/Packages`
- Observed response: HTTP 200; 9,930 bytes; eight package entries.
- This is an application package feed for the NT72690 platform, not a system firmware catalog.

## VIDAA launcher canary request

Static inspection of the downloaded launcher found a route under `vidaa/canary/api/strategy/match`, with `appName=tv.vidaa.ui.tvbrowser`, a model value, and chip-platform data. The response selects a browser canary service URL. This is not evidence of the TV firmware OTA check route.

The broader semantic analysis of the minified launcher bundle did not complete because the analysis worker ran out of memory. String-search results should not be treated as proof that a route is absent from all code paths.
