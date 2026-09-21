# QA & Testing

This folder contains the public QA documentation for **P2P NOOB**.

The project was tested using smoke, functional, negative, exploratory, endurance, research, retest, and targeted regression activities.

Testing covered the live monitor, web interface, Telegram notifications, external reference handling, historical market collection, and PostgreSQL persistence.

## QA outcome

The main implemented user flows remained functional after the selected fixes. Testing identified several input-validation and UI-state issues, which were fixed and retested successfully.

**Known major open defect:** BUG-001 — rare abnormal historical collection cycle that can remain active for hours before saving a delayed snapshot.

## Documents

- [`test_plan.md`](test_plan.md) — testing scope, approach, environment, and completion criteria.
- [`test_results.md`](test_results.md) — consolidated smoke, functional, negative, exploratory, endurance, retest, and targeted-regression results.

- [`defects.md`](defects.md) - structured defect records, available evidence and investigation limits.

## Note on historical findings

Some test cases intentionally preserve the **pre-fix behavior** that originally failed. The results show both the original finding and the current post-fix status instead of rewriting earlier failures as if they never occurred.

This QA iteration is focused on the implemented project flows and selected failure scenarios rather than exhaustive production certification.
