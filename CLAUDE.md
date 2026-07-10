# CLAUDE.md

This file is read by Claude Code at the start of every session in this repo. It captures project context, conventions, and decisions already established — follow it consistently rather than re-deriving conventions from scratch each session.

## Project overview

Ambia is a native Android/iOS app (VoIP calling + eSIM purchase/management + KYC/registration + Wallet/Payments + Settings). This repo (`C:\Projects\ambia-automation`) is a solo QA engineer's UI automation project, built by someone growing from limited programming experience into a skilled automation engineer over time.

**Tooling strategy:** Maestro first (low-code, in progress now), Appium second (Python, deferred — a previously stalled Appium project exists separately and will be revisited once Maestro instincts are solid). Full phased plan lives in `Ambia_Automation_Roadmap.docx`; live progress tracking lives in `Ambia_Automation_Tracker.xlsx`. Treat both as sources of truth for scope and priority — check them before assuming what's done or what's next.

**Old Appium project (reference only, not actively maintained):** `C:\Projects\Ambia Automation V1`. This is a separate, currently-paused project — use it only to look up previously-identified resource IDs/locators when relevant, not as a source of current conventions or structure (this repo's conventions in this file and `docs/naming-conventions.md` take precedence). Note the space in the folder name — quote the full path in any command that references it (e.g. `"C:\Projects\Ambia Automation V1\..."`).

## Environment

- Windows, Command Prompt (not PowerShell, not WSL)
- Maestro CLI installed natively (no WSL required)
- Testing against a **physical Samsung Android device via USB** — no emulator currently in use
- Real package name: `com.eligeafrica.ambia` (note: `adb shell pm list packages` prefixes output with `package:` — that prefix is a display artifact, never part of the actual appId)
- Staging environment has intermittent outages — several deliverables are blocked on it (see Known scope decisions below)

## Repo structure

```
ambia-automation/
├── maestro/
│   ├── flows/{core,kyc,calls,contacts,esim,wallet,menu_settings}/
│   │       # core/ = cross-cutting flows not specific to one feature
│   │       #        (app launch, session persistence, tab navigation)
│   ├── subflows/          # reusable atomic/setup/assertion building blocks
│   └── config/            # config.yaml, test-data.yaml
├── appium/                # Phase 4 - not active yet
├── ci/                    # Phase 5 - not active yet
├── docs/
│   └── naming-conventions.md   # authoritative selector/naming rules
└── reports/                # git-ignored
```

## Git conventions

- `main` branch, always green, merged via PR when working with others (currently solo, direct commits acceptable)
- `feature/<module-name>` branches for larger chunks of work
- Commit format: `type(scope): description` (e.g. `feat(calls): automate app launch reaches home screen`)
- **Commit after each working flow or fix — not batched at the end of a session.** This repo was local-only (no remote) for a stretch early on; don't let that happen again — push regularly.
- Every commit message and every CLI command/code block shown to the user must be preceded by a comment stating which deliverable it fulfills (standing instruction from the user).

## Selector strategy (Maestro)

Preference order, established from real debugging on this project:
1. **`id`** (resource-id suffix, e.g. `txtBalance` not the full `com.eligeafrica.ambia:id/txtBalance`) — most stable
2. **Stable `text`** — fine when no id exists and the label is static
3. **Regex `text`** (e.g. `"Airtime Balance : KES.*"`) — last resort for dynamic values, confirmed via `maestro hierarchy` first, not guessed

Known traps on this app, confirmed via real failures — watch for both:
- **Repeated elements sharing the same generic id** (e.g. the bottom tab bar — `tab_container`, `tab_icon`, `tab_below_text` all repeat across every tab). For these, `text` is the *correct* selector, not a fallback — id is not unique enough to disambiguate.
- **Leading/trailing whitespace in text nodes** (e.g. `" Calls"`, `" Menu"` — confirmed via raw `maestro hierarchy` dump, not visible in Studio's click-to-inspect). Use a tolerant regex like `.*Menu.*` rather than an exact string when this is suspected.
- **Misleadingly-named or reused ids across different screens/tabs** — don't infer an element's purpose from its id name. Confirmed instances: on Transactions History, `fav_unfav` is actually the download icon and `user_info` is actually the filter icon (both also share `content-desc="Ambia"` with the back button, so `id` is the only usable selector); in the eSIM store, the Regional tab's list container is `local_esims_list` despite showing region groupings, not local countries; `toolbar_text` is reused generically across many unrelated screens (Transactions History, Call Settings, Calling Rates, Preferences, Forwarding Verification) so it can't identify *which* screen loaded — assert a screen-specific id instead. Always confirm via live `maestro hierarchy`/bounds, not the id string.

## Maestro flow file requirements

- Every flow file, **including subflows invoked via `runFlow`**, requires both a config section (at minimum `appId:`) and the `---` separator — this is mandatory per Maestro's own schema, not optional for subflows.
- Verify actual on-disk file content before deep-troubleshooting a parse/config error — this project has repeatedly hit issues caused by edits not persisting, stray characters (e.g. a trailing `]`), or editor autocomplete silently swapping content. When a fix "should have worked" but the same error recurs, re-paste the real current file content before theorizing further.

## Testing/device conventions

- Samsung-specific settings required for automation to work reliably: **USB debugging (Security settings)** ON, **Auto Blocker** OFF, Ambia battery usage set to **Unrestricted**
- Close Maestro Studio Desktop before running CLI tests against the same device — running both simultaneously causes session/heartbeat file-lock conflicts
- Any new or modified flow should pass **5 consecutive runs** before being considered done — a single green run is not sufficient (this is the standing repeatability bar for this project, not just Item 5)

## Known scope decisions / carry-overs (do not silently re-litigate these)

- **KYC is shelved.** Screen inspection and all KYC-dependent flows (submission, resubmission) are blocked on a staging environment outage. Do not propose or build KYC-dependent work until the user confirms staging is back and KYC is closed out.
- **OTP-based flows and Logout are also staging-blocked** — logging out would strand the physical device logged-out with no way to log back in until staging returns.
- **AmbiaPay is deliberately excluded** from the general tab-navigation smoke flow — it belongs to a dedicated Wallet/Payments track, not general nav.
- **"Add new contact" hands off to the native Samsung Contacts app** (`com.samsung.android.app.contacts`), not an in-app Ambia form — this is a real multi-app interaction and creates a permanent contact on the physical device. Test contact name uses a `ZZ_` prefix (e.g. `ZZ_Maestro_Test_Contact`) for easy manual identification/cleanup — no automated teardown flow exists yet.

## Current status

Phase 1 (Foundation) and Phase 2 (Framework Design) are substantially complete. Phase 3 (Maestro Priority 1–2 flows) is in progress. Check `Ambia_Automation_Tracker.xlsx` → Master Project Tracker for the authoritative up-to-date status before starting new work.
