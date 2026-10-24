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
