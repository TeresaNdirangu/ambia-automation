# Naming Conventions

Status: Finalized in Phase 2 (Framework Design).

## Flow files

`feature_action_expectedstate.yaml`

Examples:
- `kyc_skip_shows_registration_incomplete.yaml`
- `calls_search_returns_matching_contact.yaml`

## Subflows

- Atomic actions: `tap_<element>.yaml`, `dismiss_<dialog>.yaml`
- Setup flows: `launch_and_<state>.yaml`, `login_as_<user_type>.yaml`
- Assertion flows: `assert_<expected_state>.yaml`

## Tags

- `smoke` — critical-path flows run on every PR
- `regression` — full suite, run nightly
- Feature tags: `voip`, `esim`, `kyc`, `wallet`, `payments`, `settings`

## Selector Strategy

Preference order, based on real debugging experience on this project:

1. **`id`** — most stable. Survives copy/wording changes. Use the resource-id
   suffix only (e.g. `txtBalance`, not the full `com.eligeafrica.ambia:id/txtBalance`).
2. **Stable `text`** — fine when there's no id and the label is static.
3. **Regex `text`** — last resort for dynamic values (amounts, counters, timestamps)
   *only* when no id exists on the element. Confirmed via `maestro hierarchy` first,
   not guessed — Studio's click-to-inspect can occasionally surface a parent/wrapper
   view instead of the actual text node, so cross-check the raw hierarchy dump when
   a selector doesn't behave as expected.