# Ontology Playground — HarmonyOS App (Signed Build)

The HarmonyOS application project for Ontology Playground. It embeds the packaged, single-file web bundle into its `rawfile` resources and builds a **signed, installable HAP** for HarmonyOS devices and the emulator.

This repository is the distribution build of the app's native shell: it is structurally the same ArkWeb host as the `OntologyPlayground-App` shell submodule, but it is configured with signing so the output can be installed and shipped.

## Architecture

- **Framework:** HarmonyOS ArkUI (ArkTS), built with the standard HarmonyOS toolchain (hvigor).
- **Entry module (`entry`):**
  - `pages/Index.ets` — full-screen `Web({ src: $rawfile('index.html'), controller })` with JavaScript / DOM-storage / file / database access enabled.
  - `entryability/EntryAbility.ets` — `UIAbility` loading `pages/Index`.
  - `entrybackupability/EntryBackupAbility.ets` — backup/restore hook.
- **`build-profile.json5`:** targets HarmonyOS SDK **6.0.0 (API 20)** with **debug + release signing configs**; the build produces a **signed** `entry-default-signed.hap`.
- **`rawfile/index.html`:** the self-contained web bundle (produced by the web app's `npm run build:harmony`). It is the only artifact the web layer contributes here.

## Build & Run

1. Produce the web build (`npm run build:harmony` in the web-app repo) and copy `build/index.html` into:
   `entry/src/main/resources/rawfile/index.html`
2. Open in DevEco Studio (HarmonyOS SDK 6.0.0).
3. Build → **Build HAP** (or `hvigorw assembleHap`). The signed HAP lands at
   `entry/build/default/outputs/default/entry-default-signed.hap`.
4. Install on a device / emulator:
   ```bash
   hdc -t <device-ip:port> install entry/build/default/outputs/default/entry-default-signed.hap
   hdc -t <device-ip:port> shell aa start -b com.janeconan.ontologyplayground -a EntryAbility
   ```

## Signing

Signing materials (`*.p12`, `*.p7b`, `*.cer`, `*.csr`) are **git-ignored** and never committed. Configure signing via DevEco Studio auto-signing (or supply your own `material/` + `build-profile.json5` entries locally).

## Status

Used for device / emulator distribution. Keep `rawfile/index.html` synchronized with the latest web build before each signed build.
