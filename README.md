# Ambia UI Automation

UI test automation for the Ambia mobile app (VoIP calling, eSIM management, KYC/registration, Wallet/Payments, Settings & Profile).

This repo follows the phased roadmap in `docs/Ambia_Automation_Roadmap` (Maestro first, Appium second). Progress is tracked in the companion Excel workbook, `Ambia_Automation_Tracker.xlsx`.

## Structure

```
ambia-automation/
├── maestro/
│   ├── flows/            # Test flows, grouped by feature module
│   │   ├── kyc/
│   │   ├── calls/
│   │   ├── contacts/
│   │   ├── esim/
│   │   ├── wallet/
│   │   └── menu_settings/
│   ├── subflows/         # Reusable building blocks (setup, atomic actions, assertions)
│   └── config/           # Environment configs and test data
├── appium/
│   └── src/
│       ├── pages/         # Page Object Model (Phase 4)
│       ├── tests/
│       ├── utils/
│       └── config/
├── ci/
│   └── github-actions/    # CI workflows (Phase 5)
├── reports/                # Test run output — git-ignored
└── docs/                    # Naming conventions, test case matrix, notes
```

## Status

Currently in **Phase 1 — Automation Foundation**. See the tracker workbook for live progress.

## Getting Started

1. Install the Maestro CLI: https://maestro.dev
2. Connect an Android emulator (Android Studio) or physical device with USB debugging enabled
3. Run the sample flow: `maestro test maestro/flows/kyc/skip_kyc.yaml`

## Branching Strategy

- `main` — always green, merged via PR only
- `feature/<module-name>` — one branch per test module, e.g. `feature/kyc-flows`
- Commit messages reference test case IDs where applicable, e.g. `feat(kyc): automate Skip KYC flow — KYC-01`
