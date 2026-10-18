# Launchpad: 26.3 port notes

Unofficial community port of Sinytra's Launchpad to Minecraft 26.3 / NeoForge 26.3.0.89 (FML 12.0.12),
made for the Zoompack 5 modpack. All credit goes to Sinytra. The licence is unchanged
(GPL-3.0-only WITH Classpath-exception-2.0). Not published and not affiliated with Sinytra.

## Upstream pattern

26.1 -> 26.2 was commit `c6167d6` "Port to 26.2. Wow, no changes required.": version bumps only.
26.3 needed two code changes.

## What changed

- **`RegistryHelper`**: NeoForge 26.3 removed `DataPackRegistryEvent.NewRegistry`. The replacement,
  `NewDatapackRegistryEvent`, has a package-private constructor and `process()`, so both are invoked
  reflectively to post the event to `fabric_registry_sync_v0`.
- **`BlockCallbacksMixin`**: in `NeoForgeRegistryCallbacks$BlockCallbacks.onBake` the new-block
  `Set.clear()` moved into a `SolidDebugger.runAndDump` lambda. The injection now happens right after
  `runAndDump`, which is still before the `BLOCKSTATE_TO_ID_MAP` rebuild loop, so the ID-map reset keeps working.
- Versions: MC 26.3, NeoForge 26.3.0.89, testframework 26.3.0.89, Launchpad 1.11.0, FFAPI
  `0.163.0+26.3+5.0.0+local` (the locally published 26.3 FFAPI build; switch to a real release once
  one exists). Forgified Fabric Loader stays at 2.5.90+0.19.5+26.1.2 (FML 12 changes don't affect it).

No other FML 12 API Launchpad uses changed: `neoforgespi.locating`, `JarContents`, `ModFile` and the
jar-in-jar locator are unchanged.

## Test status

- `./gradlew build`: passes; 4/4 unit tests.
- At runtime, in a NeoForge 26.3.0.89 game-test server together with Connector 26.3 + FFAPI 26.3, it
  loads Fabric mods (AppleSkin, Krypton, Mielon's The Sift + NeoForge GeckoLib). The ID-map fix and
  the registry event run without errors.
- The client is not tested.

## Keeping it up to date

- `git fetch origin && git rebase origin/26.2.x` (or merge) to pick up upstream fixes. The two commits
  here are small.
- Once Sinytra publishes FFAPI for 26.3, replace the `+local` FFAPI version in
  `gradle/libs.versions.toml`.
