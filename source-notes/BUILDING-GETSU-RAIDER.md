# Building GETSU RAIDER (PPSA99606) from source

Development sources are in `~/.cache/getsu/raider-src`. This is the existing
GETSU RAIDER project, not a new port. The exact-title helper and executable
have been built locally; see `BUILD-VERIFICATION.json` for their hashes.
Public release materials are still under review. No PPSA99606 console
validation is claimed. The Prospero Win source tree (LGPL-2.1-or-later)
includes our changes —

- **the GETSU front-end** — `native/getsu_landing.{c,h}` (the invented
  moonlight-over-the-forest installer screen) and `native/pw_getsu_front.{c,h}`
  (folder layout, dropzone scan, prefix seeding, game install), wired into
  `native/wine64_main.c` behind `-DPW_GETSU_FRONT=1`;
- **a sandbox fix** in `native/wine64_main.c` — `resolve_sandbox_app0()` looks
  up `/mnt/sandbox/<title>_NNN/app0` at run time instead of the fixed `_000`,
  which breaks once the tile has been reinstalled;
- **our ntdll memory work** — `wine/ps5/pw_wine_dmem.c`, `pw_wine_dmem_ps5.c`
  (lazy commit + a demand page file so the game and GPU share the 12 GiB pool)
  and our four Wine patches `wine/patches/0986-*`, `0995-*`, `0996-*`, `0997-*`
  (the other 100 patches there are prospero-win's);
- **the RADV video-memory-budget patch** — `patches/radv/`.

## 1. The title (eboot.bin)

### Prepare the missing helper locally (not a console operation)

Use the mpereiraesaa fork at commit
`b48b7d7236eca25c7b9dfd6c040c763ae05dde80`. The original ArkSama daemon is a
different program and is not a replacement for this exact-title ELF helper.
The fork's documented Makefile target is `owned-helper`, with
`TARGET_TITLE=PPSA99606`, `PS5_PAYLOAD_SDK` pointing to the public SDK and
`LOGGING_CLIENT` pointing to the directory containing the matching LGPL
`ps5log.h`. Python 3, Make and the SDK's Clang/linker are required.

Perform any later build in an isolated copy under this existing project's
`build/` directory. Do not run builds in the shared Lapy or foundation cache:
those are also dependencies of other projects. Preserve GETSU SOTF in full.
Do not use the default `make` target (resident service), `legacy`, upload,
deployment or console-loading targets. No PS5 operation is part of local build.

The maintainer's helper build produces `lapy.elf`, `lapy-manifest.json` and a
private `manifest.json`. Calculate the ELF's SHA-256 after building. Its
release manifest must identify PPSA99606, mode `elf-helper`, the matching ELF
and protocol digests, and `root_layout_probe_retry`. Do not edit a PPSA99605
manifest to make it appear compatible.

The RAIDER version of `tools/build_native.sh` accepts the first two files;
it validates the explicit pinned SHA-256 and the manifest before staging.
It generates its own `release.json` marked `source_type: local-build`, without
an upstream release URL or personal absolute paths. No manually invented
GitHub release is needed. Keep the maintainer's private `manifest.json` and
build log out of public archives: they contain local source paths.

### Build the title after the helper has been verified

The following is a future build recipe, not evidence that a build has run.
Use an isolated foundation copy at the pinned commit, not the shared cache.

    CXX=g++ \
    PW_TITLE_ID=PPSA99606 PW_TITLE_NAME="GETSU RAIDER" \
    PW_DATA_ROOT=/data/getsu-raider \
    PW_GETSU_FRONT=1 \
    PW_LAPY_RELEASE=ppsa99606-local \
    PW_LAPY_ELF_SHA256=<SHA-256 of the PPSA99606 lapy.elf> \
    PW_LAPY_LOCAL_DIR=/path/to/lapy-build \
    PS5_NATIVE_FOUNDATION=/path/to/isolated/ps5-native-app-boilerplate \
    bash tools/build_native.sh

`PS5_NATIVE_FOUNDATION` is a checkout of the PS5 Native App Boilerplate at
commit `9c0b994a048521af6fb84c73ded364504fe250e9` (see `SOURCES.txt`).

`PW_LAPY_LOCAL_DIR` must hold `lapy.elf` (with its `lapy-manifest.json`), built for
this title (PPSA99606) from the unmodified
[PS5 Lapy JB Daemon](https://github.com/mpereiraesaa/PS5-Lapy-JB-Daemon) at
commit `b48b7d7236eca25c7b9dfd6c040c763ae05dde80` with that project's
`tools/build_owned_daemon.py`, target title `PPSA99606`. The result is the
`lapy.elf` whose measured SHA-256 replaces the placeholder above. The locally
built helper has SHA-256
`78d0bcbda0ef5f59af90fb432d4d23c07622e1a8e73aa99f524da698d971b543`.
It was built using the isolated SDK and Clang 18, mode `elf-helper`, one request,
with no console validation. Its original source is now in `third_party/lapy/`;
the matching logger is `native/ps5log/ps5log.h`.

The title script clears its output under `raider-src/build/native` and
`raider-src/dist/PPSA99606`. Do not point its output at the existing desktop
runtime folder. After successful verification, merge only `eboot.bin`,
`lapy.elf`, its distribution manifest and the title metadata into
`GETSU RAIDER/dist/PPSA99606`. Compare `sce_module/libc.prx` against the
existing copy before deciding whether a replacement is needed. Preserve
the already prepared Wine runtime and prefix seed.

`PW_GETSU_FRONT=1` compiles the GETSU files in and makes `main()` run
`run_getsu()` instead of the stock launcher. Build from a path with no spaces
(the `prospero-clang` wrapper breaks on spaces); use `CXX=g++` (clang++-18 +
libstdc++16 miscompiles the libc builder).

## 2. ntdll.prx (page file) and 3. libvulkan.prx (budget)

Build the PS5 Wine runtime (`tools/build_wine_ps5.sh` + `tools/build_host_wine.sh`);
it applies every patch in `wine/patches/`, ours included, and our page-file code
is already in `wine/ps5/pw_wine_dmem*.c` and unit-tested under `tests/`. For the driver, apply `patches/radv/0001-radv-ps5-budget-headroom.patch`
to Mesa/RADV at the revision in `SOURCES.txt` and build it as
`tools/build_mesa_zink.sh` / PS5_Vulkan do.

## 4. The bundled Wine prefix (win/prefix-seed/)

The title cannot run `wineboot` on the console, so the released app folder
ships a ready, initialised Wine prefix (Wine PE DLLs + DXVK + vkd3d-proton) that
GETSU lays into `/data/getsu-raider/prefixes/sottr/` on first install. Build one on
a PC with `tools/pw_install.py` (its `create_prefix` task runs `wineboot --init`,
then installs DXVK and the CPU DLL), using the same pinned Wine as this tree.
The released seed carries DXVK `2.6.2-prospero3` and vkd3d-proton `3.0.1`, the
release archives pinned (URL + SHA-256) in `tools/pw_install.py`.
Its `dosdevices/.pw-symlinks` (`c: -> ../drive_c`, `z: -> /`) is recreated at
runtime by the title (`wine/ps5/pw_wine_cwd.h`). Keep `drive_c/Games/` empty;
GETSU drops the player's game into it.

GETSU RAIDER's seed is GETSU SOTF's seed without Sons of the Forest's settings
file, plus Shadow of the Tomb Raider's graphics options appended to `user.reg`
(`runtime/sottr-settings.reg`: 2560x1440, Direct3D 12, ray tracing off, quality
levels one step below the highest tested on the console).

## 5. Assembling the app folder

`tools/package_release.sh` lays out the folder, then drop in the page-file
`ntdll.prx` and the budget `libvulkan.prx` and the `win/prefix-seed/` prefix, as
the released package does.

## Licences

Prospero Win, Wine, vkd3d-proton and our ntdll changes are LGPL-2.1-or-later;
the title as a whole is GPL-3.0-or-later (boilerplate + SDK); RADV/Mesa and DXVK
are permissive. The combined Vulkan PRX has GPL components, as detailed in
`THIRD_PARTY.md`. Lapy's own code is MIT; the helper also incorporates the
LGPL logger/protocol and SDK runtime components. Retain their applicable
notices and corresponding source/build materials. Full texts in `LICENSES/`;
`SOURCES.txt` records revisions and the measured helper hash.

## Remaining release gates

- Local helper/title builds, identity checks and helper digest recording are
  complete. Preserve their local-build provenance and protocol hash.
- Assemble the RAIDER source companion, including the RAIDER helper-staging
  corrections, its notices and exact dependency references.
- Review personal metadata inherited from the Wine seed and the private
  `reference-ps5/` material before packaging. Do not include private reference
  logs or registry dumps in public ZIPs.
- Obtain separate authorization before any PS5 test or publication. A local
  build and matching protocol do not establish console safety or compatibility.
