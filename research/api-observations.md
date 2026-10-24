# API observations

All endpoints below are public service names and routes observed in read-only research. No credentials, authorization headers, signed query values, cookies, or request dumps are included.

## Firmware object listing

- Production listing host: `https://ota-dl.vidaahub.com/`
- Test listing host: `https://ota-dl.test.vidaahub.com/`
- Container path: `/` (container `ota`)
- Query parameters used: `restype=container`, `comp=list`, optional `prefix`, `maxresults`, and a cache-busting `_cb` value.
- Purpose: enumerate object names and metadata returned by the public listing endpoint.
- OTA check hosts identified in the recap: `https://ota-tv.vidaahub.com/` and `https://upgrade-plc-tv.vidaahub.com/`. The exact check path and payload remain unknown; the object-listing endpoint is a separate service.

### Observed behavior and caveats

- A later audit of the saved page XML reconciled the earlier totals: production had 5,050 rows but only 5,000 distinct object names; test had 15,356 rows but only 5,256 distinct names.
- In the production snapshot, page 2 repeated 50 names from page 1 and page 3 was empty. In the test snapshot, pages 3 and 5 repeated page 1, page 4 repeated page 2, and page 6 added 256 names. The previously reported totals were raw row counts, not unique object counts.
- The live CDN has also returned stale or prefix/marker-inconsistent XML responses in subsequent checks. The production snapshot contains 5,000 unique, mostly manual objects and does not establish a complete production firmware catalog.
- A direct request to the Azure Blob origin returned HTTP 409 (`Public access is not permitted`). No access-control bypass was attempted.
- The test listing's `include=metadata` response was HTTP 200 but returned stale-looking XML with `Prefix=EM-OTA`, `MaxResults=5`, and five blobs. Treat it as cache-affected.

**Conclusion:** neither current listing behavior nor saved snapshots prove that the exact target package is absent from every production object.

## Targeted follow-up (2026-10-24)

Read-only GET requests were sent to the production and test listing hosts with unique, non-secret `_cb` cache-busters and targeted model/build prefixes. No authentication values were sent or recorded.

### Production listing host

- Six targeted prefixes were tried, including the exact `VS0350UF2_0208` identifier and the `EM-OTA/` / `EU-OTA/` variants.
- All responses were HTTP 200, but results were inconsistent: some exact-prefix requests returned `Prefix=VS0` with zero blobs; other target prefixes returned either the same first 50 manual objects or the same 5,000-object manual-heavy page with a continuation marker.
- The returned XML did not reliably echo the requested prefix, so these responses cannot establish whether the target firmware object exists.

### Test listing host

- Exact-model prefix requests returned a 5-object `EM-OTA` response with a continuation marker, while broader prefixes returned the same 5,000-object page without the requested prefix echoed.
- A page-1 request for `prefix=VS0350` returned 5,000 objects and a continuation marker. Reusing that marker with a fresh `_cb` for page 2 returned five objects; all five also appeared on page 1. This is consistent with stale cache or ignored continuation behavior.
- The broad returned page contains the already-known UF3 and P1120 filename candidates, but no exact UF2 target match. Since the responses are not trustworthy filtered pages, this is not proof of absence.

**Current assessment:** direct API calls confirm that the listing endpoints respond, but current CDN behavior still prevents a reliable exhaustive answer from prefix or marker queries.

## OPKG package feed

- Index: `https://app-dl.vidaahub.com/nt72690/Packages`
- Observed response: HTTP 200; 9,930 bytes; eight package entries.
- This is an application package feed for the NT72690 platform, not a system firmware catalog.

## VIDAA launcher canary request

Static inspection of the downloaded launcher found a route under `vidaa/canary/api/strategy/match`, with `appName=tv.vidaa.ui.tvbrowser`, a model value, and chip-platform data. The response selects a browser canary service URL. This is not evidence of the TV firmware OTA check route.

The broader semantic analysis of the minified launcher bundle did not complete because the analysis worker ran out of memory. String-search results should not be treated as proof that a route is absent from all code paths.
