# QA & Testing

This folder contains the public QA documentation for **P2P Market Monitor / P2P NOOB**.

**QA iteration date:** 2026-09-19  
**Iteration status:** COMPLETED  
**Retest:** PASS  
**Targeted regression:** PASS  
**Known major open defect:** BUG-001 — rare abnormal V2 collection cycle

The project was tested using smoke, functional, negative, exploratory, endurance, research, retest, and targeted regression activities.

## Start here

1. [`reports/final_qa_report.md`](reports/final_qa_report.md) — canonical final QA report.
2. [`reports/final_qa_report.pdf`](reports/final_qa_report.pdf) — presentation-ready report.
3. [`results/test_coverage_matrix.md`](results/test_coverage_matrix.md) — compact coverage/status overview.
4. [`defects/defect_and_gap_register.md`](defects/defect_and_gap_register.md) — defects, UX findings, requirement gaps, and deferred coverage.
5. [`research/endurance_and_research_report.md`](research/endurance_and_research_report.md) — V1/V2 endurance findings and BUG-001 research.
6. [`results/retest_and_targeted_regression.md`](results/retest_and_targeted_regression.md) — post-fix verification.

## Folder structure

```text
qa/
├── README.md
├── reports/
│   ├── final_qa_report.md
│   └── final_qa_report.pdf
├── planning/
│   └── test_plan.md
├── results/
│   ├── test_coverage_matrix.md
│   ├── amount_negative_testing.md
│   ├── target_profit_validation_testing.md
│   ├── exploratory_testing.md
│   └── retest_and_targeted_regression.md
├── defects/
│   ├── bug_001_v2_long_cycle.md
│   └── defect_and_gap_register.md
├── research/
│   ├── endurance_and_research_report.md
│   └── v2_long_cycle_research_plan.txt
└── evidence/
    └── v2_long_cycle_terminal.jpg
```

## Important note

Historical test-result files preserve the behavior observed **before** fixes. They are intentionally not rewritten to turn earlier FAIL results into PASS.

The final status of the tested build is defined by the final QA report plus the retest and targeted-regression results.

This is a focused QA iteration of the implemented build, not exhaustive production certification. Trade execution, real-money processing, production load testing, a full security audit, and future product features were outside the tested scope.
