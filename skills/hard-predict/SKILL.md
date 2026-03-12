# Foresight Engine — Hard Predict

This skill is the deterministic backbone of the Foresight Engine. It is invoked by the `foresight-analyst` agent — not directly by the user.

**Hard Predict** chains Claude's intelligence (signal collection, driver extraction, scenario writing) with Python modules (deterministic scoring, matrix, probabilities, formatting). Every number is computed, not estimated. Every run produces identical scores for identical inputs.

---

## When Hard Predict Runs

The user says any of:
- "Run hard predict: [question]"
- "Hard predict: [question]"
- "Give me auditable foresight on [question]"
- "I need reproducible scores for [question]"

The `foresight-analyst` agent is then spawned and executes the pipeline below.

---

## Pipeline (Claude + Python Chain)

| Step | Who | What |
|---|---|---|
| 1. Validate | Python | `input_validator.py` — 5-rule check |
| 2. Collect signals | Claude | WebSearch, 18+ signals → `signals.json` |
| 3. Score signals | Python | `signal_scorer.py` → `scored_signals.json` |
| 4. Extract drivers | Claude | Identify top 3 structural drivers from scored clusters |
| 5. Build matrix | Python | `matrix_builder.py` → `matrix.json` |
| 6. Cross-impact | Claude | Identify convergence and friction points from matrix |
| 7. Find analogues | Claude | 3 historical analogues → `analogues.json` |
| 8. Probabilities | Python | `probability_calc.py` → `probabilities.json` |
| 9. Confidence | Python | `confidence_calc.py` → integer |
| 10. Decision guidance | Python | `decision_guidance.py` → `guidance.json` |
| 11. Write scenarios | Claude | 4 scenarios (PREFERABLE uses backcasting) |
| 12. Format report | Python | `report_formatter.py report_data.json` |

---

## Scripts Location

All Python modules are in:
```
${CLAUDE_PLUGIN_ROOT}/skills/hard-predict/scripts/
```

---

## Output Guarantee

- Signal scores: identical every run for identical input
- Probabilities: normalized to sum exactly to 100%
- Regional multipliers: exact float multiplication
- All 18 matrix cells: computed (no skipping)
- Confidence: integer 0–100

Audit trail: all intermediate JSON files written to plugin root.
