# Edenwood / VIDAA firmware research

A sanitized, evidence-tracked notebook for identifying the firmware package that corresponds to an Edenwood ED50EA03UHD-EL television running VIDAA on a Novatek NT72690 platform.

## Current conclusion

No exact firmware package has been confirmed for the target TV. The closest name-family package is not proof of a match, and the visible UF3 package belongs to a different variant. **Do not install or flash any candidate.**

The public OTA listing observations are affected by CDN caching and unreliable continuation behavior. The saved test-container snapshot is useful evidence, but it is not a definitive live catalog. The production listing has not been exhaustively paginated in a reliable way.

## What is recorded

- Target device identifiers that are model/build-level, not unique device identifiers.
- Sanitized API endpoints and observed response behavior.
- Firmware candidate comparison and reasons each candidate is unconfirmed.
- Static analysis of the VIDAA launcher package.
- A dated research timeline and outstanding questions.
- Saved listing snapshot audit with duplicate-page accounting.

## Safety and scope

All recorded network observations were read-only. No firmware was installed, no system package was modified, and no USB upgrade was confirmed. A manual OTA check capture was not available; the user deferred it. Avoid probing private services or bypassing access controls. Do not attempt a firmware update without a verified model, project, region, and package match.

## Repository hygiene

This repository excludes downloaded packages, raw captures, local machine paths, unique device identifiers, and credentials. See [SECURITY.md](SECURITY.md) and [.gitignore](.gitignore).
