# Notice

Copyright © 2026 the 21-simulator author. All rights reserved.

## What this repository is

The public product page, documentation and release channel for 21-simulator.

It contains documentation and released build artifacts. **It does not contain
the 21-simulator source code**, and no build published from it grants access to
that source. See [LICENSE.md](LICENSE.md) for the terms that apply to a
distributed build.

## Not open source

21-simulator is proprietary software. It is not released under an open-source
licence, and the absence of one here is deliberate: on GitHub, public visibility
of a repository does not by itself grant a licence to its contents.

## Third-party components

A distributed Windows build embeds an unmodified CPython runtime and its
standard library, and is assembled with PyInstaller. Those components remain
under their own licences (the PSF Licence for CPython; PyInstaller's licence for
its bootloader), and this notice does not alter them. The application's own
engine, interface and research tooling are not covered by those licences and are
not published.

The application itself has no third-party runtime dependencies: the engine is
written against the Python standard library only.

## Build integrity

Each release publishes the SHA-256 of its artifact. Builds are **not
code-signed**, so Windows SmartScreen may warn on first run; the published
checksum is the check that actually tells you the file is the one that was
tested. Only releases on this repository are official — there is no other
download location.

## Research figures

Numbers in this repository were computed under stated rules and models, and are
published with their assumptions and limitations
([docs/research.md](docs/research.md), [docs/limitations.md](docs/limitations.md)).
They are not advice, and no gambling outcome is guaranteed.

## Contact

Open an issue on this repository, or contact the repository owner through GitHub.
