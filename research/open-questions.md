# Open questions

1. Which exact package object, if any, matches `VS0350UF2_0208`, Project ID 101, region EU_A, and the installed P1120 build?
2. Can the public OTA listing be queried with fresh, reproducible pagination such that prefix and continuation behavior can be validated independently of CDN cache?
3. What request path and model fields does the TV itself use for a manual OTA check? A user-captured trace during a check was deferred; no capture is included.
4. Is there a distinct hardware/model code in the factory menu that maps the retail model to the package naming scheme?
5. What is the exact USB package validation format? No candidate has been inspected or installed.

6. Does the `VS0355UE2_0202` base package have any documented relationship to the target `VS0350UF2_0208`? The inspected `basic.ini` supplies only a different version string.

Until these are resolved, keep all package candidates as unverified and do not flash them.
