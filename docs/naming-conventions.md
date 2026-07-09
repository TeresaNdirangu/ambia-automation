# Naming Conventions

Status: to be finalized in Phase 2 (Framework Design). This file exists now so the convention has a home from day one.

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
